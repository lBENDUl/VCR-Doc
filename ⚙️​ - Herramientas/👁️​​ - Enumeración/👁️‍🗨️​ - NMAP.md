# 👁️‍🗨️ Nmap

> Referencia rápida para reconocimiento y enumeración de redes en auditorías de seguridad. Orientada a uso en entornos autorizados.

---

## ¿Qué es?

Nmap (Network Mapper) es la herramienta estándar para el reconocimiento de redes. Permite identificar hosts activos, puertos abiertos, servicios en ejecución, versiones de software y sistemas operativos.

---

## Parámetros básicos

### Control de hosts y resolución

| Flag | Descripción |
|------|-------------|
| `-Pn` | No hace ping previo; asume que el host está activo. Útil cuando el firewall bloquea ICMP. |
| `-n` | Sin resolución DNS. Acelera considerablemente el escaneo. |
| `-sn` | Ping sweep: detecta hosts activos sin escanear puertos. |

```bash
# Descubrir hosts activos en una subred
nmap -sn 192.168.1.0/24

# Escanear sin ping y sin DNS
nmap -Pn -n 192.168.1.100
```

---

### Tipos de escaneo

| Flag | Tipo | Notas |
|------|------|-------|
| `-sS` | SYN scan (half-open) | Sigiloso, no completa el handshake TCP. **Requiere root.** |
| `-sT` | TCP Connect scan | Completa la conexión. Detectable pero funciona sin root. |
| `-sU` | UDP scan | Más lento. Para servicios como DNS, SNMP, TFTP. |
| `-sV` | Detección de versiones | Identifica el servicio y su versión en cada puerto abierto. |
| `-O`  | Detección de SO | Intenta identificar el sistema operativo. Requiere root. |
| `-sC` | Scripts por defecto | Ejecuta los scripts NSE de la categoría `default`. |

```bash
# Escaneo completo típico
nmap -sS -sV -sC -O 192.168.1.100

# Escaneo de todos los puertos
nmap -sS -p- 192.168.1.100

# Combinar detección de versiones y scripts
nmap -sV -sC 192.168.1.100
```

---

### Velocidad (-T)

| Nivel | Nombre | Uso recomendado |
|-------|--------|-----------------|
| `-T0` | Paranoid | Máximo sigilo, muy lento |
| `-T1` | Sneaky | Sigilo alto |
| `-T2` | Polite | Reduce carga en la red |
| `-T3` | Normal | Por defecto |
| `-T4` | Aggressive | Rápido, bueno en redes locales |
| `-T5` | Insane | Muy rápido, puede perder paquetes |

```bash
nmap -T4 -sS 192.168.1.100
```

---

### Puertos

```bash
# Puertos específicos
nmap -p 22,80,443 192.168.1.100

# Rango de puertos
nmap -p 1-1000 192.168.1.100

# Todos los puertos (1-65535)
nmap -p- 192.168.1.100

# Solo puertos abiertos en la salida
nmap --open 192.168.1.100
```

---

### Salida de resultados

```bash
# Guardar en los tres formatos principales
nmap -oA resultado 192.168.1.100

# Solo XML (para herramientas que lo procesen)
nmap -oX resultado.xml 192.168.1.100

# Leer lista de hosts desde archivo
nmap -iL hosts.txt
```

---

## Evasión de firewalls e IDS

Técnicas para auditorías donde los sistemas de detección pueden interferir. Usar siempre en entornos autorizados.

### Fragmentación de paquetes

```bash
# Fragmenta los paquetes en trozos pequeños
nmap -f 192.168.1.100

# Más fragmentación
nmap -ff 192.168.1.100

# Control manual del MTU (debe ser múltiplo de 8)
nmap --mtu 16 192.168.1.100
```

### Señuelos (Decoys)

Hace que el IDS vea escaneos provenientes de múltiples IPs a la vez, dificultando identificar el origen real.

```bash
nmap -D 192.168.1.50,192.168.1.60,ME 192.168.1.100
```

`ME` indica que tu IP real también se incluya en la lista.

### Puerto de origen

Algunos firewalls permiten tráfico desde puertos de confianza (DNS 53, FTP 21…).

```bash
nmap --source-port 53 192.168.1.100
```

### Datos adicionales en paquetes

Varía el tamaño de los paquetes para evitar reglas basadas en firmas.

```bash
nmap --data-length 200 192.168.1.100
```

### Control de velocidad

Ralentizar el escaneo para evadir IDS basados en tasa de paquetes.

```bash
nmap --min-rate 10 192.168.1.0/24
```

### Spoofing de MAC

Útil en redes locales para evadir filtros por dirección MAC.

```bash
nmap --spoof-mac 00:11:22:33:44:55 192.168.1.100
```

### Idle/Zombie scan

Escaneo completamente anónimo usando un host "zombie" intermediario con IP ID incremental predecible.

```bash
nmap -Pn -v -n -p80 -b usuario:contraseña@zombie_host objetivo.com
```

---

## Scripts NSE (Nmap Scripting Engine)

Scripts escritos en Lua que extienden las capacidades de Nmap. Ubicados en `/usr/share/nmap/scripts/`.

### Categorías

| Categoría | Descripción |
|-----------|-------------|
| `default` | Scripts seguros y útiles, se ejecutan con `-sC` |
| `discovery` | Descubrimiento de información en la red |
| `safe` | No invasivos, no generan alertas |
| `intrusive` | Más agresivos, pueden ser detectados |
| `vuln` | Detección de vulnerabilidades conocidas |

### Scripts más usados

```bash
# Ejecutar categoría entera
nmap --script vuln 192.168.1.100
nmap --script discovery 192.168.1.100

# Scripts específicos
nmap --script http-enum 192.168.1.100         # Enumerar rutas web comunes
nmap --script ftp-anon 192.168.1.100          # Comprobar FTP anónimo
nmap --script smb-enum-shares 192.168.1.100   # Shares SMB disponibles
nmap --script http-title 192.168.1.0/24       # Títulos de páginas web en la red
nmap --script banner 192.168.1.100            # Banner grabbing de servicios
```

### Estructura básica de un script personalizado en Lua

```lua
description = [[Reporta puertos TCP abiertos]]
author = "tu nombre"
license = "Same as Nmap"
categories = {"discovery"}

-- Condición de ejecución: cuándo debe correr el script
portrule = function(host, port)
    return port.protocol == "tcp" and port.state == "open"
end

-- Acción principal
action = function(host, port)
    return "Puerto TCP abierto detectado en " .. host.ip
end
```

Los scripts personalizados van en `/usr/share/nmap/scripts/` y se activan con `--script nombre_script`.

---

## Workflow recomendado

Un flujo típico en una auditoría, de menos a más intrusivo:

```bash
# 1. Descubrir hosts activos
nmap -sn -oA 01_hosts 192.168.1.0/24

# 2. Escaneo de puertos en hosts activos
nmap -sS -sV -p- --open -oA 02_puertos -iL hosts_activos.txt

# 3. Scripts y detección de SO
nmap -sC -O --script vuln -oA 03_scripts -iL hosts_activos.txt

# 4. Enumeración web (si hay servicios HTTP)
nmap -p 80,443,8000,8080,8443 --open -oX 04_web.xml -iL hosts_activos.txt
```

---

## Herramientas complementarias

### EyeWitness

Captura pantallas de aplicaciones web a partir de un XML de Nmap. Genera un informe HTML interactivo con las capturas agrupadas, tecnologías detectadas y credenciales por defecto sugeridas.

```bash
sudo apt install eyewitness
eyewitness --web -x 04_web.xml -d informe_web/
```

### Aquatone

Similar a EyeWitness pero escrito en Go, más rápido. Lee directamente el XML de Nmap y agrupa páginas similares automáticamente.

```bash
# Instalación
wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
unzip aquatone_linux_amd64_1.7.0.zip
sudo mv aquatone /usr/local/bin/

# Uso
cat 04_web.xml | aquatone -nmap
# Genera aquatone_report.html
```

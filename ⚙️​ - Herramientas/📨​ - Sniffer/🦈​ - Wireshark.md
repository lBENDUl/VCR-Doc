# 🦈 Wireshark
## Análisis de Tráfico de Red y Captura de Paquetes

---

## Introducción

Wireshark es una herramienta de análisis de tráfico de red de código abierto que permite capturar y examinar paquetes en tiempo real. Es considerada el estándar de facto para la investigación forense de redes, resolución de problemas de conectividad y análisis de seguridad. Proporciona visibilidad profunda en todo lo que ocurre en una red.

Esta guía proporciona un enfoque profesional para dominar Wireshark, desde la captura básica de paquetes hasta análisis avanzados de protocolos y detección de anomalías. Es un recurso esencial para administradores de redes, especialistas en seguridad y profesionales de DevOps.

---

## Tabla de Contenidos

1. [Instalación y Configuración](#instalación-y-configuración)
2. [Interfaz de Usuario](#interfaz-de-usuario)
3. [Captura de Paquetes](#captura-de-paquetes)
4. [Filtros de Visualización](#filtros-de-visualización)
5. [Filtros de Captura](#filtros-de-captura)
6. [Análisis de Protocolos Comunes](#análisis-de-protocolos-comunes)
7. [Búsqueda y Estadísticas](#búsqueda-y-estadísticas)
8. [Exportación y Reportes](#exportación-y-reportes)
9. [Casos de Uso Prácticos](#casos-de-uso-prácticos)
10. [Mejores Prácticas](#mejores-prácticas)

---

## Instalación y Configuración

### Instalación en Linux

#### Debian/Ubuntu

```bash
sudo apt update
sudo apt install wireshark wireshark-common
```

Durante la instalación, se preguntará si deseas permitir que usuarios no-root capturen paquetes. Responde "Sí" para más comodidad.

#### Agregar Usuario al Grupo wireshark

Para ejecutar Wireshark sin privilegios root:

```bash
sudo usermod -aG wireshark $USER
```

Luego cierra sesión y vuelve a iniciar sesión para que los cambios surtan efecto.

### Instalación en macOS

```bash
brew install wireshark
```

### Instalación en Windows

Descarga el instalador desde [wireshark.org](https://www.wireshark.org/download) y ejecuta el asistente de instalación. Se instalarán automáticamente los drivers necesarios para captura de paquetes.

### Verificación de Instalación

```bash
wireshark --version
```

---

## Interfaz de Usuario

La interfaz de Wireshark consta de varios componentes clave:

### Barra de Herramientas Principal

- **Seleccionar interfaz**: Permite elegir qué interfaz de red monitorear
- **Iniciar/Detener captura**: Botones para controlar la captura en tiempo real
- **Filtro de visualización**: Campo de texto para aplicar filtros en tiempo real
- **Búsqueda**: Buscar paquetes específicos

### Panel de Paquetes (Arriba)

Muestra una lista de todos los paquetes capturados con información resumida:
- Número de paquete
- Tiempo transcurrido
- Dirección IP origen y destino
- Protocolo
- Información del protocolo

### Panel de Detalles (Centro)

Cuando seleccionas un paquete, muestra la estructura jerárquica del paquete:
- Ethernet Frame
- IP Header
- TCP/UDP Header
- Datos de aplicación

### Panel de Bytes (Abajo)

Muestra la representación hexadecimal y ASCII del paquete seleccionado. Útil para análisis a bajo nivel y búsqueda de patrones específicos.

---

## Captura de Paquetes

### Iniciar una Captura Básica

1. Abre Wireshark
2. Selecciona la interfaz de red a monitorear (generalmente eth0, wlan0, etc.)
3. Haz clic en el botón de play azul o presiona Ctrl+E
4. El tráfico comenzará a capturarse en tiempo real

### Tipos de Interfaz

- **Ethernet (eth0, eth1)**: Conexiones cableadas
- **Wireless (wlan0, wlan1)**: Conexiones inalámbricas
- **Loopback (lo)**: Tráfico local de la máquina
- **Docker/Contenedores**: Interfases virtuales para tráfico de contenedores

### Captura Pasiva vs Activa

**Captura Pasiva**: Solo observas el tráfico existente en la red

**Captura Activa**: Generas tráfico activamente para análisis (ping, DNS queries, etc.)

```bash
# Ejemplo: Generar tráfico mientras capturas
ping -c 10 192.168.1.1  # En una terminal
# Inicia captura en Wireshark en otra terminal
```

### Limitación de Captura por Tamaño

Para evitar que archivos enormes llenen tu disco:

**Opción de línea de comandos:**

```bash
wireshark -i eth0 -w captura.pcap -a filesize:100000
# Limita archivos a 100 MB
```

### Captura desde Línea de Comandos

Usa `tshark` (versión terminal de Wireshark):

```bash
# Captura simple
tshark -i eth0

# Captura con filtro
tshark -i eth0 -f "tcp port 443"

# Guardar captura a archivo
tshark -i eth0 -w captura.pcap

# Leer archivo pcap
tshark -r captura.pcap
```

---

## Filtros de Visualización

Los filtros de visualización se aplican **después** de la captura para mostrar solo los paquetes que te interesan. Son fundamentales para analizar grandes volúmenes de tráfico.

### Sintaxis Básica

```
protocolo.campo operador valor
```

### Operadores

| Operador | Significado | Ejemplo |
|----------|-------------|---------|
| `==` | Igual a | `ip.src == 192.168.1.100` |
| `!=` | No igual a | `ip.dst != 8.8.8.8` |
| `>` | Mayor que | `tcp.len > 1000` |
| `<` | Menor que | `tcp.len < 100` |
| `contains` | Contiene texto | `http contains "password"` |
| `matches` | Expresión regular | `http.request.uri matches "\.php$"` |
| `&&` o `and` | Y lógico | `ip.src == 192.168.1.1 && tcp.port == 443` |
| `\|\|` o `or` | O lógico | `tcp.port == 80 or tcp.port == 443` |
| `!` o `not` | Negación | `not icmp` |

### Filtros por Protocolo

```bash
# Solo tráfico HTTP
http

# Solo tráfico HTTPS
tls

# Solo DNS
dns

# Solo ARP
arp

# Solo DHCP
dhcp

# Solo tráfico ICMP (ping)
icmp
```

### Filtros por Dirección IP

```bash
# Tráfico desde una IP específica
ip.src == 192.168.1.100

# Tráfico hacia una IP específica
ip.dst == 8.8.8.8

# Tráfico en ambas direcciones
ip.addr == 192.168.1.100

# Tráfico de una subred
ip.src == 192.168.1.0/24
```

### Filtros por Puerto

```bash
# Tráfico en puerto específico
tcp.port == 443

# Tráfico origen o destino en puerto
tcp.dstport == 80

# Puertos en rango
tcp.port >= 1024 && tcp.port <= 65535

# Múltiples puertos
tcp.port in {80, 443, 8080}
```

### Filtros por Protocolo de Aplicación

```bash
# HTTP
http.request
http.response
http.request.method == "GET"

# DNS
dns.qry.name contains "google.com"
dns.resp.type == 1  # A record

# DHCP
dhcp.option.dhcp_message_type == 1  # DISCOVER
dhcp.option.dhcp_message_type == 5  # ACK

# FTP
ftp.request.command == "USER"
```

### Filtros Combinados (Casos Reales)

```bash
# Tráfico HTTP desde una máquina específica
http && ip.src == 192.168.1.100

# Conexiones TCP fallidas (flags de reset)
tcp.flags.reset == 1

# Tráfico HTTPS de una subred (excluir broadcast)
tls && ip.src == 192.168.1.0/24 && not eth.dst == ff:ff:ff:ff:ff:ff

# Paquetes grandes de TCP
tcp.len > 1000

# Tráfico DNS que no sea a servidores públicos
dns && ip.dst != 8.8.8.8 && ip.dst != 1.1.1.1

# Correo electrónico (SMTP)
tcp.port == 25 or tcp.port == 587 or tcp.port == 465
```

### Guardar Filtros Favoritos

Los filtros frecuentemente utilizados pueden guardarse:

1. Ingresa tu filtro en la barra de filtros
2. Haz clic en el botón de guardado (disquete) a la derecha
3. Dale un nombre descriptivo

---

## Filtros de Captura

Los filtros de captura se aplican **durante** la captura, reduciendo el volumen de datos capturados. Son más eficientes que los filtros de visualización para capturas grandes.

### Sintaxis BPF (Berkeley Packet Filter)

```
protocolo dirección host puerto
```

### Ejemplos de Filtros de Captura

```bash
# Solo tráfico TCP
tcp

# Solo puerto 443
port 443

# Múltiples puertos
port 80 or port 443 or port 8080

# Host específico
host 192.168.1.100

# Desde o hacia una IP
src host 192.168.1.100
dst host 8.8.8.8

# Rango de IPs
net 192.168.1.0/24

# Protocolo y puerto
tcp port 443

# Excluir tráfico
not icmp
not port 22  # Excluir SSH

# Combinaciones complejas
(tcp port 80 or tcp port 443) and host 192.168.1.100
tcp.port == 443 and not ip.src == 192.168.1.50
```

### Aplicar Filtro de Captura

En la interfaz gráfica:

1. Selecciona la interfaz de red
2. Haz clic en el icono de engranaje (Capture Options)
3. En la sección "Capture Filter", ingresa tu filtro
4. Haz clic en "Start"

Desde línea de comandos:

```bash
wireshark -i eth0 -f "tcp port 443"
tshark -i eth0 -f "not arp"
```

---

## Análisis de Protocolos Comunes

### HTTP/HTTPS

#### Analizar Solicitudes HTTP

```bash
# Ver todas las solicitudes HTTP
http.request

# Solicitudes GET
http.request.method == "GET"

# Solicitudes POST
http.request.method == "POST"

# Búsqueda de URLs específicas
http.request.uri contains "/api/"

# Respuestas HTTP error
http.response.code >= 400
```

#### Extraer Credenciales HTTP Básicas

Cuando el tráfico es HTTP (no HTTPS), las credenciales viajan en Base64:

1. Filtra por `http.request`
2. Expande el detalle del paquete
3. Busca "Authorization" en los headers
4. Decodifica el valor Base64

### DNS

#### Análisis DNS

```bash
# Todas las consultas DNS
dns.flags.response == 0

# Todas las respuestas DNS
dns.flags.response == 1

# Consultas A (IPv4)
dns.qry.type == 1

# Consultas MX (Mail)
dns.qry.type == 15

# Búsqueda de dominio específico
dns.qry.name contains "example.com"
```

#### Extracción de Servidores DNS

1. Filtra por `dns`
2. En el panel de detalles, expande "Domain Name System"
3. Observa los servidores a los que se consulta en "Queries"

### TCP/IP

#### Análisis de Handshake TCP

El three-way handshake consta de tres paquetes:

1. **SYN**: Cliente envía flag SYN
2. **SYN-ACK**: Servidor responde con SYN y ACK
3. **ACK**: Cliente confirma con ACK

```bash
# Ver solo handshakes TCP
tcp.flags.syn == 1

# Conexiones completadas
tcp.flags.syn == 1 or tcp.flags.ack == 1

# Conexiones rechazadas (RST)
tcp.flags.reset == 1

# Fin de conexión (FIN)
tcp.flags.fin == 1
```

### ARP (Address Resolution Protocol)

```bash
# Todas las solicitudes ARP
arp.opcode == 1

# Todas las respuestas ARP
arp.opcode == 2

# ARP spoofing detection
arp && arp.src.hw_mac == <dirección_MAC_problema>
```

#### Detectar ARP Spoofing

1. Filtra por `arp`
2. Observa si la misma dirección IP responde desde MACs diferentes
3. Identifica la MAC legítima del dispositivo

### DHCP

```bash
# Solicitudes DHCP (DISCOVER)
dhcp.option.dhcp_message_type == 1

# Ofertas DHCP (OFFER)
dhcp.option.dhcp_message_type == 2

# Solicitudes DHCP (REQUEST)
dhcp.option.dhcp_message_type == 3

# Confirmaciones DHCP (ACK)
dhcp.option.dhcp_message_type == 5
```

### FTP

```bash
# Todas las conexiones FTP
ftp

# Comandos FTP
ftp.request.command

# Búsqueda de contraseñas FTP (inseguro)
ftp.request.command == "PASS"
```

---

## Búsqueda y Estadísticas

### Búsqueda de Paquetes

#### Búsqueda Rápida

Presiona Ctrl+F para abrir la barra de búsqueda:

- **Packet bytes**: Busca en datos hexadecimales
- **String**: Busca texto en los paquetes
- **Regex**: Expresiones regulares

### Estadísticas de Tráfico

#### Menú Statistics

El menú "Statistics" proporciona análisis agregados:

**Statistics → Protocol Hierarchy**

Muestra una jerarquía de todos los protocolos capturados y su porcentaje de uso.

**Statistics → Conversations**

Muestra todas las conversaciones (flujos) entre pares de direcciones:
- IPv4 Conversations: IP-to-IP
- TCP Conversations: TCP-to-TCP
- UDP Conversations: UDP-to-UDP

**Statistics → Endpoints**

Lista todos los hosts únicos y estadísticas por endpoint:
- Paquetes enviados/recibidos
- Bytes transmitidos
- Duración de conexión

**Statistics → I/O Graph**

Visualiza el tráfico en el tiempo con gráficos:
- Eje Y: Paquetes/segundo o bytes/segundo
- Eje X: Tiempo
- Múltiples gráficos superpuestos para comparar

### Análisis de Flujos

#### Seguir Flujos TCP/UDP

1. Haz clic derecho en un paquete
2. Selecciona "Follow" → "TCP Stream" o "UDP Stream"
3. Se abrirá una ventana mostrando todo el tráfico entre esos dos hosts

Esto es especialmente útil para ver conversaciones completas de un vistazo.

---

## Exportación y Reportes

### Guardar Capturas

#### Formato PCAP

```bash
# Guardar captura completa
File → Export as → pcapng

# Nombre recomendado con timestamp
captura_$(date +%Y%m%d_%H%M%S).pcap
```

#### Formato PCAPNG (Recomendado)

Formato más moderno que PCAP con más metadatos:

```bash
# Desde línea de comandos
wireshark -i eth0 -w captura.pcapng
```

### Exportación de Paquetes

#### Exportar Paquetes Específicos

1. Filtra para mostrar solo los paquetes que deseas
2. Selecciona los paquetes (puedes usar Shift+Click para rango)
3. File → Export Specified Packets
4. Elige el formato de destino

#### Exportar a CSV

```bash
# Menú gráfico
File → Export as → CSV

# Desde línea de comandos
tshark -r captura.pcap -T fields -e frame.number -e ip.src -e ip.dst -e tcp.sport -e tcp.dport > salida.csv
```

### Exportación de Objetos

#### Extraer Archivos Transferidos

Si HTTP fue capturado, puedes extraer archivos:

1. File → Export Objects → HTTP
2. Selecciona los archivos que deseas
3. Haz clic en "Save"

Esto funciona para:
- HTTP
- DICOM (imágenes médicas)
- CoAP (IoT)

### Generación de Reportes

#### Estadísticas a Archivo de Texto

```bash
# Exportar estadísticas de protocolos
tshark -r captura.pcap -q -z protocol,tree > reporte.txt

# Exportar conversaciones
tshark -r captura.pcap -q -z conv,tcp > conversaciones_tcp.txt
```

---

## Casos de Uso Prácticos

### Caso 1: Diagnosticar Problema de Conexión Lenta

**Objetivo**: Identificar por qué una aplicación es lenta

**Pasos**:

1. Inicia captura en la máquina problemática
2. Genera tráfico de la aplicación (accede a funcionalidades lentas)
3. Detén la captura
4. Filtra por la dirección IP del servidor: `ip.dst == <ip_servidor>`
5. Analiza:
   - **Tiempo de respuesta**: Statistics → I/O Graph
   - **Retransmisiones**: Busca paquetes TCP duplicados
   - **Tamaño de paquetes**: Observa si hay fragmentación
   - **Latencia**: Compara tiempos de envío/respuesta

### Caso 2: Investigar Tráfico Sospechoso

**Objetivo**: Detectar comportamiento anómalo en la red

**Pasos**:

1. Captura tráfico de la máquina sospechosa
2. Filtra por puertos no estándar: `not (tcp.port == 80 or tcp.port == 443 or tcp.port == 22)`
3. Identifica:
   - **Conexiones a IPs externas**: `ip.dst not in {192.168.0.0/16, 10.0.0.0/8}`
   - **Puertos de comando**: Busca conexiones inversas en puertos altos
   - **Exfiltración de datos**: Analiza volumen de bytes enviados

4. Exporta flujos sospechosos: Clic derecho → Follow TCP Stream

### Caso 3: Análisis de Tráfico DNS

**Objetivo**: Detectar consultas DNS maliciosas o bloqueos de DNS

**Pasos**:

1. Filtra por DNS: `dns.qry.name`
2. Statistics → Endpoints para ver servidores DNS consultados
3. Busca patrones:
   - **Dominios sospechosos**: Contiene caracteres extraños
   - **Dominios bloqueados**: DNS responde con NXDOMAIN
   - **Fast flux**: La misma IP responde a múltiples dominios

### Caso 4: Validar Certificados HTTPS

**Objetivo**: Verificar que HTTPS esté configurado correctamente

**Pasos**:

1. Filtra por TLS: `tls.handshake`
2. Expande "TLS handshake" en detalles del paquete
3. En "Server Hello", verifica:
   - **Versión de TLS**: Debe ser TLS 1.2 o superior
   - **Cipher suite**: Debe ser seguro (no NULL, DES, RC4)
   - **Certificado**: Puede ser extraído para análisis

### Caso 5: Auditar Cambios de Configuración

**Objetivo**: Monitorear cambios de red o configuración en tiempo real

**Pasos**:

1. Filtra por protocolo de gestión: `ssh or telnet or snmp`
2. Sigue los flujos para ver comandos ejecutados
3. Documenta cambios realizados para auditoría

---

## Recursos Adicionales

### Comandos Útiles de tshark

```bash
# Listar interfaces disponibles
tshark -D

# Captura detallada a verbose
tshark -i eth0 -v

# Mostrar solo headers
tshark -i eth0 -n

# Especificar campos para salida
tshark -i eth0 -T fields -e ip.src -e ip.dst -e tcp.flags

# Estadísticas de protocolo
tshark -r captura.pcap -q -z io,stat,1

# Detectar errores
tshark -r captura.pcap -q -z checksum,tcp
```

### Filtros Avanzados

```bash
# Conversiones de tipo de dato
tcp.port == 0x50  # Puerto 80 en hexadecimal

# Búsqueda de patrones binarios
tcp.payload contains "admin"

# Variables con regex
http.request.uri ~ "/admin.*\?.*="

# Múltiples condiciones complejas
(http.request or http.response) and ip.src == 192.168.1.1 and tcp.port == 80
```

Desde diagnósticos básicos de conectividad hasta análisis forense avanzado, Wireshark es indispensable. La práctica continuada con sus filtros, estadísticas y herramientas de análisis permitirá resolver problemas complejos de red de manera eficiente.

**Recuerda siempre capturar solo en sistemas y redes donde tienes autorización, y trata los archivos de captura con el cuidado que merecen, ya que pueden contener información sensible.**

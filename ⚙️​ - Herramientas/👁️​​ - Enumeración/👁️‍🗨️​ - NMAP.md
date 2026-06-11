# Nmap
## Enumeración y Reconocimiento de Redes

**Versión 1.0 - Junio 2024**

---

## Introducción

Nmap (Network Mapper) es una herramienta de código abierto fundamental en el arsenal de cualquier profesional de seguridad informática. Permite realizar reconocimiento de redes, identificar hosts activos, descubrir puertos abiertos y determinar servicios en ejecución. Esta guía proporciona un enfoque profesional para dominar Nmap, desde los conceptos básicos hasta técnicas avanzadas de evasión.

El objetivo de este documento es servir como referencia práctica y técnica para profesionales de ciberseguridad durante auditorías de seguridad, pruebas de penetración y evaluaciones de vulnerabilidades.

---

## Tabla de Contenidos

1. [Parámetros Básicos](#parámetros-básicos)
2. [Técnicas de Evasión de Firewalls e IDS](#técnicas-de-evasión-de-firewalls-e-ids)
3. [Scripts NSE (Nmap Scripting Engine)](#scripts-nse-nmap-scripting-engine)
4. [Herramientas Complementarias de Enumeración](#herramientas-complementarias-de-enumeración)
5. [Workflow Práctico](#workflow-práctico-escaneo-completo)
6. [Mejores Prácticas](#mejores-prácticas)

---

## Parámetros Básicos

Los parámetros básicos de Nmap forman la base para cualquier escaneo. Comprender cómo funcionan permite construir comandos más complejos y efectivos, adaptados a diferentes escenarios y objetivos de seguridad.

### -Pn (No realizar ping)

Este parámetro desactiva el descubrimiento de hosts y asume que todos los objetivos están en línea. Es útil cuando los firewalls bloquean solicitudes ICMP (ping). Acelera significativamente el escaneo al evitar la fase de descubrimiento.

```bash
nmap -Pn 192.168.1.100
```

### -n (Sin resolución DNS)

Desactiva la resolución inversa de DNS para las direcciones IP. Mejora significativamente la velocidad del escaneo, especialmente en redes grandes, evitando consultas DNS que pueden ser bloqueadas o ralentizar el proceso.

```bash
nmap -n 192.168.1.0/24
```

### -T[0-5] (Control de Velocidad del Escaneo)

Este parámetro controla la agresividad del escaneo. Los valores van de 0 (paranoico, muy sigiloso) a 5 (insano, muy agresivo). La elección depende del entorno y objetivos de seguridad.

| Nivel | Nombre | Descripción |
|-------|--------|-------------|
| T0 | Paranoid | Muy lento, máximo sigilo |
| T1 | Sneaky | Lento, muy sigiloso |
| T2 | Polite | Velocidad reducida |
| T3 | Normal | Equilibrio (por defecto) |
| T4 | Aggressive | Muy rápido |
| T5 | Insane | Muy agresivo, puede causar pérdida de paquetes |

### -sS (SYN Scan - Escaneo Sigiloso)

También conocido como "half-open scan", no completa el three-way handshake de TCP. Envía SYN, recibe SYN-ACK y responde con RST, evitando establecer una conexión completa. Esto lo hace más rápido, sigiloso y menos probable de ser detectado.

```bash
nmap -sS 192.168.1.100
```

### -sT (TCP Connect Scan)

Escaneo predeterminado para TCP que completa el three-way handshake completo. Establece una conexión real con la máquina objetivo. Es más detectable pero funciona en sistemas sin privilegios root.

```bash
nmap -sT 192.168.1.100
```

### -sn (Ping Sweep)

Realiza un escaneo de toda una subred para identificar qué hosts están activos, sin escanear puertos. Ideal para mapeo rápido de la red y descubrimiento de dispositivos.

```bash
nmap -sn 192.168.1.0/24
```

### -O (Detección de Sistema Operativo)

Permite a Nmap intentar determinar el sistema operativo del objetivo basándose en características de respuesta TCP/IP. Requiere privilegios administrativos y realiza múltiples pruebas, por lo que puede ser lento. No siempre es 100% preciso.

```bash
nmap -O 192.168.1.100
```

### -sV (Detección de Versiones de Servicios)

Sondea los puertos abiertos para determinar qué servicios están ejecutándose y sus versiones. Utiliza una base de datos de firmas para identificar las aplicaciones. Información crucial para identificar vulnerabilidades conocidas.

```bash
nmap -sV 192.168.1.100
```

### -b (Idle/Zombie Scan)

Técnica avanzada que utiliza un host "zombie" intermediario para realizar el escaneo, ocultando la dirección IP verdadera. Requiere un host accesible con características específicas de IP ID incrementales.

```bash
nmap -Pn -v -n -p80 -b usuario:contraseña@10.10.1.1 objetivo.com
```

---

## Técnicas de Evasión de Firewalls e IDS

En auditorías de seguridad profesionales, los firewalls e IDS (Intrusion Detection Systems) presentan desafíos significativos. Nmap ofrece múltiples técnicas para evadir estas defensas de forma ética y legal en entornos autorizados.

### --mtu (Maximum Transmission Unit)

Ajusta el tamaño de los paquetes enviados. Algunos firewalls inspeccionan solo paquetes de tamaño estándar. Al fragmentar intencionalmente los paquetes en unidades más pequeñas, es posible evadir ciertos filtros. El valor debe ser múltiplo de 8.

```bash
nmap --mtu 16 -sS 192.168.1.100
```

### --data-length (Longitud de Datos)

Añade datos adicionales a los paquetes de escaneo. Los firewalls a veces filtran basándose en tamaños de paquete predeterminados. Esta opción agrega bytes adicionales para variar el tamaño y potencialmente evadir reglas basadas en firmas.

```bash
nmap --data-length 200 192.168.1.100
```

### --source-port (Puerto de Origen)

Especifica el puerto de origen para los paquetes de escaneo. Muchas reglas de firewall permiten tráfico desde puertos específicos (como DNS en puerto 53 o SMTP en puerto 25). Esta técnica aprovecha esos agujeros.

```bash
nmap --source-port 53 192.168.1.100
```

### -D (Decoy Scan - Escaneo con Señuelos)

Envía paquetes decoy (falsos) junto con los reales para confundir a los sistemas de detección. El IDS verá múltiples fuentes de escaneo, dificultando la identificación del atacante verdadero. Puede incluir tu propia IP (ME) para referencia.

```bash
nmap -D 192.168.1.50,192.168.1.60,ME 192.168.1.100
```

### -f (Fragmented - Fragmentación de Paquetes)

Fragmenta los paquetes de escaneo en fragmentos IP más pequeños. Algunos firewalls y sistemas de detección tienen dificultades para reconstruir paquetes fragmentados, permitiendo evadir la detección. Utilizar -f dos veces aumenta más la fragmentación.

```bash
nmap -f 192.168.1.100        # Fragmentación estándar
nmap -ff 192.168.1.100       # Fragmentación más agresiva
```

### --spoof-mac (Spoofing de Dirección MAC)

Falsifica la dirección MAC de origen. Útil en redes locales para evadir listas de control de acceso basadas en MAC o para enmascarar la identidad del dispositivo. Requiere ejecutarse en modo promiscuo.

```bash
nmap --spoof-mac 00:11:22:33:44:55 192.168.1.100
```

### --min-rate (Velocidad Mínima de Paquetes)

Controla la velocidad de envío de paquetes. Valores bajos ralentizan el escaneo pero lo hacen más sigiloso. Útil para evadir sistemas de detección basados en tasa (rate-based IDS que detectan anomalías por volumen de tráfico).

```bash
nmap --min-rate 10 192.168.1.0/24
```

---

## Scripts NSE (Nmap Scripting Engine)

El NSE (Nmap Scripting Engine) permite extender las capacidades de Nmap mediante scripts personalizados escritos en Lua. Estos scripts permiten automatizar tareas complejas de reconocimiento, detección de vulnerabilidades y obtención de información detallada sobre servicios.

### Categorías de Scripts Disponibles

| Categoría | Descripción |
|-----------|-------------|
| **default** | Scripts básicos y útiles, seguros y ampliamente probados |
| **discovery** | Descubrimiento de información de la red y dispositivos |
| **safe** | Scripts seguros que no causan interrupciones o alertas |
| **intrusive** | Scripts más invasivos, pueden ser detectados por IDS |
| **vuln** | Scripts específicamente enfocados en detección de vulnerabilidades |

### -sC (Default Scripts)

Ejecuta todos los scripts de la categoría "default". Son scripts ampliamente probados y seguros que proporcionan información valiosa sin ser invasivos. Excelente punto de partida para cualquier enumeración.

```bash
nmap -sC 192.168.1.100
```

### Scripts Populares y Útiles

#### http-enum

Enumera directorios y archivos comunes en servidores web. Intenta acceder a paths típicos y reporta cuáles existen y son accesibles.

```bash
nmap --script http-enum 192.168.1.100
```

#### ftp-anon

Intenta conectarse a servidores FTP utilizando credenciales anónimas. Si tiene éxito, enumerará el contenido accesible del servidor FTP. Útil para detectar configuraciones inseguras.

```bash
nmap --script ftp-anon 192.168.1.100
```

#### smb-enum-shares

Enumera shares (recursos compartidos) en sistemas Windows mediante SMB. Identifica qué recursos están disponibles e información sobre permisos.

```bash
nmap --script smb-enum-shares 192.168.1.100
```

### Creación de Scripts Personalizados en Lua

Lua es el lenguaje de scripting utilizado por Nmap. Los scripts personalizados permiten implementar lógica de reconocimiento específica para casos de uso particulares.

#### Estructura Básica de un Script NSE

```lua
-- DESCRIPCIÓN
description = [[Script que reporta puertos TCP abiertos]]
author = "Tu Nombre"
license = "Same as Nmap--See https://nmap.org/book/man-legal.html"
categories = {"discovery"}

-- REGLA: Cuándo ejecutar el script
portrule = function(host, port)
  return port.protocol == "tcp" and port.state == "open"
end

-- ACCIÓN: Qué hacer
action = function(host, port)
  return "Puerto TCP abierto identificado"
end
```

Los campos principales son:

- **description**: Descripción del script
- **categories**: Categorías a las que pertenece
- **portrule/hostrule**: Condición para ejecutar el script
- **action**: Función principal del script

---

## Herramientas Complementarias de Enumeración

Para auditorías a gran escala que involucren múltiples hosts, las herramientas que procesan la salida XML de Nmap permiten automatizar la generación de informes visuales y la identificación de aplicaciones web.

### EyeWitness

Herramienta automatizada que captura pantallas de aplicaciones web a partir de la salida XML de Nmap. Identifica aplicaciones, detecta fingerprints de tecnologías y sugiere credenciales predeterminadas. Genera un informe HTML interactivo.

#### Instalación

```bash
sudo apt install eyewitness
```

#### Uso Básico

```bash
eyewitness --web -x resultados.xml -d salida_eyewitness
```

**Opciones útiles:**
- `-f`: Archivo de URLs
- `--single`: URL única
- `--threads`: Número de threads paralelos
- `--timeout`: Segundos de espera máximos
- `--ocr`: Extrae texto de las imágenes capturadas

### Aquatone

Similar a EyeWitness, Aquatone es una herramienta moderna escrita en Go que captura pantallas de URLs. Lee directamente archivos XML de Nmap y genera reportes HTML organizados con clustering automático de páginas similares.

#### Instalación

```bash
wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
unzip aquatone_linux_amd64_1.7.0.zip
sudo mv aquatone /usr/local/bin/
```

#### Uso Básico

```bash
cat resultados.xml | aquatone -nmap
```

Aquatone genera un archivo `aquatone_report.html` que puede ser abierto en cualquier navegador para visualizar las capturas de pantalla agrupadas.

---

## Workflow Práctico: Escaneo Completo

A continuación se presenta un flujo de trabajo recomendado para una enumeración profesional y completa:

### Paso 1: Descubrimiento de Hosts

```bash
nmap -sn -oA descubrimiento 192.168.1.0/24
```

### Paso 2: Escaneo de Puertos en Hosts Activos

```bash
nmap -sS -sV -p- -oA puertos -iL hosts_activos.txt
```

### Paso 3: Ejecución de Scripts NSE

```bash
nmap -sC --script vuln -oA scripts -iL hosts_activos.txt
```

### Paso 4: Enumeración Web (si aplica)

```bash
nmap -p 80,443,8000,8080 --open -oX web_discovery.xml -iL hosts_activos.txt
```

```bash
eyewitness --web -x web_discovery.xml -d salida_web
```


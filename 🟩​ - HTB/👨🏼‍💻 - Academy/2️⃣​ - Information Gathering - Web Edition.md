---
tags:
  - HTB-Academy
  - Penetration-Tester
---

# Introducción

---

`Web Reconnaissance` es la base de una evaluación de seguridad exhaustiva. Este proceso implica recopilar sistemática y meticulosamente información sobre un sitio web o aplicación web de destino. Piense en ello como la fase preparatoria antes de profundizar en el análisis y la explotación potencial. Forma una parte crítica de la "`Information Gathering`"fase del Proceso de Prueba de Penetración.

![Diagrama de Flujo del Proceso de Pruebas de Penetración: Pre-Compromiso, Recopilación de Información, Evaluación de Vulnerabilidad, Explotación, Post-Explotación, Movimiento Lateral, Prueba de Concepto y Post-Compromiso.](https://academy.hackthebox.com/storage/modules/144/PT-process.png)

Los objetivos principales del reconocimiento web incluyen:

- `Identifying Assets`: Descubrir todos los componentes de acceso público del objetivo, como páginas web, subdominios, direcciones IP y tecnologías utilizadas. Este paso proporciona una visión general completa de la presencia en línea del objetivo.
- `Discovering Hidden Information`: Localización de información confidencial que podría estar expuesta inadvertidamente, incluidos archivos de copia de seguridad, archivos de configuración o documentación interna. Estos hallazgos pueden revelar información valiosa y posibles puntos de entrada para los ataques.
- `Analysing the Attack Surface`: Examinar la superficie de ataque del objetivo para identificar posibles vulnerabilidades y debilidades. Esto implica evaluar las tecnologías utilizadas, las configuraciones y los posibles puntos de entrada para la explotación.
- `Gathering Intelligence`: Recopilación de información que se puede aprovechar para una mayor explotación o ataques de ingeniería social. Esto incluye identificar personal clave, direcciones de correo electrónico o patrones de comportamiento que podrían ser explotados.

Los atacantes aprovechan esta información para adaptar sus ataques, lo que les permite atacar debilidades específicas y eludir las medidas de seguridad. Por el contrario, los defensores usan recon para identificar y parchear vulnerabilidades de manera proactiva antes de que los actores maliciosos puedan aprovecharlas.

## Tipos de Reconocimiento

El reconocimiento web abarca dos metodologías fundamentales: `active` y `passive` reconocimiento. Cada enfoque ofrece distintas ventajas y desafíos, y comprender sus diferencias es crucial para la recopilación adecuada de información.

### Reconocimiento Activo

En reconocimiento activo, el atacante `directly interacts with the target system` para recopilar información. Esta interacción puede tomar varias formas:

|Técnica|Descripción|Ejemplo|Herramientas|Riesgo de Detección|
|---|---|---|---|---|
|`Port Scanning`|Identificar puertos y servicios abiertos que se ejecutan en el objetivo.|Usar Nmap para escanear un servidor web en busca de puertos abiertos como 80 (HTTP) y 443 (HTTPS).|Mapa, Masscan, Unicornscan|Alto: La interacción directa con el objetivo puede desencadenar sistemas de detección de intrusos (IDS) y firewalls.|
|`Vulnerability Scanning`|Probar el objetivo de vulnerabilidades conocidas, como software obsoleto o configuraciones erróneas.|Ejecutar Nessus contra una aplicación web para verificar si hay fallas de inyección SQL o vulnerabilidades de secuencias de comandos en sitios cruzados (XSS).|Nessus, OpenVAS, Nikto|Alto: Los escáneres de vulnerabilidad envían cargas útiles de explotación que las soluciones de seguridad pueden detectar.|
|`Network Mapping`|Mapeo de la topología de red del objetivo, incluidos los dispositivos conectados y sus relaciones.|Usar traceroute para determinar la ruta que toman los paquetes para llegar al servidor de destino, revelando posibles saltos de red e infraestructura.|Traceroute, Mapa|Medio a alto: El tráfico de red excesivo o inusual puede levantar sospechas.|
|`Banner Grabbing`|Recuperar información de banners mostrados por servicios que se ejecutan en el objetivo.|Conectarse a un servidor web en el puerto 80 y examinar el banner HTTP para identificar el software y la versión del servidor web.|Netcat, rizo|Bajo: El agarre de banner generalmente implica una interacción mínima, pero aún se puede registrar.|
|`OS Fingerprinting`|Identificar el sistema operativo que se ejecuta en el objetivo.|Uso de las capacidades de detección de SO de Nmap (`-O`) para determinar si el objetivo está ejecutando Windows, Linux u otro sistema operativo.|Mapa, Xprobe2|Bajo: Las huellas dactilares del SO suelen ser pasivas, pero se pueden detectar algunas técnicas avanzadas.|
|`Service Enumeration`|Determinar las versiones específicas de los servicios que se ejecutan en puertos abiertos.|Uso de la detección de versiones de servicio de Nmap (`-sV`) para determinar si un servidor web está ejecutando Apache 2.4.50 o Nginx 1.18.0.|Mapa|Bajo: Similar al acaparamiento de banners, la enumeración del servicio se puede registrar, pero es menos probable que active alertas.|
|`Web Spidering`|Rastrear el sitio web de destino para identificar páginas web, directorios y archivos.|Ejecutar un rastreador web como Burp Suite Spider o OWASP ZAP Spider para trazar la estructura de un sitio web y descubrir recursos ocultos.|Burp Suite Spider, OWASP ZAP Spider, Scrapy (personalizable)|De bajo a medio: Se puede detectar si el comportamiento del rastreador no está cuidadosamente configurado para imitar el tráfico legítimo.|

El reconocimiento activo proporciona una visión directa y, a menudo, más completa de la infraestructura y la postura de seguridad del objetivo. Sin embargo, también conlleva un mayor riesgo de detección, ya que las interacciones con el objetivo pueden desencadenar alertas o levantar sospechas.

### Reconocimiento Pasivo

En contraste, el reconocimiento pasivo implica recopilar información sobre el objetivo `without directly interacting` con eso. Esto se basa en analizar la información y los recursos disponibles públicamente, tales como:

|Técnica|Descripción|Ejemplo|Herramientas|Riesgo de Detección|
|---|---|---|---|---|
|`Search Engine Queries`|Utilizar motores de búsqueda para descubrir información sobre el objetivo, incluidos sitios web, perfiles de redes sociales y artículos de noticias.|Buscando en Google "`[Target Name] employees`"para encontrar información de los empleados o perfiles de redes sociales.|Google, DuckDuckGo, Bing y motores de búsqueda especializados (por ejemplo, Shodan)|Muy bajo: Las consultas de los motores de búsqueda son actividad normal de Internet y es poco probable que activen alertas.|
|`WHOIS Lookups`|Consultar bases de datos WHOIS para recuperar los detalles de registro de dominio.|Realizar una búsqueda de WHOIS en un dominio de destino para encontrar el nombre del registrante, la información de contacto y los servidores de nombres.|herramienta de línea de comandos de Whois, servicios de búsqueda de WHOIS en línea|Muy bajo: Las consultas de WHOIS son legítimas y no levantan sospechas.|
|`DNS`|Análisis de registros DNS para identificar subdominios, servidores de correo y otra infraestructura.|Usando `dig` para enumerar los subdominios de un dominio de destino.|dig, nslookup, anfitrión, dnsenum, feroz, dnsrecon|Muy bajo: Las consultas DNS son esenciales para la navegación por Internet y generalmente no se marcan como sospechosas.|
|`Web Archive Analysis`|Examinar instantáneas históricas del sitio web del objetivo para identificar cambios, vulnerabilidades o información oculta.|Usar la Wayback Machine para ver versiones anteriores de un sitio web de destino para ver cómo ha cambiado con el tiempo.|Wayback Máquina|Muy bajo: Acceder a versiones archivadas de sitios web es una actividad normal.|
|`Social Media Analysis`|Recopilación de información de plataformas de redes sociales como LinkedIn, Twitter o Facebook.|Buscar en LinkedIn empleados de una organización objetivo para conocer sus roles, responsabilidades y posibles objetivos de ingeniería social.|LinkedIn, Twitter, Facebook, herramientas OSINT especializadas|Muy bajo: El acceso a los perfiles de redes sociales públicas no se considera intrusivo.|
|`Code Repositories`|Analizar repositorios de código de acceso público como GitHub para credenciales o vulnerabilidades expuestas.|Buscar en GitHub fragmentos de código o repositorios relacionados con el destino que puedan contener información confidencial o vulnerabilidades de código.|GitHub, GitLab|Muy bajo: Los repositorios de código están destinados al acceso público, y buscarlos no es sospechoso.|

El reconocimiento pasivo generalmente se considera más sigiloso y menos propenso a activar alarmas que el reconocimiento activo. Sin embargo, puede producir información menos completa, ya que se basa en lo que ya es de acceso público.

En este módulo, profundizaremos en las herramientas y técnicas esenciales utilizadas en el reconocimiento web, comenzando con WHOIS. Comprender el protocolo WHOIS proporciona una puerta de entrada para acceder a información vital sobre registros de dominios, detalles de propiedad y la infraestructura digital de los objetivos. Este conocimiento fundamental prepara el escenario para métodos de reconocimiento más avanzados que exploraremos más adelante.


# WHOIS

---

WHOIS es un protocolo de consulta y respuesta ampliamente utilizado diseñado para acceder a bases de datos que almacenan información sobre recursos de Internet registrados. Principalmente asociado con nombres de dominio, WHOIS también puede proporcionar detalles sobre bloques de direcciones IP y sistemas autónomos. Piense en ello como una guía telefónica gigante para Internet, que le permite buscar quién posee o es responsable de varios activos en línea.

  WHOIS

```shell-session
vcrdcr@htb[/htb]$ whois inlanefreight.com

[...]
Domain Name: inlanefreight.com
Registry Domain ID: 2420436757_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.registrar.amazon
Registrar URL: https://registrar.amazon.com
Updated Date: 2023-07-03T01:11:15Z
Creation Date: 2019-08-05T22:43:09Z
[...]
```

Cada registro de WHOIS generalmente contiene la siguiente información:

- `Domain Name`: El nombre de dominio en sí (por ejemplo, example.com)
- `Registrar`: La empresa donde se registró el dominio (por ejemplo, GoDaddy, Namecheap)
- `Registrant Contact`: La persona u organización que registró el dominio.
- `Administrative Contact`: La persona responsable de administrar el dominio.
- `Technical Contact`: La persona que maneja problemas técnicos relacionados con el dominio.
- `Creation and Expiration Dates`: Cuando se registró el dominio y cuando está configurado para caducar.
- `Name Servers`: Servidores que traducen el nombre de dominio a una dirección IP.

## Historia de WHOIS

La historia de WHOIS está intrínsecamente ligada a la visión y dedicación de [Elizabeth Feinler](https://en.wikipedia.org/wiki/Elizabeth_J._Feinler), un informático que jugó un papel fundamental en la configuración de la Internet temprana.

En la década de 1970, Feinler y su equipo en el Centro de Información de Red (NIC) del Instituto de Investigación de Stanford reconocieron la necesidad de un sistema para rastrear y administrar el creciente número de recursos de red en ARPANET, el precursor de la Internet moderna. Su solución fue la creación del directorio WHOIS, una base de datos rudimentaria pero innovadora que almacenaba información sobre usuarios de red, nombres de host y nombres de dominio.

Haga clic para ampliar un poco interesante de historial de Internet si está interesado

  

## Por qué WHOIS Importa para Web Recon

Los datos de WHOIS sirven como un tesoro de información para los probadores de penetración durante la fase de reconocimiento de una evaluación. Ofrece información valiosa sobre la huella digital de la organización objetivo y las posibles vulnerabilidades:

- `Identifying Key Personnel`: Los registros de WHOIS a menudo revelan los nombres, direcciones de correo electrónico y números de teléfono de las personas responsables de administrar el dominio. Esta información se puede aprovechar para ataques de ingeniería social o para identificar objetivos potenciales para campañas de phishing.
- `Discovering Network Infrastructure`: Los detalles técnicos como los servidores de nombres y las direcciones IP proporcionan pistas sobre la infraestructura de red del objetivo. Esto puede ayudar a los probadores de penetración a identificar posibles puntos de entrada o configuraciones erróneas.
- `Historical Data Analysis`: Acceso a registros históricos de WHOIS a través de servicios como [WhoisFreaks](https://whoisfreaks.com/) puede revelar cambios en la propiedad, información de contacto o detalles técnicos a lo largo del tiempo. Esto puede ser útil para rastrear la evolución de la presencia digital del objetivo.


# Utilizando WHOIS

---

Consideremos tres escenarios para ayudar a ilustrar el valor de los datos de WHOIS.

## Escenario 1: Investigación de Phishing

Una puerta de enlace de seguridad de correo electrónico marca un correo electrónico sospechoso enviado a varios empleados dentro de una empresa. El correo electrónico afirma ser del banco de la compañía e insta a los destinatarios a hacer clic en un enlace para actualizar la información de su cuenta. Un analista de seguridad investiga el correo electrónico y comienza realizando una búsqueda de WHOIS en el dominio vinculado en el correo electrónico.

El registro de WHOIS revela lo siguiente:

- `Registration Date`: El dominio fue registrado hace solo unos días.
- `Registrant`: La información del registrante está oculta detrás de un servicio de privacidad.
- `Name Servers`: Los servidores de nombres están asociados con un proveedor de alojamiento a prueba de balas conocido que a menudo se usa para actividades maliciosas.

Esta combinación de factores plantea importantes banderas rojas para el analista. La fecha de registro reciente, la información oculta del registrante y el alojamiento sospechoso sugieren fuertemente una campaña de phishing. El analista alerta rápidamente al departamento de TI de la compañía para bloquear el dominio y advierte a los empleados sobre la estafa.

Una investigación adicional sobre el proveedor de alojamiento y las direcciones IP asociadas puede descubrir dominios de phishing adicionales o infraestructura que utiliza el actor de amenazas.

## Escenario 2: Análisis de malware

Un investigador de seguridad está analizando una nueva cepa de malware que ha infectado varios sistemas dentro de una red. El malware se comunica con un servidor remoto para recibir comandos y filtrar datos robados. Para obtener información sobre la infraestructura del actor de amenazas, el investigador realiza una búsqueda WHOIS en el dominio asociado con el servidor de comando y control (C2).

El registro de WHOIS revela:

- `Registrant`: El dominio está registrado a un individuo utilizando un servicio de correo electrónico gratuito conocido por el anonimato.
- `Location`: La dirección del solicitante de registro se encuentra en un país con una alta prevalencia de delitos cibernéticos.
- `Registrar`: El dominio fue registrado a través de un registrador con un historial de políticas de abuso laxas.

Sobre la base de esta información, el investigador concluye que el servidor C2 probablemente esté alojado en un servidor comprometido o "a prueba de balas". Luego, el investigador utiliza los datos de WHOIS para identificar al proveedor de alojamiento y notificarle sobre la actividad maliciosa.

## Escenario 3: Informe de Inteligencia de Amenazas

Una firma de ciberseguridad rastrea las actividades de un sofisticado grupo de actores de amenazas conocido por atacar a instituciones financieras. Los analistas recopilan datos de WHOIS en múltiples dominios asociados con las campañas pasadas del grupo para compilar un informe completo de inteligencia de amenazas.

Al analizar los registros de WHOIS, los analistas descubren los siguientes patrones:

- `Registration Dates`: Los dominios se registraron en grupos, a menudo poco antes de los ataques importantes.
- `Registrants`: Los solicitantes de registro utilizan varios alias e identidades falsas.
- `Name Servers`: Los dominios a menudo comparten los mismos servidores de nombre, lo que sugiere una infraestructura común.
- `Takedown History`: Muchos dominios han sido eliminados después de los ataques, lo que indica intervenciones previas de aplicación de la ley o de seguridad.

Estas ideas permiten a los analistas crear un perfil detallado de las tácticas, técnicas y procedimientos del actor de amenazas (TTP). El informe incluye indicadores de compromiso (IOC) basados en los datos de WHOIS, que otras organizaciones pueden usar para detectar y bloquear futuros ataques.

## Usando WHOIS

Antes de usar el `whois` comando, deberá asegurarse de que esté instalado en su sistema Linux. Es una utilidad disponible a través de administradores de paquetes linux, y si no está instalado, se puede instalar simplemente con

  Utilizando WHOIS

```shell-session
vcrdcr@htb[/htb]$ sudo apt update
vcrdcr@htb[/htb]$ sudo apt install whois -y
```

La forma más sencilla de acceder a los datos de WHOIS es a través de `whois` herramienta de línea de comandos. Realicemos una búsqueda de WHOIS en `facebook.com`:

  Utilizando WHOIS

```shell-session
vcrdcr@htb[/htb]$ whois facebook.com

   Domain Name: FACEBOOK.COM
   Registry Domain ID: 2320948_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.registrarsafe.com
   Registrar URL: http://www.registrarsafe.com
   Updated Date: 2024-04-24T19:06:12Z
   Creation Date: 1997-03-29T05:00:00Z
   Registry Expiry Date: 2033-03-30T04:00:00Z
   Registrar: RegistrarSafe, LLC
   Registrar IANA ID: 3237
   Registrar Abuse Contact Email: abusecomplaints@registrarsafe.com
   Registrar Abuse Contact Phone: +1-650-308-7004
   Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
   Domain Status: serverDeleteProhibited https://icann.org/epp#serverDeleteProhibited
   Domain Status: serverTransferProhibited https://icann.org/epp#serverTransferProhibited
   Domain Status: serverUpdateProhibited https://icann.org/epp#serverUpdateProhibited
   Name Server: A.NS.FACEBOOK.COM
   Name Server: B.NS.FACEBOOK.COM
   Name Server: C.NS.FACEBOOK.COM
   Name Server: D.NS.FACEBOOK.COM
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
>>> Last update of whois database: 2024-06-01T11:24:10Z <<<

[...]
Registry Registrant ID:
Registrant Name: Domain Admin
Registrant Organization: Meta Platforms, Inc.
[...]
```

La salida de WHOIS para `facebook.com` revela varios detalles clave:

1. `Domain Registration`:
    
    - `Registrar`: RegistrarSafe, LLC
    - `Creation Date`1997-03-29
    - `Expiry Date`2033-03-30
    
    Estos detalles indican que el dominio está registrado en RegistrarSafe, LLC, y ha estado activo durante un período considerable, lo que sugiere su legitimidad y presencia en línea establecida. La fecha de caducidad distante refuerza aún más su longevidad.
    
2. `Domain Owner`:
    
    - `Registrant/Admin/Tech Organization`: Meta Plataformas, Inc.
    - `Registrant/Admin/Tech Contact`: Administrador de Dominio
    
    Esta información identifica a Meta Platforms, Inc. como la organización detrás `facebook.com`y "Admin de dominio" como punto de contacto para asuntos relacionados con el dominio. Esto es consistente con la expectativa de que Facebook, una destacada plataforma de redes sociales, es propiedad de Meta Platforms, Inc.
    
3. `Domain Status`:
    
    - `clientDeleteProhibited`, `clientTransferProhibited`, `clientUpdateProhibited`, `serverDeleteProhibited`, `serverTransferProhibited`, y `serverUpdateProhibited`
    
    Estos estados indican que el dominio está protegido contra cambios, transferencias o eliminaciones no autorizadas tanto en el lado del cliente como en el del servidor. Esto pone de relieve un fuerte énfasis en la seguridad y el control sobre el dominio.
    
4. `Name Servers`:
    
    - `A.NS.FACEBOOK.COM`, `B.NS.FACEBOOK.COM`, `C.NS.FACEBOOK.COM`, `D.NS.FACEBOOK.COM`
    
    Estos servidores de nombres están todos dentro del `facebook.com` domain, lo que sugiere que Meta Platforms, Inc. gestiona su infraestructura DNS. Es una práctica común que las grandes organizaciones mantengan el control y la confiabilidad sobre su resolución de DNS.
    

En general, la producción de WHOIS para `facebook.com` se alinea con las expectativas de un dominio bien establecido y seguro propiedad de una gran organización como Meta Platforms, Inc.

Si bien el registro de WHOIS proporciona información de contacto para problemas relacionados con el dominio, es posible que no sea directamente útil para identificar empleados individuales o vulnerabilidades específicas. Esto pone de relieve la necesidad de combinar los datos de WHOIS con otras técnicas de reconocimiento para comprender la huella digital del objetivo de manera integral.



# DNS

---

El `Domain Name System` (`DNS`) actúa como el GPS de Internet, guiando su viaje en línea desde puntos de referencia memorables (nombres de dominio) hasta coordenadas numéricas precisas (Direcciones IP). Al igual que el GPS traduce un nombre de destino en latitud y longitud para la navegación, el DNS traduce nombres de dominio legibles por humanos (como `www.example.com`) en las direcciones IP numéricas (como `192.0.2.1`) que las computadoras usan para comunicarse.

Imagine navegar por una ciudad memorizando la latitud y longitud exactas de cada lugar que desee visitar. Sería increíblemente engorroso e ineficiente. DNS elimina esta complejidad al permitirnos usar nombres de dominio fáciles de recordar. Cuando escribe un nombre de dominio en su navegador, DNS actúa como su navegador, encontrando rápidamente la dirección IP correspondiente y dirigiendo su solicitud al destino correcto en Internet.

Sin DNS, navegar por el mundo en línea sería similar a conducir sin un mapa o GPS –, un esfuerzo frustrante y propenso a errores.

## Cómo funciona DNS

Imagina que quieres visitar un sitio web como `www.example.com`. Escribe este nombre de dominio amigable en su navegador, pero su computadora no entiende las palabras – habla el idioma de los números, específicamente las direcciones IP. Entonces, ¿cómo encuentra su computadora la dirección IP del sitio web? Ingrese DNS, el traductor confiable de Internet.

![Diagrama de flujo que muestra dos secciones principales: Base de Datos de Usuario y Recopilación de Datos. Incluye pasos como 'Verificar usuario', 'Enviar a la Base de Datos de Origen', 'Almacenar Datos' y 'Enviar al Sistema de UI'.](https://mermaid.ink/svg/pako:eNptkk1uwjAQha8y8rpcIItWkAAtUNQmlSrksDDxlEQQT-QfJIS4ex2nNG1arzx-n5-ex3NhBUlkEdtr0ZSwSnMFfhm36w425DTEVDfOou60do15XGJxMBCLosQtjEb3MLk8vcCMnJIP156ceA3WFIiYZ6ikgWSdwatDfQZLkKKh4wn1dnBngyZceuQxKYWFNS39jjtTWfyCvdsgb2t9c-wN4-CU_A7dy8mPjFOeYuG0qU4IK6KDa4bgLdjck9ZpZcC_20e7dWmYbRroGU-JLKxFjZCh7h88C_KCv62Sf9RFUJd87GxJurLCtsH-cssuUlfMu8blit2xGnUtKul_-NKKObMl1pizyG-l0Iec5erqOeEsZWdVsMhqh3dMk9uXLPoQR-Mr10hhMamE73L9fYqysqSfuwEKc3T9BOe0sj4)

1. `Your Computer Asks for Directions (DNS Query)`: Cuando ingresa el nombre de dominio, su computadora primero verifica su memoria (cache) para ver si recuerda la dirección IP de una visita anterior. De lo contrario, se acerca a un resolutor de DNS, generalmente proporcionado por su proveedor de servicios de Internet (ISP).
    
2. `The DNS Resolver Checks its Map (Recursive Lookup)`: El resolvedor también tiene una caché, y si no encuentra la dirección IP allí, comienza un viaje a través de la jerarquía DNS. Comienza preguntando a un servidor de nombres raíz, que es como el bibliotecario de Internet.
    
3. `Root Name Server Points the Way`: El servidor raíz no conoce la dirección exacta, pero sabe quién hace – el servidor de nombres de Dominio de Nivel Superior (TLD) responsable de la finalización del dominio (por ejemplo, .com, .org). Apunta al resolutor en la dirección correcta.
    
4. `TLD Name Server Narrows It Down`: El servidor de nombres TLD es como un mapa regional. Sabe qué servidor de nombres autorizado es responsable del dominio específico que está buscando (por ejemplo `example.com`) y envía el resolvedor allí.
    
5. `Authoritative Name Server Delivers the Address`: El servidor de nombres autorizado es la parada final. Es como la dirección del sitio web que desea. Contiene la dirección IP correcta y la envía de vuelta al resolutor.
    
6. `The DNS Resolver Returns the Information`: El resolutor recibe la dirección IP y se la da a su computadora. También lo recuerda por un tiempo (lo almacena en caché), en caso de que desee volver a visitar el sitio web pronto.
    
7. `Your Computer Connects`: Ahora que su computadora conoce la dirección IP, puede conectarse directamente al servidor web que aloja el sitio web y puede comenzar a navegar.
    

### El Archivo Hosts

El `hosts` el archivo es un archivo de texto simple que se utiliza para asignar nombres de host a direcciones IP, proporcionando un método manual de resolución de nombres de dominio que omite el proceso DNS. Mientras que DNS automatiza la traducción de nombres de dominio a direcciones IP, el `hosts` el archivo permite anulaciones directas y locales. Esto puede ser particularmente útil para el desarrollo, la solución de problemas o el bloqueo de sitios web.

El `hosts` el archivo se encuentra en `C:\Windows\System32\drivers\etc\hosts` en Windows y en `/etc/hosts` en Linux y MacOS. Cada línea en el archivo sigue el formato:

Código: txt

```txt
<IP Address>    <Hostname> [<Alias> ...]
```

Por ejemplo:

Código: txt

```txt
127.0.0.1       localhost
192.168.1.10    devserver.local
```

Para editar el `hosts` archivo, ábralo con un editor de texto utilizando privilegios administrativos/root. Agregue nuevas entradas según sea necesario y luego guarde el archivo. Los cambios surten efecto inmediatamente sin requerir un reinicio del sistema.

Los usos comunes incluyen redirigir un dominio a un servidor local para el desarrollo:

Código: txt

```txt
127.0.0.1       myapp.local
```

prueba de conectividad especificando una dirección IP:

Código: txt

```txt
192.168.1.20    testserver.local
```

o bloquear sitios web no deseados redirigiendo sus dominios a una dirección IP inexistente:

Código: txt

```txt
0.0.0.0       unwanted-site.com
```

### Es como una Carrera de Retransmisión

Piense en el proceso de DNS como una carrera de relevos. Su computadora comienza con el nombre de dominio y lo pasa al resolutor. El resolvedor luego pasa la solicitud al servidor raíz, al servidor TLD y, finalmente, al servidor autorizado, cada uno se acerca al destino. Una vez que se encuentra la dirección IP, se retransmite de nuevo por la cadena a su computadora, lo que le permite acceder al sitio web.

### Conceptos Clave DNS

En el `Domain Name System` (`DNS`), a `zone` es una parte distinta del espacio de nombres de dominio que administra una entidad o administrador específico. Piense en ello como un contenedor virtual para un conjunto de nombres de dominio. Por ejemplo, `example.com` y todos sus subdominios (como `mail.example.com` o `blog.example.com`) normalmente pertenecería a la misma zona DNS.

El archivo de zona, un archivo de texto que reside en un servidor DNS, define los registros de recursos (discutidos a continuación) dentro de esta zona, proporcionando información crucial para traducir nombres de dominio a direcciones IP.

Para ilustrar, aquí hay un ejemplo simplificado de para qué es un archivo de zona `example.com` podría parecer:

Código: zona

```dns-zone
$TTL 3600 ; Default Time-To-Live (1 hour)
@       IN SOA   ns1.example.com. admin.example.com. (
                2024060401 ; Serial number (YYYYMMDDNN)
                3600       ; Refresh interval
                900        ; Retry interval
                604800     ; Expire time
                86400 )    ; Minimum TTL

@       IN NS    ns1.example.com.
@       IN NS    ns2.example.com.
@       IN MX 10 mail.example.com.
www     IN A     192.0.2.1
mail    IN A     198.51.100.1
ftp     IN CNAME www.example.com.
```

Este archivo define los servidores de nombres autorizados (`NS` registros), servidor de correo (`MX` registro) y direcciones IP (`A` registros) para varios hosts dentro del `example.com` dominio.

Los servidores DNS almacenan varios registros de recursos, cada uno con un propósito específico en el proceso de resolución de nombres de dominio. Exploremos algunos de los conceptos de DNS más comunes:

|Concepto DNS|Descripción|Ejemplo|
|---|---|---|
|`Domain Name`|Una etiqueta legible por humanos para un sitio web u otro recurso de Internet.|`www.example.com`|
|`IP Address`|Un identificador numérico único asignado a cada dispositivo conectado a Internet.|`192.0.2.1`|
|`DNS Resolver`|Un servidor que traduce nombres de dominio en direcciones IP.|El servidor DNS de su ISP o los resolutores públicos como Google DNS (`8.8.8.8`)|
|`Root Name Server`|Los servidores de nivel superior en la jerarquía DNS.|Hay 13 servidores raíz en todo el mundo, llamados A-M: `a.root-servers.net`|
|`TLD Name Server`|Servidores responsables de dominios específicos de nivel superior (por ejemplo, .com, .org).|[Verisign](https://en.wikipedia.org/wiki/Verisign) para `.com`, [PIR](https://en.wikipedia.org/wiki/Public_Interest_Registry) para `.org`|
|`Authoritative Name Server`|El servidor que contiene la dirección IP real para un dominio.|A menudo administrado por proveedores de alojamiento o registradores de dominio.|
|`DNS Record Types`|Diferentes tipos de información almacenada en DNS.|A, AAAA, CNAME, MX, NS, TXT, etc.|

Ahora que hemos explorado los conceptos fundamentales de DNS, profundicemos en los componentes básicos de la información de DNS – los diversos tipos de registros. Estos registros almacenan diferentes tipos de datos asociados con nombres de dominio, cada uno con un propósito específico:

|Tipo de Registro|Nombre Completo|Descripción|Ejemplo de Archivo de Zona|
|---|---|---|---|
|`A`|Registro de Dirección|Asigna un nombre de host a su dirección IPv4.|`www.example.com.`EN A `192.0.2.1`|
|`AAAA`|Registro de direcciones IPv6|Asigna un nombre de host a su dirección IPv6.|`www.example.com.`EN AAAA `2001:db8:85a3::8a2e:370:7334`|
|`CNAME`|Registro de Nombre Canónico|Crea un alias para un nombre de host, apuntándolo a otro nombre de host.|`blog.example.com.`EN CNAME `webserver.example.net.`|
|`MX`|Registro de Intercambio de Correo|Especifica el servidor de correo(s) responsable de manejar el correo electrónico para el dominio.|`example.com.`EN MX 10 `mail.example.com.`|
|`NS`|Nombre Registro del Servidor|Delega una zona DNS en un servidor de nombres autorizado específico.|`example.com.`EN NS `ns1.example.com.`|
|`TXT`|Registro de Texto|Almacena información de texto arbitraria, a menudo utilizada para la verificación de dominio o políticas de seguridad.|`example.com.` EN TXT `"v=spf1 mx -all"` (Récord SPF)|
|`SOA`|Inicio del Registro de Autoridad|Especifica información administrativa sobre una zona DNS, incluido el servidor de nombres principal, el correo electrónico de la persona responsable y otros parámetros.|`example.com.`EN SOA `ns1.example.com. admin.example.com. 2024060301 10800 3600 604800 86400`|
|`SRV`|Registro de Servicio|Define el nombre de host y el número de puerto para servicios específicos.|`_sip._udp.example.com.`EN SRV 10 5 5060 `sipserver.example.com.`|
|`PTR`|Registro de Puntero|Se utiliza para búsquedas DNS inversas, asignando una dirección IP a un nombre de host.|`1.2.0.192.in-addr.arpa.`EN PTR `www.example.com.`|

El "`IN`"en los ejemplos significa "Internet." Es un campo de clase en los registros DNS que especifica la familia de protocolos. En la mayoría de los casos, verás "`IN`"utilizado, ya que denota el conjunto de protocolos de Internet (IP) utilizado para la mayoría de los nombres de dominio. Existen otros valores de clase (por ejemplo `CH` para Chaosnet, `HS` para Hesíodo), pero rara vez se utilizan en configuraciones DNS modernas.

En esencia, "`IN`"es simplemente una convención que indica que el registro se aplica a los protocolos de Internet estándar que usamos hoy en día. Si bien puede parecer un detalle adicional, comprender su significado proporciona una comprensión más profunda de la estructura de registros DNS.

## Por qué DNS Importa para Web Recon

DNS no es simplemente un protocolo técnico para traducir nombres de dominio; es un componente crítico de la infraestructura de un objetivo que se puede aprovechar para descubrir vulnerabilidades y obtener acceso durante una prueba de penetración:

- `Uncovering Assets`: Los registros DNS pueden revelar una gran cantidad de información, incluidos subdominios, servidores de correo y registros de servidores de nombres. Por ejemplo, a `CNAME` registro que apunta a un servidor obsoleto (`dev.example.com` CNAME `oldserver.example.net`) podría conducir a un sistema vulnerable.
- `Mapping the Network Infrastructure`: Puede crear un mapa completo de la infraestructura de red del objetivo mediante el análisis de datos DNS. Por ejemplo, identificar los servidores de nombres (`NS` los registros) para un dominio pueden revelar el proveedor de alojamiento utilizado, mientras que un `A` registro para `loadbalancer.example.com` puede identificar un equilibrador de carga. Esto le ayuda a comprender cómo se conectan los diferentes sistemas, identificar el flujo de tráfico e identificar posibles puntos de estrangulamiento o debilidades que podrían explotarse durante una prueba de penetración.
- `Monitoring for Changes`: El monitoreo continuo de los registros DNS puede revelar cambios en la infraestructura del objetivo a lo largo del tiempo. Por ejemplo, la aparición repentina de un nuevo subdominio (`vpn.example.com`) podría indicar un nuevo punto de entrada en la red, mientras que a `TXT` registro que contiene un valor como `_1password=...` sugiere fuertemente que la organización está utilizando 1Password, que podría aprovecharse para ataques de ingeniería social o campañas de phishing dirigidas.


# Digando DNS

---

Habiendo establecido una sólida comprensión de los fundamentos del DNS y sus diversos tipos de registros, ahora hagamos la transición a lo práctico. Esta sección explorará las herramientas y técnicas para aprovechar el DNS para el reconocimiento web.

## Herramientas DNS

El reconocimiento de DNS implica la utilización de herramientas especializadas diseñadas para consultar servidores DNS y extraer información valiosa. Estas son algunas de las herramientas más populares y versátiles en el arsenal de profesionales de reconocimiento web:

|Herramienta|Características Clave|Casos de Uso|
|---|---|---|
|`dig`|Herramienta de búsqueda de DNS versátil que admite varios tipos de consultas (A, MX, NS, TXT, etc.) y salida detallada.|Consultas DNS manuales, transferencias de zona (si está permitido), solución de problemas de DNS y análisis en profundidad de los registros DNS.|
|`nslookup`|Herramienta de búsqueda de DNS más simple, principalmente para registros A, AAAA y MX.|Consultas DNS básicas, verificaciones rápidas de la resolución del dominio y registros del servidor de correo.|
|`host`|Herramienta de búsqueda de DNS simplificada con salida concisa.|Verificaciones rápidas de registros A, AAAA y MX.|
|`dnsenum`|Herramienta automatizada de enumeración de DNS, ataques de diccionario, fuerza bruta, transferencias de zona (si está permitido).|Descubrir subdominios y recopilar información de DNS de manera eficiente.|
|`fierce`|Herramienta de reconocimiento y enumeración de subdominios de DNS con búsqueda recursiva y detección de comodines.|Interfaz fácil de usar para el reconocimiento de DNS, identificando subdominios y objetivos potenciales.|
|`dnsrecon`|Combina múltiples técnicas de reconocimiento de DNS y admite varios formatos de salida.|Cumeración completa de DNS, identificación de subdominios y recopilación de registros DNS para un análisis más detallado.|
|`theHarvester`|Herramienta OSINT que recopila información de varias fuentes, incluidos registros DNS (direcciones de correo electrónico).|Recopilación de direcciones de correo electrónico, información de empleados y otros datos asociados con un dominio de múltiples fuentes.|
|`Online DNS Lookup Services`|Interfaces fáciles de usar para realizar búsquedas de DNS.|Búsquedas de DNS rápidas y fáciles, convenientes cuando las herramientas de línea de comandos no están disponibles, verificando la disponibilidad del dominio o la información básica|

## El Domain Information Groper

El `dig` comando (`Domain Information Groper`) es una utilidad versátil y potente para consultar servidores DNS y recuperar varios tipos de registros DNS. Su flexibilidad y su salida detallada y personalizable lo convierten en una opción ideal.

### Comandos de excavación comunes

|Comando|Descripción|
|---|---|
|`dig domain.com`|Realiza una búsqueda predeterminada de registros A para el dominio.|
|`dig domain.com A`|Recupera la dirección IPv4 (Un registro) asociada con el dominio.|
|`dig domain.com AAAA`|Recupera la dirección IPv6 (Registro AAAA) asociada con el dominio.|
|`dig domain.com MX`|Encuentra los servidores de correo (Registros MX) responsables del dominio.|
|`dig domain.com NS`|Identifica los servidores de nombres autorizados para el dominio.|
|`dig domain.com TXT`|Recupera cualquier registro TXT asociado con el dominio.|
|`dig domain.com CNAME`|Recupera el registro de nombre canónico (CNAME) para el dominio.|
|`dig domain.com SOA`|Recupera el inicio del registro de autoridad (SOA) para el dominio.|
|`dig @1.1.1.1 domain.com`|Especifica un servidor de nombres específico para consultar; en este caso 1.1.1.1|
|`dig +trace domain.com`|Muestra la ruta completa de la resolución DNS.|
|`dig -x 192.168.1.1`|Realiza una búsqueda inversa en la dirección IP 192.168.1.1 para encontrar el nombre de host asociado. Es posible que deba especificar un servidor de nombres.|
|`dig +short domain.com`|Proporciona una respuesta breve y concisa a la consulta.|
|`dig +noall +answer domain.com`|Muestra solo la sección de respuesta de la salida de consulta.|
|`dig domain.com ANY`|Recupera todos los registros DNS disponibles para el dominio (Nota: Muchos servidores DNS ignoran `ANY` consultas para reducir la carga y prevenir el abuso, según [RFC 8482](https://datatracker.ietf.org/doc/html/rfc8482)).|

Precaución: Algunos servidores pueden detectar y bloquear consultas DNS excesivas. Tenga cuidado y respete los límites de la tasa. Siempre obtenga permiso antes de realizar un amplio reconocimiento de DNS en un objetivo.

## DNS Groping

  Digando DNS

```shell-session
vcrdcr@htb[/htb]$ dig google.com

; <<>> DiG 9.18.24-0ubuntu0.22.04.1-Ubuntu <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16449
;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0
;; WARNING: recursion requested but not available

;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             0       IN      A       142.251.47.142

;; Query time: 0 msec
;; SERVER: 172.23.176.1#53(172.23.176.1) (UDP)
;; WHEN: Thu Jun 13 10:45:58 SAST 2024
;; MSG SIZE  rcvd: 54
```

Esta salida es el resultado de una consulta DNS utilizando el `dig` comando para el dominio `google.com`. El comando se ejecutó en un sistema en ejecución `DiG` versión `9.18.24-0ubuntu0.22.04.1-Ubuntu`. La salida se puede dividir en cuatro secciones clave:

1. Encabezado
    
    - `;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16449`: Esta línea indica el tipo de consulta (`QUERY`), el estado exitoso (`NOERROR`), y un identificador único (`16449`) para esta consulta específica.
        
        - `;; flags: qr rd ad; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0`: Esto describe las banderas en el encabezado DNS:
            - `qr`: Indicador de respuesta de consulta: indica que esta es una respuesta.
            - `rd`: Recursión Bandera deseada - significa que se solicitó la recursión.
            - `ad`: Authentic Data flag - significa que el resolutor considera que los datos son auténticos.
            - Los números restantes indican el número de entradas en cada sección de la respuesta DNS: 1 pregunta, 1 respuesta, 0 registros de autoridad y 0 registros adicionales.
    - `;; WARNING: recursion requested but not available`: Esto indica que se solicitó la recursión, pero el servidor no la admite.
        
2. Sección de Preguntas
    
    - `;google.com. IN A`: Esta línea especifica la pregunta: "Para qué sirve la dirección IPv4 (Un registro) `google.com`?"
3. Sección de Respuesta
    
    - `google.com. 0 IN A 142.251.47.142`: Esta es la respuesta a la consulta. Indica que la dirección IP asociada con `google.com` es `142.251.47.142`. El '`0`' representa el `TTL` (time-to-live), indicando cuánto tiempo se puede almacenar en caché el resultado antes de ser actualizado.
4. Pie de página
    
    - `;; Query time: 0 msec`: Esto muestra el tiempo que tardó la consulta en procesarse y la respuesta en recibirse (0 milisegundos).
        
    - `;; SERVER: 172.23.176.1#53(172.23.176.1) (UDP)`: Esto identifica el servidor DNS que proporcionó la respuesta y el protocolo utilizado (UDP).
        
    - `;; WHEN: Thu Jun 13 10:45:58 SAST 2024`: Esta es la marca de tiempo de cuando se realizó la consulta.
        
    - `;; MSG SIZE rcvd: 54`: Esto indica el tamaño del mensaje DNS recibido (54 bytes).
        

An `opt pseudosection` a veces puede existir en un `dig` consulta. Esto se debe a los Mecanismos de Extensión para DNS (`EDNS`), que permite características adicionales como tamaños de mensajes más grandes y extensiones de seguridad DNS (`DNSSEC`) soporte.

Si solo desea la respuesta a la pregunta, sin ninguna otra información, puede consultar `dig` usando `+short`:


# Subdominios

---

Al explorar los registros DNS, nos hemos centrado principalmente en el dominio principal (por ejemplo `example.com`) y su información asociada. Sin embargo, debajo de la superficie de este dominio primario se encuentra una red potencial de subdominios. Estos subdominios son extensiones del dominio principal, a menudo creadas para organizar y separar diferentes secciones o funcionalidades de un sitio web. Por ejemplo, una empresa podría usar `blog.example.com` por su blog, `shop.example.com` para su tienda en línea, o `mail.example.com` por sus servicios de correo electrónico.

## ¿Por qué es esto importante para el reconocimiento web?

Los subdominios a menudo alojan información valiosa y recursos que no están directamente vinculados desde el sitio web principal. Esto puede incluir:

- `Development and Staging Environments`: Las empresas a menudo usan subdominios para probar nuevas funciones o actualizaciones antes de implementarlas en el sitio principal. Debido a las medidas de seguridad relajadas, estos entornos a veces contienen vulnerabilidades o exponen información confidencial.
- `Hidden Login Portals`: Los subdominios pueden alojar paneles administrativos u otras páginas de inicio de sesión que no están destinadas a ser de acceso público. Los atacantes que buscan acceso no autorizado pueden encontrarlos como objetivos atractivos.
- `Legacy Applications`: Las aplicaciones web más antiguas y olvidadas pueden residir en subdominios, que potencialmente contienen software obsoleto con vulnerabilidades conocidas.
- `Sensitive Information`: Los subdominios pueden exponer inadvertidamente documentos confidenciales, datos internos o archivos de configuración que podrían ser valiosos para los atacantes.

## Enumeración de Subdominio

`Subdomain enumeration` es el proceso de identificar y enumerar sistemáticamente estos subdominios. Desde una perspectiva de DNS, los subdominios están representados típicamente por `A` (o `AAAA` para registros IPv6), que asignan el nombre del subdominio a su dirección IP correspondiente. Además, `CNAME` los registros se pueden usar para crear alias para subdominios, apuntándolos a otros dominios o subdominios. Hay dos enfoques principales para la enumeración de subdominios:

### 1. Enumeración Activa de Subdominio

Esto implica interactuar directamente con los servidores DNS del dominio de destino para descubrir subdominios. Un método es intentar un `DNS zone transfer`, donde un servidor mal configurado puede filtrar inadvertidamente una lista completa de subdominios. Sin embargo, debido a las medidas de seguridad más estrictas, esto rara vez tiene éxito.

Una técnica activa más común es `brute-force enumeration`, que implica probar sistemáticamente una lista de posibles nombres de subdominio contra el dominio de destino. Herramientas como `dnsenum`, `ffuf`, y `gobuster` puede automatizar este proceso, utilizando listas de palabras de nombres de subdominios comunes o listas generadas a medida basadas en patrones específicos.

### 2. Enumeración Pasiva de Subdominio

Esto se basa en fuentes externas de información para descubrir subdominios sin consultar directamente los servidores DNS del objetivo. Un recurso valioso es `Certificate Transparency (CT) logs`, repositorios públicos de certificados SSL/TLS. Estos certificados a menudo incluyen una lista de subdominios asociados en su campo Nombre Alternativo de Asunto (SAN), proporcionando un tesoro de objetivos potenciales.

Otro enfoque pasivo implica utilizar `search engines` como Google o DuckDuckGo. Empleando operadores de búsqueda especializados (por ejemplo `site:`), puede filtrar los resultados para mostrar solo los subdominios relacionados con el dominio de destino.

Además, varias bases de datos y herramientas en línea agregan datos DNS de múltiples fuentes, lo que le permite buscar subdominios sin interactuar directamente con el objetivo.

Cada uno de estos métodos tiene sus fortalezas y debilidades. La enumeración activa ofrece más control y potencial para el descubrimiento integral, pero puede ser más detectable. La enumeración pasiva es más sigilosa, pero es posible que no descubra todos los subdominios existentes. La combinación de ambos enfoques proporciona una estrategia de enumeración de subdominios más completa y efectiva.


# Subdominio Bruteforcing

---

`Subdomain Brute-Force Enumeration`es una poderosa técnica de descubrimiento de subdominios activos que aprovecha listas predefinidas de nombres de subdominios potenciales. Este enfoque prueba sistemáticamente estos nombres contra el dominio de destino para identificar subdominios válidos. Al usar listas de palabras cuidadosamente diseñadas, puede aumentar significativamente la eficiencia y la efectividad de sus esfuerzos de descubrimiento de subdominios.

El proceso se divide en cuatro pasos:

1. `Wordlist Selection`: El proceso comienza con la selección de una lista de palabras que contiene posibles nombres de subdominio. Estas listas de palabras pueden ser:
    - `General-Purpose`: Que contiene una amplia gama de nombres de subdominio comunes (por ejemplo `dev`, `staging`, `blog`, `mail`, `admin`, `test`). Este enfoque es útil cuando no conoce las convenciones de nombres del objetivo.
    - `Targeted`: Centrado en industrias, tecnologías o patrones de nombres específicos relevantes para el objetivo. Este enfoque es más eficiente y reduce las posibilidades de falsos positivos.
    - `Custom`: Puede crear su propia lista de palabras basada en palabras clave, patrones o inteligencia específicos recopilados de otras fuentes.
2. `Iteration and Querying`: Un script o herramienta itera a través de la lista de palabras, agregando cada palabra o frase al dominio principal (por ejemplo `example.com`) para crear nombres de subdominio potenciales (por ejemplo `dev.example.com`, `staging.example.com`).
3. `DNS Lookup`: Se realiza una consulta DNS para cada subdominio potencial para verificar si se resuelve en una dirección IP. Esto se hace típicamente usando el tipo de registro A o AAAA.
4. `Filtering and Validation`: Si un subdominio se resuelve correctamente, se agrega a una lista de subdominios válidos. Se pueden tomar más medidas de validación para confirmar la existencia y funcionalidad del subdominio (por ejemplo, intentando acceder a él a través de un navegador web).

Hay varias herramientas disponibles que sobresalen en la enumeración de fuerza bruta:

|Herramienta|Descripción|
|---|---|
|[dnseno](https://github.com/fwaeytens/dnsenum)|Herramienta completa de enumeración de DNS que admite ataques de diccionario y fuerza bruta para descubrir subdominios.|
|[feroz](https://github.com/mschwager/fierce)|Herramienta fácil de usar para el descubrimiento recursivo de subdominios, con detección de comodines y una interfaz fácil de usar.|
|[dnsrecon](https://github.com/darkoperator/dnsrecon)|Herramienta versátil que combina múltiples técnicas de reconocimiento DNS y ofrece formatos de salida personalizables.|
|[amasar](https://github.com/owasp-amass/amass)|Herramienta mantenida activamente centrada en el descubrimiento de subdominios, conocida por su integración con otras herramientas y fuentes de datos extensas.|
|[activo](https://github.com/tomnomnom/assetfinder)|Herramienta simple pero efectiva para encontrar subdominios utilizando diversas técnicas, ideal para escaneos rápidos y livianos.|
|[pastos](https://github.com/d3mondev/puredns)|Herramienta de fuerza bruta DNS potente y flexible, capaz de resolver y filtrar resultados de manera efectiva.|

### DNSEnum

`dnsenum`es una herramienta de línea de comandos versátil y ampliamente utilizada escrita en Perl. Es un completo kit de herramientas para el reconocimiento de DNS, que proporciona varias funcionalidades para recopilar información sobre la infraestructura DNS de un dominio de destino y los subdominios potenciales. La herramienta ofrece varias funciones clave:

- `DNS Record Enumeration`: `dnsenum` puede recuperar varios registros DNS, incluidos los registros A, AAAA, NS, MX y TXT, proporcionando una visión general completa de la configuración DNS del objetivo.
- `Zone Transfer Attempts`: La herramienta intenta automáticamente transferencias de zona desde servidores de nombres descubiertos. Si bien la mayoría de los servidores están configurados para evitar transferencias de zona no autorizadas, un intento exitoso puede revelar un tesoro de información de DNS.
- `Subdomain Brute-Forcing`: `dnsenum` admite la enumeración de fuerza bruta de subdominios utilizando una lista de palabras. Esto implica probar sistemáticamente nombres de subdominios potenciales contra el dominio de destino para identificar los válidos.
- `Google Scraping`: La herramienta puede raspar los resultados de búsqueda de Google para encontrar subdominios adicionales que podrían no aparecer directamente en los registros DNS.
- `Reverse Lookup`: `dnsenum` puede realizar búsquedas de DNS inversas para identificar dominios asociados con una dirección IP determinada, lo que podría revelar otros sitios web alojados en el mismo servidor.
- `WHOIS Lookups`: La herramienta también puede realizar consultas de WHOIS para recopilar información sobre la propiedad del dominio y los detalles de registro.

Veamos `dnsenum` en acción, demostrando cómo enumerar subdominios para nuestro objetivo `inlanefreight.com`. En esta demostración, usaremos el `subdomains-top1million-5000.txt` lista de palabras de [Seclistas](https://github.com/danielmiessler/SecLists), que contiene los 5000 subdominios más comunes.

Código: bash

```bash
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r
```

En este comando:

- `dnsenum --enum inlanefreight.com`: Especificamos el dominio de destino que queremos enumerar, junto con un acceso directo para algunas opciones de ajuste `--enum`.
- `-f /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt`: Indicamos el camino a la lista de palabras SecLists que usaremos para la fuerza bruta. Ajuste la ruta si su instalación de SecLists es diferente.
- `-r`: Esta opción permite la fuerza bruta del subdominio recursivo, lo que significa que si `dnsenum` encuentra un subdominio, luego intentará enumerar los subdominios de ese subdominio.

  Subdominio Bruteforcing

```shell-session
vcrdcr@htb[/htb]$ dnsenum --enum inlanefreight.com -f  /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt 

dnsenum VERSION:1.2.6

-----   inlanefreight.com   -----


Host's addresses:
__________________

inlanefreight.com.                       300      IN    A        134.209.24.248

[...]

Brute forcing with /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt:
_______________________________________________________________________________________

www.inlanefreight.com.                   300      IN    A        134.209.24.248
support.inlanefreight.com.               300      IN    A        134.209.24.248
[...]


done.
```

# Transferencias de Zona DNS

---

Si bien la fuerza bruta puede ser un enfoque fructífero, existe un método menos invasivo y potencialmente más eficiente para descubrir subdominios – transferencias de zona DNS. Este mecanismo, diseñado para replicar registros DNS entre servidores de nombres, puede convertirse inadvertidamente en una mina de oro de información para miradas indiscretas si está mal configurado.

## Qué es una Transferencia de Zona

Una transferencia de zona DNS es esencialmente una copia al por mayor de todos los registros DNS dentro de una zona (un dominio y sus subdominios) de un servidor de nombres a otro. Este proceso es esencial para mantener la consistencia y la redundancia en los servidores DNS. Sin embargo, si no están adecuadamente aseguradas, las partes no autorizadas pueden descargar todo el archivo de zona, revelando una lista completa de subdominios, sus direcciones IP asociadas y otros datos confidenciales de DNS.

![Diagrama que muestra la transferencia de datos entre servidores secundarios y primarios. Incluye pasos: Solicitud XML, Registro XML, bucle para reintentos, Informe XML y AOK (Conocimiento).](https://mermaid.ink/svg/pako:eNqNkc9qwzAMxl9F-JSx7gV8KISWXcY2aHYYwxdjK39obGWKvBFK333ukg5aGNQnW9b3Q_q-g3LkUWk14mfC6HDb2YZtMBHyGdFR9JanCvkL-WG9vh-4C38FDeX74w52J-0oUHxQRHhjG8ca-W5mXAgy4YqpoXotM8EReygqsSxANZRJWuJOpoXSEw0gC3ku3QTfvlQLfBZh9DeOdbELbCgMPQr-58u1LZsnKEq3j_Tdo28wYJS8iVqpgBxs57PjhxPLKGnzr1E6XzNxb5SJx9xnk1A1Rae0cMKVYkpNq3Rt-zG_0uCtnLM6t6DvhPh5zvM31uMPG8qm-A)

1. `Zone Transfer Request (AXFR)`: El servidor DNS secundario inicia el proceso enviando una solicitud de transferencia de zona al servidor principal. Esta solicitud normalmente usa el tipo AXFR (Transferencia de Zona Completa).
2. `SOA Record Transfer`: Al recibir la solicitud (y potencialmente autenticar el servidor secundario), el servidor primario responde enviando su registro de Inicio de Autoridad (SOA). El registro SOA contiene información vital sobre la zona, incluido su número de serie, que ayuda al servidor secundario a determinar si sus datos de zona están actualizados.
3. `DNS Records Transmission`: El servidor primario transfiere todos los registros DNS de la zona al servidor secundario, uno por uno. Esto incluye registros como A, AAAA, MX, CNAME, NS y otros que definen los subdominios del dominio, servidores de correo, servidores de nombres y otras configuraciones.
4. `Zone Transfer Complete`: Una vez que se han transmitido todos los registros, el servidor principal señala el final de la transferencia de zona. Esta notificación informa al servidor secundario que ha recibido una copia completa de los datos de la zona.
5. `Acknowledgement (ACK)`: El servidor secundario envía un mensaje de acuse de recibo al servidor principal, confirmando la recepción y el procesamiento exitosos de los datos de la zona. Esto completa el proceso de transferencia de zona.

## La Vulnerabilidad de Transferencia de Zona

Si bien las transferencias de zona son esenciales para la administración legítima de DNS, un servidor DNS mal configurado puede transformar este proceso en una vulnerabilidad de seguridad significativa. El problema central radica en los controles de acceso que rigen quién puede iniciar una transferencia de zona.

En los primeros días de Internet, permitir que cualquier cliente solicitara una transferencia de zona desde un servidor DNS era una práctica común. Este enfoque abierto simplificó la administración pero abrió un agujero de seguridad enorme. Significaba que cualquier persona, incluidos los actores maliciosos, podía pedirle a un servidor DNS una copia completa de su archivo de zona, que contiene una gran cantidad de información confidencial.

La información obtenida de una transferencia de zona no autorizada puede ser invaluable para un atacante. Revela un mapa completo de la infraestructura DNS del objetivo, que incluye:

- `Subdomains`: Una lista completa de subdominios, muchos de los cuales pueden no estar vinculados desde el sitio web principal o fácilmente detectables a través de otros medios. Estos subdominios ocultos podrían alojar servidores de desarrollo, entornos de puesta en escena, paneles administrativos u otros recursos sensibles.
- `IP Addresses`: Las direcciones IP asociadas con cada subdominio, proporcionando objetivos potenciales para reconocimiento o ataques adicionales.
- `Name Server Records`: Detalles sobre los servidores de nombres autorizados para el dominio, revelando el proveedor de alojamiento y posibles configuraciones erróneas.

### Remediación

Afortunadamente, la conciencia de esta vulnerabilidad ha crecido, y la mayoría de los administradores de servidores DNS han mitigado el riesgo. Los servidores DNS modernos generalmente están configurados para permitir transferencias de zona solo a servidores secundarios de confianza, asegurando que los datos de zonas confidenciales permanezcan confidenciales.

Sin embargo, las configuraciones erróneas aún pueden ocurrir debido a errores humanos o prácticas obsoletas. Esta es la razón por la cual intentar una transferencia de zona (con la autorización adecuada) sigue siendo una técnica de reconocimiento valiosa. Incluso si no tiene éxito, el intento puede revelar información sobre la configuración del servidor DNS y la postura de seguridad.

#### Transferencias de Zona de Explotación

Puedes usar el `dig` comando para solicitar una transferencia de zona:

  Transferencias de Zona DNS

```shell-session
vcrdcr@htb[/htb]$ dig axfr @nsztm1.digi.ninja zonetransfer.me
```

Este comando instruye `dig` para solicitar una transferencia de zona completa (`axfr`) del servidor DNS responsable de `zonetransfer.me`. Si el servidor está mal configurado y permite la transferencia, recibirá una lista completa de registros DNS para el dominio, incluidos todos los subdominios.

  Transferencias de Zona DNS

```shell-session
vcrdcr@htb[/htb]$ dig axfr @nsztm1.digi.ninja zonetransfer.me

; <<>> DiG 9.18.12-1~bpo11+1-Debian <<>> axfr @nsztm1.digi.ninja zonetransfer.me
; (1 server found)
;; global options: +cmd
zonetransfer.me.	7200	IN	SOA	nsztm1.digi.ninja. robin.digi.ninja. 2019100801 172800 900 1209600 3600
zonetransfer.me.	300	IN	HINFO	"Casio fx-700G" "Windows XP"
zonetransfer.me.	301	IN	TXT	"google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"
zonetransfer.me.	7200	IN	MX	0 ASPMX.L.GOOGLE.COM.
...
zonetransfer.me.	7200	IN	A	5.196.105.14
zonetransfer.me.	7200	IN	NS	nsztm1.digi.ninja.
zonetransfer.me.	7200	IN	NS	nsztm2.digi.ninja.
_acme-challenge.zonetransfer.me. 301 IN	TXT	"6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"
_sip._tcp.zonetransfer.me. 14000 IN	SRV	0 0 5060 www.zonetransfer.me.
14.105.196.5.IN-ADDR.ARPA.zonetransfer.me. 7200	IN PTR www.zonetransfer.me.
asfdbauthdns.zonetransfer.me. 7900 IN	AFSDB	1 asfdbbox.zonetransfer.me.
asfdbbox.zonetransfer.me. 7200	IN	A	127.0.0.1
asfdbvolume.zonetransfer.me. 7800 IN	AFSDB	1 asfdbbox.zonetransfer.me.
canberra-office.zonetransfer.me. 7200 IN A	202.14.81.230
...
;; Query time: 10 msec
;; SERVER: 81.4.108.41#53(nsztm1.digi.ninja) (TCP)
;; WHEN: Mon May 27 18:31:35 BST 2024
;; XFR size: 50 records (messages 1, bytes 2085)
```

`zonetransfer.me` es un servicio específicamente configurado para demostrar los riesgos de las transferencias de zona para que el `dig` el comando devolverá el registro de zona completo.


# Hosts Virtuales

---

Una vez que el DNS dirige el tráfico al servidor correcto, la configuración del servidor web se vuelve crucial para determinar cómo se manejan las solicitudes entrantes. Los servidores web como Apache, Nginx o IIS están diseñados para alojar múltiples sitios web o aplicaciones en un solo servidor. Lo logran a través del alojamiento virtual, lo que les permite diferenciar entre dominios, subdominios o incluso sitios web separados con contenido distinto.

## Cómo Funcionan los Anfitriones Virtuales: Comprender los VHosts y Subdominios

En el centro de `virtual hosting` es la capacidad de los servidores web para distinguir entre múltiples sitios web o aplicaciones que comparten la misma dirección IP. Esto se logra aprovechando el `HTTP Host` encabezado, una información incluida en cada `HTTP` solicitud enviada por un navegador web.

La diferencia clave entre `VHosts` y `subdomains` es su relación con el `Domain Name System (DNS)` y la configuración del servidor web.

- `Subdomains`: Estas son extensiones de un nombre de dominio principal (por ejemplo `blog.example.com` es un subdominio de `example.com`). `Subdomains` típicamente tienen los suyos `DNS records`, señalando la misma dirección IP que el dominio principal o una diferente. Se pueden utilizar para organizar diferentes secciones o servicios de un sitio web.
- `Virtual Hosts` (`VHosts`): Los hosts virtuales son configuraciones dentro de un servidor web que permiten alojar múltiples sitios web o aplicaciones en un solo servidor. Pueden asociarse con dominios de nivel superior (por ejemplo `example.com`) o subdominios (por ejemplo `dev.example.com`). Cada host virtual puede tener su propia configuración separada, lo que permite un control preciso sobre cómo se manejan las solicitudes.

Si un host virtual no tiene un registro DNS, puede acceder a él modificando el `hosts` archive en su máquina local. El `hosts` el archivo le permite asignar un nombre de dominio a una dirección IP manualmente, evitando la resolución DNS.

Los sitios web a menudo tienen subdominios que no son públicos y no aparecerán en los registros DNS. Estos `subdomains` solo son accesibles internamente o a través de configuraciones específicas. `VHost fuzzing` es una técnica para descubrir público y no público `subdomains` y `VHosts` probando varios nombres de host contra una dirección IP conocida.

Los hosts virtuales también se pueden configurar para usar diferentes dominios, no solo subdominios. Por ejemplo:

Código: apacheconf

```apacheconf
# Example of name-based virtual host configuration in Apache
<VirtualHost *:80>
    ServerName www.example1.com
    DocumentRoot /var/www/example1
</VirtualHost>

<VirtualHost *:80>
    ServerName www.example2.org
    DocumentRoot /var/www/example2
</VirtualHost>

<VirtualHost *:80>
    ServerName www.another-example.net
    DocumentRoot /var/www/another-example
</VirtualHost>
```

Aquí, `example1.com`, `example2.org`, y `another-example.net` son dominios distintos alojados en el mismo servidor. El servidor web utiliza el `Host` encabezado para servir el contenido apropiado basado en el nombre de dominio solicitado.

### Servidor VHost Lookup

A continuación se ilustra el proceso de cómo un servidor web determina el contenido correcto para servir en función de la `Host` encabezado:

![Diagrama de secuencia que muestra las interacciones entre Browser, WebServer, VirtualHostConfig y DocumentRoot. Incluye solicitud HTTP, respuesta del servidor y pasos de acceso a archivos.](https://mermaid.ink/svg/pako:eNqNUsFuwjAM_ZUop00CPqAHDhubuCBNBW2XXrzUtNFap3McOoT496WUVUA3aTkltp_f84sP2rgcdaI9fgYkgwsLBUOdkYqnARZrbAMk6oFd65HHiTd8XyPvfku9WpYA1dJ5eXS0tcW4ZOFMqJEkdU4y6vNnqul8PvRO1HKzeVFpp9KLumvbdmapAsItoy1KmRlX3_fwAXTd4OkLakuoOjVqiZAj_7_PaJJEPVvK1QrElJYK1UcDg1h3HmOEmV4LSlEC0-CA6i24Zb406IRhizuM7BV6BVFCit4FNuh77GX9DeGfmEu-s_mD4b5x5PH2Y4aqhfVNBftufomsGemJrpFrsHncqkOHy7SUWGOmk3jNgT8yndEx1kEQt96T0YlwwIlmF4pSJ1uofHyFJgf52cchirkVx6t-aU-7e_wG--_4bQ)

1. `Browser Requests a Website`: Cuando ingresa un nombre de dominio (por ejemplo, `www.inlanefreight.com`) en su navegador, inicia una solicitud HTTP al servidor web asociado con la dirección IP de ese dominio.
2. `Host Header Reveals the Domain`: El navegador incluye el nombre de dominio en la solicitud `Host` encabezado, que actúa como una etiqueta para informar al servidor web qué sitio web se está solicitando.
3. `Web Server Determines the Virtual Host`: El servidor web recibe la solicitud, examina el `Host` encabezado y consulta su configuración de host virtual para encontrar una entrada coincidente para el nombre de dominio solicitado.
4. `Serving the Right Content`: Al identificar la configuración correcta del host virtual, el servidor web recupera los archivos y recursos correspondientes asociados con ese sitio web desde la raíz de su documento y los envía de vuelta al navegador como respuesta HTTP.

En esencia, el `Host` el encabezado funciona como un interruptor, lo que permite al servidor web determinar dinámicamente qué sitio web servir en función del nombre de dominio solicitado por el navegador.

### Tipos de Alojamiento Virtual

Hay tres tipos principales de alojamiento virtual, cada uno con sus ventajas e inconvenientes:

1. `Name-Based Virtual Hosting`: Este método se basa únicamente en el `HTTP Host header` para distinguir entre sitios web. Es el método más común y flexible, ya que no requiere múltiples direcciones IP. Es rentable, fácil de configurar y es compatible con la mayoría de los servidores web modernos. Sin embargo, requiere que el servidor web admita el nombre basado `virtual hosting` y puede tener limitaciones con ciertos protocolos como `SSL/TLS`.
2. `IP-Based Virtual Hosting`: Este tipo de alojamiento asigna una dirección IP única a cada sitio web alojado en el servidor. El servidor determina qué sitio web servir en función de la dirección IP a la que se envió la solicitud. No depende del `Host header`, se puede utilizar con cualquier protocolo, y ofrece un mejor aislamiento entre sitios web. Aún así, requiere múltiples direcciones IP, que pueden ser caras y menos escalables.
3. `Port-Based Virtual Hosting`: Diferentes sitios web están asociados con diferentes puertos en la misma dirección IP. Por ejemplo, un sitio web puede ser accesible en el puerto 80, mientras que otro está en el puerto 8080. `Port-based virtual hosting` se puede utilizar cuando las direcciones IP son limitadas, pero no es tan común o fácil de usar como `name-based virtual hosting` y puede requerir que los usuarios especifiquen el número de puerto en la URL.

## Herramientas de Descubrimiento de Host Virtual

Mientras que el análisis manual de `HTTP headers` y revertir `DNS lookups` puede ser eficaz, especializado `virtual host discovery tools` automatice y agilice el proceso, haciéndolo más eficiente y completo. Estas herramientas emplean varias técnicas para sondear el servidor de destino y descubrir el potencial `virtual hosts`.

Hay varias herramientas disponibles para ayudar en el descubrimiento de hosts virtuales:

|Herramienta|Descripción|Características|
|---|---|---|
|[gobuster](https://github.com/OJ/gobuster)|Una herramienta multipropósito a menudo utilizada para la fuerza bruta de directorio/archivo, pero también efectiva para el descubrimiento de host virtual.|Rápido, admite múltiples métodos HTTP, puede usar listas de palabras personalizadas.|
|[Feroxbuster](https://github.com/epi052/feroxbuster)|Similar a Gobuster, pero con una implementación basada en Rust, conocida por su velocidad y flexibilidad.|Admite recursión, descubrimiento de comodines y varios filtros.|
|[ffuf](https://github.com/ffuf/ffuf)|Otro fuzzer web rápido que se puede utilizar para el descubrimiento de host virtual borrando el `Host` encabezado.|Opciones de entrada y filtrado de listas de palabras personalizables.|

### gobuster

Gobuster es una herramienta versátil comúnmente utilizada para la fuerza bruta de directorios y archivos, pero también sobresale en el descubrimiento de host virtual. Envía sistemáticamente solicitudes HTTP con diferentes `Host` se dirige a una dirección IP de destino y luego analiza las respuestas para identificar hosts virtuales válidos.

Hay un par de cosas que necesitas preparar para la fuerza bruta `Host` encabezados:

1. `Target Identification`: Primero, identifique la dirección IP del servidor web de destino. Esto se puede hacer a través de búsquedas de DNS u otras técnicas de reconocimiento.
2. `Wordlist Preparation`: Prepare una lista de palabras que contenga nombres de host virtuales potenciales. Puede usar una lista de palabras precompilada, como SecLists, o crear una personalizada basada en la industria de su objetivo, convenciones de nombres u otra información relevante.

El `gobuster` el comando a bruteforce vhosts generalmente se ve así:

  Hosts Virtuales

```shell-session
vcrdcr@htb[/htb]$ gobuster vhost -u http://<target_IP_address> -w <wordlist_file> --append-domain
```

- El `-u` flag especifica la URL de destino (reemplazar `<target_IP_address>` con la IP real).
- El `-w` flag especifica el archivo de lista de palabras (reemplazar `<wordlist_file>` con el camino a tu lista de palabras).
- El `--append-domain` flag agrega el dominio base a cada palabra en la lista de palabras.

En las versiones más recientes de Gobuster, se requiere que la bandera de dominio de apéndice agregue el dominio base a cada palabra de la lista de palabras al realizar el descubrimiento de host virtual. Esta bandera garantiza que Gobuster construya correctamente los nombres de host virtuales completos, lo cual es esencial para la enumeración precisa de subdominios potenciales. En versiones anteriores de Gobuster, esta funcionalidad se manejaba de manera diferente, y la bandera de dominio de append no era necesaria. Es posible que los usuarios de versiones anteriores no encuentren este indicador disponible o necesario, ya que la herramienta adjuntó el dominio base de forma predeterminada o empleó un mecanismo diferente para la generación de host virtual.

`Gobuster`generará potenciales hosts virtuales a medida que los descubra. Analice los resultados cuidadosamente, notando cualquier hallazgo inusual o interesante. Es posible que se necesite más investigación para confirmar la existencia y funcionalidad de los hosts virtuales descubiertos.

Hay un par de otros argumentos que vale la pena conocer:

- Considera usar el `-t` bandera para aumentar el número de hilos para un escaneo más rápido.
- El `-k` flag puede ignorar los errores de certificado SSL/TLS.
- Puedes usar el `-o` marque para guardar la salida en un archivo para su posterior análisis.

  Hosts Virtuales

```shell-session
vcrdcr@htb[/htb]$ gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:             http://inlanefreight.htb:81
[+] Method:          GET
[+] Threads:         10
[+] Wordlist:        /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt
[+] User Agent:      gobuster/3.6
[+] Timeout:         10s
[+] Append Domain:   true
===============================================================
Starting gobuster in VHOST enumeration mode
===============================================================
Found: forum.inlanefreight.htb:81 Status: 200 [Size: 100]
[...]
Progress: 114441 / 114442 (100.00%)
===============================================================
Finished
===============================================================
```

El descubrimiento de host virtual puede generar tráfico significativo y puede ser detectado por sistemas de detección de intrusiones (IDS) o firewalls de aplicaciones web (WAF). Tenga cuidado y obtenga la autorización adecuada antes de escanear cualquier objetivo.


# Registros de Transparencia de Certificados

---

En la extensa masa de Internet, la confianza es un producto frágil. Una de las piedras angulares de esta confianza es el `Secure Sockets Layer/Transport Layer Security` (`SSL/TLS`) protocolo, que cifra la comunicación entre su navegador y un sitio web. En el corazón de SSL/TLS se encuentra el `digital certificate`, un pequeño archivo que verifica la identidad de un sitio web y permite una comunicación segura y encriptada.

Sin embargo, el proceso de emisión y gestión de estos certificados no es infalible. Los atacantes pueden explotar certificados deshonestos o mal emitidos para hacerse pasar por sitios web legítimos, interceptar datos confidenciales o propagar malware. Aquí es donde entran en juego los registros de Transparencia de Certificados (CT).

## ¿Qué son los Registros de Transparencia de Certificados?

`Certificate Transparency` (`CT`) los registros son libros de contabilidad públicos, solo anexos, que registran la emisión de certificados SSL/TLS. Cada vez que una Autoridad de Certificación (CA) emite un nuevo certificado, debe enviarlo a varios registros CT. Las organizaciones independientes mantienen estos registros y están abiertas para que cualquiera los inspeccione.

Piense en los registros CT como un `global registry of certificates`. Proporcionan un registro transparente y verificable de cada certificado SSL/TLS emitido para un sitio web. Esta transparencia sirve para varios propósitos cruciales:

- `Early Detection of Rogue Certificates`: Al monitorear los registros de CT, los investigadores de seguridad y los propietarios de sitios web pueden identificar rápidamente certificados sospechosos o mal emitidos. Un certificado deshonesto es un certificado digital no autorizado o fraudulento emitido por una autoridad de certificación de confianza. La detección temprana permite una acción rápida para revocar los certificados antes de que puedan usarse con fines maliciosos.
- `Accountability for Certificate Authorities`: Los registros de CT responsabilizan a las CA por sus prácticas de emisión. Si una CA emite un certificado que viola las reglas o estándares, será visible públicamente en los registros, lo que provocará posibles sanciones o pérdida de confianza.
- `Strengthening the Web PKI (Public Key Infrastructure)`: La Web PKI es el sistema de confianza que sustenta la comunicación segura en línea. Los registros CT ayudan a mejorar la seguridad e integridad de la PKI Web al proporcionar un mecanismo para la supervisión pública y la verificación de certificados.

Haga clic para ampliar un desglose técnico de cómo funcionan los Registros CT

![](https://academy.hackthebox.com/storage/modules/144/diagram-001.png)

  

## CT Logs y Web Recon

Los registros de transparencia de certificados ofrecen una ventaja única en la enumeración de subdominios en comparación con otros métodos. A diferencia de los enfoques de fuerza bruta o basados en listas de palabras, que se basan en adivinar o predecir nombres de subdominios, los registros CT proporcionan un registro definitivo de los certificados emitidos para un dominio y sus subdominios. Esto significa que no está limitado por el alcance de su lista de palabras o la efectividad de su algoritmo de fuerza bruta. En su lugar, obtiene acceso a una vista histórica y completa de los subdominios de un dominio, incluidos aquellos que podrían no usarse activamente o no ser fácilmente adivinables.

Además, los registros CT pueden revelar subdominios asociados con certificados antiguos o vencidos. Estos subdominios pueden alojar software o configuraciones obsoletas, lo que los hace potencialmente vulnerables a la explotación.

En esencia, los registros CT proporcionan una forma confiable y eficiente de descubrir subdominios sin la necesidad de una fuerza bruta exhaustiva o confiar en la integridad de las listas de palabras. Ofrecen una ventana única en la historia de un dominio y pueden revelar subdominios que de otro modo podrían permanecer ocultos, mejorando significativamente sus capacidades de reconocimiento.

## Buscando registros CT

Hay dos opciones populares para buscar registros CT:

|Herramienta|Características Clave|Casos de Uso|Prosperar|Contras|
|---|---|---|---|---|
|[crt.sh](https://crt.sh/)|Interfaz web fácil de usar, búsqueda simple por dominio, muestra detalles del certificado, entradas SAN.|Búsquedas rápidas y fáciles, identificación de subdominios, verificación del historial de emisión de certificados.|Gratis, fácil de usar, no se requiere registro.|Opciones limitadas de filtrado y análisis.|
|[Censys](https://search.censys.io/)|Potente motor de búsqueda para dispositivos conectados a Internet, filtrado avanzado por dominio, IP, atributos de certificado.|Análisis en profundidad de certificados, identificación de configuraciones erróneas, búsqueda de certificados y hosts relacionados.|Amplias opciones de datos y filtrado, acceso a API.|Requiere registro (nivel gratuito disponible).|

### búsqueda de crt.sh

Mientras `crt.sh` ofrece una interfaz web conveniente, también puede aprovechar su API para búsquedas automatizadas directamente desde su terminal. Veamos cómo encontrar todos los subdominios 'dev' en `facebook.com` usando `curl` y `jq`:

  Registros de Transparencia de Certificados

```shell-session
vcrdcr@htb[/htb]$ curl -s "https://crt.sh/?q=facebook.com&output=json" | jq -r '.[]
 | select(.name_value | contains("dev")) | .name_value' | sort -u
 
*.dev.facebook.com
*.newdev.facebook.com
*.secure.dev.facebook.com
dev.facebook.com
devvm1958.ftw3.facebook.com
facebook-amex-dev.facebook.com
facebook-amex-sign-enc-dev.facebook.com
newdev.facebook.com
secure.dev.facebook.com
```

- `curl -s "https://crt.sh/?q=facebook.com&output=json"`: Este comando obtiene la salida JSON de crt.sh para los certificados que coinciden con el dominio `facebook.com`.
- `jq -r '.[] | select(.name_value | contains("dev")) | .name_value'`: Esta parte filtra los resultados de JSON, seleccionando solo las entradas donde el `name_value` el campo (que contiene el dominio o subdominio) incluye la cadena "`dev`". El `-r` la bandera dice `jq` para emitir cadenas sin procesar.
- `sort -u`: Esto ordena los resultados alfabéticamente y elimina los duplicados.


# Huellas dactilares

---

La impresión digital se centra en extraer detalles técnicos sobre las tecnologías que impulsan un sitio web o una aplicación web. De manera similar a cómo una huella digital identifica de manera única a una persona, las firmas digitales de servidores web, sistemas operativos y componentes de software pueden revelar información crítica sobre la infraestructura de un objetivo y las posibles debilidades de seguridad. Este conocimiento permite a los atacantes adaptar los ataques y explotar vulnerabilidades específicas de las tecnologías identificadas.

La huella digital sirve como piedra angular del reconocimiento web por varias razones:

- `Targeted Attacks`: Al conocer las tecnologías específicas en uso, los atacantes pueden centrar sus esfuerzos en exploits y vulnerabilidades que se sabe que afectan a esos sistemas. Esto aumenta significativamente las posibilidades de un compromiso exitoso.
- `Identifying Misconfigurations`: La impresión digital puede exponer software mal configurado u obsoleto, configuraciones predeterminadas u otras debilidades que podrían no ser evidentes a través de otros métodos de reconocimiento.
- `Prioritising Targets`: Cuando se enfrentan a múltiples objetivos potenciales, las huellas digitales ayudan a priorizar los esfuerzos al identificar sistemas más propensos a ser vulnerables o mantener información valiosa.
- `Building a Comprehensive Profile`: La combinación de datos de huellas dactilares con otros hallazgos de reconocimiento crea una visión holística de la infraestructura del objetivo, ayudando a comprender su postura de seguridad general y posibles vectores de ataque.

## Técnicas de Huellas Digitales

Hay varias técnicas utilizadas para el servidor web y la tecnología de huellas digitales:

- `Banner Grabbing`: El acaparamiento de banners implica analizar los banners presentados por los servidores web y otros servicios. Estos banners a menudo revelan el software del servidor, los números de versión y otros detalles.
- `Analysing HTTP Headers`: Los encabezados HTTP transmitidos con cada solicitud y respuesta de página web contienen una gran cantidad de información. El `Server` el encabezado normalmente divulga el software del servidor web, mientras que el `X-Powered-By` el encabezado puede revelar tecnologías adicionales como lenguajes de scripting o marcos.
- `Probing for Specific Responses`: El envío de solicitudes especialmente diseñadas al objetivo puede generar respuestas únicas que revelan tecnologías o versiones específicas. Por ejemplo, ciertos mensajes de error o comportamientos son característicos de servidores web o componentes de software particulares.
- `Analysing Page Content`: El contenido de una página web, incluida su estructura, scripts y otros elementos, a menudo puede proporcionar pistas sobre las tecnologías subyacentes. Puede haber una cabecera de copyright que indique el uso de software específico, por ejemplo.

Existe una variedad de herramientas que automatizan el proceso de toma de huellas digitales, combinando varias técnicas para identificar servidores web, sistemas operativos, sistemas de administración de contenido y otras tecnologías:

|Herramienta|Descripción|Características|
|---|---|---|
|`Wappalyzer`|Extensión del navegador y servicio en línea para perfiles de tecnología de sitios web.|Identifica una amplia gama de tecnologías web, incluidos CMS, marcos, herramientas de análisis y más.|
|`BuiltWith`|Perfilador de tecnología web que proporciona informes detallados en la pila de tecnología de un sitio web.|Ofrece planes gratuitos y de pago con diferentes niveles de detalle.|
|`WhatWeb`|Herramienta de línea de comandos para la toma de huellas digitales del sitio web.|Utiliza una vasta base de datos de firmas para identificar diversas tecnologías web.|
|`Nmap`|Escáner de red versátil que se puede utilizar para diversas tareas de reconocimiento, incluyendo servicio y huellas dactilares OS.|Se puede utilizar con scripts (NSE) para realizar huellas digitales más especializadas.|
|`Netcraft`|Ofrece una gama de servicios de seguridad web, que incluyen huellas dactilares de sitios web e informes de seguridad.|Proporciona informes detallados sobre la tecnología de un sitio web, el proveedor de alojamiento y la postura de seguridad.|
|`wafw00f`|Herramienta de línea de comandos diseñada específicamente para identificar Firewalls de Aplicaciones Web (WAF).|Ayuda a determinar si un WAF está presente y, de ser así, su tipo y configuración.|

## Impresión digital inlanefreight.com

Apliquemos nuestro conocimiento de huellas digitales para descubrir el ADN digital de nuestro host especialmente diseñado `inlanefreight.com`. Aprovecharemos las técnicas manuales y automatizadas para recopilar información sobre su servidor web, tecnologías y vulnerabilidades potenciales.

### Banner Agarrando

Nuestro primer paso es recopilar información directamente desde el propio servidor web. Podemos hacer esto usando el `curl` comando con el `-I` bandera (o `--head`) para obtener solo los encabezados HTTP, no todo el contenido de la página.

  Huellas dactilares

```shell-session
vcrdcr@htb[/htb]$ curl -I inlanefreight.com
```

La salida incluirá el banner del servidor, revelando el software del servidor web y el número de versión:

  Huellas dactilares

```shell-session
vcrdcr@htb[/htb]$ curl -I inlanefreight.com

HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:07:44 GMT
Server: Apache/2.4.41 (Ubuntu)
Location: https://inlanefreight.com/
Content-Type: text/html; charset=iso-8859-1
```

En este caso, vemos eso `inlanefreight.com` está corriendo `Apache/2.4.41`, específicamente el `Ubuntu` versión. Esta información es nuestra primera pista, insinuando la pila de tecnología subyacente. También está tratando de redirigir a `https://inlanefreight.com/` así que toma esas pancartas también

  Huellas dactilares

```shell-session
vcrdcr@htb[/htb]$ curl -I https://inlanefreight.com

HTTP/1.1 301 Moved Permanently
Date: Fri, 31 May 2024 12:12:12 GMT
Server: Apache/2.4.41 (Ubuntu)
X-Redirect-By: WordPress
Location: https://www.inlanefreight.com/
Content-Type: text/html; charset=UTF-8
```

Ahora tenemos un encabezado realmente interesante, el servidor está tratando de redirigirnos de nuevo, pero esta vez vemos que es `WordPress` eso es hacer la redirección a `https://www.inlanefreight.com/`

  Huellas dactilares

```shell-session
vcrdcr@htb[/htb]$ curl -I https://www.inlanefreight.com

HTTP/1.1 200 OK
Date: Fri, 31 May 2024 12:12:26 GMT
Server: Apache/2.4.41 (Ubuntu)
Link: <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/"
Link: <https://www.inlanefreight.com/index.php/wp-json/wp/v2/pages/7>; rel="alternate"; type="application/json"
Link: <https://www.inlanefreight.com/>; rel=shortlink
Content-Type: text/html; charset=UTF-8
```

Algunos encabezados más interesantes, incluyendo un camino interesante que contiene `wp-json`. El `wp-` prefijo es común a WordPress.

### Wafw00f

`Web Application Firewalls` (`WAFs`) son soluciones de seguridad diseñadas para proteger las aplicaciones web de varios ataques. Antes de continuar con más huellas dactilares, es crucial determinar si `inlanefreight.com` emplea un WAF, ya que podría interferir con nuestras sondas o potencialmente bloquear nuestras solicitudes.

Para detectar la presencia de un WAF, usaremos el `wafw00f` herramienta. Para instalar `wafw00f`, puedes usar pip3:

  Huellas dactilares

```shell-session
vcrdcr@htb[/htb]$ pip3 install git+https://github.com/EnableSecurity/wafw00f
```

Una vez instalado, pase el dominio que desea verificar como argumento a la herramienta:

  Huellas dactilares

```shell-session
vcrdcr@htb[/htb]$ wafw00f inlanefreight.com

                ______
               /      \
              (  W00f! )
               \  ____/
               ,,    __            404 Hack Not Found
           |`-.__   / /                      __     __
           /"  _/  /_/                       \ \   / /
          *===*    /                          \ \_/ /  405 Not Allowed
         /     )__//                           \   /
    /|  /     /---`                        403 Forbidden
    \\/`   \ |                                 / _ \
    `\    /_\\_              502 Bad Gateway  / / \ \  500 Internal Error
      `_____``-`                             /_/   \_\

                        ~ WAFW00F : v2.2.0 ~
        The Web Application Firewall Fingerprinting Toolkit
    
[*] Checking https://inlanefreight.com
[+] The site https://inlanefreight.com is behind Wordfence (Defiant) WAF.
[~] Number of requests: 2
```

El `wafw00f` escanear en `inlanefreight.com` revela que el sitio web está protegido por el `Wordfence Web Application Firewall` (`WAF`), desarrollado por Defiant.

Esto significa que el sitio tiene una capa de seguridad adicional que podría bloquear o filtrar nuestros intentos de reconocimiento. En un escenario del mundo real, sería crucial tener esto en cuenta a medida que avanza con una mayor investigación, ya que es posible que deba adaptar las técnicas para evitar o evadir los mecanismos de detección de la WAF.

### Nikto

`Nikto` es un potente escáner de servidor web de código abierto. Además de su función principal como herramienta de evaluación de vulnerabilidades, `Nikto's` las capacidades de huellas digitales proporcionan información sobre la pila de tecnología de un sitio web.

`Nikto`está preinstalado en pwnbox, pero si necesita instalarlo, puede ejecutar los siguientes comandos:

  Huellas dactilares

```shell-session
vcrdcr@htb[/htb]$ sudo apt update && sudo apt install -y perl
vcrdcr@htb[/htb]$ git clone https://github.com/sullo/nikto
vcrdcr@htb[/htb]$ cd nikto/program
vcrdcr@htb[/htb]$ chmod +x ./nikto.pl
```

Para escanear `inlanefreight.com` usando `Nikto`, solo ejecutando los módulos de huellas dactilares, ejecute el siguiente comando:

  Huellas dactilares

```shell-session
vcrdcr@htb[/htb]$ nikto -h inlanefreight.com -Tuning b
```

El `-h` flag especifica el host de destino. El `-Tuning b` la bandera dice `Nikto` para ejecutar solo los módulos de Identificación de Software.

`Nikto`a continuación, iniciará una serie de pruebas, intentando identificar software obsoleto, archivos o configuraciones inseguras y otros riesgos potenciales de seguridad.

  Huellas dactilares

```shell-session
vcrdcr@htb[/htb]$ nikto -h inlanefreight.com -Tuning b

- Nikto v2.5.0
---------------------------------------------------------------------------
+ Multiple IPs found: 134.209.24.248, 2a03:b0c0:1:e0::32c:b001
+ Target IP:          134.209.24.248
+ Target Hostname:    www.inlanefreight.com
+ Target Port:        443
---------------------------------------------------------------------------
+ SSL Info:        Subject:  /CN=inlanefreight.com
                   Altnames: inlanefreight.com, www.inlanefreight.com
                   Ciphers:  TLS_AES_256_GCM_SHA384
                   Issuer:   /C=US/O=Let's Encrypt/CN=R3
+ Start Time:         2024-05-31 13:35:54 (GMT0)
---------------------------------------------------------------------------
+ Server: Apache/2.4.41 (Ubuntu)
+ /: Link header found with value: ARRAY(0x558e78790248). See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link
+ /: The site uses TLS and the Strict-Transport-Security HTTP header is not defined. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security
+ /: The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type. See: https://www.netsparker.com/web-vulnerability-scanner/vulnerabilities/missing-content-type-header/
+ /index.php?: Uncommon header 'x-redirect-by' found, with contents: WordPress.
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ /: The Content-Encoding header is set to "deflate" which may mean that the server is vulnerable to the BREACH attack. See: http://breachattack.com/
+ Apache/2.4.41 appears to be outdated (current is at least 2.4.59). Apache 2.2.34 is the EOL for the 2.x branch.
+ /: Web Server returns a valid response with junk HTTP methods which may cause false positives.
+ /license.txt: License file found may identify site software.
+ /: A Wordpress installation was found.
+ /wp-login.php?action=register: Cookie wordpress_test_cookie created without the httponly flag. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
+ /wp-login.php:X-Frame-Options header is deprecated and has been replaced with the Content-Security-Policy HTTP header with the frame-ancestors directive instead. See: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options
+ /wp-login.php: Wordpress login found.
+ 1316 requests: 0 error(s) and 12 item(s) reported on remote host
+ End Time:           2024-05-31 13:47:27 (GMT0) (693 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

El escaneo de reconocimiento en `inlanefreight.com` revela varios hallazgos clave:

- `IPs`: El sitio web se resuelve en ambos IPv4 (`134.209.24.248`) e IPv6 (`2a03:b0c0:1:e0::32c:b001`) direcciones.
- `Server Technology`: El sitio web se ejecuta en `Apache/2.4.41 (Ubuntu)`
- `WordPress Presence`: El escaneo identificó una instalación de WordPress, incluida la página de inicio de sesión (`/wp-login.php`). Esto sugiere que el sitio podría ser un objetivo potencial para exploits comunes relacionados con WordPress.
- `Information Disclosure`: La presencia de un `license.txt` el archivo podría revelar detalles adicionales sobre los componentes de software del sitio web.
- `Headers`: Se encontraron varios encabezados no estándar o inseguros, incluyendo un desaparecido `Strict-Transport-Security` encabezado y un potencialmente inseguro `x-redirect-by` encabezado.


# Arrastrarse

`Crawling`, a menudo llamado `spidering`, es el `automated process of systematically browsing the World Wide Web`. Similar a cómo una araña navega por su web, un rastreador web sigue enlaces de una página a otra, recopilando información. Estos rastreadores son esencialmente bots que utilizan algoritmos predefinidos para descubrir e indexar páginas web, haciéndolas accesibles a través de motores de búsqueda o para otros fines como el análisis de datos y el reconocimiento web.

## Cómo Funcionan los Rastreadores Web

El funcionamiento básico de un rastreador web es sencillo pero poderoso. Comienza con una URL semilla, que es la página web inicial para rastrear. El rastreador obtiene esta página, analiza su contenido y extrae todos sus enlaces. Luego agrega estos enlaces a una cola y los rastrea, repitiendo el proceso iterativamente. Dependiendo de su alcance y configuración, el rastreador puede explorar un sitio web completo o incluso una gran parte de la web.

1. `Homepage`: Comienzas con la página de inicio que contiene `link1`, `link2`, y `link3`.
    
    Código: txt
    
    ```txt
    Homepage
    ├── link1
    ├── link2
    └── link3
    ```
    
2. `Visiting link1`: Visitando `link1` muestra la página de inicio, `link2`y también `link4` y `link5`.
    
    Código: txt
    
    ```txt
    link1 Page
    ├── Homepage
    ├── link2
    ├── link4
    └── link5
    ```
    
3. `Continuing the Crawl`: El rastreador continúa siguiendo estos enlaces sistemáticamente, reuniendo todas las páginas accesibles y sus enlaces.
    

Este ejemplo ilustra cómo un rastreador web descubre y recopila información siguiendo sistemáticamente los enlaces, distinguiéndolo del fuzzing que implica adivinar enlaces potenciales.

Hay dos tipos principales de estrategias de rastreo.

### Breadth-Primer Arrastramiento

![Diagrama de flujo que muestra una URL de Semilla que conduce a la Página 1, que se ramifica a la Página 2 y la Página 3. La Página 2 se conecta a la Página 4 y a la Página 5, mientras que la Página 3 se conecta a la Página 6 y a la Página 7.](https://mermaid.ink/svg/pako:eNo90D0PgjAQBuC_0twsg98Jgwkf6oKJgThZhkpPIEohpR0M4b970shNd09uuHsHKFqJ4EOpRVexJOWqtw83ZIiS3dKEK0YV3K-iRLbMuUIluQqY5x1Y6HSV_yFysCYIJ4gdbGY4OtgSRBOcHOxmODvYE8ACGtSNqCXdOPwu4WAqbJCDT60U-sWBq5H2hDVt9lEF-EZbXIBubVmB_xTvnibbSWEwrgX91syKsjatvrgIpiTGL-8RVcQ)

`Breadth-first crawling`prioriza explorar el ancho de un sitio web antes de profundizar. Comienza rastreando todos los enlaces en la página de semillas, luego pasa a los enlaces en esas páginas, y así sucesivamente. Esto es útil para obtener una visión general de la estructura y el contenido de un sitio web.

### Profundidad-Primera Arrastramiento

![Diagrama de flujo que muestra una URL de Semilla que conduce a la Página 1, luego a la Página 2. La Página 2 se conecta a la Página 3, que se ramifica a la Página 4 y a la Página 5.](https://mermaid.ink/svg/pako:eNo9zz0PgjAQBuC_0twsg18LgwlfGyYG4uQ5VHoC0RZS2sEQ_rsnTezU98mlvXeGZlAEMbRWjp0oKzSTf4RQEylxrUo0gk9yu8iWxPaOhoxCk4goOok06I41XSELsGfIVsgDHBjyFYoAR4YivCEEGtiAJqtlr3iZ-fclgutIE0LMVyXtCwHNwnPSu6H-mAZiZz1twA6-7SB-yvfEyY9KOsp7ySX0X0n1brDn0HWtvHwB2SFOww)

En contraste, `depth-first crawling` prioriza la profundidad sobre la amplitud. Sigue una única ruta de enlaces en la medida de lo posible antes de retroceder y explorar otras rutas. Esto puede ser útil para encontrar contenido específico o profundizar en la estructura de un sitio web.

La elección de la estrategia depende de los objetivos específicos del proceso de rastreo.

## Extracción de Información Valiosa

Los rastreadores pueden extraer una amplia gama de datos, cada uno con un propósito específico en el proceso de reconocimiento:

- `Links (Internal and External)`: Estos son los bloques de construcción fundamentales de la web, conectando páginas dentro de un sitio web (`internal links`) y a otros sitios web (`external links`). Los rastreadores recopilan meticulosamente estos enlaces, lo que le permite trazar la estructura de un sitio web, descubrir páginas ocultas e identificar relaciones con recursos externos.
- `Comments`: Las secciones de comentarios en blogs, foros u otras páginas interactivas pueden ser una mina de oro de información. Los usuarios a menudo revelan inadvertidamente detalles confidenciales, procesos internos o indicios de vulnerabilidades en sus comentarios.
- `Metadata`: Metadatos se refiere a `data about data`. En el contexto de las páginas web, incluye información como títulos de páginas, descripciones, palabras clave, nombres de autores y fechas. Estos metadatos pueden proporcionar un contexto valioso sobre el contenido, el propósito y la relevancia de una página para sus objetivos de reconocimiento.
- `Sensitive Files`: Los rastreadores web se pueden configurar para buscar activamente archivos confidenciales que podrían estar expuestos inadvertidamente en un sitio web. Esto incluye `backup files` (por ejemplo `.bak`, `.old`), `configuration files` (por ejemplo `web.config`, `settings.php`), `log files` (por ejemplo `error_log`, `access_log`), y otros archivos que contienen contraseñas, `API keys`u otra información confidencial. Examinar cuidadosamente los archivos extraídos, especialmente los archivos de copia de seguridad y configuración, puede revelar un tesoro de información confidencial, como `database credentials`, `encryption keys`o incluso fragmentos de código fuente.

### La Importancia del Contexto

Comprender el contexto que rodea los datos extraídos es primordial.

Una sola pieza de información, como un comentario que menciona una versión de software específica, puede no parecer significativa por sí sola. Sin embargo, cuando se combina con otros hallazgos, como una versión desactualizada que aparece en los metadatos o un archivo de configuración potencialmente vulnerable descubierto a través de crawling—, puede transformarse en un indicador crítico de una vulnerabilidad potencial.

El verdadero valor de los datos extraídos radica en conectar los puntos y construir una imagen completa del panorama digital del objetivo.

Por ejemplo, una lista de enlaces extraídos puede parecer inicialmente mundana. Pero después de un examen más detallado, observa un patrón: varias URL apuntan a un directorio llamado `/files/`. Esto desencadena su curiosidad y decide visitar manualmente el directorio. Para su sorpresa, usted encuentra que la navegación de directorios está habilitada, exponiendo una gran cantidad de archivos, incluyendo archivos de respaldo, documentos internos y datos potencialmente sensibles. Este descubrimiento no habría sido posible simplemente mirando los enlaces individuales de forma aislada; el análisis contextual lo llevó a este hallazgo crítico.

Del mismo modo, los comentarios aparentemente inocuos pueden ganar importancia cuando se correlacionan con otros descubrimientos. Un comentario que mencione un "servidor de archivos" podría no levantar ninguna bandera roja inicialmente. Sin embargo, cuando se combina con el descubrimiento de la `/files/` directorio, refuerza la posibilidad de que el servidor de archivos sea de acceso público, exponiendo potencialmente información confidencial o datos confidenciales.

Por lo tanto, es esencial abordar el análisis de datos de manera integral, teniendo en cuenta las relaciones entre los diferentes puntos de datos y sus posibles implicaciones para sus objetivos de reconocimiento.



# robots.txt

---

Imagina que eres un invitado en una gran fiesta en la casa. Si bien es libre de mezclarse y explorar, puede haber ciertas habitaciones marcadas como "Privadas" que se espera que evite. Esto es similar a cómo `robots.txt` funciones en el mundo del rastreo web. Actúa como un virtual "`etiquette guide`"para bots, describiendo a qué áreas de un sitio web se les permite acceder y cuáles están fuera de los límites.

## ¿Qué es robots.txt?

Técnicamente, `robots.txt` es un archivo de texto simple colocado en el directorio raíz de un sitio web (por ejemplo, `www.example.com/robots.txt`). Se adhiere al Estándar de Exclusión de Robots, pautas sobre cómo deben comportarse los rastreadores web cuando visitan un sitio web. Este archivo contiene instrucciones en forma de "directivas" que le dicen a los bots qué partes del sitio web pueden y no pueden rastrear.

### Cómo funciona robots.txt

Las directivas en robots.txt generalmente se dirigen a agentes de usuario específicos, que son identificadores para diferentes tipos de bots. Por ejemplo, una directiva puede verse así:

Código: txt

```txt
User-agent: *
Disallow: /private/
```

Esta directiva dice a todos los agentes de usuario (`*` es un comodín) con el que no se les permite acceder a ninguna URL que comience `/private/`. Otras directivas pueden permitir el acceso a directorios o archivos específicos, establecer retrasos de rastreo para evitar sobrecargar un servidor o proporcionar enlaces a mapas de sitio para un rastreo eficiente.

### Comprender la estructura de robots.txt

El archivo robots.txt es un documento de texto sin formato que vive en el directorio raíz de un sitio web. Sigue una estructura directa, con cada conjunto de instrucciones, o "registro", separado por una línea en blanco. Cada registro consta de dos componentes principales:

1. `User-agent`: Esta línea especifica a qué rastreador o bot se aplican las siguientes reglas. Un comodín (`*`) indica que las reglas se aplican a todos los bots. También se pueden dirigir agentes de usuario específicos, como "Googlebot" (Crawler de Google) o "Bingbot" (Crawler de Microsoft).
2. `Directives`: Estas líneas proporcionan instrucciones específicas al agente de usuario identificado.

Las directivas comunes incluyen:

|Directiva|Descripción|Ejemplo|
|---|---|---|
|`Disallow`|Especifica rutas o patrones que el bot no debe rastrear.|`Disallow: /admin/`(no permitir el acceso al directorio de administración)|
|`Allow`|Permite explícitamente que el bot rastree caminos o patrones específicos, incluso si caen bajo un nivel más amplio `Disallow` regla.|`Allow: /public/`(permitir el acceso al directorio público)|
|`Crawl-delay`|Establece un retraso (en segundos) entre las solicitudes sucesivas del bot para evitar sobrecargar el servidor.|`Crawl-delay: 10`(retraso de 10 segundos entre solicitudes)|
|`Sitemap`|Proporciona la URL a un mapa del sitio XML para un rastreo más eficiente.|`Sitemap: https://www.example.com/sitemap.xml`|

### ¿Por qué respetar robots.txt?

Si bien robots.txt no es estrictamente ejecutable (un bot deshonesto aún podría ignorarlo), la mayoría de los rastreadores web legítimos y los bots de motores de búsqueda respetarán sus directivas. Esto es importante por varias razones:

- `Avoiding Overburdening Servers`: Al limitar el acceso del rastreador a ciertas áreas, los propietarios de sitios web pueden evitar el tráfico excesivo que podría ralentizar o incluso bloquear sus servidores.
- `Protecting Sensitive Information`: Robots.txt puede proteger la información privada o confidencial de ser indexada por los motores de búsqueda.
- `Legal and Ethical Compliance`: En algunos casos, ignorar las directivas robots.txt podría considerarse una violación de los términos de servicio de un sitio web o incluso un problema legal, especialmente si implica acceder a datos privados o con derechos de autor.

## robots.txt en Reconocimiento Web

Para el reconocimiento web, robots.txt sirve como una valiosa fuente de inteligencia. Al respetar las directivas descritas en este archivo, los profesionales de seguridad pueden obtener información crucial sobre la estructura y las posibles vulnerabilidades de un sitio web de destino:

- `Uncovering Hidden Directories`: Las rutas no permitidas en robots.txt a menudo apuntan a directorios o archivos que el propietario del sitio web intencionalmente quiere mantener fuera del alcance de los rastreadores de los motores de búsqueda. Estas áreas ocultas pueden albergar información confidencial, archivos de respaldo, paneles administrativos u otros recursos que podrían interesar a un atacante.
- `Mapping Website Structure`: Al analizar las rutas permitidas y no permitidas, los profesionales de seguridad pueden crear un mapa rudimentario de la estructura del sitio web. Esto puede revelar secciones que no están vinculadas desde la navegación principal, lo que puede conducir a páginas o funcionalidades no descubiertas.
- `Detecting Crawler Traps`: Algunos sitios web incluyen intencionalmente directorios "honeypot" en robots.txt para atraer bots maliciosos. La identificación de tales trampas puede proporcionar información sobre la conciencia de seguridad del objetivo y las medidas defensivas.

### Analizando robots.txt

Aquí hay un ejemplo de un archivo robots.txt:

Código: txt

```txt
User-agent: *
Disallow: /admin/
Disallow: /private/
Allow: /public/

User-agent: Googlebot
Crawl-delay: 10

Sitemap: https://www.example.com/sitemap.xml
```

Este archivo contiene las siguientes directivas:

- No se permite que todos los agentes de usuario accedan al `/admin/` y `/private/` directorios.
- Todos los agentes de usuario pueden acceder al `/public/` directorio.
- El `Googlebot` (El rastreador web de Google) tiene instrucciones específicas de esperar 10 segundos entre las solicitudes.
- El mapa del sitio, ubicado en `https://www.example.com/sitemap.xml`, se proporciona para facilitar el rastreo y la indexación.

Al analizar este robots.txt, podemos inferir que el sitio web probablemente tenga un panel de administración ubicado en `/admin/` y algo de contenido privado en el `/private/` directorio.


# URIs Bien Conocidos

---

El `.well-known` estándar, definido en [RFC 8615](https://datatracker.ietf.org/doc/html/rfc8615), sirve como un directorio estandarizado dentro del dominio raíz de un sitio web. Esta ubicación designada, típicamente accesible a través del `/.well-known/` ruta en un servidor web, centraliza los metadatos críticos de un sitio web, incluidos los archivos de configuración y la información relacionada con sus servicios, protocolos y mecanismos de seguridad.

Al establecer una ubicación consistente para dichos datos, `.well-known` simplifica el proceso de descubrimiento y acceso para varias partes interesadas, incluidos navegadores web, aplicaciones y herramientas de seguridad. Este enfoque simplificado permite a los clientes localizar y recuperar automáticamente archivos de configuración específicos mediante la construcción de la URL adecuada. Por ejemplo, para acceder a la política de seguridad de un sitio web, un cliente solicitaría `https://example.com/.well-known/security.txt`.

El `Internet Assigned Numbers Authority` (`IANA`) mantiene a [registro](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml) de `.well-known` URI, cada uno con un propósito específico definido por varias especificaciones y estándares. A continuación se muestra una tabla que destaca algunos ejemplos notables:

|Sufijo URI|Descripción|Estado|Referencia|
|---|---|---|---|
|`security.txt`|Contiene información de contacto para que los investigadores de seguridad informen sobre vulnerabilidades.|Permanente|RFC 9116|
|`/.well-known/change-password`|Proporciona una URL estándar para dirigir a los usuarios a una página de cambio de contraseña.|Provisional|https://w3c.github.io/webappsec-change-password-url/#the-change-password-well-known-uri|
|`openid-configuration`|Define los detalles de configuración de OpenID Connect, una capa de identidad en la parte superior del protocolo OAuth 2.0.|Permanente|http://openid.net/specs/openid-connect-discovery-1_0.html|
|`assetlinks.json`|Se utiliza para verificar la propiedad de los activos digitales (por ejemplo, aplicaciones) asociados con un dominio.|Permanente|https://github.com/google/digitalassetlinks/blob/master/well-known/specification.md|
|`mta-sts.txt`|Especifica la política de SMTP MTA Strict Transport Security (MTA-STS) para mejorar la seguridad del correo electrónico.|Permanente|RFC 8461|

Esto es solo una pequeña muestra de los muchos `.well-known` URI registrados en IANA. Cada entrada en el registro ofrece pautas y requisitos específicos para la implementación, asegurando un enfoque estandarizado para aprovechar el `.well-known` mecanismo para diversas aplicaciones.

## Web Recon y .bien conocido

En web recon, el `.well-known` Los URI pueden ser invaluables para descubrir puntos finales y detalles de configuración que se pueden probar más a fondo durante una prueba de penetración. Un URI particularmente útil es `openid-configuration`.

El `openid-configuration` URI es parte del protocolo OpenID Connect Discovery, una capa de identidad construida sobre el protocolo OAuth 2.0. Cuando una aplicación cliente desea utilizar OpenID Connect para la autenticación, puede recuperar la configuración del proveedor de OpenID Connect accediendo al `https://example.com/.well-known/openid-configuration` punto final. Este punto final devuelve un documento JSON que contiene metadatos sobre los puntos finales del proveedor, métodos de autenticación compatibles, emisión de tokens y más:

Código: json

```json
{
  "issuer": "https://example.com",
  "authorization_endpoint": "https://example.com/oauth2/authorize",
  "token_endpoint": "https://example.com/oauth2/token",
  "userinfo_endpoint": "https://example.com/oauth2/userinfo",
  "jwks_uri": "https://example.com/oauth2/jwks",
  "response_types_supported": ["code", "token", "id_token"],
  "subject_types_supported": ["public"],
  "id_token_signing_alg_values_supported": ["RS256"],
  "scopes_supported": ["openid", "profile", "email"]
}
```

La información obtenida de la `openid-configuration` endpoint ofrece múltiples oportunidades de exploración:

1. `Endpoint Discovery`:
    - `Authorization Endpoint`: Identificar la URL para las solicitudes de autorización de usuario.
    - `Token Endpoint`: Encontrar la URL donde se emiten los tokens.
    - `Userinfo Endpoint`: Localización del punto final que proporciona información del usuario.
2. `JWKS URI`: El `jwks_uri` revela el `JSON Web Key Set` (`JWKS`), detallando las claves criptográficas utilizadas por el servidor.
3. `Supported Scopes and Response Types`: Comprender qué alcances y tipos de respuesta son compatibles ayuda a trazar la funcionalidad y las limitaciones de la implementación de OpenID Connect.
4. `Algorithm Details`: La información sobre los algoritmos de firma compatibles puede ser crucial para comprender las medidas de seguridad vigentes.

Explorando el [Registro IANA](https://www.iana.org/assignments/well-known-uris/well-known-uris.xhtml) y experimentando con los diversos `.well-known` URIs es un enfoque invaluable para descubrir oportunidades adicionales de reconocimiento web. Como se demostró con el `openid-configuration` en el punto final anterior, estos URI estandarizados proporcionan acceso estructurado a metadatos críticos y detalles de configuración, lo que permite a los profesionales de la seguridad trazar de manera integral el panorama de seguridad de un sitio web.


# Grietas Espeluznantes

---

El rastreo web es vasto e intrincado, pero no tiene que embarcarse solo en este viaje. Una gran cantidad de herramientas de rastreo web están disponibles para ayudarlo, cada una con sus propias fortalezas y especialidades. Estas herramientas automatizan el proceso de rastreo, haciéndolo más rápido y más eficiente, lo que le permite concentrarse en analizar los datos extraídos.

## Rastreadores Web Populares

1. `Burp Suite Spider`: Burp Suite, una plataforma de prueba de aplicaciones web ampliamente utilizada, incluye un potente rastreador activo llamado Spider. Spider sobresale en el mapeo de aplicaciones web, la identificación de contenido oculto y el descubrimiento de posibles vulnerabilidades.
2. `OWASP ZAP (Zed Attack Proxy)`: ZAP es un escáner de seguridad de aplicaciones web gratuito y de código abierto. Se puede utilizar en modos automatizados y manuales e incluye un componente de araña para rastrear aplicaciones web e identificar posibles vulnerabilidades.
3. `Scrapy (Python Framework)`: Scrapy es un framework Python versátil y escalable para crear rastreadores web personalizados. Proporciona características enriquecidas para extraer datos estructurados de sitios web, manejar escenarios de rastreo complejos y automatizar el procesamiento de datos. Su flexibilidad lo hace ideal para tareas de reconocimiento personalizadas.
4. `Apache Nutch (Scalable Crawler)`: Nutch es un rastreador web de código abierto altamente extensible y escalable escrito en Java. Está diseñado para manejar rastreos masivos en toda la web o centrarse en dominios específicos. Si bien requiere más experiencia técnica para configurar y configurar, su potencia y flexibilidad lo convierten en un activo valioso para proyectos de reconocimiento a gran escala.

Adherirse a prácticas de rastreo éticas y responsables es crucial sin importar qué herramienta elija. Siempre obtenga permiso antes de rastrear un sitio web, especialmente si planea realizar escaneos extensos o intrusivos. Tenga en cuenta los recursos del servidor del sitio web y evite sobrecargarlos con solicitudes excesivas.

## Desguace

Aprovecharemos Scrapy y una araña personalizada adaptada para el reconocimiento `inlanefreight.com`. Si está interesado en obtener más información sobre las técnicas de rastreo/spidering, consulte el "[Usando Proxies Web](https://academy.hackthebox.com/module/details/110)"módulo, ya que también forma parte de CBBH.

### Instalación de Scrapy

Antes de comenzar, asegúrese de tener Scrapy instalado en su sistema. Si no lo hace, puede instalarlo fácilmente usando pip, el instalador del paquete Python:

  Grietas Espeluznantes

```shell-session
vcrdcr@htb[/htb]$ pip3 install scrapy
```

Este comando descargará e instalará Scrapy junto con sus dependencias, preparando su entorno para construir nuestra araña.

### ReconSpider

Primero, ejecute este comando en su terminal para descargar la araña de desguace personalizada `ReconSpider`y extraerlo al directorio de trabajo actual.

  Grietas Espeluznantes

```shell-session
vcrdcr@htb[/htb]$ wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip
vcrdcr@htb[/htb]$ unzip ReconSpider.zip 
```

Con los archivos extraídos, puede ejecutar `ReconSpider.py` usando el siguiente comando:

  Grietas Espeluznantes

```shell-session
vcrdcr@htb[/htb]$ python3 ReconSpider.py http://inlanefreight.com
```

Reemplazar `inlanefreight.com` con el dominio que quieres arañar. La araña rastreará el objetivo y recopilará información valiosa.

### resultados.json

Después de correr `ReconSpider.py`, los datos se guardarán en un archivo JSON, `results.json`. Este archivo se puede explorar utilizando cualquier editor de texto. A continuación se muestra la estructura del archivo JSON producido:

Código: json

```json
{
    "emails": [
        "lily.floid@inlanefreight.com",
        "cvs@inlanefreight.com",
        ...
    ],
    "links": [
        "https://www.themeansar.com",
        "https://www.inlanefreight.com/index.php/offices/",
        ...
    ],
    "external_files": [
        "https://www.inlanefreight.com/wp-content/uploads/2020/09/goals.pdf",
        ...
    ],
    "js_files": [
        "https://www.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=3.3.2",
        ...
    ],
    "form_fields": [],
    "images": [
        "https://www.inlanefreight.com/wp-content/uploads/2021/03/AboutUs_01-1024x810.png",
        ...
    ],
    "videos": [],
    "audio": [],
    "comments": [
        "<!-- #masthead -->",
        ...
    ]
}
```

Cada clave en el archivo JSON representa un tipo diferente de datos extraídos del sitio web de destino:

|Clave JSON|Descripción|
|---|---|
|`emails`|Enumera las direcciones de correo electrónico que se encuentran en el dominio.|
|`links`|Enumera las URL de los enlaces que se encuentran dentro del dominio.|
|`external_files`|Enumera URL de archivos externos como archivos PDF.|
|`js_files`|Enumera las URL de los archivos JavaScript utilizados por el sitio web.|
|`form_fields`|Enumera los campos de formulario que se encuentran en el dominio (vacío en este ejemplo).|
|`images`|Enumera las URL de las imágenes encontradas en el dominio.|
|`videos`|Enumera las URL de los videos encontrados en el dominio (vacío en este ejemplo).|
|`audio`|Enumera las URL de los archivos de audio que se encuentran en el dominio (vacío en este ejemplo).|
|`comments`|Enumera los comentarios HTML que se encuentran en el código fuente.|

Al explorar esta estructura JSON, puede obtener información valiosa sobre la arquitectura, el contenido y los posibles puntos de interés de la aplicación web para una mayor investigación.


# Descubrimiento del Motor de Búsqueda

---

Los motores de búsqueda sirven como nuestros guías en el vasto paisaje de Internet, ayudándonos a navegar a través de la extensión aparentemente interminable de información. Sin embargo, más allá de su función principal de responder consultas diarias, los motores de búsqueda también tienen un tesoro de datos que pueden ser invaluables para el reconocimiento web y la recopilación de información. Esta práctica, conocida como descubrimiento de motores de búsqueda o recopilación de OSINT (Open Source Intelligence), implica el uso de motores de búsqueda como herramientas poderosas para descubrir información sobre sitios web, organizaciones e individuos objetivo.

En esencia, el descubrimiento de motores de búsqueda aprovecha el inmenso poder de los algoritmos de búsqueda para extraer datos que pueden no ser fácilmente visibles en los sitios web. Los profesionales e investigadores de seguridad pueden profundizar en la web indexada mediante el empleo de operadores de búsqueda especializados, técnicas y herramientas, descubriendo todo, desde información de empleados y documentos confidenciales hasta páginas de inicio de sesión ocultas y credenciales expuestas.

## Por qué importa Search Engine Discovery

El descubrimiento de motores de búsqueda es un componente crucial del reconocimiento web por varias razones:

- `Open Source`: La información recopilada es de acceso público, lo que la convierte en una forma legal y ética de obtener información sobre un objetivo.
    
- `Breadth of Information`: Los motores de búsqueda indexan una gran parte de la web, ofreciendo una amplia gama de posibles fuentes de información.
    
- `Ease of Use`: Los motores de búsqueda son fáciles de usar y no requieren habilidades técnicas especializadas.
    
- `Cost-Effective`: Es un recurso gratuito y fácilmente disponible para la recopilación de información.
    

La información que puede reunir de los Motores de Búsqueda también se puede aplicar de varias maneras diferentes:

- `Security Assessment`: Identificar vulnerabilidades, datos expuestos y posibles vectores de ataque.
- `Competitive Intelligence`: Recopilación de información sobre los productos, servicios y estrategias de los competidores.
- `Investigative Journalism`: Descubrir conexiones ocultas, transacciones financieras y prácticas poco éticas.
- `Threat Intelligence`: Identificar amenazas emergentes, rastrear actores maliciosos y predecir posibles ataques.

Sin embargo, es importante tener en cuenta que el descubrimiento de motores de búsqueda tiene limitaciones. Los motores de búsqueda no indexan toda la información, y algunos datos pueden ocultarse o protegerse deliberadamente.

## Operadores de Búsqueda

Los operadores de búsqueda son como los códigos secretos de los motores de búsqueda. Estos comandos y modificadores especiales desbloquean un nuevo nivel de precisión y control, lo que le permite identificar tipos específicos de información en medio de la inmensidad de la web indexada.

Si bien la sintaxis exacta puede variar ligeramente entre los motores de búsqueda, los principios subyacentes siguen siendo consistentes. Profundicemos en algunos operadores de búsqueda esenciales y avanzados:

|Operador|Descripción del Operador|Ejemplo|Ejemplo Descripción|
|:--|:--|:--|:--|
|`site:`|Limita los resultados a un sitio web o dominio específico.|`site:example.com`|Encuentre todas las páginas de acceso público en example.com.|
|`inurl:`|Encuentra páginas con un término específico en la URL.|`inurl:login`|Busque páginas de inicio de sesión en cualquier sitio web.|
|`filetype:`|Búsquedas de archivos de un tipo particular.|`filetype:pdf`|Encuentra documentos PDF descargables.|
|`intitle:`|Encuentra páginas con un término específico en el título.|`intitle:"confidential report"`|Busque documentos titulados "informe confidencial" o variaciones similares.|
|`intext:`o `inbody:`|Busca un término dentro del texto del cuerpo de las páginas.|`intext:"password reset"`|Identifique las páginas web que contienen el término “password reset”.|
|`cache:`|Muestra la versión en caché de una página web (si está disponible).|`cache:example.com`|Vea la versión en caché de example.com para ver su contenido anterior.|
|`link:`|Encuentra páginas que enlazan a una página web específica.|`link:example.com`|Identificar sitios web que enlazan a example.com.|
|`related:`|Encuentra sitios web relacionados con una página web específica.|`related:example.com`|Descubre sitios web similares a example.com.|
|`info:`|Proporciona un resumen de la información sobre una página web.|`info:example.com`|Obtenga detalles básicos sobre example.com, como su título y descripción.|
|`define:`|Proporciona definiciones de una palabra o frase.|`define:phishing`|Obtenga una definición de "phishing" de varias fuentes.|
|`numrange:`|Busca números dentro de un rango específico.|`site:example.com numrange:1000-2000`|Encuentre páginas en example.com que contengan números entre 1000 y 2000.|
|`allintext:`|Encuentra páginas que contienen todas las palabras especificadas en el texto del cuerpo.|`allintext:admin password reset`|Busque páginas que contengan "admin" y "restablecimiento de contraseña" en el texto del cuerpo.|
|`allinurl:`|Encuentra páginas que contienen todas las palabras especificadas en la URL.|`allinurl:admin panel`|Busque páginas con "admin" y "panel" en la URL.|
|`allintitle:`|Encuentra páginas que contienen todas las palabras especificadas en el título.|`allintitle:confidential report 2023`|Busque páginas con "confidencial," "informe," y "2023" en el título.|
|`AND`|Reduzca los resultados al exigir que todos los términos estén presentes.|`site:example.com AND (inurl:admin OR inurl:login)`|Encuentre páginas de administración o de inicio de sesión específicamente en example.com.|
|`OR`|Amplía los resultados al incluir páginas con cualquiera de los términos.|`"linux" OR "ubuntu" OR "debian"`|Busque páginas web que mencionen Linux, Ubuntu o Debian.|
|`NOT`|Excluye los resultados que contienen el término especificado.|`site:bank.com NOT inurl:login`|Encuentra páginas en bank.com excluyendo las páginas de inicio de sesión.|
|`*`(wildcard)|Representa cualquier carácter o palabra.|`site:socialnetwork.com filetype:pdf user* manual`|Busque manuales de usuario (guía del usuario, manual del usuario) en formato PDF en socialnetwork.com.|
|`..`(búsqueda de rango)|Encuentra resultados dentro de un rango numérico especificado.|`site:ecommerce.com "price" 100..500`|Busque productos con un precio entre 100 y 500 en un sitio web de comercio electrónico.|
|`" "`(marcas de cotización)|Búsquedas de frases exactas.|`"information security policy"`|Encuentre documentos que mencionen la frase exacta "política de seguridad de la información".|
|`-`(signo menos)|Excluye términos de los resultados de búsqueda.|`site:news.com -inurl:sports`|Busque artículos de noticias en news.com excluyendo contenido relacionado con deportes.|

### Dorking Google

Google Dorking, también conocido como Google Hacking, es una técnica que aprovecha el poder de los operadores de búsqueda para descubrir información confidencial, vulnerabilidades de seguridad o contenido oculto en sitios web, utilizando Google Search.

Aquí hay algunos ejemplos comunes de Google Dorks, para obtener más ejemplos, consulte el [Base de datos de Google Hacking](https://www.exploit-db.com/google-hacking-database):

- Encontrar Páginas de Inicio de Sesión:
    - `site:example.com inurl:login`
    - `site:example.com (inurl:login OR inurl:admin)`
- Identificación de Archivos Expuestos:
    - `site:example.com filetype:pdf`
    - `site:example.com (filetype:xls OR filetype:docx)`
- Descubriendo Archivos de Configuración:
    - `site:example.com inurl:config.php`
    - `site:example.com (ext:conf OR ext:cnf)`(búsquedas de extensiones comúnmente utilizadas para archivos de configuración)
- Localización de copias de seguridad de bases de datos:
    - `site:example.com inurl:backup`
    - `site:example.com filetype:sql`



# Archivos Web

---

En el acelerado mundo digital, los sitios web van y vienen, dejando solo rastros fugaces de su existencia. Sin embargo, gracias a la [Wayback Machine de Internet Archive](https://web.archive.org/)tenemos una oportunidad única de volver a visitar el pasado y explorar las huellas digitales de los sitios web como lo fueron antes.

### ¿Qué es la Wayback Machine?

![Página de inicio de Internet Archive Wayback Machine con barra de búsqueda para páginas web, herramientas como extensiones de navegador y opciones para el servicio de suscripción, búsqueda de colecciones y guardar páginas.](https://academy.hackthebox.com/storage/modules/144/wayback.png)

`The Wayback Machine`es un archivo digital de la World Wide Web y otra información en Internet. Fundada por Internet Archive, una organización sin fines de lucro, ha estado archivando sitios web desde 1996.

Permite a los usuarios "retroceder en el tiempo" y ver instantáneas de sitios web tal como aparecieron en varios puntos de su historia. Estas instantáneas, conocidas como capturas o archivos, proporcionan una visión de las versiones anteriores de un sitio web, incluido su diseño, contenido y funcionalidad.

### ¿Cómo Funciona la Máquina Wayback?

La Wayback Machine funciona mediante el uso de rastreadores web para capturar instantáneas de sitios web a intervalos regulares automáticamente. Estos rastreadores navegan a través de la web, siguiendo enlaces y páginas de indexación, al igual que cómo funcionan los rastreadores de motores de búsqueda. Sin embargo, en lugar de simplemente indexar la información para fines de búsqueda, Wayback Machine almacena todo el contenido de las páginas, incluyendo HTML, CSS, JavaScript, imágenes y otros recursos.

La operación de la Wayback Machine se puede visualizar como un proceso de tres pasos:

![Diagrama de flujo con tres pasos: Rastreo, Archivo, Acceso.](https://mermaid.ink/svg/pako:eNpNjkEOgjAQRa_SzBou0IUJ4lI3uqQsJu1IG2lLhlZjCHe3YGLc_f9m8vMW0NEQSBgYJyvOVxWarmV8jS4Mvajrgzh2DWvrnhtQ4b_t57ZrtKZ53gBU4Ik9OlMWFxWEUJAseVIgSzTIDwUqrOUPc4q3d9AgE2eqgGMeLMg7jnNpeTKY6OSwaPkfJeNS5MtXePdeP1LGQQs)

1. `Crawling`: La Wayback Machine emplea rastreadores web automatizados, a menudo llamados "bots", para navegar por Internet sistemáticamente. Estos bots siguen enlaces de una página web a otra, como la forma en que haría clic en hipervínculos para explorar un sitio web. Sin embargo, en lugar de solo leer el contenido, estos bots descargan copias de las páginas web que encuentran.
2. `Archiving`: Las páginas web descargadas, junto con sus recursos asociados, como imágenes, hojas de estilo y scripts, se almacenan en el vasto archivo de Wayback Machine. Cada página web capturada está vinculada a una fecha y hora específica, creando una instantánea histórica del sitio web en ese momento. Este proceso de archivo ocurre a intervalos regulares, a veces diarios, semanales o mensuales, dependiendo de la popularidad y la frecuencia de las actualizaciones del sitio web.
3. `Accessing`: Los usuarios pueden acceder a estas instantáneas archivadas a través de la interfaz de Wayback Machine. Al ingresar la URL de un sitio web y seleccionar una fecha, puede ver cómo se veía el sitio web en ese punto específico. Wayback Machine le permite navegar por páginas individuales y proporciona herramientas para buscar términos específicos dentro del contenido archivado o descargar sitios web archivados completos para su análisis fuera de línea.

La frecuencia con la que la Wayback Machine archiva un sitio web varía. Algunos sitios web pueden archivarse varias veces al día, mientras que otros pueden tener solo algunas instantáneas distribuidas durante varios años. Los factores que influyen en esta frecuencia incluyen la popularidad del sitio web, su tasa de cambio y los recursos disponibles para el Archivo de Internet.

Es importante tener en cuenta que la Wayback Machine no captura todas las páginas web en línea. Prioriza los sitios web considerados de valor cultural, histórico o de investigación. Además, los propietarios de sitios web pueden solicitar que su contenido sea excluido de Wayback Machine, aunque esto no siempre está garantizado.

## Por qué la Máquina Wayback Importa para el Reconocimiento Web

La Wayback Machine es un tesoro para el reconocimiento web, que ofrece información que puede ser instrumental en varios escenarios. Su importancia radica en su capacidad para revelar el pasado de un sitio web, proporcionando información valiosa que puede no ser evidente en su estado actual:

1. `Uncovering Hidden Assets and Vulnerabilities`: La Wayback Machine le permite descubrir páginas web antiguas, directorios, archivos o subdominios que podrían no ser accesibles en el sitio web actual, lo que podría exponer información confidencial o fallas de seguridad.
2. `Tracking Changes and Identifying Patterns`: Al comparar instantáneas históricas, puede observar cómo ha evolucionado el sitio web, revelando cambios en la estructura, el contenido, las tecnologías y las vulnerabilidades potenciales.
3. `Gathering Intelligence`: El contenido archivado puede ser una fuente valiosa de OSINT, proporcionando información sobre las actividades pasadas del objetivo, las estrategias de marketing, los empleados y las opciones tecnológicas.
4. `Stealthy Reconnaissance`: El acceso a instantáneas archivadas es una actividad pasiva que no interactúa directamente con la infraestructura del objetivo, lo que la convierte en una forma menos detectable de recopilar información.

## Going Wayback en HTB

Podemos ver la primera versión archivada de HackTheBox ingresando la página que estamos buscando en la Wayback Machine y seleccionando la fecha de captura más temprana disponible, siendo `2017-06-10 @ 04h23:01`

![Wayback Machine captura de la página de inicio de Hack The Box, versión 0.8.7 beta, con un logotipo de cubo geométrico y una sección 'Acerca' que describe la plataforma para probar habilidades de penetración.](https://academy.hackthebox.com/storage/modules/144/wayback-htb.png)



# Automatizando Recon

---

Si bien el reconocimiento manual puede ser efectivo, también puede llevar mucho tiempo y ser propenso a errores humanos. La automatización de las tareas de reconocimiento web puede mejorar significativamente la eficiencia y la precisión, lo que le permite recopilar información a escala e identificar posibles vulnerabilidades más rápidamente.

## ¿Por qué Automatizar el Reconocimiento?

La automatización ofrece varias ventajas clave para el reconocimiento web:

- `Efficiency`: Las herramientas automatizadas pueden realizar tareas repetitivas mucho más rápido que los humanos, liberando tiempo valioso para el análisis y la toma de decisiones.
- `Scalability`: La automatización le permite escalar sus esfuerzos de reconocimiento a través de un gran número de objetivos o dominios, descubriendo un alcance más amplio de información.
- `Consistency`: Las herramientas automatizadas siguen reglas y procedimientos predefinidos, asegurando resultados consistentes y reproducibles y minimizando el riesgo de error humano.
- `Comprehensive Coverage`: La automatización se puede programar para realizar una amplia gama de tareas de reconocimiento, incluida la enumeración de DNS, el descubrimiento de subdominios, el rastreo web, el escaneo de puertos y más, lo que garantiza una cobertura completa de posibles vectores de ataque.
- `Integration`: Muchos marcos de automatización permiten una fácil integración con otras herramientas y plataformas, creando un flujo de trabajo continuo desde el reconocimiento hasta la evaluación y explotación de vulnerabilidades.

## Marcos de Reconocimiento

Estos marcos tienen como objetivo proporcionar un conjunto completo de herramientas para el reconocimiento web:

- [FinalRecon](https://github.com/thewhiteh4t/FinalRecon): Una herramienta de reconocimiento basada en Python que ofrece una gama de módulos para diferentes tareas como la verificación de certificados SSL, la recopilación de información de Whois, el análisis de encabezado y el rastreo. Su estructura modular permite una fácil personalización para necesidades específicas.
- [Recon-ng](https://github.com/lanmaster53/recon-ng): Un potente framework escrito en Python que ofrece una estructura modular con varios módulos para diferentes tareas de reconocimiento. Puede realizar enumeración de DNS, descubrimiento de subdominios, escaneo de puertos, rastreo web e incluso explotar vulnerabilidades conocidas.
- [elHarvester](https://github.com/laramies/theHarvester): Diseñado específicamente para recopilar direcciones de correo electrónico, subdominios, hosts, nombres de empleados, puertos abiertos y banners de diferentes fuentes públicas como motores de búsqueda, servidores de claves PGP y la base de datos SHODAN. Es una herramienta de línea de comandos escrita en Python.
- [SpiderFoot](https://github.com/smicallef/spiderfoot): Una herramienta de automatización de inteligencia de código abierto que se integra con varias fuentes de datos para recopilar información sobre un objetivo, incluidas direcciones IP, nombres de dominio, direcciones de correo electrónico y perfiles de redes sociales. Puede realizar búsquedas de DNS, rastreo web, escaneo de puertos y más.
- [Marco OSINT](https://osintframework.com/): Una colección de varias herramientas y recursos para la recopilación de inteligencia de código abierto. Cubre una amplia gama de fuentes de información, incluidas las redes sociales, los motores de búsqueda, los registros públicos y más.

### FinalRecon

`FinalRecon`ofrece una gran cantidad de información de reconocimiento:

- `Header Information`: Revela los detalles del servidor, las tecnologías utilizadas y las posibles configuraciones erróneas de seguridad.
- `Whois Lookup`: Descubre los detalles de registro del dominio, incluida la información del registrante y los datos de contacto.
- `SSL Certificate Information`: Examina el certificado SSL/TLS para la validez, el emisor y otros detalles relevantes.
- `Crawler`:
    - HTML, CSS, JavaScript: Extrae enlaces, recursos y vulnerabilidades potenciales de estos archivos.
    - Enlaces Internos/Externos: Mapas de la estructura del sitio web e identifica las conexiones a otros dominios.
    - Imágenes, robots.txt, sitemap.xml: Reúne información sobre rutas de rastreo permitidas/no permitidas y estructura del sitio web.
    - Enlaces en JavaScript, Wayback Machine: Descubre enlaces ocultos y datos históricos del sitio web.
- `DNS Enumeration`: Consulta más de 40 tipos de registros DNS, incluidos los registros DMARC para la evaluación de seguridad del correo electrónico.
- `Subdomain Enumeration`: Aprovecha múltiples fuentes de datos (crt.sh, AnubisDB, ThreatMiner, CertSpotter, Facebook API, VirusTotal API, Shodan API, BeVigil API) para descubrir subdominios.
- `Directory Enumeration`: Admite listas de palabras personalizadas y extensiones de archivos para descubrir directorios y archivos ocultos.
- `Wayback Machine`: Recupera las URL de los últimos cinco años para analizar los cambios en el sitio web y las posibles vulnerabilidades.

La instalación es rápida y fácil:

  Automatizando Recon

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/thewhiteh4t/FinalRecon.git
vcrdcr@htb[/htb]$ cd FinalRecon
vcrdcr@htb[/htb]$ pip3 install -r requirements.txt
vcrdcr@htb[/htb]$ chmod +x ./finalrecon.py
vcrdcr@htb[/htb]$ ./finalrecon.py --help

usage: finalrecon.py [-h] [--url URL] [--headers] [--sslinfo] [--whois]
                     [--crawl] [--dns] [--sub] [--dir] [--wayback] [--ps]
                     [--full] [-nb] [-dt DT] [-pt PT] [-T T] [-w W] [-r] [-s]
                     [-sp SP] [-d D] [-e E] [-o O] [-cd CD] [-k K]

FinalRecon - All in One Web Recon | v1.1.6

optional arguments:
  -h, --help  show this help message and exit
  --url URL   Target URL
  --headers   Header Information
  --sslinfo   SSL Certificate Information
  --whois     Whois Lookup
  --crawl     Crawl Target
  --dns       DNS Enumeration
  --sub       Sub-Domain Enumeration
  --dir       Directory Search
  --wayback   Wayback URLs
  --ps        Fast Port Scan
  --full      Full Recon

Extra Options:
  -nb         Hide Banner
  -dt DT      Number of threads for directory enum [ Default : 30 ]
  -pt PT      Number of threads for port scan [ Default : 50 ]
  -T T        Request Timeout [ Default : 30.0 ]
  -w W        Path to Wordlist [ Default : wordlists/dirb_common.txt ]
  -r          Allow Redirect [ Default : False ]
  -s          Toggle SSL Verification [ Default : True ]
  -sp SP      Specify SSL Port [ Default : 443 ]
  -d D        Custom DNS Servers [ Default : 1.1.1.1 ]
  -e E        File Extensions [ Example : txt, xml, php ]
  -o O        Export Format [ Default : txt ]
  -cd CD      Change export directory [ Default : ~/.local/share/finalrecon ]
  -k K        Add API key [ Example : shodan@key ]
```

Para empezar, primero clonarás el `FinalRecon` repositorio de GitHub usando `git clone https://github.com/thewhiteh4t/FinalRecon.git`. Esto creará un nuevo directorio llamado "FinalRecon" que contiene todos los archivos necesarios.

A continuación, navegue en el directorio recién creado con `cd FinalRecon`. Una vez dentro, instalará las dependencias de Python requeridas usando `pip3 install -r requirements.txt`. Esto asegura que `FinalRecon` tiene todas las bibliotecas y módulos que necesita para funcionar correctamente.

Para asegurarse de que el script principal es ejecutable, deberá cambiar los permisos del archivo utilizando `chmod +x ./finalrecon.py`. Esto le permite ejecutar el script directamente desde su terminal.

Finalmente, puedes verificar eso `FinalRecon` se instala correctamente y obtiene una visión general de sus opciones disponibles ejecutándose `./finalrecon.py --help`. Esto mostrará un mensaje de ayuda con detalles sobre cómo usar la herramienta, incluidos los diversos módulos y sus respectivas opciones:

|Opción|Argumento|Descripción|
|---|---|---|
|`-h`, `--help`||Mostrar el mensaje de ayuda y salir.|
|`--url`|URL|Especifique la URL de destino.|
|`--headers`||Recuperar información de encabezado para la URL de destino.|
|`--sslinfo`||Obtenga información de certificado SSL para la URL de destino.|
|`--whois`||Realice una búsqueda de Whois para el dominio de destino.|
|`--crawl`||Rastrea el sitio web de destino.|
|`--dns`||Realice la enumeración de DNS en el dominio de destino.|
|`--sub`||Enumerar subdominios para el dominio de destino.|
|`--dir`||Buscar directorios en el sitio web de destino.|
|`--wayback`||Recuperar las URL de Wayback para el objetivo.|
|`--ps`||Realice un escaneo rápido de puertos en el objetivo.|
|`--full`||Realice un escaneo de reconocimiento completo en el objetivo.|

Por ejemplo, si queremos `FinalRecon` para recopilar información de encabezado y realizar una búsqueda de Whois `inlanefreight.com`, usaríamos las banderas correspondientes (`--headers` y `--whois`), entonces el comando sería:

  Automatizando Recon

```shell-session
vcrdcr@htb[/htb]$ ./finalrecon.py --headers --whois --url http://inlanefreight.com

 ______  __   __   __   ______   __
/\  ___\/\ \ /\ "-.\ \ /\  __ \ /\ \
\ \  __\\ \ \\ \ \-.  \\ \  __ \\ \ \____
 \ \_\   \ \_\\ \_\\"\_\\ \_\ \_\\ \_____\
  \/_/    \/_/ \/_/ \/_/ \/_/\/_/ \/_____/
 ______   ______   ______   ______   __   __
/\  == \ /\  ___\ /\  ___\ /\  __ \ /\ "-.\ \
\ \  __< \ \  __\ \ \ \____\ \ \/\ \\ \ \-.  \
 \ \_\ \_\\ \_____\\ \_____\\ \_____\\ \_\\"\_\
  \/_/ /_/ \/_____/ \/_____/ \/_____/ \/_/ \/_/

[>] Created By   : thewhiteh4t
 |---> Twitter   : https://twitter.com/thewhiteh4t
 |---> Community : https://twc1rcle.com/
[>] Version      : 1.1.6

[+] Target : http://inlanefreight.com

[+] IP Address : 134.209.24.248

[!] Headers :

Date : Tue, 11 Jun 2024 10:08:00 GMT
Server : Apache/2.4.41 (Ubuntu)
Link : <https://www.inlanefreight.com/index.php/wp-json/>; rel="https://api.w.org/", <https://www.inlanefreight.com/index.php/wp-json/wp/v2/pages/7>; rel="alternate"; type="application/json", <https://www.inlanefreight.com/>; rel=shortlink
Vary : Accept-Encoding
Content-Encoding : gzip
Content-Length : 5483
Keep-Alive : timeout=5, max=100
Connection : Keep-Alive
Content-Type : text/html; charset=UTF-8

[!] Whois Lookup : 

   Domain Name: INLANEFREIGHT.COM
   Registry Domain ID: 2420436757_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.registrar.amazon.com
   Registrar URL: http://registrar.amazon.com
   Updated Date: 2023-07-03T01:11:15Z
   Creation Date: 2019-08-05T22:43:09Z
   Registry Expiry Date: 2024-08-05T22:43:09Z
   Registrar: Amazon Registrar, Inc.
   Registrar IANA ID: 468
   Registrar Abuse Contact Email: abuse@amazonaws.com
   Registrar Abuse Contact Phone: +1.2024422253
   Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
   Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
   Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
   Name Server: NS-1303.AWSDNS-34.ORG
   Name Server: NS-1580.AWSDNS-05.CO.UK
   Name Server: NS-161.AWSDNS-20.COM
   Name Server: NS-671.AWSDNS-19.NET
   DNSSEC: unsigned
   URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/


[+] Completed in 0:00:00.257780

[+] Exported : /home/htb-ac-643601/.local/share/finalrecon/dumps/fr_inlanefreight.com_11-06-2024_11:07:59
```




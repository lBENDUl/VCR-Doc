---
tags:
  - Enumeración
---
## Intro

IIS tilde directorio enumeración es una técnica utilizada para descubrir archivos ocultos, directorios y nombres de archivos cortos (aka el `8.3 format`) en algunas versiones de Microsoft Internet Information Services (IIS) servidores web. Este método aprovecha una vulnerabilidad específica en IIS, como resultado de cómo gestiona los nombres de archivos cortos dentro de sus directorios.

Cuando se crea un archivo o carpeta en un servidor IIS, Windows genera un nombre de archivo corto en el servidor `8.3 format`, que consta de ocho caracteres para el nombre del archivo, un período y tres caracteres para la extensión. Curiosamente, estos nombres de archivos cortos pueden otorgar acceso a sus archivos y carpetas correspondientes, incluso si estaban destinados a estar ocultos o inaccesibles.

La tilde (`~`) carácter, seguido de un número de secuencia, significa un nombre de archivo corto en una URL. Por lo tanto, si alguien determina el nombre de archivo corto de un archivo o carpeta, puede explotar el carácter tilde y el nombre de archivo corto en la URL para acceder a datos confidenciales u recursos ocultos.

La enumeración de directorios IIS tilde implica principalmente el envío de solicitudes HTTP al servidor con distintas combinaciones de caracteres en la URL para identificar nombres de archivos cortos válidos. Una vez que se detecta un nombre de archivo corto válido, esta información se puede utilizar para acceder al recurso relevante o enumerar la estructura del directorio.

El proceso de enumeración comienza enviando solicitudes con varios caracteres siguiendo la tilde:

```http
http://example.com/~a
http://example.com/~b
http://example.com/~c
...
```

Supongamos que el servidor contiene un directorio oculto llamado SecretDocuments. Cuando se envía una solicitud a `http://example.com/~s`, el servidor responde con un `200 OK` código de estado, que revela un directorio con un nombre corto que comienza con "s". El proceso de enumeración continúa agregando más caracteres:

```http
http://example.com/~se
http://example.com/~sf
http://example.com/~sg
...
```

Para la solicitud `http://example.com/~se`, el servidor devuelve a `200 OK` código de estado, refinando aún más el nombre corto a "se". Se envían solicitudes adicionales, tales como:

```http
http://example.com/~sec
http://example.com/~sed
http://example.com/~see
...
```

El servidor entrega un `200 OK` código de estado para la solicitud `http://example.com/~sec`, estrechando aún más el nombre corto a "seg".

Continuando con este procedimiento, el nombre corto `secret~1` finalmente se descubre cuando el servidor devuelve un `200 OK` código de estado para la solicitud `http://example.com/~secret`.

Una vez que el nombre corto `secret~1` se identifica, se puede realizar una enumeración de nombres de archivos específicos dentro de esa ruta, lo que podría exponer documentos confidenciales.

Por ejemplo, si el nombre corto `secret~1` se determina para el directorio oculto SecretDocuments, se puede acceder a los archivos de ese directorio enviando solicitudes como:

```http
http://example.com/secret~1/somefile.txt
http://example.com/secret~1/anotherfile.docx
```

La misma técnica de enumeración de directorios IIS tilde también puede detectar nombres de archivos cortos 8.3 para archivos dentro del directorio. Después de obtener los nombres cortos, se puede acceder directamente a esos archivos utilizando los nombres cortos en las solicitudes.

```http
http://example.com/secret~1/somefi~1.txt
```

En nombres de archivo cortos 8.3, como `somefi~1.txt`, el número "1" es un identificador único que distingue archivos con nombres similares dentro del mismo directorio. Los números que siguen a la tilde (`~`) ayuda al sistema de archivos a diferenciar entre archivos que comparten similitudes en sus nombres, asegurando que cada archivo tenga un nombre de archivo corto distinto de 8.3.

Por ejemplo, si dos archivos nombrados `somefile.txt` y `somefile1.txt` existen en el mismo directorio, sus nombres de archivo cortos 8.3 serían:

- `somefi~1.txt`para `somefile.txt`
- `somefi~2.txt`para `somefile1.txt`

## Enumeración

La fase inicial implica mapear el objetivo y determinar qué servicios están operando en sus respectivos puertos.

#### Nmap - Puertos abiertos

```shell-session
vcrdcr@htb[/htb]$ nmap -p- -sV -sC --open 10.129.224.91

Starting Nmap 7.92 ( https://nmap.org ) at 2023-03-14 19:44 GMT
Nmap scan report for 10.129.224.91
Host is up (0.011s latency).
Not shown: 65534 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: Bounty
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 183.38 seconds

```

IIS 7.5 se ejecuta en el puerto 80. Ejecutar un ataque de enumeración de tilde en esta versión podría ser una opción viable.

#### Tilde Enumeración usando IIS ShortName Scanner

El envío manual de solicitudes HTTP para cada letra del alfabeto puede ser un proceso tedioso. Afortunadamente, hay una herramienta llamada `IIS-ShortName-Scanner` eso puede automatizar esta tarea. Puedes encontrarlo en GitHub en el siguiente enlace: [IIS-ShortName-Scanner](https://github.com/irsdl/IIS-ShortName-Scanner). Para usar `IIS-ShortName-Scanner`, deberá instalar Oracle Java en Pwnbox o en su VM local. Los detalles se pueden encontrar en el siguiente enlace. [Cómo instalar Oracle Java](https://ubuntuhandbook.org/index.php/2022/03/install-jdk-18-ubuntu/)

Cuando ejecute el siguiente comando, le pedirá un proxy, simplemente presione enter para No.

```shell-session
vcrdcr@htb[/htb]$ java -jar iis_shortname_scanner.jar 0 5 http://10.129.204.231/

Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
Do you want to use proxy [Y=Yes, Anything Else=No]? 
# IIS Short Name (8.3) Scanner version 2023.0 - scan initiated 2023/03/23 15:06:57
Target: http://10.129.204.231/
|_ Result: Vulnerable!
|_ Used HTTP method: OPTIONS
|_ Suffix (magic part): /~1/
|_ Extra information:
  |_ Number of sent requests: 553
  |_ Identified directories: 2
    |_ ASPNET~1
    |_ UPLOAD~1
  |_ Identified files: 3
    |_ CSASPX~1.CS
      |_ Actual extension = .CS
    |_ CSASPX~1.CS??
    |_ TRANSF~1.ASP
```

Al ejecutar la herramienta, descubre 2 directorios y 3 archivos. Sin embargo, el objetivo no permite `GET` acceso a `http://10.129.204.231/TRANSF~1.ASP`, requiriendo la fuerza bruta del nombre de archivo restante.

#### Generar Lista de palabras

La imagen pwnbox ofrece una extensa colección de listas de palabras ubicadas en el `/usr/share/wordlists/` directorio, que se puede utilizar para este propósito.

```shell-session
vcrdcr@htb[/htb]$ egrep -r ^transf /usr/share/wordlists/* | sed 's/^[^:]*://' > /tmp/list.txt
```

Este comando combina `egrep` y `sed` para filtrar y modificar el contenido de los archivos de entrada, guarde los resultados en un nuevo archivo.

|**Parte de Comando**|**Descripción**|
|---|---|
|`egrep -r ^transf`|El `egrep` el comando se utiliza para buscar líneas que contienen un patrón específico en los archivos de entrada. El `-r` flag indica una búsqueda recursiva a través de directorios. El `^transf` el patrón coincide con cualquier línea que comience con "transf". La salida de este comando serán líneas que comienzan con "transf" junto con sus nombres de archivo de origen.|
|`\|`|El símbolo de la tubería (`\|`) se usa para pasar la salida del primer comando (`egrep`) al segundo comando (`sed`). En este caso, las líneas que comienzan con "transf" y sus nombres de archivo serán la entrada para el `sed` comando.|
|`sed 's/^[^:]*://'`|El `sed` el comando se utiliza para realizar una operación de búsqueda y reemplazo en su entrada (en este caso, la salida de `egrep`). El `'s/^[^:]*://'` la expresión dice `sed` para encontrar cualquier secuencia de caracteres al comienzo de una línea (`^`) hasta el primer colon (`:`), y reemplácelos con nada (, eliminando efectivamente el texto coincidente). El resultado serán las líneas que comienzan con "transf" pero sin los nombres de archivo y los dos puntos.|
|`> /tmp/list.txt`|El símbolo mayor que (`>`) se usa para redirigir la salida de todo el comando (i.e., las líneas modificadas) a un nuevo archivo llamado `/tmp/list.txt`.|

#### Gobuster Enumeración

Una vez que haya creado la lista de palabras personalizada, puede usar `gobuster` para enumerar todos los elementos en el objetivo. GoBuster es una herramienta de fuerza bruta de directorio y archivo de código abierto escrita en el lenguaje de programación Go. Está diseñado para probadores de penetración y profesionales de seguridad para ayudar a identificar y descubrir archivos ocultos, directorios o recursos en servidores web durante las evaluaciones de seguridad.

```shell-session
vcrdcr@htb[/htb]$ gobuster dir -u http://10.129.204.231/ -w /tmp/list.txt -x .aspx,.asp

===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.204.231/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /tmp/list.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              asp,aspx
[+] Timeout:                 10s
===============================================================
2023/03/23 15:14:05 Starting gobuster in directory enumeration mode
===============================================================
/transf**.aspx        (Status: 200) [Size: 941]
Progress: 306 / 309 (99.03%)
===============================================================
2023/03/23 15:14:11 Finished
===============================================================
```

Desde la salida redactada, puede ver eso `gobuster` ha identificado con éxito un `.aspx` archivo como el nombre de archivo completo correspondiente al nombre corto descubierto anteriormente `TRANSF~1.ASP`


## Ataque


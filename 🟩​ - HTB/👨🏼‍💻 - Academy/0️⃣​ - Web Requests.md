#HTB-Academy #Bug-Bounty-Hunter

# URL
 ![url_structure](https://academy.hackthebox.com/storage/modules/35/url_structure.png)


| **Component**  | **Example**          | **Description**                                                                                                                                                                       |
| -------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Scheme`       | `http://` `https://` | This is used to identify the protocol being accessed by the client, and ends with a colon and a double slash (`://`)                                                                  |
| `User Info`    | `admin:password@`    | This is an optional component that contains the credentials (separated by a colon `:`) used to authenticate to the host, and is separated from the host with an at sign (`@`)         |
| `Host`         | `inlanefreight.com`  | The host signifies the resource location. This can be a hostname or an IP address                                                                                                     |
| `Port`         | `:80`                | The `Port` is separated from the `Host` by a colon (`:`). If no port is specified, `http` schemes default to port `80` and `https` default to port `443`                              |
| `Path`         | `/dashboard.php`     | This points to the resource being accessed, which can be a file or a folder. If there is no path specified, the server returns the default index (e.g. `index.html`).                 |
| `Query String` | `?login=true`        | The query string starts with a question mark (`?`), and consists of a parameter (e.g. `login`) and a value (e.g. `true`). Multiple parameters can be separated by an ampersand (`&`). |
| `Fragments`    | `#status`            | Fragments are processed by the browsers on the client-side to locate sections within the primary resource (e.g. a header or section on the page).                                     |

## URL FLOW

  
![HTTP_Flow](https://academy.hackthebox.com/storage/modules/35/HTTP_Flow.png)
El diagrama anterior presenta la anatomía de una petición HTTP a muy alto nivel. La primera vez que un usuario introduce la URL (inlanefreight.com) en el navegador, éste envía una petición a un servidor DNS (Domain Name Resolution) para resolver el dominio y obtener su IP. El servidor DNS busca la dirección IP de inlanefreight.com y la devuelve. Todos los nombres de dominio deben resolverse de esta manera, ya que un servidor no puede comunicarse sin una dirección IP.


>**NOTA**
>Nuestros navegadores suelen buscar primero los registros en el archivo local '/etc/hosts', y si el dominio solicitado no existe en él, entonces contactarían con otros servidores DNS. Podemos utilizar '/etc/hosts' para añadir manualmente registros para la resolución DNS, añadiendo la IP seguida del nombre de dominio.

Una vez que el navegador obtiene la dirección IP vinculada al dominio solicitado, envía una petición GET al puerto HTTP predeterminado (por ejemplo, 80), solicitando la raíz / ruta. A continuación, el servidor web recibe la petición y la procesa. Por defecto, los servidores están configurados para devolver un archivo index cuando se recibe una petición de /.

En este caso, el contenido de index.html es leído y devuelto por el servidor web como respuesta HTTP. La respuesta también contiene el código de estado (por ejemplo, 200 OK), que indica que la solicitud se ha procesado correctamente. A continuación, el navegador web renderiza el contenido de index.html y lo presenta al usuario.

> **NOTA**
> Este módulo se centra principalmente en las peticiones web HTTP. Para más información sobre HTML y aplicaciones web, puede consultar el módulo Introducción a las aplicaciones web.

## cURL

En este módulo, vamos a enviar solicitudes web a través de dos de las herramientas más importantes para cualquier probador de penetración web, un navegador web, como Chrome o Firefox, y la herramienta de línea de comandos cURL.

cURL (client URL) es una herramienta de línea de comandos y una biblioteca que soporta principalmente HTTP junto con muchos otros protocolos. Esto hace que sea un buen candidato para los scripts, así como la automatización, por lo que es esencial para el envío de diversos tipos de solicitudes web desde la línea de comandos, lo cual es necesario para muchos tipos de pruebas de penetración web.

Podemos enviar una petición HTTP básica a cualquier URL utilizándola como argumento para cURL, como sigue:

HyperText Transfer Protocol (HTTP)

```shell-session
vcrdcr@htb[/htb]$ curl inlanefreight.com

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
...SNIP...
```

Vemos que cURL no renderiza el código HTML/JavaScript/CSS, a diferencia de un navegador web, sino que lo imprime en su formato crudo. Sin embargo, como probadores de penetración, estamos principalmente interesados en el contexto de solicitud y respuesta, que por lo general se hace mucho más rápido y más conveniente que un navegador web.

También podemos utilizar cURL para descargar una página o un archivo y dar salida al contenido en un archivo utilizando la bandera -O. Si queremos especificar el nombre del archivo de salida, podemos utilizar la bandera -o y especificar el nombre. De lo contrario, podemos utilizar -O y cURL utilizará el nombre del archivo remoto, de la siguiente manera:

  
HyperText Transfer Protocol (HTTP)

```shell-session
vcrdcr@htb[/htb]$ curl -O inlanefreight.com/index.html
vcrdcr@htb[/htb]$ ls
index.html
```

Como podemos ver, la salida no se imprimió esta vez, sino que se guardó en index.html. Observamos que cURL sigue imprimiendo algún estado mientras procesa la petición. Podemos silenciar el estado con la bandera -s, como sigue:

  
HyperText Transfer Protocol (HTTP)

```shell-session
vcrdcr@htb[/htb]$ curl -s -O inlanefreight.com/index.html
```

Esta vez, cURL no imprimió nada, ya que la salida se guardó en el archivo index.html. Por último, podemos utilizar la bandera -h para ver qué otras opciones podemos utilizar con cURL:

  
HyperText Transfer Protocol (HTTP)

```shell-session
vcrdcr@htb[/htb]$ curl -h
Usage: curl [options...] <url>
 -d, --data <data>   HTTP POST data
 -h, --help <category> Get help for commands
 -i, --include       Include protocol response headers in the output
 -o, --output <file> Write to file instead of stdout
 -O, --remote-name   Write output to a file named as the remote file
 -s, --silent        Silent mode
 -u, --user <user:password> Server user and password
 -A, --user-agent <name> Send User-Agent <name> to server
 -v, --verbose       Make the operation more talkative

This is not the full help, this menu is stripped into categories.
Use "--help category" to get an overview of all categories.
Use the user manual `man curl` or the "--help all" flag for all options.
```

Como menciona el mensaje anterior, podemos usar --help all para imprimir un menú de ayuda más detallado, o --help category (por ejemplo -h http) para imprimir la ayuda detallada de una bandera específica. Si alguna vez necesitamos leer documentación más detallada, podemos usar man curl para ver la página completa del manual de cURL.

En las próximas secciones, cubriremos la mayoría de las banderas anteriores y veremos dónde debemos utilizar cada una de ellas.

# Hypertext Transfer Protocol Secure (HTTPS)

En la sección anterior hemos visto cómo se envían y procesan las peticiones HTTP. Sin embargo, uno de los inconvenientes significativos de HTTP es que todos los datos se transfieren en texto claro. Esto significa que cualquiera entre el origen y el destino puede realizar un ataque Man-in-the-middle (MiTM) para ver los datos transferidos.

Para contrarrestar este problema, se creó el protocolo HTTPS (HTTP Secure), en el que todas las comunicaciones se transfieren en formato cifrado, de modo que aunque un tercero interceptara la petición, no podría extraer los datos de ella. Por esta razón, HTTPS se ha convertido en el esquema dominante para los sitios web en Internet, y HTTP se está eliminando gradualmente, y pronto la mayoría de los navegadores web no permitirán visitar sitios web HTTP.

## HTTPS Overview

Si examinamos una petición HTTP, podemos ver el efecto de no aplicar comunicaciones seguras entre un navegador web y una aplicación web. Por ejemplo, el siguiente es el contenido de una solicitud HTTP de inicio de sesión:

![http_clear](https://academy.hackthebox.com/storage/modules/35/https_clear.png)


Podemos ver que las credenciales de inicio de sesión se pueden ver en texto claro. Esto facilitaría que alguien en la misma red (como una red inalámbrica pública) capturara la solicitud y reutilizara las credenciales con fines maliciosos.

En cambio, cuando alguien intercepta y analiza el tráfico de una solicitud HTTPS, vería algo como lo siguiente:

![https_google_enc](https://academy.hackthebox.com/storage/modules/35/https_google_enc.png)

Como vemos, los datos se transfieren como un único flujo cifrado, lo que hace muy difícil que alguien pueda capturar información como credenciales o cualquier otro dato sensible.

Los sitios web que aplican HTTPS pueden identificarse a través de **https://** en su URL (por ejemplo, https://www.google.com), así como el icono del candado en la barra de direcciones del navegador web, a la izquierda de la URL:

![https_google](https://academy.hackthebox.com/storage/modules/35/https_google.png)

Así, si visitamos un sitio web que utiliza HTTPS, como Google, todo el tráfico estaría cifrado.

## HTTPS Flow

Veamos cómo funciona HTTPS a alto nivel:
![HTTPS_Flow](https://academy.hackthebox.com/storage/modules/35/HTTPS_Flow.png)

Si escribimos **http://** en lugar de **https://** para visitar un sitio web que aplica HTTPS, el navegador intenta resolver el dominio y redirige al usuario al servidor web que aloja el sitio web de destino. Primero se envía una solicitud al puerto **80**, que es el protocolo HTTP sin cifrar. El servidor lo detecta y redirige al cliente al puerto seguro HTTPS **443** en su lugar. Esto se hace a través del código de respuesta **301 Moved Permanently**, del que hablaremos en una próxima sección.

A continuación, el cliente (navegador web) envía un paquete «client hello», dando información sobre sí mismo. A continuación, el servidor responde con un «server hello», seguido de un intercambio de claves para intercambiar certificados SSL. El cliente verifica la clave/certificado y envía uno propio. A continuación, se inicia un «handshake» cifrado para confirmar si el cifrado y la transferencia funcionan correctamente.

Una vez que el «handshake» finaliza con éxito, se continúa con la comunicación HTTP normal, que se encripta a continuación. Esta es una visión general de muy alto nivel del intercambio de claves, que está más allá del alcance de este módulo.

>NOTA
>Dependiendo de las circunstancias, un atacante puede ser capaz de realizar un ataque HTTP downgrade, que degrada la comunicación HTTPS a HTTP, haciendo que los datos transferidos sean en texto claro.
>Esto se hace configurando un proxy Man-In-The-Middle (MITM) para transferir todo el tráfico a través del host del atacante sin el conocimiento del usuario .Sin embargo, la mayoría de los navegadores, servidores y aplicaciones web modernos protegen contra este ataque.



## cURL for HTTPS

cURL debería manejar automáticamente todos los estándares de comunicación HTTPS y realizar un handshake seguro y luego cifrar y descifrar los datos automáticamente. Sin embargo, si alguna vez contactamos con un sitio web con un certificado SSL inválido o desactualizado, entonces cURL por defecto no procedería con la comunicación para protegernos de los ataques MITM antes mencionados:

  
Hypertext Transfer Protocol Secure (HTTPS)

```shell-session
vcrdcr@htb[/htb]$ curl https://inlanefreight.com

curl: (60) SSL certificate problem: Invalid certificate chain
More details here: https://curl.haxx.se/docs/sslcerts.html
...SNIP...
```

Los navegadores web modernos harían lo mismo, advirtiendo al usuario de que no visite un sitio web con un certificado SSL no válido.

Podemos encontrarnos con este problema cuando probamos una aplicación web local o con una aplicación web alojada con fines de práctica, ya que dichas aplicaciones web pueden no haber implementado todavía un certificado SSL válido. Para omitir la comprobación del certificado con cURL, podemos utilizar el indicador -k:

  
Hypertext Transfer Protocol Secure (HTTPS)

```shell-session
vcrdcr@htb[/htb]$ curl -k https://inlanefreight.com

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
...SNIP...
```

Como podemos ver, esta vez la solicitud ha sido aceptada y hemos recibido los datos de respuesta.

# HTTP Requests and Responses

Las comunicaciones HTTP consisten principalmente en una petición HTTP y una respuesta HTTP. Una petición HTTP la realiza el cliente (por ejemplo, cURL/navegador) y la procesa el servidor (por ejemplo, el servidor web). Las peticiones contienen todos los detalles que requerimos del servidor, incluyendo el recurso (p.e. URL, ruta, parámetros), cualquier dato de la petición, cabeceras u opciones que especifiquemos, y muchas otras opciones que discutiremos a lo largo de este módulo.

Una vez que el servidor recibe la petición HTTP, la procesa y responde enviando la respuesta HTTP, que contiene el código de respuesta, como se verá más adelante, y puede contener los datos del recurso si el solicitante tiene acceso a ellos.

## HTTP Request
Comencemos examinando el siguiente ejemplo de petición HTTP:


![raw_request](https://academy.hackthebox.com/storage/modules/35/raw_request.png)

La imagen anterior muestra una petición HTTP GET a la URL:

- `http://inlanefreight.com/users/login.html`

La primera línea de cualquier solicitud HTTP contiene tres campos principales «separados por espacios»:

| **Field** | **Example**         | **Description**                                                                                                                        |
| --------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Method`  | `GET`               | El método o verbo HTTP, que especifica el tipo de acción a realizar.                                                                   |
| `Path`    | `/users/login.html` | La ruta al recurso al que se accede. Este campo también puede ir acompañado de una cadena de consulta (por ejemplo, `?username=user`). |
| `Version` | `HTTP/1.1`          | El tercer y último campo se utiliza para indicar la versión HTTP.                                                                      |
|           |                     |                                                                                                                                        |

El siguiente conjunto de líneas contiene pares de valores de cabecera HTTP, como `Host`, `User-Agent`, `Cookie`, y muchas otras posibles cabeceras. Estas cabeceras se utilizan para especificar varios atributos de una petición. Las cabeceras terminan con una nueva línea, necesaria para que el servidor valide la petición. Por último, una petición puede terminar con el cuerpo de la petición y los datos.

**Nota:** La versión 1.X de HTTP envía las peticiones en texto claro y utiliza un carácter de nueva línea para separar los distintos campos y las distintas peticiones. La versión 2.X de HTTP, en cambio, envía las peticiones como datos binarios en forma de diccionario.

## HTTP Response

Una vez que el servidor procesa nuestra petición, envía su respuesta. A continuación se muestra un ejemplo de respuesta HTTP:

![raw_response](https://academy.hackthebox.com/storage/modules/35/raw_response.png)

La primera línea de una respuesta HTTP contiene dos campos separados por espacios. El primero es la `versión HTTP` (por ejemplo, `HTTP/1.1`) y el segundo indica el `código de respuesta HTTP` (por ejemplo, `200 OK`).

Los códigos de respuesta se utilizan para determinar el estado de la petición, como se verá más adelante. Después de la primera línea, la respuesta enumera sus cabeceras, de forma similar a una petición HTTP. Tanto las cabeceras de la petición como las de la respuesta se tratan en la siguiente sección.

Finalmente, la respuesta puede terminar con un cuerpo de respuesta, que está separado por una nueva línea después de las cabeceras. El cuerpo de la respuesta suele definirse como código `HTML`. Sin embargo, también puede responder con otros tipos de código como `JSON`, recursos del sitio web como imágenes, hojas de estilo o scripts, o incluso un documento como un PDF alojado en el servidor web.

## cURL

En nuestros ejemplos anteriores con cURL, sólo especificamos la URL y obtuvimos el cuerpo de la respuesta a cambio. Sin embargo, cURL también nos permite previsualizar la petición HTTP completa y la respuesta HTTP completa, lo que puede ser muy útil cuando realizamos pruebas de penetración web o escribimos exploits. Para ver la petición HTTP completa y la respuesta, podemos simplemente añadir la bandera `-v` verbose a nuestros comandos anteriores, y debería imprimir tanto la petición como la respuesta:

  HTTP Requests and Responses

```shell-session
vcrdcr@htb[/htb]$ curl inlanefreight.com -v

*   Trying SERVER_IP:80...
* TCP_NODELAY set
* Connected to inlanefreight.com (SERVER_IP) port 80 (#0)
> GET / HTTP/1.1
> Host: inlanefreight.com
> User-Agent: curl/7.65.3
> Accept: */*
> Connection: close
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 401 Unauthorized
< Date: Tue, 21 Jul 2020 05:20:15 GMT
< Server: Apache/X.Y.ZZ (Ubuntu)
< WWW-Authenticate: Basic realm="Restricted Content"
< Content-Length: 464
< Content-Type: text/html; charset=iso-8859-1
< 
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>

...SNIP...
```

Como podemos ver, esta vez, obtenemos la petición HTTP completa y la respuesta. La petición simplemente enviaba `GET / HTTP/1.1` junto con las cabeceras `Host`, `User-Agent` y `Accept`. A cambio, la respuesta HTTP contenía el mensaje `HTTP/1.1 401 Unauthorized`, que indica que no tenemos acceso al recurso solicitado, como veremos en una próxima sección. Al igual que la petición, la respuesta también contenía varias cabeceras enviadas por el servidor, incluyendo `Date`, `Content-Length`, y `Content-Type`. Finalmente, la respuesta contenía el cuerpo de la respuesta en HTML, que es el mismo que recibimos anteriormente cuando usamos cURL sin la bandera `-v`.

**Ejercicio:** El indicador `-vvv` muestra una salida aún más detallada. Intenta usar esta bandera para ver qué detalles adicionales de la petición y la respuesta se muestran con ella.

## Browser DevTools

La mayoría de los navegadores web modernos vienen con herramientas integradas para desarrolladores (`DevTools`), que están pensadas principalmente para que los desarrolladores prueben sus aplicaciones web. Sin embargo, como probadores de penetración web, estas herramientas pueden ser un activo vital en cualquier evaluación web que realicemos, ya que un navegador (y sus DevTools) se encuentran entre los activos que es más probable que tengamos en cada ejercicio de evaluación web. En este módulo, también discutiremos cómo utilizar algunas de las herramientas básicas de desarrollo del navegador para evaluar y monitorizar diferentes tipos de peticiones web.

Cada vez que visitamos cualquier sitio web o accedemos a cualquier aplicación web, nuestro navegador envía múltiples peticiones web y maneja múltiples respuestas HTTP para renderizar la vista final que vemos en la ventana del navegador. Para abrir las herramientas de desarrollo del navegador en Chrome o Firefox, podemos hacer clic en [`CTRL+SHIFT+I`] o simplemente hacer clic en [`F12`]. Las devtools contienen múltiples pestañas, cada una de las cuales tiene su propio uso. En este módulo nos centraremos principalmente en la pestaña `Red`, ya que es la responsable de las peticiones web.

Si hacemos clic en la pestaña Red y refrescamos la página, podremos ver la lista de peticiones enviadas por la página:  : ![devtools_network_requests](https://academy.hackthebox.com/storage/modules/35/devtools_network_requests.jpg)

Como podemos ver, las devtools nos muestran de un vistazo el estado de la respuesta (es decir, el código de respuesta), el método de solicitud utilizado (`GET`), el recurso solicitado (es decir, URL/dominio), junto con la ruta solicitada. Además, podemos utilizar `Filter URLs` para buscar una solicitud específica, en caso de que el sitio web cargue demasiadas para pasar.

**Ejercicio:** Intenta hacer clic en cualquiera de las peticiones para ver sus detalles. A continuación, puede hacer clic en la pestaña `Response` para ver el cuerpo de la respuesta, y luego haga clic en el botón `Raw` para ver el código fuente en bruto (sin renderizar) del cuerpo de la respuesta.

# HTTP Headers

Hemos visto ejemplos de peticiones HTTP y cabeceras de respuesta en la sección anterior. Estas cabeceras HTTP transmiten información entre el cliente y el servidor. Algunas cabeceras sólo se utilizan con peticiones o respuestas, mientras que otras cabeceras generales son comunes a ambas.

Las cabeceras pueden tener uno o varios valores, añadidos después del nombre de la cabecera y separados por dos puntos. Podemos dividir las cabeceras en las siguientes categorías:

1. `General Headers`
2. `Entity Headers`
3. `Request Headers`
4. `Response Headers`
5. `Security Headers`

Analicemos cada una de estas categorías.

---

## General Headers

Las [cabeceras generales](https://www.w3.org/Protocols/rfc2616/rfc2616-sec4.html) se utilizan tanto en las peticiones HTTP como en las respuestas. Son contextuales y se utilizan para `describir el mensaje más que su contenido`.

|**Header**|**Example**|**Description**|
|---|---|---|
|`Date`|`Date: Wed, 16 Feb 2022 10:38:44 GMT`|Holds the date and time at which the message originated. It's preferred to convert the time to the standard [UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) time zone.|
|`Connection`|`Connection: close`|Dictates if the current network connection should stay alive after the request finishes. Two commonly used values for this header are `close` and `keep-alive`. The `close` value from either the client or server means that they would like to terminate the connection, while the `keep-alive` header indicates that the connection should remain open to receive more data and input.|

---

## Entity Headers

Al igual que las cabeceras generales, las [cabeceras de entidad](https://www.w3.org/Protocols/rfc2616/rfc2616-sec7.html) pueden ser `comunes tanto a la solicitud como a la respuesta`. Estas cabeceras se utilizan para `describir el contenido` (entidad) transferido por un mensaje. Suelen encontrarse en las respuestas y en las peticiones POST o PUT.

|**Header**|**Example**|**Description**|
|---|---|---|
|`Content-Type`|`Content-Type: text/html`|Used to describe the type of resource being transferred. The value is automatically added by the browsers on the client-side and returned in the server response. The `charset` field denotes the encoding standard, such as [UTF-8](https://en.wikipedia.org/wiki/UTF-8).|
|`Media-Type`|`Media-Type: application/pdf`|The `media-type` is similar to `Content-Type`, and describes the data being transferred. This header can play a crucial role in making the server interpret our input. The `charset` field may also be used with this header.|
|`Boundary`|`boundary="b4e4fbd93540"`|Acts as a marker to separate content when there is more than one in the same message. For example, within a form data, this boundary gets used as `--b4e4fbd93540` to separate different parts of the form.|
|`Content-Length`|`Content-Length: 385`|Holds the size of the entity being passed. This header is necessary as the server uses it to read data from the message body, and is automatically generated by the browser and tools like cURL.|
|`Content-Encoding`|`Content-Encoding: gzip`|Data can undergo multiple transformations before being passed. For example, large amounts of data can be compressed to reduce the message size. The type of encoding being used should be specified using the `Content-Encoding` header.|

---

## Request Headers

El cliente envía [Request Headers](https://tools.ietf.org/html/rfc2616) en una transacción HTTP. Estas cabeceras se `utilizan en una petición HTTP y no están relacionadas con el contenido` del mensaje. Las siguientes cabeceras se ven comúnmente en las peticiones HTTP.

| **Header**      | **Example**                              | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| --------------- | ---------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Host`          | `Host: www.inlanefreight.com`            | Used to specify the host being queried for the resource. This can be a domain name or an IP address. HTTP servers can be configured to host different websites, which are revealed based on the hostname. This makes the host header an important enumeration target, as it can indicate the existence of other hosts on the target server.                                                                                                                          |
| `User-Agent`    | `User-Agent: curl/7.77.0`                | The `User-Agent` header is used to describe the client requesting resources. This header can reveal a lot about the client, such as the browser, its version, and the operating system.                                                                                                                                                                                                                                                                              |
| `Referer`       | `Referer: http://www.inlanefreight.com/` | Denotes where the current request is coming from. For example, clicking a link from Google search results would make `https://google.com` the referer. Trusting this header can be dangerous as it can be easily manipulated, leading to unintended consequences.                                                                                                                                                                                                    |
| `Accept`        | `Accept: */*`                            | The `Accept` header describes which media types the client can understand. It can contain multiple media types separated by commas. The `*/*` value signifies that all media types are accepted.                                                                                                                                                                                                                                                                     |
| `Cookie`        | `Cookie: PHPSESSID=b4e4fbd93540`         | Contains cookie-value pairs in the format `name=value`. A [cookie](https://en.wikipedia.org/wiki/HTTP_cookie) is a piece of data stored on the client-side and on the server, which acts as an identifier. These are passed to the server per request, thus maintaining the client's access. Cookies can also serve other purposes, such as saving user preferences or session tracking. There can be multiple cookies in a single header separated by a semi-colon. |
| `Authorization` | `Authorization: BASIC cGFzc3dvcmQK`      | Another method for the server to identify clients. After successful authentication, the server returns a token unique to the client. Unlike cookies, tokens are stored only on the client-side and retrieved by the server per request. There are multiple types of authentication types based on the webserver and application type used.                                                                                                                           |

Puede encontrar una lista completa de las cabeceras de solicitud y su uso [aquí](https://tools.ietf.org/html/rfc7231#section-5).

---

## Response Headers

Las [Response Headers](https://tools.ietf.org/html/rfc7231#section-7) pueden ser `utilizadas en una respuesta HTTP y no están relacionadas con el contenido`. Algunas cabeceras de respuesta como `Age`, `Location` y `Server` se utilizan para proporcionar más contexto sobre la respuesta. Las siguientes cabeceras se ven comúnmente en las respuestas HTTP.

|**Header**|**Example**|**Description**|
|---|---|---|
|`Server`|`Server: Apache/2.2.14 (Win32)`|Contains information about the HTTP server, which processed the request. It can be used to gain information about the server, such as its version, and enumerate it further.|
|`Set-Cookie`|`Set-Cookie: PHPSESSID=b4e4fbd93540`|Contains the cookies needed for client identification. Browsers parse the cookies and store them for future requests. This header follows the same format as the `Cookie` request header.|
|`WWW-Authenticate`|`WWW-Authenticate: BASIC realm="localhost"`|Notifies the client about the type of authentication required to access the requested resource.|

---

## Security Headers

Por último, tenemos [Security Headers](https://owasp.org/www-project-secure-headers/). Con el aumento de la variedad de navegadores y ataques basados en la web, era necesario definir ciertas cabeceras que mejoraran la seguridad. Las cabeceras de seguridad HTTP son `una clase de cabeceras de respuesta utilizadas para especificar ciertas reglas y políticas` que debe seguir el navegador al acceder al sitio web.

|**Header**|**Example**|**Description**|
|---|---|---|
|`Content-Security-Policy`|`Content-Security-Policy: script-src 'self'`|Dictates the website's policy towards externally injected resources. This could be JavaScript code as well as script resources. This header instructs the browser to accept resources only from certain trusted domains, hence preventing attacks such as [Cross-site scripting (XSS)](https://en.wikipedia.org/wiki/Cross-site_scripting).|
|`Strict-Transport-Security`|`Strict-Transport-Security: max-age=31536000`|Prevents the browser from accessing the website over the plaintext HTTP protocol, and forces all communication to be carried over the secure HTTPS protocol. This prevents attackers from sniffing web traffic and accessing protected information such as passwords or other sensitive data.|
|`Referrer-Policy`|`Referrer-Policy: origin`|Dictates whether the browser should include the value specified via the `Referer` header or not. It can help in avoiding disclosing sensitive URLs and information while browsing the website.|

**Nota:** Esta sección sólo menciona un pequeño subconjunto de las cabeceras HTTP más comunes. Existen muchas otras cabeceras contextuales que pueden utilizarse en las comunicaciones HTTP. También es posible que las aplicaciones definan cabeceras personalizadas en función de sus necesidades. Puede encontrar una lista completa de las cabeceras HTTP estándar [aquí](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers).

---

## cURL

En la sección anterior, vimos cómo el uso de la bandera `-v` con cURL nos muestra todos los detalles de la petición HTTP y la respuesta. Si sólo estuviéramos interesados en ver las cabeceras de respuesta, entonces podemos usar la bandera `-I` para enviar una petición `HEAD` y mostrar sólo las cabeceras de respuesta. Además, podemos utilizar la bandera `-i` para mostrar tanto las cabeceras como el cuerpo de la respuesta (por ejemplo, el código HTML). La diferencia entre ambas es que `-I` envía una petición `HEAD` (como veremos en la siguiente sección), mientras que `-i` envía cualquier petición que especifiquemos e imprime también las cabeceras.

El siguiente comando muestra un ejemplo del uso de la bandera `-I`:

  HTTP Headers

```shell-session
vcrdcr@htb[/htb]$ curl -I https://www.inlanefreight.com

Host: www.inlanefreight.com
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_5) AppleWebKit/605.1.15 (KHTML, like Gecko)
Cookie: cookie1=298zf09hf012fh2; cookie2=u32t4o3tb3gg4
Accept: text/plain
Referer: https://www.inlanefreight.com/
Authorization: BASIC cGFzc3dvcmQK

Date: Sun, 06 Aug 2020 08:49:37 GMT
Connection: keep-alive
Content-Length: 26012
Content-Type: text/html; charset=ISO-8859-4
Content-Encoding: gzip
Server: Apache/2.2.14 (Win32)
Set-Cookie: name1=value1,name2=value2; Expires=Wed, 09 Jun 2021 10:18:14 GMT
WWW-Authenticate: BASIC realm="localhost"
Content-Security-Policy: script-src 'self'
Strict-Transport-Security: max-age=31536000
Referrer-Policy: origin
```

**Ejercicio:** Intenta repasar todas las cabeceras anteriores, y comprueba si puedes recordar el uso de cada una de ellas.

Además de ver las cabeceras, cURL también nos permite establecer cabeceras de petición con la bandera `-H`, como veremos en una sección posterior. Algunas cabeceras, como las cabeceras `User-Agent` o `Cookie`, tienen sus propias banderas. Por ejemplo, podemos usar la `-A` para establecer nuestro `User-Agent`, como sigue:

  HTTP Headers

```shell-session
vcrdcr@htb[/htb]$ curl https://www.inlanefreight.com -A 'Mozilla/5.0'

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
...SNIP...
```

**Ejercicio:** Intenta usar las banderas `-I` o `-v` con el ejemplo anterior, para asegurarte de que hemos cambiado nuestro User-Agent con la bandera `-A`.

---

## Browser DevTools

Por último, vamos a ver cómo podemos previsualizar las cabeceras HTTP utilizando las devtools del navegador. Al igual que hicimos en la sección anterior, podemos ir a la pestaña `Red` para ver las diferentes peticiones realizadas por la página. Podemos pulsar sobre cualquiera de las peticiones para ver sus detalles: devtools_network_requests_details](https://academy.hackthebox.com/storage/modules/35/devtools_network_requests_details.jpg)

En la primera pestaña `Headers` vemos tanto las cabeceras de petición HTTP como las de respuesta HTTP. Las devtools organizan automáticamente las cabeceras en secciones, pero podemos pulsar sobre el botón `Raw` para ver sus detalles en su formato crudo. Además, podemos comprobar la pestaña `Cookies` para ver las cookies utilizadas por la solicitud, como se discutirá en una próxima sección.

# HTTP Methods and Codes

HTTP admite múltiples métodos para acceder a un recurso. En el protocolo HTTP, varios métodos de petición permiten al navegador enviar información, formularios o archivos al servidor. Estos métodos se utilizan, entre otras cosas, para indicar al servidor cómo procesar la solicitud que enviamos y cómo responder.

Hemos visto diferentes métodos HTTP utilizados en las peticiones HTTP que hemos probado en las secciones anteriores. Con cURL, si usamos `-v` para previsualizar la petición completa, la primera línea contiene el método HTTP (por ejemplo, `GET / HTTP/1.1`), mientras que con browser devtools, el método HTTP se muestra en la columna `Method`. Además, las cabeceras de respuesta también contienen el código de respuesta HTTP, que indica el estado de procesamiento de nuestra petición HTTP.

---

## Request Methods

A continuación se exponen algunos de los métodos más utilizados:

|**Method**|**Description**|
|---|---|
|`GET`|Requests a specific resource. Additional data can be passed to the server via query strings in the URL (e.g. `?param=value`).|
|`POST`|Sends data to the server. It can handle multiple types of input, such as text, PDFs, and other forms of binary data. This data is appended in the request body present after the headers. The POST method is commonly used when sending information (e.g. forms/logins) or uploading data to a website, such as images or documents.|
|`HEAD`|Requests the headers that would be returned if a GET request was made to the server. It doesn't return the request body and is usually made to check the response length before downloading resources.|
|`PUT`|Creates new resources on the server. Allowing this method without proper controls can lead to uploading malicious resources.|
|`DELETE`|Deletes an existing resource on the webserver. If not properly secured, can lead to Denial of Service (DoS) by deleting critical files on the web server.|
|`OPTIONS`|Returns information about the server, such as the methods accepted by it.|
|`PATCH`|Applies partial modifications to the resource at the specified location.|

La lista sólo destaca algunos de los métodos HTTP más utilizados. La disponibilidad de un método concreto depende tanto del servidor como de la configuración de la aplicación. Para obtener una lista completa de métodos HTTP, puede visitar este [enlace](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods).

**Nota:** La mayoría de las aplicaciones web modernas se basan principalmente en los métodos `GET` y `POST`. Sin embargo, cualquier aplicación web que utilice APIs REST también depende de `PUT` y `DELETE`, que se utilizan para actualizar y eliminar datos en el punto final de la API, respectivamente. Consulta el módulo [Introducción a las Aplicaciones Web](https://academy.hackthebox.com/module/details/75) para más detalles.

---

## Response Codes

Los códigos de estado HTTP se utilizan para indicar al cliente el estado de su solicitud. Un servidor HTTP puede devolver cinco tipos de códigos de respuesta:

|**Type**|**Description**|
|---|---|
|`1xx`|Provides information and does not affect the processing of the request.|
|`2xx`|Returned when a request succeeds.|
|`3xx`|Returned when the server redirects the client.|
|`4xx`|Signifies improper requests `from the client`. For example, requesting a resource that doesn't exist or requesting a bad format.|
|`5xx`|Returned when there is some problem `with the HTTP server` itself.|

A continuación se muestran algunos de los ejemplos más comunes de cada uno de los tipos de métodos HTTP anteriores:

|**Code**|**Description**|
|---|---|
|`200 OK`|Returned on a successful request, and the response body usually contains the requested resource.|
|`302 Found`|Redirects the client to another URL. For example, redirecting the user to their dashboard after a successful login.|
|`400 Bad Request`|Returned on encountering malformed requests such as requests with missing line terminators.|
|`403 Forbidden`|Signifies that the client doesn't have appropriate access to the resource. It can also be returned when the server detects malicious input from the user.|
|`404 Not Found`|Returned when the client requests a resource that doesn't exist on the server.|
|`500 Internal Server Error`|Returned when the server cannot process the request.|

Para obtener una lista completa de los códigos de respuesta HTTP estándar, puede visitar este [enlace](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status). Aparte de los códigos HTTP estándar, varios servidores y proveedores como [Cloudflare](https://support.cloudflare.com/hc/en-us/articles/115003014432-HTTP-Status-Codes) o [AWS](https://docs.aws.amazon.com/AmazonSimpleDB/latest/DeveloperGuide/APIError.html) implementan sus propios códigos.

# GET

Siempre que visitamos cualquier URL, nuestros navegadores realizan por defecto una petición GET para obtener los recursos remotos alojados en esa URL. Una vez que el navegador recibe la página inicial que está solicitando, puede enviar otras peticiones utilizando varios métodos HTTP. Esto se puede observar a través de la pestaña Red en las devtools del navegador, como se vio en la sección anterior.

**Ejercicio:** Escoge cualquier sitio web de tu elección, y monitoriza la pestaña Red en el navegador devtools mientras lo visitas para entender lo que la página está realizando. Esta técnica se puede utilizar para entender a fondo cómo una aplicación web interactúa con su backend, que puede ser un ejercicio esencial para cualquier evaluación de aplicaciones web o ejercicio de bug bounty.

---

## HTTP Basic Auth

Cuando visitamos el ejercicio que se encuentra al final de esta sección, nos pide que introduzcamos un nombre de usuario y una contraseña. A diferencia de los formularios de acceso habituales, que utilizan parámetros HTTP para validar las credenciales del usuario (por ejemplo, solicitud POST), este tipo de autenticación utiliza una autenticación HTTP básica, que es gestionada directamente por el servidor web para proteger una página/directorio específico, sin interactuar directamente con la aplicación web.

Para acceder a la página, tenemos que introducir un par de credenciales válidas, que en este caso son admin:admin:

   

![](https://academy.hackthebox.com/storage/modules/35/http_auth_login.jpg)

Una vez introducidas las credenciales, accederíamos a la página:

   

![](https://academy.hackthebox.com/storage/modules/35/http_auth_index.jpg)

Intentemos acceder a la página con cURL, y añadiremos `-i` para ver las cabeceras de respuesta:

  GET

```shell-session
vcrdcr@htb[/htb]$ curl -i http://<SERVER_IP>:<PORT>/
HTTP/1.1 401 Authorization Required
Date: Mon, 21 Feb 2022 13:11:46 GMT
Server: Apache/2.4.41 (Ubuntu)
Cache-Control: no-cache, must-revalidate, max-age=0
WWW-Authenticate: Basic realm="Access denied"
Content-Length: 13
Content-Type: text/html; charset=UTF-8

Access denied
```

Como podemos ver, obtenemos `Access denied` en el cuerpo de la respuesta, y también obtenemos `Basic realm=«Access denied»` en la cabecera `WWW-Authenticate`, lo que confirma que esta página efectivamente utiliza `basic HTTP auth`, como se discutió en la sección Cabeceras. Para proporcionar las credenciales a través de cURL, podemos utilizar la bandera `-u`, de la siguiente manera:

  GET

```shell-session
vcrdcr@htb[/htb]$ curl -u admin:admin http://<SERVER_IP>:<PORT>/

<!DOCTYPE html>
<html lang="en">

<head>
...SNIP...
```

Esta vez sí obtenemos la página en la respuesta. Hay otro método por el que podemos proporcionar las credenciales `basic HTTP auth`, que es directamente a través de la URL como (`username:password@URL`), tal y como comentamos en la primera sección. Si intentamos lo mismo con cURL o con nuestro navegador, también conseguiremos acceder a la página:

  GET

```shell-session
vcrdcr@htb[/htb]$ curl http://admin:admin@<SERVER_IP>:<PORT>/

<!DOCTYPE html>
<html lang="en">

<head>
...SNIP...
```

También podemos intentar visitar la misma URL en un navegador, y deberíamos obtener autenticación también.

**Ejercicio:** Intenta ver las cabeceras de respuesta añadiendo -i a la petición anterior, y comprueba en qué se diferencia una respuesta autenticada de una no autenticada.

---

## HTTP Authorization Header

Si añadimos la bandera `-v` a cualquiera de nuestros comandos cURL anteriores:

  GET

```shell-session
vcrdcr@htb[/htb]$ curl -v http://admin:admin@<SERVER_IP>:<PORT>/

*   Trying <SERVER_IP>:<PORT>...
* Connected to <SERVER_IP> (<SERVER_IP>) port PORT (#0)
* Server auth using Basic with user 'admin'
> GET / HTTP/1.1
> Host: <SERVER_IP>
> Authorization: Basic YWRtaW46YWRtaW4=
> User-Agent: curl/7.77.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Date: Mon, 21 Feb 2022 13:19:57 GMT
< Server: Apache/2.4.41 (Ubuntu)
< Cache-Control: no-store, no-cache, must-revalidate
< Expires: Thu, 19 Nov 1981 08:52:00 GMT
< Pragma: no-cache
< Vary: Accept-Encoding
< Content-Length: 1453
< Content-Type: text/html; charset=UTF-8
< 

<!DOCTYPE html>
<html lang="en">

<head>
...SNIP...
```

Como estamos usando `basic HTTP auth`, vemos que nuestra petición HTTP establece la cabecera `Authorization` en `Basic YWRtaW46YWRtaW4=`, que es el valor codificado en base64 de `admin:admin`. Si estuviéramos utilizando un método moderno de autenticación (por ejemplo, `JWT`), la `Authorization` sería del tipo `Bearer` y contendría un token encriptado más largo.

Intentemos establecer manualmente la `Authorization`, sin proporcionar las credenciales, para ver si nos permite acceder a la página. Podemos establecer la cabecera con la bandera `-H`, y usaremos el mismo valor de la petición HTTP anterior. Podemos añadir la bandera `-H` varias veces para especificar múltiples cabeceras:

  GET

```shell-session
vcrdcr@htb[/htb]$ curl -H 'Authorization: Basic YWRtaW46YWRtaW4=' http://<SERVER_IP>:<PORT>/

<!DOCTYPE html
<html lang="en">

<head>
...SNIP...
```

Como vemos, esto también nos dio acceso a la página. Estos son algunos de los métodos que podemos utilizar para autenticarnos en la página. La mayoría de las aplicaciones web modernas utilizan formularios de acceso construidos con el lenguaje de scripting back-end (por ejemplo, PHP), que utilizan peticiones HTTP POST para autenticar a los usuarios y luego devuelven una cookie para mantener su autenticación.

---

## GET Parameters

Una vez autentificados, accedemos a la función «Búsqueda de ciudades», en la que podemos introducir un término de búsqueda y obtener una lista de ciudades coincidentes:

   

![](https://academy.hackthebox.com/storage/modules/35/http_auth_index.jpg)

Como la página devuelve nuestros resultados, puede estar contactando con un recurso remoto para obtener la información, y luego mostrarla en la página. Para verificar esto, podemos abrir el navegador devtools e ir a la pestaña Red, o usar el atajo [`CTRL+SHIFT+E`] para llegar a la misma pestaña. Antes de introducir nuestro término de búsqueda y ver las peticiones, es posible que tengamos que hacer clic en el icono `trash` de la parte superior izquierda, para asegurarnos de que borramos cualquier petición anterior y sólo monitorizamos las peticiones más recientes: ![network_clear_requests](https://academy.hackthebox.com/storage/modules/35/network_clear_requests.jpg)

Después de esto, podemos introducir cualquier término de búsqueda y pulsar enter, e inmediatamente notaremos que se envía una nueva petición al backend: ![network_clear_requests](https://academy.hackthebox.com/storage/modules/35/web_requests_get_search.jpg)

Cuando hacemos clic en la petición, se envía a `search.php` con el parámetro GET `search=le` utilizado en la URL. Esto nos ayuda a entender que la función de búsqueda solicita otra página para los resultados.

Ahora, podemos enviar la misma petición directamente a `search.php` para obtener los resultados completos de la búsqueda, aunque probablemente los devolverá en un formato específico (por ejemplo, JSON) sin tener el diseño HTML que se muestra en la captura de pantalla anterior.

Para enviar una petición GET con cURL, podemos utilizar exactamente la misma URL vista en las capturas de pantalla anteriores, ya que las peticiones GET colocan sus parámetros en la URL. Sin embargo, las devtools del navegador proporcionan un método más conveniente para obtener el comando cURL. Podemos hacer clic con el botón derecho en la petición y seleccionar `Copiar>Copiar como cURL`. Entonces, podemos pegar el comando copiado en nuestro terminal y ejecutarlo, y deberíamos obtener exactamente la misma respuesta:

  GET

```shell-session
vcrdcr@htb[/htb]$ curl 'http://<SERVER_IP>:<PORT>/search.php?search=le' -H 'Authorization: Basic YWRtaW46YWRtaW4='

Leeds (UK)
Leicester (UK)
```

**Nota:** El comando copiado contendrá todas las cabeceras utilizadas en la petición HTTP. Sin embargo, podemos eliminar la mayoría de ellas y sólo mantener las cabeceras de autenticación necesarias, como la cabecera `Authorization`.

También podemos repetir la petición exacta dentro de las devtools del navegador, seleccionando `Copy>Copy as Fetch`. Esto copiará la misma petición HTTP usando la librería JavaScript Fetch. Entonces, podemos ir a la consola de JavaScript pulsando [`CTRL+SHIFT+K`], pegar nuestro comando Fetch y pulsar enter para enviar la petición: ![network_clear_requests](https://academy.hackthebox.com/storage/modules/35/web_requests_fetch_search.jpg)

Como vemos, el navegador envió nuestra petición, y podemos ver la respuesta devuelta tras ella. Podemos hacer clic en la respuesta para ver sus detalles, expandir varios detalles y leerlos.

# POST

En la sección anterior, vimos cómo las peticiones `GET` pueden ser utilizadas por las aplicaciones web para funcionalidades como la búsqueda y el acceso a páginas. Sin embargo, cuando las aplicaciones web necesitan transferir archivos o mover los parámetros de usuario de la URL, utilizan peticiones `POST`.

A diferencia de HTTP `GET`, que coloca los parámetros de usuario dentro de la URL, HTTP `POST` coloca los parámetros de usuario dentro del cuerpo de la petición HTTP. Esto tiene tres ventajas principales:

- Falta de registro: Como las peticiones POST pueden transferir archivos grandes (por ejemplo, carga de archivos), no sería eficiente para el servidor registrar todos los archivos cargados como parte de la URL solicitada, como sería el caso con un archivo cargado a través de una petición GET.
- Menos requisitos de codificación Las URL están diseñadas para ser compartidas, lo que significa que deben ajustarse a caracteres que puedan convertirse en letras. La petición POST coloca datos en el cuerpo que puede aceptar datos binarios. Los únicos caracteres que necesitan ser codificados son los que se utilizan para separar parámetros.
- `Se pueden enviar más datos`: La longitud máxima de una URL varía entre navegadores (Chrome/Firefox/IE), servidores web (IIS, Apache, nginx), redes de distribución de contenidos (Fastly, Cloudfront, Cloudflare) e incluso acortadores de URL (bit.ly, amzn.to). En general, la longitud de una URL debe mantenerse por debajo de los 2.000 caracteres, por lo que no pueden manejar una gran cantidad de datos.

Veamos algunos ejemplos de cómo funcionan las peticiones POST, y cómo podemos utilizar herramientas como cURL o browser devtools para leer y enviar peticiones POST.

---

## Login Forms

El ejercicio al final de esta sección es similar al ejemplo que vimos en la sección GET. Sin embargo, una vez que visitamos la aplicación web, vemos que utiliza un formulario de inicio de sesión PHP en lugar de autenticación básica HTTP:

   

![](https://academy.hackthebox.com/storage/modules/35/web_requests_post_login.jpg)

Si intentamos acceder con `admin`:`admin`, entramos y vemos una función de búsqueda similar a la que vimos anteriormente en la sección GET:

   

![](https://academy.hackthebox.com/storage/modules/35/web_requests_login_search.jpg)

Si borramos la pestaña Red de nuestro navegador devtools e intentamos entrar de nuevo, veremos que se están enviando muchas peticiones. Podemos filtrar las peticiones por la IP de nuestro servidor, de forma que sólo se muestren las peticiones que van al servidor web de la aplicación web (es decir, filtrar las peticiones externas), y nos daremos cuenta de que se está enviando la siguiente petición POST: ![web_requests_login_request](https://academy.hackthebox.com/storage/modules/35/web_requests_login_request.jpg)

Podemos hacer clic en la petición, hacer clic en la pestaña `Request` (que muestra el cuerpo de la petición), y luego hacer clic en el botón `Raw` para mostrar los datos sin procesar de la petición. Vemos que los siguientes datos están siendo enviados como datos de la petición POST:

Código: bash

```bash
usuario=admin&contraseña=admin
```

Con los datos de la petición a mano, podemos intentar enviar una petición similar con cURL, para ver si esto nos permitiría iniciar sesión también. Además, como hicimos en la sección anterior, podemos simplemente hacer clic con el botón derecho sobre la petición y seleccionar `Copiar>Copiar como cURL`. Sin embargo, es importante poder crear peticiones POST manualmente, así que vamos a intentarlo.

Usaremos la bandera `-X POST` para enviar una petición `POST`. Entonces, para añadir nuestros datos POST, podemos utilizar la bandera `-d` y añadir los datos anteriores después de ella, de la siguiente manera:

  POST

```shell-session
vcrdcr@htb[/htb]$ curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/

...SNIP...
        <em>Type a city name and hit <strong>Enter</strong></em>
...SNIP...
```

Si examinamos el código HTML, no veremos el código del formulario de inicio de sesión, pero sí el código de la función de búsqueda, lo que indica que efectivamente nos hemos autenticado.

**Consejo:** Muchos formularios de login nos redirigirían a una página diferente una vez 

---

## Authenticated Cookies

Si nos hemos autenticado correctamente, deberíamos haber recibido una cookie para que nuestros navegadores puedan persistir nuestra autenticación, y no tengamos que iniciar sesión cada vez que visitemos la página. Podemos usar las banderas `-v` o `-i` para ver la respuesta, que debería contener la cabecera `Set-Cookie` con nuestra cookie autenticada:

  POST

```shell-session
vcrdcr@htb[/htb]$ curl -X POST -d 'username=admin&password=admin' http://<SERVER_IP>:<PORT>/ -i

HTTP/1.1 200 OK
Date: 
Server: Apache/2.4.41 (Ubuntu)
Set-Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1; path=/

...SNIP...
        <em>Type a city name and hit <strong>Enter</strong></em>
...SNIP...
```

Con nuestra cookie autenticada, ahora deberíamos ser capaces de interactuar con la aplicación web sin necesidad de proporcionar nuestras credenciales cada vez. Para probar esto, podemos establecer la cookie anterior con la bandera `-b` en cURL, de la siguiente manera:

  POST

```shell-session
vcrdcr@htb[/htb]$ curl -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/

...SNIP...
        <em>Type a city name and hit <strong>Enter</strong></em>
...SNIP...
```

Como podemos ver, efectivamente fuimos autenticados y llegamos a la función de búsqueda. También es posible especificar la cookie como una cabecera, de la siguiente manera:

Code: bash

```bash
curl -H 'Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' http://<SERVER_IP>:<PORT>/
```

También podemos intentar lo mismo con nuestros navegadores. Primero cerremos la sesión, y luego deberíamos volver a la página de inicio de sesión. Entonces, podemos ir a la pestaña `Storage` en las devtools con [`SHIFT+F9`]. En la pestaña `Storage`, podemos hacer clic en `Cookies` en el panel izquierdo y seleccionar nuestro sitio web para ver nuestras cookies actuales. Puede que tengamos o no cookies existentes, pero si hemos cerrado sesión, entonces nuestra cookie PHP no debería estar autenticada, razón por la cual obtenemos el formulario de inicio de sesión y no la función de búsqueda: ![web_requests_cookies](https://academy.hackthebox.com/storage/modules/35/web_requests_cookies.jpg)

Ahora, intentemos usar nuestra cookie autenticada anterior, y veamos si entramos sin necesidad de proporcionar nuestras credenciales. Para ello, podemos simplemente reemplazar el valor de la cookie por el nuestro. Si no, podemos hacer clic con el botón derecho en la cookie y seleccionar «Borrar todo», y después hacer clic en el icono «+» para añadir una nueva cookie. Después de eso, tenemos que introducir el nombre de la cookie, que es la parte antes de la `=` (`PHPSESSID`), y luego el valor de la cookie, que es la parte después de la `=` (`c1nsa6op7vtk7kdis7bcnbadf1`). Entonces, una vez que nuestra cookie está establecida, podemos refrescar la página, y veremos que efectivamente nos autenticamos sin necesidad de hacer login, simplemente usando una cookie autenticada: ![web_requests_auth_cookie](https://academy.hackthebox.com/storage/modules/35/web_requests_auth_cookie.jpg)

Como podemos ver, tener una cookie válida puede ser suficiente para autenticarse en muchas aplicaciones web. Esto puede ser una parte esencial de algunas

---

## JSON Data

Por último, vamos a ver qué peticiones se envían cuando interactuamos con la función `Buscar ciudad`. Para ello, iremos a la pestaña Red de las herramientas de desarrollo del navegador y haremos clic en el icono de la papelera para borrar todas las peticiones. A continuación, podemos realizar cualquier consulta de búsqueda para ver qué peticiones se envían: ![web_requests_search_request](https://academy.hackthebox.com/storage/modules/35/web_requests_search_request.jpg)

Como podemos ver, el formulario de búsqueda envía una petición POST a `search.php`, con los siguientes datos:

Código: json

```json
{«search»: «london»}
```

Código: bash

```bash
POST /buscar.php HTTP/1.1
Host: servidor_ip
Usuario-Agente: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:97.0) Gecko/20100101 Firefox/97.0
Aceptar: */*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referencia: http://server_ip/index.php
Tipo de contenido: application/json
Origen: http://server_ip
Contenido-Longitud: 19
DNT: 1
Conexión: keep-alive
Cookie: PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1
```

Efectivamente, tenemos `Content-Type: application/json`. Intentemos replicar esta petición como hicimos antes, pero incluyendo tanto la cookie como las cabeceras de tipo de contenido, y enviando nuestra petición a `search.php`:

  POST

```shell-session
vcrdcr@htb[/htb]$ curl -X POST -d '{«search»: «london»}'' -b 'PHPSESSID=c1nsa6op7vtk7kdis7bcnbadf1' -H 'Content-Type: application/json' http://<SERVER_IP>:<PORT>/search.php
[«London (UK)»]
```

Como podemos ver, hemos sido capaces de interactuar con la función de búsqueda directamente sin necesidad de iniciar sesión o interactuar con el front-end de la aplicación web. Esto puede ser una habilidad esencial cuando se realizan evaluaciones de aplicaciones web o ejercicios de bug bounty, ya que es mucho más rápido probar aplicaciones web de esta manera.

**Ejercicio:** Intenta repetir la petición anterior sin añadir la cookie o las cabeceras content-type, y observa cómo la aplicación web actuaría de forma diferente.

Por último, vamos a intentar repetir la misma petición usando `Fetch`, como hicimos en la sección anterior. Podemos hacer click con el botón derecho sobre la petición y seleccionar `Copy>Copy as Fetch`, y luego ir a la pestaña `Console` y ejecutar nuestro código allí: ![web_requests_fetch_post](https://academy.hackthebox.com/storage/modules/35/web_requests_fetch_post.jpg)

Nuestra petición devuelve con éxito los mismos datos que obtuvimos con cURL. Prueba a buscar diferentes ciudades interactuando directamente con el search.php a través de Fetch o cURL.



# CRUD API

En las secciones anteriores vimos ejemplos de una aplicación web `Búsqueda de Ciudad` que utiliza parámetros PHP para buscar el nombre de una ciudad. En esta sección veremos cómo una aplicación web de este tipo puede utilizar APIs para realizar lo mismo, e interactuaremos directamente con el punto final de la API.

---

## APIs

Hay varios tipos de APIs. Muchas APIs se utilizan para interactuar con una base de datos, de tal manera que podríamos especificar la tabla solicitada y la fila solicitada dentro de nuestra consulta API, y luego utilizar un método HTTP para realizar la operación necesaria. Por ejemplo, para el endpoint `api.php` de nuestro ejemplo, si quisiéramos actualizar la tabla `city` de la base de datos, y la fila que vamos a actualizar tiene el nombre de ciudad `london`, entonces la URL sería algo como esto:

Code: bash

```bash
curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london ...SNIP...
```

## CRUD

Como podemos ver, podemos especificar fácilmente la tabla y la fila sobre la que queremos realizar una operación a través de tales APIs. Luego podemos utilizar diferentes métodos HTTP para realizar diferentes operaciones sobre esa fila. En general, las API realizan 4 operaciones principales sobre la entidad de base de datos solicitada:

|Operation|HTTP Method|Description|
|---|---|---|
|`Create`|`POST`|Adds the specified data to the database table|
|`Read`|`GET`|Reads the specified entity from the database table|
|`Update`|`PUT`|Updates the data of the specified database table|
|`Delete`|`DELETE`|Removes the specified row from the database table|

Estas cuatro operaciones están vinculadas principalmente a las APIs CRUD comúnmente conocidas, pero el mismo principio se utiliza también en las APIs REST y en varios otros tipos de APIs. Por supuesto, no todas las APIs funcionan de la misma manera, y el control de acceso del usuario limitará qué acciones podemos realizar y qué resultados podemos ver. El módulo [Introducción a las Aplicaciones Web](https://academy.hackthebox.com/module/details/75) explica con más detalle estos conceptos, por lo que puedes consultarlo para obtener más información sobre las APIs y su uso.

---

## Read

Lo primero que haremos al interactuar con una API es leer datos. Como mencionamos anteriormente, podemos simplemente especificar el nombre de la tabla después de la API (por ejemplo `/city`) y luego especificar nuestro término de búsqueda (por ejemplo `/london`), de la siguiente manera:

  CRUD API

```shell-session
vcrdcr@htb[/htb]$ curl http://<SERVER_IP>:<PORT>/api.php/city/london

[{"city_name":"London","country_name":"(UK)"}]
```

Vemos que el resultado se envía como una cadena JSON. Para tenerlo correctamente formateado en formato JSON, podemos canalizar la salida a la utilidad `jq`, que le dará el formato adecuado. También silenciaremos cualquier salida cURL innecesaria con `-s`, como sigue:

  CRUD API

```shell-session
vcrdcr@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/london | jq

[
  {
    "city_name": "London",
    "country_name": "(UK)"
  }
]
```

Como podemos ver, obtenemos la salida en un formato agradable. También podemos proporcionar un término de búsqueda y obtener todos los resultados coincidentes:

  CRUD API

```shell-session
vcrdcr@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/le | jq

[
  {
    "city_name": "Leeds",
    "country_name": "(UK)"
  },
  {
    "city_name": "Dudley",
    "country_name": "(UK)"
  },
  {
    "city_name": "Leicester",
    "country_name": "(UK)"
  },
  ...SNIP...
]
```

Por último, podemos pasar una cadena vacía para recuperar todas las entradas de la tabla:

  CRUD API

```shell-session
vcrdcr@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/ | jq

[
  {
    "city_name": "London",
    "country_name": "(UK)"
  },
  {
    "city_name": "Birmingham",
    "country_name": "(UK)"
  },
  {
    "city_name": "Leeds",
    "country_name": "(UK)"
  },
  ...SNIP...
]
```

`Pruebe a visitar cualquiera de los enlaces anteriores utilizando su navegador, para ver cómo se muestra el resultado.`

---

## Create

Para añadir una nueva entrada, podemos utilizar una petición HTTP POST, que es bastante similar a la que hemos realizado en la sección anterior. Podemos simplemente POST nuestros datos JSON, y se añadirá a la tabla. Como esta API utiliza datos JSON, también estableceremos la cabecera `Content-Type` a JSON, como sigue:

  CRUD API

```shell-session
vcrdcr@htb[/htb]$ curl -X POST http://<SERVER_IP>:<PORT>/api.php/city/ -d '{"city_name":"HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'
```

Ahora, podemos leer el contenido de la ciudad que hemos añadido (`HTB_City`), para ver si se ha añadido correctamente:

  CRUD API

```shell-session
vcrdcr@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/HTB_City | jq

[
  {
    "city_name": "HTB_City",
    "country_name": "HTB"
  }
]
```

Como podemos ver, se ha creado una nueva ciudad, que antes no existía.

**Ejercicio:** Intenta añadir una nueva ciudad a través del navegador devtools, utilizando una de las peticiones Fetch POST que utilizaste en la sección anterior.

---

## UPDATE

Ahora que sabemos cómo leer y escribir entradas a través de APIs, vamos a empezar a discutir otros dos métodos HTTP que no hemos utilizado hasta ahora: `PUT` y `DELETE`. Como se mencionó al principio de la sección, `PUT` se utiliza para actualizar las entradas de la API y modificar sus detalles, mientras que `DELETE` se utiliza para eliminar una entidad específica.

**Nota:** El método HTTP `PATCH` también puede utilizarse para actualizar entradas de la API en lugar de `PUT`. Para ser precisos, `PATCH` se utiliza para actualizar parcialmente una entrada (sólo modifica algunos de sus datos «por ejemplo, sólo nombre_ciudad»), mientras que `PUT` se utiliza para actualizar toda la entrada. También podemos utilizar el método HTTP `OPTIONS` para ver cuál de los dos es aceptado por el servidor, y luego utilizar el método apropiado en consecuencia. En esta sección, nos centraremos en el método `PUT`, aunque su uso es bastante similar.

Usar `PUT` es bastante similar a `POST` en este caso, con la única diferencia de que tenemos que especificar el nombre de la entidad que queremos editar en la URL, de lo contrario la API no sabrá qué entidad editar. Por lo tanto, todo lo que tenemos que hacer es especificar el nombre `city` en la URL, cambiar el método de solicitud a `PUT`, y proporcionar los datos JSON como lo hicimos con POST, de la siguiente manera:

  CRUD API

```shell-session
vcrdcr@htb[/htb]$ curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london -d '{"city_name":"New_HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'
```

Vemos en el ejemplo anterior que primero especificamos `/city/london` como nuestra ciudad, y pasamos una cadena JSON que contenía `«city_name»: «New_HTB_City»` en los datos de la petición. Así, la ciudad de Londres ya no debería existir, y debería existir una nueva ciudad con el nombre `Nueva_ciudad_HTB`. Intentemos leer ambas para confirmarlo:


  CRUD API

```shell-session
vcrdcr@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City | jq

[
  {
    "city_name": "New_HTB_City",
    "country_name": "HTB"
  }
]
```

Efectivamente, hemos sustituido con éxito el nombre de la ciudad antigua por el de la ciudad nueva.

**Nota:** En algunas APIs, la operación `Update` también se puede utilizar para crear nuevas entradas. Básicamente, enviaríamos nuestros datos, y si no existen, los crearía. Por ejemplo, en el ejemplo anterior, aunque no existiera una entrada con la ciudad de `londres`, crearía una nueva entrada con los datos que le pasáramos. En nuestro ejemplo, sin embargo, este no es el caso. Intente actualizar una ciudad inexistente y vea lo que obtendría.

---

## DELETE

Por último, vamos a intentar borrar una ciudad, que es tan fácil como leer una ciudad. Simplemente especificamos el nombre de la ciudad para la API y usamos el método HTTP `DELETE`, y borraría la entrada, como sigue:

  CRUD API

```shell-session
vcrdcr@htb[/htb]$ curl -X DELETE http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City
```

  CRUD API

```shell-session
vcrdcr@htb[/htb]$ curl -s http://<SERVER_IP>:<PORT>/api.php/city/New_HTB_City | jq
[]
```

Como podemos ver, después de borrar `New_HTB_City`, obtenemos un array vacío cuando intentamos leerlo, lo que significa que ya no existe.

**Ejercicio:** Intenta borrar cualquiera de las ciudades que has añadido antes mediante peticiones POST, y luego lee todas las entradas para confirmar que se han borrado correctamente.

Con esto, somos capaces de realizar las 4 operaciones `CRUD` a través de cURL. En una aplicación web real, estas acciones pueden no estar permitidas para todos los usuarios, o se consideraría una vulnerabilidad si cualquiera puede modificar o borrar cualquier entrada. Cada usuario tendría ciertos privilegios sobre lo que puede `leer` o `escribir`, donde `escribir` se refiere a añadir, modificar o borrar datos. Para autenticar a nuestro usuario para usar la API, necesitaríamos pasar una cookie o una cabecera de autorización (por ejemplo, JWT), como hicimos en una sección anterior. Aparte de eso, las operaciones son similares a lo que practicamos en esta sección.

94.237.61.52:33589
curl -X PUT http://<SERVER_IP>:<PORT>/api.php/city/london -d '{"city_name":"New_HTB_City", "country_name":"HTB"}' -H 'Content-Type: application/json'
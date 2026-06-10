---
tags:
  - HTB-Academy
  - Penetration-Tester
---


# Introducción a los Proxies Web

---

Hoy en día, la mayoría de las aplicaciones web y móviles modernas funcionan conectándose continuamente a servidores de back-end para enviar y recibir datos y luego procesar estos datos en el dispositivo del usuario, como sus navegadores web o teléfonos móviles. Dado que la mayoría de las aplicaciones dependen en gran medida de los servidores de back-end para procesar datos, probar y proteger los servidores de back-end es cada vez más importante.

Las solicitudes web de prueba a servidores de back-end constituyen la mayor parte de las Pruebas de Penetración de Aplicaciones Web, que incluyen conceptos que se aplican tanto a aplicaciones web como móviles. Para capturar las solicitudes y el tráfico que pasa entre las aplicaciones y los servidores de back-end y manipular este tipo de solicitudes con fines de prueba, debemos usar `Web Proxies`.

---

## ¿Qué son los Proxies Web?

Los proxies web son herramientas especializadas que se pueden configurar entre un navegador/aplicación móvil y un servidor de back-end para capturar y ver todas las solicitudes web que se envían entre ambos extremos, actuando esencialmente como herramientas man-in-the-middle (MITM). Mientras que otros `Network Sniffing` las aplicaciones, como Wireshark, operan analizando todo el tráfico local para ver qué pasa a través de una red, los Proxies web funcionan principalmente con puertos web como, entre otros `HTTP/80` y `HTTPS/443`.

Los proxies web se consideran entre las herramientas más esenciales para cualquier pentester web. Simplifican significativamente el proceso de captura y reproducción de solicitudes web en comparación con herramientas anteriores basadas en CLI. Una vez que se configura un proxy web, podemos ver todas las solicitudes HTTP realizadas por una aplicación y todas las respuestas enviadas por el servidor de back-end. Además, podemos interceptar una solicitud específica para modificar sus datos y ver cómo los maneja el servidor de back-end, que es una parte esencial de cualquier prueba de penetración web.

---

## Usos de Proxies Web

Si bien el uso principal de los proxies web es capturar y reproducir solicitudes HTTP, tienen muchas otras características que permiten diferentes usos para los proxies web. La siguiente lista muestra algunas de las otras tareas para las que podemos usar proxies web:

- Escaneo de vulnerabilidades de aplicaciones web
- Fuzzing web
- Rastreo web
- Mapeo de aplicaciones web
- Análisis de solicitudes web
- Web configuration testing
- Code reviews

In this module, we will not discuss any specific web attacks, as other HTB Academy web modules cover various web attacks. However, we will thoroughly cover how to use web proxies and their various features and mention which type of web attacks require which feature. We will be covering the two most common web proxy tools: `Burp Suite` and `ZAP`.

---

## Burp Suite

[Burp Suite (Burp)](https://portswigger.net/burp) -pronounced Burp Sweet- is the most common web proxy for web penetration testing. It has an excellent user interface for its various features and even provides a built-in Chromium browser to test web applications. Certain Burp features are only available in the commercial version `Burp Pro/Enterprise`, but even the free version is an extremely powerful testing tool to keep in our arsenal.

Algunos de los `paid-only` las características son:

- Escáner de aplicaciones web activo
- Intruso de Rápida Erupción
- La capacidad de cargar ciertas extensiones de Burp

La comunidad `free` la versión de Burp Suite debería ser suficiente para la mayoría de los probadores de penetración. Una vez que comenzamos las pruebas de penetración de aplicaciones web más avanzadas, el `pro` las características pueden ser útiles. La mayoría de las características que cubriremos en este módulo están disponibles en la comunidad `free` versión de Burp Suite, pero también tocaremos algunos de los `pro` características, como el Active Web App Scanner.

**Consejo:** Si tiene una dirección de correo electrónico educativa o comercial, puede solicitar una prueba gratuita de Burp Pro en esto [enlace](https://portswigger.net/burp/pro/trial) para poder seguir junto con algunas de las características de Burp Pro solo se muestran más adelante en este módulo.

---

## Proxy de ataque Zed OWASP (ZAP)

[Proxy de ataque Zed OWASP (ZAP)](https://www.zaproxy.org/) es otra herramienta común de proxy web para pruebas de penetración web. ZAP es un proyecto gratuito y de código abierto iniciado por el [Proyecto de Seguridad de Aplicaciones Web Abiertas (OWASP)](https://owasp.org/) y mantenido por la comunidad, por lo que no tiene características de pago como Burp. Ha crecido significativamente en los últimos años y está ganando rápidamente el reconocimiento del mercado como la herramienta líder de proxy web de código abierto.

Al igual que Burp, ZAP proporciona varias características básicas y avanzadas que se pueden utilizar para pentesting web. ZAP también tiene ciertas fortalezas sobre Burp, que cubriremos a lo largo de este módulo. La principal ventaja de ZAP sobre Burp es ser un proyecto gratuito de código abierto, lo que significa que no enfrentaremos ninguna limitación o limitación en nuestros escaneos que solo se levantan con una suscripción de pago. Además, con una creciente comunidad de contribuyentes, ZAP está ganando muchas de las características de Burp de pago solo de forma gratuita.

Al final, aprender ambas herramientas puede ser bastante similar y nos proporcionará opciones para cada situación a través de un pentest web, y podemos optar por utilizar cualquiera que encontremos más adecuado para nuestras necesidades. En algunos casos, es posible que no veamos suficiente valor para justificar una suscripción de pago a Burp, y podemos cambiar a ZAP para tener una experiencia completamente abierta y gratuita. En otras situaciones en las que queremos una solución más madura para pentests avanzados o pentesting corporativo, podemos encontrar que el valor proporcionado por Burp Pro está justificado y podemos cambiar a Burp para estas características.

# Configuración

---

Tanto Burp como ZAP están disponibles para Windows, macOS y cualquier distribución de Linux. Ambos ya están instalados en su instancia PwnBox y se puede acceder desde el muelle inferior o el menú de la barra superior. Ambas herramientas están preinstaladas en distribuciones comunes de Penetration Testing Linux como Parrot o Kali. Cubriremos el proceso de instalación y configuración de Burp y Zap en esta sección, lo que será útil si queremos instalar las herramientas en nuestra propia VM.

---

## Suite Burp

Si Burp no está preinstalado en nuestra VM, podemos comenzar descargándolo desde [Página de descarga de Burp](https://portswigger.net/burp/releases/). Una vez descargado, podemos ejecutar el instalador y seguir las instrucciones, que varían de un sistema operativo a otro, pero deberían ser bastante sencillas. Hay instaladores para Windows, Linux y macOS.

Una vez instalado, Burp se puede iniciar desde el terminal escribiendo `burpsuite`, o desde el menú de la aplicación como se mencionó anteriormente. Otra opción es descargar el `JAR` archivo (que se puede utilizar en todos los sistemas operativos con un Java Runtime Environment (JRE) instalado) desde la página de descargas anterior. Podemos ejecutarlo con la siguiente línea de comandos o haciendo doble clic en ella:

  Configuración

```shell-session
vcrdcr@htb[/htb]$ java -jar </path/to/burpsuite.jar>
```

Nota: Tanto Burp como ZAP confían en Java Runtime Environment para ejecutarse, pero este paquete debe incluirse en el instalador de forma predeterminada. Si no, podemos seguir las instrucciones que se encuentran en esto [página](https://docs.oracle.com/goldengate/1212/gg-winux/GDRAD/java.htm).

Una vez que iniciamos Burp, se nos pide que creemos un nuevo proyecto. Si estamos ejecutando la versión de la comunidad, solo podríamos usar proyectos temporales sin la capacidad de salvar nuestro progreso y continuar más adelante:

![Proyecto Comunitario Burp](https://academy.hackthebox.com/storage/modules/110/burp_project_community.jpg)

Si estamos utilizando la versión pro/empresa, tendremos la opción de iniciar un nuevo proyecto o abrir un proyecto existente.

![Proyecto Burp Pro](https://academy.hackthebox.com/storage/modules/110/burp_project_prof.jpg)

Es posible que tengamos que guardar nuestro progreso si estuviéramos probando enormes aplicaciones web o ejecutando un `Active Web Scan`. Sin embargo, es posible que no necesitemos salvar nuestro progreso y, en muchos casos, podemos comenzar un `temporary` proyecto cada vez.

Entonces, seleccionemos `temporary project`, y haga clic en continuar. Una vez que lo hagamos, se nos pedirá que utilicemos `Burp Default Configurations`, o a `Load a Configuration File`y elegiremos la primera opción:

![Burp Proyecto Config](https://academy.hackthebox.com/storage/modules/110/burp_project_config.jpg)

Una vez que comencemos a utilizar en gran medida las características de Burp, es posible que deseemos personalizar nuestras configuraciones y cargarlas al iniciar Burp. Por ahora, podemos mantener `Use Burp Defaults`, y `Start Burp`. Una vez hecho todo esto, deberíamos estar listos para comenzar a usar Burp.

---

## ZAP

Podemos descargar ZAP desde su [descargar página](https://www.zaproxy.org/download/), elija el instalador que se ajuste a nuestro sistema operativo y siga las instrucciones básicas de instalación para instalarlo. ZAP también se puede descargar como un archivo JAR multiplataforma y lanzarse con el `java -jar` comando o haciendo doble clic en él, de manera similar a Burp.

Para empezar con ZAP, podemos lanzarlo desde el terminal con el `zaproxy` coméntalo o accede a él desde el menú de la aplicación como Burp. Una vez que ZAP se inicie, a diferencia de la versión gratuita de Burp, se nos pedirá que creemos un nuevo proyecto o un proyecto temporal. Usemos un proyecto temporal eligiendo `no`, ya que no vamos a estar trabajando en un gran proyecto que tendremos que persistir durante varios días:

![ZAP Nuevo Config](https://academy.hackthebox.com/storage/modules/110/zap_new_project.jpg)

Después de eso, tendremos ZAP en ejecución, y podemos continuar el proceso de configuración de proxy, como discutiremos en la siguiente sección.

Consejo: Si prefiere usar un tema oscuro, puede hacerlo en Burp yendo a (`User Options>Display`) y seleccionando "oscuro" en (`theme`), y en ZAP yendo a (`Tools>Options>Display`) y seleccionando "Flat Dark" en (`Look and Feel`).


# Configuración de Proxy

---

Ahora que hemos instalado y comenzado ambas herramientas, aprenderemos a usar la función más utilizada; `Web Proxy`.

Podemos configurar estas herramientas como un proxy para cualquier aplicación, de modo que todas las solicitudes web se enruten a través de ellas para que podamos examinar manualmente qué solicitudes web envía y recibe una aplicación. Esto nos permitirá entender mejor lo que la aplicación está haciendo en segundo plano y nos permite interceptar y cambiar estas solicitudes o reutilizarlas con varios cambios para ver cómo responde la aplicación.

---

## Navegador Pre-Configurado

Para usar las herramientas como proxies web, debemos configurar la configuración del proxy de nuestro navegador para usarlas como proxy o usar el navegador preconfigurado. Ambas herramientas tienen un navegador preconfigurado que viene con configuraciones de proxy preconfiguradas y los certificados CA preinstalados, lo que hace que iniciar una prueba de penetración web sea muy rápido y fácil.

En Burp's (`Proxy>Intercept`), podemos hacer clic en `Open Browser`, que abrirá el navegador preconfigurado de Burp, y enrutará automáticamente todo el tráfico web a través de Burp: ![Burp Navegador Preconfigurado](https://academy.hackthebox.com/storage/modules/110/burp_preconfigured_browser.jpg)

En ZAP, podemos hacer clic en el icono del navegador Firefox al final de la barra superior, y se abrirá el navegador preconfigurado:

![Navegador Preconfigurado ZAP](https://academy.hackthebox.com/storage/modules/110/zap_preconfigured_browser.jpg)

Para nuestros usos en este módulo, usar el navegador preconfigurado debería ser suficiente.

---

## Configuración de Proxy

En muchos casos, es posible que deseemos usar un navegador real para pentesting, como Firefox. Para usar Firefox con nuestras herramientas de proxy web, primero debemos configurarlo para usarlas como proxy. Podemos ir manualmente a las preferencias de Firefox y configurar el proxy para usar el puerto de escucha del proxy web. Tanto Burp como ZAP usan puerto `8080` por defecto, pero podemos utilizar cualquier puerto disponible. Si elegimos un puerto que está en uso, el proxy no se iniciará y recibiremos un mensaje de error.

**Nota:** En caso de que quisiéramos servir el proxy web en un puerto diferente, podemos hacerlo en Burp under (`Proxy>Options`), o en ZAP bajo (`Tools>Options>Local Proxies`). En ambos casos, debemos asegurarnos de que el proxy configurado en Firefox utilice el mismo puerto.

En lugar de cambiar manualmente el proxy, podemos utilizar la extensión de Firefox [Proxy Foxy](https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-standard/) para cambiar fácil y rápidamente el proxy de Firefox. Esta extensión está preinstalada en su instancia de PwnBox y se puede instalar en su propio navegador Firefox visitando el [Página de extensiones de Firefox](https://addons.mozilla.org/en-US/firefox/addon/foxyproxy-standard/) y haciendo clic `Add to Firefox` para instalarlo.

Una vez que tengamos la extensión agregada, podemos configurar el proxy web en él haciendo clic en su icono en la barra superior de Firefox y luego eligiendo `options`:

![Opciones de Foxyproxy](https://academy.hackthebox.com/storage/modules/110/foxyproxy_options.jpg)

Una vez que estemos en el `options` página, podemos hacer clic en `add` en el panel izquierdo, y luego usar `127.0.0.1` como la IP, y `8080` como el puerto, y nómalo `Burp` o `ZAP`:

![Foxyproxy Añadir](https://academy.hackthebox.com/storage/modules/110/foxyproxy_add.jpg)

Nota: Esta configuración ya se ha añadido a Foxy Proxy en PwnBox, por lo que no tiene que hacer este paso si está utilizando PwnBox.

Finalmente, podemos hacer clic en el `Foxy Proxy` icono y seleccionar `Burp`/`ZAP`. ![Uso de Foxyproxy](https://academy.hackthebox.com/storage/modules/110/foxyproxy_use.jpg)

---

## Instalación de CA Certificate

Otro paso importante al usar Burp Proxy/ZAP con nuestro navegador es instalar los certificados CA del proxy web. Si no hacemos este paso, es posible que algún tráfico HTTPS no se enrute correctamente o que tengamos que hacer clic `accept` cada vez que Firefox necesita enviar una solicitud HTTPS.

Podemos instalar el certificado de Burp una vez que seleccionamos Burp como nuestro proxy en `Foxy Proxy`, navegando a `http://burp`y descargue el certificado desde allí haciendo clic en `CA Certificate`:

   

![](https://academy.hackthebox.com/storage/modules/110/burp_cert.jpg)

Para obtener el certificado de ZAP, podemos ir a (`Tools>Options>Dynamic SSL Certificate`), luego haga clic en `Save`:

![ZAP cert](https://academy.hackthebox.com/storage/modules/110/zap_cert.jpg)

También podemos cambiar nuestro certificado generando uno nuevo con el `Generate` botón.

Una vez que tengamos nuestros certificados, podemos instalarlos dentro de Firefox navegando a [acerca de:preferences#privacy](about:preferences#privacy), desplazándose hacia abajo y haciendo clic `View Certificates`:

![Cerrar Firefox](https://academy.hackthebox.com/storage/modules/110/firefox_cert.jpg)

Después de eso, podemos seleccionar el `Authorities` pestaña y luego haga clic en `import`, y seleccione el certificado de CA descargado:

![Importar Firefox Cert](https://academy.hackthebox.com/storage/modules/110/firefox_import_cert.jpg)

Finalmente, debemos seleccionar `Trust this CA to identify websites` y `Trust this CA to identify email users`, y luego haga clic en Aceptar: ![Confía en Firefox Cert](https://academy.hackthebox.com/storage/modules/110/firefox_trust_cert.jpg)

Una vez que instalamos el certificado y configuramos el proxy de Firefox, todo el tráfico web de Firefox comenzará a enrutarse a través de nuestro proxy web.


# Interceptar Solicitudes Web

---

Ahora que hemos configurado nuestro proxy, podemos usarlo para interceptar y manipular varias solicitudes HTTP enviadas por la aplicación web que estamos probando. Comenzaremos aprendiendo cómo interceptar solicitudes web, cambiarlas y luego enviarlas a su destino previsto.

---

## Interceptar Solicitudes

#### Arrugar

En Burp, podemos navegar hasta el `Proxy` la pestaña y la intercepción de la solicitud deben estar activadas de forma predeterminada. Si queremos activar o desactivar la intercepción de la solicitud, podemos ir al `Intercept` sub-pestaña y haga clic en `Intercept is on/off` botón para hacerlo:

![Burp Intercept En](https://academy.hackthebox.com/storage/modules/110/burp_intercept_htb_on.jpg)

Una vez que activemos la intercepción de la solicitud, podemos iniciar el navegador preconfigurado y luego visitar nuestro sitio web de destino después de generarlo desde el ejercicio al final de esta sección. Luego, una vez que volvamos a Burp, veremos la solicitud interceptada esperando nuestra acción, y podemos hacer clic en `forward` para reenviar la solicitud:

![Burp Intercept Página](https://academy.hackthebox.com/storage/modules/110/burp_intercept_page.jpg)

Nota: como todo el tráfico de Firefox será interceptado en este caso, podemos ver que otra solicitud ha sido interceptada antes de esta. Si esto sucede, haga clic en 'Enviar', hasta que obtengamos la solicitud a nuestra IP de destino, como se muestra arriba.

#### ZAP

En ZAP, la intercepción está desactivada de forma predeterminada, como lo muestra el botón verde en la barra superior (verde indica que las solicitudes pueden pasar y no ser interceptadas). Podemos hacer clic en este botón para activar o desactivar la Intercepción de solicitud, o podemos usar el acceso directo [`CTRL+B`] para activarlo o desactivarlo:

![ZAP Intercept En](https://academy.hackthebox.com/storage/modules/110/zap_intercept_htb_on.jpg)

Luego, podemos iniciar el navegador preconfigurado y volver a visitar la página web del ejercicio. Veremos la solicitud interceptada en el panel superior derecho, y podemos hacer clic en el paso (derecha al rojo `break` botón) para reenviar la solicitud:

![Página de Intercepción ZAP](https://academy.hackthebox.com/storage/modules/110/zap_intercept_page.jpg)

ZAP también tiene una poderosa característica llamada `Heads Up Display (HUD)`, lo que nos permite controlar la mayoría de las principales funciones de ZAP desde el navegador preconfigurado. Podemos habilitar el `HUD` al hacer clic en su botón al final de la barra de menú superior:

![ZAP HUD En](https://academy.hackthebox.com/storage/modules/110/zap_enable_HUD.jpg)

El HUD tiene muchas características que cubriremos a medida que pasemos por el módulo. Para interceptar solicitudes, podemos hacer clic en el segundo botón desde la parte superior del panel izquierdo para activar la intercepción de solicitudes:

   

![](https://academy.hackthebox.com/storage/modules/110/zap_hud_break.jpg)

Nota: En algunas versiones de los navegadores, el HUD del ZAP podría no funcionar según lo previsto.

Ahora, una vez que actualicemos la página o enviemos otra solicitud, el HUD interceptará la solicitud y nos la presentará para que actuemos:

   

![](https://academy.hackthebox.com/storage/modules/110/zap_hud_break_request.jpg)

Podemos elegir `step` para enviar la solicitud y examinar su respuesta y romper cualquier solicitud adicional, o podemos optar por `continue` y deje que la página envíe las solicitudes restantes. El `step` botón es útil cuando queremos examinar cada paso de la funcionalidad de la página, mientras `continue` es útil cuando solo estamos interesados en una sola solicitud y podemos reenviar las solicitudes restantes una vez que alcancemos nuestra solicitud de destino.

**Consejo:** La primera vez que use el navegador ZAP preconfigurado, se le presentará el tutorial de HUD. Puede considerar tomar este tutorial después de esta sección, ya que le enseñará los conceptos básicos del HUD. Incluso si no comprende todo, las próximas secciones deben cubrir lo que se perdió. Si no obtiene el tutorial, puede hacer clic en el botón de configuración en la parte inferior derecha y elegir "Tome el tutorial de HUD".

---

## Manipulación de Solicitudes Interceptadas

Una vez que interceptemos la solicitud, permanecerá colgada hasta que la reenviemos, como lo hicimos anteriormente. Podemos examinar la solicitud, manipularla para hacer los cambios que queramos y luego enviarla a su destino. Esto nos ayuda a comprender mejor qué información envía una aplicación web en particular en sus solicitudes web y cómo puede responder a cualquier cambio que realicemos en esa solicitud.

Existen numerosas aplicaciones para esto en Web Penetration Testing, como las pruebas para:

1. Inyecciones SQL
2. Inyecciones de comando
3. Bypass de carga
4. Bypass de autenticación
5. XSS
6. XXE
7. Manejo de errores
8. Deserialización

Y muchas otras vulnerabilidades web potenciales, como veremos en otros módulos web en HTB Academy. Entonces, mostremos esto con un ejemplo básico para demostrar la interceptación y manipulación de solicitudes web.

Volvamos a activar la intercepción de solicitud en la herramienta de nuestra elección, configure el `IP` valor en la página, luego haga clic en el `Ping` botón. Una vez que nuestra solicitud es interceptada, debemos obtener una solicitud HTTP similar a la siguiente :

Código: http

```http
POST /ping HTTP/1.1
Host: 46.101.23.188:30820
Content-Length: 4
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://46.101.23.188:30820
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://46.101.23.188:30820/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

ip=1
```

Por lo general, solo podemos especificar números en el `IP` campo que utiliza el navegador, ya que la página web nos impide enviar caracteres no numéricos utilizando JavaScript front-end. Sin embargo, con el poder de interceptar y manipular solicitudes HTTP, podemos intentar usar otros caracteres para "romper" la aplicación ("romper" el flujo de solicitud/respuesta manipulando el parámetro de destino, sin dañar la aplicación web de destino). Si la aplicación web no verifica y valida las solicitudes HTTP en el back-end, es posible que podamos manipularla y explotarla.

Entonces, cambiemos el `ip` valor del parámetro de `1` a `;ls;` y vea cómo la aplicación web maneja nuestra entrada:

   

![](https://academy.hackthebox.com/storage/modules/110/ping_manipulate_request.jpg)

Una vez que hagamos clic en continuar/avanzar, veremos que la respuesta cambió de la salida de ping predeterminada a la `ls` salida, lo que significa que manipulamos con éxito la solicitud para inyectar nuestro comando:

   

![](https://academy.hackthebox.com/storage/modules/110/ping_inject.jpg)

Esto demuestra un ejemplo básico de cómo la interceptación y la manipulación de solicitudes pueden ayudar a probar aplicaciones web en busca de diversas vulnerabilidades, lo que se considera una herramienta esencial para poder probar diferentes aplicaciones web de manera efectiva.

Nota: Como se mencionó anteriormente, no cubriremos ataques web específicos en este módulo, sino más bien cómo Web Proxies puede facilitar varios tipos de ataques. Otros módulos web en HTB Academy cubren este tipo de ataques en profundidad.


# Interceptando Respuestas

---

En algunos casos, es posible que tengamos que interceptar las respuestas HTTP del servidor antes de que lleguen al navegador. Esto puede ser útil cuando queremos cambiar el aspecto de una página web específica, como habilitar ciertos campos deshabilitados o mostrar ciertos campos ocultos, lo que puede ayudarnos en nuestras actividades de prueba de penetración.

Entonces, veamos cómo podemos lograr eso con el ejercicio que probamos en la sección anterior.

En nuestro ejercicio anterior, el `IP` el campo solo nos permitió ingresar valores numéricos. Si interceptamos la respuesta antes de que llegue a nuestro navegador, podemos editarla para aceptar cualquier valor, lo que nos permitiría ingresar la carga útil que usamos la última vez directamente.

---

## Arrugar

En Burp, podemos habilitar la intercepción de respuesta yendo a (`Proxy>Options`) y habilitación `Intercept Response` bajo `Intercept Server Responses`:

![Burp Habilitar Respuesta Int](https://academy.hackthebox.com/storage/modules/110/response_interception_enable.jpg)

Después de eso, podemos habilitar la intercepción de solicitudes una vez más y actualizar la página con [`CTRL+SHIFT+R`] en nuestro navegador (para forzar una actualización completa). Cuando volvamos a Burp, deberíamos ver la solicitud interceptada, y podemos hacer clic en `forward`. Una vez que reenviemos la solicitud, veremos nuestra respuesta interceptada:

![Respuesta de Intercept Burp](https://academy.hackthebox.com/storage/modules/110/response_intercept_response_1_1.jpg)

Intentemos cambiar el `type="number"` en la línea 27 a `type="text"`, lo que debería permitirnos escribir cualquier valor que queramos. También cambiaremos el `maxlength="3"` a `maxlength="100"` para que podamos ingresar una entrada más larga:

Código: html

```html
<input type="text" id="ip" name="ip" min="1" max="255" maxlength="100"
    oninput="javascript: if (this.value.length > this.maxLength) this.value = this.value.slice(0, this.maxLength);"
    required>
```

Ahora, una vez que hacemos clic en `forward` una vez más, podemos volver a Firefox para examinar la respuesta editada:

   

![](https://academy.hackthebox.com/storage/modules/110/response_intercept_response_2.jpg)

Como podemos ver, podríamos cambiar la forma en que el navegador representa la página y ahora podemos ingresar cualquier valor que queramos. Podemos utilizar la misma técnica para habilitar persistentemente cualquier botón HTML deshabilitado modificando su código HTML.

Ejercicio: Intente usar la carga útil que usamos la última vez directamente dentro del navegador, para probar cómo las respuestas de interceptación pueden facilitar las pruebas de penetración de aplicaciones web.

---

## ZAP

Tratemos de ver cómo podemos hacer lo mismo con ZAP. Como vimos en la sección anterior, cuando nuestras solicitudes son interceptadas por ZAP, podemos hacer clic en `Step`y enviará la solicitud e interceptará automáticamente la respuesta:

![Respuesta de Intercepción ZAP](https://academy.hackthebox.com/storage/modules/110/zap_response_intercept_response.jpg)

Una vez que hacemos los mismos cambios que hicimos anteriormente y hacemos clic en `Continue`, veremos que también podemos usar cualquier valor de entrada:

   

![](https://academy.hackthebox.com/storage/modules/110/ZAP_edit_response.jpg)

Sin embargo, ZAP HUD también tiene otra característica poderosa que puede ayudarnos en casos como este. Si bien en muchos casos es posible que tengamos que interceptar la respuesta para realizar cambios personalizados, si todo lo que queríamos era habilitar campos de entrada deshabilitados o mostrar campos de entrada ocultos, entonces podemos hacer clic en el tercer botón de la izquierda (el icono de la bombilla) y habilitará/mostrará estos campos sin que tengamos que interceptar la respuesta o actualizar la página.

Por ejemplo, la siguiente aplicación web tiene el `IP` campo de entrada como deshabilitado:

   

![](https://academy.hackthebox.com/storage/modules/110/ZAP_disabled_field.jpg)

En estos casos, podemos hacer clic en el `Show/Enable` botón, y habilitará el botón para nosotros, y podemos interactuar con él para agregar nuestra entrada:

   

![](https://academy.hackthebox.com/storage/modules/110/ZAP_enable_field.jpg)

De manera similar, podemos usar esta función para mostrar todos los campos o botones ocultos. `Burp` también tiene una característica similar, que podemos habilitar en `Proxy>Options>Response Modification`, luego seleccione una de las opciones, como `Unhide hidden form fields`.

Otra característica similar es el `Comments` botón, que indicará las posiciones donde hay comentarios HTML que normalmente solo son visibles en el código fuente. Podemos hacer clic en el `+` botón en el panel izquierdo y seleccione `Comments` para agregar el `Comments` botón, y una vez que hacemos clic en él, el `Comments` se deben mostrar los indicadores. Por ejemplo, la siguiente captura de pantalla muestra un indicador de una posición que tiene un comentario, y flotando sobre él con nuestro cursor muestra el contenido del comentario:

   

![](https://academy.hackthebox.com/storage/modules/110/ZAP_show_comments.jpg)

Poder modificar el aspecto de la página web nos facilita mucho realizar pruebas de penetración de aplicaciones web en ciertos escenarios en lugar de tener que enviar nuestra entrada a través de una solicitud interceptada. A continuación, veremos cómo podemos automatizar este proceso para modificar nuestros cambios en la respuesta automáticamente para que no tengamos que seguir interceptando y cambiando manualmente las respuestas.


# Modificación Automática

---

Es posible que deseemos aplicar ciertas modificaciones a todas las solicitudes HTTP salientes o a todas las respuestas HTTP entrantes en ciertas situaciones. En estos casos, podemos utilizar modificaciones automáticas basadas en las reglas que establecemos, por lo que las herramientas de proxy web las aplicarán automáticamente.

---

## Modificación Automática de Solicitud

Comencemos con un ejemplo de modificación automática de solicitudes. Podemos elegir hacer coincidir cualquier texto dentro de nuestras solicitudes, ya sea en el encabezado de la solicitud o en el cuerpo de la solicitud, y luego reemplazarlos con texto diferente. Por el bien de la demostración, reemplacemos nuestro `User-Agent` con `HackTheBox Agent 1.0`, que puede ser útil en los casos en que podamos estar tratando con filtros que bloquean ciertos Usuarios-Agentes.

#### Burp Match y Reemplazar

Podemos ir a (`Proxy>Options>Match and Replace`) y haga clic en `Add` en Burp. Como muestra la siguiente captura de pantalla, estableceremos las siguientes opciones:

![Burp Match Reemplazar](https://academy.hackthebox.com/storage/modules/110/burp_match_replace_user_agent_1.jpg)

|||
|---|---|
|`Type`: `Request header`|Dado que el cambio que queremos realizar estará en el encabezado de la solicitud y no en su cuerpo.|
|`Match`: `^User-Agent.*$`|El patrón regex que coincide con toda la línea `User-Agent` en él.|
|`Replace`: `User-Agent: HackTheBox Agent 1.0`|Este es el valor que reemplazará la línea que coincidimos anteriormente.|
|`Regex match`: Verdadero|No sabemos la cadena exacta de User-Agent que queremos reemplazar, por lo que usaremos regex para que coincida con cualquier valor que coincida con el patrón que especificamos anteriormente.|

Una vez que ingresamos las opciones anteriores y hacemos clic en `Ok`, nuestra nueva opción Match and Replace se agregará y habilitará y comenzará a reemplazar automáticamente el `User-Agent` encabezado en nuestras solicitudes con nuestro nuevo User-Agent. Podemos verificarlo visitando cualquier sitio web utilizando el navegador Burp preconfigurado y revisando la solicitud interceptada. Veremos que nuestro Agente de Usuario ha sido reemplazado automáticamente:

![Burp Match Reemplazar](https://academy.hackthebox.com/storage/modules/110/burp_match_replace_user_agent_2.jpg)

#### Replacero ZAP

ZAP tiene una característica similar llamada `Replacer`, a la que podemos acceder presionando [`CTRL+R`] o haciendo clic en `Replacer` en el menú de opciones de ZAP. Es bastante similar a lo que hicimos anteriormente, por lo que podemos hacer clic en `Add` y agregue las mismas opciones que usamos anteriormente:

![ZAP Match Reemplazar](https://academy.hackthebox.com/storage/modules/110/zap_match_replace_user_agent_1.jpg)

- `Description`: `HTB User-Agent`.
- `Match Type`: `Request Header (will add if not present)`.
- `Match String`: `User-Agent`. Podemos seleccionar el encabezado que queremos en el menú desplegable, y ZAP reemplazará su valor.
- `Replacement String`: `HackTheBox Agent 1.0`.
- `Enable`: Verdadero.

ZAP también tiene el `Request Header String` que podemos usar con un patrón Regex. `Try using this option with the same values we used for Burp to see how it works.`

ZAP también proporciona la opción de establecer el `Initiators`, a la que podemos acceder haciendo clic en la otra pestaña en las ventanas que se muestran arriba. Los iniciadores nos permiten seleccionar dónde está nuestro `Replacer` se aplicará la opción. Mantendremos la opción predeterminada de `Apply to all HTTP(S) messages` para aplicar en todas partes.

Ahora podemos habilitar la intercepción de solicitudes presionando [`CTRL+B`], luego puede visitar cualquier página en el navegador ZAP preconfigurado:

![ZAP Match Reemplazar](https://academy.hackthebox.com/storage/modules/110/zap_match_replace_user_agent_2.jpg)

---

## Modificación Automática de Respuesta

El mismo concepto también se puede usar con respuestas HTTP. En la sección anterior, es posible que haya notado cuando interceptamos la respuesta que las modificaciones que hicimos a la `IP` el campo era temporal y no se aplicaba cuando actualizábamos la página a menos que interceptáramos la respuesta y los añadimos de nuevo. Para resolver esto, podemos automatizar la modificación de la respuesta de manera similar a lo que hicimos anteriormente para habilitar automáticamente cualquier carácter en el `IP` campo para una inyección de comandos más fácil.

Volvamos a (`Proxy>Options>Match and Replace`) en Burp para agregar otra regla. Esta vez usaremos el tipo de `Response body` dado que el cambio que queremos hacer existe en el cuerpo de la respuesta y no en sus encabezados. En este caso, no tenemos que usar regex como sabemos la cadena exacta que queremos reemplazar, aunque es posible usar regex para hacer lo mismo si lo preferimos.

![Burp Match Reemplazar](https://academy.hackthebox.com/storage/modules/110/burp_match_replace_response_1.jpg)

- `Type`: `Response body`.
- `Match`: `type="number"`.
- `Replace`: `type="text"`.
- `Regex match`: Falso.

Intenta agregar otra regla para cambiar `maxlength="3"` a `maxlength="100"`.

Ahora, una vez que actualizamos la página con [`CTRL+SHIFT+R`], veremos que podemos agregar cualquier entrada al campo de entrada, y esto también debe persistir entre las actualizaciones de la página:

![Burp Match Reemplazar](https://academy.hackthebox.com/storage/modules/110/burp_match_replace_response_2.jpg)

Ahora podemos hacer clic en `Ping`y nuestra inyección de comandos debe funcionar sin interceptar y modificar la solicitud.

Ejercicio 1: Intente aplicar las mismas reglas con ZAP Replacer. Puede hacer clic en la pestaña a continuación para mostrar las opciones correctas.



Ejercicio 2: Intente agregar una regla que agregue automáticamente `;ls;` cuando hacemos clic en `Ping`, haciendo coincidir y reemplazar el cuerpo de solicitud de la `Ping` solicitar.



# Repetir Solicitudes

---

En las secciones anteriores, omitimos con éxito la validación de entrada para usar una entrada no numérica para alcanzar la inyección de comandos en el servidor remoto. Si queremos repetir el mismo proceso con un comando diferente, tendríamos que interceptar la solicitud nuevamente, proporcionar una carga útil diferente, reenviarla nuevamente y finalmente revisar nuestro navegador para obtener el resultado final.

Como puede imaginar, si hiciéramos esto para cada comando, nos llevaría una eternidad enumerar un sistema, ya que cada comando requeriría 5-6 pasos para ser ejecutado. Sin embargo, para tales tareas repetitivas, podemos utilizar la repetición de solicitudes para facilitar significativamente este proceso.

La repetición de solicitudes nos permite reenviar cualquier solicitud web que haya pasado previamente por el proxy web. Esto nos permite realizar cambios rápidos en cualquier solicitud antes de enviarla, luego obtener la respuesta dentro de nuestras herramientas sin interceptar y modificar cada solicitud.

---

## Historia de Proxy

Para comenzar, podemos ver el historial de solicitudes HTTP en `Burp` en (`Proxy>HTTP History`):

![Pestaña de historial de quemaduras](https://academy.hackthebox.com/storage/modules/110/burp_history_tab.jpg)

En `ZAP` HUD, podemos encontrarlo en el panel Historial inferior o en la interfaz de usuario principal de ZAP en la parte inferior `History` pestaña también:

![pestaña de historial ZAP](https://academy.hackthebox.com/storage/modules/110/zap_history_tab.jpg)

Ambas herramientas también proporcionan opciones de filtrado y clasificación para el historial de solicitudes, lo que puede ser útil si tratamos una gran cantidad de solicitudes y queremos localizar una solicitud específica. `Try to see how filters work on both tools.`

Nota: Ambas herramientas también mantienen el historial de WebSockets, que muestra todas las conexiones iniciadas por la aplicación web incluso después de cargarse, como actualizaciones asíncronas y búsqueda de datos. WebSockets puede ser útil cuando se realizan pruebas avanzadas de penetración web, y están fuera del alcance de este módulo.

Si hacemos clic en cualquier solicitud en el historial de cualquiera de las herramientas, se mostrarán sus detalles:

`Burp`: ![Detalles de la solicitud de Burp](https://academy.hackthebox.com/storage/modules/110/burp_history_details.jpg)

`ZAP`: ![Detalles de la solicitud ZAP](https://academy.hackthebox.com/storage/modules/110/zap_history_details.jpg)

Consejo: Si bien ZAP solo muestra la solicitud final/modificada que se envió, Burp proporciona la capacidad de examinar tanto la solicitud original como la solicitud modificada. Si se editó una solicitud, el encabezado del panel diría `Original Request`, y podemos hacer clic en él y seleccionar `Edited Request` para examinar la solicitud final que se envió.

---

## Repetir Solicitudes

#### Arrugar

Una vez que localizamos la solicitud que queremos repetir, podemos hacer clic en [`CTRL+R`] en Burp para enviarlo al `Repeater` tab, y luego podemos navegar a la `Repeater` pestaña o haga clic en [`CTRL+SHIFT+R`] para ir directamente a él. Una vez en `Repeater`, podemos hacer clic en `Send` para enviar la solicitud:

![Burp repetir solicitud](https://academy.hackthebox.com/storage/modules/110/burp_repeater_request.jpg)

Consejo: También podemos hacer clic derecho en la solicitud y seleccionar `Change Request Method` para cambiar el método HTTP entre POST/GET sin tener que reescribir toda la solicitud.

#### ZAP

En ZAP, una vez que localizamos nuestra solicitud, podemos hacer clic derecho sobre ella y seleccionar `Open/Resend with Request Editor`, que abriría la ventana del editor de solicitudes, y nos permitiría reenviar la solicitud con el `Send` botón para enviar nuestra solicitud: ![Solicitud de reenvío ZAP](https://academy.hackthebox.com/storage/modules/110/zap_repeater_request.jpg)

También podemos ver el `Method` menú desplegable, lo que nos permite cambiar rápidamente el método de solicitud a cualquier otro método HTTP.

Consejo: De forma predeterminada, la ventana del Editor de solicitudes en ZAP tiene la Solicitud/Respuesta en diferentes pestañas. Puede hacer clic en los botones de visualización para cambiar la forma en que están organizados. Para que coincida con el aspecto anterior, elija las mismas opciones de visualización que se muestran en la captura de pantalla.

Podemos lograr el mismo resultado dentro del navegador preconfigurado con `ZAP HUD`. Podemos localizar la solicitud en el panel Historial inferior, y una vez que hacemos clic en él, el `Request Editor` window se mostrará, lo que nos permitirá reenviarlo. Podemos seleccionar `Replay in Console` para obtener la respuesta en el mismo `HUD` ventana, o seleccione `Replay in Browser` para ver la respuesta dada en el navegador:

![Reenviar ZAP HUD](https://academy.hackthebox.com/storage/modules/110/zap_hud_resend.jpg)

Por lo tanto, intentemos modificar nuestra solicitud y enviarla. En las tres opciones (`Burp Repeater`, `ZAP Request Editor`, y `ZAP HUD`), vemos que las solicitudes son modificables, y podemos seleccionar el texto que queremos cambiar y reemplazarlo con lo que queramos, y luego hacer clic en `Send` botón para enviarlo de nuevo:

![Burp modificar repetir](https://academy.hackthebox.com/storage/modules/110/burp_repeat_modify.jpg)

Como podemos ver, podríamos modificar fácilmente el comando y obtener instantáneamente su salida usando Burp `Repeater`. Intenta hacer lo mismo en `ZAP Request Editor` y `ZAP HUD` para ver cómo funcionan.

Finalmente, podemos ver en nuestra solicitud POST anterior que los datos están codificados por URL. Esta es una parte esencial del envío de solicitudes HTTP personalizadas, que discutiremos en la siguiente sección.


# Codificación/Decodificación

---

A medida que modificamos y enviamos solicitudes HTTP personalizadas, es posible que tengamos que realizar varios tipos de codificación y decodificación para interactuar correctamente con el servidor web. Ambas herramientas tienen codificadores incorporados que pueden ayudarnos a codificar y decodificar rápidamente varios tipos de texto.

---

## codificación de URL

Es esencial asegurarse de que nuestros datos de solicitud estén codificados por URL y que nuestros encabezados de solicitud estén configurados correctamente. De lo contrario, podemos obtener un error del servidor en la respuesta. Es por eso que la codificación y decodificación de datos se vuelve esencial a medida que modificamos y repetimos las solicitudes web. Algunos de los caracteres clave que necesitamos codificar son:

- `Spaces`: Puede indicar el final de los datos de solicitud si no está codificado
- `&`: Interpretado de otro modo como un delimitador de parámetros
- `#`: Interpretado de otro modo como un identificador de fragmento

Para codificar el texto URL en Burp Repeater, podemos seleccionar ese texto y hacer clic derecho sobre él, luego seleccionar (`Convert Selection>URL>URL encode key characters`), o seleccionando el texto y haciendo clic en [`CTRL+U`]. Burp también admite la codificación de URL a medida que escribimos si hacemos clic con el botón derecho y habilitamos esa opción, que codificará todo nuestro texto a medida que lo escribimos. Por otro lado, ZAP debe codificar automáticamente todos nuestros datos de solicitud en segundo plano antes de enviar la solicitud, aunque es posible que no lo veamos explícitamente.

Hay otros tipos de codificación de URL, como `Full URL-Encoding` o `Unicode URL` codificación, que también puede ser útil para solicitudes con muchos caracteres especiales.

---

## Decodificación

Si bien la codificación URL es clave para las solicitudes HTTP, no es el único tipo de codificación que encontraremos. Es muy común que las aplicaciones web codificen sus datos, por lo que deberíamos poder decodificar rápidamente esos datos para examinar el texto original. Por otro lado, los servidores de back-end pueden esperar que los datos se codifiquen en un formato particular o con un codificador específico, por lo que debemos poder codificar rápidamente nuestros datos antes de enviarlos.

Los siguientes son algunos de los otros tipos de codificadores compatibles con ambas herramientas:

- HTML
- Unicode
- Base64
- hexade ASCII

Para acceder al codificador completo en Burp, podemos ir al `Decoder` pestaña. En ZAP, podemos usar el `Encoder/Decoder/Hash` haciendo clic en [`CTRL+E`]. Con estos codificadores, podemos ingresar cualquier texto y codificarlo o decodificarlo rápidamente. Por ejemplo, tal vez nos encontramos con la siguiente cookie que está codificada en base64, y necesitamos decodificarla: `eyJ1c2VybmFtZSI6Imd1ZXN0IiwgImlzX2FkbWluIjpmYWxzZX0=`

Podemos ingresar la cadena anterior en Burp Decoder y seleccionar `Decode as > Base64`y obtendremos el valor decodificado:

![Burp B64 Decodificación](https://academy.hackthebox.com/storage/modules/110/burp_b64_decode.jpg)

En versiones recientes de Burp, también podemos usar el `Burp Inspector` herramienta para realizar codificación y decodificación (entre otras cosas), que se puede encontrar en varios lugares como `Burp Proxy` o `Burp Repeater`:

![Inspector de Burp](https://academy.hackthebox.com/storage/modules/110/burp_inspector.jpg)

En ZAP, podemos usar el `Encoder/Decoder/Hash` herramienta, que decodificará automáticamente cadenas utilizando varios decodificadores en el `Decode` pestaña: ![decodificación ZAP B64](https://academy.hackthebox.com/storage/modules/110/zap_b64_decode.jpg)

Consejo: Podemos crear pestañas personalizadas en el codificador/Decodificador/Hash de ZAP con el botón "Agregar Nueva Pestaña", y luego podemos agregar cualquier tipo de codificador/decodificador en el que queramos que se muestre el texto. Intente crear su propia pestaña con algunos codificadores/decodificadores.

---

## Codificación

Como podemos ver, el texto tiene el valor `{"username":"guest", "is_admin":false}`. Por lo tanto, si estábamos realizando una prueba de penetración en una aplicación web y descubrimos que la cookie tiene este valor, es posible que deseemos probar la modificación para ver si cambia nuestros privilegios de usuario. Entonces, podemos copiar el valor anterior, cambiar `guest` a `admin` y `false` a `true`e intente codificarlo nuevamente utilizando su método de codificación original (`base64`):

![Burp B64 Codificar](https://academy.hackthebox.com/storage/modules/110/burp_b64_encode.jpg)

![Codificación ZAP B64](https://academy.hackthebox.com/storage/modules/110/zap_b64_encode.jpg)

Consejo: La salida del decodificador Burp se puede codificar/descodificar directamente con un codificador diferente. Seleccione el nuevo método de codificador en el panel de salida en la parte inferior y se codificará/descodificará nuevamente. En ZAP, podemos copiar el texto de salida y pegarlo en el campo de entrada anterior.

Luego podemos copiar la cadena codificada base64 y usarla con nuestra solicitud en Burp `Repeater` o ZAP `Request Editor`. El mismo concepto se puede utilizar para codificar y decodificar varios tipos de texto codificado para realizar pruebas efectivas de penetración web sin utilizar otras herramientas para hacer la codificación.


# Herramientas de Proxys

---

Un aspecto importante del uso de proxies web es permitir la interceptación de solicitudes web realizadas por herramientas de línea de comandos y aplicaciones cliente gruesas. Esto nos da transparencia en las solicitudes web realizadas por estas aplicaciones y nos permite utilizar todas las diferentes características de proxy que hemos utilizado con las aplicaciones web.

Para enrutar todas las solicitudes web realizadas por una herramienta específica a través de nuestras herramientas de proxy web, tenemos que configurarlas como proxy de la herramienta (es decir. `http://127.0.0.1:8080`), de manera similar a lo que hicimos con nuestros navegadores. Cada herramienta puede tener un método diferente para configurar su proxy, por lo que es posible que tengamos que investigar cómo hacerlo para cada uno.

Esta sección cubrirá algunos ejemplos de cómo usar proxies web para interceptar solicitudes web realizadas por dichas herramientas. Puede usar Burp o ZAP, ya que el proceso de configuración es el mismo.

Nota: Las herramientas de proxy generalmente las ralentizan, por lo tanto, solo las herramientas proxy cuando necesita investigar sus solicitudes y no para el uso normal.

---

## Proxycaínas

Una herramienta muy útil en Linux es [proxycain](https://github.com/haad/proxychains), que enruta todo el tráfico proveniente de cualquier herramienta de línea de comandos a cualquier proxy que especifiquemos. `Proxychains` agrega un proxy a cualquier herramienta de línea de comandos y, por lo tanto, es el método más simple y fácil para enrutar el tráfico web de las herramientas de línea de comandos a través de nuestros proxies web.

Para usar `proxychains`, primero tenemos que editar `/etc/proxychains.conf`, comente la línea final y agregue la siguiente línea al final:

  Herramientas de Proxys

```shell-session
#socks4         127.0.0.1 9050
http 127.0.0.1 8080
```

También deberíamos permitir `Quiet Mode` para reducir el ruido por uncommenting `quiet_mode`. Una vez hecho esto, podemos anteponernos `proxychains` a cualquier comando, y el tráfico de ese comando debe enrutarse `proxychains` (es decir, nuestro proxy web). Por ejemplo, intentemos usar `cURL` en uno de nuestros ejercicios anteriores:

  Herramientas de Proxys

```shell-session
vcrdcr@htb[/htb]$ proxychains curl http://SERVER_IP:PORT

ProxyChains-3.1 (http://proxychains.sf.net)
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <title>Ping IP</title>
    <link rel="stylesheet" href="./style.css">
</head>
...SNIP...
</html>    
```

Vemos que funcionó como lo haría normalmente, con el adicional `ProxyChains-3.1` línea al principio, para tener en cuenta que se está enrutando `ProxyChains`. Si volvemos a nuestro proxy web (Burp en este caso), veremos que la solicitud realmente lo ha pasado:

![Proxychains Curl](https://academy.hackthebox.com/storage/modules/110/proxying_proxychains_curl.jpg)

---

## Mapa

A continuación, intentemos proxy `nmap` a través de nuestro proxy web. Para saber cómo usar las configuraciones proxy para cualquier herramienta, podemos ver su manual con `man nmap`, o su página de ayuda con `nmap -h`:

  Herramientas de Proxys

```shell-session
vcrdcr@htb[/htb]$ nmap -h | grep -i prox

  --proxies <url1,[url2],...>: Relay connections through HTTP/SOCKS4 proxies
```

Como podemos ver, podemos usar el `--proxies` bandera. También deberíamos agregar el `-Pn` marcar para omitir el descubrimiento del host (como se recomienda en la página de manual). Finalmente, también usaremos el `-sC` bandera para examinar lo que hace un escaneo de script nmap:

  Herramientas de Proxys

```shell-session
vcrdcr@htb[/htb]$ nmap --proxies http://127.0.0.1:8080 SERVER_IP -pPORT -Pn -sC

Starting Nmap 7.91 ( https://nmap.org )
Nmap scan report for SERVER_IP
Host is up (0.11s latency).

PORT      STATE SERVICE
PORT/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 0.49 seconds
```

Una vez más, si vamos a nuestra herramienta de proxy web, veremos todas las solicitudes realizadas por nmap en el historial de proxy:

![proxy nmap](https://academy.hackthebox.com/storage/modules/110/proxying_nmap.jpg)

Nota: El proxy incorporado de Nmap todavía está en su fase experimental, como se menciona en su manual (`man nmap`), por lo que no todas las funciones o el tráfico pueden enrutarse a través del proxy. En estos casos, simplemente podemos recurrir a `proxychains`, como hicimos antes.

---

## Metasploit

Finalmente, intentemos proxy de tráfico web realizado por los módulos Metasploit para investigarlos y depurarlos mejor. Deberíamos comenzar comenzando Metasploit con `msfconsole`. Luego, para establecer un proxy para cualquier exploit dentro de Metasploit, podemos usar el `set PROXIES` bandera. Probemos el `robots_txt` escáner como ejemplo y ejecutarlo contra uno de nuestros ejercicios anteriores:

  Herramientas de Proxys

```shell-session
vcrdcr@htb[/htb]$ msfconsole

msf6 > use auxiliary/scanner/http/robots_txt
msf6 auxiliary(scanner/http/robots_txt) > set PROXIES HTTP:127.0.0.1:8080

PROXIES => HTTP:127.0.0.1:8080


msf6 auxiliary(scanner/http/robots_txt) > set RHOST SERVER_IP

RHOST => SERVER_IP


msf6 auxiliary(scanner/http/robots_txt) > set RPORT PORT

RPORT => PORT


msf6 auxiliary(scanner/http/robots_txt) > run

[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Una vez más, podemos volver a nuestra herramienta de proxy web de elección y examinar el historial de proxy para ver todas las solicitudes enviadas:

![proxy msf](https://academy.hackthebox.com/storage/modules/110/proxying_msf.jpg)

Vemos que la solicitud ha pasado por nuestro proxy web. El mismo método se puede utilizar con otros escáneres, exploits y otras características en Metasploit.

De manera similar, podemos usar nuestros proxies web con otras herramientas y aplicaciones, incluidos scripts y clientes gruesos. Todo lo que tenemos que hacer es establecer el proxy de cada herramienta para utilizar nuestro proxy web. Esto nos permite examinar exactamente lo que estas herramientas están enviando y recibiendo y potencialmente repetir y modificar sus solicitudes mientras se realizan pruebas de penetración de aplicaciones web.


# Intruso Burp

---

Tanto Burp como ZAP proporcionan características adicionales que no sean el proxy web predeterminado, que son esenciales para las pruebas de penetración de aplicaciones web. Dos de las características adicionales más importantes son `web fuzzers` y `web scanners`. Los fuzzers web incorporados son herramientas poderosas que actúan como herramientas de fuzzing web, enumeración y fuerza bruta. Esto también puede actuar como una alternativa para muchos de los fuzzers basados en CLI que utilizamos, como `ffuf`, `dirbuster`, `gobuster`, `wfuzz`, entre otros.

El fuzzer web de Burp se llama `Burp Intruder`, y se puede utilizar para difuminar páginas, directorios, subdominios, parámetros, valores de parámetros y muchas otras cosas. Aunque es mucho más avanzado que la mayoría de las herramientas de fuzzing web basadas en CLI, el gratuito `Burp Community` la versión se estrangula a una velocidad de 1 solicitud por segundo, lo que la hace extremadamente lenta en comparación con las herramientas de fuzzing web basadas en CLI, que generalmente pueden leer hasta 10k solicitudes por segundo. Es por eso que solo usaríamos la versión gratuita de Burp Intruder para consultas cortas. El `Pro` la versión tiene una velocidad ilimitada, que puede rivalizar con las herramientas comunes de fuzzing web, además de las características muy útiles de Burp Intruder. Esto lo convierte en una de las mejores herramientas de fuzzing web y fuerza bruta.

En esta sección, demostraremos los diversos usos de Burp Intruder para el fuzzing web y la enumeración.

---

## Objetivo

Como de costumbre, iniciaremos Burp y su navegador preconfigurado y luego visitaremos la aplicación web desde el ejercicio al final de esta sección. Una vez que lo hagamos, podemos ir al Historial de Proxy, localizar nuestra solicitud, luego hacer clic derecho en la solicitud y seleccionar `Send to Intruder`, o use el acceso directo [`CTRL+I`] para enviarlo a `Intruder`.

Entonces podemos ir a `Intruder` haciendo clic en su pestaña o con el acceso directo [`CTRL+SHIFT+I`], lo que nos lleva directamente a `Burp Intruder`:

![intruso_objetivo](https://academy.hackthebox.com/storage/modules/110/burp_intruder_target.jpg)

En la primera pestaña, '`Target`', vemos los detalles del objetivo que estaremos borrando, que se alimenta de la solicitud que enviamos `Intruder`.

---

## Posiciones

La segunda pestaña, '`Positions`', es donde colocamos el puntero de posición de carga útil, que es el punto donde se colocarán e iterarán las palabras de nuestra lista de palabras. Vamos a demostrar cómo fuzz directorios web, que es similar a lo que se hace por herramientas como `ffuf` o `gobuster`.

Para comprobar si existe un directorio web, nuestro fuzzing debe estar en '`GET /DIRECTORY/`', de modo que las páginas existentes regresen `200 OK`de lo contrario, lo conseguiríamos `404 NOT FOUND`. Entonces, tendremos que seleccionar `DIRECTORY` como la posición de carga útil, envolviéndola con `§` o seleccionando la palabra `DIRECTORY` y haciendo clic en el `Add §` botón:

![intruso_posición](https://academy.hackthebox.com/storage/modules/110/burp_intruder_position.jpg)

Consejo: el `DIRECTORY` en este caso está el nombre del puntero, que puede ser cualquier cosa, y se puede usar para referirse a cada puntero, en caso de que estemos usando más que posición con diferentes listas de palabras para cada uno.

Lo último a seleccionar en la pestaña de destino es el `Attack Type`. El tipo de ataque define cuántos punteros de carga útil se utilizan y determina qué carga útil se asigna a qué posición. Por simplicidad, nos apegaremos al primer tipo `Sniper`, que utiliza solo una posición. Intenta hacer clic en el `?` en la parte superior de la ventana para leer más sobre los tipos de ataque, o mira esto [enlace](https://portswigger.net/burp/documentation/desktop/tools/intruder/positions#attack-type).

Nota: Asegúrese de dejar las dos líneas adicionales al final de la solicitud, de lo contrario podemos obtener una respuesta de error del servidor.

---

## Cargas útiles

En la tercera pestaña, '`Payloads`', podemos elegir y personalizar nuestras cargas útiles/listas de palabras. Esta carga útil/lista de palabras es lo que se repetiría, y cada elemento/línea de la misma se colocaría y probaría uno por uno en la Posición de carga útil que elegimos anteriormente. Hay cuatro cosas principales que debemos configurar:

- Conjuntos de Carga Útil
- Opciones de carga útil
- Procesamiento de carga útil
- Codificación de carga útil

#### Conjuntos de Carga Útil

Lo primero que debemos configurar es el `Payload Set`. El conjunto de carga útil identifica el número de carga útil, dependiendo del tipo de ataque y el número de cargas útiles que utilizamos en los Punteros de Posición de carga útil:

![Conjuntos de Carga Útil](https://academy.hackthebox.com/storage/modules/110/burp_intruder_payload_set.jpg)

En este caso, solo tenemos un Conjunto de carga útil, ya que elegimos el '`Sniper`' Tipo de ataque con una sola posición de carga útil. Si hemos elegido el '`Cluster Bomb`' tipo de ataque, por ejemplo, y agregó varias posiciones de carga útil, obtendríamos más conjuntos de carga útil para elegir y elegir diferentes opciones para cada uno. En nuestro caso, seleccionaremos `1` para el conjunto de carga útil.

A continuación, tenemos que seleccionar el `Payload Type`, que es el tipo de cargas útiles/listas de palabras que utilizaremos. Burp proporciona una variedad de Tipos de carga útil, cada uno de los cuales actúa de cierta manera. Por ejemplo:

- `Simple List`: El tipo básico y más fundamental. Proporcionamos una lista de palabras, e Intruder itera sobre cada línea en ella.
    
- `Runtime file`: Similar a `Simple List`, pero carga línea por línea a medida que se ejecuta el escaneo para evitar el uso excesivo de memoria por parte de Burp.
    
- `Character Substitution`: Nos permite especificar una lista de caracteres y sus reemplazos, y Burp Intruder intenta todas las permutaciones potenciales.
    

Hay muchos otros tipos de carga útil, cada uno con sus propias opciones, y muchos de los cuales pueden crear listas de palabras personalizadas para cada ataque. Intenta hacer clic en el `?` al lado de `Payload Sets`, y luego haga clic en `Payload Type`, para obtener más información sobre cada Tipo de carga útil. En nuestro caso, iremos con un básico `Simple List`.

#### Opciones de carga útil

A continuación, debemos especificar las Opciones de Carga Útil, que son diferentes para cada Tipo de Carga Útil que seleccionamos `Payload Sets`. Para un `Simple List`, tenemos que crear o cargar una lista de palabras. Para hacerlo, podemos ingresar cada elemento manualmente haciendo clic `Add`, que construiría nuestra lista de palabras sobre la marcha. La otra opción más común es hacer clic en `Load`, y luego seleccione un archivo para cargar en Burp Intruder.

Seleccionaremos `/opt/useful/seclists/Discovery/Web-Content/common.txt` como nuestra lista de palabras. Podemos ver que Burp Intruder carga todas las líneas de nuestra lista de palabras en la tabla Opciones de carga útil:

![Opciones de carga útil](https://academy.hackthebox.com/storage/modules/110/burp_intruder_payload_wordlist.jpg)

Podemos agregar otra lista de palabras o agregar manualmente algunos elementos, y se agregarían a la misma lista de elementos. Podemos usar esto para combinar varias listas de palabras o crear listas de palabras personalizadas. En Burp Pro, también podemos seleccionar de una lista de listas de palabras existentes contenidas en Burp eligiendo entre `Add from list` opción de menú.

Consejo: En caso de que quieras usar una lista de palabras muy grande, es mejor usarla `Runtime file` como el Tipo de carga útil en lugar de `Simple List`, para que Burp Intruder no tenga que cargar toda la lista de palabras con anticipación, lo que puede acelerar el uso de la memoria.

#### Procesamiento de carga útil

Otra opción que podemos aplicar es `Payload Processing`, lo que nos permite determinar las reglas de borrado sobre la lista de palabras cargada. Por ejemplo, si queríamos agregar una extensión después de nuestro elemento de carga útil, o si queríamos filtrar la lista de palabras según criterios específicos, podemos hacerlo con el procesamiento de carga útil.

Intentemos agregar una regla que omita cualquier línea que comience con un `.` (como se muestra en la captura de pantalla de la lista de palabras anterior). Podemos hacerlo haciendo clic en el `Add` botón y luego seleccionando `Skip if matches regex`, lo que nos permite proporcionar un patrón regex para los elementos que queremos omitir. Luego, podemos proporcionar un patrón regex que coincida con las líneas que comienzan con `.`, que es: `^\..*$`:

![procesamiento de carga útil](https://academy.hackthebox.com/storage/modules/110/burp_intruder_payload_processing_1.jpg)

Podemos ver que nuestra regla se agrega y habilita: ![procesamiento de carga útil](https://academy.hackthebox.com/storage/modules/110/burp_intruder_payload_processing_2.jpg)

#### Codificación de carga útil

La cuarta y última opción que podemos aplicar es `Payload Encoding`, lo que nos permite habilitar o deshabilitar la codificación de URL de carga útil.

![codificación de carga útil](https://academy.hackthebox.com/storage/modules/110/burp_intruder_payload_encoding.jpg)

Lo dejaremos habilitado.

---

## Opciones

Finalmente, podemos personalizar nuestras opciones de ataque desde el `Options` pestaña. Hay muchas opciones que podemos personalizar (o dejar por defecto) para nuestro ataque. Por ejemplo, podemos establecer el número de `retried on failure` y `pause before retry` a 0.

Otra opción útil es la `Grep - Match`, lo que nos permite marcar solicitudes específicas dependiendo de sus respuestas. Como estamos difuminando directorios web, solo estamos interesados en respuestas con código HTTP `200 OK`. Entonces, primero lo habilitaremos y luego haremos clic `Clear` para borrar la lista actual. Después de eso, podemos escribir `200 OK` para hacer coincidir cualquier solicitud con esta cadena y haga clic en `Add` para agregar la nueva regla. Finalmente, también lo desactivaremos `Exclude HTTP Headers`, como lo que estamos buscando está en el encabezado HTTP:

![las opciones coinciden](https://academy.hackthebox.com/storage/modules/110/burp_intruder_options_match.jpg)

También podemos utilizar el `Grep - Extract` opción, que es útil si las respuestas HTTP son largas, y solo estamos interesados en una cierta parte de la respuesta. Por lo tanto, esto nos ayuda a mostrar solo una parte específica de la respuesta. Solo estamos buscando respuestas con código HTTP `200 OK`, independientemente de su contenido, por lo que no optaremos por esta opción.

Prueba otro `Intruder` opciones y use la ayuda de Burp haciendo clic en `?` junto a cada uno para aprender más sobre cada opción.

Nota: También podemos usar el `Resource Pool` pestaña para especificar cuántos recursos de red utilizará Intruder, lo que puede ser útil para ataques muy grandes. Para nuestro ejemplo, lo dejaremos en sus valores predeterminados.

---

## Ataque

Ahora que todo está configurado correctamente, podemos hacer clic en el `Start Attack` botón y espera a que termine nuestro ataque. Una vez más, en el libre `Community Version`estos ataques serían muy lentos y tomarían una cantidad considerable de tiempo para listas de palabras más largas.

Lo primero que notaremos es que todas las líneas comienzan con `.` fueron omitidos, y comenzamos directamente con las líneas después de ellos:

![intrusor_ataque_excluir](https://academy.hackthebox.com/storage/modules/110/burp_intruder_attack_exclude.jpg)

También podemos ver el `200 OK` columna, que muestra las solicitudes que coinciden con el `200 OK` valor de Grep que especificamos en la pestaña Opciones. Podemos hacer clic en él para ordenarlo, de modo que tengamos resultados coincidentes en la parte superior. De lo contrario, podemos ordenar por `status` o por `Length`. Una vez que finaliza nuestro escaneo, vemos que recibimos un golpe `/admin`:

![intruso_ataque](https://academy.hackthebox.com/storage/modules/110/burp_intruder_attack.jpg)

Ahora podemos visitar manualmente la página `<http://SERVER_IP:PORT/admin/>`, para asegurarse de que existe.

Del mismo modo, podemos usar `Burp Intruder` para hacer cualquier tipo de fuzzing web y fuerza bruta, incluyendo fuerza bruta para contraseñas, o fuzzing para ciertos parámetros de PHP, y así sucesivamente. Incluso podemos usar `Intruder` para realizar la pulverización de contraseñas contra aplicaciones que usan autenticación de Active Directory (AD) como Outlook Web Access (OWA), portales SSL VPN, Servicios de Escritorio remoto (RDS), Citrix, aplicaciones web personalizadas que usan autenticación AD y más. Sin embargo, como la versión gratuita de `Intruder` está extremadamente estrangulado, en la siguiente sección, veremos el fuzzer de ZAP y sus diversas opciones, que no tienen un nivel pagado.


# ZAP Fuzzer

---

El fuzzer de ZAP se llama (`ZAP Fuzzer`). Puede ser muy potente para difuminar varios puntos finales web, aunque le faltan algunas de las características proporcionadas por Burp Intruder. ZAP Fuzzer, sin embargo, no acelera la velocidad de fuzzing, lo que lo hace mucho más útil que el Intruder gratuito de Burp.

En esta sección, intentaremos replicar lo que hicimos en la sección anterior usando ZAP Fuzzer para tener una comparación de "manzanas a manzanas" y decidir cuál nos gusta más.

---

## Fuzz

Para comenzar nuestro fuzzing, visitaremos la URL del ejercicio al final de esta sección para capturar una solicitud de muestra. Como estaremos fuzzing para directorios, vamos a visitar `<http://SERVER_IP:PORT/test/>` para colocar nuestra ubicación fuzzing en `test` más tarde. Una vez que localicemos nuestra solicitud en el historial de proxy, haremos clic derecho sobre ella y seleccionaremos (`Attack>Fuzz`), que abrirá el `Fuzzer` ventana:

![procesamiento de carga útil](https://academy.hackthebox.com/storage/modules/110/zap_fuzzer.jpg)

Las principales opciones que necesitamos configurar para nuestro ataque Fuzzer son:

- Ubicación de Fuzz
- Cargas útiles
- Procesadores
- Opciones

Intentemos configurarlos para nuestro ataque de fuzzing de directorio web.

---

## Ubicaciones

El `Fuzz Location` es muy similar a `Intruder Payload Position`, donde se colocarán nuestras cargas útiles. Para colocar nuestra ubicación en una palabra determinada, podemos seleccionarla y hacer clic en la `Add` botón en el panel derecho. Entonces, seleccionemos `test` y haga clic en `Add`:

![procesamiento de carga útil](https://academy.hackthebox.com/storage/modules/110/zap_fuzzer_add.jpg)

Como podemos ver, esto colocó un `green` marcador en nuestra ubicación seleccionada y abrió el `Payloads` ventana para que configuremos nuestras cargas útiles de ataque.

---

## Cargas útiles

Las cargas útiles de ataque en Fuzzer de ZAP son similares en concepto a las cargas útiles de Intruder, aunque no son tan avanzadas como las de Intruder. Podemos hacer clic en el `Add` botón para agregar nuestras cargas útiles y seleccionar entre 8 tipos diferentes de carga útil. Los siguientes son algunos de ellos:

- `File`: Esto nos permite seleccionar una lista de palabras de carga útil de un archivo.
- `File Fuzzers`: Esto nos permite seleccionar listas de palabras de bases de datos integradas de listas de palabras.
- `Numberzz`: Genera secuencias de números con incrementos personalizados.

Una de las ventajas de ZAP Fuzzer es tener listas de palabras integradas entre las que podemos elegir para que no tengamos que proporcionar nuestra propia lista de palabras. Se pueden instalar más bases de datos desde el ZAP Marketplace, como veremos en una sección posterior. Entonces, podemos seleccionar `File Fuzzers` como el `Type`y luego seleccionaremos la primera lista de palabras de `dirbuster`:

![procesamiento de carga útil](https://academy.hackthebox.com/storage/modules/110/zap_fuzzer_add_payload.jpg)

Una vez que hacemos clic en el `Add` botón, nuestra lista de palabras de carga útil se agregará, y podemos examinarla con el `Modify` botón.

---

## Procesadores

También es posible que deseemos realizar algún procesamiento en cada palabra en nuestra lista de palabras de carga útil. Los siguientes son algunos de los procesadores de carga útil que podemos utilizar:

- Base64 Decodificar/Encodificar
- Hash MD5
- Cadena Postfix
- Cuerda Prefijo
- SHA-1/256/512 Hash
- decodificación URL/Encode
- Guión

Como podemos ver, tenemos una variedad de codificadores y algoritmos de hash para seleccionar. También podemos agregar una cadena personalizada antes de la carga útil con `Prefix String` o una cadena personalizada con `Postfix String`. Finalmente, el `Script` type nos permite seleccionar un script personalizado que construimos y ejecutamos en cada carga útil antes de usarlo en el ataque.

Seleccionaremos el `URL Encode` procesador para nuestro ejercicio para garantizar que nuestra carga útil se codifique correctamente y evitar errores del servidor si nuestra carga útil contiene caracteres especiales. Podemos hacer clic en el `Generate Preview` botón para obtener una vista previa de cómo se verá nuestra carga útil final en la solicitud:

![procesamiento de carga útil](https://academy.hackthebox.com/storage/modules/110/zap_fuzzer_add_processor.jpg)

Una vez hecho esto, podemos hacer clic en `Add` para agregar el procesador y hacer clic en `Ok` en las ventanas de procesadores y cargas útiles para cerrarlas.

---

## Opciones

Finalmente, podemos establecer algunas opciones para nuestros fuzzers, similar a lo que hicimos con Burp Intruder. Por ejemplo, podemos establecer el `Concurrent threads per scan` a `20`, entonces nuestro escaneo se ejecuta muy rápidamente:

![procesamiento de carga útil](https://academy.hackthebox.com/storage/modules/110/zap_fuzzer_options.jpg)

El número de subprocesos que establecemos puede estar limitado por la cantidad de potencia de procesamiento de la computadora que queremos usar o la cantidad de conexiones que el servidor nos permite establecer.

También podemos optar por ejecutar las cargas útiles `Depth first`, que intentaría todas las palabras de la lista de palabras en una sola posición de carga útil antes de pasar a la siguiente (por ejemplo, pruebe todas las contraseñas para un solo usuario antes de forzar brutamente al siguiente usuario). También podríamos usar `Breadth first`, que ejecutaría cada palabra de la lista de palabras en todas las posiciones de carga útil antes de pasar a la siguiente palabra (por ejemplo, intente cada contraseña para todos los usuarios antes de pasar a la siguiente contraseña).

---

## Empezar

Con todas nuestras opciones configuradas, finalmente podemos hacer clic en el `Start Fuzzer` botón para iniciar nuestro ataque. Una vez que se inicia nuestro ataque, podemos ordenar los resultados por el `Response` código, ya que solo estamos interesados en respuestas con código `200`:

![procesamiento de carga útil](https://academy.hackthebox.com/storage/modules/110/zap_fuzzer_attack.jpg)

Como podemos ver, tenemos un éxito con el código `200` con el `skills` carga útil, lo que significa que el `/skills/` el directorio existe en el servidor y es accesible. Podemos hacer clic en la solicitud en la ventana de resultados para ver sus detalles: ![procesamiento de carga útil](https://academy.hackthebox.com/storage/modules/110/zap_fuzzer_dir.jpg)

Podemos ver en la respuesta que esta página es realmente accesible por nosotros. Hay otros campos que pueden indicar un golpe exitoso dependiendo del escenario de ataque, como `Size Resp. Body` lo que puede indicar que obtuvimos una página diferente si su tamaño era diferente a otras respuestas, o `RTT` para ataques como `time-based SQL injections`, que se detectan por un retardo de tiempo en la respuesta del servidor.


# Escáner Burp

---

Una característica esencial de las herramientas de proxy web son sus escáneres web. Burp Suite viene con `Burp Scanner`, un potente escáner para varios tipos de vulnerabilidades web, utilizando un `Crawler` para construir la estructura del sitio web, y `Scanner` para escaneo pasivo y activo.

Burp Scanner es una función Pro-Only, y no está disponible en la versión comunitaria gratuita de Burp Suite. Sin embargo, dado el amplio alcance que cubre Burp Scanner y las características avanzadas que incluye, lo convierte en una herramienta de nivel empresarial y, como tal, se espera que sea una característica de pago.

---

## Alcance Objetivo

Para iniciar un escaneo en Burp Suite, tenemos las siguientes opciones:

1. Comience a escanear en una solicitud específica del Historial de Proxy
2. Inicie un nuevo escaneo en un conjunto de objetivos
3. Inicie un escaneo en elementos dentro del alcance

Para iniciar un escaneo en una solicitud específica del Historial de Proxy, podemos hacer clic derecho sobre él una vez que lo ubiquemos en el historial y luego seleccionar `Scan` para poder configurar el escaneo antes de ejecutarlo, o seleccione `Passive/Active Scan` para iniciar rápidamente un escaneo con las configuraciones predeterminadas:

![Solicitud de Escaneo](https://academy.hackthebox.com/storage/modules/110/burp_scan_request.jpg)

También podemos hacer clic en el `New Scan` botón en el `Dashboard` tab, que abriría el `New Scan` ventana de configuración para configurar un escaneo en un conjunto de objetivos personalizados. En lugar de crear un escaneo personalizado desde cero, veamos cómo podemos utilizar el alcance para definir correctamente lo que se incluye/excluye de nuestros escaneos utilizando el `Target Scope`. El `Target Scope` se puede utilizar con todas las funciones de Burp para definir un conjunto personalizado de objetivos que se procesarán. Burp también nos permite limitar Burp a elementos dentro del alcance para ahorrar recursos ignorando cualquier URL fuera del alcance.

Nota: Estaremos escaneando la aplicación web desde el ejercicio que se encuentra al final de la siguiente sección. Si obtiene una licencia para usar Burp Pro, puede generar el objetivo al final de la siguiente sección y seguir aquí.

Si vamos a (`Target>Site map`), mostrará una lista de todos los directorios y archivos que eructo ha detectado en varias solicitudes que pasaron por su proxy:

![Mapa del Sitio](https://academy.hackthebox.com/storage/modules/110/burp_site_map_before.jpg)

Para agregar un elemento a nuestro alcance, podemos hacer clic derecho sobre él y seleccionar `Add to scope`:

![Agregar a Alcance](https://academy.hackthebox.com/storage/modules/110/burp_add_to_scope.jpg)

Nota: Cuando agregue el primer elemento a su alcance, Burp le dará la opción de restringir sus características solo a elementos dentro del alcance e ignorar cualquier elemento fuera del alcance.

También es posible que debamos excluir algunos elementos del alcance si escanearlos puede ser peligroso o puede finalizar nuestra sesión 'como una función de cierre de sesión'. Para excluir un elemento de nuestro alcance, podemos hacer clic derecho en cualquier elemento del alcance y seleccionar `Remove from scope`. Finalmente, podemos ir a (`Target>Scope`) para ver los detalles de nuestro alcance. Aquí, también podemos agregar/eliminar otros elementos y usar control de alcance avanzado para especificar patrones regex que se incluirán/excluirán.

![Alcance Objetivo](https://academy.hackthebox.com/storage/modules/110/burp_target_scope.jpg)

---

## Rastreador

Una vez que tengamos nuestro alcance listo, podemos ir a la `Dashboard` pestaña y haga clic en `New Scan` para configurar nuestro escaneo, que se rellenaría automáticamente con nuestros elementos de alcance:

![Nuevo Scan](https://academy.hackthebox.com/storage/modules/110/burp_new_scan.jpg)

Vemos que Burp nos da dos opciones de escaneo: `Crawl and Audit` y `Crawl`. Un Web Crawler navega por un sitio web accediendo a cualquier enlace que se encuentre en sus páginas, accediendo a cualquier formulario y examinando cualquier solicitud que realice para crear un mapa completo del sitio web. Al final, Burp Scanner nos presenta un mapa del objetivo, mostrando todos los datos de acceso público en un solo lugar. Si seleccionamos `Crawl and Audit`, Burp ejecutará su escáner después de su Crawler (como veremos más adelante).

Nota: Un escaneo de rastreo solo sigue y mapea los enlaces que se encuentran en la página que especificamos, y cualquier página que se encuentre en ella. No realiza un escaneo fuzzing para identificar páginas a las que nunca se hace referencia, como lo que dirbuster o ffuf harían. Esto se puede hacer con Burp Intruder o Content Discovery, y luego agregarlo al alcance, si es necesario.

Seleccionemos `Crawl` como comienzo y ve al `Scan configuration` pestaña para configurar nuestro escaneo. Desde aquí, podemos optar por hacer clic en `New` para crear una configuración personalizada, lo que nos permitiría establecer configuraciones como la velocidad o el límite de rastreo, si Burp intentará iniciar sesión en cualquier formulario de inicio de sesión y algunas otras configuraciones. En aras de la simplicidad, haremos clic en el `Select from library` botón, que nos da algunas configuraciones preestablecidas que podemos elegir (o configuraciones personalizadas que definimos anteriormente):

![Configurar Crawl](https://academy.hackthebox.com/storage/modules/110/burp_crawl_config.jpg)

Seleccionaremos el `Crawl strategy - fastest` opción y continuar a la `Application login` pestaña. En esta pestaña, podemos agregar un conjunto de credenciales para que Burp intente en cualquier formulario/campos de inicio de sesión que pueda encontrar. También podemos registrar un conjunto de pasos realizando un inicio de sesión manual en el navegador preconfigurado, de modo que Burp sepa qué pasos seguir para obtener una sesión de inicio de sesión. Esto puede ser esencial si estuviéramos ejecutando nuestro escaneo utilizando un usuario autenticado, lo que nos permitiría cubrir partes de la aplicación web a las que Burp podría no tener acceso. Como no tenemos ninguna credencial, la dejaremos vacía.

Con eso, podemos hacer clic en el `Ok` botón para iniciar nuestro escaneo de rastreo. Una vez que comienza nuestro escaneo, podemos ver su progreso en el `Dashboard` pestaña debajo `Tasks`:

![Configurar Crawl](https://academy.hackthebox.com/storage/modules/110/burp_crawl_progress.jpg)

También podemos hacer clic en el `View details` botón en las tareas para ver más detalles sobre el escaneo en ejecución o haga clic en el icono de engranaje para personalizar aún más nuestras configuraciones de escaneo. Finalmente, una vez que se complete nuestro escaneo, veremos `Crawl Finished` en la información de la tarea, y luego podemos volver a (`Target>Site map`) para ver el mapa del sitio actualizado:

![Mapa del Sitio](https://academy.hackthebox.com/storage/modules/110/burp_site_map_after.jpg)

---

## Escáner Pasivo

Ahora que el mapa del sitio está completamente construido, podemos seleccionar escanear este objetivo en busca de posibles vulnerabilidades. Cuando elegimos el `Crawl and Audit` opción en el `New Scan` diálogo, Burp realizará dos tipos de escaneos: A `Passive Vulnerability Scan` y un `Active Vulnerability Scan`.

A diferencia de un Escaneo Activo, un Escaneo Pasivo no envía nuevas solicitudes, sino que analiza la fuente de las páginas ya visitadas en el objetivo/alcance y luego intenta identificar `potential` vulnerabilidades. Esto es muy útil para un análisis rápido de un objetivo específico, como la falta de etiquetas HTML o posibles vulnerabilidades XSS basadas en DOM. Sin embargo, sin enviar ninguna solicitud para probar y verificar estas vulnerabilidades, un Escaneo Pasivo solo puede sugerir una lista de vulnerabilidades potenciales. Aun así, Burp Passive Scanner proporciona un nivel de `Confidence` para cada vulnerabilidad identificada, que también es útil para priorizar vulnerabilidades potenciales.

Comencemos intentando realizar un Escaneo Pasivo solamente. Para hacerlo, podemos seleccionar una vez más el objetivo en (`Target>Site map`) o una solicitud en Burp Proxy History, luego haga clic derecho en él y seleccione `Do passive scan` o `Passively scan this target`. El Escaneo Pasivo comenzará a ejecutarse y su tarea se puede ver en el `Dashboard` tab también. Una vez que finaliza el escaneo, podemos hacer clic en `View Details` para revisar las vulnerabilidades identificadas y luego seleccionar el `Issue activity` pestaña:

![Escaneo Pasivo](https://academy.hackthebox.com/storage/modules/110/burp_passive_scan.jpg)

Alternativamente, podemos ver todos los problemas identificados en el `Issue activity` panel en el `Dashboard` pestaña. Como podemos ver, muestra la lista de vulnerabilidades potenciales, su gravedad y su confianza. Por lo general, queremos buscar vulnerabilidades con `High` gravedad y `Certain` confianza. Sin embargo, debemos incluir todos los niveles de severidad y confianza para aplicaciones web muy sensibles, con un enfoque especial en `High` gravedad y `Confident/Firm` confianza.

---

## Escáner Activo

Finalmente llegamos a la parte más poderosa de Burp Scanner, que es su Active Vulnerability Scanner. Un escaneo activo ejecuta un escaneo más completo que un escaneo pasivo, de la siguiente manera:

1. Comienza ejecutando un Crawl y un web fuzzer (como dirbuster/ffuf) para identificar todas las páginas posibles
    
2. Ejecuta un Escaneo Pasivo en todas las páginas identificadas
    
3. Comprueba cada una de las vulnerabilidades identificadas desde el Escaneo Pasivo y envía solicitudes para verificarlas
    
4. Realiza un análisis de JavaScript para identificar más vulnerabilidades potenciales
    
5. Elimina varios puntos y parámetros de inserción identificados para buscar vulnerabilidades comunes como XSS, Inyección de comandos, Inyección SQL y otras vulnerabilidades web comunes
    

El escáner Burp Active es considerado una de las mejores herramientas en ese campo y se actualiza con frecuencia para buscar vulnerabilidades web recientemente identificadas por el equipo de investigación de Burp.

Podemos iniciar un Escaneo Activo de manera similar a como comenzamos un Escaneo Pasivo seleccionando el `Do active scan` en el menú del botón derecho en una solicitud en Burp Proxy History. Alternativamente, podemos ejecutar un escaneo en nuestro alcance con el `New Scan` botón en el `Dashboard` pestaña, que nos permitiría configurar nuestro escaneo activo. Esta vez, seleccionaremos el `Crawl and Audit` opción, que realizaría todos los puntos anteriores y todo lo que hemos discutido hasta ahora.

También podemos establecer las configuraciones de Crawl (como discutimos anteriormente) y las configuraciones de Auditoría. Las configuraciones de auditoría nos permiten seleccionar qué tipo de vulnerabilidades queremos escanear (defaults to all), donde el escáner intentaría insertar sus cargas útiles, además de muchas otras configuraciones útiles. Una vez más, podemos seleccionar un preajuste de configuración con el `Select from library` botón. Para nuestra prueba, como nos interesa `High` vulnerabilidades que pueden permitirnos obtener control sobre el servidor backend, seleccionaremos el `Audit checks - critical issues only` opción. Finalmente, podemos agregar detalles de inicio de sesión, como vimos anteriormente con las configuraciones de Crawl.

Una vez que seleccionamos nuestras configuraciones, podemos hacer clic en el `Ok` botón para iniciar el escaneo, y la tarea de escaneo activo debe agregarse en el `Tasks` panel en el `Dashboard` pestaña:

![Escaneo Activo](https://academy.hackthebox.com/storage/modules/110/burp_active_scan.jpg)

El escaneo ejecutará todos los pasos mencionados anteriormente, por lo que tomará mucho más tiempo terminar que nuestros escaneos anteriores dependiendo de las configuraciones que seleccionamos. A medida que se ejecuta el escaneo, podemos ver las diversas solicitudes que está haciendo haciendo clic en el `View details` botón y selección del `Logger` pestaña, o yendo a la `Logger` tab en Burp, que muestra todas las solicitudes que pasaron o fueron hechas por Burp:

![Registrador](https://academy.hackthebox.com/storage/modules/110/burp_logger.jpg)

Una vez realizado el escaneo, podemos ver el `Issue activity` panel en el `Dashboard` pestaña para ver y filtrar todos los problemas identificados hasta ahora. Desde el filtro sobre los resultados, seleccionemos `High` y `Certain` y vea nuestros resultados filtrados:

![Altas Vulnerabilidades](https://academy.hackthebox.com/storage/modules/110/burp_high_vulnerabilities.jpg)

Vemos que Burp identificó un `OS command injection` vulnerabilidad, que se clasifica con un `High` gravedad y `Firm` confianza. Como Burp confía firmemente en que existe esta vulnerabilidad grave, podemos leerla haciendo clic en ella y leyendo el aviso mostrado y ver la solicitud enviada y la respuesta recibida, para poder saber si la vulnerabilidad puede ser explotada o cómo representa una amenaza en el servidor web:

![Detalles Vulnerables](https://academy.hackthebox.com/storage/modules/110/burp_vuln_details.jpg)

---

## Reportando

Finalmente, una vez que se completen todos nuestros escaneos y se hayan identificado todos los problemas potenciales, podemos ir a (`Target>Site map`), haga clic derecho en nuestro objetivo y seleccione (`Issue>Report issues for this host`). Se nos pedirá que seleccionemos el tipo de exportación para el informe y qué información nos gustaría incluir en el informe. Una vez exportamos el informe, podemos abrirlo en cualquier navegador web para ver sus detalles:

![Informe de Escaneo](https://academy.hackthebox.com/storage/modules/110/burp_scan_report.jpg)

Como podemos ver, el informe de Burp es muy organizado y se puede personalizar para incluir solo problemas seleccionados por gravedad/confianza. También muestra detalles de prueba de concepto sobre cómo explotar la vulnerabilidad e información sobre cómo remediarla. Estos informes se pueden utilizar como datos complementarios para los informes detallados que preparamos para nuestros clientes o los desarrolladores de aplicaciones web cuando realizamos una prueba de penetración web o se pueden almacenar para nuestra referencia futura. Nunca debemos simplemente exportar un informe de cualquier herramienta de penetración y enviarlo a un cliente como el entregable final. En cambio, los informes y los datos generados por las herramientas pueden ser útiles como datos de apéndice para los clientes que pueden necesitar los datos de escaneo sin procesar para los esfuerzos de remediación o para importarlos a un panel de seguimiento.



# Escáner ZAP

---

ZAP también viene incluido con un Escáner Web similar a Burp Scanner. ZAP Scanner es capaz de construir mapas de sitios utilizando ZAP Spider y realizar escaneos pasivos y activos para buscar varios tipos de vulnerabilidades.

---

## Araña

Empecemos con `ZAP Spider`, que es similar a la función Crawler en Burp. Para iniciar un escaneo Spider en cualquier sitio web, podemos localizar una solicitud de nuestra pestaña Historial y seleccionar (`Attack>Spider`) en el menú del clic derecho. Otra opción es usar el HUD en el navegador preconfigurado. Una vez que visitamos la página o el sitio web en el que queremos iniciar nuestro escaneo Spider, podemos hacer clic en el segundo botón en el panel derecho (`Spider Start`), lo que nos pediría que iniciáramos el escaneo:

![Araña ZAP](https://academy.hackthebox.com/storage/modules/110/zap_spider.jpg)

Nota: Cuando hacemos clic en el botón Spider, ZAP puede decirnos que el sitio web actual no está en nuestro alcance, y nos pedirá que lo agreguemos automáticamente al alcance antes de iniciar el escaneo, a lo que podemos decir 'Sí'. El alcance es el conjunto de URL que ZAP probará si iniciamos un escaneo genérico, y podemos personalizarlo para escanear varios sitios web y URL. Intente agregar múltiples objetivos al alcance para ver cómo se ejecutaría el escaneo de manera diferente.

Nota: En algunas versiones de los navegadores, el HUD del ZAP podría no funcionar según lo previsto.

Una vez que hacemos clic en `Start` en la ventana emergente, nuestro escaneo Spider debería comenzar a arañar el sitio web buscando enlaces y validándolos, muy similar a cómo funciona Burp Crawler. Podemos ver el progreso de la exploración de araña tanto en el HUD en el `Spider` botón o en la interfaz de usuario principal de ZAP, que debe cambiar automáticamente a la pestaña Spider actual para mostrar el progreso y las solicitudes enviadas. Cuando se complete nuestro escaneo, podemos verificar la pestaña Sitios en la interfaz de usuario principal de ZAP, o podemos hacer clic en el primer botón en el panel derecho (`Sites Tree`), que debería mostrarnos una vista ampliable de la lista de árboles de todos los sitios web identificados y sus subdirectorios: ![Araña ZAP](https://academy.hackthebox.com/storage/modules/110/zap_sites.jpg)

Consejo: ZAP también tiene un tipo diferente de araña llamada `Ajax Spider`, que se puede iniciar desde el tercer botón en el panel derecho. La diferencia entre esto y el escáner normal es que Ajax Spider también intenta identificar los enlaces solicitados a través de las solicitudes AJAX de JavaScript, que pueden estar ejecutándose en la página incluso después de que se cargue. Intente ejecutarlo después de que el Spider normal termine su escaneo, ya que esto puede dar una mejor salida y agregar algunos enlaces que el Spider normal puede haber perdido, aunque puede tardar un poco más en terminar.

---

## Escáner Pasivo

A medida que ZAP Spider ejecuta y realiza solicitudes a varios puntos finales, ejecuta automáticamente su escáner pasivo en cada respuesta para ver si puede identificar posibles problemas desde el código fuente, como la falta de encabezados de seguridad o vulnerabilidades XSS basadas en DOM. Esta es la razón por la que incluso antes de ejecutar Active Scanner, podemos ver que el botón de alertas comienza a llenarse con algunos problemas identificados. Las alertas en el panel izquierdo nos muestran los problemas identificados en la página actual que estamos visitando, mientras que el panel derecho nos muestra las alertas generales en esta aplicación web, que incluye alertas encontradas en otras páginas:

![Araña ZAP](https://academy.hackthebox.com/storage/modules/110/zap_alerts.jpg)

También podemos comprobar el `Alerts` pestaña en la interfaz de usuario principal de ZAP para ver todos los problemas identificados. Si hacemos clic en cualquier alerta, ZAP nos mostrará sus detalles y las páginas en las que se encontró:

![Araña ZAP](https://academy.hackthebox.com/storage/modules/110/zap_site_alerts.jpg)

---

## Escáner Activo

Una vez que el árbol de nuestro sitio está poblado, podemos hacer clic en el `Active Scan` botón en el panel derecho para iniciar un escaneo activo en todas las páginas identificadas. Si aún no hemos ejecutado un Spider Scan en la aplicación web, ZAP lo ejecutará automáticamente para construir un árbol de sitio como un objetivo de escaneo. Una vez que se inicia el Active Scan, podemos ver su progreso de manera similar a como lo hicimos con el Spider Scan:

![Araña ZAP](https://academy.hackthebox.com/storage/modules/110/zap_active_scan.jpg)

Active Scanner probará varios tipos de ataques contra todas las páginas identificadas y parámetros HTTP para identificar tantas vulnerabilidades como sea posible. Esta es la razón por la cual el Active Scanner tardará más en completarse. A medida que se ejecuta Active Scan, veremos que el botón de alertas comienza a llenarse con más alertas a medida que ZAP descubre más problemas. Además, podemos consultar la interfaz de usuario principal de ZAP para obtener más detalles sobre el escaneo en ejecución y podemos ver las diversas solicitudes enviadas por ZAP:

![Araña ZAP](https://academy.hackthebox.com/storage/modules/110/zap_active_scan_progress.jpg)

Una vez que finaliza el Active Scan, podemos ver las alertas para ver cuáles hacer un seguimiento. Si bien todas las alertas deben informarse y tenerse en cuenta, el `High` las alertas son las que generalmente llevan a comprometer directamente la aplicación web o el servidor de back-end. Si hacemos clic en el `High Alerts` botón, nos mostrará la Alerta Alta identificada: ![Araña ZAP](https://academy.hackthebox.com/storage/modules/110/zap_high_alert.jpg)

También podemos hacer clic en él para ver más detalles al respecto y ver cómo podemos replicar y parchear esta vulnerabilidad:

![Araña ZAP](https://academy.hackthebox.com/storage/modules/110/zap_alert_details.jpg)

En la ventana de detalles de alerta, también podemos hacer clic en la URL para ver los detalles de solicitud y respuesta que ZAP utilizó para identificar esta vulnerabilidad, y también podemos repetir la solicitud a través de ZAP HUD o ZAP Request Editor:

![Araña ZAP](https://academy.hackthebox.com/storage/modules/110/zap_alert_evidence.jpg)

---

## Reportando

Finalmente, podemos generar un informe con todos los hallazgos identificados por ZAP a través de sus diversos escaneos. Para hacerlo, podemos seleccionar (`Report>Generate HTML Report`) desde la barra superior, lo que nos pediría la ubicación de guardado para guardar el informe. También podemos exportar el informe en otros formatos como `XML` o `Markdown`. Una vez que generamos nuestro informe, podemos abrirlo en cualquier navegador para verlo:

![Araña ZAP](https://academy.hackthebox.com/storage/modules/110/zap_report.jpg)

Como podemos ver, el informe muestra todos los detalles identificados de manera organizada, lo que puede ser útil para mantener como un registro para varias aplicaciones web en las que ejecutamos nuestros escaneos durante una prueba de penetración.



# Extensiones

---

Tanto Burp como ZAP tienen capacidades de extensión, de modo que la comunidad de usuarios de Burp puede desarrollar extensiones para Burp para que todos las usen. Dichas extensiones pueden realizar acciones específicas en cualquier solicitud capturada, por ejemplo, o agregar nuevas características, como decodificar y embellecer el código. Burp permite la extensibilidad a través de su `Extender` característica y su [Tienda BApp](https://portswigger.net/bappstore), mientras que ZAP tiene su [Mercado ZAP](https://www.zaproxy.org/addons/) para instalar nuevos plugins.

---

## Tienda BApp

Para encontrar todas las extensiones disponibles, podemos hacer clic en el `Extender` pestaña dentro de Burp y seleccione el `BApp Store` subpestaña. Una vez que hagamos esto, veremos una gran cantidad de extensiones. Podemos ordenarlas por `Popularity` para que sepamos cuáles usuarios encuentran más útiles:

![Tienda BApp](https://academy.hackthebox.com/storage/modules/110/burp_bapp_store.jpg)

Nota: Algunas extensiones son solo para usuarios Pro, mientras que la mayoría de las demás están disponibles para todos.

Vemos muchas extensiones útiles, nos tomamos un tiempo para revisarlas y ver cuáles son las más útiles para usted, y luego intentamos instalarlas y probarlas. Intentemos instalar el `Decoder Improved` extensión:

![Extensión Burp](https://academy.hackthebox.com/storage/modules/110/burp_extension.jpg)

Nota: Algunas extensiones tienen requisitos que no suelen instalarse en Linux/macOS/Windows de forma predeterminada, como 'pythonal, por lo que hay que instalarlas antes de poder instalar la extensión.

Una vez que instalamos `Decoder Improved`, veremos su nueva pestaña agregada a Burp. Cada extensión tiene un uso diferente, por lo que podemos hacer clic en la documentación de cualquier extensión en `BApp Store` para leer más sobre él o visite su página de GitHub para obtener más información sobre su uso. Podemos usar esta extensión tal como usaríamos Burp's Decoder, con el beneficio de tener muchos codificadores adicionales incluidos. Por ejemplo, podemos ingresar texto con el que queremos ser hash `MD5`, y seleccione `Hash With>MD5`:

![Decodificador Mejorado](https://academy.hackthebox.com/storage/modules/110/burp_extension_decoder_improved.jpg)

Del mismo modo, podemos realizar otros tipos de codificación y hash. Hay muchas otras extensiones Burp que se pueden utilizar para ampliar aún más la funcionalidad de Burp.

Algunas extensiones que vale la pena revisar incluyen, pero no se limitan a:

||||
|---|---|---|
|.EMbellecedor NET|J2EEScan|Escáner de Vulnerabilidad de Software|
|Reportero de Versión de Software|Scan++ Activo|Cheques de Escáner Adicionales|
|controles de seguridad de AWS|Escáner Alimentado Backslash|Wsdler|
|Escáner de Deserialización Java|C02|Probador de Almacenamiento en la Nube|
|escáner CMS|Error Comprobaciones de Mensaje|Detectar JS Dinámico|
|Analizador de Encabezados|Auditor HTML5|Comprobación de Inyección de Objetos PHP|
|Seguridad JavaScript|Júbilo.JS|Auditor CSP|
|Encabezado de Dirección IP Aleatoria|Autorizar|escáner CSRF|
|Buscador de Enlaces JS|||

---

## Mercado ZAP

ZAP también tiene su propia característica de extensibilidad con el `Marketplace` eso nos permite instalar varios tipos de complementos desarrollados por la comunidad. Para acceder al mercado de ZAP, podemos hacer clic en el `Manage Add-ons` botón y luego seleccione el `Marketplace` pestaña:

![Botón del Mercado](https://academy.hackthebox.com/storage/modules/110/zap_marketplace_button.jpg)

En esta pestaña, podemos ver los diferentes complementos disponibles para ZAP. Algunos complementos pueden estar en su `Release` construir, lo que significa que deben ser estables para ser utilizados, mientras que otros están en su `Beta/Alpha` construye, lo que significa que pueden experimentar algunos problemas en su uso. Intentemos instalar el `FuzzDB Files` y `FuzzDB Offensive` complementos, que añade nuevas listas de palabras para ser utilizados en el fuzzer de ZAP: ![Instalar FuzzDB](https://academy.hackthebox.com/storage/modules/110/zap_fuzzdb_install.jpg)

Ahora, tendremos la opción de elegir entre las diversas listas de palabras y cargas útiles proporcionadas por FuzzDB al realizar un ataque. Por ejemplo, supongamos que debíamos realizar un ataque fuzzing de Inyección de Comando en uno de los ejercicios que usamos anteriormente en este módulo. En ese caso, veremos que tenemos más opciones en el `File Fuzzers` listas de palabras, incluida una lista de palabras de Inyección de Comando OS en (`fuzzdb>attack>os-cmd-execution`), que sería perfecto para este ataque:

![CMD Exec FuzzDB](https://academy.hackthebox.com/storage/modules/110/zap_fuzzdb_cmd_exec.jpg)

Ahora, si ejecutamos el fuzzer en nuestro ejercicio usando la lista de palabras anterior, veremos que fue capaz de explotarlo de varias maneras, lo que sería muy útil si estuviéramos tratando con una aplicación web protegida por WAF:

![CMD Exec FuzzDB](https://academy.hackthebox.com/storage/modules/110/zap_fuzzer_cmd_inj.jpg)

Intente repetir lo anterior con el primer ejercicio en este módulo para ver cómo los complementos pueden ayudar a facilitar su prueba de penetración.

---

## Pensamientos Cerradores

A lo largo de este módulo, hemos demostrado la potencia de los proxies de Burp Suite y ZAP y analizado las diferencias y similitudes entre las versiones gratuitas y profesionales de Burp y el proxy ZAP gratuito y de código abierto. Estas herramientas son esenciales para los probadores de penetración centrados en las evaluaciones de seguridad de aplicaciones web, pero tienen muchas aplicaciones para todos los profesionales de seguridad ofensivos, así como para los profesionales y desarrolladores del equipo azul. Después de trabajar a través de cada uno de los ejemplos y ejercicios en este módulo, intente algunos cuadros centrados en ataques web en la plataforma principal Hack The Box y otros módulos relacionados con la seguridad de aplicaciones web dentro de HTB Academy para fortalecer sus habilidades en torno a estas dos herramientas. Son imprescindibles en su caja de herramientas junto con Nmap, Hashcat, Wireshark, tcpdump, sqlmap, Ffuf, Gobuster, etc.



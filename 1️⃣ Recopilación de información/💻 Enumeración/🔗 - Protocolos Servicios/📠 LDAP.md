---
tags:
  - Enumeración
---

`LDAP` (Lightweight Directory Access Protocol) es `a protocol` usado para `access and manage directory information`. A `directory` es a `hierarchical data store` que contiene información sobre recursos de red como `users`, `groups`, `computers`, `printers`, y otros dispositivos. LDAP proporciona una excelente funcionalidad:

| **Funcionalidad**         | **Descripción**                                                                                                                                                                                                                  |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Efficient`               | Consultas y conexiones eficientes y rápidas a servicios de directorio, gracias a su lenguaje de consulta lean y almacenamiento de datos no normalizado.                                                                          |
| `Global naming model`     | Admite múltiples directorios independientes con un modelo de nomenclatura global que garantiza entradas únicas.                                                                                                                  |
| `Extensible and flexible` | Esto ayuda a cumplir con los requisitos futuros y locales al permitir atributos y esquemas personalizados.                                                                                                                       |
| `Compatibility`           | Es compatible con muchos productos y plataformas de software, ya que se ejecuta directamente sobre TCP/IP y SSL, y lo es `platform-independent`, adecuado para su uso en entornos heterogéneos con diversos sistemas operativos. |
| `Authentication`          | Proporciona `authentication` mecanismos que permiten a los usuarios `sign on once` y acceda a múltiples recursos en el servidor de forma segura.                                                                                 |

Sin embargo, también sufre algunos problemas importantes:

|Funcionalidad|Descripción|
|---|---|
|`Compliance`|Servidores de directorio `must be LDAP compliant` para que se implemente el servicio, que puede `limit the choice` de vendedores y productos.|
|`Complexity`|`Difficult to use and understand`para muchos desarrolladores y administradores, que pueden no saber cómo configurar los clientes LDAP correctamente o usarlo de forma segura.|
|`Encryption`|LDAP `does not encrypt its traffic by default`, que expone datos sensibles a posibles escuchas y manipulaciones. LDAPS (LDAP sobre SSL) o StartTLS deben usarse para habilitar el cifrado.|
|`Injection`|`Vulnerable to LDAP injection attacks`, donde los usuarios maliciosos pueden manipular consultas LDAP y `gain unauthorised access` a datos o recursos. Para evitar tales ataques, se deben implementar la validación de entrada y la codificación de salida.|

LDAP es `commonly used` para proporcionar un `central location` para `accessing` y `managing` servicios de directorio. Los servicios de directorio son colecciones de información sobre la organización, sus usuarios y activos, como nombres de usuario y contraseñas. LDAP permite a las organizaciones almacenar, administrar y proteger esta información de forma estandarizada. Aquí hay algunos casos de uso comunes:

|**Caso de Uso**|**Descripción**|
|---|---|
|`Authentication`|LDAP se puede utilizar para `central authentication`, permitiendo a los usuarios tener credenciales de inicio de sesión únicas en múltiples aplicaciones y sistemas. Este es uno de los casos de uso más comunes para LDAP.|
|`Authorisation`|LDAP puede `manage permissions` y `access control` para recursos de red como carpetas o archivos en un recurso compartido de red. Sin embargo, esto puede requerir una configuración o integración adicional con protocolos como Kerberos.|
|`Directory Services`|LDAP proporciona una manera de `search`, `retrieve`, y `modify data` almacenado en un directorio, lo que lo hace útil para administrar un gran número de usuarios y dispositivos en una red corporativa. `LDAP is based on the X.500 standard` para servicios de directorio.|
|`Synchronisation`|LDAP se puede utilizar para `keep data consistent` a través de múltiples sistemas por `replicating changes` hecho en un directorio a otro.|

Hay dos implementaciones populares de LDAP: `OpenLDAP`, un software de código abierto ampliamente utilizado y compatible, y `Microsoft Active Directory`, una implementación basada en Windows que se integra perfectamente con otros productos y servicios de Microsoft.

Aunque LDAP y AD son `related`, ellos `serve different purposes`. `LDAP` es a `protocol` eso especifica el método de acceso y modificación de los servicios de directorio, mientras que `AD` es a `directory service` que almacena y gestiona datos de usuarios e informáticos. Si bien LDAP puede comunicarse con AD y otros servicios de directorio, no es un servicio de directorio en sí. AD ofrece funcionalidades adicionales como administración de políticas, inicio de sesión único e integración con varios productos de Microsoft.

|**LDAP**|**Active Directory (AD)**|
|---|---|
|A `protocol` eso define cómo los clientes y servidores se comunican entre sí para acceder y manipular los datos almacenados en un servicio de directorio.|A `directory server` que utiliza LDAP como uno de sus protocolos para proporcionar autenticación, autorización y otros servicios para redes basadas en Windows.|
|An `open and cross-platform protocol` que se puede utilizar con diferentes tipos de servidores de directorios y aplicaciones.|`Proprietary software`eso solo funciona con sistemas basados en Windows y requiere componentes adicionales como DNS (Domain Name System) y Kerberos por su funcionalidad.|
|Tiene un `flexible and extensible schema` eso permite que los administradores o desarrolladores definan atributos personalizados y clases de objetos.|Tiene un `predefined schema` esto sigue y amplía el estándar X.500 con clases de objetos adicionales y atributos específicos para entornos de Windows. Las modificaciones deben hacerse con precaución y cuidado.|
|Soporta `multiple authentication mechanisms` tales como enlace simple, SASL, etc.|Es compatible `Kerberos` como su mecanismo de autenticación principal, pero también es compatible con NTLM (NT LAN Manager) y LDAP sobre SSL/TLS para compatibilidad con versiones anteriores.|

LDAP funciona utilizando un `client-server architecture`. Un cliente envía una solicitud LDAP a un servidor, que busca en el servicio de directorio y devuelve una respuesta al cliente. LDAP es un protocolo que es más simple y más eficiente que X.500, en el que se basa. Utiliza un modelo cliente-servidor, donde los clientes envían solicitudes a servidores utilizando mensajes LDAP codificados en ASN.1 (Abstract Syntax Notation One) y transmitidos a través de TCP/IP (Transmission Control Protocol/Protocolo de Internet). Los servidores procesan las solicitudes y envían respuestas utilizando el mismo formato. LDAP admite varias solicitudes, como `bind`, `unbind`, `search`, `compare`, `add`, `delete`, `modify`, etc.

`LDAP requests` son `messages` que los clientes envían a los servidores a `perform operations` sobre los datos almacenados en un servicio de directorio. Una solicitud LDAP se compone de varios componentes:

1. `Session connection`: El cliente se conecta al servidor a través de un puerto LDAP (usly 389 or 636).
2. `Request type`: El cliente especifica la operación que desea realizar, como `bind`, `search`, etc.
3. `Request parameters`: El cliente proporciona información adicional para la solicitud, como el `distinguished name` (DN) de la entrada a acceder o modificar, el alcance y filtro de la consulta de búsqueda, los atributos y valores a añadir o cambiar, etc.
4. `Request ID`: El cliente asigna un identificador único para cada solicitud para que coincida con la respuesta correspondiente del servidor.

Una vez que el servidor recibe la solicitud, la procesa y devuelve un mensaje de respuesta que incluye varios componentes:

1. `Response type`: El servidor indica la operación que se realizó en respuesta a la solicitud.
2. `Result code`: El servidor indica si la operación fue exitosa o no y por qué.
3. `Matched DN:`Si corresponde, el servidor devuelve el DN de la entrada existente más cercana que coincida con la solicitud.
4. `Referral`: El servidor devuelve una URL de otro servidor que puede tener más información sobre la solicitud, si corresponde.
5. `Response data`: El servidor devuelve cualquier dato adicional relacionado con la respuesta, como los atributos y valores de una entrada que se buscó o modificó.

Después de recibir y procesar la respuesta, el cliente se desconecta del puerto LDAP.

#### ldapsearch

Por ejemplo, `ldapsearch` es una utilidad de línea de comandos utilizada para buscar información almacenada en un directorio utilizando el protocolo LDAP. Se usa comúnmente para consultar y recuperar datos de un servicio de directorio LDAP.

```shell-session
vcrdcr@htb[/htb]$ ldapsearch -H ldap://ldap.example.com:389 -D "cn=admin,dc=example,dc=com" -w secret123 -b "ou=people,dc=example,dc=com" "(mail=john.doe@example.com)"

```

Este comando se puede desglosar de la siguiente manera:

- Conéctese al servidor `ldap.example.com` en puerto `389`.
- Encuadernar (authenticate) como `cn=admin,dc=example,dc=com` con contraseña `secret123`.
- Buscar bajo la base DN `ou=people,dc=example,dc=com`.
- Usa el filtro `(mail=john.doe@example.com)` para encontrar entradas que tengan esta dirección de correo electrónico.

El servidor procesaría la solicitud y enviaría una respuesta, que podría verse así:

```ldap
dn: uid=jdoe,ou=people,dc=example,dc=com
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: John Doe
sn: Doe
uid: jdoe
mail: john.doe@example.com

result: 0 Success
```

Esta respuesta incluye las entradas `distinguished name (DN)` eso coincide con los criterios de búsqueda y sus atributos y valores.

---

## LDAP Inyección

`LDAP injection` es un ataque que `exploits web applications that use LDAP` (Lightweight Directory Access Protocol) para la autenticación o el almacenamiento de información del usuario. El atacante puede `inject malicious code` o `characters` en consultas LDAP para alterar el comportamiento de la aplicación, `bypass security measures`, y `access sensitive data` almacenado en el directorio LDAP.

Para probar la inyección LDAP, puede usar valores de entrada que contengan `special characters or operators` eso puede cambiar el significado de la consulta:

|Entrada|Descripción|
|---|---|
|`*`|Un asterisco `*` puede `match any number of characters`.|
|`( )`|Paréntesis `( )` puede `group expressions`.|
|`\|`|Una barra vertical `\|` puede realizar `logical OR`.|
|`&`|Un ampersand `&` puede realizar `logical AND`.|
|`(cn=*)`|Valores de entrada que intentan eludir las comprobaciones de autenticación o autorización inyectando condiciones que `always evaluate to true` se puede utilizar. Por ejemplo, `(cn=*)` o `(objectClass=*)` se puede usar como valores de entrada para campos de nombre de usuario o contraseña.|

Los ataques de inyección LDAP son `similar to SQL injection attacks` pero diríjase al servicio de directorio LDAP en lugar de a una base de datos.

Por ejemplo, supongamos que una aplicación utiliza la siguiente consulta LDAP para autenticar usuarios:

```php
(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))
```

En esta consulta, `$username` y `$password` contiene las credenciales de inicio de sesión del usuario. Un atacante podría inyectar el `*` personaje en el `$username` o `$password` campo para modificar la consulta LDAP y omitir la autenticación.

Si un atacante inyecta el `*` personaje en el `$username` campo, la consulta LDAP coincidirá con cualquier cuenta de usuario con cualquier contraseña. Esto permitiría al atacante obtener acceso a la aplicación con cualquier contraseña, como se muestra a continuación:

```php
$username = "*";
$password = "dummy";
(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))
```

Alternativamente, si un atacante inyecta el `*` personaje en el `$password` campo, la consulta LDAP coincidiría con cualquier cuenta de usuario con cualquier contraseña que contenga la cadena inyectada. Esto permitiría al atacante obtener acceso a la aplicación con cualquier nombre de usuario, como se muestra a continuación:

```php
$username = "dummy";
$password = "*";
(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))
```

Los ataques de inyección de LDAP pueden conducir a `severe consequences`, como `unauthorised access` a información sensible, `elevated privileges`, e incluso `full control over the affected application or server`. Estos ataques también pueden afectar considerablemente la integridad y disponibilidad de los datos, ya que los atacantes pueden `alter or remove data` dentro del servicio de directorio, causando interrupciones en las aplicaciones y servicios que dependen de esos datos.

Para mitigar los riesgos asociados con los ataques de inyección LDAP, es crucial `thoroughly validate` y `sanitize user input` antes de incorporarlo a las consultas LDAP. Este proceso debe implicar `removing LDAP-specific special characters` como `*` y `employing parameterised queries` para garantizar que la entrada del usuario es `treated solely as data`, no código ejecutable.

---

## Enumeración

Enumerar el objetivo nos ayuda a comprender los servicios y los puertos expuestos. An `nmap` el escaneo de servicios es un tipo de técnica de escaneo de red utilizada para identificar y analizar los servicios que se ejecutan en un sistema o red de destino. Al sondear los puertos abiertos y evaluar las respuestas, nmap puede deducir qué servicios están activos y sus respectivas versiones. El escaneo proporciona información valiosa sobre la infraestructura de red del objetivo y las posibles vulnerabilidades y superficies de ataque.

#### nmap

```shell-session
vcrdcr@htb[/htb]$ nmap -p- -sC -sV --open --min-rate=1000 10.129.204.229

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-23 14:43 SAST
Nmap scan report for 10.129.204.229
Host is up (0.18s latency).
Not shown: 65533 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT    STATE SERVICE VERSION
80/tcp  open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Login
389/tcp open  ldap    OpenLDAP 2.2.X - 2.3.X

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 149.73 seconds

```

nmap detecta un `http` servidor que se ejecuta en puerto `80` y un `ldap` servidor que se ejecuta en puerto `389`

#### Inyección

Como `OpenLDAP` se ejecuta en el servidor, es seguro asumir que la aplicación web se ejecuta en el puerto `80` utiliza LDAP para la autenticación.

Intentar iniciar sesión con un carácter comodín (`*`) en los campos de nombre de usuario y contraseña otorga acceso al sistema, de manera efectiva `bypassing any authentication measures that had been implemented`. Esto es un `significant` problema de seguridad, ya que permite a cualquier persona con conocimiento de la vulnerabilidad `gain unauthorised access` al sistema y datos potencialmente sensibles.
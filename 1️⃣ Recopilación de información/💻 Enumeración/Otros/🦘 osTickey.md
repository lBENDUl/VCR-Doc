---
tags:
  - Enumeración
---
## Enumeración

Mirando hacia atrás en nuestro escaneo de EyeWitness de antes, notamos una captura de pantalla de una instancia de osTicket que también muestra que una cookie se llama `OSTSESSID` se estableció al visitar la página.

![Página del Centro de soporte con opciones para abrir un nuevo ticket y verificar el estado del ticket, los detalles del servidor y el código de respuesta 200.](https://academy.hackthebox.com/storage/modules/113/osticket_eyewitness.png)

Además, la mayoría de las instalaciones de osTicket mostrarán el logotipo de osTicket con la frase `powered by` delante de él en el pie de página de la página. El pie de página también puede contener las palabras `Support Ticket System`.

![Página del Centro de soporte con opciones para abrir un nuevo ticket y verificar el estado del ticket, dando la bienvenida a los usuarios al sistema de soporte.](https://academy.hackthebox.com/storage/modules/113/osticket_main.png)

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



## Ataque
Una búsqueda de osTicket en exploit-db muestra varios problemas, incluyendo la inclusión remota de archivos, inyección SQL, carga arbitraria de archivos, XSS, etc osTicket versión 1.14.1 sufre de [CVE-2020-24881](https://nvd.nist.gov/vuln/detail/CVE-2020-24881) que era una vulnerabilidad de la SSRF. Si se explota, este tipo de falla puede aprovecharse para obtener acceso a recursos internos o realizar un escaneo interno de puertos.

Además de las vulnerabilidades relacionadas con las aplicaciones web, los portales de soporte a veces se pueden usar para obtener una dirección de correo electrónico para un dominio de la empresa, que se puede usar para registrarse en otras aplicaciones expuestas que requieren que se envíe una verificación por correo electrónico. Como se mencionó anteriormente en el módulo, esto se ilustra en el cuadro de liberación semanal HTB [Entrega](https://0xdf.gitlab.io/2021/05/22/htb-delivery.html) con un video tutorial [aquí](https://www.youtube.com/watch?v=gbs43E71mFM).

Caminemos a través de un ejemplo rápido, que está relacionado con esto [excelente publicación de blog](https://medium.com/intigriti/how-i-hacked-hundreds-of-companies-through-their-helpdesk-b7680ddc2d4c) que [@ippsec](https://twitter.com/ippsec) también se mencionó una inspiración para su caja Entrega que recomiendo encarecidamente salir después de leer esta sección.

Supongamos que encontramos un servicio expuesto, como el servidor Slack de una empresa o GitLab, que requiere una dirección de correo electrónico válida de la empresa para unirse. Muchas empresas tienen un correo electrónico de soporte como `support@inlanefreight.local`y los correos electrónicos enviados a esto están disponibles en portales de soporte en línea que pueden variar desde Zendesk hasta una herramienta personalizada interna. Además, un portal de soporte puede asignar una dirección de correo electrónico interna temporal a un nuevo ticket para que los usuarios puedan verificar rápidamente su estado.

Si nos encontramos con un portal de atención al cliente durante nuestra evaluación y podemos enviar un nuevo ticket, es posible que podamos obtener una dirección de correo electrónico válida de la empresa.

   

![Página del Centro de soporte para abrir un nuevo ticket con campos para información de contacto, tema de ayuda 'Retroalimentación' y resumen de problemas 'Su sitio es lento'.](https://academy.hackthebox.com/storage/modules/113/new_ticket.png)

Esta es una versión modificada de osTicket como ejemplo, pero podemos ver que se proporcionó una dirección de correo electrónico.

   

![Página de confirmación del Centro de soporte que muestra 'Solicitud de ticket de soporte creada' con ID de ticket 940288 y correo electrónico para obtener información adicional.](https://academy.hackthebox.com/storage/modules/113/ticket_email.png)

Ahora, si iniciamos sesión, podemos ver información sobre el ticket y formas de publicar una respuesta. Si la compañía configuró su software de servicio de asistencia para correlacionar los números de tickets con los correos electrónicos, entonces cualquier correo electrónico enviado al correo electrónico que recibimos al registrarse `940288@inlanefreight.local`, aparecería aquí. Con esta configuración, si podemos encontrar un portal externo como un Wiki, un servicio de chat (Slack, Mattermost, Rocket.chat) o un repositorio de Git como GitLab o Bitbucket, podemos usar este correo electrónico para registrar una cuenta y el portal de soporte de la mesa de ayuda para recibir un correo electrónico de confirmación de registro.

   

![Vista de ticket del Centro de soporte que muestra la ID de ticket 940288, el estado abierto, el problema 'Su sitio es lento' y una sección de respuesta.](https://academy.hackthebox.com/storage/modules/113/ost_tickets.png)

---

### osTicket - Exposición Sensible a Datos

Digamos que estamos en una prueba de penetración externa. Durante nuestro OSINT y la recopilación de información, descubrimos varias credenciales de usuario utilizando la herramienta [Deshacer](http://dehashed.com/) (para nuestros propósitos, los datos de muestra a continuación son ficticios).

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

   

![página de inicio de sesión de OsTicket que requiere correo electrónico o nombre de usuario y contraseña para la autenticación.](https://academy.hackthebox.com/storage/modules/113/osticket_admin.png)

Probemos las credenciales para `jclayton`. Sin suerte. Luego probamos las credenciales para `kgrimes` y no tenga éxito, pero al notar que la página de inicio de sesión también acepta una dirección de correo electrónico, lo intentamos `kevin@inlanefreight.local` ¡y obtenga un inicio de sesión exitoso!

   

![panel de control de OsTicket que muestra la pestaña de tickets abiertos sin resultados y opciones para ver, buscar y crear tickets.](https://academy.hackthebox.com/storage/modules/113/osticket_kevin.png)

El usuario `kevin` parece ser un agente de soporte, pero no tiene tickets abiertos. ¿Quizás ya no están activos? En una empresa ocupada, esperaríamos ver algunos boletos abiertos. Excavando un poco, encontramos un boleto cerrado, una conversación entre un empleado remoto y el agente de soporte.

   

![Admite la conversación de tickets con Charles Smithson solicitando el restablecimiento de contraseña debido a problemas de VPN, Kevin Grimes proporciona instrucciones de restablecimiento y respuestas de seguimiento que confirman la resolución.](https://academy.hackthebox.com/storage/modules/113/osticket_ticket.png)

El empleado afirma que fueron bloqueados de su cuenta VPN y le pide al agente que la restablezca. Luego, el agente le dice al usuario que la contraseña se restableció a la nueva contraseña estándar de unión. El usuario no tiene esta contraseña y le pide al agente que los llame para proporcionarles la contraseña (¡sólida conciencia de seguridad!). Luego, el agente comete un error y envía la contraseña al usuario directamente a través del portal. A partir de aquí, podríamos probar esta contraseña contra el portal VPN expuesto, ya que es posible que el usuario no la haya cambiado.

Además, el agente de soporte afirma que esta es la contraseña estándar dada a los nuevos usuarios y establece la contraseña del usuario en este valor. Hemos estado en muchas organizaciones donde el servicio de asistencia utiliza una contraseña estándar para nuevos usuarios y restablecimientos de contraseñas. A menudo, la política de contraseña de dominio es laxa y no obliga al usuario a cambiar en el siguiente inicio de sesión. Si este es el caso, puede funcionar para otros usuarios. Aunque fuera del alcance de este módulo, en este escenario, valdría la pena usar herramientas como [linkedin2usnombre](https://github.com/initstring/linkedin2username) para crear una lista de usuarios de los empleados de la empresa e intentar un ataque de pulverización de contraseñas contra el punto final de VPN con esta contraseña estándar.

Muchas aplicaciones como osTicket también contienen una libreta de direcciones. También valdría la pena exportar todos los correos electrónicos/nombres de usuario de la libreta de direcciones como parte de nuestra enumeración, ya que también podrían ser útiles en un ataque como la pulverización de contraseñas.

---

### Pensamientos Cerradores

Aunque esta sección mostró algunos escenarios ficticios, se basan en cosas que es probable que veamos en el mundo real. Cuando nos encontramos con portales de soporte (especialmente externos), debemos probar la funcionalidad y ver si podemos hacer cosas como crear un ticket y tener una dirección de correo electrónico legítima de la empresa asignada a nosotros. A partir de ahí, es posible que podamos usar la dirección de correo electrónico para iniciar sesión en otros servicios de la empresa y obtener acceso a datos confidenciales.

Esta sección también muestra los peligros de la reutilización de contraseñas y los tipos de datos que es muy probable que encontremos si podemos obtener acceso a la cola de tickets de soporte de un agente de la mesa de ayuda. Las organizaciones pueden prevenir este tipo de fuga de información tomando algunos pasos relativamente fáciles:

- Limite qué aplicaciones están expuestas externamente
- Hacer cumplir la autenticación multifactor en todos los portales externos
- Brindar capacitación en conciencia de seguridad a todos los empleados y aconsejarles que no usen sus correos electrónicos corporativos para inscribirse en servicios de terceros
- Hacer cumplir una política de contraseña segura en Active Directory y en todas las aplicaciones, sin permitir palabras comunes como variaciones de `welcome`, y `password`, el nombre de la empresa, y las estaciones y meses
- Requerir que un usuario cambie su contraseña después de su inicio de sesión inicial y caduque periódicamente las contraseñas del usuario
 
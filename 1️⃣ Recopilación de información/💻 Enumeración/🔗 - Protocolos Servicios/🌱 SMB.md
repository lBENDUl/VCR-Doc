# SMB

SMB (Server Message Block) es el protocolo de Microsoft para compartir archivos, impresoras y recursos en red. **Samba** es su implementación libre para sistemas Unix/Linux. Es uno de los servicios más frecuentes en redes Windows y uno de los más explotados históricamente (EternalBlue, MS17-010, EternalRed/CVE-2017-7494).

Puerto por defecto: **445/TCP** (SMBv2/v3). Puerto legacy: **139/TCP** (NetBIOS sobre SMB).

---

## Enumeración

### Nmap

```bash
nmap -sV -p 139,445 <IP>
nmap -p 445 --script smb-security-mode <IP>
nmap -p 445 --script smb-enum-shares,smb-enum-users <IP>
nmap -p 445 --script smb-vuln-ms17-010 <IP>
```

### smbmap — ver shares y permisos

```bash
# Sesión anónima
smbmap -H <IP>

# Con credenciales
smbmap -H <IP> -u usuario -p contraseña

# Listar contenido de un share
smbmap -H <IP> -r nombre_share
```

### smbclient — conectarse al share

```bash
# Listar shares (anónimo)
smbclient -L <IP> -N

# Conectarse a un share
smbclient //<IP>/nombre_share -N
smbclient //<IP>/nombre_share -U usuario%contraseña
```

### CrackMapExec — enumeración completa

```bash
# Enumerar shares
crackmapexec smb <IP> -u usuario -p contraseña --shares

# Enumerar usuarios
crackmapexec smb <IP> -u usuario -p contraseña --users

# Comprobar si es vulnerable a MS17-010
crackmapexec smb <IP> -u '' -p '' -M ms17-010
```

---

## Montar share en local

Útil para navegar o analizar el contenido de un share como si fuera un directorio local.

```bash
mount -t cifs //<IP>/nombre_share /mnt/mounted
```

Para desmontarlo:

```bash
umount /mnt/mounted
```

> ⚠️ Lo que se monta son los archivos reales del servidor. Cualquier modificación o borrado afecta al share original.

---

## Autenticación anónima (null session)

```bash
smbclient -L <IP> -N
crackmapexec smb <IP> -u '' -p ''
```

Si el servidor responde con la lista de shares sin pedir credenciales, la sesión nula está habilitada.

---

## Explotación

### CVE-2017-7494 — EternalRed (Samba RCE)

Afecta a Samba 3.5.0 – 4.4.13 / 4.5.9 / 4.6.4. Permite ejecución remota de código si el share es escribible.

Laboratorio de práctica:

```bash
git clone https://github.com/vulhub/vulhub
cd vulhub/samba/CVE-2017-7494
docker-compose up -d
```

---

## Ver también

- [smbmap](../06-herramientas/smbmap.md)
- [smbclient](../06-herramientas/smbclient.md)

# smbmap

smbmap enumera recursos compartidos SMB/Samba y sus permisos de acceso. Es la herramienta ideal para la fase de reconocimiento sobre servicios SMB: muestra qué shares existen, qué nivel de acceso tiene el usuario actual y permite navegar el contenido.

---

## Uso básico

```bash
# Sesión anónima
smbmap -H 192.168.1.10

# Con credenciales
smbmap -H 192.168.1.10 -u usuario -p contraseña

# Especificar dominio
smbmap -H 192.168.1.10 -u usuario -p contraseña -d DOMINIO
```

Salida típica:

```
[+] IP: 192.168.1.10:445
    Disk          Permissions    Comment
    ----          -----------    -------
    ADMIN$        NO ACCESS      Remote Admin
    C$            NO ACCESS      Default share
    IPC$          READ ONLY      IPC Service
    datos         READ, WRITE
```

---

## Listar contenido de un share

```bash
# Listar el contenido del share "datos"
smbmap -H 192.168.1.10 -r datos

# Listar de forma recursiva
smbmap -H 192.168.1.10 -R datos
```

---

## Descargar y subir archivos

```bash
# Descargar archivo
smbmap -H 192.168.1.10 --download "datos\archivo.txt"

# Subir archivo
smbmap -H 192.168.1.10 --upload /tmp/shell.php "datos\shell.php"
```

---

## Ejecutar comandos (si hay permisos)

```bash
smbmap -H 192.168.1.10 -u admin -p contraseña -x "ipconfig"
```

---

## Parámetros principales

| Flag | Descripción |
|---|---|
| `-H` | IP o hostname del servidor |
| `-P` | Puerto (por defecto 445) |
| `-u` | Usuario |
| `-p` | Contraseña |
| `-d` | Dominio |
| `-s` | Share específico a enumerar |
| `-r` | Listar contenido (un nivel) |
| `-R` | Listar contenido recursivo |

---

## Ver también

- [smbclient](smbclient.md) — Interacción con el share (descarga, subida, navegación)
- [SMB](../01-reconocimiento/smb.md) — Protocolo y vectores de explotación

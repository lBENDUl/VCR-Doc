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
# Listar el contenido del share "datos" (un nivel)
smbmap -H 192.168.1.10 -r datos

# Listar de forma recursiva
smbmap -H 192.168.1.10 -R datos
```

---

## Descargar y subir archivos

```bash
# Descargar un archivo concreto
smbmap -H 192.168.1.10 --download "datos\archivo.txt"

# Subir un archivo
smbmap -H 192.168.1.10 --upload /tmp/shell.php "datos\shell.php"
```

---

## Ejecutar comandos remotos

Si el usuario tiene permisos suficientes (admin):

```bash
smbmap -H 192.168.1.10 -u admin -p contraseña -x "whoami"
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
| `--download` | Descargar archivo del share |
| `--upload` | Subir archivo al share |
| `-x` | Ejecutar comando en el sistema remoto |

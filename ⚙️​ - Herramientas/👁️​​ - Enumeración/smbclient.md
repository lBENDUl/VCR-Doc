# smbclient

smbclient es una herramienta de línea de comandos para interactuar con recursos compartidos SMB/Samba. A diferencia de smbmap (orientado a enumeración), smbclient proporciona una interfaz similar a FTP para navegar, descargar y subir archivos.

---

## Enumerar recursos compartidos

```bash
# Sesión anónima (sin credenciales)
smbclient -L 192.168.1.10 -N

# Con credenciales
smbclient -L 192.168.1.10 -U usuario%contraseña
```

---

## Conectarse a un recurso compartido

```bash
# Sesión anónima
smbclient //192.168.1.10/compartido -N

# Con credenciales
smbclient //192.168.1.10/compartido -U usuario%contraseña
```

---

## Comandos dentro de la sesión

```bash
ls                        # Listar archivos
get archivo.txt           # Descargar archivo
put archivo.txt           # Subir archivo
cd carpeta                # Cambiar directorio
mget *.txt                # Descargar múltiples archivos
recurse ON; mget *        # Descarga recursiva
```

---

## Parámetros principales

| Flag | Descripción |
|---|---|
| `-L` | Listar recursos compartidos del servidor |
| `-N` | Sin contraseña (sesión anónima/null) |
| `-U` | Usuario y contraseña (`usuario%pass`) |
| `-c` | Ejecutar comando sin entrar en modo interactivo |

Ejemplo con `-c`:

```bash
smbclient //192.168.1.10/compartido -N -c "ls"
```

---

## Ver también

- [smbmap](smbmap.md) — Enumeración de permisos y recursos
- [SMB](../01-reconocimiento/smb.md) — Protocolo, autenticación y explotación

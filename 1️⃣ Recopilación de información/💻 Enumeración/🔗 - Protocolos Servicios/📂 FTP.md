---
tags:
  - Protocolo
  - Enumeración
---
# 📂 FTP

FTP (File Transfer Protocol) es un protocolo de red para la transferencia de archivos entre cliente y servidor. Habitualmente expuesto en el puerto **21/TCP**. Su principal debilidad es que **transmite credenciales en texto claro**, y muchos servidores permiten acceso anónimo sin autenticación.

---

## Puertos

| Puerto | Descripción |
|---|---|
| 21/TCP | Canal de control (comandos) |
| 20/TCP | Canal de datos (transferencia activa) |
| Dinámico | Canal de datos en modo pasivo |

---

## Enumeración

### Detectar versión con Nmap

```bash
nmap -sV -p 21 <IP>
nmap -sC -p 21 <IP>
```

### Comprobar acceso anónimo

```bash
# Nmap script
nmap -p 21 --script ftp-anon <IP>

# Manual
ftp <IP>
# Usuario: anonymous
# Contraseña: (vacía o cualquier email)
```

Si el servidor responde `230 Login successful`, el acceso anónimo está habilitado.

---

## Conexión y comandos básicos

```bash
ftp <IP>

# Dentro del cliente FTP:
ls                  # Listar archivos
get archivo.txt     # Descargar archivo
put archivo.txt     # Subir archivo
binary              # Modo binario (para ejecutables, imágenes)
passive             # Cambiar a modo pasivo
bye                 # Cerrar sesión
```

Con `netcat` (útil cuando el cliente ftp no está disponible):

```bash
nc -nv <IP> 21
```

---

## Explotación

### Fuerza bruta con Hydra

```bash
hydra -l usuario -P /usr/share/wordlists/rockyou.txt ftp://<IP>

# Con lista de usuarios
hydra -L usuarios.txt -P passwords.txt ftp://<IP> -t 4
```

### Acceso anónimo con permisos de escritura

Si se puede escribir en el servidor FTP y hay un servidor web en el mismo host, puede ser posible subir una web shell:

```bash
ftp <IP>
put shell.php
```

---

## Laboratorio de práctica

- [docker-ftp-server](https://github.com/garethflowers/docker-ftp-server) — Servidor FTP con autenticación
- [docker-anon-ftp](https://github.com/metabrainz/docker-anon-ftp) — Servidor FTP con acceso anónimo

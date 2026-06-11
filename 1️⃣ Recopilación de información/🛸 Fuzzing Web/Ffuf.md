# ffuf

ffuf (Fuzz Faster U Fool) es la herramienta de fuzzing web más rápida y versátil actualmente. Usa el placeholder `FUZZ` igual que Wfuzz pero con mejor rendimiento, filtros más potentes y salida más limpia. Es el estándar de facto en CTFs y bug bounty.

- [github.com/ffuf/ffuf](https://github.com/ffuf/ffuf)

---

## Instalación

```bash
sudo apt install ffuf

# O desde Go
go install github.com/ffuf/ffuf/v2@latest
```

---

## Sintaxis básica

```bash
ffuf -u <URL con FUZZ> -w <diccionario>
```

---

## Casos de uso

### Fuzzing de directorios

```bash
ffuf -u https://ejemplo.com/FUZZ \
  -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt

# Con extensiones
ffuf -u https://ejemplo.com/FUZZ \
  -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words.txt \
  -e .php,.html,.txt,.bak
```

### Enumeración de subdominios / virtual hosts

```bash
ffuf -u http://ejemplo.com/ \
  -H "Host: FUZZ.ejemplo.com" \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -fc 301,302
```

### Fuzzing de parámetros GET

```bash
ffuf -u "https://ejemplo.com/page?FUZZ=test" \
  -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt \
  -fs 0
```

### Fuzzing de parámetros POST

```bash
ffuf -u https://ejemplo.com/login \
  -X POST \
  -d "username=admin&password=FUZZ" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -w /usr/share/wordlists/rockyou.txt \
  -fc 401
```

### Fuzzing con cookie de sesión

```bash
ffuf -u "https://ejemplo.com/FUZZ" \
  -w wordlist.txt \
  -H "Cookie: session=abc123"
```

### Múltiples diccionarios (W1 y W2)

```bash
ffuf -u https://ejemplo.com/login \
  -X POST \
  -d "user=W1&pass=W2" \
  -w usuarios.txt:W1 \
  -w passwords.txt:W2 \
  -fc 401 -t 10
```

---

## Filtros — fundamental para limpiar el ruido

| Flag | Descripción |
|---|---|
| `-fc` | Filtrar por código HTTP (ocultar) |
| `-sc` | Mostrar solo ese código HTTP |
| `-fs` | Filtrar por tamaño de respuesta (bytes) |
| `-ss` | Mostrar solo ese tamaño |
| `-fw` | Filtrar por número de palabras |
| `-fl` | Filtrar por número de líneas |
| `-fr` | Filtrar por regex en la respuesta |
| `-mc` | Mostrar solo esos códigos (alternativa a -sc) |

Flujo típico: primero lanzar sin filtros, identificar el tamaño/código de la respuesta genérica, y relanzar con ese filtro:

```bash
# 1. Identificar qué devuelve para palabras que no existen
ffuf -u https://ejemplo.com/FUZZ -w wordlist.txt -t 5

# 2. Si todas las 404 tienen el mismo tamaño (ej. 1234 bytes)
ffuf -u https://ejemplo.com/FUZZ -w wordlist.txt -fs 1234
```

---

## Parámetros generales

| Flag | Descripción |
|---|---|
| `-u` | URL con FUZZ |
| `-w` | Diccionario (múltiples con `-w file:KEYWORD`) |
| `-t` | Hilos (por defecto 40) |
| `-e` | Extensiones adicionales a probar |
| `-H` | Cabecera HTTP |
| `-X` | Método HTTP (GET, POST, PUT...) |
| `-d` | Datos del cuerpo (POST) |
| `-x` | Proxy (`http://127.0.0.1:8080`) |
| `-r` | Seguir redirecciones |
| `-o` | Archivo de salida |
| `-of` | Formato de salida (json, html, csv, md) |
| `-v` | Verbose (muestra URL completa) |
| `-ac` | Autocalibración de filtros |
| `-s` | Silencioso (sin banner ni barra de progreso) |

---

## Autocalibración

ffuf puede detectar automáticamente el tamaño de respuesta "vacía" del servidor:

```bash
ffuf -u https://ejemplo.com/FUZZ -w wordlist.txt -ac
```

---

## Enviar tráfico a través de Burp Suite

```bash
ffuf -u https://ejemplo.com/FUZZ -w wordlist.txt -x http://127.0.0.1:8080
```


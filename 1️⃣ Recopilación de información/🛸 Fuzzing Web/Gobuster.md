# Gobuster

Gobuster es una herramienta de fuerza bruta para descubrir directorios, archivos, subdominios (vhosts) y buckets en la nube. Destaca por su velocidad al estar escrita en Go.

- [github.com/OJ/gobuster](https://github.com/OJ/gobuster)

---

## Instalación

```bash
sudo apt install gobuster

# O desde Go
go install github.com/OJ/gobuster/v3@latest
```

---

## Modos principales

### dir — fuerza bruta de directorios y archivos

```bash
gobuster dir -u https://ejemplo.com \
  -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt

# Con extensiones de archivo
gobuster dir -u https://ejemplo.com \
  -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt \
  -x php,html,txt,bak

# Ignorar certificado SSL inválido
gobuster dir -u https://ejemplo.com -w wordlist.txt -k

# Añadir cabecera (cookie de sesión, token...)
gobuster dir -u https://ejemplo.com -w wordlist.txt \
  -H "Cookie: session=abc123"
```

### vhost — enumeración de subdominios/virtual hosts

```bash
gobuster vhost -u https://ejemplo.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -t 20 | grep -v "403"

# Añadir dominio base si no se resuelve automáticamente
gobuster vhost -u https://ejemplo.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  --append-domain
```

### dns — fuerza bruta de subdominios vía DNS

```bash
gobuster dns -d ejemplo.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -t 20
```

### fuzz — fuzzing genérico de parámetros

```bash
gobuster fuzz -u "https://ejemplo.com/page?id=FUZZ" \
  -w /usr/share/seclists/Fuzzing/fuzz-Bo0oM.txt \
  --exclude-length 0
```

---

## Parámetros más usados

| Flag | Descripción |
|---|---|
| `-u` | URL objetivo |
| `-w` | Ruta al diccionario |
| `-t` | Hilos (por defecto 10) |
| `-x` | Extensiones a probar (php,html,txt...) |
| `-k` | Ignorar errores de certificado TLS |
| `-b` | Códigos HTTP a ignorar (por defecto 404) |
| `-s` | Códigos HTTP a mostrar |
| `-H` | Cabecera HTTP adicional |
| `-o` | Guardar resultado en archivo |
| `--delay` | Espera entre peticiones (evasión) |
| `-q` | Modo silencioso (sin banner) |

---

## Ejemplos prácticos

```bash
# Buscar paneles de administración
gobuster dir -u https://ejemplo.com \
  -w /usr/share/seclists/Discovery/Web-Content/common.txt \
  -x php,html -b 404,403 -t 30

# Buscar archivos de backup y configuración
gobuster dir -u https://ejemplo.com \
  -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt \
  -x bak,old,conf,config,zip,tar.gz

# Enumerar vhosts filtrando respuestas genéricas
gobuster vhost -u https://ejemplo.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  --exclude-length 301 -t 30
```

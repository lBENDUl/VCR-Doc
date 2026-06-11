[[0️⃣​ - Attacking Web Applications with Ffuf]] 

#Linux #Fuzzing

**CheatSheet ->** [[Attacking_Web_Applications_With_Ffuf_Module_Cheat_Sheet.pdf]]

---

Es una herramienta para el fuzzing web de las más comunes y confiables disponibles.
# Instalación

Suele estar instalado en sistemas operativos orientados a la seguridad, en el caso de querer instalarlo seria de la siguiente manera:

```shell-session
apt install ffuf -y
```

o

Descargándolo desde el repositorio
https://github.com/ffuf/ffuf


# Funcionamiento

Ver la ayuda:

```shell-session
vcrdcr@htb[/htb]$ ffuf -h
```

## Opciones Principales

Para lista de palabras `-w`
Con esto tambien podemos asignar una lista de palabras a una palabra clave. Por ejemplo de la siguiente manera:

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ
```

Para la URL `-u`

```shell-session
vcrdcr@htb[/htb]$ ffuf -w <SNIP> -u http://SERVER_IP:PORT/FUZZ
```


## Opciones Secundarias

### Aumento de Velocidad
Aumentar el número de hilos para mayor velocidad de escaneo `-t`

Por ejmplo: 
	`-t 200`


### URLs Completas

Para ver las URLs completas se indica con la bandera `-v`


### Recursividad

En muchas paginas hay directorios dentro de otros por lo que la recursividad nos puede venir muy bien.
Se utiliza con la bandera `-recursion` y podemos especificar la profundidad con el `-recursion-depth`.

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v
```


# Directorios

Para ello utilizamos la palabra clave de FUZZ en donde estaría la palabra del directorio.

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ
```

# Páginas

## Extensión

Para ello utilizamos la palabra clave de FUZZ en donde la extension .FUZZ
Para ello podemos utilizar una lista de palabras de extensiones de [[Seclistas]].

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ <SNIP>
```

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ
```


## Nombres de archivo

Una vez sabida la extensión y visto de que servidor consiste, podemos ver los nombres de los archivos de la siguiente manera:

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php
```

# Subdominios

## Públicos

Para ello utilizaremos un diccionario especifico de subdominios, como por ejemplo `subdomains-top1million-5000.txt`.
Ponemos la palabra clave antes del dominio separado de un punto.

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/
```

```shell-session
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.academy.htb/
```


## Privados Vhost Fuzzing

Para buscar VHosts, sin agregar manualmente toda la lista de palabras a nuestro `/etc/hosts`, estaremos borrando encabezados HTTP, específicamente el `Host:` encabezado. Para hacer eso, podemos usar el `-H` bandera para especificar un encabezado y usará el `FUZZ` palabra clave dentro de ella, de la siguiente manera:

```shell-session
ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'
```


# Filtrado


  -fc              Filter HTTP status codes from response. Comma separated list of codes and ranges
  
  -fl              Filter by amount of lines in response. Comma separated list of line counts and ranges
  
  -fr              Filter regexp
  
  -fs              Filter HTTP response size. Comma separated list of sizes and ranges
  
  -fw              Filter by amount of words in response. Comma separated list of word counts and ranges
  
>NOTA
>Se tiene que poner en el filtro los que se quieren excluir.

```
ffuf -w /home/kali/Desktop/subdomains-top1million-5000.txt:FUZZ -u http://94.237.50.242:48094/ -H 'Host: FUZZ.academy.htb' -fs 980
```


# Get

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx
```

```
ffuf -w /home/kali/Desktop/burp-parameter-names.txt:FUZZ -u http://94.237.55.200:46041/admin/admin.php?FUZZ=key -H 'Host: admin.academy.htb' -v -fs 798
```

# POST

Para fuzz el `data` campo con `ffuf`, podemos usar el `-d` bandera, como vimos anteriormente en la salida de `ffuf -h`. También tenemos que agregar `-X POST` para enviar `POST` solicitudes.

```shell-session
ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx
```

>NOTA
>En PHP, los datos "POST" "content-type" solo pueden aceptar "application/x-www-form-urlencoded". Por lo tanto, podemos establecer eso en "ffuf" con "-H 'Content-Type: application/x-www-form-urlencoded'".

# Recoger valor

```
ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:33902/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Host: admin.academy.htb' -fs 768

```

# Parámetros mas comunes

[enlace](https://book.hacktricks.wiki/en/pentesting-web/file-inclusion/index.html#top-25-parameters)


```
?cat={payload} 
?dir={payload} 
?action={payload} 
?board={payload} 
?date={payload} 
?detail={payload} 
?file={payload} 
?download={payload} 
?path={payload} 
?folder={payload} 
?prefix={payload} 
?include={payload} 
?page={payload} 
?inc={payload} 
?locate={payload} 
?show={payload} 
?doc={payload} 
?site={payload} 
?type={payload} 
?view={payload} 
?content={payload} 
?document={payload} 
?layout={payload} 
?mod={payload}
?conf={payload}
```

# Lista de palabras

SecList
https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/LFI

Rutas de servidor
- [lista de palabras para Linux](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt)
- [lista de palabras para Windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt)

Rutas LFI
-  [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt)

Registros/Configuraciones del Servidor (Preciso)
- [lista de palabras para Linux](https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Linux)
- [lista de palabras para Windows](https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Windows)


cat << 'EOF' > /home/claude/gitbook/06-herramientas/ffuf.md
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




# Otros

Ayudas de HTB
[[Attacking_Web_Applications_With_Ffuf_Module_Cheat_Sheet.pdf]] 

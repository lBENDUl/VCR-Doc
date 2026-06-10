#python

La biblioteca ‘**requests**‘ en Python es una de las herramientas más populares y poderosas para realizar solicitudes HTTP. Su diseño es intuitivo y fácil de usar, lo que la hace una opción preferida para interactuar con APIs y servicios web. 

**Introducción a requests**

‘**requests**‘ es una biblioteca de Python que simplifica enormemente el proceso de enviar solicitudes HTTP. Está diseñada para ser más fácil de usar que las opciones incorporadas en Python, como ‘**urllib**‘, proporcionando una API más amigable.

**Características Principales**

- **Simplicidad y Facilidad de Uso**: Con requests, enviar solicitudes GET, POST, PUT, DELETE, entre otras, se puede realizar en pocas líneas de código. Su sintaxis es clara y concisa.
- **Gestión de Parámetros URL**: Permite manejar parámetros de consulta y cuerpos de solicitud con facilidad, automatizando la codificación de URL.
- **Manejo de Respuestas**: ‘**requests**‘ facilita la interpretación de respuestas HTTP, proporcionando un objeto de respuesta que incluye el contenido, el estado, los encabezados, y más.
- **Soporte para Autenticaciones**: Ofrece soporte integrado para diferentes formas de autenticación, incluyendo autenticación básica, digest, y OAuth.
- **Manejo de Sesiones y Cookies**: Permite mantener sesiones y gestionar cookies, lo cual es útil para interactuar con sitios web que requieren autenticación o mantienen estado.
- **Soporte para SSL**: ‘**requests**‘ maneja SSL (Secure Sockets Layer) y TLS (Transport Layer Security), permitiendo realizar solicitudes seguras a sitios HTTPS.
- **Manejo de Excepciones y Errores**: Proporciona métodos para manejar y reportar errores de red y HTTP de manera efectiva.

**Uso Práctico**

La biblioteca se utiliza ampliamente para interactuar con APIs RESTful, automatizar interacciones con sitios web, y en tareas de scraping web. Sus capacidades para manejar solicitudes complejas y sus características de seguridad la hacen ideal para una amplia gama de aplicaciones, desde scripts simples hasta sistemas empresariales complejos.

**Conclusión**

La comprensión y el uso efectivo de ‘**requests**‘ son habilidades esenciales para cualquier desarrollador Python que trabaje con HTTP y APIs web. Esta biblioteca no solo facilita la realización de tareas relacionadas con la red, sino que también ayuda a escribir código más limpio y mantenible.


# requests.get()

Petición GET

```python
response = requests.get("https://google.es")

print(response.status_code)

print(response.text)
```


```python
values={:}

response = requests.get("https://google.es", params=values, timeout=10)

print(f"URL completa: {response.url}")
print(response.text)
```

# requests.post()

Petición POST

```python
values={:}
headers = {'User-Agent':'my-app/1.0.0'}

response = requests.post("https://google.es", data=values, headers=headers, timeout=10)

print(f"URL completa: {response.url}")
```

# response.status_code

Devuelve el código de estado de la solicitud enviada anteriormente.

```python
print(response.status_code)
```


# response.text

Devuelve el resultado en forma de texto

```python
print(response.text)
```


# response.url

Devuelve la url final

```python
response.url
```


# Edición de cabecera (headers)

```python
values={:}
headers = {'User-Agent':'my-app/1.0.0'}

response = requests.post("https://google.es", data=values, headers=headers)

print(f"URL completa: {response.url}")
```


# timeout / tiempo de espera

Establece un tiempo de espera en segundos

```python
try:
	response = requests.post("https://google.es", timeout=10)

except requests.Timeout:
	print("La petición ha excedido el límite de tiempo de espera")
except requests.HTTPError as http_err:
	print(f"Error en la solicitud: {http_err}")
except request.RequestException as err:
	print(f"Error: {err}")
```


# Respuesta JSON

```python
response = requests.get("https//google.es")

data = response.json()
```


# Autenticación web por formulario básico

```python
response = request.get('', auth=('user', 'contraseña'))

print(response.text)
```


# Arrastrar la misma sesión (cookie)

```python
s = requests.Session()

response = s.get(set_cookies_url)
response2 = s.get(url)
```


```python
with requests.Session() as session:
	response1 = session.get('https://httpbin.org/get')
	response2 = session.get('https://httpbin.org/get')
```

# Quitar redirección a https

```python
r = requests.get(url, allow_redirects=False)
```
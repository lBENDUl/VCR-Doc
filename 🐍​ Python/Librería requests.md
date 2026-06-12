# Python — Librería `requests`


## Instalación

```bash
pip install requests
```

```python
import requests
```

---

## GET

```python
response = requests.get("https://api.ejemplo.com/datos")

print(response.status_code)  # 200, 404, 500...
print(response.text)         # cuerpo como string
print(response.content)      # cuerpo como bytes
print(response.url)          # URL final (tras redirecciones)
print(response.headers)      # cabeceras de respuesta (dict)
```

### Con parámetros en la URL

```python
params = {
    "q": "python",
    "page": 1,
    "limit": 10
}
response = requests.get("https://api.ejemplo.com/buscar", params=params)
print(response.url)
# https://api.ejemplo.com/buscar?q=python&page=1&limit=10
```

---

## POST

### Datos de formulario (`application/x-www-form-urlencoded`)

```python
datos = {
    "username": "usuario",
    "password": "contraseña"
}
response = requests.post("https://ejemplo.com/login", data=datos)
```

### JSON (`application/json`)

```python
payload = {"nombre": "Ana", "edad": 30}
response = requests.post("https://api.ejemplo.com/usuarios", json=payload)
```

### Subir un archivo

```python
with open("documento.pdf", "rb") as f:
    response = requests.post(
        "https://api.ejemplo.com/upload",
        files={"archivo": f}
    )
```

---

## Otros métodos HTTP

```python
# PUT — actualizar recurso completo
response = requests.put("https://api.ejemplo.com/usuarios/1", json={"nombre": "Ana"})

# PATCH — actualizar parcialmente
response = requests.patch("https://api.ejemplo.com/usuarios/1", json={"edad": 31})

# DELETE — eliminar recurso
response = requests.delete("https://api.ejemplo.com/usuarios/1")

# HEAD — solo cabeceras, sin cuerpo
response = requests.head("https://ejemplo.com")
```

---

## Respuesta JSON

```python
response = requests.get("https://api.ejemplo.com/datos")

if response.status_code == 200:
    datos = response.json()  # dict o list
    print(datos["clave"])
```

---

## Cabeceras personalizadas

```python
headers = {
    "User-Agent": "mi-app/1.0",
    "Authorization": "Bearer mi_token_aqui",
    "Accept": "application/json"
}

response = requests.get("https://api.ejemplo.com/datos", headers=headers)
```

---

## Timeout

Siempre es buena práctica fijar un timeout para evitar que la petición se quede colgada indefinidamente.

```python
# Timeout en segundos
response = requests.get("https://api.ejemplo.com", timeout=10)

# Timeout separado para conexión y lectura
response = requests.get("https://api.ejemplo.com", timeout=(5, 30))
# (5s para conectar, 30s para leer la respuesta)
```

---

## Manejo de errores

```python
try:
    response = requests.get("https://api.ejemplo.com/datos", timeout=10)
    response.raise_for_status()  # lanza excepción si status >= 400

    datos = response.json()

except requests.Timeout:
    print("La petición tardó demasiado")
except requests.HTTPError as e:
    print(f"Error HTTP {response.status_code}: {e}")
except requests.ConnectionError:
    print("No se pudo conectar al servidor")
except requests.RequestException as e:
    print(f"Error de red: {e}")
```

### Comprobar el status manualmente

```python
response = requests.get("https://api.ejemplo.com")

if response.ok:  # True si status < 400
    print("Todo bien")

# O directamente
if response.status_code == 200:
    ...
elif response.status_code == 404:
    print("No encontrado")
elif response.status_code == 401:
    print("No autorizado")
```

---

## Sesiones

Mantienen configuración (cabeceras, cookies, autenticación) entre peticiones. Más eficientes porque reutilizan la conexión TCP.

```python
with requests.Session() as s:
    # Configuración compartida para todas las peticiones de la sesión
    s.headers.update({"Authorization": "Bearer mi_token"})

    r1 = s.get("https://api.ejemplo.com/perfil")
    r2 = s.get("https://api.ejemplo.com/pedidos")
    r3 = s.post("https://api.ejemplo.com/accion", json={"key": "val"})
```

### Sesión con login + cookie

```python
with requests.Session() as s:
    # Login (la cookie de sesión se guarda automáticamente)
    s.post("https://ejemplo.com/login", data={"user": "ana", "pass": "1234"})

    # Peticiones autenticadas sin volver a hacer login
    perfil  = s.get("https://ejemplo.com/perfil")
    ajustes = s.get("https://ejemplo.com/ajustes")
```

---

## Autenticación

### Basic Auth

```python
response = requests.get(
    "https://api.ejemplo.com/privado",
    auth=("usuario", "contraseña")
)
```

### Token en cabecera

```python
headers = {"Authorization": "Bearer eyJhbGci..."}
response = requests.get("https://api.ejemplo.com/datos", headers=headers)
```

### Digest Auth

```python
from requests.auth import HTTPDigestAuth

response = requests.get(
    "https://api.ejemplo.com",
    auth=HTTPDigestAuth("usuario", "contraseña")
)
```

---

## Cookies

```python
# Enviar cookies manualmente
cookies = {"session_id": "abc123"}
response = requests.get("https://ejemplo.com", cookies=cookies)

# Leer cookies de la respuesta
print(response.cookies)
print(response.cookies.get("session_id"))
```

---

## SSL y certificados

```python
# Verificar certificado (por defecto True)
response = requests.get("https://ejemplo.com", verify=True)

# Deshabilitar verificación (en laboratorios/proxies internos)
import urllib3
urllib3.disable_warnings()
response = requests.get("https://ejemplo.com", verify=False)

# Usar un certificado propio
response = requests.get("https://ejemplo.com", verify="/ruta/ca-bundle.crt")
```

---

## Redirecciones

```python
# Seguir redirecciones (por defecto True)
response = requests.get("https://ejemplo.com")
print(response.history)  # lista de redirecciones intermedias

# No seguir redirecciones
response = requests.get("https://ejemplo.com", allow_redirects=False)
print(response.status_code)  # 301 o 302
print(response.headers["Location"])
```

---

## Proxies

```python
proxies = {
    "http":  "http://proxy.ejemplo.com:8080",
    "https": "http://proxy.ejemplo.com:8080"
}
response = requests.get("https://api.ejemplo.com", proxies=proxies)
```

---

## Patrones habituales

### Paginación de API

```python
resultados = []
page = 1

while True:
    r = requests.get("https://api.ejemplo.com/items", params={"page": page})
    r.raise_for_status()
    datos = r.json()

    if not datos:
        break

    resultados.extend(datos)
    page += 1

print(f"Total: {len(resultados)} items")
```

### Reintentos con backoff

```python
from requests.adapters import HTTPAdapter
from urllib3.util.retry import Retry

session = requests.Session()

retry = Retry(
    total=3,           # 3 reintentos
    backoff_factor=1,  # espera 1s, 2s, 4s...
    status_forcelist=[429, 500, 502, 503, 504]
)
adapter = HTTPAdapter(max_retries=retry)
session.mount("https://", adapter)

response = session.get("https://api.ejemplo.com/datos", timeout=10)
```

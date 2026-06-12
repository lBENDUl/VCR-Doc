# Python — Librería `urllib3`


## ¿Cuándo usar `urllib3` vs `requests`?

`requests` está construida sobre `urllib3` y es más cómoda para la mayoría de casos. Usa `urllib3` directamente cuando necesites:
- Control fino del pool de conexiones
- Rendimiento máximo con muchas peticiones al mismo host
- Sin dependencias externas extra (urllib3 viene con Python)
- Integración en librerías que no quieren depender de `requests`

---

## Instalación

```bash
pip install urllib3
```

```python
import urllib3
```

---

## PoolManager — Controlador de conexiones

El `PoolManager` gestiona un pool de conexiones reutilizables, lo que es mucho más eficiente que abrir una nueva conexión TCP en cada petición.

```python
import urllib3

http = urllib3.PoolManager()
```

---

## GET

```python
import urllib3

http = urllib3.PoolManager()

response = http.request("GET", "https://httpbin.org/get")

print(response.status)          # 200
print(response.data.decode())   # cuerpo como string
print(response.headers)         # cabeceras (dict-like)
```

### Con parámetros en la URL

```python
response = http.request(
    "GET",
    "https://httpbin.org/get",
    fields={"q": "python", "page": "1"}
)
print(response.data.decode())
```

---

## POST

### Datos de formulario

```python
response = http.request(
    "POST",
    "https://httpbin.org/post",
    fields={"usuario": "ana", "contraseña": "1234"}
)
print(response.data.decode())
```

### JSON

```python
import json, urllib3

http = urllib3.PoolManager()

payload = {"nombre": "Ana", "edad": 30}
encoded = json.dumps(payload).encode("utf-8")

response = http.request(
    "POST",
    "https://httpbin.org/post",
    body=encoded,
    headers={"Content-Type": "application/json"}
)

data = json.loads(response.data.decode())
print(data)
```

---

## Otros métodos HTTP

```python
# PUT
response = http.request("PUT", "https://api.ejemplo.com/usuarios/1",
                        body=json.dumps({"nombre": "Ana"}).encode(),
                        headers={"Content-Type": "application/json"})

# DELETE
response = http.request("DELETE", "https://api.ejemplo.com/usuarios/1")

# HEAD
response = http.request("HEAD", "https://ejemplo.com")
print(response.headers)
```

---

## Cabeceras personalizadas

```python
headers = {
    "User-Agent": "mi-app/1.0",
    "Authorization": "Bearer mi_token",
    "Accept": "application/json"
}

response = http.request("GET", "https://api.ejemplo.com/datos", headers=headers)
```

---

## Respuesta JSON

```python
import json, urllib3

http = urllib3.PoolManager()
response = http.request("GET", "https://httpbin.org/json")

datos = json.loads(response.data.decode())
print(datos)
```

---

## Timeout

```python
# Timeout global en el PoolManager
http = urllib3.PoolManager(timeout=urllib3.Timeout(connect=3, read=10))

# Timeout por petición
response = http.request(
    "GET",
    "https://api.ejemplo.com",
    timeout=urllib3.Timeout(connect=3, read=10)
)

# Timeout simple (se aplica a conexión y lectura)
response = http.request("GET", "https://api.ejemplo.com", timeout=5.0)
```

---

## Reintentos automáticos

```python
# Reintentos configurados en el PoolManager
retry = urllib3.Retry(
    total=3,                        # máximo 3 reintentos
    backoff_factor=0.5,             # espera entre reintentos
    status_forcelist=[500, 502, 503, 504],  # reintentar en estos status
    allowed_methods=["GET", "POST"]
)
http = urllib3.PoolManager(retries=retry)

# O por petición
response = http.request(
    "GET",
    "https://api.ejemplo.com",
    retries=urllib3.Retry(3)
)

# Deshabilitar reintentos (falla inmediatamente)
response = http.request("GET", "https://api.ejemplo.com", retries=False)
```

---

## SSL / HTTPS

```python
# Verificación SSL activada por defecto
http = urllib3.PoolManager()
response = http.request("GET", "https://ejemplo.com")

# Deshabilitar verificación SSL (solo en entornos de laboratorio)
import urllib3
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

http = urllib3.PoolManager(cert_reqs="CERT_NONE")
response = http.request("GET", "https://ejemplo.com")

# Certificado CA propio
http = urllib3.PoolManager(ca_certs="/ruta/a/ca-bundle.crt")
```

---

## Subir archivos

```python
with open("documento.pdf", "rb") as f:
    response = http.request(
        "POST",
        "https://api.ejemplo.com/upload",
        fields={"archivo": ("documento.pdf", f.read(), "application/pdf")}
    )
```

---

## Streaming de respuestas grandes

```python
http = urllib3.PoolManager()

response = http.request("GET", "https://ejemplo.com/archivo-grande.zip",
                        preload_content=False)  # no cargar todo en memoria

with open("descarga.zip", "wb") as f:
    for chunk in response.stream(8192):  # chunks de 8KB
        f.write(chunk)

response.release_conn()
```

---

## ProxyManager — Uso con proxy

```python
proxy = urllib3.ProxyManager("http://proxy.empresa.com:8080")
response = proxy.request("GET", "https://api.ejemplo.com/datos")
```

---

## Manejo de errores

```python
import urllib3

http = urllib3.PoolManager()

try:
    response = http.request("GET", "https://api.ejemplo.com", timeout=5.0, retries=False)

    if response.status == 200:
        datos = json.loads(response.data.decode())
    elif response.status == 404:
        print("Recurso no encontrado")
    elif response.status >= 500:
        print("Error del servidor")

except urllib3.exceptions.MaxRetryError:
    print("No se pudo conectar tras varios reintentos")
except urllib3.exceptions.TimeoutError:
    print("Timeout")
except urllib3.exceptions.NewConnectionError:
    print("No hay conexión de red")
except urllib3.exceptions.HTTPError as e:
    print(f"Error HTTP: {e}")
```

---

## Comparativa rápida `urllib3` vs `requests`

```python
# urllib3
import urllib3, json
http = urllib3.PoolManager()
r = http.request("GET", "https://httpbin.org/get")
datos = json.loads(r.data.decode())

# requests (equivalente)
import requests
r = requests.get("https://httpbin.org/get")
datos = r.json()
```

`requests` es más concisa para uso general. `urllib3` da más control sobre conexiones, pooling y tiene menos dependencias.

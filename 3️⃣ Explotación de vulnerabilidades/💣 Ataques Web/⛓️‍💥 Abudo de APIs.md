# Abuso de APIs REST

Una **API** (*Application Programming Interface*) es un conjunto de endpoints que permiten la comunicación entre aplicaciones. Las APIs REST son las más comunes en entornos web actuales y, cuando están mal aseguradas, presentan una superficie de ataque significativa.

---

## Conceptos básicos

Una API REST expone recursos a través de URLs y métodos HTTP:

| Método | Acción típica |
|---|---|
| `GET` | Leer/listar recursos |
| `POST` | Crear un nuevo recurso |
| `PUT` / `PATCH` | Actualizar un recurso existente |
| `DELETE` | Eliminar un recurso |

El primer paso en el abuso de APIs es la **enumeración**: descubrir qué endpoints existen y qué métodos aceptan.

---

## Herramientas

### Postman

Interfaz gráfica para construir, enviar y analizar peticiones HTTP/HTTPS a APIs. Permite:
- Gestionar colecciones de requests
- Configurar autenticación (Bearer tokens, API keys, OAuth)
- Encadenar peticiones con variables de entorno

### curl

Para interacciones rápidas desde terminal:
```bash
# GET básico
curl -s https://api.target.com/v1/users

# POST con JSON
curl -s -X POST https://api.target.com/v1/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"test"}'

# Con token de autenticación
curl -s https://api.target.com/v1/profile \
  -H "Authorization: Bearer eyJhbGci..."

# Seguir redirecciones + mostrar headers
curl -sv https://api.target.com/v1/users
```

---

## Fase de reconocimiento

### 1. Descubrir endpoints

Muchas APIs exponen documentación automática:
```
/api/docs
/api/swagger
/swagger.json
/openapi.json
/v1/docs
/redoc
```

### 2. Fuzzing de endpoints con ffuf

```bash
# Enumerar rutas de la API
ffuf -w /usr/share/seclists/Discovery/Web-Content/api/api-endpoints.txt \
  -u https://target.com/api/FUZZ \
  -mc 200,201,204,301,302,401,403

# Enumerar versiones de API
ffuf -w version_list.txt -u https://target.com/FUZZ/users -mc 200
```

### 3. Métodos HTTP no esperados

Probar todos los métodos en cada endpoint:
```bash
for method in GET POST PUT DELETE PATCH OPTIONS HEAD; do
  echo -n "$method: "
  curl -s -o /dev/null -w "%{http_code}" -X $method https://api.target.com/v1/users
  echo
done
```

---

## Vulnerabilidades comunes en APIs

### BOLA — Broken Object Level Authorization (IDOR)

El atacante cambia el ID de un objeto en la petición para acceder a datos de otro usuario:

```bash
# Usuario accede a su propio perfil
GET /api/v1/users/1337/profile

# Prueba BOLA: acceder al perfil de otro usuario
GET /api/v1/users/1/profile
GET /api/v1/users/2/profile
```

### Exposición de información excesiva

Comparar la respuesta del endpoint para el propio usuario vs lo que devuelve para otros. A veces los campos sensibles (email, teléfono, rol) se devuelven aunque no deberían ser accesibles.

### Inyección SQL / NoSQL en parámetros JSON

```bash
curl -s -X GET "https://api.target.com/v1/users?id=1 OR 1=1--"
curl -s -X POST https://api.target.com/v1/search \
  -d '{"username": {"$ne": null}}'
```

### Mass Assignment

Enviar campos adicionales en el body que la API podría aplicar sin validación:

```json
{"username": "victima", "role": "admin", "verified": true}
```

### BFLA — Broken Function Level Authorization

Acceder a funciones de administración sin tener el rol adecuado:
```bash
# Endpoint de admin sin autenticación elevada
GET /api/v1/admin/users
DELETE /api/v1/admin/users/5
```

---

## Autenticación y tokens

### JWT (JSON Web Tokens)

Decodificar un JWT para ver su contenido (no requiere la clave):
```bash
# El JWT tiene 3 partes separadas por '.': header.payload.signature
echo "eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiYWRtaW4ifQ" | base64 -d
```

Vectores de ataque:
- **Algorithm: none** — cambiar el header a `{"alg":"none"}` y eliminar la firma
- **Fuerza bruta del secret** con `hashcat` o `jwt_tool`
- **Confusión RS256 → HS256** — firmar con la clave pública como si fuera simétrica

### API Keys expuestas

Buscar en repositorios, archivos JavaScript del frontend, respuestas de la propia API:
```bash
# Buscar en el código JS del sitio
curl -s https://target.com/app.js | grep -i "api_key\|apikey\|secret\|token"
```

---

## Laboratorio para practicar

- **crAPI** (Completely Ridiculous API): [https://github.com/OWASP/crAPI](https://github.com/OWASP/crAPI)

```bash
# Despliegue con Docker Compose (rama develop para mayor compatibilidad)
curl -o docker-compose.yml \
  https://raw.githubusercontent.com/OWASP/crAPI/develop/deploy/docker/docker-compose.yml
VERSION=develop docker-compose pull
VERSION=develop docker-compose -f docker-compose.yml --compatibility up -d
```

---

## Mitigaciones

- Implementar autorización a nivel de objeto en cada endpoint (no asumir que el token es suficiente)
- Rate limiting y throttling para prevenir enumeración y fuerza bruta
- Validar y filtrar todos los campos del body — no confiar en mass assignment
- Ocultar o deshabilitar la documentación automática en producción
- Rotar y revocar tokens comprometidos
- Auditar regularmente los endpoints con herramientas como OWASP ZAP o Burp Suite

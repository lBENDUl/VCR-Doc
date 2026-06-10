---
tags:
  - Fuzzing
  - Web
  - WordPress
  - Enumeración
---
---

# Introducción

Una de las herramientas que utilizamos en esta clase para enumerar este gestor de contenido es **Wpscan**. Wpscan es una herramienta de código abierto que se utiliza para escanear sitios web en busca de vulnerabilidades de seguridad en WordPress.

Con Wpscan, podemos realizar una enumeración completa del sitio web y obtener información detallada sobre la instalación de WordPress, como la versión utilizada, los plugins y temas instalados y los usuarios registrados en el sitio. También nos permite realizar pruebas de fuerza bruta para descubrir contraseñas débiles y vulnerabilidades conocidas en plugins y temas.

Wpscan es una herramienta muy útil para los administradores de sitios web que desean mejorar la seguridad de su sitio WordPress, ya que permite identificar y corregir vulnerabilidades antes de que sean explotadas por atacantes malintencionados. Además, es una herramienta fácil de usar y muy efectiva para identificar posibles debilidades de seguridad en nuestro sitio web.

El uso de esta herramienta es bastante sencillo, a continuación se indica la sintaxis básica:

```bash
wpscan --url https://example.com
```

Si deseas enumerar usuarios o plugins vulnerables en WordPress utilizando la herramienta wpscan, puedes añadir los siguientes parámetros a la línea de comandos:

```bash
wpscan --url https://example.com --enumerate u
```

En caso de querer enumerar plugins existentes los cuales sean vulnerables, puedes añadir el siguiente parámetro a la línea de comandos:

```bash
wpscan --url https://example.com --enumerate vp
```

En el caso de que queramos tener un escaneo más a fondo, podemos realizar el escaneo proporcionando un API-TOKEN.

Para ello vamos a la web https://wpscan.com, nos logueamos y nos aparece un token. Copiamos el token y realizamos el siguiente comando:

```bash
wpscan --url https://example.com -e vp --api-token="TOKEN"
```

# Lab

- **DVWP**: [https://github.com/vavkamil/dvwp](https://github.com/vavkamil/dvwp)
# Otros recursos

## Ruta de plugins

A veces en WordPress existe un directorio donde vemos los plugins que estan instalados. Dicha ruta es wp-content/plugins/

Ejemplo: -> localhost:222/wp-content/plugins/

En el caso de que en el navegador no aparezca, podemos realizar una solicitud con `curl` y filtramos por plugins para ver si nos da respuesta:

```bash
curl -s -X GET "https://localhost:31337/" | grep plugins
```

```bash
curl -s -X GET "https://localhost:31337/" | grep -oP 'plugins/\K[^/]+' | sort -u
```

## Archivo xmlrpc.php

Este archivo es una característica de WordPress que permite la comunicación entre el sitio web y aplicaciones externas utilizando el protocolo **XML-RPC**.

El archivo xmlrpc.php es utilizado por muchos plugins y aplicaciones móviles de WordPress para interactuar con el sitio web y realizar diversas tareas, como publicar contenido, actualizar el sitio y obtener información.

Sin embargo, este archivo también puede ser abusado por atacantes malintencionados para aplicar **fuerza bruta** y descubrir **credenciales válidas** de los usuarios del sitio. Esto se debe a que xmlrpc.php permite a los atacantes realizar un número ilimitado de solicitudes de inicio de sesión sin ser bloqueados, lo que hace que la ejecución de un ataque de fuerza bruta sea relativamente sencilla.

En la siguiente clase estaremos desarrollando un script en Bash desde cero para realizar este tipo de ataques.



---
tags:
  - "#Fuzzing"
  - Web
  - Joomla
  - Enumeración
---
---

# Introducción

Joomscan es una herramienta de línea de comandos diseñada específicamente para escanear sitios web que utilizan Joomla y buscar posibles vulnerabilidades y debilidades de seguridad.

Joomscan utiliza una variedad de técnicas de enumeración para identificar información sobre el sitio web de Joomla, como la versión de Joomla utilizada, los plugins y módulos instalados y los usuarios registrados en el sitio. También utiliza una base de datos de vulnerabilidades conocidas para buscar posibles vulnerabilidades en la instalación de Joomla.

Para utilizar Joomscan, primero debemos descargar la herramienta desde su sitio web oficial. A continuación se os proporciona el enlace al proyecto:

- **Joomscan**: [https://github.com/OWASP/joomscan](https://github.com/OWASP/joomscan)

Una vez descargado, podemos utilizar la siguiente sintaxis básica para escanear un sitio web de Joomla:

```bash
perl joomscan.pl -u <URL>
```

Donde **<URL>** es la URL del sitio web que deseamos escanear. Joomscan escaneará el sitio web y nos proporcionará una lista detallada de posibles vulnerabilidades y debilidades de seguridad.

# Lab

- **CVE-2015-8562**: [https://github.com/vulhub/vulhub/tree/master/joomla/CVE-2015-8562](https://github.com/vulhub/vulhub/tree/master/joomla/CVE-2015-8562)
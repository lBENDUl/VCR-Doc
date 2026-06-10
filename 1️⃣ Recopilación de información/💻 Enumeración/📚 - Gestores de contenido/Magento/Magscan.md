---
tags:
  - Fuzzing
  - Web
  - Enumeración
  - Magento
---
---

# Introducción

Magescan puede detectar vulnerabilidades comunes en Magento, incluyendo problemas con permisos de archivos, errores de configuración y vulnerabilidades conocidas en extensiones populares de Magento.

A continuación se proporciona el enlace directo al proyecto en Github:

- **Magescan**: [https://github.com/steverobbins/magescan](https://github.com/steverobbins/magescan)

Su sintaxis y modo de uso es bastante sencillo, a continuación se comparte un ejemplo:

```bash
php magescan.phar scan:all https://example.com
```

Donde “**magescan.pha**r” es el archivo ejecutable de la herramienta “**Magescan**“, “**scan:all**” es el comando específico de Magescan que indica que se realizará un escaneo exhaustivo de todas las vulnerabilidades conocidas en el sitio web objetivo y “**https://example.com**” es la URL del sitio web objetivo que se escaneará en busca de vulnerabilidades.

# Lab

- **Magento 2.2 SQL Injection**: [https://github.com/vulhub/vulhub/tree/master/magento/2.2-sqli](https://github.com/vulhub/vulhub/tree/master/magento/2.2-sqli)
# Identificación de Tecnologías Web

Conocer el stack tecnológico de una aplicación web es el primer paso para identificar vulnerabilidades. El servidor web, el CMS, los frameworks y las librerías JS determinan los vectores de ataque disponibles.

---

## Herramientas

### WhatWeb (CLI)

Escanea una URL y devuelve el stack detectado: servidor, CMS, lenguaje, librerías, etc.

```bash
whatweb https://ejemplo.com

# Más detalle
whatweb -v https://ejemplo.com

# Escaneo agresivo
whatweb -a 3 https://ejemplo.com
```

- [github.com/urbanadventurer/WhatWeb](https://github.com/urbanadventurer/WhatWeb)

### Wappalyzer (extensión de navegador)

Detecta tecnologías en tiempo real mientras navegas. Muestra CMS, frameworks JS, CDN, analytics, herramientas de marketing, etc. sin necesidad de lanzar escaneos.

- [Extensión para Firefox](https://addons.mozilla.org/es/firefox/addon/wappalyzer/)

### BuiltWith (online)

Herramienta web que muestra el stack completo de un dominio con historial de tecnologías y estadísticas de uso.

- [builtwith.com](https://builtwith.com/)

---

## Qué buscar

| Tecnología detectada | Por qué importa |
|---|---|
| Versión de servidor (Apache 2.4.49, nginx 1.14...) | Buscar CVEs específicos |
| CMS (WordPress, Joomla, Drupal) | Plugins vulnerables, rutas predecibles |
| Framework (Laravel, Django, Rails) | Vulnerabilidades de versión, rutas de debug |
| Librerías JS (jQuery 1.x, Angular antiguo) | XSS, prototype pollution |
| CDN / WAF (Cloudflare, Akamai) | Condiciona la estrategia de explotación |

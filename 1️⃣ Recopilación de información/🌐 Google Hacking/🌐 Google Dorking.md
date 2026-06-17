# 🌐 Google Dorking

---

## ¿Qué es?

Google Dorking (o Google Hacking) consiste en usar los operadores de búsqueda avanzada de Google para encontrar información que no aparece en resultados normales: archivos expuestos, paneles de administración, credenciales filtradas, configuraciones incorrectas, etc.

La técnica en sí no es ilegal, pero el uso malicioso de la información obtenida sí puede serlo. Úsala siempre con autorización explícita sobre el objetivo.

---

## Operadores principales

### `site:`
Limita la búsqueda a un dominio o subdominio concreto.

```
site:ejemplo.com
site:*.ejemplo.com
```

### `filetype:`
Busca archivos con una extensión específica.

```
filetype:pdf site:ejemplo.com
filetype:sql
filetype:env
filetype:log
filetype:xml
```

### `inurl:`
Busca páginas que contengan el término en la URL.

```
inurl:admin site:ejemplo.com
inurl:login
inurl:wp-admin
```

### `intitle:`
Busca páginas donde el término aparezca en el título HTML.

```
intitle:"index of"
intitle:"panel de administración"
```

### `intext:`
Busca páginas donde el término aparezca en el cuerpo del texto.

```
intext:"contraseña" filetype:txt
intext:"DB_PASSWORD" filetype:env
```

### `"cadena exacta"`
Las comillas obligan a que la búsqueda sea literal, sin variaciones.

```
"Index of /" "parent directory"
```

---

## Dorks útiles por categoría

### Archivos de configuración expuestos

```
filetype:env DB_PASSWORD
filetype:ini [database]
filetype:cfg password
"wp-config.php" inurl:wp-config
```

### Paneles de administración

```
intitle:"phpMyAdmin" inurl:phpmyadmin
intitle:"RouterOS" inurl:webfig
inurl:"/admin/login.php"
inurl:"/wp-admin" site:objetivo.com
```

### Directorios abiertos

```
intitle:"index of" "parent directory"
intitle:"index of" passwd
intitle:"index of" .git
```

### Cámaras y dispositivos expuestos

```
inurl:"/view/index.shtml"
intitle:"Live View / - AXIS"
inurl:":8080/web/client"
```

### Credenciales filtradas

```
filetype:log "username" "password"
filetype:sql "INSERT INTO users"
intext:"password=" filetype:txt
```

---

## Combinar operadores

Los operadores se pueden combinar para afinar la búsqueda:

```
site:ejemplo.com filetype:pdf confidencial
site:ejemplo.com inurl:admin intitle:"login"
filetype:sql intext:"CREATE TABLE users" site:github.com
```

---

## Herramientas de apoyo

**[Exploit Database - Google Hacking Database (GHDB)](https://www.exploit-db.com/google-hacking-database)**
Colección enorme de dorks categorizados por tipo: paneles de administración, archivos sensibles, errores, etc. El punto de partida ideal para conocer qué se puede buscar.

**[Pentest-Tools](https://pentest-tools.com)**
Plataforma con herramientas de pentesting online. Su módulo de Google Dorking ofrece plantillas predefinidas que hacen las búsquedas más efectivas y eficientes.

---

## Notas de uso ético

- Usar dorks sobre sistemas propios o con autorización escrita del propietario.
- Google puede bloquear temporalmente IPs que lancen muchas búsquedas automáticas (CAPTCHA).
- Para automatizar dorks a escala, existen herramientas como `dorkbot` o `googler`, pero requieren especial cuidado legal.
- La información encontrada mediante dorking puede ser útil en la fase de reconocimiento pasivo de una auditoría, antes de cualquier interacción directa con el objetivo.

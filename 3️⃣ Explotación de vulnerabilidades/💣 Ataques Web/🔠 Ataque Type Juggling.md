# Type Juggling

El Type Juggling (o "confusión de tipos") explota el comportamiento de PHP en comparaciones débiles. PHP convierte automáticamente los tipos de datos al comparar valores con `==` (comparación laxa), lo que puede producir resultados inesperados que un atacante puede aprovechar para saltarse la autenticación.

---

## Comparación débil vs estricta en PHP

| Operador | Tipo de comparación | Ejemplo |
|---|---|---|
| `==` | Laxa (convierte tipos) | `"0e123" == "0e456"` → `true` |
| `===` | Estricta (tipo + valor) | `"0e123" === "0e456"` → `false` |

La raíz del problema es siempre el uso de `==` donde debería usarse `===`.

---

## Técnica 1: String → Array (PHP antiguo)

### Condiciones necesarias

- Versión de PHP antigua
- Comparación de contraseña con `strcmp()` sin validar el tipo de entrada

### Por qué funciona

`strcmp()` en versiones antiguas de PHP devuelve `0` (éxito) cuando uno de los argumentos es un array, en lugar de lanzar un error. Si la condición es `strcmp(...) == 0`, el login se bypasea.

### Explotación

Interceptar la petición de login con Burp Suite / Caido y cambiar el parámetro `password` de string a array:

**Petición original:**
```
usuario=admin&password=cualquiercosa
```

**Petición modificada:**
```
usuario=admin&password[]=
```

El campo `password` pasa a ser un array vacío. `strcmp([], $PASSWORD)` devuelve `0`, y la condición `== 0` es verdadera → acceso concedido.

---

## Técnica 2: Magic Hashes (valores exponenciales en MD5)

### Condiciones necesarias

- La contraseña se almacena y compara como hash MD5
- La comparación usa `==` en lugar de `===`

### Por qué funciona

PHP interpreta las cadenas que empiezan por `0e` seguidas de dígitos como notación científica (`0 × 10^N = 0`). Si tanto el hash almacenado como el hash del input empiezan por `0e`, PHP los considera iguales bajo `==`.

```php
"0e12345" == "0e67890"  // → true en PHP (ambos son 0 en notación científica)
```

### Strings mágicos cuyo MD5 empieza por 0e

```bash
echo -n "240610708" | md5sum    # 0e462097431906509019562988736854
echo -n "QNKCDZO"    | md5sum   # 0e830400451993494058024219903391
echo -n "aabg7XSs"   | md5sum   # 0e087386482136013740957780965295
echo -n "aabC9RqS"   | md5sum   # 0e041022518165728065344349536299
```

Si el hash almacenado en la base de datos empieza por `0e`, cualquiera de estas cadenas como contraseña pasará la validación `==`.

---

## Ejemplo de código vulnerable

```php
// VULNERABLE: usa == y strcmp con tipos no validados
$PASSWORD = 0e087386482136013740957780965295;

$password_input = md5($_POST['password']);

if ($password_input == $PASSWORD) {
    // acceso concedido
}
```

```php
// SEGURO: usa === y validación de tipo
if ($password_input === $PASSWORD) {
    // acceso concedido
}
```

---

## Laboratorio para practicar

Crear un archivo `/var/www/html/index.php` con el código vulnerable mostrado arriba y probar con los strings mágicos listados. Ver cómo `==` concede acceso mientras que `===` lo deniega.

---

## Mitigaciones

- Usar siempre `===` para comparaciones de contraseñas, hashes y tokens
- Validar el tipo de los parámetros recibidos antes de procesarlos (`is_string()`, `gettype()`)
- Usar `password_hash()` y `password_verify()` en PHP moderno, que gestionan todo esto correctamente
- Actualizar PHP a versiones modernas donde `strcmp()` con arrays lanza advertencias y no devuelve 0

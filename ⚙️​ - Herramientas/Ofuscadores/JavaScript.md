#Ofuscación [[0️⃣​ - JavaScript Deobfuscation]] 

Consola:
https://jsconsole.com/

# Ofuscación

## Minimizador de código

Muchas herramientas pueden ayudarnos a minimizar el código JavaScript, como [javascript-minificador](https://javascript-minifier.com/). Simplemente copiamos nuestro código y hacemos clic `Minify`y obtenemos la salida minificada a la derecha.

## Ofuscadores

Ofusquemos nuestra línea de código para que sea más oscura y difícil de leer. Primero, lo intentaremos [EmbellecerHerramientas](http://beautifytools.com/javascript-obfuscator.php) para ofuscar nuestro código

>**NOTA**
>El tipo anterior de ofuscación se conoce como "embalaje", que generalmente es reconocible a partir de los seis argumentos de función utilizados en la función inicial "function(p,a,c,k,e,d)".

Otros:
-  [JJ Codificar](https://utf-8.jp/public/jjencode.html) 
- [Codificación AA](https://utf-8.jp/public/aaencode.html)

## Ofuscador Avanzado

Visitamos [https://obfuscator.io](https://obfuscator.io/). Antes de hacer clic `obfuscate`, cambiaremos `String Array Encoding` a `Base64`, como se ve a continuación:

   

![](https://academy.hackthebox.com/storage/modules/41/js_deobf_obfuscator_2.jpg)


# Desofuscación

## Beautify

Vemos que el código actual que tenemos está escrito en una sola línea. Esto se conoce como `Minified JavaScript` código. Para formatear correctamente el código, necesitamos `Beautify` nuestro código. El método más básico para hacerlo es a través de nuestro `Browser Dev Tools`.

Por ejemplo, si estábamos usando Firefox, podemos abrir el depurador del navegador con [ `CTRL+SHIFT+Z` ], y luego haga clic en nuestro script `secret.js`. Esto mostrará el script en su formato original, pero podemos hacer clic en el '`{ }`' botón en la parte inferior, que hará `Pretty Print` el script en su formato JavaScript adecuado.

Además, podemos utilizar muchas herramientas en línea o complementos de editor de código, como [Prettier](https://prettier.io/playground/) o [Embellecer](https://beautifier.io/). Copiemos el `secret.js` guión

## Deobfuscate

Una buena herramienta es [Desempaquetador](https://matthewfl.com/unPacker.html). Intentemos copiar nuestro código ofuscado anterior y ejecutarlo en UnPacker haciendo clic en `UnPack` botón.

>**NOTA**
>Cuidado de dejar espacios antes del código ya que no funcionara correctamente.

# Codificación en respuesta API

Cubriremos 3 de los métodos de codificación de texto más utilizados:

## Base64

`base64` la codificación se utiliza generalmente para reducir el uso de caracteres especiales, como cualquier caracteres codificados en `base64` se representaría en caracteres alfanuméricos, además de `+` y `/` sólo. Independientemente de la entrada, incluso si está en formato binario, la cadena codificada base64 resultante solo los usaría.

#### Base64 Codificar

Para codificar cualquier texto en `base64` en Linux, podemos hacer eco y canalizarlo con '`|`' a `base64`:

```shell-session
vcrdcr@htb[/htb]$ echo https://www.hackthebox.eu/ | base64

aHR0cHM6Ly93d3cuaGFja3RoZWJveC5ldS8K
```

#### Decodificación Base64

Si queremos decodificar alguna `base64` cadena codificada, podemos usar `base64 -d`, como sigue:

  Decodificación

```shell-session
vcrdcr@htb[/htb]$ echo aHR0cHM6Ly93d3cuaGFja3RoZWJveC5ldS8K | base64 -d

https://www.hackthebox.eu/
```

## Hex

Otro método de codificación común es `hex` codificación, que codifica cada carácter en su `hex` orden en el `ASCII` mesa. Por ejemplo, `a` es `61` en hex, `b` es `62`, `c` es `63`y así sucesivamente. Puedes encontrar el completo `ASCII` tabla en Linux usando el `man ascii` comando.

#### Manchado Hex

Cualquier cadena codificada en `hex` estaría compuesto solo de caracteres hexagonales, que son solo 16 caracteres: 0-9 y a-f. Eso hace que detectar `hex` cadenas codificadas tan fáciles como detectar `base64` cadenas codificadas.

#### Codificar Hex

Para codificar cualquier cadena en `hex` en Linux, podemos usar el `xxd -p` comando:

  Codificación

```shell-session
vcrdcr@htb[/htb]$ echo https://www.hackthebox.eu/ | xxd -p

68747470733a2f2f7777772e6861636b746865626f782e65752f0a
```

#### Decodificar Hex

Para decodificar a `hex` cadena codificada, podemos utilizar el `xxd -p -r` comando:

  Decodificación

```shell-session
vcrdcr@htb[/htb]$ echo 68747470733a2f2f7777772e6861636b746865626f782e65752f0a | xxd -p -r

https://www.hackthebox.eu/
```


## César/Rot13

Otra técnica de codificación común y muy antigua es un cifrado César, que cambia cada letra por un número fijo. Por ejemplo, cambiar por 1 caracter hace `a` convertirse `b`, y `b` convertirse `c`y así sucesivamente. Muchas variaciones del cifrado César utilizan un número diferente de cambios, el más común de los cuales es `rot13`, que cambia cada carácter 13 veces hacia adelante.

#### Viendo César/Rot13

A pesar de que este método de codificación hace que cualquier texto parezca aleatorio, todavía es posible detectarlo porque cada carácter se asigna a un carácter específico. Por ejemplo, en `rot13`, `http://www` convertirse `uggc://jjj`, que todavía tiene algunas semejanzas y puede ser reconocido como tal.

#### Rot13 Codificar

No hay un comando específico en Linux para hacer `rot13` codificación. Sin embargo, es bastante fácil crear nuestro propio comando para hacer el cambio de personaje:

  Codificación

```shell-session
vcrdcr@htb[/htb]$ echo https://www.hackthebox.eu/ | tr 'A-Za-z' 'N-ZA-Mn-za-m'

uggcf://jjj.unpxgurobk.rh/
```

#### Rot13 Decodificar

Podemos usar el mismo comando anterior para decodificar rot13 también:

  Decodificación

```shell-session
vcrdcr@htb[/htb]$ echo uggcf://jjj.unpxgurobk.rh/ | tr 'A-Za-z' 'N-ZA-Mn-za-m'

https://www.hackthebox.eu/
```

Otra opción para codificar/descodificar rot13 sería usar una herramienta en línea, como [rot13](https://rot13.com/).


# Identificación de codificación

Algunas herramientas pueden ayudarnos a determinar automáticamente el tipo de codificación, como [Identificador de Cifrado](https://www.boxentriq.com/code-breaking/cipher-identifier). Pruebe las cadenas codificadas anteriores con [Identificador de Cifrado](https://www.boxentriq.com/code-breaking/cipher-identifier), para ver si puede identificar correctamente el método de codificación.


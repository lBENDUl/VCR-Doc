---
tags:
  - Web
  - Explotacion
  - Padding-Oracle
---

# Notas

## Introducción

Esta vulnerabilidad se usa para el cifrado por bloques

En el caso de que el texto no rellene todo el bloque (ya que el bloque tiene un tamaño ficho), estos bloques sobrantes se rellenan de caracteres aleatorios.
Por ejemplo en PKCS7:

![[Pasted image 20250416125703.png]]

**DESCIFRADO:**
Cuando una aplicación descifra los datos que ya están cifrados, lo que hace es descifrar primero el mensaje y después limpia el relleno.

Si durante la limpieza del relleno, tenemos un relleno invalido y tenemos un tipo de comportamiento detectable, hay es cuando decimos que es vulnerable.

El comportamiento detectable puede ser:
- Error
- Falta de resultado
- Respuesta más lenta 
- etc

Estos cifrados utilizan la operatoria XOR
![[Pasted image 20250416131034.png]]



## Práctica

### Descodificar y codificar

Hacemos login en una web, y por debajo nos ha dado una cookie de sesión, esta cookie viene principalmente de texto claro.
Por ejemplo:
**3U45ZrgkKaW3wGiEwijfk32p72vwyKOH**

Esto esta cifrado, para ver el texto descifrado, hay una herramienta llamada [[📟​ padbuster]].

Realizaremos lo siguiente:

```bash
padbuster http://192.168.1.17/index.php 3U45ZrgkKaW3wGiEwijfk32p72vwyKOH 8 -cookies 'auth=3U45ZrgkKaW3wGiEwijfk32p72vwyKOH'
```

**Salida:**

```
** Finished ***

[+] Decrypted value (ASCII): user=bendu

[+] Decrypted value (HEX): 757365723D62656E6475060606060606

[+] Decrypted value (Base64): dXNlcj1iZW5kdQYGBgYGBg==

-------------------------------------------------------
```

Ahora como podemos saber que tiene la cookie, ahora podemos realizar crear una nueva cookie para ganar acceso a las sesiones que tengan privilegios:

```bash
padbuster http://192.168.1.17/index.php 3U45ZrgkKaW3wGiEwijfk32p72vwyKOH 8 -cookies 'auth=3U45ZrgkKaW3wGiEwijfk32p72vwyKOH' -plaintext 'user=admin'
```

Esto nos dará una nueva cookie:

```
-------------------------------------------------------
** Finished ***

[+] Encrypted value is: BAitGdYuupMjA3gl1aFoOwAAAAAAAAAA
-------------------------------------------------------
```

Y si la ponemos en el navegador, en el caso de que haya un usuario llamado admin, tendremos su sesión.

### Bit flipper - Fuerza bruta

También se puede realizar fuerza bruta, la cadena va por bloques y los bloques que sean iguales, los bits van a seguir siendo iguales. Por ejemplo:

**bdmin** y **admin** van a tener la mayoría de bits iguales, solo cambiaran los bloques diferentes.

En la practica, por ejemplo nos creamos un usuario, por ejemplo bdmin.
Nos da la siguiente cookie:
**2BZXj0IZyOfcQao31JuTTPRp8RKu**

Abrimos Burp Suite e interceptamos la petición.

Enviamos la petición al Intruder.

Seleccionamos la cookie de sesión como payload

Ataque Sniper tipo Bit flipper

Y esto nos realiza una fuerza bruta con variante de la cookie que hemos seleccionado.

Si nos aparece una request con un length diferente, posiblemente hemos cogido una cookie valida para una sesión de admin o el usuario parecido.

**ESTO SERIA IR PROBANDO CON USUARIOS PARECIDOS**
# Hack4u

Un ataque de oráculo de relleno (**Padding Oracle Attack**) es un tipo de ataque contra datos cifrados que permite al atacante **descifrar** el contenido de los datos **sin conocer la clave**.

Un oráculo hace referencia a una “indicación” que brinda a un atacante información sobre si la acción que ejecuta es correcta o no. Imagina que estás jugando a un juego de mesa o de cartas con un niño: la cara se le ilumina con una gran sonrisa cuando cree que está a punto de hacer un buen movimiento. Eso es un oráculo. En tu caso, como oponente, puedes usar este oráculo para planear tu próximo movimiento según corresponda.

El relleno es un término criptográfico específico. Algunos cifrados, que son los algoritmos que se usan para cifrar los datos, funcionan en **bloques de datos** en los que cada bloque tiene un tamaño fijo. Si los datos que deseas cifrar no tienen el tamaño adecuado para rellenar los bloques, los datos se **rellenan** automáticamente hasta que lo hacen. Muchas formas de relleno requieren que este siempre esté presente, incluso si la entrada original tenía el tamaño correcto. Esto permite que el relleno siempre se quite de manera segura tras el descifrado.

Al combinar ambos elementos, una implementación de software con un oráculo de relleno revela si los datos descifrados tienen un relleno válido. El oráculo podría ser algo tan sencillo como devolver un valor que dice “Relleno no válido”, o bien algo más complicado como tomar un tiempo considerablemente diferente para procesar un bloque válido en lugar de uno no válido.

Los cifrados basados en bloques tienen otra propiedad, denominada “**modo**“, que determina la relación de los datos del primer bloque con los datos del segundo bloque, y así sucesivamente. Uno de los modos más usados es **CBC**. CBC presenta un bloque aleatorio inicial, conocido como “**vector de inicialización**” (**IV**), y combina el bloque anterior con el resultado del cifrado estático a fin de que cifrar el mismo mensaje con la misma clave no siempre genere la misma salida cifrada.

Un atacante puede usar un oráculo de relleno, en combinación con la manera de estructurar los datos de CBC, para enviar mensajes ligeramente modificados al código que expone el oráculo y seguir enviando datos hasta que el oráculo indique que son correctos. Desde esta respuesta, el atacante puede descifrar el mensaje byte a byte.

Las redes informáticas modernas son de una calidad tan alta que un atacante puede detectar diferencias muy pequeñas (menos de 0,1 ms) en el tiempo de ejecución en sistemas remotos. Las aplicaciones que suponen que un descifrado correcto solo puede ocurrir cuando no se alteran los datos pueden resultar vulnerables a ataques desde herramientas que están diseñadas para observar diferencias en el descifrado correcto e incorrecto. Si bien esta diferencia de temporalización puede ser más significativa en algunos lenguajes o bibliotecas que en otros, ahora se cree que se trata de una amenaza práctica para todos los lenguajes y las bibliotecas cuando se tiene en cuenta la respuesta de la aplicación ante el error.

Este tipo de ataque se basa en la capacidad de cambiar los datos cifrados y probar el resultado con el oráculo. La única manera de mitigar completamente el ataque es detectar los cambios en los datos cifrados y rechazar que se hagan acciones en ellos. La manera estándar de hacerlo es crear una firma para los datos y validarla antes de realizar cualquier operación. La firma debe ser verificable y el atacante no debe poder crearla; de lo contrario, podría modificar los datos cifrados y calcular una firma nueva en función de esos datos cambiados.

Un tipo común de firma adecuada se conoce como “**código de autenticación de mensajes hash con clave**” (**HMAC**). Un HMAC difiere de una suma de comprobación en que requiere una clave secreta, que solo conoce la persona que genera el HMAC y la persona que la valida. Si no se tiene esta clave, no se puede generar un HMAC correcto. Cuando recibes los datos, puedes tomar los datos cifrados, calcular de manera independiente el HMAC con la clave secreta que compartes tanto tú como el emisor y, luego, comparar el HMAC que este envía respecto del que calculaste. Esta comparación debe ser de tiempo constante; de lo contrario, habrás agregado otro oráculo detectable, permitiendo así un tipo de ataque distinto.

En resumen, para usar de manera segura los cifrados de bloques de CBC rellenados, es necesario combinarlos con un HMAC (u otra comprobación de integridad de datos) que se valide mediante una comparación de tiempo constante antes de intentar descifrar los datos. Dado que todos los mensajes modificados tardan el mismo tiempo en generar una respuesta, el ataque se evita.

El ataque de oráculo de relleno puede parecer un poco complejo de entender, ya que implica un proceso de retroalimentación para adivinar el contenido cifrado y modificar el relleno. Sin embargo, existen herramientas como **PadBuster** que pueden automatizar gran parte del proceso.

PadBuster es una herramienta diseñada para automatizar el proceso de descifrado de mensajes cifrados en modo **CBC** que utilizan relleno **PKCS #7**. La herramienta permite a los atacantes enviar peticiones HTTP con **rellenos maliciosos** para determinar si el relleno es válido o no. De esta forma, los atacantes pueden adivinar el contenido cifrado y descifrar todo el mensaje.

A continuación, se proporciona el enlace directo de descarga a la máquina ‘Padding Oracle’ de **Vulnhub**, la cual estaremos importando en VMWare para practicar esta vulnerabilidad:

- **Pentester Lab** **– Padding Oracle**: [https://www.vulnhub.com/?q=padding+oracle](https://www.vulnhub.com/?q=padding+oracle)

# Laboratorios

A continuación, se proporciona el enlace directo de descarga a la máquina ‘Padding Oracle’ de **Vulnhub**, la cual estaremos importando en VMWare para practicar esta vulnerabilidad:

- **Pentester Lab** **– Padding Oracle**: [https://www.vulnhub.com/?q=padding+oracle](https://www.vulnhub.com/?q=padding+oracle)


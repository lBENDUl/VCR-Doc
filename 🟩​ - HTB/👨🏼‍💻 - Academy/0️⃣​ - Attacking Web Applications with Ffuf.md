#HTB-Academy #Bug-Bounty-Hunter [[Ffuf]]  [[Fuzzing]] 


# Introduction

Bienvenido a la `Attacking Web Applications with Ffuf` ¡módulo!

Hay muchas herramientas y métodos para utilizar para el fuzzing de directorios y parámetros/brute-forcing. En este módulo nos centraremos principalmente en el [ffuf](https://github.com/ffuf/ffuf) herramienta para fuzzing web, ya que es una de las herramientas más comunes y confiables disponibles para fuzzing web.

Se discutirán los siguientes temas:

- Fuzzing para directorios
- Fuzzing para archivos y extensiones
- Identificación de vhosts ocultos
- Fuzzing para parámetros PHP
- Fuzzing para valores de parámetros

Herramientas como `ffuf` proporcione una forma automatizada práctica de borrar los componentes individuales de la aplicación web o una página web. Esto significa, por ejemplo, que utilizamos una lista que se utiliza para enviar solicitudes al servidor web si la página con el nombre de nuestra lista existe en el servidor web. Si obtenemos un código de respuesta 200, entonces sabemos que esta página existe en el servidor web y podemos verla manualmente.

# Web Fuzzing

Comenzaremos aprendiendo los conceptos básicos de uso `ffuf` para difuminar sitios web para directorios. Realizamos el ejercicio en la siguiente pregunta, y visitamos la URL que nos da, y vemos el siguiente sitio web:

   

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_main_site.jpg)

El sitio web no tiene enlaces a nada más, ni nos da ninguna información que pueda llevarnos a más páginas. Entonces, parece que nuestra única opción es '`fuzz`' el sitio web.

---

## Fuzzing

El término `fuzzing` se refiere a una técnica de prueba que envía varios tipos de entrada de usuario a una determinada interfaz para estudiar cómo reaccionaría. Si estuviéramos buscando vulnerabilidades de inyección SQL, estaríamos enviando caracteres especiales aleatorios y viendo cómo reaccionaría el servidor. Si estuviéramos borrando para un desbordamiento de búfer, estaríamos enviando cadenas largas e incrementando su longitud para ver si y cuándo se rompería el binario.

Por lo general, utilizamos listas de palabras predefinidas de términos de uso común para cada tipo de prueba de fuzzing web para ver si el servidor web las aceptaría. Esto se hace porque los servidores web no suelen proporcionar un directorio de todos los enlaces y dominios disponibles (a menos que estén terriblemente configurados), por lo que tendríamos que verificar varios enlaces y ver cuáles devuelven las páginas. Por ejemplo, si visitamos [https://www.hackthebox.eu/doesnotexist](https://www.hackthebox.eu/doesnotexist), obtendríamos un código HTTP `404 Page Not Found`, y vea la página siguiente:

   

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_HTB_404.jpg)

Sin embargo, si visitamos una página que existe, como `/login`, obtendríamos la página de inicio de sesión y obtendríamos un código HTTP `200 OK`, y vea la página siguiente:

   

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_HTB_login.jpg)

Esta es la idea básica detrás del fuzzing web para páginas y directorios. Aún así, no podemos hacer esto manualmente, ya que llevará una eternidad. Es por eso que tenemos herramientas que lo hacen de forma automática, eficiente y muy rápida. Dichas herramientas envían cientos de solicitudes cada segundo, estudian el código HTTP de respuesta y determinan si la página existe o no. Por lo tanto, podemos determinar rápidamente qué páginas existen y luego examinarlas manualmente para ver su contenido.

---

## Listas de palabras

Para determinar qué páginas existen, deberíamos tener una lista de palabras que contenga palabras de uso común para directorios y páginas web, muy similar a una `Password Dictionary Attack`, que discutiremos más adelante en el módulo. Aunque esto no revelará todas las páginas bajo un sitio web específico, ya que algunas páginas se nombran al azar o usan nombres únicos, en general, esto devuelve la mayoría de las páginas, alcanzando una tasa de éxito de hasta el 90% en algunos sitios web.

No tendremos que reinventar la rueda creando manualmente estas listas de palabras, ya que se han hecho grandes esfuerzos para buscar en la web y determinar las palabras más utilizadas para cada tipo de fuzzing. Algunas de las listas de palabras más utilizadas se pueden encontrar en GitHub [Seclistas](https://github.com/danielmiessler/SecLists) repositorio, que categoriza las listas de palabras bajo varios tipos de fuzzing, incluso incluyendo contraseñas de uso común, que luego utilizaremos para Password Brute Forcing.

Dentro de nuestro PwnBox, podemos encontrar todo `SecLists` repo disponible bajo `/opt/useful/SecLists`. La lista de palabras específica que utilizaremos para páginas y fuzzing de directorios es otra lista de palabras comúnmente utilizada llamada `directory-list-2.3`y está disponible en varias formas y tamaños. Podemos encontrar el que usaremos en:

  Fuzzing Web

```shell-session
vcrdcr@htb[/htb]$ locate directory-list-2.3-small.txt

/opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
```

Consejo: Echando un vistazo a esta lista de palabras, notaremos que contiene comentarios de derechos de autor al principio, que pueden considerarse como parte de la lista de palabras y desordenar los resultados. Podemos utilizar lo siguiente en `ffuf` para deshacerse de estas líneas con el `-ic` bandera.

# Directory Fuzzing

Ahora que entendemos el concepto de Web Fuzzing y conocemos nuestra lista de palabras, deberíamos estar listos para comenzar a usar `ffuf` para encontrar directorios de sitios web.

---

## Ffuf

`Ffuf` está preinstalado en su instancia de PwnBox. Si desea usarlo en su propia máquina, puede usar "`apt install ffuf -y`"o descárgalo y úsalo desde su [Repo GitHub](https://github.com/ffuf/ffuf.git). Como nuevo usuario de esta herramienta, comenzaremos emitiendo el `ffuf -h` comando para ver cómo se pueden utilizar las herramientas:

  Directorio Fuzzing

```shell-session
vcrdcr@htb[/htb]$ ffuf -h

HTTP OPTIONS:
  -H               Header `"Name: Value"`, separated by colon. Multiple -H flags are accepted.
  -X               HTTP method to use (default: GET)
  -b               Cookie data `"NAME1=VALUE1; NAME2=VALUE2"` for copy as curl functionality.
  -d               POST data
  -recursion       Scan recursively. Only FUZZ keyword is supported, and URL (-u) has to end in it. (default: false)
  -recursion-depth Maximum recursion depth. (default: 0)
  -u               Target URL
...SNIP...

MATCHER OPTIONS:
  -mc              Match HTTP status codes, or "all" for everything. (default: 200,204,301,302,307,401,403)
  -ms              Match HTTP response size
...SNIP...

FILTER OPTIONS:
  -fc              Filter HTTP status codes from response. Comma separated list of codes and ranges
  -fs              Filter HTTP response size. Comma separated list of sizes and ranges
...SNIP...

INPUT OPTIONS:
...SNIP...
  -w               Wordlist file path and (optional) keyword separated by colon. eg. '/path/to/wordlist:KEYWORD'

OUTPUT OPTIONS:
  -o               Write output to file
...SNIP...

EXAMPLE USAGE:
  Fuzz file paths from wordlist.txt, match all responses but filter out those with content-size 42.
  Colored, verbose output.
    ffuf -w wordlist.txt -u https://example.org/FUZZ -mc all -fs 42 -c -v
...SNIP...
```

Como podemos ver, el `help` la salida es bastante grande, por lo que solo guardamos las opciones que pueden ser relevantes para nosotros en este módulo.

---

## Directorio Fuzzing

Como podemos ver en el ejemplo anterior, las dos opciones principales son `-w` para listas de palabras y `-u` para la URL. Podemos asignar una lista de palabras a una palabra clave para referirnos a ella donde queremos fuzz. Por ejemplo, podemos elegir nuestra lista de palabras y asignar la palabra clave `FUZZ` añadiéndole `:FUZZ` después de eso:

  Directorio Fuzzing

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ
```

A continuación, como queremos ser fuzzing para directorios web, podemos colocar el `FUZZ` palabra clave donde el directorio estaría dentro de nuestra URL, con:

  Directorio Fuzzing

```shell-session
vcrdcr@htb[/htb]$ ffuf -w <SNIP> -u http://SERVER_IP:PORT/FUZZ
```

Ahora, comencemos nuestro objetivo en la pregunta a continuación y ejecutemos nuestro comando final:

  Directorio Fuzzing

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://SERVER_IP:PORT/FUZZ
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

<SNIP>
blog                    [Status: 301, Size: 326, Words: 20, Lines: 10]
:: Progress: [87651/87651] :: Job [1/1] :: 9739 req/sec :: Duration: [0:00:09] :: Errors: 0 ::
```

Vemos eso `ffuf` probado para casi 90k URLs en menos de 10 segundos. Esta velocidad puede variar según la velocidad de Internet y el ping si lo usó `ffuf` en su máquina, pero aún debe ser extremadamente rápido.

Incluso podemos hacerlo ir más rápido si tenemos prisa al aumentar el número de hilos a 200, por ejemplo, con `-t 200`, pero esto no se recomienda, especialmente cuando se usa en un sitio remoto, ya que puede interrumpirlo y causar una `Denial of Service`o reduzca su conexión a Internet en casos graves. Recibimos un par de visitas, y podemos visitar una de ellas para verificar que existe:

   
http://SERVER_IP:PORT/blog
![](https://academy.hackthebox.com/storage/modules/54/web_fnb_blog.jpg)

Obtenemos una página vacía, lo que indica que el directorio no tiene una página dedicada, pero también muestra que sí tenemos acceso a ella, ya que no obtenemos un código HTTP `404 Not Found` o `403 Access Denied`. En la siguiente sección, buscaremos páginas debajo de este directorio para ver si está realmente vacío o si tiene archivos y páginas ocultos.

# Page Fuzzing

Ahora entendemos el uso básico de `ffuf` mediante la utilización de listas de palabras y palabras clave. A continuación, aprenderemos cómo localizar páginas.

Nota: Podemos generar el mismo objetivo de la sección anterior para los ejemplos de esta sección también.

---

## Extensión Fuzzing

En la sección anterior, descubrimos que teníamos acceso a `/blog`, pero el directorio devolvió una página vacía, y no podemos localizar manualmente ningún enlace o página. Por lo tanto, una vez más utilizaremos el fuzzing web para ver si el directorio contiene páginas ocultas. Sin embargo, antes de comenzar, debemos averiguar qué tipos de páginas utiliza el sitio web, como `.html`, `.aspx`, `.php`, o algo más.

Una forma común de identificar eso es encontrar el tipo de servidor a través de los encabezados de respuesta HTTP y adivinar la extensión. Por ejemplo, si el servidor es `apache`, entonces puede ser `.php`o si lo fue `IIS`, entonces podría ser `.asp` o `.aspx`y así sucesivamente. Sin embargo, este método no es muy práctico. Entonces, volveremos a utilizar `ffuf` para difuminar la extensión, similar a cómo fuzzed para directorios. En lugar de colocar el `FUZZ` palabra clave donde estaría el nombre del directorio, lo colocaríamos donde estaría la extensión `.FUZZ`y use una lista de palabras para extensiones comunes. Podemos utilizar la siguiente lista de palabras en `SecLists` para extensiones:

  Página Fuzzing

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ <SNIP>
```

¡Antes de comenzar a fuzzing, debemos especificar qué archivo sería esa extensión al final de! Siempre podemos usar dos listas de palabras y tener una palabra clave única para cada una, y luego hacerlo `FUZZ_1.FUZZ_2` para fuzz para ambos. Sin embargo, hay un archivo que siempre podemos encontrar en la mayoría de los sitios web, que es `index.*`, por lo que lo usaremos como nuestras extensiones de archivo y fuzz en él.

Nota: La lista de palabras que elegimos ya contiene un punto (.), por lo que no tendremos que agregar el punto después del "índice" en nuestro fuzzing.

Ahora, podemos volver a ejecutar nuestro comando, colocando cuidadosamente nuestro `FUZZ` palabra clave donde estaría la extensión después `index`:

  Página Fuzzing

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://SERVER_IP:PORT/blog/indexFUZZ
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 5
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

.php                    [Status: 200, Size: 0, Words: 1, Lines: 1]
.phps                   [Status: 403, Size: 283, Words: 20, Lines: 10]
:: Progress: [39/39] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Errors: 0 ::
```

Tenemos un par de visitas, pero solo `.php` nos da una respuesta con código `200`. ¡Genial! Ahora sabemos que este sitio web se ejecuta en `PHP` para empezar a fuzzing `PHP` archivos.

---

## Página Fuzzing

Ahora usaremos el mismo concepto de palabras clave con las que hemos estado usando `ffuf`, uso `.php` como la extensión, coloque nuestro `FUZZ` palabra clave donde debería estar el nombre del archivo y use la misma lista de palabras que usamos para difuminar directorios:

  Página Fuzzing

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/blog/FUZZ.php


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://SERVER_IP:PORT/blog/FUZZ.php
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

index                   [Status: 200, Size: 0, Words: 1, Lines: 1]
REDACTED                [Status: 200, Size: 465, Words: 42, Lines: 15]
:: Progress: [87651/87651] :: Job [1/1] :: 5843 req/sec :: Duration: [0:00:15] :: Errors: 0 ::
```

Obtenemos un par de hits; ambos tienen un código HTTP 200, lo que significa que podemos acceder a ellos. index.php tiene un tamaño de 0, lo que indica que es una página vacía, mientras que la otra no, lo que significa que tiene contenido. Podemos visitar cualquiera de estas páginas para verificar esto:

   
http://SERVER_IP:PORT/blog/REDACTED.php
![](https://academy.hackthebox.com/storage/modules/54/web_fnb_login.jpg)

# Recursive Fuzzing

Hasta ahora, hemos estado difuminando directorios, luego pasando por debajo de estos directorios y luego difuminando archivos. Sin embargo, si tuviéramos docenas de directorios, cada uno con sus propios subdirectorios y archivos, esto tomaría mucho tiempo en completarse. Para poder automatizar esto, utilizaremos lo que se conoce como `recursive fuzzing`.

---

## Banderas Recursivas

Cuando escaneamos recursivamente, inicia automáticamente otro escaneo bajo cualquier directorio recién identificado que pueda tener en sus páginas hasta que haya borrado el sitio web principal y todos sus subdirectorios.

Algunos sitios web pueden tener un gran árbol de subdirectorios, como `/login/user/content/uploads/...etc`y esto expandirá el árbol de escaneo y puede tomar mucho tiempo escanearlos a todos. Es por eso que siempre se recomienda especificar un `depth` para nuestro escaneo recursivo, de modo que no escanee directorios que sean más profundos que esa profundidad. Una vez que borramos los primeros directorios, podemos elegir los directorios más interesantes y ejecutar otro escaneo para dirigir mejor nuestro escaneo.

En `ffuf`, podemos habilitar el escaneo recursivo con el `-recursion` bandera, y podemos especificar la profundidad con el `-recursion-depth` bandera. Si especificamos `-recursion-depth 1`únicamente difuminará los directorios principales y sus subdirectorios directos. Si se identifican sub-sub-directorios (como `/login/user`, no los difuminará para las páginas). Cuando se utiliza la recursión en `ffuf`, podemos especificar nuestra extensión con `-e .php`

Nota: todavía podemos usar 'p.phpurg como nuestra extensión de página, ya que estas extensiones suelen ser en todo el sitio.

Finalmente, también agregaremos la bandera `-v` para emitir las URL completas. De lo contrario, puede ser difícil saber cuál `.php` el archivo se encuentra debajo de qué directorio.

---

## Escaneo Recursivo

Repitamos el primer comando que usamos, agreguemos los indicadores de recursión mientras especificamos `.php` como nuestra extensión, y ver qué resultados obtenemos:

  Fuzzing Recursivo

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ -recursion -recursion-depth 1 -e .php -v


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://SERVER_IP:PORT/FUZZ
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt
 :: Extensions       : .php 
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

[Status: 200, Size: 986, Words: 423, Lines: 56] | URL | http://SERVER_IP:PORT/
    * FUZZ: 

[INFO] Adding a new job to the queue: http://SERVER_IP:PORT/forum/FUZZ
[Status: 200, Size: 986, Words: 423, Lines: 56] | URL | http://SERVER_IP:PORT/index.php
    * FUZZ: index.php

[Status: 301, Size: 326, Words: 20, Lines: 10] | URL | http://SERVER_IP:PORT/blog | --> | http://SERVER_IP:PORT/blog/
    * FUZZ: blog

<...SNIP...>
[Status: 200, Size: 0, Words: 1, Lines: 1] | URL | http://SERVER_IP:PORT/blog/index.php
    * FUZZ: index.php

[Status: 200, Size: 0, Words: 1, Lines: 1] | URL | http://SERVER_IP:PORT/blog/
    * FUZZ: 

<...SNIP...>
```

Como podemos ver esta vez, el escaneo tomó mucho más tiempo, envió casi seis veces el número de solicitudes, y la lista de palabras se duplicó en tamaño (una vez con `.php` y una vez sin). Aún así, obtuvimos una gran cantidad de resultados, incluidos todos los resultados que identificamos anteriormente, todos con una sola línea de comando.

# DNS Records

Una vez que accedemos a la página debajo `/blog`, tenemos un mensaje diciendo `Admin panel moved to academy.htb`. Si visitamos el sitio web en nuestro navegador, obtenemos `can’t connect to the server at www.academy.htb`:

   
http://academy.htb:PORT
![](https://academy.hackthebox.com/storage/modules/54/web_fnb_cant_connect_academy.jpg)

Esto se debe a que los ejercicios que hacemos no son sitios web públicos a los que pueda acceder cualquier persona que no sean sitios web locales dentro de HTB. Los navegadores solo entienden cómo ir a las IP, y si les proporcionamos una URL, intentan asignar la URL a una IP mirando al local `/etc/hosts` archivo y el DNS público `Domain Name System`. Si la URL tampoco está, no sabría cómo conectarse a ella.

Si visitamos la IP directamente, el navegador va directamente a esa IP y sabe cómo conectarse a ella. Pero en este caso, le decimos que vaya a `academy.htb`, entonces mira al local `/etc/hosts` archiva y no encuentra ninguna mención de ello. Le pregunta al DNS público al respecto (como el DNS de Google `8.8.8.8`) y no encuentra ninguna mención de la misma, ya que no es un sitio web público, y finalmente no se conecta. Entonces, para conectarse a `academy.htb`, tendríamos que agregarlo a nuestro `/etc/hosts` archivo. Podemos lograr eso con el siguiente comando:

  registros DNS

```shell-session
vcrdcr@htb[/htb]$ sudo sh -c 'echo "SERVER_IP  academy.htb" >> /etc/hosts'
```

Ahora podemos visitar el sitio web (no olvide agregar el PUERTO en la URL) y ver que podemos llegar al sitio web:

   
http://academy.htb:PORT
![](https://academy.hackthebox.com/storage/modules/54/web_fnb_main_site.jpg)

Sin embargo, obtenemos el mismo sitio web que obtuvimos cuando visitamos la IP directamente, por lo que `academy.htb` es el mismo dominio que hemos estado probando hasta ahora. Podemos verificar eso visitando `/blog/index.php`, y ver que podemos acceder a la página.

Cuando ejecutamos nuestras pruebas en esta IP, no encontramos nada al respecto `admin` o paneles, incluso cuando hicimos un completo `recursive` escanea nuestro objetivo. `So, in this case, we start looking for sub-domains under '*.academy.htb' and see if we find anything, which is what we will attempt in the next section.`

# Sub-Domain Fuzzing

En esta sección, aprenderemos a usar `ffuf` para identificar subdominios (es decir, `*.website.com`) para cualquier sitio web.

---

## Subdominios

Un subdominio es cualquier sitio web subyacente a otro dominio. Por ejemplo, `https://photos.google.com` es el `photos` subdominio de `google.com`.

En este caso, simplemente estamos revisando diferentes sitios web para ver si existen al verificar si tienen un registro DNS público que nos redirigiría a una IP de servidor en funcionamiento. Entonces, hagamos un escaneo y veamos si recibimos algún golpe. Antes de que podamos comenzar nuestro escaneo, necesitamos dos cosas:

- A `wordlist`
- A `target`

Por suerte para nosotros, en el `SecLists` repo, hay una sección específica para las listas de palabras de subdominio, que consiste en palabras comunes que generalmente se usan para subdominios. Podemos encontrarlo en `/opt/useful/seclists/Discovery/DNS/`. En nuestro caso, estaríamos usando una lista de palabras más corta, que es `subdomains-top1million-5000.txt`. Si queremos extender nuestro escaneo, podemos elegir una lista más grande.

En cuanto a nuestro objetivo, lo usaremos `inlanefreight.com` como nuestro objetivo y ejecute nuestro escaneo en él. Usemos `ffuf` y colocar el `FUZZ` palabra clave en el lugar de los subdominios, y ver si obtenemos algún golpe:

  Subdominio Fuzzing

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u https://FUZZ.inlanefreight.com/


        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : https://FUZZ.inlanefreight.com/
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

[Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 381ms]
    * FUZZ: support

[Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 385ms]
    * FUZZ: ns3

[Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 402ms]
    * FUZZ: blog

[Status: 301, Size: 0, Words: 1, Lines: 1, Duration: 180ms]
    * FUZZ: my

[Status: 200, Size: 22266, Words: 2903, Lines: 316, Duration: 589ms]
    * FUZZ: www

<...SNIP...>
```

Vemos que recuperamos algunos golpes. Ahora, podemos intentar ejecutar lo mismo `academy.htb` y ver si recibimos algún golpe:

  Subdominio Fuzzing

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.academy.htb/


        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : https://FUZZ.academy.htb/
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

:: Progress: [4997/4997] :: Job [1/1] :: 131 req/sec :: Duration: [0:00:38] :: Errors: 4997 ::
```

Vemos que no recibimos ningún golpe. Esto significa que no hay subdominio bajo `academy.htb`? - No.

Esto significa que no hay `public` subdominios bajo `academy.htb`, ya que no tiene un registro DNS público, como se mencionó anteriormente. Aunque añadimos `academy.htb` a nuestro `/etc/hosts` archivo, solo agregamos el dominio principal, así que cuando `ffuf` está buscando otros subdominios, no los encontrará `/etc/hosts`y le preguntará al DNS público, que obviamente no los tendrá.

# Vhost Fuzzing

Como vimos en la sección anterior, pudimos fuzz subdominios públicos utilizando registros DNS públicos. Sin embargo, cuando se trataba de subdominios difusos que no tienen un registro DNS público o subdominios en sitios web que no son públicos, no podíamos usar el mismo método. En esta sección, aprenderemos cómo hacerlo con `Vhost Fuzzing`.

---

## Vhosts vs. Subdominios

La diferencia clave entre VHosts y subdominios es que un VHost es básicamente un 'subdominio' servido en el mismo servidor y tiene la misma IP, de modo que una sola IP podría estar sirviendo a dos o más sitios web diferentes.

`VHosts may or may not have public DNS records.`

En muchos casos, muchos sitios web en realidad tendrían subdominios que no son públicos y no los publicarán en registros DNS públicos, y por lo tanto, si los visitamos en un navegador, no podríamos conectarnos, ya que el DNS público no sabría su IP. Una vez más, si usamos el `sub-domain fuzzing`únicamente podríamos identificar subdominios públicos, pero no identificaremos ningún subdominio que no sea público.

Aquí es donde utilizamos `VHosts Fuzzing` en una IP que ya tenemos. Ejecutaremos un escaneo y una prueba para escaneos en la misma IP, y luego podremos identificar subdominios y VHosts públicos y no públicos.

---

## Vhosts Fuzzing

Para buscar VHosts, sin agregar manualmente toda la lista de palabras a nuestro `/etc/hosts`, estaremos borrando encabezados HTTP, específicamente el `Host:` encabezado. Para hacer eso, podemos usar el `-H` bandera para especificar un encabezado y usará el `FUZZ` palabra clave dentro de ella, de la siguiente manera:

  Fuzzing Vhost

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb'


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://academy.htb:PORT/
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
________________________________________________

mail2                   [Status: 200, Size: 900, Words: 423, Lines: 56]
dns2                    [Status: 200, Size: 900, Words: 423, Lines: 56]
ns3                     [Status: 200, Size: 900, Words: 423, Lines: 56]
dns1                    [Status: 200, Size: 900, Words: 423, Lines: 56]
lists                   [Status: 200, Size: 900, Words: 423, Lines: 56]
webmail                 [Status: 200, Size: 900, Words: 423, Lines: 56]
static                  [Status: 200, Size: 900, Words: 423, Lines: 56]
web                     [Status: 200, Size: 900, Words: 423, Lines: 56]
www1                    [Status: 200, Size: 900, Words: 423, Lines: 56]
<...SNIP...>
```

Vemos que todas las palabras en la lista de palabras están regresando `200 OK`¡! Esto se espera, ya que simplemente estamos cambiando el encabezado mientras visitamos `http://academy.htb:PORT/`. Entonces, sabemos que siempre obtendremos `200 OK`. Sin embargo, si el VHost existe y enviamos uno correcto en el encabezado, deberíamos obtener un tamaño de respuesta diferente, como en ese caso, estaríamos obteniendo la página de ese VHosts, que es probable que muestre una página diferente.

# Filtering Results

Hasta ahora, no hemos estado utilizando ningún filtrado para nuestros `ffuf`y los resultados se filtran automáticamente de forma predeterminada por su código HTTP, que filtra el código `404 NOT FOUND`y mantiene el resto. Sin embargo, como vimos en nuestra anterior carrera de `ffuf`, podemos obtener muchas respuestas con el código `200`. Entonces, en este caso, tendremos que filtrar los resultados en función de otro factor, que aprenderemos en esta sección.

---

## Filtrado

`Ffuf` proporciona la opción de hacer coincidir o filtrar un código HTTP específico, el tamaño de la respuesta o la cantidad de palabras. Podemos ver eso con `ffuf -h`:

  Filtrado de Resultados

```shell-session
vcrdcr@htb[/htb]$ ffuf -h
...SNIP...
MATCHER OPTIONS:
  -mc              Match HTTP status codes, or "all" for everything. (default: 200,204,301,302,307,401,403)
  -ml              Match amount of lines in response
  -mr              Match regexp
  -ms              Match HTTP response size
  -mw              Match amount of words in response

FILTER OPTIONS:
  -fc              Filter HTTP status codes from response. Comma separated list of codes and ranges
  -fl              Filter by amount of lines in response. Comma separated list of line counts and ranges
  -fr              Filter regexp
  -fs              Filter HTTP response size. Comma separated list of sizes and ranges
  -fw              Filter by amount of words in response. Comma separated list of word counts and ranges
<...SNIP...>
```

En este caso, no podemos usar la coincidencia, ya que no sabemos cuál sería el tamaño de respuesta de otros VHosts. Sabemos el tamaño de respuesta de los resultados incorrectos, que, como se ve en la prueba anterior, es `900`y podemos filtrarlo con `-fs 900`. Ahora, repitamos el mismo comando anterior, agreguemos la bandera anterior y veamos qué obtenemos:

  Filtrado de Resultados

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -fs 900


       /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://academy.htb:PORT/
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt
 :: Header           : Host: FUZZ.academy.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: 900
________________________________________________

<...SNIP...>
admin                   [Status: 200, Size: 0, Words: 1, Lines: 1]
:: Progress: [4997/4997] :: Job [1/1] :: 1249 req/sec :: Duration: [0:00:04] :: Errors: 0 ::
```

Podemos verificarlo visitando la página, y viendo si podemos conectarnos a ella:

   

![](https://academy.hackthebox.com/storage/modules/54/web_fnb_blog.jpg)

Nota 1: No olvide agregar "admin.academy.htb" a "/etc/hosts".

Nota 2: Si su ejercicio se ha reiniciado, asegúrese de tener el puerto correcto al visitar el sitio web.

Vemos que podemos acceder a la página, pero obtenemos una página vacía, a diferencia de lo que tenemos `academy.htb`, por lo tanto, confirmar esto es de hecho un VHost diferente. Incluso podemos visitar `https://admin.academy.htb:PORT/blog/index.php`, y veremos que obtendríamos un `404 PAGE NOT FOUND`, confirmando que ahora estamos realmente en un VHost diferente.

Intente ejecutar un escaneo recursivo en `admin.academy.htb`y vea qué páginas puede identificar.

# Parameter Fuzzing-GET

Si corremos un recursivo `ffuf` escanear en `admin.academy.htb`, deberíamos encontrar `http://admin.academy.htb:PORT/admin/admin.php`. Si intentamos acceder a esta página, vemos lo siguiente:

   
http://admin.academy.htb:PORT/admin/admin.php
![](https://academy.hackthebox.com/storage/modules/54/web_fnb_admin.jpg)

Eso indica que debe haber algo que identifique a los usuarios para verificar si tienen acceso a leer el `flag`. No iniciamos sesión, ni tenemos ninguna cookie que pueda verificarse en el backend. Entonces, tal vez haya una clave que podamos pasar a la página para leer el `flag`. Tales llaves se pasarían normalmente como un `parameter`, usando cualquiera de a `GET` o a `POST` Solicitud HTTP. Esta sección discutirá cómo fuzz para tales parámetros hasta que identifiquemos un parámetro que puede ser aceptado por la página.

**Consejo:** Los parámetros de fuzzing pueden exponer parámetros no publicados que son de acceso público. Dichos parámetros tienden a ser menos probados y menos seguros, por lo que es importante probar dichos parámetros para las vulnerabilidades web que discutimos en otros módulos.

---

## GET Solicitar Fuzzing

De manera similar a cómo hemos estado difuminando varias partes de un sitio web, usaremos `ffuf` para enumerar parámetros. Empecemos primero con fuzzing para `GET` solicitudes, que generalmente se pasan justo después de la URL, con un `?` símbolo, como:

- `http://admin.academy.htb:PORT/admin/admin.php?param1=key`.

Entonces, todo lo que tenemos que hacer es reemplazar `param1` en el ejemplo anterior con `FUZZ` y vuelva a ejecutar nuestro escaneo. Sin embargo, antes de que podamos comenzar, debemos elegir una lista de palabras adecuada. Una vez más, `SecLists` tiene exactamente eso adentro `/opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt`. Con eso, podemos ejecutar nuestro escaneo.

Una vez más, recuperaremos muchos resultados, por lo que filtraremos el tamaño de respuesta predeterminado que estamos obteniendo.

  Parámetro Fuzzing - GET

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key -fs xxx


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : GET
 :: URL              : http://admin.academy.htb:PORT/admin/admin.php?FUZZ=key
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

<...SNIP...>                    [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]
```

Recibimos un golpe de vuelta. Intentemos visitar la página y agregar esto `GET` parámetro, y ver si podemos leer la bandera ahora:

   
http://admin.academy.htb:PORT/admin/admin.php?REDACTED=key
![](https://academy.hackthebox.com/storage/modules/54/web_fnb_admin_param1.jpg)

As we can see, the only hit we got back has been `deprecated` and appears to be no longer in use.

# Parameter Fuzzing-POST

La principal diferencia entre `POST` solicitudes y `GET` las solicitudes son eso `POST` las solicitudes no se pasan con la URL y no se pueden agregar simplemente después de un `?` símbolo. `POST` las solicitudes se pasan en el `data` campo dentro de la solicitud HTTP. Mira el [Solicitudes Web](https://academy.hackthebox.com/module/details/35) módulo para obtener más información sobre las solicitudes HTTP.

Para fuzz el `data` campo con `ffuf`, podemos usar el `-d` bandera, como vimos anteriormente en la salida de `ffuf -h`. También tenemos que agregar `-X POST` para enviar `POST` solicitudes.

Consejo: En PHP, los datos "POST" "content-type" solo pueden aceptar "application/x-www-form-urlencoded". Por lo tanto, podemos establecer eso en "ffuf" con "-H 'Content-Type: application/x-www-form-urlencoded'".

Entonces, repitamos lo que hicimos antes, pero coloquemos nuestro `FUZZ` palabra clave después del `-d` bandera:

  Parámetro Fuzzing - POST

```shell-session
vcrdcr@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'FUZZ=key' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx


        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v1.1.0-git
________________________________________________

 :: Method           : POST
 :: URL              : http://admin.academy.htb:PORT/admin/admin.php
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : FUZZ=key
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

id                      [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]
<...SNIP...>
```

Como podemos ver esta vez, tenemos un par de éxitos, el mismo que obtuvimos al fuzzing `GET` y otro parámetro, que es `id`. Veamos qué obtenemos si enviamos un `POST` solicitud con el `id` parámetro. Podemos hacer eso con `curl`, como sigue:

  Parámetro Fuzzing - POST

```shell-session
vcrdcr@htb[/htb]$ curl http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=key' -H 'Content-Type: application/x-www-form-urlencoded'

<div class='center'><p>Invalid id!</p></div>
<...SNIP...>
```

Como podemos ver, el mensaje ahora dice `Invalid id!`.

# Value Fuzzing

Después de fuzzing un parámetro de trabajo, ahora tenemos que fuzz el valor correcto que devolvería el `flag` contenido que necesitamos. Esta sección discutirá el fuzzing para los valores de los parámetros, que debería ser bastante similar al fuzzing para los parámetros, una vez que desarrollemos nuestra lista de palabras.

---

## Lista de palabras personalizada

Cuando se trata de difuminar valores de parámetros, es posible que no siempre encontremos una lista de palabras prefabricada que funcione para nosotros, ya que cada parámetro esperaría un cierto tipo de valor.

Para algunos parámetros, como los nombres de usuario, podemos encontrar una lista de palabras prefabricada para nombres de usuario potenciales, o podemos crear la nuestra basada en usuarios que potencialmente pueden estar usando el sitio web. Para tales casos, podemos buscar varias listas de palabras bajo el `seclists` directorio e intente encontrar uno que pueda contener valores que coincidan con el parámetro al que nos dirigimos. En otros casos, como los parámetros personalizados, es posible que tengamos que desarrollar nuestra propia lista de palabras. En este caso, podemos adivinar que el `id` el parámetro puede aceptar una entrada de número de algún tipo. Estos identificadores pueden estar en un formato personalizado, o pueden ser secuenciales, como desde 1-1000 o 1-1000000, y así sucesivamente. Comenzaremos con una lista de palabras que contenga todos los números del 1-1000.

Hay muchas maneras de crear esta lista de palabras, desde escribir manualmente los ID en un archivo o escribirlos con Bash o Python. La forma más sencilla es usar el siguiente comando en Bash que escribe todos los números del 1-1000 a un archivo:

  Valor Fuzzing

```shell-session
vcrdcr@htb[/htb]$ for i in $(seq 1 1000); do echo $i >> ids.txt; done
```

Una vez que ejecutamos nuestro comando, debemos tener lista nuestra lista de palabras:

  Valor Fuzzing

```shell-session
vcrdcr@htb[/htb]$ cat ids.txt

1
2
3
4
5
6
<...SNIP...>
```

Ahora podemos pasar a fuzzing por valores.

---

## Valor Fuzzing

Nuestro comando debe ser bastante similar al `POST` comando que solíamos fuzz para parámetros, pero nuestro `FUZZ` la palabra clave debe ponerse donde estaría el valor del parámetro, y usaremos el `ids.txt` lista de palabras que acabamos de crear, de la siguiente manera:

  Valor Fuzzing

```shell-session
vcrdcr@htb[/htb]$ ffuf -w ids.txt:FUZZ -u http://admin.academy.htb:PORT/admin/admin.php -X POST -d 'id=FUZZ' -H 'Content-Type: application/x-www-form-urlencoded' -fs xxx


        /'___\  /'___\           /'___\
       /\ \__/ /\ \__/  __  __  /\ \__/
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/
         \ \_\   \ \_\  \ \____/  \ \_\
          \/_/    \/_/   \/___/    \/_/

       v1.0.2
________________________________________________

 :: Method           : POST
 :: URL              : http://admin.academy.htb:30794/admin/admin.php
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : id=FUZZ
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

<...SNIP...>                      [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]
```

Vemos que recibimos un golpe de inmediato. Finalmente podemos enviar otro `POST` solicitud usando `curl`, como hicimos en la sección anterior, use el `id` valor que acabamos de encontrar, y recoger la bandera.

# Skills Assessment-Web Fuzzing

Se le da la dirección IP de una academia en línea, pero no tiene más información sobre su sitio web. Como primer paso para realizar una Prueba de Penetración, se espera que localice todas las páginas y dominios vinculados a su IP para enumerar la IP y los dominios correctamente.

Finalmente, debe hacer algunas pelusas en las páginas que identifique para ver si alguna de ellas tiene algún parámetro con el que pueda interactuar. Si encuentra parámetros activos, vea si puede recuperar datos de ellos.

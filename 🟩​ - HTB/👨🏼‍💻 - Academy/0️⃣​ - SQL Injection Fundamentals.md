#HTB-Academy #Bug-Bounty-Hunter

# Introducción

---

La mayoría de las aplicaciones web modernas utilizan una estructura de base de datos en el back-end. Dichas bases de datos se utilizan para almacenar y recuperar datos relacionados con la aplicación web, desde contenido web real hasta información y contenido del usuario, etc. Para que las aplicaciones web sean dinámicas, la aplicación web debe interactuar con la base de datos en tiempo real. A medida que las solicitudes HTTP(S) llegan del usuario, el back-end de la aplicación web emitirá consultas a la base de datos para crear la respuesta. Estas consultas pueden incluir información de la solicitud HTTP(S) u otra información relevante.

![dbms_arquitectura](https://academy.hackthebox.com/storage/modules/33/db_request_3.png)

Cuando la información suministrada por el usuario se utiliza para construir la consulta a la base de datos, los usuarios maliciosos pueden engañar a la consulta para que se utilice para algo distinto de lo que el programador original pretendía, proporcionando al usuario acceso para consultar la base de datos utilizando un ataque conocido como inyección SQL (SQLi).

La inyección SQL se refiere a ataques contra bases de datos relacionales como `MySQL` (mientras que las inyecciones contra bases de datos no relacionales, como MongoDB, son inyección NoSQL). Este módulo se centrará en `MySQL` para introducir conceptos de inyección SQL.

---

## Inyección SQL (SQLi)

Muchos tipos de vulnerabilidades de inyección son posibles dentro de las aplicaciones web, como la inyección HTTP, la inyección de código y la inyección de comandos. El ejemplo más común, sin embargo, es la inyección SQL. Una inyección SQL ocurre cuando un usuario malicioso intenta pasar la entrada que cambia la consulta SQL final enviada por la aplicación web a la base de datos, lo que permite al usuario realizar otras consultas SQL no deseadas directamente contra la base de datos.

Hay muchas maneras de lograr esto. Para que una inyección SQL funcione, el atacante primero debe inyectar código SQL y luego subvertir la lógica de la aplicación web cambiando la consulta original o ejecutando una completamente nueva. En primer lugar, el atacante tiene que inyectar código fuera de los límites de entrada de usuario esperados, por lo que no se ejecuta como una simple entrada de usuario. En el caso más básico, esto se hace inyectando una sola cita (`'`) o una cita doble (`"`) para escapar de los límites de entrada del usuario e inyectar datos directamente en la consulta SQL.

Una vez que un atacante puede inyectar, tiene que buscar una manera de ejecutar una consulta SQL diferente. Esto se puede hacer usando código SQL para crear una consulta de trabajo que ejecute tanto las consultas SQL previstas como las nuevas. Hay muchas maneras de lograr esto, como usar [apilado](https://www.sqlinjection.net/stacked-queries/) consultas o uso [Unión](https://www.mysqltutorial.org/sql-union-mysql.aspx/) consultas. Finalmente, para recuperar la salida de nuestra nueva consulta, tenemos que interpretarla o capturarla en el front-end de la aplicación web.

---

## Casos de Uso e Impacto

Una inyección SQL puede tener un impacto tremendo, especialmente si los privilegios en el servidor back-end y la base de datos son muy laxos.

Primero, podemos recuperar información secreta/sensible que no debería ser visible para nosotros, como inicios de sesión de usuarios y contraseñas o información de tarjetas de crédito, que luego se pueden usar para otros fines maliciosos. Las inyecciones SQL causan muchas violaciones de contraseñas y datos contra sitios web, que luego se reutilizan para robar cuentas de usuario, acceder a otros servicios o realizar otras acciones nefastas.

Otro caso de uso de la inyección SQL es subvertir la lógica de aplicación web prevista. El ejemplo más común de esto es omitir el inicio de sesión sin pasar un par válido de credenciales de nombre de usuario y contraseña. Otro ejemplo es acceder a características que están bloqueadas a usuarios específicos, como paneles de administración. Los atacantes también pueden leer y escribir archivos directamente en el servidor de back-end, lo que a su vez puede llevar a colocar puertas traseras en el servidor de back-end, y obtener control directo sobre él, y eventualmente tomar el control de todo el sitio web.

---

## Prevención

Las inyecciones SQL generalmente son causadas por aplicaciones web mal codificadas o privilegios de servidor back-end y bases de datos mal seguros. Más adelante, discutiremos formas de reducir las posibilidades de ser vulnerable a las inyecciones SQL a través de métodos de codificación seguros como la desinfección y validación de la entrada del usuario y los privilegios y control adecuados del usuario de back-end.
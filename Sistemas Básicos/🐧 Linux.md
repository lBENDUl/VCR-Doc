---
tags:
  - Linux
---

En esta sección veremos algunos comandos básicos para movernos e interactuar con archivos en sistemas Linux, y la estructura de directorios principal en este sistema operativo.

## Comandos básicos

| Comando                  | Descripción |
|--------------------------|-------------|
| `ls`                    | Listar archivos y directorios en el directorio actual. |
| `pwd`                   | Mostrar el directorio de trabajo actual. |
| `cd`                    | Cambiar de directorio. |
| `cd ..`                 | Directorio anterior. |
| `mkdir`                 | Crear un nuevo directorio. |
| `touch`                 | Crear un nuevo archivo vacío. |
| `cp`                    | Copiar archivos o directorios. |
| `mv`                    | Mover o renombrar archivos o directorios. |
| `rm`                    | Eliminar archivos o directorios. |
| `cat`                   | Mostrar el contenido de un archivo. |
| `cat /etc/passwd`       | Mostrar todos los usuarios del sistema. |
| `cut -d: -f1 /etc/passwd` | Extraer solo el nombre de usuario de passwd. |
| `awk -F: '$3 >= 1000 && $3 < 65534 {print $1}' /etc/passwd` | Mostrar solo usuarios humanos. |
| `getent passwd`         | Información sobre usuarios y servicios. |
| `less`                  | Ver contenido de archivo paginado. |
| `head`                  | Mostrar las primeras líneas de un archivo. |
| `tail`                  | Mostrar las últimas líneas de un archivo. |
| `grep`                  | Buscar patrones en archivos de texto. |
| `find`                  | Buscar archivos y directorios. |
| `file documento.txt`    | Ver el tipo de un documento. |
| `ps`                    | Mostrar procesos en ejecución. |
| `kill`                  | Terminar procesos. |
| `top`                   | Mostrar información en tiempo real sobre el sistema. |
| `df`                    | Mostrar el espacio en disco disponible. |
| `du`                    | Mostrar el uso del espacio en disco de archivos y directorios. |
| `tar`                   | Comprimir y descomprimir archivos y directorios. |
| `chmod`                 | Cambiar permisos de archivos y directorios. |
| `chown`                 | Cambiar propietario de archivos y directorios. |
| `useradd`               | Agregar un nuevo usuario. |
| `userdel`               | Eliminar un usuario. |
| `passwd`                | Cambiar la contraseña del usuario. |
| `sudo`                  | Ejecutar comandos con privilegios de superusuario. |
| `apt` (o `yum` en otras distribuciones) | Gestionar paquetes en sistemas basados en Debian (o en otras distribuciones). |
| `sudo apt-get update`   | Actualizar la lista de paquetes. |
| `wget`                  | Descargar archivos desde la web. |
| `ssh`                   | Conectar a un servidor remoto mediante SSH. |
| `scp`                   | Copiar archivos de forma segura entre sistemas a través de SSH. |

## Estructura de directorios en Linux

| Directorio  | Descripción |
|------------|-------------|
| `/`        | Directorio raíz del sistema; el punto de inicio de la estructura de directorios en Linux. |
| `/bin`     | Contiene binarios esenciales para el sistema y el usuario, como comandos básicos (`ls`, `cp`, `mv`, etc.). |
| `/boot`    | Archivos necesarios para el arranque del sistema, como el kernel de Linux y archivos de configuración. |
| `/dev`     | Archivos de dispositivos; representa dispositivos de hardware y pseudo-dispositivos del sistema. |
| `/etc`     | Archivos de configuración del sistema y los servicios instalados. |
| `/home`    | Directorio donde se encuentran los archivos personales de cada usuario. |
| `/lib`     | Bibliotecas compartidas necesarias para los binarios de `/bin` y `/sbin`. |
| `/media`   | Punto de montaje para dispositivos extraíbles, como discos USB y CDs. |
| `/mnt`     | Directorio temporal para montar sistemas de archivos adicionales de manera manual. |
| `/opt`     | Directorio para aplicaciones adicionales que no forman parte de la instalación principal del sistema. |
| `/proc`    | Sistema de archivos virtual que contiene información del sistema y procesos en ejecución. |
| `/root`    | Directorio de inicio del usuario root; el administrador del sistema. |
| `/run`     | Información sobre los servicios y el sistema que se almacena desde el arranque hasta el apagado. |
| `/sbin`    | Binarios esenciales para tareas administrativas y de sistema (solo accesibles con permisos root). |
| `/srv`     | Contiene datos de servicios proporcionados por el sistema (ej., datos de un servidor web o FTP). |
| `/sys`     | Sistema de archivos virtual similar a `/proc`, usado para información y control del hardware del sistema. |
| `/tmp`     | Almacenamiento temporal; se borra al reiniciar el sistema. |
| `/usr`     | Archivos de usuario y de aplicaciones; incluye binarios, bibliotecas y documentación de programas. |
| `/var`     | Archivos de datos variables, como logs del sistema, colas de impresión, y archivos temporales de aplicaciones. |

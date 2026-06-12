# Python — Tkinter (interfaces gráficas)


## Estructura básica de una app

```python
import tkinter as tk

# 1. Crear la ventana principal
root = tk.Tk()

# 2. Configurar la ventana
root.title("Mi aplicación")
root.geometry("800x600")       # ancho x alto
root.geometry("800x600+100+50") # ancho x alto + posición X + posición Y
root.resizable(True, True)     # redimensionable (ancho, alto)
root.minsize(400, 300)         # tamaño mínimo

# 3. Añadir widgets...
label = tk.Label(root, text="¡Hola, mundo!")
label.pack()

# 4. Iniciar el bucle de eventos (siempre al final)
root.mainloop()
```

---

## Layouts (gestores de geometría)

Hay tres formas de colocar widgets. **No mezclar `pack` y `grid` en el mismo contenedor.**

### `pack()` — Apilado

Coloca widgets en bloques, uno detrás de otro.

```python
label1 = tk.Label(root, text="Arriba", bg="red")
label2 = tk.Label(root, text="Abajo", bg="blue")
label3 = tk.Label(root, text="Izquierda", bg="green")

label1.pack(side="top", fill="x")           # ocupa todo el ancho
label2.pack(side="bottom", fill="x")
label3.pack(side="left", fill="y")          # ocupa todo el alto

# expand=True → el widget ocupa el espacio sobrante
label1.pack(expand=True, fill="both")       # expande en ambas direcciones

# Padding
label1.pack(padx=10, pady=5)
```

### `grid()` — Cuadrícula

Organiza widgets en filas y columnas. El más recomendado para formularios.

```python
tk.Label(root, text="Nombre:").grid(row=0, column=0, sticky="w", padx=5, pady=5)
tk.Entry(root).grid(row=0, column=1, padx=5)

tk.Label(root, text="Email:").grid(row=1, column=0, sticky="w", padx=5, pady=5)
tk.Entry(root).grid(row=1, column=1, padx=5)

# columnspan → ocupa varias columnas
tk.Button(root, text="Enviar").grid(row=2, column=0, columnspan=2, pady=10)

# sticky: N, S, E, W o combinaciones (tk.EW = expandir horizontalmente)
tk.Entry(root).grid(row=0, column=1, sticky="ew")

# Hacer que las columnas se expandan con la ventana
root.columnconfigure(1, weight=1)
```

### `place()` — Posición absoluta o relativa

Control total sobre la posición. Poco flexible al redimensionar.

```python
label = tk.Label(root, text="Fijo")
label.place(x=50, y=100)                        # posición absoluta

label.place(relx=0.5, rely=0.5, anchor="center")  # centrado en la ventana
label.place(relx=0.0, rely=0.0, relwidth=0.5)     # ocupa el 50% del ancho
```

---

## Widgets

### `Label` — Etiqueta de texto o imagen

```python
label = tk.Label(
    root,
    text="Texto del label",
    font=("Arial", 14, "bold"),
    fg="white",         # color de texto
    bg="navy",          # color de fondo
    wraplength=200,     # ancho máximo antes de saltar de línea
    justify="left"      # left, center, right
)
label.pack(padx=10, pady=10)

# Actualizar texto en tiempo de ejecución
label.config(text="Nuevo texto")
```

### `Entry` — Campo de texto de una línea

```python
entry = tk.Entry(root, width=30, show="*")  # show="*" para contraseñas
entry.pack(padx=10, pady=5)

# Leer valor
valor = entry.get()

# Escribir valor
entry.insert(0, "Texto inicial")

# Borrar
entry.delete(0, tk.END)

# Variable vinculada (se actualiza automáticamente)
var = tk.StringVar()
entry = tk.Entry(root, textvariable=var)
print(var.get())
var.set("Nuevo valor")
```

### `Text` — Área de texto multilínea

```python
texto = tk.Text(root, height=10, width=50, wrap="word")
texto.pack(padx=10, pady=5)

# Insertar texto
texto.insert(tk.END, "Línea 1\n")
texto.insert("1.0", "Al principio\n")  # fila.columna

# Leer todo el contenido
contenido = texto.get("1.0", tk.END)

# Solo la línea 2
linea = texto.get("2.0", "2.end")

# Borrar todo
texto.delete("1.0", tk.END)

# Hacer el widget de solo lectura
texto.config(state="disabled")
texto.config(state="normal")  # volver a editable
```

### `Button` — Botón

```python
def al_hacer_clic():
    print("Clic!")
    label.config(text="¡Pulsado!")

btn = tk.Button(
    root,
    text="Pulsa aquí",
    command=al_hacer_clic,
    bg="steelblue",
    fg="white",
    font=("Arial", 12),
    width=15,
    height=2,
    relief="raised"   # flat, groove, raised, ridge, solid, sunken
)
btn.pack(pady=10)

# Deshabilitar/habilitar
btn.config(state="disabled")
btn.config(state="normal")
```

### `Frame` — Contenedor

Agrupa widgets y simplifica el layout. Se pueden anidar.

```python
frame = tk.Frame(root, bg="lightgray", bd=2, relief="sunken")
frame.pack(fill="both", expand=True, padx=10, pady=10)

# Los widgets dentro del frame lo usan como padre
label = tk.Label(frame, text="Dentro del frame", bg="lightgray")
label.pack(padx=5, pady=5)
```

### `Canvas` — Dibujo y gráficos

```python
canvas = tk.Canvas(root, width=400, height=300, bg="white")
canvas.pack()

# Figuras
canvas.create_rectangle(50, 50, 200, 150, fill="blue", outline="black")
canvas.create_oval(220, 50, 370, 150, fill="red")
canvas.create_line(0, 0, 400, 300, fill="green", width=2)
canvas.create_polygon(200, 200, 250, 250, 150, 250, fill="yellow")

# Texto en el canvas
canvas.create_text(200, 280, text="Hola Canvas", font=("Arial", 12))

# Mover un elemento
rect = canvas.create_rectangle(10, 10, 100, 100, fill="purple")
canvas.move(rect, 50, 30)

# Eliminar
canvas.delete(rect)
canvas.delete("all")  # borrar todo
```

### `Menu` — Barra de menú

```python
menubar = tk.Menu(root)

# Menú Archivo
menu_archivo = tk.Menu(menubar, tearoff=0)
menu_archivo.add_command(label="Nuevo",   command=lambda: print("Nuevo"))
menu_archivo.add_command(label="Abrir",   command=lambda: print("Abrir"))
menu_archivo.add_separator()
menu_archivo.add_command(label="Salir",   command=root.quit)

menubar.add_cascade(label="Archivo", menu=menu_archivo)

# Menú Editar
menu_editar = tk.Menu(menubar, tearoff=0)
menu_editar.add_command(label="Copiar",   command=lambda: print("Copiar"))
menu_editar.add_command(label="Pegar",    command=lambda: print("Pegar"))

menubar.add_cascade(label="Editar", menu=menu_editar)

root.config(menu=menubar)
```

### `messagebox` — Diálogos de mensaje

```python
from tkinter import messagebox

messagebox.showinfo("Título",    "Operación completada")
messagebox.showwarning("Aviso", "Hay un problema")
messagebox.showerror("Error",   "Algo salió mal")

# Con confirmación (devuelve True/False)
if messagebox.askyesno("Confirmar", "¿Deseas continuar?"):
    print("Confirmado")

respuesta = messagebox.askquestion("Pregunta", "¿Guardar cambios?")
# respuesta es "yes" o "no"
```

### `filedialog` — Diálogos de archivo

```python
from tkinter import filedialog

# Seleccionar archivo para abrir
ruta = filedialog.askopenfilename(
    title="Selecciona un archivo",
    filetypes=[("Archivos de texto", "*.txt"), ("Todos", "*.*")]
)

# Seleccionar ruta para guardar
ruta_guardado = filedialog.asksaveasfilename(
    defaultextension=".txt",
    filetypes=[("Texto", "*.txt")]
)

# Seleccionar directorio
directorio = filedialog.askdirectory(title="Selecciona carpeta")
```

---

## Variables de control

Vinculan widgets a variables Python y se actualizan automáticamente.

```python
# Tipos
var_str  = tk.StringVar()
var_int  = tk.IntVar()
var_bool = tk.BooleanVar()
var_dbl  = tk.DoubleVar()

# Leer y escribir
var_str.get()
var_str.set("nuevo valor")

# Escuchar cambios
def al_cambiar(*args):
    print(f"Nuevo valor: {var_str.get()}")

var_str.trace_add("write", al_cambiar)
```

### Widgets que usan variables de control

```python
# Checkbox
var_check = tk.BooleanVar()
check = tk.Checkbutton(root, text="Acepto los términos", variable=var_check)
check.pack()
print(var_check.get())  # True/False

# Radio buttons
var_radio = tk.StringVar(value="opcion1")
tk.Radiobutton(root, text="Opción 1", variable=var_radio, value="opcion1").pack()
tk.Radiobutton(root, text="Opción 2", variable=var_radio, value="opcion2").pack()

# Combobox (necesita ttk)
from tkinter import ttk
combo = ttk.Combobox(root, values=["Rojo", "Verde", "Azul"])
combo.set("Rojo")
combo.pack()
print(combo.get())
```

---

## Eventos y bindings

```python
# Click izquierdo
widget.bind("<Button-1>", lambda e: print("Click izquierdo"))

# Doble click
widget.bind("<Double-Button-1>", lambda e: print("Doble click"))

# Tecla específica
root.bind("<Return>", lambda e: print("Enter pulsado"))
root.bind("<Escape>", lambda e: root.quit())

# Cualquier tecla
root.bind("<Key>", lambda e: print(f"Tecla: {e.keysym}"))

# Ratón encima / fuera
widget.bind("<Enter>", lambda e: widget.config(bg="lightblue"))
widget.bind("<Leave>", lambda e: widget.config(bg="white"))

# Redimensionar ventana
root.bind("<Configure>", lambda e: print(f"Tamaño: {e.width}x{e.height}"))
```

---

## Actualizar la UI periódicamente: `after()`

```python
import time

def actualizar_reloj():
    hora_actual = time.strftime("%H:%M:%S")
    label_reloj.config(text=hora_actual)
    root.after(1000, actualizar_reloj)  # llamar de nuevo en 1000ms

label_reloj = tk.Label(root, font=("Courier", 24))
label_reloj.pack()
actualizar_reloj()
```

---

## Ejemplo completo: formulario básico

```python
import tkinter as tk
from tkinter import messagebox

def enviar():
    nombre = entry_nombre.get().strip()
    email  = entry_email.get().strip()
    if not nombre or not email:
        messagebox.showwarning("Campos vacíos", "Rellena todos los campos")
        return
    messagebox.showinfo("Enviado", f"Nombre: {nombre}\nEmail: {email}")

root = tk.Tk()
root.title("Formulario")
root.geometry("350x200")
root.resizable(False, False)

frame = tk.Frame(root, padx=20, pady=20)
frame.pack(fill="both", expand=True)

tk.Label(frame, text="Nombre:").grid(row=0, column=0, sticky="w", pady=5)
entry_nombre = tk.Entry(frame, width=25)
entry_nombre.grid(row=0, column=1, pady=5)

tk.Label(frame, text="Email:").grid(row=1, column=0, sticky="w", pady=5)
entry_email = tk.Entry(frame, width=25)
entry_email.grid(row=1, column=1, pady=5)

tk.Button(frame, text="Enviar", command=enviar, bg="steelblue", fg="white", width=20).grid(
    row=2, column=0, columnspan=2, pady=15
)

root.mainloop()
```

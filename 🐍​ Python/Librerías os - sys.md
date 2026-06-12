# Python — Librerías `os` y `sys`

> Referencia rápida para interactuar con el sistema operativo y el intérprete desde Python.

---

## Librería `os`

Permite interactuar con el sistema de archivos, variables de entorno, procesos y rutas de forma portable entre sistemas operativos.

```python
import os
```

---

### Directorio actual y navegación

```python
# Directorio de trabajo actual (equivalente a pwd)
os.getcwd()

# Cambiar de directorio (equivalente a cd)
os.chdir("/tmp")

# Listar contenido de un directorio (equivalente a ls)
os.listdir()
os.listdir("/etc")
os.listdir(os.getcwd())
```

---

### Crear directorios

```python
# Crear un directorio simple
os.mkdir("mi_directorio")

# Crear directorios anidados de golpe (como mkdir -p)
os.makedirs("padre/hijo/nieto")

# Con exist_ok=True no lanza error si ya existe
os.makedirs("padre/hijo/nieto", exist_ok=True)
```

---

### Eliminar archivos y directorios

```python
# Eliminar un archivo
os.remove("archivo.txt")

# Eliminar un directorio vacío
os.rmdir("mi_directorio")

# Eliminar una cadena de directorios vacíos
os.removedirs("padre/hijo/nieto")
```

> Para eliminar directorios con contenido usar `shutil.rmtree()` (ver más abajo).

---

### Renombrar / mover

```python
os.rename("viejo_nombre.txt", "nuevo_nombre.txt")
```

---

### `os.path` — Gestión de rutas

```python
ruta = "/home/usuario/documentos/archivo.txt"

os.path.exists(ruta)           # True/False — ¿existe?
os.path.isfile(ruta)           # True si es archivo
os.path.isdir("/home/usuario") # True si es directorio

os.path.basename(ruta)         # "archivo.txt"
os.path.dirname(ruta)          # "/home/usuario/documentos"
os.path.split(ruta)            # ("/home/usuario/documentos", "archivo.txt")
os.path.splitext(ruta)         # ("/home/usuario/documentos/archivo", ".txt")

# Construir rutas de forma portable (funciona en Win/Linux/Mac)
os.path.join("/home/usuario", "documentos", "archivo.txt")
# → "/home/usuario/documentos/archivo.txt"

# Tamaño del archivo en bytes
os.path.getsize(ruta)
```

---

### Variables de entorno

```python
# Leer una variable de entorno
os.getenv("HOME")
os.getenv("PATH")
os.getenv("MI_VAR", "valor_por_defecto")  # si no existe, devuelve el default

# Ver todas las variables de entorno
os.environ          # diccionario con todas
os.environ["HOME"]  # acceso directo (lanza KeyError si no existe)

# Establecer una variable de entorno (solo para el proceso actual)
os.environ["MI_VAR"] = "mi_valor"
```

---

### Ejecutar comandos del sistema

```python
# Ejecutar un comando (devuelve el código de salida)
os.system("ls -la")

# Mejor opción para capturar salida → usar subprocess
import subprocess
resultado = subprocess.run(["ls", "-la"], capture_output=True, text=True)
print(resultado.stdout)
```

---

### Información del sistema

```python
os.name          # 'posix' en Linux/Mac, 'nt' en Windows
os.getpid()      # PID del proceso actual
os.getppid()     # PID del proceso padre
os.cpu_count()   # número de CPUs disponibles
```

---

### Recorrer un árbol de directorios

```python
# os.walk() genera (directorio, subdirectorios, archivos) recursivamente
for directorio, subdirs, archivos in os.walk("/home/usuario"):
    print(f"Directorio: {directorio}")
    for archivo in archivos:
        ruta_completa = os.path.join(directorio, archivo)
        print(f"  {ruta_completa}")
```

---

## Módulo `shutil`

Operaciones de alto nivel sobre archivos y directorios: copiar, mover, comprimir y eliminar árboles completos.

```python
import shutil
```

---

### Copiar archivos

```python
# Copia el archivo (solo contenido, sin metadatos)
shutil.copy("origen.txt", "destino.txt")

# Copia contenido y metadatos (permisos, fechas...)
shutil.copy2("origen.txt", "destino.txt")

# Copia un directorio completo (destino no debe existir)
shutil.copytree("directorio_origen", "directorio_destino")

# Con dirs_exist_ok=True permite que el destino ya exista (Python 3.8+)
shutil.copytree("origen", "destino", dirs_exist_ok=True)
```

---

### Mover archivos y directorios

```python
shutil.move("origen.txt", "/otra/ruta/destino.txt")
shutil.move("mi_directorio", "/otra/ubicacion/")
```

---

### Eliminar directorios con contenido

```python
# Elimina el directorio y todo su contenido
shutil.rmtree("directorio_a_borrar")

# Con ignore_errors=True no falla si algo no se puede borrar
shutil.rmtree("directorio_a_borrar", ignore_errors=True)
```

---

### Comprimir y descomprimir

```python
# Crear un zip/tar de un directorio
shutil.make_archive("nombre_salida", "zip", "directorio_a_comprimir")
shutil.make_archive("nombre_salida", "gztar", "directorio_a_comprimir")  # .tar.gz

# Descomprimir
shutil.unpack_archive("archivo.zip", "directorio_destino")
```

---

### Información del disco

```python
uso = shutil.disk_usage("/")
print(f"Total:  {uso.total / 1e9:.1f} GB")
print(f"Usado:  {uso.used  / 1e9:.1f} GB")
print(f"Libre:  {uso.free  / 1e9:.1f} GB")
```

---

## Librería `sys`

Orientada a la interacción con el intérprete de Python: argumentos de la línea de comandos, rutas de módulos, salida estándar y control de ejecución.

```python
import sys
```

---

### Argumentos de línea de comandos

`sys.argv` es una lista donde el índice `0` siempre es el nombre del script.

```python
# Ejecutado como: python script.py arg1 arg2
nombre_script     = sys.argv[0]   # "script.py"
primer_argumento  = sys.argv[1]   # "arg1"
total_argumentos  = len(sys.argv) # 3

# Patrón común para validar argumentos
if len(sys.argv) < 2:
    print(f"Uso: python {sys.argv[0]} <argumento>")
    sys.exit(1)

argumento = sys.argv[1]
```

---

### Salir del programa

```python
sys.exit()    # sale con código 0 (éxito)
sys.exit(0)   # éxito
sys.exit(1)   # error genérico
sys.exit("Mensaje de error")  # imprime el mensaje en stderr y sale con código 1
```

---

### Rutas de búsqueda de módulos

```python
# Lista de directorios donde Python busca módulos al hacer import
for ruta in sys.path:
    print(ruta)

# Añadir una ruta extra en tiempo de ejecución
sys.path.append("/ruta/a/mis/modulos")
```

---

### Información del intérprete

```python
sys.version        # "3.11.4 (main, ...)"
sys.version_info   # sys.version_info(major=3, minor=11, micro=4, ...)
sys.platform       # "linux", "darwin", "win32"
sys.executable     # ruta al binario de Python en uso

# Comprobar versión mínima requerida
if sys.version_info < (3, 10):
    sys.exit("Se requiere Python 3.10 o superior")
```

---

### Salida estándar y de error

```python
# Escribir directamente en stdout/stderr sin print()
sys.stdout.write("Mensaje estándar\n")
sys.stderr.write("Mensaje de error\n")

# Redirigir stdout a un archivo
import io
sys.stdout = open("salida.log", "w")
print("Esto va al archivo")
sys.stdout = sys.__stdout__  # restaurar
```

---

### Módulos importados

```python
# Diccionario con todos los módulos actualmente importados
sys.modules

# Comprobar si un módulo está importado
"os" in sys.modules  # True
```

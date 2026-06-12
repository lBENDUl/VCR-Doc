# Python — Archivos y directorios (lectura y escritura)

---

## Abrir un archivo: `open()`

```python
f = open("archivo.txt", "r")   # abrir
contenido = f.read()
f.close()                       # cerrar siempre
```

Lo correcto es usar el gestor de contexto `with`, que cierra el archivo automáticamente aunque ocurra un error:

```python
with open("archivo.txt", "r") as f:
    contenido = f.read()
```

---

### Modos de apertura

| Modo | Descripción |
|------|-------------|
| `"r"` | Lectura (por defecto). Error si no existe. |
| `"w"` | Escritura. Crea el archivo o lo sobreescribe. |
| `"a"` | Append. Añade al final sin borrar el contenido. |
| `"x"` | Creación exclusiva. Error si ya existe. |
| `"r+"` | Lectura y escritura. El archivo debe existir. |
| `"rb"` / `"wb"` | Modo binario (imágenes, zips, etc.) |

---

## Leer archivos

```python
# Leer todo el contenido como un string
with open("archivo.txt", "r", encoding="utf-8") as f:
    contenido = f.read()

# Leer línea a línea (eficiente para archivos grandes)
with open("archivo.txt", "r", encoding="utf-8") as f:
    for linea in f:
        print(linea.strip())  # strip() elimina el \n del final

# Leer todas las líneas en una lista
with open("archivo.txt", "r", encoding="utf-8") as f:
    lineas = f.readlines()
# lineas = ["línea 1\n", "línea 2\n", ...]

# Leer solo la primera línea
with open("archivo.txt", "r") as f:
    primera = f.readline()
```

---

## Escribir en archivos

```python
# Sobreescribir (o crear si no existe)
with open("salida.txt", "w", encoding="utf-8") as f:
    f.write("Primera línea\n")
    f.write("Segunda línea\n")

# Escribir varias líneas de golpe
lineas = ["línea 1\n", "línea 2\n", "línea 3\n"]
with open("salida.txt", "w", encoding="utf-8") as f:
    f.writelines(lineas)

# Añadir al final sin borrar
with open("log.txt", "a", encoding="utf-8") as f:
    f.write("Nueva entrada de log\n")
```

---

## Archivos binarios

```python
# Leer una imagen
with open("imagen.png", "rb") as f:
    datos = f.read()

# Copiar un archivo binario
with open("origen.jpg", "rb") as entrada:
    with open("copia.jpg", "wb") as salida:
        salida.write(entrada.read())
```

---

## Trabajar con JSON

```python
import json

# Leer JSON desde archivo
with open("datos.json", "r", encoding="utf-8") as f:
    datos = json.load(f)

# Escribir JSON a archivo
datos = {"nombre": "Ana", "edad": 30}
with open("datos.json", "w", encoding="utf-8") as f:
    json.dump(datos, f, indent=4, ensure_ascii=False)
```

---

## Trabajar con CSV

```python
import csv

# Leer CSV
with open("datos.csv", "r", encoding="utf-8") as f:
    reader = csv.DictReader(f)
    for fila in reader:
        print(fila["nombre"], fila["edad"])

# Escribir CSV
cabeceras = ["nombre", "edad", "ciudad"]
filas = [["Ana", 30, "Madrid"], ["Luis", 25, "Barcelona"]]

with open("salida.csv", "w", newline="", encoding="utf-8") as f:
    writer = csv.writer(f)
    writer.writerow(cabeceras)
    writer.writerows(filas)
```

---

## Archivos temporales

```python
import tempfile

# Archivo temporal (se borra automáticamente al salir del with)
with tempfile.NamedTemporaryFile(mode="w", suffix=".txt", delete=True) as tmp:
    tmp.write("datos temporales")
    print(tmp.name)  # ruta del archivo temporal

# Directorio temporal
with tempfile.TemporaryDirectory() as tmpdir:
    print(tmpdir)  # ruta del directorio temporal
```

---

## Gestión de rutas modernas: `pathlib`

`pathlib.Path` es la forma moderna y recomendada de trabajar con rutas en Python 3. Más legible y portable que `os.path`.

```python
from pathlib import Path

ruta = Path("/home/usuario/documentos/archivo.txt")

# Información de la ruta
ruta.name        # "archivo.txt"
ruta.stem        # "archivo"
ruta.suffix      # ".txt"
ruta.parent      # Path("/home/usuario/documentos")

# Construir rutas con el operador /
base = Path("/home/usuario")
nueva_ruta = base / "proyecto" / "main.py"

# Comprobar existencia
ruta.exists()
ruta.is_file()
ruta.is_dir()

# Crear directorios
Path("nuevo/directorio").mkdir(parents=True, exist_ok=True)

# Leer y escribir directamente
texto = ruta.read_text(encoding="utf-8")
ruta.write_text("nuevo contenido", encoding="utf-8")

datos = ruta.read_bytes()
ruta.write_bytes(b"\x00\x01\x02")

# Listar contenido de un directorio
for archivo in Path(".").iterdir():
    print(archivo)

# Buscar archivos por patrón (recursivo con **)
for py_file in Path(".").rglob("*.py"):
    print(py_file)

# Renombrar / mover
ruta.rename("nuevo_nombre.txt")

# Eliminar
Path("archivo.txt").unlink()           # eliminar archivo
Path("directorio_vacio").rmdir()       # eliminar directorio vacío
```

---

## Patrón común: comprobar antes de actuar

```python
from pathlib import Path

ruta = Path("mi_archivo.txt")

if ruta.exists():
    contenido = ruta.read_text(encoding="utf-8")
else:
    print("El archivo no existe")

# Crear directorio solo si no existe
Path("logs").mkdir(exist_ok=True)
```

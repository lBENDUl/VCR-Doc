#python

Las bibliotecas ‘**os**‘ y ‘**sys**‘ de Python son herramientas esenciales para cualquier desarrollador que busque interactuar eficazmente con el sistema operativo y gestionar el entorno de ejecución de sus programas. Estas bibliotecas proporcionan una amplia gama de funcionalidades que permiten una mayor flexibilidad y control en el desarrollo de software.

**Biblioteca os**

La biblioteca ‘**os**‘ en Python es una herramienta poderosa para interactuar con el sistema operativo. Proporciona una interfaz portátil para usar funcionalidades dependientes del sistema operativo, lo que significa que los programas pueden funcionar en diferentes sistemas operativos sin cambios significativos en el código. Algunas de sus capacidades incluyen:

- **Manipulación de Archivos y Directorios**: Permite realizar operaciones como crear, eliminar, mover archivos y directorios, y consultar sus propiedades.
- **Ejecución de Comandos del Sistema**: Facilita la ejecución de comandos del sistema operativo desde un programa Python.
- **Gestión de Variables de Entorno**: Ofrece funciones para leer y modificar las variables de entorno del sistema.
- **Obtención de Información del Sistema**: Proporciona métodos para obtener información relevante sobre el sistema operativo, como la estructura de directorios, detalles del usuario, procesos, etc.

**Biblioteca sys**

La biblioteca ‘**sys**‘ es fundamental para interactuar con el entorno de ejecución del programa Python. A diferencia de ‘**os**‘, que se centra en el sistema operativo, ‘**sys**‘ está más orientada a la interacción con el intérprete de Python. Sus principales usos incluyen:

- **Argumentos de Línea de Comandos**: Permite acceder y manipular los argumentos que se pasan al programa Python desde la línea de comandos.
- **Gestión de la Salida del Programa**: Facilita el control sobre la salida estándar (**stdout**) y la salida de error (**stderr**), lo cual es esencial para la depuración y la presentación de resultados.
- **Información del Intérprete**: Ofrece acceso a configuraciones y funcionalidades relacionadas con el intérprete de Python, como la versión de Python en uso, la lista de módulos importados y la gestión de la ruta de búsqueda de módulos.

Ambas bibliotecas son cruciales para el desarrollo de aplicaciones Python que requieren interacción avanzada con el entorno de sistema y el intérprete. Su comprensión y uso adecuado permite a los desarrolladores escribir código más robusto, portable y eficiente.


# Librería os

## os.getcwd()

Nos mustra la ruta actual.
Como si fuera un pwd en consola.


## os.listdir()

Lista el contenido de un directoio.
Como si fuera un ls en consola.

```python
os.listdir(os.getcwd())
```


## os.mkdir()

Crea un directorio

```python
os.mkdir("mi_directorio")
```


## os.path.exists()

Devuelve `true` o `false` dependiendo de si la ruta pasada por parámetro existe o no.

```python
os.path.exists("/etc/passwd")
```


## os.getenv()

Devuelve el valor de la variable de entorno

```python
get_env = os.getenv('KITTY_INSTALLATION_DIR')
```


# Librería sys

## sys.argv[#]

Es un array del comando que se ha ejecutado

```python
nombre_comando = sys.argv[0]
argumentos_totales = len(sys.argv)
```

## sys.path

Contiene las rutas de python

```python
for path in sys.path:
	print(path)
```

## sys.exit()

Sale del código, dependiendo del parámetro sale con un estado diferente

```python
sys.exit(1)
```
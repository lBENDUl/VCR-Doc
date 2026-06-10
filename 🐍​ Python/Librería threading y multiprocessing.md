#python 


Las bibliotecas ‘**threading**‘ y ‘**multiprocessing**‘ en Python son herramientas esenciales para la programación concurrente y paralela. Proporcionan mecanismos para ejecutar múltiples tareas simultáneamente, aprovechando mejor los recursos del sistema. A continuación, se presenta una descripción detallada de ambas bibliotecas y sus diferencias.

**Descripción Detallada de threading y multiprocessing**

**Biblioteca threading**

‘**threading**‘ es una biblioteca para la programación concurrente que permite a los programas ejecutar múltiples ‘**hilos**‘ de ejecución al mismo tiempo. Los hilos son entidades más ligeras que los procesos, comparten el mismo espacio de memoria y son ideales para tareas que requieren poco procesamiento o que están limitadas por E/S.

- **Uso Principal**: Ideal para tareas que no son intensivas en CPU o que esperan recursos (como E/S de red o de archivos).
- **Ventajas**: Bajo costo de creación y cambio de contexto, compartición eficiente de memoria y recursos entre hilos.
- **Desventajas**: Limitada por el Global Interpreter Lock (GIL) en CPython, que previene la ejecución de múltiples hilos de Python al mismo tiempo en un solo proceso.

**Biblioteca multiprocessing**

‘**multiprocessing**‘, por otro lado, se enfoca en la creación de procesos. Cada proceso en multiprocessing tiene su propio espacio de memoria. Esto significa que pueden ejecutarse en paralelo real en sistemas con múltiples núcleos de CPU, superando la limitación del GIL.

- **Uso Principal**: Ideal para tareas intensivas en CPU que requieren paralelismo real.
- **Ventajas**: Capacidad para realizar cálculos intensivos en paralelo, aprovechando múltiples núcleos de CPU.
- **Desventajas**: Mayor costo en recursos y complejidad en la comunicación entre procesos debido a espacios de memoria separados.

**Diferencias Clave**

- **Modelo de Ejecución**: ‘**threading**‘ ejecuta hilos en un solo proceso compartiendo el mismo espacio de memoria, mientras ‘**multiprocessing**‘ ejecuta múltiples procesos con memoria independiente.
- **Uso de CPU**: ‘**multiprocessing**‘ es más adecuado para tareas que requieren mucho cálculo y pueden beneficiarse de múltiples núcleos de CPU, mientras que ‘**threading**‘ es mejor para tareas limitadas por E/S.
- **Global Interpreter Lock (GIL)**: ‘**threading**‘ está limitado por el GIL en CPython, lo que restringe la ejecución en paralelo de hilos, mientras que ‘**multiprocessing**‘ no tiene esta limitación.
- **Gestión de Recursos**: ‘**threading**‘ es más eficiente en términos de memoria y creación de hilos, pero ‘**multiprocessing**‘ es más eficaz para tareas aisladas y seguras en cuanto a datos.

**Conclusión**

Entender y utilizar adecuadamente ‘**threading**‘ y ‘**multiprocessing**‘ es crucial para optimizar aplicaciones Python, especialmente en términos de rendimiento y eficiencia. La elección entre ambas depende de las necesidades específicas de la tarea, como el tipo de carga de trabajo (CPU-intensiva vs E/S-intensiva) y los requisitos de arquitectura de la aplicación.


# threading

```python
def tarea(num_tarea):
	print("Tarea iniciando")
	time.sleep(2)
	print("Tarea finalizando")

thread1 = threading.Thread(target=tarea, args(1,))
thread2 = threading.Thread(target=tarea, args(2,))

thread1.start()
thread2.start()

# Garantia de que los hilos concluyan
thread1.join()
thread2.join()
```


# multiprocessing

No comparten memoria. pero pueden aprovechar múltiples núcleos de la CPU

```python
def tarea(num_tarea):
	print("Tarea iniciando")
	time.sleep(2)
	print("Tarea finalizando")

proceso1 = multiprocessing.Process(target=tarea, args(1,))
proceso2 = multiprocessing.Process(target=tarea, args(2,))

proceso1.start()
proceso2.start()

# Garantia de que los hilos concluyan
proceso1.join()
proceso2.join()

```
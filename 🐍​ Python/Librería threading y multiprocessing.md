# Python — `threading` y `multiprocessing`


## ¿Cuál usar?

| | `threading` | `multiprocessing` |
|---|---|---|
| **Modelo** | Hilos (comparten memoria) | Procesos (memoria independiente) |
| **Ideal para** | Tareas limitadas por I/O (red, disco, espera) | Tareas intensivas de CPU (cálculo, procesamiento) |
| **GIL** | Afectado (no paralelismo real en CPU) | No afectado (paralelismo real) |
| **Coste** | Bajo (hilos son ligeros) | Mayor (procesos consumen más RAM) |
| **Comunicación** | Directa (variables compartidas) | Requiere `Queue`, `Pipe` o `Manager` |

> **Regla rápida**: descarga de archivos, scraping, peticiones de red → `threading`. Procesamiento de imágenes, ML, cálculo matemático → `multiprocessing`.

---

## `threading`

### Uso básico

```python
import threading
import time

def tarea(nombre, segundos):
    print(f"[{nombre}] Iniciando")
    time.sleep(segundos)
    print(f"[{nombre}] Finalizado")

# Crear hilos
t1 = threading.Thread(target=tarea, args=("Hilo-1", 2))
t2 = threading.Thread(target=tarea, args=("Hilo-2", 3))

# Arrancar
t1.start()
t2.start()

# Esperar a que terminen antes de continuar
t1.join()
t2.join()

print("Todos los hilos terminaron")
```

### Clase Thread personalizada

```python
import threading

class MiHilo(threading.Thread):
    def __init__(self, nombre, datos):
        super().__init__()
        self.nombre = nombre
        self.datos  = datos

    def run(self):
        # Este método se ejecuta al llamar .start()
        print(f"[{self.nombre}] procesando {len(self.datos)} elementos")
        # ... lógica aquí ...

hilo = MiHilo("Worker-1", [1, 2, 3])
hilo.start()
hilo.join()
```

### `ThreadPoolExecutor` — Pool de hilos (forma moderna)

```python
from concurrent.futures import ThreadPoolExecutor, as_completed

def descargar(url):
    import requests
    r = requests.get(url, timeout=10)
    return url, r.status_code

urls = [
    "https://ejemplo.com",
    "https://python.org",
    "https://github.com",
]

with ThreadPoolExecutor(max_workers=5) as executor:
    # Enviar todas las tareas
    futuros = {executor.submit(descargar, url): url for url in urls}

    # Recoger resultados conforme terminan
    for futuro in as_completed(futuros):
        url, status = futuro.result()
        print(f"{url} → {status}")
```

### `map` con ThreadPoolExecutor

```python
from concurrent.futures import ThreadPoolExecutor

def procesar(n):
    return n * n

with ThreadPoolExecutor(max_workers=4) as executor:
    resultados = list(executor.map(procesar, range(10)))

print(resultados)  # [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

---

### Sincronización: `Lock`

Cuando varios hilos modifican la misma variable, puede haber condiciones de carrera. Usar `Lock` para proteger la sección crítica.

```python
import threading

contador = 0
lock = threading.Lock()

def incrementar(n):
    global contador
    for _ in range(n):
        with lock:  # solo un hilo a la vez puede entrar aquí
            contador += 1

hilos = [threading.Thread(target=incrementar, args=(1000,)) for _ in range(5)]
for h in hilos: h.start()
for h in hilos: h.join()

print(f"Contador final: {contador}")  # siempre 5000
```

### Evento entre hilos: `Event`

```python
import threading, time

evento = threading.Event()

def esperar_señal():
    print("Esperando señal...")
    evento.wait()  # bloquea hasta que se llame evento.set()
    print("¡Señal recibida!")

def enviar_señal():
    time.sleep(2)
    print("Enviando señal")
    evento.set()

t1 = threading.Thread(target=esperar_señal)
t2 = threading.Thread(target=enviar_señal)
t1.start(); t2.start()
t1.join();  t2.join()
```

### Hilo demonio

Un hilo demonio muere automáticamente cuando el programa principal termina. Útil para tareas de fondo (logs, monitorización).

```python
t = threading.Thread(target=mi_funcion, daemon=True)
t.start()
# Si el main termina, este hilo también muere
```

---

## `multiprocessing`

### Uso básico

```python
import multiprocessing
import time

def tarea(nombre):
    print(f"[{nombre}] PID: {multiprocessing.current_process().pid}")
    time.sleep(2)
    print(f"[{nombre}] Finalizado")

p1 = multiprocessing.Process(target=tarea, args=("Proceso-1",))
p2 = multiprocessing.Process(target=tarea, args=("Proceso-2",))

p1.start()
p2.start()

p1.join()
p2.join()
```

### `ProcessPoolExecutor` — Pool de procesos (forma moderna)

```python
from concurrent.futures import ProcessPoolExecutor

def calcular(n):
    return sum(i * i for i in range(n))

datos = [1_000_000, 2_000_000, 3_000_000, 4_000_000]

if __name__ == "__main__":  # necesario en Windows/macOS
    with ProcessPoolExecutor(max_workers=4) as executor:
        resultados = list(executor.map(calcular, datos))
    print(resultados)
```

> En Windows y macOS siempre envolver el código en `if __name__ == "__main__":` para evitar errores al crear procesos.

---

### Comunicación entre procesos: `Queue`

```python
import multiprocessing

def productor(q):
    for i in range(5):
        q.put(i)
        print(f"Producido: {i}")
    q.put(None)  # señal de fin

def consumidor(q):
    while True:
        item = q.get()
        if item is None:
            break
        print(f"Consumido: {item}")

if __name__ == "__main__":
    q = multiprocessing.Queue()

    p = multiprocessing.Process(target=productor, args=(q,))
    c = multiprocessing.Process(target=consumidor, args=(q,))

    p.start(); c.start()
    p.join();  c.join()
```

### Valor compartido entre procesos

```python
import multiprocessing

def incrementar(contador, lock):
    for _ in range(1000):
        with lock:
            contador.value += 1

if __name__ == "__main__":
    contador = multiprocessing.Value("i", 0)  # entero compartido
    lock = multiprocessing.Lock()

    procesos = [
        multiprocessing.Process(target=incrementar, args=(contador, lock))
        for _ in range(5)
    ]
    for p in procesos: p.start()
    for p in procesos: p.join()

    print(f"Contador final: {contador.value}")  # 5000
```

---

## `asyncio` — Concurrencia async/await

Para I/O intensivo sin hilos reales. Ideal cuando controlas el código de principio a fin y usas librerías async-compatibles.

```python
import asyncio
import aiohttp  # pip install aiohttp

async def descargar(session, url):
    async with session.get(url) as resp:
        print(f"{url} → {resp.status}")
        return await resp.text()

async def main():
    urls = ["https://ejemplo.com", "https://python.org", "https://github.com"]
    async with aiohttp.ClientSession() as session:
        tareas = [descargar(session, url) for url in urls]
        resultados = await asyncio.gather(*tareas)

asyncio.run(main())
```

---

## Comparativa práctica

### Tarea I/O: mejor `threading` o `asyncio`

```python
# Con threading — descarga de URLs en paralelo
from concurrent.futures import ThreadPoolExecutor
import requests

def fetch(url):
    return requests.get(url).status_code

urls = ["https://python.org"] * 20

with ThreadPoolExecutor(max_workers=10) as ex:
    resultados = list(ex.map(fetch, urls))
```

### Tarea CPU: mejor `multiprocessing`

```python
# Con multiprocessing — cálculo intensivo en paralelo
from concurrent.futures import ProcessPoolExecutor

def factorizar(n):
    factores = []
    d = 2
    while d * d <= n:
        while n % d == 0:
            factores.append(d)
            n //= d
        d += 1
    if n > 1:
        factores.append(n)
    return factores

numeros = [999983, 1299709, 15485863, 32452843]

if __name__ == "__main__":
    with ProcessPoolExecutor() as ex:
        resultados = list(ex.map(factorizar, numeros))
    print(resultados)
```

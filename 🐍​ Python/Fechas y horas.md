# Python — Fechas y horas (`datetime`)


## Importar

```python
from datetime import date, time, datetime, timedelta
```

---

## Tipos principales

| Tipo | Descripción |
|------|-------------|
| `date` | Solo fecha: año, mes, día |
| `time` | Solo hora: hora, minuto, segundo, microsegundo |
| `datetime` | Fecha + hora combinadas |
| `timedelta` | Diferencia o duración de tiempo |

---

## `date` — Trabajar con fechas

```python
from datetime import date

# Fecha actual
hoy = date.today()
print(hoy)           # 2024-01-15
print(hoy.year)      # 2024
print(hoy.month)     # 1
print(hoy.day)       # 15
print(hoy.weekday()) # 0=lunes ... 6=domingo

# Crear una fecha específica
navidad = date(2024, 12, 25)

# Diferencia entre fechas (devuelve timedelta)
dias_hasta = navidad - hoy
print(dias_hasta.days)  # número de días
```

---

## `time` — Trabajar con horas

```python
from datetime import time

# Crear una hora
hora = time(14, 30, 0)       # 14:30:00
hora = time(14, 30, 45, 500000)  # 14:30:45.500000

print(hora.hour)        # 14
print(hora.minute)      # 30
print(hora.second)      # 45
print(hora.microsecond) # 500000
```

---

## `datetime` — Fecha y hora combinadas

```python
from datetime import datetime

# Momento actual
ahora = datetime.now()
print(ahora)            # 2024-01-15 14:30:45.123456

# Solo fecha o solo hora desde un datetime
ahora.date()
ahora.time()

# Crear un datetime específico
dt = datetime(2024, 6, 15, 10, 30, 0)

# Acceder a componentes
dt.year    # 2024
dt.month   # 6
dt.day     # 15
dt.hour    # 10
dt.minute  # 30
dt.second  # 0
```

---

## `timedelta` — Duraciones y aritmética

```python
from datetime import datetime, timedelta

ahora = datetime.now()

# Sumar y restar tiempo
manana          = ahora + timedelta(days=1)
hace_una_semana = ahora - timedelta(weeks=1)
en_dos_horas    = ahora + timedelta(hours=2)
hace_30_min     = ahora - timedelta(minutes=30)

# Parámetros disponibles
delta = timedelta(
    days=1,
    seconds=30,
    microseconds=0,
    milliseconds=500,
    minutes=15,
    hours=2,
    weeks=1
)

# Diferencia entre dos datetimes
inicio = datetime(2024, 1, 1)
fin    = datetime(2024, 3, 15)
diff   = fin - inicio
print(diff.days)         # 74
print(diff.total_seconds())  # segundos totales
```

---

## Formatear fechas: `strftime()`

Convierte un `datetime` a string con el formato que quieras.

```python
ahora = datetime.now()

ahora.strftime("%Y-%m-%d")             # "2024-01-15"
ahora.strftime("%d/%m/%Y")             # "15/01/2024"
ahora.strftime("%d/%m/%Y %H:%M:%S")   # "15/01/2024 14:30:45"
ahora.strftime("%A, %d de %B de %Y")  # "Monday, 15 de January de 2024"
ahora.strftime("%H:%M")               # "14:30"
ahora.strftime("%I:%M %p")            # "02:30 PM"  (formato 12h)
```

### Códigos de formato más usados

| Código | Descripción | Ejemplo |
|--------|-------------|---------|
| `%Y` | Año con siglo | 2024 |
| `%y` | Año sin siglo | 24 |
| `%m` | Mes (01-12) | 01 |
| `%B` | Nombre del mes | January |
| `%b` | Nombre abreviado | Jan |
| `%d` | Día (01-31) | 15 |
| `%A` | Nombre del día | Monday |
| `%a` | Nombre abreviado | Mon |
| `%H` | Hora 24h (00-23) | 14 |
| `%I` | Hora 12h (01-12) | 02 |
| `%M` | Minutos (00-59) | 30 |
| `%S` | Segundos (00-59) | 45 |
| `%p` | AM/PM | PM |
| `%f` | Microsegundos | 123456 |
| `%j` | Día del año (001-366) | 015 |
| `%W` | Semana del año | 03 |

---

## Parsear strings: `strptime()`

Convierte un string a `datetime` según el formato indicado.

```python
# String → datetime
fecha_str = "15/01/2024 14:30:00"
dt = datetime.strptime(fecha_str, "%d/%m/%Y %H:%M:%S")
print(dt)       # 2024-01-15 14:30:00
print(type(dt)) # <class 'datetime.datetime'>

fecha_str2 = "2024-01-15"
dt2 = datetime.strptime(fecha_str2, "%Y-%m-%d")
```

---

## Comparar fechas

```python
from datetime import datetime

d1 = datetime(2024, 1, 1)
d2 = datetime(2024, 6, 15)

d1 < d2    # True
d1 == d2   # False
d2 > d1    # True

# Ordenar una lista de fechas
fechas = [datetime(2024, 3, 1), datetime(2024, 1, 5), datetime(2024, 2, 20)]
fechas_ordenadas = sorted(fechas)
```

---

## Timestamp (Unix time)

```python
import time
from datetime import datetime

# Timestamp actual (segundos desde 1970-01-01 00:00:00 UTC)
ts = time.time()
print(ts)  # 1705315845.123

# Timestamp → datetime
dt = datetime.fromtimestamp(ts)

# datetime → timestamp
ts = dt.timestamp()

# UTC
dt_utc = datetime.utcnow()
```

---

## Zonas horarias con `zoneinfo` (Python 3.9+)

```python
from datetime import datetime
from zoneinfo import ZoneInfo

# Crear datetime con zona horaria
madrid  = datetime.now(tz=ZoneInfo("Europe/Madrid"))
ny      = datetime.now(tz=ZoneInfo("America/New_York"))
utc     = datetime.now(tz=ZoneInfo("UTC"))

print(madrid.strftime("%H:%M %Z"))   # "14:30 CET"

# Convertir entre zonas
en_tokyo = madrid.astimezone(ZoneInfo("Asia/Tokyo"))
```

> En Python < 3.9 se usa la librería externa `pytz` con la misma lógica:
> ```python
> import pytz
> zona = pytz.timezone("Europe/Madrid")
> dt = datetime.now(tz=zona)
> ```

---

## Ejemplos prácticos

### Calcular edad

```python
from datetime import date

def calcular_edad(fecha_nacimiento: date) -> int:
    hoy = date.today()
    edad = hoy.year - fecha_nacimiento.year
    # Ajuste si aún no ha llegado el cumpleaños este año
    if (hoy.month, hoy.day) < (fecha_nacimiento.month, fecha_nacimiento.day):
        edad -= 1
    return edad

print(calcular_edad(date(1995, 8, 20)))
```

### Medir tiempo de ejecución

```python
from datetime import datetime

inicio = datetime.now()
# ... código a medir ...
fin = datetime.now()

duracion = fin - inicio
print(f"Tardó {duracion.total_seconds():.3f} segundos")
```

### Generar nombre de archivo con timestamp

```python
from datetime import datetime

timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
nombre = f"backup_{timestamp}.zip"
print(nombre)  # "backup_20240115_143045.zip"
```

### Comprobar si una fecha es fin de semana

```python
from datetime import date

def es_fin_de_semana(d: date) -> bool:
    return d.weekday() >= 5  # 5=sábado, 6=domingo

print(es_fin_de_semana(date.today()))
```

# Python — Expresiones regulares (`re`)

## Importar

```python
import re
```

---

## Funciones principales

| Función | Descripción |
|---------|-------------|
| `re.search()` | Busca el patrón en cualquier parte del string. Devuelve el primer match o `None`. |
| `re.match()` | Comprueba si el string **empieza** con el patrón. |
| `re.fullmatch()` | Comprueba si el patrón coincide con el string **completo**. |
| `re.findall()` | Devuelve una lista con todas las coincidencias. |
| `re.finditer()` | Igual que `findall` pero devuelve un iterador de objetos match. |
| `re.sub()` | Sustituye las coincidencias por otra cadena. |
| `re.split()` | Divide el string usando el patrón como separador. |
| `re.compile()` | Compila un patrón para reutilizarlo (más eficiente en bucles). |

---

## `re.findall()` — Encontrar todas las coincidencias

```python
texto = "Mi gato está en el tejado y mi otro gato está en el jardín"

matches = re.findall("gato", texto)
print(matches)  # ['gato', 'gato']
```

Con patrón numérico:

```python
texto = "Hoy es 10/10/2023 y mañana es 11/10/2023"

matches = re.findall(r"\d{2}/\d{2}/\d{4}", texto)
print(matches)  # ['10/10/2023', '11/10/2023']
```

Con grupos (paréntesis): cada grupo se captura por separado:

```python
texto = "Contacto: soporte@empresa.io o info@empresa.io"

matches = re.findall(r"(\w+)@([\w.]+)", texto)
print(matches)  # [('soporte', 'empresa.io'), ('info', 'empresa.io')]
```

---

## `re.search()` — Buscar primera coincidencia

```python
texto = "El precio es 42 euros"

match = re.search(r"\d+", texto)
if match:
    print(match.group())   # "42"
    print(match.start())   # posición de inicio
    print(match.end())     # posición de fin
    print(match.span())    # (13, 15)
```

---

## `re.match()` — Comprobar inicio

```python
# Solo comprueba desde el principio del string
if re.match(r"\d{4}", "2024-01-01"):
    print("Empieza con año")

if not re.match(r"\d{4}", "Año: 2024"):
    print("No empieza con año")  # este se ejecuta
```

---

## `re.sub()` — Sustituir

```python
texto = "Mi gato está en el tejado y mi gato en el jardín"

nuevo = re.sub(r"gato", "perro", texto)
print(nuevo)  # "Mi perro está en el tejado y mi perro en el jardín"

# Limitar el número de sustituciones
nuevo = re.sub(r"gato", "perro", texto, count=1)

# Usar una función como reemplazo
nuevo = re.sub(r"\d+", lambda m: str(int(m.group()) * 2), "a1 b2 c3")
print(nuevo)  # "a2 b4 c6"
```

---

## `re.split()` — Dividir por patrón

```python
texto = "Campo1,Campo2;Campo3 Campo4"

# Separar por coma
partes = re.split(r",", texto)
print(partes)  # ['Campo1', 'Campo2;Campo3 Campo4']

# Separar por coma, punto y coma o espacio
partes = re.split(r"[,; ]+", texto)
print(partes)  # ['Campo1', 'Campo2', 'Campo3', 'Campo4']
```

---

## `re.finditer()` — Iterador de matches

Útil para archivos grandes o cuando necesitas información de posición:

```python
texto = "error en línea 10, warning en línea 25, error en línea 42"
patron = r"error en línea (\d+)"

for match in re.finditer(patron, texto):
    print(f"Match: {match.group()} | Número de línea: {match.group(1)} | Posición: {match.span()}")
```

---

## `re.compile()` — Compilar para reutilizar

Cuando usas el mismo patrón muchas veces, compilarlo mejora el rendimiento:

```python
patron = re.compile(r"\b[A-Z]{2,}\b")  # palabras en mayúsculas de 2+ letras

textos = ["El DNS falla", "Revisar el IP y el MAC", "Sin problema"]
for t in textos:
    matches = patron.findall(t)
    if matches:
        print(matches)
```

---

## Flags modificadores

Se pasan como tercer argumento o dentro de `re.compile()`:

| Flag | Descripción |
|------|-------------|
| `re.IGNORECASE` / `re.I` | Ignora mayúsculas/minúsculas |
| `re.MULTILINE` / `re.M` | `^` y `$` funcionan por línea, no por string completo |
| `re.DOTALL` / `re.S` | `.` también captura saltos de línea |
| `re.VERBOSE` / `re.X` | Permite comentarios y espacios en el patrón |

```python
re.findall(r"hola", "Hola mundo HOLA", re.IGNORECASE)
# ['Hola', 'HOLA']

patron = re.compile(r"""
    (\d{1,3})   # primer octeto
    \.(\d{1,3}) # segundo octeto
    \.(\d{1,3}) # tercer octeto
    \.(\d{1,3}) # cuarto octeto
""", re.VERBOSE)
```

---

## Referencia de metacaracteres

### Clases de caracteres

| Patrón | Coincide con |
|--------|-------------|
| `.` | Cualquier carácter excepto `\n` |
| `\d` | Dígito `[0-9]` |
| `\D` | No dígito |
| `\w` | Palabra `[a-zA-Z0-9_]` |
| `\W` | No palabra |
| `\s` | Espacio en blanco (espacio, tab, `\n`…) |
| `\S` | No espacio |
| `[abc]` | a, b o c |
| `[^abc]` | Cualquier cosa excepto a, b, c |
| `[a-z]` | Letra minúscula |
| `[A-Za-z0-9]` | Alfanumérico |

### Cuantificadores

| Patrón | Descripción |
|--------|-------------|
| `*` | 0 o más |
| `+` | 1 o más |
| `?` | 0 o 1 (opcional) |
| `{n}` | Exactamente n veces |
| `{n,}` | n o más veces |
| `{n,m}` | Entre n y m veces |
| `*?` / `+?` | Versión no greedy (mínimo posible) |

### Anclas y límites

| Patrón | Descripción |
|--------|-------------|
| `^` | Inicio del string (o línea con `re.M`) |
| `$` | Fin del string (o línea con `re.M`) |
| `\b` | Límite de palabra |
| `\B` | No límite de palabra |

### Grupos

| Patrón | Descripción |
|--------|-------------|
| `(abc)` | Grupo de captura |
| `(?:abc)` | Grupo sin captura |
| `(?P<nombre>abc)` | Grupo con nombre |
| `a\|b` | a o b |

---

## Ejemplos prácticos

### Validar email

```python
def es_email_valido(correo):
    patron = re.compile(r"^[A-Za-z0-9._%+\-]+@[A-Za-z0-9.\-]+\.[A-Za-z]{2,}$")
    return bool(patron.fullmatch(correo))

es_email_valido("usuario@ejemplo.com")  # True
es_email_valido("no-es-un-email")       # False
```

### Validar IPv4

```python
def es_ipv4(ip):
    patron = r"^(\d{1,3}\.){3}\d{1,3}$"
    if not re.match(patron, ip):
        return False
    return all(0 <= int(n) <= 255 for n in ip.split("."))

es_ipv4("192.168.1.1")   # True
es_ipv4("999.0.0.1")     # False
```

### Extraer URLs de un texto

```python
texto = "Visita https://ejemplo.com o http://otro.org/path?q=1 para más info"
urls = re.findall(r"https?://[^\s]+", texto)
print(urls)  # ['https://ejemplo.com', 'http://otro.org/path?q=1']
```

### Limpiar espacios múltiples

```python
texto = "Esto   tiene    espacios    extra"
limpio = re.sub(r"\s+", " ", texto).strip()
print(limpio)  # "Esto tiene espacios extra"
```

### Extraer grupos con nombre

```python
log = "2024-01-15 ERROR módulo_auth: token inválido"

patron = re.compile(
    r"(?P<fecha>\d{4}-\d{2}-\d{2}) (?P<nivel>\w+) (?P<modulo>\S+): (?P<mensaje>.+)"
)

match = patron.match(log)
if match:
    print(match.group("fecha"))    # "2024-01-15"
    print(match.group("nivel"))    # "ERROR"
    print(match.group("mensaje"))  # "token inválido"
    print(match.groupdict())       # diccionario con todos los grupos
```

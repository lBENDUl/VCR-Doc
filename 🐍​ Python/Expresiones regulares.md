#python

La librería ‘**re**‘ en Python proporciona un conjunto completo de herramientas para trabajar con expresiones regulares, que son patrones de cadenas diseñados para la búsqueda y manipulación de texto.

Aquí hay varios aspectos importantes de esta librería:

- **Funciones Básicas**: ‘**re**‘ incluye funciones como ‘**search**‘ (para buscar un patrón dentro de una cadena), ‘**match**‘ (para verificar si una cadena comienza con un patrón específico), ‘**findall**‘ (para encontrar todas las ocurrencias de un patrón), y ‘**sub**‘ (para reemplazar partes de una cadena que coinciden con un patrón).
- **Compilación de Patrones**: Permite compilar expresiones regulares en objetos de patrón, lo que puede mejorar el rendimiento cuando se usan repetidamente.
- **Grupos y Captura**: Ofrece la capacidad de definir grupos dentro de patrones de expresiones regulares, lo que facilita extraer partes específicas de una cadena que coinciden con subpatrones.
- **Flags**: Soporta modificadores que alteran la forma en que las expresiones regulares son interpretadas y coincididas, como ignorar mayúsculas y minúsculas o permitir el modo multilínea.
- **Patrones Complejos**: Permite la creación de patrones complejos utilizando una variedad de símbolos y secuencias especiales, como cuantificadores, aserciones y clases de caracteres.
- **Aplicaciones Prácticas**: Las expresiones regulares son extremadamente útiles en tareas como la validación de formatos (por ejemplo, direcciones de correo electrónico), el análisis de registros (logs), el procesamiento de lenguaje natural, y la limpieza y preparación de datos.
- **Curva de Aprendizaje**: Aunque potentes, las expresiones regulares pueden ser complejas y requieren una curva de aprendizaje. Sin embargo, una vez dominadas, se convierten en una herramienta invaluable en el arsenal de cualquier programador.

En resumen, la librería ‘**re**‘ en Python es una herramienta esencial para cualquier tarea que implique procesamiento complejo de cadenas de texto, proporcionando una forma poderosa y flexible de buscar, analizar, y manipular datos basados en texto.

# re.findall()

```python
import re

text = "Mi gato está en el tejado y mi otro gato está en el jardin"

matches = re.findall("gato", text)

print(matches) #['gato', 'gato']
```

```python
text = "Hoy es 10/10/2020"

matches = re.findall("\d{2}\/\d{2}\/\d{4}", text)
```


Los paréntesis indican el caracter o los caracteres que queremos recoger.

```python
text = "Los usuarios pueden contactarnos a soporte@hack4u.io o a info@hack4u.io"

matches = re.findall("(\w+)@(\w+\.\w{2,})", text)

print(matches) # [('soporte','hack4u.io'),('info','hack4u.io')]
```

Patrones

```python
import re

def validar_correo(correo):
	patron = "[A-Za-z0-9._+-]+@[A-Za-z0-9]+\.[A-Za-z]{2,}"
	print(re.findall(patron, correo))

validar_correo("soporte@hack4u.io")
```

# re.sub()

Sustituye una cadena por otra.

```python
import re

texto = "Mi gato está en el tejado y mi perro está en el jardín"

nuevo_texto = re.sub("gato", "perro", texto)

print(nuevo_texto) # Mi perro está en el tejado y mi perro está en el jardín
```

# re.split()

Separa una cadena a partir de un carácter o cadena.

```python
import re

texto = "Campo1,Campo2,Campo3,Campo4,Campo5"

nuevo_texto = re.split(",", texto)

print(nuevo_texto) # ['Campo1','Campo2','Campo3','Campo4','Campo5']
```

# re.finditer()

```python
for match in re.finditer(patron, texto):
	print(match.group(0))
```
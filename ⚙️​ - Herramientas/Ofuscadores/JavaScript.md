# JavaScript — Ofuscación y Deofuscación

Cuando se analiza una aplicación web, el código JavaScript del cliente puede estar ofuscado o minimizado para dificultar su lectura. Entender cómo revertirlo es esencial en análisis de código, bug bounty y CTFs.

---

## Herramientas de referencia

- Consola online: [jsconsole.com](https://jsconsole.com/)
- Ofuscador avanzado: [obfuscator.io](https://obfuscator.io/)
- Desofuscador: [matthewfl.com/unPacker.html](https://matthewfl.com/unPacker.html)
- Beautifier: [beautifier.io](https://beautifier.io/) / [prettier.io](https://prettier.io/playground/)

---

## Ofuscación

### Minimización

Elimina espacios, saltos de línea y renombra variables para reducir el tamaño. Herramienta: [javascript-minifier.com](https://javascript-minifier.com/).

### Ofuscadores básicos

- [BeautifyTools JS Obfuscator](http://beautifytools.com/javascript-obfuscator.php) — produce el patrón `function(p,a,c,k,e,d)` conocido como *packing*
- [JJEncode](https://utf-8.jp/public/jjencode.html)
- [AAEncode](https://utf-8.jp/public/aaencode.html)

### Ofuscación avanzada con obfuscator.io

1. Ir a [obfuscator.io](https://obfuscator.io/)
2. Cambiar `String Array Encoding` a `Base64`
3. Pegar el código y pulsar `Obfuscate`

---

## Deofuscación

### Beautify desde el navegador

En Firefox: `F12 → Depurador → seleccionar el script → botón { }` (Pretty Print).

### UnPacker

Para código empaquetado con el patrón `function(p,a,c,k,e,d)`:

1. Abrir [matthewfl.com/unPacker.html](https://matthewfl.com/unPacker.html)
2. Pegar el código ofuscado
3. Clic en `UnPack`

> ⚠️ No dejar espacios al inicio del código o no funcionará correctamente.

---

## Codificación en respuestas de API

Las APIs y el código JS suelen codificar datos. Las tres más habituales:

### Base64

```bash
# Codificar
echo "https://ejemplo.com" | base64

# Decodificar
echo "aHR0cHM6Ly9lamVtcGxvLmNvbQo=" | base64 -d
```

Reconocible por el alfabeto alfanumérico + `+`, `/` y padding con `=`.

### Hex

```bash
# Codificar
echo "https://ejemplo.com" | xxd -p

# Decodificar
echo "68747470733a2f2f..." | xxd -p -r
```

Reconocible porque solo contiene caracteres `0-9` y `a-f`.

### ROT13

```bash
# Codificar
echo "https://ejemplo.com" | tr 'A-Za-z' 'N-ZA-Mn-za-m'

# Decodificar (misma operación)
echo "uggcf://rwhzcbe.pbz" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

También disponible en: [rot13.com](https://rot13.com/)

---

## Identificar el tipo de codificación

Si no está claro qué codificación se está usando: [Cipher Identifier — boxentriq.com](https://www.boxentriq.com/code-breaking/cipher-identifier)

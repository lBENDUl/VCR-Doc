# Padding Oracle Attack

Un ataque de Padding Oracle permite a un atacante **descifrar datos cifrados sin conocer la clave**, aprovechando que la aplicación revela (a través de su comportamiento) si el relleno de un bloque descifrado es válido o no.

---

## Conceptos criptográficos previos

### Cifrado por bloques y relleno (padding)

Los cifrados de bloque (DES, AES, etc.) operan sobre bloques de tamaño fijo (8 o 16 bytes). Si el mensaje no ocupa exactamente un número entero de bloques, se añade un **relleno** al final para completarlo.

El estándar más usado es **PKCS #7**: el relleno consiste en repetir el byte que indica cuántos bytes se han añadido. Por ejemplo, si faltan 6 bytes, se añaden seis bytes con el valor `0x06`.

### Modo CBC

El modo **CBC** (Cipher Block Chaining) encadena los bloques: cada bloque antes de cifrarse se combina mediante XOR con el bloque cifrado anterior. El primer bloque usa un **vector de inicialización (IV)** aleatorio.

Durante el descifrado, si el relleno del último bloque no es válido, la aplicación lo detecta. Aquí es donde surge la vulnerabilidad.

---

## Por qué es vulnerable

Si la aplicación indica de alguna forma que el relleno es inválido (con un mensaje de error, un código HTTP diferente, o incluso un tiempo de respuesta distinto), un atacante puede enviar bloques modificados y, observando las respuestas, descifrar el mensaje **byte a byte** sin necesitar la clave.

El "oráculo" puede ser tan sutil como:
- Un mensaje de error diferente
- Una redirección en lugar de un 200
- Una diferencia de pocos milisegundos en la respuesta

---

## Herramienta: PadBuster

**PadBuster** automatiza todo el proceso. Ver la guía de referencia: [PadBuster](./PadBuster.md)

### Descifrar una cookie existente

```bash
padbuster http://192.168.1.17/index.php \
  3U45ZrgkKaW3wGiEwijfk32p72vwyKOH \
  8 \
  -cookies 'auth=3U45ZrgkKaW3wGiEwijfk32p72vwyKOH'
```

Salida:
```
[+] Decrypted value (ASCII): user=bendu
```

### Forjar una cookie con un valor arbitrario

Una vez conocido el formato del texto plano, se puede crear una cookie válida para cualquier usuario:

```bash
padbuster http://192.168.1.17/index.php \
  3U45ZrgkKaW3wGiEwijfk32p72vwyKOH \
  8 \
  -cookies 'auth=3U45ZrgkKaW3wGiEwijfk32p72vwyKOH' \
  -plaintext 'user=admin'
```

La cookie resultante, al colocarla en el navegador, dará acceso a la sesión de `admin`.

---

## Técnica complementaria: Bit Flipper (Burp Suite)

Si el objetivo es acceder a una cuenta cuyo nombre difiere en pocos caracteres del propio (ej. `bdmin` vs `admin`), los bloques cifrados serán muy similares. Se puede usar el ataque **Sniper - Bit Flipper** de Burp Suite:

1. Crear una cuenta con un nombre parecido (ej. `bdmin`) y obtener su cookie
2. Interceptar la petición con Burp Suite → enviar al Intruder
3. Seleccionar la cookie como payload y configurar el ataque tipo **Bit Flipper**
4. Las respuestas con un tamaño diferente al habitual indican una cookie válida para otro usuario

---

## Mitigaciones

- Combinar el cifrado CBC con un **HMAC** que valide la integridad de los datos antes de descifrar. Si el HMAC no es válido, rechazar la petición sin llegar a descifrar
- La comparación del HMAC debe ser de **tiempo constante** para no introducir un nuevo oráculo de temporización
- Usar modos de cifrado autenticados modernos como **AES-GCM**, que combinan cifrado e integridad en uno

---

## Laboratorio para practicar

- **Pentester Lab – Padding Oracle** (Vulnhub): [https://www.vulnhub.com/?q=padding+oracle](https://www.vulnhub.com/?q=padding+oracle)

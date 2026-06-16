# Hashcat

Hashcat es la herramienta de cracking de hashes más rápida disponible. Aprovecha la GPU para realizar ataques de fuerza bruta, diccionario, y reglas a velocidades muy superiores a las herramientas basadas en CPU.

---

## Identificar el tipo de hash

Antes de crackear, hay que saber qué algoritmo generó el hash. Herramientas útiles:

```bash
# hashid
hashid '$2y$10$abc...'

# hash-identifier
hash-identifier

# Ejemplo de hashes comunes
echo -n "texto" | md5sum        # MD5
echo -n "texto" | sha1sum       # SHA1
echo -n "texto" | sha256sum     # SHA256
```

---

## Modos de hash más comunes (`-m`)

| Modo | Algoritmo |
|---|---|
| `0` | MD5 |
| `100` | SHA1 |
| `1400` | SHA256 |
| `1800` | SHA512crypt (`$6$`) — Linux shadow |
| `3200` | bcrypt (`$2*$`) |
| `5500` | NetNTLMv1 |
| `5600` | NetNTLMv2 |
| `1000` | NTLM |
| `13100` | Kerberoast (TGS-REP) |
| `18200` | AS-REP Roasting |

---

## Tipos de ataque (`-a`)

| Modo | Tipo |
|---|---|
| `0` | Diccionario (wordlist) |
| `1` | Combinación (dos wordlists) |
| `3` | Fuerza bruta / máscara |
| `6` | Wordlist + máscara |
| `7` | Máscara + wordlist |

---

## Uso básico

```bash
# Ataque de diccionario (el más habitual)
hashcat -m 0 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt

# Con reglas (aumenta variantes: mayúsculas, números al final, etc.)
hashcat -m 0 -a 0 hashes.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule

# Fuerza bruta con máscara (8 caracteres: 4 letras min + 4 dígitos)
hashcat -m 0 -a 3 hashes.txt ?l?l?l?l?d?d?d?d

# Ver el resultado (hashes crackeados)
hashcat -m 0 hashes.txt --show
```

---

## Máscaras

| Símbolo | Juego de caracteres |
|---|---|
| `?l` | Minúsculas (a-z) |
| `?u` | Mayúsculas (A-Z) |
| `?d` | Dígitos (0-9) |
| `?s` | Especiales (`!@#$...`) |
| `?a` | Todos los anteriores |

---

## Opciones útiles

```bash
# Mostrar velocidad estimada sin ejecutar el ataque
hashcat -m 1000 hashes.txt rockyou.txt --speed-only

# Guardar resultado en archivo
hashcat -m 0 hashes.txt rockyou.txt -o crackeados.txt

# Continuar un ataque interrumpido
hashcat -m 0 hashes.txt rockyou.txt --restore

# Usar CPU en lugar de GPU (más lento, para entornos virtuales)
hashcat -m 0 hashes.txt rockyou.txt -D 1
```

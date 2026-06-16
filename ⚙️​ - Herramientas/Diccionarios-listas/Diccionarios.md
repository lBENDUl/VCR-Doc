# Diccionarios para Fuerza Bruta

Los diccionarios (o wordlists) son listas de palabras, contraseñas o rutas utilizadas en ataques de fuerza bruta y fuzzing. Elegir el diccionario adecuado para cada situación es clave para la eficiencia de la auditoría.

---

## RockYou

`rockyou.txt` es el diccionario de contraseñas más utilizado en pentesting. Se compiló a partir de una brecha de datos real de 2009 en la plataforma RockYou, exponiendo más de 32 millones de contraseñas en texto claro.

**Ubicación en Kali Linux / Parrot:**
```bash
/usr/share/wordlists/rockyou.txt

# Si está comprimido:
gunzip /usr/share/wordlists/rockyou.txt.gz
```

**Características:**
- Más de 14 millones de contraseñas únicas
- Contraseñas reales de usuarios reales — alta tasa de éxito en entornos sin políticas de contraseñas
- Incluye patrones comunes: palabras simples, combinaciones de letras y números, nombres propios

---

## SecLists

La colección más completa para pentesting. Incluye wordlists para contraseñas, rutas web, subdominios, nombres de usuario, payloads de fuzzing, y más.

```bash
# Instalar en Kali/Parrot
sudo apt install seclists

# Ubicación
/usr/share/seclists/
```

Estructura de directorios relevante:

```
/usr/share/seclists/
├── Discovery/
│   ├── Web-Content/          # Rutas web (directorios, archivos)
│   │   ├── common.txt
│   │   ├── directory-list-2.3-medium.txt
│   │   └── api/
│   ├── DNS/                  # Subdominios
│   └── Infrastructure/
├── Passwords/
│   ├── Common-Credentials/   # Top contraseñas más usadas
│   └── Leaked-Databases/     # Brechas de datos reales
├── Usernames/
└── Fuzzing/
    ├── LFI/                  # Payloads para LFI
    ├── SQLi/
    └── LDAP-openldap-attributes.txt
```

---

## Otros diccionarios útiles

| Diccionario | Uso |
|---|---|
| `rockyou.txt` | Contraseñas generales, primera opción en CTF |
| `SecLists/Passwords/Common-Credentials/10k-most-common.txt` | Contraseñas más usadas, rápido |
| `SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt` | Fuzzing de directorios web |
| `SecLists/Discovery/DNS/subdomains-top1million-5000.txt` | Enumeración de subdominios |
| `SecLists/Usernames/Names/names.txt` | Enumeración de usuarios |

---

## Generar diccionarios personalizados

### CeWL — basado en el contenido del sitio web

Genera un wordlist a partir del texto de una web. Útil cuando la organización usa términos propios:

```bash
cewl http://target.com -d 3 -m 6 -w custom_wordlist.txt
```

### Crunch — por patrón

Genera combinaciones siguiendo un patrón definido:

```bash
# Contraseñas de 8 caracteres: 4 minúsculas + 4 números
crunch 8 8 -t @@@@%%%% -o output.txt

# Todas las combinaciones de 6 caracteres alfanuméricos
crunch 6 6 abcdefghijklmnopqrstuvwxyz0123456789 -o output.txt
```

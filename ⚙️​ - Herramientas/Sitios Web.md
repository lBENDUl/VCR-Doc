# Recursos Online — Vulnerabilidades y Exploits

Referencias clave para buscar vulnerabilidades conocidas, exploits públicos y calcular el riesgo de un CVE.

---

## Bases de datos de exploits

| Recurso | Descripción |
|---|---|
| [exploit-db.com](https://www.exploit-db.com/) | Base de datos de exploits públicos. Mantenida por Offensive Security. Incluye exploits verificados y el archivo de Google Hacking Database (GHDB) |
| [rapid7.com/db](https://www.rapid7.com/db/) | Base de datos de vulnerabilidades y módulos de Metasploit asociados |
| [vulnerability-lab.com](https://www.vulnerability-lab.com/) | Repositorio de vulnerabilidades con PoCs y advisories de seguridad |

---

## Calculadora de severidad CVSS

El sistema CVSS (Common Vulnerability Scoring System) puntúa vulnerabilidades del 0 al 10 según su impacto y explotabilidad.

- [nvd.nist.gov — CVSS v3 Calculator](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator)

Útil para evaluar el riesgo real de un CVE en un informe de pentest o para priorizar remediaciones.

---

## Búsqueda de CVEs

```bash
# Buscar desde línea de comandos con searchsploit (Kali)
searchsploit <nombre_software> <versión>

# Ejemplo
searchsploit apache 2.4.49
```

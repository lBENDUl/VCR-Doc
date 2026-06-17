# 🗄️ DNS

DNS (Domain Name System) es el sistema que traduce nombres de dominio a direcciones IP. Su enumeración en reconocimiento permite descubrir subdominios, servidores de correo, zonas DNS internas y posibles vectores de ataque.

---

## Tipos de registros relevantes

| Registro | Descripción |
|---|---|
| `A` | IPv4 del dominio |
| `AAAA` | IPv6 del dominio |
| `MX` | Servidores de correo |
| `NS` | Servidores de nombres autoritativos |
| `TXT` | Texto libre (SPF, DKIM, verificaciones) |
| `CNAME` | Alias de otro dominio |
| `PTR` | Resolución inversa (IP → nombre) |
| `SOA` | Información de la zona DNS |
| `SRV` | Servicios específicos (VoIP, LDAP...) |

---

## Consultas manuales

```bash
# Registro A
dig ejemplo.com
dig ejemplo.com A +short

# Registro MX
dig ejemplo.com MX
host -t MX ejemplo.com

# Todos los registros
dig ejemplo.com ANY

# Consultar servidor DNS específico
dig @8.8.8.8 ejemplo.com

# Resolución inversa
dig -x 1.2.3.4
```

---

## Transferencia de zona (AXFR)

Si el servidor DNS está mal configurado, permite descargar toda la zona — todos los registros del dominio de una vez.

```bash
# Identificar servidores NS
dig NS ejemplo.com

# Intentar transferencia de zona
dig axfr ejemplo.com @ns1.ejemplo.com

# Con host
host -l ejemplo.com ns1.ejemplo.com
```

Si tiene éxito, se obtiene un listado completo de subdominios, IPs internas y estructura de la infraestructura.

---

## Enumeración de subdominios por fuerza bruta

```bash
# dnsenum — completo (fuerza bruta + transferencia de zona + registros)
dnsenum --enum ejemplo.com \
  -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt \
  -r

# dnsrecon
dnsrecon -d ejemplo.com -t brt \
  -D /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# fierce
fierce --domain ejemplo.com
```

---

## Herramientas de referencia

| Herramienta | Uso principal |
|---|---|
| `dig` | Consultas manuales detalladas, AXFR |
| `nslookup` | Consultas rápidas A/MX |
| `host` | Síntesis de registros A/AAAA/MX |
| `dnsenum` | Enumeración automatizada + fuerza bruta |
| `dnsrecon` | Enumeración completa con múltiples técnicas |
| `fierce` | Reconocimiento con detección de wildcards |
| `theHarvester` | OSINT: emails + subdominios desde fuentes públicas |

---

## Ver también

- [Subdominios](subdominios.md) — Técnicas pasivas y activas con Gobuster, Wfuzz, CTFR

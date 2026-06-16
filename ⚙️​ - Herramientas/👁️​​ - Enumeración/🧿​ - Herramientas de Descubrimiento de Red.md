# Herramientas de Descubrimiento de Red

## arp-scan

Escáner de red de capa 2 que utiliza paquetes ARP para descubrir hosts activos en la red local. Al operar a nivel ARP es más fiable que un ping sweep cuando ICMP está bloqueado.

```bash
# Escaneo de la red local en la interfaz ens33
arp-scan -I ens33 --localnet --ignoredups

# Escaneo de un rango específico
arp-scan 192.168.1.0/24

# Mostrar fabricante del dispositivo (OUI lookup)
arp-scan -I eth0 --localnet
```

> `--ignoredups` evita mostrar duplicados cuando un host responde varias veces.

**Ventajas sobre Nmap en red local:** más rápido y detecta hosts que filtran ICMP o TCP.

---

## Masscan

Escáner de puertos de alta velocidad. Puede escanear todo Internet en menos de 6 minutos. Usa su propia pila TCP/IP, independiente del sistema operativo.

```bash
# Escanear puertos comunes en una subred completa
masscan -p21,22,80,139,443,445,8080 192.168.0.0/16 --rate=1000

# Escanear todos los puertos en un host
masscan -p1-65535 10.10.10.5 --rate=5000

# Guardar resultado en formato grepable
masscan -p80,443 192.168.1.0/24 --rate=500 -oG resultado.txt
```

| Parámetro | Descripción |
|---|---|
| `-p` | Puertos o rangos a escanear |
| `--rate` | Paquetes por segundo (ajustar según la red) |
| `-oG` / `-oX` | Salida grepable o XML |
| `--exclude` | Excluir IPs del escaneo |

**Cuándo usar Masscan vs Nmap:** Masscan para descubrimiento rápido en rangos grandes; Nmap para análisis detallado (versiones, scripts, OS) sobre los puertos ya identificados.

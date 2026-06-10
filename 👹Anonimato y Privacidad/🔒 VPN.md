---
tags:
  - Anonimato-Privacidad
  - VPN
---
# VPN

Una VPN (Virtual Private Network) cifra todo el tráfico del sistema y lo enruta a través de un servidor remoto, ocultando la IP real del usuario. A diferencia de un proxy, actúa a nivel de sistema operativo y protege todas las conexiones, no solo las de una aplicación concreta.

---

## ProtonVPN

[ProtonVPN](https://protonvpn.com/es-es) es la opción recomendada por su enfoque en privacidad. Fue creada por el mismo equipo de ProtonMail (CERN/MIT) y tiene sede en Suiza, con leyes de privacidad sólidas.

### Características destacadas

- Cifrado **AES-256** con autenticación HMAC-SHA384
- **Política de cero registros** — no se almacena actividad del usuario
- Protección contra **fugas de DNS**
- **Secure Core**: encadena el tráfico a través de servidores en países con legislación de privacidad fuerte (Suiza, Islandia, Suecia) antes de salir al destino final
- Tiene **plan gratuito** sin límite de datos

### Instalación en Kali Linux

```bash
# Descargar el paquete .deb desde la web oficial
wget https://protonvpn.com/download/protonvpn-stable-release_1.0.3-3_all.deb
sudo dpkg -i protonvpn-stable-release_1.0.3-3_all.deb
sudo apt update
sudo apt install proton-vpn-gnome-desktop
```

---

## VPN + TOR

Combinar VPN con TOR es la configuración más anónima disponible:

1. Conectar a ProtonVPN
2. Usar TOR Browser o ProxyChains con TOR

El proveedor VPN solo ve tráfico cifrado hacia la red TOR. Los nodos TOR solo ven la IP de salida de la VPN, no la IP real.

**Contrapartida:** velocidad significativamente reducida por las capas adicionales.

---

## Referencias

- [protonvpn.com](https://protonvpn.com/es-es)

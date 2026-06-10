---
tags:
  - Anonimato-Privacidad
  - TOR
---
# Red TOR

TOR (The Onion Router) es una red de anonimato desarrollada originalmente por el laboratorio naval de EEUU. Encapsula el tráfico en múltiples capas de cifrado y lo hace rebotar por una serie de nodos operados por voluntarios, de forma que ningún nodo individual conoce tanto el origen como el destino.

---

## Cómo funciona

El tráfico pasa por tres nodos: **entrada → intermedio → salida**. Cada nodo solo conoce al nodo anterior y al siguiente, nunca el recorrido completo. Las capas de cifrado se van eliminando en cada salto (de ahí el nombre "cebolla").

---

## Hidden Services (.onion)

Los hidden services son servidores accesibles únicamente desde la red TOR. Su dirección `.onion` se deriva de una clave pública, por lo que no dependen de DNS convencional. El servidor crea un descriptor con su clave pública y los puntos de entrada, que se almacena en una tabla hash distribuida dentro de la red TOR.

---

## Instalación y uso

### TOR Browser

```bash
sudo apt-get install tor torbrowser-launcher
torbrowser-launcher
```

### Brave Browser con TOR

Brave incluye integración nativa con TOR en sus ventanas privadas (`Nueva ventana privada con Tor`). Ofrece mejor rendimiento que TOR Browser en muchos casos.

### Servicio TOR para ProxyChains

```bash
# Comprobar estado
sudo service tor status

# Iniciar
sudo service tor start
```

Añadir al final de `/etc/proxychains4.conf`:

```
socks5 127.0.0.1 9050
```

Activar `strict_chain` para forzar que todo pase por TOR. A partir de ahí, cualquier herramienta con ProxyChains usará la red TOR:

```bash
proxychains4 nmap -sT -Pn <objetivo>
proxychains4 curl https://<objetivo>
```

---

## Integración con herramientas

### SQLmap a través de TOR

```bash
sudo service tor start
sqlmap -u "http://<objetivo>/page?id=1" --proxy socks5://127.0.0.1:9050
```

### WPScan a través de TOR

```bash
sudo service tor start
wpscan --url http://<objetivo> --proxy socks5://127.0.0.1:9050
```

---

## TOR sobre VPN

Conectarse primero a la VPN y luego usar TOR añade una capa extra: el proveedor VPN no ve el tráfico TOR y los nodos de entrada TOR no ven la IP real.

1. Conectar a ProtonVPN (o cualquier VPN de confianza)
2. Usar TOR Browser o ProxyChains con TOR

Es la configuración más anónima pero también la más lenta.

---

## Verificar si la IP aparece como TOR

- [IP Quality Score — TOR IP Check](https://www.ipqualityscore.com/tor-ip-address-check)

Permite comprobar si una IP es reconocida como nodo de salida de TOR. Muchos servicios bloquean activamente este tipo de IPs.

---

## Limitaciones

- TOR es lento por los múltiples saltos
- Muchos servicios bloquean nodos de salida conocidos
- No cifra el tráfico más allá del nodo de salida (usar HTTPS siempre)
- No es adecuado para anonimato completo si se inician sesiones con cuentas personales

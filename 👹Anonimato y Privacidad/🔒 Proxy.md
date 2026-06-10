# Proxy

Un proxy es un servidor intermediario entre el cliente e Internet. El tráfico sale con la IP del proxy en lugar de la IP real del usuario. A diferencia de una VPN, no cifra el tráfico ni actúa a nivel de sistema operativo, sino a nivel de aplicación.

---

## Proxy vs VPN

| | Proxy | VPN |
|---|---|---|
| Cifrado | No | Sí (AES-256 típicamente) |
| Alcance | Por aplicación | Todo el sistema |
| Velocidad | Generalmente más rápido | Algo más lento por el cifrado |
| Coste | Gratuitos disponibles | Mayoría de pago |
| Estabilidad | Variable | Más estable |

---

## Configurar proxy público en Kali Linux

1. Buscar un proxy público: [free-proxy-list.net](https://free-proxy-list.net/anonymous-proxy.html) o buscar `anonymous proxy list` en Google.
2. En Firefox: `Preferencias → Network Settings → Manual proxy configuration`
3. Introducir IP y puerto del proxy.

---

## Privoxy

Privoxy es un proxy web local de código abierto que filtra el tráfico antes de enviarlo a la red. Útil para bloquear anuncios, scripts y como paso intermedio para encadenar con TOR.

```bash
sudo apt-get install privoxy

# Editar configuración
sudo nano /etc/privoxy/config
# Al final del archivo, añadir la línea de forwarding (descomentar el ejemplo)
# y ajustar IP:PUERTO destino

sudo service privoxy start
```

Configurar el navegador con proxy manual en `127.0.0.1:8118`.

---

## ProxyChains

ProxyChains redirige el tráfico de cualquier herramienta TCP a través de una cadena de proxies (SOCKS4, SOCKS5, HTTP). Viene instalado por defecto en Kali.

### Configuración

```bash
sudo nano /etc/proxychains4.conf
```

Al final del archivo, añadir los proxies en el formato:

```
socks5  192.168.67.78  1080
socks5  10.10.10.1     1080
```

Ajustes recomendados en el mismo archivo:

- Descomentar `dynamic_chain` (usa los proxies disponibles, ignora los caídos)
- Comentar `strict_chain` y `random_chain`
- Comentar `proxy_dns` si da problemas de resolución

Solo puede haber **un modo activo** a la vez.

### Uso

Basta con anteponer `proxychains4` a cualquier comando:

```bash
proxychains4 nmap -sT -Pn -p 80,443 <objetivo>
proxychains4 curl http://<objetivo>
proxychains4 sqlmap -u "http://<objetivo>/page?id=1"
```

> ⚠️ Nmap con ProxyChains requiere usar `-sT` (TCP connect scan) ya que los scans SYN raw no funcionan a través de proxies.

---

## ProxySite

Servicio web para navegar anónimamente desde el navegador sin configuración adicional. Solo hay que introducir la URL en [proxysite.com](https://www.proxysite.com/es/).

Útil para comprobaciones rápidas, no para uso en pentesting real.

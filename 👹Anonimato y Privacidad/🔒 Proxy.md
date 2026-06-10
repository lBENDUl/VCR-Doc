---
tags:
  - Anonimato-Privacidad
  - Proxy
---

Un proxy es un servidor que actúa como intermediario entre el usuario y Internet. Al utilizar un proxy, el tráfico de Internet se enruta a través del servidor proxy en lugar de la dirección IP real.

![[Pasted image 20250327132534.png]]


## Proxy VS VPN

- Una VPN cifra y encapsula el tráfico, un Proxy no
    
- Una VPN suele trabajar a nivel sistema operativo y redirige todo el tráfico de red, el proxy funciona a nivel de aplicación.
    
- La conexión por VPN suele ser más estable que con un Proxy
    
- Los servicios VPN suelen ser de pago, los Proxy gratuitos
    
- Las conexiones por VPN pueden ser más lentas que con un proxy porque requieren cifrar y encapsular la información

## Configurar proxy publico en Kali Linux

Encontrar un proxy público, hay multitud de servicios de proxy gratis online.

[![Logo](https://free-proxy-list.net/favicon.ico)Anonymous Proxy List - Free Proxy List](https://free-proxy-list.net/anonymous-proxy.html)

- Buscar en Google: anonymous proxy list
    
- Abrir firefox > preferencias
    
- Network Settings
    
- Manual proxy settings
    
- Meter los proxys que queramos


## Privoxy


[![Logo](https://github.com/fluidicon.png)GitHub - unisx/privoxy: A non-caching web proxy. This repo is unofficial mirror of privoxy.GitHub](https://github.com/unisx/privoxy)

Privoxy es un software libre y de código abierto que actúa como un filtro de proxy web. Su objetivo es proteger la privacidad del usuario y mejorar la seguridad en línea al filtrar y bloquear contenido no deseado en la web.

Privoxy funciona como un proxy local que se ejecuta en el equipo del usuario y filtra el tráfico de Internet antes de enviarlo a la red. El software utiliza una serie de reglas y patrones de filtrado para bloquear anuncios, scripts maliciosos, cookies y otros tipos de contenido no deseado en la web.

Copiar

```
sudo apt-get install privoxy
sudo emacs /etc/privoxy/config
Forwarding # Copiar el example y pegar al final del todo descomentado 
Cambiar la IP y el puerto en formato IP:PUERTO
sudo service privoxy start
```

- En el navegador: configurar proxy manual
    
- 127.0.0.1 puerto 8118


## ProxySite

[![Logo](https://www.proxysite.com/android-chrome-192x192.png)ProxySite.com - Web proxy gratuita](https://www.proxysite.com/es/)

ProxySite es un servicio en línea que permite a los usuarios navegar por Internet de forma anónima y acceder a contenido restringido geográficamente. ProxySite actúa como un servidor proxy remoto que enmascara la dirección IP real del usuario y permite acceder a sitios web y servicios que de otra manera estarían bloqueados geográficamente.

Para utilizar ProxySite, los usuarios simplemente deben ingresar la URL del sitio web que quieren visitar en el campo de búsqueda del sitio web de ProxySite. El tráfico de Internet se enruta a través del servidor proxy remoto de ProxySite, lo que oculta la dirección IP real del usuario y enmascara su ubicación geográfica.


## ProxyChains

[![Logo](https://github.com/fluidicon.png)GitHub - haad/proxychains: proxychains - a tool that forces any TCP connection made by any given application to follow through proxy like TOR or any other SOCKS4, SOCKS5 or HTTP(S) proxy. Supported auth-types: "user/pass" for SOCKS4/5, "basic" for HTTP.GitHub](https://github.com/haad/proxychains)

ProxyChains es un software libre y de código abierto que permite a los usuarios redirigir el tráfico de Internet a través de múltiples servidores proxy. El software utiliza una serie de proxies para ocultar la dirección IP real del usuario y enmascarar su ubicación geográfica.

ProxyChains funciona como un proxy local que se ejecuta en el equipo del usuario y redirige el tráfico de Internet a través de una cadena de proxies remotos. El software también ofrece opciones de encriptación para proteger la privacidad y la seguridad del usuario en línea.

- Encadena varios múltiples proxys para que sea muy difícil saber la dirección de origen
    
- Viene por defecto en Kali
    

### Configuración


```
sudo emacs /etc/proxychains4.conf
# Al final añadir proxys
# Buscar listado de proxys socks5
# Copiar listado de proxys y pegar en emacs 
# Limpiar el texto con el formato de ProxyChains:
socks5  192.168.67.78  1080
```

![](https://afsh4ck.gitbook.io/~gitbook/image?url=https%3A%2F%2F2648005400-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FRolFIJKRJaxKzAUqQKJb%252Fuploads%252Fyjss0OXGENk6VADxnijy%252Fproxychains-conf.jpg%3Falt%3Dmedia%26token%3D9256da5d-79f0-4f5f-bc94-86fbc3e9e16e&width=768&dpr=4&quality=100&sign=fcc7d52c&sv=2)

### Activar función

Descomentar el inicio de una función para que actúe de ese modo

Solo puede haber una función descomentada

Vamos a usar el modo dynamic_chain

![](https://afsh4ck.gitbook.io/~gitbook/image?url=https%3A%2F%2F2648005400-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FRolFIJKRJaxKzAUqQKJb%252Fuploads%252Ftc8yyKcSYqmCtub5kntt%252Fdynamic-chain.jpg%3Falt%3Dmedia%26token%3Da65c1f04-f574-4621-a33a-fdd858f1e397&width=768&dpr=4&quality=100&sign=6f2f5c02&sv=2)

Comentar las líneas de remote dns subnet y proxy dns

### Usar ProxyChains

Solamente tenemos que poner proxychains4 delante del comando que queremos utilizar, por ejemplo un nmap

Copiar

```
proxychains4 nmap -v -sV hackthissite.org
```
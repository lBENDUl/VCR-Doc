---
tags:
  - Anonimato-Privacidad
  - TOR
---
## ¿Que es TOR?

TOR (The Onion Routing Protocol) es un proyecto desarrollado por el laboratorio de investigación naval de los EEUU para proteger sus comunicaciones.

El principio básico de Tor es el "enrutamiento de cebolla" que consiste en encapsular en varias capas de cifrado el tráfico de red para garantizar su seguridad y privacidad.

La red TOR esta formada por un conjunto de servidores operados por voluntarios que establecen túneles virtuales entre ellos utilizando este protocolo de enrutamiento.

![](https://afsh4ck.gitbook.io/~gitbook/image?url=https%3A%2F%2F2648005400-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FRolFIJKRJaxKzAUqQKJb%252Fuploads%252FArZIN5Hp1bKjhlLqthZ4%252FCaptura%2520de%2520pantalla%25202023-06-27%2520a%2520las%252016.50.40.png%3Falt%3Dmedia%26token%3Deab46738-a9c9-4dc7-b869-7e65f5e024db&width=768&dpr=4&quality=100&sign=38e9f628&sv=2)

Esquema de cifrado en la Red TOR

## TOR Hidden Services

- Los hidden services de TOR se corresponden con todas esas páginas web que se encuentran en la dark web y en muchas ocasiones realizan actividades ilegales
    
- Los hidden services son servidores que solo pueden ser accedidos a través de nodos de la red TOR que se conocen como entry points
    
- Una vez seleccionados los entry points, el servidor (hidden service) crea un hidden service descriptor que contiene una clave pública y la dirección IP de los entry points
    
- Esta información se almacena en una tabla hash distribuida y para acceder a ella se utiliza la onion address que se deriva de la clave pública del hidden service
    

## TOR Browser

El navegador TOR es un navegador web gratuito y de código abierto que permite a los usuarios navegar por Internet de forma anónima y segura. El navegador TOR utiliza la red TOR para enmascarar la dirección IP real del usuario y ocultar su ubicación geográfica. Además, el navegador ofrece opciones de seguridad y privacidad avanzadas, como la encriptación de extremo a extremo, la protección contra seguimiento en línea y la eliminación automática de cookies y datos de navegación.

#### Instalar en Kali Linux

```
sudo apt-get install tor torbrowser-launcher
```

## Brave Browser con TOR

[![Logo](https://brave.com/static-assets/images/cropped-brave_appicon_release-192x192.png)Navegador web seguro, rápido y privado con Adblocker | Brave BrowserBrave Browser](https://brave.com/es/)

Brave Browser con TOR es una versión del navegador web Brave que incluye la funcionalidad de navegación anónima de la red TOR. ofrece una mayor velocidad y rendimiento en comparación con otros navegadores web que utilizan la red TOR. La versión del navegador utiliza un enfoque de "circuito extendido" para enrutar el tráfico de Internet a través de la red TOR de manera más eficiente y rápida.

- Instalar siguiendo los pasos de su web
    
- Ventana privada con navegación por TOR
    

## ProxyChains con TOR

ProxyChains con TOR es una técnica que combina el uso de ProxyChains y la red TOR para proporcionar una mayor privacidad y seguridad en línea. Al utilizar ProxyChains con TOR, el tráfico de Internet se enruta a través de una cadena de servidores proxy remotos antes de pasar a través de la red TOR, lo que proporciona una mayor capa de anonimato y encriptación.

### Configuración


```
sudo service tor status
sudo service tor start
sudo emacs /etc/proxychains4.conf
Añadir al final:
socks5 127.0.0.1:9050
```

Usar el modo Strict Chain

Ahora al usar ProxyChains lo redirige por TOR

## SQLmap con TOR

Podemos lanzar SQLmap a través de la red TOR usando el flag `--proxy`

Copiar

```
sudo service tor status
sudo service tor start
sqlmap -u TARGET --proxy socks5://127.0.0.1:9050
```

## WPscan con TOR

De igual manera, podemos utilizar WPscan proxificado por TOR.

Copiar

```
wpscan -u TARGET --proxy socks5://127.0.0.1:9050
```

## TOR sobre VPN

Al utilizar TOR sobre VPN, el tráfico de Internet se enruta primero a través de la VPN antes de pasar a través de la red TOR. Esto proporciona una capa adicional de encriptación y anonimato al ocultar la dirección IP real del usuario y enmascarar su ubicación geográfica.

1. Conectarse a protonvpn
    
2. Hacer peticiones con TOR desde Brave o con ProxyChains configurado
    

Es lo más anónimo pero lo más lento

## IP Quality Score

[![Logo](https://www.ipqualityscore.com/templates/img/icons/fav/apple-touch-icon.png)Tor Detection Test | Tor IP Address Check | Tor IP Test](https://www.ipqualityscore.com/tor-ip-address-check)

IP Quality Score es un servicio en línea que permite a los usuarios verificar la calidad y la seguridad de una dirección IP. El servicio utiliza una serie de herramientas y técnicas de análisis para evaluar la reputación de una dirección IP y determinar si se ha utilizado para actividades malintencionadas en el pasado.

Podemos detectar si una IP se está redirigiendo por TOR

## Servicios que bloquean TOR

[![Logo](https://www.google.com/favicon.ico)List of services blocking Tor - Google SearchGoogle](https://www.google.com/search?q=List+of+services+blocking+Tor&rlz=1C5CHFA_enES991ES991&sxsrf=APwXEddTr9WO2vHMGNMDh_gjqbRPlQDtJg%3A1687878921849&ei=Cf2aZN6-M7uZkdUPvfWU0AM&ved=0ahUKEwie57383uP_AhW7TKQEHb06BToQ4dUDCA8&uact=5&oq=List+of+services+blocking+Tor&gs_lp=Egxnd3Mtd2l6LXNlcnAiHUxpc3Qgb2Ygc2VydmljZXMgYmxvY2tpbmcgVG9yMgoQABhHGNYEGLADMgoQABhHGNYEGLADMgoQABhHGNYEGLADMgoQABhHGNYEGLADMgoQABhHGNYEGLADMgoQABhHGNYEGLADMgoQABhHGNYEGLADMgoQABhHGNYEGLADSN0HUPEFWPEFcAF4AZABAJgBAKABAKoBALgBA8gBAPgBAeIDBBgAIEGIBgGQBgg&sclient=gws-wiz-serp)
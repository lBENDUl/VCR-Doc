#Linux #Fuzzing #Certificado 


Herramientas para inspeccionar el certificado SSL es ‘**Openssl**‘. OpenSSL es una biblioteca de software libre y de código abierto que se utiliza para implementar protocolos de seguridad en línea, como TLS (Transport Layer Security), SSL (Secure Sockets Layer). La biblioteca OpenSSL proporciona una implementación de estos protocolos para permitir que las aplicaciones se comuniquen de manera segura y encriptada a través de la red.

Uno de los comandos que vemos en esta clase haciendo uso de esta herramienta es el siguiente:

```
openssl s_client -connect ejemplo.com:443
```

Con este comando, podemos **inspeccionar** el **certificado SSL** de un servidor web. El comando se conecta al servidor en el puerto 443 y muestra información detallada sobre el certificado SSL, como la validez del certificado, la fecha de caducidad, el tipo de cifrado, etc.
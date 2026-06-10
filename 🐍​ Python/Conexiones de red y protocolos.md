#python

# Intro

Los protocolos **TCP** (Transmission Control Protocol) y **UDP** (User Datagram Protocol) son fundamentales en la comunicación de red, y la librería ‘**socket**‘ en Python proporciona las herramientas necesarias para interactuar con ellos. Aquí tienes una descripción detallada de ambos protocolos y el uso de ‘**socket**‘:

**Protocolo TCP**

- **Orientado a la Conexión**: TCP es un protocolo orientado a la conexión, lo que significa que establece una conexión segura y confiable entre el emisor y el receptor antes de la transmisión de datos.
- **Fiabilidad y Control de Flujo**: Garantiza la entrega de datos sin errores y en el mismo orden en que se enviaron. También gestiona el control de flujo y la corrección de errores.
- **Uso en Aplicaciones**: Es ampliamente utilizado en aplicaciones que requieren una entrega fiable de datos, como navegadores web, correo electrónico, y transferencia de archivos.

**Protocolo UDP**

- **No Orientado a la Conexión**: A diferencia de TCP, UDP es un protocolo no orientado a la conexión. Envía datagramas (paquetes de datos) sin establecer una conexión previa.
- **Rápido y Ligero**: UDP es más rápido y tiene menos sobrecarga que TCP, ya que no verifica la llegada de paquetes ni mantiene el orden de los mismos.
- **Uso en Aplicaciones**: Ideal para aplicaciones donde la velocidad es crucial y se pueden tolerar algunas pérdidas de datos, como juegos en línea, streaming de video y voz sobre IP (VoIP).

**Librería ‘socket’ en Python**

La librería ‘**socket**‘ en Python es una herramienta esencial para la programación de comunicaciones en red. Permite a los desarrolladores crear aplicaciones que pueden enviar y recibir datos a través de la red, ya sea en una red local o a través de Internet. Aquí tienes una visión general de la librería ‘**socket**‘:

- **Creación de sockets**: La librería ‘**socket**‘ proporciona clases y funciones para crear sockets, que son puntos finales de comunicación. Puedes crear sockets tanto para el protocolo TCP (Transmission Control Protocol) como para UDP (User Datagram Protocol).
- **Conexiones TCP**: Puedes utilizar ‘**socket**‘ para establecer conexiones TCP, que son conexiones confiables y orientadas a la conexión. Esto es útil para aplicaciones que requieren transferencia de datos confiable, como la transmisión de archivos o la comunicación cliente-servidor.
- **Comunicación UDP**: La librería ‘**socket**‘ también admite la comunicación mediante UDP, que es un protocolo de envío de mensajes sin conexión. Es adecuado para aplicaciones que necesitan una comunicación rápida y eficiente, como juegos en línea o aplicaciones de transmisión de video en tiempo real.
- **Funciones de envío y recepción**: Puedes utilizar métodos como ‘**send()**‘ y ‘**recv()**‘ para enviar y recibir datos a través de sockets. Esto te permite transferir información entre dispositivos de manera eficiente.
- **Gestión de conexiones**: La librería ‘**socket**‘ incluye métodos como ‘**bind()**‘ para asociar un socket a una dirección y puerto específicos, y ‘**listen()**‘ para poner un socket en modo de escucha, lo que le permite aceptar conexiones entrantes.
- **Conexiones cliente-servidor**: Con ‘**socket**‘, puedes crear aplicaciones cliente-servidor, donde un programa actúa como servidor esperando conexiones entrantes y otro actúa como cliente para conectarse al servidor.

En resumen, la librería ‘**socket**‘ en Python proporciona las herramientas necesarias para desarrollar aplicaciones de red, permitiendo la comunicación entre dispositivos a través de diferentes protocolos y ofreciendo control sobre la transferencia de datos. Es una parte fundamental de la programación de redes en Python y se utiliza en una amplia variedad de aplicaciones, desde servidores web hasta aplicaciones de chat y juegos en línea.


# Socket TCP

`socket.AF_INET` -> Define que estamos trabajando con IPv4
`socket.SOCK_STREAM` -> Conexiones TCP

**SERVIDOR**
```python
import socket

server_socker = socker.socket(socket.AF_INET, socket.SOCK_STREAM)
server_address = ('localhost', 1234)
server_socker.bind(server_address)

# Limitar el número de conexiones
server_socket.listen(1)

while True:
	client_socket, client_address = server_socket.accept()
	data = client_socket.recv(1024)
	print(f"Mensaje recibido del cliente: {data.decode()}")
	print(f"Información del cliente que se ha comunicado con nosotros: {client_address})
	client_socket.sendall(f"Un saludo crack\n".encode())
	client_socket.close()

```

**CLIENTE**
```python
import socket

client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server_address = ('localhost', 1234)
client_socket.connect(server_address)
try:
	message = b"Este es un mensaje de prueba que estoy enviando al servidor"
	client_socket.sendall(message)
	data = client_socket.rec(1024)
	print(f"\nEl servidor nos ha respondido con este mensaje: {data.decode()}")
finally:
	client_socket.close()
```


## Ejemplo mejorado

**SERVIDOR**
```python
import socket

def start_server():
	host = 'localhost'
	port = 1234
	with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
		s.bind((hots, port))
		print(f"Servidor en escucha {host}:{port}")
		s.listen(1)
		conn, addr = s.accept()
		with conn:
			print(f"Se ha conectado un nuevo cliente: {addr}")
			while True:
				data = conn.recv(1024)
				if not data:
					break
				conn.sendall(data)

start_server()
```

**CLIENTE**
```python
import socket

def start_client():
	host = 'localhost'
	port = 1234
	with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
		s.connect((host, port))
		s.sendall(b"Hola, servidor")
		data = s.recv(1024)

	print(f"\n Mensaje recibido del servidor: {data.decode()}")

start_client()
```

# Socket UDP

**SERVIDOR**
```python
def start_udp_server():
	host = 'localhost'
	port = 1234
	#UDP
	with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s:
		s.bind((host, port))
		print(f"\n Servidor UDP iniciado en {host}:{port}")
		while True:
		data, addr = s.recvfrom(1024)
		print(f"\nMensaje enviado por el cliente: {data.decode()}")
		print(f"Información del cliente que nos ha enviado el mensaje: {addr}")

start_udp_server()
```

**CLIENTE**
```python
import socket

def start_udp_client():
	host = 'localhost'
	port = 1234
	with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s:
		message = "Hola, se está tensando".encode("utf-8")
		s.sendto(message, (host, port))

start_udp_client()
```

# Configuración del socket y con hilos

La función ‘**setsockopt**‘ en la programación de redes juega un papel crucial al permitir a los desarrolladores ajustar y controlar varios aspectos de los sockets. Los sockets son fundamentales en la comunicación de red, proporcionando un punto final para el envío y recepción de datos en una red.

**Niveles en setsockopt**

Cuando utilizas ‘setsockopt’, puedes especificar diferentes niveles de configuración, que determinan el ámbito y la aplicación de las opciones que estableces:

- **Nivel de Socket (SOL_SOCKET)**: Este nivel afecta a las opciones aplicables a todos los tipos de sockets, independientemente del protocolo que estén utilizando. Las opciones en este nivel controlan aspectos generales del comportamiento del socket, como el tiempo de espera, el tamaño del buffer, y el reuso de direcciones y puertos.
- **Nivel de Protocolo**: Este nivel permite configurar opciones específicas para un protocolo de red en particular, como TCP o UDP. Por ejemplo, puedes ajustar opciones relacionadas con la calidad del servicio, la forma en que se manejan los paquetes de datos, o características específicas de un protocolo.

**  
socket.SOL_SOCKET**

‘**socket.SOL_SOCKET**‘ es una constante en muchos lenguajes de programación que se usa con ‘setsockopt’ para indicar que las opciones que se van a ajustar son a nivel de socket. Esto significa que las opciones aplicadas en este nivel afectarán a todas las operaciones de red realizadas a través del socket, sin importar el protocolo de transporte específico (como TCP o UDP) que esté utilizando.

**socket.SO_REUSEADDR**

‘**socket.SO_REUSEADDR**‘ es otra opción comúnmente utilizada en setsockopt. Esta opción es muy útil en el desarrollo de aplicaciones de red. Lo que hace es permitir que un socket se enlace a un puerto que todavía está siendo utilizado por un socket que ya no está activo. Esto es particularmente útil en situaciones donde un servidor se reinicia y sus sockets aún están en un estado de “espera de cierre” (**TIME_WAIT**), lo que podría impedir que el servidor se vuelva a enlazar al mismo puerto.

Al establecer ‘**SO_REUSEADDR**‘, el sistema operativo permite reutilizar el puerto inmediatamente, lo que facilita la reanudación rápida de los servicios del servidor.

En resumen, ‘**setsockopt**‘ con diferentes niveles y opciones, como ‘**SOL_SOCKET**‘ y ‘**SO_REUSEADDR**‘, proporciona una flexibilidad significativa en la configuración de sockets para una comunicación de red eficiente y efectiva.

**SERVIDOR**
```python
import socket
import threading

class ClientThread(threading.Thread):
	def __init__(self, client_sock, client_addr):
		super().__init__()
		self.client_sock = client_sock
		self:client_addr = client_addr
		print(f"Nuevo cliente conectado: {client:addr}")

	def run(self):
		message = ''
		while True:
			self.client_sock.recv(1024)
			message = data.decode()
			if message == 'bye':
				break
			print(f"\nMensaje enviado por el cliente: {message}")
			self.client_sock.send(data)
		print(f"\n Cliente {self.client_addr} desconectado")
		self.client_sock.close()


HOST = 'localhost'
PORT = 1234

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as server_socket:
	#TIME_WAIT
	server_socket.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
	server_socket.bind((HOST, PORT))
	print(f"En espera de conexiones entrantes...")

	while True:
		server_socket.listen()
		client_sock, client_addr = server_socket.accept()
		new_thread = ClientThread(client_sock, client_addr)
		new_thread.start() # run()
		
```

**CLIENTE**

```python
import socket

def start_client():
	host = 'localhost'
	port = 1234
	with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
		s.connect((host, port))
		while True:
			message = input("\n Introduce tu mensaje:")
			s.sendall(message.encode())
			if message == 'bye'
				break
				
			data = s.recv(1024)
			print(f"\nMensaje de respuesta del servidor: {data.decode()}")

	print(f"\n Mensaje recibido del servidor: {data.decode()}")

start_client()
```
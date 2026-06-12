# Python — Conexiones de red y sockets


## Conceptos clave

| | TCP | UDP |
|---|---|---|
| **Conexión** | Orientado a conexión (handshake) | Sin conexión |
| **Fiabilidad** | Garantiza entrega y orden | Sin garantías |
| **Velocidad** | Más lento | Más rápido |
| **Uso típico** | Web, SSH, FTP, bases de datos | DNS, streaming, juegos, VoIP |
| **Socket** | `SOCK_STREAM` | `SOCK_DGRAM` |

---

## Constantes importantes

```python
import socket

socket.AF_INET      # IPv4
socket.AF_INET6     # IPv6
socket.SOCK_STREAM  # TCP
socket.SOCK_DGRAM   # UDP
```

---

## TCP

### Servidor TCP básico

```python
import socket

server = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
server.bind(("localhost", 1234))
server.listen(5)  # cola de hasta 5 conexiones pendientes

print("Servidor escuchando en puerto 1234...")

while True:
    conn, addr = server.accept()  # bloquea hasta recibir conexión
    print(f"Cliente conectado: {addr}")

    data = conn.recv(1024)        # recibir hasta 1024 bytes
    print(f"Recibido: {data.decode()}")

    conn.sendall("Mensaje recibido\n".encode())
    conn.close()
```

### Cliente TCP básico

```python
import socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.connect(("localhost", 1234))
    s.sendall("Hola servidor".encode())

    data = s.recv(1024)
    print(f"Respuesta: {data.decode()}")
```

---

### Servidor TCP mejorado (con contexto)

```python
import socket

def iniciar_servidor(host="localhost", port=1234):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        s.bind((host, port))
        s.listen(1)
        print(f"Servidor en {host}:{port}")

        conn, addr = s.accept()
        with conn:
            print(f"Conectado: {addr}")
            while True:
                data = conn.recv(1024)
                if not data:
                    break
                conn.sendall(data)  # eco: devuelve lo mismo que recibe

iniciar_servidor()
```

> `SO_REUSEADDR` permite reutilizar el puerto inmediatamente después de cerrar el servidor, evitando el error `Address already in use` por el estado `TIME_WAIT`.

### Cliente TCP mejorado

```python
import socket

def iniciar_cliente(host="localhost", port=1234):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((host, port))
        s.sendall(b"Hola, servidor")
        data = s.recv(1024)
        print(f"Respuesta: {data.decode()}")

iniciar_cliente()
```

---

### Servidor TCP con múltiples clientes (hilos)

```python
import socket
import threading

class ClienteThread(threading.Thread):
    def __init__(self, conn, addr):
        super().__init__()
        self.conn = conn
        self.addr = addr

    def run(self):
        print(f"[{self.addr}] Conectado")
        with self.conn:
            while True:
                data = self.conn.recv(1024)
                if not data or data.decode().strip() == "bye":
                    break
                print(f"[{self.addr}] → {data.decode()}")
                self.conn.sendall(data)  # eco
        print(f"[{self.addr}] Desconectado")


def iniciar_servidor(host="localhost", port=1234):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
        s.bind((host, port))
        print(f"Servidor multicliente en {host}:{port}")

        while True:
            s.listen()
            conn, addr = s.accept()
            hilo = ClienteThread(conn, addr)
            hilo.start()

iniciar_servidor()
```

### Cliente interactivo

```python
import socket

def iniciar_cliente(host="localhost", port=1234):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((host, port))
        while True:
            mensaje = input("Mensaje: ").strip()
            s.sendall(mensaje.encode())
            if mensaje == "bye":
                break
            data = s.recv(1024)
            print(f"Servidor: {data.decode()}")

iniciar_cliente()
```

---

## UDP

### Servidor UDP

```python
import socket

def iniciar_servidor_udp(host="localhost", port=1234):
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s:
        s.bind((host, port))
        print(f"Servidor UDP en {host}:{port}")

        while True:
            data, addr = s.recvfrom(1024)  # recibe datos y dirección del emisor
            print(f"[{addr}] → {data.decode()}")
            s.sendto("Recibido".encode(), addr)  # responder al emisor

iniciar_servidor_udp()
```

### Cliente UDP

```python
import socket

def iniciar_cliente_udp(host="localhost", port=1234):
    with socket.socket(socket.AF_INET, socket.SOCK_DGRAM) as s:
        mensaje = "Hola UDP".encode()
        s.sendto(mensaje, (host, port))

        data, addr = s.recvfrom(1024)
        print(f"Respuesta: {data.decode()}")

iniciar_cliente_udp()
```

---

## `setsockopt` — Configuración del socket

```python
# Nivel de configuración
socket.SOL_SOCKET   # opciones generales del socket
socket.IPPROTO_TCP  # opciones específicas de TCP

# Opciones comunes
socket.SO_REUSEADDR  # reutilizar puerto en TIME_WAIT
socket.SO_KEEPALIVE  # enviar keepalives para detectar conexiones muertas
socket.SO_RCVBUF     # tamaño del buffer de recepción
socket.SO_SNDBUF     # tamaño del buffer de envío

# Aplicar opción
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
s.setsockopt(socket.SOL_SOCKET, socket.SO_KEEPALIVE, 1)

# Timeout de operaciones (recv/send no bloquean más de N segundos)
s.settimeout(5.0)  # lanza socket.timeout si pasan 5s sin datos
```

---

## Timeout y manejo de errores

```python
import socket

with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.settimeout(5)  # timeout en segundos

    try:
        s.connect(("destino.com", 80))
        s.sendall(b"GET / HTTP/1.0\r\nHost: destino.com\r\n\r\n")
        respuesta = s.recv(4096)
        print(respuesta.decode())

    except socket.timeout:
        print("Tiempo de espera agotado")
    except ConnectionRefusedError:
        print("Conexión rechazada")
    except socket.gaierror as e:
        print(f"Error DNS/nombre de host: {e}")
    except socket.error as e:
        print(f"Error de socket: {e}")
```

---

## Utilidades

### Obtener IP de un hostname

```python
import socket

ip = socket.gethostbyname("python.org")
print(ip)  # 151.101.x.x

hostname = socket.gethostname()        # nombre del equipo local
ip_local = socket.gethostbyname(hostname)
```

### Escanear puertos (port scanner básico)

```python
import socket

def escanear_puerto(host, port, timeout=1):
    try:
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
            s.settimeout(timeout)
            resultado = s.connect_ex((host, port))  # 0 = abierto
            return resultado == 0
    except socket.error:
        return False

host = "127.0.0.1"
for port in range(1, 1025):
    if escanear_puerto(host, port):
        print(f"Puerto {port} abierto")
```

### Enviar petición HTTP raw

```python
import socket

def http_get(host, path="/"):
    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.connect((host, 80))
        peticion = f"GET {path} HTTP/1.0\r\nHost: {host}\r\n\r\n"
        s.sendall(peticion.encode())

        respuesta = b""
        while True:
            chunk = s.recv(4096)
            if not chunk:
                break
            respuesta += chunk

        return respuesta.decode(errors="replace")

print(http_get("example.com"))
```

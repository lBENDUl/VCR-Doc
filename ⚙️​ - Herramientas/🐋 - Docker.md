# Docker

Docker permite crear, distribuir y ejecutar aplicaciones en contenedores aislados. En ciberseguridad se usa habitualmente para desplegar entornos de laboratorio vulnerables de forma rápida y limpia.

---

## Instalación

```bash
sudo apt install docker.io
sudo service docker start
```

Verificar que funciona:

```bash
docker ps
```

---

## Dockerfile — estructura básica

Un `Dockerfile` define cómo se construye una imagen. Las instrucciones más comunes:

| Instrucción | Descripción |
|---|---|
| `FROM` | Imagen base |
| `RUN` | Ejecuta comandos durante la construcción |
| `COPY` | Copia archivos del host al contenedor |
| `CMD` | Comando por defecto al arrancar el contenedor |
| `EXPOSE` | Documenta los puertos que usa el contenedor |

Ejemplo mínimo:

```dockerfile
FROM ubuntu:latest
RUN apt update && apt install -y curl
CMD ["/bin/bash"]
```

---

## Gestión de imágenes

```bash
# Construir imagen desde el directorio actual
docker build -t mi_imagen:v1 .

# Descargar imagen desde Docker Hub
docker pull ubuntu:latest

# Listar imágenes locales
docker images

# Eliminar imagen
docker rmi <id_imagen>

# Eliminar todas las imágenes
docker rmi $(docker images -q)
```

---

## Gestión de contenedores

```bash
# Crear y arrancar contenedor interactivo
docker run -dit --name mi_contenedor mi_imagen

# Listar contenedores en ejecución
docker ps

# Listar todos (incluidos detenidos)
docker ps -a

# Ejecutar comando en contenedor activo
docker exec -it <id_contenedor> bash

# Eliminar contenedor
docker rm <id_contenedor>

# Eliminar todos los contenedores (incluidos detenidos)
docker rm $(docker ps -a -q) --force
```

Opciones de `docker run` más usadas:

| Flag | Descripción |
|---|---|
| `-d` | Segundo plano (detached) |
| `-i` | Entrada interactiva |
| `-t` | Pseudoterminal (TTY) |
| `--name` | Nombre del contenedor |

---

## Port forwarding

Redirige tráfico del host al contenedor con `-p HOST:CONTENEDOR`:

```bash
# TCP: puerto 80 del host → puerto 8080 del contenedor
docker run -p 80:8080 mi_imagen

# UDP
docker run -p 53:53/udp mi_imagen
```

---

## Monturas (volúmenes)

Comparte directorios entre host y contenedor con `-v HOST:CONTENEDOR`:

```bash
# Montura de lectura y escritura
docker run -v /home/usuario/datos:/datos mi_imagen

# Solo lectura
docker run -v /home/usuario/datos:/datos:ro mi_imagen
```

Los cambios en el directorio del host se reflejan en el contenedor y viceversa.

---

## Docker Compose

Docker Compose orquesta múltiples contenedores definidos en un archivo `docker-compose.yml`. Útil para desplegar entornos con varios servicios interconectados.

```bash
# Levantar todos los servicios en segundo plano
docker-compose up -d

# Parar y eliminar
docker-compose down
```

### Laboratorios vulnerables con Vulhub

[Vulhub](https://github.com/vulhub/vulhub) es un repositorio con entornos Docker preconfigurados para practicar vulnerabilidades reales.

```bash
git clone https://github.com/vulhub/vulhub
cd vulhub/<servicio>/<CVE>
docker-compose up -d
```

**Problemas comunes:**

Si el contenedor no arranca (especialmente Elasticsearch):
```bash
sudo sysctl -w vm.max_map_count=262144
docker-compose up -d
```

Si `apt` falla dentro de un contenedor Debian antiguo, reemplazar `/etc/apt/sources.list` con:
```
deb http://archive.debian.org/debian/ jessie contrib main non-free
```
Y luego `apt update`.

---

## Referencias

- [Vulhub](https://github.com/vulhub/vulhub) — Entornos vulnerables listos para Docker
- [NodeJS Reverse Shell](https://github.com/appsecco/vulnerable-apps/tree/master/node-reverse-shell)
- [ImageTragick (ImageMagick)](https://github.com/vulhub/vulhub/tree/master/imagemagick/imagetragick)

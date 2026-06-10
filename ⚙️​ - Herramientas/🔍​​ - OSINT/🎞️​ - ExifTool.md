# ExifTool

ExifTool extrae metadatos de imágenes, documentos y otros archivos. En reconocimiento es útil para analizar archivos públicos subidos a una web: pueden revelar rutas internas, nombres de usuario, versiones de software o coordenadas GPS.

---

## Instalación

```bash
sudo apt install libimage-exiftool-perl
```

---

## Uso básico

```bash
# Ver todos los metadatos de un archivo
exiftool imagen.jpg

# Ver solo campos específicos
exiftool -Author -CreateDate documento.pdf

# Procesar todos los archivos de un directorio
exiftool /ruta/directorio/

# Exportar a CSV
exiftool -csv *.jpg > metadatos.csv
```

---

## Campos relevantes en pentesting

| Campo | Qué puede revelar |
|---|---|
| `Author` / `Creator` | Nombres de usuario del sistema |
| `Software` | Versión de la aplicación que generó el archivo |
| `GPS Latitude/Longitude` | Ubicación física |
| `Camera Model` | Información del dispositivo |
| `Create Date` | Horarios de actividad |
| `Windows XP Comments` | Rutas internas del sistema |

---

## Eliminar metadatos

```bash
# Eliminar todos los metadatos
exiftool -all= imagen.jpg

# En múltiples archivos
exiftool -all= *.jpg
```

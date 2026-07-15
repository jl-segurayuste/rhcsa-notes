# Gestión de contenedores

## Halle imágenes en contenedores y extráigalas desde un registro remoto

Gestiona y extrae imágenes de contenedores desde un registro remoto con Podman y Skopeo.

### 1. Buscar Imágenes en un Registro Remoto con Skopeo

```bash
skopeo list-tags docker://nombre_registro/nombre_imagen
```

Esto lista las etiquetas disponibles para la imagen en el registro remoto.

### 2. Extraer una Imagen desde un Registro Remoto con Skopeo

```bash
skopeo copy docker://nombre_registro/nombre_imagen:etiqueta_contenedor dir:/ruta_local
```

Esto descarga y extrae la imagen del registro remoto a la ruta local especificada.

### 3. Utilizar Podman para Gestionar Imágenes y Contenedores

```bash
# Descargar una imagen desde el registro remoto
podman pull nombre_registro/nombre_imagen:etiqueta_contenedor

# Listar imágenes locales
podman images

# Correr un contenedor
podman run -it nombre_imagen:etiqueta_contenedor /bin/bash
```

### 4. Utilizar `podman save` y `podman load` para Exportar e Importar Imágenes

```bash
# Exportar una imagen a un archivo tar
podman save -o nombre_imagen.tar nombre_imagen:etiqueta_contenedor

# Importar una imagen desde un archivo tar
podman load -i nombre_imagen.tar
```

### Notas Adicionales

- Necesitas permisos y acceso al registro remoto para descargar las imágenes.
- Las políticas de SELinux pueden afectar las operaciones de contenedores; revisa y ajústalas si es necesario.

## Examine las imágenes en contenedores

Examina las imágenes disponibles en tu sistema con Podman y Skopeo.

### Usando Podman

1. **Listar todas las imágenes locales:**

```bash
podman images
```

Esto mostrará una lista de todas las imágenes locales almacenadas en tu sistema.

2. **Mostrar detalles de una imagen específica:**

```bash
podman inspect nombre_imagen
```

Proporciona información detallada sobre una imagen específica, incluyendo metadatos y configuraciones.

3. **Explorar el sistema de archivos de una imagen:**

```bash
podman run -it --rm nombre_imagen /bin/bash
```

Esto te llevará al sistema de archivos de la imagen, permitiéndote explorar su contenido.

### Usando Skopeo

1. **Listar etiquetas de una imagen en un registro remoto:**

```bash
skopeo list-tags docker://nombre_registro/nombre_imagen
```

Muestra las etiquetas disponibles para una imagen en un registro remoto.

2. **Inspeccionar metadatos de una imagen en un registro remoto:**

```bash
skopeo inspect docker://nombre_registro/nombre_imagen:etiqueta_contenedor
```

Proporciona información detallada sobre una imagen en un registro remoto.

### Notas Adicionales

- Las políticas de SELinux pueden afectar las operaciones relacionadas con imágenes y contenedores.
- Al explorar el sistema de archivos de una imagen con `podman run`, puedes examinar su contenido y comprender mejor su estructura interna.

## Gestione los contenedores con comandos, como Podman y Skopeo

Gestionar contenedores implica operaciones como la creación, ejecución, supervisión y eliminación de contenedores. A continuación se detallan algunos comandos básicos utilizando Podman y Skopeo.

### Podman

1. **Crear un contenedor:**

```bash
podman run -it --name mi_contenedor nombre_imagen:etiqueta_contenedor
```

2. **Listar contenedores en ejecución:**

```bash
podman ps
```

3. **Listar todos los contenedores (incluso detenidos):**

```bash
podman ps -a
```

4. **Detener un contenedor en ejecución:**

```bash
podman stop mi_contenedor
```

5. **Reanudar un contenedor detenido:**

```bash
podman start mi_contenedor
```

6. **Ver log de un contenedor:**

```bash
podman logs mi_contenedor
```

7. **Ejecutar un comando en un contenedor en ejecución:**

```bash
podman exec -it mi_contenedor comando
```

8. **Eliminar un contenedor:**

```bash
podman rm mi_contenedor
```

### Skopeo

1. **Copiar una imagen entre registros:**

```bash
skopeo copy docker://origen/nombre_imagen:etiqueta_contenedor docker://destino/nombre_imagen:etiqueta_contenedor
```

2. **Sincronizar imágenes entre registros:**

```bash
skopeo sync docker://origen/ docker://destino/
```

3. **Eliminar una imagen en un registro remoto:**

```bash
skopeo delete docker://nombre_registro/nombre_imagen:etiqueta_contenedor
```

4. **Listar etiquetas de una imagen en un registro remoto:**

```bash
skopeo list-tags docker://nombre_registro/nombre_imagen
```

## Diseñe un contenedor a partir de un archivo de contenedor

Diseñar un contenedor implica crear un archivo de definición del contenedor, típicamente conocido como Dockerfile (o Containerfile en el ecosistema Podman). Este archivo especifica cómo se construirá la imagen del contenedor y qué configuraciones y comandos deben ejecutarse dentro del contenedor.

### Ejemplo de Dockerfile

```dockerfile
# Usa una imagen base (puedes elegir según tus necesidades)
FROM alpine:latest

# Etiqueta del creador
LABEL maintainer="TuNombre <tucorreo@ejemplo.com>"

# Instala paquetes o aplicaciones necesarias
RUN apk --update add \
    paquete1 \
    paquete2 \
    && rm -rf /var/cache/apk/*

# Configura variables de entorno
ENV VARIABLE_ENTORNO="valor"

# Copia archivos locales al sistema de archivos del contenedor
COPY archivos_locales /ruta_en_el_contenedor

# Establece el directorio de trabajo dentro del contenedor
WORKDIR /ruta_en_el_contenedor

# Expone un puerto para acceso externo
EXPOSE 8080

# Ejecuta un comando al iniciar el contenedor
CMD ["comando", "argumento"]

# Puedes agregar más comandos o configuraciones según tus necesidades

# Ejemplo de cómo construir y ejecutar la imagen:
#   docker build -t nombre_imagen:etiqueta_contenedor .
#   docker run -it nombre_imagen:etiqueta_contenedor
```

### Pasos para Utilizar el Dockerfile

1. **Crea un archivo Dockerfile:** crea un archivo llamado `Dockerfile` en tu directorio de trabajo y agrega el contenido del ejemplo.

2. **Construye la imagen:** ejecuta el siguiente comando para construir la imagen del contenedor:

```bash
docker build -t nombre_imagen:etiqueta_contenedor .
```

3. **Ejecuta el contenedor:** después de construir la imagen, puedes ejecutar un contenedor a partir de ella:

```bash
docker run -it nombre_imagen:etiqueta_contenedor
```

Dockerfile utiliza una sintaxis específica, así que ten cuidado con la indentación y el formato.

> **Nota:** Podman también es una alternativa a Docker y puede utilizarse de manera similar. Los comandos pueden variar ligeramente, pero el enfoque general es el mismo.

## Realice una gestión básica de los contenedores, como ejecutar, iniciar, detener y registrar aquellos que se encuentran en funcionamiento

Ejecuta, inicia, detén y revisa el registro de contenedores con estos comandos básicos de Docker.

### Operaciones Básicas con Docker

1. **Ejecutar un contenedor:**

```bash
docker run -it --name mi_contenedor nombre_imagen:etiqueta_contenedor
```

Este comando ejecuta un contenedor en modo interactivo y le asigna un nombre.

2. **Listar contenedores en ejecución:**

```bash
docker ps
```

Muestra una lista de contenedores en ejecución.

3. **Detener un contenedor en ejecución:**

```bash
docker stop mi_contenedor
```

Detiene un contenedor que está en ejecución.

4. **Iniciar un contenedor detenido:**

```bash
docker start mi_contenedor
```

Inicia un contenedor que ha sido detenido previamente.

5. **Ver el registro de un contenedor:**

```bash
docker logs mi_contenedor
```

Muestra el registro de salida del contenedor.

6. **Eliminar un contenedor detenido:**

```bash
docker rm mi_contenedor
```

Elimina un contenedor que ha sido detenido.

### Notas Adicionales

- Puedes utilizar la opción `-d` con `docker run` para ejecutar el contenedor en segundo plano.
- Para ejecutar un comando específico dentro de un contenedor en ejecución, utiliza:

```bash
docker exec -it mi_contenedor comando
```

- Al listar contenedores con `docker ps`, la opción `-a` muestra todos los contenedores, incluidos los detenidos.

Estos comandos son para Docker, pero con Podman como alternativa suelen ser muy similares.

## Ejecute un servicio dentro de un contenedor

Para ejecutar un servicio dentro de un contenedor, utiliza el comando que inicia el servicio deseado en el contenedor.

### Ejemplo de Ejecución de un Servicio en Docker

Supongamos que deseas ejecutar un servidor web Nginx dentro de un contenedor:

1. **Descargar la imagen de Nginx:**

```bash
docker pull nginx
```

2. **Ejecutar un contenedor con el servicio Nginx:**

```bash
docker run -d --name mi_servicio_nginx -p 8080:80 nginx
```

- `-d`: ejecuta el contenedor en segundo plano (modo daemon).
- `--name mi_servicio_nginx`: asigna un nombre al contenedor.
- `-p 8080:80`: mapea el puerto 8080 del host al puerto 80 del contenedor.

3. **Verificar que el servicio está en ejecución:**

```bash
docker ps
```

Asegúrate de que el contenedor esté en ejecución.

4. **Acceder al servicio Nginx:** abre un navegador web y visita `http://localhost:8080`. Verás la página de inicio de Nginx.

### Notas Adicionales

- Puedes utilizar la opción `-v` para montar volúmenes y compartir datos entre el host y el contenedor.
- Para detener el servicio, utiliza:

```bash
docker stop mi_servicio_nginx
```

- Para eliminar el contenedor después de detener el servicio, utiliza:

```bash
docker rm mi_servicio_nginx
```

Este ejemplo utiliza Nginx, pero el procedimiento es similar para otros servicios.

## Configure un contenedor para que inicie automáticamente como un servicio systemd

Para que un contenedor se inicie automáticamente como un servicio systemd, crea un archivo de unidad systemd.

### Paso 1: Crear un Archivo de Unidad systemd (`mi_contenedor.service`)

Crea un archivo de unidad systemd, por ejemplo, `mi_contenedor.service`. Puedes usar un editor de texto como `nano` o `vim` para crear el archivo.

```ini
# mi_contenedor.service
[Unit]
Description=Servicio Mi Contenedor

[Service]
Restart=always
ExecStart=/usr/bin/docker start -a mi_contenedor
ExecStop=/usr/bin/docker stop -t 2 mi_contenedor

[Install]
WantedBy=default.target
```

Este archivo de unidad especifica que el contenedor denominado `mi_contenedor` debería iniciarse automáticamente como un servicio systemd. Ajusta los comandos `ExecStart` y `ExecStop` según tus necesidades.

### Paso 2: Colocar el Archivo de Unidad en el Directorio de Systemd

Coloca el archivo `mi_contenedor.service` en el directorio `/etc/systemd/system/`.

```bash
sudo cp mi_contenedor.service /etc/systemd/system/
```

### Paso 3: Recargar la Configuración de systemd

Recarga la configuración de systemd para que reconozca el nuevo archivo de unidad.

```bash
sudo systemctl daemon-reload
```

### Paso 4: Habilitar y Iniciar el Servicio

Habilita el servicio para que se inicie automáticamente al arrancar y luego inicia el servicio.

```bash
sudo systemctl enable mi_contenedor.service
sudo systemctl start mi_contenedor.service
```

### Notas Adicionales

- El ejemplo utiliza `Restart=always` para reiniciar automáticamente el servicio si se detiene inesperadamente.
- Puedes revisar el registro del servicio con `journalctl -u mi_contenedor.service`.
- Para detener y deshabilitar el servicio, utiliza:

```bash
sudo systemctl stop mi_contenedor.service
sudo systemctl disable mi_contenedor.service
```

## Asigne un almacenamiento permanente a un contenedor

Cuando se trabaja con contenedores, el almacenamiento puede ser efímero o persistente. El almacenamiento efímero se pierde cuando el contenedor se detiene o se elimina, mientras que el almacenamiento persistente se mantiene incluso después de que el contenedor se detiene o se elimina.

A continuación se presenta una forma de asignar almacenamiento persistente a un contenedor usando Docker como ejemplo, mediante volúmenes:

1. **Crea un volumen:**

```bash
docker volume create mi_volumen
```

Esto creará un volumen llamado `mi_volumen`.

2. **Ejecuta un contenedor asociado con el volumen:**

```bash
docker run -d --name mi_contenedor -v mi_volumen:/ruta/en/el/contenedor mi_imagen
```

Esto ejecutará un contenedor llamado `mi_contenedor` asociado con el volumen `mi_volumen`. El directorio `/ruta/en/el/contenedor` dentro del contenedor estará vinculado al volumen.

3. **Almacena datos en el volumen:**

Puedes copiar datos al volumen o crear nuevos archivos dentro del contenedor, y estos se almacenarán en el volumen asociado.

```bash
docker exec -it mi_contenedor bash
# Dentro del contenedor
echo "Datos importantes" > /ruta/en/el/contenedor/datos.txt
exit
```

4. **Detén y reinicia el contenedor:**

Puedes detener y reiniciar el contenedor, pero los datos en el volumen persistirán.

```bash
docker stop mi_contenedor
docker start mi_contenedor
```

Incluso después de detener y reiniciar el contenedor, los datos en el volumen `mi_volumen` seguirán estando disponibles.

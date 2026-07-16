# Ejercicio de LVM con PE

Para realizar este ejercicio, que consiste en crear un grupo de volúmenes (Volume Group) de 2 GiB con extents físicos de 8 MiB, y luego crear un volumen lógico de 500 MiB llamado `mydata` dentro de este grupo y montarlo persistentemente en el directorio `/mydata`, sigue estos pasos detallados:

> **Nota:** primero se crea una partición de 2G para que luego el VG se cree con el tamaño que solicita para el VG.
>
> [Imagen no migrada: captura de pantalla mostrando la partición de 2G creada, previa a la creación del VG]

## 1. Crear un Grupo de Volúmenes (Volume Group)

### Comando

```bash
sudo vgcreate -s 8M myvg /dev/sdx
```

### Explicación de los parámetros

- `vgcreate`: comando para crear un nuevo grupo de volúmenes.
- `-s 8M`: especifica el tamaño de los extents físicos (PE size) en 8 MiB. Los extents son las unidades mínimas de asignación en LVM.
- `myvg`: nombre que le damos al nuevo grupo de volúmenes.
- `/dev/sdx`: dispositivo físico que será parte del grupo de volúmenes. Reemplaza `/dev/sdx` con el dispositivo real que deseas usar, por ejemplo, `/dev/sdb`.

## 2. Crear un Volumen Lógico (Logical Volume)

### Comando

```bash
sudo lvcreate -L 500M -n mydata myvg
```

### Explicación de los parámetros

- `lvcreate`: comando para crear un nuevo volumen lógico.
- `-L 500M`: especifica el tamaño del volumen lógico en 500 MiB.
- `-n mydata`: nombre del nuevo volumen lógico (`mydata` en este caso).
- `myvg`: nombre del grupo de volúmenes donde se creará el volumen lógico (`myvg` en este caso).

## 3. Formatear el Volumen Lógico

### Comando

```bash
sudo mkfs.ext4 /dev/myvg/mydata
```

### Explicación

- `mkfs.ext4`: formatea el volumen lógico como un sistema de archivos ext4.
- `/dev/myvg/mydata`: ruta al dispositivo del volumen lógico creado anteriormente.

## 4. Crear el Punto de Montaje

### Comando

```bash
sudo mkdir /mydata
```

### Explicación

- `mkdir /mydata`: crea el directorio `/mydata` donde se montará el volumen lógico.

## 5. Montar el Volumen Lógico Persistentemente

### Modificar el archivo `/etc/fstab`

Para montar el volumen lógico `/mydata` automáticamente al iniciar el sistema, debes editar el archivo `/etc/fstab` y agregar una entrada para este volumen lógico.

1. Abrir `/etc/fstab` en un editor de texto (por ejemplo, `sudo nano /etc/fstab`).
2. Agregar la siguiente línea al final del archivo:

   ```plain text
   /dev/myvg/mydata  /mydata  ext4  defaults  0  0
   ```

   - `/dev/myvg/mydata`: dispositivo del volumen lógico.
   - `/mydata`: punto de montaje.
   - `ext4`: tipo de sistema de archivos (puedes ajustar esto según el tipo de sistema de archivos que hayas formateado).
   - `defaults`: opciones de montaje predeterminadas.
   - `0 0`: opciones de copia de seguridad.

## 6. Montar el Volumen Lógico sin Reiniciar

### Comando

```bash
sudo mount -a
```

### Explicación

- `mount -a`: monta todos los sistemas de archivos definidos en `/etc/fstab`, incluyendo `/mydata`.

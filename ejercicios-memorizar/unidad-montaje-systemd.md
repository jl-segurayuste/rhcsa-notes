# Unidad de montaje para systemd

Cuando creas un archivo de unidad de montaje para **systemd**, varios elementos y secciones son necesarios para definir correctamente cómo se debe montar un sistema de archivos. Los archivos de unidad de montaje tienen la extensión **`.mount`** y se ubican en el directorio **`/etc/systemd/system/`** o en uno de los directorios de unidades de systemd.

### Elementos requeridos en un archivo de unidad de montaje (`.mount`)

1. **Nombre del archivo de unidad**:
   - El nombre del archivo de unidad debe corresponder al punto de montaje, pero con las barras (`/`) reemplazadas por guiones (`-`). Por ejemplo, si deseas montar en **`/mnt/data`**, el archivo de unidad se debe llamar **`mnt-data.mount`**.
2. **Secciones del archivo**:
   Los archivos de unidad **`.mount`** contienen varias secciones que definen las propiedades del montaje.

### Estructura básica de un archivo de unidad de montaje

### 1. `[Unit]`: Descripción básica del montaje

En esta sección se define una descripción del propósito de la unidad y las dependencias necesarias.

```bash
[Unit]
Description=Mount point for /mnt/data
Documentation=man:systemd.mount(5)
After=network.target
```

- **`Description`**: Una descripción de la unidad.
- **`After`**: Define cuándo debería activarse esta unidad. En este caso, se activa después de la red.

### 2. `[Mount]`: Configuración del montaje

Aquí se configuran los detalles sobre el sistema de archivos que se va a montar y las opciones de montaje.

```bash
[Mount]
What=/dev/sdb1
Where=/mnt/data
Type=ext4
Options=defaults
```

- **`What`**: Especifica el dispositivo o sistema de archivos a montar (por ejemplo, `/dev/sdb1`).
- **`Where`**: Define el punto de montaje (por ejemplo, `/mnt/data`).
- **`Type`**: El tipo de sistema de archivos (por ejemplo, `ext4`).
- **`Options`**: Opciones de montaje, como `defaults` o configuraciones específicas (por ejemplo, `ro` para montarlo como de solo lectura).

### 3. `[Install]`: Control de habilitación/deshabilitación

Esta sección controla cómo se instala la unidad y si debe activarse automáticamente.

```bash
[Install]
WantedBy=multi-user.target
```

- **`WantedBy`**: Define cuándo debe iniciarse este montaje. `multi-user.target` asegura que el sistema de archivos se monte en el entorno multiusuario normal.

### Pasos para crear y activar una unidad de montaje

1. **Crear el archivo de unidad**: Por ejemplo, si deseas montar `/mnt/data`, creas el archivo `mnt-data.mount` en `/etc/systemd/system/`.

   ```bash
   sudo nano /etc/systemd/system/mnt-data.mount
   ```

2. **Escribir la configuración en el archivo de unidad** (basado en la estructura anterior).

3. **Recargar las unidades de `systemd`** para que reconozca la nueva unidad:

   ```bash
   sudo systemctl daemon-reload
   ```

4. **Iniciar y habilitar la unidad**:
   - Para montar el sistema de archivos inmediatamente:

     ```bash
     sudo systemctl start mnt-data.mount
     ```

   - Para habilitar el montaje en cada arranque:

     ```bash
     sudo systemctl enable mnt-data.mount
     ```

### Comprobación del estado de la unidad

Puedes verificar el estado de la unidad de montaje con:

```bash
systemctl status mnt-data.mount
```

Esto te indicará si el sistema de archivos ha sido montado correctamente o si hubo algún error.

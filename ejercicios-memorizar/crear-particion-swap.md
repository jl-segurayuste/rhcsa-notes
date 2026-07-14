# Crear partición Swap

> **SIMULATION.** Create a 2G swap partition which take effect automatically at boot-start, and it should not affect the original swap partition.

Para crear una partición de swap de 2G que se active automáticamente al iniciar el sistema, sin afectar la partición de swap original, sigue estos pasos detallados:

## Paso 1: Crear una nueva partición de 2G

1. **Abrir `fdisk` para el disco en cuestión** (por ejemplo, `/dev/sda`):

   ```bash
   fdisk /dev/sda
   ```

2. **Listar la tabla de particiones actual**:

   ```plain text
   Command (m for help): p
   ```

3. **Crear una nueva partición**:

   ```plain text
   Command (m for help): n
   ```

4. **Seleccionar el tipo de partición**:
   - Para una partición primaria, presiona `p`.
   - Para una partición extendida, presiona `e`.
   - En la mayoría de los casos, si ya tienes cuatro particiones primarias, deberás crear una partición extendida y luego una partición lógica dentro de ella.

5. **Especificar el número de partición** (si se te solicita).

6. **Definir el primer sector** (presiona `Enter` para usar el valor predeterminado).

7. **Definir el último sector o tamaño**:

   ```plain text
   +2G
   ```

8. **Cambiar el tipo de partición a `swap`**:

   ```plain text
   Command (m for help): t
   ```

9. **Ingresar el número hexadecimal para la partición de swap**:

   ```plain text
   Hex code (type L to list all codes): 82
   ```

10. **Guardar los cambios y salir de `fdisk`**:

    ```plain text
    Command (m for help): w
    ```

## Paso 2: Notificar al sistema sobre la nueva partición

1. **Actualizar la tabla de particiones**:

   ```bash
   partx -a /dev/sda
   partprobe
   ```

## Paso 3: Configurar y activar la nueva partición de swap

1. **Crear el sistema de archivos swap en la nueva partición**:

   ```bash
   mkswap /dev/sdaX
   ```

   (Reemplaza `/dev/sdaX` con el nombre de la nueva partición creada, por ejemplo, `/dev/sda8`).

2. **Obtener el UUID de la nueva partición de swap**:

   ```bash
   blkid /dev/sdaX
   ```

   Copia el UUID de la nueva partición de swap.

3. **Agregar la nueva partición de swap al archivo `/etc/fstab` para que se active al arrancar**:

   ```bash
   vim /etc/fstab
   ```

   Agrega la siguiente línea al final del archivo (reemplaza `UUID=XXXXX` con el UUID que obtuviste):

   ```plain text
   UUID=XXXXX swap swap defaults 0 0
   ```

4. **Activar la nueva partición de swap**:

   ```bash
   swapon /dev/sdaX
   ```

## Paso 4: Verificar la configuración de swap

1. **Verificar que la nueva partición de swap esté activa**:

   ```bash
   swapon -s
   ```

2. **Confirmar que la nueva partición de swap se activará automáticamente al reiniciar**:

   ```bash
   cat /proc/swaps
   ```

Siguiendo estos pasos, habrás creado una nueva partición de swap de 2G que se activará automáticamente al arrancar el sistema, sin afectar la partición de swap original.

## Ejemplo completo con partición real (`/dev/sdd1`)

Excelente, ahora que has creado el espacio de intercambio (swap) en `/dev/sdd1`, el siguiente paso es configurar el sistema para que lo use automáticamente al iniciar. A continuación, se detallan los pasos restantes.

### Paso 1: Copiar el UUID de la nueva partición de swap

El UUID de la partición de swap del ejemplo es `5acaaba7-b439-4be0-b1d3-af079f224d5c`. Necesitarás este UUID para configurar `/etc/fstab`.

### Paso 2: Editar `/etc/fstab`

1. Abre el archivo `/etc/fstab` en tu editor de texto favorito (en este ejemplo, `vim`):

   ```bash
   vim /etc/fstab
   ```

2. Añade la siguiente línea al final del archivo para incluir la nueva partición de swap:

   ```plain text
   UUID=5acaaba7-b439-4be0-b1d3-af079f224d5c swap swap defaults 0 0
   ```

3. Guarda y cierra el archivo. En `vim`, puedes hacerlo presionando `Esc`, luego escribiendo `:wq` y presionando `Enter`.

### Paso 3: Activar la nueva partición de swap

Activa la partición de swap manualmente con el siguiente comando:

```bash
swapon /dev/sdd1
```

### Paso 4: Verificar la configuración de swap

1. Verifica que la partición de swap esté activa:

   ```bash
   swapon -s
   ```

   Deberías ver una salida similar a:

   ```plain text
   Filename                                Type            Size    Used    Priority
   /dev/sdd1                               partition       2097148 0       -2
   ```

2. También puedes verificarlo con:

   ```bash
   cat /proc/swaps
   ```

   Esto debería mostrar que `/dev/sdd1` está activa como swap.

Siguiendo estos pasos, habrás configurado correctamente tu partición de swap de 2G para que se active automáticamente al arrancar el sistema.

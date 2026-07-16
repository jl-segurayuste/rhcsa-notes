# Crear VDO

Para agregar un nuevo disco virtual de 10 GiB a tu máquina virtual, crear un volumen VDO (Virtual Data Optimizer) de 20 GiB en ese disco y montarlo de forma persistente, debes seguir varios pasos. A continuación se describe el procedimiento detallado:

## Paso 1: Agregar un nuevo disco virtual

1. **Apaga tu máquina virtual** (esto es necesario en muchos hipervisores para agregar hardware).
2. **Accede a la configuración de tu máquina virtual** en el hipervisor que estés usando (por ejemplo, VirtualBox, VMware, etc.).
3. **Agrega un nuevo disco duro** de 10 GiB a la máquina virtual.
4. **Inicia la máquina virtual**.

## Paso 2: Preparar el disco para VDO

1. **Identifica el nuevo disco**:

   ```bash
   lsblk
   ```

   Busca el disco que tiene aproximadamente 10 GiB, por ejemplo, `/dev/sdb`.

2. **Crear una partición** en el nuevo disco:

   ```bash
   parted /dev/sdb --script mklabel gpt
   parted /dev/sdb --script mkpart primary 0% 100%
   ```

3. **Instalar VDO** (si no está ya instalado):

   ```bash
   sudo yum install vdo kmod-kvdo -y
   ```

4. **Crear y configurar el VDO**:

   ```bash
   sudo vdo create --name=myvdo --device=/dev/sdb1 --vdoLogicalSize=20G
   ```

5. **Formatear el VDO con el sistema de archivos XFS**:

   ```bash
   sudo mkfs.xfs -K /dev/mapper/myvdo
   ```

## Paso 3: Montar el VDO de forma persistente

1. **Crear un punto de montaje**:

   ```bash
   sudo mkdir /mnt/myvdo
   ```

2. **Montar el VDO**:

   ```bash
   sudo mount /dev/mapper/myvdo /mnt/myvdo
   ```

3. **Añadir la entrada al `/etc/fstab`** para que el montaje sea persistente:

   ```bash
   echo "/dev/mapper/myvdo /mnt/myvdo xfs defaults 0 0" | sudo tee -a /etc/fstab
   ```

## Explicación de los comandos

- **`lsblk`**: lista todos los bloques de dispositivos (discos y particiones) conectados al sistema.
- **`parted`**: utilizado para crear y manipular tablas de particiones. En este caso, se usa para crear una tabla de particiones GPT y una partición primaria en el nuevo disco.
- **`vdo create`**: crea un volumen VDO.
  - `--name=myvdo`: especifica el nombre del volumen VDO.
  - `--device=/dev/sdb1`: especifica la partición en la que se creará el VDO.
  - `--vdoLogicalSize=20G`: especifica el tamaño lógico del VDO.
- **`mkfs.xfs -K /dev/mapper/myvdo`**: formatea el volumen VDO con el sistema de archivos XFS.
- **`mkdir /mnt/myvdo`**: crea un directorio para montar el volumen VDO.
- **`mount /dev/mapper/myvdo /mnt/myvdo`**: monta el volumen VDO en el directorio creado.
- **`echo ... | tee -a /etc/fstab`**: añade la entrada al archivo `/etc/fstab` para que el volumen VDO se monte automáticamente al iniciar el sistema.

## Verificación

1. **Verificar que el volumen está montado**:

   ```bash
   df -h | grep /mnt/myvdo
   ```

2. **Reiniciar el sistema** y comprobar que el volumen se monta automáticamente:

   ```bash
   sudo reboot
   ```

   Después del reinicio, verifica de nuevo con `df -h` que el volumen está montado en `/mnt/myvdo`.

## Qué es VDO

### ¿Qué es un VDO?

VDO (Virtual Data Optimizer) es una tecnología de Red Hat que se utiliza para optimizar el almacenamiento de datos en Linux. VDO trabaja reduciendo la cantidad de espacio en disco necesario para almacenar datos mediante técnicas como la deduplicación y la compresión. Esto es especialmente útil en entornos donde se manejan grandes cantidades de datos, como centros de datos y nubes privadas, permitiendo ahorrar espacio en disco y, en consecuencia, costos asociados al almacenamiento.

### Características principales de VDO

1. **Deduplicación**: identifica bloques de datos idénticos y almacena solo una copia de ellos, eliminando duplicados.
2. **Compresión**: reduce el tamaño de los datos mediante algoritmos de compresión.
3. **Thin Provisioning**: permite crear volúmenes lógicos más grandes que el espacio físico disponible, asignando espacio según sea necesario.

### Nota sobre el tamaño lógico

Aunque el disco físico tiene 10 GiB, el tamaño lógico del volumen VDO configurado (`--vdoLogicalSize=20G`) es de 20 GiB gracias a la deduplicación y compresión.

> **Nota RHEL 8 vs RHEL 9:** este ejercicio usa el paquete `vdo` y el comando standalone `vdo create` (válidos en RHEL 8). En **RHEL 9** ese paquete y comando ya no existen — la gestión de VDO se integró en LVM. El equivalente en RHEL 9 es crear el volumen VDO con `lvcreate`, por ejemplo:
>
> ```bash
> sudo vgcreate myvg /dev/sdb1
> sudo lvcreate --type vdo -n myvdo -L 10G --virtualsize 20G myvg
> sudo mkfs.xfs -K /dev/myvg/myvdo
> ```
>
> El resto de pasos (punto de montaje, `/etc/fstab`, verificación) son iguales.

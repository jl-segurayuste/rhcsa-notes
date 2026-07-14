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

Siguiendo estos pasos, habrás agregado un nuevo disco virtual, creado un volumen VDO de mayor tamaño y configurado el montaje persistente de dicho volumen.

## Qué es VDO

### ¿Qué es un VDO?

VDO (Virtual Data Optimizer) es una tecnología de Red Hat que se utiliza para optimizar el almacenamiento de datos en Linux. VDO trabaja reduciendo la cantidad de espacio en disco necesario para almacenar datos mediante técnicas como la deduplicación y la compresión. Esto es especialmente útil en entornos donde se manejan grandes cantidades de datos, como centros de datos y nubes privadas, permitiendo ahorrar espacio en disco y, en consecuencia, costos asociados al almacenamiento.

### Características principales de VDO

1. **Deduplicación**: identifica bloques de datos idénticos y almacena solo una copia de ellos, eliminando duplicados.
2. **Compresión**: reduce el tamaño de los datos mediante algoritmos de compresión.
3. **Thin Provisioning**: permite crear volúmenes lógicos más grandes que el espacio físico disponible, asignando espacio según sea necesario.

### ¿Qué se está haciendo al realizar este ejercicio?

El ejercicio consiste en agregar un nuevo disco virtual a una máquina, crear un volumen VDO en ese disco, y montarlo de manera persistente. A continuación, se explica cada paso y su propósito.

### 1. Agregar un nuevo disco virtual

Se agrega un nuevo disco virtual de 10 GiB a la máquina virtual. Este disco servirá como el almacenamiento físico sobre el cual se creará el volumen VDO.

### 2. Preparar el disco para VDO

- **Identificar el nuevo disco**: se utiliza el comando `lsblk` para listar todos los bloques de dispositivos conectados al sistema y así identificar el nuevo disco.
- **Crear una partición**: con `parted`, se crea una partición en el nuevo disco. Esto organiza el espacio del disco para que pueda ser utilizado por VDO.
- **Instalar VDO**: si no está instalado, se instala el paquete `vdo` junto con su módulo del kernel `kmod-kvdo`.

### 3. Crear y configurar el VDO

- **Crear el volumen VDO**: este comando crea un volumen VDO llamado `myvdo` en la partición `/dev/sdb1`. Aunque el disco físico tiene 10 GiB, el tamaño lógico del volumen VDO es 20 GiB, gracias a la deduplicación y compresión.

  ```bash
  sudo vdo create --name=myvdo --device=/dev/sdb1 --vdoLogicalSize=20G
  ```

### 4. Formatear el VDO

- **Formatear con XFS**: se formatea el volumen VDO con el sistema de archivos XFS, lo cual prepara el volumen para almacenar datos.

  ```bash
  sudo mkfs.xfs -K /dev/mapper/myvdo
  ```

### 5. Montar el VDO de forma persistente

- **Crear el punto de montaje**:

  ```bash
  sudo mkdir /mnt/myvdo
  ```

  Se crea un directorio en `/mnt` donde se montará el volumen VDO.

- **Montar el volumen**:

  ```bash
  sudo mount /dev/mapper/myvdo /mnt/myvdo
  ```

  Se monta el volumen VDO en el directorio creado.

- **Hacer el montaje persistente**:

  ```bash
  echo "/dev/mapper/myvdo /mnt/myvdo xfs defaults 0 0" | sudo tee -a /etc/fstab
  ```

  Se añade una entrada en `/etc/fstab` para que el volumen VDO se monte automáticamente al iniciar el sistema.

### Propósito del ejercicio

Este ejercicio demuestra cómo agregar un nuevo almacenamiento a una máquina virtual, cómo optimizar ese almacenamiento utilizando VDO, y cómo configurar el sistema para que utilice este almacenamiento de manera eficiente y persistente. Los beneficios incluyen:

- **Ahorro de espacio**: al usar deduplicación y compresión, se puede almacenar más datos en menos espacio físico.
- **Optimización de recursos**: VDO ayuda a gestionar mejor los recursos de almacenamiento disponibles.
- **Persistencia**: configurar el montaje en `/etc/fstab` asegura que el volumen VDO esté disponible después de cada reinicio del sistema.

Al completar este ejercicio, habrás aprendido a configurar y utilizar VDO, una poderosa herramienta de optimización de almacenamiento en Linux.

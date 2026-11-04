# Configuración del almacenamiento local

## Enumere, cree y elimine particiones en discos MBR y GPT

La administración de particiones en discos MBR (Master Boot Record) y GPT (GUID Partition Table) se realiza principalmente con `fdisk` para MBR y `gdisk` o `parted` para GPT.

### Unidades de tamaño

| Símbolo | Nombre | Valor | Símbolo | Nombre | Valor |
|---|---|---|---|---|---|
| KB | Kilobyte | 10^3 | KiB | Kibibyte | 1024^1 |
| MB | Megabyte | 10^6 | MiB | Mebibyte | 1024^2 |
| GB | Gigabyte | 10^9 | GiB | Gibibyte | 1024^3 |
| TB | Terabyte | 10^12 | TiB | Tebibyte | 1024^4 |
| PB | Petabyte | 10^15 | PiB | Pebibyte | 1024^5 |
| EB | Exabyte | 10^18 | EiB | Exbibyte | 1024^6 |
| ZB | Zettabyte | 10^21 | ZiB | Zebibyte | 1024^7 |
| YB | Yottabyte | 10^24 | YiB | Yobibyte | 1024^8 |

### Tipos de dispositivos de disco

| Device Name | Descripción |
|---|---|
| /dev/sda | Un disco duro que utiliza el controlador SCSI. Usado para dispositivos de disco SCSI y SATA. Común en servidores físicos pero también en máquinas virtuales VMware. |
| /dev/nvme0n1 | El primer disco duro en una interfaz NVM Express (NVMe). NVMe es un método de grado de servidor para abordar dispositivos SSD avanzados. Nótese al final del nombre del dispositivo que el primer disco en este caso se denomina n1 en lugar de a (como es común con los otros tipos). |
| /dev/hda | El tipo de dispositivo de disco IDE (heredado). Raramente se ve este tipo de dispositivo en computadoras modernas. |
| /dev/vda | Un disco en una máquina virtual KVM que utiliza el controlador de disco virtio. Este es el tipo de dispositivo de disco común para máquinas virtuales KVM. |
| /dev/xvda | Un disco en una máquina virtual Xen que utiliza el controlador de disco virtual Xen. Se ve esto al instalar RHEL como una máquina virtual en la virtualización Xen. RHEL 8 no puede ser utilizado como un hipervisor Xen, pero podrías ver máquinas virtuales RHEL 8 encima del hipervisor Xen usando estos tipos de disco. |

### MBR (Master Boot Record)

#### 1. Listar Particiones

Lista las particiones en un disco MBR con `fdisk`:

```shell
sudo fdisk -l /dev/sdX
```

Reemplaza `/dev/sdX` con el nombre del disco, como `/dev/sda`.

#### 2. Crear una Nueva Partición

```shell
sudo fdisk /dev/sdX
```

Sigue las instrucciones interactivas para crear la nueva partición.

#### 3. Eliminar una Partición

```shell
sudo fdisk /dev/sdX
```

Sigue las instrucciones interactivas para eliminar la partición.

### GPT (GUID Partition Table)

#### 1. Listar Particiones

Lista las particiones en un disco GPT con `gdisk` o `parted`:

```shell
sudo gdisk -l /dev/sdX
```

o

```shell
sudo parted /dev/sdX print
```

Reemplaza `/dev/sdX` con el nombre del disco, como `/dev/sda`.

#### 2. Crear una Nueva Partición

Con `gdisk`:

```shell
sudo gdisk /dev/sdX
```

Sigue las instrucciones interactivas para crear la nueva partición.

O con `parted`:

```shell
sudo parted /dev/sdX
(parted) mkpart primary <tipo_fs> <inicio> <fin>
```

Reemplaza `<tipo_fs>`, `<inicio>` y `<fin>` según tus necesidades.

#### 3. Eliminar una Partición

```shell
sudo gdisk /dev/sdX
```

Sigue las instrucciones interactivas para eliminar la partición.

O con `parted`:

```shell
sudo parted /dev/sdX
(parted) rm <número_partición>
```

Reemplaza `<número_partición>` con el número de la partición que deseas eliminar.

> **Nota:** `partprobe` es un comando en sistemas Linux que se utiliza para informar al kernel del sistema operativo sobre los cambios en las particiones de disco sin la necesidad de reiniciar el sistema o desmontar las particiones.

### Ejercicio: crear una partición con `parted` paso a paso

Procedimiento paso a paso para crear una partición en un disco nuevo con `parted`:

#### Paso 1: Abrir la Shell Interactiva de `parted`

```shell
parted /dev/sdc
```

Este comando abre la shell interactiva de `parted` para el disco `/dev/sdc`. Necesitas ser root para realizar estos cambios.

#### Paso 2: Obtener una Visión General de los Comandos Disponibles

```shell
help
```

Este comando muestra una lista de comandos disponibles en `parted`, lo cual es útil para familiarizarse con las capacidades de la herramienta.

#### Paso 3: Imprimir la Tabla de Particiones Actual

```shell
print
```

Como el disco es nuevo, verás un mensaje indicando que no se reconoce la etiqueta del disco.

#### Paso 4: Crear una Nueva Etiqueta de Disco

```shell
mklabel
```

`parted` te pedirá que especifiques un tipo de etiqueta de disco. Al presionar Tab dos veces, verás una lista de tipos disponibles. Selecciona `gpt` (GUID Partition Table) y presiona Enter. GPT es un esquema de particionamiento moderno y robusto.

#### Paso 5: Crear una Nueva Partición

```shell
mkpart
```

`parted` te pedirá que nombres la partición. Escribe `part1` y presiona Enter. El nombre de la partición es arbitrario.

#### Paso 6: Especificar el Tipo de Sistema de Archivos

`parted` te preguntará por el tipo de sistema de archivos. Aunque esto puede ser confuso, ya que el tipo no se utiliza en realidad en este punto, puedes escribir `xfs` y presionar Enter para continuar. En este contexto, la selección del sistema de archivos no afectará la creación de la partición.

#### Paso 7: Especificar la Ubicación de Inicio de la Partición

`parted` te pedirá que especifiques la ubicación de inicio de la partición. Puedes hacerlo en bloques o en un desplazamiento desde el inicio del disco. Especifica `1MiB` (1 Mebibyte) para que la partición comience en el primer MiB del disco y presiona Enter. Esto deja un pequeño espacio al inicio del disco, lo cual es una práctica común para alineación de particiones.

#### Paso 8: Especificar el Final de la Partición

`parted` te pedirá que especifiques el final de la partición. Escribe `1GiB` para que la partición termine en el primer GiB del disco y presiona Enter.

#### Paso 9: Imprimir la Tabla de Particiones y Salir

```shell
print
```

Este comando muestra la tabla de particiones actual para que puedas verificar que la partición se ha creado correctamente.

```shell
quit
```

Este comando sale de la shell interactiva de `parted` y aplica los cambios realizados.

### Creación del sistema de fichero

| File System | Descripción |
|---|---|
| XFS | El sistema de archivos predeterminado en RHEL 8. |
| Ext4 | El sistema de archivos predeterminado en versiones anteriores de RHEL; todavía disponible y soportado en RHEL 8. |
| Ext3 | La versión anterior de Ext4. En RHEL 8, no es necesario usar Ext3. |
| Ext2 | Un sistema de archivos muy básico que se desarrolló a principios de los años 90. No es necesario usar este sistema de archivos en RHEL 8. |
| BtrFS | Un sistema de archivos relativamente nuevo que no está soportado en RHEL 8. |
| NTFS | Un sistema de archivos compatible con Windows que no está soportado en RHEL 8. |
| VFAT | Un sistema de archivos que ofrece compatibilidad con Windows y Mac y es el equivalente funcional del sistema de archivos FAT32. Útil en unidades USB que intercambian datos con otras computadoras, pero no en los discos duros de un servidor. |

### Mount (Capítulo 14)

## Cree y elimine volúmenes lógicos

> Ver también el fichero ampliado sobre este tema: [LVM (Logical Volume Manager)](lvm-logical-volume-manager.md).

La gestión de volúmenes físicos se realiza comúnmente con el administrador de volúmenes lógicos `LVM` (Logical Volume Manager).

| Command | Explanation |
|---|---|
| pvcreate | Creates physical volumes |
| pvs | Shows a summary of available physical volumes |
| pvdisplay | Shows a list of physical volumes and their properties |
| pvremove | Removes the physical volume signature from a block device |
| vgcreate | Creates volume groups |
| vgs | Shows a summary of available volume groups |
| vgdisplay | Shows a detailed list of volume groups and their properties |
| vgremove | Removes a volume group |
| lvcreate | Creates logical volumes |
| lvs | Shows a summary of all available logical volumes |
| lvdisplay | Shows a detailed list of available logical volumes and their properties |
| lvremove | Removes a logical volume |

> **Nota:** Si se está usando XFS como File System, no se podrá reducir el tamaño, solo aumentar.

### Physical y Group Volume

Para poder crear uno, es necesario tener una partición o disco que no esté asignado ya a un volume group. La creación se puede realizar de distintos modos:

1. Se añade un disco nuevo en el que no existe ninguna partición. En este caso, al no tener ninguna partición creada obliga a crear el *volume group* directamente. Se puede crear con el siguiente comando.

```bash
vgcreate vgnombre /dev/sdx
```

> [Imagen no migrada: captura de pantalla mostrando la salida de `vgcreate` al crear un volume group ocupando un disco completo sin particiones]

El inconveniente que tiene al crearlo así es que el *volume group* ocupará el 100% del disco añadido.

2. El segundo modo de hacerlo es realizar particiones con el tamaño que se desea, de este modo se podrá organizar según las necesidades del pv que necesite.

```bash
vgcreate vgnombre2 /dev/sdiX
```

> [Imagen no migrada: captura de pantalla mostrando la salida de `vgcreate` al crear un volume group sobre una partición concreta del disco]

De este modo se crea con el tamaño que se necesita y pudiendo utilizar el resto del disco para otras particiones y otros pv.

> **Nota:** Todo esto se realiza sin ningún sistema de ficheros. Para poder usar el comando `pvcreate` ese disco o partición debe tener el sistema de ficheros **XFS LVM (8e)**.

Para eliminarlo, primero hay que eliminar el vg y luego el pv. Con los comandos `vgremove` y `pvremove`.

### Logical Volume

Para crear un logical volume se realiza con el siguiente comando.

```bash
lvcreate -n lvnombre -L 100M vg
```

> **A tener en cuenta:** `lvresize` y `lvextend` son dos comandos utilizados en la gestión de volúmenes lógicos en Linux bajo LVM (Logical Volume Manager). Aunque están relacionados y pueden parecer similares, se utilizan para propósitos ligeramente diferentes dentro de la gestión de espacio en disco. Aquí está la diferencia entre ambos:
>
> **lvextend**
> - **Propósito específico:** el comando `lvextend` se utiliza específicamente para **aumentar** el tamaño de un volumen lógico existente. Es decir, no puede reducir el tamaño de un volumen; sólo puede hacerlo más grande.
> - **Uso común:** se utiliza cuando se desea añadir más espacio a un volumen lógico sin modificar ningún otro aspecto del volumen. Por ejemplo, si tienes un volumen lógico que se está quedando sin espacio, puedes usar `lvextend` para aumentar su capacidad.
> - **Ejemplo de comando:** este comando extiende el volumen lógico llamado `mylv` en el grupo de volúmenes `myvg` por 100 GB adicionales.
>   ```shell
>   lvextend -L+100G /dev/myvg/mylv
>   ```
>
> **lvresize**
> - **Propósito general:** el comando `lvresize` es más flexible y puede utilizarse tanto para aumentar como para **disminuir** el tamaño de un volumen lógico. Básicamente, es una herramienta más general que puede manejar ambos casos de redimensionamiento.
> - **Uso común:** se utiliza cuando necesitas ajustar el tamaño de un volumen lógico, ya sea hacia arriba o hacia abajo. Esto lo hace más versátil que `lvextend`.
> - **Ejemplo de comando:** estos comandos ajustan el tamaño del volumen lógico `mylv` en el grupo de volúmenes `myvg` aumentando o reduciendo su capacidad según sea necesario.
>   ```shell
>   lvresize -L +50G /dev/myvg/mylv  # Aumenta el tamaño por 50 GB
>   lvresize -L -20G /dev/myvg/mylv  # Reduce el tamaño por 20 GB
>   ```
>
> **Cuidado al reducir:** sin `-r`, `lvresize -L -20G` recorta el volumen sin tocar el sistema de archivos y lo
> deja corrupto si los datos ocupaban esa zona. Usa siempre `lvresize -r -L -20G …` (reduce primero el
> sistema de archivos y luego el volumen). Con XFS no se puede reducir: `-r` falla y no cambia nada.
>
> **Resumen:** mientras `lvextend` está especializado únicamente en la expansión de volúmenes lógicos, `lvresize` ofrece una funcionalidad más amplia, pudiendo tanto aumentar como reducir el tamaño de un volumen lógico. Esto hace que `lvresize` sea una herramienta más flexible y a menudo preferida cuando se trabaja en la administración de espacio en LVM.

**Ajustar el sistema de archivos** (después de extender el volumen lógico, necesitas ajustar el sistema de archivos para usar el nuevo espacio):

Para un sistema de archivos ext4, por ejemplo:

```shell
resize2fs /dev/mapper/vgdata3-lvdata3
```

Para un sistema de archivos XFS:

```shell
xfs_growfs /mount/point
```

### Redimensionar Logical Volume y File system

La diferencia entre los comandos `lvresize` radica en cómo especifican el tamaño del volumen lógico en relación al grupo de volúmenes (VG) y el espacio libre disponible. A continuación, te explico cada comando en detalle:

1. **Redimensionar el volumen lógico para que ocupe el 75% del espacio total del grupo de volúmenes:**

   ```shell
   lvresize -r -l 75%VG /dev/vgdata/lvdata
   ```

   - **Descripción:** este comando redimensiona el volumen lógico `/dev/vgdata/lvdata` para que ocupe el 75% del tamaño total del grupo de volúmenes `vgdata`.
   - **Interpretación:** si el grupo de volúmenes `vgdata` tiene, por ejemplo, 100 GB en total, este comando ajustará el tamaño del volumen lógico a 75 GB.

2. **Añadir el 75% del tamaño total del grupo de volúmenes al volumen lógico:**

   ```shell
   lvresize -r -l +75%VG /dev/vgdata/lvdata
   ```

   - **Descripción:** este comando intenta añadir el 75% del tamaño total del grupo de volúmenes `vgdata` al volumen lógico `/dev/vgdata/lvdata`.
   - **Interpretación:** si el grupo de volúmenes `vgdata` tiene 100 GB en total y el volumen lógico ya ocupa 20 GB, este comando añadirá 75 GB adicionales al volumen lógico, haciendo que el nuevo tamaño sea 95 GB (20 GB + 75 GB).

3. **Añadir el 75% del espacio libre al volumen lógico:**

   ```shell
   lvresize -r -l +75%FREE /dev/vgdata/lvdata
   ```

   - **Descripción:** este comando añade el 75% del espacio libre del grupo de volúmenes `vgdata` al volumen lógico `/dev/vgdata/lvdata`.
   - **Interpretación:** si el grupo de volúmenes `vgdata` tiene 40 GB de espacio libre, este comando añadirá el 75% de esos 40 GB al volumen lógico, es decir, 30 GB adicionales.

4. **Redimensionar el volumen lógico a un tamaño total que equivalga al 75% del espacio libre:**

   ```shell
   lvresize -r -l 75%FREE /dev/vgdata/lvdata
   ```

   - **Descripción:** este comando redimensiona el volumen lógico `/dev/vgdata/lvdata` para que su tamaño total sea equivalente al 75% del espacio libre en el grupo de volúmenes `vgdata`.
   - **Interpretación:** si el grupo de volúmenes `vgdata` tiene 40 GB de espacio libre, este comando ajustará el tamaño total del volumen lógico para que sea 30 GB (75% de 40 GB).

**Resumen de diferencias:**

- **75%VG vs. +75%VG:**
  - **75%VG:** redimensiona el volumen lógico para que ocupe el 75% del tamaño total del grupo de volúmenes.
  - **+75%VG:** añade el 75% del tamaño total del grupo de volúmenes al volumen lógico actual.
- **75%FREE vs. +75%FREE:**
  - **75%FREE:** redimensiona el volumen lógico para que su tamaño total sea el 75% del espacio libre en el grupo de volúmenes.
  - **+75%FREE:** añade el 75% del espacio libre al volumen lógico actual.

## Configure los sistemas para montar los sistemas de archivos durante el arranque con un ID único universal (UUID) o una etiqueta

Configurar el montaje durante el arranque con UUID o etiqueta se hace en el archivo `/etc/fstab` (File System Table), que define cómo se montan los sistemas de archivos al iniciar el sistema.

Campos de `/etc/fstab`:

| Campo | Descripción |
|---|---|
| **Device** | El dispositivo que debe ser montado. Se puede utilizar un nombre de dispositivo, UUID o etiqueta. |
| **Mount Point** | El directorio o interfaz del kernel donde se necesita montar el dispositivo. |
| **File System** | El tipo de sistema de archivos. |
| **Mount Options** | Opciones de montaje. |
| **Dump Support** | Usa 1 para habilitar el soporte para hacer copias de seguridad usando la utilidad `dump`. |
| **Automatic Check** | Especifica si el sistema de archivos debe ser comprobado automáticamente al iniciar. Usa 0 para deshabilitar la verificación automática, 1 si este es el sistema de archivos raíz y debe ser verificado automáticamente, y 2 para todos los demás sistemas de archivos que necesitan verificación automática al iniciar. Los sistemas de archivos de red deben tener esta opción configurada en 0. |

### Ejemplo de `/etc/fstab`

```
# <Device>      <Mount Point>  <File System>  <Mount Options>      <Dump Support>  <Automatic Check>
UUID=xxxx-xxxx  /              ext4           defaults             1               1
UUID=yyyy-yyyy  /home          ext4           defaults             1               2
UUID=zzzz-zzzz  swap           swap           defaults             0               0
```

### Detalles de los campos

1. **Device**: este campo especifica el dispositivo que será montado. Puede ser un nombre de dispositivo, UUID, o una etiqueta (label). Por ejemplo:
   - `/dev/sda1`
   - `UUID=xxxx-xxxx`
   - `LABEL=mydisk`
2. **Mount Point**: el directorio en el cual se montará el sistema de archivos. Por ejemplo:
   - `/` (root)
   - `/home`
   - `/mnt/data`
3. **File System**: el tipo de sistema de archivos a montar. Algunos ejemplos comunes son:
   - `ext4`
   - `xfs`
   - `nfs`
4. **Mount Options**: opciones específicas de montaje que pueden incluir configuraciones como `defaults`, `noatime`, `rw`, `ro`, entre otras.
5. **Dump Support**: este campo define si el sistema de archivos debe ser incluido en las operaciones de `dump`. Usualmente:
   - `0` (no usar `dump`)
   - `1` (usar `dump`)
6. **Automatic Check**: determina si el sistema de archivos debe ser verificado automáticamente durante el arranque. Los valores típicos son:
   - `0` (no verificar)
   - `1` (verificar, usualmente para el sistema de archivos raíz)
   - `2` (verificar, para otros sistemas de archivos)

### Opciones de montaje comunes

| Opción | Uso |
|---|---|
| **auto/noauto** | El sistema de archivos será (o no) montado automáticamente. |
| **acl** | Agrega soporte para listas de control de acceso del sistema de archivos. |
| **user_xattr** | Agrega soporte para atributos extendidos del usuario. |
| **ro** | Monta el sistema de archivos en modo de solo lectura. |
| **atime/noatime** | Deshabilita o habilita las modificaciones del tiempo de acceso. |
| **noexec/exec** | Deniega o permite la ejecución de archivos de programa desde el sistema de archivos. |
| **_netdev** | Usa esto para montar un sistema de archivos de red, indicando que se debe esperar hasta que la red esté disponible. |

### Utilizando UUID

1. **Obtener el UUID del sistema de archivos:**

   ```shell
   sudo blkid
   ```

   Encuentra el UUID asociado al sistema de archivos que deseas montar durante el arranque.

2. **Editar el archivo `/etc/fstab`:**

   ```shell
   sudo nano /etc/fstab
   ```

   Agrega una línea para el montaje del sistema de archivos utilizando el UUID. El formato típico es:

   ```
   UUID=uuid_del_sistema_de_archivos   punto_de_montaje   tipo_de_sistema_de_archivos   opciones   0   0
   ```

   Por ejemplo:

   ```
   UUID=123e4567-89ab-cdef-0123-456789abcdef   /mnt/mi_sistema_de_archivos   ext4   defaults   0   2
   ```

### Utilizando etiqueta

1. **Agregar una etiqueta al sistema de archivos:**

   ```shell
   sudo e2label /dev/sdXn nueva_etiqueta
   ```

   Reemplaza `/dev/sdXn` con la partición correspondiente y `nueva_etiqueta` con la etiqueta que deseas asignar.

2. **Editar el archivo `/etc/fstab`:**

   ```shell
   sudo nano /etc/fstab
   ```

   Agrega una línea para el montaje del sistema de archivos utilizando la etiqueta. El formato típico es:

   ```
   LABEL=etiqueta_del_sistema_de_archivos   punto_de_montaje   tipo_de_sistema_de_archivos   opciones   0   0
   ```

   Por ejemplo:

   ```
   LABEL=mi_etiqueta   /mnt/mi_sistema_de_archivos   ext4   defaults   0   2
   ```

### Ejemplo completo

```shell
# Obtener el UUID del sistema de archivos
sudo blkid

# Editar el archivo /etc/fstab para montar utilizando UUID
sudo nano /etc/fstab

# Agregar una línea con el UUID del sistema de archivos
UUID=123e4567-89ab-cdef-0123-456789abcdef   /mnt/mi_sistema_de_archivos   ext4   defaults   0   2
```

o

```shell
# Agregar una etiqueta al sistema de archivos
sudo e2label /dev/sdXn mi_etiqueta

# Editar el archivo /etc/fstab para montar utilizando la etiqueta
sudo nano /etc/fstab

# Agregar una línea con la etiqueta del sistema de archivos
LABEL=mi_etiqueta   /mnt/mi_sistema_de_archivos   ext4   defaults   0   2
```

Ten cuidado al editar `/etc/fstab`: un error en esta configuración puede afectar el arranque del sistema.

## Agregue particiones y volúmenes lógicos nuevos, y cambie a un sistema de forma no destructiva

Realiza copias de seguridad antes de cualquier cambio en la configuración del almacenamiento para evitar la pérdida de datos accidental.

### Agregar particiones y volúmenes lógicos nuevos

1. **Crear una nueva partición:** utiliza herramientas como `fdisk` o `parted` para crear una nueva partición en el disco deseado.
2. **Inicializar el volumen físico con LVM:**

   ```shell
   sudo pvcreate /dev/sdXn
   ```

   Reemplaza `/dev/sdXn` con el nombre de la nueva partición.

3. **Agregar el volumen físico al grupo de volúmenes existente o crear un nuevo grupo de volúmenes:**
   - **Agregar a un grupo de volúmenes existente:**

     ```shell
     sudo vgextend nombre_del_grupo /dev/sdXn
     ```

   - **Crear un nuevo grupo de volúmenes:**

     ```shell
     sudo vgcreate nuevo_grupo /dev/sdXn
     ```

4. **Crear un nuevo volumen lógico:**

   ```shell
   sudo lvcreate -L tamaño_del_volumen -n nombre_del_volumen nombre_del_grupo
   ```

   Reemplaza `tamaño_del_volumen`, `nombre_del_volumen`, y `nombre_del_grupo` con los valores adecuados.

### Cambiar a un sistema de forma no destructiva

1. **Crear un nuevo sistema de archivos en el volumen lógico:** por ejemplo, si es un sistema de archivos ext4:

   ```shell
   sudo mkfs.ext4 /dev/nombre_del_grupo/nombre_del_volumen
   ```

2. **Montar el volumen lógico:**

   ```shell
   sudo mkdir /mnt/punto_de_montaje
   sudo mount /dev/nombre_del_grupo/nombre_del_volumen /mnt/punto_de_montaje
   ```

3. **Actualizar el archivo `/etc/fstab` para montar el volumen lógico en el arranque:**

   ```shell
   sudo nano /etc/fstab
   ```

   Agrega una línea similar a la siguiente:

   ```
   /dev/nombre_del_grupo/nombre_del_volumen   /mnt/punto_de_montaje   tipo_de_sistema_de_archivos   defaults   0   2
   ```

4. **Cambiar al nuevo sistema de archivos de forma no destructiva:** puedes hacer esto desmontando temporalmente el sistema de archivos original y montando el nuevo en su lugar. Por ejemplo:

   ```shell
   sudo umount /ruta/actual
   sudo mount /dev/nombre_del_grupo/nombre_del_volumen /ruta/actual
   ```

   Luego, puedes cambiar la entrada en `/etc/fstab` para que apunte al nuevo sistema de archivos.

## Revisar el sistema de fichero usado

Determina qué sistema de archivos se está utilizando en una partición con estos comandos:

### Usar el comando `lsblk`

El comando `lsblk` con la opción `-f` mostrará información sobre el sistema de archivos de cada dispositivo de bloque.

```shell
lsblk -f
```

### Usar el comando `df`

El comando `df -T` mostrará los sistemas de archivos montados y sus tipos.

```shell
df -T
```

### Usar el comando `blkid`

El comando `blkid` proporciona información detallada sobre los dispositivos de bloque, incluido el tipo de sistema de archivos.

```shell
blkid
```

### Usar el comando `file`

El comando `file` puede identificar el tipo de sistema de archivos en una partición.

```shell
file -s /dev/mapper/vgdata3-lvdata3
```

### Ejemplo completo

```shell
# Mostrar información de los dispositivos de bloque, incluidos los sistemas de archivos
lsblk -f

# Mostrar los sistemas de archivos montados y sus tipos
df -T

# Mostrar información detallada de los dispositivos de bloque
blkid

# Identificar el tipo de sistema de archivos en una partición específica
file -s /dev/mapper/vgdata3-lvdata3
```

### Salida esperada

1. **`lsblk -f`**:

   ```
   NAME                    FSTYPE      LABEL UUID                                   FSAVAIL FSUSE% MOUNTPOINT
   sda
   ├─sda1                  ext4              e2e3e8b4-43df-4d50-8e8b-2732f5d348b8   15.8G    32%  /
   ├─sda2                  swap              3b1c7bdb-7a57-4b37-a9ef-372e504b2817                [SWAP]
   └─sda3                  xfs               7a9647f1-d24f-4c22-8d6b-2df76b1d4d88
   ```

2. **`df -T`**:

   ```
   Filesystem     Type      1K-blocks    Used Available Use% Mounted on
   /dev/sda1      ext4       20642444  6238660  13303784  32% /
   /dev/sda3      xfs       524032000 32409536 491622464   7% /mnt/data
   ```

3. **`blkid`**:

   ```
   /dev/sda1: UUID="e2e3e8b4-43df-4d50-8e8b-2732f5d348b8" TYPE="ext4"
   /dev/sda2: UUID="3b1c7bdb-7a57-4b37-a9ef-372e504b2817" TYPE="swap"
   /dev/sda3: UUID="7a9647f1-d24f-4c22-8d6b-2df76b1d4d88" TYPE="xfs"
   ```

4. **`file -s`**:

   ```
   /dev/mapper/vgdata3-lvdata3: XFS filesystem data (blksz 4096, inosz 512, v2 dirs)
   ```

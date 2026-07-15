# LVM (Logical Volume Manager)

> Subpágina vinculada desde [Configuración del almacenamiento local](04-almacenamiento-local.md), sección "Cree y elimine volúmenes lógicos".

LVM es una herramienta de administración de volúmenes en sistemas Linux que permite gestionar el almacenamiento de manera más flexible al crear, redimensionar y mover particiones de manera dinámica. Aquí tienes una introducción básica:

## Conceptos clave

### Physical Volumes (PV)

- Son discos duros o particiones que LVM utiliza como componentes básicos.
- Puedes inicializar un disco o partición como un PV usando el comando `pvcreate`.

**Inicializar un disco como Physical Volume:**

```shell
pvcreate /dev/sdX   # Reemplaza /dev/sdX con tu disco o partición
```

### Volume Groups (VG)

- Los Physical Volumes se agrupan en Volume Groups.
- Puedes crear un VG con el comando `vgcreate`.

**Crear un Volume Group:**

```shell
vgcreate nombre_del_grupo /dev/sdX   # Reemplaza nombre_del_grupo y /dev/sdX
```

### Logical Volumes (LV)

- Los Logical Volumes son equivalentes a las particiones en un sistema no LVM.
- Se crean dentro de un Volume Group y puedes redimensionarlos fácilmente.
- Utiliza el comando `lvcreate` para crear Logical Volumes.

**Crear un Logical Volume:**

```shell
lvcreate -L tamaño -n nombre_del_lv nombre_del_grupo
```

**Formatear el Logical Volume:**

```shell
mkfs.tipo_de_sistema_de_archivos /dev/nombre_del_grupo/nombre_del_lv
```

**Montar el Logical Volume:**

```shell
mount /dev/nombre_del_grupo/nombre_del_lv /ruta_de_montaje
```

**Redimensionar Logical Volumes:** puedes aumentar o disminuir el tamaño de un LV con `lvresize`.

```shell
lvresize -L +2G /dev/nombre_del_grupo/nombre_del_lv   # Aumenta en 2GB
```

## Pasos básicos para trabajar con LVM

**Eliminar Logical Volumes y Volume Groups:**

```shell
lvremove /dev/nombre_del_grupo/nombre_del_lv   # Eliminar LV
vgremove nombre_del_grupo   # Eliminar VG
```

La gestión de LVM puede volverse más compleja a medida que se exploran funciones avanzadas como instantáneas (snapshots) o migración de datos.

**Listado de volúmenes lógicos:**

```bash
lvdisplay #lv
vgdisplay #lv
```

## ¿Un VG por instalación o un VG por partición?

No es necesario crear un Grupo de Volúmenes (VG) por cada partición. De hecho, generalmente se utiliza un solo VG para toda la instalación del sistema y se crean varios Logical Volumes (LV) dentro de ese VG para cada partición.

La ventaja de utilizar un único VG es que te proporciona flexibilidad para redistribuir el espacio entre las diferentes particiones según sea necesario. Puedes ajustar el tamaño de los LVs sin preocuparte por la cantidad de espacio disponible en cada VG.

A continuación, un ejemplo de cómo podrías estructurar la configuración de LVM para varias particiones:

1. **Crear un Grupo de Volúmenes (VG):**

   ```shell
   sudo vgcreate debian_vg /dev/sdX   # Reemplaza /dev/sdX con tu dispositivo físico
   ```

2. **Crear Logical Volumes (LVs) dentro del VG:**

   ```shell
   sudo lvcreate -L 512M -n boot_lv debian_vg
   sudo lvcreate -L 2G -n swap_lv debian_vg
   sudo lvcreate -L 20G -n root_lv debian_vg
   sudo lvcreate -L 10G -n var_lv debian_vg
   sudo lvcreate -l 100%FREE -n home_lv debian_vg
   ```

3. **Formatear los LVs con sistemas de archivos:**

   ```shell
   sudo mkfs.ext4 /dev/debian_vg/boot_lv
   sudo mkswap /dev/debian_vg/swap_lv
   sudo mkfs.ext4 /dev/debian_vg/root_lv
   sudo mkfs.ext4 /dev/debian_vg/var_lv
   sudo mkfs.ext4 /dev/debian_vg/home_lv
   ```

4. **Montar los LVs en sus respectivos directorios:**

   ```shell
   sudo mount /dev/debian_vg/root_lv /mnt   # Puedes montar en /mnt temporalmente durante la instalación
   sudo mkdir /mnt/boot
   sudo mount /dev/debian_vg/boot_lv /mnt/boot
   sudo mkdir /mnt/var
   sudo mount /dev/debian_vg/var_lv /mnt/var
   sudo mkdir /mnt/home
   sudo mount /dev/debian_vg/home_lv /mnt/home
   ```

Después de montar estos sistemas de archivos, puedes proceder con la instalación del sistema operativo en `/mnt` y configurar GRUB para que arranque desde la partición de arranque (`/boot`). Una vez que el sistema esté instalado, estos pasos te darán una estructura de LVM para gestionar de manera flexible las particiones del sistema.

### Un solo VG vs. un VG por partición: ventajas y desventajas

No hay un enfoque "incorrecto": hay diferentes formas válidas de configurar volúmenes lógicos con LVM. La elección de utilizar un único Grupo de Volúmenes (VG) para todo el sistema o grupos separados para cada partición depende en gran medida de las preferencias y necesidades específicas.

**1. Un solo Grupo de Volúmenes (VG) para todo el sistema:**

- **Ventajas:**
  - Mayor flexibilidad para redistribuir el espacio entre las particiones según sea necesario.
  - Menos complicado si estás administrando muchas particiones.
- **Desventajas:**
  - Si una partición consume demasiado espacio, podría afectar a otras particiones en el mismo VG.

**2. Un Grupo de Volúmenes (VG) por partición:**

- **Ventajas:**
  - Aísla el espacio de almacenamiento de cada partición, lo que evita que una partición consuma demasiado espacio de otras.
  - Mayor granularidad en la gestión del espacio.
- **Desventajas:**
  - Puede ser más complicado si estás administrando muchas particiones.
  - Menos flexibilidad para redistribuir el espacio entre particiones.

Si has creado un VG para cada partición, eso está bien y es completamente funcional. Simplemente asegúrate de que esta configuración satisface tus necesidades y facilita la administración del sistema. Si estás aprendiendo sobre LVM, puedes experimentar con ambos enfoques para comprender mejor sus ventajas y desventajas en diferentes situaciones.

## Redimensionado en caliente

Una de las ventajas clave de LVM es su capacidad para redimensionar volúmenes lógicos en caliente, lo que significa que puedes realizar cambios en el tamaño de tus volúmenes lógicos sin apagar el sistema o interrumpir los servicios.

Para lograr esto, puedes añadir espacio a un Grupo de Volúmenes (VG) en caliente, ya sea extendiendo un disco físico existente o añadiendo un nuevo disco físico. Luego, puedes utilizar ese espacio añadido para aumentar el tamaño de los volúmenes lógicos dentro de ese VG.

Secuencia de pasos generales:

1. **Añadir espacio al Grupo de Volúmenes:** si estás añadiendo espacio a un VG existente, utiliza `vgextend` para añadir un nuevo disco físico o extender uno existente.

   ```shell
   sudo vgextend nombre_del_vg /dev/nuevo_disco_fisico
   ```

2. **Aumentar el tamaño del Logical Volume:** utiliza `lvextend` para aumentar el tamaño de un Logical Volume en el VG.

   ```shell
   sudo lvextend -L +10G /dev/nombre_del_vg/nombre_del_lv
   ```

   En este ejemplo, se aumenta el tamaño en 10 GB, pero ajusta el valor según tus necesidades.

3. **Ampliar el sistema de archivos:** después de aumentar el tamaño del LV, necesitas ampliar el sistema de archivos en el LV para utilizar el nuevo espacio.

   ```shell
   sudo resize2fs /dev/nombre_del_vg/nombre_del_lv
   ```

Aunque estos pasos son generalmente seguros, realiza copias de seguridad antes de cambios significativos en la configuración de almacenamiento.

### Reconsiderando: un solo VG vs. VG separados por partición

La elección entre tener un solo Grupo de Volúmenes (VG) con varios volúmenes lógicos (LVs) o tener un VG separado para cada partición depende de varios factores y preferencias personales:

**Un solo Grupo de Volúmenes (VG):**

- **Ventajas:**
  1. **Flexibilidad para redistribuir espacio:** puedes ajustar el tamaño de cada LV sin preocuparte por la cantidad de espacio disponible en cada VG. Esta flexibilidad es útil si, por ejemplo, deseas redimensionar el sistema de archivos raíz sin tocar otros volúmenes lógicos.
  2. **Menos complejidad:** al tener un solo VG, la administración generalmente es más simple, ya que todas las particiones comparten el mismo conjunto de discos físicos.
- **Desventajas:**
  1. **Mayor riesgo de agotar espacio:** si una partición consume demasiado espacio, podría afectar a otras particiones en el mismo VG. Sin una cuidadosa monitorización, podría haber problemas de espacio imprevistos.

**VG separados para cada partición:**

- **Ventajas:**
  1. **Aislamiento del espacio:** cada VG actúa como un límite para el espacio disponible, evitando que una partición consuma todo el espacio disponible.
  2. **Mayor granularidad:** puedes asignar un VG separado para aplicaciones específicas o servicios, lo que puede facilitar la administración y el mantenimiento.
- **Desventajas:**
  1. **Menos flexibilidad en la redistribución:** redimensionar volúmenes lógicos entre VGs es más complicado que dentro de un solo VG. Si necesitas redistribuir espacio entre particiones, puede requerir más pasos y ser más propenso a errores.
  2. **Mayor complejidad general:** la gestión de múltiples VGs y particiones puede volverse más compleja a medida que crece la cantidad de discos físicos y particiones.

En resumen, ambas configuraciones son válidas y tienen sus propios conjuntos de ventajas y desventajas. La elección dependerá de las necesidades específicas y preferencias de administración del sistema. En entornos más grandes y complejos, tener un solo VG puede ofrecer más flexibilidad, mientras que en entornos más pequeños o específicos, la separación puede ser preferida por motivos de aislamiento y organización.

## Creando una plantilla de particionado en CentOS 7

- Primero se crea la partición `/boot` con 256M con formato ext4. Esta partición no llevará LVM.
- Segundo se crea la partición root que se montará en `/`. Se crea el `rootvg` y se le asigna todo el espacio libre al vg para que luego permita aumentar los lv una vez instalado el sistema. En caso de no haber hecho este ajuste, al iniciar el sistema operativo el pv o vg no verá el espacio que sobra del disco, ya que la partición sda2 está creada con espacio sin ocupar.
- Se crea el resto de particiones con sus lvs:

  > [Contenido no disponible: la página de origen contenía un bloque sincronizado (`synced block`) de Notion que referencia contenido de otra página no compartida con esta integración. Notion no permitió recuperar ese contenido; para verlo habría que compartir la página de origen o copiar el bloque directamente.]

  - `/opt` 1GB
  - `/usr` 4GB
  - `/tmp` 1GB
  - `/swap` 500M (no olvides asignar el formato swap)

> [Imagen no migrada: captura de pantalla de la plantilla de particionado LVM en el instalador de CentOS 7, mostrando las particiones /boot, root vg y los lvs de /opt, /usr, /tmp y swap]

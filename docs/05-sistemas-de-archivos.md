# Creación y configuración de sistemas de archivos

## Cree, monte, desmonte y utilice los sistemas de archivos vfat, ext4 y xfs

La creación y configuración de sistemas de archivos en Linux implica el uso de comandos específicos según el tipo de sistema de archivos que desees utilizar. A continuación, ejemplos para crear, montar y desmontar sistemas de archivos con VFAT (FAT32), ext4 y XFS.

### VFAT (FAT32)

**Crear un sistema de archivos VFAT:**

```shell
sudo mkfs.vfat -F 32 /dev/sdXn
```

Reemplaza `/dev/sdXn` con el nombre de la partición que deseas formatear.

**Montar y desmontar un sistema de archivos VFAT:**

```shell
# Montar
sudo mount /dev/sdXn /ruta/de/montaje

# Desmontar
sudo umount /ruta/de/montaje
```

### ext4

**Crear un sistema de archivos ext4:**

```shell
sudo mkfs.ext4 /dev/sdXn
```

Reemplaza `/dev/sdXn` con el nombre de la partición que deseas formatear.

**Montar y desmontar un sistema de archivos ext4:**

```shell
# Montar
sudo mount /dev/sdXn /ruta/de/montaje

# Desmontar
sudo umount /ruta/de/montaje
```

### XFS

**Crear un sistema de archivos XFS:**

```shell
sudo mkfs.xfs /dev/sdXn
```

Reemplaza `/dev/sdXn` con el nombre de la partición que deseas formatear.

**Montar y desmontar un sistema de archivos XFS:**

```shell
# Montar
sudo mount /dev/sdXn /ruta/de/montaje

# Desmontar
sudo umount /ruta/de/montaje
```

Estos comandos te permitirán crear, montar y desmontar sistemas de archivos con los formatos VFAT, ext4 y XFS en Linux. Asegúrate de reemplazar las rutas de las particiones y las rutas de montaje según tu configuración específica.

## Monte y desmonte los sistemas de archivos de red utilizando NFS

Para montar y desmontar sistemas de archivos de red utilizando NFS (Network File System) en Linux, puedes seguir estos pasos. Ten en cuenta que tanto el servidor NFS como el cliente deben estar configurados correctamente para que esto funcione.

### Montar un sistema de archivos NFS

```shell
sudo mount -t nfs servidor:/ruta/compartida /ruta/de/montaje
```

- Reemplaza `servidor` con la dirección IP o el nombre del servidor NFS.
- Reemplaza `/ruta/compartida` con la ruta en el servidor que deseas montar.
- Reemplaza `/ruta/de/montaje` con la ruta local donde deseas montar el sistema de archivos.

### Desmontar un sistema de archivos NFS

```shell
sudo umount /ruta/de/montaje
```

- Reemplaza `/ruta/de/montaje` con la ruta local del sistema de archivos que deseas desmontar.

### Ejemplo completo

**Montar un sistema de archivos NFS:**

```shell
sudo mount -t nfs 192.168.1.1:/compartido /mnt/nfs_share
```

**Desmontar un sistema de archivos NFS:**

```shell
sudo umount /mnt/nfs_share
```

Asegúrate de que el servidor NFS esté configurado correctamente y tenga las exportaciones adecuadas definidas en su archivo `/etc/exports`. Además, asegúrate de que el cliente NFS tenga los permisos necesarios para acceder al sistema de archivos remoto.

Ten en cuenta que estos comandos son para montar y desmontar sistemas de archivos NFS de forma manual. Si deseas que se monten automáticamente al inicio, deberías agregar las entradas correspondientes en el archivo `/etc/fstab`.

```
servidor:/ruta/compartida  /ruta/de/montaje  nfs   defaults  0 0
```

Ajusta las rutas y direcciones IP según tu configuración específica.

## Configure autofs

Autofs es una herramienta en sistemas Linux que permite montar sistemas de archivos automáticamente bajo demanda. Facilita la administración y montaje de sistemas de archivos de red de manera dinámica cuando se accede a ellos, en lugar de montarlos de forma estática.

**Ventajas de autofs:**

1. **Montaje bajo demanda:** autofs permite el montaje automático bajo demanda. Esto significa que los sistemas de archivos se montan solo cuando se accede a ellos, ahorrando recursos al no tener todos los sistemas de archivos montados constantemente.
2. **Recursos de red:** es especialmente útil para gestionar recursos de red, como comparticiones NFS (Network File System), donde los recursos pueden no estar siempre disponibles.
3. **Dinamismo:** autofs es dinámico y se puede configurar para manejar cambios en la disponibilidad de recursos de manera más flexible.

**Ventajas de /etc/fstab:**

1. **Estabilidad en el arranque:** si necesitas que un sistema de archivos esté disponible desde el inicio, `/etc/fstab` proporciona un enfoque más estático y estable, ya que los sistemas de archivos se montan automáticamente al arrancar el sistema.
2. **Configuración directa:** es más directo y sencillo de configurar. Puedes definir todos los puntos de montaje en un solo lugar (`/etc/fstab`) y asegurarte de que estén disponibles desde el inicio.
3. **Recursos locales:** es adecuado para sistemas de archivos locales o dispositivos que siempre están disponibles al inicio.

En resumen, la elección entre autofs y `/etc/fstab` depende de tus requisitos específicos. Si la estabilidad en el arranque y la simplicidad son prioritarias, `/etc/fstab` puede ser la mejor opción. Si estás gestionando recursos de red y prefieres el montaje bajo demanda, autofs ofrece mayor flexibilidad. Algunos entornos pueden incluso utilizar ambos métodos según la necesidad del escenario.

A continuación, una guía básica para configurar autofs en un sistema Linux:

### Paso 1: Instalar Autofs

Asegúrate de que el paquete autofs esté instalado en tu sistema. Puedes instalarlo con el siguiente comando según tu distribución.

Para sistemas basados en Red Hat/Fedora:

```shell
sudo yum install autofs
```

### Paso 2: Configurar Autofs

1. Edita el archivo de configuración principal de autofs, que generalmente se encuentra en `/etc/auto.master`. Puedes usar un editor de texto como `nano` o `vi`:

   ```shell
   sudo nano /etc/auto.master
   ```

2. Agrega una línea al final del archivo para configurar el montaje automático de un directorio específico. Por ejemplo:

   ```
   /mnt/auto    /etc/auto.nfs
   ```

   Esto indica que autofs buscará las configuraciones de montaje en el archivo `/etc/auto.nfs` cuando se acceda a `/mnt/auto`.

3. Crea el archivo `/etc/auto.nfs` para especificar las configuraciones de montaje. Puedes hacerlo con tu editor de texto preferido:

   ```shell
   sudo nano /etc/auto.nfs
   ```

4. Agrega líneas en este archivo para cada recurso que desees montar automáticamente. Por ejemplo, para un recurso NFS:

   ```
   my_nfs_share    -rw,soft,intr servidor:/compartido
   ```

   - `my_nfs_share`: es el nombre del directorio que se creará en `/mnt/auto`.
   - `rw,soft,intr`: son opciones de montaje (puedes ajustarlas según tus necesidades).
   - `servidor:/compartido`: es la ubicación del recurso remoto.

5. Guarda y cierra el archivo.

### Paso 3: Reiniciar Autofs

Reinicia el servicio autofs para que los cambios surtan efecto:

```shell
sudo systemctl restart autofs
```

### Paso 4: Acceder a los Recursos Automáticamente

Ahora, cuando accedas a `/mnt/auto/my_nfs_share` en tu sistema, autofs automáticamente montará el recurso NFS desde el servidor en el directorio especificado.

Este es solo un ejemplo básico. Puedes configurar autofs para montar diversos tipos de recursos, como sistemas de archivos NFS, CIFS, entre otros. Ajusta la configuración según tus necesidades específicas.

## Amplíe los volúmenes lógicos actuales

Para ampliar volúmenes lógicos en LVM (Logical Volume Manager), puedes seguir los pasos a continuación. Asegúrate de hacer copias de seguridad importantes antes de realizar operaciones que puedan afectar el almacenamiento.

### Paso 1: Verificar el Espacio Disponible

Antes de ampliar un volumen lógico, verifica si hay espacio no asignado en el grupo de volúmenes o si es necesario agregar un nuevo disco físico y extender el grupo de volúmenes.

```shell
# Verificar el espacio no asignado en el grupo de volúmenes
sudo vgdisplay

# Agregar un nuevo disco físico al grupo de volúmenes (si es necesario)
sudo vgextend nombre_del_grupo /dev/sdXn
```

### Paso 2: Ampliar el Volumen Lógico

Supongamos que tienes un volumen lógico llamado "mi_volumen" en el grupo de volúmenes "mi_grupo". A continuación, amplía el volumen lógico:

```shell
# Verificar el espacio actual del volumen lógico
sudo lvdisplay /dev/mi_grupo/mi_volumen

# Ampliar el volumen lógico (por ejemplo, en 10 GB)
sudo lvextend -L +10G /dev/mi_grupo/mi_volumen
```

### Paso 3: Ampliar el Sistema de Archivos

Si el volumen lógico contiene un sistema de archivos, debes ampliar el sistema de archivos para que ocupe el nuevo espacio. El método específico depende del tipo de sistema de archivos:

**Para ext4:**

```shell
# Ampliar el sistema de archivos ext4
sudo resize2fs /dev/mi_grupo/mi_volumen
```

**Para XFS:**

```shell
# Ampliar el sistema de archivos XFS
sudo xfs_growfs /mnt/punto_de_montaje  # Ajusta el punto de montaje según tu configuración
```

### Notas adicionales

- Asegúrate de ajustar los nombres de grupo y volumen según tu configuración real.
- El tamaño puede ajustarse según tus necesidades (`+10G` es solo un ejemplo).
- Si utilizas LVM Thin Provisioning, hay comandos específicos para ese caso.

## Cree y configure directorios con GID definido para la colaboración

Para crear y configurar directorios con un GID (Group ID) definido para la colaboración, puedes seguir estos pasos. Se asume que ya tienes un grupo existente con un GID específico. Si no es así, primero deberás crear un grupo utilizando el comando `sudo groupadd`.

### Paso 1: Crear un Nuevo Grupo (si es necesario)

```shell
sudo groupadd colaboradores
```

Este comando crea un nuevo grupo llamado "colaboradores". Puedes cambiar el nombre según tus preferencias.

### Paso 2: Crear un Directorio y Asignarle el GID

```shell
sudo mkdir /ruta/del/directorio
sudo chown :colaboradores /ruta/del/directorio
sudo chmod 2775 /ruta/del/directorio
```

- `sudo mkdir`: crea el directorio.
- `sudo chown :colaboradores`: asigna el grupo "colaboradores" al directorio.
- `sudo chmod 2775`: configura los permisos del directorio, donde el bit `2` establece el GID.

### Paso 3: Configurar Permisos

Para asegurar que los futuros archivos y directorios creados dentro de este directorio hereden el GID, puedes configurar los permisos de manera recursiva.

```shell
sudo chmod g+s /ruta/del/directorio
```

`g+s`: establece el bit "setgid" en el directorio. El bit "setgid" en un directorio significa que los nuevos archivos creados en ese directorio heredarán el grupo del directorio, en lugar del grupo primario del usuario que crea el archivo. Esto es útil en entornos donde varios usuarios necesitan trabajar en un conjunto común de archivos y se quiere garantizar que los archivos compartan un grupo común.

> **Nota:** la opción `g+s` establece el bit "setgid" en el directorio, lo que significa que los archivos creados en ese directorio heredarán el grupo del directorio en lugar del grupo primario del usuario que los creó. Esto se aplica a los subdirectorios también.
>
> En cambio, si usas `chmod 2775`, estás estableciendo permisos específicos, incluido el bit "setgid", pero esto no garantiza que los subdirectorios hereden automáticamente el grupo del directorio principal. Puedes tener que configurar permisos adicionales en subdirectorios según tus necesidades.

### Verificación de Configuración

Puedes verificar la configuración con el siguiente comando:

```shell
ls -ld /ruta/del/directorio
```

Deberías ver algo como:

```
drwxrwsr-x 2 usuario colaboradores 4096 Jan 20 12:34 /ruta/del/directorio
```

El "s" al final de los permisos indica que el GID está configurado.

Con estos pasos, has creado un directorio con un GID definido para la colaboración. Los archivos creados en este directorio heredarán automáticamente el GID, permitiendo una colaboración más efectiva entre los miembros del grupo.

## Diagnostique y corrija problemas de permisos de archivos

Diagnosticar y corregir problemas de permisos de archivos puede ser una tarea crucial para asegurar la integridad y la seguridad de un sistema. Aquí hay algunas pautas generales y pasos que puedes seguir para diagnosticar y corregir problemas de permisos:

### Diagnóstico de Problemas de Permisos

1. **Revisar permisos actuales:** utiliza el comando `ls` para verificar los permisos actuales de un archivo o directorio:

   ```shell
   ls -l archivo_o_directorio
   ```

   Esto mostrará los detalles de permisos, propietario y grupo.

2. **Verificar propietario y grupo:** asegúrate de que el propietario y el grupo sean los correctos. Puedes ajustarlos con `chown` si es necesario.

   ```shell
   sudo chown nuevo_propietario:nuevo_grupo archivo_o_directorio
   ```

3. **Comprobar configuración de [SELinux](https://access.redhat.com/documentation/es-es/red_hat_enterprise_linux/8/html/using_selinux/index):** si SELinux está habilitado, verifica si está causando problemas:

   ```shell
   getenforce
   ```

   Puedes ajustar los contextos de SELinux con `chcon` si es necesario.

   ```shell
   sudo chcon -Rv --type=nuevo_contexto archivo_o_directorio
   ```

### Corrección de Problemas de Permisos

1. **Cambiar permisos con `chmod`:** si los permisos actuales no son los correctos, puedes ajustarlos con el comando `chmod`:

   ```shell
   sudo chmod nuevos_permisos archivo_o_directorio
   ```

   Donde `nuevos_permisos` pueden ser expresados en notación octal (por ejemplo, 755) o simbólica (por ejemplo, u+rwx, g+rx, o-w).

2. **Cambiar propietario y grupo con `chown`:** si es necesario cambiar el propietario o el grupo:

   ```shell
   sudo chown nuevo_propietario:nuevo_grupo archivo_o_directorio
   ```

3. **Reparar contexto de SELinux:** si SELinux está causando problemas, puedes reparar el contexto con `restorecon`:

   ```shell
   sudo restorecon -Rv archivo_o_directorio
   ```

4. **Permisos predeterminados en nuevo archivo:** para cambiar los permisos predeterminados para nuevos archivos, puedes ajustar la máscara umask en el perfil de usuario.

   ```shell
   umask nuevo_valor
   ```

   Esto cambiará la máscara umask temporalmente. Para hacerlo permanente, puedes modificar la configuración en el archivo `.bashrc` o `.bash_profile` del usuario.

### Recomendaciones Finales

- **Realizar diagnóstico recurrente:** es buena práctica realizar diagnósticos periódicos de los permisos para identificar y abordar cualquier problema de manera proactiva.
- **Documentar cambios:** documenta cualquier cambio que realices en la configuración de permisos para futuras referencias.
- **Pruebas después de cambios:** después de realizar cambios, realiza pruebas para asegurarte de que los permisos se hayan configurado correctamente y que no afecten el funcionamiento normal del sistema.
- **Copia de seguridad:** antes de realizar cambios significativos, realiza copias de seguridad para evitar la pérdida accidental de datos.

Estas son pautas generales, y la aplicación exacta puede variar según el entorno y el sistema operativo específicos. Asegúrate de comprender el impacto de cualquier cambio que realices en los permisos.

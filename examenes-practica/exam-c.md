# Examen de práctica C

## 1. Configuración de repositorio desde ISO

1. Se inserta el disco de instalación.
2. Se crea el directorio `/repo`.
3. Se añade la siguiente línea al `/etc/fstab`. Revisar en qué unidad `dev` está el disco con `lsblk`:

```bash
/dev/sr0 /repo iso9660 defaults 0 0
```

Esta línea en el archivo `fstab` sirve para montar automáticamente una unidad de CD o DVD (como `sr0`) al sistema de archivos en el directorio `/repo`. Aquí la explicación de cada campo:

- **`/dev/sr0`**: Especifica el dispositivo que se montará, en este caso, el primer dispositivo óptico en el sistema (comúnmente un CD/DVD).
- **`/repo`**: Es el punto de montaje donde el contenido del CD o DVD estará disponible después de montarlo. Este directorio debe existir en el sistema para que el montaje se realice correctamente.
- **`iso9660`**: Es el tipo de sistema de archivos que se usa para CDs y DVDs. El sistema `iso9660` es un estándar para este tipo de medios y permite leer la estructura de archivos de un CD o DVD.
- **`defaults`**: Son opciones de montaje predeterminadas, que normalmente incluyen:
  - `rw`: Lectura y escritura (aunque en este caso el CD o DVD es de solo lectura).
  - `suid`: Permite el uso del bit `setuid` y `setgid`.
  - `dev`: Permite la interpretación de dispositivos especiales en el sistema de archivos.
  - `exec`: Permite la ejecución de binarios.
  - `auto`: Se montará automáticamente al arrancar.
  - `nouser`: Solo el superusuario puede montar.
  - `async`: Se realiza entrada y salida de manera asíncrona.
- **`0` (primer 0)**: Especifica si se debe incluir este sistema de archivos en el programa de respaldo `dump`. Un `0` indica que no se incluirá en el respaldo.
- **`0` (segundo 0)**: Indica el orden en que se debe hacer la verificación del sistema de archivos durante el arranque. En este caso, `0` significa que no se verificará.

### Propósito

Esta línea permite que el sistema monte el CD o DVD automáticamente en `/repo` si hay un disco presente en la unidad en el arranque, haciendo que su contenido esté disponible en este directorio.

4. Se ejecuta el comando `mount -a` para comprobar que se monta la unidad correctamente.
5. Se lanzan los siguientes comandos para añadir estos directorios al repositorio de software:

```bash
dnf config-manager --add-repo=file://repo/BaseOS
```

```bash
dnf config-manager --add-repo=file://repo/AppStream
```

Explicación de cada comando:

1. **`dnf config-manager --add-repo=file://repo/BaseOS`**: Este comando añade un repositorio llamado `BaseOS` usando la ruta `file://repo/BaseOS` como fuente. Aquí, `BaseOS` contiene los paquetes básicos del sistema operativo, incluyendo el núcleo y herramientas esenciales. Al ejecutarlo, el sistema agrega esta ruta local como un repositorio, permitiendo que `dnf` busque paquetes y actualizaciones en `BaseOS`.
2. **`dnf config-manager --add-repo=file://repo/AppStream`**: Este comando añade otro repositorio, `AppStream`, usando la ruta `file://repo/AppStream` como origen. `AppStream` contiene aplicaciones y paquetes adicionales que se pueden instalar y actualizar, dándole al usuario acceso a una variedad de software opcional. Al añadirlo de esta forma, `dnf` también buscará en este repositorio para la instalación de aplicaciones adicionales.

### Notas

Estos comandos son útiles cuando se tiene un repositorio montado en el sistema (por ejemplo, desde un DVD, un dispositivo de red o una imagen ISO montada) y se desea usarlo para la gestión de paquetes sin necesidad de acceso a Internet.

6. Se añade la siguiente línea a los ficheros generados del repositorio:

```bash
gpgcheck=0
```

7. Se deshabilitan todos los repositorios:

```bash
dnf config-manager --disable \*
```

8. Se habilitan los repositorios añadidos:

```bash
dnf config-manager --enable repo_BaseOS
```

```bash
dnf config-manager --enable repo_AppStream
```

9. Se genera la cache:

```bash
dnf makecache
```

10. Se comprueban los repositorios:

```bash
dnf repolist
```

## 2. Cambiar contraseña root a través del shell de GRUB

Pasos para realizar este ejercicio en AlmaLinux 9:

### Pasos para recuperar acceso root y cambiar la contraseña

1. **Reinicia el sistema y accede al menú GRUB**:
   - Reinicia la máquina virtual.
   - Tan pronto como aparezca el menú GRUB, presiona una tecla para detener el arranque automático.
2. **Edita las opciones de arranque**:
   - Selecciona la entrada que quieras iniciar (generalmente, la primera) y presiona la tecla **`e`** para editar las opciones de arranque.
3. **Modifica las opciones del kernel**:
   - Busca la línea que comienza con `linux` o `linuxefi`. Debería contener una ruta al kernel.
   - Al final de esa línea, añade la opción `rd.break`:

```text
linux /vmlinuz... ro crashkernel=auto rd.break
```

   - Esta opción interrumpirá el proceso de arranque y te llevará a un shell de emergencia.
4. **Arranca el sistema en modo de emergencia**:
   - Presiona **Ctrl + X** para iniciar el sistema con las opciones modificadas.
5. **Remonta el sistema de archivos como lectura/escritura**:
   - Una vez en el shell de emergencia, remonta el sistema de archivos raíz con permisos de escritura:

```bash
mount -o remount,rw /sysroot
```

6. **Cambia la raíz del sistema de archivos**:
   - Cambia al sistema de archivos raíz con `chroot`:

```bash
chroot /sysroot
```

7. **Cambia la contraseña del usuario root**:
   - Usa el comando `passwd` para establecer una nueva contraseña:

```bash
passwd
```

   - Introduce `mypassword` cuando se te solicite.
8. **Reetiqueta los contextos de SELinux**:
   - Para asegurarte de que SELinux funciona correctamente después del cambio, crea un archivo que indique la reetiquetación en el próximo arranque:

```bash
touch /.autorelabel
```

9. **Sal del entorno `chroot` y reinicia**:
   - Sal del entorno `chroot`:

```bash
exit
```

   - Reinicia el sistema:

```bash
reboot
```

10. **Verifica el acceso**:
    - Después de reiniciar, inicia sesión como `root` utilizando la nueva contraseña `mypassword`.

### Explicación de los pasos

- **`rd.break`**: Detiene el proceso de arranque antes de que el sistema monte completamente el sistema de archivos, lo que permite modificaciones.
- **`mount -o remount,rw /sysroot`**: Cambia el sistema de archivos raíz al modo de lectura/escritura.
- **`chroot /sysroot`**: Cambia la raíz del sistema de archivos al directorio `/sysroot`, simulando el entorno del sistema en ejecución.
- **`passwd`**: Cambia la contraseña del usuario root.
- **`touch /.autorelabel`**: Indica a SELinux que reetiquete todos los archivos en el próximo arranque, asegurando la coherencia de permisos.

Este proceso es esencial para recuperar el acceso root sin conocer la contraseña anterior.

## 3. Configurar la longitud mínima de la contraseña y cambios de la misma

Modificar el archivo `/etc/login.defs` con los parámetros correspondientes es un paso válido, pero en sistemas modernos como AlmaLinux 9 (y otros derivados de RHEL), no siempre es suficiente para aplicar las políticas de contraseñas de forma efectiva. Esto se debe a que el módulo **PAM (Pluggable Authentication Module)** es quien controla muchos aspectos relacionados con la autenticación y las políticas de contraseñas.

### Análisis de la configuración

1. **`PASS_MIN_DAYS 3`**: Esto establece que los usuarios deben esperar al menos 3 días antes de cambiar su contraseña nuevamente. Este parámetro es adecuado y cumple con el requisito.
2. **`PASS_MIN_LEN 6`**: Aunque este parámetro está presente en `/etc/login.defs`, no siempre se aplica directamente. En sistemas modernos, el requisito de longitud mínima de contraseña suele ser gestionado por el módulo `pam_pwquality.so` en PAM.

### Pasos adicionales para asegurar la configuración

**1. Verificar la configuración en `/etc/security/pwquality.conf`**

Este archivo controla las políticas de calidad de contraseñas, incluida la longitud mínima. Asegúrate de que el archivo tenga la configuración correcta (esto asegura que las contraseñas tengan al menos 6 caracteres):

```bash
minlen = 6
```

**2. Configurar PAM para respetar estas políticas**

Edita el archivo `/etc/pam.d/system-auth` o `/etc/pam.d/password-auth` y verifica que la línea con `pam_pwquality.so` esté configurada correctamente. Por ejemplo:

```text
password    requisite     pam_pwquality.so retry=3
```

- **`retry=3`**: Permite al usuario 3 intentos para introducir una contraseña que cumpla con las políticas.
- Asegúrate de que el archivo `pwquality.conf` esté siendo utilizado.

**3. Aplicar los cambios de `PASS_MIN_DAYS`**

Los parámetros de `/etc/login.defs` como `PASS_MIN_DAYS` afectan las configuraciones de contraseñas a nivel de usuario. Para que el parámetro se aplique a un usuario específico (por ejemplo, "user1"), usa el comando:

```bash
sudo chage --mindays 3 user1
```

### Conclusión

- La configuración en `/etc/login.defs` está bien y aplica globalmente a los usuarios creados después de modificar el archivo.
- Sin embargo, para garantizar que el requisito de longitud mínima de contraseñas sea efectivo, también debes configurar y verificar las políticas en **PAM** y **pwquality**. Esto asegura que las políticas de contraseñas sean aplicadas en todo el sistema.

## 4. Creación de grupos y usuario que pertenezcan a esos grupos

Comandos más eficientes para crear los usuarios con las configuraciones de grupo solicitadas.

1. **Crear los grupos** `sales` y `account`:

```bash
sudo groupadd sales
sudo groupadd account
```

2. **Crear los usuarios y asignarles los grupos secundarios** en un solo paso:
   - Para *linda* y *anna* con grupo secundario `sales`:

```bash
sudo useradd -G sales linda
sudo useradd -G sales anna
```

   - Para *serene* y *alex* con grupo secundario `account`:

```bash
sudo useradd -G account serene
sudo useradd -G account alex
```

### Verificación

Puedes verificar que los usuarios tienen las pertenencias de grupo correctas con el comando `groups`:

```bash
groups linda anna serene alex
```

O para ver la configuración de cada usuario:

```bash
id linda
id anna
id serene
id alex
```

Esto mostrará todos los grupos a los que cada usuario pertenece, incluyendo los secundarios `sales` y `account`.

## 5. Configuración de SSH

> **Atención:** esta subpágina está marcada en rojo en Notion (posible ejercicio incompleto o revisado con dudas).

Para configurar un servidor SSH que permita al usuario root conectarse y utilice el puerto 2022, sigue estos pasos cuidadosamente:

### 1. Editar la configuración de SSH

El archivo principal de configuración de SSH se encuentra en `/etc/ssh/sshd_config`.

1. Abre el archivo con un editor de texto:

```bash
sudo nano /etc/ssh/sshd_config
```

2. Busca y modifica los siguientes parámetros:
   - **Permitir el acceso SSH para el usuario root**: Encuentra la línea con `PermitRootLogin` y cámbiala a:

```text
PermitRootLogin yes
```

     Si esta línea no existe, añádela.
   - **Cambiar el puerto predeterminado a 2022**: Encuentra la línea con `#Port 22` (o similar) y cámbiala a:

```text
Port 2022
```

3. Guarda los cambios y cierra el archivo.

### 2. Asegurarte de que SELinux permite el nuevo puerto (si está habilitado)

Si estás en un sistema con **SELinux** habilitado (como RHEL, AlmaLinux o CentOS):

1. Agrega el puerto 2022 a la política de SSH:

```bash
sudo semanage port -a -t ssh_port_t -p tcp 2022
```

   Si el puerto ya está configurado, el comando puede fallar. Para verificar los puertos permitidos, usa:

```bash
sudo semanage port -l | grep ssh
```

2. Confirma que SELinux permite el uso del puerto.

### 3. Abrir el puerto en el firewall

1. Abre el puerto 2022 en **firewalld**:

```bash
sudo firewall-cmd --permanent --add-port=2022/tcp
sudo firewall-cmd --reload
```

2. Verifica que el puerto esté abierto:

```bash
sudo firewall-cmd --list-all
```

### 4. Reiniciar el servicio SSH

Reinicia el servicio SSH para aplicar los cambios:

```bash
sudo systemctl restart sshd
```

### 5. Verificar la configuración

1. Comprueba que el servicio SSH está escuchando en el puerto 2022:

```bash
sudo ss -tuln | grep 2022
```

2. Prueba conectarte al servidor:

```bash
ssh root@<server_ip> -p 2022
```

### Explicación de los pasos

1. **`PermitRootLogin yes`**: Permite que el usuario root se conecte vía SSH. Esto es útil para tareas de administración, pero debes usarlo con precaución.
2. **`Port 2022`**: Cambia el puerto predeterminado (22) al 2022, lo cual puede mejorar la seguridad al dificultar ataques automáticos.
3. **SELinux**: Si está habilitado, debe configurarse para aceptar el nuevo puerto.
4. **Firewall**: Abre el puerto 2022 en el firewall para permitir conexiones entrantes.
5. **Reinicio del servicio SSH**: Es necesario para aplicar los cambios en el archivo de configuración.

Con estos pasos, el servidor SSH estará configurado según los requisitos del ejercicio.

## 6. Ejercicio de ACLs

Para cumplir con los requisitos especificados, necesitas configurar directorios compartidos con grupos, permisos especiales y listas de control de acceso (ACL). A continuación se muestra cómo lograrlo paso a paso:

### 1. Crear los directorios y asignar los grupos

1. **Crear los directorios**:

```bash
sudo mkdir -p /groups/sales
sudo mkdir -p /groups/account
```

2. **Crear los grupos si no existen**:

```bash
sudo groupadd sales
sudo groupadd account
```

3. **Asignar los grupos a los directorios**:

```bash
sudo chgrp sales /groups/sales
sudo chgrp account /groups/account
```

### 2. Configurar permisos básicos

1. **Asignar permisos básicos a los grupos**:

```bash
sudo chmod 770 /groups/sales
sudo chmod 770 /groups/account
```

   - **`770`**: Los miembros del grupo tienen lectura, escritura y ejecución. Otros no tienen acceso.

2. **Configurar permisos para que los archivos creados hereden el grupo propietario**:

```bash
sudo chmod g+s /groups/sales
sudo chmod g+s /groups/account
```

   - **`g+s` (SetGID)**: Asegura que los archivos y subdirectorios creados en estos directorios hereden el grupo del directorio principal.

### 3. Configurar ACLs para los permisos adicionales

1. **Permitir a los miembros del grupo eliminar solo sus propios archivos**: no se necesitan configuraciones adicionales aquí; este comportamiento es predeterminado en sistemas Linux.
2. **Otorgar a Alex permisos especiales para eliminar archivos de todos los usuarios**:
   - **Permitir a Alex acceso total al directorio `sales`**:

```bash
sudo setfacl -m u:alex:rwx /groups/sales
```

   - **Configurar permisos por defecto para futuros archivos creados en `sales`**:

```bash
sudo setfacl -d -m u:alex:rwx /groups/sales
```

   - **Repetir para el directorio `account`**:

```bash
sudo setfacl -m u:alex:rwx /groups/account
sudo setfacl -d -m u:alex:rwx /groups/account
```

### 4. Verificar la configuración

1. **Verificar los permisos básicos**:

```bash
ls -ld /groups/sales /groups/account
```

2. **Verificar las ACLs**:

```bash
getfacl /groups/sales
getfacl /groups/account
```

### Explicación de los comandos

- **`groupadd <nombre>`**: Crea un grupo nuevo.
- **`chgrp <grupo> <directorio>`**: Cambia el grupo propietario de un directorio.
- **`chmod 770`**: Configura permisos de lectura, escritura y ejecución para el propietario y grupo, sin acceso para otros.
- **`chmod g+s`**: Habilita el bit SetGID para heredar el grupo.
- **`setfacl -m u:alex:rwx <directorio>`**: Asigna permisos específicos al usuario Alex para acceder y modificar los archivos.
- **`setfacl -d`**: Establece permisos por defecto para futuros archivos creados en el directorio.
- **`getfacl`**: Muestra las ACL configuradas en el directorio.

### Resultados finales

1. Los miembros del grupo `sales` tienen acceso total a `/groups/sales`. Lo mismo aplica para `account`.
2. Solo Alex puede eliminar los archivos de otros usuarios en ambos directorios.
3. Las configuraciones se heredan para nuevos archivos en cada directorio.

## 7. Ejercicio de LVM con tamaño de extensión en el VG

### Crear un grupo de volúmenes y configurar un volumen lógico

### 1. Crear un grupo de volúmenes de 4 GiB con un tamaño de extensión de 2 MiB

1. **Identificar los discos disponibles**:

```bash
lsblk
```

   Esto mostrará los dispositivos disponibles, como `/dev/sdb`.

2. **Crear una partición física en el disco** (opcional):

```bash
sudo fdisk /dev/sdX
```

   - Selecciona `n` para nueva partición.
   - Elige `primary` o `logical`.
   - Usa el tamaño completo del disco.

3. **Crear un volumen físico**:

```bash
sudo pvcreate /dev/sdX1
```

4. **Crear el grupo de volúmenes** con tamaño de extensión de 2 MiB:

```bash
sudo vgcreate --physicalextentsize 2M myvg /dev/sdX1
```

   - **`--physicalextentsize 2M`**: Define el tamaño de cada extensión física como 2 MiB.
   - **`myvg`**: Nombre del grupo de volúmenes.

### 2. Crear un volumen lógico de 1 GiB

1. **Crear el volumen lógico dentro del grupo de volúmenes**:

```bash
sudo lvcreate -L 1G -n myfiles myvg
```

   - **`-L 1G`**: Especifica el tamaño del volumen lógico (1 GiB).
   - **`-n myfiles`**: Asigna el nombre `myfiles` al volumen lógico.
   - **`myvg`**: Especifica el grupo de volúmenes en el que se creará.

### 3. Formatear el volumen lógico con Ext3

1. **Formatear el volumen lógico**:

```bash
sudo mkfs.ext3 /dev/myvg/myfiles
```

   - **`/dev/myvg/myfiles`**: Ruta del volumen lógico.

### 4. Crear el punto de montaje y montarlo

1. **Crear el directorio de montaje**:

```bash
sudo mkdir /myfiles
```

2. **Montar el volumen lógico**:

```bash
sudo mount /dev/myvg/myfiles /myfiles
```

3. **Verificar el montaje**:

```bash
df -h
```

### 5. Hacer el montaje persistente

1. **Añadir una entrada al archivo `/etc/fstab`**:

```bash
echo '/dev/myvg/myfiles /myfiles ext3 defaults 0 0' | sudo tee -a /etc/fstab
```

2. **Probar el montaje**:

```bash
sudo mount -a
```

### Explicación detallada de los comandos

- **`pvcreate`**: Inicializa un disco o partición para usarlo como un volumen físico en LVM.
- **`vgcreate`**: Crea un grupo de volúmenes, un contenedor que combina varios volúmenes físicos.
- **`--physicalextentsize`**: Define el tamaño de las extensiones físicas dentro del grupo (por defecto 4 MiB).
- **`lvcreate`**: Crea un volumen lógico dentro de un grupo de volúmenes.
- **`mkfs.ext3`**: Formatea el volumen lógico con el sistema de archivos Ext3.
- **`/etc/fstab`**: Archivo de configuración que hace persistente el montaje tras reinicios.

### Resultados esperados

1. Se ha creado un grupo de volúmenes llamado `myvg` de 4 GiB con extensiones de 2 MiB.
2. El volumen lógico `myfiles` de 1 GiB está formateado con Ext3 y montado en `/myfiles`.
3. El volumen lógico se monta automáticamente en cada reinicio.

## 8. Grupo con permisos de sudo

### Pasos para configurar el grupo `sysadmins` y habilitar privilegios de sudo

### 1. Crear el grupo `sysadmins`

```bash
sudo groupadd sysadmins
```

- **`groupadd`**: Crea un nuevo grupo en el sistema.
- **`sysadmins`**: Nombre del grupo.

### 2. Añadir los usuarios `linda` y `anna` al grupo `sysadmins`

```bash
sudo usermod -aG sysadmins linda
sudo usermod -aG sysadmins anna
```

- **`usermod`**: Modifica propiedades de usuarios.
- **`-aG`**: Añade el usuario al grupo indicado sin eliminar su pertenencia a otros grupos.
- **`sysadmins`**: Grupo al que se añade al usuario.

### 3. Verificar la pertenencia al grupo

```bash
groups linda
groups anna
```

Esto mostrará los grupos a los que pertenece cada usuario.

### 4. Configurar privilegios de sudo para el grupo `sysadmins`

Edita el archivo de configuración de sudoers con el siguiente comando:

```bash
sudo visudo
```

Agrega la siguiente línea al archivo (normalmente debajo de donde dice `# Allow members of group wheel...`):

```text
%sysadmins ALL=(ALL) ALL
```

- **`%sysadmins`**: Especifica que estamos configurando privilegios para el grupo `sysadmins`.
- **`ALL=(ALL)`**: Permite ejecutar comandos como cualquier usuario, incluido `root`.
- **`ALL`**: Permite ejecutar cualquier comando.

### 5. Verificar la configuración

Inicia sesión como `linda` o `anna` y verifica que pueden ejecutar comandos administrativos con sudo. Por ejemplo:

```bash
sudo whoami
```

Esto debería devolver `root`, confirmando que tienen privilegios de administración.

### Explicación de los pasos

1. **Grupo `sysadmins`**: Agrupa a los usuarios con privilegios administrativos.
2. **`usermod`**: Permite añadir usuarios a este grupo, dándoles permisos homogéneos.
3. **`sudo visudo`**: Garantiza que los cambios en la configuración de sudo se realizan correctamente, evitando errores de sintaxis.
4. **Configuración de sudo**: Asegura que los usuarios del grupo pueden ejecutar todos los comandos administrativos.

### Resultado esperado

Los usuarios `linda` y `anna` pertenecen al grupo `sysadmins` y pueden ejecutar comandos administrativos usando `sudo`.

## 9. Optimize your server with the appropriate profile that optimizes throughput

### Optimizing the Server for Throughput

To optimize a server for throughput on systems like AlmaLinux 9 or RHEL 9, you can use **tuned**, a performance tuning framework. Here's how to configure it:

### 1. Install `tuned` if not already installed

```bash
sudo dnf install -y tuned
```

- **`dnf install`**: Installs packages.
- **`-y`**: Automatically answers "yes" to prompts.
- **`tuned`**: The package that provides performance tuning profiles.

### 2. Enable and start the `tuned` service

```bash
sudo systemctl enable --now tuned
```

- **`enable`**: Configures the service to start on boot.
- **`--now`**: Starts the service immediately.

### 3. List available profiles

```bash
sudo tuned-adm list
```

- **`tuned-adm`**: The command-line tool for managing tuning profiles.
- **`list`**: Lists all available tuning profiles.
- Look for a profile like **`throughput-performance`**.

### 4. Apply the appropriate profile

```bash
sudo tuned-adm profile throughput-performance
```

- **`profile throughput-performance`**: Activates the profile optimized for high throughput.

### 5. Verify the active profile

```bash
sudo tuned-adm active
```

Confirms that the **`throughput-performance`** profile is active.

### Explanation of the Profile

The **`throughput-performance`** profile is designed for server workloads that prioritize throughput over latency. It includes optimizations such as:

- Disabling power-saving features for CPU.
- Tuning I/O elevators for higher throughput.
- Adjusting kernel parameters for network and disk performance.

### 6. Optional: Verify Kernel Parameters

After applying the profile, you can check the kernel parameters to ensure they have been applied:

```bash
sysctl -a | grep net
sysctl -a | grep vm
```

### Summary

- The `throughput-performance` profile optimizes the server for high throughput.
- The configuration is persistent across reboots.
- Use `tuned-adm` to manage and verify profiles efficiently.

### Result

The server is now optimized for throughput and ready to handle workloads like high-speed networking or disk I/O-intensive applications.

## 10. LVM partición para swap

### Adding a 10 GiB Disk and Configuring a 5 GiB Swap Volume Using LVM

### 1. Add a New 10 GiB Disk to Your Virtual Machine

1. Shut down your VM.
2. Add a new virtual disk of 10 GiB from the virtualization platform (e.g., VirtualBox or VMware).
3. Start the VM.

### 2. Identify the New Disk

After the VM starts, find the new disk:

```bash
lsblk
```

Look for a new disk, such as `/dev/sdb`. Ensure it's unpartitioned and not in use.

### 3. Create a Physical Volume

Initialize the new disk as an LVM physical volume:

```bash
sudo pvcreate /dev/sdb
```

- **`pvcreate`**: Prepares a block device for use by LVM.

### 4. Create a Volume Group

Create a volume group (VG) named `vg_swap`:

```bash
sudo vgcreate vg_swap /dev/sdb
```

- **`vgcreate`**: Creates a new volume group.
- **`vg_swap`**: Name of the volume group.

### 5. Create a Logical Volume

Create a 5 GiB logical volume (LV) for swap:

```bash
sudo lvcreate -L 5G -n lv_swap vg_swap
```

- **`lvcreate`**: Creates a logical volume.
- **`-L 5G`**: Specifies the size of the volume (5 GiB).
- **`-n lv_swap`**: Names the logical volume `lv_swap`.

### 6. Format the Logical Volume as Swap

```bash
sudo mkswap /dev/vg_swap/lv_swap
```

- **`mkswap`**: Configures a device for swap usage.

### 7. Enable the Swap Space

```bash
sudo swapon /dev/vg_swap/lv_swap
```

- **`swapon`**: Activates a swap device.

### 8. Make the Swap Persistent

Add the swap volume to `/etc/fstab`:

```bash
echo '/dev/vg_swap/lv_swap none swap defaults 0 0' | sudo tee -a /etc/fstab
```

- **`/etc/fstab`**: Configuration file for persistent mounts.
- **`none swap defaults`**: Mounts the device as swap with default options.

### 9. Verify the Configuration

1. Confirm that the swap is active:

```bash
swapon --show
```

2. Verify the entry in `/etc/fstab`:

```bash
cat /etc/fstab
```

### Summary

- **New 10 GiB Disk**: Added as `/dev/sdb`.
- **LVM Logical Volume**: Created a 5 GiB logical volume in `vg_swap`.
- **Swap**: Configured and activated as persistent swap space.

This setup ensures efficient use of the new disk for swap storage while leveraging LVM flexibility.

## 11. Crear subdirectorios

You can create multiple directories in a single command using **`mkdir`** with a loop. Here's the solution:

```bash
mkdir -p /users/user{1..5}
```

### Explanation:

1. **`mkdir`**: The command used to create directories.
2. **`-p`**:
   - Ensures that parent directories are created if they don't exist (in this case, `/users`).
   - Prevents errors if the parent directory already exists.
3. **`/users/user{1..5}`**:
   - **`{1..5}`**: Brace expansion generates the sequence `user1`, `user2`, ..., `user5`.
   - Combines the base path (`/users/`) with each expanded value.

### Verification:

```bash
ls -l /users
```

You should see the directories `user1`, `user2`, ..., `user5`.

## 12. Configuración de web server

> **Atención:** esta subpágina está marcada en rojo en Notion (posible ejercicio incompleto o revisado con dudas).

Here is how you can configure a web server (e.g., Apache HTTP server) with a nondefault document root `/webfiles`:

### Steps to Configure the Web Server

1. **Install the Apache HTTP Server**:

```bash
sudo dnf install -y httpd
```

2. **Create the Nondefault Document Root `/webfiles`**:

```bash
sudo mkdir -p /webfiles
```

3. **Create the `index.html` File**:

```bash
echo "hello world" | sudo tee /webfiles/index.html
```

4. **Adjust SELinux Contexts**: Since the new directory `/webfiles` is not the default document root, SELinux will block access. Update the SELinux context:

```bash
sudo semanage fcontext -a -t httpd_sys_content_t "/webfiles(/.*)?"
sudo restorecon -R /webfiles
```

5. **Update the Apache Configuration**:
   - Edit the Apache configuration file for the default site.

```bash
sudo nano /etc/httpd/conf.d/webfiles.conf
```

   - Add the following configuration:

```text
<VirtualHost *:80>
    DocumentRoot "/webfiles"
    <Directory "/webfiles">
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
```

6. **Adjust Firewall Rules**: Ensure that HTTP traffic is allowed through the firewall:

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
```

7. **Start and Enable the Apache Service**:

```bash
sudo systemctl enable --now httpd
```

8. **Test the Configuration**: Open a web browser and navigate to the server's IP or hostname (e.g., `http://<server-ip>`). You should see the text **hello world**. Alternatively, use `curl` to test:

```bash
curl http://localhost
```

### Explanation of Key Commands:

- **`dnf install -y httpd`**: Installs the Apache web server.
- **`mkdir -p /webfiles`**: Creates the directory `/webfiles` for the nondefault document root.
- **`semanage fcontext`**: Sets the SELinux context for `/webfiles` and its subdirectories.
- **`restorecon -R /webfiles`**: Applies the SELinux context to the directory recursively.
- **Apache Configuration**:
  - **`DocumentRoot`**: Sets the location of the root directory for web documents.
  - **`Require all granted`**: Allows access to all clients.
- **`firewall-cmd`**: Opens the firewall for HTTP traffic.
- **`curl`**: Tests the HTTP response from the server.

## 13. Contenedor Podman MariaDB

> **Atención:** esta subpágina está marcada en rojo en Notion (posible ejercicio incompleto o revisado con dudas).

### 1. Pull del contenedor MariaDB

```bash
podman pull docker.io/library/mariadb
```

Correcto, el comando descarga la imagen oficial de MariaDB desde Docker Hub. Es una buena práctica utilizar la imagen oficial, ya que es mantenida y actualizada regularmente.

### 2. Creación del directorio para almacenamiento persistente

```bash
sudo mkdir -p /var/mariadb-container
```

Perfecto. Es importante crear un directorio en el host para almacenar de manera persistente los datos de la base de datos.

### 3. Configuración del contexto de SELinux

```bash
sudo semanage fcontext -a -t container_file_t "/var/mariadb-container(/.*)?"
sudo restorecon -Rv /var/mariadb-container
sudo semanage port -a -t container_port_t -p tcp 3306
```

- **SELinux**: El comando `semanage` ajusta el contexto de SELinux para permitir que el contenedor acceda correctamente a los archivos en `/var/mariadb-container`. Luego, `restorecon` aplica este contexto de manera recursiva.
- **Recomendación**: Esta es una buena práctica, pero asegúrate de tener el paquete `policycoreutils-python-utils` instalado para usar `semanage`.

Si el puerto ya está configurado, aparecerá un error. En ese caso, puedes verificarlo con:

```bash
sudo semanage port -l | grep 3306
```

> **Nota:** Si no sabes qué tipo de etiqueta o configuración usar, puedes buscar configuraciones relacionadas con contenedores o cualquier otra categoría mediante los comandos:
>
> - **Listar todas las etiquetas de archivo disponibles**:
>
> ```bash
> sudo semanage fcontext -l | grep container
> ```
>
> Esto te mostrará etiquetas como `container_file_t`, `container_var_lib_t`, etc.
>
> - **Listar todos los puertos configurados para contenedores**:
>
> ```bash
> sudo semanage port -l | grep container
> ```
>
> Esto ayuda a identificar qué puertos están permitidos para contenedores.
>
> - **Ver detalles de otras configuraciones de SELinux para contenedores**:
>
> ```bash
> sudo semanage boolean -l | grep container
> ```

### Configuración del firewall para permitir tráfico de MariaDB

```bash
sudo firewall-cmd --permanent --add-port=3306/tcp
sudo firewall-cmd --reload
```

### Ejecución del contenedor con configuración adecuada

```bash
podman run -d \
  --name mariadb-container \
  -p 3306:3306 \
  -v /var/mariadb-container:/var/lib/mysql:Z \
  -e MARIADB_ROOT_PASSWORD=your_secure_root_password \
  mariadb
```

- **`-v /var/mariadb-container:/var/lib/mysql:Z`**: El uso de `:Z` es clave, ya que le indica a SELinux que cambie el contexto de seguridad de los archivos montados para que el contenedor pueda acceder a ellos.

**Hacer que el contenedor se inicie automáticamente al arrancar el sistema:** genera un archivo `systemd` para el contenedor:

```bash
sudo podman generate systemd --name mariadb-container --restart-policy always > /etc/systemd/system/container-mariadb-container.service
sudo systemctl enable --now container-mariadb-container.service
```

## 14. Systemd user container

> **Atención:** esta subpágina está marcada en rojo en Notion (posible ejercicio incompleto o revisado con dudas).

### Ejercicio 16: "Configure your system such that the container is automatically started as a Systemd user container"

### Lo que solicita:

- **Reutilizar** el contenedor creado en el ejercicio 15.
- Configurarlo para que se ejecute como un **Systemd user container**, es decir, gestionado a nivel de usuario, no del sistema global.
- El contenedor debe iniciarse automáticamente cuando el usuario inicia sesión.

### Cómo realizarlo:

### Ejecución del contenedor con configuración adecuada

```bash
sudo podman run -d --name mariadb-student \
    -e MYSQL_ROOT_PASSWORD=root_password \
    -v /var/mariadb-container:/var/lib/mysql:Z \
    -p 3307:3306 \
    mariadb
```

- **`-v /var/mariadb-container:/var/lib/mysql:Z`**: El uso de `:Z` es clave, ya que le indica a SELinux que cambie el contexto de seguridad de los archivos montados para que el contenedor pueda acceder a ellos.

1. **Reconfigurar el contenedor como un servicio de usuario:** a diferencia del ejercicio 15, aquí no necesitas un archivo `systemd` global en `/etc/systemd/system`. Generarás un archivo para el usuario:

```bash
podman generate systemd --name mariadb-student --files
```

> **Nota:** Este archivo se guarda en el directorio de configuración del usuario (`~/.config/systemd/user/`), lo que hace que sea un servicio administrado por el usuario. Si la ruta no está creada habría que crearla.

2. **Habilitar el servicio del usuario:** habilita y arranca el servicio como usuario:

```bash
systemctl --user enable --now container-mariadb-container.service
```

   Esto asegura que el contenedor se inicie automáticamente cada vez que el usuario inicia sesión.

3. **Permitir servicios de usuario persistentes:** asegúrate de habilitar **lingering** para el usuario si deseas que el contenedor funcione incluso sin una sesión activa:

```bash
sudo loginctl enable-linger $USER
```

### Resumen de diferencias:

| Aspecto | Ejercicio 15 | Ejercicio 16 |
|---|---|---|
| **Alcance del servicio** | Servicio gestionado por el sistema (global) | Servicio gestionado por el usuario (local) |
| **Archivo systemd generado** | `/etc/systemd/system/` | `~/.config/systemd/user/` |
| **Inicio automático** | Al iniciar el sistema | Al iniciar sesión del usuario (o con lingering) |
| **Quién lo gestiona** | `systemctl` (global) | `systemctl --user` (usuario) |

Ambos ejercicios solicitan que el contenedor se ejecute automáticamente, pero el **alcance** de la configuración es lo que marca la diferencia.

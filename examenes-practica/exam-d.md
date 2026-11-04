# Examen de práctica D

## 1. Configuración de repositorio desde ISO (versión marcada como incorrecta)

> **Nota:** el título original de esta página en Notion es "Configuración de repositorio desde ISO ESTÁ MAL", indicando que esta versión del ejercicio contiene un error respecto a la versión correcta (ver Examen C, ejercicio 1). Se conserva tal cual para referencia de estudio.

1. Se inserta el disco de instalación.
2. Se el directorio `/repo`. *(sic — en el original falta el verbo "crea"; el paso correcto es crear el directorio `/repo`)*
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
dnf config-manager --add-repo=file:///repo/BaseOS
```

```bash
dnf config-manager --add-repo=file:///repo/AppStream
```

Explicación de cada comando:

1. **`dnf config-manager --add-repo=file:///repo/BaseOS`**: Este comando añade un repositorio llamado `BaseOS` usando la ruta `file:///repo/BaseOS` como fuente. Aquí, `BaseOS` contiene los paquetes básicos del sistema operativo, incluyendo el núcleo y herramientas esenciales. Al ejecutarlo, el sistema agrega esta ruta local como un repositorio, permitiendo que `dnf` busque paquetes y actualizaciones en `BaseOS`.
2. **`dnf config-manager --add-repo=file:///repo/AppStream`**: Este comando añade otro repositorio, `AppStream`, usando la ruta `file:///repo/AppStream` como origen. `AppStream` contiene aplicaciones y paquetes adicionales que se pueden instalar y actualizar, dándole al usuario acceso a una variedad de software opcional. Al añadirlo de esta forma, `dnf` también buscará en este repositorio para la instalación de aplicaciones adicionales.

### Notas

Estos comandos son útiles cuando se tiene un repositorio montado en el sistema (por ejemplo, desde un DVD, un dispositivo de red o una imagen ISO montada) y se desea usarlo para la gestión de paquetes sin necesidad de acceso a Internet.

6. Se añade la siguiente línea a los ficheros generados del repositorio:

```bash
gpgcheck=0
```

## 2. Crear partición y montarla con un label indicado

Pasos para completar el ejercicio de creación, formateo y montaje persistente de una partición en el segundo disco:

### Paso 1: Crear la Partición de 500 MiB en el Segundo Disco

1. **Listar los discos disponibles** para identificar el nombre de tu segundo disco:

```bash
lsblk
```

   Supongamos que tu segundo disco es `/dev/sdb`.

2. **Iniciar `fdisk`** en el disco para crear una nueva partición:

```bash
sudo fdisk /dev/sdb
```

3. Dentro de `fdisk`:
   - Pulsa `n` para crear una nueva partición.
   - Elige `p` para una partición primaria (o `e` si prefieres una extendida).
   - Selecciona el número de la partición (generalmente `1` si es la primera en ese disco).
   - Especifica el tamaño de 500 MiB usando `+500M`.
   - Pulsa `w` para escribir los cambios y salir.

### Paso 2: Formatear la Partición con el Sistema de Archivos Ext4

Formatea la nueva partición en Ext4 y establece el label `mydata`:

```bash
sudo mkfs.ext4 -L mydata /dev/sdb1
```

*Nota*: Ajusta `/dev/sdb1` si el nombre de la partición es distinto.

### Paso 3: Crear el Punto de Montaje

```bash
sudo mkdir -p /mydata
```

### Paso 4: Configurar el Montaje Persistente en `/etc/fstab`

1. **Obtén el UUID** de la partición recién creada:

```bash
sudo blkid /dev/sdb1
```

2. **Edita el archivo `/etc/fstab`**:

```bash
sudo nano /etc/fstab
```

3. Agrega la siguiente línea al final del archivo, usando el UUID que obtuviste en el paso anterior:

```text
UUID=<UUID_DE_LA_PARTICION> /mydata ext4 defaults 0 0
```

   O también puedes usar el label:

```text
LABEL=mydata /mydata ext4 defaults 0 0
```

### Paso 5: Montar la Partición

Aplica la configuración de `/etc/fstab` para montar la partición sin reiniciar:

```bash
sudo mount -a
```

### Verificación

Confirma que la partición está montada correctamente en `/mydata`:

```bash
df -h /mydata
```

## 3. Configuración de parámetros de contraseñas para nuevos usuarios

Para establecer valores predeterminados para usuarios nuevos y configurar advertencias y caducidad de contraseñas en el sistema, puedes editar las políticas de contraseña en el archivo `/etc/login.defs` y usar el comando `chage` para ajustar configuraciones predeterminadas para usuarios.

### Paso 1: Configurar la Advertencia y la Caducidad de Contraseña en `/etc/login.defs`

1. Abre el archivo `/etc/login.defs` con un editor de texto:

```bash
sudo nano /etc/login.defs
```

2. Busca y ajusta los siguientes parámetros (o añádelos si no existen):

```text
PASS_MAX_DAYS 120
PASS_WARN_AGE 3
```

   - `PASS_MAX_DAYS` define el número máximo de días que una contraseña es válida antes de requerir un cambio. En este caso, lo establecemos en 120 días.
   - `PASS_WARN_AGE` define el número de días de advertencia antes de que expire la contraseña, en este caso, 3 días.

3. Guarda los cambios y cierra el editor.

### Paso 2: Aplicar los Cambios a Nuevos Usuarios con `useradd`

Con los cambios en `/etc/login.defs`, los valores predeterminados de advertencia y caducidad se aplicarán automáticamente a los usuarios nuevos.

### Paso 3: Verificar las Configuraciones Predeterminadas con un Usuario Nuevo

Para verificar que los valores predeterminados se apliquen correctamente, puedes crear un usuario nuevo y revisar su configuración de advertencia y caducidad de contraseña con `chage`:

```bash
sudo useradd usuario_prueba
sudo chage -l usuario_prueba
```

Esto te mostrará la información de caducidad y advertencia de la contraseña para el usuario.

## 4. Creación de usuario con UID y grupo concreto

Para crear los usuarios `lori` y `laura` con los UIDs específicos y agregar ambos al grupo `sales` como grupo secundario, puedes usar estos comandos:

1. **Crear el grupo secundario `sales`** (si no existe):

```bash
sudo groupadd sales
```

2. **Crear el usuario `lori` con UID 2000 y añadirlo al grupo `sales`**:

```bash
sudo useradd -u 2000 -G sales lori
```

3. **Crear el usuario `laura` con UID 2001 y añadirlo al grupo `sales`**:

```bash
sudo useradd -u 2001 -G sales laura
```

4. **Verificar los usuarios y grupos** para confirmar:

```bash
id lori
id laura
```

Esto mostrará los detalles de los usuarios `lori` y `laura`, incluyendo sus UIDs y los grupos a los que pertenecen.

## 5. Ejercicio de ACLs

Pasos para crear y configurar los directorios compartidos `/groups/sales` y `/groups/data` según los requisitos:

### 1. Crear los grupos `sales` y `data`

Si no existen aún, creamos los grupos:

```bash
sudo groupadd sales
sudo groupadd data
```

### 2. Crear los directorios compartidos

Creamos los directorios y asignamos el grupo propietario adecuado a cada uno:

```bash
sudo mkdir -p /groups/sales
sudo mkdir -p /groups/data
sudo chgrp sales /groups/sales
sudo chgrp data /groups/data
```

### 3. Establecer permisos de acceso completos para cada grupo

Para que los miembros de cada grupo tengan permisos completos en su respectivo directorio, usamos `chmod` con `rwx` y el bit SGID (`s`) para que los archivos creados en el directorio hereden el grupo:

```bash
sudo chmod 2770 /groups/sales
sudo chmod 2770 /groups/data
```

- **Explicación:** El modo `2770` establece permisos `rwxrws---` (el `2` inicial es el bit SGID; con `770` a secas queda `rwxrwx---` y los ficheros nuevos no heredan el grupo), donde:
  - `rwx` da acceso completo al propietario.
  - `rws` permite que los miembros del grupo tengan acceso completo y asegura que los archivos y directorios creados hereden el grupo.
  - `---` evita el acceso para los demás.

### 4. Asignar permisos especiales para el usuario `alex`

Queremos que `alex` tenga permisos de lectura en ambos directorios y pueda borrar archivos. Usamos `setfacl` (Access Control Lists) para configurar permisos adicionales:

```bash
# Directorio: rwx (r para listar, x para entrar, w+x para poder borrar ficheros dentro)
sudo setfacl -m u:alex:rwx /groups/sales
sudo setfacl -m u:alex:rwx /groups/data

# Ficheros que se creen a partir de ahora: alex solo los lee
sudo setfacl -d -m u:alex:r /groups/sales
sudo setfacl -d -m u:alex:r /groups/data

# Ficheros que ya existen: lectura (X mayúscula = x solo en subdirectorios)
sudo setfacl -R -m u:alex:rX /groups/sales /groups/data
```

- **Explicación:**
  - `setfacl -m u:alex:rwx` permite acceso total a `alex` para que pueda leer, escribir y borrar archivos en ambos directorios.
  - `setfacl -d -m u:alex:r` establece que, por defecto, `alex` tenga solo permisos de lectura en archivos nuevos, evitando escritura en archivos futuros.
  - Borrar un fichero depende de los permisos del **directorio** (`w` y `x`), no de los del fichero: con solo `r` sobre el directorio, alex no podría ni entrar ni borrar. La contrapartida es que con `w` también puede crear ficheros; no se puede dar «borrar» sin dar «escribir en el directorio».

### Verificación

```bash
getfacl /groups/sales
getfacl /groups/data
```

## 6. Crear partición swap

### Crear una partición swap de 1 GiB y montarla de forma persistente

### Pasos y comandos:

1. **Crea una nueva partición de 1 GiB**:

```bash
sudo fdisk /dev/sdX
```

   - Inicia `fdisk` en el disco donde deseas crear la partición (reemplaza `/dev/sdX` con tu dispositivo).
   - Dentro de `fdisk`, usa los comandos:
     - `n`: para crear una nueva partición.
     - `t`: para cambiar el tipo de partición (usa el código `82` para swap).
     - `w`: para escribir los cambios.

2. **Formatea la partición como swap**:

```bash
sudo mkswap /dev/sdX1
```

   Formatea la partición creada (ajusta `/dev/sdX1` según la partición generada).

3. **Activa la partición swap**:

```bash
sudo swapon /dev/sdX1
```

4. **Agrega la entrada a `/etc/fstab`** para montarla automáticamente:

```bash
echo '/dev/sdX1 none swap defaults 0 0' | sudo tee -a /etc/fstab
```

## 7. Encontrar todos los archivos que tienen el SUID activado

### Encontrar todos los archivos con el permiso SUID y escribir el resultado en `/root/suidfiles`

### Comandos:

1. **Buscar archivos con SUID**:

```bash
sudo find / -perm /4000 > /root/suidfiles
```

   - `find /`: busca desde la raíz del sistema.
   - `-perm /4000`: encuentra archivos con el bit SUID activado.
   - `> /root/suidfiles`: redirige la salida al archivo `/root/suidfiles`.

## 8. Crear VG de 1GB y LV de 512 de swap

### Crear un grupo de volúmenes LVM de 1 GiB, un volumen swap de 512 MiB y montarlo de forma persistente

### Pasos y comandos:

1. **Crear una partición de 1 GiB** en un disco libre y establecer su tipo como `8e` (LVM) usando `fdisk`.
2. **Crear un grupo de volúmenes LVM**:

```bash
sudo vgcreate myvg /dev/sdX1
```

3. **Crear un volumen lógico swap** de 512 MiB:

```bash
sudo lvcreate -L 512M -n swapvol myvg
```

4. **Formatear y activar el volumen como swap**:

```bash
sudo mkswap /dev/myvg/swapvol
sudo swapon /dev/myvg/swapvol
```

5. **Añadir a `/etc/fstab`**:

```bash
echo '/dev/myvg/swapvol none swap defaults 0 0' | sudo tee -a /etc/fstab
```

## 9. Crear un pool y volumen Stratis

> **Atención:** esta subpágina está marcada en rojo en Notion (posible ejercicio incompleto o revisado con dudas).

Pasos para crear un **pool y volumen Stratis** en un disco de 10 GiB, y montarlo de forma persistente en `/stratis`, omitiendo la parte de añadir la unidad de almacenamiento.

### Pasos para configurar el Stratis pool y volumen

1. **Instalar las herramientas necesarias para Stratis:** asegúrate de que el sistema tenga instalados los paquetes necesarios:

```bash
sudo dnf install stratis-cli stratisd -y
```

2. **Habilitar y arrancar el servicio Stratis:**

```bash
sudo systemctl enable --now stratisd
```

3. **Identificar el nuevo disco:** encuentra el nombre del dispositivo del disco de 10 GiB (por ejemplo, `/dev/vdb`):

```bash
lsblk
```

4. **Crear el Stratis pool:** usa el dispositivo identificado para crear el **pool** llamado `stratispool`:

```bash
sudo stratis pool create stratispool /dev/vdb
```

5. **Crear el volumen Stratis:** dentro del **pool**, crea un volumen llamado `stratisvol`:

```bash
sudo stratis filesystem create stratispool stratisvol
```

6. **Montar el volumen en `/stratis`:**
   - Crea el directorio de montaje:

```bash
sudo mkdir -p /stratis
```

   - Obtén el identificador del dispositivo Stratis (se muestra como `/stratis/stratispool/stratisvol`):

```bash
sudo lsblk -o NAME,MOUNTPOINT,FSTYPE,SIZE
```

   - Añade el volumen a `/etc/fstab` para que se monte automáticamente al iniciar el sistema:

```bash
echo "/dev/stratis/stratispool/stratisvol /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0" | sudo tee -a /etc/fstab
# sin x-systemd.requires, en el arranque el sistema de ficheros puede no existir aún y se entra en emergencia
```

   - Monta el volumen de inmediato:

```bash
sudo mount -a
```

7. **Verificar el montaje y estado del Stratis:**
   - Verifica que el volumen está montado correctamente:

```bash
df -h | grep /stratis
```

   - Comprueba el estado del Stratis pool y volumen:

```bash
sudo stratis pool list
sudo stratis filesystem list
```

### Notas importantes

1. **Sistema de archivos:** Stratis utiliza XFS por defecto, por lo que no necesitas formatear manualmente el volumen.
2. **Espacio dinámico:** El tamaño del volumen es dinámico y crece según sea necesario, dentro del límite del espacio disponible en el pool.

## 10. Instalación y cambio de puerto en httpd

Cómo instalar y configurar un servidor HTTP para que escuche en el puerto 8080, añadiendo el puerto al contexto de SELinux y permitiéndolo en **firewalld**.

### Pasos a seguir

1. **Instalar el servidor HTTP:**

```bash
sudo dnf install httpd -y
```

2. **Configurar el servidor para escuchar en el puerto 8080:**
   - Edita el archivo de configuración de Apache:

```bash
sudo vi /etc/httpd/conf/httpd.conf
```

   - Busca la línea que especifica el puerto (normalmente `Listen 80`) y cámbiala a:

```text
Listen 8080
```

3. **Configurar SELinux para permitir el puerto 8080:**
   - Verifica los puertos permitidos para el servicio HTTP:

```bash
sudo semanage port -l | grep http_port_t
```

   - Añade el puerto 8080 al contexto `http_port_t`:

```bash
sudo semanage port -a -t http_port_t -p tcp 8080 || sudo semanage port -m -t http_port_t -p tcp 8080   # el 8080 ya está definido
```

4. **Permitir el puerto 8080 en firewalld:**
   - Abre el puerto 8080 de manera permanente:

```bash
sudo firewall-cmd --permanent --add-port=8080/tcp
```

   - Recarga la configuración del firewall para aplicar los cambios:

```bash
sudo firewall-cmd --reload
```

5. **Habilitar y arrancar el servicio HTTP:**
   - Activa el servicio para que se inicie automáticamente al arrancar el sistema:

```bash
sudo systemctl enable --now httpd
```

   - Verifica que el servicio está en ejecución:

```bash
sudo systemctl status httpd
```

6. **Probar el servidor web:**
   - Usa `curl` para verificar que el servidor está escuchando en el puerto 8080:

```bash
curl http://localhost:8080
```

   - También puedes probar desde otro sistema usando la dirección IP del servidor.

### Notas adicionales

- **SELinux:** Si el comando `semanage` no está disponible, instala el paquete necesario:

```bash
sudo dnf install policycoreutils-python-utils -y
```

- **Firewalld:** Si necesitas permitir el servicio HTTP en lugar de un puerto específico, usa:

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
```

## 11. Proporcionar el comando sudo a un usuario

Agregar al usuario al grupo wheel:

```bash
usermod -aG wheel laura
```

## 12. Crear un directorio `/users` y exportarlo mediante NFS

> **Atención:** esta subpágina está marcada en rojo en Notion (posible ejercicio incompleto o revisado con dudas).

### Ejercicio 14: Exportar directorios con NFS

1. **Crear la estructura de directorios**:

```bash
sudo mkdir -p /users/linda /users/anna
```

2. **Ajustar permisos para los directorios:** asegúrate de que sean accesibles para usuarios:

```bash
sudo chmod -R 755 /users
```

3. **Instalar NFS Server:** si NFS no está instalado:

```bash
sudo dnf install -y nfs-utils
```

4. **Configurar la exportación en `/etc/exports`:** añade las siguientes líneas:

```text
/users/linda *(rw,sync,no_root_squash)
/users/anna *(rw,sync,no_root_squash)
```

5. **Habilitar y reiniciar el servicio NFS**:

```bash
sudo systemctl enable --now nfs-server
sudo exportfs -rav
```

6. **Verificar que las exportaciones estén activas**:

```bash
sudo exportfs -v
```

### Firewall

NFS utiliza varios puertos para su funcionamiento, que deben estar abiertos en el firewall:

```bash
sudo firewall-cmd --permanent --add-service=nfs
sudo firewall-cmd --permanent --add-service=rpc-bind
sudo firewall-cmd --permanent --add-service=mountd
sudo firewall-cmd --reload
```

### SELinux

SELinux debe estar configurado para permitir las exportaciones NFS:

1. Habilita el contexto necesario para los directorios exportados:

```bash
sudo semanage fcontext -a -t nfs_t "/users(/.*)?"
sudo restorecon -Rv /users
```

2. Permite que el servidor NFS funcione con SELinux habilitado:

```bash
sudo setsebool -P nfs_export_all_rw on
```

## 13. Crear usuarios y configurar `autofs` para montar automáticamente las carpetas NFS

### Ejercicio 15: Configurar usuarios y Autofs

1. **Crear usuarios y sus directorios de inicio**:

```bash
sudo useradd -m -d /home/users/linda linda
sudo useradd -m -d /home/users/anna anna
```

2. **Editar la configuración de Autofs**:
   - **Instalar Autofs**:

```bash
sudo dnf install -y autofs
```

   - **Editar el archivo `/etc/auto.master`:** añade esta línea al final:

```text
/home/users /etc/auto.users
```

   - **Crear `/etc/auto.users`:** especifica los puntos de montaje NFS para cada usuario:

```text
linda -rw,soft nfs-server:/users/linda
anna  -rw,soft nfs-server:/users/anna
```

     Reemplaza `nfs-server` con el hostname o IP del servidor NFS.

3. **Reiniciar el servicio Autofs**:

```bash
sudo systemctl enable --now autofs
```

4. **Probar la configuración:** navega a `/home/users/linda` o `/home/users/anna`. Los directorios deben montarse automáticamente mediante NFS.

### Notas Importantes

- Asegúrate de que el cliente tenga permisos en el servidor NFS para montar los directorios.
- Verifica conectividad entre el cliente y el servidor NFS.
- Usa el comando `showmount -e nfs-server` en el cliente para confirmar las exportaciones disponibles.

### Firewall

Si el cliente NFS se encuentra en una máquina diferente al servidor:

1. Asegúrate de que los puertos NFS estén abiertos en el servidor (ya cubiertos en el Ejercicio 14).
2. En el cliente, no es necesario modificar el firewall para Autofs, ya que simplemente monta los puntos NFS disponibles.

### SELinux

Si SELinux está habilitado en el cliente, es necesario asegurarse de que pueda montar NFS:

1. Permite el montaje de NFS en el cliente:

```bash
sudo setsebool -P use_nfs_home_dirs on
```

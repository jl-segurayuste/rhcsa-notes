# Configurar servidor FTP

Este ejercicio está dirigido a instalar y configurar un servidor FTP (vsftpd) en un sistema Linux para permitir descargas anónimas desde el directorio `/var/ftp/pub`. Vamos a desglosar los pasos y su explicación.

## Pasos y explicación detallada

### 1. Configuración del repositorio local

```bash
cd /etc/yum.repos.d
vim local.repo
```

En este paso, se accede al directorio donde se encuentran los archivos de configuración de repositorios de YUM (`/etc/yum.repos.d`) y se crea un nuevo archivo de repositorio llamado `local.repo`.

```
[local]
name=local.repo
baseurl=file:///mnt
enabled=1
gpgcheck=0
```

- `name=local.repo`: Nombre descriptivo del repositorio.
- `baseurl=file:///mnt`: Especifica la ubicación del repositorio local en el sistema de archivos (`/mnt`). Esto indica a YUM que busque los paquetes a instalar en el directorio `/mnt`.
- `enabled=1`: Habilita este repositorio para que YUM lo utilice.
- `gpgcheck=0`: Desactiva la verificación GPG para este repositorio (útil en entornos de prueba).

### 2. Actualizar el cache de YUM

```bash
yum makecache
```

Este comando actualiza la caché de metadatos de YUM para el nuevo repositorio configurado (`local.repo`). Esto asegura que YUM tenga acceso y conozca los paquetes disponibles en el repositorio local.

### 3. Instalación de vsftpd

```bash
yum install -y vsftpd
```

Se utiliza YUM para instalar el paquete `vsftpd`, que es un servidor FTP muy seguro y ampliamente utilizado en entornos Linux.

### 4. Reinicio del servicio vsftpd

```bash
service vsftpd restart
```

Este comando reinicia el servicio vsftpd para aplicar la configuración recién instalada.

### 5. Configuración de inicio automático

```bash
chkconfig vsftpd on
```

Habilita el servicio vsftpd para que se inicie automáticamente en el arranque del sistema.

### 6. Verificación del estado de vsftpd

```bash
chkconfig --list vsftpd
```

Muestra el estado de inicio del servicio vsftpd para verificar que esté configurado para iniciarse automáticamente.

### 7. Configuración de vsftpd.conf

```bash
vim /etc/vsftpd/vsftpd.conf
```

```
anonymous_enable=YES
```

Editando el archivo de configuración `vsftpd.conf`, se habilita la opción `anonymous_enable` para permitir descargas anónimas. Esta configuración permite que los usuarios se conecten al servidor FTP sin autenticación, lo que es típico en servidores FTP públicos o donde se permite el acceso anónimo para compartir archivos públicos.

### 8. Reiniciar vsftpd para aplicar la configuración

```bash
service vsftpd restart
```

El servicio ya se reinició en el paso 4, pero eso fue *antes* de editar `vsftpd.conf`. Cualquier cambio en el fichero de configuración no surte efecto hasta que el servicio se reinicia de nuevo, así que este paso es obligatorio para que `anonymous_enable=YES` se aplique.

## Funcionamiento

Una vez completados estos pasos, el servidor vsftpd estará configurado para permitir descargas anónimas desde el directorio `/var/ftp/pub`. Los usuarios podrán acceder a este directorio sin necesidad de autenticación, utilizando un cliente FTP y especificando el servidor FTP con la dirección IP o nombre del sistema donde se realizó esta configuración.

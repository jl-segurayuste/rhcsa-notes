# Crear repositorio con la ISO automontada

Para configurar tu sistema para montar automáticamente la ISO del disco de instalación en el directorio `/repo` y usarlo como el único repositorio para la instalación, puedes seguir estos pasos:

1. **Descargar la ISO del disco de instalación**: asegúrate de tener la ISO de instalación descargada en tu sistema. Por ejemplo, vamos a suponer que la ISO está en el directorio `/root/` y se llama `CentOS-8-x86_64-dvd1.iso`.

2. **Crear el directorio de montaje**: crea el directorio `/repo` donde montarás la ISO.

   ```bash
   mkdir -p /repo
   ```

   Comprobar nombre del dispositivo:

   ```bash
   lsblk
   ```

   > [Imagen no migrada: captura de pantalla de la salida de `lsblk` mostrando el dispositivo del CD-ROM/ISO]

3. **Configurar el montaje automático de la ISO**: edita el archivo `/etc/fstab` para añadir una entrada que monte automáticamente la ISO en el arranque del sistema.

   ```bash
   vim /etc/fstab
   ```

   Añade la siguiente línea al final del archivo:

   ```plain text
   /dev/sr0 /repo iso9660 defaults 0 0
   ```

   Esto asegurará que la ISO se monte automáticamente en el directorio `/repo` cada vez que se reinicie el sistema. Nota: `/dev/sr0` es un dispositivo de bloques (la unidad óptica), no un fichero regular, así que no lleva la opción `loop` — esa opción solo es necesaria cuando el origen es un fichero `.iso` (ver la línea equivalente en el "Resumen de comandos" más abajo, que sí usa la ruta al fichero ISO con `loop`).

4. **Montar la ISO manualmente por primera vez**: para montar la ISO inmediatamente sin tener que reiniciar el sistema, ejecuta:

   ```bash
   mount -a
   ```

5. **Configurar el sistema para usar la ISO como único repositorio**: primero, elimina todos los repositorios externos existentes. Mueve los archivos de repositorio existentes a otro directorio para backup.

   ```bash
   mkdir /etc/yum.repos.d/backup
   mv /etc/yum.repos.d/*.repo /etc/yum.repos.d/backup/
   ```

6. **Crear un nuevo archivo de repositorio que apunte a la ISO montada**: crea un nuevo archivo de repositorio, por ejemplo `/etc/yum.repos.d/local.repo`.

   ```bash
   vim /etc/yum.repos.d/local.repo
   ```

   Añade la siguiente configuración:

   ```plain text
   [local]
   name=Local Repo
   baseurl=file:///repo
   enabled=1
   gpgcheck=0
   ```

7. **No registrar el sistema con subscription-manager**: asegúrate de que el sistema no está registrado con `subscription-manager`.

   ```bash
   subscription-manager unregister
   subscription-manager clean
   ```

8. **Actualizar la caché de YUM**: finalmente, actualiza la caché de YUM para que el sistema reconozca el nuevo repositorio local.

   ```bash
   yum clean all
   yum makecache
   ```

## Resumen de comandos

```bash
# Crear el directorio de montaje
mkdir -p /repo

# Editar el archivo fstab
echo '/root/CentOS-8-x86_64-dvd1.iso /repo iso9660 loop 0 0' >> /etc/fstab

# Montar la ISO manualmente
mount -a

# Mover los repositorios existentes a un directorio de backup
mkdir /etc/yum.repos.d/backup
mv /etc/yum.repos.d/*.repo /etc/yum.repos.d/backup/

# Crear el archivo del nuevo repositorio local
cat <<EOL > /etc/yum.repos.d/local.repo
[local]
name=Local Repo
baseurl=file:///repo
enabled=1
gpgcheck=0
EOL

# Asegurarse de que el sistema no esté registrado con subscription-manager
subscription-manager unregister
subscription-manager clean

# Actualizar la caché de YUM
yum clean all
yum makecache
```

## Configuración alternativa: repositorio apuntando a BaseOS y AppStream

Si al montar la ISO ves los directorios `AppStream` y `BaseOS` (típicos en las distribuciones de CentOS y RHEL), puedes crear un archivo de repositorio YUM que apunte directamente a estos directorios en lugar de a la raíz de `/repo`.

### Pasos para configurar el repositorio local

1. **Crear el archivo del repositorio local**: crea un archivo en `/etc/yum.repos.d/` que apunte a los directorios de la ISO.

   ```bash
   cat <<EOF > /etc/yum.repos.d/local.repo
   [BaseOS]
   name=Local BaseOS
   baseurl=file:///repo/BaseOS
   gpgcheck=0
   enabled=1

   [AppStream]
   name=Local AppStream
   baseurl=file:///repo/AppStream
   gpgcheck=0
   enabled=1
   EOF
   ```

2. **Limpiar y actualizar el caché de YUM**: después de configurar el archivo del repositorio, limpia el caché de YUM y actualízalo.

   ```bash
   yum clean all
   yum makecache
   ```

3. **Verificar que los repositorios locales están configurados correctamente**: puedes listar los repositorios para verificar que los locales están habilitados.

   ```bash
   yum repolist
   ```

   Deberías ver algo como:

   | repo id | repo name |
   |---|---|
   | BaseOS | Local BaseOS |
   | AppStream | Local AppStream |

### Paso adicional (opcional)

Si deseas que la ISO se monte automáticamente en cada reinicio, puedes agregar una entrada en `/etc/fstab`.

1. **Editar `/etc/fstab`**: añade la siguiente línea al archivo `/etc/fstab`:

   ```plain text
   /root/CentOS-8-x86_64-dvd1.iso /repo iso9660 loop 0 0
   ```

2. **Montar automáticamente**: monta todas las entradas del archivo `/etc/fstab` para verificar que la configuración es correcta.

   ```bash
   mount -a
   ```

# Examen de práctica B

## Grupos con requerimientos especiales

Los requerimientos implican configurar permisos avanzados para cumplir con ciertas políticas de acceso y seguridad:

### 1. Los miembros del grupo `livingopensource` tienen acceso completo a su directorio

Esto significa que los usuarios del grupo `livingopensource` deben poder:

- Crear nuevos archivos o directorios dentro de `/groups/livingopensource`.
- Leer cualquier archivo dentro del directorio.
- Modificar cualquier archivo dentro del directorio.

Este nivel de acceso corresponde a los permisos **lectura, escritura y ejecución** (`rwx`) en Unix/Linux.

### 2. Los miembros del grupo `operations` tienen acceso completo a su directorio

Igual que en el caso anterior, pero aplicado al directorio `/groups/operations`.

### 3. Los usuarios solo pueden eliminar sus propios archivos

Esta condición es especial porque los permisos por defecto de Unix/Linux no cumplen esto directamente. Para implementarlo, se debe habilitar el **sticky bit** en los directorios.

- **Sticky bit** (`t`): Si está habilitado en un directorio, solo el propietario del archivo y el usuario root pueden eliminar o renombrar los archivos dentro del directorio, aunque otros usuarios tengan permisos de escritura.

### 4. Otros usuarios (no pertenecientes a los grupos) no deben tener acceso a los directorios

Aquí, "otros" se refiere a usuarios que:

- No son miembros del grupo `livingopensource`.
- No son miembros del grupo `operations`.
- No tienen permisos específicos asignados sobre los directorios.

Para implementar esto, debes configurar permisos **000** (sin acceso) para "otros" en ambos directorios. Esto se hace asignando una máscara que excluya los permisos para "otros".

### Revisión final de los permisos

Deberás usar una combinación de:

1. **Propietarios y grupos** para asignar control sobre los directorios.
2. **Permisos Unix tradicionales** (`rwx`) para definir lo que pueden hacer los grupos.
3. **Sticky bit** para restringir el borrado de archivos.
4. **Máscaras de permisos** (`umask` o `setfacl`) para garantizar que "otros" no tengan acceso.

### Pasos detallados con los comandos (AlmaLinux 9)

**1. Crear los grupos**

Crea los grupos `livingopensource` y `operations`:

```bash
sudo groupadd livingopensource
sudo groupadd operations
```

**2. Crear los directorios**

Crea los directorios para los grupos en la ubicación especificada:

```bash
sudo mkdir -p /groups/livingopensource
sudo mkdir -p /groups/operations
```

**3. Asignar la propiedad de los directorios a los grupos**

Configura los directorios para que pertenezcan a los grupos correspondientes:

```bash
sudo chown :livingopensource /groups/livingopensource
sudo chown :operations /groups/operations
```

**4. Configurar los permisos básicos**

Configura los permisos para que los miembros de los grupos tengan acceso completo (lectura, escritura y ejecución) y "otros" usuarios no tengan ningún acceso:

```bash
sudo chmod 770 /groups/livingopensource
sudo chmod 770 /groups/operations
```

**5. Habilitar el sticky bit**

Esto restringe que los usuarios puedan eliminar archivos que no sean de su propiedad dentro del directorio:

```bash
sudo chmod +t /groups/livingopensource
sudo chmod +t /groups/operations
```

**6. Configurar el SGID (Set Group ID) en los directorios**

El **SGID** asegura que todos los archivos y subdirectorios creados dentro hereden el grupo del directorio padre, en lugar del grupo del usuario que los creó:

```bash
sudo chmod g+s /groups/livingopensource
sudo chmod g+s /groups/operations
```

**7. Crear usuarios de prueba (opcional)**

Si necesitas usuarios para probar la configuración, créalos y añádelos a los grupos:

```bash
# Crear usuarios de prueba
sudo useradd -m -G livingopensource user1
sudo useradd -m -G livingopensource user2
sudo useradd -m -G operations user3
sudo useradd -m -G operations user4

# Configurar contraseñas
sudo passwd user1
sudo passwd user2
sudo passwd user3
sudo passwd user4
```

**8. Verificar los permisos**

Verifica que los permisos estén configurados correctamente:

```bash
ls -ld /groups/livingopensource /groups/operations
```

El resultado esperado debe ser algo similar a:

```
drwxrws--T 2 root livingopensource 4096 <fecha> /groups/livingopensource
drwxrws--T 2 root operations       4096 <fecha> /groups/operations
```

**9. Probar la configuración**

1. **Inicia sesión como un usuario del grupo** y verifica que puede crear, modificar y eliminar sus propios archivos en el directorio correspondiente.
2. **Prueba como otro usuario del grupo** que puede leer y modificar archivos de otros usuarios, pero no eliminarlos.
3. **Prueba con un usuario no autorizado** que no tenga acceso a ninguno de los directorios.

**10. Documentación adicional (opcional)**

Si deseas que esta configuración persista para nuevos archivos o subdirectorios, considera usar **ACL (Access Control Lists)** para mayor flexibilidad. Por ejemplo:

```bash
sudo setfacl -d -m g:livingopensource:rwx /groups/livingopensource
sudo setfacl -d -m g:operations:rwx /groups/operations
```

Esto asegura que los permisos se hereden en futuros archivos o directorios.

---

## Creación de directorios y subdirectorios concatenados

```bash
mkdir -p /users/user{1..5}
```

---

## Instalación y configuración automática web server

Este ejercicio implica instalar un servidor web, configurarlo para que inicie automáticamente y asegurarse de que el firewall y SELinux permitan su correcto funcionamiento.

### 1. Instalar el servidor web

En este caso, asumimos que deseas instalar `httpd` (el servidor Apache en RHEL/CentOS/Fedora):

```bash
sudo dnf install -y httpd
```

### 2. Configurar el inicio automático y arrancar el servicio

```bash
sudo systemctl enable --now httpd
```

Esto asegura que el servicio `httpd` se inicie automáticamente en cada arranque y lo inicia inmediatamente.

### 3. Configurar el firewall

Permitir el acceso al puerto predeterminado (80 para HTTP):

```bash
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload
```

Si planeas usar HTTPS (puerto 443), puedes permitirlo también:

```bash
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```

### 4. Configurar SELinux

Por defecto, el servidor web tiene configurado el contexto de SELinux adecuado para sus archivos y operaciones comunes, pero si necesitas usar una ruta personalizada o habilitar funciones adicionales, configúralo.

**Si necesitas usar una ruta personalizada para los archivos web:**

Por ejemplo, si decides que los archivos del servidor web estarán en `/mywebdata`:

1. Asignar el contexto correcto:

   ```bash
   sudo semanage fcontext -a -t httpd_sys_content_t "/mywebdata(/.*)?"
   sudo restorecon -Rv /mywebdata
   ```

2. Permitir acceso para que el servidor web lea o sirva contenido: asegúrate de que SELinux permita el acceso a esos archivos.

**Si planeas usar un puerto personalizado:**

Por ejemplo, para usar el puerto 8080:

1. Agregar el puerto a SELinux:

   ```bash
   sudo semanage port -a -t http_port_t -p tcp 8080
   ```

2. Permitir el puerto en el firewall:

   ```bash
   sudo firewall-cmd --permanent --add-port=8080/tcp
   sudo firewall-cmd --reload
   ```

3. Actualizar la configuración de Apache para escuchar en ese puerto: editar `/etc/httpd/conf/httpd.conf` y modificar la línea:

   ```
   Listen 8080
   ```

4. Reiniciar el servicio para aplicar los cambios:

   ```bash
   sudo systemctl restart httpd
   ```

### 5. Verificación

- Comprobar que el servicio está activo:

  ```bash
  sudo systemctl status httpd
  ```

- Probar que el servidor responde correctamente:

  ```bash
  curl http://localhost
  ```

### Resumen

El firewall y SELinux son aspectos críticos para este ejercicio. Configura:

1. El puerto y/o servicio en el firewall.
2. El contexto de SELinux para las rutas o puertos personalizados.

---

## 3. ISO de instalación y usarla como único repositorio

> Nota: este ejercicio es idéntico en contenido al ejercicio "3. ISO de instalación y usarla como único repositorio" del [Examen de práctica A](exam-a.md#3-iso-de-instalación-y-usarla-como-único-repositorio). Se reproduce aquí completo tal como aparece en la página de origen de EXAM B.

### Ejercicio: Configurar el sistema para montar automáticamente una ISO de instalación y usarla como único repositorio.

### Pasos detallados

#### 1. Montar la ISO en /repo

**1.1 Ubica la ISO del disco de instalación**

Supongamos que la ISO está ubicada en `/home/student/AlmaLinux.iso`.

**1.2 Crea el punto de montaje**:

```bash
mkdir -p /repo
```

**1.3 Edita el archivo `/etc/fstab` para montar automáticamente la ISO**:

Añade la siguiente línea al final de `/etc/fstab`:

```bash
/dev/sr0  /repo  iso9660  defaults  0  0
```

**1.4 Aplica los cambios de montaje**:

```bash
systemctl daemon-reload
mount -a
```

**1.5 Verifica que la ISO esté montada correctamente**:

```bash
df -h | grep /repo
```

#### 2. Configurar el repositorio local

**2.1. Desactiva todos los repositorios externos**:

```bash
dnf config-manager --disable \*
```

**2.2. Se añaden el nuevo repositorio**:

```bash
dnf config-manager --add-repo=file:///repo/AppStream
dnf config-manager --add-repo=file:///repo/BaseOS
```

**2.3. Añadir la opción gpgcheck=0 a los repositorios creados**:

```bash
sed -i '$a gpgcheck=0' /etc/yum.repos.d/repo_*
```

#### 3. Probar el repositorio

**3.1 Actualiza la caché de los repositorios**:

```bash
sudo dnf clean all
sudo dnf makecache
```

**3.2 Prueba instalando un paquete para verificar el funcionamiento**:

```bash
sudo dnf install vim
```

#### 4. Notas adicionales

- **SELinux**: Si SELinux está habilitado y bloquea el acceso, ajusta el contexto del archivo ISO:

  ```bash
  sudo semanage fcontext -a -t iso9660_t "/home/student/AlmaLinux.iso"
  sudo restorecon -v /home/student/AlmaLinux.iso
  ```

- **Persistencia**: Con la entrada en `/etc/fstab`, el montaje de la ISO será automático en cada reinicio.
- **Deshabilitar `subscription-manager`**: Si hay referencias a `subscription-manager`, puedes deshabilitarlo temporalmente:

  ```bash
  dnf config-manager --set-disabled subscription-manager
  ```

### Verificaciones finales

1. **Comprobar los repositorios activos**:

   ```bash
   dnf repolist all
   ```

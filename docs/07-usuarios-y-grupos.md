# Gestión de los usuarios y los grupos

## Cree, borre y modifique cuentas de usuario locales

Para gestionar usuarios locales en sistemas basados en Red Hat (como RHEL, CentOS y Fedora), puedes utilizar comandos como `useradd`, `userdel` y `usermod`. Aquí te proporciono algunos pasos básicos:

### 1. Crear un Usuario Local

```bash
sudo useradd nombre_usuario
```

Esto creará un nuevo usuario llamado `nombre_usuario`. Puedes especificar opciones adicionales al comando según tus necesidades, como el directorio de inicio, la shell predeterminada, etc.

Ejemplo con opciones adicionales:

```bash
sudo useradd -m -s /bin/bash nombre_usuario
```

Este comando crea un usuario con un directorio de inicio (`-m`) y asigna la shell `/bin/bash` (`-s`).

### 2. Eliminar un Usuario Local

```bash
sudo userdel nombre_usuario
```

Esto eliminará el usuario llamado `nombre_usuario`. Puedes utilizar la opción `-r` para eliminar también el directorio de inicio del usuario.

```bash
sudo userdel -r nombre_usuario
```

### 3. Modificar Propiedades de un Usuario

```bash
sudo usermod opciones nombre_usuario
```

Puedes utilizar `usermod` para modificar diversas propiedades del usuario, como el nombre de usuario, el directorio de inicio, la shell, etc.

Ejemplo para cambiar el directorio de inicio:

```bash
sudo usermod -d /nuevo/directorio nombre_usuario
```

### 4. Cambiar la Contraseña de un Usuario

```bash
sudo passwd nombre_usuario
```

Esto te permitirá cambiar la contraseña del usuario `nombre_usuario`.

### 5. Verificar la Información de un Usuario

```bash
id nombre_usuario
```

Este comando muestra información detallada sobre el usuario.

### 6. Verificar la Configuración del Sistema de Usuarios

Puedes verificar la configuración general del sistema de usuarios en el archivo `/etc/login.defs`. Este archivo contiene configuraciones predeterminadas para la creación de usuarios.

```bash
cat /etc/login.defs
```

### Notas Adicionales

- Asegúrate de tener privilegios de superusuario (`sudo`) para ejecutar estos comandos.
- Los comandos pueden variar ligeramente según la distribución específica y la versión de Red Hat que estás utilizando.
- Puedes consultar la documentación y manuales de usuario (`man`) para obtener información detallada sobre estos comandos. Por ejemplo, `man useradd`, `man userdel`, etc.

## Cambie contraseñas y ajuste su duración para las cuentas de usuario locales

Para cambiar contraseñas y ajustar la duración de las contraseñas para cuentas de usuario locales en sistemas basados en Red Hat, puedes utilizar el comando `passwd` y configurar las políticas de contraseñas con el comando `chage`. Aquí te proporciono algunos pasos básicos:

### 1. Cambiar la Contraseña de un Usuario

```bash
sudo passwd nombre_usuario
```

Esto te permitirá cambiar la contraseña del usuario `nombre_usuario`. Se te pedirá que ingreses la nueva contraseña.

### 2. Configurar la Duración de Contraseñas con `chage`

```bash
sudo chage opciones nombre_usuario
```

Puedes utilizar `chage` para configurar varias opciones relacionadas con la duración de las contraseñas. Algunas opciones comunes incluyen:

- `-M`: Establece el número máximo de días que una contraseña es válida antes de que el usuario sea obligado a cambiarla.
- `-m`: Establece el número mínimo de días antes de que un usuario pueda cambiar su contraseña nuevamente.
- `-W`: Establece el número de días de advertencia antes de que la contraseña expire.

Ejemplo para establecer la duración máxima a 90 días y la advertencia a 7 días:

```bash
sudo chage -M 90 -W 7 nombre_usuario
```

### 3. Verificar la Configuración Actual

Para ver la configuración actual de la cuenta de usuario, puedes utilizar:

```bash
sudo chage -l nombre_usuario
```

Esto mostrará información detallada sobre la configuración de contraseñas para el usuario especificado.

> [Imagen no migrada: captura de pantalla de salida de ejemplo de `chage -l` (Untitled.png), adjunta en la página original de Notion]

### Notas Adicionales

- Asegúrate de tener privilegios de superusuario (`sudo`) para ejecutar estos comandos.
- Los comandos y opciones pueden variar según la versión específica y la configuración de tu sistema.
- Ten en cuenta que la configuración de duración de contraseñas puede depender del sistema de autenticación que estés utilizando (por ejemplo, `pam_unix`, `pam_pwquality`, etc.).

## Cree, borre y modifique los grupos locales y la pertenencia a grupos

Para gestionar grupos locales y la pertenencia a grupos en sistemas basados en Red Hat, puedes utilizar comandos como `groupadd`, `groupdel`, `usermod` y `gpasswd`. Aquí te proporciono algunos pasos básicos:

### 1. Crear un Grupo Local

```bash
sudo groupadd nombre_grupo
```

Esto creará un nuevo grupo llamado `nombre_grupo`.

### 2. Eliminar un Grupo Local

```bash
sudo groupdel nombre_grupo
```

Esto eliminará el grupo llamado `nombre_grupo`.

### 3. Modificar la Pertenencia de un Usuario a un Grupo

```bash
sudo usermod -aG nombre_grupo nombre_usuario
```

Esto agregará al usuario `nombre_usuario` al grupo `nombre_grupo`. Asegúrate de utilizar la opción `-aG` para agregar al usuario al grupo sin eliminarlo de otros grupos a los que pueda pertenecer.

### 4. Modificar la Pertenencia de un Usuario a Múltiples Grupos

```bash
sudo usermod -aG grupo1,grupo2,grupo3 nombre_usuario
```

Esto agregará al usuario `nombre_usuario` a los grupos `grupo1`, `grupo2` y `grupo3`. Ajusta la lista de grupos según tus necesidades.

### 5. Verificar la Pertenencia de un Usuario a Grupos

```bash
groups nombre_usuario
```

Este comando mostrará los grupos a los que pertenece el usuario `nombre_usuario`.

### 6. Cambiar el Grupo Principal de un Usuario

```bash
sudo usermod -g nombre_grupo nombre_usuario
```

Esto cambiará el grupo principal del usuario `nombre_usuario` al grupo `nombre_grupo`.

### 7. Modificar la Pertenencia de un Grupo a un Usuario

```bash
sudo gpasswd -a nombre_usuario nombre_grupo
```

Esto agregará al usuario `nombre_usuario` al grupo `nombre_grupo`. Asegúrate de utilizar la opción `-a` para agregar al usuario al grupo sin eliminarlo de otros grupos a los que pueda pertenecer.

### Notas Adicionales

- Asegúrate de tener privilegios de superusuario (`sudo`) para ejecutar estos comandos.
- Los comandos y opciones pueden variar según la versión específica y la configuración de tu sistema.

Estos son comandos básicos para gestionar grupos locales y la pertenencia a grupos en sistemas basados en Red Hat. Ajusta según tus necesidades y preferencias.

## Configure el acceso de superusuario

En sistemas basados en Red Hat, el acceso de superusuario se gestiona a través de la cuenta de usuario "root". Aquí te proporciono algunos pasos y conceptos clave para configurar el acceso de superusuario:

### 1. Acceder como Superusuario

Puedes acceder a la cuenta de superusuario "root" utilizando el comando `su` o `sudo`:

```bash
# Utilizando su
su -

# Utilizando sudo
sudo -i
```

### 2. Establecer Contraseña para la Cuenta de "root"

Si la contraseña de la cuenta "root" no está establecida, puedes configurarla con el siguiente comando:

```bash
sudo passwd root
```

Ingresa la nueva contraseña cuando se te solicite.

### 3. Deshabilitar Acceso Directo como "root"

Puede ser una buena práctica deshabilitar el acceso directo como "root" mediante SSH. Edita el archivo de configuración SSH:

```bash
sudo nano /etc/ssh/sshd_config
```

Asegúrate de que la siguiente línea esté configurada como `PermitRootLogin no`:

```
PermitRootLogin no
```

Guarda los cambios y reinicia el servicio SSH:

```bash
sudo systemctl restart sshd
```

### 4. Configurar Usuarios con Privilegios de Sudo

En lugar de utilizar la cuenta "root" directamente, puedes asignar privilegios de sudo a otros usuarios. Asegúrate de que estos usuarios estén en el grupo `wheel` para tener acceso a sudo:

```bash
# Agregar usuario al grupo wheel
sudo usermod -aG wheel nombre_usuario
```

Asegúrate de que el archivo `/etc/sudoers` incluya la línea:

```
%wheel ALL=(ALL) ALL
```

### 5. Uso de Sudo

Los usuarios en el grupo `wheel` pueden utilizar sudo para ejecutar comandos con privilegios de superusuario:

```bash
sudo comando
```

### 6. Auditoría y Registro

Es importante auditar y registrar las actividades del superusuario. Puedes revisar registros del sistema en `/var/log` y configurar registros de auditoría según sea necesario.

### Notas Adicionales

- Configurar el acceso de superusuario de manera segura es esencial para la seguridad del sistema.
- Evita el uso excesivo de la cuenta "root" y favorece el uso de sudo para reducir riesgos.
- Estas configuraciones pueden variar según la versión específica y la configuración de tu sistema.
- Ten cuidado al editar archivos de configuración críticos y realiza copias de seguridad antes de hacer cambios significativos.

Recuerda seguir las mejores prácticas de seguridad y documentar cualquier cambio en la configuración del sistema. Estos pasos proporcionan una guía general y deberían ser adaptados según los requisitos específicos y las políticas de seguridad de tu entorno.

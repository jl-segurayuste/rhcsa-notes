# Gestión de los usuarios y los grupos

## Cree, borre y modifique cuentas de usuario locales

Gestiona usuarios locales en sistemas basados en Red Hat con `useradd`, `userdel` y `usermod`.

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
sudo usermod -d /nuevo/directorio -m nombre_usuario
```

Sin `-m`, `usermod -d` solo cambia la ruta en `/etc/passwd`: el contenido del home antiguo no se mueve.

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

## Cambie contraseñas y ajuste su duración para las cuentas de usuario locales

Cambia contraseñas con `passwd` y configura las políticas de expiración con `chage`.

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

- La configuración de duración de contraseñas puede depender del sistema de autenticación en uso (por ejemplo, `pam_unix`, `pam_pwquality`).

## Cree, borre y modifique los grupos locales y la pertenencia a grupos

Gestiona grupos locales y la pertenencia a grupos con `groupadd`, `groupdel`, `usermod` y `gpasswd`.

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

Esto agregará al usuario `nombre_usuario` al grupo `nombre_grupo` (`gpasswd -d` lo quita). A diferencia de `usermod -G` sin `-a`, `gpasswd -a` nunca quita al usuario de sus otros grupos.

## Configure el acceso de superusuario

En sistemas basados en Red Hat, el acceso de superusuario se gestiona a través de la cuenta de usuario "root".

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

`/etc/sudoers` se edita **siempre con `visudo`** (comprueba la sintaxis antes de guardar: un error dejaría a todos sin `sudo`). Asegúrate de que incluya la línea:

```
%wheel ALL=(ALL) ALL
```

### 5. Uso de Sudo

Para un usuario o grupo concreto es más limpio un fichero aparte en `/etc/sudoers.d/` (también con `visudo`):

```bash
sudo visudo -f /etc/sudoers.d/operadores
```

```
%operadores ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart httpd   # solo esa orden, sin contraseña
natasha     ALL=(ALL) ALL                                         # todo, pidiendo su contraseña
```

Los usuarios en el grupo `wheel` pueden utilizar sudo para ejecutar comandos con privilegios de superusuario:

```bash
sudo comando
```

### 6. Auditoría y Registro

Es importante auditar y registrar las actividades del superusuario. Puedes revisar registros del sistema en `/var/log` y configurar registros de auditoría según sea necesario.

### Notas Adicionales

- Evita el uso excesivo de la cuenta "root" y favorece el uso de sudo para reducir riesgos.
- Ten cuidado al editar archivos de configuración críticos y realiza copias de seguridad antes de hacer cambios significativos.

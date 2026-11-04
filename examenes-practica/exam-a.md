# Examen de práctica A

> Nota: en el Notion original la numeración de ejercicios de este examen empieza en el 3 (no existen ejercicios 1 y 2 en la página de origen). Se mantiene la numeración tal cual para conservar fidelidad con la fuente.

## 3. ISO de instalación y usarla como único repositorio

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
- **Deshabilitar `subscription-manager`**: es un *plugin* de dnf, no un repositorio (`config-manager --set-disabled` no lo encuentra). Para que deje de avisar de que el sistema no está registrado:

  ```bash
  sudo sed -i 's/^enabled=1/enabled=0/' /etc/dnf/plugins/subscription-manager.conf
  # o, solo para una orden:  dnf --disableplugin=subscription-manager install …
  ```

### Verificaciones finales

1. **Comprobar los repositorios activos**:

   ```bash
   dnf repolist all
   ```

---

## 4. Cambiar contraseña root a través del shell de GRUB (1)

Pasos para realizar este ejercicio en AlmaLinux 9:

### Pasos para recuperar acceso root y cambiar la contraseña

1. **Reinicia el sistema y accede al menú GRUB**:
   - Reinicia la máquina virtual.
   - Tan pronto como aparezca el menú GRUB, presiona una tecla para detener el arranque automático.
2. **Edita las opciones de arranque**:
   - Selecciona la entrada que quieras iniciar (generalmente, la primera) y presiona la tecla `e` para editar las opciones de arranque.
3. **Modifica las opciones del kernel**:
   - Busca la línea que comienza con `linux` o `linuxefi`. Debería contener una ruta al kernel.
   - Al final de esa línea, añade la opción `rd.break`:

     ```
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

---

## 5. Validación de contraseña para 90 días y primer UID en el 2000

Para configurar valores predeterminados para nuevos usuarios en tu sistema, puedes modificar los archivos de configuración del sistema que controlan estas configuraciones. Aquí los pasos para establecer la validez predeterminada de contraseñas a 90 días y el primer UID para nuevos usuarios en 2000.

### 1. Configurar la validez de la contraseña

Esto se define en el archivo `/etc/login.defs`:

1. Edita el archivo:

   ```bash
   sudo nano /etc/login.defs
   ```

2. Busca las líneas relacionadas con `PASS_MAX_DAYS` y `PASS_MIN_DAYS`, y configúralas como sigue:

   ```bash
   PASS_MAX_DAYS   90
   PASS_MIN_DAYS   0
   ```

   - `PASS_MAX_DAYS`: Define que la contraseña caduca después de 90 días.
   - `PASS_MIN_DAYS`: Define el número mínimo de días antes de poder cambiar la contraseña (puedes ajustar según lo necesario).

3. Guarda y cierra el archivo.

### 2. Establecer el primer UID para nuevos usuarios

Esto también se define en el archivo `/etc/login.defs`:

1. En el mismo archivo `/etc/login.defs`, busca o añade las siguientes líneas:

   ```bash
   UID_MIN      2000
   UID_MAX      60000
   GID_MIN      2000
   GID_MAX      60000
   ```

   - `UID_MIN`: Define el primer UID asignado a un nuevo usuario.
   - `UID_MAX`: Define el rango máximo para los UIDs de usuarios normales.
   - Lo mismo aplica para `GID_MIN` y `GID_MAX`, que definen el rango de IDs de grupo.

2. Guarda y cierra el archivo.

### 3. Probar la configuración

1. Crea un nuevo usuario y verifica que su UID comienza en 2000:

   ```bash
   sudo useradd testuser
   id testuser
   ```

2. Verifica la validez de la contraseña predeterminada:

   ```bash
   sudo chage -l testuser
   ```

   Deberías ver algo como esto:

   ```
   Maximum number of days between password change: 90
   ```

### Notas adicionales

- En RHEL `adduser` es solo un enlace a `useradd`: los valores por defecto salen de `/etc/login.defs` y `/etc/default/useradd` (`useradd -D` los muestra). `/etc/adduser.conf` es de Debian.
- Para usuarios ya existentes, tendrás que ajustar la validez de la contraseña manualmente usando el comando `chage`:

  ```bash
  sudo chage -M 90 existing_user
  ```

---

## 6. Creación de usuarios y grupos. Un usuario con nologin

1. Creación de grupos:

   ```bash
   groupadd livingopensource && \
   groupadd operations
   ```

2. Creación de usuarios:

   ```bash
   useradd -G livingopensource -s /sbin/nologin -u 12345 santos && \
   useradd -G livingopensource edwin && \
   useradd -G operations serene && \
   useradd -G operations alex
   ```

---

## 7. Ejercicio de permisos sin ACL

Este ejercicio implica la activación de GUID 2000.

1. Creación de directorios:

   ```bash
   mkdir -p /groups/{livingopensource,operations}
   ```

2. Asignación de permisos:

   ```bash
   chown :operations /groups/operations && \
   chown :livingopensource /groups/livingopensource && \
   chmod 2770 /groups/operations && \
   chmod 2770 /groups/livingopensource
   ```

---

## 8. LVM

Crear un VG de 2GB con el nombre `myvg`, usando 8 MiB de physical extents. Dentro de este, crear un LV de 500 MiB con el nombre `mydata` y montarlo de manera permanente en `/mydata`.

1. Se accede a la herramienta `fdisk` y se genera una partición de 2GB:

   ```bash
   fdisk /dev/sdb
   # Dentro del menú se ejecutan los siguientes comandos
   # Crear una nueva tabla de particiones
   g
   # Crear una nueva partición primaria
   n
   # Asignación de 2GB
   +2G
   # Asignación del tipo de partición
   t
   lvm
   # Se guardan los cambios
   w
   ```

2. Creación del VG:

   ```bash
   vgcreate -s 8M myvg /dev/sdb1
   ```

3. Creación del LV:

   ```bash
   lvcreate -n mydata -L 500M myvg
   ```

4. Se formatea la partición creada:

   ```bash
   mkfs.ext4 /dev/mapper/myvg-mydata
   ```

5. Se monta la partición de manera permanente:

   ```bash
   mkdir /mydata && \
   echo "/dev/mapper/myvg-mydata /mydata ext4 defaults 0 0" >> /etc/fstab && \
   systemctl daemon-reload && \
   mount -a
   ```

> Nota: en el original el paso 2 aparece como `vgrcreate` y el paso 4 referencia `/dev/mapper/myvg-mydatga`; se han corregido aquí a `vgcreate` y `/dev/mapper/myvg-mydata` (nombre de comando y de LV consistentes con el resto del ejercicio) ya que son errores tipográficos evidentes de la fuente original.

---

## 9. Ejercicio con find

Pasos para encontrar todos los archivos que pertenecen al usuario `edwin` y copiarlos al directorio `/rootedwinfiles`:

### Pasos detallados

1. **Crear el directorio de destino**: Asegúrate de que el directorio de destino existe. Si no, créalo:

   ```bash
   sudo mkdir -p /rootedwinfiles
   ```

2. **Encontrar los archivos del usuario `edwin`**: Usa el comando `find` para listar todos los archivos propiedad de `edwin`:

   ```bash
   sudo find / -user edwin
   ```

   Esto buscará en todo el sistema (`/`) los archivos propiedad de `edwin`. Puede tardar dependiendo del tamaño del sistema.

3. **Copiar los archivos al directorio de destino**: Para copiar los archivos encontrados, combina `find` con `cp`:

   ```bash
   sudo find / -type f -user edwin -exec cp --parents {} /rootedwinfiles \;
   ```

   - **`--parents`**: Mantiene la estructura de directorios original.
   - **`{}`**: Representa cada archivo encontrado.
   - **`\;`**: Finaliza el comando ejecutado con `exec`.

4. **Verificar los archivos copiados**: Lista los archivos copiados en el directorio `/rootedwinfiles` para asegurarte de que todo está correcto:

   ```bash
   ls -lR /rootedwinfiles
   ```

### Notas adicionales

- Si deseas excluir errores de permisos (que pueden ocurrir al buscar en ciertas rutas), redirige los errores:

  ```bash
  sudo find / -user edwin 2>/dev/null -exec cp --parents {} /rootedwinfiles \;
  ```

- Para realizar pruebas sin copiar realmente los archivos, usa `echo` en lugar de `cp`:

  ```bash
  sudo find / -user edwin -exec echo {} \;
  ```

---

## 10. Tarea programada

```bash
0 2 * * 1-5 /usr/bin/touch /etc/motd
```

---

## 11. Crear un pool y volumen Stratis

### Ejercicio RHCSA: Configurar Stratis con disco de 10 GiB

**Enunciado**: Añadir un disco de 10 GiB, crear un pool y volumen Stratis y montarlo en `/stratis`.

### Pasos completos (versión final optimizada)

**1. Verificar discos disponibles**:

```bash
lsblk
fdisk -l
```

*Identificar el disco de 10 GiB disponible (ej: /dev/sdb)*

**2. Instalar y configurar Stratis**:

```bash
sudo dnf install -y stratisd stratis-cli
sudo systemctl enable --now stratisd
sudo systemctl status stratisd
```

**3. Crear pool Stratis**:

```bash
# Crear pool (ajustar /dev/sdb según tu sistema)
sudo stratis pool create mypool /dev/sdb

# Verificar creación
stratis pool list
```

**4. Crear filesystem Stratis**:

```bash
# Crear filesystem en el pool
sudo stratis filesystem create mypool stratisvol

# Verificar creación
stratis filesystem list
```

**5. Crear directorio de montaje**:

```bash
sudo mkdir -p /stratis
```

**6. Configurar montaje permanente**:

```bash
# Obtener UUID del filesystem
UUID=$(sudo blkid -s UUID -o value /dev/stratis/mypool/stratisvol)

# Si el comando anterior no funciona, usar método alternativo:
# UUID=$(lsblk -no UUID /dev/stratis/mypool/stratisvol)

# Añadir entrada a /etc/fstab
echo "UUID=$UUID /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0" | sudo tee -a /etc/fstab

# Método alternativo si UUID no funciona:
# echo "/dev/stratis/mypool/stratisvol /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0" | sudo tee -a /etc/fstab
```

**7. Montar el filesystem**:

```bash
sudo mount /stratis
```

**8. Verificación completa**:

```bash
# Verificar pool
stratis pool list

# Verificar filesystem
stratis filesystem list

# Verificar montaje
df -h /stratis
mount | grep stratis

# Probar escritura
echo "Test Stratis filesystem" | sudo tee /stratis/test.txt   # sudo echo … > no funciona: la redirección la hace tu shell, sin sudo
cat /stratis/test.txt

# Verificar /etc/fstab
tail -1 /etc/fstab
```

**9. Probar persistencia (opcional)**:

```bash
sudo systemctl reboot
# Después del reinicio verificar:
df -h /stratis
```

### Comandos resumidos para examen

```bash
# Instalación
sudo dnf install -y stratisd stratis-cli
sudo systemctl enable --now stratisd

# Crear pool y filesystem
sudo stratis pool create mypool /dev/sdb
sudo stratis filesystem create mypool stratisvol

# Configurar montaje
sudo mkdir -p /stratis
UUID=$(sudo blkid -s UUID -o value /dev/stratis/mypool/stratisvol)
echo "UUID=$UUID /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0" | sudo tee -a /etc/fstab
sudo mount /stratis

# Verificar
stratis pool list
stratis filesystem list
df -h /stratis
```

### Puntos críticos para el examen

**Obligatorios:**

- Servicio `stratisd` habilitado y corriendo
- Pool creado con el disco de 10 GiB
- Filesystem creado en el pool
- Montado en `/stratis`
- Entrada correcta en `/etc/fstab`
- Opción `x-systemd.requires=stratisd.service` en fstab
- Filesystem type `xfs`
- Montaje persiste tras reinicio

### Comandos de verificación del examinador

```bash
stratis pool list          # Debe mostrar mypool
stratis filesystem list    # Debe mostrar stratisvol
df -h /stratis            # Debe mostrar montaje
cat /etc/fstab            # Debe tener entrada correcta
systemctl reboot          # Probar persistencia
```

### Troubleshooting común

```bash
# Si UUID no funciona
echo "/dev/stratis/mypool/stratisvol /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0" | sudo tee -a /etc/fstab

# Si hay problemas
systemctl status stratisd
journalctl -u stratisd
```

### Detalles técnicos importantes

- Stratis maneja automáticamente el thin provisioning.
- El filesystem aparece como 1 TiB independientemente del tamaño del pool.
- `x-systemd.requires=stratisd.service` asegura que stratisd esté corriendo antes del montaje.
- El tipo de filesystem es siempre `xfs` para Stratis.

### Explicación detallada de la línea añadida a /etc/fstab para Stratis

**Línea completa en /etc/fstab:**

```bash
UUID=b988f39e-bf03-413e-af69-d0d5bf17d973 /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0
```

**Desglose campo por campo:**

**Campo 1: `UUID=b988f39e-bf03-413e-af69-d0d5bf17d973`**

- **Propósito**: Identifica de forma única el filesystem.
- **Por qué UUID**: Los UUIDs no cambian aunque se muevan los discos o cambien las letras de dispositivo.
- **Alternativa válida**: `/dev/stratis/mypool/stratisvol` (pero menos robusto).
- **Cómo obtenerlo**: `blkid -s UUID -o value /dev/stratis/mypool/stratisvol`.

**Campo 2: `/stratis`**

- **Propósito**: Punto de montaje (mount point).
- **Requisito**: El directorio debe existir antes del montaje.
- **Creación**: `mkdir -p /stratis`.
- **Permisos**: Por defecto hereda del filesystem padre.

**Campo 3: `xfs`**

- **Propósito**: Tipo de filesystem.
- **Para Stratis**: Siempre es `xfs` (Stratis usa XFS internamente).
- **No usar**: `stratis` como tipo (error común).
- **Importante**: No confundir con otros tipos como ext4, btrfs.

**Campo 4: `defaults,x-systemd.requires=stratisd.service`**

Esta es la parte más crítica y compleja:

- **`defaults`**
  - Equivale a: `rw,suid,dev,exec,auto,nouser,async`.
  - `rw`: Lectura y escritura.
  - `suid`: Permite ejecutables con setuid.
  - `dev`: Permite dispositivos especiales.
  - `exec`: Permite ejecutar binarios.
  - `auto`: Monta automáticamente en boot.
  - `nouser`: Solo root puede montar.
  - `async`: Escrituras asíncronas (mejor rendimiento).
- **`,x-systemd.requires=stratisd.service`** (CRÍTICO)
  - `x-systemd.requires`: Directiva especial de systemd.
  - Función: Asegura que `stratisd.service` esté corriendo ANTES del montaje.
  - Por qué necesario: Stratis necesita el daemon activo para funcionar.
  - Sin esto: El montaje fallará en el boot si stratisd no está iniciado.
  - Sintaxis: Siempre separado por coma, sin espacios.

**Campo 5: `0` (primer cero)**

- **Propósito**: Dump (respaldo con comando dump).
- **Valores posibles**: `0` No respaldar / `1` Respaldar.
- **Para Stratis**: Siempre `0` (dump no es compatible con Stratis).

**Campo 6: `0` (segundo cero)**

- **Propósito**: Orden de verificación con fsck.
- **Valores posibles**: `0` No verificar / `1` Verificar primero (solo para /) / `2` Verificar después.
- **Para Stratis**: Siempre `0` (fsck no es aplicable a Stratis).

**Ejemplos de líneas incorrectas comunes:**

Error 1: Tipo de filesystem incorrecto

```bash
UUID=xxx /stratis stratis defaults 0 0
```

Problema: `stratis` no es un tipo de filesystem válido.

Error 2: Falta dependencia systemd

```bash
UUID=xxx /stratis xfs defaults 0 0
```

Problema: Puede fallar en boot si stratisd no está iniciado.

Error 3: Espacios en opciones

```bash
UUID=xxx /stratis xfs defaults, x-systemd.requires=stratisd.service 0 0
```

Problema: El espacio después de la coma causa error.

Error 4: Usar device path en lugar de UUID

```bash
/dev/sdb /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0
```

Problema: Debería usar `/dev/stratis/mypool/stratisvol` o mejor UUID.

**Versiones correctas alternativas:**

Opción 1: Con UUID (recomendado)

```bash
UUID=b988f39e-bf03-413e-af69-d0d5bf17d973 /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0
```

Opción 2: Con device path

```bash
/dev/stratis/mypool/stratisvol /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0
```

Opción 3: Con opciones adicionales de seguridad

```bash
UUID=xxx /stratis xfs defaults,nodev,nosuid,x-systemd.requires=stratisd.service 0 0
```

**Comandos para verificar la línea:**

Verificar sintaxis antes del montaje:

```bash
# Verificar sintaxis de fstab
mount -fav

# Verificar línea específica
findmnt --verify /etc/fstab
```

Probar montaje:

```bash
# Montar según fstab
mount /stratis

# Verificar que funcionó
df -h /stratis
```

Verificar tras reinicio:

```bash
# Después de reboot
systemctl status stratisd
mount | grep stratis
df -h /stratis
```

**Consideraciones importantes para el examen:**

1. La opción `x-systemd.requires=stratisd.service` es OBLIGATORIA.
2. El tipo debe ser `xfs`, nunca `stratis`.
3. Los últimos dos campos deben ser `0 0` para Stratis.
4. No usar espacios en las opciones de montaje.
5. UUID es más robusto que device paths.
6. Verificar siempre con `mount -a` antes de reiniciar.

---

## 12. Usuario que solo puede iniciar sesión para ejecutar passwd

### Opción 3: Pasos completos

**1. Crear el script:**

```bash
sudo vi /usr/local/bin/passwd-shell.sh
```

**2. Contenido del script:**

```bash
#!/bin/bash
# Shell que solo permite cambiar contraseña
/usr/bin/passwd
exit
```

**3. Hacer el script ejecutable:**

```bash
sudo chmod +x /usr/local/bin/passwd-shell.sh
```

**4. Añadir el script a shells válidos:**

```bash
echo "/usr/local/bin/passwd-shell.sh" | sudo tee -a /etc/shells
```

**5. Crear el usuario bob con el shell restringido:**

```bash
sudo useradd -s /usr/local/bin/passwd-shell.sh bob
```

**6. Verificar la configuración:**

```bash
# Verificar que el usuario se creó correctamente
grep bob /etc/passwd

# Verificar que el shell está en /etc/shells
grep passwd-shell /etc/shells
```

**7. Probar el funcionamiento:**

```bash
sudo su - bob
```

Cuando bob inicie sesión:

- Se ejecutará automáticamente `passwd`.
- Podrá cambiar su contraseña.
- Después de cambiar la contraseña (o cancelar), la sesión terminará automáticamente.
- No podrá ejecutar ningún otro comando.

**Resultado esperado:**

```
[bob@localhost ~]$
Changing password for user bob.
Current password:
```

Después de cambiar la contraseña o cancelar (Ctrl+C), la sesión termina automáticamente.

### Funcionalidad interactiva limitada exclusivamente a ejecutar `passwd`

### Solución revisada: Shell restringido interactivo

1. **Creación del script `/usr/local/bin/restricted-shell-passwd.sh`**

   Actualiza el script para que sea interactivo y permita al usuario ingresar `passwd` directamente desde el prompt:

   ```bash
   #!/bin/bash
   # Shell restringido para cambiar contraseñas
   while true; do
       echo -n "Restricted shell> "
       read -r cmd || exit 0      # Ctrl+D o fin de la entrada: salir (si no, bucle infinito)
       if [ "$cmd" == "passwd" ]; then
           /usr/bin/passwd
       else
           echo "Access denied. You are only allowed to change your password."
       fi
   done
   ```

   Este script:
   - Muestra un prompt (`Restricted shell>`).
   - Permite al usuario escribir un comando.
   - Ejecuta `passwd` si el usuario lo ingresa, y bloquea cualquier otro comando.
   - Sale con Ctrl+D: sin el `|| exit 0`, `read` fallaría en cada vuelta y el bucle se quedaría
     imprimiendo el prompt sin parar.
   - El shell no limita lo que no pasa por él: por SSH el usuario aún podría abrir túneles
     (`ssh -L`). Para cerrarlo, en `sshd_config`: `Match User bob` + `AllowTcpForwarding no` y
     `X11Forwarding no`.

2. **Verifica y configura los permisos del script**

   Asegúrate de que el script sea ejecutable:

   ```bash
   sudo chmod 755 /usr/local/bin/restricted-shell-passwd.sh
   ```

3. Se añade la ruta del script al fichero `/etc/shells` para que pueda ser asignada como shell en la creación del usuario:

   ```bash
   echo "/usr/local/bin/restricted-shell-passwd.sh" >> /etc/shells
   ```

   Se crea el usuario:

   ```bash
   useradd -s /usr/local/bin/restricted-shell-passwd.sh bob
   ```

4. **Prueba el nuevo shell interactivo**

   Ahora intenta iniciar sesión como `bob`:

   ```bash
   su - bob
   ```

   Verás algo como:

   ```
   Restricted shell>
   ```

   - Si escribes `passwd`, el sistema permitirá cambiar la contraseña.
   - Si escribes cualquier otro comando (`ls`, `whoami`, etc.), mostrará:

     ```
     Access denied. You are only allowed to change your password.
     ```

### Subsección: Explicación del script (subpágina anidada)

> Nota: esta subpágina de Notion ("EXPLICACION DEL SCRIPT") era corta, por lo que se incluye aquí como subsección en lugar de fichero aparte.

**Detalles importantes**

- Este enfoque mantiene al usuario en un shell interactivo restringido.
- **El bucle infinito (`while true`)** asegura que el usuario no pueda salir del prompt, excepto cerrando la sesión.
- **Seguridad:** El usuario no podrá ejecutar comandos arbitrarios, ya que todos serán rechazados excepto `passwd`.

Desglose del script paso a paso:

**1. Primera línea: `#!/bin/bash`**

Esta línea, conocida como *shebang*, indica que este script debe ejecutarse usando el intérprete de Bash.

**2. Comentario**

```bash
# Shell restringido para cambiar contraseñas
```

Este comentario es simplemente informativo para los administradores del sistema. No tiene efecto en la ejecución del script.

**3. Bucle infinito: `while true; do`**

```bash
while true; do
```

Este es un bucle infinito que ejecuta su contenido repetidamente. El propósito aquí es mantener al usuario dentro de un *prompt* restringido hasta que cierre la sesión.

- `true` es un comando que siempre devuelve éxito (`0`), haciendo que el bucle nunca termine.
- Esto evita que el usuario pueda salir accidentalmente del entorno restringido (a menos que cierre la sesión manualmente).

**4. Mostrar el prompt personalizado**

```bash
echo -n "Restricted shell> "
```

- Muestra un mensaje como `Restricted shell> `, que actúa como un indicador de que el usuario debe ingresar un comando.
- La opción `-n` evita que `echo` añada un salto de línea, por lo que el cursor del usuario aparece al final del mensaje.

**5. Leer el comando del usuario**

```bash
read -r cmd || exit 0
```

- Este comando espera que el usuario escriba algo y lo almacena en la variable `cmd`. Si la entrada se acaba (Ctrl+D), `read` falla y `|| exit 0` cierra la sesión; sin él, el bucle se repetiría sin fin.
- La opción `-r` evita que Bash interprete caracteres de escape (`\`) en la entrada del usuario, garantizando que se lea exactamente lo que el usuario escribe.

Por ejemplo: si el usuario escribe `passwd`, el valor de `cmd` será exactamente `passwd`.

**6. Condicional: Validar el comando ingresado**

```bash
if [ "$cmd" == "passwd" ]; then
    /usr/bin/passwd
else
    echo "Access denied. You are only allowed to change your password."
fi
```

- Bloque `if [ "$cmd" == "passwd" ]`: Este condicional verifica si el valor ingresado por el usuario (`$cmd`) es exactamente `passwd`. Si es cierto, ejecuta el comando `passwd` como un subproceso normal (sin `exec`).
- **Importante: aquí NO se debe usar `exec`.** `exec` reemplaza el proceso actual (el propio script) por el comando indicado, por lo que si se usara `exec /usr/bin/passwd`, en cuanto `passwd` terminara se acabaría también el proceso del script — la sesión se cerraría en vez de volver al `while true` y mostrar de nuevo `Restricted shell>`. Al llamar a `passwd` sin `exec`, el script continúa ejecutándose después de que `passwd` termine y el bucle vuelve a mostrar el prompt, que es el comportamiento que se describe a continuación.
- Bloque `else`: Si el usuario ingresa cualquier cosa que no sea `passwd`, el script muestra `Access denied. You are only allowed to change your password.`. Esto asegura que ningún otro comando sea ejecutado.

**7. Cierre del bucle**

```bash
done
```

- Este `done` indica el final del bloque del bucle `while`.
- Después de ejecutar lo que está dentro del bucle, el script vuelve al principio (a `while true`) y muestra el mensaje de nuevo, manteniendo al usuario restringido.

**Resumen del flujo de trabajo**

1. El script inicia y muestra el mensaje: `Restricted shell>`.
2. El usuario escribe un comando.
3. Si el comando es `passwd`, se ejecuta el programa `/usr/bin/passwd` para cambiar la contraseña.
4. Si el comando es cualquier otra cosa, se muestra un mensaje de acceso denegado.
5. El bucle se repite para mantener al usuario restringido.

**¿Por qué es seguro?**

1. **Validación estricta del comando**: Solo se permite ejecutar `passwd`.
2. **No hay escape**: No hay forma de ejecutar comandos arbitrarios, ni siquiera con caracteres especiales como `;` o `&`.
3. **Bucle infinito**: El usuario no puede salir del entorno restringido a menos que cierre la sesión.

---

## 13. Instalar y levantar automáticamente el servicio de vsftpd

### Instalación y configuración de vsftpd

**1. Instalar el servicio vsftpd:**

```bash
dnf install -y vsftpd
```

**2. Habilitar el servicio para que inicie automáticamente en el arranque:**

```bash
systemctl enable vsftpd
```

**3. Iniciar el servicio:**

```bash
systemctl start vsftpd
```

**4. Verificar que está funcionando:**

```bash
systemctl status vsftpd
```

**5. Verificar que está habilitado para el arranque:**

```bash
systemctl is-enabled vsftpd
```

(Debería mostrar "enabled")

**6. Verificar que el puerto está escuchando:**

```bash
ss -tuln | grep :21
```

**7. (Opcional) Configurar firewall si está activo:**

```bash
firewall-cmd --permanent --add-service=ftp
firewall-cmd --reload
```

**8. Verificar que funciona después de reinicio (opcional para testing):**

```bash
systemctl reboot
# Después del reinicio
systemctl status vsftpd
```

**Comandos resumidos:**

```bash
dnf install -y vsftpd
systemctl enable --now vsftpd
systemctl status vsftpd
```

El `--now` en `systemctl enable` hace tanto el enable como el start en un solo comando.

---

## 14. Crear contenedor HTTP como servicio del sistema

### Ejercicio RHCSA: Crear contenedor HTTP como servicio del sistema

**Enunciado**: Create a container that runs an HTTP server. Ensure that it is started on system boot as a system user service.

### Procedimiento (RHEL 9)

Detalle, alternativa con Quadlet y problemas habituales en
[`crear-contenedor-http-servicio-sistema.md`](../ejercicios-memorizar/crear-contenedor-http-servicio-sistema.md).

```bash
# Como root
useradd container-user && passwd container-user      # usuario normal: recibe subuid/subgid
loginctl enable-linger container-user                # sus servicios arrancan sin que inicie sesión
mkdir -p /httproot
echo "<h1>Hello from Container HTTP Server</h1>" > /httproot/index.html
chown -R container-user: /httproot
firewall-cmd --permanent --add-port=8080/tcp && firewall-cmd --reload

# Como container-user, en una sesión real (systemctl --user no funciona tras su/sudo -u)
ssh container-user@localhost                         # o: machinectl shell container-user@
podman run -d --name http-container -p 8080:80 \
    -v /httproot:/usr/local/apache2/htdocs:Z docker.io/library/httpd
mkdir -p ~/.config/systemd/user && cd ~/.config/systemd/user
podman generate systemd --new --files --name http-container
podman rm -f http-container                          # con --new el servicio crea el suyo
systemctl --user daemon-reload
systemctl --user enable --now container-http-container.service
```

Errores que aparecían en versiones anteriores de estas notas:

- `useradd -r -d /var/lib/containers`: un usuario de sistema no recibe rangos de `subuid`/`subgid` y
  ese directorio es el almacenamiento de los contenedores de root.
- `--security-opt label=disable`: desactiva SELinux para el contenedor; `:Z` ya resuelve el contexto.
- `-p 127.0.0.1:8080:80` con el puerto abierto en el cortafuegos: solo responde desde la propia máquina.

### Verificación

```bash
loginctl show-user container-user | grep Linger      # Linger=yes
curl http://localhost:8080
systemctl reboot   # y, tras el arranque, sin iniciar sesión como container-user: curl http://localhost:8080
```

## 15. Arranque automático del container user

Esto ya se realizó completamente en el ejercicio anterior.

### Lo que ya se configuró

**Servicio de usuario del sistema:**

- Usuario `container-user` (usuario normal, con lingering).
- Servicio generado con `podman generate systemd`.
- Archivo de servicio en `~/.config/systemd/user/`.

**Inicio automático en boot:**

- `loginctl enable-linger container-user` → Permite servicios sin login.
- `systemctl --user enable container-http-container.service` → Habilitado para autoarranque.

**Funcionamiento como servicio del sistema:**

- Servicio systemd funcional.
- Se inicia automáticamente en el arranque.
- Persistente tras reinicios.

### Para verificar que está correctamente configurado

```bash
# Verificar que lingering está habilitado
sudo loginctl show-user container-user | grep Linger
# Debe mostrar: Linger=yes

# Verificar que el servicio está habilitado
sudo systemctl --user -M container-user@ is-enabled container-http-container.service
# Debe mostrar: enabled

# Verificar que está corriendo
curl http://localhost:8080
# Debe mostrar la página web

# Probar tras reinicio (opcional)
sudo systemctl reboot
# Después del reinicio:
curl http://localhost:8080
```

### Respuesta para el examen

> "The container is already configured as a system user service with automatic startup on boot. This was implemented using: a regular user with lingering enabled, systemd user service generated by podman, service enabled for automatic startup."

**El ejercicio ya está completo.**

---

## 16. Configurar servidor NFS

### Ejercicio 1: Configurar servidor NFS

**Enunciado**: Create a directory with the name /users and ensure it contains the subdirectories linda and anna. Export this directory by using an NFS server.

### Pasos completos

**1. Crear estructura de directorios:**

```bash
# Crear directorio principal y subdirectorios
sudo mkdir -p /users/{linda,anna}

# Verificar estructura
ls -la /users/
```

**2. Instalar servidor NFS:**

```bash
sudo dnf install -y nfs-utils
```

**3. Habilitar e iniciar servicios NFS:**

```bash
# Habilitar e iniciar servicios necesarios
sudo systemctl enable --now nfs-server
sudo systemctl enable --now rpcbind

# Verificar estado de ambos servicios
sudo systemctl status nfs-server
sudo systemctl status rpcbind
```

**4. Configurar exportaciones NFS:**

```bash
# Crear/editar archivo de exportaciones
echo "/users *(rw,sync,no_root_squash,no_all_squash)" | sudo tee -a /etc/exports

# Verificar contenido
cat /etc/exports
```

> `no_root_squash` hace que root en el cliente sea root también en el servidor: úsalo solo si el enunciado lo pide. Por defecto (`root_squash`) root del cliente se convierte en `nobody`.

**5. Configurar permisos del directorio:**

```bash
# Establecer permisos apropiados
sudo chmod 755 /users
sudo chmod 755 /users/linda
sudo chmod 755 /users/anna

# Verificar permisos
ls -la /users/
```

**6. Configurar firewall:**

```bash
# Añadir servicios NFS al firewall
sudo firewall-cmd --permanent --add-service=nfs
sudo firewall-cmd --permanent --add-service=mountd
sudo firewall-cmd --permanent --add-service=rpc-bind

# Recargar firewall
sudo firewall-cmd --reload

# Verificar reglas aplicadas
sudo firewall-cmd --list-services
```

**7. Aplicar exportaciones:**

```bash
# Exportar directorios configurados
sudo exportfs -arv

# Verificar exportaciones activas
sudo exportfs -v
showmount -e localhost
```

**8. Verificación completa:**

```bash
# Verificar servicios
sudo systemctl is-active nfs-server
sudo systemctl is-active rpcbind

# Verificar exportaciones
showmount -e localhost
# Debe mostrar: /users *

# Probar montaje manual
sudo mkdir -p /mnt/test
sudo mount -t nfs localhost:/users /mnt/test
df -h /mnt/test
sudo umount /mnt/test
sudo rmdir /mnt/test
```

### Verificaciones críticas para el examen

**NFS:**

- `showmount -e localhost` → Debe mostrar `/users`, `/users/linda`, `/users/anna`.
- `sudo systemctl status nfs-server` → active (running).

---

## 17. Crear usuarios y configurar autofs para montar automáticamente las carpetas NFS

### Ejercicio 2: Configurar AutoFS para usuarios con NFS

**Enunciado**: Create users linda and anna and set their home directories to /home/users/linda and /home/users/anna. Make sure that while these users access their home directory, autofs is used to mount the NFS shares /users/linda and /users/anna from the same server.

### Pasos completos (versión final funcionando)

**1. Configurar exportaciones NFS adicionales:**

```bash
# Añadir exportaciones de subdirectorios individuales
sudo tee -a /etc/exports > /dev/null << 'EOF'
/users/linda *(rw,sync,no_root_squash,no_all_squash)
/users/anna *(rw,sync,no_root_squash,no_all_squash)
EOF

# Aplicar nuevas exportaciones
sudo exportfs -arv

# Verificar exportaciones
showmount -e localhost
# Debe mostrar: /users/linda *, /users/anna *, /users *
```

**2. Instalar AutoFS:**

```bash
sudo dnf install -y autofs
```

**3. Crear usuarios con directorios home personalizados:**

```bash
# Crear usuarios sin crear directorios home automáticamente
sudo useradd -M -d /home/users/linda linda
sudo useradd -M -d /home/users/anna anna

# Establecer contraseñas
sudo passwd linda
sudo passwd anna

# Verificar usuarios creados
grep -E 'linda|anna' /etc/passwd
```

**4. Configurar permisos correctos en NFS:**

```bash
# Cambiar propietario de los directorios NFS
sudo chown linda:linda /users/linda
sudo chown anna:anna /users/anna

# Verificar permisos
ls -la /users/
```

**5. Crear estructura de directorios para AutoFS:**

```bash
# Crear directorio padre para montajes autofs
sudo mkdir -p /home/users
```

**6. Configurar AutoFS - Archivo maestro:**

```bash
# Configurar archivo maestro de autofs
echo "/home/users /etc/auto.users --timeout=60" | sudo tee -a /etc/auto.master

# Verificar configuración
cat /etc/auto.master | grep users
```

**7. Configurar AutoFS - Archivo de mapeo:**

```bash
# Crear archivo de mapeo para usuarios
sudo tee /etc/auto.users > /dev/null << 'EOF'
linda -rw,soft,intr,vers=4 localhost:/users/linda
anna -rw,soft,intr,vers=4 localhost:/users/anna
EOF

# Verificar contenido
cat /etc/auto.users
```

**8. Habilitar e iniciar AutoFS:**

```bash
# Habilitar e iniciar servicio autofs
sudo systemctl enable --now autofs

# Verificar estado
sudo systemctl status autofs
```

**9. Verificación completa:**

```bash
# Verificar servicios activos
sudo systemctl is-active autofs
sudo systemctl is-active nfs-server
sudo systemctl is-active rpcbind

# Verificar montaje autofs
mount | grep auto.users
# Debe mostrar: /etc/auto.users on /home/users type autofs

# Probar acceso automático
ls /home/users/linda/
ls /home/users/anna/

# Verificar que los usuarios pueden trabajar
sudo su - linda -c 'pwd && touch ~/test-file.txt && ls -la ~'
sudo su - anna -c 'pwd && touch ~/test-file.txt && ls -la ~'

# Verificar sincronización con NFS
ls -la /users/linda/
ls -la /users/anna/
```

**10. Prueba de persistencia tras reinicio:**

```bash
# Reiniciar sistema
sudo systemctl reboot

# Después del reinicio, verificar
sudo systemctl status autofs
sudo systemctl status nfs-server
sudo su - linda -c 'ls -la ~ && pwd'
sudo su - anna -c 'ls -la ~ && pwd'
```

### Comandos resumidos para examen

**NFS:**

```bash
sudo mkdir -p /users/{linda,anna}
sudo dnf install -y nfs-utils
sudo systemctl enable --now nfs-server rpcbind
echo "/users *(rw,sync,no_root_squash,no_all_squash)" | sudo tee -a /etc/exports
sudo firewall-cmd --permanent --add-service={nfs,mountd,rpc-bind} && sudo firewall-cmd --reload
sudo exportfs -arv
```

**AutoFS:**

```bash
sudo tee -a /etc/exports > /dev/null << 'EOF'
/users/linda *(rw,sync,no_root_squash,no_all_squash)
/users/anna *(rw,sync,no_root_squash,no_all_squash)
EOF
sudo exportfs -arv
sudo dnf install -y autofs
sudo useradd -M -d /home/users/linda linda
sudo useradd -M -d /home/users/anna anna
sudo chown linda:linda /users/linda
sudo chown anna:anna /users/anna
sudo mkdir -p /home/users
echo "/home/users /etc/auto.users --timeout=60" | sudo tee -a /etc/auto.master
sudo tee /etc/auto.users > /dev/null << 'EOF'
linda -rw,soft,intr,vers=4 localhost:/users/linda
anna -rw,soft,intr,vers=4 localhost:/users/anna
EOF
sudo systemctl enable --now autofs
```

### Verificaciones críticas para el examen

**AutoFS:**

- `sudo su - linda` → Debe funcionar sin errores.
- `sudo su - anna` → Debe funcionar sin errores.
- `touch ~/test.txt` → Debe crear archivos en ambos usuarios.
- Los archivos deben aparecer en `/users/linda/` y `/users/anna/`.

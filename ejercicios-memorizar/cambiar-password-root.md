# Cambiar la contraseña de root sin saber cual es

> **Enunciado:** You are new System Administrator and from now you are going to handle the system and your main task is Network monitoring, Backup and Restore. But you don't know the root password. Change the root password to redhat and login in default Runlevel.

Para cambiar la contraseña de root en un sistema Linux cuando no conoces la contraseña actual, necesitarás acceder al modo de recuperación (rescue mode) o modo de usuario único (single-user mode). Aquí tienes los pasos para hacerlo:

1. **Reiniciar el sistema:**
   - Reinicia el sistema.

2. **Acceder al GRUB:**
   - Durante el arranque, espera a que aparezca el menú de GRUB. Si no ves el menú de GRUB, presiona la tecla `Esc` o `Shift` para mostrarlo.

3. **Editar las opciones del GRUB:**
   - Selecciona la entrada de arranque (normalmente la primera opción) y presiona la tecla `e` para editar las opciones de arranque.

4. **Modificar la línea del kernel:**
   - Encuentra la línea que comienza con `linux` o `linux16` (dependiendo de la versión de GRUB y Linux).
   - Al final de esta línea, añade `rd.break` para Red Hat/CentOS 7 y superiores, o `single` para versiones anteriores (esto indica al sistema que arranque en modo de usuario único).

     Ejemplo:

     ```
     linux16 /vmlinuz-3.10.0-957.el7.x86_64 ro root=/dev/mapper/rhel-root rd.break
     ```

5. **Arrancar en modo de usuario único:**
   - Presiona `Ctrl+X` o `F10` para arrancar el sistema con las opciones modificadas.

6. **Montar el sistema de archivos en modo lectura y escritura:**
   - Cuando el sistema arranque en el modo de usuario único, estarás en un shell básico.
   - Monta el sistema de archivos en modo lectura/escritura:

     ```bash
     mount -o remount,rw /sysroot
     ```

7. **Cambiar la contraseña de root:**
   - Cambia la raíz del sistema de archivos al entorno `chroot`:

     ```bash
     chroot /sysroot
     ```

   - Cambia la contraseña de root:

     ```bash
     passwd
     ```

     - Introduce la nueva contraseña (en este caso, `redhat`).

8. **Reetiquetar SELinux (opcional pero recomendado):**
   - Para asegurar que los contextos SELinux se etiqueten correctamente, crea un archivo `.autorelabel`:

     ```bash
     touch /.autorelabel
     ```

9. **Salir y reiniciar:**
   - Sal del entorno `chroot`:

     ```bash
     exit
     ```

   - Reinicia el sistema:

     ```bash
     reboot
     ```

## Paso a paso detallado

1. **Reinicia el sistema:**
   - Desde una sesión de terminal o interfase gráfica, selecciona reiniciar el sistema.

2. **Accede al GRUB:**
   - Durante el arranque, en el menú de GRUB, selecciona la opción de arranque que usas normalmente y presiona `e` para editar.

3. **Editar las opciones del GRUB:**
   - Añade `rd.break` al final de la línea `linux` o `linux16`.

4. **Arranca en modo de usuario único:**
   - Presiona `Ctrl+X` o `F10` para iniciar.

5. **Montar el sistema de archivos en lectura/escritura:**
   - En el shell que aparece, monta el sistema de archivos:

     ```bash
     mount -o remount,rw /sysroot
     ```

6. **Cambiar la contraseña de root:**
   - Cambia a `chroot`:

     ```bash
     chroot /sysroot
     ```

   - Cambia la contraseña:

     ```bash
     passwd
     ```

7. **SELinux reetiquetado (opcional):**
   - Crear el archivo para reetiquetar:

     ```bash
     touch /.autorelabel
     ```

8. **Salir y reiniciar:**
   - Sal y reinicia:

     ```bash
     exit
     reboot
     ```

Después de seguir estos pasos, la contraseña de root será cambiada a `redhat` y podrás iniciar sesión en el sistema con el nuevo password.

## ¿Qué es `rd.break`?

El parámetro `rd.break` es utilizado en el proceso de arranque de Linux para interrumpir el flujo de arranque y proporcionar acceso a un shell de emergencia antes de que se monte el sistema de archivos raíz. Esto es particularmente útil para tareas de recuperación y mantenimiento del sistema, como cambiar la contraseña de root o reparar el sistema de archivos.

### Proceso con `rd.break`

1. **Interrupción del arranque:**
   - Al añadir `rd.break` a la línea del kernel en el menú de GRUB, el sistema se interrumpe antes de montar el sistema de archivos raíz (`/sysroot`). Esto te lleva a un shell de emergencia en modo de mantenimiento.

## ¿Qué es `chroot` y cómo funciona?

El comando `chroot` cambia el directorio raíz para un proceso y sus hijos a un nuevo directorio. Esto crea un entorno de "jaula" donde todos los archivos y directorios fuera de ese directorio no son accesibles.

`chroot` cambia el punto de referencia para todos los accesos a archivos. Cuando un proceso está en un entorno `chroot`, cualquier referencia a `/` se traduce en el nuevo directorio raíz especificado por `chroot`. Esto es útil para crear un entorno aislado, por ejemplo, para pruebas o para la recuperación del sistema.

### Sintaxis

```bash
chroot NUEVO_DIRECTORIO COMANDO
```

- `NUEVO_DIRECTORIO`: El nuevo directorio raíz que deseas establecer.
- `COMANDO`: El comando que deseas ejecutar en el nuevo entorno. Si se omite, se inicia una nueva shell en el nuevo entorno.

### Ejemplo paso a paso con `rd.break`

Aquí hay un ejemplo práctico de cómo usar `chroot` para cambiar la contraseña de root en un sistema Linux:

1. **Reinicia el sistema.**
   - Durante el arranque, accede al menú de GRUB (presionando `Esc` o `Shift` si es necesario).

2. **Edita la entrada de GRUB.**
   - Selecciona la entrada del kernel y presiona `e` para editarla.
   - Añade `rd.break` al final de la línea del kernel.

     ```
     linux16 /vmlinuz-... ro root=UUID=... rd.break
     ```

3. **Arranca el sistema.**
   - Presiona `Ctrl+X` o `F10` para arrancar el sistema con las opciones modificadas.

4. **Montar el sistema de archivos.**
   - Una vez en el shell de emergencia, monta el sistema de archivos raíz en modo lectura/escritura.

     ```bash
     mount -o remount,rw /sysroot
     ```

5. **Usa `chroot` para cambiar el entorno raíz.**
   - Cambia el entorno raíz al nuevo sistema de archivos montado.

     ```bash
     chroot /sysroot
     ```

   - Ahora, el shell está operando en el contexto del sistema de archivos raíz del sistema principal.

6. **Cambia la contraseña de root.**
   - Ejecuta el comando `passwd` para cambiar la contraseña de root.

     ```bash
     passwd
     ```

7. **Opcional: reetiquetar el sistema de archivos (si usas SELinux).**
   - Esto asegura que las etiquetas de SELinux se actualicen correctamente.

     ```bash
     touch /.autorelabel
     ```

8. **Sal del entorno `chroot` y reinicia.**
   - Sal del entorno `chroot` y reinicia el sistema.

     ```bash
     exit
     reboot
     ```

### Resumen del proceso

1. **Reiniciar y acceder a GRUB:**
   - Editar las opciones de arranque y añadir `rd.break`.
2. **Montar el sistema de archivos:**
   - `mount -o remount,rw /sysroot`
3. **Cambiar el entorno raíz con `chroot`:**
   - `chroot /sysroot`
4. **Cambiar la contraseña de root:**
   - `passwd`
5. **Reetiquetar el sistema de archivos (opcional):**
   - `touch /.autorelabel`
6. **Salir y reiniciar:**
   - `exit`
   - `reboot`

### ¿Por qué es útil `chroot`?

- **Recuperación del sistema**: Puedes realizar tareas de recuperación y mantenimiento en el sistema de archivos original.
- **Seguridad**: Puedes crear entornos aislados para ejecutar programas de manera segura sin afectar al sistema principal.
- **Pruebas**: Puedes crear entornos de prueba independientes del sistema operativo principal.

Usar `chroot` es una herramienta poderosa para administradores de sistemas, proporcionando un entorno seguro y aislado para diversas tareas administrativas y de recuperación.

# Cómo evitarlo

Como responsable de seguridad, evitar que se utilice el método descrito anteriormente para cambiar la contraseña de root mediante `chroot` y otros procedimientos similares implica implementar varias medidas de seguridad. Aquí hay algunas estrategias que puedes seguir:

### 1. Proteger el acceso al GRUB

**Contraseña para GRUB:** Configurar una contraseña para GRUB para evitar que usuarios no autorizados modifiquen las opciones de arranque.

**Configuración de contraseña para GRUB:**

1. **Generar una contraseña cifrada:**

   ```bash
   grub-mkpasswd-pbkdf2
   ```

   Introduce una contraseña y copia la cadena resultante.

2. **Editar el archivo de configuración de GRUB:** Abre `/etc/grub.d/40_custom` y añade las siguientes líneas:

   ```bash
   set superusers="admin"
   password_pbkdf2 admin <contraseña_cifrada>
   ```

3. **Actualizar la configuración de GRUB:**

   ```bash
   grub2-mkconfig -o /boot/grub2/grub.cfg
   ```

### 2. Bloquear el acceso físico

**Control de acceso físico:** Asegura que solo personal autorizado tenga acceso físico al servidor. Usa cerraduras, tarjetas de acceso y otros métodos para restringir el acceso a la máquina.

### 3. Habilitar seguridad en el arranque (Secure Boot)

**Secure Boot:** Habilita Secure Boot en el firmware UEFI para asegurar que solo sistemas operativos firmados y confiables se puedan cargar.

### 4. Deshabilitar el modo de usuario único

**Modificar el acceso al modo de usuario único:** Configura el sistema para que el modo de usuario único requiera una contraseña de root.

1. **Editar el archivo de configuración del modo de usuario único:** Abre `/usr/lib/systemd/system/rescue.service` y `/usr/lib/systemd/system/emergency.service`.
2. **Añadir la siguiente línea a ambos archivos:**

   ```bash
   ExecStart=-/bin/sh -c '/usr/sbin/sulogin; /bin/systemctl --fail --no-block default'
   ```

3. **Reiniciar el sistema para aplicar los cambios.**

### 5. Monitorear y auditar el acceso

**Habilitar el registro y monitoreo:** Configura y revisa regularmente los registros del sistema para detectar intentos de acceso no autorizado.

**Configurar el sistema de auditoría:**

1. **Instalar auditd:**

   ```bash
   yum install auditd
   ```

2. **Habilitar y iniciar auditd:**

   ```bash
   systemctl enable auditd
   systemctl start auditd
   ```

3. **Configurar reglas de auditoría:** Añade reglas en `/etc/audit/rules.d/audit.rules` para monitorear cambios críticos en el sistema.

### 6. Usar controles de acceso basados en roles (RBAC)

**Implementar RBAC:** Usa controles de acceso basados en roles para limitar las acciones que los usuarios pueden realizar. SELinux y AppArmor pueden proporcionar un control granular sobre lo que los usuarios y procesos pueden hacer.

### 7. Mantenimiento de software actualizado

**Mantén el software actualizado:** Asegúrate de que todos los sistemas operativos, firmware y software estén actualizados con los últimos parches de seguridad para reducir las vulnerabilidades explotables.

### 8. Cifrado de disco completo

**Cifrado de disco:** Implementa cifrado de disco completo para proteger los datos en caso de acceso físico no autorizado.

**Ejemplo con LUKS:**

1. **Instalar herramientas de cifrado:**

   ```bash
   yum install cryptsetup
   ```

2. **Configurar cifrado en una partición:**

   ```bash
   cryptsetup luksFormat /dev/sdaX
   cryptsetup luksOpen /dev/sdaX crypted_disk
   mkfs.ext4 /dev/mapper/crypted_disk
   ```

### 9. Políticas de contraseña fuertes

**Enforce strong password policies:** Asegura que todas las cuentas de usuario, especialmente la de root, usen contraseñas fuertes que se cambian regularmente.

### 10. Deshabilitar usuarios root en SSH

**Deshabilitar acceso root vía SSH:** Modifica la configuración de SSH para evitar que el usuario root se conecte directamente.

**Modificar `/etc/ssh/sshd_config`:**

1. **Editar el archivo:**

   ```bash
   PermitRootLogin no
   ```

2. **Reiniciar el servicio SSH:**

   ```bash
   systemctl restart sshd
   ```

Implementando estas medidas de seguridad, puedes reducir significativamente el riesgo de que alguien no autorizado pueda cambiar la contraseña de root y acceder al sistema sin permiso.

---

## Subpágina relacionada

Existe una subpágina anidada con el procedimiento equivalente para **Debian**, ya que el proceso difiere del de Red Hat/CentOS descrito arriba: ver [Distribuciones Debian](cambiar-password-root-debian.md).

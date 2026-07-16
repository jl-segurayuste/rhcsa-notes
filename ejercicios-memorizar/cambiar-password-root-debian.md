# Cambiar la contraseña de root sin saberla - Distribuciones Debian

> Subpágina de [Cambiar la contraseña de root sin saber cual es](cambiar-password-root.md), con el procedimiento específico para Debian 12 (a diferencia del ejercicio principal, orientado a Red Hat/CentOS).

Cambiar la contraseña de root en **Debian 12** sin conocer la actual sigue un proceso ligeramente diferente al de Red Hat/CentOS.

## Método 1: Usando GRUB (Recomendado para Debian 12)

### 1. Reiniciar el sistema

```bash
reboot
```

### 2. Acceder al menú GRUB

- Durante el arranque, presiona `ESC` o mantén presionada `Shift` para mostrar el menú GRUB.
- Si GRUB aparece automáticamente, continúa al siguiente paso.

### 3. Editar las opciones de arranque

- Selecciona la entrada de Debian (normalmente la primera).
- Presiona `e` para editar.

### 4. Modificar la línea del kernel

Busca la línea que comienza con `linux` y contiene `/boot/vmlinuz-...`

**Cambia esto:**

```
linux /boot/vmlinuz-... root=UUID=... ro quiet
```

**Por esto:**

```
linux /boot/vmlinuz-... root=UUID=... rw init=/bin/bash
```

Cambios específicos:

- Cambia `ro` por `rw` (lectura-escritura).
- Agrega `init=/bin/bash` al final.

### 5. Arrancar el sistema modificado

- Presiona `Ctrl+X` o `F10` para arrancar.

### 6. Cambiar la contraseña de root

Ahora estarás en una shell como root:

```bash
# Montar el sistema de archivos en modo lectura-escritura
mount -o remount,rw /

# Cambiar la contraseña de root
passwd root
# Ingresa la nueva contraseña: redhat

# Si tienes SELinux habilitado (poco común en Debian)
touch /.autorelabel

# Sincronizar los cambios
sync

# Reiniciar el sistema
exec /sbin/init
```

## Método 2: Modo Recovery (Alternativo)

### 1. Acceder al modo Recovery

- En el menú GRUB, selecciona "Advanced options for Debian GNU/Linux".
- Selecciona la entrada que dice "(recovery mode)".

### 2. Seleccionar Root Shell

- En el menú de recuperación, selecciona "root - Drop to root shell prompt".

### 3. Remontar el sistema de archivos

```bash
mount -o remount,rw /
```

### 4. Cambiar la contraseña

```bash
passwd root
# Ingresa: redhat
```

### 5. Reiniciar

```bash
reboot
```

## Verificar el runlevel predeterminado

Después de reiniciar y hacer login con la nueva contraseña:

### Para Debian 12 (usa systemd)

```bash
# Ver el target actual
systemctl get-default

# Establecer runlevel gráfico (equivalente a runlevel 5)
systemctl set-default graphical.target

# O establecer runlevel multiusuario sin GUI (equivalente a runlevel 3)
systemctl set-default multi-user.target
```

### Correspondencia de runlevels

- Runlevel 3 → `multi-user.target`
- Runlevel 5 → `graphical.target`

## Resumen de tareas del administrador

Una vez que hayas cambiado la contraseña y accedido al sistema:

### 1. Monitoreo de red

```bash
# Instalar herramientas básicas
apt update
apt install net-tools iftop nethogs nmap

# Monitorear tráfico
iftop
nethogs
```

### 2. Backup y Restore

```bash
# Instalar herramientas de backup
apt install rsync tar

# Ejemplo de backup básico
tar -czf /backup/sistema-$(date +%Y%m%d).tar.gz /etc /home

# Backup con rsync
rsync -av --delete /origen/ /destino/
```

> **Nota importante:** La contraseña "redhat" es solo para propósitos de práctica. En un entorno de producción, siempre usa contraseñas seguras y complejas.

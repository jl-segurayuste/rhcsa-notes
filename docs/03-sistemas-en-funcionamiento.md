# Uso de sistemas en funcionamiento

## Arranque, reinicio y apagado normal de un sistema

En la administración de sistemas Unix/Linux, el arranque, reinicio y apagado del sistema son operaciones fundamentales.

### Arranque del sistema

- **Arrancar el sistema** (este comando reiniciará el sistema):
  ```shell
  sudo reboot
  ```

### Reinicio del sistema

- **Reiniciar el sistema inmediatamente:**
  ```shell
  sudo shutdown -r now
  ```
- **Programar un reinicio en un momento específico:**
  ```shell
  sudo shutdown -r 21:30
  ```
  Este comando programará un reinicio a las 21:30.

### Apagado del sistema

- **Apagar el sistema inmediatamente:**
  ```shell
  sudo shutdown -h now
  ```
- **Programar un apagado en un momento específico:**
  ```shell
  sudo shutdown -h 22:00
  ```
  Este comando programará un apagado a las 22:00.

### Otros comandos relacionados

- **Cancelar un reinicio o apagado programado:**
  ```shell
  sudo shutdown -c
  ```
- **Reiniciar inmediatamente sin preguntas:**
  ```shell
  sudo reboot -f
  ```
- **Apagar inmediatamente sin preguntas:**
  ```shell
  sudo poweroff -f
  ```

## Inicio manual del sistema en diferentes destinos (targets)

En sistemas Unix/Linux, puedes iniciar el sistema manualmente en diferentes destinos utilizando el comando `init` o su sucesor, `systemd`.

### Usando `init`

- **Iniciar en el nivel de ejecución 3 (modo texto):**
  ```shell
  sudo init 3
  ```
- **Iniciar en el nivel de ejecución 5 (modo gráfico):**
  ```shell
  sudo init 5
  ```

### Usando `systemd`

- **Iniciar en el destino `multi-user.target` (modo texto):**
  ```shell
  sudo systemctl isolate multi-user.target
  ```
- **Iniciar en el destino `graphical.target` (modo gráfico):**
  ```shell
  sudo systemctl isolate graphical.target
  ```

Para familiarizarte con los diferentes niveles de ejecución y objetivos en `systemd`, y practicar el uso de comandos como `systemctl isolate`:

1. **Entender los niveles de ejecución (`runlevels`) en `systemd`:**
   - Los niveles de ejecución en `systemd` se denominan "objetivos" (`targets`).
   - Cada objetivo define un conjunto específico de servicios que deben ejecutarse cuando se alcanza ese nivel.
   - Los objetivos principales incluyen `multi-user.target` (equivalente al nivel de ejecución 3 en sistemas SysV), `graphical.target` (nivel de ejecución 5), `rescue.target` (modo de rescate, similar al nivel de ejecución 1) y `emergency.target` (modo de emergencia, similar al nivel de ejecución 1 pero más limitado).
2. **Explorar los objetivos de `systemd`:**
   - Listar todos los objetivos disponibles:
     ```plain text
     systemctl list-unit-files --type=target
     ```
     Lista todos los targets cargados en el sistema.
     ```bash
     systemctl --type=service
     ```
     Listar unidades no cargadas:
     ```bash
     systemctl list-units --failed
     ```
3. **Cambiar el objetivo del sistema:**
   - Puedes cambiar el objetivo del sistema en tiempo de ejecución con `systemctl isolate`. Por ejemplo, esto cambia el sistema al objetivo `multi-user.target` (similar al modo de usuario múltiple, sin interfaz gráfica):
     ```plain text
     sudo systemctl isolate multi-user.target
     ```
4. **Practicar con diferentes objetivos:**
   - Experimenta cambiando entre diferentes objetivos para comprender cómo afectan al comportamiento del sistema.
   - Puedes cambiar al objetivo de rescate (`rescue.target`) para realizar tareas de mantenimiento o solución de problemas.
   - Prueba el objetivo de emergencia (`emergency.target`) para situaciones críticas donde solo se necesita un entorno mínimo.

Los cambios de objetivo con `systemctl isolate` afectan al sistema en tiempo de ejecución y no persisten después del reinicio. Si deseas cambiar el objetivo predeterminado al inicio, puedes utilizar `systemctl set-default`.

| Objetivo de systemd | Nivel de ejecución (runlevel) |
|---|---|
| poweroff.target | 0 |
| rescue.target | 1 |
| multi-user.target | 3 |
| graphical.target | 5 |
| reboot.target | 6 |

**Notas:**

- Los niveles de ejecución en `init` y los destinos en `systemd` indican diferentes modos de operación del sistema. El nivel 3 generalmente representa el modo texto y el nivel 5 representa el modo gráfico en sistemas que usan `init`.
- `systemctl isolate` en `systemd` cambia a un destino específico, con un efecto similar a cambiar entre los niveles de ejecución en sistemas basados en `init`.

En sistemas modernos, especialmente aquellos que utilizan `systemd`, la práctica común es usar `systemctl` para cambiar entre destinos en lugar de `init`.

## Interrumpir el proceso de arranque para obtener acceso a un sistema

Para interrumpir el proceso de arranque y obtener acceso a un sistema en sistemas basados en `init`, puedes utilizar el modo de usuario único o el modo de rescate. Esto te permite iniciar el sistema en un entorno mínimo y de solo lectura, lo que facilita la resolución de problemas.

### Usando `init` / `systemd`

1. Reinicia el sistema y espera a que aparezca la pantalla de selección de sistema operativo (si está disponible).
2. Durante el conteo regresivo, presiona la tecla `Esc` o `Shift` para interrumpir el proceso de arranque.
3. Selecciona la entrada del kernel que deseas arrancar y presiona la tecla `e` para editar.
4. Agrega `single` o `init=/bin/bash` al final de la línea que comienza con `linux`. Luego, presiona `Ctrl+X` para iniciar en modo de usuario único o en modo de consola de emergencia.

Estos métodos permiten acceder al sistema en un entorno mínimo y de solo lectura, útil para tareas de mantenimiento o solución de problemas. Este acceso tiene privilegios elevados, así que úsalo con responsabilidad.

## GRUB 2

> **Nota (para el examen RHCSA):** asegúrate de entender el contenido del fichero `/etc/default/grub`. Es la parte más importante de la configuración de GRUB 2.

Ejemplo del fichero de configuración:

```bash
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/rootlv rd.lvm.lv=rootvg/swaplv rd.lvm.lv=rootvg/usrlv rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
```

Significado de cada parámetro:

1. **`GRUB_TIMEOUT=5`**: establece el tiempo (en segundos) que GRUB espera antes de iniciar automáticamente el sistema operativo predeterminado. En este caso, espera 5 segundos.
2. **`GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"`**: establece el nombre del distribuidor de GRUB, extraído mediante `sed` desde `/etc/system-release`.
3. **`GRUB_DEFAULT=saved`**: especifica el sistema operativo predeterminado que se iniciará. El valor `saved` significa que GRUB recordará el último sistema operativo que se inició con éxito y lo usará como predeterminado para el próximo arranque.
4. **`GRUB_DISABLE_SUBMENU=true`**: si está configurado como `true`, GRUB no mostrará submenús en la lista de sistemas operativos durante el arranque, simplificando el menú.
5. **`GRUB_TERMINAL_OUTPUT="console"`**: establece la salida del terminal de GRUB en la consola. Útil para servidores y sistemas sin interfaz gráfica durante el arranque.
6. **`GRUB_CMDLINE_LINUX="crashkernel=auto rd.lvm.lv=rootvg/rootlv rd.lvm.lv=rootvg/swaplv rd.lvm.lv=rootvg/usrlv rhgb quiet"`**: especifica las opciones de la línea de comandos del kernel:
   - `crashkernel=auto`: configura automáticamente el tamaño de la memoria reservada para el kernel de rescate.
   - `rd.lvm.lv=rootvg/rootlv`: le dice al initramfs (dracut) que active el volumen lógico `rootlv` de `rootvg` durante el arranque. Cuál es la raíz lo indica `root=` (en las entradas BLS); `rd.lvm.lv` solo lo activa.
   - `rd.lvm.lv=rootvg/swaplv`: activa también el volumen de swap en el initramfs (necesario para hibernar).
   - `rd.lvm.lv=rootvg/usrlv`: activa el volumen de `/usr`, que tiene que estar disponible antes de pasar al sistema real.
   - `rhgb`: habilita el Red Hat Graphical Boot.
   - `quiet`: reduce la cantidad de mensajes de arranque que se muestran en la consola.
7. **`GRUB_DISABLE_RECOVERY="true"`**: si está configurado como `true`, deshabilita la opción de recuperación en el menú de GRUB (no se mostrarán entradas de menú para modos de recuperación).

> **Consejo:** en el examen, es importante saber de inmediato si algo no funciona bien. Para lograr esto, es una buena idea eliminar las opciones de arranque `rhgb` y `quiet`. Sin estas opciones, no tendrás que adivinar por qué tu servidor tarda mucho tiempo después de un reinicio; simplemente podrás verlo.

## Modificar el cargador de arranque del sistema

Modificar el cargador de arranque en sistemas basados en Red Hat (RHEL, CentOS, Fedora) generalmente implica trabajar con GRUB (Grand Unified Bootloader).

### 1. Realizar una copia de seguridad

Antes de realizar cambios en el cargador de arranque, es recomendable realizar una copia de seguridad de la configuración actual:

```shell
sudo cp /etc/default/grub /etc/default/grub.bak
```

### 2. Editar el archivo de configuración de GRUB

```shell
sudo nano /etc/default/grub
```

En este archivo puedes ajustar diversas configuraciones, como el tiempo de espera del menú de arranque, las opciones de kernel, etc.

### 3. Aplicar cambios y actualizar GRUB

```shell
sudo grub2-mkconfig -o /boot/grub2/grub.cfg                # BIOS; también UEFI en RHEL 9.3+ y RHEL 10
sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg       # UEFI en RHEL 8 (y RHEL 9 antes de la 9.3)
```

`/boot/grub/grub.cfg` (sin el 2) es la ruta de Debian/Ubuntu, no de RHEL. Para cambiar solo los
argumentos del kernel, en RHEL 8+ (entradas BLS) lo recomendado es `grubby`, que no necesita regenerar
`grub.cfg`:

```shell
sudo grubby --update-kernel=ALL --args="quiet"         # añadir a todos los kernels
sudo grubby --update-kernel=ALL --remove-args="rhgb"   # quitar
sudo grubby --info=DEFAULT                             # comprobar
```

### 4. Instalar GRUB en el sector de arranque del disco

Si necesitas reinstalar GRUB en el sector de arranque del disco (reemplaza `/dev/sdX` con la identificación de tu disco de arranque):

```shell
sudo grub2-install /dev/sdX   # Solo en arranque BIOS (en RHEL la orden es siempre grub2-install)
```

En UEFI no se usa `grub2-install`: se reinstalan los paquetes `grub2-efi-x64` y `shim-x64`
(`dnf reinstall grub2-efi-x64 shim-x64`). `grub-install` (sin el 2) es de Debian/Ubuntu.

### 5. Verificar la configuración

Puedes verificar que los cambios se hayan aplicado correctamente reiniciando el sistema o revisando el archivo de configuración de GRUB.

### Notas adicionales

- Si estás utilizando UEFI en lugar de BIOS, los pasos y los comandos pueden ser diferentes.
- Realizar cambios en el cargador de arranque puede tener un impacto significativo en la capacidad de arranque del sistema.

## Identificar procesos con uso intensivo de CPU y memoria, y eliminarlos

Identifica y elimina procesos con uso intensivo de CPU y memoria con `top`, `ps` y `kill`.

### Identificar procesos con `top`

1. Abre una terminal y ejecuta `top`.
2. Observa la lista de procesos. Puedes ordenarlos por uso de CPU o memoria presionando `Shift+P` (ordenar por CPU) y `Shift+M` (ordenar por memoria).
3. Identifica el PID (identificador de proceso) del proceso que deseas eliminar.
4. Presiona `k` e ingresa el PID del proceso a eliminar, luego pulsa `Enter`.

### Identificar procesos con `ps` y `grep`

- Listar procesos ordenados por uso de CPU:
  ```shell
  ps aux --sort=-%cpu
  ```
- Ordenar por uso de memoria:
  ```shell
  ps aux --sort=-%mem
  ```
- Mostrar una lista completa de todos los procesos con el comando exacto usado para iniciarlos:
  ```shell
  ps -ef
  ```
- Eliminar el proceso (reemplaza `<PID>` con el PID real):
  ```shell
  kill -9 <PID>
  ```

`kill -9` fuerza la terminación inmediata del proceso; úsalo con precaución, ya que la terminación abrupta de ciertos procesos podría afectar la estabilidad del sistema.

## Ajustar la programación de los procesos

Para ajustar la prioridad de ejecución de procesos en sistemas Unix/Linux, puedes utilizar `nice` y `renice`.

### `nice`

`nice` ajusta la prioridad de ejecución de un proceso antes de iniciarlo. Cuanto más alto sea el valor, menor será la prioridad del proceso.

```shell
nice -n valor_complejidad comando
```

- `valor_complejidad` puede variar de -20 (mayor prioridad) a 19 (menor prioridad).
- `comando` es el comando que deseas ejecutar con la nueva prioridad.

Ejemplo:

```shell
nice -n 10 ./mi_programa
```

### `renice`

`renice` ajusta la prioridad de un proceso ya en ejecución.

```shell
renice valor_complejidad -p PID
```

Ejemplo (ajustar la prioridad del proceso con PID 1234 a un valor de 5):

```shell
renice 5 -p 1234
```

Solo el superusuario (root) puede aumentar la prioridad de un proceso; cualquier usuario puede disminuir la prioridad de sus propios procesos.

## `pgrep`: búsqueda de procesos

`pgrep` es una utilidad de línea de comandos que se utiliza para buscar procesos en ejecución basados en un patrón o criterio de búsqueda. Devuelve los PIDs de los procesos que coinciden con el criterio especificado.

### Usos comunes de `pgrep`

1. **Buscar procesos por nombre:**
   ```shell
   pgrep firefox
   ```
2. **Buscar procesos por usuario:**
   ```shell
   pgrep -u username
   ```
3. **Buscar procesos por grupo:**
   ```shell
   pgrep -G groupname
   ```
4. **Buscar coincidencias parciales (expresiones regulares):**
   ```shell
   pgrep -f "python script"
   ```
5. **Buscar procesos hijos de un PID específico:**
   ```shell
   pgrep -P 1234
   ```
6. **Mostrar nombres de procesos junto a sus PIDs:**
   ```shell
   pgrep -l ssh
   ```

### Ejemplos prácticos

- Encontrar todos los procesos con el nombre "apache2": `pgrep apache2`
- Encontrar procesos de un usuario específico: `pgrep -u root`
- Encontrar procesos que coincidan con una expresión regular: `pgrep -f "nginx: worker process"`
- Listar procesos con sus nombres: `pgrep -l bash`

`pgrep` es una herramienta útil para buscar y gestionar procesos, permitiendo encontrarlos rápidamente por nombre, usuario, grupo o expresiones regulares.

## Señales de procesos: SIGTERM, SIGKILL y SIGHUP

En sistemas Unix y Linux, los comandos para enviar señales a los procesos se utilizan para gestionar su comportamiento. Las señales SIGTERM, SIGKILL y SIGHUP son especialmente comunes.

### SIGTERM (señal 15)

Señal predeterminada para pedir educadamente a un proceso que se detenga. Los procesos pueden capturar esta señal y realizar una limpieza antes de detenerse.

```shell
kill -15 PID
# o simplemente:
kill PID
```

Ejemplo: detener un proceso con el PID 1234 de manera educada: `kill 1234`.

### SIGKILL (señal 9)

Fuerza la terminación inmediata de un proceso. No puede ser capturada ni ignorada, por lo que es útil cuando un proceso no responde a SIGTERM.

```shell
kill -9 PID
```

Ejemplo: `kill -9 1234`.

### SIGHUP (señal 1)

Se utiliza comúnmente para hacer que un proceso recargue su configuración, sin tener que detenerlo y reiniciarlo.

```shell
kill -1 PID
```

Ejemplo: `kill -1 1234`.

### Uso avanzado con `pkill`

`pkill` es similar a `kill`, pero permite enviar señales a procesos por nombre en lugar de PID.

```shell
pkill -15 myapp   # SIGTERM
pkill -9 myapp    # SIGKILL
pkill -1 myapp    # SIGHUP
```

### Resumen

- SIGTERM (15) pide a un proceso que se detenga amablemente.
- SIGKILL (9) fuerza la terminación inmediata de un proceso.
- SIGHUP (1) se usa para hacer que un proceso recargue su configuración.

## `killall` vs `pkill`

Ambos comandos se usan para enviar señales a procesos, pero cada uno tiene sus propias características.

### `killall`

Envía señales a todos los procesos con un nombre especificado.

```shell
killall [opciones] nombre_proceso
```

Ejemplo (terminar todos los procesos de `myapp`):

```shell
killall myapp
```

### `pkill`

Similar a `killall`, pero ofrece más flexibilidad: permite enviar señales a procesos basándose en el nombre, el ID del usuario, el grupo, el terminal desde el que fue iniciado, entre otros.

```shell
pkill [opciones] [patrón]
```

Ejemplo (terminar todos los procesos con el nombre `myapp`):

```shell
pkill myapp
```

### Comparación y ejemplos prácticos

**Terminación por nombre:**

```shell
killall myapp
pkill myapp
```

**Enviar SIGKILL (9) para forzar la terminación:**

```shell
killall -9 myapp
pkill -9 myapp
```

**Filtrado avanzado con `pkill`:**

- Por ID de usuario (terminar procesos de `myapp` del usuario `user1`):
  ```shell
  pkill -u user1 myapp
  ```
- Por terminal (terminar procesos de `myapp` iniciados desde `tty1`):
  ```shell
  pkill -t tty1 myapp
  ```

### Ejemplo completo

Escenario: tienes una aplicación `myapp` que se ejecuta con varios procesos y quieres:

1. Terminar todos los procesos con `killall`:
   ```shell
   killall myapp
   ```
2. Terminar procesos de `myapp` del usuario `user1` con `pkill`:
   ```shell
   pkill -u user1 myapp
   ```
3. Forzar la terminación de todos los procesos de `myapp` con SIGKILL:
   ```shell
   pkill -9 myapp
   ```

### Resumen

- **`killall`**: envía señales a todos los procesos con un nombre especificado.
- **`pkill`**: envía señales a procesos basándose en múltiples criterios (nombre, usuario, terminal, etc.).

## Estados de procesos en `top`

El comando `top` muestra información en tiempo real sobre los procesos del sistema, incluido su estado, indicado por una letra en la columna "S":

1. **Running (R)**: el proceso está activo y utilizando tiempo de CPU, o está en la cola de procesos ejecutables esperando para recibir servicio. Ejemplo: un proceso realizando cálculos intensivos.
2. **Sleeping (S)**: el proceso está esperando que un evento se complete (p. ej. E/S). Ejemplo: un proceso esperando entrada del usuario o respuesta de un disco.
3. **Uninterruptable sleep (D)**: sueño que no puede ser interrumpido, generalmente mientras se espera la finalización de una operación de E/S que no puede interrumpirse. Ejemplo: espera de lectura/escritura en disco duro.
4. **Stopped (T)**: el proceso ha sido detenido, normalmente mediante `Ctrl-Z` en un shell interactivo.
5. **Zombie (Z)**: el proceso ha terminado, pero su entrada en la tabla de procesos no ha sido eliminada por su proceso padre.

### Ejemplo de salida de `top`

```plain text
top - 10:35:02 up 1 day,  2:34,  3 users,  load average: 0.09, 0.08, 0.09
Tasks: 104 total,   1 running, 103 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.3 us,  0.1 sy,  0.0 ni, 99.5 id,  0.0 wa,  0.0 hi,  0.1 si,  0.0 st
KiB Mem :  2048052 total,   891036 free,   546388 used,   609628 buff/cache
KiB Swap:  2097148 total,  2097148 free,        0 used.  1227924 avail Mem

PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
1234 root      20   0  162444   2344   1232 R   0.7  0.1   0:00.34 top
5678 user      20   0  123456   5678   4321 S   0.3  0.3   0:12.34 process_name
```

En la columna "S": **R** = Running, **S** = Sleeping, **D** = Uninterruptable sleep, **T** = Stopped, **Z** = Zombie.

### Cómo interpretar los estados

- **Running (R)**: idealmente deberías tener solo unos pocos procesos en ejecución al mismo tiempo; demasiados pueden ralentizar el sistema.
- **Sleeping (S)**: la mayoría de los procesos estarán en este estado, ya que pasan la mayor parte de su tiempo esperando eventos.
- **Uninterruptable sleep (D)**: muchos procesos en este estado pueden indicar un problema con el hardware o los dispositivos de E/S (disco lento, fallos de red).
- **Stopped (T)**: se usa para pausar temporalmente procesos; muchos procesos en este estado pueden deberse a intervenciones manuales con `Ctrl-Z`.
- **Zombie (Z)**: unos pocos zombis pueden no ser preocupantes, pero un gran número puede indicar que los procesos padre no gestionan correctamente la finalización de sus hijos.

### Ejemplos prácticos de estados de procesos

**Estado "Running" (R):**

```shell
while true; do echo "Running"; done &
```

Bucle infinito en segundo plano; el proceso estará continuamente en estado "Running".

**Estado "Sleeping" (S):**

```shell
sleep 100 &
```

El proceso estará en "Sleeping" mientras espera que el tiempo de sueño se complete.

**Estado "Uninterruptable sleep" (D):**

```shell
dd if=/dev/zero of=/dev/null &
```

Copia datos indefinidamente y puede poner al proceso en estado "D" si se usa con E/S intensiva.

**Estado "Stopped" (T):**

```shell
cat /dev/urandom > /dev/null
```

Mientras se ejecuta, presiona `Ctrl-Z`; el proceso pasará al estado "Stopped" hasta que se reanude con `fg` o `bg`.

**Estado "Zombie" (Z):**

```shell
bash -c 'sleep 1 & exec sleep 30' &
sleep 2; ps -eo pid,ppid,stat,cmd | awk '$3 ~ /^Z/'     # [sleep] <defunct>
```

El hijo (`sleep 1`) termina al segundo, pero su padre se ha sustituido con `exec sleep 30`, que no
recoge el estado de sus hijos: el hijo queda como zombi hasta que el padre termina. (Un script que lanza
`sleep 5 &` y acaba no deja zombi: el hijo queda huérfano, lo adopta systemd y lo recoge.) Un zombi no se
mata con `kill`: hay que terminar o reiniciar a su padre.

### Cómo verificar y gestionar estados

Verificar procesos zombis con `ps`:

```shell
ps -eo pid,ppid,stat,cmd | awk '$3 ~ /^Z/'
```

(`ps aux | grep Z` también encuentra cualquier línea con una Z mayúscula, no solo los zombis.)

Gestionar procesos:

- **Terminar procesos:**
  ```shell
  kill <PID>
  ```
- **Mover procesos entre primer plano y segundo plano:**
  ```shell
  bg %<job_number>   # Mover un trabajo al segundo plano
  fg %<job_number>   # Mover un trabajo al primer plano
  ```
- **Parar temporalmente un proceso:**
  ```shell
  kill -STOP <PID>
  ```
- **Reanudar un proceso parado:**
  ```shell
  kill -CONT <PID>
  ```

## `tuned`: ajuste de rendimiento

`tuned` es una herramienta muy útil en RHEL 8 para ajustar el rendimiento del sistema de manera automática y dinámica.

### Instalación

```shell
sudo yum install tuned
# o
sudo dnf install tuned
```

### Iniciar y habilitar

```shell
sudo systemctl start tuned
sudo systemctl enable --now tuned
```

### Comandos principales

- **Listar perfiles disponibles:**
  ```shell
  sudo tuned-adm list
  ```
- **Aplicar un perfil** (por ejemplo, `throughput-performance`):
  ```shell
  sudo tuned-adm profile throughput-performance
  ```
- **Ver el perfil activo:**
  ```shell
  sudo tuned-adm active
  ```
- **Desactivar tuned** (volver a la configuración predeterminada del sistema):
  ```shell
  sudo tuned-adm off
  ```
- **Recomendar un perfil según el hardware y la carga de trabajo actual:**
  ```shell
  sudo tuned-adm recommend
  ```

### Perfiles predefinidos en RHEL 8

- **balanced**: perfil equilibrado para la mayoría de los sistemas.
- **performance**: maximiza el rendimiento a expensas del consumo de energía.
- **powersave**: optimiza la configuración para ahorrar energía.
- **throughput-performance**: optimiza el rendimiento para operaciones intensivas de E/S.
- **latency-performance**: minimiza la latencia, ideal para aplicaciones sensibles a la latencia.
- **virtual-guest**: optimiza para máquinas virtuales como huéspedes.
- **virtual-host**: optimiza para servidores que ejecutan varias máquinas virtuales.

### Creación y personalización de perfiles

Si los perfiles predefinidos no cumplen tus necesidades, puedes crear perfiles personalizados:

1. **Crear un directorio para el perfil personalizado:**
   ```shell
   sudo mkdir /etc/tuned/my-custom-profile
   ```
2. **Crear el archivo de configuración del perfil:**
   ```shell
   sudo nano /etc/tuned/my-custom-profile/tuned.conf
   ```
   Ejemplo básico de `tuned.conf`:
   ```ini
   [main]
   summary=My Custom Performance Profile
   include=throughput-performance

   [cpu]
   governor=performance

   [disk]
   readahead=4096
   ```
   En este ejemplo, el perfil `my-custom-profile` incluye el perfil `throughput-performance` y añade configuraciones específicas para la CPU y el disco.
3. **Aplicar el perfil personalizado:**
   ```shell
   sudo tuned-adm profile my-custom-profile
   ```

### Ajustes adicionales

- **Mostrar la configuración detallada del perfil activo:**
  ```shell
  sudo tuned-adm active
  ```
- **Ver el estado del servicio tuned:**
  ```shell
  sudo systemctl status tuned
  ```
- **Cambiar dinámicamente entre perfiles sin reiniciar el sistema:**
  ```shell
  sudo tuned-adm profile latency-performance
  ```
- **Excluir dispositivos específicos** de ser gestionados por tuned, en el archivo de configuración del perfil:
  ```ini
  [devices]
  exclude=sdh,sdi
  ```

### Ejemplo de uso práctico

Si tienes un servidor de base de datos que necesita un alto rendimiento de E/S:

```shell
sudo tuned-adm profile throughput-performance
```

Algunos cambios pueden requerir un reinicio para aplicarse completamente:

```shell
sudo reboot
```

## Gestionar los perfiles de ajuste con `sysctl`

`sysctl` es una utilidad que permite visualizar y modificar parámetros del kernel en tiempo de ejecución.

- **Ver todos los parámetros:**
  ```shell
  sysctl -a
  ```
- **Buscar un parámetro específico:**
  ```shell
  sysctl nombre_del_parametro
  ```
- **Modificar un parámetro en tiempo de ejecución:**
  ```shell
  sudo sysctl -w nombre_del_parametro=nuevo_valor
  ```
- **Guardar cambios de manera persistente:**
  1. Edita `/etc/sysctl.conf`.
  2. Agrega o modifica la línea correspondiente al parámetro.
  3. Aplica los cambios:
     ```shell
     sudo sysctl -p
     ```

### Archivos de configuración específicos del sistema

- **`/etc/security/limits.conf`**: establece límites de recursos para usuarios y procesos.
- **`/etc/sysctl.conf` o `/etc/sysctl.d/`**: contienen configuraciones relacionadas con el kernel y el rendimiento del sistema.
- **`/etc/security/limits.d/`**: al igual que `limits.conf`, permite establecer límites de recursos.
- **`/etc/security/namespace.conf`**: directorios polinstanciados de `pam_namespace` (por ejemplo, un `/tmp` privado por usuario); no tiene que ver con contenedores.

Ajustar parámetros del sistema puede tener un impacto significativo en el rendimiento y la estabilidad, así que comprende su efecto antes de realizar cambios.

## Localizar e interpretar los diarios y archivos de registro del sistema

### Archivos de unidad de systemd: directorios y precedencia

Los archivos de unidad (unit files) pueden existir en múltiples directorios:

- `/run/systemd/system`
- `/etc/systemd/system`
- `/usr/lib/systemd/system`

**Precedencia** (cuando un archivo de unidad con el mismo nombre existe en más de un directorio):

- **Más alta precedencia: `/etc/systemd/system`** — configuraciones del administrador; mandan sobre todo lo demás.
- **Segunda precedencia: `/run/systemd/system`** — unidades de tiempo de ejecución (se pierden al reiniciar).
- **Más baja precedencia: `/usr/lib/systemd/system`** — las que instalan los paquetes.

**Ejemplo práctico:** si `example.service` existe en los tres directorios, systemd usa el de `/etc/systemd/system`. Por eso, para cambiar una unidad de un paquete se copia a `/etc/systemd/system` (o se usa `systemctl edit`, que crea un *drop-in* en `/etc/systemd/system/example.service.d/`) y nunca se edita la de `/usr/lib`.

Resumen: precedencia `/etc` > `/run` > `/usr/lib` (`systemd-analyze unit-paths` muestra el orden exacto).

- `/etc`: configuraciones personalizadas por el administrador del sistema.
- `/run`: configuraciones temporales y generadas en el arranque.
- `/usr/lib`: configuraciones predeterminadas proporcionadas por el sistema.

## Archivos de unidad de servicio de Systemd (Systemd Service Unit)

Cada archivo de unidad contiene varias secciones que especifican diversas configuraciones y dependencias.

### Sección `[Unit]`

Describe la unidad y define sus dependencias con otras unidades del sistema.

- **After**: especifica que esta unidad debe iniciarse después de otra unidad mencionada. Por ejemplo, `After=network.target` indica que la unidad actual debe iniciarse después de que se haya iniciado la unidad de red.
- **Before**: (opcional) indica que esta unidad debe iniciarse antes de la unidad mencionada. Por ejemplo, `Before=httpd.service`.

```ini
[Unit]
Description=Mi Servicio Personalizado
After=network.target
Before=httpd.service
```

### Sección `[Service]`

Describe cómo se debe iniciar, detener y gestionar el servicio.

- **ExecStart**: comando que se ejecuta para iniciar el servicio.
- **ExecStop**: comando que se ejecuta para detener el servicio.
- **Type**: especifica cómo se debe iniciar el proceso. Tipos comunes:
  - **forking**: usado para procesos daemon que bifurcan un proceso hijo.
  - **oneshot**: utilizado para ejecutar un solo comando y luego salir.

```ini
[Service]
ExecStart=/usr/bin/mi_servicio_inicio
ExecStop=/usr/bin/mi_servicio_parada
Type=forking
```

### Sección `[Install]`

Indica en qué objetivo (target) debe iniciarse esta unidad.

- **WantedBy**: especifica el objetivo en el que esta unidad debe ser incluida (por ejemplo, `multi-user.target` o `graphical.target`).

```ini
[Install]
WantedBy=multi-user.target
```

### Ejemplo completo de un archivo de unidad

```ini
[Unit]
Description=Mi Servicio Personalizado
After=network.target
Before=httpd.service

[Service]
ExecStart=/usr/bin/mi_servicio_inicio
ExecStop=/usr/bin/mi_servicio_parada
Type=forking

[Install]
WantedBy=multi-user.target
```

**Detalles adicionales:**

- **After**: asegura que el servicio de red esté activo antes de iniciar este servicio.
- **Before**: indica que este servicio debe iniciarse antes del servicio HTTP.
- **ExecStart** / **ExecStop**: definen los comandos que inician y detienen el servicio.
- **Type=forking**: indica que el servicio es un proceso daemon que se bifurca.
- **WantedBy=multi-user.target**: indica que este servicio debe iniciarse en el nivel de ejecución multiusuario.

Para más detalles sobre las opciones disponibles y configuraciones avanzadas: `man 5 systemd.service`.

## Archivos de unidad de montaje de Systemd (Systemd Mount Unit)

Especifican cómo y dónde se deben montar los sistemas de archivos. Ejemplo: el archivo `tmp.mount`, usado para montar un sistema de archivos temporal (`tmpfs`) en `/tmp`.

### Sección `[Unit]`

Define metadatos y dependencias del servicio de montaje:

- **Description**: breve descripción del propósito de la unidad.
- **Documentation**: enlaces a documentación relevante.
- **ConditionPathIsSymbolicLink**: asegura que el montaje no se realice si `/tmp` es un enlace simbólico.
- **DefaultDependencies**: define si se deben aplicar las dependencias predeterminadas.
- **Conflicts**: especifica con qué otras unidades existe un conflicto, impidiendo que se activen simultáneamente.
- **Before** y **After**: establecen el orden relativo de inicio respecto a otras unidades.

```ini
[Unit]
Description=Temporary Directory (/tmp)
Documentation=man:hier(7)
Documentation=https://www.freedesktop.org/wiki/Software/systemd/APIFileSystems
ConditionPathIsSymbolicLink=!/tmp
DefaultDependencies=no
Conflicts=umount.target
Before=local-fs.target umount.target
After=swap.target
```

### Sección `[Mount]`

Especifica los detalles técnicos de cómo se debe realizar el montaje:

- **What**: dispositivo o recurso que se montará.
- **Where**: punto de montaje en el sistema de archivos local.
- **Type**: tipo de sistema de archivos.
- **Options**: opciones de montaje que se aplicarán.

```ini
[Mount]
What=tmpfs
Where=/tmp
Type=tmpfs
Options=mode=1777,strictatime,nosuid,nodev
```

**Explicación adicional:**

- `What=tmpfs`: tmpfs es un sistema de archivos temporal almacenado en RAM, lo que permite un acceso muy rápido.
- `Where=/tmp`: `/tmp` es el directorio estándar en sistemas UNIX/Linux para almacenamiento temporal.
- `Options=mode=1777,strictatime,nosuid,nodev`:
  - `mode=1777`: todos los usuarios pueden escribir en este directorio, pero solo el propietario de un archivo puede borrarlo.
  - `strictatime`: actualiza el tiempo de acceso en cada lectura.
  - `nosuid`: no permite la ejecución de set-user-identifier o set-group-identifier.
  - `nodev`: impide la creación de dispositivos en este sistema de archivos.

Los archivos de unidad de montaje de Systemd son una herramienta poderosa para la gestión sistemática de montajes en sistemas basados en Linux, asegurando que los recursos estén disponibles conforme se requieran y manteniendo el sistema organizado y seguro.

## Archivos de unidad de socket de Systemd (Systemd Socket Unit)

Los archivos de unidad de socket permiten que las aplicaciones se comuniquen entre sí, definidos como archivos o puertos a través de los cuales Systemd escuchará conexiones entrantes. Esto permite que un servicio no se ejecute continuamente, sino que se inicie solo cuando llega una conexión al socket especificado. Cada archivo de socket necesita un archivo de servicio correspondiente para manejar la comunicación entrante. Ejemplo: `cockpit.socket`.

### Sección `[Unit]`

- **Description**: describe la función del socket.
- **Documentation**: enlaces a documentación relevante.
- **Wants**: especifica otros servicios que este socket desea tener disponibles antes de activarse.

```ini
[Unit]
Description=Cockpit Web Service Socket
Documentation=man:cockpit-ws(8)
Wants=cockpit-motd.service
```

### Sección `[Socket]`

- **ListenStream**: define el puerto TCP al que Systemd debe escuchar para conexiones entrantes.
- **ListenDatagram**: se usa para especificar un puerto UDP en lugar de TCP.

```ini
[Socket]
ListenStream=9090
ExecStartPost=-/usr/share/cockpit/motd/update-motd '' localhost
ExecStartPost=-/bin/ln -snf active.motd /run/cockpit/motd
ExecStopPost=-/bin/ln -snf /usr/share/cockpit/motd/inactive.motd /run/cockpit/motd
```

### Sección `[Install]`

- **WantedBy**: indica el objetivo en el que este socket debería estar activo.

```ini
[Install]
WantedBy=sockets.target
```

**Explicación adicional:**

- `ListenStream=9090`: el socket estará escuchando en el puerto TCP 9090 para conexiones entrantes.
- `ExecStartPost` / `ExecStopPost`: comandos que se ejecutan después de que el socket se inicia/detiene.
- `WantedBy=sockets.target`: el socket debería estar activo cuando se active el objetivo `sockets.target`.

Los archivos de unidad de socket en Systemd son una parte esencial de la arquitectura de comunicación del sistema, permitiendo que los servicios se activen según la demanda de conexiones entrantes, mejorando la eficiencia al evitar que los servicios se ejecuten continuamente si no se están utilizando.

## Systemd target units

Los archivos de unidad de destino (target units) se utilizan para construir la funcionalidad necesaria en el servidor, cargando las unidades en el orden correcto y en el momento adecuado. Una unidad de destino se utiliza para definir grupos de unidades: algunos objetivos definen el estado en el que debe iniciarse un servidor (comparables a los runlevels de versiones anteriores de RHEL); otros son simplemente un grupo de servicios que facilita la gestión de las unidades necesarias para obtener funcionalidades específicas.

Un ejemplo de objetivo es `printer.target`, usado para iniciar o detener fácilmente todas las unidades necesarias para proporcionar funcionalidad de impresión. Los objetivos pueden tener dependencias entre sí, definidas en el archivo de unidad de destino — por ejemplo, `basic.target` define todas las unidades que siempre deben iniciarse. Puedes usar `systemctl list-dependencies` para obtener una visión general de las dependencias existentes.

Ejemplo del archivo de unidad de destino `multi-user.target`, que define el estado operativo normal de un servidor RHEL:

```ini
[Unit]
Description=Multi-User System
Documentation=man:systemd.special(7)
Requires=basic.target
Conflicts=rescue.service rescue.target
After=basic.target rescue.service rescue.target
AllowIsolate=yes

[Install]
Alias=default.target
```

En este archivo se especifican los requisitos, conflictos y el orden de carga. La sección `[Install]` determina a qué objetivo se debe agregar la unidad al habilitarla con `systemctl enable`. Cuando se agrega una unidad a un objetivo, se crea un enlace simbólico en el directorio del objetivo en `/etc/systemd/system`. Por ejemplo, si habilitas el servicio `vsftpd` para que se inicie automáticamente, se creará un enlace simbólico `/etc/systemd/system/multi-user.target.wants/vsftpd.service` que apunta al archivo de unidad en `/usr/lib/systemd/system/vsftpd.service`. En terminología de Systemd, este enlace simbólico se conoce como un "want", porque define lo que el objetivo desea iniciar cuando se procesa.

### Managing Units Through Systemd — Ejercicio 11-1: administración de unidades con `systemctl`

1. `yum -y install vsftpd` — instala el servicio FTP muy seguro (VSFTPD).
2. `systemctl start vsftpd` — activa el servidor FTP en la máquina.
3. `systemctl status vsftpd` — muestra que el servicio `vsftpd` está actualmente operativo. En la línea `Loaded` se puede ver que el servicio está deshabilitado, lo que significa que no se activará en un reinicio. El "preset del proveedor" también aparece como deshabilitado, es decir, por defecto tras la instalación esta unidad no se habilita automáticamente.
4. `systemctl enable vsftpd` — crea un enlace simbólico en el directorio `wants` del objetivo multiusuario para asegurar que el servicio se inicie automáticamente después de un reinicio.
5. `systemctl status vsftpd` de nuevo — ahora el archivo de unidad ha cambiado de deshabilitado a habilitado.

### Diferentes tipos de estados

| Estado | Descripción |
|---|---|
| Loaded | El archivo de unidad ha sido procesado y la unidad está activa. |
| Active (running) | La unidad está en ejecución con uno o más procesos activos. |
| Active (exited) | La unidad ha completado con éxito una ejecución única. |
| Active (waiting) | La unidad está en ejecución y espera un evento. |
| Inactive (dead) | La unidad no está en ejecución. |
| Enabled | La unidad se iniciará durante el arranque. |
| Disabled | La unidad no se iniciará durante el arranque. |
| Static | La unidad no se puede habilitar pero puede ser iniciada automáticamente por otra unidad. |

### Comandos útiles de `systemctl`

| Comando | Descripción |
|---|---|
| `systemctl --type=service` | Muestra solo las unidades de servicio. |
| `systemctl list-units --type=service` | Muestra todas las unidades de servicio activas (mismo resultado que el comando anterior). |
| `systemctl list-units --type=service --all` | Muestra unidades de servicio inactivas y activas. |
| `systemctl --failed --type=service` | Muestra todos los servicios que han fallado. |
| `systemctl status -l your.service` | Muestra información detallada del estado sobre servicios. |

> **Nota:** para establecer vim como editor por defecto de `systemctl edit`:
> ```shell
> export SYSTEMD_EDITOR="/bin/vim"
> ```

## Diarios del sistema (configuración de logging)

Los diarios del sistema pueden variar según la distribución y la configuración del sistema. Algunas distribuciones utilizan `systemd` y sus herramientas asociadas, mientras que otras pueden usar `syslog` u otras soluciones.

### Utilizando `journalctl` con systemd

- Ver todos los mensajes de diario desde el último arranque:
  ```shell
  journalctl
  ```
- Ver mensajes de diario específicos de un servicio:
  ```shell
  journalctl -u nombre_del_servicio
  ```
- Ver mensajes de diario desde un tiempo específico:
  ```shell
  journalctl --since "2023-01-20 12:00:00"
  ```

### Utilizando archivos syslog

En RHEL rsyslog escribe en `/var/log/messages` (general), `/var/log/secure` (autenticación),
`/var/log/maillog`, `/var/log/cron` y `/var/log/boot.log`. Los nombres `syslog` y `auth.log` son de
Debian/Ubuntu. Puedes usar `cat`, `tail` o `less` para verlos:

```shell
sudo tail -f /var/log/messages
```

### Reading Log Files — archivos de registro más comunes

| Archivo de registro | Explicación |
|---|---|
| `/var/log/messages` | El archivo de registro más utilizado; es el archivo genérico donde se escriben la mayoría de los mensajes. |
| `/var/log/dmesg` | Contiene mensajes de registro del kernel. |
| `/var/log/secure` | Contiene mensajes relacionados con la autenticación (errores de autenticación en el servidor). |
| `/var/log/boot.log` | Mensajes relacionados con el inicio del sistema. |
| `/var/log/audit/audit.log` | Contiene mensajes de auditoría; SELinux escribe en este archivo. |
| `/var/log/maillog` | Mensajes relacionados con el correo electrónico. |
| `/var/log/samba` | Archivos de registro para el servicio Samba (Samba, por defecto, no se gestiona a través de rsyslog, escribe directamente en `/var/log`). |
| `/var/log/sssd` | Mensajes escritos por el servicio `sssd`, que desempeña un papel importante en el proceso de autenticación. |
| `/var/log/cups` | Mensajes de registro generados por el servicio de impresión CUPS. |
| `/var/log/httpd/` | Directorio con los archivos de registro escritos por el servidor web Apache (Apache escribe directamente, no a través de rsyslog). |

## Configuración de Rsyslog

El servicio `rsyslogd` en RHEL 8 es responsable de gestionar los registros (logs) del sistema. Para asegurarte de que la información se registre en la ubicación deseada, puedes configurar `rsyslogd` a través del archivo de configuración principal `/etc/rsyslog.conf`.

### Archivos de configuración de rsyslogd

- **Archivo principal**: `/etc/rsyslog.conf` es el archivo central donde se configura `rsyslogd`.
- **Directorio adicional**: el contenido de `/etc/rsyslog.d` también se incluye en la configuración. Este directorio puede llenarse instalando paquetes RPM que añaden configuraciones específicas de registro.
- **Opciones de inicio**: se pueden especificar en `/etc/sysconfig/rsyslog`, que generalmente contiene una línea `SYSLOGD_OPTIONS=""` donde puedes añadir parámetros de inicio.

### Secciones del archivo `rsyslog.conf`

1. **`#### MODULES ####`**: rsyslogd es modular; esta sección incluye módulos que mejoran las funciones soportadas.
2. **`#### GLOBAL DIRECTIVES ####`**: parámetros globales, como la ubicación de archivos auxiliares o el formato de la marca de tiempo predeterminada.
3. **`#### RULES ####`**: la parte más importante del archivo; aquí se definen las reglas que especifican qué información debe registrarse y a qué destino.

### Facilidades, prioridades y destinos de logs

- **Facilidad (facility)**: especifica una categoría de información que se registra (por compatibilidad con el servicio syslog heredado). Ejemplos: `auth`, `cron`, `daemon`, `kern`, `mail`, `user`, etc.
- **Prioridad (priority)**: define la severidad del mensaje. Al especificar una prioridad, por defecto se registran todos los mensajes con esa prioridad y todas las superiores. Ejemplos (orden de severidad): `debug`, `info`, `notice`, `warning`, `err`, `crit`, `alert`, `emerg`.
- **Destino (destination)**: dónde debe escribirse el mensaje. Los destinos típicos son archivos, pero también se pueden usar módulos de rsyslog para procesamiento adicional (servidores remotos, bases de datos, etc.).

### Tabla de facilidades de registro

| Facilidad | Descripción |
|---|---|
| auth/authpriv | Mensajes relacionados con la autenticación. |
| cron | Mensajes generados por el servicio `crond`. |
| daemon | Facilidad genérica que puede ser usada para daemons no especificados. |
| kern | Mensajes del kernel. |
| lpr | Mensajes generados por el sistema de impresión heredado `lpd`. |
| mail | Mensajes relacionados con el correo electrónico. |
| mark | Facilidad especial que se puede usar para escribir un marcador periódicamente. |
| news | Mensajes generados por el sistema de noticias NNTP. |
| security | Igual que auth/authpriv. No debería usarse más. |
| syslog | Mensajes generados por el sistema syslog. |
| user | Mensajes generados en el espacio de usuario. |
| uucp | Mensajes generados por el sistema heredado UUCP. |
| local0-7 | Mensajes generados por servicios configurados por cualquiera de las facilidades local0 a local7. |

### Tabla de prioridades de rsyslogd

| Prioridad | Descripción |
|---|---|
| debug | Mensajes de depuración que proporcionan la máxima cantidad de información sobre la operación del servicio. |
| info | Mensajes informativos sobre la operación normal del servicio. |
| notice | Mensajes informativos sobre elementos que podrían convertirse en un problema en el futuro. |
| warning / warn | Algo no está óptimo, pero aún no es un error real. |
| err / error | Ha ocurrido un error no crítico. |
| crit | Ha ocurrido un error crítico. |
| alert | La disponibilidad del servicio está a punto de ser interrumpida. Requiere atención inmediata. |
| emerg / panic | La disponibilidad del servicio ha sido interrumpida. Máxima severidad. Requiere acción inmediata. |

Estas prioridades se utilizan en la configuración de `rsyslogd` para definir cómo se deben gestionar y almacenar los mensajes de log. Ejemplo de configuración en `/etc/rsyslog.conf`:

```
# Enviar todos los mensajes de nivel err y superiores al archivo /var/log/errors.log
*.err                    /var/log/errors.log

# Enviar todos los mensajes de nivel crit y superiores a la consola
*.crit                   /dev/console

# Enviar todos los mensajes de nivel alert y superiores al correo del administrador
*.alert                  root@example.com
```

## Conservar los diarios del sistema (retención)

### Ajustar la retención de diarios con `systemd-journald`

1. **Ver la configuración actual** (uso de disco de los diarios):
   ```shell
   journalctl --disk-usage
   ```
2. **Modificar la configuración** en `/etc/systemd/journald.conf`:
   ```shell
   sudo nano /etc/systemd/journald.conf
   ```
   Opciones relevantes:
   - **`Storage=auto`**: determina el método de almacenamiento para los registros. "auto" permite que systemd elija automáticamente el método más apropiado según la disponibilidad de características del sistema.
   - **`Compress=yes`**: habilita la compresión de registros para ahorrar espacio en disco.
   - **`Seal=yes`**: si está activado, los registros se sellarán y no podrán modificarse después de un período de tiempo específico.
   - **`SplitMode=uid`**: divide los registros en directorios basados en el UID de cada sesión de usuario.
   - **`SyncIntervalSec=5m`**: establece el intervalo de sincronización de registros en disco (en este caso, cada 5 minutos).
   - **`RateLimitInterval=30s`** y **`RateLimitBurst=1000`**: controlan el límite de velocidad para la escritura de registros.
   - **`SystemMaxUse=`, `SystemKeepFree=`, `SystemMaxFileSize=`**: controlan el uso máximo de espacio en disco para registros del sistema, la cantidad mínima de espacio libre y el tamaño máximo de un archivo de registro.
   - **`RuntimeMaxUse=`, `RuntimeKeepFree=`, `RuntimeMaxFileSize=`**: lo mismo, pero para el diario volátil de `/run/log/journal` (el que se usa cuando el almacenamiento no es persistente y se pierde al reiniciar).
   - **`MaxRetentionSec=`**: define el tiempo máximo que se retendrán los registros en el sistema.
   - **`MaxFileSec=1month`**: define el tiempo después del cual se cerrará un archivo de registro.
   - **`ForwardToSyslog=yes`, `ForwardToKMsg=no`, `ForwardToConsole=no`, `ForwardToWall=yes`**: controlan si los registros se deben reenviar a otros destinos (syslog, kmsg, consola, wall).
   - **`TTYPath=/dev/console`**: especifica la ruta de la TTY a la que se deben enviar los registros.
   - **`MaxLevelStore=debug`, `MaxLevelSyslog=debug`, `MaxLevelKMsg=notice`, `MaxLevelConsole=info`, `MaxLevelWall=emerg`**: establecen el nivel máximo de gravedad para almacenar registros/reenviar a cada destino.
   - **`LineMax=48K`**: establece el tamaño máximo de una línea de registro.
3. **Aplicar cambios**, reiniciando `systemd-journald`:
   ```shell
   sudo systemctl restart systemd-journald
   ```

## Rotación de archivos de log (`logrotate`)

En sistemas que utilizan el sistema de archivos tradicional y syslog, la rotación de archivos puede ser gestionada por `logrotate`.

### Verificar configuración actual

```shell
cat /etc/logrotate.conf
```

### Configurar rotación de archivos

Puedes agregar configuraciones específicas para archivos de registro en `/etc/logrotate.d/`. En RHEL la de los logs de rsyslog (`/var/log/messages`, `secure`, `maillog`…) es `/etc/logrotate.d/rsyslog`; los ejemplos de abajo (`syslog`, `su root adm`, `www-data`) vienen de Debian/Ubuntu, donde se llaman así:

```shell
sudo nano /etc/logrotate.d/syslog
```

Contenido del archivo de configuración:

```
# see "man logrotate" for details
# rotate log files weekly
weekly
# use the adm group by default, since this is the owning group
# of /var/log/syslog.
su root adm
# keep 4 weeks worth of backlogs
rotate 4
# create new (empty) log files after rotating old ones
create
# use date as a suffix of the rotated file
#dateext
# uncomment this if you want your log files compressed
#compress
# packages drop log rotation information into this directory
include /etc/logrotate.d
# system-specific logs may be also be configured here.
# see "man logrotate" for details
```

Explicación de las directivas:

- **`weekly`**: los archivos de log se rotan semanalmente.
- **`rotate 4`**: mantiene los archivos de log rotados de las últimas 4 semanas; tras la cuarta rotación, el archivo más antiguo se elimina.
- **`create`**: después de rotar un archivo de log, crea uno nuevo vacío con los mismos permisos y propietario que el original.
- **`dateext`** (comentado): usa la fecha como sufijo en los archivos de log rotados (por ejemplo, `logfile-20240601`).
- **`compress`** (comentado): si se descomenta, `logrotate` comprimirá los archivos rotados usando gzip.
- **`include /etc/logrotate.d`**: incluye configuraciones adicionales de rotación de logs, permitiendo que paquetes RPM instalen sus propias configuraciones sin modificar el archivo principal.

### Ejecutar rotación manualmente

```shell
sudo logrotate -f /etc/logrotate.conf
```

Establecer políticas de retención adecuadas es importante para evitar el agotamiento del espacio en disco debido a registros no gestionados.

### Configuración global vs. configuración específica

La configuración de rotación de logs se puede realizar tanto a nivel global (para todos los archivos de logs) como de manera específica (para archivos o servicios individuales), mediante configuraciones globales en el archivo principal de logrotate y configuraciones específicas en archivos dentro de `logrotate.d`.

**Configuración global** — `/etc/logrotate.conf`:

```
# logrotate configuration file
# see "man logrotate" for details
# rotate log files weekly
weekly
# keep 4 weeks worth of backlogs
rotate 4
# create new (empty) log files after rotating old ones
create
# use date as a suffix of the rotated file
dateext
# uncomment this if you want your log files compressed
#compress
# include additional configurations from this directory
include /etc/logrotate.d
```

**Configuración específica** — ejemplo para nginx en `/etc/logrotate.d/nginx`:

```
/var/log/nginx/*.log {
daily
missingok
rotate 14
compress
delaycompress
notifempty
create 0640 www-data adm
sharedscripts
postrotate
[ -f /var/run/nginx.pid ] && kill -USR1 `cat /var/run/nginx.pid`
endscript
}
```

**Cómo se aplican las configuraciones:**

- **Global**: las configuraciones definidas en `/etc/logrotate.conf` se aplican a todos los archivos de logs a menos que haya configuraciones específicas que las sobrescriban.
- **Específica**: las configuraciones en archivos dentro de `/etc/logrotate.d/` se aplican únicamente a los archivos de log definidos en esos archivos, y pueden sobrescribir las directivas globales.

**Ejemplo de configuración mixta:**

- `/etc/logrotate.conf`: `weekly`, `rotate 4`, `create`, `dateext`, `include /etc/logrotate.d`.
- `/etc/logrotate.d/nginx`: rotación diaria (`daily`), `rotate 14`, `compress`, `delaycompress`, `notifempty`, `create 0640 www-data adm`, `sharedscripts`, con `postrotate`/`endscript` para recargar nginx tras rotar.
- `/etc/logrotate.d/mysql`:
  ```
  /var/log/mysql/*.log {
  monthly
  missingok
  rotate 12
  compress
  delaycompress
  notifempty
  create 0640 mysql mysql
  sharedscripts
  postrotate
  /usr/bin/mysqladmin flush-logs
  endscript
  }
  ```

**Cómo se procesan las configuraciones:**

1. **Configuraciones globales**: las directivas en `/etc/logrotate.conf` se aplican a todos los archivos de logs a menos que un archivo en `/etc/logrotate.d/` contenga directivas específicas para ciertos archivos.
2. **Configuraciones específicas**: los archivos en `/etc/logrotate.d/` contienen directivas específicas que se aplican solo a los archivos de log definidos en esos archivos, y pueden sobrescribir las configuraciones globales.

Por ejemplo: los logs de nginx se rotarán diariamente (14 rotaciones, comprimidos); los logs de mysql se rotarán mensualmente (12 rotaciones, comprimidos); todos los demás logs seguirán las directivas globales.

## Iniciar, detener y verificar el estado de los servicios de red

Para iniciar, detener y verificar el estado de los servicios de red en sistemas Linux, puedes utilizar `systemctl`.

**Iniciar un servicio de red:**

```shell
sudo systemctl start nombre_del_servicio
```

Ejemplo (SSH):

```shell
sudo systemctl start sshd
```

**Detener un servicio de red:**

```shell
sudo systemctl stop nombre_del_servicio
```

Ejemplo:

```shell
sudo systemctl stop sshd
```

**Verificar el estado de un servicio de red:**

```shell
sudo systemctl status nombre_del_servicio
```

Ejemplo:

```shell
sudo systemctl status sshd
```

**Ejemplos adicionales:**

- **Reiniciar un servicio de red:**
  ```shell
  sudo systemctl restart nombre_del_servicio
  ```
- **Habilitar un servicio para iniciar en el arranque del sistema:**
  ```shell
  sudo systemctl enable nombre_del_servicio
  ```
- **Deshabilitar un servicio para que no inicie en el arranque:**
  ```shell
  sudo systemctl disable nombre_del_servicio
  ```

## Transferencia segura de archivos: SCP y SFTP

Transfiere archivos de forma segura entre sistemas con SCP (Secure Copy Protocol) o SFTP (Secure File Transfer Protocol), ambos basados en SSH.

### SCP (Secure Copy)

Sintaxis básica (similar a `cp`):

```shell
scp origen destino
```

**Desde sistema local a sistema remoto:**

```shell
scp archivo.txt usuario@direccion_remota:/ruta/destino/
```

**Desde sistema remoto a sistema local:**

```shell
scp usuario@direccion_remota:/ruta/origen/archivo.txt /ruta/destino/
```

**Entre sistemas remotos:**

```shell
scp usuario1@direccion1:/ruta/origen/archivo.txt usuario2@direccion2:/ruta/destino/
```

### SFTP (Secure File Transfer Protocol)

El cliente SFTP permite interactuar con sistemas remotos de forma similar a FTP, pero a través de una conexión segura SSH.

**Conectar:**

```shell
sftp usuario@direccion_remota
```

Luego puedes utilizar los comandos `get` y `put` para transferir archivos de forma segura.

**Transferir un archivo:**

```shell
sftp usuario@direccion_remota:/ruta/origen/archivo.txt /ruta/destino/
```

Estos comandos asumen que tienes un servidor SSH configurado en el sistema remoto y las credenciales de inicio de sesión.

## Rsync

`rsync` es una herramienta muy poderosa y flexible para la sincronización de archivos y directorios entre dos ubicaciones, ya sea en la misma máquina o entre máquinas remotas. Su uso común incluye la realización de copias de seguridad, la sincronización de archivos y el mantenimiento de réplicas de directorios en diferentes sistemas.

| Opción | Uso |
|---|---|
| `-r` | Sincroniza todo el árbol de directorios. |
| `-l` | También sincroniza enlaces simbólicos. |
| `-p` | Preserva permisos. |
| `-n` | Realiza solo una simulación ("dry run"), sin sincronizar realmente nada. |
| `-a` | Usa el modo archivo, asegurando que se sincronicen árboles de subdirectorios completos y todas las propiedades de los archivos. |
| `-A` | Conserva las listas de control de acceso (ACL). `-a` no las incluye, por eso se combina como `-aAX`. |
| `-X` | Sincroniza el contexto SELinux también. |

### Ejemplos básicos

**Sincronizar directorios localmente:**

```shell
rsync -a /path/to/source/ /path/to/destination/
```

`-a` es el "modo archivo": rsync intentará sincronizar directorios completos y mantener las propiedades de los archivos, como permisos y tiempos de modificación.

**Sincronizar con un servidor remoto:**

```shell
rsync -avz /path/to/source/ user@remote:/path/to/destination/
```

`-v` aumenta la verbosidad y `-z` habilita la compresión de datos durante la transferencia, lo que puede acelerar la sincronización sobre conexiones lentas.

**Realizar una simulación (dry run) sin hacer cambios:**

```shell
rsync -avn /path/to/source/ /path/to/destination/
```

La opción `-n` asegura que rsync solo muestre los cambios que haría sin aplicarlos realmente.

**Sincronizar archivos manteniendo ACLs y contexto SELinux:**

```shell
rsync -aAX /path/to/source/ /path/to/destination/
```

Útil en entornos donde el mantenimiento de ACLs y los contextos de SELinux es crucial, como en servidores con configuraciones de seguridad detalladas.

Consulta `man rsync` para el resto de opciones avanzadas.

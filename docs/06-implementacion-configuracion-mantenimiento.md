# Implementación, configuración y mantenimiento de los sistemas

## Programe tareas con at, cron y temporizadores de systemd

> En el EX200 de RHEL 10 este objetivo incluye los **temporizadores de systemd** (`.timer`): ver
> [`system-timers.md`](../teoria-memorizar/system-timers.md) (unidad `.timer` + `.service`, `OnCalendar=`,
> `systemctl enable --now nombre.timer`, `systemctl list-timers`).

El archivo `/etc/anacrontab` se utiliza en sistemas Linux para definir y programar tareas que deben ejecutarse periódicamente, similar a Cron, pero con algunas diferencias clave. A diferencia de Cron, Anacron garantiza la ejecución de tareas incluso si el sistema no está encendido en el momento exacto programado. Esto es útil en sistemas que no están en funcionamiento continuo (por ejemplo, laptops).

### Contenido de `/etc/anacrontab`

El archivo típico de configuración de Anacron `/etc/anacrontab` tiene un formato específico que define los trabajos de Anacron. Aquí hay un ejemplo y una explicación detallada de cada parte:

### Ejemplo de `/etc/anacrontab`

```
# /etc/anacrontab: configuration file for anacron

# Format: period in days   delay in minutes   job-identifier   command

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# The maximum number of jobs that can be run simultaneously
MAXJOB=5

1   5   cron.daily   run-parts /etc/cron.daily
7   10  cron.weekly  run-parts /etc/cron.weekly
30  15  cron.monthly run-parts /etc/cron.monthly
```

### Explicación de las Partes

1. **Encabezados y Variables de Entorno:**
   - `SHELL=/bin/sh`: Define la shell que se usará para ejecutar los comandos.
   - `PATH=/sbin:/bin:/usr/sbin:/usr/bin`: Define las rutas donde se buscarán los comandos ejecutables.
   - `MAILTO=root`: Define la dirección de correo electrónico donde se enviarán los resultados de los trabajos. Si se deja vacío, no se enviarán correos.
   - `MAXJOB=5`: Define el número máximo de trabajos que Anacron puede ejecutar simultáneamente.
2. **Líneas de Trabajo de Anacron:** Cada línea después de las variables de entorno define un trabajo de Anacron. El formato es:

   ```
   period   delay   job-identifier   command
   ```

   - `period`: La frecuencia en días con la que se debe ejecutar el trabajo.
   - `delay`: El retraso en minutos antes de ejecutar el trabajo después de que Anacron haya iniciado.
   - `job-identifier`: Un identificador único para el trabajo.
   - `command`: El comando o script que se debe ejecutar.

   Por ejemplo:
   - `1 5 cron.daily run-parts /etc/cron.daily`: Esto indica que el trabajo `cron.daily` debe ejecutarse cada día (periodo de 1 día) con un retraso de 5 minutos después de que Anacron inicie. El comando que se ejecuta es `run-parts /etc/cron.daily`, que ejecuta todos los scripts en el directorio `/etc/cron.daily`.
   - `7 10 cron.weekly run-parts /etc/cron.weekly`: Esto indica que el trabajo `cron.weekly` debe ejecutarse cada 7 días (una vez por semana) con un retraso de 10 minutos.
   - `30 15 cron.monthly run-parts /etc/cron.monthly`: Esto indica que el trabajo `cron.monthly` debe ejecutarse cada 30 días (aproximadamente una vez al mes) con un retraso de 15 minutos.

### Propósito de Anacron

El propósito de Anacron es asegurarse de que las tareas periódicas se ejecuten con regularidad, incluso si el sistema no está encendido continuamente. Si el sistema se apaga y se vuelve a encender, Anacron verifica las tareas programadas y las ejecuta según sea necesario, respetando los periodos y retrasos configurados en el archivo `/etc/anacrontab`. Esto es especialmente útil para sistemas que no están en funcionamiento las 24 horas del día, los 7 días de la semana.

La programación de tareas en sistemas Linux se puede hacer mediante las utilidades `at` y `cron`. A continuación, una breve descripción de ambas herramientas:

### Utilidad `at`

La utilidad `at` permite ejecutar comandos o scripts en un momento específico del tiempo. Aquí hay algunos pasos básicos para programar tareas con `at`:

#### 1. Programar una Tarea Única con `at`

```bash
at HH:MM
```

Sustituye `HH:MM` con la hora y el minuto en formato de 24 horas en el que deseas ejecutar la tarea. Se abrirá un editor interactivo donde puedes ingresar los comandos que deseas ejecutar.

Ejemplo:

```bash
at 15:30
```

Luego, en el editor interactivo, escribe tus comandos y presiona `Ctrl+D` para guardar y salir.

#### 2. Ver Lista de Tareas Programadas con `atq`

```bash
atq
```

Esto mostrará una lista de tareas programadas con `at`. Puedes usar `atrm` para eliminar una tarea específica de la lista.

### Utilidad `cron`

La utilidad `cron` es más adecuada para tareas recurrentes y permite programar tareas que se ejecutan de manera periódica. Aquí hay algunos pasos básicos para programar tareas con `cron`:

#### 1. Editar la Tabla `crontab` del Usuario

```bash
crontab -e
```

Esto abrirá la tabla cron del usuario en un editor de texto. Cada línea en la tabla representa una tarea programada.

#### 2. Sintaxis de la Tabla `crontab`

```
Minuto Hora DiaDelMes Mes DiaDeLaSemana Comando
```

- **Minuto (0-59)**
- **Hora (0-23)**
- **DiaDelMes (1-31)**
- **Mes (1-12)**
- **DiaDeLaSemana (0-7)** (0 y 7 representan domingo)
- **Comando**

#### 3. Ejemplos de Entradas en `crontab`

Ejemplo para ejecutar un script todos los días a las 3:30 PM:

```
30 15 * * * /ruta/al/script.sh
```

Ejemplo para ejecutar un comando cada hora:

```
0 * * * * /ruta/al/comando
```

#### 4. Ver y Eliminar Tareas Programadas con `cron`

```bash
crontab -l  # Ver tareas programadas
crontab -r  # Eliminar todas las tareas programadas
```

Para opciones avanzadas, consulta `man at`, `man cron` y `man crontab`.

## Inicie y detenga los servicios, además de configurarlos para que se inicien automáticamente al arrancar

Inicia, detén y configura el arranque automático de servicios con `systemctl`, común en sistemas basados en systemd como Red Hat, Fedora, CentOS y Ubuntu. En todos los comandos siguientes, reemplaza `nombre_del_servicio` con el nombre real del servicio.

### Iniciar y Detener Servicios con `systemctl`

#### Iniciar un Servicio

```bash
sudo systemctl start nombre_del_servicio
```

#### Detener un Servicio

```bash
sudo systemctl stop nombre_del_servicio
```

#### Reiniciar un Servicio

```bash
sudo systemctl restart nombre_del_servicio
```

### Configurar Inicio Automático al Arrancar

#### Configurar un Servicio para Iniciar Automáticamente

```bash
sudo systemctl enable nombre_del_servicio
```

#### Desactivar el Inicio Automático de un Servicio

```bash
sudo systemctl disable nombre_del_servicio
```

#### Ver el Estado de un Servicio

```bash
sudo systemctl status nombre_del_servicio
```

### Ejemplo Práctico

Supongamos que deseas administrar el servicio `nginx`, que es un servidor web. Aquí tienes ejemplos:

```bash
# Iniciar nginx
sudo systemctl start nginx

# Detener nginx
sudo systemctl stop nginx

# Reiniciar nginx
sudo systemctl restart nginx

# Configurar nginx para iniciar automáticamente al arrancar
sudo systemctl enable nginx

# Desactivar el inicio automático de nginx
sudo systemctl disable nginx

# Ver el estado de nginx
sudo systemctl status nginx
```

## Configure los sistemas para que se inicien automáticamente en un destino específico

El objetivo es elegir el *target* con el que arranca el sistema (modo texto o gráfico), no qué servicios
arrancan. Se hace con el *default target*:

```bash
systemctl get-default                          # el actual, p. ej. graphical.target
sudo systemctl set-default multi-user.target   # arrancar siempre en modo texto
sudo systemctl set-default graphical.target    # o en modo gráfico
```

`set-default` cambia el enlace `/etc/systemd/system/default.target` y solo tiene efecto en el siguiente
arranque. Para cambiar ya, sin reiniciar: `sudo systemctl isolate multi-user.target`. Ver también los
*targets* en el [capítulo 3](03-sistemas-en-funcionamiento.md).

En qué *target* arranca un **servicio** lo decide su unidad (`WantedBy=multi-user.target` en `[Install]`);
`systemctl enable --now servicio` lo activa en ese *target* y lo arranca ya (`enable` es un subcomando, no
una opción `--enable`).

## Configure los clientes de servicios de tiempo (Capítulo 25)

### Tabla 25-2. Comprensión del Tiempo en Linux

| Concepto | Explicación |
|---|---|
| Reloj de hardware | El reloj de hardware que reside en la tarjeta principal de un sistema informático |
| Reloj en tiempo real | Igual que el reloj de hardware |
| Tiempo del sistema | El tiempo que es mantenido por el sistema operativo |
| Reloj de software | Similar al tiempo del sistema |
| Hora Universal Coordinada (UTC) | Un estándar mundial de tiempo |
| Horario de verano | Cálculo que se hace para cambiar la hora automáticamente cuando ocurren cambios de horario de verano |
| Hora local | La hora que corresponde a la hora en la zona horaria actual |

Configurar un servidor para usar tiempo NTP en RHEL 8 es fácil si el servidor ya está conectado a Internet. Si este es el caso, el archivo `/etc/chrony.conf` está configurado con una lista estándar de servidores NTP en Internet que deben ser contactados. Lo único que el administrador tiene que hacer es activar NTP, usando el comando `timedatectl set-ntp 1`.

### Tabla 25-3. Comandos relacionados con la gestión del tiempo en RHEL 8

| Comando | Descripción corta |
|---|---|
| date | Gestiona la hora local |
| hwclock | Gestiona la hora del hardware |
| timedatectl | Desarrollado para gestionar todos los aspectos del tiempo en RHEL 8 |

> **Nota:** En un sistema Linux, el tiempo se calcula como un desplazamiento del tiempo epoch. El tiempo epoch es el número de segundos desde el 1 de enero de 1970, en UTC. En algunos registros (como `/var/log/audit/audit.log`), encontrarás marcas de tiempo en tiempo epoch y no en tiempo humano. Para convertir una marca de tiempo epoch a tiempo humano, puedes usar la opción `--date`, seguida de la cadena epoch, comenzando con un `@`:
>
> ```bash
> date --date '@1420987251'
> ```
>
> El uso del tiempo epoch también crea un problema potencial de temporización en Linux. En un sistema de 32 bits, el número de segundos que se pueden contar en el campo reservado para la notación de tiempo se desborda el 19 de enero de 2038 (el «efecto 2038»; intenta establecer una fecha de 2050 en un sistema de 32 bits y no funcionará). Los sistemas de 64 bits pueden gestionar el tiempo hasta bien entrado el siglo XXII.

En RHEL el cliente de hora es **chronyd** (paquete `chrony`). `systemd-timesyncd` es el de Debian/Ubuntu y no
está en los repositorios de RHEL.

### Configurar el cliente NTP con chrony

```bash
sudo dnf install -y chrony
sudo vim /etc/chrony.conf
```

Comenta las líneas `pool …` de origen y añade el servidor que pida el enunciado:

```
server classroom.example.com iburst
```

`iburst` hace que la primera sincronización sea rápida (varias consultas seguidas al arrancar).

```bash
sudo systemctl enable --now chronyd
sudo systemctl restart chronyd      # si ya estaba en marcha, para que lea el cambio
sudo timedatectl set-ntp true        # NTP activo (equivale a habilitar chronyd)
chronyc sources -v                   # el servidor con ^* es el que se está usando
timedatectl                          # «System clock synchronized: yes»
```

Ejercicio completo en [`sincronizacion-ntp.md`](../ejercicios-memorizar/sincronizacion-ntp.md).

### Tabla 25-4. Descripción General del Comando timedatectl

| Comando | Explicación |
|---|---|
| status | Muestra la configuración actual del tiempo. |
| set-time TIME | Establece la hora actual. |
| set-timezone ZONE | Establece la zona horaria actual. |
| list-timezones | Muestra una lista de todas las zonas horarias. |
| set-local-rtc [0 \| 1] | Controla si el RTC (el reloj en tiempo real, normalmente conocido como el reloj de hardware) está en hora local. |
| set-ntp [0 \| 1] | Controla si NTP está habilitado. |

### Zona horaria

```bash
timedatectl list-timezones | grep Madrid
sudo timedatectl set-timezone Europe/Madrid
```

## Instale y actualice paquetes de software desde Red Hat Network, un repositorio remoto, o desde el sistema de archivos local

| Task | Explanation |
|---|---|
| search | Buscar el nombre exacto de un paquete |
| [what]provides | Realizar una búsqueda profunda en el paquete para buscar archivos específicos dentro del mismo |
| info | Proporcionar más información sobre el paquete |
| install | Instalar el paquete |
| remove | Eliminar el paquete |
| list | Listar todos los paquetes o los paquetes instalados |
| group list | Listar grupos de paquetes |
| group install | Instalar todos los paquetes de un grupo |
| update | Actualizar los paquetes especificados |
| clean all | Eliminar todos los metadatos almacenados |

Instala y actualiza paquetes en sistemas basados en Red Hat (RHEL, CentOS, Fedora) con `yum` o `dnf`, según la versión del sistema operativo.

### Utilizando `yum` (en versiones antiguas de RHEL/CentOS) o `dnf` (en versiones recientes y Fedora)

#### 1. Actualizar la Base de Datos de Paquetes

Antes de instalar o actualizar paquetes, es recomendable actualizar la base de datos de paquetes para obtener la información más reciente:

```bash
sudo yum update   # Para RHEL/CentOS versiones antiguas
sudo dnf update   # Para RHEL/CentOS versiones recientes y Fedora
```

#### 2. Instalar un Paquete

Para instalar un paquete específico, utiliza el siguiente comando:

```bash
sudo yum install nombre_del_paquete   # Para RHEL/CentOS versiones antiguas
sudo dnf install nombre_del_paquete   # Para RHEL/CentOS versiones recientes y Fedora
```

#### 3. Actualizar un Paquete

Para actualizar un paquete a su última versión disponible, utiliza el siguiente comando:

```bash
sudo yum update nombre_del_paquete   # Para RHEL/CentOS versiones antiguas
sudo dnf update nombre_del_paquete   # Para RHEL/CentOS versiones recientes y Fedora
```

#### 4. Eliminar un Paquete

Para eliminar un paquete, puedes usar el siguiente comando:

```bash
sudo yum remove nombre_del_paquete   # Para RHEL/CentOS versiones antiguas
sudo dnf remove nombre_del_paquete   # Para RHEL/CentOS versiones recientes y Fedora
```

### Utilizando Repositorios Remotos

Si necesitas instalar paquetes desde un repositorio remoto que no está configurado en tu sistema, puedes agregar el repositorio primero y luego instalar el paquete. La configuración de repositorios se encuentra en archivos en el directorio `/etc/yum.repos.d/`.

### Utilizando Repositorio Local

Si tienes los paquetes almacenados localmente, puedes instalarlos directamente. Asegúrate de conocer la ruta completa al paquete RPM:

```bash
sudo yum localinstall /ruta/al/paquete.rpm   # Para RHEL/CentOS versiones antiguas
sudo dnf install /ruta/al/paquete.rpm       # RHEL 8+: instala el RPM local y resuelve sus dependencias
                                             # (localinstall queda como alias obsoleto)
```

Reemplaza `/ruta/al/paquete.rpm` con la ruta real al paquete RPM en tu sistema.

## Modifique el cargador de arranque del sistema

Está detallado en el [capítulo 3](03-sistemas-en-funcionamiento.md) (GRUB 2). En resumen, en RHEL 8+:

```bash
sudo grubby --update-kernel=ALL --args="console=ttyS0"   # añadir argumentos a todos los kernels
sudo grubby --update-kernel=ALL --remove-args="rhgb quiet"
sudo grubby --default-kernel                             # kernel por defecto
sudo vim /etc/default/grub && sudo grub2-mkconfig -o /boot/grub2/grub.cfg   # timeout, etc.
```


# Implementación, configuración y mantenimiento de los sistemas

## Programe tareas con at y cron

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

Estos son solo ejemplos básicos, y hay muchas opciones más avanzadas disponibles para `at` y `cron`. Asegúrate de revisar las páginas de manual (`man at`, `man cron`, `man crontab`) para obtener más detalles y opciones avanzadas.

## Inicie y detenga los servicios, además de configurarlos para que se inicien automáticamente al arrancar

La administración de servicios en sistemas Linux implica iniciar, detener y configurar servicios para que se inicien automáticamente al arrancar el sistema. La forma de hacerlo puede variar según la distribución del sistema operativo. A continuación, ejemplos utilizando los comandos `systemctl`, comunes en sistemas basados en systemd, como Red Hat, Fedora, CentOS y Ubuntu.

### Iniciar y Detener Servicios con `systemctl`

#### Iniciar un Servicio

```bash
sudo systemctl start nombre_del_servicio
```

Reemplaza `nombre_del_servicio` con el nombre del servicio que deseas iniciar.

#### Detener un Servicio

```bash
sudo systemctl stop nombre_del_servicio
```

Reemplaza `nombre_del_servicio` con el nombre del servicio que deseas detener.

#### Reiniciar un Servicio

```bash
sudo systemctl restart nombre_del_servicio
```

Reemplaza `nombre_del_servicio` con el nombre del servicio que deseas reiniciar.

### Configurar Inicio Automático al Arrancar

#### Configurar un Servicio para Iniciar Automáticamente

```bash
sudo systemctl enable nombre_del_servicio
```

Reemplaza `nombre_del_servicio` con el nombre del servicio que deseas configurar para iniciar automáticamente.

#### Desactivar el Inicio Automático de un Servicio

```bash
sudo systemctl disable nombre_del_servicio
```

Reemplaza `nombre_del_servicio` con el nombre del servicio que deseas configurar para no iniciar automáticamente.

#### Ver el Estado de un Servicio

```bash
sudo systemctl status nombre_del_servicio
```

Reemplaza `nombre_del_servicio` con el nombre del servicio del cual deseas ver el estado.

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

Asegúrate de reemplazar `nginx` con el nombre del servicio que estás administrando. Estos comandos pueden variar ligeramente según la distribución específica de Linux que estés utilizando.

## Configure los sistemas para que se inicien automáticamente en un destino específico

En sistemas Linux basados en systemd, puedes configurar servicios para que se inicien automáticamente en un destino específico utilizando el comando `systemctl`. Aquí tienes los pasos generales:

### Configurar un Servicio para Iniciar Automáticamente en un Destino Específico

#### 1. Identificar el Nombre del Servicio

Antes de configurar un servicio para iniciar automáticamente en un destino específico, necesitas conocer el nombre del servicio. Puedes usar el siguiente comando para listar los servicios y encontrar el que te interesa:

```bash
sudo systemctl list-units --type=service
```

#### 2. Configurar el Servicio para Iniciar en un Destino Específico

Utiliza el siguiente comando para configurar un servicio para que se inicie automáticamente en un destino específico. Reemplaza `nombre_del_servicio` con el nombre del servicio y `nombre_del_destino` con el nombre del destino.

```bash
sudo systemctl enable --now nombre_del_servicio.service
```

- `--enable`: Configura el servicio para iniciar automáticamente.
- `--now`: Inicia el servicio inmediatamente.

#### 3. Verificar el Estado del Servicio

Puedes verificar el estado del servicio y asegurarte de que esté ejecutándose correctamente:

```bash
sudo systemctl status nombre_del_servicio
```

Reemplaza `nombre_del_servicio` con el nombre del servicio que configuraste.

### Ejemplo Práctico

Supongamos que deseas configurar el servicio `nginx` para que se inicie automáticamente en el destino `multi-user.target`. El comando sería:

```bash
sudo systemctl enable --now nginx.service
```

Esto configurará `nginx` para que se inicie automáticamente en el destino `multi-user.target` y lo iniciará inmediatamente.

Recuerda que el nombre del servicio puede variar según la distribución y la configuración específica de tu sistema. Asegúrate de reemplazar `nginx` con el nombre del servicio que estás configurando.

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
> El uso del tiempo epoch también crea un problema potencial de temporización en Linux. En un sistema de 32 bits, el número de segundos que se pueden contar en el campo reservado para la notación de tiempo se excederá en 2037. (Intenta establecer la hora en algún momento de 2050 si estás en un kernel de 32 bits; no funcionará). Los sistemas de 64 bits pueden gestionar el tiempo hasta bien entrado el siglo XXII.

Para configurar los clientes de servicios de tiempo en sistemas Linux, generalmente se utiliza el servicio `systemd-timesyncd` que es parte del sistema de inicio systemd. Este servicio proporciona una implementación simple de cliente NTP (Network Time Protocol). Aquí hay algunos pasos para configurar el cliente de servicios de tiempo:

### Configurar el Cliente NTP con `systemd-timesyncd`

### Tabla 25-4. Descripción General del Comando timedatectl

| Comando | Explicación |
|---|---|
| status | Muestra la configuración actual del tiempo. |
| set-time TIME | Establece la hora actual. |
| set-timezone ZONE | Establece la zona horaria actual. |
| list-timezones | Muestra una lista de todas las zonas horarias. |
| set-local-rtc [0 \| 1] | Controla si el RTC (el reloj en tiempo real, normalmente conocido como el reloj de hardware) está en hora local. |
| set-ntp [0 \| 1] | Controla si NTP está habilitado. |

#### 1. Verificar si `systemd-timesyncd` está instalado

Puedes verificar si el servicio `systemd-timesyncd` está instalado utilizando el siguiente comando:

```bash
sudo systemctl status systemd-timesyncd
```

#### 2. Instalar `systemd-timesyncd` si no está instalado

```bash
sudo apt-get update  # Para sistemas basados en Debian/Ubuntu
sudo apt-get install systemd-timesyncd
```

#### 3. Configurar `systemd-timesyncd`

Edita el archivo de configuración de `systemd-timesyncd` para especificar los servidores NTP. Puedes utilizar tu editor de texto preferido, por ejemplo:

```bash
sudo nano /etc/systemd/timesyncd.conf
```

Asegúrate de especificar al menos un servidor NTP en la sección `[Time]`. Puedes utilizar servidores NTP públicos, por ejemplo:

```
[Time]
NTP=pool.ntp.org
```

#### 4. Reiniciar `systemd-timesyncd`

Después de realizar cambios en la configuración, reinicia el servicio para que los cambios surtan efecto:

```bash
sudo systemctl restart systemd-timesyncd
```

#### 5. Verificar el Estado de `systemd-timesyncd`

Puedes verificar el estado del servicio para asegurarte de que esté sincronizado correctamente:

```bash
sudo systemctl status systemd-timesyncd
```

### Comandos Adicionales

- **Detener `systemd-timesyncd`:**

  ```bash
  sudo systemctl stop systemd-timesyncd
  ```

- **Iniciar `systemd-timesyncd`:**

  ```bash
  sudo systemctl start systemd-timesyncd
  ```

- **Desactivar `systemd-timesyncd` para que no se inicie automáticamente al arrancar:**

  ```bash
  sudo systemctl disable systemd-timesyncd
  ```

- **Activar `systemd-timesyncd` para que se inicie automáticamente al arrancar:**

  ```bash
  sudo systemctl enable systemd-timesyncd
  ```

**Nota:**
- Es posible que necesites ajustar el procedimiento según la distribución específica de Linux que estés utilizando. El proceso puede variar en sistemas diferentes.
- Si prefieres utilizar un cliente NTP diferente, como `ntp`, `chrony`, u otros, el proceso de instalación y configuración será diferente. Consulta la documentación de la herramienta específica que estás utilizando.

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

Para instalar y actualizar paquetes de software en sistemas basados en Red Hat (como Red Hat Enterprise Linux, CentOS y Fedora), puedes utilizar el sistema de gestión de paquetes `yum` o `dnf` (dependiendo de la versión del sistema operativo).

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
sudo dnf localinstall /ruta/al/paquete.rpm   # Para RHEL/CentOS versiones recientes y Fedora
```

Reemplaza `/ruta/al/paquete.rpm` con la ruta real al paquete RPM en tu sistema.

Estos son comandos básicos para instalar y actualizar paquetes en sistemas basados en Red Hat. Ten en cuenta que las versiones específicas de RHEL/CentOS y Fedora pueden tener ligeras variaciones en los comandos y las herramientas utilizadas.

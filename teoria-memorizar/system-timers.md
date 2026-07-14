# System Timers

Los **timers de systemd** son unidades especiales de `systemd` que se utilizan para programar tareas y ejecutar servicios en momentos específicos o en intervalos de tiempo. Son equivalentes a los **cron jobs** tradicionales de Unix, pero más integrados dentro del ecosistema de `systemd`, ofreciendo más flexibilidad y control.

## Conceptos básicos de los systemd timers

1. **Timer Unit (.timer)**: Un archivo de unidad `.timer` define cuándo y con qué frecuencia debe activarse un servicio o una tarea programada. Los timers no ejecutan tareas directamente; en su lugar, activan servicios (unidades `.service`).
2. **Service Unit (.service)**: Los timers están vinculados a unidades `.service`. Cuando el timer se activa, ejecuta el servicio correspondiente. Por ejemplo, un timer que se ejecuta diariamente puede estar vinculado a un servicio que realiza una copia de seguridad de los archivos.

## Ejemplo de un archivo de unidad .timer

Un archivo de timer tiene la extensión `.timer` y está ubicado en `/etc/systemd/system/` o en `/usr/lib/systemd/system/`. Un ejemplo básico sería:

```ini
[Unit]
Description=Ejecución diaria de backup

[Timer]
OnCalendar=daily
Persistent=true

[Install]
WantedBy=timers.target
```

### Explicación

- **[Unit]**: Sección estándar que contiene la descripción del timer.
- **[Timer]**: Sección que define cuándo se debe ejecutar el servicio.
  - `OnCalendar=daily` especifica que este timer se ejecutará una vez al día. También puedes usar otras configuraciones de tiempo como `weekly`, `monthly`, o una hora específica como `OnCalendar=Mon *-*-* 08:00:00`.
  - `Persistent=true` asegura que si el sistema estaba apagado cuando el timer debía ejecutarse, la tarea se ejecutará la próxima vez que el sistema se inicie.
- **[Install]**: Define cómo el timer se integra en `systemd`. `WantedBy=timers.target` asegura que el timer se active cuando el sistema está configurado para manejar timers.

## Ejemplo de un archivo de unidad .service

El timer anterior necesita un servicio para ejecutar cuando se active. El servicio puede verse así:

```ini
[Unit]
Description=Backup diario

[Service]
ExecStart=/usr/local/bin/backup.sh
```

Este servicio ejecuta el script `/usr/local/bin/backup.sh` cada vez que el timer lo activa.

## Comandos útiles para gestionar timers

1. **Listar todos los timers activos**:

   ```bash
   systemctl list-timers --all
   ```

   Esto muestra los timers en ejecución y cuándo se ejecutarán la próxima vez.

2. **Iniciar o detener un timer manualmente**:
   - Para iniciar un timer:
     ```bash
     systemctl start nombre.timer
     ```
   - Para detener un timer:
     ```bash
     systemctl stop nombre.timer
     ```

3. **Habilitar un timer para que se ejecute en el arranque**:

   ```bash
   systemctl enable nombre.timer
   ```

4. **Ver el estado de un timer**:

   ```bash
   systemctl status nombre.timer
   ```

## Tipos de timers en systemd

- **OnCalendar**: Permite programar tareas basadas en un calendario, usando una sintaxis similar a `cron` (por ejemplo, `daily`, `weekly`, `Mon *-*-* 08:00:00`).
- **OnActiveSec**: Ejecuta el servicio después de un período desde que el sistema arrancó.
- **OnBootSec**: Ejecuta el servicio después de un período desde el arranque del sistema.
- **OnUnitActiveSec**: Ejecuta el servicio después de un período desde la última vez que la unidad se activó.
- **OnUnitInactiveSec**: Ejecuta el servicio después de un período desde la última vez que la unidad estuvo inactiva.

## Ventajas de systemd timers sobre cron

1. **Persistencia**: Si el sistema estaba apagado cuando se debía ejecutar un timer (con la opción `Persistent=true`), se ejecutará cuando el sistema se vuelva a iniciar.
2. **Mayor flexibilidad**: Los timers pueden ejecutarse en base a eventos más variados, no solo a intervalos regulares de tiempo.
3. **Integración con systemd**: Permite un mejor control y monitoreo, utilizando todas las funcionalidades de systemd como dependencias, monitoreo de servicios, logs centralizados, etc.
4. **Control de los servicios asociados**: Systemd puede manejar los servicios que los timers ejecutan, incluyendo reinicios, dependencias y límites de recursos.

## Ejemplo completo: Crear un backup diario con systemd timers

1. **Archivo del timer** (`/etc/systemd/system/backup.timer`):

   ```ini
   [Unit]
   Description=Ejecución diaria del backup

   [Timer]
   OnCalendar=daily
   Persistent=true

   [Install]
   WantedBy=timers.target
   ```

2. **Archivo del servicio** (`/etc/systemd/system/backup.service`):

   ```ini
   [Unit]
   Description=Servicio de backup

   [Service]
   ExecStart=/usr/local/bin/backup.sh
   ```

3. **Activar el timer**:

   ```bash
   systemctl enable --now backup.timer
   ```

Este ejemplo aseguraría que el script `backup.sh` se ejecutará una vez al día.

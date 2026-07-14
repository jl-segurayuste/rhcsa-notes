# Tarea programada crontab

Para programar una tarea que ejecute el comando `touch /etc/motd` todos los días de lunes a viernes a las 2 a.m., debes usar el administrador de tareas cron en Linux. Cron permite programar tareas para que se ejecuten automáticamente en horarios específicos.

## Pasos para programar la tarea con cron

1. **Abrir el archivo crontab**: para editar las tareas programadas para el usuario actual, usa el siguiente comando:

   ```bash
   crontab -e
   ```

2. **Agregar la entrada cron**: dentro del editor de crontab, agrega la siguiente línea:

   ```bash
   0 2 * * 1-5 touch /etc/motd
   ```

   Esta línea se desglosa de la siguiente manera:
   - `0 2 * * 1-5 touch /etc/motd`

> **Nota:** el contenido original de esta página en Notion termina abruptamente en este punto (la explicación campo a campo de la expresión cron no llegó a redactarse). Se ha migrado tal cual está, sin añadir contenido no presente en la fuente. Se comprobó tanto la página duplicada (`125fe1e8b70e80018045ca5115595c66`, "Tarea programada crontab (1)") como la original (`9911034f1bd446568b3a495e810575f1`, "Tarea programada crontab") y ambas están cortadas exactamente en el mismo punto.
>
> Para referencia rápida (no proviene de la página original, es la sintaxis estándar de crontab): el formato es `minuto hora dia-del-mes mes dia-de-la-semana comando`. En este caso: `0 2 * * 1-5` = minuto 0, hora 2 (2 a.m.), cualquier día del mes, cualquier mes, días de la semana 1 a 5 (lunes a viernes).

# Configurar logrotate

Si deseas configurar **logrotate** para que **todos los archivos de log** del sistema mantengan **diez versiones antiguas**, puedes modificar la configuración general de logrotate que se encuentra en el archivo `/etc/logrotate.conf`.

### Procedimiento

1. **Editar la configuración general de logrotate:**

   Abre el archivo `/etc/logrotate.conf` con un editor de texto:

   ```bash
   nano /etc/logrotate.conf
   ```

2. **Añadir la directiva `rotate 10` para todos los logs:**

   Dentro del archivo, añade o modifica la directiva `rotate` para especificar que quieres mantener diez versiones antiguas de todos los archivos de log que gestiona logrotate.

   El archivo podría verse de la siguiente manera:

   ```bash
   # see "man logrotate" for details
   # rotate log files weekly
   weekly

   # keep 10 weeks worth of backlogs
   rotate 10

   # create new (empty) log files after rotating old ones
   create

   # use date as a suffix of the rotated file
   dateext

   # compress old log files
   compress

   # uncompressed logs will be removed after rotation
   delaycompress

   # logrotate will ignore missing log files and errors
   missingok

   # do not rotate empty log files
   notifempty
   ```

   ### Explicación de las directivas

   - **`rotate 10`**: Esta opción define que se deben mantener **10 versiones antiguas** de todos los archivos de log del sistema.
   - **`weekly`**: Realiza la rotación semanalmente (puedes cambiarlo a `daily`, `monthly` si es necesario).
   - **`compress`**: Comprime los archivos de log antiguos para ahorrar espacio.
   - **`delaycompress`**: Retrasa la compresión hasta la siguiente rotación.
   - **`notifempty`**: No rota los archivos si están vacíos.
   - **`missingok`**: No genera errores si falta algún archivo de log.

3. **Guardar los cambios** y salir del editor.

Este ajuste hará que todos los logs rotados por logrotate en el sistema mantengan hasta diez versiones antiguas por defecto, sin tener que configurarlo individualmente para cada archivo.

### Forzar la rotación manual

Puedes forzar que logrotate realice una rotación inmediata de todos los logs usando el siguiente comando:

```bash
logrotate -f /etc/logrotate.conf
```

Esto aplicará la configuración que acabas de realizar y comenzará a mantener hasta diez versiones antiguas de todos los archivos de log del sistema.

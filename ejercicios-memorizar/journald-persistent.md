# Journald persistent

El procedimiento que mencionas para hacer que el journal de systemd sea persistente aún después del reinicio se puede realizar con los siguientes cuatro comandos:

1. **Crear el directorio donde se almacenarán los logs de manera persistente:**

   ```bash
   mkdir /var/log/journal
   ```

   Esto crea el directorio donde se almacenarán los logs.

2. **Cambiar la propiedad del directorio:**

   ```bash
   chown root:systemd-journal /var/log/journal
   ```

   Este comando cambia la propiedad del directorio para que el grupo `systemd-journal` tenga acceso.

3. **Configurar los permisos adecuados:**

   ```bash
   chmod 2755 /var/log/journal
   ```

   Esto otorga los permisos necesarios para que el journal pueda escribir en el directorio y establece el bit setgid (indicado por el número **2**) para que los archivos creados dentro hereden el grupo propietario del directorio.

4. **Volcar (flush) los logs en memoria al almacenamiento persistente recién creado:**

   ```bash
   killall -USR1 systemd-journald
   ```

   La señal **SIGUSR1** no recarga la configuración de `systemd-journald` (eso sería `systemctl reload systemd-journald` o `SIGHUP`); lo que hace es indicarle que vuelque los datos del journal en memoria/runtime (`/run/log/journal`) al almacenamiento persistente que se acaba de crear en `/var/log/journal`, sin necesidad de reiniciar el servicio.

Estos comandos en secuencia configuran el journal para que almacene los logs de manera persistente en el disco.

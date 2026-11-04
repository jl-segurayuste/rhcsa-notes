# Journald persistent

Para hacer que el journal de systemd sea persistente tras el reinicio, ejecuta estos cuatro comandos:

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

   La señal **SIGUSR1** le indica a `systemd-journald` que vuelque el journal en memoria (`/run/log/journal`)
   al directorio persistente recién creado, sin reiniciar el servicio. Es lo mismo que `journalctl --flush`.

## Alternativa: `Storage=persistent`

La otra forma (la de la documentación de Red Hat) es fijarlo en la configuración, que no depende de que exista
el directorio:

```bash
sudo mkdir -p /etc/systemd/journald.conf.d
printf '[Journal]\nStorage=persistent\n' | sudo tee /etc/systemd/journald.conf.d/persistent.conf
sudo systemctl restart systemd-journald
```

Comprobación: `journalctl --list-boots` debe mostrar varios arranques después de reiniciar.

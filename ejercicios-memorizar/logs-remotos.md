# Logs remotos

> **Enunciado:** You are a System administrator. Using Log files very easy to monitor the system. Now there are 50 servers running as Mail, Web, Proxy, DNS services etc. You want to centralize the logs from all servers into on LOG Server. How will you configure the LOG Server to accept logs from remote host?

Para centralizar los logs de varios servidores en un servidor de logs central, puedes usar `rsyslog`, que es una solución robusta y flexible para la recolección de logs. Aquí te explico cómo configurar un servidor de logs para aceptar registros desde hosts remotos.

### Pasos para configurar el servidor de logs

1. **Instala `rsyslog` en el servidor de logs** (si no está instalado):

   ```bash
   sudo yum install rsyslog
   ```

2. **Configura `rsyslog` para aceptar logs de hosts remotos:**

   Edita el archivo de configuración de `rsyslog`:

   ```bash
   sudo vi /etc/rsyslog.conf
   ```

   Asegúrate de que las siguientes líneas no estén comentadas (quita el `#` si es necesario) para permitir el protocolo UDP y TCP:

   ```bash
   # Provides UDP syslog reception
   $ModLoad imudp
   $UDPServerRun 514

   # Provides TCP syslog reception
   $ModLoad imtcp
   $InputTCPServerRun 514
   ```

   Estas líneas activan el módulo de recepción de `rsyslog` para UDP y TCP en el puerto 514, que es el puerto estándar para syslog.

3. **Reinicia el servicio `rsyslog` para aplicar los cambios:**

   ```bash
   sudo systemctl restart rsyslog
   ```

4. **Asegúrate de que el firewall permita el tráfico en el puerto 514:**

   ```bash
   sudo firewall-cmd --add-port=514/udp --permanent
   sudo firewall-cmd --add-port=514/tcp --permanent
   sudo firewall-cmd --reload
   ```

5. **Configura los servidores remotos para enviar logs al servidor central:**

   Edita el archivo de configuración de `rsyslog` en cada servidor remoto:

   ```bash
   sudo vi /etc/rsyslog.conf
   ```

   Añade la siguiente línea para enviar los logs al servidor de logs central (reemplaza `logserver.example.com` con el nombre o la dirección IP de tu servidor de logs):

   ```bash
   *.* @@logserver.example.com:514
   ```

   La doble arroba (`@@`) indica que los logs se enviarán a través de TCP. Para usar UDP, usa una sola arroba (`@`).

6. **Reinicia `rsyslog` en los servidores remotos para aplicar los cambios:**

   ```bash
   sudo systemctl restart rsyslog
   ```

### Verificación y monitoreo

Para verificar que los logs se están enviando y recibiendo correctamente, puedes revisar los archivos de log en el servidor central. Por ejemplo, puedes revisar `/var/log/messages`:

```bash
sudo tail -f /var/log/messages
```

También puedes crear reglas específicas en `rsyslog.conf` para almacenar los logs de cada servidor en archivos separados, lo cual facilita la organización y el monitoreo.

### Configuración avanzada

Para una configuración más avanzada, puedes utilizar `logrotate` para gestionar el tamaño de los archivos de log y `rsyslog` para aplicar filtros y reglas específicas según tus necesidades.

Estas configuraciones proporcionan una solución básica pero efectiva para centralizar los logs de múltiples servidores, lo que facilita el monitoreo y la administración de los registros del sistema.

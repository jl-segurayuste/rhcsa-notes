# Exam 1

## Árbol de directorios y SELinux

Aquí está cómo puedes crear la jerarquía de directorios y aplicar los contextos de SELinux correspondientes a `/etc` de manera recursiva:

### Comando completo:

```bash
mkdir -p /dir1/dir2/dir3/dir4
semanage fcontext -a -t etc_t "/dir1(/.*)?"
restorecon -Rv /dir1
```

### Explicación:

1. **`mkdir -p /dir1/dir2/dir3/dir4`:**
   - Crea la jerarquía de directorios `/dir1/dir2/dir3/dir4`.
   - La opción `p` asegura que si los directorios intermedios no existen, se crean también.
2. **`semanage fcontext -a -t etc_t "/dir1(/.*)?"`:**
   - Configura un contexto SELinux para `/dir1` y todos sus subdirectorios.
   - **`a`:** Agrega una nueva regla de contexto.
   - **`t etc_t`:** Aplica el tipo de contexto `etc_t`, que es usado por el sistema en `/etc`.
   - **`"/dir1(/.*)?"`:** Define el patrón. `(/.*)?` incluye todos los subdirectorios y archivos recursivamente.
3. **`restorecon -Rv /dir1`:**
   - Aplica los contextos definidos en las reglas de `semanage` a los directorios y archivos bajo `/dir1`.
   - **`R`:** Actúa recursivamente.
   - **`v`:** Muestra detalles del proceso.

### Verificación:

Puedes verificar los contextos de SELinux aplicados con:

```bash
ls -lZ /dir1/dir2/dir3/dir4
```

Esto mostrará los contextos de los directorios, y deberías ver algo similar a `system_u:object_r:etc_t:s0`.

### Notas adicionales:

- Asegúrate de tener privilegios de root para ejecutar los comandos.
- Si no tienes `semanage` instalado, puedes instalarlo con el paquete `policycoreutils-python-utils` en distribuciones basadas en RHEL/CentOS/AlmaLinux/Rocky Linux.

## Enable access to the atd service for user20 and deny for user30

```bash
echo "user30" >> /etc/at.deny
# just don't create at.allow
```

## Comprimir con bzip2

Use tar and bzip2 to create a compressed archive of the `/etc/sysconfig` directory. Store the archive under `/tmp` as etc.tar.bz2:

```bash
tar -jcvf /tmp/etc.tar.bz2 /etc/sysconfig/
```

## Mensaje personalizado al archivo /var/log/messages

Aquí tienes los pasos para agregar un mensaje personalizado al archivo `/var/log/messages` y confirmar que se haya registrado correctamente usando una expresión regular:

### Pasos:

1. **Agregar el mensaje al archivo de log:**

   Usa el comando `logger` para agregar el mensaje al archivo de log. Este comando asegura que el mensaje se registre en `/var/log/messages`.

   ```bash
   logger "This is the RHCSA sample exam on $(date) by $LOGNAME"
   ```

   **Explicación:**
   - `logger`: Es el comando para escribir en los archivos de log del sistema.
   - `"This is the RHCSA sample exam on $(date) by $LOGNAME"`: Mensaje que contiene la fecha actual y el nombre de usuario que ejecuta el comando.

2. **Confirmar que el mensaje está en `/var/log/messages`:**

   Usa `grep` con una expresión regular para buscar el mensaje en el archivo `/var/log/messages`.

   ```bash
   grep -i "This is the " /var/log/messages
   ```

   **Explicación de la expresión regular:**
   - `i`: Ignora mayúsculas/minúsculas.

3. **Verifica el resultado:**

   Si el mensaje fue registrado correctamente, el comando `grep` mostrará la línea que contiene el mensaje.

   **Ejemplo de salida:**

   ```text
   Nov 29 15:30:45 localhost root: This is the RHCSA sample exam on Wed Nov 29 15:30:45 UTC 2024 by root
   ```

4. **Solución alternativa si no ves el mensaje:**

   - Asegúrate de que el servicio de `rsyslog` esté activo y ejecutándose:

     ```shell
     systemctl status rsyslog
     ```

   - Reinicia el servicio si es necesario:

     ```shell
     systemctl restart rsyslog
     ```

# Sincronización de servidor NTP

Para configurar la sincronización de tiempo en un sistema usando `timedatectl`, generalmente se usa junto con `systemd-timesyncd` o `chrony` para la sincronización de tiempo. Aquí se explica cómo hacerlo usando `timedatectl` y `chrony`.

## Configuración de Sincronización de Tiempo con `timedatectl`

`timedatectl` se utiliza principalmente para consultar y cambiar la configuración de fecha y hora en un sistema que usa `systemd`. Para la sincronización de tiempo, puedes usar `timedatectl` para verificar y habilitar la sincronización, pero la configuración detallada del servidor NTP se hará mediante `chrony`.

## Pasos para Configurar la Sincronización de Tiempo usando `timedatectl` y `chrony`

1. **Instalar chrony**:

   ```bash
   yum install -y chrony
   ```

2. **Configurar chrony**:
   - Abre el archivo de configuración de `chrony`:

     ```bash
     vi /etc/chrony.conf
     ```

   - Comenta las líneas existentes que especifican los servidores NTP predeterminados (si es necesario) y añade la línea para el servidor NTP específico:

     ```plain text
     # pool 2.rhel.pool.ntp.org iburst
     server 172.24.40.10 iburst
     ```

3. **Reiniciar el servicio chronyd**:

   ```bash
   systemctl restart chronyd
   ```

4. **Habilitar chrony para que se inicie automáticamente en el arranque**:

   ```bash
   systemctl enable chronyd
   ```

5. **Verificar y Habilitar la Sincronización de Tiempo con `timedatectl`**:
   - Verificar el estado de la sincronización de tiempo:

     ```bash
     timedatectl status
     ```

   - Habilitar la sincronización de tiempo:

     ```bash
     timedatectl set-ntp true
     ```

6. **Verificar la Configuración de chrony**:
   - Comprobar el estado del servicio:

     ```bash
     systemctl status chronyd
     ```

   - Verificar las fuentes de tiempo utilizadas por `chrony`:

     ```bash
     chronyc sources -v
     ```

## Configuración Detallada

1. **Instalar chrony**:

   ```bash
   yum install -y chrony
   ```

2. **Editar la configuración de `chrony`**:

   ```bash
   vi /etc/chrony.conf
   ```

   Comenta cualquier línea que especifique servidores NTP predeterminados añadiendo un `#` al inicio de la línea. Luego, añade la línea para tu servidor NTP:

   ```plain text
   server 172.24.40.10 iburst
   ```

3. **Reiniciar el servicio `chronyd`**:

   ```bash
   systemctl restart chronyd
   ```

   Esto reinicia `chronyd` para aplicar la nueva configuración.

4. **Habilitar `chronyd` para que se inicie automáticamente**:

   ```bash
   systemctl enable chronyd
   ```

   Este comando asegura que `chronyd` se inicie automáticamente al arrancar el sistema.

5. **Habilitar la sincronización de tiempo con `timedatectl`**:

   ```bash
   timedatectl set-ntp true
   ```

   Este comando habilita la sincronización de tiempo en el sistema.

6. **Verificar la configuración de `chrony`**:
   - Comprobar el estado del servicio (muestra el estado actual de `chronyd` y si está corriendo sin problemas):

     ```bash
     systemctl status chronyd
     ```

   - Verificar las fuentes de tiempo (muestra las fuentes de tiempo que `chronyd` está utilizando y su estado de sincronización):

     ```bash
     chronyc sources -v
     ```

## Resumen

Usando `timedatectl` junto con `chrony`, puedes configurar y verificar la sincronización de tiempo en tu sistema RHEL. `timedatectl` se encarga de habilitar la sincronización de tiempo, mientras que `chrony` se encarga de la configuración detallada de los servidores NTP.

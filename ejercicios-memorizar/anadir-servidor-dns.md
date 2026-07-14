# Añadir servidor DNS

> **Enunciado:** Your System is configured in 192.168.0.0/24 Network and your nameserver is 192.168.0.254. Make successfully resolve to [server1.example.com](http://server1.example.com/).

Si se modifica `/etc/resolv.conf` directamente sin haber deshabilitado el DNS en la configuración de NetworkManager, no se está haciendo de la manera más correcta, ya que cualquier cambio en NetworkManager sobreescribe el archivo de configuración de `resolv.conf`.

Al usar `nmcli` sin especificar una interfaz en particular puede ser problemático si no conoces el nombre exacto de la conexión. Sin embargo, hay formas de gestionar los ajustes de DNS a nivel global si NetworkManager está configurado para manejar todas las conexiones.

## Configuración global de DNS con NetworkManager

Para asegurarte de que el nameserver se aplica globalmente, puedes ajustar la configuración de DNS en NetworkManager de manera que afecte a todas las conexiones.

### 1. Editar el archivo de configuración global de NetworkManager

Abre el archivo `/etc/NetworkManager/NetworkManager.conf`:

```bash
sudo vi /etc/NetworkManager/NetworkManager.conf
```

### 2. Añadir o modificar la sección `[main]`

Asegúrate de que la sección `[main]` contenga la línea `dns=none`. Si no existe, añádela:

```plain text
[main]
dns=none
```

### 3. Editar el archivo de configuración de resolvconf

A continuación, edita el archivo `/etc/resolv.conf` manualmente o crea un archivo en `/etc/resolvconf/resolv.conf.d/base` si `resolvconf` está instalado:

```bash
sudo vi /etc/resolv.conf
```

Añade la línea para tu nameserver:

```plain text
nameserver 192.168.0.254
```

### 4. Reiniciar NetworkManager

Aplica los cambios reiniciando NetworkManager:

```bash
sudo systemctl restart NetworkManager
```

## Alternativa: utilizar `nmcli` para todas las conexiones

Si necesitas aplicar el nameserver a todas las conexiones de manera dinámica y no sabes los nombres de las conexiones de antemano, puedes hacerlo con un script que itere sobre todas las conexiones:

```bash
for con in $(nmcli -t -f NAME con show); do
    nmcli con mod "$con" ipv4.dns "192.168.0.254"
    nmcli con up "$con"
done
```

## Verificar la configuración

Para verificar que el nameserver se ha configurado correctamente, revisa el contenido de `/etc/resolv.conf`:

```bash
cat /etc/resolv.conf
```

Deberías ver `nameserver 192.168.0.254` listado en el archivo.

## Prueba la resolución de nombres

Finalmente, prueba que la resolución de nombres funciona correctamente:

```bash
host server1.example.com
```

## Resumen

Para asegurar que el nameserver se aplica globalmente en un entorno gestionado por NetworkManager, puedes ajustar la configuración global de NetworkManager o iterar sobre todas las conexiones con un script para aplicar los ajustes de DNS. De esta manera, te aseguras de que los cambios sean persistentes y efectivos para todas las conexiones de red en el sistema.

## A una interfaz específica

Es mejor utilizar `nmcli` para cambiar el nameserver si tu sistema está gestionado por NetworkManager, ya que de esta manera los cambios se aplicarán de manera persistente y no serán sobrescritos por NetworkManager.

### Pasos para cambiar el nameserver usando `nmcli`

1. **Lista las conexiones disponibles**:

   ```bash
   nmcli con show
   ```

   Esto te dará una lista de todas las conexiones configuradas en tu sistema. Identifica el nombre de la conexión para la que deseas cambiar el nameserver.

2. **Modifica la configuración de DNS de la conexión**:

   ```bash
   nmcli con mod <connection_name> ipv4.dns "192.168.0.254"
   ```

   Reemplaza `<connection_name>` con el nombre de la conexión que encontraste en el paso anterior.

3. **Reinicia la conexión para aplicar los cambios**:

   ```bash
   nmcli con up <connection_name>
   ```

### Verifica la configuración

Para verificar que el nameserver se ha configurado correctamente, puedes revisar el contenido de `/etc/resolv.conf`:

```bash
cat /etc/resolv.conf
```

Deberías ver `nameserver 192.168.0.254` listado en el archivo.

### Prueba la resolución de nombres

Finalmente, prueba que la resolución de nombres funciona correctamente:

```bash
host server1.example.com
```

Si la configuración es correcta, deberías obtener una respuesta con la dirección IP correspondiente a `server1.example.com`.

### Ejemplo completo

Supongamos que la conexión se llama `System eth0`. Aquí tienes los comandos completos:

1. Lista las conexiones:

   ```bash
   nmcli con show
   ```

2. Modifica la configuración de DNS:

   ```bash
   nmcli con mod "System eth0" ipv4.dns "192.168.0.254"
   ```

3. Reinicia la conexión:

   ```bash
   nmcli con up "System eth0"
   ```

4. Verifica el contenido de `/etc/resolv.conf`:

   ```bash
   cat /etc/resolv.conf
   ```

5. Prueba la resolución de nombres:

   ```bash
   host server1.example.com
   ```

Siguiendo estos pasos, te aseguras de que el nameserver se configure de manera persistente y que no se sobrescriba por NetworkManager.

# Gestión de las conexiones de red básicas

## Configure las direcciones IPv4 e IPv6

Para configurar direcciones IPv4 e IPv6 en sistemas basados en Red Hat (como RHEL, CentOS y Fedora), generalmente se utiliza el comando `nmcli` (NetworkManager Command-Line Interface). A continuación se detallan instrucciones básicas para configurar direcciones IP tanto IPv4 como IPv6. Asegúrate de tener privilegios de superusuario para realizar estas operaciones.

Tabla de referencia de máscaras de subred (valor binario del último octeto y su equivalente decimal):

| Binary Value | Decimal Value |
|---|---|
| 00000000 | 0 |
| 00100000 | 32 |
| 01000000 | 64 |
| 01100000 | 96 |
| 10000000 | 128 |
| 10100000 | 160 |
| 11000000 | 192 |
| 11100000 | 224 |

La utilidad `ip` es una herramienta moderna y versátil para configurar y monitorear diversos aspectos de la red en sistemas Linux. Esta herramienta reemplaza a las antiguas utilidades como `ifconfig`, `route` y `netstat`. A continuación se explica cómo usar `ip` para verificar la configuración de la dirección de red y otras funcionalidades importantes.

### Verificar y Configurar Direcciones de Red con `ip addr`

- **Ver las direcciones IP asignadas a todas las interfaces de red:**

```bash
ip addr show
```

O simplemente:

```bash
ip a
```

Esto mostrará una lista de todas las interfaces de red y sus direcciones IP asociadas.

### Configurar y Monitorear Información de Enrutamiento con `ip route`

- **Ver las rutas de enrutamiento actuales:**

```bash
ip route show
```

Esto muestra la tabla de enrutamiento actual.

- **Agregar una ruta estática:**

```bash
sudo ip route add 192.168.2.0/24 via 192.168.1.1 dev eth0
```

Esto agrega una ruta a la red `192.168.2.0/24` a través de la puerta de enlace `192.168.1.1` utilizando la interfaz `eth0`.

- **Eliminar una ruta estática:**

```bash
sudo ip route del 192.168.2.0/24
```

### Configurar y Monitorear el Estado del Enlace de Red con `ip link`

- **Ver el estado de las interfaces de red:**

```bash
ip link show
```

Esto muestra todas las interfaces de red y su estado (UP o DOWN).

- **Activar una interfaz de red:**

```bash
sudo ip link set dev eth0 up
```

- **Desactivar una interfaz de red:**

```bash
sudo ip link set dev eth0 down
```

### Otros Aspectos de la Utilidad `ip`

La utilidad `ip` puede gestionar muchos otros aspectos de la red, pero para el examen RHCSA no es necesario conocer todos esos detalles. Sin embargo, algunas funcionalidades adicionales que puede manejar incluyen:

- Configuración de túneles de red.
- Gestión de políticas de enrutamiento avanzadas.
- Configuración de direcciones IP virtuales.

### Ejemplo de Verificación de Configuración de Red

Para verificar la configuración de la dirección de red de una interfaz específica, podrías usar el siguiente comando:

```bash
ip addr show dev eth0
```

Esto mostrará solo la información relevante para la interfaz `eth0`.

### Resumen

La utilidad `ip` es fundamental para la gestión de redes en sistemas Linux modernos. Ofrece un control detallado y una visibilidad completa sobre las direcciones IP, las rutas de enrutamiento y el estado de los enlaces de red, entre otras cosas. Conocer estos comandos básicos permite gestionar y solucionar problemas de red de manera eficiente en un entorno Linux.

### Validación de la disponibilidad de puertos y servicios

Para validar la disponibilidad de puertos y servicios en un servidor, se pueden utilizar las herramientas `netstat` y `ss`. En este contexto, se recomienda usar `ss` ya que es una herramienta más moderna y eficiente.

**Uso del comando `ss` para verificar puertos**

El comando `ss` (Socket Statistics) se utiliza para proporcionar información sobre las conexiones de red y los sockets en el sistema. Para ver todos los puertos TCP en escucha en el sistema local, puedes usar la opción `-lt`:

```bash
ss -lt
```

**Ejemplo de salida de `ss -lt`**

```
[root@server1 ~]# ss -lt
State      Recv-Q Send-Q          Local Address:Port           Peer Address:Port
LISTEN     0      32          192.168.122.1:domain             0.0.0.0:*
LISTEN     0      128         0.0.0.0:ssh                     0.0.0.0:*
LISTEN     0      5           127.0.0.1:ipp                   0.0.0.0:*
LISTEN     0      128         0.0.0.0:sunrpc                  0.0.0.0:*
LISTEN     0      128         [::]:ssh                        [::]:*
LISTEN     0      5           [::1]:ipp                       [::]:*
LISTEN     0      128         [::]:sunrpc                     [::]:*
```

**Interpretación de la salida**

En la salida de `ss -lt`, cada línea muestra un puerto que está en estado de escucha (LISTEN). Estos son los componentes clave:

- **State**: el estado de la conexión. En este caso, todos los puertos están en el estado `LISTEN`.
- **Recv-Q** y **Send-Q**: los tamaños de las colas de recepción y envío.
- **Local Address:Port**: la dirección IP local y el puerto en escucha.
- **Peer Address:Port**: la dirección IP y el puerto del par remoto (en el caso de puertos en escucha, normalmente se muestra vacío).

**Direcciones y puertos**

- `192.168.122.1:domain`: este puerto está escuchando en la dirección IP `192.168.122.1` en el puerto DNS (`domain`).
- `0.0.0.0:ssh`: este puerto está escuchando en todas las direcciones IPv4 en el puerto SSH.
- `127.0.0.1:ipp`: este puerto está escuchando solo en la dirección de loopback IPv4 (localhost) en el puerto de impresión (`ipp`).
- `[::]:ssh`: este puerto está escuchando en todas las direcciones IPv6 en el puerto SSH.
- `[::1]:ipp`: este puerto está escuchando solo en la dirección de loopback IPv6 en el puerto de impresión (`ipp`).

**Notas importantes**

- **IPv4 Loopback (127.0.0.1)** y **IPv6 Loopback (::1)**: los puertos que escuchan en estas direcciones solo son accesibles desde la propia máquina local. No pueden ser alcanzados desde otras máquinas en la red.
- **0.0.0.0**: representa todas las direcciones IPv4 disponibles en la máquina. Los puertos que escuchan en `0.0.0.0` están accesibles desde cualquier dirección IPv4.
- **[::]**: representa todas las direcciones IPv6 disponibles en la máquina. Los puertos que escuchan en `[::]` están accesibles desde cualquier dirección IPv6.

**Verificación de configuración de red**

Para verificar la configuración de red y la disponibilidad de puertos, sigue estos pasos:

1. Ejecutar el comando `ss -lt` para listar todos los puertos TCP en escucha.
2. Revisar la salida para asegurarse de que los puertos críticos estén en escucha en las direcciones IP correctas.
3. Asegurarse de que los puertos necesarios estén accesibles desde las redes externas si es necesario, verificando que no estén limitados a las direcciones de loopback (`127.0.0.1` o `::1`).

Estos pasos ayudan a diagnosticar y resolver problemas relacionados con la disponibilidad de puertos y servicios en un servidor.

### Configuración de Direcciones IPv4 con `nmcli`

1. **Listar conexiones disponibles:**

```bash
sudo nmcli con show
```

Esto mostrará una lista de las conexiones de red disponibles.

2. **Seleccionar la conexión a configurar:**

```bash
sudo nmcli con show "Nombre de la Conexión"
```

Reemplaza `"Nombre de la Conexión"` con el nombre de la conexión que deseas configurar.

3. **Configurar una dirección IPv4 estática:**

```bash
sudo nmcli con mod "Nombre de la Conexión" ipv4.addresses "Dirección IP/Longitud de Prefijo"
```

Reemplaza `"Dirección IP/Longitud de Prefijo"` con la dirección IPv4 y la longitud de prefijo que deseas asignar, por ejemplo, `192.168.1.2/24`.

4. **Configurar la puerta de enlace IPv4:**

```bash
sudo nmcli con mod "Nombre de la Conexión" ipv4.gateway "Puerta de Enlace"
```

Reemplaza `"Puerta de Enlace"` con la dirección de la puerta de enlace IPv4.

5. **Configurar los servidores DNS IPv4:**

```bash
sudo nmcli connection modify "Nombre de la Conexión" ipv4.dns "Servidores DNS Separados por Comas"
```

Reemplaza `"Servidores DNS Separados por Comas"` con las direcciones de los servidores DNS IPv4, separadas por comas.

### Configuración de Direcciones IPv6 con `nmcli`

1. **Configurar una dirección IPv6 estática:**

```bash
sudo nmcli connection modify "Nombre de la Conexión" ipv6.addresses "Dirección IPv6/Longitud de Prefijo"
```

Reemplaza `"Dirección IPv6/Longitud de Prefijo"` con la dirección IPv6 y la longitud de prefijo que deseas asignar, por ejemplo, `2001:db8::2/64`.

2. **Configurar la puerta de enlace IPv6:**

```bash
sudo nmcli connection modify "Nombre de la Conexión" ipv6.gateway "Puerta de Enlace"
```

Reemplaza `"Puerta de Enlace"` con la dirección de la puerta de enlace IPv6.

3. **Configurar los servidores DNS IPv6:**

```bash
sudo nmcli connection modify "Nombre de la Conexión" ipv6.dns "Servidores DNS IPv6 Separados por Comas"
```

Reemplaza `"Servidores DNS IPv6 Separados por Comas"` con las direcciones de los servidores DNS IPv6, separadas por comas.

4. **Guardar los cambios y reiniciar la conexión:**

```bash
sudo nmcli connection up "Nombre de la Conexión"
```

Reemplaza `"Nombre de la Conexión"` con el nombre de la conexión que estás configurando.

Estos son comandos básicos para configurar direcciones IPv4 e IPv6 en conexiones de red utilizando `nmcli`. Asegúrate de adaptar los comandos según la configuración específica del sistema y la interfaz de red que estás configurando.

### Ejemplos adicionales de modificación de una conexión con `nmcli`

1. **Asegurar que la conexión estática no se conecte automáticamente:**

```bash
nmcli con mod static connection.autoconnect no
```

Este comando modifica la configuración de la conexión llamada "static" para que no se conecte automáticamente cuando el sistema arranque o cuando la interfaz esté disponible.

2. **Añadir un servidor DNS a la conexión estática:**

```bash
nmcli con mod static ipv4.dns 10.0.0.10
```

Este comando agrega el servidor DNS `10.0.0.10` a la configuración de la conexión "static". Nota que al agregar la conexión se utiliza `ip4`, pero al modificar parámetros existentes se usa `ipv4`.

3. **Añadir un segundo servidor DNS:**

```bash
nmcli con mod static +ipv4.dns 8.8.8.8
```

Utilizando el signo `+`, este comando agrega un segundo servidor DNS (`8.8.8.8`) a la lista de servidores DNS para la conexión "static".

4. **Cambiar la dirección IP existente:**

```bash
nmcli con mod static ipv4.addresses 10.0.0.100/24
```

Este comando cambia la dirección IP de la conexión "static" a `10.0.0.100` con una máscara de subred de `24` bits.

5. **Añadir una segunda dirección IP:**

```bash
nmcli con mod static +ipv4.addresses 10.20.30.40/16
```

Este comando añade una segunda dirección IP (`10.20.30.40`) con una máscara de subred de `16` bits a la conexión "static".

6. **Activar los cambios realizados:**

```bash
nmcli con up static
```

Este comando activa la conexión "static" con las nuevas configuraciones que se han realizado.

**Resumen de los comandos**

| Comando | Descripción |
|---|---|
| `nmcli con mod static connection.autoconnect no` | Desactiva la conexión automática de la conexión "static". |
| `nmcli con mod static ipv4.dns 10.0.0.10` | Agrega un servidor DNS (`10.0.0.10`) a la conexión "static". |
| `nmcli con mod static +ipv4.dns 8.8.8.8` | Agrega un segundo servidor DNS (`8.8.8.8`) a la conexión "static". |
| `nmcli con mod static ipv4.addresses 10.0.0.100/24` | Cambia la dirección IP de la conexión "static" a `10.0.0.100/24`. |
| `nmcli con mod static +ipv4.addresses 10.20.30.40/16` | Agrega una segunda dirección IP (`10.20.30.40/16`) a la conexión "static". |
| `nmcli con up static` | Activa la conexión "static" con las configuraciones actualizadas. |

Estos pasos muestran cómo modificar varios parámetros de una conexión de red y cómo activarlos utilizando `nmcli`, lo que permite una administración flexible y detallada de las conexiones de red en un sistema Linux.

**Modificar la segunda dirección IP añadida a una conexión**

Para modificar la segunda dirección IP que se ha añadido a la conexión "static" utilizando `nmcli`, primero debes conocer la sintaxis exacta y los detalles de las direcciones IP que se están configurando:

1. **Eliminar la segunda dirección IP existente.** Suponiendo que la segunda IP es `10.20.30.40/16`, puedes eliminarla utilizando el siguiente comando:

```bash
nmcli con mod static -ipv4.addresses 10.20.30.40/16
```

2. **Añadir la nueva segunda dirección IP.** Supongamos que la nueva dirección IP es `10.20.30.50/16`:

```bash
nmcli con mod static +ipv4.addresses 10.20.30.50/16
```

3. **Activar los cambios realizados:**

```bash
nmcli con up static
```

### Ejemplo Completo

Si deseas realizar todos los pasos juntos, se vería así:

```bash
# Eliminar la segunda dirección IP existente
nmcli con mod static -ipv4.addresses 10.20.30.40/16

# Agregar la nueva segunda dirección IP
nmcli con mod static +ipv4.addresses 10.20.30.50/16

# Activar la conexión para aplicar los cambios
nmcli con up static
```

Este proceso asegura que la segunda dirección IP en la conexión "static" se actualice correctamente.

## Configure la resolución de nombre de host

Para configurar la resolución de nombres de host en sistemas basados en Red Hat (como RHEL, CentOS y Fedora), generalmente se utiliza el archivo `/etc/hosts` y la configuración del sistema. A continuación se detallan algunos pasos básicos.

### Modificar el Archivo `/etc/hosts`

1. **Abrir el archivo `/etc/hosts`:**

Utiliza un editor de texto para abrir el archivo `/etc/hosts`. Puedes utilizar `nano`, `vim` u otro editor de tu elección:

```bash
sudo nano /etc/hosts
```

2. **Añadir entradas de resolución de nombres:**

Añade líneas al archivo en el formato `dirección_IP nombre_de_host`. Por ejemplo:

```
192.168.1.2   servidor1
192.168.1.3   servidor2
```

Reemplaza `192.168.1.2`, `192.168.1.3`, `servidor1` y `servidor2` con las direcciones IP y los nombres de host que deseas configurar.

3. **Guardar y cerrar el archivo.**

### Configurar la Resolución de Nombres con NetworkManager (`nmcli`)

1. **Mostrar la configuración de la conexión:**

```bash
sudo nmcli connection show "Nombre de la Conexión"
```

Reemplaza `"Nombre de la Conexión"` con el nombre de la conexión que deseas configurar.

2. **Configurar el dominio de búsqueda DNS:**

```bash
sudo nmcli connection modify "Nombre de la Conexión" ipv4.dns-search "dominio.local"
```

Reemplaza `"dominio.local"` con tu dominio de búsqueda DNS.

3. **Reiniciar la conexión:**

```bash
sudo nmcli connection down "Nombre de la Conexión" && sudo nmcli connection up "Nombre de la Conexión"
```

Reemplaza `"Nombre de la Conexión"` con el nombre de la conexión que estás configurando.

### Notas Adicionales

- La configuración del archivo `/etc/hosts` tiene prioridad sobre la resolución de nombres a través de DNS. Asegúrate de que las entradas en `/etc/hosts` reflejen tu configuración deseada.
- La configuración de `nmcli` puede variar según la versión específica de NetworkManager en tu sistema. Ajusta los comandos según sea necesario.
- Si tu sistema utiliza un servidor DNS (por ejemplo, configurado en `/etc/resolv.conf`), asegúrate de que la resolución de nombres sea coherente con la configuración de DNS.

## Configure los servicios de red para que se inicien automáticamente al arrancar

Si te refieres a la configuración de interfaces de red en sistemas basados en Red Hat, ten en cuenta que la ubicación del archivo de configuración puede variar según la versión del sistema operativo.

**Configurar interfaces de red para iniciar automáticamente:**

1. **Identificar las interfaces de red:**

Puedes listar las interfaces de red disponibles con el siguiente comando:

```bash
ip link show
```

Identifica las interfaces que deseas configurar para que se inicien automáticamente.

2. **Acceder a la configuración de la interfaz:**

Abre el archivo de configuración de la interfaz que deseas editar. Por ejemplo, para una interfaz llamada `enp0s3`:

```bash
sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3
```

3. **Ajustar la configuración:**

Asegúrate de que el archivo de configuración contenga la siguiente línea para configurar la interfaz para que se inicie automáticamente al arrancar:

```
ONBOOT=yes
```

Puedes agregar esta línea si no está presente o cambiar `no` a `yes` si ya está presente.

4. **Guardar y cerrar el archivo.**

5. **Reiniciar el servicio de red (opcional):**

Puedes reiniciar el servicio de red para aplicar los cambios:

```bash
sudo systemctl restart network
```

> **Nota:** la ubicación del script y el nombre de la interfaz pueden variar según la versión específica del sistema operativo. Asegúrate de utilizar el nombre correcto para tu interfaz y la versión de Red Hat.

Además, ten en cuenta que en sistemas más recientes basados en Red Hat, como RHEL 8 y versiones más recientes, se ha producido una transición hacia `nmcli` y NetworkManager para la configuración de red. Si estás utilizando NetworkManager, puedes configurar interfaces mediante la interfaz gráfica [`nmtui`](https://access.redhat.com/documentation/es-es/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/adding-a-connection-profile-using-nmtui_configuring-and-managing-network-access) o mediante comandos `nmcli`.

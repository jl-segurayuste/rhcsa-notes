# Cambiar hostname y la IP

> **Enunciado:** Configure your Host Name, IP Address, Gateway and DNS.

- Host name: `station.domain40.example.com`
- `/etc/sysconfig/network` → `hostname=abc.com`
- `hostname abc.com`
- IP Address: `172.24.40.40/24`
- Gateway: `172.24.40.1`
- DNS: `172.24.40.1`

Para configurar el nombre de host, la dirección IP, la puerta de enlace y el DNS en un sistema Red Hat basado en la descripción dada, sigue los pasos detallados a continuación.

## Pasos para configurar el hostname, IP address, gateway y DNS

### 1. Configurar la dirección IP, puerta de enlace y DNS

1. Abre una terminal como root o utiliza `sudo` para los comandos.
2. Edita el archivo de configuración de la interfaz de red. Normalmente, los archivos de configuración de red se encuentran en `/etc/sysconfig/network-scripts/`.

   ```bash
   cd /etc/sysconfig/network-scripts/
   ls
   ```

3. Busca el archivo correspondiente a tu interfaz de red, por ejemplo, `ifcfg-eth0` o `ifcfg-enp0s3` dependiendo de la configuración de tu sistema. Edita este archivo:

   ```bash
   vim ifcfg-eth0
   ```

4. Configura los siguientes parámetros dentro del archivo:

   ```plain text
   IPADDR=172.24.40.40
   NETMASK=255.255.255.0
   GATEWAY=172.24.40.1
   DNS1=172.24.40.1
   ```

5. Guarda y cierra el archivo (`:wq` en vim).

### 2. Configurar el nombre de host

1. Edita el archivo `/etc/sysconfig/network`:

   ```bash
   vim /etc/sysconfig/network
   ```

2. Añade o modifica la línea `HOSTNAME` para que coincida con el nombre de host deseado:

   ```plain text
   HOSTNAME=station.domain40.example.com
   ```

3. Guarda y cierra el archivo (`:wq` en vim).

### 3. Aplicar los cambios

1. Reinicia el servicio de red para aplicar los cambios en la configuración de la red:

   ```bash
   systemctl restart network
   ```

2. Cambia el nombre de host temporalmente hasta que reinicies el sistema:

   ```bash
   hostnamectl set-hostname station.domain40.example.com
   ```

### 4. Verificación

1. Verifica la configuración de la red:

   ```bash
   ip addr show
   ```

   Asegúrate de que la dirección IP esté configurada correctamente.

2. Verifica la configuración del nombre de host:

   ```bash
   hostnamectl
   ```

3. Asegúrate de que puedas hacer ping a la puerta de enlace y al DNS:

   ```bash
   ping -c 4 172.24.40.1
   ```

## Comandos completos

```bash
# Paso 1: Configurar IP, Gateway y DNS
cd /etc/sysconfig/network-scripts/
vim ifcfg-eth0
# Añadir/editar las siguientes líneas:
# IPADDR=172.24.40.40
# NETMASK=255.255.255.0
# GATEWAY=172.24.40.1
# DNS1=172.24.40.1
:wq  # Guarda y sale del editor vim

# Paso 2: Configurar el nombre de host
vim /etc/sysconfig/network
# Añadir/editar la siguiente línea:
# HOSTNAME=station.domain40.example.com
:wq  # Guarda y sale del editor vim

# Paso 3: Aplicar cambios
systemctl restart network
hostnamectl set-hostname station.domain40.example.com

# Verificación
ip addr show
hostnamectl
ping -c 4 172.24.40.1
```

Siguiendo estos pasos, habrás configurado correctamente la dirección IP, puerta de enlace, DNS y nombre de host en tu sistema Red Hat.

> **Nota:** Este ejercicio usa la sintaxis clásica de `/etc/sysconfig/network` e `ifcfg-*` (RHEL 6/7 con `network` service). En RHEL 8/9 (RHCSA actual) la forma recomendada es usar `nmcli` para configurar IP, gateway y DNS, y `hostnamectl set-hostname` para el hostname persistente, ya que NetworkManager gestiona la red por defecto.

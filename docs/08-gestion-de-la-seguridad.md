# Gestión de la seguridad

## Restrinja el acceso a la red con un firewall o firewall-cmd (Capítulo 23)

### Comprendiendo el Firewall en Linux

#### Uso del Firewall

Un firewall se utiliza para limitar el tráfico entrante y saliente de un servidor. En Linux, el firewall se implementa en el kernel mediante el subsistema netfilter. Netfilter permite que los módulos del kernel inspeccionen cada paquete entrante, saliente o reenviado y actúen sobre ellos, permitiéndolos o bloqueándolos. El firewall del kernel permite la inspección de paquetes entrantes, salientes y aquellos que atraviesan de una interfaz a otra si el servidor RHEL proporciona funcionalidad de enrutamiento.

#### Soluciones Previas

Para interactuar con netfilter, se pueden usar diferentes soluciones. En versiones anteriores de Red Hat Enterprise Linux, iptables era la solución predeterminada para configurar el filtrado de paquetes netfilter. Esta solución funcionaba con la utilidad de línea de comandos iptables, que proporcionaba una forma detallada de definir reglas de firewall, pero resultaba complicada de usar para administradores ocasionales debido a la sintaxis compleja de los comandos iptables y a las reglas de ordenamiento que podían volverse relativamente complejas.

El servicio iptables ya no está disponible en Red Hat Enterprise Linux 8. Ha sido reemplazado por nftables, una solución más nueva con opciones más avanzadas que las ofrecidas por iptables. La herramienta de línea de comandos nftables ofrece una interfaz avanzada para escribir reglas directamente en nftables.

#### Comprendiendo Firewalld

En Red Hat Enterprise Linux 7 se introdujo una nueva solución de firewall: Firewalld es un servicio del sistema que puede configurar reglas de firewall utilizando diferentes interfaces. Los administradores pueden gestionar reglas en un entorno Firewalld, pero lo más importante es que las aplicaciones pueden solicitar que se abran puertos utilizando el sistema de mensajería DBus, lo que significa que las reglas pueden agregarse o eliminarse sin ninguna acción directa del administrador del sistema, permitiendo que las aplicaciones aborden el firewall desde el espacio del usuario.

Firewalld se desarrolló como una solución completamente nueva para gestionar los firewalls en Linux. Utiliza el servicio Firewalld para gestionar la configuración del firewall netfilter.

**Resumen**

- **Firewall en Linux**: Utiliza netfilter en el kernel para inspeccionar y controlar paquetes de red.
- **Soluciones Previas**: iptables era la herramienta anterior, ahora reemplazada por nftables en RHEL 8.
- **Firewalld**: Introducido en RHEL 7, es un servicio del sistema que permite la gestión dinámica de reglas de firewall, incluyendo la interacción con aplicaciones a través de DBus para abrir y cerrar puertos automáticamente.

### Comprendiendo las Zonas de Firewalld

#### Gestión de Firewalld con Zonas

Firewalld facilita la gestión del firewall mediante el uso de zonas. Una zona es una colección de reglas que se aplican a los paquetes entrantes que coinciden con una dirección de origen o interfaz de red específica. Por defecto, Firewalld se aplica solo a los paquetes entrantes y no filtra los paquetes salientes.

El uso de zonas es particularmente importante en servidores con múltiples interfaces. En tales servidores, las zonas permiten a los administradores asignar fácilmente un conjunto específico de reglas. En servidores con solo una interfaz de red, puede ser suficiente utilizar solo una zona, que es la zona predeterminada. Cada paquete que ingresa a un sistema es analizado por su dirección de origen y, basado en esa dirección de origen, Firewalld determina si el paquete pertenece a una zona específica. Si no es así, se utiliza la zona de la interfaz de red entrante. Si no hay una zona específica disponible, el paquete es manejado por las configuraciones en la zona predeterminada.

#### Zonas Predeterminadas de Firewalld

Firewalld funciona con algunas zonas predeterminadas. La Tabla 23-2 describe estas zonas predeterminadas.

| Nombre de la Zona | Configuración Predeterminada |
|---|---|
| **block** | Las conexiones de red entrantes son rechazadas con un mensaje "icmp-host-prohibited". Solo se permiten conexiones de red iniciadas en este sistema. |
| **dmz** | Para uso en computadoras en la zona desmilitarizada (DMZ). Solo se aceptan conexiones entrantes seleccionadas y se permite el acceso limitado a la red interna. |
| **drop** | Cualquier paquete entrante es descartado y no hay respuesta. |
| **external** | Para uso en redes externas con enmascaramiento (Network Address Translation [NAT]) habilitado, utilizado especialmente en routers. Solo se aceptan conexiones entrantes seleccionadas. |
| **home** | Para uso en redes domésticas. La mayoría de las computadoras en la misma red son de confianza y solo se aceptan conexiones entrantes seleccionadas. |
| **internal** | Para uso en redes internas. La mayoría de las computadoras en la misma red son de confianza y solo se aceptan conexiones entrantes seleccionadas. |
| **public** | Para uso en áreas públicas. Otras computadoras en la misma red no son de confianza y se aceptan conexiones limitadas. Esta es la zona predeterminada para todas las interfaces de red recién creadas. |
| **trusted** | Se aceptan todas las conexiones de red. |
| **work** | Para uso en áreas de trabajo. La mayoría de las computadoras en la misma red son de confianza y solo se aceptan conexiones entrantes seleccionadas. |

### Comprendiendo los Servicios de Firewalld

#### Servicios en Firewalld

El segundo elemento clave al trabajar con Firewalld es el servicio. Es importante notar que un servicio en Firewalld no es lo mismo que un servicio en systemd; un servicio de Firewalld especifica qué tráfico entrante y saliente debe ser aceptado en el firewall. Típicamente, incluye los puertos que deben abrirse, así como los módulos del kernel que deben cargarse. En Firewalld, algunos servicios predeterminados están definidos, lo que permite a los administradores permitir o denegar fácilmente el acceso a puertos específicos en un servidor.

Detrás de cada servicio hay un archivo de configuración que explica qué puertos UDP o TCP están involucrados y, si es necesario, qué módulos del kernel deben cargarse. Para obtener una lista de todos los servicios disponibles en su computadora, puede usar el comando `firewall-cmd --get-services` (ver Ejemplo 23-1).

**Ejemplo 23-1: Uso de `firewall-cmd --get-services` para Obtener una Lista de Todos los Servicios Disponibles**

```shell
[root@server1 ~]# firewall-cmd --get-services
RH-Satellite-6 amanda-client amanda-k5-client amqp amqps apcupsd
audit bacula bacula-client bgp bitcoin bitcoin-rpc bitcoin-testnet
bitcoin-testnet-rpc ceph ceph-mon cfengine cockpit condor-collector
ctdb dhcp dhcpv6 dhcpv6-client distcc dns docker-registry dockerswarm dropbox-lansync elasticsearch etcd-client etcd-server finger
freeipa-ldap freeipa-ldaps freeipa-replication freeipa-trust ftp
ganglia-client ganglia-master git gre high-availability http https
imap imaps ipp ipp-client ipsec irc ircs iscsi-target isns jenkins
kadmin kerberos kibana klogin kpasswd kprop kshell ldap ldaps
libvirt libvirt-tls lightning-network llmnr managesieve matrix mdns
minidlna mongodb mosh mountd mqtt mqtt-tls ms-wbt mssql murmur
mysql nfs nfs3 nmea-0183 nrpe ntp nut openvpn ovirt-imageio ovirtstorageconsole ovirt-vmconsole plex pmcd pmproxy pmwebapi pmwebapis
pop3 pop3s postgresql privoxy proxy-dhcp ptp pulseaudio puppetmaster
quassel radius redis rpc-bind rsh rsyncd rtsp salt-master samba
samba-client samba-dc sane sip sips slp smtp smtp-submission smtps
snmp snmptrap spideroak-lansync squid ssh steam-streaming svdrp svn
syncthing syncthing-gui synergy syslog syslog-tls telnet tftp tftpclient tinc tor-socks transmission-client upnp-client vdsm vncserver wbem-http wbem-https wsman wsmans xdmcp xmpp-bosh xmpp-client
xmpp-local xmpp-server zabbix-agent zabbix-server
```

En esencia, al trabajar con Firewalld, se trata de agregar los servicios correctos a las zonas correctas. En casos especiales, la configuración puede mejorarse con ajustes más específicos.

#### Archivos de Configuración de Servicios

Detrás de cada servicio hay un archivo de configuración en formato XML. Estos archivos XML se pueden modificar fácilmente. Los archivos XML predeterminados instalados por RPM se almacenan en `/usr/lib/firewalld/services`. Los archivos XML personalizados se pueden agregar al directorio `/etc/firewalld/services` y se detectarán automáticamente después de reiniciar el servicio Firewalld.

**Resumen**

- **Servicios en Firewalld**: Especifican el tráfico entrante y saliente permitido, incluyendo puertos y módulos del kernel.
- **Archivos de Configuración**: Cada servicio tiene un archivo de configuración XML que define los puertos y módulos necesarios.
- **Comando clave**: `firewall-cmd --get-services` proporciona una lista de todos los servicios disponibles.
- **Ubicación de los Archivos**: Archivos XML predeterminados en `/usr/lib/firewalld/services`, archivos personalizados en `/etc/firewalld/services`.

### Trabajando con Servicios en Firewalld

Al trabajar con Firewalld, es esencial asignar los servicios correctos a las zonas adecuadas. Los servicios en Firewalld determinan qué tráfico de red debe permitirse o bloquearse, y suelen incluir los puertos que deben abrirse y los módulos del kernel que deben cargarse.

#### Configuración de Servicios

Detrás de cada servicio en Firewalld hay un archivo de configuración en formato XML. Estos archivos XML definen los detalles de los servicios, como los puertos y protocolos específicos, y cualquier módulo de kernel necesario. Los archivos XML predeterminados instalados por paquetes RPM se encuentran en `/usr/lib/firewalld/services`. Si necesitas personalizar un servicio, puedes crear un archivo XML en el directorio `/etc/firewalld/services`. Los archivos personalizados en este directorio se reconocen automáticamente después de reiniciar el servicio Firewalld.

Modificar y gestionar estos archivos XML permite un control detallado sobre el tráfico de red y la configuración del firewall en sistemas basados en Linux.

Restringe el acceso a la red con `firewalld` y su interfaz de línea de comandos `firewall-cmd`:

1. **Verificar el Estado del Firewall:** Antes de realizar cambios, verifica el estado actual del firewall:
   ```shell
   sudo firewall-cmd --state
   ```
2. **Habilitar `firewalld` (si no está habilitado):**
   ```shell
   sudo systemctl enable firewalld
   sudo systemctl start firewalld
   ```
3. **Verificar las Zonas Disponibles:**
   ```shell
   sudo firewall-cmd --get-zones
   ```
4. **Identificar la Zona de Red a Configurar:** Selecciona la zona de red adecuada para tu interfaz de red. Puedes ver la interfaz activa con el comando:
   ```shell
   ip link show
   ```
5. **Configurar Reglas de Firewall:** Asumamos que la interfaz activa es `enp0s3` y quieres permitir tráfico SSH y bloquear todo lo demás:
   ```shell
   # Asegurarse de que la interfaz está asignada a una zona específica
   sudo firewall-cmd --zone=public --change-interface=enp0s3 --permanent

   # Permitir tráfico SSH
   sudo firewall-cmd --zone=public --add-service=ssh --permanent

   # Bloquear todo lo demás
   sudo firewall-cmd --zone=public --remove-service=dhcp --permanent
   sudo firewall-cmd --zone=public --remove-service=dns --permanent
   sudo firewall-cmd --zone=public --remove-service=http --permanent
   # Añadir más servicios según sea necesario

   # Recargar las reglas del firewall
   sudo firewall-cmd --reload
   ```
6. **Verificar las Reglas Configuradas:** Puedes ver las reglas configuradas en la zona seleccionada con el siguiente comando:
   ```shell
   sudo firewall-cmd --list-all
   ```

#### Ejercicio 23-1: Gestionando el Firewall con `firewall-cmd`

Este ejercicio te guía a través de varios comandos `firewall-cmd` para gestionar y configurar el firewall en un sistema Linux utilizando Firewalld.

**Paso a Paso:**

1. **Abrir un shell como root**: Esto garantiza que tienes los permisos necesarios para gestionar el firewall.
2. **Mostrar la zona predeterminada**:
   ```shell
   firewall-cmd --get-default-zone
   ```
   Este comando muestra la zona predeterminada actual del firewall, que generalmente es `public`.
3. **Ver las zonas disponibles**:
   ```shell
   firewall-cmd --get-zones
   ```
   Lista todas las zonas disponibles que puedes utilizar en Firewalld.
4. **Mostrar los servicios disponibles en el servidor**:
   ```shell
   firewall-cmd --get-services
   ```
   Muestra todos los servicios predefinidos que Firewalld puede gestionar.
5. **Ver los servicios en la zona actual**:
   ```shell
   firewall-cmd --list-services
   ```
   Muestra una lista de servicios que están permitidos en la zona predeterminada actual.
6. **Listar toda la configuración del firewall**:
   ```shell
   firewall-cmd --list-all
   firewall-cmd --list-all --zone=public
   ```
   Muestra un resumen completo de la configuración del firewall, incluyendo interfaces, servicios, puertos y otras configuraciones avanzadas.

**Ejemplo de Salida de Configuración Actual del Firewall:**

```shell
[root@server1 ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: ens160
  sources:
  services: cockpit dhcpv6-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

1. **Agregar un servicio al firewall**:
   ```shell
   firewall-cmd --add-service=vnc-server
   firewall-cmd --list-all
   ```
   Agrega el servicio `vnc-server` a la configuración del firewall actual. Verifica que el servicio se haya agregado correctamente.
2. **Reiniciar el servicio Firewalld**:
   ```shell
   systemctl restart firewalld
   firewall-cmd --list-all
   ```
   Reinicia Firewalld y observa que el servicio `vnc-server` ya no está listado, porque solo se agregó a la configuración en tiempo de ejecución, no a la configuración persistente.
3. **Agregar el servicio de forma permanente**:
   ```shell
   firewall-cmd --add-service=vnc-server --permanent
   firewall-cmd --list-all
   ```
   Agrega el servicio `vnc-server` de forma permanente. Verifica y nota que no se muestra inmediatamente en la configuración en tiempo de ejecución.
4. **Recargar la configuración del firewall**:
   ```shell
   firewall-cmd --reload
   firewall-cmd --list-all
   ```
   Recarga la configuración persistente en la configuración en tiempo de ejecución para aplicar los cambios.
5. **Agregar un puerto al firewall**:
   ```shell
   firewall-cmd --add-port=2022/tcp --permanent
   firewall-cmd --reload
   firewall-cmd --list-all
   ```
   Agrega el puerto `2022/tcp` a la configuración del firewall de forma permanente y recarga la configuración. Verifica que el puerto se haya agregado correctamente.

**Resumen**

- **Zona predeterminada**: Verifica y trabaja con la zona predeterminada del firewall.
- **Servicios y zonas**: Lista y gestiona servicios y zonas del firewall.
- **Configuración persistente vs. en tiempo de ejecución**: Entiende la diferencia entre hacer cambios temporales y permanentes, y cómo recargar la configuración para aplicar cambios permanentes.
- **Añadir servicios y puertos**: Aprende a agregar servicios y puertos tanto temporal como permanentemente al firewall.

### Configuración Persistente vs. Configuración en Tiempo de Ejecución en Firewalld

En Firewalld, los cambios que realizas pueden ser temporales (en tiempo de ejecución) o permanentes. Es importante entender la diferencia entre estos dos tipos de configuración y cómo gestionarlos.

**Configuración en Tiempo de Ejecución**

- **Temporal**: Los cambios en tiempo de ejecución se aplican inmediatamente y afectan al estado actual del firewall. Sin embargo, estos cambios no se guardan en el disco.
- **Volatilidad**: Los cambios temporales se pierden cuando se reinicia el servicio Firewalld o se reinicia el sistema.

**Configuración Persistente**

- **Permanente**: Los cambios permanentes se guardan en los archivos de configuración del disco. Esto significa que estos cambios persisten a través de los reinicios del servicio Firewalld y del sistema.
- **Aplicación**: Los cambios permanentes no afectan inmediatamente el estado actual del firewall hasta que se recargue la configuración.

**Ejemplo Práctico**

Vamos a ilustrar esta diferencia con un ejemplo concreto.

1. **Agregar un Servicio en Tiempo de Ejecución**:
   ```shell
   firewall-cmd --add-service=vnc-server
   ```
   Este comando agrega el servicio `vnc-server` temporalmente. El servicio estará activo inmediatamente pero se perderá si se reinicia Firewalld o el sistema.
2. **Verificar los Servicios Activos**:
   ```shell
   firewall-cmd --list-all
   ```
   Verifica que `vnc-server` esté en la lista de servicios activos.
3. **Reiniciar Firewalld**:
   ```shell
   systemctl restart firewalld
   firewall-cmd --list-all
   ```
   Después de reiniciar Firewalld, el servicio `vnc-server` ya no estará en la lista de servicios activos porque solo se agregó temporalmente.
4. **Agregar un Servicio de Forma Permanente**:
   ```shell
   firewall-cmd --add-service=vnc-server --permanent
   ```
   Este comando agrega el servicio `vnc-server` de forma permanente, guardando el cambio en la configuración del disco. Sin embargo, este cambio no se aplicará inmediatamente.
5. **Recargar la Configuración**:
   ```shell
   firewall-cmd --reload
   firewall-cmd --list-all
   ```
   Recarga la configuración persistente en el estado actual del firewall. Ahora, `vnc-server` estará activo y este cambio persistirá a través de reinicios.
6. **Agregar un Puerto de Forma Permanente y Recargar**:
   ```shell
   firewall-cmd --add-port=2022/tcp --permanent
   firewall-cmd --reload
   firewall-cmd --list-all
   ```
   Agrega el puerto `2022/tcp` de forma permanente y recarga la configuración para que el cambio se aplique inmediatamente.

**Resumen**

- **Cambios en Tiempo de Ejecución**: Se aplican inmediatamente pero no persisten a través de reinicios.
- **Cambios Permanentes**: Se guardan en el disco y persisten a través de reinicios, pero no se aplican inmediatamente hasta que se recargue la configuración.
- **Recargar Configuración**: Utiliza `firewall-cmd --reload` para aplicar cambios permanentes a la configuración actual del firewall.

#### Tabla 23-3: Opciones comunes de firewall-cmd

| Opciones de firewall-cmd | Explicación |
|---|---|
| `--get-zones` | Lista todas las zonas disponibles |
| `--get-default-zone` | Muestra la zona que actualmente está configurada como la zona predeterminada |
| `--set-default-zone=<ZONE>` | Cambia la zona predeterminada |
| `--get-services` | Muestra todos los servicios disponibles |
| `--list-services` | Muestra los servicios que están en uso actualmente |
| `--add-service=<service-name> [--zone=<ZONE>]` | Agrega un servicio a la zona predeterminada actual o a la zona especificada |
| `--remove-service=<service-name>` | Elimina un servicio de la configuración |
| `--list-all [--zone=<ZONE>]` | Lista todas las configuraciones en una zona |
| `--add-port=<port/protocol> [--zone=<ZONE>]` | Agrega un puerto y protocolo |
| `--remove-port=<port/protocol> [--zone=<ZONE>]` | Elimina un puerto de la configuración |
| `--add-interface=<INTERFACE> [--zone=<ZONE>]` | Agrega una interfaz a la zona predeterminada o a una zona específica |
| `--remove-interface=<INTERFACE> [--zone=<ZONE>]` | Elimina una interfaz de una zona específica |
| `--add-source=<ipaddress/netmask> [--zone=<ZONE>]` | Agrega una dirección IP específica |
| `--remove-source=<ipaddress/netmask> [--zone=<ZONE>]` | Elimina una dirección IP de la configuración |
| `--permanent` | Escribe la configuración en el disco y no en el tiempo de ejecución |
| `--reload` | Recarga la configuración en disco |

> **Nota:** La principal diferencia entre "drop" y "reject" radica en cómo responden al tráfico bloqueado:
>
> 1. **Drop (descartar):**
>    - Cuando se utiliza la acción "drop", el firewall descarta silenciosamente el tráfico bloqueado.
>    - Esto significa que el tráfico bloqueado simplemente se desecha y no se envía ninguna respuesta al remitente.
>    - Desde la perspectiva del remitente, el tráfico simplemente parece perderse en la red, sin recibir ninguna notificación de que fue bloqueado por el firewall.
> 2. **Reject (rechazar):**
>    - Cuando se utiliza la acción "reject", el firewall responde activamente al tráfico bloqueado con un mensaje de error.
>    - Este mensaje de error notifica al remitente que su tráfico fue bloqueado por el firewall.
>    - En lugar de descartar silenciosamente el tráfico, el firewall envía una respuesta de "rechazo" al remitente para informarle que su solicitud fue denegada.

## Gestione los permisos predeterminados de archivos

La gestión de permisos predeterminados de archivos se realiza principalmente a través de los valores umask y de los atributos predeterminados en los sistemas de archivos.

### Umask

El umask es una máscara de bits que se utiliza para establecer permisos predeterminados para nuevos archivos y directorios. Se resta del conjunto completo de permisos (777 para directorios y 666 para archivos) para determinar los permisos reales.

> Ojo: no es una resta, son bits que se **quitan**. Con `umask 033` un fichero queda en `644` (no en `666 − 033 = 633`) y un directorio en `744`: el `3` quita `w` y `x`, y el fichero no tenía `x` que quitar.

Para ver el umask actual:
```shell
umask
```

Para cambiar el umask (por ejemplo, a 0022):
```shell
umask 0022
```

### Atributos Predeterminados de Archivos y Directorios

Algunos sistemas de archivos permiten configurar atributos predeterminados para archivos y directorios. Esto puede hacerse utilizando el comando `setfacl`. A continuación, te doy un ejemplo básico:

1. **Instalar acl (Access Control List) si no está instalado:**
   ```shell
   sudo yum install acl   # Para sistemas basados en yum (como RHEL, CentOS)
   ```
2. **Establecer permisos predeterminados para un directorio:**
   ```shell
   # Ejemplo: Establecer permisos predeterminados para archivos en el directorio /mi/directorio
   sudo setfacl -m d:u::rwX,d:g::rwX,d:o::r-X /mi/directorio
   ```

En este ejemplo, se establecen permisos predeterminados para usuarios (`u`), grupos (`g`) y otros (`o`) dentro del directorio especificado. Puedes ajustar los permisos según tus necesidades.

- `setfacl`: Es el comando utilizado para establecer permisos de control de acceso.
- `m`: Indica que se están modificando (o añadiendo) permisos.
- `d:u::rwX,d:g::rwX,d:o::r-X`: Son las reglas ACL que se están aplicando.
  - `d:u::rwX`: Establece permisos predeterminados para el propietario (`u`, usuario) en `rwX`: lectura y escritura, más `x` en los directorios (y en los ficheros que ya fueran ejecutables).
  - `d:g::rwX`: Establece permisos predeterminados para el grupo del directorio (`g`, grupo) en `rwX`.
  - `d:o::r-X`: Establece permisos predeterminados para otros usuarios (`o`, others) en `r-X`: lectura en los ficheros y lectura más acceso (`x`) en los directorios. La `X` mayúscula solo da ejecución a los directorios y a los ficheros que ya eran ejecutables.
- `/mi/directorio`: Es la ruta al directorio al cual se le están aplicando los permisos ACL.

> **Nota:** El comando `chmod` y `setfacl` son herramientas relacionadas pero tienen propósitos ligeramente diferentes en Linux.
>
> 1. **`chmod` (Change Mode):**
>    - `chmod` es un comando que se utiliza para cambiar los permisos de un archivo o directorio en Linux. Puedes utilizar `chmod` para cambiar los permisos de lectura, escritura y ejecución para el propietario, el grupo y otros usuarios.
>
>    Ejemplo:
>    ```shell
>    chmod 755 archivo.txt
>    ```
>    Esto establece permisos de lectura, escritura y ejecución para el propietario, y permisos de lectura y ejecución para el grupo y otros usuarios.
> 2. **`setfacl` (Set File Access Control Lists):**
>    - `setfacl` se utiliza para establecer permisos de control de acceso (ACL) adicionales en archivos y directorios. Los ACL permiten definir permisos más granulares que los permisos tradicionales de Unix (lectura, escritura, ejecución). Puedes asignar permisos a usuarios y grupos específicos.
>
>    Ejemplo:
>    ```shell
>    setfacl -m u:usuario1:rw archivo.txt
>    ```
>    Esto otorga permisos de lectura y escritura al usuario específico "usuario1" además de los permisos existentes.
>
> **Diferencias clave:**
> - `chmod` se centra en los permisos tradicionales de Unix (lectura, escritura, ejecución) y actúa sobre el propietario, grupo y otros.
> - `setfacl` se utiliza para gestionar permisos ACL, lo que permite definir permisos para usuarios y grupos específicos, y proporciona un mayor nivel de control en comparación con `chmod`.

### ACL: Listas de control de acceso

Las Listas de Control de Acceso (ACLs, por sus siglas en inglés) son una característica de los sistemas de archivos de Linux que permiten definir permisos a nivel de usuario o grupo, más allá de los permisos estándar de propietario, grupo y otros.

Las ACLs permiten asignar permisos de lectura, escritura y ejecución a usuarios y grupos específicos para un archivo o directorio. Esto puede ser útil en entornos donde los permisos estándar no son suficientemente granulares.

> **Consejo:** Puedes usar `getfacl` para crear una copia de seguridad de los ACL, que luego puede ser restaurada usando el comando `setfacl`. Para crear la copia de seguridad, utiliza `getfacl -R /directorio > archivo.acls`. Para restaurar las configuraciones desde el archivo de copia de seguridad, utiliza `setfacl --restore=archivo.acl`.
>
> **Atención:** Hay que tener en cuenta que se debe estar en el directorio desde dónde se quiere realizar la copia de seguridad, ya sea de manera recursiva o solo del directorio indicado. Si no se ubica dentro del directorio desde el que se quiere realizar la copia, dará problemas con la ruta.
>
> > [Imagen no migrada: Captura de pantalla del error producido al ejecutar la copia de seguridad de ACL sin estar situado en la ruta correcta]
>
> Se muestra el comando con el que se realiza la copia de seguridad:
> ```shell
> getfacl -R . > backup.acl
> ```
> Comando para restaurar la copia de seguridad:
> ```shell
> setfacl --restore=backup.acl
> ```

Aquí te dejo un ejemplo de cómo usar las ACLs:

1. **Ver las ACLs de un archivo**: Para ver las ACLs de un archivo, puedes usar el comando `getfacl`. Por ejemplo:
   ```bash
   getfacl archivo.txt
   ```
   Esto mostrará las ACLs actuales para `archivo.txt`.
2. **Establecer una ACL para un usuario**: Para establecer una ACL para un usuario, puedes usar el comando `setfacl`. Por ejemplo, para dar al usuario `usuario1` permisos de lectura y escritura en `archivo.txt`, puedes usar:
   ```shell
   setfacl -m u:usuario1:rw archivo.txt
   ```
   Esto establece una ACL que da a `usuario1` permisos de lectura (`r`) y escritura (`w`) en `archivo.txt`.
3. **Eliminar una ACL**: Para eliminar una ACL, puedes usar la opción `x` con `setfacl`. Por ejemplo, para eliminar la ACL para `usuario1` en `archivo.txt`, puedes usar:
   ```shell
   setfacl -x u:usuario1 archivo.txt
   ```
   Esto eliminará la ACL para `usuario1` en `archivo.txt`.

El comando `setfacl -m d:g:sales:rx /data` establece una ACL (Lista de Control de Acceso) predeterminada en el directorio `/data`. Aquí te explico cada parte del comando:

- `setfacl`: Es el comando que se usa para establecer las ACLs.
- `m`: Esta opción se usa para modificar la ACL de un archivo o directorio.
- `d:`: Esta opción se usa para establecer una ACL predeterminada. Las ACLs predeterminadas se aplican a todos los nuevos archivos y subdirectorios que se creen en el directorio.
- `g:sales:rx`: Esta es la ACL que se está estableciendo. `g` indica que se trata de una ACL de grupo. `sales` es el nombre del grupo. `rx` son los permisos que se están otorgando, en este caso, lectura (`r`) y ejecución (`x`).

Por lo tanto, este comando otorga al grupo `sales` permisos de lectura y ejecución en todos los nuevos archivos y subdirectorios que se creen en el directorio `/data`. Sin embargo, no afecta a los archivos y subdirectorios que ya existen en `/data`.

> **Nota:** Este consejo se refiere a cómo configurar las Listas de Control de Acceso (ACLs) para un directorio de manera que afecten tanto a los archivos y subdirectorios actuales como a los que se creen en el futuro.
>
> 1. **`setfacl -R -m`**: Este comando se usa para modificar las ACLs de los archivos y subdirectorios actuales en un directorio. La opción `R` hace que el comando sea recursivo, es decir, se aplica a todos los archivos y subdirectorios dentro del directorio especificado. La opción `m` se usa para modificar la ACL. Por ejemplo, si quieres dar al usuario `usuario1` permisos de lectura y escritura en todos los archivos y subdirectorios dentro del directorio `directorio1`, puedes usar:
>    ```shell
>    setfacl -R -m u:usuario1:rw directorio1
>    ```
> 2. **`setfacl -m d:`**: Este comando se usa para establecer una ACL predeterminada en un directorio. Las ACLs predeterminadas se aplican a todos los nuevos archivos y subdirectorios que se creen en el directorio. Por ejemplo, si quieres que todos los nuevos archivos y subdirectorios en `directorio1` tengan permisos de lectura y escritura para `usuario1`, puedes usar:
>    ```shell
>    setfacl -m d:u:usuario1:rw directorio1
>    ```
>
> Así, cuando configures las ACLs para un directorio, tendrás que usar ambos comandos: `setfacl -R -m` para los archivos y subdirectorios actuales, y `setfacl -m d:` para los archivos y subdirectorios que se creen en el futuro.

> **Atención:** Antes de empezar a trabajar con las Listas de Control de Acceso (ACLs), es posible que tengas que preparar tu sistema de archivos para soportar las ACLs. Debido a que los metadatos del sistema de archivos necesitan ser extendidos, no siempre hay soporte por defecto para las ACLs en el sistema de archivos.
>
> Si al intentar establecer las ACLs en un sistema de archivos recibes un mensaje que dice "operación no soportada", probablemente tu sistema de archivos no tiene soporte para las ACLs. Para solucionar esto, necesitas agregar la opción de montaje **`acl`** en el archivo **`/etc/fstab`** para que el sistema de archivos se monte con soporte para ACLs por defecto.
>
> El archivo `/etc/fstab` contiene la información necesaria para montar automáticamente las particiones y dispositivos de almacenamiento en el sistema de archivos en el arranque del sistema. En este archivo, puedes especificar varias opciones de montaje para cada dispositivo, incluyendo `acl` para habilitar el soporte de ACLs. En RHEL no hace falta: XFS siempre las admite y ext4 las activa por defecto (lo muestra `tune2fs -l /dev/… | grep 'Default mount options'`). Solo es necesario en sistemas de ficheros creados sin esa opción.
>
> 1. **Abre el archivo `/etc/fstab` con un editor de texto**. Puedes usar cualquier editor de texto que prefieras. Por ejemplo, si quieres usar `nano`, puedes escribir en la terminal:
>    ```shell
>    sudo nano /etc/fstab
>    ```
> 2. **Busca la línea que corresponde a la partición del sistema de archivos** donde quieres habilitar el soporte de ACL. Esta línea tendrá una estructura similar a esta:
>    ```shell
>    UUID=ejemplo-de-uuid /punto/de/montaje ext4 defaults 0 2
>    ```
> 3. **Añade la opción `acl` a las opciones de montaje**. Las opciones de montaje son la cuarta columna, y están separadas por comas. Después de añadir `acl`, la línea debería verse así:
>    ```shell
>    UUID=ejemplo-de-uuid /punto/de/montaje ext4 defaults,acl 0 2
>    ```
> 4. **Guarda y cierra el archivo**. Si estás usando `nano`, puedes hacerlo pulsando `Ctrl+X`, luego `Y` para confirmar, y finalmente `Enter` para salir.
> 5. **Monta de nuevo el sistema de archivos**. Puedes hacerlo con el comando `mount -a`, que montará todos los sistemas de archivos especificados en `/etc/fstab`.
>
> Ten cuidado al modificar `/etc/fstab`: un error en este archivo puede hacer que el sistema no arranque correctamente.

> **Nota:** Cuando ves un símbolo "+" al final de la lista de permisos, como en "drwxr-xr-x+", indica que hay configuraciones de ACL (Listas de Control de Acceso) aplicadas al archivo o directorio.
>
> > [Imagen no migrada: Captura de pantalla de un listado `ls -l` mostrando el símbolo "+" al final de los permisos indicando ACL aplicadas]
>
> Para ver las ACL específicas aplicadas a un archivo o directorio, puedes usar el comando `getfacl`.
> ```bash
> getfacl nombre_del_directorio
> ```
>
> > [Imagen no migrada: Captura de pantalla de la salida del comando `getfacl` sobre un directorio]

Comandos adicionales relacionados con ACL (Listas de Control de Acceso) que pueden ser útiles:

1. **`setfacl`:**
   ```shell
   setfacl -m u:usuario:permisos,d:u:usuario:permisos nombre_del_archivo_o_directorio
   ```
   Establece reglas específicas de ACL para un usuario en particular en un archivo o directorio.
2. **`getfattr`:**
   ```shell
   getfattr -d nombre_del_archivo_o_directorio
   ```
   Muestra los atributos extendidos, incluidas las ACL, asociados con un archivo o directorio.
3. **`setfattr`:**
   ```shell
   setfattr -n user.nombre_atributo -v valor nombre_del_archivo_o_directorio
   ```
   Establece un atributo extendido, como una ACL específica, para un archivo o directorio.
4. **`chacl`:**
   ```shell
   chacl --remove-all nombre_del_archivo_o_directorio
   ```
   Elimina todas las ACL del archivo o directorio.

El soporte para ACL debe estar habilitado en el sistema de archivos y el kernel para que estos comandos funcionen.

> **Atención:** Para el examen de RHCSA, es importante conocer las utilidades de archivado y compresión disponibles en Linux. Aunque la herramienta más comúnmente utilizada y conocida es `tar`, también puedes encontrarte con `star`, que es una herramienta avanzada para la creación de archivos y copias de seguridad.
>
> **¿Qué es `star`?**
>
> `star` (Schily's tar) es una versión mejorada del comando `tar` con varias características adicionales. Es parte del paquete de herramientas Schily Tools.
>
> **Características de `star`**
>
> - **Velocidad**: `star` es conocido por ser muy rápido en comparación con otras herramientas de archivado.
> - **Compatibilidad**: Soporta varios formatos de archivado (`tar`, `cpio`, `pax`, etc.).
> - **Fiabilidad**: Tiene características avanzadas para la verificación de datos y la recuperación de errores.
> - **Soporte extendido de metadatos**: `star` puede manejar atributos extendidos y ACLs (listas de control de acceso), lo cual es útil para sistemas que requieren una conservación precisa de permisos y atributos de archivo.
>
> **Comandos básicos de `star`**
>
> A continuación se presentan algunos comandos básicos de `star` que podrían ser útiles para el examen RHCSA:
>
> 1. **Crear un archivo**:
>    ```shell
>    star -c -f archivo.star /ruta/del/directorio
>    ```
>    - `c`: Crea un nuevo archivo.
>    - `f archivo.star`: Especifica el nombre del archivo.
> 2. **Extraer un archivo**:
>    ```shell
>    star -x -f archivo.star
>    ```
>    - `x`: Extrae los archivos del archivo especificado.
>    - `f archivo.star`: Especifica el archivo a extraer.
> 3. **Listar el contenido de un archivo**:
>    ```shell
>    star -t -f archivo.star
>    ```
>    - `t`: Lista el contenido del archivo.
>    - `f archivo.star`: Especifica el archivo a listar.
> 4. **Copiar archivos preservando atributos extendidos**:
>    ```shell
>    star -c -xattr -acl -f archivo.star /ruta/del/directorio
>    ```
>    - `xattr`: Maneja atributos extendidos.
>    - `acl`: Maneja listas de control de acceso (ACLs).
>
> **Ejemplo de uso práctico**
>
> Supongamos que quieres crear una copia de seguridad de `/etc` y `/home` en un archivo llamado `backup.star`, y luego extraerlo:
>
> 1. **Crear el archivo de respaldo**:
>    ```shell
>    star -c -f backup.star /etc /home
>    ```
> 2. **Extraer el archivo de respaldo**:
>    ```shell
>    star -x -f backup.star
>    ```
>
> **Comparación con `tar`**
>
> Aunque `star` tiene muchas características avanzadas, para el examen RHCSA es más probable que te encuentres con `tar` debido a su ubicuidad y simplicidad. En RHEL 8+ `tar` también conserva ACL, atributos extendidos y contextos SELinux: `tar --acls --xattrs --selinux -czf copia.tar.gz dir/` (y las mismas opciones al extraer). Sin embargo, conocer `star` y sus capacidades adicionales puede darte una ventaja en situaciones donde se requiera un archivado más avanzado.
>
> **Consideraciones de seguridad**
>
> Al igual que con cualquier herramienta que maneja archivos críticos del sistema, ten en cuenta los permisos y los contextos de seguridad de SELinux al utilizar `star`.

### Persistencia de Umask y Atributos Predeterminados

Para que los cambios en el umask sean persistentes, puedes configurar el umask en el archivo de inicio del shell, como `~/.bashrc` o `~/.bash_profile`.

Las ACL (también las predeterminadas, `d:`) ya son persistentes: se guardan en los metadatos del sistema de ficheros y sobreviven a los reinicios, sin scripts de inicio. Lo que no se conserva es al copiar sin `cp -a` ni al archivar con `tar` sin `--acls`.

## Configure la autenticación basada en claves para SSH

La autenticación basada en claves para SSH permite acceder a sistemas remotos sin ingresar contraseñas cada vez.

1. **Generar un Par de Claves en el Cliente:** En el cliente desde el cual te conectarás al servidor, ejecuta el siguiente comando para generar un par de claves SSH (si aún no tienes uno):
   ```shell
   ssh-keygen -t ed25519            # o: ssh-keygen -t rsa -b 4096
   ```
   Este comando creará un par de claves pública (`id_ed25519.pub`) y privada (`id_ed25519`) en `~/.ssh/`.
   Si el servidor tiene SELinux y el `authorized_keys` se copió a mano, `restorecon -Rv ~/.ssh`.
2. **Copiar la Clave Pública al Servidor:** Utiliza el comando `ssh-copy-id` para copiar tu clave pública al servidor:
   ```shell
   ssh-copy-id usuario@servidor
   ```
   Este comando instalará tu clave pública en el archivo `~/.ssh/authorized_keys` en el servidor.

   Si `ssh-copy-id` no está disponible, puedes hacerlo manualmente:
   ```shell
   cat ~/.ssh/id_ed25519.pub | ssh usuario@servidor 'mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys'
   ```
3. **Configuración Adicional en el Servidor:** Asegúrate de que el archivo de configuración de SSH en el servidor (`/etc/ssh/sshd_config`) permite la autenticación mediante claves. Verifica que la siguiente línea esté presente y sin el símbolo `#` al principio:
   ```
   PubkeyAuthentication yes
   ```
   Guarda los cambios y reinicia el servicio SSH:
   ```shell
   sudo systemctl restart sshd
   ```
4. **Autenticarse Utilizando Claves:** Ahora, cuando te conectes al servidor, se te debería autenticar automáticamente utilizando la clave privada:
   ```shell
   ssh usuario@servidor
   ```

**Notas Adicionales:**
- Asegúrate de que las claves privadas (`id_ed25519`, `id_rsa`) estén protegidas: `~/.ssh` en 700 y la clave privada en 600 (si no, `ssh` se niega a usarla).
- Puedes configurar opciones adicionales en el archivo `~/.ssh/config` en el cliente para personalizar la conexión SSH.
- La autenticación basada en claves es más segura que la basada en contraseñas, pero debes proteger tus claves privadas y actualizarlas si es necesario.

## Establezca los modos de cumplimiento y permisivo para SELinux

| Elemento | Uso |
|---|---|
| **Policy** | Una colección de reglas que definen qué fuente tiene acceso a qué objetivo. |
| **Source domain** | El objeto que intenta acceder a un objetivo. Típicamente un usuario o un proceso. |
| **Target domain** | El objeto al que intenta acceder un dominio de origen. Típicamente un archivo o un puerto. |
| **Context** | Una etiqueta de seguridad que se usa para categorizar objetos en SELinux. |
| **Rule** | Una parte específica de la política que determina qué dominio de origen tiene qué permisos de acceso a qué dominio de destino. |
| **Labels** | Lo mismo que una etiqueta de contexto, definida para determinar qué dominio de origen tiene acceso a qué dominio de destino. |

SELinux (Security-Enhanced Linux) es un conjunto de extensiones de seguridad para el kernel Linux que proporciona funciones de control de acceso obligatorio (MAC). Establece el modo de cumplimiento y permisivo con `setenforce` y `semanage`.

1. **Verificar el Estado Actual de SELinux:**
   ```shell
   sestatus
   ```
   Esto mostrará el estado actual de SELinux y si está habilitado o deshabilitado.
2. **Cambiar el Modo de Cumplimiento:** El modo de cumplimiento de SELinux puede ser `enforcing` (imponer políticas de seguridad) o `permissive` (registrará violaciones de políticas pero no las impondrá).

   Para cambiar a modo `enforcing`:
   ```shell
   sudo setenforce 1
   ```
   Para cambiar a modo `permissive`:
   ```shell
   sudo setenforce 0
   ```
3. **Configurar el Modo Predeterminado en el Arranque:** Para configurar el modo predeterminado al arranque, edita el archivo `/etc/selinux/config`:
   ```shell
   sudo nano /etc/selinux/config
   ```
   Establece `SELINUX` en `enforcing` o `permissive` según tu preferencia:
   ```
   SELINUX=enforcing
   # o
   SELINUX=permissive
   ```
   Guarda los cambios y reinicia el sistema.
4. **Modificar Reglas de Política (Opcional):** Puedes usar `semanage` para modificar reglas de política SELinux. Por ejemplo, para permitir que Apache se conecte a un puerto no estándar:
   ```shell
   sudo semanage port -a -t http_port_t -p tcp 8080
   # si el puerto ya está definido (el 8080 lo está), -m en vez de -a:
   sudo semanage port -m -t http_port_t -p tcp 8080
   ```
5. **¿Hace falta reiniciar?** `setenforce` cambia entre `enforcing` y `permissive` al instante; lo de `/etc/selinux/config` se aplica en el siguiente arranque. Solo hace falta reiniciar (y un reetiquetado, `touch /.autorelabel`) al pasar de `disabled` a `enforcing`/`permissive`.

**Notas Adicionales:**
- En modo `enforcing`, SELinux aplicará las políticas de seguridad. En modo `permissive`, SELinux solo registrará violaciones de políticas en el log pero no las impondrá.
- SELinux es una herramienta poderosa pero puede requerir ajustes cuidadosos según los requisitos específicos de la aplicación y del sistema.
- Es importante entender las políticas de SELinux y sus efectos antes de cambiar el modo de cumplimiento.

### Entendiendo los Ajustes de Contexto y la Política

Los ajustes de contexto son una parte importante de las operaciones de SELinux. El contexto es una etiqueta que puede aplicarse a diferentes objetos:

```
Archivos y directorios
Puertos
Procesos
Usuarios
```

Las etiquetas de contexto definen la naturaleza del objeto, y las reglas de SELinux se crean para hacer coincidir las etiquetas de contexto de los objetos fuente con las etiquetas de contexto de los objetos objetivo. Por lo tanto, establecer las etiquetas de contexto correctas es una habilidad muy importante para los administradores de sistemas.

> **Nota:** Gestionar las etiquetas de contexto de SELinux es una habilidad clave y **sí está en los objetivos** del EX200 («List and identify SELinux file and process context», «Restore default file contexts»). Una etiqueta de contexto incorrecta es la causa más habitual de los problemas con SELinux.

Cada etiqueta de contexto siempre consta de tres partes diferentes:

> [Imagen no migrada: Diagrama ilustrando las tres partes de una etiqueta de contexto SELinux (usuario, rol, tipo)]

- **Usuario**: El usuario puede ser reconocido por `_u` en la etiqueta de contexto; generalmente está configurado como `system_u` en la mayoría de los directorios. Los usuarios de SELinux no son los mismos que los usuarios de Linux, y no son importantes en el examen de RHCSA.
- **Rol**: El rol puede ser reconocido por `_r` en la etiqueta de contexto. La mayoría de los objetos están etiquetados con el rol `object_r`. En la gestión avanzada de SELinux, usuarios específicos de SELinux pueden ser asignados permisos a roles de SELinux específicos. Para el examen de RHCSA, no es necesario que sepas cómo configurar roles.
- **Tipo**: El contexto de tipo puede ser reconocido por `_t` en la etiqueta de contexto. Se aplican una amplia variedad de tipos de contexto a los directorios en el sistema de archivos `/`. Asegúrate de saber cómo trabajar con los tipos de contexto, ya que son lo que se espera que sepas en el examen de RHCSA.

### Entendiendo y Trabajando con los Tipos de Contexto en SELinux

Un componente clave de SELinux son los tipos de contexto, que forman una parte esencial de las etiquetas de seguridad aplicadas a archivos, procesos y otros objetos en el sistema. Estas etiquetas de contexto son cruciales para definir las políticas de seguridad que SELinux utiliza para permitir o denegar el acceso a recursos.

**¿Qué es un Tipo de Contexto?**

Un tipo de contexto en SELinux es un identificador que se aplica a un objeto (archivo, directorio, proceso) y que define la política de acceso para ese objeto. En las etiquetas de contexto, el tipo se identifica generalmente con un sufijo `_t`. Por ejemplo, en un archivo con contexto `system_u:object_r:httpd_sys_content_t:s0`, el `httpd_sys_content_t` representa el tipo de contexto.

**Importancia de los Tipos de Contexto**

Los tipos de contexto son fundamentales para la seguridad de SELinux porque determinan las operaciones que se pueden realizar sobre los objetos. SELinux utiliza estos tipos para decidir si un proceso puede leer, escribir, ejecutar o gestionar un archivo o recurso. Cada tipo tiene asociadas políticas que definen las interacciones permitidas con otros tipos de contexto.

#### Guía para Configurar Tipos de Contexto en SELinux para el Examen RHCSA

Configura los tipos de contexto con las herramientas `semanage` y `restorecon`.

#### Uso del Comando `semanage`

`semanage` es la herramienta recomendada para manipular los tipos de contexto en SELinux porque sus cambios se aplican directamente en las políticas de SELinux, asegurando que sean persistentes a través de reinicios y relabelados del sistema de archivos.

1. **Instalación de `semanage`:** Si `semanage` no está instalado por defecto, puedes instalarlo usando el comando:
   ```shell
   yum whatprovides */semanage
   ```
   Luego instala el paquete adecuado que se muestra en los resultados.
2. **Encontrar el Contexto Adecuado:** Antes de modificar cualquier contexto, es importante verificar los contextos existentes para comprender cómo están configurados. Por ejemplo:
   ```shell
   ls -Z /var/www
   ```
   Esto muestra los tipos de contexto aplicados en los directorios bajo `/var/www`.
3. **Establecer un Nuevo Contexto:** Para aplicar un tipo de contexto a un directorio que será utilizado por un servidor web Apache, puedes usar el siguiente comando:
   ```shell
   semanage fcontext -a -t httpd_sys_content_t "/mydir(/.*)?"
   ```
   Aquí, `-a` agrega una nueva política, `-t` especifica el tipo de contexto y la expresión regular `"/mydir(/.*)?"` se refiere a todo el contenido dentro de `/mydir`.

   Comando de ejemplo:
   ```shell
   semanage fcontext -a -t httpd_sys_script_exec_t '/var/www/prueba_selinux(/.*)?'
   restorecon -Rv /var/www/prueba_selinux
   ```
   Este comando utiliza `semanage`, una herramienta de administración de políticas de SELinux, para agregar una regla de contexto de archivo. Aquí está lo que hace cada parte del comando:
   - `semanage`: Es el nombre de la herramienta de línea de comandos utilizada para administrar políticas de SELinux.
   - `fcontext`: Indica que queremos trabajar con las reglas de contexto de archivos.
   - `-a`: Es una opción que indica que queremos agregar una nueva regla de contexto.
   - `-t httpd_sys_script_exec_t`: Esta parte especifica el tipo de contexto que queremos asignar al archivo o directorio. En este caso, el tipo de contexto es `httpd_sys_script_exec_t`, que generalmente se usa para scripts CGI y otros archivos de script ejecutables para servidores web como Apache.
   - `'/var/www/prueba_selinux(/.*)?'`: La expresión regular de las rutas a las que se aplica: el directorio y todo lo que contiene. Tiene que ser una **ruta absoluta**: las reglas se comparan con la ruta completa del fichero, así que una ruta relativa (`prueba_selinux/`) no coincide nunca con nada y `restorecon` no cambia ningún contexto. Las comillas evitan que la shell interprete `(`, `*` y `?`.

#### Aplicar Cambios con `restorecon`

Después de configurar las políticas con `semanage`, debes aplicar estos cambios al sistema de archivos utilizando `restorecon`, que efectivamente aplica los contextos definidos por las políticas a los archivos y directorios en el sistema de archivos.

- **Aplicar el Contexto al Sistema de Archivos:** Aquí, `R` aplica el comando de manera recursiva a todos los subdirectorios y archivos, y `v` (verbose) muestra detalles sobre los cambios aplicados.
  ```shell
  restorecon -R -v /mydir
  ```

#### Consideraciones Importantes

- **No usar `chcon` para Cambios Permanentes:** Evita usar `chcon` para cambios de contexto permanentes, ya que estos cambios no son persistentes a través de relabelados del sistema de archivos y pueden perderse, lo cual es crítico especialmente en un entorno de examen como el RHCSA.
- **Manuales:** para más detalle, consulta `man semanage` y `man semanage-fcontext`.

### Restaure contextos de archivos predeterminados

En SELinux, cada objeto (archivo, directorio o proceso) tiene un contexto de seguridad asociado, una etiqueta que indica las políticas de seguridad que se le aplican. Herramientas y comandos para enumerar e identificar el contexto de archivos y procesos:

1. **Listar el Contexto de un Archivo o Directorio:**
   ```shell
   ls -Z nombre_archivo
   ```
   Esto mostrará el contexto de seguridad del archivo o directorio.
2. **Listar Todos los Contextos de un Directorio:**
   ```shell
   ls -Z
   ```
   Esto mostrará el contexto de seguridad de todos los archivos y directorios en el directorio actual.
3. **Cambiar el Contexto de un Archivo o Directorio:**
   ```shell
   chcon -t tipo_contexto nombre_archivo
   ```
   Esto cambiará el contexto de seguridad del archivo o directorio.
4. **Identificar el Contexto de un Proceso:**
   ```shell
   ps auxZ
   ```
   Esto mostrará el contexto de seguridad de los procesos en ejecución.
5. **Listar Tipos de Contexto Disponibles:**
   ```shell
   seinfo -t
   ```
   Esto mostrará una lista de los tipos de contexto disponibles.
6. **Listar Tipos de Contexto de Procesos:**
   ```shell
   sesearch -A -s httpd_t -c file       # qué puede hacer el dominio httpd_t con ficheros
   ```
   `sesearch` y `seinfo` vienen en el paquete `setools-console`.
7. **Obtener Información Detallada sobre un Contexto:**
   ```shell
   sesearch -A -s proceso -t tipo_contexto
   ```
   Esto proporcionará información detallada sobre el contexto de seguridad especificado.

### Gestione las etiquetas de puerto SELinux

> **Nota:** El comando `semanage` es parte del paquete `policycoreutils-python` (o `policycoreutils-python-utils` en algunas distribuciones más recientes). Si ves el mensaje `command not found`, significa que necesitas instalar este paquete.
>
> Aquí están los pasos para instalar el paquete correcto y usar el comando `semanage` para cambiar el contexto de seguridad de SELinux.
>
> **Instalación de `policycoreutils-python`**
>
> 1. **Verificar la versión de tu sistema operativo:** Si estás usando RHEL, CentOS o Fedora, puedes usar `yum` para instalar el paquete necesario.
> 2. **Instalar `policycoreutils-python-utils` (para RHEL 8 y superiores):**
>    ```shell
>    sudo yum install policycoreutils-python-utils
>    ```
>    Para RHEL 7 o CentOS 7:
>    ```shell
>    sudo yum install policycoreutils-python
>    ```
>
> **Uso del Comando `semanage`**
>
> 1. **Instalar el paquete necesario:**
>    ```shell
>    sudo yum install policycoreutils-python-utils
>    ```
> 2. **Verificar la instalación:**
>    ```shell
>    rpm -q policycoreutils-python-utils
>    semanage fcontext -l | head -3
>    ```
>    Si el paquete está instalado y `semanage` lista reglas, la instalación es correcta (`semanage` no tiene opción `--version`).
> 3. **Añadir el contexto de seguridad para el directorio `/web`:**
>    ```shell
>    sudo semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"
>    ```
> 4. **Aplicar los cambios de contexto:** Después de agregar el contexto, necesitas aplicar los cambios usando `restorecon`:
>    ```shell
>    sudo restorecon -Rv /web
>    ```
>    Aquí, `-R` significa recursivo y `-v` significa verbose (detallado), por lo que verás una lista de los archivos a los que se les ha aplicado el nuevo contexto.
>
> **Ejemplo Completo**
> ```shell
> # Instalar el paquete necesario
> sudo yum install policycoreutils-python-utils
>
> # Añadir el contexto de seguridad
> sudo semanage fcontext -a -t httpd_sys_content_t "/web(/.*)?"
>
> # Aplicar los cambios de contexto
> sudo restorecon -Rv /web
> ```
>

SELinux utiliza etiquetas de puerto para controlar las conexiones de red y aplicar políticas de seguridad. Aquí hay algunos comandos y pasos para gestionar las etiquetas de puerto SELinux:

1. **Verificar Etiquetas de Puerto Actuales:**
   ```shell
   semanage port -l
   ```
   Esto mostrará una lista de etiquetas de puerto SELinux y sus configuraciones actuales.
2. **Agregar una Etiqueta de Puerto:**
   ```shell
   sudo semanage port -a -t tipo_contexto -p protocolo puerto
   ```
   Esto agrega una etiqueta de puerto para un contexto y protocolo específicos.
3. **Eliminar una Etiqueta de Puerto:**
   ```shell
   sudo semanage port -d -t tipo_contexto -p protocolo puerto
   ```
   Esto elimina una etiqueta de puerto para un contexto y protocolo específicos.
4. **Modificar una Etiqueta de Puerto Existente:**
   ```shell
   sudo semanage port -m -t tipo_contexto -p protocolo puerto
   ```
   Esto modifica una etiqueta de puerto existente para un contexto y protocolo específicos.
5. **Restablecer Configuraciones de Etiquetas de Puerto a los Valores Predeterminados:**
   ```shell
   sudo semanage port -C     # listar solo las personalizaciones locales
   sudo semanage port -D     # borrarlas todas (vuelve a la política por defecto)
   ```
   (`-r` no es «restablecer»: es el rango MLS de la etiqueta.)
6. **Listar Todos los Tipos de Contexto de Etiquetas de Puerto:**
   ```shell
   sudo semanage port -l | awk '{print $1}' | sort -u
   ```
   Esto muestra todos los tipos de contexto de etiquetas de puerto utilizados.

**Notas Adicionales:**
- Los cambios en las etiquetas de puerto pueden afectar el acceso de red de las aplicaciones, así que considera cuidadosamente cualquier ajuste.

### Utilice una configuración booleana para modificar los ajustes de SELinux del sistema

Los booleanos de SELinux permiten habilitar o deshabilitar características específicas del sistema. Se gestionan con `getsebool`, `setsebool` y `semanage`.

1. **Verificar el Estado Actual de los Booleanos:**
   ```shell
   getsebool -a
   ```
   Esto mostrará una lista de booleanos y sus estados actuales (activado o desactivado).
2. **Buscar un Booleano Específico:**
   ```shell
   getsebool nombre_booleano
   ```
   Esto mostrará el estado actual de un booleano específico.
3. **Habilitar o Deshabilitar un Booleano:**
   ```shell
   sudo setsebool -P nombre_booleano 1   # Para habilitar
   sudo setsebool -P nombre_booleano 0   # Para deshabilitar
   ```
   El `-P` hace que los cambios sean persistentes (se mantengan después de reiniciar).
4. **Listar Booleanos Relacionados con una Función Específica:**
   ```shell
   semanage boolean -l
   ```
   Esto mostrará booleanos relacionados con una función específica.
5. **Modificar Ajustes de Booleanos a través de `semanage`:**
   ```shell
   sudo semanage boolean -m --on nombre_booleano
   sudo semanage boolean -m --off nombre_booleano
   ```
   Esto modificará el estado de un booleano y hará que el cambio sea persistente.

**Notas Adicionales:**
- Utiliza la opción `-P` con `setsebool` para hacer que los cambios sean persistentes; sin ella, solo se aplican en la sesión actual.

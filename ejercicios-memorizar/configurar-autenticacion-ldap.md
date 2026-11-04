# Configurar la Autenticación LDAP

Este ejercicio se centra en configurar la autenticación del sistema en un entorno Linux utilizando LDAP como base de datos de cuentas de usuario. Aquí tienes los pasos detallados junto con la explicación.

## Pasos para configurar la autenticación LDAP

### 1. Instalación de los componentes necesarios

Para configurar la autenticación LDAP, primero necesitas instalar los paquetes adecuados que facilitan la integración del sistema con LDAP.

```bash
yum groupinstall -y directory-client
```

Esto instalará los paquetes necesarios como `krb5-workstation`, `pam-krb5`, y `sssd` que son esenciales para configurar la autenticación LDAP.

### 2. Configuración utilizando `system-config-authentication`

`system-config-authentication` es una herramienta gráfica que simplifica la configuración de la autenticación LDAP en sistemas Linux.

```bash
system-config-authentication
```

A continuación, se detalla cómo configurar utilizando esta herramienta:

- **User Account Database**: Selecciona `LDAP` para indicar que las cuentas de usuario estarán almacenadas en LDAP.
- **LDAP Search Base DN**: Ingresa el DN base de búsqueda LDAP. Este especifica la ubicación dentro del árbol LDAP donde se buscarán las cuentas de usuario. Por ejemplo, `dc=example,dc=com`.
- **LDAP Server**: Ingresa el nombre de dominio completo (FQDN) del servidor LDAP. Por ejemplo, `ldap://instructor.example.com`. Es importante usar el nombre de dominio en lugar de la dirección IP.
- **Download CA Certificate**: Descarga el certificado CA necesario para establecer una conexión segura con el servidor LDAP. Esto asegura que la comunicación entre el cliente y el servidor LDAP sea cifrada y autenticada.
- **Authentication Method**: Selecciona `LDAP password` para indicar que las contraseñas de los usuarios se verificarán utilizando el servidor LDAP configurado.
- Aplica los cambios después de configurar todos los parámetros necesarios.

### 3. Verificación de la configuración

Una vez configurado, puedes verificar si un usuario LDAP puede autenticarse correctamente usando el siguiente comando:

```bash
getent passwd ldapuser40
```

Este comando debería devolver la información de la cuenta de usuario `ldapuser40` obtenida desde el servidor LDAP si la configuración de autenticación LDAP se ha realizado correctamente.

> **Nota:** El comando `system-config-authentication` no está disponible en todos los sistemas Linux modernos, especialmente en distribuciones recientes como Red Hat Enterprise Linux (RHEL) 8 y versiones más nuevas. Este comando solía ser parte de herramientas gráficas específicas para configurar la autenticación en entornos de escritorio y servidores.
>
> Sin embargo, en sistemas más recientes, la configuración de la autenticación y la integración con LDAP generalmente se realiza manualmente a través de la edición de archivos de configuración y la instalación de paquetes necesarios. Aquí te doy un ejemplo de cómo podrías configurar la autenticación LDAP manualmente en un sistema basado en RHEL 8 o similar.

## Configuración manual de autenticación LDAP en RHEL 8 y 9

> LDAP no figura en los objetivos actuales del EX200 (RHEL 9), pero aparece en exámenes de práctica antiguos.
> En RHEL 8/9 ya no existen `system-config-authentication`, `authconfig` ni el paquete `pam_ldap`: la
> autenticación la hace SSSD y los ficheros de PAM los gestiona `authselect` (no se editan a mano).

### 1. Instalación de paquetes necesarios

```bash
sudo dnf install -y sssd sssd-ldap oddjob-mkhomedir
```

### 2. Configuración de SSSD

```bash
sudo vim /etc/sssd/sssd.conf
```

```
[sssd]
domains = example.com
config_file_version = 2
services = nss, pam

[domain/example.com]
id_provider = ldap
auth_provider = ldap
ldap_uri = ldap://ldap.example.com
ldap_search_base = dc=example,dc=com
# Para validar contraseñas, SSSD exige un canal cifrado: StartTLS (o ldaps://) con el certificado de la CA
ldap_id_use_start_tls = True
ldap_tls_cacert = /etc/openldap/certs/ca.crt
ldap_tls_reqcert = demand
cache_credentials = True
```

Ajusta `ldap_uri`, `ldap_search_base` y la ruta del certificado a tu entorno. Sin TLS, `getent passwd`
funciona pero el inicio de sesión con contraseña falla.

**Permisos obligatorios** (si no, `sssd` se niega a arrancar):

```bash
sudo chown root:root /etc/sssd/sssd.conf
sudo chmod 600 /etc/sssd/sssd.conf
```

### 3. Certificado de la CA

Descarga el certificado de la CA del servidor LDAP a la ruta indicada en `ldap_tls_cacert`, por ejemplo:

```bash
sudo mkdir -p /etc/openldap/certs
sudo curl -o /etc/openldap/certs/ca.crt http://servidor/ruta/ca.crt
```

### 4. PAM y NSS con `authselect`

En RHEL 8/9 `/etc/pam.d/system-auth` es un enlace a un perfil de `authselect`: lo que se edite a mano se
pierde en la siguiente actualización o al cambiar de perfil. Se selecciona el perfil de SSSD con creación
automática del directorio personal:

```bash
sudo authselect select sssd with-mkhomedir --force
sudo systemctl enable --now oddjobd     # crea el home en el primer inicio de sesión
```

### 5. Arrancar SSSD y verificar

```bash
sudo systemctl enable --now sssd
getent passwd ldapuser40        # el usuario se resuelve desde LDAP
su - ldapuser40                 # el inicio de sesión funciona (y se crea su home)
```

Si algo falla: `journalctl -u sssd` y `sssctl config-check`.

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

## Configuración manual de autenticación LDAP en RHEL 8

### 1. Instalación de paquetes necesarios

Asegúrate de tener instalados los paquetes necesarios para la autenticación LDAP:

```bash
sudo yum install -y sssd sssd-client krb5-workstation pam_ldap
```

### 2. Configuración de SSSD (System Security Services Daemon)

Editar el archivo de configuración de SSSD para configurar la autenticación LDAP:

```bash
sudo vim /etc/sssd/sssd.conf
```

Aquí tienes un ejemplo básico de configuración `sssd.conf` para autenticación LDAP:

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
ldap_tls_reqcert = allow
```

Asegúrate de ajustar los valores `ldap_uri`, `ldap_search_base` y otros parámetros según tu configuración LDAP específica.

### 3. Configuración de PAM (Pluggable Authentication Modules)

Editar el archivo de configuración de PAM para incluir soporte para autenticación LDAP:

```bash
sudo vim /etc/pam.d/system-auth
```

Agrega o modifica las líneas necesarias para usar LDAP para la autenticación:

```
auth        required      pam_env.so
auth        sufficient    pam_unix.so nullok try_first_pass
auth        requisite     pam_succeed_if.so uid >= 1000 quiet_success
auth        sufficient    pam_sss.so use_first_pass
auth        required      pam_deny.so

account     required      pam_unix.so
account     sufficient    pam_localuser.so
account     sufficient    pam_succeed_if.so uid < 1000 quiet
account     [default=bad success=ok user_unknown=ignore] pam_sss.so
account     required      pam_permit.so

password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=
password    sufficient    pam_unix.so sha512 shadow nullok try_first_pass use_authtok
password    sufficient    pam_sss.so use_authtok
password    required      pam_deny.so

session     optional      pam_keyinit.so revoke
session     required      pam_limits.so
session     optional      pam_mkhomedir.so
session     [success=1 default=ignore] pam_succeed_if.so service in crond quiet use_uid
session     required      pam_unix.so
```

### 4. Descargar y configurar el certificado CA

Descarga el certificado CA necesario desde el servidor LDAP y colócalo en un directorio seguro, como `/etc/pki/tls/certs/`.

### 5. Reiniciar servicios y verificación

Reinicia los servicios relevantes para aplicar los cambios:

```bash
sudo systemctl restart sssd
```

Verifica que la autenticación LDAP esté funcionando correctamente:

```bash
getent passwd ldapuser40
```

Este comando debería mostrar la entrada de usuario para `ldapuser40` recuperada desde el servidor LDAP.


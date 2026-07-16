# Configuración de autofs para Directorio Home Automontado

Para configurar `autofs` de manera que los usuarios LDAP tengan un directorio automontado al iniciar sesión, sigue estos pasos detallados.

## Configuración de autofs para directorio home automontado

### 1. Instalar autofs y asegurarse de que esté habilitado

```bash
sudo yum install -y autofs
sudo systemctl enable autofs
```

### 2. Configurar el archivo `/etc/auto.master`

Este archivo define los puntos de montaje y los mapas de autofs que se usarán.

```bash
sudo vim /etc/auto.master
```

Agrega la siguiente línea al final del archivo:

```
/rhome /etc/auto.ldap --timeout=60
```

Esto indica que el directorio `/rhome` será controlado por el mapa de autofs `auto.ldap`.

### 3. Crear el archivo de mapa `auto.ldap`

```bash
sudo cp /etc/auto.misc /etc/auto.ldap
sudo vim /etc/auto.ldap
```

Dentro de `auto.ldap`, define cómo se montarán los directorios para los usuarios LDAP. Usaremos los parámetros `-rw`, `soft`, e `intr` para permitir montajes lectura-escritura con manejo suave y permitir la interrupción del montaje.

```
ldapuser40 -rw,soft,intr 172.24.40.10:/rhome/ldapuser40
* -rw,soft,intr 172.24.40.10:/rhome/&
```

- `ldapuser40`: Esta línea indica que el usuario `ldapuser40` tendrá su directorio home montado desde `172.24.40.10:/rhome/ldapuser40`.
- `*`: Esta línea genérica indica que cualquier otro usuario LDAP tendrá su directorio home montado desde `172.24.40.10:/rhome/<usuario>`.

### 4. Reiniciar el servicio autofs

```bash
sudo systemctl restart autofs
```

### 5. Verificar los montajes

Puedes verificar que los montajes se hayan configurado correctamente usando `showmount` en el servidor remoto (`172.24.40.10` en este caso).

```bash
showmount -e 172.24.40.10
```

Esto debería mostrar los recursos exportados desde el servidor NFS `172.24.40.10`.

### 6. Probar el acceso al directorio home automontado

Intenta iniciar sesión como `ldapuser40` y verifica si su directorio home se monta automáticamente:

```bash
su - ldapuser40
```

Esto debería hacer que autofs monte automáticamente `/rhome/ldapuser40` desde `172.24.40.10` al acceder al directorio home de `ldapuser40`.

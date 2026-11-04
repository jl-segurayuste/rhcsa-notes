# Examen para practicar

> [Adjunto no migrado: `Rhel8-Questions-Answers.pdf` — documento PDF adjunto a la página original de Notion, alojado en un enlace S3 firmado no descargable directamente. Revisar manualmente en Notion si se necesita su contenido.]

Esta página contenía originalmente un índice/tabla de contenidos (generado a partir de un documento Word) y el desarrollo de un examen de práctica completo ("Exam Real"), dividido en dos secciones.

> **Nota:** la página original enlazaba además a copias de varios ejercicios que ya están migrados con su propio fichero en [`ejercicios-memorizar/`](../ejercicios-memorizar/): *Cambiar la contraseña de root sin saber cual es*, *Logs remotos*, *Añadir servidor DNS*, *Instalar un paquete desde FTP*, *Cambiar hostname y la IP*, *Crear usuarios con grupos específicos*, *Crear partición Swap*, *Crear usuario con UID y contraseña*, *Configurar servidor FTP*, *Instalar HTTP server*, *Configurar la Autenticación LDAP*, *Configuración de autofs para Directorio Home Automontado*, *Sincronización de servidor NTP*, *Cambiar el Tamaño de un Volumen Lógico*, *Crear repositorio con la ISO automontada*, *Configurar valores predeterminados para nuevos usuarios*, *Ejercicio de LVM con PE*, *Búsqueda y ejecución con find*, *Tarea programada crontab*, *Crear VDO* y *Crear usuario con permisos para cambiar contraseña*. Consulta esos ficheros para el detalle paso a paso; aquí se conserva únicamente el enunciado del examen tal cual aparecía en la página original.

## Exam Real

### Secction I

#### 1] IP-Configuration

Create a New connection with a static network connection using the settings given below. Be sure to replace the X with the correct number of your system.

| Parameter | Setting |
| --- | --- |
| IP ADDRESS | 172.25.250.X |
| NETMASK | 255.255.255.0 |
| GATEWAY | 172.25.250.254 |
| NAME SERVER | 172.25.254.254 |

#### 2] Hostnamectl

Set a hostname as `device3.servera.lab.example.com`

#### 3] Yum configuration

Configure your `servera.example.com` as yum client so that you can download and install packages from your yum repository at:

```
http://content.example.com/rhel8.2/x86_64/dvd/BaseOS
http://content.example.com/rhel7.0/x86_64/dvd/AppStream
```

> El enunciado se conserva tal como estaba, pero RHEL 7 no tiene AppStream: la ruta buena es `rhel8.2/x86_64/dvd/AppStream`.
> Ver [`configuracion-repositorios-url.md`](../teoria-memorizar/configuracion-repositorios-url.md) (incluido el aviso de GPG).

#### 4] User Management

Add users `natasha`, `harry`, `sarah`, set password as `password`. Create a group `admin`. `natasha` and `harry` are secondary members of the `admin` group. `sarah` should not have an interactive shell prompt. Make a directory `/mnt/vm`; the `admin` group must belong to the directory. All users in the group have read and write access to each other's files.

#### 5] File Permission

Create one file and give it permissions such that root has read, group has full permission, and other users have write permission.

#### 6] ACL

Copy `/etc/fstab` to `/var/tmp/fstab`.

- Assign `natasha` read/write ACL permission on `/var/tmp/fstab`.
- Assign `harry` read-only permission on `/var/tmp/fstab`.
- `sarah` has no permission, and other users have read-only permission.

#### 7] Crontab

Set a cronjob for user `natasha` that runs every 5 minutes.

#### 8] Autofs

- Direct
- Indirect

#### 9] Tar

Create a tar file `/tmp/root.tar.gz` that compresses `/root`.

#### 11] grep

- Copy lines matching `strato` from `/usr/share/dict/words` to the directory `/tmp/data`.
- Copy lines matching `root` from `/etc/passwd` to the directory `/tmp/data1`.

#### 12] Httpd-Port-Labeling

*(Ver detalle en [`teoria-memorizar/httpd-port-labeling.md`](../teoria-memorizar/httpd-port-labeling.md).)*

### Secction II

#### 1] Root Password

Reset `serverb` password as `Account5.` and all users should have a password `Redhat`.

#### 2] Yum Configure

Configure your `servera.example.com` as yum client so that you can download and install packages from your yum repository at:

```
http://content.example.com/rhel8.2/x86_64/dvd/BaseOS
http://content.example.com/rhel7.0/x86_64/dvd/AppStream
```

#### 3] NTP

Configure an NTP server as `classroom.example.com`.

#### 4] LVM create

Create logical volume `lv1` with volume group `group`. The logical volume `lv1` should be of size 60 extents. Create volume group `vgroup`, PE size should be 4 MB. Filesystem type is `ext4`. This logical volume must be mounted at `/mnt/redhat`.

#### 5] Resize The Partition

Resize the LVM size to 200M; the mount point is `/sbi`, and the LV size must end up between 180M and 220M.

> **Nota:** primero comprueba el tamaño actual del LV. Si es mayor de 200M, el LV debe reducirse a un valor entre 180-220M; si es menor de 200M, el LV debe ampliarse de forma que (tamaño dado del LV + valor necesario para ampliar) quede en ese rango. Ejemplo: si el LV dado es de 100M y se debe ampliar en 200M, resultado 100+100=200M.

#### 6] Swap Partition

Create a swap partition of size 985M on `vdb`; remember that the old partition may not be deleted.

#### 7] Tuned

Set a tuned profile as recommended.

#### 8] VDO

Create a logical VDO named `vdo1` on `/dev/vdd` of size 51GB.

> **Nota:** VDO siempre se crea sobre un disco sin particionar.

#### 9] Container review

*(Sin desarrollar en la página original.)*

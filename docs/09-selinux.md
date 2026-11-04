# Diagnostique y aborde los incumplimientos rutinarios de las políticas de SELinux

> En el EX200 de RHEL 10 este objetivo ya no aparece como tal, pero es la forma de resolver cualquier tarea
> que SELinux bloquee (un servicio que no arranca en un puerto, una web que da 403…). Al terminar el examen
> SELinux tiene que estar en **enforcing**.

## 1. Confirmar que es SELinux

```shell
getenforce                                   # Enforcing
sudo ausearch -m AVC -ts recent              # denegaciones de los últimos minutos
sudo grep AVC /var/log/audit/audit.log | tail
```

Si no hay AVC, el problema no es SELinux (permisos, cortafuegos, configuración del servicio). Una prueba
rápida: `setenforce 0` (pasa a **permissive**, no lo desactiva), repetir la acción y volver con
`setenforce 1`. Si en permissive funciona, es SELinux.

## 2. Leer la explicación

```shell
sudo dnf install -y setroubleshoot-server     # trae sealert
sudo sealert -a /var/log/audit/audit.log      # explica cada denegación y propone la solución
```

`sealert` suele decir directamente cuál de los casos siguientes es y qué orden ejecutar.

## 3. Arreglarlo, en este orden

1. **Contexto de fichero incorrecto** (lo más habitual: ficheros movidos con `mv`, directorios nuevos
   fuera de las rutas estándar):

   ```shell
   ls -Zd /web                                                # tipo actual
   sudo semanage fcontext -a -t httpd_sys_content_t '/web(/.*)?'
   sudo restorecon -Rv /web
   ```

   Si la ruta es una de las estándar (`/var/www/html`…) basta con `restorecon -Rv`.

2. **Puerto no estándar:**

   ```shell
   sudo semanage port -l | grep http_port_t
   sudo semanage port -a -t http_port_t -p tcp 82      # -m si el puerto ya está definido
   ```

3. **Booleano** (comportamientos que la política permite activar):

   ```shell
   getsebool -a | grep httpd
   sudo setsebool -P httpd_enable_homedirs on          # -P: persistente
   ```

4. **Último recurso: un módulo de política propio**, solo con las denegaciones de ese servicio:

   ```shell
   sudo ausearch -m AVC -c httpd --raw | audit2allow -M mi_httpd
   cat mi_httpd.te                                     # revisar qué se va a permitir
   sudo semodule -i mi_httpd.pp
   ```

   Pasar todo `audit.log` a `audit2allow` crearía un módulo que permite cualquier cosa que se haya
   denegado alguna vez, también lo que estaba bien bloqueado.

## 4. No dejarlo desactivado

- `setenforce 0` solo sirve para diagnosticar: vuelve a `setenforce 1`.
- `SELINUX=disabled` en `/etc/selinux/config` apaga SELinux en el siguiente arranque y, al volver a
  activarlo, hay que reetiquetar todo (`touch /.autorelabel` y reiniciar). En el examen, nunca.
- `seinfo` y `sesearch` (paquete `setools-console`) sirven para consultar la política.

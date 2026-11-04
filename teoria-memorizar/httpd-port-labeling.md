# Httpd Port Labeling

**Httpd-Port-Labeling** es un ejercicio relacionado con la administración de servidores web en sistemas Linux, particularmente con el etiquetado de puertos en SELinux para el servicio HTTP (Apache). A continuación se explica lo que implica este ejercicio y cómo se realiza.

## Enunciado posible

> 12. Httpd-Port-Labeling
>
> Se requiere configurar el servidor Apache (httpd) para que pueda escuchar en puertos no estándar en un sistema con SELinux habilitado. Asegúrese de que SELinux permita la conexión de Apache en estos puertos. Realice el etiquetado de puertos necesario para permitir la conexión de httpd a un puerto personalizado. El puerto debe estar configurado en la variable `PORT_NUMBER`, y se debe permitir que el servicio httpd acepte conexiones en este puerto.

## Explicación de lo que se está solicitando

Este ejercicio está relacionado con **SELinux** (Security-Enhanced Linux), que es una capa de seguridad adicional en Linux. En particular, se enfoca en la configuración de **Apache HTTPD** para que pueda escuchar en un puerto no estándar.

Por defecto, Apache escucha en el puerto 80 para HTTP y en el puerto 443 para HTTPS. Sin embargo, en algunas configuraciones puede ser necesario cambiar estos puertos para evitar conflictos o mejorar la seguridad.

Cuando se cambian los puertos de un servicio en un sistema con SELinux habilitado, SELinux puede bloquear las conexiones si no se configura correctamente. Esto se debe a que SELinux restringe los servicios de red a puertos específicos por razones de seguridad.

El ejercicio pide realizar lo siguiente:

1. **Cambiar el puerto en el que Apache escucha.**
2. **Configurar SELinux para permitir que Apache escuche en este nuevo puerto** mediante el etiquetado de puertos.

## Cómo realizarlo

1. **Definir el puerto en Apache**

   Primero, hay que configurar Apache para escuchar en un puerto diferente. Por ejemplo, si se quiere que Apache escuche en el puerto 8080, hay que modificar el archivo de configuración de Apache.

   - Edita el archivo de configuración de Apache (`/etc/httpd/conf/httpd.conf` o un archivo similar, dependiendo de la distribución) y agrega o modifica la línea `Listen`:

     ```bash
     Listen 8080
     ```

2. **Configurar SELinux para permitir Apache en el puerto nuevo**

   Después de configurar Apache, hay que indicarle a SELinux que permita el tráfico HTTP en este puerto. Esto se hace mediante el etiquetado de puertos. Utilizando la herramienta `semanage`, se puede asociar el puerto 8080 con el contexto de seguridad `http_port_t`, que es el contexto utilizado por Apache.

   - Ejecuta el siguiente comando para permitir que SELinux permita que Apache escuche en el puerto 8080:

     ```bash
     sudo semanage port -a -t http_port_t -p tcp 8080
     # Si responde que el puerto ya está definido (el 8080 lo está en la política de RHEL):
     sudo semanage port -m -t http_port_t -p tcp 8080
     ```

   Este comando le dice a SELinux que asocie el puerto TCP 8080 con el tipo `http_port_t`, lo que permitirá a Apache aceptar conexiones en este puerto.

3. **Verificar que SELinux ha etiquetado correctamente el puerto**

   Después de etiquetar el puerto, se puede verificar que el puerto 8080 esté correctamente etiquetado con el contexto `http_port_t` usando:

   ```bash
   sudo semanage port -l | grep http_port_t
   ```

   Esto debería mostrar algo similar a:

   ```text
   http_port_t      tcp      8080, 80, 81, 443, …
   ```

   (el 8080 aparece en la lista de `http_port_t`; el resto depende de la versión de la política)

4. **Reiniciar Apache**

   Finalmente, reinicia el servicio de Apache para que los cambios surtan efecto:

   ```bash
   sudo systemctl restart httpd
   sudo firewall-cmd --permanent --add-port=8080/tcp && sudo firewall-cmd --reload
   ```

   Si no arranca, `journalctl -u httpd` y `ausearch -m AVC -ts recent` dicen si es SELinux.

## Objetivo del ejercicio

Enseñar a trabajar con SELinux para modificar configuraciones de seguridad de servicios y puertos.

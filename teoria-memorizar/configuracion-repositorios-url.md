# Configuración de repositorios con URL

Configura un cliente YUM/DNF con el comando `dnf config-manager` para añadir repositorios.

## Configuración paso a paso

1. **Añadir el repositorio BaseOS de RHEL 8.2**

   Usa el comando `dnf config-manager` para añadir el repositorio:

   ```bash
   sudo dnf config-manager --add-repo=http://content.example.com/rhel8.2/x86_64/dvd/BaseOS
   ```

   Esto creará automáticamente un archivo de configuración para el repositorio en `/etc/yum.repos.d/`.

2. **Añadir el repositorio AppStream de RHEL 8.2**

   Similar al paso anterior, añade el repositorio AppStream:

   ```bash
   sudo dnf config-manager --add-repo=http://content.example.com/rhel8.2/x86_64/dvd/AppStream
   ```

3. **Verificar los repositorios configurados**

   Asegúrate de que los repositorios se hayan añadido correctamente con el siguiente comando:

   ```bash
   dnf repolist
   ```

   La salida debería incluir algo como:

   ```text
   repo id                                              repo name
   content.example.com_rhel8.2_x86_64_dvd_BaseOS        content.example.com_rhel8.2_x86_64_dvd_BaseOS
   content.example.com_rhel8.2_x86_64_dvd_AppStream     content.example.com_rhel8.2_x86_64_dvd_AppStream
   ```

4. **Probar los repositorios**

   Intenta buscar un paquete para confirmar que el servidor YUM/DNF está funcionando correctamente:

   ```bash
   sudo dnf search httpd
   ```

5. **(Opcional) Modificar prioridades o configuración de los repositorios**

   Si necesitas modificar los archivos de configuración de los repositorios (por ejemplo, establecer prioridades), edita los archivos en `/etc/yum.repos.d/`. Los archivos tendrán nombres como:

   ```text
   /etc/yum.repos.d/content.example.com_rhel8.2_x86_64_dvd_BaseOS.repo
   /etc/yum.repos.d/content.example.com_rhel8.2_x86_64_dvd_AppStream.repo
   ```

   Por ejemplo, puedes establecer la prioridad añadiendo:

   ```text
   priority=1
   ```

6. **Actualizar el caché de metadatos**

   Una vez configurados los repositorios, actualiza el caché de DNF/YUM:

   ```bash
   sudo dnf makecache
   ```

> **Ojo con GPG:** `dnf config-manager --add-repo` crea el `.repo` sin clave. Al instalar, dnf comprueba la
> firma y falla («GPG check FAILED» / «Public key … is not installed») si no se importa la clave:
> `rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release`. En el examen, si el enunciado lo permite, se
> añade `gpgcheck=0` a cada sección del `.repo`.
>
> (Antes estas notas mezclaban `rhel8.2/BaseOS` con `rhel7.0/AppStream`: RHEL 7 no tiene AppStream.)


# Configurar valores predeterminados para nuevos usuarios

Para configurar valores predeterminados para nuevos usuarios en un sistema Linux, puedes modificar ciertos archivos de configuración que controlan estas propiedades. Aquí están los pasos detallados para establecer la validez predeterminada de la contraseña a 90 días y establecer el primer UID que se utilizará para nuevos usuarios a 2000.

## Establecer la validez de la contraseña a 90 días

La validez de la contraseña para nuevos usuarios se puede configurar en el archivo `/etc/login.defs`.

1. **Abrir el archivo `login.defs` para editar**:

   ```bash
   sudo nano /etc/login.defs
   ```

2. **Encontrar y modificar la línea que establece `PASS_MAX_DAYS`**: busca la línea que contiene `PASS_MAX_DAYS`. Si no existe, añádela. Establece el valor a 90 días:

   ```plain text
   PASS_MAX_DAYS   90
   UID_MIN         2000
   ```

   Esta configuración asegura que la contraseña de cualquier nuevo usuario caducará después de 90 días.

3. **Guardar y cerrar el archivo**: guarda los cambios y cierra el editor.

## Verificación de la configuración

Para verificar que las configuraciones se han aplicado correctamente, puedes crear un nuevo usuario y revisar sus propiedades.

1. **Crear un nuevo usuario**:

   ```bash
   sudo useradd testuser
   ```

2. **Verificar el UID del nuevo usuario**: utiliza el siguiente comando para verificar que el UID del nuevo usuario es 2000:

   ```bash
   id testuser
   ```

   Deberías ver algo como:

   ```plain text
   uid=2000(testuser) gid=2000(testuser) groups=2000(testuser)
   ```

3. **Verificar la validez de la contraseña**: utiliza el siguiente comando para verificar la validez de la contraseña:

   ```bash
   sudo chage -l testuser
   ```

   Deberías ver algo como:

   ```plain text
   Last password change                                    : never
   Password expires                                        : never
   Password inactive                                       : never
   Account expires                                         : never
   Minimum number of days between password change          : 0
   Maximum number of days between password change          : 90
   Number of days of warning before password expires       : 7
   ```

Estos pasos configuran correctamente los valores predeterminados para la validez de la contraseña y el primer UID para nuevos usuarios en un sistema Linux.

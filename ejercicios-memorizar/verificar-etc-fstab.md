# Verificar el contenido del archivo /etc/fstab

Para verificar el contenido del archivo `/etc/fstab` antes de iniciar el sistema (bootear) y asegurarte de que no haya errores de sintaxis o configuración, puedes usar el comando `mount` con la opción `--verify` o `findmnt --verify`.

### Opciones disponibles

1. **Comando `mount --verify`:**

   ```bash
   mount --verify
   ```

   Este comando comprueba el archivo `/etc/fstab` para detectar errores, advirtiéndote si hay problemas antes de que afecten al proceso de arranque. Si el archivo tiene errores de sintaxis o configuraciones inválidas, te lo indicará.

2. **Comando `findmnt --verify`:**

   ```bash
   findmnt --verify
   ```

   Similar a `mount --verify`, este comando valida las entradas del archivo `/etc/fstab` y es útil para verificar que no haya configuraciones inválidas antes de intentar montar los sistemas de archivos.

Ambos comandos son herramientas útiles para asegurarte de que no habrá problemas de montaje al iniciar el sistema.

# Verificar el contenido del archivo /etc/fstab

Para verificar el contenido del archivo `/etc/fstab` antes de iniciar el sistema (bootear) y asegurarte de que no haya errores de sintaxis o configuración, puedes usar `findmnt --verify` o un montaje "en falso" con `mount -fav`.

> **Nota:** `mount` no tiene ninguna opción `--verify` (no existe tal flag en el comando `mount`). La herramienta pensada para esto es `findmnt --verify` (equivalente a `findmnt -x`).

### Opciones disponibles

1. **Comando `findmnt --verify`:**

   ```bash
   findmnt --verify
   ```

   Este comando valida las entradas del archivo `/etc/fstab`, comprobando su sintaxis y usabilidad, y te avisa si hay problemas antes de que afecten al proceso de arranque.

2. **Montaje en falso con `mount -fav`:**

   ```bash
   mount -fav
   ```

   `-f` (fake) hace que `mount` recorra todo el proceso de montaje sin llegar a ejecutar la llamada real de montaje, `-a` monta todas las entradas de `/etc/fstab` y `-v` muestra la salida detallada. Es útil para detectar errores de `/etc/fstab` sin llegar a montar nada de verdad.

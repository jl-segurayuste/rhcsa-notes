# Cambiar el Tamaño de un Volumen Lógico

## Explicación del Ejercicio

El objetivo es cambiar la capacidad de un volumen lógico (Logical Volume, LV) de 190 MB a 300 MB. El LV ya está montado, lo que implica que cualquier cambio de tamaño debe hacerse con el sistema de archivos en uso. Además, la capacidad del volumen debe fluctuar dentro de un rango de 280 MB a 320 MB.

## Pasos para Cambiar el Tamaño de un Volumen Lógico

1. **Verificar la Capacidad del Grupo de Volúmenes (VG)**: antes de extender el LV, es necesario asegurarse de que el VG tenga suficiente espacio libre. Si no es así, se necesitará añadir más espacio al VG.

   ```bash
   vgdisplay
   ```

2. **Verificar el Volumen Lógico (LV)**: verifica el LV para obtener su nombre exacto y confirmar el tamaño actual.

   ```bash
   lvdisplay /dev/vg_name/lv_name
   ```

3. **Extender el Volumen Lógico**: utiliza el comando `lvextend` para aumentar el tamaño del LV. En este caso, aumentamos en 110 MB (de 190 MB a 300 MB).

   ```bash
   lvextend -L 300M /dev/vg_name/lv_name
   ```

4. **Redimensionar el Sistema de Archivos**: después de extender el LV, es necesario redimensionar el sistema de archivos para utilizar el nuevo espacio disponible.

   ```bash
   resize2fs /dev/vg_name/lv_name
   ```

5. **Verificar la Montura Automática**: asegúrate de que el LV está correctamente montado.

   ```bash
   mount -a
   ```

6. **Verificar los Cambios**: confirma que el LV tiene el tamaño correcto y que el sistema de archivos ha sido redimensionado.

   ```bash
   lvdisplay /dev/vg_name/lv_name
   ```

## Ejemplo Completo

Supongamos que el VG se llama `vg2` y el LV se llama `lv2`.

1. **Verificar el VG y el LV**:

   ```bash
   vgdisplay vg2
   lvdisplay /dev/vg2/lv2
   ```

2. **Extender el LV a 300 MB**:

   ```bash
   lvextend -L 300M /dev/vg2/lv2
   ```

3. **Redimensionar el Sistema de Archivos**:

   ```bash
   resize2fs /dev/vg2/lv2
   ```

4. **Montar el LV**:

   ```bash
   mount -a
   ```

5. **Verificar los Cambios**:

   ```bash
   lvdisplay /dev/vg2/lv2
   ```

## Pasos Alternativos para Reducir el Tamaño del LV

> **Atención:** si necesitas reducir el tamaño de un LV, asegúrate de hacer una copia de seguridad de los datos antes de reducir el tamaño.

1. **Desmontar el Sistema de Archivos**:

   ```bash
   umount /dev/vg2/lv2
   ```

2. **Comprobar el Sistema de Archivos**:

   ```bash
   e2fsck -f /dev/vg2/lv2
   ```

3. **Redimensionar el Sistema de Archivos**:

   ```bash
   resize2fs /dev/vg2/lv2 100M
   ```

4. **Reducir el LV**:

   ```bash
   lvreduce -L 100M /dev/vg2/lv2
   ```

5. **Montar el LV**:

   ```bash
   mount -a
   ```

6. **Verificar los Cambios**:

   ```bash
   lvdisplay /dev/vg2/lv2
   ```

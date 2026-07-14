# Comandos VIM

| Comando | Explicación |
| --- | --- |
| `:q!` | Sale del archivo sin guardar cambios. El `!` fuerza la salida sin confirmación. Úsalo solo si estás seguro de lo que haces. |
| `:w filename` | Guarda el archivo actual con un nuevo nombre de archivo. |
| `dd` | Elimina la línea actual. |
| `yy` | Copia la línea actual. |
| `p` | Pega el contenido que ha sido cortado o copiado. |
| `v` | Entra en modo visual, lo que te permite seleccionar un bloque de texto con las teclas de flecha. Usa `d` para cortar la selección o `y` para copiarla. |
| `u` | Deshace el último comando. Repite tantas veces como sea necesario. |
| `Ctrl-r` | Rehace el último deshacer. (No se puede repetir más de una vez). |
| `gg` | Va a la primera línea del documento. |
| `G` | Va a la última línea del documento. |
| `/texto` | Busca el texto desde la posición actual del cursor hacia adelante. |
| `?texto` | Busca el texto desde la posición actual del cursor hacia atrás. |
| `^` | Va a la primera posición de la línea actual. |
| `$` | Va a la última posición de la línea actual. |
| `!ls` | Inserta la salida del comando `ls` (u otro comando) en el archivo actual. |
| `:%s/old/new/g` | Reemplaza todas las ocurrencias de `old` por `new` en el archivo. |

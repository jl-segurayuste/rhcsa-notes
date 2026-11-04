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
| `Ctrl-r` | Rehace lo deshecho; se puede repetir tantas veces como `u`. |
| `gg` | Va a la primera línea del documento. |
| `G` | Va a la última línea del documento. |
| `/texto` | Busca el texto desde la posición actual del cursor hacia adelante. |
| `?texto` | Busca el texto desde la posición actual del cursor hacia atrás. |
| `^` | Va al primer carácter no blanco de la línea (`0` va a la columna 0). |
| `$` | Va a la última posición de la línea actual. |
| `:r !ls` | Inserta la salida del comando `ls` (u otro) debajo del cursor. (`:!ls` solo la muestra.) |
| `:%s/old/new/g` | Reemplaza todas las ocurrencias de `old` por `new` en el archivo. |

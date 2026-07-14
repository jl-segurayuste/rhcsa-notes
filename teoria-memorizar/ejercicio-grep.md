# Ejercicio con grep

Para realizar la operación descrita, se puede usar el comando `grep` con las opciones adecuadas. Este es el comando completo:

```bash
grep -i '^essential' /usr/share/dict/linux.words | grep -v '^$' > /tmp/pattern.txt
```

## Explicación

1. **`grep -i '^essential' /usr/share/dict/linux.words`**
   - `grep`: busca patrones en un fichero.
   - `-i`: hace la búsqueda insensible a mayúsculas/minúsculas.
   - `^essential`: encuentra líneas que empiezan (`^`) por la palabra "essential".
2. **`| grep -v '^$'`**
   - `|`: canaliza (pipe) la salida del primer comando al segundo.
   - `grep -v '^$'`: filtra las líneas vacías. La opción `-v` invierte la coincidencia, es decir, excluye las líneas que coinciden con el patrón `^$` (líneas vacías).
3. **`> /tmp/pattern.txt`**
   - Redirige la salida final al fichero `/tmp/pattern.txt`.

## Tras ejecutar el comando

Puedes ver los resultados con:

```bash
cat /tmp/pattern.txt
```

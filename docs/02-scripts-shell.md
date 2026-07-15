# Creación de scripts de shell sencillos

## ¿Qué es un script de shell?

Escritura secuencial de comandos en un archivo de texto para su ejecución por el sistema operativo.

El **shebang** es la **primera línea** de un script de Bash (u otro lenguaje de scripting) que le indica al sistema **con qué intérprete ejecutar el script**.

## El shebang (`#!`)

### Ejemplo

```bash
#!/bin/bash
```

Este shebang le dice al sistema: *"Usa el intérprete `/bin/bash` para ejecutar este script."*

### ¿Por qué se llama *shebang*?

Viene de:

- `#` → "sharp"
- `!` → "bang"

Juntos: **shebang**.

### ¿Para qué sirve?

Sin el shebang, si ejecutas el script como:

```shell
./miprog.sh
```

el sistema **no sabrá con qué intérprete** ejecutarlo, a menos que lo lances manualmente así:

```shell
bash miprog.sh
```

### Otros ejemplos útiles

- **Bash:**
  ```shell
  #!/bin/bash
  ```
- **Sh (POSIX shell):**
  ```shell
  #!/bin/sh
  ```
- **Python:**
  ```python
  #!/usr/bin/env python3
  ```
- **Perl:**
  ```perl
  #!/usr/bin/perl
  ```

> **Nota:** `#!/usr/bin/env` es una forma más flexible que busca el intérprete en el `PATH`.

### Buenas prácticas

- Siempre incluye el shebang en scripts ejecutables.
- Asegúrate de que el intérprete especificado exista en esa ruta.
- Dale permisos de ejecución al script:
  ```shell
  chmod +x script.sh
  ```

## Elementos del lenguaje Bash

### 1. Comandos externos

Son programas o utilidades del sistema que se ejecutan desde el script, como si los escribieras en la terminal.

Ejemplos: `ls`, `grep`, `awk`, `curl`, `tar`.

Dónde están: suelen estar en `/bin`, `/usr/bin`, etc.

### 2. Palabras clave (keywords) o estructuras del lenguaje

Son **parte del lenguaje Bash en sí mismo**, no son comandos externos. Se interpretan directamente por el shell.

Ejemplos: `if`, `then`, `else`, `elif`, `fi`, `for`, `while`, `until`, `do`, `done`, `case`, `esac`, `function`, `in`, `select`.

Estas **no aparecen con `which` ni están en el sistema de archivos**, porque son parte del propio intérprete Bash.

Puedes ver todas las palabras clave con:

```shell
compgen -k
```

### 3. Comandos internos (built-ins)

Son comandos integrados en Bash, no son programas externos. Son más rápidos y a menudo esenciales para manipular el entorno.

Ejemplos: `cd`, `echo`, `read`, `exit`, `type`, `declare`, `set`, `test` o `[`.

Para ver si algo es un built-in:

```shell
type nombre_del_comando
```

### 4. Operadores

Se usan para comparar, asignar o realizar operaciones lógicas o aritméticas.

Ejemplos: `=`, `==`, `!=`, `-lt`, `-eq`, `-gt`, `&&`, `||`, `[[ ... ]]`, `[ ... ]`, `$((...))` (para aritmética).

## Ejecución condicional (uso de `if`, `test`, `[ ]`, etc.)

Ejecutar código condicionalmente es fundamental en scripts de shell para tomar decisiones basadas en ciertas condiciones. Puedes usar `if`, `test`, `[ ]` y otros constructos para lograr esto. Ejemplo básico:

```shell
#!/bin/bash

# Solicitar al usuario un número
echo "Ingrese un número:"
read numero

# Verificar si el número es positivo, negativo o cero
if [ $numero -gt 0 ]; then
    echo "El número es positivo."
elif [ $numero -lt 0 ]; then
    echo "El número es negativo."
else
    echo "El número es cero."
fi
```

En este ejemplo:

- `read numero`: lee un número proporcionado por el usuario.
- `if [ $numero -gt 0 ]; then`: verifica si el número es mayor que cero.
- `elif [ $numero -lt 0 ]; then`: verifica si el número es menor que cero.
- `else`: se ejecuta si ninguna de las condiciones anteriores es verdadera.
- `fi`: finaliza la estructura condicional.

Notas adicionales:

- Puedes utilizar operadores de comparación como `eq` (igual), `ne` (no igual), `ge` (mayor o igual), `le` (menor o igual), etc.
- La variable `$numero` contiene el valor ingresado por el usuario.

### Comparación de números en `bash`

| Condición | Operador | Descripción | Ejemplo |
|---|---|---|---|
| Igual | `-eq` | Verifica si dos números son iguales | `if [ "$num1" -eq "$num2" ]; then echo "Iguales"; fi` |
| No igual | `-ne` | Verifica si dos números no son iguales | `if [ "$num1" -ne "$num2" ]; then echo "Diferentes"; fi` |
| Mayor que | `-gt` | Verifica si el primer número es mayor que el segundo | `if [ "$num1" -gt "$num2" ]; then echo "Mayor"; fi` |
| Mayor o igual que | `-ge` | Verifica si el primer número es mayor o igual que el segundo | `if [ "$num1" -ge "$num2" ]; then echo "Mayor o igual"; fi` |
| Menor que | `-lt` | Verifica si el primer número es menor que el segundo | `if [ "$num1" -lt "$num2" ]; then echo "Menor"; fi` |
| Menor o igual que | `-le` | Verifica si el primer número es menor o igual que el segundo | `if [ "$num1" -le "$num2" ]; then echo "Menor o igual"; fi` |

#### Ejemplo completo (corchetes simples)

```shell
#!/bin/bash

echo -n "Introduce el primer número: "
read num1
echo -n "Introduce el segundo número: "
read num2

if [ "$num1" -eq "$num2" ]; then
  echo "Los números son iguales"
fi

if [ "$num1" -ne "$num2" ]; then
  echo "Los números son diferentes"
fi

if [ "$num1" -gt "$num2" ]; then
  echo "$num1 es mayor que $num2"
fi

if [ "$num1" -ge "$num2" ]; then
  echo "$num1 es mayor o igual que $num2"
fi

if [ "$num1" -lt "$num2" ]; then
  echo "$num1 es menor que $num2"
fi

if [ "$num1" -le "$num2" ]; then
  echo "$num1 es menor o igual que $num2"
fi
```

#### Ejemplo completo (dobles corchetes)

```shell
#!/bin/bash

echo -n "Introduce el primer número: "
read num1
echo -n "Introduce el segundo número: "
read num2

if [[ "$num1" -eq "$num2" ]]; then
  echo "Los números son iguales"
fi

if [[ "$num1" -ne "$num2" ]]; then
  echo "Los números son diferentes"
fi

if [[ "$num1" -gt "$num2" ]]; then
  echo "$num1 es mayor que $num2"
fi

if [[ "$num1" -ge "$num2" ]]; then
  echo "$num1 es mayor o igual que $num2"
fi

if [[ "$num1" -lt "$num2" ]]; then
  echo "$num1 es menor que $num2"
fi

if [[ "$num1" -le "$num2" ]]; then
  echo "$num1 es menor o igual que $num2"
fi
```

### Comparación de caracteres/cadenas en `bash`

| Condición | Operador | Descripción | Ejemplo |
|---|---|---|---|
| Igual | `==` | Verifica si dos cadenas son iguales | `if [ "$str1" == "$str2" ]; then echo "Iguales"; fi` |
| No igual | `!=` | Verifica si dos cadenas no son iguales | `if [ "$str1" != "$str2" ]; then echo "Diferentes"; fi` |
| Vacío | `-z` | Verifica si la cadena está vacía | `if [ -z "$str" ]; then echo "Cadena vacía"; fi` |
| No vacío | `-n` | Verifica si la cadena no está vacía | `if [ -n "$str" ]; then echo "Cadena no vacía"; fi` |
| Menor que (ASCII) | `<` | Verifica si la primera cadena es menor que la segunda (orden ASCII) | `if [[ "$str1" < "$str2" ]]; then echo "Menor"; fi` |
| Mayor que (ASCII) | `>` | Verifica si la primera cadena es mayor que la segunda (orden ASCII) | `if [[ "$str1" > "$str2" ]]; then echo "Mayor"; fi` |

#### Ejemplo completo (corchetes simples)

```shell
#!/bin/bash

echo -n "Introduce la primera cadena: "
read str1
echo -n "Introduce la segunda cadena: "
read str2

# Verifica si las cadenas son iguales
if [ "$str1" == "$str2" ]; then
  echo "Las cadenas son iguales"
fi

# Verifica si las cadenas no son iguales
if [ "$str1" != "$str2" ]; then
  echo "Las cadenas son diferentes"
fi

# Verifica si la primera cadena está vacía
if [ -z "$str1" ]; then
  echo "La primera cadena está vacía"
fi

# Verifica si la segunda cadena no está vacía
if [ -n "$str2" ]; then
  echo "La segunda cadena no está vacía"
fi

# Verifica si la primera cadena es menor que la segunda (orden ASCII)
if [[ "$str1" < "$str2" ]]; then
  echo "La primera cadena es menor que la segunda (en orden ASCII)"
fi

# Verifica si la primera cadena es mayor que la segunda (orden ASCII)
if [[ "$str1" > "$str2" ]]; then
  echo "La primera cadena es mayor que la segunda (en orden ASCII)"
fi
```

#### Ejemplo completo (dobles corchetes)

```shell
#!/bin/bash

echo -n "Introduce la primera cadena: "
read str1
echo -n "Introduce la segunda cadena: "
read str2

# Verifica si las cadenas son iguales
if [[ "$str1" == "$str2" ]]; then
  echo "Las cadenas son iguales"
fi

# Verifica si las cadenas no son iguales
if [[ "$str1" != "$str2" ]]; then
  echo "Las cadenas son diferentes"
fi

# Verifica si la primera cadena está vacía
if [ -z "$str1" ]; then
  echo "La primera cadena está vacía"
fi

# Verifica si la segunda cadena no está vacía
if [ -n "$str2" ]; then
  echo "La segunda cadena no está vacía"
fi

# Verifica si la primera cadena es menor que la segunda (orden ASCII)
if [[ "$str1" < "$str2" ]]; then
  echo "La primera cadena es menor que la segunda (en orden ASCII)"
fi

# Verifica si la primera cadena es mayor que la segunda (orden ASCII)
if [[ "$str1" > "$str2" ]]; then
  echo "La primera cadena es mayor que la segunda (en orden ASCII)"
fi
```

## Bucles: `for` y `while` para procesar entradas de línea de comandos y archivos

### Bucle `for`

El bucle `for` se utiliza para iterar sobre una lista de elementos, como una serie de números, una lista de palabras, o los contenidos de un directorio. Sintaxis básica:

```bash
for var in list; do
  # comandos
done
```

**Ejemplo 1: iterar sobre una lista de palabras**

```bash
#!/bin/bash

for word in apple banana cherry; do
  echo "Fruit: $word"
done
```

El bucle itera sobre cada palabra en la lista (`apple`, `banana`, `cherry`) y ejecuta `echo` para cada una.

**Ejemplo 2: iterar sobre una secuencia de números**

```bash
#!/bin/bash

for i in {1..5}; do
  echo "Number: $i"
done
```

El bucle itera sobre la secuencia de números del 1 al 5.

### Bucle `while`

El bucle `while` ejecuta comandos mientras una condición sea verdadera. Sintaxis básica:

```bash
while [ condition ]; do
  # comandos
done
```

**Ejemplo 1: contar hacia atrás**

```bash
#!/bin/bash

counter=5

while [ $counter -gt 0 ]; do
  echo "Counter: $counter"
  counter=$((counter - 1))
done
```

El bucle continúa ejecutándose mientras `counter` sea mayor que 0. En cada iteración, `counter` se decrementa en 1.

**Ejemplo 2: leer entradas del usuario hasta que se introduce una palabra específica**

```bash
#!/bin/bash

input=""

while [ "$input" != "exit" ]; do
  echo -n "Enter a word (type 'exit' to quit): "
  read input
done
```

Sigue ejecutándose mientras la entrada del usuario no sea "exit". Cada vez que se ejecuta, solicita al usuario que introduzca una palabra.

### Diferencias clave

- **`for`**: se utiliza para iterar sobre una lista de elementos predefinidos. El número de iteraciones está determinado por el tamaño de la lista.
- **`while`**: se utiliza para ejecutar comandos repetidamente mientras una condición sea verdadera. El número de iteraciones depende de cuándo se cumpla la condición.

### Ejercicios para practicar

1. **Bucle `for`**: escribe un script que imprima los nombres de todos los archivos en el directorio actual.
2. **Bucle `while`**: escribe un script que solicite al usuario un número y calcule la suma acumulada de esos números hasta que el usuario introduzca `0`.
3. **Bucle `for` con secuencia**: escribe un script que imprima los números del 1 al 10 y su cuadrado.
4. **Bucle `while` con condición**: escribe un script que genere números aleatorios y los imprima hasta que uno de ellos sea divisible por 5.

### Procesar argumentos de línea de comandos con `for`

```shell
#!/bin/bash

# Imprimir todos los argumentos proporcionados en la línea de comandos
echo "Argumentos proporcionados:"
for arg in "$@"; do
    echo "$arg"
done
```

Explicación paso a paso:

1. **Shebang (`#!/bin/bash`)**: indica que el script debe ser interpretado y ejecutado por Bash.
2. **Comentario**: los comentarios en Bash comienzan con `#` y son ignorados por el intérprete.
3. **`echo "Argumentos proporcionados:"`**: imprime un encabezado antes de listar los argumentos.
4. **`for arg in "$@"; do`**: inicia un bucle que itera a través de todos los argumentos proporcionados en la línea de comandos. `$@` es una variable especial que representa todos los argumentos pasados al script.
5. **`echo "$arg"`**: dentro del bucle, imprime cada argumento en una nueva línea.
6. **`done`**: cierra el bucle `for`.

Ejecución:

```shell
./nombre_del_script.sh arg1 arg2 arg3
```

Salida:

```plain text
Argumentos proporcionados:
arg1
arg2
arg3
```

### Procesar argumentos de línea de comandos con `while`

Se puede lograr la misma funcionalidad con un bucle `while` en lugar de `for`:

```shell
#!/bin/bash

# Imprimir todos los argumentos proporcionados en la línea de comandos
echo "Argumentos proporcionados:"

# Inicializar el índice
index=1

# Utilizar un bucle while para iterar a través de los argumentos
while [ $index -le $# ]; do
    # Imprimir el argumento actual
    echo "${!index}"

    # Incrementar el índice
    ((index++))
done
```

Explicación:

1. **`index=1`**: inicializa una variable `index` en 1, usada para acceder a los argumentos uno por uno.
2. **`while [ $index -le $# ]; do`**: se ejecuta mientras `index` sea menor o igual al número total de argumentos (`$#`).
3. **`echo "${!index}"`**: imprime el argumento correspondiente al valor actual de `index`. La expresión `"${!index}"` accede al valor de la variable cuyo nombre es el contenido de `index` (indirección).
4. **`((index++))`**: incrementa `index` en cada iteración.
5. **`done`**: cierra el bucle.

Ambos bucles (`for` y `while`) logran la misma tarea. El bucle `for` es más conciso en este contexto, pero algunos programadores prefieren `while` por su flexibilidad en otras situaciones.

> **Nota — Indirección en Bash:** en Bash, la indirección (usando `!`) referencia el valor de una variable cuyo nombre se determina mediante la expansión del valor que precede a la exclamación. Es útil con índices o nombres de variables dinámicos:
> - El orden en que se declaran las variables y los parámetros pasados afecta la indirección; las variables pasadas por parámetro ocupan las primeras posiciones.
> - La indirección prioriza las variables pasadas por parámetro sobre las declaradas localmente.
>
> Ejemplo:
> ```shell
> #!/bin/bash
>
> variable1="a"
> variable2="b"
> parametro1="valor1"
> parametro2="valor2"
> parametro3="valor3"
>
> indice=3
>
> echo "Valor de la indirección: \${!indice} -> ${!indice}"
> ```
> - Si ejecutamos el script sin parámetros (`./mi_script.sh`), el resultado será `"valor3"`.
> - Si ejecutamos el script con parámetros (`./mi_script.sh parametro1 parametro2 parametro3`), el resultado seguirá siendo `"valor3"`.
>
> Cambiando `indice` a 4, apuntaría a `variable2` cuando se han pasado parámetros.

### Procesar archivos en un directorio con `for`

```shell
#!/bin/bash

# Imprimir el nombre de todos los archivos en el directorio actual
echo "Archivos en el directorio actual:"
for archivo in *; do
    if [ -f "$archivo" ]; then
        echo "$archivo"
    fi
done
```

Explicación:

1. **Shebang**: indica que el script debe ser interpretado por Bash.
2. **Comentario**: describe el propósito del script.
3. **`echo "Archivos en el directorio actual:"`**: imprime un encabezado.
4. **`for archivo in *; do`**: itera a través de todos los elementos del directorio actual (`*` representa todos los elementos, incluidos archivos y directorios).
5. **`if [ -f "$archivo" ]; then`**: verifica si el elemento actual es un archivo regular (`-f`).
6. **`echo "$archivo"`**: si es un archivo, imprime su nombre.
7. **`fi`** / **`done`**: cierran la estructura condicional y el bucle.

Ejecución:

```shell
chmod +x nombre_del_script.sh
./nombre_del_script.sh
```

> **Nota:** si deseas que el script imprima solo los nombres de los archivos con un tamaño mayor que 1GB, puedes modificarlo usando `du` (disk usage) para obtener el tamaño de cada archivo y compararlo con un umbral:
> ```shell
> #!/bin/bash
>
> # Imprimir el nombre de archivos con tamaño mayor que 1GB en el directorio actual
> echo "Archivos con tamaño mayor que 1GB:"
> for archivo in *; do
>     if [ -f "$archivo" ]; then
>         tamaño=$(du -b "$archivo" | awk '{print $1}')  # Obtener el tamaño del archivo en bytes
>         tamaño_en_gb=$((tamaño / 1024**3))  # Convertir el tamaño a GB
>
>         if [ $tamaño_en_gb -gt 1 ]; then
>             echo "$archivo"
>         fi
>     fi
> done
> ```
> Explicación de los cambios:
> 1. `tamaño=$(du -b "$archivo" | awk '{print $1}')`: usa `du -b` para obtener el tamaño del archivo en bytes; `awk` extrae la primera columna.
> 2. `tamaño_en_gb=$((tamaño / 1024**3))`: convierte el tamaño de bytes a gigabytes.
> 3. `if [ $tamaño_en_gb -gt 1 ]; then`: verifica si el tamaño en GB es mayor que 1.
>
> También puedes usar la opción `-h` de `ls` para mostrar tamaños en formato legible por humanos:
> ```shell
> #!/bin/bash
>
> # Imprimir el nombre de archivos con tamaño mayor que 1GB en el directorio actual
> echo "Archivos con tamaño mayor que 1GB:"
> for archivo in *; do
>     if [ -f "$archivo" ]; then
>         tamaño=$(ls -lh "$archivo" | awk '{print $5}')  # Obtener el tamaño del archivo en formato humano
>
>         if [ "$tamaño" != "0" ] && [ "$tamaño" != "1.0G" ]; then
>             echo "$archivo"
>         fi
>     fi
> done
> ```
> Explicación:
> 1. `tamaño=$(ls -lh "$archivo" | awk '{print $5}')`: usa `ls -lh` para obtener el tamaño en formato humano.
> 2. `if [ "$tamaño" != "0" ] && [ "$tamaño" != "1.0G" ]; then`: filtra los archivos que no sean de tamaño cero o exactamente 1GB.

### Leer líneas de un archivo con `while`

```shell
#!/bin/bash

# Leer líneas de un archivo e imprimirlas
archivo="ejemplo.txt"
echo "Contenido de $archivo:"
while IFS= read -r linea; do
    echo "$linea"
done < "$archivo"
```

Explicación:

1. `archivo="ejemplo.txt"`: asigna el nombre del archivo que se va a leer.
2. `echo "Contenido de $archivo:"`: imprime un mensaje con el nombre del archivo.
3. `while IFS= read -r linea; do`: se ejecuta mientras haya líneas en el archivo. `IFS= read -r linea` lee cada línea y la asigna a `linea`.
4. `echo "$linea"`: imprime la línea actual.
5. `done < "$archivo"`: cierra el bucle y redirige la entrada del bucle desde el archivo especificado.

Este tipo de estructura es útil cuando necesitas procesar cada línea de un archivo de forma individual.

## Procesar entradas de scripts (`$1`, `$2`, etc.)

Procesar entradas de scripts utilizando los parámetros de línea de comandos (`$1`, `$2`, etc.) es una tarea común. Ejemplo básico:

```shell
#!/bin/bash

# Verificar si se proporciona al menos un argumento
if [ $# -eq 0 ]; then
    echo "Por favor, proporciona al menos un argumento."
    exit 1
fi

# Imprimir todos los argumentos proporcionados
echo "Argumentos proporcionados:"
for arg in "$@"; do
    echo "$arg"
done

# Procesar cada argumento individualmente
echo "Procesando argumentos individualmente:"
contador=1
while [ $contador -le $# ]; do
    echo "Argumento $contador: ${!contador}"
    contador=$((contador + 1))
done
```

Explicación paso a paso:

- **Verificación de argumentos:** `$#` representa el número total de argumentos proporcionados. La condición `[ $# -eq 0 ]` verifica si no se proporcionan argumentos. Si no hay al menos uno, el script imprime un mensaje y sale con código 1 (error).
- **Bucle `for` para imprimir argumentos:** `$@` representa todos los argumentos; el bucle itera sobre cada uno y lo imprime.
- **Bucle `while` para procesar argumentos individualmente:** la condición `[ $contador -le $# ]` se cumple mientras `contador` sea menor o igual al número total de argumentos. `${!contador}` es indirección: si `contador` es 2, `${!contador}` equivale a `$2`.

Ejecución:

```shell
./script.sh uno dos tres cuatro
```

Salida esperada:

```plain text
Argumentos proporcionados:
uno
dos
tres
cuatro
Procesando argumentos individualmente:
Argumento 1: uno
Argumento 2: dos
Argumento 3: tres
Argumento 4: cuatro
```

### Variables especiales importantes

- **`$0`** - Nombre del script
- **`$1, $2, $3...`** - Argumentos del script
- **`$#`** - Número total de argumentos
- **`$@`** - Todos los argumentos
- **`$$`** - PID del proceso actual
- **`$?`** - Código de salida del último comando

## Procesar la salida de comandos de shell dentro de un script

Procesar la salida de comandos de shell dentro de un script es una tarea común y puede realizarse utilizando la expansión de comandos y las tuberías (pipes). Ejemplo básico:

```shell
#!/bin/bash

# Almacenar la salida de un comando en una variable
fecha_actual=$(date)
echo "Fecha actual: $fecha_actual"

# Procesar la salida de un comando en un bucle
echo "Listado de archivos en el directorio actual:"
for archivo in $(ls); do
    echo "$archivo"
done

# Filtrar la salida de un comando usando grep
echo "Procesar solo archivos .txt:"
for archivo in $(ls | grep '\.txt$'); do
    echo "$archivo"
done
```

En este ejemplo:

- `fecha_actual=$(date)`: almacena la salida del comando `date` en la variable `$fecha_actual`.
- `for archivo in $(ls); do`: utiliza la expansión de comandos para iterar sobre la salida de `ls`.
- `for archivo in $(ls | grep '\.txt$'); do`: utiliza la tubería (`|`) para enviar la salida de `ls` a `grep` y filtrar solo los archivos que terminan en `.txt`.

La expansión de comandos (`$()`) y las tuberías (`|`) son herramientas poderosas para procesar la salida de comandos en scripts de shell. Sin embargo, es importante manejar adecuadamente casos especiales, como nombres de archivos que contienen espacios o caracteres especiales; en esos casos pueden necesitarse enfoques más avanzados, como el uso de comillas o el comando `read` para procesar la salida línea por línea.

## Sintaxis de scripts en Bash — guía de referencia

### 1. Estructura básica de un script

```bash
#!/bin/bash                 # 1. Shebang (obligatorio)
# Comentario descriptivo    # 2. Comentarios
                            # 3. Línea vacía (opcional pero recomendada)
comando1                    # 4. Comandos y código
variable="valor"            # 5. Variables
funcion() {                 # 6. Funciones
    echo "Hola"
}
```

### 2. Shebang (`#!`)

```bash
#!/bin/bash              # Usar bash
#!/bin/sh                # Usar sh (más básico)
#!/bin/zsh               # Usar zsh
#!/usr/bin/env bash      # Buscar bash en el PATH (más portable)
```

Reglas del shebang:

- **DEBE** ser la primera línea del archivo.
- **DEBE** empezar con `#!`.
- **No puede** tener espacios antes.
- **No puede** tener líneas vacías antes.

```bash
# CORRECTO
#!/bin/bash

# INCORRECTO
   #!/bin/bash          # Espacio antes
# Un comentario
#!/bin/bash             # No es la primera línea
```

### 3. Comentarios

**Comentarios de línea simple:**

```bash
# Este es un comentario completo
comando1               # Comentario al final de línea
# Comentario con explicación detallada
# que puede ocupar múltiples líneas
```

**Comentarios de bloque:**

```bash
# ===================================
# Script de backup automático
# Autor: Juan Pérez
# Fecha: 2024-09-15
# Versión: 1.0
# ===================================
```

**Comentarios para documentación:**

```bash
#!/bin/bash
#
# DESCRIPCIÓN: Script para procesar archivos de log
# USO: ./script.sh <directorio> [opciones]
# EJEMPLOS:
#   ./script.sh /var/log
#   ./script.sh /tmp --verbose
#
```

### 4. Espaciado y formato

**Espacios en comandos y variables:**

```bash
# CORRECTO - Variables
nombre="Juan"                    # Sin espacios alrededor del =
edad=25                          # Sin espacios alrededor del =

# INCORRECTO - Variables
nombre = "Juan"                  # Error: espacios alrededor del =
edad= 25                         # Error: espacio después del =

# CORRECTO - Condicionales
if [ $edad -gt 18 ]; then        # Espacios dentro de [ ]
    echo "Mayor de edad"
fi

# INCORRECTO - Condicionales
if [$edad -gt 18]; then          # Error: sin espacios dentro de [ ]
    echo "Mayor de edad"
fi
```

**Indentación (espaciado interno):**

```bash
# Usar 4 espacios o 2 espacios consistentemente
if [ $edad -gt 18 ]; then
    echo "Mayor de edad"              # 4 espacios de indentación
    if [ "$nombre" = "admin" ]; then
        echo "Usuario administrador"  # 8 espacios (4 + 4)
    fi
fi

# También válido con 2 espacios
for archivo in *.txt; do
  echo "Procesando: $archivo"     # 2 espacios
  if [ -f "$archivo" ]; then
    wc -l "$archivo"              # 4 espacios (2 + 2)
  fi
done
```

### 5. Terminación de comandos

**Punto y coma (`;`):**

```bash
# Separar comandos en la misma línea
cd /tmp; ls -la; pwd

# En condicionales (obligatorio antes de then)
if [ -f "archivo.txt" ]; then
    echo "Archivo existe"
fi

# Alternativa: then en nueva línea
if [ -f "archivo.txt" ]
then
    echo "Archivo existe"
fi
```

**Continuación de línea (`\`):**

```bash
# Comando muy largo dividido en múltiples líneas
comando_muy_largo_con_muchas_opciones \
    --opcion1 valor1 \
    --opcion2 valor2 \
    --archivo archivo_con_nombre_muy_largo.txt

# En condicionales complejas
if [ "$usuario" = "admin" ] && \
   [ "$password" = "secreto" ] && \
   [ -f "/etc/configuracion.conf" ]; then
    echo "Acceso autorizado"
fi
```

### 6. Caracteres especiales y su significado

**Comillas y su uso:**

```bash
# Comillas dobles: permiten expansión de variables
nombre="Juan"
mensaje="Hola $nombre"          # Resultado: "Hola Juan"

# Comillas simples: literal (sin expansión)
mensaje='Hola $nombre'          # Resultado: "Hola $nombre"

# Sin comillas: problemático con espacios
archivo=mi documento.txt        # Error: se interpreta como dos argumentos
archivo="mi documento.txt"      # Correcto
```

**Metacaracteres importantes:**

```bash
$variable        # Expansión de variable
${variable}      # Expansión explícita de variable
$(comando)       # Sustitución de comando
`comando`        # Sustitución de comando (estilo antiguo)
*                # Wildcard: cualquier secuencia de caracteres
?                # Wildcard: cualquier carácter único
[]               # Clase de caracteres: [abc] o [a-z]
{}               # Expansión de llaves: {a,b,c}
|                # Pipe: pasar salida como entrada
&                # Ejecutar en background
&&               # AND lógico: ejecutar si anterior exitoso
||               # OR lógico: ejecutar si anterior falla
>                # Redirección: sobrescribir archivo
>>               # Redirección: agregar a archivo
<                # Redirección: entrada desde archivo
```

### 7. Sintaxis de estructuras de control

**Condicionales:**

```bash
# if-then-else-fi
if [ condición ]; then
    comandos
elif [ otra_condición ]; then
    otros_comandos
else
    comandos_por_defecto
fi

# case-in-esac
case $variable in
    patrón1)
        comandos
        ;;
    patrón2|patrón3)
        comandos
        ;;
    *)
        comandos_por_defecto
        ;;
esac
```

**Bucles:**

```bash
# for-do-done
for variable in lista; do
    comandos
done

# while-do-done
while [ condición ]; do
    comandos
done

# until-do-done
until [ condición ]; do
    comandos
done
```

**Funciones:**

```bash
# Definición tradicional
function nombre_funcion() {
    local variable_local="valor"
    echo "Función ejecutada"
    return 0
}

# Definición simplificada
nombre_funcion() {
    comandos
}

# Llamada a la función
nombre_funcion parametro1 parametro2
```

### 8. Reglas de sintaxis específicas

**Variables:**

```bash
# CORRECTO
VAR="valor"              # Sin espacios alrededor del =
echo $VAR                # Usar con $
echo ${VAR}               # Forma explícita
echo "$VAR"               # Con comillas (recomendado)

# INCORRECTO
VAR = "valor"             # Espacios alrededor del =
echo VAR                  # Sin $ no expande la variable
```

**Arrays:**

```bash
# CORRECTO
array=("elemento1" "elemento2" "elemento3")
echo ${array[0]}         # Primer elemento
echo ${array[@]}         # Todos los elementos
echo ${#array[@]}        # Número de elementos

# Iteración correcta
for elemento in "${array[@]}"; do
    echo "$elemento"
done
```

**Operadores de comparación:**

```bash
# CORRECTO - Números
[ $a -eq $b ]            # igual
[ $a -ne $b ]            # no igual
[ $a -lt $b ]            # menor que
[ $a -gt $b ]            # mayor que

# CORRECTO - Strings
[ "$str1" = "$str2" ]    # igual (usar = no ==)
[ "$str1" != "$str2" ]   # diferente
[ -z "$str" ]            # string vacío
[ -n "$str" ]            # string no vacío
```

### 9. Manejo de errores y validaciones

**Verificar argumentos:**

```bash
# Verificar número de argumentos
if [ $# -eq 0 ]; then
    echo "Error: No se proporcionaron argumentos"
    echo "Uso: $0 <archivo> [opciones]"
    exit 1
fi

# Verificar si archivo existe
archivo="$1"
if [ ! -f "$archivo" ]; then
    echo "Error: El archivo '$archivo' no existe"
    exit 1
fi
```

**Control de errores:**

```bash
# Salir si cualquier comando falla
set -e

# Tratar variables no definidas como error
set -u

# Mostrar comandos antes de ejecutarlos (debug)
set -x

# Combinado
set -euo pipefail       # Configuración estricta recomendada
```

### 10. Buenas prácticas de sintaxis

**Estructura general recomendada:**

```bash
#!/bin/bash
#
# DESCRIPCIÓN: Qué hace el script
# USO: Cómo usarlo
# AUTOR: Tu nombre
# FECHA: Fecha de creación
#

# Configuración estricta (opcional pero recomendada)
set -euo pipefail

# Variables globales
readonly SCRIPT_NAME=$(basename "$0")
readonly SCRIPT_DIR=$(dirname "$0")

# Funciones (definir antes de usar)
mostrar_ayuda() {
    cat << EOF
Uso: $SCRIPT_NAME [opciones] archivo

Opciones:
    -h, --help     Mostrar esta ayuda
    -v, --verbose  Modo detallado

Ejemplos:
    $SCRIPT_NAME archivo.txt
    $SCRIPT_NAME -v archivo.txt
EOF
}

# Función principal
main() {
    # Validar argumentos
    if [ $# -eq 0 ]; then
        mostrar_ayuda
        exit 1
    fi

    # Lógica principal del script
    echo "Procesando: $1"
}

# Ejecutar función principal con todos los argumentos
main "$@"
```

**Nombres de variables y funciones:**

```bash
# Variables en minúsculas con guiones bajos
nombre_usuario="juan"
archivo_configuracion="/etc/app.conf"
contador_errores=0

# Constantes en mayúsculas
readonly MAX_INTENTOS=5
readonly LOG_FILE="/var/log/app.log"

# Funciones con nombres descriptivos
procesar_archivo() {
    local archivo="$1"
    # procesar...
}

validar_entrada() {
    local entrada="$1"
    # validar...
}
```

**Uso de comillas:**

```bash
# SIEMPRE usar comillas con variables que pueden contener espacios
archivo="/path/with spaces/file.txt"
cp "$archivo" "/backup/"
echo "Procesando: $archivo"

# Usar comillas con expansiones
for archivo in *.txt; do
    echo "Encontrado: $archivo"
    if [ -f "$archivo" ]; then
        wc -l "$archivo"
    fi
done
```

### 11. Errores comunes de sintaxis

**Error 1: espacios en asignaciones**

```bash
# INCORRECTO
nombre = "Juan"              # Error: bash interpreta como comando

# CORRECTO
nombre="Juan"                # Sin espacios alrededor del =
```

**Error 2: falta de espacios en condicionales**

```bash
# INCORRECTO
if [$edad -gt 18]; then      # Error: faltan espacios dentro de [ ]

# CORRECTO
if [ $edad -gt 18 ]; then    # Espacios después de [ y antes de ]
```

**Error 3: comillas incorrectas**

```bash
# PROBLEMÁTICO
archivo=mi archivo.txt       # Error: se interpreta como dos argumentos
cp $archivo /backup/         # Error: falla si el nombre tiene espacios

# CORRECTO
archivo="mi archivo.txt"     # Con comillas
cp "$archivo" /backup/       # Con comillas al usar
```

**Error 4: uso incorrecto de comparadores**

```bash
# INCORRECTO
if [ $edad == 18 ]; then     # == no es estándar en [ ]
if [ $edad = "18" ]; then    # Comparación de string vs número

# CORRECTO
if [ $edad -eq 18 ]; then           # Comparación numérica
if [ "$nombre" = "Juan" ]; then     # Comparación de strings
```

### Resumen de reglas sintácticas

| Elemento | Regla | Ejemplo |
|---|---|---|
| **Shebang** | Primera línea, sin espacios antes | `#!/bin/bash` |
| **Variables** | Sin espacios alrededor de `=` | `var="valor"` |
| **Condicionales** | Espacios dentro de `[ ]` | `[ $a -eq $b ]` |
| **Bucles** | `; do` o `then` en nueva línea | `for i in lista; do` |
| **Comillas** | Usar con variables que pueden tener espacios | `"$variable"` |
| **Funciones** | Paréntesis después del nombre | `funcion() {` |
| **Comentarios** | Empezar con `#` | `# Comentario` |
| **Indentación** | Consistente (2 o 4 espacios) | `comando` |

## Script de ejemplo: menú interactivo con `case`

Este menú:

- Presenta varias opciones al usuario.
- Usa patrones con `|`, `[a-z]`, `*`, etc.
- Incluye validación con patrón comodín.

```bash
#!/bin/bash

clear
echo "=== MENÚ PRINCIPAL ==="
echo "a) Mostrar la fecha actual"
echo "b) Mostrar los archivos en el directorio actual"
echo "c) Saludar con tu nombre"
echo "q) Salir"
echo "-------------------------"

# Leer la opción del usuario
read -p "Elige una opción (a/b/c/q): " opcion

case $opcion in
  a|A)
    echo "La fecha actual es: $(date)"
    ;;
  b|B)
    echo "Archivos en el directorio actual:"
    ls
    ;;
  c|C)
    read -p "¿Cómo te llamas? " nombre
    # Patrón: si el nombre tiene al menos una letra (letras y espacios)
    case $nombre in
      [a-zA-Z]*)
        echo "¡Hola, $nombre!"
        ;;
      *)
        echo "Nombre no válido."
        ;;
    esac
    ;;
  q|Q)
    echo "Saliendo del programa."
    ;;
  *)
    echo "Opción no válida."
    ;;
esac
```

### Cosas interesantes del script

- `a|A`, `b|B`…: permite usar letras **mayúsculas o minúsculas**.
- En el sub-`case` del nombre: `[a-zA-Z]*` permite que el nombre empiece por letras, seguido de cualquier cosa (letras, espacios…); `*` es el **catch-all**, que atrapa cualquier otra cosa y da mensaje de error.

### Cómo ejecutarlo

1. Guarda el código en un archivo, por ejemplo: `menu.sh`.
2. Dale permisos de ejecución:
   ```bash
   chmod +x menu.sh
   ```
3. Ejecuta:
   ```bash
   ./menu.sh
   ```

## Condicionales `if` y `case` en detalle

### Condicional `if` en Bash

**Sintaxis general:**

```bash
if [ CONDICIÓN ]; then
  # comandos si la condición es verdadera
elif [ OTRA_CONDICIÓN ]; then
  # comandos si la otra condición es verdadera
else
  # comandos si ninguna condición fue verdadera
fi
```

**Desglose completo:**

| Elemento | Significado |
|---|---|
| `if` | Inicia la evaluación condicional. |
| `[ CONDICIÓN ]` | Expresión que será evaluada (con espacios entre corchetes y la condición). |
| `then` | Palabra clave que indica el inicio del bloque de comandos si la condición es verdadera. |
| `elif` | (Opcional) "else if": otra condición a evaluar si la anterior falló. |
| `else` | (Opcional) Bloque de comandos si ninguna condición fue verdadera. |
| `fi` | Final del bloque `if` (es `if` al revés). |

**Operadores comunes en condiciones**

Comparación de números (con `eq`, `ne`, etc.):

| Comparación | Significado |
|---|---|
| `-eq` | Igual a |
| `-ne` | No igual a |
| `-lt` | Menor que |
| `-le` | Menor o igual que |
| `-gt` | Mayor que |
| `-ge` | Mayor o igual que |

Comparación de cadenas:

| Comparación | Significado |
|---|---|
| `=` o `==` | Igual a |
| `!=` | No igual a |
| `-z "cadena"` | Cadena vacía |
| `-n "cadena"` | Cadena no vacía |

Archivos:

| Comando | Significado |
|---|---|
| `-f archivo` | ¿Existe y es un archivo regular? |
| `-d dir` | ¿Existe y es un directorio? |
| `-e archivo` | ¿Existe el archivo o directorio? |
| `-r` | ¿Es legible? |
| `-w` | ¿Es escribible? |
| `-x` | ¿Es ejecutable? |

**Ejemplo básico:**

```bash
#!/bin/bash

numero=5

if [ $numero -gt 3 ]; then
  echo "El número es mayor que 3"
elif [ $numero -eq 3 ]; then
  echo "El número es igual a 3"
else
  echo "El número es menor que 3"
fi
```

**Cuidado con los espacios:**

```bash
[ $a -gt 3 ]   # correcto
[$a -gt 3]     # ERROR: necesita espacios entre [, la condición y ]
```

### Condicional `case` en Bash

Usado cuando se quiere comparar **una variable contra múltiples patrones** (más limpio que muchos `if-elif`).

**Sintaxis general:**

```bash
case VARIABLE in
  patrón1)
    # comandos si VARIABLE coincide con patrón1
    ;;
  patrón2)
    # comandos si VARIABLE coincide con patrón2
    ;;
  *)
    # comandos si ningún patrón coincide (caso por defecto)
    ;;
esac
```

**Desglose:**

| Elemento | Significado |
|---|---|
| `case` | Inicia el condicional tipo `case` |
| `VARIABLE` | Lo que se va a evaluar contra los patrones |
| `in` | Indica que comienzan los patrones |
| `patrón)` | El patrón que se va a comparar con la variable |
| `;;` | Termina el bloque de comandos para ese patrón |
| `*` | Patrón comodín (por defecto, si ninguno coincide) |
| `esac` | Final del bloque `case` (es `case` al revés) |

**Ejemplo:**

```bash
#!/bin/bash

read -p "Ingrese una opción (a, b, c): " opcion

case $opcion in
  a)
    echo "Seleccionaste A"
    ;;
  b)
    echo "Seleccionaste B"
    ;;
  c)
    echo "Seleccionaste C"
    ;;
  *)
    echo "Opción no válida"
    ;;
esac
```

**`case` admite patrones avanzados:**

| Patrón | Significado |
|---|---|
| `a\|b` | Coincide con `a` o `b` |
| `[0-9])` | Coincide con cualquier dígito |
| `*)` | Coincide con cualquier otra cosa |

## Cuándo usar `if` o `case`

| Necesitas... | Usa |
|---|---|
| Comparar números, cadenas, condiciones de archivos | `if` |
| Evaluar una variable contra múltiples valores | `case` |
| Evaluar patrones (como expresiones regulares) | `case` |
| Necesitas lógica más compleja (varias condiciones) | `if` (con `elif` y `else`) |

### ¿Cuándo usar `if`?

`if` es más **versátil y poderoso** cuando necesitas evaluar condiciones **más complejas o variadas**.

Usa `if` cuando:

| Escenario | Ejemplo |
|---|---|
| Comparar **números** | `[ $x -gt 10 ]` |
| Comparar **cadenas** con operadores lógicos | `[ "$x" = "hola" ] && [ "$y" != "mundo" ]` |
| Verificar propiedades de archivos o directorios | `[ -f archivo.txt ]` |
| Combinar muchas condiciones con `&&`, `\|\|` | |
| Necesitas condiciones **dinámicas o booleanas** | por ejemplo, en scripts más lógicos o matemáticos |

Ejemplo:

```bash
if [ -f archivo.txt ] && [ -r archivo.txt ]; then
  echo "El archivo existe y es legible"
else
  echo "Archivo no disponible o sin permisos"
fi
```

### ¿Cuándo usar `case`?

`case` es mejor cuando estás evaluando una variable **contra múltiples valores posibles** o **patrones fijos**.

Usa `case` cuando:

| Escenario | Ejemplo |
|---|---|
| Crear **menús** con varias opciones | a, b, c, salir |
| Validar **entradas de usuario simples** | sí/no, 1/2/3 |
| Evaluar **extensiones de archivos** | `*.txt`, `*.jpg`, etc. |
| Quieres **limpieza visual** al evitar muchos `if` anidados | |

Ejemplo:

```bash
case $opcion in
  start) echo "Iniciando servicio";;
  stop) echo "Deteniendo servicio";;
  restart) echo "Reiniciando servicio";;
  *) echo "Opción no válida";;
esac
```

### Resumen comparativo

| Característica | `if` | `case` |
|---|---|---|
| Comparaciones complejas | Sí | No |
| Múltiples valores/patrones | Poco legible | Claro y limpio |
| Archivos y condiciones | Sí (`-f`, `-d`, etc.) | No |
| Expresiones aritméticas | Sí (`-eq`, `-gt`, etc.) | No |
| Patrones de texto | Limitado | Avanzado (globbing) |
| Combinación de condiciones | `&&`, `\|\|` | — |

### Consejo final

- Usa `if` cuando estás evaluando **lógica**, condiciones de archivos, números, o combinaciones booleanas.
- Usa `case` cuando estás **comparando un valor contra múltiples opciones conocidas** o patrones de texto.

## Controles de flujo en Bash

Los controles de flujo son **comandos que modifican el comportamiento normal** del script, permitiendo saltar, terminar o cambiar la ejecución de bucles y funciones.

### `break` — salir del bucle

`break` sale completamente del bucle actual.

```bash
break        # Sale del bucle más interno
break n      # Sale de n bucles anidados
```

Ejemplo:

```bash
for i in {1..10}; do
    if [ $i -eq 5 ]; then
        echo "Saliendo en $i"
        break                # Sale del for completamente
    fi
    echo "Número: $i"
done
echo "Bucle terminado"       # Se ejecuta después del break
```

Salida:

```plain text
Número: 1
Número: 2
Número: 3
Número: 4
Saliendo en 5
Bucle terminado
```

### `continue` — saltar iteración

`continue` salta a la **siguiente iteración** del bucle sin ejecutar el resto del código.

```bash
continue     # Salta a la siguiente iteración
continue n   # Salta en n bucles anidados
```

Ejemplo:

```bash
for i in {1..5}; do
    if [ $((i % 2)) -eq 0 ]; then
        echo "Saltando número par: $i"
        continue             # Salta a la siguiente iteración
    fi
    echo "Número impar: $i"  # Solo se ejecuta para impares
done
echo "Bucle completado"
```

Salida:

```plain text
Número impar: 1
Saltando número par: 2
Número impar: 3
Saltando número par: 4
Número impar: 5
Bucle completado
```

### `exit` — terminar script

`exit` termina completamente el script.

```bash
exit         # Salir con código 0 (éxito)
exit n       # Salir con código n (error si n > 0)
```

Ejemplo:

```bash
archivo="$1"
if [ ! -f "$archivo" ]; then
    echo "Error: El archivo no existe"
    exit 1               # Termina script con error
fi

echo "Procesando archivo..."
exit 0                   # Termina script con éxito
```

Códigos de salida comunes:

- `0` = Éxito
- `1` = Error general
- `2` = Uso incorrecto
- `127` = Comando no encontrado

### `return` — salir de función

`return` sale de una función y opcionalmente devuelve un código.

```bash
return       # Salir de función con código 0
return n     # Salir de función con código n
```

Ejemplo:

```bash
validar_numero() {
    local numero="$1"

    if [[ ! $numero =~ ^[0-9]+$ ]]; then
        echo "No es un número válido"
        return 1         # Sale de función con error
    fi

    if [ $numero -lt 0 ]; then
        echo "Número negativo"
        return 2         # Sale con código específico
    fi

    echo "Número válido: $numero"
    return 0             # Sale con éxito
}

# Usar la función
if validar_numero "25"; then
    echo "Validación exitosa"
else
    echo "Validación fallida (código: $?)"
fi
```

### Combinando controles de flujo

**Ejemplo con `break` y `continue`:**

```bash
while true; do
    read -p "Ingresa un número (0=salir, par=saltar): " num

    if [ $num -eq 0 ]; then
        echo "Saliendo..."
        break            # Sale del while
    fi

    if [ $((num % 2)) -eq 0 ]; then
        echo "Número par, saltando procesamiento"
        continue         # Vuelve al read
    fi

    echo "Procesando número impar: $num"
done
```

**Bucles anidados:**

```bash
for grupo in A B C; do
    echo "Procesando grupo: $grupo"

    for i in {1..5}; do
        if [ $i -eq 3 ] && [ "$grupo" = "B" ]; then
            echo "  Saltando grupo B en posición 3"
            break        # Solo sale del for interno
        fi

        if [ $i -eq 2 ]; then
            echo "  Saltando posición 2"
            continue     # Solo afecta el for interno
        fi

        echo "  Item $i del grupo $grupo"
    done
done
```

### Cuándo usar cada uno

| Control | Cuándo usar | Efecto |
|---|---|---|
| **`break`** | Salir de bucle cuando se cumple condición | Termina bucle completamente |
| **`continue`** | Saltar iteración específica | Va a siguiente vuelta del bucle |
| **`exit`** | Error fatal o fin normal del script | Termina todo el script |
| **`return`** | Salir de función con resultado | Termina solo la función |

### Ejemplo práctico: procesador de archivos

```bash
#!/bin/bash

procesar_archivo() {
    local archivo="$1"

    if [ ! -f "$archivo" ]; then
        echo "Archivo no existe: $archivo"
        return 1
    fi

    if [ ! -r "$archivo" ]; then
        echo "Sin permisos de lectura: $archivo"
        return 2
    fi

    echo "Archivo procesado: $archivo"
    return 0
}

# Procesar múltiples archivos
for archivo in *.txt; do
    if [ "$archivo" = "secreto.txt" ]; then
        echo "Saltando archivo secreto"
        continue
    fi

    if ! procesar_archivo "$archivo"; then
        echo "Error procesando $archivo (código: $?)"

        if [ "$archivo" = "critico.txt" ]; then
            echo "Archivo crítico falló. Terminando script."
            exit 1
        fi
    fi

    if [ "$(wc -l < "$archivo" 2>/dev/null)" -gt 1000 ]; then
        echo "Archivo muy grande. Terminando procesamiento."
        break
    fi
done

echo "Procesamiento completado"
```

### Resumen rápido

- **`break`**: sale del bucle → "¡Basta, termino!"
- **`continue`**: salta a siguiente iteración → "Omito esta, sigo con la siguiente"
- **`exit`**: termina el script → "Se acabó todo"
- **`return`**: sale de función → "Termino esta tarea"

## Bucle `while` en detalle: sintaxis palabra por palabra

**Sintaxis básica:**

```bash
while [ condición ]; do
    # comandos
done
```

Explicación palabra por palabra:

- `while`: palabra clave que inicia el bucle. Indica que el bloque de código se ejecutará **mientras** la condición sea verdadera.
- `[ condición ]`: la **condición lógica** que se evalúa. Si el resultado es verdadero (código de salida 0), se ejecuta el cuerpo del bucle.
- `do`: indica el inicio del **bloque de comandos** que se ejecutarán mientras la condición sea verdadera.
- `# comandos`: aquí van las instrucciones que deseas repetir.
- `done`: indica el **final del bloque** del bucle.

> **Nota:** `[ condición ]` es equivalente a `test condición`. Se pueden usar operadores como `-le` (menor o igual), `=` (igual para cadenas), etc.

### Ejemplo 1: bucle de contador

```bash
contador=1
while [ $contador -le 5 ]; do
    echo "Contador: $contador"
    contador=$((contador + 1))
done
```

¿Qué hace?

1. `contador=1`: se inicializa una variable `contador` en 1.
2. `while [ $contador -le 5 ]`: se verifica si el contador es menor o igual que 5.
3. `echo "Contador: $contador"`: imprime el valor del contador.
4. `contador=$((contador + 1))`: incrementa el contador en 1.
5. Repite hasta que `contador` sea mayor que 5.
6. Cuando la condición es falsa, sale del bucle.

Resultado:

```plain text
Contador: 1
Contador: 2
Contador: 3
Contador: 4
Contador: 5
```

### Ejemplo 2: bucle infinito con salida condicional

```bash
while true; do
    read -p "Ingresa un comando (quit para salir): " comando

    if [ "$comando" = "quit" ]; then
        break
    fi

    echo "Ejecutaste: $comando"
done
```

¿Qué hace?

1. `while true`: bucle infinito. `true` siempre devuelve 0 (verdadero).
2. `read -p ...`: solicita al usuario que escriba un comando.
3. `if [ "$comando" = "quit" ]`: si el usuario escribe `quit`...
4. `break`: ...sale del bucle.
5. Si no, se imprime el comando ejecutado.

Ejemplo de ejecución:

```plain text
Ingresa un comando (quit para salir): hola
Ejecutaste: hola
Ingresa un comando (quit para salir): ls
Ejecutaste: ls
Ingresa un comando (quit para salir): quit
```

### Ejemplo 3: leer archivo línea por línea

```bash
while IFS= read -r linea; do
    echo "Procesando: $linea"
done < archivo.txt
```

¿Qué hace?

1. `IFS=`: desactiva el separador de campos para preservar los espacios al principio/final.
2. `read -r linea`: lee una línea del archivo y la guarda en la variable `linea`, sin interpretar barras invertidas (`-r`).
3. `echo`: imprime la línea procesada.
4. `< archivo.txt`: redirige el archivo como entrada estándar.

Resultado (si `archivo.txt` tiene 3 líneas):

```plain text
Procesando: línea 1
Procesando: línea 2
Procesando: línea 3
```

### Ejemplo 4: verificar si un proceso está activo

```bash
proceso="nginx"
while pgrep "$proceso" > /dev/null; do
    echo "$proceso está corriendo"
    sleep 5
done
echo "$proceso se ha detenido"
```

¿Qué hace?

1. `proceso="nginx"`: define el nombre del proceso a monitorear.
2. `pgrep "$proceso"`: busca procesos que coincidan con el nombre. Si encuentra alguno, devuelve 0 (verdadero).
3. `> /dev/null`: oculta la salida de `pgrep`.
4. `sleep 5`: espera 5 segundos antes de la siguiente verificación.
5. Si `pgrep` no encuentra el proceso, el bucle termina y se imprime que se ha detenido.

Ejemplo de ejecución:

```plain text
nginx está corriendo
nginx está corriendo
...
nginx se ha detenido
```

Cuando el proceso `nginx` deja de correr, el bucle se rompe.

### Resumen general

| Parte | Significado |
|---|---|
| `while [ condición ]; do` | Ejecuta comandos mientras la condición sea verdadera |
| `while true; do` | Bucle infinito |
| `read` | Lee entrada del usuario o archivo |
| `break` | Sale del bucle prematuramente |
| `pgrep` | Verifica si un proceso está en ejecución |
| `IFS= read -r` | Lee líneas de archivo con formato preservado |

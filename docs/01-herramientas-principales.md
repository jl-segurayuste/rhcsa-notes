# Conocimiento y uso de las herramientas principales

## Acceda a un intérprete de comandos de shell y escriba comandos con la sintaxis correcta

1. **Acceder al intérprete de comandos:**
   - En la mayoría de los sistemas basados en Linux, puedes acceder a un intérprete de comandos (generalmente Bash) de varias maneras, como:
     - Abrir una terminal desde un entorno gráfico.
     - Iniciar sesión en un sistema de forma remota a través de SSH.
     - Usar la consola en sistemas sin entorno gráfico.
2. **Sintaxis básica de un comando:**
   - Un comando típico tiene la siguiente estructura:
     ```shell
     comando [opciones] [argumentos]
     ```
   - Por ejemplo, para listar archivos en un directorio, puedes usar el comando `ls`:
     ```shell
     ls
     ```
3. **Redirección de entrada-salida:**
   - Puedes redirigir la entrada y salida de comandos utilizando símbolos como `>`, `>>`, `|`, `2>`, etc.
   - Ejemplo de redirección de salida a un archivo:
     ```shell
     ls > lista_archivos.txt
     ```
4. **Expresiones regulares y `grep`:**
   - Las expresiones regulares son patrones que describen conjuntos de cadenas de texto. `grep` se utiliza para buscar patrones en archivos.
   - Ejemplo de búsqueda de un patrón en un archivo:
     ```shell
     grep "patron" archivo.txt
     ```
5. **Acceder a sistemas remotos con SSH:**
   - Para acceder a un sistema remoto de forma segura, puedes utilizar SSH.
   - Ejemplo de conexión SSH a un servidor:
     ```shell
     ssh usuario@direccion_ip
     ```
6. **Cambiar de usuario en destinos multiusuario:**
   - Utiliza el comando `su` para cambiar de usuario.
   - Ejemplo de cambiar a otro usuario:
     ```shell
     su - otro_usuario
     ```
7. **Archivar, comprimir, desempaquetar y descomprimir archivos:**
   - Utiliza comandos como `tar`, `gzip`, `bzip2` para trabajar con archivos.
   - Ejemplo de crear un archivo comprimido:
     ```shell
     tar czvf archivo.tar.gz directorio/
     ```
8. **Crear y editar archivos de texto:**
   - Utiliza editores de texto como `nano`, `vim` o `gedit` para crear o editar archivos.
   - Ejemplo de crear un archivo con `nano`:
     ```shell
     nano nombre_archivo.txt
     ```
9. **Crear, borrar, copiar y mover archivos y directorios:**
   - Utiliza comandos como `touch`, `rm`, `cp`, `mv`.
   - Ejemplo de copiar un archivo:
     ```shell
     cp origen.txt destino/
     ```
10. **Crear enlaces físicos y simbólicos:**
    - Usa `ln` para crear enlaces.
    - Ejemplo de crear un enlace simbólico:
      ```shell
      ln -s archivo enlace_simbolico
      ```
11. **Listar, configurar y cambiar permisos:**
    - Utiliza `ls` para listar archivos y `chmod` para cambiar permisos.
    - Ejemplo de cambiar permisos:
      ```shell
      chmod 755 archivo
      ```
12. **Localizar, leer y utilizar documentación del sistema:**
    - Utiliza comandos como `man`, `info`, y explora archivos en `/usr/share/doc`.
    - Ejemplo de leer la página de manual de un comando:
      ```shell
      man nombre_comando
      ```

Recuerda practicar estos comandos en un entorno de laboratorio para fortalecer tus habilidades.

## Utilice redirección de entrada-salida (`>`, `>>`, `|`, `2>`, etc.)

La redirección de entrada-salida es una técnica importante en la línea de comandos de Linux para manejar flujos de datos. Aquí tienes ejemplos de cómo utilizar diferentes operadores de redirección:

1. **`>` (Redirección de salida):**
   - Este operador se utiliza para redirigir la salida de un comando hacia un archivo, sobrescribiendo el contenido existente o creando uno nuevo si no existe.
     ```shell
     ls > lista_archivos.txt
     ```
     Esto creará un archivo llamado `lista_archivos.txt` que contendrá la lista de archivos en el directorio.
2. **`>>` (Redirección de salida al final del archivo):**
   - Similar a `>`, pero agrega la salida al final del archivo en lugar de sobrescribirlo.
     ```shell
     echo "Nuevo contenido" >> archivo_existente.txt
     ```
     Esto agregará la línea "Nuevo contenido" al final del archivo `archivo_existente.txt`.
3. **`|` (Tubería, Pipe):**
   - Permite enviar la salida de un comando como entrada a otro.
     ```shell
     ls | grep "patron"
     ```
     Esto mostrará solo las líneas que contienen el "patron" en la salida del comando `ls`.
4. **`2>` (Redirección de salida de error):**
   - Redirige los mensajes de error a un archivo en lugar de la salida estándar.
     ```shell
     comando_que_genera_error 2> errores.txt
     ```
     Esto redirigirá los mensajes de error del comando al archivo `errores.txt`.
5. **`&>` (Redirección de salida estándar y error):**
   - Combina la salida estándar y los mensajes de error en un solo archivo.
     ```shell
     comando &> salida_y_errores.txt
     comando > salida_y_errores.txt 2>&1
     ```
     Esto redirigirá tanto la salida estándar como los mensajes de error del comando al archivo `salida_y_errores.txt`.
6. **`<` (Redirección de entrada):**
   - Utilizado para tomar la entrada desde un archivo en lugar del teclado.
     ```shell
     comando < entrada.txt
     ```
     Esto enviará el contenido del archivo `entrada.txt` como entrada al comando.
7. **`&>>` (Redirección de salida estándar y error al final del archivo):**
   - Similar a `&>`, pero agrega la salida y los mensajes de error al final del archivo en lugar de sobrescribirlo.
     ```shell
     comando &>> salida_y_errores.txt
     ```
     Esto agregará tanto la salida estándar como los mensajes de error del comando al final del archivo `salida_y_errores.txt`.
8. **`| tee` (Redirección y visualización):**
   - Permite enviar la salida de un comando a un archivo y también visualizarla en la pantalla.
     ```shell
     ls | tee lista_archivos.txt
     ```
     Esto mostrará la lista de archivos en la pantalla y la guardará en el archivo `lista_archivos.txt`.
9. **`< <(comando)` (Redirección desde la salida de un comando):**
   - Utilizado para redirigir la salida de un comando como entrada para otro.
     ```shell
     grep "patron" < <(cat archivo.txt)
     ```
     Esto buscará el "patron" en el contenido del archivo utilizando `grep`.
10. **`<<` (Redirección here document):**
    - Permite ingresar varias líneas de entrada directamente en el terminal.
      ```shell
      cat <<FIN
      Línea 1
      Línea 2
      FIN
      ```
      Esto mostrará las líneas "Línea 1" y "Línea 2" en la salida del comando `cat`.

Tabla de descriptores de archivo estándar (fundamentales para la redirección y el manejo de errores en sistemas tipo Unix):

| Nombre | Destino Predeterminado | Uso en Redirección | Número de Descriptor de Archivo |
| --- | --- | --- | --- |
| STDIN | Teclado de la computadora | `<` (igual que `0<`) | 0 |
| STDOUT | Monitor de la computadora | `>` (igual que `1>`) | 1 |
| STDERR | Monitor de la computadora | `2>` | 2 |

- **STDIN (Entrada Estándar)**: Por defecto, está asociado al teclado de la computadora. En la redirección, `<` se utiliza para redirigir la entrada desde un archivo o comando. Su número de descriptor de archivo es 0.
- **STDOUT (Salida Estándar)**: Por defecto, está asociado al monitor (o terminal) de la computadora. En la redirección, `>` se utiliza para redirigir la salida a un archivo. Su número de descriptor de archivo es 1.
- **STDERR (Error Estándar)**: Al igual que STDOUT, también está asociado al monitor de la computadora. Sin embargo, maneja específicamente los mensajes de error. `2>` se utiliza para redirigir la salida de error a un archivo. Su número de descriptor de archivo es 2.

Estos descriptores son fundamentales para administrar las secuencias de entrada/salida y el manejo de errores en entornos tipo Unix. La redirección permite a los usuarios controlar de dónde proviene la entrada y a dónde van los mensajes de salida/error.

## Utilice expresiones regulares y grep para analizar textos

Las expresiones regulares y el comando `grep` son herramientas poderosas para buscar y analizar texto en archivos o flujos de datos. Aquí tienes algunos ejemplos:

1. **Búsqueda básica con `grep`:**
   - Buscar una cadena específica en un archivo:
     ```shell
     grep "patron" archivo.txt
     ```
     Esto mostrará las líneas que contienen el "patrón" en el archivo.
2. **Expresiones regulares básicas:**
   - Buscar líneas que comiencen con una palabra específica:
     ```shell
     grep "^inicio" archivo.txt
     ```
     Aquí, `^` denota el comienzo de la línea.
   - Buscar líneas que terminen con una palabra específica:
     ```shell
     grep "final$" archivo.txt
     ```
     Aquí, `$` denota el final de la línea.
   - Buscar líneas que contengan una palabra específica como palabra completa:
     ```shell
     grep "\<palabra\>" archivo.txt
     ```
     Aquí, `\<` y `\>` indican límites de palabra.
3. **Buscar líneas que no coincidan con un patrón:**
   ```shell
   grep -v "patron" archivo.txt
   ```
   Esto mostrará las líneas que no contienen el "patrón".
4. **Contar ocurrencias de un patrón:**
   ```shell
   grep -c "patron" archivo.txt
   ```
   Esto mostrará el número total de líneas que contienen el "patrón".
5. **Buscar en varios archivos:**
   ```shell
   grep "patron" archivo1.txt archivo2.txt
   ```
   Esto buscará el "patrón" en ambos archivos.
6. **Búsqueda recursiva en directorios:**
   ```shell
   grep -r "patron" directorio/
   ```
   Esto buscará de manera recursiva el "patrón" en todos los archivos dentro del directorio.

### Clases de caracteres POSIX

Las clases de caracteres POSIX son una característica de las expresiones regulares y se utilizan para definir conjuntos de caracteres de manera más intuitiva y fácil de entender. Estas clases son especialmente útiles en scripts de shell y herramientas de línea de comandos como `grep`, `sed`, y en las estructuras `case` de `bash`.

**Clase de caracteres `[:alpha:]`**: representa todas las letras del alfabeto, tanto en mayúsculas como en minúsculas (de `a` a `z` y de `A` a `Z`).

**Ejemplo básico con `grep`:**

```shell
grep '[[:alpha:]]' archivo.txt
```

**Ejemplo en `bash`:**

```bash
#!/bin/bash
echo "Introduce un carácter:"
read caracter

case "$caracter" in
  [[:alpha:]])
    echo "El carácter $caracter es una letra."
    ;;
  *)
    echo "El carácter $caracter no es una letra."
    ;;
esac
```

**Otras clases de caracteres POSIX:**

- `[:alnum:]`: Alfanuméricos (letras y dígitos).
- `[:digit:]`: Dígitos (`0` a `9`).
- `[:lower:]`: Letras minúsculas (`a` a `z`).
- `[:upper:]`: Letras mayúsculas (`A` a `Z`).
- `[:space:]`: Espacios en blanco (incluye espacio, tabulador, nueva línea, etc.).
- `[:punct:]`: Signos de puntuación.
- `[:xdigit:]`: Dígitos hexadecimales (`0`-`9`, `A`-`F`, `a`-`f`).

**Ejemplo extendido con varias clases:**

```bash
#!/bin/bash
echo "Introduce un carácter:"
read caracter

case "$caracter" in
  [[:alpha:]])
    echo "El carácter $caracter es una letra."
    ;;
  [[:digit:]])
    echo "El carácter $caracter es un dígito."
    ;;
  [[:space:]])
    echo "El carácter $caracter es un espacio en blanco."
    ;;
  [[:punct:]])
    echo "El carácter $caracter es un signo de puntuación."
    ;;
  *)
    echo "El carácter $caracter es otro tipo de símbolo."
    ;;
esac
```

**¿Por qué usar clases de caracteres POSIX?**

1. **Legibilidad**: hacen que las expresiones regulares sean más fáciles de leer y entender.
2. **Compatibilidad**: son estándar en POSIX, lo que asegura que funcionarán de manera consistente en diferentes sistemas y herramientas.
3. **Flexibilidad**: permiten definir patrones complejos de forma concisa.

El uso de `[:alpha:]` y otras clases de caracteres POSIX en `bash` y otras herramientas de línea de comandos permite escribir scripts más claros y robustos.

## Acceda a los sistemas remotos con SSH

1. **Sintaxis básica de conexión SSH:**
   ```shell
   ssh usuario@direccion_ip
   ```
   Donde `usuario` es tu nombre de usuario en el sistema remoto, y `direccion_ip` es la dirección IP del sistema al que deseas conectarte.
2. **Especificar un puerto diferente:**
   - Por defecto, SSH utiliza el puerto 22. Si el sistema remoto utiliza un puerto diferente, especifica el puerto con la opción `-p`:
     ```shell
     ssh -p puerto usuario@direccion_ip
     ```
3. **Autenticación mediante clave SSH:**
   ```shell
   ssh -i ruta_a_la_clave_privada usuario@direccion_ip
   ```
4. **Reenvío de agentes SSH:**
   ```shell
   ssh -A usuario@direccion_ip
   ```
5. **Ejecutar comandos remotos:**
   ```shell
   ssh usuario@direccion_ip "comando_a_ejecutar"
   ```
6. **Cerrar la conexión SSH:**
   ```shell
   exit
   ```
7. **Configuración de archivos de configuración SSH:**
   - Puedes configurar opciones de conexión SSH en el archivo `~/.ssh/config`. Esto puede ser útil para evitar la entrada repetitiva de opciones en la línea de comandos.

Recuerda que la seguridad es crucial al trabajar con SSH. Asegúrate de mantener tus claves privadas seguras y sigue las mejores prácticas de seguridad, como deshabilitar el acceso de root directo y mantener el software SSH actualizado.

| Opción | Uso |
| --- | --- |
| `-v` | Verbose; muestra en detalle lo que está ocurriendo mientras se establece la conexión. |
| `-Y` | Habilita el soporte para aplicaciones gráficas (X11 forwarding). |
| `-p <PORT>` | Utilizado para conectarse a un servicio SSH que no está escuchando en el puerto por defecto 22. |

**Explicaciones detalladas:**

1. **`-v` (Verbose)**: activa el modo detallado. Esto hace que `ssh` imprima a la consola mensajes detallados sobre el proceso de la conexión, útil para diagnosticar problemas de conexión, autenticación y configuración. Puedes incrementar la verbosidad usando `-vv` o `-vvv`.
2. **`-Y` (X11 Forwarding)**: activa el reenvío seguro de X11, protocolo que permite ejecutar aplicaciones gráficas en un servidor remoto mientras se visualiza la interfaz gráfica localmente. Es una opción menos restrictiva que `-X`, ya que desactiva algunas comprobaciones de seguridad.
3. **`-p <PORT>`**: normalmente el servicio SSH escucha en el puerto 22 por defecto. Si un servidor SSH escucha en un puerto diferente, esta opción permite especificarlo.

## Inicie sesión y cambie de usuario en destinos multiusuario

1. **Iniciar sesión con un usuario diferente:**
   ```shell
   ssh usuario@direccion_ip
   ```
   Ingresa la contraseña cuando se te solicite.
2. **Cambiar de usuario dentro de la sesión:**
   ```shell
   su - otro_usuario
   ```
   El flag `-` (o `--login`) indica que se deben cargar las variables de entorno del usuario al que estás cambiando, lo cual es útil para obtener un entorno de shell completo como el del usuario. También puedes usar el equivalente:
   ```shell
   su -l otro_usuario
   ```
3. **Cambiar de usuario sin cerrar sesión:**
   ```shell
   su otro_usuario
   ```
4. **Cerrar sesión de usuario actual:**
   ```shell
   exit
   ```
5. **Ejecutar comandos como otro usuario sin cambiar completamente:**
   ```shell
   sudo -u otro_usuario comando_a_ejecutar
   ```
   Esto te pedirá la contraseña del usuario actual (el que está utilizando `sudo`).

Recuerda que para realizar algunas de estas acciones, tu usuario actual puede necesitar privilegios específicos, como pertenecer al grupo `sudo`. Además, el uso de `sudo` puede requerir configuración específica en el sistema.

## Archive, comprima, desempaquete y descomprima archivos utilizando tar, gzip y bzip2

1. **Archivar archivos con `tar`:**
   ```shell
   tar -cvf archivo_respaldo.tar directorio/
   ```
   Donde:
   - `c`: Crea un nuevo archivo.
   - `v`: Muestra la información detallada de los archivos mientras se crea el archivo.
   - `f`: Especifica el nombre del archivo.
2. **Comprimir archivos con `gzip`:**
   ```shell
   gzip archivo_respaldo.tar
   ```
   Esto creará un archivo comprimido llamado `archivo_respaldo.tar.gz` y eliminará el archivo tar original.
3. **Descomprimir archivos con `gzip`:**
   ```shell
   gzip -d archivo_respaldo.tar.gz
   ```
4. **Comprimir archivos con `bzip2`:**
   ```shell
   bzip2 archivo_respaldo.tar
   ```
   Esto creará un archivo comprimido llamado `archivo_respaldo.tar.bz2` y eliminará el archivo tar original.
5. **Descomprimir archivos con `bzip2`:**
   ```shell
   bzip2 -d archivo_respaldo.tar.bz2
   ```

Es importante mencionar que al utilizar `tar`, el orden de las opciones (`cvf` o `xvf`, por ejemplo) es crucial. La secuencia `-zxvf` puede usarse si quieres descomprimir y extraer al mismo tiempo.

Recuerda ajustar los nombres de archivos y directorios según tus necesidades específicas. Además, ten cuidado al sobrescribir archivos existentes, ya que estas operaciones pueden modificar o eliminar archivos.

## Cree y edite archivos de texto

### Usando `nano`

1. **Crear un nuevo archivo con `nano`:**
   ```shell
   nano nombre_archivo.txt
   ```
2. **Editar un archivo existente con `nano`:**
   ```shell
   nano nombre_archivo.txt
   ```
3. **Comandos básicos en `nano`:**
   - `Ctrl + O`: Guardar el archivo.
   - `Ctrl + X`: Salir de `nano`.

### Usando `vi`

1. **Crear un nuevo archivo con `vi`:**
   ```shell
   vi nombre_archivo.txt
   ```
2. **Editar un archivo existente con `vi`:**
   ```shell
   vi nombre_archivo.txt
   ```
3. **Comandos básicos en `vi`:**
   - Modo de inserción:
     - Presiona `i` para entrar al modo de inserción.
     - Escribe o edita el contenido del archivo.
     - Para salir del modo de inserción, presiona `Esc`.
   - Guardar y salir:
     - `:w` y presiona `Enter`: Guarda el archivo.
     - `:q` y presiona `Enter`: Sale del editor.
     - `:wq` o `ZZ`: Guarda y sale.

Estos son solo ejemplos básicos, y ambos editores tienen más funcionalidades. `nano` es más amigable para principiantes, mientras que `vi` puede ser más poderoso una vez que te familiarizas con su modo de comandos.

## Cree, borre, copie y mueva archivos y directorios

### Crear archivos y directorios

1. **Crear un archivo vacío con `touch`:**
   ```shell
   touch nombre_archivo.txt
   ```
2. **Crear un directorio con `mkdir`:**
   ```shell
   mkdir nombre_directorio
   ```

### Eliminar archivos y directorios

1. **Eliminar un archivo con `rm`:**
   ```shell
   rm nombre_archivo.txt
   ```
   Ten cuidado, ya que este comando es irreversible.
2. **Eliminar un directorio con `rmdir` o `rm`:**
   ```shell
   rmdir nombre_directorio
   ```
   Esto eliminará el directorio si está vacío. Para eliminar directorios no vacíos, usa `rm` con la opción `-r` (recursivo). Ten cuidado al usar `-r` para evitar la eliminación accidental de datos importantes.
   ```shell
   rm -r nombre_directorio
   ```

### Copiar archivos y directorios

1. **Copiar un archivo con `cp`:**
   ```shell
   cp archivo_origen.txt archivo_destino.txt
   ```
2. **Copiar un directorio con `cp` (recursivo):**
   ```shell
   cp -r directorio_origen/ directorio_destino/
   ```

### Mover archivos y directorios

1. **Mover un archivo con `mv`:**
   ```shell
   mv archivo_origen.txt directorio_destino/
   ```
2. **Mover un directorio con `mv`:**
   ```shell
   mv directorio_origen/ directorio_destino/
   ```

Ten cuidado al utilizar comandos de eliminación (`rm`), especialmente en directorios, para evitar pérdida de datos accidental.

## Cree enlaces físicos y simbólicos

### Enlace físico

Un enlace físico es una referencia adicional a un archivo existente. Los enlaces físicos comparten el mismo inodo y, por lo tanto, la misma información de contenido en disco.

> **Nota:** El "inodo" (inode) es una estructura de datos en sistemas de archivos Unix y Linux que almacena información importante sobre un archivo o directorio. Cada archivo o directorio tiene su propio inodo único, que contiene información fundamental pero no el nombre del archivo ni su ubicación. La información almacenada en un inodo incluye:
>
> 1. **Número de Inodo (inode number):** identificador único asignado al inodo.
> 2. **Tipo de Archivo:** indica si es un archivo regular, un directorio, un enlace simbólico, etc.
> 3. **Permisos:** información sobre los permisos del archivo.
> 4. **Dueño y Grupo:** identificación del usuario y grupo propietarios del archivo.
> 5. **Tamaño del Archivo:** tamaño en bytes del archivo.
> 6. **Fecha y Hora de Creación/Modificación/Último Acceso.**
> 7. **Número de Enlaces Duros:** indica cuántos enlaces duros apuntan al mismo inodo. Cuando este número llega a cero, el sistema operativo libera el espacio y elimina el inodo.
> 8. **Bloques de Datos Directos, Indirectos y Doblemente Indirectos:** almacenan direcciones de bloques de datos que contienen el contenido real del archivo.
>
> La existencia de los inodos permite la implementación eficiente de enlaces duros, ya que varios nombres (o rutas) pueden apuntar al mismo inodo. Cuando accedes a un archivo, el sistema operativo utiliza el número de inodo para localizar y acceder a la información del archivo en la tabla de inodos.

1. **Crear un enlace físico con `ln`:**
   ```shell
   ln archivo_existente.txt enlace_fisico.txt
   ```
   Esto creará un enlace físico llamado `enlace_fisico.txt` que apunta al mismo contenido que `archivo_existente.txt`.

> **Atención:** Los enlaces físicos se crean solo para archivos, no para directorios.

### Enlace simbólico

Un enlace simbólico, también conocido como symlink o soft link, es un tipo de enlace que contiene la ruta del archivo al que apunta.

1. **Crear un enlace simbólico con `ln` y la opción `-s`:**
   ```shell
   ln -s archivo_existente.txt enlace_simbolico.txt
   ```

> **Atención:** Al contrario de los enlaces duros, si eliminas el enlace simbólico, el archivo original no se ve afectado. Sin embargo, si eliminas el archivo original, el enlace simbólico apuntará a un destino inexistente.

### Verificación

Puedes verificar los enlaces existentes y sus ubicaciones utilizando el comando `ls` con la opción `-l` (long listing):

```shell
ls -l
```

Esto mostrará información detallada sobre los archivos, incluyendo el número de enlaces (columna `Links`) y la ruta del enlace simbólico, si lo hay.

### Notas adicionales

- Al eliminar un enlace físico o un enlace simbólico, el archivo original no se elimina a menos que sea el último enlace que hace referencia a ese inodo específico.
- Los enlaces físicos no pueden atravesar sistemas de archivos, mientras que los enlaces simbólicos pueden referenciar archivos en diferentes sistemas de archivos.
- Los enlaces simbólicos son útiles cuando necesitas crear referencias a archivos en ubicaciones diferentes o cuando trabajas con directorios.

## Enumere, configure y cambie permisos ugo/rwx estándares

En sistemas Unix/Linux, la gestión de permisos es crucial para controlar el acceso a archivos y directorios. Los permisos están asociados con usuarios (u), grupos (g) y otros (o), y se definen en términos de leer (r), escribir (w) y ejecutar (x).

### Enumerar permisos existentes

1. **Utilizar `ls` con la opción `-l` para obtener una lista detallada de archivos y directorios:**
   ```shell
   ls -l
   ```
   La salida mostrará permisos, propietario, grupo, tamaño, etc.

### Configurar permisos utilizando `chmod`

1. **Configurar permisos utilizando el formato ugo/rwx:**
   ```shell
   chmod u+rwx,g+rw,o+r archivo_o_directorio
   ```
   Esto agrega permisos de lectura, escritura y ejecución para el usuario, lectura y escritura para el grupo, y solo lectura para otros.
2. **Usar números octales para establecer permisos de manera más compacta:**
   - **r (read) = 4, w (write) = 2, x (execute) = 1.**
   - Suma estos valores para establecer permisos.
   - Ejemplo: `chmod 754 archivo_o_directorio` establecerá permisos como `rwxr-xr--`.

### Cambiar permisos de manera recursiva con `chmod`

1. **Cambiar permisos de manera recursiva en un directorio y sus subdirectorios:**
   ```shell
   chmod -R ugo+rwx directorio
   ```
   Esto cambia los permisos para el directorio y todos sus contenidos de manera recursiva.

### Notas adicionales

- Para cambiar el propietario o el grupo de un archivo o directorio, puedes usar `chown` y `chgrp` respectivamente.
  ```shell
  chown nuevo_propietario:nuevo_grupo archivo_o_directorio
  ```
- Ten cuidado al otorgar permisos, ya que proporcionar permisos excesivos puede comprometer la seguridad del sistema.
- Utiliza la opción `-R` con precaución, ya que puede modificar muchos archivos y directorios a la vez.

### Permisos avanzados

- **SUID (Set User ID)**: permite a los usuarios ejecutar un archivo con los permisos del propietario del archivo. Se representa con el valor numérico 4. Para aplicarlo: `chmod u+s`.
  - Ejemplo: para que cualquier usuario que ejecute `programa` lo haga con los permisos del propietario del archivo:
    ```bash
    chmod u+s programa
    ```
- **SGID (Set Group ID)**: permite a los usuarios ejecutar un archivo con los permisos del grupo del archivo. Se representa con el valor numérico 2. Para aplicarlo: `chmod g+s`.
  - Ejemplo: para que todos los archivos creados dentro de `directorio` pertenezcan al mismo grupo que el directorio:
    ```bash
    chmod g+s directorio
    ```
- **Sticky Bit**: restringe la eliminación de archivos en un directorio. Solo el propietario del archivo puede eliminarlo. Se representa con el valor numérico 1. Para aplicarlo: `chmod +t`.
  - Ejemplo: para que solo el propietario de un archivo dentro de `directorio_compartido` pueda eliminarlo:
    ```bash
    chmod +t directorio_compartido
    ```

Para aplicar estos permisos, necesitas agregar un argumento de cuatro dígitos a `chmod`, donde el primer dígito se refiere a los permisos especiales. Por ejemplo, `chmod 2755 /somedir` añadiría el permiso SGID a un directorio, y establecería rwx para el usuario y rx para el grupo y otros.

## Localice, lea y utilice la documentación del sistema (man, info y /usr/share/doc)

### `man` (Manual)

1. **Consultar el manual de un comando:**
   ```shell
   man nombre_comando
   ```
   Reemplaza `nombre_comando` con el comando para el que deseas consultar el manual. Puedes navegar por el manual usando las teclas de dirección y salir presionando `q`.
2. **Buscar en el manual:**
   ```shell
   man -k palabra_clave
   ```
   Esto buscará la palabra clave en la base de datos de manuales y mostrará los resultados relacionados. Por ejemplo:
   ```shell
   man -k grep
   ```
   Podría devolver algo como:
   ```shell
   egrep (1)            - print lines matching a pattern
   fgrep (1)            - print lines matching a pattern
   grep (1)             - print lines matching a pattern
   zgrep (1)            - compressing and decompressing gzipped files
   zfgrep (1)           - compressing and decompressing gzipped files
   ```
   Esto indica que hay varios comandos relacionados con la palabra clave "grep". Puedes usar `man` seguido del nombre del comando para obtener más información sobre cada uno de ellos, por ejemplo `man grep`.

### `info`

1. **Consultar información de un comando:**
   ```shell
   info nombre_comando
   ```
   Similar a `man`, `info` proporciona información detallada sobre el comando. Puedes navegar utilizando las teclas de dirección y salir presionando `q`.

### Archivos en `/usr/share/doc`

1. **Explorar la documentación en `/usr/share/doc`:**
   ```shell
   ls /usr/share/doc
   ```
   Esto listará el contenido del directorio. Muchas veces, la documentación de los paquetes instalados se encuentra aquí en formato de archivos de texto, HTML u otros.
2. **Leer archivos de documentación:**
   ```shell
   less /usr/share/doc/nombre_paquete/nombre_archivo
   ```
   Reemplaza `nombre_paquete` y `nombre_archivo` con el paquete y archivo específico que deseas leer.
3. **Abrir archivos de documentación con un visor de documentos:**
   - Algunos paquetes pueden incluir documentación en formato HTML o PDF. Puedes abrirlos con un visor de documentos como `xdg-open` o `evince`.

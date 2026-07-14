# Umask para directorios y diferente para ficheros

La lógica que plantea este **script** para diferenciar entre valores de `umask` para archivos y directorios tiene un pequeño inconveniente: **`umask` no distingue entre la creación de archivos y directorios**. Sin embargo, se puede ajustar el enfoque para intentar lograr algo similar.

## Corrección y explicación del script

El propósito del script es aplicar un `umask` diferente dependiendo de si se está creando un **archivo** o un **directorio**. Sin embargo, **`umask` no tiene un estado asociado a si el objeto es un archivo o directorio** en tiempo real. Dicho esto, la lógica debe basarse en intervenir en el momento de ejecutar comandos como `touch` o `mkdir`.

## Ajuste del script

Dado que `umask` no permite diferenciar entre archivos y directorios automáticamente, se puede usar un enfoque como este:

1. **Sobrescribir comandos como `mkdir` y `touch`**: se pueden interceptar los comandos de creación usando funciones en el shell.

2. **Script ajustado**:

   ```bash
   # Define umask para archivos y directorios
   UMASK_FILES=026   # Archivos: rw-r-----
   UMASK_DIRS=027    # Directorios: rwxr-x---

   # Función personalizada para mkdir
   function mkdir {
       local prev_umask=$(umask)  # Guarda el umask actual
       umask $UMASK_DIRS          # Aplica umask para directorios
       command mkdir "$@"         # Llama al comando mkdir original
       umask $prev_umask          # Restaura el umask original
   }

   # Función personalizada para touch
   function touch {
       local prev_umask=$(umask)  # Guarda el umask actual
       umask $UMASK_FILES         # Aplica umask para archivos
       command touch "$@"         # Llama al comando touch original
       umask $prev_umask          # Restaura el umask original
   }
   ```

3. **Guardar el script**: coloca este código en el archivo `~/.bashrc` del usuario que necesita esta configuración.

4. **Aplicar cambios**: aplica el script con:

   ```bash
   source ~/.bashrc
   ```

## Prueba del script

### Crear un archivo

```bash
touch testfile
ls -l testfile
```

Debería tener los permisos:

```bash
-rw-r----- 1 user group 0 <fecha> testfile
```

### Crear un directorio

```bash
mkdir testdir
ls -ld testdir
```

Debería tener los permisos:

```bash
drwxr-x--- 2 user group 4096 <fecha> testdir
```

## Consideraciones

1. **Limitaciones de `umask`**:
   - No tiene soporte nativo para diferenciar entre tipos de objetos creados.
   - Esta solución se basa en interceptar comandos comunes; si el usuario usa otros métodos para crear archivos/directorios, como un script o programa en C, esta lógica no aplicará.
2. **Aplicación global**:
   - Para configurar esto a nivel global, se puede modificar `/etc/bashrc`, pero hay que tener en cuenta que esto afecta a todos los usuarios.
3. **SELinux**:
   - Si se trabaja en un entorno con SELinux habilitado, verifica que los contextos de seguridad sean consistentes con las políticas aplicadas.

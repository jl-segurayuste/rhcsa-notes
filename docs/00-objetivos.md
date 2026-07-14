# Objetivos del Examen RHCSA

Documento de objetivos oficiales del examen RHCSA 8 (Red Hat Certified System Administrator).

## 1. Conocimiento y uso de las herramientas principales

1. **Acceso y comandos en el intérprete de comandos de shell**
   - Acceso a un intérprete de comandos de shell.
   - Escritura de comandos con la sintaxis correcta.
2. **Redirección de entrada y salida**
   - Uso de redirección de entrada-salida (`>`, `>>`, `|`, `2>`, etc.).
3. **Expresiones regulares y grep**
   - Utilización de expresiones regulares y `grep` para analizar textos.
4. **Acceso a sistemas remotos con SSH**
   - Conexión a sistemas remotos mediante SSH.
5. **Inicio de sesión y cambio de usuario**
   - Inicio de sesión en destinos multiusuario.
   - Cambio de usuario en destinos multiusuario.
6. **Archivado y compresión de archivos**
   - Archivado, compresión, desempaquetado y descompresión de archivos utilizando `tar`, `gzip` y `bzip2`.
7. **Edición de archivos de texto**
   - Creación y edición de archivos de texto.
8. **Gestión de archivos y directorios**
   - Creación, borrado, copia y movimiento de archivos y directorios.
   - Creación de enlaces físicos y simbólicos.
9. **Gestión de permisos**
   - Listado, configuración y cambio de permisos `ugo/rwx` estándares.
10. **Documentación del sistema**
    - Localización, lectura y utilización de la documentación del sistema, incluyendo `man`, `info` y archivos en `/usr/share/doc`.

## 2. Creación de scripts de shell sencillos

1. **Ejecución condicional de código**
   - Uso de `if`, `test`, `[]`, etc.
2. **Estructuras de bucle**
   - Uso de estructuras de bucle (`for`, etc.) para procesar la entrada de línea de comandos y archivos.
3. **Procesamiento de entradas de scripts**
   - Procesamiento de entradas de scripts (`$1`, `$2`, etc.).
4. **Procesamiento de la salida de comandos**
   - Procesamiento de la salida de los comandos de shell dentro de un script.

## 3. Uso de sistemas en funcionamiento

1. **Gestión del ciclo de vida del sistema**
   - Arranque, reinicio y apagado normal del sistema.
   - Inicio manual de sistemas en destinos diferentes.
   - Interrupción del proceso de arranque para obtener acceso a un sistema.
2. **Gestión de procesos**
   - Identificación y eliminación de procesos con uso intensivo de CPU y memoria.
   - Ajuste de la programación de los procesos.
   - Gestión de perfiles de ajuste.
3. **Gestión de registros y servicios**
   - Localización e interpretación de diarios y archivos de registro del sistema.
   - Conservación de los diarios del sistema.
   - Inicio, detención y verificación del estado de los servicios de red.
   - Transferencia segura de archivos entre diferentes sistemas.

## 4. Configuración del almacenamiento local

1. **Gestión de particiones**
   - Listado, creación y eliminación de particiones en discos MBR y GPT.
2. **Gestión de volúmenes físicos y lógicos**
   - Creación y eliminación de volúmenes físicos.
   - Asignación de volúmenes físicos a grupos de volúmenes.
   - Creación y eliminación de volúmenes lógicos.
3. **Configuración del montaje de sistemas de archivos**
   - Configuración de sistemas para montar sistemas de archivos durante el arranque usando UUID o etiquetas.
   - Adición de particiones y volúmenes lógicos nuevos de manera no destructiva.

## 5. Creación y configuración de sistemas de archivos

1. **Gestión de sistemas de archivos**
   - Creación, montaje, desmontaje y uso de sistemas de archivos `vfat`, `ext4` y `xfs`.
   - Montaje y desmontaje de sistemas de archivos de red utilizando NFS.
2. **Configuración de autofs y ampliación de volúmenes**
   - Configuración de `autofs`.
   - Ampliación de volúmenes lógicos actuales.
3. **Gestión de directorios colaborativos**
   - Creación y configuración de directorios con GID definido para la colaboración.
   - Diagnóstico y corrección de problemas de permisos de archivos.

## 6. Implementación, configuración y mantenimiento de sistemas

1. **Programación de tareas**
   - Programación de tareas con `at` y `cron`.
2. **Gestión de servicios y configuración de arranque**
   - Inicio y detención de servicios.
   - Configuración de servicios para que se inicien automáticamente al arrancar.
   - Configuración de sistemas para que se inicien automáticamente en un destino específico.
   - Configuración de clientes de servicios de tiempo.
3. **Gestión de software y del cargador de arranque**
   - Instalación y actualización de paquetes de software desde Red Hat Network, un repositorio remoto o el sistema de archivos local.
   - Modificación del cargador de arranque del sistema.

## 7. Gestión de conexiones de red básicas

1. **Configuración de red**
   - Configuración de direcciones IPv4 e IPv6.
   - Configuración de la resolución de nombre de host.
   - Configuración de servicios de red para que se inicien automáticamente al arrancar.
   - Restricción del acceso a la red con un firewall o `firewall-cmd`.

## 8. Gestión de usuarios y grupos

1. **Gestión de cuentas de usuario**
   - Creación, borrado y modificación de cuentas de usuario locales.
   - Cambio de contraseñas y ajuste de su duración para las cuentas de usuario locales.
2. **Gestión de grupos**
   - Creación, borrado y modificación de grupos locales y la pertenencia a grupos.
3. **Acceso de superusuario**
   - Configuración del acceso de superusuario.

## 9. Gestión de la seguridad

1. **Gestión del firewall**
   - Establecimiento de ajustes de firewall con `firewall-cmd` o `firewalld`.
2. **Gestión de permisos y autenticación**
   - Gestión de permisos predeterminados de archivos.
   - Configuración de la autenticación basada en claves para SSH.
3. **Gestión de SELinux**
   - Establecimiento de modos de cumplimiento y permisivo para SELinux.
   - Listado e identificación del contexto de archivos y procesos de SELinux.
   - Restauración de contextos de archivos predeterminados.
   - Gestión de etiquetas de puerto SELinux.
   - Uso de configuraciones booleanas para modificar ajustes de SELinux del sistema.
   - Diagnóstico y abordaje de incumplimientos rutinarios de las políticas de SELinux.

## 10. Gestión de contenedores

1. **Gestión de imágenes en contenedores**
   - Búsqueda y extracción de imágenes en contenedores desde un registro remoto.
   - Examinación de imágenes en contenedores.
2. **Gestión de contenedores**
   - Gestión de contenedores con comandos como `Podman` y `Skopeo`.
   - Diseño de contenedores a partir de un archivo de contenedor.
   - Gestión básica de contenedores: ejecución, inicio, detención y registro de contenedores en funcionamiento.
   - Ejecución de un servicio dentro de un contenedor.
   - Configuración de un contenedor para que inicie automáticamente como un servicio `systemd`.
   - Asignación de almacenamiento permanente a un contenedor.

Este documento proporciona una guía completa de los objetivos y las habilidades que deben dominar los candidatos al examen RHCSA 8. Para una preparación detallada, se recomienda estudiar cada una de las áreas mencionadas y practicar las tareas específicas en un entorno de Red Hat Enterprise Linux 8.

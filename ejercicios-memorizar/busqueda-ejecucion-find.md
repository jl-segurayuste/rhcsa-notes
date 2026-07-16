# Búsqueda y ejecución con find

Para encontrar todos los archivos que son propiedad del usuario `edwin` y copiarlos al directorio `/rootedwinfiles`, podemos utilizar el comando `find` junto con `cp`. Aquí se explica cómo hacerlo paso a paso:

## Comando y explicación

### 1. Usando `find` para encontrar archivos propiedad de `edwin`

El comando `find` es muy útil para buscar archivos en base a diferentes criterios como el nombre del archivo, la propiedad del archivo, el tamaño, etc. En este caso, queremos encontrar archivos que sean propiedad del usuario `edwin`.

```bash
find / -type f -user edwin
```

- `find /`: especifica que la búsqueda comienza desde el directorio raíz (`/`). Puedes ajustar `/` por el directorio desde donde deseas iniciar la búsqueda.
- `-type f`: esta opción especifica que estamos buscando archivos regulares (no directorios ni enlaces simbólicos).
- `-user edwin`: este filtro limita la búsqueda a archivos que sean propiedad del usuario `edwin`.

### 2. Copiar los archivos encontrados al directorio `/rootedwinfiles`

Una vez que tenemos la lista de archivos que pertenecen a `edwin`, podemos copiarlos al directorio `/rootedwinfiles`. Aquí utilizaremos el comando `cp`.

```bash
find / -type f -user edwin -exec cp {} /rootedwinfiles \;
```

- `-exec cp {} /rootedwinfiles \;`: esto ejecuta el comando `cp` para cada archivo encontrado por `find`. `{}` es reemplazado por cada archivo encontrado y `\;` indica el final del comando `-exec`.

## Explicación del proceso

- **`find / -type f -user edwin`**: este comando busca todos los archivos regulares que pertenecen al usuario `edwin` dentro del sistema de archivos a partir del directorio raíz (`/`).
- **`-exec cp {} /rootedwinfiles \;`**: para cada archivo encontrado por `find`, `cp` lo copia al directorio `/rootedwinfiles`.

## Consideraciones

> **Atención — Permisos de ejecución:** asegúrate de tener los permisos adecuados para acceder a todos los archivos que son propiedad de `edwin` y para copiarlos al directorio `/rootedwinfiles`. Si ejecutas este comando como usuario diferente, es posible que no puedas acceder a todos los archivos.

> **Atención — Riesgos potenciales:** al copiar archivos del sistema, ten en cuenta que algunos archivos pueden ser críticos para el funcionamiento del sistema. Es importante entender qué archivos estás copiando y asegurarte de no afectar la integridad o la seguridad del sistema.

## Si el directorio no está creado

Si el directorio `/rootedwinfiles` no está creado y deseas copiar los archivos propiedad del usuario `edwin` a ese directorio, necesitas asegurarte de crear primero el directorio para evitar errores durante la operación de copia. Aquí se explica cómo hacerlo:

### Creación del directorio `/rootedwinfiles`

Para crear el directorio `/rootedwinfiles`, puedes usar el comando `mkdir` antes de ejecutar el comando `find` para buscar y copiar los archivos propiedad de `edwin`.

```bash
mkdir /rootedwinfiles
```

Este comando crea un directorio llamado `rootedwinfiles` en el directorio raíz (`/`). Puedes ajustar la ubicación de acuerdo a tu preferencia, por ejemplo, si deseas crearlo en un directorio diferente al raíz.

### Ejecutar el comando `find` y `cp`

Una vez que el directorio esté creado, puedes proceder a ejecutar el comando `find` para buscar los archivos y el comando `cp` para copiarlos al directorio recién creado.

```bash
find / -type f -user edwin -exec cp {} /rootedwinfiles \;
```

### Explicación del proceso

1. **Crear el directorio**: el comando `mkdir /rootedwinfiles` crea el directorio `/rootedwinfiles` si no existe.
2. **Buscar y copiar archivos**: después de crear el directorio, utilizas el comando `find` junto con `-exec cp` para buscar todos los archivos regulares (`-type f`) que son propiedad del usuario `edwin` (`-user edwin`) y copiarlos (`cp`) al directorio `/rootedwinfiles`.

## Crear el directorio dentro del propio comando find

Puedes incluir la creación del directorio `/rootedwinfiles` dentro del comando `find` utilizando la opción `-exec`:

```bash
find / -type f -user edwin -exec bash -c 'mkdir -p /rootedwinfiles && cp "$1" /rootedwinfiles' -- {} \;
```

### Explicación del comando

- **`find / -type f -user edwin`**: este comando `find` busca todos los archivos (`-type f`) que son propiedad del usuario `edwin` (`-user edwin`) en todo el sistema (`/`).
- **`-exec bash -c 'mkdir -p /rootedwinfiles && cp "$1" /rootedwinfiles' -- {} \;`**:
  - `-exec`: esta opción ejecuta el comando especificado después de `-exec`.
  - `bash -c '...'`: ejecuta un nuevo proceso de bash y le pasa como argumento el comando dentro de las comillas simples.
  - `mkdir -p /rootedwinfiles`: crea el directorio `/rootedwinfiles` si no existe (`-p` asegura que se creen todos los componentes necesarios).
  - `&&`: permite ejecutar múltiples comandos secuencialmente si el comando anterior tiene éxito.
  - `cp "$1" /rootedwinfiles`: copia el archivo encontrado (`$1`) al directorio `/rootedwinfiles`.
  - `-- {}`: indica que `{}` se sustituirá por cada archivo encontrado por `find`.
  - `\;`: marca el final del comando `-exec`.


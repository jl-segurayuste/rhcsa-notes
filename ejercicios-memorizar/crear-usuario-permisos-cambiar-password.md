# Crear usuario con permisos para cambiar contraseña

El shell `/bin/passwd` no es un shell real y no existe en la mayoría de los sistemas Unix o Linux. Parece que hay una confusión o malentendido. Sin embargo, es posible restringir a un usuario para que solo pueda cambiar su contraseña utilizando el comando `passwd` y no pueda iniciar sesión en un shell interactivo normal.

Aquí hay algunas formas de restringir el acceso de un usuario:

## 1. Usar `/sbin/nologin`

El uso de `/sbin/nologin` como el shell del usuario evita que inicie sesión, pero muestra un mensaje diciendo que el acceso no está permitido.

```bash
sudo useradd bob -s /sbin/nologin
```

Para cambiar el shell de un usuario existente:

```bash
sudo chsh -s /sbin/nologin bob
```

## 2. Usar `/bin/false`

El uso de `/bin/false` como el shell del usuario también evitará que inicie sesión, pero sin mostrar un mensaje.

```bash
sudo useradd bob -s /bin/false
```

Para cambiar el shell de un usuario existente:

```bash
sudo chsh -s /bin/false bob
```

## 3. Crear un script personalizado

Puedes crear un script que solo permita al usuario cambiar su contraseña y configurarlo como su shell. Aquí tienes un ejemplo de cómo hacerlo:

1. Crea un script en `/usr/local/bin/passwd_shell`:

   ```bash
   #!/bin/bash
   exec /usr/bin/passwd
   ```

2. Haz que el script sea ejecutable:

   ```bash
   sudo chmod +x /usr/local/bin/passwd_shell
   ```

3. Asigna este script como el shell del usuario:

   ```bash
   sudo useradd bob -s /usr/local/bin/passwd_shell
   ```

   Para cambiar el shell de un usuario existente:

   ```bash
   sudo chsh -s /usr/local/bin/passwd_shell bob
   ```

## Verificación de los shells disponibles

Para ver los shells disponibles en tu sistema, puedes revisar el archivo `/etc/shells`:

```bash
cat /etc/shells
```

El resultado será algo como esto:

```
/bin/sh
/bin/bash
/sbin/nologin
/bin/dash
/bin/tcsh
/bin/csh
/bin/false
```

## Resumen

El shell `/bin/passwd` no existe como un shell estándar. En lugar de eso, puedes restringir a un usuario para que no pueda iniciar sesión utilizando shells como `/sbin/nologin` o `/bin/false`, o crear un script personalizado que solo permita cambiar la contraseña y configurarlo como el shell del usuario. Esto asegura que el usuario no pueda usar el sistema para nada más que cambiar su propia contraseña.

## Comprobación

Para comprobar que la configuración del shell del usuario se ha realizado correctamente, puedes seguir estos pasos:

### 1. Verificar el shell del usuario

Puedes verificar el shell actual asignado al usuario utilizando el comando `getent` o inspeccionando el archivo `/etc/passwd`.

#### Usar `getent`

```bash
getent passwd bob
```

Este comando mostrará una línea similar a esta:

```
bob:x:1001:1001::/home/bob:/usr/local/bin/passwd_shell
```

Aquí, `/usr/local/bin/passwd_shell` es el shell asignado a `bob`.

#### Inspeccionar `/etc/passwd`

Alternativamente, puedes inspeccionar directamente el archivo `/etc/passwd`:

```bash
grep '^bob:' /etc/passwd
```

### 2. Intentar iniciar sesión como el usuario

Intenta iniciar sesión como el usuario `bob`. Dependiendo del shell asignado, deberías observar el siguiente comportamiento:

#### Si se usó `/sbin/nologin`

```bash
su - bob
```

Debería mostrar un mensaje como:

```
This account is currently not available.
```

#### Si se usó `/bin/false`

```bash
su - bob
```

Debería simplemente cerrar la sesión inmediatamente sin mostrar ningún mensaje.

#### Si se usó un script personalizado (como `/usr/local/bin/passwd_shell`)

```bash
su - bob
```

Deberías ser redirigido directamente al comando `passwd` para cambiar la contraseña.

## Ejemplo completo

Aquí tienes un ejemplo completo, suponiendo que usamos el script personalizado para cambiar la contraseña:

1. **Crear el script:**

   ```bash
   sudo bash -c 'echo -e "#!/bin/bash\nexec /usr/bin/passwd" > /usr/local/bin/passwd_shell'
   sudo chmod +x /usr/local/bin/passwd_shell
   ```

2. **Asignar el shell al usuario:**

   ```bash
   sudo useradd bob -s /usr/local/bin/passwd_shell
   ```

3. **Verificar la asignación del shell:**

   ```bash
   getent passwd bob
   # O alternativamente
   grep '^bob:' /etc/passwd
   ```

4. **Probar iniciar sesión como el usuario:**

   ```bash
   su - bob
   ```

   Deberías ver el prompt para cambiar la contraseña, algo como:

   ```
   Changing password for user bob.
   New password:
   ```

Al seguir estos pasos, puedes estar seguro de que la configuración del shell del usuario se ha realizado correctamente y que el usuario está limitado a la funcionalidad especificada.

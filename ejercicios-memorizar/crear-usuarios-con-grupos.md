# Crear usuarios con grupos específicos

> **Enunciado:** Add 3 users: harry, natasha, tom.
> The requirements: The additional group of the two users harry, natasha is the admin group. The user tom's login shell should be non-interactive.

Para agregar los usuarios `harry`, `natasha` y `tom` con las especificaciones dadas, sigue estos pasos detallados:

## Pasos para crear y configurar usuarios

### 1. Crear los usuarios `harry` y `natasha` con el grupo adicional `admin`

1. Abre una terminal como root o utiliza `sudo` para los comandos.
2. Agrega el usuario `harry` y asigna el grupo adicional `admin`:

   ```bash
   useradd -G admin harry
   ```

3. Agrega el usuario `natasha` y asigna el grupo adicional `admin`:

   ```bash
   useradd -G admin natasha
   ```

### 2. Crear el usuario `tom` con un shell no interactivo

1. Agrega el usuario `tom` y configura su shell de inicio de sesión como no interactivo (`/sbin/nologin`):

   ```bash
   useradd -s /sbin/nologin tom
   ```

### 3. Verificar la configuración

1. Verifica que los usuarios `harry` y `natasha` estén en el grupo `admin`:

   ```bash
   id harry
   id natasha
   ```

2. Verifica el shell de inicio de sesión del usuario `tom`:

   ```bash
   grep tom /etc/passwd
   ```

## Comandos completos

```bash
# Agregar usuarios harry y natasha al grupo adicional admin
useradd -G admin harry
useradd -G admin natasha

# Agregar usuario tom con shell no interactivo
useradd -s /sbin/nologin tom

# Verificar grupos adicionales de harry y natasha
id harry
id natasha

# Verificar el shell de inicio de sesión de tom
grep tom /etc/passwd
```

## Explicación de los comandos

1. **`useradd -G admin harry`**: este comando agrega el usuario `harry` y lo incluye en el grupo adicional `admin`.
2. **`useradd -G admin natasha`**: este comando agrega el usuario `natasha` y lo incluye en el grupo adicional `admin`.
3. **`useradd -s /sbin/nologin tom`**: este comando agrega el usuario `tom` y establece su shell de inicio de sesión como `/sbin/nologin`, lo que significa que el usuario no podrá iniciar sesión interactivamente.
4. **`id harry`** y **`id natasha`**: estos comandos muestran la información de los usuarios `harry` y `natasha`, incluyendo los grupos a los que pertenecen.
5. **`grep tom /etc/passwd`**: este comando muestra la línea correspondiente al usuario `tom` en el archivo `/etc/passwd`, permitiendo verificar su shell de inicio de sesión.

> **Nota:** si el grupo `admin` no existe previamente, hay que crearlo antes con `groupadd admin`, o `useradd -G admin` fallará.

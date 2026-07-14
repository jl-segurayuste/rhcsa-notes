# Instalar un paquete desde FTP

> **Enunciado:** One Package named zsh is dump on [ftp://server1.example.com](ftp://server1.example.com/) under /pub/updates directory and your FTP server is 192.168.0.254. Install the package zsh.

Esta simulación guía a través del proceso de instalación de un paquete llamado zsh que se encuentra en un servidor FTP específico.

## 1. Descargar e instalar el paquete directamente desde FTP

Puedes usar el comando `rpm` directamente para instalar el paquete desde la ubicación FTP especificada:

```bash
rpm -ivh ftp://server1.example.com/pub/updates/zsh-*
```

Este comando descargará e instalará el paquete zsh desde el servidor FTP indicado.

Alternativamente, si prefieres descargar primero el paquete y luego instalarlo, puedes seguir estos pasos:

## 2. Conectar al servidor FTP

Utiliza el cliente FTP para conectarte al servidor FTP especificado. Puedes usar el comando `ftp` y el usuario anónimo para iniciar sesión:

```bash
ftp server1.example.com
```

## 3. Navegar al directorio del paquete

Cambia al directorio donde se encuentra el paquete. En este caso, necesitas navegar a `/pub/updates`:

```bash
cd pub/updates
```

## 4. Descargar el paquete

Utiliza el comando `mget` para descargar el paquete zsh. Esto descargará el paquete al directorio local desde donde estás ejecutando el cliente FTP:

```bash
mget zsh-*
```

## 5. Cerrar la conexión FTP

Una vez que hayas descargado el paquete, puedes cerrar la conexión FTP:

```bash
bye
```

## 6. Instalar el paquete

Después de descargar el paquete, puedes instalarlo utilizando el comando `rpm`:

```bash
rpm -ivh zsh-*
```

## 7. Verificar la instalación

Finalmente, puedes verificar si el paquete zsh se instaló correctamente utilizando el comando `rpm -q`:

```bash
rpm -q zsh
```

Este comando te mostrará la versión del paquete zsh instalada en tu sistema. Si la instalación fue exitosa, deberías ver el nombre y la versión del paquete.

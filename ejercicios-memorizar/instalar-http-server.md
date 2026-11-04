# Instalar HTTP server

> **Enunciado:** Configure a HTTP server, which can be accessed through `http://station.domain40.example.com`. Please download the released page from `http://ip/dir/example.html`.

Este ejercicio te guía a través de la configuración de un servidor HTTP (Apache) en Linux para que pueda ser accesible a través de un nombre de dominio específico y permita la descarga de una página web desde una URL externa. Aquí está el paso a paso con explicación.

## Pasos

### 1. Instalación de Apache HTTP Server

```bash
yum install -y httpd
```

**Explicación:** Instala el servidor HTTP Apache en el sistema.

### 2. Configuración para iniciar Apache automáticamente al arrancar el sistema

```bash
chkconfig httpd on
```

**Explicación:** Habilita que Apache se inicie automáticamente cada vez que se inicie el sistema operativo.

### 3. Navegar al directorio del servidor web

```bash
cd /var/www/html
```

**Explicación:** Este es el directorio raíz por defecto donde Apache sirve los archivos web.

### 4. Descargar un archivo HTML de una URL externa

```bash
wget http://ip/dir/example.html
```

**Explicación:** Descarga el archivo `example.html` desde la URL especificada (`http://ip/dir/example.html`) al directorio actual (`/var/www/html`).

### 5. Copiar y renombrar el archivo HTML descargado

```bash
cp example.html index.html
```

**Explicación:** Copia el archivo `example.html` descargado y lo renombra como `index.html`, que es el archivo por defecto que servirá Apache cuando se acceda al directorio raíz (`/var/www/html`) sin especificar un nombre de archivo específico.

### 6. Configurar el archivo de configuración principal de Apache

```bash
vim /etc/httpd/conf/httpd.conf
```

**Explicación:** Edita el archivo de configuración principal de Apache (`httpd.conf`) para configurar un virtual host que maneje las solicitudes para el nombre de servidor `station.domain40.example.com`.

### 7. Editar el archivo de configuración de Apache (`httpd.conf`)

```
NameVirtualHost 192.168.0.254:80
<VirtualHost 192.168.0.254:80>
DocumentRoot /var/www/html/
ServerName station.domain40.example.com
</VirtualHost>
```

**Explicación:** Estas líneas configuran un virtual host en Apache con las siguientes directivas:

- `NameVirtualHost 192.168.0.254:80`: Declara que el servidor va a escuchar en la dirección IP `192.168.0.254` y en el puerto `80`.
- `<VirtualHost 192.168.0.254:80>`: Inicia la definición del virtual host.
- `DocumentRoot /var/www/html/`: Especifica el directorio raíz de documentos para este virtual host.
- `ServerName station.domain40.example.com`: Asigna el nombre del servidor que se utilizará para acceder a este virtual host.

> **Nota RHEL 8/9 (Apache 2.4):**
>
> - `chkconfig httpd on` → `systemctl enable --now httpd`, y abrir el cortafuegos:
>   `firewall-cmd --permanent --add-service=http && firewall-cmd --reload`.
> - `NameVirtualHost` ya no existe (Apache 2.4 solo avisa de que no tiene efecto): basta el bloque
>   `<VirtualHost …>`, mejor en un fichero propio como `/etc/httpd/conf.d/station.conf`.
> - Comprueba la sintaxis con `apachectl configtest` antes de reiniciar.
> - Un fichero copiado con `mv` desde otra carpeta conserva su contexto SELinux y httpd no lo puede servir:
>   `restorecon -Rv /var/www/html` (con `cp` o `wget` dentro de la carpeta ya hereda el correcto).

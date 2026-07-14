# Crear contenedor HTTP como servicio del sistema

## Enunciado

Create a container that runs an HTTP server. Ensure that it is started on system boot as a system user service.

## Pasos completos (versión final corregida)

### 1. Configuración inicial del sistema

```bash
# Crear directorio para contenido web
sudo mkdir -p /httproot

# Configurar firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --reload

# Configurar SELinux
sudo semanage port -a -t http_port_t -p tcp 8080 2>/dev/null || sudo semanage port -m -t http_port_t -p tcp 8080
sudo semanage fcontext -a -t container_file_t "/httproot(/.*)?"
sudo restorecon -Rv /httproot/
```

### 2. Crear usuario del sistema

```bash
sudo useradd -r -s /bin/bash -d /var/lib/containers -m container-user
```

### 3. Configurar permisos y submapeos

```bash
# Configurar permisos del directorio web
sudo chown container-user:container-user /httproot
sudo chmod 755 /httproot

# Añadir submapeos UID/GID (usar el siguiente rango disponible)
echo "container-user:558752:65536" | sudo tee -a /etc/subuid
echo "container-user:558752:65536" | sudo tee -a /etc/subgid

# Habilitar lingering
sudo loginctl enable-linger container-user

# Migrar sistema podman
cd /tmp
sudo -u container-user podman system migrate
```

### 4. Configurar contenedor como usuario del sistema

```bash
# Cambiar al usuario del sistema
sudo su - container-user

# Crear contenido de prueba
echo "<h1>Hello from Container HTTP Server</h1>" > /httproot/index.html

# CORRECCIÓN CRÍTICA: Usar el DocumentRoot correcto para httpd oficial
podman run -d --name http-container \
    -p 127.0.0.1:8080:80 \
    -v /httproot:/usr/local/apache2/htdocs:Z \
    --security-opt label=disable \
    --restart=always \
    docker.io/library/httpd:latest

# Verificar que funciona inmediatamente
podman exec http-container cat /usr/local/apache2/htdocs/index.html

# Generar servicio systemd
mkdir -p ~/.config/systemd/user
podman generate systemd --new --files --name http-container
mv *.service ~/.config/systemd/user/

# Salir del usuario
exit
```

### 5. Habilitar servicio del sistema

```bash
# Configurar variables de entorno para systemd
export XDG_RUNTIME_DIR="/run/user/$(id -u container-user)"
mkdir -p "$XDG_RUNTIME_DIR"
sudo chown container-user:container-user "$XDG_RUNTIME_DIR"

# Habilitar y iniciar servicio
sudo -u container-user XDG_RUNTIME_DIR="$XDG_RUNTIME_DIR" systemctl --user daemon-reload
sudo -u container-user XDG_RUNTIME_DIR="$XDG_RUNTIME_DIR" systemctl --user enable container-http-container.service
sudo -u container-user XDG_RUNTIME_DIR="$XDG_RUNTIME_DIR" systemctl --user start container-http-container.service

# Restaurar shell nologin
sudo usermod -s /sbin/nologin container-user
```

### 6. Verificación

```bash
# Verificar servicio
sudo -u container-user XDG_RUNTIME_DIR="/run/user/$(id -u container-user)" systemctl --user status container-http-container.service

# CORRECCIÓN: Probar servidor HTTP con IPv4 explícito
curl -4 http://localhost:8080
# O usar IP directa
curl http://127.0.0.1:8080
# Debe mostrar: <h1>Hello from Container HTTP Server</h1>

# Verificar contenedor
sudo -u container-user podman ps

# Verificar inicio automático
sudo loginctl show-user container-user | grep Linger
```

### 7. Prueba de persistencia tras reinicio

```bash
# Reiniciar sistema
sudo systemctl reboot

# Después del reinicio, verificar
curl -4 http://localhost:8080
# o
curl http://127.0.0.1:8080
```

## Comandos resumidos para examen

```bash
# Configuración inicial
sudo mkdir -p /httproot
sudo firewall-cmd --permanent --add-port=8080/tcp && sudo firewall-cmd --reload
sudo semanage port -a -t http_port_t -p tcp 8080 2>/dev/null || sudo semanage port -m -t http_port_t -p tcp 8080
sudo semanage fcontext -a -t container_file_t "/httproot(/.*)?" && sudo restorecon -Rv /httproot/

# Crear usuario
sudo useradd -r -s /bin/bash -d /var/lib/containers -m container-user
sudo chown container-user:container-user /httproot
echo "container-user:558752:65536" | sudo tee -a /etc/subuid
echo "container-user:558752:65536" | sudo tee -a /etc/subgid
sudo loginctl enable-linger container-user

# Migrar podman
cd /tmp && sudo -u container-user podman system migrate

# Crear contenedor
sudo su - container-user
echo "<h1>Hello from Container HTTP Server</h1>" > /httproot/index.html
podman run -d --name http-container -p 127.0.0.1:8080:80 -v /httproot:/usr/local/apache2/htdocs:Z --security-opt label=disable --restart=always docker.io/library/httpd:latest
mkdir -p ~/.config/systemd/user
podman generate systemd --new --files --name http-container
mv *.service ~/.config/systemd/user/
exit

# Habilitar servicio
sudo -u container-user XDG_RUNTIME_DIR="/run/user/$(id -u container-user)" systemctl --user daemon-reload
sudo -u container-user XDG_RUNTIME_DIR="/run/user/$(id -u container-user)" systemctl --user enable --now container-http-container.service
sudo usermod -s /sbin/nologin container-user

# Verificar
curl -4 http://localhost:8080
```

## Correcciones críticas aplicadas

### 1. DocumentRoot correcto

- **Antes**: `-v /httproot:/var/www/html:z`
- **Ahora**: `-v /httproot:/usr/local/apache2/htdocs:Z`

### 2. Networking IPv4

- **Antes**: `-p 8080:80`
- **Ahora**: `-p 127.0.0.1:8080:80`

### 3. Verificación con IPv4

- **Antes**: `curl http://localhost:8080`
- **Ahora**: `curl -4 http://localhost:8080` o `curl http://127.0.0.1:8080`

### 4. Opciones adicionales de robustez

- `--security-opt label=disable`: Evita problemas SELinux persistentes
- `--restart=always`: Mejora la persistencia tras reinicios
- Contexto SELinux permanente con `sudo restorecon -Rv /httproot/`

## Puntos críticos para el examen

### Obligatorios

- Usuario del sistema con lingering habilitado
- Submapeos UID/GID configurados
- Servicio systemd de usuario habilitado
- Contenedor accesible en puerto 8080
- Inicio automático en el arranque del sistema
- DocumentRoot correcto: `/usr/local/apache2/htdocs`
- Verificación con IPv4: `curl -4 http://localhost:8080`

### Comandos de verificación del examinador

```bash
sudo -u container-user podman ps                                    # Contenedor corriendo
sudo loginctl show-user container-user | grep Linger               # Linger=yes
curl -4 http://localhost:8080                                      # Contenido personalizado
systemctl reboot && curl -4 http://localhost:8080                  # Persistencia
```

### Troubleshooting común

- Si IPv6 da problemas: usar `curl -4` o `127.0.0.1`
- Si SELinux bloquea: verificar `ls -Z /httproot/` y usar `--security-opt label=disable`
- Si no persiste: verificar lingering y regenerar servicio systemd

> **Nota:** Este manual incluye todas las correcciones identificadas y está listo para el examen RHCSA.

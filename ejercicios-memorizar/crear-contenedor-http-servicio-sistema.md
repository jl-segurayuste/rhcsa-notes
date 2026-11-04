# Crear contenedor HTTP como servicio del sistema

## Enunciado

Create a container that runs an HTTP server. Ensure that it is started on system boot as a system user service.

Es decir: un contenedor **rootless** (de un usuario normal) que arranca solo al encender la máquina,
sin que nadie inicie sesión, gestionado por `systemd --user`.

## 1. Preparar el usuario y el contenido (como root)

```bash
# Usuario normal: useradd le asigna solo los rangos de /etc/subuid y /etc/subgid que necesita podman
sudo useradd container-user
sudo passwd container-user

# Sus servicios arrancan en el arranque aunque no haya iniciado sesión
sudo loginctl enable-linger container-user

# Contenido web, propiedad del usuario
sudo mkdir -p /httproot
echo "<h1>Hello from Container HTTP Server</h1>" | sudo tee /httproot/index.html
sudo chown -R container-user: /httproot

# Cortafuegos: el puerto que se publicará
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```

Notas:

- No uses `useradd -r` ni `-d /var/lib/containers`: ese directorio es el almacenamiento de los
  contenedores de root y un usuario de sistema no recibe rangos de `subuid`/`subgid`.
- El contexto SELinux de `/httproot` lo pone el propio podman con `:Z` (paso 2); no hace falta
  `semanage fcontext`.
- Un contenedor rootless no necesita `semanage port` para publicar el 8080 (no hay un proceso
  confinado de httpd escuchando en ese puerto del host).

## 2. Crear el contenedor (como container-user, en una sesión real)

`systemctl --user` necesita la sesión del usuario (`XDG_RUNTIME_DIR`, D-Bus). `su -` o `sudo -u` no
la crean; entra por SSH o con `machinectl`:

```bash
ssh container-user@localhost
# o, desde root:  machinectl shell container-user@
```

```bash
podman run -d --name http-container -p 8080:80 \
    -v /httproot:/usr/local/apache2/htdocs:Z \
    docker.io/library/httpd

curl http://localhost:8080          # <h1>Hello from Container HTTP Server</h1>
```

- `:Z` cambia el contexto de `/httproot` a `container_file_t` (solo para este contenedor) y SELinux
  sigue protegiendo. **No** uses `--security-opt label=disable`: apaga SELinux para el contenedor.
- `/usr/local/apache2/htdocs` es el DocumentRoot de la imagen oficial `httpd`.

## 3. Servicio de systemd del usuario

```bash
mkdir -p ~/.config/systemd/user
cd ~/.config/systemd/user
podman generate systemd --new --files --name http-container   # crea container-http-container.service

# Con --new el servicio crea su propio contenedor: quitar el creado a mano
podman rm -f http-container

systemctl --user daemon-reload
systemctl --user enable --now container-http-container.service
systemctl --user status container-http-container.service
```

### Alternativa en RHEL 9.4+ y RHEL 10: Quadlet

`podman generate systemd` está obsoleto en las versiones nuevas de podman. Con Quadlet se describe el
contenedor en un fichero y systemd genera el servicio:

```ini
# ~/.config/containers/systemd/http-container.container
[Container]
Image=docker.io/library/httpd
PublishPort=8080:80
Volume=/httproot:/usr/local/apache2/htdocs:Z

[Install]
WantedBy=default.target
```

```bash
systemctl --user daemon-reload
systemctl --user start http-container.service    # el [Install] lo activa en el arranque
```

## 4. Verificación

```bash
# Como root
loginctl show-user container-user | grep Linger      # Linger=yes
curl http://localhost:8080                            # el contenido de /httproot
ls -Zd /httproot                                      # ...:container_file_t:...

sudo systemctl reboot
# Tras el arranque, SIN iniciar sesión como container-user:
curl http://localhost:8080
```

## Problemas habituales

| Síntoma | Causa |
|---|---|
| `Failed to connect to bus` con `systemctl --user` | Sesión creada con `su`/`sudo -u`: entra con `ssh` o `machinectl shell` |
| No arranca tras reiniciar | Falta `loginctl enable-linger` o el servicio no está `enable` |
| `403 Forbidden` o `Permission denied` en el contenido | Falta `:Z` en el volumen (contexto SELinux) |
| `cannot find UID/GID for user` | Usuario sin rangos en `/etc/subuid` y `/etc/subgid` (creado con `-r`) |
| `curl` desde otro equipo no responde | Falta abrir el puerto en el cortafuegos, o se publicó solo en `127.0.0.1` |

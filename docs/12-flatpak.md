# Flatpak

> Objetivo nuevo del EX200 sobre RHEL 10 (dominio *Manage software*): configurar el acceso a repositorios
> Flatpak e instalar y quitar paquetes Flatpak. Las órdenes son las de `flatpak` 1.14+; comprobadas con
> `flatpak <orden> --help`.

Flatpak instala aplicaciones de escritorio empaquetadas con sus propias dependencias (*runtimes*),
aisladas del sistema. No sustituye a `dnf`: conviven.

## Conceptos

| Término | Qué es |
|---|---|
| **Remoto** (*remote*) | Repositorio de Flatpak (equivalente a un repo de dnf). Ejemplo: Flathub |
| **Ref / ID de aplicación** | Nombre único al revés de un dominio: `org.gnome.Calculator` |
| **Runtime** | Base compartida que necesitan las aplicaciones (se instala sola) |
| **Instalación del sistema** | Por defecto (`--system`), en `/var/lib/flatpak`, para todos los usuarios; necesita root |
| **Instalación del usuario** | Con `--user`, en `~/.local/share/flatpak`, solo para ese usuario |

## Repositorios (remotos)

```bash
dnf install -y flatpak                       # si no está instalado

flatpak remotes                              # listar los remotos configurados
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
flatpak remote-ls flathub | head             # qué ofrece un remoto
flatpak remote-delete flathub                # quitar un remoto
```

- En el examen la URL del repositorio te la dan en el enunciado; la forma es la misma.
- `remote-add` sin `--user` lo añade para todo el sistema y queda guardado (sobrevive al reinicio).
- `--if-not-exists` evita el error si ya estaba añadido.

## Instalar, consultar y quitar

```bash
flatpak search calculator                    # buscar en los remotos
flatpak install -y flathub org.gnome.Calculator
flatpak list --app                           # aplicaciones instaladas
flatpak info org.gnome.Calculator            # versión, remoto, tamaño…
flatpak run org.gnome.Calculator             # ejecutarla
flatpak update -y                            # actualizar todo

flatpak uninstall -y org.gnome.Calculator    # quitarla
flatpak uninstall -y --unused                # quitar runtimes que ya no usa nadie
flatpak uninstall -y --delete-data org.gnome.Calculator   # quitarla y borrar sus datos de usuario
```

- `-y` responde «sí» a las preguntas: útil en scripts y en el examen.
- Para un usuario concreto, las mismas órdenes con `--user` (y sin root).

## Comprobación tras reiniciar

```bash
flatpak remotes                 # el remoto sigue configurado
flatpak list --app              # la aplicación sigue instalada
```

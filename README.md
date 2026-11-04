# RHCSA Notes

Apuntes de estudio y ejercicios de práctica para la certificación **Red Hat Certified System Administrator (RHCSA)**, organizados como libro de referencia en Markdown.

## Versión: RHEL 8, 9 y 10

La teoría de `docs/` sigue mayormente el temario de RHCSA sobre RHEL 8 y los exámenes de práctica de
`examenes-practica/` se resolvieron sobre AlmaLinux 9. **El EX200 actual se basa en RHEL 10**: añade
Flatpak ([`docs/12-flatpak.md`](docs/12-flatpak.md)) y quita los contenedores, VDO y Stratis, que se
conservan como referencia. La lista completa de diferencias está en
[`docs/00-objetivos.md`](docs/00-objetivos.md#cambios-en-el-examen-actual-ex200-sobre-rhel-10). Donde
cambian los comandos entre versiones (`yum`/`dnf`, `ifcfg-*`/keyfile de NetworkManager, `vdo`/LVM-VDO,
`podman generate systemd`/Quadlet…), los ficheros indican las variantes.

## Estado

✅ Migración completa desde los apuntes personales de Notion (julio 2026). Contenido migrado:

| Carpeta | Ficheros | Contenido |
| --- | --- | --- |
| [`docs/`](docs/) | 14 | Los capítulos del temario (00-12, el 12 añadido para RHEL 10) + 1 subpágina ampliada (LVM) |
| [`teoria-memorizar/`](teoria-memorizar/) | 13 | 12 temas puntuales + 1 subpágina (redimensionar LVs) |
| [`ejercicios-memorizar/`](ejercicios-memorizar/) | 29 | 28 ejercicios recurrentes + 1 variante (reset de root en Debian) |
| [`examenes-practica/`](examenes-practica/) | 6 | Índice general + Exam 1 + Exámenes A, B, C y D |

Un puñado de capturas de pantalla, un adjunto PDF y un "synced block" de Notion no se pudieron migrar (quedan anotados con `> [Imagen no migrada: ...]` / `> [Adjunto no migrado: ...]` en el propio fichero donde faltan, para revisar a mano si hace falta su contenido).

## Estructura

- [`docs/`](docs/) — Teoría organizada por objetivo oficial del examen (redes, herramientas, scripts, sistemas en funcionamiento, almacenamiento, sistemas de archivos, gestión de sistemas, usuarios y grupos, seguridad, SELinux, contenedores). Ver índice completo más abajo.
- [`teoria-memorizar/`](teoria-memorizar/) — Apuntes puntuales de temas que conviene tener memorizados el día del examen (LVM, firewall-cmd, VIM, timers, IPs privadas, secciones de `man`...).
- [`ejercicios-memorizar/`](ejercicios-memorizar/) — Ejercicios prácticos recurrentes que conviene poder resolver de memoria (fstab, logrotate, journald persistente, reset de contraseña root, LVM, Stratis, VDO, contenedores como servicio systemd...).
- [`examenes-practica/`](examenes-practica/) — Exámenes de práctica completos (estilo EX200) con sus ejercicios.

## Índice de `docs/`

0. [Objetivos del Examen RHCSA](docs/00-objetivos.md)
1. [Conocimiento y uso de las herramientas principales](docs/01-herramientas-principales.md)
2. [Creación de scripts de shell sencillos](docs/02-scripts-shell.md)
3. [Uso de sistemas en funcionamiento](docs/03-sistemas-en-funcionamiento.md)
4. [Configuración del almacenamiento local](docs/04-almacenamiento-local.md)
5. [Creación y configuración de sistemas de archivos](docs/05-sistemas-de-archivos.md)
6. [Implementación, configuración y mantenimiento de los sistemas](docs/06-implementacion-configuracion-mantenimiento.md)
7. [Gestión de los usuarios y los grupos](docs/07-usuarios-y-grupos.md)
8. [Gestión de la seguridad](docs/08-gestion-de-la-seguridad.md)
9. [Diagnostique y aborde los incumplimientos rutinarios de las políticas de SELinux](docs/09-selinux.md)
10. [Gestión de las conexiones de red básicas](docs/10-conexiones-de-red.md)
11. [Gestión de contenedores](docs/11-gestion-de-contenedores.md) *(ya no está en el EX200 de RHEL 10)*
12. [Flatpak](docs/12-flatpak.md) *(nuevo en el EX200 de RHEL 10)*

Además, [`docs/lvm-logical-volume-manager.md`](docs/lvm-logical-volume-manager.md) amplía el tema de LVM enlazado desde el capítulo 4.

## Objetivo

Servir como guía de repaso propia y, de paso, como recurso público para cualquiera que esté preparando el RHCSA.

## Licencia

MIT — usa y adapta libremente.

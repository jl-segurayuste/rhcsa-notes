# Diagnostique y aborde los incumplimientos rutinarios de las políticas de SELinux

Cuando SELinux detecta y registra un incumplimiento de política, puedes diagnosticar y abordar el problema siguiendo estos pasos:

## 1. Verifica los Registros de SELinux

Revisa los registros de SELinux en busca de mensajes de incumplimiento. Los registros suelen estar en `/var/log/audit/audit.log` o `/var/log/messages`. Utiliza herramientas como `grep` para buscar mensajes relacionados con SELinux:

```shell
grep AVC /var/log/audit/audit.log
```

## 2. Utiliza `ausearch` para Investigar

La herramienta `ausearch` te permite buscar eventos de auditoría y puede ayudarte a comprender mejor los incumplimientos de SELinux. Puedes filtrar los resultados para encontrar mensajes específicos:

```shell
ausearch -m avc -ts today
```

## 3. Instala y Utiliza `setroubleshoot`

El paquete `setroubleshoot` proporciona herramientas adicionales para diagnosticar problemas de SELinux. Instálalo y utiliza el comando `sealert` para obtener informes detallados sobre incumplimientos de políticas:

```shell
sudo yum install setroubleshoot
sudo sealert -a /var/log/audit/audit.log
```

## 4. Ajusta Booleanos de SELinux

En algunos casos, ajustar los booleanos de SELinux puede solucionar problemas. Verifica si hay booleanos relacionados con el problema y ajústalos según sea necesario. Usa comandos como `getsebool` y `setsebool`.

## 5. Crea y Aplica Módulos de Política

Si el incumplimiento es legítimo y necesitas permitir la acción, puedes crear un módulo de política personalizado. Utiliza `audit2allow` para generar un módulo basado en los registros de auditoría:

```shell
grep AVC /var/log/audit/audit.log | audit2allow -M mypolicy
sudo semodule -i mypolicy.pp
```

## 6. Reporta y Soluciona Problemas

Si encuentras incumplimientos recurrentes, es posible que haya un problema de política subyacente. Puedes informar sobre problemas y buscar soluciones en la comunidad de SELinux o en los recursos de soporte de Red Hat.

## 7. Desactiva SELinux Temporalmente (Opcional)

En situaciones críticas, puedes desactivar SELinux temporalmente para evitar bloqueos. Esto no es una solución permanente, pero puede ser útil para propósitos de diagnóstico:

```shell
sudo setenforce 0
```

## Notas Adicionales

- La mayoría de las acciones permitidas o denegadas por SELinux se registran en los registros de auditoría.
- La herramienta `seinfo` puede proporcionar información útil sobre contextos de seguridad y políticas.
- Evita desactivar SELinux permanentemente a menos que sea absolutamente necesario.

# Common firewall-cmd Options

Opciones de `firewall-cmd` más comunes:

| firewall-cmd option | Explicación |
|---|---|
| `--get-zone` | Muestra la zona en uso de una interfaz de red específica. |
| `--get-default-zone` | Muestra la zona predeterminada actual, que se aplica a todas las interfaces sin zona asignada. |
| `--set-default-zone=ZONE` | Cambia la zona predeterminada del firewall. |
| `--get-services` | Muestra todos los servicios admitidos que pueden ser habilitados o deshabilitados. |
| `--list-services` | Enumera los servicios permitidos en la zona actual o especificada. |
| `--add-service=<service>` | Permite un servicio en la zona actual o especificada, como `http`, `ssh`, `smtp`, etc. |
| `--remove-service=<service>` | Revoca el acceso de un servicio en la zona actual o especificada. |
| `--list-all-zones` | Muestra la configuración completa de todas las zonas, incluyendo servicios y puertos abiertos. |
| `--add-port=<port/protocol>` | Abre un puerto específico en la zona indicada. Ejemplo: `80/tcp`. |
| `--remove-port=<port/protocol>` | Cierra un puerto específico en la zona indicada. |
| `--add-interface=<interface>` | Asigna una interfaz de red a una zona específica. |
| `--remove-interface=<interface>` | Elimina la asociación de una interfaz de red con una zona específica. |
| `--add-source=<IP/range>` | Permite una dirección IP o rango de IPs en una zona específica. |
| `--remove-source=<IP/range>` | Elimina el acceso de una IP o rango en la zona indicada. |
| `--permanent` | Realiza cambios permanentes que persisten tras reinicios. |
| `--runtime-to-permanent` | Guarda los cambios de configuración temporal en la configuración permanente. |
| `--reload` | Recarga el firewall para aplicar cambios realizados con `--permanent`. |

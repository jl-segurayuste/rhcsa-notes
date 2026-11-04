# Firewall Default Zones

En **firewalld**, las **zonas predeterminadas** agrupan interfaces y conexiones de red en perfiles de seguridad específicos, cada uno con un conjunto de reglas y permisos. Las zonas de firewalld permiten gestionar fácilmente las políticas de acceso para diferentes tipos de redes, como redes privadas, públicas, o laborales, y ofrecen control granular sobre los puertos y servicios permitidos.

Aquí están las zonas predeterminadas en firewalld:

1. **drop**: Todo el tráfico entrante se descarta sin respuesta, mientras que el tráfico saliente está permitido. No es seguro para redes donde se necesita comunicación bidireccional.
2. **block**: El tráfico entrante se rechaza con un mensaje ICMP `icmp-host-prohibited` (en IPv6, `icmp6-adm-prohibited`). El tráfico saliente está permitido.
3. **public**: Se usa para redes públicas donde no se confía en otros dispositivos. Solo se permite el tráfico esencial configurado explícitamente.
4. **external**: Ideal para redes externas con enmascaramiento NAT habilitado, permite compartir la conexión de internet de manera controlada.
5. **dmz (Demilitarized Zone)**: Se usa para exponer servicios a redes no confiables, como servidores de correo o web, limitando el acceso al sistema interno.
6. **work**: Destinada a redes laborales donde se confía en la mayoría de dispositivos. Más permisiva que *public*, permite tráfico esencial.
7. **home**: Se utiliza para redes domésticas. Permite la comunicación entre dispositivos de confianza en la misma red.
8. **internal**: Para redes internas; se confía en la mayoría de equipos y se aceptan algunas conexiones entrantes (por defecto, prácticamente los mismos servicios que *home*). La zona de confianza total es *trusted*.
9. **trusted**: Permite todo el tráfico de entrada y salida, sin restricciones. Debe usarse solo en redes completamente seguras.

Cada zona tiene un propósito específico y es importante elegir la adecuada según el tipo de red y el nivel de seguridad requerido para las interfaces conectadas.

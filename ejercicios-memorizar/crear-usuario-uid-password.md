# Crear usuario con UID y contraseña

Comando para crear un usuario especificando un UID concreto y una contraseña cifrada en la misma línea:

```bash
useradd -u 1234 -p $(openssl passwd -1 alex111) alex
```

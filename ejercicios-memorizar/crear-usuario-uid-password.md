# Crear usuario con UID y contraseña

Comando para crear un usuario especificando un UID concreto y una contraseña cifrada en la misma línea:

```bash
useradd -u 1234 alex
echo alex111 | passwd --stdin alex
```

> `passwd --stdin` guarda la contraseña con el algoritmo del sistema (SHA-512 o yescrypt). La forma de una
> sola línea con `useradd -p $(openssl passwd -1 …)` usaba MD5, débil y distinto del resto del sistema; si
> se prefiere así, que sea `openssl passwd -6` (SHA-512).

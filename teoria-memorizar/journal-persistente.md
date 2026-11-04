# Configuración de journal de manera persistente

Se genera el directorio:

```bash
mkdir /var/log/journal
```

Se le otorgan permisos y se activa el GUID:

```bash
chmod 2755 /var/log/journal
```

Se asignan los propietarios:

```bash
chown root:systemd-journal /var/log/journal
```

Se reinicia el system unit para aplicar los cambios:

```bash
systemctl restart systemd-journal-flush.service
```

# Crear un pool y volumen Stratis

## Enunciado

Configurar Stratis con disco de 10 GiB: añadir un disco de 10 GiB, crear un pool y volumen Stratis y montarlo en `/stratis`.

## Pasos completos (versión final optimizada)

### 1. Verificar discos disponibles

```bash
lsblk
fdisk -l
```

*Identificar el disco de 10 GiB disponible (ej: `/dev/sdb`)*

### 2. Instalar y configurar Stratis

```bash
sudo dnf install -y stratisd stratis-cli
sudo systemctl enable --now stratisd
sudo systemctl status stratisd
```

### 3. Crear pool Stratis

```bash
# Crear pool (ajustar /dev/sdb según tu sistema)
sudo stratis pool create mypool /dev/sdb

# Verificar creación
stratis pool list
```

### 4. Crear filesystem Stratis

```bash
# Crear filesystem en el pool
sudo stratis filesystem create mypool stratisvol

# Verificar creación
stratis filesystem list
```

### 5. Crear directorio de montaje

```bash
sudo mkdir -p /stratis
```

### 6. Configurar montaje permanente

```bash
# Obtener UUID del filesystem
UUID=$(sudo blkid -s UUID -o value /dev/stratis/mypool/stratisvol)

# Si el comando anterior no funciona, usar método alternativo:
# UUID=$(lsblk -no UUID /dev/stratis/mypool/stratisvol)

# Añadir entrada a /etc/fstab
echo "UUID=$UUID /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0" | sudo tee -a /etc/fstab

# Método alternativo si UUID no funciona:
# echo "/dev/stratis/mypool/stratisvol /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0" | sudo tee -a /etc/fstab
```

### 7. Montar el filesystem

```bash
sudo mount /stratis
```

### 8. Verificación completa

```bash
# Verificar pool
stratis pool list

# Verificar filesystem
stratis filesystem list

# Verificar montaje
df -h /stratis
mount | grep stratis

# Probar escritura
echo "Test Stratis filesystem" | sudo tee /stratis/test.txt   # sudo echo … > no funciona: la redirección la hace tu shell, sin sudo
cat /stratis/test.txt

# Verificar /etc/fstab
tail -1 /etc/fstab
```

### 9. Probar persistencia (opcional)

```bash
sudo systemctl reboot
# Después del reinicio verificar:
df -h /stratis
```

## Comandos resumidos para examen

```bash
# Instalación
sudo dnf install -y stratisd stratis-cli
sudo systemctl enable --now stratisd

# Crear pool y filesystem
sudo stratis pool create mypool /dev/sdb
sudo stratis filesystem create mypool stratisvol

# Configurar montaje
sudo mkdir -p /stratis
UUID=$(sudo blkid -s UUID -o value /dev/stratis/mypool/stratisvol)
echo "UUID=$UUID /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0" | sudo tee -a /etc/fstab
sudo mount /stratis

# Verificar
stratis pool list
stratis filesystem list
df -h /stratis
```

## Puntos críticos para el examen

### Obligatorios

- Servicio `stratisd` habilitado y corriendo
- Pool creado con el disco de 10 GiB
- Filesystem creado en el pool
- Montado en `/stratis`
- Entrada correcta en `/etc/fstab`
- Opción `x-systemd.requires=stratisd.service` en fstab
- Filesystem type `xfs`
- Montaje persiste tras reinicio

### Comandos de verificación del examinador

```bash
stratis pool list          # Debe mostrar mypool
stratis filesystem list    # Debe mostrar stratisvol
df -h /stratis            # Debe mostrar montaje
cat /etc/fstab            # Debe tener entrada correcta
systemctl reboot          # Probar persistencia
```

### Troubleshooting común

```bash
# Si UUID no funciona
echo "/dev/stratis/mypool/stratisvol /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0" | sudo tee -a /etc/fstab

# Si hay problemas
systemctl status stratisd
journalctl -u stratisd
```

### Detalles técnicos importantes

- Stratis maneja automáticamente el thin provisioning.
- El filesystem aparece como 1 TiB independientemente del tamaño del pool.
- `x-systemd.requires=stratisd.service` asegura que stratisd esté corriendo antes del montaje.
- El tipo de filesystem es siempre `xfs` para Stratis.

---

## Explicación detallada de la línea añadida a /etc/fstab para Stratis

### Línea completa en /etc/fstab

```bash
UUID=b988f39e-bf03-413e-af69-d0d5bf17d973 /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0
```

### Desglose campo por campo

#### Campo 1: `UUID=b988f39e-bf03-413e-af69-d0d5bf17d973`

- **Propósito**: Identifica de forma única el filesystem.
- **Por qué UUID**: Los UUIDs no cambian aunque se muevan los discos o cambien las letras de dispositivo.
- **Alternativa válida**: `/dev/stratis/mypool/stratisvol` (pero menos robusto).
- **Cómo obtenerlo**: `blkid -s UUID -o value /dev/stratis/mypool/stratisvol`

#### Campo 2: `/stratis`

- **Propósito**: Punto de montaje (mount point).
- **Requisito**: El directorio debe existir antes del montaje.
- **Creación**: `mkdir -p /stratis`
- **Permisos**: Por defecto hereda del filesystem padre.

#### Campo 3: `xfs`

- **Propósito**: Tipo de filesystem.
- **Para Stratis**: Siempre es `xfs` (Stratis usa XFS internamente).
- **No usar**: `stratis` como tipo (error común).
- **Importante**: No confundir con otros tipos como ext4, btrfs.

#### Campo 4: `defaults,x-systemd.requires=stratisd.service`

Esta es la parte más crítica y compleja:

**`defaults`**

- **Equivale a**: `rw,suid,dev,exec,auto,nouser,async`
- **`rw`**: Lectura y escritura
- **`suid`**: Permite ejecutables con setuid
- **`dev`**: Permite dispositivos especiales
- **`exec`**: Permite ejecutar binarios
- **`auto`**: Monta automáticamente en boot
- **`nouser`**: Solo root puede montar
- **`async`**: Escrituras asíncronas (mejor rendimiento)

**`,x-systemd.requires=stratisd.service`** (CRÍTICO)

- **`x-systemd.requires`**: Directiva especial de systemd.
- **Función**: Asegura que `stratisd.service` esté corriendo ANTES del montaje.
- **Por qué necesario**: Stratis necesita el daemon activo para funcionar.
- **Sin esto**: El montaje fallará en el boot si stratisd no está iniciado.
- **Sintaxis**: Siempre separado por coma, sin espacios.

#### Campo 5: `0` (primer cero)

- **Propósito**: Dump (respaldo con comando dump).
- **Valores posibles**:
  - `0`: No respaldar
  - `1`: Respaldar
- **Para Stratis**: Siempre `0` (dump no es compatible con Stratis).

#### Campo 6: `0` (segundo cero)

- **Propósito**: Orden de verificación con fsck.
- **Valores posibles**:
  - `0`: No verificar
  - `1`: Verificar primero (solo para /)
  - `2`: Verificar después
- **Para Stratis**: Siempre `0` (fsck no es aplicable a Stratis).

### Ejemplos de líneas incorrectas comunes

**Error 1: Tipo de filesystem incorrecto**

```bash
UUID=xxx /stratis stratis defaults 0 0
```

**Problema**: `stratis` no es un tipo de filesystem válido.

**Error 2: Falta dependencia systemd**

```bash
UUID=xxx /stratis xfs defaults 0 0
```

**Problema**: Puede fallar en boot si stratisd no está iniciado.

**Error 3: Espacios en opciones**

```bash
UUID=xxx /stratis xfs defaults, x-systemd.requires=stratisd.service 0 0
```

**Problema**: El espacio después de la coma causa error.

**Error 4: Usar device path en lugar de UUID**

```bash
/dev/sdb /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0
```

**Problema**: Debería usar `/dev/stratis/mypool/stratisvol` o mejor UUID.

### Versiones correctas alternativas

**Opción 1: Con UUID (recomendado)**

```bash
UUID=b988f39e-bf03-413e-af69-d0d5bf17d973 /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0
```

**Opción 2: Con device path**

```bash
/dev/stratis/mypool/stratisvol /stratis xfs defaults,x-systemd.requires=stratisd.service 0 0
```

**Opción 3: Con opciones adicionales de seguridad**

```bash
UUID=xxx /stratis xfs defaults,nodev,nosuid,x-systemd.requires=stratisd.service 0 0
```

### Comandos para verificar la línea

**Verificar sintaxis antes del montaje:**

```bash
# Verificar sintaxis de fstab
mount -fav

# Verificar línea específica
findmnt --verify /etc/fstab
```

**Probar montaje:**

```bash
# Montar según fstab
mount /stratis

# Verificar que funcionó
df -h /stratis
```

**Verificar tras reinicio:**

```bash
# Después de reboot
systemctl status stratisd
mount | grep stratis
df -h /stratis
```

### Consideraciones importantes para el examen

1. La opción `x-systemd.requires=stratisd.service` es OBLIGATORIA.
2. El tipo debe ser `xfs`, nunca `stratis`.
3. Los últimos dos campos deben ser `0 0` para Stratis.
4. No usar espacios en las opciones de montaje.
5. UUID es más robusto que device paths.
6. Verificar siempre con `mount -a` antes de reiniciar.

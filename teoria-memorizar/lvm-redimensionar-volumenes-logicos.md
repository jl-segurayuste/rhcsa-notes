# Redimensionar volúmenes lógicos

A continuación se muestra 4 modos de realizar el redimensionamiento. La opción `-r` es importante porque también redimensiona el File System que tenga en ese momento el volumen lógico.

Vamos a explicar cada comando y luego haremos una comparativa de los cuatro:

### Explicación de cada comando

1. **`lvresize -r -l 75%VG /dev/vgdata/lvdata`**:
   - Ajusta el tamaño del volumen lógico `lvdata` al **75% del tamaño total del grupo de volúmenes (VG)**.
   - Si `vgdata` tiene un tamaño total de 100 GB, el volumen lógico se ajustará para ocupar 75 GB.
   - No es incremental, simplemente ajusta el tamaño al 75% del total del VG, independientemente del tamaño actual del volumen lógico.

2. **`lvresize -r -l +75%VG /dev/vgdata/lvdata`**:
   - Aumenta el tamaño del volumen lógico `lvdata` en **un 75% del tamaño total del grupo de volúmenes (VG)**.
   - Es incremental, lo que significa que añade el 75% del tamaño total del VG al tamaño actual del volumen lógico.
   - Por ejemplo, si `vgdata` tiene un tamaño de 100 GB y `lvdata` ya ocupa 20 GB, se sumarán 75 GB adicionales, resultando en un total de 95 GB.

3. **`lvresize -r -l +75%FREE /dev/vgdata/lvdata`**:
   - Incrementa el tamaño del volumen lógico `lvdata` en **un 75% del espacio libre disponible (FREE)** dentro del grupo de volúmenes `vgdata`.
   - Por ejemplo, si el grupo de volúmenes tiene 40 GB de espacio libre, el volumen lógico se incrementará en un 75% de ese espacio libre (es decir, 30 GB adicionales).
   - Es una operación incremental que solo se suma al tamaño actual del volumen lógico utilizando el espacio libre disponible.

4. **`lvresize -r -l 75%FREE /dev/vgdata/lvdata`**:
   - Ajusta el tamaño del volumen lógico `lvdata` para que ocupe **el 75% del espacio libre disponible (FREE)** dentro del grupo de volúmenes `vgdata`.
   - No es incremental, simplemente establece el tamaño al 75% del espacio libre en el VG.
   - Por ejemplo, si hay 40 GB libres en el VG, el comando configurará el tamaño de `lvdata` para que sea de 30 GB, independientemente de su tamaño actual.

### Comparativa de los cuatro comandos

| Comando | Operación | Incremental | Referencia de Tamaño |
|---|---|---|---|
| `lvresize -r -l 75%VG` | Fija el tamaño al 75% del tamaño total del grupo de volúmenes | No | Tamaño total del VG |
| `lvresize -r -l +75%VG` | Incrementa el tamaño en un 75% del tamaño total del grupo de volúmenes | Sí | Tamaño total del VG |
| `lvresize -r -l +75%FREE` | Incrementa el tamaño en un 75% del espacio libre del grupo de volúmenes | Sí | Espacio libre del VG |
| `lvresize -r -l 75%FREE` | Fija el tamaño al 75% del espacio libre del grupo de volúmenes | No | Espacio libre del VG |

### Resumen

- **75%VG** ajusta el tamaño del volumen lógico para que ocupe exactamente el 75% del tamaño total del grupo de volúmenes, sin tener en cuenta el tamaño actual.
- **+75%VG** incrementa el tamaño del volumen lógico en un 75% del tamaño total del VG, sumándolo al tamaño actual.
- **+75%FREE** incrementa el tamaño del volumen lógico en un 75% del espacio libre disponible en el VG.
- **75%FREE** ajusta el tamaño del volumen lógico para que ocupe exactamente el 75% del espacio libre del grupo de volúmenes, sin importar su tamaño actual.

# IPs Privadas

### 1. Clase A (Rango privado)

- **Rango de direcciones**: **10.0.0.0** a **10.255.255.255**
- **Bloque privado**: **10.0.0.0/8** (una red de clase A; máscara **255.0.0.0**)
- **Primera IP asignable**: **10.0.0.1** (la dirección **10.0.0.0** es la dirección de red)
- **Última IP asignable**: **10.255.255.254** (la dirección **10.255.255.255** es la dirección de broadcast)
- **Cantidad de direcciones asignables**: Aproximadamente **16.7 millones** de direcciones.
- **Uso típico**: Redes grandes (grandes corporaciones, centros de datos).

### 2. Clase B (Rango privado)

- **Rango de direcciones**: **172.16.0.0** a **172.31.255.255**
- **Bloque privado**: **172.16.0.0/12** (16 redes de clase B; la máscara de clase B es **255.255.0.0**, **/16**)
- **Primera IP asignable**: **172.16.0.1** (la dirección **172.16.0.0** es la dirección de red)
- **Última IP asignable**: **172.31.255.254** (la dirección **172.31.255.255** es la dirección de broadcast)
- **Cantidad de direcciones asignables**: Aproximadamente **1 millón** de direcciones.
- **Uso típico**: Redes de tamaño mediano (universidades, empresas medianas).

### 3. Clase C (Rango privado)

- **Rango de direcciones**: **192.168.0.0** a **192.168.255.255**
- **Bloque privado**: **192.168.0.0/16** (256 redes de clase C; la máscara de clase C es **255.255.255.0**, **/24**)
- **Primera IP asignable**: **192.168.0.1** (la dirección **192.168.0.0** es la dirección de red)
- **Última IP asignable**: **192.168.255.254** (la dirección **192.168.255.255** es la dirección de broadcast)
- **Cantidad de direcciones**: **65 536** en todo el bloque (65 534 asignables si se usa como una sola red /16; en la práctica se divide en redes /24 de 254).
- **Uso típico**: Redes pequeñas (hogares, pequeñas oficinas).

### Resumen

| Clase | Rango de IP | Bloque privado (RFC 1918) | Primera IP asignable | Última IP asignable | Direcciones asignables |
|---|---|---|---|---|---|
| **A** | 10.0.0.0 – 10.255.255.255 | 10.0.0.0/8 | 10.0.0.1 | 10.255.255.254 | ~16.7 millones |
| **B** | 172.16.0.0 – 172.31.255.255 | 172.16.0.0/12 | 172.16.0.1 | 172.31.255.254 | ~1 millón |
| **C** | 192.168.0.0 – 192.168.255.255 | 192.168.0.0/16 | 192.168.0.1 | 192.168.255.254 | ~65,536 |

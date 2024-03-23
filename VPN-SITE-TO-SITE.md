###  Guía General

```markdown
# Configuración de VPN Site-to-Site con IPSec en Router Cisco ISR (IOS 15.x)

La configuración de una VPN site-to-site en un router Cisco con IOS 15.x implica varios pasos principales, incluyendo la configuración de los parámetros de criptografía para IKE Phase 1 y Phase 2, definición del tráfico interesado, y aplicación de la política de criptografía al tráfico.

## Paso 1: Definición de los Parámetros de Criptografía

### Configuración de IKE Phase 1

Primero, define los parámetros para la fase 1 del Intercambio de Claves de Internet (IKE), que establece el canal seguro para negociar la fase 2 de IPSec.

```plaintext
crypto isakmp policy 10
 encryption aes 256
 hash sha256
 authentication pre-share
 group 5
 lifetime 86400
```

- **encryption aes 256**: Usa AES con una clave de 256 bits para encriptación.
- **hash sha256**: Utiliza SHA-256 para hashing.
- **authentication pre-share**: Autenticación mediante llave precompartida.
- **group 5**: Usa el grupo Diffie-Hellman 5 para el intercambio de claves.
- **lifetime 86400**: Establece el tiempo de vida de la asociación de seguridad a 24 horas.

### Definición de Llave Precompartida de ISAKMP

Asigna una llave precompartida que se usará para autenticar el túnel con el sitio remoto.

```plaintext
crypto isakmp key MYKEY address 192.168.1.2
```

- **MYKEY**: Llave precompartida para la autenticación.
- **address 192.168.1.2**: Dirección IP del router remoto.

## Paso 2: Configuración de IPSec (IKE Phase 2)

### Configuración del Conjunto de Transformación de IPSec

Define los parámetros para la fase 2, que protege el tráfico de datos entre los sitios.

```plaintext
crypto ipsec transform-set MYSET esp-aes 256 esp-sha-hmac 
```

- **MYSET**: Nombre del conjunto de transformación.
- **esp-aes 256**: Encriptación AES 256 bits.
- **esp-sha-hmac**: HMAC SHA para integridad.

### Creación de la Política de IPSec

Asocia el conjunto de transformación a una política de IPSec.

```plaintext
crypto map MYMAP 10 ipsec-isakmp
 set peer 192.168.1.2
 set transform-set MYSET
 match address 101
```

- **set peer 192.168.1.2**: Especifica la dirección IP del peer remoto.
- **set transform-set MYSET**: Usa el conjunto de transformación `MYSET`.
- **match address 101**: Asocia la ACL 101 para definir el tráfico interesado.

## Paso 3: Configuración de VPN

### Definición de la ACL para el Tráfico Interesado

Crea una Access Control List (ACL) para identificar el tráfico que pasará por la VPN.

```plaintext
access-list 101 permit ip 10.1.1.0 0.0.0.255 10.2.2.0 0.0.0.255
```

### Aplicación del Crypto Map a la Interfaz Externa

Aplica la política de IPSec a la interfaz que conecta con el sitio remoto.

```plaintext
interface GigabitEthernet0/0
 crypto map MYMAP
```

Con estos pasos, se establecerá una VPN site-to-site segura entre dos sitios utilizando IPSec en routers Cisco con IOS 15.x. Recuerda reemplazar los valores de ejemplo por los específicos de tu red.
```

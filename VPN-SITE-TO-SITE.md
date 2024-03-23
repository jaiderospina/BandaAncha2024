Para configurar una VPN site-to-site utilizando IPSec en routers Cisco, el proceso puede variar ligeramente dependiendo del modelo específico del router y de la versión de IOS que esté utilizando. A continuación, te proporcionaré una guía general que aplica a muchos modelos y versiones de IOS, enfocándome en routers de la serie Cisco ISR (Integrated Services Routers) con IOS 15.x, que son comunes y soportan una amplia gama de funcionalidades de red, incluyendo VPNs IPSec.

Paso 1: Definición de los Parámetros de Criptografía
Definir una Política de ISAKMP (IKE Phase 1):

plaintext
Copy code
crypto isakmp policy 10
 encryption aes 256
 hash sha256
 authentication pre-share
 group 5
 lifetime 86400
Aquí, 10 es el número de prioridad de la política (menor es más prioritario). Se configura AES 256-bits para encriptación, SHA-256 para hash, autenticación mediante llave precompartida, y un grupo Diffie-Hellman de 5. El tiempo de vida es 86400 segundos (24 horas).

Definir una Llave Precompartida de ISAKMP (IKE Phase 1):

plaintext
Copy code
crypto isakmp key 6 [llave] address [IP_remoto]
Donde [llave] es la contraseña compartida y [IP_remoto] es la dirección IP del router remoto.

Paso 2: Configuración de IPSec (IKE Phase 2)
Definir una Transformación de IPSec:

plaintext
Copy code
crypto ipsec transform-set MYSET esp-aes 256 esp-sha-hmac 
MYSET es un nombre arbitrario para el conjunto de transformación, que utiliza AES 256-bits y HMAC-SHA para ESP.

Crear una Política de IPSec:

plaintext
Copy code
crypto ipsec profile MYPROFILE
 set transform-set MYSET
Asigna el conjunto de transformación MYSET al perfil de IPSec MYPROFILE.

Paso 3: Configuración de VPN
Definir una ACL para el Tráfico de Interés:

plaintext
Copy code
access-list 101 permit ip [LAN_local] [Wildcard_LAN_local] [LAN_remota] [Wildcard_LAN_remota]
Esta ACL define qué tráfico será encriptado, donde [LAN_local] y [LAN_remota] son las redes locales y remotas, respectivamente.

Configurar el Crypto Map:

plaintext
Copy code
crypto map MYMAP 10 ipsec-isakmp
 set peer [IP_remoto]
 set transform-set MYSET
 set pfs group5
 match address 101
MYMAP es el nombre del crypto map, que asocia la ACL 101, el conjunto de transformación, y la autenticación PFS con el grupo Diffie-Hellman especificado.

Aplicar el Crypto Map a la Interfaz:

plaintext
Copy code
interface [Interfaz_Externa]
 crypto map MYMAP
Donde [Interfaz_Externa] es la interfaz que se conecta a Internet o a la red externa.

Consideraciones Adicionales
Modelo y IOS: Asegúrate de que el router soporte las funciones necesarias para IPSec. Los modelos ISR con IOS 15.x generalmente lo hacen, pero verifica la documentación específica de tu modelo para detalles precisos.
Seguridad y Rendimiento: Ajusta los parámetros de criptografía según tus requisitos específicos de seguridad y rendimiento.
Compatibilidad: Asegúrate de que la configuración sea compatible con el dispositivo en el otro extremo de la VPN.

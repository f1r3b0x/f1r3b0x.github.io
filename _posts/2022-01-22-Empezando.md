---
title: 'Empezando: distribución y VPN' 
published: true
---

## [](#header-1)Una distro de _pentesting_

Uno de los requisitos para empezar como consultores de seguridad es saber configurar, mantener y asegurar máquinas atacantes (tanto con sistemas Linux como Windows), por lo que a ello vamos. Empezaremos eligiendo una distribución Linux. Como somos principiantes, elegiremos una distro enfocada al _pentesting_ con diversas herramientas ya precargadas. Antes de investigar, yo sólo conocía Kali Linux. Pero tras investigar un poco, he descubierto otras posibilidades como _Parrot Security OS_ o _BlackArch_ y al no tener claro cual instalar me he decantado por Parrot, pues es la que utilizan en HTB y al echar un ojo en Internet he visto personalizaciones que han llamado mi atención.

Una vez decidido, debemos tener en cuenta que lo más óptimo para evitar problemas cuando cometamos un error es realizar nuestra instalación en una máquina virtual.
Por mi parte he tenido algo de suerte, ya que estaba familiarizado de antes con las máquinas virtuales y su instalación. Aunque para probar algo nuevo he decidido usar un _software_ distinto para mi como es VMware (en lugar de VirtualBOX).

Continuando con el curso, se nos habla de la importancia de una buena organización para maximizar la productividad y no perder información. Esto se consigue a través de organización en nuestro sistema de carpetas y mediante la toma de notas/apuntes (función principal de este blog).

También se nos recomienda aprender lenguaje de marcado, que nosotros aprenderemos al ir publicando artículos y mejorarlos en esta web.

## [](#header-2)_Virtual Private Networks_ (VPNs)

Las VPNs nos permiten crear un canal seguro de comunicaciones, cambiando nuestra dirección IP pública y encriptando las comunicaciones a través del canal creado. Además de permitirnos acceder a los equipos y recursos de una red interna.

![Imagen VPNs _Hack The Box_](https://academy.hackthebox.com/storage/modules/77/GettingStarted.png)

Mirándolo por encima podríamos decir que, lo que hace una VPN, es enrutar la conexión de nuestro dispositivo a través del servidor privado elegido en lugar de a nuestr proveedor de servicios de internet (ISP). Es por esto que nuestra IP pública pasa a originarse del servidor VPN.

Principalmente, hay dos tipos de acceso remoto por VPN:
- VPN basada en cliente: requiere el uso de software cliente para establecer la conexión. 
- VPN SSL: utiliza el navegador web como el cliente VPN, por lo que la conexión se establece entre el navegador y una puerta VPN SSL configurable (para limitar el acceso).

En el caso de ser empleados de una empresa, nuestro ordenador podrá disponer de los mismos recursos que si estuviéramos en el trabajo o de una parte limitada para trabajos remotos.

### [](#header-3)Utilidad de una VPN

Una conexión a un servidor VPN nos puede ser útil para:
- Conectarnos a un servidor en otro lugar de nuestro país
- Conectarnos a un servidor en otro país
- Ocultar nuestra IP pública
- Rebasar algunas restricciones de red o cortafuegos al conectar a una posible red hostil

Si bien es cierto que el uso de una VPN aumenta nuestra privacidad y seguridad, no es infalible; ya el proveedor del servicio VPN puede controlar los datos o estar incumpliendo prácticas de seguridad. Por lo tanto, **jamás** debemos pensar que utilizar este servicio oculta totalmente nuestra actividad y nos hace anónimos.

## [](#header-4)VPN de HTB

Los servicios como _Hack The Box_, que ofrecen máquinas vulnerables para practicar, suelen requerir que los jugadores se conecten a la red objetivo mediante una VPN para acceder
al laboratorio privado. Al conectarnos a esta VPN, siempre debemos considerar la red como "hostil". Debido a esto, se recomienda conectar siempre desde una máquina virtual
que no disponga de información comprometedora, bloquear cualquier servidor web y no permitir la autenticación por contraseña si SSH está activado en nuestra máquina atacante.

Al conectarnos a nuestra VPN de HTB utilizamos el siguiente comando en nuestra terminal: `sudo openvpn usuario.ovpn`. Siendo `sudo` el comando para elevar la petición al usuario _root_,
`openvpn` el cliente de la VPN y `user.ovpn` nuestro archivo de clave VPN descargado desde la página de HTB.

Mediante otra terminal, pues deberemos dejar esa trabajando en el proceso, podemos usar el comando `ifconfig` para ver información (IP, máscara de red, etc.) sobre nuestra conexión
en "tunX:". Además con `netstat -rn` podremos ver las redes accesibles mediante la VPN.

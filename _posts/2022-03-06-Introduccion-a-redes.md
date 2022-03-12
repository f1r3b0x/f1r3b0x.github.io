---
title: Introducción a redes
published: true
---

Hoy empezaremos con la teoría de otro módulo de _Hack The Box_, introducción a redes.  
Una red permite a dos ordenadores comunicarse entre ellos. Hay un gran número de topologías (de árbol, en estrella, etc.), medios (ethernet, fibra, coaxial, sin cable, etc.) y 
protocolos (TCP, UDP, IPX, etc.) que se utilizan para proporcionar la red.  
Para ser un buen profesional de seguridad es importante comprender el funcionamiento de las redes para, por ejemplo, poder entender lo que sucede cuando fallan.  

Si bien construir una única red gigante no es difícil; no es demasiado interesante, pues obligar a pivotar a los atacantes por muchas redes pequeñas interconectadas mientras 
intentan pasar desapercibidos los ralentiza mucho más. Veamos algunos ejemplos:

##### [](#header-1)Ejemplo 1

Crear redes pequeñas con Listas de Control de Acceso a su alrededor es como poner una valla alrededor de una propiedad. Si alguien salta la valla, resultará mucho más sospechoso
que si no hubiera valla. ¿Por qué la red de la impresora se está comunicando con servidores a través de HTTP?

##### [](#header-2)Ejemplo 2

Tomarse el tiempo para documentar y mapear el propósito de cada red es como poner luces alrededor de la propiedad, nos aseguramos de que toda actividad sea vista. ¿Por qué la red
de la impresora se comunica con internet?

##### [](#header-3)Ejemplo 3

Si ponemos arbustos debajo de la ventana, reduciremos las posibilidades de que alguien quiera abrirla. De la misma forma los Sistemas de Detección de Intrusiones como `Suricata` o 
`Snort` hacen que un atacante se lo piense dos veces antes de escanear la red. ¿Por qué se ha realizado un escaneo de puertos desde la red de la impresora?

### [](#header-4)Contando una historia de Pentesters

Es muy común que las redes usen una subred /24, por lo que muchos Pentesters establecen su máscara de red como 255.255.255.0 sin hacer comprovaciones. Las /24 permiten a los nodos 
comunicarse con aquellos que tengan los primeros 3 octetos de su dirección IP iguales (ej.: 192.168.1.xxx). Configurar la submáscara de red como /25 dividirá ese rango de
comunicación a la mitad. Ha habido casos en que el consultor ha pensado que un Controlador de Dominio no estaba en línea cuando en realidad estaba en otra subred. La estructura era:  
* Puerta de enlace del servidor: 10.20.0.1/25
* Controlador de dominio: 10.20.0.10/25
* Puerta de enlace del cliente: 10.20.0.129/25
* Dirección IP del Pentester: 10.20.0.252/24 (Configuró la puerta de enlace a 10.20.0.1)

Pese a tener una contraseña robada con `Impacket`, no consiguieron salir de la red de clientes para llegar a objetivos más valiosos. Esto se debe a que no entendieron la red.  

Al finalizar este módulo podremos comprender a la perfección esta historia, así que sigamos.

### [](#header-5)Información básica

![Diagrama de alto nivel trabajo desde casa _Hack The Box_](https://academy.hackthebox.com/storage/modules/34/redesigned/net_overview.png)

Todo Internet se basa en redes subdivididas, como la "Home Network" y "Company Network".  
Un buen símil del funcionamiento de la red es la entrega de mensajería o paquetes, enviados por un ordenador y recibidos por otro.

Al intercambiar datos con un ordenador, debemos saber la dirección a la que deberían ir los paquetes. La dirección del sitio web o _Uniform Resource Locator_ (URL) que entramos en 
nuestro navegador también se conoce como _Fully Qualified Domain Name_ (FQDN). Pero tienen una diferencia:
* Una FQDN como `www.hackthebox.eu` sólo especifica la dirección del "edificio".
* Una URL como `https://hackthebox.eu/example?floor=2&office=dev&employee=17` también especifica la "planta", "oficina" y "empleado" a quién se dirige el paquete.

Al enviar el paquete a través de nuestra oficina de correo (`router`), el paquete se envía a la oficina de correo central (`ISP`). Allí miran el registro de direcciones (`Domain Name
Service`) en que se encuentra la dirección del paquete, y devuelven las coordenadas geográficas (`dirección IP`) para poder dirigir el paquete a su destino.

Una vez que el servidor *web* ha recibido nuestro paquete con la petición para ver su sitio *web*, el servidor nos reenvía el paquete con los datos para la presentación del
sitio a través del `router` de la "Red de la Compañía" a nuestra `dirección IP`.

### [](#header-6)Puntos extra

Lo esperable es que la red de la compañía del diagrama consista de 5 redes separadas:
1. El servidor *web* debe estar en una Zona Desmilitarizada (DMZ) ya que los clientes pueden iniciar comunicaciones con el servidor, haciéndolo más sencillo de comprometer.  
Ponerlo en una red separada permite a los administradores poner proteciones de red entre el servidor *web* y otros dispositivos.
2. Las estaciones de trabajo deberían tener su propia red, y en un mundo perfecto, cada estación debería tener una regla basada en anfitrión en el cortafuegos, para 
prevenir que hable con otras estaciones. Ataques como el `spoofing` o el `man in the middle (MITM)` pueden ser un grave problema si la estación se encuentra en la misma red que
un servidor.
3. El *switch* y el *router* deben estar en una "Red de Administración". Esto previene que las estaciones capturen el tráfico de red entre esos dispositivos. Si vemos la 
advertencia `OSPF` (*Open Shortest Path First*), puede significar que el *router* no tiene una red de confianza; por lo que cualquiera en la red interna podría enviar una
advertencia maliciosa y realizar un ataque `MITM`.
4. Los teléfonos IP también deberían disponer de una red propia. Esto se hace para prevenir que los ordenadores pueden pinchar las llamadas.
5. Las impresoras deben tener su propia red. Es casi imposible segurizar una impresora. Debido a cómo funciona Windows, si una impresora requiere de autenticación durante una
impresión, el computador intentará una autenticación `NTLMv2`, que puede llevar al robo de credenciales. Además, las impresoras son buenas para persistir y, comúnmente, se les
envía mucha información sensible.

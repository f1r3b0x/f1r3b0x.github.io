---
title: Escalada de privilegios
published: true
---

Al acceder a un servidor remoto lo normal es que sea como usuarios no privilegiados, lo que no nos brinda acceso completo a la máquina. Para conseguir el acceso completo, necesitamos
encontrar una vulnerabilidad interna que nos permita escalar nuestros privilegios al usuario `root` en `Linux` o el `administrador/SYSTEM` en `Windows`.  
Veamos algunos métodos comunes para hacerlo.

### [](#header-1)Chuletas PrivEsc

Podemos encontrar instrucciones y chuletas en línea que tienen una colección de instrucciones que podemos seguir y los comandos para seguirlas. Algunos recursos interesantes son
"HackTricks" y "PayloadsAllTheThings". Lo mejor que podemos hacer es experimentar con estas técnicas para escalar nuestros privilegios.

### [](#header-2)_Scripts_ de enumeración

Muchos de los comandos mencionados se pueden lanzar automáticamente con un _script_ para buscar vulnerabilidades. Algunos de los _scripts_ de enumeración para Linux incluyen
"LinEnum" y "linuxprivchecker", y para Windows incluyen "Seatbelt" y "JAWS".

Otra herramienta para esto es la "Privilege Escalation Awesome SUITE (PEASS)", que se mantiene al día en cuanto a _scripts_ para enumeración en Linux y Windows.

**Importante:** algunos de estos _scripts_ pueden hacer mucho "ruido" que puede llamar la atención del anti-virus o del _software_ de seguridad que monitoriza este tipo de eventos.
Por lo que en ocasiones, nos puede interesar realizar la enumeración de forma manual.

### [](#header-3)_Exploits_ del Núcleo (_Kernel_)

Cuando encontramos un servidor que usa un sistema operativo antiguo, debemos buscar las vulnerabilidades pontenciales del _kernel_. Si el servidor no se mantiene con las últimas
actualizaciones y parches, es probable que sea vulnerable a _exploits_ específicos del _kernel_ encontrados en versiones no parcheadas de Linux y Windows.

Podemos buscar en Google o usar `searchsploit` la versión del núcleo, como por ejemplo el `DirtyCow` en Linux 3.9.0-73-generic.

Esto funciona de la misma forma en Windows. Además, es importante tener en cuenta que los _exploits_ del núcleo pueden causar inestabilidad en el sistema, por lo que es importante
tener cuidado si los usamos en sistemas en producción. Lo mejor es probarlos en entornos controlados como laboratiorios, y ejecutarlos únicamente en sistemas en producción con
la aprovación explícita de nuestro cliente.

### [](#header-4)Software Vulnerable

Otra cosa a mirar es el software instalado. En Linux podemos verlo con el comando `dpkg -l` o mirar en `C:\Program Files` en Windows para ver el software instalado. Luego debemos
buscar _exploits_ públicos para cualquier sistema instalado, sobre todo si hay versiones antiguas en uso.

### [](#header-5)Privilegios de Usuario

Otro aspecto crítico que mirar después de ganar acceso a un servidor son los privilegios de que dispone el usuario al que tenemos acceso. Suponinedo que tengamos permitido lanzar
comandos específicos como _root_ (o otro usuario). En ese caso, quizá podamos escalar nuestros privilegios a usuarios _root/system_ o ganar acceso como otro usuario. Algunas maneras
típcas de explotar algunos privilegios de usuario son:  
1. Sudo
2. SUID
3. Windows Token Privileges

El comando `sudo` de Linux permite a un usuario ejecutar comandos como si fuera otro. En ocasiones se permite a usuarios no privilegiados ejecutar comandos como _root_ sin darles
acceso al usuario _root_. Normalmente se hace para lanzar comandos específicos que sólo se puden lanzar como _root_ (como `tcpdump`) o permitir al usuario acceder a ciertos
directorios raíz. Podemos mirar los privilegios que nos da `sudo` con `sudo -l`.  
Con `sudo su -` podemos cambiar al usuario *root*.  
Si al hacer `sudo -l` vemos una entrada con `NOPASSWD`, significa que podemos ejecutar ese comando sin contraseña con `sudo -u <usuario> /bin/echo Hello World!`.

Al encontrar una aplicación particular que podamos lanzar con `sudo`, miraremos formas de explotarla para ganar un intérprete como _root_. Páginas como "GTFOBins" o "LOLBAS" pueden
ayudarnos con esta tarea.

### [](#header-6)Tareas Programadas

Tanto en Linux como en Windows hay métodos para tener _scripts_ corriendo, en intérvalos de tiempo específicos, para llevar a cabo una tarea. Por ejemplo tener un antivirus
realizando un escaneo cada hora. Suele haber 2 maneras de utilizar las tareas programadas (Windows) o trabajos crónicos (Linux) para escalar nuestros privilegios:  
1. Añadir nuevas tareas programadas/trabajos crónicos  
2. Trucarlas para ejecutar software malicioso

La forma más sencilla es comprobar si tenemos permitido añadir tareas programadas nuevas. En Linux, una forma de mantener tareas programadas es mediante `Cron Jobs`. Hay directorios
específicos que quizá podemos usar para añadir nuevos trabajos crónicos si tenemos permisos de escritura sobre ellos. Se incluyen:  
1. /etc/crontab
2. /etc/cron.d
3. /var/spool/cron/crontabs/root

Si podemos escribir en un directorio llamado por un trabajo crónico, escribiremos un _bash script_ con un intérprete de comandos inverso, cuyo trabajo será enviarnos una terminal al
ser ejecutado.

### [](#header-7)Credenciales Expuestas

El siguiente paso será buscar archivos y mirar si contienen credenciales expuestas. Es muy común que las haya en archivos de configuración, archivos de registro y archivos de
historial de usuarios (`bash_history` --> Linux y `PSReadLine` --> Windows). Los _scripts_ de enumeración mencionados anteriormente suelen buscar contraseñas potenciales en los
archivos por nosotros.

Al encontrar una contraseña deberemos utilizarla para iniciar sesión en el servicio correspondiente y contemplar la posibilidad de que el usuario haya reutilizado la misma
credencial como contraseña de _root_.  
También podemos usar las credenciales para iniciar sesión por `ssh` en el servidor como ese usuario.

### [](#header-8)Llaves SSH

Si tenemos acceso de lectura sobre el directorio `.ssh` para un usuario específico, podemos leer sus llaves SSH privadas en `/home/user/.ssh/id_rsa` o `/root/.ssh/id_rsa` y usarlas
para iniciar sesión en el servidor. Si podemos leer el directorio `/root/.ssh/` y el archivo `id_rsa`, podemos copiarlo a nuestra máquina usando:  
`vim id_rsa`  
`chmod 600 id_rsa`  
`ssh <usario>@<IP> -i id_rsa`

Si tenemos acceso de escritura a un directorio `/.ssh/ de usuario, podemos poner nuestra llave pública en el directorio del usuario `/home/<usuario>/.ssh/authorized_keys`. La
finalidad de esto es ganar acceso ssh después de conseguir un intérprete como ese usuario. La configuración SSH actual no acceptará llaves escritas por otros usuarios, así
que sólo funcionará si ya hemos ganado acceso sobre ese usuario.  
Primero crearemos una llave nueva con:  
`ssh-keygen -f key`  

Esto nos proporcionará 2 archivos: `key` (que usaremos con `ssh -i`) y `key.pub`, que copiaremos a la máquina remota. La añadiremos en `/root/.ssh/authorized_keys` con:  
`echo "ssh-rsa AAAAB...SNIP...M= <usuario>@parrot" >> /root/.ssh/authorized_keys`.  
Ahora el servidor remoto nos permitirá iniciar sesión como ese usuario usando nuestra llave privada:  
`ssh root@<IP> -i key`

Para más detalles consultar los módulos *Linux Privilege Escalation* y *Windows Privilege Escalation*.

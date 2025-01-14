---
title: Escaneo de Servicios
published: true
---

Lo primero que debemos hacer al explorar una máquina es identificar el sistema operativo y cualquier servicio disponible que pueda estar ejecutándose. Un servicio es una aplicación 
ejecutando en un ordenador que realiza funciones útiles para otros ordenadores. Las máquinas especializadadas en ejecutar servicios se conocen como "servidores".  
En lugar de realizar las acciones esperadas de ese servicio, a nosotros nos interesa obligar al servicio a hacer algo que nos ayude (como ejecutar un comando).

A los ordenadores se les asigna una dirección IP, que les permite ser identificados en la red de forma única. A los servicios, que ejecutan en esos ordenadores, se les asigna un
número de puerto para hacerlos accesibles. Ese número estará entre el 1 y el 65535, aunque los números del 1 al 1023 se reservan a servicios privilegiados. El puerto 0 pertenece a
la navegación TCP/IP y no se usa en los mensajes TCP o UDP. Si algo intenta trabajar en el puerto 0, se le pasará al siguiente puerto libre a partir del 1024.

Para acceder a un servicio de forma remota, debemos conectarnos con la IP y puerto adecuados y utilizar un lenguaje que el servicio pueda entender. Ya que examinar los 65535 puertos
de forma manual es prácticamente impensable, hay herramientas que lo hacen por nosotros. Como _Network Mapper_ (Nmap).

### [](#header-2)Nmap

Un escaneo básico con `nmap` sería algo así como `nmap <dirección_IP>`. Esto sólo escanearía los 1000 puertos más comunes y nos devolvería aquellos abiertos y su servicio más común.
Por defecto, el escaneo sería únicamente de los puertos TCP.  
En cuanto al estado, no siempre es **abierto** o **cerrado**; también podríamos encontrar un estado **filtrado**. Este se da cuando un cortafuegos sólo permite el acceso desde 
direcciones determinadas.

Con la experiencia notaremos que algunos puertos, como el 3389 (Servicios de Escritorio Remoto), son un indicador del sistema operativo de la máquina objetivo.

En un escaneo más avanzado, podríamos usar parámetros como `-sC` para especificar que `nmap` debería probar sus scripts para obtener información más detallada; `-sV` para realizar
un escaneo de versión, cuya función es identificar el protocolo del servicio, el nombre de la aplicación y la versión; o `-p-` para escanear el rango completo (65535) de puertos
TCP.  
De esta manera, podemos llegar a conocer la versión del sistema operativo objetivo. Por ejemplo mediante la versión de los paquetes que se ejecutan en un puerto, pese a que no es 
100% confiable.

El parámetro `-sC` hace que `nmap` devuelva la página de cabezera y de título del servidor, que nos pueden brindar mucha información al abrirlas en el navegador. En ocasiones, 
nos convendrá que `nmap` ejecute un _script_ en concreto; lo que podemos hacer mediante:  
`nmap --script <nombre del script> -p<puerto> <_host_>`

### [](#header-3)Servicios de ataque de redes

#### [](#header-4)_Banner Grabbing_

Como ya hemos visto anteriormente, leer la cartelera de un servicio es una buena técnica para identificarlo. Esto se puede hacer con `nmap -sV --script=banner <objetivo>`  
o como ya vimos con `Netcat`: `nc -nv <dirección_ip> <puerto>`  
o `nmap -sV --script=banner -p21 <dirección_ip>/<puerto>`

#### [](#header-5)FTP

Este servicio llamado _File Transfer Protocol_ o Protocolo de Transferencia de archivos, suele contener información interesante. Un escaneo `Nmap` del puerto 21 (FTP)
puede revelar la versión instalada de "vsftpd" y además nos dice que la autenticación anónima está activada y que un directorio `pub`.  
Una vez escaneado podemos conectarnos al servicio mediante `ftp -p <dirección_ip>` .

FTP nos permite usar comandos comunes como `cd` y `ls` y nos permite descargar archivos usando el comando `get`. Podemos inspeccionar lo descargado con `cat`.

#### [](#header-6)SMB

El Bloque de Mensajes del Servidor (SMB) es un protocolo de Windows que da vectores para movimineto lateral y vertical. Datos sensibles, como las credenciales, pueden estar en 
los archivos compartidos de la red y algunas versiones de SMB pueden ser vulnerables a exploits RCE (como _EternalBlue_). Es crucial enumerar cuidadosamente esta superfície de
ataque potencial. `Nmap` tiene diversos _scripts_ para enumerar SMB, como `nmap --script smb-os-discovery.nse -p445 <dirección_ip>`.

El marco de trabajo _Metasploit_ tiene diversos módulos para validar y explotar la vulnerabilidad de _EternalBlue_.

##### [](#header-7)_Shares_

SMB permite a los usuarios y administradores compartir carpetas y hacerlas accesibles remotamente para otros usuarios. A menudo esas carpetas contienen información sensible. 
Una herramienta que enumera e interactúa con lo compartido por SMB es `smbclient`. Con el parámetro -L, especificamos que queremos una lista de lo compartido y disponible en el 
anfitrión remoto. Y con -N suprimimos la solicitud de contraseña: 
`smbclient -N -L \\\\<dirección_IP>`

Luego, podemos intentar conectarnos:  
`smbclient \\\\<dirección_ip>\\<nombre_compartición>`  
Es común probar como invitado (_guest_) si no disponemos de credenciales o, en caso contrario, con:  
`smbclient -U <usuario> \\\\<dirección_ip>\\<nombre_compartición>`  
Una vez dentro, al igual que con FTP, podemos usar comandos como `cd`, `ls` o `get` para movernos y descargar archivos.

### [](#header-8)SNMP

Las cadenas de comunidad de SNMP (_Simple Network Management Protocol_) nos dan información y estadísticas acerca de un dispositivo o _router_, con el objetivo de ayudarnos a ganar
acceso a él. En las versiones 1 y 2c de SNMP, se accede mediante una cadena de texto plano; por lo que, si sabemos el nombre, podemos acceder. A partir de la versión 3 de SNMP nos
encontramos con encriptación y autenticación, lo cual dificulta la tarea. Pero examinando los parámetros del proceso podemos encontrarnos credenciales usadas en la línea de comandos.
Credenciales que quizá podamos reutilizar para otros servicios del entorno empresarial. Otras informaciones de interés también se nos podrían revelar con este método.

`snmpwalk -v 2c -c public <dirección_ip> <cadena de comunidad>`  
`snmpwalk -v 2c -c private <dirección_ip>`

Herramientas como `onesixtyone` se pueden usar para encontrar la cadena mediante un diccionario de cadenas comunes (como el incluido en el GitHub de la herramienta):  
`onesixstone -c <archivo_diccionario> <dirección_ip>`

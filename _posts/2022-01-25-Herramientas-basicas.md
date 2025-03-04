---
title: Herramientas básicas
published: true
---

Como podremos ver a continuación, he decidido explicar en este artículo el concepto de servidor _web_. Pienso que es interesante tenerlo presente para clarificar las funciones de
alguna de las herramientas explicadas más adelante.

### [](#header-1)¿Qué es un servidor _web_?

Es una aplicación que corre en el servidor _back-end_, cuya función es encargarse de todo el tráfico HTTP del lado del navegador del cliente, direccionarlo a las páginas de destino
de peticiones y finalmente respondiendo al navegador del cliente. Suelen correr en los puertos TCP 80 o 443 y conectan al usuario final con partes de la aplicación web, manejando sus
posibles respuestas.

Debido a que las aplicaciones _web_ suelen estar abiertas al público, pueden derivar en ataques al servidor _back end_ si este tiene vulnerabilidades. Por lo que suelen ser objetivos de
alto valor para atacantes y _pentesters_.

Hay muchos tipos de vulnerabilidades que pueden afectar a las aplicaciones web. Listas como el top 10 de _Open Web Application Security Project_ (OWASP).

## [](#header-2)Herramientas básicas

Herramientas como `SSH`, `Netcat`, `Tmux` y `Vim/Nvim` son usadas diariamente por la mayoría de los professionales de la ciberseguridad. Por lo tanto, y pese a que no son realmente
herramientas de _pentesting_, son imprescindibles y debemos dominarlas.

### [](#header-3)_Secure Shell (SSH)_

El SSH es un protocolo de red que trabaja por defecto en el puerto 22 y provee a los administradores de sistemas una vía segura para acceder a un ordenador de forma remota.  
Se puede configurar mediante autenticación por contraseña o mediante autenticación por llave pública en conjunto con su pareja SSH pública/privada. SSH puede ser usado para acceder
remotamente a sistemas en la misma red o a través de _Internet_, para facilitar conexiones a recursos en otras redes y subiendo/decargando archivos a y desde sistemas remotos.

SSH utiliza un modelo cliente-servidor que conecta al usuario usando un cliente SSH (como OpenSSH) a un servidor SSH. Si al hacer un ataque obtenemos credenciales SSH en texto claro, 
podemos utilizarlas para entrar al servidor de forma remota mediate `ssh username@server_IP`  
También es posible leer claves locales privadas en sistemas comprometidos o añadir nuestra clave pública para ganar acceso SSH a un usuario específico.

### [](#header-4)_Netcat_

`ncat` o `nc` es una utilidad de red para interactuar con los puertos TCP/UDP. Sus posibles usos durante un _pentest_ son muchos, pero principalmente es para conectarse a terminales.  
También se puede usar para conectar a cualquier puerto en escucha y interactuar con su servicio. Podemos conectar al puerto TCP 22 con `netcat`:  
`netcat IP 22`

Al hacer esto se nos muestra su "cartelera", la cual indica que servicio está corriendo el puerto introducido.  
La alternativa en Windows a este servicio se llama `PowerCat`.  
_Netcat_ también se puede usar para transferir archivos entre máquinas.

Otra utilidad similar a _netcat_ es `socat`, cuyas funciones permiten (al contrario que netcat) reenviar puertos y conectar a dispositivos serie. `Socat` también sirve para 
convertir una _shell_ a un "TTY" totalmente interactivo. Un binario de `socat` puede enviarse a un sistema después de tomar el control remoto para conseguir una conexión _reverse shell_
más estable.

### [](#header-5)Tmux

Los multiplexores de terminales, como `tmux` o `Screen`, sirven para ampliar las funciones de una terminal, como tener diversas ventanas en una terminal e ir saltando entre ellas.

Una vez ejectutado `tmux`, la tecla predefinida para indicar comandos `tmux` es [CTRL + B].  
Para abrir una ventana nueva debemos hacer [CTRL + B] seguido de [CTRL + C].  
Abajo podemos observar las ventanas numeradas. Para cambiar a otra ventana haremos [CTRL + B]
seguido de [CTRL + <numVentana>].  
Podemos partir una ventana verticalmente con [CTRL + %] tras [CTRL + B]. Horizontalmente
sería [CTRL + "].  
Cambiamos de panel mediante [CTRL + B] y [<flechaDir>].

Esto sería lo básico para usar `tmux` pero hay mucho más, como para hacer _logging_.

### [](#header-6)Vim

`Vim` o su fork `Nvim` son editores de texto que se pueden utilizar para escribir código o editar archivos de texto en sistemas Linux. Su particularidad es que funciona íntregramente con
teclado, por lo que no es necesaria la utilización de ratón. Pese a ser difícil de dominar, cuando lo hagamos nuestra productividad y eficiencia se incrementarán exponencialmente.

Abrimos un archivo con: `vim <dirección_archivo>`
Creamos un archivo con: `vim <nombre_archivo>`

Al empezar, nos encontramos en el modo normal. Este nos permite navegar por el archivo, por lo que para entrar en el modo de edición debemos pulsar `i` (entrando al modo de inserción)
. Hay muchos atajos que podemos utilizar en el modo normal, como:
- x --> Cortar caracter
- dw -> Cortar palabra
- dd -> Cortar toda la línea
- yw -> Copiar palabra
- yy -> Copiar toda la línea
- p --> Pegar

Podemos multiplicar el efecto de cada comando añadiendo un número delante. Por ejemplo, '4x' cortaría 4 caracteres.  
También existe el modo de comandos. Al que se accede con `:`. Algunos de sus comandos son:
- :1 --> Ir a la línea 1
- :w --> Guardar el archivo
- :q --> Salir
- :q! -> Forzar salida (sin guardar)
- :wq -> Guardar y salir

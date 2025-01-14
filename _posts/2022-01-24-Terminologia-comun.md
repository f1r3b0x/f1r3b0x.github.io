---
title: Terminología común
published: true
---

En este apartado del curso se nos muestra la terminología más importante para iniciarnos en el campo, como veremos a continuación.

### [](#header-2)¿Qué es un _Shell_/Terminal?

En un sistema Linux, un terminal es un programa que interactúa con el usuario a través de comandos introducidos mediante el teclado y los pasa al sistema operativo (SO).
Antaño era la única manera de comunicarse con los SOs, pero con el tiempo fueron surgiendo versiones gráficas o GUIs (_Graphic User Interfaces_). Estas complementan a las interfaces de 
comandos, pues son más intuitivas para los usuarios comunes pero mucho menos potentes para aquellos con más dominio del sistema.

Los sistemas Linux pueden operar con diversos terminales, como:
- Bash (_Bourne Again Shell_), el terminal por defecto de la mayoría de distros de Linux, evolución del clásico sh de Unix
- Zsh, la cual utilizaré yo por ahora en nuestra máquina _Parrot_
- Tcsh
- Ksh
- Fish shell
- Etc...

También podemos leer acerca de la expresión "_getting a shell_" en referencia a una caja o sistema. Lo que se pretende decir con esto, es que hemos obtenido acceso a nivel de terminal
y podemos lanzar comandos de forma interactiva como si tuviéramos la sesión iniciada en el objetivo. Esto se puede conseguir explotando alguna vulnerabilidad de un servicio o aplicación web
o obteniendo las credenciales para acceder al objetivo de forma remota.

Hay 3 tipus de conexiones a un _shell_:
- _Reverse shell_: Se inicia una conexión a modo de "escucha" en nuestra máquina atacante.
- _Bind shell_: Se "ata" a un puerto específico del objetivo y se espera a una conexión desde nuestra máquina.
- _Web shell_: Se lanzan comandos de SO mediante el navegador web, de forma no interactiva o semi-interactiva. Esto nos deja abierta la posibilidad de jugar con una vulnerabilidad de
               subida de fichero, subiendo un script en lenguaje PHP para lanzar un único comando.

Cadauno de estos terminales tiene sus casos de uso, igual que no sólo podemos apoyarnos en programas escritos en PHP para obtener acceso al terminal. Los lenguajes pueden ser tan diversos
(Python, Bash, Java, C, etc.) como la complejidad y longitud del _script_ utilizado.

### [](#header-3) ¿Qué es un Puerto?

Los puertos son puntos virtuales donde las conexiones de red empiezan y acaban. Para una mayor claridad, un puerto se podría ver como una ventana o puerta a una casa. Para nosotros
son de gran importancia, pues una ventana abierta o mal cerrada nos puede permitir el acceso a la vivienda.      
El SO es el encargado de manejar los puertos y se asocian a procesos o servicios específicos y permiten a los ordenadores diferenciar entre los diversos tipos de tráfico.

A cada puerto se le asigna un número, estando la mayoría estandarizados en todos los dispositivos con conexión a Internet. Esto no quiere decir que un servicio no pueda ser reconfigurado
para redirigirse por otro puerto.

Hay 2 tipos de puertos:
- Protocolo de control de transmisión (TCP): orientado a conexión, lo que significa que una conexión entre el cliente y el servidor debe ser establecida antes de poder enviar datos.
El servidor debe estar en modo de escucha, esperando las solicitudes de conexión por parte de los clientes.
- Protocolo de datagramas de usuario (UDP): usa un modelo de comunicación independiente de la conexión. No requiere que se haya establecido conexión cliente-servidor como en el caso del
TCP. Evidentemente por sí sólo no garantiza la entrega de la información, por lo que es útil cuando no se requiere comprovar la conexión o ya lo hace la aplicación. Esto hace interesante
al UDP cuando la aplicación que lo usa funciona a tiempo real. Pues las comprobaciones del TCP ralentizan la entrega de paquetes.

Hay 65.535 puertos TCP y 65.535 puertos UDP diferentes. Algunos de los puertos más conocidos son:

<html>
<table>
<head>
<tr>
<th>Puertos</th>
<th>Categoría</th>
<th>Protocolo</th>
</tr>
</head>
<body>
<tr>
<td>20/21</td>
<td>TCP</td>
<td>FTP</td>
</tr>
<tr>
<td>22</td>
<td>TCP</td>
<td>SSH</td>
<tr>
<td>23</td>
<td>TCP</td>
<td>Telnet</td> 
</tr>
<tr>
<td>25</td>
<td>TCP</td>
<td>SMTP</td> 
</tr>
<tr>
<td>80</td>
<td>TCP</td>
<td>HTTP</td> 
</tr> 
<tr>
<td>161</td>
<td>TCP/UDP</td>
<td>SNMP</td> 
</tr>
<tr>
<td>389</td>
<td>TCP/UDP</td>
<td>LDAP</td> 
</tr>
<tr>
<td>443</td>
<td>TCP</td>
<td>SSL/TLS (HTTPS)</td> 
</tr>
<tr>
<td>445</td>
<td>TCP</td>
<td>SMB</td> 
</tr>
<tr>
<td>3389</td>
<td>TCP</td>
<td>RDP</td> 
</tr>

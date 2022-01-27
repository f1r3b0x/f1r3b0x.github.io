---
title: Enumeración Web
published: true
---

Al escanear los servicios, es común ver servidores _web_ ejecutando en los puertos 80 y 443. Como ya mencionamos anteriormente, las aplicaciones web que corren en estos servidores
suelen ser objetivos interesantes al realizar un _pentest_. Por lo que veremos que la enumeración _web_ es clave, sobre todo si no hay muchos servicios expuestos o están
bien parcheados.

### [](#header-1)Gobuster

Al descubrir una aplicación _web_, debemos intentar descubrir archivos ocultos o directorios en el servidor que no están destinados al público. Para esto podemos usar herramientas
como `ffuf` o `GoBuster`, cuya función en enumerar sus directorios. Puede que encontremos funciones ocultas o páginas que contengan datos sensibles que podamos usar para llevar a
cabo la penetración.

#### [](#header-2)Enumeración de directorios y/o archivos

`GoBuster` es una herramienta versátil que permite realizar fuerza-bruta sobre directorios, anfitriones virtuales (_Virtual Hosts_ o vhosts) y a las direcciones gestionadas por el
Sistema de Nombres de Dominio (DNS). Pese a tener algunas funcionalidades más, en este punto a nosotros nos interesa la fuerza-bruta sobre archivos y directorios; para lo que
utilizaremos el _switch_ `dir` y un diccionario de palabras comunes:  
`gobuster dir -u http://<dirección_ip> -w /usr/share/dirb/wordlists/common.txt`  
Esto nos devolverá un listado de subdirecciones con un código de estado HTTP.

Algunos de los códigos HTTP, con los que nos convendrá familiarizarnos (quizá con el módulo _Web Requests_), son:  
- 200 --> La petición del recurso ha sido satisfactoria
- 403 --> Tenemos el acceso prohibido a ese recurso
- 301 --> Seremos redirigidos (no es un caso de fallo)

Al realizar la fuerza-bruta es probable que se nos muestre el tipo de Sistema de Gestión de Contenido (CMS), siendo el más común "WordPress", cuya superfície potencial de ataque
es muy grande. Es interesante visitar los subdominios descubiertos, pues en ocasiones descubriremos información o encontraremos que podemos ganar acceso para ejecutar código de forma
remota (RCE).

#### [](#header-3)Enumeración de Subdominios DNS

Existe la posibilidad que haya recursos que puedan ser explotados en los subdominios. Podemos usar `GoBuster` para enumerar los subdominios disponibles de un dominio mediante el 
parámetro `dns`:  
`gobuster dns -d <dirección_dns> -w <diccionario>`

También descargaremos e instalaremos `SecLists`, pues nos brindará diccionarios de interés para lo mencionado en el párrafo anterior. Como, por ejemplo:  
"/usr/share/SecLists/Discovery/DNS/namelist.txt"  
Para más detalles sobre la enumeración y el _fuzzing_ podemos realizar el módulo "_Attacking Web Applications with Ffuf_".

### [](#header-4)Consejos para la enumeración web

Los siguientes consejos nos servirán tanto para la resolución de máquinas en HTB como para el mundo real.

#### [](#header-5)_Banner Grabbing_ / Encabezados de Servidores _Web_

Los encabezados de los servidores _web_ dibujan bien aquello que se aloja en un servidor. Mostrando, por ejemplo, el marco de trabajo específico de la applicación en uso, las opciones
de autenticación y si el servidor carece de alguna medida esencial de seguridad o no está correctamente configurado. Una buena herramienta para extraer la información de los
encabezados de servidores _web_ es `cURL`:  
`curl -IL <dirreción_url>`

Otra herramienta interesante para tomar instantáneas de aplicaciones _web_ objetivo e indentificar datos y posibles credenciales por defecto.

#### [](#header-6)Whatweb

Podemos extraer la versión de un servidor _web_, sus marcos de trabajo y aplicaciones con la ayuda del comando `whatweb`. Conocer la versión de las diversas cosas nos permite 
empezar a buscar vulnerabilidades potenciales. El uso sería:  
`whatweb <dirección_ip>`  
Como la mayoría de las herramientas, `whatweb` tiene muchas opciones según nuestras necesidades y pese a no ser necesario memorizarlas, es conveniente irlas consultando y
recordando mediante la práctica.

#### [](#header-7)Certificados

Los certificados SSL/TLS suelen ser buenas fuentes de información si la página hace uso de HTTPS. Navegar a https://<dirección_ip>/ y ver su certificado puede revelar información
de interés para posibles ataques de _phishing_.

#### [](#header-8)Robots.txt

Muchas páginas web contienen un archivo `robots.txt`, con instrucciones para los motores de búsqueda que indiquen qué se puede usar para indexar y que no. Esto nos permite deducir
que el archivo mencionado puede contener la localización de archivos privados y páginas de administración (desactivadas para su indexación).

#### [](#header-9)Código Fuente

También vale la pena ojear el código fuente de las páginas que encontremos. Mediante [CTRL + U] el navegador nos muestra el código fuente de la página. Aquí podemos llegar a
encontrar información sensible, como credenciales para una cuenta de pruebas.

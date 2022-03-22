# Obligatorio de Redes

## 3 Aplicaciones

### 3.1 Telnet

*** Escriba el comando utilizado ***

> Acceso al equipo usuario y contraseña redes.

```bash
# Por defecto nos conecta al puerto 23
telnet 192.168.56.2

# En caso de especificar el puerto se usa el siguiente comando
telnet 192.168.56.2 23
```
- El usuario y contraseña son "ort-grupo1"

*** ¿Que tipo de tareas puede realizar en el host de destino? ***

> En el host destino puedo tomar control del equipo, ejecutar comandos, revisar directorios, administrar aplicaciones, etc. Por ejemplo:

```bash
Last login: Tue Mar 22 01:00:38 UTC 2022 from 192.168.56.1 on pts/0
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-96-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Mar 22 01:02:18 UTC 2022

  System load:  0.04              Processes:             100
  Usage of /:   89.6% of 2.68GB   Users logged in:       1
  Memory usage: 9%                IP address for enp0s3: 192.168.56.2
  Swap usage:   0%                IP address for enp0s8: 10.0.3.15

  => / is using 89.6% of 2.68GB


0 packages can be updated.
0 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


No mail.
ort-grupo1@servidor_redes:~$ systemctl status ufw.service
ÔùÅ ufw.service - Uncomplicated firewall
   Loaded: loaded (/lib/systemd/system/ufw.service; enabled; vendor preset: enabled)
   Active: active (exited) since Tue 2022-03-22 03:48:20 UTC; 2h 45min left
     Docs: man:ufw(8)
  Process: 401 ExecStart=/lib/ufw/ufw-init start quiet (code=exited, status=0/SUCCESS)
 Main PID: 401 (code=exited, status=0/SUCCESS)

Warning: Journal has been rotated since unit was started. Log output is incomplete or unavailable.
ort-grupo1@servidor_redes:~$ pwd
/home/ort-grupo1
ort-grupo1@servidor_redes:~$ ls /
bin   cdrom  etc   initrd.img      lib    lost+found  mnt  proc  run   snap  sys  usr  vmlinuz
boot  dev    home  initrd.img.old  lib64  media       opt  root  sbin  srv   tmp  var  vmlinuz.old
ort-grupo1@servidor_redes:~$
```

*** ¿Que es necesario para que pueda acceder desde un equipo a otro remoto por Telnet? ***

> Es necesario que el equipo destino tenga habilitado tanto el servicio como el puerto a la escucha.
En el archivo /etc/inetd.conf se encuentra la configuración:

``` bash
#
# Internet superserver configuration database
#
#
# Lines starting with "#:LABEL:" or "#<off>#" should not
# be changed unless you know what you are doing!
#
# If you want to disable an entry so it isn't touched during
# package updates just comment it out with a single '#' character.
#
# Packages should modify this file by using update-inetd(8)
#
# <service_name> <sock_type> <proto> <flags> <user> <server_path> <args>
#
#:INTERNAL: Internal services
#discard                stream  tcp     nowait  root    internal
#discard                dgram   udp     wait    root    internal
#daytime                stream  tcp     nowait  root    internal
#time           stream  tcp     nowait  root    internal

#:STANDARD: These are standard services.
ftp             stream  tcp     nowait  root    /usr/sbin/tcpd  /usr/sbin/in.ftpd
telnet          stream  tcp     nowait  telnetd /usr/sbin/tcpd  /usr/sbin/in.telnetd

```

*** ¿Hasta que capa deben de entenderse los nodos entre si para que el acceso por Telnet sea exitoso? ***

*** Si analiza el trafico capturado con Wireshark: ***
*** ¿Cu´al es el n´umero de puerto de origen y de destino con los que se est´a accediendo? ***

> En la comunicacion el puerto de origen esta en el IP 192.168.56.1 (el pc host) y es el 55381, el puerto de destino es el estandar de Telnet, el puerto 23, en el IP 192.168.56.2 (el equipo virtual).
Se puede ver en la siguiente imagen:

![Imagen wireshark](./assets/telnet1.png)

*** Identifique los paquetes Telnet de intercambio entre el cliente y el servidor. ¿Qu´e informaci´on contienen esos paquetes? ***

> Contiene en texto plano toda la comunicacion entre el cliente y el servidor, en la imagen anterior se pudo ver la comunicacion hacia la maquina virtual, en la siguiente se puede ver la respuesta de la misma:

![Imagen wireshark](./assets/telnet2.png)

> Dentro de toda la conversacion se marca en la misma el estado del ufw, el firewall del equipo.

### 3.2 SMTP: Simple Mail Transport Protocol - RFC 821

*** ¿Qu´e comando debe ejecutar para conectarse, mediante Telnet, al puerto 25 (SMTP) del servidor? ***

- Para conectarnos a la maquina virtual por Telnet, debemos ejecutar el comando:

```bash
telnet 192.168.56.2 25
```

*** Mediante el empleo del nombre asignado a su usuario (ort-grupo1) realice un di´alogo SMTP a su propio usuario y al usuario ort-grupo2. Detalle cu´ales fueron los comandos utilizados en cada caso ***

```bash
helo ort-grupo1
250 servidor_redes.lan
mail from: ort-grupo1@servidor_redes
250 2.1.0 Ok
rcpt to: ort-grupo2@servidor_redes
250 2.1.5 Ok
data
354 End data with <CR><LF>.<CR><LF>
From: "Federico Alonso" <ort-grupo1@servidor_redes>
To: "Usuario Grupo 2" <ort-grupo2@servidor_redes>
Subject: Dialogo para el oblgatorio
Hola ort-grupo2, esta es una prueba de envio de correo.
Saludos Federico Alonso.
.
```
![Imagen SMTP](./assets/smtp1.PNG)

*** Identifique: el sobre, el encabezado y el cuerpo del mensaje. ¿Cu´ales son las diferencias entre el sobre y el encabezado? ***

![Imagen SMTP](./assets/smtp2.png)

> En el sobre no podria mentir los datos, mientras que en el encabezado puedo poner las direcciones que desee.

### 3.3 POP3: Post Office Protocol version 3 - RFC 1939

*** Utilizando el protocolo Telnet, establezca una conexi´on al puerto est´andar del protocolo. Liste el comando utilizado. ***

```bash
telnet 192.168.56.2 110
```

*** Establezca un di´alogo POP3, ingresando con el usuario asignado a su grupo (ort-grupo1), liste sus mensajes y recupere los mismos. ¿Qu´e comandos utiliz´o? ***

```bash
user ort-grupo2
+OK
pass ort-grupo2
+OK Logged in
list
+OK 2 messages:
1 642
2 532
.
retr 1
+OK 642 octets
Return-Path: <ort-grupo1@servidor_redes>
X-Original-To: ort-grupo2@servidor_redes
Delivered-To: ort-grupo2@servidor_redes
Received: from ort-grupo1 (unknown [192.168.56.1])
        by servidor_redes.lan (Postfix) with SMTP id DE943561
        for <ort-grupo2@servidor_redes>; Tue, 22 Mar 2022 01:50:22 +0000 (UTC)
From: "Federico Alonso" <ort-grupo1@servidor_redes>
To: "Usuario Grupo 2" <ort-grupo2@servidor_redes>
Subject: Dialogo para el oblgatorio
Subject: Dialogo para el oblgatorio

Hola ort-grupo2, esta es una prueba de envio de correo.
Saludos Federico Alonso.
.
```
*** Verifique la correcta recepci´on de los mensajes que envi´o a su propio usuario en la parte de SMTP. Borre el ´ultimo mensaje de la casilla. ¿Mediante qu´e comando lo hace? ***

```bash
dele 2
+OK Marked to be deleted.
list
+OK 1 messages:
1 642
.
```

### 3.4 HTTP: Hypertext Transfer Protocol - RFC 1945

*** Establezca una conexi´on al servidor a trav´es del puerto habitual del protocolo HTTP, utilizando la aplicaci´on Telnet. ¿Qu´e comando utiliz´o? ***
*** Recupere la p´agina de prueba usada para el laboratorio, utilizando como URL la direcci´on IP del servidor. Indique el comando utilizado (cuando ejecute el comando, presione dos veces la tecla “enter”). Indique tambi´en la salida obtenida ***
*** Indique el lenguaje en el cual est´a escrita la p´agina ***
*** ¿Con qu´e comando traer´ıa ´unicamente el encabezado de la p´agina? Indique la salida obtenida y comp´arela con la obtenida en el punto anterior ***
*** Acceda mediante un navegador web a la p´agina y compare los resultados obtenidos. ***
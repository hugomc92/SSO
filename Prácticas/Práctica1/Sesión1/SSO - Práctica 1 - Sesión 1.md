# Práctica 1 - Administración de la seguridad en Linux


## Sesión 1 - Seguridad básica en Linux: privilegios de usuario y permisos
#

### **Hugo Maldonado Cózar**
#


### **Ejercicio 1** 
#### Indicar los formatos de los archivos `/etc/passwd`, `/etc/group`, `/etc/shadow` y `/etc/gshadow`.

Para apreciar los formatos de dichos ficheros, vamos a mostrar alguna línea de los mismos y a indicar qué significa cada parte.
```
$ cat /etc/passwd
...
hugomaldonado:x:1000:1000:Hugo Maldonado,,,:/home/hugomaldonado:/bin/bash
...
```
**Formato [[1]](#bibliografía):**
nombre_usuario:contraseña_usuario: id_Usuario(UID): id_grupo(GID): usuario_info:directorio_home: shell_comandos_defecto

```
$ cat /etc/group
...
hugomaldonado:x:1000:
...
```
**Formato [[2]](#bibliografía):**
nombre_grupo:contraseña_grupo:id_grupo(GID):lista_miembros

```
$ sudo cat /etc/shadow
...
hugomaldonado:$6$uZm/jRGM$HwvTwmhZOwvVcL0xE56dnpUQ53Dvp1ACAnwxn0lTbYEJIRtgCdlXhgxrLboCUd24CTOlkQ6PnMpzyjbsJ8sAs1:17054:0:99999:7:::
...
```
**Formato [[3]](#bibliografía):**
nombre_usuario:contraseña_usuario(encriptada): dias_desde_cambio: minimos_dias_requerir_cambio_contraseña: maximos_dias_requerir_cambio_contraseña: dias_para_avisar_expiracion_contraseña: dias_desactivar_cuenta_si_contraseña_expirada: dias_desde_cuenta_expirada

```
$ sudo cat /etc/gshadow
...
hugomaldonado:!::
...
```
**Formato [[4]](#bibliografía):**
nombre_grupo: contraseña_grupo(encriptada): administradores: grupo: miembros_grupo

#
### **Ejercicio 2**
#### Modificar el archivo `/etc/logins.def` para que los usuarios creados a partir de ese momento tengan valores asignados para las directivas `FAILLOG_ENAB` , `PASS_MIN_LENGTH` y `LOGIN_TIMEOUT` . Crear un usuario y comprobar que tienen efectos las citadas directivas.

Primero vamos a ver el fichero con la orden `cat` y a buscar los parámetros que nos insteresa, para luego modificarlo con el editor `nano`:
```
$ cat /etc/logins.def

$ nano /etc/logins.def
```
**FAILLOG_ENAB yes**

Ya estaba predefinido de esta forma
Lo que indica que ya se están registrando en `/var/log/faillog` los inicios de sesión fallidos
Para comprobarlo:
```bash
$ cat /var/log/faillog
$
```
Vemos entonces como en realidad no se están logueando los fallos de inicio de sesión en dicho documento
Investigando en distintas webs, en especial en [[5]](#bibliografía), he encontrado que todos los logs de inicio de sesión se guardan en el fichero `/var/log/auth.log`
Para comprobarlo:

```
$ cat /var/log/auth.log
...
Oct  1 12:01:16 ubuntuserver login[1343]: FAILED LOGIN (1) on '/dev/tty1' FOR 'hugomaldonado', Authentication failure
Oct  1 12:03:44 ubuntuserver login[1388]: pam_unix(login:session): session opened for user hugomaldonado by LOGIN(uid=0)
Oct  1 12:03:44 ubuntuserver systemd-logind[722]: New session 2 of user hugomaldonado.
Oct  1 12:03:44 ubuntuserver login[1388]: pam_unix(login:session): session opened for user hugomaldonado by LOGIN(uid=0)
Oct  1 12:03:44 ubuntuserver systemd-logind[722]: New session 3 of user hugomaldonado.
Oct  1 12:05:21 ubuntuserver sshd[1457]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.1.208  user=hugomaldonado
Oct  1 12:05:23 ubuntuserver sshd[1457]: Failed password for hugomaldonado from 192.168.1.208 port 39844 ssh2
Oct  1 12:05:28 ubuntuserver sshd[1457]: Accepted password for hugomaldonado from 192.168.1.208 port 39844 ssh2
Oct  1 12:05:28 ubuntuserver sshd[1457]: pam_unix(sshd:session): session opened for user hugomaldonado by (uid=0)
Oct  1 12:05:28 ubuntuserver systemd-logind[722]: New session 4 of user hugomaldonado.
```

Además, podemos observar como incluso las intentos de logueo (tanto con éxito como fallidos) a través de SSH tambien se registran.


**PASS_MIN_LEN = 10**

Establecer a 10 el mínimo de caracteres para la contraseña
El mismo fichero nos indica que este parámetro está obsoleto y que se comprueba a través de `pam`

**LOGIN_TIMEOUT 60**

Ya estaba predefinido
En el momento en que se va a loguear, si tarda más del tiempo establecido (60s) en introducir la contraseña, resetea la sesión y hay que introducir de nuevo el nombre de usuario.
 	

Ahora procedemos con la creación del nuevo usuario para comprobar que lo editado está correctamente establecido.

```
$ sudo adduser hugo2
Añadiendo el usuario `hugo2' ...
Añadiendo el nuevo grupo `hugo2' (1001) ...
Añadiendo el nuevo usuario `hugo2' (1001) con grupo `hugo2' ...
El directorio personal `/home/hugo2' ya existe.  No se copiará desde `/etc/skel'.
Introduzca la nueva contraseña de UNIX: 
Vuelva a escribir la nueva contraseña de UNIX: 
passwd: password updated successfully
Changing the user information for hugo2
Enter the new value, or press ENTER for the default
	Full Name []: Hugo 2
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
¿Es correcta la información? [S/n] s

 
$ cat /var/passwd
...
hugomaldonado:x:1000:1000:Hugo Maldonado,,,:/home/hugomaldonado:/bin/bash
hugo2:x:1001:1001:Hugo 2,,,:/home/hugo2:/bin/bash
```
Vemos como se ha creado el usuario con éxito.

Nos logueamos con el nuevo usuario para comprobar los parámetros
```
$ su hugo2
```
De esta forma, el timeout del login no funciona.

Sin embargo, se queda registrado la actividad, tanto los fallos como los éxitos

```
$ cat /var/log/auth.log
Oct  1 12:24:52 ubuntuserver su[1882]: pam_unix(su:auth): authentication failure; logname=hugomaldonado uid=1000 euid=0 tty=/dev/pts/0 ruser=hugomaldonado rhost=  user=hugo2
Oct  1 12:24:54 ubuntuserver su[1882]: pam_authenticate: Authentication failure
Oct  1 12:24:54 ubuntuserver su[1882]: FAILED su for hugo2 by hugomaldonado
Oct  1 12:24:54 ubuntuserver su[1882]: - /dev/pts/0 hugomaldonado:hugo2
Oct  1 12:24:58 ubuntuserver su[1888]: Successful su for hugo2 by hugomaldonado
Oct  1 12:24:58 ubuntuserver su[1888]: + /dev/pts/0 hugomaldonado:hugo2
Oct  1 12:24:58 ubuntuserver su[1888]: pam_unix(su:session): session opened for user hugo2 by hugomaldonado(uid=1000)
Oct  1 12:24:58 ubuntuserver su[1888]: pam_systemd(su:session): Cannot create session: Already running in a session
```

Ahora bien, si en vez de por `su` nos registramos desde la misma máquina, si salta el timeout, y si no se ha introducido la contraseña en menos de 60 segundos, vuelve a obligar la introducción del nombre de usuario

#
### **Ejercicio 3**
#### Crear un ACL para un archivo de vuestro sistema de forma que el usuario creado en el Ejercicio 2 tenga acceso de lectura y escritura.

Lo primero que he tenido que hacer es instalar los paquetes necesarios, ya que la versión de Ubuntu Server 16.04.1 que estoy utilizando no tiene por defecto instalados los paquetes `getfacl` ni `setfacl`

```
$ sudo apt-get install attr
```

Una vez ya instalados, lo que vamos a hacer es, con nuestro propio usuario, crear un fichero de texto para la prueba

```
$ echo "Esto es simplemente una prueba para el ejercicio 4 de la sesión 1" > ./file_ejercicio4

$ cat file_ejercicio4 
Esto es simplemente una prueba para el ejercicio 4 de la sesión 1
```

Ahora, comprobamos el ACL de dicho fichero antes de realizar ninguna modificación

```
$ getfacl file_ejercicio4 
# file: file_ejercicio4
# owner: hugomaldonado
# group: hugomaldonado
user::rw-
group::rw-
other::r--
```

Podemos observar como sólo el usuario `hugomaldonado` tiene acceso de lectura escritura, así como su grupo (es el de por defecto: `hugomaldonado`), mientras que el grupo otros sólo tiene de lectura.

Procedemos a cambiar los ajustes del ACL:

```
$ setfacl -m u:hugo2:rw file_ejercicio4

$ getfacl file_ejercicio4 
# file: file_ejercicio4
# owner: hugomaldonado
# group: hugomaldonado
user::rw-
user:hugo2:rw-
group::rw-
mask::rw-
other::r--
```

Y ahora sí podemos ver como al usuario `hugo2` (creado en el ejercicio 2) se le han dado privilegios de lectura y escritura.

Procedemos a hacer una prueba desde el usuario `hugo2`

``` 
$ echo "Lo he editado con el usuario hugo2" > /home/hugomaldonado/file_ejercicio4 

$ cat /home/hugomaldonado/file_ejercicio4 
Lo he editado con el usuario hugo2
```

Y podemos observar, como el usuario `hugo2` ha podido tanto modificar el archivo como acceder a él para leerlo.

#
### **Ejercicio 4**
#### Mirar los permisos, propietario y grupo de la orden `/usr/bin/passwd` e indicar que llamadas al sistema involucra para que cualquier usuario del sistema pueda modificar su clave que se almacena en un archivo para el cual no tiene permisos explícitos.

Para mirar los permisos de la orden `/usr/bin/passwd` podemos mirarlo de distinas maneras.

La primera y más básica es:

```
$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 54256 mar 29  2016 /usr/bin/passwd
```

La segunda, aprendida en esta práctica es manejarlo con el ACL:

```
$ getfacl /usr/bin/passwd 
# file: usr/bin/passwd
# owner: root
# group: root
# flags: s--
user::rwx
group::r-x
other::r-x
```

En ambos casos podemos observar cómo (tal y como dice el guión de la práctica) el propietario de la orden es el usuario `root`, así como el usuario tiene permisos de lectura, escritura y ejecución, mientras que el grupo y el grupo 'otros' sólo tienen permiso de lectura y ejecución. Además, también podemos observar que, en ambos métodos, tiene el bit de `setuid` activado.
En el método 1 se ve en los primeros bits activos `-rws...` mientras que en el método 2 se ve que está activa en el apartado de banderas `#flags: s--`

Que esta orden tenga activo el bit de `setuid` indica que, cuando se ejecuta y sólo durante la ejecución, el usuario que la ha ejecutado, obtiene los permisos del usuario (o grupo de usuarios) del propietario del fichero, en este caso del `root`, ajustándose los credenciales `euid` y `fsuid` del proceso que lo ha ejecutado a 0 (por ser el `root` el propietario), por lo que ya le permite la ejecución de la orden, en este caso, la modificación de la contraseña de UNIX del usuario que ha ejecutado la orden.

Podemos comprobarlo con la orden `strace`, que nos muestra las llamadas al sistema que se realizan por un determinado programa pasado como argumento:

Para ello, vamos a hacer lo siguiente: vamos a abrir dos terminales conectadas por `ssh` a la máquina, en una nos loguearemos con nuestro principal usuario y en la otra con el que hemos creado con anterioridad.

Una vez logueados, con el usuario creado en el ejercicio 2 vamos a lanzar la orden `passwd` y con el usuario principal vamos a monitorizarlo.


```
Terminal de usuario 2
$ passwd
Cambiando la contraseña de pepe.
(actual) contraseña de Unix:

Terminal de usuario principal (lo ejecutamos todo como root)
# ps -ef | grep passwd
root	1549	0	22:17	tty1	00:00:00	passwd

# strace -p 1549 -o /home/syscall-passwd
strace: Process 1549 attached

#cat /home/syscall-passwd | grep uid
geteuid()                               = 0
setresuid(-1, 1001, -1)                 = 0
setresuid(-1, 0, -1)                    = 0
getuid()                                = 1001
geteuid()                               = 0
setresuid(-1, 1001, -1)                 = 0
getuid()                                = 1001
--- SIGCHLD {si_signo=SIGCHLD, si_code=CLD_EXITED, si_pid=1203, si_uid=1001, si_status=0, si_utime=0, si_stime=0} ---
setresuid(-1, 0, -1)                    = 0

```

Aquí, podemos ver como claramente, entre otras muchas llamadas al sistema (pues el archivo tiene mucha más información), para que esto tenga efecto, el ejecutable /usr/bin/passwd tiene que realizar como mínimo las siguientes llamadas al sistema:

- geteuid(). Que le devuelve 0 pues es el usuario root el propietario del archivo

- setresuid(-1, 1001, -1). Para establecer el euid y  fsuid acorde al flag setuid con el uid del usuario que ha realizado la llamada.

- setresuid(-1, 0, -1). Para lo mismo que arriba pero estableciendo el euid y fsuid a 0 por ser el root el propietario, para otorgarle los permisos temporalmente.

- getuid(). Que devuelve 1001 pues es el uid del usuario que ha invocado la order /usr/bin/passwd. Es el UID Real.

- geteuid(). Ahora se comprueba que se ha cambiado correctamente el UID Efectivo a 0 para tener los privilegios de root

- setresuid(-1, 1001, -1). Para restablecer los permisos del usuario normal y quitar los privilegios de administrador.

- getuid(). Para comprobar que efectivamente se han devuelto los permisos de usuario normal correctamente. Se ve como vuelve a devolver 1001 que es el UID del usuario normal.


#
## Bibliografía 
#

[1] [/etc/passwd](http://geek-university.com/linux/etc-passwd-file-format/)

[2] [/etc/group](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Introduction_To_System_Administration/s3-acctspgrps-group.html)

[3] [/etc/shadow](http://www.cyberciti.biz/faq/understanding-etcshadow-file/)

[4] [/etc/gshadow](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Introduction_To_System_Administration/s3-acctsgrps-gshadow.html)

[5] [fail login](https://ubuntuforums.org/showthread.php?t=1908105)

[6] [setuid](http://man7.org/linux/man-pages/man2/setuid.2.html)

[7] [seteuid](http://man7.org/linux/man-pages/man2/seteuid.2.html)
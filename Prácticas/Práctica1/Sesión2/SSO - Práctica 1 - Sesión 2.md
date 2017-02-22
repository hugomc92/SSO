# Práctica 1 - Administración de la seguridad en Linux


## Sesión 2 - Seguridad básica en Linux: control de acceso al sistema
#

### **Hugo Maldonado Cózar**
#

### **Ejercicio 2.1** 
#### En el sistema que tenemos en uso, indicar los archivos de configuración existentes y comentar la misión de un par de ellos y cómo lo hacen.

Para saber los archivos de configuración de `PAM` que tenemos en nuestro sistema ejecutamos el siguiente comando:

```
$ ls /etc/pam.d/
atd             common-password                other      su
chfn            common-session                 passwd     sudo
chpasswd        common-session-noninteractive  polkit-1   systemd-user
chsh            cron                           runuser    vmtoolsd
common-account  login                          runuser-l
common-auth     newusers                       sshd
```

Aquí podemos ver todos los archivos de configuración correspondientes a diversos servicios, tales como `ssh`, `atd` (comentado en el guión de prácticas), `login`, `newusers` (registrar usuario nuevo), etc.

Vamos a comentar la misión de dos de los mismos y cómo lo hacen:
- **passwd**. Cambiar la contraseña de un usuario del sistema.

Lo primero que vamos a hacer es analizar este fichero:

```
$ cat /etc/pam.d/passwd 
#
# The PAM configuration file for the Shadow `passwd' service
#

@include common-password
```

Podemos ver que es un fichero muy simple, que sólo incluye la configuración establecida en `common-password`. Por tanto, vamos a analizar dicho fichero:

```
$ cat /etc/pam.d/common-password 
# Una vez eliminado slos comentarios del fichero obtenemos
password	[success=1 default=ignore]	pam_unix.so obscure sha512
password	requisite			pam_deny.so
password	required			pam_permit.so
```

Podemos ver que tiene varias líneas con distintas políticas de PAM.

Lo primero que hace es ejecutar la primera línea: `password	[success=1 default=ignore]	pam_unix.so obscure sha512`. Esto nos indica que es un tipo de servicio de contraseña (`password`); que el tipo de control es `[success=1 default=ignore]`, siendo más refinado que los "usuales", nos indica que si el módulo termina con éxito, se salta la primera línea que sigue, con cualquier otro valor de retorno, ejecuta el caso por defecto que indica que esta línea sea ignorada y siga con la siguiente; que el módulo a ejecutar es `pam_unix.so`, lo que nos indica que recuperará información de la cuenta, así como la autenticación para poder ejecutar la orden del proceso involucrado; y los argumentos son `obscure` y `sha512`, que, según podemos ver en la página del manual de `pam_unix` **[[1]](#ref)**, `obscure` hace que se habiliten algunas comprobaciones adicionales en la seguridad de la contraseña; `sha512` indica que la contraseña se encripte usando este algoritmo de encriptación.

La siguiente línea: `password	requisite			pam_deny.so` sólo se ejecutará si la primera línea devuelve algún otro valor distinto de éxito. Esta línea nos indica que es un tipo de servicio de contraseña (`password`); que el tipo de control es `requisite`, lo que indica que inmediatamente se provoca un fallo de autenticación si la llamada al módulo da un fallo, sin llegar a ejecutar el resto de módulos; y que el módulo a ejecutar es `pam_deny.so`, lo que hace que se deniegue el acceso directamente.

La última línea: `password	required			pam_permit.so` sólo será ejecutada si la primera línea devuelve éxito (ya que se habrá saltado la segunda). Esta línea nos indica que es un tipo de servicio de contraseña (`password`); que el tipo de control es `required`, lo que nos indica que dará un fallo de autenticación si la llamada al módulo da un fallo, aunque sí se invocarán el resto de módulos (aunque al ser el último ya no invocará ninguno más); y que el módulo que se ejecutará es `pam_permit.so`, lo que hace que se permita el acceso directamente.


- **runuser**. "Ejecuta un comando con un UID y GUID cambiado" **[[2]](#ref)**.

Lo primero que vamos a hacer es analizar este fichero:

```
$ cat /etc/pam.d/runuser
#%PAM-1.0
auth		sufficient	pam_rootok.so
session		optional	pam_keyinit.so revoke
session		required	pam_limits.so
session		required	pam_unix.so
```

Podemos ver que tiene varias líneas con distintas políticas de PAM.

Lo primero que hace es ejecutar la primera línea: `auth		sufficient	pam_rootok.so`. Esto nos indica que es un tipo de servicio de autenticación (`auth`); que el control es `sufficient`, lo que indica que si esta línea tiene éxito la autenticación retorna inmediatamente éxito sin llegar a ejecutar las líneas posteriores, pero si se produce un fallo sigue con la ejecución de las siguientes líneas; y que el módulo a ejecutar es `pam_rootok.so`, que autentica al usuario si el UID es 0 (verifica si realmente el usuario que lo ha ejecutado es el `root`).

Justo después (y sólo si la primera no ha tenido éxito) ejecuta la siguiente línea: `session		optional	pam_keyinit.so revoke`. Nos indica que es un tipo de servicio de sesión (`session`); que el control es `optional`, lo que indica que si esta línea falla o tiene éxito no es relevante ya que no es la única en el fichero de configuración (que es cuando se ve como un éxito o fallo del sistema de autenticación); que el módulo que ejecuta es `pam_keyinit.so`, lo que indica que "asegura que el proceso involucrado tiene una sesión de claves distinta a la sesión de claves predeterminada del usuario." **[[3]](#ref)**; y que el argumento pasado al módulo es `revoke`, lo que indica que "hace que el anillo de claves de sesión del proceso que invoca sea revocado cuando el proceso involucrado termina si se ha creado el anillo de claves de sesión para este proces en el primer lugar" **[[3]](#ref)**.

Posteriormente ejecuta la siguiente línea: `session		required	pam_limits.so`. Esto nos indica que es un tipo de sevicio de sesión (`session`); que el control es `required` (ya comentado anteriormente); y que el módulo que va a ejecutar es `pam_limits.so`, lo que nos indica que establece los límites de los recursos del sistema que se pueden obtener en una sesión de usuario.

Por último, se ejecuta la siguiente línea: `session		required	pam_unix.so`. Esto nos indica que es un tipo de servicio de sesión(`session`); que el control es `required` (ya comentado anteriormente); y que el módulo que va a ejecutar es `pam_unix.so` (ya comentado anteriormente).

#
### **Ejercicio 2.2** 
#### (a) Modificar la configuración para que se la autenticación exija que la clave de un usuario tenga una longitud mínima. Deberemos utilizar el módulo pam_cracklib ¡Cuidado! pues modificaciones inadecuadas pueden dejar sin acceso a usuarios que existen en el sistema.

Para modificar la configuración para exigir que la clave de usuario tenga una longitud mínima, debemos modificar el archivo de configuración correspondiente a esto, que es `/etc/pam.d/commom-password`. Según podemos ver en la página de manual de `pam_unix` **[[1]](#ref)**, existe un argumento que se le puede pasar a este módulo, que es `minlen=N`, que hace que se pueda especificar un tamaño mínimo para las contraseñas.

```
$ sudo nano /etc/pam.d/common-password

# Cambiamos la línea
password        [success=1 default=ignore]      pam_unix.so obscure sha512

# Y añadimos el parámetro minlen=N para establecer una longitud mínima (yo la estableceré a 8)
password        [success=1 default=ignore]      pam_unix.so obscure sha512 minlen=8
```

Una vez hecho esto, ya tenemos establecido que al longitud mínima de las contraseñas de los usuarios sea 8.

Si intentamos cambiar la contraseña del usuario creado en la sesión anterior podemos comprobarlo:

```
# Conectado como hugo2. Intentando cambiar la contraseña por 1234567 la primera vez y 12345678 posteriormente
$ passwd 
Cambiando la contraseña de hugo2.
(actual) contraseña de UNIX: 
Introduzca la nueva contraseña de UNIX: 
Vuelva a escribir la nueva contraseña de UNIX: 
Debe elegir una contraseña más larga
Introduzca la nueva contraseña de UNIX: 
Vuelva a escribir la nueva contraseña de UNIX: 
passwd: password updated successfully
```

Podemos ver, que habiendo intentado cambiar la contraseña por una de longitud menor a 8 no nos ha dejado diciéndonos que debe ser más larga, y cuando hemos introducido una de longitud 8 sí nos ha dejado.

#### (b) Piensa otra modificación de tu preferencia e implementala. Por ejemplo, deshabilitar el acceso a root directo por consola, evitar que un usuario que no es el root tire el sistema, etc.

Lo que voy a hacer es limitar más concretamente qué usuarios pueden conectarse a través de `ssh` al equipo. Es decir, modificando la configuración del servicio `sshd` voy a estipular los usuarios que sí pueden conectarse por `ssh` y al resto voy a restringirles la conexión.

Lo primero que he hecho ha sido analizar el archivo de configuración de `PAM` referido al `ssh`: `/etc/pam.d/sshd`:

```
$ cat /etc/pam.d/sshd 
# PAM configuration for the Secure Shell service

# Standard Un*x authentication.
@include common-auth

# Disallow non-root logins when /etc/nologin exists.
account    required     pam_nologin.so

# Uncomment and edit /etc/security/access.conf if you need to set complex
# access limits that are hard to express in sshd_config.
#account  required     pam_access.so

# Standard Un*x authorization.
@include common-account

# SELinux needs to be the first session rule.  This ensures that any
# lingering context has been cleared.  Without this it is possible that a
# module could execute code in the wrong domain.
session [success=ok ignore=ignore module_unknown=ignore default=bad]        pam_selinux.so close

# Set the loginuid process attribute.
session    required     pam_loginuid.so

# Create a new session keyring.
session    optional     pam_keyinit.so force revoke

# Standard Un*x session setup and teardown.
@include common-session

# Print the message of the day upon successful login.
# This includes a dynamically generated part from /run/motd.dynamic
# and a static (admin-editable) part from /etc/motd.
session    optional     pam_motd.so  motd=/run/motd.dynamic
session    optional     pam_motd.so noupdate

# Print the status of the user's mailbox upon successful login.
session    optional     pam_mail.so standard noenv # [1]

# Set up user limits from /etc/security/limits.conf.
session    required     pam_limits.so

# Read environment variables from /etc/environment and
# /etc/security/pam_env.conf.
session    required     pam_env.so # [1]
# In Debian 4.0 (etch), locale-related environment variables were moved to
# /etc/default/locale, so read that as well.
session    required     pam_env.so user_readenv=1 envfile=/etc/default/locale

# SELinux needs to intervene at login time to ensure that the process starts
# in the proper default security context.  Only sessions which are intended
# to run in the user's context should be run after this.
session [success=ok ignore=ignore module_unknown=ignore default=bad]        pam_selinux.so open

# Standard Un*x password updating.
@include common-password
```

Tras analizarlo, me he fijado concretamente en la siguiente parte:

```
# Uncomment and edit /etc/security/access.conf if you need to set complex
# access limits that are hard to express in sshd_config.
#account  required     pam_access.so 
```

Investigando en la página de manual de `pam_acces.so` **[[4]](#ref)** he encontrado que hay un argumento que se le puede pasar a este módulo, en concreto es `accessfile`, para que se pueda establecer la configuración, por tanto vamos a modificar este archivo de configuración y establecerlo en la política de `PAM` para establecer la configuración que vamos a necesitar. Vemos que el fichero que tenemos que modificar es `/etc/security/access.conf`.

```
$ cat /etc/security/access.conf 
# Login access control table.
#
# Comment line must start with "#", no space at front.
# Order of lines is important.
#
# When someone logs in, the table is scanned for the first entry that
# matches the (user, host) combination, or, in case of non-networked
# logins, the first entry that matches the (user, tty) combination.  The
# permissions field of that table entry determines whether the login will
# be accepted or refused.
#
# Format of the login access control table is three fields separated by a
# ":" character:
#
# [Note, if you supply a 'fieldsep=|' argument to the pam_access.so
# module, you can change the field separation character to be
# '|'. This is useful for configurations where you are trying to use
# pam_access with X applications that provide PAM_TTY values that are
# the display variable like "host:0".]
#
# 	permission : users : origins
#
# The first field should be a "+" (access granted) or "-" (access denied)
# character.
#
# The second field should be a list of one or more login names, group
# names, or ALL (always matches). A pattern of the form user@host is
# matched when the login name matches the "user" part, and when the
# "host" part matches the local machine name.
#
# The third field should be a list of one or more tty names (for
# non-networked logins), host names, domain names (begin with "."), host
# addresses, internet network numbers (end with "."), ALL (always
# matches), NONE (matches no tty on non-networked logins) or
# LOCAL (matches any string that does not contain a "." character).
#
# You can use @netgroupname in host or user patterns; this even works
# for @usergroup@@hostgroup patterns.
#
# The EXCEPT operator makes it possible to write very compact rules.
#
# The group file is searched only when a name does not match that of the
# logged-in user. Both the user's primary group is matched, as well as
# groups in which users are explicitly listed.
# To avoid problems with accounts, which have the same name as a group,
# you can use brackets around group names '(group)' to differentiate.
# In this case, you should also set the "nodefgroup" option.
#
# TTY NAMES: Must be in the form returned by ttyname(3) less the initial
# "/dev" (e.g. tty1 or vc/1)
#
##############################################################################
#
# Disallow non-root logins on tty1
#
#-:ALL EXCEPT root:tty1
#
# Disallow console logins to all but a few accounts.
#
#-:ALL EXCEPT wheel shutdown sync:LOCAL
#
# Same, but make sure that really the group wheel and not the user
# wheel is used (use nodefgroup argument, too):
#
#-:ALL EXCEPT (wheel) shutdown sync:LOCAL
#
# Disallow non-local logins to privileged accounts (group wheel).
#
#-:wheel:ALL EXCEPT LOCAL .win.tue.nl
#
# Some accounts are not allowed to login from anywhere:
#
#-:wsbscaro wsbsecr wsbspac wsbsym wscosor wstaiwde:ALL
#
# All other accounts are allowed to login from anywhere.
#
##############################################################################
# All lines from here up to the end are building a more complex example.
##############################################################################
#
# User "root" should be allowed to get access via cron .. tty5 tty6.
#+ : root : cron crond :0 tty1 tty2 tty3 tty4 tty5 tty6
#
# User "root" should be allowed to get access from hosts with ip addresses.
#+ : root : 192.168.200.1 192.168.200.4 192.168.200.9
#+ : root : 127.0.0.1
#
# User "root" should get access from network 192.168.201.
# This term will be evaluated by string matching.
# comment: It might be better to use network/netmask instead.
#          The same is 192.168.201.0/24 or 192.168.201.0/255.255.255.0
#+ : root : 192.168.201.
#
# User "root" should be able to have access from domain.
# Uses string matching also.
#+ : root : .foo.bar.org
#
# User "root" should be denied to get access from all other sources.
#- : root : ALL
#
# User "foo" and members of netgroup "nis_group" should be
# allowed to get access from all sources.
# This will only work if netgroup service is available.
#+ : @nis_group foo : ALL
#
# User "john" should get access from ipv4 net/mask
#+ : john : 127.0.0.0/24
#
# User "john" should get access from ipv4 as ipv6 net/mask
#+ : john : ::ffff:127.0.0.0/127
#
# User "john" should get access from ipv6 host address
#+ : john : 2001:4ca0:0:101::1
#
# User "john" should get access from ipv6 host address (same as above)
#+ : john : 2001:4ca0:0:101:0:0:0:1
#
# User "john" should get access from ipv6 net/mask
#+ : john : 2001:4ca0:0:101::/64
#
# All other users should be denied to get access from all sources.
#- : ALL : ALL
```

Vemos como todo está comentado, lo cual nos indica que se están permitiendo todas las opciones, sin establecerse ninguna restricción, pero nos indica cómo podemos hacer para establecer la configuración que necestiamos. Por tanto:

```
# Añadimos la configuración al final de dicho fichero
echo "+ : hugomaldonado : ALL" >> /etc/security/access.conf
echo "- : ALL : ALL" >> /etc/security/access.conf
```

La primera línea es la que va a hacer que el usuario `hugomaldonado` pueda conectarse, mientras que la segunda que hemos añadido hará que el resto de usuarios no puedan conectarse a través de `ssh`.

Ahora debemos actualizar el fichero de configuración de `PAM` del servicio `sshd`:

```
$ sudo nano /etc/pam.d/sshd

# Descomentamos y modificamos esta línea
#account  required     pam_access.so

# Quedando finalmente así
account  required     pam_access.so accessfile=/etc/security/access.conf
```

Ahora, comprobamos que los cambios han surgido efecto. Vamos a tratar de conectar al usuario `hugo2` a través de `ssh`:

```
$ ssh hugo2@192.168.1.74
hugo2@192.168.1.74's password: 
Permission denied, please try again.
hugo2@192.168.1.74's password: 
Permission denied, please try again.
hugo2@192.168.1.74's password: 
Permission denied (publickey,password).
```

Como podemos observar, este usuario, que en otros ejercicios tanto de esta sesión como de la anterior hemos podido conectar por `ssh` ya no puede conectarse a través de este medio. Sin embargo:

```
$ ssh hugomaldonado@192.168.1.74                       255 
hugomaldonado@192.168.1.74's password: 
Welcome to Ubuntu 16.04.1 LTS (GNU/Linux 4.4.0-38-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

Pueden actualizarse 0 paquetes.
0 actualizaciones son de seguridad.


Last login: Sun Oct  6 16:00:51 2016 from 192.168.1.71
```

Podemos ver como al usuario que sí le hemos dado permiso para conectarse lo hace sin problemas.

Ahora bien, según hemos estipulado, para permitir que cualquier otro usuario pueda conectarse por `ssh` tendremos que añadirlo al fichero de configuración `/etc/security/access.conf` y añadir la regla `+ : nombre_usuario : ALL`. Mientras un usuario no tenga específicamente esta regla en este fichero de configuración, no podrá conectarse a través de `ssh` al equipo.

#
### **Ejercicio 2.3** 
#### Crear en el sistema un usuario con las características que deseéis, entrando como ese usuario cambiar la contraseña y analizar los archivos log para ver el mensaje correspondiente.

Para este ejercicio vamos a utilizar el usuario que creamos en la sesión anterior y que ya hemos usado con anterioridad en esta sesión.

```
$ cat /var/passwd
...
hugomaldonado:x:1000:1000:Hugo Maldonado,,,:/home/hugomaldonado:/bin/bash
hugo2:x:1001:1001:Hugo 2,,,:/home/hugo2:/bin/bash
```

En concreto, nos referimos al usuario sin privilegio alguno, `hugo2`.

A pesar de que en el guión se especifica que las modificaciones o intentos de modificaciones se registrar en el archivo `/var/log/messages`, en `Ubuntu Server 16.04.1` que estoy usando no existe dicho fichero.
Investigando por los ficheros, he visto que tanto los cambios como los intentos quedan registrados en el archivo `/var/log/auth.log`

```
# Conectado como hugo2 he ejecutado lo siguiente
# Cambio correcto de contraseña
$ passwd 
Cambiando la contraseña de hugo2.
(actual) contraseña de UNIX: 
Introduzca la nueva contraseña de UNIX: 
Vuelva a escribir la nueva contraseña de UNIX: 
passwd: password updated successfully

# Intento fallido de cambio de contraseña, al intentar meter varias veces contraseñas demasiado cortas
hugo2@ubuntuserver:~$ passwd 
Cambiando la contraseña de hugo2.
(actual) contraseña de UNIX: 
Introduzca la nueva contraseña de UNIX: 
Vuelva a escribir la nueva contraseña de UNIX: 
Debe elegir una contraseña más larga
Introduzca la nueva contraseña de UNIX: 
Vuelva a escribir la nueva contraseña de UNIX: 
Debe elegir una contraseña más larga
Introduzca la nueva contraseña de UNIX: 
Vuelva a escribir la nueva contraseña de UNIX: 
Debe elegir una contraseña más larga
passwd: Error de manipulación del testigo de autenticación
passwd: password unchanged

# Conectado como hugomaldonado he ejecutado lo siguiente
$ cat /var/log/auth.log 
...
Oct  6 22:36:03 ubuntuserver passwd[9142]: pam_unix(passwd:chauthtok): password changed for hugo2
Oct  6 22:36:43 ubuntuserver passwd[9147]: pam_unix(passwd:chauthtok): new password not acceptable
```

Por lo tanto, se puede ver como tanto las modificaciones de contraseña de los usuarios, como los intentos fallidos de cambios de contraseña se quedan registrados en dicho fichero.

#
### **Ejercicio 2.4** 
#### Modificar el archivo sudoers para que un usuario determinado tenga acceso a todas las órdenes del root.

Lo primero que vamos a hacer es demostrar que el usuario creado, no tiene privilegios `root` para nada. Para ello:

```
# Conectado como hugo2
$ cat /etc/sudoers
cat: /etc/sudoers: Permiso denegado
$ sudo cat /etc/sudoers
[sudo] password for hugo2: 
hugo2 no está en el archivo sudoers. Se informará de este incidente.
```

Aquí se ve claramente, que el acceso al fichero `/etc/sudoers` sólo puede hacerse con permisos de administrador, pero el usuario `hugo2` no los tiene, por lo que no puede ver el contenido del mismo.

Para darle permiso al usuario `hugo2`, vamos a modificar el archivo `/etc/sudoers` para así establecerlo:

```
# Conectado como hugomaldonado (que si tiene permisos root)
$ sudo nano /etc/sudoers

#Añadimos al final la siguiente línea
hugo2   ALL=(ALL:ALL) ALL

# Guardamos y cerramos
```

Ahora el usuario `hugo2` está dentro de la lista de usuarios permitidos para ejecutar acciones como administrador, es más, le hemos dado privilegios para que ejecute cualquier orden como administrador mediante `sudo`. Para corroborarlo:

```
# Conectado como hugo2
$ sudo cat /etc/sudoers
[sudo] password for hugo2: 
...
hugo2	ALL=(ALL:ALL) ALL
```

Ahora vemos, que el usuario `hugo2` sí ha podido acceder al archivo al que antes no se le permitía por falta de privilegios, y vemos como la línea que hemos introducido anteriormente con el usuario que sí los tenía está en dicho archivo y funciona correctamente.


#
### **Ejercicio 2.5** 
#### Analiza el contenidos de estos archivos de registro del sistema de prácticas y comprueba que efectivamente se registran los eventos indicados.

Para empezar, comprobando en mi máquina (`Ubuntu Server 16.04.1`) he detectado que el archivo `/etc/syslog.conf` no existe, en **[[5]](#ref)** he encontrado que es porque en `Ubuntu` desde versiones muy antiguas (pues el post es de 2011), no se usa ese fichero de configuración, ya que Ubuntu utiliza `rsyslog` en vez de `syslog`, por lo que los archivos de configuración son los que se establecen en `/etc/rsyslog.conf` y en dicho archivo muestra que las reglas por defecto de `rsyslog` se encuentran en `/etc/rsyslog.d/50-default.conf`.
Por tanto, vamos a analizar el fichero `/etc/rsyslog.d/50-default.conf` para ver algunas de las opciones de `log` que se están registrando

```
$ cat /etc/rsyslog.d/50-default.conf 
#  Default rules for rsyslog.
#
#			For more information see rsyslog.conf(5) and /etc/rsyslog.conf

#
# First some standard log files.  Log by facility.
#
auth,authpriv.*			/var/log/auth.log
*.*;auth,authpriv.none		-/var/log/syslog
#cron.*				/var/log/cron.log
#daemon.*			-/var/log/daemon.log
kern.*				-/var/log/kern.log
#lpr.*				-/var/log/lpr.log
mail.*				-/var/log/mail.log
#user.*				-/var/log/user.log
...
```

Podemos ver como (tal y como hemos comentado tanto en la sesión anterior como en esta en ejercicios anteriores) todos los `logs` relacionados con la autenticación se guardan en `/var/log/auth.log` (como ya hemos comprobado con anterioridad). También vemos que hay opciones comentadas (como las de `cron` o `daemon`) lo que indica que no se están produciendo los registros de dichas órdenes.

Me voy a centrar en la regla `kern.* -/var/log/kern.log`, que indica que todos los mensajes del kernel (tanto buenos como errores y demás) se están guardando en `/var/log/kern.log`

```
$ cat /var/log/kern.log 
...
Oct  6 21:57:47 ubuntuserver kernel: [    7.769342] audit: type=1400 audit(1475956667.360:8): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/NetworkManager/nm-dhcp-helper" pid=670 comm="apparmor_parser"
Oct  6 21:57:47 ubuntuserver kernel: [    7.769345] audit: type=1400 audit(1475956667.360:9): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/connman/scripts/dhclient-script" pid=670 comm="apparmor_parser"
Oct  6 21:57:47 ubuntuserver kernel: [    7.770636] audit: type=1400 audit(1475956667.360:10): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/bin/lxc-start" pid=671 comm="apparmor_parser"
Oct  6 21:57:47 ubuntuserver kernel: [    7.772499] audit: type=1400 audit(1475956667.364:11): apparmor="STATUS" operation="profile_load" profile="unconfined" name="/usr/lib/lxd/lxd-bridge-proxy" pid=672 comm="apparmor_parser"
Oct  6 21:57:47 ubuntuserver kernel: [    7.792561] Console: switching to colour frame buffer device 100x37
Oct  6 21:57:47 ubuntuserver kernel: [    7.793652] vboxvideo 0000:00:02.0: fb0: vboxdrmfb frame buffer device
Oct  6 21:57:47 ubuntuserver kernel: [    7.861658] [drm] Initialized vboxvideo 1.0.0 20130823 for 0000:00:02.0 on minor 0
Oct  6 21:57:47 ubuntuserver kernel: [    8.061683] cgroup: new mount options do not match the existing superblock, will be ignored
Oct  6 21:57:47 ubuntuserver kernel: [    8.207611] e1000: enp0s3 NIC Link is Up 1000 Mbps Full Duplex, Flow Control: RX
Oct  6 21:57:47 ubuntuserver kernel: [    8.211934] IPv6: ADDRCONF(NETDEV_UP): enp0s3: link is not ready
Oct  6 21:57:47 ubuntuserver kernel: [    8.229094] IPv6: ADDRCONF(NETDEV_CHANGE): enp0s3: link becomes ready
Oct  6 21:57:48 ubuntuserver kernel: [    8.575250] ppdev: user-space parallel port driver
Oct  6 21:57:49 ubuntuserver kernel: [   10.279484] floppy0: no floppy controllers found
Oct  6 22:23:45 ubuntuserver kernel: [ 1564.393994] e1000: enp0s3 NIC Link is Down
Oct  6 22:23:51 ubuntuserver kernel: [ 1570.373603] e1000: enp0s3 NIC Link is Up 1000 Mbps Full Duplex, Flow Control: RX
```

Podemos observar que todos los mensajes del kernel se están almacenando ahí correctamente.

Otra interesante es la regla `mail.* -/var/log/mail.log` ya que indica que todo lo relacionado con el mail (la funcion `mail` del sistema) se está almacenando en `/var/log/mail.log`, pero como aún no he usado esta función no tengo siquiera el fichero creado.

```
$ ls /var/log/
alternatives.log  btmp          faillog    lastlog              wtmp
apt               dist-upgrade  fsck       lxd
auth.log          dmesg         installer  syslog
bootstrap.log     dpkg.log      kern.log   unattended-upgrades
```

Por eso he decidido comentarla también, para indicar que aunque esté estipulado en la configuración del `rsyslog`, si alguna regla no se ha tenido que usar desde el comienzo hasta la actualidad, no se tiene por qué haber creado siquiera el fichero que se indique en la misma.

#
### **Ejercicio 2.6** 
#### Analizar las conexiones al sistema de prácticas y al de casa. ¿hay o ha habido alguna conexión ajena al equipo?

En este caso, dado que lo estoy realizando en mi propio ordenador, y he llegado a este ejercicio con posterioridad a la clase de prácticas donde se trató esta sesión, sólo voy a analizar las conexiones de mi ordenador.

Para ello:

```
$ last
hugo2    pts/1        192.168.1.71     Sat Oct  6 22:35   still logged in
hugomald pts/0        192.168.1.71     Sat Oct  6 22:35   still logged in
hugo2    pts/1        192.168.1.71     Sat Oct  6 22:13 - 22:35  (00:21)
hugomald pts/0        192.168.1.71     Sat Oct  6 22:01 - 22:24  (00:23)
hugomald tty1                          Sat Oct  6 22:01    gone - no logout
reboot   system boot  4.4.0-38-generic Sat Oct  6 21:57   still running
hugomald pts/0        192.168.56.1     Tue Oct  4 12:45 - 14:19  (01:34)
hugomald tty1                          Tue Oct  4 12:43 - down   (01:35)
reboot   system boot  4.4.0-38-generic Tue Oct  4 12:42 - 14:19  (01:36)
reboot   system boot  4.4.0-38-generic Tue Oct  4 12:37 - 14:19  (01:42)
...

wtmp begins Thu Sep 29 12:46:18 2016
```

Podemos ver como todas las entradas y salidas producidas en esta máquina han sido las correctas, es decir, sólo han sido desde el usuario `hugomaldonado` o `hugo2` a través del propio sistema (`tty1` por ejemplo) o bien por `ssh` (`pts/0` o `pts/1`).

No obstante, vamos a analizar también si ha habido algun intento fallido de conexión:

```
$ sudo lastb 

btmp begins Thu Sep 29 12:46:19 2016
```

Como el fichero relacionado a dicha orden (`/var/log/btmp`) está vacío, podemos descartar que este tiempo haya habido intentos de conexión fallidos a nuestro equipo.

Por último, vamos a comprobar también los intentos de conexión de red:

```
$ lastlog 
Username         Port     From             Latest
root                                       **Never logged in**
...
hugomaldonado    pts/0    192.168.1.71     sáb oct  6 22:35:00 +0200 2016
hugo2            pts/1    192.168.1.71     sáb oct  6 22:35:47 +0200 2016
```
Aquí podemos ver lo mismo (además de ver que el usuario root en sí no se ha conectado nunca ni desde el sistema ni por `ssh`), es decir, que las únicas conexiones que ha habido al equipo han sido por los dos únicos usuarios que hay y en tiempos en los que es normal que se hayan producido ya que se estaban realizando las prácticas.

Por lo que podemos descartar que haya habido conexiones ajenas no autorizadas.

#
## Bibliografía <a id="ref"></a>
#

[1] [pam_unix](http://manpages.ubuntu.com/manpages/precise/man8/pam_unix.8.html)

[2] [runuser](http://man7.org/linux/man-pages/man1/runuser.1.html)

[3] [pam_keyinit](http://man.he.net/man8/pam_keyinit)

[4] [pam_access](https://linux.die.net/man/8/pam_access)

[5] [syslog Ubuntu](http://askubuntu.com/questions/42152/where-is-syslog-conf)
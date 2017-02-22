# Práctica 1 - Administración de la seguridad en Linux


## Sesión 4 - Seguridad básica en Linux: AppAmor

### **Hugo Maldonado Cózar**
# <a></a>


### **Ejercicio 4.1**
#### Determinar los perfiles activos en la distribución Linux de tu equipo. Elige uno de los perfiles y analiza sus características.

Para determinar los perfiles activos de nuestra distribución podemos ejecutar el siguiente comando:

```bash
$ sudo apparmor_status		# Es igual a sudo aa-status
apparmor module is loaded.
13 profiles are loaded.
13 profiles are in enforce mode.
   /sbin/dhclient
   /usr/bin/lxc-start
   /usr/lib/NetworkManager/nm-dhcp-client.action
   /usr/lib/NetworkManager/nm-dhcp-helper
   /usr/lib/connman/scripts/dhclient-script
   /usr/lib/lxd/lxd-bridge-proxy
   /usr/lib/snapd/snap-confine
   /usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /usr/sbin/tcpdump
   lxc-container-default
   lxc-container-default-cgns
   lxc-container-default-with-mounting
   lxc-container-default-with-nesting
0 profiles are in complain mode.
1 processes have profiles defined.
1 processes are in enforce mode.
   /sbin/dhclient (2330) 
0 processes are in complain mode.
0 processes are unconfined but have a profile defined.
```

Aquí, podemos ver como tenemos 13 perfiles activados, todos ellos en modo `enforce`. También podemos ver como actualmente sólo un proceso de los que hay ejecutándose en este momento en el sistema está establecido para ejecutar un perfil de `App Armor` y es el servicio cliente `DHCP` que está en `/sbin/dhclient`.

Si ejecutamos:

```
$ ps auxZ | grep -v '^unconfined'
LABEL                           USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
/sbin/dhclient (enforce)        root       885  0.0  0.0  16120   860 ?        Ss   16:56   0:00 /sbin/dhclient -1 -v -pf /run/dhclient.enp0s5.pid -lf /var/lib/dhcp/dhclient.enp0s5.leases -I -df /var/lib/dhcp/dhclient6.enp0s5.leases enp0s5
```

Podemos observar como es cierto, y el único proceso que está ejecutando un perfil de `App Armor` es el citado anteriormente.

Tal y como podemos ver en la página de manual de `AppArmor` (**[[1]](#ref)**), los perfiles se guardan como texto plano en el directorio `/etc/apparmor.d` o bien en `/var/lib/apparmor/profiles/`, por tanto:

```bash
$ ls /etc/apparmor.d/
abstractions    local           tunables                      usr.sbin.rsyslogd
cache           lxc             usr.bin.lxc-start             usr.sbin.tcpdump
disable         lxc-containers  usr.lib.lxd.lxd-bridge-proxy
force-complain  sbin.dhclient   usr.lib.snapd.snap-confine

$ ls /var/lib/apparmor/profiles/
```

Podemos ver que, en este caso, todos los perfiles están guardados en `/etc/apparmor.d`, ya que vemos que el otro directorio nombrado está vacío.

Además se puede ver cómo están los mismos perfiles que hemos listado anteriormente con el comando `apparmor_status`. Hay que decir, que aunque hay menos archivos, en realidad hay más perfiles, bien porque se encuentran definididos dentro de carpetas, como es el caso del directorio ´lxc´ o bien porque un archivo los contenga, como es el caso del `snapd`, que si bien con el comando nos muestra que tenemos dos perfiles, sólo tenemos un archivo en el directorio.

El perfil que vamos a analizar es el asociado a `tcpdump`, es decir, `usr.sbin.tcpdump`:

```bash
$ cat usr.sbin.tcpdump 
# vim:syntax=apparmor
# Last Modified: Wed Feb  3 07:58:30 2009
# Author: Jamie Strandboge <jamie@canonical.com>
#include <tunables/global>

/usr/sbin/tcpdump {
  #include <abstractions/base>
  #include <abstractions/nameservice>
  #include <abstractions/user-tmp>

  capability net_raw,
  capability setuid,
  capability setgid,
  capability dac_override,
  network raw,
  network packet,

  # for -D
  capability sys_module,
  @{PROC}/bus/usb/ r,
  @{PROC}/bus/usb/** r,

  # for finding an interface
  @{PROC}/[0-9]*/net/dev r,
  /sys/bus/usb/devices/ r,
  /sys/class/net/ r,
  /sys/devices/**/net/* r,

  # for -j
  capability net_admin,

  # for tracing USB bus, which libpcap supports
  /dev/usbmon* r,
  /dev/bus/usb/ r,
  /dev/bus/usb/** r,

  # for init_etherarray(), with -e
  /etc/ethers r,

  # for USB probing (see libpcap-1.1.x/pcap-usb-linux.c:probe_devices())
  /dev/bus/usb/**/[0-9]* w,

  # for -z
  /bin/gzip ixr,
  /bin/bzip2 ixr,

  # for -F and -w
  audit deny @{HOME}/.* mrwkl,
  audit deny @{HOME}/.*/ rw,
  audit deny @{HOME}/.*/** mrwkl,
  audit deny @{HOME}/bin/ rw,
  audit deny @{HOME}/bin/** mrwkl,
  owner @{HOME}/ r,
  owner @{HOME}/** rw,

  # for -r, -F and -w
  /**.[pP][cC][aA][pP] rw,

  # for convenience with -r (ie, read pcap files from other sources)
  /var/log/snort/*log* r,

  /usr/sbin/tcpdump r,

  # Site-specific additions and overrides. See local/README for details.
  #include <local/usr.sbin.tcpdump>
}
```

Analizando el fichero del perfil, podemos ver varias características del mismo:

**Incluye varios componentes de propósito general** ya creados como:

- **`tunables/global`** que lo que hace es cargar variables para poder utilizarlas.

	```bash
	$ cat tunables/global
	#include <tunables/home>
	#include <tunables/multiarch>
	#include <tunables/proc>
	#include <tunables/alias>
	#include <tunables/kernelvars>
	#include <tunables/xdg-user-dirs>
	```
	
	Algunos de los que podemos ver dentro de los directorios que se muestran son:
		
	- **`@{HOME}`** que enruta el directorio home de todos los usuarios, es equivalente a `/home/*/` así como a `/root/`.

	- **`@{multiarch}`** que es el conjunto de parámetros establecidos para el soporte de la "multi arquitectura", es decir, para soportar distintos tipos de arquitecturas.

	- **`@{PROC}`**, que es la ruta donde está el pseudo sistema de ficheros `procfs` que es utilizado para permitir el acceso a la información del kernel sobre los procesos. Es equivalente a `/proc/`.

	- **Alias** que hubiera establecidas, en este caso no hay ninguna en el fichero `tunables/alias`.

	- **`@{pid}`** establece una expresión regular para cualquier número para identificar el `PID` de cualquier proceso.

	- **`@{XDG_***_DIR}`** que define el nombre de los directorios de usuario. Segun el valor de los *** pueden significar el escritorio (**DESKTOP**), la carpeta de descargas (**DOWNLOAD**), la de documentos (**DOCUMENTS**), etc.

- **`abstractions/base`** que lo que hace es establecer una base para el resto de perfiles.
	
	```bash
	$ cat abstractions/base 
	# Tras haber eliminado los comentarios.
	/dev/log                       w,
	/dev/random                    r,
  	/dev/urandom                   r,
	/etc/locale/**                 r,
	/etc/locale.alias              r,
	/etc/localtime                 r,
	/etc/writable/localtime        r,
	/usr/share/locale-bundle/**    r,
	/usr/share/locale-langpack/**  r,
	/usr/share/locale/**           r,
	/usr/share/**/locale/**        r,
	/usr/share/zoneinfo/           r,
	/usr/share/zoneinfo/**         r,
	/usr/share/X11/locale/**       r,
	/{,var/}run/systemd/journal/dev-log w,

	/usr/lib{,32,64}/locale/**             mr,
	/usr/lib{,32,64}/gconv/*.so            mr,
	/usr/lib{,32,64}/gconv/gconv-modules*  mr,
	/usr/lib/@{multiarch}/gconv/*.so           mr,
	/usr/lib/@{multiarch}/gconv/gconv-modules* mr,
	
	/etc/bindresvport.blacklist    r,
	
	/etc/ld.so.cache               mr,
	/lib{,32,64}/ld{,32,64}-*.so   mrix,
	/lib{,32,64}/**/ld{,32,64}-*.so     mrix,
	/lib/@{multiarch}/ld{,32,64}-*.so    mrix,
	/lib/tls/i686/{cmov,nosegneg}/ld-*.so     mrix,
	/lib/i386-linux-gnu/tls/i686/{cmov,nosegneg}/ld-*.so     mrix,
	/opt/*-linux-uclibc/lib/ld-uClibc*so* mrix,
	
	/lib{,32,64}/**                r,
	/lib{,32,64}/lib*.so*          mr,
	/lib{,32,64}/**/lib*.so*       mr,
	/lib/@{multiarch}/**            r,
	/lib/@{multiarch}/lib*.so*      mr,
	/lib/@{multiarch}/**/lib*.so*   mr,
	/usr/lib{,32,64}/**            r,
	/usr/lib{,32,64}/*.so*         mr,
	/usr/lib{,32,64}/**/lib*.so*   mr,
	/usr/lib/@{multiarch}/**          r,
	/usr/lib/@{multiarch}/lib*.so*    mr,
	/usr/lib/@{multiarch}/**/lib*.so* mr,
	/lib/tls/i686/{cmov,nosegneg}/lib*.so*    mr,
	/lib/i386-linux-gnu/tls/i686/{cmov,nosegneg}/lib*.so*    mr,

	/dev/null                      rw,
 
	/dev/zero                      rw,
  
 	/dev/full                      rw,

	@{PROC}/sys/kernel/version     r,
 
 	@{PROC}/sys/kernel/ngroups_max r,

	@{PROC}/meminfo                r,
	@{PROC}/stat                   r,
	@{PROC}/cpuinfo                r,
	/sys/devices/system/cpu/online r,
	
	@{PROC}/@{pid}/maps            r,
	
	@{PROC}/sys/crypto/*           r,
	
	/usr/share/common-licenses/**  r,
	
	@{PROC}/filesystems            r,
	
	@{PROC}/sys/vm/overcommit_memory r,
	
	@{PROC}/sys/kernel/cap_last_cap r,
	
	ptrace (readby),
	
	ptrace (tracedby),
	
	ptrace (read) peer=@{profile_name},
	
	signal (receive) peer=unconfined,
	
	signal peer=@{profile_name},
	
	signal (receive, send) set=("exists"),
	
	unix peer=(label=@{profile_name}),
	
	unix (receive) peer=(label=unconfined),
	
	unix (create),
	
	unix (getattr, getopt, setopt, shutdown),
	
	owner @{HOME}/.Private/** mrixwlk,
	  
	owner @{HOMEDIRS}/.ecryptfs/*/.Private/** mrixwlk,
	```
	
	Podemos ver como el fichero (aun eliminado los comentarios) es muy grande y contiene mucha información. Debido al tamaño no podemos comentarlo entero, pero algunas de las características que aquí se muestran son:
	
	- Establece permisos de lectura al `log`, fichero con permiso **`r`**.

	- Ficheros y ejecutables con permisos **`w`**. Establece permisos de escritura  a `/dev/random/`, `/dev/urandom/`, `/etc/locale/**`, `/usr/share/..`, etc.

	- Ficheros y ejecutables con permisos **`m`**. Permite `PROT_EXEC` con `mmap` a ciertos ficheros como `/usr/lib{32,64/locale/**`, `/usr/lib{32,64/gconv/*.so`, a `/etc/ld.so.cache`, etc.

	- Ficheros y ejecutables con permisos **`mrix`**. Permite `PROT_EXEC` con `mmap`, así como lectura y modo de ejecución heredado a `/lib{,32,64}/ld{,32,64}-*.so`, `/lib{,32,64}/**/ld{,32,64}-*.so`, `/opt/*-linux-uclibc/lib/ld-uClibc*so*`, etc.

	- Permitir la traceabilidad de nuestro programa, tanto para otros procesos como para nosotros mismos, así como acceder a nuestras entradas en `/proc`: `ptrace (readby)`, `ptrace (traceadby)`, `ptrace (read) peer=@{profile_name}`.

	- Permite a otros procesos y a nosotros mismos mandarnos señales, así como la comprobación de que realmente existe el `PID` al que se le manda señales: `signal (receive) peer=unconfined,`, `signal peer=@{profile_name},`, `signal (receive, send) set=("exists")`.

	- Permite a los procesos `unconfined` crear sockets con nosotros, así como crearlos nosotros con otros programas o servicios: `unix peer=(label=@{profile_name})`, `unix (receive) peer=(label=unconfined)`, `unix (create)`

- **`abstractions/nameservice`** que lo que hace es establecer los mecanismos para que el programa pueda ejecutar operaciones como buscar usuarios por nombre o ID, así como los grupos, host, etc.

	```bash
	$ cat abstractions/nameservice
	# Tras haber eliminado los comentarios.
	/etc/group              r,
	/etc/host.conf          r,
	/etc/hosts              r,
	/etc/nsswitch.conf      r,
	/etc/gai.conf           r,
	/etc/passwd             r,
	/etc/protocols          r,
	
	/var/lib/extrausers/group  r,
	/var/lib/extrausers/passwd r,
	
	/var/lib/sss/mc/group   r,
	/var/lib/sss/mc/passwd  r,
	/var/lib/sss/pipes/nss  rw,
	
	/etc/resolv.conf        r,
	
	/{,var/}run/{resolvconf,NetworkManager,systemd/resolve}/resolv.conf r,
	/etc/resolvconf/run/resolv.conf r,
	
	/etc/samba/lmhosts      r,
	/etc/services           r,
	
	/var/lib/misc/*.db      r,
	
	/{,var/}run/.nscd_socket   rw,
	/{,var/}run/nscd/socket    rw,
	/{var/db,var/cache,var/run,run}/nscd/{passwd,group,services,hosts}    r,
	
	/{,var/}run/nscd/db*  rmix,
	
	/lib{,32,64}/libnss_*.so*      mr,
	/usr/lib{,32,64}/libnss_*.so*  mr,
	/lib/@{multiarch}/libnss_*.so*      mr,
	/usr/lib/@{multiarch}/libnss_*.so*  mr,
	/etc/default/nss               r,
	
	/{,var/}run/avahi-daemon/socket rw,
	
	@{PROC}/@{pid}/net/psched r,
	/etc/libnl-*/classid r,
	
	network inet  stream,
	network inet6 stream,
	network inet  dgram,
	network inet6 dgram,
	
	network netlink raw,
	
	@{PROC}/@{pid}/net/route r,
	```
	
	Algunas de las características que aquí se muestran son:
	
	- Ficheros y ejecutables con permisos **`r`**. Establece permisos de lectura a `/etc/group `, `/etc/host.conf`, `/etc/hosts`, etc.

	- Ficheros y ejecutables con permisos **`rw`**. Establece permisos de lectura y escritura a `/var/lib/sss/pipes/nss`, `/{,var/}run/.nscd_socket`, `/{,var/}run/nscd/socket`, etc.

	- Ficheros y ejecutables con permisos **`m`**. Permite `PROT_EXEC` con `mmap` a ciertos ficheros como `/usr/lib{32,64/locale/**`, `/usr/lib{32,64/gconv/*.so`, a `/etc/ld.so.cache`, etc.

	- Ficheros y ejecutables con permisos **`rmix`**. Establece permisos de lectura, permite `PROT_EXEC` con `mmap` y modo de ejecución heredado a `/{,var/}run/nscd/db*`.

	- Incluye otros perfiles prefijados como `abstractions/nis`, `abstractions/ldapclient`, `abstractions/kerberosclient`, `abstractions/winbind`, etc.

	- Da acceso a redes en `TCP`y `UDP`, `network inet  stream`, `network inet6 stream`, `network inet  dgram`, `network inet6 dgram` y `network netlink raw`.

	- Detalles de interfaz `@{PROC}/@{pid}/net/route r`.

- **`abstractions/user-tmp`** que lo que hace es establecer los mecanismos para que se pueda acceder a los directorios temporales
	
	```bash
	$ cat abstractions/user-tmp 
	# Tras haber quitado los comentarios
	owner @{HOME}/tmp/**  rwkl,
	owner @{HOME}/tmp/    rw,
	
	owner /var/tmp/**     rwkl,
	/var/tmp/             rw,
	owner /tmp/**         rwkl,
	/tmp/                 rw,
	```
	
	Aquí podemos observar una serie características:
	
	- Directorios y ficheros con permisos **`rwkl`**. Establece permisos  de lectura, escritura, bloqueo de archivos y modo enlazado a ficheros temporales tanto de usuario como globales: `owner @{HOME}/tmp/**`, `owner /var/tmp/**` y `owner /tmp/**`.

	- Directorios y ficheros con permisos **`rw`**. Establece permisos  de lectura y escritura a ficheros temporales tanto de usuario como globales: `owner @{HOME}/tmp/`, `/var/tmp/` y `/tmp/`.
	
	
**Habilita una serie de capacidades** para el correcto funcionamiento (**[[3]](#ref)**):

- **net_raw**. Permite usar los sockets de `RAW` y `PACKET`.

- **setuid**. Permite la manipulación de los `UIDs` de los procesos. Hace que se borre el `UID` del proceso cuando pasan las credenciales por los sockets.

- **setgid**. Permite la manipulación de los `GIDs` de los procesos. Hace que se borre el `GID` del proceso cuando pasan las credenciales por los sockets.

- **dac_override**. Permite que se pasen las comprobaciones de lectura, escritura y ejecución.

- **sys_module**. Permite que se carguen y descarguen módulos en el Kernel.

- **net_admin**. Permite varias operaciones relacionadas con la red:
	- Configuraciones de las interfaces de red.
	
	- Administración del `firewall`.
	
	- Modificación de las tablas de rutas.
	
	- Vinculaciones mediante `proxys`.
	
	- Establecer el tipo de servicio (`Type-Of-Service` o `TOS`).

	- Limpiar las estadísticas de los drivers de red.

	- Poner las interfaces de red en modo promiscuo.

	- Habilitar la multidifusión.
	 

**Habilita reglas de red** para el análisis de paquetes que `tcpdump`necesita: `network raw` y `network packet`.

**Establecer permisos a distintos paths**

- Ficheros y ejecutables con permisos **`r`**. Establece permisos de lectura a `@{PROC}/bus/usb/`, `@{PROC}/bus/usb/**`, `@{PROC}/[0-9]*/net/dev`, `/sys/bus/usb/devices/`, etc.

- Ficheros y ejecutables con permisos **`w`**. Establece permisos de escritura a ` /dev/bus/usb/**/[0-9]*`

- Ficheros y ejecutables con permisos **`rw`**. Establece permisos de lectura y escritura a `owner @{HOME}/**`, `/**.[pP][cC][aA][pP]`, etc.

- Ficheros y ejecutables con permisos **`ixr`**. Establece permisos de lectura y modo de ejecución heredado a `/bin/gzip ixr` y `/bin/bzip2 ixr`.

En definitiva, incluye una serie de reglas y perfiles, que permiten que la orden `tcpdump` pueda hacer su cometido.

Hay que comentar, que según el archivo que estamos viendo, algunos de los perfiles, capacidades, etc se aplican según los parámetros con los que se ejecute la orden `tcpdump`. 

	
# <a></a>
### **Ejercicio 4.2** 
#### Selecciona un programa de tu distribución que no tenga perfil asociado y crea y activa un perfíl con los privilegios que estimes oportunos. Indica cómo se ha reflejado en el perfil.

En el ejercicio anterior, hemos visto los perfiles que están activados y para qué aplicaciones.

Ahora voy a escoger la aplicación `Nginx`, un servidor web bastante usado (aunque menos que `Apache`) y le voy a establecer un perfil para endurecer su seguridad. Voy a realizar una configuración básica, para principalmente, establecer qué directorios pueden ser accedidos desde los navegadores y cuáles no.

Una vez instalado y corriendo el servicio, podríamos acceder al servidor web simplemente indicando la dirección IP del servidor.

![ngix1](img/nginx1.png?style=center)

Ahora, vamos a crear los directorios que queremos que sean accesibles y los que no.

```bash
# Este será el directorio al que permitiremos acceso
$ sudo mkdir /var/www/html/available

# Este será el directorio al que no permitiremos acceso
$ sudo mkdir /var/www/html/not_available
```

Después, vamos a crear archivos de prueba para ambos directorios.

```bash
$ sudo nano /var/www/html/available/index.html
$ sudo nano /var/www/html/not_available/index.html
```

Por simplificar, vamos a crear el contenido para ambos es igual, exceptuando la línea que se muestra al cliente.

El **accesible**

```html
<html>
	<body>
        <h1>File in allowed directory</h1>
	</body>
</html>
```
![nginx file allowed](img/nginx2.png?style=center)

El **no accesible**

```html
<html>
	<body>
        <h1>File in not allowed directory</h1>
	</body>
</html>
```

![nginx file not allowed](img/nginx3.png?style=center)

Ahora que ya está el servidor funcionando y hemos comprobado como ambos archivos son perfectamente accesibles desde un navegador, vamos a realizar el perfil `App Armor` para endurecer la seguridad y evitar el acceso a la carpeta `not_available`.

Lo primero que he decidido es que sea un **perfil sistémico** ya que, será un servicio que esté siempre que la máquina encendida en funcionamiento, atendiendo peticiones web.

Acto seguido, vamos a realizar un análisis estático. Como hemos elegido un perfil sistémico, vamos a utilizar el siguiente comando:

```bash
$ sudo aa-autodep /usr/sbin/nginx
Writing updated profile for /usr/sbin/nginx.
```

Vamos a ver qué ha generado por defecto `App Armor`:

```
$ sudo cat /etc/apparmor.d/usr.sbin.nginx 
# Last Modified: Fri Oct 21 22:38:26 2016
#include <tunables/global>

/usr/sbin/nginx flags=(complain) {
  #include <abstractions/base>

  /usr/sbin/nginx mr,
}
```

Por ahora, podemos ver que además de incluir `tunabes/global` y `abstractions/base` ya comentados en el ejercicio anterior, lo único que hace es permitir la lectura y `PROT_EXEC` con `mmap`.

Lo siguiente que vamos a hacer, es añadirle funcionalidad. Para ello lo primero que vamos a hacer, es ponerlo en modo `complain`, reiniciar el servicio, acceder a algunas de las páginas anteriormente creadas, y utilizar la herramienta de `App Armor` `aa-logprof` para rastrear el log que ha dejado y decidir sobre las políticas de acceso.

```bash
$ sudo aa-complain nginx
Setting /usr/sbin/nginx to complain mode.

$ sudo service nginx restart
$ sudo aa-logprof 
Reading log entries from /var/log/syslog.
Updating AppArmor profiles in /etc/apparmor.d.
Complain-mode changes:

Profile:    /usr/sbin/nginx
Capability: dac_override
Severity:   9

 [1 - #include <abstractions/lxc/container-base>]
  2 - #include <abstractions/lxc/start-container> 
  3 - capability dac_override, 
(A)llow / [(D)eny] / (I)gnore / Audi(t) / Abo(r)t / (F)inish
Adding #include <abstractions/lxc/container-base> to profile.

Profile:  /usr/sbin/nginx
Path:     /etc/group
Mode:     r
Severity: 4

  1 - #include <abstractions/apache2-common> 
  2 - #include <abstractions/nameservice> 
 [3 - /etc/group]
[(A)llow] / (D)eny / (I)gnore / (G)lob / Glob with (E)xtension / (N)ew / Abo(r)t / (F)inish / (M)ore
Adding /etc/group r to profile

Profile:  /usr/sbin/nginx
Path:     /etc/nginx/conf.d/
Mode:     r
Severity: unknown

 [1 - /etc/nginx/conf.d/]
[(A)llow] / (D)eny / (I)gnore / (G)lob / Glob with (E)xtension / (N)ew / Abo(r)t / (F)inish / (M)ore
Adding /etc/nginx/conf.d/ r to profile

Profile:  /usr/sbin/nginx
Path:     /etc/nginx/mime.types
Mode:     r
Severity: unknown

 [1 - /etc/nginx/mime.types]
[(A)llow] / (D)eny / (I)gnore / (G)lob / Glob with (E)xtension / (N)ew / Abo(r)t / (F)inish / (M)ore
Adding /etc/nginx/mime.types r to profile

Profile:  /usr/sbin/nginx
Path:     /etc/nginx/nginx.conf
Mode:     r
Severity: unknown

 [1 - /etc/nginx/nginx.conf]
[(A)llow] / (D)eny / (I)gnore / (G)lob / Glob with (E)xtension / (N)ew / Abo(r)t / (F)inish / (M)ore
Adding /etc/nginx/nginx.conf r to profile

....

(S)ave Changes / Save Selec(t)ed Profile / [(V)iew Changes] / View Changes b/w (C)lean profiles / Abo(r)t
Writing updated profile for /usr/sbin/nginx.
```

Ahora, si volvemos a mirar que tiene el archivo del perfil anteriormente creado:

```bash
$ sudo cat /etc/apparmor.d/usr.sbin.nginx 
# Last Modified: Fri Oct 21 23:00:12 2016
#include <tunables/global>

/usr/sbin/nginx flags=(complain) {
  #include <abstractions/apache2-common>
  #include <abstractions/base>

  capability dac_override,

  /etc/nginx/conf.d/ r,
  /etc/nginx/mime.types r,
  /etc/nginx/nginx.conf r,
  /etc/nginx/sites-enabled/default r,
  /etc/ssl/openssl.cnf r,
  /run/nginx.pid w,
  /usr/sbin/nginx mr,
  /var/log/nginx/error.log w,
  /var/www/html/available/index.html r,
}
```

Vemos como efectivamente ha cambiado, y vemos todos los ficheros a los que le hemos dado acceso al utilizar la herramienta `app-logprof`.

Pero aún no he terminado de configurar el objetivo que me he marcado, aún, ambos directorios son accesibles.

Lo primero vamos a establecerle dos **capacidades** que puede necesitar un servidor web: `capability setuid` y `capability setgid`.

Y ahora tenemos que denegar el acceso a la carpeta que queremos que no sea accesible: `deny /var/www/html/not_available/* r`.

Ahora, el fichero nos queda así:

```bash
$ sudo nano /etc/apparmor.d/usr.sbin.nginx 
$ sudo cat /etc/apparmor.d/usr.sbin.nginx 
# Last Modified: Fri Oct 21 23:00:12 2016
#include <tunables/global>

/usr/sbin/nginx flags=(complain) {
  #include <abstractions/apache2-common>
  #include <abstractions/base>

  capability dac_override,
  capability setuid,
  capability setgid,

  /etc/nginx/conf.d/ r,
  /etc/nginx/mime.types r,
  /etc/nginx/nginx.conf r,
  /etc/nginx/sites-available/* r,
  /etc/nginx/sites-enabled/ r,
  /etc/ssl/openssl.cnf r,
  /run/nginx.pid rw,
  /usr/sbin/nginx mr,
  /var/log/nginx/error.log w,
  /var/www/html/available/* r,
  deny /var/www/html/not_available/* r,
}
```

Ahora, vamos a ponerlo en modo `Enforce`:

```bash
$ sudo aa-enforce nginx
Setting /usr/sbin/nginx to enforce mode.
```

Recargamos todos los perfiles y reiniciamos el servicio web:

```bash
$ sudo invoke-rc.d apparmor reload
$ sudo service nginx restart
```

Vamos a comprobar el estado de `App Armor`:

```bash
$ sudo apparmor_status 
apparmor module is loaded.
14 profiles are loaded.
14 profiles are in enforce mode.
   /sbin/dhclient
   /usr/bin/lxc-start
   /usr/lib/NetworkManager/nm-dhcp-client.action
   /usr/lib/NetworkManager/nm-dhcp-helper
   /usr/lib/connman/scripts/dhclient-script
   /usr/lib/lxd/lxd-bridge-proxy
   /usr/lib/snapd/snap-confine
   /usr/lib/snapd/snap-confine//mount-namespace-capture-helper
   /usr/sbin/nginx
   /usr/sbin/tcpdump
   lxc-container-default
   lxc-container-default-cgns
   lxc-container-default-with-mounting
   lxc-container-default-with-nesting
0 profiles are in complain mode.
4 processes have profiles defined.
4 processes are in enforce mode.
   /sbin/dhclient (908) 
   /usr/sbin/nginx (2630) 
   /usr/sbin/nginx (2631) 
   /usr/sbin/nginx (2632) 
0 processes are in complain mode.
0 processes are unconfined but have a profile defined.
```

Podemos observar como tenemos un perfil más que los indicados en el ejercicio anterior y demás podemos ver cómo está en modo enforme, y hay 3 procesos que están utilizando este perfil.

![nginx file not allowed done](img/nginx4.png?style=center)

Y si intentamos acceder al directorio al que queriamos que no se tuviera acceso, podemos ver cómo `App Armor` está ejecutando el perfil y denegando el acceso como queríamos mientras que si intentamos acceder al sitio que sí queremos que se pueda acceder, sigue siendo perfectamente accesible.

![nginx file allowed done](img/nginx2.png?style=center)

# <a></a>

## Bibliografía <a id="ref"></a>

[1] [App Armor](http://manpages.ubuntu.com/manpages/xenial/en/man7/apparmor.7.html)

[2] [App Armor Ubuntu Wiki](https://wiki.ubuntu.com/AppArmor)

[3] [Capabilities](https://linux.die.net/man/7/capabilities)


# <a></a>
## GLOSARIO DE TÉRMINOS

- **`PROT_EXEC` con `mmap`**: "Memory map as executable", es decir, permitir el mapa de memoria como ejecutable. Lo que hace es permitir que ese bloque de memoria del proceso sea ejecutado.
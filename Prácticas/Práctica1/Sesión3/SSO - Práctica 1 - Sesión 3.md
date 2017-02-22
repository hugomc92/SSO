# Práctica 1 - Administración de la seguridad en Linux


## Sesión 3 - Seguridad básica en Linux: privilegios de usuario y permisos
#

### **Hugo Maldonado Cózar**
#

### **Ejercicio 3.1** 
#### a) Utiliza esta herramienta para conocer que procesos/servicios1 de nuestro sistema están accediendo a la red o tiene archivos abiertos. Indicar algunos de los servicios que tenéis activos, es decir, la actividad de la red, indicando que información da la herramienta.

Para la realización de este ejercicio, usaré mi equipo directamente, sin máquinas virtuales de por medio, ya ésta no tiene activos ningún servicio conectado a internet (comprobado con la orden `lsof -i`).

Lo primero que hacemos es ejecutar dicha orden:

```
$ lsof
...
WorkerPoo 2452 2462 hugomaldonado  mem       REG                8,6    412130    3146135 /opt/visual-studio-code/natives_blob.bin
WorkerPoo 2452 2462 hugomaldonado  mem       REG                8,6    618284    3146136 /opt/visual-studio-code/snapshot_blob.bin
WorkerPoo 2452 2462 hugomaldonado  mem       REG                8,6    549040    4211713 /usr/lib/libsystemd.so.0.16.0
WorkerPoo 2452 2462 hugomaldonado  DEL       REG               0,19                87055 /dev/shm/.org.chromium.Chromium.LoW3gL
WorkerPoo 2452 2462 hugomaldonado  DEL       REG               0,19                87054 /dev/shm/.org.chromium.Chromium.Xj90Ds
WorkerPoo 2452 2462 hugomaldonado  DEL       REG               0,19                37853 /dev/shm/.org.chromium.Chromium.sLb109
WorkerPoo 2452 2462 hugomaldonado  DEL       REG               0,19                37850 /dev/shm/.org.chromium.Chromium.jKLY5E
WorkerPoo 2452 2462 hugomaldonado  mem       REG                8,6      4317    3146165 /opt/visual-studio-code/locales/es.pak
WorkerPoo 2452 2462 hugomaldonado    0r      CHR                1,3       0t0       1028 /dev/null
WorkerPoo 2452 2462 hugomaldonado    1u      CHR                1,3       0t0       1028 /dev/null
WorkerPoo 2452 2462 hugomaldonado    2u      CHR                1,3       0t0       1028 /dev/null
WorkerPoo 2452 2462 hugomaldonado    3r     FIFO               0,10       0t0      85065 pipe
WorkerPoo 2452 2462 hugomaldonado    4r      CHR                1,9       0t0       1033 /dev/urandom
WorkerPoo 2452 2462 hugomaldonado    5r      REG                8,6  10207936    3146139 /opt/visual-studio-code/icudtl.dat
WorkerPoo 2452 2462 hugomaldonado    6u     unix 0xffff880074c2f400       0t0      20361 type=SEQPACKET
WorkerPoo 2452 2462 hugomaldonado    7r      REG                8,6    618284    3146136 /opt/visual-studio-code/snapshot_blob.bin
WorkerPoo 2452 2462 hugomaldonado    8r      REG                8,6    412130    3146135 /opt/visual-studio-code/natives_blob.bin
WorkerPoo 2452 2462 hugomaldonado    9r      REG                8,6      4317    3146165 /opt/visual-studio-code/locales/es.pak
WorkerPoo 2452 2462 hugomaldonado   10r      REG                8,6   9520105    3146141 /opt/visual-studio-code/content_shell.pak
WorkerPoo 2452 2462 hugomaldonado   11r      CHR                1,9       0t0       1033 /dev/urandom
WorkerPoo 2452 2462 hugomaldonado   12r      CHR                1,9       0t0       1033 /dev/urandom
WorkerPoo 2452 2462 hugomaldonado   13w     FIFO               0,10       0t0      85065 pipe
WorkerPoo 2452 2462 hugomaldonado   14u     unix 0xffff8803c7e87800       0t0      86094 type=STREAM
WorkerPoo 2452 2462 hugomaldonado   15r      REG                8,6    412130    3146135 /opt/visual-studio-code/natives_blob.bin
WorkerPoo 2452 2462 hugomaldonado   16r      REG                8,6    618284    3146136 /opt/visual-studio-code/snapshot_blob.bin
WorkerPoo 2452 2462 hugomaldonado   17u  a_inode               0,11         0       8290 [eventpoll]
WorkerPoo 2452 2462 hugomaldonado   18r     FIFO               0,10       0t0      85066 pipe
WorkerPoo 2452 2462 hugomaldonado   19w     FIFO               0,10       0t0      85066 pipe
WorkerPoo 2452 2462 hugomaldonado   20u  a_inode               0,11         0       8290 [eventfd]
WorkerPoo 2452 2462 hugomaldonado   21u  a_inode               0,11         0       8290 [eventpoll]
WorkerPoo 2452 2462 hugomaldonado   22u  a_inode               0,11         0       8290 [eventpoll]
WorkerPoo 2452 2462 hugomaldonado   23u     unix 0xffff88043cdf8000       0t0      85069 type=STREAM
WorkerPoo 2452 2462 hugomaldonado   24u     unix 0xffff88043cdfe000       0t0      85070 type=STREAM
WorkerPoo 2452 2462 hugomaldonado   25r     FIFO               0,10       0t0      85071 pipe
WorkerPoo 2452 2462 hugomaldonado   26w     FIFO               0,10       0t0      85071 pipe
WorkerPoo 2452 2462 hugomaldonado   27u     unix 0xffff8803c7e85c00       0t0      86096 type=STREAM
WorkerPoo 2452 2462 hugomaldonado   28u     unix 0xffff8803c7e92c00       0t0      44944 type=STREAM
WorkerPoo 2452 2462 hugomaldonado   29u      REG               0,19     65536      87054 /dev/shm/.org.chromium.Chromium.Xj90Ds (deleted)
WorkerPoo 2452 2462 hugomaldonado   30u      REG               0,19        96      37850 /dev/shm/.org.chromium.Chromium.jKLY5E (deleted)
WorkerPoo 2452 2462 hugomaldonado   31u      REG               0,19   1048576      37851 /dev/shm/.org.chromium.Chromium.nxqFPy (deleted)
WorkerPoo 2452 2462 hugomaldonado   32u      REG               0,19   1048576      37852 /dev/shm/.org.chromium.Chromium.JmMgqR (deleted)
WorkerPoo 2452 2462 hugomaldonado   33u      REG               0,19     65536      87055 /dev/shm/.org.chromium.Chromium.LoW3gL (deleted)
WorkerPoo 2452 2462 hugomaldonado   34u      REG               0,19        96      37853 /dev/shm/.org.chromium.Chromium.sLb109 (deleted)
WorkerPoo 2452 2462 hugomaldonado   35r      REG                8,6    165300    3146196 /opt/visual-studio-code/resources/electron.asar
WorkerPoo 2452 2462 hugomaldonado   37r      REG                8,6    388352    4729934 /usr/share/fonts/TTF/LiberationSerif-Regular.ttf
WorkerPoo 2452 2462 hugomaldonado   38r      REG                8,6    190776    4729889 /usr/share/fonts/TTF/DroidSans.ttf
WorkerPoo 2452 2462 hugomaldonado   39r      REG                8,6    350200    4729930 /usr/share/fonts/TTF/LiberationSans-Regular.ttf
WorkerPoo 2452 2462 hugomaldonado   40r      REG                8,6   5398328    4729896 /usr/share/fonts/TTF/DroidSansFallbackFull.ttf
WorkerPoo 2452 2462 hugomaldonado   41r      REG                8,6    353936    4729927 /usr/share/fonts/TTF/LiberationSans-Bold.ttf
WorkerPoo 2452 2462 hugomaldonado   45u      REG               0,19   2097152      44971 /dev/shm/.org.chromium.Chromium.hbiaBT (deleted)
WorkerPoo 2452 2462 hugomaldonado   55r      REG                8,6    119380    4729902 /usr/share/fonts/TTF/DroidSansMono.ttf
WorkerPoo 2452 2462 hugomaldonado   56r      REG                8,6    313408    4729926 /usr/share/fonts/TTF/LiberationMono-Regular.ttf
lsof      2583      hugomaldonado  cwd       DIR                8,6      4096    4992848 /home/hugomaldonado
lsof      2583      hugomaldonado  rtd       DIR                8,6      4096          2 /
lsof      2583      hugomaldonado  txt       REG                8,6    152816    4214484 /usr/bin/lsof
lsof      2583      hugomaldonado  mem       REG                8,6   1951744    4211904 /usr/lib/libc-2.24.so
lsof      2583      hugomaldonado  mem       REG                8,6    168640    4211541 /usr/lib/ld-2.24.so
lsof      2583      hugomaldonado  mem       REG                8,6   1674704    4725270 /usr/lib/locale/locale-archive
lsof      2583      hugomaldonado    0u      CHR              136,1       0t0          4 /dev/pts/1
lsof      2583      hugomaldonado    1u      CHR              136,1       0t0          4 /dev/pts/1
lsof      2583      hugomaldonado    2u      CHR              136,1       0t0          4 /dev/pts/1
lsof      2583      hugomaldonado    3r      DIR                0,4         0          1 /proc
lsof      2583      hugomaldonado    4r      DIR                0,4         0      85510 /proc/2583/fd
lsof      2583      hugomaldonado    5w     FIFO               0,10       0t0      85515 pipe
lsof      2583      hugomaldonado    6r     FIFO               0,10       0t0      85516 pipe
lsof      2584      hugomaldonado  cwd       DIR                8,6      4096    4992848 /home/hugomaldonado
lsof      2584      hugomaldonado  rtd       DIR                8,6      4096          2 /
lsof      2584      hugomaldonado  txt       REG                8,6    152816    4214484 /usr/bin/lsof
lsof      2584      hugomaldonado  mem       REG                8,6   1951744    4211904 /usr/lib/libc-2.24.so
lsof      2584      hugomaldonado  mem       REG                8,6    168640    4211541 /usr/lib/ld-2.24.so
lsof      2584      hugomaldonado  mem       REG                8,6   1674704    4725270 /usr/lib/locale/locale-archive
lsof      2584      hugomaldonado    4r     FIFO               0,10       0t0      85515 pipe
lsof      2584      hugomaldonado    7w     FIFO               0,10       0t0      85516 pipe
```

Debido a la cantidad enorme de información que esto conlleva, sólo he mostrado las últimas líneas de la ejecución.

Ahora bien, si miramos en la página del manual de `lsof`, vemos que si aplicamos ciertos parámetros podemo obtener el resultado buscado, más concretamente:
- **-i**. Nos muestra todos los archivos y procesos/servicios conectados a internet.
- **-n**. Para que no se resuelvan las direcciones IPs con los nombres de dominio.
- **-P**. Para que no resuelvan los nombres de los puertos.

Los dos últimos parámetros los hemos introducido para conocer los servicios y los puertos que están escuchando y a los que están accediendo (más que con los nombres de dominio asociados).

```
$ lsof -i -n -P
COMMAND   PID          USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
megasync 1106 hugomaldonado   19u  IPv4  17225      0t0  TCP 127.0.0.1:6342 (LISTEN)
megasync 1106 hugomaldonado   25u  IPv4  10172      0t0  TCP 192.168.1.71:38682->31.216.147.134:443 (ESTABLISHED)
megasync 1106 hugomaldonado   30u  IPv4  10238      0t0  TCP 192.168.1.71:50822->31.216.147.139:80 (ESTABLISHED)
megasync 1106 hugomaldonado   34u  IPv4  98130      0t0  TCP 192.168.1.71:56752->31.216.145.33:8080 (ESTABLISHED)
megasync 1106 hugomaldonado   35u  IPv4  98839      0t0  TCP 192.168.1.71:53284->31.216.144.82:8080 (ESTABLISHED)
ssh      1600 hugomaldonado    3u  IPv4  19377      0t0  TCP 192.168.1.71:44472->192.168.1.74:22 (ESTABLISHED)
chrome   1829 hugomaldonado   67u  IPv4  29263      0t0  UDP *:5353 
chrome   1829 hugomaldonado   68u  IPv6  29264      0t0  UDP *:5353 
chrome   1829 hugomaldonado  105u  IPv4  31996      0t0  TCP 192.168.1.71:52970->66.102.1.188:5228 (ESTABLISHED)
chrome   1829 hugomaldonado  113u  IPv4  31299      0t0  UDP *:5353 
chrome   1829 hugomaldonado  183u  IPv4  36643      0t0  TCP 192.168.1.71:33956->192.30.253.124:443 (ESTABLISHED)
chrome   1829 hugomaldonado  194u  IPv4  31345      0t0  TCP 192.168.1.71:42968->192.30.253.125:443 (ESTABLISHED)
```

Vemos como ahora, la lista producida es considerablemente más pequeña.

Ahora podemos comprobar si dichos procesos/servicios se están ejecutando:

```
$ ps -ax | grep megasync
1106 tty2     Sl+    0:03 megasync

$ ps -ax | grep ssh
1600 pts/0    S+     0:00 ssh hugomaldonado@192.168.1.74

$ ps -ax | grep chrome
1829 tty2     SLl+   0:09 /opt/google/chrome/chrome
1839 tty2     S+     0:00 /opt/google/chrome/chrome-sandbox /opt/google/chrome/chrome --type=zygote
1840 tty2     S+     0:00 /opt/google/chrome/chrome --type=zygote
1842 tty2     S+     0:00 /opt/google/chrome/chrome-sandbox /opt/google/chrome/nacl_helper
1843 tty2     S+     0:00 /opt/google/chrome/nacl_helper
1845 tty2     S+     0:00 /opt/google/chrome/chrome --type=zygote
1920 tty2     Sl+    0:02 /opt/google/chrome/chrome --type=gpu-process --channel=1829.1.2121715438 ...
1950 tty2     Sl+    0:03 /opt/google/chrome/chrome --type=renderer ...
1990 tty2     S+     0:00 /opt/google/chrome/chrome --type=gpu-broker
2032 tty2     Sl+    0:03 /opt/google/chrome/chrome --type=renderer ....
2053 tty2     Sl+    0:03 /opt/google/chrome/chrome --type=renderer  ....
```

Por tanto, podemos comprobar como efectivamente, lo que la orden `lsof` nos mostró es correcto.

Ahora bien, vamos a analizar, por ejemplo, qué tiene activo el proceso de `ssh`, para ello:

```
$ lsof -i -n -P | grep ssh   
ssh      1600 hugomaldonado    3u  IPv4  19377      0t0  TCP 192.168.1.71:44472->192.168.1.74:22 (ESTABLISHED)
```

Podemos ver cómo está conectado el servicio `ssh` a una máquina dentro de la misma red local (la máquina virtual con la que hago las prácticas).

La información que nos da esta erramienta esta clasificada por columnas de la siguiente manera:
- **Primera**: Es el comando del proceso/servicio que está siendo listado.
- **Segunda**: Es el ID del proceso/servicio (`PID`) que está siendo listado.
- **Tercera**: Es el usuario que ha ejecutado dicho proceso/servicio.
- **Cuarta**: Es el descriptor del fichero.
- **Quinta**: Es el tipo de fichero, con la orden `lsof -i` vemos que todos son de tipo `IPv4` con lo que corroboramos que sólo se están mostrando los ficheros o procesos/servicios que tienen conexión a internet.
- **Sexta**: Es el número de dispositivo.
- **Séptima**: Es el tamaño del fichero
- **Octava**: Es el tipo de nodo, con la orden `lsof -i` nos indica el tipo de conexión (`TCP` o `UDP`).
- **Novena**: Las direcciones y los puertos tanto emisor (mi máquina) como receptor (a dónde están conectados).


#### b) Qué órdenes y opciones darías para conocer que cuenta podría estar generando tráfico saliente malicioso de ssh y dónde se encuentra el archivo.

Utilizando esta herramienta de gran utilidad, podríamos determinar esto con facilidad.
Lo primero sería como anteriormente he dicho, mostrar todos los procesos que estén conectados a la red con `lsof -i`.

Si detectamos que hay conexión `ssh` cuando no debería haberla podemos pasar a analizar con más detalle dicho servicio.

Mediante la orden comentada anteriormente: `lsof -i -n -P | grep ssh` podemos saber qué usuario está utilizando el servicio `ssh`.

Si detectamos que es una irregularidad, podemos seguir analizando todos los ficheros abiertos por este servicio mediante la orden `lsof -c ssh` y ya analizar todas las salidas que nos ha devuelto, para analizar los ficheros que están abiertos, para comprobar si es correcto o si debemos borrar algún fichero, o denegarle permisos, por estar generando ese tráfico no autorizado.

#### c) Muestra los archivos a los que esta accediendo un proceso concreto y los que están en uso por un usuario.

Esta herramienta, además nos permite con el parámetro `-c` listar todos los servicios abiertos de un proceso en concreto:

```
$ lsof -c VBoxSVC    
COMMAND  PID          USER   FD      TYPE             DEVICE SIZE/OFF    NODE NAME
VBoxSVC 1372 hugomaldonado  cwd       DIR                8,6     4096 4992848 /home/hugomaldonado
VBoxSVC 1372 hugomaldonado  rtd       DIR                8,6     4096       2 /
VBoxSVC 1372 hugomaldonado  txt       REG                8,6  5242720 4214223 /usr/lib/virtualbox/VBoxSVC
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   731280 4212810 /usr/lib/libvorbisenc.so.2.0.11
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   183016 4212809 /usr/lib/libvorbis.so.0.4.8
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    26840 4212551 /usr/lib/libogg.so.0.8.2
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   482472 4211545 /usr/lib/libFLAC.so.8.3.0
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    22688 4211738 /usr/lib/libXdmcp.so.6.0.0
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    14512 4211732 /usr/lib/libXau.so.6.0.0
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    22760 4211789 /usr/lib/libasyncns.so.0.3.1
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   483640 4212703 /usr/lib/libsndfile.so.1.0.27
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   166864 4212884 /usr/lib/libxcb.so.1.1.0
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    43344 4212329 /usr/lib/libjson-c.so.2.0.2
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   536720 4726702 /usr/lib/pulseaudio/libpulsecommon-9.0.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   333672 4212639 /usr/lib/libpulse.so.0.20.0
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    80520 4212169 /usr/lib/libgpg-error.so.0.19.1
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  1108736 4212099 /usr/lib/libgcrypt.so.20.1.3
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    72024 4212432 /usr/lib/liblz4.so.1.7.1
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    17256 4211921 /usr/lib/libcap.so.2.25
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   549040 4211713 /usr/lib/libsystemd.so.0.16.0
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   325328 4211981 /usr/lib/libdbus-1.so.3.14.7
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   381008 4214233 /usr/lib/virtualbox/components/VBoxDDU.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  3221344 4214237 /usr/lib/virtualbox/components/VBoxVMM.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  3221344 4214210 /usr/lib/virtualbox/VBoxVMM.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   792880 4214234 /usr/lib/virtualbox/components/VBoxREM.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  2465928 4214235 /usr/lib/virtualbox/components/VBoxRT.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  1065216 4214238 /usr/lib/virtualbox/components/VBoxXPCOM.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    47600 4212532 /usr/lib/libnss_files-2.24.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   126632 4214239 /usr/lib/virtualbox/components/VBoxXPCOMIPCC.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    84816 4212665 /usr/lib/libresolv-2.24.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    14568 4212363 /usr/lib/libkeyutils.so.1.5
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    52176 4212395 /usr/lib/libkrb5support.so.0.1
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    14688 4211958 /usr/lib/libcom_err.so.2.1
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   195440 4212332 /usr/lib/libk5crypto.so.3.1
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   933120 4212394 /usr/lib/libkrb5.so.3.3
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   317904 4212186 /usr/lib/libgssapi_krb5.so.2.2
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   187856 4212724 /usr/lib/libssh2.so.1.0.1
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   211496 4212298 /usr/lib/libidn.so.11.6.16
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   154264 4212433 /usr/lib/liblzma.so.5.2.2
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  1063288 4212435 /usr/lib/libm-2.24.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  2855464 4195436 /usr/lib/libcrypto.so.1.0.0
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   512672 4195374 /usr/lib/libssl.so.1.0.0
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   480792 4211974 /usr/lib/libcurl.so.4.4.0
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  1471328 4212889 /usr/lib/libxml2.so.2.9.4
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    31712 4212676 /usr/lib/librt-2.24.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    88592 4212899 /usr/lib/libz.so.1.2.8
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    39224 4211963 /usr/lib/libcrypt-2.24.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  1951744 4211904 /usr/lib/libc-2.24.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   720728 4212093 /usr/lib/libgcc_s.so.1
VBoxSVC 1372 hugomaldonado  mem       REG                8,6 11449024 4212730 /usr/lib/libstdc++.so.6.0.22
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   220336 4212610 /usr/lib/libpng16.so.16.25.0
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  1065216 4214212 /usr/lib/virtualbox/VBoxXPCOM.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6    14608 4211997 /usr/lib/libdl-2.24.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  2465928 4214205 /usr/lib/virtualbox/VBoxRT.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   143432 4212633 /usr/lib/libpthread-2.24.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   381008 4214189 /usr/lib/virtualbox/VBoxDDU.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6   168640 4211541 /usr/lib/ld-2.24.so
VBoxSVC 1372 hugomaldonado  mem       REG                8,6  1674704 4725270 /usr/lib/locale/locale-archive
VBoxSVC 1372 hugomaldonado    0u      CHR                1,3      0t0    1028 /dev/null
VBoxSVC 1372 hugomaldonado    1u      CHR                1,3      0t0    1028 /dev/null
VBoxSVC 1372 hugomaldonado    2u      CHR                1,3      0t0    1028 /dev/null
VBoxSVC 1372 hugomaldonado    3r      DIR                8,6     4096 4214173 /usr/lib/virtualbox
VBoxSVC 1372 hugomaldonado    4r      DIR                8,6     4096 4214173 /usr/lib/virtualbox
VBoxSVC 1372 hugomaldonado    5r      DIR                8,6     4096 4214173 /usr/lib/virtualbox
VBoxSVC 1372 hugomaldonado    6r      DIR                8,6     4096 4214182 /usr/lib/virtualbox/components
VBoxSVC 1372 hugomaldonado    7w      REG                8,6    13423 4982789 /home/hugomaldonado/.config/VirtualBox/VBoxSVC.log
VBoxSVC 1372 hugomaldonado    8r     FIFO               0,10      0t0   19007 pipe
VBoxSVC 1372 hugomaldonado    9w     FIFO               0,10      0t0   19007 pipe
VBoxSVC 1372 hugomaldonado   10u     unix 0xffff88042bec2c00      0t0   19008 type=STREAM
VBoxSVC 1372 hugomaldonado   11r     FIFO               0,10      0t0   19011 pipe
VBoxSVC 1372 hugomaldonado   12w     FIFO               0,10      0t0   19011 pipe
VBoxSVC 1372 hugomaldonado   13r  a_inode               0,11        0    8290 inotify
VBoxSVC 1372 hugomaldonado   14u     unix 0xffff88041e1c5400      0t0   20979 type=DGRAM
VBoxSVC 1372 hugomaldonado   15u     unix 0xffff88041e1c7400      0t0   20980 type=DGRAM
VBoxSVC 1372 hugomaldonado   16u     unix 0xffff88041a488c00      0t0   22131 type=STREAM
VBoxSVC 1372 hugomaldonado   18r      CHR                1,9      0t0    1033 /dev/urandom
```

He analizado en este caso todos los ficheros abiertos por el programa `Virtual Box` para la virtualización de la máquina en la que hago las prácticas.

También podríamos mostrar los archivos abiertos filtrando por el usuario que queramos con el parámetro `-u`:

```
$ lsoft -u hugomaldonado
...
man       2616 hugomaldonado  mem       REG                8,6   1674704    4725270 /usr/lib/locale/locale-archive
man       2616 hugomaldonado  mem       REG                8,6      7589     661781 /usr/share/locale/es/LC_MESSAGES/man-db.mo
man       2616 hugomaldonado    0r     FIFO               0,10       0t0      88846 pipe
man       2616 hugomaldonado    1w     FIFO               0,10       0t0      88847 pipe
man       2616 hugomaldonado    2u      CHR              136,1       0t0          4 /dev/pts/1
man       2616 hugomaldonado    3r      DIR                8,6      4096    4992848 /home/hugomaldonado
less      2617 hugomaldonado  cwd       DIR                8,6      4096    4992848 /home/hugomaldonado
less      2617 hugomaldonado  rtd       DIR                8,6      4096          2 /
less      2617 hugomaldonado  txt       REG                8,6    153984    4196623 /usr/bin/less
less      2617 hugomaldonado  mem       REG                8,6    143432    4212633 /usr/lib/libpthread-2.24.so
less      2617 hugomaldonado  mem       REG                8,6   1951744    4211904 /usr/lib/libc-2.24.so
less      2617 hugomaldonado  mem       REG                8,6    469288    4212589 /usr/lib/libpcre.so.1.2.7
less      2617 hugomaldonado  mem       REG                8,6    444680    4212490 /usr/lib/libncursesw.so.6.0
less      2617 hugomaldonado  mem       REG                8,6    168640    4211541 /usr/lib/ld-2.24.so
less      2617 hugomaldonado  mem       REG                8,6   1674704    4725270 /usr/lib/locale/locale-archive
less      2617 hugomaldonado    0r     FIFO               0,10       0t0      88847 pipe
less      2617 hugomaldonado    1u      CHR              136,1       0t0          4 /dev/pts/1
less      2617 hugomaldonado    2u      CHR              136,1       0t0          4 /dev/pts/1
less      2617 hugomaldonado    3r      CHR                5,0       0t0       1035 /dev/tty
zsh       2656 hugomaldonado  cwd       DIR                8,6      4096    4992848 /home/hugomaldonado
zsh       2656 hugomaldonado  rtd       DIR                8,6      4096          2 /
zsh       2656 hugomaldonado  txt       REG                8,6    775576    4198222 /usr/bin/zsh
zsh       2656 hugomaldonado  mem       REG                8,6     68664     787526 /usr/lib/zsh/5.2/zsh/compctl.so
zsh       2656 hugomaldonado  mem       REG                8,6     31512     787554 /usr/lib/zsh/5.2/zsh/zutil.so
zsh       2656 hugomaldonado  mem       REG                8,6    153064     787527 /usr/lib/zsh/5.2/zsh/complete.so
zsh       2656 hugomaldonado  mem       REG                8,6    325960     787549 /usr/lib/zsh/5.2/zsh/zle.so
zsh       2656 hugomaldonado  mem       REG                8,6     47600    4212532 /usr/lib/libnss_files-2.24.so
zsh       2656 hugomaldonado  mem       REG                8,6   1951744    4211904 /usr/lib/libc-2.24.so
zsh       2656 hugomaldonado  mem       REG                8,6   1063288    4212435 /usr/lib/libm-2.24.so
zsh       2656 hugomaldonado  mem       REG                8,6    444680    4212490 /usr/lib/libncursesw.so.6.0
zsh       2656 hugomaldonado  mem       REG                8,6     14608    4211997 /usr/lib/libdl-2.24.so
zsh       2656 hugomaldonado  mem       REG                8,6     17256    4211921 /usr/lib/libcap.so.2.25
zsh       2656 hugomaldonado  mem       REG                8,6    168640    4211541 /usr/lib/ld-2.24.so
zsh       2656 hugomaldonado  mem       REG                8,6   1674704    4725270 /usr/lib/locale/locale-archive
zsh       2656 hugomaldonado  mem       REG                8,6    152038     661760 /usr/share/locale/es/LC_MESSAGES/libc.mo
zsh       2656 hugomaldonado    0u      CHR              136,2       0t0          5 /dev/pts/2
zsh       2656 hugomaldonado    1u      CHR              136,2       0t0          5 /dev/pts/2
zsh       2656 hugomaldonado    2u      CHR              136,2       0t0          5 /dev/pts/2
zsh       2656 hugomaldonado   10u      CHR              136,2       0t0          5 /dev/pts/2
chrome    3376 hugomaldonado  cwd   unknown                                         /proc/3376/cwd (readlink: Permission denied)
chrome    3376 hugomaldonado  rtd   unknown                                         /proc/3376/root (readlink: Permission denied)
chrome    3376 hugomaldonado  txt   unknown                                         /proc/3376/exe (readlink: Permission denied)
chrome    3376 hugomaldonado NOFD                                                   /proc/3376/fd (opendir: Permission denied)
chrome    3396 hugomaldonado  cwd   unknown                                         /proc/3396/cwd (readlink: Permission denied)
chrome    3396 hugomaldonado  rtd   unknown                                         /proc/3396/root (readlink: Permission denied)
chrome    3396 hugomaldonado  txt   unknown                                         /proc/3396/exe (readlink: Permission denied)
chrome    3396 hugomaldonado NOFD                                                   /proc/3396/fd (opendir: Permission denied)
lsof      4672 hugomaldonado  cwd       DIR                8,6      4096    4992848 /home/hugomaldonado
lsof      4672 hugomaldonado  rtd       DIR                8,6      4096          2 /
lsof      4672 hugomaldonado  txt       REG                8,6    152816    4214484 /usr/bin/lsof
...
```

Un mecanismo de seguridad de esta herramienta interesante de comentar, es que no podemos listar los archivos abiertos por el usuario `root`:

```
$lsof -u root                                                                                                                        1 
lsof: ID 0 request rejected because of security mode.
```

Y tampoco a los procesos/servicios conectados a internet:

```
$ lsof -i -u root                                                                                                                        1 
lsof: ID 0 request rejected because of security mode.
```


### **Ejercicio 3.2**
#### Ejecuta la orden ps para conocer los procesos de sistema habituales que vamos a encontrar en nuestro sistema y que por tanto no deberán considerarse sospechosos en nuestras labores de seguridad. Indica 5 de los procesos que te han aparecido y cual es su labor.

Los parámetros de la orden `ps -eau`, según el manual de `ps` significan:
- **e**: Nos muestra el entorno (environment).
- **a**: Nos muestra los procesos de todos los usuarios.
- **u**: Nos muestra el usuario que ha lanzado el proceso y la hora de inicio.

```
$ps -eau                                                                                                                                1 
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
...
hugomal+   882  0.0  0.0 186888  5256 tty2     Ssl+ 15:10   0:00 /usr/lib/gdm/gdm-wayland-session gnome-session XDG_SEAT=seat0 LOGNAME=hugomaldonado USER=hugoma
hugomal+   886  0.0  0.0 574784 13372 tty2     Sl+  15:10   0:00 /usr/lib/gnome-session/gnome-session-binary LC_PAPER=es_ES.UTF-8 XDG_VTNR=2 LC_ADDRESS=es_ES.UT
hugomal+   902  4.4  1.0 2061944 178864 tty2   Sl+  15:10   2:35 /usr/bin/gnome-shell LC_PAPER=es_ES.UTF-8 XDG_VTNR=2 LC_ADDRESS=es_ES.UTF-8 XDG_SESSION_ID=c2 L
hugomal+   928  0.7  0.4 331956 77596 tty2     Sl+  15:10   0:26 /usr/bin/Xwayland :0 -rootless -noreset -listen 4 -listen 5 -displayfd 6 LC_PAPER=es_ES.UTF-8 X
hugomal+  1024  0.0  0.2 1221344 43220 tty2    Sl+  15:10   0:00 /usr/lib/gnome-settings-daemon/gnome-settings-daemon XDG_VTNR=2 LC_PAPER=es_ES.UTF-8 XDG_SESSIO
hugomal+  1099  0.0  0.1 425304 21656 tty2     Sl+  15:10   0:01 pamac-tray LC_PAPER=es_ES.UTF-8 XDG_VTNR=2 LC_ADDRESS=es_ES.UTF-8 XDG_SESSION_ID=c2 LC_MONETARY
hugomal+  1106  0.2  0.3 529552 60312 tty2     Sl+  15:10   0:08 megasync LC_PAPER=es_ES.UTF-8 XDG_VTNR=2 LC_ADDRESS=es_ES.UTF-8 XDG_SESSION_ID=c2 LC_MONETARY=e
hugomal+  1111  0.9  0.1 1211988 21204 tty2    SNl+ 15:10   0:31 /usr/lib/tracker/tracker-extract LC_PAPER=es_ES.UTF-8 XDG_VTNR=2 LC_ADDRESS=es_ES.UTF-8 XDG_SES
hugomal+  1114  0.2  0.2 625180 36216 tty2     SNl+ 15:10   0:07 /usr/lib/tracker/tracker-miner-fs LC_PAPER=es_ES.UTF-8 XDG_VTNR=2 LC_ADDRESS=es_ES.UTF-8 XDG_SE
hugomal+  1117  0.0  0.1 268148 31176 tty2     Sl+  15:10   0:00 /usr/bin/python3 /usr/share/system-config-printer/applet.py XDG_VTNR=2 LC_PAPER=es_ES.UTF-8 XDG
hugomal+  1119  0.0  0.0 450384 15960 tty2     SNl+ 15:10   0:00 /usr/lib/tracker/tracker-miner-user-guides LC_PAPER=es_ES.UTF-8 XDG_VTNR=2 LC_ADDRESS=es_ES.UTF
hugomal+  1132  0.0  0.1 594916 16980 tty2     SNl+ 15:10   0:00 /usr/lib/tracker/tracker-miner-apps LC_PAPER=es_ES.UTF-8 XDG_VTNR=2 LC_ADDRESS=es_ES.UTF-8 XDG_
hugomal+  1179  0.0  0.0 524820 13384 tty2     Sl+  15:10   0:00 /usr/lib/gnome-settings-daemon/gsd-printer XDG_VTNR=2 LC_PAPER=es_ES.UTF-8 XDG_SESSION_ID=c2 LC
hugomal+  1335  0.0  0.0  25788  4592 pts/0    Ss   15:11   0:00 zsh LC_TELEPHONE=es_ES.UTF-8 LANG=es_ES.UTF-8 DISPLAY=:0 SHLVL=0 LOGNAME=hugomaldonado XDG_VTNR
hugomal+  1347  0.2  0.5 1403352 86992 tty2    Sl+  15:11   0:09 /usr/lib/virtualbox/VirtualBox
hugomal+  1600  0.0  0.0  38076  5276 pts/0    S+   15:12   0:00 ssh hugomaldonado@192.168.1.74 LC_TELEPHONE=es_ES.UTF-8 LANG=es_ES.UTF-8 DISPLAY=:0 SHLVL=1 LOG
hugomal+  1829  0.7  1.1 1073532 180296 tty2   SLl+ 15:13   0:24 /opt/google/chrome/chrome                                                                     
hugomal+  1836  0.0  0.0   5976   720 tty2     S+   15:13   0:00 cat LC_PAPER=es_ES.UTF-8 XDG_VTNR=2 LC_ADDRESS=es_ES.UTF-8 XDG_SESSION_ID=c2 LC_MONETARY=es_ES.
hugomal+  1837  0.0  0.0   5976   728 tty2     S+   15:13   0:00 cat LC_PAPER=es_ES.UTF-8 XDG_VTNR=2 LC_ADDRESS=es_ES.UTF-8 XDG_SESSION_ID=c2 LC_MONETARY=es_ES.
hugomal+  1839  0.0  0.0   6340   808 tty2     S+   15:13   0:00 /opt/google/chrome/chrome-sandbox /opt/google/chrome/chrome --type=zygote
hugomal+  1840  0.0  0.2 405224 47388 tty2     S+   15:13   0:00 /opt/google/chrome/chrome --type=zygote                                                       
hugomal+  1842  0.0  0.0   6340   764 tty2     S+   15:13   0:00 /opt/google/chrome/chrome-sandbox /opt/google/chrome/nacl_helper
hugomal+  1843  0.0  0.0  27020  4492 tty2     S+   15:13   0:00 /opt/google/chrome/nacl_helper
hugomal+  1845  0.0  0.0 405224 11328 tty2     S+   15:13   0:00 /opt/google/chrome/chrome --type=zygote
hugomal+  1920  0.2  1.1 801832 181420 tty2    Sl+  15:13   0:09 /opt/google/chrome/chrome --type=gpu-process --channel=1829.1.2121715438 --mojo-application-cha
hugomal+  1950  0.1  1.1 1022840 184436 tty2   Sl+  15:13   0:06 /opt/google/chrome/chrome --type=renderer --enable-features=BlockSmallPluginContent<PluginPower
hugomal+  1990  0.0  0.0 407864 11620 tty2     S+   15:13   0:00 /opt/google/chrome/chrome --type=gpu-broker                                                   
hugomal+  2032  0.2  0.9 933148 151060 tty2    Sl+  15:13   0:07 /opt/google/chrome/chrome --type=renderer --enable-features=BlockSmallPluginContent<PluginPower
hugomal+  2053  0.1  1.8 1368980 309540 tty2   Sl+  15:13   0:03 /opt/google/chrome/chrome --type=renderer --enable-features=BlockSmallPluginContent<PluginPower
hugomal+  2236  0.0  0.0  28008  4848 pts/1    Ss   15:16   0:00 zsh LC_TELEPHONE=es_ES.UTF-8 LANG=es_ES.UTF-8 DISPLAY=:0 SHLVL=0 LOGNAME=hugomaldonado XDG_VTNR
hugomal+  2656  0.0  0.0  27904  4892 pts/2    Ss+  15:25   0:00 zsh LC_TELEPHONE=es_ES.UTF-8 LANG=es_ES.UTF-8 DISPLAY=:0 SHLVL=0 LOGNAME=hugomaldonado XDG_VTNR
hugomal+  6294  0.0  0.0  34620  3188 pts/1    R+   16:09   0:00 ps -eau LC_TELEPHONE=es_ES.UTF-8 LANG=es_ES.UTF-8 DISPLAY=:0 SHLVL=1 LOGNAME=hugomaldonado XDG_
...
```

Podemos ver algunos como el `gnome-shell` que es la terminal que estoy usando para la realización de las prácticas; `VirtualBox` para la virtualización como he comentado anteriormente; `megasync` para la sincronización con el servicio `MEGA`; `chrome` el navegador de `Google`, `Chrome`; o `ssh` para la conexión con la máquina virtual.

No se aprecia nada fuera de lo común.

### **Ejercicio 3.3**
#### Una vez instalada la herramienta indicar, y ejecutada en vuestro sistema:

Para este ejercicio, ya he vuelto a utilizar la máquina virtual en la que estoy realizando las pruebas normalmente.

Para la instalación de la herramienta he ejecutado `sudo apt-get install lynis`.

#### a) Mostrar que vulnerabilidades hay en vuestro sistema, indicando cómo deberíamos solucionarlas, en caso de que sea necesario.

Lo primero que hacemos es ejecutar la herramienta:

```
$ sudo lynis audit system --auditor "Hugo Maldonado"
```

Una vez que se ha ejecutado, vamos a analizar los datos del informe que ha realizado y que han sido guardados en `/var/log/lynis-report.dat`, aunque si analizamos el log que genera en `/var/log/lynis.log` vemos que la información es la misma, aunque presentada de forma distinta.


```
$ sudo cat /var/log/lynis-report.dat 
# Lynis Report
report_version_major=1
report_version_minor=0
report_datetime_start=2016-10-15 16:27:00
auditor=Hugo Maldonado
lynis_version=2.1.1
os=Linux
os_name=Ubuntu
os_fullname=Ubuntu 16.04
os_version=16.04
linux_version=Ubuntu
hostname=ubuntuserver
...
```

Ahora, vamos a descartar todo lo que tiene que ver con el informe de las tareas realizadas, y vamos a quedarnos con las vulnerabilidades que ha encontrado, así como con las sugerencias, para analizarlas:

```
warning[]=NONE|Version of Lynis is very old and should be updated|
lynis_update_available=1
```

Aquí podemos ver un warning, diciéndonos que la versión de `Lynis` está desactualizada y que hay una versión superior que deberíamos instalar (la versión del respositorio `apt` está desactualizada y no hemos ejecutado el comando `lynis --check-update`).

```
suggestion[]=CUST-0280|Install libpam-tmpdir to set $TMP and $TMPDIR for PAM sessions|
suggestion[]=CUST-0285|Install libpam-usb to enable multi-factor authentication for PAM sessions|
suggestion[]=CUST-0520|Install 'ecryptfs-utils' and configure for each user.|
suggestion[]=CUST-0810|Install apt-listbugs to display a list of critical bugs prior to each APT installation.|
suggestion[]=CUST-0811|Install apt-listchanges to display any significant changes prior to any upgrade via APT.|
suggestion[]=CUST-0830|Install debian-goodies so that you can run checkrestart after upgrades to determine which services are using old versions of libraries and need restarting.|
suggestion[]=CUST-0870|Install debsecan to generate lists of vulnerabilities which affect this installation.|
suggestion[]=CUST-0875|Install debsums for the verification of installed package files against MD5 checksums.|
suggestion[]=DEB-0880|Install fail2ban to automatically ban hosts that commit multiple authentication errors.|
```

Aquí podemos ver como nos sugiere que instalemos ciertas herramientas con diferentes fines para mejorar la seguridad, algunas como `fail2ban` para bloquear automáticamente IPs que hacen múltiples errores de login, `libpam-tmpdir` para establecer `$TMP` y `$TMPDIR` en la sesión de `PAM`, `libpm-usb` para habilitar autenticaciones basadas en múltiples factores...

```
suggestion[]=BOOT-5122|Set a password on GRUB bootloader to prevent altering boot configuration (e.g. boot in single user mode without password)|
```

Aquí nos indica que sería bueno establecer una contraseña al gestor de arranque `GRUB` para evitar que se altere la configuración de arranque.

```
suggestion[]=BOOT-5180|Determine runlevel and services at startup|
```

Aquí nos sugiere que determinemos el nivel de ejecución y los servicios del tiempo de arranque, es decir, que controlemos qué procesos y servicios pueden ejecutarse en tiempo de ejecución de la máquina y cuales no.

```
suggestion[]=AUTH-9262|Install a PAM module for password strength testing like pam_cracklib or pam_passwdqc|
```

Aquí nos sugiere que instalemos un módulo de `PAM` para mejorar la seguridad de las contraseñas como `pam_cracklib` o `pam_passwdqc`.

```
suggestion[]=AUTH-9286|Configure password aging limits to enforce password changing on a regular base|
```

Aquí nos sugiere que configuremos las contraseñas más fuertemente para endurecerlas y ser más dificiles de hallar.

```
suggestion[]=AUTH-9328|Default umask in /etc/login.defs could be more strict like 027|
suggestion[]=AUTH-9328|Default umask in /etc/init.d/rc could be more strict like 027|
```

Aquí nos sugiere que la máscara por defecto tanto en `/etc/login.defs` y `/etc/init.d/rc` debería ser más estricta.

```
suggestion[]=FILE-6310|To decrease the impact of a full /home file system, place /home on a separated partition|
suggestion[]=FILE-6310|To decrease the impact of a full /tmp file system, place /tmp on a separated partition|
```

Aquí nos sugiere que es mejor tener los sistemas de ficheros `/home` y `/tmp` en particiones diferentes, en vez de todo en la misma partición.

```
suggestion[]=STRG-1840|Disable drivers like USB storage when not used, to prevent unauthorized storage or data theft|
```

Aquí nos sugiere deshabilitar los drivers que no se estén usando como el almacenamiento USB, para prevenir almacenamientos o robos de datos desautorizados.

```
suggestion[]=PKGS-7370|Install debsums utility for the verification of packages with known good database.|
```

Aquí nos sugiere instalar la utilidad `debsums` para la verificación de paquetes con una base de datos bien corroborada.

```
vulnerable_package[]=tzdata
warning[]=PKGS-7392|Found one or more vulnerable packages.|
suggestion[]=PKGS-7392|Update your system with apt-get update, apt-get upgrade, apt-get dist-upgrade and/or unattended-upgrades|
suggestion[]=PKGS-7394|Install package apt-show-versions for patch management purposes|
```

Aquí nos advierte de un paquete instalado que es vulnerable, y nos recomienda que actualicemos el sistema, y la aplicación para evitar la vulnerabilidad.

```
suggestion[]=FIRE-4590|Configure a firewall/packet filter to filter incoming and outgoing traffic|
```

Aquí nos sugiere que configuremos un `firewall` o filtro de paquetes para controlar el tráfico saliente y entrante.

```
suggestion[]=BANN-7126|Add a legal banner to /etc/issue, to warn unauthorized users|
suggestion[]=BANN-7130|Add legal banner to /etc/issue.net, to warn unauthorized users|
```

Aquí nos sugiere que añadamos un banner legal a `/etc/issue` y `/etc/issue.net` para advertir a usuarios desautorizados.

```
suggestion[]=ACCT-9622|Enable process accounting|
```

Aquí nos sugiere habilitar la contabilidad de procesos, esto es, un método para registrar los comandos ejecutados.

```
suggestion[]=ACCT-9626|Enable sysstat to collect accounting (no results)|
```

Aquí nos sugiere habilitar `systat` para recopilar la información de los procesos de lo nombrado justo arriba.

```
suggestion[]=ACCT-9628|Enable auditd to collect audit information|
```

Aquí nos sugiere habilitar `auditd` para recopilar información de auditoría. 


```
suggestion[]=FINT-4350|Install a file integrity tool to monitor changes to critical and sensitive files|
```

Aquí nos sugiere instalar una herramienta que asegure la integridad de los ficheros para monitorizar cambios en ficheros críticos y sensibles.

```
suggestion[]=KRNL-6000|One or more sysctl values differ from the scan profile and could be tweaked|
```

Aquí nos indica que uno o algunos valores de `sysctl` son diferentes del perfil de escaneamiento y podrían ser modificados para mejorar la seguridad.

```
suggestion[]=HRDN-7230|Harden the system by installing at least one malware scanner, to perform periodic file system scans|
```

Aquí nos sugiere que instalando al menos un escaner de `malware` que lleve a cabo escaneamientos periódicos endurecemos el sistema.

Como podemos observar, `Ubuntu Server 16.04.1` no viene de fábrica con todas las mejoras en seguridad necesarias que deberíamos de tener en cuenta a la hora de montar por ejemplo un servidor con acceso público. Pero la mayoría son fáciles de solucionar, ya que consisten en instalar herramientas que fortalezcan al sistema, o modificar pequeños ajustes para mejorar la seguridad. También debemos de tener el sistema siempre actualizado para evitar las máximas vulnerabilidades posibles (yo en este caso no tenía la máquina virtual actualizada del todo y por eso han salido más vulnerabilidades de las que habrían salido si lo hubiera tenido actualizado)

#### b) En clase de teoría, vimos la vulnerabilidad Shellshock (CVE-2014-6271), mirar si la herramienta citada comprueba dicha vulnerabilidad indicando como lo hace (esto nos servirá para conocer como podríamos desarrollar nuestro propio test). Consejo, revisar el contenido del archivo de la instalación de la herramienta include/tests_shells.

Tal y como nos recomienda el enunciado, vamos a buscar el fichero `tests_sheels` y analizarlo para ver si comprueba la vulnaribilidad `Shellshock` o no y cómo lo hace si así lo hace.

```
$ sudo cat /usr/share/lynis/include/tests_shells
...
```

Analizando el archivo vemos que contiene lo siguiente:

```bash
# Test        : SHLL-6290
# Description : Check for Shellshock vulnerability
Register --test-no SHLL-6290 --weight H --network NO --category security --description "Perform Shellshock vulnerability tests"
if [ ${SKIPTEST} -eq 0 ]; then
	FOUND=0
	x=""
	#Display --indent 2 --text "- Testing for Shellshock vulnerability"
	LogText "Test: Check if bash is in the list of shells."
	if [ -f /etc/shells ]; then
		LogText "Test: checking for bash shell in /etc/shells"
		FIND=`${EGREPBINARY} '(/usr)?(/local)?/bin/bash' /etc/shells | ${GREPBINARY} -v "^#" | head -1`
		else
		LogText "Test: checking if bash is available via which command"
		FIND=`which bash 2> /dev/null | head -1`
	fi

	LogText "Result: command revealed ${FIND} as output"
	if [ ! "${FIND}" = "" ]; then
		if [ -x "${FIND}" -a ! -L "${FIND}" ]; then
			LogText "Result: found ${FIND} as a valid shell"
			CreateTempFile || ExitFatal
			SHELLSHOCK_TMP="${TEMP_FILE}"

			# CVE-2014-6271
			LogText "Test: Check for first exploit (CVE-2014-6271)"
			echo "env 'x=() { :;}; echo vulnerable' 'BASH_FUNC_x()=() { :;}; echo vulnerable' bash -c \"echo test\" 2>&1 | ${GREPBINARY} 'vulnerable'" > ${SHELLSHOCK_TMP}
			VULNERABLE=`${FIND} ${SHELLSHOCK_TMP} 2> /dev/null`
			rm -f ${SHELLSHOCK_TMP}
			if [ ! "${VULNERABLE}" = "" ]; then
				LogText "Output: ${VULNERABLE}"
				LogText "Result: Vulnerable to original shellshock (CVE-2014-6271)"
				Display --indent 2 --text "- Shellshock: CVE-2014-6271 (original shellshocker)" --result "${STATUS_WARNING}" --color RED
				FOUND=1
				else
				LogText "Result: Not vulnerable to original shellshock (CVE-2014-6271)"
				#Display --indent 4 --text "- CVE-2014-6271 (original shellshocker)" --result "${STATUS_OK}" --color GREEN
			fi

			# CVE-2014-6277 (disabled, as this test was giving too much false positives)

			# CVE-2014-6278
			LogText "Test: Check for CVE-2014-6278"
			echo "shellshocker='() { echo vulnerable; }' bash -c shellshocker 2>/dev/null | ${GREPBINARY} 'vulnerable'" > ${SHELLSHOCK_TMP}
			VULNERABLE=`${FIND} ${SHELLSHOCK_TMP} 2> /dev/null`
			rm -f ${SHELLSHOCK_TMP}
			if [ ! "${VULNERABLE}" = "" ]; then
				LogText "Output: ${VULNERABLE}"
				LogText "Result: Vulnerable to CVE-2014-6278"
				Display --indent 2 --text "- Shellshock: CVE-2014-6278 (Florian's patch, lcamtuf bug #2)" --result "${STATUS_WARNING}" --color RED
				FOUND=1
				else
				LogText "Result: Not vulnerable to CVE-2014-6278"
				#Display --indent 4 --text "- CVE-2014-6278 (Florian's patch, lcamtuf bug #2)" --result "${STATUS_OK}" --color GREEN
			fi

			# CVE-2014-7169
			LogText "Test: Check for taviso bug CVE-2014-7169"
			echo "(cd /tmp; rm -f /tmp/echo; env X='() { (a)=>\' bash -c "echo echo nonvuln" 2>/dev/null; [[ \"\$(cat echo 2> /dev/null)\" == \"nonvuln\" ]] && echo \"vulnerable\" 2> /dev/null) | ${GREPBINARY} '   vulnerable'" > ${SHELLSHOCK_TMP}
			VULNERABLE=`${FIND} ${SHELLSHOCK_TMP} 2> /dev/null`
			rm -f ${SHELLSHOCK_TMP}
			if [ ! "${VULNERABLE}" = "" ]; then
				LogText "Output: ${VULNERABLE}"
				LogText "Result: Vulnerable to taviso bug (CVE-2014-7169)"
				Display --indent 2 --text "- Shellshock: CVE-2014-7169 (taviso bug)" --result "${STATUS_WARNING}" --color RED
				FOUND=1
				else
				LogText "Result: Not vulnerable to taviso bug (CVE-2014-7169)"
				#Display --indent 4 --text "- CVE-2014-7169 (taviso bug)" --result "${STATUS_OK}" --color GREEN
			fi

			# CVE-2014-7186
			LogText "Test: Check for CVE-2014-7186"
			echo "(bash -c 'true <<EOF <<EOF <<EOF <<EOF <<EOF <<EOF <<EOF <<EOF <<EOF <<EOF <<EOF <<EOF <<EOF <<EOF' 2>/dev/null || echo \"vulnerable\") | ${GREPBINARY} 'vulnerable'" > ${SHELLSHOCK_TMP}
			VULNERABLE=`${FIND} ${SHELLSHOCK_TMP} 2> /dev/null`
			rm -f ${SHELLSHOCK_TMP}
			if [ ! "${VULNERABLE}" = "" ]; then
				LogText "Output: ${VULNERABLE}"
				LogText "Result: Vulnerable to CVE-2014-7186"
				Display --indent 2 --text "- Shellshock: CVE-2014-7186 redir_stack bug" --result "${STATUS_WARNING}" --color RED
				FOUND=1
				else
				LogText "Result: Not vulnerable to CVE-2014-7186"
				#Display --indent 4 --text "- CVE-2014-7186 redir_stack bug" --result "${STATUS_OK}" --color GREEN
			fi

			# CVE-2014-7187
			LogText "Test: Check for CVE-2014-7187"
			echo "((for x in {1..200}; do echo \"for x$x in ; do :\"; done; for x in {1..200}; do echo done; done) | bash || echo \"vulnerable\") | ${GREPBINARY} 'vulnerable'" > ${SHELLSHOCK_TMP}
			VULNERABLE=`${FIND} ${SHELLSHOCK_TMP} 2> /dev/null`
			rm -f ${SHELLSHOCK_TMP}
			if [ ! "${VULNERABLE}" = "" ]; then
				LogText "Output: ${VULNERABLE}"
				LogText "Result: Vulnerable to CVE-2014-7187"
				Display --indent 2 --text "- Shellshock: CVE-2014-7187 nested loops off by one bug" --result "${STATUS_WARNING}" --color RED
				FOUND=1
				else
				LogText "Result: Not vulnerable to CVE-2014-7187"
				#Display --indent 4 --text "- CVE-2014-7187 nested loops off by one bug" --result "${STATUS_OK}" --color GREEN
			fi

			# CVE-2014-////
			LogText "Test: Check for bug Exploit #3 - shellshocker.net (no CVE)"
			echo "env X=' () { }; echo hello' bash -c 'date'| ${GREPBINARY} 'hello'" > ${SHELLSHOCK_TMP}
			VULNERABLE=`${FIND} ${SHELLSHOCK_TMP} 2> /dev/null`
			rm -f ${SHELLSHOCK_TMP}
			if [ ! "${VULNERABLE}" = "" ]; then
				LogText "Output: ${VULNERABLE}"
				LogText "Result: Vulnerable to CVE-2014-//// (exploit #3 on shellshocker.net)"
				Display --indent 2 --text "- Shellshock: Exploit #3 on shellshocker.net (no CVE)" --result "${STATUS_WARNING}" --color RED
				FOUND=1
				else
				LogText "Result: Not vulnerable to exploit #3 on shellshocker.net (no CVE)"
				#Display --indent 4 --text "- Exploit#3 on shellshocker.net (no CVE)" --result "${STATUS_OK}" --color GREEN
			fi
			else
			LogText "Result: bash binary found, but not executable, or it is symlinked"
		fi
		else
		LogText "Result: could not find bash to be a valid shell"
	fi

	if [ ${FOUND} -eq 1 ]; then
		ReportWarning ${TEST_NO} "System vulnerable to Shellshock (bash)"
		AddHP 0 25
		else
		AddHP 5 5
	fi
	unset x
fi
```

Como podemos comprobar, sí que realiza la comprobación de dicha vulnerabilidad, y no sólo de la básica, si no de muchas modificaciones que se han descubierto sobre la misma.

A grandes rasgos lo que hace para comprobarlo es comprobar que la consola `bash` existe en el sistema o puede ejecutarse, si es así prueba a ejecutar todas las variantes de la vulnerabilidad mostrando todos los pasos por los que pasa y si es o no vulnerable a cada una de las variantes de la vulnerabilidad, si no existe o no puede ejecutarse la consola `bash` termina pues no es vulnerable.
# Práctica 3 -Auditoría informática e Informática forense

## Sesión 2 - Análisis forense en Linux (II)

### **Hugo Maldonado Cózar**
# <a></a>

### **Ejercicio 3.2.1**
#### Crear un volcado de memoria de la máquina forense (local) que estéis utilizando.

Primero vamos a conocer y a mostrar los detalles de la máquina en la que vamos a realizar el volcado de memoria:

```bash
# Distribución
$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 16.10
Release:	16.10
Codename:	yakkety

# Versión del Kernel
$ uname -a
Linux ubuntu-sso 4.8.0-32-generic #34-Ubuntu SMP Tue Dec 13 14:30:43 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux

# Memoria RAM
$ cat /proc/meminfo | head -1
MemTotal:        4041840 kB
```

Acto seguido vamos a instalar el software necesario tal y como indica el guión:

```
# Clonamos el repositorio de LiME
$ git clone https://github.com/504ensicsLabs/LiME.git

# Instalamos los paquetes necesarios para la compilación de LiME
$ sudo apt-get install make build-essential linux-headers-4.8.0-32

# Procedemos a la compilación del software
$ cd LiME/src/
$ make

# Analizamos tras la compilación
$ ls
disk.c                    lime.h      lime.o  Makefile         Module.symvers
disk.o                    lime.mod.c  main.c  Makefile.sample  tcp.c
lime-4.8.0-32-generic.ko  lime.mod.o  main.o  modules.order    tcp.o
```

Una vez seguidos los pasos para instalar el software, vamos a proceder al volcado de memoria local:

```
$ sudo insmod lime-4.8.0-32-generic.ko "path=/home/hugomaldonado/ram_dumps/dump1 format=raw"

$ cd /home/hugomaldonado/ram_dumps/

$ ls -l
total 4193840
-r--r--r-- 1 root root 4294487040 ene  7 11:18 dump1

$ du dump1 
4193840	dump1
```

Podemos ver como el tamaño del volcado de memoria y el tamaño de la RAM de la máquina coinciden prácticamente, lo cual es una buena señal de que se ha realizado correctamente.

Como está en formato `raw` no podemos ver su contenido de forma normal con el comando `cat` por ejemplo. Ya que al ser un volcado de memoria, lo que se ha volcado al archivo han sido las direcciones de memoria junto con su contenido en hexadecimal, con lo que para ver el contenido y analizarlo manualmente podríamos hacerlo con un editor hexadecimal como `xxd`:

```
$ xxd dump1 
00000000: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000010: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000020: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000030: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000040: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000050: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000060: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000070: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000080: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000090: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000000a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000000b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000000c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000000d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000000e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000000f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000100: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000110: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000120: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000130: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000140: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000150: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000160: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000170: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000180: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000190: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000001f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000200: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000210: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000220: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000230: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000240: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000250: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000260: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000270: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000280: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000290: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000002f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000300: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000310: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000320: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000330: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000340: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000350: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000360: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000370: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000380: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000390: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000003f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000400: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000410: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000420: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000430: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000440: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000450: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000460: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000470: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000480: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000490: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000004a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000004b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000004c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000004d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000004e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000004f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000500: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000510: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000520: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000530: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000540: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000550: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000560: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000570: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000580: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000590: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000005a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000005b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000005c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000005d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000005e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000005f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000600: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000610: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000620: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000630: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000640: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000650: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000660: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000670: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000680: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000690: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000006a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000006b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000006c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000006d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000006e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000006f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000700: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000710: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000720: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000730: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000740: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000750: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000760: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000770: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000780: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000790: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000007a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000007b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000007c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000007d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000007e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000007f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000800: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000810: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000820: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000830: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000840: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000850: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000860: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000870: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000880: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000890: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000008a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000008b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000008c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000008d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000008e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000008f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000900: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000910: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000920: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000930: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000940: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000950: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000960: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000970: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000980: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000990: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000009a0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000009b0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000009c0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000009d0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000009e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
000009f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000a00: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000a10: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000a20: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000a30: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000a40: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000a50: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000a60: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000a70: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000a80: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000a90: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000aa0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000ab0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000ac0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000ad0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000ae0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000af0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000b00: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000b10: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000b20: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000b30: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000b40: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000b50: 0000 0000 0000 0000 0000 0000 0000 0000  ................
00000b60: 0000 0000 0000 0000 0000 0000 0000 0000  ................

...

0000f000: 301b feaf a05f f7af 0000 0000 904d fdaf  0...._.......M..
0000f010: f059 f7af b06a f7af 0000 0000 0000 0000  .Y...j..........
0000f020: 0022 feaf 0000 0000 3016 feaf 5062 f7af  ."......0...Pb..
0000f030: c0a9 feaf 9039 f8af 0000 0000 0000 0000  .....9..........
0000f040: b026 feaf 6058 efaf 0000 0000 0000 0000  .&..`X..........
0000f050: 3031 d1af f0b2 feaf 40af f8af c058 efaf  01......@....X..
0000f060: 50bf feaf 0000 0000 0000 0000 f06c f7af  P............l..
0000f070: 106b f7af f039 f8af 0000 0000 0000 0000  .k...9..........
0000f080: 0000 0000 e015 feaf 0000 0000 d01d feaf  ................
0000f090: 20fb f8af f04f fdaf 0000 0000 0000 0000   ....O..........
0000f0a0: 005c fdaf 501c feaf d0ab feaf b065 f7af  .\..P........e..
0000f0b0: 0000 0000 d02b d1af 0000 0000 0000 0000  .....+..........
0000f0c0: 0000 0000 0000 0000 b0f4 f8af 0000 0000  ................
0000f0d0: 1052 feaf 0000 0000 0000 0000 3068 fdaf  .R..........0h..
0000f0e0: 0000 0000 0000 0000 7004 fdaf 0000 0000  ........p.......
0000f0f0: 80bc feaf 101d feaf 0000 0000 0053 fdaf  .............S..
0000f100: 0000 0000 0000 0000 0000 0000 d0ca ecaf  ................
0000f110: 0000 0000 40fc f8af 8064 fdaf 60e5 eeaf  ....@....d..`...
0000f120: 0000 0000 d059 fdaf 2032 f8af 0000 0000  .....Y.. 2......
0000f130: 505d f7af 2029 feaf 705c efaf 30cb ecaf  P].. )..p\..0...
0000f140: 90bb feaf 50aa feaf 80b6 feaf 0000 0000  ....P...........
0000f150: c040 f9af 30a7 f8af f065 fdaf 3063 efaf  .@..0....e..0c..
0000f160: 0000 0000 0000 0000 0000 0000 d068 f7af  .............h..
0000f170: 2043 f9af 1014 feaf 10ba feaf 20b0 feaf   C.......... ...
0000f180: 0000 0000 0000 0000 0000 0000 b062 f7af  .............b..
0000f190: 8032 f8af 0000 0000 d0f8 f8af 0000 0000  .2..............
0000f1a0: 0000 0000 505f fdaf d060 f7af 0000 0000  ....P_...`......
0000f1b0: c0a5 f8af 1066 f7af 0000 0000 0000 0000  .....f..........
0000f1c0: 90e7 eeaf 0000 0000 0000 0000 0034 f8af  .............4..
0000f1d0: 00b8 feaf 0000 0000 c069 f9af f02a feaf  .........i...*..
0000f1e0: 0000 0000 d042 f9af 80ad feaf 2061 fdaf  .....B...... a..
0000f1f0: 0000 0000 0000 0000 0000 0000 706e f7af  ............pn..
0000f200: 0000 0000 d0b7 feaf 0000 0000 205d fdaf  ............ ]..
0000f210: a033 f8af 0000 0000 c033 d1af 0000 0000  .3.......3......
0000f220: 80b9 feaf d0ae feaf 5026 feaf 305c f7af  ........P&..0\..
0000f230: 0000 0000 106e f7af 0000 0000 60b8 feaf  .....n......`...
0000f240: 9069 f7af 00fd f8af 3056 f7af 80aa feaf  .i......0V......
0000f250: 50b9 feaf 70f8 f8af 502c f9af 3055 fdaf  P...p...P,..0U..
0000f260: a067 f7af 0000 0000 70f5 f8af d0e6 eeaf  .g......p.......
0000f270: 0000 0000 30f9 f8af 2060 feaf 701a feaf  ....0... `..p...
0000f280: 5057 f7af 0000 0000 706b f7af 0000 0000  PW......pk......
0000f290: 8041 f9af 0000 0000 b057 f7af 0000 0000  .A.......W......
0000f2a0: 3083 feaf 80fb f8af 9064 f7af 50c9 ecaf  0........d..P...
0000f2b0: 10b1 feaf 7058 f7af 8060 feaf c01f feaf  ....pX...`......
0000f2c0: 2020 feaf 0000 0000 0000 0000 a062 fdaf    ...........b..
0000f2d0: 8034 d1af f0e8 eeaf 20ad feaf 8043 f9af  .4...... ....C..
0000f2e0: e052 feaf 20bc feaf 4033 f8af 0000 0000  .R.. ...@3......
0000f2f0: 0000 0000 c0b2 feaf 0000 0000 0000 0000  ................
0000f300: 90b2 feaf 0000 0000 0000 0000 0000 0000  ................
0000f310: a0bd feaf 0000 0000 5032 d1af 0000 0000  ........P2......
0000f320: 0000 0000 4051 feaf 80f3 f8af 0000 0000  ....@Q..........
0000f330: 6059 efaf 503a f8af f0af feaf 0000 0000  `Y..P:..........
0000f340: 401e feaf d038 f8af 0000 0000 105c efaf  @....8.......\..
0000f350: c05e feaf 60b2 feaf f0b8 feaf 9056 f7af  .^..`........V..
0000f360: f056 f7af 0000 0000 0000 0000 1059 fdaf  .V...........Y..
0000f370: 504e fdaf 0000 0000 0000 0000 b058 fdaf  PN...........X..
0000f380: 705d efaf 8052 feaf b0d9 f8af 0000 0000  p]...R..........
0000f390: 0000 0000 90f9 f8af 0063 fdaf 7066 f7af  .........c..pf..
0000f3a0: f0f6 f8af e0bf feaf c018 edaf 0000 0000  ................
0000f3b0: 60b5 feaf 0000 0000 1067 fdaf 9061 f7af  `........g...a..
0000f3c0: 3061 f7af f02c d1af 0000 0000 0000 0000  0a...,..........
0000f3d0: 0000 0000 0000 0000 2035 f8af 0000 0000  ........ 5......
0000f3e0: 906c f7af 0000 0000 4052 fdaf 10f5 f8af  .l......@R......
0000f3f0: 90e8 eeaf 0000 0000 3064 f7af e066 f7af  ........0d...f..
0000f400: 105e f7af 7038 f8af e034 d1af 4025 feaf  .^..p8...4..@%..
0000f410: a02f d1af f0fe fdaf 5013 feaf 00bb feaf  ./......P.......
0000f420: 70ca ecaf 9059 f7af 90f6 f8af d055 f7af  p....Y.......U..
0000f430: f0a9 feaf f04f f9af 0000 0000 00af feaf  .....O..........
0000f440: 0000 0000 0000 0000 0000 0000 f05e fdaf  .............^..
0000f450: 0000 0000 302c d1af 0000 0000 705e f7af  ....0,......p^..
0000f460: 7063 f7af 0000 0000 e041 f9af d029 feaf  pc.......A...)..
0000f470: 0000 0000 0000 0000 801b edaf 6027 feaf  ............`'..
0000f480: f061 f7af 8057 f9af 7030 d1af 0000 0000  .a...W..p0......
0000f490: 0000 0000 c057 efaf c031 f8af 30b2 feaf  .....W...1..0...
0000f4a0: e055 fdaf 0000 0000 0000 0000 f05b f9af  .U...........[..
0000f4b0: 0000 0000 c034 f8af 10ca ecaf 0000 0000  .....4..........
0000f4c0: 0000 0000 1058 efaf 0000 0000 9031 d1af  .....X.......1..
0000f4d0: 0000 0000 0000 0000 d014 feaf 0031 f8af  .............1..
0000f4e0: f019 edaf 0000 0000 e032 f8af 9024 feaf  .........2...$..
0000f4f0: 0000 0000 3039 f8af b032 d1af b03a f8af  ....09...2...:..
0000f500: 0000 0000 0000 0000 0000 0000 0000 0000  ................
0000f510: 0000 0000 3059 f7af 0000 0000 f064 f7af  ....0Y.......d..
0000f520: 6034 f8af 4035 d1af b01c feaf 10b7 feaf  `4..@5..........
0000f530: 001f feaf 0000 0000 f04e fdaf 0068 f7af  .........N...h..
0000f540: 0000 0000 50f4 f8af f031 d1af 0000 0000  ....P....1......
0000f550: b015 f9af d067 fdaf 9056 fdaf e0f3 f8af  .....g...V......
0000f560: 805c feaf 50fa f8af 0000 0000 101d edaf  .\..P...........
0000f570: a0b7 feaf d05b f7af c01a edaf b057 fdaf  .....[.......W..
0000f580: 0000 0000 0000 0000 0036 d1af 1018 feaf  .........6......
0000f590: 0000 0000 1058 f7af e061 fdaf c0b8 feaf  .....X...a......
0000f5a0: 0000 0000 a021 feaf 6062 feaf 0000 0000  .....!..`b......
0000f5b0: e020 feaf 0000 0000 0000 0000 2096 f9af  . .......... ...
0000f5c0: 40e8 eeaf 0000 0000 0000 0000 d054 fdaf  @............T..
0000f5d0: 0000 0000 701d feaf 0000 0000 306c f7af  ....p.......0l..

...

```

Como el archivo es enorme, he mostrado algunas partes donde se pueden ver que no hay información y otras en las que sí que serían las que más nos interesarían a la hora de un análisis forense del fichero.


# <a></a>
### **Ejercicio 3.2.2**
#### Instalar Volatility para analizar una imagen de RAM y probar diferentes plugins para ver la información de suministran.

Para instalar este framework vamos a instalar el `python` y sus dependencia criptográfica:

```
$ sudo apt-get install python python-crypto
```

Vamos a bajarnos el código fuente del framework:

```
$ wget https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/volatility/volatility-2.3.1.tar.gz

$ ls
LiME  ram_dumps  volatility-2.3.1.tar.gz
```

Lo descomprimimos, compilamos e instalamos:

```
$ gunzip volatility-2.3.1.tar.gz 

$ ls
LiME  ram_dumps  volatility-2.3.1.tar

$ tar -xvf volatility-2.3.1.tar

$ ls
LiME  ram_dumps  volatility-2.3.1  volatility-2.3.1.tar

$ sudo mv volatility-2.3.1 /opt/

$ cd /opt/volatility-2.3.1

$ make

$ sudo make install
```

Ya tenemos el framework instalado en nuestro equipo.

Listamos los perfiles que vienen instalados:

```
$ cd /opt/volatility-2.3.1/

$ python vol.py --info|grep Linux
Volatility Foundation Volatility Framework 2.3.1
linux_banner            - Prints the Linux banner information
linux_yarascan          - A shell in the Linux memory image
```

Como podemos ver, apenas vienen perfiles, por lo que vamos a instalar nosotros los que nos recomiendan en la Wiki de Volatility (**[[1]](#ref)**):

```
$ git clone https://github.com/volatilityfoundation/profiles

$ ls -l profiles/Linux/
total 24
drwxrwxr-x 4 hugomaldonado hugomaldonado 4096 ene  7 13:29 CentOS
drwxrwxr-x 4 hugomaldonado hugomaldonado 4096 ene  7 13:29 Debian
drwxrwxr-x 4 hugomaldonado hugomaldonado 4096 ene  7 13:29 Fedora
drwxrwxr-x 4 hugomaldonado hugomaldonado 4096 ene  7 13:29 OpenSUSE
drwxrwxr-x 4 hugomaldonado hugomaldonado 4096 ene  7 13:29 RedHat
drwxrwxr-x 4 hugomaldonado hugomaldonado 4096 ene  7 13:29 Ubuntu
```

Y ahora vamos a instalarlos, según nos indican en la wiki:

```
$ cp -r profiles/Linux/Ubuntu/ /opt/volatility-2.3.1/volatility/plugins/overlays/linux/

$ cd /opt/volatility-2.3.1/

$ python vol.py --info
Volatility Foundation Volatility Framework 2.3.1


Plugins
-------
apihooks                - Detect API hooks in process and kernel memory
atoms                   - Print session and window station atom tables
atomscan                - Pool scanner for _RTL_ATOM_TABLE
bioskbd                 - Reads the keyboard buffer from Real Mode memory
callbacks               - Print system-wide notification routines
clipboard               - Extract the contents of the windows clipboard
cmdscan                 - Extract command history by scanning for _COMMAND_HISTORY
connections             - Print list of open connections [Windows XP and 2003 Only]
connscan                - Scan Physical memory for _TCPT_OBJECT objects (tcp connections)
consoles                - Extract command history by scanning for _CONSOLE_INFORMATION
crashinfo               - Dump crash-dump information
deskscan                - Poolscaner for tagDESKTOP (desktops)
devicetree              - Show device tree
dlldump                 - Dump DLLs from a process address space
dlllist                 - Print list of loaded dlls for each process
driverirp               - Driver IRP hook detection
driverscan              - Scan for driver objects _DRIVER_OBJECT
dumpcerts               - Dump RSA private and public SSL keys
dumpfiles               - Extract memory mapped and cached files
envars                  - Display process environment variables
eventhooks              - Print details on windows event hooks
evtlogs                 - Extract Windows Event Logs (XP/2003 only)
filescan                - Scan Physical memory for _FILE_OBJECT pool allocations
gahti                   - Dump the USER handle type information
gditimers               - Print installed GDI timers and callbacks
gdt                     - Display Global Descriptor Table
getservicesids          - Get the names of services in the Registry and return Calculated SID
getsids                 - Print the SIDs owning each process
handles                 - Print list of open handles for each process
hashdump                - Dumps passwords hashes (LM/NTLM) from memory
hibinfo                 - Dump hibernation file information
hivedump                - Prints out a hive
hivelist                - Print list of registry hives.
hivescan                - Scan Physical memory for _CMHIVE objects (registry hives)
hpakextract             - Extract physical memory from an HPAK file
hpakinfo                - Info on an HPAK file
idt                     - Display Interrupt Descriptor Table
iehistory               - Reconstruct Internet Explorer cache / history
imagecopy               - Copies a physical address space out as a raw DD image
imageinfo               - Identify information for the image
impscan                 - Scan for calls to imported functions
kdbgscan                - Search for and dump potential KDBG values
kpcrscan                - Search for and dump potential KPCR values
ldrmodules              - Detect unlinked DLLs
linux_arp               - Print the ARP table
linux_banner            - Prints the Linux banner information
linux_bash              - Recover bash history from bash process memory
linux_check_afinfo      - Verifies the operation function pointers of network protocols
linux_check_creds       - Checks if any processes are sharing credential structures
linux_check_evt_arm     - Checks the Exception Vector Table to look for syscall table hooking
linux_check_fop         - Check file operation structures for rootkit modifications
linux_check_idt         - Checks if the IDT has been altered
linux_check_modules     - Compares module list to sysfs info, if available
linux_check_syscall     - Checks if the system call table has been altered
linux_check_syscall_arm - Checks if the system call table has been altered
linux_check_tty         - Checks tty devices for hooks
linux_cpuinfo           - Prints info about each active processor
linux_dentry_cache      - Gather files from the dentry cache
linux_dmesg             - Gather dmesg buffer
linux_dump_map          - Writes selected memory mappings to disk
linux_find_file         - Recovers tmpfs filesystems from memory
linux_ifconfig          - Gathers active interfaces
linux_iomem             - Provides output similar to /proc/iomem
linux_keyboard_notifier - Parses the keyboard notifier call chain
linux_lsmod             - Gather loaded kernel modules
linux_lsof              - Lists open files
linux_memmap            - Dumps the memory map for linux tasks
linux_moddump           - Extract loaded kernel modules
linux_mount             - Gather mounted fs/devices
linux_mount_cache       - Gather mounted fs/devices from kmem_cache
linux_netstat           - Lists open sockets
linux_pidhashtable      - Enumerates processes through the PID hash table
linux_pkt_queues        - Writes per-process packet queues out to disk
linux_proc_maps         - Gathers process maps for linux
linux_psaux             - Gathers processes along with full command line and start time
linux_pslist            - Gather active tasks by walking the task_struct->task list
linux_pslist_cache      - Gather tasks from the kmem_cache
linux_pstree            - Shows the parent/child relationship between processes
linux_psxview           - Find hidden processes with various process listings
linux_route_cache       - Recovers the routing cache from memory
linux_sk_buff_cache     - Recovers packets from the sk_buff kmem_cache
linux_slabinfo          - Mimics /proc/slabinfo on a running machine
linux_tmpfs             - Recovers tmpfs filesystems from memory
linux_vma_cache         - Gather VMAs from the vm_area_struct cache
linux_volshell          - Shell in the memory image
linux_yarascan          - A shell in the Linux memory image
lsadump                 - Dump (decrypted) LSA secrets from the registry
mac_arp                 - Prints the arp table
mac_check_syscalls      - Checks to see if system call table entries are hooked
mac_check_sysctl        - Checks for unknown sysctl handlers
mac_check_trap_table    - Checks to see if mach trap table entries are hooked
mac_dead_procs          - Prints terminated/de-allocated processes
mac_dmesg               - Prints the kernel debug buffer
mac_dump_maps           - Dumps memory ranges of processes
mac_find_aslr_shift     - Find the ASLR shift value for 10.8+ images
mac_ifconfig            - Lists network interface information for all devices
mac_ip_filters          - Reports any hooked IP filters
mac_list_sessions       - Enumerates sessions
mac_list_zones          - Prints active zones
mac_lsmod               - Lists loaded kernel modules
mac_lsof                - Lists per-process opened files
mac_machine_info        - Prints machine information about the sample
mac_mount               - Prints mounted device information
mac_netstat             - Lists active per-process network connections
mac_notifiers           - Detects rootkits that add hooks into I/O Kit (e.g. LogKext)
mac_pgrp_hash_table     - Walks the process group hash table
mac_pid_hash_table      - Walks the pid hash table
mac_print_boot_cmdline  - Prints kernel boot arguments
mac_proc_maps           - Gets memory maps of processes
mac_psaux               - Prints processes with arguments in user land (**argv)
mac_pslist              - List Running Processes
mac_pstree              - Show parent/child relationship of processes
mac_psxview             - Find hidden processes with various process listings
mac_route               - Prints the routing table
mac_tasks               - List Active Tasks
mac_trustedbsd          - Lists malicious trustedbsd policies
mac_version             - Prints the Mac version
mac_volshell            - Shell in the memory image
mac_yarascan            - Scan memory for yara signatures
machoinfo               - Dump Mach-O file format information
malfind                 - Find hidden and injected code
mbrparser               - Scans for and parses potential Master Boot Records (MBRs)
memdump                 - Dump the addressable memory for a process
memmap                  - Print the memory map
messagehooks            - List desktop and thread window message hooks
mftparser               - Scans for and parses potential MFT entries
moddump                 - Dump a kernel driver to an executable file sample
modscan                 - Scan Physical memory for _LDR_DATA_TABLE_ENTRY objects
modules                 - Print list of loaded modules
mutantscan              - Scan for mutant objects _KMUTANT
netscan                 - Scan a Vista, 2008 or Windows 7 image for connections and sockets
patcher                 - Patches memory based on page scans
printkey                - Print a registry key, and its subkeys and values
privs                   - Display process privileges
procexedump             - Dump a process to an executable file sample
procmemdump             - Dump a process to an executable memory sample
pslist                  - Print all running processes by following the EPROCESS lists
psscan                  - Scan Physical memory for _EPROCESS pool allocations
pstree                  - Print process list as a tree
psxview                 - Find hidden processes with various process listings
raw2dmp                 - Converts a physical memory sample to a windbg crash dump
screenshot              - Save a pseudo-screenshot based on GDI windows
sessions                - List details on _MM_SESSION_SPACE (user logon sessions)
shellbags               - Prints ShellBags info
shimcache               - Parses the Application Compatibility Shim Cache registry key
sockets                 - Print list of open sockets
sockscan                - Scan Physical memory for _ADDRESS_OBJECT objects (tcp sockets)
ssdt                    - Display SSDT entries
strings                 - Match physical offsets to virtual addresses (may take a while, VERY verbose)
svcscan                 - Scan for Windows services
symlinkscan             - Scan for symbolic link objects
thrdscan                - Scan physical memory for _ETHREAD objects
threads                 - Investigate _ETHREAD and _KTHREADs
timeliner               - Creates a timeline from various artifacts in memory
timers                  - Print kernel timers and associated module DPCs
unloadedmodules         - Print list of unloaded modules
userassist              - Print userassist registry keys and information
userhandles             - Dump the USER handle tables
vaddump                 - Dumps out the vad sections to a file
vadinfo                 - Dump the VAD info
vadtree                 - Walk the VAD tree and display in tree format
vadwalk                 - Walk the VAD tree
vboxinfo                - Dump virtualbox information
vmwareinfo              - Dump VMware VMSS/VMSN information
volshell                - Shell in the memory image
windows                 - Print Desktop Windows (verbose details)
wintree                 - Print Z-Order Desktop Windows Tree
wndscan                 - Pool scanner for tagWINDOWSTATION (window stations)
yarascan                - Scan process or kernel memory with Yara signatures


Profiles
--------
LinuxUbuntu10044x64                   - A Profile for Linux Ubuntu10044 x64
LinuxUbuntu10044x86                   - A Profile for Linux Ubuntu10044 x86
LinuxUbuntu1010x64                    - A Profile for Linux Ubuntu1010 x64
LinuxUbuntu1010x86                    - A Profile for Linux Ubuntu1010 x86
LinuxUbuntu1104x64                    - A Profile for Linux Ubuntu1104 x64
LinuxUbuntu1104x86                    - A Profile for Linux Ubuntu1104 x86
LinuxUbuntu1110x64                    - A Profile for Linux Ubuntu1110 x64
LinuxUbuntu1110x86                    - A Profile for Linux Ubuntu1110 x86
LinuxUbuntu12044x64                   - A Profile for Linux Ubuntu12044 x64
LinuxUbuntu12044x86                   - A Profile for Linux Ubuntu12044 x86
LinuxUbuntu12045x64                   - A Profile for Linux Ubuntu12045 x64
LinuxUbuntu1210x64                    - A Profile for Linux Ubuntu1210 x64
LinuxUbuntu1210x86                    - A Profile for Linux Ubuntu1210 x86
LinuxUbuntu1304x64                    - A Profile for Linux Ubuntu1304 x64
LinuxUbuntu1304x86                    - A Profile for Linux Ubuntu1304 x86
LinuxUbuntu1310x64                    - A Profile for Linux Ubuntu1310 x64
LinuxUbuntu1310x86                    - A Profile for Linux Ubuntu1310 x86
LinuxUbuntu14043serverx64             - A Profile for Linux Ubuntu14043server x64
LinuxUbuntu14043x64                   - A Profile for Linux Ubuntu14043 x64
LinuxUbuntu14045x64                   - A Profile for Linux Ubuntu14045 x64
LinuxUbuntu1404x64                    - A Profile for Linux Ubuntu1404 x64
LinuxUbuntu1404x86                    - A Profile for Linux Ubuntu1404 x86
LinuxUbuntu1510serverx64              - A Profile for Linux Ubuntu1510server x64
LinuxUbuntu1510x64                    - A Profile for Linux Ubuntu1510 x64
LinuxUbuntu16041x64                   - A Profile for Linux Ubuntu16041 x64
LinuxUbuntu1604x64                    - A Profile for Linux Ubuntu1604 x64
LinuxUbuntu610x64                     - A Profile for Linux Ubuntu610 x64
LinuxUbuntu610x86                     - A Profile for Linux Ubuntu610 x86
LinuxUbuntu704x64                     - A Profile for Linux Ubuntu704 x64
LinuxUbuntu704x86                     - A Profile for Linux Ubuntu704 x86
LinuxUbuntu710x64                     - A Profile for Linux Ubuntu710 x64
LinuxUbuntu710x86                     - A Profile for Linux Ubuntu710 x86
LinuxUbuntu8044x64                    - A Profile for Linux Ubuntu8044 x64
LinuxUbuntu8044x86                    - A Profile for Linux Ubuntu8044 x86
LinuxUbuntu810x64                     - A Profile for Linux Ubuntu810 x64
LinuxUbuntu810x86                     - A Profile for Linux Ubuntu810 x86
LinuxUbuntu904x64                     - A Profile for Linux Ubuntu904 x64
LinuxUbuntu904x86                     - A Profile for Linux Ubuntu904 x86
LinuxUbuntu910x64                     - A Profile for Linux Ubuntu910 x64
LinuxUbuntu910x86                     - A Profile for Linux Ubuntu910 x86
MacElCapitan_10_11_15A284x64          - A Profile for Mac ElCapitan_10.11_15A284 x64
MacElCapitan_10_11_1_15B42x64         - A Profile for Mac ElCapitan_10.11.1_15B42 x64
MacElCapitan_10_11_2_15C50x64         - A Profile for Mac ElCapitan_10.11.2_15C50 x64
MacElCapitan_10_11_3_15D13bx64        - A Profile for Mac ElCapitan_10.11.3_15D13b x64
MacElCapitan_10_11_3_15D21x64         - A Profile for Mac ElCapitan_10.11.3_15D21 x64
MacElCapitan_10_11_4_15E65x64         - A Profile for Mac ElCapitan_10.11.4_15E65 x64
MacElCapitan_10_11_6_15G1004x64       - A Profile for Mac ElCapitan_10.11.6_15G1004 x64
MacLeopard_10_5_3_Intelx86            - A Profile for Mac Leopard_10.5.3_Intel x86
MacLeopard_10_5_4_Intelx86            - A Profile for Mac Leopard_10.5.4_Intel x86
MacLeopard_10_5_5_Intelx86            - A Profile for Mac Leopard_10.5.5_Intel x86
MacLeopard_10_5_6_Intelx86            - A Profile for Mac Leopard_10.5.6_Intel x86
MacLeopard_10_5_7_Intelx86            - A Profile for Mac Leopard_10.5.7_Intel x86
MacLeopard_10_5_8_Intelx86            - A Profile for Mac Leopard_10.5.8_Intel x86
MacLeopard_10_5_Intelx86              - A Profile for Mac Leopard_10.5_Intel x86
MacLion_10_7_1_AMDx64                 - A Profile for Mac Lion_10.7.1_AMD x64
MacLion_10_7_1_Intelx86               - A Profile for Mac Lion_10.7.1_Intel x86
MacLion_10_7_2_AMDx64                 - A Profile for Mac Lion_10.7.2_AMD x64
MacLion_10_7_2_Intelx86               - A Profile for Mac Lion_10.7.2_Intel x86
MacLion_10_7_3_AMDx64                 - A Profile for Mac Lion_10.7.3_AMD x64
MacLion_10_7_3_Intelx86               - A Profile for Mac Lion_10.7.3_Intel x86
MacLion_10_7_4_AMDx64                 - A Profile for Mac Lion_10.7.4_AMD x64
MacLion_10_7_4_Intelx86               - A Profile for Mac Lion_10.7.4_Intel x86
MacLion_10_7_5_AMDx64                 - A Profile for Mac Lion_10.7.5_AMD x64
MacLion_10_7_5_Intelx86               - A Profile for Mac Lion_10.7.5_Intel x86
MacLion_10_7_AMDx64                   - A Profile for Mac Lion_10.7_AMD x64
MacLion_10_7_Intelx86                 - A Profile for Mac Lion_10.7_Intel x86
MacMavericks_10_9_1_AMDx64            - A Profile for Mac Mavericks_10.9.1_AMD x64
MacMavericks_10_9_2_13C1021_AMDx64    - A Profile for Mac Mavericks_10.9.2_13C1021.AMD x64
MacMavericks_10_9_2__13C64_AMDx64     - A Profile for Mac Mavericks_10.9.2__13C64.AMD x64
MacMavericks_10_9_3_AMDx64            - A Profile for Mac Mavericks_10.9.3_AMD x64
MacMavericks_10_9_4_AMDx64            - A Profile for Mac Mavericks_10.9.4_AMD x64
MacMavericks_10_9_5_13F1077_AMDx64    - A Profile for Mac Mavericks_10.9.5_13F1077_AMD x64
MacMavericks_10_9_5_AMDx64            - A Profile for Mac Mavericks_10.9.5_AMD x64
MacMountainLion_10_8_1_AMDx64         - A Profile for Mac MountainLion_10.8.1_AMD x64
MacMountainLion_10_8_2_AMDx64         - A Profile for Mac MountainLion_10.8.2_AMD x64
MacMountainLion_10_8_3_AMDx64         - A Profile for Mac MountainLion_10.8.3_AMD x64
MacMountainLion_10_8_4_12e55_AMDx64   - A Profile for Mac MountainLion_10.8.4_12e55_AMD x64
MacMountainLion_10_8_5_12F2518_AMDx64 - A Profile for Mac MountainLion_10.8.5_12F2518_AMD x64
MacMountainLion_10_8_5_12f37_AMDx64   - A Profile for Mac MountainLion_10.8.5_12f37_AMD x64
MacMountainLion_10_8_5_12f45_AMDx64   - A Profile for Mac MountainLion_10.8.5_12f45_AMD x64
MacSierra_10_12_0_16A323x64           - A Profile for Mac Sierra_10.12.0_16A323 x64
MacSierra_10_12_2_16C67x64            - A Profile for Mac Sierra_10.12.2_16C67 x64
MacSnowLeopard_10_6_1_AMDx64          - A Profile for Mac SnowLeopard_10.6.1_AMD x64
MacSnowLeopard_10_6_1_Intelx86        - A Profile for Mac SnowLeopard_10.6.1_Intel x86
MacSnowLeopard_10_6_2_AMDx64          - A Profile for Mac SnowLeopard_10.6.2_AMD x64
MacSnowLeopard_10_6_2_Intelx86        - A Profile for Mac SnowLeopard_10.6.2_Intel x86
MacSnowLeopard_10_6_3_Intelx86        - A Profile for Mac SnowLeopard_10.6.3_Intel x86
MacSnowLeopard_10_6_4_AMDx64          - A Profile for Mac SnowLeopard_10.6.4_AMD x64
MacSnowLeopard_10_6_4_Intelx86        - A Profile for Mac SnowLeopard_10.6.4_Intel x86
MacSnowLeopard_10_6_5_AMDx64          - A Profile for Mac SnowLeopard_10.6.5_AMD x64
MacSnowLeopard_10_6_5_Intelx86        - A Profile for Mac SnowLeopard_10.6.5_Intel x86
MacSnowLeopard_10_6_6_AMDx64          - A Profile for Mac SnowLeopard_10.6.6_AMD x64
MacSnowLeopard_10_6_6_Intelx86        - A Profile for Mac SnowLeopard_10.6.6_Intel x86
MacSnowLeopard_10_6_7_AMDx64          - A Profile for Mac SnowLeopard_10.6.7_AMD x64
MacSnowLeopard_10_6_7_Intelx86        - A Profile for Mac SnowLeopard_10.6.7_Intel x86
MacSnowLeopard_10_6_8_AMDx64          - A Profile for Mac SnowLeopard_10.6.8_AMD x64
MacSnowLeopard_10_6_8_Intelx86        - A Profile for Mac SnowLeopard_10.6.8_Intel x86
MacSnowLeopard_10_6_AMDx64            - A Profile for Mac SnowLeopard_10.6_AMD x64
MacSnowLeopard_10_6_Intelx86          - A Profile for Mac SnowLeopard_10.6_Intel x86
MacYosemite_10_10_14A389x64           - A Profile for Mac Yosemite_10.10_14A389 x64
MacYosemite_10_10_14B25x64            - A Profile for Mac Yosemite_10.10_14B25 x64
MacYosemite_10_10_2_14C1514x64        - A Profile for Mac Yosemite_10.10.2_14C1514 x64
MacYosemite_10_10_3_14D131x64         - A Profile for Mac Yosemite_10.10.3_14D131 x64
MacYosemite_10_10_3_14D136x64         - A Profile for Mac Yosemite_10.10.3_14D136 x64
MacYosemite_10_10_4_14E46x64          - A Profile for Mac Yosemite_10.10.4_14E46 x64
MacYosemite_10_10_5_14F1021x64        - A Profile for Mac Yosemite_10.10.5_14F1021 x64
MacYosemite_10_10_5_14F27x64          - A Profile for Mac Yosemite_10.10.5_14F27 x64
VistaSP0x64                           - A Profile for Windows Vista SP0 x64
VistaSP0x86                           - A Profile for Windows Vista SP0 x86
VistaSP1x64                           - A Profile for Windows Vista SP1 x64
VistaSP1x86                           - A Profile for Windows Vista SP1 x86
VistaSP2x64                           - A Profile for Windows Vista SP2 x64
VistaSP2x86                           - A Profile for Windows Vista SP2 x86
Win2003SP0x86                         - A Profile for Windows 2003 SP0 x86
Win2003SP1x64                         - A Profile for Windows 2003 SP1 x64
Win2003SP1x86                         - A Profile for Windows 2003 SP1 x86
Win2003SP2x64                         - A Profile for Windows 2003 SP2 x64
Win2003SP2x86                         - A Profile for Windows 2003 SP2 x86
Win2008R2SP0x64                       - A Profile for Windows 2008 R2 SP0 x64
Win2008R2SP1x64                       - A Profile for Windows 2008 R2 SP1 x64
Win2008SP1x64                         - A Profile for Windows 2008 SP1 x64
Win2008SP1x86                         - A Profile for Windows 2008 SP1 x86
Win2008SP2x64                         - A Profile for Windows 2008 SP2 x64
Win2008SP2x86                         - A Profile for Windows 2008 SP2 x86
Win7SP0x64                            - A Profile for Windows 7 SP0 x64
Win7SP0x86                            - A Profile for Windows 7 SP0 x86
Win7SP1x64                            - A Profile for Windows 7 SP1 x64
Win7SP1x86                            - A Profile for Windows 7 SP1 x86
WinXPSP1x64                           - A Profile for Windows XP SP1 x64
WinXPSP2x64                           - A Profile for Windows XP SP2 x64
WinXPSP2x86                           - A Profile for Windows XP SP2 x86
WinXPSP3x86                           - A Profile for Windows XP SP3 x86


Scanner Checks
--------------
CheckHiveSig           - Check for a registry hive signature
CheckPoolIndex         - Checks the pool index
CheckPoolSize          - Check pool block size
CheckPoolType          - Check the pool type
CheckProcess           - Check sanity of _EPROCESS
CheckSocketCreateTime  - Check that _ADDRESS_OBJECT.CreateTime makes sense
CheckThreads           - Check sanity of _ETHREAD
KPCRScannerCheck       - Checks the self referential pointers to find KPCRs
MultiPrefixFinderCheck - Checks for multiple strings per page, finishing at the offset
MultiStringFinderCheck - Checks for multiple strings per page
PoolTagCheck           - This scanner checks for the occurance of a pool tag


Address Spaces
--------------
AMD64PagedMemory        - Standard AMD 64-bit address space.
ArmAddressSpace         - No docs        
FileAddressSpace        - This is a direct file AS.
HPAKAddressSpace        - This AS supports the HPAK format
IA32PagedMemory         - Standard IA-32 paging address space.
IA32PagedMemoryPae      - This class implements the IA-32 PAE paging address space. It is responsible
LimeAddressSpace        - Address space for Lime
MachOAddressSpace       - Address space for mach-o files to support atc-ny memory reader
VMWareSnapshotFile      - This AS supports VMware snapshot files
VirtualBoxCoreDumpElf64 - This AS supports VirtualBox ELF64 coredump format
WindowsCrashDumpSpace32 - This AS supports windows Crash Dump format
WindowsCrashDumpSpace64 - This AS supports windows Crash Dump format
WindowsHiberFileSpace32 - This is a hibernate address space for windows hibernation files.
```

Tras intentar varios de los plugins, en ninguno obtengo información, ni aún especificando el perfil de mi distribución. Es posible que el problema sea que la version del kernel del volcado de memoria no coincida con el asociado al perfil (tanto al que toma por defecto como si lo especifico) tal y como se indica en las issues de volatility en su github, en especial este [enlace](https://github.com/volatilityfoundation/volatility/issues/245) que aún sigue abierta la incidencia, por lo que es un error conocido y reportado pero aún no solucionado.

En la incidencia sugieren revisar la versión del kernel del volcado de memoria y aplicar el perfil en función:

```
$ strings -a /home/hugomaldonado/ram_dumps/dump2.lime | grep Kernel | grep version
        start = version[len("Darwin Kernel Version "):]
        start = version[len("Darwin Kernel Version "):]
```

Como se puede ver, la versión del kernel indica "Darwin" que es la nomenclatura de los SOs Mac, por lo que no está cogiendo la versión del guest sino del host de la máquina virtual.

Aún así probando con ese perfil asociado y con otros no he conseguido obtener información. Algunos de los que he probado han sido:

```
$ python vol.py -v -f /home/hugomaldonado/ram_dumps/dump1 memmap --profile=LinuxUbuntu16041x64

$ python vol.py -v -f /home/hugomaldonado/ram_dumps/dump1 memmap --profile=MacSierra_10_12_2_16C67x64

$ python vol.py -v -f /home/hugomaldonado/ram_dumps/dump1 memmap --profile=MacSierra_10_12_2_16C67x64

$ python vol.py -v -f /home/hugomaldonado/ram_dumps/dump1 memmap --profile=LinuxUbuntu1604x64

$ python vol.py -v -f /home/hugomaldonado/ram_dumps/dump1 memmap --profile=LinuxUbuntu1510serverx64
```


# <a></a>
## Bibliografía <a id="ref"></a>


[1] [Volatility Wiki](https://github.com/volatilityfoundation/volatility/wiki/Linux#Linux-Profiles)

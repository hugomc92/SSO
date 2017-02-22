# Práctica 2 - Ingeniería inversa y vulnerabilidades


## Sesión 1 - El formato ELF (Excutable and Linkable Format) en Linux

### **Hugo Maldonado Cózar**
# <a></a>

### **Ejercicio 2.1.1**
#### Construye y compila un programa simple, por ejemplo uno similar al "Hola, Mundo", en dos versiones: una en C y otra en C++.

Programas "Hello World":

**C** (`hello_world.c`)

```c
// Hello World program in C

#include<stdio.h>

int main() {

	printf("Hello World\n");
	
	return 0;
}
```

**C++** (`hello_world.cpp`)

```c++
// Hello World program in C++

#include<iostream>

using namespace std;

int main() {
	
	cout << "Hello World" << endl;
	
	return 0;
}
```

Compilamos y ejecutamos ambos:

```
$ gcc hello_world.c -o hello_worldC

$ g++ hello_world.cpp -o hello_worldC++

$ ls
hello_world.c  hello_worldC  hello_worldC++  hello_world.cpp

$ ./hello_worldC
Hello World

$ ./hello_worldC++ 
Hello World

```

#### a) Consulta los manuales, o en Internet que contienen las secciones `.interp`, `.got`, `.got.plt`.

- **`.interp`** (**[[1]](#ref)**) Esta sección contiene el nombre de ruta de un intérprete de programa. Si el archivo tiene un segmento cargable que incluye la reubicación, los atributos de las secciones incluirán el bit `SHF_ALLOC`, de lo contrario, ese bit estará apagado.

- **`.got`** (**[[2]](#ref)**) Esta sección es la denominada Tabla de Desplazamiento Global (Global Offset Table). Sirve principalmente para enlazar dinámicamente la dirección de memoria de un símbolo en vez de en el código binario directamente, en un enlazador dinámico.

- **`.got.plt`** (**[[3]](#ref)**) Esta sección es la denominada Tabla de Vinculación de Procedimientos (Procedure Linkage Table). Sirve para llamar a procedimientos/funciones externos cuya dirección no se conoce en el momento del enlazamiento (linking), queda resuelta por el vinculador dinámico en tiempo de ejecución.

	Por defecto la .got es rellenada dinámicamente mientras el programa esta en ejecución. La primera vez que una función es llamada, la .got contiene un puntero a la .plt donde el enlazador busca la posición actual de la función en cuestión. Cuando se encuentra su dirección, esta es escrita en la .got.
La segunda vez que una función es llamada, la .got ya contiene la posición de esa función anteriormente llamada y no hace falta volver a buscarla. A esto se le llama “lazy binding”.

#### b) Compara los ELFs de las dos versiones listando las secciones ¿hay alguna diferencia relevante respecto a las secciones de un programa compilado con gcc? ¿qué contienen las secciones `.ctors` y `.dtors`?

Vamos a listar los ELFs de ambos:

Programa escrito en `C`:

```
$ readelf -S hello_worldC
Hay 29 encabezados de sección, comenzando en el desplazamiento: 0x19a8:

Encabezados de Sección:
  [Nr] Nombre            Tipo             Dirección         Despl
       Tamaño            TamEnt           Opts   Enl   Info  Alin
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .interp           PROGBITS         0000000000000238  00000238
       000000000000001c  0000000000000000   A       0     0     1
  [ 2] .note.ABI-tag     NOTE             0000000000000254  00000254
       0000000000000020  0000000000000000   A       0     0     4
  [ 3] .note.gnu.build-i NOTE             0000000000000274  00000274
       0000000000000024  0000000000000000   A       0     0     4
  [ 4] .gnu.hash         GNU_HASH         0000000000000298  00000298
       000000000000001c  0000000000000000   A       5     0     8
  [ 5] .dynsym           DYNSYM           00000000000002b8  000002b8
       00000000000000c0  0000000000000018   A       6     1     8
  [ 6] .dynstr           STRTAB           0000000000000378  00000378
       0000000000000096  0000000000000000   A       0     0     1
  [ 7] .gnu.version      VERSYM           000000000000040e  0000040e
       0000000000000010  0000000000000002   A       5     0     2
  [ 8] .gnu.version_r    VERNEED          0000000000000420  00000420
       0000000000000020  0000000000000000   A       6     1     8
  [ 9] .rela.dyn         RELA             0000000000000440  00000440
       00000000000000f0  0000000000000018   A       5     0     8
  [10] .init             PROGBITS         0000000000000530  00000530
       0000000000000017  0000000000000000  AX       0     0     4
  [11] .plt              PROGBITS         0000000000000550  00000550
       0000000000000010  0000000000000010  AX       0     0     16
  [12] .plt.got          PROGBITS         0000000000000560  00000560
       0000000000000010  0000000000000000  AX       0     0     8
  [13] .text             PROGBITS         0000000000000570  00000570
       00000000000001c2  0000000000000000  AX       0     0     16
  [14] .fini             PROGBITS         0000000000000734  00000734
       0000000000000009  0000000000000000  AX       0     0     4
  [15] .rodata           PROGBITS         0000000000000740  00000740
       0000000000000010  0000000000000000   A       0     0     4
  [16] .eh_frame_hdr     PROGBITS         0000000000000750  00000750
       0000000000000034  0000000000000000   A       0     0     4
  [17] .eh_frame         PROGBITS         0000000000000788  00000788
       00000000000000f4  0000000000000000   A       0     0     8
  [18] .init_array       INIT_ARRAY       0000000000200dd8  00000dd8
       0000000000000008  0000000000000008  WA       0     0     8
  [19] .fini_array       FINI_ARRAY       0000000000200de0  00000de0
       0000000000000008  0000000000000008  WA       0     0     8
  [20] .jcr              PROGBITS         0000000000200de8  00000de8
       0000000000000008  0000000000000000  WA       0     0     8
  [21] .dynamic          DYNAMIC          0000000000200df0  00000df0
       00000000000001c0  0000000000000010  WA       6     0     8
  [22] .got              PROGBITS         0000000000200fb0  00000fb0
       0000000000000050  0000000000000008  WA       0     0     8
  [23] .data             PROGBITS         0000000000201000  00001000
       0000000000000010  0000000000000000  WA       0     0     8
  [24] .bss              NOBITS           0000000000201010  00001010
       0000000000000008  0000000000000000  WA       0     0     1
  [25] .comment          PROGBITS         0000000000000000  00001010
       000000000000002d  0000000000000001  MS       0     0     1
  [26] .shstrtab         STRTAB           0000000000000000  000018a6
       00000000000000fe  0000000000000000           0     0     1
  [27] .symtab           SYMTAB           0000000000000000  00001040
       0000000000000630  0000000000000018          28    45     8
  [28] .strtab           STRTAB           0000000000000000  00001670
       0000000000000236  0000000000000000           0     0     1
```

Programa escrito en `C++`:

```
$ readelf -S hello_worldC++ 
Hay 29 encabezados de sección, comenzando en el desplazamiento: 0x1c18:

Encabezados de Sección:
  [Nr] Nombre            Tipo             Dirección         Despl
       Tamaño            TamEnt           Opts   Enl   Info  Alin
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .interp           PROGBITS         0000000000000238  00000238
       000000000000001c  0000000000000000   A       0     0     1
  [ 2] .note.ABI-tag     NOTE             0000000000000254  00000254
       0000000000000020  0000000000000000   A       0     0     4
  [ 3] .note.gnu.build-i NOTE             0000000000000274  00000274
       0000000000000024  0000000000000000   A       0     0     4
  [ 4] .gnu.hash         GNU_HASH         0000000000000298  00000298
       0000000000000024  0000000000000000   A       5     0     8
  [ 5] .dynsym           DYNSYM           00000000000002c0  000002c0
       0000000000000150  0000000000000018   A       6     1     8
  [ 6] .dynstr           STRTAB           0000000000000410  00000410
       0000000000000177  0000000000000000   A       0     0     1
  [ 7] .gnu.version      VERSYM           0000000000000588  00000588
       000000000000001c  0000000000000002   A       5     0     2
  [ 8] .gnu.version_r    VERNEED          00000000000005a8  000005a8
       0000000000000040  0000000000000000   A       6     2     8
  [ 9] .rela.dyn         RELA             00000000000005e8  000005e8
       0000000000000198  0000000000000018   A       5     0     8
  [10] .init             PROGBITS         0000000000000780  00000780
       0000000000000017  0000000000000000  AX       0     0     4
  [11] .plt              PROGBITS         00000000000007a0  000007a0
       0000000000000010  0000000000000010  AX       0     0     16
  [12] .plt.got          PROGBITS         00000000000007b0  000007b0
       0000000000000028  0000000000000000  AX       0     0     8
  [13] .text             PROGBITS         00000000000007e0  000007e0
       0000000000000242  0000000000000000  AX       0     0     16
  [14] .fini             PROGBITS         0000000000000a24  00000a24
       0000000000000009  0000000000000000  AX       0     0     4
  [15] .rodata           PROGBITS         0000000000000a30  00000a30
       0000000000000011  0000000000000000   A       0     0     4
  [16] .eh_frame_hdr     PROGBITS         0000000000000a44  00000a44
       0000000000000044  0000000000000000   A       0     0     4
  [17] .eh_frame         PROGBITS         0000000000000a88  00000a88
       0000000000000134  0000000000000000   A       0     0     8
  [18] .init_array       INIT_ARRAY       0000000000200d98  00000d98
       0000000000000010  0000000000000008  WA       0     0     8
  [19] .fini_array       FINI_ARRAY       0000000000200da8  00000da8
       0000000000000008  0000000000000008  WA       0     0     8
  [20] .jcr              PROGBITS         0000000000200db0  00000db0
       0000000000000008  0000000000000000  WA       0     0     8
  [21] .dynamic          DYNAMIC          0000000000200db8  00000db8
       00000000000001d0  0000000000000010  WA       6     0     8
  [22] .got              PROGBITS         0000000000200f88  00000f88
       0000000000000078  0000000000000008  WA       0     0     8
  [23] .data             PROGBITS         0000000000201000  00001000
       0000000000000010  0000000000000000  WA       0     0     8
  [24] .bss              NOBITS           0000000000201020  00001010
       0000000000000118  0000000000000000  WA       0     0     32
  [25] .comment          PROGBITS         0000000000000000  00001010
       000000000000002d  0000000000000001  MS       0     0     1
  [26] .shstrtab         STRTAB           0000000000000000  00001b15
       00000000000000fe  0000000000000000           0     0     1
  [27] .symtab           SYMTAB           0000000000000000  00001040
       0000000000000720  0000000000000018          28    49     8
  [28] .strtab           STRTAB           0000000000000000  00001760
       00000000000003b5  0000000000000000           0     0     1
```

La primera y principal diferencia es en el tamaño de algunas de las secciones como `.gnu.hash`, `.dynsym`, `.dynstr`, `.gnu.version`, `.rela.dyn`, `.init_array`, `.fini_array`...

Otra diferencia es en la sección `.gnu.version_r` (**[[1]](#ref)**), la cual indica los requerimientos de la versión. Podemos ver como en el programa escrito en `C` la columna de dirección es `0000000000000420` en el escrito en `C++` es `00000000000005a8`, así como la de Info que en `C` indica un 1 y en `C++` un 2.

A partir de esta diferencia, ya hay muchas secciones que, lógicamente, no coinciden en direcciones de memoria.

Otra diferencia a parte de las direcciones es en la sección `.bss` (**[[1]](#ref)**), que es la sección que contiene datos que contribuyen a la imagen de memoria del programa. El programa puede tratar estos datos como no inicializados, aunque el sistema debe inicializarlos con ceros cuando el programa comience a funcionar. La diferencia está en la columna `Despl Alin`, que mientras que en `C` indica un 1 en `C++` es un 32.

La última diferencia es en la sección `.symtab` (**[[1]](#ref)**), que es la sección que contiene la tabla de símbolos. La diferencia está en la columna `Info` que mientras en `C` indica un 45 en `C++` es un 49.

La sección **`.ctors`** contiene una lista de punteros de función de constructores global. Actualmente el compilador no establece esta sección sino una que realiza la misma función pero se llama `.init_array`. 

En `C` está sección contiene:

```
[18] .init_array       INIT_ARRAY       0000000000200dd8  00000dd8
       0000000000000008  0000000000000008  WA       0     0     8
```

Y en `C++`:

```
[18] .init_array       INIT_ARRAY       0000000000200d98  00000d98
       0000000000000010  0000000000000008  WA       0     0     8
```

Se puede observar como son idénticas, salvo por las direcciones de memoria y desplazamientos que ocupan.

La sección **`.dtors`** contiene una lista de punteros de función de destructores global. Actualmente el compilador no establece esta sección sino una que realiza la misma función pero se llama `.fini_array`.

En `C` está sección contiene:

```
  [19] .fini_array       FINI_ARRAY       0000000000200de0  00000de0
       0000000000000008  0000000000000008  WA       0     0     8
```

Y en C++:

```
  [19] .fini_array       FINI_ARRAY       0000000000200da8  00000da8
       0000000000000008  0000000000000008  WA       0     0     8
```


Se puede observar como son idénticas, salvo por las direcciones de memoria y desplazamientos que ocupan.


#### c) Con la opción `readelf -r` podemos ver las secciones de reubicación. Indicar qué contienen estas secciones.

Programa en `C`:

```
$ readelf -r hello_worldC

La sección de reubicación '.rela.dyn' en el desplazamiento 0x440 contiene 10 entradas:
  Desplaz         Info           Tipo           Val. Símbolo  Nom. Símbolo + Adend
000000200dd8  000000000008 R_X86_64_RELATIVE                    670
000000200de0  000000000008 R_X86_64_RELATIVE                    630
000000201008  000000000008 R_X86_64_RELATIVE                    201008
000000200fc8  000100000006 R_X86_64_GLOB_DAT 0000000000000000 _ITM_deregisterTMClone + 0
000000200fd0  000200000006 R_X86_64_GLOB_DAT 0000000000000000 puts@GLIBC_2.2.5 + 0
000000200fd8  000300000006 R_X86_64_GLOB_DAT 0000000000000000 __libc_start_main@GLIBC_2.2.5 + 0
000000200fe0  000400000006 R_X86_64_GLOB_DAT 0000000000000000 __gmon_start__ + 0
000000200fe8  000500000006 R_X86_64_GLOB_DAT 0000000000000000 _Jv_RegisterClasses + 0
000000200ff0  000600000006 R_X86_64_GLOB_DAT 0000000000000000 _ITM_registerTMCloneTa + 0
000000200ff8  000700000006 R_X86_64_GLOB_DAT 0000000000000000 __cxa_finalize@GLIBC_2.2.5 + 0
```

Programa en `C++`:

```
$ readelf -r hello_worldC++

La sección de reubicación '.rela.dyn' en el desplazamiento 0x5e8 contiene 17 entradas:
  Desplaz         Info           Tipo           Val. Símbolo  Nom. Símbolo + Adend
000000200d98  000000000008 R_X86_64_RELATIVE                    8e0
000000200da0  000000000008 R_X86_64_RELATIVE                    98c
000000200da8  000000000008 R_X86_64_RELATIVE                    8a0
000000201008  000000000008 R_X86_64_RELATIVE                    201008
000000200fa0  000100000006 R_X86_64_GLOB_DAT 0000000000000000 __gmon_start__ + 0
000000200fa8  000200000006 R_X86_64_GLOB_DAT 0000000000000000 _Jv_RegisterClasses + 0
000000200fb0  000300000006 R_X86_64_GLOB_DAT 0000000000000000 _ZNSt8ios_base4InitC1E@GLIBCXX_3.4 + 0
000000200fb8  000400000006 R_X86_64_GLOB_DAT 0000000000000000 __libc_start_main@GLIBC_2.2.5 + 0
000000200fc0  000500000006 R_X86_64_GLOB_DAT 0000000000000000 __cxa_atexit@GLIBC_2.2.5 + 0
000000200fc8  000600000006 R_X86_64_GLOB_DAT 0000000000000000 _ZNSt8ios_base4InitD1E@GLIBCXX_3.4 + 0
000000200fd0  000700000006 R_X86_64_GLOB_DAT 0000000000000000 _ITM_deregisterTMClone + 0
000000200fd8  000800000006 R_X86_64_GLOB_DAT 0000000000000000 _ZStlsISt11char_traits@GLIBCXX_3.4 + 0
000000200fe0  000900000006 R_X86_64_GLOB_DAT 0000000000000000 _ITM_registerTMCloneTa + 0
000000200fe8  000a00000006 R_X86_64_GLOB_DAT 0000000000000000 __cxa_finalize@GLIBC_2.2.5 + 0
000000200ff0  000b00000006 R_X86_64_GLOB_DAT 0000000000000000 _ZNSolsEPFRSoS_E@GLIBCXX_3.4 + 0
000000200ff8  000c00000006 R_X86_64_GLOB_DAT 0000000000000000 _ZSt4endlIcSt11char_tr@GLIBCXX_3.4 + 0
000000201020  000d00000005 R_X86_64_COPY     0000000000201020 _ZSt4cout@GLIBCXX_3.4 + 0
```

Este comando muestra la información de reubicación de la sección `rela.dyn`, que indica las varialbes que deben ser reubicadas en tiempo de ejecución.

Podemos ver como en el programa en `C` contiene 10 entradas mientras que en `C++` contiene 17 entradas. Hay algunas que son comunes en ambos, pero se puede apreciar como `C++` necesita mas reubicaciones, probablemente por el uso de librerías más complejas como `iostream` frente a `stdio.h` de `C`.

# <a></a>
### **Ejercicio 2.1.2**
#### Mira en el manual en línea o en Internet las opciones de la orden `objdump`, e indica:
#### a) Qué opciones nos permiten ver la información que nos suministra `readelf`.

Estas son las opciones que podemos utilizar al usar la orden `objdump`:

```
$ objdump 
Modo de empleo: objdump <opcion(es)> <fichero(s)>
 Muestra la información de <fichero(s)> objeto.
 Se requiere por lo menos una de los siguientes opciones:
  -a, --archive-headers    Display archive header information
  -f, --file-headers       Display the contents of the overall file header
  -p, --private-headers    Display object format specific file header contents
  -P, --private=OPT,OPT... Display object format specific contents
  -h, --[section-]headers  Display the contents of the section headers
  -x, --all-headers        Display the contents of all headers
  -d, --disassemble        Display assembler contents of executable sections
  -D, --disassemble-all    Display assembler contents of all sections
  -S, --source             Intermix source code with disassembly
  -s, --full-contents      Display the full contents of all sections requested
  -g, --debugging          Display debug information in object file
  -e, --debugging-tags     Display debug information using ctags style
  -G, --stabs              Display (in raw form) any STABS info in the file
  -W[lLiaprmfFsoRt] or
  --dwarf[=rawline,=decodedline,=info,=abbrev,=pubnames,=aranges,=macro,=frames,
          =frames-interp,=str,=loc,=Ranges,=pubtypes,
          =gdb_index,=trace_info,=trace_abbrev,=trace_aranges,
          =addr,=cu_index]
                           Display DWARF info in the file
  -t, --syms               Display the contents of the symbol table(s)
  -T, --dynamic-syms       Display the contents of the dynamic symbol table
  -r, --reloc              Display the relocation entries in the file
  -R, --dynamic-reloc      Display the dynamic relocation entries in the file
  @<file>                  Read options from <file>
  -v, --version            Display this program's version number
  -i, --info               List object formats and architectures supported
  -H, --help               Display this information
```

Toda la información que podemos obtener con `readelf -a` (que nos muestra la información completa del fichero `ELF`) la podemos obtener con `objdump -apfhxtR` (e incluso más).

#### b) Qué otras opciones nos permite realizar `objdump` desde el punto de la ingeniería inversa.

Una opción muy interesante de `objdump` desde el punto de la ingeniería inversa es la opción de descompilarlo mediante la orden `objdump -s`:

```
$ objdump -s hello_worldC

hello_worldC:     formato del fichero elf64-x86-64

Contenido de la sección .interp:
 0238 2f6c6962 36342f6c 642d6c69 6e75782d  /lib64/ld-linux-
 0248 7838362d 36342e73 6f2e3200           x86-64.so.2.    
Contenido de la sección .note.ABI-tag:
 0254 04000000 10000000 01000000 474e5500  ............GNU.
 0264 00000000 02000000 06000000 20000000  ............ ...
Contenido de la sección .note.gnu.build-id:
 0274 04000000 14000000 03000000 474e5500  ............GNU.
 0284 c06b214d cd68f414 4cef188a 8cf52180  .k!M.h..L.....!.
 0294 f2681482                             .h..            
Contenido de la sección .gnu.hash:
 0298 01000000 01000000 01000000 00000000  ................
 02a8 00000000 00000000 00000000           ............    
Contenido de la sección .dynsym:
 02b8 00000000 00000000 00000000 00000000  ................
 02c8 00000000 00000000 31000000 20000000  ........1... ...
 02d8 00000000 00000000 00000000 00000000  ................
 02e8 0b000000 12000000 00000000 00000000  ................
 02f8 00000000 00000000 1f000000 12000000  ................
 0308 00000000 00000000 00000000 00000000  ................
 0318 4d000000 20000000 00000000 00000000  M... ...........
 0328 00000000 00000000 5c000000 20000000  ........\... ...
 0338 00000000 00000000 00000000 00000000  ................
 0348 70000000 20000000 00000000 00000000  p... ...........
 0358 00000000 00000000 10000000 22000000  ............"...
 0368 00000000 00000000 00000000 00000000  ................
Contenido de la sección .dynstr:
 0378 006c6962 632e736f 2e360070 75747300  .libc.so.6.puts.
 0388 5f5f6378 615f6669 6e616c69 7a65005f  __cxa_finalize._
 0398 5f6c6962 635f7374 6172745f 6d61696e  _libc_start_main
 03a8 005f4954 4d5f6465 72656769 73746572  ._ITM_deregister
 03b8 544d436c 6f6e6554 61626c65 005f5f67  TMCloneTable.__g
 03c8 6d6f6e5f 73746172 745f5f00 5f4a765f  mon_start__._Jv_
 03d8 52656769 73746572 436c6173 73657300  RegisterClasses.
 03e8 5f49544d 5f726567 69737465 72544d43  _ITM_registerTMC
 03f8 6c6f6e65 5461626c 6500474c 4942435f  loneTable.GLIBC_
 0408 322e322e 3500                        2.2.5.          
Contenido de la sección .gnu.version:
 040e 00000000 02000200 00000000 00000200  ................
Contenido de la sección .gnu.version_r:
 0420 01000100 01000000 10000000 00000000  ................
 0430 751a6909 00000200 8a000000 00000000  u.i.............
Contenido de la sección .rela.dyn:
 0440 d80d2000 00000000 08000000 00000000  .. .............
 0450 70060000 00000000 e00d2000 00000000  p......... .....
 0460 08000000 00000000 30060000 00000000  ........0.......
 0470 08102000 00000000 08000000 00000000  .. .............
 0480 08102000 00000000 c80f2000 00000000  .. ....... .....
 0490 06000000 01000000 00000000 00000000  ................
 04a0 d00f2000 00000000 06000000 02000000  .. .............
 04b0 00000000 00000000 d80f2000 00000000  .......... .....
 04c0 06000000 03000000 00000000 00000000  ................
 04d0 e00f2000 00000000 06000000 04000000  .. .............
 04e0 00000000 00000000 e80f2000 00000000  .......... .....
 04f0 06000000 05000000 00000000 00000000  ................
 0500 f00f2000 00000000 06000000 06000000  .. .............
 0510 00000000 00000000 f80f2000 00000000  .......... .....
 0520 06000000 07000000 00000000 00000000  ................
Contenido de la sección .init:
 0530 4883ec08 488b05a5 0a200048 85c07402  H...H.... .H..t.
 0540 ffd04883 c408c3                      ..H....         
Contenido de la sección .plt:
 0550 ff35620a 2000ff25 640a2000 0f1f4000  .5b. ..%d. ...@.
Contenido de la sección .plt.got:
 0560 ff256a0a 20006690 ff258a0a 20006690  .%j. .f..%.. .f.
Contenido de la sección .text:
 0570 31ed4989 d15e4889 e24883e4 f050544c  1.I..^H..H...PTL
 0580 8d05aa01 0000488d 0d330100 00488d3d  ......H..3...H.=
 0590 0c010000 ff153e0a 2000f40f 1f440000  ......>. ....D..
 05a0 488d3d69 0a200048 8d05690a 20005548  H.=i. .H..i. .UH
 05b0 29f84889 e54883f8 0e761548 8b05060a  ).H..H...v.H....
 05c0 20004885 c074095d ffe0660f 1f440000   .H..t.]..f..D..
 05d0 5dc30f1f 4000662e 0f1f8400 00000000  ]...@.f.........
 05e0 488d3d29 0a200048 8d35220a 20005548  H.=). .H.5". .UH
 05f0 29fe4889 e548c1fe 034889f0 48c1e83f  ).H..H...H..H..?
 0600 4801c648 d1fe7418 488b05e1 09200048  H..H..t.H.... .H
 0610 85c0740c 5dffe066 0f1f8400 00000000  ..t.]..f........
 0620 5dc30f1f 4000662e 0f1f8400 00000000  ]...@.f.........
 0630 803dd909 20000075 2748833d b7092000  .=.. ..u'H.=.. .
 0640 00554889 e5740c48 8b3dba09 2000e815  .UH..t.H.=.. ...
 0650 ffffffe8 48ffffff 5dc605b0 09200001  ....H...].... ..
 0660 f3c30f1f 4000662e 0f1f8400 00000000  ....@.f.........
 0670 488d3d71 07200048 833f0075 0be95eff  H.=q. .H.?.u..^.
 0680 ffff660f 1f440000 488b0559 09200048  ..f..D..H..Y. .H
 0690 85c074e9 554889e5 ffd05de9 40ffffff  ..t.UH....].@...
 06a0 554889e5 488d3d99 000000e8 b0feffff  UH..H.=.........
 06b0 b8000000 005dc366 0f1f8400 00000000  .....].f........
 06c0 41574156 4189ff41 5541544c 8d250607  AWAVA..AUATL.%..
 06d0 20005548 8d2d0607 20005349 89f64989   .UH.-.. .SI..I.
 06e0 d54c29e5 4883ec08 48c1fd03 e83ffeff  .L).H...H....?..
 06f0 ff4885ed 742031db 0f1f8400 00000000  .H..t 1.........
 0700 4c89ea4c 89f64489 ff41ff14 dc4883c3  L..L..D..A...H..
 0710 014839dd 75ea4883 c4085b5d 415c415d  .H9.u.H...[]A\A]
 0720 415e415f c390662e 0f1f8400 00000000  A^A_..f.........
 0730 f3c3                                 ..              
Contenido de la sección .fini:
 0734 4883ec08 4883c408 c3                 H...H....       
Contenido de la sección .rodata:
 0740 01000200 48656c6c 6f20576f 726c6400  ....Hello World.
Contenido de la sección .eh_frame_hdr:
 0750 011b033b 34000000 05000000 00feffff  ...;4...........
 0760 80000000 20feffff 50000000 50ffffff  .... ...P...P...
 0770 a8000000 70ffffff c8000000 e0ffffff  ....p...........
 0780 10010000                             ....            
Contenido de la sección .eh_frame:
 0788 14000000 00000000 017a5200 01781001  .........zR..x..
 0798 1b0c0708 90010710 14000000 1c000000  ................
 07a8 c8fdffff 2b000000 00000000 00000000  ....+...........
 07b8 14000000 00000000 017a5200 01781001  .........zR..x..
 07c8 1b0c0708 90010000 24000000 1c000000  ........$.......
 07d8 78fdffff 10000000 000e1046 0e184a0f  x..........F..J.
 07e8 0b770880 003f1a3b 2a332422 00000000  .w...?.;*3$"....
 07f8 1c000000 44000000 a0feffff 17000000  ....D...........
 0808 00410e10 8602430d 06520c07 08000000  .A....C..R......
 0818 44000000 64000000 a0feffff 65000000  D...d.......e...
 0828 00420e10 8f02420e 188e0345 0e208d04  .B....B....E. ..
 0838 420e288c 05480e30 8606480e 3883074d  B.(..H.0..H.8..M
 0848 0e40720e 38410e30 410e2842 0e20420e  .@r.8A.0A.(B. B.
 0858 18420e10 420e0800 14000000 ac000000  .B..B...........
 0868 c8feffff 02000000 00000000 00000000  ................
 0878 00000000                             ....            
Contenido de la sección .init_array:
 200dd8 70060000 00000000                    p.......        
Contenido de la sección .fini_array:
 200de0 30060000 00000000                    0.......        
Contenido de la sección .jcr:
 200de8 00000000 00000000                    ........        
Contenido de la sección .dynamic:
 200df0 01000000 00000000 01000000 00000000  ................
 200e00 0c000000 00000000 30050000 00000000  ........0.......
 200e10 0d000000 00000000 34070000 00000000  ........4.......
 200e20 19000000 00000000 d80d2000 00000000  .......... .....
 200e30 1b000000 00000000 08000000 00000000  ................
 200e40 1a000000 00000000 e00d2000 00000000  .......... .....
 200e50 1c000000 00000000 08000000 00000000  ................
 200e60 f5feff6f 00000000 98020000 00000000  ...o............
 200e70 05000000 00000000 78030000 00000000  ........x.......
 200e80 06000000 00000000 b8020000 00000000  ................
 200e90 0a000000 00000000 96000000 00000000  ................
 200ea0 0b000000 00000000 18000000 00000000  ................
 200eb0 15000000 00000000 00000000 00000000  ................
 200ec0 03000000 00000000 b00f2000 00000000  .......... .....
 200ed0 07000000 00000000 40040000 00000000  ........@.......
 200ee0 08000000 00000000 f0000000 00000000  ................
 200ef0 09000000 00000000 18000000 00000000  ................
 200f00 18000000 00000000 00000000 00000000  ................
 200f10 fbffff6f 00000000 01000008 00000000  ...o............
 200f20 feffff6f 00000000 20040000 00000000  ...o.... .......
 200f30 ffffff6f 00000000 01000000 00000000  ...o............
 200f40 f0ffff6f 00000000 0e040000 00000000  ...o............
 200f50 f9ffff6f 00000000 03000000 00000000  ...o............
 200f60 00000000 00000000 00000000 00000000  ................
 200f70 00000000 00000000 00000000 00000000  ................
 200f80 00000000 00000000 00000000 00000000  ................
 200f90 00000000 00000000 00000000 00000000  ................
 200fa0 00000000 00000000 00000000 00000000  ................
Contenido de la sección .got:
 200fb0 f00d2000 00000000 00000000 00000000  .. .............
 200fc0 00000000 00000000 00000000 00000000  ................
 200fd0 00000000 00000000 00000000 00000000  ................
 200fe0 00000000 00000000 00000000 00000000  ................
 200ff0 00000000 00000000 00000000 00000000  ................
Contenido de la sección .data:
 201000 00000000 00000000 08102000 00000000  .......... .....
Contenido de la sección .comment:
 0000 4743433a 20285562 756e7475 20362e32  GCC: (Ubuntu 6.2
 0010 2e302d35 7562756e 74753132 2920362e  .0-5ubuntu12) 6.
 0020 322e3020 32303136 31303035 00        2.0 20161005.
```

La cual puede servir de gran ayuda, ya que tanto podemos ver las direcciones utilizadas por el programa, como en algunos puntos lo que contienen las mismas (véase como en el ejemplo se puede ver claramente la palabra "Hello World" que se imprime).

Otra opción realmente útil es la opción de "desmontarlo", es decir, de ver las instrucciones en lenguaje máquina (código ensamblador del programa):

```
$ objdump -d hello_worldC

hello_worldC:     formato del fichero elf64-x86-64


Desensamblado de la sección .init:

0000000000000530 <_init>:
 530:	48 83 ec 08          	sub    $0x8,%rsp
 534:	48 8b 05 a5 0a 20 00 	mov    0x200aa5(%rip),%rax        # 200fe0 <_GLOBAL_OFFSET_TABLE_+0x30>
 53b:	48 85 c0             	test   %rax,%rax
 53e:	74 02                	je     542 <_init+0x12>
 540:	ff d0                	callq  *%rax
 542:	48 83 c4 08          	add    $0x8,%rsp
 546:	c3                   	retq   

Desensamblado de la sección .plt:

0000000000000550 <.plt>:
 550:	ff 35 62 0a 20 00    	pushq  0x200a62(%rip)        # 200fb8 <_GLOBAL_OFFSET_TABLE_+0x8>
 556:	ff 25 64 0a 20 00    	jmpq   *0x200a64(%rip)        # 200fc0 <_GLOBAL_OFFSET_TABLE_+0x10>
 55c:	0f 1f 40 00          	nopl   0x0(%rax)

Desensamblado de la sección .plt.got:

0000000000000560 <.plt.got>:
 560:	ff 25 6a 0a 20 00    	jmpq   *0x200a6a(%rip)        # 200fd0 <_GLOBAL_OFFSET_TABLE_+0x20>
 566:	66 90                	xchg   %ax,%ax
 568:	ff 25 8a 0a 20 00    	jmpq   *0x200a8a(%rip)        # 200ff8 <_GLOBAL_OFFSET_TABLE_+0x48>
 56e:	66 90                	xchg   %ax,%ax

Desensamblado de la sección .text:

0000000000000570 <_start>:
 570:	31 ed                	xor    %ebp,%ebp
 572:	49 89 d1             	mov    %rdx,%r9
 575:	5e                   	pop    %rsi
 576:	48 89 e2             	mov    %rsp,%rdx
 579:	48 83 e4 f0          	and    $0xfffffffffffffff0,%rsp
 57d:	50                   	push   %rax
 57e:	54                   	push   %rsp
 57f:	4c 8d 05 aa 01 00 00 	lea    0x1aa(%rip),%r8        # 730 <__libc_csu_fini>
 586:	48 8d 0d 33 01 00 00 	lea    0x133(%rip),%rcx        # 6c0 <__libc_csu_init>
 58d:	48 8d 3d 0c 01 00 00 	lea    0x10c(%rip),%rdi        # 6a0 <main>
 594:	ff 15 3e 0a 20 00    	callq  *0x200a3e(%rip)        # 200fd8 <_GLOBAL_OFFSET_TABLE_+0x28>
 59a:	f4                   	hlt    
 59b:	0f 1f 44 00 00       	nopl   0x0(%rax,%rax,1)

00000000000005a0 <deregister_tm_clones>:
 5a0:	48 8d 3d 69 0a 20 00 	lea    0x200a69(%rip),%rdi        # 201010 <__TMC_END__>
 5a7:	48 8d 05 69 0a 20 00 	lea    0x200a69(%rip),%rax        # 201017 <__TMC_END__+0x7>
 5ae:	55                   	push   %rbp
 5af:	48 29 f8             	sub    %rdi,%rax
 5b2:	48 89 e5             	mov    %rsp,%rbp
 5b5:	48 83 f8 0e          	cmp    $0xe,%rax
 5b9:	76 15                	jbe    5d0 <deregister_tm_clones+0x30>
 5bb:	48 8b 05 06 0a 20 00 	mov    0x200a06(%rip),%rax        # 200fc8 <_GLOBAL_OFFSET_TABLE_+0x18>
 5c2:	48 85 c0             	test   %rax,%rax
 5c5:	74 09                	je     5d0 <deregister_tm_clones+0x30>
 5c7:	5d                   	pop    %rbp
 5c8:	ff e0                	jmpq   *%rax
 5ca:	66 0f 1f 44 00 00    	nopw   0x0(%rax,%rax,1)
 5d0:	5d                   	pop    %rbp
 5d1:	c3                   	retq   
 5d2:	0f 1f 40 00          	nopl   0x0(%rax)
 5d6:	66 2e 0f 1f 84 00 00 	nopw   %cs:0x0(%rax,%rax,1)
 5dd:	00 00 00 

00000000000005e0 <register_tm_clones>:
 5e0:	48 8d 3d 29 0a 20 00 	lea    0x200a29(%rip),%rdi        # 201010 <__TMC_END__>
 5e7:	48 8d 35 22 0a 20 00 	lea    0x200a22(%rip),%rsi        # 201010 <__TMC_END__>
 5ee:	55                   	push   %rbp
 5ef:	48 29 fe             	sub    %rdi,%rsi
 5f2:	48 89 e5             	mov    %rsp,%rbp
 5f5:	48 c1 fe 03          	sar    $0x3,%rsi
 5f9:	48 89 f0             	mov    %rsi,%rax
 5fc:	48 c1 e8 3f          	shr    $0x3f,%rax
 600:	48 01 c6             	add    %rax,%rsi
 603:	48 d1 fe             	sar    %rsi
 606:	74 18                	je     620 <register_tm_clones+0x40>
 608:	48 8b 05 e1 09 20 00 	mov    0x2009e1(%rip),%rax        # 200ff0 <_GLOBAL_OFFSET_TABLE_+0x40>
 60f:	48 85 c0             	test   %rax,%rax
 612:	74 0c                	je     620 <register_tm_clones+0x40>
 614:	5d                   	pop    %rbp
 615:	ff e0                	jmpq   *%rax
 617:	66 0f 1f 84 00 00 00 	nopw   0x0(%rax,%rax,1)
 61e:	00 00 
 620:	5d                   	pop    %rbp
 621:	c3                   	retq   
 622:	0f 1f 40 00          	nopl   0x0(%rax)
 626:	66 2e 0f 1f 84 00 00 	nopw   %cs:0x0(%rax,%rax,1)
 62d:	00 00 00 

0000000000000630 <__do_global_dtors_aux>:
 630:	80 3d d9 09 20 00 00 	cmpb   $0x0,0x2009d9(%rip)        # 201010 <__TMC_END__>
 637:	75 27                	jne    660 <__do_global_dtors_aux+0x30>
 639:	48 83 3d b7 09 20 00 	cmpq   $0x0,0x2009b7(%rip)        # 200ff8 <_GLOBAL_OFFSET_TABLE_+0x48>
 640:	00 
 641:	55                   	push   %rbp
 642:	48 89 e5             	mov    %rsp,%rbp
 645:	74 0c                	je     653 <__do_global_dtors_aux+0x23>
 647:	48 8b 3d ba 09 20 00 	mov    0x2009ba(%rip),%rdi        # 201008 <__dso_handle>
 64e:	e8 15 ff ff ff       	callq  568 <_init+0x38>
 653:	e8 48 ff ff ff       	callq  5a0 <deregister_tm_clones>
 658:	5d                   	pop    %rbp
 659:	c6 05 b0 09 20 00 01 	movb   $0x1,0x2009b0(%rip)        # 201010 <__TMC_END__>
 660:	f3 c3                	repz retq 
 662:	0f 1f 40 00          	nopl   0x0(%rax)
 666:	66 2e 0f 1f 84 00 00 	nopw   %cs:0x0(%rax,%rax,1)
 66d:	00 00 00 

0000000000000670 <frame_dummy>:
 670:	48 8d 3d 71 07 20 00 	lea    0x200771(%rip),%rdi        # 200de8 <__JCR_END__>
 677:	48 83 3f 00          	cmpq   $0x0,(%rdi)
 67b:	75 0b                	jne    688 <frame_dummy+0x18>
 67d:	e9 5e ff ff ff       	jmpq   5e0 <register_tm_clones>
 682:	66 0f 1f 44 00 00    	nopw   0x0(%rax,%rax,1)
 688:	48 8b 05 59 09 20 00 	mov    0x200959(%rip),%rax        # 200fe8 <_GLOBAL_OFFSET_TABLE_+0x38>
 68f:	48 85 c0             	test   %rax,%rax
 692:	74 e9                	je     67d <frame_dummy+0xd>
 694:	55                   	push   %rbp
 695:	48 89 e5             	mov    %rsp,%rbp
 698:	ff d0                	callq  *%rax
 69a:	5d                   	pop    %rbp
 69b:	e9 40 ff ff ff       	jmpq   5e0 <register_tm_clones>

00000000000006a0 <main>:
 6a0:	55                   	push   %rbp
 6a1:	48 89 e5             	mov    %rsp,%rbp
 6a4:	48 8d 3d 99 00 00 00 	lea    0x99(%rip),%rdi        # 744 <_IO_stdin_used+0x4>
 6ab:	e8 b0 fe ff ff       	callq  560 <_init+0x30>
 6b0:	b8 00 00 00 00       	mov    $0x0,%eax
 6b5:	5d                   	pop    %rbp
 6b6:	c3                   	retq   
 6b7:	66 0f 1f 84 00 00 00 	nopw   0x0(%rax,%rax,1)
 6be:	00 00 

00000000000006c0 <__libc_csu_init>:
 6c0:	41 57                	push   %r15
 6c2:	41 56                	push   %r14
 6c4:	41 89 ff             	mov    %edi,%r15d
 6c7:	41 55                	push   %r13
 6c9:	41 54                	push   %r12
 6cb:	4c 8d 25 06 07 20 00 	lea    0x200706(%rip),%r12        # 200dd8 <__frame_dummy_init_array_entry>
 6d2:	55                   	push   %rbp
 6d3:	48 8d 2d 06 07 20 00 	lea    0x200706(%rip),%rbp        # 200de0 <__init_array_end>
 6da:	53                   	push   %rbx
 6db:	49 89 f6             	mov    %rsi,%r14
 6de:	49 89 d5             	mov    %rdx,%r13
 6e1:	4c 29 e5             	sub    %r12,%rbp
 6e4:	48 83 ec 08          	sub    $0x8,%rsp
 6e8:	48 c1 fd 03          	sar    $0x3,%rbp
 6ec:	e8 3f fe ff ff       	callq  530 <_init>
 6f1:	48 85 ed             	test   %rbp,%rbp
 6f4:	74 20                	je     716 <__libc_csu_init+0x56>
 6f6:	31 db                	xor    %ebx,%ebx
 6f8:	0f 1f 84 00 00 00 00 	nopl   0x0(%rax,%rax,1)
 6ff:	00 
 700:	4c 89 ea             	mov    %r13,%rdx
 703:	4c 89 f6             	mov    %r14,%rsi
 706:	44 89 ff             	mov    %r15d,%edi
 709:	41 ff 14 dc          	callq  *(%r12,%rbx,8)
 70d:	48 83 c3 01          	add    $0x1,%rbx
 711:	48 39 dd             	cmp    %rbx,%rbp
 714:	75 ea                	jne    700 <__libc_csu_init+0x40>
 716:	48 83 c4 08          	add    $0x8,%rsp
 71a:	5b                   	pop    %rbx
 71b:	5d                   	pop    %rbp
 71c:	41 5c                	pop    %r12
 71e:	41 5d                	pop    %r13
 720:	41 5e                	pop    %r14
 722:	41 5f                	pop    %r15
 724:	c3                   	retq   
 725:	90                   	nop
 726:	66 2e 0f 1f 84 00 00 	nopw   %cs:0x0(%rax,%rax,1)
 72d:	00 00 00 

0000000000000730 <__libc_csu_fini>:
 730:	f3 c3                	repz retq 

Desensamblado de la sección .fini:

0000000000000734 <_fini>:
 734:	48 83 ec 08          	sub    $0x8,%rsp
 738:	48 83 c4 08          	add    $0x8,%rsp
 73c:	c3                   	retq  
```

Esto es realmente útil porque si se conoce código ensamblador, se puede saber exactamente qué hace el programa y cómo lo hace, e incluso editar las instrucciones (con algún editor de código que pueda leer la información en binario o el código ensamblador del mismo) para que realicen algo distinto (siempre que se tengan los permisos adecuados para poder realizar alguna modificación).

Con la orden `objdump -D` podemos ver aún más información del código ensamblador.

Otra opción interesantes es poder ver la información de depuración (`DWARF`) con la orden `objdump -W`.

# <a></a>
### **Ejercicio 2.1.3**
#### Modifica el programa realizado en el ejercicio anterior para que el programa se detenga durante un rato, por ejemplo, con un `sleep()`, al objeto de que podamos visualizar el archivo `/proc/<PID>/maps` de su ejecución. Ahora analiza la información de su `ELF` para entrever cómo se ha construido dicho proceso a través de la información del `ELF`, por ejemplo, las direcciones y permisos de las regiones de texto y datos, etc.

Modificación del programa en `C`:

```c
// Hello World program in C

#include<stdio.h>
#include <unistd.h>

int main() {

    sleep(500);
    printf("Hello World\n");

    return 0;
}
```

Le he puesto que espere 500 segundos antes de mostrar el mensajep ara poder analizar el archivo asociado.

Una vez compilado vamos a lanzarlo y con otra terminal (en concreto con otra instancia de la utilidad `screen` vamos a ver cual es su `PID` y analizar el fichero asociado).

```
$ ps -ax | grep hello
 3020 ?        Ss     0:00 SCREEN -S hello
 3031 pts/2    S+     0:00 ./hello_worldCSleep
```

Una vez que ya tenemos su `PID`:

```
$ cat /proc/3031/maps 
address           		  perms offset  dev   inode   					 pathname
561f8e09d000-561f8e09e000 r-xp 00000000 08:01 131087                     /home/hugomaldonado/hello_worldCSleep
561f8e29d000-561f8e29e000 r--p 00000000 08:01 131087                     /home/hugomaldonado/hello_worldCSleep
561f8e29e000-561f8e29f000 rw-p 00001000 08:01 131087                     /home/hugomaldonado/hello_worldCSleep
7f027e26e000-7f027e42b000 r-xp 00000000 08:01 1442283                    /lib/x86_64-linux-gnu/libc-2.24.so
7f027e42b000-7f027e62b000 ---p 001bd000 08:01 1442283                    /lib/x86_64-linux-gnu/libc-2.24.so
7f027e62b000-7f027e62f000 r--p 001bd000 08:01 1442283                    /lib/x86_64-linux-gnu/libc-2.24.so
7f027e62f000-7f027e631000 rw-p 001c1000 08:01 1442283                    /lib/x86_64-linux-gnu/libc-2.24.so
7f027e631000-7f027e635000 rw-p 00000000 00:00 0 
7f027e635000-7f027e65a000 r-xp 00000000 08:01 1442259                    /lib/x86_64-linux-gnu/ld-2.24.so
7f027e84d000-7f027e84f000 rw-p 00000000 00:00 0 
7f027e856000-7f027e859000 rw-p 00000000 00:00 0 
7f027e859000-7f027e85a000 r--p 00024000 08:01 1442259                    /lib/x86_64-linux-gnu/ld-2.24.so
7f027e85a000-7f027e85b000 rw-p 00025000 08:01 1442259                    /lib/x86_64-linux-gnu/ld-2.24.so
7f027e85b000-7f027e85c000 rw-p 00000000 00:00 0 
7ffd6d51e000-7ffd6d53f000 rw-p 00000000 00:00 0                          [stack]
7ffd6d59c000-7ffd6d59e000 r--p 00000000 00:00 0                          [vvar]
7ffd6d59e000-7ffd6d5a0000 r-xp 00000000 00:00 0                          [vdso]
ffffffffff600000-ffffffffff601000 r-xp 00000000 00:00 0                  [vsyscall]
```

Si comparamos las direcciones de memoria de las tres primeras líneas con los datos obtenidos de las secciones con la orden `readelf -S` o con la orden `objdump -d`, podemos ver como las tres primeras líneas de este archivo representan respectivamente a segmento de código, de datos y BSS.

El segmento de código almacena el binario del proceso, el segmento de datos almacena las variables estáticas inicializadas por el programador y el segmento BSS almacena las variables estáticas que no han sido inicializadas pero que el sistema rellena con ceros.

En este archivo podemos ver como la sección de código tiene permisos `r-xp` (escritura, ejecución y de página privada), el de datos `r--p` (lectura y de página privada) y el de BSS `rw-p` (lectura, escritura y de página privada).

Además con la opción `-d` o `-D` de `objdump` podríamos analizar su código ensamblador para ver las llamadas que se van haciendo y ver en qué momento y cómo se hace la espera ociosa por la instrucción `sleep`, así como el resto de ejecución del programa.

# <a></a>
## Bibliografía <a id="ref"></a>


[1] [Secciones](https://refspecs.linuxfoundation.org/LSB_1.2.0/gLSB/specialsections.html)

[2] [.got](http://bottomupcs.sourceforge.net/csbu/x3824.htm)

[3] [.got.plt](http://reverseengineering.stackexchange.com/questions/1992/what-is-plt-got)
# Práctica 2 - Ingeniería inversa y vulnerabilidades


## Sesión 2 - Explotaciones y protecciones del formato ELF

### **Hugo Maldonado Cózar**
# <a></a>

### **Ejercicio 2.2.1**
#### Para el sistema que utilizas, indica la arquitectura, distribución y compilador que utilizas e indica que protecciones se utilizan de cara a proteger un binario ELF.

Distribución:

```
$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 16.10
Release:	16.10
Codename:	yakkety
```

Arquitectura y Kernel:

```
$ uname -a
Linux ubuntu-sso 4.8.0-30-generic #32-Ubuntu SMP Fri Dec 2 03:43:27 UTC 2016 x86_64 x86_64 x86_64 GNU/Linux
```

Compilador:

```
$ gcc --version
gcc (Ubuntu 6.2.0-5ubuntu12) 6.2.0 20161005
Copyright (C) 2016 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

Los archivos ELF tienen varios sistemas de protección incluidos.

**Desbordamiento de buffer**:

Protección para evitar el desbordamiento del buffer, tanto en el kernel como en el compilador

- **Kernel**
	
	Aleatorizacion de la disposicion del espacio de direcciones (ALSR), ademas se construyen de forma independiente (PIE - position independent executable) con la opción `n-fPIE`, esto implica una penalizacion entre 5%-10%.

- **Compilador**

	- Proteccion de pila
	- Fuente fortificada, incluimos el flag `-D_FORTIFY_SOURCE=1`.
	- Protector de aplastamiento de pila (SSP), incluye una clave en la pila, que en el momento en que sea machacada por otra informacion arroja un error y sale del programa.

**Pila no ejecutable**

Para ello podemos usar el bit XD (executable disable, Intel) o el bit NX (no execute, AMD). Este es el bit mas significativo de una Entrada en la Tabla de Paginas (PTE) que indica si la página es ejecutable (NX=0) o no (NX=1).

Aunque tambien es posible cambiar los permisos de ejecucion de la pila con `execstack -s program`.

**Bypass de la protección de la pila**

Se puede realizar un bypass aunque hayamos protegido la ejecución de la pila imponiendo el flag -z noexecstack durante la compilación.

**Aleatorizacion del Espacio de Direcciones**

Podemos defendernos contra un shellcode reubicando las regiones de memoria de un proceso. De forma que si programamos un shellcode para una dirección fija, arrojará siempre un `Segmentation Fault`.

Esto lo haremos cuando se construye con soporte para código independiente de la ejecución `gcc -fPIC`.

**Comprobacion de ASLR**

Posee la opcion por defecto de aleatoriedad en pila, página `vdso`, memoria compartida y segmento de datos

# <a></a>
### **Ejercicio 2.2.2**
#### Podemos mejorar el siguiente virus:
#### a) Escribiendo un mejor scaner/limpiador para él.

Examinando el virus podemos ver que hay una funcion de payload en el que podemos introducir cualquier codigo malicioso que queramos.

El trozo de codigo malicioso se adjunta al ejecutable (prepend) y se ejecuta en primer lugar el codigo malicioso y en segundo lugar el main tradicional.

**Escapar los bytes de infección en el archivo**
	
Para esto podriamos usar `dd` que es una utilidad para copiar archivos byte a byte y podemos especificar el offset de skip que en este caso sería el numero de bytes VIRUS_SIZE que figura en el código de lx3k2.

```bash
#!/bin/sh
VIRUS_SIZE=12397
MAGIC_SIZE=4
INFECTED=$1
if [ -z "$INFECTED" ];then
	echo "Usage $0 <infected file>"
	exit 1
fi
INFECTED_SIZE=`ls -l $INFECTED| awk '$5 {print $5}'`
ORIG_SIZE=`expr $INFECTED_SIZE - $VIRUS_SIZE - $MAGIC_SIZE`

echo "Tamaño del fragmento de Virus: $VIRUS_SIZE"
echo "Tamaño del original: $ORIG_SIZE"
mv $INFECTED $INFECTED.vx
dd if=$INFECTED.vx of=$INFECTED count=$ORIG_SIZE skip=$VIRUS_SIZE bs=1
chmod $INFECTED --reference $INFECTED.vx
echo "Se ha limpiado $INFECTED, se deja una copia del archivo infectado en $INFECTED.vx"
```

Comprobamos que efectivamente hace su cometido:

```
$ ls tests/
hello_world.c  hello_worldC
$ scanelf -eq test 2>/dev/null
RWX --- ---  dir-pruebas/chello.vx
$ ./test/hello_worldC
Segmentation Fault
```

Otra opción para mejorar el virus podría ser salteando el lugar donde lo introducimos para hacer más difícil la creación de un limpiador, pues en vez de truncar el fichero para eliminarlo habría que analizar en que parte comienza el virus e incluso si está salteado por el código y se insertan los saltos para cambiar de un sitio a otro sería aún más complicado. Además si encima le agregamos algunos datos aleatorios sería todavía mas difícil, ya que no podría buscarse por tamaño completo.

#### b) Añadir `#ifdef` para comprobar la arquitectura de forma que cambie de forma automática el valor `EM_386` en tiempo de compilación para adaptarlo al sistema donde estemos.

En primer lugar vamos a examinar en el sistema si hubiera una estructura de `Elf64_Ehdr` dentro de la cabecera `elf.h` para simplificarnos el trabajo.

```
$ locate elf.h
...
/usr/src/linux-headers-4.8.0-30/include/linux/elf.h
/usr/src/linux-headers-4.8.0-30/include/uapi/linux/elf.h
...

$ cat /usr/src/linux-headers-4.8.0-30/include/uapi/linux/elf.h
...
typedef struct elf64_hdr {
  unsigned char	e_ident[EI_NIDENT];	/* ELF "magic number" */
  Elf64_Half e_type;
  Elf64_Half e_machine;
  Elf64_Word e_version;
  Elf64_Addr e_entry;		/* Entry point virtual address */
  Elf64_Off e_phoff;		/* Program header table file offset */
  Elf64_Off e_shoff;		/* Section header table file offset */
  Elf64_Word e_flags;
  Elf64_Half e_ehsize;
  Elf64_Half e_phentsize;
  Elf64_Half e_phnum;
  Elf64_Half e_shentsize;
  Elf64_Half e_shnum;
  Elf64_Half e_shstrndx;
} Elf64_Ehdr;
...
```

Ahora, vamos a añadir lo necesario al código para adaptarlo al sistema donde estemos:

```C
#if __GNUC__
	#if __x86_64__ || __ppc64__
		#define 64ENV
	#else
		#define 32ENV
	#endif
#endif

...

int infect(char *filename, int hd, char *virus)
{
   ...
   #ifdef 64ENV
        Elf64_Ehdr hdr;
    #else
        Elf32_Ehdr hdr;
    #endif
   ...
```

Y ya lo tendríamos adaptado para 32 y 64 bits, lo que es la inmensa mayoría de las arquitecturas de ordenadores actuales.

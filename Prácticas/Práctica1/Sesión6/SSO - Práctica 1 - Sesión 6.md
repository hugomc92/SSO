# Práctica 1 - Encriptación/desencriptación en Linux


## Sesión 6 - Encriptación de archivos

### **Hugo Maldonado Cózar**
# <a></a>

### **Ejercicio 6.1**
#### Utilizar gpg para crear vuestras claves públicas y privadas. Una vez creadas, agruparos por pareja y enviaros un archivo/mensaje encriptado. Indicar todos los pasos que habéis seguido.

Lo primero voy a crear mis claves privada y pública. Para ello:

```bash
$ gpg --full-gen-key
gpg (GnuPG) 2.1.15; Copyright (C) 2016 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Por favor seleccione tipo de clave deseado:
   (1) RSA y RSA (por defecto)
   (2) DSA y ElGamal
   (3) DSA (sólo firmar)
   (4) RSA (sólo firmar)
Su elección: 1
las claves RSA pueden tener entre 1024 y 4096 bits de longitud.
¿De qué tamaño quiere la clave? (2048) 4096
El tamaño requerido es de 4096 bits
Por favor, especifique el período de validez de la clave.
         0 = la clave nunca caduca
      <n>  = la clave caduca en n días
      <n>w = la clave caduca en n semanas
      <n>m = la clave caduca en n meses
      <n>y = la clave caduca en n años
¿Validez de la clave (0)? 0
La clave nunca caduca
¿Es correcto? (s/n) s

GnuPG debe construir un ID de usuario para identificar su clave.

Nombre y apellidos: Hugo Maldonado
Dirección de correo electrónico: hugomaldonado@correo.ugr.es
Comentario: SSO
Ha seleccionado este ID de usuario:
    "Hugo Maldonado (SSO) <hugomaldonado@correo.ugr.es>"

¿Cambia (N)ombre, (C)omentario, (D)irección o (V)ale/(S)alir? V
Es necesario generar muchos bytes aleatorios. Es una buena idea realizar
alguna otra tarea (trabajar en otra ventana/consola, mover el ratón, usar
la red y los discos) durante la generación de números primos. Esto da al
generador de números aleatorios mayor oportunidad de recoger suficiente
entropía.
gpg: clave FCF742D6B6FD7446 marcada como de confianza absoluta
gpg: directory '/home/hugomaldonado/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/home/hugomaldonado/.gnupg/openpgp-revocs.d/80C95532001A4210539B646EFCF742D6B6FD7446.rev'
claves pública y secreta creadas y firmadas.

pub   rsa4096 2016-11-14 [SC]
      80C95532001A4210539B646EFCF742D6B6FD7446
uid                      Hugo Maldonado (SSO) <hugomaldonado@correo.ugr.es>
sub   rsa4096 2016-11-14 [E]
```

En mi caso, he indicado que quiero generar mis claves privada y pública de tipo `RSA` (ambas), con una longitud de 4096 bits para asegurar la fortaleza de la misma y con un periodo de validez sin caducidad.
Durante el proceso también nos pide una contraseña para la generación de dichas claves.

```bash
$ gpg --list-keys
gpg: comprobando base de datos de confianza
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: nivel: 0  validez:   1  firmada:   0  confianza: 0-, 0q, 0n, 0m, 0f, 1u
/home/hugomaldonado/.gnupg/pubring.kbx
--------------------------------------
pub   rsa4096 2016-11-14 [SC]
      80C95532001A4210539B646EFCF742D6B6FD7446
uid           [  absoluta ] Hugo Maldonado (SSO) <hugomaldonado@correo.ugr.es>
sub   rsa4096 2016-11-14 [E]

hugomaldonado@ubuntuserver:~$ gpg --list-secret-keys
/home/hugomaldonado/.gnupg/pubring.kbx
--------------------------------------
sec   rsa4096 2016-11-14 [SC]
      80C95532001A4210539B646EFCF742D6B6FD7446
uid           [  absoluta ] Hugo Maldonado (SSO) <hugomaldonado@correo.ugr.es>
ssb   rsa4096 2016-11-14 [E]
```

Ahora, exportamos ambas claves a ficheros para poder utilizarlas en el cifrado/descifrado de otros archivos. 

Primero vamos a generar el fichero para la clave pública:

```bash
$ gpg --armor --export --output hugo_pubkey.gpg Hugo 

$ ls
hugo_pubkey.gpg

$ cat hugo_pubkey.gpg 
-----BEGIN PGP PUBLIC KEY BLOCK-----

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
9aNTWrEZ7TTtC1gGVBS4zQ==
=hSne
-----END PGP PUBLIC KEY BLOCK-----
```

Y ahora la clave privada (aunque no es necesario para el desarrollo de esta práctica, lo hacemos para indicar cómo se debería hacer en caso de necesitar transpasar el fichero de clave privada a un computador diferente, por ejemplo para una migración de servidor):

```bash
$ gpg --armor --export-secret-key --output hugo_privkey.gpg Hugo Maldonado
hugomaldonado@ubuntuserver:~$ ls
hugo_privkey.gpg  hugo_pubkey.gpg
```

Por motivos de seguridad, no se debe mostrar el contenido de la clave privada como sí he mostrado el contenido de la clave pública. La privada debe ser un fichero que sepamos que está a buen recaudo y al que nadie excepto nosotros vamos a poder tener acceso.

Vamos a comprobar cuáles son las huellas de mis claves, para que cuando vayan a descrifrar nuestros mensajes comprueben que es una clave verídica:

```bash
# Sacamos la huella de la creada anteriormente
$ gpg --fingerprint Hugo Maldonado
pub   rsa4096 2016-11-14 [SC]
      80C9 5532 001A 4210 539B  646E FCF7 42D6 B6FD 7446
uid           [  absoluta ] Hugo Maldonado (SSO) <hugomaldonado@correo.ugr.es>
sub   rsa4096 2016-11-14 [E]

# Sacamos la huella del fichero
$ gpg -fingerprint hugo_pubkey.gpg
pub   rsa4096 2016-11-14 [SC]
      80C95532001A4210539B646EFCF742D6B6FD7446
uid           Hugo Maldonado (SSO) <hugomaldonado@correo.ugr.es>
sub   rsa4096 2016-11-14 [E]
```

Vemos como ambas coinciden y es la que tenemos que indicarle a alguien cuando vaya a descrifrar alguno de nuestros mensajes/ficheros cifrados.

Ahora vamos a mandarle a **Román** mi clave pública para que pueda encriptar un mensaje/fichero y sólo yo pueda desencriptarlos. Así como vamos a recibir su clave pública para encriptarle un mensaje/fichero y que sólo él pueda leerlo.

```bash
$ ls
burial92_pubkey.gpg  hugo_pubkey.gpg  prueba.txt
hugo_privkey.gpg     prueba2.txt      prueba.txt.asc
```

Como se puede ver, ya tengo en el directorio la clave pública de **Román**.

Le pregunto cual es la huella digital de su clave pública: **647B E09E B5FD BB3B 2FE4  CA15 B5C6 0F62 8F96 771A**.

Vamos a comprobar si la clave que me ha mandado tiene la misma huella digital:

```bash
$ cat burial92_pubkey.gpg 
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG v1

mQENBFgp2D4BCACo8i9ROqrevTDV7ffk+xFwi/arCuR2W1UXvyWa/Tp/ZA4wt8Gr
AuJExZ7U/Lwijk6CUU2KmTLpvc8dNtherA7wsVC2RQ7x9AGVmdRzmPFExwwR8K16
x/bUn+SNYIgYfz17UtH59338dvWqbtn8+06vli86z9RXdo5LmEO6O7LdjTNqe3LS
qkHK+Qsu24bDbuazncDbiibY3kU4Sr+GSIi+/QiQIZd06C9wvdJKS3NvRdRilDs/
Ca5jL7oTPyhq+0T9gO8WjrT5rd6t0g6lrsczVehrk8r8wepmfS13BBM2M65pwkd2
hfuX1oq5A4bgLGkx5T6buyGTdNYzKQzzqLJXABEBAAG0NFJvbWFuIEFycmFueiBH
dWVycmVybyAoU1NPIEdQRykgPGJ1cmlhbDkyQGdtYWlsLmNvbT6JATgEEwECACIF
Algp2D4CGwMGCwkIBwMCBhUIAgkKCwQWAgMBAh4BAheAAAoJELXGD2KPlncaDxsH
/2LemRW6CSjdxHuSRHwa/2lxNzD9hRsuS5bvpuBv8W1VGwqvwfgkI3WZoTuoUC1M
4xXVfI9hLuVQOqcOF+qv5d2V7yIX0yPZ2/paNN6DjX7k1fB+q+LZvRX58GDkMTrX
ChmO5yEm4GwZu1Q+SJAShJPLkeBkkRruTNszpf8T7K3+/LEGqzx2uJK4xLGa+J6o
sAUt9yWCNO69sKR05DlOweZhBmnycGBsYaFzCvetqfdXSi7UUGU0Oc475skwO71A
bqS1U+Z3/MEBdWYuKUtOolhRhG5US6zNUTUXB6pnBQGxpPcnajIzIQXclHy8aBEx
YkC5u5EszM3OdMuA/pM63ru5AQ0EWCnYPgEIAL3kUnTtnOKBjBun8iFXTwHgo0gA
5zB3jg5jI8fNqB1GMvGoaxpD43PE2FnCfmch8kjeWvwYbAD11cb1L47cQY5c3AN6
GIBuZbJtu9JuqQQuqICWqVKCoMhwbnSk6KKwoSFW8eiXiCzbQGSl/GCcFqRiPVju
gD90VsKFXKIDvUDYClYzUKAB8HSfPqhzvV6O/T76qKaFFsET8TvidD5k79WQEt5i
l72qn+5FQXFaGe5AEJGQaSeHJg56hdr/L0bTyMNuPAs9+PhzCxlOpcFgN4iTk8e5
tlti5sMBjmvWYZv0ds3dIo4UM9wdNQcI0wOX7xJhuNyNzhmCwIE+/Qb4KEEAEQEA
AYkBHwQYAQIACQUCWCnYPgIbDAAKCRC1xg9ij5Z3GsOGB/kBtWzxqSPQKe3yOr4G
7lnyHJoF3GdN/N/A1pC2sX/8N9w0RXnVYYRB0i40Le532N3iX1Di6E14V6WWyUt5
oPIyg/TNp6+ZDc7RdB301f0+Y3+cxJJDoqVDjU1WZZgqXprl7LNhY5Sg8SrePc4I
ydtil9cEv3NkgtQyNdNlx7dO10Ss/eLBBqJTHJ7xV3Ryr6v9+xfFQvJPkTmJhKsJ
HVpFD86qNftS/N7PfD6/qRkweGDgPOvuCfj2PvgHQv7xsL22vb5jwo4Ja9tNVtWW
v0QUBDZIMSoDYBjySXTpbHo0Q1ud5ehLBzWLFW+1JzFs5DLOfqcg6fZRaqMDEzpz
e5Tg
=QzE7
-----END PGP PUBLIC KEY BLOCK-----

$ gpg -fingerprint burial92_pubkey.gpg 
pub   rsa2048 2016-11-14 [SC]
      647BE09EB5FDBB3B2FE4CA15B5C60F628F96771A
uid           Roman Arranz Guerrero (SSO GPG) <burial92@gmail.com>
sub   rsa2048 2016-11-14 [E]
```

Como podemos ver, la huella digital que le he preguntado y la que obtengo con la orden `gpg` coinciden, por lo que damos por válida y autenticada la clave que me ha mandado. Por lo que podemos importarla en nuestro anillo sin problema alguno:

```bash
$ gpg --import burial92_pubkey.gpg
gpg: clave B5C60F628F96771A: clave pública "Roman Arranz Guerrero (SSO GPG) <burial92@gmail.com>" importada
gpg: Cantidad total procesada: 1
gpg:               importadas: 1

$ gpg --list-keys
/home/hugomaldonado/.gnupg/pubring.kbx
--------------------------------------
pub   rsa4096 2016-11-14 [SC]
      80C95532001A4210539B646EFCF742D6B6FD7446
uid           [  absoluta ] Hugo Maldonado (SSO) <hugomaldonado@correo.ugr.es>
sub   rsa4096 2016-11-14 [E]

pub   rsa2048 2016-11-14 [SC]
      647BE09EB5FDBB3B2FE4CA15B5C60F628F96771A
uid           [desconocida] Roman Arranz Guerrero (SSO GPG) <burial92@gmail.com>
sub   rsa2048 2016-11-14 [E]
```

Ahora, vamos a crear el fichero que vamos a cifrar con su clave pública:

```bash
$ echo "FICHERO DE PRUEBA PARA ENCRIPTACIÓN CON CLAVE PRIVADA GPG" > prueba.txt

$ ls
burial92_pubkey.gpg  hugo_privkey.gpg  hugo_pubkey.gpg  prueba.txt

$ cat prueba.txt 
FICHERO DE PRUEBA PARA ENCRIPTACIÓN CON CLAVE PRIVADA GPG
```

Acto seguido, vamos a cifrarlo con la clave pública de **Román** para que sólo él pueda desencriptarlo con su clave privada:

```bash
$ gpg --armor --recipient burial92@gmail.com --encrypt prueba.txt 
gpg: E9C2342C18C572D9: No hay seguridad de que esta clave pertenezca realmente
al usuario que se nombra
sub  rsa2048/E9C2342C18C572D9 2016-11-14 Roman Arranz Guerrero (SSO GPG) <burial92@gmail.com>
 Huella clave primaria: 647B E09E B5FD BB3B 2FE4  CA15 B5C6 0F62 8F96 771A
      Huella de subclave: B848 48CD D82F 78A5 E017  4879 E9C2 342C 18C5 72D9

No es seguro que la clave pertenezca a la persona que se nombra en el
identificador de usuario. Si *realmente* sabe lo que está haciendo,
puede contestar sí a la siguiente pregunta.

¿Usar esta clave de todas formas? (s/N) s

$ ls
burial92_pubkey.gpg  hugo_privkey.gpg  hugo_pubkey.gpg  prueba.txt  prueba.txt.asc

$ cat prueba.txt.asc 
-----BEGIN PGP MESSAGE-----

hQEMA+nCNCwYxXLZAQf+I2UCRRJNLormmfYTyOYpi8lHmQkqYVLDnIRYuyzydPjh
EPCIuXSISuBHS/anxaa1cC7/33YTDk/JQ0xSapAljd9hmw9AAIXM5jhbNkwjw1N1
G55ZbWYiygjeWfdnYRSXr5Vc340eRrD78UGt/X0XMwcO2kpsJ3agMbQA9RY9/SW5
XcdOVuxIxZ4WQeZgwlQOpHc7/WQLFz/6qk8ENiXzFo9Vn2nrUoOxYzF5QIgyBkhC
JFEhn4i45ht1YC1DSFZI5Wp1T/fypejiQ2XAG2LafWZ7ZwzA3m9oJJXUACbMNSwq
UuA6NORvhpQdCtZcK0CLpbOl5mPdmjJR+ptFQQUUZdJ9ASR0N9o9DPAIPmQvJXGE
fwcWP6E9wa0Claa3ijqkXGj1eF2tlqranEKoY+ZD7gwack1Yc4EdGqUtkxPYMeL7
uKgeTgKdRp5Bp62ALFGO2bUk+cHaiWm1oaywVH1JiHvkT7YBRhqzhCbkF4DjdOJx
Eoz0nMsz+zzYVLE41SU=
=Oh/8
-----END PGP MESSAGE-----
```

Por lo que vemos que se ha encriptado correctamente.

Como se puede ver, nos ha avisado de que no hay seguridad de que dicha clave pertenezca realmente al usuario que dice, pero como anteriormente ya hemos comprobado la huella digital de la misma, podemos usarla con tranquilidad, pues sabemos que está autenticada.

Ahora se lo mando a **Román** para que proceda a desencriptarlo.

A continuación vamos a proceder a desencriptar el fichero que nos ha mandado cifrado con mi llave pública que ya le he mandado anteriormente:

```bash
$ ls
archivo.txt.asc      hugo_privkey.gpg  prueba.txt
burial92_pubkey.gpg  hugo_pubkey.gpg   prueba.txt.asc
```

Podemos ver que el fichero que nos ha mandado es `archivo.txt.asc`.

Continuamos con la desencriptación:

```bash
$ gpg --output archivo.txt --decrypt archivo.txt.asc 
gpg: cifrado con clave RSA de 4096 bits, ID EA0D8B06BA238086, creada el 2016-11-14
      "Hugo Maldonado (SSO) <hugomaldonado@correo.ugr.es>"

$ ls
archivo.txt      burial92_pubkey.gpg  hugo_pubkey.gpg  prueba.txt.asc
archivo.txt.asc  hugo_privkey.gpg     prueba.txt

$ cat archivo.txt
Esto esta cifrado
```

Preguntándole a **Román**, efectivamente ese era el contenido del fichero cifrado que me había mandado.

Como se puede ver, he podido descrifrar con mi clave privada el archivo que **Román** me ha mandado encriptado con mi clave pública.


# <a></a>
### **Ejercicio 6.2**
#### Consulta la documentación de OpenSSL para encriptar/desecriptar un archivo/mensaje con una clave de tu eleción.

Lo primero que vamos a hacer es crear un fichero de prueba para realizar el ejercicio.

```bash
$ echo "FICHERO DE PRUEBA PARA ENCRIPTACIÓN CON OPENSSL" > prueba.txt

$ ls
prueba.txt

$ cat prueba.txt 
FICHERO DE PRUEBA PARA ENCRIPTACIÓN CON OPENSSL
```

Para este ejemplo, voy a utilizar el algoritmo de cifrado `AES` con una clave de longitud de 256 bits para garantizar una seguridad más que suficiente. Aunque utilicemos este algoritmo, vamos a introducir la clave que vamos a utilizar para que se compute la clave secreta. 

Según podemos ver en la página del manual de `OpenSSL` (**[[1]](#ref)**), podemos utilizar la opción `enc` para encriptar un fichero con un cifrado específico, en este caso el descrito anteriormente. También vamos a utilizar la opción `-salt` que añade más dureza a la encriptación.

```bash
# Comprobamos qué ficheros hay actualmente
$ ls
prueba.txt

# Mostramos el contenido del fichero que vamos a encriptar
$ cat prueba.txt 
FICHERO DE PRUEBA PARA ENCRIPTACIÓN CON OPENSSL

# Encriptamos el fichero. Nos pide una contraseña para el método de encriptación AES que debemos introducir
$ openssl enc -aes-256-cbc -salt -in prueba.txt -out prueba.enc
enter aes-256-cbc encryption password:
Verifying - enter aes-256-cbc encryption password:

# Mostramos los ficheros después de la encriptación.
# Vemos cómo efectivamente se ha creado el fichero encriptado
$ ls
prueba.enc  prueba.txt

# Intentamos ver el fichero encriptado
$ cat prueba.enc
Salted__#??C-???????i٧?=?98????0xNe??N??1????_O???/g?d!??NX)V??U8??P?ɐ?
```

Ahora, podemos corroborar como efectivamente hemos encriptado el fichero, y es imposible leer ni deducir el contenido del mismo una vez cifrado.

Para desencriptarlo, tenemos que introducir la opción `-d` de la orden `openssl`

```bash
# Desencriptamos. Nos pide la contraseña del fichero encriptado.
$ openssl enc -aes-256-cbc -d -in prueba.enc -out prueba-d.txt
enter aes-256-cbc decryption password:

# Mostramos los archivos que hay actuales
$ ls
prueba-d.txt  prueba.enc  prueba.txt

# Mostramos el fichero donde hemos guardado la información desencriptada.
$ cat prueba-d.txt 
FICHERO DE PRUEBA PARA ENCRIPTACIÓN CON OPENSSL
```

Como podemos comprobar, se ha desencriptrado con éxito al introducir la contraseña con la que lo encriptamos y el fichero desencriptado es exactamente igual al fichero que encriptamos la primera vez.

Ahora bien, vamos a encriptar el mismo archivo pero vamos a utilizar mi clave publica que generamos en el ejercicio anterior, para que sólo quien la tenga pueda desencriptarlo.

```
# Borramos los archivos anteriores
$ rm prueba-d.txt
$ rm prueba.enc

$ ls
hugo_pubkey.gpg prueba.txt

$ openssl enc -aes-256-cbc -pass file:hugo_pubkey.gpg -salt -in prueba.txt -out prueba.enc

$ ls
hugo_privkey.gpg  hugo_pubkey.gpg  prueba.enc  prueba.txt

$ cat prueba.enc 
Salted__?[??.?'Ho???S?pj?7Y!??"?K@??d??iٮCqz7????{??L??i?ڦ??g#??????b?
```

Ya lo tenemos encriptado con esta clave. Ahora vamos a desencriptarlo de la forma que lo haría otra persona que tuviera la clave:

```
$ openssl enc -aes-256-cbc -pass file:hugo_pubkey.gpg -d -in prueba.enc -out prueba2.txt

$ ls
hugo_privkey.gpg  hugo_pubkey.gpg  prueba2.txt  prueba.enc  prueba.txt

$ cat prueba2.txt 
FICHERO DE PRUEBA PARA ENCRIPTACIÓN CON OPENSSL
```

Se puede ver que se desencripta correctamente y se puede ver el contenido original del fichero, sin tener que introducir ningún tipo de contraseña.



# <a></a>
### **Ejercicio 6.3**
####  Busca información sobre la vulnerabilidad para poder contestar a las siguientes cuestiones:

**[[2]](#ref)**

#### a) ¿En qué consiste la vulnerabilidad Heartbleed?

La vulnerabilidad `Heartbleed` es un bug detectado en la bliblioteca de criptografía `OpenSSL`.

Consiste principalmente en permitir el robo de información protegida por el cifrado `SSL`/`TLS` utilizado (como uso mayoritario) en la encriptación de la información transimitida través de internet.

Más concretamente, esta vulnerabilidad permite a cualquier usuario de internet leer la memoria de los sistemas protegidos por las versiones vulnerables de `OpenSSL` sin dejar ningún tipo de rastro. Esto compromete las claves de seguridad secretas utilizadas para identificar a los proveedores de servicios y para cifrar el tráfico, los nombres y contraseñas de los usuarios, el contenido real... Simplemente vigilando las comunicaciones se pueden obtener los datos que se desean.

Técnicamente este bug lo que permite es que el servidor que use `OpenSSL` para cifrar las comunicaciones no compruebe la longitud de la palabra leída, por lo que cualquiera le podría decir que busque una palabra más corta y espere una longitud mayor, dándole información abitraria y recibiendo la información de vuelta.

Aunque el fallo fue reportado por unos ingenieros de seguridad de `Google` el 7 de abril de 2014, ya llevaba unos 2 años descubierto, y durante todo ese tiempo, gente con el conocimiento necesario y sin poder ser rastreada de ninguna manera, pudo estar explotándolo y obteniendo información que debería ser completamente segura.

#### b) ¿Cómo saber si mi sistema la sufre?

Para saber si nuestro sistema la sufre, lo primero que debemos saber es la version de nuestra librería de encriptación `OpenSSL`, para ello:

```bash
$ openssl version
OpenSSL 1.0.2g  1 Mar 2016
```

En mi caso tengo la versión 1.0.2g que ya es una versión de la librería que arregló esta vulnerabilidad (y es lo más normal, ya que se parcheó en 2014 al poco de ser descubierta).

Toda versión que esté en el rango 1.0.1 a 1.0.1f sufre esta vulnerabilidad, pues este bug se podría aplicar en cualquiera de esas versiones.	


#### c) ¿Cómo puede subsanarla?

Si nuestro sistema la sufre, es porque no tenemos la versión de `OpenSSL` actualizada, por lo que la forma de subsanarlo es actualizando cuanto antes a las últimas versiones que sí que tienen arreglada esta vulnerabilidad.



# <a></a>
## Bibliografía <a id="ref"></a>


[1] [OpenSSL](https://linux.die.net/man/1/openssl)

[2] [Heartbleed](http://heartbleed.com/)
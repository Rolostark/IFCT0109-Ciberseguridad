---
title: "🪐 Laboratorio VENUS (HackMyVM) — Reto de Linux y Escalada"
aliases:
  - VM Venus
  - Venus HackMyVM
  - Walkthrough Venus
tags:
  - ifct0109/pocs
  - linux
  - ctf
  - hackmyvm
  - writeup
  - venus
---

# 🪐 Laboratorio VENUS (HackMyVM) — Reto Completo de Linux

> [!abstract] Enunciado del Reto
> **Venus** es un entorno de laboratorio técnico estilo OverTheWire / Bandit alojado en la plataforma **HackMyVM**. Consta de una serie progresiva de misiones donde el alumno debe investigar el sistema de archivos, filtrar información oculta, evadir restricciones, abusar de permisos y escalar privilegios secuencialmente de usuario en usuario hasta conseguir el control total.

---

# 🪐 Laboratorio VENUS de HackMyVM

> 📋 INFORMACIÓN DEL LABORATORIO

**Características de conexión:**

* Host: venus.hackmyvm.eu

* Port: 5000

* User: hacker

* Pass: havefun!

## 📖 Descripción

Venus es un laboratorio CTF (Capture The Flag) de la plataforma HackMyVM que consta de **50 flags** y algunos ocultos adicionales. Está disponible en línea a través de SSH y es ideal para practicar habilidades en Linux.

## 🔌 Conexión al Laboratorio

Para conectarnos al laboratorio, abrimos un terminal de Linux y ejecutamos:

```bash
ssh hacker@venus.hackmyvm.eu -p 5000
```

Cuando nos solicite la contraseña, introducimos: `havefun!`

## 🎯 Misión 0x01 - Archivos Ocultos

> 📌 Objetivo:  La usuaria sophia ha guardado su contraseña en un fichero oculto en esta carpeta. Encuéntralo y logueate como sophia.

### ⚙️ Comandos Iniciales (Ejecutar Siempre)

Antes de comenzar cada misión, debemos ejecutar estos comandos:

```bash
cd
pwd
```

Esto nos sitúa en el **home** del usuario actual y verificamos nuestra ubicación. En Venus, los directorios home están en `/pwned` en lugar de `/home`.

### 📝 Instrucciones Generales del Laboratorio

Leemos el archivo `readme.txt` que contiene las reglas:

```bash
cat readme.txt
```

> [!warning] ⚠️ Reglas importantes:
>
>          Cada usuario tiene un archivo `mission.txt` en su home con la descripción de la misión
>          Solo tenemos permisos de escritura en `/tmp` y `/var/tmp`, por lo que cualquier descompresión o creación de archivos debe hacerse en estas carpetas

### 🔍 Resolución

Primero leemos la misión actual:

```bash
cat mission.txt
```

La pista menciona un **archivo oculto**. En Linux, los archivos ocultos comienzan con un punto (`.`). Listamos todos los archivos incluyendo los ocultos:

```bash
ls -la
```

Identificamos un archivo sospechoso que contiene la contraseña de sophia. Lo leemos:

```bash
cat .myhiddenpazz
```

Obtenemos la contraseña: `Y1o645M3mR84ejc`

Ahora cambiamos al usuario sophia usando el comando `su` (switch user):

```bash
su sophia
```

Introducimos la contraseña cuando nos la solicite.

Una vez dentro, nos situamos en su home y verificamos:

```bash
cd
pwd
```

> [!warning] 💡 Nota:  También podemos conectarnos directamente como sophia por SSH usando:
>
>
> ```bash
ssh sophia@venus.hackmyvm.eu -p 5000
```
>
>
>        Contraseña: `sophia:Y1o645M3mR84ejc`

## 🎯 Misión 0x02 - Búsqueda con Find

> 📌 Objetivo:  La usuaria angela ha guardado su password en un fichero pero no recuerda donde... solo recuerda que el fichero se llamaba `whereismypazz.txt`

### 🔍 Resolución

Leemos la misión:

```bash
cat mission.txt
```

Necesitamos buscar un archivo por nombre en todo el sistema. Usamos el comando `find`:

```bash
find / -type f -name "whereismypazz.txt" 2>/dev/null
```

#### 📚 Explicación del comando:

* `/` ?Busca desde la raíz del sistema

* `-type f` ?Solo busca archivos (no directorios)

* `-name "whereismypazz.txt"` ?Nombre exacto del archivo

* `2>/dev/null` ?Redirige los errores a /dev/null (los oculta)

El comando nos devuelve:

```bash
/usr/share/whereismypazz.txt
```

Leemos el archivo para obtener la contraseña:

```bash
cat /usr/share/whereismypazz.txt
```

Contraseña: `oh5p9gAABugHBje`

#### 🚀 Nivel Avanzado - Ejecutar Comandos con Find

Podemos hacer que `find` ejecute automáticamente `cat` cuando encuentre el archivo:

```bash
find / -type f -name "whereismypazz.txt" -exec cat {} \; 2>/dev/null
```

O mostrar el archivo encontrado Y su contenido:

```bash
find / -type f -name "whereismypazz.txt" -exec sh -c 'ls -la "$1"; cat "$1"' _ {} \; 2>/dev/null
```

Iniciamos sesión como angela:

```bash
su angela
```

Contraseña: `angela:oh5p9gAABugHBje`

Nos situamos en su home:

```bash
cd
pwd
```

## 🎯 Misión 0x03 - Línea Específica de un Archivo

> 📌 Objetivo:  La password de la usuaria emma está en la línea 4069 del fichero `findme.txt`

### 🔍 Resolución

Leemos la misión y verificamos el contenido del home:

```bash
cat mission.txt
ls -la
```

Vemos que existe el archivo `findme.txt`. Este archivo tiene 4618 líneas, por lo que no es práctico leerlo manualmente.

Usamos `cat` con numeración de líneas y filtramos con `grep`:

```bash
cat -n findme.txt | grep 4069
```

#### 📚 Explicación del comando:

* `cat -n` ?Muestra el contenido numerando cada línea

* `|` ?Pipe (tubería): pasa la salida al siguiente comando

* `grep 4069` ?Filtra solo las líneas que contienen "4069"

El resultado muestra:

```bash
2928 68540699866288e
4069 fIvltaGaq0OUH8O
```

La contraseña está en la línea 4069: `fIvltaGaq0OUH8O`

#### 🚀 Nivel Avanzado - Búsqueda Precisa con Regex

Para mostrar solo cuando la línea COMIENZA con 4069 (ten en cuenta los espacios al inicio):

```bash
cat -n findme.txt | grep -e "^\s*4069"
```

#### 📚 Explicación de la expresión regular:

* `^` ?Inicio de línea

* `\s*` ?Cero o más espacios en blanco

* `4069` ?El número buscado

Iniciamos sesión como emma y nos situamos en su home:

```bash
su emma
cd
pwd
```

Contraseña: `emma:fIvltaGaq0OUH8O`

## 🎯 Misión 0x04 - Archivo con Nombre Especial

> 📌 Objetivo:  La usuaria mia ha dejado su password en el fichero `-`

### 🔍 Resolución

Leemos la misión y listamos el contenido:

```bash
cat mission.txt
ls -la
```

Vemos que efectivamente existe un archivo llamado `-` (guion).

> [!warning] ⚠️ Problema:  En Linux, el guion `-` tiene un significado especial en los comandos (indica opciones). Si intentamos `cat -`, el sistema lo interpretará como una opción, no como un nombre de archivo.

Para leerlo correctamente, especificamos la ruta relativa con `./`:

```bash
cat ./-
```

#### 📚 Explicación:

* `./` ?Directorio actual

* `-` ?Nombre del archivo

* Al poner `./` delante, el sistema entiende que es un archivo, no una opción

Contraseña obtenida: `iKXIYg0pyEH2Hos`

Iniciamos sesión como mia:

```bash
su mia
```

Contraseña: `mia:iKXIYg0pyEH2Hos`

## 🎯 Misión 0x05 - Buscar Directorio

> 📌 Objetivo:  Parece que la usuaria camila ha dejado su password dentro de una carpeta llamada `hereiam`

### 🔍 Resolución

Usamos `find` para buscar un directorio por nombre:

```bash
find / -type d -name "hereiam" 2>/dev/null
```

#### 📚 Explicación del comando:

* `-type d` ?Busca directorios (no archivos)

* El resto es igual que en misiones anteriores

Encontramos:

```bash
/opt/hereiam
```

Listamos su contenido:

```bash
ls -la /opt/hereiam/
```

Leemos el archivo que contiene la contraseña:

```bash
cat /opt/hereiam/.here
```

Contraseña: `F67aDmCAAgOOaOc`

Iniciamos como camila:

```bash
su camila
```

Contraseña: `camila:F67aDmCAAgOOaOc`

## 🎯 Misión 0x06 - Buscar Archivo sin Conocer su Nombre

> 📌 Objetivo:  La usuaria luna ha dejado su password en algún fichero dentro de la carpeta `muack`

### 🔍 Resolución

Esta misión es más compleja porque no sabemos el nombre del archivo. Buscamos cualquier archivo dentro de la carpeta `muack` y mostramos su contenido automáticamente:

```bash
find muack/ -type f -exec cat {} \;
```

#### 📚 Explicación del comando:

* `muack/` ?Busca solo en este directorio y subdirectorios

* `-exec cat {} \;` ?Ejecuta `cat` en cada archivo encontrado

          `{}` ?Se reemplaza por el nombre del archivo encontrado

* `\;` ?Indica el final del comando a ejecutar

Contraseña encontrada: `j3vkuoKQwvbhkMc`

#### 🔄 Variante del Comando

Podemos usar `-ok` en lugar de `-exec` para que pida confirmación antes de ejecutar cada comando:

```bash
find muack/ -type f -ok cat {} \;
```

#### 🚀 Nivel Avanzado - Mostrar Nombre y Contenido

Para ver tanto el nombre del archivo como su contenido:

```bash
find muack/ -type f -exec sh -c 'ls -la "$1"; cat "$1"' _ {} \;
```

Resultado:

```bash
-rw-r----- 1 root camila 16 Apr 5 2024 muack/111/111/muack
j3vkuoKQwvbhkMc
```

Iniciamos como luna:

```bash
su luna
```

Contraseña: `luna:j3vkuoKQwvbhkMc`

## 🎯 Misión 0x07 - Buscar por Tamaño

> 📌 Objetivo:  La usuaria eleanor ha dejado su password en un fichero que ocupa 6969 bytes

### 🔍 Resolución

Usamos `find` con el parámetro `-size` para buscar por tamaño:

```bash
find / -type f -size 6969c 2>/dev/null
```

#### 📚 Explicación del comando:

* `-size 6969c` ?Busca archivos de exactamente 6969 bytes

          La `c` indica bytes (characters)

* Otras unidades: `k` (kilobytes), `M` (megabytes), `G` (gigabytes)

Encontramos:

```bash
/usr/share/moon.txt
```

Leemos su contenido:

```bash
cat /usr/share/moon.txt
```

Contraseña: `UNDchvln6Bmtu7b`

Iniciamos como eleanor:

```bash
su eleanor
```

Contraseña: `eleanor:UNDchvln6Bmtu7b`

## 🎯 Misión 0x08 - Buscar por Propietario

> 📌 Objetivo:  La usuaria victoria ha dejado su password en un fichero en el cual el propietario es el usuario `violin`

### 🔍 Resolución

Buscamos archivos cuyo propietario sea el usuario `violin`:

```bash
find / -type f -user violin 2>/dev/null
```

#### 📚 Explicación del comando:

* `-user violin` ?Busca archivos cuyo propietario sea "violin"

* También existe `-group` para buscar por grupo

Encontramos:

```bash
/usr/local/games/yo
```

Leemos el contenido:

```bash
cat /usr/local/games/yo
```

Contraseña: `pz8OqvJBFxH0cSj`

Iniciamos como victoria:

```bash
su victoria
```

Contraseña: `victoria:pz8OqvJBFxH0cSj`

## 🎯 Misión 0x09 - Descomprimir ZIP

> 📌 Objetivo:  La usuaria isla ha dejado su password en un fichero zip

### 🔍 Resolución

Listamos el contenido del home:

```bash
ls -la
```

Vemos un archivo `passw0rd.zip`. Como no tenemos permisos de escritura en nuestro home, debemos descomprimirlo en `/tmp`:

```bash
unzip passw0rd.zip -d /tmp/
```

#### 📚 Explicación del comando:

* `unzip` ?Comando para descomprimir archivos ZIP

* `-d /tmp/` ?Especifica el directorio de destino (destination)

> [!warning] ⚠️ Importante:  Si nos pregunta si queremos sobreescribir, respondemos `y` (yes)

Al extraer, nos muestra la ruta del archivo. Lo leemos:

```bash
cat /tmp/pwned/victoria/passw0rd.txt
```

Contraseña: `D3XTob0FUImsoBb`

Iniciamos como isla:

```bash
su isla
```

Contraseña: `isla:D3XTob0FUImsoBb`

## 🎯 Misión 0x10 - Filtrar con Grep (Inicio de Línea)

> 📌 Objetivo:  El password de la usuaria violet está en la línea que empieza por `a9HFX` (sin ser estos 5 caracteres parte de su password)

### 🔍 Resolución

Listamos el contenido del home y vemos un archivo llamado `passy` con muchas contraseñas.

Filtramos el contenido para mostrar solo las líneas que contienen `a9HFX`:

```bash
cat passy | grep a9HFX
```

Entre los resultados, identificamos la línea que COMIENZA con `a9HFX`:

```bash
a9HFXWKINVzNQLKLDVAc
```

La contraseña es la línea SIN los caracteres `a9HFX`: `WKINVzNQLKLDVAc`

#### 🚀 Nivel Avanzado - Filtro Preciso con Regex

Para mostrar solo las líneas que COMIENZAN con `a9HFX`, usamos el acento circunflejo `^`:

```bash
cat passy | egrep ^a9HFX
```

O usando grep con expresiones regulares:

```bash
cat passy | grep -e ^a9HFX
cat passy | grep -e "^a9HFX"
```

#### 📚 Explicación de expresiones regulares:

* `^` ?Ancla que indica inicio de línea

* Esto asegura que solo coincidan líneas que EMPIEZAN con el patrón especificado

Iniciamos como violet:

```bash
su violet
```

Contraseña: `violet:WKINVzNQLKLDVAc`

## 🎯 Misión 0x11 - Filtrar con Grep (Final de Línea)

> 📌 Objetivo:  El password de la usuaria lucy se encuentra en la línea que acaba por `0JuAZ` (sin ser estos últimos 5 caracteres parte de su password)

### 🔍 Resolución

Similar a la misión anterior. Vemos un archivo `end` en el home. Filtramos:

```bash
cat end | grep 0JuAZ
```

Resultado:

```bash
OCmMUjebG53giud0JuAZ
```

La contraseña de lucy es: `OCmMUjebG53giud`

#### 🚀 Nivel Avanzado - Final de Línea con $

Para asegurar que los caracteres están al FINAL de la línea, usamos el signo dólar `$`:

```bash
cat end | egrep 0JuAZ$
```

#### 📚 Explicación:

* `$` ?Ancla que indica final de línea

* Garantiza que el patrón aparece al final, no en medio

Iniciamos como lucy:

```bash
su lucy
```

Contraseña: `lucy:OCmMUjebG53giud`

## 🎯 Misión 0x12 - Filtrar con Grep (Inicio y Final)

> 📌 Objetivo:  El password de la usuaria elena está entre los caracteres `fu` y `ck`

### 🔍 Resolución

Vemos un archivo `file.yo` con muchas contraseñas. Esta vez necesitamos filtrar usando TANTO inicio como final de línea:

```bash
cat file.yo | grep ^fu.*ck$
```

Resultado:

```bash
fu4xZ5lIKYmfPLg9tck
```

#### 📚 Explicación de la expresión regular:

* `^fu` ?La línea comienza con "fu"

* `.*` ?Cualquier cantidad de caracteres (el punto indica cualquier carácter, el asterisco indica cero o más repeticiones)

* `ck$` ?La línea termina con "ck"

La contraseña de elena (sin fu al inicio ni ck al final): `4xZ5lIKYmfPLg9t`

Iniciamos como elena:

```bash
su elena
```

Contraseña: `elena:4xZ5lIKYmfPLg9t`

## 🎯 Misión 0x13 - Variables de Entorno

> 📌 Objetivo:  La password de alice está en una variable de entorno

### 🔍 Resolución

Las variables de entorno son valores que el sistema y las aplicaciones utilizan para configuración. Listamos todas las variables:

```bash
env
```

Entre las variables encontramos:

```bash
PASS=Cgecy2MY2MWbaqt
```

#### 🚀 Nivel Avanzado - Filtrado Específico

Para filtrar la salida y encontrar más fácilmente la contraseña:

```bash
env | grep -ie pass -ie elena
```

#### 📚 Explicación del comando:

* `-i` ?Ignora mayúsculas/minúsculas (case insensitive)

* `-e` ?Permite usar múltiples patrones de búsqueda

* Busca tanto "pass" como "elena" en cualquier combinación de mayúsculas/minúsculas

Resultado:

```bash
USER=elena
PASS=Cgecy2MY2MWbaqt
```

Iniciamos como alice:

```bash
su alice
```

Contraseña: `alice:Cgecy2MY2MWbaqt`

## 🎯 Misión 0x14 - Archivo /etc/passwd

> 📌 Objetivo:  El admin ha dejado la password de anna como comentario en el fichero passwd

### 🔍 Resolución

El archivo `/etc/passwd` es un archivo fundamental en Linux que almacena información básica sobre las cuentas de usuario del sistema.

Leemos su contenido y filtramos por el usuario alice:

```bash
cat /etc/passwd | grep alice
```

Resultado:

```bash
alice:x:1014:1014:w8NvY27qkpdePox:/pwned/alice:/bin/bash
```

#### 📚 Estructura de /etc/passwd:

Cada línea tiene 7 campos separados por dos puntos `:`

* `alice` ?Nombre de usuario

* `x` ?Contraseña (la x indica que está en /etc/shadow)

* `1014` ?UID (ID de usuario)

* `1014` ?GID (ID de grupo)

* `w8NvY27qkpdePox` ?Campo GECOS (comentario/descripción) ?AQUÍ ESTÁ LA CONTRASEÑA

* `/pwned/alice` ?Directorio home

* `/bin/bash` ?Shell predeterminado

La contraseña de anna está en el campo de comentarios: `w8NvY27qkpdePox`

Iniciamos como anna:

```bash
su anna
```

Contraseña: `anna:w8NvY27qkpdePox`

## 🎯 Misión 0x15 - Comando Sudo

> 📌 Objetivo:  Puede que sudo te ayude para ser natalia

### 🔍 Resolución

El comando `sudo` (superuser do) permite ejecutar comandos con privilegios de superusuario (root) o con los privilegios de otro usuario del sistema, de forma temporal y controlada.

Verificamos qué permisos tiene anna con sudo:

```bash
sudo -l
```

Resultado:

```bash
Matching Defaults entries for anna on venus:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User anna may run the following commands on venus:
    (natalia) NOPASSWD: /bin/bash
```

#### 📚 Explicación de la salida:

* `(natalia)` ?Anna puede ejecutar comandos como el usuario natalia

* `NOPASSWD` ?No necesita introducir contraseña

* `/bin/bash` ?Puede ejecutar una shell bash

Ejecutamos una bash como natalia:

```bash
sudo -u natalia bash
```

#### 📚 Explicación del comando:

* `-u natalia` ?Ejecuta como el usuario natalia

* `bash` ?Abre una shell bash

Ahora somos natalia. Nos situamos en su home y leemos su contraseña por si necesitamos acceder después:

```bash
cd
pwd
cat natalia_pass.txt
```

Contraseña: `natalia:NMuc4DkYKDsmZ5z`

## 🎯 Misión 0x16 - Decodificar Base64

> 📌 Objetivo:  El password de eva está encodeado en el fichero `base64.txt`

### 🔍 Resolución

Base64 es un sistema de codificación que convierte datos binarios en texto ASCII. Se usa comúnmente para transmitir datos.

Listamos el contenido del home y encontramos `base64.txt`. Para decodificarlo:

```bash
cat base64.txt | base64 -d
```

#### 📚 Explicación del comando:

* `base64` ?Comando para codificar/decodificar en Base64

* `-d` ?Modo decode (decodificar)

* Para codificar se usaría `-e` o sin opciones

Contraseña: `upsCA3UFu10fDAO`

#### 🚀 Nivel Avanzado - Decodificar con OpenSSL

También podemos usar OpenSSL, que incluye múltiples codificadores/decodificadores:

```bash
cat base64.txt | openssl base64 -d
```

Iniciamos como eva:

```bash
su eva
```

Contraseña: `eva:upsCA3UFu10fDAO`

## 🎯 Misión 0x17 - Buscar por Fecha de Modificación

> 📌 Objetivo:  La password de la usuaria clara se encuentra en un fichero modificado el 01 de Mayo de 1968

### 🔍 Resolución

Esta misión es compleja porque requiere entender cómo Linux maneja las fechas.

#### 📚 Conceptos importantes:

* Unix Epoch (época Unix) es el 1 de enero de 1970 a las 00:00:00 UTC

* Todos los tiempos en Unix se miden desde esta fecha

* El comando `find` con `-mtime` busca por días desde la modificación

#### 📚 Uso de -mtime:

* `-mtime 3` ?Archivos modificados exactamente hace 3 días

* `-mtime -3` ?Archivos modificados hace MENOS de 3 días (más recientes)

* `-mtime +3` ?Archivos modificados hace MÁS de 3 días (más antiguos)

Calculamos los días transcurridos desde 1970 hasta 2025:

```bash
echo $(( (2025 - 1970) * 365 ))
```

Resultado: `20075` días

Buscamos archivos modificados ANTES de 2025 (más de 20075 días):

```bash
find / -type f -mtime +20075 2>/dev/null
```

Encontramos:

```bash
/usr/lib/cmdo
```

Leemos su contenido:

```bash
cat /usr/lib/cmdo
```

Contraseña: `39YziWp5gSvgQN9`

Iniciamos como clara:

```bash
su clara
```

Contraseña: `clara:39YziWp5gSvgQN9`

## 🎯 Misión 0x18 - Crackear ZIP Protegido

> 📌 Objetivo:  La password de frida está en el zip protegido con password (rockyou.txt puede ayudarte)

### 🔍 Resolución

Vemos un archivo `protected.zip` en el home. Como está protegido con contraseña, necesitamos descargarlo a nuestra máquina Kali para crackearlo.

#### 📥 Paso 1: Descargar el archivo desde Venus a Kali

Abrimos un nuevo terminal en Kali (o dividimos la pantalla con Terminator) y ejecutamos:

```bash
scp -P 5000 clara@venus.hackmyvm.eu:~/protected.zip ./
```

#### 📚 Explicación del comando SCP:

* `scp` ?Secure Copy Protocol (copia segura por SSH)

* `-P 5000` ?Puerto SSH (nota: -P en mayúscula en scp)

* `clara@venus.hackmyvm.eu:~/protected.zip` ?Usuario@host:ruta_archivo

* `./` ?Directorio actual en Kali

Introducimos la contraseña de clara cuando nos la solicite.

#### 🔓 Paso 2: Extraer el hash del ZIP

Usamos `zip2john` para extraer el hash de la contraseña:

```bash
zip2john protected.zip > frida.txt
```

#### 🔨 Paso 3: Crackear el hash con John the Ripper

Usamos el diccionario rockyou.txt para crackear:

```bash
john frida.txt --format=pkzip --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt
```

Resultado:

```bash
pass123 (protected.zip/pwned/clara/protected.txt)
```

#### 🔄 Método Alternativo: fcrackzip

También podemos usar `fcrackzip` (puede necesitar instalación):

```bash
ln -s /usr/share/seclists/Passwords/Leaked-Databases/rockyou.txt rockyou.txt
fcrackzip -D -u protected.zip -p rockyou.txt
```

#### 📚 Explicación de fcrackzip:

* `-D` ?Modo diccionario

* `-u` ?Usa unzip para verificar contraseñas

* `-p rockyou.txt` ?Especifica el archivo de diccionario

Resultado:

```bash
PASSWORD FOUND!!!!: pw == pass123
```

#### 📂 Paso 4: Descomprimir el ZIP

Volvemos a Venus y descomprimimos (usamos un directorio aleatorio en /tmp para evitar conflictos):

```bash
unzip protected.zip -d /tmp/aghtd
```

Introducimos la contraseña: `pass123`

Leemos el contenido:

```bash
cat /tmp/aghtd/pwned/clara/protected.txt
```

Contraseña: `Ed4ErEUJEaMcXli`

Iniciamos como frida:

```bash
su frida
```

Contraseña: `frida:Ed4ErEUJEaMcXli`

## 🎯 Misión 0x19 - Encontrar Líneas Duplicadas

> 📌 Objetivo:  La password de eliza es el único string que se repite (sin estar ordenado) en `repeated.txt`

### 🔍 Resolución

Usamos el comando `uniq` que muestra valores únicos cuando se repiten en líneas consecutivas:

```bash
uniq -d repeated.txt
```

#### 📚 Explicación del comando:

* `uniq` ?Filtra líneas adyacentes repetidas

* `-d` ?Solo muestra líneas duplicadas (repeated)

* Otras opciones útiles:

          `-u` ?Solo muestra líneas únicas (no repetidas)

* `-c` ?Cuenta las repeticiones

Contraseña: `Fg6b6aoksceQqB9`

Iniciamos como eliza:

```bash
su eliza
```

Contraseña: `eliza:Fg6b6aoksceQqB9`

## 🎯 Misión 0x20 - Clave SSH Privada

> 📌 Objetivo:  La usuaria iris me ha dejado su key

### 🔍 Resolución

Investigamos en el home y encontramos un archivo oculto:

```bash
ls -la
```

Vemos: `.iris_key`

Miramos su contenido:

```bash
cat .iris_key
```

Es una clave privada SSH (RSA private key). Las claves SSH permiten autenticación sin contraseña.

#### 📚 Conceptos de SSH con claves:

* SSH puede usar autenticación por contraseña O por claves

* Existen dos claves: privada (secreta) y pública (se puede compartir)

* La clave privada debe mantenerse segura

* Se usa `-i` para especificar la clave privada al conectar

Usamos la clave privada para conectarnos como iris:

```bash
ssh iris@localhost -i .iris_key
```

#### 📚 Explicación del comando:

* `iris@localhost` ?Conectamos al usuario iris en la misma máquina

* `-i .iris_key` ?Especifica el archivo de identidad (clave privada)

Escribimos `yes` para validar la conexión.

Una vez dentro, leemos la contraseña de iris por si necesitamos acceder nuevamente:

```bash
cat irispass.txt
```

Contraseña: `iris:kYjyoLcnBZ9EJdz`

## 🎯 Misión 0x21 - Imagen en Base64

> 📌 Objetivo:  La usuaria eloise ha guardado su password de una forma particular

### 🔍 Resolución

Descargamos el archivo desde Kali:

```bash
scp -P 5000 iris@venus.hackmyvm.eu:~/eloise .
```

Si analizamos el contenido con CyberChef o desde terminal, descubrimos que es una imagen JPEG codificada en Base64.

Verificamos el tipo de archivo:

```bash
base64 -d eloise | file -
```

Decodificamos y guardamos como imagen:

```bash
cat eloise | openssl base64 -d > eloise.jpeg
```

#### 📚 Explicación del comando:

* `openssl base64 -d` ?Decodifica Base64 con OpenSSL

* `> eloise.jpeg` ?Redirige la salida a un archivo de imagen

Abrimos la imagen y vemos la contraseña: `yOUJlV0SHOnbSPm`

Iniciamos como eloise:

```bash
su eloise
```

Contraseña: `eloise:yOUJlV0SHOnbSPm`

## 🎯 Misión 0x22 - Hexadecimal

> 📌 Objetivo:  La usuaria lucia ha sido creativa en la forma de guardar su password

### 🔍 Resolución

Leemos el archivo del home:

```bash
cat hi
```

Vemos algo como:

```bash
00000000: 7576 4d77 4644 5172 5157 504d 6547 500a
```

Son valores hexadecimales. Podemos decodificarlos usando `xxd`:

```bash
xxd -r hi
```

#### 📚 Explicación del comando:

* `xxd` ?Herramienta para hacer volcados hexadecimales

* `-r` ?Modo reverso (reverse): convierte hexadecimal a texto

Resultado: `uvMwFDQrQWPMeGP`

También podemos usar CyberChef para decodificar hexadecimal.

Iniciamos como lucia:

```bash
su lucia
```

Contraseña: `lucia:uvMwFDQrQWPMeGP`

## 🎯 Misión 0x23 - Bucle While con Find

> 📌 Objetivo:  La usuaria isabel ha dejado su password en un fichero en la carpeta `/etc/xdg` pero no recuerda el nombre, sin embargo tiene `dict.txt` que puede ayudarle a recordar

### 🔍 Resolución

Tenemos un archivo `dict.txt` con posibles nombres de archivo. Usamos un bucle `while` para buscar cada nombre:

Primero probamos el bucle para ver los nombres:

```bash
while read line; do echo "$line"; done < ./dict.txt
```

Ahora buscamos cada nombre en `/etc/xdg`:

```bash
while read line; do find /etc/xdg/"$line" 2>/dev/null; done < ./dict.txt
```

#### 📚 Explicación del bucle while:

* `while read line` ?Lee cada línea del archivo

* `do ... done` ?Ejecuta comandos para cada línea

* `

Encontramos:

```bash
/etc/xdg/readme
```

Leemos su contenido:

```bash
cat /etc/xdg/readme
```

Contraseña: `H5ol8Z2mrRsorC0`

Iniciamos como isabel:

```bash
su isabel
```

Contraseña: `isabel:H5ol8Z2mrRsorC0`

## 🎯 Misión 0x24 - Líneas Únicas (No Repetidas)

> 📌 Objetivo:  La password de la usuaria freya es el único string que NO se repite en `repeated.txt`

### 🔍 Resolución

Esta es la operación contraria a la Misión 0x19. Ahora buscamos líneas únicas (que no se repiten):

```bash
cat different.txt | uniq -u
```

#### 📚 Explicación del comando:

* `uniq -u` ?Muestra solo líneas únicas (que aparecen una sola vez)

* Es lo opuesto a `-d` que mostraba duplicadas

Contraseña: `EEDyYFDwYsmYawj`

Iniciamos como freya:

```bash
su freya
```

Contraseña: `freya:EEDyYFDwYsmYawj`

## 🎯 Misión 0x25 - Tareas Cron Automáticas

> 📌 Objetivo:  La usuaria alexa pone su password en un fichero .txt en la carpeta `/free` cada minuto y luego lo borra

### 🔍 Resolución

Verificamos si tenemos tareas cron configuradas:

```bash
crontab -l
```

#### 📚 ¿Qué es Cron?

Cron es un planificador de tareas en Linux que ejecuta comandos automáticamente en horarios específicos.

#### 💡 Estrategia:

Como el archivo aparece y desaparece cada minuto, crearemos nuestra propia tarea cron que copie el contenido de `/free` a otro lugar antes de que se borre.

Creamos un directorio en /tmp:

```cmd
mkdir /tmp/ifct
```

Editamos nuestro crontab:

```bash
crontab -e
```

Añadimos esta línea al final del archivo:

```bash
* * * * * cp /free/* /tmp/ifct
```

#### 📚 Explicación del formato cron:

* Los 5 asteriscos significan:

          Minuto (0-59)

* Hora (0-23)

* Día del mes (1-31)

* Mes (1-12)

* Día de la semana (0-7, donde 0 y 7 son domingo)

* `* * * * *` ?Ejecutar cada minuto

* `cp /free/* /tmp/ifct` ?Copia todo de /free a /tmp/ifct

Guardamos (Ctrl+O, Enter, Ctrl+X en nano o :wq en vim).

Esperamos aproximadamente un minuto y listamos:

```bash
ls -l /tmp/ifct/
```

Vemos archivos copiados:

```bash
-rw-r--r-- 1 freya freya 16 Apr 3 11:50 beer.txt
-rw-r--r-- 1 freya freya  0 Apr 3 11:50 test
```

Leemos el contenido:

```bash
cat /tmp/ifct/beer.txt
```

Contraseña: `mxq9O3MSxxX9Q3S`

Iniciamos como alexa:

```bash
su alexa
```

Contraseña: `alexa:mxq9O3MSxxX9Q3S`

## 🎯 Misión 0x26 - Servidor HTTP Local

> 📌 Objetivo:  El password de la usuaria ariel está online! (HTTP)

### 🔍 Resolución

Hay un servidor web corriendo localmente en Venus. Accedemos con `curl`:

```bash
curl http://127.0.0.1
```

#### 📚 Explicación de curl:

* `curl` ?Herramienta para transferir datos desde/hacia servidores

* `http://127.0.0.1` ?Dirección localhost (la propia máquina)

* También se puede usar `http://localhost`

Contraseña: `33EtHoz9a0w2Yqo`

Iniciamos como ariel:

```bash
su ariel
```

Contraseña: `ariel:33EtHoz9a0w2Yqo`

## 🎯 Misión 0x27 - Archivos Temporales de Vim (.swp)

> 📌 Objetivo:  Parece ser que a ariel no le dio tiempo a guardar la password de lola... ¡menos mal que hay un temporal!

### 🔍 Resolución

Listamos archivos ocultos:

```bash
ls -la
```

Vemos:

```bash
-rw-r----- 1 root ariel 12288 Apr 7 2022 .goas.swp
```

#### 📚 ¿Qué es un archivo .swp?

Los archivos `.swp` son archivos de intercambio (swap) creados por el editor Vim. Vim los usa para guardar cambios temporalmente mientras editas. Si Vim se cierra inesperadamente, el archivo .swp puede recuperarse.

Recuperamos el contenido del archivo swap con Vim:

```bash
vi -r .goas.swp
```

#### 📚 Comandos de Vim:

* `vi -r` ?Abre Vim en modo recuperación

* Para guardar el contenido: `:w /tmp/dict.txt`

* Para salir: `:q`

Guardamos el contenido recuperado:

```bash
:w /tmp/dict.txt
:q
```

Editamos el archivo para limpiar las primeras líneas y dejar solo el diccionario de contraseñas:

```bash
nano /tmp/dict.txt
```

Copiamos el diccionario limpio a nuestra Kali y hacemos fuerza bruta SSH:

```bash
hydra -l lola -P ./pass.txt ssh://venus.hackmyvm.eu:5000
```

O con ncrack:

```bash
ncrack -v -u lola -P ./pass.txt ssh://venus.hackmyvm.eu:5000
```

#### 📚 Explicación de herramientas de fuerza bruta:

* `-l lola` ?Login/usuario a probar

* `-P ./pass.txt` ?Archivo de contraseñas (diccionario)

* `-v` ?Modo verbose (muestra progreso)

Resultado:

```bash
[5000][ssh] host: venus.hackmyvm.eu login: lola password: d3LieOzRGX5wud6
```

Iniciamos como lola:

```bash
su lola
```

Contraseña: `lola:d3LieOzRGX5wud6`

## 🎯 Misión 0x28 - Comparar Listas y Curl

> 📌 Objetivo:  La usuaria celeste ha dejado un listado de nombres de posibles páginas .html donde encontrar su password

### 🔍 Resolución

Buscamos archivos HTML en el servidor web local y comparamos con el listado que nos dieron:

Buscamos archivos HTML y extraemos solo los nombres:

```bash
find /var/www -wholename "*/html/*.html" | cut -c 15-50
```

#### 📚 Explicación del comando:

* `-wholename` ?Coincide con la ruta completa del archivo

* `*/html/*.html` ?Patrón que busca archivos .html en carpetas llamadas html

* `| cut -c 15-50` ?Corta caracteres del 15 al 50 de cada línea

Probamos acceder a las páginas HTML encontradas. Una solución automatizada sería:

```bash
find /var/www -wholename "*/html/*.html" | cut -c 15-50 > /tmp/celeste1.txt
while read line; do curl http://127.0.0.1/$line; done < /tmp/celeste1.txt
```

O comparando con el archivo `pages.txt` proporcionado:

```bash
find /var/www -wholename "*/html/*.html" | cut -c 15-50 | awk -F "." '{print $1}' > /tmp/celeste1.txt
cat /tmp/celeste1.txt ./pages.txt > /tmp/celeste3.txt
sort /tmp/celeste3.txt | uniq -d > /tmp/celeste4.txt
while read line; do curl http://127.0.0.1/$line; done < /tmp/celeste4.txt
```

Accediendo directamente a la página correcta:

```bash
curl http://127.0.0.1/cebolla.html
```

Contraseña: `VLSNMTKwSV2o8Tn`

Iniciamos como celeste:

```bash
su celeste
```

Contraseña: `celeste:VLSNMTKwSV2o8Tn`

## 🎯 Misión 0x29 - Base de Datos MySQL

> 📌 Objetivo:  La usuaria celeste tiene acceso al mysql, pero ¿para qué?

### 🔍 Resolución

Celeste tiene acceso a MySQL. Conectamos:

```bash
mysql -u celeste -p
```

Introducimos la contraseña de celeste cuando nos la solicite.

#### 📚 Comandos básicos de MySQL:

Listar bases de datos:

```bash
show databases;
```

Usar una base de datos:

```bash
use venus;
```

Listar tablas:

```bash
show tables;
```

Ver contenido de una tabla:

```bash
select * from people;
```

En la tabla `people` encontramos usuarios y contraseñas. Buscamos a nina:

```bash
| 74 | nina | ixpeqdWuvC5N9kG |
```

Verificamos que nina existe en el sistema:

```bash
cat /etc/passwd | grep nina
```

Resultado:

```bash
nina:x:1030:1030::/pwned/nina:/bin/bash
```

Iniciamos como nina:

```bash
su nina
```

Contraseña: `nina:ixpeqdWuvC5N9kG`

## 🎯 Misión 0x30 - Métodos HTTP (PUT)

> 📌 Objetivo:  La usuaria kira esconde algo en `http://localhost/method.php`

### 🔍 Resolución

Probamos acceder normalmente:

```bash
curl http://localhost/method.php
```

Respuesta:

```bash
I dont like this method!
```

#### 📚 Métodos HTTP:

HTTP define varios métodos de petición:

* `GET` ?Obtener datos (por defecto en curl)

* `POST` ?Enviar datos

* `PUT` ?Actualizar/crear recursos

* `DELETE` ?Eliminar recursos

* Y otros menos comunes

Probamos con diferentes métodos. Con `-X` especificamos el método:

Método POST:

```bash
curl -X POST http://localhost/method.php
```

Respuesta: `I dont like this method!`

Método PUT:

```bash
curl -X PUT http://localhost/method.php
```

¡Éxito! Contraseña: `tPlqxSKuT4eP3yr`

Iniciamos como kira:

```bash
su kira
```

Contraseña: `kira:tPlqxSKuT4eP3yr`

## 🎯 Misión 0x31 - User-Agent HTTP

> 📌 Objetivo:  La usuaria veronica visita mucho `http://localhost/waiting.php`

### 🔍 Resolución

La página espera un User-Agent específico. El User-Agent es una cabecera HTTP que identifica el navegador o cliente que hace la petición.

Usamos `-A` para especificar un User-Agent personalizado:

```bash
curl -A PARADISE http://localhost/waiting.php
```

#### 📚 Explicación del comando:

* `-A PARADISE` ?Establece el User-Agent como "PARADISE"

* También se puede usar `-H "User-Agent: PARADISE"`

Contraseña: `QTOel6BodTx2cwX`

Iniciamos como veronica:

```bash
su veronica
```

Contraseña: `veronica:QTOel6BodTx2cwX`

## 🎯 Misión 0x32 - Alias de Bash

> 📌 Objetivo:  La usuaria veronica usa mucho la password de lana, así que ha creado un alias

### 🔍 Resolución

Los alias son atajos de comandos en Linux. Listamos los alias configurados:

```bash
alias
```

#### 📚 ¿Qué es un Alias?

Un alias es un nombre corto para un comando más largo. Por ejemplo:

* `alias ll='ls -la'` ?Crear alias ll para listar detallado

* `alias update='sudo apt update && sudo apt upgrade'` ?Alias para actualizar

Encontramos:

```bash
alias lanapass='UWbc0zNEVVops1v'
```

La contraseña está definida como alias: `UWbc0zNEVVops1v`

Iniciamos como lana:

```bash
su lana
```

Contraseña: `lana:UWbc0zNEVVops1v`

## 🎯 Misión 0x33 - Descomprimir TAR.GZ

> 📌 Objetivo:  A la usuaria noa le gusta comprimir sus cosas

### 🔍 Resolución

Vemos un archivo `zip.gz`. Verificamos su tipo real:

```bash
file zip.gz
```

Resultado:

```bash
zip.gz: POSIX tar archive (GNU)
```

A pesar del nombre, es un archivo TAR, no GZ. Creamos un directorio temporal y extraemos:

```cmd
mkdir /tmp/ifct0109
tar xvf zip.gz -C /tmp/ifct0109
```

#### 📚 Explicación del comando tar:

* `tar` ?Herramienta para archivar archivos

* `x` ?Extraer (extract)

* `v` ?Verbose (mostrar progreso)

* `f` ?File (especifica el archivo)

* `-C /tmp/ifct0109` ?Cambiar al directorio de destino antes de extraer

#### 📚 Tipos comunes de compresión:

* `.tar` ?Solo archiva, no comprime

* `.tar.gz` o `.tgz` ?TAR + GZIP (añadir opción `z`)

* `.tar.bz2` ?TAR + BZIP2 (añadir opción `j`)

* `.zip` ?Formato ZIP (usar `unzip`)

Salida:

```bash
pwned/lana/zip
```

Leemos el contenido:

```bash
cat /tmp/ifct0109/pwned/lana/zip
```

Contraseña: `9WWOPoeJrq6ncvJ`

Iniciamos como noa:

```bash
su noa
```

Contraseña: `noa:9WWOPoeJrq6ncvJ`

## 🎯 Misión 0x34 - Comando Strings

> 📌 Objetivo:  La password de maia está rodeada de basura

### 🔍 Resolución

Hay un archivo binario con datos. Usamos `strings` para extraer texto legible:

```bash
strings trash
```

#### 📚 ¿Qué hace strings?

El comando `strings` busca secuencias de caracteres imprimibles en archivos binarios. Es muy útil para:

* Analizar ejecutables

* Buscar contraseñas o URLs ocultas

* Forense digital

* Extraer texto de archivos corruptos

Resultado:

```bash
\nh1hnDPHpydEjoEN
```

La contraseña válida (sin el \n al inicio): `h1hnDPHpydEjoEN`

Iniciamos como maia:

```bash
su maia
```

Contraseña: `maia:h1hnDPHpydEjoEN`

## 🎯 Misión 0x35 - Fuerza Bruta con Crunch

> 📌 Objetivo:  La usuaria gloria ha olvidado los 2 últimos caracteres de su password... Solo recuerdan que eran 2 letras minúsculas

### 🔍 Resolución

Leemos el archivo `forget`:

```bash
cat forget
```

Contenido:

```bash
v7xUVE2e5bjUc??
```

Conocemos toda la contraseña excepto los 2 últimos caracteres (letras minúsculas).

#### 💡 Estrategia

Usaremos `crunch` en Kali para generar un diccionario con todas las combinaciones posibles, y luego haremos fuerza bruta SSH.

Desde Kali, generamos el diccionario:

```bash
crunch 15 15 -t v7xUVE2e5bjUc@@ -o gloria.txt
```

#### 📚 Explicación de crunch:

* `15 15` ?Longitud mínima y máxima (15 caracteres)

* `-t v7xUVE2e5bjUc@@` ?Patrón (template)

          `@@` ?Será reemplazado por letras minúsculas (a-z)

* Otros patrones: `%` números, `,` mayúsculas, `^` símbolos

* `-o gloria.txt` ?Archivo de salida

Esto generará 676 combinaciones (26 × 26 = 676).

Ahora hacemos fuerza bruta SSH:

```bash
ncrack -v -u gloria -P ./gloria.txt ssh://venus.hackmyvm.eu:5000
```

Resultado:

```bash
Discovered credentials for ssh on 185.233.104.77 5000/tcp:
185.233.104.77 5000/tcp ssh: 'gloria' 'v7xUVE2e5bjUcxw'
```

Contraseña encontrada: `v7xUVE2e5bjUcxw`

Iniciamos como gloria:

```bash
su gloria
```

Contraseña: `gloria:v7xUVE2e5bjUcxw`

## 🎯 Misión 0x36 - Código QR ASCII

> 📌 Objetivo:  A la usuaria alora le gustan los dibujos, por eso ha guardado su password como...

### 🔍 Resolución

Verificamos el tipo de archivo:

```bash
file image
```

Resultado:

```bash
image: ASCII text
```

Leemos el contenido:

```bash
cat image
```

Vemos un código QR dibujado con caracteres ASCII. Para visualizarlo mejor, reemplazamos `#` por bloques sólidos:

```bash
cat image | tr "#" "\030"
```

#### 📚 Explicación del comando:

* `tr` ?Comando translate (traducir/reemplazar caracteres)

* `"#"` ?Carácter a reemplazar

* `"\030"` ?Código octal para un carácter de bloque visual

Podemos escanear el QR con el móvil o usar herramientas online. Contenido del QR:

```bash
mhrTFCoxGoqUxtw
```

Iniciamos como alora:

```bash
su alora
```

Contraseña: `alora:mhrTFCoxGoqUxtw`

## 🎯 Misión 0x37 - Archivo ISO con Strings

> 📌 Objetivo:  La usuaria julie ha creado una iso con su password

### 🔍 Resolución

Hay un archivo `music.iso`. Los archivos ISO son imágenes de disco (como CDs o DVDs). Usamos `strings` para extraer texto:

```bash
strings music.iso
```

Entre el contenido encontramos la contraseña: `sjDf4i2MSNgSvOv`

Iniciamos como julie:

```bash
su julie
```

Contraseña: `julie:sjDf4i2MSNgSvOv`

## 🎯 Misión 0x38 - Comparar Archivos con Diff

> 📌 Objetivo:  La usuaria irene cree que en la diferencia está lo bonito

### 🔍 Resolución

Hay dos archivos casi idénticos. Usamos `diff` para encontrar las diferencias:

```bash
diff 1.txt 2.txt
```

#### 📚 ¿Qué hace diff?

El comando `diff` compara archivos línea por línea y muestra las diferencias. Es muy útil para:

* Comparar versiones de código

* Ver cambios en configuraciones

* Encontrar diferencias en logs

Salida:

```bash
174c174
< 8VeRLEFkBpe2DSD
---
> aNHRdohjOiNizlU
```

#### 📚 Explicación de la salida:

* `174c174` ?Línea 174 cambió

* `` ?Contenido del segundo archivo

La contraseña es la del primer archivo: `8VeRLEFkBpe2DSD`

Iniciamos como irene:

```bash
su irene
```

Contraseña: `irene:8VeRLEFkBpe2DSD`

## 🎯 Misión 0x39 - Desencriptar con RSA

> 📌 Objetivo:  La usuaria adela le ha dejado prestada su password a irene

### 🔍 Resolución

Tenemos una clave privada RSA (`id_rsa.pem`) y un archivo encriptado (`pass.enc`).

Verificamos las opciones de OpenSSL para RSA:

```bash
openssl rsautl --help
```

Opciones importantes:

* `-decrypt` ?Desencriptar con clave privada

* `-inkey` ?Archivo de clave de entrada

* `-in` ?Archivo de entrada a desencriptar

Desencriptamos:

```bash
openssl rsautl -decrypt -inkey id_rsa.pem -in pass.enc
```

#### 📚 Explicación de RSA:

RSA es un sistema de criptografía asimétrica:

* Usa un par de claves: pública y privada

* Lo encriptado con la clave pública solo se puede desencriptar con la privada

* Lo firmado con la clave privada se puede verificar con la pública

Contraseña: `nbhlQyKuaXGojHx`

Iniciamos como adela:

```bash
su adela
```

Contraseña: `adela:nbhlQyKuaXGojHx`

## 🎯 Misión 0x40 - Código Morse

> 📌 Objetivo:  La usuaria sky ha guardado su password en algo que puede ser escuchado

### 🔍 Resolución

Leemos el archivo:

```bash
cat wtf
```

Contenido:

```bash
.--. .- .--. .- .--. .- .-. .- -.. .. ... .
```

Es código Morse. Podemos decodificarlo usando CyberChef u otras herramientas online.

#### 📚 Código Morse:

* Sistema de comunicación que usa puntos (.) y rayas (-)

* Cada letra tiene una combinación única

* Los espacios separan letras

Resultado decodificado: `PAPAPARADISE`

La contraseña en minúsculas: `papaparadise`

Iniciamos como sky:

```bash
su sky
```

Contraseña: `sky:papaparadise`

## 🎯 Misión 0x41 - Header HTTP Personalizado

> 📌 Objetivo:  La usuaria sarah utiliza header para `http://localhost/key.php`

### 🔍 Resolución

Probamos acceder normalmente:

```bash
curl http://localhost/key.php
```

Respuesta:

```bash
Key header is true?
```

Necesitamos enviar una cabecera HTTP personalizada llamada "key" con valor "true":

```bash
curl http://localhost/key.php -H "key: true"
```

#### 📚 Explicación del comando:

* `-H "key: true"` ?Añade una cabecera HTTP personalizada

* Formato: `-H "Nombre: Valor"`

* Se pueden añadir múltiples cabeceras con múltiples `-H`

Contraseña: `LWOHeRgmIxg7fuS`

Iniciamos como sarah:

```bash
su sarah
```

Contraseña: `sarah:LWOHeRgmIxg7fuS`

## 🎯 Misión 0x42 - Archivo con Nombre de Puntos

> 📌 Objetivo:  La password de mercy está oculta en este directorio

### 🔍 Resolución

Listamos archivos ocultos cuidadosamente:

```bash
ls -la
```

Encontramos un archivo llamado `...` (tres puntos). Recordemos que:

* `.` ?Directorio actual

* `..` ?Directorio padre

* `...` ?¡Es un nombre de archivo válido!

Leemos el archivo:

```bash
cat ...
```

Contraseña: `ym5yyXZ163uIS8L`

Iniciamos como mercy:

```bash
su mercy
```

Contraseña: `mercy:ym5yyXZ163uIS8L`

## 🎯 Misión 0x43 - Historial de Comandos

> 📌 Objetivo:  La usuaria mercy siempre se equivoca con la password de paula

### 🔍 Resolución

Revisamos el historial de comandos:

```bash
history
```

#### 📚 ¿Qué es history?

El comando `history` muestra los comandos ejecutados anteriormente en bash. Es útil para:

* Recordar comandos complejos

* Auditoría de seguridad

* Encontrar errores (o contraseñas accidentalmente escritas)

Entre los comandos ejecutados encontramos:

```bash
9 dlHZ6cvX6cLuL8p
```

Contraseña: `dlHZ6cvX6cLuL8p`

> [!warning] ⚠️ Buena práctica de seguridad:  Nunca escribas contraseñas directamente en la terminal, ya que quedan guardadas en el historial. Usa siempre prompts seguros o archivos temporales.

Iniciamos como paula:

```bash
su paula
```

Contraseña: `paula:dlHZ6cvX6cLuL8p`

## 🎯 Misión 0x44 - Buscar por Grupo

> 📌 Objetivo:  La usuaria karla confía en mí, es parte de mi grupo de amigos

### 🔍 Resolución

Verificamos a qué grupos pertenece paula:

```bash
groups paula
```

Resultado:

```bash
paula : paula hidden
```

Paula pertenece a un grupo llamado `hidden`. Buscamos archivos que pertenezcan a este grupo:

```bash
find / -group hidden -type f 2>/dev/null
```

#### 📚 Explicación del comando:

* `-group hidden` ?Busca archivos cuyo grupo sea "hidden"

* Similar a `-user` que usamos en la Misión 0x08

Encontramos:

```bash
/usr/src/.karl-a
```

Leemos el contenido:

```bash
cat /usr/src/.karl-a
```

Contraseña: `gYAmvWY3I7yDKRf`

Iniciamos como karla:

```bash
su karla
```

Contraseña: `karla:gYAmvWY3I7yDKRf`

## 🎯 Misión 0x45 - Metadatos EXIF

> 📌 Objetivo:  La usuaria denise ha guardado su password en la imagen

### 🔍 Resolución

Hay una imagen en el home. Las imágenes contienen metadatos EXIF con información como cámara, fecha, ubicación, etc.

Usamos `exiftool` para ver los metadatos:

```bash
exiftool yuju.jpg
```

#### 📚 ¿Qué son los metadatos EXIF?

EXIF (Exchangeable Image File Format) son datos incrustados en imágenes que contienen:

* Modelo de cámara

* Configuración de la foto (ISO, apertura, velocidad)

* Fecha y hora

* Coordenadas GPS

* ¡Y campos personalizados!

Entre los metadatos encontramos:

```bash
About : pFg92DpGucMWccA
```

Contraseña: `pFg92DpGucMWccA`

Iniciamos como denise:

```bash
su denise
```

Contraseña: `denise:pFg92DpGucMWccA`

## 🎯 Misión 0x46 - Comando Doas

> 📌 Objetivo:  La usuaria zora no deja de gritar doas!

### 🔍 Resolución

`doas` es un comando similar a `sudo`, diseñado para ser más simple y seguro. Permite ejecutar comandos como otro usuario.

Ejecutamos una bash como zora:

```bash
doas -u zora bash
```

#### 📚 Explicación de doas:

* `doas` ?Comando alternativo a sudo (usado en OpenBSD y algunos Linux)

* `-u zora` ?Ejecuta como el usuario zora

* `bash` ?Abre una shell bash

Nos pedirá la contraseña de denise. Una vez dentro, leemos la contraseña de zora:

```bash
cd
cat zora_pass.txt
```

Contraseña: `zora:BWm1R3jCcb53riO`

## 🎯 Misión 0x47 - Virtual Host HTTP

> 📌 Objetivo:  La usuaria belen ha dejado su password en `venus.hmv`

### 🔍 Resolución

El servidor web tiene virtual hosts (sitios virtuales). Accedemos especificando el host:

```bash
curl -H 'HOST: venus.hmv' http://localhost
```

#### 📚 ¿Qué es un Virtual Host?

Los virtual hosts permiten alojar múltiples sitios web en un mismo servidor. El servidor decide qué contenido mostrar según la cabecera HOST enviada.

#### 📚 Explicación del comando:

* `-H 'HOST: venus.hmv'` ?Establece la cabecera HOST

* El servidor responde con el contenido específico para venus.hmv

Contraseña: `2jA0E8bQ4WrGwWZ`

Iniciamos como belen:

```bash
su belen
```

Contraseña: `belen:2jA0E8bQ4WrGwWZ`

## 🎯 Misión 0x48 - Crackear Hash con John

> 📌 Objetivo:  Parece que belen ha robado el password de la usuaria leona...

### 🔍 Resolución

Tenemos un archivo con un hash de contraseña. Descargamos a Kali y crackeamos con John the Ripper:

```bash
scp -P 5000 belen@venus.hackmyvm.eu:~/leona.txt .
```

Crackeamos con John:

```bash
john leona.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

#### 📚 ¿Qué es un Hash?

Un hash es una función criptográfica de un solo sentido:

* Convierte texto en una cadena de longitud fija

* No se puede "revertir" para obtener el original

* El cracking consiste en probar muchas contraseñas hasta encontrar una coincidencia

Vemos el resultado en el archivo de John:

```bash
cat ~/.john/john.pot
```

Resultado:

```bash
$1$leona$lhWp56YnWAMz6z32Bw53L0:freedom
```

La contraseña es: `freedom`

Iniciamos como leona:

```bash
su leona
```

Contraseña: `leona:freedom`

## 🎯 Misión 0x49 - Configuración DNS (BIND)

> 📌 Objetivo:  La usuaria ava juega mucho con el DNS de venus.hmv últimamente...

### 🔍 Resolución

BIND es el servidor DNS más usado en Linux. Su configuración está en `/etc/bind`.

Listamos los archivos de configuración:

```bash
ls -la /etc/bind
```

Leemos la zona DNS de venus.hmv:

```bash
cat /etc/bind/db.venus.hmv
```

#### 📚 ¿Qué es DNS?

DNS (Domain Name System) traduce nombres de dominio (como google.com) en direcciones IP. BIND es el software que gestiona estas traducciones.

Dentro del archivo encontramos la contraseña: `oCXBeeEeYFX34NU`

Iniciamos como ava:

```bash
su ava
```

Contraseña: `ava:oCXBeeEeYFX34NU`

## 🎯 Misión 0x50 - Contraseña Oculta (Morse)

> 📌 Objetivo:  El password de maria está en algún lugar...

### 🔍 Resolución

Esta es una misión especial. La pista está en una misión anterior.

Recordamos la Misión 0x40 donde descubrimos la contraseña de sky en morse:

La pista dice "somewhere in the sky" (en algún lugar del cielo). Probamos con la contraseña de sky en morse:

```bash
su maria
```

Password: `.--. .- .--. .- .--. .- .-. .- -.. .. ... .`

¡Funciona! Leemos la misión final:

```bash
cat mission.txt
```

> 🎉 ¡FELICIDADES!
>    Has completado el laboratorio VENUS con las 50 flags.

## 🏆 Conclusión

¡Enhorabuena por completar todas las misiones del laboratorio VENUS! Has practicado múltiples habilidades esenciales en Linux y hacking ético:

* 🔍 Búsqueda de archivos con `find`

* 📝 Filtrado de texto con `grep`, `awk`, `sed`

* 🗜?Compresión y descompresión de archivos

* 🔐 Criptografía y decodificación (Base64, RSA, morse)

* 🌐 Protocolos HTTP y peticiones web

* 🔨 Fuerza bruta y cracking de contraseñas

* ⚙️ Tareas automatizadas con cron

* 🗄?Bases de datos MySQL

* 🔑 Autenticación SSH con claves

* 🛠?Permisos y privilegios en Linux

Este laboratorio es una excelente base para seguir practicando en otros CTFs y mejorar tus habilidades en ciberseguridad.

---

## 🔗 Enlaces y Referencias Cruzadas
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice del Módulo Linux]]
* 🔥 [[02 - MODULOS/Prácticas (PoCs)/Linux/12 - Laboratorio HADES (HackMyVM)|Laboratorio HADES (HackMyVM)]]
* 🏆 [[03 - EXTRA/03 - Retos de Ciberseguridad|12 Retos Técnicos de Ciberseguridad]]

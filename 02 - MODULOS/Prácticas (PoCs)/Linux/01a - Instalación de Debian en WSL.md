---
title: "Instalación Debian en WSL - Curso Linux Pentesting"
aliases:
  - Instalación Debian en WSL - Curso Linux Pentesting
tags:
  - ifct0109/pocs
  - linux
  - wsl
  - debian
---

# Instalación de Debian en WSL

Windows Subsystem for Linux - Tu primer entorno Linux

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

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

🔗 [https://aka.ms/wsl2kernel](https://aka.ms/wsl2kernel)

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

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

Instalación de Debian en WSL - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v1.0

---

## 🔗 Navegación
* 🐧 [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|Volver al Portal del Curso de Linux]]
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice General de Linux]]

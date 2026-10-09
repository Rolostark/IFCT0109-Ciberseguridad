---
title: "Linux — Comandos Esenciales (+100 Comandos)"
aliases:
  - Linux Commands
  - Comandos Esenciales Linux
  - Linux CheatSheet
  - Guía Rápida Linux
tags:
  - ifct0109
  - linux
  - cheatsheet
  - comandos
  - terminal
  - bash
  - sysadmin
---

> [[03 - EXTRA/01 - Enlaces favoritos|← Volver a Enlaces Favoritos]] | [[02 - MODULOS/05 - Prácticas (PoCs)|Ir a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Módulo Linux]]

# 🐧 Linux — CheatSheet de Comandos Esenciales (+100 Comandos)

> [!abstract] Documento Oficial de Referencia
> Esta guía transcribe, organiza y enriquece al 100% el documento oficial del curso:
> 📄 **Archivo PDF Original:** [📥 Descargar linuxcommands.pdf](../../archivos_descargados/documentos/linuxcommands.pdf)  
> 👨‍💻 **Autor Original:** Waleed Mousa (*Essential Linux Commands [+100]*)
> 
> 🔗 **Laboratorios Prácticos Relacionados en el Vault:**
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux - Introducción y Entorno|Linux 01 — Introducción y Entorno (WSL y Docker)]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/02 - Curso de Linux - Estructura de Archivos (FHS)|Linux 02 — Estructura de Archivos (FHS)]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/03 - Curso de Linux - Comandos Esenciales de Sistema|Linux 03 — Comandos Esenciales de Sistema]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/04 - Curso de Linux - Gestión de Grupos y Sudoers|Linux 04 — Gestión de Grupos y Sudoers]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/05 - Curso de Linux - Gestión de Usuarios y Autenticación|Linux 05 — Gestión de Usuarios y Autenticación]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/06 - Curso de Linux - Permisos, SUID, SGID y Sticky Bit|Linux 06 — Permisos, SUID y Escalada]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/07 - Filtrado de Información en Linux|Linux 07 — Filtrado Avanzado (Grep, Sed, Awk)]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/08 - Curso de Bash Scripting en Linux|Linux 08 — Curso de Bash Scripting]]
> * [[03 - EXTRA/Documentos/Material Preparación LPIC|Material Preparación Certificación LPIC-1]]

---

## 📑 Índice Temático Rápido

* [📁 1. Operaciones con Archivos](#1-operaciones-con-archivos)
* [📂 2. Operaciones con Directorios](#2-operaciones-con-directorios)
* [⚙️ 3. Gestión de Procesos y Redirecciones](#3-gesti%C3%B3n-de-procesos-y-redirecciones)
* [🔐 4. Permisos y Propietarios de Archivos](#4-permisos-y-propietarios-de-archivos)
* [🌐 5. Redes y Conectividad](#5-redes-y-conectividad)
* [🗜️ 6. Compresión y Empaquetado](#6-compresi%C3%B3n-y-empaquetado)
* [📝 7. Procesamiento y Filtrado de Texto](#7-procesamiento-y-filtrado-de-texto)
* [💾 8. Uso de Disco y Almacenamiento](#8-uso-de-disco-y-almacenamiento)
* [ℹ️ 9. Información del Sistema y Hardware](#9-informaci%C3%B3n-del-sistema-y-hardware)
* [📦 10. Gestión de Paquetes (APT)](#10-gesti%C3%B3n-de-paquetes-apt)
* [⚡ 11. Operadores de Ejecución y Control de Flujo](#11-operadores-de-ejecuci%C3%B3n-y-control-de-flujo)
* [🐙 12. Control de Versiones con Git](#12-control-de-versiones-con-git)
* [🌲 13. Variables de Entorno y Alias](#13-variables-de-entorno-y-alias)
* [⏰ 14. Programación de Tareas (Cron Jobs)](#14-programaci%C3%B3n-de-tareas-cron-jobs)
* [🐍 15. Gestión de Paquetes Python (PIP)](#15-gesti%C3%B3n-de-paquetes-python-pip)
* [📜 16. Fundamentos de Bash Scripting](#16-fundamentos-de-bash-scripting)
* [📊 17. Monitorización del Sistema y Rendimiento](#17-monitorizaci%C3%B3n-del-sistema-y-rendimiento)
* [🔍 18. Búsqueda y Localización de Ficheros](#18-b%C3%BAsqueda-y-localizaci%C3%B3n-de-ficheros)
* [🧪 19. Rendimiento de Disco y Comandos de Prueba](#19-rendimiento-de-disco-y-comandos-de-prueba)

---

## <a id="1-operaciones-con-archivos"></a>📁 1. Operaciones con Archivos

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `ls` | Lista todos los archivos y directorios del directorio de trabajo actual. | `ls` |
| `ls -R` | Lista los archivos de forma recursiva, incluyendo todos los subdirectorios. | `ls -R /etc/nginx/` |
| `ls -a` | Muestra todos los archivos, incluidos los archivos ocultos (los que comienzan por punto `.`). | `ls -a ~` |
| `ls -al` | Muestra un listado detallado (permisos, propietarios, tamaño, fecha) incluyendo archivos ocultos. | `ls -al /var/log` |
| `cd directoryname` | Cambia el directorio de trabajo activo a la ruta especificada. | `cd /var/www/html` |
| `cd ..` | Sube un nivel en el árbol jerárquico de directorios (directorio padre). | `cd ..` |
| `pwd` | Muestra la ruta absoluta del directorio de trabajo actual (*Print Working Directory*). | `pwd` |
| `cat > filename` | Crea un nuevo archivo y permite escribir contenido directamente desde la terminal (finalizar con `Ctrl + D`). | `cat > notas.txt` |
| `cat filename` | Muestra en pantalla el contenido completo de un archivo de texto. | `cat /etc/hosts` |
| `cat file1 file2 > file3` | Concatena dos archivos (`file1` y `file2`) y guarda el contenido resultante en un nuevo archivo (`file3`). | `cat parte1.txt parte2.txt > final.txt` |
| `touch filename` | Crea un archivo vacío si no existe, o actualiza la marca de tiempo de modificación si ya existe. | `touch archivo.txt` |
| `rm filename` | Elimina un archivo del sistema de archivos de forma permanente. | `rm archivo.txt` |
| `cp source destination` | Copia un archivo desde la ruta de origen a la ruta de destino. | `cp config.conf config.conf.bak` |
| `mv source destination` | Mueve un archivo a otra ubicación o lo renombra en la misma ruta. | `mv notas.txt /tmp/notas.txt` |
| `find / -name filename` | Busca archivos o directorios por nombre en todo el sistema empezando desde la raíz `/`. | `find / -name id_rsa 2>/dev/null` |
| `file filename` | Determina el tipo de formato y codificación real de un archivo mediante su cabecera (*magic bytes*). | `file payload.bin` |
| `less filename` | Visor paginado interactivo para leer archivos grandes sin saturar la terminal (salir con `q`). | `less /var/log/syslog` |
| `head filename` | Muestra por defecto las primeras 10 líneas de un archivo. | `head -n 20 access.log` |
| `tail filename` | Muestra por defecto las últimas 10 líneas de un archivo (con `-f` monitoriza en tiempo real). | `tail -f /var/log/auth.log` |
| `lsof` | Lista todos los archivos abiertos en el sistema y los procesos que los están utilizando (*List Open Files*). | `lsof -i :80` |
| `du -h --max-depth=1` | Muestra el tamaño ocupado por cada subdirectorio del directorio actual en formato legible (`-h`). | `du -h --max-depth=1 /home` |
| `fdisk` | Herramienta interactiva de particionamiento y gestión de tablas de particiones de discos. | `sudo fdisk -l` |

---

## <a id="2-operaciones-con-directorios"></a>📂 2. Operaciones con Directorios

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `mkdir directoryname` | Crea un nuevo directorio en el directorio de trabajo actual. | `mkdir -p /opt/tools/bin` |
| `rmdir directoryname` | Elimina un directorio **únicamente si está completamente vacío**. | `rmdir testdir` |
| `cp -r source destination` | Copia directorios completos de forma recursiva con todos sus contenidos y subcarpetas. | `cp -r /var/www /backup/www` |
| `mv olddir newdir` | Renombra un directorio o lo desplaza a otra ruta en el sistema. | `mv proyecto_v1 proyecto_v2` |
| `find / -type d -name directoryname` | Busca exclusivamente directorios (`-type d`) coincidentes con el nombre desde la raíz. | `find / -type d -name "*.git" 2>/dev/null` |

---

## <a id="3-gestion-de-procesos-y-redirecciones"></a>⚙️ 3. Gestión de Procesos y Redirecciones

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `ps` | Muestra una instantánea de los procesos activos asociados a la sesión actual del usuario. | `ps aux` |
| `top` | Monitor de procesos interactivo en tiempo real que lista uso de CPU, memoria y carga del sistema. | `top` |
| `kill pid` | Envía una señal de terminación (por defecto `SIGTERM` 15) al identificador de proceso (PID). | `kill 1234` |
| `pkill name` | Finaliza procesos coincidentes con el nombre de la aplicación o comando. | `pkill nginx` |
| `bg` | Reanuda un proceso detenido (`Ctrl + Z`) enviándolo a ejecutarse en segundo plano (*background*). | `bg` |
| `fg` | Trae al primer plano (*foreground*) el trabajo suspendido más reciente. | `fg` |
| `fg n` | Trae al primer plano el trabajo especificado por su número de tarea `n`. | `fg 1` |
| `renice +n [pid]` | Modifica la prioridad de ejecución (*nice value*) de un proceso en ejecución (valores de -20 a 19). | `renice +5 1234` |
| `&>filename` | Redirige tanto la salida estándar (`stdout`) como los errores estándar (`stderr`) al archivo especificado. | `command &> salida_total.log` |
| `1>filename` | Redirige únicamente la salida estándar (`stdout`, descriptor 1) hacia el archivo. | `nmap 10.10.10.1 1> scan.txt` |
| `2>filename` | Redirige únicamente la salida de errores (`stderr`, descriptor 2) hacia un archivo o a `/dev/null`. | `find / -perm -4000 2>/dev/null` |

---

## <a id="4-permisos-y-propietarios-de-archivos"></a>🔐 4. Permisos y Propietarios de Archivos

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `chmod octal filename` | Cambia los permisos de acceso de un archivo mediante notación octal (de `0` a `7` para Usuario, Grupo y Otros). | `chmod 755 script.sh` |
| `chown ownername filename` | Cambia el usuario propietario de un archivo o directorio. | `chown www-data index.php` |
| `chgrp groupname filename` | Cambia el grupo propietario de un archivo o directorio. | `chgrp sudo herramienta` |

> [!tip] Notación Octal Rápida
> * `4` = Lectura (`r`) | `2` = Escritura (`w`) | `1` = Ejecución (`x`)
> * `chmod 755` = Propietario (`rwx`), Grupo (`r-x`), Otros (`r-x`)
> * `chmod 600` = Propietario (`rw-`), Grupo (`---`), Otros (`---`) — Típico para llaves SSH `id_rsa`.

---

## <a id="5-redes-y-conectividad"></a>🌐 5. Redes y Conectividad

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `ping host` | Envía paquetes ICMP Echo Request al host destino para comprobar conectividad y latencia. | `ping -c 4 8.8.8.8` |
| `whois domain` | Consulta las bases de datos de registro Whois para obtener titularidad, registradores y servidores DNS. | `whois example.com` |
| `dig domain` | Utilidad de consulta DNS avanzada para resolver registros A, MX, NS, TXT, etc. (*Domain Information Groper*). | `dig example.com ANY` |
| `netstat -pnltu` | Muestra conexiones activas y sockets a la escucha con puertos numéricos y PID/programa asociado. | `netstat -pnltu` |
| `ifconfig` | Herramienta clásica de visualización y configuración de interfaces de red e IPs asignadas. | `ifconfig eth0` |
| `ssh user@host` | Inicia una sesión remota cifrada y segura contra el servidor SSH en el host indicado. | `ssh root@192.168.1.50` |
| `scp source destination` | Copia archivos de forma segura entre hosts remotos utilizando el protocolo SSH. | `scp backup.tar user@remoto:/tmp/` |
| `wget url` | Descarga directa de archivos y recursos web mediante protocolos HTTP, HTTPS y FTP. | `wget https://example.com/file.zip` |
| `curl url` | Realiza peticiones de red y transferencias de datos soportando HTTP/S, cabeceras personalizadas y APIs. | `curl -I https://google.com` |
| `traceroute domain` | Traza y muestra la ruta de enrutamiento y los saltos (*hops*) que recorren los paquetes hasta el destino. | `traceroute 1.1.1.1` |
| `mtr domain` | Diagnóstico interactivo que combina de forma continua las funciones de `traceroute` y `ping`. | `mtr google.com` |
| `ss` | Utilidad moderna y ultra-rápida de inspección de sockets; sustituto recomendado de `netstat`. | `ss -tulnp` |
| `nmap` | Escáner perimetral de red para descubrimiento de hosts activos, puertos abiertos y versiones de servicios. | `nmap -sV -sC 10.10.10.5` |

---

## <a id="6-compresion-y-empaquetado"></a>🗜️ 6. Compresión y Empaquetado

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `tar cf file.tar files` | Crea un empaquetado `.tar` sin comprimir que agrupa los archivos indicados (`c`=create, `f`=file). | `tar cf backup.tar /etc/nginx` |
| `tar xf file.tar` | Extrae el contenido de un archivo `.tar` en el directorio actual (`x`=extract). | `tar xf backup.tar` |
| `tar -cvf archive.tar dirname/` | Crea un archivo `.tar` mostrando en consola el progreso detallado (`v`=verbose). | `tar -cvf datos.tar datos/` |
| `tar -xvf archive.tar` | Extrae un archivo `.tar` mostrando cada archivo desempaquetado en pantalla. | `tar -xvf datos.tar` |
| `tar -jcvf archive.tar.bz2 dirname/` | Crea un archivo comprimido con algoritmo **Bzip2** (`-j`), ofreciendo mayor tasa de compresión. | `tar -jcvf logs.tar.bz2 /var/log/` |
| `tar -jxvf archive.tar.bz2` | Descomprime y extrae un archivo comprimido con Bzip2 (`.tar.bz2` o `.tbz2`). | `tar -jxvf logs.tar.bz2` |
| `gzip file` | Comprime un archivo individual utilizando el algoritmo Lempel-Ziv y lo renombra a `file.gz`. | `gzip accesos.log` |
| `gzip -d file.gz` | Descomprime un archivo `.gz` restaurando el archivo original (`-d`=decompress). | `gzip -d accesos.log.gz` |
| `zip -r file.zip files` | Genera un archivo `.zip` comprimido empaquetando archivos o carpetas recursivamente (`-r`). | `zip -r web.zip /var/www/html` |
| `unzip file.zip` | Extrae el contenido completo de un archivo `.zip` en la carpeta actual. | `unzip web.zip` |

---

## <a id="7-procesamiento-y-filtrado-de-texto"></a>📝 7. Procesamiento y Filtrado de Texto

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `grep pattern files` | Busca líneas que contengan la expresión o texto `pattern` dentro de los archivos indicados. | `grep "root" /etc/passwd` |
| `grep -r pattern dir` | Busca el patrón de forma recursiva dentro de todos los archivos y subcarpetas del directorio. | `grep -r "DB_PASSWORD" /var/www/` |
| `command \| grep pattern` | Filtra la salida de un comando anterior a través de un cauce (*pipeline* `\|`). | `ps aux \| grep apache` |
| `echo 'text'` | Imprime una cadena de texto o variable en la salida estándar de la consola. | `echo "Hola Mundo"` |
| `sed 's/str1/str2/g' file` | Editor de flujo: reemplaza todas las ocurrencias de `str1` por `str2` en pantalla sin alterar el archivo. | `sed 's/localhost/127.0.0.1/g' hosts` |
| `sed -i 's/str1/str2/g' file` | Reemplaza cadenas editando el archivo directamente en su lugar (*in-place*, opción `-i`). | `sed -i 's/80/8080/g' nginx.conf` |
| `diff file1 file2` | Compara dos archivos línea por línea y muestra las diferencias entre ellos. | `diff config.old config.new` |
| `wc filename` | Cuenta y muestra el número de líneas, palabras y bytes de un archivo (*Word Count*). | `wc -l /etc/passwd` |
| `awk` | Lenguaje de procesamiento y filtrado estructurado de patrones columna por columna. | `awk '{print $1, $3}' access.log` |
| `cut -d':' -f1 /etc/passwd` | Extrae columnas delimitadas por un separador (`-d':'`) seleccionando campos (`-f1`). | `cut -d':' -f1 /etc/passwd` |

---

## <a id="8-uso-de-disco-y-almacenamiento"></a>💾 8. Uso de Disco y Almacenamiento

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `df` | Muestra el espacio libre y ocupado en todos los sistemas de archivos montados (*Disk Free*). | `df` |
| `df -h` | Muestra el espacio de disco en formato legible para humanos (Gigabytes, Megabytes). | `df -h` |
| `du` | Muestra el espacio de disco consumido por archivos y subdirectorios (*Disk Usage*). | `du /var/log` |
| `du -sh` | Resume el tamaño total consumido por el directorio actual en formato legible. | `du -sh .` |
| `free` | Muestra la memoria RAM y Swap física utilizada y disponible en el sistema. | `free` |
| `free -m` | Muestra las estadísticas de memoria expresadas en Megabytes (MB). | `free -m` |
| `whereis app` | Localiza las rutas de binarios ejecutables, código fuente y páginas man de una aplicación. | `whereis python` |

---

## <a id="9-informacion-del-sistema-y-hardware"></a>ℹ️ 9. Información del Sistema y Hardware

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `date` | Imprime o configura la fecha y hora actual del sistema operativo. | `date "+%Y-%m-%d %H:%M:%S"` |
| `cal` | Muestra un calendario visual del mes en curso en la terminal. | `cal` |
| `uptime` | Muestra cuánto tiempo lleva encendido el sistema, número de usuarios y medias de carga (Load Average). | `uptime` |
| `w` | Muestra los usuarios conectados actualmente al sistema y lo que están ejecutando. | `w` |
| `whoami` | Imprime el nombre del usuario con el que se está ejecutando la sesión actual. | `whoami` |
| `uname -a` | Muestra toda la información de la arquitectura y versión del kernel de Linux (*Unix Name*). | `uname -a` |

---

## <a id="10-gestion-de-paquetes-apt"></a>📦 10. Gestión de Paquetes (APT)

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `sudo apt-get update` | Actualiza la lista local de paquetes y versiones disponibles desde los repositorios oficiales. | `sudo apt update` |
| `sudo apt-get upgrade` | Descarga e instala las versiones más recientes de todos los paquetes actualmente instalados. | `sudo apt upgrade -y` |
| `sudo apt-get install pkgname` | Descarga e instala un paquete y todas sus dependencias necesarias en el sistema. | `sudo apt install git curl` |
| `sudo apt-get remove pkgname` | Desinstala un paquete del sistema conservando sus archivos de configuración global. | `sudo apt remove apache2` |

---

## <a id="11-operadores-de-ejecucion-y-control-de-flujo"></a>⚡ 11. Operadores de Ejecución y Control de Flujo

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `command1 ; command2` | Ejecuta `command1` y a continuación `command2`, independientemente de si el primero tuvo éxito o falló. | `date ; ls -la` |
| `command1 && command2` | Operador AND lógico: Ejecuta `command2` **únicamente si** `command1` finalizó con éxito (código de salida 0). | `apt update && apt upgrade` |
| `command1 \|\| command2` | Operador OR lógico: Ejecuta `command2` **únicamente si** `command1` falla (código de retorno distinto de 0). | `ping -c 1 host \|\| echo "Caído"` |
| `command &` | Lanza la ejecución del comando en segundo plano (*background*), liberando la terminal inmediatamente. | `python3 -m http.server 8080 &` |

---

## <a id="12-control-de-versiones-con-git"></a>🐙 12. Control de Versiones con Git

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `git init` | Inicializa un nuevo repositorio Git local en el directorio actual. | `git init` |
| `git clone url` | Clona y descarga una copia completa de un repositorio remoto en una nueva carpeta local. | `git clone https://github.com/...` |
| `git add filename` | Añade los cambios de un archivo al área de preparación (*staging area*). | `git add .` |
| `git commit -m "msg"` | Registra y empaqueta los cambios del staging area en un nuevo commit con un mensaje descriptivo. | `git commit -m "feat: base"` |
| `git status` | Muestra el estado del árbol de trabajo, archivos modificados y pendientes de commit. | `git status` |
| `git pull` | Descarga y fusiona las últimas modificaciones de la rama remota en la rama activa. | `git pull origin main` |
| `git push` | Sube los commits locales registrados al repositorio remoto configurado. | `git push origin main` |
| `git branch` | Lista todas las ramas locales existentes en el repositorio (la activa marcada con `*`). | `git branch` |
| `git branch branchname` | Crea una nueva rama local con el nombre especificado a partir del commit actual. | `git branch dev` |
| `git checkout branchname` | Cambia de rama activa o restaura archivos del árbol de trabajo. | `git checkout dev` |
| `git merge branchname` | Fusiona la historia de la rama especificada dentro de la rama activa actual. | `git merge dev` |
| `git stash` | Guarda temporalmente los cambios no confirmados del área de trabajo en una pila limpia. | `git stash` |
| `git stash apply` | Restaura los cambios guardados previamente en el stash sobre el directorio de trabajo activo. | `git stash apply` |
| `git log` | Muestra el historial cronológico de commits confirmados con autor, fecha y hash SHA. | `git log --oneline -n 10` |
| `git reset` | Deshace cambios retrocediendo el puntero HEAD al estado de un commit previo. | `git reset --soft HEAD~1` |
| `git rm filename` | Elimina un archivo del control de versiones y del sistema de archivos local. | `git rm secreto.env` |
| `git rebase` | Reaplica commits de una rama sobre la punta de otra base para un historial lineal. | `git rebase main` |
| `git revert` | Genera un nuevo commit que invierte de forma segura los cambios de un commit específico. | `git revert HEAD` |
| `git cherry-pick commitID` | Aplica individualmente los cambios introducidos por un commit específico en la rama actual. | `git cherry-pick a1b2c3d` |

---

## <a id="13-variables-de-entorno-y-alias"></a>🌲 13. Variables de Entorno y Alias

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `env` | Muestra todas las variables de entorno exportadas activas en la sesión actual. | `env` |
| `echo $VARIABLE` | Imprime en pantalla el valor almacenado en una variable de entorno específica. | `echo $USER` |
| `export VARIABLE=value` | Asigna y exporta una variable de entorno para que esté disponible en los subprocesos hijos. | `export ARCH=amd64` |
| `alias new='old opts'` | Crea un atajo o alias personalizado que invoca un comando previo con opciones fijadas. | `alias ll='ls -la'` |
| `echo $PATH` | Imprime la variable `$PATH` con la lista de directorios donde la shell busca binarios ejecutables. | `echo $PATH` |
| `export PATH=$PATH:/dir` | Añade un nuevo directorio a la ruta de búsqueda de ejecutables `$PATH`. | `export PATH=$PATH:/opt/bin` |

---

## <a id="14-programacion-de-tareas-cron-jobs"></a>⏰ 14. Programación de Tareas (Cron Jobs)

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `crontab -l` | Lista todas las tareas programadas activas en la tabla cron del usuario actual. | `crontab -l` |
| `crontab -e` | Abre el editor para añadir, modificar o configurar tareas programadas de cron. | `crontab -e` |
| `crontab -r` | Elimina y borra por completo el archivo crontab del usuario actual. | `crontab -r` |
| `crontab -v` | Muestra la fecha y hora de la última modificación realizada en el crontab (en sistemas compatibles). | `crontab -v` |
| `crontab file` | Instala y carga las tareas programadas directamente desde un archivo de texto plano. | `crontab mis_tareas.cron` |
| `@reboot command` | Directiva especial de cron para ejecutar un comando o script automáticamente tras el arranque del sistema. | `@reboot /home/user/init.sh` |

> [!info] Estructura Estándar de Cron (5 Campos)
> `* * * * * comando`  
> `Minuto (0-59) | Hora (0-23) | Día del Mes (1-31) | Mes (1-12) | Día de la Semana (0-7)`

---

## <a id="15-gestion-de-paquetes-python-pip"></a>🐍 15. Gestión de Paquetes Python (PIP)

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `pip install pkgname` | Descarga e instala un paquete o librería de Python desde el repositorio oficial PyPI. | `pip install requests` |
| `pip uninstall pkgname` | Desinstala un paquete de Python instalado previamente en el entorno. | `pip uninstall requests` |
| `pip freeze > reqs.txt` | Vuelca la lista de todos los paquetes instalados y sus versiones exactas a un archivo de requisitos. | `pip freeze > requirements.txt` |
| `pip install -r reqs.txt` | Instala de forma masiva todos los paquetes y dependencias listados en `requirements.txt`. | `pip install -r requirements.txt` |

---

## <a id="16-fundamentos-de-bash-scripting"></a>📜 16. Fundamentos de Bash Scripting

| Elemento / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `#!/bin/bash` | Cabecera *Shebang*: Especifica qué intérprete binario ejecutará el script. | `#!/bin/bash` |
| `$0, $1 ... $9, ${10}` | Argumentos posicionales: `$0` es el nombre del script, `$1` el primer argumento, etc. | `echo "Arg 1: $1"` |
| `if [ condition ]; then ... fi` | Estructura condicional para evaluar comprobaciones numéricas, de cadenas o ficheros. | `if [ -f /etc/passwd ]; then echo "Existe"; fi` |
| `for i in {1..10}; do ... done` | Bucle de iteración que ejecuta un bloque de instrucciones para cada elemento. | `for ip in {1..254}; do ping -c 1 192.168.1.$ip; done` |
| `while [ cond ]; do ... done` | Bucle de repetición que se ejecuta de forma continuada mientras la condición sea verdadera. | `while true; do sleep 1; done` |
| `function name() { ... }` | Definición de una función reutilizable con bloque de código propio dentro del script. | `saludar() { echo "Hola $1"; }` |

---

## <a id="17-monitorizacion-del-sistema-y-rendimiento"></a>📊 17. Monitorización del Sistema y Rendimiento

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `iostat` | Informa sobre estadísticas de uso de CPU y tasas de entrada/salida (I/O) de discos y particiones. | `iostat -xz 1` |
| `vmstat` | Informa sobre memoria virtual, procesos, paginación, bloques de I/O, trampas y actividad de CPU. | `vmstat 2 5` |
| `htop` | Visor y gestor interactivo y colorido de procesos en tiempo real (alternativa avanzada a `top`). | `htop` |

---

## <a id="18-busqueda-y-localizacion-de-ficheros"></a>🔍 18. Búsqueda y Localización de Ficheros

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `locate filename` | Busca rápidamente rutas de archivos mediante una base de datos indexada (actualizada con `updatedb`). | `locate rockyou.txt` |
| `whereis programname` | Localiza las rutas del binario ejecutable, código fuente y manual man de una orden. | `whereis bash` |
| `which commandname` | Devuelve la ruta absoluta del binario ejecutable que resolvería la shell según la variable `$PATH`. | `which python3` |

---

## <a id="19-rendimiento-de-disco-y-comandos-de-prueba"></a>🧪 19. Rendimiento de Disco y Comandos de Prueba

| Comando / Sintaxis | Descripción | Ejemplo de Uso |
| :--- | :--- | :--- |
| `dd if=/dev/zero of=/tmp/output.img bs=8k count=256k` | Crea un archivo de prueba escribiendo ceros para medir la velocidad de escritura secuencial en disco. | `dd if=/dev/zero of=/tmp/test.img bs=1M count=100` |
| `hdparm -Tt /dev/sda` | Mide la velocidad de lectura directa del disco duro y de la memoria caché de almacenamiento. | `sudo hdparm -Tt /dev/sda` |
| `yes > /dev/null &` | Genera una cadena continua infinita enviada a `/dev/null` para someter la CPU a una carga del 100%. | `yes > /dev/null &` |

> [!caution] Alerta de Seguridad: Fork Bomb
> ```bash
> :(){ :|:& };:
> ```
> * **Definición:** Función recursiva que se autollama a sí misma dos veces y se envía a segundo plano en bucle infinito.
> * **Efecto:** Agota en segundos la tabla de procesos del kernel (*Process Table Starvation*) congelando el sistema por completo (DoS local).
> * **Regla:** **Nunca ejecutar en entornos de producción.** Utilizar directivas de seguridad en `/etc/security/limits.conf` (`nproc`) para mitigar este vector.

---

> [!tip] Ayuda y Manuales del Sistema
> Recuerda que en cualquier sistema Linux puedes consultar la documentación exhaustiva y los modificadores de cualquier comando ejecutando:
> ```bash
> man <comando>       # Ej: man ls
> <comando> --help    # Ej: grep --help
> info <comando>      # Ej: info tar
> ```

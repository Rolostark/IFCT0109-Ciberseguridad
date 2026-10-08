---
title: "Instalación Docker en Kali Linux - Curso Linux Pentesting"
aliases:
  - Instalación Docker en Kali Linux - Curso Linux Pentesting
tags:
  - ifct0109/pocs
  - linux
  - docker
  - kali
---

# Instalación de Docker en Kali Linux

Entorno Debian 12 containerizado para el curso

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

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

      [📄 Descargar Dockerfile](archivos/Dockerfile)
      [🔧 Descargar docker-menu.sh](archivos/docker-menu.sh)

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

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

Instalación de Docker en Kali Linux - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v1.0

---

## 🔗 Navegación
* 🐧 [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|Volver al Portal del Curso de Linux]]
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice General de Linux]]

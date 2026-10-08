---
title: "Índice del Módulo - Escalada de Privilegios en Windows"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - escalada-de-privilegios-en-windows
  - privilege-escalation
  - winpeas
  - potato
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]]

# ⚡ Escalada de Privilegios en Windows — Índice del Módulo

> [!abstract] Resumen del Módulo
> Este módulo práctico enseña cómo elevar privilegios desde una cuenta estándar (`User` / `tecnico`) hasta el control total del sistema operativo (**NT AUTHORITY\SYSTEM** o **Administrador**) en entornos Windows 10 y Windows Server. Incluye metodologías de enumeración manual y automatizada (WinPEAS, Seatbelt), abuso de configuraciones débiles de servicios, manipulación de claves de registro, extracción de credenciales en memoria/disco y explotación de privilegios de tokens.

---

## 📦 Recursos Clave del Módulo

> [!tip] Cheatsheet Rápido de Comandos
> Accede al archivo completo de sintaxis y secuencias organizadas por fase:
> 👉 **[[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/Cheatsheet de Comandos de Escalada|Acceder al Cheatsheet de Comandos de Escalada]]**

---

## 🗺️ Mapa de Técnicas y Laboratorios Prácticos

| Fase / Módulo | Técnica Específica | Vector / Herramienta | Enlace a la Práctica |
| :-: | :--- | :--- | :--- |
| **01** | Enumeración del Sistema | Red, Defender, AppLocker, Usuarios, Named Pipes | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/01 - Enumeración y Recopilación de Información\|01 - Enumeración y Recopilación de Información]] |
| **02** | Explotación del Kernel | Detección de CVEs con WES-NG y exploits de kernel | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/02 - Explotación del Kernel de Windows\|02 - Explotación del Kernel de Windows]] |
| **03-01** | Insecure Service Permissions | Modificación de binario/parámetros en `daclsvc` | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/03-01 - Servicios - Insecure Service Permissions\|03-01 - Insecure Service Permissions]] |
| **03-02** | DLL Hijacking | Suplantación de DLLs faltantes en rutas buscadas | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/03-02 - Servicios - DLL Hijacking\|03-02 - DLL Hijacking]] |
| **03-03** | Unquoted Service Path | Inserción de binario malicioso en rutas con espacios | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/03-03 - Servicios - Unquoted Service Path\|03-03 - Unquoted Service Path]] |
| **03-04** | Weak Registry Permissions | Alteración de `ImagePath` en claves de registro HKLM | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/03-04 - Servicios - Weak Registry Permissions\|03-04 - Weak Registry Permissions]] |
| **03-05** | Insecure Service Executable | Reemplazo directo del `.exe` original del servicio | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/03-05 - Servicios - Insecure Service Executable\|03-05 - Insecure Service Executable]] |
| **04-01** | Autorun (Inicio Automático) | Permisos de escritura en ejecutables de inicio | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/04-01 - Registro - Autorun (Inicio Automático)\|04-01 - Autorun (Inicio Automático)]] |
| **04-02** | AlwaysInstallElevated | Instalación de paquetes `.msi` maliciosos como SYSTEM | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/04-02 - Registro - AlwaysInstallElevated\|04-02 - AlwaysInstallElevated]] |
| **05-01** | Contraseñas en el Registro | Búsqueda de `AutoLogon`, `VNC`, `PuTTY` en el Registry | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/05-01 - Contraseñas en el Registro\|05-01 - Contraseñas en el Registro]] |
| **05-02** | Credential Manager | Extracción y reutilización con `cmdkey /list` y `runas` | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/05-02 - Credenciales Almacenadas (Credential Manager)\|05-02 - Credenciales Almacenadas]] |
| **05-03** | Archivos de Configuración | Búsqueda de `unattend.xml`, `sysprep.inf`, `web.config` | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/05-03 - Contraseñas en Archivos de Configuración\|05-03 - Contraseñas en Archivos]] |
| **05-04** | SAM y Hashdump | Volcado de hives `SAM` y `SYSTEM` para crackeo | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/05-04 - SAM (Security Account Manager) y Hashdump\|05-04 - Extracción de SAM y Hashdump]] |
| **06** | Aplicaciones Instaladas | Detección de software de terceros desactualizado | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/06 - Explotación de Aplicaciones Instaladas Vulnerables\|06 - Aplicaciones Instaladas Vulnerables]] |
| **07** | Tareas Programadas | Abuso de Scheduled Tasks (`schtasks`) mal configuradas | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/07 - Explotación de Tareas Programadas (Scheduled Tasks)\|07 - Tareas Programadas (Scheduled Tasks)]] |
| **08** | Carpeta de Inicio (Startup) | Inserción de payloads en `%ProgramData%\Microsoft\Windows\Start Menu` | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/08 - Explotación de Carpeta de Inicio (Startup Folder)\|08 - Carpeta de Inicio (Startup)]] |
| **09** | Token Impersonation | Abuso de `SeImpersonatePrivilege` (Juicy/God Potato) | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/09 - Token Impersonation (SeImpersonatePrivilege)\|09 - Token Impersonation (Potato)]] |

---

## ⚙️ Entorno de Laboratorio

* **Máquina Víctima:** Windows 10 Pro (`PrivEscWin10`) — IP: `10.0.0.200`
* **Máquina Atacante:** Kali Linux — IP: `10.0.0.5`
* **Credenciales Iniciales:** `User:password321` / `tecnico:SegInf`
* **Credenciales Administrador:** `Adminis:password123`
* **Ruta de Herramientas:** `C:\tools` (WinPEAS, Seatbelt, Sysinternals, AccessChk)

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]]

---
title: "🧪 Pruebas de Concepto (PoCs) — Catálogo Integral de Laboratorios"
aliases:
  - PoCs
  - Practicas
  - Laboratorios PoC
tags:
  - ifct0109/pocs
  - laboratorios
  - ejercicios
  - linux
  - msdos
  - redes
  - auditoria
  - hacking-etico
---

# 🧪 Pruebas de Concepto (PoCs) — Catálogo Integral de Laboratorios

> [!abstract] Catálogo de Prácticas
> Recopilación exhaustiva de laboratorios prácticos paso a paso que integran el temario oficial del certificado **IFCT0109 (Seguridad Informática)**. Todos los contenidos han sido sincronizados **1:1 con el portal oficial**, desacoplados en notas modulares y adaptados con imágenes y recursos descargados para su ejecución completa en modo **offline**.
> 
> 🔗 **Navegación:** [[00 - INDICE|🏠 Inicio]] &emsp;|&emsp; [[01 - CURSO/01 - Tablero de contenidos|🗂️ Tablero de Contenidos]] &emsp;|&emsp; [[01 - CURSO/02 - Tablero de contenidos curso 023|📋 Tablero Curso 023]]

---

## 🗂️ Módulos de Prácticas (Acceso Rápido)

> [!info] 🐧 [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Módulo Completo de Linux (17 Notas y Retos HackMyVM)]]
> *Curso de Linux para Pentesting (Debian WSL/Docker, FHS, Comandos, Grupos, Usuarios, Permisos), filtrado de datos (grep, awk, sed), Bash Scripting, Linux Capabilities, Debian + DWM y writeups íntegros de las VMs **Venus** (54 misiones) y **Hades** (50 misiones).*

> [!info] 💾 [[02 - MODULOS/Prácticas (PoCs)/MS-DOS/01 - Comandos de MS-DOS|Módulo MS-DOS y Conexiones de Red]]
> *Comandos de administración en Windows (24 ejercicios prácticos) y [[02 - MODULOS/Prácticas (PoCs)/MS-DOS/02 - Conexiones Directas e Inversas con Netcat|Conexiones Directas e Inversas con Netcat]] (Bind Shell vs Reverse Shell).*

> [!info] 🖥️ [[02 - MODULOS/Prácticas (PoCs)/Sistemas/01 - Creación de NAS con OpenMediaVault|Módulo de Sistemas (Almacenamiento y Dominios)]]
> *Despliegue de almacenamiento en red [[02 - MODULOS/Prácticas (PoCs)/Sistemas/01 - Creación de NAS con OpenMediaVault|NAS con OpenMediaVault]] y laboratorio de 6 partes de [[02 - MODULOS/Prácticas (PoCs)/Sistemas/02 - Active Directory en Windows Server 2019|Active Directory y GPOs en Windows Server 2019]] (Red, Roles, Promoción a DC, OUs, Directivas GPO y Registro).*

> [!info] 📖 [[02 - MODULOS/Prácticas (PoCs)/Conceptos Fundamentales/01 - Conexión Segura con SSH y Túneles|Módulo de Conceptos Fundamentales]]
> *Túneles SSH, peticiones cURL, curso de 10 módulos de [[02 - MODULOS/Prácticas (PoCs)/Conceptos Fundamentales/03 - Auditoría Web con Firefox DevTools|Firefox DevTools Lab]] (HTML, CSS, Consola, Debugger, Red, Storage, Responsive, A11y, Performance), transferencia de archivos, HTTP, ataques MITM, phishing y payloads con Msfvenom.*

> [!info] 🔍 [[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/01 - Escaneo y Enumeración de Redes con Nmap|Módulo de Herramientas de Auditoría]]
> *Nmap, Responder (LLMNR/NBT-NS), Metasploit (+ Meterpreter Post-Explotación), Hashcat, Gobuster, Hydra (+ minirock), WiFi (Aircrack-ng), Wireshark (+ Retos prácticos de tráfico), BloodHound (4 laboratorios + collectors), Pivoting (5 laboratorios de túneles + binarios) y Buffer Overflow (2 laboratorios + binarios).*

> [!info] 🔒 [[02 - MODULOS/Prácticas (PoCs)/Encriptado de Datos/01 - Cifrado de Contenedores con 7-Zip|Módulo de Encriptado de Datos]]
> *Cifrado de ficheros (7-Zip), directorios (EncFS), discos enteros (BitLocker), volúmenes ocultos (VeraCrypt), infraestructura asimétrica (Gpg4win) y técnicas de esteganografía forense.*

> [!info] 🛡️ [[02 - MODULOS/Prácticas (PoCs)/Firewall/01 - Instalación y Topología de Red con IPFire|Módulo de Seguridad Perimetral y Firewalls]]
> *Topologías por zonas con IPFire, políticas perimetrales y NAT, túneles VPN (OpenVPN/IPsec), IDS/IPS Suricata, servidor proxy Squid y cortafuegos avanzados en Windows y Linux (Iptables/NFTables/UFW).*

> [!info] 🕷️ [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Módulo de Vulnerabilidades Web (PoC)]]
> *Laboratorios prácticos sobre aplicaciones vulnerables: **DVWA** (10 prácticas: Inyecciones de comandos, fuerza bruta, CSRF, LFI, subida de ficheros, XSS, SQLi manual y con SQLmap) y **VulnWeb** (8 módulos: Command Injection, SSTI, LFI/RFI, CSRF, File Upload, XSS, SQLi, Tokens JWT).*

> [!info] ⚡ [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/00 - Índice del Módulo Escalada de Privilegios en Windows|Módulo de Escalada de Privilegios en Windows]]
> *Arsenal de elevación local (17 laboratorios y cheatsheet de comandos): Recopilación de información, exploits de kernel, servicios (permisos inseguros, DLL Hijacking, Unquoted Paths, Weak Registry, ejecutables reemplazables), claves de registro (Autorun, AlwaysInstallElevated), contraseñas (registro, archivos, SAM), tareas programadas y suplantación de tokens (`SeImpersonatePrivilege`).*

> [!info] 🚨 [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/00 - Índice del Módulo Gestión de Incidentes|Módulo de Gestión de Incidentes de Seguridad]]
> *Flujo defensivo integral: **Análisis de Malware** (análisis dinámico en sandbox, estático e ingeniería inversa), **SOC & MITRE ATT&CK Framework**, monitorización y defensa activa con **SIEM y Wazuh**, y **Análisis Forense Digital** (adquisición, preservación y cadena de custodia).*

> [!info] 📱 [[02 - MODULOS/Prácticas (PoCs)/Pentesting en Android/01 - Entorno y Emulador con Android Studio|Módulo de Pentesting en Android]]
> *Configuración de AVD, metodología de auditoría móvil, ingeniería inversa con Apktool y APK Easy Tool, port forwarding con Ngrok/Serveo, monitoreo con L3MON y análisis de ofuscación Smali.*

---

## 📊 Matriz Global de Prácticas y Laboratorios

| Área Técnica                  | Icono | Prácticas Clave Incluidas                                                                                                             | Acceso Directo al Módulo                                                                                                      |
| :---------------------------- | :---: | :------------------------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------- |
| **Linux**                     |  🐧   | Curso Pentesting (WSL/Docker, Módulos 1–8), Filtros, Bash Scripting, Capabilities, Debian+DWM, VMs Venus y Hades                      | [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux\|Acceder a Linux]]                                         |
| **MS-DOS**                    |  💾   | Consola CMD (24 ejercicios), sintaxis de ayuda, batch scripting y Netcat directo/inverso                                              | [[02 - MODULOS/Prácticas (PoCs)/MS-DOS/01 - Comandos de MS-DOS\|Acceder a MS-DOS]]                                            |
| **Sistemas**                  |  🖥️  | Servidor NAS OpenMediaVault y Laboratorio 6 partes de Active Directory 2019 (DC, OUs, GPOs, Regedit)                                  | [[02 - MODULOS/Prácticas (PoCs)/Sistemas/01 - Creación de NAS con OpenMediaVault\|Acceder a Sistemas]]                        |
| **Conceptos Fundamentales**   |  📖   | Túneles SSH, cURL, DevTools Lab (10 módulos), transferencia de ficheros, HTTP, MITM, Phishing, Msfvenom                               | [[02 - MODULOS/Prácticas (PoCs)/Conceptos Fundamentales/01 - Conexión Segura con SSH y Túneles\|Acceder a Conceptos]]         |
| **Herramientas de Auditoría** |  🔍   | Nmap, Responder, Metasploit, Hashcat, Gobuster, Hydra, WiFi, Wireshark (+Retos), BloodHound (4 labs), Pivoting (5 labs), BoF (2 labs) | [[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/01 - Escaneo y Enumeración de Redes con Nmap\|Acceder a Auditoría]] |
| **Encriptado de Datos**       |  🔒   | 7-Zip, EncFS, BitLocker, VeraCrypt, Gpg4win (firmas/PKI) y Esteganografía forense                                                     | [[02 - MODULOS/Prácticas (PoCs)/Encriptado de Datos/01 - Cifrado de Contenedores con 7-Zip\|Acceder a Encriptado]]            |
| **Firewall**                  |  🛡️  | IPFire (zonas, reglas, NAT, OpenVPN/IPsec, IDS Suricata, Proxy Squid) y Firewall Windows/Linux                                        | [[02 - MODULOS/Prácticas (PoCs)/Firewall/01 - Instalación y Topología de Red con IPFire\|Acceder a Firewall]]                 |
| **Vulnerabilidades WEB**      |  🕷️  | DVWA (10 laboratorios OWASP) y VulnWeb (8 laboratorios: Inyecciones, SSTI, CSRF, XSS, SQLi, JWT)                                      | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web\|Acceder a Vulnerabilidades Web]] |
| **Escalada en Windows**       |   ⚡   | CheatSheet comandos, Kernel, Servicios inseguros, DLL Hijacking, Unquoted Paths, Registro, SAM, Tokens                                | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/00 - Índice del Módulo Escalada de Privilegios en Windows\|Acceder a Escalada en Windows]] |
| **Gestión de Incidentes**     |  🚨   | Análisis de Malware (Dinámico, Estático, Reversa), SOC & MITRE ATT&CK, SIEM Wazuh, Forense Digital                                    | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/00 - Índice del Módulo Gestión de Incidentes\|Acceder a Gestión de Incidentes]] |
| **Pentesting en Android**     |  📱   | Android Studio, Apktool, Smali, Ngrok, Serveo, C2 L3MON, ofuscación y APK Easy Tool                                                   | [[02 - MODULOS/Prácticas (PoCs)/Pentesting en Android/01 - Entorno y Emulador con Android Studio\|Acceder a Android]]         |

---

> [!tip] Recursos Offline y Descargas
> * Los paquetes pesados de laboratorio (`.7z`) se encuentran en `archivos_descargados/paquetes_7z/` (ignorados por Git para optimizar el repositorio).
> * Los recursos y ejecutables complementarios (`SharpHound`, `minirock.txt`, zips de AD) se ubican en `archivos_descargados/recursos/`.
> * Todas las capturas y diagramas (450+ imágenes) están descargadas localmente en `archivos_descargados/imagenes/` para navegación 100% offline.

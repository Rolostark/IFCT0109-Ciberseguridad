---
title: "🧪 Pruebas de Concepto (PoCs) — Laboratorios Oficiales"
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
> Recopilación exhaustiva de laboratorios prácticos paso a paso que integran el temario oficial del certificado **IFCT0109 (Seguridad Informática)**. Todos los contenidos han sido desacoplados y adaptados para su ejecución completa en modo **offline**.
> 
> 🔗 **Navegación:** [[00 - INDICE|🏠 Inicio]] &emsp;|&emsp; [[01 - CURSO/01 - Tablero de contenidos|🗂️ Tablero de Contenidos]] &emsp;|&emsp; [[01 - CURSO/02 - Tablero de contenidos curso 023|📋 Tablero Curso 023]]

---

## 🗂️ Módulos de Prácticas (Acceso Rápido)

> [!info] 🐧 [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Módulo Completo de Linux (13 Notas y Retos HackMyVM)]]
> *Curso de Linux para Pentesting (Módulos 1 a 6), filtrado de datos (grep, awk, sed), Bash Scripting, Linux Capabilities, Debian + DWM y writeups íntegros de las VMs **Venus** (54 misiones) y **Hades** (50 misiones).*

> [!info] 💾 [[02 - MODULOS/Prácticas (PoCs)/MS-DOS/01 - Comandos de MS-DOS|Módulo MS-DOS y Conexiones de Red]]
> *Comandos de administración en Windows (24 ejercicios prácticos) y [[02 - MODULOS/Prácticas (PoCs)/MS-DOS/02 - Conexiones Directas e Inversas con Netcat|Conexiones Directas e Inversas con Netcat]] (Bind Shell vs Reverse Shell).*

> [!info] 🖥️ [[02 - MODULOS/Prácticas (PoCs)/Sistemas/01 - Creación de NAS con OpenMediaVault|Módulo de Sistemas (Almacenamiento y Dominios)]]
> *Despliegue de almacenamiento en red [[02 - MODULOS/Prácticas (PoCs)/Sistemas/01 - Creación de NAS con OpenMediaVault|NAS con OpenMediaVault]] y administración de [[02 - MODULOS/Prácticas (PoCs)/Sistemas/02 - Active Directory en Windows Server 2019|Active Directory y GPOs en Windows Server 2019]].*

> [!info] 📖 [[02 - MODULOS/Prácticas (PoCs)/Conceptos Fundamentales/01 - Conexión Segura con SSH y Túneles|Módulo de Conceptos Fundamentales]]
> *Túneles SSH, peticiones cURL, auditoría web con Firefox DevTools, protocolos HTTP, ataques MITM, phishing y creación de payloads con Msfvenom.*

> [!info] 🔍 [[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/01 - Escaneo y Enumeración de Redes con Nmap|Módulo de Herramientas de Auditoría]]
> *Reconocimiento con Nmap, envenenamiento LLMNR con Responder, explotación con Metasploit, cracking GPU con Hashcat, fuzzing con Gobuster, fuerza bruta con Hydra, auditoría WiFi, Wireshark, BloodHound, Pivoting y Buffer Overflow.*

> [!info] 🔒 [[02 - MODULOS/Prácticas (PoCs)/Encriptado de Datos/01 - Cifrado de Contenedores con 7-Zip|Módulo de Encriptado de Datos]]
> *Cifrado de ficheros (7-Zip), directorios (EncFS), discos enteros (BitLocker), volúmenes ocultos (VeraCrypt), infraestructura asimétrica (Gpg4win) y técnicas de esteganografía.*

> [!info] 🛡️ [[02 - MODULOS/Prácticas (PoCs)/Firewall/01 - Instalación y Topología de Red con IPFire|Módulo de Seguridad Perimetral y Firewalls]]
> *Topologías por zonas con IPFire, políticas de filtrado y NAT, túneles VPN, IDS/IPS Suricata, servidor proxy Squid y cortafuegos avanzados en Windows y Linux (Iptables/UFW).*

> [!info] 📱 [[02 - MODULOS/Prácticas (PoCs)/Pentesting en Android/01 - Entorno y Emulador con Android Studio|Módulo de Pentesting en Android]]
> *Configuración de AVD, metodología de auditoría móvil, ingeniería inversa con Apktool y APK Easy Tool, port forwarding con Ngrok/Serveo, monitoreo con L3MON y análisis de ofuscación Smali.*

---

## 📊 Matriz Global de Prácticas y Laboratorios

| Área Técnica | Icono | Prácticas Clave Incluidas | Acceso Directo al Módulo |
| :--- | :---: | :--- | :--- |
| **Linux** | 🐧 | Curso Pentesting (1–6), Filtros, Bash Scripting, Capabilities, Debian+DWM, VMs Venus y Hades | [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux\|Acceder a Linux]] |
| **MS-DOS** | 💾 | Consola CMD (24 ejercicios), sintaxis de ayuda, batch scripting y Netcat directo/inverso | [[02 - MODULOS/Prácticas (PoCs)/MS-DOS/01 - Comandos de MS-DOS\|Acceder a MS-DOS]] |
| **Sistemas** | 🖥️ | Servidor NAS OpenMediaVault (RAID/SMB/NFS) y Controlador de Dominio Active Directory 2019 | [[02 - MODULOS/Prácticas (PoCs)/Sistemas/01 - Creación de NAS con OpenMediaVault\|Acceder a Sistemas]] |
| **Conceptos Fundamentales** | 📖 | Túneles SSH, cURL, Firefox DevTools, transferencia de ficheros, HTTP, MITM, Phishing, Msfvenom | [[02 - MODULOS/Prácticas (PoCs)/Conceptos Fundamentales/01 - Conexión Segura con SSH y Túneles\|Acceder a Conceptos]] |
| **Herramientas de Auditoría** | 🔍 | Nmap, Responder, Metasploit, Hashcat, Gobuster, Hydra, WiFi, Wireshark, BloodHound, Pivoting, BoF | [[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/01 - Escaneo y Enumeración de Redes con Nmap\|Acceder a Auditoría]] |
| **Encriptado de Datos** | 🔒 | 7-Zip, EncFS, BitLocker, VeraCrypt, Gpg4win (firmas/PKI) y Esteganografía forense | [[02 - MODULOS/Prácticas (PoCs)/Encriptado de Datos/01 - Cifrado de Contenedores con 7-Zip\|Acceder a Encriptado]] |
| **Firewall** | 🛡️ | IPFire (zonas, reglas, NAT, OpenVPN/IPsec, IDS Suricata, Proxy Squid) y Firewall Windows/Linux | [[02 - MODULOS/Prácticas (PoCs)/Firewall/01 - Instalación y Topología de Red con IPFire\|Acceder a Firewall]] |
| **Pentesting en Android** | 📱 | Android Studio, Apktool, Smali, Ngrok, Serveo, C2 L3MON, ofuscación y APK Easy Tool | [[02 - MODULOS/Prácticas (PoCs)/Pentesting en Android/01 - Entorno y Emulador con Android Studio\|Acceder a Android]] |

---

> [!tip] Navegación Cruzada
> * Consulta el estado de realización de estas prácticas en el [[01 - CURSO/02 - Tablero de contenidos curso 023|Tablero Kanban del Curso 023]].
> * Dispones de los recursos pesados descargados offline en la carpeta `archivos_descargados/paquetes_7z/` y `archivos_descargados/documentos/`.

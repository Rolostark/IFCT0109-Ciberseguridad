---
title: "Historial de Pasos — Migración y Creación del Vault Obsidian IFCT0109"
aliases:
  - Pasos
  - Historial
  - Registro de Migracion
tags:
  - ifct0109
  - documentacion
  - git
  - pasos
---

# 📜 Registro de Pasos — Migración a Obsidian Vault IFCT0109

Este documento registra cronológicamente los pasos ejecutados para transformar el material docente del curso **IFCT0109: Seguridad Informática** en una bóveda de **Obsidian** 100% funcional y autónoma offline.

---

## 📌 Paso 1: Análisis del Contenido Original
* Inspección de los archivos HTML originales alojados en la carpeta del curso.
* Detección de la correspondencia con el Certificado de Profesionalidad oficial **IFCT0109 (Seguridad Informática)** impartido por Manuel FR (`cursosmfr.es`).
* Identificación de dependencias externas: enlaces web, formularios, descargas en `.7z`, documentos `.docx`, hojas `.xlsx` y archivos `.pdf`.

---

## 📌 Paso 2: Conversión Inicial a Markdown para Obsidian
* Transformación de los portales web estáticos en notas Markdown con sintaxis enriquecida:
  * **Metadatos YAML (Frontmatter):** `title`, `aliases`, `tags`.
  * **Wikilinks bidireccionales:** `[[Nota|Texto]]`.
  * **Callouts visuales de Obsidian:** `> [!info]`, `> [!tip]`, `> [!abstract]`, `> [!example]`, `> [!warning]`.
  * **Diagramas Mermaid:** Diagrama de flujo de aprendizaje en el Roadmap (`flowchart LR`).
  * **Extracción de Dorks:** Tabulación de las 54 consultas de Google Dorks para OSINT y Bug Bounty clasificadas por nivel y categoría.

---

## 📌 Paso 3: Reorganización según `INDICE.png`
* Adaptación de la estructura del Vault siguiendo fielmente la captura de referencia `INDICE.png`:
  * Numeración de carpetas y archivos para forzar el orden visual exacto en el explorador de archivos de Obsidian:
    * `01 - CURSO/` (`01 - Tablero de contenidos`, `02 - Tablero de contenidos curso 023`, `03 - Roadmap`).
    * `02 - MODULOS/` (`01 - Magerit`, `02 - ISO 27002`, `03 - Google Dorks`, `04 - Hacking Ético`, `05 - Prácticas (PoCs)`).
    * `03 - EXTRA/` (`01 - Enlaces favoritos`, `02 - Recursos Bug Bounty`, `03 - Retos de Ciberseguridad`, `04 - Conclusiones Finales`).
  * Creación de `00 - INDICE.md` e `Inicio.md` reproduciendo las tarjetas, descripciones y el panel de novedades.

---

## 📌 Paso 4: Configuración de Tableros Kanban
* Transformación de los tableros de contenido al formato oficial del plugin **Obsidian Kanban** (`kanban-plugin: basic`).
* Columnas horizontales por cada Unidad Formativa oficial (`MF0490_3`, `MF0486_3`, `MF0487_3`, `MF0489_3`, `MF0488_3`, etc.) con tarjetas arrastrables (*drag and drop*) y casillas `- [ ]`.
* Descarga e instalación directa del plugin de la comunidad `obsidian-kanban` (v2.0.51) dentro de `.obsidian/plugins/obsidian-kanban/` y activación en `community-plugins.json`.

---

## 📌 Paso 5: Descarga de Recursos y Archivos Pesados
* Creación de la carpeta dedicada `archivos_descargados/` para aislar los recursos binarios del cuerpo de notas:
  * **`documentos/`**: `simulacro.docx`, `riesgos.xlsx`, `msdos.pdf`, `linuxcommands.pdf`, `nmap_cs.pdf`, `lpi-material.pdf` (8.7 MB).
  * **`imagenes/`**: `certificaciones.jpg`, `linux.png`, `nmap.png`, `tmux.png`, `terminator.png`.
  * **`paquetes_7z/`**: Paquete Bug Bounty `bb_recursos_20260321.7z` y los 12 retos de laboratorio CTF (`bitlocker.7z`, `office.7z`, `creds_hunting.7z`, `lsass.7z`, `unshadow.7z`, `reverse.7z`, `usbsmith.7z`, `ftpzip.7z`, `wifi.7z`, `memoria.7z`, `evidencias.7z`, `infected.7z`).

---

## 📌 Paso 6: Transformación de Documentos Office y PDF a Markdown
* **Word (`simulacro.docx`):** Convertido a `02 - MODULOS/MAGERIT - Simulacro Clínica.md` (628 líneas con inventario de activos A1–A17, árbol de dependencias, matrices y caso práctico ClínicaSalud).
* **Excel (`riesgos.xlsx`):** Convertido a `02 - MODULOS/MAGERIT - Matriz de Análisis de Riesgos.md` (tabulación completa de hojas: Matriz, Clasificación, Amenazas INCIBE, Salvaguardas).
* **PDFs:** Convertidos a notas Markdown en `03 - EXTRA/Documentos/`:
  * `MS-DOS CheatSheet.md`
  * `Linux Comandos Esenciales.md`
  * `Nmap CheatSheet.md`
  * `Material Preparación LPIC.md`

---

## 📌 Paso 7: Desacoplamiento Offline y Gestión con Git
* Reemplazo de todos los enlaces que apuntaban al servidor remoto `cursosmfr.es` por rutas relativas locales y wikilinks.
* Configuración de `.gitignore` para excluir binarios pesados (`*.7z`) y mantener el repositorio optimizado.
* Inicialización del repositorio Git con commits estructurados por etapas.

---

## 📌 Paso 8: Desglose Exhaustivo de Controles ISO/IEC 27002:2022
* Extracción e importación integral de los contenidos detallados de `cursosmfr.es/ifct0109/iso27002/`:
  * Creación de la subcarpeta `02 - MODULOS/ISO 27002/`.
  * **`01 - Seguridad Física.md`**: 14 controles (7.1 a 7.14) con sus objetivos formativos, aspectos clave numerados, subsección de gestión y destrucción de soportes (7.10) y anclajes `^c71` a `^c714`.
  * **`02 - Seguridad Lógica.md`**: 20 controles de las cláusulas 5, 6 y 8 (RBAC, DAM/IRM, teletrabajo, MFA, UEBA, Security by Design, sincronización horaria, utilidades privilegiadas) y anclajes específicos.
  * **`03 - Robustecimiento de Sistemas y Redes.md`**: 15 controles tecnológicos (código malicioso, análisis de vulnerabilidades, CIS Benchmarks, borrado seguro, DLP, backups 3-2-1, alta disponibilidad, microsegmentación de redes, filtrado web y ciclo de vida de claves criptográficas).
* Actualización de la nota central `02 - MODULOS/02 - ISO 27002.md` con enlaces directos por control hacia sus anclajes respectivos.

---

## 📌 Paso 9: Subcarpeta Hacking Ético y Guías Integrales (Fuzzing y Reconocimiento)
* Descarga y conversión de los módulos técnicos de `cursosmfr.es/ifct0109/hacking/`:
  * Creación de la subcarpeta `02 - MODULOS/Hacking Ético/`.
  * **`01 - Web Fuzzing.md`**: Guía exhaustiva de fuzzing web (FFUF, Gobuster, Feroxbuster, cURL, Subfinder, API Fuzzer, SecLists), con comandos de instalación paso a paso, rutas de wordlists, fuzzing GET/POST/VHost, APIs (REST, SOAP, GraphQL) y tablas comparativas de matchers y filtros.
  * **`02 - Reconocimiento y Herramientas Bug Bounty.md`**: Arsenal completo de más de 30 herramientas de reconocimiento y enumeración (`httpx`, `WhatWeb`, `wafw00f`, `dnsx`, `puredns`, `katana`, `gau`, `gowitness`, `gf`, `nuclei`), scripts de automatización Bash (`virustotal.sh`, `lostfuzzer.sh`, `gf_critical_script.sh`), servicios de inteligencia y recursos online.
* Actualización de `02 - MODULOS/04 - Hacking Ético.md` como nodo central (*hub*) con accesos directos y llamadas temáticas.

---

## 📌 Paso 10: Integración de Prácticas PoC de MS-DOS y Conexiones de Red con Netcat
* Descarga y adaptación de los laboratorios alojados en `cursosmfr.es/ifct0109/pocs/msdos/`:
  * Descarga y aislamiento local de imágenes ilustrativas en `archivos_descargados/imagenes/msdos/` (`image1.png`, `image2.png`, `image3.png`, `image4.png`).
  * Descarga y almacenamiento del binario para Windows en `archivos_descargados/paquetes_7z/nc.7z` (contraseña `123456`).
  * Creación de la subcarpeta `02 - MODULOS/Prácticas (PoCs)/MS-DOS/`.
  * **`01 - Comandos de MS-DOS.md`**: Transcripción completa de los 24 ejercicios prácticos del entorno CMD (permisos de administrador, `help`, personalización con `color`, `shutdown` y cancelación, diagnóstico ICMP con `ping` continuo `-t`, variable `%PATH%` y vectores de Privilege Escalation / DLL Hijacking, navegación y filtros de búsqueda recursiva `dir /s`, manipulación de directorios, redirecciones de salida `>` y `>>`, creación por consola con `copy con infored.bat`, y análisis técnico del script por lotes interactivo con variables `set /p`).
  * **`02 - Conexiones Directas e Inversas con Netcat.md`**: Guía técnica detallada sobre fundamentos de sockets en red (Bind Shell vs Reverse Shell), bypass de firewalls y tablas NAT mediante conexiones inversas salientes, escenarios de chat, ejecución remota interactiva (`cmd.exe`/`/bin/bash`), transferencia y exfiltración de archivos con `<` y `>`, desglose de flags de Netcat, tabla comparativa técnica, contramedidas defensivas (EDR, NDR, egress filtering) y cláusula de uso ético.
* Actualización de notas vinculadas:
  * `02 - MODULOS/05 - Prácticas (PoCs).md`: Añadido bloque destacado de acceso directo al módulo MS-DOS y actualización de enlaces de la tabla.
  * `01 - CURSO/01 - Tablero de contenidos.md` y `01 - CURSO/02 - Tablero de contenidos curso 023.md`: Enlaces directos a las nuevas notas en las tarjetas Kanban.
  * `03 - EXTRA/Documentos/MS-DOS CheatSheet.md`: Enlaces cruzados hacia las prácticas interactivas correspondientes.

---

## 📌 Paso 11: Integración Integral de Todos los Módulos de Prácticas (PoCs)
* Extracción, adaptación y conversión integral de todos los módulos prácticos disponibles en `cursosmfr.es/ifct0109/pocs/`:
  * **Descarga de activos binarios offline:**
    * Paquete de herramientas y datasets de filtrado: `archivos_descargados/paquetes_7z/recursos_filtros.7z` (12 KB).
    * Documento técnico de resolución de Hades: `archivos_descargados/documentos/hades.pdf` (529 KB).
    * Paquete de recursos de Android Studio y APKs: `archivos_descargados/paquetes_7z/recursos_android.7z` (134 MB).
    * Logo oficial de Debian: `archivos_descargados/imagenes/debian-logo.png`.
  * **Módulo Linux (`02 - MODULOS/Prácticas (PoCs)/Linux/` - 13 notas):**
    * `00 - Índice del Módulo Linux.md`: Hub del módulo con accesos organizados.
    * `01 - Curso de Linux - Introducción y Entorno.md`: Historia de GNU/Linux, distros de hacking, arquitectura del kernel, entorno gráfico y guías completas de despliegue en WSL y Docker.
    * `02 - Curso de Linux - Estructura de Archivos (FHS).md`: Jerarquía de directorios estándar, `/bin`, `/etc`, `/proc`, `/sys`, `/var`, `/tmp` y particularidades de Kali Linux.
    * `03 - Curso de Linux - Comandos Esenciales de Sistema.md`: Navegación, manipulación, visualización, búsqueda, archivado con `tar` y paquetería `apt`/`dpkg`.
    * `04 - Curso de Linux - Gestión de Grupos y Sudoers.md`: Grupos del sistema, `/etc/group`, sintaxis avanzada de `/etc/sudoers`, auditoría con `sudo -l` y vectores de escalada.
    * `05 - Curso de Linux - Gestión de Usuarios y Autenticación.md`: `/etc/passwd`, `/etc/shadow`, algoritmos de hash, ciclo de vida de usuarios, cracking y persistencia.
    * `06 - Curso de Linux - Permisos, SUID, SGID y Sticky Bit.md`: Permisos rwx, notación octal, bits especiales de seguridad, auditoría con `find` y explotación GTFOBins.
    * `07 - Filtrado de Información en Linux.md`: Práctica completa con `grep`, `egrep`, Regex, `cut`, `tr`, `sort`, `uniq`, `awk` y dataset descargable.
    * `08 - Curso de Bash Scripting en Linux.md`: Scripting base, variables de entorno y locales, control de flujo y automatización para pentesting.
    * `09 - Linux Capabilities y Escalada de Privilegios.md`: Auditoría de capabilities (`getcap`, `setcap`, `capsh`), abuso de `CAP_NET_BIND_SERVICE`, `CAP_SETUID` y escalada de privilegios.
    * `10 - Instalación de Debian + DWM (Entorno Minimalista).md`: Compilación de DWM, `dmenu` y configuración de entorno minimalista.
    * `11 - Laboratorio VENUS (HackMyVM).md`: Writeup exhaustivo de las 54 misiones del reto Venus en HackMyVM.
    * `12 - Laboratorio HADES (HackMyVM).md`: Solucionario integral de las 50 fases del reto Hades (SSTV, audio, IRC, Gemini, Conky, QR, Wireshark y binarios SUID).
  * **Módulo Sistemas (`02 - MODULOS/Prácticas (PoCs)/Sistemas/` - 2 notas):**
    * `01 - Creación de NAS con OpenMediaVault.md`: Despliegue de NAS con OMV, volúmenes RAID, cuotas y recursos compartidos (SMB/NFS/FTP).
    * `02 - Active Directory en Windows Server 2019.md`: Controlador de Dominio, DNS, OUs, usuarios, grupos y directivas GPO de endurecimiento.
  * **Módulo Conceptos Fundamentales (`02 - MODULOS/Prácticas (PoCs)/Conceptos Fundamentales/` - 8 notas):**
    * SSH y túneles, cURL y peticiones HTTP, Firefox DevTools para inspección web, transferencia de archivos (`wget`, `scp`, `nc`), arquitectura web, ataques MITM con envenenamiento ARP, ingeniería social/phishing y generación de payloads con Msfvenom.
  * **Módulo Herramientas de Auditoría (`02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/` - 11 notas):**
    * Nmap, Responder (LLMNR/NBT-NS), Metasploit, Hashcat, Gobuster, Hydra, WiFi (Aircrack-ng), Wireshark, BloodHound, Pivoting de redes y análisis de Buffer Overflow.
  * **Módulo Encriptado de Datos (`02 - MODULOS/Prácticas (PoCs)/Encriptado de Datos/` - 6 notas):**
    * 7-Zip (AES-256), EncFS, BitLocker, VeraCrypt (volúmenes ocultos), Gpg4win (claves PGP/firmas) y Esteganografía forense.
  * **Módulo Firewall (`02 - MODULOS/Prácticas (PoCs)/Firewall/` - 7 notas):**
    * IPFire (instalación, zonas verde/roja/azul/naranja, reglas perimetrales, NAT, OpenVPN/IPsec, IDS Suricata, proxy Squid) y Firewall avanzado de Windows y Linux (Iptables/UFW).
  * **Módulo Pentesting en Android (`02 - MODULOS/Prácticas (PoCs)/Pentesting en Android/` - 7 notas):**
    * Android Studio, metodología de análisis dinámico y estático, Apktool, túneles Ngrok/Serveo, C2 L3MON, ofuscación Smali y APK Easy Tool.
* **Actualización del Catálogo y Tableros Kanban:**
  * Reestructuración de `02 - MODULOS/05 - Prácticas (PoCs).md` con matriz global y accesos directos temáticos.
  * Enlace de todas las tarjetas prácticas en `01 - CURSO/01 - Tablero de contenidos.md` y `01 - CURSO/02 - Tablero de contenidos curso 023.md`.

---

## 📌 Paso 12: Alineación Fiel 1:1 de la Guía de Web Fuzzing y Herramientas
* Comparación e inspección del contenido original en vivo en `https://www.cursosmfr.es/ifct0109/hacking/fuzzing/` y `https://www.cursosmfr.es/ifct0109/hacking/fuzzing/tools/index.html`:
  * **Reestructuración de `02 - MODULOS/Hacking Ético/01 - Web Fuzzing.md`:** Adaptación fiel al documento original, manteniendo el encabezado formal con metadatos de fase y descripción, el listado exacto de wordlists de SecLists (ficheros/directorios, parámetros, vhost, subdominios, extensiones, apis), comandos directos de fuzzing con `ffuf`, `gobuster`, `feroxbuster`, pruebas GET/POST, virtual hosts y APIs (REST, SOAP, GraphQL), y las tablas íntegras de matchers y filtros por herramienta.
  * **Creación de `02 - MODULOS/Hacking Ético/Herramientas de Web Fuzzing.md`:** Transcripción fiel del módulo de instalación y catálogo de herramientas (`tools/index.html`) para FFUF, Gobuster, Feroxbuster, cURL, Subfinder, API Fuzzer (`webfuzz_api`) y SecLists.
  * Vinculación bidireccional entre la guía principal, la ficha de herramientas y el nodo central `02 - MODULOS/04 - Hacking Ético.md`.







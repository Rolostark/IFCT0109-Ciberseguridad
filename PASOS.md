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

---

## 📌 Paso 13: Sincronización Integral 1:1 de PoCs, Descarga de Recursos y Desacoplamiento Completo de Laboratorios
* **Auditoría e Inspección Profunda de Todo el Portal (`cursosmfr.es/ifct0109`):**
  * Rastreo automatizado completo y descubrimiento de todas las páginas y submódulos prácticos oficiales que se encontraban tras enlaces de navegación (`ws2019_1` a `6`, `devtools` 0 a 9, `handler`, `retos`, `bloodhound` 1 a 4, `pivoting` 1 a 5, `bufferoverflow` 1 a 2).
* **Descarga Masiva y Centralizada de Medios Offline:**
  * **Más de 450 capturas técnicas e infografías** descargadas localmente en `archivos_descargados/imagenes/` organizadas por módulos (`sistemas/`, `conceptos/`, `encriptado/`, `firewall/`, `pentesting_en_android/`, `herramientas_de_auditoría/`).
  * **Paquetes pesados de laboratorio** descargados en `archivos_descargados/paquetes_7z/` (`docker_bloodhound.7z` ~100MB, `pivoting.7z` ~16MB, `BufferOverflow.7z` ~37MB, `recursos_android.7z` ~134MB, `recursos_filtros.7z`), cumpliendo las directivas de `.gitignore` para no sobrecargar el repositorio Git.
  * **Herramientas complementarias y binarios** descargados en `archivos_descargados/recursos/` (`SharpHound.exe`, `SharpHound.ps1`, `minirock.txt` y archivos de datos de Active Directory).
* **Desacoplamiento Modular 1:1 de Todos los Laboratorios:**
  * **Linux (`02 - MODULOS/Prácticas (PoCs)/Linux/` - 17 notas):** Reorganización completa en notas independientes: Instalación en WSL y Docker, Introducción y Arquitectura, Entorno Gráfico, FHS, Comandos Básicos, Grupos, Usuarios, Permisos, Filtrado de Información, Bash Scripting, Linux Capabilities, Debian + DWM, y writeups íntegros de Venus (54 misiones) y Hades (50 misiones).
  * **Sistemas (`02 - MODULOS/Prácticas (PoCs)/Sistemas/` - 8 notas):** Servidor NAS OMV y laboratorio de 6 partes de Active Directory 2019 (`02a` a `02f`: Red/IP fija, Roles AD DS/DNS, Promoción a DC/Bosque, OUs y Cuentas, Directivas GPO y Registro/Auditoría) con `02` como portal unificado.
  * **Conceptos Fundamentales (`02 - MODULOS/Prácticas (PoCs)/Conceptos Fundamentales/` - 18 notas):** Despliegue íntegro de Firefox DevTools Lab en 10 módulos (`03a` a `03j`: Introducción, Inspector HTML, Inspector CSS, Consola, Debugger, Red HTTP, Almacenamiento, Diseño adaptable, Accesibilidad y Rendimiento), junto a SSH, cURL, transferencia de archivos, HTTP, MITM, Phishing y Msfvenom.
  * **Herramientas de Auditoría (`02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/` - 24 notas):**
    * Nmap, Responder, Metasploit (+ Meterpreter `03a`), Hashcat, Gobuster, Hydra, WiFi y Wireshark (+ Retos prácticos `08a`).
    * BloodHound: 4 notas paso a paso (`09a` a `09d`: Instalación, Recolección con SharpHound, Análisis Cypher y Alternativas).
    * Pivoting: 5 notas paso a paso (`10a` a `10e`: Socat relays, Túnel SSH dinámico con Proxychains, Chisel inverso, Chisel + Socat en 4 saltos, y Ligolo-ng con interfaces TUN).
    * Buffer Overflow: 2 notas paso a paso (`11a` y `11b`: Identificación de fallo y Explotación con Shellcode).
* **Interconexión Total mediante Obsidian Wikilinks y Navegación Bidireccional:**
  * Reemplazo de todos los enlaces `.html` remotos o huérfanos por wikilinks nativos (`[[...]]`) de Obsidian que garantizan navegación fluida y funcionalidad 100% offline.
  * Actualización integral del catálogo matriz `02 - MODULOS/05 - Prácticas (PoCs).md`.

---

## 📌 Paso 14: Sincronización 1:1 de la Metodología Bug Bounty de Reconocimiento, Arsenal y Máquinas Hackeables
* **Autenticación con Cookie de Sesión Oficial (`PHPSESSID`):**
  * Acceso e inspección directa a `cursosmfr.es/ifct0109/hacking/recon/index.php`, `recon/tools/index.html`, `hacking/index.php` y `hacking/vms/index.php`.
* **Despliegue Fiel de la Subcarpeta `02 - MODULOS/Hacking Ético/`:**
  * **`02 - Reconocimiento Bug Bounty.md`:** Transcripción 1:1 de la metodología oficial en 13 fases operativas numeradas (`00_` a `13_`): Scope, información perimetral (`httpx`, `whatweb`, `wafw00f`, `cdncheck`), resolución DNS e IPInfo, Google Dorks, enumeración masiva pasiva y activa de subdominios (`subfinder`, `crt`, `shuffledns`, `alterx`), validación con `puredns`, protección WAF/CDN, escaneo de puertos y conversión con `naabu`/`nmap`/`nmapurls`, análisis web con `httpx`, capturas con `gowitness` y Chrome headless, recolección y deduplicación de URLs (`urlfinder`, `katana`, `urless`, `gau`), filtrado por vectores críticos con patrones `gf` y escaneo DAST con `nuclei`, y scripts bash automatizados.
  * **`Herramientas de Reconocimiento Bug Bounty.md`:** Catálogo oficial 1:1 de herramientas agrupadas por categorías técnicas (Análisis Web, DNS, Subdominios, Dorks, Puertos, Screenshots, Crawling, GF Patterns, Vulnerabilidades, Inteligencia OSINT, Scripts y Recursos Online).
  * **`03 - Máquinas Hackeables (VMs).md`:** Catálogo integral de máquinas de laboratorio para entrenamiento práctico: procedimiento previo de conexión en Red NAT con Kali Linux y descubrimiento con `netdiscover`, catálogo clasificado de máquinas Linux (Conceptos Básicos, Vulnerabilidades Web OWASP, Avanzadas), sistemas Windows Server y Active Directory (`dc01` a `dc03`, examen, big), y plataforma Dockerlabs con guía paso a paso de despliegue mediante scripts `auto_deploy.sh`.
* **Alineación del Hub Central y Navegación:**
  * Reestructuración de `02 - MODULOS/04 - Hacking Ético.md` interconectando las 3 secciones principales del módulo (Web Fuzzing, Reconocimiento Bug Bounty y Máquinas Hackeables).

---

## 📌 Paso 15: Integración de Guía de Laboratorio Thoth y Descarga Offline de Capturas de WS2008
* **Creación de la Guía de Máquina Thoth:**
  * Elaboración de `02 - MODULOS/Hacking Ético/Máquinas/Thoth.md` transcribiendo la guía oficial paso a paso: LHOST/RHOST, escaneo Nmap (puertos 21, 22, 80), acceso anónimo FTP y lectura de `note.txt`, ataque de diccionario con Hydra sobre FTP utilizando `rockyou.txt`, acceso SSH, captura de `user.txt`, enumeración con `sudo -l`, elevación con GTFOBins y lectura de `/root/root.txt`.
  * Vinculación bidireccional desde la matriz `02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs).md`.
* **Cosecha de Medios Offline para Máquinas Windows:**
  * Descarga e incorporación local de las **39 capturas de pantalla** oficiales de Windows Server 2008 en `archivos_descargados/imagenes/vms/ws2008_images/media/` (`image2.png` a `image40.png`).
* **Rastreo Completo de las 42 Máquinas Virtuales:**
  * Descarga y validación en local del contenido íntegro de las 42 páginas de laboratorio de máquinas vulnerables del servidor oficial (`cursosmfr.es/ifct0109/hacking/vms/`).

---

## 📌 Paso 16: Integración Completa de las 15 Máquinas de Conceptos Básicos en Obsidian
* **Generación y Despliegue de Guías de Laboratorio Individuales (`02 - MODULOS/Hacking Ético/Máquinas/`):**
  * Creación y transcripción íntegra 1:1 de los 15 entornos de Conceptos Básicos:
    1. **`Thoth.md`:** FTP anónimo, notas con usuarios, Hydra + Rockyou, acceso SSH y escalada con GTFOBins.
    2. **`mhz_c1f.md`:** Enumeración web con Gobuster, credenciales en ficheros, esteganografía con Steghide en imágenes de `/home/mhz_c1f/Paintings/`, pivoteo y `sudo su`.
    3. **`Looz.md`:** WordPress admin, credenciales en código fuente, fuerza bruta SSH con `ncrack`, y escalada con binario SUID en `/home/alatar/Private/shell_testv1.0`.
    4. **`Funbox.md`:** Servicios heredados y explotación directa.
    5. **`Friendly.md`:** Metodología y resolución estándar de reconocimiento, reverse shell y permisos débiles.
    6. **`Funbox7.md`:** Criptografía básica, crackeo de hashes y abuso de tareas programadas (cron).
    7. **`Connection.md`:** Puertos de gestión y filtrado de credenciales.
    8. **`Alzheimer.md`:** Mala configuración de servidor y escalada vertical.
    9. **`Hommie.md`:** Enumeración SMB/SSH y sanitización deficiente.
    10. **`MoneyBox.md`:** Fuerza bruta SSH, binarios SUID y captura de flags.
    11. **`OSBytesec.md`:** Vulnerabilidades web ligeras y bypass de autenticación.
    12. **`dpwwn.md`:** MySQL expuesto, inyección SQL básica y escalada por servicios.
    13. **`Hannah.md`:** Cabeceras HTTP, parámetros ocultos y elevación de privilegios.
    14. **`Pwned.md`:** Explotación web inicial, shell interactiva y permisos de fichero.
    15. **`Helium.md`:** Servicios de monitorización y elevación con binarios del sistema.
* **Actualización del Catálogo Central de VMs:**
  * Actualización de la tabla en `02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs).md` vinculando las 15 máquinas directamente con Obsidian Wikilinks escapados.

---

## 📌 Paso 17: Integración Total 1:1 de las 42 Máquinas Hackeables en Obsidian
* **Despliegue de los 4 Bloques Completos de Máquinas (`02 - MODULOS/Hacking Ético/Máquinas/`):**
  * **Vulnerabilidades Web (12 máquinas):** `Vulny.md`, `Evil box one.md`, `Uvalde.md`, `Inferno.md`, `Friendly2.md`, `Flower.md`, `Insomnia.md`, `Cengbox.md`, `Faust.md`, `Liceo.md`, `Nebula.md`, `Medusa.md`.
  * **Avanzadas (7 máquinas):** `Superhuman.md`, `Chromatica.md`, `w140.md`, `Aragog.md`, `Observer.md`, `Twister.md`, `dc09.md`.
  * **Windows Server Standalone (3 máquinas):** `ws2008.md` (integrando las 39 capturas de pantalla offline de explotación con EternalBlue y Meterpreter), `Liar.md`, `Simple.md`.
  * **Active Directory (5 laboratorios):** `dc01.md`, `dc02.md`, `dc03.md`, `Examen ad.md`, `BIG.md` (con flujos de Kerberoasting, BloodHound, Pass-the-Hash y volcado con secretsdump).
* **Interconexión Matrix y Enlaces Nativos:**
  * Actualización total de las tablas de `02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs).md` interconectando el 100% de las 42 máquinas del curso con Obsidian Wikilinks.

---

## 📌 Paso 18: Importación Integral de los Módulos Restantes de Prácticas (PoCs)
* **Descubrimiento y Auditoría de `pocs/index.php`:**
  * Inspección integral de los 11 módulos oficiales de la plataforma de Manuel FR, identificando 3 áreas técnicas no migradas previamente: **Vulnerabilidades Web**, **Escalada de Privilegios en Windows** y **Gestión de Incidentes de Seguridad**.
* **Descarga Offline Multihilo de Medios y Capturas (428 Imágenes):**
  * Descarga y verificación al 100% (428/428) de todas las capturas de pantalla y diagramas de las prácticas en sus rutas dedicadas:
    * `archivos_descargados/imagenes/vulnerabilidades_web/`
    * `archivos_descargados/imagenes/escalada_de_privilegios_en_windows/`
    * `archivos_descargados/imagenes/gestion_de_incidentes/`
* **Despliegue del Módulo: Vulnerabilidades Web (`02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/`):**
  * Creación del índice maestro `00 - Índice del Módulo Vulnerabilidades Web.md`.
  * **DVWA (10 Prácticas):** Command Injection, Brute Force, CSRF, Local File Inclusion (LFI), Insecure File Upload, XSS, SQL Injection (Fundamentos, Union-based, automatización con SQLMap, Blind SQLi boolean/time-based).
  * **VulnWeb Lab Docker (8 Módulos):** OS Command Injection, SSTI (Jinja2/Twig), LFI/RFI, CSRF, File Upload (bypass MIME/Magic Bytes), Cross-Site Scripting, SQL Injection y autenticación con Tokens JWT.
* **Despliegue del Módulo: Escalada de Privilegios en Windows (`02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/`):**
  * Creación del índice maestro `00 - Índice del Módulo Escalada de Privilegios en Windows.md`.
  * Generación del `Cheatsheet de Comandos de Escalada.md` con la sintaxis completa de ataque organizada por fases.
  * **17 Laboratorios Técnicos:** Enumeración y recopilación del sistema, explotación del kernel (WES-NG), servicios inseguros (Insecure Permissions, DLL Hijacking, Unquoted Service Paths, Weak Registry, Insecure Executables), registro de Windows (Autorun, AlwaysInstallElevated), extracción de credenciales (Registro, Credential Manager, Archivos de configuración, SAM/SYSTEM), aplicaciones de terceros, tareas programadas (`schtasks`), carpeta de inicio (`Startup`) y suplantación de privilegios con tokens (`SeImpersonatePrivilege` con Juicy Potato / God Potato).
* **Despliegue del Módulo: Gestión de Incidentes de Seguridad (`02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/`):**
  * Creación del índice maestro `00 - Índice del Módulo Gestión de Incidentes.md`.
  * **Análisis de Malware (3 Prácticas):** Análisis Dinámico (sandbox, Process Hacker, Regshot, Wireshark), Análisis Estático (hashes, PE-bear, FLOSS, Strings) e Ingeniería Inversa (desensamblado con Ghidra y depuración con x64dbg).
  * **SOC & MITRE ATT&CK (7 Prácticas):** Introducción al SOC y modelos de Tiers, Cyber Threat Intelligence (CTI, IOCs vs IOAs, TTPs, Pirámide del Dolor), MITRE ATT&CK Framework, ATT&CK Navigator (layers y cobertura defensiva), Operacionalización defensiva y Purple Teaming, MITRE D3FEND (contramedidas y hardening) y Caso de Estudio Real.
  * **SIEM y Wazuh (6 Prácticas):** Introducción a SIEM y arquitectura Wazuh, despliegue de appliance OVA y agentes, detección de vulnerabilidades CVE y evaluación SCA (CIS Benchmarks), detección de malware (FIM, YARA, VirusTotal, Sysmon), respuesta ante incidentes automatizada con Active Response, e integración de Wazuh como IDS con Suricata en Debian 12.
  * **Análisis Forense Digital (9 Prácticas):** Adquisición de memoria RAM (FTK Imager, DumpIt, RFC 3227) y disco duro (E01, metadatos, cadena de custodia), análisis de RAM con Volatility (`pstree`, `malfind`, credenciales), análisis de disco con FTK Imager (NIST DOMEX), análisis integral con Autopsy (palabras clave, timeline, artefactos), análisis con OSForensics, práctica evaluable HDD (20 preguntas), análisis forense de dispositivos móviles iPhone/iOS (bases de datos SQLite, PList), y práctica evaluable móvil (20 preguntas).
* **Interconexión Global del Vault:**
  * Actualización de la matriz y tarjetas de acceso en `02 - MODULOS/05 - Prácticas (PoCs).md` con enlaces directos hacia los 11 módulos completos del curso.

---

## 📌 Paso 19: Transformación del Repositorio de Enlaces a Tablero Kanban Interactivo
* **Conversión a Formato Oficial Obsidian Kanban:**
  * Transformación de `03 - EXTRA/01 - Enlaces favoritos.md` al formato `kanban-plugin: basic` con configuración `%% kanban:settings %%`.
  * Organización de los enlaces del portal oficial `cursosmfr.es/ifct0109/links/` en 9 columnas horizontales visuales:
    1. **Recursos y CheatSheets:** PDFs y cheatsheets locales (`msdos.pdf`, `lpi-material.pdf`, `linuxcommands.pdf`, `nmap_cs.pdf`, imágenes `tmux`, `terminator`, `linux.png`, `certificaciones.jpg`, retos y expresiones regulares).
    2. **SGSI y Normativa:** ISO 27002, ENS Navegable del CCN-CERT y Controles CIS.
    3. **Seguridad Informática y Auditoría:** Generadores de informes (SysReptor, Peng), herramientas forenses (NirSoft) y extensiones del navegador (FoxyProxy, Wappalyzer, HackTools, HackBar, User-Agent Switcher, Cookie-Editor).
    4. **Bug Bounty y Certificaciones:** 10 plataformas de Bug Bounty (YesWeHack, HackerOne, Intigriti, Bugcrowd, Open Bug Bounty, Zerod.io, Secur0, Immunefi, ZDI, Google Bug Hunters), repositorios de writeups e informes, y certificaciones oficiales (CompTIA, OffSec, EC-Council, eJPT, HTB, PortSwigger, Braindumps).
    5. **CTFs y Laboratorios:** 17 plataformas prácticas (PortSwigger Academy, HTB, TryHackMe, DockerLabs, HackViser, The Hackers Labs, HackMyVM, OverTheWire, RingZer0, AttackDefense, WebSploit, PentesterLab, OSBoxes, TJNull OSCP).
    6. **OSINT y Privacidad:** Marcos OSINT, Google Dorks, Shodan, Censys, PimEyes, tests de privacidad (PrivacyTests, EFF Fingerprinting, Tor), identidades sintéticas y temporales (YOPmail, Receive SMS, Fake Name, FaceApp, ThisPersonDoesNotExist), y servicios VPN (Mullvad, Riseup, Proton, TunnelBear, Hide.me, ExpressVPN, NordVPN, perfiles OpenVPN libres).
    7. **Inteligencia Artificial:** Asistentes (Claude, ChatGPT, Copilot, Gemini, Perplexity), detectores/humanizadores (QuillBot, Humbot), multimedia generativa (Suno, Raphael, Lumiere, Genmo, Sora) y entornos locales (Ollama, LM Studio, Pinokio, Hugging Face).
    8. **Academias, Cursos y Multimedia:** Cisco NetAcad (Networking, Linux, Hacking Ético), Full Stack Open, HackViser, PortSwigger, y simulacro en vídeo de ataque real.
    9. **Cuestionarios de Autoevaluación:** 25 formularios oficiales clasificados en Cuestionario Inicial, Gestión de Servicios MF0490_3 (7 tests), Controles ISO 27002 (8 tests) y Seguridad Digital (9 tests de Microsoft Forms).


---

## 📌 Paso 20: Reestructuración y Enriquecimiento de la Guía de Comandos Esenciales de Linux
* **Diagnóstico de Formato:**
  * Corrección de `03 - EXTRA/Documentos/Linux Comandos Esenciales.md`, el cual presentaba una importación defectuosa de más de 1.800 líneas donde cada palabra del PDF se encontraba fragmentada en una línea individual dentro de un único bloque de código Bash ilegible.
* **Transformación y Estructuración Fiel (+100 Comandos de `linuxcommands.pdf`):**
  * Transcripción y tabulación íntegra de los 125 comandos y operadores contenidos en el documento oficial de Waleed Mousa.
  * Creación de un **Índice Temático Rápido** con anclajes directos navegables.
  * Distribución en 19 tablas estructuradas con sintaxis de comando, descripción técnica en español y ejemplos prácticos:
    1. Operaciones con Archivos (22 comandos).
    2. Operaciones con Directorios (5 comandos).
    3. Gestión de Procesos y Redirecciones (10 comandos y descriptores `&>`, `1>`, `2>`).
    4. Permisos y Propietarios de Archivos (con callout explicativo de notación octal).
    5. Redes y Conectividad (13 comandos: `ping`, `dig`, `netstat`, `ss`, `curl`, `nmap`, etc.).
    6. Compresión y Empaquetado (`tar`, `gzip`, `zip`, `bzip2`).
    7. Procesamiento y Filtrado de Texto (`grep`, `sed`, `awk`, `cut`, `wc`, `diff`).
    8. Uso de Disco y Almacenamiento (`df`, `du`, `free`, `whereis`).
    9. Información del Sistema y Hardware (`uname`, `uptime`, `whoami`, etc.).
    10. Gestión de Paquetes APT (`update`, `upgrade`, `install`, `remove`).
    11. Operadores de Ejecución y Control de Flujo (`;`, `&&`, `||`, `&`).
    12. Control de Versiones con Git (19 comandos esenciales).
    13. Variables de Entorno y Alias (`env`, `export`, `$PATH`, `alias`).
    14. Programación de Tareas con Cron (con desglose de los 5 campos y directiva `@reboot`).
    15. Gestión de Paquetes Python con PIP (`freeze`, `install -r`).
    16. Fundamentos de Bash Scripting (Shebang, `$0..$N`, `if`, `for`, `while`, funciones).
    17. Monitorización del Sistema y Rendimiento (`iostat`, `vmstat`, `htop`).
    18. Búsqueda y Localización de Ficheros (`locate`, `whereis`, `which`).
    19. Rendimiento de Disco y Comandos de Prueba (`dd`, `hdparm`, `yes` y alerta de seguridad para *Fork Bomb*).
* **Interconexión con el Vault:**
  * Cabecera con migas de pan y enlaces cruzados hacia los 8 laboratorios temáticos de Linux en `02 - MODULOS/Prácticas (PoCs)/Linux/` y el manual de certificación LPIC-1.

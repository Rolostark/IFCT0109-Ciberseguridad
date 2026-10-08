---
title: "🐧 Módulo de Linux — Administración, Scripting y Retos CTF"
aliases:
  - Modulo Linux
  - PoCs Linux
  - Linux Hub
tags:
  - ifct0109/pocs
  - linux
  - administracion
  - pentesting
  - ctf
---

# 🐧 Módulo de Linux — Administración, Scripting y Retos de Hacking Ético

> [!abstract] Contenido y Objetivos del Módulo
> Este módulo proporciona una ruta de aprendizaje integral en **GNU/Linux orientada al Hacking Ético y la Seguridad Informática**: desde los fundamentos conceptuales y arquitectura de sistemas hasta la automatización en Bash, manipulación avanzada de expresiones regulares, auditoría de permisos/capabilities y resolución completa de retos prácticos en máquinas reales de CTF (**HackMyVM**).
> 
> 🔗 **Navegación:** [[00 - INDICE|🏠 Inicio]] &emsp;|&emsp; [[02 - MODULOS/05 - Prácticas (PoCs)|🧪 Catálogo de PoCs]] &emsp;|&emsp; [[01 - CURSO/02 - Tablero de contenidos curso 023|📋 Tablero Kanban]]

---

## 📚 1. Curso de Linux para Pentesting (Estructura Modular)

> [!info] 🏛️ [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux - Introducción y Entorno|Módulo 1: Introducción, Arquitectura y Entorno (WSL y Docker)]]
> *Historia de GNU/Linux, distribuciones de ciberseguridad (Kali, Parrot), arquitectura del kernel, CLI vs GUI, y despliegue rápido en Windows con WSL o contenedores Docker.*

> [!info] 📁 [[02 - MODULOS/Prácticas (PoCs)/Linux/02 - Curso de Linux - Estructura de Archivos (FHS)|Módulo 2: Estructura del Sistema de Archivos (FHS)]]
> *Jerarquía de directorios estándar FHS (`/bin`, `/etc`, `/proc`, `/var`, `/tmp`), estructura propia de Kali Linux y puntos de interés en post-explotación.*

> [!info] 💻 [[02 - MODULOS/Prácticas (PoCs)/Linux/03 - Curso de Linux - Comandos Esenciales de Sistema|Módulo 3: Comandos Esenciales de Sistema]]
> *Comandos de navegación (`ls`, `cd`, `pwd`), manipulación de archivos (`cp`, `mv`, `rm`), visualización (`cat`, `less`, `tail`), búsqueda (`find`, `grep`), archivado (`tar`) y paquetería `apt`.*

> [!info] 👥 [[02 - MODULOS/Prácticas (PoCs)/Linux/04 - Curso de Linux - Gestión de Grupos y Sudoers|Módulo 4: Gestión de Grupos y Archivo Sudoers]]
> *Grupos del sistema, `/etc/group`, configuración y sintaxis de `/etc/sudoers`, auditoría con `sudo -l` y vectores de escalada de privilegios.*

> [!info] 👤 [[02 - MODULOS/Prácticas (PoCs)/Linux/05 - Curso de Linux - Gestión de Usuarios y Autenticación|Módulo 5: Gestión de Usuarios y Autenticación]]
> *Archivos `/etc/passwd` y `/etc/shadow`, tipos de cuentas, creación y gestión (`useradd`, `passwd`), cracking de hashes y mecanismos de persistencia.*

> [!info] 🔐 [[02 - MODULOS/Prácticas (PoCs)/Linux/06 - Curso de Linux - Permisos, SUID, SGID y Sticky Bit|Módulo 6: Permisos, SUID, SGID y Sticky Bit]]
> *Permisos estándar (`rwx`), notación octal, bits especiales SUID y SGID, búsqueda de binarios vulnerables y explotación con GTFOBins.*

---

## 🛠️ 2. Prácticas Especializadas y Laboratorios Técnicos

> [!tip] 🔍 [[02 - MODULOS/Prácticas (PoCs)/Linux/07 - Filtrado de Información en Linux|Práctica: Filtrado de Información en Linux (Grep, Awk, Sed, Regex)]]
> *Procesamiento y extracción forense de texto con `grep`, `egrep`, `cut`, `tr`, `sort`, `uniq` y redirecciones sobre logs del sistema. Incluye dataset offline.*

> [!tip] 📜 [[02 - MODULOS/Prácticas (PoCs)/Linux/08 - Curso de Bash Scripting en Linux|Práctica: Curso de Bash Scripting para Automatización]]
> *Desarrollo de scripts defensivos y ofensivos: variables locales, de entorno y permanentes, condicionales, bucles y automatización de reconocimiento.*

> [!tip] 💉 [[02 - MODULOS/Prácticas (PoCs)/Linux/09 - Linux Capabilities y Escalada de Privilegios|Práctica: Linux Capabilities y Escalada de Privilegios]]
> *Desglose y auditoría de Linux Capabilities (`getcap`, `setcap`, `capsh`), abuso de privilegios granulares (`CAP_NET_BIND_SERVICE`, `CAP_SETUID`).*

> [!tip] 🐧 [[02 - MODULOS/Prácticas (PoCs)/Linux/10 - Instalación de Debian + DWM (Entorno Minimalista)|Práctica: Instalación de Debian + DWM]]
> *Construcción paso a paso de una estación de trabajo de alto rendimiento basada en Debian y el gestor de ventanas DWM de Suckless.*

---

## 🏆 3. Máquinas Virtuales y Retos de Laboratorio (HackMyVM)

> [!example] 🪐 [[02 - MODULOS/Prácticas (PoCs)/Linux/11 - Laboratorio VENUS (HackMyVM)|Reto VENUS (HackMyVM) — Walkthrough de 54 Misiones]]
> *Laboratorio progresivo de resolución de misiones: filtrado de ficheros, permisos, bypass de entornos y escalada progresiva de usuarios.*

> [!example] 🔥 [[02 - MODULOS/Prácticas (PoCs)/Linux/12 - Laboratorio HADES (HackMyVM)|Reto HADES (HackMyVM) — Solucionario de 50 Misiones]]
> *Walkthrough técnico completo de las 50 fases de la máquina Hades: esteganografía, SSTV, IRC, sockets, Keepass, GTFOBins y capturas de red.*

---

## 📄 Recursos Offline de Referencia
* 📑 [[03 - EXTRA/Documentos/Linux Comandos Esenciales|Linux Comandos Esenciales CheatSheet]]
* 🎓 [[03 - EXTRA/Documentos/Material Preparación LPIC|Material Oficial de Preparación LPIC-1]]
* 📦 Recursos de filtrado: `archivos_descargados/paquetes_7z/recursos_filtros.7z`
* 📑 Solucionario Hades: [📥 hades.pdf](../../../archivos_descargados/documentos/hades.pdf)

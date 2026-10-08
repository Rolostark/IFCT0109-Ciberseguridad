---
title: "🧪 Hacking Ético — Laboratorios y Tutoriales"
aliases:
  - Hacking Ético
  - Laboratorios Ofensivos
tags:
  - hacking-etico
  - fuzzing
  - recon
  - vms
  - laboratorios
  - bug-bounty
  - ifct0109
---

# 🧪 Hacking Ético — Tutoriales y Máquinas Hackeables

> [!abstract] Contenido del Módulo
> Espacio dedicado a la formación ofensiva práctica: metodologías de reconocimiento en auditorías y Bug Bounty, técnicas avanzadas de enumeración y fuzzing web, y máquinas virtuales de entrenamiento.
> 
> 🔗 **Navegación:** [[00 - INDICE|🏠 Inicio]] &emsp;|&emsp; [[01 - CURSO/01 - Tablero de contenidos|🗂️ Tablero de Contenidos]] &emsp;|&emsp; [[03 - EXTRA/02 - Recursos Bug Bounty|🐛 Recursos Bug Bounty]]

---

## 📚 Tutoriales y Guías Detalladas (Acceso Rápido)

> [!info] 🔍 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|1. Web Fuzzing (Herramientas, Comandos, APIs y Filtros)]]
> *Guía completa de enumeración de directorios, archivos, parámetros GET/POST, VHosts, APIs REST/SOAP/GraphQL con `ffuf`, `gobuster`, `feroxbuster` y diccionarios SecLists.*

> [!info] 🎯 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento y Herramientas Bug Bounty|2. Reconocimiento y Arsenal de Herramientas Bug Bounty]]
> *Flujo de reconocimiento completo con más de 30 herramientas: `subfinder`, `httpx`, `dnsx`, `gowitness`, `katana`, `nuclei`, scripts Bash personalizados y patrones GF.*

---

## 🌐 1. Resumen de Áreas Técnicas

### 🔍 Enumeración Web (Fuzzing)
* **Herramientas principales:** `ffuf`, `gobuster`, `feroxbuster`, `cURL` y `webfuzz_api`.
* **Diccionarios clave:** Colección completa de [SecLists](https://github.com/danielmiessler/SecLists).
* **Técnicas cubiertas:** Fuzzing recursivo, parámetros ocultos, virtual hosts y esquemas GraphQL.
* 📖 **Ver manual completo:** [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Acceso al Tutorial Detallado de Web Fuzzing]]

### 🎯 Reconocimiento Bug Bounty Hunter
* **Metodología estructurada:** Recolección pasiva (OSINT) y activa (DNS, puertos, crawling).
* **Arsenal:** `subfinder`, `httpx`, `naabu`, `gowitness`, `katana`, `gau`, `gf` y `nuclei`.
* **Automatización:** Scripts integrados (`virustotal.sh`, `lostfuzzer.sh`, `gf_critical_script.sh`).
* 📖 **Ver arsenal completo:** [[02 - MODULOS/Hacking Ético/02 - Reconocimiento y Herramientas Bug Bounty|Acceso al Arsenal de Reconocimiento]]

---

## 💻 2. Máquinas Hackeables (Entornos de Práctica)

* **Diversas Máquinas Virtuales (VMs):**
  * Laboratorios descargables en formatos `.ova` / `.vmdk` para VirtualBox o VMware.
  * Simulación de vulnerabilidades reales: subida de archivos arbitraria, inyecciones SQL, RCE y escalada de privilegios local.

---

> [!tip] Retos Relacionados
> Pon a prueba tus capacidades ofensivas y analíticas en los [[03 - EXTRA/03 - Retos de Ciberseguridad|12 Retos Técnicos de Ciberseguridad (CTFs)]].

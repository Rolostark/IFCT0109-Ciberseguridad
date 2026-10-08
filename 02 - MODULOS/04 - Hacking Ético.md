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

> [!info] 🔍 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|1. Guía de Web Fuzzing - Bug Bounty]]
> *Guía completa y fiel de técnicas de fuzzing: Wordlists de SecLists, enumeración de directorios/archivos, recursive fuzzing, GET/POST fuzzing, VHosts, Web APIs (REST, SOAP, GraphQL) y tablas comparativas de filtros y matchers (`ffuf`, `gobuster`, `feroxbuster`).*
> &emsp;↳ 🛠️ [[02 - MODULOS/Hacking Ético/Herramientas de Web Fuzzing|Herramientas de Web Fuzzing (Catálogo y Usos)]]

> [!info] 🎯 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|2. Metodología de Reconocimiento Bug Bounty]]
> *Flujo de reconocimiento completo en 13 pasos con comandos reales (`00_` a `13_`): `httpx`, `dnsx`, `puredns`, `katana`, `gowitness`, `gf`, `nuclei`, scripts Bash y recursos OSINT.*
> &emsp;↳ 🛠️ [[02 - MODULOS/Hacking Ético/Herramientas de Reconocimiento Bug Bounty|Herramientas de Reconocimiento Bug Bounty (Catálogo y Usos)]]

> [!info] 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|3. Máquinas Hackeables (VMs y Dockerlabs)]]
> *Entornos prácticos con escenarios de vulnerabilidad reales: Linux (básicos, web, avanzadas), Windows Server y Active Directory, y contenedores Dockerlabs con guías de despliegue.*

---

## 🌐 1. Resumen de Áreas Técnicas

### 🔍 Enumeración Web (Fuzzing)
* **Herramientas principales:** `ffuf`, `gobuster`, `feroxbuster`, `cURL` y `webfuzz_api`.
* **Diccionarios clave:** Colección completa de [SecLists](https://github.com/danielmiessler/SecLists).
* **Técnicas cubiertas:** Fuzzing recursivo, parámetros ocultos, virtual hosts y esquemas GraphQL.
* 📖 **Ver manual completo:** [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Acceso al Tutorial Detallado de Web Fuzzing]] &emsp;|&emsp; 🛠️ [[02 - MODULOS/Hacking Ético/Herramientas de Web Fuzzing|Catálogo de Herramientas]]

### 🎯 Reconocimiento Bug Bounty Hunter
* **Metodología estructurada:** Recolección pasiva (OSINT) y activa (DNS, puertos, crawling).
* **Arsenal:** `subfinder`, `httpx`, `naabu`, `gowitness`, `katana`, `gau`, `gf` y `nuclei`.
* **Automatización:** Scripts integrados (`virustotal.sh`, `lostfuzzer.sh`, `gf_critical_script.sh`).
* 📖 **Ver metodología completa:** [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Acceso a la Metodología de Reconocimiento]] &emsp;|&emsp; 🛠️ [[02 - MODULOS/Hacking Ético/Herramientas de Reconocimiento Bug Bounty|Catálogo de Herramientas]]

---

## 💻 2. Máquinas Hackeables (Entornos de Práctica)

* **Diversas Máquinas Virtuales (VMs) y Dockerlabs:**
  * Escenarios en entornos Linux (básicos, web y avanzados), Windows Server y Active Directory (`dc01` a `dc03`, examen, big).
  * Contenedores ultraligeros de Dockerlabs (`dockerlabs.es`) con scripts `auto_deploy.sh`.
* 📖 **Ver catálogo de máquinas:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Acceso a las Máquinas Hackeables y Guía Docker]]

---

> [!tip] Retos Relacionados
> Pon a prueba tus capacidades ofensivas y analíticas en los [[03 - EXTRA/03 - Retos de Ciberseguridad|12 Retos Técnicos de Ciberseguridad (CTFs)]].

---
title: "🐛 Recursos Bug Bounty — Paquete de Herramientas y Wordlists"
aliases:
  - Recursos Bug Bounty
  - Bug Bounty Tools
  - WebShells y Payloads
tags:
  - bugbounty
  - payloads
  - webshells
  - wordlists
  - polyglots
---

# 🐛 Recursos Bug Bounty — Herramientas, Payloads y Wordlists

> [!abstract] Paquete de Utilidades Ofensivas
> Conjunto seleccionado de recursos técnicos para pruebas de penetración web y programas de Bug Bounty: webshells ligeras, archivos políglotas para elusión de filtros de subida, vectores de inyección y diccionarios de fuzzing.

> [!example] Descarga del Paquete Completo
> * **Archivo:** [📦 bb_recursos_20260321.7z](../archivos_descargados/paquetes_7z/bb_recursos_20260321.7z) *(Formato comprimido .7z)*  
> * **Contenido:** Colección organizada de scripts, payloads verificados y listas de palabras.

---

## 📂 Desglose del Contenido del Paquete

### 🐚 1. `files/shells` — WebShells Listas para Uso
Shells diseñadas para distintos entornos de ejecución en servidores web comprometidos:
* **Entornos PHP:**
  * Shell básica de ejecución de comandos (`system`, `passthru`, `exec`).
  * WebShell avanzada **Powny** (interfaz interactiva completa en un único archivo PHP).
  * Reverse Shell de **PentestMonkey** (conexión inversa TCP hacia handler Netcat).
* **Entornos ASP / JSP:**
  * Shells ligeras en formato clásico ASP y JSP para servidores Windows IIS y Apache Tomcat.
  * WebShells con navegación por el sistema de ficheros.
* **Bypasses de Servidor Web:**
  * Ficheros `.htaccess` personalizados para forzar la interpretación de extensiones de imagen (`.jpg`, `.png`) como código PHP ejecutable.

---

### 🖼️ 2. `files/FileUpload` — Bypass de Filtros de Subida
Recursos orientados a auditar controles de subida de archivos (*File Upload Restrictions*):
* **`polyglots/` — Archivos Políglotas:**
  * Imágenes válidas JPEG/PNG/GIF con código PHP válido incrustado en los metadatos o cabeceras (superan validaciones `getimagesize()` y magic bytes).
  * Políglotas preparados para ejecución directa: lectura de archivos (`cat`), ejecución de comandos (`cmd`), reverse shells y scripts SUID/SSH.
* **`vuln/` — Payloads Ocultos en Imágenes:**
  * Imágenes con payloads incrustados de **Cross-Site Scripting (XSS)** y Blind XSS.
  * Inyección de comandos del sistema operativo (*Command Injection*).
  * Robo de cookies de sesión (*Cookie Hijacking*).
  * Inyección de código HTML (*HTMLi*) e **XML External Entity (XXE)** mediante metadatos SVG/XML.
* **Imágenes Base:**
  * Plantillas limpias en PNG y JPG para reconstruir payloads personalizados.

---

### 📑 3. `wordlists/` — Diccionarios Especializados
Listas optimizadas para herramientas de fuzzing (`ffuf`, `gobuster`, `feroxbuster`):
* `sqli.txt` — Payloads de evasión para SQL Injection (autenticación bypass, union-based, time-based).
* `idor.txt` — Identificadores numéricos, UUIDs y secuencias para auditar Insecure Direct Object References.
* `sensitive_files.txt` — Detección de copias de seguridad (`.bak`, `.old`, `.swp`), ficheros `.git`, `.env`, `docker-compose.yml` y volcados de bases de datos.
* `ssti.txt` — Expresiones de inyección en motores de plantillas (Jinja2, Twig, Freemarker, Smarty).
* `subdomains.txt` — Diccionario de subdominios comunes para descubrimiento de superficie de ataque.

---

> [!caution] Advertencia de Seguridad
> Estos recursos deben emplearse exclusivamente en sistemas propios o en activos dentro del alcance explícito (*scope*) de un programa de Bug Bounty autorizado.

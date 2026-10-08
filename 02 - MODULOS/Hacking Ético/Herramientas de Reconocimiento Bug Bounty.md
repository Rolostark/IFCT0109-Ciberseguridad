---
title: "🛠️ Herramientas de Reconocimiento — Bug Bounty"
aliases:
  - Herramientas de Reconocimiento Bug Bounty
  - Herramientas Recon Bug Bounty
  - Tools Recon
  - Herramientas Recon
tags:
  - ifct0109
  - hacking-etico
  - recon
  - herramientas
  - bug-bounty
---

# 🛠️ Herramientas - Reconocimiento Bug Bounty

> [!abstract] Información de Herramientas
> **Categoría:** Herramientas para Reconocimiento y Enumeración  
> **Descripción:** Listado completo de herramientas utilizadas en la fase de reconocimiento para Bug Bounty.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|🔍 Volver a la Metodología de Reconocimiento]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|⬅️ Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## 🌐 Análisis Web y Tecnologías

### `httpx`
Herramienta rápida y multiuso para sondear servicios HTTP.

> [!info] Usos
> * Detección de códigos de estado HTTP
> * Identificación de CDN
> * Detección de tecnologías web
> * Análisis de headers y servidores web
> * Validación masiva de dominios activos

---

### `WhatWeb`
Herramienta de fingerprinting web que identifica tecnologías utilizadas en sitios web.

> [!info] Usos
> * Identificación de CMS, frameworks y plugins
> * Detección de versiones de software
> * Análisis de tecnologías del lado del servidor
> * Generación de informes detallados y resumidos

---

### `wafw00f`
Herramienta de identificación de Web Application Firewalls (WAF).

> [!info] Usos
> * Detección de WAF activos
> * Identificación del tipo y fabricante de WAF
> * Análisis de protecciones en aplicaciones web

---

### `cdncheck`
Herramienta para verificar si un dominio o IP está detrás de un CDN.

> [!info] Usos
> * Identificación de CDN
> * Detección de WAF via CDN
> * Análisis de infraestructura de red

---

## 🔍 DNS y Resolución

### `dnsx`
Herramienta rápida y multiuso para consultas DNS.

> [!info] Usos
> * Resolución de dominios a IPs
> * Consultas de registros DNS (A, AAAA, CNAME, MX, etc.)
> * Reconocimiento DNS masivo
> * Extracción de información DNS en formato JSON

---

### `puredns`
Herramienta de resolución DNS rápida y precisa con soporte para bruteforce.

> [!info] Usos
> * Validación masiva de subdominios
> * Bruteforce de subdominios
> * Uso de resolvers confiables
> * Filtrado de subdominios activos

---

## 🎯 Enumeración de Subdominios

### `subfinder`
Herramienta de descubrimiento pasivo de subdominios.

> [!info] Usos
> * Enumeración pasiva de subdominios
> * Uso de múltiples fuentes públicas
> * Descubrimiento rápido y silencioso

---

### `crt` (crt.sh CLI)
Cliente de línea de comandos para consultar el servicio crt.sh de Certificate Transparency.

> [!info] Usos
> * Búsqueda de subdominios en certificados SSL/TLS
> * Análisis de Certificate Transparency Logs
> * Descubrimiento de subdominios históricos

---

### `shuffledns`
Herramienta de bruteforce DNS masivo y rápido.

> [!info] Usos
> * Bruteforce activo de subdominios
> * Uso de múltiples resolvers DNS
> * Validación de subdominios con wildcards

---

### `alterx`
Generador de subdominios basado en patrones y permutaciones.

> [!info] Usos
> * Generación de variaciones de subdominios
> * Creación de wordlists personalizadas
> * Permutaciones basadas en patrones comunes

---

## 🔎 Google Dorks

### `dorks_hunter`
Herramienta automatizada para generar y ejecutar Google Dorks.

> [!info] Usos
> * Generación automática de dorks específicos por dominio
> * Búsqueda de información sensible expuesta
> * Descubrimiento de archivos y directorios
> * *Ver módulo detallado:* [[02 - MODULOS/03 - Google Dorks|Google Dorks para Bug Bounty]]

---

## 🔌 Escaneo de Puertos

### `naabu`
Escáner de puertos rápido escrito en Go.

> [!info] Usos
> * Escaneo rápido de puertos
> * Identificación de servicios activos
> * Escaneo de top ports

---

### `nmap`
Herramienta de escaneo de red y auditoría de seguridad.

> [!info] Usos
> * Escaneo detallado de puertos
> * Detección de versiones de servicios
> * Identificación de sistemas operativos
> * Generación de informes en XML

---

### `nmapurls`
Herramienta para extraer URLs de archivos XML de nmap.

> [!info] Usos
> * Extracción de endpoints HTTP/HTTPS
> * Conversión de resultados nmap a URLs
> * Generación de listas de objetivos web

---

## 📸 Capturas y Visualización

### `gowitness`
Herramienta para capturar screenshots de sitios web.

> [!info] Usos
> * Capturas masivas de pantallas web
> * Almacenamiento en base de datos SQLite
> * Generación de reportes visuales
> * Análisis rápido de múltiples aplicaciones web

---

## 🔗 Recolección de URLs

### `urlfinder`
Herramienta para recolección pasiva de URLs.

> [!info] Usos
> * Descubrimiento pasivo de URLs
> * Recolección desde múltiples fuentes
> * Extracción de endpoints históricos

---

### `katana`
Crawler web rápido y configurable.

> [!info] Usos
> * Crawling activo de sitios web
> * Extracción de JavaScript y contenido dinámico
> * Descubrimiento de endpoints y parámetros
> * Control de profundidad de crawling

---

### `gau` (Get All URLs)
Herramienta para obtener URLs conocidas de múltiples fuentes.

> [!info] Usos
> * Recolección de URLs desde Wayback Machine
> * Extracción desde Common Crawl
> * Obtención de URLs desde OTX AlienVault

---

### `urless`
Herramienta para deduplicar y limpiar listas de URLs.

> [!info] Usos
> * Eliminación de URLs duplicadas
> * Normalización de URLs
> * Limpieza de listas grandes

---

### `waybackurls`
Obtiene URLs de Wayback Machine para un dominio.

> [!info] Usos
> * Extracción de URLs históricas
> * Descubrimiento de endpoints antiguos
> * Análisis de evolución del sitio web

---

### `ffuf`
Fuzzer web rápido escrito en Go.

> [!info] Usos
> * Fuzzing de parámetros y directorios
> * Descubrimiento de contenido oculto
> * Búsqueda de archivos sensibles
> * *Ver tutorial completo:* [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]

---

## 🎯 Análisis de URLs y Patrones

### `gf` (grep filters)
Herramienta de filtrado de URLs basada en patrones predefinidos.

> [!info] Usos
> * Filtrado de URLs con patrones de SQLi, XSS, RCE, LFI
> * Identificación de parámetros interesantes
> * Búsqueda de endpoints sensibles
> * Detección de posibles vectores de ataque
> 
> **Patrones disponibles:** `sqli`, `xss`, `rce`, `lfi`, `idor`, `redirect`, `ssrf`, `secrets`, `api-keys`, `jwt`, `debug-pages`, `s3-buckets`, `cors`, `takeovers`

---

### `uro`
Herramienta para reducir URLs basándose en similitudes.

> [!info] Usos
> * Deduplicación inteligente de URLs
> * Reducción de ruido en listas grandes
> * Optimización de tiempo en análisis

---

## 🔬 Análisis de Vulnerabilidades

### `nuclei`
Escáner de vulnerabilidades rápido basado en plantillas.

> [!info] Usos
> * Escaneo automatizado de vulnerabilidades
> * Detección de misconfigurations
> * Análisis DAST (Dynamic Application Security Testing)
> * Uso de miles de plantillas de la comunidad
> * Detección de exposiciones y debilidades comunes

---

## 🔍 Servicios de Inteligencia

### `curl`
Herramienta de línea de comandos para transferir datos con URLs.

> [!info] Usos
> * Consultas a APIs (`ipinfo.io`)
> * Verificación de servicios web
> * Análisis de respuestas HTTP

---

### `jq`
Procesador de JSON en línea de comandos.

> [!info] Usos
> * Parseo de respuestas JSON
> * Extracción de datos específicos
> * Formato y filtrado de salidas

---

## 📜 Scripts Personalizados

### `virustotal.sh`
Script para consultar información de IPs en VirusTotal.
* **Usos:** Verificación de reputación de IPs, detección de amenazas conocidas y análisis de seguridad de hosts.

### `wayback.sh`
Script para automatizar consultas a Wayback Machine.
* **Usos:** Extracción automatizada de URLs históricas, análisis de cambios en sitios web y descubrimiento de contenido antiguo.

### `alienvault.sh`
Script para consultar AlienVault OTX.
* **Usos:** Recolección de URLs desde OTX, obtención de indicadores de compromiso (IoCs) e inteligencia de amenazas.

### `lostfuzzer.sh`
Script automatizado de fuzzing y análisis.
* **Usos:** Fuzzing automatizado de dominios, análisis con nuclei integrado y generación de reportes de vulnerabilidades.

### `gf_critical_script.sh`
Script para filtrar patrones críticos con gf.
* **Usos:** Filtrado de vectores de ataque críticos, búsqueda de secretos y credenciales, e identificación rápida de vulnerabilidades de alta prioridad.
* **Patrones críticos:** `sqli`, `sqli-error`, `xss`, `ssrf`, `rce`, `rce-2`, `lfi`, `xxe`, `ssti`, `idor`, `redirect`, `jwt`, `secrets`, `api-keys`, `aws-keys`, `github`, `firebase`, `debug-pages`, `js-sinks`, `domxss`, `s3-buckets`, `cors`, `takeovers`, `upload-fields`.

### `gf_all_script.sh`
Script para ejecutar todos los filtros gf disponibles.
* **Usos:** Análisis exhaustivo con todos los patrones gf, cobertura completa de vectores de ataque y generación de informes comprehensivos.

---

## 🌍 Recursos Online

* **[ViewDNS.info](https://viewdns.info/):** Suite de herramientas DNS online (Reverse IP Lookup, información de hosting compartido, análisis de infraestructura DNS).
* **[Bug Bounty Dorks (Karthik)](https://karthikdude.github.io/bug-bounty-dorks/):** Colección curada de Google Dorks para Bug Bounty (búsqueda de información sensible, archivos expuestos y paneles de administración).
* **[Subdomain Finder (c99.nl)](https://subdomainfinder.c99.nl/):** Herramienta online para encontrar subdominios (enumeración rápida, verificación cruzada y análisis sin instalación).

---

## 📦 Dependencias y Utilidades

* **Google Chrome:** Navegador web necesario como backend para `gowitness`, renderizado web y capturas de pantalla automatizadas.
* **Python HTTP Server:** Servidor web simple incluido en Python (`python3 -m http.server 8000`) para visualización de reportes de `gowitness` y compartir archivos temporalmente.
* **`wget`:** Herramienta de descarga de archivos desde la web (wordlists, listas de resolvers DNS de Trickest, etc.).

---

> [!note] 📝 Nota Oficial
> Esta colección de herramientas cubre todo el proceso de reconocimiento en Bug Bounty, desde la enumeración inicial hasta el análisis avanzado de vulnerabilidades. Cada herramienta debe utilizarse dentro del scope autorizado del programa.

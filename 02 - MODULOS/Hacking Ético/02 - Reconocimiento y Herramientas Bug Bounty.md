---
title: "Metodología de Reconocimiento y Herramientas — Bug Bounty Hunter"
aliases:
  - Reconocimiento
  - Recon Bug Bounty
  - Herramientas Recon
  - Recon
  - bug bounty recon
tags:
  - hacking-etico
  - recon
  - bug-bounty
  - subfinder
  - httpx
  - nuclei
  - katana
  - dns
  - ifct0109
---

# 🎯 Reconocimiento y Arsenal de Herramientas — Bug Bounty

> [!abstract] Fase: Reconocimiento y Enumeración de Superficie de Ataque
> Guía completa del arsenal de herramientas, scripts y flujos de trabajo empleados durante la fase de reconocimiento pasivo y activo en programas de Bug Bounty y auditorías de seguridad.
> 
> 🔗 **Navegación:** [[02 - MODULOS/04 - Hacking Ético|⬅️ Volver a Hacking Ético]] &emsp;|&emsp; [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|🔍 Ir a Web Fuzzing]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## 📑 Categorías del Arsenal

* [[#1. Análisis Web y Detección de Tecnologías|1. Análisis Web y Detección de Tecnologías]]
* [[#2. DNS y Resolución Masiva|2. DNS y Resolución Masiva]]
* [[#3. Enumeración y Permutación de Subdominios|3. Enumeración y Permutación de Subdominios]]
* [[#4. Google Dorks Automatizados|4. Google Dorks Automatizados]]
* [[#5. Escaneo de Puertos y Conversión a URLs|5. Escaneo de Puertos y Conversión a URLs]]
* [[#6. Capturas de Pantalla y Reconocimiento Visual|6. Capturas de Pantalla y Reconocimiento Visual]]
* [[#7. Recolección y Crawling de URLs|7. Recolección y Crawling de URLs]]
* [[#8. Filtrado de URLs y Búsqueda de Vectores (GF / URO)|8. Filtrado de URLs y Búsqueda de Vectores (GF / URO)]]
* [[#9. Escaneo Automatizado de Vulnerabilidades (Nuclei)|9. Escaneo Automatizado de Vulnerabilidades (Nuclei)]]
* [[#10. Servicios de Inteligencia y Procesamiento JSON|10. Servicios de Inteligencia y Procesamiento JSON]]
* [[#11. Scripts Automatizados de Reconocimiento|11. Scripts Automatizados de Reconocimiento]]
* [[#12. Recursos y Plataformas Online|12. Recursos y Plataformas Online]]
* [[#13. Dependencias de Sistema|13. Dependencias de Sistema]]

---

## 1. Análisis Web y Detección de Tecnologías

### `httpx` (ProjectDiscovery)
* **Descripción:** Sondeador HTTP rápido y multifuncional diseñado para procesar miles de hosts concurrentemente.
* **Usos:**
  * Comprobación masiva de dominios vivos con códigos de estado (`-status-code`).
  * Detección de tecnologías web y servidores (`-tech-detect`).
  * Identificación de CDNs y firewalls perimetrales (`-cdn`).
  * Extracción de títulos web (`-title`), web server headers y hashes de favicon.

### `WhatWeb`
* **Descripción:** Herramienta de *fingerprinting* de aplicaciones web de última generación.
* **Usos:**
  * Reconocimiento de CMS (WordPress, Joomla, Drupal), frameworks (React, Laravel, Django) y plugins instalados.
  * Extracción precisa de números de versión para búsqueda de CVEs.
  * Análisis de tecnologías subyacentes del servidor (Apache, Nginx, IIS, versiones de PHP/Node).

### `wafw00f`
* **Descripción:** Identificador de Web Application Firewalls (WAF).
* **Usos:**
  * Detectar presencia de cortafuegos de aplicación activos.
  * Identificar fabricante (Cloudflare, AWS WAF, Akamai, Imperva, ModSecurity, F5).
  * Adaptar las técnicas de evasión antes de lanzar escaneos agresivos.

### `cdncheck`
* **Descripción:** Comprobador de rangos IP y dominios para determinar si están protegidos tras un CDN.
* **Usos:**
  * Identificar infraestructura de CDN (Cloudflare, CloudFront, Fastly).
  * Evitar escaneos inútiles contra nodos perimetrales del CDN.
  * Descubrir IPs reales de origen (*origin bypass*).

---

## 2. DNS y Resolución Masiva

### `dnsx`
* **Descripción:** Conjunto de utilidades DNS multipropósito de alto rendimiento.
* **Usos:**
  * Resolución concurrente de dominios a direcciones IPv4 e IPv6.
  * Consultas masivas de registros A, AAAA, CNAME, MX, TXT, PTR.
  * Exportación estructurada en formato JSON para canalizar con otras herramientas (`dnsx -json`).

### `puredns`
* **Descripción:** Resolvedor DNS rápido y preciso optimizado para mitigar respuestas comodín (*wildcards*).
* **Usos:**
  * Filtrado estricto de subdominios activos mediante listas de *trusted resolvers*.
  * Bruteforce masivo de DNS con tasa mínima de falsos positivos.

---

## 3. Enumeración y Permutación de Subdominios

### `subfinder`
* **Descripción:** Herramienta de enumeración pasiva de subdominios.
* **Usos:**
  * Recolección pasiva y silenciosa sin enviar paquetes directamente al objetivo.
  * Consulta a más de 30 fuentes públicas (Censys, Shodan, VirusTotal, CertSpotter).

### `crt` (crt.sh CLI)
* **Descripción:** Cliente para consultar registros de *Certificate Transparency* (CT).
* **Usos:**
  * Extracción de todos los subdominios que hayan emitido certificados SSL/TLS legítimos.
  * Identificación de subdominios antiguos o en entornos de prueba (*pre-production, dev, staging*).

### `shuffledns`
* **Descripción:** Wrapper en Go alrededor de `massdns` para resolución y fuerza bruta DNS masiva.
* **Usos:**
  * Fuerza bruta activa a gran escala con manejo automático de wildcards DNS.

### `alterx`
* **Descripción:** Generador rápido de permutaciones y mutaciones de subdominios basado en reglas y expresiones regulares.
* **Usos:**
  * Creación de variantes basadas en nombres descubiertos (`api-dev.target.com`, `admin-test.target.com`).
  * Generación de wordlists contextuales altamente efectivas.

---

## 4. Google Dorks Automatizados

### `dorks_hunter`
* **Descripción:** Script de automatización de consultas dorks específicas por dominio.
* **Usos:**
  * Automatizar la ejecución de las 54 consultas de Google Dorks para Bug Bounty.
  * Identificación de documentos confidenciales, credenciales expuestas y endpoints administrativos.
  * *Ver módulo completo:* [[02 - MODULOS/03 - Google Dorks|Google Dorks para Bug Bounty]].

---

## 5. Escaneo de Puertos y Conversión a URLs

### `naabu`
* **Descripción:** Escáner de puertos SYN/CONNECT de alta velocidad en Go.
* **Usos:**
  * Escaneo rápido de los 1.000 puertos más comunes (*top-ports*) de subdominios activos.
  * Descubrimiento ágil de puertos no estándar (8080, 8443, 8000, 8888, 9000, 3000).

### `nmap`
* **Descripción:** El estándar de la industria en escaneo e identificación de redes y servicios.
* **Usos:**
  * Escaneo profundo de versiones de servicios (`-sV`) y scripts NSE (`-sC`).
  * Generación de informes estructurados en XML (`-oX scan.xml`).

### `nmapurls`
* **Descripción:** Utilidad para transformar los resultados XML generados por Nmap en listas de URLs utilizables.
* **Usos:**
  * Extraer directamente `http://target:port` y `https://target:port` para alimentar crawlers y fuzzer.

---

## 6. Capturas de Pantalla y Reconocimiento Visual

### `gowitness`
* **Descripción:** Herramienta de capturas de pantalla web basada en Chrome Headless escrita en Go.
* **Usos:**
  * Renderizado masivo y visual de cientos de endpoints web descubiertos.
  * Base de datos local SQLite con reportes interactivos navegables vía web (`gowitness server`).
  * Triaje visual ultrarrápido para descartar páginas por defecto y detectar paneles de login interesantes.

---

## 7. Recolección y Crawling de URLs

### `urlfinder`
* **Descripción:** Descubridor pasivo de endpoints a partir de fuentes públicas.

### `katana` (ProjectDiscovery)
* **Descripción:** Crawler web moderno de última generación.
* **Usos:**
  * Rastrear enlaces profundos con renderizado de JavaScript (*headless crawling*).
  * Extracción de rutas en código JavaScript (`.js`), formularios y parámetros ocultos.

### `gau` (Get All URLs)
* **Descripción:** Recolector masivo de URLs conocidas.
* **Usos:**
  * Consulta concurrente a Wayback Machine, Common Crawl y AlienVault OTX.
  * Obtención en segundos de miles de endpoints históricos de cualquier dominio.

### `waybackurls`
* **Descripción:** Extractor específico para los archivos históricos de Wayback Machine.

### `urless`
* **Descripción:** Normalizador y desduplicador inteligente de listas de URLs.
* **Usos:**
  * Reducción de parámetros repetitivos para optimizar el tiempo de auditoría.

---

## 8. Filtrado de URLs y Búsqueda de Vectores (GF / URO)

### `gf` (Grep Filters de TomNomNom)
* **Descripción:** Utilidad que aplica expresiones regulares predefinidas sobre listas masivas de URLs para identificar parámetros vulnerables a inyecciones.
* **Patrones disponibles:**
  * `sqli`, `sqli-error`: Parámetros `id=`, `query=`, `search=`.
  * `xss`: Parámetros reflejados (`q=`, `keyword=`, `name=`).
  * `ssrf`: Parámetros con URLs (`url=`, `dest=`, `redirect=`, `uri=`).
  * `rce`, `lfi`, `idor`, `redirect`, `secrets`, `api-keys`, `jwt`, `debug-pages`, `s3-buckets`, `cors`, `takeovers`.

### `uro`
* **Descripción:** Herramienta en Python para reducir el volumen de URLs preservando la diversidad de parámetros y rutas.

---

## 9. Escaneo Automatizado de Vulnerabilidades (Nuclei)

### `nuclei` (ProjectDiscovery)
* **Descripción:** Escáner de vulnerabilidades extremadamente rápido basado en plantillas YAML comunitarias.
* **Usos:**
  * Detección de fallos de configuración (*misconfigurations*), CVEs recientes de alta gravedad, exposiciones de paneles administrativos, paneles Git expuestos y fugas de tokens API.

---

## 10. Servicios de Inteligencia y Procesamiento JSON

* **`curl`:** Realización de peticiones HTTP/S y consultas a APIs de geolocalización e IP (`ipinfo.io`).
* **`jq`:** Manipulación, filtrado y extracción de campos clave en respuestas JSON por consola.

---

## 11. Scripts Automatizados de Reconocimiento

El material del curso incorpora scripts Bash para encadenar las fases de reconocimiento:

### `virustotal.sh`
* Consulta automatizada a la API de VirusTotal para verificar la reputación de direcciones IP y dominios sospechosos.

### `wayback.sh`
* Consulta por lotes a Wayback Machine para volcar endpoints históricos de subdominios específicos.

### `alienvault.sh`
* Extracción automatizada de IoCs y endpoints desde la plataforma AlienVault OTX.

### `lostfuzzer.sh`
* Flujo automatizado que combina resolución DNS, filtrado de URLs y lanzamiento de plantillas Nuclei sobre los objetivos descubiertos.

### `gf_critical_script.sh`
* Filtra las URLs recolectadas en busca de los vectores más críticos:
  > `sqli`, `sqli-error`, `xss`, `ssrf`, `rce`, `lfi`, `xxe`, `ssti`, `idor`, `redirect`, `jwt`, `secrets`, `api-keys`, `aws-keys`, `github`, `firebase`, `debug-pages`, `js-sinks`, `domxss`, `s3-buckets`, `cors`, `takeovers`, `upload-fields`.

### `gf_all_script.sh`
* Ejecución iterativa de todos los patrones GF para generar un informe exhaustivo de parámetros sospechosos.

---

## 12. Recursos y Plataformas Online

* **[ViewDNS.info](https://viewdns.info/):** Herramientas online para búsqueda inversa de IP (*Reverse IP Lookup*), historial DNS y detección de servidores compartidos.
* **[Bug Bounty Dorks (Karthik)](https://karthikdude.github.io/bug-bounty-dorks/):** Colección categorizada de dorks para caza de recompensas.
* **[Subdomain Finder (c99.nl)](https://subdomainfinder.c99.nl/):** Motor online para descubrimiento rápido de subdominios sin necesidad de instalar herramientas.

---

## 13. Dependencias de Sistema

* **Google Chrome (Headless):** Motor de renderizado requerido por `gowitness` para capturar pantallas.
* **Python HTTP Server:** Servidor ligero (`python3 -m http.server 8080`) para previsualizar galerías de capturas localmente.
* **`wget`:** Descarga automatizada de diccionarios y resolvers DNS actualizados.

---

> [!tip] Siguiente Paso
> Continúa con las pruebas de inyección y fuzzing avanzado en:
> 🔍 **[[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]**

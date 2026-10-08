---
title: "Guía de Web Fuzzing — Bug Bounty & Hacking Ético"
aliases:
  - Web Fuzzing
  - Fuzzing
  - Fuzzing Bug Bounty
  - Guia Fuzzing
  - ffuf
  - gobuster
  - feroxbuster
tags:
  - hacking-etico
  - fuzzing
  - bug-bounty
  - web
  - ffuf
  - gobuster
  - feroxbuster
  - seclists
  - api-fuzzing
  - ifct0109
---

# 🔍 Guía de Web Fuzzing — Bug Bounty & Hacking Ético

> [!abstract] Fase: Enumeración y Descubrimiento de Contenido Web
> Guía completa y práctica de técnicas de **fuzzing web** para el descubrimiento de rutas, archivos ocultos, parámetros GET/POST, subdominios, virtual hosts y APIs en auditorías de seguridad y programas de Bug Bounty.
> 
> 🔗 **Navegación:** [[02 - MODULOS/04 - Hacking Ético|⬅️ Volver a Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]] &emsp;|&emsp; [[03 - EXTRA/02 - Recursos Bug Bounty|🐛 Recursos Bug Bounty]]

---

## 🛠️ Herramientas Esenciales de Web Fuzzing

> [!info] Ecosistema de Herramientas
> Selección de utilidades de alto rendimiento para auditoría web ofensiva y recolección de activos:

* **FFUF (Fuzz Faster U Fool):**
  * Herramienta ultrarrápida y flexible escrita en Go.
  * **Casos de uso:** Enumeración de directorios y archivos, fuzzing recursivo, parámetros GET/POST y valores de variables.
* **Gobuster:**
  * Herramienta clásica de enumeración por fuerza bruta escrita en Go.
  * **Casos de uso:** Descubrimiento de directorios, fuzzing de Virtual Hosts (VHost) y resolución DNS de subdominios.
* **Feroxbuster:**
  * Herramienta recursiva de alta concurrencia escrita en Rust.
  * **Casos de uso:** Descubrimiento profundo de contenido web con control automático de recursión y filtrado dinámico.
* **cURL:**
  * Utilidad universal por línea de comandos para transferencia de datos y validación de cabeceras HTTP/S.
  * **Casos de uso:** Verificación manual de hallazgos, inspección de respuestas y pruebas directas.
* **Subfinder:**
  * Herramienta pasiva de reconocimiento de subdominios desarrollada por ProjectDiscovery.
  * **Casos de uso:** Mapeo inicial de superficie de ataque sin generar tráfico directo contra el objetivo.
* **API Fuzzer (`webfuzz_api`):**
  * Herramienta especializada en endpoints REST, SOAP y GraphQL.
  * **Repositorio oficial:** [GitHub — PandaSt0rm/webfuzz_api](https://github.com/PandaSt0rm/webfuzz_api)
* **SecLists:**
  * La colección de diccionarios de referencia mundial para auditorías de seguridad: [GitHub — SecLists](https://github.com/danielmiessler/SecLists).

---

## 📚 Diccionarios Recomendados (SecLists)

Ubicaciones estándar dentro del repositorio [SecLists](https://github.com/danielmiessler/SecLists) clasificadas por propósito:

### 📁 Nombres de Ficheros y Directorios
* `Discovery/Web-Content/common.txt` — Diccionario básico rápido para pruebas preliminares.
* `Discovery/Web-Content/directory-list-2.3-medium.txt` — Estándar de la industria (extraído de sitios reales).
* `Discovery/Web-Content/big.txt` — Lista exhaustiva para descubrimientos profundos.
* `Discovery/Web-Content/raft-large-directories.txt` — Directorios comunes ordenados por frecuencia.
* `Discovery/Web-Content/directory-list-2.3-big.txt` — Diccionario extendido para auditorías exhaustivas.

### 🏷️ Parámetros Web
* `Discovery/Web-Content/common.txt`
* `Discovery/Web-Content/burp-parameter-names.txt` — Lista clásica de Burp Suite para descubrir parámetros ocultos.

### 🌐 Virtual Hosts (VHost)
* `Discovery/Web-Content/common.txt`

### 🔎 Subdominios (DNS)
* `Discovery/DNS/subdomains-top1million-5000.txt` — Los 5.000 subdominios más frecuentes de Internet.

### 📄 Extensiones de Archivo
* `Discovery/Web-Content/web-extensions.txt`
* `Discovery/Web-Content/web-extensions-big.txt`

### 🔌 Endpoints de APIs
* `Discovery/Web-Content/api/api-endpoints.txt`
* `Discovery/Web-Content/api/actions.txt`
* `Discovery/Web-Content/api/objects.txt`

---

## 🎯 Comandos Prácticos de Web Fuzzing

### 1. Enumeración de Directorios

```bash
# FFUF: coloreado (-c), ignorar comentarios (-ic)
ffuf -ic -c -u http://IP:PORT/FUZZ -w wordlist.txt

# Gobuster
gobuster dir -u http://IP:PORT -w wordlist.txt

# Feroxbuster
feroxbuster -u http://IP:PORT -w wordlist.txt
```

### 2. Enumeración de Archivos con Extensiones

```bash
# FFUF con extensiones comunes
ffuf -ic -c -v -w wordlist.txt -u http://IP:PORT/FUZZ -e .php,.html,.txt

# Gobuster con flags de extensión
gobuster dir -u http://IP:PORT -w wordlist.txt -x php,html,txt

# Feroxbuster (profundidad 1)
feroxbuster -u http://IP:PORT -w wordlist.txt -x php,html,txt -d 1
```

### 3. Fuzzing Recursivo

```bash
# FFUF recursivo hasta nivel 2 con límite de peticiones (rate limiting)
ffuf -ic -c -v -w wordlist.txt -u http://IP:PORT/FUZZ -e .html -recursion -recursion-depth 2 -rate 500 -timeout 0

# Feroxbuster recursivo hasta nivel 4
feroxbuster -u http://IP:PORT -w wordlist.txt -x php,html,txt -d 4
```

### 4. Fuzzing de Parámetros y Valores (Parameter Fuzzing)

#### Método GET:
```bash
# Validar respuesta base
curl http://IP:PORT/get.php

# Descubrir nombres de parámetros ocultos (?FUZZ=1)
ffuf -ic -c -v -r -w wordlist.txt -u http://IP:PORT/get.php?FUZZ=1 -fc 404

# Descubrir valores válidos para un parámetro identificado (?PARAM=FUZZ)
ffuf -ic -c -v -r -w wordlist.txt -u http://IP:PORT/get.php?PARAM=FUZZ -fc 404
```

#### Método POST:
```bash
# Probar endpoint POST
curl -d "" http://IP:PORT/post.php

# Descubrir nombres de parámetros por POST
ffuf -ic -c -r -v -w wordlist.txt -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "FUZZ=1" -fc 404

# Descubrir valores por POST
ffuf -ic -c -r -v -w wordlist.txt -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -fc 404
```

### 5. Fuzzing de Virtual Hosts y Subdominios

```bash
# 1. Asociar IP al dominio en la resolución local si es necesario
echo "IP domain.dns" | sudo tee -a /etc/hosts

# 2. Fuzzing de Virtual Hosts (VHost) en cabecera Host con Gobuster
gobuster vhost -w wordlist.txt -u http://DOMAIN.DNS:PORT --append-domain -o vhosts.txt && cat vhosts.txt | grep 200

# 3. Enumeración de subdominios por fuerza bruta DNS
gobuster dns -d example.org -w wordlist.txt

# 4. Descubrimiento pasivo rápido con Subfinder
subfinder -d example.org -silent
```

### 6. Validación de Hallazgos

```bash
# Petición completa
curl http://IP:PORT

# Inspeccionar únicamente las cabeceras HTTP devueltas
curl -I http://IP:PORT
```

---

## 🔌 Web APIs Fuzzing

### 1. REST Endpoints y Parámetros
* **Estructura típica:**
  * `/users` — Colección de recursos de usuarios.
  * `/users/123` — Recurso específico con identificador `123`.
  * `/products` — Colección de recursos de productos.
  * `/products/456` — Recurso específico con identificador `456`.
* **Metodología de enumeración:**
  * Revisar documentación pública o rutas expuestas (`/swagger.json`, `/openapi.json`, `/v1/api-docs`).
  * Análisis de tráfico de red a través de proxy (Burp Suite / OWASP ZAP).
  * Fuzzing de nombres de parámetros utilizando `Discovery/Web-Content/api/`.

### 2. SOAP (Simple Object Access Protocol)
* Los parámetros y métodos se definen en el archivo XML **WSDL** (*Web Services Description Language*), que describe la interfaz, operaciones y estructura de mensajes.
* **Enumeración:**
  * Análisis de archivos `.wsdl` expuestos (`?wsdl`).
  * Inspección de tráfico SOAP XML.
  * Fuzzing de métodos y parámetros con listas API.

### 3. GraphQL APIs
* Generalmente cuentan con un único punto de entrada común: `/graphql`.
* **Descubrimiento de Queries y Mutaciones:**
  * **Introspección:** Enviar una consulta de introspección para volcar todo el esquema si no está deshabilitado en producción:
    ```graphql
    { "__schema": { "types": { "name": true } } }
    ```
  * Documentación y análisis de tráfico en cliente web.

### 4. Tipos de API Fuzzing
* **Parameter Fuzzing:** Encontrar campos no documentados que activen lógica administrativa o depuración (`debug=true`, `admin=1`).
* **Data Format Fuzzing:** Modificar tipos de datos esperados (arrays en vez de enteros, objetos JSON anidados, inyecciones de tipo).
* **Sequence Fuzzing:** Alterar el flujo habitual de llamadas consecutivas (ej.: confirmar compra antes de pagar).

### 5. Script Automatizado para APIs (`webfuzz_api`)
Utilidad en Python para auditoría masiva de endpoints:
```bash
python3 api_fuzzer.py http://IP:PORT > api_fuzz.txt && cat api_fuzz.txt | grep -v 404
```

---

## 🔧 Filtros y Opciones de Coincidencia (Matchers & Filters)

### 1. Opciones en FFUF

#### Matchers (Mostrar respuestas que coincidan con la condición):
| Parámetro | Función | Descripción |
| :--- | :--- | :--- |
| `-mc` | **Match Status Code** | Coincidir con códigos HTTP (por defecto: 200, 204, 301, 302, 307, 401, 403). Usar `all` para todos. |
| `-ml` | **Match Lines** | Coincidir por número exacto de líneas en el cuerpo de la respuesta. |
| `-mr` | **Match Regexp** | Coincidir cuando la respuesta contenga una expresión regular específica. |
| `-ms` | **Match Size** | Coincidir por tamaño exacto en bytes de la respuesta. |
| `-mw` | **Match Words** | Coincidir por cantidad exacta de palabras devueltas. |

#### Filters (Ocultar respuestas no deseadas):
| Parámetro | Función | Descripción |
| :--- | :--- | :--- |
| `-fc` | **Filter Status Code** | Filtrar y descartar códigos HTTP específicos (ej: `-fc 404,403`). |
| `-fl` | **Filter Lines** | Descartar respuestas con un número concreto de líneas (ej: `-fl 12`). |
| `-fr` | **Filter Regexp** | Descartar respuestas que coincidan con una expresión regular. |
| `-fs` | **Filter Size** | Descartar respuestas de tamaños específicos en bytes (muy útil para omitir páginas de error personalizadas, ej: `-fs 2849`). |
| `-fw` | **Filter Words** | Descartar respuestas con una cantidad específica de palabras. |

---

### 2. Opciones de Filtrado en Gobuster

| Flag | Tipo | Descripción | Escenario de Ejemplo |
| :--- | :--- | :--- | :--- |
| `-s` | Include (Permitir) | Incluir únicamente respuestas con códigos específicos. | Buscar redirecciones específicas: `-s 301,302,307`. |
| `-b` | Exclude (Denegar) | Excluir respuestas con los códigos HTTP especificados. | Ocultar errores no deseados: `-b 404`. |
| `--exclude-length` | Exclude Size | Excluir respuestas según longitud en bytes (soporta rangos). | Omitir respuestas vacías o errores comunes: `--exclude-length 0,404`. |

---

### 3. Opciones de Filtrado Avanzado en Feroxbuster

| Flag | Tipo | Descripción | Escenario de Ejemplo |
| :--- | :--- | :--- | :--- |
| `--dont-scan` | Exclusión de ruta | Omitir URLs o patrones para evitar recursión en bucle. | Excluir directorios con estáticos pesados: `--dont-scan /uploads`. |
| `-S, --filter-size` | Filtrado por tamaño | Excluir respuestas según tamaño en bytes o rangos. | Omitir páginas genéricas de error de 1KB: `-S 1024`. |
| `-X, --filter-regex` | Expresión regular | Filtrar respuestas cuyo cuerpo o cabeceras coincidan con un regex. | Descartar páginas de acceso restringido: `-X "Access Denied"`. |
| `-W, --filter-words` | Filtrado por palabras | Descartar respuestas según conteo o rango de palabras. | Eliminar errores concisos: `-W 0-10`. |
| `-N, --filter-lines` | Filtrado por líneas | Descartar respuestas según cantidad de líneas. | Omitir respuestas muy largas: `-N 50-`. |
| `-C, --filter-status` | Lista negra de códigos | Excluir códigos HTTP concretos. | Oprimir errores estándar: `-C 404,500`. |
| `--filter-similar-to` | Similitud de página | Descartar páginas muy similares a una de referencia. | Evitar duplicados de error suave: `--filter-similar-to error.html`. |
| `-s, --status-codes` | Lista blanca de códigos | Incluir únicamente respuestas con los códigos definidos. | Centrarse en respuestas de éxito: `-s 200,204,301,302`. |

---

> [!warning] Responsabilidad y Buenas Prácticas
> Todas estas técnicas deben aplicarse **estrictamente dentro del ámbito (*scope*) autorizado** del programa de Bug Bounty o contrato de auditoría. El fuzzing agresivo puede degradar el rendimiento del servidor o activar mecanismos de bloqueo (WAF/Rate Limiting); modula siempre la concurrencia (`-t`, `-rate`) y los tiempos de espera (`-timeout`).

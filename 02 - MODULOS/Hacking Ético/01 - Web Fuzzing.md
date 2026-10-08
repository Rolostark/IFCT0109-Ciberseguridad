---
title: "🔍 Guía de Web Fuzzing — Bug Bounty"
aliases:
  - Guía de Web Fuzzing - Bug Bounty
  - Web Fuzzing Bug Bounty
  - Fuzzing
  - ffuf
  - gobuster
  - feroxbuster
tags:
  - ifct0109
  - hacking-etico
  - fuzzing
  - bug-bounty
  - wordlists
  - seclists
  - apis
---

# 🔍 Guía de Web Fuzzing - Bug Bounty

> [!abstract] Información de la Guía
> **Fase:** Enumeración y Descubrimiento de Contenido Web  
> **Descripción:** Guía completa de técnicas de fuzzing web para descubrimiento de directorios, archivos, parámetros, subdominios y APIs en programas de Bug Bounty.  
> **Herramientas:** Guía para la instalación de las herramientas necesarias en el [[02 - MODULOS/Hacking Ético/Herramientas de Web Fuzzing|enlace]].

---

## 📚 Wordlists (SecLists)

### Nombres de ficheros y directorios
* `Discovery/Web-Content/common.txt`
* `Discovery/Web-Content/directory-list-2.3-medium.txt`
* `Discovery/Web-Content/big.txt`
* `Discovery/Web-Content/raft-large-directories.txt`
* `Discovery/Web-Content/directory-list-2.3-big.txt`

### Parámetros WEB
* `Discovery/Web-Content/common.txt`
* `Discovery/Web-Content/burp-parameter-names.txt`

### VHost
* `Discovery/Web-Content/common.txt`

### Subdomains
* `Discovery/DNS/subdomains-top1million-5000.txt`

### Extensiones
* `Discovery/Web-Content/web-extensions.txt`
* `Discovery/Web-Content/web-extensions-big.txt`

### APIs
* `Discovery/Web-Content/api/api-endpoints.txt`
* `Discovery/Web-Content/api/actions.txt`
* `Discovery/Web-Content/api/objects.txt`

---

## 🎯 Web Fuzzing

### Enumeración de Directorios
```bash
ffuf -ic -c -u http://IP:PORT/FUZZ -w wordlist.txt
```
```bash
gobuster dir -u http://IP:PORT -w wordlist.txt
```
```bash
feroxbuster -u http://IP:PORT -w wordlist.txt
```

### Enumeración de Archivos
```bash
ffuf -ic -c -v -w wordlist.txt -u http://IP:PORT/FUZZ -e .php,.html,.txt
```
```bash
gobuster dir -u http://IP:PORT -w wordlist.txt -x php,html,txt
```
```bash
feroxbuster -u http://IP:PORT -w wordlist.txt -x php,html,txt -d 1
```

### Recursive Fuzzing
```bash
ffuf -ic -c -v -w wordlist.txt -u http://IP:PORT/FUZZ -e .html -recursion -recursion-depth 2 -rate 500 -timeout 0
```
```bash
feroxbuster -u http://IP:PORT -w wordlist.txt -x php,html,txt -d 4
```

### Parameter and Value Fuzzing

#### GET
```bash
curl http://IP:PORT/get.php
```
```bash
ffuf -ic -c -v -r -w wordlist -u http://IP:PORT/get.php?FUZZ=1 -fc 404
```
```bash
ffuf -ic -c -v -r -w wordlist -u http://IP:PORT/get.php?PARAM=FUZZ -fc 404
```

#### POST
```bash
curl -d "" http://IP:PORT/post.php
```
```bash
ffuf -ic -c -r -v -w wordlist.txt -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "FUZZ=1" -fc 404
```
```bash
ffuf -ic -c -r -v -w wordlist.txt -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "y=FUZZ" -fc 404
```

### Virtual Host and Subdomain Fuzzing
```bash
echo "IP domain.dns" | sudo tee -a /etc/hosts
```
```bash
gobuster vhost -w wordlist.txt -u http://DOMAIN.DNS:PORT --append-domain -o vhosts.txt && cat vhosts.txt | grep 200
```

### Subdomain Fuzzing
```bash
gobuster dns -d example.org -w wordlist.txt
```
```bash
subfinder -d example.org -silent
```

### Validating Findings
```bash
curl http://IP:PORT
```
```bash
curl -I http://IP:PORT
```

---

## 🔌 Web APIs Fuzzing

### REST Endpoints and Parameters

#### Estructura:
* `/users` - Represents a collection of user resources.
* `/users/123` - Represents a specific user with the ID 123.
* `/products` - Represents a collection of product resources.
* `/products/456` - Represents a specific product with the ID 456.

#### Enumeración:
* API Documentation
* Proxy Network Traffic Analysis
* Parameter Name Fuzzing (`Discovery/Web-Content/api/`)

### SOAP (Simple Object Access Protocol)
The parameters are defined in the Web Services Description Language (WSDL) file, an XML-based document that describes the web service's interface, operations, and message formats.

#### Enumeración:
* WSDL Analysis
* Proxy Network Traffic Analysis
* Fuzzing for Parameter Names and Values (`Discovery/Web-Content/api/`)

### GraphQL API
Endpoint is usually a URL like `/graphql` and serves as the entry point for all queries and mutations sent to the API.

#### Discovering Queries and Mutations:
* Introspection
* API Documentation
* Proxy Network Traffic Analysis

### Types of API Fuzzing
* Parameter Fuzzing
* Data Format Fuzzing
* Sequence Fuzzing

### API Fuzzing Tool
* **Repositorio:** [https://github.com/PandaSt0rm/webfuzz_api](https://github.com/PandaSt0rm/webfuzz_api)

```bash
python3 api_fuzzer.py http://IP:PORT > api_fuzz.txt && cat api_fuzz.txt | grep -v 404
```

---

## 🔧 Filtros de Herramientas

### FFUF

#### MATCHER OPTIONS:
| Flag | Descripción |
| :--- | :--- |
| `-mc` | Match HTTP status codes, or "all" for everything. (default: 200,204,301,302,307,401,403) |
| `-ml` | Match amount of lines in response |
| `-mr` | Match regexp |
| `-ms` | Match HTTP response size |
| `-mw` | Match amount of words in response |

#### FILTER OPTIONS:
| Flag | Descripción |
| :--- | :--- |
| `-fc` | Filter HTTP status codes from response. Comma separated list of codes and ranges |
| `-fl` | Filter by amount of lines in response. Comma separated list of line counts and ranges |
| `-fr` | Filter regexp |
| `-fs` | Filter HTTP response size. Comma separated list of sizes and ranges |
| `-fw` | Filter by amount of words in response. Comma separated list of word counts and ranges |

---

### Gobuster

| Flag | Descripción | Escenario de Ejemplo |
| :--- | :--- | :--- |
| `-s` (include) | Include only responses with the specified status codes (comma-separated). | You're looking for redirects, so you filter for codes 301,302,307 |
| `-b` (exclude) | Exclude responses with the specified status codes (comma-separated). | The server returns many 404 errors. Exclude them with -b 404 |
| `--exclude-length` | Exclude responses with specific content lengths (comma-separated, supports ranges). | You're not interested in 0-byte or 404-byte responses, so use --exclude-length 0,404 |

---

### Feroxbuster

| Flag | Descripción | Escenario de Ejemplo |
| :--- | :--- | :--- |
| `--dont-scan` | Exclude specific URLs or patterns from being scanned (even if found in links during recursion). | You know the /uploads directory contains only images, so you can exclude it using --dont-scan /uploads. |
| `-S, --filter-size` | Exclude responses based on their size (in bytes). You can specify single sizes or comma-separated ranges. | You've noticed many 1KB error pages. Use -S 1024 to exclude them. |
| `-X, --filter-regex` | Exclude responses whose body or headers match the specified regular expression. | Filter out pages with a specific error message using -X "Access Denied". |
| `-W, --filter-words` | Exclude responses with a specific word count or range of word counts. | Eliminate responses with very few words (e.g., error messages) using -W 0-10. |
| `-N, --filter-lines` | Exclude responses with a specific line count or range of line counts. | Filter out long, verbose pages with -N 50-. |
| `-C, --filter-status` | Exclude responses based on specific HTTP status codes. This operates as a denylist. | Suppress common error codes like 404 and 500 using -C 404,500. |
| `--filter-similar-to` | Exclude responses that are similar to a given webpage. | Remove duplicate or near-duplicate pages based on a reference page using --filter-similar-to error.html. |
| `-s, --status-codes` | Include only responses with the specified status codes. This operates as an allowlist (default: all). | Focus on successful responses using -s 200,204,301,302. |

---

> [!note] 📝 Nota
> Todas estas técnicas deben aplicarse dentro del scope autorizado del programa de Bug Bounty. El fuzzing agresivo puede generar mucho tráfico, ajusta los parámetros de rate y timeout según sea necesario.

---

## 🔗 Navegación y Referencias
* 🛠️ [[02 - MODULOS/Hacking Ético/Herramientas de Web Fuzzing|Guía de Herramientas de Web Fuzzing (Instalación y Usos)]]
* 🎯 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento Bug Bounty]]
* 🛠️ [[02 - MODULOS/Hacking Ético/Herramientas de Reconocimiento Bug Bounty|Herramientas de Reconocimiento Bug Bounty]]
* ⬅️ [[02 - MODULOS/04 - Hacking Ético|Volver al Módulo de Hacking Ético]]

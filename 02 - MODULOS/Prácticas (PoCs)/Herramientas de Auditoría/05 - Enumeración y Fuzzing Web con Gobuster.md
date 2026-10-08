---
title: "🔍 Práctica: Fuzzing de Directorios, DNS y VHosts con Gobuster"
aliases:
  - Fuzzing de Directorios, DNS y VHosts con Gobuster
  - PoC gobuster
tags:
  - ifct0109/pocs
  - auditoria
  - gobuster
  - fuzzing
  - web
  - dns
---

# 🔍 Práctica: Fuzzing de Directorios, DNS y VHosts con Gobuster

> [!abstract] Resumen del Laboratorio
> Arsenal técnico de auditoría y pentesting del Certificado IFCT0109: reconocimiento de infraestructura, captura y análisis de hashes, explotación, pivoting perimetral e investigación de desbordamientos de memoria en entornos controlados.

---

# 📂 Enumeración Web - Gobuster, Feroxbuster y FFUF

## 📚 Introducción al Web Fuzzing

El **web fuzzing** (o enumeración web) es una técnica esencial en el reconocimiento de aplicaciones web. Consiste en realizar peticiones automáticas para descubrir:

* Directorios y archivos ocultos

* Subdominios y virtual hosts

* Parámetros de aplicaciones web

* APIs y endpoints no documentados

En esta práctica aprenderás a usar las tres herramientas más populares de fuzzing: **Gobuster**, **Feroxbuster** y **FFUF**.

## 📖 Wordlists Recomendadas (SecLists)

Las wordlists son diccionarios de palabras comunes usadas para el fuzzing. La colección **SecLists** es el estándar en la industria.

### Nombres de Ficheros y Directorios

```bash
Discovery/Web-Content/common.txt
Discovery/Web-Content/directory-list-2.3-medium.txt
Discovery/Web-Content/big.txt
Discovery/Web-Content/raft-large-directories.txt
Discovery/Web-Content/directory-list-2.3-big.txt
```

### Parámetros WEB

```bash
Discovery/Web-Content/common.txt
Discovery/Web-Content/burp-parameter-names.txt
```

### Virtual Hosts

```bash
Discovery/Web-Content/common.txt
```

### Subdominios

```bash
Discovery/DNS/subdomains-top1million-5000.txt
```

### Extensiones de Archivos

```bash
Discovery/Web-Content/web-extensions.txt
Discovery/Web-Content/web-extensions-big.txt
```

### APIs

```bash
Discovery/Web-Content/api/api-endpoints.txt
Discovery/Web-Content/api/actions.txt
Discovery/Web-Content/api/objects.txt
```

> Instalación de SecLists:
>
>
> ```bash
sudo apt install seclists -y
```
>
>
>              Ubicación: `/usr/share/seclists/`

## 🛠️ Herramientas de Web Fuzzing

### 1. Gobuster

Herramienta rápida escrita en Go, especializada en fuerza bruta de directorios, archivos, DNS y vhosts.

#### Enumeración de Directorios:

```cmd
gobuster dir -u http://IP:PORT -w wordlist.txt
```

#### Enumeración de Archivos con extensiones:

```cmd
gobuster dir -u http://IP:PORT -w wordlist.txt -x php,html,txt
```

#### Virtual Host Fuzzing:

```bash
gobuster vhost -w wordlist.txt -u http://DOMAIN.DNS:PORT --append-domain -o vhosts.txt
cat vhosts.txt | grep 200
```

#### Subdomain Fuzzing:

```bash
gobuster dns -d example.org -w wordlist.txt
```

### 2. Feroxbuster

Herramienta moderna escrita en Rust, con soporte nativo para fuzzing recursivo y muy rápida.

#### Enumeración de Directorios:

```bash
feroxbuster -u http://IP:PORT -w wordlist.txt
```

#### Enumeración de Archivos:

```bash
feroxbuster -u http://IP:PORT -w wordlist.txt -x php,html,txt -d 1
```

#### Fuzzing Recursivo (profundidad 4):

```bash
feroxbuster -u http://IP:PORT -w wordlist.txt -x php,html,txt -d 4
```

### 3. FFUF (Fuzz Faster U Fool)

Herramienta versátil y extremadamente rápida, ideal para fuzzing de parámetros, valores y fuzzing avanzado.

#### Enumeración de Directorios:

```bash
ffuf -ic -c -u http://IP:PORT/FUZZ -w wordlist.txt
```

#### Enumeración de Archivos:

```bash
ffuf -ic -c -v -w wordlist.txt -u http://IP:PORT/FUZZ -e .php,.html,.txt
```

#### Fuzzing Recursivo:

```bash
ffuf -ic -c -v -w wordlist.txt -u http://IP:PORT/FUZZ -e .html -recursion -recursion-depth 2 -rate 500 -timeout 0
```

#### Opciones comunes:

* `-ic`: Ignorar comentarios en wordlist

* `-c`: Salida con colores

* `-v`: Modo verbose

* `-r`: Seguir redirecciones

* `-fc 404`: Filtrar código de estado 404

## 🎯 Técnicas de Fuzzing Avanzadas

### Fuzzing de Parámetros GET

Primero verifica qué parámetros acepta la aplicación:

```bash
curl http://IP:PORT/get.php
```

Fuzzea el nombre del parámetro:

```bash
ffuf -ic -c -v -r -w wordlist.txt -u http://IP:PORT/get.php?FUZZ=1 -fc 404
```

Fuzzea el valor del parámetro:

```bash
ffuf -ic -c -v -r -w wordlist.txt -u http://IP:PORT/get.php?PARAM=FUZZ -fc 404
```

### Fuzzing de Parámetros POST

Verifica qué acepta:

```bash
curl -d "" http://IP:PORT/post.php
```

Fuzzea parámetros POST:

```bash
ffuf -ic -c -r -v -w wordlist.txt -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "FUZZ=1" -fc 404
```

Fuzzea valores POST:

```bash
ffuf -ic -c -r -v -w wordlist.txt -u http://IP:PORT/post.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "param=FUZZ" -fc 404
```

### Enumeración de Subdominios

Primero añade el dominio al archivo hosts:

```bash
echo "IP domain.dns" | sudo tee -a /etc/hosts
```

Enumera subdominios con diferentes herramientas:

```bash
gobuster dns -d example.org -w wordlist.txt
subfinder -d example.org -silent
```

## 📝 Notas Importantes

* Velocidad vs Sigilo: Herramientas como ffuf son muy rápidas pero generan mucho tráfico. En pentests reales, considera usar límites de velocidad.

* Códigos de estado: Aprende a filtrar respuestas (200=éxito, 301/302=redirección, 403=prohibido, 404=no encontrado).

* Wordlists adecuadas: Usa wordlists específicas según el objetivo (CMS, API, archivos de configuración, etc.).

* Recursividad: Ten cuidado con la profundidad recursiva, puede generar miles de peticiones.

* WAF/IDS: Los firewalls de aplicaciones web pueden bloquear fuzzing agresivo. Ajusta la velocidad si es necesario.

← Volver al índice principal

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🧪 [[02 - MODULOS/04 - Hacking Ético|Módulo Central de Hacking Ético]]

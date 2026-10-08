---
title: "🛠️ Herramientas de Web Fuzzing — Bug Bounty"
aliases:
  - Herramientas de Web Fuzzing
  - Herramientas Fuzzing
  - Tools Web Fuzzing
tags:
  - ifct0109
  - hacking-etico
  - fuzzing
  - herramientas
  - bug-bounty
---

# 🛠️ Herramientas de Web Fuzzing - Bug Bounty

> [!abstract] Información de Herramientas
> **Categoría:** Herramientas para Enumeración y Descubrimiento Web  
> **Descripción:** Listado de herramientas esenciales utilizadas en fuzzing web para Bug Bounty.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|🔍 Volver a la Guía de Web Fuzzing]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|⬅️ Hacking Ético]]

---

## 🔍 Herramientas de Fuzzing Web

### FFUF (Fuzz Faster U Fool)
Herramienta de fuzzing web rápida y flexible escrita en Go.

> [!info] Usos
> * Enumeración de directorios y archivos
> * Fuzzing recursivo
> * Fuzzing de parámetros GET y POST
> * Fuzzing de valores de parámetros

---

### Gobuster
Herramienta de enumeración escrita en Go para directorios, archivos, subdominios y virtual hosts.

> [!info] Usos
> * Enumeración de directorios y archivos
> * Fuzzing de Virtual Hosts
> * Fuzzing de subdominios (DNS)

---

### Feroxbuster
Herramienta de descubrimiento de contenido rápida y simple escrita en Rust.

> [!info] Usos
> * Enumeración de directorios y archivos
> * Fuzzing recursivo con control de profundidad
> * Filtrado avanzado de resultados

---

### cURL
Herramienta de línea de comandos para transferir datos con URLs.

> [!info] Usos
> * Validación de hallazgos
> * Pruebas de parámetros GET y POST
> * Inspección de headers HTTP

---

### Subfinder
Herramienta de descubrimiento de subdominios que encuentra subdominios válidos usando fuentes pasivas.

> [!info] Usos
> * Enumeración pasiva de subdominios
> * Descubrimiento de activos del dominio objetivo

---

## 🔌 Herramientas para API Fuzzing

### API Fuzzer (webfuzz_api)
Herramienta personalizada para fuzzing de APIs REST, SOAP y GraphQL.

> [!info] Usos
> * Fuzzing de endpoints REST
> * Fuzzing de parámetros API
> * Fuzzing de formatos de datos
> * Fuzzing de secuencias API
> 
> **Repositorio:** [https://github.com/PandaSt0rm/webfuzz_api](https://github.com/PandaSt0rm/webfuzz_api)

---

## 📚 Recursos Adicionales

### SecLists
Colección de múltiples tipos de listas usadas durante evaluaciones de seguridad.

> [!info] Usos
> * Wordlists para directorios y archivos
> * Wordlists para parámetros web
> * Wordlists para subdominios y VHosts
> * Wordlists para extensiones de archivos
> * Wordlists para endpoints y objetos API

---

> [!note] 📝 Nota
> Todas estas herramientas deben utilizarse únicamente dentro del scope autorizado del programa de Bug Bounty. Asegúrate de tener los permisos necesarios antes de realizar cualquier tipo de fuzzing o enumeración.

---
title: "Índice del Módulo - Vulnerabilidades Web"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - vulnerabilidades-web
  - dvwa
  - vulnweb
  - owasp
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]]

# 🌐 Vulnerabilidades Web — Índice del Módulo

> [!abstract] Resumen del Módulo
> Este módulo completo aborda las principales **vulnerabilidades de aplicaciones web** recogidas en el estándar **OWASP Top 10**. A través de dos entornos prácticos complementarios —**DVWA (Damn Vulnerable Web Application)** y **VulnWeb Lab (Contenedores Docker)**—, se cubren las técnicas de identificación, explotación, post-explotación y mitigación para cada vector de ataque web.

---

## 📊 Matriz General de Laboratorios Web

| # | Vulnerabilidad / Práctica | Entorno | Severidad OWASP | Vector Principal | Acceso Directo |
| :-: | :--- | :---: | :---: | :--- | :--- |
| **01** | Command Injection (OS Injection) | DVWA | 🔴 Crítica | RCE (Remote Code Execution) | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/01 - Command Injection (DVWA)\|01 - Command Injection]] |
| **02** | Brute Force (Fuerza Bruta Web) | DVWA | 🟠 Alta | Account Takeover / Diccionarios | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/02 - Brute Force (DVWA)\|02 - Brute Force]] |
| **03** | CSRF (Cross-Site Request Forgery) | DVWA | 🟠 Alta | Falsificación de peticiones | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/03 - CSRF - Cross-Site Request Forgery (DVWA)\|03 - CSRF]] |
| **04** | Local File Inclusion (LFI) | DVWA / bWAPP | 🟠 Alta | Lectura de `/etc/passwd` y Wrappers | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/04 - Local File Inclusion - LFI (DVWA)\|04 - Local File Inclusion]] |
| **05** | Insecure File Upload | DVWA | 🔴 Crítica | Subida de Webshells PHP | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/05 - File Upload Inseguro (DVWA)\|05 - File Upload Inseguro]] |
| **06** | Cross-Site Scripting (XSS) | DVWA | 🟠 Alta | Reflected, Stored y DOM XSS | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/06 - Cross-Site Scripting - XSS (DVWA)\|06 - Cross-Site Scripting]] |
| **07-1** | SQLi Fundamentos y Error-Based | DVWA | 🔴 Crítica | Inyección SQL basada en errores | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/07-01 - SQL Injection - Fundamentos y Error-Based (DVWA)\|07-01 - SQLi Error-Based]] |
| **07-2** | SQLi Union-Based | DVWA | 🔴 Crítica | Extracción con operador `UNION` | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/07-02 - SQL Injection - Union-Based (DVWA)\|07-02 - SQLi Union-Based]] |
| **07-3** | SQLi Automatización con SQLMap | DVWA | 🔴 Crítica | Dumps de BBDD automatizados | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/07-03 - SQL Injection - Automatización con SQLMap (DVWA)\|07-03 - SQLMap Automation]] |
| **07-4** | SQLi Blind (Boolean y Time-Based) | bWAPP | 🔴 Crítica | Inyecciones a ciegas (Sleep/True) | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/07-04 - SQL Injection - Blind SQLi (DVWA)\|07-04 - Blind SQL Injection]] |

---

## 🛡️ Laboratorio VulnWeb (Contenedores Docker)

> [!tip] Despliegue del Laboratorio VulnWeb
> Para ejecutar estos laboratorios, levanta el contenedor con Docker y añade la resolución a tu archivo `/etc/hosts`:
> ```bash
> sudo bash -c 'echo "<IP_docker> vulnweb.local" >> /etc/hosts'
> ```
> Configura el navegador mediante proxy local (Burp Suite o Caido en `127.0.0.1:8080`) y FoxyProxy.

| Módulo Lab | Técnica Vulnerable | Descripción Técnica | Enlace a la Práctica |
| :--- | :--- | :--- | :--- |
| **Lab 01** | OS Command Injection | Concatenación de comandos con `;`, `&&`, `\|` | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/Lab - 01 OS Command Injection\|Lab 01 - Command Injection]] |
| **Lab 02** | Template Injection (SSTI) | Explotación de motores Jinja2, Twig, Smarty | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/Lab - 02 Server-Side Template Injection (SSTI)\|Lab 02 - SSTI Template Injection]] |
| **Lab 03** | File Inclusion (LFI / RFI) | Path traversal, wrappers `php://filter` y RFI | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/Lab - 03 File Inclusion (LFI y RFI)\|Lab 03 - LFI / RFI]] |
| **Lab 04** | Cross-Site Request Forgery | Ataques con formularios auto-submit ocultos | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/Lab - 04 Cross-Site Request Forgery (CSRF)\|Lab 04 - CSRF]] |
| **Lab 05** | Insecure File Upload | Bypass de MIME-type, doble extensión, Magic Bytes | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/Lab - 05 File Upload Inseguro\|Lab 05 - File Upload]] |
| **Lab 06** | Cross-Site Scripting (XSS) | Inyección de payloads JS para secuestro de sesiones | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/Lab - 06 Cross-Site Scripting (XSS)\|Lab 06 - XSS Cross-Site Scripting]] |
| **Lab 07** | SQL Injection (SQLi) | Inyección booleana, temporal y extracción de hashes | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/Lab - 07 SQL Injection (SQLi)\|Lab 07 - SQL Injection]] |
| **Lab 08** | Token JWT (JSON Web Tokens) | Ataques de algoritmo `none`, crackeo de clave débil | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/Lab - 08 Token JWT (JSON Web Tokens)\|Lab 08 - Token JWT]] |

---

## 🎯 Mapeo contra OWASP Top 10

* **A01: Broken Access Control:** CSRF, Insecure File Upload, LFI.
* **A02: Cryptographic Failures:** Token JWT con clave débil / alg: none.
* **A03: Injection:** OS Command Injection, SQL Injection, SSTI, LFI.
* **A05: Security Misconfiguration:** File Upload sin validación en servidor, mensajes de error detallados.
* **A07: Identification and Authentication Failures:** Fuerza Bruta de contraseñas con Hydra / Burp Intruder.

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]]

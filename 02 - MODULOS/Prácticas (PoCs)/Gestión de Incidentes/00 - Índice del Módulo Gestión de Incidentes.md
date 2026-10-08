---
title: "Índice del Módulo - Gestión de Incidentes de Seguridad"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - gestion-de-incidentes
  - malware
  - soc
  - mitre
  - siem
  - wazuh
  - forense
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]]

# 🚨 Gestión de Incidentes de Seguridad — Índice del Módulo

> [!abstract] Resumen del Módulo
> Este módulo aborda el ciclo integral de **defensa, detección, análisis y respuesta ante incidentes** en infraestructuras corporativas. Está estructurado en 4 grandes áreas técnicas complementarias:
> 1. **Análisis de Malware:** Métodos estáticos, dinámicos e ingeniería inversa con desensambladores.
> 2. **SOC & MITRE ATT&CK:** Modelos operativos de un SOC moderno, Cyber Threat Intelligence (CTI), matrices ATT&CK, ATT&CK Navigator y contramedidas defensivas con MITRE D3FEND.
> 3. **SIEM y Wazuh:** Despliegue de Wazuh (arquitectura por componentes), monitorización de integridad (FIM), reglas YARA/VirusTotal, detección de CVEs/SCA, Active Response y correlación con IDS Suricata.
> 4. **Análisis Forense Digital:** Adquisición y cadena de custodia de memoria RAM y discos duros (E01), análisis de procesos y malware con Volatility, investigación con FTK Imager, Autopsy y OSForensics, y forense en dispositivos móviles iOS (iPhone).

---

## 🦠 1. Análisis de Malware

| # | Práctica | Metodología y Herramientas | Enlace a la Práctica |
| :-: | :--- | :--- | :--- |
| **01** | Análisis Dinámico | Entorno sandbox aislado, Process Hacker, Regshot, Wireshark, Procmon | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/01 - Malware - Análisis Dinámico\|01 - Malware: Análisis Dinámico]] |
| **02** | Análisis Estático | Hashes criptográficos, VirusTotal, PE-bear, FLOSS, Strings, bibliotecas importadas | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/02 - Malware - Análisis Estático\|02 - Malware: Análisis Estático]] |
| **03** | Ingeniería Inversa | Desensamblado con Ghidra, depuración interactiva con x64dbg/x32dbg, flujo de ejecución | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/03 - Malware - Ingeniería Inversa\|03 - Malware: Ingeniería Inversa]] |

---

## 🖥️ 2. Security Operations Center (SOC) & MITRE ATT&CK

| # | Práctica | Contenido Técnico | Enlace a la Práctica |
| :-: | :--- | :--- | :--- |
| **04** | Introducción al SOC | Organización por niveles (Tier 1-3), EDR/XDR/SIEM/SOAR, Blue vs Red vs Purple Team | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/04 - SOC - 01 Introducción al SOC y Organización\|04 - SOC: Introducción y Organización]] |
| **05** | Cyber Threat Intelligence | IOCs vs IOAs, TTPs, Pirámide del Dolor de David Bianco, feeds OSINT y CTI comercial | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/05 - SOC - 02 Cyber Threat Intelligence (CTI)\|05 - SOC: Cyber Threat Intelligence (CTI)]] |
| **06** | MITRE ATT&CK Framework | Matrices Enterprise/Mobile/ICS, tácticas, técnicas, sub-técnicas, grupos y software | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/06 - SOC - 03 MITRE ATT&CK Framework\|06 - SOC: MITRE ATT&CK Framework]] |
| **07** | ATT&CK Navigator | Visualización matricial, creación de capas (layers), filtrado de plataformas y mapas de calor | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/07 - SOC - 04 ATT&CK Navigator y Cobertura Defensiva\|07 - SOC: ATT&CK Navigator]] |
| **08** | Poniendo ATT&CK en Práctica | Operacionalización de TTPs, toma de decisiones defensivas y ejercicios de Purple Teaming | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/08 - SOC - 05 Operacionalizando ATT&CK y Purple Teaming\|08 - SOC: Operacionalización y Purple Team]] |
| **09** | MITRE D3FEND | Matriz de contramedidas defensivas: Model, Harden, Detect, Isolate, Deceive, Evict | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/09 - SOC - 06 MITRE D3FEND Contramedidas Defensivas\|09 - SOC: MITRE D3FEND Contramedidas]] |
| **10** | Caso de Estudio Real | Análisis integral de un incidente real, mapeo de TTPs en matriz y plan de mitigaciones | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/10 - SOC - 07 Caso de Estudio Real\|10 - SOC: Caso de Estudio Real]] |

---

## 🔭 3. SIEM y Wazuh

| # | Práctica | Arquitectura y Operaciones | Enlace a la Práctica |
| :-: | :--- | :--- | :--- |
| **11** | Introducción a Wazuh | Conceptos de SIEM, arquitectura modular (agente, servidor central, indexer, dashboard) | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/11 - SIEM - 01 Introducción a Wazuh y Arquitectura SIEM\|11 - SIEM: Introducción a Wazuh]] |
| **12** | Despliegue e Instalación | Despliegue de appliance OVA en VirtualBox, configuración de red estática y enrolamiento de agentes | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/12 - SIEM - 02 Despliegue de Wazuh (OVA) y Agentes\|12 - SIEM: Instalación y Agentes]] |
| **13** | Vulnerabilidades y SCA | Módulo Syscollector, detección de CVEs y evaluación de configuración de seguridad (CIS Benchmarks) | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/13 - SIEM - 03 Detección de Vulnerabilidades y SCA\|13 - SIEM: Vulnerabilidades y SCA]] |
| **14** | Detección de Malware | FIM (File Integrity Monitoring), Who-data, integración VirusTotal, YARA rules y Sysmon | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/14 - SIEM - 04 Detección de Malware (FIM, YARA, VirusTotal)\|14 - SIEM: Detección de Malware]] |
| **15** | Respuesta ante Incidentes | Configuración de Active Response: bloqueo IP automático tras ataques de fuerza bruta RDP | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/15 - SIEM - 05 Respuesta ante Incidentes con Active Response\|15 - SIEM: Active Response Automatizado]] |
| **16** | Wazuh como IDS con Suricata | Instalación de Suricata en Debian 12, ingestión de logs en Wazuh y detección de ataques web (SQLi/XSS) | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/16 - SIEM - 06 Integración de Wazuh como IDS con Suricata\|16 - SIEM: IDS Suricata Integrado]] |

---

## 🔍 4. Análisis Forense Digital

| # | Práctica | Tipo de Evidencia | Herramientas Utilizadas | Enlace a la Práctica |
| :-: | :--- | :--- | :--- | :--- |
| **17** | Adquisición de RAM | Volátil | FTK Imager, DumpIt, `pagefile.sys`, RFC 3227 | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/17 - Forense - 01 Adquisición de RAM e Información Volátil\|17 - Forense: Adquisición de RAM]] |
| **18** | Adquisición de Disco Duro | No Volátil | FTK Imager (formato E01, metadatos, verificación SHA-1/MD5) | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/18 - Forense - 02 Adquisición de Disco Duro (E01) con FTK Imager\|18 - Forense: Adquisición HDD E01]] |
| **19** | Análisis de Volcados de RAM | Volátil | Volatility 2 & 3: árbol de procesos (`pstree`), `malfind`, `hashdump`, `lsadump` | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/19 - Forense - 03 Análisis de Volcados de RAM con Volatility\|19 - Forense: Volatility RAM Analysis]] |
| **20** | Análisis de Disco con FTK Imager | Sistema de Archivos | Apertura de imagen NIST, inspección de `$MFT`, particiones y archivos borrados | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/20 - Forense - 04 Análisis de Disco con FTK Imager\|20 - Forense: Análisis con FTK Imager]] |
| **21** | Análisis Integral con Autopsy | Artefactos OS | Creación de caso, ingest modules, Keyword Search, web artifacts, timeline | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/21 - Forense - 05 Análisis Integral de Disco con Autopsy\|21 - Forense: Autopsy Integral Analysis]] |
| **22** | Análisis Forense con OSForensics | Artefactos Windows | Exploración de registros, actividad reciente, contraseñas y cuentas | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/22 - Forense - 06 Análisis Forense con OSForensics\|22 - Forense: OSForensics Windows]] |
| **23** | Práctica: Forense de Disco | Caso NIST | 20 preguntas técnicas evaluables sobre la imagen `nps-2009-domexusers.E01` | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/23 - Forense - 07 Práctica Evaluable Análisis Forense de Disco\|23 - Forense: Práctica HDD NIST (20 Qs)]] |
| **24** | Forense Móvil (iOS / iPhone) | Dispositivos Móviles | iBackup Viewer, iPhone Analyzer, bases de datos SQLite (`sms.db`, `CallHistory.storedata`) | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/24 - Forense - 08 Análisis Forense de Dispositivos Móviles (iOS)\|24 - Forense: Móviles iPhone / iOS]] |
| **25** | Práctica: Forense Móvil | Caso iPhone | 20 preguntas interactivas de extracción (IMEI, chats WhatsApp, notas de voz, WiFi) | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/25 - Forense - 09 Práctica Evaluable Forense Móvil (iPhone)\|25 - Forense: Práctica Móvil (20 Qs)]] |

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]]

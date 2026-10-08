---
title: "🔍 Práctica: Mapeo de Rutas de Ataque en Active Directory con BloodHound"
aliases:
  - Mapeo de Rutas de Ataque en Active Directory con BloodHound
  - PoC bloodhound
tags:
  - ifct0109/pocs
  - auditoria
  - bloodhound
  - sharphound
  - active-directory
  - privesc
---

# 🔍 Práctica: Mapeo de Rutas de Ataque en Active Directory con BloodHound

> [!abstract] Resumen del Laboratorio
> Arsenal técnico de auditoría y pentesting del Certificado IFCT0109: reconocimiento de infraestructura, captura y análisis de hashes, explotación, pivoting perimetral e investigación de desbordamientos de memoria en entornos controlados.

---

# 🩸 Módulo BloodHound

## 🔍 Análisis de Seguridad de Active Directory

Domina BloodHound para identificar rutas de ataque y vulnerabilidades en dominios de Active Directory

En este módulo aprenderás a instalar, configurar y utilizar **BloodHound**, una herramienta de código abierto que utiliza teoría de grafos para analizar la seguridad de los dominios de Active Directory y Azure.

### 🎯 Objetivos del Módulo:

* ✅ Instalar BloodHound (Legacy y Community Edition)

* ✅ Recolectar información con SharpHound y BloodHound.py

* ✅ Analizar datos y detectar rutas de escalada de privilegios

* ✅ Comprender los edges y relaciones de Active Directory

* ✅ Utilizar consultas Cypher personalizadas

* ✅ Conocer herramientas alternativas (ldapdomaindump, PywerView)

* ✅ Realizar análisis completos de seguridad en AD

### 💡 ¿Qué Aprenderás?

**BloodHound** te permite visualizar relaciones complejas en Active Directory que serían imposibles de identificar manualmente. Descubrirás:

* 🔴 Caminos de escalada de privilegios hasta Domain Admin

* 🟠 Vulnerabilidades como AS-REP Roasting y Kerberoasting

* 🟡 Permisos peligrosos (GenericAll, WriteDacl, ForceChangePassword)

* 🟢 Usuarios y grupos con acceso privilegiado

* 🔵 Delegaciones inseguras y configuraciones erróneas

## 📚 Prácticas del Módulo

      🔧 Instalación y Configuración

        Práctica 01
        💿
        Instalación de BloodHound

          Instalación de BloodHound en múltiples plataformas: Docker Legacy, instalación nativa en Kali, y Community Edition con Docker. Configuración de Neo4j y preparación del entorno.

          Instalación
          Docker
          Neo4j
          Kali Linux

        → Ir a la práctica

        Práctica 02
        📊
        Recolección de Información

          Uso de SharpHound (PowerShell y C#) para recolectar información de Active Directory desde Windows. BloodHound.py para recolección desde Linux. Práctica con VM BIG.ova.

          SharpHound
          BloodHound.py
          Recolección
          Evil-WinRM

        → Ir a la práctica

      🔍 Análisis y Explotación

        Práctica 03
        🎯
        Análisis de Datos

          Análisis completo de datos con BloodHound. Pre-Built Queries, identificación de rutas de ataque, escalada de privilegios práctica con adm_puffy. Glosario completo de edges y consultas Cypher.

          Análisis
          Escalada
          DCSync
          Cypher

        → Ir a la práctica

        Práctica 04
        🔧
        Herramientas Alternativas

          Herramientas complementarias para análisis de Active Directory: ldapdomaindump (HTML), PywerView (CLI), y BloodHound.py. Comparación de ventajas y casos de uso.

          ldapdomaindump
          PywerView
          Alternativas
          CLI

        → Ir a la práctica

### 📥 Zona de Descargas - Recursos del Módulo

Descarga todos los recursos necesarios para completar las prácticas del módulo BloodHound:

#### 🔧 Recolectores (Collectors)

#### 📦 SharpHound.exe

Recolector binario para Windows (C#). Compatible con BloodHound Legacy.

            ⬇️ Descargar SharpHound.exe
            (1.0 MB)

#### 📜 SharpHound.ps1

Script PowerShell para recolección en Windows. Ideal para entornos con PowerShell habilitado.

            ⬇️ Descargar SharpHound.ps1
            (1.2 MB)

#### 📦 Datos de Ejemplo (Recursos)

#### 🩸 20240812121134_BloodHound.zip

**Archivo principal para la práctica.** Datos recolectados de la VM BIG.ova con SharpHound.

            ⬇️ Descargar Datos BIG.ova
            (11 KB)

#### 🌐 20240811202959_BloodHound_ce.zip

Datos de ejemplo para BloodHound Community Edition. Compatible con versión 5.x.

            ⬇️ Descargar Datos CE
            (23 KB)

#### 📊 ad_example_data_ce.zip

Datos de ejemplo de Active Directory para Community Edition. Dominio WRAITH.CORP.

            ⬇️ Descargar Ejemplo AD
            (149 KB)

#### 🐳 docker_bloodhound.7z

Entorno Docker completo con BloodHound Legacy preconfigurado. Incluye script de inicio.

            ⬇️ Descargar Docker BloodHound
            (95 MB)

**💡 Consejo:** Para la práctica principal utiliza `20240812121134_BloodHound.zip`. Los demás archivos son opcionales para explorar diferentes escenarios.

## 📊 Resumen del Módulo

| Nº | Práctica | Herramienta | Dificultad |
| --- | --- | --- | --- |
| 01 | Instalación de BloodHound | BloodHound + Neo4j + Docker | ⭐⭐ |
| 02 | Recolección de Información | SharpHound + BloodHound.py | ⭐⭐⭐ |
| 03 | Análisis de Datos | BloodHound + Cypher | ⭐⭐⭐⭐ |
| 04 | Herramientas Alternativas | ldapdomaindump + PywerView | ⭐⭐ |

## 🔑 Conceptos Clave del Módulo

#### 🧮 Teoría de Grafos:

* Nodo: Objeto de AD (usuario, grupo, equipo, OU)

* Edge (Arista): Relación o permiso entre nodos

* Camino: Secuencia de edges que conectan dos nodos

* Shortest Path: Camino más corto para escalada de privilegios

#### 🔴 Edges Críticos:

| Edge | Descripción |
| --- | --- |
| `GenericAll` | Control total sobre el objeto |
| `WriteDacl` | Modificar permisos (ACL) del objeto |
| `ForceChangePassword` | Cambiar contraseña sin conocer la actual |
| `AddAllowedToAct` | Configurar RBCD para escalada |
| `AdminTo` | Administrador local del equipo |

#### ⚡ Ataques Comunes:

* AS-REP Roasting: Obtener TGT de usuarios sin pre-autenticación Kerberos

* Kerberoasting: Extraer tickets de servicio y crackear contraseñas

* DCSync: Replicar hashes simulando un Domain Controller

* Pass-the-Hash: Autenticación con hash NTLM sin contraseña

* Golden Ticket: Forjar tickets Kerberos con hash de KRBTGT

## 🎓 Recursos Adicionales

#### 📚 Documentación Oficial

* BloodHound Docs

* GitHub Legacy

* GitHub Community Edition

#### 🔧 Herramientas

* SharpHound

* BloodHound.py

* PowerView

#### 🎥 Contenido Educativo

* BloodHound Tutorial

* Netwrix Blog

* Pentest Partners

## 🚀 ¡Comienza Tu Aprendizaje!

        🩸 Comenzar con la Práctica 01

4 prácticas completas | Recursos descargables | Guías paso a paso

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🧪 [[02 - MODULOS/04 - Hacking Ético|Módulo Central de Hacking Ético]]

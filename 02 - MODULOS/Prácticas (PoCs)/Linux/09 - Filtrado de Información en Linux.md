---
title: "🔍 Filtrado de Información en Linux — Expresiones Regulares y Pipes"
aliases:
  - Filtrado de Informacion Linux
  - PoC Filtros Linux
  - Grep Sed Awk Linux
tags:
  - ifct0109/pocs
  - linux
  - filtros
  - grep
  - regex
  - text-processing
---

# 🔍 Filtrado de Información en Linux — Expresiones Regulares y Procesamiento de Texto

> [!abstract] Resumen del Laboratorio
> En auditorías de seguridad y administración de sistemas, el filtrado eficiente de flujos de texto, ficheros de registro (logs) y salidas de herramientas de escaneo es una habilidad indispensable. Esta práctica cubre exhaustivamente el uso de `grep`, `egrep`, expresiones regulares (Regex), `cut`, `tr`, `sort`, `uniq` y redirecciones para procesar y aislar datos críticos con precisión forense.
> 
> 📦 **Recurso de Laboratorio:** El archivo comprimido con los datasets de prueba se encuentra disponible localmente en `archivos_descargados/paquetes_7z/recursos_filtros.7z`.

---

# 🔍 Filtrado de Información

Técnicas avanzadas de extracción y procesado de datos en Pentesting

grep · cut · tr · awk · sed · column · wc

Manuel Fernández Ramos  ·  Módulo Avanzado v1.0

## 📋 Descripción de la Práctica

> [!info] 🎯 Contenidos
>      En un pentest real, una vez dentro de un sistema obtienes grandes cantidades de información en bruto: usuarios del sistema, hashes de contraseñas, conexiones de red, logs de autenticación, tráfico HTTP capturado, resultados de herramientas de enumeración como  enum4linux ...
>      El objetivo de esta práctica es que domines las herramientas de línea de comandos que te permiten  extraer, filtrar y transformar  esa información de forma eficiente, sin depender de ninguna herramienta gráfica.
>       Trabajarás siempre sobre el mismo archivo:  `datos.txt`, que contiene datos simulados y reales de un escenario de pentesting.

    **⚙️ Antes de empezar:** Descarga el archivo `recursos.7z`, descomprímelo con `7z x recursos.7z` en tu entorno Linux (WSL, Docker o Kali) y trabaja desde ese directorio. Todos los comandos de la práctica asumen que estás ahí.

## 📥 Archivo de Datos

### 📦 recursos.7z

        ▸ Opción 1 — wget (recomendado)
        wget https://cursosmfr.es/ifct0109/pocs/linux/filtros/recursos.7z

        ▸ Opción 2 — curl
        curl -O https://cursosmfr.es/ifct0109/pocs/linux/filtros/recursos.7z

        ▸ Opción 3 — descarga directa
        ⬇️ Descargar recursos.7z

        ▸ Descomprimir
        7z x recursos.7z

      ▸ Verificar que tienes todos los archivos
      ls -lh *.txt *.log

> [!info] 📂 ¿Qué contiene el archivo?
>
>
> | Archivo | Contenido |
> | --- | --- |
> | `passwd.txt` | Usuarios del sistema con distintos shells: bash, sh, rbash, git-shell, false, nologin... |
> | `shadow.txt` | Hashes de contraseñas en distintos formatos: $1 (MD5), $5 (SHA-256), $6 (SHA-512), bloqueadas |
> | `netstat.txt` | Conexiones de red activas: servicios en LISTEN, conexiones ESTABLISHED y UDP |
> | `auth.log` | Log de autenticación con logins exitosos, fallidos, fuerza bruta, sudo y eventos sospechosos |
> | `trafico.txt` | Tráfico HTTP capturado: tokens JWT, cookies, CSRF, credenciales, subida de webshell |
> | `nmap.txt` | Escaneo de varios hosts: Linux, Samba y controlador de dominio Windows |
> | `tokens.txt` | Tokens JWT, API keys, cookies de sesión, CSRF y credenciales en claro |
> | `users.txt` | Salida real de enum4linux: usuarios y grupos de un dominio Windows (SOUPEDECODE) |

## 📚 Secciones de la Práctica

      🟢

        grep
        ✅ Disponible

      🔵

        egrep + regex
        ✅ Disponible

      ✂️

        cut
        ✅ Disponible

      🔄

        tr
        ✅ Disponible

      📊

        column
        ✅ Disponible

      ⚙️

        awk
        ✅ Disponible

      🔧

        sed
        ✅ Disponible

## 🟢 Sección 1 — `grep`

  6 ejemplos

### ¿Qué es grep?

`grep` (*Global Regular Expression Print*) busca líneas que coincidan con un patrón dentro de un archivo o de la salida de otro comando. Es una de las herramientas más usadas en pentesting para filtrar rápidamente la información relevante de entre grandes volúmenes de datos.

Sintaxis básica:

    grep [opciones] "patrón" archivo.txt

    cat archivo.txt | grep [opciones] "patrón"

### Parámetros más usados

| Parámetro | Descripción | Ejemplo |
| --- | --- | --- |
| `-v` | Invierte el filtro — muestra las líneas que NO coinciden con el patrón | `grep -v "nologin"` |
| `-e` | Permite indicar varios patrones de búsqueda en un mismo comando | `grep -e "pat1" -e "pat2"` |
| `-i` | Ignora mayúsculas y minúsculas en la búsqueda | `grep -i "ROOT"` |
| `-c` | Muestra el número de líneas que coinciden, no las líneas en sí | `grep -c "Failed"` |
| `-A n` | Muestra n líneas después de cada coincidencia | `grep -A 3 "root"` |
| `-B n` | Muestra n líneas antes de cada coincidencia | `grep -B 3 "root"` |
| `-C n` | Muestra n líneas antes y después de cada coincidencia | `grep -C 3 "root"` |
| `""` | Coincidencia exacta de palabra completa, no subcadena | `grep ""` |

### Ejemplos con passwd.txt

#### Ejemplo 1 — Búsqueda básica de cadena

Muestra todas las líneas del archivo que contengan la cadena `/bin/bash`. En el bloque de `/etc/passwd` esto nos da directamente los usuarios que tienen una shell de inicio de sesión real.

      cat passwd.txt | grep "/bin/bash"

#### Ejemplo 2 — `-v` Excluir coincidencias

Con `-v` el comportamiento se invierte: grep muestra las líneas que **no** contienen el patrón. Útil para eliminar el ruido de cuentas de sistema que no tienen sesión interactiva. Se pueden combinar varios patrones en uno usando `\|` como separador (equivale a OR).

      cat passwd.txt | grep -v "false\|nologin"

#### Ejemplo 3 — `-e` Varios patrones de exclusión

Con `-e` se puede indicar cada patrón por separado, lo que resulta más legible cuando hay varios. Se puede combinar con `-v` para excluir múltiples cadenas a la vez.

      cat passwd.txt | grep -v -e "false" -e "nologin" -e "postgres"

      Los ejemplos 2 y 3 hacen lo mismo pero con distinta sintaxis. `-e` es preferible cuando los patrones son muchos o contienen caracteres especiales, ya que evita tener que escaparlos.

#### Ejemplo 4 — `-i` Ignorar mayúsculas y minúsculas

Por defecto grep distingue entre mayúsculas y minúsculas. Con `-i` la búsqueda es insensible a esa distinción. Útil cuando no sabes cómo está escrito exactamente el dato que buscas.

      cat passwd.txt | grep -i "Bin"

      Prueba también sin `-i` para ver la diferencia en los resultados.

#### Ejemplo 5 — Coincidencia exacta de palabra

Sin delimitadores de palabra, buscar `bin` devuelve también resultados como `/sbin` o `/bin/bash`. Con `\` forzamos que sea una palabra completa, sin caracteres alfanuméricos pegados a los lados.

      cat passwd.txt | grep "\<bin\>"

#### Ejemplo 6 — `-C` Mostrar contexto alrededor de la coincidencia

A veces no basta con ver la línea que coincide, sino también las que la rodean para entender el contexto. Con `-C n` grep muestra *n* líneas antes y después de cada resultado. También existen `-A n` (solo después) y `-B n` (solo antes).

      cat passwd.txt | grep -C 3 "root"

      Muy útil en logs: si encuentras un evento sospechoso, con `-C` ves qué ocurrió justo antes y después sin tener que abrir el archivo completo.

#### Otros ejemplos

      cat users.txt | grep -i "Administrator"

      cat users.txt | grep -i -C3 "Administrator"

      cat users.txt | grep -v -i "Administrator"

## 🔵 Sección 2 — `egrep` y Expresiones Regulares

  7 ejemplos

### ¿Qué es egrep?

`egrep` es equivalente a `grep -E` y activa las **expresiones regulares extendidas (ERE)**. Esto permite usar patrones más potentes sin necesidad de escapar ciertos caracteres como `|`, `+` o `?`.

En pentesting las expresiones regulares son clave para buscar patrones concretos en grandes volúmenes de datos: usuarios que siguen un patrón de nombre, servicios que escuchan en un rango de puertos, IPs de una subred específica, etc.

Para practicar y construir expresiones regulares: https://regexr.com/

### Metacaracteres principales

| Metacarácter | Significado | Ejemplo |
| --- | --- | --- |
| `[abc]` | Coincide con cualquiera de los caracteres dentro de los corchetes | `egrep "^[abc]"` |
| `[a-z]` | Rango de caracteres — coincide con cualquier letra entre a y z | `egrep "^[a-e]"` |
| `^` | Ancla de inicio — el patrón debe ocurrir al principio de la línea | `egrep "^root"` |
| `[^abc]` | Negación — coincide con cualquier carácter que no esté en los corchetes | `egrep "^[^a-m]"` |
| `$` | Ancla de fin — el patrón debe ocurrir al final de la línea | `egrep "bash$"` |
| `.` | Comodín — coincide con cualquier carácter individual (excepto salto de línea) | `egrep "/bin/....$"` |
| `|` | OR — coincide con el patrón de la izquierda o el de la derecha | `egrep "bash$|/sh$"` |

### Ejemplos con passwd.txt

#### Ejemplo 1 — `[]` Usuarios que empiecen por a, b o c

Los corchetes permiten indicar un conjunto de caracteres válidos en esa posición. Combinado con `^` (inicio de línea) filtramos solo los usuarios cuyo nombre empiece por alguna de esas letras.

      cat passwd.txt | egrep "^[abc]"

#### Ejemplo 2 — `[a-e]` Rango alfabético al inicio

Con el guion dentro de los corchetes definimos un rango. `[a-e]` equivale a escribir `[abcde]`. Se puede usar tanto con letras como con números.

      cat passwd.txt | egrep "^[a-e]"

#### Ejemplo 3 — `^` Inicio de línea exacto

El acento circunflejo fuera de los corchetes ancla el patrón al inicio de la línea. Buscar `^root` devuelve solo la línea que comienza exactamente por `root`, no líneas donde aparezca en otra posición.

      cat passwd.txt | egrep "^root"

      Prueba sin el `^` para ver la diferencia: `egrep "root"` devuelve también líneas donde root aparece en cualquier posición, como el directorio `/root`.

#### Ejemplo 4 — `[^]` Excluir un rango al inicio

Dentro de los corchetes, el `^` actúa como negación. `[^a-m]` significa "cualquier carácter que no esté entre la a y la m". Útil para ver qué usuarios quedan fuera de un rango de nombres.

      cat passwd.txt | egrep "^[^a-m]"

#### Ejemplo 5 — `$` Final de línea

El signo `$` ancla el patrón al final de la línea. Combinado con una ruta de shell nos permite filtrar exactamente los usuarios que tienen esa shell asignada, sin falsos positivos.

      cat passwd.txt | egrep "/bin/bash$"

#### Ejemplo 6 — `.` Comodín de un carácter

El punto coincide con cualquier carácter individual. `/bin/....$` busca líneas que acaben en `/bin/` seguido de exactamente 4 caracteres cualquiera. Devuelve shells como `/bin/bash` y `/bin/false`, ambas de 4 letras.

      cat passwd.txt | egrep "/bin/....$"

#### Ejemplo 7 — `|` OR entre patrones

En egrep el carácter `|` no necesita escaparse (a diferencia de grep básico donde se usa `\|`). Permite buscar líneas que coincidan con uno u otro patrón en un solo comando.

      cat passwd.txt | egrep "/bin/bash$|/bin/sh$"

      Compara con el equivalente en grep básico: `grep "/bin/bash$\|/bin/sh$"`. En egrep la sintaxis es más limpia y legible.

## ✂️ Sección 3 — `cut`

  6 ejemplos

### ¿Qué es cut?

`cut` extrae columnas o campos concretos de cada línea de texto. Es especialmente útil cuando los datos están estructurados con un separador fijo, como los dos puntos en `/etc/passwd` o la barra invertida en la salida de `enum4linux`.

Sintaxis básica:

    cut -d "delimitador" -f número_de_campo

### Opciones principales

| Opción | Descripción | Ejemplo |
| --- | --- | --- |
| `-d "x"` | Define el carácter delimitador que separa los campos | `cut -d ":"` |
| `-f 1` | Extrae el campo número 1 (los campos empiezan en 1, no en 0) | `cut -f 1` |
| `-f 1,3` | Extrae los campos 1 y 3 | `cut -f 1,3` |
| `-f 1-4` | Extrae los campos del 1 al 4 inclusive | `cut -f 1-4` |

    **¿Cómo se numeran los campos?** cut divide cada línea por el delimitador y numera los trozos de izquierda a derecha empezando por 1. En la línea `root:x:0:0:root:/root:/bin/bash` con delimitador `:`, el campo 1 es `root`, el campo 6 es `/root` y el campo 7 es `/bin/bash`.

### Ejemplos con passwd.txt

#### Ejemplo 1 — Extraer solo el nombre de usuario

Combinamos `grep -v` para quedarnos solo con los usuarios reales descartando cuentas de sistema, y luego `cut` extrae el primer campo, que en el formato de `/etc/passwd` es siempre el nombre de usuario.

      cat passwd.txt | grep -v -e "false" -e "nologin" -e "postgres" | cut -d ":" -f1

#### Ejemplo 2 — Usuario y directorio home

Extraemos dos campos a la vez: el campo 1 (usuario) y el campo 6 (directorio home). Útil para saber rápidamente dónde buscar archivos de configuración, claves SSH o historiales de comandos de cada usuario.

      cat passwd.txt | grep "/bin/bash" | cut -d ":" -f1,6

#### Ejemplo 3 — Usuario, UID y shell

Extraemos tres campos: nombre (1), UID (3) y shell (7). Los usuarios con UID menor de 1000 son cuentas de sistema; los iguales o mayores a 1000 corresponden a usuarios humanos. La shell indica si la cuenta tiene acceso interactivo al sistema.

      cat passwd.txt | grep -v "nologin" | cut -d ":" -f1,3,7

### Ejemplos con users.txt

#### Ejemplo 4 — Aislar todos los usuarios del dominio

La salida de `enum4linux` tiene el formato `SOUPEDECODE\usuario`. Usamos `egrep` para filtrar las líneas de Domain Users y Domain Controllers, luego dos `cut` encadenados: el primero divide por `\` y toma lo que hay después, el segundo divide por espacio y toma solo el nombre de usuario.

      cat users.txt | egrep 'Domain Users|Domain Controllers' | cut -d'\' -f2 | cut -d' ' -f1

#### Ejemplo 5 — Extraer el nombre de todos los grupos

Las líneas de grupo tienen el formato `group:[NombreGrupo] rid:[0x...]`. Dividiendo por `[` tomamos la segunda parte, y luego dividiendo por `]` tomamos la primera, obteniendo solo el nombre limpio del grupo.

      cat users.txt | grep "^group:" | cut -d"[" -f2 | cut -d"]" -f1

#### Ejemplo 6 — Extraer los grupos administradores del dominio

Filtramos las líneas que contienen `Administrators'` y extraemos la parte después de la barra invertida. Nos da directamente las cuentas con privilegios de administrador en el dominio, información crítica en cualquier proceso de pentesting de Active Directory.

      cat users.txt | grep "Administrators'" | cut -d'\' -f2

## 🔄 Sección 4 — `tr`

  9 ejemplos

### ¿Qué es tr?

`tr` (*translate*) es una herramienta que trabaja carácter a carácter: reemplaza, elimina o comprime caracteres en la salida de un comando. A diferencia de `sed`, no trabaja con patrones complejos sino con caracteres individuales o rangos, lo que lo hace muy rápido y directo para limpiar y transformar texto.

Sintaxis básica:

    tr 'carácter_origen' 'carácter_destino'

    cat archivo.txt | tr 'origen' 'destino'

### Opciones principales

| Opción / Sintaxis | Descripción | Ejemplo |
| --- | --- | --- |
| `tr 'a' 'b'` | Reemplaza cada carácter del primer argumento por el correspondiente del segundo | `tr ':' ' '` |
| `tr -d 'x'` | Elimina todos los caracteres indicados | `tr -d '[]'` |
| `tr -s 'x'` | Comprime secuencias repetidas de un carácter en una sola ocurrencia | `tr -s ' '` |
| `tr 'a-z' 'A-Z'` | Convierte minúsculas a mayúsculas usando rangos | `tr 'a-z' 'A-Z'` |
| `tr 'A-Z' 'a-z'` | Convierte mayúsculas a minúsculas | `tr 'A-Z' 'a-z'` |

    **Importante:** `tr` no acepta archivos como argumento directamente, siempre trabaja con la entrada estándar. Por eso siempre se usa con `cat archivo |` o con redirección `tr 'a' 'b'

### Ejemplos con passwd.txt

#### Ejemplo 1 — Reemplazar `:` por espacios

El separador `:` de `/etc/passwd` hace la salida difícil de leer. Reemplazándolo por espacios la información queda mucho más clara de un vistazo, aunque se pierde la referencia exacta de qué campo es cada columna.

      cat passwd.txt | grep "/bin/bash" | tr ":" " "

#### Ejemplo 2 — Convertir nombres de usuario a mayúsculas

Útil para generar variantes de nombres de usuario en mayúsculas para wordlists o ataques de diccionario. Encadenamos `cut` para extraer solo el nombre y luego `tr` para la conversión.

      cat passwd.txt | grep "/bin/bash" | cut -d":" -f1 | tr 'a-z' 'A-Z'

#### Ejemplo 3 — Convertir nombres de usuario a minúsculas

El caso inverso. En entornos Windows los nombres de usuario no distinguen mayúsculas, pero en Linux sí. Normalizar a minúsculas es útil para comparar listas de usuarios de distintas fuentes.

      cat passwd.txt | grep "/bin/bash" | cut -d":" -f1 | tr 'A-Z' 'a-z'

### Ejemplos con netstat.txt

#### Ejemplo 4 — Reemplazar `/` por `:` en conexiones ESTABLISHED

La columna PID/Program de netstat usa `/` como separador entre el PID y el nombre del proceso. Reemplazándolo por `:` obtenemos un formato más limpio y fácil de procesar con otras herramientas.

      cat netstat.txt | grep "ESTABLISHED" | tr '/' ':'

#### Ejemplo 5 — Eliminar los `*` de los servicios en LISTEN

La columna Foreign-Address muestra `0.0.0.0:*` en los servicios en escucha. Con `tr -d` eliminamos el asterisco para obtener una salida más limpia, especialmente útil si vamos a procesar esa salida con `cut` o `awk` después.

      cat netstat.txt | grep "LISTEN" | tr -d '*'

#### Ejemplo 6 — Comprimir espacios múltiples con `-s`

La salida de netstat tiene columnas separadas por varios espacios para alinear visualmente los datos. Con `tr -s ' '` comprimimos todas las secuencias de espacios en uno solo, lo que facilita el procesado posterior con `cut` usando espacio como delimitador.

      cat netstat.txt | tr -s ' '

### Ejemplos con auth.log

#### Ejemplo 7 — Eliminar los corchetes de los PID

Los logs de autenticación incluyen el PID del proceso entre corchetes, como `sshd[1042]`. Eliminándolos con `tr -d` la salida queda más limpia para su posterior procesado.

      cat auth.log | grep "Accepted" | tr -d '[]'

#### Ejemplo 8 — Pasar los intentos fallidos a minúsculas

Normalizar a minúsculas es útil cuando vamos a comparar o contar cadenas y no queremos que las mayúsculas afecten al resultado. También facilita el procesado posterior con otras herramientas.

      cat auth.log | grep "Failed" | tr 'A-Z' 'a-z'

### Extra

#### Ejemplo 9 — Generar un hash MD5 limpio

El comando `md5sum` devuelve el hash seguido de un espacio y un guión. Con `tr -d` eliminamos esos caracteres sobrantes y obtenemos solo el hash limpio, listo para usar en scripts o comparaciones.

      echo -n "password" | md5sum | tr -d " -"

## 📊 Sección 5 — `column`

  6 ejemplos

### ¿Qué es column?

`column` formatea texto en columnas alineadas automáticamente. Cuando la salida de un comando tiene campos de distinto ancho, `column` calcula el ancho máximo de cada columna y los alinea, haciendo la información mucho más legible de un vistazo.

En pentesting es especialmente útil al final de un pipeline, justo antes de leer los resultados, para presentar datos como usuarios, puertos o conexiones de forma tabulada.

Sintaxis básica:

    column -t

    cat archivo.txt | column -t

### Opciones principales

| Opción | Descripción | Ejemplo |
| --- | --- | --- |
| `-t` | Crea una tabla alineada automáticamente detectando los campos por espacios | `column -t` |
| `-s "x"` | Define el delimitador de entrada (por defecto espacio o tabulador) | `column -s ":" -t` |
| `-s ":" -t` | Tabulación directa con delimitador personalizado, sin necesidad de `tr` previo | `column -s ":" -t` |

    **Consejo:** `column -t` siempre va al final del pipeline, después de haber filtrado y transformado los datos. Su único objetivo es hacer la salida legible, no procesar información.

### Ejemplos con passwd.txt

#### Ejemplo 1 — Tabular la salida completa

Con `-s ":"` le indicamos a `column` directamente que el delimitador de entrada es `:`, sin necesidad de pasar por `tr` antes. El resultado es un passwd perfectamente tabulado y fácil de leer.

      cat passwd.txt | column -s ":" -t

#### Ejemplo 2 — Solo usuarios reales tabulados

Filtramos primero con `grep` para quedarnos con los usuarios con shell real y usamos `-s ":"` para que `column` interprete directamente los dos puntos como separador. El resultado es una tabla limpia solo con las cuentas interactivas.

      cat passwd.txt | grep "/bin/bash" | column -s ":" -t

### Ejemplos con netstat.txt

#### Ejemplo 3 — Tabular las conexiones ESTABLISHED

Primero comprimimos los espacios múltiples con `tr -s` para que `column` pueda detectar correctamente los campos, y luego tabulamos. El resultado muestra claramente qué IPs están conectadas y a qué proceso.

      cat netstat.txt | grep "ESTABLISHED" | tr -s ' ' | column -t

#### Ejemplo 4 — Tabular los servicios en LISTEN

Misma técnica aplicada a los servicios en escucha. De un vistazo vemos todos los puertos abiertos, en qué dirección escuchan y qué proceso los gestiona, información crítica en la fase de reconocimiento.

      cat netstat.txt | grep "LISTEN" | tr -s ' ' | column -t

### Ejemplos con nmap.txt

#### Ejemplo 5 — Tabular los puertos abiertos

Filtramos las líneas con `/tcp` para quedarnos solo con los resultados de puertos y tabulamos. Obtenemos una tabla clara con puerto, estado, servicio y versión de cada uno.

      cat nmap.txt | grep "/tcp" | tr -s ' ' | column -t

### Ejemplos con users.txt

#### Ejemplo 6 — Tabular los grupos con su RID

Extraemos las líneas de grupos, eliminamos los dos puntos con `tr -d`, reemplazamos los corchetes por espacios y tabulamos. El resultado es una tabla limpia con el nombre de cada grupo y su RID correspondiente.

      cat users.txt | grep "^group:" | tr -d ':' | tr '[]' '  ' | column -t

## ⚙️ Sección 6 — `awk`

  7 ejemplos

### ¿Qué es awk?

`awk` es un lenguaje de procesado de texto orientado a campos. A diferencia de `cut`, que simplemente extrae columnas, `awk` puede filtrar, transformar y operar sobre los datos en una sola instrucción. Es una de las herramientas más potentes del shell para analizar información estructurada.

### ¿Cómo funciona?

`awk` lee el texto línea a línea y divide cada línea en campos usando un separador (por defecto el espacio). Cada campo es accesible mediante una variable numerada:

    awk '{print $1}'     # primer campo

    awk '{print $2}'     # segundo campo

    awk '{print $NF}'    # último campo (NF = Number of Fields)

    awk '{print $1, $3}'  # campos 1 y 3

Para definir un separador distinto al espacio se usa la opción `-F`:

    awk -F":" '{print $1}'    # usa : como separador

    awk -F"\" '{print $2}'   # usa \ como separador

`awk` también permite añadir **condiciones** antes de la acción. Solo procesa las líneas que cumplen la condición:

    awk -F":" '$3 > 999 {print $1}'    # solo si el campo 3 es mayor que 999

    awk -F":" '$7 == "/bin/bash" {print $1}'    # solo si el campo 7 es exactamente /bin/bash

### Referencia de variables y opciones

| Variable / Opción | Descripción |
| --- | --- |
| `$1, $2, $3...` | Campo número 1, 2, 3... de la línea actual (empiezan en 1) |
| `$NF` | Último campo de la línea (Number of Fields) |
| `$0` | La línea completa sin modificar |
| `NF` | Número total de campos en la línea actual |
| `NR` | Número de línea actual (útil para numerar resultados) |
| `-F "x"` | Define el separador de campos de entrada |
| `print` | Imprime los campos indicados separados por espacio |
| `condición {acción}` | Solo ejecuta la acción en las líneas que cumplen la condición |

### Ejemplos con passwd.txt

#### Ejemplo 1 — Mostrar solo el nombre de usuario

Usando `-F":"` definimos los dos puntos como separador y `$1` extrae el primer campo de cada línea, que en `/etc/passwd` siempre es el nombre de usuario.

      cat passwd.txt | awk -F":" '{print $1}'

#### Ejemplo 2 — Mostrar usuario y shell

`$1` es el nombre de usuario y `$NF` siempre apunta al último campo, que en passwd es la shell. Esta combinación funciona aunque el número de campos varíe entre líneas.

      cat passwd.txt | awk -F":" '{print $1, $NF}'

#### Ejemplo 3 — Mostrar usuario, UID y home

Extraemos tres campos en una sola instrucción: nombre (1), UID (3) y directorio home (6). Información clave para localizar archivos de cada usuario durante un pentest.

      cat passwd.txt | awk -F":" '{print $1, $3, $6}'

#### Ejemplo 4 — Solo usuarios humanos (UID > 999)

Añadimos una condición antes de la acción: `$3 > 999` filtra solo las líneas donde el campo 3 (UID) es mayor que 999. En Linux los usuarios humanos tienen UID igual o superior a 1000. Con una sola línea obtenemos usuario, UID y shell solo de cuentas reales.

      cat passwd.txt | awk -F":" '$3 > 999 {print $1, $3, $7}'

#### Ejemplo 5 — Solo usuarios con bash como shell

La condición `$7 == "/bin/bash"` actúa como un filtro exacto sobre el campo 7. A diferencia de `grep "/bin/bash"`, aquí la comparación es sobre el campo concreto, no sobre cualquier parte de la línea, evitando falsos positivos.

      cat passwd.txt | awk -F":" '$7 == "/bin/bash" {print $1, $7}'

### Más ejemplos con passwd.txt

#### Ejemplo 6 — Numerar cada línea con NR

`NR` (*Number of Record*) contiene el número de línea actual. Imprimiéndolo junto al nombre de usuario obtenemos un listado numerado, útil para contar rápidamente cuántos usuarios hay o para referenciar una entrada concreta.

      cat passwd.txt | awk -F":" '{print NR " " $1}'

#### Ejemplo 7 — Añadir etiquetas a los campos

`awk` permite concatenar texto literal con los campos usando comillas. Esto genera una salida autodescriptiva muy útil para informes o para cuando la salida va a ser leída por otra persona.

      cat passwd.txt | awk -F":" '{print "usuario: " $1 " UID: " $3}'

## 🔧 Sección 7 — `sed`

  8 ejemplos

### ¿Qué es sed?

`sed` (*Stream EDitor*) es un editor de texto que trabaja en tiempo real sobre la salida de un comando o sobre un archivo, línea a línea, sin necesidad de abrirlo. Es la herramienta más potente para **sustituir, eliminar, extraer e insertar** texto de forma precisa.

A diferencia de `tr` que trabaja carácter a carácter, `sed` trabaja con **patrones y expresiones regulares**, lo que le da una potencia mucho mayor.

Sintaxis básica:

    sed 'comando' archivo.txt

    cat archivo.txt | sed 'comando'

### El comando de sustitución `s`

Es el más usado. Su estructura es siempre la misma:

    sed 's/patron/reemplazo/flags'

      # s        → sustitución

      # /        → separador (puede ser cualquier carácter)

      # patron   → lo que buscamos

      # reemplazo → con qué lo sustituimos

      # flags    → g (todas), 2 (segunda ocurrencia), i (sin case), p (imprimir)

### Grupos de captura

Con `\(...\)` capturamos partes del patrón y las referenciamos en el reemplazo con `\1`, `\2`, etc. Esto permite extraer exactamente la parte que nos interesa de una línea compleja.

    sed 's/.*valor="\([^"]*\)".*/\1/'

      # .*       → cualquier cosa antes de valor="

      # \(       → abre el grupo de captura

      # [^"]*    → todo lo que no sea comilla (el valor)

      # \)       → cierra el grupo de captura

      # .*       → cualquier cosa después

      # \1       → imprime solo lo capturado por el grupo

### Opciones y comandos principales

| Comando / Opción | Descripción | Ejemplo |
| --- | --- | --- |
| `s/pat/repl/` | Sustituye la primera ocurrencia por línea | `sed 's/root/ROOT/'` |
| `s/pat/repl/g` | Sustituye todas las ocurrencias de la línea | `sed 's/:/|/g'` |
| `s/pat/repl/2` | Sustituye solo la segunda ocurrencia | `sed 's/:/|/2'` |
| `s/pat/repl/i` | Ignora mayúsculas y minúsculas | `sed 's/root/ROOT/i'` |
| `/pat/d` | Elimina las líneas que contienen el patrón | `sed '/nologin/d'` |
| `-n 'Np'` | Imprime solo la línea N (`-n` suprime la salida automática) | `sed -n '3p'` |
| `-n 'N,Mp'` | Imprime desde la línea N hasta la M | `sed -n '1,5p'` |
| `-e 'cmd1' -e 'cmd2'` | Ejecuta varios comandos en una sola llamada | `sed -e 's/a/A/' -e 's/b/B/'` |
| `-i` | Modifica el archivo directamente (in-place) | `sed -i 's/old/new/g' file` |

### Ejemplos con passwd.txt

#### Ejemplo 1 — Sustituir la shell bash por sh

Reemplazamos `/bin/bash` por `/bin/sh` en la salida. Las barras del patrón deben escaparse con `\` ya que `/` es el separador de sed. Sin el flag `g` sustituye solo la primera ocurrencia por línea, suficiente aquí ya que la shell aparece una sola vez.

      cat passwd.txt | sed 's/\/bin\/bash/\/bin\/sh/'

#### Ejemplo 2 — Sustituir el separador `:` por `|`

Reemplazamos todos los `:` de cada línea por `|` para hacer la salida más legible. El flag `g` es imprescindible ya que hay varios `:` en cada línea de passwd.

      cat passwd.txt | sed 's/:/ | /g'

#### Ejemplo 3 — Eliminar líneas con `d`

El comando `d` elimina de la salida todas las líneas que contienen el patrón. Útil para limpiar entradas de cuentas de sistema que no tienen sesión interactiva.

      cat passwd.txt | sed '/nologin/d'

#### Ejemplo 4 — Imprimir solo las líneas 1 a 5

`-n` suprime la salida automática de todas las líneas y `p` imprime solo el rango indicado. Útil para ver una muestra del archivo sin volcar todo su contenido.

      cat passwd.txt | sed -n '1,5p'

### Ejemplos con auth.log

#### Ejemplo 5 — Extraer solo la hora de los logins exitosos

Encadenamos dos `sed`: el primero elimina la fecha (`Apr [0-9]*`) y el segundo elimina todo desde `server` en adelante. El resultado es solo la hora de cada login aceptado.

      cat auth.log | grep "Accepted" | sed 's/Apr [0-9]* //' | sed 's/ server.*//'

#### Ejemplo 6 — Anonimizar la IP del atacante

Reemplazamos la IP del atacante por la cadena `ATACANTE`. Los puntos deben escaparse con `\.` para que sed los trate como puntos literales y no como comodines de expresión regular.

      cat auth.log | sed 's/10\.10\.10\.99/ATACANTE/g'

### Ejemplos con trafico.txt

#### Ejemplo 7 — Extraer el token CSRF con grupos de captura

Uso avanzado de `sed`: el patrón `.*value="` consume todo hasta el atributo, `\([^"]*\)` captura el valor entre comillas y `.*` consume el resto. En el reemplazo `\1` imprime solo lo capturado, descartando el resto de la línea.

      cat trafico.txt | grep 'name="csrf"' | sed 's/.*value="\([^"]*\)".*/\1/'

      **Desglose:** `.*value="` consume todo lo anterior  ·
      `\(` abre el grupo  ·
      `[^"]*` captura lo que no es comilla  ·
      `\)` cierra el grupo  ·
      `".*` consume el resto  ·
      `\1` imprime solo lo capturado

#### Ejemplo 8 — Extraer el Bearer token

Filtramos la línea con `Bearer` y usamos `sed` para eliminar todo lo que hay antes de `Bearer`, dejando solo el token JWT limpio.

      cat trafico.txt | grep "Bearer" | sed 's/.*Bearer //'

Curso Linux para Pentesting  ·  Manuel Fernández Ramos  ·  v1.0

---

## 🔗 Enlaces y Referencias Cruzadas
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice del Módulo Linux]]
* 📜 [[02 - MODULOS/Prácticas (PoCs)/Linux/08 - Curso de Bash Scripting en Linux|Curso de Bash Scripting]]
* 📄 [[03 - EXTRA/Documentos/Linux Comandos Esenciales|CheatSheet de Comandos de Linux]]

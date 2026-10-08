---
title: "📜 Curso de Bash Scripting en Linux — Automatización para Pentesting"
aliases:
  - Bash Scripting Linux
  - Scripting Pentesting
  - PoC Bash Scripting
tags:
  - ifct0109/pocs
  - linux
  - bash
  - scripting
  - automatizacion
---

# 📜 Curso de Bash Scripting en Linux — Automatización para Pentesting

> [!abstract] Resumen del Módulo
> La automatización de tareas mediante scripts en Bash permite a los analistas de seguridad y administradores optimizar escaneos, parsear respuestas de red y ejecutar rutinas complejas sin intervención manual. Este manual cubre desde la sintaxis base (shebang, permisos de ejecución), variables locales, de entorno y permanentes, hasta control de flujo y desarrollo de herramientas propias en consola.

---

# 🐚 Curso Básico de Scripting en Linux

## 📖 Introducción

El **Bash scripting** es una herramienta fundamental para cualquier usuario de sistemas operativos Unix, como Linux y macOS. Esta técnica permite a los usuarios automatizar tareas repetitivas, simplificar procesos complejos y mejorar la eficiencia operativa mediante la escritura de scripts, que son simplemente archivos de texto que contienen una secuencia de comandos de shell.

## ✨ Ventajas del Bash Scripting

El Bash scripting ofrece numerosas ventajas que lo convierten en una herramienta indispensable para administradores de sistemas, desarrolladores y usuarios avanzados. A continuación, se detallan algunas de las principales ventajas:

### 1. Automatización de Tareas Repetitivas

La capacidad de automatizar tareas repetitivas es una de las mayores ventajas del Bash scripting. En lugar de realizar manualmente las mismas acciones una y otra vez, un script puede ejecutarlas automáticamente, ahorrando tiempo y reduciendo la posibilidad de errores humanos.

### 2. Facilidad de Uso y Acceso

El Bash shell está preinstalado en la mayoría de los sistemas Unix, lo que significa que no es necesario instalar software adicional para empezar a escribir scripts. Además, Bash utiliza una sintaxis clara y comprensible, lo que facilita su aprendizaje incluso para aquellos con poca experiencia en programación.

### 3. Interoperabilidad y Flexibilidad

Los scripts Bash pueden interactuar fácilmente con otros programas y utilidades del sistema, permitiendo una gran flexibilidad. Puedes combinar comandos de diferentes programas, manipular archivos, gestionar procesos y mucho más, todo dentro de un mismo script.

### 4. Portabilidad

Los scripts Bash son altamente portátiles. Un script escrito en Bash puede ejecutarse en cualquier sistema que tenga Bash instalado, lo que incluye la mayoría de las distribuciones de Linux y macOS. Esto hace que los scripts sean fáciles de compartir y reutilizar en diferentes entornos.

### 5. Ahorro de Recursos

A diferencia de algunos lenguajes de programación más pesados, los scripts Bash suelen tener un bajo consumo de recursos. Esto es particularmente importante en servidores y sistemas con recursos limitados, donde la eficiencia es crucial.

### 6. Facilidad de Depuración y Mantenimiento

Los scripts Bash son relativamente fáciles de depurar y mantener. La sintaxis sencilla del lenguaje permite identificar y corregir errores rápidamente. Además, al estar escritos en texto plano, los scripts pueden ser versionados y documentados de manera eficiente.

### 7. Control y Personalización del Sistema

El Bash scripting permite a los usuarios un control detallado sobre su sistema. Puedes personalizar tu entorno de trabajo, automatizar configuraciones del sistema y ajustar el comportamiento del sistema operativo según tus necesidades específicas.

## 📝 Creación de un Script

Un script Bash es un archivo de texto que comienza con una línea llamada **"shebang"**, que indica al sistema operativo qué intérprete usar para ejecutar el script.

Para Bash, esta línea es:

```bash
#!/bin/bash
```

Después de esta línea, puedes escribir cualquier comando que normalmente escribirías en la línea de comandos.

### 🔧 Paso 1: Crear el archivo con nano

Vamos a crear un archivo con `nano` llamado `hola.sh` con el siguiente contenido:

```bash
nano hola.sh
```

### ✍️ Paso 2: Escribir el contenido del script

Dentro del editor nano, escribe el siguiente código:

```bash
#!/bin/bash
echo "Hola mundo!!!"
```

### 💾 Paso 3: Guardar y salir

Guarda el archivo presionando `CTRL+O`, luego presiona `Enter` para confirmar el nombre del archivo, y finalmente sal del editor con `CTRL+X`.

### 🔐 Paso 4: Asignar permisos de ejecución

El siguiente paso siempre es asignarle permisos de ejecución:

```bash
chmod +x hola.sh
```

### ▶️ Paso 5: Ejecutar el script

Finalmente ejecutamos el script:

```bash
./hola.sh
```

**Resultado:**

```bash
./hola.sh
Hola mundo!!!
```

### 📌 Modificar el script

Si necesitas editar el script más adelante, simplemente vuelve a abrirlo con nano:

```bash
nano hola.sh
```

Realiza los cambios necesarios, guarda con `CTRL+O` y sal con `CTRL+X`. No es necesario volver a asignar permisos de ejecución si el archivo ya los tiene.

> 💡 Nota:  En este curso ejecutaremos principalmente los comandos directamente desde el terminal, pero es importante conocer cómo crear scripts en archivos para automatizar tareas más complejas y reutilizar código.

## 📦 Variables

Una variable es un **espacio reservado en la memoria** de un programa para almacenar un valor que puede cambiar según las condiciones o la información del programa.

Las variables se utilizan para almacenar datos temporales o permanentes, y se les asigna un nombre único para hacer referencia a ellas desde el código del programa.

Las variables pueden contener diferentes tipos de datos, como números, caracteres, texto, arrays, objetos, etc.

El valor de una variable puede ser asignado, leído o modificado en cualquier momento durante la ejecución del programa, lo que permite que los programas realicen diferentes operaciones.

## 🌍 Variables de Entorno

En Linux, las **variables de entorno** son valores que se almacenan en el sistema operativo y que pueden ser utilizados por los programas y procesos en ejecución. Estas variables pueden ser establecidas por el usuario o por el sistema operativo y pueden ser accedidas y modificadas por los programas en cualquier momento.

### 📋 Variables de Entorno Más Comunes

Algunas de las variables de entorno más comunes en Linux son:

* PATH: Esta variable contiene una lista de directorios separados por `:` donde el sistema busca los programas ejecutables. Cuando se introduce un comando en la terminal, el sistema buscará el programa en los directorios especificados en la variable PATH.

* HOME: Esta variable indica la ubicación del directorio de inicio del usuario actual. Por ejemplo, si la variable HOME está establecida en `/home/usuario`, entonces el sistema buscará el directorio personal del usuario en esa ubicación.

* USER: Esta variable indica el nombre del usuario actual que ha iniciado sesión.

* SHELL: Esta variable indica el tipo de shell que se está utilizando actualmente. Por defecto, la mayoría de las distribuciones de Linux utilizan el shell Bash.

* TERM: Esta variable indica el tipo de terminal que se está utilizando. Esta variable es útil para programas que necesitan saber cómo interactuar con la terminal.

Estas son solo algunas de las variables de entorno más comunes en Linux. Los usuarios pueden establecer y modificar estas variables utilizando la terminal o el archivo de configuración de su shell. Las variables de entorno también pueden ser útiles para personalizar la configuración del sistema o para proporcionar información a los programas y procesos que se ejecutan en el sistema.

### 📝 Listar Variables de Entorno

Para ver las variables de entorno definidas escribimos en el terminal:

```bash
env
printenv
```

O para ver el contenido de una en particular, hacemos referencia a ella con el carácter `$` delante del nombre de la variable:

```bash
echo $PATH
echo $HOME
echo $SHELL
echo $TERM
```

Poniendo el nombre de la variable entre llaves:

```bash
echo ${USER}
echo ${LANG}
echo ${PWD}
echo ${IFS}
```

Este último formato nos permite extraer partes de la variable:

```bash
echo ${PATH:0:1}   # Sintaxis ${variable:inicio:longitud}
```

> 💡 Nota:  En la práctica de parámetros veremos otras opciones.

### ✏️ Definir Variables de Entorno

Podemos definir nuestras propias variables para utilizarlas en el Shell en el que estemos trabajando:

```bash
export rhost=example.org
ping $rhost
nmap -sT -p- $rhost
```

### 🔧 Modificar Variables de Entorno

Igual que a la hora de definir una nueva variable, también las podemos modificar. Una de las modificaciones más útiles es añadir directorios al comienzo o al final del PATH:

```bash
echo $PATH
export PATH=/tmp:$PATH
echo $PATH
```

### ⏱️ Permanencia de Variables de Entorno

Las modificaciones realizadas anteriormente sólo permanecen en el Shell que tenemos abierto, ni abriendo otra pestaña o dividiendo el terminal pasan de unos a otros.

Permanecen si ejecutamos otro Shell en la línea de comandos:

```bash
bash
echo $PATH
```

> ⚠️ Importante:  Si necesitamos mantener una variable o su valor deberemos editar el archivo de configuración del correspondiente Shell que estemos usando.

### 🗑️ Eliminar Variables de Entorno

El comando `unset` permite eliminar una variable:

```bash
unset rhost
```

## 💼 Variables Locales

Son variables que **sólo están disponibles en el shell donde se crean**, aunque se pueden exportar como variables de entorno, pero al definirlas sólo funcionan en el Shell en el que se crean.

### 📝 Declarar Variables Locales

Se pueden declarar definiendo directamente un valor:

```bash
gateway=10.0.0.1
a=3
b=4
```

### 🔍 Ver el Contenido de una Variable

Para ver su contenido se pone el signo `$` delante, igual que las variables de entorno:

```bash
ping $gateway
echo $a
```

### 📄 Tipo de Dato por Defecto: Texto

Por defecto al declararse de esta forma, se declaran de **tipo texto**:

```bash
c=$a+$b
echo $c    # Se concatenan como texto
```

### 🔢 Declaración como Entero

La declaración como entero se debe hacer de la siguiente forma:

```bash
declare -i a
declare -i b
declare -i c
```

Para ver la lista de variables declaradas como enteros:

```bash
declare -i    # Muestra la lista de variables declaradas
```

Ahora sí funcionará la suma correctamente:

```bash
a=3
b=4
c=$a+$b
echo $c    # Resultado: 7
```

### 🌍 Exportar Variables Locales

Una variable local se puede exportar a los siguientes shell que abramos mediante la orden `export`:

```bash
export a
```

> 💡 Diferencia Clave:  Las variables locales solo existen en el shell actual. Si abres una nueva terminal, no estarán disponibles a menos que las exportes con `export`.

## 💾 Variables Permanentes

Si necesitas que una variable esté disponible de forma permanente en todas tus sesiones de terminal, puedes agregarla al archivo de configuración de tu shell.

### 📝 Archivos de Configuración según tu Shell

Dependiendo del shell que utilices, deberás editar uno de estos archivos:

* Bash: `~/.bashrc`

* Zsh: `~/.zshrc`

> 💡 Consejo:  Para saber qué shell estás usando, ejecuta: `echo $SHELL`

### ✏️ Paso 1: Editar el Archivo de Configuración

Abre el archivo correspondiente con tu editor favorito. Por ejemplo, para Bash:

```bash
nano ~/.bashrc
```

O para Zsh:

```bash
nano ~/.zshrc
```

### ➕ Paso 2: Añadir la Variable al Final del Archivo

Ve hasta el final del archivo y añade tus variables. Por ejemplo:

```bash
# Mis variables personalizadas
export rhost=example.org
export SERVIDOR_BACKUP=192.168.1.100
```

Guarda el archivo (`CTRL+O`) y sal del editor (`CTRL+X`).

### 🔄 Paso 3: Cargar los Cambios

Para que los cambios surtan efecto sin necesidad de cerrar la terminal, ejecuta:

Para Bash:

```bash
source ~/.bashrc
```

Para Zsh:

```bash
source ~/.zshrc
```

También puedes usar el comando más corto:

```bash
. ~/.bashrc
. ~/.zshrc
```

### ✅ Verificar que Funciona

Comprueba que la variable se ha cargado correctamente:

```bash
echo $rhost
echo $SERVIDOR_BACKUP
```

> ⚠️ Importante:  A partir de ahora, cada vez que abras una nueva terminal, estas variables estarán disponibles automáticamente.

### 🔍 Diferencia entre .bashrc y .bash_profile

| Archivo | Cuándo se ejecuta | Uso recomendado |
| --- | --- | --- |
| `~/.bashrc` | Cada vez que abres una terminal interactiva | Variables y alias para uso diario |
| `~/.bash_profile` | Solo al iniciar sesión (login shell) | Variables de entorno del sistema |
| `~/.zshrc` | Cada vez que abres una terminal Zsh | Configuración de Zsh |

## 🔄 Sustitución de Comandos

Una de las características más potentes de Bash es la capacidad de **ejecutar un comando y guardar su salida en una variable**. Esto se hace utilizando la sintaxis `$(comando)`.

### 📖 Sintaxis Básica

```bash
variable=$(comando)
```

Todo lo que esté dentro de `$()` se ejecutará como un comando, y el resultado se almacenará en la variable.

### 💡 Ejemplos Prácticos

#### Ejemplo 1: Guardar el contenido de un archivo

```bash
USUARIOS=$(cat /etc/passwd) ; echo "$USUARIOS"
```

Esto guarda todo el contenido del archivo `/etc/passwd` en la variable `USUARIOS`.

#### Ejemplo 2: Guardar la fecha actual

```bash
FECHA=$(date) ; echo "Hoy es: $FECHA"
```

**Resultado:**

```bash
Hoy es: vie 28 mar 2026 12:34:56 CET
```

#### Ejemplo 3: Codificar texto en Base64

```bash
TEXTO="Hola mundo" ; CODIFICADO=$(echo $TEXTO | base64) ; echo "Texto original: $TEXTO" ; echo "Texto codificado: $CODIFICADO"
```

**Resultado:**

```bash
Texto original: Hola mundo
Texto codificado: SG9sYSBtdW5kbwo=
```

#### Ejemplo 4: Decodificar desde Base64

```bash
DECODIFICADO=$(echo SG9sYSBtdW5kbwo= | base64 -d) ; echo "Texto decodificado: $DECODIFICADO"
```

**Resultado:**

```bash
Texto decodificado: Hola mundo
```

#### Ejemplo 5: Contar archivos en un directorio

```bash
NUM_ARCHIVOS=$(ls -lR /home/$USER | wc -l) ; echo "Hay $NUM_ARCHIVOS archivos en este directorio"
```

#### Ejemplo 6: Obtener tu dirección IP

```bash
MI_IP=$(hostname -I | awk '{print $1}') ; echo "Mi IP es: $MI_IP"
```

### 🆚 Sintaxis Alternativa (Backticks)

Existe una sintaxis más antigua usando comillas invertidas `` ``, pero **no se recomienda su uso**:

```bash
FECHA=`date`    # ❌ Sintaxis antigua
$ FECHA=$(date)   # ✅ Sintaxis moderna (recomendada)
```

> 💡 Ventaja de $() : Es más legible y permite anidar comandos fácilmente.

### 🔗 Anidar Sustituciones de Comandos

Puedes anidar múltiples sustituciones:

```bash
DIRECTORIO=$(basename $(pwd)) ; echo "Estás en el directorio: $DIRECTORIO"
```

### ⚠️ Buenas Prácticas

* Usa comillas dobles al hacer echo de variables con espacios: `echo "$VARIABLE"`

* Prefiere `$()` sobre backticks `` ``

* Verifica que el comando funciona antes de guardarlo en una variable

> 🎯 Resumen:  La sustitución de comandos `$()` te permite capturar la salida de cualquier comando y usarla como valor de una variable, lo que es fundamental para crear scripts potentes y automatizados.

## 💬 Comillas Dobles vs Comillas Simples

En Bash, el tipo de comillas que uses afecta cómo se interpretan las variables y los comandos. Es fundamental entender la diferencia para evitar errores.

### 📋 Diferencias Principales

| Tipo | Símbolo | Interpreta Variables | Interpreta Comandos | Uso |
| --- | --- | --- | --- | --- |
| Comillas Dobles | `" "` | ✅ Sí | ✅ Sí | Cuando quieres expandir variables |
| Comillas Simples | `' '` | ❌ No | ❌ No | Cuando quieres texto literal |
| Sin Comillas | - | ✅ Sí | ✅ Sí | ⚠️ Cuidado con espacios |

### 💡 Ejemplo 1: Variables

```bash
NOMBRE="Juan"
echo "Hola $NOMBRE"     # Con comillas dobles
echo 'Hola $NOMBRE'     # Con comillas simples
echo Hola $NOMBRE       # Sin comillas
```

**Resultado:**

```bash
Hola Juan        # ✅ Se expande la variable
Hola $NOMBRE     # ❌ Se muestra literalmente
Hola Juan        # ✅ Se expande la variable
```

### 💡 Ejemplo 2: Comandos con $()

```bash
echo "La fecha es: $(date)"
echo 'La fecha es: $(date)'
echo La fecha es: $(date)
```

**Resultado:**

```bash
La fecha es: sab 28 mar 2026 12:34:56 CET    # ✅ Se ejecuta el comando
La fecha es: $(date)                           # ❌ Se muestra literalmente
La fecha es: sab 28 mar 2026 12:34:56 CET     # ✅ Se ejecuta el comando
```

### 💡 Ejemplo 3: Espacios y Caracteres Especiales

```bash
$ RUTA="/home/usuario/Mis Documentos"
$ echo "$RUTA"        # Con comillas dobles
$ echo '$RUTA'        # Con comillas simples
$ echo $RUTA          # Sin comillas
```

**Resultado:**

```bash
/home/usuario/Mis Documentos       # ✅ Correcto
$RUTA                              # ❌ Literal
/home/usuario/Mis                  # ⚠️ Se corta en el espacio
```

> ⚠️ Importante:  Sin comillas, el espacio rompe el argumento en dos partes. Siempre usa comillas dobles cuando trabajes con rutas o textos que puedan contener espacios.

### 🎯 Cuándo Usar Cada Una

* Comillas Dobles `" "`: Úsalas cuando quieras que las variables y comandos se expandan (caso más común).

* Comillas Simples `' '`: Úsalas cuando necesites texto literal, sin interpretar nada.

* Sin Comillas: Solo si estás seguro de que no hay espacios ni caracteres especiales.

### ✨ Ejemplo Práctico Combinado

```bash
$ USUARIO="Carlos"
$ MENSAJE="Bienvenido"
$ echo "$MENSAJE $USUARIO"           # Ambas se expanden
$ echo '$MENSAJE $USUARIO'           # Nada se expande
$ echo "El usuario \$USUARIO es: $USUARIO"   # Escapar con \
```

**Resultado:**

```bash
Bienvenido Carlos
$MENSAJE $USUARIO
El usuario $USUARIO es: Carlos
```

> 💡 Regla de Oro:  Cuando tengas dudas, usa comillas dobles `" "`. Es la opción más segura y común en scripts.

## ⌨️ Entrada de Datos: Comando read

La instrucción `read` nos permite **guardar en una variable el valor que introduzca el usuario**.

### 📖 Uso Básico

Podemos usar `read` en una sola línea:

```bash
echo "Teclea una palabra"; read -r palabra; echo "Has introducido $palabra"
```

### 🔧 Parámetros de read

Algunos parámetros que se pueden utilizar con la instrucción `read` en `bash` son:

| Parámetro | Descripción | Ejemplo |
| --- | --- | --- |
| `-n1` | Lee tan sólo un carácter, sin tener que pulsar ENTER | `read -n1 letra` |
| `-s` | No muestra por pantalla lo que se teclea (para passwords) | `read -s password` |
| `-p` | Imprime un mensaje (nos ahorra usar echo) | `read -p "Nombre: " nombre` |
| `-t5` | Da 5 segundos de tiempo para introducir el valor | `read -t5 respuesta` |
| `-d` | Usa el delimitador que le indiquemos | `read -d ":" datos` |
| `-a` | Lee variables tipo array | `read -a numeros` |

### 📄 Leer el Contenido de un Archivo

Podemos usar `read` para leer líneas de un archivo sin usar bucles:

#### Ejemplo: Leer la primera línea de un archivo

```bash
read line < /etc/passwd ; echo "Primera línea: $line"
```

```bash
cat /etc/passwd | read line; echo "Primera línea: $line"
```

#### Ejemplo: Leer múltiples campos de una línea

```bash
IFS=':' read -r usuario password uid gid info home shell < /etc/passwd
echo "Usuario: $usuario"
echo "Home: $home"
echo "Shell: $shell"
```

> 💡 IFS (Internal Field Separator):  Es la variable que define qué caracteres se usan para separar campos. Por defecto es espacio, tabulador (\t) y nueva línea (\n). Aquí lo cambiamos a `:` para leer archivos como `/etc/passwd`.

#### Ejemplo: Leer todo el contenido en una variable

```bash
CONTENIDO=$(cat /etc/hosts) ; echo "$CONTENIDO"
```

#### Ejemplo: Leer archivo línea por línea con mapfile (bash) o zmodload (zsh)

```bash
#!/bin/bash

# Leer todo el archivo en un array (sin bucles)
mapfile -t LINEAS < /etc/passwd  (Sólo para entornos bash)

En zsh
LINEAS=("${(@f)$(cat /etc/passwd)}")

# Mostrar la primera línea (En zsh los arrays empiezan en 1
echo "Primera línea: ${LINEAS[1]}"

# Mostrar la segunda línea
echo "Segunda línea: ${LINEAS[2]}"

# Mostrar el número total de líneas
echo "Total de líneas: ${#LINEAS[@]}"
```

### ⚠️ Consideraciones Importantes

* `read` sin parámetros lee hasta encontrar un salto de línea (ENTER)

* Con `-n` lee un número específico de caracteres

* Con `-d` lee hasta encontrar el delimitador especificado

* Usar `-r` previene que la barra invertida `\` sea interpretada como escape

> 🎯 Tip:  Siempre usa `read -r` en scripts para evitar comportamientos inesperados con caracteres especiales.

## 🔁 Bucle for-do-done

Es una sentencia de control de flujo que permite **repetir una sección del programa**, en forma de bucle, durante un número fijo de veces. En cada iteración hay una variable que va cambiando, de modo que cada iteración es ligeramente distinta.

### 📖 Sintaxis Básica

```bash
#!/bin/bash

for var in list
do
    commands
done
```

### 🎛️ Control del Bucle

Existen diferentes formas de controlar la progresión del bucle.

### 💡 Ejemplo 1: Valores Directos

Poniendo los valores directamente en la instrucción FOR:

```bash
for i in 1 2 3 4; do echo "Cuento hasta $i"; done
```

### 💡 Ejemplo 2: Iterar sobre un Rango de Números

```bash
for number in {1..10}; do echo "Number: $number"; done
```

O con `seq`:

```bash
for number in $(seq 1 10); do echo "Number: $number"; done
```

### 💡 Ejemplo 3: Rango con Incremento Específico

```bash
for number in {1..10..2}; do echo "Number: $number"; done
```

O con `seq`:

```bash
for number in $(seq 1 2 10); do echo "Number: $number"; done
```

### 💡 Ejemplo 4: Estilo C (con contador)

Otra forma de controlarlo:

```bash
for ((i = 1; i <= 5; i++)); do echo "Iteration $i"; done
```

### 💡 Ejemplo 5: Recorrer un Array

```bash
fruits=("apple" "banana" "cherry"); for fruit in "${fruits[@]}"; do echo "I like $fruit"; done
```

### 💡 Ejemplo 6: Iterar sobre Archivos de un Directorio

```bash
for file in /etc/*; do echo "File: $file"; done
```

### 💡 Ejemplo 7: Leer un Archivo Línea por Línea

```bash
file="/etc/passwd"; for line in $(cat $file); do echo "Line: $line"; done
```

> ⚠️ Nota:  Este método lee palabra por palabra, no línea por línea completa. Para leer líneas completas con espacios, es mejor usar `while read` que veremos más adelante.

### 📋 Tabla Resumen de Opciones

| Tipo de Iteración | Sintaxis | Ejemplo |
| --- | --- | --- |
| Valores directos | `for i in val1 val2 val3` | `for i in 1 2 3` |
| Rango simple | `for i in {inicio..fin}` | `for i in {1..10}` |
| Rango con incremento | `for i in {inicio..fin..paso}` | `for i in {1..10..2}` |
| Con seq | `for i in $(seq inicio paso fin)` | `for i in $(seq 1 2 10)` |
| Estilo C | `for ((i=0; i 💡 Tip:  Siempre usa comillas dobles alrededor de las variables cuando trabajes con nombres de archivos que puedan contener espacios: `"$file"` en lugar de `$file`

## 🔄 Bucle while

El bucle `while` ejecuta un bloque de comandos **mientras una condición sea verdadera**. A diferencia del bucle `for`, el `while` se usa cuando no sabemos cuántas veces se repetirá el bucle, ya que depende de una condición.

### 📖 Sintaxis Básica

```bash
#!/bin/bash

while [ condición ]
do
    commands
done
```

### 💡 Ejemplo 1: Contador Simple

```bash
contador=1; while [ $contador -le 5 ]; do echo "Contador: $contador"; contador=$((contador + 1)); done
```

**Resultado:**

```bash
Contador: 1
Contador: 2
Contador: 3
Contador: 4
Contador: 5
```

### 💡 Ejemplo 2: Leer Archivo Línea por Línea (MÉTODO MÁS USADO)

Esta es la forma más común y práctica de leer archivos línea por línea:

```bash
cat /etc/passwd | while read line; do echo $line; done
```

O de forma equivalente:

```bash
while read line; do echo "Usuario: $line"; done < /etc/passwd
```

### 💡 Ejemplo 3: Leer Archivo con Múltiples Campos

Separar campos del archivo `/etc/passwd` usando IFS:

```bash
cat /etc/passwd | while IFS=':' read usuario pass uid gid info home shell; do echo "Usuario: $usuario - Shell: $shell"; done
```

### 💡 Ejemplo 4: Leer Solo las Primeras 5 Líneas

```bash
cat /etc/passwd | head -n 5 | while read line; do echo "Línea: $line"; done
```

### 💡 Ejemplo 5: Filtrar y Procesar Líneas

Mostrar solo los usuarios que tienen bash como shell:

```bash
cat /etc/passwd | egrep -v "sync|false|nologin" | while IFS=':' read user x uid x x home shell; do echo "$user usa $shell"; done
```

### 💡 Ejemplo 6: Procesar Archivos de un Directorio

```bash
ls /etc/*.conf | while read archivo; do echo "Procesando: $archivo"; wc -l "$archivo"; done
```

### 💡 Ejemplo 7: Leer Entrada del Usuario

```bash
echo "Escribe 'salir' para terminar"; while read entrada; do if [ "$entrada" = "salir" ]; then break; fi; echo "Escribiste: $entrada"; done
```

### 💡 Ejemplo 8: Monitorizar un Archivo de Log

```bash
tail -f /var/log/syslog | while read line; do echo "[$(date +%T)] $line"; done
```

### 💡 Ejemplo 9: Esperar a que un Servicio Esté Disponible

```bash
echo "Introduce la dirección del servidor"; read -r servidor; while ! ping -c 1 $servidor &> /dev/null; do echo "Esperando conexión..."; sleep 2; done; echo 'Conectado!'
```

### 💡 Ejemplo 10: Esperar a que Aparezca un Archivo

```bash
archivo="mi_archivo.txt"; while [ ! -f "$archivo" ]; do echo "Esperando a que el archivo $archivo aparezca..."; sleep 1; done; echo "El archivo $archivo ha aparecido."
```

### 🎯 Ejemplos Prácticos Combinados

#### Backup de archivos que contengan una palabra:

```bash
grep -l "TODO" *.txt | while read archivo; do cp "$archivo" "backup_$archivo"; done
```

#### Convertir archivos de formato:

```bash
ls *.jpg | while read img; do convert "$img" "${img%.jpg}.png"; done
```

#### Contar líneas de múltiples archivos:

```bash
find . -name "*.sh" | while read script; do echo "$script: $(wc -l < "$script") líneas"; done
```

#### Procesar output de un comando:

```bash
ps aux | grep python | while read user pid resto; do echo "Usuario: $user - PID: $pid"; done
```

#### Leer archivo CSV:

```bash
cat datos.csv | while IFS=',' read nombre edad ciudad; do echo "$nombre tiene $edad años y vive en $ciudad"; done
```

### 📋 Tabla de Operadores de Comparación

| Operador | Descripción | Ejemplo |
| --- | --- | --- |
| `-eq` | Igual a (números) | `[ $a -eq $b ]` |
| `-ne` | No igual a | `[ $a -ne $b ]` |
| `-lt` | Menor que | `[ $a -lt $b ]` |
| `-le` | Menor o igual que | `[ $a -le $b ]` |
| `-gt` | Mayor que | `[ $a -gt $b ]` |
| `-ge` | Mayor o igual que | `[ $a -ge $b ]` |
| `=` o `==` | Igual a (strings) | `[ "$a" = "$b" ]` |
| `!=` | No igual a (strings) | `[ "$a" != "$b" ]` |
| `-z` | String vacío | `[ -z "$a" ]` |
| `-n` | String no vacío | `[ -n "$a" ]` |

### ⚠️ Diferencias: while read vs for

| Aspecto | while read | for line in $(cat) |
| --- | --- | --- |
| Lee líneas completas | ✅ Sí | ❌ No, lee palabra por palabra |
| Respeta espacios | ✅ Sí | ❌ No, los usa como separadores |
| Archivos grandes | ✅ Eficiente | ❌ Carga todo en memoria |
| Uso recomendado | ✅ Leer archivos línea por línea | ⚠️ Solo para listas simples |

> 💡 Recomendación:  Para leer archivos línea por línea,  siempre usa `while read`  en lugar de `for`. Es más seguro y respeta los espacios en las líneas.

### 🛑 Control del Bucle

* `break` - Sale del bucle inmediatamente

* `continue` - Salta a la siguiente iteración

#### Ejemplo con break:

```bash
cat /etc/passwd | while read line; do echo $line; if echo $line | grep -q "nobody"; then echo "Encontrado nobody, saliendo..."; break; fi; done
```

#### Ejemplo con continue:

```bash
cat /etc/passwd | while IFS=':' read user resto; do if [ "$user" = "root" ]; then continue; fi; echo "Usuario: $user"; done
```

> 🎯 Resumen:  El bucle `while` es ideal para procesar archivos línea por línea, esperar condiciones, y cuando no sabemos cuántas iteraciones necesitamos. Combinado con `read` y pipes, se convierte en una herramienta muy potente para scripting.

---

## 🔗 Enlaces y Referencias Cruzadas
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice del Módulo Linux]]
* 🔍 [[02 - MODULOS/Prácticas (PoCs)/Linux/07 - Filtrado de Información en Linux|Filtrado de Información en Linux]]
* 💉 [[02 - MODULOS/Prácticas (PoCs)/Linux/09 - Linux Capabilities y Escalada de Privilegios|Linux Capabilities y Escalada]]

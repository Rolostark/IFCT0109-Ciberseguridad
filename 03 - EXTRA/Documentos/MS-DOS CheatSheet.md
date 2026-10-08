---
title: "MS-DOS — CheatSheet de Comandos de Windows CMD"
aliases:
  - MSDOS CheatSheet
  - Comandos MSDOS
  - Windows CMD CheatSheet
tags:
  - ifct0109
  - msdos
  - cmd
  - cheatsheet
  - comandos
  - windows
---

> [[03 - EXTRA/01 - Enlaces favoritos|← Volver a Enlaces Favoritos]] | [[02 - MODULOS/05 - Prácticas (PoCs)|Ir a Prácticas (PoCs)]]

# 💾 MS-DOS & Windows CMD — CheatSheet de Comandos del Sistema

> [!abstract] Documento Oficial de Referencia
> Esta guía transcribe, estructura y enriquece al 100% el documento oficial del curso:
> 📄 **Archivo PDF Original:** [📥 Descargar msdos.pdf](../../archivos_descargados/documentos/msdos.pdf)
> 
> 🔗 **Laboratorios Prácticos Relacionados:**
> * [[02 - MODULOS/Prácticas (PoCs)/MS-DOS/01 - Comandos de MS-DOS|Práctica 01 — 24 Ejercicios de Consola MS-DOS]]
> * [[02 - MODULOS/Prácticas (PoCs)/MS-DOS/02 - Conexiones Directas e Inversas con Netcat|Práctica 02 — Conexiones Directas e Inversas con Netcat]]
> * [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/Cheatsheet de Comandos de Escalada|Cheatsheet de Comandos para Escalada de Privilegios]]

---

## 📑 Índice Rápido Alfabético

[A](#a) | [B](#b) | [C](#c) | [D](#d) | [E](#e) | [F](#f) | [G](#g) | [H](#h) | [I](#i) | [L](#l) | [M](#m) | [N](#n) | [P](#p) | [R](#r) | [S](#s) | [T](#t) | [V](#v) | [W](#w) | [X](#x)

---

## <a id="a"></a>🔤 Comandos A

| Comando / Sintaxis              | Categoría | Descripción                                                                                          | Ejemplo de Uso             |
| :------------------------------ | :-------: | :--------------------------------------------------------------------------------------------------- | :------------------------- |
| `arp <option> <address>`        |    Red    | Muestra y modifica las entradas de la caché del protocolo de resolución de direcciones (ARP).        | `arp -a`                   |
| `assoc .<extension>=<filetype>` | Archivos  | Muestra o modifica las asociaciones de extensiones de archivos con sus tipos correspondientes.       | `assoc .txt`               |
| `attrib <+/-> <attribute>`      | Archivos  | Muestra o cambia los atributos de archivos (`R` sólo lectura, `A` archivo, `S` sistema, `H` oculto). | `attrib +h +s secreto.txt` |

---

## <a id="b"></a>🔤 Comandos B

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `bcdboot <path>` | Arranque | Inicializa y repara los archivos del almacén de datos de la configuración de arranque (BCD). | `bcdboot C:\Windows /s S:` |

---

## <a id="c"></a>🔤 Comandos C

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `cd <directory>` | Navegación | Cambia el directorio actual o muestra la ruta del directorio activo. | `cd /d C:\tools` |
| `chkdsk <volume> <options>` | Disco | Comprueba el disco y muestra un informe de estado con errores del sistema de archivos. | `chkdsk C: /f /r` |
| `choice <options> <choices>` | Scripting | Solicita al usuario seleccionar una opción entre una lista de teclas en scripts `.bat`. | `choice /c SNC /m "Continuar?"` |
| `cipher <option> <file/directory>` | Seguridad | Muestra o altera el cifrado de archivos y directorios en particiones NTFS (EFS). | `cipher /e /s:C:\confidencial` |
| `<command> \| clip` | Utilidad | Redirige la salida de cualquier comando directamente al portapapeles de Windows. | `dir \| clip` |
| `clip < <filename>` | Utilidad | Copia el contenido de un archivo directamente al portapapeles de Windows. | `clip < clave.txt` |
| `cls` | Consola | Limpia la pantalla de la ventana del símbolo del sistema. | `cls` |
| `cmd <options> <command>` | Intérprete | Inicia una nueva instancia del intérprete de comandos de Windows. | `cmd /k script.bat` |
| `color <background><font>` | Consola | Modifica los colores de fondo y texto de la consola (ej. `0A` para verde matriz). | `color 0A` |
| `comp <file 1> <file 2> <options>` | Archivos | Compara el contenido de dos archivos o conjuntos de archivos byte a byte. | `comp doc1.txt doc2.txt` |
| `compact <options> <file>` | Disco | Muestra y controla la compresión de archivos o directorios en volúmenes NTFS. | `compact /c /s:C:\datos` |
| `copy <options> <src> <dest>` | Archivos | Copia uno o más archivos en otra ubicación del sistema de archivos. | `copy backup.bak D:\backups\` |

---

## <a id="d"></a>🔤 Comandos D

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `date <options>` | Sistema | Muestra o establece la fecha actual del sistema. | `date /t` |
| `defrag <volume> <options>` | Disco | Desfragmenta y optimiza los archivos en volúmenes de disco locales. | `defrag C: /u /v` |
| `del <options> <files>` | Archivos | Elimina uno o más archivos especificados (equivalente a `erase`). | `del /f /q temp.log` |
| `dir <path> <options>` | Navegación | Muestra la lista de subdirectorios y archivos contenidos en un directorio. | `dir /a:h /s C:\` |
| `doskey <options>` | Consola | Recuerda comandos de Windows, edita líneas de comandos y crea macros. | `doskey /history` |
| `driverquery <options>` | Sistema | Muestra el estado y las propiedades de todos los controladores de dispositivo instalados. | `driverquery /v /fo table` |

---

## <a id="e"></a>🔤 Comandos E

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `echo <message>` | Scripting | Muestra mensajes en pantalla o activa/desactiva el eco de comandos (`echo off`). | `echo Hola Mundo > salida.txt` |
| `erase <options> <files>` | Archivos | Elimina uno o varios archivos especificados (sinónimo de `del`). | `erase archivo_viejo.tmp` |
| `exit <options>` | Consola | Cierra la consola de comandos CMD actual o finaliza el script por lotes actual. | `exit /b 0` |

---

## <a id="f"></a>🔤 Comandos F

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `fc <options> <file 1> <file 2>` | Archivos | Compara dos archivos o conjuntos de archivos y muestra las diferencias entre ellos. | `fc /n origen.txt destino.txt` |
| `find <string> <file>` | Búsqueda | Busca una cadena literal de texto específica en un archivo o flujo de entrada. | `find "ERROR" logs.txt` |
| `findstr <string> <file>` | Búsqueda | Busca patrones de cadenas en archivos utilizando expresiones regulares básicas. | `findstr /i /r "pass.*=" config.ini` |
| `ftype <filetype>=<command>` | Sistema | Muestra o modifica los tipos de archivo utilizados en las asociaciones de extensiones. | `ftype txtfile` |

---

## <a id="g"></a>🔤 Comandos G

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `getmac <options>` | Red | Muestra la dirección MAC (Media Access Control) de los adaptadores de red instalados. | `getmac /v /fo list` |

---

## <a id="h"></a>🔤 Comandos H

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `help <command>` | Ayuda | Proporciona información de ayuda completa y parámetros para los comandos de CMD. | `help xcopy` |
| `hostname` | Sistema | Muestra el nombre de host de la máquina en la red local. | `hostname` |

---

## <a id="i"></a>🔤 Comandos I

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `ipconfig <options>` | Red | Muestra todos los valores de configuración de red TCP/IP actuales y refresca DNS/DHCP. | `ipconfig /all` |

---

## <a id="l"></a>🔤 Comandos L

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `label <options> <volume>` | Disco | Crea, cambia o elimina la etiqueta de volumen asignada a un disco. | `label C: Sistema` |

---

## <a id="m"></a>🔤 Comandos M

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `makecab <options> <src> <dest>` | Archivos | Empaqueta archivos individuales en archivos comprimidos de archivador Cabinet (`.cab`). | `makecab datos.txt datos.cab` |
| `md <path>` / `mkdir <path>` | Navegación | Crea un nuevo directorio o subcarpeta en la ruta indicada. | `mkdir C:\laboratorio\pruebas` |
| `mklink <options> <link> <target>` | Archivos | Crea un enlace simbólico, enlace físico o unión de directorios (Junction). | `mklink /D enlace C:\destino` |
| `more <file>` / `<cmd> \| more` | Consola | Muestra la salida pantalla por pantalla (paginación interactiva). | `type log.txt \| more` |
| `mountvol <path> <volume>` | Disco | Crea, elimina o lista puntos de montaje de volúmenes de disco en el sistema. | `mountvol C:\mnt /L` |
| `move <options> <src> <dest>` | Archivos | Mueve archivos y cambia el nombre a archivos y directorios. | `move *.txt C:\archivados\` |
| `msiexec <options> <package>` | Sistema | Ejecuta y gestiona el instalador de paquetes Windows Installer (`.msi`). | `msiexec /i setup.msi /quiet` |
| `msinfo32 <options>` | Sistema | Abre la herramienta de diagnóstico integral de Información del Sistema. | `msinfo32` |
| `mstsc <options> <file>` | Red | Inicia el cliente de Conexión a Escritorio Remoto (RDP). | `mstsc /v:10.0.0.50 /admin` |

---

## <a id="n"></a>🔤 Comandos N

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `net <subcommand> <options>` | Sistema/Red | Suite de administración de red: usuarios (`net user`), grupos (`net localgroup`), recursos compartidos (`net share`). | `net user administrador /domain` |
| `netstat <options>` | Red | Muestra conexiones TCP activas, puertos en escucha, tablas de rutas y estadísticas. | `netstat -ano` |
| `nslookup <host> <dns>` | Red | Realiza consultas directas e inversas contra servidores DNS para resolver dominios e IPs. | `nslookup cursosmfr.es 8.8.8.8` |

---

## <a id="p"></a>🔤 Comandos P

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `path <location>` | Sistema | Muestra o establece la variable de entorno `PATH` con las rutas de búsqueda de ejecutables. | `path %PATH%;C:\tools` |
| `ping <options> <host>` | Red | Comprueba la conectividad a nivel de red con otro equipo enviando mensajes ICMP Echo. | `ping -n 4 10.0.0.1` |
| `powercfg <options> <args>` | Sistema | Controla y optimiza los planes y configuraciones de energía del equipo. | `powercfg /hibernate off` |
| `prompt <string/variables>` | Consola | Personaliza el texto del indicador de línea de comandos de CMD (ej. `$P$G` para ruta y `>`). | `prompt [$T] $P$G` |

---

## <a id="r"></a>🔤 Comandos R

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `rd <path>` / `rmdir <path>` | Navegación | Elimina uno o más directorios (con `/s` elimina recursivamente). | `rmdir /s /q C:\temp` |
| `ren <path><old> <new>` | Archivos | Cambia el nombre de uno o varios archivos o directorios (sinónimo de `rename`). | `ren doc.txt nuevo_doc.txt` |
| `rename <path><old> <new>` | Archivos | Cambia el nombre a archivos o directorios sin moverlos de ruta. | `rename *.bak *.old` |
| `robocopy <src> <dest> <opts>` | Archivos | Herramienta avanzada y robusta de copia y replicación de árboles de directorios. | `robocopy C:\src D:\dst /MIR /Z` |
| `route <options> <cmd> <val>` | Red | Manipula y muestra las tablas de enrutamiento de red local de Windows. | `route print` |

---

## <a id="s"></a>🔤 Comandos S

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `schtasks /<subcommand>` | Sistema | Programa comandos y programas para ejecutarse periódicamente en el Programador de Tareas. | `schtasks /query /fo TABLE` |
| `set <var>=<value>` | Sistema | Muestra, establece o elimina variables de entorno de la sesión actual de CMD. | `set PATH` |
| `sfc <options> <files>` | Sistema | System File Checker: examina la integridad de los archivos de sistema protegidos y los repara. | `sfc /scannow` |
| `shutdown <options>` | Sistema | Permite reiniciar (`/r`), apagar (`/s`) o cerrar sesión en equipos locales o remotos. | `shutdown /r /t 0` |
| `sort <file>` | Utilidad | Lee datos de entrada, los ordena alfabética o numéricamente y muestra los resultados. | `sort usuarios.txt /o ordenados.txt` |
| `start <title> <options>` | Consola | Inicia una ventana de consola independiente o ejecuta un programa o documento específico. | `start cmd.exe` |
| `systeminfo <options>` | Sistema | Muestra información de configuración detallada del hardware, versión de Windows, parches y BIOS. | `systeminfo` |

---

## <a id="t"></a>🔤 Comandos T

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `takeown <options> <file>` | Seguridad | Reasigna la propiedad de un archivo o directorio a la cuenta de usuario o administradores. | `takeown /f C:\bloqueado /r /d s` |
| `taskkill <options> <proc>` | Procesos | Finaliza una o varias tareas o procesos por PID (`/pid`) o nombre de imagen (`/im`). | `taskkill /im notepad.exe /f` |
| `tasklist <options>` | Procesos | Muestra la lista de procesos actualmente en ejecución en el equipo local o remoto. | `tasklist /svc` |
| `telnet <command> <options>` | Red | Cliente Telnet para comunicación por terminal de texto con hosts remotos. | `telnet 10.0.0.1 23` |
| `time <options>` | Sistema | Muestra o ajusta la hora actual del reloj del sistema operativo. | `time /t` |
| `timeout /t <seconds>` | Scripting | Pausa el procesador de comandos durante el número especificado de segundos. | `timeout /t 5 /nobreak` |
| `title <string>` | Consola | Establece el título que se muestra en la barra superior de la ventana del símbolo del sistema. | `title Consola de Auditoria` |
| `tracert <options> <dest>` | Red | Traza y muestra la ruta que siguen los paquetes de red hasta alcanzar un host remoto. | `tracert 8.8.8.8` |
| `tree <options> <path>` | Navegación | Muestra gráficamente la estructura de directorios de una unidad o ruta en forma de árbol. | `tree C:\tools /f /a` |
| `type <file path>` | Archivos | Muestra el contenido de un archivo de texto en la pantalla de la consola. | `type C:\Windows\System32\drivers\etc\hosts` |
| `tzutil <options>` | Sistema | Utilidad para consultar (`/g`) o configurar (`/s`) la zona horaria del sistema. | `tzutil /g` |

---

## <a id="v"></a>🔤 Comandos V

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `ver` | Sistema | Muestra la versión exacta del sistema operativo Windows instalado. | `ver` |
| `vol <drive>` | Disco | Muestra la etiqueta del volumen de disco y el número de serie de la unidad especificada. | `vol C:` |

---

## <a id="w"></a>🔤 Comandos W

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `where <options> <location> <file>` | Búsqueda | Busca y muestra la ubicación de archivos ejecutables que coinciden con el patrón en el PATH. | `where python` |
| `whoami <options>` | Seguridad | Muestra la información del usuario actual, SID, grupos y privilegios asignados (`/priv`). | `whoami /all` |

---

## <a id="x"></a>🔤 Comandos X

| Comando / Sintaxis | Categoría | Descripción | Ejemplo de Uso |
| :--- | :---: | :--- | :--- |
| `xcopy <src> <dest> <options>` | Archivos | Copia archivos y árboles completos de directorios incluyendo atributos y permisos (`/e /i /h`). | `xcopy C:\origen D:\destino /e /h /y` |

---

## 💡 Prácticas Recomendadas para Administración y Pentesting en Windows

> [!tip] Comandos de Enumeración Rápida en una Sesión de Auditoría
> Cuando obtengas una shell en una máquina Windows durante una auditoría o máquina hackeable, ejecuta la siguiente secuencia básica:
> ```cmd
> whoami /all
> systeminfo
> netstat -ano
> tasklist /svc
> net user
> net localgroup administradores
> route print
> ipconfig /all
> ```
> Para una guía completa de elevación de privilegios basada en estos comandos, consulta:
> 👉 [[02 - MODULOS/Prácticas (PoCs)/Escalada de Privilegios en Windows/Cheatsheet de Comandos de Escalada|Cheatsheet de Comandos para Escalada de Privilegios en Windows]].

---

> [[03 - EXTRA/01 - Enlaces favoritos|← Volver a Enlaces Favoritos]] | [[02 - MODULOS/05 - Prácticas (PoCs)|Ir a Prácticas (PoCs)]]

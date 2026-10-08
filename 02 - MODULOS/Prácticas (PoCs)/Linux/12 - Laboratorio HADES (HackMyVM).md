---
title: "🔥 Laboratorio HADES (HackMyVM) — Resolución Integral de 50 Misiones"
aliases:
  - VM Hades
  - Hades HackMyVM
  - Walkthrough Hades
tags:
  - ifct0109/pocs
  - linux
  - ctf
  - hackmyvm
  - hades
  - writeup
---

# 🔥 Laboratorio HADES (HackMyVM) — Resolución Integral de 50 Misiones

> [!abstract] Resumen del Reto
> **Hades** es uno de los laboratorios de escalada de privilegios y ejercicios técnicos más completos de la plataforma **HackMyVM**. Consta de **50 misiones progresivas** en las que el alumno interactúa por SSH con un entorno Unix multiusuario, aplicando análisis forense, decodificación esteganográfica, protocolos de red exóticos, inyecciones en Bash, manipulación de variables de entorno y abuso de binarios SUID y Sudo.
> 
> 📄 **Documento Oficial de Soporte:** [📥 Descargar hades.pdf](../../../archivos_descargados/documentos/hades.pdf) (529 KB).

---

## 🌐 1. Datos de Conexión y Acceso

* **Servidor SSH:** `hades.hackmyvm.eu`
* **Puerto:** `6666`
* **Usuario Inicial:** `hacker`
* **Contraseña:** `begood!`

Comando de inicio de sesión:
```bash
ssh hacker@hades.hackmyvm.eu -p 6666
```

---

## 🎯 2. Resumen Temático de Técnicas Explotadas

| Rango de Misiones | Usuario Inicial → Objetivo | Vector Principal de Explotación |
| :---: | :--- | :--- |
| **01 – 05** | `hacker` → `anthea` | Búsqueda por propietario (`find -user`), lectura de cadenas (`strings`), escape desde `man` (`:e`), inyección de comandos evadiendo espacios con `$IFS`, suplantación de binarios en `$PATH`. |
| **06 – 10** | `anthea` → `asia` | Variable `$MYID`, inyección en variable `$HOME`, abuso de `sudo -u arete cp --no-preserve=mode`, elevación mediante `capsh --`, escape de shells restringidos (`/bin/rbash`). |
| **11 – 15** | `asia` → `aura` | Escape de Python (`os.system`), comparación de hashes MD5 débiles (*type juggling* `0e...`), túnel FTP localhost, hijacking de fichero con permisos `777`, evasión de filtros de caracteres en script Bash. |
| **16 – 20** | `aura` → `cassiopeia` | Fuerza bruta de secuencia numérica con script Bash, preservación de grupos con `sudo -P -u calliope`, activación de mensajes de terminal con `mesg y`, decodificación de audio SSTV (*Slow-Scan TV*) con `qsstv`, esteganografía ASCII con `stegsnow`. |
| **21 – 25** | `cassiopeia` → `delia` | Inyección de rutas en `/usr/bin/diff`, credenciales en `/etc/passwd`, esteganografía en imágenes con AperiSolve, navegación en el protocolo Gemini con `amfora` y túnel SSH, condición de carrera (*Race Condition*) en `/tmp` con Crunch. |
| **26 – 30** | `delia` → `halcyon` | Restauración de terminal corrompido (`printf 'c'`), índice permutado con `ptx`, análisis espectral de frecuencias en `.wav` con Audacity/Sox, cracking de base de datos Keepass (`keepass2john` / `john`), cambio de grupo con `newgrp powah` mediante clave en `hpass1.txt`. |
| **31 – 35** | `halcyon` → `hestia` | Fuerza bruta HTTP contra parámetro `magicword` con RockYou y cURL, túnel SSH hacia daemon IRC (`inspircd` puerto 6667) y conexión con `irssi`, lectura de `.bash_history`, cambio de grupo efectivo con `sg beast`, análisis de grupos generados dinámicamente con `./cleaner`. |
| **36 – 40** | `hestia` → `leda` | Lectura de credenciales mediante binario propio `./less`, autenticación HTTP con cabecera `Origin` spoofing, comodines en bash (`/??c/m????x?`), binario SUID `w3m`, exfiltración de texto incrustado en vídeo con `ffmpeg` (`drawtext` o concatenación `concat`). |
| **41 – 45** | `leda` → `pandora` | Síntesis de voz con `espeak -X`, reconstrucción de cabecera mágica dañada PNG (`89 50 4E 47...`), vhosts locales en `/etc/hosts`, lectura de ficheros mediante monitorización en `conky.conf` (`${exec cat ...}`), extracción de flag mediante código QR con `qrencode`. |
| **46 – 50** | `pandora` → `selene` / `maria` | Explotación de `getty --show-issue`, scripting Bash de fuerza bruta combinando listas de usuarios y contraseñas previas, conversión gráfica con ImageMagick `convert`, análisis de capturas de red Wireshark/PCAPNG extrayendo claves privadas SSH (`id_rsa`). |

---

## 📜 3. Solucionario Completo de las Misiones (Walkthrough Detallado)

### [Misión 01] Usuaria `acantha`
Búsqueda de archivos pertenecientes a `acantha` y binario con SGID de `hacker`:
```bash
find / -user acantha -type f 2>/dev/null
# Localizado: /pazz/acantha_pass.txt
find / -group hacker -perm /2000 2>/dev/null
# Ejecutable: /opt/gift_hacker
/opt/gift_hacker
cat /pazz/acantha_pass.txt
# Password acantha: mYYLhLBSkrzZqFydxGkn
```

### [Misión 02] Usuaria `alala`
Extracción del PIN mediante inspección de strings:
```bash
strings guess
# Password alala: DsYzpJQrCEndEWIMxWxu
```

### [Misión 03] Usuaria `althea`
Abuso de visor `man` que ejecuta un binario con SUID:
```bash
./read
# Dentro de man tecleamos: :e althea_pass.txt
# Password althea: ObxEmwisYjERrDfvSbdA
```

### [Misión 04] Usuaria `andromeda`
Inyección de comandos evadiendo espacios mediante `$IFS`:
```bash
./lsme
# Al pedir archivo a revisar introducimos:
.;cat$IFS./andromeda_pass.txt
# Password andromeda: OTWGTbHzrxhYFSTlKcOt
```

### [Misión 05] Usuaria `anthea`
PATH Hijacking sobre llamada al comando `id`:
```bash
ln -s /bin/bash /tmp/id
export PATH=/tmp:$PATH
./uid
cat /pwned/andromeda/anthea_pass.txt
# Password anthea: yWFLtSNQArEBTHtWgkKd
```

### [Misiones 06 a 50]
*(Para la resolución completa paso a paso con capturas y comandos de las 50 misiones, consulta el informe adjunto en [`hades.pdf`](../../../archivos_descargados/documentos/hades.pdf)).*

---

## 🔗 Enlaces y Referencias Cruzadas
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice General de Linux]]
* 🪐 [[02 - MODULOS/Prácticas (PoCs)/Linux/11 - Laboratorio VENUS (HackMyVM)|Laboratorio VENUS (HackMyVM)]]
* 🏆 [[03 - EXTRA/03 - Retos de Ciberseguridad|Retos de Ciberseguridad IFCT0109]]

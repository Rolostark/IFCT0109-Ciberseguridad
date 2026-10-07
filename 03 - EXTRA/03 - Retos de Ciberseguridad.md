---
title: "Retos de Seguridad Informática (CTFs)"
aliases:
  - Retos CTF
  - Desafios Forenses
  - CTF
tags:
  - ctf
  - retos
  - forense
  - criptografia
  - malware
  - ingenieria-inversa
---

# 🔐 Retos de Seguridad Informática (CTFs & Challenges)

> [!abstract] Aprendizaje Práctico Basado en Retos
> Desafíos técnicos de laboratorio para poner a prueba habilidades en análisis forense digital, extracción de credenciales, análisis de tráfico de red, ingeniería inversa y criptografía.

> [!warning] Contraseña de Descompresión de los Retos
> **Todos los retos están comprimidos en formato `.7z`.**  
> La contraseña para descomprimir los archivos es proporcionada por el docente durante las sesiones de clase.  
> Dentro de cada archivo comprimido encontrarás el enunciado detallado, los ficheros de evidencia (dumps de memoria, capturas pcap, imágenes de disco) y la plantilla para entregar la FLAG.

---

## 🏆 Catálogo de los 12 Retos Técnicos

### 🎯 Reto: Bitlocker
* **Categoría:** `Criptografía` | **Dificultad:** ⭐ Fácil
* **Enunciado y Objetivo:**
> Encuentra la contraseña del disco duro cifrado con Bitlocker. 
                    Monta el disco duro para acceder a la información.
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (Bitlocker.7z)](../archivos_descargados/paquetes_7z/bitlocker.7z)

---

### 🎯 Reto: Office
* **Categoría:** `Criptografía` | **Dificultad:** ⭐ Fácil
* **Enunciado y Objetivo:**
> Tienes un archivo de excel con información confidencial. 
                    Extrae el hash del archivo y averigua la contraseña para abrirlo.
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (Office.7z)](../archivos_descargados/paquetes_7z/office.7z)

---

### 🎯 Reto: Caza Credenciales
* **Categoría:** `Red` | **Dificultad:** ⭐ Fácil
* **Enunciado y Objetivo:**
> Tienes un archivo de una captura de tráfico de red.
		    Tendrás que encontrar la respuesta a diferentes preguntas.
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (Caza Credenciales.7z)](../archivos_descargados/paquetes_7z/creds_hunting.7z)

---

### 🎯 Reto: lsass.exe
* **Categoría:** `Criptografía` | **Dificultad:** ⭐ Fácil
* **Enunciado y Objetivo:**
> Tienes un dump de memoria del proceso lsass.exe.
                    Accede a los hashes de los usuarios y crackea el del usuario Vendor.
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (lsass.exe.7z)](../archivos_descargados/paquetes_7z/lsass.7z)

---

### 🎯 Reto: Unshadow
* **Categoría:** `Criptografía` | **Dificultad:** ⭐ Fácil
* **Enunciado y Objetivo:**
> Hemos capturado los archivos passwd y shadow.
		    Utilizando la herramienta unshadow extrae los hashes y crackealos.
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (Unshadow.7z)](../archivos_descargados/paquetes_7z/unshadow.7z)

---

### 🎯 Reto: Hora de Descompilar
* **Categoría:** `Ingeniería Inversa` | **Dificultad:** ⭐ Fácil
* **Enunciado y Objetivo:**
> Encuentra la FLAG oculta en el código.
		    Utiliza un debugger o un descompilador
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (Hora de Descompilar.7z)](../archivos_descargados/paquetes_7z/reverse.7z)

---

### 🎯 Reto: USB Acquisition
* **Categoría:** `Forense` | **Dificultad:** ⭐ Fácil
* **Enunciado y Objetivo:**
> Hemos hecho una adquisición del USB del agente Smith
                    Encuentra la Bandera
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (USB Acquisition.7z)](../archivos_descargados/paquetes_7z/usbsmith.7z)

---

### 🎯 Reto: Información Confidencial
* **Categoría:** `Red` | **Dificultad:** ⭐ Fácil
* **Enunciado y Objetivo:**
> Se ha detectado una descarga de datos en la red a través del FTP.
                    Localiza el fichero descargado y su contenido.
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (Información Confidencial.7z)](../archivos_descargados/paquetes_7z/ftpzip.7z)

---

### 🎯 Reto: Investiga la Wifi
* **Categoría:** `Forense` | **Dificultad:** ⭐ Fácil
* **Enunciado y Objetivo:**
> Hemos hecho una adquisisción de un teléfono móvil
                    Encuentra la clave de la wifi
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (Investiga la Wifi.7z)](../archivos_descargados/paquetes_7z/wifi.7z)

---

### 🎯 Reto: Analisis de RAM
* **Categoría:** `Forense` | **Dificultad:** ⭐ Fácil
* **Enunciado y Objetivo:**
> Cuando se hizo la adquisición de RAM había un fichero abierto con información Confidencial.
                    Extrae ese archivo de la RAM y lee la bandera.
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (Analisis de RAM.7z)](../archivos_descargados/paquetes_7z/memoria.7z)

---

### 🎯 Reto: Analisis de Pendrive
* **Categoría:** `Forense` | **Dificultad:** ⭐⭐ Medio
* **Enunciado y Objetivo:**
> Analiza un dump de memoria de un pen drive.
                    Extrae archivos borrados y descomprime el zip encriptado.
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (Analisis de Pendrive.7z)](../archivos_descargados/paquetes_7z/evidencias.7z)

---

### 🎯 Reto: Analisis de Tráfico de Malware
* **Categoría:** `Forense` | **Dificultad:** ⭐⭐⭐ Difícil
* **Enunciado y Objetivo:**
> El profesor de historia ha sido engañado y le han robado datos de su carpeta personal.
                    Hemos capturado el tráfico de red durante el ataque.
* **Archivo de Evidencia:** [📥 Descargar Paquete del Reto (Analisis de Tráfico de Malware.7z)](../archivos_descargados/paquetes_7z/infected.7z)


---

> [!tip] Metodología Recomendada de Resolución
> 1. Descarga el paquete `.7z` y calcula su hash SHA-256 antes de abrirlo para comprobar su integridad.
> 2. Descomprime en una máquina virtual aislada (ej. Kali Linux / FlareVM).
> 3. Emplea las herramientas adecuadas para cada caso:
>    * **Volatilidad / Memoria:** `Volatility 3`, `Mimikatz`.
>    * **Tráfico de Red:** `Wireshark`, `tshark`, `NetworkMiner`.
>    * **Ingeniería Inversa:** `Ghidra`, `Cutter`, `x64dbg`.
>    * **Forense de Discos:** `Autopsy`, `FTK Imager`, `sleuthkit`.
>    * **Cracking:** `John the Ripper`, `hashcat`.

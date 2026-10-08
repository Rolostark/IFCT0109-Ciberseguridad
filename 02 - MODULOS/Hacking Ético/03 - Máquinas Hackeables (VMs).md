---
title: "💻 Máquinas Hackeables — Laboratorios y Entornos Vulnerables"
aliases:
  - Máquinas Hackeables
  - VMs Hackeables
  - Laboratorios VMs
  - Dockerlabs
tags:
  - ifct0109
  - hacking-etico
  - vms
  - docker
  - active-directory
  - linux
  - windows
  - ctf
---

# 💻 Máquinas Hackeables — Entornos de Práctica Ofensiva

> [!abstract] Información del Laboratorio
> **Categoría:** Máquinas Virtuales y Contenedores Hackeables  
> **Descripción:** Catálogo de máquinas vulnerables locales y entornos Dockerlabs para entrenamiento de auditorías, explotación web, elevación de privilegios y pivoteo en sistemas Linux, Windows y Active Directory.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/04 - Hacking Ético|⬅️ Hacking Ético]] &emsp;|&emsp; [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|🌐 Web Fuzzing]] &emsp;|&emsp; [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|🔍 Reconocimiento Bug Bounty]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Procedimiento Previo sobre las Máquinas Virtuales (VMs)

Antes de iniciar cualquier ejercicio contra una máquina víctima en VirtualBox / VMware:

1. **Importar la VM Hackeable** en tu hipervisor.
2. **Configurar la tarjeta de red** de la máquina hackeable en modo **Red NAT** (por ejemplo: `RED NAT > Hacking`).
3. **Encender la VM Hackeable y tu máquina atacante (Kali Linux)** en el mismo segmento de Red NAT.
4. **Escanear la red local** desde una terminal de Kali para descubrir la dirección IP asignada a la víctima:
   ```bash
   sudo netdiscover -r 10.0.0.0/24
   ```
5. **Elegir la máquina de entrenamiento** del catálogo inferior.
6. **Definir las variables de entorno:** LHOST (IP de Kali) y RHOST (IP de la víctima) para personalizar tus comandos de ataque.

---

## 🐧 1. Sistema Operativo Linux

### 📂 Conceptos Básicos
Máquinas introductorias diseñadas para asentar enumeración de servicios (FTP anónimo, HTTP, SSH), fuerza bruta controlada con Hydra y escalada local básica con GTFOBins o permisos `sudo`.

| Máquina | Enfoque Principal / Técnicas |
| :--- | :--- |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Thoth\|thoth]]** | FTP anónimo · Diccionario Rockyou con Hydra · `sudo -l` GTFOBins |
| **[[02 - MODULOS/Hacking Ético/Máquinas/mhz_c1f\|mhz_c1f]]** | Enumeración de directorios web · Credenciales por defecto · Sudoers |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Looz\|looz]]** | Fuzzing de endpoints · Explotación de CMS · Path traversal |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Funbox\|funbox]]** | Servicios heredados · Explotación de servicios de red no autenticados |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Friendly\|friendly]]** | Servicios web sencillos · Reverse shell · Permisos débiles |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Funbox7\|funbox7]]** | Criptografía básica · Crackeo de hashes · Tareas cron |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Connection\|connection]]** | Puertos de gestión · Filtrado de credenciales · Grupos locales |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Alzheimer\|alzheimer]]** | Mala configuración de servidor · Elevación vertical |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Hommie\|hommie]]** | Enumeración de usuarios SMB/SSH · Sanitización deficiente |
| **[[02 - MODULOS/Hacking Ético/Máquinas/MoneyBox\|moneybox]]** | Fuerza bruta SSH · Binarios SUID · Captura de flags |
| **[[02 - MODULOS/Hacking Ético/Máquinas/OSBytesec\|osbytesec]]** | Vulnerabilidades en servicios web ligeros · Bypass de autenticación |
| **[[02 - MODULOS/Hacking Ético/Máquinas/dpwwn\|dpwwn-1]]** | MySQL expuesto · Inyección SQL básica · Escalada por servicios |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Hannah\|hannah]]** | Cabeceras HTTP · Parámetros ocultos · Escalada básica |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Pwned\|pwned]]** | Explotación web inicial · Shell interactiva · Permisos de fichero |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Helium\|helium]]** | Servicios de monitorización · Elevación con binarios del sistema |

---

### 📂 Vulnerabilidades Web
Entornos enfocados en la explotación de vectores OWASP Top 10 (LFI/RFI, Subida arbitraria de archivos, Inyección SQL, SSTI y bypass de controles).

| Máquina | Vector Principal de Ataque |
| :--- | :--- |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Vulny\|vulny]]** | Arbitrary File Upload & Web Shell |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Evil box one\|evilbox]]** | LFI (Local File Inclusion) & PHP Wrappers |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Uvalde\|uvalde]]** | Inyecciones en formularios y autenticación rota |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Inferno\|inferno]]** | Fuerza bruta HTTP y paneles administrativos expuestos |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Friendly2\|friendly2]]** | Deserialización / Explotación de endpoints web |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Flower\|flower]]** | SSTI (Server-Side Template Injection) en Python/Node |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Insomnia\|insomnia]]** | JWT manipulation & Insecure Direct Object References (IDOR) |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Cengbox\|cengbox]]** | SQL Injection ciega y volcado de bases de datos |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Faust\|faust]]** | Bypass de filtros WAF y evasión de extensiones |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Liceo\|liceo]]** | Explotación de aplicaciones educativas y CMS obsoletos |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Nebula\|nebula]]** | Command Injection en formularios de utilidades de red |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Medusa\|medusa]]** | Explotación web encadenada y movimientos laterales |

---

### 📂 Avanzadas
Laboratorios que exigen análisis forense de binarios, pivoteo interno, encadenamiento complejo de vulnerabilidades (*exploit chains*) y evasión de restricciones.

| Máquina | Escenario Complejo |
| :--- | :--- |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Superhuman\|superhuman]]** | Reverse engineering ligero y servicios personalizados |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Chromatica\|chromatica]]** | Explotación web a nivel de API y escalada multi-usuario |
| **[[02 - MODULOS/Hacking Ético/Máquinas/w140\|w140]]** | Hardening de servicios y bypass de restricciones AppArmor |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Aragog\|aragog]]** | Análisis de capacidades de Linux (`getcap` / `setcap`) |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Observer\|observer]]** | Fugas de información en logs, depuración y pivoteo local |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Twister\|twisted]]** | Evasión de entornos enjaulados (*Restricted Shells / rbash*) |
| **[[02 - MODULOS/Hacking Ético/Máquinas/dc09\|dc09]]** | Ataques estilo DefCon / OSCP con vectores encadenados |

---

## 🪟 2. Sistema Operativo Windows y Active Directory

### 📂 Windows Server Standalone
Escenarios de sistemas Windows sin controlador de dominio para practicar enumeración de puertos SMB/RPC, búsqueda de credenciales en memoria y escalada con privilegios de Windows (`SeImpersonatePrivilege`, Potato exploits, servicios no cotizados).

* **[[02 - MODULOS/Hacking Ético/Máquinas/ws2008|Windows Server 2008 (ws2008)]]:** Explotación de vulnerabilidades heredadas (MS17-010 EternalBlue, servicios sin parches) con capturas de pantalla integradas.
* **[[02 - MODULOS/Hacking Ético/Máquinas/Liar|liar]]:** Escalada de privilegios local en Windows, abuso de tareas programadas y DLL Hijacking.
* **[[02 - MODULOS/Hacking Ético/Máquinas/Simple|simple]]:** Enumeración SMB, extracción de hashes NTLM y elevación vía permisos débiles en servicios (`sc.exe`).

---

### 📂 Active Directory (AD)
Entornos corporativos de dominio completo para auditar la cadena completa de ataque en directorio activo:

| Laboratorio AD | Objetivos y Técnicas Cubiertas |
| :--- | :--- |
| **[[02 - MODULOS/Hacking Ético/Máquinas/dc01\|dc01]]** | Enumeración de usuarios (`rpcclient`, `cme`), Kerberoasting y AS-REP Roasting |
| **[[02 - MODULOS/Hacking Ético/Máquinas/dc02\|dc02]]** | Abuso de ACLs y permisos delegados, recolección con SharpHound y BloodHound |
| **[[02 - MODULOS/Hacking Ético/Máquinas/dc03\|dc03]]** | Escalada a Domain Admin mediante credenciales en memoria (Mimikatz) y tokens |
| **[[02 - MODULOS/Hacking Ético/Máquinas/Examen ad\|examen]]** | Auditoría integral estilo prueba técnica: compromiso perimetral $\rightarrow$ DA |
| **[[02 - MODULOS/Hacking Ético/Máquinas/BIG\|big]]** | Bosque corporativo con relaciones de confianza entre dominios (*domain trusts*) |

> [!tip] Laboratorio Paso a Paso de Active Directory
> Para el despliegue y auditoría detallada de AD 2019 con BloodHound y herramientas de recolección, consulta:
> * 🗄️ [[02 - MODULOS/Prácticas (PoCs)/Sistemas/02 - Laboratorio Active Directory 2019|Laboratorio Completo Active Directory 2019]]
> * 🩸 [[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/09a - Instalación de BloodHound|Guía de Despliegue y Uso de BloodHound]]

---

## 🐳 3. Dockerlabs (Contenedores Ligeros)

Plataforma comunitaria de laboratorios ofensivos desplegables en segundos sin sobrecargar la RAM del sistema atacante.

🌐 **Portal Oficial:** [https://dockerlabs.es](https://dockerlabs.es)

### 📂 Máquinas Destacadas (Nivel Muy Fácil)

* 📗 **[Trust](https://athem.gitbook.io/athem/ctf/trust-dockerlabs.es)** — Introducción al flujo de intrusión en contenedores.
* 📗 **[Bozaruwarahctf](https://github.com/trr0r/WriteUps/blob/main/DockerLabs/Bozaruwarahctf.md)** — Enumeración web y obtención de consola interactiva.
* 📗 **[Vacaciones](https://athem.gitbook.io/athem/ctf/vacaciones-dokerlabs.es)** — Análisis de código fuente y abuso de comandos en el contenedor.
* 📗 **[Injection](https://noon3.gitbook.io/blog/practica-de-hacking-plataformas-y-recursos/dockerlabs/injection)** — Inyecciones de comandos y sanitización deficiente.

---

### 📦 Guía de Instalación de Docker en Kali Linux

```bash
# 1. Actualizar repositorios e instalar el motor de Docker
sudo apt update && sudo apt install docker.io -y

# 2. Agregar nuestro usuario al grupo docker para evitar usar sudo continuamente
sudo usermod -aG docker $USER

# 3. Cerrar sesión y volver a iniciarla en Kali Linux para aplicar los grupos
```

---

### 🚀 Flujo de Trabajo para Desplegar Máquinas Dockerlabs

1. Descarga el paquete `.zip` de la máquina deseada desde [dockerlabs.es](https://dockerlabs.es).
2. Abre una terminal en Kali y descomprime el archivo:
   ```bash
   cd ~/Descargas
   unzip [nombre_maquina].zip
   chmod +x auto_deploy.sh
   ./auto_deploy.sh [nombre_maquina].tar
   ```
   *(Si no añadiste tu usuario al grupo docker, ejecútalo con `sudo ./auto_deploy.sh [nombre_maquina].tar`)*.

3. Al iniciar, el script notificará la IP asignada al contenedor (por defecto Kali tendrá la `172.17.0.1` en la interfaz `docker0`).
4. Comprueba la visibilidad del laboratorio:
   ```bash
   ping -c 4 [ip_docker]
   ```
5. *(Recomendado)* Asegúrate de tener descomprimido el diccionario oficial `rockyou`:
   ```bash
   sudo gunzip /usr/share/wordlists/rockyou.txt.gz
   ```
6. **Finalización y Limpieza:**
   Una vez terminada la práctica, pulsa `CTRL + C` en la terminal del auto-deploy y elimina los archivos residuales:
   ```bash
   rm [nombre_maquina].* auto_deploy.sh
   ```

---

> [!note] 📝 Conexión con los Retos del Curso
> Para poner a prueba estas técnicas en laboratorios de análisis forense, esteganografía, cracking y malware, continúa con los [[03 - EXTRA/03 - Retos de Ciberseguridad|12 Retos Técnicos de Ciberseguridad (CTFs)]].

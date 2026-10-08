---
title: "🖥️ Máquina: Thoth — Hacking Ético"
aliases:
  - Thoth
  - Máquina Thoth
  - VM Thoth
  - Writeup Thoth
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ftp
  - hydra
  - brute-force
  - privesc
  - gtfobins
---

# 🖥️ Máquina: Thoth

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** FTP Anónimo · Login Brute Force (Hydra) · SSH Access · Privilege Escalation (Sudo & GTFOBins)  
> **Descripción:** Escenario de auditoría donde exploramos distintos servicios en un servidor Linux: análisis de red perimetral, acceso no autenticado a FTP, extracción de información sensible, ataque de diccionario contra credenciales y escalada vertical de privilegios a root.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.7`
* **`RHOST` (Víctima / Thoth):** Ej. `10.0.0.9`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP de la máquina Thoth tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de Servicios y Puertos

El primer paso consiste en descubrir qué puertos TCP se encuentran abiertos y qué servicios están expuestos.

### 1.1 Escaneo rápido de todos los puertos
```bash
nmap -sT -p- $RHOST
```

### 1.2 Escaneo exhaustivo de servicios y versiones
Tras identificar los puertos activos (21 FTP, 22 SSH y 80 HTTP), lanzamos un análisis detallado de versiones y scripts por defecto de Nmap:

```bash
nmap -A -p21,22,80 $RHOST
```

> [!info] 📋 Resultados del Reconocimiento
> * **Puerto 21/tcp (FTP):** Servicio FTP activo que **permite acceso anónimo** (`Anonymous FTP login allowed`).
> * **Puerto 22/tcp (SSH):** Servidor OpenSSH activo.
> * **Puerto 80/tcp (HTTP):** Servidor Apache mostrando la página por defecto de instalación.

---

## 📂 Fase 2: Exploración de FTP y Recolección de Información

Aprovechando que el servicio FTP permite login anónimo, iniciamos sesión para inspeccionar los archivos compartidos.

### 2.1 Conexión anónima al servidor FTP
```bash
ftp anonymous@$RHOST
```
*(Cuando solicite contraseña, pulsa `Enter` o introduce cualquier correo de prueba)*.

### 2.2 Listado y lectura de evidencias en FTP
```text
ftp> ls
ftp> more note.txt
```

> [!example] 📝 Evidencia Encontrada
> El archivo `note.txt` se puede leer directamente desde el servidor FTP y contiene notas internas con una lista o pista clara sobre **usuarios legítimos del sistema**.

---

## 🔐 Fase 3: Ataque de Fuerza Bruta y Acceso Inicial por SSH

Con los nombres de usuario identificados en la nota, procedemos a realizar un ataque de fuerza bruta contra el servicio FTP para recuperar contraseñas débiles.

### 3.1 Preparación del diccionario Rockyou
En Kali Linux, descomprimimos el diccionario estándar si aún no lo está:
```bash
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
```

### 3.2 Fuerza bruta con Hydra sobre FTP
```bash
hydra -l <usuario> -P /usr/share/wordlists/rockyou.txt ftp://$RHOST
```

> [!info] 🔑 Credenciales Obtenidas
> El ataque con Hydra descifra una contraseña válida asociada al usuario descubierto.

### 3.3 Conexión remota por SSH
Dado que el puerto 22 está activo, reutilizamos las credenciales obtenidas para acceder a una sesión de consola interactiva en la máquina:

```bash
ssh <usuario>@$RHOST
```

### 3.4 Captura de la primera bandera (User Flag)
Una vez dentro del sistema, navegamos al directorio del usuario y leemos la bandera:

```bash
cat user.txt
```

---

## 🚀 Fase 4: Escalada de Privilegios a Root

Con acceso de usuario estándar en el sistema operativo, procedemos a auditar vectores de elevación de privilegios locales.

### 4.1 Comprobación de privilegios `sudo`
```bash
sudo -l
```

> [!info] 🔍 Hallazgo de Configuración Sudoers
> La salida de `sudo -l` revela que el usuario actual tiene permisos para ejecutar un **binario específico como root sin necesidad de contraseña** (`NOPASSWD`).

### 4.2 Explotación mediante GTFOBins
Consultamos la referencia comunitaria [GTFOBins](https://int0x33.github.io/) para conocer la sintaxis de escape o bypass del binario permitido:

```bash
sudo <binario> ...
```

> [!tip] Obtención de Shell Root
> Al ejecutar el binario aprovechando sus argumentos interactivos o funciones de escape de shell, obtenemos un prompt con privilegios de superusuario (`#` uid=0).

### 4.3 Captura de la bandera final (Root Flag)
```bash
cat /root/root.txt
```

---

## 📝 Resumen del Flujo de Explotación (Kill Chain)

```text
[1. Nmap Scan] ──► Puerto 21 (FTP Anónimo) descubierto
                      │
[2. FTP Login]  ──► Descarga y lectura de 'note.txt' (Nombres de usuario)
                      │
[3. Hydra + Rockyou] ──► Contraseña recuperada para el usuario
                      │
[4. SSH Login]  ──► Acceso al sistema & captura de 'user.txt'
                      │
[5. sudo -l]    ──► Binario permitido sin contraseña
                      │
[6. GTFOBins]   ──► Shell como Root & captura de '/root/root.txt'
```

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

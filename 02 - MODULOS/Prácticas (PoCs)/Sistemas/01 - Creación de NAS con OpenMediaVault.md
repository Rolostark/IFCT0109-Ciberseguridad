---
title: "🖥️ Práctica: Creación de Servidor NAS con OpenMediaVault"
aliases:
  - Creacion de NAS
  - OpenMediaVault NAS
  - PoC OMV
tags:
  - ifct0109/pocs
  - sistemas
  - nas
  - almacenamiento
  - omv
---

# 🖥️ Práctica: Creación de Servidor NAS con OpenMediaVault (OMV)

> [!abstract] Resumen del Laboratorio
> Implementación de una solución de almacenamiento conectado en red (**NAS**) utilizando la distribución Open Source **OpenMediaVault (OMV)** basada en Debian. Gestión de discos, volúmenes RAID, sistemas de archivos, usuarios, permisos y servicios compartidos (SMB/CIFS, NFS, FTP).

---

# 💾 Práctica Open Media Vault

## Creación de NAS con servicios SMB y FTP

> 📋 ENUNCIADO (DESCRIPCIÓN)

**Objetivo:** Crear una máquina virtual de Open Media Vault y activar los servicios de SMB (compartición de archivos Windows) y FTP (transferencia de archivos).

#### 🎯 ¿Qué es Open Media Vault?

Open Media Vault (OMV) es una solución NAS (Network Attached Storage) de código abierto basada en Debian Linux. Permite convertir cualquier ordenador en un servidor de archivos con una interfaz web intuitiva.

## 🎯 Prueba de Concepto (PoC)

### ⚙️ Paso 1: Crear la Máquina Virtual

Crear una máquina virtual con las siguientes características:

| Componente | Especificación | Notas |
| --- | --- | --- |
| 💻 Procesador | 1 Core | Suficiente para NAS básico |
| 🧠 RAM | 1 GB | Mínimo recomendado |
| 💿 Disco Duro | 20 GB dinámico | Para el sistema operativo |
| 📀 ISO | Open Media Vault | Última versión estable |
| 🌐 Red | NAT / Bridge | Bridge para acceso LAN directo |
| 🖥️ Tipo Sistema | Debian 64 bits | OMV está basado en Debian |
| 📛 Nombre VM | Open Media Vault | Identificador de la VM |

> [!warning] 💡 Consejo:  Si usas VirtualBox o VMware, asegúrate de activar la aceleración de virtualización (VT-x/AMD-V) en la BIOS si no lo has hecho ya.

### 📦 Paso 2: Instalación del Sistema

Iniciar la VM y realizar la instalación. Durante el proceso ten en cuenta:

#### 🔑 Configuración de credenciales

**Paso importante:** Poner la clave de superusuario (root)

```bash
Usuario: root
Contraseña: [la que tú elijas]
```

💡 Anota esta contraseña, la necesitarás para administrar el sistema

#### ⚠️ IMPORTANTE: Gestor de Arranque

En el último paso de la instalación pregunta dónde instalar el gestor de arranque GRUB:

* ✅ Seleccionar: `/dev/sda`

* ❌ NO seleccionar: La opción "Introducir manualmente"

Si lo haces mal, el sistema no arrancará correctamente.

### 🖥️ Paso 3: Primer Acceso al Sistema

Una vez completada la instalación, la VM reiniciará y mostrará un entorno de texto Linux.

#### 🔐 Iniciar sesión como root

```bash
login: root
password: [tu contraseña de instalación]
```

#### 🔍 Obtener la dirección IP de la VM

Necesitamos saber la IP para acceder a la interfaz web. Hay dos opciones:

**Opción 1:** Usar `ip a` (comando moderno)

```bash
ip a
```

**Opción 2:** Instalar net-tools y usar `ifconfig`

```bash
apt-get install net-tools
ifconfig
```

#### 🛠️ Comando de rescate OMV

Comando muy importante para gestionar problemas con el entorno web:

```bash
omv-firstaid
```

**Permite:**

* 🔓 Desbloquear acceso web

* 🌐 Cambiar configuración de IP

* 🔄 Resetear contraseña de admin

* 🔧 Reparar configuración dañada

### 🌐 Paso 4: Acceso a la Interfaz Web

Acceder desde un navegador a la IP obtenida en el paso anterior:

#### 🔑 Credenciales por defecto del entorno web

```bash
URL: https://[IP_DE_TU_VM]
Usuario: admin
Contraseña: openmediavault
```

> [!warning] 🔒 Seguridad:  La primera acción debe ser cambiar esta contraseña por defecto.

## ⚙️ Configuración Inicial del NAS

A continuación, realizamos las siguientes acciones en orden:

### 1️⃣ Cambiar Contraseña del Entorno Web

**Ruta:** Sistema → General Settings → Web Administrator Password

```bash
Nueva contraseña: [elige una contraseña segura]
```

### 2️⃣ Ampliar Tiempo de Sesión

**Ruta:** Sistema → General Settings → Auto Logout

```bash
Tiempo: 30 minutos (o el que prefieras)
```

### 3️⃣ Configurar IP Fija

**Ruta:** Red → Interfaces → Editar ens33

| Parámetro | Valor | Ejemplo |
| --- | --- | --- |
| Método | Static | - |
| Dirección IP | IP fija en tu red | 192.168.1.100 |
| Máscara | Netmask de tu red | 255.255.255.0 (/24) |
| Gateway | IP del router | 192.168.1.1 |
| DNS | Servidor DNS | 8.8.8.8 o 1.1.1.1 |

        **💡 Nota NAT vs Bridge:**

* NAT: Usa el rango de la red virtual (ej: 10.0.2.x)

* Bridge: Usa el rango de tu red local (ej: 192.168.1.x)

### 3️⃣a Sincronizar el Reloj (NTP)

**Ruta:** Sistema → Fecha y Hora

```bash
Habilitar: ✅ Usar servidor NTP
Servidor: pool.ntp.org (viene por defecto)
Zona horaria: Europe/Madrid
```

### 4️⃣ Crear Certificado Digital (SSL)

**Ruta:** Sistema → Certificados → SSL

```bash
Crear certificado autofirmado:
  Nombre común: [nombre de tu NAS]
  Validez: 365 días o más
```

### 5️⃣ Habilitar Conexión Segura (HTTPS)

**Ruta:** Sistema → General Settings

```bash
✅ Habilitar SSL/TLS
Certificado: [el que creaste en paso 4]
Puerto HTTPS: 443
✅ Forzar SSL/TLS (evita acceso HTTP no seguro)
```

> [!warning] ⚠️ Importante:  Tras aplicar, accede con `https://` en vez de `http://`

### 6️⃣ Crear Usuarios

**Ruta:** Usuarios → Usuarios → Añadir

| Usuario | Propósito | Configuración |
| --- | --- | --- |
| `smbuser` | Acceso SMB/CIFS | Nombre: smbuser               Contraseña: [elige una]               Shell: /bin/bash |
| `ftpuser` | Acceso FTP | Nombre: ftpuser               Contraseña: [elige una]               Shell: /bin/bash |

## 💿 Gestión de Discos

### 7️⃣ Añadir Disco de Datos (10 GB)

#### 🔧 Proceso:

* Apagar la VM

* Añadir un disco duro virtual de 10 GB

* Iniciar la VM

* En OMV: Almacenamiento → Discos → Escanear

### 8️⃣ Formatear el Disco

**Ruta:** Almacenamiento → Sistema de Archivos → Crear

```bash
Disco: /dev/sdb (el nuevo disco de 10GB)
Sistema de archivos: EXT4
Etiqueta: datos
```

### 9️⃣ Montar el Disco

**Ruta:** Almacenamiento → Sistema de Archivos → Seleccionar disco → Montar

> [!warning] ✅ Verificación:  El disco debe aparecer con estado "En línea" y porcentaje de uso.

## 📁 Configurar Servicio SMB/CIFS (Compartir Carpetas Windows)

### 1️⃣0️⃣ Habilitar y Configurar SMB/CIFS

#### a) Habilitar el servicio

**Ruta:** Servicios → SMB/CIFS → Configuración

```bash
✅ Habilitar
Grupo de trabajo: WORKGROUP
Descripción: NAS OMV
```

#### b) Crear Carpeta Compartida

**Ruta:** Almacenamiento → Carpetas Compartidas → Crear

```bash
Nombre: compartida
Sistema de archivos: [seleccionar el disco montado]
Ruta relativa: compartida/
Permisos: Administrador: lectura/escritura
         Usuarios: lectura/escritura
         Otros: sin acceso
```

#### c) Crear Recurso Compartido SMB

**Ruta:** Servicios → SMB/CIFS → Recursos Compartidos → Crear

```bash
✅ Habilitar
Carpeta compartida: [seleccionar "compartida"]
Nombre: compartida
Comentario: Carpeta de archivos compartidos
✅ Público: No (requiere autenticación)
✅ Navegable: Sí
```

#### d) Asignar Permisos al Usuario

**Ruta:** Almacenamiento → Carpetas Compartidas → compartida → Permisos → ACL

```bash
Usuario: smbuser
Permisos: Lectura/Escritura ✅
```

#### e) Probar el Acceso SMB

**Desde Windows:**

```bash
Win + R → Ejecutar:
\\[IP_DEL_NAS]\compartida

Usuario: smbuser
Contraseña: [la que creaste]
```

**Desde Linux:**

```bash
smb://[IP_DEL_NAS]/compartida
```

## 📡 Configurar Servicio FTP

### 1️⃣1️⃣ Habilitar y Configurar FTP

#### a) Habilitar el servicio FTP

**Ruta:** Servicios → FTP → Configuración

```bash
✅ Habilitar
Puerto: 21
Conexiones máximas: 10
✅ FTP Pasivo (IMPORTANTE)
   Puerto pasivo mín: 49152
   Puerto pasivo máx: 65534
Mensaje de bienvenida: Bienvenido al FTP de OMV
```

> [!warning] ⚠️ Importante:  El modo pasivo es esencial para que funcione correctamente con firewalls y NAT.

#### b) Crear Carpeta para FTP

**Ruta:** Almacenamiento → Carpetas Compartidas → Crear

```bash
Nombre: myftp
Sistema de archivos: [seleccionar el disco montado]
Ruta relativa: myftp/
Permisos: [similar a SMB]
```

#### c) Asignar Permisos al Usuario FTP

**Ruta:** Almacenamiento → Carpetas Compartidas → myftp → Permisos

```bash
Usuario: ftpuser
Permisos: Lectura/Escritura ✅
```

#### d) Configurar Home del Usuario FTP

**Ruta:** Usuarios → Usuarios → ftpuser → Editar

```bash
Carpeta personal: /srv/dev-disk-by-uuid-[...]/myftp
```

#### e) Probar el Acceso FTP

**Desde cliente FTP (FileZilla, WinSCP, etc.):**

```bash
Servidor: [IP_DEL_NAS]
Puerto: 21
Protocolo: FTP
Usuario: ftpuser
Contraseña: [la que creaste]
```

**Desde línea de comandos:**

```bash
ftp [IP_DEL_NAS]
Usuario: ftpuser
Contraseña: [contraseña]
```

## ✅ Checklist de Verificación Final

| ✓ | Tarea | Verificación |
| --- | --- | --- |
| ☐ | VM creada e instalada | Sistema arranca correctamente |
| ☐ | IP fija configurada | Acceso web estable |
| ☐ | Certificado SSL activo | Acceso HTTPS funcionando |
| ☐ | Usuarios creados | smbuser y ftpuser existen |
| ☐ | Disco de datos montado | Aparece en "Sistema de Archivos" |
| ☐ | SMB/CIFS funcionando | Conexión desde Windows/Linux OK |
| ☐ | FTP funcionando | Conexión con FileZilla OK |
| ☐ | Permisos correctos | Usuarios pueden leer/escribir |

## 🔧 Solución de Problemas Comunes

#### ❌ No puedo acceder a la interfaz web

**Solución:**

```bash
# En la consola de la VM:
omv-firstaid

# Opción 1: Configure network interface
# Opción 2: Change web admin password
# Opción 3: Reconfigure web control panel
```

#### ⚠️ No puedo conectar por SMB desde Windows

**Posibles causas:**

* Firewall de Windows bloqueando el puerto 445

* Usuario/contraseña incorrectos

* Servicio SMB no habilitado en OMV

**Solución:** Verificar credenciales y deshabilitar temporalmente el firewall para probar

#### ⚠️ FTP se conecta pero no lista archivos

**Causa más común:** Modo pasivo no habilitado o puertos bloqueados

**Solución:**

* Verificar que FTP pasivo está habilitado

* En FileZilla: Edición → Configuración → Conexión → FTP → Activo

* Verificar permisos de carpeta para ftpuser

## 📚 Recursos Adicionales

* 🌐 Página oficial de Open Media Vault

* 📖 Documentación oficial

* 💬 Foro de la comunidad

* 🎥 Tutoriales en YouTube sobre OMV

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🏢 [[02 - MODULOS/Prácticas (PoCs)/Sistemas/02 - Active Directory en Windows Server 2019|Active Directory en Windows Server 2019]]

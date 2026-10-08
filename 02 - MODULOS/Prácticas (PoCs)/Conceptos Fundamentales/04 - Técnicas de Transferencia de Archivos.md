---
title: "🔄 PoC - Métodos de Transferencia de Archivos"
aliases:
  - 🔄 PoC - Métodos de Transferencia de Archivos
tags:
  - ifct0109/pocs
  - conceptos
  - transferencia-archivos
---

# 🔄 Transferencia de Archivos entre Sistemas

## 🎯 Objetivos de la PoC

Esta Prueba de Concepto tiene como objetivo que el alumno domine diferentes métodos para transferir archivos entre sistemas Linux y Windows, comprendiendo:

* 🔄 Transferencia mediante servidores HTTP (Python)

* 🔐 Transferencia segura con SCP (SSH)

* 📡 Transferencia con Netcat (raw sockets)

* 🔢 Codificación y transferencia con Base64

* 🪟 Descarga de archivos en Windows con certutil.exe

* ✅ Validación de integridad con hash MD5

## 🖥️ Preparación del Entorno

> [!warning] ⚙️ Requisitos Previos
>
>          ✅ Kali Linux (ya configurado)
>          ✅ Debian VM (importar OVA)
>          ✅ Ambas máquinas en la misma red (red NAT o Bridge)
>          ✅ Conectividad verificada entre ambas

### 📦 Paso 1: Importar y Configurar Debian

**1. Importar la OVA de Debian en VirtualBox/VMware**

**2. Iniciar la máquina Debian**

**3. Instalar el servidor SSH:**

```bash
sudo apt update
sudo apt install openssh-server -y
```

**4. Verificar que SSH está activo:**

```bash
sudo systemctl status ssh
```

### 🔌 Paso 2: Verificar Conectividad

**Desde Kali Linux, conectar por SSH a Debian:**

```bash
ssh tecnico@IP_DEBIAN
```

**📝 Credenciales de Debian:**

* Usuario: `tecnico`

* Contraseña: `SegInf`

**Si la conexión es exitosa, ya estamos listos para comenzar las prácticas.**

## 🌐 Método 1: Transferencia mediante Servidor HTTP

Python incluye un módulo que permite crear un servidor HTTP simple en cualquier directorio. Es útil para transferencias rápidas en redes locales.

### 📤 Desde Kali hacia Debian

#### 🖥️ En Kali Linux (Origen)

**1. Crear directorio de trabajo:**

```cmd
cd ~
mkdir kali-tr
cd kali-tr
```

**2. Crear archivo con el contenido de /etc/passwd:**

```bash
cat /etc/passwd > pass_kali.txt
```

**3. Iniciar servidor HTTP en el directorio:**

```bash
python3 -m http.server 8000
```

**Salida esperada:**

```bash
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

#### 🖥️ En Debian (Destino)

**4. Abrir nueva terminal en Debian y crear directorio:**

```cmd
cd ~
mkdir deb-tr
cd deb-tr
```

**5. Obtener IP de Kali:**

```bash
# Desde Kali ejecutar:
ip a | grep inet
```

**6. Descargar el archivo desde Debian:**

```bash
# Opción 1: wget
wget http://IP_KALI:8000/pass_kali.txt

# Opción 2: curl
curl http://IP_KALI:8000/pass_kali.txt -o pass_kali.txt
```

**7. Verificar que se descargó correctamente:**

```bash
ls -lh pass_kali.txt
head pass_kali.txt
```

### 📥 Desde Debian hacia Kali (Proceso Inverso)

#### 🖥️ En Debian (ahora Origen)

**8. Crear archivo con /etc/passwd de Debian:**

```bash
cd ~/deb-tr
cat /etc/passwd > pass_deb.txt
```

**9. Iniciar servidor HTTP:**

```bash
python3 -m http.server 8000
```

#### 🖥️ En Kali (ahora Destino)

**10. Descargar desde Kali:**

```bash
cd ~/kali-tr
wget http://IP_DEBIAN:8000/pass_deb.txt
```

**11. Verificar descarga:**

```bash
ls -lh pass_deb.txt
head pass_deb.txt
```

### ✅ Validación de Integridad con MD5

Para asegurarnos de que el archivo se transfirió correctamente sin corrupciones, comparamos el hash MD5:

```bash
# En Kali
md5sum pass_kali.txt
# Resultado: a1b2c3d4e5f6... pass_kali.txt

# En Debian
md5sum pass_kali.txt
# Resultado: a1b2c3d4e5f6... pass_kali.txt (debe coincidir)
```

> [!warning] ✅ Si los hashes coinciden:  El archivo se transfirió correctamente
>         ❌ Si los hashes NO coinciden:  Hubo corrupción, repetir transferencia

## 🔐 Método 2: Transferencia con SCP (Secure Copy)

**SCP (Secure Copy Protocol)** utiliza SSH para transferir archivos de forma segura y cifrada entre sistemas.

### 📚 Sintaxis de SCP

```bash
# Enviar archivo local a sistema remoto
scp archivo_local usuario@host_remoto:/ruta/destino

# Descargar archivo desde sistema remoto
scp usuario@host_remoto:/ruta/archivo archivo_local
```

### 📤 Envío desde Kali a Debian

#### 🖥️ En Kali

**1. Crear script de prueba:**

```bash
cd ~/kali-tr
echo "ping -c 4 8.8.8.8" > kali_script
chmod +x kali_script
```

**2. Probar el script localmente:**

```bash
./kali_script
# Debe hacer ping a 8.8.8.8
```

**3. Enviar a Debian con SCP:**

```bash
scp kali_script tecnico@IP_DEBIAN:/home/tecnico/deb-tr/
```

**Se pedirá la contraseña de Debian:** `SegInf`

#### 🖥️ En Debian

**4. Verificar recepción del archivo:**

```bash
cd ~/deb-tr
ls -lh kali_script
```

**5. Dar permisos de ejecución:**

```bash
chmod +x kali_script
```

**6. Ejecutar el script:**

```bash
./kali_script
# Debe hacer ping a 8.8.8.8
```

### 📥 Descarga desde Debian a Kali (Proceso Inverso)

#### 🖥️ En Kali

**7. Eliminar el archivo local:**

```bash
cd ~/kali-tr
rm kali_script
```

**8. Descargar desde Debian usando SCP:**

```bash
scp tecnico@IP_DEBIAN:/home/tecnico/deb-tr/kali_script .
```

**9. Verificar descarga:**

```bash
ls -lh kali_script
./kali_script
```

> [!warning] 💡 Ventajas de SCP
>
>          ✅  Cifrado:  Toda la comunicación está cifrada por SSH
>          ✅  Autenticación:  Requiere credenciales válidas
>          ✅  Integridad:  Garantiza que el archivo no se modifique en tránsito
>          ✅  Simple:  Sintaxis sencilla y directa

## 📡 Método 3: Transferencia con Netcat

**Netcat (nc)** es conocida como la "navaja suiza" de las redes. Permite crear conexiones TCP/UDP directas entre dos sistemas.

### 📚 Funcionamiento de Netcat para Transferencia

| Máquina | Acción | Comando |
| --- | --- | --- |
| Destino | Se pone en escucha | `nc -lvnp PUERTO > archivo_destino` |
| Origen | Envía el archivo | `nc IP_DESTINO PUERTO

#### 🖥️ En Kali (Origen)

**1. Crear archivo codificado en Base64:**

```bash
cd ~/kali-tr
echo -n "ping -c 4 8.8.8.8" | base64 > archivo_nc
```

**Contenido del archivo:**

```bash
cat archivo_nc
# Salida: cGluZyAtYyA0IDguOC44Ljg=
```

#### 🖥️ En Debian (Destino)

**2. Poner Debian en escucha en el puerto 9002:**

```bash
cd ~/deb-tr
nc -lvnp 9002 > archivo_nc
```

**Salida esperada:**

```bash
Listening on 0.0.0.0 9002
```

#### 🖥️ En Kali (Envío)

**3. Enviar el archivo desde Kali:**

```bash
nc IP_DEBIAN 9002 < archivo_nc
```

**4. Esperar unos segundos (la transferencia no muestra confirmación)**

**5. Detener Netcat con `Ctrl+C` en ambas máquinas**

### ✅ Validación de Transferencia

```bash
# En Kali
md5sum archivo_nc
# Resultado: abc123...

# En Debian
md5sum archivo_nc
# Resultado: abc123... (debe coincidir)
```

> [!warning] ⚠️ Limitaciones de Netcat
>
>          ❌ No hay cifrado (transmisión en claro)
>          ❌ No hay confirmación automática de transferencia
>          ❌ Hay que detener manualmente con Ctrl+C
>          ✅ Muy rápido para transferencias en red local
>          ✅ Disponible en casi todos los sistemas

## 🔢 Método 4: Transferencia con Codificación Base64

Base64 es un método de codificación que convierte datos binarios en texto ASCII. Es útil para copiar/pegar contenido entre sistemas sin necesidad de transferencia de archivos.

### 📚 ¿Cómo Funciona?

| Acción | Comando |
| --- | --- |
| Codificar texto | `echo "texto" | base64` |
| Decodificar texto | `echo "Y29kaWZpY2Fkbw==" | base64 -d` |
| Codificar archivo | `base64 archivo > archivo.b64` |
| Decodificar archivo | `base64 -d archivo.b64 > archivo` |

### 🧪 Práctica: Codificar y Ejecutar Comando

#### 🖥️ En Kali

**1. Codificar un comando:**

```bash
echo -n "ping -c 4 8.8.8.8" | base64
```

**Resultado:**

```bash
cGluZyAtYyA0IDguOC44Ljg=
```

**2. Decodificar y ejecutar localmente (prueba):**

```bash
echo cGluZyAtYyA0IDguOC44Ljg= | base64 -d | bash
```

**Salida:** Ping a 8.8.8.8 (4 paquetes)

### 📥 Ejecutar el Archivo de Netcat Decodificado

En la práctica anterior transferimos `archivo_nc` que contiene un comando codificado en Base64.

#### 🖥️ En Debian

**1. Ver contenido del archivo:**

```bash
cd ~/deb-tr
cat archivo_nc
# Salida: cGluZyAtYyA0IDguOC44Ljg=
```

**2. Decodificar y ejecutar:**

```bash
cat archivo_nc | base64 -d | bash
```

**Salida esperada:** Ping a 8.8.8.8 (4 paquetes)

#### 💡 Explicación del Comando

| Parte | Función |
| --- | --- |
| `cat archivo_nc` | Lee el contenido del archivo |
| `| base64 -d` | Decodifica de Base64 a texto |
| `| bash` | Ejecuta el comando decodificado en bash |

### 🎯 Casos de Uso de Base64

* ✅ Transferir comandos sin crear archivos

* ✅ Copiar/pegar entre sistemas sin conectividad directa

* ✅ Ofuscar comandos (no es cifrado, solo codificación)

* ✅ Transferir archivos pequeños a través de terminales

* ⚠️ NO es un método de cifrado seguro

## 🪟 Método 5: Descarga en Windows con Certutil

**Certutil.exe** es una utilidad nativa de Windows para gestionar certificados, pero también puede descargar archivos desde URLs.

### 📚 Sintaxis de Certutil

```bash
certutil.exe -urlcache -split -f URL_ARCHIVO
```

#### 🔧 Explicación de Parámetros

| Parámetro | Función |
| --- | --- |
| `-urlcache` | Utiliza la caché de URLs |
| `-split` | Descarga en partes (útil para archivos grandes) |
| `-f` | Fuerza la descarga (sobrescribe) |

### 💻 Ejemplo Práctico

#### 🖥️ En Kali (preparar servidor)

**1. Crear directorio y colocar archivo nc.exe:**

```bash
cd ~/kali-tr
# Copiar o descargar nc.exe a este directorio
python3 -m http.server 8000
```

#### 🪟 En Windows (CMD como Administrador)

**2. Descargar nc.exe usando certutil:**

```bash
certutil.exe -urlcache -split -f http://IP_KALI:8000/nc.exe nc.exe
```

**Salida esperada:**

```bash
****  Online  ****
000000  ...
01e800
CertUtil: -URLCache command completed successfully.
```

**3. Verificar descarga:**

```cmd
dir nc.exe
nc.exe -h
```

### 🎯 Ventajas de Certutil

* ✅ Nativo de Windows (no requiere instalación)

* ✅ Disponible en todas las versiones modernas de Windows

* ✅ Funciona incluso con restricciones de PowerShell

* ⚠️ Puede ser bloqueado por antivirus/EDR

* ⚠️ Deja rastros en los logs del sistema

> [!warning] 🔐 Consideraciones de Seguridad
>        Certutil es frecuentemente utilizado en ataques reales para descargar herramientas maliciosas. Por esta razón:
>
>          ⚠️ Muchos antivirus monitorean su uso
>          ⚠️ Puede generar alertas de seguridad
>          ⚠️ Los SIEM suelen registrar su ejecución
>          ✅ Útil conocerlo para detección y respuesta a incidentes

## 📊 Comparativa de Métodos

| Método | Velocidad | Seguridad | Complejidad | Mejor Para |
| --- | --- | --- | --- | --- |
| HTTP (Python) | ⚡⚡⚡ | ❌ Sin cifrado | ⭐ Muy fácil | Red local, pruebas rápidas |
| SCP (SSH) | ⚡⚡ | ✅ Cifrado | ⭐⭐ Fácil | Transferencias seguras |
| Netcat | ⚡⚡⚡ | ❌ Sin cifrado | ⭐⭐ Medio | Raw sockets, velocidad |
| Base64 | ⚡ | ❌ Solo codificación | ⭐ Muy fácil | Comandos pequeños, copiar/pegar |
| Certutil | ⚡⚡ | ❌ Sin cifrado | ⭐ Muy fácil | Windows, sin herramientas adicionales |

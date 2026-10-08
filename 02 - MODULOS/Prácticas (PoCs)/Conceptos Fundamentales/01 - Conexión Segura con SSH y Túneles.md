---
title: "🔐 Configuración y Uso de SSH"
aliases:
  - 🔐 Configuración y Uso de SSH
tags:
  - ifct0109/pocs
  - conceptos
  - ssh
  - tuneles
---

# 🔐 Configuración y Uso de SSH

## 📖 ENUNCIADO (DESCRIPCIÓN)

SSH (Secure Shell) es un protocolo de red que permite acceder de forma segura a sistemas remotos a través de una conexión cifrada. En esta práctica aprenderemos a configurar un servidor SSH en Debian, conectarnos desde Kali Linux, gestionar la autenticación mediante claves públicas/privadas y crear túneles SSH.

## 🚀 Prueba de Concepto (PoC)

> [!warning] 🖥️ Requisitos previos
>
>          ✅  Debian VM  - Actuará como servidor SSH
>          ✅  Kali Linux  - Actuará como cliente SSH
>          ✅ Ambas máquinas en la misma red (o configuradas para comunicarse)

**Importar la OVA de Debian e iniciarla.**

## 📦 Instalación de OpenSSH en Debian

### Instalar OpenSSH Server

```bash
sudo apt update
sudo apt install openssh-server
```

### 📝 Editar la configuración del servidor SSH

Para editar la configuración del servidor SSH:

```bash
sudo nano /etc/ssh/sshd_config
```

### 📁 Directorio de configuración SSH

El directorio donde se encuentran los hosts conocidos y las claves públicas es el siguiente:

```bash
/home/usuario/.ssh/
```

### ⚙️ Gestión del servicio SSH

Para iniciar / parar o reiniciar el servidor SSH:

```bash
sudo /etc/init.d/ssh start
sudo /etc/init.d/ssh stop
sudo /etc/init.d/ssh restart
```

**O usando systemctl:**

```bash
sudo systemctl start ssh
sudo systemctl stop ssh
sudo systemctl restart ssh
```

### 🔌 Primera conexión SSH desde Kali a Debian

En este momento ya podríamos acceder por SSH a la VM de Debian desde Kali:

```bash
ssh tecnico@10.0.0.16
```

**Nota:** Cambia `10.0.0.16` por la IP real de tu Debian.

## ⚙️ Configuración de sshd_config en Debian

### 🔢 Cambiar el puerto por defecto del servidor SSH

```bash
Port 22  # Para la práctica vamos a dejar el puerto por defecto
```

> [!warning] 📌 Nota:  En entornos de producción se recomienda cambiar el puerto por defecto (22) a otro personalizado para reducir intentos de conexión automatizados.

### 🔐 Configuraciones de seguridad

Edita el archivo `/etc/ssh/sshd_config` y cambia/añade las siguientes configuraciones:

```bash
PermitRootLogin no        # Evita que se pueda acceder como root
MaxAuthTries 3            # Número máximo de intentos antes de desconectarnos
MaxStartups 3             # Número de Login simultáneos desde una misma IP
DenyUsers tecnico         # Lista negra de usuarios
```

### ✅ Aplicar cambios y probar

**1. En Debian - Reinicia el servicio SSH:**

```bash
sudo /etc/init.d/ssh restart
```

**2. En Kali - Intenta conectarte:**

```bash
ssh tecnico@10.0.0.16
```

Deberías recibir un mensaje de "Permission denied" si el usuario está en `DenyUsers`.

### 📋 Listas blancas vs listas negras

Cambia ahora `DenyUsers` por `AllowUsers`:

```bash
AllowUsers tecnico  # Lista blanca de usuarios
```

| Directiva | Función | Ejemplo |
| --- | --- | --- |
| `AllowUsers` | Lista blanca - Solo estos usuarios pueden conectar | `AllowUsers tecnico admin` |
| `DenyUsers` | Lista negra - Estos usuarios NO pueden conectar | `DenyUsers root guest` |

### ✅ Reiniciar servicio y probar nuevamente

**1. En Debian:**

```bash
sudo /etc/init.d/ssh restart
```

**2. En Kali - Trata nuevamente de conectarte:**

```bash
ssh tecnico@10.0.0.16
```

Ahora SÍ deberías poder conectar si el usuario está en `AllowUsers`.

### 🔑 Configuración de claves de host

Descomenta las siguientes tres líneas en el archivo de configuración `/etc/ssh/sshd_config`:

```bash
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
```

## 🔐 Autenticación en SSH

### 🔑 Habilitar autenticación con contraseña

En `/etc/ssh/sshd_config`:

```bash
PasswordAuthentication yes
```

### 🔐 Habilitar autenticación con clave pública

En `/etc/ssh/sshd_config`:

```bash
PubkeyAuthentication yes
```

| Método | Seguridad | Ventajas | Desventajas |
| --- | --- | --- | --- |
| Contraseña | ⚠️ Media | Fácil de usar, no requiere configuración adicional | Vulnerable a fuerza bruta, phishing |
| Clave pública/privada | ✅ Alta | Muy seguro, no vulnerable a fuerza bruta | Requiere configuración inicial |

## 🔑 Autenticación mediante clave pública/privada

Este método es mucho más seguro que usar contraseñas. Vamos a generar el par de claves en Kali Linux y copiar la clave pública al servidor Debian.

### 🔨 Paso 1: Generar el par de claves en Kali

```bash
ssh-keygen -t rsa -b 4096
```

#### 📋 Explicación de parámetros:

* `-t rsa` → Tipo de clave (RSA)

* `-b 4096` → Longitud de la clave en bits (mayor = más seguro)

**Preguntas que aparecerán:**

* Dónde guardar la clave: Presiona Enter para aceptar la ubicación por defecto (`/home/tecnico/.ssh/id_rsa`)

* Passphrase: Se recomienda añadir una para mayor seguridad

* Confirmar passphrase: Repite la misma

> [!warning] También nos pregunta por clave secreta (passphrase), que para dar más seguridad sería conveniente definirla.
>          Esta passphrase añade una capa adicional de seguridad: incluso si alguien roba tu clave privada, no podrá usarla sin conocer la passphrase.

**Se generan DOS archivos:**

* `~/.ssh/id_rsa` → Clave PRIVADA (NUNCA compartir, se queda en Kali)

* `~/.ssh/id_rsa.pub` → Clave PÚBLICA (se enviará a Debian)

### 📤 Paso 2: Copiar la clave pública a Debian

Desde Kali, enviamos la clave pública al servidor Debian:

```bash
ssh-copy-id tecnico@10.0.0.16
```

**¿Qué hace este comando?**

* Lee tu clave pública (`~/.ssh/id_rsa.pub`) en Kali

* Se conecta por SSH a Debian (pedirá la contraseña del usuario `tecnico`)

* Añade la clave pública al archivo `~/.ssh/authorized_keys` en Debian

En el proceso nos pedirá la **contraseña del usuario tecnico en Debian**. Es la ÚLTIMA vez que necesitarás usar contraseña para conectarte a ese servidor.

### 🔌 Paso 3: Conectar con autenticación RSA

Ahora desde Kali, conectamos a Debian:

```bash
ssh tecnico@10.0.0.16
```

**Salida esperada:**

```bash
Enter passphrase for key '/home/tecnico/.ssh/id_rsa':
```

Nos pedirá la **passphrase** que creamos al generar el par de claves (NO la contraseña del usuario).

Una vez introducida correctamente, entraremos en Debian sin necesidad de contraseña del usuario.

### ✅ Verificar la instalación de la clave (dentro de Debian)

Una vez conectado a Debian, comprueba cómo nuestra clave pública aparece en el archivo `authorized_keys`:

```bash
cat ~/.ssh/authorized_keys
```

Deberías ver una línea que empieza con `ssh-rsa AAAAB3...` (tu clave pública de Kali)

### 🔒 Paso 4: (Opcional) Deshabilitar autenticación por contraseña

Una vez verificado que la autenticación con clave funciona, podemos deshabilitar completamente la autenticación por contraseña para mayor seguridad.

Edita `/etc/ssh/sshd_config`:

```bash
sudo nano /etc/ssh/sshd_config
```

Cambia:

```bash
PasswordAuthentication no
```

> [!warning] ⚠️ IMPORTANTE:  Asegúrate de que la autenticación con clave pública funciona ANTES de hacer este cambio, o podrías quedarte sin acceso al servidor.

Reinicia el servicio SSH:

```bash
sudo /etc/init.d/ssh restart
```

## 🔐 Flujo de Autenticación con Clave Pública/Privada

### 📚 ¿Cómo funciona?

        **1️⃣ Generación de claves (en KALI)**

```bash
ssh-keygen -t rsa -b 4096
```

Genera dos archivos en Kali:

* `~/.ssh/id_rsa` → Clave PRIVADA (se queda en Kali, NUNCA compartir)

* `~/.ssh/id_rsa.pub` → Clave PÚBLICA (se enviará a Debian)

        **2️⃣ Copia de clave pública a Debian (desde KALI)**

```bash
ssh-copy-id tecnico@10.0.0.16
```

La clave pública se almacena en Debian en:

```bash
/home/tecnico/.ssh/authorized_keys
```

        **3️⃣ Conexión SSH (desde KALI)**

```bash
ssh tecnico@10.0.0.16
```

El servidor Debian envía un desafío cifrado con la clave pública

        **4️⃣ Respuesta del cliente (KALI)**

Kali descifra el desafío con su clave privada y responde

        **5️⃣ Autenticación exitosa**

Si la respuesta es correcta → Acceso concedido a Debian ✅

## 🌐 Túnel SSH (Port Forwarding)

Los túneles SSH permiten redirigir tráfico de red de forma segura a través de una conexión SSH cifrada.

### 📦 Paso 1: Instalar Apache2 en Debian

```bash
sudo apt install apache2
```

### ⚙️ Paso 2: Cambiar puerto de Apache en Debian

Edita el archivo de configuración de puertos:

```bash
sudo nano /etc/apache2/ports.conf
```

Cambia:

```bash
Listen 80
```

Por:

```bash
Listen 8080
```

Reinicia Apache:

```bash
sudo systemctl restart apache2
```

**Verificar que Apache está corriendo en el puerto 8080:**

```cmd
sudo netstat -tlnp | grep apache2
```

O desde el navegador en Debian accede a:

```bash
http://localhost:8080
```

### 🔒 Paso 3: Crear túnel SSH desde Kali

```bash
ssh -L 8888:localhost:8080 tecnico@10.0.0.16
```

#### 📋 Explicación de parámetros:

* `-L` → Local port forwarding (reenvío de puerto local)

* `8888` → Puerto LOCAL en tu máquina Kali

* `localhost:8080` → Destino en el servidor Debian (Apache escuchando en 8080)

* `tecnico@10.0.0.16` → Usuario y servidor SSH (Debian)

**¿Qué hace este comando?**

* Establece una conexión SSH a Debian

* Crea un túnel que redirige el puerto 8888 de Kali al puerto 8080 de Debian

* Todo el tráfico viaja cifrado por SSH

### 🌐 Paso 4: Acceder al servicio desde Kali

Una vez establecido el túnel, desde el navegador en Kali accede a:

```bash
http://localhost:8888
```

**Resultado:** Verás la página de Apache que está corriendo en Debian en el puerto 8080, pero accediendo desde Kali en el puerto 8888 mediante un túnel SSH cifrado.

> [!warning] 📌 Importante:  Mientras el túnel esté activo (la sesión SSH no se cierre), podrás acceder a `http://localhost:8888` en Kali para ver el Apache de Debian.
>          Si cierras la conexión SSH, el túnel se cierra y ya no podrás acceder.

### 🔄 Resumen del flujo completo

* Debian: Apache corriendo en puerto 8080

* Kali: Crea túnel SSH → `ssh -L 8888:localhost:8080 tecnico@10.0.0.16`

* Kali: Abre navegador → `http://localhost:8888`

* Resultado: Navegador en Kali muestra Apache de Debian (todo cifrado por SSH)

### 🎯 Casos de uso de túneles SSH

| Escenario | Ventaja |
| --- | --- |
| Acceder a servicios internos desde fuera | No necesitas exponer puertos en firewall |
| Cifrar tráfico de aplicaciones inseguras | HTTP, FTP, etc. viajan cifrados por SSH |
| Bypass de restricciones de red | Acceder a servicios bloqueados localmente |
| Administración remota segura | Acceso a paneles web internos sin VPN |

## 📚 Resumen de Comandos SSH

| Máquina | Comando | Descripción |
| --- | --- | --- |
| Kali | `ssh usuario@host` | Conexión SSH básica |
| Kali | `ssh-keygen -t rsa -b 4096` | Generar par de claves RSA de 4096 bits |
| Kali | `ssh-copy-id usuario@host` | Copiar clave pública al servidor Debian |
| Kali | `ssh -L local:destino:puerto usuario@host` | Túnel SSH (port forwarding local) |
| Kali | `ssh -p PUERTO usuario@host` | Conectar a puerto SSH no estándar |
| Kali | `ssh -i /ruta/clave usuario@host` | Usar una clave privada específica |
| Debian | `sudo nano /etc/ssh/sshd_config` | Editar configuración del servidor SSH |
| Debian | `sudo /etc/init.d/ssh restart` | Reiniciar servicio SSH |
| Debian | `cat ~/.ssh/authorized_keys` | Ver claves públicas autorizadas |

## 🔒 Buenas Prácticas de Seguridad SSH

* ✅ Deshabilitar login como root (`PermitRootLogin no`) en Debian

* ✅ Usar autenticación con claves públicas/privadas (generar en Kali, copiar a Debian)

* ✅ Deshabilitar autenticación por contraseña una vez configuradas las claves en Debian

* ✅ Cambiar el puerto por defecto (22) a uno personalizado en Debian

* ✅ Limitar intentos de autenticación (`MaxAuthTries`) en Debian

* ✅ Usar listas blancas de usuarios (`AllowUsers`) en Debian

* ✅ Proteger la clave privada con passphrase en Kali

* ✅ NUNCA compartir la clave privada (se queda en Kali)

* ✅ Mantener SSH actualizado regularmente en Debian

* ✅ Usar fail2ban para bloquear IPs con intentos fallidos en Debian

* ✅ Revisar logs periódicamente en Debian (`/var/log/auth.log`)

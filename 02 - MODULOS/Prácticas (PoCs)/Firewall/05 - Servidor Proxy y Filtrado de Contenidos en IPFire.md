---
title: "🛡️ Práctica: Configuración de Proxy Squid y Filtrado Web en IPFire"
aliases:
  - Configuración de Proxy Squid y Filtrado Web en IPFire
  - PoC ipfire
tags:
  - ifct0109/pocs
  - firewall
  - ipfire
  - proxy
  - squid
  - filtrado-web
---

# 🛡️ Práctica: Configuración de Proxy Squid y Filtrado Web en IPFire

> [!abstract] Resumen del Laboratorio
> Módulo de Seguridad Perimetral y Cortafuegos del Certificado IFCT0109: topologías por zonas (ROJA, VERDE, AZUL, NARANJA), segmentación perimetral, políticas de filtrado, túneles cifrados VPN, sistemas IDS/IPS y filtrado a nivel de host.

---

# 🌐 Proxy Web con IPFire

## 📖 ENUNCIADO (DESCRIPCIÓN)

Vamos a activar el **Web Proxy** en nuestro firewall y aplicar diferentes opciones de filtrado y control de navegación.

#### 🎯 Objetivos de la Práctica:

* ✅ Activar el proxy web Squid en IPFire

* ✅ Configurar el proxy transparente en la zona GREEN

* ✅ Activar el antivirus Squid ClamAV

* ✅ Bloquear dominios con listas negras personalizadas

* ✅ Configurar listas negras automáticas por categorías

* ✅ Bloquear por expresiones regulares y extensiones de archivo

#### 💡 ¿Qué es un Proxy Transparente?

Un **proxy transparente** intercepta el tráfico web sin que el usuario tenga que configurar nada en su navegador. Todo el tráfico HTTP/HTTPS pasa automáticamente por el proxy al estar integrado en el firewall.

| Tipo | Configuración cliente | Ventaja |
| --- | --- | --- |
| Proxy Normal | Manual en cada equipo | Control explícito |
| Proxy Transparente | Ninguna (automático) | Fácil despliegue masivo |

## 🚀 Prueba de Concepto (PoC)

### 📦 Paso 1: Instalar SquidClamav

Añadir al sistema **SquidClamav** desde el menú **IPFire > Pakfire**.

#### 📦 SquidClamav:

Es la integración entre **Squid** (proxy) y **ClamAV** (antivirus). Permite analizar en tiempo real todo el contenido web descargado a través del proxy, bloqueando archivos maliciosos antes de que lleguen al usuario.

### 🌐 Paso 2: Acceder al Menú Web Proxy

Vamos al menú **Red > Web Proxy**.

### ⚙️ Paso 3: Activar el Proxy Transparente en GREEN

Activamos en **GREEN** el proxy y marcamos la opción **Transparente en GREEN**.

#### ⚙️ Configuración del Proxy:

| Opción | Valor |
| --- | --- |
| Proxy en GREEN | ✅ Activado |
| Transparente en GREEN | ✅ Activado |
| Puerto | 8080 (por defecto) |

### 🔌 Paso 4: Activar Módulos Adicionales

Activamos los siguientes módulos:

#### 🔌 Módulos a Activar:

* 🦠 SquidClamav → Antivirus integrado en el proxy

* 🔗 URL Filter → Filtrado de URLs y dominios

* ⚡ Acelerador de Actualizaciones → Caché de actualizaciones de Windows/software

## 🚫 Filtrado de Contenidos

### 📋 Paso 6: Bloquear Dominios con Lista Negra Personalizada

Vamos a intentar bloquear algún dominio. Entramos en **Network > URL Filter**.

Debemos escribir la URL en **Custom Blacklist** bloqueando tanto por dominio como por URL. Y activamos **Enable custom blacklist**.

#### 🚫 Ejemplos de Bloqueo en Custom Blacklist:

| Tipo | Ejemplo | Efecto |
| --- | --- | --- |
| Dominio exacto | `facebook.com` | Bloquea solo ese dominio |
| URL concreta | `facebook.com/login` | Bloquea esa URL específica |
| Comodín | `*facebook*` | Bloquea cualquier URL que contenga "facebook" |
| Comodín subdominio | `*.youtube.com` | Bloquea todos los subdominios |

> [!warning] 💡 Tip:  Se pueden utilizar asteriscos como comodines. Ejemplo: `*Facebook*` bloqueará cualquier URL que contenga la palabra Facebook.

Salvamos y probamos a navegar a ese dominio para comprobar que está bloqueado.

### 📋 Paso 7: Listas Negras Automáticas por Categorías

En el mismo menú **Network > URL Filter** también podemos definir **listas negras automáticas** para el bloqueo de navegación.

#### ⚙️ Pasos para Activar Listas Negras Automáticas:

* Bajar hasta la opción Actualización automática de las listas negras

* Habilitarla y configurarla en Diaria

* Seleccionar un source (fuente de listas negras)

* Pulsar en Actualizar ahora

Ahora en la **zona superior del menú URL Filter** aparecen muchas categorías que podemos marcar y que el proxy bloqueará automáticamente.

#### 📂 Categorías Disponibles (ejemplos):

* 🔞 adult → Contenido adulto

* 🎰 gambling → Juegos de azar

* 💀 malware → Sitios maliciosos

* 🎣 phishing → Sitios de phishing

* 📱 social networks → Redes sociales

* 🎮 games → Videojuegos online

* 📺 streaming → Plataformas de streaming

* 🛒 shopping → Compras online

### 🔍 Paso 8: Bloqueo por Expresiones y Extensiones de Archivo

Haz pruebas utilizando **Lista de expresiones** y bloqueando también por **extensiones de archivos**.

#### 📝 Ejemplos de Bloqueo Avanzado:

**Lista de Expresiones (palabras clave en la URL):**

* `casino` → Bloquea URLs que contengan "casino"

* `crack` → Bloquea URLs que contengan "crack"

* `torrent` → Bloquea URLs que contengan "torrent"

**Extensiones de Archivo bloqueadas:**

* `.exe` → Ejecutables Windows

* `.bat` → Scripts de comandos

* `.torrent` → Archivos torrent

* `.mp3` → Archivos de audio

* `.avi` → Archivos de video

## 📚 Conclusiones

### 🎓 Lo que hemos aprendido:

* ✅ Activar y configurar Squid como proxy web en IPFire

* ✅ Diferencia entre proxy normal y proxy transparente

* ✅ Integrar ClamAV con el proxy para análisis antivirus

* ✅ Crear listas negras personalizadas de dominios y URLs

* ✅ Usar comodines para bloqueo flexible

* ✅ Configurar listas negras automáticas por categorías

* ✅ Bloquear descargas por extensión de archivo

* ✅ Filtrar contenidos por expresiones regulares

### 🔄 Resumen de Capas de Filtrado:

| Capa | Herramienta | Qué Filtra |
| --- | --- | --- |
| 1ª | Reglas Firewall | Puertos y protocolos |
| 2ª | Proxy Squid | URLs y dominios |
| 3ª | URL Filter | Categorías y listas negras |
| 4ª | SquidClamav | Contenido malicioso (antivirus) |

### 🎯 Navegación:

← Práctica 04: IDS/IPS con IPFire

→ Práctica 06A: Firewall de Windows

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🔒 [[02 - MODULOS/ISO 27002/03 - Robustecimiento de Sistemas y Redes|ISO 27002 — Robustecimiento y Segmentación de Redes]]

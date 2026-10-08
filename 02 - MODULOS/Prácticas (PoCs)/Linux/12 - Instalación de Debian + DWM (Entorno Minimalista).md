---
title: "🐧 Instalación de Debian + DWM — Entorno Minimalista"
aliases:
  - Debian DWM
  - Instalacion Debian DWM
  - PoC Debian DWM
tags:
  - ifct0109/pocs
  - linux
  - debian
  - dwm
  - hacking-etico
---

# 🐧 Instalación de Debian + DWM — Entorno Minimalista

> [!abstract] Resumen del Laboratorio
> Guía práctica para desplegar un entorno de trabajo ultra liviano, eficiente y altamente personalizable basado en **Debian** junto con el gestor de ventanas en mosaico **DWM** (*Dynamic Window Manager* de Suckless). Ideal para auditores de seguridad y estaciones de hacking ético donde el consumo de recursos y el control total de la interfaz por teclado son prioritarios.

---

# 🐧 Debian + DWM - Entorno Minimalista

## 🪟 Dynamic Window Manager sobre Debian

Construye desde cero un entorno de escritorio minimalista, rápido y completamente personalizable

En este módulo aprenderás a instalar **Debian GNU/Linux** desde cero en una máquina virtual y a configurar **DWM (Dynamic Window Manager)**, un gestor de ventanas en mosaico minimalista de la filosofía *suckless*. Al terminar tendrás un entorno gráfico ligero, rápido y totalmente a tu medida.

### 🎯 Objetivos del Módulo:

* ✅ Instalar Debian estable desde la imagen NetInst en VirtualBox

* ✅ Configurar el sistema base: sudo, paquetes esenciales, ZSH

* ✅ Compilar e instalar DWM, ST y dmenu desde el código fuente

* ✅ Personalizar LightDM: fondo, tema e imagen de usuario

* ✅ Crear un sistema de scripts de inicio modular

* ✅ Personalizar tags, colores y fuentes de DWM

* ✅ Construir una barra de estado con información del sistema y del tiempo

* ✅ Automatizar tareas con cron y scripts de shell

### 🤔 ¿Por Qué DWM?

| Característica | DWM | Escritorio Tradicional (GNOME/KDE) |
| --- | --- | --- |
| Consumo de RAM | ~30-50 MB | 500 MB - 2 GB |
| Configuración | Editando código C y recompilando | Menús gráficos de ajustes |
| Tamaño del código | < 2000 líneas | Millones de líneas |
| Gestión de ventanas | Automática en mosaico | Flotante manual |
| Curva de aprendizaje | Alta (pero gratificante) | Baja |
| Productividad | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |

### 🔄 Flujo General del Módulo

      🐧 Instalar
Debian
      →
      ⚙️ Configurar
Sistema Base
      →
      🔨 Compilar
DWM + ST
      →
      🖥️ Configurar
LightDM
      →
      🎨 Personalizar
Aspecto

## 📚 Prácticas del Módulo

      🏗️ Base del Sistema

        Práctica 01
        🐧
        Instalación de Debian y DWM Intermedio

          Instalación completa de Debian NetInst en VirtualBox. Configuración inicial del sistema: sudo, paquetes esenciales, compilación e instalación de DWM 6.4, ST 0.9 y dmenu. Configuración de ZSH al estilo Kali Linux.

          Debian
          DWM
          VirtualBox
          ZSH
          Compilación

        → Ir a la práctica

      🚀 Configuración del Entorno

        Práctica 02
        🖥️
        Inicio de Sesión y Scripts Intermedio

          Personalización de LightDM: fondo de pantalla, tema y posición del panel de login. Sistema de scripts de inicio modular con `start` e `inicio.sh`. Script de fondo aleatorio con feh y automatización con cron.

          LightDM
          Scripts
          feh
          Crontab
          Autostart

        → Ir a la práctica

      🎨 Personalización Visual

        Práctica 03
        🎨
        Aspecto Visual de DWM Avanzado

          Personalización completa del aspecto: tags con iconos Font Awesome, combinaciones de colores, barra de estado dinámica con IP, RAM, CPU, disco y tiempo meteorológico desde wttr.in. Script dwmbar con actualización automática.

          config.def.h
          Font Awesome
          dwmbar
          wttr.in
          xsetroot

        → Ir a la práctica

## 📊 Resumen del Módulo

| Nº | Práctica | Herramientas Principales | Dificultad | Duración Estimada |
| --- | --- | --- | --- | --- |
| 01 | Instalación de Debian y DWM | VirtualBox, apt, gcc, make, ZSH | ⭐⭐⭐ | 90 min |
| 02 | Inicio de Sesión y Scripts | LightDM, feh, bash, crontab | ⭐⭐⭐ | 60 min |
| 03 | Aspecto Visual de DWM | config.def.h, Font Awesome, xsetroot, wttr.in | ⭐⭐⭐⭐ | 90 min |

### 🛠️ Herramientas y Tecnologías del Módulo

| Herramienta | Descripción | Práctica |
| --- | --- | --- |
| Debian NetInst | Imagen mínima de instalación de Debian estable | 01 |
| DWM 6.4 | Dynamic Window Manager — gestor de ventanas en mosaico | 01, 03 |
| ST 0.9 | Simple Terminal — emulador de terminal minimalista suckless | 01 |
| dmenu | Lanzador de aplicaciones minimalista (Alt+P) | 01 |
| LightDM | Gestor de inicio de sesión gráfico | 02 |
| feh | Visor de imágenes y gestor de fondos de escritorio | 02 |
| ZSH | Shell avanzado con autocompletado y resaltado de sintaxis | 01 |
| Font Awesome | Fuente de iconos para tags y barra de estado | 03 |
| xsetroot | Establece el texto de la barra de estado de DWM | 03 |
| wttr.in | Servicio web de información meteorológica por terminal | 03 |

### ✅ Al Completar este Módulo Serás Capaz de:

* 🎯 Instalar y configurar Debian GNU/Linux desde cero

* 🎯 Compilar software desde el código fuente en C

* 🎯 Gestionar un entorno gráfico sin gestor de escritorio convencional

* 🎯 Personalizar DWM editando directamente su código fuente

* 🎯 Crear scripts de inicio modulares y reutilizables

* 🎯 Usar Font Awesome para personalizar la interfaz con iconos

* 🎯 Construir una barra de estado dinámica con información del sistema

* 🎯 Automatizar tareas periódicas con cron

### 🔗 Recursos Adicionales

| Recurso | URL | Descripción |
| --- | --- | --- |
| Debian Official | debian.org | Página oficial de Debian con descargas y documentación |
| suckless.org | suckless.org | Proyecto suckless: DWM, ST, dmenu y más |
| DWM | dwm.suckless.org | Documentación oficial y patches de DWM |
| Font Awesome v4 | fontawesome.com | Catálogo de iconos para usar en tags y barra |
| wttr.in | wttr.in | Servicio de información meteorológica para terminal |
| Arch Wiki - DWM | wiki.archlinux.org | Documentación detallada sobre DWM (válida para Debian) |

## 📚 Navegación del Curso

        ↑ Volver al Menú Principal

---

## 🔗 Enlaces y Referencias Cruzadas
* 🗂️ [[02 - MODULOS/Prácticas (PoCs)/Linux/00 - Índice del Módulo Linux|Índice del Módulo Linux]]
* 🐧 [[02 - MODULOS/Prácticas (PoCs)/Linux/01 - Curso de Linux - Introducción y Entorno|Curso de Linux — Introducción y Entorno]]
* 📄 [[03 - EXTRA/Documentos/Linux Comandos Esenciales|Linux Comandos Esenciales CheatSheet]]

---
title: "Pivoting"
aliases:
  - Pivoting
tags:
  - ifct0109/pocs
  - auditoria
  - pivoting
---

# Pivoting

## 🔀 Movimiento lateral a través de redes segmentadas

Aprende a usar sistemas comprometidos como plataforma para alcanzar redes internas inaccesibles, dominando las herramientas y técnicas más utilizadas en pentesting profesional.

## ¿Qué es el Pivoting?

El **pivoting** es la técnica que permite a un atacante o pentester moverse lateralmente dentro de una red, usando sistemas ya comprometidos como trampolines para alcanzar segmentos de red que no son directamente accesibles desde el exterior. Es una de las habilidades más importantes en cualquier prueba de penetración de redes complejas y entornos corporativos segmentados.

En la práctica, una red corporativa real nunca expone directamente sus sistemas críticos (controladores de dominio, servidores de bases de datos, sistemas SCADA) al exterior. Estos sistemas solo son accesibles desde redes internas. El pivoting permite, tras comprometer un sistema perimetral, avanzar hacia esos objetivos internos saltando de máquina en máquina a través de las distintas subredes.

### Tipos de pivoting

* Pivoting directo: desde la primera máquina comprometida se accede directamente a otros sistemas de su misma red interna.

* Pivoting a través de saltos: se encadenan múltiples máquinas comprometidas, usando cada una como nuevo punto de entrada hacia redes más profundas.

* Pivoting en cadena: variante más compleja que combina saltos a través de distintas subredes y capas de segmentación.

### 🎯 Objetivos del módulo

* ✅ Comprender qué es el pivoting y cuándo aplicarlo

* ✅ Usar Socat para encadenar redirecciones de tráfico entre subredes

* ✅ Crear túneles SSH dinámicos y usarlos con Proxychains

* ✅ Desplegar Chisel para crear proxies SOCKS inversos a través de firewalls

* ✅ Combinar Chisel y Socat para escenarios de múltiples saltos

* ✅ Usar Ligolo-ng para crear interfaces TUN y rutas de red reales

* ✅ Aplicar técnicas de reconocimiento, explotación y escalada en cada red alcanzada

## 📦 Recursos necesarios

Descarga el archivo con la OVA del laboratorio y todos los binarios necesarios para las prácticas:

      📦

pivoting.7z

        Herramientas necesarias (socat, chisel, ligolo, nmap, scripts)

      [⬇️ Descargar](../../../archivos_descargados/paquetes_7z/pivoting.7z)

## 📚 Prácticas del módulo

      🔀 Técnicas de pivoting

      [
        Práctica 01
        🔌
        Pivoting con Socat

          Introducción al pivoting y sus tipos. Uso de Socat para encadenar redirecciones de tráfico entre múltiples subredes. Obtención de una reverse shell desde una red profunda hasta Kali mediante una cadena de relays Socat.

          Socat
          Relay
          Reverse shell
          Cadena

        → Ir a la práctica
      ]([[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/10a - Pivoting - Práctica 1 - Redirección y Relays con Socat|10a - Pivoting 1 - Socat]])

      [
        Práctica 02
        🔐
        Pivoting con Túnel SSH

          Túnel SSH dinámico con la opción `-D` y Proxychains para enrutar tráfico. Reconocimiento de red interna, enumeración web, explotación de LFI, fuerza bruta SSH y escalada de privilegios con sudo.

          SSH
          Proxychains
          LFI
          Fuerza bruta
          Escalada

        → Ir a la práctica
      ]([[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/10b - Pivoting - Práctica 2 - Túnel SSH Dinámico y Proxychains|10b - Pivoting 2 - Túnel SSH]])

      [
        Práctica 03
        ⛏️
        Pivoting con Chisel

          Chisel como herramienta de tunelización TCP/UDP sobre HTTP. Proxy SOCKS inverso para atravesar firewalls. Encadenamiento de túneles SSH y Chisel para alcanzar la red `20.20.20.0/24`. Escalada con vim.

          Chisel
          SOCKS
          Proxy inverso
          Gobuster
          Hydra

        → Ir a la práctica
      ]([[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/10c - Pivoting - Práctica 3 - Pivoting con Chisel|10c - Pivoting 3 - Chisel]])

      [
        Práctica 04
        🔗
        Pivoting con Chisel + Socat

          Escenario de cuatro saltos de red combinando Chisel (túnel SOCKS) y Socat (relays). Distribución de herramientas en cadena, subida de reverse shell PHP y explotación de la víctima final en la red `30.30.30.0/24`.

          Chisel
          Socat
          PHP shell
          4 saltos
          Upload

        → Ir a la práctica
      ]([[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/10d - Pivoting - Práctica 4 - Encadenamiento Chisel y Socat (4 Saltos)|10d - Pivoting 4 - Chisel + Socat]])

      [
        Práctica 05
        🌐
        Pivoting con Ligolo-ng

          Ligolo-ng como alternativa superior a proxychains: interfaces TUN y rutas de red reales en Kali. Tres túneles encadenados para alcanzar las redes `10.10.10.x`, `20.20.20.x` y `30.30.30.x` con port forwarding integrado.

          Ligolo-ng
          TUN
          Rutas de red
          Port forwarding
          3 túneles

        → Ir a la práctica
      ]([[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/10e - Pivoting - Práctica 5 - Pivoting Avanzado con Ligolo-ng|10e - Pivoting 5 - Ligolo-ng]])

## 📚 Navegación del curso

      [
        ↑ Volver al Menú Principal
      ]([[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/01 - Escaneo y Enumeración de Redes con Nmap|Índice de Herramientas de Auditoría]])

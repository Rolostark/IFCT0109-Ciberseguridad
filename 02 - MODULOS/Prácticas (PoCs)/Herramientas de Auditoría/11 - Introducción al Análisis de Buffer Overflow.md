---
title: "Buffer Overflow"
aliases:
  - Buffer Overflow
tags:
  - ifct0109/pocs
  - auditoria
  - bufferoverflow
---

# Buffer Overflow

## 💥 Comprendiendo y explotando desbordamientos de búfer

Aprende qué es un buffer overflow, cómo se produce a nivel de memoria y cómo un atacante puede aprovechar esta vulnerabilidad para tomar el control de un programa.

## ¿Qué es un Buffer Overflow?

Un **buffer overflow** (desbordamiento de búfer) es una de las vulnerabilidades más antiguas y a la vez más peligrosas de la historia de la seguridad informática. Ocurre cuando un programa escribe más datos en un bloque de memoria (búfer) de los que este puede contener, sobreescribiendo zonas de memoria adyacentes que no deberían modificarse.

Para entenderlo, imaginemos un vaso de agua: si intentamos verter más agua de la que cabe, el exceso se derrama y moja lo que haya alrededor. En memoria, ese "derrame" puede sobreescribir variables críticas, punteros de función o, lo que es más importante, la **dirección de retorno** de una función, que indica al procesador a dónde debe saltar cuando la función termine de ejecutarse.

### ¿Cómo funciona a nivel de memoria?

Cuando un programa llama a una función, el sistema operativo organiza la memoria en una estructura llamada **pila (stack)**. En ella se almacenan, entre otras cosas, los parámetros de la función, las variables locales y la dirección de retorno. Si existe un búfer en esa función y el programa no comprueba el tamaño de los datos que escribe en él, un atacante puede enviar datos más largos de lo esperado para sobrescribir la dirección de retorno y redirigir la ejecución del programa a una ubicación arbitraria, como por ejemplo un shellcode inyectado por el propio atacante.

### Impacto real

Los buffer overflows han sido responsables de algunos de los ataques más devastadores de la historia: el gusano Morris (1988), el exploit de Code Red (2001), la vulnerabilidad de Windows que permitió la propagación de WannaCry (2017) y muchos otros. Aunque los compiladores modernos y los sistemas operativos incorporan protecciones como **ASLR** (Address Space Layout Randomization), **DEP/NX** (Data Execution Prevention) y **stack canaries**, los buffer overflows siguen siendo relevantes, especialmente en software embebido, sistemas heredados y aplicaciones mal compiladas.

### Tipos de buffer overflow

* Stack-based buffer overflow: el más clásico. El búfer desbordado está en la pila, y el atacante sobreescribe la dirección de retorno.

* Heap-based buffer overflow: el búfer está en el montón (heap), la zona de memoria dinámica. Más complejo de explotar pero igualmente peligroso.

* Off-by-one: variante donde el desbordamiento es de un único byte, suficiente para alterar el comportamiento del programa.

> En este módulo trabajaremos con un programa vulnerable de ejemplo para comprender primero la vulnerabilidad y después aplicar una explotación real. Todo el trabajo se realizará en un entorno controlado de laboratorio.

## 📦 Recursos necesarios

Descarga el archivo con el programa vulnerable y las herramientas necesarias para las prácticas:

      📦

BufferOverflow.7z

        Programa vulnerable y herramientas de explotación para las prácticas

      [⬇️ Descargar](../../../archivos_descargados/paquetes_7z/BufferOverflow.7z)

## 📚 Prácticas del módulo

      [
        Práctica 01
        🔎
        La vulnerabilidad

          Análisis de un programa vulnerable a buffer overflow. Identificación de la vulnerabilidad en el código, comprensión del comportamiento en memoria y primeros pasos para caracterizar el fallo antes de la explotación.

          Vulnerabilidad
          Stack
          Análisis
          Memoria

        → Ir a la práctica
      ]([[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/11a - Buffer Overflow - Práctica 1 - Identificación de la Vulnerabilidad|11a - Buffer Overflow 1 - Análisis]])

      [
        Práctica 02
        💥
        La explotación

          Explotación práctica del buffer overflow identificado. Control del flujo de ejecución, sobreescritura de la dirección de retorno y ejecución de código arbitrario en el programa vulnerable.

          Explotación
          Shellcode
          EIP
          Payload

        → Ir a la práctica
      ]([[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/11b - Buffer Overflow - Práctica 2 - Control de Flujo y Shellcode|11b - Buffer Overflow 2 - Explotación]])

## 📚 Navegación del curso

      [
        ↑ Volver al Menú Principal
      ]([[02 - MODULOS/Prácticas (PoCs)/Herramientas de Auditoría/01 - Escaneo y Enumeración de Redes con Nmap|Índice de Herramientas de Auditoría]])

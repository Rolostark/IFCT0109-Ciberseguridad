---
title: "🏢 Práctica: Active Directory en Windows Server 2019"
aliases:
  - Active Directory Windows Server
  - WS2019 AD
  - PoC Active Directory
tags:
  - ifct0109/pocs
  - sistemas
  - windows-server
  - active-directory
  - gpo
---

# 🏢 Práctica: Configuración de Active Directory en Windows Server 2019

> [!abstract] Resumen del Laboratorio
> Instalación, promoción y administración de un controlador de dominio (**Domain Controller**) bajo Windows Server 2019. Configuración del servicio DNS, creación de Unidades Organizativas (OUs), altas de usuarios y grupos de seguridad, y despliegue de Directivas de Grupo (**GPO**) para robustecimiento perimetral y restricción de estaciones cliente.

---

# 🖥️ Active Directory en Windows Server 2019

## Parte 1: Enunciado y Configuración Inicial

## 📖 ENUNCIADO (DESCRIPCIÓN)

**Active Directory** (AD) es un servicio de directorio desarrollado por **Microsoft** que se utiliza para gestionar y organizar los recursos en una red de computadoras. Básicamente, es una base de datos que almacena información sobre los **usuarios**, **equipos** y otros **dispositivos** en una red, y permite a los administradores gestionar esos recursos de forma **centralizada**.

En un entorno de Active Directory, los **administradores** pueden:

* Gestionar cuentas de usuario: Crear, modificar o eliminar cuentas de usuario.

* Gestionar accesos y permisos: Controlar qué usuarios tienen acceso a qué recursos en la red (archivos, impresoras, etc.).

* Aplicar políticas de seguridad: Configurar reglas de seguridad que se apliquen de forma centralizada a los equipos y usuarios de la red (como contraseñas seguras, bloqueo de cuentas, etc.).

* Delegar administración: Dar permisos a ciertos usuarios para que gestionen partes específicas del directorio sin tener que otorgarles control total.

Active Directory es fundamental en la gestión de la seguridad informática, ya que ofrece una manera centralizada y estructurada para aplicar políticas de seguridad en toda la organización.

* Autenticación y Autorización: AD asegura que solo los usuarios autorizados puedan acceder a los recursos mediante el uso de credenciales y políticas de acceso.

* Políticas de Grupo (GPOs): Los administradores pueden establecer configuraciones de seguridad, como la longitud mínima de las contraseñas, la expiración de contraseñas o el control del acceso a ciertos recursos.

* Auditoría: Con AD, se puede hacer un seguimiento de las actividades de los usuarios (quién accede a qué y cuándo), lo cual es vital para la detección de actividades sospechosas o no autorizadas.

* Seguridad a nivel de red: A través de las OUs, es posible segmentar y aplicar medidas de seguridad específicas para diferentes grupos de usuarios o dispositivos.

## 🚀 Prueba de Concepto (PoC)

**Vamos a trabajar con la OVA WS2019 por lo que la importamos.**

**Antes de iniciarla la ponemos en RED Adaptador Puente y la iniciamos.**

Una vez iniciada la VM tenemos que iniciar sesión pulsando Ctrl + Alt + Supr.

Para ello tenemos que seguir la siguiente secuencia en el menú de VirtualBox.

**Entrada > Teclado > Insertar Ctrl+Alt+Del**

Y ahora introducimos las credenciales de acceso.

> [!warning] Credenciales de acceso:
>         Usuario:  Administrador
>         Password:  SegInf.2093

**Recomendado instalar las Guest Additions y habilitar portapapeles bidireccional.**

## ⚙️ Configuración Inicial

Antes de avanzar y configurar el dominio lo primero que vamos a hacer es configurar una IP fija en el servidor y cambiar el nombre del servidor.

### 📝 Cambiar el Nombre del Equipo

Pulsamos botón **derecho** en inicio y luego vamos a Sistema.

Una vez dentro pulsamos en Cambiar el nombre de este equipo.

El nombre del equipo se te va a asignar por orden y va a tener la estructura SGXX donde XX será un determinado número.

Al cambiar el nombre del equipo nos hará reiniciar.

De momento pulsamos reiniciar más tarde.

### 🌐 Configurar IP Fija

Ahora vamos a configurar una IP fija. Seguimos los siguientes pasos:

Pulsamos botón derecho en Inicio > Conexiones de red > Y cambiar opciones del adaptador.

En la ventana siguiente pulsamos Botón derecho en el adaptador Ethernet y Vamos a Propiedades:

Ahora en el listado que aparece hacemos doble clic en Protocolo de Internet Versión 4

O lo seleccionamos y pulsamos en Propiedades.

En la ventana siguiente configuramos la IP fija según los siguientes valores:

> [!warning] Nota:  El último número de la Dirección IP (en la captura el 50) te habrá sido asignado al iniciar la práctica, ese número es exclusivo, NO debes poner el que aparece en la captura sino que debes poner el tuyo.

Una vez configurada la IP pulsamos en Aceptar, nuevamente en Aceptar. Respondemos SI si aparece la siguiente imagen:

Y reiniciamos la VM de Windows Server.

    Anterior
    Siguiente: Instalar Características de AD

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🖥️ [[02 - MODULOS/Prácticas (PoCs)/Sistemas/01 - Creación de NAS con OpenMediaVault|Servidor NAS OpenMediaVault]]

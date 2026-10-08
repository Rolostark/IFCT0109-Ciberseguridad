---
title: "🖥️ Active Directory - Parte 2: Instalar Características"
aliases:
  - 🖥️ Active Directory - Parte 2: Instalar Características
tags:
  - ifct0109/pocs
  - sistemas
  - windows-server
  - active-directory
---

# 🖥️ Active Directory en Windows Server 2019

  

## Parte 2: Instalar Características de Active Directory

  
  
    

## 📦 Instalar Características de Active Directory

    
    

Vamos a promover el Servidor a un Controlador de dominio siguiendo los siguientes pasos.

    
    

Comenzamos pulsando en Inicio > Administrador del servidor

    
    

![Administrador del servidor](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image13.png)

    
    

Cerramos la ventana de advertencia que aparece.

    
    

![Advertencia](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image14.png)

    
    

A continuación pulsamos en Agregar roles y características:

    

### 📋 Tipo de instalación

    
    

Instalación basada en roles y pulsamos siguiente.

    
    

![Tipo de instalación](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image15.png)

    

### 🖥️ Elegir servidor

    
    

Marcamos el servidor y pulsamos siguiente

    
    

![Seleccionar servidor](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image16.png)

    
    

Ahora tenemos que marcar Servicios de dominio de active directory. Importante NO hay que marcar Servicios de Certificados de active directory.

    
    

![Servicios de dominio AD](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image17.png)

    
    

Al marcarlo aparece una ventana en la que pulsamos en agregar características.

    
    

![Agregar características](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image18.png)

    
    

Una vez marcado Servicios de dominio de Active Directory pulsamos en Siguiente:

    
    

![Confirmación servicios](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image19.png)

    
    

En la ventana de Seleccionar características también pulsamos en Siguiente sin marcar nada:

    
    

![Seleccionar características](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image20.png)

    
    

Nuevamente Siguiente en la ventana.

    
    

![AD DS](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image21.png)

    
    

Y en ahora marcamos Reiniciar si es necesario (sale una ventana pidiendo confirmación y pulsamos en si) y ya pulsamos en Instalar.

    
    

![Confirmar instalación](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image22.png)

    
    

Comenzará ahora el proceso de instalación.

    
    

![Progreso instalación](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image23.png)

    
    

Esta ventana podemos cerrarla.

    
    

En la ventana de Administrador del servidor hay un icono de una bandera. Cuando termina de instalarse las características necesarios, en esa zona aparece un enlace para promover el servidor a controlador de dominio.

    
    

Si hemos tenido que reiniciar deberíamos acceder a Inicio > Administrador del servidor.

    
    

![Promover servidor](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image24.png)

    
    

Esta opción sólo aparece disponible cuando termina de instalar todo.

  

  
    

## 🎖️ Promover servidor a controlador de dominio

    
    

Entramos en Inicio > Administrador del Servidor.

    
    

En el área de notificaciones pulsamos a promover este servidor a controlador de dominio.

    
    

![Notificación promover](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image25.png)

    
    

Ahora vamos a crear un nuevo bosque. El nombre del bosque tendrá la siguiente estructura:

    
    

wsgr**XX**.local (Donde XX es el número que tienes asignado)

    
    

![Configuración implementación](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image26.png)

    
    

Pulsamos en siguiente.

    
    

En la siguiente ventana definimos la contraseña de restauración. Puede que tarde un poco hasta que nos deje escribirla.

    
    

Contraseña restauración: SegInf.2131

    
    

(Es contraseña de laboratorio se deben usar siempre contraseñas seguras)

    
    

![Opciones controlador dominio](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image27.png)

    
    

Pulsamos en siguiente.

    
    

En la siguiente ventana NO vamos a crear delegación DNS por lo que pulsamos en siguiente sin marcar nada.

    
    

![Opciones DNS](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image28.png)

    
    

Ahora esperamos hasta que nos deje escribir el Nombre de Dominio NetBIOS, si todo va bien cogerá el nombre automáticamente con la estructura WSGRXX (Donde XX es el número que se te asignó)

    
    

![Opciones adicionales](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image29.png)

    
    

Pulsamos si todo es correcto en siguiente.

    
    

En la siguiente ventana dejamos las rutas de acceso como está y pulsamos en siguiente:

    
    

![Rutas de acceso](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image30.png)

    
    

Igualmente en la ventana revisar opciones pulsamos siguiente:

    
    

![Revisar opciones](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image31.png)

    
    

Ahora hace una comprobación de requisitos previos si todo va bien el botón de instalar aparece como disponible, lo pulsamos.

    
    

![Comprobación requisitos](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image32.png)

    
    

Comienza el proceso de instalación, el cual se reiniciará automáticamente.

    
    

Al iniciar después del reinicio ya tendremos el dominio creado.

    
    

![Dominio creado](../../../archivos_descargados/imagenes/sistemas/ws2019_images/media/image33.png)

  

  
  

    [Anterior: Configuración Inicial]([[02 - MODULOS/Prácticas (PoCs)/Sistemas/02a - Active Directory - Parte 1 - Configuración Inicial|Parte 1: Configuración Inicial]])
    [Siguiente: Configuración del AD]([[02 - MODULOS/Prácticas (PoCs)/Sistemas/02c - Active Directory - Parte 3 - Configuración del AD|Parte 3: Configuración del AD]])

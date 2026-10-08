---
title: "🎯 Retos Prácticos de Wireshark"
aliases:
  - 🎯 Retos Prácticos de Wireshark
tags:
  - ifct0109/pocs
  - auditoria
  - wireshark
  - retos
---

# 🎯 Retos Prácticos de Wireshark

  
  
    

## 📖 INSTRUCCIONES

    
    

Bienvenido a los retos prácticos de Wireshark. En esta sección pondrás a prueba tus conocimientos analizando capturas de tráfico real.

    
    

### 📋 Cómo funciona:

    
      
* 🔽 **Descarga** el archivo de captura (.pcapng) de cada reto
      
* 📂 **Abre** el archivo en Wireshark
      
* 🔍 **Analiza** el tráfico usando filtros y las técnicas aprendidas
      
* ✅ **Responde** las preguntas seleccionando las opciones correctas
      
* 🎯 **Verifica** tus respuestas pulsando el botón correspondiente
      
* 💡 Si te atascas, puedes **ver la solución completa** con explicaciones
    

    

      

**💡 Consejo:** Intenta resolver cada reto por ti mismo antes de ver la solución. El aprendizaje es mayor cuando investigas y pruebas diferentes filtros.

    

  

  
  
    

      

### 🎯 RETO 1: Análisis de Navegación Web

      
      

        

**Descarga el archivo de captura para este reto:**

        [📥 Descargar Reto01.pcapng](retos/Reto01.pcapng)
      

      

Analiza la captura y responde las siguientes preguntas sobre la navegación web del usuario.

      
      

        

#### Pregunta 1: ¿El usuario se conectó a www.wireshark.es?

        
        

          
            
            Sí
          
          
            
            No
          
        

      

      
      

        

#### Pregunta 2: El navegador permitido es Microsoft Edge, pero el usuario instaló otro. ¿Cuál de estos tres?

        
        

          
            
            Firefox
          
          
            
            Opera
          
          
            
            Safari
          
        

      

      
      

        

#### Pregunta 3: ¿Qué navegador se usó para conectarse a "as.com"?

        
        

          
            
            Microsoft Edge
          
          
            
            Firefox
          
        

      

      
      

        

#### Pregunta 4: ¿Qué navegador se usó para "youtube.com"?

        
        

          
            
            Microsoft Edge
          
          
            
            Firefox
          
        

      

      
      

        

#### Pregunta 5: ¿Qué navegador se usó para "networkthinker.com"?

        
        

          
            
            Microsoft Edge
          
          
            
            Firefox
          
        

      

      

        ✅ Verificar Respuestas
        💡 Ver Solución Completa
      

      

      

        

#### 📚 Solución Completa del Reto 1

        
        

**Respuestas correctas:**

        
          
* **¿Navegó por wireshark.es?** → **NO**
            
              **Método 1:** Estadísticas → HTTP → Requests (no aparece wireshark.es)
              
* **Método 2:** Filtro `http contains "wireshark.es"` (0 resultados)
            
          
          
          
* **Navegador no autorizado instalado:** → **Firefox**
            
              Filtro: `http.user_agent`
              
* Botón derecho en línea con Edge → Apply as filter → ... and not selected
              
* Añade `&& http.user_agent` al filtro resultante
              
* Verás que usa Firefox además de Edge
            
          
          
          
* **Navegador usado en as.com:** → **Edge**
            
              Filtro: `http contains "as.com"`
              
* Revisar campo `User-Agent` en los paquetes
            
          
          
          
* **Navegador usado en youtube.com:** → **Firefox**
            
              Filtro: `http contains "youtube.com"`
            
          
          
          
* **Navegador usado en networkthinker.com:** → **Firefox**
            
              Filtro: `http contains "networkthinker.com"`
            
          
        

        

**🎓 Aprendizajes clave:**

        
          
* El campo `http.user_agent` identifica el navegador y sistema operativo
          
* Puedes usar estadísticas HTTP para ver todas las URLs visitadas
          
* Los filtros combinados permiten análisis más específicos
        
      

    

  

  
  
    

      

### 🎯 RETO 2: Análisis de Descarga de Imágenes

      
      

        

**Descarga el archivo de captura para este reto:**

        [📥 Descargar Reto02.pcapng](retos/Reto02.pcapng)
      

      

Analiza qué usuario está descargando diferentes tipos de imágenes.

      
      

        

#### Pregunta 1: ¿Qué IP tiene el usuario que descarga imágenes PNG?

        
        

          
            
            10.10.1.30
          
          
            
            10.10.1.11
          
          
            
            Ninguna de las anteriores
          
        

      

      
      

        

#### Pregunta 2: ¿El mismo usuario descargó también imágenes GIF?

        
        

          
            
            Sí
          
          
            
            No
          
        

      

      
      

        

#### Pregunta 3: ¿De cuántos sitios distintos se descargaron imágenes JPG (jfif)?

        
        

          
            
            2 sitios
          
          
            
            11 sitios
          
          
            
            Se desconoce
          
        

      

      
      

        

#### Pregunta 4: ¿A qué personaje famoso pertenece la tercera imagen JPG que menos ocupa?

        
        

          
            
            Scarlett Johansson
          
          
            
            Rafa Nadal
          
          
            
            Russell Crowe
          
        

      

      

        ✅ Verificar Respuestas
        💡 Ver Solución Completa
      

      

      

        

#### 📚 Solución Completa del Reto 2

        
        

**Respuestas correctas:**

        
          
* **IP que descarga PNG:** → **10.10.1.30**
            
              Filtro: `png`
              
* Observa la columna `Destination` (IP destino)
              
* Todas las imágenes PNG van a 10.10.1.30
            
          
          
          
* **¿Descargó también GIF?** → **SÍ (46 archivos)**
            
              Filtro: `ip.dst == 10.10.1.30 && image-gif`
              
* Resultado: 46 paquetes con imágenes GIF
            
          
          
          
* **Sitios distintos con JPG:** → **2 sitios**
            
              Filtro: `image-jfif`
              
* Mira la barra de estado (muestra paquetes totales)
              
* Revisa las IPs de origen: solo 2 servidores distintos
            
          
          
          
* **Tercera imagen JPG más pequeña:** → **Rafa Nadal**
            
              Filtro: `image-jfif`
              
* Ordena por tamaño (clic en columna Length)
              
* Selecciona el tercer paquete más pequeño
              
* Botón derecho en capa JFIF → Show Packet Bytes
              
* Visualiza la imagen: es Rafa Nadal
            
          
        

        

**🎓 Aprendizajes clave:**

        
          
* Filtros de tipo de imagen: `png`, `image-gif`, `image-jfif`
          
* Combinar filtros de IP con filtros de contenido
          
* Ordenar columnas para análisis de tamaño
          
* Extraer y visualizar imágenes con "Show Packet Bytes"
        
      

    

  

  
  
    

      

### 🎯 RETO 3A: Detección de Protocolos Inseguros

      
      

        

**Descarga el archivo de captura para este reto:**

        [📥 Descargar Reto03a.pcapng](retos/Reto03a.pcapng)
      

      

Un hacker está monitorizando protocolos inseguros (FTP, Telnet). Identifica las conexiones comprometidas.

      
      

        

#### Pregunta 1: ¿Desde qué equipo se estableció conexión exitosa por FTP o Telnet?

        
        

          
            
            10.10.1.11
          
          
            
            10.10.1.30
          
          
            
            10.10.1.32
          
        

      

      
      

        

#### Pregunta 2: ¿Con qué usuario se estableció la conexión?

        
        

          
            
            admin
          
          
            
            network
          
          
            
            zinedine
          
        

      

      
      

        

#### Pregunta 3: ¿Cuál es la contraseña?

        
        

          
            
            1234
          
          
            
            Thinker
          
          
            
            Network
          
        

      

      
      

        

#### Pregunta 4: ¿Se vieron comprometidos datos de configuración de red?

        
        

          
            
            Sí
          
          
            
            No
          
        

      

      

        ✅ Verificar Respuestas
        💡 Ver Solución Completa
      

      

      

        

#### 📚 Solución Completa del Reto 3A

        
        

**Respuestas correctas:**

        
          
* **IP que estableció conexión:** → **10.10.1.11**
            
              Filtro: `telnet`
              
* Hay tráfico Telnet presente
              
* Añade el destino al filtro (10.10.1.1)
              
* Ves conexiones desde 10.10.1.11 y 10.10.1.32
              
* Sigue el flujo TCP de cada una
              
* Solo 10.10.1.11 tiene autenticación exitosa
            
          
          
          
* **Usuario usado:** → **admin**
            
              En el flujo TCP de 10.10.1.11 verás el login
              
* Usuario: `admin`
            
          
          
          
* **Contraseña:** → **Network**
            
              En el mismo flujo TCP, después del usuario
              
* Contraseña: `Network`
            
          
          
          
* **¿Datos de configuración comprometidos?** → **SÍ**
            
              En el flujo TCP verás el comando `Get Config`
              
* El atacante obtuvo la configuración del dispositivo
            
          
        

        

**🎓 Aprendizajes clave:**

        
          
* Telnet transmite TODO en texto plano (usuarios, contraseñas, comandos)
          
* Seguir flujos TCP permite reconstruir sesiones completas
          
* Los protocolos inseguros son extremadamente vulnerables a sniffing
          
* Siempre usar SSH en lugar de Telnet
        
      

    

  

  
  
    

      

### 🎯 RETO 3B: Exfiltración de Datos por USB

      
      

        

**Descarga el archivo de captura para este reto:**

        [📥 Descargar Reto03b.pcapng](retos/Reto03b.pcapng)
      

      

Analiza si hubo exfiltración de datos sensibles a través de USB.

      
      

        

#### Pregunta 1: ¿Hay indicios de tráfico USB?

        
        

          
            
            Sí
          
          
            
            No
          
        

      

      
      

        

#### Pregunta 2: ¿Se llevó el contenido de la configuración del Firewall?

        
        

          
            
            Sí
          
          
            
            No
          
        

      

      

        ✅ Verificar Respuestas
        💡 Ver Solución Completa
      

      

      

        

#### 📚 Solución Completa del Reto 3B

        
        

**Respuestas correctas:**

        
          
* **¿Tráfico USB presente?** → **SÍ**
            
              Filtro: `usb`
              
* Resultado: Hay tráfico USB capturado
            
          
          
          
* **¿Se exfiltró configuración del Firewall?** → **SÍ**
            
              Filtro: `usb && frame contains "Firewall"`
              
* Hay paquetes que contienen la palabra "Firewall"
              
* Botón derecho en campo "leftover" → Show Packet Bytes
              
* Se visualiza el contenido de la configuración del firewall
              
* Datos sensibles fueron copiados a USB
            
          
        

        

**🎓 Aprendizajes clave:**

        
          
* Wireshark puede capturar tráfico USB además de tráfico de red
          
* El campo `frame contains "texto"` busca en todo el paquete
          
* Los datos USB pueden revelar información sensible copiada a dispositivos externos
          
* Importante monitorizar tráfico USB en entornos de alta seguridad
        
      

    

  

  
  
    

      

### 🎯 RETO 4: Análisis de Acceso a Servidor y FTP

      
      

        

**Descarga el archivo de captura para este reto:**

        [📥 Descargar Reto04.pcapng](retos/Reto04.pcapng)
      

      

Analiza intentos de acceso a un servidor web y FTP.

      
      

        

#### Pregunta 1: ¿Qué máquina hay instalada en 192.168.1.12?

        
        

          
            
            Proxmox
          
          
            
            Open Media Vault
          
          
            
            Windows Server
          
        

      

      
      

        

#### Pregunta 2: ¿Con qué navegador se conecta desde 192.168.1.101?

        
        

          
            
            Firefox
          
          
            
            Chrome
          
          
            
            Edge
          
        

      

      
      

        

#### Pregunta 3: El usuario por defecto es "admin". ¿Inicia sesión 192.168.1.101 en 192.168.1.12?

        
        

          
            
            Sí
          
          
            
            No
          
        

      

      
      

        

#### Pregunta 4: ¿La máquina 192.168.1.12 usa certificado de conexión segura?

        
        

          
            
            Sí
          
          
            
            No
          
        

      

      
      

        

#### Pregunta 5: ¿Qué usuario intenta conectarse al FTP?

        
        

          
            
            root
          
          
            
            ftpuser
          
          
            
            anónimo
          
        

      

      
      

        

#### Pregunta 6: ¿Con qué clave NO logra conectarse?

        
        

          
            
            abc.123
          
          
            
            20182018
          
          
            
            root
          
        

      

      

        ✅ Verificar Respuestas
        💡 Ver Solución Completa
      

      

      

        

#### 📚 Solución Completa del Reto 4

        
        

**Respuestas correctas:**

        
          
* **Máquina en 192.168.1.12:** → **Open Media Vault**
            
              Filtro: `http.request.method == "POST"`
              
* Botón derecho → Follow TCP Stream
              
* En el flujo se menciona "Open Media Vault"
            
          
          
          
* **Navegador desde 192.168.1.101:** → **Firefox**
            
              Filtro: `ip.src == 192.168.1.101 && http`
              
* Revisar campo `User-Agent`
              
* Muestra Firefox
            
          
          
          
* **¿Inicia sesión exitosamente?** → **SÍ**
            
              En el flujo TCP de las peticiones POST
              
* Se ve el usuario "admin" y contraseña
              
* La autenticación es exitosa
            
          
          
          
* **¿Usa certificado seguro?** → **NO**
            
              La conexión es HTTP (puerto 80), no HTTPS
              
* Si fuera HTTPS no podríamos ver las credenciales en texto plano
              
* Conexión completamente insegura
            
          
          
          
* **Usuario FTP:** → **ftpuser**
            
              Filtro: `ftp`
              
* Busca comandos `USER`
              
* Usuario: `ftpuser`
            
          
          
          
* **Clave con la que NO se conecta:** → **Abc.123**
            
              En el tráfico FTP busca comandos `PASS`
              
* Seguido de respuesta `530 Login incorrect`
              
* La contraseña fallida es `abc.123`
            
          
        

        

**🎓 Aprendizajes clave:**

        
          
* Método POST se usa para enviar credenciales y formularios
          
* HTTP sin cifrar expone completamente las credenciales
          
* FTP también transmite usuarios y contraseñas en texto plano
          
* Siempre usar HTTPS para interfaces web administrativas
          
* Usar SFTP o FTPS en lugar de FTP tradicional
        
      

    

  

  
    

## 🎉 ¡Felicidades!

    
    

Has completado los retos prácticos de Wireshark. Ahora dominas:

    
    
      
* ✅ Captura y análisis de tráfico de red
      
* ✅ Uso de filtros de captura y visualización
      
* ✅ Seguimiento de flujos TCP/HTTP
      
* ✅ Extracción de imágenes y archivos
      
* ✅ Detección de credenciales en texto plano
      
* ✅ Identificación de protocolos inseguros
      
* ✅ Análisis forense de tráfico
    

    

      

### 🔐 Lecciones de Seguridad Aprendidas

      

Estos retos demuestran por qué es crucial:

      
        
* 🔒 Usar siempre HTTPS en lugar de HTTP
        
* 🔒 Usar SSH en lugar de Telnet
        
* 🔒 Usar SFTP/FTPS en lugar de FTP
        
* 🔒 Cifrar datos sensibles en tránsito
        
* 🔒 No confiar en "seguridad por oscuridad"
        
* 🔒 Monitorizar tráfico sospechoso en la red
      
    

    [🔧 Ver Alternativas a Wireshark →](wireshark_alternativas.html)

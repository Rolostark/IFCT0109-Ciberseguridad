---
title: "📖 Práctica: Ataques Man-in-the-Middle y Envenenamiento ARP"
aliases:
  - Ataques Man-in-the-Middle y Envenenamiento ARP
  - PoC mitm
tags:
  - ifct0109/pocs
  - conceptos-fundamentales
  - mitm
  - arp-spoofing
  - ettercap
  - redes
---

# 📖 Práctica: Ataques Man-in-the-Middle y Envenenamiento ARP

> [!abstract] Resumen del Laboratorio
> Módulo de Conceptos Fundamentales del Certificado IFCT0109: análisis de mecanismos base de redes, inspección de protocolos, desarrollo de pruebas de concepto e interactividad con herramientas de diagnóstico y auditoría técnica.

---

# 🕵️ Ataque Man In The Middle (ARP Spoofing)

## 📖 ENUNCIADO (DESCRIPCIÓN)

Vamos a realizar un ataque Man In The Middle sobre una máquina virtual con Windows 7, desde una máquina virtual con Kali Linux, ambas en la misma red.

## 📚 CONCEPTOS PREVIOS

### 🔌 ¿Qué es el Protocolo ARP?

El protocolo **ARP (Protocolo de Resolución de Direcciones)** se utiliza para convertir las direcciones IP en direcciones MAC. Cada dispositivo en una red local tiene una dirección IP y una dirección MAC única, y cuando un dispositivo necesita enviar un paquete a otro dispositivo, utiliza la dirección MAC del destinatario para encontrarlo en la red.

#### 💡 Funcionamiento Normal de ARP

* Dispositivo A quiere enviar datos a Dispositivo B

* Dispositivo A conoce la IP de B, pero NO su dirección MAC

* Dispositivo A envía un mensaje ARP broadcast: "¿Quién tiene la IP 192.168.10.202?"

* Dispositivo B responde: "Yo tengo esa IP, mi MAC es AA:BB:CC:DD:EE:FF"

* Dispositivo A guarda esta información en su tabla ARP

### ☠️ ¿Qué es el Envenenamiento ARP (ARP Spoofing)?

El **envenenamiento ARP** (también conocido como spoofing o suplantación de ARP) es un tipo de ataque informático en el que un atacante envía mensajes ARP falsificados a una red local con el fin de asociar su dirección MAC con la dirección IP de otro dispositivo en la red.

El envenenamiento ARP ocurre cuando un atacante envía mensajes ARP falsificados que contienen una dirección MAC diferente para una dirección IP determinada. Esto hace que la víctima envíe sus paquetes a la dirección MAC falsificada en lugar del destinatario real.

#### ⚠️ Flujo del Ataque MITM con ARP Spoofing

* Atacante (Kali) envía mensajes ARP falsos a la víctima

* Mensaje dice: "Yo soy el router, mi MAC es XX:XX:XX:XX:XX:XX"

* Víctima (Windows 7) actualiza su tabla ARP con la MAC del atacante

* Víctima envía TODO su tráfico al atacante creyendo que es el router

* Atacante captura el tráfico y lo reenvía al router real

* La víctima NO detecta nada anormal (tiene internet)

### 🎯 Objetivo de esta Práctica

* ✅ Comprender cómo funciona el protocolo ARP

* ✅ Realizar un ataque ARP Spoofing en entorno controlado

* ✅ Interceptar tráfico HTTP no cifrado

* ✅ Capturar credenciales de acceso en texto claro

* ✅ Entender por qué HTTPS es fundamental

## 🚀 Prueba de Concepto (PoC)

> [!warning] ⚠️ ADVERTENCIA LEGAL
>        Este ataque SOLO debe realizarse en entornos controlados de laboratorio con fines educativos. Realizar este ataque en redes sin autorización explícita es ILEGAL y puede conllevar consecuencias penales.
>         Solo en tu laboratorio. Solo con tus máquinas virtuales.

## 🖥️ Paso 1: Preparación del Entorno

### 📋 Requisitos

* 🐉 Máquina virtual con Kali Linux (atacante)

* 💻 Máquina virtual con Windows 7 (víctima)

* 🌐 Ambas máquinas en la misma red (modo puente o red interna)

### ✅ Encender ambas máquinas

Una vez encendidas, apunta las siguientes IPs:

| Elemento | Dirección IP | Tu Valor |
| --- | --- | --- |
| Kali Linux (Atacante) | Ejemplo: 192.168.10.100 | IP VM Kali Linux: _________________ |
| Windows 7 (Víctima) | Ejemplo: 192.168.10.202 | IP VM Windows 7: _________________ |
| Puerta de enlace (Router) | Ejemplo: 192.168.10.1 | IP Puerta de enlace Predeterminada: _________________ |

#### 🔍 Obtener la IP y la MAC en Kali Linux:

```bash
ip a
```

#### 🔍 Obtener la IP en Windows 7:

```cmd
ipconfig
```

## 🔗 Paso 2: Verificar Conectividad

Hacemos un PING desde la máquina de Linux a la de Windows 7 para asegurar que hay conectividad.

#### 🐉 En Kali Linux:

```bash
ping -c 4 192.168.10.202
```

**Nota:** Reemplaza `192.168.10.202` con la IP real de tu Windows 7.

Llegue o no el ping a Windows 7 la arp debería actualizarse en Windows 7.

## 📊 Paso 3: Examinar la Tabla ARP en Windows 7 (ANTES del Ataque)

Ahora vamos a mirar la tabla ARP que está guardada en Windows 7 **ANTES** del ataque.

#### 💻 En Windows 7 (CMD):

```bash
arp -a
```

Observa la dirección física (MAC) de las IPs que hemos anotado anteriormente.

La máquina de Kali debería salir en el listado. Para asegurarnos, hicimos ping en el punto 2.

**En mi caso:**

> [!warning] 📝 Anota las direcciones MAC:
>
>          MAC del router (puerta de enlace): __________________
>          MAC de Kali Linux: __________________
>
>        Estas direcciones MAC deben ser  diferentes . Después del ataque, ambas serán  iguales  (la de Kali).

## 🛣️ Paso 4: Comprobar Ruta ANTES del Ataque (tracert)

Antes de realizar el ataque, vamos a comprobar la ruta que siguen los paquetes desde Windows 7 hasta internet.

#### 💻 En Windows 7 (CMD):

```bash
tracert 8.8.8.8
```

**Resultado esperado ANTES del ataque:**

Verás que el primer salto es directamente el router (puerta de enlace), por ejemplo:

```bash
Traza a la dirección 8.8.8.8 ...

  1    <1 ms    <1 ms    <1 ms  192.168.10.1
  2    10 ms    9 ms     8 ms   [IP del ISP]
  3    ...
  ...
```

> [!warning] 🎯 Observa:  El primer salto es  192.168.10.1  (el router real)

## 🔧 Paso 5: Preparar el Entorno de Kali para el Ataque

### 📦 Instalar dsniff (si no está instalado)

En versiones más **nuevas** de Kali hay que instalar el paquete `dsniff`, que incluye la herramienta `arpspoof`.

#### 🐉 En Kali Linux:

```bash
sudo apt update
sudo apt install dsniff
```

### 👑 Cambiar a usuario root

La mayoría del proceso desde Kali necesitamos ser root, por lo que vamos a cambiarnos a usuario root.

```bash
sudo su
```

Ponemos la password y obtendremos el Shell `#` que indica que estamos como root.

## ☠️ Paso 6: Comenzar el Envenenamiento ARP

### 📋 Sintaxis del Ataque

La sintaxis para realizar este ataque es la siguiente:

```bash
arpspoof -i <dispositivo_de_red> -t <ip_victima> <ip_router>
```

#### 📋 Parámetros:

* `-i ` → Interfaz de red (eth0, wlan0, etc.)

* `-t ` → Dirección IP de la víctima (Windows 7)

* `` → Dirección IP del router (puerta de enlace)

### 🚀 Ejecutar el Ataque

Desde Kali sustituimos la sintaxis por nuestros valores:

**Por lo que desde Kali sustituimos la sintaxis por nuestros valores, en mi caso:**

#### 🐉 En Kali Linux (como root):

```bash
# arpspoof -i eth0 -t 192.168.10.202 192.168.10.1
```

**⚠️ Reemplaza:**

* `eth0` → Tu interfaz de red

* `192.168.10.202` → IP de Windows 7

* `192.168.10.1` → IP del router

Verás mensajes continuos del tipo:

```bash
aa:bb:cc:dd:ee:ff aa:bb:cc:dd:ee:ff 0806 42: arp reply ...
```

Esto indica que el envenenamiento ARP está activo. **NO cierres esta terminal.**

## 🔍 Paso 7: Verificar el Envenenamiento en Windows 7

Si vemos la tabla ARP de la víctima, deberían coincidir ambas MAC: la de Kali y la del router.

**En Windows:**

#### 💻 En Windows 7 (CMD):

```bash
arp -a
```

> [!warning] 🎯 Observa:
>
>          La MAC del router ahora es la MISMA que la MAC de Kali
>          Windows 7 cree que Kali ES el router
>          Todo el tráfico de Windows 7 va primero a Kali

### 🚫 Windows 7 NO tiene internet (todavía)

Sin embargo, aunque Kali está en medio, de momento si probamos internet desde Windows veremos que NO tiene.

#### 💻 En Windows 7 (CMD):

```bash
ping 8.8.8.8
```

El ping falla porque Kali está capturando el tráfico pero NO lo está reenviando al router real.

## 🔄 Paso 8: Habilitar IP Forwarding en Kali

Necesitamos desde Kali generar el **IP_FORWARD** para que la máquina salga a internet cuando nos haga una petición.

**En Kali abrimos un nuevo terminal**, el envenenamiento debe continuar. Y ponemos lo siguiente (hay que transformarse antes en root en este nuevo terminal `$ sudo su`):

> [!warning] ⚠️ Importante:  Abre un  NUEVO terminal  en Kali. El terminal del envenenamiento debe continuar ejecutándose.

#### 🐉 En Kali Linux (nuevo terminal como root):

```bash
# Convertirse en root primero
sudo su

# Habilitar IP forwarding
echo 1 > /proc/sys/net/ipv4/ip_forward
```

Ahora ya estamos en medio y la máquina de Windows puede navegar, pero **todo el tráfico pasa primero por nuestra máquina** por lo que podríamos interceptarlo.

## ✅ Paso 9: Verificar que el Ataque Funciona

Ahora vamos a comprobar que Windows 7 tiene internet y que estamos interceptando todo el tráfico.

### 📊 Comprobar tabla ARP (envenenada)

#### 💻 En Windows 7 (CMD):

```bash
arp -a
```

Verifica que la MAC del router y la MAC de Kali son idénticas (envenenamiento activo).

### 🌐 Comprobar conectividad a Internet

#### 💻 En Windows 7 (CMD):

```bash
ping 8.8.8.8
```

Ahora el ping debería funcionar correctamente. Windows 7 tiene internet, pero todo pasa por Kali.

### 🛣️ Comprobar Ruta DESPUÉS del Ataque (tracert)

#### 💻 En Windows 7 (CMD):

```bash
tracert 8.8.8.8
```

**Resultado esperado DESPUÉS del ataque:**

Ahora verás que el **primer salto es la IP de Kali** (192.168.10.201), y el **segundo salto es el router** (192.168.10.1). Esto demuestra que el tráfico está pasando primero por la máquina atacante antes de llegar al router real.

```bash
Traza a la dirección 8.8.8.8 ...

  1    <1 ms    <1 ms    <1 ms  192.168.10.201  (Kali - Atacante)
  2    1 ms     1 ms     1 ms   192.168.10.1    (Router real)
  3    10 ms    9 ms     8 ms   [IP del ISP]
  4    ...
  ...
```

> [!warning] 🎯 Diferencia clave:
>     ANTES del ataque:  Windows → Router (192.168.10.1) → Internet
>     DESPUÉS del ataque:  Windows → Kali (192.168.10.100) → Router (192.168.10.1) → Internet
>    El tracert muestra claramente que ahora Kali está en medio del camino interceptando todo el tráfico.

## 🦈 Paso 10: Capturar el Tráfico con Wireshark

### 🚀 Abrir Wireshark en Kali

Abrimos desde Kali el software Wireshark y hacemos doble clic en el dispositivo de red en el que tenemos que capturar, en mi caso es `eth0`.

Empezará a capturar el tráfico de toda la red que pasa por nuestra máquina.

### 🌐 Navegar desde Windows 7

Vamos a navegar a diversos lugares no seguros e iniciar sesión en correo no seguro para comprobar si se captura.

#### 💻 Desde Windows 7 (navegador):

* Navega por la web http://www.almagro.es/

* Ahora entra en la web de correo http://webmail.cursosmfr.es

            Usuario: ejercicioarp@cursosmfr.es

* Password: XXXXXXXXXX

> [!warning] 🔑 ¿Por qué estas webs?
>
>          ✅ Usan  HTTP  (no cifrado)
>          ✅ Transmiten credenciales en  texto claro
>          ✅ Podemos capturar usuario y contraseña fácilmente
>
>        En webs con  HTTPS  el tráfico está cifrado y NO podríamos ver las contraseñas.

### 🛑 Detener la Captura

Para la captura de tráfico de Wireshark pulsando el botón rojo de stop.

## 🔍 Paso 11: Analizar el Tráfico Capturado

### 🎯 Buscar las Credenciales

Aplicamos el filtro `http contains "login"` en la barra de filtros de Wireshark para buscar peticiones de login y veremos si ha capturado la password.

```bash
http contains "login"
```

#### 🔍 Pasos para encontrar las credenciales:

* Busca paquetes HTTP POST (método de envío de formularios)

* Click derecho en un paquete → Follow → HTTP Stream

* Verás el contenido completo de la petición HTTP

* Busca campos como `user=`, `password=`, `email=`, etc.

> [!warning] ✅ Deberías ver algo como:
>
>
> ```bash
POST /login HTTP/1.1
Host: webmail.cursosmfr.es
Content-Type: application/x-www-form-urlencoded

user=ejercicioarp@cursosmfr.es&password=XXXXXXXXXX
```
>
>
>
>         ¡Credenciales capturadas en texto claro!  🎯

## 📊 Comparativa ANTES vs DESPUÉS del Ataque

| Aspecto | ANTES del Ataque | DESPUÉS del Ataque |
| --- | --- | --- |
| Tabla ARP | Router: MAC realKali: MAC diferente | Router: MAC de KaliKali: MAC de Kali (iguales) |
| Tracert primer salto | 192.168.10.1 (router real) | 192.168.10.1 (pero MAC de Kali) |
| Tráfico | Windows → Router directo | Windows → Kali → Router |
| Privacidad | Tráfico privado | Kali ve TODO el tráfico HTTP |
| Víctima detecta ataque | N/A | NO (tiene internet normal) |

## 🛡️ Cómo Protegerse del ARP Spoofing

### 🔐 Medidas de Protección

| Medida | Descripción | Efectividad |
| --- | --- | --- |
| Usar HTTPS | Todo el tráfico va cifrado, incluso si es interceptado | ✅ Alta |
| ARP estáticas | Configurar entradas ARP fijas en la tabla | ✅ Alta (tedioso) |
| VPN | Cifrar todo el tráfico mediante túnel VPN | ✅ Muy Alta |
| Switches con DAI | Dynamic ARP Inspection en switches gestionados | ✅ Alta |
| Herramientas anti-ARP | Software que detecta cambios sospechosos en tabla ARP | ⚠️ Media |
| Segmentación de red | VLANs para separar tráfico sensible | ✅ Alta |

## 📚 Conclusiones

### 🎓 Lo que hemos aprendido:

* ✅ El protocolo ARP es fundamental pero vulnerable

* ✅ ARP Spoofing permite interceptar tráfico en redes locales

* ✅ El tráfico HTTP (no cifrado) es completamente visible

* ✅ Las credenciales en texto claro pueden ser capturadas

* ✅ HTTPS protege incluso en caso de MITM

* ✅ La víctima NO detecta el ataque (tiene internet normal)

* ✅ El tracert muestra que el tráfico pasa por Kali antes del router real

### ⚠️ Responsabilidad Ética:

Este conocimiento debe usarse ÚNICAMENTE para:

* 🎓 Educación y formación en seguridad

* 🔐 Auditorías de seguridad autorizadas

* 🛡️ Proteger redes propias

* 📖 Concienciar sobre riesgos de seguridad

**NUNCA para actividades ilegales o no autorizadas.**

## 🧹 Limpieza Post-Práctica

### 🛑 Detener el Ataque

#### 🐉 En Kali Linux:

* Detener arpspoof: `Ctrl + C` en el terminal de arpspoof

* Deshabilitar IP forwarding:

```bash
echo 0 > /proc/sys/net/ipv4/ip_forward
```

#### 💻 En Windows 7:

Limpiar la tabla ARP:

```bash
arp -d
```

O simplemente reiniciar la máquina virtual.

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🏠 [[00 - INDICE|Inicio del Vault]]

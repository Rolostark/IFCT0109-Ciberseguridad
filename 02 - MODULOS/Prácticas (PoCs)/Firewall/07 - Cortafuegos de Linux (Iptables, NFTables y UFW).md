---
title: "🛡️ Práctica: Filtrado de Paquetes en Linux con Iptables, NFTables y UFW"
aliases:
  - Filtrado de Paquetes en Linux con Iptables, NFTables y UFW
  - PoC iptables
tags:
  - ifct0109/pocs
  - firewall
  - iptables
  - nftables
  - ufw
  - linux-firewall
---

# 🛡️ Práctica: Filtrado de Paquetes en Linux con Iptables, NFTables y UFW

> [!abstract] Resumen del Laboratorio
> Módulo de Seguridad Perimetral y Cortafuegos del Certificado IFCT0109: topologías por zonas (ROJA, VERDE, AZUL, NARANJA), segmentación perimetral, políticas de filtrado, túneles cifrados VPN, sistemas IDS/IPS y filtrado a nivel de host.

---

# 🐧 Firewall en Linux - UFW e IPTables

## 📖 ENUNCIADO (DESCRIPCIÓN)

#### 🖥️ Entorno de la Práctica:

| Parámetro | Valor |
| --- | --- |
| Máquina | Debian12_dwm_20230710 |
| RAM | 2 GB (reducir si es necesario) |
| Credenciales | tecnico / SegInf |
| Red | Hacking |
| Abrir navegador | `ALT + W` |

## 📚 Conceptos Previos

### 🔥 ¿Qué es IPTables?

**IPTables** es un software que se encarga de actuar como un firewall o cortafuegos en nuestra red. Es una parte muy importante de los sistemas operativos Linux, y está específicamente diseñado para permitir el acceso autorizado y bloquear el acceso no autorizado.

### 🛡️ ¿Qué es UFW?

Las siglas **UFW** significan **"Uncomplicated Firewall"** y hacen referencia a una aplicación que tiene como objetivo establecer reglas en **iptables**, el firewall nativo de Linux. Puesto que iptables tiene una sintaxis relativamente compleja, UFW simplifica su uso.

#### 💡 Relación entre UFW e IPTables:

| Herramienta | Nivel | Complejidad | Uso |
| --- | --- | --- | --- |
| IPTables | Bajo nivel (kernel) | Alta | Control total y granular |
| UFW | Alto nivel (frontend) | Baja | Gestión sencilla del día a día |

UFW traduce sus reglas a comandos IPTables internamente.

## ⚙️ Instalación y Configuración Inicial

### 📦 Instalación de UFW

Desde el terminal ejecutamos la siguiente línea:

```bash
sudo apt install ufw
```

Instala también una serie de paquetes entre los que se encuentra **iptables**, ya que como hemos comentado UFW al fin y al cabo es una forma más sencilla de configurar el firewall nativo de Linux IPTables.

### 📊 Comprobar Estado Inicial

Vamos a comprobar lo primero el estado de ambos firewalls:

#### 🐧 Comprobar Estado:

```bash
# Estado de UFW (aparecerá como inactivo)
sudo ufw status verbose

# Estado de IPTables (sin reglas definidas aún)
sudo iptables -L -n -v
```

### 🟢 Activar UFW

```bash
sudo ufw enable
sudo ufw status verbose
```

### 📝 Configuración por Defecto de UFW

En la ruta `/etc/default/ufw` podemos comprobar la configuración por defecto del firewall, entre ellas podemos comprobar si se encuentra activado IPv6.

Vamos a editar el archivo y ponemos IPv6 en **no**:

> [!warning] ⚠️ Importante:  En caso de realizar algún cambio en este archivo, luego debemos  reiniciar el firewall  mediante los siguientes comandos:
>
>
> ```bash
sudo ufw disable
sudo ufw enable
```

### 🔒 Políticas por Defecto

Comprobamos la configuración por defecto del firewall:

```bash
sudo ufw status verbose
```

Por defecto, el firewall UFW tiene reglas para **negar todas las conexiones entrantes** y **solo permite todas las conexiones salientes**.

Podríamos también definirlas explícitamente con los siguientes comandos:

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

## 📋 Gestión de Reglas en UFW

### 🔍 Listar Reglas

```bash
# Ver reglas con detalles
sudo ufw status verbose

# Ver reglas numeradas (útil para eliminar por número)
sudo ufw status numbered
```

### 📖 Sintaxis de UFW

#### 📋 Palabras Clave de UFW:

| Palabra Clave | Significado |
| --- | --- |
| `allow` | Permitir |
| `deny` | Denegar |
| `from` | Desde un origen |
| `to` | Hacia un destino |
| `in` | Entrada |
| `out` | Salida |
| `on eth0` | Dispositivo de red específico |
| `proto` | Protocolo (tcp, udp) |
| `port` | Puerto o puertos separados por comas |
| `app list` | Listado de nombres de aplicaciones |
| `any` | Cualquier origen o destino |
| `insert 1` | Crear una regla en una posición concreta |

## 🚀 Prueba de Concepto (PoC)

### 📥 Reglas de Entrada

#### ✅ Permitir Conexiones Entrantes:

**Permitir todas las conexiones al puerto 22 (SSH):**

```bash
sudo ufw allow 22
```

**Permitir SSH solo desde una subred concreta:**

```bash
sudo ufw allow from 10.0.0.0/24 to any port 22
```

**Permitir HTTP y HTTPS por separado:**

```bash
sudo ufw allow 80
sudo ufw allow 443
```

**Permitir HTTP y HTTPS en una sola línea:**

```bash
sudo ufw allow proto tcp from any to any port 80,443
```

**Permitir un rango de puertos:**

```bash
sudo ufw allow 6000:6003/tcp
sudo ufw allow 6000:6003/udp
```

#### 🚫 Bloquear Conexiones Entrantes:

**Bloquear una IP concreta:**

```bash
sudo ufw deny from 10.0.0.100
```

**Bloquear una red entera en una interfaz de red:**

```bash
sudo ufw deny in on eth0 from 10.0.0.0/24
```

### 📤 Reglas de Salida

#### 🚫 Bloquear Tráfico Saliente:

**Bloquear tráfico SMTP saliente (correo):**

```bash
sudo ufw deny out 25
```

**Bloquear toda la navegación (HTTP + HTTPS):**

```bash
sudo ufw deny out proto tcp from any to any port 80,443
```

**Bloquear solo navegación no segura (HTTP):**

```bash
sudo ufw deny out proto tcp from any to any port 80
```

## 🧪 Ejercicios Prácticos

### 🧹 Paso Previo: Borrar Todas las Reglas

```bash
sudo ufw status numbered
sudo ufw delete [número]   # Repetir para cada regla
```

### 🧪 Ejercicio 1: Bloquear HTTP y Navegar

Añade la siguiente regla:

```bash
sudo ufw deny out proto tcp from any to any port 80
```

Navega a **Google.es** y busca información sobre Almagro. Entra en la web almagro.es

> [!warning] 💡 Recuerda:  En la máquina Debian puedes abrir el navegador pulsando  ALT + W

### 🧪 Ejercicio 2: Bloquear También HTTPS

Crea ahora la siguiente regla adicional:

```bash
sudo ufw deny out proto tcp from any to any port 443
```

Intenta navegar por distintas webs. Comprueba las reglas activas:

```bash
sudo ufw status numbered
```

### 🧪 Ejercicio 3: Bloquear Todo el TCP y Permitir Solo HTTPS

Elimina las reglas anteriores y crea la siguiente:

```bash
sudo ufw deny out proto tcp from any to any
```

Ahora pon la siguiente regla de permiso:

```bash
sudo ufw allow out proto tcp from any to any port 443
```

#### 🔍 Observación:

Aún estando esta última regla añadida **no debería dejarnos navegar** por distintos sitios seguros.

**¿Por qué?** Porque la regla DENY está en posición superior a la regla ALLOW. Las reglas se aplican en orden: la primera que coincide gana.

### 🧪 Ejercicio 4: Prioridad de Reglas con insert

Elimina la última regla y agrégala en la **posición 1**:

```bash
sudo ufw insert 1 allow out proto tcp from any to any port 443
```

#### ✅ Resultado:

Ahora **sí nos debería dejar navegar** en navegación segura (HTTPS), ya que la regla ALLOW está en la posición 1, por encima de la regla DENY.

Este ejercicio demuestra que el **orden de las reglas es fundamental** en cualquier firewall.

### 🗑️ Eliminar Reglas

```bash
# Ver reglas numeradas
sudo ufw status numbered

# Eliminar una regla por su número
sudo ufw delete 12
```

## 🔄 Reset de UFW a Valores de Fábrica

Si queremos resetear por completo el cortafuegos eliminando todas las reglas:

```bash
sudo ufw reset
```

> [!warning] ⚠️ Atención:  Este comando elimina  todas las reglas  configuradas y desactiva UFW. Úsalo con precaución en entornos de producción.

## 📚 Conclusiones

### 🎓 Lo que hemos aprendido:

* ✅ Diferencia entre IPTables (bajo nivel) y UFW (alto nivel)

* ✅ Instalar y activar UFW en Debian/Ubuntu

* ✅ Configurar políticas por defecto (deny in, allow out)

* ✅ Crear reglas de entrada y salida

* ✅ Bloquear IPs, redes y puertos específicos

* ✅ Bloquear rangos de puertos

* ✅ El orden de las reglas es fundamental

* ✅ Insertar reglas en posiciones específicas con `insert`

* ✅ Eliminar reglas por número

* ✅ Resetear UFW a valores de fábrica

### ⚠️ Lección Clave - Orden de las Reglas:

Las reglas se procesan **de arriba a abajo**. La primera regla que coincide con el tráfico se aplica y el resto se ignoran.

| Posición | Regla | Resultado |
| --- | --- | --- |
| 1 (arriba) | DENY TCP port 443 | 🚫 HTTPS bloqueado aunque haya ALLOW abajo |
| 2 (abajo) | ALLOW TCP port 443 | ❌ Nunca se llega a esta regla |

**Solución:** Usar `sudo ufw insert 1 allow...` para poner la regla ALLOW antes que el DENY.

### 📋 Referencia Rápida de Comandos:

| Comando | Descripción |
| --- | --- |
| `sudo ufw enable` | Activar UFW |
| `sudo ufw disable` | Desactivar UFW |
| `sudo ufw status verbose` | Ver estado y reglas |
| `sudo ufw status numbered` | Ver reglas numeradas |
| `sudo ufw allow [puerto]` | Permitir puerto |
| `sudo ufw deny [puerto]` | Bloquear puerto |
| `sudo ufw delete [número]` | Eliminar regla por número |
| `sudo ufw insert 1 [regla]` | Insertar regla en posición 1 |
| `sudo ufw reset` | Resetear a valores de fábrica |
| `sudo iptables -L -n -v` | Ver reglas IPTables subyacentes |

### 🎯 Navegación:

← Práctica 06A: Firewall de Windows

← 🏠 Volver al Índice del Módulo

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🔒 [[02 - MODULOS/ISO 27002/03 - Robustecimiento de Sistemas y Redes|ISO 27002 — Robustecimiento y Segmentación de Redes]]

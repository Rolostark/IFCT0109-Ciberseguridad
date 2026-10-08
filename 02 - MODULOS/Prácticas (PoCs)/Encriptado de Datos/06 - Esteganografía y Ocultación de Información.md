---
title: "🔒 Práctica: Técnicas de Esteganografía y Extracción de Secretos en Medios"
aliases:
  - Técnicas de Esteganografía y Extracción de Secretos en Medios
  - PoC esteganografia
tags:
  - ifct0109/pocs
  - encriptado
  - esteganografia
  - steghide
  - analisis-forense
---

# 🔒 Práctica: Técnicas de Esteganografía y Extracción de Secretos en Medios

> [!abstract] Resumen del Laboratorio
> Módulo de Cifrado y Criptografía Aplicada del Certificado IFCT0109: implementación de algoritmos simétricos y asimétricos, cifrado en reposo, firmas digitales, gestión de claves y técnicas forenses de esteganografía.

---

# 🖼️ Esteganografía con OpenPuff

## 📖 ENUNCIADO (DESCRIPCIÓN)

### 🎭 ¿Qué es la Esteganografía?

La **esteganografía** es el arte y la ciencia de ocultar información dentro de otra información, de modo que el mensaje oculto pase desapercibido para observadores no autorizados. A diferencia de la criptografía, que se centra en el cifrado y descifrado de mensajes, la esteganografía se enfoca en ocultar la existencia misma del mensaje.

#### 🔐 Diferencia entre Criptografía y Esteganografía:

| Aspecto | Criptografía | Esteganografía |
| --- | --- | --- |
| Objetivo | Ocultar el CONTENIDO del mensaje | Ocultar la EXISTENCIA del mensaje |
| Visibilidad | El mensaje cifrado es visible | El mensaje está completamente oculto |
| Sospecha | Se sabe que hay información cifrada | Nadie sospecha que hay información oculta |
| Ejemplo | Texto cifrado: "xK8!mQ#2pL" | Imagen normal con datos ocultos en píxeles |

La esteganografía puede ser utilizada con diversos fines, desde el espionaje y la seguridad hasta la protección de derechos de autor y la privacidad. Sin embargo, también puede ser utilizada de manera maliciosa, por ejemplo, para ocultar malware en archivos o para facilitar actividades ilegales.

> [!warning] ⚠️ Importante:  La esteganografía no proporciona seguridad por sí sola. Si bien oculta la existencia del mensaje, no garantiza la protección del contenido oculto. Para una protección completa, a menudo se combina la esteganografía con técnicas criptográficas para asegurar que solo las personas autorizadas puedan acceder al contenido oculto.

## 🛠️ Herramientas de Esteganografía

Algunas herramientas de esteganografía son:

* OpenStego: Es una herramienta de esteganografía de código abierto que te permite ocultar mensajes en archivos de imagen y audio. Proporciona varias técnicas de ocultamiento y es compatible con una amplia gama de formatos de archivo.

* Steghide: Es una herramienta de línea de comandos para ocultar datos en archivos de imagen y audio. Ofrece encriptación y compresión opcional de los datos ocultos, lo que proporciona una capa adicional de seguridad.

* OutGuess: Es una herramienta de esteganografía que permite ocultar información en archivos de imagen. Utiliza un algoritmo de compresión para asegurar que los cambios en los datos ocultos sean mínimos y no sean perceptibles visualmente.

* QuickStego: Es una herramienta de esteganografía sencilla y fácil de usar que te permite ocultar mensajes en imágenes digitales. Solo necesitas seleccionar una imagen de base y un archivo de mensaje, y QuickStego se encargará del resto.

* SteganoGUI: Es una interfaz gráfica de usuario para varias herramientas de esteganografía, como OpenStego, OutGuess y JSteg. Proporciona una forma fácil de utilizar estas herramientas sin tener que usar la línea de comando.

### 🎯 ¿Por qué usaremos OpenPuff?

Nosotros usaremos **OpenPuff** que utiliza un enfoque llamado **"Triple Esteganografía"**, que implica ocultar un archivo dentro de otro archivo y luego ocultar ese archivo resultante en otro archivo adicional. Esto proporciona una capa adicional de seguridad y dificulta la detección del mensaje oculto.

#### ✅ Características de OpenPuff:

* 🔒 Encriptación de datos ocultos con contraseña

* 🧩 Esteganografía Fragmentada (dividir mensaje en varios archivos)

* 🔑 Hasta 3 contraseñas para mayor seguridad

* 📁 Soporta múltiples formatos (imágenes, audio, video, PDF)

* 🎭 Triple Esteganografía (ocultamiento en capas)

## 🔍 Técnicas para Detectar Esteganografía

Existen algunas técnicas y herramientas que se pueden utilizar para detectar si un archivo contiene información oculta mediante esteganografía. Aquí hay algunas:

#### 🔎 Métodos de Detección:

* Análisis visual: Un enfoque básico pero útil es examinar visualmente el archivo en busca de anomalías o patrones sospechosos. Esto implica buscar cambios notables en la calidad de la imagen, la presencia de patrones extraños o áreas con colores inusuales. Sin embargo, este método puede no ser efectivo si se utilizan técnicas avanzadas de esteganografía.

* Herramientas de análisis: Existen herramientas especializadas que pueden ayudar a detectar la presencia de información oculta en archivos. Algunas herramientas populares incluyen StegDetect, Ghiro y OutGuess-NG. Estas herramientas analizan los archivos en busca de firmas, patrones o características asociadas con las técnicas de esteganografía conocidas. Sin embargo, es importante tener en cuenta que estas herramientas no son infalibles y pueden no detectar métodos de esteganografía más sofisticados o recientes.

* Análisis de metadatos: Los archivos digitales a menudo contienen metadatos, como información sobre la cámara o el software utilizado para crear la imagen. Algunas técnicas de esteganografía pueden alterar los metadatos de los archivos. Por lo tanto, verificar y comparar los metadatos de un archivo original con los metadatos del archivo sospechoso puede revelar discrepancias y posibles indicios de información oculta.

* Análisis de frecuencias: Algunas técnicas de esteganografía introducen cambios en las frecuencias de la imagen o el sonido para ocultar datos. El análisis de las frecuencias de la imagen o el audio mediante transformadas como la Transformada de Fourier puede revelar diferencias notables en las características de la señal original y la señal sospechosa.

* Análisis estadístico: Las imágenes y los archivos de audio tienen ciertas características estadísticas que se pueden utilizar para detectar posibles alteraciones. Por ejemplo, el análisis de la distribución de colores en una imagen puede revelar discrepancias en las regiones modificadas. También se pueden aplicar pruebas estadísticas para detectar patrones no aleatorios en los datos.

## 📦 Preparación del Entorno - OpenPuff

Como hemos comentado anteriormente vamos a usar **OpenPuff** para demostrar la esteganografía.

Primero descargamos el archivo **Esteganografía** de la zona de recursos y lo descomprimimos.

Dentro encontraremos los siguientes archivos y carpetas:

#### 📂 Contenido del Paquete:

* 📁 Exiftool – Programa para analizar metadatos

* 📁 ExifDataView – Programa para analizar metadatos

* 📁 Openpuff – Programa para realizar la esteganografía

* 🖼️ Ballon – Una imagen donde ocultar información

* 📄 Confidencial – Información que usaremos para ocultarla

* 📄 LoremIpsum – Información que usaremos para ocultarla

* 📁 Ejemplos – Ejemplos de archivos limpios donde ocultar información

* 📁 Pruebas – Una carpeta para guardar las pruebas que hagamos

* 📁 Stego – Archivos de ejemplo con esteganografía en su interior

## 🔒 Ocultar Información en un Documento

### 🚀 Paso 1: Abrir OpenPuff en Modo "Hide"

Abrimos OpenPuff y pulsamos en el apartado de **Steganography** en **Hide**

La siguiente ventana vamos a ver que se divide en 4 diferentes zonas.

### 🔑 Zona 1: Configurar Contraseñas

**En el primer paso** tenemos la zona 1 donde ponemos la o las claves que queremos utilizar para encriptar la información dentro del archivo.

#### 🔐 Configuración de Contraseñas:

* Podemos usar hasta tres contraseñas para dar más seguridad a lo que ocultemos

* Marcando enable (B) o (C) se habilitan contraseñas adicionales

* La contraseña que usemos debe ser de al menos 8 caracteres

* A más contraseñas, más seguridad (pero más difícil de recordar)

### 📄 Zona 2: Seleccionar Archivo a Ocultar

**El segundo paso** en la zona 2, elegimos el archivo de información que queremos ocultar.

Pulsamos en el botón **[...]** y navegamos hasta el archivo que queremos ocultar (por ejemplo, `confidencial.txt`).

### 🖼️ Zona 3: Seleccionar Carriers (Archivos Contenedores)

**El tercer paso** es elegir el o los carriers donde queremos ocultar la información.

Pulsamos **Add**, entramos en la carpeta **ejemplos** y seleccionamos varios carriers (los que comienzan con la palabra **clean**).

#### 📊 ¿Cuántos Carriers Necesito?

En función del archivo que queramos ocultar, necesitamos más o menos carriers. Hay que añadir hasta que la siguiente zona aparezca en **verde**:

### ⚙️ Zona 4: Bit Selection Options

**En el cuarto paso** en la zona 4 elegimos el **bit selection options**. Este parámetro determina cómo se seleccionan los bits del archivo original y cómo se modifican para incorporar los datos ocultos.

> [!warning] 🎯 Importante:  Permite elegir la estrategia que mejor se adapte a tus necesidades, equilibrando la seguridad y la calidad del archivo resultante. El uso de opciones de selección de bits más seguras puede generar cambios más notorios en los archivos, lo que podría aumentar las posibilidades de detección.

### ✅ Finalizar: Ocultar Datos

Finalmente, pulsamos en **"Hide Data!"** y guardamos el resultado en la carpeta **pruebas**.

Cuando termina, en mi caso sólo ha necesitado una imagen para ocultar la información.

Tendremos la imagen con la información oculta dentro.

Tenemos el software **exiftoolgui** para comparar el resultado de ambos archivos.

### 📄 Ejemplo 2: Ocultar un PDF

Vamos a hacer otra prueba añadiendo ahora el archivo PDF.

En este caso se han necesitado **3 carriers**, es importante el orden para luego poder extraer la información.

> [!warning] ⚠️ Muy Importante:  El  ORDEN de los carriers  es crucial. Debes recordar el orden exacto en que los añadiste para poder extraer la información correctamente después.

## 🔓 Mostrar Información de un Documento

Abrimos OpenPuff y elegimos **Unhide**.

### 🔑 Paso 1: Introducir Contraseña(s)

Ponemos la **Clave en (A)**, añadimos el o los carriers, pulsamos **Unhide** y obtendremos el archivo `confidencial.txt`.

### 📄 Paso 2: Extraer PDF (Múltiples Carriers)

Lo mismo si añadimos los carriers que contienen el PDF:

> [!warning] ⚠️ Orden Importante:  Al ser varios carriers tenemos que añadirlos en el  mismo orden  en el que se usaron para ocultar la información.

## 🧹 Limpiando Información de un Carrier

Podemos limpiar el contenido de un Carrier usando el propio OpenPuff, lo abrimos y pulsamos en **CleanUp**.

Añadimos el archivo Carrier y pulsamos **Clean Up**.

Limpiará el documento y ya no podremos extraer información de él.

#### 🎯 ¿Para qué sirve CleanUp?

* ✅ Eliminar información oculta de forma segura

* ✅ Reutilizar un carrier limpio

* ✅ Borrar rastros de esteganografía

## 💧 Añadiendo Marcas de Agua con OpenPuff

OpenPuff también se puede usar para añadir algún tipo de firma o marca al documento.

Abrimos OpenPuff y seleccionamos **SetMark**.

Vamos a añadir una marca al archivo **ballon** y pulsamos **Set Mark**.

Elegimos el destino del archivo, vamos a elegir la carpeta **pruebas**.

### 🔍 Verificar Marca de Agua

Se genera una marca invisible en el documento, ni siquiera en los metadatos la podemos ver.

La forma de ver esa marca es con el propio OpenPuff en la opción **Check Mark**.

#### 💡 Usos de las Marcas de Agua:

* 🔐 Protección de derechos de autor

* ✍️ Firma digital invisible

* 📅 Rastreo de versiones de documentos

* 👤 Identificación de origen/propietario

También se puede eliminar la marca con el mismo procedimiento que para eliminar la información de un Carrier (usando **CleanUp**).

## 🔍 Recursos para Detectar Esteganografía

### 🌐 Herramientas Online

#### 🔗 Recursos Web:

**Información sobre comprobación de esteganografía:**

https://stegonline.georgeom.net/checklist

**Analizar archivos en línea:**

https://stegonline.georgeom.net/upload

**Otros sitios para analizar imágenes:**

* https://fotoforensics.com/

* https://29a.ch/photo-forensics/#forensic-magnifier

### 🛠️ Técnicas de Análisis

Algunas opciones son:

#### 1️⃣ Comando type o file en Linux

Comprobar que el tipo de archivo.

**En Windows:**

```bash
D:\Esteganografia\stego> type archivo
```

**En Linux:**

```bash
$ file archivo
```

#### 2️⃣ Strings

Usando el comando **strings** para ver posibles cadenas de texto en el archivo.

```bash
D:\Esteganografia\stego> strings strings-stego.jpg
```

#### 3️⃣ Metadatos

Usando herramientas para comprobar los metadatos, en busca de información que pueda ayudar.

#### 📷 Ejemplo: Imagen "Juegos de Guerra"

En la imagen **Juegos de guerra** el thumbnail es diferente a la imagen original. Con **exiftool** vemos la siguiente información en la pestaña Exif:

La extraemos con la línea de comandos de exiftool:

Se genera una imagen con un mensaje.

Podemos usar la web https://29a.ch/photo-forensics/#forensic-magnifier para verlo directamente.

## 📚 Conclusiones

### 🎓 Lo que hemos aprendido:

* ✅ La esteganografía oculta la existencia del mensaje, no solo su contenido

* ✅ OpenPuff permite Triple Esteganografía con hasta 3 contraseñas

* ✅ Los carriers son archivos contenedores (imágenes, audio, video, PDF)

* ✅ El orden de los carriers es crucial para extraer información

* ✅ Las marcas de agua invisibles sirven para proteger derechos de autor

* ✅ Existen múltiples técnicas de detección (metadatos, análisis estadístico, etc.)

* ✅ La combinación de esteganografía + criptografía da máxima seguridad

### 🎯 Casos de Uso:

| Uso Legítimo | Uso Malicioso |
| --- | --- |
| 🔐 Comunicaciones secretas autorizadas | 💀 Ocultar malware en imágenes |
| ©️ Marcas de agua para derechos de autor | 🕵️ Espionaje y robo de información |
| 🛡️ Protección de datos sensibles | 📧 Filtración de datos corporativos |
| 🎨 Arte digital con mensajes ocultos | 🌐 Comunicación de grupos criminales |

### ⚠️ Consideraciones Éticas y Legales:

* ⚠️ Usar solo para fines legítimos y éticos

* ⚠️ En algunos países hay restricciones legales

* ⚠️ No usar para ocultar malware o actividades ilegales

* ⚠️ Respetar las leyes de privacidad y protección de datos

### 🔑 Consejos de Seguridad:

* 🔐 Combina esteganografía CON criptografía (doble capa)

* 🔑 Usa contraseñas fuertes y múltiples (hasta 3 en OpenPuff)

* 📝 Recuerda el ORDEN de los carriers

* 🧹 Usa CleanUp para borrar rastros

* 🔍 Verifica archivos sospechosos con herramientas de análisis

---

## 🔗 Enlaces y Referencias
* 🗂️ [[02 - MODULOS/05 - Prácticas (PoCs)|Catálogo General de Prácticas (PoCs)]]
* 🛡️ [[02 - MODULOS/02 - ISO 27002|Controles Criptográficos ISO 27002]]

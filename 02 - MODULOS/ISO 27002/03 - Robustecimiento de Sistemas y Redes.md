---
title: "ISO 27002:2022 — Robustecimiento de Sistemas y Redes"
aliases:
  - Robustecimiento
  - Hardening
  - Seguridad en Redes
  - ISO 27002 Robustecimiento
  - robustecimiento
tags:
  - iso27002
  - iso27001
  - hardening
  - redes
  - dlp
  - backups
  - criptografia
  - ifct0109
---

# 🛡️ Robustecimiento de Sistemas y Redes

> [!abstract] Cláusula 8 de ISO/IEC 27002:2022
> Esta sección recoge los **15 controles tecnológicos** dedicados a la protección frente a malware, gestión de vulnerabilidades, configuración segura (*hardening*), prevención de fugas de datos (DLP), copias de seguridad, redundancia, seguridad y segregación de redes, filtrado web y criptografía.
> 
> 🔗 **Navegación:** [[02 - MODULOS/02 - ISO 27002|⬅️ Volver al Índice General ISO 27002]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## 📑 Índice de Controles

* [[#Control 8.7 — Controles contra el código malicioso|Control 8.7 — Controles contra el código malicioso]]
* [[#Control 8.8 — Gestión de vulnerabilidades técnicas|Control 8.8 — Gestión de vulnerabilidades técnicas]]
* [[#Control 8.9 — Gestión de la configuración|Control 8.9 — Gestión de la configuración]]
* [[#Control 8.10 — Eliminación de la información|Control 8.10 — Eliminación de la información]]
* [[#Control 8.11 — Enmascaramiento de datos|Control 8.11 — Enmascaramiento de datos]]
* [[#Control 8.12 — Prevención de fugas de datos|Control 8.12 — Prevención de fugas de datos]]
* [[#Control 8.13 — Copias de seguridad de la información|Control 8.13 — Copias de seguridad de la información]]
* [[#Control 8.14 — Redundancia de los recursos de tratamiento de la información|Control 8.14 — Redundancia de los recursos de tratamiento de la información]]
* [[#Control 8.16 — Seguimiento de actividades|Control 8.16 — Seguimiento de actividades]]
* [[#Control 8.19 — Instalación de software en sistemas de producción|Control 8.19 — Instalación de software en sistemas de producción]]
* [[#Control 8.20 — Seguridad de redes|Control 8.20 — Seguridad de redes]]
* [[#Control 8.21 — Seguridad de los servicios de red|Control 8.21 — Seguridad de los servicios de red]]
* [[#Control 8.22 — Segregación en redes|Control 8.22 — Segregación en redes]]
* [[#Control 8.23 — Filtrado de webs|Control 8.23 — Filtrado de webs]]
* [[#Control 8.24 — Uso de la criptografía|Control 8.24 — Uso de la criptografía]]

---

### Control 8.7 — Controles contra el código malicioso ^c87

> [!info] Objetivo
> Garantizar que la información y otros activos asociados estén protegidos contra el código malicioso, implementando una protección técnica respaldada por una adecuada concienciación de los usuarios.

#### Protección técnica
1. La protección **no debe basarse únicamente en el software de detección y reparación**, sino en una combinación de controles técnicos, organizativos y de concienciación.
2. Deben implementarse **listas de aplicaciones permitidas** (*whitelisting*) que impidan o detecten el uso de software no autorizado, así como **listas de bloqueo** para sitios web maliciosos.
3. El software de detección debe instalarse, actualizarse y ejecutarse regularmente, realizando **escaneos de datos recibidos** por red, correo, mensajería, descargas y páginas web antes de su uso.
4. Debe aplicarse un enfoque de **defensa en profundidad**, ubicando herramientas en múltiples puntos: puertas de enlace, servidores de correo y dispositivos de usuario final.
5. Debe realizarse una **validación automatizada periódica** del software y contenido de los sistemas, especialmente en los que soportan procesos críticos.

#### Gestión de excepciones y continuidad
1. Debe existir un **proceso formal para autorizar la desactivación temporal** de medidas contra malware, con aprobación, justificación documentada y fecha de revisión.
2. Deben prepararse **planes de continuidad** para recuperarse de ataques, incluyendo copias de seguridad en línea y fuera de línea.
3. Deben **aislarse los entornos** donde un ataque pueda tener consecuencias catastróficas.

#### Concienciación y formación
1. Todos los usuarios deben recibir **formación** sobre cómo identificar y gestionar correos, archivos o aplicaciones potencialmente infectados.
2. Deben establecerse procedimientos para **recopilar información sobre nuevos tipos de malware**, verificando que procede de fuentes cualificadas.
3. Deben definirse **procedimientos y responsabilidades claros** para la protección, notificación y recuperación en caso de ataques.

---

### Control 8.8 — Gestión de vulnerabilidades técnicas ^c88

> [!info] Objetivo
> Evitar la explotación de vulnerabilidades técnicas, obteniendo información sobre las vulnerabilidades de los sistemas, evaluando la exposición de la organización y adoptando las medidas adecuadas.

#### Identificación
1. Como requisito previo debe disponerse de un **inventario preciso de activos** que incluya proveedor, nombre, versión, estado de despliegue y responsables.
2. Deben definirse **roles y responsabilidades claros** para la gestión de vulnerabilidades.
3. Deben utilizarse **herramientas de exploración de vulnerabilidades** y realizarse **pruebas de penetración** planificadas por personal competente y autorizado.
4. Los proveedores deben garantizar contractualmente la **notificación y tratamiento** de las vulnerabilidades de sus productos.
5. Debe rastrearse el uso de **bibliotecas y código de terceros** en busca de vulnerabilidades.

#### Evaluación
1. Las vulnerabilidades identificadas deben **analizarse y verificarse** para determinar qué respuesta y reparación es necesaria, evaluando los riesgos asociados.

#### Adopción de medidas
1. Debe implementarse un **proceso de gestión de actualizaciones** que garantice la instalación de parches aprobados, probando y documentando cambios antes de aplicarlos.
2. Las actualizaciones deben obtenerse de **fuentes legítimas** y probarse antes de su instalación.
3. Deben abordarse primero los **sistemas de alto riesgo** y definirse un calendario de reacción.
4. Cuando no exista actualización disponible, deben considerarse **medidas alternativas**: deshabilitar servicios vulnerables, controles de acceso adicionales, parches virtuales o aumento de la supervisión.
5. Respecto a las **actualizaciones automáticas**, la organización debe decidir si las activa, ya que pueden interferir con operaciones en curso.

#### Otras consideraciones
1. Debe mantenerse un **registro de auditoría** de todos los pasos dados.
2. El proceso debe **alinearse con la gestión de incidentes**.
3. En servicios en la nube, la **responsabilidad debe estar claramente definida** en el contrato con el proveedor.

---

### Control 8.9 — Gestión de la configuración ^c89

> [!info] Objetivo
> Garantizar que el hardware, el software, los servicios y las redes funcionen correctamente con la configuración de seguridad requerida, evitando alteraciones por cambios no autorizados o incorrectos.

#### Plantillas estándar de configuración
1. Deben definirse **plantillas estándar de configuración segura**, apoyándose en directrices de proveedores u organizaciones de seguridad independientes (CIS Benchmarks).
2. Las plantillas deben **revisarse y actualizarse periódicamente** para hacer frente a nuevas amenazas y vulnerabilidades.
3. Aspectos a contemplar: **minimizar identidades con privilegios, deshabilitar servicios innecesarios, cambiar contraseñas predeterminadas**, configurar cierre de sesión automático y sincronizar relojes.

#### Administración de configuraciones
1. Debe mantenerse un **registro de todas las configuraciones y cambios**, almacenados de forma segura en bases de datos de configuraciones (CMDB).
2. Los registros deben incluir: propietario del activo, fecha del último cambio, versión de la plantilla y **relación con las configuraciones de otros activos**.
3. Todos los cambios deben seguir el **proceso formal de gestión de cambios**.

#### Monitorización de configuraciones
1. Las configuraciones deben **supervisarse continuamente**, revisando ajustes, seguridad de contraseñas y actividades realizadas.
2. Las configuraciones reales deben **compararse con las plantillas objetivo**, abordando cualquier desviación.
3. La **automatización** (infraestructura como código) es especialmente efectiva para gestionar configuraciones de seguridad.
4. Las plantillas de configuración son **información confidencial** y deben protegerse.

---

### Control 8.10 — Eliminación de la información ^c810

> [!info] Objetivo
> Evitar la exposición innecesaria de información sensible y cumplir con los requisitos legales, eliminando la información almacenada cuando ya no sea necesaria.

#### Principio general
1. La información sensible **no debe conservarse más tiempo del necesario**, reduciendo el riesgo de divulgación. Debe seguirse la política de conservación y la legislación aplicable.
2. Debe guardarse un **registro de evidencia** de los resultados de la eliminación. Si se recurre a proveedores externos, debe solicitárseles prueba o certificado de destrucción.

#### Métodos de borrado
1. Debe utilizarse **software de borrado seguro aprobado** que garantice que la información no pueda recuperarse con herramientas forenses.
2. El método debe ser **adecuado al tipo de soporte**: desmagnetización para discos magnéticos, restauración de fábrica previo cifrado para smartphones, destrucción física cuando la sensibilidad lo requiera.
3. Deben eliminarse también las **versiones obsoletas, copias y archivos temporales**.
4. Los proveedores de eliminación deben estar **autorizados y certificados**.
5. Estos procesos deben **automatizarse** siempre que sea posible.

#### Terceros y servicios en la nube
1. Deben incluirse **cláusulas específicas sobre eliminación** en los acuerdos con terceros.
2. En entornos de nube, debe verificarse que el **método de supresión del proveedor es aceptable**.

#### Devolución de equipos
1. Al devolver equipos a proveedores, debe **protegerse la información retirando dispositivos de almacenamiento y memoria** antes de que salgan de las instalaciones.

---

### Control 8.11 — Enmascaramiento de datos ^c811

> [!info] Objetivo
> Limitar la exposición de datos sensibles, incluidos los datos de carácter personal, cumpliendo los requisitos legales aplicables.

#### Técnicas principales
1. **Anonimización:** altera de forma irreversible los datos, de manera que el titular ya no puede ser identificado ni directa ni indirectamente.
2. **Seudonimización:** sustituye la información identificativa por un alias, pero permite la reidentificación si se conoce el algoritmo. La información adicional debe mantenerse separada y protegida.
3. Otras técnicas: **cifrado, anulación de caracteres, sustitución de valores, uso de diferentes números y fechas, sustitución por hash** (siempre con función "salt" para evitar ataques de enumeración y tablas rainbow).

#### Consideraciones en la aplicación
1. Debe aplicarse el **principio de mínimo acceso**, diseñando consultas y máscaras para mostrar solo los datos necesarios.
2. En determinados casos pueden aplicarse mecanismos de **ofuscación de la ofuscación** (ocultando al usuario que ciertos datos están ofuscados, por ejemplo en entornos sanitarios).
3. La anonimización debe ser **completa**, considerando todos los campos, ya que la combinación de campos podría permitir identificación indirecta.

#### Tipos de enmascaramiento
* **Estático:** los datos se enmascaran en la base de datos original.
* **Dinámico:** se aplica en tiempo real mediante automatización y reglas sobre consultas.
* **Sobre la marcha:** los datos se enmascaran en la memoria de una aplicación en tránsito.

#### Otras consideraciones
1. Debe controlarse el **nivel de fortaleza** en función del uso de los datos.
2. Debe **prohibirse el cotejo** de datos tratados con otra información para identificar al titular.
3. Deben cumplirse requisitos legales como el **enmascaramiento obligatorio de información de tarjetas de pago** (PCI-DSS).
4. Debe evitarse el uso de **datos personales en identificadores de recursos** (nombres de archivo, URLs).

---

### Control 8.12 — Prevención de fugas de datos ^c812

> [!info] Objetivo
> Detectar e impedir la divulgación y extracción no autorizadas de información por parte de personas o sistemas.

#### Identificación y monitorización
1. Debe **identificarse y clasificarse** la información a proteger: información personal, modelos de precios, diseños de productos, etc.
2. Deben **monitorizarse los canales** de posible fuga: correo electrónico, transferencias de archivos, dispositivos móviles y de almacenamiento portátiles.

#### Herramientas DLP (Data Loss Prevention)
1. Las herramientas DLP deben **identificar, detectar y bloquear** la divulgación no autorizada (ej.: impedir que datos de una base se copien a una hoja de cálculo o se suban a servicios no autorizados).
2. Debe evaluarse si es necesario **restringir la capacidad de copiar, pegar o cargar datos** a servicios o dispositivos externos.
3. Cuando sea necesario exportar datos, el **propietario debe poder aprobar la exportación** y los usuarios responsabilizarse de sus acciones.

#### Medidas adicionales
1. **Capturas de pantalla y fotografías** deben abordarse mediante condiciones de uso, formación y auditorías.
2. Las **copias de seguridad con información sensible** deben protegerse con cifrado, control de acceso y protección física.
3. Deben considerarse técnicas activas de **engaño al adversario** como *honeypots* o sustitución de información auténtica por falsa (canary tokens).

#### Consideraciones legales
1. La prevención de fugas implica el **control de comunicaciones y actividades del personal**, lo que plantea consideraciones legales importantes (privacidad, protección de datos, legislación laboral).

---

### Control 8.13 — Copias de seguridad de la información ^c813

> [!info] Objetivo
> Permitir la recuperación tras la pérdida de datos o sistemas, manteniendo y probando periódicamente las copias de seguridad.

#### Política y planificación
1. Debe establecerse una **política específica** que refleje los requisitos de negocio como el **RPO** (*Recovery Point Objective*), la criticidad de la información y la frecuencia de las copias.
2. Deben elaborarse **registros precisos** y procedimientos de restauración documentados.
3. Para sistemas críticos, las copias deben cubrir **toda la información, aplicaciones y datos** necesarios para la recuperación completa (regla 3-2-1).

#### Almacenamiento y protección
1. Las copias deben almacenarse en una **ubicación remota y segura**, a distancia suficiente del sitio principal.
2. Deben contar con **protección física, medioambiental y cifrado** coherente con el emplazamiento principal.
3. Debe detectarse cualquier **pérdida involuntaria de datos antes de realizar la copia**, evitando respaldar datos corruptos.

#### Pruebas y verificación
1. Deben realizarse **pruebas periódicas** de los soportes de copia, probando la restauración en un sistema de prueba.
2. Las pruebas deben cotejarse con los **tiempos de recuperación (RTO)** requeridos por el plan de continuidad.

#### Servicios en la nube
1. Deben realizarse copias de seguridad en el entorno cloud, verificando que el servicio del proveedor **cumple con los requisitos** de la organización.

#### Retención y eliminación
1. Debe determinarse el **periodo de conservación** considerando requisitos legales.
2. Una vez expirado, debe procederse a la **eliminación segura** de acuerdo con el [[#Control 8.10 — Eliminación de la información|Control 8.10]].

---

### Control 8.14 — Redundancia de los recursos de tratamiento de la información ^c814

> [!info] Objetivo
> Garantizar el funcionamiento continuo de las instalaciones de tratamiento de la información, implementando la redundancia suficiente para satisfacer los requisitos de disponibilidad.

#### Diseño e implementación
1. La organización debe **identificar los requisitos de disponibilidad** y diseñar una arquitectura con redundancia adecuada.
2. La redundancia puede implementarse **duplicando total o parcialmente** las instalaciones, mediante componentes de repuesto o teniendo dos de todo (*high availability*).
3. Los componentes redundantes deben garantizar el **mismo nivel de seguridad** que los primarios.

#### Mecanismos de activación
1. Debe determinarse si los componentes redundantes se activan **siempre, automáticamente (failover) o manualmente** en caso de emergencia.
2. Deben existir mecanismos de **alerta de fallos** para ejecutar el procedimiento previsto.

#### Medidas específicas
1. Entre las medidas concretas: **dos o más proveedores de red (ISP)**, redes redundantes, dos centros de datos geográficamente separados, fuentes de alimentación redundantes (UPS/SAI) y componentes duplicados (CPUs, discos RAID, cortafuegos en clúster, enrutadores).
2. En entornos con múltiples instancias de software debe implementarse **balanceo de carga automático**.

#### Pruebas y consideraciones
1. Los sistemas redundantes deben **probarse periódicamente en modo producción**.
2. La redundancia no resuelve los **fallos internos de las aplicaciones**.
3. En la nube pública es posible tener **múltiples instancias activas con conmutación automática** entre distintas zonas de disponibilidad.

---

### Control 8.16 — Seguimiento de actividades ^c816

> [!info] Objetivo
> Detectar comportamientos anómalos y posibles incidentes de seguridad, monitorizando redes, sistemas y aplicaciones.

#### Alcance y línea base
1. El alcance debe determinarse en función de los **requisitos de seguridad, negocio y legislación**.
2. Debe establecerse una **línea base de comportamiento normal**: utilización en periodos normales/picos, horarios, ubicación y frecuencia de acceso.
3. Elementos a monitorizar: **tráfico de red**, acceso a sistemas críticos, registros de herramientas de seguridad (antivirus, IDS/IPS, firewalls, DLP) y uso de recursos.

#### Detección de anomalías
1. El sistema debe detectar: terminación no planificada de procesos, actividad de **código malicioso**, tráfico desde IPs maliciosas, características de ataques conocidos, comportamiento inusual y accesos no autorizados.

#### Herramientas y alertas
1. **Monitorización continua** con herramientas capaces de manejar grandes volúmenes y notificación en tiempo real.
2. Alertas basadas en **umbrales predefinidos**, ajustados para minimizar falsos positivos.
3. **Sistemas redundantes** y personal capacitado (SOC) para interpretar incidentes.

#### Respuesta y mejora continua
1. Procedimientos para **responder oportunamente** y para **gestionar falsos positivos**.
2. La monitorización puede mejorarse con **inteligencia de amenazas, machine learning, listas de bloqueo/permitidas** y pruebas de penetración.

> [!tip] UEBA (User and Entity Behavior Analytics)
> UEBA utiliza inteligencia artificial y *machine learning* para aprender el comportamiento habitual de cada usuario y entidad, construyendo una línea base individualizada. Detecta amenazas desconocidas y amenazas internas que no generarían alertas en sistemas tradicionales. Es especialmente útil combinado con SIEM.

---

### Control 8.19 — Instalación de software en sistemas de producción ^c819

> [!info] Objetivo
> Garantizar la integridad de los sistemas operacionales y evitar la explotación de vulnerabilidades técnicas, gestionando de forma segura la instalación de software en producción.

#### Gestión de la instalación
1. Las actualizaciones solo deben ser realizadas por **administradores capacitados con la autorización adecuada**.
2. Solo debe instalarse **código ejecutable aprobado**; queda prohibido el código en desarrollo o compiladores en máquinas de producción.
3. Todo software debe instalarse solo después de **pruebas extensas y exitosas**, con **estrategia de retroceso (*rollback*)** definida.
4. Debe mantenerse un **registro de auditoría** de todas las actualizaciones, archivando las versiones antiguas como contingencia.

#### Gestión de versiones y parches
1. Las decisiones de actualizar deben considerar **requisitos de negocio y seguridad**.
2. Los parches deben aplicarse cuando ayuden a **eliminar o reducir vulnerabilidades**.
3. El software debe mantenerse en **nivel de soporte activo**, evaluando riesgos de continuar sin soporte.
4. El software de **código abierto** debe mantenerse actualizado, considerando riesgos de proyectos abandonados.

#### Control sobre la instalación
1. Deben definirse **reglas estrictas** sobre qué software pueden instalar los usuarios (principio de mínimo privilegio).
2. Cuando los proveedores participen, el acceso debe concederse **solo cuando sea necesario** y sus actividades deben monitorizarse.

---

### Control 8.20 — Seguridad de redes ^c820

> [!info] Objetivo
> Garantizar la seguridad en el uso de los servicios de red y sus instalaciones, protegiendo redes y dispositivos frente a accesos no autorizados.

#### Gestión y documentación
1. Deben establecerse **responsabilidades y procedimientos claros** para la gestión de equipos y dispositivos de red, con documentación actualizada (diagramas de topología, configuraciones).
2. Debe separarse la **responsabilidad operativa de las redes** de las operaciones de sistemas TIC cuando sea posible.
3. Las actividades de gestión deben **coordinarse** para optimizar el servicio y garantizar coherencia de controles.

#### Protección y control del acceso
1. Deben implementarse controles para salvaguardar la **confidencialidad e integridad** de los datos en redes públicas, de terceros o inalámbricas.
2. Debe implementarse **autenticación de sistemas en la red** (802.1X) y restringirse conexiones mediante cortafuegos.
3. Los canales de **administración de red** deben segregarse del resto del tráfico (red de gestión *out-of-band*).

#### Medidas específicas
1. **Bastionado de dispositivos de red**: desactivar protocolos vulnerables (Telnet, HTTP) y eliminar servicios innecesarios.
2. En caso de ataque, poder **aislar temporalmente subredes críticas** ("puentes levadizos").
3. Aplicar controles a **redes virtualizadas** (SDN, SD-WAN).

#### Registro y monitorización
1. Implementar **registro y seguimiento** de actividades de red, coordinándose con los controles de monitorización y registro de eventos.

---

### Control 8.21 — Seguridad de los servicios de red ^c821

> [!info] Objetivo
> Garantizar la seguridad en el uso de los servicios de red, identificando, implementando y monitorizando los mecanismos de seguridad y niveles de servicio.

#### Gestión de proveedores
1. Las medidas de seguridad deben **identificarse e implementarse** para cada servicio, verificando que los proveedores las aplican correctamente.
2. Debe supervisarse la **capacidad del proveedor** periódicamente, acordando el derecho a auditoría.
3. Pueden usarse **certificados de terceros** proporcionados por los proveedores (ISO 27001, SOC 2).

#### Normas de uso
1. Deben formularse normas que definan: **redes y servicios accesibles, requisitos de autenticación, procedimientos de autorización** y seguimiento del uso.
2. Deben contemplarse **medios de acceso** (VPN, redes inalámbricas) y **atributos contextuales** (tiempo, ubicación).

#### Características de seguridad
1. Deben considerarse las **tecnologías aplicadas** (autenticación, cifrado, controles de conexión) y los parámetros técnicos requeridos.
2. Debe gestionarse el **almacenamiento en caché** según requisitos de rendimiento, disponibilidad y confidencialidad.
3. Deben implementarse procedimientos para **restringir el acceso** a servicios cuando sea necesario.

---

### Control 8.22 — Segregación en redes ^c822

> [!info] Objetivo
> Dividir la red en límites de seguridad y controlar el tráfico entre ellos en función de las necesidades de negocio.

#### Dominios de red
1. La red debe dividirse en **dominios separados**: por niveles de confianza/criticidad (pública, DMZ, estaciones de trabajo, servidores) o por unidades organizativas (RRHH, finanzas, desarrollo).
2. La segregación puede ser **física o lógica** (VLANs, VRF, microsegmentación), con perímetro bien definido.
3. Los criterios deben basarse en una **evaluación de los requisitos de seguridad** de cada dominio.

#### Control del tráfico
1. El acceso entre dominios debe controlarse mediante **puertas de enlace** (cortafuegos de nueva generación NGFW, enrutadores de filtrado ACL), alineándose con la política de control de acceso.

#### Redes inalámbricas
1. Requieren tratamiento especial por su **perímetro mal definido**.
2. En entornos sensibles, todo acceso inalámbrico debe tratarse como **conexión externa**.
3. La **WiFi de invitados** debe separarse de la del personal y tener al menos las mismas o mayores restricciones.

---

### Control 8.23 — Filtrado de webs ^c823

> [!info] Objetivo
> Proteger los sistemas frente a código malicioso y prevenir el acceso a recursos web no autorizados.

#### Tipos de sitios web a bloquear
1. La organización debe identificar qué sitios bloquear: sitios con **función de subida sin justificación**, sitios **maliciosos o de phishing**, servidores de comando y control (C2), sitios identificados mediante inteligencia de amenazas y sitios con **contenido ilegal**.

#### Implementación técnica
1. El filtrado puede implementarse **bloqueando direcciones IP o dominios**, mediante proxy web, navegadores o tecnologías antimalware/EDR y DNS seguro (DNS filtering).
2. Técnicas: **firmas, análisis heurístico, categorización URL, listas blancas y negras** y configuraciones personalizadas.

#### Política y formación
1. Debe establecerse una **política de uso seguro de recursos en línea**, manteniéndola actualizada.
2. Debe impartirse **formación** sobre uso seguro, incluyendo el proceso de excepción para recursos restringidos.
3. El personal debe saber **no ignorar los avisos del navegador** sobre sitios no seguros (certificados inválidos, advertencias de phishing).

---

### Control 8.24 — Uso de la criptografía ^c824

> [!info] Objetivo
> Garantizar un uso efectivo y adecuado de la criptografía para proteger la confidencialidad, autenticidad e integridad de la información.

#### Política de uso
1. Debe definirse una **política específica** que establezca principios generales, nivel de protección requerido y tipo, fortaleza y calidad de los algoritmos (longitudes de clave mínimas).
2. Deben definirse **roles y responsabilidades** claros para la implementación y la gestión de claves.
3. Debe tenerse en cuenta el **impacto del cifrado en otros controles** (detección de malware, inspección SSL/TLS por firewalls, filtrado de contenidos).
4. Deben considerarse las **regulaciones nacionales e internacionales** aplicables (normativa de exportación, ENS, GDPR).

#### Objetivos de seguridad
* **Confidencialidad:** cifrado de información sensible almacenada (en reposo, ej. AES-256) o transmitida (en tránsito, ej. TLS 1.3).
* **Integridad y autenticidad:** firmas digitales o códigos de autenticación de mensajes (HMAC).
* **No repudio:** pruebas criptográficas fehacientes de la ocurrencia de eventos o transacciones.
* **Autenticación:** verificación inequívoca de identidad de usuarios, servicios y entidades.

#### Gestión de claves criptográficas
1. El ciclo de vida completo debe gestionarse de forma segura: **generación, emisión, distribución, almacenamiento, cambio, revocación, recuperación, archivo y destrucción**.
2. Deben configurarse **fechas de activación y desactivación** para garantizar que las claves solo se usen durante el periodo autorizado.
3. Las **claves comprometidas** deben tratarse de forma inmediata (revocación y emisión de CRL/OCSP). Al abandonar la organización, las claves deben revocarse y archivarse.
4. Todas las claves deben estar **protegidas contra modificación, pérdida y uso no autorizado** (almacenamiento en HSM o KMS).
5. La autenticidad de las claves públicas debe garantizarse mediante **autoridades de certificación reconocidas (PKI)** y certificados válidos.
6. Deben **registrarse y auditarse** todas las actividades relacionadas con la gestión de claves.

---

> [!tip] Enlaces Relacionados
> * [[02 - MODULOS/02 - ISO 27002|Índice General ISO 27002:2022]]
> * [[02 - MODULOS/ISO 27002/01 - Seguridad Física|🏢 Controles de Seguridad Física]]
> * [[02 - MODULOS/ISO 27002/02 - Seguridad Lógica|💻 Controles de Seguridad Lógica y Acceso]]

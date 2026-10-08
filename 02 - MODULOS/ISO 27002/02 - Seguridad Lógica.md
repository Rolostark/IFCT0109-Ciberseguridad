---
title: "ISO 27002:2022 — Seguridad Lógica y Control de Acceso"
aliases:
  - Seguridad Logica
  - Controles Logicos
  - ISO 27002 Seguridad Logica
  - slogica
tags:
  - iso27002
  - iso27001
  - seguridad-logica
  - control-de-acceso
  - ifct0109
---

# 💻 Controles de Acceso — Seguridad Lógica

> [!abstract] Cláusulas 5, 6 y 8 de ISO/IEC 27002:2022
> Esta sección recoge los **20 controles fundamentales de seguridad lógica y control de acceso** seleccionados de las cláusulas organizativas, de personas y tecnológicas.
> 
> 🔗 **Navegación:** [[02 - MODULOS/02 - ISO 27002|⬅️ Volver al Índice General ISO 27002]] &emsp;|&emsp; [[00 - INDICE|🏠 Inicio]]

---

## 📑 Índice de Controles

### Cláusula 5 — Controles Organizativos
* [[#Control 5.3 — Segregación de tareas|Control 5.3 — Segregación de tareas]]
* [[#Control 5.10 — Uso aceptable de la información y activos asociados|Control 5.10 — Uso aceptable de la información y activos asociados]]
* [[#Control 5.11 — Devolución de activos|Control 5.11 — Devolución de activos]]
* [[#Control 5.12 — Clasificación de la información|Control 5.12 — Clasificación de la información]]
* [[#Control 5.13 — Etiquetado de la información|Control 5.13 — Etiquetado de la información]]
* [[#Control 5.14 — Transferencia de la información|Control 5.14 — Transferencia de la información]]
* [[#Control 5.15 — Control de acceso|Control 5.15 — Control de acceso]]
* [[#Control 5.16 — Gestión de identidad|Control 5.16 — Gestión de identidad]]
* [[#Control 5.17 — Información de autenticación|Control 5.17 — Información de autenticación]]
* [[#Control 5.18 — Derechos de acceso|Control 5.18 — Derechos de acceso]]

### Cláusula 6 — Controles de Personas
* [[#Control 6.7 — Teletrabajo|Control 6.7 — Teletrabajo]]

### Cláusula 8 — Controles Tecnológicos
* [[#Control 8.1 — Dispositivos finales de usuario|Control 8.1 — Dispositivos finales de usuario]]
* [[#Control 8.2 — Gestión de privilegios de acceso|Control 8.2 — Gestión de privilegios de acceso]]
* [[#Control 8.3 — Restricción de acceso a la información|Control 8.3 — Restricción de acceso a la información]]
* [[#Control 8.4 — Acceso al código fuente|Control 8.4 — Acceso al código fuente]]
* [[#Control 8.5 — Autenticación segura|Control 8.5 — Autenticación segura]]
* [[#Control 8.15 — Registros de eventos|Control 8.15 — Registros de eventos]]
* [[#Control 8.16 — Seguimiento de actividades|Control 8.16 — Seguimiento de actividades]]
* [[#Control 8.17 — Sincronización del reloj|Control 8.17 — Sincronización del reloj]]
* [[#Control 8.18 — Uso de programas de utilidad con privilegios|Control 8.18 — Uso de programas de utilidad con privilegios]]
* [[#Control 8.26 — Requisitos de seguridad de las aplicaciones|Control 8.26 — Requisitos de seguridad de las aplicaciones]]

---

## 🏛️ Cláusula 5 — Controles Organizativos

### Control 5.3 — Segregación de tareas ^c53

> [!info] Objetivo
> Reducir el riesgo de fraude, error y elusión de los controles de seguridad de la información, separando las funciones y áreas de responsabilidad que puedan entrar en conflicto entre diferentes personas.

**Aspectos clave:**
1. El principio fundamental es que **ninguna persona debe poder ejecutar por sí sola tareas contradictorias** que en conjunto podrían facilitar un fraude, un error o la elusión de controles.
2. Son ejemplos típicos de tareas que deben segregarse: **solicitar, aprobar y aplicar derechos de acceso**; iniciar, aprobar y ejecutar un cambio; o el diseño, implementación y revisión de código.
3. Al diseñar los controles de segregación debe tenerse en cuenta la posibilidad de **colusión**, es decir, que varias personas se pongan de acuerdo para eludir los controles.
4. En **organizaciones pequeñas** donde la segregación completa no sea viable, deben aplicarse controles compensatorios como la monitorización de actividades, registros de auditoría o supervisión por parte de la dirección.
5. Cuando se utilizan sistemas de **control de acceso basado en roles (RBAC)**, debe evitarse que una misma persona acumule roles conflictivos, considerando el uso de herramientas automatizadas para identificar y eliminar dichos conflictos cuando el número de roles sea elevado.
6. Los roles deben estar **cuidadosamente definidos** para minimizar problemas de acceso si un rol es eliminado o reasignado.

---

### Control 5.10 — Uso aceptable de la información y activos asociados ^c510

> [!info] Objetivo
> Definir las reglas sobre cómo los empleados y otras partes interesadas deben utilizar la información y los activos de la organización de forma correcta y segura.

**Aspectos clave:**
1. Establece **qué usos están permitidos y cuáles están prohibidos** para la información y los activos de la organización.
2. Los usuarios deben **conocer y aceptar estas reglas** antes de acceder a los activos.
3. La política debe cubrir todos los tipos de activos: información digital, documentos físicos, equipos, software y servicios.

---

### Control 5.11 — Devolución de activos ^c511

> [!info] Objetivo
> Garantizar que cuando una persona deja de tener relación con la organización devuelve todos los activos que le fueron asignados, asegurando que no se retiene acceso ni información de forma indebida.

**Aspectos clave:**
1. Aplica en situaciones de **fin de contrato, cambio de puesto** o cualquier modificación en la relación laboral.
2. Incluye la devolución de activos tanto **físicos** (equipos, tarjetas, llaves) como **de información** (documentos, datos almacenados).
3. Debe establecerse un **procedimiento formal** vinculado al proceso de baja o cambio de puesto.

---

### Control 5.12 — Clasificación de la información ^c512

> [!info] Objetivo
> Establecer que la información debe clasificarse según su nivel de sensibilidad y criticidad para la organización, de forma que se le apliquen los controles de protección adecuados.

**Aspectos clave:**
1. La clasificación la determina el **propietario de la información** en función del impacto que tendría su divulgación, modificación o pérdida.
2. Deben definirse **niveles de clasificación claros** (por ejemplo: pública, interna, confidencial, restringida).
3. Los controles de protección deben ser **proporcionales al nivel de clasificación** asignado.

---

### Control 5.13 — Etiquetado de la información ^c513

> [!info] Objetivo
> Complementar la clasificación (5.12) estableciendo que una vez clasificada la información, debe etiquetarse de forma apropiada para que todos los usuarios puedan identificar su nivel y aplicar el tratamiento correspondiente.

**Aspectos clave:**
1. El etiquetado puede ser **físico** (marcas en documentos impresos, carpetas) o **electrónico** (metadatos, marcas de agua, cabeceras).
2. Debe permitir a cualquier usuario **identificar fácilmente** el nivel de clasificación de la información.
3. El sistema de etiquetado debe ser **coherente y fácil de aplicar** en toda la organización.

---

### Control 5.14 — Transferencia de la información ^c514

> [!info] Objetivo
> Establecer que cuando la información se transfiere dentro de la organización o hacia terceros, deben aplicarse las medidas de protección adecuadas según su nivel de clasificación.

**Aspectos clave:**
1. Cubre **todos los medios de transferencia**: correo electrónico, mensajería, soportes físicos, transferencia verbal, plataformas en la nube, etc.
2. Puede requerir **acuerdos formales de confidencialidad** o transferencia con las partes implicadas.
3. Las medidas deben ser **proporcionales a la clasificación** de la información transferida (cifrado, canales seguros, registro de entregas, etc.).

---

### Control 5.15 — Control de acceso ^c515

> [!info] Objetivo
> Establecer la política general de control de acceso que define las reglas de acceso físico y lógico a la información y otros activos, basándose en los requisitos del negocio y de seguridad.

> [!important] Control Marco
> Este es el control marco que establece la política general. Los controles 5.16 a 5.18 y los controles tecnológicos de la cláusula 8 implementan esta política en la práctica.

**Aspectos clave:**
1. Debe definirse una **política de control de acceso** basada en los requisitos del negocio y de seguridad de la información.
2. La política debe aplicar el **principio de mínimo privilegio**: cada usuario solo accede a lo estrictamente necesario para su función.
3. Debe contemplar modelos de control de acceso como **RBAC** (basado en roles), **MAC** (control de acceso obligatorio) o **ABAC** (basado en atributos).
4. Debe cubrir tanto el **acceso lógico** (sistemas, aplicaciones, datos) como el **acceso físico** (instalaciones, zonas restringidas).

---

### Control 5.16 — Gestión de identidad ^c516

> [!info] Objetivo
> Gestionar el ciclo de vida completo de las identidades, permitiendo la identificación única de los individuos y sistemas que acceden a la información y garantizando la adecuada asignación de los derechos de acceso.

**Aspectos clave:**
1. Cada identidad asignada a una persona debe estar vinculada a **una única persona**, de forma que pueda responsabilizarse de las acciones realizadas con dicha identidad.
2. Las **identidades compartidas** entre varias personas solo están permitidas cuando existe una justificación empresarial u operativa, y deben estar sujetas a aprobación y documentación específicas.
3. Las identidades asignadas a **entidades no humanas** (máquinas, servicios, aplicaciones) deben contar con una aprobación debidamente segregada y una supervisión permanente e independiente.
4. Las identidades deben **desactivarse o eliminarse** de forma oportuna cuando ya no sean necesarias, por ejemplo cuando una persona abandona la organización o cambia de función.
5. Debe evitarse la asignación de **identidades duplicadas**, es decir, no se deben asignar múltiples identidades a la misma entidad dentro de un mismo contexto.
6. Deben mantenerse **registros de todos los eventos significativos** relacionados con el uso y la gestión de las identidades y la información de autenticación.
7. Cuando se utilicen **identidades de terceros** como credenciales de redes sociales, la organización debe garantizar que proporcionan el nivel de confianza requerido y que los riesgos asociados son conocidos y gestionados.
8. El **ciclo de vida de una identidad** incluye fases como la verificación, creación, activación, provisión de derechos y revocación, debiendo gestionarse cada una de forma controlada.

---

### Control 5.17 — Información de autenticación ^c517

> [!info] Objetivo
> Garantizar la correcta autenticación de las entidades y evitar fallos en los procesos de autenticación, controlando la asignación y gestión de la información de autenticación mediante un proceso formal.

#### Asignación y gestión
1. Las contraseñas o PINs generados como **información temporal** deben ser únicos, no adivinables y los usuarios deben estar obligados a cambiarlos tras el primer uso.
2. Antes de proporcionar información de autenticación nueva, debe **verificarse la identidad del usuario** mediante un procedimiento establecido.
3. La información de autenticación debe transmitirse siempre de forma segura a través de **canales autenticados y protegidos**, evitando el uso de correo electrónico sin cifrar.
4. La información de autenticación **por defecto o del fabricante** debe cambiarse inmediatamente tras la instalación de sistemas o software.
5. Deben mantenerse **registros de los eventos significativos** relativos a la asignación y gestión, garantizando su confidencialidad mediante herramientas aprobadas como gestores de contraseñas.

#### Responsabilidades del usuario
1. Los usuarios deben mantener **confidencial** su información de autenticación y **no compartirla con nadie**.
2. Cualquier información comprometida debe **cambiarse de inmediato**.
3. Las contraseñas deben ser **robustas**, evitando información personal fácilmente adivinable, palabras de diccionario o combinaciones simples. Se recomienda el uso de **frases de contraseña** con caracteres alfanuméricos y especiales y una longitud mínima establecida.
4. No deben **reutilizarse** las mismas contraseñas en distintos servicios o sistemas.
5. Estas obligaciones deben estar recogidas en las **condiciones de empleo** del usuario.

#### Sistema de gestión de contraseñas
1. Debe permitir a los usuarios **elegir y cambiar** sus propias contraseñas, con procedimiento de confirmación.
2. Debe forzar el **cambio en el primer inicio de sesión** y cuando sea necesario (tras incidentes de seguridad, cambios de empleo).
3. Debe evitar la **reutilización de contraseñas anteriores** y el uso de contraseñas comunes o comprometidas.
4. Las contraseñas no deben **mostrarse en pantalla** durante su introducción y deben almacenarse y transmitirse de forma protegida mediante **técnicas criptográficas** (hashing).

> [!tip] Consideraciones adicionales
> Exigir cambios frecuentes puede ser contraproducente (contraseñas débiles, anotadas en post-its). El SSO reduce la cantidad de credenciales pero incrementa el impacto en caso de compromiso. Otros medios de autenticación incluyen claves criptográficas, tarjetas inteligentes y biometría.

---

### Control 5.18 — Derechos de acceso ^c518

> [!info] Objetivo
> Garantizar que el acceso a la información y a otros activos asociados es definido y autorizado de acuerdo con los requisitos del negocio, gestionando el ciclo de vida completo de los derechos de acceso desde su provisión hasta su eliminación.

#### Provisión y revocación
1. La asignación debe contar con la **autorización del propietario** de la información, pudiendo requerir aprobación adicional por parte de la dirección.
2. Deben considerarse los requisitos de negocio y la **segregación de funciones**, separando los roles de aprobación, implementación y revisión.
3. Los derechos deben **eliminarse oportunamente** cuando un usuario ya no los necesita, especialmente al abandonar la organización o cambiar de puesto.
4. Pueden concederse **derechos temporales** por un periodo limitado, especialmente para personal temporal, revocándose automáticamente en la fecha de expiración.
5. Los derechos de **proveedores y terceros** solo deben activarse una vez completados los procedimientos de autorización.
6. Debe mantenerse un **registro central** de todos los derechos concedidos (físicos y lógicos) y de todos los cambios realizados.

#### Revisión de los derechos
1. Deben **revisarse periódicamente**, especialmente tras cambios organizativos (promociones, cambios de puesto, fin de empleo).
2. Las revisiones deben prestar especial atención a las **autorizaciones de acceso privilegiado**.
3. Antes de que un usuario cambie de puesto o abandone la organización, sus derechos deben ser **revisados, ajustados o eliminados**, considerando factores de riesgo como si la baja es voluntaria o forzosa.

> [!tip] Consideraciones adicionales
> Se recomienda gestionar los derechos mediante roles o perfiles de usuario. La clonación de identidades debe hacerse con cautela, basándose en roles definidos. Los contratos deben incluir cláusulas sobre sanciones por acceso no autorizado. En casos de cese por la dirección, la gestión de derechos debe ser especialmente cuidadosa ante el riesgo de sabotaje.

---

## 👥 Cláusula 6 — Controles de Personas

### Control 6.7 — Teletrabajo ^c67

> [!info] Objetivo
> Garantizar la seguridad de la información cuando el personal trabaja de manera remota, implementando las medidas necesarias para proteger la información a la que se accede, procesa o almacena fuera de las instalaciones de la organización.

#### Política de teletrabajo
1. La organización debe establecer una **política específica** que defina las condiciones y restricciones aplicables, incluyendo los trabajos permitidos, la clasificación de la información manejable y los sistemas accesibles.
2. Debe tenerse en cuenta que algunas recomendaciones pueden no ser aplicables debido a la **legislación local** de las distintas jurisdicciones.

#### Seguridad física del entorno remoto
1. Debe evaluarse la **seguridad física del lugar de trabajo remoto**, incluyendo archivadores con cerradura, normas de escritorio despejado y procedimientos de impresión y eliminación seguros.
2. Deben contemplarse las amenazas de **acceso no autorizado por personas del entorno** del trabajador (familiares, amigos) así como en lugares públicos.

#### Seguridad de las comunicaciones
1. Deben implementarse mecanismos seguros de **autenticación y control de acceso remoto**, prestando especial atención a la autenticación de factor único.
2. El uso de **redes domésticas y públicas** debe estar regulado (cortafuegos, antimalware, configuración de redes inalámbricas).
3. Debe controlarse el uso de **acceso a escritorios virtuales** y el almacenamiento de información en equipos de propiedad privada.

#### Gestión de dispositivos
1. Cuando no se permita el uso de equipos privados, la organización debe **proveer equipos adecuados**.
2. Los dispositivos deben contar con **bloqueo de pantalla, temporizadores, seguimiento de ubicación y borrado remoto**.
3. Deben establecerse **procedimientos de copia de seguridad y continuidad** adaptados al entorno remoto.

#### Formación y finalización
1. Los trabajadores remotos deben recibir **formación específica** sobre procesos de negocio seguros en remoto.
2. Al finalizar el teletrabajo deben **revocarse autorizaciones y derechos de acceso** y recuperarse los equipos proporcionados.

---

## 💻 Cláusula 8 — Controles Tecnológicos

### Control 8.1 — Dispositivos finales de usuario ^c81

> [!info] Objetivo
> Proteger la información contra los riesgos introducidos por el uso de dispositivos finales de usuario, estableciendo políticas y procedimientos para su configuración y manejo seguros.

#### Política general de dispositivos
1. Debe establecerse una **política específica** sobre configuración y manejo seguros, comunicada a todo el personal relevante.
2. Debe contemplar: registro de dispositivos, **protección física, restricción de instalación de software, cifrado, protección contra malware** y copias de seguridad.
3. Debe controlarse el uso de **dispositivos extraíbles** como memorias USB, pudiendo desactivarse los puertos físicos cuando sea necesario.
4. La información especialmente sensible puede requerir que solo sea **accesible desde el dispositivo pero nunca almacenada en él**.

#### Responsabilidades del usuario
1. Los usuarios deben **cerrar sesiones activas** y proteger sus dispositivos cuando no estén en uso.
2. En lugares públicos deben extremar precauciones, usando **filtros de privacidad** en pantalla cuando sea necesario.
3. Debe existir un **procedimiento para robo o pérdida** de dispositivos.

#### Uso de dispositivos personales (BYOD)
1. Debe garantizarse la **separación entre el uso personal y profesional**, usando software específico (MDM/MAM).
2. Los usuarios deben aceptar formalmente sus obligaciones, permitiendo a la organización el **borrado remoto** en caso de pérdida o fin de autorización.
3. Deben considerarse aspectos legales como la **propiedad intelectual** y las licencias de software.

#### Conexiones inalámbricas
1. Deben establecerse procedimientos para la **configuración segura de conexiones inalámbricas**, desactivando protocolos vulnerables.
2. Las conexiones inalámbricas presentan **riesgos adicionales** respecto a las conexiones por cable, especialmente fuera de las instalaciones.

---

### Control 8.2 — Gestión de privilegios de acceso ^c82

> [!info] Objetivo
> Garantizar que únicamente los usuarios, componentes de software y servicios autorizados dispongan de derechos de acceso privilegiados, restringiendo y controlando su asignación y uso.

> [!warning] Factor crítico de violación
> El uso inadecuado de los privilegios de administrador es uno de los principales factores de fallo o violación de los sistemas, por lo que su gestión rigurosa es especialmente crítica.

**Aspectos clave:**
1. Los privilegios deben asignarse siguiendo el **principio de mínimo privilegio**: solo a personas con la competencia necesaria y para los roles que lo requieran.
2. Debe existir un **proceso formal de autorización** para la asignación, manteniendo un registro de todos los privilegios.
3. Los privilegios deben tener una **fecha de expiración definida** y revisarse periódicamente.
4. Los requisitos de autenticación para accesos privilegiados deben ser **más exigentes** que los del acceso normal, pudiendo requerir reautenticación.
5. Debe evitarse el uso de **identidades genéricas de administración** como "root". Cada persona debe tener una identidad separada para tareas privilegiadas, que **no debe usarse para actividades cotidianas** (correo, navegación web).
6. El acceso privilegiado temporal (**procedimiento "break glass"**) debe concederse solo durante el tiempo estrictamente necesario.
7. Todos los accesos privilegiados deben **registrarse** con fines de auditoría, y las identidades privilegiadas **no deben compartirse**.

---

### Control 8.3 — Restricción de acceso a la información ^c83

> [!info] Objetivo
> Garantizar únicamente el acceso autorizado y evitar el acceso no autorizado a la información y otros activos asociados, de acuerdo con las políticas de control de acceso definidas.

#### Restricción de acceso básica
1. No debe permitirse el acceso a información sensible por parte de **identidades desconocidas o anónimas**. El acceso público solo debe concederse a ubicaciones sin información confidencial.
2. Deben proporcionarse mecanismos para controlar **qué datos puede acceder cada usuario** y qué tipo de operaciones puede realizar: lectura, escritura, eliminación o ejecución.
3. Deben implementarse **controles de aislamiento** físicos o lógicos para aplicaciones, datos o sistemas sensibles.

#### Gestión de acceso dinámico (DAM)
> [!abstract] ¿Qué es DAM?
> La gestión de acceso dinámico va más allá de los controles estáticos. En lugar de permisos fijos, evalúa las condiciones en tiempo real (identidad, dispositivo, ubicación, horario, estado de seguridad del dispositivo, comportamiento previo del usuario) y decide si permite el acceso.

1. Son especialmente útiles cuando se necesita un **control granular** sobre quién accede, durante qué período y de qué manera.
2. Permiten gestionar en tiempo real el uso y distribución, protegiéndola contra **cambios, copia o distribución no autorizados**, incluyendo la impresión.
3. Deben proteger la información a lo largo de **todo su ciclo de vida**: creación, procesamiento, almacenamiento, transmisión y eliminación.
4. Los permisos pueden concederse en función de **múltiples condiciones simultáneas**.
5. Deben incluirse mecanismos de **cifrado, registro de accesos, alertas** ante usos indebidos y control de permisos de impresión.

**Tecnologías que lo implementan:**
Las más conocidas son las soluciones de **IRM** (*Information Rights Management*) como Microsoft Azure Information Protection, que "envuelven" los documentos con una capa de protección que viaja con ellos. También los sistemas **ABAC** (*Attribute-Based Access Control*) basan decisiones en la evaluación de múltiples atributos en tiempo real.

> [!note] Complemento a las ACL
> Las técnicas DAM no reemplazan la gestión clásica mediante ACL, sino que la complementan. Son especialmente valiosas en la respuesta a incidentes, ya que los permisos pueden modificarse o revocarse incluso cuando los datos han sido compartidos fuera de la organización.

---

### Control 8.4 — Acceso al código fuente ^c84

> [!info] Objetivo
> Impedir la introducción de funcionalidades no autorizadas, evitar cambios no intencionales o maliciosos y mantener la confidencialidad de la propiedad intelectual.

**Aspectos clave:**
1. El acceso al código fuente y sus elementos asociados (diseños, especificaciones, planes de verificación) y a las herramientas de desarrollo debe estar **estrictamente controlado**.
2. El almacenamiento debe gestionarse mediante un **sistema centralizado de gestión de código fuente** que permita controlar y auditar todos los accesos y cambios.
3. El **acceso de lectura y escritura** deben tratarse de forma diferenciada. La lectura puede ser más amplia, pero la escritura debe restringirse a personal privilegiado o propietarios designados.
4. Los desarrolladores no deben tener **acceso directo al repositorio**, sino a través de herramientas que controlen y registren sus actividades.
5. Cualquier actualización debe seguir los **procedimientos de control de cambios** establecidos.
6. Debe mantenerse un **registro de auditoría completo** de todos los accesos y cambios.
7. Si se pretende publicar el código fuente, deben implementarse controles adicionales que garanticen su integridad, como el uso de **firma digital**.

---

### Control 8.5 — Autenticación segura ^c85

> [!info] Objetivo
> Garantizar que los usuarios y entidades estén autenticados de forma segura cuando acceden a sistemas, aplicaciones y servicios, implementando tecnologías y procedimientos adecuados al nivel de riesgo.

#### Métodos de autenticación
1. La solidez del método debe ser **proporcional al nivel de clasificación** de la información a la que se accede.
2. Cuando se requiera autenticación sólida deben utilizarse **métodos alternativos a las contraseñas**: certificados digitales, tarjetas inteligentes o biometría.
3. Para sistemas críticos debe implementarse **MFA** (autenticación multifactorial), combinando al menos dos factores: algo que sabes, algo que tienes, algo que eres.
4. La MFA puede combinarse con **reglas contextuales** (ubicación, dispositivo, horario) para verificaciones adicionales en circunstancias inusuales.
5. La autenticación biométrica debe ir **acompañada de alternativas**, ya que puede no estar disponible en determinadas condiciones. Si los datos biométricos se comprometen, deben invalidarse de inmediato.

#### Procedimientos de inicio de sesión
1. No debe mostrarse **información del sistema** hasta que el inicio de sesión se complete con éxito.
2. Los mensajes de error **no deben indicar qué parte de los datos es correcta o incorrecta**.
3. Deben implementarse protecciones contra **fuerza bruta**: CAPTCHA, bloqueo de cuenta tras intentos fallidos, restablecimiento obligatorio.
4. Las contraseñas **nunca deben mostrarse en texto legible** ni transmitirse sin cifrar.
5. Deben registrarse tanto los **intentos fallidos como los exitosos**, generando alertas ante posibles violaciones.
6. Tras un inicio exitoso debe mostrarse información sobre el **último acceso e intentos fallidos**.
7. Las sesiones inactivas deben **finalizarse automáticamente**, especialmente en ubicaciones de alto riesgo.
8. Deben establecerse **límites de tiempo de conexión** para aplicaciones de alto riesgo.

---

### Control 8.15 — Registros de eventos ^c815

> [!info] Objetivo
> Registrar eventos, generar evidencias, garantizar la integridad de la información de registro, prevenir el acceso no autorizado e identificar eventos de seguridad que puedan conducir a incidentes.

#### Contenido de los registros
1. La organización debe determinar **qué datos se recopilan**, documentándolo en una política específica.
2. Cada registro debe incluir: **ID de usuario, actividades del sistema, fechas y horas, identidad del dispositivo, ubicación** y direcciones y protocolos de red.
3. Eventos que deben registrarse: intentos de acceso exitosos y fallidos, cambios de configuración, uso de privilegios, acceso a archivos, activación/desactivación de seguridad, y creación/modificación/eliminación de identidades.
4. Todos los sistemas deben tener **fuentes de tiempo sincronizadas** para permitir la correlación entre sistemas.

#### Protección de los registros
1. Los usuarios, incluso privilegiados, **no deben poder eliminar ni desactivar** los registros de sus propias actividades.
2. Los registros deben protegerse contra alteraciones mediante **hashing criptográfico, archivos de solo apéndice** (*append-only*) o archivos de transparencia pública.
3. Cuando deban enviarse a proveedores externos, deben **anonimizarse previamente** (enmascaramiento de datos).
4. Los registros pueden contener **datos personales**, por lo que deben adoptarse medidas de protección de la privacidad.

#### Análisis de registros
1. El análisis debe orientarse a identificar **actividades inusuales o comportamientos anómalos**, apoyándose en herramientas como **SIEM**, **IDS** o **UEBA**.
2. Debe considerar la **correlación entre sistemas**, comparación con patrones conocidos, análisis de tendencias e inteligencia de amenazas.
3. Deben revisarse aspectos como accesos a recursos protegidos, registros DNS, informes de proveedores y registros de acceso físico.
4. Los incidentes identificados deben investigarse como parte del **proceso de gestión de incidentes**.

---

### Control 8.16 — Seguimiento de actividades ^c816

> [!info] Objetivo
> Detectar comportamientos anómalos y posibles incidentes de seguridad de la información, monitorizando redes, sistemas y aplicaciones y tomando las medidas adecuadas para evaluarlos y responder.

#### Alcance y línea base
1. El alcance y nivel de monitorización debe determinarse en función de los **requisitos de seguridad y de negocio** y la legislación aplicable.
2. Debe establecerse una **línea base de comportamiento normal**: utilización de sistemas en periodos normales y picos, horarios habituales, ubicación y frecuencia de acceso.
3. Elementos a monitorizar: **tráfico de red**, acceso a sistemas críticos, registros de herramientas de seguridad (antivirus, IDS, IPS, firewalls, DLP) y uso de recursos (CPU, memoria, ancho de banda).

#### Detección de anomalías
1. El sistema debe detectar: terminación no planificada de procesos, actividad de **código malicioso**, tráfico desde IPs maliciosas, características de ataques conocidos (DoS, desbordamientos de búfer), comportamiento inusual (keyloggers, inyección de procesos) y accesos no autorizados.

#### Herramientas y alertas
1. Debe utilizarse **monitorización continua** con herramientas capaces de manejar grandes volúmenes y notificación en tiempo real.
2. Las alertas deben basarse en **umbrales predefinidos**, ajustados para minimizar falsos positivos.
3. Debe contarse con **sistemas redundantes** y personal capacitado para interpretar los incidentes.

> [!tip] UEBA (User and Entity Behavior Analytics)
> UEBA utiliza inteligencia artificial y *machine learning* para aprender el comportamiento habitual de cada usuario y entidad, construyendo una línea base individualizada y detectando automáticamente desviaciones significativas. A diferencia de los sistemas basados en reglas fijas, UEBA detecta amenazas desconocidas y amenazas internas (por ejemplo, un empleado que descarga volúmenes inusuales de información antes de abandonar la organización). Es especialmente útil combinado con sistemas SIEM.

---

### Control 8.17 — Sincronización del reloj ^c817

> [!info] Objetivo
> Garantizar que los relojes de los sistemas de procesamiento de información estén sincronizados con fuentes de tiempo aprobadas, permitiendo la correlación y análisis de eventos de seguridad.

**Aspectos clave:**
1. Los requisitos de **representación del tiempo, sincronización y precisión** deben documentarse e implementarse.
2. Debe definirse un **tiempo de referencia estándar** para todos los sistemas, incluyendo sistemas de gestión de edificios y control de entrada/salida.
3. Como reloj de referencia debe utilizarse un **reloj atómico nacional o GPS**.
4. Deben usarse protocolos estándar como **NTP** (*Network Time Protocol*) o **PTP** (*Precision Time Protocol*).
5. Pueden utilizarse **dos fuentes de tiempo externas simultáneamente** para mejorar la fiabilidad.
6. En **entornos multi-nube o híbridos**, la sincronización puede ser especialmente compleja y debe monitorizarse.
7. Un ajuste incorrecto puede **obstaculizar investigaciones** y dañar la credibilidad de las pruebas en casos legales.

---

### Control 8.18 — Uso de programas de utilidad con privilegios ^c818

> [!info] Objetivo
> Garantizar que el uso de programas de utilidad que pueden invalidar los controles del sistema esté rigurosamente restringido y controlado.

> [!abstract] Ejemplos típicos
> Herramientas de diagnóstico, parches, antivirus, desfragmentadores de disco, depuradores, herramientas de copia de seguridad y herramientas de red.

**Aspectos clave:**
1. Su uso debe limitarse al **número mínimo posible de usuarios** autorizados y de confianza (principio de mínimo privilegio, control 8.2).
2. Debe aplicarse un **proceso formal de identificación, autenticación y autorización** para cada uso.
3. Los **niveles de autorización** deben estar claramente definidos y documentados. El uso puntual requiere autorización previa específica.
4. No deben estar disponibles para usuarios con acceso a aplicaciones donde se requiere **segregación de funciones**.
5. Todos los programas innecesarios deben **eliminarse o deshabilitarse**, reduciendo la superficie de ataque.
6. Debe existir una **segregación lógica** (y si es posible, a nivel de red) entre los programas de utilidad y el software de aplicación.
7. Su disponibilidad debe **limitarse en el tiempo** (solo durante la duración de un cambio autorizado).
8. Debe mantenerse un **registro completo** de todo uso con fines de auditoría y trazabilidad.

---

### Control 8.26 — Requisitos de seguridad de las aplicaciones ^c826

> [!info] Objetivo
> Garantizar que todos los requisitos de seguridad se identifican, especifican y aprueban antes de desarrollar o adquirir cualquier aplicación, integrando la seguridad desde el inicio.

**Aspectos clave:**
1. Los requisitos deben identificarse mediante una **evaluación de riesgos** y desarrollarse con el apoyo de especialistas en seguridad (*Security by Design*).
2. Deben contemplarse: **autenticación de entidades, clasificación de la información** que procesará, segregación de acceso y protección de datos en tránsito y en reposo.
3. La aplicación debe ser **resiliente frente a ataques** (inyecciones SQL, desbordamiento de búfer) e incluir controles de entrada/salida, validación de datos y gestión de mensajes de error.
4. Deben considerarse los **requisitos legales, regulatorios y de privacidad** aplicables.
5. Para aplicaciones con **servicios transaccionales** entre organizaciones: garantizar integridad, no repudio y confidencialidad mediante firmas digitales o funciones hash.
6. Para aplicaciones de **pedidos y pagos electrónicos**: proteger datos del pedido y pago, evitar duplicidades y almacenar detalles fuera de entornos accesibles desde Internet.
7. Algunas consideraciones pueden abordarse mediante el uso de **criptografía**, considerando la legislación aplicable.

---

> [!tip] Siguiente Sección
> Consulta los controles de robustecimiento en: [[02 - MODULOS/ISO 27002/03 - Robustecimiento de Sistemas y Redes|🛡️ Robustecimiento de Sistemas y Redes]]

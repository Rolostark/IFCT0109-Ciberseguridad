---
title: "MAGERIT — Caso Práctico Simulacro ClínicaSalud"
aliases:
  - Simulacro Clinica
  - Simulacro Magerit
  - Caso Practico Magerit
tags:
  - magerit/caso-practico
  - simulacro
  - clinicasalud
  - gestion-de-riesgos
---

# 🏥 MAGERIT — Caso Práctico: Simulacro ClínicaSalud

> [!abstract] Información del Documento
> **Documento Original:** `simulacro.docx` (Convertido a Markdown para consulta offline).
> **Archivo Original:** [📥 Descargar simulacro.docx](../archivos_descargados/documentos/simulacro.docx)

---

SIMULACRO DE

ANÁLISIS DE RIESGOS

METODOLOGÍA MAGERIT v3


|  |
| :--- |

Caso práctico: ClínicaSalud

Análisis completo de tres activos representativos

Material para uso en clase

Umbral de aceptación: riesgos B y MB


# 1. Introducción y contexto

Este documento presenta un simulacro completo de análisis de riesgos siguiendo la metodología MAGERIT v3, aplicado a un caso ficticio: ClínicaSalud, una clínica médica pequeña con gestión de historias clínicas electrónicas.

El objetivo es recorrer paso a paso el proceso real que seguiría un analista, incluyendo el inventario de activos, el árbol de dependencias, y el análisis completo de tres activos representativos de distintas capas: un activo de datos, uno de hardware y uno de personal.

Organización: ClínicaSalud (clínica médica ficticia)

Metodología: MAGERIT v3

Umbral de riesgo aceptable: La dirección acepta riesgos de nivel Bajo (B) y Muy Bajo (MB). Todo riesgo de nivel Medio o superior requiere tratamiento.


# 2. Esquema general del proceso

El análisis de riesgos MAGERIT sigue este flujo secuencial:


| Fase | Pregunta clave |
| :--- | :--- |
| 1. Inventario de activos | ¿Qué tengo y cómo se relacionan entre sí? |
| 2. Valoración de activos | ¿Cuánto vale cada activo en las dimensiones ACIDA? |
| 3. Amenazas | ¿Qué puede pasarle a cada activo? (catálogo MAGERIT) |
| 4. Degradación | ¿Cuánto daño causa cada amenaza? (100%, 10%, 1%) |
| 5. Impacto | Valor × Degradación (primer cruce en tabla) |
| 6. Frecuencia | ¿Con qué frecuencia ocurre? (MF, F, N, PF) |
| 7. Riesgo | Impacto × Frecuencia (segundo cruce en tabla) |
| 8. Comparar con umbral | ¿Está por encima de lo aceptable? |
| 9. Salvaguardas | Solo para riesgos por encima del umbral |
| 10. Riesgo residual | Recalcular tras aplicar salvaguardas |
| 11. Aceptación | La dirección firma y asume lo que queda |


| Nota: La unidad de trabajo es el par (activo, amenaza). Para cada par se estima degradación y frecuencia, y se realizan dos cruces en tabla para obtener primero el impacto y luego el riesgo. |
| :--- |


# 3. Inventario de activos de ClínicaSalud

A continuación se presenta el inventario completo de activos identificados en la organización, clasificados según la tipología MAGERIT:


| ID | Activo | Tipo | Capa | Dim. crítica |
| :--- | :--- | :--- | :--- | :--- |
| A1 | Historias clínicas electrónicas | [D] | 1. Datos | C: MA |
| A2 | Datos de facturación y seguros | [D] | 1. Datos | C: A, I: A |
| A3 | Claves de cifrado / certificados | [K] | 1. Datos | C: MA |
| A4 | Servicio de atención al paciente | [S] | 2. Servicios | D: A |
| A5 | App gestión clínica (HIS) | [SW] | 3. Software | D: A, I: A |
| A6 | Correo electrónico | [SW] | 3. Software | C: M, I: M |
| A7 | Servidor principal | [HW] | 4. Hardware | D: A |
| A8 | PCs / puestos de trabajo | [HW] | 4. Hardware | D: M |
| A9 | Red local (LAN/WiFi) | [COM] | 5. Comunic. | D: M |
| A10 | Discos de backup | [MEDIA] | 5. Soportes | D: A, C: A |
| A11 | SAI / UPS | [AUX] | 6. Auxiliar | D: A |
| A12 | Climatización sala servidores | [AUX] | 6. Auxiliar | D: M |
| A13 | Sala de servidores | [L] | 7. Instalac. | D: A |
| A14 | Edificio de la clínica | [L] | 7. Instalac. | D: M |
| A15 | Personal clínico (médicos, enf.) | [P] | 8. Personal | C: A |
| A16 | Personal administrativo | [P] | 8. Personal | C: M |
| A17 | Administrador de sistemas / TI | [P] | 8. Personal | C: MA, D: A |


## 3.1 Orden de análisis

El análisis se realiza de arriba hacia abajo en la jerarquía de dependencias. Primero se valoran los activos superiores (los que tienen valor de negocio propio) y luego se desciende hacia la infraestructura. La razón es que necesitamos conocer el valor de los activos superiores para poder propagar ese valor hacia abajo por las dependencias.


| Orden | Capa | Razón |
| :--- | :--- | :--- |
| 1º | [D] Datos, [K] Claves | Tienen valor intrínseco para la organización. No dependen de otros para tener valor. |
| 2º | [S] Servicios | Su valor viene de los datos que manejan y los procesos que soportan. |
| 3º | [SW] Aplicaciones | Heredan valor de los servicios que prestan y datos que gestionan. |
| 4º | [HW] Hardware, [COM] Redes | Su valor es heredado de las capas superiores que dependen de ellos. |
| 5º | [MEDIA], [AUX] Soportes y auxiliares | Infraestructura de soporte. Valor heredado. |
| 6º | [L] Instalaciones | Contienen y protegen los activos anteriores. |
| 7º | [P] Personas | Interactúan con todas las capas. Se analizan por perfil/rol, no individualmente. |


## 3.2 Árbol de dependencias

Los activos se organizan en capas. Los de arriba dependen de los de abajo para funcionar. El valor se hereda de arriba hacia abajo: si las historias clínicas valen MA en confidencialidad, el servidor que las almacena hereda parte de ese valor. Cuando una amenaza se materializa en un activo inferior (por ejemplo, un incendio destruye el servidor), el impacto se propaga hacia arriba por la cadena de dependencias hasta llegar a los datos.

En ClínicaSalud, las dependencias principales son:


| Activo superior |  | Depende de | Tipo de dependencia |
| :--- | :--- | :--- | :--- |
| Historias clínicas [D] | → | App HIS [SW] | Necesita la app para ser accesibles |
| Historias clínicas [D] | → | Personal clínico [P] | Personas que las gestionan |
| Datos facturación [D] | → | App HIS [SW] | Gestión integrada |
| App HIS [SW] | → | Servidor principal [HW] | Ejecuta la aplicación |
| App HIS [SW] | → | Red local [COM] | Permite el acceso desde los PCs |
| Correo [SW] | → | Servidor principal [HW] | Alojado en el servidor |
| Servidor [HW] | → | Sala de servidores [L] | Ubicación física |
| Servidor [HW] | → | SAI [AUX] | Alimentación protegida |
| Servidor [HW] | → | Climatización [AUX] | Control de temperatura |
| Servidor [HW] | → | Admin. sistemas [P] | Mantenimiento y operación |
| Red local [COM] | → | Sala de servidores [L] | Equipamiento de red ubicado aquí |


| Nota: Las amenazas naturales e industriales (fuego, agua, averías) se asignan a los activos de infraestructura (HW, L, AUX), no a los datos. El impacto llega a los datos de forma indirecta a través de las dependencias. Una salvaguarda como un backup offsite rompe la dependencia única y reduce el impacto propagado. |
| :--- |


## 3.3 Tratamiento de las personas como activo

Las personas se analizan por perfil o rol, no individualmente. En ClínicaSalud se identifican tres perfiles con niveles de acceso y riesgo distintos:


| Perfil | Acceso | Criticidad |
| :--- | :--- | :--- |
| Personal clínico (médicos, enfermería) | Historiales clínicos, datos de salud | Alta (datos sensibles RGPD) |
| Personal administrativo | Facturación, citas, datos de contacto | Media |
| Administrador de sistemas / TI | Acceso privilegiado (root) a servidor, BBDD, red, backups | Muy alta (acceso total) |


| Nota: Las personas tienen una doble naturaleza en MAGERIT. Como activo, les pueden ocurrir cosas (enfermedad, extorsión, ingeniería social) y esas amenazas se analizan en el activo [P]. Pero como origen de amenazas, cuando un usuario comete un error (E.1) y borra un historial, esa amenaza se analiza en el activo que sufre las consecuencias (Historias Clínicas [D]), no en el activo persona. El catálogo de MAGERIT ya asigna cada amenaza al tipo de activo que recibe el impacto. |
| :--- |


# 4. Escalas y tablas de valoración


## 4.1 Valor del activo


| Nivel | Valor | Descripción |
| :--- | :--- | :--- |
| MA | Muy alto | Daño muy grave para la organización (sanciones RGPD, pérdida de confianza irreparable) |
| A | Alto | Daño grave (interrupción prolongada, pérdida económica significativa) |
| M | Medio | Daño importante pero manejable |
| B | Bajo | Daño menor, consecuencias limitadas |
| MB | Muy bajo | Daño insignificante, apenas perceptible |


## 4.2 Degradación


| Nivel | Degradación | Descripción |
| :--- | :--- | :--- |
| 100% | Totalmente degradada (Completa) | El activo queda completamente destruido o comprometido en esa dimensión |
| 10% | Algo afectada (Parcial) | El activo sufre un daño significativo pero parcial |
| 1% | Prácticamente nada afectada (Insignificante) | El daño es mínimo, apenas perceptible |


## 4.3 Tabla de Impacto (Valor × Degradación)

Este es el primer cruce. Se toma el valor del activo en la dimensión más afectada y se cruza con la degradación que causa la amenaza:


| Valor \ Degradación | 1% | 10% | 100% |
| :--- | :--- | :--- | :--- |
| Muy alto (MA) | M | A | MA |
| Alto (A) | B | M | A |
| Medio (M) | MB | B | M |
| Bajo (B) | MB | MB | B |
| Muy bajo (MB) | MB | MB | MB |


## 4.4 Frecuencia


| Nivel | Frecuencia | Descripción |
| :--- | :--- | :--- |
| MF | Muy frecuente | A diario |
| F | Frecuente | Mensualmente |
| N | Normal | Una vez al año |
| PF | Poco frecuente | Cada varios años |


## 4.5 Tabla de Riesgo (Impacto × Frecuencia)

Este es el segundo cruce. Se toma el impacto obtenido en el paso anterior y se cruza con la frecuencia estimada de la amenaza:


| Impacto \ Frecuencia | PF | N | F | MF |
| :--- | :--- | :--- | :--- | :--- |
| Muy alto (MA) | A | MA | MA | MA |
| Alto (A) | M | A | MA | MA |
| Medio (M) | B | M | A | MA |
| Bajo (B) | MB | B | M | A |
| Muy bajo (MB) | MB | MB | B | M |


| Nota: El impacto se calcula en la primera tabla y el riesgo en la segunda. Son dos pasos diferenciados que no se deben fusionar. El impacto refleja la gravedad del daño; el riesgo incorpora además la probabilidad de que ocurra. |
| :--- |


# 5. Análisis del Activo 1: Historias Clínicas Electrónicas

Activo: Historias Clínicas Electrónicas

Tipo MAGERIT: [D] Datos

Capa: 1 - Datos (valor de negocio propio)


## 5.1 Valoración del activo


| Dimensión | Valor | Justificación |
| :--- | :--- | :--- |
| Confidencialidad | MA | Datos de salud, categoría especial RGPD art. 9 |
| Integridad | A | Un error en un historial puede causar diagnóstico erróneo |
| Disponibilidad | A | Sin acceso a historiales no se puede atender al paciente |


## 5.2 Amenazas, impacto y riesgo

Para cada amenaza del catálogo MAGERIT aplicable a este tipo de activo [D], se indica la dimensión principal afectada, el valor del activo en esa dimensión, la degradación estimada, el impacto resultante (primer cruce), la frecuencia estimada, y el riesgo final (segundo cruce).


### Errores y fallos no intencionados


| Amenaza | Dim | Valor | Degr. | Impacto | Frec. | Riesgo | ¿Tratar? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| E.1 Errores de usuarios | D | A | 100% | A | F | MA | SÍ |
| E.2 Errores del administrador | C | MA | 10% | A | N | A | SÍ |
| E.3 Errores de monitorización | I | A | 1% | B | N | B | NO |
| E.4 Errores de configuración | I | A | 10% | M | PF | B | NO |
| E.15 Alteración accidental info. | I | A | 10% | M | F | A | SÍ |
| E.18 Destrucción de información | D | A | 100% | A | PF | M | SÍ |
| E.19 Fugas de información | C | MA | 10% | A | N | A | SÍ |


### Ataques intencionados


| Amenaza | Dim | Valor | Degr. | Impacto | Frec. | Riesgo | ¿Tratar? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| A.3 Manip. registros actividad | I | A | 10% | M | PF | B | NO |
| A.4 Manip. configuración | D | A | 100% | A | PF | M | SÍ |
| A.5 Suplantación identidad | C | MA | 100% | MA | N | MA | SÍ |
| A.6 Abuso de privilegios | C | MA | 10% | A | F | MA | SÍ |
| A.11 Acceso no autorizado | C | MA | 100% | MA | N | MA | SÍ |
| A.13 Repudio | I | A | 1% | B | PF | MB | NO |
| A.15 Modificación información | I | A | 100% | A | PF | M | SÍ |
| A.18 Destrucción información | D | A | 100% | A | PF | M | SÍ |
| A.19 Divulgación información | C | MA | 100% | MA | N | MA | SÍ |


## 5.3 Mapa de riesgos


| Nivel | Amenazas | Total |
| :--- | :--- | :--- |
| MA | E.1, A.5, A.6, A.11, A.19 | 5 |
| A | E.2, E.15, E.19 | 3 |
| M | E.18, A.4, A.15, A.18 | 4 |
| B | E.3, E.4, A.3, A.13 (aceptados) | 4 |
| MB | A.13 | 1 |

Resultado: 12 amenazas requieren tratamiento (5 MA + 3 A + 4 M). Las 4 de nivel B y la de MB se aceptan.


## 5.4 Tratamiento de riesgos


### Riesgos MA


| Amenaza | Salvaguarda propuesta | Tipo | Riesgo | Residual |
| :--- | :--- | :--- | :--- | :--- |
| E.1 Errores usuario | Formación + validaciones en app + backups diarios | Prev.+Corr. | MA | M |
| A.5 Suplantación | MFA obligatoria + política de contraseñas robusta | Preventiva | MA | B |
| A.6 Abuso privilegios | RBAC (mínimo privilegio) + revisión trimestral permisos | Preventiva | MA | B |
| A.11 Acceso no autoriz. | Cifrado BBDD + segmentación red + logs acceso | Prev.+Det. | MA | M |
| A.19 Divulgación | DLP + cifrado en tránsito y en reposo | Preventiva | MA | M |


### Riesgos A


| Amenaza | Salvaguarda propuesta | Tipo | Riesgo | Residual |
| :--- | :--- | :--- | :--- | :--- |
| E.2 Errores admin. | Procedimientos + entorno pruebas + doble validación | Preventiva | A | B |
| E.15 Alteración accid. | Controles integridad + versionado registros clínicos | Det.+Corr. | A | B |
| E.19 Fugas accident. | Política escritorio limpio + control USB + DLP | Preventiva | A | M |


### Riesgos M


| Amenaza | Salvaguarda propuesta | Tipo | Riesgo | Residual |
| :--- | :--- | :--- | :--- | :--- |
| E.18 Destrucción | Backup 3-2-1 + retención 30 días | Correctiva | M | B |
| A.4 Manip. config. | Hashing configuraciones + alertas cambios | Detectiva | M | B |
| A.15 Modif. info. | Auditoría de cambios + alertas integridad | Detectiva | M | B |
| A.18 Destrucción | Backup offline + control acceso estricto | Prev.+Corr. | M | B |


## 5.5 Riesgo residual


| Nivel | Amenazas | Total | Decisión |
| :--- | :--- | :--- | :--- |
| M | E.1, A.11, A.19, E.19 | 4 | Dirección acepta formalmente |
| B | Resto de amenazas tratadas | 8 | Dentro del umbral aceptado |
| B | Amenazas ya aceptadas (E.3, E.4, A.3) | 3 | Ya estaban dentro del umbral |
| MB | A.13 | 1 | Ya estaba dentro del umbral |


| Nota: Quedan 4 riesgos residuales en nivel M. La dirección debe aceptarlos formalmente o invertir en medidas adicionales (cifrado homomórfico, CASB, DLP avanzado). Se compromete a revisarlos en 6 meses. |
| :--- |


# 6. Análisis del Activo 2: Servidor Principal

Activo: Servidor Principal

Tipo MAGERIT: [HW] Equipos Informáticos

Capa: 4 - Hardware (valor heredado de capas superiores)


## 6.1 Valoración del activo


| Dimensión | Valor | Justificación |
| :--- | :--- | :--- |
| Disponibilidad | A | Sin servidor no hay acceso a historiales ni a la app HIS |
| Confidencialidad | A | Valor heredado: almacena datos de salud (dependencia) |
| Integridad | M | Un fallo de HW puede corromper datos en disco |


## 6.2 Amenazas, impacto y riesgo


### Desastres naturales


| Amenaza | Dim | Valor | Degr. | Impacto | Frec. | Riesgo | ¿Tratar? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| N.1 Fuego | D | A | 100% | A | PF | M | SÍ |
| N.2 Daños por agua | D | A | 100% | A | PF | M | SÍ |
| N.* Otros desastres nat. | D | A | 100% | A | PF | M | SÍ |


### De origen industrial


| Amenaza | Dim | Valor | Degr. | Impacto | Frec. | Riesgo | ¿Tratar? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| I.1 Fuego (industrial) | D | A | 100% | A | PF | M | SÍ |
| I.2 Daños por agua | D | A | 100% | A | PF | M | SÍ |
| I.* Desastres industriales | D | A | 100% | A | PF | M | SÍ |
| I.3 Contam. mecánica | D | A | 10% | M | N | M | SÍ |
| I.4 Contam. electromagn. | D | A | 10% | M | PF | B | NO |
| I.5 Avería física/lógica | D | A | 100% | A | F | MA | SÍ |
| I.6 Corte eléctrico | D | A | 100% | A | N | A | SÍ |
| I.7 Temp./humedad inadec. | D | A | 10% | M | N | M | SÍ |
| I.11 Emanaciones electrom. | C | A | 1% | B | N | B | NO |


### Errores y fallos no intencionados


| Amenaza | Dim | Valor | Degr. | Impacto | Frec. | Riesgo | ¿Tratar? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| E.2 Errores del admin. | D | A | 100% | A | N | A | SÍ |
| E.23 Errores mant. HW | D | A | 10% | M | N | M | SÍ |
| E.24 Agotamiento recursos | D | A | 10% | M | F | A | SÍ |
| E.25 Pérdida de equipos | D | A | 100% | A | PF | M | SÍ |


### Ataques intencionados


| Amenaza | Dim | Valor | Degr. | Impacto | Frec. | Riesgo | ¿Tratar? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| A.6 Abuso de privilegios | D | A | 100% | A | N | A | SÍ |
| A.7 Uso no previsto | D | A | 10% | M | N | M | SÍ |
| A.11 Acceso no autorizado | C | A | 100% | A | N | A | SÍ |
| A.23 Manipulación equipos | D | A | 10% | M | PF | B | NO |
| A.24 Denegación de servicio | D | A | 100% | A | N | A | SÍ |
| A.25 Robo | D | A | 100% | A | PF | M | SÍ |
| A.26 Ataque destructivo | D | A | 100% | A | PF | M | SÍ |


## 6.3 Mapa de riesgos


| Nivel | Amenazas | Total |
| :--- | :--- | :--- |
| MA | I.5 | 1 |
| A | I.6, E.2, E.24, A.6, A.11, A.24 | 6 |
| M | N.1, N.2, N.*, I.1, I.2, I.*, I.3, I.7, E.23, E.25, A.7, A.25, A.26 | 13 |
| B | I.4, I.11, A.23 (aceptados) | 3 |

Resultado: 20 amenazas requieren tratamiento (1 MA + 6 A + 13 M). Las 3 de nivel B se aceptan.


| Nota: El servidor tiene un perfil de riesgo más distribuido que las historias clínicas: muchas amenazas de nivel medio (industriales, ambientales) pero solo una MA. Los datos tenían 5 riesgos MA. Los activos de valor intrínseco alto generan riesgos más extremos. |
| :--- |


## 6.4 Tratamiento de riesgos


### Riesgo MA


| Amenaza | Salvaguarda propuesta | Tipo | Riesgo | Residual |
| :--- | :--- | :--- | :--- | :--- |
| I.5 Avería física/lóg. | Contrato mant. SLA 4h + servidor réplica standby | Correctiva | MA | B |


### Riesgos A


| Amenaza | Salvaguarda propuesta | Tipo | Riesgo | Residual |
| :--- | :--- | :--- | :--- | :--- |
| I.6 Corte eléctrico | SAI (UPS) + grupo electrógeno o CPD respaldo | Preventiva | A | B |
| E.2 Errores admin. | Procedimientos + entorno pruebas + control cambios | Preventiva | A | B |
| E.24 Agotam. recursos | Monitorización (Zabbix/Nagios) + alertas capacidad | Detectiva | A | B |
| A.6 Abuso privilegios | Cuentas nominales + mínimo privilegio + logs | Prev.+Det. | A | B |
| A.11 Acceso no autoriz. | Control acceso físico + acceso lógico + bastionado | Preventiva | A | B |
| A.24 Denegación serv. | Firewall + IDS/IPS + segmentación red | Prev.+Det. | A | B |


### Riesgos M


| Amenaza | Salvaguarda propuesta | Tipo | Riesgo | Residual |
| :--- | :--- | :--- | :--- | :--- |
| N.1/I.1 Fuego | Extintor + detector humos + backup offsite | Prev.+Corr. | M | B |
| N.2/I.2 Agua | Sala sin tuberías + sensores humedad + falso suelo | Preventiva | M | B |
| N.*/I.* Otros desastres | Backup offsite + plan continuidad negocio | Correctiva | M | B |
| I.3 Contam. mecánica | Filtros aire + limpieza periódica + rack cerrado | Preventiva | M | B |
| I.7 Temp./humedad | Climatización redundante + monitorización alertas | Prev.+Det. | M | B |
| E.23 Errores mant. HW | Ventanas mantenimiento + rollback disponible | Correctiva | M | B |
| E.25 Pérdida equipos | Inventario + seguros + servidor réplica | Prev.+Corr. | M | B |
| A.7 Uso no previsto | Política uso aceptable + monit. + hardening | Prev.+Det. | M | B |
| A.25 Robo | Control acceso físico + alarma + seguro | Preventiva | M | B |
| A.26 Ataque destructivo | Control acceso + vigilancia + plan continuidad | Prev.+Det. | M | B |


## 6.5 Riesgo residual


| Nivel | Amenazas | Total | Decisión |
| :--- | :--- | :--- | :--- |
| B | Todas las amenazas tratadas | 20 | Dentro del umbral aceptado |
| B | Amenazas ya aceptadas (I.4, I.11, A.23) | 3 | Ya estaban dentro del umbral |

Resultado: Todos los riesgos residuales están en nivel B. No quedan riesgos por encima del umbral. La dirección no necesita aceptar riesgos residuales adicionales para este activo.


| Nota: Comparado con las historias clínicas, donde quedaban 4 riesgos residuales en M, el servidor es más fácil de proteger hasta niveles aceptables. Los activos con valor intrínseco muy alto (datos de salud) son los más difíciles de llevar a niveles aceptables. |
| :--- |


# 7. Análisis del Activo 3: Administrador de Sistemas / TI

Activo: Administrador de Sistemas / Personal TI

Tipo MAGERIT: [P] Personas

Capa: 8 - Personal (interactúa con todas las capas)

Perfil: Acceso privilegiado (root) a servidor, base de datos, red, backups y configuraciones


## 7.1 Valoración del activo


| Dimensión | Valor | Justificación |
| :--- | :--- | :--- |
| Disponibilidad | A | Sin admin. TI no se puede mantener ni restaurar el sistema |
| Confidencialidad | MA | Tiene acceso privilegiado a todos los datos, incluidos los de salud |
| Integridad | A | Puede modificar configuraciones y datos de forma masiva |


| Nota: El administrador de TI es el perfil de personal más crítico porque tiene acceso total. Una amenaza sobre esta persona (extorsión, ingeniería social, indisponibilidad) puede tener un impacto en cascada sobre todos los activos del sistema. |
| :--- |


## 7.2 Amenazas, impacto y riesgo

Para el tipo de activo [P] Personas, el catálogo MAGERIT contempla las siguientes amenazas:


### Errores y fallos no intencionados


| Amenaza | Dim | Valor | Degr. | Impacto | Frec. | Riesgo | ¿Tratar? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| E.7 Defic. en la organización | D | A | 10% | M | N | M | SÍ |
| E.19 Fugas de información | C | MA | 10% | A | N | A | SÍ |
| E.28 Indisponib. del personal | D | A | 100% | A | N | A | SÍ |


### Ataques intencionados


| Amenaza | Dim | Valor | Degr. | Impacto | Frec. | Riesgo | ¿Tratar? |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| A.28 Indisponib. del personal | D | A | 100% | A | PF | M | SÍ |
| A.29 Extorsión | C | MA | 100% | MA | PF | A | SÍ |
| A.30 Ingeniería social | C | MA | 100% | MA | N | MA | SÍ |


## 7.3 Mapa de riesgos


| Nivel | Amenazas | Total |
| :--- | :--- | :--- |
| MA | A.30 Ingeniería social | 1 |
| A | E.19, E.28, A.29 | 3 |
| M | E.7, A.28 | 2 |

Resultado: Las 6 amenazas requieren tratamiento. Ninguna queda por debajo del umbral.


| Nota: A pesar de tener pocas amenazas (solo 6 frente a las 16 de los datos o las 23 del servidor), todas superan el umbral. Esto se debe a que el administrador TI tiene un valor muy alto (acceso privilegiado a datos de salud) y las amenazas sobre personas tienden a ser de alta degradación. |
| :--- |


## 7.4 Tratamiento de riesgos


### Riesgo MA


| Amenaza | Salvaguarda propuesta | Tipo | Riesgo | Residual |
| :--- | :--- | :--- | :--- | :--- |
| A.30 Ingeniería social | Formación periódica + simulacros phishing + protocolos verificación + filtro email | Preventiva | MA | M |


### Riesgos A


| Amenaza | Salvaguarda propuesta | Tipo | Riesgo | Residual |
| :--- | :--- | :--- | :--- | :--- |
| E.19 Fugas información | Acuerdo confidencialidad + política escritorio limpio + concienciación | Preventiva | A | B |
| E.28 Indisponib. accid. | Documentación procedimientos + admin. suplente formado + acceso de emergencia | Prev.+Corr. | A | B |
| A.29 Extorsión | Política de denuncia + segregación funciones + auditoría accesos privilegiados | Prev.+Det. | A | B |


### Riesgos M


| Amenaza | Salvaguarda propuesta | Tipo | Riesgo | Residual |
| :--- | :--- | :--- | :--- | :--- |
| E.7 Defic. organización | Definición clara de roles y responsabilidades + procedimientos documentados | Preventiva | M | B |
| A.28 Indisponib. delib. | Contrato con cláusulas + admin. suplente + proveedor externo de respaldo | Prev.+Corr. | M | B |


## 7.5 Riesgo residual


| Nivel | Amenazas | Total | Decisión |
| :--- | :--- | :--- | :--- |
| M | A.30 Ingeniería social | 1 | Dirección acepta formalmente |
| B | Resto de amenazas | 5 | Dentro del umbral aceptado |

Resultado: Queda 1 riesgo residual en nivel M (ingeniería social). La dirección lo acepta formalmente, comprometiéndose a mantener la formación y simulacros de forma continuada.


| Nota: La ingeniería social es una de las amenazas más difíciles de eliminar porque depende del factor humano. Por muy buena que sea la formación, siempre existe un riesgo residual. Esta es una amenaza que afecta especialmente al perfil de administrador TI por su nivel de acceso privilegiado. |
| :--- |


# 8. Resumen general y aceptación de riesgo residual

A continuación se presenta el resumen de riesgos residuales que quedan por encima del umbral (nivel M) tras aplicar todas las salvaguardas, y que requieren aceptación formal por la dirección:


| Activo | Amenaza residual | Nivel | Motivo de aceptación |
| :--- | :--- | :--- | :--- |
| Historias Clínicas [D] | E.1 Errores de usuario | M | Imposible eliminar error humano al 100% |
| Historias Clínicas [D] | A.11 Acceso no autorizado | M | Cifrado adicional no justifica coste |
| Historias Clínicas [D] | A.19 Divulgación información | M | DLP avanzado excede presupuesto |
| Historias Clínicas [D] | E.19 Fugas accidentales | M | Factor humano irreducible |
| Admin. TI [P] | A.30 Ingeniería social | M | Factor humano irreducible |

Servidor Principal [HW]: Todos los riesgos residuales en nivel B. No requiere aceptación adicional.


| DOCUMENTO DE ACEPTACIÓN DE RIESGO RESIDUAL La dirección de ClínicaSalud, tras revisar el análisis de riesgos realizado, acepta formalmente los 5 riesgos residuales de nivel Medio (M) detallados en la tabla anterior. Se compromete a:     - Revisar estos riesgos en un plazo máximo de 6 meses.     - Valorar inversiones adicionales si el contexto de amenazas evoluciona.     - Mantener la formación continuada del personal, especialmente del administrador TI.  Firma: ________________________________          Fecha: __ / __ / ____ Director/a General de ClínicaSalud |
| :--- |


| Nota: Este documento de aceptación es obligatorio. La dirección no puede ignorar los riesgos: o los trata o los acepta formalmente. Esto es un requisito tanto de MAGERIT como del Esquema Nacional de Seguridad (ENS) y del RGPD. |
| :--- |

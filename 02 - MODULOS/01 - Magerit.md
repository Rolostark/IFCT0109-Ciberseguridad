---
title: "MAGERIT — Metodología de Análisis y Gestión de Riesgos"
aliases:
  - Magerit
  - Gestion de Riesgos
  - Activos Magerit
tags:
  - magerit
  - gestion-de-riesgos
  - activos
  - ccn-cert
  - metodologia
---
# ⚖️ MAGERIT — Gestión de Riesgos de los Sistemas de Información

> [!abstract] Marco Metodológico
> **MAGERIT v3** (*Metodología de Análisis y Gestión de Riesgos de los Sistemas de Información*) es la metodología estándar elaborada por el Consejo Superior de Administración Electrónica (actualmente mantenida por el **CCN-CERT**) para el análisis de riesgos en las Administraciones Públicas y empresas de España.

---

## 🗂️ Taxonomía Oficial de Activos (Catálogo de Tipos)

Un **activo** es cualquier recurso del sistema de información o relacionado con él necesario para que la organización funcione correctamente y alcance sus objetivos.

---

### [D] Datos / Información
> Los datos son el corazón que permite a una organización prestar sus servicios. La información es un activo abstracto que se almacena en equipos o se transfiere por medios de transmisión.

* `[files]` — **Ficheros:** Archivos de datos o bases de datos estructuradas.
* `[backup]` — **Copias de respaldo:** Copias de seguridad de datos.
* `[conf]` — **Datos de configuración:** Críticos para la funcionalidad de los componentes y del sistema global.
* `[int]` — **Datos de gestión interna:** Parámetros operativos propios.
* `[password]` — **Credenciales:** Contraseñas y claves de usuarios.
* `[auth]` — **Datos de validación de credenciales:** Tablas de hashes, certificados de identidad.
* `[acl]` — **Datos de control de acceso:** Listas de control de accesos y permisos.
* `[log]` — **Registro de actividad:** Trazas para sustanciar requisitos de auditoría y no repudio.
* `[source]` — **Código fuente:** Repositorios de desarrollo con valor comercial.
* `[exe]` — **Código ejecutable:** Binarios y ejecutables en producción.
* `[test]` — **Datos de prueba:** Información empleada en entornos de testeo.

---

### [K] Claves Criptográficas
> Activos destinados a proteger el secreto, garantizar la integridad o autenticar a las partes.

* **Protección de la información:**
  * `[encrypt]` — Claves de cifra simétrica (`[shared_secret]`, ej. AES, 3-DES).
  * `[public_encryption]` — Clave pública de cifra (ej. RSA).
  * `[public_decryption]` — Clave privada de descifrado.
* **Firma digital:**
  * `[sign]` — Claves de firma (`[public_signature]` privada de firma, `[public_verification]` pública de verificación).
* **Protección de comunicaciones:**
  * `[channel]` — Claves de cifrado del canal de transmisión.
  * `[authentication]` — Claves para autenticación de equipos/servicios.
* **Soportes y certificados:**
  * `[x509]` — Certificados digitales de clave pública (X.509).

---

### [S] Servicios
> Funciones que satisfacen las necesidades de usuarios y procesos de negocio:

* `[anon]` — **Anónimo:** Servicios prestados sin requerir identificación previa del usuario.
* `[pub]` — **Al público general:** Sin relación contractual formal (ej. portal web informativo).
* `[ext]` — **A usuarios externos:** Bajo relación contractual (ej. banca online, clientes).
* `[int]` — **Interno:** Servicios dirigidos exclusivamente a empleados de la propia organización.

---

### [SW] Software — Aplicaciones Informáticas
> Programas automatizados que gestionan, procesan y transforman los datos:

* `[prp]` — Desarrollo propio (*in house*).
* `[sub]` — Desarrollo a medida subcontratado.
* `[std]` — Software estándar comercial (*off the shelf*).
* `[browser]` — Navegadores web.
* `[www]` — Servidores de presentación web (Apache, Nginx, IIS).
* `[app]` — Servidores de aplicaciones (Tomcat, Node, .NET).
* `[email_server]` / `[email_client]` — Servidores y clientes de correo electrónico.
* `[file]` — Servidores de ficheros (NFS, SMB).
* `[dbms]` — Sistemas gestores de bases de datos (PostgreSQL, MySQL, Oracle).
* `[os]` — Sistemas operativos (Windows, Linux, macOS).
* `[hypervisor]` — Gestores de virtualización (VMware ESXi, Proxmox, KVM).
* `[av]` — Antivirus / EDR.
* `[backup]` — Software de gestión de copias de seguridad.

---

### [HW] Equipamiento Informático (Hardware)
> Medios materiales físicos que soportan las aplicaciones y almacenan los datos:

* `[host]` — Grandes equipos / Servidores centrales (*Mainframes*).
* `[mid]` — Servidores de gama media.
* `[pc]` — Informática personal de escritorio.
* `[mobile]` — Informática móvil (portátiles, smartphones).
* `[vhost]` — Servidores virtuales.
* `[peripheral]` — Medios de impresión `[print]` y escáneres `[scan]`.
* `[crypto]` — Dispositivos criptográficos físicos (HSM, tokens USB).
* **Equipos de red y frontera `[bp]`:**
  * `[switch]` — Conmutadores de red.
  * `[router]` — Encaminadores de red.
  * `[firewall]` — Cortafuegos y dispositivos UTM.
  * `[wap]` — Puntos de acceso inalámbrico Wi-Fi.
  * `[pabx]` — Centralitas telefónicas y teléfonos IP `[ipphone]`.

---

### [COM] Redes de Comunicaciones
> Medios de transporte que trasladan los datos de un lugar a otro:

* `[LAN]` — Red de área local cableada.
* `[wifi]` — Red inalámbrica local.
* `[MAN]` / `[Internet]` — Red metropolitana y salida a Internet.
* `[pp]` — Enlaces dedicados punto a punto.
* `[PSTN]` / `[ISDN]` / `[ADSL]` — Redes de telefonía y datos fijas.
* `[mobile]` — Comunicaciones por telefonía móvil (4G/5G).
* `[sat]` — Enlaces satelitales.

---

### [Media] Soportes de Información
> Dispositivos que almacenan la información de forma temporal o permanente:

* **Soportes Electrónicos:**
  * `[disk]` — Discos duros magnéticos y SSDs.
  * `[san]` — Almacenamiento en red (Storage Area Network / NAS).
  * `[usb]` — Memorias flash USB / Pendrives.
  * `[mc]` / `[ic]` — Tarjetas de memoria y tarjetas inteligentes (*Smartcards*).
  * `[tape]` — Cintas magnéticas para almacenamiento masivo.
* **Soportes No Electrónicos:**
  * `[printed]` — Documentación impresa en papel.
  * `[film]` — Microfilms y microfichas.

---

### [AUX] Equipamiento Auxiliar
> Elementos que asisten y protegen el entorno físico del sistema:

* `[power]` / `[ups]` — Fuentes de alimentación y Sistemas de Alimentación Ininterrumpida (SAI).
* `[gen]` — Grupos electrógenos y generadores diésel.
* `[ac]` — Equipos de refrigeración y climatización.
* `[cabling]` / `[wire]` / `[fiber]` — Cableado eléctrico y fibra óptica.
* `[destroy]` — Equipos de trituración / destrucción certificada de soportes.
* `[furniture]` / `[safe]` — Armarios rack y cajas fuertes ignífugas.

---

### [L] Instalaciones
> Emplazamientos físicos donde se ubican los sistemas y las personas:

* `[site]` — Recinto o complejo empresarial.
* `[building]` — Edificio de oficinas.
* `[local]` — Centro de Procesamiento de Datos (CPD) o cuarto técnico.
* `[mobile]` — Vehículos y plataformas móviles (coche, avión, buque).
* `[channel]` — Canalizaciones de comunicaciones.
* `[backup]` — Centro de respaldo alternativo (*Disaster Recovery Site*).

---

### [P] Personal
> Recursos humanos vinculados con los sistemas de información:

* `[ui]` — Usuarios internos (empleados).
* `[ue]` — Usuarios externos (clientes, ciudadanos).
* `[adm]` — Administradores de sistemas.
* `[sec]` — Administradores y responsables de seguridad (CISO).
* `[com]` — Administradores de red y comunicaciones.
* `[dba]` — Administradores de bases de datos.
* `[des]` — Desarrolladores y analistas de software.
* `[op]` — Operadores de sistemas.
* `[sub]` / `[prov]` — Proveedores y personal externo subcontratado.

---

## 📥 Recursos y Plantillas de Descarga

> [!example] Documentos Prácticos
> * [Descargar Caso Práctico: Simulacro Clínica (.docx)]([[02 - MODULOS/MAGERIT - Simulacro Clínica|Simulacro Clínica (Markdown)]] · [Descargar .docx original](../archivos_descargados/documentos/simulacro.docx)) — Caso real de empresa para aplicar la metodología.
> * [Descargar Plantilla Excel de Análisis de Riesgos (.xlsx)]([[02 - MODULOS/MAGERIT - Matriz de Análisis de Riesgos|Matriz de Riesgos (Markdown)]] · [Descargar .xlsx original](../archivos_descargados/documentos/riesgos.xlsx)) — Matriz de evaluación de impacto y probabilidad.

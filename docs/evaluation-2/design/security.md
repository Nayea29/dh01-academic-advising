# DH-01 — Seguridad y Privacidad

**Driver principal:** AD-02 — Confidentiality and Controlled Access  
**Decisiones relacionadas:** ADR-004 y ADR-005  
**Escenario principal:** QS-01  
**Estado:** Diseño de seguridad documentado; prototipo HTML interactivo disponible; controles de backend pendientes de implementación y validación

## 1. Propósito, activos y amenazas

Este documento define los mecanismos propuestos para proteger las identidades, las operaciones y la información sensible del Academic Advising Case Management System (DH-01).

El diseño responde a FR-14, QR-01, QR-02, BR-07 y BR-08. La autorización dependerá del rol institucional, la relación vigente del usuario con el caso y la sensibilidad de los recursos solicitados.

Conforme a ADR-004, la autenticación utilizará identidades ficticias administradas internamente mediante sesiones opacas de servidor.

La propuesta busca establecer controles verificables para una futura implementación integrada, sin asumir conexión con un proveedor institucional real de identidad.

La Evaluación 2 incluye un [prototipo HTML interactivo](../../../prototype/index.html) que permite explorar interfaces diferenciadas, roles y restricciones simuladas utilizando datos sintéticos.

**El prototipo no implementa autenticación real, sesiones de servidor, autorización contextual en una API ni transacciones PostgreSQL.** Los mecanismos de seguridad descritos a continuación corresponden a la arquitectura propuesta.

### 1.1. Activos y límites de confianza

| Activo o límite | Riesgo | Control propuesto |
|---|---|---|
| Credenciales y sesiones | Suplantación o robo de sesión. | Hash seguro, expiración, cookies protegidas y revocación. |
| Casos e identificadores | Acceso a registros ajenos mediante manipulación de identificadores. | Autorización por recurso en cada lectura y escritura. |
| Notas internas | Exposición mediante respuestas demasiado amplias. | Recursos separados y respuestas filtradas por audiencia. |
| Asignaciones | Uso de permisos después de una reasignación. | Verificación de la relación vigente y revalidación transaccional. |
| C-WEB y solicitudes | Manipulación de roles, campos o estados. | Validación y determinación de identidad desde el servidor. |
| Reportes | Divulgación mediante filtros y recuentos. | Restricción de alcance antes de filtrar, agregar y paginar. |
| C-DB y respaldos | Acceso técnico excesivo. | Conexiones restringidas, privilegio mínimo y accesos separados. |
| X-SIS | Respuestas inválidas o indisponibilidad. | Adaptador, validación, timeout y manejo controlado de errores. |

El navegador no se considerará un entorno confiable para aplicar controles definitivos de seguridad.

PostgreSQL no estará expuesto directamente a los clientes web.

Las respuestas del SIS simulado se tratarán como entradas externas, aunque contengan exclusivamente datos sintéticos.

Estas medidas deberán verificarse posteriormente mediante una implementación del backend y pruebas reproducibles.

### 1.2. Modelo de amenazas de la implementación propuesta

El análisis contempla usuarios legítimos que intentan operaciones fuera de sus permisos, clientes que manipulan solicitudes HTTP, errores de implementación y accesos técnicos con privilegios excesivos.

El uso de datos sintéticos reduce la exposición de información real, pero no elimina la necesidad de aplicar y comprobar controles de seguridad.

Las principales superficies de entrada son:

| Superficie | Entrada controlable | Verificación |
|---|---|---|
| Login | Usuario, contraseña, origen y frecuencia. | Credenciales, limitación de intentos y respuestas genéricas. |
| API de casos | Identificadores, cuerpos y versión. | Esquema, propiedad, estado y relación vigente. |
| Listas y reportes | Filtros, ordenamiento y paginación. | Campos permitidos y alcance autorizado. |
| Sesión | Cookie y token CSRF. | Validez, expiración, revocación y vinculación a sesión. |
| Renderizado web | Solicitudes, notas y recomendaciones. | Tratamiento como texto y salida segura. |
| X-SIS | Respuestas, códigos y tiempos de espera. | Esquema, identidad sintética y timeout. |
| Configuración | Variables, conexiones y certificados. | Separación de secretos y controles de acceso. |

La seguridad deberá verificarse en el backend incluso cuando `C-WEB` oculte determinadas funcionalidades.

El prototipo HTML representa algunas restricciones a nivel local. Estas demostraciones no permiten verificar los controles de una API frente a solicitudes directas o manipuladas.

## 2. Autenticación, sesiones y protección de solicitudes

### 2.1. Credenciales

Se propone utilizar **Argon2id** mediante una biblioteca mantenida para almacenar hashes de contraseñas.

Los parámetros de configuración se seleccionarán según las capacidades del entorno y deberán documentarse antes de las pruebas.

Cada contraseña tendrá una sal gestionada por la biblioteca.

No se almacenarán contraseñas en texto plano ni se incluirán en logs, respuestas de API o archivos de evidencia.

Las cuentas ficticias se crearán mediante un procedimiento controlado de preparación de datos.

La implementación inicial propuesta no contempla registro público de usuarios ni recuperación de contraseñas por correo electrónico.

Los errores de autenticación utilizarán mensajes genéricos para dificultar la enumeración de cuentas.

**Estado actual:** el selector de perfiles del prototipo HTML es un mecanismo de demostración y no constituye autenticación mediante credenciales reales o sintéticas verificadas por un servidor.

### 2.2. Mecanismo de sesión

DH-01 utilizará sesiones opacas administradas por el servidor y persistidas mediante la tabla técnica `SESSION` en PostgreSQL.

El navegador recibirá un identificador aleatorio mediante una cookie segura. La base de datos conservará una representación criptográficamente protegida de ese identificador (`tokenDigest`), junto con los datos necesarios para administrar su vigencia.

| Parámetro | Propuesta inicial | Estado |
|---|---|---|
| Cookie | `HttpOnly`, `Secure`, `SameSite=Lax` y alcance restringido. | Configuración propuesta. |
| Expiración por inactividad | 30 minutos. | Valor técnico inicial. |
| Duración absoluta | Máximo de 8 horas desde la autenticación. | Valor técnico inicial. |
| Login exitoso | Crear un nuevo identificador de sesión para prevenir fijación. | Pendiente de validación. |
| Logout | Revocar la sesión y eliminar la cookie. | Pendiente de validación. |
| Cuenta desactivada | Rechazar sesiones asociadas en solicitudes posteriores. | Pendiente de validación. |
| Protección de login | Límite inicial de 5 intentos fallidos por cuenta y origen durante 15 minutos. | Pendiente de calibración, sin bloqueo indefinido. |

Los valores de expiración y limitación de intentos son propuestas técnicas, no políticas institucionales confirmadas.

En entornos desplegados y accesibles mediante red se utilizará HTTPS.

Las posibles excepciones para desarrollo local mediante loopback deberán documentarse y no trasladarse indiscriminadamente a otros entornos.

El almacenamiento `localStorage` del prototipo HTML no representa la tabla `SESSION` ni el mecanismo de sesión diseñado.

### 2.3. Ciclo de vida de la sesión

**Creación.** Después de verificar las credenciales, el servidor generará un identificador aleatorio nuevo. No se reutilizará una sesión previa para convertirla en una sesión autenticada.

La cookie contendrá el identificador opaco y la base de datos conservará su `tokenDigest`, evitando almacenar directamente el secreto reutilizable.

**Uso.** Cada solicitud protegida comprobará que:

- La sesión existe.
- No ha sido revocada.
- No ha superado la expiración por inactividad.
- No ha superado su duración absoluta.
- La cuenta asociada permanece activa.

Los roles, permisos y relaciones sensibles se obtendrán del estado vigente del servidor.

La actualización de `lastActivityAt` y `expiresAt` deberá respetar la política de inactividad sin superar `absoluteExpiresAt`.

**Expiración.** La expiración por inactividad y la expiración absoluta se evaluarán independientemente. Una cookie conservada por el navegador no permitirá acceder después de cualquiera de estos límites.

La eliminación física de sesiones expiradas será una tarea de mantenimiento. La autorización no dependerá de que esa limpieza haya ocurrido.

**Cierre y revocación.** El logout revocará la sesión del servidor y eliminará la cookie. Un identificador revocado no podrá utilizarse nuevamente.

La desactivación de cuentas o la modificación de credenciales podrá requerir revocación de sesiones asociadas, conforme al procedimiento administrativo que se defina.

**Concurrencia de sesiones.** El número de sesiones simultáneas por usuario no constituye una política institucional confirmada.

La implementación deberá establecer explícitamente si permite varias sesiones y qué alcance tiene cada operación de logout.

No se afirmará que existe cierre global de sesiones cuando el mecanismo solo invalide la sesión actual.

### 2.4. Protección CSRF y origen

Las solicitudes autenticadas que modifiquen información deberán cumplir protección CSRF.

Se propone utilizar un token asociado a la sesión, enviado desde `C-WEB` mediante el encabezado:

`X-CSRF-Token`

La consulta de sesión proporcionará el token CSRF sin exponer el identificador secreto de sesión.

`C-API` deberá comprobar el token y el origen permitido antes de procesar escrituras protegidas.

El inicio de sesión también verificará origen y tipo de contenido para evitar solicitudes inducidas desde sitios externos.

La configuración CORS estará restringida a los orígenes autorizados. No se combinarán credenciales con un origen comodín.

`SameSite=Lax` será una protección complementaria y no sustituirá la validación CSRF del servidor.

El identificador de sesión no se almacenará en `localStorage` ni se devolverá dentro de respuestas JSON.

**Esta sección describe el comportamiento de la futura arquitectura.** El prototipo HTML no dispone del backend ni de las sesiones necesarias para demostrar protección CSRF real.

## 3. Autorización contextual, permisos y delegaciones

### 3.1. Matriz propuesta de permisos

| Operación | Student | Academic Adviser | Program Coordinator | Advising Coordinator | IT Services |
|---|---|---|---|---|---|
| Crear solicitud propia | Sí, para sí mismo. | No en flujo inicial. | Referencia mediante operación separada. | No en flujo inicial. | No. |
| Consultar caso | Propio, con representación limitada. | Asignación vigente. | Programa autorizado, información limitada. | Ámbito de coordinación autorizado. | Sin contenido académico por defecto. |
| Realizar triage y asignar | No. | No en flujo inicial. | No por defecto. | Sí, dentro de su ámbito. | No. |
| Reasignar | No. | Puede solicitarlo; no ejecutar por defecto. | No. | Sí, con motivo. | No. |
| Registrar seguimiento y orientación | No. | Caso asignado y estado válido. | No. | Sin facultad automática de atención. | No. |
| Responder información solicitada | Caso propio y solicitud abierta. | No en nombre del estudiante. | No por defecto. | No por defecto. | No. |
| Consultar notas internas | Nunca. | Caso asignado y permiso correspondiente. | No por defecto. | Solo autorización especial validada. | No por función técnica. |
| Resolver | No. | Caso asignado y condiciones cumplidas. | No. | Sin facultad automática de atención. | No. |
| Reportes operacionales | No. | Su propia carga de trabajo. | Información limitada de su programa. | Supervisión de su ámbito. | Salud técnica, sin contenido académico. |
| Configurar cuentas de prueba | No. | No. | No. | No por defecto. | Permiso técnico explícito. |

La matriz representa una propuesta conservadora de autorización.

Los roles institucionales no concederán acceso ilimitado a los recursos del sistema.

Las delegaciones, permisos excepcionales y ámbitos de acceso requerirán validación institucional.

El prototipo HTML utiliza esta separación como referencia para representar las pantallas y acciones de los distintos perfiles. No constituye una implementación de autorización real.

### 3.2. Secuencia de decisión

Cada solicitud protegida seguirá una secuencia de autorización:

1. Verificar sesión e identidad activa.
2. Identificar la operación solicitada.
3. Comprobar el rol y los permisos explícitos.
4. Recuperar el recurso y su relación vigente con el actor.
5. Evaluar propiedad, asignación o ámbito institucional.
6. Verificar sensibilidad de los datos y estado del caso.
7. Para escrituras críticas, repetir las comprobaciones relevantes después de bloquear el caso.
8. Ejecutar la operación permitida y construir una respuesta con únicamente los campos autorizados.

Se aplicará el principio de **denegación por defecto**.

Los permisos enviados por `C-WEB` no constituirán una fuente de autoridad.

Los recursos anidados deberán validarse contra el caso al que pertenecen.

Después de una reasignación, el acceso se calculará utilizando la nueva responsabilidad vigente.

### 3.3. Administración de permisos y delegaciones

La delegación de triage o asignación requerirá definir:

- Actor autorizado para delegar.
- Usuario destinatario.
- Operaciones delegadas.
- Alcance institucional.
- Vigencia.
- Mecanismo de revocación.

Estas capacidades no forman parte automáticamente del alcance inicial propuesto.

Hasta definir e implementar un mecanismo institucionalmente aprobado, únicamente el Advising Coordinator autorizado podrá iniciar triage y realizar la clasificación y asignación inicial.

La revocación deberá impedir autorizaciones posteriores, sin confiar en permisos almacenados previamente en el navegador.

Las operaciones concurrentes con una revocación requerirán definir un punto verificable de autorización.

Para la relación de asignación, se utilizará la comprobación transaccional después de bloquear el caso.

La revocación inmediata de delegaciones durante transacciones en curso requerirá un diseño adicional si esta funcionalidad se incorpora posteriormente.

### 3.4. Separación técnica y académica

IT Services podrá administrar aspectos técnicos del sistema y cuentas ficticias autorizadas sin recibir permisos ordinarios para consultar el contenido de los casos.

La administración directa de PostgreSQL implica capacidades técnicas distintas de las autorizaciones aplicadas por la API.

Por esta razón, la matriz de permisos de DH-01 no representa una garantía absoluta frente a administradores de base de datos con privilegios elevados.

Los accesos administrativos, credenciales privilegiadas y respaldos deberán contar con controles independientes.

## 4. Confidencialidad, clasificación y protección de datos

### 4.1. Clasificación de la información

| Categoría | Ejemplos | Audiencia autorizada |
|---|---|---|
| Técnica pública mínima | Estado básico de liveness. | Consumidores permitidos por configuración de red. |
| General del caso | Estado, categoría y fechas. | Usuarios con propiedad, asignación o ámbito autorizado. |
| Dirigida al estudiante | Recomendaciones y actividades `STUDENT`. | Estudiante propietario y personal autorizado. |
| Interna de asesoría | Notas y actividades `INTERNAL`. | Academic Adviser vigente y permisos especiales aprobados. |
| Seguridad y operación | Sesiones, intentos de acceso y errores. | Personal técnico expresamente autorizado. |
| Secreta | Contraseñas, tokens y credenciales. | Procesos y administradores que estrictamente lo requieran. |

La clasificación de la información será una propiedad aplicada por el backend y no solamente una decisión visual de la interfaz.

Una respuesta JSON que contenga información no autorizada constituirá una exposición, aunque React no muestre esos campos.

Por ello, ocultar determinadas secciones en el prototipo HTML es una ilustración de las políticas previstas, pero no demuestra que los datos sensibles estén protegidos frente a solicitudes directas.

### 4.2. Separación de información y minimización

`InternalNote` y `Recommendation` se mantendrán como recursos separados.

Las respuestas destinadas a estudiantes incluirán únicamente información permitida por la política correspondiente.

La misma regla se aplicará a:

- Listados de casos.
- Detalles individuales.
- Actividades de seguimiento.
- Recomendaciones.
- Historial.
- Reportes.
- Respuestas de error.

Los reportes operacionales no necesitarán recuperar el contenido completo de las notas ni los detalles confidenciales de los casos.

Los datos utilizados para demostraciones serán sintéticos y reconocibles como información ficticia.

No se copiarán expedientes académicos reales.

El historial visible para estudiantes podrá incluir hitos generales autorizados, pero no eventos que revelen la existencia o el contenido de notas internas.

### 4.3. Validación de entradas y presentación

Las interfaces definirán los campos aceptados, sus tipos, longitudes y valores permitidos.

El backend rechazará campos que intenten imponer identidades, roles o permisos desde el cliente.

Las validaciones de formato se ejecutarán antes de las operaciones de negocio, mientras que las políticas de autorización y las invariantes se verificarán sobre recursos vigentes.

Un cuerpo JSON correctamente formado no garantiza que la operación sea autorizada.

Las consultas utilizarán parámetros para evitar concatenaciones inseguras de SQL.

Los campos de ordenamiento se seleccionarán mediante listas explícitas de valores permitidos.

Las solicitudes, notas y recomendaciones se presentarán como texto. Una futura incorporación de contenido enriquecido requerirá controles adicionales de sanitización.

Los mensajes de error evitarán exponer información interna o valores secretos.

Los límites de tamaño de cuerpos, paginación y frecuencia de autenticación ayudarán a controlar el consumo de recursos, pero no sustituirán la autorización.

### 4.4. Privacidad, respaldos y retención

Los eventos de negocio conservarán información mínima para la trazabilidad, como actor, caso, operación, fecha y recurso afectado.

No se duplicarán innecesariamente contraseñas, tokens ni texto confidencial de notas.

Los accesos denegados podrán registrarse como eventos técnicos de seguridad, diferenciados de las acciones de negocio confirmadas.

Las conexiones ordinarias a PostgreSQL deberán utilizar privilegios mínimos.

Las migraciones y operaciones administrativas podrán utilizar credenciales diferenciadas cuando corresponda.

Los secretos permanecerán fuera del repositorio. Los archivos `.env` deberán excluirse mediante `.gitignore`, sin considerar esta medida suficiente para demostrar que no existen secretos en el historial de Git.

Los respaldos de prueba tendrán acceso restringido y no deberán publicarse en el repositorio.

Las políticas institucionales de retención, anonimización y eliminación deberán definirse posteriormente. No se establece un plazo normativo sin una fuente institucional que lo respalde.

El prototipo HTML utiliza exclusivamente información sintética y almacenamiento local, sin representar el mecanismo definitivo de respaldos y retención.

## 5. Auditoría de seguridad y manejo de fallos

### 5.1. Auditoría técnica y de negocio

DH-01 distinguirá los eventos de negocio del registro técnico de seguridad y diagnóstico.

| Registro | Finalidad | Información mínima |
|---|---|---|
| Evento de negocio | Reconstruir operaciones confirmadas. | Caso, actor, evento, fecha y recurso. |
| Acceso denegado | Analizar operaciones no autorizadas. | Actor cuando se conozca, operación, resultado y requestId. |
| Autenticación | Diagnosticar login y revocación. | Resultado, referencia técnica y fecha. |
| Error de infraestructura | Investigar fallos. | Tipo de error y correlación, sin secretos. |

Los eventos obligatorios de negocio deberán registrarse dentro de la misma transacción que confirma las modificaciones relacionadas.

`AuditEvent.caseSequence` permitirá reconstruir el orden de los eventos dentro de cada caso.

Los logs técnicos no sustituirán eventos como `CASE_REASSIGNED` o `CASE_RESOLVED`.

La auditoría de negocio tampoco se utilizará para almacenar indiscriminadamente errores, solicitudes HTTP o información confidencial.

La lectura de una nota no generará automáticamente un evento de negocio.

Si una política futura exige auditar consultas de información sensible, deberán definirse alcance, volumen y retención de esos registros.

El historial mostrado en el prototipo HTML es una simulación local y no constituye evidencia de auditoría persistente o transaccional.

### 5.2. Fallos y respuestas

| Situación | Comportamiento esperado |
|---|---|
| Sesión ausente o inválida | 401 y ninguna operación protegida. |
| Caso fuera del alcance | 404 sin confirmar existencia innecesariamente. |
| Operación denegada sobre caso conocido | 403 sin escrituras ni exposición de notas. |
| Versión o estado incompatible | 409 sin modificaciones parciales. |
| Token CSRF inválido o ausente | Rechazo de seguridad sin escritura. |
| Auditoría obligatoria indisponible | Rollback de toda la operación de negocio. |
| Fallo técnico | Mensaje genérico y requestId, con diagnóstico seguro separado. |

Los registros de auditoría de negocio no serán modificables mediante la API ordinaria.

Esta restricción no implica resistencia absoluta frente a administradores de PostgreSQL con privilegios elevados, limitación reconocida en ADR-005.

Los mensajes y conflictos simulados por el prototipo no son respuestas HTTP provenientes de C-API y no demuestran el cumplimiento de estos contratos.

## 6. Seguridad del transporte, despliegue e integraciones

### 6.1. Punto de entrada y conexiones

En los entornos desplegados, los navegadores accederán mediante HTTPS.

Cuando un reverse proxy termine el cifrado TLS, el backend deberá reconocer correctamente el esquema externo mediante una configuración explícita de proxies confiables.

No se confiará indiscriminadamente en encabezados enviados por cualquier cliente.

Una configuración incorrecta del proxy podría afectar cookies `Secure`, controles de origen y registros técnicos.

PostgreSQL aceptará únicamente conexiones necesarias y autorizadas.

Los puertos internos de Node.js y del simulador X-SIS no necesitarán exposición pública para un entorno de demostración integrado.

Las conexiones internas se protegerán según su exposición y características de red.

La separación lógica de los nodos no implicará automáticamente aislamiento físico.

**Alcance actual:** el prototipo HTML puede abrirse localmente en un navegador. Esto no constituye un despliegue seguro de la arquitectura C-WEB, C-API y C-DB.

### 6.2. Integración X-SIS

CMP-10 — SIS Integration Adapter validará las respuestas del servicio simulado.

Las verificaciones incluirán:

- Estructura de la respuesta.
- Identificador sintético del estudiante.
- Valores y catálogos esperados.
- Códigos de error.
- Tiempos de espera.

Las respuestas inválidas se tratarán conforme a los contratos de interfaces.

No se inventará información académica cuando el simulador falle.

Las consultas a X-SIS se realizarán fuera de las transacciones que mantienen bloqueos prolongados sobre casos.

El mock no tendrá acceso directo a C-DB.

Una futura conexión con el SIS institucional requerirá revisar autenticación, autorización, privacidad y protección de datos.

X-SIS se mantiene como un servicio HTTP propuesto y no se encuentra implementado como parte del prototipo HTML actual.

### 6.3. Secretos y configuración

Los parámetros de configuración deberán mantenerse separados del código fuente.

Cuando exista implementación se proporcionará un ejemplo de configuración sin valores secretos.

Las credenciales reales no se incluirán en capturas, comandos de evidencia, archivos de prueba ni registros públicos.

Antes de publicar resultados se revisarán:

- Cookies y encabezados.
- Cadenas de conexión.
- Variables de entorno.
- Mensajes de error.
- Identificadores de sesión.
- Contenido confidencial de registros.

La sanitización deberá conservar la información necesaria para reproducir las pruebas sin exponer secretos.

## 7. Escenarios de abuso y validación

### 7.1. Escenarios de abuso

Los siguientes escenarios permitirán evaluar los mecanismos de seguridad propuestos.

| ID | Intento | Resultado esperado | Prueba relacionada |
|---|---|---|---|
| AB-01 | Student sustituye `caseId` por el de otro estudiante. | Sin acceso a datos ajenos ni confirmación innecesaria de existencia. | T-SEC-01 |
| AB-02 | Student consulta directamente `/notes`. | Denegación incluso en un caso propio. | T-SEC-01 |
| AB-03 | Adviser conserva una pantalla después de reasignación. | Escritura rechazada al perder asignación vigente. | T-SEC-01, T-AUD-01 |
| AB-04 | Cliente envía `authorId` de otro usuario. | Campo rechazado; actor derivado de sesión. | T-API-01 |
| AB-05 | Program Coordinator manipula `programId` en filtros. | Ámbito no ampliado. | T-SEC-01 |
| AB-06 | Un tercero induce una escritura desde otro origen. | Verificación de origen y token CSRF. | T-SEC-02 |
| AB-07 | Cliente introduce contenido HTML activo. | Sin ejecución arbitraria del contenido. | T-API-01 |
| AB-08 | Usuario solicita una página excesivamente grande. | Aplicación de límites y respuesta controlada. | T-API-01 |
| AB-09 | Fallo de X-SIS produce datos improvisados. | Error explícito sin información inventada. | T-SIS-01 |
| AB-10 | Usuario técnico intenta consultar contenido de asesoría. | Denegación sin permiso académico explícito. | T-SEC-01 |

Los identificadores `AB` identifican escenarios de abuso y no representan nuevos requisitos institucionales.

El prototipo HTML puede ilustrar determinadas denegaciones visuales o conflictos, pero estos escenarios requieren pruebas mediante solicitudes directas a los servicios implementados.

### 7.2. Matriz de comprobaciones negativas

| Actor | Operación negativa | Resultado esperado |
|---|---|---|
| Student propietario | Leer notas internas. | No recibe contenido confidencial. |
| Student ajeno | Consultar o responder información de otro caso. | Ninguna lectura ni escritura. |
| Adviser no asignado | Crear nota o resolver. | Sin modificaciones confirmadas. |
| Adviser anterior | Escribir después de reasignación. | Operación rechazada. |
| Program Coordinator | Leer notas por pertenecer al programa. | Acceso denegado. |
| IT Services | Consultar contenido académico mediante funciones técnicas. | Sin acceso ordinario. |
| Sesión expirada | Ejecutar escritura con token CSRF antiguo. | Ninguna operación de negocio. |
| Cliente con campos manipulados | Imponer roles o identidad. | Rechazo de entrada. |

También se ejecutarán pruebas positivas para verificar que los usuarios autorizados puedan realizar las operaciones permitidas.

No se considerará seguro un sistema que simplemente rechace todas las solicitudes.

### 7.3. Trazabilidad

| Control | Referencias | Verificación prevista |
|---|---|---|
| Sesión, expiración y revocación | FR-14, QR-01, ADR-004 | T-AUTH-01: rechazar credenciales y sesiones inválidas. |
| Rol, relación y confidencialidad | FR-12, FR-14, QR-01, QR-02, BR-07, BR-08, QS-01 | T-SEC-01: denegar o filtrar los intentos no autorizados. |
| CSRF, validación y recursos anidados | QR-01, AD-02, ADR-004 | T-SEC-02, T-API-01: rechazar solicitudes inválidas sin cambios. |
| Permisos posteriores a reasignación | FR-09, FR-14, BR-08 | T-SEC-01, T-AUD-01: impedir acceso del responsable anterior. |
| Auditoría mínima y atómica | QR-03, BR-06, ADR-005, QS-03 | Verificar historial consistente y sin datos confidenciales innecesarios. |
| Secretos y aislamiento | C-03, C-04, C-08, AD-07 | Comprobar configuración y accesos restringidos. |

Las pruebas se desarrollan en el [Plan de Validación](../validation/test-plan.md).

Los parámetros de hash, la configuración TLS, las bibliotecas de seguridad y los permisos excepcionales deberán definirse y comprobarse antes de declarar conformidad con los escenarios de calidad.

**Estado actual:** las pruebas técnicas relacionadas con estos controles permanecen en `NOT EXECUTED`. La existencia del prototipo HTML no sustituye estas verificaciones.

## 8. Respuesta a incidentes, riesgos y decisiones abiertas

### 8.1. Respuesta a una falla observada

Si una prueba identifica exposición de información o una escritura no autorizada, se registrará una incidencia con evidencia sanitizada, recursos afectados y criterio incumplido.

El procedimiento propuesto será:

1. Identificar la operación, el contrato y el permiso afectados.
2. Conservar evidencia suficiente sin ampliar la exposición.
3. Limitar temporalmente el flujo vulnerable del entorno de prueba cuando sea necesario.
4. Corregir el control correspondiente en el backend.
5. Revisar otras operaciones que puedan compartir la misma vulnerabilidad.
6. Ejecutar nuevamente la prueba fallida y las verificaciones de regresión pertinentes.
7. Registrar la corrección y su resultado conservando el historial de la incidencia.

Este procedimiento corresponde a una futura implementación de DH-01 y no constituye un plan institucional completo de respuesta a incidentes.

Si durante la demostración del prototipo se identifica una inconsistencia visual o funcional, deberá documentarse como una observación del prototipo y no confundirse automáticamente con una vulnerabilidad comprobada de C-API, que todavía no está implementada.

### 8.2. Riesgos residuales

Persisten riesgos relacionados con:

- Privilegios administrativos sobre PostgreSQL.
- Configuraciones incorrectas de infraestructura.
- Dependencias sin versiones o parámetros definitivos.
- Políticas de retención pendientes de aprobación.
- Errores de implementación no detectados por pruebas.
- Falta de integración con un proveedor institucional real de identidad.
- Confusión entre las restricciones simuladas del prototipo y los mecanismos de seguridad de servidor.

La autenticación interna constituye una propuesta viable para una implementación de prueba independiente, pero no demuestra equivalencia con la infraestructura de identidad de Horizon University.

Cualquier integración futura deberá revisar las identidades, sesiones y correspondencias de usuarios, manteniendo las políticas de autorización contextual.

La aceptación técnica requerirá evidencia de los controles implementados. La aceptación institucional deberá confirmar por separado las políticas, permisos excepcionales y condiciones de conservación de datos.

### 8.3. Decisiones pendientes

Antes de implementar y validar definitivamente los mecanismos de seguridad será necesario precisar:

- Parámetros de Argon2id y procedimiento de gestión de cuentas ficticias.
- Mantenimiento de la tabla técnica `SESSION`.
- Duración, renovación, revocación y concurrencia de sesiones.
- Configuración definitiva de cookies, CSRF y orígenes autorizados.
- Mecanismos de delegación y revocación de permisos de triage.
- Autorizaciones excepcionales de acceso a notas e historial.
- Políticas institucionales de retención y eliminación.
- Seguridad de respaldos y accesos administrativos.
- Configuración de infraestructura y protección de secretos.
- Procedimientos de recuperación y respuesta a incidentes.

Las decisiones que afecten permisos, contratos de interfaces o persistencia deberán actualizarse de forma coherente en los documentos correspondientes.

**Documentos relacionados:**

- [Domain and Data Model](domain-and-data.md)
- [Lifecycle and Runtime](lifecycle-and-runtime.md)
- [API Contracts](interfaces.md)
- [Architecture Decision Records](../adr/README.md)
- [Validation Plan](../validation/test-plan.md)
- [Validation Findings](../validation/findings.md)
- [Project Status](../project-status.md)
- [Prototipo interactivo y limitaciones](../../../prototype/README.md)

Este documento establece el diseño propuesto de autenticación, autorización contextual, confidencialidad, protección de sesiones y auditoría para DH-01.

La Evaluación 2 incluye un prototipo HTML que representa roles y flujos académicos, pero no implementa los mecanismos de seguridad de servidor definidos en esta propuesta.

**La conformidad técnica de DH-01 dependerá de ejecutar las pruebas correspondientes sobre una implementación integrada, mientras que la aceptación institucional requerirá confirmar las políticas pendientes. Las 18 pruebas técnicas del plan permanecen en estado `NOT EXECUTED`.**
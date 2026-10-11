# DH-01 — Plan de Validación Arquitectónica y Funcional

**Cobertura:** FR-01 a FR-14, QR-01 a QR-06 y QS-01 a QS-07  
**Estado:** Plan documentado; prototipo HTML interactivo disponible; 18 pruebas técnicas no ejecutadas  
**Alcance:** Revisión arquitectónica, demostración interactiva y estrategia de validación de una futura implementación

## 1. Propósito, alcance y estrategia

Este documento define la estrategia, los procedimientos y los criterios de aceptación propuestos para validar la arquitectura y el comportamiento del Academic Advising Case Management System (DH-01).

El plan responde a los requisitos funcionales, reglas de negocio, restricciones, decisiones arquitectónicas y escenarios de calidad documentados para la Evaluación 2.

La validación prioriza los mecanismos que podrían comprometer la confidencialidad, la integridad de los casos, la consistencia transaccional y la trazabilidad de las operaciones.

También contempla pruebas de funcionalidades, interfaces, rendimiento, modificabilidad, usabilidad, recuperación y despliegue.

**La entrega actual incluye una propuesta de arquitectura y diseño, acompañada de un prototipo HTML interactivo de demostración ubicado en `prototype/index.html`.**

El prototipo utiliza datos sintéticos, lógica ejecutada en el navegador y almacenamiento mediante `localStorage`. Permite explorar roles, estados y flujos académicos, pero no implementa la arquitectura integrada React, Node.js/Express y PostgreSQL.

En consecuencia, se distinguen cuatro elementos:

- **Revisión documental:** evaluación de la coherencia entre requisitos, decisiones, contratos y vistas arquitectónicas.
- **Prototipo interactivo:** demostración local de interfaces, roles y comportamientos simulados.
- **Validación técnica planificada:** procedimientos que requieren una implementación del backend, persistencia e infraestructura.
- **Resultados observados:** información obtenida y respaldada mediante ejecuciones verificables.

La existencia del prototipo no constituye evidencia de que los mecanismos técnicos de seguridad, concurrencia, auditoría o recuperación funcionen en la arquitectura propuesta.

Los resultados esperados tampoco se consideran resultados obtenidos.

El estado de ejecución de cada prueba se documenta en [Validation Findings](findings.md).

### 1.1. Estrategia de validación

La estrategia contempla diferentes niveles de evaluación.

| Nivel | Objetivo | Actividades previstas | Momento |
|---|---|---|---|
| Revisión documental | Evaluar coherencia de requisitos, decisiones y vistas. | Revisar identificadores, responsabilidades, reglas, estados y supuestos. | Evaluación 2 |
| Demostración interactiva | Ilustrar el comportamiento esperado del sistema. | Explorar roles, formularios, estados, restricciones y seguimiento. | Evaluación 2 |
| Pruebas unitarias | Verificar reglas de dominio y autorización. | Transiciones, resolución, audiencias y validaciones. | Implementación futura |
| Integración | Evaluar la colaboración entre componentes y PostgreSQL. | Transacciones, restricciones, auditoría y concurrencia. | Implementación futura |
| API | Comprobar contratos y seguridad mediante solicitudes directas. | Autenticación, permisos, errores y versiones. | Implementación futura |
| Flujo completo | Verificar operaciones desde C-WEB hasta la persistencia. | Creación, triage, asignación, atención y resolución. | Implementación futura |
| Calidad y operación | Evaluar QS-04 a QS-07. | Rendimiento, modificabilidad, usabilidad y recuperación. | Implementación futura |

La revisión documental y la exploración del prototipo pueden realizarse sin implementar la arquitectura completa.

En cambio, las pruebas técnicas, transaccionales y de integración necesitan código ejecutable, servicios identificables y un entorno reproducible.

La evaluación dará prioridad a la seguridad, integridad y consistencia antes de realizar mediciones de rendimiento.

Las herramientas específicas de pruebas se seleccionarán cuando se implemente el entorno técnico. No se afirma que actualmente exista un framework de pruebas instalado.

### 1.2. Prioridades

Las pruebas se organizan según el impacto potencial de una falla.

| Prioridad | Criterio |
|---|---|
| Crítica | Mecanismos cuya falla podría comprometer confidencialidad, integridad o auditoría obligatoria. |
| Alta | Funcionalidades principales, contratos, restricciones relacionales y decisiones estructurales. |
| Media | Calidad operacional, rendimiento, usabilidad y recuperación bajo condiciones definidas. |

Esta clasificación permitirá priorizar una futura implementación y seleccionar verificaciones de mayor valor.

La prioridad de una prueba no implica que haya sido ejecutada.

## 2. Entorno, datos y preparación de la validación

### 2.1. Entorno propuesto

El entorno técnico de pruebas utilizaría la arquitectura documentada en las vistas y decisiones del proyecto.

| Identificador | Tecnología propuesta | Función |
|---|---|---|
| C-WEB | React | Interfaz web integrada. |
| C-API | Node.js + Express | Backend y reglas de negocio. |
| C-DB | PostgreSQL | Persistencia relacional. |
| X-SIS | Servicio HTTP mock | Simulación de integración académica. |

La configuración deberá seguir la [Vista de Despliegue](../views/deployment-view.md) y los [Supuestos de Despliegue](../deployment-assumptions.md).

Para ejecutar las pruebas será necesario documentar las versiones de software, los recursos utilizados, las migraciones, las semillas sintéticas, la configuración no secreta y la revisión de código.

El entorno deberá permitir:

1. Iniciar C-WEB y C-API de forma reproducible.
2. Aplicar migraciones y cargar datos sintéticos.
3. Consultar registros persistidos en PostgreSQL.
4. Controlar respuestas, errores y demoras de X-SIS.
5. Ejecutar operaciones concurrentes mediante conexiones independientes.
6. Introducir fallos controlados.
7. Registrar resultados y conservar evidencia sanitizada.

Las pruebas de concurrencia y rollback deberán utilizar PostgreSQL real dentro del entorno de evaluación.

Los mocks de repositorios no permitirían demostrar por sí solos bloqueos transaccionales ni atomicidad real.

Las restauraciones deberán realizarse exclusivamente sobre bases de datos sintéticas aisladas.

**Estado actual:** el entorno técnico descrito es una propuesta y no se encuentra implementado. El prototipo HTML funciona localmente en el navegador, sin C-API, PostgreSQL ni X-SIS desplegados.

### 2.2. Fixtures sintéticos

Se proponen los siguientes conjuntos de datos para futuras pruebas.

| Fixture | Finalidad |
|---|---|
| Students S-A y S-B de diferentes programas | Evaluar propiedad y acceso a casos ajenos. |
| Advisers A y B y un adviser inactivo | Evaluar asignación, reasignación y elegibilidad. |
| Program Coordinators de diferentes programas | Comprobar referencias y consultas por ámbito. |
| Advising Coordinator autorizado | Ejecutar triage, clasificación y asignación. |
| IT Services sin permisos académicos | Comprobar separación entre funciones técnicas y contenido. |
| Casos en los siete estados | Verificar transiciones y permisos. |
| InternalNotes, Recommendations y FollowUps de ambas audiencias | Evaluar confidencialidad y seguimiento. |
| Sesiones válidas, expiradas y revocadas | Evaluar autenticación y revocación. |
| X-SIS con éxito, 404, error, respuesta inválida y timeout | Evaluar integración y manejo de fallos. |

Las semillas deberán ser reproducibles y contener únicamente información ficticia.

El prototipo actual utiliza datos sintéticos para representar escenarios de demostración. Dichos datos no sustituyen las semillas relacionales que deberán utilizarse durante las pruebas de PostgreSQL.

El flujo inicial no permitirá delegación activa del triage. Una delegación futura requerirá autorización institucional y mecanismos explícitos de concesión y revocación.

Se mantendrán conjuntos diferenciados para pruebas funcionales, seguridad, usabilidad y rendimiento.

Cada prueba deberá indicar si necesita una base limpia o una secuencia previa de operaciones.

### 2.3. Línea base y condiciones de entrada

Antes de una campaña técnica deberá identificarse una revisión concreta del código y de la documentación.

Los resultados se asociarán con la versión evaluada.

| Precondición | Comprobación | Consecuencia si falta |
|---|---|---|
| Código ejecutable | C-WEB y C-API pueden iniciarse. | Las pruebas integradas no podrán comenzar. |
| PostgreSQL preparado | Migraciones y semillas aplicadas. | No iniciar pruebas de persistencia. |
| Usuarios sintéticos | Roles y relaciones definidos. | Corregir fixtures. |
| Sesiones disponibles | Autenticación y persistencia configuradas. | Pruebas de sesión pendientes. |
| Contratos definidos | Rutas y versiones coinciden con el diseño. | Registrar desviaciones. |
| Entorno aislado | Datos y servicios identificados como prueba. | No iniciar restauraciones o inyección de fallos. |
| Registro de evidencia | RUN-ID y mecanismo de almacenamiento disponibles. | No declarar aprobaciones sin trazabilidad. |

Una precondición ausente antes de intentar una prueba no constituye automáticamente una ejecución bloqueada.

El estado `BLOCKED` se reservará para intentos iniciados y documentados que no puedan continuar.

El funcionamiento local del prototipo HTML no satisface por sí mismo estas condiciones de entrada para las pruebas del backend.

## 3. Catálogo y trazabilidad de pruebas

El catálogo contiene **18 pruebas técnicas previstas**, relacionadas con requisitos funcionales, escenarios de calidad y decisiones arquitectónicas.

| ID | Objetivo | Prioridad | Referencias |
|---|---|---|---|
| T-SEC-01 | Confidencialidad y autorización contextual. | Crítica | QS-01, AD-02, ADR-004 |
| T-SEC-02 | Protección CSRF y recursos anidados. | Crítica | QR-01, AD-02, ADR-004 |
| T-LIFE-01 | Resolución sin acciones pendientes. | Crítica | QS-02, AD-01, AD-04, ADR-003 |
| T-LIFE-02 | Concurrencia entre seguimiento y resolución. | Crítica | QS-02, ADR-003 |
| T-AUD-01 | Historial conservado después de reasignar. | Alta | QS-03, AD-03, ADR-005 |
| T-PERF-01 | Consultas operacionales bajo carga. | Media | QS-04, AD-06 |
| T-MOD-01 | Impacto de un cambio de política. | Alta | QS-05, AD-05, ADR-001 |
| T-UX-01 | Comprensión del estado y siguiente acción. | Media | QS-06, AD-06 |
| T-REC-01 | Reinicio sin pérdida de datos. | Media | QS-07, AD-07 |
| T-FUNC-01 | Flujo completo y referencia autorizada. | Alta | FR-01, FR-02, FR-06, FR-07, FR-08, FR-12 |
| T-TRIAGE-01 | Clasificación y asignación válidas. | Alta | FR-03, FR-04, BR-01, BR-02, BR-03 |
| T-DATA-01 | Integridad relacional y asignación única. | Alta | ADR-002, FR-04, FR-08 |
| T-TXN-01 | Rollback ante fallo de auditoría. | Crítica | ADR-003, ADR-005, QS-02, QS-03 |
| T-AUTH-01 | Sesiones, expiración y revocación. | Crítica | FR-14, QR-01, ADR-004 |
| T-API-01 | Contratos HTTP, validación y versiones. | Alta | FR-14, QR-01, ADR-003, ADR-004 |
| T-SIS-01 | Integración sintética y manejo de fallos. | Media | C-05, C-06, AD-07 |
| T-REC-02 | Respaldo y restauración. | Media | ADR-002, AD-07 |
| T-DEP-01 | Reproducibilidad y aislamiento. | Alta | C-03, C-04, C-05, C-08, AD-07 |

Las referencias permiten relacionar cada prueba con una necesidad arquitectónica.

Ninguno de estos identificadores representa una prueba actualmente aprobada.

### 3.1. Correspondencia con los escenarios de calidad

| Escenario | Prueba principal | Evidencia esperada |
|---|---|---|
| QS-01 — Confidencialidad | T-SEC-01 | Accesos permitidos y denegados. |
| QS-02 — Integridad | T-LIFE-01, T-LIFE-02 | Estados, pendientes, versiones y concurrencia. |
| QS-03 — Auditoría | T-AUD-01, T-TXN-01 | Historial, eventos y rollback. |
| QS-04 — Rendimiento | T-PERF-01 | Latencias, errores, carga y configuración. |
| QS-05 — Modificabilidad | T-MOD-01 | Cambios controlados y regresión. |
| QS-06 — Usabilidad | T-UX-01 | Respuestas y observaciones de participantes. |
| QS-07 — Recuperación | T-REC-01 | Tiempo de reinicio y conservación de datos. |

La correspondencia completa con los requisitos se encuentra en la [Matriz de Trazabilidad](../traceability.md).

### 3.2. Relación del prototipo con el plan

El prototipo HTML permite ilustrar algunos comportamientos que posteriormente deberán verificarse mediante las pruebas técnicas.

| Comportamiento demostrado | Pruebas relacionadas | Limitación |
|---|---|---|
| Vistas diferenciadas por rol. | T-SEC-01, T-SEC-02 | No existe autenticación ni autorización de servidor. |
| Transiciones y resolución de casos. | T-LIFE-01 | No existen transacciones PostgreSQL. |
| Conflictos de versión simulados. | T-LIFE-02 | No se demuestra concurrencia real. |
| Historial de operaciones. | T-AUD-01 | La auditoría es local, no transaccional en servidor. |
| Formularios y flujos de casos. | T-FUNC-01, T-TRIAGE-01 | No existe integración C-WEB → C-API → C-DB. |
| Interfaces y navegación. | T-UX-01 | No se ha ejecutado la evaluación formal con cinco participantes. |

Esta relación permite utilizar el prototipo como apoyo para explicar la arquitectura sin atribuirle resultados de las pruebas.

## 4. Procedimientos de los escenarios de calidad

Los procedimientos siguientes especifican acciones y criterios de aceptación para los escenarios prioritarios.

Su ejecución técnica dependerá de una implementación posterior.

### 4.1. T-SEC-01 — Confidencialidad

**Preparación:** crear casos propios y ajenos, notas internas y cuentas de los cinco roles, sin concesiones especiales implícitas.

**Procedimiento:**

1. Consultar un caso propio como Student y revisar los campos recibidos.
2. Intentar acceder a notas internas mediante solicitudes directas.
3. Manipular `caseId` e identificadores anidados para consultar recursos de otros casos.
4. Repetir con un adviser no asignado, un Program Coordinator fuera de ámbito e IT Services.
5. Comprobar los accesos positivos correspondientes a usuarios autorizados.
6. Reasignar un caso y repetir consultas y escrituras con el adviser anterior y el nuevo.
7. Revisar listas, reportes, historial y errores para detectar posibles filtraciones.

**Criterio de aceptación:** el 100 % de los intentos definidos en la matriz deberá ser permitido, denegado o filtrado correctamente según la política aplicable.

Ninguna respuesta destinada al Student deberá contener `InternalNote`.

Las escrituras rechazadas no deberán modificar datos.

**Evidencia esperada:** matriz de actor, recurso y operación; solicitudes y respuestas sanitizadas; comparación del estado persistido.

La ocultación de notas internas en el prototipo únicamente ilustra el comportamiento previsto. No constituye una validación de autorización contextual del backend.

### 4.2. T-SEC-02 — CSRF y recursos anidados

**Preparación:** contar con sesiones válidas, cookies opacas, tokens CSRF y recursos asociados a casos diferentes.

**Procedimiento:**

1. Realizar una escritura legítima con cookie y token válidos.
2. Repetir la operación sin `X-CSRF-Token`.
3. Enviar un token inválido o perteneciente a otra sesión.
4. Realizar la solicitud desde un origen no permitido.
5. Manipular `caseId` y `followUpId` para referenciar un recurso de otro caso.
6. Repetir con una `InformationRequest` ajena.
7. Comprobar que las solicitudes rechazadas no modificaron información ni generaron eventos de éxito.

**Criterio de aceptación:** los intentos que incumplan CSRF, origen o pertenencia deberán rechazarse sin efectos persistentes.

Las solicitudes válidas deberán funcionar cuando cumplan autorización y reglas de negocio.

**Evidencia esperada:** solicitudes HTTP sanitizadas, códigos de respuesta, versiones y consultas de integridad.

### 4.3. T-LIFE-01 — Condiciones de resolución

**Procedimiento:**

1. Crear, clasificar y asignar un caso; iniciar su atención.
2. Registrar una `Recommendation` y un `FollowUp` en estado `PENDING`.
3. Intentar resolver y comprobar el rechazo sin cambios.
4. Completar el seguimiento y comprobar el retorno a `IN_PROGRESS`.
5. Resolver correctamente y verificar `RESOLVED`, `resolvedAt` y `CASE_RESOLVED`.
6. Repetir sin `Recommendation` y con una `InformationRequest` en estado `OPEN`.
7. Intentar resolver desde un estado inválido y como adviser no asignado.

**Criterio de aceptación:** deberán rechazarse los cierres con pendientes, sin recomendación, desde estados inválidos o sin autorización.

El cierre válido deberá confirmar conjuntamente el estado del caso y el evento obligatorio de auditoría.

BR-05 se aplicará a todos los FollowUps `PENDING` del flujo inicial, sin diferenciar entre actividades obligatorias y opcionales.

El prototipo permite presentar ejemplos de resolución y restricciones, pero no permite comprobar el compromiso transaccional de los registros.

### 4.4. T-LIFE-02 — Concurrencia

Esta prueba evaluará la consistencia entre una operación de creación de seguimiento y una resolución que compiten sobre el mismo caso.

Se utilizarán conexiones independientes y mecanismos de sincronización capaces de generar concurrencia real.

**Preparación:** caso `IN_PROGRESS`, con `Recommendation` registrada y sin pendientes. Ambas solicitudes partirán de la misma versión.

**Procedimiento:**

1. Ejecutar simultáneamente una creación de FollowUp y una resolución.
2. Repetir forzando que la creación obtenga primero el bloqueo.
3. Repetir forzando que la resolución obtenga primero el bloqueo.
4. Registrar versiones, respuestas, tiempos y estado persistido.
5. Ejecutar variantes con `expectedVersion` desactualizado.
6. Comprobar que los eventos de auditoría corresponden únicamente a operaciones confirmadas.

**Criterio de aceptación:** no deberá existir un caso `RESOLVED` con un FollowUp pendiente válidamente confirmado.

Las operaciones incompatibles deberán rechazarse mediante conflicto, sin pérdida silenciosa de cambios.

**Evidencia esperada:** barreras de sincronización, solicitudes y respuestas, versiones, seguimiento e historial persistidos.

Deberá indicarse el número de repeticiones realizadas.

Una ejecución secuencial no será suficiente para demostrar el comportamiento concurrente.

La simulación de conflictos que incorpora el prototipo no equivale a ejecutar esta prueba.

### 4.5. T-AUD-01 — Reasignación e historial

**Procedimiento:**

1. Crear un caso y asignarlo al Adviser A.
2. Registrar una nota interna y un seguimiento.
3. Reasignarlo al Adviser B con un motivo.
4. Consultar asignación vigente e historial.
5. Verificar eventos, actores, fechas, responsables y motivos.
6. Comprobar la conservación de notas y seguimientos.
7. Verificar que `NOTE_CREATED` registra metadatos sin copiar contenido confidencial.
8. Comprobar el orden de los eventos mediante `caseSequence`.

**Criterio de aceptación:** todas las reasignaciones válidas incluidas en la prueba deberán conservar el historial exigido por QS-03.

Existirá una sola asignación vigente y la autorización deberá corresponder al nuevo responsable.

### 4.6. T-PERF-01 — Rendimiento

QS-04 propone una carga de **12.000 casos sintéticos y 20 solicitudes concurrentes durante cinco minutos**.

Se documentarán distribución de casos, filtros, paginación, recursos asignados y condiciones de caché.

**Procedimiento:**

1. Preparar 12.000 casos con distribución representativa.
2. Ejecutar consultas filtradas y paginadas bajo la carga prevista.
3. Separar el calentamiento del período de medición.
4. Registrar solicitudes totales, tiempos de servidor, percentil 95 y errores.
5. Comparar los resultados con los criterios de QS-04.

**Criterio de aceptación:** al menos el 95 % de las respuestas del servidor deberán completarse en menos de **800 ms**, sin errores de consulta bajo las condiciones previstas.

Se distinguirá el tiempo de procesamiento del servidor de la latencia total percibida por el cliente.

Los resultados corresponderán únicamente al entorno y a las operaciones evaluadas.

### 4.7. T-MOD-01 — Evolución de política

**Procedimiento:**

1. Seleccionar una categoría o parámetro de seguimiento configurable.
2. Modificar un valor permitido sin introducir una política institucional no aprobada.
3. Ejecutar las pruebas del componente afectado.
4. Ejecutar las pruebas de regresión relacionadas.
5. Revisar archivos y dependencias modificados.
6. Conservar el diff y los resultados antes y después del cambio.

**Criterio de aceptación:** el cambio no deberá requerir modificaciones en componentes independientes de autenticación, sesiones o auditoría.

Las pruebas relacionadas deberán aprobarse.

La mantenibilidad no se evaluará únicamente contando módulos o carpetas, sino observando el impacto de una modificación controlada.

### 4.8. T-UX-01 — Comprensión del caso

Se propone una evaluación formativa con cinco participantes y tareas basadas en casos sintéticos.

**Procedimiento:**

1. Presentar casos en diferentes estados.
2. Solicitar la identificación del estado y de la siguiente acción esperada.
3. Registrar respuestas sin ayuda.
4. Identificar dificultades de terminología y navegación.
5. Clasificar cada respuesta como correcta sin ayuda, correcta con ayuda o incorrecta.

**Criterio de aceptación:** al menos **4 de 5 participantes** deberán identificar correctamente el estado y la siguiente acción correspondiente.

Se documentarán perfiles, instrucciones y condiciones de evaluación.

La muestra no permitirá generalizar automáticamente los resultados a todos los usuarios de Horizon University.

El prototipo HTML podrá utilizarse como material de interacción para esta evaluación si se aplica el procedimiento y se registran las respuestas. Su disponibilidad actual no implica que T-UX-01 haya sido ejecutada.

### 4.9. T-REC-01 — Reinicio controlado

**Procedimiento:**

1. Persistir casos, asignaciones, seguimientos y eventos conocidos.
2. Detener únicamente `C-API`, manteniendo PostgreSQL disponible.
3. Reiniciar el backend y medir el tiempo hasta recuperar una respuesta funcional.
4. Consultar los registros confirmados previamente.
5. Verificar relaciones e historial.
6. Comprobar liveness, readiness y una consulta autorizada.

**Criterio de aceptación:** recuperar la capacidad funcional dentro de **60 segundos** después del reinicio, sin pérdida de datos confirmados.

PostgreSQL permanecerá disponible durante toda la prueba.

Esta verificación no demostrará alta disponibilidad ni recuperación ante pérdida física de la base de datos.

La conservación de información del prototipo mediante `localStorage` no sustituye esta prueba.

## 5. Pruebas funcionales, contratos e integridad

### 5.1. Procedimientos complementarios

Además de los escenarios anteriores, se prevén pruebas específicas sobre requisitos funcionales, contratos e infraestructura.

| ID | Procedimiento | Criterio observable |
|---|---|---|
| T-FUNC-01 | Ejecutar el flujo completo desde C-WEB hasta C-DB. | Estados, datos y respuestas consistentes. |
| T-TRIAGE-01 | Intentar asignaciones inválidas y una válida. | Rechazos y asignación atómica. |
| T-DATA-01 | Intentar relaciones inexistentes y asignaciones duplicadas. | Restricciones relacionales efectivas. |
| T-TXN-01 | Inyectar fallos antes de auditoría obligatoria. | Rollback completo. |
| T-AUTH-01 | Probar login, expiración, logout y cuenta desactivada. | Sesiones válidas permitidas e inválidas rechazadas. |
| T-API-01 | Enviar campos desconocidos, versiones obsoletas y filtros inválidos. | Contratos y errores correctos. |
| T-SIS-01 | Simular respuestas, errores, timeout y esquemas inválidos. | Fallos de integración controlados. |
| T-REC-02 | Respaldar y restaurar información sintética. | Datos y relaciones recuperados. |
| T-DEP-01 | Reproducir instalación y comprobar aislamiento. | Entorno reproducible y configuración segura. |

Las inyecciones de fallos se realizarán únicamente dentro de un entorno de pruebas aislado.

### 5.2. T-FUNC-01 — Intake y referencia

| Subcaso | Acción | Resultado esperado |
|---|---|---|
| FUNC-A | Student registra solicitud propia. | Caso `NEW`, origen `STUDENT_REQUEST` y actor correcto. |
| FUNC-B | Student intenta imponer otro `studentId`. | Solicitud rechazada. |
| FUNC-C | Program Coordinator refiere a un estudiante dentro de su ámbito. | Caso `NEW`, origen `PROGRAM_REFERRAL`. |
| FUNC-D | Program Coordinator refiere a un estudiante fuera de su ámbito. | Rechazo sin crear caso ni evento de éxito. |
| FUNC-E | Ocurre una falla antes de la auditoría de creación. | Rollback del caso. |

Se comprobará el registro del origen, el actor que realizó la operación y el estudiante destinatario.

Una confirmación visual no será suficiente sin verificar el estado persistido.

### 5.3. T-TRIAGE-01 — Clasificación y asignación

| Subcaso | Precondición | Acción | Resultado esperado |
|---|---|---|---|
| TRI-A | `NEW` | Intentar asignar. | Rechazo por estado. |
| TRI-B | `NEW` | Iniciar triage autorizado. | `UNDER_TRIAGE` y evento. |
| TRI-C | `UNDER_TRIAGE` | Seleccionar categoría inactiva. | Rechazo sin asignación. |
| TRI-D | `UNDER_TRIAGE` | Seleccionar adviser inactivo. | Rechazo sin cambios parciales. |
| TRI-E | `UNDER_TRIAGE` | Clasificar y asignar datos válidos. | `ASSIGNED`, clasificación, responsable y auditoría. |
| TRI-F | `ASSIGNED` | Repetir asignación inicial. | Conflicto; una asignación vigente. |
| TRI-G | `NEW` | Adviser intenta iniciar triage sin delegación. | Acceso denegado. |

Una categoría seleccionada inicialmente por el usuario no equivale a una clasificación formal.

El caso positivo de triage será ejecutado por un Advising Coordinator autorizado.

### 5.4. Atención e información adicional

Este procedimiento complementará T-FUNC-01:

1. Iniciar atención como adviser vigente y verificar `IN_PROGRESS`.
2. Solicitar información y comprobar `WAITING_FOR_INFORMATION`.
3. Consultar la solicitud como Student sin exponer notas internas.
4. Responder como propietario y comprobar `ANSWERED`.
5. Verificar el retorno a `IN_PROGRESS` o `FOLLOW_UP`, según los pendientes.
6. Registrar una `Recommendation` y comprobar su visibilidad autorizada.
7. Registrar una `InternalNote` y verificar que permanece restringida.

También deberán intentarse dos operaciones inválidas:

- Responder una InformationRequest perteneciente a otro caso.
- Responder una InformationRequest que ya está `ANSWERED`.

Ambos intentos deberán rechazarse sin cambios persistentes.

### 5.5. Seguimiento por audiencia

Se crearán actividades FollowUp con audiencia `INTERNAL` y `STUDENT` en un mismo caso.

El adviser vigente podrá gestionarlas conforme a las reglas autorizadas.

El Student solamente visualizará las actividades con audiencia `STUDENT`.

La finalización de un seguimiento no eliminará los demás registros.

Cuando se complete el último FollowUp pendiente y el caso esté en `FOLLOW_UP`, podrá regresar a `IN_PROGRESS`.

Durante `WAITING_FOR_INFORMATION`, completar un seguimiento no deberá eliminar la condición de espera si continúa existiendo una solicitud de información abierta.

La declaración de una actividad por parte del estudiante no sustituirá automáticamente la confirmación requerida al adviser vigente.

### 5.6. Restricciones de datos y sesiones

T-DATA-01 comprobará:

- Claves primarias y foráneas.
- Unicidad de asignación vigente.
- Unicidad de clasificación vigente.
- Una sola InformationRequest `OPEN` por caso.
- Unicidad de `(caseId, caseSequence)` en `AuditEvent`.
- Pertenencia de recursos a sus casos.
- Coherencia de estados y fechas de FollowUp.

T-AUTH-01 verificará la persistencia técnica de sesiones.

Se comprobará que `tokenDigest` sea único y que el token secreto no se almacene directamente en formato reutilizable.

Las sesiones expiradas, revocadas o vinculadas a cuentas inactivas no deberán permitir operaciones.

## 6. Seguridad, atomicidad y concurrencia

### 6.1. Matriz ampliada de seguridad

| Subcaso | Actor y recurso | Solicitud | Resultado esperado |
|---|---|---|---|
| SEC-A | Student propietario | Consultar su caso. | Campos generales y audiencia autorizada. |
| SEC-B | Student propietario | Consultar notas internas. | 403 sin contenido. |
| SEC-C | Student ajeno | Consultar otro caso. | 404 sin revelar existencia. |
| SEC-D | Adviser vigente | Crear nota en caso asignado. | Permitido según política. |
| SEC-E | Adviser no asignado | Crear nota. | Denegación sin escritura. |
| SEC-F | Adviser anterior | Registrar seguimiento tras reasignación. | Denegación por relación vigente. |
| SEC-G | Program Coordinator | Consultar casos de su programa. | Resultados dentro del ámbito. |
| SEC-H | Program Coordinator | Filtrar otro programa. | Sin ampliación de acceso. |
| SEC-I | IT Services | Consultar contenido académico. | Acceso denegado por función técnica. |
| SEC-J | Coordinador sin permiso de notas | Consultar notas internas. | Denegación. |

Las verificaciones deberán ejecutarse mediante solicitudes directas a la API y, cuando corresponda, desde la interfaz.

Se revisarán filtraciones indirectas por errores, filtros, recuentos, historial y campos de respuesta.

Los totales de las listas no deberán incluir casos fuera del ámbito autorizado.

### 6.2. Sesión y CSRF

T-AUTH-01 contemplará:

- Inicio de sesión válido.
- Contraseña incorrecta.
- Cuenta desconocida.
- Cookie alterada.
- Expiración por inactividad.
- Expiración absoluta.
- Acceso después de logout.
- Cuenta desactivada.
- Persistencia y revocación de sesiones en PostgreSQL.
- Ausencia de secretos de sesión en JSON y logs.

T-SEC-02 comprobará las escrituras sin token CSRF, con tokens de otra sesión y desde orígenes no autorizados.

También se ejecutarán casos positivos para comprobar que las sesiones válidas permiten las operaciones autorizadas.

Los parámetros se evaluarán conforme a [Security Design](../design/security.md).

### 6.3. Puntos de fallo controlados

| Operación | Punto de fallo | Registros que deben permanecer sin cambios |
|---|---|---|
| Creación | Antes de insertar `CASE_CREATED`. | Caso e historial. |
| Asignación | Después de escribir la asignación y antes de auditar. | Clasificación, asignación, estado y versión. |
| Reasignación | Después de finalizar la asignación anterior. | Asignación anterior y ausencia de nueva asignación parcial. |
| Nota | Antes de `NOTE_CREATED`. | Nota, versión y evento. |
| Resolución | Antes de `CASE_RESOLVED`. | Estado, resolvedAt, versión e historial. |

El fallo deberá producirse dentro de la transacción correspondiente.

Eliminar manualmente un evento después del COMMIT no constituye una prueba válida de rollback.

T-TXN-01 deberá demostrar que la operación completa falla si no puede registrarse un cambio obligatorio.

### 6.4. Barreras de sincronización

Las pruebas concurrentes deberán registrar los momentos en que cada conexión adquiere el bloqueo y realiza COMMIT o ROLLBACK.

Las operaciones deberán superponerse realmente.

T-LIFE-02 cubrirá ambos órdenes de adquisición del bloqueo entre creación de seguimiento y resolución.

Podrán considerarse casos complementarios:

- Dos reasignaciones simultáneas.
- Reasignación frente a escritura del adviser anterior.
- Doble finalización de un seguimiento.
- Respuesta de información simultánea con otras modificaciones.

La evaluación comprobará versiones, permisos vigentes, estado persistido y eventos confirmados.

## 7. Mediciones de calidad y recuperación

### 7.1. Diseño de la medición de rendimiento

Se utilizarán 12.000 casos sintéticos distribuidos por estados, programas, categorías y advisers.

El conjunto deberá incluir suficientes registros coincidentes con los filtros para evitar mediciones artificialmente favorables.

Las 20 solicitudes concurrentes deberán generarse mediante emisores simultáneos o una configuración equivalente y documentada.

El período medido será de cinco minutos, separado del calentamiento previo.

| Métrica | Interpretación |
|---|---|
| Solicitudes enviadas | Volumen total durante el período. |
| Solicitudes correctas | Respuestas conformes al contrato. |
| Errores | Respuestas inesperadas o fallos. |
| Latencia de cliente | Tiempo completo observado por el emisor. |
| Tiempo del servidor | Duración medida dentro de C-API. |
| Percentil 95 | Latencia correspondiente al 95 % de las muestras. |
| Proporción bajo 800 ms | Verificación directa del objetivo QS-04. |

El resultado deberá analizar el período completo sin excluir silenciosamente solicitudes fallidas.

Se documentará el método de cálculo de percentiles.

Si únicamente se dispone de mediciones del cliente, se declarará esa limitación.

Los resultados corresponderán exclusivamente a los endpoints y condiciones evaluados.

### 7.2. Diseño de la evaluación de usabilidad

| Perfil | Tarea | Respuesta esperada |
|---|---|---|
| Student | Consultar caso en espera de información. | Identificar que debe responder una solicitud. |
| Student | Consultar caso en seguimiento. | Reconocer el estado y la acción STUDENT. |
| Adviser | Consultar caso asignado. | Identificar responsable y pendientes. |
| Adviser | Intentar resolver con pendientes. | Comprender el rechazo y la siguiente acción. |
| Usuario autorizado | Revisar caso `RESOLVED`. | Reconocer que la atención finalizó. |

Se documentará la composición de los cinco participantes.

El evaluador utilizará instrucciones consistentes y registrará las respuestas antes de proporcionar ayuda.

Las respuestas se clasificarán como:

- Correcta sin ayuda.
- Correcta con ayuda.
- Incorrecta.

El criterio de QS-06 establece que al menos cuatro de cinco participantes identifiquen correctamente el estado y la siguiente acción.

Los resultados se interpretarán dentro del alcance formativo definido.

### 7.3. Reinicio del proceso

T-REC-01 deberá identificar el servicio reiniciado y las condiciones del entorno.

Reiniciar `C-API` no implicará recrear PostgreSQL ni eliminar el almacenamiento persistente.

Se verificarán:

- Disponibilidad del proceso.
- Liveness.
- Readiness.
- Consulta funcional autorizada.
- Conservación de casos.
- Conservación de asignaciones e historial.

El objetivo será recuperar la capacidad funcional en un máximo de 60 segundos desde el reinicio.

Una respuesta de liveness sin acceso operativo a PostgreSQL no será suficiente para demostrar recuperación funcional.

### 7.4. Restauración de respaldo

T-REC-02 deberá registrar:

- Archivo de respaldo.
- Herramienta y versión.
- Fecha de generación.
- Base sintética de origen.
- Base sintética de destino.
- Procedimiento de restauración.
- Resultados de integridad.

La restauración se realizará en una base independiente.

Se verificarán conteos conocidos, relaciones, asignaciones, seguimientos y eventos de auditoría.

Cuando corresponda, se comprobará la estructura técnica de sesiones sin exponer tokens reutilizables.

La prueba no permitirá declarar objetivos institucionales de RPO, RTO o alta disponibilidad que no hayan sido establecidos y evaluados.

## 8. Evidencia, resultados y gestión de fallas

### 8.1. Registro de ejecuciones

Cuando se realicen pruebas, cada ejecución deberá registrar:

1. Identificador RUN-ID.
2. Revisión de código y documentación.
3. Fecha con zona horaria.
4. Herramientas y versiones.
5. Configuración no secreta del entorno.
6. Migraciones y datos utilizados.
7. Procedimiento ejecutado.
8. Resultado esperado.
9. Resultado observado.
10. Evidencia reproducible.
11. Limitaciones identificadas.

La ubicación futura propuesta para los resultados es:

```text
docs/evaluation-2/validation/evidence/
└── <RUN-ID>/
    └── <TEST-ID>/
```

Esta estructura es orientativa.

**La carpeta `validation/evidence/` no forma parte de la entrega actual y no debe crearse vacía.**

Solo se incorporarán archivos cuando existan pruebas ejecutadas y resultados verificables.

Las evidencias podrán incluir JSON sanitizado, registros SQL, métricas, capturas y diferencias de código.

No deberán incluir cookies, tokens, contraseñas, secretos ni datos académicos reales.

### 8.2. Estados de validación

El registro de [Validation Findings](findings.md) utilizará:

| Estado | Significado |
|---|---|
| NOT EXECUTED | No existe un intento de ejecución documentado. |
| BLOCKED | Un intento registrado no pudo continuar. |
| PASS | El resultado observado satisface el criterio y cuenta con evidencia suficiente. |
| FAIL | El resultado observado incumple el criterio. |
| INCONCLUSIVE | La ejecución no permite determinar el cumplimiento. |

Las pruebas que no cuenten con evidencia suficiente no se considerarán aprobadas.

Un procedimiento escrito y su resultado esperado no representan una ejecución.

**Estado actual de DH-01:** las 18 pruebas técnicas previstas permanecen como `NOT EXECUTED`. La existencia del prototipo HTML no modifica este resultado.

### 8.3. Gestión de fallas y reevaluación

Cada falla deberá conservar su evidencia original y una referencia a la versión donde fue observada.

Las correcciones se asociarán con nuevas revisiones de código y nuevas ejecuciones.

No se sobrescribirán resultados anteriores.

La prioridad de tratamiento considerará confidencialidad, integridad e impacto funcional.

Las pruebas se repetirán cuando se modifiquen componentes, configuraciones o precondiciones relevantes.

Cuando un resultado cuestione una decisión arquitectónica, deberán revisarse el ADR, las vistas, los contratos, los riesgos y las pruebas relacionadas.

### 8.4. Responsabilidades propuestas

| Función | Actividad | Estado |
|---|---|---|
| Desarrollo | Preparar implementación y mecanismos de fallo controlado. | Pendiente de asignación para la fase de desarrollo. |
| Validación | Ejecutar, revisar y registrar resultados. | Pendiente de asignación para la fase de desarrollo. |
| Operación | Preparar entorno y restauraciones. | Pendiente de asignación para la fase de desarrollo. |
| Documentación | Mantener trazabilidad y referencias de evidencia. | Definida como responsabilidad documental del equipo. |
| Interlocución institucional | Confirmar políticas abiertas. | Interlocutor autorizado pendiente. |

Para la Evaluación 2, la distribución documental propuesta es:

| Integrante | Área principal |
|---|---|
| Nayelhi Álvarez | Requisitos y diseño del sistema. |
| Sergio Pérez | Arquitectura y decisiones técnicas. |
| Pedro Peñate | Validación, trazabilidad y planificación. |

Esta distribución no implica nombramientos institucionales ni compromisos confirmados de desarrollo futuro.

## 9. Criterios de cierre, limitaciones y referencias

### 9.1. Criterios de cierre

El cierre documental de la Evaluación 2 requiere que:

1. Los requisitos y escenarios de calidad estén identificados.
2. Las decisiones arquitectónicas tengan una justificación.
3. Los componentes y vistas sean consistentes entre sí.
4. Los mecanismos de seguridad, integridad y auditoría estén documentados.
5. El plan de validación contemple los riesgos y comportamientos principales.
6. Los criterios de aceptación sean observables.
7. La matriz de trazabilidad relacione requisitos, decisiones y pruebas.
8. El estado de ejecución sea presentado correctamente.
9. Las políticas y supuestos pendientes estén explícitos.
10. El prototipo interactivo esté identificado como una demostración local, con sus limitaciones diferenciadas de la arquitectura propuesta.

Para una futura campaña técnica, el cierre requerirá resultados reproducibles sobre las capacidades implementadas.

No deberán quedar fallas críticas sin tratar dentro del alcance declarado como verificado.

Los objetivos cuantitativos solo se considerarán satisfechos si se miden bajo condiciones compatibles con sus escenarios.

El informe técnico de una campaña deberá identificar:

- Pruebas ejecutadas y no ejecutadas.
- Pruebas bloqueadas o inconclusas.
- Resultados asociados a versiones.
- Fallas abiertas.
- Correcciones pendientes.
- Limitaciones del entorno.
- Capacidades verificadas y no verificadas.

No se declarará validado todo DH-01 cuando únicamente se haya comprobado una parte del sistema.

Los resultados técnicos futuros se registrarán en [Validation Findings](findings.md).

### 9.2. Limitaciones del plan

El plan establece procedimientos y criterios para verificar la propuesta arquitectónica.

No demuestra por sí mismo que los controles de confidencialidad, concurrencia, persistencia o recuperación funcionen.

Aunque DH-01 incluye un prototipo HTML interactivo, este no incorpora los contenedores integrados ni la infraestructura necesaria para ejecutar las pruebas técnicas del plan.

Los objetivos cuantitativos QS-04, QS-06 y QS-07 corresponden a escenarios propuestos y no a compromisos institucionales de producción.

Las pruebas futuras utilizarán información sintética y no podrán demostrar interoperabilidad con servicios institucionales reales que no hayan sido integrados.

Las políticas pendientes de delegación, reapertura, retención y escalamiento deberán confirmarse antes de incorporar criterios de aceptación institucional definitivos.

### 9.3. Documentos relacionados

- [Architecture and Design](../architecture-and-design.md)
- [Requirements and Drivers](../requirements-and-drivers.md)
- [Quality Scenarios](../quality-scenarios.md)
- [Architecture Decision Records](../adr/README.md)
- [Architectural Views](../views/README.md)
- [Domain and Data Model](../design/domain-and-data.md)
- [Lifecycle and Runtime](../design/lifecycle-and-runtime.md)
- [API Contracts](../design/interfaces.md)
- [Security Design](../design/security.md)
- [Deployment Assumptions](../deployment-assumptions.md)
- [Traceability Matrix](../traceability.md)
- [Validation Findings](findings.md)
- [Project Status](../project-status.md)
- [Prototipo interactivo y limitaciones](../../../prototype/README.md)

Este documento constituye el **plan de validación arquitectónica y funcional de DH-01** para la Evaluación 2.

Su contribución consiste en identificar los mecanismos críticos, establecer procedimientos reproducibles y definir qué evidencias permitirían comprobar las decisiones de diseño durante una implementación futura.

**La entrega actual incluye planificación documental y un prototipo HTML navegable. No demuestra la ejecución de las 18 pruebas técnicas ni el cumplimiento empírico de los escenarios de calidad de la arquitectura propuesta.**
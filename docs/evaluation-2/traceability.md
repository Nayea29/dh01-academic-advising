# DH-01 — Matriz de Trazabilidad


## 1. Propósito, convenciones y cobertura

Este documento relaciona los requisitos de DH-01 con los drivers arquitectónicos, decisiones, componentes, entidades, interfaces, pruebas y actividades de implementación.

Su propósito es justificar las decisiones de diseño, identificar las responsabilidades de cada elemento y establecer cómo podrá verificarse el cumplimiento de las necesidades del Academic Advising Case Management System.

La matriz permite recorrer la arquitectura en ambas direcciones: desde un requisito hasta su diseño y validación, o desde una decisión y componente hasta las necesidades que justifican su existencia.

Se utilizan los siguientes identificadores:

| Prefijo | Significado | Fuente |
|---|---|---|
| FR | Requisito funcional. | `requirements-and-drivers.md` |
| QR | Requisito de calidad. | `requirements-and-drivers.md` |
| BR / PBR | Regla de negocio / regla complementaria propuesta. | `requirements-and-drivers.md` |
| C / AS | Restricción / supuesto. | `requirements-and-drivers.md` |
| AD | Architecture Driver. | `requirements-and-drivers.md` |
| ADR | Architecture Decision Record. | `adr/` |
| QS | Escenario de calidad verificable. | `quality-scenarios.md` |
| CMP | Componente lógico del backend. | `views/component-view.md` |
| T | Prueba prevista. | `validation/test-plan.md` |
| R / B | Riesgo / actividad del backlog. | `project-status.md` |

Un Architecture Driver (`AD`) representa un factor que orienta las decisiones arquitectónicas, mientras que un Architecture Decision Record (`ADR`) documenta una decisión seleccionada y su justificación.

Las referencias a escenarios de calidad pueden complementar la trazabilidad funcional, pero no sustituyen los criterios específicos de cada requisito.

### 1.1. Estado de la cobertura

La cobertura establecida en esta matriz es principalmente documental.

Se distinguen tres niveles:

| Nivel | Criterio |
|---|---|
| Cobertura documental | Existen responsabilidades, comportamientos y procedimientos de validación relacionados con el requisito. |
| Cobertura de implementación | Existe código identificable que materializa la capacidad. |
| Cobertura de ejecución | Existe una prueba ejecutada con revisión, entorno, resultado observado y evidencia. |

Según la línea base documental registrada, los requisitos cuentan con propuestas de diseño y procedimientos de validación, pero no con implementación o ejecución demostradas.

La documentación permite justificar decisiones y planificar verificaciones, sin demostrar automáticamente conformidad funcional, seguridad, rendimiento o aceptación institucional.

## 2. Trazabilidad de requisitos funcionales y reglas

### 2.1. Cobertura funcional

La siguiente matriz relaciona los catorce requisitos funcionales con sus reglas, drivers, decisiones, componentes y pruebas previstas.

Todas las filas representan cobertura documental. La implementación y los resultados de ejecución permanecen pendientes de verificación.

| FR | Regla relacionada | Driver | Decisión | Componentes | Diseño relacionado | Validación |
|---|---|---|---|---|---|---|
| FR-01 — Case Creation | PBR-03 | AD-01 | ADR-001, ADR-002, ADR-003, ADR-005 | CMP-01, CMP-03, CMP-08, CMP-09 | Creación de casos, dominio, ciclo de vida e interfaces. | T-FUNC-01, T-DATA-01 |
| FR-02 — Multiple Intake Origins | BR-08 | AD-01, AD-02 | ADR-002, ADR-004 | CMP-01, CMP-02, CMP-03 | Diferenciación de origen, estudiante y actor; referencia autorizada. | T-FUNC-01, T-SEC-01 |
| FR-03 — Request Classification | BR-01, BR-02, PBR-09 | AD-01, AD-05 | ADR-001, ADR-003 | CMP-03, CMP-04 | Clasificación formal y catálogos controlados. | T-TRIAGE-01, T-MOD-01 |
| FR-04 — Explicit Assignment | BR-01, BR-03 | AD-01, AD-06 | ADR-002, ADR-003 | CMP-03, CMP-04, CMP-09 | Asignación vigente única y responsable visible según autorización. | T-TRIAGE-01, T-DATA-01, T-UX-01 |
| FR-05 — Case History | BR-06 | AD-03 | ADR-002, ADR-005 | CMP-08, CMP-09 | Eventos persistentes vinculados a operaciones confirmadas. | T-AUD-01, T-TXN-01 |
| FR-06 — Adviser Case Management | BR-08 | AD-01, AD-02 | ADR-003, ADR-004 | CMP-02, CMP-03, CMP-05, CMP-06 | Atención limitada al adviser vigente. | T-FUNC-01, T-SEC-01 |
| FR-07 — Additional Information | BR-04, BR-05 | AD-04 | ADR-002, ADR-003 | CMP-03, CMP-05 | InformationRequest y estado WAITING_FOR_INFORMATION. | T-FUNC-01, T-LIFE-01 |
| FR-08 — Follow-up Management | BR-04, BR-05 | AD-01, AD-04 | ADR-002, ADR-003 | CMP-03, CMP-05, CMP-09 | Seguimiento por audiencia, estados y bloqueo común. | T-FUNC-01, T-LIFE-01, T-LIFE-02 |
| FR-09 — Referral / Reassignment | BR-03, BR-06, PBR-06 | AD-01, AD-03 | ADR-003, ADR-004, ADR-005 | CMP-02, CMP-03, CMP-04, CMP-08 | Reasignación autorizada, atómica y auditable. | T-AUD-01, T-TXN-01, T-SEC-01 |
| FR-10 — Reassignment History | BR-06 | AD-03 | ADR-002, ADR-005 | CMP-04, CMP-08, CMP-09 | Asignaciones históricas y metadatos del cambio. | T-AUD-01 |
| FR-11 — Resolution | BR-05 | AD-01, AD-04 | ADR-003, ADR-005 | CMP-03, CMP-05, CMP-08, CMP-09 | Recommendation, ausencia de pendientes y resolución transaccional. | T-LIFE-01, T-LIFE-02, T-TXN-01 |
| FR-12 — Student Visibility | BR-07, BR-08 | AD-02, AD-06 | ADR-004 | CMP-01, CMP-02, CMP-03, CMP-06, CMP-07 | StudentCase y representaciones según audiencia. | T-SEC-01, T-FUNC-01, T-UX-01 |
| FR-13 — Operational Monitoring | PBR-04, BR-08 | AD-06 | ADR-001, ADR-002, ADR-004 | CMP-02, CMP-07, CMP-09 | Filtros, carga, categorías, volumen y seguimiento vencido. | T-PERF-01, T-SEC-01, T-API-01 |
| FR-14 — Access Control | BR-07, BR-08 | AD-02 | ADR-004 | CMP-01, CMP-02, CMP-06 | Sesiones opacas, autorización contextual, CSRF y recursos protegidos. | T-AUTH-01, T-SEC-01, T-SEC-02, T-API-01 |

El indicador general de retraso de un caso en FR-13 permanece sujeto a definición institucional.

Inicialmente, se propone identificar un FollowUp vencido cuando está `PENDING` y su fecha `dueAt` es anterior al momento de consulta. Esto no establece una política definitiva de overdue o escalation.

La referencia a PBR-06 en FR-09 no habilita delegaciones automáticamente. En el flujo inicial, la reasignación corresponde al Advising Coordinator autorizado.

### 2.2. Reglas de negocio

| Regla | Aplicación en el diseño | Verificación o decisión pendiente |
|---|---|---|
| BR-01 — Clasificación previa | Triage explícito y asignación posterior a clasificación concluida. | T-TRIAGE-01 |
| BR-02 — Tipo y programa | CaseClassification con categoría y programa válidos. | T-TRIAGE-01 |
| BR-03 — Adviser principal y reasignación | Una asignación vigente e historial de responsables. | T-DATA-01, T-AUD-01 |
| BR-04 — Seguimiento interno o estudiantil | FollowUp con audiencia INTERNAL o STUDENT. | T-FUNC-01, T-SEC-01 |
| BR-05 — Resolución sin pendientes | Ningún FollowUp PENDING ni InformationRequest OPEN al cerrar. | T-LIFE-01, T-LIFE-02 |
| BR-06 — Historial de responsabilidad | Reasignación y evento de auditoría en una transacción. | T-AUD-01, T-TXN-01 |
| BR-07 — Notas internas restringidas | InternalNote separada de Recommendation y StudentCase. | T-SEC-01 |
| BR-08 — Rol, relación y sensibilidad | Autorización contextual y revalidación de permisos. | T-SEC-01, T-SEC-02, T-API-01 |
| BR-09 — Políticas por validar | Estados, permisos, retrasos, escalamiento y reapertura sujetos a validación. | Aprobación institucional pendiente. |

En el flujo inicial, BR-05 se aplicará a cualquier FollowUp `PENDING`, independientemente de su audiencia.

Permitir seguimientos opcionales que no impidan la resolución requerirá revisar explícitamente la política, el modelo de dominio, las interfaces y las pruebas.

### 2.3. Reglas complementarias propuestas

| PBR | Tratamiento | Estado |
|---|---|---|
| PBR-01 | Coordinación supervisa triage; posible delegación explícita. | Reservado al Advising Coordinator en el flujo inicial. |
| PBR-02 | Asignación manual considerando programa, categoría y carga. | Propuesto; sin algoritmo automático. |
| PBR-03 | Transiciones centralizadas en C-API. | Diseño definido en ciclo de vida. |
| PBR-04 | Parámetros controlados para retraso. | Seguimiento vencido propuesto; umbral general pendiente. |
| PBR-05 | Escalamiento diferenciado de reasignación. | Política pendiente, sin automatización inicial. |
| PBR-06 | Reasignación por coordinación y posible delegado futuro. | Coordinación autorizada; delegación pendiente. |
| PBR-07 | Notas internas y recomendaciones separadas. | Diseño documentado, implementación pendiente. |
| PBR-08 | Reapertura como extensión posterior. | Fuera del flujo inicial. |
| PBR-09 | Categorías y prioridades controladas. | Categorías propuestas; prioridades por validar. |

Las reglas complementarias representan interpretaciones y decisiones de diseño del equipo. Su presencia en varios documentos no constituye aprobación institucional.

## 3. Calidad, restricciones y supuestos

### 3.1. Requisitos de calidad y escenarios

| QR | Drivers | Escenarios | Respuesta arquitectónica | Pruebas | Estado |
|---|---|---|---|---|---|
| QR-01 — Security | AD-02 | QS-01 | Autenticación, sesiones opacas, CSRF, autorización contextual y aislamiento. | T-AUTH-01, T-SEC-01, T-SEC-02, T-API-01, T-DEP-01 | Sin demostrar |
| QR-02 — Confidentiality | AD-02 | QS-01 | Notas separadas, respuestas autorizadas y auditoría mínima. | T-SEC-01, T-AUD-01 | Sin demostrar |
| QR-03 — Traceability | AD-03 | QS-03 | Historial persistente y eventos transaccionales. | T-AUD-01, T-TXN-01 | Sin demostrar |
| QR-04 — Maintainability | AD-05 | QS-05 | Monolito modular y contratos internos. | T-MOD-01 | Sin demostrar |
| QR-05 — Usability | AD-06 | QS-06; QS-04 como apoyo | Estados comprensibles, responsables visibles y consultas oportunas. | T-UX-01, T-PERF-01 | Sin demostrar |
| QR-06 — Availability | AD-07 | QS-07; QS-04 como apoyo | Persistencia independiente, salud y recuperación. | T-REC-01, T-REC-02, T-DEP-01 | Sin demostrar |

QS-02 comprueba la integridad del ciclo de vida asociada especialmente con FR-08, FR-11 y BR-05.

No se introduce un requisito QR adicional para representar esa necesidad, porque no existe un identificador correspondiente en los requisitos originales.

Los objetivos cuantitativos de QS-04, QS-06 y QS-07 siguen siendo criterios de evaluación, no resultados medidos.

### 3.2. Restricciones

| Restricción | Respuesta arquitectónica | Verificación prevista |
|---|---|---|
| C-01 — Datos sintéticos | Semillas y perfiles ficticios. | T-FUNC-01, T-DEP-01 |
| C-02 — Identidad institucional o equivalente | Autenticación interna según ADR-004. | T-AUTH-01 |
| C-03 — Acceso contextual | Rol, relación y sensibilidad. | T-SEC-01, T-SEC-02 |
| C-04 — Información sensible | Recursos y representaciones separados. | T-SEC-01, T-AUD-01 |
| C-05 — Independencia | Servicios propios y configuración reproducible. | T-DEP-01, T-SIS-01 |
| C-06 — SIS simulado | X-SIS independiente y CMP-10. | T-SIS-01 |
| C-07 — Recursos limitados | Monolito modular y despliegue simplificado. | T-MOD-01, T-DEP-01 |
| C-08 — Acceso remoto | HTTPS y restricciones de red. | T-DEP-01 |
| C-09 — Viabilidad semestral | Incrementos y backlog priorizado. | Seguimiento del backlog y T-DEP-01 como apoyo. |
| C-10 — Trazabilidad | Auditoría persistente junto con modificaciones relevantes. | T-AUD-01, T-TXN-01 |
| C-11 — Evolución de políticas | Configuración y catálogos controlados. | T-MOD-01 |

Los supuestos AS-01 a AS-08 permanecen documentados en [Requirements and Drivers](requirements-and-drivers.md).

Los supuestos específicos AS-DEP-01 a AS-DEP-08 se desarrollan en [Deployment Assumptions](deployment-assumptions.md).

La disponibilidad real de los recursos y configuraciones descritos todavía debe comprobarse.

### 3.3. Parámetros propuestos y su origen

| Parámetro | Origen documental | Naturaleza |
|---|---|---|
| Siete estados | ADR-003 y AS-01. | Propuesta operacional. |
| 12.000 casos y 20 solicitudes concurrentes | QS-04. | Carga sintética propuesta. |
| 95 % de respuestas bajo 800 ms | QS-04. | Objetivo de rendimiento. |
| Cuatro de cinco participantes | QS-06. | Criterio de usabilidad. |
| Recuperación en 60 segundos | QS-07. | Objetivo de reinicio controlado. |
| Sesión: 30 minutos de inactividad y 8 horas absolutas | Security Design. | Parámetros técnicos propuestos. |
| Sesión opaca persistida mediante tokenDigest | Security Design y Domain and Data Model. | Mecanismo técnico propuesto. |
| Timeout SIS de 2.000 ms | API Contracts. | Parámetro técnico propuesto. |
| Cuerpo de 64 KiB y texto de 10.000 caracteres | API Contracts. | Límites propuestos. |

La separación entre requisitos, decisiones y parámetros técnicos evita atribuir nuevas restricciones al enunciado original o confundir objetivos previstos con resultados obtenidos.

## 4. Drivers, decisiones y componentes

### 4.1. Drivers y vistas

| Driver | Decisiones relacionadas | Componentes principales | Vistas |
|---|---|---|---|
| AD-01 — Case Lifecycle Integrity | ADR-001, ADR-002, ADR-003 | CMP-03, CMP-04, CMP-05, CMP-09 | V-CON, V-CMP |
| AD-02 — Confidentiality and Controlled Access | ADR-004 | CMP-01, CMP-02, CMP-06, CMP-07 | V-CTX, V-CON, V-CMP |
| AD-03 — Traceability and Historical Consistency | ADR-002, ADR-005 | CMP-04, CMP-08, CMP-09 | V-CMP |
| AD-04 — Follow-up and Resolution Consistency | ADR-002, ADR-003 | CMP-03, CMP-05, CMP-09 | V-CMP |
| AD-05 — Maintainability and Policy Evolution | ADR-001 | CMP-01 a CMP-10 | V-CON, V-CMP |
| AD-06 — Operational Visibility | ADR-002, ADR-004 como apoyo | CMP-01, CMP-07, CMP-09 y C-WEB | V-CON, V-CMP |
| AD-07 — Deployment Feasibility | ADR-001, ADR-002 | C-WEB, C-API, C-DB, X-SIS | V-DEP |

Las referencias a ADR representan decisiones que apoyan cada driver y no modifican los drivers principales declarados por las decisiones.

Se mantienen los identificadores `V-CON` y `V-CMP` utilizados en las vistas originales.

### 4.2. ADR-001 — Monolito modular

ADR-001 organiza las capacidades en un único backend con responsabilidades internas diferenciadas.

Esta decisión favorece mantenibilidad, despliegue simplificado y coordinación transaccional.

| Elemento de diseño | Necesidad relacionada | Evidencia futura |
|---|---|---|
| CMP-01 separa HTTP de la lógica de negocio. | QR-04, AD-05. | Revisión de módulos y T-MOD-01. |
| CMP-03 a CMP-06 separan responsabilidades de atención. | FR-03, FR-06, FR-08, FR-11. | T-FUNC-01 y pruebas del dominio. |
| C-API constituye una aplicación backend desplegable. | C-07, C-09, AD-07. | T-DEP-01. |
| Contratos internos y UnitOfWork coordinan operaciones. | AD-01, AD-04. | T-TXN-01 y revisión de dependencias. |

La modularidad deberá comprobarse mediante límites, dependencias y modificaciones controladas, no únicamente contando carpetas.

### 4.3. ADR-002 — PostgreSQL

PostgreSQL proporciona persistencia relacional, restricciones de integridad, consultas estructuradas y mecanismos transaccionales.

| Elemento de diseño | Necesidad relacionada | Evidencia futura |
|---|---|---|
| Claves y relaciones de casos. | FR-01, FR-07, FR-08. | T-DATA-01. |
| Una asignación vigente por caso. | FR-04, BR-03. | T-DATA-01, T-TRIAGE-01. |
| Transacciones compartidas. | FR-09, FR-11, BR-05, BR-06. | T-TXN-01. |
| Persistencia independiente del backend. | QR-06, QS-07. | T-REC-01. |
| Consultas estructuradas e índices. | FR-13, QS-04. | T-PERF-01. |
| Tabla técnica SESSION. | FR-14, ADR-004. | T-AUTH-01 y pruebas de persistencia. |

La elección de PostgreSQL no garantiza por sí sola integridad o recuperación.

Estas propiedades dependerán del esquema implementado, el uso correcto de transacciones y las pruebas correspondientes.

### 4.4. ADR-003 — Ciclo de vida consistente

ADR-003 establece operaciones explícitas de negocio, validación de estados y control de concurrencia.

| Elemento de diseño | Necesidad relacionada | Evidencia futura |
|---|---|---|
| Siete estados y operaciones controladas. | FR-01, FR-03, FR-04, FR-11. | T-FUNC-01, T-TRIAGE-01. |
| Condiciones de resolución. | BR-05, AD-04. | T-LIFE-01. |
| Bloqueo común y expectedVersion. | QS-02. | T-LIFE-02, T-API-01. |
| Rechazo de operaciones sin cambios parciales. | AD-01. | T-TXN-01. |

Los siete estados representan una propuesta seleccionada y requieren confirmación institucional.

Su implementación técnica no sustituirá esa aprobación.

### 4.5. ADR-004 — Autenticación y autorización contextual

ADR-004 propone autenticación interna con cuentas sintéticas y autorización basada en roles, relaciones y recursos.

| Elemento de diseño | Necesidad relacionada | Evidencia futura |
|---|---|---|
| Identidades ficticias y sesiones opacas. | C-02, FR-14. | T-AUTH-01. |
| SESSION y tokenDigest. | FR-14, QR-01. | T-AUTH-01. |
| Protección CSRF y verificación de origen. | QR-01, AD-02. | T-SEC-02. |
| Rol y relación vigente. | BR-08, AD-02. | T-SEC-01. |
| Campos diferenciados por audiencia. | FR-12, BR-07, QR-02. | T-SEC-01, T-API-01. |
| Validación de recursos anidados. | FR-14, BR-08. | T-SEC-02, T-API-01. |
| Actualización de permisos después de reasignar. | FR-09, FR-14. | T-AUD-01, T-SEC-01. |

Las cookies de sesión, CSRF y los parámetros de expiración desarrollan la decisión como mecanismos técnicos propuestos.

No se presentan como requisitos prescritos explícitamente por Horizon University.

### 4.6. ADR-005 — Auditoría persistente

ADR-005 establece la conservación de eventos relevantes mediante registros persistentes y transacciones compartidas.

| Elemento de diseño | Necesidad relacionada | Evidencia futura |
|---|---|---|
| Catálogo de eventos de negocio. | FR-05, QR-03. | T-AUD-01. |
| Responsable anterior, nuevo y motivo. | FR-10, BR-06. | T-AUD-01. |
| Eventos y cambios confirmados conjuntamente. | AD-03. | T-TXN-01. |
| Ordenamiento por AuditEvent.caseSequence. | FR-05, FR-10. | T-AUD-01. |
| Metadatos sin duplicar notas internas. | QR-02, BR-07. | T-SEC-01, T-AUD-01. |

Los eventos obligatorios se registrarán en la misma transacción de negocio que los cambios relacionados.

`caseSequence` permitirá ordenar el historial dentro de cada caso, mientras que `AdvisingCase.version` se utilizará para el control de modificaciones concurrentes.

### 4.7. Responsabilidades de componentes

| Componente | Entradas principales | Responsabilidad verificable | Pruebas relacionadas |
|---|---|---|---|
| CMP-01 — API Controllers | Solicitudes HTTP. | Validar entradas, delegar operaciones y construir respuestas. | T-API-01, T-SEC-02 |
| CMP-02 — Authentication & Authorization | Sesión, actor y recurso. | Autenticación, SESSION y autorización contextual. | T-AUTH-01, T-SEC-01, T-SEC-02 |
| CMP-03 — Case Management | Operación y caso vigente. | Gestionar estados, versiones e invariantes. | T-FUNC-01, T-LIFE-01 |
| CMP-04 — Triage & Assignment | Clasificación y adviser. | Asignar y reasignar conservando historial. | T-TRIAGE-01, T-AUD-01 |
| CMP-05 — Follow-up & Resolution | Pendientes e información adicional. | Gestionar seguimiento y cierre consistente. | T-LIFE-01, T-LIFE-02 |
| CMP-06 — Notes & Confidentiality | Contenido y audiencia. | Separar notas y recomendaciones. | T-SEC-01, T-FUNC-01 |
| CMP-07 — Reporting & Queries | Filtros y alcance. | Consultas y agregados autorizados. | T-PERF-01, T-API-01 |
| CMP-08 — Audit & History | Eventos y contexto transaccional. | Registrar historial de operaciones confirmadas. | T-AUD-01, T-TXN-01 |
| CMP-09 — Persistence Layer | Repositorios y UnitOfWork. | Restricciones, transacciones y acceso a datos. | T-DATA-01, T-TXN-01, T-REC-01 |
| CMP-10 — SIS Integration Adapter | Identificador sintético. | Validar integración y controlar fallos externos. | T-SIS-01 |

Las pruebas de integración pueden involucrar varios componentes, por lo que ninguna asignación de la tabla implica responsabilidad exclusiva.

La tabla técnica SESSION pertenece funcionalmente a CMP-02, que utiliza CMP-09 para acceder a PostgreSQL.

## 5. Entidades, interfaces y pruebas

### 5.1. Trazabilidad de entidades

| Entidad | Requisitos relacionados | Contrato o comportamiento |
|---|---|---|
| User / Role | FR-14, C-02 | Autenticación y autorización. |
| UserProgramScope | FR-02, FR-13, FR-14 | Referencias y consultas según programa. |
| StudentProfile | FR-01, FR-02, FR-12 | Identidad sintética y propiedad del caso. |
| AcademicProgram | FR-02, FR-03, FR-13 | Programa y alcance académico. |
| RequestCategory | FR-03, BR-02 | Clasificación controlada. |
| AdvisingCase | FR-01, FR-06, FR-11 | Creación, atención y resolución. |
| CaseClassification | FR-03, BR-01, BR-02 | Clasificación formal. |
| CaseAssignment | FR-04, FR-09, FR-10 | Asignación y reasignación. |
| FollowUp | FR-08, BR-04, BR-05 | Seguimiento y finalización. |
| InformationRequest | FR-07 | Solicitud y respuesta de información. |
| InternalNote | FR-06, BR-07 | Notas internas restringidas. |
| Recommendation | FR-06, FR-11, FR-12 | Orientación destinada al estudiante. |
| AuditEvent | FR-05, FR-10, QR-03 | Historial y eventos obligatorios. |
| SESSION | FR-14, ADR-004 | Persistencia y revocación de sesiones opacas. |

SESSION es una tabla técnica de autenticación y no una entidad académica.

Su relación con User y la persistencia de `tokenDigest` se describen en [Domain and Data Model](design/domain-and-data.md).

Las posibles concesiones especiales de permisos siguen siendo extensiones sujetas a definición institucional.

### 5.2. Grupos de contratos

| Grupo | Documentos de referencia | Criterios que deben coincidir |
|---|---|---|
| Autenticación | Interfaces, seguridad y dominio. | Cookie opaca, SESSION, expiración y CSRF. |
| Creación y referencias | Interfaces, dominio y ciclo de vida. | Origen, actor, estudiante y estado NEW. |
| Triage y asignación | Interfaces y ciclo de vida. | Clasificación formal y adviser válido. |
| Información adicional | Interfaces, ciclo de vida y seguridad. | Solicitud OPEN, propietario y estado calculado. |
| Seguimiento | Dominio, ciclo de vida y seguridad. | Audiencia, pendientes y finalización. |
| Notas y recomendaciones | Dominio e interfaces. | Recursos separados y condición de orientación. |
| Resolución | Ciclo de vida y plan de pruebas. | IN_PROGRESS, versión, Recommendation y ausencia de pendientes. |
| Reportes | Interfaces y seguridad. | Alcance aplicado antes de filtros, paginación y recuentos. |
| SIS | Interfaces y vistas. | Mock independiente y contrato sintético. |

Las interfaces expuestas a C-WEB deberán aplicar las mismas políticas de autorización que los servicios internos.

Las operaciones de escritura no permitirán que el cliente imponga identidades, roles o transiciones mediante campos arbitrarios.

### 5.3. Trazabilidad desde las pruebas

| Prueba | Pregunta de validación | Artefactos vinculados |
|---|---|---|
| T-SEC-01 | ¿Cada actor accede únicamente a información y operaciones autorizadas? | Matriz de permisos, DTO y políticas. |
| T-SEC-02 | ¿Se rechazan escrituras sin CSRF y recursos anidados ajenos? | Sesión, origen, contratos y pertenencia. |
| T-LIFE-01 | ¿Se impide resolver sin cumplir las condiciones de cierre? | Estados, guardas y pendientes. |
| T-LIFE-02 | ¿Las operaciones concurrentes conservan BR-05? | Bloqueos, versiones y estado final. |
| T-AUD-01 | ¿Puede reconstruirse una reasignación válida? | Asignaciones históricas y eventos. |
| T-PERF-01 | ¿La consulta evaluada cumple QS-04? | Carga, filtros, métricas y entorno. |
| T-MOD-01 | ¿Un cambio controlado conserva límites modulares? | Diff y regresión. |
| T-UX-01 | ¿Los participantes comprenden el estado y la siguiente acción? | Tareas y respuestas. |
| T-REC-01 | ¿El reinicio conserva datos y recupera capacidad funcional? | Tiempos y consultas posteriores. |
| T-FUNC-01 | ¿Funciona el flujo completo del incremento implementado? | C-WEB, C-API y persistencia. |
| T-TRIAGE-01 | ¿La asignación exige clasificación y responsable válidos? | Transiciones y elegibilidad. |
| T-DATA-01 | ¿Las restricciones impiden estructuras inconsistentes? | Esquema y restricciones. |
| T-TXN-01 | ¿Una falla obligatoria revierte toda la operación? | Inyección de fallos y datos persistidos. |
| T-AUTH-01 | ¿Las sesiones cumplen el mecanismo propuesto? | Login, cookies, SESSION y revocación. |
| T-API-01 | ¿Los contratos rechazan entradas inválidas y conflictos? | Rutas, esquemas y errores. |
| T-SIS-01 | ¿La integración valida respuestas y controla fallos? | Mock, adaptador y operaciones independientes. |
| T-REC-02 | ¿Un respaldo permite recuperar información coherente? | Archivo, restauración y relaciones. |
| T-DEP-01 | ¿El entorno es reproducible y restringe accesos? | Configuración, instrucciones y conectividad. |

Estas preguntas siguen sin respuesta empírica en la línea base documental registrada.

El [Validation Plan](validation/test-plan.md) define los procedimientos. [Validation Findings](validation/findings.md) registrará los resultados cuando existan ejecuciones verificables.

## 6. Riesgos, backlog y brechas

### 6.1. Trazabilidad de riesgos y actividades

| Riesgo | Necesidad comprometida | Actividades previstas | Validación |
|---|---|---|---|
| R-01 — Políticas abiertas | BR-09 y supuestos institucionales. | B-01. | Registro de decisiones. |
| R-02 — Exposición de notas | QR-01, QR-02, BR-07. | B-04, B-06. | T-SEC-01 |
| R-03 — Cierre concurrente | FR-11, BR-05. | B-07. | T-LIFE-02 |
| R-04 — Historial inconsistente | FR-05, FR-10, BR-06. | B-05, B-07. | T-AUD-01, T-TXN-01 |
| R-05 — Recursos y consultas | FR-13, QS-04. | B-10, B-12. | T-PERF-01 |
| R-06 — Secretos y sesiones | FR-14, QR-01. | B-02, B-04. | T-AUTH-01, T-SEC-02, T-DEP-01 |
| R-07 — Dependencia SIS | C-05, C-06. | B-09. | T-SIS-01 |
| R-08 — Persistencia y respaldos | QR-06, QS-07. | B-03, B-11. | T-REC-01, T-REC-02 |
| R-09 — Acoplamiento | QR-04, AD-05. | B-05 a B-08. | T-MOD-01 |
| R-10 — Viabilidad temporal | C-09. | Priorización B-01 a B-13. | Seguimiento del esfuerzo cuando exista estimación. |
| R-11 — Estado sobredimensionado | Evidencia y veracidad documental. | B-13. | Informe de campaña con alcance explícito. |

Estas relaciones permiten identificar actividades destinadas a reducir los riesgos, pero no implican que las mitigaciones hayan sido ejecutadas.

### 6.2. Cobertura y brechas pendientes

La cobertura documental se establece cuando un requisito tiene una responsabilidad identificada, un comportamiento propuesto y un procedimiento de validación relacionado.

Sin embargo, esta cobertura debe evaluarse por suficiencia, no únicamente por la presencia del identificador en una tabla.

La cobertura de implementación requerirá código identificable.

La cobertura de ejecución exigirá pruebas con una revisión determinada, entorno, resultados y evidencia.

Una prueba parcial podrá demostrar un camino funcional sin comprobar todas las alternativas o restricciones del requisito.

Las principales brechas registradas son:

- Implementación todavía no demostrada.
- Pruebas y evidencias reproducibles no disponibles.
- Políticas institucionales de estados, permisos y cierre pendientes.
- Definiciones de overdue, escalation y reopening sin confirmar.
- Políticas de retención, anonimización y datos reales pendientes.
- Parámetros técnicos sujetos a verificación.
- Responsables y estimaciones del backlog pendientes de asignación.

Una capacidad puede tener un diseño técnico detallado y continuar condicionada por estas decisiones.

## 7. Cambios y recorrido de trazabilidad

### 7.1. Gestión de cambios con impacto

| Cambio | Documentos que deben revisarse | Pruebas relacionadas |
|---|---|---|
| Nuevo estado | ADR-003, ciclo de vida, interfaces, dominio y vistas. | T-FUNC-01, T-LIFE-01, T-LIFE-02 |
| Nuevo rol o ámbito | ADR-004, seguridad, interfaces y dominio. | T-SEC-01, T-SEC-02, T-AUTH-01, T-API-01 |
| Cambio en regla de resolución | Requisitos, ADR-003, ciclo de vida y plan. | T-LIFE-01, T-LIFE-02 |
| Nuevo evento obligatorio | ADR-005, dominio y contratos internos. | T-AUD-01, T-TXN-01 |
| Cambio de política de seguimiento | Requisitos, ciclo de vida, consultas y pruebas. | T-FUNC-01, T-MOD-01 |
| Cambio de sesiones o CSRF | Seguridad, dominio, interfaces y ADR-004. | T-AUTH-01, T-SEC-02 |
| Sustitución del mock SIS | Contexto, contenedores, despliegue e interfaces. | T-SIS-01, T-DEP-01 |
| Cambio de topología | V-DEP y deployment-assumptions.md. | T-DEP-01, T-REC-01, T-PERF-01 según impacto |

Los cambios deberán conservar su motivo, alcance y versión documental.

No se reutilizarán automáticamente resultados anteriores cuando se modifiquen condiciones relevantes de su ejecución.

### 7.2. Ejemplo de recorrido completo

El requisito **FR-11 — Resolution** permite mostrar cómo se relacionan una necesidad, las decisiones arquitectónicas y las pruebas.

**Necesidad y regla de negocio:** FR-11 establece la resolución del caso después de proporcionar orientación y verificar que no existan acciones pendientes. BR-05 refuerza esta restricción.

**Drivers:** AD-01 y AD-04 convierten la integridad del ciclo de vida y la consistencia del seguimiento en factores que orientan el diseño.

**Decisiones:** ADR-003 establece transiciones controladas y consistencia transaccional. ADR-005 exige registrar los eventos obligatorios de auditoría.

**Componentes:** CMP-03, CMP-05 y CMP-09 coordinan la resolución. CMP-08 registra el evento correspondiente dentro de la transacción.

**Datos y comportamiento:** Domain and Data Model define las entidades y el bloqueo común del caso. Lifecycle and Runtime establece las condiciones de resolución: estado `IN_PROGRESS`, Recommendation registrada, versión vigente, ausencia de FollowUps `PENDING` y ausencia de InformationRequests `OPEN`.

**Interfaz:** API Contracts propone la operación de resolución y las respuestas `409 Conflict` cuando existen versiones desactualizadas o condiciones de negocio incumplidas.

**Validación:** T-LIFE-01 comprobará las guardas de cierre. T-LIFE-02 evaluará la consistencia cuando resolución y seguimiento compitan sobre un mismo caso.

Cuando se ejecuten estas pruebas, `validation/findings.md` deberá enlazar sus resultados y evidencias.

Hasta entonces, el recorrido demuestra trazabilidad documental, no cumplimiento técnico comprobado.

### 7.3. Criterios de revisión de la matriz

La matriz deberá revisarse utilizando los siguientes criterios:

1. Cada FR tiene una responsabilidad de diseño y al menos una verificación prevista.
2. Cada ADR responde a una necesidad identificable y permite evaluar sus consecuencias.
3. Cada prueba está relacionada con un criterio observable.
4. AD y ADR conservan significados distintos.
5. Las decisiones abiertas no se presentan como políticas institucionales aprobadas.
6. Los resultados futuros identifican revisión, entorno y alcance.
7. Las entidades, contratos y mecanismos utilizan nombres consistentes.
8. Los cambios de requisitos, reglas o pruebas actualizan las relaciones correspondientes sin atribuir automáticamente cobertura de implementación.

La matriz funcionará como herramienta de revisión bidireccional entre las necesidades, la solución propuesta y la evidencia futura.

## 8. Documentos relacionados y mantenimiento

### 8.1. Documentos de referencia

- [Requirements and Drivers](requirements-and-drivers.md)
- [Quality Scenarios](quality-scenarios.md)
- [Architecture Decision Records](adr/README.md)
- [Architectural Views](views/README.md)
- [Domain and Data Model](design/domain-and-data.md)
- [Lifecycle and Runtime](design/lifecycle-and-runtime.md)
- [API Contracts](design/interfaces.md)
- [Security Design](design/security.md)
- [Validation Plan](validation/test-plan.md)
- [Validation Findings](validation/findings.md)
- [Project Status](project-status.md)

### 8.2. Actualización y conclusión

La matriz deberá actualizarse cuando cambien los requisitos, las decisiones arquitectónicas, los componentes, los contratos, las reglas de negocio, la implementación o las pruebas.

Los resultados futuros deberán incluir referencias a ejecuciones identificadas mediante RUN-ID y evidencia verificable.

Una relación documental no se convertirá automáticamente en una declaración de cumplimiento.

La cobertura se evaluará según la suficiencia y coherencia de los elementos relacionados, y no únicamente por la cantidad de referencias registradas.

El diseño de DH-01 establece conexiones trazables entre las necesidades académicas, los drivers, las decisiones arquitectónicas, los componentes, el modelo de datos y los procedimientos de validación.

La implementación y la conformidad técnica continúan pendientes de demostración conforme a la línea base documental registrada.

Este documento constituye la referencia central para revisar la consistencia de los artefactos de la Evaluación 2 y localizar la evidencia necesaria cuando se ejecute el prototipo.
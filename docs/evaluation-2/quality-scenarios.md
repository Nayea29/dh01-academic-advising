# DH-01 — Academic Advising Case Management System

# Quality Attribute Scenarios

## 1. Propósito

Este documento define los escenarios de calidad que orientan y permiten evaluar la arquitectura propuesta para el Academic Advising Case Management System.

Los escenarios se derivan de los requisitos funcionales, requisitos de calidad, reglas de negocio y restricciones identificados durante el análisis del proyecto.

El objetivo es transformar atributos generales, como seguridad, confidencialidad, trazabilidad, mantenibilidad, usabilidad y disponibilidad, en comportamientos observables que puedan verificarse mediante pruebas o actividades de evaluación.

Cada escenario describe una situación específica, la respuesta esperada del sistema y una medida que permite determinar si la solución satisface el objetivo propuesto.

Los valores cuantitativos incluidos constituyen objetivos iniciales de diseño, no resultados medidos ni niveles de servicio oficialmente aprobados por Horizon University.

## 2. Estructura de los escenarios de calidad

Los escenarios se describen mediante seis elementos:

| Elemento | Descripción |
|---|---|
| Source | Actor o elemento que genera el estímulo. |
| Stimulus | Evento, condición o acción que debe atender el sistema. |
| Environment | Condiciones bajo las cuales ocurre el estímulo. |
| Artifact | Componente, recurso o parte de la arquitectura afectada. |
| Response | Comportamiento esperado del sistema. |
| Response Measure | Criterio observable o cuantificable para evaluar la respuesta. |

Esta estructura permite relacionar las expectativas de calidad con mecanismos arquitectónicos concretos y actividades de validación.

## 3. Escenarios de calidad prioritarios

### QS-01 — Protection of Confidential Advising Information

**Atributos de calidad:** Security y Confidentiality.

**Requisitos relacionados:** QR-01, QR-02, FR-12, FR-14, BR-07 y BR-08.

**Architecture Driver:** AD-02 — Confidentiality and Controlled Access.

**Descripción:**

El sistema debe impedir que usuarios sin autorización consulten notas internas u otra información confidencial asociada a los casos académicos.

La protección debe aplicarse en el backend y no depender exclusivamente de los elementos visibles en la interfaz.

| Elemento | Descripción |
|---|---|
| Source | Un estudiante autenticado o un usuario institucional sin permiso sobre las notas de un caso. |
| Stimulus | El usuario intenta consultar información interna mediante una solicitud directa a la API. |
| Environment | El sistema está funcionando normalmente y el usuario cuenta con una sesión válida, pero no posee autorización para acceder al recurso solicitado. |
| Artifact | Módulo de autorización, API de gestión de casos y mecanismo de acceso a datos. |
| Response | El backend verifica la identidad, el rol, la relación con el caso y el tipo de información solicitada. La operación se rechaza o se entrega únicamente la información autorizada, según el contrato del endpoint. |
| Response Measure | El 100 % de los intentos no autorizados incluidos en la matriz de pruebas deben ser rechazados o filtrados correctamente, sin exposición de campos internos ni modificación de datos protegidos. |

**Mecanismos arquitectónicos propuestos:**

- Autenticación de usuarios.
- Autorización basada en roles y relación con el caso.
- Verificación de permisos en el backend.
- Separación entre notas internas y recomendaciones visibles para estudiantes.
- Respuestas de API limitadas a los campos autorizados.
- Registro de eventos de seguridad relevantes sin exponer información confidencial.

**Validación prevista — T-SEC-01:**

1. Crear un caso con información y notas internas utilizando datos sintéticos.
2. Autenticar un usuario con rol Student asociado al caso.
3. Solicitar el detalle autorizado del caso.
4. Verificar que la respuesta no incluya notas internas.
5. Intentar consultar directamente el recurso de notas internas.
6. Verificar que la solicitud sea rechazada.
7. Repetir los intentos con usuarios sin relación con el caso.

**Resultado esperado:** ningún usuario sin autorización accede a información interna del asesor.

**Estado:** Definido; prueba no ejecutada.

---

### QS-02 — Case Lifecycle and Resolution Integrity

**Atributos de calidad:** Reliability y Data Integrity.

**Requisitos relacionados:** FR-08, FR-11 y BR-05.

**Architecture Drivers:** AD-01 — Case Lifecycle Integrity y AD-04 — Follow-up and Resolution Consistency.

**Descripción:**

El sistema debe impedir que un caso sea marcado como resuelto cuando todavía existan actividades de asesoría pendientes.

Esta condición debe mantenerse incluso cuando se produzcan solicitudes concurrentes o se intente modificar el estado directamente mediante la API.

| Elemento | Descripción |
|---|---|
| Source | Academic Adviser autorizado para gestionar un caso. |
| Stimulus | El adviser intenta resolver un caso que tiene al menos un follow-up obligatorio pendiente. |
| Environment | El caso está activo, el usuario está autenticado y la base de datos está disponible. |
| Artifact | Case Management Service, Follow-up Management y base de datos. |
| Response | El sistema verifica las actividades pendientes dentro de una operación consistente y rechaza la resolución cuando las condiciones de cierre no se cumplen. |
| Response Measure | En el 100 % de los escenarios de prueba con acciones obligatorias pendientes, la resolución debe rechazarse y el caso debe conservar un estado activo. No deben registrarse resoluciones contradictorias con los follow-ups existentes. |

**Mecanismos arquitectónicos propuestos:**

- Validaciones centralizadas de reglas de negocio.
- Modelo explícito de estados y transiciones.
- Relaciones entre casos y follow-ups.
- Operaciones transaccionales para cambios de estado relevantes.
- Control de concurrencia sobre operaciones que afecten el cierre y las actividades pendientes.

**Validación prevista — T-LIFE-01:**

1. Crear y asignar un caso.
2. Registrar un follow-up pendiente.
3. Intentar resolver el caso.
4. Comprobar que la operación sea rechazada.
5. Completar el follow-up.
6. Intentar nuevamente la resolución.
7. Verificar que el caso pueda pasar a RESOLVED.
8. Comprobar que el historial registre la transición válida.

**Validación adicional — T-LIFE-02:**

Ejecutar concurrentemente una operación que cree un follow-up obligatorio y otra que intente resolver el mismo caso. Verificar que el estado final sea consistente: el sistema no debe conservar simultáneamente un caso RESOLVED y una acción obligatoria pendiente creada válidamente antes de su cierre.

**Resultado esperado:** ninguna resolución válida incumple las reglas de negocio sobre seguimiento pendiente.

**Estado:** Definido; pruebas no ejecutadas.

---

### QS-03 — Case History and Reassignment Traceability

**Atributo de calidad:** Traceability.

**Requisitos relacionados:** QR-03, FR-05, FR-09, FR-10 y BR-06.

**Architecture Driver:** AD-03 — Traceability and Historical Consistency.

**Descripción:**

El sistema debe conservar un historial consistente de las operaciones relevantes sobre los casos académicos, especialmente los cambios de estado y de responsable.

Los registros históricos deben permitir reconstruir el desarrollo de un caso sin depender de información almacenada en herramientas externas.

| Elemento | Descripción |
|---|---|
| Source | Advising Coordinator autorizado. |
| Stimulus | Se reasigna un caso desde un Academic Adviser hacia otro. |
| Environment | El caso existe, tiene un adviser responsable y contiene acciones históricas previamente registradas. |
| Artifact | Assignment Service, Case History y persistencia de datos. |
| Response | El sistema actualiza el responsable, conserva las asignaciones anteriores y genera un evento histórico vinculado al caso. |
| Response Measure | El 100 % de las reasignaciones aceptadas durante las pruebas debe conservar el responsable anterior, el nuevo responsable, el actor que autorizó la operación, la fecha y el motivo registrado. Ninguna reasignación válida debe eliminar eventos históricos existentes. |

**Mecanismos arquitectónicos propuestos:**

- Registro persistente de eventos históricos.
- Relación entre cada evento y el caso correspondiente.
- Registro de actor, fecha y operación.
- Actualización de responsabilidad e historial dentro de una misma transacción.
- Consulta de historial con control de acceso.

**Validación prevista — T-AUD-01:**

1. Crear un caso.
2. Asignarlo al Adviser A.
3. Registrar una acción de seguimiento.
4. Reasignarlo al Adviser B.
5. Consultar el caso y su historial.
6. Confirmar que el responsable actual sea el Adviser B.
7. Verificar que el historial conserve la asignación anterior, la acción de seguimiento y el motivo de reasignación.

**Resultado esperado:** la reasignación modifica la responsabilidad actual sin destruir la historia del caso.

**Estado:** Definido; prueba no ejecutada.

---

### QS-04 — Operational Query Performance

**Atributo de calidad:** Performance Efficiency.

**Requisitos relacionados:** FR-12, FR-13, QR-05 y QR-06.

**Architecture Driver:** AD-06 — Operational Visibility.

**Descripción:**

El sistema debe proporcionar consultas operacionales con tiempos de respuesta adecuados para que los coordinadores puedan revisar casos, asignaciones, actividades pendientes y distribución de carga.

El RFP establece una población institucional aproximada de 12.000 estudiantes, entre 20 y 30 advisers y entre 8 y 12 Program Coordinators. Estas cifras se utilizarán como referencia de planificación, pero no constituyen una carga concurrente medida.

Para la validación inicial se propone una carga de prueba sintética y acotada.

| Elemento | Descripción |
|---|---|
| Source | Advising Coordinator autenticado. |
| Stimulus | Solicita una lista filtrada de casos activos y retrasados, incluyendo responsable, categoría y estado. |
| Environment | Entorno de prueba estable, con una base de datos que contiene 12.000 casos sintéticos y 20 consultas concurrentes durante cinco minutos. |
| Artifact | Reporting Queries, API REST y base de datos. |
| Response | El sistema ejecuta consultas filtradas y paginadas y devuelve únicamente la información autorizada. |
| Response Measure | Objetivo propuesto: al menos el 95 % de las solicitudes deben obtener una respuesta del servidor en menos de 800 ms, sin errores de consulta, bajo la carga y el entorno especificados. |

**Mecanismos arquitectónicos propuestos:**

- Consultas con filtros y paginación.
- Índices sobre campos frecuentemente utilizados.
- Evitar consultas innecesarias a la base de datos.
- Separación lógica entre operaciones de modificación y consultas operacionales.
- Medición de los tiempos de respuesta.

**Validación prevista — T-PERF-01:**

1. Crear una base de datos con 12.000 casos sintéticos.
2. Distribuir los casos entre distintos estados, categorías y asesores.
3. Ejecutar 20 solicitudes concurrentes contra el endpoint de consulta durante cinco minutos.
4. Registrar tiempos de respuesta, percentil 95 y errores.
5. Comparar los resultados con el objetivo establecido.

**Limitación:** la prueba permitirá evaluar el comportamiento en un entorno específico, pero no demostrará capacidad institucional de producción ni disponibilidad bajo cualquier condición.

**Estado:** Objetivo propuesto; prueba no ejecutada.

---

### QS-05 — Maintainability and Policy Evolution

**Atributo de calidad:** Maintainability.

**Requisitos relacionados:** QR-04, C-07 y C-11.

**Architecture Driver:** AD-05 — Maintainability and Policy Evolution.

**Descripción:**

El sistema debe permitir modificar reglas operacionales sin generar cambios innecesarios en componentes independientes.

Las categorías de clasificación y los criterios utilizados para identificar casos retrasados constituyen ejemplos de políticas que podrían evolucionar.

| Elemento | Descripción |
|---|---|
| Source | Equipo de desarrollo, a partir de una actualización de política autorizada. |
| Stimulus | Se modifica una categoría de clasificación o un parámetro configurable utilizado para determinar el retraso de un caso. |
| Environment | Sistema en entorno de desarrollo con módulos y pruebas disponibles. |
| Artifact | Módulo de clasificación, configuración de políticas y consultas operacionales. |
| Response | El cambio se realiza en el componente o configuración correspondiente, sin requerir modificaciones en módulos que no dependen de esa política. |
| Response Measure | Para el cambio controlado seleccionado, no deben modificarse componentes independientes de autenticación, historial o gestión de sesiones. Todas las pruebas de regresión relacionadas deben superar sus verificaciones. |

**Mecanismos arquitectónicos propuestos:**

- Separación de responsabilidades.
- Módulos con interfaces definidas.
- Reglas de negocio centralizadas.
- Configuraciones validadas para políticas modificables.
- Pruebas unitarias y de integración.
- Documentación de dependencias entre componentes.

**Validación prevista — T-MOD-01:**

1. Seleccionar una configuración de clasificación o retraso.
2. Modificar un valor permitido.
3. Ejecutar las pruebas del módulo afectado.
4. Ejecutar las pruebas de regresión relacionadas.
5. Revisar los archivos modificados y las dependencias afectadas.
6. Confirmar que los componentes independientes no hayan requerido modificaciones.

**Resultado esperado:** la modificación controlada de una política tiene un impacto limitado y verificable.

**Estado:** Definido; prueba no ejecutada.

---

### QS-06 — Case Status Usability

**Atributo de calidad:** Usability.

**Requisitos relacionados:** QR-05, FR-04, FR-08 y FR-12.

**Architecture Driver:** AD-06 — Operational Visibility.

**Descripción:**

Los estudiantes y asesores deben poder identificar claramente el estado de una solicitud y las acciones necesarias para continuar el proceso.

La interfaz debe utilizar información consistente con el estado real registrado por el backend.

| Elemento | Descripción |
|---|---|
| Source | Estudiante o Academic Adviser que utiliza el sistema. |
| Stimulus | El usuario consulta un caso para conocer su estado, responsable o acciones pendientes. |
| Environment | Interfaz web operativa, con datos sintéticos y una sesión válida. |
| Artifact | Interfaz web, API de consulta y modelo de estado del caso. |
| Response | El sistema presenta el estado general con una descripción comprensible y muestra únicamente la información y las acciones correspondientes al usuario. |
| Response Measure | En una prueba formativa propuesta con cinco participantes, al menos cuatro deben identificar correctamente el estado general del caso y la siguiente acción esperada, cuando corresponda, sin asistencia del evaluador. |

**Mecanismos arquitectónicos propuestos:**

- Estados de negocio claramente definidos.
- Respuestas de API consistentes.
- Presentación de información adaptada al rol.
- Separación entre información interna y visible para estudiantes.
- Mensajes comprensibles para operaciones fallidas.
- Identificación visible del responsable cuando el usuario esté autorizado para conocerlo.

**Validación prevista — T-UX-01:**

1. Preparar casos sintéticos en diferentes estados.
2. Presentar a los participantes tareas de consulta de estado.
3. Solicitarles identificar el responsable, el progreso y la siguiente acción.
4. Registrar las respuestas correctas y dificultades observadas.
5. Identificar problemas de presentación o terminología.

**Resultado esperado:** los usuarios comprenden el progreso de sus solicitudes y las acciones que corresponden a su rol.

**Estado:** Prueba planificada; no ejecutada.

---

### QS-07 — Availability and Recovery

**Atributo de calidad:** Availability.

**Requisitos relacionados:** QR-06, C-05 y C-08.

**Architecture Driver:** AD-07 — Deployment Feasibility.

**Descripción:**

El sistema debe conservar la información persistente y permitir reanudar las operaciones después de una interrupción temporal de sus procesos de aplicación.

La prueba inicial se concentrará en la recuperación ante reinicios controlados, sin asumir que el entorno cuenta con alta disponibilidad.

| Elemento | Descripción |
|---|---|
| Source | Interrupción o reinicio controlado del servicio backend. |
| Stimulus | El proceso de la API se detiene y posteriormente se reinicia. |
| Environment | Entorno de prueba con frontend, backend y base de datos persistente; la base de datos permanece disponible. |
| Artifact | Backend API, infraestructura de ejecución y base de datos. |
| Response | Durante la interrupción las operaciones dependientes de la API dejan de estar disponibles. Después del reinicio, el sistema restablece la conexión y permite consultar los casos previamente registrados. |
| Response Measure | Objetivo propuesto: el servicio debe recuperar su capacidad de respuesta dentro de los 60 segundos posteriores al reinicio controlado, sin pérdida de casos previamente confirmados como persistidos. |

**Mecanismos arquitectónicos propuestos:**

- Persistencia independiente del proceso de aplicación.
- Configuración reproducible del entorno.
- Manejo adecuado de conexiones a la base de datos.
- Endpoint de comprobación de salud del servicio.
- Procedimientos documentados de inicio y recuperación.

**Validación prevista — T-REC-01:**

1. Iniciar el sistema y registrar casos sintéticos.
2. Confirmar que los registros fueron persistidos.
3. Detener el backend.
4. Reiniciarlo sin eliminar ni recrear la base de datos.
5. Medir el tiempo necesario para recuperar una respuesta válida.
6. Consultar los registros existentes y comprobar su integridad.

**Limitación:** esta prueba no demuestra recuperación ante pérdida de la base de datos, tolerancia a fallos de infraestructura ni un porcentaje determinado de disponibilidad mensual.

**Estado:** Objetivo propuesto; prueba no ejecutada.

## 4. Matriz consolidada de escenarios

| ID | Atributo principal | Driver | Validación | Prioridad |
|---|---|---|---|---|
| QS-01 | Security / Confidentiality | AD-02 | T-SEC-01 | Crítica |
| QS-02 | Reliability / Integrity | AD-01, AD-04 | T-LIFE-01, T-LIFE-02 | Crítica |
| QS-03 | Traceability | AD-03 | T-AUD-01 | Alta |
| QS-04 | Performance Efficiency | AD-06 | T-PERF-01 | Media |
| QS-05 | Maintainability | AD-05 | T-MOD-01 | Alta |
| QS-06 | Usability | AD-06 | T-UX-01 | Media |
| QS-07 | Availability / Recovery | AD-07 | T-REC-01 | Media |

Se priorizan especialmente la confidencialidad, la consistencia del ciclo de vida y la trazabilidad, dado que su incumplimiento afectaría directamente las capacidades centrales del sistema.

## 5. Relación entre escenarios y decisiones arquitectónicas

Los escenarios de calidad funcionan como criterios para justificar las decisiones técnicas.

| Escenario | Necesidad arquitectónica | Decisión que deberá justificarse |
|---|---|---|
| QS-01 | Proteger datos según rol y relación con el caso. | Estrategia de autenticación y autorización. |
| QS-02 | Garantizar reglas de cierre y transiciones consistentes. | Modelo de dominio, transacciones y control de concurrencia. |
| QS-03 | Conservar historial durante operaciones relevantes. | Diseño de persistencia y auditoría. |
| QS-04 | Consultar información operacional eficientemente. | Estrategia de consultas, paginación e índices. |
| QS-05 | Reducir el impacto de cambios de política. | Organización modular y configuración controlada. |
| QS-06 | Presentar estados claros y consistentes. | Contratos de consulta y diseño de interfaces. |
| QS-07 | Recuperar el servicio sin perder datos persistidos. | Estrategia de despliegue y persistencia. |

Las alternativas y decisiones seleccionadas se documentarán en los Architecture Decision Records (ADR).

## 6. Estrategia de validación

La validación deberá concentrarse inicialmente en los escenarios que representan mayores riesgos para el funcionamiento del sistema.

Se propone el siguiente orden:

**Primera prioridad — Confidencialidad**

Verificar que las restricciones de acceso se apliquen correctamente incluso cuando los usuarios realicen solicitudes directas a la API.

**Segunda prioridad — Integridad del ciclo de vida**

Comprobar que los casos no puedan resolverse cuando existan actividades obligatorias pendientes y que las operaciones concurrentes no generen estados contradictorios.

**Tercera prioridad — Trazabilidad**

Verificar que las asignaciones, reasignaciones y transiciones aceptadas conserven registros históricos completos.

**Cuarta prioridad — Mantenibilidad y operación**

Evaluar modificaciones controladas de políticas, consultas operacionales, comprensión del estado de los casos y recuperación después de reinicios.

Los procedimientos completos, herramientas utilizadas, condiciones del entorno y evidencias obtenidas se documentarán en `validation/test-plan.md` y `validation/findings.md`.

## 7. Estado de validación y limitaciones

Los escenarios presentados establecen comportamientos esperados y objetivos iniciales de calidad.

Hasta contar con resultados verificables, las métricas se consideran objetivos propuestos.

| Evidencia | Estado |
|---|---|
| Escenarios de calidad definidos | Documentado |
| Criterios de respuesta establecidos | Documentado |
| Objetivos cuantitativos propuestos | Documentado, pendiente de validación |
| Pruebas de seguridad | No ejecutadas |
| Pruebas de integridad y concurrencia | No ejecutadas |
| Pruebas de trazabilidad | No ejecutadas |
| Pruebas de rendimiento | No ejecutadas |
| Pruebas de mantenibilidad | No ejecutadas |
| Pruebas de usabilidad | No ejecutadas |
| Pruebas de recuperación | No ejecutadas |

La ejecución posterior deberá registrar el entorno, las versiones utilizadas, los datos sintéticos, los procedimientos y los resultados observados.

No se considerará demostrado un atributo de calidad únicamente por haber definido un componente, un diagrama o un caso de prueba.

## 8. Conclusión

Los escenarios de calidad permiten transformar requisitos generales en comportamientos concretos y verificables.

Para DH-01, la protección de información sensible, la integridad del ciclo de vida, la conservación del historial y la mantenibilidad constituyen factores particularmente relevantes para el diseño arquitectónico.

Las respuestas y métricas propuestas ofrecen criterios para comparar alternativas, establecer responsabilidades entre componentes y desarrollar una estrategia de validación.

Los resultados de las pruebas permitirán posteriormente identificar limitaciones, revisar decisiones de arquitectura y determinar si los mecanismos seleccionados satisfacen los objetivos establecidos.
# DH-01 — Academic Advising Case Management System

## Requirements, Constraints and Architecture Drivers

## 1. Propósito

El presente documento identifica y analiza los requisitos funcionales, atributos de calidad, reglas de negocio, restricciones y factores que influyen en el diseño arquitectónico del Academic Advising Case Management System.

Su propósito es establecer una relación clara entre las necesidades institucionales identificadas y las decisiones técnicas necesarias para construir una solución coherente, segura, mantenible y viable.

No todos los requisitos afectan de la misma manera a la arquitectura. Por esta razón, el análisis se concentra especialmente en aquellos que determinan la organización de los componentes, las responsabilidades del sistema, las estructuras de datos, los mecanismos de seguridad y las interacciones entre sus elementos.

Este documento constituye una referencia para la selección de la arquitectura, la elaboración de diagramas, la definición de interfaces y el diseño de las estrategias de validación.

## 2. Requisitos funcionales

Los requisitos funcionales describen las capacidades que el sistema debe proporcionar para gestionar las solicitudes de asesoría académica durante su ciclo de vida.

Se mantienen los identificadores establecidos en el catálogo de requisitos del proyecto, con el objetivo de preservar su trazabilidad.

| ID | Requisito | Descripción |
|---|---|---|
| FR-01 | Case Creation | Permitir crear solicitudes de asesoría académica. |
| FR-02 | Multiple Intake Origins | Permitir solicitudes iniciadas por estudiantes o mediante referencias autorizadas de Program Coordinators. |
| FR-03 | Request Classification | Clasificar solicitudes según su tipo y programa o unidad académica correspondiente. |
| FR-04 | Explicit Assignment | Establecer un Academic Adviser responsable para cada caso activo después del triage. |
| FR-05 | Case History | Conservar un historial de las acciones relevantes realizadas sobre cada caso. |
| FR-06 | Adviser Case Management | Permitir que el adviser revise información, registre orientación y recomendaciones y actualice acciones. |
| FR-07 | Additional Information | Permitir solicitar y registrar información adicional necesaria para atender un caso. |
| FR-08 | Follow-up Management | Crear, registrar y completar acciones de seguimiento. |
| FR-09 | Referral / Reassignment | Permitir referir o reasignar casos cuando el responsable no pueda continuar atendiéndolos. |
| FR-10 | Reassignment History | Conservar los cambios de responsabilidad sin eliminar el historial anterior. |
| FR-11 | Resolution | Permitir resolver un caso cuando se haya proporcionado orientación y no existan acciones pendientes. |
| FR-12 | Student Visibility | Permitir que el estudiante consulte el estado general y la información autorizada de sus casos. |
| FR-13 | Operational Monitoring | Proporcionar información operacional sobre casos abiertos, retrasos, carga de trabajo, tipos de solicitudes y volumen. |
| FR-14 | Access Control | Restringir el acceso según el rol, la relación del usuario con el caso y la sensibilidad de la información. |

Estos requisitos definen las capacidades funcionales consideradas dentro del alcance del sistema.

## 3. Requisitos funcionales arquitectónicamente significativos

Un requisito se considera arquitectónicamente significativo cuando influye directamente en la estructura del sistema, sus responsabilidades, sus mecanismos de persistencia, la comunicación entre componentes o el cumplimiento de atributos de calidad.

Se identifican los siguientes grupos principales.

### 3.1 Gestión del ciclo de vida de los casos

**Requisitos relacionados:** FR-01, FR-03, FR-04, FR-06 y FR-11.

El sistema debe administrar las solicitudes desde su creación hasta su resolución, permitiendo clasificaciones, asignaciones y cambios de estado controlados.

Esta necesidad requiere un modelo de dominio que represente explícitamente los casos académicos, sus estados y las operaciones permitidas durante su ciclo de vida.

La lógica de transición no debe depender únicamente de la interfaz de usuario, puesto que diferentes actores podrían ejecutar operaciones sobre un mismo caso.

**Implicaciones arquitectónicas:**

- Definir un componente responsable de la gestión de casos.
- Establecer estados y transiciones controladas.
- Centralizar las validaciones de reglas de negocio.
- Mantener consistencia entre el estado del caso y sus actividades.
- Evitar modificaciones no autorizadas o transiciones inválidas.

### 3.2 Asignación y reasignación

**Requisitos relacionados:** FR-03, FR-04, FR-09 y FR-10.

Cada caso activo que haya completado el proceso de clasificación debe contar con un responsable claramente identificable.

Cuando sea necesario cambiar de asesor, el sistema debe conservar la información del caso y registrar la modificación de responsabilidad.

La asignación requiere separar conceptualmente la identidad del caso de su responsable actual, puesto que este último puede cambiar durante el proceso.

**Implicaciones arquitectónicas:**

- Definir una relación explícita entre casos y asesores.
- Registrar el responsable actual.
- Conservar el historial de asignaciones anteriores.
- Controlar quién puede realizar una reasignación.
- Garantizar que el cambio de responsabilidad y su registro histórico se mantengan consistentes.

### 3.3 Gestión de seguimiento y resolución

**Requisitos relacionados:** FR-07, FR-08 y FR-11.

Las actividades de seguimiento pueden consistir en interacciones con estudiantes o acciones internas realizadas por los asesores.

El sistema debe permitir registrar estas actividades, consultar sus estados y determinar si existen acciones pendientes antes de resolver un caso.

**Implicaciones arquitectónicas:**

- Modelar los follow-ups como registros relacionados con un caso.
- Mantener estados individuales para las acciones de seguimiento.
- Incorporar fechas límite cuando correspondan.
- Validar las condiciones de resolución desde la lógica de negocio.
- Impedir que se registre una resolución válida mientras existan acciones obligatorias pendientes.

### 3.4 Confidencialidad y control de acceso

**Requisitos relacionados:** FR-02, FR-06, FR-12 y FR-14.

Los usuarios necesitan diferentes niveles de acceso según sus responsabilidades institucionales y su relación con cada solicitud.

En particular, las notas internas de los asesores no deben estar disponibles para los estudiantes ni para actores que carezcan de autorización.

**Implicaciones arquitectónicas:**

- Incorporar mecanismos de autenticación.
- Implementar autorización en el backend.
- Verificar roles y relaciones con los casos.
- Separar la información visible para estudiantes de los registros internos.
- Controlar permisos tanto de lectura como de modificación.
- Evitar que las respuestas de la API incluyan campos restringidos para el usuario solicitante.

### 3.5 Trazabilidad e información operacional

**Requisitos relacionados:** FR-05, FR-10 y FR-13.

La universidad necesita conocer qué acciones se realizaron sobre cada caso, quién las ejecutó y cómo cambió su estado o responsabilidad.

Adicionalmente, el Advising Coordinator necesita consultar información sobre el funcionamiento general del servicio.

**Implicaciones arquitectónicas:**

- Mantener eventos históricos de acciones relevantes.
- Registrar actor, fecha, operación y referencia al caso.
- Diseñar consultas operacionales sobre datos estructurados.
- Controlar la visibilidad de los reportes.
- Evitar que la información histórica se pierda durante una reasignación o resolución.

## 4. Requisitos de calidad

Los atributos de calidad representan propiedades que el sistema debe mantener durante su funcionamiento y evolución.

| ID | Atributo | Descripción |
|---|---|---|
| QR-01 | Security | Proteger la información sensible frente a accesos no autorizados. |
| QR-02 | Confidentiality | Evitar que usuarios no autorizados consulten notas internas. |
| QR-03 | Traceability | Permitir reconstruir acciones y cambios relevantes del caso. |
| QR-04 | Maintainability | Facilitar el mantenimiento con recursos técnicos limitados. |
| QR-05 | Usability | Permitir identificar claramente el estado y responsable de los casos. |
| QR-06 | Availability | Facilitar el acceso al sistema para consultar y gestionar solicitudes cuando sea necesario. |

### 4.1 Importancia arquitectónica

**Security y Confidentiality — QR-01 / QR-02**

Estos atributos requieren una arquitectura que no confíe exclusivamente en las restricciones de la interfaz. La autorización debe aplicarse a las operaciones protegidas y al acceso a los datos.

**Traceability — QR-03**

La trazabilidad requiere persistencia histórica y mecanismos que permitan relacionar las acciones con los usuarios, los casos y los cambios realizados.

**Maintainability — QR-04**

Debido a los recursos técnicos institucionales limitados, el sistema debe organizar sus responsabilidades de manera que las modificaciones puedan realizarse sin afectar innecesariamente funcionalidades independientes.

**Usability — QR-05**

La arquitectura debe proporcionar información consistente sobre el estado, la responsabilidad y las acciones pendientes, para que las interfaces puedan presentar datos comprensibles y actualizados.

**Availability — QR-06**

La solución debe contemplar condiciones de despliegue, persistencia y recuperación compatibles con el acceso remoto y las necesidades operacionales del servicio.

Las métricas y condiciones de verificación de estos atributos se desarrollan en `quality-scenarios.md`.

## 5. Reglas de negocio

Las reglas de negocio establecen las condiciones que deben cumplirse durante las operaciones del sistema.

### 5.1 Reglas identificadas

| ID | Regla |
|---|---|
| BR-01 | Antes de establecer la asignación definitiva debe realizarse el proceso de clasificación o triage. |
| BR-02 | La clasificación debe considerar el tipo de solicitud y el programa o unidad correspondiente. |
| BR-03 | La mayoría de los casos tendrá un adviser principal, permitiendo referral o reassignment cuando sea necesario. |
| BR-04 | Un follow-up puede consistir en una actividad con el estudiante o una acción interna del adviser. |
| BR-05 | Un caso no debe considerarse resuelto mientras existan acciones de asesoría pendientes. |
| BR-06 | Los cambios de responsabilidad deben quedar registrados en el historial. |
| BR-07 | Los estudiantes no pueden consultar las notas internas del personal. |
| BR-08 | El acceso a información sensible depende del rol, la relación con el caso y la sensibilidad de la información. |
| BR-09 | Las reglas definitivas de overdue, escalation, reopening, estados y permisos requieren validación institucional. |

### 5.2 Reglas complementarias propuestas

A partir del análisis de las necesidades operacionales, se plantean las siguientes reglas adicionales como propuestas de diseño.

Estas reglas no sustituyen las anteriores y permanecen sujetas a validación institucional.

| ID | Regla propuesta | Justificación |
|---|---|---|
| PBR-01 | El Advising Coordinator supervisará el triage, permitiendo delegación explícita a personal autorizado. | Evitar dependencia exclusiva de un usuario y mantener responsabilidades claras. |
| PBR-02 | La asignación inicial de advisers será manual y tendrá en cuenta programa, categoría y carga de trabajo. | Evitar automatizar decisiones cuyos criterios todavía no están completamente definidos. |
| PBR-03 | Los estados del caso deberán cambiar mediante transiciones controladas. | Mantener consistencia en el ciclo de vida. |
| PBR-04 | Los criterios de overdue deberán poder configurarse según las políticas institucionales. | Permitir diferentes tiempos de atención según el contexto. |
| PBR-05 | El escalamiento y la reasignación serán operaciones diferenciadas. | No todo escalamiento implica un cambio de responsable. |
| PBR-06 | Las reasignaciones deberán ser autorizadas por el Advising Coordinator o un rol expresamente delegado. | Controlar los cambios de responsabilidad. |
| PBR-07 | Las notas internas permanecerán separadas de las recomendaciones visibles para estudiantes. | Proteger la confidencialidad. |
| PBR-08 | La reapertura de casos resueltos se considerará una extensión del ciclo de vida, sujeta a reglas específicas. | Evitar incorporar un comportamiento aún no definido completamente. |
| PBR-09 | Las categorías y prioridades podrán administrarse mediante configuraciones controladas. | Facilitar la evolución de las políticas de clasificación. |

## 6. Restricciones arquitectónicas

Las restricciones determinan los límites dentro de los cuales debe diseñarse e implementarse la solución.

| ID | Restricción | Consecuencia arquitectónica |
|---|---|---|
| C-01 | Utilizar datos sintéticos. | Los entornos de desarrollo y validación no dependerán de registros reales de estudiantes. |
| C-02 | Autenticación institucional o equivalente simulado. | Se requiere una interfaz de autenticación que permita representar identidades verificables. |
| C-03 | Acceso según rol y relación con el caso. | La autorización debe considerar tanto las funciones del usuario como el recurso solicitado. |
| C-04 | Protección de información sensible. | Se necesitan límites explícitos de acceso y exposición de datos. |
| C-05 | Independencia de otros proyectos. | El sistema debe poder ejecutarse y mantenerse sin depender de aplicaciones desarrolladas por otros equipos. |
| C-06 | Integración simulada con el SIS. | Las dependencias institucionales deben poder reemplazarse por implementaciones de prueba. |
| C-07 | Personal técnico limitado. | Se debe controlar la complejidad operacional. |
| C-08 | Acceso remoto. | La arquitectura debe contemplar comunicación de red segura. |
| C-09 | Viabilidad en un semestre. | Se debe priorizar una implementación incremental de las capacidades esenciales. |
| C-10 | Trazabilidad de acciones relevantes. | Los cambios de estado y responsabilidad deben conservar evidencia persistente. |
| C-11 | Evolución de políticas institucionales. | Las reglas modificables deben mantenerse organizadas y desacopladas cuando sea razonable. |

## 7. Supuestos de diseño

Los siguientes supuestos permiten desarrollar una propuesta arquitectónica coherente mientras se obtiene una definición institucional definitiva de determinadas reglas.

| ID | Supuesto | Impacto en el diseño | Estado |
|---|---|---|---|
| AS-01 | Se utilizarán siete estados principales para el ciclo de vida del caso. | Modelo de estados y transiciones. | Propuesto |
| AS-02 | El Advising Coordinator supervisará el triage. | Permisos y responsabilidades. | Propuesto |
| AS-03 | La asignación de asesores será manual inicialmente. | Componente de asignación y flujo operacional. | Propuesto |
| AS-04 | Los criterios de retraso serán configurables. | Seguimiento y consultas operacionales. | Propuesto |
| AS-05 | El escalamiento no cambiará automáticamente al asesor responsable. | Separación entre escalamiento y reasignación. | Propuesto |
| AS-06 | El estudiante no tendrá acceso a notas internas. | Autorización y contratos de consulta. | Respaldado por BR-07 |
| AS-07 | La reapertura se tratará inicialmente como una extensión del flujo principal. | Alcance de implementación y modelo de estados. | Propuesto |
| AS-08 | Los períodos de retención dependerán de políticas institucionales posteriores. | Ciclo de vida y conservación de datos. | Pendiente de política |

La implementación debe permitir revisar estas decisiones sin introducir modificaciones innecesarias en componentes que no estén relacionados.

## 8. Architecture Drivers

Los architecture drivers son los factores que tienen mayor influencia sobre las decisiones estructurales del sistema.

Se derivan de los requisitos funcionales, los atributos de calidad, las reglas de negocio y las restricciones del proyecto.

### AD-01 — Case Lifecycle Integrity

**Origen:** FR-01, FR-03, FR-04, FR-08, FR-11, BR-01 y BR-05.

El sistema debe mantener un ciclo de vida consistente y evitar operaciones incompatibles con el estado actual de un caso.

Esto requiere reglas de transición centralizadas y validaciones de negocio ejecutadas en el backend.

**Consecuencia arquitectónica:** establecer un módulo de gestión de casos responsable de las operaciones y transiciones principales.

### AD-02 — Confidentiality and Controlled Access

**Origen:** FR-12, FR-14, QR-01, QR-02, BR-07 y BR-08.

La información disponible para cada usuario depende de sus permisos y de su relación con el caso.

La arquitectura debe separar la autenticación de la autorización y controlar el acceso a recursos específicos.

**Consecuencia arquitectónica:** incorporar mecanismos centralizados de autorización y definir contratos de consulta que no expongan información restringida.

### AD-03 — Traceability and Historical Consistency

**Origen:** FR-05, FR-09, FR-10, QR-03 y BR-06.

Las operaciones relevantes deben conservar un historial que permita reconstruir el desarrollo de un caso.

**Consecuencia arquitectónica:** implementar un mecanismo persistente de eventos históricos, vinculado a las operaciones que modifican la información de los casos.

### AD-04 — Follow-up and Resolution Consistency

**Origen:** FR-07, FR-08, FR-11, BR-04 y BR-05.

La resolución de una solicitud depende de que se hayan completado las acciones de asesoría necesarias.

**Consecuencia arquitectónica:** modelar las actividades de seguimiento como entidades relacionadas y validar las condiciones de cierre mediante reglas de dominio y operaciones consistentes.

### AD-05 — Maintainability and Policy Evolution

**Origen:** QR-04, C-07, C-09 y C-11.

La solución debe mantenerse con recursos técnicos limitados y permitir la evolución de determinadas políticas institucionales.

**Consecuencia arquitectónica:** organizar el sistema en componentes con responsabilidades explícitas y mantener las reglas configurables separadas de la lógica que no depende de ellas.

### AD-06 — Operational Visibility

**Origen:** FR-04, FR-08, FR-12, FR-13 y QR-05.

El sistema debe proporcionar información clara y consistente sobre casos, responsables, actividades pendientes y resultados operacionales.

**Consecuencia arquitectónica:** definir consultas específicas para las necesidades de estudiantes, asesores y coordinadores, aplicando filtros y restricciones de acceso.

### AD-07 — Deployment Feasibility

**Origen:** QR-06, C-05, C-06, C-08 y C-09.

El sistema debe ser viable dentro del período disponible, permitir acceso remoto y funcionar independientemente de otros proyectos.

**Consecuencia arquitectónica:** priorizar una arquitectura con responsabilidades de despliegue manejables y dependencias externas claramente identificadas.

## 9. Matriz de trazabilidad preliminar

La siguiente matriz conecta los principales requisitos y reglas con sus respectivos drivers arquitectónicos.

| Requisitos y reglas | Driver | Responsabilidad de diseño | Validación prevista |
|---|---|---|---|
| FR-01, FR-03, FR-04, BR-01 | AD-01 | Gestión del ciclo de vida y asignaciones. | Verificar clasificación previa a la asignación y transiciones válidas. |
| FR-11, BR-05 | AD-01, AD-04 | Validación de resolución. | Intentar resolver un caso con follow-ups pendientes. |
| FR-12, FR-14, BR-07, BR-08 | AD-02 | Autenticación, autorización y exposición controlada de datos. | Intentar consultar notas internas desde una cuenta de estudiante. |
| FR-05, FR-09, FR-10, BR-06 | AD-03 | Historial y reasignación. | Verificar conservación del historial después de cambiar el responsable. |
| FR-07, FR-08, BR-04 | AD-04 | Gestión de follow-ups. | Registrar, consultar y completar acciones de seguimiento. |
| QR-04, C-07, C-11 | AD-05 | Modularidad y políticas configurables. | Revisar el impacto de modificar una regla de clasificación. |
| FR-12, FR-13, QR-05 | AD-06 | Consultas y visibilidad operacional. | Verificar que cada rol vea únicamente la información autorizada. |
| QR-06, C-05, C-08, C-09 | AD-07 | Infraestructura y despliegue. | Comprobar la ejecución independiente y el acceso mediante el entorno previsto. |

Esta matriz constituye una referencia inicial. Se ampliará cuando se documenten las decisiones arquitectónicas, los componentes definitivos y los escenarios específicos de validación.

## 10. Conclusión

El análisis de requisitos permite identificar que los principales desafíos arquitectónicos del Academic Advising Case Management System no se limitan a registrar solicitudes académicas.

La solución debe garantizar un ciclo de vida controlado, responsabilidad claramente identificable, seguimiento consistente, trazabilidad histórica y protección de información sensible.

Estos objetivos influyen directamente en la organización de los componentes, las estructuras de datos, los mecanismos de autorización y las operaciones que modifican el estado de los casos.

Los drivers identificados servirán como criterios para comparar alternativas arquitectónicas y justificar las decisiones de diseño.

La arquitectura seleccionada deberá demostrar cómo satisface estos factores, cuáles son sus limitaciones y qué mecanismos permitirán validar su funcionamiento.
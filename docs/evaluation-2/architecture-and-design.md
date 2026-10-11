# DH-01 — Academic Advising Case Management System

## Architecture and Design Document

**Institución:** Horizon University — Digital Horizon Initiative  


## Índice del documento

1. [Propósito y alcance del diseño](#1-propósito-y-alcance-del-diseño)
2. [Requisitos significativos y restricciones](#2-requisitos-significativos-y-restricciones)
3. [Escenarios de calidad y factores arquitectónicos](#3-escenarios-de-calidad-y-factores-arquitectónicos)
4. [Estrategia arquitectónica y decisiones de diseño](#4-estrategia-arquitectónica-y-decisiones-de-diseño)
5. [Vistas arquitectónicas](#5-vistas-arquitectónicas)
6. [Dominio, datos y comportamiento](#6-dominio-datos-y-comportamiento)
7. [Interfaces y seguridad](#7-interfaces-y-seguridad)
8. [Supuestos de despliegue](#8-supuestos-de-despliegue)
9. [Validación, riesgos y plan de trabajo](#9-validación-riesgos-y-plan-de-trabajo)
10. [Trazabilidad, glosario y referencias](#10-trazabilidad-glosario-y-referencias)

# 1. Propósito y alcance del diseño

### 1.1 Introducción

El proyecto DH-01 — Academic Advising Case Management System forma parte de la iniciativa Digital Horizon de Horizon University y tiene como propósito proponer una solución tecnológica para mejorar la gestión de solicitudes de asesoría académica dirigidas a estudiantes de pregrado.

El análisis del contexto institucional permitió identificar dificultades relacionadas con la fragmentación de la información, la falta de claridad sobre la responsabilidad de los casos, el seguimiento de actividades pendientes y la limitada visibilidad operacional de los coordinadores.

A partir del análisis del Request for Proposal (RFP), el estudio del proceso actual (AS-IS), el proceso de discovery y la definición del proceso propuesto (TO-BE), se establecieron los requisitos funcionales, requisitos de calidad, reglas de negocio y límites del proyecto.

El presente documento desarrolla la propuesta de arquitectura y diseño del sistema, transformando las necesidades y requisitos identificados en una solución técnicamente estructurada, coherente y viable.

Se busca explicar cómo se organizará el sistema, cuáles serán las responsabilidades de sus componentes, cómo interactuarán entre sí, de qué manera se administrará la información y qué mecanismos permitirán satisfacer los requisitos funcionales y de calidad establecidos.

### 1.2 Propósito del documento

El propósito de este documento es presentar y justificar la arquitectura y el diseño propuestos para el Academic Advising Case Management System.

La documentación busca demostrar que las decisiones arquitectónicas responden directamente a los problemas institucionales, requisitos y restricciones identificados durante el análisis del proyecto.

Específicamente, se pretende:

1. Establecer la relación entre los requisitos del proyecto y las decisiones arquitectónicas.
2. Identificar los atributos de calidad que influyen en la estructura del sistema.
3. Evaluar alternativas arquitectónicas y justificar las decisiones seleccionadas.
4. Describir los componentes del sistema, sus responsabilidades y sus interacciones.
5. Proponer un modelo de dominio y de datos para administrar el ciclo de vida de los casos.
6. Definir los mecanismos de seguridad, confidencialidad y trazabilidad.
7. Documentar las interfaces, dependencias y condiciones de despliegue.
8. Establecer una estrategia de validación para evaluar la viabilidad del diseño.
9. Identificar los riesgos técnicos, las dependencias y las actividades necesarias para la implementación.

El resultado esperado es una propuesta arquitectónica que proporcione una base consistente para el desarrollo, mantenimiento y evolución del sistema.

### 1.3 Visión del sistema

El Academic Advising Case Management System se concibe como una aplicación web centralizada que permitirá gestionar las solicitudes de asesoría académica desde su creación hasta su resolución.

La solución busca proporcionar un proceso estructurado en el que cada caso pueda registrarse, clasificarse, asignarse a un responsable, recibir seguimiento y conservar un historial de las acciones realizadas.

Los estudiantes podrán consultar información autorizada sobre el estado de sus solicitudes, mientras que los Academic Advisers podrán administrar los casos bajo su responsabilidad, registrar recomendaciones y gestionar las actividades de seguimiento.

Por su parte, los coordinadores dispondrán de mecanismos de asignación, supervisión y consulta de información operacional según sus responsabilidades y permisos.

El sistema incorporará controles de acceso basados en roles, relación con los casos y sensibilidad de la información, con el objetivo de proteger los registros internos y evitar la divulgación de información confidencial.

La solución no pretende reemplazar los sistemas académicos institucionales existentes, sino proporcionar una herramienta especializada en la administración y trazabilidad del proceso de asesoría académica.

### 1.4 Alcance del diseño arquitectónico

La propuesta arquitectónica contempla las siguientes capacidades funcionales:

| Capacidad | Descripción |
|---|---|
| Centralized Case Intake | Registro centralizado de solicitudes de asesoría académica. |
| Multiple Intake Origins | Creación de solicitudes por estudiantes y referencias autorizadas de Program Coordinators. |
| Triage and Classification | Clasificación inicial según tipo de solicitud y programa o unidad académica. |
| Case Assignment | Asignación explícita de un Academic Adviser responsable. |
| Case Management and History | Administración de información, acciones, recomendaciones e historial de los casos. |
| Follow-up Management | Registro y seguimiento de acciones pendientes, tanto internas como dirigidas al estudiante. |
| Referral / Reassignment | Transferencia o reasignación de casos preservando su historial. |
| Student Case Visibility | Consulta del estado general y de la información autorizada por parte del estudiante. |
| Operational Monitoring | Supervisión de casos abiertos, retrasos, carga de trabajo y volumen de solicitudes. |
| Role and Relationship-Based Access | Protección de información mediante controles de acceso según rol, relación y sensibilidad. |

El diseño contempla tanto el flujo principal de gestión de casos como escenarios relevantes de seguridad, reasignación, seguimiento y resolución.

### Funcionalidades fuera del alcance

El proyecto no contempla:

- Reemplazo o implementación completa del Student Information System (SIS).
- Procesos de matrícula académica.
- Modificación de calificaciones o expedientes académicos oficiales.
- Prestación de servicios de asesoría médica o psicológica.
- Implementación de un calendario universitario completo.
- Desarrollo de modelos predictivos de riesgo académico.
- Integración completa con todos los sistemas institucionales existentes.
- Automatización integral de reglas de asignación que todavía no han sido validadas.

Estas exclusiones permiten concentrar el diseño en las necesidades centrales de gestión de casos y mantener la viabilidad del proyecto dentro de las restricciones de tiempo y recursos disponibles.

### 1.5 Enfoque y resultados esperados del diseño

El trabajo arquitectónico se concentra en definir una solución que responda a los requisitos del proyecto y permita evaluar su viabilidad técnica antes de avanzar hacia una implementación completa.

Para ello, se desarrollarán vistas arquitectónicas, modelos de datos, contratos de interfaces, escenarios de calidad, registros de decisiones y una estrategia inicial de validación.

Cuando resulte pertinente, podrán utilizarse prototipos o pruebas de concepto para comprobar decisiones arquitectónicas específicas.

Las funcionalidades implementadas y los resultados de pruebas ejecutadas se documentarán de manera independiente de las capacidades que permanezcan en fase de diseño.

El objetivo es establecer una base técnica consistente que facilite la implementación, reduzca incertidumbres, permita evaluar decisiones de diseño y mantenga una relación clara entre requisitos, arquitectura y validación.




# 2. Requisitos significativos y restricciones

### 2.1 Restricciones y condiciones del proyecto

La propuesta arquitectónica debe respetar las restricciones establecidas por Horizon University en el RFP DH-01.

| ID | Restricción | Implicación para el diseño |
|---|---|---|
| C-01 | Utilizar únicamente datos sintéticos de estudiantes. | La solución y las pruebas no dependerán de registros académicos reales. |
| C-02 | Utilizar autenticación institucional o un equivalente simulado. | La arquitectura deberá contemplar un mecanismo de autenticación adecuado. |
| C-03 | Restringir el acceso según rol y relación con el caso. | Los controles de autorización deberán aplicarse a las operaciones y datos protegidos. |
| C-04 | Proteger la información sensible. | El diseño deberá separar la información interna de la información visible al estudiante. |
| C-05 | Operar independientemente de otros proyectos estudiantiles. | La solución deberá contar con componentes y dependencias propias. |
| C-06 | Permitir la simulación de integración con el SIS. | No será obligatorio acceder a sistemas académicos reales. |
| C-07 | Considerar recursos técnicos institucionales limitados. | Se priorizarán la mantenibilidad y una complejidad operacional razonable. |
| C-08 | Soportar acceso remoto. | El sistema deberá contemplar acceso a través de una interfaz web y mecanismos de comunicación seguros. |
| C-09 | Ser viable dentro de un semestre académico. | Las decisiones arquitectónicas deberán considerar el tiempo y los recursos disponibles. |
| C-10 | Mantener trazabilidad de cambios importantes de estado y asignación. | El diseño deberá contemplar registros persistentes de acciones relevantes. |
| C-11 | Permitir futuras modificaciones de políticas. | Las reglas del sistema deberán organizarse para evolucionar sin cambios innecesarios en otros componentes. |

### 2.2 Supuestos y aspectos pendientes de validación

El análisis del proyecto permitió identificar aspectos que todavía requieren una definición institucional más precisa.

Entre ellos se encuentran:

- Los estados definitivos del ciclo de vida de un caso.
- Los criterios que determinan cuándo un caso está retrasado.
- Las condiciones y autoridades responsables del escalamiento.
- La matriz definitiva de permisos para cada rol.
- El responsable formal del proceso de triage.
- Las condiciones para reabrir casos resueltos.
- Las categorías y prioridades definitivas de clasificación.
- Las políticas de conservación de información después del cierre.

Estas cuestiones se mantendrán como decisiones abiertas o supuestos explícitos durante el diseño arquitectónico.

Cuando sea necesario plantear una solución provisional, se documentará su justificación, sus consecuencias y las actividades necesarias para validarla.

De esta manera, se evitará presentar decisiones técnicas propuestas por el equipo como si fueran políticas institucionales confirmadas.

### 2.3 Requisitos arquitectónicamente significativos

El análisis de requisitos del Academic Advising Case Management System permitió identificar siete factores principales que influyen en su arquitectura: integridad del ciclo de vida, confidencialidad y control de acceso, trazabilidad histórica, consistencia del seguimiento y resolución, mantenibilidad, visibilidad operacional y viabilidad del despliegue.

Estos factores se derivan de los requisitos funcionales, atributos de calidad, reglas de negocio y restricciones institucionales.

Entre los aspectos más relevantes se encuentran la necesidad de controlar las transiciones de estado, conservar el historial durante las reasignaciones, impedir la resolución de casos con acciones pendientes y proteger las notas internas frente a usuarios no autorizados.

Las decisiones arquitectónicas deberán responder a estas necesidades mediante componentes con responsabilidades claramente definidas, validaciones de negocio, mecanismos de persistencia consistentes y controles de autorización.

El análisis detallado de los requisitos y su relación con los architecture drivers se encuentra en [Requirements and Architecture Drivers](requirements-and-drivers.md).

## 3. Escenarios de calidad y factores arquitectónicos

Los atributos de calidad del Academic Advising Case Management System se han definido mediante escenarios que especifican situaciones concretas, respuestas esperadas y criterios observables de validación.

El análisis considera principalmente la confidencialidad, la integridad del ciclo de vida de los casos, la trazabilidad histórica, el rendimiento de las consultas operacionales, la mantenibilidad, la usabilidad y la recuperación del servicio.

Entre los escenarios de mayor prioridad se encuentran la prevención del acceso no autorizado a notas internas, La imposibilidad de resolver casos mientras existan actividades de seguimiento pendientes o solicitudes de información abiertas y la conservación del historial durante las reasignaciones.

Estos escenarios tienen consecuencias directas sobre la arquitectura. La confidencialidad requiere controles de autorización en el backend; la integridad del ciclo de vida necesita validaciones de negocio y operaciones consistentes; y la trazabilidad exige registrar los cambios relevantes junto con las operaciones que los producen.

Asimismo, las necesidades de mantenibilidad y viabilidad operacional influyen en la separación de responsabilidades, la organización modular y la selección de mecanismos de despliegue.

### 3.1 Escenarios prioritarios

| Escenario | Objetivo |
|---|---|
| QS-01 — Confidentiality | Impedir el acceso no autorizado a información interna. |
| QS-02 — Lifecycle Integrity | Evitar resoluciones y estados inconsistentes. |
| QS-03 — Traceability | Conservar el historial de acciones y reasignaciones. |
| QS-04 — Performance | Proporcionar consultas operacionales eficientes. |
| QS-05 — Maintainability | Facilitar la modificación de políticas y reglas. |
| QS-06 — Usability | Permitir comprender el estado y las acciones de cada caso. |
| QS-07 — Availability | Recuperar el servicio después de interrupciones controladas. |

### 3.2 Relación con la arquitectura

Los escenarios se vinculan con los architecture drivers AD-01 a AD-07 y servirán para justificar las decisiones arquitectónicas, definir responsabilidades entre componentes y establecer las actividades de validación.

Las métricas cuantitativas constituyen objetivos propuestos para un entorno de prueba determinado y no representan resultados de pruebas ejecutadas ni compromisos institucionales definitivos.

Los escenarios completos, sus medidas de respuesta y procedimientos de verificación están documentados en [Quality Attribute Scenarios](quality-scenarios.md).

## 4. Estrategia arquitectónica y decisiones de diseño

### 4.1. Alternativas evaluadas

Se compararon tres alternativas considerando mantenibilidad, consistencia de datos, complejidad de desarrollo y recursos disponibles.

| Alternativa | Ventajas | Limitaciones |
|---|---|---|
| Monolito tradicional | Implementación y despliegue sencillos. | Mayor riesgo de acoplamiento entre responsabilidades. |
| Monolito modular | Separación lógica, transacciones locales y operación manejable. | Despliegue conjunto de los módulos. |
| Microservicios | Escalado y despliegue independiente. | Mayor complejidad de comunicación, consistencia y operación. |

### 4.2. Arquitectura seleccionada

Se selecciona como propuesta un **monolito modular**, con una aplicación web React (C-WEB), un backend Node.js/Express (C-API), PostgreSQL (C-DB) y un servicio HTTP mock independiente para el SIS (X-SIS).

Esta alternativa responde al alcance de DH-01 porque permite separar responsabilidades funcionales y mantener operaciones transaccionales dentro de un backend único. Al mismo tiempo, reduce la complejidad de infraestructura frente a una solución distribuida.

### 4.3. Decisiones arquitectónicas

| ADR | Decisión | Justificación principal |
|---|---|---|
| ADR-001 | Monolito modular. | Equilibrio entre mantenibilidad y viabilidad operacional. |
| ADR-002 | PostgreSQL. | Integridad referencial y transacciones consistentes. |
| ADR-003 | Ciclo de vida controlado. | Prevención de estados inválidos y conflictos concurrentes. |
| ADR-004 | Autenticación y autorización contextual. | Protección de información según identidad, rol y relación. |
| ADR-005 | Auditoría persistente. | Conservación de operaciones e historial de responsabilidad. |

Las decisiones priorizan consistencia, seguridad y facilidad de evolución. Como compromiso, los módulos compartirán despliegue y deberán mantener contratos internos claros para evitar acoplamientos innecesarios.

**Documento de detalle:** [Architecture Decision Records](adr/README.md).

## 5. Vistas arquitectónicas

La arquitectura se representa mediante vistas complementarias basadas en el enfoque C4, junto con una vista de despliegue.

| Vista | Propósito | Documento |
|---|---|---|
| Contexto — V-CTX | Identificar actores, límites y dependencias externas. | [System Context](views/system-context.md) |
| Contenedores — V-CON | Mostrar C-WEB, C-API, C-DB y X-SIS y sus relaciones. | [Container View](views/container-view.md) |
| Componentes — V-CMP | Describir los diez componentes lógicos CMP-01 a CMP-10 del backend. | [Component View](views/component-view.md) |
| Despliegue — V-DEP | Representar nodos, procesos y conexiones propuestas. | [Deployment View](views/deployment-view.md) |

La vista de contexto diferencia las responsabilidades de DH-01 de los sistemas académicos institucionales. La vista de contenedores establece los límites tecnológicos, mientras que la de componentes detalla la separación interna del monolito modular.

La vista de despliegue presenta cómo podrían ejecutarse estas responsabilidades en recursos limitados, sin asumir infraestructura institucional ya disponible.

Los diagramas y las descripciones completas se encuentran en el [Índice de Vistas](views/README.md).

## 6. Dominio, datos y comportamiento

El diseño utiliza `AdvisingCase` como entidad central, relacionada con estudiantes, clasificaciones, asignaciones, solicitudes de información, seguimientos, notas, recomendaciones y eventos de auditoría.

PostgreSQL permitirá representar estas relaciones mediante restricciones e índices. Se propone mantener una única asignación vigente y conservar las asignaciones anteriores para reconstruir la historia del caso.

El ciclo de vida se organiza en siete estados, con transiciones controladas desde C-API. Para resolver un caso se requiere orientación registrada, ausencia de solicitudes de información abiertas y de seguimientos pendientes, además de un responsable autorizado y una versión vigente.

Las operaciones críticas utilizarán transacciones, bloqueo del caso y validación de concurrencia. Los eventos obligatorios de auditoría formarán parte de la misma transacción que los cambios correspondientes.

El modelo también contempla una tabla técnica `SESSION` para persistir sesiones de autenticación, separada de las entidades del proceso académico.

**Documentos de detalle:** [Modelo de dominio y datos](design/domain-and-data.md) y [Ciclo de vida e interacciones](design/lifecycle-and-runtime.md).

## 7. Interfaces y seguridad

C-WEB se comunicará con C-API mediante contratos REST/JSON bajo `/api/v1`. El backend será responsable de validar las entradas, aplicar reglas de negocio y devolver respuestas acordes con el estado y los permisos del usuario.

La autenticación propuesta utiliza cuentas ficticias y sesiones opacas de servidor, persistidas en PostgreSQL. Se contemplan cookies protegidas, expiración, revocación y protección CSRF para operaciones de escritura.

La autorización será contextual: además del rol, considerará la relación vigente con el caso, el alcance académico y la sensibilidad de la información. Las notas internas estarán separadas de las recomendaciones visibles para estudiantes y los recursos anidados deberán comprobar su pertenencia al caso autorizado.

Los contratos también contemplan errores de autenticación, autorización, recursos inexistentes, conflictos de versión y fallos de la integración simulada.

**Documentos de detalle:** [Interfaces](design/interfaces.md) y [Seguridad](design/security.md).

## 8. Supuestos de despliegue

La propuesta contempla la ejecución de React, Node.js/Express, PostgreSQL y X-SIS como componentes con responsabilidades separadas. Estos podrán alojarse en un mismo equipo cuando los recursos disponibles lo permitan.

Docker Compose se considera una opción para preparar entornos reproducibles. También se proponen HTTPS, configuración externa, restricciones de conectividad, almacenamiento persistente y procedimientos de respaldo.

La capacidad efectiva de infraestructura y los mecanismos de recuperación deberán verificarse mediante pruebas. No se presume disponibilidad de servicios institucionales reales ni se promete alta disponibilidad.

**Documento de detalle:** [Supuestos de despliegue](deployment-assumptions.md).

## 9. Validación, riesgos y plan de trabajo

La estrategia de validación contempla pruebas funcionales, de seguridad, integración, API, transacciones y atributos de calidad.

Se han definido **18 pruebas**, con prioridad para los controles de confidencialidad, integridad del ciclo de vida, concurrencia y auditoría. Las demás verifican asignación, persistencia, integración SIS, rendimiento, modificabilidad, usabilidad y recuperación.

Los principales riesgos identificados incluyen políticas institucionales sin confirmar, exposición de información interna, inconsistencias transaccionales, dificultades de despliegue y limitaciones de tiempo.

El trabajo posterior está organizado en un backlog de trece actividades y cinco incrementos propuestos, desde la base técnica hasta la evaluación de calidad.

**Estado de validación:** según la línea base documental revisada, el prototipo no dispone de implementación ejecutable identificada y no se han aportado resultados de pruebas reproducibles. Ningún atributo de calidad se declara demostrado.

**Documentos de detalle:** [Plan de validación](validation/test-plan.md), [Resultados y hallazgos](validation/findings.md) y [Estado del proyecto](project-status.md).

## 10. Trazabilidad, glosario y referencias

### 10.1. Trazabilidad

La [Matriz de Trazabilidad](traceability.md) relaciona requisitos funcionales, atributos de calidad, reglas de negocio, restricciones, drivers, ADR, componentes y pruebas previstas.

Esta relación permite justificar las decisiones arquitectónicas y comprobar que cada necesidad tenga una respuesta de diseño y una forma propuesta de verificación.

La cobertura actual es documental; no equivale a una implementación ni a pruebas aprobadas.

### 10.2. Glosario

| Término | Definición |
|---|---|
| Case | Solicitud de asesoría académica con estado y registros asociados. |
| Triage | Evaluación y clasificación previa a la asignación inicial. |
| Follow-up | Acción de seguimiento interna o dirigida al estudiante. |
| Reassignment | Cambio autorizado de responsable que conserva el historial. |
| Overdue | Indicador de retraso cuya política definitiva está pendiente. |
| AD | Factor arquitectónico derivado de una necesidad significativa. |
| ADR | Registro que documenta y justifica una decisión arquitectónica. |
| QS | Escenario de calidad con respuesta y criterios observables. |
| Evidencia | Registro reproducible de una ejecución, asociado a una versión y un entorno. |

### 10.3. Conclusión

La arquitectura propuesta para DH-01 busca responder a las necesidades de gestión de asesoría académica mediante un monolito modular, persistencia relacional, control de acceso contextual y operaciones auditables.

El diseño mantiene una relación trazable entre requisitos, decisiones, componentes y validación prevista. Las políticas institucionales abiertas, la implementación y la comprobación empírica permanecen pendientes.

Esta documentación proporciona una base estructurada para desarrollar, evaluar y evolucionar el sistema sin presentar sus propuestas como funcionalidades ya implementadas.
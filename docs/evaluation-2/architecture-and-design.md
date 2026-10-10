# DH-01 — Academic Advising Case Management System

## Architecture and Design Document

**Estado:** Propuesta de diseño en elaboración.  
**Institución:** Horizon University — Digital Horizon Initiative  
**Documento:** Arquitectura y diseño de software.

## Índice del documento

1. [Propósito y alcance del diseño](#1-propsito-y-alcance-del-diseo)
2. [Requisitos significativos y restricciones](#2-requisitos-significativos-y-restricciones)
3. [Escenarios de calidad y drivers arquitectónicos](#3-escenarios-de-calidad-y-drivers-arquitectnicos)
4. [Alternativas arquitectónicas y decisiones ADR](#4-alternativas-arquitectnicas-y-decisiones-adr)
5. [Vistas arquitectónicas](#5-vistas-arquitectnicas)
6. [Dominio datos y comportamiento](#6-dominio-datos-y-comportamiento)
7. [Interfaces y seguridad](#7-interfaces-y-seguridad)
8. [Supuestos de despliegue](#8-supuestos-de-despliegue)
9. [Validación riesgos y plan de trabajo](#9-validacin-riesgos-y-plan-de-trabajo)
10. [Trazabilidad glosario y referencias](#10-trazabilidad-glosario-y-referencias)

# 1. Propósito y alcance del diseño

## 1.1 Introducción

El proyecto DH-01 — Academic Advising Case Management System forma parte de la iniciativa Digital Horizon de Horizon University y tiene como propósito proponer una solución tecnológica para mejorar la gestión de solicitudes de asesoría académica dirigidas a estudiantes de pregrado.

El análisis del contexto institucional permitió identificar dificultades relacionadas con la fragmentación de la información, la falta de claridad sobre la responsabilidad de los casos, el seguimiento de actividades pendientes y la limitada visibilidad operacional de los coordinadores.

A partir del análisis del Request for Proposal (RFP), el estudio del proceso actual (AS-IS), el proceso de discovery y la definición del proceso propuesto (TO-BE), se establecieron los requisitos funcionales, requisitos de calidad, reglas de negocio y límites del proyecto.

El presente documento desarrolla la propuesta de arquitectura y diseño del sistema, transformando las necesidades y requisitos identificados en una solución técnicamente estructurada, coherente y viable.

Se busca explicar cómo se organizará el sistema, cuáles serán las responsabilidades de sus componentes, cómo interactuarán entre sí, de qué manera se administrará la información y qué mecanismos permitirán satisfacer los requisitos funcionales y de calidad establecidos.

## 1.2 Propósito del documento

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

## 1.3 Visión del sistema

El Academic Advising Case Management System se concibe como una aplicación web centralizada que permitirá gestionar las solicitudes de asesoría académica desde su creación hasta su resolución.

La solución busca proporcionar un proceso estructurado en el que cada caso pueda registrarse, clasificarse, asignarse a un responsable, recibir seguimiento y conservar un historial de las acciones realizadas.

Los estudiantes podrán consultar información autorizada sobre el estado de sus solicitudes, mientras que los Academic Advisers podrán administrar los casos bajo su responsabilidad, registrar recomendaciones y gestionar las actividades de seguimiento.

Por su parte, los coordinadores dispondrán de mecanismos de asignación, supervisión y consulta de información operacional según sus responsabilidades y permisos.

El sistema incorporará controles de acceso basados en roles, relación con los casos y sensibilidad de la información, con el objetivo de proteger los registros internos y evitar la divulgación de información confidencial.

La solución no pretende reemplazar los sistemas académicos institucionales existentes, sino proporcionar una herramienta especializada en la administración y trazabilidad del proceso de asesoría académica.

## 1.4 Alcance del diseño arquitectónico

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

## 1.5 Enfoque y resultados esperados del diseño

El trabajo arquitectónico se concentra en definir una solución que responda a los requisitos del proyecto y permita evaluar su viabilidad técnica antes de avanzar hacia una implementación completa.

Para ello, se desarrollarán vistas arquitectónicas, modelos de datos, contratos de interfaces, escenarios de calidad, registros de decisiones y una estrategia inicial de validación.

Cuando resulte pertinente, podrán utilizarse prototipos o pruebas de concepto para comprobar decisiones arquitectónicas específicas.

Las funcionalidades implementadas y los resultados de pruebas ejecutadas se documentarán de manera independiente de las capacidades que permanezcan en fase de diseño.

El objetivo es establecer una base técnica consistente que facilite la implementación, reduzca incertidumbres, permita evaluar decisiones de diseño y mantenga una relación clara entre requisitos, arquitectura y validación.

---

> Las siguientes secciones de diseño se desarrollarán y enlazarán desde este documento conforme se definan las decisiones técnicas y evidencias correspondientes.

# 2. Requisitos significativos y restricciones

## 2.1 Restricciones y condiciones del proyecto

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

## 2.2 Supuestos y aspectos pendientes de validación

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

Entre los escenarios de mayor prioridad se encuentran la prevención del acceso no autorizado a notas internas, la imposibilidad de resolver casos con actividades obligatorias pendientes y la conservación del historial durante las reasignaciones.

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

### 4.1 Evaluación de alternativas arquitectónicas

La selección de la arquitectura del Academic Advising Case Management System considera los principales factores identificados: integridad del ciclo de vida, confidencialidad, trazabilidad, mantenibilidad y viabilidad operacional.

Se analizaron tres alternativas principales: monolito tradicional sin separación modular explícita, monolito modular y arquitectura de microservicios.

| Alternativa | Ventajas | Limitaciones |
|---|---|---|
| Monolito tradicional | Implementación y despliegue relativamente sencillos. | Mayor riesgo de acoplamiento entre responsabilidades. |
| Monolito modular | Separación lógica de responsabilidades, consistencia transaccional y operación manejable. | Los módulos comparten el ciclo de despliegue. |
| Microservicios | Escalamiento y despliegue independiente de servicios. | Mayor complejidad de comunicación, consistencia y operación. |

### 4.2 Arquitectura seleccionada

Se propone utilizar una arquitectura de monolito modular, compuesta por una aplicación web, una API backend y una base de datos relacional.

El backend estará organizado en módulos responsables de autenticación y autorización, gestión de casos, clasificación y asignación, seguimiento, historial y reportes.

Esta estrategia permite mantener límites claros entre responsabilidades relacionadas, sin introducir la complejidad de coordinación y despliegue de múltiples servicios independientes.

La arquitectura favorece las operaciones transaccionales necesarias para preservar la integridad del ciclo de vida y conservar el historial de las acciones relevantes.

### 4.3 Decisiones arquitectónicas principales

| ADR | Decisión | Justificación principal |
|---|---|---|
| ADR-001 | Modular Monolith Architecture | Equilibrio entre separación de responsabilidades y simplicidad operacional. |
| ADR-002 | Relational Database and Persistent Data Management | Relaciones estructuradas, integridad referencial y transacciones. |
| ADR-003 | Controlled Case Lifecycle and Transactional Consistency | Prevención de transiciones inválidas y estados contradictorios. |
| ADR-004 | Authentication and Resource-Based Authorization | Protección de información según rol y relación con los casos. |
| ADR-005 | Persistent Audit Trail and Case History | Conservación de acciones y cambios de responsabilidad. |

### 4.4 Consecuencias y compromisos

La estrategia seleccionada prioriza la mantenibilidad, la consistencia y una complejidad operacional compatible con las restricciones del proyecto.

Como consecuencia, los módulos no dispondrán de despliegues independientes y compartirán la infraestructura principal del backend.

El diseño también requiere mantener contratos internos claros, controlar las dependencias entre módulos y desarrollar pruebas sobre las operaciones críticas.

Estas limitaciones se consideran aceptables para el alcance actual, dado que no existe una necesidad demostrada de adoptar una arquitectura distribuida.

Las decisiones completas, sus alternativas, consecuencias y condiciones de reconsideración se documentan en el directorio [Architecture Decision Records](adr/README.md).

## 5. Vistas arquitectónicas

La arquitectura del Academic Advising Case Management System se representa mediante un conjunto de vistas complementarias que permiten analizar los límites del sistema, sus principales responsabilidades, las relaciones entre sus componentes y las condiciones previstas de ejecución.

Se utilizará el enfoque C4 para documentar el contexto, los contenedores y los componentes principales, complementado con una vista de despliegue.

### 5.1 Vista de contexto del sistema

La vista de contexto identifica a los principales actores institucionales que interactúan con DH-01: Student, Academic Adviser, Program Coordinator, Advising Coordinator e IT Services.

También establece las relaciones con los servicios externos de identidad institucional y Student Information System, cuya integración podrá representarse mediante mecanismos simulados.

DH-01 será responsable de administrar las solicitudes de asesoría, sus estados, asignaciones, seguimientos, recomendaciones e historial. La autenticación institucional y la administración de registros académicos oficiales permanecerán como responsabilidades externas.

Esta delimitación evita atribuir al sistema funciones que exceden su alcance y permite identificar las relaciones que deberán considerarse durante el diseño de interfaces y seguridad.

El diagrama completo, las responsabilidades de los actores y las dependencias externas están documentados en [System Context View](views/system-context.md).

# 6. Dominio, datos y comportamiento

Se definirán los conceptos principales —casos, actores, asignaciones, notas, recomendaciones, acciones de seguimiento e historial— junto con sus relaciones, restricciones y transiciones válidas. Se explicará especialmente la asignación de responsable y la prohibición de resolver un caso con acciones pendientes.

**Documento de detalle:** [Modelo de dominio y datos](design/domain-and-data.md) y [ciclo de vida e interacciones](design/lifecycle-and-runtime.md).

**Estado:** pendiente de desarrollo y revisión.

# 7. Interfaces y seguridad

Se especificarán las interfaces principales, los contratos de entrada y salida, errores esperados y límites de confianza. Los permisos se evaluarán según el rol, la relación con el caso y la sensibilidad de los datos, evitando revelar notas internas a estudiantes.

**Documento de detalle:** [Contratos de interfaces](design/interfaces.md) y [seguridad](design/security.md).

**Estado:** pendiente de desarrollo y revisión.

# 8. Supuestos de despliegue

Se describirán los componentes ejecutables, entornos, conexiones, dependencias institucionales y supuestos operacionales necesarios para ejecutar una solución accesible remotamente y mantenible con recursos limitados.

**Documento de detalle:** [Supuestos de despliegue](deployment-assumptions.md).

**Estado:** pendiente de desarrollo y revisión.

# 9. Validación, riesgos y plan de trabajo

Se diseñarán pruebas de los riesgos arquitectónicos más importantes. Los resultados se registrarán solamente después de ejecutarlos, diferenciando evidencias obtenidas, pruebas pendientes y limitaciones conocidas. También se actualizarán prioridades y dependencias del proyecto.

**Documento de detalle:** [Plan de pruebas](validation/test-plan.md), [hallazgos](validation/findings.md) y [estado del proyecto](project-status.md).

**Estado:** pendiente de desarrollo y revisión.

# 10. Trazabilidad, glosario y referencias

La trazabilidad conectará los requisitos existentes con decisiones, componentes, interfaces y pruebas. Los términos del dominio mantendrán una definición consistente; las referencias documentales identificarán las fuentes de los requisitos y del diseño.

**Documento de detalle:** [Matriz de trazabilidad](traceability.md).

**Estado:** pendiente de desarrollo y revisión.


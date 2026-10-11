# DH-01 — Academic Advising Case Management System

**Horizon University · Digital Horizon Initiative**  
**Asignatura:** Software Design II  
**Entrega:** Evaluation 2 — Architecture and Design Review  
**Fecha:** 11 de octubre de 2026  
**Estado:** Diseño arquitectónico documentado y prototipo interactivo de demostración

## Descripción

DH-01 propone un sistema web centralizado para gestionar solicitudes de asesoría académica de estudiantes de pregrado, desde su registro hasta su resolución.

La solución busca mejorar la clasificación, asignación, seguimiento y trazabilidad de los casos, proporcionando visibilidad operacional a los coordinadores y protegiendo la información confidencial.

## Equipo de trabajo

| Integrante | Responsabilidad |
|---|---|
| Nayelhi Álvarez | Requisitos y diseño del sistema |
| Sergio Pérez | Arquitectura y decisiones técnicas |
| Pedro Peñate | Validación, trazabilidad y planificación |

## Arquitectura propuesta

Se propone un **monolito modular** compuesto por:

- **React (C-WEB):** interfaz web.
- **Node.js / Express (C-API):** lógica de negocio, seguridad y gestión de casos.
- **PostgreSQL (C-DB):** persistencia e historial.
- **X-SIS:** servicio simulado de integración académica.

La arquitectura prioriza mantenibilidad, integridad de los datos, autorización contextual y trazabilidad.

Estas tecnologías corresponden a la arquitectura diseñada y no a una implementación completa incluida en la entrega.

## Prototipo interactivo

Se incluye un [prototipo navegable](prototype/index.html) que permite explorar los principales flujos del sistema mediante datos sintéticos y lógica simulada.

El prototipo presenta interfaces diferenciadas para los cinco roles y permite demostrar:

- Registro y consulta de solicitudes.
- Clasificación, asignación y reasignación de casos.
- Atención, solicitudes de información y seguimiento.
- Registro de recomendaciones y notas internas.
- Resolución y visualización del historial.
- Simulación de restricciones y conflictos de versión.

**Cómo ejecutarlo:** descargar el repositorio y abrir `prototype/index.html` en un navegador compatible con JavaScript. No requiere instalar dependencias ni iniciar un servidor.

El prototipo utiliza almacenamiento local (`localStorage`) y no implementa los servicios Node.js/Express, PostgreSQL ni la integración HTTP con X-SIS descritos en la arquitectura.

Los controles de acceso y los conflictos mostrados son simulaciones, no pruebas de seguridad o concurrencia del backend.

Consulta [Alcance y limitaciones del prototipo](prototype/README.md).

## Documentación

| Apartado | Documento |
|---|---|
| Arquitectura y diseño | [Documento principal](docs/evaluation-2/architecture-and-design.md) |
| Requisitos y drivers | [Requisitos](docs/evaluation-2/requirements-and-drivers.md) |
| Escenarios de calidad | [Escenarios](docs/evaluation-2/quality-scenarios.md) |
| Decisiones arquitectónicas | [ADR](docs/evaluation-2/adr/README.md) |
| Vistas y diagramas | [Vistas](docs/evaluation-2/views/README.md) |
| Dominio y datos | [Modelo](docs/evaluation-2/design/domain-and-data.md) |
| Ciclo de vida | [Estados e interacciones](docs/evaluation-2/design/lifecycle-and-runtime.md) |
| Interfaces | [Contratos API](docs/evaluation-2/design/interfaces.md) |
| Seguridad | [Seguridad y privacidad](docs/evaluation-2/design/security.md) |
| Despliegue | [Supuestos](docs/evaluation-2/deployment-assumptions.md) |
| Trazabilidad | [Matriz](docs/evaluation-2/traceability.md) |
| Riesgos y planificación | [Estado del proyecto](docs/evaluation-2/project-status.md) |
| Validación | [Plan de pruebas](docs/evaluation-2/validation/test-plan.md) |
| Resultados | [Registro de validación](docs/evaluation-2/validation/findings.md) |

## Estado de la entrega

La Evaluación 2 presenta una **propuesta de arquitectura y diseño**, acompañada de un **prototipo interactivo de demostración**.

La documentación incluye requisitos, decisiones arquitectónicas, diagramas, contratos, modelos, riesgos, trazabilidad y procedimientos de validación.

El prototipo permite explorar visualmente los procesos y reglas principales, pero no constituye una implementación completa de la arquitectura.

Las **18 pruebas técnicas previstas permanecen en estado `NOT EXECUTED`**, y las políticas institucionales pendientes se encuentran identificadas en la documentación.

**Punto de entrada recomendado:** [Architecture and Design Document](docs/evaluation-2/architecture-and-design.md).
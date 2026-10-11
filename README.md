# DH-01 — Academic Advising Case Management System

**Horizon University · Digital Horizon Initiative**  
**Asignatura:** Software Design II  
**Entrega:** Evaluation 2 — Architecture and Design Review  
**Fecha:** 11 de octubre de 2026  
**Estado:** Diseño arquitectónico documentado

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

La Evaluación 2 presenta una **propuesta de arquitectura y diseño**, con requisitos, decisiones, diagramas, contratos, modelos y procedimientos de validación documentados.

No se incluye un prototipo funcional. Las 18 pruebas definidas permanecen sin ejecutar y las políticas institucionales pendientes están identificadas en la documentación.

**Punto de entrada recomendado:** [Architecture and Design Document](docs/evaluation-2/architecture-and-design.md).
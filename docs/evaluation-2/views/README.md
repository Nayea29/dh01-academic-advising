# DH-01 — Architecture Views

Este directorio reúne las vistas arquitectónicas del **Academic Advising Case Management System (DH-01)**, desarrolladas para la Evaluación 2 de Software Design II.

Las vistas describen el sistema desde diferentes perspectivas: su interacción con actores y servicios externos, su estructura tecnológica, la organización interna de sus componentes y su distribución en el entorno de ejecución.

## Índice de vistas

| ID | Vista | Documento |
|---|---|---|
| V-CTX | System Context | [System Context View](system-context.md) |
| V-CONT | Container View | [Container View](container-view.md) |
| V-COMP | Component View | [Component View](component-view.md) |
| V-DEP | Deployment View | [Deployment View](deployment-view.md) |

## Organización de las vistas

**System Context (V-CTX):** identifica los actores, sistemas externos y límites de responsabilidad de DH-01.

**Container View (V-CONT):** presenta las principales unidades de ejecución, incluyendo C-WEB, C-API, C-DB y X-SIS, junto con sus relaciones.

**Component View (V-COMP):** describe la organización interna del backend C-API y las responsabilidades de sus componentes.

**Deployment View (V-DEP):** representa la distribución propuesta de los contenedores y sus dependencias en el entorno de despliegue.

## Relación con el diseño arquitectónico

Los diagramas utilizan **Mermaid**, lo que permite mantenerlos como documentación editable y versionada.

Las vistas complementan las decisiones registradas en los [Architecture Decision Records](../adr/README.md) y se relacionan con los requisitos, escenarios de calidad y restricciones documentados para DH-01.
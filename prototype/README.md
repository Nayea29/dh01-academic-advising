# DH-01 — Prototipo Interactivo

Este directorio contiene un prototipo interactivo del **Academic Advising Case Management System**, desarrollado como complemento de la Evaluación 2 de Software Design II.

## Ejecución

Abrir `index.html` en un navegador compatible con JavaScript.

No requiere instalación de dependencias, servidor backend ni base de datos.

## Funcionalidades demostradas

El prototipo permite explorar los flujos principales de gestión de solicitudes de asesoría académica:

- Registro y consulta de solicitudes.
- Clasificación y asignación de casos.
- Atención, seguimiento y recomendaciones.
- Reasignación y resolución.
- Vistas diferenciadas según el rol.
- Historial de operaciones.
- Simulación de restricciones y conflictos.

## Alcance y limitaciones

El prototipo utiliza datos sintéticos, lógica ejecutada en el navegador y almacenamiento local (`localStorage`).

El cambio de perfiles permite explorar las diferentes vistas, pero no constituye un mecanismo de autenticación real.

No implementa el backend Node.js/Express, la base de datos PostgreSQL ni la integración HTTP con el SIS simulado que se describen en la arquitectura propuesta.

Las validaciones y los conflictos mostrados son simulaciones y no acreditan seguridad, concurrencia ni transacciones de servidor.

**Este prototipo es una herramienta de demostración del diseño, no una implementación completa de DH-01.**

La arquitectura y la estrategia de validación se encuentran en [`docs/evaluation-2/`](../docs/evaluation-2/architecture-and-design.md).
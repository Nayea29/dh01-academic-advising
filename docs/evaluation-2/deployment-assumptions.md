# DH-01 — Supuestos y Restricciones de Despliegue

**Proyecto:** Academic Advising Case Management System  
**Documento:** Deployment Assumptions and Constraints  
**Vista relacionada:** V-DEP — Deployment View  
**Decisiones relacionadas:** ADR-001, ADR-002, ADR-003, ADR-004 y ADR-005  
**Architecture Driver principal:** AD-07 — Viabilidad de despliegue  
**Escenario de calidad principal:** QS-07 — Persistencia y recuperación  
**Estado:** Diseño propuesto, pendiente de validación

## 1. Propósito y alcance

Este documento establece los supuestos técnicos, recursos y restricciones necesarios para desplegar y validar el Academic Advising Case Management System (DH-01).

La solución utilizará una arquitectura de monolito modular, compuesta por una aplicación web React, un backend Node.js + Express y una base de datos PostgreSQL. Adicionalmente, se contempla un servicio mock del Student Information System (SIS) para proporcionar datos académicos sintéticos.

El despliegue estará orientado a un entorno de desarrollo, pruebas y demostración. Su objetivo será permitir la ejecución de los flujos principales de gestión de casos y la validación de atributos de calidad como seguridad, integridad, auditabilidad, rendimiento y recuperación.

El prototipo utilizará usuarios ficticios y no requerirá acceso a los sistemas institucionales reales de Horizon University.

Los siguientes supuestos delimitan la propuesta:

- **AS-DEP-01:** Se dispondrá de un equipo o servidor donde puedan instalarse las dependencias necesarias.
- **AS-DEP-02:** El equipo tendrá permisos para configurar los servicios de aplicación y base de datos.
- **AS-DEP-03:** Todos los datos académicos y las identidades utilizados serán sintéticos.
- **AS-DEP-04:** La autenticación se implementará internamente en el backend.
- **AS-DEP-05:** PostgreSQL será el sistema principal de persistencia.
- **AS-DEP-06:** Los módulos de negocio se ejecutarán dentro de una única aplicación backend.
- **AS-DEP-07:** La integración con el SIS se representará mediante un servicio mock independiente.
- **AS-DEP-08:** Los nodos lógicos podrán compartir un mismo equipo físico durante el prototipo.

No se presupone la disponibilidad de infraestructura institucional, servidores dedicados, credenciales universitarias ni integraciones reales.

## 2. Infraestructura y tecnologías propuestas

La configuración de referencia contempla los siguientes elementos:

| Identificador | Componente | Tecnología | Ubicación propuesta |
|---|---|---|---|
| C-WEB | Aplicación Web | React | N-APP |
| C-API | Backend API | Node.js + Express | N-APP |
| C-DB | Base de Datos | PostgreSQL | N-DB |
| X-SIS | Simulador del SIS | Servicio mock HTTP | N-MOCK |

`N-APP` y `N-DB` representan responsabilidades lógicas, no necesariamente equipos físicos independientes.

Para simplificar el despliegue, se propone ejecutar los servicios en un único equipo de prueba, manteniendo separados los procesos y restringiendo el acceso a PostgreSQL.

### 2.1. Recursos estimados

Como punto de partida, se propone la siguiente capacidad:

| Recurso | Estimación inicial |
|---|---|
| CPU | 2 núcleos |
| Memoria RAM | 4 GB |
| Almacenamiento | 20 GB disponibles, preferiblemente SSD |
| Sistema operativo | Linux o equivalente compatible |
| Backend | Una instancia Node.js |
| Base de datos | Una instancia PostgreSQL |
| Simulador SIS | Un servicio mock independiente |

Estas cifras son estimaciones para planificar el prototipo, no requisitos mínimos comprobados. Su suficiencia deberá verificarse durante las pruebas.

### 2.2. Estrategia de ejecución

Se recomienda utilizar **Docker Compose**, si el entorno disponible lo permite, para facilitar la configuración y reproducción de los servicios.

Como alternativa, React, Node.js, PostgreSQL y el simulador podrán ejecutarse mediante instalaciones y procesos independientes.

En cualquiera de los casos se deberá documentar:

- Versiones de las dependencias.
- Procedimiento de instalación.
- Configuración requerida.
- Migraciones de base de datos.
- Inicialización de usuarios y datos de prueba.
- Comandos para iniciar los servicios.

El uso de Docker no modifica la estrategia arquitectónica de monolito modular establecida en ADR-001.

## 3. Configuración, conectividad y seguridad

La aplicación utilizará configuración externa al código fuente para administrar los parámetros del entorno.

Las variables previstas incluyen:

| Variable de referencia | Propósito |
|---|---|
| `NODE_ENV` | Identificar el entorno de ejecución. |
| `PORT` | Puerto interno de C-API. |
| `DATABASE_URL` | Conexión a PostgreSQL. |
| `SESSION_SECRET` | Secreto del mecanismo de sesión, si corresponde. |
| `SIS_MOCK_BASE_URL` | Dirección del simulador del SIS. |
| `SIS_REQUEST_TIMEOUT_MS` | Tiempo máximo de espera de la integración. |
| `LOG_LEVEL` | Nivel de registros técnicos. |

Los nombres son referenciales y podrán ajustarse durante la implementación.

### 3.1. Comunicaciones

| Origen | Destino | Comunicación prevista |
|---|---|---|
| Navegador | C-WEB | HTTPS |
| Aplicación web | C-API | REST / JSON mediante HTTPS desde el navegador |
| Reverse proxy | Backend Node.js | Conexión interna restringida |
| C-API | C-DB | Protocolo PostgreSQL / SQL |
| C-API | X-SIS | HTTP(S) / JSON mediante adaptador |

Se propone utilizar un servidor HTTP o reverse proxy para servir React y dirigir las solicitudes de API hacia Node.js.

La base de datos no deberá exponerse públicamente. Cuando PostgreSQL y el backend se ejecuten en el mismo equipo, podrá utilizarse una conexión local restringida.

El simulador del SIS estará separado del backend como servicio de prueba, aunque podrá ejecutarse en la misma máquina física.

### 3.2. Controles de seguridad

El despliegue deberá considerar:

- Autenticación interna mediante usuarios ficticios.
- Contraseñas almacenadas mediante algoritmos de hash seguros.
- Autorización aplicada por el backend según roles y recursos.
- Protección de las comunicaciones externas mediante HTTPS.
- Restricción de conexiones a PostgreSQL.
- Gestión de secretos fuera del código fuente.
- Separación de permisos técnicos y permisos de acceso a información académica.
- Registros técnicos sin contraseñas, tokens ni contenido confidencial innecesario.

La utilización de datos sintéticos no elimina la necesidad de implementar controles de seguridad.

Los mecanismos concretos de autenticación, autorización y protección de información se detallarán en `design/security.md`.

## 4. Persistencia, respaldos y recuperación

PostgreSQL será responsable de conservar la información relacionada con usuarios, casos, asignaciones, seguimiento, notas y eventos de auditoría.

La persistencia deberá mantenerse independientemente de los reinicios del backend.

Si se utilizan contenedores Docker, PostgreSQL deberá contar con almacenamiento persistente correctamente configurado.

### 4.1. Integridad

Las operaciones que modifiquen varios registros relacionados deberán mantener consistencia mediante transacciones.

Por ejemplo, una reasignación y su evento obligatorio de auditoría deberán confirmarse dentro de la misma transacción.

La gestión de estados y operaciones atómicas seguirá las decisiones establecidas en ADR-003 y ADR-005.

### 4.2. Respaldos

Se propone establecer un procedimiento básico de respaldo de PostgreSQL utilizando herramientas compatibles, como `pg_dump`.

El procedimiento deberá documentar cómo generar la copia y dónde almacenarla de manera segura.

### 4.3. Recuperación

La capacidad de recuperación deberá verificarse mediante una prueba que contemple:

1. Crear datos sintéticos conocidos.
2. Generar un respaldo.
3. Restaurarlo en una base de prueba.
4. Consultar los registros recuperados.
5. Verificar relaciones y eventos relevantes.
6. Documentar los resultados.

También deberá comprobarse que un reinicio controlado de `C-API` no elimina los datos previamente confirmados.

El prototipo no contempla inicialmente alta disponibilidad ni recuperación automática ante cualquier fallo físico.

La persistencia y recuperación se relacionan con el escenario QS-07. La existencia de PostgreSQL o de un archivo de respaldo no demuestra por sí sola su cumplimiento.

## 5. Riesgos y restricciones operacionales

Los principales riesgos identificados son:

| Riesgo | Consecuencia | Medida propuesta |
|---|---|---|
| Recursos insuficientes | Respuestas lentas o interrupciones | Ejecutar pruebas de carga y ajustar recursos. |
| PostgreSQL expuesto públicamente | Acceso no autorizado | Restringir conectividad y permisos. |
| Secretos incluidos en el repositorio | Compromiso de credenciales | Gestionarlos mediante configuración externa. |
| Falta de almacenamiento persistente | Pérdida de datos después de reinicios | Configurar y verificar la persistencia. |
| Ausencia de respaldos comprobados | Dificultad para restaurar información | Ejecutar pruebas de respaldo y restauración. |
| Fallo de X-SIS | Errores en consultas académicas | Aislar la integración y manejar tiempos de espera. |
| Migraciones incompatibles | Errores de aplicación o integridad | Utilizar migraciones versionadas. |
| Autenticación implementada de forma insegura | Accesos indebidos | Validar credenciales, sesiones y permisos. |
| Dependencias sin versiones controladas | Problemas de reproducibilidad | Documentar y fijar versiones compatibles. |

La integración real con sistemas institucionales permanece fuera del alcance del prototipo.

Tampoco se contempla inicialmente una infraestructura distribuida con múltiples réplicas, balanceadores de carga, colas de mensajes o un Background Worker independiente.

Estas capacidades podrán reconsiderarse si nuevos requisitos o resultados de validación demuestran su necesidad.

Las medidas de mitigación descritas son propuestas y no deberán considerarse implementadas sin evidencia.

## 6. Validación y trazabilidad

La configuración propuesta deberá permitir ejecutar pruebas funcionales y arquitectónicas reproducibles.

Las principales verificaciones serán:

| Escenario | Verificación prevista |
|---|---|
| QS-01 — Seguridad | Acceso permitido y denegado según roles y recursos. |
| QS-02 — Integridad | Transiciones válidas, reasignaciones atómicas y control de concurrencia. |
| QS-03 — Auditabilidad | Persistencia de eventos relevantes junto con sus operaciones. |
| QS-04 — Rendimiento | Consultas operacionales bajo carga sintética definida. |
| QS-05 — Mantenibilidad | Revisión de límites modulares y pruebas de regresión. |
| QS-06 — Usabilidad | Ejecución de flujos representativos por stakeholder. |
| QS-07 — Recuperación | Persistencia tras reinicio y restauración de respaldo. |

Para QS-04 se contempla un escenario con aproximadamente 12.000 casos sintéticos y 20 usuarios concurrentes durante cinco minutos, de acuerdo con el escenario de calidad definido. La meta de tiempo de respuesta deberá medirse, no suponerse satisfecha por la infraestructura propuesta.

Cada prueba ejecutada deberá registrar su configuración, procedimiento, resultado esperado, resultado observado y evidencia correspondiente.

Los resultados se documentarán en `validation/`, diferenciando pruebas aprobadas, fallidas y pendientes de ejecución.

### 6.1. Relación con decisiones arquitectónicas

| Referencia | Relación |
|---|---|
| ADR-001 | Monolito modular y despliegue simplificado. |
| ADR-002 | PostgreSQL como almacenamiento persistente. |
| ADR-003 | Reglas de negocio y transacciones consistentes. |
| ADR-004 | Autenticación interna y autorización por recursos. |
| ADR-005 | Auditoría persistente. |
| AD-07 | Viabilidad y recuperación del entorno. |
| V-DEP | Ubicación de contenedores y comunicaciones. |

### 6.2. Documentos relacionados

- [Vista de Despliegue](views/deployment-view.md).
- [Vista de Contenedores](views/container-view.md).
- [Vista de Componentes](views/component-view.md).
- [Decisiones Arquitectónicas](adr/README.md).
- [Escenarios de Calidad](quality-scenarios.md).
- [Diseño de Seguridad](design/security.md).
- [Diseño de Interfaces](design/interfaces.md).
- [Plan de Pruebas](validation/test-plan.md).
- [Hallazgos de Validación](validation/findings.md).

## 7. Conclusión

El despliegue propuesto para DH-01 prioriza la simplicidad, la reproducibilidad y la viabilidad técnica.

La solución podrá ejecutarse en un único equipo físico mediante procesos separados para la aplicación web, el backend, PostgreSQL y el simulador del SIS.

La arquitectura mantendrá autenticación interna, autorización en el backend, persistencia relacional y auditoría de operaciones relevantes.

Las decisiones de infraestructura estarán sujetas a las capacidades reales del entorno y a los resultados de las pruebas.

El cumplimiento de los atributos de calidad deberá demostrarse mediante evidencia documentada, no únicamente mediante la selección de tecnologías.

Este documento complementa la Vista de Despliegue y proporciona los supuestos necesarios para preparar y validar el prototipo de DH-01.
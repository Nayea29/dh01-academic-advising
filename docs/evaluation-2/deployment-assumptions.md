# DH-01 — Supuestos y Restricciones de Despliegue

**Vista relacionada:** V-DEP — Deployment View  
**Decisiones relacionadas:** ADR-001, ADR-002, ADR-003, ADR-004 y ADR-005  
**Architecture Driver principal:** AD-07 — Viabilidad de despliegue  
**Escenario de calidad principal:** QS-07 — Persistencia y recuperación  
**Estado:** Supuestos de despliegue documentados; prototipo HTML interactivo disponible; infraestructura integrada pendiente de implementación

## 1. Propósito y alcance

Este documento establece los supuestos técnicos, los recursos estimados y las restricciones necesarias para desplegar y validar el Academic Advising Case Management System (DH-01).

La arquitectura propuesta utiliza un monolito modular compuesto por una aplicación web React, un backend Node.js + Express y una base de datos PostgreSQL. También contempla un servicio mock del Student Information System (SIS) que proporcionará información académica sintética.

El despliegue técnico estará orientado a desarrollo, pruebas y demostración. Su finalidad será permitir la ejecución de los principales procesos de asesoría académica y la validación de atributos de calidad relacionados con seguridad, integridad, auditabilidad, rendimiento y recuperación.

La Evaluación 2 incluye un [prototipo HTML interactivo](../../prototype/index.html), ejecutable directamente en el navegador, que utiliza datos sintéticos y almacenamiento local mediante `localStorage`.

**El prototipo existente no requiere el despliegue de los contenedores C-WEB, C-API, C-DB ni X-SIS.** Las condiciones de infraestructura descritas en este documento corresponden a una futura implementación integrada de la arquitectura y no a requisitos de ejecución del HTML actual.

La implementación futura utilizará identidades ficticias y no requerirá acceso a los sistemas institucionales reales de Horizon University.

Se establecen los siguientes supuestos:

| ID | Supuesto |
|---|---|
| AS-DEP-01 | Se dispondrá de un equipo o servidor compatible con las dependencias necesarias. |
| AS-DEP-02 | Existirán permisos suficientes para configurar los servicios de aplicación y base de datos. |
| AS-DEP-03 | Los usuarios y la información académica empleados serán sintéticos. |
| AS-DEP-04 | La autenticación se administrará internamente en el backend. |
| AS-DEP-05 | PostgreSQL será el sistema principal de persistencia. |
| AS-DEP-06 | Los módulos de negocio se ejecutarán dentro de una única aplicación backend. |
| AS-DEP-07 | La integración con el SIS se representará mediante un servicio mock independiente. |
| AS-DEP-08 | Los nodos lógicos podrán compartir un mismo equipo físico durante una futura implementación de prueba. |

Estos supuestos no implican que se disponga de servidores institucionales, credenciales reales, infraestructura dedicada o integraciones oficiales.

El prototipo HTML es un artefacto de demostración separado. Su funcionamiento local no confirma ni invalida la viabilidad de los supuestos de despliegue del sistema integrado.

## 2. Infraestructura y tecnologías propuestas

La configuración de referencia para una futura implementación contempla los siguientes elementos:

| Identificador | Componente | Tecnología | Nodo lógico |
|---|---|---|---|
| C-WEB | Aplicación web | React | N-APP |
| C-API | Backend API | Node.js + Express | N-APP |
| C-DB | Base de datos | PostgreSQL | N-DB |
| X-SIS | Simulador del SIS | Servicio HTTP mock | N-MOCK |

`N-APP` y `N-DB` representan responsabilidades lógicas y no necesariamente servidores físicos independientes.

Para simplificar el despliegue, se propone ejecutar los servicios en un único equipo de prueba, manteniendo procesos separados y conexiones restringidas a PostgreSQL.

**Esta configuración todavía no se encuentra implementada.** El prototipo HTML puede utilizarse en un navegador sin instalar PostgreSQL, Node.js ni Docker.

### 2.1. Recursos estimados

Como punto de partida para implementar el sistema integrado en un entorno de prueba, se propone:

| Recurso | Estimación inicial |
|---|---|
| CPU | 2 núcleos |
| Memoria RAM | 4 GB |
| Almacenamiento | 20 GB disponibles, preferiblemente SSD |
| Sistema operativo | Linux o entorno equivalente compatible |
| Backend | Una instancia de Node.js |
| Base de datos | Una instancia PostgreSQL |
| Simulador SIS | Un servicio mock independiente |

Estas cifras son estimaciones para planificación, no requisitos mínimos comprobados.

La suficiencia de estos recursos deberá evaluarse durante la implementación y las pruebas de rendimiento.

Las estimaciones no se refieren al consumo de recursos del prototipo HTML existente, que se ejecuta localmente en el navegador.

### 2.2. Estrategia de ejecución

Se recomienda utilizar **Docker Compose** cuando el entorno disponible lo permita, para facilitar la configuración y reproducción de los servicios de la arquitectura propuesta.

Como alternativa, React, Node.js, PostgreSQL y el simulador podrán ejecutarse mediante instalaciones y procesos independientes.

En cualquiera de las dos opciones deberán documentarse:

- Versiones de las dependencias.
- Procedimiento de instalación.
- Variables de configuración requeridas.
- Migraciones de PostgreSQL.
- Inicialización de usuarios y datos sintéticos.
- Comandos de inicio y detención de servicios.
- Procedimiento básico de comprobación de disponibilidad.

El uso de Docker Compose no modifica la arquitectura de monolito modular seleccionada en ADR-001. Los componentes internos de `C-API` continuarán ejecutándose como una única aplicación backend.

La ejecución del prototipo HTML actual es independiente de esta estrategia: basta con abrir `prototype/index.html` en un navegador compatible con JavaScript.

## 3. Configuración, conectividad y seguridad

Los parámetros de la futura implementación se mantendrán separados del código fuente mediante mecanismos apropiados de configuración.

Las variables previstas son:

| Variable de referencia | Propósito |
|---|---|
| `NODE_ENV` | Identificar el entorno de ejecución. |
| `PORT` | Configurar el puerto interno del backend. |
| `DATABASE_URL` | Establecer la conexión con PostgreSQL. |
| `SESSION_SECRET` | Proteger mecanismos de sesión que requieran un secreto de servidor. |
| `SIS_MOCK_BASE_URL` | Indicar la dirección del simulador SIS. |
| `SIS_REQUEST_TIMEOUT_MS` | Definir el tiempo máximo de espera de la integración. |
| `LOG_LEVEL` | Configurar el nivel de registros técnicos. |

Los nombres son referenciales y podrán ajustarse durante la implementación.

Conforme a ADR-004, la autenticación utilizará sesiones opacas de servidor, persistidas mediante la tabla técnica `SESSION` en PostgreSQL. El identificador reutilizable de sesión no deberá almacenarse directamente en texto claro.

Estas variables y controles de sesión corresponden a la implementación propuesta. El prototipo HTML no utiliza sesiones persistidas en PostgreSQL ni autenticación real de servidor.

### 3.1. Comunicaciones

| Origen | Destino | Comunicación prevista |
|---|---|---|
| Navegador | C-WEB | HTTPS |
| Aplicación web | C-API | REST / JSON sobre HTTPS desde el navegador |
| Reverse proxy | Backend Node.js | Conexión interna restringida |
| C-API | C-DB | Protocolo PostgreSQL / SQL |
| C-API | X-SIS | HTTP(S) / JSON mediante SIS Integration Adapter |

Se propone utilizar un servidor HTTP o reverse proxy para entregar la aplicación React y dirigir las solicitudes de API hacia Node.js.

PostgreSQL no deberá exponerse públicamente. Cuando la base de datos y el backend se ejecuten en el mismo equipo, podrá utilizarse una conexión local restringida.

El simulador SIS funcionará como servicio independiente del proceso `C-API`, aunque podrá compartir infraestructura física con los demás elementos.

Las conexiones internas que atraviesen redes no confiables deberán contar con protección de transporte apropiada.

**Estas comunicaciones representan la arquitectura objetivo.** El prototipo HTML interactivo no implementa solicitudes REST al backend ni conexiones PostgreSQL.

### 3.2. Controles de seguridad

El despliegue futuro deberá contemplar:

- Autenticación interna mediante usuarios ficticios.
- Contraseñas almacenadas utilizando un algoritmo de hash seguro, como Argon2id.
- Sesiones opacas administradas por el servidor y cookies con atributos de seguridad.
- Protección CSRF en las operaciones autenticadas que modifiquen información.
- Autorización contextual aplicada desde el backend.
- Comunicaciones públicas protegidas mediante HTTPS.
- Restricción de conexiones a PostgreSQL.
- Administración de secretos fuera del repositorio.
- Separación entre permisos técnicos y acceso a información académica.
- Registros técnicos sin contraseñas, tokens ni contenido confidencial innecesario.

La utilización de datos sintéticos no elimina la obligación de aplicar controles de seguridad a una futura implementación.

Los mecanismos de identidad, autorización, sesiones y privacidad se detallan en [Security Design](design/security.md).

El selector de perfiles, los controles visuales y los conflictos simulados del prototipo HTML no sustituyen estos mecanismos.

## 4. Persistencia, respaldos y recuperación

PostgreSQL conservará la información relacionada con usuarios, sesiones, casos, clasificaciones, asignaciones, seguimientos, notas, recomendaciones y eventos de auditoría.

La información confirmada deberá permanecer disponible después de reiniciar `C-API`.

Cuando se utilice Docker Compose, PostgreSQL deberá contar con un volumen persistente correctamente configurado. Si la base de datos se ejecuta directamente en el sistema operativo, su directorio de datos deberá disponer de almacenamiento persistente.

El prototipo existente utiliza `localStorage` para conservar información local en el navegador. Este mecanismo no equivale a la persistencia transaccional de PostgreSQL ni proporciona los procedimientos de respaldo y restauración propuestos.

### 4.1. Integridad

Las operaciones que modifiquen registros relacionados deberán mantener consistencia mediante transacciones.

Por ejemplo, una reasignación deberá confirmar conjuntamente la finalización de la asignación anterior, el registro del nuevo responsable, la actualización de versión del caso y el evento obligatorio de auditoría.

La consistencia del ciclo de vida seguirá ADR-003, mientras que la conservación de eventos de negocio se regirá por ADR-005.

Las restricciones de integridad y el control de concurrencia se desarrollan en [Domain and Data Model](design/domain-and-data.md).

Las operaciones simuladas en el prototipo no permiten verificar la atomicidad ni el rollback de las operaciones sobre una base de datos relacional.

### 4.2. Respaldos

Se propone un procedimiento básico de respaldo de PostgreSQL utilizando herramientas compatibles, como `pg_dump`.

El procedimiento deberá documentar:

- Información y bases de datos incluidas.
- Comando o mecanismo de generación.
- Ubicación del archivo de respaldo.
- Restricciones de acceso.
- Método de transferencia, si corresponde.
- Procedimiento de restauración.

Los respaldos contendrán únicamente información sintética del entorno de prueba y deberán protegerse frente a accesos no autorizados.

La existencia de un archivo de respaldo no será suficiente para demostrar la capacidad de recuperación.

### 4.3. Recuperación

La validación de recuperación contemplará el siguiente procedimiento:

1. Crear un conjunto conocido de datos sintéticos.
2. Generar un respaldo de PostgreSQL.
3. Restaurarlo en una base de datos de prueba.
4. Consultar los registros recuperados.
5. Verificar relaciones, asignaciones y eventos de auditoría.
6. Documentar el resultado de la restauración.

Adicionalmente, deberá comprobarse que un reinicio controlado de `C-API` no elimine información previamente confirmada.

La arquitectura propuesta no presupone alta disponibilidad, replicación de base de datos ni recuperación automática ante cualquier fallo físico.

Las pruebas de persistencia y restauración servirán como evidencia del escenario QS-07.

El funcionamiento de `localStorage` en el prototipo HTML no demuestra recuperación de C-API ni restauración de PostgreSQL.

## 5. Riesgos y restricciones operacionales

Los riesgos principales del despliegue propuesto son:

| Riesgo | Consecuencia | Mitigación propuesta |
|---|---|---|
| Recursos insuficientes | Lentitud o interrupciones. | Ejecutar pruebas con carga sintética y ajustar recursos. |
| PostgreSQL expuesto públicamente | Acceso no autorizado. | Restringir conexiones y privilegios. |
| Secretos incluidos en el repositorio | Compromiso de credenciales. | Utilizar configuración externa. |
| Almacenamiento no persistente | Pérdida de datos después de reinicios. | Configurar y comprobar persistencia. |
| Ausencia de respaldos verificados | Imposibilidad de restaurar información. | Ejecutar pruebas de respaldo y restauración. |
| Fallos del SIS simulado | Interrupción de consultas académicas. | Aislar integración y controlar tiempos de espera. |
| Migraciones incompatibles | Fallos de aplicación o pérdida de integridad. | Mantener migraciones versionadas. |
| Autenticación insegura | Accesos indebidos. | Verificar credenciales, sesiones y autorización. |
| Dependencias sin versiones definidas | Problemas de reproducibilidad. | Documentar y fijar versiones compatibles. |
| Confusión entre prototipo y arquitectura desplegada | Presentación de simulaciones locales como mecanismos técnicos implementados. | Identificar el alcance de `prototype/index.html` y mantener explícitas sus limitaciones. |

La integración real con los sistemas de Horizon University permanece fuera del alcance de la propuesta inicial.

Tampoco se contempla inicialmente una infraestructura distribuida con múltiples réplicas, balanceadores de carga, colas de mensajes o workers independientes.

Estas capacidades podrán evaluarse posteriormente si los requisitos o las pruebas justifican su incorporación.

Las mitigaciones presentadas son propuestas y deberán comprobarse antes de considerarlas implementadas.

## 6. Validación y trazabilidad

El entorno futuro deberá permitir ejecutar pruebas funcionales y arquitectónicas reproducibles.

Las principales verificaciones previstas son:

| Escenario | Verificación |
|---|---|
| QS-01 — Seguridad | Autorización según roles, recursos y relaciones vigentes. |
| QS-02 — Integridad | Transiciones válidas, asignaciones atómicas y concurrencia. |
| QS-03 — Auditabilidad | Persistencia de eventos obligatorios con los cambios correspondientes. |
| QS-04 — Rendimiento | Consultas operacionales con carga sintética. |
| QS-05 — Mantenibilidad | Revisión de los límites modulares y pruebas de regresión. |
| QS-06 — Usabilidad | Ejecución de flujos representativos por stakeholder. |
| QS-07 — Recuperación | Conservación de datos tras reinicio y restauración de respaldo. |

Para QS-04 se contempla un escenario de **12.000 casos sintéticos y 20 solicitudes concurrentes durante cinco minutos**, conforme al escenario de calidad definido.

El tiempo de respuesta deberá medirse bajo las condiciones documentadas. La selección de hardware no demuestra por sí sola el cumplimiento del objetivo.

Cada prueba ejecutada deberá registrar su configuración, procedimiento, resultado esperado, resultado observado y evidencias.

Los resultados deberán diferenciar pruebas aprobadas, fallidas y pendientes.

**Estado actual:** las 18 pruebas técnicas definidas en el plan permanecen en `NOT EXECUTED`. La disponibilidad del prototipo interactivo no modifica ese estado.

### 6.1. Relación con decisiones arquitectónicas

| Referencia | Relación |
|---|---|
| ADR-001 | Monolito modular y despliegue simplificado. |
| ADR-002 | PostgreSQL como base de datos relacional persistente. |
| ADR-003 | Consistencia del ciclo de vida y transacciones. |
| ADR-004 | Autenticación interna y autorización por recursos. |
| ADR-005 | Auditoría persistente y eventos de negocio. |
| AD-07 | Viabilidad y recuperación del entorno. |
| V-DEP | Distribución de contenedores y comunicaciones. |

### 6.2. Documentos relacionados

- [Deployment View](views/deployment-view.md)
- [Container View](views/container-view.md)
- [Component View](views/component-view.md)
- [Architecture Decision Records](adr/README.md)
- [Quality Scenarios](quality-scenarios.md)
- [Security Design](design/security.md)
- [API Contracts](design/interfaces.md)
- [Domain and Data Model](design/domain-and-data.md)
- [Validation Plan](validation/test-plan.md)
- [Validation Findings](validation/findings.md)
- [Project Status](project-status.md)
- [Prototipo interactivo y limitaciones](../../prototype/README.md)

## 7. Conclusión

El despliegue propuesto para DH-01 prioriza la simplicidad, la reproducibilidad y la viabilidad técnica.

La aplicación web React, el backend Node.js + Express, PostgreSQL y el simulador del SIS podrán ejecutarse en un mismo equipo físico mediante procesos separados.

Esta configuración mantendrá la arquitectura de monolito modular, la autenticación interna, la autorización en el backend, la persistencia relacional y la auditoría de las operaciones relevantes.

La capacidad real de infraestructura deberá evaluarse mediante las pruebas previstas, especialmente en rendimiento, seguridad y recuperación.

La Evaluación 2 incluye un **prototipo HTML interactivo**, cuya ejecución no requiere la infraestructura documentada en este archivo.

**Este documento complementa la Vista de Despliegue y establece los supuestos necesarios para una implementación y validación futura de DH-01, sin presentar la infraestructura propuesta como una solución ya desplegada o verificada.**
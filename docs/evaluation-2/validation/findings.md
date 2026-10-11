# DH-01 — Estado y Registro de Resultados de Validación

**Estado:** Documentación de validación preparada; prototipo HTML interactivo disponible; 18 pruebas técnicas no ejecutadas  
**Alcance:** Estado de validación de la Evaluación 2 y registro de resultados para futuras ejecuciones

## 1. Propósito y estado actual

Este documento presenta el estado de validación del proyecto DH-01 y establece los criterios para registrar resultados, evidencias e incidencias cuando se realicen pruebas sobre una implementación futura.

Complementa el [Plan de Validación](test-plan.md), diferenciando cuatro elementos:

- **Diseño documentado:** mecanismos, componentes y comportamientos definidos en la propuesta arquitectónica.
- **Prototipo interactivo:** demostración local de las interfaces, roles, estados y principales flujos académicos.
- **Validación planificada:** pruebas, procedimientos y criterios de aceptación establecidos para evaluar el diseño.
- **Validación ejecutada:** resultados observados y respaldados por evidencia reproducible.

La entrega de la Evaluación 2 comprende una **propuesta de arquitectura y diseño**, acompañada de un prototipo HTML interactivo disponible en [`prototype/index.html`](../../../prototype/index.html).

El prototipo utiliza datos sintéticos, lógica ejecutada en el navegador y almacenamiento local mediante `localStorage`. Permite explorar el comportamiento previsto del sistema, incluyendo cambios de perfil, registro y gestión de solicitudes, seguimiento e historial.

**El prototipo no implementa la arquitectura integrada propuesta de React, Node.js/Express, PostgreSQL y X-SIS. Tampoco incorpora autenticación real, transacciones de servidor ni controles de concurrencia mediante base de datos.**

Se han definido los procedimientos de validación técnica, pero no se han ejecutado las 18 pruebas funcionales, de integración, seguridad, rendimiento o recuperación previstas sobre la arquitectura propuesta.

Por consiguiente, las demostraciones disponibles en el prototipo no deben interpretarse como pruebas de conformidad técnica del sistema diseñado.

### 1.1. Balance del estado de la entrega

| Aspecto | Estado | Alcance |
|---|---|---|
| Requisitos y arquitectura | Documentados. | Permiten justificar y revisar las decisiones de diseño. |
| Escenarios de calidad | Definidos. | Establecen respuestas esperadas y criterios observables. |
| Modelo de dominio y contratos | Documentados como propuestas. | No demuestran funcionamiento en una implementación integrada. |
| Plan de validación | Definido. | Incluye 18 pruebas técnicas previstas. |
| Prototipo interactivo | Incluido en `prototype/index.html`. | Permite explorar flujos, estados, roles y restricciones simuladas. |
| Implementación de C-API y C-DB | No suministrada. | No existen servicios backend ni persistencia PostgreSQL ejecutables. |
| Integración X-SIS | No implementada. | Se mantiene como servicio HTTP mock propuesto. |
| Pruebas automatizadas de la arquitectura | No suministradas. | No existen resultados de ejecución técnica. |
| Evidencias de pruebas técnicas | No generadas. | No existen métricas ni resultados empíricos que reportar. |
| Políticas institucionales | Algunas pendientes de confirmación. | BR-09 continúa abierta. |

La disponibilidad del prototipo representa un avance en la comunicación visual y la exploración de la experiencia de usuario.

Sin embargo, **no demuestra empíricamente que la arquitectura propuesta satisfaga los criterios de seguridad, integridad transaccional, auditoría, rendimiento o recuperación**.

La implementación y validación de estos mecanismos permanecen pendientes.

### 1.2. Alcance de la validación documental y del prototipo

| Fuente | Información que permite revisar o demostrar | Información que no demuestra |
|---|---|---|
| `requirements-and-drivers.md` | Requisitos, reglas, restricciones y supuestos. | Cumplimiento funcional del sistema implementado. |
| `quality-scenarios.md` | Escenarios y medidas propuestas. | Métricas obtenidas. |
| `adr/` | Alternativas, decisiones y consecuencias. | Funcionamiento de las decisiones en código. |
| `views/` | Componentes, límites e interacciones. | Servicios realmente desplegados. |
| `design/` | Dominio, datos, ciclo de vida, interfaces y seguridad. | Endpoints o transacciones ejecutables. |
| `prototype/index.html` | Navegación, cinco perfiles, estados y flujos interactivos simulados. | Autenticación, autorización, transacciones y concurrencia reales. |
| `test-plan.md` | Procedimientos y criterios de aceptación. | Resultados de pruebas técnicas. |
| `traceability.md` | Relaciones entre requisitos, decisiones y validación. | Conformidad técnica demostrada. |
| `project-status.md` | Riesgos, decisiones abiertas y actividades pendientes. | Mitigaciones técnicas ya ejecutadas. |

La revisión documental puede identificar contradicciones, ausencias o inconsistencias entre artefactos.

El prototipo interactivo permite explorar cómo se representarían determinados comportamientos y restricciones desde la interfaz.

Ninguno de estos recursos permite concluir por sí solo que el sistema futuro funcionará correctamente sin ejecutar las pruebas técnicas correspondientes.

Por ejemplo, documentar transacciones PostgreSQL y `expectedVersion`, o simular visualmente un conflicto, no demuestra que dos operaciones concurrentes mantengan correctamente la integridad del ciclo de vida en una base de datos real.

## 2. Estado de las pruebas previstas

El [Plan de Validación](test-plan.md) define 18 pruebas que cubren funcionalidades, integridad, seguridad, auditoría y atributos de calidad.

Dado que no se dispone de una implementación integrada de la arquitectura propuesta, **las 18 pruebas técnicas permanecen en estado `NOT EXECUTED`**.

| Prueba | Objetivo principal | Estado | Evidencia |
|---|---|---|---|
| T-SEC-01 | Confidencialidad y autorización contextual. | NOT EXECUTED | No disponible |
| T-SEC-02 | Protección CSRF y recursos anidados. | NOT EXECUTED | No disponible |
| T-LIFE-01 | Restricciones de resolución. | NOT EXECUTED | No disponible |
| T-LIFE-02 | Consistencia ante concurrencia. | NOT EXECUTED | No disponible |
| T-AUD-01 | Historial y auditoría de reasignaciones. | NOT EXECUTED | No disponible |
| T-PERF-01 | Rendimiento de consultas. | NOT EXECUTED | No disponible |
| T-MOD-01 | Modificabilidad. | NOT EXECUTED | No disponible |
| T-UX-01 | Usabilidad. | NOT EXECUTED | No disponible |
| T-REC-01 | Recuperación después de reinicio. | NOT EXECUTED | No disponible |
| T-FUNC-01 | Flujo funcional completo. | NOT EXECUTED | No disponible |
| T-TRIAGE-01 | Clasificación y asignación. | NOT EXECUTED | No disponible |
| T-DATA-01 | Integridad relacional. | NOT EXECUTED | No disponible |
| T-TXN-01 | Atomicidad y rollback. | NOT EXECUTED | No disponible |
| T-AUTH-01 | Autenticación y sesiones. | NOT EXECUTED | No disponible |
| T-API-01 | Contratos y validaciones HTTP. | NOT EXECUTED | No disponible |
| T-SIS-01 | Integración SIS simulada. | NOT EXECUTED | No disponible |
| T-REC-02 | Respaldo y restauración. | NOT EXECUTED | No disponible |
| T-DEP-01 | Despliegue y configuración. | NOT EXECUTED | No disponible |

**Balance:** 18 pruebas técnicas diseñadas y 0 pruebas técnicas ejecutadas.

No se reportan tasas de aprobación, mediciones reales de latencia, tiempos de recuperación ni resultados de participantes.

Los valores establecidos en QS-04, QS-06 y QS-07 son objetivos propuestos y no resultados observados.

El prototipo HTML está disponible como material de demostración. Su funcionamiento no cambia automáticamente el estado de las pruebas del catálogo.

### 2.1. Reglas de clasificación

Cuando se ejecuten pruebas, se utilizarán los siguientes estados:

| Estado | Criterio |
|---|---|
| NOT EXECUTED | La prueba no se ha intentado. |
| BLOCKED | Se inició un intento documentado, pero una precondición impidió continuar. |
| PASS | La ejecución cumple el criterio de aceptación y existe evidencia suficiente. |
| FAIL | El comportamiento observado incumple el criterio. |
| INCONCLUSIVE | La ejecución no permite determinar el cumplimiento. |

Una prueba no ejecutada permanecerá como `NOT EXECUTED`, aunque todavía no exista la infraestructura necesaria para realizarla.

`BLOCKED` se utilizará solamente cuando se haya intentado una ejecución y exista registro del impedimento.

No se asignará `PASS` por disponer de diagramas, contratos, pseudocódigo, procedimientos de prueba o un prototipo interactivo.

Los resultados futuros deberán asociarse con la versión concreta sobre la que fueron obtenidos.

### 2.2. Relación del prototipo con las pruebas previstas

El prototipo permite ilustrar visualmente algunos comportamientos relacionados con el catálogo de validación.

| Capacidad ilustrada | Pruebas relacionadas | Limitación |
|---|---|---|
| Vistas diferenciadas según el perfil. | T-SEC-01, T-SEC-02 | No se implementa autorización ni protección CSRF desde un servidor. |
| Gestión de estados y restricciones de resolución. | T-LIFE-01 | No se ejecutan transacciones sobre PostgreSQL. |
| Conflictos de versión simulados. | T-LIFE-02 | No se demuestra concurrencia real entre conexiones independientes. |
| Historial de acciones y reasignaciones. | T-AUD-01 | No existe auditoría persistente y atómica en C-API y C-DB. |
| Registro, clasificación y asignación. | T-FUNC-01, T-TRIAGE-01 | No existe integración C-WEB → C-API → C-DB. |
| Navegación y comprensión de estados. | T-UX-01 | No se ha realizado la evaluación formal con cinco participantes. |

Estas relaciones indican qué decisiones pueden explicarse mediante una demostración interactiva.

**No constituyen resultados aprobados de las pruebas técnicas.**

## 3. Observaciones documentales y riesgos pendientes

Las siguientes observaciones describen el estado documental de la entrega, la existencia del prototipo y las condiciones que deberán considerarse antes de una implementación.

| ID | Observación | Implicación | Acción futura |
|---|---|---|---|
| OBS-DOC-01 | Existe un prototipo HTML interactivo con lógica local y datos sintéticos, pero no una implementación integrada de la arquitectura. | Se pueden explorar los flujos, pero no ejecutar pruebas técnicas de backend y PostgreSQL. | Implementar un incremento integrado y verificable cuando corresponda. |
| OBS-DOC-02 | No existen resultados de las 18 pruebas técnicas. | No se puede declarar cumplimiento empírico de los atributos de calidad. | Registrar resultados mediante RUN-ID y TEST-ID. |
| OBS-DOC-03 | Algunas políticas institucionales permanecen abiertas. | La validación técnica no reemplaza la aprobación institucional. | Confirmar las decisiones relacionadas con BR-09. |
| OBS-DOC-04 | Contratos, datos y controles de la arquitectura se encuentran documentados como propuestas. | Su comportamiento requiere verificación posterior. | Contrastar el código futuro con las especificaciones. |
| OBS-DOC-05 | Los perfiles, permisos, historial y conflictos del prototipo funcionan como simulaciones locales. | Podrían confundirse con mecanismos reales de seguridad, auditoría y concurrencia. | Mantener explícitas las limitaciones del prototipo en documentación y presentación. |

Estas observaciones no son defectos funcionales medidos mediante una campaña de pruebas.

Su propósito es mantener transparencia respecto al alcance real de la Evaluación 2.

Los riesgos y actividades relacionadas se encuentran en [Project Status](../project-status.md).

### 3.1. Diferencia entre hallazgo, riesgo y decisión abierta

Se utilizarán las siguientes categorías:

- **Falla de ejecución:** comportamiento observado que incumple un criterio verificable.
- **Riesgo:** situación posible que podría comprometer el proyecto.
- **Decisión abierta:** definición o política pendiente de aprobación.
- **Observación documental:** condición identificada en los artefactos de diseño o en el alcance de la entrega.

Estas categorías no son equivalentes.

Por ejemplo, la falta de aprobación institucional de una política de escalamiento corresponde a una decisión abierta.

En cambio, si una prueba técnica demostrara que un estudiante puede consultar una `InternalNote` desde una API real, existiría una falla verificable de confidencialidad.

De igual manera, que el prototipo permita cambiar de perfil sin autenticarse no demuestra por sí mismo una vulnerabilidad en C-API, porque ese componente todavía no está implementado y el selector tiene un propósito de demostración.

La existencia del prototipo facilita comunicar el diseño, pero no elimina la necesidad de verificar posteriormente los mecanismos propuestos.

### 3.2. Actualización de observaciones

Cuando se realice una nueva revisión, deberán registrarse:

1. Fecha de revisión.
2. Versión o commit del repositorio.
3. Artefactos examinados.
4. Cambios identificados.
5. Nuevas evidencias disponibles.
6. Consecuencias sobre el estado de las pruebas.

Las observaciones anteriores podrán conservarse como antecedentes históricos, siempre que se diferencien claramente del estado de la nueva revisión.

## 4. Balance por atributo de calidad

Esta sección relaciona los siete escenarios de calidad con sus mecanismos propuestos, las capacidades ilustradas por el prototipo y el estado de validación técnica.

### 4.1. Confidencialidad — QS-01

La arquitectura propone autorización contextual basada en roles, relaciones vigentes, alcance académico y sensibilidad de los datos.

Las notas internas se mantendrán separadas de las recomendaciones y de la información destinada al estudiante.

La validación prevista contempla:

- Accesos autorizados y no autorizados.
- Consultas de casos fuera del alcance del usuario.
- Respuestas de listas e historial.
- Protección de `InternalNote`.
- Validación de recursos anidados.
- Protección CSRF en operaciones de escritura.

**Relación con el prototipo:** permite explorar vistas diferenciadas por perfil y restricciones visuales sobre determinada información.

**Pruebas relacionadas:** T-SEC-01 y T-SEC-02.

**Estado:** `NOT EXECUTED`. No se dispone de respuestas reales de API que demuestren el cumplimiento de los controles.

La selección de perfiles en el prototipo no constituye autenticación ni autorización segura.

### 4.2. Integridad del ciclo de vida — QS-02

El diseño propone siete estados, transiciones controladas y validaciones centralizadas en C-API.

Para resolver un caso se requiere una `Recommendation` registrada y que no existan `FollowUp` en estado `PENDING` ni `InformationRequest` en estado `OPEN`.

Las operaciones críticas utilizarán transacciones PostgreSQL, bloqueo común del caso y control mediante `expectedVersion`.

La validación prevista comprende:

- Rechazo de transiciones inválidas.
- Rechazo de resolución con pendientes.
- Control de versiones desactualizadas.
- Operaciones concurrentes de seguimiento y resolución.
- Verificación del estado persistido después de conflictos.
- Rollback ante errores obligatorios.

**Relación con el prototipo:** permite ilustrar transiciones, condiciones de resolución y conflictos de versión simulados.

**Pruebas relacionadas:** T-LIFE-01, T-LIFE-02 y T-TXN-01.

**Estado:** `NOT EXECUTED`. No existen resultados de operaciones concurrentes reales ni evidencia transaccional.

Las restricciones simuladas no demuestran que los invariantes se mantengan frente a solicitudes concurrentes procesadas por un servidor.

### 4.3. Trazabilidad — QS-03

El diseño contempla un historial persistente de eventos y asignaciones.

Cada evento obligatorio deberá corresponder con una operación confirmada y almacenarse dentro de su misma transacción.

La validación comprobará:

- Conservación de asignaciones anteriores.
- Registro de eventos obligatorios.
- Orden mediante `AuditEvent.caseSequence`.
- Correspondencia entre cambios y eventos.
- Ausencia de eventos de éxito para operaciones revertidas.
- Exclusión de contenido confidencial innecesario.

**Relación con el prototipo:** incorpora una representación del historial y permite visualizar operaciones de gestión de casos.

**Pruebas relacionadas:** T-AUD-01 y T-TXN-01.

**Estado:** `NOT EXECUTED`. No se dispone de registros persistidos en PostgreSQL que permitan reconstruir operaciones reales del backend.

El historial local no demuestra atomicidad entre los cambios y sus eventos de auditoría.

### 4.4. Rendimiento — QS-04

El escenario establece como objetivo una prueba con:

| Parámetro | Objetivo propuesto |
|---|---|
| Volumen de datos | 12.000 casos sintéticos |
| Concurrencia | 20 solicitudes simultáneas |
| Duración | 5 minutos |
| Tiempo de respuesta | Al menos 95 % de respuestas del servidor por debajo de 800 ms |
| Errores de consulta | Ninguno bajo las condiciones definidas |

La evaluación futura deberá documentar recursos, distribución de datos, consultas, filtros, paginación y herramientas utilizadas.

**Relación con el prototipo:** permite explorar visualmente consultas y listados, pero no produce métricas representativas del backend propuesto.

**Prueba relacionada:** T-PERF-01.

**Estado:** `NOT EXECUTED`. No se dispone de métricas reproducibles bajo las condiciones establecidas.

La selección de PostgreSQL o Node.js, así como el funcionamiento visual del prototipo, no permite afirmar que se alcanzará el objetivo sin una medición.

### 4.5. Modificabilidad — QS-05

La propuesta utiliza un monolito modular con componentes lógicos y contratos internos diferenciados.

Para evaluar su modificabilidad se deberá realizar un cambio controlado y comprobar su impacto sobre el código de la arquitectura implementada.

La evidencia prevista incluye:

- Descripción del cambio.
- Archivos y módulos modificados.
- Dependencias afectadas.
- Resultados de pruebas del componente.
- Resultados de regresión.
- Comparación entre versiones.

**Relación con el prototipo:** ofrece una referencia visual de las funcionalidades y flujos esperados, pero no implementa la estructura modular de C-API.

**Prueba relacionada:** T-MOD-01.

**Estado:** `NOT EXECUTED`. No existe una implementación integrada sobre la cual medir el impacto de modificaciones entre los módulos arquitectónicos propuestos.

### 4.6. Usabilidad — QS-06

La arquitectura propone interfaces y representaciones diferenciadas según el rol del usuario.

El escenario de usabilidad busca comprobar que las personas pueden interpretar el estado de un caso y reconocer la siguiente acción.

| Parámetro | Criterio propuesto |
|---|---|
| Participantes | 5 |
| Resultado esperado | Al menos 4 de 5 identifican correctamente estado y siguiente acción |
| Registro | Respuesta correcta sin ayuda, correcta con ayuda o incorrecta |

La evaluación deberá conservar las tareas, preguntas y respuestas observadas.

**Relación con el prototipo:** constituye un recurso interactivo disponible para desarrollar una evaluación formativa de comprensión de estados y acciones.

**Prueba relacionada:** T-UX-01.

**Estado:** `NOT EXECUTED`. No se han registrado participantes ni resultados de sesiones de evaluación conforme al procedimiento.

La disponibilidad del prototipo hace posible preparar esta evaluación sin esperar necesariamente al backend completo, pero no equivale a haberla realizado.

### 4.7. Recuperación — QS-07

La propuesta contempla persistencia independiente en PostgreSQL y reinicio controlado del backend C-API.

El escenario busca comprobar que, después de reiniciar C-API manteniendo disponible la base de datos, se recupera la capacidad funcional dentro del objetivo establecido.

| Aspecto | Criterio propuesto |
|---|---|
| Condición | Reinicio controlado de C-API |
| Persistencia | PostgreSQL permanece disponible |
| Recuperación | Máximo 60 segundos |
| Verificación | Operación funcional y conservación de registros confirmados |

Adicionalmente, T-REC-02 evaluará un proceso de respaldo y restauración.

**Relación con el prototipo:** los datos pueden mantenerse localmente mediante `localStorage`, pero este almacenamiento no equivale a persistencia del backend ni a una estrategia de recuperación de PostgreSQL.

**Pruebas relacionadas:** T-REC-01 y T-REC-02.

**Estado:** `NOT EXECUTED`. No existen mediciones de recuperación ni evidencias de restauración.

QS-07 no constituye una promesa de alta disponibilidad ni demuestra recuperación ante pérdida física de datos.

## 5. Evidencia necesaria y criterios de suficiencia

### 5.1. Matriz de evidencia esperada

La siguiente matriz identifica qué información deberá recopilarse cuando se ejecuten las pruebas técnicas.

| Prueba | Evidencia principal | Evidencia complementaria | Estado |
|---|---|---|---|
| T-SEC-01 | Solicitudes y respuestas por actor. | Permisos y persistencia sin cambios indebidos. | No disponible |
| T-SEC-02 | Solicitudes CSRF y recursos anidados. | Estado antes y después. | No disponible |
| T-LIFE-01 | Estados y pendientes antes y después. | Eventos y respuestas de conflicto. | No disponible |
| T-LIFE-02 | Resultados concurrentes y estado final. | Versiones y eventos. | No disponible |
| T-AUD-01 | Asignaciones e historial. | Autorización después de reasignar. | No disponible |
| T-PERF-01 | Métricas y configuración de carga. | Recursos y datos utilizados. | No disponible |
| T-MOD-01 | Cambios y regresión. | Dependencias revisadas. | No disponible |
| T-UX-01 | Tareas y respuestas de participantes. | Observaciones de la sesión. | No disponible |
| T-REC-01 | Tiempo de recuperación y consulta funcional. | Registros de reinicio. | No disponible |
| T-FUNC-01 | Solicitudes y transiciones del flujo. | Estado persistido y eventos. | No disponible |
| T-TRIAGE-01 | Clasificación y asignaciones. | Rechazos y eventos. | No disponible |
| T-DATA-01 | Comprobación de restricciones. | Esquema y migraciones. | No disponible |
| T-TXN-01 | Estado posterior al fallo. | Punto de inyección. | No disponible |
| T-AUTH-01 | Login, expiración y revocación. | Registros sanitizados. | No disponible |
| T-API-01 | Solicitudes y respuestas HTTP. | Persistencia tras rechazos. | No disponible |
| T-SIS-01 | Respuestas del mock y adaptador. | Configuración de fallos y demoras. | No disponible |
| T-REC-02 | Restauración y verificación relacional. | Metadatos del respaldo. | No disponible |
| T-DEP-01 | Comprobación de servicios y conectividad. | Configuración no secreta. | No disponible |

La matriz establece requisitos de evidencia futura y no resultados obtenidos.

El prototipo HTML constituye un artefacto de demostración existente, pero no reemplaza los registros técnicos previstos en esta matriz.

### 5.2. Pertinencia

Cada evidencia deberá demostrar directamente el criterio correspondiente.

Por ejemplo, una captura del prototipo donde un caso aparece como `RESOLVED` no demuestra que no existieran seguimientos pendientes en PostgreSQL ni que se haya controlado correctamente la concurrencia.

Para demostrarlo será necesario consultar el estado persistido, los pendientes, la versión y los resultados de las operaciones relacionadas.

Asimismo, mostrar una vista de estudiante sin notas internas no demuestra por sí solo que una API rechace accesos no autorizados.

La evidencia deberá corresponder al mecanismo específico que pretende verificarse.

### 5.3. Reproducibilidad

Las pruebas deberán incluir información suficiente para permitir una ejecución equivalente:

- Precondiciones.
- Datos sintéticos.
- Versión evaluada.
- Procedimiento.
- Comandos y parámetros.
- Herramientas y versiones.
- Resultados y evidencia.

Cuando falte información esencial para interpretar una ejecución, podrá clasificarse como `INCONCLUSIVE`, aunque el resultado aparente sea favorable.

### 5.4. Integridad, conservación y sanitización

La evidencia deberá conservar su relación con la revisión original.

Una corrección posterior no modificará retrospectivamente el resultado registrado para una ejecución fallida.

Las pruebas desfavorables no deberán eliminarse para aparentar una campaña completamente satisfactoria.

Antes de compartir evidencia se retirarán:

- Contraseñas.
- Cookies y tokens.
- Secretos de configuración.
- Cadenas de conexión.
- Información real no autorizada.

Los identificadores sintéticos podrán conservarse cuando permitan reconstruir el comportamiento evaluado.

La sanitización no deberá eliminar los elementos necesarios para demostrar el resultado.

## 6. Registro de futuras ejecuciones e incidencias

Esta sección establece cómo deberán documentarse las pruebas cuando exista una implementación que permita ejecutarlas.

Los formatos descritos son plantillas de trabajo futuro y no evidencias actuales.

### 6.1. Ficha de resultados

| Campo | Información requerida |
|---|---|
| Identificador | RUN-ID y TEST-ID. |
| Fecha y responsable | Fecha, zona horaria y ejecutor. |
| Versión evaluada | Revisión de código, migraciones y dependencias. |
| Entorno | Recursos, topología y configuración no secreta. |
| Datos iniciales | Semillas y precondiciones. |
| Procedimiento | Pasos, comandos y parámetros utilizados. |
| Resultado esperado | Criterio definido en el plan. |
| Resultado observado | Respuestas, estados, métricas o comportamiento real. |
| Clasificación | PASS, FAIL, BLOCKED o INCONCLUSIVE, según corresponda. |
| Evidencia | Rutas relativas a archivos reales. |
| Seguimiento | Incidencia, severidad, corrección y reevaluación. |

Cada resultado deberá referirse a una ejecución verificable.

Los resultados anteriores se conservarán cuando existan nuevas ejecuciones.

### 6.2. Organización de evidencia

Si en una fase posterior se ejecutan pruebas, podrá utilizarse una estructura organizada por campaña y prueba.

La siguiente estructura es **únicamente una propuesta futura** y no implica que la carpeta exista actualmente:

```text
docs/evaluation-2/validation/evidence/
└── <RUN-ID>/
    ├── environment.md
    ├── manifest.json
    └── <TEST-ID>/
        ├── procedure.md
        ├── result.md
        ├── sanitized-responses.json
        └── execution-output.txt
```

El RUN-ID identificará una campaña concreta y el TEST-ID permitirá relacionar cada resultado con una prueba del catálogo.

Solo se crearán los archivos necesarios cuando existan ejecuciones reales.

No se crearán carpetas vacías ni archivos de relleno para representar evidencia que todavía no se ha producido.

El archivo `prototype/index.html` no pertenece a esta carpeta de evidencias, ya que corresponde a un artefacto de demostración y se mantiene en el directorio `prototype/` del repositorio.

### 6.3. Manifiesto de evidencia

| Dato | Finalidad |
|---|---|
| Ruta del archivo | Localizar la evidencia. |
| Tipo y descripción | Explicar su contenido. |
| Prueba y subcaso | Relacionar evidencia y criterio. |
| Revisión de código | Identificar la versión evaluada. |
| Fecha con zona horaria | Ordenar ejecuciones. |
| Herramienta y versión | Facilitar reproducción. |
| Sanitización aplicada | Documentar datos retirados. |
| Hash, cuando corresponda | Detectar modificaciones posteriores. |

Un hash puede ayudar a comprobar la identidad de un archivo, pero no demuestra que su contenido sea correcto ni que una prueba haya aprobado.

### 6.4. Registro de incidencias

| Campo | Descripción |
|---|---|
| Identificador | Referencia única de la incidencia. |
| Título | Comportamiento observado. |
| Requisito afectado | FR, QR, BR o escenario relacionado. |
| Reproducción | Precondiciones y pasos. |
| Esperado | Criterio documental vigente. |
| Observado | Resultado real con evidencia. |
| Impacto | Consecuencias sobre usuarios, datos u operación. |
| Severidad | Nivel justificado. |
| Estado | Abierta, en corrección, pendiente de reevaluación o cerrada. |
| Responsable | Persona o rol asignado. |
| Corrección | Revisión y reevaluación relacionadas. |

**Estado actual:** no se registran incidencias funcionales verificadas mediante la ejecución de las pruebas técnicas del catálogo.

Las observaciones documentales identificadas en la sección 3 no se clasifican como fallas funcionales.

### 6.5. Severidad propuesta

| Nivel | Criterio |
|---|---|
| Crítica | Exposición no autorizada o corrupción que comprometa invariantes centrales. |
| Alta | Incumplimiento de asignación, historial o flujo esencial sin alternativa segura. |
| Media | Deterioro de consultas, usabilidad o comportamiento operacional limitado. |
| Baja | Defecto localizado de presentación o documentación sin impacto central demostrado. |

La severidad no determina automáticamente la probabilidad del problema ni el esfuerzo necesario para corregirlo.

Estos aspectos se evaluarán por separado.

## 7. Reevaluación, resultados parciales y planificación

### 7.1. Reevaluación y comparación entre versiones

Cuando se corrija una falla, la prueba correspondiente deberá ejecutarse nuevamente sobre la revisión que contiene el cambio.

La nueva ejecución conservará una referencia al resultado original.

Una aprobación posterior no eliminará el registro de una falla anterior.

Las comparaciones de rendimiento deberán utilizar condiciones equivalentes o explicar sus diferencias.

Las pruebas de regresión cubrirán las responsabilidades y operaciones afectadas por cada modificación.

### 7.2. Interpretación de resultados parciales

**Prototipo interactivo.** La disponibilidad de `prototype/index.html` permite ilustrar los flujos y explorar su representación visual, sin demostrar que se hayan implementado o validado los servicios de la arquitectura propuesta.

**Implementación parcial.** Si en una etapa futura se implementan únicamente creación y asignación de casos, solo podrán declararse verificadas aquellas capacidades que efectivamente hayan superado pruebas documentadas.

Esto no permitirá afirmar que también se han validado resolución, concurrencia o recuperación.

**Pruebas con recursos limitados.** Una medición realizada con menos casos o menor concurrencia que los valores establecidos en QS-04 podrá considerarse preliminar, pero no demostrará automáticamente el cumplimiento del escenario.

**Políticas provisionales.** Una prueba podrá comprobar que el software respeta una política de permisos propuesta, sin demostrar que dicha política cuenta con aprobación institucional.

Todo resultado parcial deberá identificar su alcance, condiciones y limitaciones.

### 7.3. Relación con planificación y arquitectura

Los resultados de validación podrán justificar modificaciones en el diseño y generar actividades adicionales en el backlog.

Sin embargo, una falla no implicará necesariamente que el estilo arquitectónico seleccionado sea incorrecto.

Antes de revisar una decisión importante deberán analizarse las posibles causas, incluyendo implementación, configuración, persistencia y contratos.

Entre las pruebas de mayor relevancia se encuentran:

- `T-MOD-01`: límites modulares y modificabilidad.
- `T-PERF-01`: eficiencia de consultas y recursos.
- `T-LIFE-02`: consistencia de operaciones concurrentes.
- `T-AUD-01`: integridad del historial.
- `T-TXN-01`: atomicidad y rollback.

Las decisiones que cambien deberán reflejarse en los ADR, diagramas, contratos y matrices correspondientes.

### 7.4. Próximas actividades verificables

Para una fase futura de implementación y validación, se propone la siguiente secuencia:

1. Confirmar las políticas institucionales pendientes.
2. Seleccionar un incremento funcional de alcance limitado.
3. Utilizar el prototipo existente como referencia de flujos e interfaz cuando corresponda.
4. Preparar un entorno reproducible.
5. Configurar PostgreSQL y el servicio SIS simulado.
6. Implementar los contratos y reglas necesarios para el incremento.
7. Cargar datos sintéticos.
8. Ejecutar las pruebas críticas correspondientes al alcance implementado.
9. Registrar resultados mediante RUN-ID y TEST-ID.
10. Actualizar riesgos, decisiones y backlog a partir de la evidencia.

Estas actividades constituyen una propuesta de continuación del proyecto.

**No representan requisitos de implementación ya completados ni una condición adicional para la entrega documental de la Evaluación 2.**

## 8. Informe de campaña y conclusión

### 8.1. Estructura del informe futuro

Cuando exista una campaña de validación ejecutada, el informe deberá incluir:

1. Alcance de la campaña y versión evaluada.
2. Entorno, herramientas y datos sintéticos.
3. Pruebas efectivamente ejecutadas.
4. Criterios de aceptación utilizados.
5. Resultados observados y evidencia relacionada.
6. Incidencias y riesgos residuales.
7. Pruebas pendientes, bloqueadas o inconclusas.
8. Comparación con los objetivos de calidad.
9. Conclusiones limitadas al alcance demostrado.
10. Actividades de corrección y reevaluación.

Esta estructura es una plantilla para resultados futuros y no representa una campaña ejecutada.

### 8.2. Conclusión del estado de validación

DH-01 cuenta con una propuesta documental de arquitectura y diseño que define los requisitos, escenarios de calidad, componentes, decisiones, contratos y mecanismos principales del Academic Advising Case Management System.

La entrega incluye además un **prototipo HTML interactivo de demostración**, disponible en `prototype/index.html`, que representa mediante datos sintéticos los roles, estados y flujos académicos principales.

El proyecto dispone de un plan de validación con **18 pruebas técnicas previstas**, orientadas a comprobar integridad, confidencialidad, trazabilidad, rendimiento, modificabilidad, usabilidad y recuperación.

El prototipo no implementa el backend Node.js/Express, la base de datos PostgreSQL ni la integración HTTP con X-SIS.

Por tanto, las pruebas técnicas del catálogo permanecen en estado `NOT EXECUTED`, sin resultados empíricos ni evidencias reproducibles de la arquitectura integrada.

Los escenarios QS-01 a QS-07 continúan siendo objetivos de evaluación y no atributos de calidad técnicamente acreditados.

La documentación presentada permite evaluar la coherencia de la propuesta, sus decisiones, responsabilidades y procedimientos de validación. El prototipo complementa esta revisión mediante una representación interactiva del comportamiento esperado.

Los resultados técnicos podrán incorporarse en una etapa posterior, cuando exista una implementación y se realicen pruebas reproducibles.

**Documentos relacionados:**

- [Validation Plan](test-plan.md)
- [Requirements and Drivers](../requirements-and-drivers.md)
- [Quality Scenarios](../quality-scenarios.md)
- [Domain and Data Model](../design/domain-and-data.md)
- [Lifecycle and Runtime](../design/lifecycle-and-runtime.md)
- [API Contracts](../design/interfaces.md)
- [Security Design](../design/security.md)
- [Traceability Matrix](../traceability.md)
- [Project Status](../project-status.md)
- [Prototipo interactivo y limitaciones](../../../prototype/README.md)

Este documento mantiene una separación explícita entre **diseño documentado, prototipo interactivo, validación planificada y resultados efectivamente observados**, garantizando que la Evaluación 2 presente el estado real de DH-01 sin atribuir a la arquitectura una implementación o unas pruebas técnicas que todavía no se han realizado.
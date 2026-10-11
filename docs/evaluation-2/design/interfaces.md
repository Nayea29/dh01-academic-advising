# DH-01 — Contratos de Interfaces

**Architecture Drivers:** AD-01 a AD-07  
**Decisiones relacionadas:** ADR-001, ADR-003, ADR-004 y ADR-005

## 1. Propósito y límites

Este documento especifica las interfaces entre `C-WEB` y `C-API`, el contrato de integración simulada con `X-SIS` y los principales contratos internos del monolito modular.

Las rutas, estructuras y comportamientos descritos constituyen el diseño de interfaces previsto para DH-01.

`C-WEB` consumirá una API REST con mensajes JSON mediante HTTPS en los entornos desplegados. `C-API` será responsable de validar las entradas, aplicar los permisos y ejecutar las reglas de negocio. `C-DB` no será accesible directamente desde el navegador.

Los contratos deberán mantener coherencia con el [Modelo de Dominio y Datos](domain-and-data.md), el [Ciclo de Vida](lifecycle-and-runtime.md), las decisiones arquitectónicas y la vista de componentes.

El diseño busca garantizar que:

- Las operaciones HTTP respeten las transiciones válidas del ciclo de vida.
- La identidad y los permisos se determinen en el servidor.
- Las escrituras no sobrescriban silenciosamente cambios concurrentes.
- Las respuestas expongan únicamente información autorizada.
- Las integraciones externas no comprometan la consistencia de los casos.
- Los errores permitan a `C-WEB` actuar sin asumir resultados no confirmados.
- Cada contrato tenga criterios verificables y trazabilidad con los requisitos.

## 2. Convenciones, autenticación y validación

### 2.1. Convenciones generales

| Aspecto | Convención propuesta |
|---|---|
| Base de API | `/api/v1` |
| Representación | JSON UTF-8 e identificadores UUID internos. |
| Fechas | ISO 8601 con zona horaria; almacenamiento en UTC. |
| Autenticación | Cookie de sesión opaca administrada por el servidor. |
| Protección de escrituras | Token CSRF válido asociado a la sesión. |
| Escrituras sobre casos existentes | `expectedVersion` obligatorio y verificado dentro de la transacción. |
| Paginación | `page` desde 1 y `pageSize` entre 1 y 100; valor predeterminado propuesto de 20. |
| Ordenamiento | Campos permitidos mediante una lista explícita y criterio estable por identificador. |
| Validación | Rechazo de campos desconocidos, valores fuera de catálogo y formatos inválidos. |
| Auditoría | El actor se obtiene de la sesión, nunca de un campo enviado por el cliente. |

Se propone un límite inicial de 10.000 caracteres por campo textual y 64 KiB por cuerpo JSON, sujeto a revisión según los requisitos de usabilidad y recursos disponibles.

No se contemplan archivos adjuntos en el contrato inicial.

La versión de la API (`/api/v1`) es independiente del atributo `version` de cada caso. La primera identifica la versión del contrato HTTP; la segunda permite detectar modificaciones concurrentes sobre el recurso.

### 2.2. Autenticación y sesión

Conforme a ADR-004, DH-01 utilizará sesiones internas opacas administradas por el servidor.

| Método y ruta | Entrada | Resultado | Condición |
|---|---|---|---|
| POST `/auth/login` | `login`, `password` | 200 con identidad, roles y cookie de sesión. | Credenciales válidas y controles contra intentos repetidos. |
| GET `/auth/session` | Cookie de sesión | Identidad, roles, alcances y token CSRF. | Sesión vigente. |
| POST `/auth/logout` | Cookie y token CSRF | 204; revocación de sesión y eliminación de cookie. | Sesión válida o limpieza segura de cookie residual. |

Las rutas de la tabla son relativas a `/api/v1`.

El backend almacenará únicamente el resumen criptográfico del identificador de sesión, conforme al diseño definido en [Seguridad](security.md).

No se devolverán hashes de contraseñas, identificadores secretos de sesión ni credenciales en las respuestas JSON.

Los roles y ámbitos obtenidos mediante `/auth/session` podrán utilizarse para adaptar la interfaz, pero no reemplazarán la autorización que debe aplicarse en cada operación del backend.

### 2.3. Protección CSRF

Las operaciones que modifican información mediante sesiones basadas en cookies deberán incluir un token CSRF válido.

Se utilizará el encabezado propuesto:

`X-CSRF-Token`

El token se obtendrá mediante el contrato de sesión y estará vinculado al contexto de autenticación.

`C-API` comprobará su validez antes de ejecutar operaciones protegidas.

Una solicitud con una cookie válida, pero sin el token CSRF requerido, será rechazada sin modificar información de negocio.

La generación, validación y renovación del token se describen en [Security Design](security.md).

La protección CSRF complementa las políticas de autorización y las restricciones transaccionales, pero no las sustituye.

### 2.4. Respuestas y errores

Las consultas individuales devolverán un objeto `data` con la representación autorizada.

Las consultas de listas devolverán `data` y `pagination`, incluyendo `page`, `pageSize` y `total` calculados únicamente sobre los recursos permitidos al usuario.

Los errores utilizarán una estructura uniforme:

```json
{
  "error": {
    "code": "CASE_HAS_PENDING_ACTIONS",
    "message": "El caso conserva acciones pendientes.",
    "requestId": "req-synthetic-001"
  }
}
```

| HTTP | Uso propuesto |
|---|---|
| 200 | Consulta o modificación aceptada. |
| 201 | Recurso creado. |
| 204 | Operación completada sin contenido. |
| 400 | Formato, parámetros o campos inválidos. |
| 401 | Sesión ausente, inválida o expirada. |
| 403 | Operación o información denegada. |
| 404 | Recurso inexistente o fuera del alcance del usuario. |
| 409 | Conflicto de versión, estado o reglas de negocio. |
| 429 | Límite de solicitudes excedido. |
| 502 | Respuesta inválida de una dependencia externa. |
| 503 | Dependencia requerida temporalmente indisponible. |
| 504 | Tiempo de espera agotado de una dependencia externa. |
| 500 | Error interno no previsto. |

Para reducir la posibilidad de enumerar recursos, se utilizará `404` cuando un caso no exista o no sea visible para el usuario.

Si el usuario puede conocer el caso, pero no tiene permiso para realizar una operación o consultar una categoría protegida de información, se utilizará `403`.

Los errores no deberán revelar datos confidenciales ni detalles internos de infraestructura.

Una falla de protección CSRF provocará un rechazo de seguridad, sin ejecutar la operación solicitada.

### 2.5. Identificadores y pertenencia

Los UUID se validarán antes de consultar los recursos.

Un identificador correctamente formado no significa que el recurso exista ni que el usuario tenga autorización.

Cuando una ruta incluya `caseId` y el identificador de otro recurso, el backend verificará su relación.

| Entrada | Comprobación | Rechazo propuesto |
|---|---|---|
| `caseId` mal formado | Formato UUID. | 400 `INVALID_IDENTIFIER` |
| Caso inexistente | Existencia del recurso. | 404 `RESOURCE_NOT_FOUND` |
| Caso ajeno | Propiedad, asignación o ámbito. | 404 `RESOURCE_NOT_FOUND` |
| FollowUp perteneciente a otro caso | Relación con `caseId`. | 404 `RESOURCE_NOT_FOUND` |
| Nota de un caso conocido sin permiso | Sensibilidad y autorización. | 403 `OPERATION_FORBIDDEN` |

La validación deberá comprobar tanto la existencia como la pertenencia del recurso anidado.

### 2.6. Campos protegidos y valores controlados

El servidor asignará los campos relacionados con identidad, autoría, fechas de creación y versión inicial.

El cliente no podrá modificar directamente:

- `createdBy`
- `authorId`
- `actorId`
- `passwordHash`
- Roles o permisos
- Registros `AuditEvent`
- `status` mediante una actualización genérica

Los campos desconocidos serán rechazados.

| Campo | Regla |
|---|---|
| `origin` | Derivado de la ruta de creación. |
| `audience` | `INTERNAL` o `STUDENT`. |
| `expectedVersion` | Entero positivo obligatorio en escrituras sobre casos existentes. |
| `dueAt` | ISO 8601 con zona horaria; opcional. |
| `description`, `content`, `question`, `response` | Texto no vacío después de normalizar espacios periféricos. |
| `categoryId` | Categoría existente y activa. |
| `adviserId` | Usuario activo con rol Academic Adviser. |

Las fechas límite anteriores al momento de creación se tratarán conforme a la política institucional de seguimiento, que requiere validación.

Todas las operaciones protegidas heredarán las convenciones de autenticación, autorización, CSRF, validación y manejo de errores de esta sección.

## 3. Operaciones de casos y atención

### 3.1. Catálogo de endpoints

Las siguientes rutas se proponen para las operaciones principales de DH-01.

Todas utilizan el prefijo `/api/v1`.

| Método y ruta | Entrada principal | Resultado | Actor y condición |
|---|---|---|---|
| POST `/cases` | `description`, `categoryId` opcional | 201: caso `NEW`, id y versión. | Student; `studentId` derivado de sesión. |
| POST `/referrals` | `studentId`, `description`, `categoryId` opcional | 201: caso `NEW` con origen de referencia. | Program Coordinator autorizado. |
| GET `/cases` | Filtros y paginación | Lista autorizada. | Ámbito aplicado antes de paginar y contar. |
| GET `/cases/{caseId}` | Identificador | Representación según el rol. | Propiedad, asignación o ámbito autorizado. |
| POST `/cases/{caseId}/triage` | `expectedVersion` | Caso `UNDER_TRIAGE`. | Advising Coordinator; origen `NEW`. |
| POST `/cases/{caseId}/assignment` | `expectedVersion`, `categoryId`, `programId`, `adviserId` | Caso `ASSIGNED`. | Clasificación y asignación atómicas desde `UNDER_TRIAGE`. |
| POST `/cases/{caseId}/start` | `expectedVersion` | Caso `IN_PROGRESS`. | Adviser vigente; origen `ASSIGNED`. |
| POST `/cases/{caseId}/reassignments` | `expectedVersion`, `adviserId`, `reason` | Responsable y versión actualizados. | Coordinador autorizado; caso asignado y abierto. |
| POST `/cases/{caseId}/information-requests` | `expectedVersion`, `question` | Solicitud `OPEN` y estado de espera. | Adviser vigente; ninguna solicitud abierta previa. |
| POST `/cases/{caseId}/information-requests/{requestId}/response` | `expectedVersion`, `response` | Solicitud `ANSWERED` y estado recalculado. | Student asociado. |
| POST `/cases/{caseId}/follow-ups` | `expectedVersion`, `audience`, `description`, `dueAt` opcional | FollowUp `PENDING`. | Adviser vigente; estado permitido. |
| GET `/cases/{caseId}/follow-ups` | Paginación | Seguimientos autorizados. | Student solo recibe audiencia `STUDENT`. |
| POST `/cases/{caseId}/follow-ups/{followUpId}/complete` | `expectedVersion` | FollowUp `COMPLETED` y estado actualizado. | Adviser vigente; seguimiento correspondiente al caso. |
| POST `/cases/{caseId}/notes` | `expectedVersion`, `content` | Metadatos de nota creada. | Adviser autorizado. |
| GET `/cases/{caseId}/notes` | Paginación | Notas internas autorizadas. | Permiso específico; nunca Student. |
| POST `/cases/{caseId}/recommendations` | `expectedVersion`, `content` | Recomendación creada. | Adviser vigente; caso en atención. |
| GET `/cases/{caseId}/recommendations` | Paginación | Recomendaciones autorizadas. | Student propietario o personal autorizado. |
| POST `/cases/{caseId}/resolution` | `expectedVersion` | Caso `RESOLVED`. | Adviser vigente; todas las condiciones de cierre. |
| GET `/cases/{caseId}/history` | Paginación | Historial según autorización. | Personal autorizado; Student solo recibe hitos generales permitidos. |

La validación de identificadores anidados impedirá consultar o modificar recursos de otros casos.

No se ofrecerá un `PATCH` genérico para modificar estados, roles, responsables o niveles de visibilidad.

Las operaciones de triage y asignación estarán reservadas inicialmente al Advising Coordinator autorizado. Cualquier delegación futura requerirá una política explícita de concesión, alcance y revocación.

### 3.2. Reglas de las operaciones principales

**Creación y referencia:** la solicitud propia derivará `studentId` de la sesión autenticada. La referencia académica verificará el alcance del Program Coordinator respecto al estudiante.

**Triage y asignación:** el triage deberá iniciarse explícitamente. La clasificación y la asignación inicial se registrarán conjuntamente en una transacción.

**Reasignación:** estará permitida en `ASSIGNED`, `IN_PROGRESS`, `WAITING_FOR_INFORMATION` y `FOLLOW_UP`. Conservará el estado y actualizará la responsabilidad y el historial.

**Información adicional:** se propone una única `InformationRequest` abierta por caso. El Student asociado podrá responder, y el servidor calculará el estado resultante.

**Seguimientos:** crear un FollowUp en `IN_PROGRESS` llevará a `FOLLOW_UP`. Crear o completar actividades durante `WAITING_FOR_INFORMATION` conservará el estado de espera. Completar el último pendiente en `FOLLOW_UP` regresará a `IN_PROGRESS`.

**Notas y recomendaciones:** `InternalNote` y `Recommendation` serán recursos separados con políticas de acceso diferentes.

**Resolución:** únicamente podrá realizarse desde `IN_PROGRESS`, por el adviser vigente, con al menos una Recommendation, sin FollowUps `PENDING` y sin InformationRequests `OPEN`.

Las transiciones detalladas se encuentran en [Lifecycle and Runtime](lifecycle-and-runtime.md).

### 3.3. Representaciones diferenciadas

Las respuestas deberán construirse conforme al rol, la relación con el caso y la sensibilidad de la información.

| Representación | Campos previstos | Exclusiones |
|---|---|---|
| StudentCase | Identificador, categoría, estado, fechas generales, información autorizada del adviser, recomendaciones y seguimientos `STUDENT`. | Notas internas, motivos confidenciales, secretos y auditoría técnica. |
| AdviserCase | Datos del caso asignado, actividades, notas autorizadas y versión. | Credenciales y casos ajenos. |
| CoordinatorCase | Clasificación, responsable, información de carga e hitos operacionales dentro del ámbito permitido. | Notas internas sin autorización específica. |
| TechnicalHealth | Disponibilidad técnica mínima. | Usuarios, casos, cadenas de conexión y secretos. |

Las exclusiones se aplicarán desde el servidor.

Ocultar información en la interfaz no será suficiente para garantizar su confidencialidad.

Ni el rol de Program Coordinator, ni el de Advising Coordinator, ni IT Services concederán acceso automático a notas internas.

La edición de notas, los cambios de visibilidad y la eliminación de registros requerirán contratos adicionales y no forman parte del flujo inicial.

## 4. Ejemplos de contratos y conflictos

Los siguientes ejemplos utilizan identificadores y datos ficticios.

### 4.1. Solicitud propia

**Solicitud:**

POST `/api/v1/cases`

```json
{
  "description": "Solicito orientación para organizar mi carga académica."
}
```

La ausencia de `categoryId` permite realizar posteriormente la clasificación durante el triage.

El estudiante y su programa se obtienen del perfil autenticado.

**Respuesta propuesta — 201 Created:**

```json
{
  "data": {
    "id": "10000000-0000-4000-8000-000000000001",
    "status": "NEW",
    "origin": "STUDENT_REQUEST",
    "version": 1,
    "createdAt": "2026-10-10T15:00:00Z"
  }
}
```

La respuesta confirma la creación, pero no implica que exista una clasificación o asignación inicial.

### 4.2. Referencia de programa

**Solicitud:**

POST `/api/v1/referrals`

```json
{
  "studentId": "20000000-0000-4000-8000-000000000001",
  "description": "Se solicita acompañamiento sobre planificación académica."
}
```

El backend verificará que el Program Coordinator tenga autorización sobre el programa académico del estudiante.

El caso conservará `PROGRAM_REFERRAL` como origen.

El usuario que registra la referencia será identificado como actor de creación, mientras que `studentId` corresponderá al estudiante que recibirá la atención.

La referencia no otorgará acceso automático a futuras notas internas.

### 4.3. Detalle de StudentCase

**Solicitud:**

GET `/api/v1/cases/{caseId}`

**Respuesta propuesta:**

```json
{
  "data": {
    "id": "10000000-0000-4000-8000-000000000001",
    "status": "FOLLOW_UP",
    "category": "ACADEMIC_PLANNING",
    "version": 6,
    "assignedAdviser": {
      "displayName": "Adviser A"
    },
    "recommendations": [
      {
        "id": "30000000-0000-4000-8000-000000000001",
        "content": "Preparar una propuesta de carga para la próxima asesoría."
      }
    ],
    "pendingStudentFollowUps": 1
  }
}
```

`ACADEMIC_PLANNING` es una categoría ilustrativa.

La representación no incluirá notas internas ni la cantidad de actividades confidenciales.

Los contadores corresponderán únicamente a información visible para el estudiante.

### 4.4. Clasificación y asignación inicial

**Solicitud:**

POST `/api/v1/cases/{caseId}/assignment`

```json
{
  "expectedVersion": 2,
  "categoryId": "40000000-0000-4000-8000-000000000001",
  "programId": "50000000-0000-4000-8000-000000000001",
  "adviserId": "60000000-0000-4000-8000-000000000001"
}
```

El backend validará categoría, programa, adviser, versión y autorización.

La respuesta confirmada incluirá `ASSIGNED`, la información permitida del responsable y la nueva versión.

Los eventos `CASE_CLASSIFIED` y `CASE_ASSIGNED` deberán persistirse conjuntamente con la operación.

El cliente no podrá trasladar libremente el caso a otro programa mediante el campo `programId`.

### 4.5. Seguimiento

**Solicitud:**

POST `/api/v1/cases/{caseId}/follow-ups`

```json
{
  "expectedVersion": 4,
  "audience": "STUDENT",
  "description": "Preparar las opciones de asignaturas para revisión.",
  "dueAt": "2026-10-17T22:00:00Z"
}
```

La operación creará un FollowUp `PENDING` y devolverá el estado y la versión actualizados.

La audiencia `STUDENT` permitirá que el estudiante consulte la acción, pero no le concederá automáticamente permiso para completarla.

### 4.6. Resolución con pendientes

**Solicitud:**

POST `/api/v1/cases/{caseId}/resolution`

```json
{
  "expectedVersion": 6
}
```

**Respuesta propuesta — 409 Conflict:**

```json
{
  "error": {
    "code": "CASE_HAS_PENDING_ACTIONS",
    "message": "El caso conserva acciones pendientes.",
    "requestId": "req-synthetic-resolution-001"
  }
}
```

La resolución será rechazada si existe cualquier FollowUp `PENDING` o InformationRequest `OPEN`.

La verificación de autorización se ejecutará antes de revelar condiciones internas del caso.

### 4.7. Versión desactualizada

**Respuesta propuesta — 409 Conflict:**

```json
{
  "error": {
    "code": "VERSION_CONFLICT",
    "message": "La información del caso ha cambiado. Consulte su versión actual.",
    "requestId": "req-synthetic-version-001"
  }
}
```

Cuando se detecte un conflicto, el cliente deberá recuperar el estado y la versión actuales.

No repetirá automáticamente una operación de reasignación o resolución utilizando una versión sustituida.

## 5. Consultas operacionales, historial y salud

### 5.1. Consultas y filtros

GET `/api/v1/reports/operations` devolverá indicadores autorizados por estado, categoría, programa y adviser.

También permitirá identificar actividades vencidas conforme a la definición operativa propuesta.

El endpoint estará disponible únicamente para coordinación autorizada y no incluirá contenido de notas internas.

Los listados de casos admitirán:

| Parámetro | Uso | Restricción |
|---|---|---|
| `status` | Filtrar por uno o varios estados. | Validación contra catálogo. |
| `programId` | Filtrar por programa. | No amplía el ámbito autorizado. |
| `categoryId` | Filtrar por categoría. | UUID válido. |
| `adviserId` | Filtrar por responsable. | Aplicar autorización. |
| `followUpOverdue` | Identificar seguimientos vencidos. | Booleano; no representa overdue general del caso. |
| `createdFrom`, `createdTo` | Intervalo de creación. | Fechas válidas y orden coherente. |
| `page`, `pageSize` | Paginación. | Límites de la sección 2. |
| `sortBy`, `sortDirection` | Ordenamiento. | Campos permitidos, sin expresiones SQL arbitrarias. |

Un filtro no constituye un permiso.

Si el usuario solicita explícitamente información de un programa fuera de su alcance, se propone responder `403 OPERATION_FORBIDDEN`.

Cuando no se indique un programa específico, la consulta quedará limitada automáticamente al ámbito autorizado.

### 5.2. Respuesta paginada

```json
{
  "data": [
    {
      "id": "10000000-0000-4000-8000-000000000001",
      "status": "IN_PROGRESS",
      "assignedAdviser": {
        "displayName": "Adviser A"
      }
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "total": 1
  }
}
```

`total` corresponderá exclusivamente a los casos autorizados que cumplan los filtros.

Un actor con permisos limitados no recibirá el total institucional de casos.

### 5.3. Recuentos de reportes

El reporte operacional distinguirá:

- Casos abiertos.
- Casos resueltos.
- Casos pendientes de asignación.
- Carga de trabajo por Academic Adviser.
- Seguimientos vencidos.

Los casos `RESOLVED` no incrementarán la carga activa, aunque conserven una asignación histórica.

Las consultas agregadas deberán utilizar criterios temporales consistentes.

Si los indicadores se calculan mediante consultas independientes, sus resultados podrán variar por escrituras concurrentes. No se presentarán como una fotografía transaccional única si no lo son.

El escenario QS-04 y las pruebas de rendimiento permitirán evaluar las consultas relevantes.

### 5.4. Historial y visibilidad

GET `/api/v1/cases/{caseId}/history` devolverá los eventos del caso permitidos al usuario.

El historial interno utilizará `AuditEvent.caseSequence` para ordenar los eventos confirmados.

El atributo `version` representa modificaciones del caso y no sustituye la secuencia de auditoría.

La representación del estudiante podrá mostrar hitos generales, como creación, asignación, inicio de atención y resolución.

No incluirá motivos confidenciales de reasignación ni información que revele el contenido o la existencia de notas internas.

Los hitos públicos definitivos deberán validarse conforme a las políticas institucionales.

Los eventos de negocio se mantendrán diferenciados de los logs técnicos de diagnóstico.

### 5.5. Salud de la aplicación

Se proponen dos endpoints técnicos:

| Endpoint | Propósito |
|---|---|
| GET `/health/live` | Comprobar que el proceso responde. |
| GET `/health/ready` | Comprobar disponibilidad básica de la aplicación y conexión con PostgreSQL. |

Estas rutas se proponen fuera del prefijo funcional `/api/v1`.

La disponibilidad de `X-SIS` se comprobará separadamente y no deberá impedir que el sistema se considere preparado para operaciones independientes del simulador.

Las respuestas serán mínimas y podrán restringirse por red.

Un resultado satisfactorio de `/health/ready` no demuestra por sí solo que todos los procesos de negocio funcionan correctamente.

## 6. Integración simulada con X-SIS

### 6.1. Contrato externo

La integración con el SIS se realizará mediante CMP-10 — SIS Integration Adapter.

| Elemento | Propuesta |
|---|---|
| Operación | GET `/students/{syntheticStudentId}/academic-context` |
| Solicitante | CMP-10 — SIS Integration Adapter |
| Respuesta | `syntheticStudentId`, `program`, `academicStatus` |
| Datos | Exclusivamente sintéticos |
| Configuración | `SIS_MOCK_BASE_URL`, `SIS_REQUEST_TIMEOUT_MS` |
| Timeout inicial | 2.000 ms, sujeto a validación |
| Reintentos | Sin reintentos automáticos iniciales |

**Respuesta propuesta:**

```json
{
  "syntheticStudentId": "STU-001",
  "program": "Computer Science",
  "academicStatus": "ACTIVE"
}
```

El identificador `syntheticStudentId` pertenece al servicio simulado y no debe confundirse con el UUID interno `StudentProfile.id`.

El adaptador verificará que la respuesta corresponda al estudiante solicitado y transformará los datos al formato interno requerido.

Los campos de programa y estado académico deberán validarse antes de utilizarlos en una operación de negocio.

### 6.2. Fallos y límites

El adaptador comprobará la estructura, el identificador solicitado y los valores esperados.

Se contemplan los siguientes escenarios:

- Estudiante sintético no encontrado.
- Servicio temporalmente indisponible.
- Tiempo de espera agotado.
- Respuesta inválida o incompleta.
- Datos que no corresponden al estudiante solicitado.

Las consultas a `X-SIS` se realizarán fuera de las transacciones que mantengan bloqueadas filas de casos.

Las respuestas fallidas no se sustituirán por información inventada presentada como válida.

El mock no tendrá acceso directo a `C-DB`.

Una conexión futura con el SIS institucional requerirá autorización, revisión del contrato y evaluación de seguridad y tratamiento de datos.

## 7. Contratos internos del monolito modular

Los componentes de `C-API` colaborarán mediante contratos internos que separen las responsabilidades de autorización, dominio, persistencia e integración.

| Contrato propuesto | Responsabilidad |
|---|---|
| `AuthorizationPolicy.can(actor, operation, resourceContext)` | Evaluar permisos contextuales. |
| `CaseService.create / start / resolve` | Gestionar casos y transiciones. |
| `AssignmentService.classifyAndAssign / reassign` | Gestionar clasificación, asignaciones e historial. |
| `FollowUpService.create / complete` | Gestionar seguimientos y pendientes. |
| `NoteService.createInternal / listAuthorized` | Proteger notas según su audiencia. |
| `AuditService.append(event, transaction)` | Registrar eventos dentro de la transacción existente. |
| `UnitOfWork.execute(operation)` | Coordinar transacciones y rollback. |
| `SISAdapter.getAcademicContext(studentId)` | Consultar el SIS simulado mediante una interfaz interna. |

Los nombres son orientativos y podrán ajustarse a la estructura final del código.

Los controladores HTTP no implementarán reglas complejas del dominio ni ejecutarán directamente consultas SQL.

Los componentes utilizarán interfaces internas definidas, manteniendo los límites del monolito modular establecidos en ADR-001.

El contexto de transacción de `UnitOfWork` deberá ser compartido por los componentes que participen en una misma operación.

Esto permitirá confirmar conjuntamente los cambios del caso, las entidades relacionadas y los eventos obligatorios de auditoría.

Las operaciones sobre un caso seguirán el protocolo de bloqueo, versión y validación definido en [Domain and Data Model](domain-and-data.md) y [Lifecycle and Runtime](lifecycle-and-runtime.md).

Las consultas a `X-SIS` deberán ejecutarse fuera de transacciones que mantengan bloqueos prolongados.

## 8. Comportamiento de C-WEB y evolución del contrato

### 8.1. Comportamiento de la interfaz

| Respuesta | Comportamiento esperado |
|---|---|
| 201 | Mostrar referencia y estado inicial del recurso creado. |
| 200 | Actualizar la interfaz utilizando el estado y la versión confirmados. |
| 400 | Señalar los campos inválidos. |
| 401 | Solicitar autenticación nuevamente. |
| 403 / 404 | Informar que la operación o recurso no está disponible. |
| 409 | Recuperar el contexto actual antes de repetir una decisión. |
| 429 | Respetar el tiempo de espera establecido. |
| 502 / 503 / 504 | Informar indisponibilidad y conservar entradas cuando sea seguro. |

La interfaz no deberá presentar una transición como finalizada antes de recibir una confirmación válida de persistencia.

Podrán utilizarse controles para evitar dobles clics, pero esas medidas no sustituirán la validación del backend.

Ante un conflicto `409`, `C-WEB` deberá recuperar el estado y la versión actuales antes de permitir una nueva decisión.

### 8.2. Disponibilidad, reintentos e incertidumbre

Las consultas de lectura podrán repetirse de forma controlada ante fallos transitorios.

Las escrituras no tendrán una política general de reintentos automáticos en el contrato inicial.

Si el cliente experimenta un timeout después de enviar una operación, consultará el estado y el historial autorizado para determinar si el cambio fue confirmado.

Cuando no sea posible establecer el resultado, se informará la incertidumbre en lugar de afirmar éxito o fracaso.

Una futura estrategia con claves de idempotencia deberá definir alcance, almacenamiento de resultados y expiración.

### 8.3. Observabilidad

`requestId` permitirá relacionar una respuesta con los registros técnicos correspondientes.

No será un token de autorización ni permitirá acceder a información interna.

Los logs podrán registrar:

- Método HTTP.
- Plantilla de ruta.
- Código de respuesta.
- Duración de la operación.
- Identificador técnico de correlación.

No se registrarán indiscriminadamente contraseñas, cookies, identificadores sensibles ni contenido confidencial de notas.

Las métricas del servidor y la latencia observada por el cliente deberán diferenciarse durante las pruebas de rendimiento.

### 8.4. Versionado y compatibilidad

El prefijo `/api/v1` representa la primera versión del contrato funcional propuesto.

Los cambios que eliminen campos, modifiquen permisos o alteren precondiciones requerirán una revisión coordinada de `C-WEB`, `C-API` y las pruebas relacionadas.

La incorporación de nuevos campos también requerirá revisar las políticas de confidencialidad.

Un campo no será considerado seguro únicamente porque una versión anterior del cliente lo ignore.

La adopción futura de OpenAPI u otra herramienta formal podrá facilitar la generación y validación de esquemas. El documento Markdown constituye la especificación de referencia actual.

## 9. Trazabilidad, aceptación y decisiones pendientes

### 9.1. Trazabilidad y validación

| Grupo de interfaces | Referencias | Pruebas relacionadas |
|---|---|---|
| Sesión y acceso autorizado | FR-12, FR-14, QR-01, QR-02, BR-07, BR-08, AD-02, ADR-004, QS-01 | T-AUTH-01, T-SEC-01, T-API-01 |
| Creación, referencia y atención | FR-01, FR-02, FR-06, FR-07, FR-08, AD-01, AD-04 | T-FUNC-01, T-API-01 |
| Clasificación y asignación | FR-03, FR-04, BR-01, BR-02, AD-01, ADR-003 | T-TRIAGE-01 |
| Resolución y concurrencia | FR-11, BR-05, AD-01, AD-04, QS-02 | T-LIFE-01, T-LIFE-02 |
| Reasignación e historial | FR-05, FR-09, FR-10, BR-06, AD-03, ADR-005, QS-03 | T-AUD-01, T-TXN-01 |
| Consultas y reportes | FR-13, QR-05, AD-06, QS-04, QS-06 | T-PERF-01, T-UX-01 |
| SIS simulado y recuperación | C-05, C-06, QR-06, AD-07, QS-07 | T-SIS-01, T-REC-01 |

Los identificadores de pruebas deberán mantenerse consistentes con el catálogo de [Validation Plan](../validation/test-plan.md).

La validación comprobará entradas, salidas, autorización, estados, auditoría, manejo de errores e integración simulada.

### 9.2. Criterios de aceptación

Los contratos deberán satisfacer los siguientes criterios:

1. Cada operación tendrá entradas, salidas, permisos y estados compatibles claramente definidos.
2. Los códigos de error distinguirán autenticación, autorización, validación y conflictos.
3. Las respuestas destinadas al Student no contendrán información interna.
4. Las escrituras no aceptarán identidad, roles ni estados arbitrarios desde el cliente.
5. Los recursos anidados pertenecerán al caso autorizado.
6. Las operaciones protegidas mediante cookies aplicarán el mecanismo CSRF.
7. El simulador respetará el contrato sintético y sus errores se manejarán de forma controlada.
8. El historial utilizará `caseSequence` para ordenar los eventos de cada caso.
9. Las operaciones transaccionales confirmarán conjuntamente los cambios y sus eventos obligatorios de auditoría.
10. La implementación mantendrá trazabilidad entre los contratos, requisitos y pruebas correspondientes.

Estos criterios deberán comprobarse mediante solicitudes directas a la API y mediante los flujos de `C-WEB`.

### 9.3. Decisiones pendientes

Las interfaces propuestas permiten definir las interacciones principales sin incorporar funcionalidades ajenas al alcance actual.

Antes de su implementación definitiva deberán confirmarse:

- El mecanismo institucional de delegación y revocación de permisos de triage.
- Las políticas definitivas de sesión, expiración, renovación y CSRF.
- Los límites de campos, cuerpos y paginación.
- Las reglas para corregir respuestas o cancelar seguimientos.
- Los criterios institucionales sobre suficiencia de recomendaciones.
- La distinción futura entre seguimientos obligatorios y opcionales.
- El contrato definitivo del simulador X-SIS.
- La necesidad de claves de idempotencia para escrituras.
- La posible adopción de OpenAPI.
- Las reglas de evolución y compatibilidad de versiones de API.

Las decisiones que afecten seguridad, estados, integridad o información compartida deberán revisarse conjuntamente con los ADR, las vistas arquitectónicas y el plan de pruebas.

**Documentos relacionados:**

- [Domain and Data Model](domain-and-data.md)
- [Lifecycle and Runtime](lifecycle-and-runtime.md)
- [Security Design](security.md)
- [Component View](../views/component-view.md)
- [Validation Plan](../validation/test-plan.md)
- [Architecture Decision Records](../adr/README.md)

Este documento establece los contratos, los comportamientos esperados y los criterios verificables para las interfaces principales del Academic Advising Case Management System, manteniendo coherencia con la arquitectura de monolito modular y las decisiones de seguridad, concurrencia y auditoría.
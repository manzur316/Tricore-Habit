# PROVIDER_RESEARCH_HIKVISION_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** CERTIFIED como documento base de investigación Hikvision ISAPI Person-Based Access Control  
**Estado de implementación:** BLOCKED hasta laboratorio con hardware/dispositivo real  
**Fecha:** 2026-06-10  
**Archivo autoritativo:** `PROVIDER_RESEARCH_HIKVISION_V0.1.md`  
**Fuente técnica primaria:** `Intelligent Security API (Person-Based Access Control) Part 1.pdf`  
**Fuente técnica secundaria degradada:** `ISAPI HIKVISION.pdf` — metadata/video; no autoritativa para control de acceso  
**Documentos padre:**
- `PROJECT_CHARTER_TRICOR_HABITAT.md`
- `DOMAIN_MODEL_V0.1.md`
- `ACCESS_POLICY_CONFIGURATION_V0.1.md`
- `CLOUD_EDGE_ARCHITECTURE_V0.1.md`

**Documentos dependientes, NO actualizados por este archivo:**
- `PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md`
- `QA_HARNESS_SPEC_V0.1.md`
- `ROADMAP_V0.1.md`
- `AI_AGENT_RULES_AND_HANDOFF.md`

---

## 0. Control de auditoría

### 0.1 Veredicto de la fuente

```text
Fuente: Intelligent Security API (Person-Based Access Control) Part 1.pdf
Veredicto: MATCH
Motivo: contiene endpoints reales de control de acceso person-based para personas, tarjetas, permisos, horarios, puertas, apertura remota, eventos, QR events, remote check y capacidades.
```

### 0.2 Veredicto del documento

```text
Documento: PROVIDER_RESEARCH_HIKVISION_V0.1.md
Estado: CERTIFIED como research base
Uso permitido: diseño de adapter, mapping canónico y planificación de laboratorio
Uso no permitido: implementación productiva sin lab test
```

### 0.3 Cambio aplicado en esta revisión

Este documento fue reescrito para enfocarse en **Hikvision ISAPI Person-Based Access Control**. La investigación genérica anterior sobre HikCentral, Hik-Connect, Device Gateway e integraciones amplias queda degradada a ruta alternativa/futura. El foco autoritativo para este archivo pasa a ser el PDF de control de acceso basado en persona.

### 0.4 Archivos no tocados

Esta actualización modifica exclusivamente:

```text
PROVIDER_RESEARCH_HIKVISION_V0.1.md
```

No se actualizaron:

```text
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
QA_HARNESS_SPEC_V0.1.md
ROADMAP_V0.1.md
AI_AGENT_RULES_AND_HANDOFF.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
```

---

## 1. Propósito

Este documento define la investigación técnica base para integrar **Hikvision** como proveedor futuro de control de acceso en Tricor Hábitat usando **ISAPI Person-Based Access Control**.

El objetivo es extraer solo lo necesario para Tricor:

- Endpoints reales de control de acceso.
- Capacidades necesarias para validar el proveedor.
- Mapeo canónico Tricor → Hikvision.
- Límites y riesgos.
- Qué entra a v1 futura y qué queda fuera.
- Qué debe probarse en laboratorio antes de implementación.

Regla central:

```text
Tricor conserva su dominio canónico.
Hikvision se integra mediante un adapter.
El adapter Hikvision usa endpoints reales ISAPI.
El dominio Tricor no debe adoptar nombres internos de Hikvision.
```

---

## 2. Contexto correcto de Hikvision para Tricor

### 2.1 Documento correcto

El documento correcto para este research es:

```text
Intelligent Security API (Person-Based Access Control) Developer Guide
```

Este documento cubre:

- ISAPI description.
- HTTP/REST-style operations.
- Authentication.
- Person information.
- Card information.
- Fingerprint information.
- Face information.
- Access permission control schedule.
- Door control schedule.
- Remote door, elevator and buzzer control.
- Access control events.
- QR code events.
- Remote verification.
- Capabilities and response codes.

### 2.2 Documento incorrecto o parcial anterior

El PDF anterior:

```text
ISAPI HIKVISION.pdf
```

queda clasificado como:

```text
PARTIAL / NOT AUTHORITATIVE FOR ACCESS CONTROL
```

Razón: documenta metadata de video, RTSP, ANPR, face capture, behavior analysis y metadata capabilities. Puede servir para investigación futura de video/ANPR, pero no debe usarse como base para el adapter de control de acceso.

---

## 3. Arquitectura recomendada para Hikvision

### 3.1 Ruta técnica recomendada

Para Tricor Hábitat, la ruta base de Hikvision debe ser:

```text
Tricor Cloud
→ Tricor Edge
→ Hikvision ISAPI Person-Based Access Control
→ dispositivo/controlador Hikvision
```

### 3.2 Cloud directo

No se aprueba Cloud directo todavía.

Estado:

```text
Cloud direct: LAB_REQUIRED / NOT CONFIRMED
```

Razón: el PDF documenta endpoints ISAPI sobre dispositivos/controladores. Para uso residencial real, lo más seguro es tratarlo igual que otros proveedores locales: el Edge ejecuta comandos contra el dispositivo/controlador en sitio.

### 3.3 Por qué Edge

El Edge permite:

- Acceso local a IP/puerto del dispositivo.
- Manejo de autenticación Basic/Digest.
- Reintentos.
- Cola de comandos.
- Observed state.
- Diagnóstico local.
- Aislamiento de secretos.
- Evitar exponer dispositivos Hikvision directamente a internet.

---

## 4. Modelo ISAPI relevante

### 4.1 Estilo de API

El PDF define ISAPI con estilo RESTful sobre HTTP/RTSP. Para Tricor se usará solo la parte HTTP/HTTPS de control de acceso.

Métodos relevantes:

```text
GET     leer capacidades/estado/datos
POST    crear/buscar recursos o ejecutar búsquedas
PUT     actualizar, configurar, setear o ejecutar control remoto
DELETE  eliminar cuando el endpoint lo soporte explícitamente
```

### 4.2 Formato de URL

Formato base:

```text
<protocol>://<host>[:port]/ISAPI/<Resource>?format=json
```

Ejemplo:

```text
http://192.168.1.50/ISAPI/AccessControl/UserInfo/Search?format=json
```

### 4.3 Formato de mensajes

Para Tricor, usar JSON cuando el endpoint lo permita:

```http
Content-Type: application/json
```

La convención del documento:

```text
Nodos no-leaf: UpperCamelCase
Nodos leaf: lowerCamelCase
```

### 4.4 Autenticación

El PDF indica que la comunicación ISAPI requiere autenticación HTTP. Debe soportarse:

```text
Basic Authentication
Digest Access Authentication
```

Regla Tricor:

```text
El adapter Hikvision debe implementar Digest como ruta primaria.
Basic solo puede aceptarse si el dispositivo/laboratorio lo requiere y el canal está protegido.
Credenciales y secretos viven en Edge secret storage, no en el dominio Tricor.
```

---

## 5. Capacidades principales a consultar

Antes de activar Hikvision, el adapter debe consultar capacidades.

### 5.1 Access Control capabilities

```http
GET /ISAPI/AccessControl/capabilities
```

Uso Tricor:

- Detectar soporte general de Access Control.
- Detectar soporte de remote door control.
- Detectar soporte de remote check.
- Detectar soporte de QR events.
- Detectar capacidades de configuración del dispositivo.

Estado:

```text
CORE_V1_RESEARCH
LAB_REQUIRED
```

### 5.2 Access Controller configuration

```http
GET /ISAPI/AccessControl/AcsCfg/capabilities?format=json
GET /ISAPI/AccessControl/AcsCfg?format=json
PUT /ISAPI/AccessControl/AcsCfg?format=json
```

Uso Tricor:

- Validar si QR function está habilitada o disponible.
- Validar si remoteCheckDoorEnabled existe.
- Validar channel/check configuration cuando aplique.

Estado:

```text
CONDITIONAL
LAB_REQUIRED
```

No se debe modificar `AcsCfg` en producción sin laboratorio, porque puede afectar configuración física del dispositivo.

---

## 6. Endpoints core para personas

### 6.1 Capacidad de gestión de personas

```http
GET /ISAPI/AccessControl/UserInfo/capabilities?format=json
```

Request:

```text
None
```

Response:

```text
JSON_Cap_UserInfo o JSON_ResponseStatus
```

Uso Tricor:

- Confirmar funciones soportadas: add, delete, edit, search, setUp.
- Confirmar límites de `employeeNo`, `name`, `userType`, `Valid`, `doorRight`, `RightPlan`.
- Confirmar si el dispositivo soporta búsqueda por `employeeNo`.

Relación canónica:

```text
ProviderCapabilityMatrix.personManagement
ProviderCapabilityMatrix.accessProfileByDoorRight
ProviderCapabilityMatrix.validityWindow
```

QA requerido:

```text
qa:provider:hikvision:capabilities:user-info
```

---

### 6.2 Contar personas

```http
GET /ISAPI/AccessControl/UserInfo/Count?format=json
```

Uso Tricor:

- Diagnóstico.
- Reconciliación.
- Comparar personas canónicas esperadas vs personas en dispositivo.

Estado:

```text
CORE_DIAGNOSTIC
```

---

### 6.3 Buscar personas

```http
POST /ISAPI/AccessControl/UserInfo/Search?format=json
```

Request:

```text
JSON_UserInfoSearchCond
```

Response:

```text
JSON_UserInfoSearch o JSON_ResponseStatus
```

Uso Tricor:

- Verificar si `employeeNo` existe.
- Obtener estado observado de persona.
- Detectar drift.
- Reconciliar `doorRight`, `RightPlan`, `Valid`, tarjetas y datos base.

Relación canónica:

```text
ObservedAccessState
ProviderExternalRef
ProviderDriftDetection
```

QA requerido:

```text
qa:provider:hikvision:user-search
```

---

### 6.4 Agregar persona

```http
POST /ISAPI/AccessControl/UserInfo/Record?format=json
```

Request:

```text
JSON_UserInfo
```

Response:

```text
JSON_ResponseStatus
```

Uso Tricor:

- Crear identidad externa Hikvision para una propiedad/responsable.
- Asignar `employeeNo` controlado por Tricor.
- Configurar `userType`, `Valid`, `doorRight` y `RightPlan` si el dispositivo lo soporta.

Relación canónica:

```text
AccessIdentity → UserInfo.employeeNo
AccessProfile → UserInfo.doorRight + UserInfo.RightPlan
```

Estado:

```text
CORE_V1_RESEARCH
LAB_REQUIRED
```

---

### 6.5 Set person information

```http
PUT /ISAPI/AccessControl/UserInfo/SetUp?format=json
```

Request:

```text
JSON_UserInfo
```

Response:

```text
JSON_ResponseStatus
```

Comportamiento documentado:

```text
Si la persona no existe según employeeNo, se agrega.
Si la persona ya existe según employeeNo, se edita.
Si deleteUser=true, se elimina información de persona, pero no tarjetas/huellas/rostros vinculados.
```

Uso Tricor:

- Operación preferente para upsert de persona si capability `setUp` está disponible.
- Aplicar cambios de perfil activo/moroso/restaurado modificando `doorRight`, `RightPlan` o `Valid`.

Relación canónica:

```text
AccessCommand.APPLY_ACCESS_PROFILE
AccessCommand.RESTORE_ACCESS
AccessCommand.RESTRICT_ACCESS
```

Riesgo:

```text
deleteUser=true no elimina tarjetas/biometría/permisos vinculados. No usar para exresidentes si se requiere limpieza completa.
```

QA requerido:

```text
qa:provider:hikvision:user-setup-upsert
qa:provider:hikvision:user-setup-morosity-profile
```

---

### 6.6 Modificar persona

```http
PUT /ISAPI/AccessControl/UserInfo/Modify?format=json
```

Uso Tricor:

- Modificar datos existentes.
- Alternativa cuando `SetUp` no esté disponible.

Limitación:

```text
Debe validarse qué campos permite editar cada dispositivo.
```

---

### 6.7 Eliminar solo persona

```http
PUT /ISAPI/AccessControl/UserInfo/Delete?format=json
```

Request:

```text
JSON_UserInfoDelCond
```

Response:

```text
JSON_ResponseStatus
```

Uso Tricor:

- No recomendado como flujo principal de exresidente.

Riesgo:

```text
Borra información de persona únicamente.
No garantiza eliminación de tarjetas, huellas, rostros y permisos vinculados.
```

Estado:

```text
CONDITIONAL / DANGEROUS_WITHOUT_LAB
```

---

### 6.8 Eliminar persona con tarjetas, biometría y permisos

```http
GET /ISAPI/AccessControl/UserInfoDetail/Delete/capabilities?format=json
PUT /ISAPI/AccessControl/UserInfoDetail/Delete?format=json
GET /ISAPI/AccessControl/UserInfoDetail/DeleteProcess?format=json
```

Request principal:

```text
JSON_UserInfoDetail
```

Comportamiento documentado:

```text
UserInfoDetail/Delete inicia el borrado de persona, tarjetas, huellas, rostros y permisos por employeeNo.
DeleteProcess consulta el estado del proceso.
```

Uso Tricor:

- Flujo de exresidente / mudanza / depuración profunda.
- Limpieza de credenciales obsoletas.

Riesgo:

```text
Operación destructiva.
Debe probarse primero en laboratorio.
No debe ejecutarse automáticamente en producción sin política explícita.
```

Estado:

```text
LAB_REQUIRED
FUTURE_CORE_AFTER_LAB
```

Estrategia inicial recomendada:

```text
Para morosidad: NO borrar persona.
Para tarjeta perdida: revocar tarjeta específica.
Para exresidente: preferir proceso controlado; elegir entre Valid.endTime vencido, doorRight vacío o UserInfoDetail/Delete según laboratorio.
```

---

## 7. Endpoints core para tarjetas/credenciales

### 7.1 Capacidad de tarjetas

```http
GET /ISAPI/AccessControl/CardInfo/capabilities?format=json
```

Response:

```text
JSON_Cap_CardInfo o JSON_ResponseStatus
```

Uso Tricor:

- Confirmar soporte de `setUp`, `addCard`, `checkEmployeeNo`, tipos de tarjeta y límites.
- Confirmar si la tarjeta puede ligarse por `employeeNo`.

QA requerido:

```text
qa:provider:hikvision:capabilities:card-info
```

---

### 7.2 Contar tarjetas

```http
GET /ISAPI/AccessControl/CardInfo/Count?format=json
GET /ISAPI/AccessControl/CardInfo/Count?format=json&employeeNo=<ID>
```

Uso Tricor:

- Validar límite de credenciales por propiedad/responsable.
- Reconciliación.
- Detección de tarjetas externas no canónicas.

---

### 7.3 Buscar tarjetas

```http
POST /ISAPI/AccessControl/CardInfo/Search?format=json
```

Request:

```text
JSON_CardInfoSearchCond
```

Response:

```text
JSON_CardInfoSearch
```

Uso Tricor:

- Verificar si una tarjeta existe.
- Obtener tarjetas vinculadas a `employeeNo`.
- Detectar tarjetas activas de exresidentes.
- Detectar drift contra Tricor.

---

### 7.4 Agregar tarjetas

```http
POST /ISAPI/AccessControl/CardInfo/Record?format=json
```

Request:

```text
JSON_CardInfo
```

Response:

```text
JSON_ResponseStatus
```

Uso Tricor:

- Registrar tarjeta/tag a nombre de la identidad externa de la propiedad/responsable.

Relación canónica:

```text
Credential.cardNo → Hikvision CardInfo.cardNo
AccessIdentity.externalId → Hikvision CardInfo.employeeNo
```

---

### 7.5 Set card information

```http
PUT /ISAPI/AccessControl/CardInfo/SetUp?format=json
```

Request:

```text
JSON_CardInfo
```

Response:

```text
JSON_ResponseStatus
```

Comportamiento documentado:

```text
Si la tarjeta no existe según cardNo, se agrega.
Si la tarjeta existe según cardNo, se edita.
Si se quiere eliminar una tarjeta específica, se configura employeeNo + cardNo + deleteCard=true.
Si se quiere eliminar todas las tarjetas de una persona, se configura employeeNo + deleteCard=true.
```

Uso Tricor:

- Operación preferente para upsert/revocación de tarjeta si está soportada.
- Flujo de tarjeta perdida.
- Flujo de exresidente si se opta por revocar tarjetas sin borrar persona.

Riesgo:

```text
employeeNo y cardNo no son editables en CardInfo.Modify.
Para cambiar cardNo se debe eliminar la tarjeta previa y crear una nueva.
```

QA requerido:

```text
qa:provider:hikvision:card-setup-add
qa:provider:hikvision:card-setup-delete-one
qa:provider:hikvision:card-setup-delete-all-for-person
```

---

### 7.6 Modificar tarjeta

```http
PUT /ISAPI/AccessControl/CardInfo/Modify?format=json
```

Uso Tricor:

- Edición de atributos no clave.

Limitación:

```text
No permite editar employeeNo ni cardNo.
```

---

### 7.7 Eliminar tarjetas

```http
PUT /ISAPI/AccessControl/CardInfo/Delete?format=json
```

Request:

```text
JSON_CardInfoDelCond
```

Uso Tricor:

- Alternativa explícita para eliminación de tarjetas.

Decisión:

```text
Usar CardInfo/SetUp con deleteCard=true o CardInfo/Delete dependerá de laboratorio.
```

---

## 8. Modelo de persona y credencial para Tricor

### 8.1 Modelo canónico Tricor

Tricor no hará censo de habitantes en v1. El modelo confirmado es:

```text
Propiedad
→ propietario/responsable principal
→ límite de credenciales autorizadas
```

### 8.2 Mapeo a Hikvision

Hikvision es person-based, por lo que Tricor debe crear una identidad externa por responsable/propiedad:

```text
Tricor AccessIdentity.externalRef
→ Hikvision UserInfo.employeeNo
```

Las tarjetas/tags se asignan a ese `employeeNo`:

```text
Tricor Credential
→ Hikvision CardInfo.cardNo + employeeNo
```

### 8.3 Múltiples credenciales

Si una propiedad tiene tres tarjetas autorizadas:

```text
Propiedad A-101
Responsable: Juan Pérez
employeeNo: TH-PROP-A101
Tarjetas:
- cardNo 001
- cardNo 002
- cardNo 003
```

No se requiere registrar cada familiar como persona distinta en Hikvision.

### 8.4 Riesgo

Algunos dispositivos/proyectos podrían requerir persona individual por credencial para biometría/rostro. Eso queda fuera de v1 y se evaluará en laboratorio.

---

## 9. Access Profile y permisos

### 9.1 Campo principal: doorRight

El PDF define `doorRight` como:

```text
No. of door or lock that has access permission
```

En Tricor, `doorRight` debe representar las puertas asignadas a un perfil.

Ejemplo conceptual:

```text
ACTIVE_ACCESS_PROFILE
→ doorRight: "1,2,3,4"

MOROSO_DEFAULT_PROFILE
→ doorRight: "1"

REVOKED_PROFILE
→ doorRight: "" o Valid.endTime vencido, según laboratorio
```

### 9.2 Campo principal: RightPlan

`RightPlan` define el horario/plan de permiso por puerta.

Uso Tricor:

```text
AccessProfile
→ doorRight + RightPlan
```

### 9.3 Plantillas de horario de permisos

Endpoints relevantes:

```http
GET /ISAPI/AccessControl/UserRightPlanTemplate/<TemplateNo>?format=json
PUT /ISAPI/AccessControl/UserRightPlanTemplate/<TemplateNo>?format=json
GET /ISAPI/AccessControl/UserRightPlanTemplate/capabilities?format=json
```

Endpoints adicionales de calendario:

```http
GET/PUT /ISAPI/AccessControl/UserRightWeekPlanCfg/<PlanNo>?format=json
GET /ISAPI/AccessControl/UserRightWeekPlanCfg/capabilities?format=json
GET/PUT /ISAPI/AccessControl/UserRightHolidayGroupCfg/<GroupNo>?format=json
GET /ISAPI/AccessControl/UserRightHolidayGroupCfg/capabilities?format=json
GET/PUT /ISAPI/AccessControl/UserRightHolidayPlanCfg/<PlanNo>?format=json
GET /ISAPI/AccessControl/UserRightHolidayPlanCfg/capabilities?format=json
```

Estado:

```text
CONDITIONAL
LAB_REQUIRED
```

### 9.4 Estrategia inicial

Para v1, evitar configuración compleja de horarios si no es necesaria.

Recomendación:

```text
Usar plantillas existentes del dispositivo/controlador cuando sea posible.
Mapear perfiles Tricor a doorRight + RightPlan previamente configurados.
No crear cientos de planes dinámicos.
```

---

## 10. Morosidad y restauración de acceso

### 10.1 Propiedad al corriente

```text
Tricor estado: ACTIVE
Hikvision: UserInfo.doorRight = puertas permitidas del perfil activo
Hikvision: RightPlan = horarios/planes activos
```

### 10.2 Propiedad morosa default

Default confirmado de Tricor:

```text
Peatonal permitido
Vehicular bloqueado
QR invitados bloqueado
Amenidades futuras bloqueadas
```

Mapping Hikvision:

```text
MOROSO_DEFAULT_PROFILE
→ doorRight = puertas peatonales únicamente
→ RightPlan = plan activo para esas puertas
```

### 10.3 Restauración por pago confirmado

```text
Payment confirmed
→ Policy Engine recalcula ACTIVE_ACCESS_PROFILE
→ AccessCommand.RESTORE_ACCESS
→ Edge Hikvision adapter
→ PUT /ISAPI/AccessControl/UserInfo/SetUp?format=json
→ UserInfo.doorRight + RightPlan restaurados
→ Observed state por UserInfo/Search o AcsEvent
```

### 10.4 No borrar persona por morosidad

Regla dura:

```text
La morosidad no elimina persona, tarjeta ni biometría.
Solo cambia permisos/capacidades.
```

---

## 11. Puertas, apertura remota y Guard

### 11.1 Remote door control capabilities

```http
GET /ISAPI/AccessControl/RemoteControl/door/capabilities
```

Uso Tricor:

- Confirmar si el dispositivo permite apertura remota.
- Confirmar comandos disponibles.
- Confirmar si requiere password.

### 11.2 Remote door control

```http
PUT /ISAPI/AccessControl/RemoteControl/door/<ID>
```

Request:

```text
XML_RemoteControlDoor
```

Uso Tricor:

- Apertura manual de GuardSupervisor.
- Apertura por QR válido si el flujo queda confirmado.
- Diagnóstico de caseta.

Limitación:

```text
<ID> es el número de puerta.
65535 puede referir a todas las puertas, pero Tricor no debe usarlo en v1 salvo laboratorio controlado.
```

### 11.3 Comandos de puerta

El documento indica que para puertas puede controlarse:

```text
Remain Open
Remain Closed
Normal
```

Para Tricor, la operación común debe ser apertura momentánea o comando equivalente si el dispositivo lo soporta. Esto requiere laboratorio porque el XML exacto y el comportamiento físico dependen del dispositivo.

### 11.4 Remote control password

Endpoints relacionados:

```http
GET /ISAPI/AccessControl/remoteControlPWCheck/capabilities?format=json
PUT /ISAPI/AccessControl/remoteControlPWCheck/door/<ID>?format=json
GET /ISAPI/AccessControl/remoteControlPWCfg/capabilities?format=json
PUT /ISAPI/AccessControl/remoteControlPWCfg/door/<ID>?format=json
```

Estado:

```text
CONDITIONAL
LAB_REQUIRED
```

---

## 12. Eventos de acceso y observed state

### 12.1 Access event capabilities

```http
GET /ISAPI/AccessControl/AcsEvent/capabilities?format=json
```

Uso Tricor:

- Confirmar filtros disponibles.
- Confirmar campos de evento.
- Confirmar si permite búsqueda por employeeNo, cardNo, time range, event type.

### 12.2 Search access control events

```http
POST /ISAPI/AccessControl/AcsEvent?format=json
```

Request:

```text
JSON_AcsEventCond
```

Response:

```text
JSON_AcsEvent o JSON_ResponseStatus
```

Uso Tricor:

- ObservedAccessEvent.
- Auditoría de accesos.
- Validación de QR/tarjeta/fallo.
- Reconciliación.
- Diagnóstico de puertas.

### 12.3 Event total number

```http
GET /ISAPI/AccessControl/AcsEventTotalNum/capabilities?format=json
POST /ISAPI/AccessControl/AcsEventTotalNum?format=json
```

Uso Tricor:

- Paginación/diagnóstico.
- Validar que no se están perdiendo eventos.

### 12.4 Event notification / push

Endpoints relacionados:

```http
GET /ISAPI/Event/notification/httpHosts/capabilities
GET/PUT /ISAPI/Event/notification/httpHosts
POST /ISAPI/Event/notification/httpHosts/<ID>/test
GET /ISAPI/Event/notification/alertStream
```

Estado:

```text
FUTURE / LAB_REQUIRED
```

Tricor v1 puede iniciar con polling de eventos mediante `AcsEvent`. Push/listening mode queda para fase posterior si laboratorio lo valida.

---

## 13. QR events y QR en Tricor

### 13.1 QR code events

```http
GET /ISAPI/AccessControl/QRCodeEvent/capabilities?format=json
POST /ISAPI/AccessControl/QRCodeEvent?format=json
```

Uso Tricor:

- Obtener eventos de escaneo QR.
- Detectar `QRCodeInfo`.
- Determinar si el dispositivo pide `remoteCheck`.

### 13.2 Alcance real

Este PDF documenta **eventos de escaneo QR**, no necesariamente generación de QR de Tricor.

Regla:

```text
Tricor genera y firma sus propios QR.
Hikvision puede leer/reportar QR si el dispositivo lo soporta.
El adapter debe validar cómo llega QRCodeInfo antes de diseñar el flujo final.
```

### 13.3 Remote verification

```http
GET /ISAPI/AccessControl/remoteCheck/capabilities?format=json
PUT /ISAPI/AccessControl/remoteCheck?format=json
```

Request:

```text
JSON_RemoteCheck
```

Uso Tricor:

- Validar remotamente un evento de acceso.
- Responder success/failed a un `serialNo` de evento.
- Posible puente para QR de Tricor si el dispositivo soporta remote verification.

Estado:

```text
HIGH_VALUE
LAB_REQUIRED
```

Riesgo:

```text
Si remoteCheck no está soportado o no funciona como se espera, Tricor Guard deberá operar el QR desde la PWA/Edge y abrir puerta con RemoteControl/door si procede.
```

---

## 14. Fingerprint, face and biometrics

### 14.1 Fingerprint endpoints

Endpoints relevantes:

```http
GET /ISAPI/AccessControl/FingerPrintCfg/capabilities?format=json
POST /ISAPI/AccessControl/FingerPrint/SetUp?format=json
PUT /ISAPI/AccessControl/FingerPrint/Delete?format=json
GET /ISAPI/AccessControl/FingerPrint/DeleteProcess?format=json
GET /ISAPI/AccessControl/FingerPrint/Count?format=json&employeeNo=
```

Estado:

```text
FUTURE
CONDITIONAL
LAB_REQUIRED
```

No entra al core v1 de Tricor salvo que el cliente/proyecto lo requiera explícitamente.

### 14.2 Face library endpoints

Endpoints relevantes:

```http
GET /ISAPI/Intelligent/FDLib/capabilities?format=json
POST /ISAPI/Intelligent/FDLib/FaceDataRecord?format=json
PUT /ISAPI/Intelligent/FDLib/FDSetUp?format=json
POST /ISAPI/Intelligent/FDLib/FDSearch?format=json
PUT /ISAPI/Intelligent/FDLib/FDModify?format=json
```

Estado:

```text
FUTURE
OUT_OF_SCOPE_V1
```

Regla:

```text
No meter rostro/biometría al MVP base.
Sí dejar capability flags para futuro.
```

---

## 15. Door/reader/device configuration

Endpoints útiles para diagnóstico o configuración técnica:

```http
GET /ISAPI/AccessControl/AcsWorkStatus/capabilities?format=json
GET /ISAPI/AccessControl/AcsWorkStatus?format=json
GET /ISAPI/AccessControl/Door/param/<ID>
GET /ISAPI/AccessControl/Door/param/<ID>/capabilities
GET /ISAPI/AccessControl/CardReaderCfg/<ID>?format=json
GET /ISAPI/AccessControl/CardReaderCfg/capabilities?format=json
```

Uso Tricor:

- Diagnóstico técnico.
- Mapeo de puertas/lectores.
- Estado observado del controlador.

Estado:

```text
DIAGNOSTIC
LAB_REQUIRED
```

No deben convertirse en paneles de configuración avanzada para el cliente final en v1.

---

## 16. ProviderCapabilityMatrix propuesta

```yaml
hikvision_isapi_person_based_access_control:
  source: Intelligent Security API (Person-Based Access Control)
  status: RESEARCH_CERTIFIED_LAB_REQUIRED
  connection:
    local_edge_required: true
    http: true
    https: device_dependent
    auth_basic: supported_by_pdf
    auth_digest: required_primary
  capabilities:
    person_management:
      status: core
      endpoints:
        - GET /ISAPI/AccessControl/UserInfo/capabilities?format=json
        - POST /ISAPI/AccessControl/UserInfo/Search?format=json
        - POST /ISAPI/AccessControl/UserInfo/Record?format=json
        - PUT /ISAPI/AccessControl/UserInfo/SetUp?format=json
        - PUT /ISAPI/AccessControl/UserInfo/Modify?format=json
    card_management:
      status: core
      endpoints:
        - GET /ISAPI/AccessControl/CardInfo/capabilities?format=json
        - POST /ISAPI/AccessControl/CardInfo/Search?format=json
        - POST /ISAPI/AccessControl/CardInfo/Record?format=json
        - PUT /ISAPI/AccessControl/CardInfo/SetUp?format=json
        - PUT /ISAPI/AccessControl/CardInfo/Delete?format=json
    access_profile_mapping:
      status: core
      strategy: UserInfo.doorRight + UserInfo.RightPlan
    remote_door_open:
      status: conditional
      endpoints:
        - GET /ISAPI/AccessControl/RemoteControl/door/capabilities
        - PUT /ISAPI/AccessControl/RemoteControl/door/<ID>
    access_events:
      status: core
      endpoints:
        - GET /ISAPI/AccessControl/AcsEvent/capabilities?format=json
        - POST /ISAPI/AccessControl/AcsEvent?format=json
    qr_events:
      status: conditional
      endpoints:
        - GET /ISAPI/AccessControl/QRCodeEvent/capabilities?format=json
        - POST /ISAPI/AccessControl/QRCodeEvent?format=json
    remote_check:
      status: conditional_high_value
      endpoints:
        - GET /ISAPI/AccessControl/remoteCheck/capabilities?format=json
        - PUT /ISAPI/AccessControl/remoteCheck?format=json
    face_biometrics:
      status: future
    fingerprint_biometrics:
      status: future
    anpr_vehicle:
      status: not_from_this_pdf
```

---

## 17. Mapeo canónico Tricor → Hikvision

| Tricor | Hikvision ISAPI | Uso |
|---|---|---|
| `ProviderInstallation` | host, port, auth user/password, device identity | Configuración Edge |
| `AccessIdentity.externalRef` | `UserInfo.employeeNo` | Identidad externa |
| `PropertyResponsible.name` | `UserInfo.name` | Nombre visible |
| `AccessIdentity.validity` | `UserInfo.Valid` | Vigencia |
| `Credential.cardNo` | `CardInfo.cardNo` | Tarjeta/tag |
| `Credential.status=LOST/REVOKED` | `CardInfo.SetUp deleteCard=true` o `CardInfo/Delete` | Revocación de tarjeta |
| `AccessProfile.ACTIVE` | `UserInfo.doorRight` + `RightPlan` | Acceso normal |
| `AccessProfile.MOROSO_DEFAULT` | `doorRight` peatonal únicamente | Morosidad |
| `AccessProfile.REVOKED` | `doorRight` vacío o `Valid.endTime` vencido | Revocación, pendiente lab |
| `Door` | Hikvision door No. / lock ID | Puerta |
| `GuardSupervisorManualOpen` | `RemoteControl/door/<ID>` | Apertura manual |
| `ObservedAccessEvent` | `AcsEvent` | Eventos |
| `QRCodeObservedEvent` | `QRCodeEvent` | Evento QR |
| `RemoteValidation` | `remoteCheck` | Verificación remota |

---

## 18. Flujos Tricor sobre Hikvision

### 18.1 Onboarding de propiedad/responsable

```text
1. Tricor crea Property.
2. Tricor crea AccessIdentity.
3. Edge valida UserInfo capabilities.
4. Edge crea/actualiza UserInfo con employeeNo.
5. Edge asigna doorRight + RightPlan de ACTIVE_ACCESS_PROFILE.
6. Edge registra tarjetas con CardInfo.SetUp o Record.
7. Edge valida UserInfo/Search y CardInfo/Search.
8. Tricor guarda ProviderExternalRef.
```

### 18.2 Morosidad

```text
1. Payment status pasa a overdue.
2. Policy Engine calcula MOROSO_DEFAULT_PROFILE.
3. AccessCommand.RESTRICT_ACCESS.
4. Edge ejecuta UserInfo.SetUp con doorRight peatonal.
5. Edge valida UserInfo/Search.
6. Tricor registra ObservedAccessState.
```

### 18.3 Restauración por pago confirmado

```text
1. Payment provider confirma pago.
2. Policy Engine calcula ACTIVE_ACCESS_PROFILE.
3. AccessCommand.RESTORE_ACCESS.
4. Edge ejecuta UserInfo.SetUp con doorRight completo.
5. Edge valida UserInfo/Search.
6. Tricor marca acceso restaurado.
```

### 18.4 Tarjeta perdida

```text
1. Residente reporta tarjeta perdida.
2. Credential status = LOST_REPORTED.
3. AccessCommand.REVOKE_CREDENTIAL.
4. Edge ejecuta CardInfo.SetUp con deleteCard=true o CardInfo/Delete.
5. Edge valida CardInfo/Search.
6. Administración registra reemplazo si procede.
```

### 18.5 Exresidente / mudanza

```text
1. Administración inicia MoveOutWorkflow.
2. Si hay gracia, Tricor espera hasta endTime.
3. Al vencer, Tricor revoca credenciales y permisos.
4. Estrategia a elegir por laboratorio:
   A. Valid.endTime vencido.
   B. doorRight vacío.
   C. CardInfo deleteCard=true.
   D. UserInfoDetail/Delete.
5. Edge valida limpieza con UserInfo/Search y CardInfo/Search.
```

### 18.6 Guardia supervisor abre manualmente

```text
1. GuardSupervisor solicita apertura manual.
2. Tricor valida permisos del rol y motivo obligatorio.
3. Edge consulta RemoteControl/door capabilities si no están cacheadas.
4. Edge ejecuta PUT RemoteControl/door/<ID>.
5. Tricor audita actor, puerta, caseta, motivo, resultado.
```

### 18.7 QR

```text
1. Tricor genera QR firmado.
2. Dispositivo Hikvision puede escanear QR si capability lo soporta.
3. QRCodeEvent reporta QRCodeInfo.
4. Si remoteCheck está activo, Edge/Tricor responde success/failed.
5. Si remoteCheck no aplica, Tricor Guard PWA valida QR y Edge abre puerta con RemoteControl/door.
```

---

## 19. Clasificación de endpoints

### 19.1 CORE_V1_RESEARCH

Estos endpoints son candidatos directos para adapter v1 de Hikvision después de laboratorio:

```text
GET  /ISAPI/AccessControl/capabilities
GET  /ISAPI/AccessControl/UserInfo/capabilities?format=json
POST /ISAPI/AccessControl/UserInfo/Search?format=json
POST /ISAPI/AccessControl/UserInfo/Record?format=json
PUT  /ISAPI/AccessControl/UserInfo/SetUp?format=json
PUT  /ISAPI/AccessControl/UserInfo/Modify?format=json
GET  /ISAPI/AccessControl/CardInfo/capabilities?format=json
POST /ISAPI/AccessControl/CardInfo/Search?format=json
POST /ISAPI/AccessControl/CardInfo/Record?format=json
PUT  /ISAPI/AccessControl/CardInfo/SetUp?format=json
POST /ISAPI/AccessControl/AcsEvent?format=json
GET  /ISAPI/AccessControl/AcsEvent/capabilities?format=json
GET  /ISAPI/AccessControl/RemoteControl/door/capabilities
PUT  /ISAPI/AccessControl/RemoteControl/door/<ID>
```

### 19.2 CONDITIONAL_HIGH_VALUE

```text
GET  /ISAPI/AccessControl/QRCodeEvent/capabilities?format=json
POST /ISAPI/AccessControl/QRCodeEvent?format=json
GET  /ISAPI/AccessControl/remoteCheck/capabilities?format=json
PUT  /ISAPI/AccessControl/remoteCheck?format=json
GET  /ISAPI/AccessControl/AcsWorkStatus?format=json
GET  /ISAPI/AccessControl/AcsWorkStatus/capabilities?format=json
```

### 19.3 LAB_REQUIRED_DESTRUCTIVE

```text
PUT /ISAPI/AccessControl/UserInfo/Delete?format=json
GET /ISAPI/AccessControl/UserInfoDetail/Delete/capabilities?format=json
PUT /ISAPI/AccessControl/UserInfoDetail/Delete?format=json
GET /ISAPI/AccessControl/UserInfoDetail/DeleteProcess?format=json
PUT /ISAPI/AccessControl/CardInfo/Delete?format=json
```

### 19.4 FUTURE

```text
FingerPrint/*
FDLib/*
Attendance/*
AntiSneak/*
MultiDoorInterLockCfg/*
DoorStatusPlan/*
VerifyPlanTemplate/*
FaceTemperatureEvent/*
```

### 19.5 OUT_OF_SCOPE_V1

```text
Publish/*
customAudio/*
healthCodeCfg/*
temperatureMeasureCfg/*
maskDetection/*
M1CardEncryptCfg/*
WiegandCfg/*
Bluetooth/*
Video metadata RTSP APIs
ANPR metadata APIs
```

---

## 20. Qué NO debe asumirse

No asumir:

```text
- Que todos los dispositivos soportan QR.
- Que todos soportan remoteCheck.
- Que todos soportan remote door open.
- Que doorRight vacío revoca acceso de forma segura.
- Que UserInfoDetail/Delete está disponible en todos los modelos.
- Que CardInfo.SetUp deleteCard=true funciona igual que CardInfo/Delete.
- Que Hikvision puede operar Cloud directo sin Edge.
- Que la lectura QR de Hikvision equivale a validación Tricor.
- Que biometría/rostro entra en v1.
```

---

## 21. Reglas para laboratorio

Antes de implementar adapter real:

```text
1. Probar autenticación Digest.
2. Probar GET /AccessControl/capabilities.
3. Probar UserInfo capabilities.
4. Crear persona de prueba con UserInfo.SetUp.
5. Buscar persona con UserInfo.Search.
6. Asignar doorRight activo.
7. Cambiar doorRight a moroso peatonal.
8. Restaurar doorRight activo.
9. Crear tarjeta con CardInfo.SetUp.
10. Revocar tarjeta con deleteCard=true.
11. Consultar CardInfo.Search.
12. Probar RemoteControl/door en puerta de laboratorio.
13. Probar AcsEvent.
14. Probar QRCodeEvent si el hardware lo soporta.
15. Probar remoteCheck si el hardware lo soporta.
16. Probar exresidente con estrategia no destructiva.
17. Probar UserInfoDetail/Delete solo en datos de laboratorio.
```

---

## 22. QA Harness requerido después de certificar este research

Este archivo no actualiza el QA Harness, pero define las suites que deberán agregarse después:

```text
qa:provider:hikvision:capabilities
qa:provider:hikvision:user-info
qa:provider:hikvision:card-info
qa:provider:hikvision:access-profile-door-right
qa:provider:hikvision:morosity-profile
qa:provider:hikvision:restore-profile
qa:provider:hikvision:lost-card
qa:provider:hikvision:move-out
qa:provider:hikvision:remote-door
qa:provider:hikvision:acs-events
qa:provider:hikvision:qr-events
qa:provider:hikvision:remote-check
qa:provider:hikvision:destructive-ops-lab-only
```

No deben implementarse estas suites hasta que el laboratorio confirme formatos reales de request/response por modelo.

---

## 23. Riesgos principales

### 23.1 Diferencia por modelo

El PDF cubre múltiples series y versiones. No todos los endpoints/campos estarán disponibles en todos los dispositivos.

Mitigación:

```text
ProviderCapabilityMatrix por instalación.
Nunca activar feature sin capability check.
```

### 23.2 Operaciones destructivas

`UserInfoDetail/Delete` puede borrar persona, tarjetas, huellas, rostros y permisos.

Mitigación:

```text
Solo laboratorio.
Después, política explícita.
Nunca ejecutarlo por morosidad.
```

### 23.3 QR y remoteCheck

QRCodeEvent y remoteCheck pueden depender de configuración del dispositivo.

Mitigación:

```text
Tricor Guard PWA debe poder validar QR como fallback.
RemoteControl/door se usa solo si apertura remota está validada.
```

### 23.4 Horarios/perfiles

`RightPlan` y `UserRightPlanTemplate` pueden variar por dispositivo.

Mitigación:

```text
Usar perfiles simples primero.
Mapear puertas permitidas antes de horarios complejos.
```

---

## 24. Decisiones confirmadas

```text
1. El PDF Person-Based Access Control es MATCH.
2. Hikvision se investigará como ISAPI local vía Edge.
3. El mapping principal será UserInfo.employeeNo, CardInfo.cardNo, doorRight y RightPlan.
4. Morosidad no elimina persona ni tarjeta; cambia permisos.
5. Exresidente requiere estrategia de laboratorio.
6. QR en Hikvision es evento/capacidad, no generador canónico de QR de Tricor.
7. RemoteCheck es potencialmente valioso, pero no confirmado.
8. Biometría, rostro, asistencia, salud y video quedan fuera de v1.
```

---

## 25. Hipótesis

```text
1. Edge será requerido para Hikvision ISAPI en producción residencial.
2. UserInfo.SetUp será la operación preferida para upsert de persona.
3. CardInfo.SetUp será la operación preferida para upsert/revocación de tarjeta.
4. doorRight + RightPlan serán suficientes para ACTIVE/MOROSO/REVOKED, pero REVOKED requiere laboratorio.
5. QRCodeEvent + remoteCheck podrían permitir QR Tricor nativo en Hikvision, pero esto requiere laboratorio.
```

---

## 26. Pendientes de laboratorio

```text
- Host/puerto/protocolo real.
- Auth Digest real.
- Campos mínimos aceptados por UserInfo.SetUp.
- Formato exacto de doorRight.
- Formato exacto de RightPlan.
- Si doorRight vacío es válido.
- Si Valid.endTime vencido bloquea inmediatamente.
- Comportamiento de CardInfo.SetUp deleteCard=true.
- Comportamiento de CardInfo/Delete.
- Apertura remota real.
- Tiempo de respuesta de AcsEvent.
- QRCodeEvent real.
- remoteCheck real.
- Reintentos/timeouts/idempotencia.
```

---

## 27. Pendientes de documentación dependiente

Cuando este documento sea aceptado como base, deberán actualizarse, en orden:

```text
1. PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
2. QA_HARNESS_SPEC_V0.1.md
3. ROADMAP_V0.1.md
4. AI_AGENT_RULES_AND_HANDOFF.md
```

No deben tocarse antes de aceptar este archivo.

---

## 28. Definition of Done de este research

Este documento se considera listo como base de Hikvision cuando:

```text
[x] Usa el PDF correcto de Person-Based Access Control.
[x] Clasifica el PDF como MATCH.
[x] Degrada el PDF de metadata/video a fuente no autoritativa para acceso.
[x] Extrae endpoints core reales.
[x] Mapea Tricor → Hikvision sin contaminar dominio.
[x] Separa core, conditional, future, out-of-scope y lab-required.
[x] Define riesgos y límites.
[x] No actualiza documentos dependientes.
[x] Mantiene el mismo nombre de archivo.
```

---

## 29. Estado final

```text
Archivo: PROVIDER_RESEARCH_HIKVISION_V0.1.md
Estado: CERTIFIED como research base
Implementación: BLOCKED hasta laboratorio
Dependencias: proveedor strategy, QA Harness, Roadmap y AI Handoff pendientes de actualización posterior
```

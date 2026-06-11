# QA_HARNESS_SPEC_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado documental:** CERTIFIED como especificación base del QA Harness  
**Estado de implementación:** BLOCKED hasta construir CLI, fixtures, mocks y suites reales  
**Fecha:** 2026-06-11  
**Archivo autoritativo:** `QA_HARNESS_SPEC_V0.1.md`  
**Preparado para:** arquitectura, backend, Edge, provider adapters, pagos, configuración, CI/CD, documentación histórica y handoff posterior a Claude Code CLI  

---

## 0. Control de auditoría

### 0.1 Veredicto de actualización

```text
Fuente: repositorio Tricor-Habit + documentos base de proveedores/pagos ya asentados
Veredicto: MATCH
Motivo: QA Harness debía absorber provider contract tests reales, pagos/webhooks/consulta de pago, Edge, configuración, ZKTeco/CVSecurity 2025 e Hikvision ISAPI PBAC.
```

### 0.2 Archivos fuente usados

Este documento se alinea con:

```text
PROJECT_CHARTER_TRICOR_HABITAT.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
PROVIDER_RESEARCH_HIKVISION_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
```

### 0.3 Archivos no tocados por esta actualización

Esta actualización solo modifica:

```text
QA_HARNESS_SPEC_V0.1.md
```

No modifica:

```text
PROJECT_CHARTER_TRICOR_HABITAT.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
PROVIDER_RESEARCH_HIKVISION_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
ROADMAP_V0.1.md
AI_AGENT_RULES_AND_HANDOFF.md
REPO_STRUCTURE_V0.1.md
USER_MANUAL_PLAN.md
README.md
DOCS_INDEX_AND_DEPENDENCY_MAP.md
```

---

## 1. Propósito del documento

Este documento define la especificación formal del **QA Harness** de Tricor Hábitat.

El QA Harness es la herramienta técnica que debe impedir que el desarrollo avance por parches, suposiciones o validaciones manuales incompletas. Debe convertir flujos críticos en pruebas reproducibles, auditables y entendibles para humanos y agentes de código.

El QA Harness debe validar:

```text
- dominio canónico
- configuración y Policy Engine
- pagos y webhooks
- morosidad y restauración
- credenciales
- QR residente/invitado
- Edge y modo offline
- provider contracts
- capability matrix
- auditoría
- seguridad/roles
- observabilidad
```

Este documento no implementa el QA Harness. Es la fuente de verdad para construirlo.

---

## 2. Principio rector

```text
No se acepta ningún cambio crítico si no puede probarse con QA Harness.
```

El ciclo correcto del proyecto es:

```text
Contrato → fixture → flujo QA → fallo exacto → fix mínimo → regresión → reporte
```

No se acepta el ciclo:

```text
prompt → parche → prueba manual → error → otro parche
```

---

## 3. Estados documentales y de ejecución

### 3.1 Estado del documento

```text
Documento QA_HARNESS_SPEC_V0.1.md: CERTIFIED como especificación base.
```

### 3.2 Estado de implementación

```text
Implementación QA Harness: BLOCKED hasta bootstrap del repo.
```

### 3.3 Estado de pruebas reales

```text
Provider real tests: BLOCKED hasta laboratorio.
Payment real/sandbox tests: BLOCKED hasta credenciales sandbox y webhooks.
CI tests: BLOCKED hasta repo técnico.
```

---

## 4. Ubicación objetivo en el repo

```text
tricor-habitat/
├── apps/
│   └── qa/
│       ├── src/
│       │   ├── cli.ts
│       │   ├── config/
│       │   ├── clients/
│       │   ├── fixtures/
│       │   ├── flows/
│       │   ├── suites/
│       │   ├── mocks/
│       │   ├── provider-contracts/
│       │   ├── payments/
│       │   ├── edge/
│       │   ├── assertions/
│       │   ├── reporters/
│       │   └── utils/
│       ├── package.json
│       └── README.md
└── artifacts/
    └── qa/
```

El QA Harness puede iniciar en `apps/qa`. Si crece, partes reutilizables pueden moverse a `packages/qa-core`.

---

## 5. Ambientes de ejecución

```text
local
ci
staging
hardware-smoke
payment-sandbox
```

### 5.1 local

Usa:

```text
API local
PostgreSQL local o Docker
Edge simulator
Provider mock
Payment mock
Storage mock
```

### 5.2 ci

Usa:

```text
Base efímera
Mocks obligatorios
Sin hardware real
Sin pagos reales
Sin apertura física
```

### 5.3 staging

Usa:

```text
API staging
Base staging
Payment sandbox
Provider mock por defecto
Edge staging opcional
```

### 5.4 hardware-smoke

Usa:

```text
Edge real
Proveedor real
Credenciales de laboratorio
Puerta/perfil/persona/tarjeta de prueba
```

Nunca se ejecuta automáticamente en cada PR.

### 5.5 payment-sandbox

Usa:

```text
Mercado Pago sandbox
Webhook público controlado
Credenciales sandbox
Ordenes de prueba
Estados simulados o reales sandbox
```

---

## 6. Comandos CLI objetivo

### 6.1 Comandos base

```text
pnpm qa health
pnpm qa seed
pnpm qa reset
pnpm qa report
pnpm qa ci
```

### 6.2 Configuración

```text
pnpm qa config defaults
pnpm qa config valid
pnpm qa config invalid
pnpm qa config dependencies
pnpm qa config provider-capabilities
pnpm qa config impact
```

### 6.3 Flujos de negocio

```text
pnpm qa flow property-debt-access-lifecycle
pnpm qa flow manual-payment-fallback-lifecycle
pnpm qa flow payment-confirmed-edge-offline
pnpm qa flow resident-move-out-credential-revocation
pnpm qa flow lost-credential
pnpm qa flow guest-qr-morosity-policy
```

### 6.4 Provider contracts

```text
pnpm qa provider-contract generic-mock
pnpm qa provider-contract zkteco --mock
pnpm qa provider-contract zkteco --hardware-smoke
pnpm qa provider-contract hikvision --mock
pnpm qa provider-contract hikvision --hardware-smoke
```

### 6.5 Pagos

```text
pnpm qa payments mercado-pago --mock
pnpm qa payments mercado-pago --sandbox
pnpm qa payments mercado-pago-webhook --mock
pnpm qa payments manual-fallback
```

### 6.6 Edge

```text
pnpm qa edge health
pnpm qa edge sync
pnpm qa edge offline-reconnect
pnpm qa edge command-idempotency
pnpm qa edge provider-drift
```

---

## 7. Variables de entorno QA

```bash
QA_ENV=local
QA_BASE_URL=http://localhost:3000
QA_API_URL=http://localhost:4000
QA_EDGE_URL=http://localhost:4100
QA_DB_URL=postgresql://...
QA_RUN_ID=auto
QA_ARTIFACTS_DIR=artifacts/qa
QA_USE_PROVIDER_MOCK=true
QA_USE_PAYMENT_MOCK=true
QA_USE_STORAGE_MOCK=true
QA_ENABLE_DESTRUCTIVE_RESET=false
```

### 7.1 Variables para ZKTeco hardware-smoke

```bash
QA_ZK_ENABLED=false
QA_ZK_BASE_URL=https://localhost:8098
QA_ZK_ACCESS_TOKEN_SECRET_REF=<secret-ref>
QA_ZK_TEST_DEPT_CODE=2
QA_ZK_TEST_PERSON_PIN=1
QA_ZK_TEST_CARD_NO=7325912
QA_ZK_TEST_DOOR_ID=1
QA_ZK_ACTIVE_ACC_LEVEL_NAME=Pagador
QA_ZK_MOROSO_ACC_LEVEL_NAME=Moroso
```

Regla:

```text
Nunca imprimir access_token en logs, reportes o snapshots.
```

### 7.2 Variables para Hikvision hardware-smoke

```bash
QA_HIKVISION_ENABLED=false
QA_HIKVISION_BASE_URL=http://192.168.x.x
QA_HIKVISION_AUTH_MODE=digest
QA_HIKVISION_USERNAME_SECRET_REF=<secret-ref>
QA_HIKVISION_PASSWORD_SECRET_REF=<secret-ref>
QA_HIKVISION_TEST_EMPLOYEE_NO=tricor-test-001
QA_HIKVISION_TEST_CARD_NO=0000000001
QA_HIKVISION_TEST_DOOR_NO=1
```

Regla:

```text
Basic auth solo se permite en laboratorio o red segura; Digest debe ser ruta preferente.
```

### 7.3 Variables para Mercado Pago sandbox

```bash
QA_MP_ENABLED=false
QA_MP_ENV=sandbox
QA_MP_ACCESS_TOKEN_SECRET_REF=<secret-ref>
QA_MP_PUBLIC_KEY_REF=<secret-ref>
QA_MP_WEBHOOK_SECRET_REF=<secret-ref>
QA_MP_BASE_URL=https://api.mercadopago.com
```

---

## 8. Fixtures determinísticos

Fixtures mínimos:

```text
qa_condo_real_bilbao
qa_private_ariatza
qa_property_a101
qa_owner_juan_perez
qa_finance_operator
qa_condo_admin
qa_guard
qa_guard_supervisor
qa_resident
qa_credential_tag_001
qa_credential_resident_qr
qa_guest_qr_001
qa_access_profile_active
qa_access_profile_moroso_default
qa_access_zone_pedestrian
qa_access_zone_vehicular
qa_edge_main_gate
qa_provider_installation_mock
qa_payment_provider_mock
```

Regla:

```text
Todo fixture debe llevar qaRunId.
Nunca limpiar datos sin qaRunId fuera de base efímera.
```

---

## 9. Componentes internos del QA Harness

```text
CLI
EnvironmentChecker
ApiClient
DbInspector
FixtureFactory
FlowRunner
AssertionEngine
PolicyConfigTestRunner
ProviderContractRunner
PaymentTestRunner
EdgeSimulator
ProviderMockServer
PaymentMockServer
StorageMock
ReportBuilder
```

### 9.1 ProviderContractRunner

Responsable de validar que un proveedor cumple contratos canónicos.

Debe probar:

```text
validateInstallation
getCapabilities
validateMappings
applyAccessProfile
revokeCredential
restoreCredential
disableCredential
openDoor
fetchObservedState
reconcile
```

No todos los proveedores soportan todo. La prueba debe respetar ProviderCapabilityMatrix.

### 9.2 PaymentTestRunner

Responsable de validar pagos, webhooks, consulta server-side e idempotencia.

Debe probar:

```text
create order
receive webhook
validate signature
fetch order/payment by API
normalize status
mark payment confirmed
trigger restore workflow
reject unverified webhook
deduplicate repeated webhook
manual fallback
```

### 9.3 EdgeSimulator

Responsable de simular:

```text
heartbeat
command pull
command ack
command result
provider success
provider failure
edge offline
edge reconnect
local queue
observed state
```

---

## 10. Suites de configuración

### 10.1 qa:config:defaults

Debe validar:

```text
Preset Estándar
moroso = peatonal permitido
moroso = vehicular bloqueado
moroso = QR invitados bloqueado
moroso = zonas no esenciales bloqueadas
pago parcial no restaura por default
pago proveedor confirmado restaura por default
transferencia manual fallback habilitable
```

### 10.2 qa:config:dependencies

Debe validar:

```text
No activar restauración automática sin proveedor de pago activo.
No bloquear QR invitados si QR invitados está apagado.
No activar remote open si provider.remoteDoorOpen = UNSUPPORTED.
No usar perfil moroso si mapping MOROSO_DEFAULT_PROFILE falta.
No activar provider real si ProviderCapabilityMatrix contiene UNKNOWN en capacidades core.
```

### 10.3 qa:config:impact

Debe validar que cambios críticos produzcan vista de impacto:

```text
cuántas propiedades afectadas
qué accesos cambian
qué Edge/proveedor ejecutará comandos
qué mappings se requieren
qué incidentes podrían abrirse
```

---

## 11. Suites de dominio

### 11.1 qa:domain:organization

Prueba:

```text
Condominio → Privada → Propiedad
Privada independiente sin romper estructura interna
propiedad sin privada operativa = inválida
propietario responsable principal único
propietario con varias propiedades permitido
```

### 11.2 qa:domain:credentials

Prueba:

```text
credenciales limitadas por propiedad
residente puede reportar tarjeta perdida
residente no puede emitir tarjeta nueva
credencial perdida genera revocación/suspensión
credencial sin uso genera alerta no invasiva
exresidente revoca credenciales al terminar gracia
```

### 11.3 qa:domain:morosity

Prueba:

```text
cargo vencido genera deuda
periodo de gracia evita suspensión inmediata
fin de gracia aplica MOROSO_DEFAULT_PROFILE
pago confirmado restaura
pago parcial no restaura por default
manual fallback requiere aprobación
```

---

## 12. Suites de pagos: Mercado Pago

### 12.1 Principio de pago confirmado

Regla obligatoria:

```text
Webhook no confirma pago por sí solo.
Webhook despierta validación.
Tricor debe consultar Mercado Pago por API antes de restaurar acceso.
```

### 12.2 Endpoints base a cubrir

Dependiendo del flujo final, QA debe cubrir:

```http
POST https://api.mercadopago.com/v1/orders
GET  https://api.mercadopago.com/v1/orders/{order_id}
GET  https://api.mercadopago.com/v1/payments/{payment_id}
```

Autenticación:

```http
Authorization: Bearer <ACCESS_TOKEN>
```

### 12.3 qa:payments:mercado-pago:create-order

Debe validar:

```text
PaymentOrder interna creada
externalReference estable
idempotencyKey generado
orden creada con token del condominio
providerOrderId guardado
no se mezclan fondos SaaS con fondos de mantenimiento
```

### 12.4 qa:payments:mercado-pago:webhook-signature

Debe validar:

```text
webhook con firma válida se acepta
webhook sin firma se rechaza
webhook con firma inválida se rechaza
payload raw se almacena seguro
x-signature / request id se registran si aplica
```

### 12.5 qa:payments:mercado-pago:confirm-by-api

Debe validar:

```text
webhook recibido
payment_id/order_id extraído
Tricor consulta API de Mercado Pago
status acreditado normaliza a PaymentOrder.CONFIRMED
solo entonces se dispara RestoreAccessWorkflow
```

Estados esperados:

```text
approved / paid / processed / accredited → CONFIRMED, según endpoint real y mapping aprobado
pending / in_process → PENDING_PROVIDER
rejected / cancelled / expired → FAILED/EXPIRED/CANCELLED
refunded / charged_back → REVIEW_REQUIRED / ACCESS_RECALC_REQUIRED
```

### 12.6 qa:payments:mercado-pago:duplicate-webhook

Debe validar:

```text
mismo webhook dos veces no duplica pago
mismo payment_id no duplica restauración
mismo RestoreAccessWorkflow es idempotente
```

### 12.7 qa:payments:mercado-pago:spei-pending

Debe validar:

```text
SPEI generado queda PENDING_PROVIDER
no restaura acceso mientras no esté confirmado
al confirmarse por API/webhook dispara restauración
```

### 12.8 qa:payments:manual-fallback

Debe validar:

```text
residente sube comprobante
Finance/CondoAdmin aprueba manualmente
comprobante por sí solo no restaura
aprobación manual auditada dispara recálculo
sistema genera RestoreAccessWorkflow si deuda queda saldada
```

---

## 13. Provider contract: Generic Mock

Debe ser el primer proveedor usado en CI.

Capacidades simuladas:

```text
personManagement = SUPPORTED
credentialManagement = SUPPORTED
accessProfileManagement = SUPPORTED
remoteDoorOpen = SUPPORTED
accessEventRead = SUPPORTED
observedStateRead = SUPPORTED
guestQr = SUPPORTED
residentQr = SUPPORTED
```

Debe permitir inyectar fallas:

```text
provider offline
command timeout
mapping missing
unsupported capability
partial success
drift detected
```

---

## 14. Provider contract: ZKTeco / CVSecurity

### 14.1 Estado

```text
Documentación base: CERTIFIED
Implementación: BLOCKED hasta laboratorio
QA real: hardware-smoke only
```

### 14.2 Fuente

```text
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
```

### 14.3 Endpoints contractuales a cubrir

#### Personas

```http
GET  /api/person/get?pin={pin}&access_token={token}
GET  /api/person/get/{pin}?access_token={token}
POST /api/person/add?access_token={token}
POST /api/person/leave?access_token={token}
POST /api/person/reinstated?access_token={token}
```

#### Tarjetas

```http
GET  /api/card/getCards?pin={pin}&access_token={token}
GET  /api/card/getCards/{pin}?access_token={token}
POST /api/card/set?access_token={token}
```

#### Departamentos

```http
GET  /api/department/getDepartmentList?pageNo={pageNo}&pageSize={pageSize}&access_token={token}
GET  /api/department/get?code={code}&access_token={token}
```

#### Access levels

```http
GET  /api/accLevel/list?pageNo={pageNo}&pageSize={pageSize}&access_token={token}
POST /api/accLevel/addLevelPerson?access_token={token}
POST /api/accLevel/addLevelDoor?access_token={token}
POST /api/accLevel/syncPerson?access_token={token}
```

#### Puertas y lectores

```http
GET  /api/door/list?pageNo={pageNo}&pageSize={pageSize}&access_token={token}
GET  /api/reader/list?pageNo={pageNo}&pageSize={pageSize}&access_token={token}
POST /api/door/remoteOpenById?doorId={doorId}&interval={seconds}&access_token={token}
POST /api/door/remoteOpenByName?doorName={name}&interval={seconds}&access_token={token}
```

#### Transacciones

```http
POST /api/transaction/list?access_token={token}
POST /api/transaction/getDoorTransactionDetail?access_token={token}
```

#### QR

```http
POST /api/person/getQrCode/{pin}?access_token={token}
POST /api/v2/person/getQrCode?pin={pin}&access_token={token}
POST /api/visRegistration/add?access_token={token}
POST /api/visRegistration/getQrCode?access_token={token}
```

#### Entrance control / PSG

```http
GET  /api/psgGate/allGateState?access_token={token}
POST /api/psgGate/remoteOpenById?gateId={gateId}&access_token={token}
POST /api/psgLevel/syncPerson?access_token={token}
```

PSG queda `CONDITIONAL/LAB_REQUIRED` si no hay gate/pluma configurado.

### 14.4 qa:provider:zkteco:mock

Debe validar el contrato sin hardware:

```text
validar capabilities mock
crear persona mock
asignar tarjeta mock
aplicar perfil Pagador
aplicar perfil Moroso
restaurar Pagador
leer observed state mock
probar idempotencia de comandos
```

### 14.5 qa:provider:zkteco:hardware-smoke

Debe seguir el lab plan:

```text
T-ZK-001 connectivity/token
T-ZK-010 departments
T-ZK-020 person get
T-ZK-030 card get/set
T-ZK-040 door/list reader/list
T-ZK-050 accLevel list Pagador/Moroso
T-ZK-060 Pagador → Moroso → Pagador
T-ZK-070 transactions
T-ZK-080 QR resident/visitor
T-ZK-090 remote open with operator confirmation
```

No ejecutar destructivas automáticamente.

---

## 15. Provider contract: Hikvision ISAPI PBAC

### 15.1 Estado

```text
Research base: CERTIFIED
Implementación: BLOCKED hasta laboratorio
QA real: hardware-smoke only
```

### 15.2 Fuente

```text
PROVIDER_RESEARCH_HIKVISION_V0.1.md
Intelligent Security API (Person-Based Access Control) Part 1.pdf
```

### 15.3 Endpoints contractuales a cubrir

#### Capacidades

```http
GET /ISAPI/AccessControl/capabilities
GET /ISAPI/AccessControl/AcsCfg/capabilities?format=json
GET /ISAPI/AccessControl/UserInfo/capabilities?format=json
GET /ISAPI/AccessControl/CardInfo/capabilities?format=json
GET /ISAPI/AccessControl/RemoteControl/door/capabilities
GET /ISAPI/AccessControl/AcsEvent/capabilities?format=json
```

#### Personas

```http
GET  /ISAPI/AccessControl/UserInfo/Count?format=json
POST /ISAPI/AccessControl/UserInfo/Search?format=json
POST /ISAPI/AccessControl/UserInfo/Record?format=json
PUT  /ISAPI/AccessControl/UserInfo/SetUp?format=json
PUT  /ISAPI/AccessControl/UserInfo/Modify?format=json
PUT  /ISAPI/AccessControl/UserInfo/Delete?format=json
PUT  /ISAPI/AccessControl/UserInfoDetail/Delete?format=json
GET  /ISAPI/AccessControl/UserInfoDetail/DeleteProcess?format=json
```

#### Tarjetas

```http
GET  /ISAPI/AccessControl/CardInfo/Count?format=json
POST /ISAPI/AccessControl/CardInfo/Search?format=json
POST /ISAPI/AccessControl/CardInfo/Record?format=json
PUT  /ISAPI/AccessControl/CardInfo/SetUp?format=json
PUT  /ISAPI/AccessControl/CardInfo/Modify?format=json
PUT  /ISAPI/AccessControl/CardInfo/Delete?format=json
```

#### Acceso/permisos

Mapping principal:

```text
Tricor AccessProfile → Hikvision UserInfo.doorRight + RightPlan
```

QA debe validar:

```text
ACTIVE_ACCESS_PROFILE asigna doorRight completo
MOROSO_DEFAULT_PROFILE deja solo puerta peatonal o doorRight reducido
REVOKED/EXRESIDENTE aplica estrategia validada por laboratorio
RightPlan existe y es interpretable
```

#### Apertura remota

```http
PUT /ISAPI/AccessControl/RemoteControl/door/<ID>
```

#### Eventos

```http
POST /ISAPI/AccessControl/AcsEvent?format=json
POST /ISAPI/AccessControl/QRCodeEvent?format=json
PUT  /ISAPI/AccessControl/remoteCheck?format=json
```

### 15.4 qa:provider:hikvision:mock

Debe validar:

```text
capabilities mock
UserInfo upsert
CardInfo set/delete
AccessProfile → doorRight + RightPlan
RemoteControl/door simulated
AcsEvent observed state
QRCodeEvent observed event
remoteCheck simulated
```

### 15.5 qa:provider:hikvision:hardware-smoke

Debe validar en dispositivo real:

```text
auth Basic/Digest
capabilities reales
crear/buscar persona disposable
asignar tarjeta disposable
cambiar doorRight/RightPlan
remote open con operador presente
leer AcsEvent
QRCodeEvent si módulo/dispositivo lo soporta
revocación segura sin operación destructiva irreversible
```

`UserInfoDetail/Delete` queda marcado como destructivo y requiere aprobación explícita.

---

## 16. ProviderCapabilityMatrix tests

Debe probar para cada proveedor:

```text
UNKNOWN no permite activar feature
UNSUPPORTED oculta/deshabilita configuración
PARTIAL requiere explicación y fallback
SUPPORTED requiere QA pass
SUPPORTED_BY_DOCS no equivale a SUPPORTED_IN_INSTALLATION
LAB_REQUIRED bloquea producción
```

### 16.1 Casos obligatorios

```text
Provider sin remoteDoorOpen → Guard no muestra apertura remota.
Provider sin guestQr → bloquear QR invitado por proveedor no aplica.
Provider sin accessProfileManagement → morosidad por cambio de perfil queda bloqueada.
Provider con accessEventRead UNKNOWN → observed state no se promete como real-time.
Provider mapping incompleto → instalación no activa.
```

---

## 17. Edge tests

### 17.1 qa:edge:health

Valida:

```text
Edge enrollado
heartbeat activo
provider status reportado
config descargada
snapshot aplicado
```

### 17.2 qa:edge:offline-reconnect

Valida:

```text
Edge offline
Cloud genera comando
comando queda PENDING_EDGE_OFFLINE
se crea incidente
Edge reconecta
Edge descarga comando
Edge ejecuta
Cloud actualiza observed state
incidente se resuelve o cambia estado
```

### 17.3 qa:edge:command-idempotency

Valida:

```text
mismo commandId ejecutado dos veces no duplica permisos
provider operation idempotency key preservada
resultado repetido se normaliza sin error fatal
```

### 17.4 qa:edge:provider-drift

Valida:

```text
ObservedAccessState distinto a DesiredAccessState
se crea drift incident
reconciliación propone comando correctivo
no corrige automáticamente si operación es destructiva sin política
```

---

## 18. Flujos E2E obligatorios

### 18.1 property-debt-access-lifecycle

Pasos:

```text
crear condominio
crear privada
crear propiedad
asignar responsable
crear credencial
crear cargo vencido
aplicar morosidad
calcular MOROSO_DEFAULT_PROFILE
generar DesiredAccessState
generar AccessCommand
provider mock aplica restricción
observed state confirma
Guard ve acceso restringido
Resident ve estado moroso
pago confirmado por proveedor
Policy Engine recalcula ACTIVE_ACCESS_PROFILE
provider mock restaura
Guard/Resident ven acceso activo
auditoría completa
```

### 18.2 manual-payment-fallback-lifecycle

Pasos:

```text
crear deuda
residente sube comprobante
comprobante no restaura
Finance aprueba manualmente
pago manual queda APPROVED_MANUAL
Policy Engine recalcula
RestoreAccessWorkflow se dispara
auditoría incluye aprobador
```

### 18.3 payment-confirmed-edge-offline

Pasos:

```text
propiedad morosa
Edge offline
pago confirmado por Mercado Pago mock
PaymentOrder CONFIRMED
Access state PAYMENT_CONFIRMED_ACCESS_PENDING
Command PENDING_EDGE_OFFLINE
incidente Edge offline
Edge reconecta
comando ejecutado
access state ACTIVE
```

### 18.4 resident-move-out-credential-revocation

Pasos:

```text
propiedad con credenciales activas
MoveOutWorkflow programado
gracia activa
al expirar gracia
credenciales revocadas/suspendidas
provider command generado
exresponsable archivado
historial preservado
```

### 18.5 lost-credential

Pasos:

```text
residente reporta tarjeta perdida
Credential LOST_REPORTED
AccessCommand disableCredential
provider mock desactiva tarjeta
administración ve alerta
residente no puede registrar tarjeta nueva
```

### 18.6 guest-qr-morosity-policy

Pasos:

```text
propiedad activa genera QR invitado
propiedad entra en morosidad
policy blockGuestQr=true
QR invitado queda inválido
Guard escanea QR inválido
Guard ve motivo operativo permitido
propiedad paga
QR policy se recalcula
nuevo QR puede generarse si policy lo permite
```

---

## 19. Seguridad y roles

### 19.1 Guard

Pruebas:

```text
Guard no edita residentes
Guard no edita pagos
Guard no crea credenciales
Guard no abre manualmente sin rol supervisor
Guard puede escanear QR
Guard puede registrar eventos/rondines
```

### 19.2 GuardSupervisor

Pruebas:

```text
GuardSupervisor puede apertura manual con motivo
GuardSupervisor puede excepción temporal si policy lo permite
apertura manual genera auditoría
excepción temporal tiene duración máxima
CondoAdmin recibe notificación/auditoría
```

### 19.3 Finance

Pruebas:

```text
Finance aprueba solo pagos manuales
Finance no edita permisos directamente
aprobación manual dispara Policy Engine
rechazo de comprobante no restaura acceso
```

### 19.4 Resident

Pruebas:

```text
Resident solo ve sus propiedades
Resident puede pagar
Resident puede subir comprobante
Resident puede reportar credencial perdida
Resident no registra nueva tarjeta
Resident genera QR solo si policy lo permite
```

---

## 20. Reportes QA

Cada ejecución debe generar:

```text
artifacts/qa/latest-report.md
artifacts/qa/latest-report.json
```

Reporte mínimo:

```text
runId
environment
commitSha
suite
status
steps
failures
warnings
provider mode
payment mode
edge mode
artifacts
```

Estados:

```text
PASS
FAIL
SKIPPED_BY_CAPABILITY
BLOCKED_BY_CONFIGURATION
BLOCKED_BY_LAB_REQUIRED
BLOCKED_BY_MISSING_SECRET
```

---

## 21. CI gate mínimo

Antes de aceptar PR crítico:

```text
pnpm lint
pnpm typecheck
pnpm test
pnpm qa health
pnpm qa config valid
pnpm qa config invalid
pnpm qa flow property-debt-access-lifecycle
pnpm qa payments mercado-pago --mock
pnpm qa provider-contract generic-mock
pnpm qa edge offline-reconnect
```

No se ejecuta hardware-smoke en CI normal.

---

## 22. Hardware smoke gate

Hardware-smoke requiere confirmación humana.

Debe preguntar explícitamente:

```text
¿Confirmas que esta prueba puede abrir una puerta física?
¿Confirmas que se usará una persona/tarjeta de laboratorio?
¿Confirmas que el entorno no es producción?
```

Sin confirmación:

```text
status = BLOCKED_BY_OPERATOR_CONFIRMATION
```

---

## 23. Reglas para Claude Code CLI

Claude Code CLI debe respetar:

```text
1. No implementar proveedor real sin provider contract tests.
2. No implementar Mercado Pago sin webhook validation + API confirmation test.
3. No tocar Edge real sin Edge simulator tests.
4. No declarar success si QA Harness no existe o no pasa.
5. No agregar configuración sin PolicyConfigValidator tests.
6. No agregar endpoint sin test de permisos/tenant/auditoría.
7. No inventar endpoints de proveedor.
8. No mezclar términos del proveedor en packages/domain.
```

Reporte obligatorio de Claude:

```text
- tarea ejecutada
- archivos modificados
- pruebas ejecutadas
- pruebas no ejecutadas y razón
- QA report path
- riesgos pendientes
- siguiente paso recomendado
```

---

## 24. Roadmap de implementación del QA Harness

### Fase QA-0 — Bootstrap

```text
apps/qa
CLI básico
health check
report builder
fixtures iniciales
```

### Fase QA-1 — Config y dominio

```text
PolicyConfigValidator runner
fixtures Condominio/Privada/Propiedad
morosity policy tests
credential lifecycle tests
```

### Fase QA-2 — Provider mock + Edge simulator

```text
provider mock
actions applyAccessProfile/openDoor/fetchObservedState
edge simulator
command queue tests
```

### Fase QA-3 — Payment mock

```text
Mercado Pago mock
webhook signature simulation
GET order/payment confirmation simulation
manual fallback
```

### Fase QA-4 — Provider contract suites

```text
ZKTeco mock contract
Hikvision mock contract
capability matrix tests
mapping validation
```

### Fase QA-5 — Hardware smoke optional

```text
ZKTeco lab tests
Hikvision lab tests
Mercado Pago sandbox tests
```

---

## 25. Definition of Done

El QA Harness está listo para MVP técnico cuando:

```text
1. Existe CLI ejecutable.
2. Hay fixtures determinísticos.
3. Hay reportes md/json.
4. Hay provider mock.
5. Hay payment mock.
6. Hay edge simulator.
7. Flujos core pasan.
8. Config inválida falla correctamente.
9. Webhook duplicado no duplica pagos.
10. Provider contract generic mock pasa.
11. Edge offline/reconnect pasa.
12. Auditoría se valida en flujos críticos.
```

---

## 26. Estado final del documento

```text
QA_HARNESS_SPEC_V0.1.md: CERTIFIED como especificación base.
QA Harness implementation: BLOCKED hasta bootstrap técnico.
Provider real tests: BLOCKED hasta laboratorio.
Payment sandbox tests: BLOCKED hasta credenciales sandbox.
```

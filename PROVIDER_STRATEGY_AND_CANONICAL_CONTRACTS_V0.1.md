# PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado documental:** CERTIFIED como estrategia base de proveedores y contratos canónicos  
**Estado de implementación:** BLOCKED para proveedores reales hasta QA Harness + laboratorio  
**Fecha:** 2026-06-11  
**Archivo autoritativo:** `PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md`  
**Modo de actualización:** actualización controlada del mismo archivo, sin crear duplicados  

---

## 0. Control de auditoría

### 0.1 Veredicto de la tarea

```text
Tarea: actualizar Provider Strategy and Canonical Contracts
Veredicto: MATCH
Motivo: existen documentos base suficientes y certificados/revisados para consolidar estrategia de proveedores:
- dominio canónico de Tricor
- arquitectura Cloud + Edge
- configuración/policy engine
- provider ZKTeco / CVSecurity basado en PDF 2025
- provider Hikvision ISAPI PBAC basado en PDF Person-Based Access Control
- Mercado Pago como proveedor de pagos documentado por separado
```

### 0.2 Archivo actualizado

```text
Archivo objetivo: PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
Archivos modificados en esta tarea: solo este archivo
```

### 0.3 Archivos no tocados

```text
PROJECT_CHARTER_TRICOR_HABITAT.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
PROVIDER_RESEARCH_HIKVISION_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
QA_HARNESS_SPEC_V0.1.md
ROADMAP_V0.1.md
AI_AGENT_RULES_AND_HANDOFF.md
REPO_STRUCTURE_V0.1.md
USER_MANUAL_PLAN.md
README.md
```

### 0.4 Documentos fuente utilizados

Este documento consolida decisiones ya asentadas en el repositorio:

```text
PROJECT_CHARTER_TRICOR_HABITAT.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
PROVIDER_RESEARCH_HIKVISION_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
DOCS_INDEX_AND_DEPENDENCY_MAP.md
```

### 0.5 Estado del documento

```text
Documento: CERTIFIED
Uso permitido: arquitectura, contratos canónicos, diseño de provider-sdk, diseño de Edge, diseño de QA Harness
Uso no permitido: implementación productiva de providers sin pruebas de laboratorio
```

---

## 1. Propósito

Este documento define la estrategia oficial de integración con proveedores externos para **Tricor Hábitat** y los contratos canónicos que deben proteger el dominio del producto.

Su propósito principal es evitar que Tricor vuelva a depender de estructuras internas, rutas, nombres, permisos o payloads específicos de un proveedor.

La regla central:

```text
Tricor Hábitat define el dominio.
El proveedor expone capacidades externas.
El adapter traduce.
El QA Harness valida.
El laboratorio confirma.
```

Este documento aplica a:

```text
- proveedores de control de acceso
- provider adapters
- ProviderCapabilityMatrix
- ProviderInstallation
- ProviderMapping
- ProviderExternalRef
- AccessCommand
- DesiredAccessState
- ObservedAccessState
- Edge
- QA Harness
- Tricor Condo → Configuración → Proveedor
- Tricor Platform → soporte/monitoreo
```

Este documento **no** define implementación fiscal, contable, UI final ni código productivo.

---

## 2. Principio rector

El dominio de Tricor Hábitat nunca debe hablar en idioma de proveedor.

El dominio debe hablar en términos propios:

```text
Condominio
Privada
Propiedad
Propietario/responsable
Credencial
Zona de acceso
Perfil de acceso
Permiso
Estado deseado
Estado observado
Comando
Incidente
Auditoría
```

Los proveedores pueden hablar en términos propios:

```text
ZKTeco: pin, cardNo, accLevelIds, doorId, psgGateId, transaction
Hikvision: employeeNo, cardNo, doorRight, RightPlan, door ID, AcsEvent, QRCodeEvent
Mercado Pago: order, payment, webhook, access_token, x-signature
```

Pero esos términos solo pueden existir en:

```text
provider adapters
payment provider adapters
ProviderMapping
ProviderExternalRef
ProviderCapabilityMatrix
provider-specific QA tests
Edge connector
technical documentation
```

No deben contaminar:

```text
packages/domain
Policy Engine
reglas de morosidad
reglas de pagos
modelos principales de Tricor
UI normal de Condo/Guard/Resident
```

---

## 3. Alcance de este documento

### 3.1 En alcance

```text
- Estrategia provider-neutral de control de acceso.
- Contratos canónicos para AccessProviderAdapter.
- ProviderCapabilityMatrix.
- ProviderInstallation.
- ProviderMapping.
- ProviderExternalRef.
- Estrategia ZKTeco / CVSecurity.
- Estrategia Hikvision ISAPI Person-Based Access Control.
- Relación con Mercado Pago como proveedor de pagos, sin mezclarlo con access providers.
- Reglas de Edge.
- Reglas de QA/laboratorio.
- Reglas para futuros proveedores.
```

### 3.2 Fuera de alcance

```text
- Implementación productiva.
- Código final.
- UI final.
- Facturación fiscal propia.
- Contabilidad completa.
- Integraciones no aprobadas.
- Automatizaciones externas como core.
- Pruebas de hardware ejecutadas.
```

---

## 4. Clasificación de proveedores

Tricor maneja dos familias distintas de proveedores.

### 4.1 Access Providers

Controlan accesos físicos o permisos de acceso.

```text
ZKTeco / ZKBio CVSecurity
Hikvision ISAPI Person-Based Access Control
Generic Mock Provider
```

Estos providers usan:

```text
AccessProviderAdapter
ProviderCapabilityMatrix
ProviderMapping
ProviderExternalRef
AccessCommand
DesiredAccessState
ObservedAccessState
Edge
```

### 4.2 Payment Providers

Procesan pagos y confirman dinero.

```text
Mercado Pago
Stripe futuro
Manual transfer fallback
```

Estos providers usan:

```text
PaymentProviderInstallation
PaymentOrder
PaymentAttempt
PaymentProviderEvent
PaymentReconciliationRecord
PaymentProviderAdapter
```

Regla importante:

```text
Mercado Pago no es un AccessProvider.
Mercado Pago no aplica permisos.
Mercado Pago solo confirma pagos.
Tricor recalcula acceso.
Edge/proveedor de acceso aplica el resultado.
```

---

## 5. Decisión estratégica v0.1

### 5.1 Access provider implementado primero

```text
Provider: ZKTeco / ZKBio CVSecurity
ProviderKey: ZKTECO_CVSECURITY
Estado documental: CERTIFIED
Implementación: BLOCKED hasta laboratorio
Modo esperado: EDGE_LOCAL
Prioridad: MVP v1
```

Razón:

```text
ZKTeco / CVSecurity es el primer proveedor conocido, con manual 2025 disponible y laboratorio local definido.
Debe validar el flujo central: pagos → morosidad → perfil de acceso → Edge → proveedor → estado observado.
```

### 5.2 Access provider en research certificado

```text
Provider: Hikvision ISAPI Person-Based Access Control
ProviderKey: HIKVISION_ISAPI_PBAC
Estado documental: CERTIFIED como research base
Implementación: BLOCKED hasta laboratorio con dispositivo/controlador real
Modo esperado: EDGE_LOCAL salvo prueba contraria
Prioridad: alta, no bloquea MVP ZKTeco
```

Razón:

```text
El PDF Intelligent Security API (Person-Based Access Control) documenta endpoints reales de personas, tarjetas, permisos, puerta remota, eventos, QR events y remote check.
```

### 5.3 Payment provider prioritario

```text
Provider: Mercado Pago
PaymentProviderKey: MERCADO_PAGO
Estado documental: CERTIFIED_FOR_RESEARCH
Implementación: BLOCKED hasta sandbox, OAuth/cuenta conectada y webhooks reales
Prioridad: alta para pagos v1
```

Razón:

```text
Tricor necesita confirmar pagos por API/webhook para restaurar acceso automáticamente.
Cada condominio debe conectar su propia cuenta de cobro.
Tricor no custodia dinero de mantenimiento.
```

### 5.4 Generic Mock Provider

```text
Provider: Generic Mock
ProviderKey: GENERIC_MOCK
Estado: REQUIRED_FOR_QA
Modo: local/test
```

Razón:

```text
El QA Harness debe validar dominio, policy engine, access commands y Edge sin depender de hardware real.
```

---

## 6. ProviderKey canónico

```ts
type AccessProviderKey =
  | 'ZKTECO_CVSECURITY'
  | 'HIKVISION_ISAPI_PBAC'
  | 'GENERIC_MOCK';
```

No usar:

```text
HIKVISION_RESEARCH como provider productivo.
ZK como alias en dominio.
CVSecurity endpoint names en dominio.
Hikvision endpoint names en dominio.
Mercado Pago como AccessProviderKey.
```

Reglas:

```text
- AccessProviderKey puede vivir en provider adapters, ProviderInstallation y QA Harness.
- AccessProviderKey no debe ramificar reglas de dominio.
- PaymentProviderKey vive en pagos, no en access provider contracts.
```

Payment provider key separado:

```ts
type PaymentProviderKey =
  | 'MERCADO_PAGO'
  | 'STRIPE_FUTURE'
  | 'MANUAL_TRANSFER';
```

---

## 7. Modelo de capas

```text
Tricor Domain
    ↓
Policy Engine
    ↓
Access Orchestration
    ↓
Access Provider Contract
    ↓
Provider Adapter
    ↓
Edge Connector
    ↓
Proveedor externo
```

### 7.1 Tricor Domain

Responsable de:

```text
- propiedad
- morosidad
- pagos
- credenciales canónicas
- perfiles canónicos
- zonas canónicas
- estados deseados
- auditoría
```

No conoce endpoints, tokens ni IDs técnicos de proveedor.

### 7.2 Policy Engine

Calcula:

```text
PropertyStatus
+ PaymentStatus
+ MorosityPolicy
+ AccessProfile
+ CredentialState
+ ProviderCapabilityMatrix
= DesiredAccessState
```

### 7.3 Access Orchestration

Convierte estado deseado en comandos:

```text
DesiredAccessState
→ AccessCommand[]
```

### 7.4 Access Provider Contract

Define operaciones canónicas mínimas:

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

### 7.5 Provider Adapter

Traduce contratos canónicos a endpoints de proveedor.

No decide reglas de negocio.

### 7.6 Edge Connector

Ejecuta técnicamente el acceso local:

```text
HTTP local
HTTPS local
Digest/Basic auth
access_token query
SDK/protocolo específico futuro
```

---

## 8. Contrato canónico mínimo de Access Provider

```ts
type AccessProviderAdapter = {
  providerKey: AccessProviderKey;

  validateInstallation(input: ValidateInstallationInput): Promise<ValidationResult>;
  getCapabilities(input: GetCapabilitiesInput): Promise<ProviderCapabilityMatrix>;
  validateMappings(input: ValidateMappingsInput): Promise<MappingValidationResult>;

  applyAccessProfile(input: ApplyAccessProfileInput): Promise<ProviderOperationResult>;
  revokeCredential(input: RevokeCredentialInput): Promise<ProviderOperationResult>;
  restoreCredential(input: RestoreCredentialInput): Promise<ProviderOperationResult>;
  disableCredential(input: DisableCredentialInput): Promise<ProviderOperationResult>;

  openDoor(input: OpenDoorInput): Promise<ProviderOperationResult>;

  fetchObservedState(input: FetchObservedStateInput): Promise<ObservedAccessStateResult>;
  reconcile(input: ReconcileProviderInput): Promise<ReconciliationResult>;
};
```

No todos los proveedores soportan todos los métodos. Por eso la matriz de capacidades es obligatoria.

Regla:

```text
Si capability = UNKNOWN, no se permite ejecutar como si fuera SUPPORTED.
```

---

## 9. ProviderInstallation

Representa una instalación técnica de proveedor conectada a un condominio, privada, caseta o Edge.

```ts
type ProviderInstallation = {
  id: string;
  condominiumId: string;
  privateAreaId?: string;
  guardhouseId?: string;
  edgeNodeId?: string;
  providerKey: AccessProviderKey;
  displayName: string;
  status: ProviderInstallationStatus;
  connectionMode: ProviderConnectionMode;
  capabilitiesVersion: string;
  mappingVersion: string;
  lastHealthCheckAt?: string;
  lastSuccessfulSyncAt?: string;
  createdAt: string;
  updatedAt: string;
};
```

### 9.1 Estados

```ts
type ProviderInstallationStatus =
  | 'DRAFT'
  | 'PENDING_VALIDATION'
  | 'ACTIVE'
  | 'DEGRADED'
  | 'OFFLINE'
  | 'SUSPENDED'
  | 'ARCHIVED';
```

### 9.2 Modos de conexión

```ts
type ProviderConnectionMode =
  | 'EDGE_LOCAL'
  | 'CLOUD_API'
  | 'HYBRID'
  | 'RESEARCH_ONLY';
```

Decisión actual:

```text
ZKTeco / CVSecurity → EDGE_LOCAL
Hikvision ISAPI PBAC → EDGE_LOCAL esperado, LAB_REQUIRED
Generic Mock → local/test
```

---

## 10. ProviderCapabilityMatrix

La matriz de capacidades responde:

```text
¿Qué puede hacer realmente este proveedor en esta instalación concreta?
```

No basta con que el proveedor lo soporte en teoría. Debe estar disponible, configurado y probado para esa instalación.

```ts
type ProviderCapabilityMatrix = {
  providerInstallationId: string;
  providerKey: AccessProviderKey;
  source: 'DOCS' | 'LAB' | 'MOCK' | 'MANUAL_REVIEW';
  version: string;

  personManagement: CapabilitySupport;
  credentialManagement: CapabilitySupport;
  accessProfileManagement: CapabilitySupport;
  accessZoneManagement: CapabilitySupport;
  doorMapping: CapabilitySupport;
  remoteDoorOpen: CapabilitySupport;
  accessEventRead: CapabilitySupport;
  qrEventRead: CapabilitySupport;
  remoteAccessCheck: CapabilitySupport;
  observedStateRead: CapabilitySupport;
  cardOrTagCredentials: CapabilitySupport;
  residentQr: CapabilitySupport;
  guestQr: CapabilitySupport;
  biometricCredentials: CapabilitySupport;
  vehiclePlateAccess: CapabilitySupport;
  offlineSnapshotValidation: CapabilitySupport;
  providerSideReconciliation: CapabilitySupport;

  notes?: string[];
  limitations?: string[];
};
```

### 10.1 CapabilitySupport

```ts
type CapabilitySupport = {
  status: 'SUPPORTED' | 'UNSUPPORTED' | 'PARTIAL' | 'UNKNOWN';
  source: 'DOCS' | 'LAB' | 'MOCK' | 'ASSUMPTION_BLOCKED';
  requiresEdge?: boolean;
  requiresMapping?: boolean;
  requiresHardwareValidation?: boolean;
  requiresManualSetup?: boolean;
  limitations?: string[];
};
```

### 10.2 Reglas

```text
UNKNOWN no se trata como SUPPORTED.
PARTIAL requiere reglas explícitas.
UNSUPPORTED oculta/deshabilita configuración dependiente.
SUPPORTED por documentación todavía puede requerir LAB_REQUIRED.
SUPPORTED por laboratorio puede usarse en provider installation activa.
```

---

## 11. Capability gates por configuración

La UI de configuración no debe mostrar opciones imposibles.

Ejemplos:

```text
Si provider.guestQr = UNSUPPORTED:
- ocultar o deshabilitar configuración de QR invitados dependiente del proveedor.

Si provider.remoteDoorOpen = UNSUPPORTED:
- deshabilitar apertura remota desde Guard.

Si provider.accessProfileManagement = UNSUPPORTED:
- no permitir política de morosidad basada en cambio de perfil.

Si provider.accessEventRead = UNKNOWN:
- no prometer observed state en tiempo real.
```

Regla:

```text
Configuración visible = módulo Tricor activo + capability del proveedor + mapping válido + rol del usuario + estado de instalación.
```

---

## 12. ProviderMapping

ProviderMapping traduce entidades canónicas de Tricor a entidades externas del proveedor.

```ts
type ProviderMapping = {
  id: string;
  providerInstallationId: string;
  mappingType: ProviderMappingType;
  tricorRefType: TricorRefType;
  tricorRefId: string;
  providerRefType: ProviderRefType;
  providerRefId: string;
  providerDisplayName?: string;
  status: ProviderMappingStatus;
  validatedAt?: string;
  lastUsedAt?: string;
  notes?: string;
};
```

### 12.1 Tipos de mapping

```ts
type ProviderMappingType =
  | 'ACCESS_PROFILE'
  | 'ACCESS_ZONE'
  | 'DOOR'
  | 'CREDENTIAL_TYPE'
  | 'PERSON'
  | 'GROUP'
  | 'DEVICE'
  | 'QR_STRATEGY';
```

### 12.2 Estados

```ts
type ProviderMappingStatus =
  | 'DRAFT'
  | 'PENDING_VALIDATION'
  | 'VALID'
  | 'INVALID'
  | 'STALE'
  | 'ARCHIVED';
```

### 12.3 Regla crítica

No se puede activar un proveedor si faltan mappings requeridos.

Mappings mínimos v1:

```text
ACTIVE_ACCESS_PROFILE
MOROSO_DEFAULT_PROFILE
PEDESTRIAN_ACCESS_ZONE
VEHICULAR_ACCESS_ZONE
puertas/casetas operativas
credencial tarjeta/tag, si se usa
estrategia QR, si se usa
```

---

## 13. ProviderExternalRef

Guarda referencias externas sin contaminar entidades principales.

```ts
type ProviderExternalRef = {
  id: string;
  providerInstallationId: string;
  tricorEntityType: TricorEntityType;
  tricorEntityId: string;
  externalEntityType: string;
  externalEntityId: string;
  externalDisplayName?: string;
  status: 'ACTIVE' | 'STALE' | 'REVOKED' | 'ARCHIVED';
  lastSyncedAt?: string;
  createdAt: string;
  updatedAt: string;
};
```

Ejemplos:

```text
Tricor Credential → card id/cardNo externo
Tricor AccessProfile → access level / doorRight / RightPlan externo
Tricor Door → door id externo
Tricor ResponsiblePerson / PropertyAccessSubject → person pin / employeeNo externo
```

---

## 14. Perfiles canónicos de acceso

Perfiles iniciales:

```text
ACTIVE_ACCESS_PROFILE
MOROSO_DEFAULT_PROFILE
PEDESTRIAN_ONLY_PROFILE
REVOKED_ACCESS_PROFILE
TEMPORARY_EXCEPTION_PROFILE
```

### 14.1 ACTIVE_ACCESS_PROFILE

Representa permisos normales de una propiedad al corriente.

No significa “todas las puertas”. Significa:

```text
todos los accesos permitidos por su perfil y configuración.
```

### 14.2 MOROSO_DEFAULT_PROFILE

Default confirmado:

```text
- peatonal permitido
- vehicular bloqueado
- QR invitados bloqueado
- zonas no esenciales bloqueadas
- amenidades futuras bloqueadas
```

### 14.3 PEDESTRIAN_ONLY_PROFILE

Perfil explícito si el proveedor necesita mapping separado para conservar solo acceso peatonal.

### 14.4 REVOKED_ACCESS_PROFILE

Sin accesos operativos.

Uso:

```text
exresidente después de periodo de gracia
credencial perdida
archivo/revocación controlada
```

### 14.5 TEMPORARY_EXCEPTION_PROFILE

Permiso temporal auditado.

Debe tener:

```text
motivo
duración
actor
scope
auditoría
expiración
```

---

## 15. ZKTeco / ZKBio CVSecurity strategy

### 15.1 Estado

```text
ProviderKey: ZKTECO_CVSECURITY
Documentación base: PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
Lab plan: PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
Estado documental: CERTIFIED
Estado implementación: BLOCKED hasta laboratorio
ConnectionMode: EDGE_LOCAL
```

### 15.2 Fuente técnica

```text
ZKBio CVSecurity 3rd Party API User Manual
Version 1.2
Date November 2025
Software Version ZKBio CVSecurity 6.0.0 or above
```

### 15.3 Endpoints core ZKTeco

#### Personas

```text
POST /api/person/add?access_token={token}
GET  /api/person/get/{pin}?access_token={token}
GET  /api/person/get?pin={pin}&access_token={token}
POST /api/person/getPersonList?access_token={token}
POST /api/person/leave?access_token={token}
POST /api/person/reinstated?access_token={token}
POST /api/v2/person/addPersons?access_token={token}
POST /api/v2/person/deleteByPins?access_token={token}&pins={pins}
```

#### Departamentos

```text
POST /api/department/add?access_token={token}
GET  /api/department/get?code={code}&access_token={token}
POST /api/department/getDepartmentList?pageNo={pageNo}&pageSize={pageSize}&access_token={token}
```

#### Tarjetas

```text
GET  /api/card/getCards?pin={pin}&access_token={token}
POST /api/card/set?access_token={token}
```

#### Puertas / lectores / apertura remota

```text
GET  /api/door/list?pageNo={pageNo}&pageSize={pageSize}&access_token={token}
GET  /api/reader/list?pageNo={pageNo}&pageSize={pageSize}&access_token={token}
POST /api/door/remoteOpenById?doorId={id}&interval={seconds}&access_token={token}
POST /api/door/remoteOpenByName?doorName={name}&interval={seconds}&access_token={token}
```

#### Niveles de acceso

```text
GET  /api/accLevel/list?pageNo={pageNo}&pageSize={pageSize}&access_token={token}
POST /api/accLevel/addLevel?access_token={token}
POST /api/accLevel/addLevelDoor?access_token={token}
POST /api/accLevel/addLevelPerson?access_token={token}
```

#### Transacciones / observed state

```text
GET/POST /api/transaction/list?access_token={token}
GET/POST /api/transaction/getDoorTransactionDetail?access_token={token}
```

#### Entrance Control / PSG

```text
/api/psgDevice/*
/api/psgGate/*
/api/psgLevel/*
/api/psgReader/*
/api/psgTransaction/*
```

Estado actual PSG:

```text
LAB_RESEARCH / NOT CORE_V1 until actual gate/pluma is configured
```

#### Visitor / QR

```text
/api/visRegistration/*
/api/person/getQrCode/{pin}
/api/v2/person/getQrCode?pin={pin}
```

### 15.4 Mapeo canónico ZKTeco

| Tricor | ZKTeco / CVSecurity |
|---|---|
| PropertyAccessSubject | `person.pin` |
| Responsible display name | `person.name` / `lastName` |
| PrivateArea / hierarchy | `department.code` / `parentCode` |
| Credential card/tag | `cardNo`, `supplyCards`, `/api/card/set` |
| ActiveAccessProfile | `accLevelIds` / `accLevel` |
| MorosoAccessProfile | `accLevel` restringido |
| Door | `doorId` / `doorName` |
| Reader | `reader` list |
| Manual open | `door/remoteOpenById` or `door/remoteOpenByName` |
| Observed events | `transaction` endpoints |
| Guest QR | visitor registration / QR endpoints |

### 15.5 Reglas ZKTeco

```text
- Edge local obligatorio.
- access_token vive en Edge secret storage.
- success code default: 0, pero se debe confirmar en laboratorio.
- Algunos endpoints/documentación pueden variar entre access_token y acc_token; no implementar variantes sin laboratorio.
- Parking, elevator, biometría y video quedan fuera de v1.
- PSG queda como research si no hay gate/pluma configurado.
```

---

## 16. Hikvision ISAPI Person-Based Access Control strategy

### 16.1 Estado

```text
ProviderKey: HIKVISION_ISAPI_PBAC
Documentación base: PROVIDER_RESEARCH_HIKVISION_V0.1.md
Estado documental: CERTIFIED como research base
Estado implementación: BLOCKED hasta laboratorio
ConnectionMode esperado: EDGE_LOCAL
```

### 16.2 Fuente técnica

```text
Intelligent Security API (Person-Based Access Control) Developer Guide
```

La documentación de metadata/video queda degradada:

```text
PARTIAL / NOT AUTHORITATIVE FOR ACCESS CONTROL
```

### 16.3 Endpoints core Hikvision

#### Capabilities

```text
GET /ISAPI/AccessControl/capabilities
GET /ISAPI/AccessControl/AcsCfg/capabilities?format=json
GET /ISAPI/AccessControl/AcsCfg?format=json
```

#### Personas / usuarios

```text
GET  /ISAPI/AccessControl/UserInfo/capabilities?format=json
GET  /ISAPI/AccessControl/UserInfo/Count?format=json
PUT  /ISAPI/AccessControl/UserInfo/Delete?format=json
PUT  /ISAPI/AccessControl/UserInfo/Modify?format=json
POST /ISAPI/AccessControl/UserInfo/Record?format=json
POST /ISAPI/AccessControl/UserInfo/Search?format=json
PUT  /ISAPI/AccessControl/UserInfo/SetUp?format=json
GET  /ISAPI/AccessControl/UserInfoDetail/Delete/capabilities?format=json
PUT  /ISAPI/AccessControl/UserInfoDetail/Delete?format=json
GET  /ISAPI/AccessControl/UserInfoDetail/DeleteProcess?format=json
```

#### Tarjetas / credenciales

```text
GET  /ISAPI/AccessControl/CardInfo/capabilities?format=json
GET  /ISAPI/AccessControl/CardInfo/Count?format=json
PUT  /ISAPI/AccessControl/CardInfo/Delete?format=json
PUT  /ISAPI/AccessControl/CardInfo/Modify?format=json
POST /ISAPI/AccessControl/CardInfo/Record?format=json
POST /ISAPI/AccessControl/CardInfo/Search?format=json
PUT  /ISAPI/AccessControl/CardInfo/SetUp?format=json
```

#### Puerta / apertura remota

```text
GET /ISAPI/AccessControl/RemoteControl/door/capabilities
PUT /ISAPI/AccessControl/RemoteControl/door/<ID>
```

#### Eventos / observed state

```text
GET  /ISAPI/AccessControl/AcsEvent/capabilities?format=json
POST /ISAPI/AccessControl/AcsEvent?format=json
GET  /ISAPI/AccessControl/QRCodeEvent/capabilities?format=json
POST /ISAPI/AccessControl/QRCodeEvent?format=json
GET  /ISAPI/AccessControl/remoteCheck/capabilities?format=json
PUT  /ISAPI/AccessControl/remoteCheck?format=json
```

### 16.4 Mapeo canónico Hikvision

| Tricor | Hikvision ISAPI PBAC |
|---|---|
| AccessIdentity / PropertyAccessSubject | `UserInfo.employeeNo` |
| Responsible name | `UserInfo.name` |
| Credential card/tag | `CardInfo.cardNo + employeeNo` |
| AccessProfile | `UserInfo.doorRight + UserInfo.RightPlan` |
| AccessZone / Door | `doorRight` door/lock ID |
| Schedule / allowed time | `RightPlan` |
| Manual open | `RemoteControl/door/<ID>` |
| Observed access event | `AcsEvent` |
| QR scan event | `QRCodeEvent` |
| Remote validation | `remoteCheck` |
| Exresident cleanup | `UserInfoDetail/Delete` or strategy selected by lab |

### 16.5 Decisión central Hikvision

Hikvision no usa el mismo modelo que ZKTeco `accLevel`.

Mapping principal:

```text
Tricor AccessProfile
→ Hikvision UserInfo.doorRight
→ Hikvision UserInfo.RightPlan
```

Ejemplo conceptual:

```text
ACTIVE_ACCESS_PROFILE
→ doorRight: puertas permitidas
→ RightPlan: plan activo

MOROSO_DEFAULT_PROFILE
→ doorRight: solo puerta peatonal
→ RightPlan: plan permitido

REVOKED_ACCESS_PROFILE
→ estrategia a definir en laboratorio:
   - doorRight vacío
   - Valid.endTime vencido
   - CardInfo/Delete
   - UserInfoDetail/Delete
```

### 16.6 Reglas Hikvision

```text
- No copiar modelo ZKTeco accLevel a Hikvision.
- No asumir que Hikvision tiene perfiles equivalentes a ZKTeco.
- No usar UserInfoDetail/Delete sin laboratorio; es destructivo.
- No usar RemoteControl/door en producción sin hardware smoke.
- No asumir Cloud directo; usar Edge local hasta prueba contraria.
- QRCodeEvent no significa generación de QR por Tricor; es evento de lectura/escaneo.
```

---

## 17. Mercado Pago boundary

### 17.1 Estado

```text
Provider: Mercado Pago
PaymentProviderKey: MERCADO_PAGO
Documento: PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
Estado documental: CERTIFIED_FOR_RESEARCH
Implementación: BLOCKED hasta sandbox/OAuth/webhooks
```

### 17.2 Rol en Tricor

Mercado Pago confirma pagos.

No controla acceso.

Flujo:

```text
Mercado Pago webhook
→ Tricor valida firma
→ Tricor consulta order/payment server-side
→ Tricor marca pago confirmado
→ Policy Engine recalcula acceso
→ AccessCommand
→ Edge
→ Access Provider
```

### 17.3 Reglas

```text
- No mezclar dinero de mantenimiento con ingresos SaaS.
- Cada condominio debe conectar su propia cuenta.
- Transferencia manual existe como fallback auditado.
- Webhook no basta; Tricor debe consultar API antes de confirmar pago.
- Mercado Pago no pertenece a AccessProviderAdapter.
```

---

## 18. AccessCommand

Comando canónico generado por Tricor para ejecución por Edge/provider.

```ts
type AccessCommand = {
  id: string;
  commandType: AccessCommandType;
  condominiumId: string;
  privateAreaId?: string;
  propertyId: string;
  credentialId?: string;
  providerInstallationId: string;
  providerKey: AccessProviderKey;
  desiredStateId: string;
  idempotencyKey: string;
  status: AccessCommandStatus;
  payload: CanonicalAccessCommandPayload;
  issuedAt: string;
  expiresAt?: string;
  completedAt?: string;
};
```

### 18.1 Command types

```ts
type AccessCommandType =
  | 'APPLY_ACCESS_PROFILE'
  | 'RESTRICT_ACCESS'
  | 'RESTORE_ACCESS'
  | 'REVOKE_CREDENTIAL'
  | 'DISABLE_CREDENTIAL'
  | 'OPEN_DOOR'
  | 'FETCH_OBSERVED_STATE'
  | 'RECONCILE_PROVIDER';
```

### 18.2 Command status

```ts
type AccessCommandStatus =
  | 'PENDING'
  | 'SENT_TO_EDGE'
  | 'EDGE_RECEIVED'
  | 'APPLIED'
  | 'APPLIED_UNCONFIRMED'
  | 'CONFIRMED'
  | 'FAILED_RETRYABLE'
  | 'FAILED_PERMANENT'
  | 'BLOCKED_MAPPING_REQUIRED'
  | 'BLOCKED_CAPABILITY_UNSUPPORTED'
  | 'EXPIRED'
  | 'CANCELLED';
```

---

## 19. DesiredAccessState

Estado calculado por Tricor.

```ts
type DesiredAccessState = {
  id: string;
  propertyId: string;
  accessProfile: CanonicalAccessProfile;
  allowedZones: AccessZoneKey[];
  deniedZones: AccessZoneKey[];
  credentialStates: CredentialDesiredState[];
  reason: DesiredAccessReason;
  source: 'PAYMENT_CONFIRMED' | 'MOROSITY' | 'MANUAL_PAYMENT_APPROVED' | 'MOVE_OUT' | 'LOST_CREDENTIAL' | 'TEMPORARY_EXCEPTION' | 'ADMIN_CONFIG_CHANGE';
  computedAt: string;
  expiresAt?: string;
};
```

### 19.1 Reasons

```ts
type DesiredAccessReason =
  | 'PROPERTY_CURRENT'
  | 'PROPERTY_MOROSE'
  | 'PAYMENT_RESTORED'
  | 'MANUAL_PAYMENT_RESTORED'
  | 'CREDENTIAL_LOST'
  | 'MOVE_OUT_GRACE_ENDED'
  | 'TEMPORARY_EXCEPTION_ACTIVE'
  | 'ADMIN_OVERRIDE_AUDITED';
```

---

## 20. ObservedAccessState

Estado observado desde proveedor o Edge.

```ts
type ObservedAccessState = {
  id: string;
  propertyId: string;
  providerInstallationId: string;
  providerKey: AccessProviderKey;
  observedProfile?: string;
  observedCredentials?: ObservedCredential[];
  observedZones?: ObservedZone[];
  lastProviderEventAt?: string;
  source: 'PROVIDER_API' | 'EDGE_CACHE' | 'TRANSACTION_EVENT' | 'MANUAL_OBSERVATION' | 'UNKNOWN';
  confidence: 'HIGH' | 'MEDIUM' | 'LOW' | 'UNKNOWN';
  observedAt: string;
};
```

Regla:

```text
ObservedAccessState no sustituye a DesiredAccessState.
Sirve para detectar drift, confirmar aplicación o generar incidentes.
```

---

## 21. ProviderOperationResult

Resultado normalizado de una operación del provider.

```ts
type ProviderOperationResult = {
  ok: boolean;
  providerKey: AccessProviderKey;
  providerInstallationId: string;
  operation: ProviderOperationName;
  providerRequestId?: string;
  providerStatusCode?: string | number;
  normalizedStatus:
    | 'SUCCESS'
    | 'SUCCESS_UNCONFIRMED'
    | 'RETRYABLE_ERROR'
    | 'PERMANENT_ERROR'
    | 'UNSUPPORTED_CAPABILITY'
    | 'MAPPING_REQUIRED'
    | 'AUTH_FAILED'
    | 'NETWORK_ERROR'
    | 'UNKNOWN_ERROR';
  rawResponseRef?: string;
  errorClass?: ProviderErrorClass;
  message?: string;
};
```

### 21.1 Error classes

```ts
type ProviderErrorClass =
  | 'AUTH_INVALID'
  | 'TOKEN_EXPIRED'
  | 'NETWORK_TIMEOUT'
  | 'TLS_ERROR'
  | 'MAPPING_NOT_FOUND'
  | 'PERSON_NOT_FOUND'
  | 'CREDENTIAL_NOT_FOUND'
  | 'DOOR_NOT_FOUND'
  | 'ACCESS_PROFILE_NOT_FOUND'
  | 'UNSUPPORTED_OPERATION'
  | 'PROVIDER_REJECTED'
  | 'PROVIDER_INTERNAL_ERROR'
  | 'UNKNOWN';
```

---

## 22. Revocación y restauración canónica

### 22.1 Morosidad

```text
Property becomes MOROSE
→ Policy Engine computes MOROSO_DEFAULT_PROFILE
→ DesiredAccessState created
→ AccessCommand.RESTRICT_ACCESS
→ Provider adapter applies external mapping
→ ObservedAccessState fetched if supported
→ AuditEvent created
```

### 22.2 Pago confirmado

```text
Payment provider confirms payment
→ Payment status CONFIRMED
→ Debt recalculated
→ Policy Engine computes ACTIVE_ACCESS_PROFILE
→ AccessCommand.RESTORE_ACCESS
→ Edge/provider applies access
→ AuditEvent created
```

### 22.3 Transferencia manual fallback

```text
Finance/CondoAdmin approves manual transfer
→ Payment status APPROVED_MANUAL
→ Policy Engine recalculates access
→ Restore workflow
→ AuditEvent created
```

### 22.4 Tarjeta perdida

```text
Resident reports credential lost
→ Credential status LOST_REPORTED
→ AccessCommand.REVOKE_CREDENTIAL or DISABLE_CREDENTIAL
→ Provider adapter revokes/disables external credential
→ AuditEvent created
```

### 22.5 Exresidente / mudanza

```text
MoveOut grace period ends
→ PropertyResponsibility becomes FORMER/ARCHIVED
→ related credentials evaluated
→ AccessCommand.REVOKE_CREDENTIAL or APPLY REVOKED profile
→ provider cleanup strategy selected by adapter/lab result
→ AuditEvent created
```

---

## 23. Provider-specific translation examples

### 23.1 Tricor → ZKTeco

```text
ACTIVE_ACCESS_PROFILE
→ accLevelIds = Pagador/external active level

MOROSO_DEFAULT_PROFILE
→ accLevelIds = Moroso/restricted level

OPEN_DOOR
→ /api/door/remoteOpenById

Observed events
→ /api/transaction/list or /api/transaction/getDoorTransactionDetail
```

### 23.2 Tricor → Hikvision

```text
ACTIVE_ACCESS_PROFILE
→ UserInfo.doorRight = allowed door list
→ UserInfo.RightPlan = active schedule plan

MOROSO_DEFAULT_PROFILE
→ UserInfo.doorRight = pedestrian-only door list
→ UserInfo.RightPlan = allowed schedule

OPEN_DOOR
→ PUT /ISAPI/AccessControl/RemoteControl/door/<ID>

Observed events
→ POST /ISAPI/AccessControl/AcsEvent?format=json
```

---

## 24. QR strategy

Tricor debe distinguir dos tipos de QR:

```text
Tricor-generated QR
Provider-generated QR
```

### 24.1 Resident QR

Puede ser:

```text
- QR generado por Tricor y validado por Tricor/Edge.
- QR generado por proveedor si el provider lo soporta.
```

### 24.2 Guest QR

Puede ser:

```text
- Tricor-generated guest QR.
- Provider visitor QR si el proveedor lo soporta y laboratorio lo confirma.
```

### 24.3 Reglas

```text
- No asumir que QRCodeEvent equivale a generación de QR.
- QRCodeEvent puede ser solo evento de lectura/escaneo.
- Si Tricor genera QR, el provider debe poder abrir puerta o Edge debe poder validar y ejecutar apertura.
- Si provider genera QR, debe existir mapping y reglas de expiración.
```

---

## 25. Remote open strategy

Remote open es operación física sensible.

Reglas:

```text
- Solo GuardSupervisor o actor autorizado.
- Requiere motivo rápido.
- Requiere auditoría.
- Requiere ProviderCapabilityMatrix.remoteDoorOpen = SUPPORTED por LAB.
- Nunca se habilita solo por documentación.
- Debe existir hardware smoke test.
```

Provider translations:

```text
ZKTeco: /api/door/remoteOpenById or /api/door/remoteOpenByName
Hikvision: /ISAPI/AccessControl/RemoteControl/door/<ID>
```

---

## 26. Reconciliación y drift

El provider adapter debe permitir reconciliación cuando sea posible.

### 26.1 Drift cases

```text
Tricor cree que perfil es ACTIVE, proveedor tiene MOROSO.
Tricor cree que tarjeta está revocada, proveedor la mantiene activa.
Proveedor contiene persona/credencial que Tricor no conoce.
Edge aplicó comando, pero Cloud no recibió confirmación.
```

### 26.2 Reglas

```text
- Drift no debe arreglarse silenciosamente sin auditoría.
- QA Harness debe tener pruebas de drift.
- Provider adapter debe reportar UNKNOWN si no puede observar estado.
- Observed state con confianza LOW no debe marcar comando como CONFIRMED.
```

---

## 27. Seguridad

### 27.1 Secretos

```text
- access_token ZKTeco no se registra en logs.
- credenciales Digest/Basic Hikvision no se registran en logs.
- Mercado Pago access token no se registra en logs.
- Secrets viven referenciados por secretRef.
```

### 27.2 Tenant isolation

```text
Un provider installation pertenece a un condominio.
Un Edge no debe operar provider installations de otro condominio.
Un mapping no debe cruzar tenants.
```

### 27.3 Operaciones destructivas

Requieren laboratorio y confirmación:

```text
ZKTeco delete person / bulk delete
Hikvision UserInfoDetail/Delete
remote open de puerta
revocación masiva
cambio de mapping activo
```

---

## 28. QA requirements para Provider Strategy

Este documento no actualiza QA Harness, pero define lo que QA debe cubrir después.

### 28.1 Provider-neutral tests

```text
qa:provider:capability-matrix
qa:provider:mapping-validation
qa:provider:apply-access-profile
qa:provider:revoke-credential
qa:provider:restore-credential
qa:provider:remote-open-capability-gate
qa:provider:observed-state
qa:provider:drift-detection
```

### 28.2 ZKTeco provider tests

```text
qa:provider:zkteco:person
qa:provider:zkteco:card
qa:provider:zkteco:access-level
qa:provider:zkteco:door
qa:provider:zkteco:transaction
qa:provider:zkteco:visitor-qr
qa:provider:zkteco:remote-open-lab-only
```

### 28.3 Hikvision provider tests

```text
qa:provider:hikvision:user-info
qa:provider:hikvision:card-info
qa:provider:hikvision:door-right-right-plan
qa:provider:hikvision:remote-control-door-lab-only
qa:provider:hikvision:acs-event
qa:provider:hikvision:qr-code-event
qa:provider:hikvision:remote-check
```

### 28.4 Payment tests to coordinate

Payment provider tests live in payment QA, but must trigger access workflow tests:

```text
qa:payments:mercado-pago:webhook-signature
qa:payments:mercado-pago:get-payment-or-order
qa:flow:payment-confirmed-restores-access
qa:flow:payment-confirmed-edge-offline
```

---

## 29. Provider activation rules

Un provider installation solo puede pasar a ACTIVE si cumple:

```text
1. ProviderInstallation configurada.
2. Edge asignado si connectionMode = EDGE_LOCAL.
3. Credentials/secretRefs válidos.
4. Capabilities consultadas o cargadas desde lab.
5. Required mappings completos.
6. QA provider-contract PASS.
7. No existen BLOCKED mappings.
8. Remote open deshabilitado si no pasó hardware smoke.
9. Observed state marcado como PARTIAL si no hay lectura confiable de eventos.
```

---

## 30. Estados por proveedor

| Provider | Documento base | Estado documental | Estado implementación | Siguiente paso |
|---|---|---:|---:|---|
| ZKTeco / CVSecurity | `PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md` | CERTIFIED | BLOCKED hasta lab PASS | ejecutar lab plan cuando haya entorno |
| Hikvision ISAPI PBAC | `PROVIDER_RESEARCH_HIKVISION_V0.1.md` | CERTIFIED | BLOCKED hasta lab | crear lab plan futuro si se decide avanzar |
| Mercado Pago | `PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md` | CERTIFIED_FOR_RESEARCH | BLOCKED hasta sandbox/OAuth/webhook | actualizar QA Harness |
| Generic Mock | requerido por QA | REQUIRED | pendiente de implementación | implementar antes de providers reales |

---

## 31. Archivos dependientes recomendados

Este documento exige actualizar después, uno por uno:

```text
1. QA_HARNESS_SPEC_V0.1.md
2. ROADMAP_V0.1.md
3. AI_AGENT_RULES_AND_HANDOFF.md
4. README.md
```

No se actualizan en esta tarea.

---

## 32. Reglas para Claude Code CLI

Claude Code CLI debe seguir estas reglas cuando implemente providers:

```text
1. No implementar provider real si ProviderCapabilityMatrix no está definida.
2. No implementar remote open sin hardware-smoke plan.
3. No copiar mapping ZKTeco hacia Hikvision.
4. No tratar Mercado Pago como AccessProvider.
5. No guardar secretos en código, docs ni logs.
6. No exponer endpoints de proveedor en UI normal.
7. No usar provider fields dentro de packages/domain.
8. No declarar DONE sin QA Harness.
9. No ejecutar operaciones destructivas sin flag explícito y ambiente lab.
10. No asumir que DOCUMENTED = AVAILABLE en la instalación real.
```

---

## 33. Estado final del documento

```text
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
Estado: CERTIFIED
Implementación provider real: BLOCKED hasta QA/laboratorio
Siguiente archivo recomendado: QA_HARNESS_SPEC_V0.1.md
```

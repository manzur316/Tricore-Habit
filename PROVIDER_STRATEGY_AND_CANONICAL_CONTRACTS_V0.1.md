# PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** Draft formal inicial / fuente de verdad provisional para estrategia de proveedores y contratos canónicos  
**Fecha:** 2026-06-10  
**Documentos padre:**  
- `PROJECT_CHARTER_TRICOR_HABITAT.md`
- `DOMAIN_MODEL_V0.1.md`
- `ACCESS_POLICY_CONFIGURATION_V0.1.md`
- `CLOUD_EDGE_ARCHITECTURE_V0.1.md`
- `QA_HARNESS_SPEC_V0.1.md`
- `ROADMAP_V0.1.md`
- `REPO_STRUCTURE_V0.1.md`
- `PROVIDER_RESEARCH_HIKVISION_V0.1.md`

**Preparado para:** arquitectura, backend, Edge, provider adapters, QA Harness, Tricor Condo, Tricor Platform, documentación histórica y handoff posterior a Claude Code CLI  
**Idioma base:** Español  

---

## 1. Propósito del documento

Este documento define la estrategia oficial de integración con proveedores de control de acceso para Tricor Hábitat y los contratos canónicos que deben proteger el dominio del producto.

Su objetivo es evitar que Tricor Hábitat vuelva a depender de estructuras internas, nombres, rutas, permisos o payloads específicos de un proveedor.

La regla central es:

```text
Tricor Hábitat define el dominio.
El proveedor ejecuta capacidades externas.
El adapter traduce entre ambos mundos.
```

Este documento debe servir como fuente de verdad para:

```text
- diseño de provider adapters
- ProviderCapabilityMatrix
- ProviderInstallation
- ProviderMapping
- provider contracts
- QA Harness
- Edge
- Policy Engine
- Tricor Condo → Configuración → Proveedor
- Tricor Platform → soporte técnico y monitoreo
- handoff a Claude Code CLI
```

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

El proveedor puede hablar en otros términos:

```text
persona externa
tarjeta externa
nivel de acceso externo
puerta externa
grupo externo
device externo
evento externo
endpoint externo
payload externo
```

Pero esos términos solo pueden existir en:

```text
- provider adapters
- ProviderMapping
- ProviderExternalRef
- ProviderCapabilityMatrix
- provider-specific tests
- Edge connector
```

No deben contaminar:

```text
- packages/domain
- políticas de negocio
- modelos principales de Tricor
- UI de usuario normal
- reglas de morosidad
- reglas de pagos
```

---

## 3. Decisión estratégica v0.1

### 3.1 Proveedor implementado en v1

El primer proveedor operativo de acceso será:

```text
ZKTeco / CVSecurity
```

Decisión:

```text
Estado: v1 implementation target
Modo esperado: Edge obligatorio
Prioridad: alta
```

Razón:

```text
Es el primer proveedor conocido por el proyecto y se considera el objetivo inicial para validar el flujo real:
pagos → morosidad → perfil de acceso → Edge → proveedor → estado observado.
```

### 3.2 Proveedor en investigación prioritaria

Proveedor:

```text
Hikvision / Hik-Connect / ISAPI / HikCentral Professional OpenAPI
```

Decisión:

```text
Estado: research track
Modo esperado: por definir con laboratorio
Prioridad: alta, pero no bloquea MVP
```

Hikvision no debe implementarse en producción hasta cerrar:

```text
- superficie oficial de integración
- capacidad real de permisos
- capacidad real de puerta remota
- capacidad real de eventos
- capacidad real de personas/credenciales
- necesidad o no de Edge
- QA Harness provider contract
- hardware/sandbox de laboratorio
```

### 3.3 Proveedores futuros

Otros proveedores solo pueden entrar después de que el contrato canónico esté probado con el primer proveedor real.

No se permite agregar proveedores por atajos específicos.

Regla:

```text
Nuevo proveedor = nueva ProviderCapabilityMatrix + nuevo adapter + nuevos provider contract tests.
```

---

## 4. Qué problema resuelve esta estrategia

Sin esta estrategia, cada proveedor puede empujar al sistema a hablar su propio idioma.

Eso causaría:

```text
- lógica duplicada por proveedor
- reglas de morosidad diferentes por proveedor
- UI llena de nombres técnicos externos
- permisos incompatibles
- mapeos frágiles
- comandos no auditables
- testing imposible
- dependencia excesiva de un proveedor
- parches acumulados
```

Con contratos canónicos, Tricor puede decir:

```text
Propiedad A-101 está morosa.
Aplicar perfil MOROSO_DEFAULT_PROFILE.
Bloquear vehicular.
Bloquear QR invitados.
Conservar peatonal.
```

Y cada adapter decide cómo traducirlo.

---

## 5. Modelo de capas

Arquitectura oficial:

```text
Tricor Domain
    ↓
Policy Engine
    ↓
Access Orchestration
    ↓
Provider Contract
    ↓
Provider Adapter
    ↓
Edge Connector / Cloud Connector
    ↓
Proveedor externo
```

### 5.1 Tricor Domain

Responsable de:

```text
- propiedad
- morosidad
- pagos
- credenciales
- perfiles canónicos
- zonas canónicas
- estados deseados
- auditoría
```

No sabe cómo se llama una puerta en el proveedor.

### 5.2 Policy Engine

Responsable de calcular:

```text
PropertyStatus
+ PaymentStatus
+ MorosityPolicy
+ AccessProfile
+ CredentialState
+ ProviderCapabilityMatrix
= DesiredAccessState
```

### 5.3 Access Orchestration

Responsable de convertir estados deseados en comandos:

```text
DesiredAccessState
→ AccessCommand[]
```

### 5.4 Provider Contract

Define operaciones canónicas mínimas:

```text
- validar instalación
- listar capacidades
- validar mappings
- aplicar perfil de acceso
- revocar credencial
- restaurar credencial
- abrir puerta remotamente, si está soportado
- leer estado observado
- reconciliar datos
```

### 5.5 Provider Adapter

Traduce contratos canónicos a proveedor específico.

No decide reglas de negocio.

### 5.6 Edge Connector / Cloud Connector

Ejecuta la comunicación técnica:

```text
HTTP local
API externa
SDK
servicio local
protocolo específico
```

El tipo de conector depende del proveedor y de la instalación.

---

## 6. Contrato canónico mínimo de proveedor

Todo proveedor debe implementar una interfaz conceptual.

```ts
type AccessProviderAdapter = {
  providerKey: ProviderKey;
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

No todos los métodos están disponibles para todos los proveedores.

Por eso cada adapter debe declarar capacidades reales.

---

## 7. ProviderKey

```ts
type ProviderKey =
  | 'ZKTECO_CVSECURITY'
  | 'HIKVISION_RESEARCH'
  | 'GENERIC_MOCK';
```

Reglas:

```text
- providerKey no debe aparecer en lógica de dominio.
- providerKey sí puede aparecer en provider adapter, ProviderInstallation, QA Harness y configuración técnica.
- providerKey no debe usarse para saltarse ProviderCapabilityMatrix.
```

---

## 8. ProviderInstallation

Representa una instalación técnica de proveedor conectada a un condominio, privada, caseta o Edge.

```ts
type ProviderInstallation = {
  id: string;
  condominiumId: string;
  privateAreaId?: string;
  edgeNodeId?: string;
  providerKey: ProviderKey;
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

### 8.1 Estados

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

### 8.2 Modos de conexión

```ts
type ProviderConnectionMode =
  | 'EDGE_LOCAL'
  | 'CLOUD_API'
  | 'HYBRID'
  | 'RESEARCH_ONLY';
```

Decisión v0.1:

```text
ZKTeco / CVSecurity → EDGE_LOCAL
Hikvision → RESEARCH_ONLY hasta cerrar investigación
Generic Mock → local/test
```

---

## 9. ProviderCapabilityMatrix

La matriz de capacidades es obligatoria.

Su función es responder:

```text
¿Qué puede hacer realmente este proveedor en esta instalación concreta?
```

No basta con que el proveedor lo soporte en teoría. Debe estar disponible para esa instalación.

```ts
type ProviderCapabilityMatrix = {
  providerInstallationId: string;
  providerKey: ProviderKey;
  version: string;
  personManagement: CapabilitySupport;
  credentialManagement: CapabilitySupport;
  accessProfileManagement: CapabilitySupport;
  accessZoneManagement: CapabilitySupport;
  doorMapping: CapabilitySupport;
  remoteDoorOpen: CapabilitySupport;
  accessEventRead: CapabilitySupport;
  observedStateRead: CapabilitySupport;
  cardOrTagCredentials: CapabilitySupport;
  residentQr: CapabilitySupport;
  guestQr: CapabilitySupport;
  biometricCredentials: CapabilitySupport;
  vehiclePlateAccess: CapabilitySupport;
  offlineSnapshotValidation: CapabilitySupport;
  providerSideReconciliation: CapabilitySupport;
  notes?: string;
};
```

### 9.1 CapabilitySupport

```ts
type CapabilitySupport = {
  status: 'SUPPORTED' | 'UNSUPPORTED' | 'PARTIAL' | 'UNKNOWN';
  requiresEdge?: boolean;
  requiresMapping?: boolean;
  requiresHardwareValidation?: boolean;
  requiresManualSetup?: boolean;
  limitations?: string[];
};
```

### 9.2 Reglas

```text
UNKNOWN no se trata como SUPPORTED.
PARTIAL requiere reglas explícitas.
UNSUPPORTED debe ocultar o deshabilitar configuración dependiente.
SUPPORTED debe estar probado por QA Harness antes de producción.
```

---

## 10. Capability gates por configuración

La UI de configuración no debe mostrar opciones imposibles.

Ejemplos:

```text
Si provider.guestQr = UNSUPPORTED:
- ocultar o deshabilitar configuración de QR invitados dependiente del proveedor.
- mantener QR interno solo si Tricor puede validarlo sin proveedor.

Si provider.remoteDoorOpen = UNSUPPORTED:
- deshabilitar apertura remota desde Guard.
- permitir solo registro de evento manual, si aplica.

Si provider.accessProfileManagement = UNSUPPORTED:
- no permitir política de morosidad basada en cambio de perfil.
- exigir estrategia alternativa validada o marcar instalación incompatible.

Si provider.accessEventRead = UNKNOWN:
- no prometer estado observado en tiempo real.
- usar reconciliación manual o smoke test.
```

Regla general:

```text
Configuración visible = módulo Tricor activo + capability del proveedor + rol del usuario + estado de instalación.
```

---

## 11. ProviderMapping

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

### 11.1 Tipos de mapping

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

### 11.2 Estados

```ts
type ProviderMappingStatus =
  | 'DRAFT'
  | 'PENDING_VALIDATION'
  | 'VALID'
  | 'INVALID'
  | 'STALE'
  | 'ARCHIVED';
```

### 11.3 Regla crítica

No se puede activar un proveedor si faltan mappings requeridos.

Mappings mínimos v1:

```text
- ACTIVE_ACCESS_PROFILE
- MOROSO_DEFAULT_PROFILE
- PEDESTRIAN_ACCESS_ZONE
- VEHICULAR_ACCESS_ZONE
- puertas/casetas operativas
- credencial tarjeta/tag, si se usa
- estrategia QR, si se usa
```

---

## 12. ProviderExternalRef

Tabla o estructura para guardar referencias externas sin contaminar entidades principales.

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
Tricor Credential → proveedor card id externo
Tricor AccessProfile → proveedor access level externo
Tricor Door → proveedor door id externo
Tricor ResponsiblePerson → proveedor person id externo
```

---

## 13. Contratos canónicos de perfiles de acceso

Perfiles canónicos iniciales:

```ts
type AccessProfileKey =
  | 'ACTIVE_ACCESS_PROFILE'
  | 'MOROSO_DEFAULT_PROFILE'
  | 'FORMER_RESIDENT_PROFILE'
  | 'TEMPORARY_EXCEPTION_PROFILE'
  | 'GUEST_PROFILE'
  | 'STAFF_PROFILE_FUTURE';
```

### 13.1 ACTIVE_ACCESS_PROFILE

Estado normal de una propiedad al corriente.

Incluye todas las capacidades permitidas por su perfil de propiedad.

No significa acceso universal.

### 13.2 MOROSO_DEFAULT_PROFILE

Default aprobado:

```text
- peatonal permitido
- vehicular bloqueado
- QR invitados bloqueado
- zonas no esenciales bloqueadas
- amenidades futuras bloqueadas
```

Puede configurarse por condominio, pero debe respetar capability matrix.

### 13.3 FORMER_RESIDENT_PROFILE

Para exresidentes o propietarios dados de baja.

```text
- sin acceso permanente
- credenciales revocadas o archivadas
- historial conservado
```

### 13.4 TEMPORARY_EXCEPTION_PROFILE

Para excepción temporal auditada.

```text
- duración limitada
- motivo obligatorio
- responsable obligatorio
- notificación a CondoAdmin
- audit log obligatorio
```

### 13.5 GUEST_PROFILE

Para visitantes.

```text
- temporal
- limitado por QR
- ventana de tiempo
- uso único o reglas configuradas
```

---

## 14. Contratos canónicos de zonas de acceso

Zonas iniciales:

```ts
type AccessZoneKey =
  | 'PEDESTRIAN'
  | 'VEHICULAR'
  | 'RESIDENT_QR'
  | 'GUEST_QR'
  | 'AMENITY_FUTURE'
  | 'SERVICE_FUTURE'
  | 'TECHNICAL_RESTRICTED';
```

### 14.1 PEDESTRIAN

Acceso peatonal.

Default en morosidad:

```text
permitido si el condominio conserva peatonal.
```

### 14.2 VEHICULAR

No implica módulo de vehículos.

Significa zona/capacidad de acceso vehicular.

Default en morosidad:

```text
bloqueado.
```

### 14.3 RESIDENT_QR

QR propio del residente/responsable.

Puede bloquearse por morosidad si la política lo configura.

### 14.4 GUEST_QR

QR de invitados.

Default en morosidad:

```text
bloqueado.
```

### 14.5 AMENITY_FUTURE

Zona futura, no módulo v1.

Debe existir como concepto para que morosidad pueda bloquear zonas no esenciales en el futuro.

---

## 15. Contrato canónico de credenciales

Credencial representa medio de acceso autorizado ligado a una propiedad/responsable.

```ts
type AccessCredential = {
  id: string;
  propertyId: string;
  responsiblePersonId: string;
  credentialType: CredentialType;
  status: CredentialStatus;
  label?: string;
  providerExternalRefs: ProviderExternalRef[];
  lastUsedAt?: string;
  issuedAt: string;
  expiresAt?: string;
  revokedAt?: string;
  lostReportedAt?: string;
};
```

### 15.1 Tipos de credencial

```ts
type CredentialType =
  | 'CARD_OR_TAG'
  | 'RESIDENT_QR'
  | 'GUEST_QR'
  | 'TEMPORARY_QR'
  | 'BIOMETRIC_FUTURE'
  | 'PLATE_FUTURE';
```

### 15.2 Estados

```ts
type CredentialStatus =
  | 'ACTIVE'
  | 'SUSPENDED'
  | 'LOST_REPORTED'
  | 'REVOKED'
  | 'EXPIRED'
  | 'REPLACED'
  | 'ARCHIVED';
```

### 15.3 Regla aprobada

Las credenciales se controlan por propiedad y responsable, no por censo de habitantes.

```text
Propiedad A-101
Responsable: Juan Pérez
Límite: 3 credenciales activas
Credenciales: TAG-001, TAG-002, QR residente
```

No es obligatorio asignar cada tag a un familiar específico.

---

## 16. ProviderOperation

Toda operación enviada a un proveedor debe registrarse como operación canónica.

```ts
type ProviderOperation = {
  id: string;
  providerInstallationId: string;
  accessCommandId: string;
  operationType: ProviderOperationType;
  idempotencyKey: string;
  status: ProviderOperationStatus;
  requestHash: string;
  providerResponseCode?: string;
  providerResponseMessage?: string;
  startedAt?: string;
  completedAt?: string;
  failedAt?: string;
  retryCount: number;
};
```

### 16.1 Tipos

```ts
type ProviderOperationType =
  | 'APPLY_ACCESS_PROFILE'
  | 'REVOKE_CREDENTIAL'
  | 'RESTORE_CREDENTIAL'
  | 'DISABLE_CREDENTIAL'
  | 'OPEN_DOOR'
  | 'FETCH_OBSERVED_STATE'
  | 'RECONCILE'
  | 'VALIDATE_MAPPING'
  | 'TEST_CONNECTION';
```

### 16.2 Estados

```ts
type ProviderOperationStatus =
  | 'QUEUED'
  | 'SENT_TO_EDGE'
  | 'EXECUTING'
  | 'APPLIED'
  | 'APPLIED_UNCONFIRMED'
  | 'FAILED_RETRYABLE'
  | 'FAILED_FINAL'
  | 'CANCELLED';
```

---

## 17. AccessCommand

AccessCommand es el comando de negocio que Tricor quiere ejecutar.

```ts
type AccessCommand = {
  id: string;
  condominiumId: string;
  privateAreaId?: string;
  propertyId?: string;
  providerInstallationId: string;
  desiredAccessStateId: string;
  commandType: AccessCommandType;
  commandScope: AccessCommandScope;
  status: AccessCommandStatus;
  priority: 'LOW' | 'NORMAL' | 'HIGH' | 'CRITICAL';
  idempotencyKey: string;
  reason: AccessCommandReason;
  createdAt: string;
  scheduledFor?: string;
  completedAt?: string;
};
```

### 17.1 Tipos

```ts
type AccessCommandType =
  | 'APPLY_PROFILE'
  | 'REVOKE_ACCESS'
  | 'RESTORE_ACCESS'
  | 'REVOKE_CREDENTIAL'
  | 'RESTORE_CREDENTIAL'
  | 'OPEN_DOOR_ONCE'
  | 'RECONCILE_PROVIDER';
```

### 17.2 Reasons

```ts
type AccessCommandReason =
  | 'PAYMENT_CONFIRMED'
  | 'MOROSITY_APPLIED'
  | 'MANUAL_PAYMENT_APPROVED'
  | 'MANUAL_OPENING'
  | 'TEMPORARY_EXCEPTION'
  | 'MOVE_OUT'
  | 'LOST_CREDENTIAL'
  | 'PROVIDER_RECONCILIATION'
  | 'ADMIN_CHANGE';
```

---

## 18. DesiredAccessState

DesiredAccessState representa lo que Tricor quiere que sea verdad.

```ts
type DesiredAccessState = {
  id: string;
  propertyId: string;
  responsiblePersonId: string;
  accessProfileKey: AccessProfileKey;
  allowedZones: AccessZoneKey[];
  blockedZones: AccessZoneKey[];
  credentialStates: DesiredCredentialState[];
  reason: DesiredAccessStateReason;
  policyVersion: string;
  providerMappingVersion: string;
  createdAt: string;
};
```

### 18.1 Regla

DesiredAccessState no garantiza que el proveedor ya lo aplicó.

Para eso existe ObservedAccessState.

---

## 19. ObservedAccessState

ObservedAccessState representa lo observado o confirmado desde Edge/proveedor.

```ts
type ObservedAccessState = {
  id: string;
  providerInstallationId: string;
  propertyId: string;
  observedProfileKey?: AccessProfileKey;
  observedAllowedZones?: AccessZoneKey[];
  observedCredentialStates?: ObservedCredentialState[];
  source: 'PROVIDER_EVENT' | 'EDGE_SYNC' | 'MANUAL_AUDIT' | 'HARDWARE_SMOKE';
  observedAt: string;
  confidence: 'HIGH' | 'MEDIUM' | 'LOW';
  driftDetected: boolean;
};
```

### 19.1 Regla de honestidad operativa

Si el pago está confirmado pero Edge/proveedor no ha aplicado el cambio, el estado debe decir:

```text
PAYMENT_CONFIRMED_ACCESS_PENDING
```

No se debe mentir diciendo que el acceso ya está restaurado.

---

## 20. ProviderEvent

Evento externo normalizado.

```ts
type ProviderEvent = {
  id: string;
  providerInstallationId: string;
  externalEventId?: string;
  eventType: ProviderEventType;
  normalizedType: NormalizedAccessEventType;
  externalPayloadHash: string;
  occurredAt: string;
  receivedAt: string;
  relatedDoorId?: string;
  relatedCredentialId?: string;
  relatedPropertyId?: string;
};
```

### 20.1 NormalizedAccessEventType

```ts
type NormalizedAccessEventType =
  | 'ACCESS_GRANTED'
  | 'ACCESS_DENIED'
  | 'DOOR_OPENED'
  | 'DOOR_OPEN_FAILED'
  | 'CARD_SWIPED'
  | 'QR_SCANNED'
  | 'REMOTE_OPEN_EXECUTED'
  | 'PROVIDER_ERROR'
  | 'UNKNOWN';
```

---

## 21. Estrategia de ZKTeco / CVSecurity

### 21.1 Estado

```text
Estado: implementación v1
Modo: Edge obligatorio
Prioridad: máxima para MVP técnico
```

### 21.2 Capacidades esperadas a validar

```text
- conexión desde Edge
- autenticación técnica
- gestión de personas externas
- gestión de tarjeta/tag
- asignación de niveles/perfiles de acceso
- lectura de eventos/transacciones
- control remoto de puerta/salida auxiliar, si aplica
- validación de estado observado
```

### 21.3 Reglas

```text
1. Tricor no debe llamar al proveedor directamente desde Web.
2. Tricor Cloud no debe depender de red local del condominio.
3. Edge ejecuta operaciones locales.
4. ProviderAdapter traduce comandos canónicos.
5. Todos los cambios deben pasar por AccessCommand.
6. Todo resultado debe generar ProviderOperationResult.
```

### 21.4 Mappings mínimos

```text
ACTIVE_ACCESS_PROFILE → nivel/perfil externo activo
MOROSO_DEFAULT_PROFILE → nivel/perfil externo restringido
PEDESTRIAN → puerta/grupo peatonal externo
VEHICULAR → puerta/grupo vehicular externo
CARD_OR_TAG → tipo credencial externo
DOOR → puerta externa
```

### 21.5 QA obligatorio

```text
pnpm qa:provider-contract zkteco --mock
pnpm qa:edge sync --provider zkteco --mock
pnpm qa:flow property-debt-access-lifecycle --provider zkteco --mock
pnpm qa:hardware-smoke zkteco --manual-approval
```

---

## 22. Estrategia de Hikvision

### 22.1 Estado

```text
Estado: research track
Modo: por definir
Prioridad: alta después de cerrar proveedor v1
```

### 22.2 Superficies candidatas

```text
- ISAPI directo a dispositivo/controlador/servidor local
- HikCentral Professional OpenAPI
- Hik-Connect OpenAPI
- Device Gateway / integración LAN-WAN
```

### 22.3 Decisión v0.1

No implementar producción todavía.

Hikvision debe avanzar por laboratorio y matriz de capacidades.

### 22.4 Riesgos conocidos

```text
- no todos los dispositivos soportan las mismas capacidades
- QR dinámico puede no encajar con el flujo Tricor
- remote door open puede depender de plataforma/dispositivo
- eventos pueden variar por ruta de integración
- puede requerir cuenta partner, sandbox, instalación local o licencia adicional
```

### 22.5 Condición para implementación

Hikvision solo puede pasar a implementación si existe:

```text
- hardware o sandbox validado
- ProviderCapabilityMatrix confirmada
- mappings mínimos confirmados
- provider contract tests
- Edge/cloud decision cerrada
- seguridad de credenciales documentada
- smoke test real aprobado
```

---

## 23. Generic Mock Provider

El mock provider es obligatorio para desarrollo.

```text
Estado: obligatorio en QA Harness
Modo: local/test
```

Debe simular:

```text
- personas
- credenciales
- puertas
- perfiles de acceso
- zonas
- eventos
- opening remoto
- fallas del proveedor
- latencia
- respuestas parciales
- drift entre Tricor y proveedor
```

Debe permitir probar sin hardware real.

---

## 24. Estrategia de activación de proveedor en Tricor Condo

Flujo recomendado:

```text
1. CondoAdmin abre Configuración → Proveedor de acceso.
2. Selecciona proveedor disponible.
3. Tricor crea ProviderInstallation en DRAFT.
4. Soporte técnico configura conexión/Edge.
5. Tricor ejecuta test de conexión.
6. Tricor detecta o configura ProviderCapabilityMatrix.
7. Se configuran mappings mínimos.
8. QA Harness ejecuta provider-contract.
9. Se ejecuta smoke test.
10. ProviderInstallation pasa a ACTIVE.
```

No se debe permitir activación manual sin pruebas.

---

## 25. UI de configuración de proveedor

Panel:

```text
Tricor Condo → Configuración → Proveedor de acceso
```

Debe mostrar:

```text
- proveedor activo
- estado de instalación
- Edge asociado
- última conexión exitosa
- capabilities soportadas
- mappings requeridos
- mappings faltantes
- pruebas ejecutadas
- incidentes técnicos
```

Modo avanzado/técnico:

```text
- IDs externos
- endpoint local/remoto
- logs técnicos
- prueba de conexión
- validación de mappings
- capability matrix cruda
```

No mostrar al usuario normal:

```text
- secretos
- tokens
- payloads completos
- credenciales técnicas
```

---

## 26. Estrategia de provider mappings

Los mappings deben ser explícitos, versionados y auditables.

No se permite “adivinar” perfiles externos por nombre en producción.

### 26.1 Regla de validación

Antes de activar proveedor:

```text
- todos los perfiles requeridos deben mapearse
- todas las puertas operativas deben mapearse
- todas las zonas usadas por política deben mapearse
- todos los tipos de credencial usados deben mapearse
```

### 26.2 Mapping stale

Si el proveedor cambia y el mapping deja de ser válido:

```text
ProviderMappingStatus = STALE
ProviderInstallationStatus = DEGRADED
Se crea incidente técnico
Se bloquean cambios peligrosos
```

---

## 27. Provider capability resolver

Cada adapter debe tener un resolver.

```ts
type ProviderCapabilityResolver = {
  resolveStaticCapabilities(providerKey: ProviderKey): ProviderCapabilityMatrix;
  resolveInstallationCapabilities(input: ProviderInstallation): Promise<ProviderCapabilityMatrix>;
  validateCapabilityForPolicy(input: CapabilityPolicyValidationInput): ValidationResult;
};
```

El resolver debe responder:

```text
- qué capacidades existen en abstracto
- qué capacidades existen en esta instalación
- qué capacidades dependen de hardware
- qué capacidades dependen de mapping
- qué capacidades dependen de Edge
```

---

## 28. Estrategia de QR por proveedor

QR es una capacidad sensible.

Tricor debe diferenciar:

```text
QR generado por Tricor
QR validado por Tricor Guard/Edge
QR nativo del proveedor
QR que abre directamente puerta/proveedor
```

### 28.1 Regla

No asumir que un proveedor soporta QR dinámico con el mismo modelo de Tricor.

Si el proveedor no soporta QR nativo:

```text
Tricor puede validar QR en Guard/Edge.
Luego Guard/Edge solicita apertura remota si la política lo permite.
```

Si el proveedor soporta QR nativo:

```text
El adapter debe probar expiración, revocación, uso único y bloqueo por morosidad.
```

---

## 29. Estrategia de apertura remota

Apertura remota no es permiso permanente.

Es evento único.

```text
OPEN_DOOR_ONCE
```

Permitido para:

```text
- QR válido y puerta/pluma no respondió automáticamente
- GuardSupervisor con motivo
- excepción operativa temporal dentro de límites
```

No permitido:

```text
- guardia normal sin QR válido
- usuario sin rol
- proveedor sin capability remoteDoorOpen
- instalación degradada sin confirmación
```

Debe registrar:

```text
- quién abrió
- dónde
- cuándo
- motivo
- puerta/caseta
- QR o persona relacionada, si aplica
- estado de morosidad
- resultado del proveedor
```

---

## 30. Estrategia de excepción temporal

Excepción temporal no es apertura manual.

Es un cambio temporal de política.

```text
TemporaryAccessException
```

Debe tener:

```text
- propiedad
- responsable
- motivo
- duración
- permisos permitidos
- creador
- aprobador, si aplica
- expiración automática
- auditoría
```

GuardSupervisor puede crear excepción temporal justificada dentro de límites.

Reglas recomendadas:

```text
- duración corta por default
- notificación a CondoAdmin
- no permitir excepción indefinida
- no permitir saltarse capability matrix
- no permitir acceso a zonas técnicas
```

---

## 31. Estrategia de reconciliación

Tricor debe comparar estado deseado vs estado observado.

```text
DesiredAccessState
vs
ObservedAccessState
```

Drift examples:

```text
- Tricor cree que TAG-001 está revocado, pero proveedor lo muestra activo.
- Tricor cree que propiedad está en perfil moroso, pero proveedor la tiene en activo.
- Tricor tiene puerta vehicular mapeada, pero proveedor ya no la reporta.
- Proveedor tiene persona/credencial sin referencia Tricor.
```

Acción:

```text
- crear incidente
- marcar drift
- reintentar corrección si es seguro
- pedir revisión si implica revocación masiva o datos desconocidos
```

---

## 32. Estrategia de credenciales obsoletas

El provider adapter debe soportar el ciclo de vida aprobado.

Casos:

```text
- exresidente
- mudanza con días de gracia
- credencial perdida
- credencial inactiva
- credencial activa sin propiedad válida
- credencial en proveedor sin referencia Tricor
```

Reglas:

```text
Exresidente → revocar/archivar credenciales.
Mudanza → respetar periodo de gracia configurado.
Credencial perdida → revocar de inmediato.
Credencial sin uso → alerta no invasiva.
Credencial externa desconocida → incidente de reconciliación.
```

---

## 33. Estrategia de seguridad

### 33.1 Secretos

Secretos de proveedor:

```text
- nunca en frontend
- nunca en logs planos
- nunca en reportes de usuario
- cifrados o guardados mediante secret manager
- rotación documentada
```

### 33.2 Permisos mínimos

La cuenta técnica del proveedor debe tener solo permisos necesarios.

```text
- personas/credenciales necesarios
- perfiles/puertas necesarios
- lectura de eventos si aplica
- apertura remota si aplica
```

### 33.3 Tenant isolation

Un proveedor instalado en un condominio no puede afectar otro condominio.

QA debe probar:

```text
Tricor Condo A con proveedor A no puede leer ni modificar proveedor B.
```

---

## 34. Estrategia de errores

Los errores de proveedor deben normalizarse.

```ts
type ProviderErrorCode =
  | 'AUTH_FAILED'
  | 'CONNECTION_FAILED'
  | 'TIMEOUT'
  | 'CAPABILITY_UNSUPPORTED'
  | 'MAPPING_MISSING'
  | 'MAPPING_INVALID'
  | 'EXTERNAL_NOT_FOUND'
  | 'EXTERNAL_CONFLICT'
  | 'RATE_LIMITED'
  | 'PROVIDER_VALIDATION_ERROR'
  | 'UNKNOWN_PROVIDER_ERROR';
```

Cada error debe indicar:

```text
- retryable sí/no
- severidad
- usuario afectado
- operación afectada
- siguiente acción recomendada
```

---

## 35. Idempotencia

Toda operación externa debe ser idempotente desde Tricor.

```text
Mismo comando + misma versión de estado + misma instalación = misma idempotencyKey.
```

No se permite reintentar comandos creando duplicados externos.

Ejemplo:

```text
RestoreAccess(Property A-101, policyVersion 14, mappingVersion 3)
→ idempotencyKey estable
```

---

## 36. Auditoría

Toda operación de proveedor debe crear auditoría.

```text
- configuración de proveedor creada
- test de conexión ejecutado
- mapping creado/modificado
- capability matrix actualizada
- comando enviado
- comando aplicado
- comando fallido
- drift detectado
- apertura remota ejecutada
- excepción temporal aplicada
```

La auditoría debe ser visible según rol:

```text
Platform → global/soporte
CondoAdmin → condominio
Finance → pagos relacionados, no secretos técnicos
GuardSupervisor → eventos de guardia
Resident → solo información propia relevante
```

---

## 37. QA Harness para provider strategy

QA Harness debe incluir suites específicas.

```text
qa:provider-contract
qa:provider-capabilities
qa:provider-mappings
qa:provider-errors
qa:provider-reconciliation
qa:provider-idempotency
qa:provider-security
qa:provider-edge-sync
```

### 37.1 Pruebas mínimas

```text
1. No activa proveedor sin mappings mínimos.
2. No muestra configuración si capability no está soportada.
3. No ejecuta apertura remota si remoteDoorOpen es UNSUPPORTED.
4. No aplica perfil moroso si el mapping está ausente.
5. Reintenta comando retryable sin duplicar operación externa.
6. Marca drift cuando observed state contradice desired state.
7. Revoca credencial perdida.
8. Revoca credencial de exresidente al terminar gracia.
9. No revoca automáticamente credencial sin uso; solo alerta.
10. Aísla proveedor por condominio.
11. No filtra secretos en reportes.
12. Genera reporte markdown/json para Claude Code CLI.
```

---

## 38. Provider mock contract

El mock debe implementar el mismo contrato.

No debe ser “un mock feliz”. Debe simular fallas.

```text
- éxito
- timeout
- auth failed
- mapping missing
- unsupported capability
- partial applied
- stale mapping
- drift
- offline
- duplicate command
```

El mock debe permitir fixtures:

```text
providerCapabilities.supported.json
providerCapabilities.partial.json
providerCapabilities.unsupportedQr.json
providerMappings.valid.json
providerMappings.missingProfile.json
providerMappings.staleDoor.json
```

---

## 39. Provider adapter skeleton recomendado

Ubicación:

```text
packages/provider-sdk/
apps/edge/src/providers/
apps/api/src/provider-installations/
```

Estructura conceptual:

```text
packages/provider-sdk/
├── src/
│   ├── contracts/
│   │   ├── access-provider-adapter.ts
│   │   ├── provider-capability-matrix.ts
│   │   ├── provider-mapping.ts
│   │   ├── provider-operation.ts
│   │   └── provider-errors.ts
│   ├── validators/
│   │   ├── capability-validator.ts
│   │   └── mapping-validator.ts
│   └── testing/
│       └── mock-provider.ts

apps/edge/src/providers/
├── generic-mock/
├── zkteco-cvsecurity/
└── hikvision-research/
```

Regla:

```text
hikvision-research no es producción.
```

---

## 40. Reglas de dependencia

Permitido:

```text
packages/domain → define conceptos canónicos.
packages/provider-sdk → importa contracts canónicos.
apps/api → usa provider-sdk para orquestar.
apps/edge → usa provider-sdk para ejecutar adapters.
apps/qa → usa provider-sdk para test contracts.
```

Prohibido:

```text
packages/domain → importar adapters específicos.
apps/web → llamar proveedor directamente.
apps/web → conocer secretos de proveedor.
Policy Engine → depender de providerKey concreto.
DB core → columnas específicas de proveedor en entidades principales.
```

---

## 41. Provider activation gates

Un proveedor solo puede pasar a ACTIVE si cumple:

```text
1. ProviderInstallation validada.
2. ProviderCapabilityMatrix resuelta.
3. Mappings mínimos completos.
4. Test de conexión aprobado.
5. Provider contract tests aprobados.
6. Edge sync aprobado, si aplica.
7. Smoke test aprobado, si aplica.
8. Auditoría registrada.
9. CondoAdmin/PlatformSupport confirma activación.
```

Si falla:

```text
ProviderInstallationStatus = DEGRADED o PENDING_VALIDATION.
```

---

## 42. Provider deprecation y cambio de proveedor

Cambiar proveedor es operación crítica.

Debe requerir:

```text
- modo mantenimiento
- backup de mappings
- export de referencias externas
- bloqueo temporal de comandos no críticos
- validación de nuevo proveedor
- reconciliación posterior
- aprobación administrativa
```

No se permite cambiar proveedor sin auditoría.

---

## 43. Relación con Tricor Platform

Tricor Platform debe ver:

```text
- proveedores activos por condominio
- estado de instalación
- estado de Edge
- capacidad soportada
- health checks
- incidentes técnicos
- versiones de mapping
- últimas sincronizaciones
```

No debe mezclar esto con dinero de mantenimiento.

---

## 44. Relación con Tricor Condo

Tricor Condo debe permitir:

```text
- seleccionar proveedor permitido
- ver estado de conexión
- configurar zonas/perfiles desde UI controlada
- ver warnings de capability
- solicitar soporte técnico
- revisar incidentes de proveedor
```

No debe permitir:

```text
- editar secretos crudos
- saltarse QA de provider
- crear mappings peligrosos sin validación
- activar capacidades no soportadas
```

---

## 45. Relación con Tricor Guard

Tricor Guard no habla con proveedor directamente.

Flujo:

```text
Guard escanea QR
→ Tricor valida política
→ si corresponde, genera OPEN_DOOR_ONCE
→ Edge/provider ejecuta
→ Guard ve resultado
```

Si el proveedor no soporta apertura remota:

```text
Guard ve instrucción operativa, pero no botón de apertura remota.
```

---

## 46. Relación con Tricor Resident

Tricor Resident no sabe qué proveedor hay detrás.

Solo muestra:

```text
- acceso activo
- acceso restringido
- pago pendiente
- pago confirmado, acceso pendiente si Edge/proveedor no aplicó
- credenciales activas/bloqueadas
```

No muestra:

```text
- IDs externos
- errores técnicos del proveedor
- payloads
```

---

## 47. Contratos de respuesta para UI

La UI debe usar proyecciones, no modelos técnicos crudos.

```ts
type ProviderHealthProjection = {
  providerName: string;
  status: 'OK' | 'DEGRADED' | 'OFFLINE' | 'PENDING_SETUP';
  lastSyncLabel: string;
  capabilitiesSummary: string[];
  requiredActions: string[];
};
```

```ts
type AccessStateProjection = {
  propertyId: string;
  displayStatus: 'ACTIVE' | 'RESTRICTED' | 'PENDING_RESTORE' | 'PENDING_REVOKE' | 'UNKNOWN';
  allowedLabels: string[];
  blockedLabels: string[];
  pendingActions: string[];
};
```

---

## 48. Reglas para Claude Code CLI

Claude Code CLI debe obedecer:

```text
1. No implementar lógica de proveedor dentro del dominio.
2. No crear columnas específicas de proveedor en entidades principales.
3. No llamar proveedor desde frontend.
4. No activar capabilities sin ProviderCapabilityMatrix.
5. No omitir ProviderMappingValidator.
6. No mezclar proveedores en el mismo adapter.
7. No asumir que Hikvision está listo para producción.
8. No cambiar stack técnico aprobado.
9. No saltarse QA Harness.
10. No introducir tecnologías vetadas.
```

Cada tarea de proveedor debe entregar:

```text
- cambios realizados
- archivos tocados
- provider contract tests ejecutados
- QA report
- limitaciones
- capabilities nuevas o modificadas
```

---

## 49. Prompt recomendado para Claude Code CLI

```text
Objetivo: implementar la base de provider strategy y contratos canónicos de Tricor Hábitat.

Fuentes de verdad:
- PROJECT_CHARTER_TRICOR_HABITAT.md
- DOMAIN_MODEL_V0.1.md
- ACCESS_POLICY_CONFIGURATION_V0.1.md
- CLOUD_EDGE_ARCHITECTURE_V0.1.md
- QA_HARNESS_SPEC_V0.1.md
- REPO_STRUCTURE_V0.1.md
- PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md

Reglas:
- No implementar provider real todavía salvo skeleton aprobado.
- Crear contracts type-safe en packages/provider-sdk.
- Crear ProviderCapabilityMatrix base.
- Crear ProviderMapping contracts.
- Crear mock provider para QA.
- No introducir lógica específica de proveedor en packages/domain.
- No tocar UI salvo tipos/proyecciones si la tarea lo pide.
- Ejecutar QA Harness correspondiente.

Entregables:
- provider-sdk skeleton
- mock provider
- validators
- QA tests iniciales
- reporte de ejecución
```

---

## 50. Implementación por fases

### Fase 1 — Contratos puros

```text
- ProviderKey
- ProviderInstallation
- ProviderCapabilityMatrix
- ProviderMapping
- ProviderExternalRef
- ProviderOperation
- ProviderError
- ProviderEvent
```

### Fase 2 — Validators

```text
- ProviderMappingValidator
- ProviderCapabilityResolver
- ProviderConfigValidator
```

### Fase 3 — Mock provider

```text
- mock feliz
- mock con fallas
- mock con capabilities parciales
- mock con drift
```

### Fase 4 — Integración con QA Harness

```text
- qa:provider-contract
- qa:provider-capabilities
- qa:provider-mappings
- qa:provider-reconciliation
```

### Fase 5 — Adapter ZKTeco / CVSecurity

```text
- Edge connector
- auth técnica
- mappings mínimos
- apply profile
- revoke/restore credential
- observed state inicial
- smoke test controlado
```

### Fase 6 — Research Hikvision

```text
- no producción
- laboratorio
- capability matrix confirmada
- decisión edge/cloud
- adapter skeleton solo después de validación
```

---

## 51. Definition of Done del provider layer

Se considera DONE cuando:

```text
1. Provider contracts existen en packages/provider-sdk.
2. ProviderCapabilityMatrix es obligatoria.
3. ProviderMappingValidator bloquea configuraciones incompletas.
4. Mock provider pasa QA.
5. QA Harness genera reportes markdown/json.
6. Domain no importa provider adapters.
7. Web no llama proveedor directamente.
8. Edge ejecuta provider operations mediante contratos.
9. Auditoría registra operaciones externas.
10. No existen términos ni tecnologías vetadas.
```

---

## 52. Riesgos principales

| Riesgo | Impacto | Mitigación |
|---|---:|---|
| Proveedor no soporta capacidad esperada | Alto | ProviderCapabilityMatrix y laboratorio. |
| Mapping incorrecto | Alto | ProviderMappingValidator + QA Harness. |
| Edge offline | Alto | Command queue, pending states e incidentes. |
| QR no compatible con proveedor | Medio/Alto | QR Tricor validado por Guard/Edge como fallback. |
| Remote open inseguro | Alto | roles, motivo, auditoría, capability gate. |
| Datos externos obsoletos | Alto | reconciliación y drift detection. |
| Secretos filtrados | Crítico | secret manager, redacción de logs, QA security. |
| Integrar segundo proveedor demasiado pronto | Alto | research track no bloqueante. |

---

## 53. Glosario breve

| Término | Definición |
|---|---|
| Provider | Sistema externo de control de acceso. |
| Provider Adapter | Traductor entre contratos Tricor y proveedor externo. |
| ProviderInstallation | Instalación técnica de proveedor para un condominio/caseta. |
| ProviderCapabilityMatrix | Matriz de capacidades reales disponibles. |
| ProviderMapping | Relación entre entidad Tricor y entidad externa. |
| DesiredAccessState | Estado que Tricor desea aplicar. |
| ObservedAccessState | Estado observado o confirmado desde proveedor/Edge. |
| AccessCommand | Comando de negocio de acceso. |
| ProviderOperation | Operación técnica enviada a proveedor. |
| Drift | Diferencia entre estado deseado y estado observado. |

---

## 54. Decisión final v0.1

La estrategia oficial queda así:

```text
1. Tricor Hábitat será provider-neutral desde arquitectura.
2. ZKTeco / CVSecurity será proveedor objetivo de implementación v1 mediante Edge.
3. Hikvision seguirá como research track prioritario hasta validar laboratorio.
4. Todo proveedor debe pasar por ProviderCapabilityMatrix.
5. Todo proveedor debe usar ProviderMapping explícito.
6. Todo comando debe pasar por AccessCommand y ProviderOperation.
7. Todo estado debe distinguir desired vs observed.
8. Todo cambio externo debe estar auditado.
9. QA Harness será gate obligatorio.
10. El dominio nunca dependerá del proveedor.
```

---

## 55. Próximo documento recomendado

Después de este documento, el siguiente recomendado es:

```text
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
```

Ese documento debe definir específicamente:

```text
- instalación Edge
- autenticación técnica
- mappings mínimos
- operaciones soportadas
- comandos canónicos aplicables
- smoke tests
- restricciones
- QA provider contract
- errores esperados
- seguridad
```

No debe implementarse antes de que este documento sea aceptado como base de estrategia.


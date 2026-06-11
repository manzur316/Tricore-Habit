# DOMAIN_MODEL_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** Draft formal inicial / fuente de verdad provisional de dominio  
**Fecha:** 2026-06-10  
**Documento padre:** `PROJECT_CHARTER_TRICOR_HABITAT.md`  
**Preparado para:** Arquitectura, roadmap, QA Harness y handoff posterior a Claude Code CLI  
**Idioma base:** Español  

---

## 1. Propósito del documento

Este documento define el modelo de dominio inicial de Tricor Hábitat.

Su objetivo es fijar entidades, relaciones, estados, reglas, invariantes y límites de responsabilidad antes de iniciar implementación. No es todavía el esquema definitivo de base de datos ni la especificación completa de APIs.

Este documento debe servir como fuente de verdad para:

- Diseño de backend.
- Diseño de base de datos.
- Contratos canónicos.
- QA Harness.
- Provider adapters.
- Edge.
- Interfaces Tricor Platform, Tricor Condo, Tricor Guard y Tricor Resident.
- Handoff posterior a Claude Code CLI.

---

## 2. Principios de dominio

### 2.1 Dominio antes que proveedor

El dominio de Tricor Hábitat no debe hablar directamente en términos del proveedor de acceso.

Tricor debe usar conceptos propios:

```text
Condominio
Privada
Propiedad
Propietario/responsable
Credencial
Perfil de acceso
Zona de acceso
Estado deseado
Estado observado
Comando
Auditoría
```

Los nombres, IDs y estructuras específicas del proveedor deben vivir en `ProviderMapping`, `ProviderExternalRef` y adapters.

### 2.2 Cloud como fuente de verdad

```text
Cloud = fuente de verdad
Edge = ejecutor local
Proveedor = sistema externo de acceso
```

Ningún proveedor debe ser considerado fuente principal del dominio.

### 2.3 Configuración controlada

La flexibilidad del producto debe venir de:

- Presets.
- Policy Engine.
- Configuración avanzada controlada.
- ProviderCapabilityMatrix.
- Validaciones de conflicto.
- QA Harness.

No deben existir flujos personalizados por cliente fuera del marco de Tricor.

### 2.4 Auditoría obligatoria

Toda acción relevante debe quedar auditada. Especialmente:

- Cambios de pagos.
- Restauraciones.
- Suspensiones.
- Excepciones temporales.
- Aperturas manuales.
- Cambios de credenciales.
- Cambios de proveedor.
- Cambios de Edge.
- Acciones de soporte.
- Cambios de configuración.

### 2.5 Archivar, no borrar

La regla general es:

```text
No borrar entidades operativas críticas.
Archivar, revocar o suspender.
Mantener historial.
```

Aplica a propiedades, responsables, credenciales, pagos, estados de acceso, configuraciones, eventos y provider mappings.

---

## 3. Convención de nombres

La UI puede usar nombres en español. La implementación puede usar nombres canónicos en inglés para mantener consistencia técnica.

| Concepto UI | Nombre canónico sugerido |
|---|---|
| Condominio | `Condominium` |
| Privada | `PrivateArea` |
| Propiedad | `Property` |
| Propietario / responsable principal | `ResponsiblePerson` |
| Relación responsable-propiedad | `PropertyResponsibility` |
| Credencial | `Credential` |
| Perfil de acceso | `AccessProfile` |
| Zona de acceso | `AccessZone` |
| Capacidad de acceso | `AccessCapability` |
| Estado deseado | `DesiredAccessState` |
| Estado observado | `ObservedAccessState` |
| Proveedor de acceso | `AccessProvider` |
| Instalación de proveedor | `ProviderInstallation` |
| Edge | `EdgeNode` |
| Pago / transacción | `PaymentTransaction` |
| Cargo / cuota / multa | `Charge` |
| Deuda | `Debt` |
| Comprobante | `PaymentProof` |
| Auditoría | `AuditEvent` |

La documentación funcional puede usar español. El código debe elegir una convención única y no mezclar nombres arbitrariamente.

---

## 4. Bounded contexts iniciales

El dominio se divide en contextos para evitar mezclar responsabilidades.

```text
Tricor Hábitat Domain
├── Identity & Roles
├── Organization Structure
├── Property & Responsibility
├── Finance & Payments
├── Morosity & Policy
├── Access Control
├── Credentials & QR
├── Guard Operations
├── Provider & Edge
├── Audit & Incidents
└── Files & Storage
```

---

## 5. Organization Structure

### 5.1 Condominio / Condominium

Entidad raíz comercial y operativa de un cliente dentro de Tricor Hábitat.

Puede representar:

- Condominio.
- Privada independiente.
- Comunidad residencial.
- Fraccionamiento.
- Residencial.

Aunque comercialmente el cliente sea una privada pequeña, internamente debe existir un `Condominium` para conservar estructura, permisos, pagos, auditoría y QA Harness.

#### Campos conceptuales

```text
Condominium
- id
- displayName
- legalName opcional
- type: CONDOMINIUM | INDEPENDENT_PRIVATE | RESIDENTIAL_COMMUNITY | OTHER
- status: ACTIVE | SUSPENDED | ARCHIVED
- billingPlanId
- defaultPolicyConfigId
- paymentProviderConfigId opcional
- createdAt
- updatedAt
```

#### Invariantes

- Todo `Condominium` debe tener al menos una `PrivateArea` antes de operar propiedades.
- Un `Condominium` puede tener muchas `PrivateArea`.
- Un `Condominium` no debe compartir propiedades con otro `Condominium`.
- La configuración base de pagos, morosidad, accesos y proveedor se hereda desde `Condominium` hacia niveles inferiores, salvo overrides permitidos.

---

### 5.2 Privada / PrivateArea

Agrupador intermedio obligatorio dentro de un condominio.

Puede representar:

- Privada.
- Sección.
- Etapa.
- Zona.
- Área principal.

Para una privada independiente pequeña, el sistema debe crear un `Condominium` visible como comunidad/privada independiente y una `PrivateArea` interna principal. La UI puede nombrarla “Principal”, “Área principal” o el nombre real de la privada.

#### Campos conceptuales

```text
PrivateArea
- id
- condominiumId
- displayName
- status: ACTIVE | SUSPENDED | ARCHIVED
- policyOverrideId opcional
- accessProviderScope opcional futuro
- createdAt
- updatedAt
```

#### Invariantes

- Toda `PrivateArea` pertenece a un solo `Condominium`.
- Toda `Property` pertenece a una sola `PrivateArea`.
- Un `PrivateAdmin` solo puede ver y operar dentro de su `PrivateArea` asignada.
- No debe existir propiedad operativa fuera de una `PrivateArea`.

---

### 5.3 Propiedad / Property

Unidad final operativa, financiera y de acceso.

Puede representar:

- Casa.
- Departamento.
- Lote.
- Vivienda.
- Local residencial.

La morosidad y los pagos se controlan por `Property`.

#### Campos conceptuales

```text
Property
- id
- condominiumId
- privateAreaId
- code
- displayName
- propertyType: HOUSE | APARTMENT | LOT | OTHER
- status: ACTIVE | INACTIVE | MOVE_OUT_PENDING | ARCHIVED
- currentResponsibleId
- credentialLimitProfileId
- accessProfileId
- financialAccountId
- createdAt
- updatedAt
```

#### Invariantes

- Una `Property` pertenece a una sola `PrivateArea`.
- Una `Property` tiene un solo responsable principal activo.
- Una `Property` puede tener muchos responsables históricos, pero solo uno activo principal.
- Una `Property` puede tener varias credenciales, limitadas por configuración.
- Una `Property` no debe ser eliminada si tiene pagos, credenciales, eventos o historial.

---

## 6. Property & Responsibility

### 6.1 ResponsiblePerson

Persona responsable principal de una o varias propiedades.

No representa un censo completo de habitantes.

#### Campos conceptuales

```text
ResponsiblePerson
- id
- fullName
- email opcional
- phone opcional
- identityReference opcional
- status: ACTIVE | INACTIVE | ARCHIVED
- createdAt
- updatedAt
```

#### Invariantes

- Un `ResponsiblePerson` puede estar ligado a varias propiedades.
- Una propiedad no puede tener varios responsables principales activos al mismo tiempo.
- El responsable principal es quien administra o responde por las credenciales de la propiedad.
- No se requiere registrar a todos los ocupantes de la propiedad en v1.

---

### 6.2 PropertyResponsibility

Relación histórica entre una propiedad y su responsable.

Permite cambios de propietario/responsable, mudanza y auditoría sin perder historial.

#### Campos conceptuales

```text
PropertyResponsibility
- id
- propertyId
- responsiblePersonId
- role: PRIMARY_RESPONSIBLE
- status: ACTIVE | MOVE_OUT_SCHEDULED | GRACE_PERIOD | FORMER | ARCHIVED
- startsAt
- endsAt opcional
- graceEndsAt opcional
- createdByActorId
- archivedByActorId opcional
- createdAt
- updatedAt
```

#### Invariantes

- Solo puede existir una relación `PRIMARY_RESPONSIBLE` activa por propiedad.
- Un cambio de responsable debe iniciar un flujo de transición, no sobrescribir datos.
- Al terminar la relación activa, las credenciales asociadas deben evaluarse para revocación, suspensión o archivo.
- El historial debe mantenerse.

---

### 6.3 MoveOutWorkflow

Flujo para salida, mudanza o cambio de responsable.

#### Estados

```text
ACTIVE
MOVE_OUT_SCHEDULED
MOVE_OUT_GRACE_PERIOD
FORMER_RESIDENT
ARCHIVED
```

#### Reglas

- Puede existir periodo de gracia configurable para mudanza.
- Al terminar el periodo de gracia, el sistema debe revocar credenciales activas relacionadas.
- El flujo debe generar eventos de auditoría.
- No debe eliminar físicamente al responsable ni sus relaciones históricas.

#### Configuración relevante

```text
MoveOutGracePeriod
- 0 días
- 1 día
- 2 días
- 3 días
- personalizado, si la política lo permite
```

---

## 7. Identity & Roles

### 7.1 Actor

Entidad que ejecuta acciones en el sistema.

Puede ser:

- Usuario humano.
- Servicio interno.
- Edge.
- Provider adapter.
- QA Harness.

#### Campos conceptuales

```text
Actor
- id
- actorType: USER | SERVICE | EDGE | PROVIDER_ADAPTER | QA
- userId opcional
- serviceName opcional
- edgeNodeId opcional
- status
```

---

### 7.2 UserAccount

Cuenta de acceso a la plataforma.

#### Campos conceptuales

```text
UserAccount
- id
- email
- phone opcional
- passwordHash / externalAuthRef
- status: ACTIVE | INVITED | SUSPENDED | ARCHIVED
- personId opcional
- createdAt
- updatedAt
```

---

### 7.3 Roles

Roles iniciales:

```text
PLATFORM_OWNER
PLATFORM_ADMIN
PLATFORM_SUPPORT_AGENT
CONDO_ADMIN
PRIVATE_ADMIN
FINANCE_OPERATOR
GUARD_SUPERVISOR
GUARD
RESIDENT
```

### 7.4 RoleAssignment

Los roles deben tener scope.

#### Campos conceptuales

```text
RoleAssignment
- id
- userAccountId
- role
- scopeType: PLATFORM | CONDOMINIUM | PRIVATE_AREA | PROPERTY
- scopeId
- status: ACTIVE | SUSPENDED | ARCHIVED
- createdAt
- updatedAt
```

#### Invariantes

- `PRIVATE_ADMIN` solo puede operar su `PrivateArea`.
- `FINANCE_OPERATOR` puede aprobar transferencias manuales, pero no editar permisos directamente.
- `GUARD` no puede editar residentes, propiedades, pagos ni credenciales nuevas.
- `GUARD_SUPERVISOR` puede apertura manual y excepción temporal justificada, dentro de límites.
- `PLATFORM_SUPPORT_AGENT` puede diagnosticar, pero toda sesión debe quedar auditada.

---

## 8. Finance & Payments

### 8.1 FinancialAccount

Cuenta financiera interna por propiedad.

No representa una cuenta bancaria. Representa el estado financiero de la propiedad dentro de Tricor Condo.

#### Campos conceptuales

```text
FinancialAccount
- id
- propertyId
- status: CURRENT | IN_GRACE | MOROSE | MANUAL_REVIEW | ARCHIVED
- balance
- currency
- lastComputedAt
- createdAt
- updatedAt
```

#### Invariantes

- La morosidad se calcula por propiedad.
- El estado financiero no debe actualizar permisos directamente; debe disparar recálculo de política.

---

### 8.2 Charge

Cargo generado a una propiedad.

Tipos iniciales:

```text
MAINTENANCE_FEE
FINE
EXTRAORDINARY_FEE
OTHER
```

#### Campos conceptuales

```text
Charge
- id
- propertyId
- chargeType
- description
- amount
- currency
- dueDate
- status: OPEN | PARTIALLY_PAID | PAID | OVERDUE | CANCELLED | WAIVED
- createdByActorId
- createdAt
- updatedAt
```

#### Invariantes

- Un cargo vencido puede contribuir a morosidad según política.
- Los cargos cancelados o condonados no deben generar restricción.
- Las multas son cargos, no un módulo separado.

---

### 8.3 Debt

Vista agregada o cálculo de deuda por propiedad.

Puede implementarse como entidad persistida o proyección calculada.

#### Campos conceptuales

```text
Debt
- id
- propertyId
- totalOpenAmount
- overdueAmount
- currency
- oldestDueDate
- status: NONE | OPEN | OVERDUE | IN_GRACE | MOROSE
- computedAt
```

#### Invariantes

- `Debt` no debe duplicar reglas de negocio inconsistentes.
- Debe derivarse de cargos, pagos, ajustes y política de morosidad.

---

### 8.4 PaymentProviderConfig

Configuración de proveedor de pagos por condominio.

#### Campos conceptuales

```text
PaymentProviderConfig
- id
- condominiumId
- provider: MERCADO_PAGO | STRIPE_FUTURE | MANUAL_ONLY
- status: NOT_CONFIGURED | CONNECTED | ERROR | DISABLED
- accountExternalRef
- feePolicy
- manualTransferEnabled
- proofUploadEnabled
- createdAt
- updatedAt
```

#### Invariantes

- El dinero de mantenimiento debe caer en la cuenta configurada por el condominio.
- Tricor Platform no debe mezclar dinero de mantenimiento con ingresos SaaS.
- La restauración automática requiere proveedor activo y confirmación verificable.

---

### 8.5 PaymentOrder

Orden de pago creada por Tricor para que el residente pague dentro de la plataforma.

#### Campos conceptuales

```text
PaymentOrder
- id
- condominiumId
- propertyId
- providerConfigId
- amount
- currency
- status: CREATED | PENDING_PROVIDER | APPROVED | REJECTED | EXPIRED | CANCELLED
- providerOrderRef
- createdAt
- updatedAt
```

---

### 8.6 PaymentTransaction

Transacción confirmada o reportada por proveedor o manualmente.

#### Campos conceptuales

```text
PaymentTransaction
- id
- paymentOrderId opcional
- propertyId
- source: ONLINE_PROVIDER | MANUAL_BANK_TRANSFER | ADMIN_ADJUSTMENT
- provider: MERCADO_PAGO | MANUAL | OTHER
- amount
- currency
- status: PENDING | APPROVED | REJECTED | CANCELLED | REFUNDED | CHARGEBACK
- providerPaymentRef opcional
- approvedAt opcional
- reviewedByActorId opcional
- createdAt
- updatedAt
```

#### Invariantes

- Pago confirmado por proveedor puede disparar restauración automática.
- Pago manual requiere revisión y aprobación auditada.
- Finanzas no edita permisos; aprueba pago manual y el sistema recalcula acceso.

---

### 8.7 PaymentProof

Comprobante cargado por residente o administración.

#### Campos conceptuales

```text
PaymentProof
- id
- propertyId
- paymentTransactionId opcional
- fileAssetId
- declaredAmount
- declaredPaidAt
- status: UPLOADED | UNDER_REVIEW | LINKED_TO_PAYMENT | APPROVED_MANUAL | REJECTED | DISPUTED | ARCHIVED
- uploadedByActorId
- reviewedByActorId opcional
- reviewNotes opcional
- createdAt
- updatedAt
```

#### Invariantes

- El comprobante no restaura acceso por sí solo.
- Si corresponde a transferencia manual, debe pasar por revisión.
- El archivo real vive en storage compatible con S3; la base de datos guarda metadata.

---

## 9. Morosity & Policy

### 9.1 PolicyConfig

Configuración versionada de reglas por condominio y, si se permite, por privada.

#### Campos conceptuales

```text
PolicyConfig
- id
- scopeType: PLATFORM_DEFAULT | CONDOMINIUM | PRIVATE_AREA
- scopeId
- preset: SOFT | STANDARD | STRICT | CUSTOM
- version
- status: DRAFT | ACTIVE | ARCHIVED
- configJson
- validatedAt
- createdByActorId
- activatedByActorId opcional
- createdAt
- updatedAt
```

#### Invariantes

- Las reglas activas deben estar validadas por `PolicyConfigValidator`.
- Los cambios de política deben versionarse.
- Cambiar una política puede recalcular accesos de muchas propiedades y debe generar vista de impacto.

---

### 9.2 MorosityPolicy

Subconjunto de política enfocado en deuda y acceso.

#### Configuración conceptual

```text
MorosityPolicy
- graceDays
- suspensionRunTime
- blockGuestQrWhenMorose
- blockVehicularAccessWhenMorose
- blockResidentQrWhenMorose
- blockPedestrianAccessWhenMorose
- blockNonEssentialZonesWhenMorose
- allowPartialPaymentRestore
- allowTemporaryException
- allowManualPaymentRestore
- restoreAutomaticallyOnProviderPayment
```

#### Defaults aprobados

```text
Propiedad al corriente:
- permisos permitidos por su perfil activo.

Propiedad morosa default:
- peatonal permitido.
- vehicular bloqueado.
- QR invitados bloqueado.
- zonas no esenciales bloqueadas.
- amenidades futuras bloqueadas.
```

---

### 9.3 PolicyConfigValidator

Servicio de dominio que valida compatibilidad de configuración.

Debe impedir configuraciones inválidas.

Ejemplos:

```text
No activar restauración automática si no hay proveedor de pago activo.
No bloquear QR invitados si QR invitados está desactivado globalmente.
No usar perfil moroso si no existe mapeo del proveedor.
No activar capacidades no soportadas por proveedor.
No permitir apertura manual a GUARD si política lo prohíbe.
```

---

### 9.4 EffectivePolicy

Resultado final de aplicar jerarquía de configuración.

Precedencia:

```text
Platform default
↓
Condominium policy
↓
PrivateArea override permitido
↓
Property exception temporal auditada
```

No todos los niveles pueden sobrescribir todo.

---

## 10. Access Control

### 10.1 AccessZone

Zona lógica de acceso.

Zonas iniciales:

```text
PEDESTRIAN
VEHICULAR
SERVICE_FUTURE
AMENITY_FUTURE
```

En v1 no se maneja vehículo como entidad. Sí se maneja acceso vehicular como zona/capacidad.

#### Campos conceptuales

```text
AccessZone
- id
- condominiumId
- code
- displayName
- zoneType
- status: ACTIVE | DISABLED | ARCHIVED
```

---

### 10.2 Door

Puerta, pluma, acceso físico o punto controlado.

#### Campos conceptuales

```text
Door
- id
- condominiumId
- privateAreaId opcional
- accessZoneId
- providerInstallationId
- displayName
- providerDoorRef opcional
- status: ACTIVE | DISABLED | ARCHIVED
```

---

### 10.3 AccessCapability

Capacidad lógica que puede concederse, restringirse o revocarse.

Ejemplos:

```text
PEDESTRIAN_ACCESS
VEHICULAR_ACCESS
RESIDENT_QR_ACCESS
GUEST_QR_GENERATION
CARD_TAG_ACCESS
BIOMETRIC_ACCESS_FUTURE
FACE_ACCESS_FUTURE
PLATE_ACCESS_FUTURE
AMENITY_ACCESS_FUTURE
```

#### Invariantes

- Una capacidad solo puede usarse si Tricor la soporta y el proveedor/instalación también.
- Las capacidades futuras pueden existir en dominio como placeholders, pero no deben activarse en v1 sin soporte real.

---

### 10.4 AccessProfile

Perfil canónico de acceso.

Perfiles base:

```text
ACTIVE_ACCESS_PROFILE
MOROSE_DEFAULT_PROFILE
FORMER_RESIDENT_PROFILE
TEMPORARY_EXCEPTION_PROFILE
VISITOR_PROFILE
```

#### Campos conceptuales

```text
AccessProfile
- id
- condominiumId
- code
- displayName
- capabilities[]
- zones[]
- status: ACTIVE | DISABLED | ARCHIVED
```

#### Invariantes

- Una propiedad al corriente recibe el perfil activo permitido por su tipo/configuración.
- Una propiedad morosa recibe perfil calculado por política.
- Un exresponsable/exresidente no conserva accesos activos.
- Un perfil no se aplica al proveedor sin mapping válido.

---

### 10.5 DesiredAccessState

Estado que Tricor quiere que exista en el proveedor.

#### Campos conceptuales

```text
DesiredAccessState
- id
- propertyId
- responsiblePersonId opcional
- accessProfileId
- capabilities
- credentials
- reason
- version
- status: PENDING | COMMAND_CREATED | APPLIED | PARTIALLY_APPLIED | FAILED | CANCELLED
- computedAt
- createdAt
```

#### Invariantes

- Se calcula a partir de estado financiero, política, credenciales y capacidades.
- Cada cambio debe generar versión.
- No se debe editar proveedor sin pasar por desired state.

---

### 10.6 ObservedAccessState

Estado observado/reportado desde Edge o proveedor.

#### Campos conceptuales

```text
ObservedAccessState
- id
- propertyId
- providerInstallationId
- observedCapabilities
- observedCredentials
- providerSnapshotRef opcional
- status: MATCHES_DESIRED | DRIFT_DETECTED | UNKNOWN | ERROR
- observedAt
- createdAt
```

#### Invariantes

- Si observado no coincide con deseado, debe generarse drift o incidente.
- El estado observado no reemplaza al estado deseado; se usa para reconciliación.

---

### 10.7 AccessCommand

Comando para Edge/proveedor.

#### Tipos

```text
APPLY_ACCESS_PROFILE
REVOKE_CREDENTIAL
SUSPEND_CREDENTIAL
RESTORE_CREDENTIAL
CREATE_PROVIDER_PERSON_OR_IDENTITY
UPDATE_PROVIDER_MAPPING
OPEN_DOOR_ONCE
SYNC_PROVIDER_STATE
```

#### Campos conceptuales

```text
AccessCommand
- id
- condominiumId
- propertyId opcional
- edgeNodeId opcional
- providerInstallationId
- commandType
- payloadCanonical
- payloadProvider opcional
- desiredStateVersion opcional
- status: PENDING | DISPATCHED | APPLIED | FAILED | EDGE_OFFLINE | PROVIDER_ERROR | APPLIED_UNCONFIRMED | CANCELLED
- failureReason opcional
- createdAt
- updatedAt
```

#### Invariantes

- Todo comando debe ser auditable.
- El payload canónico debe existir antes del payload específico del proveedor.
- El comando puede quedar pendiente si Edge está offline.
- No se debe marcar acceso como restaurado si el comando aún no fue aplicado o confirmado.

---

### 10.8 AccessStateCalculator

Servicio de dominio que calcula acceso efectivo.

Fórmula conceptual:

```text
Property status
+ Financial standing
+ Morosity policy
+ Access profile
+ Credential status
+ Provider capabilities
+ Temporary exceptions
= DesiredAccessState
```

---

## 11. Credentials & QR

### 11.1 AccessIdentity

Identidad canónica de acceso usada para ligar persona/responsable, propiedad y credenciales.

En v1, el caso principal es:

```text
Propiedad → responsable principal → múltiples credenciales activas bajo su responsabilidad
```

No se hará censo completo de ocupantes.

#### Campos conceptuales

```text
AccessIdentity
- id
- propertyId
- responsiblePersonId
- status: ACTIVE | SUSPENDED | FORMER | ARCHIVED
- providerIdentityRefs[]
- createdAt
- updatedAt
```

#### Invariantes

- Una credencial debe estar ligada a una propiedad.
- Una credencial puede estar ligada al responsable principal vía `AccessIdentity`.
- No es obligatorio identificar al familiar o usuario final de cada tag desde v1.
- La inactividad se audita por credencial, no necesariamente por persona física.

---

### 11.2 Credential

Credencial de acceso.

Tipos iniciales:

```text
CARD_TAG
RESIDENT_QR
GUEST_QR
TEMPORARY_CREDENTIAL
```

Futuros según proveedor:

```text
BIOMETRIC
FACE
PLATE
```

#### Estados

```text
ACTIVE
SUSPENDED
LOST_REPORTED
REVOKED
REPLACED
EXPIRED
ARCHIVED
```

#### Campos conceptuales

```text
Credential
- id
- condominiumId
- propertyId
- accessIdentityId opcional
- credentialType
- displayLabel opcional
- providerCredentialRef opcional
- status
- lastUsedAt opcional
- expiresAt opcional
- lostReportedAt opcional
- createdAt
- updatedAt
```

#### Invariantes

- Las credenciales activas no deben superar el límite configurado por propiedad.
- El residente puede reportar una credencial propia como perdida.
- El residente no puede registrar una credencial nueva.
- Administración debe emitir o registrar reemplazos.
- Credenciales de exresponsables deben revocarse al finalizar flujo de salida, salvo periodo de gracia vigente.

---

### 11.3 CredentialLimitProfile

Define límites de credenciales por propiedad.

#### Campos conceptuales

```text
CredentialLimitProfile
- id
- condominiumId
- maxCardTags
- maxResidentQr
- maxActiveGuestQr
- maxTemporaryCredentials
- inactiveCredentialReviewDays
- createdAt
- updatedAt
```

---

### 11.4 CredentialLifecycleAuditJob

Proceso programado de revisión.

Detecta:

```text
Credenciales activas de exresponsables.
Credenciales sin uso por X días.
Credenciales sin propiedad activa.
Credenciales sin AccessIdentity válido.
Credenciales presentes en proveedor pero no vinculadas a Tricor.
```

Acciones:

```text
Crear alerta no invasiva.
Crear incidente si hay riesgo alto.
Revocar automáticamente solo si la política lo permite.
No eliminar físicamente por default.
```

---

### 11.5 QR residente

QR ligado al residente/responsable y propiedad.

Recomendación de dominio:

```text
Debe ser dinámico.
Debe estar firmado.
Debe expirar.
Debe validarse contra estado de acceso.
No debe confiar en capturas antiguas.
```

#### Campos conceptuales

```text
ResidentQrSession
- id
- propertyId
- accessIdentityId
- status: ACTIVE | EXPIRED | REVOKED
- issuedAt
- expiresAt
- lastValidatedAt opcional
```

---

### 11.6 QR invitado / GuestAccessPass

Pase de invitado generado por residente si la política lo permite.

#### Campos conceptuales

```text
GuestAccessPass
- id
- propertyId
- createdByUserId
- guestName opcional
- status: ACTIVE | USED | EXPIRED | REVOKED | BLOCKED_BY_MOROSITY
- validFrom
- validUntil
- maxUses
- usesCount
- qrTokenRef
- createdAt
- updatedAt
```

#### Invariantes

- Default: morosidad bloquea generación/uso de QR invitados.
- Puede ser configurable por condominio.
- Cada lectura debe quedar registrada.
- Si cambia estado de morosidad, el pase debe reevaluarse.

---

## 12. Guard Operations

### 12.1 GuardAccessEvent

Evento operativo registrado desde Tricor Guard.

#### Tipos

```text
QR_SCANNED
ACCESS_ALLOWED
ACCESS_DENIED
MANUAL_OPENING
PROVIDER_FAILURE
ROUND_REPORT
SECURITY_INCIDENT
```

#### Campos conceptuales

```text
GuardAccessEvent
- id
- condominiumId
- privateAreaId opcional
- gateOrDoorId opcional
- guardUserId
- eventType
- propertyId opcional
- credentialId opcional
- guestAccessPassId opcional
- result: ALLOWED | DENIED | FAILED | REPORTED
- reasonCode opcional
- notes opcional
- createdAt
```

---

### 12.2 ManualOpening

Apertura manual de evento único.

Puede realizarla `GUARD_SUPERVISOR` según política.

#### Motivos sugeridos

```text
QR_VALID_GATE_FAILED
PROVIDER_FAILURE
EMERGENCY
ADMIN_AUTHORIZED
OTHER
```

#### Campos conceptuales

```text
ManualOpening
- id
- condominiumId
- doorId
- guardSupervisorUserId
- reasonCode
- notes opcional
- relatedQrScanId opcional
- relatedPropertyId opcional
- status: REQUESTED | EXECUTED | FAILED | CANCELLED
- createdAt
- executedAt opcional
```

#### Invariantes

- No debe ser formulario pesado para el guardia.
- Motivo obligatorio.
- Auditoría completa.
- No cambia permisos permanentes.

---

### 12.3 TemporaryAccessException

Excepción temporal que modifica acceso calculado por tiempo limitado.

Puede crearla `GUARD_SUPERVISOR` si la política lo permite, con límites y auditoría.

#### Casos

```text
Mudanza
Adulto mayor
Error administrativo
Pago en aclaración
Emergencia
Caso especial autorizado
```

#### Campos conceptuales

```text
TemporaryAccessException
- id
- condominiumId
- propertyId
- createdByActorId
- reasonCode
- notes opcional
- allowedCapabilities
- startsAt
- expiresAt
- status: ACTIVE | EXPIRED | REVOKED | CANCELLED
- notifiedCondoAdminAt opcional
- createdAt
- updatedAt
```

#### Invariantes

- Debe tener expiración automática.
- Debe tener duración máxima por política.
- Debe notificar a CondoAdmin.
- Debe generar recálculo de `DesiredAccessState`.
- No debe usarse como reemplazo de pago ni convenio permanente.

---

## 13. Provider & Edge

### 13.1 AccessProvider

Proveedor soportado por Tricor.

#### Campos conceptuales

```text
AccessProvider
- id
- code
- displayName
- status: SUPPORTED | RESEARCH | DISABLED
- createdAt
- updatedAt
```

---

### 13.2 ProviderCapabilityMatrix

Matriz de capacidades por proveedor e instalación.

#### Campos conceptuales

```text
ProviderCapabilityMatrix
- id
- providerId
- providerInstallationId opcional
- capabilities
- limitations
- status: ACTIVE | NEEDS_REVIEW | ARCHIVED
- verifiedAt opcional
```

#### Invariantes

- La UI no debe permitir configurar capacidades no soportadas.
- QA Harness debe probar compatibilidad de configuración.
- Cada provider adapter debe declarar capacidades.

---

### 13.3 ProviderInstallation

Instalación concreta de un proveedor para un condominio o caseta.

#### Campos conceptuales

```text
ProviderInstallation
- id
- condominiumId
- privateAreaId opcional
- providerId
- edgeNodeId opcional
- displayName
- status: NOT_CONFIGURED | CONFIGURED | ACTIVE | ERROR | DISABLED | ARCHIVED
- connectionConfigRef
- createdAt
- updatedAt
```

#### Invariantes

- Un condominio puede tener una o varias instalaciones.
- Un condominio puede tener proveedores distintos en el futuro, pero el dominio no cambia.
- Cada instalación debe tener mappings validados antes de activarse.

---

### 13.4 ProviderMapping

Mapea conceptos canónicos de Tricor a conceptos del proveedor.

#### Tipos

```text
ACCESS_PROFILE_MAPPING
ACCESS_ZONE_MAPPING
DOOR_MAPPING
CREDENTIAL_MAPPING
IDENTITY_MAPPING
```

#### Campos conceptuales

```text
ProviderMapping
- id
- providerInstallationId
- mappingType
- canonicalRef
- providerRef
- status: ACTIVE | INVALID | NEEDS_REVIEW | ARCHIVED
- validatedAt opcional
- createdAt
- updatedAt
```

#### Invariantes

- No aplicar `DesiredAccessState` si falta mapping requerido.
- Cambios de mapping deben auditarse.
- QA Harness debe validar mappings antes de piloto real.

---

### 13.5 EdgeNode

Agente local de ejecución y observación.

#### Campos conceptuales

```text
EdgeNode
- id
- condominiumId
- privateAreaId opcional
- locationLabel
- status: ONLINE | OFFLINE | DEGRADED | DISABLED | ARCHIVED
- lastHeartbeatAt
- version
- localUiEnabled
- createdAt
- updatedAt
```

#### Invariantes

- Edge no decide morosidad.
- Edge no calcula pagos.
- Edge no administra residentes ni permisos permanentes.
- Edge ejecuta comandos y reporta estado.
- Edge puede operar con snapshot offline limitado.

---

### 13.6 EdgeCommandQueue

Cola de comandos para Edge.

#### Campos conceptuales

```text
EdgeCommandQueueItem
- id
- edgeNodeId
- accessCommandId
- status: PENDING | SENT | APPLIED | FAILED | RETRYING | CANCELLED
- attempts
- lastAttemptAt opcional
- failureReason opcional
- createdAt
- updatedAt
```

---

### 13.7 EdgeHeartbeat

Registro de salud del Edge.

#### Campos conceptuales

```text
EdgeHeartbeat
- id
- edgeNodeId
- status
- providerConnectionStatus
- pendingCommandCount
- localSnapshotVersion
- receivedAt
```

---

## 14. Audit & Incidents

### 14.1 AuditEvent

Evento de auditoría de dominio.

#### Campos conceptuales

```text
AuditEvent
- id
- condominiumId opcional
- actorId
- action
- entityType
- entityId
- beforeSnapshot opcional
- afterSnapshot opcional
- metadata
- createdAt
```

#### Invariantes

- Todo cambio sensible debe generar auditoría.
- Auditoría no debe ser editable por usuarios normales.
- Soporte SaaS debe quedar auditado.

---

### 14.2 OperationalIncident

Incidente operativo.

Ejemplos:

```text
EDGE_OFFLINE
PROVIDER_ERROR
MAPPING_MISSING
PAYMENT_PROVIDER_ERROR
ACCESS_DRIFT_DETECTED
MANUAL_REVIEW_REQUIRED
CREDENTIAL_RISK_DETECTED
```

#### Campos conceptuales

```text
OperationalIncident
- id
- condominiumId
- severity: INFO | WARNING | CRITICAL
- incidentType
- status: OPEN | ACKNOWLEDGED | RESOLVED | ARCHIVED
- relatedEntityType opcional
- relatedEntityId opcional
- description
- createdAt
- resolvedAt opcional
```

---

### 14.3 SupportSession

Sesión de soporte desde Tricor Platform.

#### Campos conceptuales

```text
SupportSession
- id
- platformSupportUserId
- condominiumId
- reason
- status: OPEN | CLOSED
- startedAt
- endedAt opcional
```

#### Invariantes

- Toda acción del soporte dentro de un condominio debe estar ligada a una sesión.
- La sesión debe tener motivo.
- No debe ocultarse al historial interno.

---

## 15. Files & Storage

### 15.1 FileAsset

Metadata de archivo almacenado en storage compatible con S3.

#### Campos conceptuales

```text
FileAsset
- id
- condominiumId
- ownerEntityType
- ownerEntityId
- storageProvider
- bucket
- objectKey
- mimeType
- sizeBytes
- checksum opcional
- status: ACTIVE | QUARANTINED | ARCHIVED | DELETED_LOGICAL
- uploadedByActorId
- createdAt
```

#### Usos iniciales

- Comprobantes de pago.
- Evidencias de soporte futuro.
- Evidencias de incidentes futuro.

#### Invariantes

- La base de datos guarda metadata.
- El archivo real vive en storage.
- Acceso a archivos debe ser privado y temporal.
- La retención se definirá en documento posterior.

---

## 16. Domain events

Los eventos de dominio deben alimentar auditoría, outbox, QA Harness y procesos asincrónicos.

Eventos iniciales:

```text
CondominiumCreated
PrivateAreaCreated
PropertyCreated
ResponsiblePersonCreated
PropertyResponsibleAssigned
MoveOutScheduled
MoveOutGraceStarted
MoveOutCompleted
CredentialCreated
CredentialReportedLost
CredentialRevoked
ChargeCreated
ChargeOverdue
PaymentOrderCreated
PaymentProviderConfirmed
ManualPaymentSubmitted
ManualPaymentApproved
ManualPaymentRejected
PropertyBecameMorose
PropertyReturnedToCurrent
PolicyConfigUpdated
DesiredAccessStateComputed
AccessCommandCreated
AccessCommandApplied
AccessCommandFailed
ObservedAccessStateReported
AccessDriftDetected
GuestQrGenerated
GuestQrScanned
ManualOpeningExecuted
TemporaryAccessExceptionCreated
TemporaryAccessExceptionExpired
EdgeWentOffline
EdgeCameOnline
ProviderMappingUpdated
SupportSessionStarted
SupportSessionClosed
```

---

## 17. Outbox y consistencia

Debe existir un patrón outbox para eventos/comandos que cruzan contextos.

Casos:

```text
Pago confirmado → recalcular acceso → crear comando Edge
Política cambiada → recalcular propiedades afectadas
Credencial perdida → revocar credencial en proveedor
Mudanza finalizada → revocar credenciales anteriores
Edge offline → dejar comandos pendientes
```

### Invariantes

- No ejecutar cambios críticos solo en memoria.
- Todo comando debe ser persistido.
- Todo proceso asincrónico debe ser reintentable.
- Los errores deben generar incidentes, no desaparecer.

---

## 18. Estados principales

### 18.1 PropertyStatus

```text
ACTIVE
INACTIVE
MOVE_OUT_PENDING
ARCHIVED
```

### 18.2 FinancialStanding

```text
CURRENT
IN_GRACE
MOROSE
MANUAL_REVIEW
ARCHIVED
```

### 18.3 ChargeStatus

```text
OPEN
PARTIALLY_PAID
PAID
OVERDUE
CANCELLED
WAIVED
```

### 18.4 PaymentStatus

```text
CREATED
PENDING
APPROVED
REJECTED
EXPIRED
CANCELLED
REFUNDED
CHARGEBACK
```

### 18.5 CredentialStatus

```text
ACTIVE
SUSPENDED
LOST_REPORTED
REVOKED
REPLACED
EXPIRED
ARCHIVED
```

### 18.6 AccessCommandStatus

```text
PENDING
DISPATCHED
APPLIED
FAILED
EDGE_OFFLINE
PROVIDER_ERROR
APPLIED_UNCONFIRMED
CANCELLED
```

### 18.7 EdgeStatus

```text
ONLINE
OFFLINE
DEGRADED
DISABLED
ARCHIVED
```

### 18.8 PolicyStatus

```text
DRAFT
ACTIVE
ARCHIVED
```

---

## 19. Relaciones cardinales principales

```text
Condominium 1 ── * PrivateArea
PrivateArea 1 ── * Property
Property 1 ── * PropertyResponsibility historical
Property 1 ── 1 active PRIMARY PropertyResponsibility
ResponsiblePerson 1 ── * PropertyResponsibility
Property 1 ── * Credential
Property 1 ── 1 FinancialAccount
Property 1 ── * Charge
Property 1 ── * PaymentTransaction
Property 1 ── * DesiredAccessState
Property 1 ── * ObservedAccessState
Condominium 1 ── * EdgeNode
Condominium 1 ── * ProviderInstallation
ProviderInstallation 1 ── * ProviderMapping
EdgeNode 1 ── * AccessCommand
UserAccount 1 ── * RoleAssignment
Condominium 1 ── * AuditEvent
```

---

## 20. Reglas críticas de negocio

### 20.1 Estructura

- La estructura interna no debe romperse: `Condominium → PrivateArea → Property`.
- Privadas independientes se modelan como un `Condominium` de tipo `INDEPENDENT_PRIVATE` con una `PrivateArea` principal.
- No debe existir propiedad operativa fuera de una privada.

### 20.2 Propiedad

- El cobro se controla por propiedad.
- La morosidad se controla por propiedad.
- Una propiedad tiene un solo responsable principal activo.
- Un responsable puede tener varias propiedades.
- No se hace censo completo de ocupantes en v1.

### 20.3 Pagos

- Pago confirmado por proveedor puede restaurar automáticamente.
- Transferencia manual existe como fallback auditado.
- Comprobante no restaura acceso por sí solo.
- Finanzas aprueba pagos manuales; no edita permisos directamente.
- Tricor no es intermediario financiero del mantenimiento.

### 20.4 Morosidad

- Default moroso: peatonal permitido, vehicular bloqueado, QR invitados bloqueado, zonas no esenciales bloqueadas.
- Pagos parciales no restauran por default.
- Excepción temporal existe, auditada y con expiración.

### 20.5 Acceso

- No se suspende la propiedad; se restringen capacidades de acceso.
- No se debe editar proveedor directamente.
- Todo cambio pasa por desired state, comando, Edge/proveedor, observed state y auditoría.
- Configuraciones dependen de capacidades del proveedor.

### 20.6 Credenciales

- Credenciales están bajo responsabilidad de la propiedad/responsable principal.
- Residente puede reportar pérdida.
- Residente no puede registrar nueva credencial.
- Exresponsables deben perder acceso al finalizar flujo de salida.
- Credenciales sin uso generan revisión no invasiva, no revocación automática por default.

### 20.7 Guardia

- Guardia normal no edita datos ni abre manualmente sin política.
- Guardia supervisor puede apertura manual y excepción temporal justificadas.
- Apertura manual es evento único.
- Excepción temporal es regla temporal con expiración.

### 20.8 Edge

- Edge ejecuta, no decide.
- Edge puede estar offline y mantener cola.
- Si Edge está offline, acceso restaurado queda pendiente de aplicación.
- UI local de Edge es técnica, no administrativa.

---

## 21. Domain services iniciales

Servicios de dominio recomendados:

```text
PolicyConfigValidator
EffectivePolicyResolver
MorosityEvaluator
AccessStateCalculator
DesiredAccessStateService
AccessCommandFactory
PaymentConfirmationHandler
ManualPaymentReviewService
CredentialLifecycleService
MoveOutWorkflowService
GuestAccessPassService
GuardAccessService
TemporaryAccessExceptionService
ProviderMappingValidator
ProviderCapabilityResolver
EdgeCommandDispatcher
AccessReconciliationService
AuditEventService
OperationalIncidentService
```

---

## 22. Proyecciones iniciales para UI

La UI no debe consultar directamente tablas complejas para cada pantalla. Deben existir proyecciones o endpoints preparados.

### 22.1 Tricor Condo

```text
PropertyAccountSummary
MorosePropertiesList
PaymentsReviewQueue
CredentialReviewQueue
AccessStateDashboard
ProviderHealthSummary
EdgeHealthSummary
AuditTimeline
```

### 22.2 Tricor Guard

```text
QrValidationResult
GuardAccessDecision
DoorStatusSummary
ManualOpeningFormOptions
GuardEventTimeline
```

### 22.3 Tricor Resident

```text
ResidentPropertySummary
ResidentAccountStatement
ResidentAccessStatus
GuestPassList
CredentialStatusList
PaymentOrderView
```

### 22.4 Tricor Platform

```text
CondominiumUsageSummary
LicenseStatusSummary
EdgeGlobalHealth
ProviderInstallationSummary
SupportSessionTimeline
```

---

## 23. QA Harness implications

Este modelo de dominio exige que el QA Harness pruebe:

```text
1. Creación Condominio → Privada → Propiedad.
2. Privada independiente sin romper estructura interna.
3. Un responsable con varias propiedades.
4. Una propiedad con un solo responsable principal activo.
5. Límites de credenciales por propiedad.
6. Reporte de credencial perdida por residente.
7. Cambio de responsable con periodo de gracia.
8. Revocación de credenciales al finalizar mudanza.
9. Generación de cargos y deuda.
10. Pago confirmado por proveedor y restauración automática.
11. Transferencia manual y revisión por Finanzas.
12. Morosidad default: peatonal permitido, vehicular/QR invitados bloqueados.
13. Configuración inválida bloqueada por PolicyConfigValidator.
14. ProviderCapabilityMatrix impidiendo reglas no soportadas.
15. DesiredAccessState generado correctamente.
16. AccessCommand creado correctamente.
17. Edge offline dejando comandos pendientes.
18. ObservedAccessState detectando drift.
19. Guardia normal sin permisos de edición.
20. Guardia supervisor con apertura manual auditada.
21. Excepción temporal con expiración.
22. QR invitado bloqueado por morosidad.
23. Comprobante manual sin restauración automática.
24. Auditoría de acciones sensibles.
```

---

## 24. Lo que este documento NO define todavía

Este documento no define todavía:

- Esquema Prisma final.
- Migraciones.
- Endpoints REST.
- Contratos OpenAPI.
- Diseño UI.
- Diseño detallado de Mercado Pago.
- Detalle de provider adapter real.
- Retención legal de comprobantes.
- Política final de privacidad.
- Implementación de Flutter.
- Pricing comercial.

Estos temas vivirán en documentos posteriores.

---

## 25. Reglas para Claude Code CLI

Cuando este documento se entregue a Claude Code CLI:

1. No implementar base de datos sin convertir primero este dominio en schema técnico revisado.
2. No crear entidades fuera de este dominio sin actualizar documentación.
3. No usar proveedor externo como fuente de verdad del dominio.
4. No permitir cambios de acceso sin `DesiredAccessState` y `AccessCommand`.
5. No permitir pago manual sin auditoría.
6. No permitir comprobante como restauración automática por sí solo.
7. No permitir múltiples responsables principales activos por propiedad.
8. No permitir credenciales activas de exresponsables después de terminar periodo de gracia.
9. No permitir configuración no soportada por proveedor.
10. No tocar varias superficies sin prueba QA asociada.
11. No implementar módulos fuera de v1 definidos en el charter.
12. Todo cambio debe tener prueba o suite QA relacionada.

---

## 26. Criterio de aprobación del dominio v0.1

Este documento queda aprobado cuando se acepte que:

- La estructura raíz es `Condominium → PrivateArea → Property`.
- La propiedad es la unidad financiera y de morosidad.
- El responsable principal es único por propiedad.
- Las credenciales se controlan por propiedad/responsable, no por censo de habitantes.
- El pago confirmado por proveedor puede restaurar automáticamente.
- Transferencia manual existe como fallback auditado.
- Acceso se calcula por Policy Engine y capacidades del proveedor.
- Edge ejecuta, Cloud decide.
- El proveedor no domina el modelo.
- QA Harness debe validar el dominio antes de producción.

---

## 27. Próximo documento recomendado

Siguiente archivo a redactar:

```text
ACCESS_POLICY_CONFIGURATION_V0.1.md
```

Objetivo:

Definir presets, configuraciones, dependencias, incompatibilidades, matriz de permisos, validaciones y suites QA para el Policy Engine de Tricor Hábitat.

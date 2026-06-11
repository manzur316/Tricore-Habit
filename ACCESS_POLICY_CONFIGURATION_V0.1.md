# ACCESS_POLICY_CONFIGURATION_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** Draft formal inicial / fuente de verdad provisional de configuración y políticas  
**Fecha:** 2026-06-10  
**Documentos padre:** `PROJECT_CHARTER_TRICOR_HABITAT.md`, `DOMAIN_MODEL_V0.1.md`  
**Preparado para:** Arquitectura, QA Harness, diseño de UI, backend y handoff posterior a Claude Code CLI  
**Idioma base:** Español  

---

## 1. Propósito del documento

Este documento define la estrategia inicial de configuración, permisos, presets, dependencias, incompatibilidades y pruebas obligatorias para Tricor Hábitat.

El objetivo es que la flexibilidad del producto venga de configuración controlada y validada, no de código personalizado por cliente ni de parches manuales.

Este documento debe servir como fuente de verdad para:

- Policy Engine.
- Panel de configuración de Tricor Condo.
- Reglas de morosidad.
- Reglas de pagos.
- Reglas de accesos.
- Reglas de QR.
- Reglas de credenciales.
- Reglas de guardias.
- Reglas de Edge.
- Matriz de capacidades por proveedor.
- QA Harness.
- Validaciones antes de activar un proveedor.
- Handoff posterior a Claude Code CLI.

No es todavía la implementación final ni el diseño definitivo de interfaz. Es la especificación base para evitar ambigüedad.

---

## 2. Principio rector

La flexibilidad de Tricor Hábitat debe vivir en un **Policy Engine configurable**.

No se debe desarrollar un flujo diferente para cada condominio.

No todo debe ser checkbox. La configuración debe combinar:

```text
Checkbox = activar/desactivar comportamiento
Número = límite, días, duración, monto, cantidad
Selector = política, perfil, proveedor, rol, estado
Matriz = permisos por rol, capacidades por proveedor, perfiles por zona
Regla = combinación validada de condiciones
```

El usuario normal debe poder operar con presets simples. La configuración avanzada debe existir, pero no debe saturar la experiencia principal.

---

## 3. Principios de configuración

### 3.1 Presets antes que 200 opciones

La UI principal debe presentar presets operativos:

```text
Suave
Estándar
Estricto
Personalizado
```

El preset `Estándar` será el default recomendado para v1.

La configuración avanzada debe quedar detrás de una sección explícita:

```text
Configuración avanzada
```

La configuración técnica debe quedar separada:

```text
Modo técnico / soporte
```

El modo técnico no debe ser para usuarios normales del condominio.

---

### 3.2 Configuración ligada a capacidades reales

No se debe asumir que todos los proveedores soportan todas las capacidades.

Cada configuración debe evaluarse contra:

```text
Capacidades de Tricor
+ Módulos activos del condominio
+ Capacidades del proveedor
+ Mapeos configurados
= Configuración realmente aplicable
```

Ejemplo:

```text
No se puede activar bloqueo por perfil de acceso si el proveedor activo no tiene perfiles mapeados.
No se puede bloquear QR de invitado si QR de invitado está desactivado.
No se puede activar restauración automática por pago si no hay proveedor de pago activo.
```

---

### 3.3 Sin personalizaciones por cliente fuera del marco

El cliente puede configurar dentro del marco del producto.

No se permitirá:

```text
Cliente A tiene código especial.
Cliente B tiene flujo especial.
Cliente C tiene una excepción hardcodeada.
```

Toda variación debe expresarse como:

```text
Preset
Configuración validada
Override permitido
Excepción temporal auditada
```

---

### 3.4 Vista de impacto obligatoria

Cuando una configuración pueda afectar accesos, pagos o credenciales, la UI debe mostrar impacto antes de aplicar.

Ejemplo:

```text
Si activas esta regla:
- 36 propiedades morosas perderán acceso vehicular.
- 36 propiedades morosas perderán QR de invitados.
- 2 Edge tienen comandos pendientes.
- El proveedor activo requiere perfil MOROSO_DEFAULT mapeado.
```

No se deben aplicar cambios críticos sin confirmación auditada.

---

### 3.5 Auditoría obligatoria

Todo cambio de configuración crítico debe registrar:

```text
usuario
rol
scope afectado
valor anterior
valor nuevo
motivo opcional u obligatorio según criticidad
fecha/hora
origen: Platform, Condo, Guard, API, QA, soporte
```

Aplica especialmente a:

- Morosidad.
- Pagos.
- Perfiles de acceso.
- Proveedor.
- Edge.
- Credenciales.
- Permisos de guardia.
- Excepciones temporales.
- Aperturas manuales.
- Restauraciones manuales.

---

### 3.6 QA Harness como requisito de aceptación

Ninguna configuración crítica debe implementarse sin pruebas.

Toda nueva configuración debe incluir:

```text
schema
valor default
dependencias
conflictos
capacidad de proveedor requerida
impacto esperado
pruebas positivas
pruebas negativas
pruebas de regresión
```

Regla para agentes de código:

```text
No agregar configuración nueva sin actualizar QA Harness.
```

---

## 4. Arquitectura conceptual de configuración

### 4.1 Scopes de configuración

La configuración se resuelve por jerarquía.

```text
Platform defaults
↓
Condominio
↓
Privada
↓
Propiedad
↓
Excepción temporal auditada
```

No todo puede sobreescribirse en todos los niveles.

| Nivel | Puede configurar | No debe configurar |
|---|---|---|
| Platform | módulos disponibles, límites SaaS, proveedores habilitados, soporte | reglas operativas diarias del condominio |
| Condominio | pagos, morosidad, accesos, guardias, proveedor, Edge | dinero de otros condominios |
| Privada | overrides limitados si CondoAdmin lo permite | proveedor global, pagos globales, permisos de otras privadas |
| Propiedad | límites y excepciones específicas | reglas globales, proveedor, Edge |
| Excepción temporal | permiso temporal auditado | configuración permanente |

---

### 4.2 EffectivePolicy

El sistema no debe leer checkboxes aislados en cada flujo. Debe resolver una política efectiva.

```text
EffectivePolicy = PlatformDefaults
                + CondominiumPolicy
                + PrivateAreaOverride permitido
                + PropertyOverride permitido
                + TemporaryException activa
                + ProviderCapabilities
                + RuntimeState
```

El resultado debe alimentar:

```text
Policy Engine
→ DesiredAccessState
→ Edge Command
→ Provider Adapter
→ ObservedAccessState
→ Audit
```

---

### 4.3 ConfigRegistry

Toda configuración debe declararse en un registro formal.

Cada entrada debe contener:

```text
key
type
label
description
defaultValue
allowedScopes
requiredCapabilities
dependencies
conflicts
validationRules
impactLevel
qaSuite
```

Ejemplo conceptual:

```text
key: morosity.blockGuestQr
type: boolean
defaultValue: true
allowedScopes: CONDOMINIUM, PRIVATE_AREA
requiredCapabilities: QR_GUEST
dependencies: qr.guest.enabled = true
conflicts: none
impactLevel: CRITICAL_ACCESS
qaSuite: qa:config:morosity
```

---

## 5. Presets operativos

Los presets reducen complejidad para la mayoría de usuarios.

### 5.1 Preset Suave

Uso esperado:

- Condominios que quieren iniciar sin suspensión agresiva.
- Implementación piloto.
- Clientes con reglas internas menos estrictas.

Comportamiento sugerido:

```text
Morosidad activa: sí
Días de gracia: mayor
Bloqueo vehicular: opcional
Bloqueo QR invitados: sí
Acceso peatonal: permitido
Notificaciones: activas
Restauración por pago confirmado: sí
Transferencia manual fallback: sí
```

---

### 5.2 Preset Estándar

Uso esperado:

- Default de Tricor Hábitat v1.
- Equilibrio entre operación real y presión de pago.

Comportamiento confirmado:

```text
Propiedad al corriente:
- Perfil activo permitido por la propiedad

Propiedad morosa:
- Acceso peatonal permitido
- Acceso vehicular bloqueado
- QR de invitados bloqueado
- Zonas no esenciales bloqueadas
- Amenidades futuras bloqueadas
- Pago parcial no restaura por default
- Pago confirmado por pasarela restaura automáticamente
- Transferencia manual existe como fallback auditado
```

---

### 5.3 Preset Estricto

Uso esperado:

- Condominios con políticas más duras.
- Debe requerir confirmación explícita.

Comportamiento sugerido:

```text
Bloqueo vehicular: sí
Bloqueo QR invitados: sí
Bloqueo QR residente: configurable
Bloqueo peatonal: configurable y advertido
Zonas no esenciales: bloqueadas
Pago parcial: no restaura
Excepciones temporales: limitadas
```

Nota: bloquear acceso peatonal puede tener implicaciones operativas, legales o de seguridad. No debe ser default.

---

### 5.4 Preset Personalizado

Uso esperado:

- Condominios con reglas específicas.
- Debe activar configuración avanzada.

Regla:

```text
Personalizado no significa sin validación.
Personalizado sigue sujeto a dependencias, conflictos, capacidades y QA.
```

---

## 6. Configuración por módulo

Las siguientes configuraciones son la base v0.1. No todas deben mostrarse como checkboxes visibles al usuario final desde el inicio. Algunas deben vivir en modo avanzado o técnico.

---

## 6.1 Configuración general de estructura

Panel sugerido:

```text
Tricor Condo → Configuración → Estructura
```

| Key | Tipo | Default | Descripción |
|---|---:|---:|---|
| `structure.enforcePrivateAreaModel` | boolean | true | Mantiene la estructura interna Condominio → Privada → Propiedad. |
| `structure.allowIndependentPrivateLabel` | boolean | true | Permite mostrar una comunidad pequeña como privada independiente sin romper estructura interna. |
| `structure.privateAreaOverrides.enabled` | boolean | true | Permite que ciertas reglas se ajusten por privada. |
| `structure.propertyOverrides.enabled` | boolean | true | Permite límites o excepciones por propiedad. |
| `structure.requirePropertyResponsible` | boolean | true | Toda propiedad operativa debe tener responsable principal. |

Reglas:

```text
No debe existir propiedad operativa fuera de una Privada.
Una privada independiente se modela internamente como Condominio + Privada principal.
La UI puede adaptar etiquetas; el dominio no cambia.
```

---

## 6.2 Configuración de pagos

Panel sugerido:

```text
Tricor Condo → Configuración → Pagos
```

| Key | Tipo | Default v1 | Descripción |
|---|---:|---:|---|
| `payments.online.enabled` | boolean | true | Permite pagos dentro de la plataforma. |
| `payments.provider.active` | selector | Mercado Pago research | Proveedor de pagos activo. |
| `payments.providerConfirmationRestoresAccess` | boolean | true | Pago confirmado por proveedor restaura automáticamente. |
| `payments.manualFallback.enabled` | boolean | true | Permite transferencia manual directa como fallback. |
| `payments.manualProof.required` | boolean | true | Requiere comprobante para pagos manuales. |
| `payments.manualApproval.enabled` | boolean | true | Permite aprobación manual auditada. |
| `payments.manualApproval.restoresAccess` | boolean | true | Pago manual aprobado dispara restauración. |
| `payments.proofUpload.enabled` | boolean | true | Permite subir comprobantes. |
| `payments.partial.enabled` | boolean | false | Permite pagos parciales. |
| `payments.partial.restoresAccess` | boolean | false | Pago parcial restaura acceso. |
| `payments.fines.enabled` | boolean | true | Permite multas. |
| `payments.extraCharges.enabled` | boolean | true | Permite cargos extraordinarios. |
| `payments.recurringMaintenance.enabled` | boolean | true | Permite cuotas recurrentes de mantenimiento. |

Reglas:

```text
Pago por proveedor confirmado = restauración automática.
Comprobante manual no restaura por sí solo.
Transferencia manual requiere aprobación de FINANCE_OPERATOR o CONDO_ADMIN.
Finance no edita permisos directamente; aprueba pago manual y el sistema recalcula acceso.
```

Dependencias:

```text
payments.providerConfirmationRestoresAccess requiere payments.online.enabled = true.
payments.providerConfirmationRestoresAccess requiere proveedor de pago conectado.
payments.manualApproval.restoresAccess requiere payments.manualApproval.enabled = true.
payments.partial.restoresAccess requiere payments.partial.enabled = true.
```

Conflictos:

```text
No permitir restauración automática por comprobante sin pago confirmado.
No permitir transferencia manual sin auditoría si afecta acceso.
```

---

## 6.3 Política de comisiones

Panel sugerido:

```text
Tricor Condo → Configuración → Pagos → Comisiones
```

No debe ser checkbox múltiple. Debe ser selector único.

```text
PaymentFeePolicy:
- ABSORB_BY_CONDO
- PASS_TO_RESIDENT
- INCLUDE_IN_DUE_AMOUNT
```

Regla:

```text
El dinero de mantenimiento entra a la cuenta configurada por el condominio.
Tricor Platform cobra el SaaS por separado.
Tricor no debe ser intermediario financiero del mantenimiento.
```

---

## 6.4 Configuración de morosidad

Panel sugerido:

```text
Tricor Condo → Configuración → Morosidad
```

| Key | Tipo | Default Estándar | Descripción |
|---|---:|---:|---|
| `morosity.enabled` | boolean | true | Activa control de morosidad. |
| `morosity.graceDays.enabled` | boolean | true | Usa días de gracia antes de suspender. |
| `morosity.graceDays.value` | number | configurable | Días de gracia. |
| `morosity.suspensionSchedule.localTime` | time | 00:00-02:00 configurable | Hora de evaluación/suspensión. |
| `morosity.autoSuspendAfterGrace` | boolean | true | Suspende automáticamente al vencer gracia. |
| `morosity.blockVehicularAccess` | boolean | true | Bloquea acceso vehicular. |
| `morosity.allowPedestrianAccess` | boolean | true | Conserva acceso peatonal. |
| `morosity.blockPedestrianAccess` | boolean | false | Bloquea acceso peatonal. |
| `morosity.blockGuestQr` | boolean | true | Bloquea generación/uso de QR invitados. |
| `morosity.blockResidentQr` | boolean | false | Bloquea QR residente. |
| `morosity.blockNonEssentialZones` | boolean | true | Bloquea zonas no esenciales. |
| `morosity.blockFutureAmenities` | boolean | true | Bloquea amenidades futuras si existen. |
| `morosity.notifyBeforeSuspension` | boolean | true | Notifica antes de suspender. |
| `morosity.notifyOnRestoration` | boolean | true | Notifica al restaurar. |
| `morosity.allowTemporaryException` | boolean | true | Permite excepción temporal auditada. |
| `morosity.temporaryException.maxDuration` | duration | configurable | Duración máxima permitida. |
| `morosity.partialPaymentRestores` | boolean | false | Pago parcial restaura acceso. |

Reglas:

```text
Default v1: moroso conserva peatonal y pierde vehicular + QR invitados + zonas no esenciales.
Pagos parciales no restauran por default.
La restauración automática solo ocurre con pago confirmado por proveedor.
La transferencia manual aprobada puede restaurar por flujo auditado.
```

Conflictos:

```text
morosity.blockGuestQr requiere qr.guest.enabled = true.
morosity.blockVehicularAccess requiere access.vehicular.enabled = true.
morosity.blockResidentQr requiere qr.resident.enabled = true.
morosity.blockPedestrianAccess y morosity.allowPedestrianAccess no deben estar ambos activos como intención final.
```

---

## 6.5 Configuración de accesos

Panel sugerido:

```text
Tricor Condo → Configuración → Accesos
```

| Key | Tipo | Default | Descripción |
|---|---:|---:|---|
| `access.pedestrian.enabled` | boolean | true | Activa zona/capacidad peatonal. |
| `access.vehicular.enabled` | boolean | true | Activa zona/capacidad vehicular. |
| `access.nonEssentialZones.enabled` | boolean | false v1 / future-ready | Permite zonas no esenciales futuras. |
| `access.partialSuspension.enabled` | boolean | true | Permite suspensión parcial. |
| `access.profiles.enabled` | boolean | true | Usa perfiles de acceso canónicos. |
| `access.profiles.byPrivateArea` | boolean | true | Permite perfiles por privada. |
| `access.profiles.byProperty` | boolean | true | Permite ajustes por propiedad cuando aplique. |
| `access.requireActiveProperty` | boolean | true | Propiedad debe estar activa para tener acceso. |
| `access.recalculateOnPolicyChange` | boolean | true | Recalcula accesos al cambiar política. |

Perfiles canónicos iniciales:

```text
ACTIVE_ACCESS_PROFILE
MOROSO_DEFAULT_PROFILE
FORMER_RESIDENT_PROFILE
MOVE_OUT_GRACE_PROFILE
TEMPORARY_EXCEPTION_PROFILE
VISITOR_PROFILE
```

Regla:

```text
Propiedad al corriente = todos los permisos permitidos por su perfil de propiedad.
Propiedad morosa default = solo acceso peatonal.
Exresidente = sin accesos, salvo reglas de mudanza/gracia aplicables.
```

Dependencias de proveedor:

```text
access.profiles.enabled requiere que el proveedor soporte perfiles o que exista estrategia alternativa validada.
Cada perfil canónico debe tener mapping hacia el proveedor activo.
No activar proveedor sin mapping de perfiles mínimos.
```

---

## 6.6 Configuración de QR

Panel sugerido:

```text
Tricor Condo → Configuración → QR
```

| Key | Tipo | Default | Descripción |
|---|---:|---:|---|
| `qr.resident.enabled` | boolean | true | Permite QR residente si la instalación lo soporta. |
| `qr.resident.dynamic` | boolean | true | QR residente debe ser dinámico o firmado. |
| `qr.resident.blockWhenMorose` | boolean | false | Bloquea QR residente por morosidad. |
| `qr.guest.enabled` | boolean | true | Permite QR de invitados. |
| `qr.guest.blockWhenMorose` | boolean | true | Bloquea QR invitados por morosidad. |
| `qr.guest.expirationRequired` | boolean | true | Todo QR invitado debe expirar. |
| `qr.guest.singleUseDefault` | boolean | true | QR invitado default de un solo uso. |
| `qr.guest.multiUseWindowAllowed` | boolean | false | Permite QR multiuso por ventana de tiempo. |
| `qr.invalidateOnMorosityChange` | boolean | true | Invalida QR cuando cambia morosidad. |
| `qr.invalidateOnResidentArchive` | boolean | true | Invalida QR al archivar responsable/residente. |
| `qr.offlineValidationWithEdgeSnapshot` | boolean | true | Permite validación offline si Edge tiene snapshot válido. |
| `qr.scanAudit.enabled` | boolean | true | Registra cada lectura de QR. |

Campos complementarios:

```text
qr.guest.defaultDuration
qr.guest.maxActivePerProperty
qr.guest.maxPerDayPerProperty
qr.resident.refreshInterval
```

Reglas:

```text
QR invitado debe estar ligado a propiedad y responsable.
QR invitado no debe seguir válido si la propiedad queda morosa y la política lo bloquea.
QR residente no debe ser captura estática eterna.
```

---

## 6.7 Configuración de credenciales

Panel sugerido:

```text
Tricor Condo → Configuración → Credenciales
```

| Key | Tipo | Default | Descripción |
|---|---:|---:|---|
| `credentials.limitPerProperty.enabled` | boolean | true | Limita credenciales activas por propiedad. |
| `credentials.limitPerProperty.value` | number | configurable | Límite de credenciales activas. |
| `credentials.ownerResponsibleModel.enabled` | boolean | true | Credenciales quedan bajo responsable principal de propiedad. |
| `credentials.residentCanReportLost` | boolean | true | Residente puede reportar pérdida. |
| `credentials.residentCanRegisterNew` | boolean | false | Residente no puede registrar credencial nueva. |
| `credentials.adminCanRegisterNew` | boolean | true | Administración registra nuevas credenciales. |
| `credentials.revokeOnResponsibleChange` | boolean | true | Revoca credenciales al cambiar responsable. |
| `credentials.revokeOnPropertyArchive` | boolean | true | Revoca credenciales al archivar propiedad. |
| `credentials.revokeOnFormerResident` | boolean | true | Revoca credenciales de exresidente/exresponsable. |
| `credentials.inactivityAudit.enabled` | boolean | true | Detecta credenciales sin uso. |
| `credentials.inactivityAudit.days` | number | configurable | Días sin uso para alerta. |
| `credentials.autoRevokeInactive` | boolean | false | Revoca automáticamente por inactividad. |
| `credentials.keepRevokedHistory` | boolean | true | Mantiene historial de revocadas. |
| `credentials.detectProviderOrphans` | boolean | true | Detecta credenciales/personas en proveedor sin vínculo canónico. |

Modelo aprobado:

```text
La propiedad tiene un responsable principal.
Un responsable puede tener varias propiedades.
Una propiedad no tendrá varios propietarios principales.
No se hará censo completo de habitantes.
Las credenciales se controlan por propiedad y responsable.
```

Reglas:

```text
Credencial perdida reportada por residente debe suspender/revocar esa credencial.
El residente no puede registrar reemplazo.
La administración registra nuevas credenciales.
Credenciales sin uso generan alerta no invasiva; no se revocan por default.
Credenciales de exresidentes/exresponsables sí deben revocarse según política.
```

---

## 6.8 Configuración de guardias

Panel sugerido:

```text
Tricor Condo → Configuración → Guardia
```

| Key | Tipo | Default | Descripción |
|---|---:|---:|---|
| `guard.scanQr.enabled` | boolean | true | Guardia puede escanear QR. |
| `guard.viewAccessStatus.enabled` | boolean | true | Guardia puede ver estado operativo de acceso. |
| `guard.showFinancialDetails` | boolean | false | No mostrar detalle financiero al guardia. |
| `guard.showSuspensionReasonDetail` | boolean | false | Mostrar motivo detallado de suspensión. |
| `guard.registerEvents.enabled` | boolean | true | Puede registrar eventos. |
| `guard.roundReports.enabled` | boolean | true | Puede registrar rondines/reportes. |
| `guard.normal.manualOpenWithoutValidQr` | boolean | false | Guardia normal no abre sin QR válido. |
| `guard.supervisor.manualOpen.enabled` | boolean | true | Supervisor puede apertura manual. |
| `guard.supervisor.manualOpen.requiresReason` | boolean | true | Motivo obligatorio. |
| `guard.supervisor.temporaryException.enabled` | boolean | true | Supervisor puede crear excepción temporal justificada. |
| `guard.supervisor.temporaryException.maxDuration` | duration | configurable | Límite de duración. |
| `guard.notifyCondoAdminOnException` | boolean | true | Notifica a CondoAdmin cuando supervisor crea excepción. |
| `guard.providerFailureReport.enabled` | boolean | true | Reportar falla de pluma/proveedor. |

Reglas:

```text
Guardia normal:
- lee QR
- consulta estado
- registra eventos
- no abre sin QR válido
- no edita residentes
- no edita pagos
- no registra credenciales

GuardSupervisor:
- puede apertura manual justificada
- puede excepción temporal justificada
- no edita residentes
- no edita pagos
- no registra credenciales
- toda acción queda auditada
```

Apertura manual y excepción temporal no son lo mismo:

```text
Apertura manual = evento único de abrir una puerta.
Excepción temporal = permiso temporal por duración limitada.
```

---

## 6.9 Configuración de roles

Panel sugerido:

```text
Tricor Condo → Configuración → Roles y permisos
```

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

### PRIVATE_ADMIN

Permisos configurables, acotados a su privada.

| Permiso | Default | Nota |
|---|---:|---|
| Ver propiedades de su privada | true | No otras privadas. |
| Ver residentes/responsables de su privada | true | Limitado a scope. |
| Ver estado de morosidad resumido | configurable | Sin detalle financiero sensible si se desactiva. |
| Solicitar bloqueo de credencial | true | No registra reemplazo. |
| Iniciar solicitud de cambio de responsable | configurable | Requiere aprobación según política. |
| Aprobar pagos | false | No default. |
| Restaurar accesos por pago | false | No directo. |
| Editar proveedor | false | Prohibido. |
| Editar reglas globales | false | Prohibido. |

### FINANCE_OPERATOR

Permisos:

```text
- Ver pagos, deudas, multas y comprobantes.
- Aprobar/rechazar transferencia manual.
- Disparar restauración solo mediante flujo de pago manual aprobado.
- No editar permisos directamente.
- No editar proveedor.
```

### CONDO_ADMIN

Permisos:

```text
- Administra configuración del condominio.
- Administra roles internos.
- Puede aprobar pagos manuales.
- Puede revisar auditoría.
- Puede crear excepciones temporales.
- Puede configurar límites y políticas.
```

---

## 6.10 Configuración de mudanza, exresidentes y salida

Panel sugerido:

```text
Tricor Condo → Operación → Cambios de responsable / mudanza
```

| Key | Tipo | Default | Descripción |
|---|---:|---:|---|
| `moveOut.enabled` | boolean | true | Habilita flujo formal de salida. |
| `moveOut.gracePeriod.enabled` | boolean | true | Permite días de gracia por mudanza. |
| `moveOut.gracePeriod.days` | number | configurable | 0, 1, 2, 3 o personalizado. |
| `moveOut.revokeAtGraceEnd` | boolean | true | Revoca al terminar gracia. |
| `moveOut.archiveFormerResponsible` | boolean | true | Archiva relación anterior. |
| `moveOut.keepHistory` | boolean | true | Mantiene historial. |
| `resident.canRequestMoveOut` | boolean | false v1 | Residente puede solicitar actualización, no ejecutar baja. |

Reglas:

```text
Cambio de responsable no debe borrar historial.
Al finalizar gracia se revocan credenciales anteriores.
El residente puede reportar pérdida de credencial, pero no puede ejecutar cambio de propiedad por sí mismo.
```

---

## 6.11 Configuración de Edge

Panel sugerido:

```text
Tricor Condo → Configuración → Edge
Tricor Platform → Monitoreo → Edge
```

| Key | Tipo | Default | Descripción |
|---|---:|---:|---|
| `edge.requiredForZktecoCvsecurity` | boolean | true | Edge obligatorio para proveedor inicial definido. |
| `edge.multiplePerCondominium.enabled` | boolean | true | Permite varios Edge. |
| `edge.scopeByGuardhouse.enabled` | boolean | true | Edge por caseta cuando aplique. |
| `edge.offlineSnapshot.enabled` | boolean | true | Operación con último snapshot válido. |
| `edge.commandQueue.enabled` | boolean | true | Cola de comandos pendientes. |
| `edge.retryOnReconnect.enabled` | boolean | true | Reintenta al reconectar. |
| `edge.heartbeat.enabled` | boolean | true | Reporta salud al Cloud. |
| `edge.offlineIncident.enabled` | boolean | true | Crea incidente al quedar offline. |
| `edge.localTechnicalUi.enabled` | boolean | true | UI local mínima técnica. |
| `edge.localAdministrativeUi.enabled` | boolean | false | No administración local de residentes/pagos. |
| `edge.providerConnectionTest.required` | boolean | true | Prueba conexión proveedor antes de activar. |

Reglas:

```text
Cloud es fuente de verdad.
Edge ejecuta y observa.
Edge no decide morosidad.
Edge no administra pagos.
Edge no edita residentes.
Si Edge está offline, comandos quedan pendientes y se genera incidente.
```

---

## 6.12 Configuración de proveedores de acceso

Panel sugerido:

```text
Tricor Condo → Configuración → Proveedor de acceso
```

| Key | Tipo | Default | Descripción |
|---|---:|---:|---|
| `provider.access.enabled` | boolean | true | Activa proveedor de acceso. |
| `provider.active` | selector | ZKTeco/CVSecurity v1 | Proveedor activo. |
| `provider.capabilityMatrix.required` | boolean | true | Requiere matriz de capacidades. |
| `provider.connectionTest.required` | boolean | true | Requiere prueba de conexión. |
| `provider.profileMapping.required` | boolean | true | Requiere mapping de perfiles. |
| `provider.doorMapping.required` | boolean | true | Requiere mapping de puertas. |
| `provider.zoneMapping.required` | boolean | true | Requiere mapping de zonas/capacidades. |
| `provider.contractQa.required` | boolean | true | Requiere QA de contratos antes de activar. |
| `provider.unsupportedConfig.blockActivation` | boolean | true | Bloquea configuración incompatible. |
| `provider.changeRequiresAudit` | boolean | true | Cambio de proveedor auditado. |

Regla crítica:

```text
No activar proveedor sin mapeo mínimo:
- perfil activo
- perfil moroso
- perfil exresidente/revocado
- puertas o zonas relevantes
- capacidades requeridas por política activa
```

---

## 6.13 Configuración de auditoría y alertas

Panel sugerido:

```text
Tricor Condo → Configuración → Auditoría
```

| Key | Tipo | Default | Descripción |
|---|---:|---:|---|
| `audit.policyChanges.enabled` | boolean | true | Audita cambios de configuración. |
| `audit.accessChanges.enabled` | boolean | true | Audita cambios de acceso. |
| `audit.paymentChanges.enabled` | boolean | true | Audita pagos. |
| `audit.manualOpen.enabled` | boolean | true | Audita apertura manual. |
| `audit.temporaryException.enabled` | boolean | true | Audita excepciones temporales. |
| `audit.credentialLifecycle.enabled` | boolean | true | Audita credenciales. |
| `alerts.credentialInactivity.enabled` | boolean | true | Alertas no invasivas por inactividad. |
| `alerts.providerOrphans.enabled` | boolean | true | Alerta de credenciales/personas sin vínculo canónico. |
| `alerts.edgeOffline.enabled` | boolean | true | Alerta cuando Edge está offline. |
| `alerts.paymentProviderIssue.enabled` | boolean | true | Alerta de falla de proveedor de pago. |

Regla:

```text
Alertas de inactividad no deben ser invasivas ni revocar por default.
Deben vivir en bandeja de revisión.
```

---

## 7. Dependencias e incompatibilidades críticas

Esta sección debe implementarse en `PolicyConfigValidator` y probarse con QA Harness.

### 7.1 Pagos

```text
Si payments.online.enabled = false:
- payments.providerConfirmationRestoresAccess debe quedar disabled.
- El sistema debe usar fallback manual si está permitido.

Si no hay proveedor de pago conectado:
- No se puede activar restauración automática por proveedor.

Si payments.manualFallback.enabled = true:
- payments.manualProof.required debe ser true o debe existir motivo de excepción.
- payments.manualApproval.enabled debe ser true.
```

---

### 7.2 Morosidad y acceso

```text
Si access.vehicular.enabled = false:
- morosity.blockVehicularAccess no aplica.

Si access.pedestrian.enabled = false:
- morosity.allowPedestrianAccess no aplica.

Si morosity.blockPedestrianAccess = true:
- mostrar advertencia fuerte.
- requerir confirmación auditada.

Si morosity.partialPaymentRestores = true:
- payments.partial.enabled debe ser true.
```

---

### 7.3 QR

```text
Si qr.guest.enabled = false:
- qr.guest.blockWhenMorose no aplica.
- morosity.blockGuestQr no aplica.

Si qr.resident.enabled = false:
- qr.resident.blockWhenMorose no aplica.

Si qr.offlineValidationWithEdgeSnapshot = true:
- edge.offlineSnapshot.enabled debe ser true.
```

---

### 7.4 Proveedor

```text
Si provider.capabilityMatrix no declara soporte para perfiles:
- access.profiles.enabled requiere estrategia alternativa aprobada.

Si el proveedor no soporta una capacidad:
- La configuración debe ocultarse, bloquearse o marcarse como no compatible.

Si una política activa requiere una capacidad no soportada:
- No permitir guardar o no permitir activar la política.
```

---

### 7.5 Guardias

```text
Si guard.supervisor.manualOpen.enabled = true:
- guard.supervisor.manualOpen.requiresReason debe ser true.
- audit.manualOpen.enabled debe ser true.

Si guard.supervisor.temporaryException.enabled = true:
- audit.temporaryException.enabled debe ser true.
- morosity.allowTemporaryException debe ser true.
- debe existir duración máxima.

Guardia normal no debe poder abrir sin QR válido por default.
```

---

### 7.6 Credenciales

```text
Si credentials.residentCanReportLost = true:
- debe existir flujo para suspender/revocar credencial reportada.
- debe notificar a administración.

Si credentials.autoRevokeInactive = true:
- requiere confirmación avanzada.
- no debe ser default.

Si credentials.revokeOnFormerResident = true:
- MoveOutWorkflow debe emitir comandos de revocación.
```

---

## 8. Prioridad de evaluación del Policy Engine

El Policy Engine debe evaluar restricciones en orden para evitar contradicciones.

Orden recomendado:

```text
1. Estado estructural
   - Condominio activo
   - Privada activa
   - Propiedad activa

2. Relación con propiedad
   - Responsable activo
   - Relación vigente
   - Mudanza / gracia

3. Estado de credencial
   - Activa
   - Perdida
   - Revocada
   - Archivada

4. Estado financiero
   - Al corriente
   - Morosa
   - Pago confirmado pendiente de aplicar
   - Pago manual pendiente de revisión

5. Excepción temporal auditada
   - Activa
   - Vigente
   - Permitida por rol y duración

6. Política de acceso
   - Perfil activo
   - Perfil moroso
   - Perfil de gracia
   - Perfil temporal
   - Perfil revocado

7. Capacidades de proveedor
   - Soporte de perfiles
   - Soporte de puertas/zonas
   - Soporte de credenciales
   - Soporte de QR

8. Estado Edge/proveedor
   - Online
   - Offline
   - Pendiente
   - Fallido
```

Regla de seguridad:

```text
Una credencial revocada, perdida o archivada no debe ser reactivada por una excepción temporal sin flujo explícito de reemplazo/restauración.
```

---

## 9. Estados de acceso calculado

Estados conceptuales iniciales:

```text
ACCESS_ACTIVE
ACCESS_MOROSE_RESTRICTED
ACCESS_PAYMENT_CONFIRMED_PENDING_PROVIDER
ACCESS_MANUAL_PAYMENT_REVIEW_PENDING
ACCESS_TEMPORARY_EXCEPTION_ACTIVE
ACCESS_MOVE_OUT_GRACE_PERIOD
ACCESS_REVOKED
ACCESS_PROVIDER_PENDING
ACCESS_PROVIDER_FAILED
```

### 9.1 Pago confirmado pero proveedor pendiente

Si el pago está confirmado, pero Edge/proveedor no ha aplicado restauración:

```text
Pago: confirmado
Acceso: ACCESS_PAYMENT_CONFIRMED_PENDING_PROVIDER
```

No se debe mostrar falsamente como restaurado si el proveedor todavía no lo aplicó.

---

## 10. Vista de impacto

Toda configuración crítica debe calcular impacto antes de aplicar.

Ejemplos:

### Cambio de morosidad

```text
Cambio: bloquear QR invitados a morosos
Impacto:
- 22 propiedades morosas afectadas
- 41 QR invitados activos serán invalidados
- 2 Edge recibirán comandos
- Proveedor activo soporta QR: sí/no
```

### Cambio de acceso vehicular

```text
Cambio: bloquear acceso vehicular a morosos
Impacto:
- 18 propiedades afectadas
- Requiere mapping de zona vehicular
- Edge activo: sí/no
```

### Cambio de proveedor

```text
Cambio: proveedor activo
Impacto:
- Requiere remapear perfiles
- Requiere remapear puertas
- Requiere QA de contrato
- Comandos pendientes deben quedar pausados
```

---

## 11. QA Harness requerido

El QA Harness debe validar configuraciones, dependencias, conflictos, proveedores y flujos.

Suites obligatorias:

```text
qa:config:defaults
qa:config:valid
qa:config:invalid
qa:config:dependencies
qa:config:provider-capabilities
qa:config:morosity-policy
qa:config:payment-policy
qa:config:qr-policy
qa:config:guard-policy
qa:config:credential-lifecycle
qa:config:edge-policy
qa:config:role-policy
qa:provider-contracts
qa:flow:property-debt-access-lifecycle
```

---

## 12. Casos de prueba obligatorios

### 12.1 Defaults

```text
1. Propiedad al corriente recibe ACTIVE_ACCESS_PROFILE.
2. Propiedad morosa recibe MOROSO_DEFAULT_PROFILE.
3. Moroso conserva peatonal por default.
4. Moroso pierde vehicular por default.
5. Moroso pierde QR invitados por default.
6. Pago parcial no restaura por default.
```

### 12.2 Pagos

```text
1. Pago confirmado por proveedor restaura acceso automáticamente.
2. Pago pendiente no restaura.
3. Transferencia manual queda pendiente de revisión.
4. Finance aprueba transferencia manual y el sistema recalcula acceso.
5. Finance no edita permisos directamente.
6. Comprobante subido no restaura por sí solo.
```

### 12.3 Morosidad

```text
1. Al vencer gracia se genera suspensión.
2. Antes de vencer gracia no se suspende.
3. Cambio de política recalcula DesiredAccessState.
4. Activar bloqueo peatonal requiere confirmación auditada.
```

### 12.4 QR

```text
1. QR invitado se invalida al cambiar a moroso si política lo bloquea.
2. QR invitado expira.
3. QR invitado de un solo uso no puede reutilizarse.
4. QR residente no es válido si la credencial/responsable está archivado.
```

### 12.5 Guardias

```text
1. Guardia normal no abre sin QR válido.
2. Guardia normal puede registrar evento.
3. GuardSupervisor puede apertura manual con motivo.
4. GuardSupervisor puede excepción temporal con motivo y duración.
5. Apertura manual queda auditada.
6. Excepción temporal notifica a CondoAdmin.
```

### 12.6 Credenciales

```text
1. Residente puede reportar tarjeta/tag perdida.
2. Tarjeta reportada como perdida queda suspendida/revocada.
3. Residente no puede registrar nueva tarjeta.
4. Administración puede registrar reemplazo.
5. Credencial sin uso genera alerta no invasiva.
6. Credencial de exresidente se revoca al cerrar mudanza.
7. Credencial activa en proveedor sin vínculo canónico genera alerta.
```

### 12.7 Edge y proveedor

```text
1. Configuración incompatible con proveedor se bloquea.
2. Proveedor sin mapping de perfil moroso no puede activarse.
3. Edge offline deja comandos pendientes.
4. Edge reconecta y ejecuta cola.
5. Estado observado confirma o reporta falla.
```

---

## 13. Reglas para UI de configuración

### 13.1 No saturar

La pantalla principal debe mostrar:

```text
Preset actual
Resumen de impacto
Estado de proveedor
Estado de pagos
Estado de Edge
```

La configuración avanzada debe estar agrupada.

### 13.2 Agrupación recomendada

```text
Configuración
├── General
├── Estructura
├── Pagos
├── Morosidad
├── Accesos
├── QR
├── Credenciales
├── Guardia
├── Roles
├── Proveedor
├── Edge
└── Auditoría
```

### 13.3 Modo técnico

El modo técnico debe incluir:

```text
ProviderCapabilityMatrix
Provider mappings
Edge diagnostics
QA provider contract status
Logs técnicos
Pruebas de conexión
```

No debe incluir administración de residentes ni pagos.

---

## 14. Reglas de implementación para Claude Code CLI

El agente de implementación debe seguir estas reglas:

```text
1. No hardcodear reglas de un cliente.
2. No asumir que todos los proveedores soportan todo.
3. No implementar configuración sin ConfigRegistry.
4. No implementar configuración sin PolicyConfigValidator.
5. No implementar configuración crítica sin auditoría.
6. No implementar configuración crítica sin QA Harness.
7. No modificar acceso directo en proveedor sin DesiredAccessState.
8. No permitir que Finance edite permisos directamente.
9. No permitir que Guard edite residentes, pagos o credenciales nuevas.
10. No permitir que Resident registre nuevas credenciales.
11. No mostrar configuraciones incompatibles como si fueran aplicables.
12. No romper la estructura Condominio → Privada → Propiedad.
```

Todo PR relacionado con configuración debe incluir:

```text
- actualización de schema
- actualización de defaults
- validaciones
- pruebas QA
- actualización documental
- reporte de impacto si aplica
```

---

## 15. Criterios de aceptación del módulo de configuración v1

El módulo puede considerarse aceptado cuando:

```text
1. Existen presets Suave, Estándar, Estricto y Personalizado.
2. Estándar produce el perfil moroso default definido.
3. Existe ConfigRegistry tipado.
4. Existe PolicyConfigValidator.
5. Existe ProviderCapabilityMatrix.
6. La UI oculta o bloquea configuraciones no soportadas.
7. Los cambios críticos quedan auditados.
8. El QA Harness prueba configuraciones válidas e inválidas.
9. El QA Harness prueba provider contracts.
10. El flujo pago confirmado → restauración está cubierto.
11. El fallback manual está cubierto.
12. El ciclo de credenciales obsoletas está cubierto.
13. GuardSupervisor está limitado y auditado.
14. Edge offline queda cubierto.
15. No existe lógica personalizada por cliente fuera del Policy Engine.
```

---

## 16. Decisiones cerradas en este documento

```text
1. La flexibilidad vive en configuración controlada.
2. No todo será checkbox.
3. Se usarán presets para evitar saturación.
4. La configuración depende de capacidades del proveedor.
5. El preset default v1 será Estándar.
6. Moroso default conserva peatonal y pierde vehicular + QR invitados.
7. Pagos parciales no restauran por default.
8. Pago confirmado por proveedor restaura automáticamente.
9. Transferencia manual existe como fallback auditado.
10. Finance aprueba pagos manuales, no edita permisos.
11. GuardSupervisor puede apertura manual y excepción temporal justificadas.
12. Residente puede reportar credencial perdida.
13. Residente no puede registrar nueva credencial.
14. Credenciales sin uso generan alertas no invasivas.
15. Exresidentes/exresponsables deben perder credenciales al cerrar salida.
16. Edge debe operar con cola, snapshot, heartbeat y UI técnica mínima.
17. QA Harness debe probar configuración, dependencias, incompatibilidades y proveedores.
```

---

## 17. Pendientes no bloqueantes

Estos puntos no bloquean la arquitectura v0.1, pero deben resolverse en documentos posteriores:

```text
1. Definir límites default de credenciales por propiedad.
2. Definir duración default de excepción temporal de GuardSupervisor.
3. Completar investigación de Mercado Pago.
4. Completar investigación de proveedor Hikvision.
5. Definir pricing comercial por condominio, propiedad, puerta, Edge o uso.
6. Diseñar UI exacta de configuración.
7. Diseñar manual de usuario de Tricor Condo.
8. Diseñar manual de usuario de Tricor Guard.
9. Diseñar manual de usuario de Tricor Resident.
```

---

## 18. Próximos documentos recomendados

Después de este documento, los siguientes entregables recomendados son:

```text
CLOUD_EDGE_ARCHITECTURE_V0.1.md
QA_HARNESS_SPEC_V0.1.md
ROADMAP_V0.1.md
AI_AGENT_RULES_AND_HANDOFF.md
USER_MANUAL_PLAN.md
```


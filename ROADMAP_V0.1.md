# ROADMAP_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado documental:** CERTIFIED como roadmap base actualizado  
**Estado de implementación:** BLOCKED hasta bootstrap técnico del repo  
**Fecha:** 2026-06-11  
**Archivo autoritativo:** `ROADMAP_V0.1.md`  
**Modo de actualización:** actualización controlada del mismo archivo, sin crear duplicados

---

## 0. Control de auditoría

### 0.1 Veredicto de la tarea

```text
Tarea: actualizar ROADMAP_V0.1.md
Veredicto: MATCH
Motivo: los documentos bloqueantes previos ya fueron consolidados en main:
- DOCS_INDEX_AND_DEPENDENCY_MAP.md existe como índice maestro DRAFT.
- PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md está CERTIFIED.
- QA_HARNESS_SPEC_V0.1.md está CERTIFIED como especificación base.
```

### 0.2 Archivo actualizado

```text
Archivo objetivo: ROADMAP_V0.1.md
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
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
QA_HARNESS_SPEC_V0.1.md
AI_AGENT_RULES_AND_HANDOFF.md
REPO_STRUCTURE_V0.1.md
USER_MANUAL_PLAN.md
README.md
DOCS_INDEX_AND_DEPENDENCY_MAP.md
```

### 0.4 Documentos fuente usados

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md
PROJECT_CHARTER_TRICOR_HABITAT.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
PROVIDER_RESEARCH_HIKVISION_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
QA_HARNESS_SPEC_V0.1.md
REPO_STRUCTURE_V0.1.md
USER_MANUAL_PLAN.md
AI_AGENT_RULES_AND_HANDOFF.md
```

### 0.5 Estado del documento

```text
Documento: CERTIFIED
Uso permitido: planeación de ejecución, handoff posterior, control de orden y gates
Uso no permitido: iniciar implementación sin AI_AGENT_RULES_AND_HANDOFF actualizado y sin bootstrap técnico aprobado
```

---

## 1. Propósito del documento

Este documento define el roadmap actualizado de Tricor Hábitat.

Su función es convertir la arquitectura, dominio, configuración, Cloud/Edge, providers, pagos y QA Harness en una secuencia de trabajo controlada para evitar:

```text
- parches improvisados
- implementación por suposiciones
- dependencias rotas
- agentes IA editando sin pruebas
- integración directa con proveedores sin contratos
- handoff prematuro a Claude Code CLI
```

El roadmap no es un calendario. Es una secuencia de ejecución por fases, gates y dependencias.

---

## 2. Principio rector

El orden correcto es:

```text
Documentos certificados
→ Índice y dependencias
→ Provider Strategy
→ QA Harness
→ Roadmap
→ AI Handoff
→ README
→ Bootstrap repo
→ Contratos canónicos
→ QA Harness inicial
→ Backend core
→ Policy Engine
→ Pagos
→ Provider mocks
→ Edge
→ Provider real lab
→ Interfaces Web/PWA
→ Piloto
→ Comercialización
```

Regla dura:

```text
No implementar proveedor real, pagos reales ni Edge productivo antes de contratos, QA Harness y laboratorio.
```

---

## 3. Estado documental actual

### 3.1 Documentos certificados o usables como base

```text
PROJECT_CHARTER_TRICOR_HABITAT.md                  CERTIFIED
DOMAIN_MODEL_V0.1.md                               CERTIFIED
ACCESS_POLICY_CONFIGURATION_V0.1.md                CERTIFIED
CLOUD_EDGE_ARCHITECTURE_V0.1.md                    CERTIFIED
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md     CERTIFIED_FOR_RESEARCH
PROVIDER_RESEARCH_HIKVISION_V0.1.md                CERTIFIED_FOR_RESEARCH
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md    CERTIFIED_FOR_RESEARCH
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md  CERTIFIED
QA_HARNESS_SPEC_V0.1.md                            CERTIFIED
USER_MANUAL_PLAN.md                                CERTIFIED
```

### 3.2 Documentos en revisión o bloqueados

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md                   DRAFT
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md   REVIEW_REQUIRED hasta ejecución real
ROADMAP_V0.1.md                                    CERTIFIED tras esta actualización
AI_AGENT_RULES_AND_HANDOFF.md                      REVIEW_REQUIRED
REPO_STRUCTURE_V0.1.md                             REVIEW_REQUIRED
README.md                                          REVIEW_REQUIRED
```

### 3.3 Bloqueo principal actual

```text
Handoff a Claude Code CLI: BLOCKED
Motivo: falta actualizar AI_AGENT_RULES_AND_HANDOFF.md y README.md.
```

---

## 4. Estado de implementación actual

```text
Código productivo: NO iniciado
Repo técnico monorepo: NO iniciado
QA Harness CLI: NO implementado
Backend Cloud: NO implementado
Edge Agent: NO implementado
Provider adapters: NO implementados
Pagos Mercado Pago: NO implementado
Interfaces Web/PWA: NO implementadas
Laboratorio ZKTeco: documentado, no ejecutado
Laboratorio Hikvision: pendiente de hardware/dispositivo real
```

Esto es correcto para la fase actual. El proyecto sigue en fase de arquitectura/documentación controlada.

---

## 5. Objetivo v1

Tricor Hábitat v1 debe resolver:

```text
Control de acceso residencial basado en pagos y morosidad
```

Flujo central:

```text
Propiedad
→ cargo/deuda
→ pago o morosidad
→ Policy Engine
→ DesiredAccessState
→ AccessCommand
→ Edge
→ Access Provider Adapter
→ proveedor físico
→ ObservedAccessState
→ auditoría
```

Superficies v1:

```text
Tricor Platform
Tricor Condo
Tricor Guard
Tricor Resident
```

No son cuatro apps separadas en v1. Son cuatro superficies dentro de una app web/PWA modular.

---

## 6. Fuera de alcance v1

No implementar en v1:

```text
- facturación fiscal propia
- contabilidad fiscal completa
- chat entre residentes
- red social
- amenidades como módulo completo
- WhatsApp
- apps móviles nativas completas
- automatizaciones libres por cliente
- vehículos como entidad principal
- múltiples proveedores reales simultáneos sin laboratorio
- Tricor como intermediario financiero del mantenimiento
- módulos premium no aprobados
```

Permitido como research/futuro:

```text
- Telegram para soporte/alertas/reportes
- integración de facturación externa
- exportaciones contables
- ticketing limitado
- Flutter futuro
- Hikvision como segundo access provider, después de laboratorio
```

---

## 7. Stack aprobado

```text
Monorepo: pnpm workspace + Turborepo
Backend Cloud: NestJS
Base de datos: PostgreSQL
ORM: Prisma
Frontend Web/PWA: Next.js
Edge: Node.js / TypeScript
Storage: S3-compatible
QA Harness: CLI TypeScript
Mobile futuro: Flutter
Docker: sí
```

Regla:

```text
El stack no cambia sin RFC técnico formal.
```

---

## 8. Entregas objetivo

### 8.1 MVP técnico

Demuestra que el sistema funciona internamente.

Debe incluir:

```text
- monorepo limpio
- contratos canónicos
- dominio base
- base de datos inicial
- QA Harness foundation
- provider mock
- payment mock
- edge simulator
- Policy Engine
- flujos críticos automatizados
```

No requiere proveedor físico ni UI completa.

### 8.2 MVP piloto

Permite operar en un condominio real controlado.

Debe incluir:

```text
- Tricor Platform mínimo
- Tricor Condo funcional
- Tricor Guard PWA mínima
- Tricor Resident PWA mínima
- Mercado Pago sandbox/real controlado
- transferencia manual fallback
- Edge real
- ZKTeco/CVSecurity validado en laboratorio
- auditoría
- manuales mínimos
```

### 8.3 MVP comercial

Permite cobrar con menor fricción.

Debe incluir:

```text
- onboarding operativo
- soporte interno
- monitoreo
- métricas de uso
- planes y cobro SaaS
- procedimientos de instalación
- documentación de usuario
- runbooks de incidentes
```

---

## 9. Gates obligatorios

### 9.1 Documentation Gate

Debe pasar antes de entregar a Claude Code CLI.

Mínimo:

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md actualizado
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md CERTIFIED
QA_HARNESS_SPEC_V0.1.md CERTIFIED
ROADMAP_V0.1.md CERTIFIED
AI_AGENT_RULES_AND_HANDOFF.md actualizado
README.md actualizado
```

Estado actual:

```text
PASSED parcialmente
BLOCKED por AI_AGENT_RULES_AND_HANDOFF.md y README.md
```

### 9.2 Repo Bootstrap Gate

Debe pasar antes de implementar dominio.

Requiere:

```text
pnpm workspace
Turborepo
apps/api
apps/web
apps/edge
apps/qa
packages/contracts
packages/domain
packages/db
packages/provider-sdk
packages/ui
packages/config
.env.example
CLAUDE.md
scripts mínimos
```

### 9.3 Domain Gate

Debe pasar antes de pagos, Edge o providers.

Requiere:

```text
entidades canónicas
eventos de dominio
Policy Engine base
AccessProfile
DesiredAccessState
AccessCommand
AuditEvent
pruebas unitarias
```

### 9.4 QA Harness Gate

Debe pasar antes de integrar proveedores reales.

Requiere:

```text
CLI QA
fixtures determinísticos
provider mock
payment mock
edge simulator
reportes md/json
suites de configuración
suites de dominio
suites de provider contract mock
suites de pagos mock
```

### 9.5 Payment Gate

Debe pasar antes de restaurar acceso por pago real.

Requiere:

```text
Mercado Pago sandbox
webhook signature validation
GET order/payment confirmation
idempotencia
externalReference
no mezclar dinero SaaS con mantenimiento
manual fallback auditado
```

### 9.6 Provider Lab Gate

Debe pasar antes de provider real productivo.

Requiere:

```text
ZKTeco lab test PASS
remote open probado
Pagador → Moroso → Pagador probado
QR probado si se usa
transacciones/observed state probados
logs sin secretos
operaciones destructivas controladas
```

Hikvision requiere gate separado:

```text
Hikvision ISAPI PBAC lab con hardware/dispositivo real
UserInfo/CardInfo/doorRight/RightPlan/RemoteControl/AcsEvent/QRCodeEvent probados
```

---

## 10. Roadmap por fases

## Fase 0 — Cierre documental y coherencia

**Objetivo:** dejar la documentación en estado coherente para handoff.

**Estado actual:** en progreso.

**Entregables:**

```text
PROJECT_CHARTER_TRICOR_HABITAT.md                  DONE
DOMAIN_MODEL_V0.1.md                               DONE
ACCESS_POLICY_CONFIGURATION_V0.1.md                DONE
CLOUD_EDGE_ARCHITECTURE_V0.1.md                    DONE
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md     DONE
PROVIDER_RESEARCH_HIKVISION_V0.1.md                DONE
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md    DONE
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md   DONE / REVIEW_REQUIRED hasta ejecución
DOCS_INDEX_AND_DEPENDENCY_MAP.md                   DONE / DRAFT
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md  DONE / CERTIFIED
QA_HARNESS_SPEC_V0.1.md                            DONE / CERTIFIED
ROADMAP_V0.1.md                                    DONE tras esta actualización
AI_AGENT_RULES_AND_HANDOFF.md                      PENDING UPDATE
README.md                                          PENDING UPDATE
```

**Criterio de terminado:**

```text
AI_AGENT_RULES_AND_HANDOFF.md actualizado
README.md actualizado
orden de lectura claro
hitos bloqueados claramente marcados
```

**Prohibido:**

```text
iniciar código productivo
entregar a Claude sin handoff actualizado
implementar provider real
```

---

## Fase 1 — AI Handoff y README

**Objetivo:** preparar el paquete final para Claude Code CLI.

**Entregables:**

```text
AI_AGENT_RULES_AND_HANDOFF.md actualizado
README.md actualizado
orden oficial de lectura
reglas de no implementación prematura
reglas de un solo ejecutor
reglas de QA obligatorio
reglas de providers/lab
```

**Criterio de terminado:**

```text
Claude puede leer el repo y saber:
- qué documentos son fuente de verdad
- qué documentos están bloqueados
- qué no debe implementar todavía
- qué archivo leer primero
- qué pruebas deben existir antes de código crítico
```

**Prohibido:**

```text
modificar documentos base certificados sin motivo
crear archivos duplicados
entregar prompts ambiguos
```

---

## Fase 2 — Bootstrap del monorepo

**Objetivo:** crear estructura técnica limpia.

**Entregables:**

```text
package.json raíz
pnpm-workspace.yaml
turbo.json
tsconfig.base.json
apps/api
apps/web
apps/edge
apps/qa
packages/contracts
packages/domain
packages/db
packages/provider-sdk
packages/ui
packages/config
docker-compose.yml
.env.example
.gitignore
CLAUDE.md
```

**QA mínimo:**

```text
pnpm install
pnpm lint
pnpm typecheck
pnpm test
```

**Criterio de terminado:**

```text
repo instala
scripts existen
typecheck base pasa
no hay lógica productiva todavía
```

---

## Fase 3 — Contratos canónicos y dominio puro

**Objetivo:** implementar dominio sin proveedor real.

**Entregables:**

```text
packages/contracts
packages/domain
Condominium
PrivateArea
Property
ResponsiblePerson
Credential
Charge
Debt
PaymentOrder
PaymentAttempt
PaymentProof
MorosityPolicy
AccessProfile
AccessZone
DesiredAccessState
ObservedAccessState
AccessCommand
AuditEvent
ProviderInstallation
ProviderMapping
ProviderCapabilityMatrix
```

**QA requerido:**

```text
pnpm qa domain
pnpm qa contracts
pnpm test packages/domain
```

**Prohibido:**

```text
endpoints de proveedor en dominio
Mercado Pago en dominio
ZKTeco/Hikvision en domain rules
```

---

## Fase 4 — Base de datos y migraciones

**Objetivo:** persistir el dominio.

**Entregables:**

```text
Prisma schema
migraciones
seeds QA
auditoría
provider mappings
payment provider installation
access commands
observed states
incidents
files metadata
```

**QA requerido:**

```text
pnpm db:migrate
pnpm db:seed
pnpm qa db
```

**Prohibido:**

```text
guardar comprobantes binarios en PostgreSQL
crear vehículos como módulo v1
permitir múltiples propietarios principales por propiedad
```

---

## Fase 5 — QA Harness foundation

**Objetivo:** construir el QA antes de módulos críticos.

**Entregables:**

```text
apps/qa
CLI
EnvironmentChecker
FixtureFactory
DbInspector
ApiClient
ReportBuilder
ProviderMockServer
PaymentMockServer
EdgeSimulator
PolicyConfigTestRunner
ProviderContractRunner
PaymentTestRunner
```

**Comandos mínimos:**

```text
pnpm qa health
pnpm qa config defaults
pnpm qa config dependencies
pnpm qa flow property-debt-access-lifecycle
pnpm qa provider-contract generic-mock
pnpm qa payments mercado-pago --mock
pnpm qa report
```

**Criterio de terminado:**

```text
QA produce reportes md/json
QA falla con mensajes accionables
QA no requiere hardware real para pruebas diarias
```

---

## Fase 6 — Backend Cloud core

**Objetivo:** implementar API central sin providers reales.

**Entregables:**

```text
Auth
Roles/scopes
Tenant isolation
Condominios
Privadas
Propiedades
Responsables
Credenciales
Cargos/deudas
Morosidad
Policy Engine orchestration
AccessCommand queue
AuditEvent
OperationalIncident
ProviderInstallation mock
```

**QA requerido:**

```text
pnpm qa health
pnpm qa domain
pnpm qa flow property-debt-access-lifecycle
```

---

## Fase 7 — Configuración y Policy Engine

**Objetivo:** implementar configuración controlada.

**Entregables:**

```text
ConfigRegistry
EffectivePolicy
PolicyConfigValidator
presets Suave/Estándar/Estricto/Personalizado
vista de impacto
capability gates
validaciones de conflictos
```

**QA requerido:**

```text
pnpm qa config defaults
pnpm qa config valid
pnpm qa config invalid
pnpm qa config dependencies
pnpm qa config provider-capabilities
pnpm qa config impact
```

---

## Fase 8 — Pagos Mercado Pago mock y sandbox

**Objetivo:** validar pagos sin acceso físico real.

**Entregables:**

```text
PaymentProviderAdapter conceptual
MercadoPagoPaymentAdapter sandbox
PaymentOrder
PaymentAttempt
PaymentProviderEvent
webhook handler
signature validator
GET order/payment confirmation
manual fallback
idempotency
```

**QA requerido:**

```text
pnpm qa payments mercado-pago --mock
pnpm qa payments mercado-pago-webhook --mock
pnpm qa payments manual-fallback
```

**Sandbox gate:**

```text
pnpm qa payments mercado-pago --sandbox
```

**Prohibido:**

```text
restaurar acceso solo por webhook sin consulta API
mezclar dinero de mantenimiento con SaaS
exponer access tokens
```

---

## Fase 9 — Provider SDK y Generic Mock Provider

**Objetivo:** validar el contrato provider-neutral.

**Entregables:**

```text
packages/provider-sdk
AccessProviderAdapter interface
ProviderCapabilityMatrix
ProviderOperationResult
GenericMockProvider
ProviderContractRunner
```

**QA requerido:**

```text
pnpm qa provider-contract generic-mock
pnpm qa flow property-debt-access-lifecycle
pnpm qa edge command-idempotency
```

---

## Fase 10 — Edge simulator y Edge Agent inicial

**Objetivo:** preparar ejecución local sin proveedor real.

**Entregables:**

```text
apps/edge
Edge enrollment conceptual
heartbeat
command pull
command ack/result
local queue
offline/reconnect
provider status
UI local técnica mínima
```

**QA requerido:**

```text
pnpm qa edge health
pnpm qa edge sync
pnpm qa edge offline-reconnect
pnpm qa edge command-idempotency
```

---

## Fase 11 — ZKTeco/CVSecurity laboratorio

**Objetivo:** ejecutar laboratorio real antes de implementación productiva.

**Fuente:**

```text
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
```

**Entregables:**

```text
lab report manual/QA
confirmación de endpoint behavior
Pagador → Moroso → Pagador
card/tag behavior
QR behavior si aplica
remote open behavior
transaction observed state
errores reales
```

**Gate:**

```text
ZKTeco lab PASS requerido para provider real.
```

**Estado actual:**

```text
BLOCKED hasta ejecución de laboratorio.
```

---

## Fase 12 — ZKTeco/CVSecurity adapter implementation

**Objetivo:** implementar adapter solo después del lab PASS.

**Entregables:**

```text
ZktecoCvsecurityAdapter
client HTTP local
person/card/accLevel/door/transaction wrappers
error normalizer
mapping validator
access profile application
reconciliation
hardware-smoke guardrails
```

**QA requerido:**

```text
pnpm qa provider-contract zkteco --mock
pnpm qa provider-contract zkteco --hardware-smoke
```

**Prohibido:**

```text
usar endpoints no probados para producción
hacer openDoor sin confirmación humana en hardware-smoke
loggear access_token
```

---

## Fase 13 — Hikvision research/lab track

**Objetivo:** preparar segundo provider sin bloquear MVP ZKTeco.

**Fuente:**

```text
PROVIDER_RESEARCH_HIKVISION_V0.1.md
```

**Entregables futuros:**

```text
Hikvision lab plan
Hikvision adapter spec
hardware/device lab
UserInfo/CardInfo/doorRight/RightPlan tests
RemoteControl/door tests
AcsEvent/QRCodeEvent tests
```

**Estado:**

```text
CERTIFIED_FOR_RESEARCH
BLOCKED para implementación hasta laboratorio
```

---

## Fase 14 — Tricor Platform y Tricor Condo Web/PWA

**Objetivo:** construir superficies administrativas mínimas.

**Entregables:**

```text
Tricor Platform mínimo
Tricor Condo funcional
estructura Condominio → Privada → Propiedad
finanzas
morosidad
configuración
proveedor/edge status
credenciales
logs/auditoría
```

**QA requerido:**

```text
pnpm qa flow property-debt-access-lifecycle
pnpm qa config impact
pnpm qa security roles
```

---

## Fase 15 — Tricor Guard y Tricor Resident PWA

**Objetivo:** operar flujo mínimo móvil sin app nativa.

**Tricor Guard:**

```text
scan QR
estado acceso
apertura con QR válido
GuardSupervisor apertura manual
GuardSupervisor excepción temporal limitada
eventos/rondines básicos
```

**Tricor Resident:**

```text
estado de cuenta
pagos
comprobantes
QR residente
QR invitado
estado de acceso
tarjeta perdida
```

**QA requerido:**

```text
pnpm qa flow guest-qr-morosity-policy
pnpm qa flow lost-credential
pnpm qa flow manual-payment-fallback-lifecycle
```

---

## Fase 16 — Staging integral

**Objetivo:** validar sistema completo antes de piloto.

**Incluye:**

```text
API staging
DB staging
PWA staging
Edge staging
payment sandbox
provider mock
ZKTeco hardware-smoke opcional
reportes QA
```

**Gate:**

```text
pnpm qa ci
pnpm qa flow core
pnpm qa payments mercado-pago --sandbox
pnpm qa provider-contract zkteco --hardware-smoke, si lab disponible
```

---

## Fase 17 — Piloto controlado

**Objetivo:** operar un condominio real con alcance limitado.

**Requisitos:**

```text
manuales mínimos
soporte definido
edge instalado
provider validado
pagos controlados
fallback manual habilitado
auditoría activa
plan de reversa
```

**Criterio de éxito:**

```text
propiedad al corriente = acceso correcto
propiedad morosa = restricciones correctas
pago confirmado = restauración automática
transferencia manual = aprobación auditada
guardia opera sin editar datos
resident usa pago/QR/credencial perdida
```

---

## Fase 18 — MVP comercial

**Objetivo:** preparar venta repetible.

**Entregables:**

```text
planes SaaS
onboarding
manuales
runbooks
soporte
monitoreo
métricas
release notes
known limitations
```

---

## 11. Dependencias críticas

```text
AI Handoff depende de Roadmap actualizado.
Claude handoff depende de AI Handoff actualizado.
Bootstrap repo depende de README + AI Handoff.
QA Harness implementation depende de QA_HARNESS_SPEC certificado.
Provider real depende de Provider Strategy + QA + lab.
Mercado Pago real depende de sandbox/webhook/API confirmation.
ZKTeco real depende de lab PASS.
Hikvision real depende de lab nuevo.
```

---

## 12. Riesgos principales

| Riesgo | Impacto | Mitigación |
|---|---:|---|
| Claude implementa desde docs viejos | Alto | AI Handoff actualizado + Docs Index |
| QA Harness no se construye temprano | Alto | QA Foundation antes de backend crítico |
| Provider real se implementa sin lab | Alto | Provider Lab Gate obligatorio |
| Mercado Pago restaura solo por webhook | Alto | Confirmación API obligatoria |
| Edge decide negocio | Alto | Cloud decide, Edge ejecuta |
| Configuración excesiva rompe flujo | Medio | presets + PolicyConfigValidator |
| README vacío confunde repo | Medio | actualizar README después de AI Handoff |
| Repo docs-only vs repo técnico no definido | Medio | resolver en REPO_STRUCTURE cuando toque |

---

## 13. Definition of Done por etapa

### Docs Done

```text
documentos actualizados
estados claros
bloqueos claros
sin duplicados
orden de lectura definido
```

### Technical Bootstrap Done

```text
instala
scripts mínimos
estructura creada
CLAUDE.md presente
sin lógica productiva prematura
```

### QA Done

```text
CLI ejecuta
fixtures determinísticos
reportes md/json
mocks funcionando
flows core corren
```

### Provider Done

```text
spec certificada
lab plan ejecutado
PASS report
adapter mock pasa
hardware-smoke pasa
logs sin secretos
```

### Payment Done

```text
webhook validado
API confirmation validada
idempotencia validada
manual fallback validado
no mezcla fondos
```

---

## 14. Próximo paso inmediato

Actualizar un solo archivo:

```text
AI_AGENT_RULES_AND_HANDOFF.md
```

Motivo:

```text
Roadmap ya absorbe el estado actual.
Ahora el handoff debe decirle a Claude exactamente qué leer, qué no tocar, qué está bloqueado y qué gates respetar.
```

No actualizar todavía:

```text
README.md
REPO_STRUCTURE_V0.1.md
```

README debe actualizarse después del AI Handoff.

---

## 15. Estado final de este roadmap

```text
Documento: ROADMAP_V0.1.md
Estado: CERTIFIED
Implementación: BLOCKED hasta AI Handoff + README + bootstrap técnico
Siguiente documento: AI_AGENT_RULES_AND_HANDOFF.md
```

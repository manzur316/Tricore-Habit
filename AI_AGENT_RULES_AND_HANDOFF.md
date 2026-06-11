# AI_AGENT_RULES_AND_HANDOFF.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado documental:** CERTIFIED como reglas base de trabajo con agentes IA  
**Estado de implementación:** BLOCKED hasta bootstrap técnico del repo  
**Estado de handoff a Claude Code CLI:** ver `DOCS_INDEX_AND_DEPENDENCY_MAP.md`
**Fecha:** 2026-06-11  
**Archivo autoritativo:** `AI_AGENT_RULES_AND_HANDOFF.md`  
**Modo de actualización:** actualización controlada del mismo archivo, sin crear duplicados  

---

## 0. Control de auditoría

### 0.1 Veredicto de la tarea

```text
Tarea: actualizar AI_AGENT_RULES_AND_HANDOFF.md
Veredicto: MATCH
Motivo:
- DOCS_INDEX_AND_DEPENDENCY_MAP.md ya existe en main.
- PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md está CERTIFIED.
- QA_HARNESS_SPEC_V0.1.md está CERTIFIED.
- ROADMAP_V0.1.md está CERTIFIED.
- El handoff anterior no incluía todos los documentos actuales ni los nuevos bloqueos de providers, pagos, laboratorio y QA.
```

### 0.2 Archivo actualizado

```text
Archivo objetivo: AI_AGENT_RULES_AND_HANDOFF.md
Archivos modificados en esta tarea: solo este archivo
```

### 0.3 Archivos no tocados

```text
README.md
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
ROADMAP_V0.1.md
REPO_STRUCTURE_V0.1.md
USER_MANUAL_PLAN.md
```

### 0.4 Documentos fuente usados

Este documento se alinea con:

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
ROADMAP_V0.1.md
REPO_STRUCTURE_V0.1.md
USER_MANUAL_PLAN.md
```

### 0.5 Estado del documento

```text
Documento: CERTIFIED
Uso permitido: reglas de trabajo, handoff, prompts para Claude Code CLI, control de alcance, QA gates
Uso no permitido: iniciar implementación productiva sin bootstrap técnico aprobado, QA Harness y labs aplicables
```

---

## 1. Propósito

Este documento define el protocolo oficial para trabajar Tricor Hábitat con agentes IA, especialmente **Claude Code CLI** como ejecutor principal del repositorio.

El objetivo es evitar que el proyecto vuelva al ciclo de deuda técnica:

```text
prompt ambiguo
→ cambio amplio
→ prueba manual incompleta
→ fallo nuevo
→ parche
→ ruptura de flujo vecino
→ pérdida de control
```

El ciclo correcto es:

```text
documento fuente de verdad
→ tarea acotada
→ plan técnico mínimo
→ cambio mínimo
→ QA Harness
→ reporte
→ revisión humana
```

---

## 2. Principio rector

Tricor Hábitat no se desarrolla por intuición del agente.

Tricor Hábitat se desarrolla por:

```text
documentos fuente de verdad
+ contratos canónicos
+ Policy Engine
+ Cloud + Edge
+ provider adapters
+ QA Harness
+ cambios pequeños y auditables
```

Ningún agente puede inventar:

```text
arquitectura
entidades
roles
permisos
proveedores
endpoints
flujos de pago
flujos de acceso
comportamiento de Edge
reglas de morosidad
mapeos de provider
```

Si falta una decisión, el agente debe marcarla como pendiente. No debe resolverla con suposiciones silenciosas.

---

## 3. Estado propio y handoff

### 3.1 Estado propio del documento

```text
AI_AGENT_RULES_AND_HANDOFF.md: CERTIFIED.
Repo técnico: no iniciado.
Implementación: no iniciada.
```

### 3.2 Estado de handoff a Claude Code CLI

```text
Estado global del handoff: ver DOCS_INDEX_AND_DEPENDENCY_MAP.md.
```

El handoff documental no debe pasar directo a implementación productiva.

---

## 4. Fuente única de verdad

La fuente operativa actual es el repositorio:

```text
https://github.com/manzur316/Tricore-Habit
branch: main
```

Los documentos del chat no superan al repositorio si existe contradicción.

Regla:

```text
Si chat y repo difieren, se reporta CONSISTENCY ISSUE.
No se implementa hasta resolverlo.
```

---

## 5. Orden oficial de lectura para Claude Code CLI

Claude debe leer en este orden:

```text
1. README.md
2. DOCS_INDEX_AND_DEPENDENCY_MAP.md
3. AI_AGENT_RULES_AND_HANDOFF.md
4. PROJECT_CHARTER_TRICOR_HABITAT.md
5. DOMAIN_MODEL_V0.1.md
6. ACCESS_POLICY_CONFIGURATION_V0.1.md
7. CLOUD_EDGE_ARCHITECTURE_V0.1.md
8. PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
9. QA_HARNESS_SPEC_V0.1.md
10. ROADMAP_V0.1.md
11. PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
12. PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
13. PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
14. PROVIDER_RESEARCH_HIKVISION_V0.1.md
15. REPO_STRUCTURE_V0.1.md
16. USER_MANUAL_PLAN.md
```

Para estado global vigente, Claude debe consultar:

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md
```

---

## 6. Estados documentales obligatorios

Claude debe respetar estos estados:

```text
CERTIFIED
CERTIFIED_FOR_RESEARCH
REVIEW_REQUIRED
BLOCKED
OBSOLETE
DRAFT
```

Reglas:

```text
CERTIFIED = usable como fuente base.
CERTIFIED_FOR_RESEARCH = válido para diseño/investigación; no autoriza producción.
REVIEW_REQUIRED = útil, pero no se debe tratar como final.
BLOCKED = no usar para implementación hasta desbloquear.
OBSOLETE = no usar.
DRAFT = guía provisional.
```

---

## 7. Estado documental global

```text
Este documento solo declara su estado propio.
Para el estado documental global, ver DOCS_INDEX_AND_DEPENDENCY_MAP.md.
AI_AGENT_RULES_AND_HANDOFF.md: CERTIFIED
```

---

## 8. Contextos prohibidos

El agente no debe mezclar contexto de:

```text
SATBot
TryHardNames
prototipos anteriores
repositorios legacy
conversaciones externas no documentadas en el repo
decisiones antiguas no documentadas
experimentos no aprobados
integraciones vetadas
```

Regla:

```text
El contexto válido vive en los documentos del repo y en el código actual del repo.
```

Si el agente recuerda algo fuera del repo, debe tratarlo como no confiable.

---

## 9. Roles del proyecto

### 9.1 Product Owner

Responsable de:

```text
prioridades comerciales
aprobación de alcance
validación de producto
decisiones de pricing
aprobación de integraciones externas
aceptación de UX/operación
```

### 9.2 Arquitecto / auditor técnico

Responsable de:

```text
coherencia documental
contratos canónicos
límites de dominio
auditoría de cambios
provider strategy
QA gates
control de dependencias
```

### 9.3 Claude Code CLI

Responsable de:

```text
implementar tareas acotadas
editar archivos del repo
ejecutar pruebas
respetar documentos fuente
reportar bloqueos
no inventar arquitectura
no hacer refactors no solicitados
```

### 9.4 QA Harness

Responsable de validar objetivamente:

```text
dominio
políticas
pagos
Edge
providers
roles
auditoría
flujos críticos
```

### 9.5 Revisor humano

Responsable de:

```text
aprobar merges
bloquear cambios riesgosos
validar reportes
confirmar alcance cumplido
```

---

## 10. Regla de un solo ejecutor

```text
Un agente edita.
Los demás revisan, diseñan o auditan.
```

No se permiten dos agentes modificando simultáneamente el mismo repo, rama, módulo o archivo.

---

## 11. Aislamiento de workspace

Estructura recomendada:

```text
projects/
├── satbot/
├── tricor-habitat/
└── legacy-reference/
```

Reglas:

```text
Claude Code CLI solo debe abrir tricor-habitat.
No debe abrir satbot.
No debe copiar código de otros proyectos.
No debe usar legacy-reference como fuente de verdad.
Si consulta legacy-reference, debe marcarlo como referencia histórica.
```

---

## 12. Nombres oficiales

```text
Tricor Hábitat = producto completo
Tricor Platform = panel interno SaaS
Tricor Condo = panel del condominio
Tricor Guard = interfaz de guardia
Tricor Resident = interfaz de residente
```

No usar:

```text
Tricorp
War
Ward
AVIT como nombre público principal
```

---

## 13. Stack técnico aprobado

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

Cambios prohibidos sin RFC:

```text
cambiar NestJS
cambiar PostgreSQL
cambiar Prisma
separar apps prematuramente
introducir app móvil nativa en v1
introducir workflows externos como core
cambiar proveedor de pagos prioritario
```

---

## 14. Estructura objetivo del repo

```text
tricor-habitat/
├── apps/
│   ├── api/
│   ├── web/
│   ├── edge/
│   └── qa/
├── packages/
│   ├── contracts/
│   ├── domain/
│   ├── db/
│   ├── provider-sdk/
│   ├── ui/
│   └── config/
├── docs/
│   ├── architecture/
│   ├── providers/
│   ├── payments/
│   ├── qa/
│   ├── roadmap/
│   ├── handoff/
│   ├── manuals/
│   └── research/
├── artifacts/
│   └── qa/
├── infra/
├── scripts/
├── .claude/
├── .github/
├── package.json
├── pnpm-workspace.yaml
├── turbo.json
├── docker-compose.yml
├── .env.example
├── README.md
└── CLAUDE.md
```

El repo actual puede estar en modo docs-only temporal. La migración a `/docs/...` debe decidirse antes del bootstrap técnico.

---

## 15. Alcance v1 obligatorio

El núcleo v1 es:

```text
pagos
→ morosidad
→ política de acceso
→ desired state
→ AccessCommand
→ Edge
→ provider adapter
→ observed state
→ auditoría
```

Superficies v1:

```text
Tricor Platform
Tricor Condo
Tricor Guard
Tricor Resident
```

V1 se implementa como app web/PWA modular, no como cuatro apps separadas.

---

## 16. Fuera de alcance v1

No implementar en v1:

```text
facturación fiscal propia
contabilidad fiscal completa
chat de residentes
red social
amenidades como módulo completo
WhatsApp
apps móviles nativas completas
automatizaciones libres por cliente
vehículos como entidad principal
múltiples proveedores reales simultáneos sin laboratorio
Tricor como intermediario financiero del mantenimiento
módulos premium no aprobados
```

Permitido como futuro/research:

```text
Telegram para soporte/alertas/reportes
integración de facturación externa
exportaciones contables
ticketing limitado
Flutter futuro
Hikvision como segundo access provider después de laboratorio
```

---

## 17. Reglas de dominio

El dominio no debe conocer nombres internos de proveedor.

Permitido en dominio:

```text
Property
ResponsiblePerson
Credential
AccessProfile
AccessZone
DesiredAccessState
ObservedAccessState
AccessCommand
AuditEvent
PaymentOrder
PaymentAttempt
Debt
MorosityPolicy
```

Prohibido en dominio:

```text
ZKTeco pin
ZKTeco accLevelIds
ZKTeco doorId
Hikvision employeeNo
Hikvision doorRight
Hikvision RightPlan
Mercado Pago payment_id
Mercado Pago order_id
access_token
endpoint URLs
payloads crudos de proveedor
```

Esos datos viven en:

```text
provider adapters
payment adapters
ProviderMapping
ProviderExternalRef
ProviderCapabilityMatrix
provider-specific QA tests
raw snapshot storage controlado
```

---

## 18. Reglas de proveedores de acceso

### 18.1 AccessProviderKey válido

```ts
type AccessProviderKey =
  | 'ZKTECO_CVSECURITY'
  | 'HIKVISION_ISAPI_PBAC'
  | 'GENERIC_MOCK';
```

### 18.2 Estado de providers

```text
ZKTeco/CVSecurity:
- research/spec certificado
- implementación bloqueada hasta laboratorio

Hikvision ISAPI PBAC:
- research certificado
- implementación bloqueada hasta laboratorio con dispositivo real

Generic Mock:
- requerido para QA
```

### 18.3 Reglas

```text
No implementar provider real antes de provider mock.
No implementar provider real antes de ProviderCapabilityMatrix.
No implementar provider real antes de provider contract tests.
No ejecutar remote open sin hardware-smoke gate.
No usar endpoints no documentados.
No copiar modelo de un proveedor a otro.
```

---

## 19. Reglas de ZKTeco / CVSecurity

Fuente base:

```text
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
```

Estado:

```text
Spec: CERTIFIED_FOR_RESEARCH
Lab: REVIEW_REQUIRED hasta ejecución
Implementación: BLOCKED
```

Reglas:

```text
Edge local obligatorio.
access_token nunca se loguea.
No usar PSG si lab dice NO APLICA / inconcluso.
No asumir peatonal vs vehicular si solo existe una puerta.
No ejecutar apertura remota sin confirmación humana.
No tratar éxito documental como éxito de hardware.
```

---

## 20. Reglas de Hikvision ISAPI PBAC

Fuente base:

```text
PROVIDER_RESEARCH_HIKVISION_V0.1.md
```

Estado:

```text
CERTIFIED_FOR_RESEARCH
Implementación: BLOCKED hasta laboratorio
```

Mapping base:

```text
Tricor AccessIdentity → Hikvision UserInfo.employeeNo
Tricor Credential → Hikvision CardInfo.cardNo + employeeNo
Tricor AccessProfile → Hikvision UserInfo.doorRight + RightPlan
Tricor ManualOpen → RemoteControl/door/<ID>
Tricor ObservedAccessEvent → AcsEvent
Tricor QR Event → QRCodeEvent
Tricor RemoteValidation → remoteCheck
```

Reglas:

```text
No usar Hikvision metadata/video como base de access control.
No asumir Hik-Connect o HikCentral como ruta principal.
No implementar remote open sin hardware.
No usar UserInfoDetail/Delete en producción sin lab.
No asumir que todos los dispositivos soportan todos los endpoints.
```

---

## 21. Reglas de Mercado Pago

Fuente base:

```text
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
```

Estado:

```text
CERTIFIED_FOR_RESEARCH
Implementación: BLOCKED hasta sandbox/OAuth/webhooks reales
```

Regla principal:

```text
Webhook no confirma pago por sí solo.
Webhook despierta validación.
Tricor consulta API.
Solo pago confirmado/acreditado restaura acceso.
```

Endpoints objetivo:

```text
POST /v1/orders
GET /v1/orders/{order_id}
GET /v1/payments/{payment_id}
```

Reglas:

```text
Mercado Pago no decide morosidad.
Mercado Pago no aplica permisos.
Tricor no custodia dinero de mantenimiento.
Cada condominio debe conectar su propia cuenta.
Tricor Platform cobra SaaS por separado.
```

---

## 22. Reglas de QA Harness

Fuente base:

```text
QA_HARNESS_SPEC_V0.1.md
```

Estado:

```text
CERTIFIED como especificación base
Implementación: BLOCKED hasta bootstrap técnico
```

Comandos objetivo:

```text
pnpm qa health
pnpm qa config defaults
pnpm qa config valid
pnpm qa config invalid
pnpm qa config dependencies
pnpm qa config provider-capabilities
pnpm qa flow property-debt-access-lifecycle
pnpm qa flow manual-payment-fallback-lifecycle
pnpm qa flow payment-confirmed-edge-offline
pnpm qa flow resident-move-out-credential-revocation
pnpm qa flow lost-credential
pnpm qa flow guest-qr-morosity-policy
pnpm qa provider-contract generic-mock
pnpm qa provider-contract zkteco --mock
pnpm qa provider-contract hikvision --mock
pnpm qa payments mercado-pago --mock
pnpm qa edge offline-reconnect
pnpm qa report
pnpm qa ci
```

Reglas:

```text
No declarar éxito sin QA.
No aceptar cambio crítico sin reporte.
No ejecutar hardware-smoke en CI normal.
No imprimir secretos.
No usar datos reales en fixtures.
```

---

## 23. Gates obligatorios

### 23.1 Documentation Gate

Requiere:

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
QA_HARNESS_SPEC_V0.1.md
ROADMAP_V0.1.md
AI_AGENT_RULES_AND_HANDOFF.md
README.md
```

Estado tras este documento:

```text
Estado global del Documentation Gate: ver DOCS_INDEX_AND_DEPENDENCY_MAP.md.
```

### 23.2 Repo Bootstrap Gate

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
```

### 23.3 Domain Gate

Requiere:

```text
entidades canónicas
eventos de dominio
Policy Engine base
DesiredAccessState
AccessCommand
AuditEvent
pruebas unitarias
```

### 23.4 QA Gate

Requiere:

```text
QA CLI
fixtures determinísticos
provider mock
payment mock
edge simulator
reportes md/json
suites de configuración
suites de dominio
provider contract mock
payment mock
```

### 23.5 Provider Lab Gate

Requiere:

```text
ZKTeco lab PASS antes de producción ZKTeco
Hikvision lab PASS antes de producción Hikvision
```

### 23.6 Payment Gate

Requiere:

```text
Mercado Pago sandbox
webhook signature validation
GET order/payment confirmation
idempotencia
externalReference
manual fallback auditado
```

---

## 24. Tipos de cambio permitidos

### 24.1 Docs-only

Requiere:

```text
un solo archivo objetivo salvo aprobación
reporte de cambios
estado final
no inventar decisiones
```

### 24.2 Domain-only

Requiere:

```text
pruebas unitarias
sin provider details
sin HTTP
sin DB directa
```

### 24.3 Backend feature

Requiere:

```text
pruebas unitarias
pruebas de integración
QA si toca flujo crítico
migración si cambia DB
```

### 24.4 UI feature

Requiere:

```text
roles correctos
mobile/PWA si Guard/Resident
no lógica crítica en UI
contratos compartidos
```

### 24.5 Edge feature

Requiere:

```text
cola
offline/reconnect
idempotencia
provider mock
logs técnicos sin secretos
```

### 24.6 Provider feature

Requiere:

```text
ProviderCapabilityMatrix
ProviderMapping
provider contract tests
mock primero
lab después
```

### 24.7 Payment feature

Requiere:

```text
payment mock
webhook validation
API confirmation
idempotencia
auditoría
no mezclar dinero SaaS/mantenimiento
```

### 24.8 QA feature

Requiere:

```text
CLI documentado
fixture determinístico
reportes md/json
prueba negativa
prueba positiva
```

---

## 25. Ciclo obligatorio de trabajo

Cada tarea debe seguir:

```text
1. Leer documentos relevantes.
2. Declarar alcance exacto.
3. Clasificar fuente: MATCH / PARTIAL / NO MATCH.
4. Presentar plan corto.
5. Ejecutar preflight si hay código.
6. Implementar cambio mínimo.
7. Ejecutar pruebas relevantes.
8. Ejecutar QA Harness si aplica.
9. Generar reporte.
10. No declarar éxito si el gate no pasó.
```

---

## 26. Preflight obligatorio

Antes de modificar código:

```text
pnpm install
pnpm lint
pnpm typecheck
pnpm test
pnpm qa health
```

Si los comandos no existen:

```text
reportar que no existen
no fingir que pasaron
crear scripts mínimos solo si la tarea lo permite
```

Para cambios docs-only:

```text
verificar archivo objetivo
verificar no tocar otros archivos
entregar reporte de cambios
```

---

## 27. Manejo de secretos

Prohibido:

```text
commitear .env
commitear access tokens
commitear client secrets
imprimir tokens en logs
pegar secretos en reportes
guardar credenciales en markdown
```

Permitido:

```text
.env.example
<SECRET_REF>
<LAB_ACCESS_TOKEN>
variables de entorno documentadas sin valor real
```

Tokens de laboratorio compartidos en conversación no deben copiarse a repo.

---

## 28. Reporte obligatorio de cambio

Cada tarea debe entregar:

```text
archivo objetivo
secciones modificadas
información nueva agregada
información eliminada/degradada
archivos NO tocados
pruebas ejecutadas
pruebas no ejecutadas y por qué
estado final
siguiente acción recomendada
```

Estados posibles:

```text
DRAFT
REVIEW_REQUIRED
CERTIFIED
BLOCKED
```

---

## 29. Reglas de Git y ramas

Regla recomendada:

```text
No trabajar directamente sobre main para implementación.
Usar branch por tarea.
```

Formato de rama:

```text
docs/update-ai-handoff
feat/domain-bootstrap
feat/qa-foundation
feat/provider-mock
```

Commits:

```text
docs: update AI handoff rules
feat: bootstrap monorepo
test: add provider contract mock suite
```

Pull Request o revisión equivalente debe incluir:

```text
resumen
archivos tocados
pruebas ejecutadas
riesgos
pendientes
```

---

## 30. Regla de cambio mínimo

Prohibido:

```text
refactorizar módulos no relacionados
renombrar entidades sin necesidad
mezclar backend/UI/Edge/QA en un cambio gigante
corregir problemas fuera de alcance sin reportar
implementar provider real dentro de otra tarea
```

Si detecta problema fuera de alcance:

```text
Out of scope issue detected:
- ubicación
- riesgo
- recomendación
- no modificado
```

---

## 31. Contradicciones

Si el agente detecta contradicción, debe reportar:

```text
CONSISTENCY ISSUE
Archivo A:
Archivo B:
Conflicto:
Riesgo:
Propuesta:
No implementado hasta resolución.
```

No debe elegir una versión silenciosamente.

---

## 32. Prompt inicial recomendado para Claude Code CLI

```text
Eres Claude Code CLI trabajando en el proyecto Tricor Hábitat.

Antes de modificar cualquier archivo:
1. Lee README.md.
2. Lee DOCS_INDEX_AND_DEPENDENCY_MAP.md.
3. Lee AI_AGENT_RULES_AND_HANDOFF.md.
4. Lee ROADMAP_V0.1.md.
5. Identifica el archivo o módulo exacto de la tarea.
6. Declara si la tarea es MATCH, PARTIAL o NO MATCH.
7. No implementes nada si la documentación base está en REVIEW_REQUIRED o BLOCKED.
8. No inventes endpoints, contratos, providers ni reglas.
9. No mezcles contexto de SATBot, TryHardNames ni proyectos legacy.
10. Ejecuta pruebas o reporta que aún no existen.
11. Entrega reporte de cambios.

Tarea:
[PEGAR TAREA ESPECÍFICA]
```

---

## 33. Plantilla de tarea para Claude Code CLI

```text
TAREA:
[descripción]

TIPO:
docs-only | domain-only | backend | web | edge | provider | payment | qa

ARCHIVOS PERMITIDOS:
[lista exacta]

ARCHIVOS PROHIBIDOS:
[lista exacta o "todos los demás"]

FUENTES OBLIGATORIAS:
[docs relevantes]

CRITERIO DE DONE:
[qué debe quedar]

QA REQUERIDO:
[comandos]

NO HACER:
[restricciones]

REPORTE ESPERADO:
- archivos modificados
- pruebas ejecutadas
- riesgos
- pendientes
```

---

## 34. Plantilla de Agent Work Report

```text
# Agent Work Report

## Tarea
...

## Alcance
...

## Fuentes leídas
...

## Archivos modificados
...

## Archivos no tocados
...

## Cambios realizados
...

## Pruebas ejecutadas
...

## Pruebas no ejecutadas
...

## QA Harness
...

## Riesgos
...

## Pendientes
...

## Estado final
PASS / REVIEW_REQUIRED / BLOCKED
```

---

## 35. Primeras tareas permitidas después del cierre documental

Después de revisión humana del handoff documental, las primeras tareas permitidas son:

```text
1. decidir estructura docs-only temporal vs mover a /docs
2. crear CLAUDE.md
3. bootstrap monorepo
4. configurar pnpm/turbo/typescript
5. crear packages/contracts
6. crear packages/domain
7. crear apps/qa skeleton
```

Prohibido como primera tarea:

```text
provider real
Mercado Pago real
Edge real
UI visual final
apps móviles
integraciones futuras
```

---

## 36. Estado final

```text
AI_AGENT_RULES_AND_HANDOFF.md: CERTIFIED
Handoff a Claude Code CLI: ver DOCS_INDEX_AND_DEPENDENCY_MAP.md
Implementación productiva: BLOCKED hasta bootstrap técnico + QA Harness + labs
Provider real: BLOCKED hasta QA + laboratorio
Pago real: BLOCKED hasta sandbox/webhooks/OAuth validado
```

Siguiente fase recomendada:

```text
revisión humana de bootstrap técnico
```

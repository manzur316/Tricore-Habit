# REPO_STRUCTURE_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión:** v0.1  
**Estado:** Documento base para handoff técnico  
**Audiencia:** Product Owner, arquitecto, Claude Code CLI, desarrolladores, QA y soporte técnico  
**Última actualización:** 2026-06-10

---

## 1. Propósito del documento

Este documento define la estructura oficial inicial del repositorio de **Tricor Hábitat**.

Su objetivo es evitar que el proyecto crezca de forma desordenada, mezcle responsabilidades o repita el patrón de parches acumulados. La estructura del repo debe reflejar la arquitectura aprobada en los documentos anteriores:

- `PROJECT_CHARTER_TRICOR_HABITAT.md`
- `DOMAIN_MODEL_V0.1.md`
- `ACCESS_POLICY_CONFIGURATION_V0.1.md`
- `CLOUD_EDGE_ARCHITECTURE_V0.1.md`
- `QA_HARNESS_SPEC_V0.1.md`
- `ROADMAP_V0.1.md`
- `AI_AGENT_RULES_AND_HANDOFF.md`
- `USER_MANUAL_PLAN.md`

Este documento debe usarse como guía directa para crear el monorepo inicial y para orientar a Claude Code CLI durante el bootstrap del proyecto.

---

## 2. Principio rector

El repositorio debe separar con claridad:

```text
Producto
Dominio
Infraestructura
Integraciones
Edge
QA Harness
Documentación
```

Ningún módulo debe depender de detalles de proveedor externo si puede depender de contratos canónicos.

Regla central:

```text
El dominio de Tricor Hábitat no debe hablar en idioma del proveedor.
```

El flujo correcto es:

```text
Dominio Tricor
→ Contratos canónicos
→ Policy Engine
→ Desired State
→ Command Queue
→ Edge
→ Provider Adapter
→ Observed State
→ Auditoría
```

---

## 3. Stack técnico aprobado

El repo debe prepararse para este stack:

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

El stack no debe cambiarse sin RFC técnico formal.

---

## 4. Estructura oficial del repositorio

Estructura inicial recomendada:

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
│   ├── manuals/
│   ├── research/
│   ├── runbooks/
│   └── handoff/
├── infra/
│   ├── docker/
│   ├── compose/
│   └── deploy/
├── scripts/
├── artifacts/
│   └── qa/
├── .claude/
├── .github/
├── package.json
├── pnpm-workspace.yaml
├── turbo.json
├── tsconfig.base.json
├── docker-compose.yml
├── .env.example
├── .gitignore
├── README.md
└── CLAUDE.md
```

Notas:

- `apps/qa` se mantiene como ubicación oficial del QA Harness para consistencia con el roadmap.
- `artifacts/qa` contiene reportes generados por ejecución local o CI.
- `docs/` contiene la fuente de verdad documental.
- `packages/domain` contiene reglas puras; no debe depender de NestJS, Next.js, Prisma, HTTP ni proveedor externo.

---

## 5. Responsabilidad por carpeta raíz

| Carpeta | Responsabilidad | Puede depender de | No debe depender de |
|---|---|---|---|
| `apps/api` | Backend Cloud, API, casos de uso, auth, jobs | `contracts`, `domain`, `db`, `provider-sdk`, `config` | `web`, `edge` |
| `apps/web` | Tricor Platform, Condo, Guard, Resident en web/PWA | `contracts`, `ui`, `config` | `db`, lógica directa de proveedor |
| `apps/edge` | Edge Agent local, ejecución de comandos, sync, UI técnica | `contracts`, `provider-sdk`, `config` | `web`, lógica financiera |
| `apps/qa` | QA Harness, fixtures, mocks, reportes | todos los contratos necesarios | hacks internos no documentados |
| `packages/contracts` | Tipos, DTOs, eventos, interfaces canónicas | librerías de validación | apps concretas |
| `packages/domain` | Entidades, value objects, Policy Engine, reglas puras | `contracts` | NestJS, Prisma, HTTP, provider real |
| `packages/db` | Prisma schema, migraciones, seeds, cliente DB | `contracts` cuando aplique | `web`, `edge` |
| `packages/provider-sdk` | Interfaces de providers, capability matrix, contratos de adapter | `contracts` | lógica UI |
| `packages/ui` | Componentes compartidos de interfaz | `contracts` mínimos, `config` | `db`, providers |
| `packages/config` | Config compartida: TypeScript, lint, env schema | nada o mínimo | dominio |
| `docs` | Fuente de verdad, manuales, research, runbooks | no aplica | no aplica |
| `infra` | Docker, despliegue, compose, scripts de infraestructura | no aplica | lógica de negocio |
| `scripts` | Automatización local, setup, mantenimiento | no aplica | lógica de negocio crítica |
| `artifacts` | Salidas generadas, reportes QA | no aplica | código fuente |

---

## 6. Reglas de dependencia

### 6.1 Dependencias permitidas

```text
apps/api      → packages/contracts, packages/domain, packages/db, packages/provider-sdk, packages/config
apps/web      → packages/contracts, packages/ui, packages/config
apps/edge     → packages/contracts, packages/provider-sdk, packages/config
apps/qa       → packages/contracts, packages/domain, packages/db, packages/provider-sdk, apps/api, apps/edge mocks
packages/db   → packages/contracts
packages/domain → packages/contracts
packages/provider-sdk → packages/contracts
packages/ui   → packages/contracts, packages/config
```

### 6.2 Dependencias prohibidas

```text
packages/domain → apps/*
packages/domain → packages/db
packages/domain → provider real
packages/domain → HTTP
packages/domain → filesystem
packages/domain → environment variables
apps/web → packages/db
apps/web → provider adapter directo
apps/edge → packages/db del Cloud
apps/edge → lógica de pagos/morosidad
apps/api → componentes UI
```

Regla práctica:

```text
Si una regla de negocio necesita ser probada sin base de datos, sin HTTP y sin proveedor, debe vivir en packages/domain.
```

---

## 7. `apps/api` — Backend Cloud

### 7.1 Propósito

`apps/api` contiene el backend principal de Tricor Hábitat.

Responsabilidades:

```text
- Auth y sesiones
- Multi-tenant
- Tricor Platform API
- Tricor Condo API
- Pagos y finanzas
- Morosidad
- Policy Engine orchestration
- Access commands
- Edge enrollment y sync
- Provider installations
- Auditoría
- Incidentes
- Files/storage metadata
- Webhooks de pagos
- Jobs programados
```

### 7.2 Estructura recomendada

```text
apps/api/
├── src/
│   ├── main.ts
│   ├── app.module.ts
│   ├── common/
│   │   ├── decorators/
│   │   ├── filters/
│   │   ├── guards/
│   │   ├── interceptors/
│   │   ├── pipes/
│   │   └── utils/
│   ├── config/
│   │   ├── env.schema.ts
│   │   └── configuration.ts
│   ├── modules/
│   │   ├── auth/
│   │   ├── platform/
│   │   ├── condo/
│   │   ├── identity/
│   │   ├── organization/
│   │   ├── properties/
│   │   ├── finance/
│   │   ├── payments/
│   │   ├── policy/
│   │   ├── access/
│   │   ├── credentials/
│   │   ├── qr/
│   │   ├── guard-operations/
│   │   ├── edge/
│   │   ├── providers/
│   │   ├── files/
│   │   ├── audit/
│   │   ├── incidents/
│   │   └── health/
│   ├── jobs/
│   │   ├── morosity-evaluation.job.ts
│   │   ├── credential-lifecycle-audit.job.ts
│   │   ├── payment-reconciliation.job.ts
│   │   └── provider-drift-detection.job.ts
│   └── bootstrap/
│       ├── swagger.ts
│       ├── logger.ts
│       └── shutdown.ts
├── test/
├── package.json
├── tsconfig.json
└── .env.example
```

### 7.3 Reglas internas de `apps/api`

```text
- Controllers no contienen lógica de negocio.
- Services orquestan casos de uso.
- Reglas puras viven en packages/domain.
- Prisma se usa desde packages/db.
- Toda acción crítica genera AuditEvent.
- Ningún endpoint debe permitir acceso cross-tenant.
- Finance no edita permisos directamente.
- Pagos confirmados disparan workflow; no manipulan proveedor directo.
```

### 7.4 Módulos clave

#### `auth`

Responsable de login, sesiones, tokens, recuperación de acceso y guards.

#### `platform`

Responsable de Tricor Platform:

```text
- clientes SaaS
- planes
- licencias
- estado de condos
- soporte Platform
```

#### `condo`

Responsable del contexto visible del cliente:

```text
- configuración general
- roles por condominio
- settings operativos
```

#### `organization`

Responsable de:

```text
Condominio → Privada → Propiedad
```

#### `finance`

Responsable de:

```text
- cargos
- multas
- cuotas
- deudas
- estados de cuenta
- aprobación manual fallback
```

#### `payments`

Responsable de:

```text
- PaymentProviderStrategy
- Mercado Pago research/adapter futuro
- PaymentAttempt
- webhooks
- validación de pago confirmado
```

#### `policy`

Responsable de:

```text
- ConfigRegistry
- PolicyConfigValidator
- MorosityPolicy
- AccessPolicy
- Vista de impacto
```

#### `access`

Responsable de:

```text
- DesiredAccessState
- ObservedAccessState
- AccessCommand
- restore/revoke workflows
```

#### `edge`

Responsable de:

```text
- Edge enrollment
- heartbeats
- command pulling
- snapshots
- sync status
```

#### `providers`

Responsable de:

```text
- ProviderInstallation
- ProviderMapping
- ProviderCapabilityMatrix
- provider contract validation
```

---

## 8. `apps/web` — Frontend Web/PWA

### 8.1 Propósito

`apps/web` contiene una sola aplicación web modular para las cuatro superficies:

```text
Tricor Platform
Tricor Condo
Tricor Guard
Tricor Resident
```

No se crearán cuatro aplicaciones separadas en v1.

### 8.2 Estructura recomendada

```text
apps/web/
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   ├── forgot-password/
│   │   └── layout.tsx
│   ├── platform/
│   │   ├── dashboard/
│   │   ├── customers/
│   │   ├── plans/
│   │   ├── licenses/
│   │   ├── support/
│   │   └── settings/
│   ├── condo/
│   │   ├── dashboard/
│   │   ├── privadas/
│   │   ├── propiedades/
│   │   ├── propietarios/
│   │   ├── finanzas/
│   │   ├── morosidad/
│   │   ├── accesos/
│   │   ├── credenciales/
│   │   ├── guardias/
│   │   ├── proveedor/
│   │   ├── edge/
│   │   ├── auditoria/
│   │   └── configuracion/
│   ├── guard/
│   │   ├── scan/
│   │   ├── access-check/
│   │   ├── manual-open/
│   │   ├── incidents/
│   │   ├── rounds/
│   │   └── supervisor/
│   ├── resident/
│   │   ├── home/
│   │   ├── estado-cuenta/
│   │   ├── pagos/
│   │   ├── qr/
│   │   ├── invitados/
│   │   ├── credenciales/
│   │   └── soporte/
│   ├── api/
│   ├── layout.tsx
│   └── page.tsx
├── components/
│   ├── platform/
│   ├── condo/
│   ├── guard/
│   ├── resident/
│   └── shared/
├── lib/
│   ├── api-client/
│   ├── auth/
│   ├── permissions/
│   ├── pwa/
│   └── utils/
├── public/
│   ├── icons/
│   └── manifest.webmanifest
├── test/
├── package.json
├── tsconfig.json
└── .env.example
```

### 8.3 Reglas de UI

```text
- Guard y Resident deben diseñarse mobile-first.
- Platform y Condo pueden ser desktop-first, pero responsivos.
- UI no debe calcular morosidad.
- UI no debe decidir permisos finales.
- UI consume proyecciones del backend.
- UI de configuración debe mostrar vista de impacto antes de guardar cambios críticos.
- UI debe ocultar opciones no soportadas por el proveedor conectado.
```

### 8.4 Subdominios futuros

La misma app puede servir distintas superficies por subdominio o ruta:

```text
platform.tricorhabitat.com → /platform
app.tricorhabitat.com      → /condo
guard.tricorhabitat.com    → /guard
resident.tricorhabitat.com → /resident
```

Esto no implica cuatro deploys obligatorios en v1.

---

## 9. `apps/edge` — Edge Agent local

### 9.1 Propósito

`apps/edge` contiene el servicio local instalado en sitio cuando el proveedor lo requiera.

Responsabilidades:

```text
- enrollment con Cloud
- heartbeat
- recepción/pull de comandos
- ejecución local contra Provider Adapter
- cola local
- modo offline
- snapshots operativos
- observed state
- drift reporting
- UI local técnica
- logs técnicos
```

### 9.2 Estructura recomendada

```text
apps/edge/
├── src/
│   ├── main.ts
│   ├── config/
│   │   ├── env.schema.ts
│   │   └── edge-config.ts
│   ├── agent/
│   │   ├── edge-agent.ts
│   │   ├── enrollment.service.ts
│   │   ├── heartbeat.service.ts
│   │   └── sync.service.ts
│   ├── commands/
│   │   ├── command-poller.ts
│   │   ├── command-runner.ts
│   │   ├── command-store.ts
│   │   └── idempotency.ts
│   ├── provider/
│   │   ├── provider-adapter.factory.ts
│   │   ├── zkteco-cvsecurity/
│   │   │   ├── adapter.ts
│   │   │   ├── mapper.ts
│   │   │   ├── client.ts
│   │   │   └── capability-matrix.ts
│   │   └── mock/
│   │       ├── adapter.ts
│   │       └── capability-matrix.ts
│   ├── local-store/
│   │   ├── snapshot-store.ts
│   │   ├── command-log-store.ts
│   │   └── observed-state-store.ts
│   ├── local-ui/
│   │   ├── server.ts
│   │   ├── routes.ts
│   │   └── views/
│   ├── diagnostics/
│   │   ├── health-check.ts
│   │   ├── provider-check.ts
│   │   └── network-check.ts
│   └── logging/
├── test/
├── package.json
├── tsconfig.json
└── .env.example
```

### 9.3 Reglas de Edge

```text
- Edge no decide morosidad.
- Edge no calcula pagos.
- Edge no administra residentes.
- Edge no es fuente de verdad.
- Edge ejecuta comandos autorizados por Cloud.
- Edge debe ser idempotente.
- Edge debe poder reintentar comandos pendientes.
- Edge debe reportar observed state.
- Edge debe tener UI local técnica mínima.
```

### 9.4 UI local técnica

Debe permitir únicamente:

```text
- ver estado de conexión
- ver último heartbeat
- ver proveedor conectado
- probar conexión con proveedor
- ver cola pendiente
- ver logs técnicos recientes
- reiniciar servicio si se habilita operación técnica
```

No debe permitir:

```text
- editar residentes
- editar pagos
- editar permisos permanentes
- crear propiedades
- cambiar reglas de morosidad
```

---

## 10. `apps/qa` — QA Harness CLI

### 10.1 Propósito

`apps/qa` contiene el QA Harness de Tricor Hábitat.

No es una herramienta opcional. Es el gate obligatorio para evitar desarrollo a ciegas.

Responsabilidades:

```text
- ejecutar suites de configuración
- ejecutar flujos end-to-end con mocks
- validar contratos canónicos
- validar ProviderCapabilityMatrix
- validar PolicyConfigValidator
- probar pagos mock
- probar Edge simulator
- probar provider mock
- generar reportes .md y .json
```

### 10.2 Estructura recomendada

```text
apps/qa/
├── src/
│   ├── cli.ts
│   ├── commands/
│   │   ├── health.command.ts
│   │   ├── domain.command.ts
│   │   ├── config.command.ts
│   │   ├── flow.command.ts
│   │   ├── provider-contract.command.ts
│   │   ├── edge.command.ts
│   │   ├── payments.command.ts
│   │   └── report.command.ts
│   ├── suites/
│   │   ├── domain/
│   │   ├── config/
│   │   ├── payments/
│   │   ├── morosity/
│   │   ├── access/
│   │   ├── qr/
│   │   ├── credentials/
│   │   ├── guard/
│   │   ├── resident/
│   │   ├── edge/
│   │   ├── provider-contracts/
│   │   ├── security/
│   │   └── audit/
│   ├── flows/
│   │   ├── property-debt-access-lifecycle.flow.ts
│   │   ├── manual-payment-fallback-lifecycle.flow.ts
│   │   ├── payment-confirmed-edge-offline.flow.ts
│   │   ├── resident-move-out-credential-revocation.flow.ts
│   │   └── lost-credential.flow.ts
│   ├── fixtures/
│   │   ├── condominiums.fixture.ts
│   │   ├── privadas.fixture.ts
│   │   ├── properties.fixture.ts
│   │   ├── owners.fixture.ts
│   │   ├── credentials.fixture.ts
│   │   ├── policies.fixture.ts
│   │   └── provider-mappings.fixture.ts
│   ├── mocks/
│   │   ├── provider.mock.ts
│   │   ├── edge.mock.ts
│   │   ├── payments.mock.ts
│   │   └── storage.mock.ts
│   ├── reporters/
│   │   ├── markdown.reporter.ts
│   │   ├── json.reporter.ts
│   │   └── console.reporter.ts
│   └── utils/
├── package.json
├── tsconfig.json
└── .env.example
```

### 10.3 Comandos mínimos esperados

```text
pnpm qa:health
pnpm qa:domain
pnpm qa:contracts
pnpm qa:config
pnpm qa:api
pnpm qa:edge
pnpm qa:provider-contract zkteco-cvsecurity
pnpm qa:payments mercado-pago --mock
pnpm qa:flow property-debt-access-lifecycle
pnpm qa:flow manual-payment-fallback-lifecycle
pnpm qa:flow payment-confirmed-edge-offline
pnpm qa:report
```

### 10.4 Artefactos QA

```text
artifacts/qa/latest-report.md
artifacts/qa/latest-report.json
artifacts/qa/history/<timestamp>-report.md
artifacts/qa/history/<timestamp>-report.json
```

Los reportes deben ser legibles para humanos y útiles para Claude Code CLI.

---

## 11. `packages/contracts` — Contratos canónicos

### 11.1 Propósito

`packages/contracts` contiene los tipos y contratos que comparten API, web, Edge, QA y provider adapters.

Responsabilidades:

```text
- DTOs canónicos
- tipos de dominio compartidos
- eventos de dominio
- comandos de acceso
- estados
- enums
- schemas de validación
- contratos Edge ↔ Cloud
- contratos provider adapter
```

### 11.2 Estructura recomendada

```text
packages/contracts/
├── src/
│   ├── index.ts
│   ├── organization/
│   ├── identity/
│   ├── properties/
│   ├── finance/
│   ├── payments/
│   ├── policy/
│   ├── access/
│   ├── credentials/
│   ├── qr/
│   ├── guard/
│   ├── edge/
│   ├── providers/
│   ├── audit/
│   ├── incidents/
│   ├── files/
│   └── common/
├── test/
├── package.json
└── tsconfig.json
```

### 11.3 Reglas

```text
- No importar desde apps.
- No contener lógica de negocio pesada.
- No contener clientes HTTP reales.
- No contener Prisma.
- No contener componentes UI.
- Debe ser estable y versionable.
```

---

## 12. `packages/domain` — Reglas puras

### 12.1 Propósito

`packages/domain` contiene el corazón lógico de Tricor Hábitat.

Responsabilidades:

```text
- entidades puras
- value objects
- Policy Engine
- ConfigRegistry
- PolicyConfigValidator
- cálculo de morosidad
- cálculo de permisos
- desired state builder
- reglas de credenciales
- reglas de guardia
- reglas de mudanza/exresidente
- validaciones de negocio
```

### 12.2 Estructura recomendada

```text
packages/domain/
├── src/
│   ├── index.ts
│   ├── organization/
│   ├── properties/
│   ├── finance/
│   ├── payments/
│   ├── policy/
│   │   ├── policy-engine.ts
│   │   ├── config-registry.ts
│   │   ├── policy-config-validator.ts
│   │   ├── presets/
│   │   └── impact-analysis/
│   ├── access/
│   │   ├── desired-state-builder.ts
│   │   ├── access-profile-resolver.ts
│   │   └── access-capability-resolver.ts
│   ├── credentials/
│   │   ├── credential-lifecycle.ts
│   │   └── stale-credential-detector.ts
│   ├── qr/
│   ├── guard/
│   ├── edge/
│   ├── providers/
│   ├── audit/
│   └── common/
├── test/
├── package.json
└── tsconfig.json
```

### 12.3 Reglas

```text
- Debe poder probarse sin base de datos.
- Debe poder probarse sin API.
- Debe poder probarse sin Edge real.
- Debe poder probarse sin proveedor real.
- No usa process.env.
- No usa filesystem.
- No hace llamadas HTTP.
- No importa Prisma.
```

---

## 13. `packages/db` — Base de datos y Prisma

### 13.1 Propósito

`packages/db` contiene schema, migraciones, seeds y cliente Prisma.

Responsabilidades:

```text
- schema Prisma
- migraciones
- cliente DB exportado
- seeds determinísticos
- helpers de test DB
- naming de tablas
```

### 13.2 Estructura recomendada

```text
packages/db/
├── prisma/
│   ├── schema.prisma
│   ├── migrations/
│   └── seed.ts
├── src/
│   ├── index.ts
│   ├── prisma.service.ts
│   ├── tenant-context.ts
│   ├── repositories/
│   └── test-utils/
├── package.json
└── tsconfig.json
```

### 13.3 Reglas

```text
- Solo este paquete debe conocer Prisma directamente.
- apps/api puede usar repositorios o servicios exportados.
- apps/web no debe importar packages/db.
- apps/edge no debe usar la base Cloud directamente.
- Migraciones deben acompañarse de QA.
```

---

## 14. `packages/provider-sdk` — Provider SDK canónico

### 14.1 Propósito

`packages/provider-sdk` define cómo se conectan los adapters de proveedor sin contaminar el dominio.

Responsabilidades:

```text
- AccessProviderAdapter interface
- ProviderCapabilityMatrix
- ProviderMapping contracts
- ProviderCommandResult
- ProviderObservedState
- ProviderHealthCheck
- errores normalizados de proveedor
```

### 14.2 Estructura recomendada

```text
packages/provider-sdk/
├── src/
│   ├── index.ts
│   ├── adapter.interface.ts
│   ├── capability-matrix.ts
│   ├── provider-mapping.ts
│   ├── provider-errors.ts
│   ├── command-result.ts
│   ├── observed-state.ts
│   └── testing/
│       ├── provider-adapter-test-suite.ts
│       └── provider-contract-fixtures.ts
├── test/
├── package.json
└── tsconfig.json
```

### 14.3 Reglas

```text
- Define contratos, no impone proveedor específico.
- No debe contener lógica de UI.
- No debe conocer pagos.
- No debe decidir morosidad.
- Debe permitir provider mocks y adapters reales.
```

---

## 15. `packages/ui` — Componentes compartidos

### 15.1 Propósito

`packages/ui` contiene componentes visuales compartidos para `apps/web`.

Responsabilidades:

```text
- botones
- tablas
- formularios
- layouts
- badges de estado
- cards
- modales
- componentes de configuración
- componentes de auditoría
```

### 15.2 Estructura recomendada

```text
packages/ui/
├── src/
│   ├── index.ts
│   ├── components/
│   │   ├── buttons/
│   │   ├── forms/
│   │   ├── tables/
│   │   ├── badges/
│   │   ├── dialogs/
│   │   ├── layouts/
│   │   └── status/
│   ├── hooks/
│   ├── tokens/
│   └── utils/
├── test/
├── package.json
└── tsconfig.json
```

### 15.3 Reglas

```text
- No debe importar packages/db.
- No debe llamar API directamente.
- No debe contener lógica de permisos compleja.
- No debe saber qué proveedor está conectado.
- Puede recibir props derivadas del backend.
```

---

## 16. `packages/config` — Configuración técnica compartida

### 16.1 Propósito

`packages/config` centraliza configuración técnica repetible.

Responsabilidades:

```text
- tsconfig compartido
- eslint config
- prettier config
- env schema helpers
- constants técnicas
- test config base
```

### 16.2 Estructura recomendada

```text
packages/config/
├── src/
│   ├── env/
│   ├── logger/
│   ├── testing/
│   └── index.ts
├── eslint/
├── typescript/
├── prettier/
├── package.json
└── tsconfig.json
```

---

## 17. `docs` — Fuente de verdad documental

### 17.1 Estructura recomendada

```text
docs/
├── PROJECT_CHARTER_TRICOR_HABITAT.md
├── DOMAIN_MODEL_V0.1.md
├── ACCESS_POLICY_CONFIGURATION_V0.1.md
├── CLOUD_EDGE_ARCHITECTURE_V0.1.md
├── QA_HARNESS_SPEC_V0.1.md
├── ROADMAP_V0.1.md
├── AI_AGENT_RULES_AND_HANDOFF.md
├── USER_MANUAL_PLAN.md
├── REPO_STRUCTURE_V0.1.md
├── architecture/
│   ├── api/
│   ├── edge/
│   ├── payments/
│   ├── providers/
│   └── security/
├── manuals/
│   ├── platform/
│   ├── condo/
│   ├── guard/
│   ├── resident/
│   └── edge/
├── research/
│   ├── payments/
│   ├── providers/
│   └── integrations/
├── runbooks/
│   ├── incidents/
│   ├── edge/
│   ├── payments/
│   └── provider/
└── handoff/
    ├── CLAUDE_BOOTSTRAP_PROMPT.md
    ├── TASK_TEMPLATE.md
    └── AGENT_WORK_REPORT_TEMPLATE.md
```

### 17.2 Reglas

```text
- Docs fuente de verdad viven en raíz de docs.
- Research vive separado de decisiones cerradas.
- Manuales viven separados de arquitectura.
- Runbooks son operativos, no roadmap.
- Handoff contiene prompts y plantillas para agentes.
```

---

## 18. `infra` — Infraestructura

### 18.1 Propósito

`infra` contiene archivos de despliegue y soporte de infraestructura.

Estructura recomendada:

```text
infra/
├── docker/
│   ├── api.Dockerfile
│   ├── web.Dockerfile
│   ├── edge.Dockerfile
│   └── qa.Dockerfile
├── compose/
│   ├── docker-compose.local.yml
│   ├── docker-compose.qa.yml
│   └── docker-compose.edge.yml
└── deploy/
    ├── staging/
    ├── production/
    └── edge/
```

### 18.2 Reglas

```text
- Infra no contiene lógica de negocio.
- Infra local debe permitir levantar Postgres, API, Web, Edge mock y QA.
- Infra de producción se define después de MVP técnico.
```

---

## 19. `scripts` — Automatización local

Estructura recomendada:

```text
scripts/
├── setup.sh
├── setup.ps1
├── db-reset.sh
├── db-reset.ps1
├── generate-env.sh
├── qa-all.sh
├── qa-all.ps1
├── check-vetted-terms.sh
└── doctor.sh
```

Reglas:

```text
- Scripts deben ser simples y auditables.
- Scripts no deben parchear datos productivos.
- Scripts peligrosos deben requerir confirmación explícita.
```

---

## 20. Archivos raíz obligatorios

### `package.json`

Debe definir scripts globales:

```json
{
  "scripts": {
    "dev": "turbo dev",
    "build": "turbo build",
    "lint": "turbo lint",
    "typecheck": "turbo typecheck",
    "test": "turbo test",
    "format": "prettier --write .",
    "qa:health": "pnpm --filter @tricor/qa qa:health",
    "qa:domain": "pnpm --filter @tricor/qa qa:domain",
    "qa:contracts": "pnpm --filter @tricor/qa qa:contracts",
    "qa:config": "pnpm --filter @tricor/qa qa:config",
    "qa:flow": "pnpm --filter @tricor/qa qa:flow",
    "qa:report": "pnpm --filter @tricor/qa qa:report"
  }
}
```

### `pnpm-workspace.yaml`

Debe incluir:

```yaml
packages:
  - "apps/*"
  - "packages/*"
```

### `turbo.json`

Debe definir pipelines mínimos:

```json
{
  "tasks": {
    "build": { "dependsOn": ["^build"], "outputs": ["dist/**", ".next/**"] },
    "lint": {},
    "typecheck": {},
    "test": {},
    "dev": { "cache": false, "persistent": true }
  }
}
```

### `.env.example`

Debe tener variables raíz no sensibles y remitir a `.env.example` por app.

### `CLAUDE.md`

Debe contener reglas de agente, fuentes de verdad y prohibiciones de contexto.

### `.claude/settings.json`

Debe limitar permisos si se usa Claude Code CLI con configuración por proyecto.

---

## 21. Convenciones de nombres

### 21.1 Paquetes

```text
@tricor/api
@tricor/web
@tricor/edge
@tricor/qa
@tricor/contracts
@tricor/domain
@tricor/db
@tricor/provider-sdk
@tricor/ui
@tricor/config
```

### 21.2 Entidades de dominio

Usar inglés técnico en código:

```text
Condominium
PrivateCommunity
Property
Owner
Credential
AccessProfile
AccessZone
AccessCommand
DesiredAccessState
ObservedAccessState
MorosityPolicy
PaymentAttempt
ProofOfPayment
AuditEvent
```

Usar español claro en UI:

```text
Condominio
Privada
Propiedad
Propietario
Credencial
Perfil de acceso
Zona de acceso
Pago
Morosidad
Auditoría
```

### 21.3 Nombres de carpetas

```text
- kebab-case para carpetas
- PascalCase para componentes React
- camelCase para variables y funciones
- PascalCase para clases y tipos
- UPPER_SNAKE_CASE para constantes globales
```

---

## 22. Modelo de superficies en `apps/web`

Las superficies deben permanecer separadas por rutas y permisos:

```text
/platform → Tricor Platform
/condo    → Tricor Condo
/guard    → Tricor Guard
/resident → Tricor Resident
```

Reglas:

```text
- Platform nunca usa rutas de Condo como atajo administrativo sin auditoría.
- Condo no puede ver datos de otro condominio.
- PrivateAdmin no puede ver otras privadas.
- Guard no puede ver finanzas.
- Resident solo ve sus propiedades vinculadas.
```

---

## 23. Modelo de módulos en `apps/api`

Cada módulo de API debe seguir una estructura consistente:

```text
module-name/
├── module-name.module.ts
├── module-name.controller.ts
├── module-name.service.ts
├── dto/
├── use-cases/
├── policies/
├── repositories/
├── events/
└── tests/
```

Para módulos complejos:

```text
finance/
├── charges/
├── debts/
├── payment-attempts/
├── manual-payments/
├── proofs/
└── reports/
```

Regla:

```text
Controller → UseCase/Application Service → Domain Service → Repository/Integration
```

No:

```text
Controller → Prisma directo
Controller → Provider directo
Controller → lógica de morosidad inline
```

---

## 24. Provider adapters

### 24.1 Regla general

El código específico de proveedor debe estar aislado.

Ubicación recomendada para provider real ejecutado por Edge:

```text
apps/edge/src/provider/<provider-name>/
```

Contratos compartidos del provider:

```text
packages/provider-sdk/
```

Configuración y mapping por condominio:

```text
apps/api/src/modules/providers/
```

### 24.2 Estructura adapter

```text
provider-name/
├── adapter.ts
├── client.ts
├── mapper.ts
├── capability-matrix.ts
├── errors.ts
├── health-check.ts
└── tests/
```

### 24.3 Reglas

```text
- Adapter no decide política de acceso.
- Adapter no decide morosidad.
- Adapter no calcula pagos.
- Adapter recibe comandos canónicos.
- Adapter devuelve resultados normalizados.
- Adapter declara capabilities reales.
```

---

## 25. Payment provider adapters

### 25.1 Ubicación

Pagos viven en Cloud, no en Edge.

```text
apps/api/src/modules/payments/
├── providers/
│   ├── mercado-pago/
│   │   ├── adapter.ts
│   │   ├── webhook.controller.ts
│   │   ├── signature-verifier.ts
│   │   ├── mapper.ts
│   │   └── tests/
│   └── mock/
```

### 25.2 Reglas

```text
- Pago confirmado por provider puede restaurar acceso automáticamente.
- Transferencia manual es fallback auditado.
- Tricor no es intermediario financiero del mantenimiento.
- La cuenta receptora pertenece al condominio.
- Tricor Platform cobra SaaS por separado.
```

---

## 26. Storage

### 26.1 Ubicación de lógica

```text
apps/api/src/modules/files/
├── storage.service.ts
├── storage-provider.interface.ts
├── s3-compatible.provider.ts
├── metadata.service.ts
└── tests/
```

### 26.2 Reglas

```text
- Archivos reales no se guardan en PostgreSQL.
- PostgreSQL guarda metadata.
- Storage debe ser S3-compatible.
- Comprobantes no restauran acceso por sí solos.
- Comprobantes respaldan pagos manuales o evidencia.
```

---

## 27. Auditoría

Auditoría debe ser transversal.

Ubicación:

```text
apps/api/src/modules/audit/
packages/contracts/src/audit/
packages/domain/src/audit/
```

Reglas:

```text
Toda acción crítica debe generar AuditEvent:
- aprobación manual de pago
- restauración de acceso
- restricción por morosidad
- apertura manual
- excepción temporal
- credencial perdida
- cambio de propietario/responsable
- cambio de configuración
- cambio de provider mapping
- Edge offline/online
```

---

## 28. Incidentes operativos

Ubicación:

```text
apps/api/src/modules/incidents/
```

Debe cubrir:

```text
- Edge offline
- provider connection failed
- payment confirmed but access pending
- command failed
- drift detected
- stale credential detected
- manual override used
```

No debe convertirse en sistema completo de tickets v1.

---

## 29. Seguridad y multi-tenant

### 29.1 Reglas obligatorias

```text
- Toda consulta sensible debe incluir tenant scope.
- PlatformSupportAgent puede entrar a cliente solo con auditoría.
- PrivateAdmin solo ve su privada.
- Guard no ve finanzas.
- Resident solo ve propiedades asociadas.
- Edge solo ve datos del condominio/caseta asignada.
```

### 29.2 Ubicación

```text
apps/api/src/common/guards/
apps/api/src/modules/auth/
apps/api/src/modules/identity/
packages/contracts/src/identity/
```

---

## 30. Configuración y Policy Engine

Ubicación principal:

```text
packages/domain/src/policy/
apps/api/src/modules/policy/
apps/web/app/condo/configuracion/
```

Reglas:

```text
- packages/domain calcula y valida.
- apps/api persiste y orquesta.
- apps/web muestra UI y vista de impacto.
- QA Harness prueba combinaciones válidas e inválidas.
```

No se debe duplicar lógica de policy en frontend.

---

## 31. Base de datos y migraciones

### 31.1 Reglas

```text
- Cada migración debe tener nombre claro.
- Seeds deben ser determinísticos.
- Datos demo no deben confundirse con datos QA.
- Migraciones destructivas requieren RFC.
- Soft delete/archive se prefiere sobre eliminación física para entidades críticas.
```

### 31.2 Entidades críticas que no se eliminan físicamente por default

```text
- propiedades
- propietarios/responsables
- credenciales
- pagos
- comprobantes metadata
- access commands
- audit events
- provider mappings
- edge installations
```

---

## 32. Artifacts y reportes generados

```text
artifacts/
├── qa/
│   ├── latest-report.md
│   ├── latest-report.json
│   └── history/
├── coverage/
└── logs/
```

Reglas:

```text
- latest-report.* puede sobrescribirse.
- history puede ignorarse en git si crece demasiado.
- Reportes críticos de CI pueden adjuntarse como artifacts del pipeline.
```

---

## 33. CI/CD inicial

Estructura recomendada:

```text
.github/
└── workflows/
    ├── ci.yml
    ├── qa.yml
    └── docs-check.yml
```

### 33.1 `ci.yml`

Debe ejecutar:

```text
pnpm install
pnpm lint
pnpm typecheck
pnpm test
pnpm build
```

### 33.2 `qa.yml`

Debe ejecutar:

```text
pnpm qa:health
pnpm qa:domain
pnpm qa:contracts
pnpm qa:config
pnpm qa:flow property-debt-access-lifecycle
pnpm qa:report
```

### 33.3 `docs-check.yml`

Debe validar:

```text
- documentos requeridos existen
- no hay términos vetados
- estructura mínima de docs
- links internos básicos
```

---

## 34. Entornos

### 34.1 Local

```text
- Postgres local en Docker
- API local
- Web local
- Edge local mock
- Provider mock
- Payment mock
- Storage mock
```

### 34.2 CI

```text
- Base efímera
- Fixtures determinísticos
- No provider real
- No pagos reales
```

### 34.3 Staging

```text
- Datos controlados
- Provider mock por default
- Smoke tests controlados
- No afectar condominios reales sin autorización
```

### 34.4 Hardware smoke

```text
- Ejecución manual controlada
- Provider real solo en entorno designado
- Reporte obligatorio
```

---

## 35. Variables de entorno

Cada app debe tener su propio `.env.example`.

```text
apps/api/.env.example
apps/web/.env.example
apps/edge/.env.example
apps/qa/.env.example
```

Reglas:

```text
- No subir secretos reales.
- No compartir claves entre ambientes.
- Edge debe tener credenciales propias.
- QA debe usar mocks por default.
```

---

## 36. Flujo de bootstrap recomendado

Comandos esperados:

```text
pnpm install
pnpm lint
pnpm typecheck
pnpm test
pnpm build
pnpm qa:health
```

Bootstrap inicial debe crear:

```text
- monorepo pnpm
- turbo.json
- tsconfig base
- packages vacíos con exports mínimos
- apps vacías que compilen
- docker-compose local
- QA Harness mínimo
- CLAUDE.md
- docs importados
```

No debe crear todavía:

```text
- proveedor real completo
- pagos reales completos
- UI final
- reglas complejas sin QA
```

---

## 37. Reglas para Claude Code CLI

Claude Code CLI debe respetar:

```text
1. Leer docs antes de tocar código.
2. No mezclar contexto de otros proyectos.
3. No usar repo legacy como fuente de verdad.
4. Un solo ejecutor modifica el repo a la vez.
5. Cambios pequeños y probables.
6. Todo cambio debe correr QA mínimo.
7. No crear módulos fuera de estructura aprobada.
8. No mover lógica de dominio a UI.
9. No conectar provider real sin provider contract tests.
10. No implementar pagos reales sin payment research aprobado.
```

---

## 38. Prompt de bootstrap para Claude Code CLI

```text
You are working inside the Tricor Hábitat repository.

Before editing files, read:
- docs/PROJECT_CHARTER_TRICOR_HABITAT.md
- docs/DOMAIN_MODEL_V0.1.md
- docs/ACCESS_POLICY_CONFIGURATION_V0.1.md
- docs/CLOUD_EDGE_ARCHITECTURE_V0.1.md
- docs/QA_HARNESS_SPEC_V0.1.md
- docs/ROADMAP_V0.1.md
- docs/AI_AGENT_RULES_AND_HANDOFF.md
- docs/REPO_STRUCTURE_V0.1.md

Task:
Bootstrap the monorepo structure exactly as defined in REPO_STRUCTURE_V0.1.md.

Constraints:
- Do not implement product features yet.
- Do not add provider real integration yet.
- Do not add real payment integration yet.
- Create package skeletons that compile.
- Add root scripts for lint, typecheck, test, build and QA health.
- Add .env.example files.
- Add CLAUDE.md with project rules.
- Add a minimal QA Harness command: qa:health.

Before finishing, run:
- pnpm install
- pnpm lint
- pnpm typecheck
- pnpm test
- pnpm build
- pnpm qa:health

Return an Agent Work Report.
```

---

## 39. Archivos mínimos para el primer commit

Primer commit recomendado:

```text
chore(repo): bootstrap tricor habitat monorepo
```

Debe incluir:

```text
- package.json
- pnpm-workspace.yaml
- turbo.json
- tsconfig.base.json
- apps/api/package.json
- apps/web/package.json
- apps/edge/package.json
- apps/qa/package.json
- packages/contracts/package.json
- packages/domain/package.json
- packages/db/package.json
- packages/provider-sdk/package.json
- packages/ui/package.json
- packages/config/package.json
- docs/*.md
- CLAUDE.md
- .env.example
- docker-compose.yml
```

No debe incluir:

```text
- secrets
- código legacy copiado
- dumps de base de datos
- provider credentials
- pagos reales
```

---

## 40. Primeros scripts mínimos

```text
pnpm dev
pnpm build
pnpm lint
pnpm typecheck
pnpm test
pnpm format
pnpm qa:health
pnpm qa:domain
pnpm qa:contracts
pnpm qa:config
pnpm qa:flow
pnpm qa:report
```

Al inicio, algunos QA pueden existir como stubs que fallan con mensaje claro:

```text
Not implemented yet. See QA_HARNESS_SPEC_V0.1.md.
```

Pero `qa:health` debe funcionar desde el bootstrap.

---

## 41. Estrategia de imports

Usar aliases por paquete:

```ts
import { Something } from '@tricor/contracts';
import { PolicyEngine } from '@tricor/domain';
import { db } from '@tricor/db';
import { AccessProviderAdapter } from '@tricor/provider-sdk';
import { Button } from '@tricor/ui';
```

Evitar imports relativos profundos entre paquetes:

```ts
// Evitar
import { X } from '../../../packages/domain/src/...';
```

---

## 42. Estrategia de testing

### 42.1 Unit tests

Ubicación:

```text
packages/*/test
apps/*/test
```

### 42.2 QA Harness

Ubicación:

```text
apps/qa/src/suites
apps/qa/src/flows
```

### 42.3 Prioridad de pruebas

```text
1. packages/domain
2. packages/contracts
3. packages/provider-sdk
4. apps/api
5. apps/edge
6. apps/web projections
7. apps/qa flows
```

---

## 43. Regla de documentación cerca del código

Además de docs globales, cada módulo crítico debe tener README local cuando crezca.

Ejemplo:

```text
apps/api/src/modules/policy/README.md
apps/edge/src/provider/zkteco-cvsecurity/README.md
apps/qa/src/suites/provider-contracts/README.md
```

El README local debe explicar:

```text
- responsabilidad del módulo
- qué no debe hacer
- dependencias permitidas
- comandos QA relacionados
```

---

## 44. Glosario de estructura

```text
apps/api
Backend Cloud.

apps/web
Aplicación web modular para Platform, Condo, Guard y Resident.

apps/edge
Agente local para ejecutar comandos contra proveedores.

apps/qa
CLI de pruebas y validación del sistema.

packages/contracts
Tipos y contratos compartidos.

packages/domain
Reglas puras del negocio.

packages/db
Prisma, migraciones y acceso a base.

packages/provider-sdk
Interfaces canónicas para proveedores.

packages/ui
Componentes UI compartidos.

packages/config
Configuración técnica compartida.
```

---

## 45. Criterios de aceptación del repo bootstrap

El bootstrap del repo se considera aceptado cuando:

```text
- La estructura raíz coincide con este documento.
- pnpm install funciona.
- pnpm lint funciona.
- pnpm typecheck funciona.
- pnpm test funciona, aunque haya tests mínimos.
- pnpm build funciona.
- pnpm qa:health funciona.
- docs fuente de verdad están en /docs.
- CLAUDE.md existe.
- .env.example existe en raíz y apps.
- No existen secretos reales.
- No hay código legacy copiado como base.
- No hay integración real de proveedor todavía.
- No hay pagos reales todavía.
```

---

## 46. Decisiones cerradas en este documento

```text
1. El proyecto usará monorepo pnpm + Turborepo.
2. Las apps oficiales iniciales son api, web, edge y qa.
3. Las superficies Platform, Condo, Guard y Resident viven en apps/web v1.
4. QA Harness vive en apps/qa.
5. Dominio puro vive en packages/domain.
6. Contratos compartidos viven en packages/contracts.
7. Prisma vive en packages/db.
8. Provider contracts viven en packages/provider-sdk.
9. Edge adapters reales viven en apps/edge/src/provider.
10. Pagos viven en apps/api/src/modules/payments.
11. Documentación fuente de verdad vive en docs.
12. Infraestructura vive en infra.
13. Reportes generados viven en artifacts.
14. La estructura protege Cloud, Edge, dominio, UI, proveedores y QA de mezclarse.
```

---

## 47. Próximo documento recomendado

Después de este documento, el siguiente entregable recomendado es:

```text
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
```

Objetivo:

```text
Investigar Mercado Pago como primer proveedor de pagos para Tricor Hábitat, validando cuentas conectadas por condominio, webhooks, pago confirmado, SPEI, comisiones, conciliación, seguridad y fallback manual.
```

---

## 48. Estado final del documento

`REPO_STRUCTURE_V0.1.md` queda aprobado como base estructural para crear el repositorio limpio de Tricor Hábitat.

El repo no debe iniciarse desde una base legacy. Debe crearse como proyecto nuevo, con esta estructura y los documentos fuente de verdad dentro de `/docs`.

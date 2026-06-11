# AI_AGENT_RULES_AND_HANDOFF.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** Draft formal inicial / fuente de verdad provisional para trabajo con agentes IA  
**Audiencia:** Product Owner, arquitecto, Claude Code CLI, revisores técnicos y futuros mantenedores  
**Propósito:** Definir reglas estrictas para que los agentes IA desarrollen Tricor Hábitat sin mezclar contextos, sin generar parches descontrolados y sin romper el flujo de negocio validado.

---

## 1. Propósito del documento

Este documento establece el protocolo oficial para desarrollar Tricor Hábitat con apoyo de agentes IA, especialmente Claude Code CLI como ejecutor principal del repositorio.

El objetivo es evitar repetir el ciclo de trabajo que provoca deuda técnica:

```text
prompt ambiguo
→ cambio amplio
→ prueba manual incompleta
→ fallo nuevo
→ parche
→ ruptura de flujo vecino
→ pérdida de control del proyecto
```

A partir de este documento, cualquier agente IA que participe en Tricor Hábitat debe trabajar bajo reglas explícitas de contexto, alcance, pruebas, documentación, seguridad y entrega.

El objetivo correcto es:

```text
fuente de verdad documentada
→ tarea acotada
→ plan técnico mínimo
→ implementación controlada
→ QA Harness
→ reporte
→ revisión humana
```

---

## 2. Principio rector

Tricor Hábitat no se desarrollará por intuición del agente.

Tricor Hábitat se desarrollará por:

```text
Documentos fuente de verdad
+ contratos canónicos
+ Policy Engine
+ Cloud + Edge
+ QA Harness
+ cambios pequeños y auditables
```

Ningún agente puede inventar arquitectura, permisos, proveedores, flujos financieros, reglas de acceso o comportamiento de Edge si no están definidos en los documentos del proyecto.

Si falta una decisión, el agente debe marcarla como pendiente, no resolverla con suposiciones silenciosas.

---

## 3. Fuente de verdad oficial

Los siguientes documentos son fuente de verdad para Tricor Hábitat v0.1:

```text
PROJECT_CHARTER_TRICOR_HABITAT.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
QA_HARNESS_SPEC_V0.1.md
ROADMAP_V0.1.md
AI_AGENT_RULES_AND_HANDOFF.md
```

El orden de prioridad es:

```text
1. AI_AGENT_RULES_AND_HANDOFF.md
2. PROJECT_CHARTER_TRICOR_HABITAT.md
3. DOMAIN_MODEL_V0.1.md
4. ACCESS_POLICY_CONFIGURATION_V0.1.md
5. CLOUD_EDGE_ARCHITECTURE_V0.1.md
6. QA_HARNESS_SPEC_V0.1.md
7. ROADMAP_V0.1.md
```

Si hay conflicto entre documentos, no se debe implementar hasta documentar el conflicto y proponer una resolución.

---

## 4. Contextos que no deben mezclarse

El agente debe entender que Tricor Hábitat es un proyecto independiente.

No debe mezclar contexto de:

```text
- SATBot
- TryHardNames
- prototipos anteriores
- repositorios legacy
- conversaciones externas no incluidas en /docs
- decisiones antiguas no documentadas
- integraciones vetadas
- experimentos no aprobados
```

Regla estricta:

```text
El contexto válido vive en /docs y en el código actual del repo.
```

Si el agente recuerda o infiere algo fuera de esos documentos, debe tratarlo como no confiable.

---

## 5. Rol de cada participante

### 5.1 Product Owner

Responsable de:

```text
- definir prioridades comerciales
- aprobar cambios de alcance
- validar decisiones de producto
- aceptar o rechazar propuestas de UX
- aprobar integraciones externas
- decidir pricing y estrategia comercial
```

El Product Owner no debe ser obligado a revisar detalles internos de código para aprobar una feature. El agente debe entregar reportes claros.

### 5.2 Arquitecto

Responsable de:

```text
- mantener coherencia de arquitectura
- actualizar documentos fuente de verdad
- revisar límites del dominio
- detectar deuda técnica
- definir contratos canónicos
- auditar cambios estructurales
```

### 5.3 Claude Code CLI

Responsable de:

```text
- implementar tareas acotadas
- editar archivos del repo
- ejecutar pruebas
- generar reportes
- respetar documentos fuente de verdad
- no inventar arquitectura
- no hacer refactors no solicitados
```

Claude Code CLI será el ejecutor principal del repositorio Tricor Hábitat.

### 5.4 QA Harness

Responsable de validar objetivamente que el cambio no rompe el sistema.

No es un accesorio. Es gate obligatorio.

### 5.5 Revisor humano

Responsable de:

```text
- revisar reportes
- aprobar merges
- validar que el cambio cumpla el alcance
- bloquear cambios peligrosos
```

---

## 6. Regla de un solo ejecutor

En Tricor Hábitat solo debe haber un agente ejecutor modificando código a la vez.

Regla:

```text
Un agente edita.
Los demás revisan, diseñan o auditan.
```

No se permite que dos agentes modifiquen simultáneamente el mismo repo, la misma rama o el mismo módulo.

Esto evita:

```text
- conflictos invisibles
- soluciones duplicadas
- parches contradictorios
- pérdida de trazabilidad
- mezcla de estilos
```

---

## 7. Aislamiento de workspace

Tricor Hábitat debe vivir en una carpeta separada.

Estructura recomendada:

```text
projects/
├── satbot/
├── tricor-habitat/
└── legacy-reference/
```

Reglas:

```text
- Claude Code CLI solo debe abrir projects/tricor-habitat.
- No debe abrir satbot.
- No debe copiar código de otros proyectos sin orden explícita.
- No debe usar legacy-reference como fuente de verdad.
- Si consulta legacy-reference, debe tratarlo como referencia histórica, no como arquitectura vigente.
```

---

## 8. Stack técnico aprobado

El stack base aprobado para v1 es:

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
El agente no puede cambiar el stack sin RFC aprobado.
```

Cambios prohibidos sin aprobación:

```text
- cambiar NestJS por otro backend
- cambiar PostgreSQL por otra base principal
- cambiar Prisma por otro ORM
- separar apps prematuramente
- introducir framework móvil en v1
- introducir motor externo de workflows como core
- introducir proveedor de pago diferente al research aprobado
```

---

## 9. Estructura de repositorio objetivo

Estructura inicial recomendada:

```text
tricor-habitat/
├── apps/
│   ├── api/              # Cloud API NestJS
│   ├── web/              # Next.js: Platform, Condo, Guard, Resident
│   ├── edge/             # Edge Agent
│   └── qa/               # QA Harness CLI
├── packages/
│   ├── contracts/        # contratos canónicos compartidos
│   ├── domain/           # reglas puras de dominio
│   ├── db/               # Prisma schema/client
│   ├── provider-sdk/     # interfaces de proveedores
│   ├── ui/               # componentes UI compartidos
│   └── config/           # tsconfig, eslint, env schemas
├── docs/
│   ├── PROJECT_CHARTER_TRICOR_HABITAT.md
│   ├── DOMAIN_MODEL_V0.1.md
│   ├── ACCESS_POLICY_CONFIGURATION_V0.1.md
│   ├── CLOUD_EDGE_ARCHITECTURE_V0.1.md
│   ├── QA_HARNESS_SPEC_V0.1.md
│   ├── ROADMAP_V0.1.md
│   └── AI_AGENT_RULES_AND_HANDOFF.md
├── artifacts/
│   └── qa/
├── docker-compose.yml
├── package.json
├── pnpm-workspace.yaml
├── turbo.json
└── CLAUDE.md
```

---

## 10. Regla sobre nombres del producto

Nombres oficiales:

```text
Tricor Hábitat = producto completo
Tricor Platform = panel interno SaaS
Tricor Condo = panel del condominio
Tricor Guard = interfaz de guardia
Tricor Resident = interfaz de residente
```

No usar nombres alternativos en código, docs o UI sin aprobación.

Ejemplos incorrectos:

```text
Tricorp
War
Ward
AVIT como nombre público principal
```

---

## 11. Alcance v1 que el agente debe respetar

El núcleo v1 es:

```text
pagos
→ morosidad
→ política de acceso
→ desired state
→ Edge
→ proveedor
→ observed state
→ auditoría
```

El agente debe priorizar ese flujo antes que funcionalidades periféricas.

Superficies v1:

```text
Tricor Platform
Tricor Condo
Tricor Guard
Tricor Resident
```

Pero v1 se implementa como una app web modular, no como cuatro apps separadas.

---

## 12. Fuera de alcance v1

No implementar en v1:

```text
- facturación fiscal propia
- chat de residentes
- amenidades como módulo completo
- red social
- contabilidad fiscal completa
- apps móviles nativas
- workflows libres por cliente
- automatizaciones críticas fuera del core
- múltiples proveedores reales simultáneos sin contratos validados
- módulos premium no aprobados
```

El agente puede crear interfaces, contratos o placeholders documentados para futuro solo si el roadmap lo pide explícitamente.

---

## 13. Reglas de documentación antes de código

Antes de implementar un módulo nuevo, debe existir al menos:

```text
- propósito del módulo
- entidades afectadas
- reglas de negocio
- permisos requeridos
- eventos de dominio
- pruebas QA esperadas
- impacto en Cloud/Edge si aplica
```

Si no existe, el agente debe proponer un parche de documentación primero.

---

## 14. Regla de cambio mínimo

Cada tarea debe modificar el mínimo número razonable de archivos.

El agente debe evitar:

```text
- refactorizar módulos no relacionados
- renombrar entidades sin necesidad
- cambiar estructura del repo durante una feature
- mezclar backend, UI, Edge y QA en un mismo cambio gigante
- corregir problemas no solicitados sin reportarlos antes
```

Si detecta un problema fuera del alcance, debe documentarlo en el reporte como:

```text
Out of scope issue detected:
- ubicación
- riesgo
- recomendación
- no modificado en este cambio
```

---

## 15. Tipos de cambio permitidos

### 15.1 Docs-only

Cambios en `/docs` sin código.

Requiere:

```text
- coherencia con documentos existentes
- no introducir decisiones no aprobadas
- actualizar índice si existe
```

### 15.2 Domain-only

Cambios en `packages/domain` o contratos puros.

Requiere:

```text
- pruebas unitarias
- actualización de contratos si aplica
- no tocar provider adapter salvo necesidad justificada
```

### 15.3 Backend feature

Cambios en `apps/api`.

Requiere:

```text
- pruebas unitarias
- pruebas de integración
- QA Harness si toca flujo crítico
- migración si cambia base de datos
```

### 15.4 UI feature

Cambios en `apps/web`.

Requiere:

```text
- respetar roles
- respetar permisos
- no inventar endpoints
- usar contratos compartidos
- cubrir vista mobile/PWA si aplica Guard o Resident
```

### 15.5 Edge feature

Cambios en `apps/edge`.

Requiere:

```text
- pruebas de cola
- pruebas offline
- pruebas de idempotencia
- provider mock
- logs técnicos
```

### 15.6 Provider feature

Cambios en provider adapters.

Requiere:

```text
- ProviderCapabilityMatrix
- provider mock
- provider contract test
- no contaminar dominio con términos del proveedor
```

### 15.7 Payment feature

Cambios en pagos.

Requiere:

```text
- PaymentProviderAdapter
- webhook validation
- payment mock
- idempotencia
- auditoría
- no mezclar dinero del SaaS con dinero del condominio
```

### 15.8 QA feature

Cambios en QA Harness.

Requiere:

```text
- comando CLI documentado
- fixture determinística
- reporte markdown/json
- integración con CI si aplica
```

---

## 16. Ciclo obligatorio de trabajo para Claude Code CLI

Todo cambio debe seguir este ciclo:

```text
1. Leer documentos relevantes.
2. Identificar alcance exacto.
3. Presentar plan corto.
4. Ejecutar preflight.
5. Implementar cambio mínimo.
6. Ejecutar pruebas relevantes.
7. Ejecutar QA Harness si aplica.
8. Generar reporte.
9. No declarar éxito si no pasó el gate.
```

---

## 17. Preflight obligatorio

Antes de modificar código, el agente debe ejecutar o verificar:

```text
pnpm install
pnpm lint
pnpm typecheck
pnpm test
pnpm qa:health
```

Si el repo aún no tiene esos comandos, el agente debe:

```text
- reportar que no existen
- crear los scripts mínimos si la tarea lo permite
- no fingir que pasaron
```

---

## 18. Comandos QA esperados

Comandos objetivo del QA Harness:

```text
pnpm qa:health
pnpm qa:config:defaults
pnpm qa:config:valid
pnpm qa:config:invalid
pnpm qa:config:dependencies
pnpm qa:config:provider-capabilities
pnpm qa:flow property-debt-access-lifecycle
pnpm qa:flow manual-payment-fallback-lifecycle
pnpm qa:flow payment-confirmed-edge-offline
pnpm qa:flow resident-move-out-credential-revocation
pnpm qa:flow lost-credential
pnpm qa:provider-contract zkteco --mock
pnpm qa:edge sync
pnpm qa:payments mercado-pago --mock
pnpm qa:report
```

No todos existirán desde el primer commit, pero el roadmap debe construirlos progresivamente.

---

## 19. Gate mínimo para aceptar cambios

Un cambio no debe considerarse DONE si no cumple:

```text
- compila
- pasa typecheck
- pasa lint
- pasa pruebas unitarias relevantes
- pasa QA Harness relevante
- genera reporte
- no rompe documentos fuente de verdad
- no modifica alcance no solicitado
```

Para módulos críticos:

```text
pagos
morosidad
Policy Engine
credenciales
Guard
Edge
provider adapters
```

el QA Harness es obligatorio.

---

## 20. Formato obligatorio del reporte de agente

Cada entrega de Claude Code CLI debe terminar con un reporte:

```text
# Agent Work Report

## Task
Descripción corta.

## Scope
Archivos/módulos tocados.

## Source documents consulted
- docs/...

## Changes made
- cambio 1
- cambio 2

## Tests run
- comando: PASS/FAIL

## QA Harness
- suite: PASS/FAIL/NOT_AVAILABLE

## Risks
- riesgo si existe

## Out of scope findings
- hallazgo no modificado

## Manual verification needed
- paso si aplica

## Final status
PASS / BLOCKED / PARTIAL
```

Si algo falló, el estado final no puede ser PASS.

---

## 21. Reglas para branch y commits

Branch naming recomendado:

```text
feature/<area>-<short-description>
fix/<area>-<short-description>
docs/<short-description>
qa/<short-description>
refactor/<area>-<short-description>
```

Ejemplos:

```text
feature/policy-engine-morosity-defaults
feature/edge-command-queue
qa/property-debt-access-lifecycle
fix/payment-webhook-idempotency
docs/domain-model-clarifications
```

Commits recomendados:

```text
docs: add AI agent handoff rules
feat(policy): add morosity preset validator
feat(edge): add command queue states
qa: add manual payment fallback flow
fix(payments): make webhook handler idempotent
```

---

## 22. Política de refactors

Refactors permitidos solo si:

```text
- están en el alcance
- tienen pruebas
- reducen complejidad real
- no mezclan features
- no cambian contratos sin migración
```

Refactors prohibidos:

```text
- reestructurar todo el repo durante una feature
- cambiar nombres de dominio sin actualizar docs
- mover módulos por preferencia estética
- cambiar stack sin RFC
- cambiar patrón de arquitectura sin aprobación
```

---

## 23. RFC obligatorio

Se requiere RFC para:

```text
- cambiar stack
- cambiar modelo de dominio
- cambiar jerarquía Condominio → Privada → Propiedad
- agregar proveedor real nuevo
- agregar proveedor de pagos nuevo
- cambiar flujo de restauración
- cambiar reglas default de morosidad
- cambiar arquitectura Cloud + Edge
- introducir app móvil nativa
- agregar integración premium
- cambiar estrategia de storage
```

Formato mínimo:

```text
# RFC: título

## Problema

## Propuesta

## Alternativas consideradas

## Impacto en dominio

## Impacto en QA Harness

## Riesgos

## Plan de migración

## Criterio de aceptación
```

---

## 24. Reglas de dominio que el agente no puede violar

### 24.1 Jerarquía interna

Debe mantenerse:

```text
Condominio → Privada → Propiedad
```

Aunque en UI se permita modelar privadas pequeñas o comunidades simples, internamente no se debe romper la estructura.

### 24.2 Propiedad

Reglas:

```text
- la morosidad se controla por propiedad
- una propiedad tiene un responsable principal
- un responsable puede tener varias propiedades
- una propiedad no debe tener varios propietarios principales
- la propiedad tiene límites de credenciales
```

### 24.3 Credenciales

Reglas:

```text
- las credenciales se ligan a propiedad y responsable principal
- no se requiere censo de habitantes
- el residente puede bloquear credencial propia por pérdida
- el residente no puede registrar nuevas credenciales
- administración registra reemplazos
- exresidentes pierden credenciales según flujo de salida
```

### 24.4 Pagos

Reglas:

```text
- pago confirmado por proveedor restaura automáticamente
- comprobante no restaura por sí solo
- transferencia manual es fallback auditado
- Finance puede aprobar pagos manuales
- Finance no edita permisos directamente
```

### 24.5 Accesos

Reglas:

```text
- no se suspende la propiedad
- no se suspende vehículo como entidad
- se suspenden capacidades/permisos de acceso
- acceso vehicular existe como zona/capacidad
- moroso default conserva acceso peatonal y bloquea vehicular + QR invitados
```

### 24.6 Guard

Reglas:

```text
- Guard no edita residentes
- Guard no edita pagos
- Guard no registra credenciales
- Guard escanea QR y registra eventos
- GuardSupervisor puede apertura manual justificada
- GuardSupervisor puede excepción temporal justificada con límites y auditoría
```

### 24.7 Edge

Reglas:

```text
- Cloud es fuente de verdad
- Edge es ejecutor local
- proveedor es sistema externo
- Edge no calcula morosidad
- Edge no administra pagos
- Edge no administra residentes
- Edge reporta observed state
```

---

## 25. Reglas de pagos y dinero

Tricor Hábitat no debe ser intermediario financiero del mantenimiento.

Reglas:

```text
- el dinero de mantenimiento cae al condominio
- cada condominio conecta su propia cuenta de cobro
- Tricor procesa órdenes y estados
- Tricor cobra SaaS por separado desde Tricor Platform
- métricas operativas pueden auditarse desde Platform
- ingresos de licencia no se mezclan con dinero del condominio
```

El primer proveedor de pagos a investigar es Mercado Pago.

No implementar pagos reales sin:

```text
- PaymentProviderStrategy documentada
- webhooks validados
- idempotencia
- manejo de pagos pendientes
- manejo de pagos rechazados
- manejo de reembolsos/cancelaciones
- política de comisiones
- QA Harness payment mock
```

---

## 26. Reglas del Policy Engine

El Policy Engine debe calcular estados, no ejecutar directamente proveedores.

Flujo correcto:

```text
estado de propiedad
+ estado de pagos
+ reglas de morosidad
+ perfil de acceso
+ capacidades del proveedor
= desired access state
```

Luego:

```text
desired access state
→ AccessCommand
→ Edge
→ Provider Adapter
→ observed state
→ auditoría
```

Prohibido:

```text
- que UI active permisos directamente en proveedor
- que Finance edite permisos directamente
- que Guard edite permisos permanentes
- que provider adapter calcule morosidad
- que Edge decida reglas de negocio
```

---

## 27. Reglas de configuración

La configuración debe tener:

```text
- presets
- modo avanzado
- modo técnico/desarrollador
- validación de dependencias
- vista de impacto
- auditoría de cambios
```

No se deben crear 200 toggles sin estructura.

Cada configuración debe responder:

```text
1. ¿Qué problema resuelve?
2. ¿Quién puede modificarla?
3. ¿Qué módulo afecta?
4. ¿Qué dependencias tiene?
5. ¿Con qué proveedor es compatible?
6. ¿Qué pruebas QA la validan?
7. ¿Qué pasa si se activa incorrectamente?
```

---

## 28. Reglas de ProviderCapabilityMatrix

Todo proveedor debe declarar capacidades.

Ejemplo conceptual:

```text
ProviderCapabilityMatrix:
- supportsAccessProfiles
- supportsDoorMapping
- supportsZoneMapping
- supportsCardCredentials
- supportsQrCredentials
- supportsBiometricCredentials
- supportsObservedStatePolling
- requiresEdge
- supportsOfflineSync
```

La UI y el Policy Engine no pueden asumir capacidades.

Regla:

```text
Si el proveedor no declara soporte, la capacidad no se considera disponible.
```

---

## 29. Reglas para provider adapters

El adapter del proveedor debe vivir aislado.

Debe traducir:

```text
contrato canónico de Tricor
→ operación específica del proveedor
```

No debe contaminar:

```text
- dominio
- UI
- Policy Engine
- pagos
- roles
```

Todo adapter requiere:

```text
- capability matrix
- mock
- contract tests
- mapeo de errores
- estrategia de retries
- logs seguros
- documentación
```

---

## 30. Reglas de Edge

Edge debe implementar:

```text
- enrollment seguro
- heartbeat
- command queue
- idempotencia
- retry policy
- local snapshot
- provider adapter
- observed state reporting
- local technical UI
- logs técnicos
```

Edge no debe implementar:

```text
- edición de residentes
- edición de pagos
- cálculo de morosidad
- administración de propiedades
- UI operativa del condominio
```

---

## 31. Reglas de offline

Cuando Edge está offline:

```text
- Cloud no debe marcar comandos como aplicados
- comandos quedan pendientes
- se crea incidente operativo
- Platform/Condo deben mostrar estado realista
```

Estados sugeridos:

```text
PENDING_EDGE_OFFLINE
PAYMENT_CONFIRMED_ACCESS_PENDING
APPLIED_UNCONFIRMED
APPLIED_CONFIRMED
FAILED_PROVIDER
FAILED_POLICY
```

El agente no debe simplificar estos estados a booleanos como `isActive` si se pierde información operacional.

---

## 32. Reglas de auditoría

Toda acción crítica debe dejar auditoría.

Acciones críticas:

```text
- aprobar pago manual
- rechazar pago manual
- restaurar acceso
- restringir acceso
- cambiar política de morosidad
- cambiar provider mapping
- cambiar permisos de rol
- bloquear credencial perdida
- revocar credencial
- archivar residente
- crear excepción temporal
- apertura manual
- cambio de estado de Edge
```

Auditoría mínima:

```text
actor
rol
scope
acción
entidad afectada
estado anterior
estado nuevo
motivo
timestamp
source
correlationId
```

---

## 33. Reglas de seguridad

El agente no puede:

```text
- hardcodear secretos
- imprimir tokens en logs
- guardar llaves en repositorio
- exponer datos financieros en logs
- exponer detalles financieros a Guard
- permitir bypass de roles
- aceptar webhooks sin validación
- confiar en datos del cliente sin validar
```

Debe usar:

```text
- env schema validation
- secrets vía variables de entorno
- logs redactados
- permisos por rol y scope
- validación server-side
- auditoría
```

---

## 34. Reglas de datos y migraciones

Toda migración debe:

```text
- ser reversible cuando sea posible
- no destruir datos sin plan
- incluir defaults seguros
- mantener auditoría
- actualizar Prisma schema
- actualizar fixtures QA
- documentar impacto
```

Prohibido:

```text
- borrar columnas con datos sin migración de respaldo
- cambiar enums sin migración clara
- crear campos booleanos ambiguos para estados complejos
- meter JSON libre para reglas críticas sin validación
```

---

## 35. Reglas de estados

No usar booleanos cuando se necesitan estados.

Evitar:

```text
isPaid
isActive
isBlocked
isSynced
```

Preferir:

```text
PaymentStatus
PropertyAccessStatus
CredentialStatus
CommandStatus
EdgeHealthStatus
ProviderSyncStatus
```

Motivo:

```text
Un booleano oculta estados intermedios como pendiente, rechazado, vencido, confirmado, aplicado, fallido, offline o en revisión.
```

---

## 36. Reglas para Tricor Platform

Tricor Platform es el panel interno SaaS.

Puede gestionar:

```text
- clientes
- condominios
- planes
- licencias
- estado de Edge
- uso por residente/puerta/propiedad
- estado de proveedores
- soporte
- auditoría global
```

No debe gestionar directamente:

```text
- dinero de mantenimiento
- pagos individuales de residentes como operador financiero principal
- permisos internos del condominio salvo soporte auditado
```

Soporte de Platform debe ser auditado.

---

## 37. Reglas para Tricor Condo

Tricor Condo es el panel operativo del condominio.

Debe gestionar:

```text
- privadas
- propiedades
- responsables
- credenciales
- pagos
- multas
- cargos
- morosidad
- reglas
- guardias
- QR
- proveedor
- Edge
- auditoría del condominio
```

Debe respetar scopes:

```text
CONDO_ADMIN → todo el condominio
PRIVATE_ADMIN → solo su privada
FINANCE_OPERATOR → pagos y finanzas
GUARD_SUPERVISOR → operación de guardia
GUARD → operación limitada
RESIDENT → vista propia
```

---

## 38. Reglas para Tricor Guard

Tricor Guard debe ser mobile-first/PWA desde v1.

Debe permitir:

```text
- login de guardia
- escaneo QR
- consulta de estado permitido/suspendido
- registro de eventos
- reportes/rondines básicos
- apertura manual para GuardSupervisor
- excepción temporal para GuardSupervisor con límites y auditoría
```

No debe permitir:

```text
- editar residentes
- editar propiedades
- editar pagos
- registrar nuevas credenciales
- cambiar reglas de morosidad
- ver detalle financiero innecesario
```

---

## 39. Reglas para Tricor Resident

Tricor Resident debe ser mobile-first/PWA desde v1.

Debe permitir:

```text
- ver estado de cuenta
- ver multas/cargos
- pagar en línea cuando esté disponible
- subir comprobante cuando aplique fallback
- ver estado de acceso
- generar QR invitados si la política lo permite
- bloquear credencial propia por pérdida
```

No debe permitir:

```text
- registrar nuevas credenciales
- editar reglas de acceso
- cambiar propiedad asociada sin aprobación
- restaurar acceso por comprobante no validado
```

---

## 40. Reglas sobre QR

QR residente e invitado deben ser seguros.

Reglas:

```text
- no QR estático permanente sin control
- QR invitado con expiración
- QR invitado puede ser uso único o ventana limitada
- QR debe invalidarse si cambia estado crítico
- QR debe respetar morosidad
- lectura de QR debe auditarse
```

El agente no debe implementar QR como texto plano sin firma o sin expiración.

---

## 41. Reglas de credenciales perdidas

El residente puede reportar pérdida.

Flujo:

```text
residente reporta pérdida
→ credencial cambia a LOST_REPORTED
→ acceso de esa credencial se bloquea/revoca
→ administración recibe alerta
→ administración registra reemplazo si procede
```

No se debe permitir que el residente cree la credencial reemplazo.

---

## 42. Reglas de exresidentes y mudanza

Debe existir flujo de salida.

Estados:

```text
ACTIVE
MOVE_OUT_SCHEDULED
MOVE_OUT_GRACE_PERIOD
FORMER_RESIDENT
ARCHIVED
```

Reglas:

```text
- puede haber días de gracia por mudanza
- al expirar, se revocan credenciales
- no se elimina historial
- credenciales obsoletas se auditan
- credenciales sin uso generan alerta no invasiva
```

---

## 43. Reglas de alertas

No todo debe ser alerta urgente.

Clasificación:

```text
INFO
REVIEW
WARNING
CRITICAL
```

Ejemplos:

```text
credencial sin uso 120 días → REVIEW
Edge offline → WARNING/CRITICAL según duración
comando fallido en proveedor → WARNING
pago confirmado con acceso pendiente → WARNING
credencial activa de exresidente → CRITICAL
```

---

## 44. Reglas para integraciones futuras

Integraciones futuras deben clasificarse antes de implementar:

```text
Core
Research
Premium futuro
Descartado v1
```

Ninguna integración futura puede convertirse en dependencia crítica del core sin RFC.

Ejemplos de integración futura:

```text
- facturación externa
- reportes automáticos
- bots de soporte
- helpdesk limitado
- exportación contable
- calendarios si algún día aplica
```

El agente no debe implementar integraciones porque “podrían ser útiles”. Debe existir decisión documentada.

---

## 45. Reglas de investigación

Cuando una tarea sea research, el resultado debe ser documento, no código.

Formato:

```text
# RESEARCH_<TOPIC>.md

## Pregunta principal

## Fuentes revisadas

## Capacidades confirmadas

## Limitaciones

## Riesgos

## Recomendación

## Impacto en Tricor

## Decisión propuesta
```

Research prioritario:

```text
- Mercado Pago
- Hikvision
- pricing de mercado
- facturación externa
```

---

## 46. Regla de no asumir soporte de proveedor

El agente no puede asumir que un proveedor soporta:

```text
- QR
- biometría
- perfiles
- puertas
- zonas
- observed state
- operación offline
- cambios masivos
- revocación granular
```

Debe documentarlo en ProviderCapabilityMatrix.

---

## 47. Reglas de UI

La UI debe reflejar permisos y capacidades reales.

Reglas:

```text
- no mostrar opciones incompatibles como si funcionaran
- deshabilitar opciones no soportadas
- explicar dependencias
- mostrar vista de impacto antes de aplicar cambios críticos
- no exponer detalle financiero a Guard
- mobile-first para Guard y Resident
```

Ejemplo de vista de impacto:

```text
Este cambio afectará:
- 42 propiedades morosas
- 17 QR invitados activos
- 3 comandos pendientes de Edge
- 1 proveedor requiere remapeo
```

---

## 48. Reglas de errores

Errores deben ser específicos.

Evitar:

```text
Algo salió mal.
```

Preferir:

```text
No se puede activar bloqueo de QR invitados porque QR invitados está desactivado en este condominio.
```

Los errores deben incluir:

```text
- código interno
- mensaje seguro
- correlationId
- recomendación operativa si aplica
```

---

## 49. Reglas de logs

Logs deben ser útiles y seguros.

Deben incluir:

```text
- correlationId
- tenantId/condoId si aplica
- commandId si aplica
- providerInstallationId si aplica
- estado anterior/nuevo cuando sea seguro
```

No deben incluir:

```text
- tokens
- secretos
- datos sensibles completos
- comprobantes completos
- datos financieros innecesarios
```

---

## 50. Reglas de fixtures

Fixtures QA deben ser determinísticas.

Fixtures mínimas:

```text
- condominio demo
- privada demo
- propiedad al corriente
- propiedad morosa
- responsable principal
- credenciales activas
- credencial perdida
- guard normal
- guard supervisor
- finance operator
- condo admin
- provider mock
- Edge mock
- payment mock
```

No usar datos aleatorios sin seed fija.

---

## 51. Reglas de mocks

Mocks no deben ser “todo OK”.

Deben simular:

```text
- proveedor disponible
- proveedor caído
- Edge offline
- comando duplicado
- pago pendiente
- pago aprobado
- pago rechazado
- credencial no encontrada
- provider mapping incompleto
- capability no soportada
```

---

## 52. Reglas de idempotencia

Deben ser idempotentes:

```text
- webhooks de pago
- comandos Edge
- provider operations
- generación de desired state
- creación de eventos críticos
```

Si llega dos veces el mismo webhook, no se debe restaurar dos veces ni duplicar estados.

Si se reintenta un comando, no debe generar un estado incoherente.

---

## 53. Reglas de consistencia

Para flujos críticos usar patrón de eventos/outbox cuando aplique.

Ejemplo:

```text
PaymentConfirmed
→ AccessStateRecalculationRequested
→ AccessCommandCreated
→ EdgeCommandDispatched
→ ProviderOperationApplied
→ ObservedStateUpdated
```

No mezclar todo en una transacción gigante con llamadas externas dentro.

---

## 54. Reglas de performance v1

No se busca optimización prematura, pero sí evitar errores obvios.

El agente debe evitar:

```text
- consultas N+1 en vistas principales
- cargar todos los residentes sin paginación
- logs excesivos
- polling agresivo
- archivos pesados en base de datos
```

Debe usar:

```text
- paginación
- filtros por scope
- índices básicos
- storage externo para archivos
- jobs para procesos pesados
```

---

## 55. Reglas de storage

Comprobantes y archivos deben guardarse en storage S3-compatible.

Base de datos guarda:

```text
- metadata
- owner
- status
- storage key
- hash si aplica
- timestamps
- auditoría
```

No guardar archivos binarios pesados en PostgreSQL salvo justificación aprobada.

---

## 56. Reglas para documentación de usuario final

Todo módulo orientado a usuario debe tener base para manual.

Manuales futuros:

```text
- Tricor Platform Manual
- Tricor Condo Manual
- Tricor Guard Manual
- Tricor Resident Manual
- Edge Technical Manual
```

Cada feature debe poder documentarse en lenguaje operativo.

Si una feature no se puede explicar claramente, probablemente está mal diseñada.

---

## 57. Reglas para cambios de alcance

Si durante implementación aparece una idea nueva, el agente debe clasificarla:

```text
- required for current task
- useful but out of scope
- future enhancement
- risky / needs RFC
- reject
```

No debe implementarla silenciosamente.

---

## 58. Prompt inicial recomendado para Claude Code CLI

Usar este prompt al iniciar el proyecto:

```text
You are working on the Tricor Hábitat repository.

Before editing any file, read:
- docs/AI_AGENT_RULES_AND_HANDOFF.md
- docs/PROJECT_CHARTER_TRICOR_HABITAT.md
- docs/DOMAIN_MODEL_V0.1.md
- docs/ACCESS_POLICY_CONFIGURATION_V0.1.md
- docs/CLOUD_EDGE_ARCHITECTURE_V0.1.md
- docs/QA_HARNESS_SPEC_V0.1.md
- docs/ROADMAP_V0.1.md

Treat those documents as the source of truth.
Do not use context from other projects.
Do not implement unapproved features.
Do not refactor unrelated modules.
Do not assume provider capabilities.
Do not bypass QA Harness.

For every task:
1. summarize the scope,
2. list files you expect to touch,
3. implement the smallest safe change,
4. run relevant tests,
5. generate an Agent Work Report.

If a requirement is missing or contradictory, stop and report the conflict instead of guessing.
```

---

## 59. Root CLAUDE.md recomendado

El repo debe incluir un `CLAUDE.md` en raíz con contenido resumido:

```text
# CLAUDE.md

This repository is Tricor Hábitat.

Read these documents before making changes:
- docs/AI_AGENT_RULES_AND_HANDOFF.md
- docs/PROJECT_CHARTER_TRICOR_HABITAT.md
- docs/DOMAIN_MODEL_V0.1.md
- docs/ACCESS_POLICY_CONFIGURATION_V0.1.md
- docs/CLOUD_EDGE_ARCHITECTURE_V0.1.md
- docs/QA_HARNESS_SPEC_V0.1.md
- docs/ROADMAP_V0.1.md

Rules:
- Do not use context from SATBot, TryHardNames, legacy repos, or external conversations.
- Implement minimal changes only.
- Do not invent architecture.
- Do not assume provider capabilities.
- Do not change stack without RFC.
- Do not bypass QA Harness.
- Run relevant tests before reporting success.
- Always produce an Agent Work Report.

Core domain:
- Condominio → Privada → Propiedad.
- Payments and morosity are controlled by property.
- Cloud is source of truth.
- Edge is local executor.
- Provider adapters are isolated.
- Policy Engine calculates desired access state.

A change is not DONE unless it passes the relevant tests and QA Harness gates.
```

---

## 60. Plantilla de tarea para Product Owner

Para pedir una implementación:

```text
Task: <nombre corto>

Goal:
<qué debe lograr>

Scope:
<archivos o módulos esperados>

Source docs:
<documentos relevantes>

Must pass:
<comandos QA/pruebas>

Do not touch:
<módulos prohibidos para esta tarea>

Acceptance criteria:
- criterio 1
- criterio 2
- criterio 3
```

Ejemplo:

```text
Task: Add morosity standard preset validator

Goal:
Implement validation for the standard morosity preset.

Scope:
packages/domain/policy
apps/qa config dependency tests

Source docs:
ACCESS_POLICY_CONFIGURATION_V0.1.md
QA_HARNESS_SPEC_V0.1.md

Must pass:
pnpm test
pnpm qa:config:defaults
pnpm qa:config:dependencies

Do not touch:
apps/web
apps/edge
payment provider code

Acceptance criteria:
- standard preset allows pedestrian access
- standard preset blocks guest QR
- standard preset blocks vehicular access
- invalid provider capability fails validation
```

---

## 61. Plantilla de reporte de fallo

Cuando algo falle:

```text
# Failure Report

## Command
<comando ejecutado>

## Failure
<mensaje o resumen>

## Expected
<resultado esperado>

## Actual
<resultado real>

## Suspected area
<módulo probable>

## Files inspected
- archivo 1
- archivo 2

## Recommendation
<fix mínimo sugerido>

## Not changed
<qué no se tocó>
```

---

## 62. Definición de DONE general

Una tarea está DONE solo si:

```text
- el alcance fue respetado
- el código compila
- lint pasa
- typecheck pasa
- pruebas relevantes pasan
- QA Harness relevante pasa
- docs actualizadas si aplica
- reporte generado
- no hay fallos ocultos
- no hay cambios fuera de alcance
```

Si no hay QA Harness implementado aún, el agente debe marcar:

```text
QA Harness: NOT_AVAILABLE
Reason: <motivo>
Replacement verification: <prueba temporal>
Follow-up required: <sí/no>
```

---

## 63. Definición de BLOCKED

Una tarea queda BLOCKED si:

```text
- falta decisión de producto
- documentos se contradicen
- proveedor no tiene capacidad confirmada
- faltan secretos o entorno
- falla una prueba crítica no relacionada y no se puede aislar
- la tarea requiere cambiar alcance mayor
```

El agente no debe resolver un BLOCKED con supuestos.

---

## 64. Secuencia recomendada para iniciar repo con Claude Code CLI

Orden recomendado:

```text
1. Crear repo base monorepo.
2. Agregar /docs con documentos v0.1.
3. Agregar CLAUDE.md.
4. Crear pnpm workspace y turbo.
5. Crear packages/contracts.
6. Crear packages/domain.
7. Crear packages/db con Prisma inicial.
8. Crear apps/qa con comandos mínimos.
9. Crear apps/api shell.
10. Crear apps/web shell.
11. Crear apps/edge shell.
12. Implementar QA Harness health.
13. Implementar domain contracts.
14. Implementar primer flujo property-debt-access-lifecycle con mocks.
```

No empezar por UI visual avanzada.

---

## 65. Primeras tareas recomendadas para Claude Code CLI

### Tarea 1: Bootstrap monorepo

```text
Crear estructura base pnpm + Turborepo.
Agregar scripts lint/typecheck/test.
Agregar docs.
Agregar CLAUDE.md.
```

### Tarea 2: Contratos canónicos iniciales

```text
Crear tipos base para:
- Condo
- PrivateCommunity
- Property
- PrincipalResponsible
- Credential
- Payment
- AccessProfile
- AccessZone
- DesiredAccessState
- ObservedAccessState
- AccessCommand
```

### Tarea 3: QA Harness health

```text
Crear apps/qa con comando qa:health.
Validar env, docs presentes y estructura de repo.
```

### Tarea 4: Policy Engine skeleton

```text
Crear PolicyConfigValidator.
Crear presets Suave, Estándar, Estricto.
Crear pruebas de defaults.
```

### Tarea 5: ProviderCapabilityMatrix skeleton

```text
Crear matriz de capacidades.
Crear provider mock.
Crear pruebas de compatibilidad.
```

---

## 66. Decisiones cerradas en este documento

```text
1. Claude Code CLI será ejecutor principal del repo.
2. Documentos en /docs son fuente de verdad.
3. No se mezcla contexto de otros proyectos.
4. Un solo agente edita a la vez.
5. QA Harness es gate obligatorio.
6. No se implementan features por intuición.
7. Cambios estructurales requieren RFC.
8. Provider capabilities no se asumen.
9. Edge no es fuente de verdad.
10. Finance no edita permisos directamente.
11. Guard no edita residentes ni pagos.
12. Resident puede bloquear credencial perdida, no crear nueva.
13. El dinero de mantenimiento no pasa por Tricor Platform.
14. Stack técnico aprobado queda protegido.
15. Manuales de usuario deben derivarse del producto documentado.
```

---

## 67. Pendientes no bloqueantes

```text
- Crear CLAUDE.md real en el repo.
- Crear prompt inicial específico por fase.
- Crear RFC template como archivo independiente.
- Crear PR template.
- Crear Agent Work Report template.
- Crear Failure Report template.
- Crear Definition of Done checklist por módulo.
- Crear manuales de usuario final.
```

---

## 68. Criterio de aprobación

Este documento queda aprobado cuando:

```text
- define límites claros para agentes IA
- evita mezcla de contextos
- protege arquitectura Cloud + Edge
- protege dominio Condominio → Privada → Propiedad
- exige QA Harness
- define reportes obligatorios
- permite handoff limpio a Claude Code CLI
- no introduce tecnologías, integraciones o módulos fuera de alcance
```

---

## 69. Próximo documento recomendado

El siguiente documento recomendado es:

```text
USER_MANUAL_PLAN.md
```

Objetivo:

```text
Definir la estructura de manuales para Tricor Platform, Tricor Condo, Tricor Guard, Tricor Resident y Edge Technical Manual.
```

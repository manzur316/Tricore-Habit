# ADVANCED_IMPLEMENTATION_GUARDRAILS_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado documental:** CERTIFIED como guardrails de implementación avanzada  
**Estado global del paquete documental:** ver `DOCS_INDEX_AND_DEPENDENCY_MAP.md`  
**Estado de implementación avanzada:** BLOCKED hasta cumplir gates específicos  
**Fecha:** 2026-06-11  
**Archivo autoritativo:** `ADVANCED_IMPLEMENTATION_GUARDRAILS_V0.1.md`  
**Propósito:** definir límites, gates y condiciones de desbloqueo antes de permitir implementación avanzada con agentes IA.

---

## 0. Control de auditoría

### 0.1 Veredicto de creación

```text
Tarea: crear ADVANCED_IMPLEMENTATION_GUARDRAILS_V0.1.md
Veredicto: MATCH
Motivo:
- El paquete documental ya está en READY_FOR_BOOTSTRAP_REVIEW.
- La implementación productiva sigue bloqueada.
- Existen riesgos reales de que un agente intente avanzar antes de tiempo hacia providers reales, pagos reales, Edge productivo, migraciones productivas o módulos premium.
- Se requiere un documento de límites y gates, no una especificación de implementación detallada.
```

### 0.2 Alcance de este documento

Este documento define:

```text
- Qué puede hacer Claude/Codex ahora.
- Qué no puede hacer todavía.
- Qué condiciones desbloquean cada categoría avanzada.
- Qué requiere revisión humana.
- Qué requiere QA Harness.
- Qué requiere laboratorio.
- Qué requiere RFC técnico.
```

Este documento **no** implementa nada.

### 0.3 Archivos fuente conceptuales

Este documento se alinea con:

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md
README.md
AI_AGENT_RULES_AND_HANDOFF.md
ROADMAP_V0.1.md
PROJECT_CHARTER_TRICOR_HABITAT.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
QA_HARNESS_SPEC_V0.1.md
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
PROVIDER_RESEARCH_HIKVISION_V0.1.md
REPO_STRUCTURE_V0.1.md
USER_MANUAL_PLAN.md
```

### 0.4 Archivos que no modifica

```text
Este documento no modifica otros archivos.
Si se agrega al repo, debe registrarse de forma mínima en DOCS_INDEX_AND_DEPENDENCY_MAP.md y README.md.
Si se desea que Claude lo lea obligatoriamente, también debe agregarse al orden de lectura de AI_AGENT_RULES_AND_HANDOFF.md.
```

---

## 1. Propósito

Tricor Hábitat ya tiene documentación suficiente para pasar a revisión de bootstrap técnico.

Eso **no** significa que el proyecto esté listo para implementación avanzada.

La diferencia central:

```text
READY_FOR_BOOTSTRAP_REVIEW
≠
READY_FOR_FULL_IMPLEMENTATION
```

Este documento evita que un agente IA convierta el cierre documental en permiso para implementar:

```text
- proveedores reales
- pagos reales
- Edge productivo
- migraciones productivas
- módulos premium
- integraciones futuras
- arquitectura no aprobada
```

La función de este archivo es imponer gates.

---

## 2. Principio rector

Toda implementación avanzada debe seguir esta cadena:

```text
Documento base
→ contrato canónico
→ mock o simulador
→ QA Harness
→ reporte reproducible
→ revisión humana
→ laboratorio o sandbox real
→ aprobación explícita
→ implementación controlada
```

No se permite esta cadena:

```text
prompt amplio
→ agente implementa
→ prueba manual
→ parche
→ producción
```

---

## 3. Estados permitidos de una categoría de implementación

Cada categoría técnica debe estar en uno de estos estados:

```text
ALLOWED_NOW
ALLOWED_AFTER_BOOTSTRAP
DESIGN_ONLY
BLOCKED_UNTIL_QA
BLOCKED_UNTIL_LAB
BLOCKED_UNTIL_SANDBOX
BLOCKED_UNTIL_RFC
BACKLOG_ONLY
NOT_ALLOWED
```

Definición:

| Estado | Significado |
|---|---|
| `ALLOWED_NOW` | Puede trabajarse en la fase documental o de preparación inmediata. |
| `ALLOWED_AFTER_BOOTSTRAP` | Puede trabajarse después de que exista monorepo base y scripts mínimos. |
| `DESIGN_ONLY` | Puede diseñarse o documentarse, pero no implementarse productivamente. |
| `BLOCKED_UNTIL_QA` | Requiere QA Harness o pruebas automatizadas antes de avanzar. |
| `BLOCKED_UNTIL_LAB` | Requiere laboratorio con hardware o proveedor real. |
| `BLOCKED_UNTIL_SANDBOX` | Requiere sandbox real del proveedor. |
| `BLOCKED_UNTIL_RFC` | Requiere RFC técnico y aprobación humana. |
| `BACKLOG_ONLY` | Puede anotarse como futuro, pero no diseñarse ni implementarse ahora. |
| `NOT_ALLOWED` | No forma parte del producto o contradice decisiones vigentes. |

---

## 4. Matriz de autorización actual

| Categoría | Estado actual | Permitido ahora | Bloqueado |
|---|---|---|---|
| Documentación base | `ALLOWED_NOW` | ajustes menores, auditoría | reescritura sin causa |
| Bootstrap monorepo | `ALLOWED_NOW` tras aprobación humana | estructura, scripts, configs | lógica productiva |
| CLAUDE.md | `ALLOWED_NOW` | reglas operativas para Claude | desbloquear implementación avanzada |
| Contratos canónicos | `ALLOWED_AFTER_BOOTSTRAP` | tipos, interfaces, validadores | payloads de proveedor en dominio |
| Dominio puro | `ALLOWED_AFTER_BOOTSTRAP` | reglas puras y tests | SQL, APIs reales, providers |
| QA Harness skeleton | `ALLOWED_AFTER_BOOTSTRAP` | CLI base, fixtures, mocks iniciales | hardware real automático |
| Base de datos | `DESIGN_ONLY` hasta schema plan | propuesta Prisma revisable | migraciones productivas |
| Backend core | `BLOCKED_UNTIL_QA` | diseño por módulos | endpoints críticos sin tests |
| Mercado Pago mock | `ALLOWED_AFTER_BOOTSTRAP` | mock/payment adapter contract | dinero real |
| Mercado Pago sandbox | `BLOCKED_UNTIL_SANDBOX` | pruebas sandbox controladas | producción |
| Mercado Pago productivo | `BLOCKED_UNTIL_SANDBOX` | no permitido ahora | cobro real sin QA/sandbox |
| Edge simulator | `ALLOWED_AFTER_BOOTSTRAP` | colas, heartbeat, reconnect mock | control físico |
| Edge productivo | `BLOCKED_UNTIL_QA` + `BLOCKED_UNTIL_LAB` | no permitido ahora | puertas reales |
| ZKTeco mock contract | `ALLOWED_AFTER_BOOTSTRAP` | mocks y mapping canónico | CVSecurity real |
| ZKTeco real | `BLOCKED_UNTIL_LAB` | laboratorio manual controlado | producción |
| Hikvision mock contract | `ALLOWED_AFTER_BOOTSTRAP` | mocks y mapping canónico | dispositivo real |
| Hikvision real | `BLOCKED_UNTIL_LAB` | laboratorio posterior | producción |
| Módulos premium | `BACKLOG_ONLY` | lista controlada | diseño profundo / implementación |
| Apps móviles nativas | `BACKLOG_ONLY` | investigación futura | Flutter productivo v1 |
| Cambios de stack | `BLOCKED_UNTIL_RFC` | RFC | cambio directo |
| Integraciones no aprobadas | `NOT_ALLOWED` | no | implementación |

---

## 5. Qué puede hacer Claude/Codex ahora

Con el repo en `READY_FOR_BOOTSTRAP_REVIEW`, Claude/Codex puede trabajar únicamente en tareas de preparación.

### 5.1 Permitido

```text
- revisar documentación
- reportar contradicciones
- preparar CLAUDE.md
- preparar BOOTSTRAP_REPO_TASK.md
- crear monorepo base si hay aprobación humana explícita
- crear package.json raíz
- crear pnpm-workspace.yaml
- crear turbo.json
- crear tsconfig base
- crear carpetas apps/ y packages/ vacías o con stubs mínimos
- crear scripts lint/typecheck/test básicos
- crear .env.example sin secretos
- crear README técnico mínimo por app/package
- crear provider mock placeholder
- crear QA Harness skeleton sin lógica crítica
```

### 5.2 Prohibido ahora

```text
- implementar backend completo
- diseñar toda la base de datos final
- crear migraciones productivas
- integrar ZKTeco real
- integrar Hikvision real
- integrar Mercado Pago real
- implementar Edge productivo
- abrir puertas reales
- crear flujos de pago reales
- crear módulos premium
- crear pantallas finales completas
- crear apps móviles nativas
- cambiar stack
- copiar código legacy
- usar tokens reales
```

---

## 6. Gate de bootstrap técnico

### 6.1 Objetivo

Crear una base técnica limpia donde el proyecto pueda crecer sin deuda estructural.

### 6.2 Requisitos antes de ejecutar

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md = CERTIFIED
README.md = CERTIFIED
AI_AGENT_RULES_AND_HANDOFF.md = CERTIFIED
ROADMAP_V0.1.md = CERTIFIED
ADVANCED_IMPLEMENTATION_GUARDRAILS_V0.1.md = registrado en índice o aprobado explícitamente
```

### 6.3 Entregables permitidos

```text
package.json
pnpm-workspace.yaml
turbo.json
tsconfig.base.json
.gitignore
.env.example
apps/api/
apps/web/
apps/edge/
apps/qa/
packages/contracts/
packages/domain/
packages/db/
packages/provider-sdk/
packages/ui/
packages/config/
docker-compose.yml básico
CLAUDE.md
```

### 6.4 Prohibiciones durante bootstrap

```text
- no lógica de negocio compleja
- no Prisma schema final
- no migraciones reales
- no provider real
- no pago real
- no Edge productivo
- no UI final
- no credenciales reales
```

### 6.5 Criterio de terminado

```text
pnpm install pasa
pnpm lint existe y pasa o reporta TODO controlado
pnpm typecheck existe y pasa o reporta TODO controlado
pnpm test existe y pasa o reporta TODO controlado
estructura de repo coincide con REPO_STRUCTURE o se documenta desviación
no hay secretos
no hay lógica productiva prematura
```

---

## 7. Gate de contratos canónicos

### 7.1 Estado

```text
ALLOWED_AFTER_BOOTSTRAP
```

### 7.2 Permitido

```text
- crear tipos compartidos
- crear interfaces
- crear enums canónicos
- crear validadores básicos
- crear tests unitarios
- crear contratos provider-neutral
```

### 7.3 Prohibido

```text
- meter nombres internos de ZKTeco en dominio
- meter nombres internos de Hikvision en dominio
- meter IDs de Mercado Pago en dominio
- crear payloads reales de provider como modelos base
- acoplar UI al proveedor
```

### 7.4 Gate de salida

```text
contracts compilan
domain compila
tests unitarios básicos pasan
no hay contaminación de proveedor
```

---

## 8. Gate de dominio puro

### 8.1 Estado

```text
ALLOWED_AFTER_BOOTSTRAP
```

### 8.2 Permitido

Implementar reglas puras para:

```text
Condominium
PrivateArea / Privada
Property
ResponsiblePerson
Credential
Charge
Debt
PaymentOrder
PaymentAttempt
MorosityPolicy
AccessProfile
AccessZone
DesiredAccessState
ObservedAccessState
AccessCommand
AuditEvent
```

### 8.3 Prohibido

```text
- consultas SQL reales
- endpoints HTTP reales
- proveedor real
- pago real
- Edge real
- side effects
- IO externo
```

### 8.4 Gate de salida

```text
reglas puras cubiertas por tests
fixtures determinísticos
sin dependencias externas
sin provider terms en dominio
```

---

## 9. Gate de base de datos

### 9.1 Estado actual

```text
DESIGN_ONLY
```

### 9.2 Regla central

No se debe pedir a Claude:

```text
Implementa toda la base de datos.
```

Se debe pedir:

```text
Propón DATABASE_SCHEMA_PLAN_V0.1.md basado estrictamente en DOMAIN_MODEL y contratos.
No crear migraciones todavía.
No crear SQL productivo.
No inventar entidades.
```

### 9.3 Permitido ahora

```text
- identificar entidades persistentes
- proponer relaciones
- detectar índices necesarios
- marcar entidades auditables
- marcar tablas futuras
- proponer schema draft
```

### 9.4 Prohibido ahora

```text
- migraciones productivas
- seeds reales
- datos reales
- optimizaciones prematuras
- triggers complejos
- procedimientos almacenados
- queries manuales no revisadas
```

### 9.5 Desbloqueo de migración inicial

Requiere:

```text
DATABASE_SCHEMA_PLAN_V0.1.md revisado
Prisma schema propuesto
QA domain tests existentes
estrategia de seeds determinísticos
estrategia de auditoría
revisión humana
```

---

## 10. Gate de backend core

### 10.1 Estado actual

```text
BLOCKED_UNTIL_QA
```

### 10.2 Permitido después de bootstrap + dominio

```text
- estructura NestJS
- módulos vacíos
- controladores stub
- servicios stub
- DTOs basados en contracts
- OpenAPI inicial controlado
```

### 10.3 Prohibido

```text
- flujos críticos sin QA
- restaurar acceso sin Payment Gate
- ejecutar comandos reales contra Edge
- llamar providers reales
- guardar secretos en código
```

### 10.4 Gate de salida

```text
unit tests
integration tests básicos
QA Harness cubre flujo aplicable
OpenAPI no inventa capacidades fuera de docs
```

---

## 11. Gate de pagos

### 11.1 Mercado Pago mock

```text
Estado: ALLOWED_AFTER_BOOTSTRAP
```

Permitido:

```text
- PaymentProviderAdapter interface
- Payment mock
- webhook mock
- status normalization mock
- idempotency mock
- audit events mock
```

### 11.2 Mercado Pago sandbox

```text
Estado: BLOCKED_UNTIL_SANDBOX
```

Requiere:

```text
- mock tests PASS
- endpoint de webhook controlado
- validación de firma
- consulta server-side order/payment
- idempotencia
- externalReference
- no mezclar dinero SaaS con mantenimiento
- secretos en env/secret manager
- reporte QA
```

### 11.3 Mercado Pago productivo

```text
Estado: BLOCKED
```

Requiere:

```text
- sandbox PASS
- OAuth/cuenta conectada validada o decisión formal de credenciales por condominio
- manual fallback auditado
- reconciliation job
- soporte para pending/rejected/cancelled/refunded
- revisión legal/operativa de flujo de dinero
- aprobación humana
```

### 11.4 Regla crítica

```text
Webhook no confirma pago por sí solo.
Webhook despierta validación.
Tricor consulta order/payment por API antes de restaurar acceso.
```

---

## 12. Gate de Edge

### 12.1 Edge simulator

```text
Estado: ALLOWED_AFTER_BOOTSTRAP
```

Permitido:

```text
- heartbeat mock
- command queue mock
- ack/result mock
- offline/reconnect simulation
- idempotency tests
- observed state mock
```

### 12.2 Edge real

```text
Estado: BLOCKED_UNTIL_QA + BLOCKED_UNTIL_LAB
```

Requiere:

```text
- Edge simulator PASS
- command idempotency PASS
- retry/backoff PASS
- logs sin secretos
- provider mock PASS
- provider contract tests
- hardware-smoke plan
- revisión humana
```

### 12.3 Prohibido sin gate

```text
- abrir puertas reales
- modificar personas reales
- modificar tarjetas reales
- cambiar perfiles reales
- hacer polling agresivo contra provider real
- guardar tokens en disco sin protección
```

---

## 13. Gate de ZKTeco / CVSecurity

### 13.1 Estado actual

```text
Research/spec: CERTIFIED_FOR_RESEARCH
Implementación real: BLOCKED_UNTIL_LAB
```

### 13.2 Permitido antes de laboratorio

```text
- adapter interface
- mock adapter
- mapping canónico
- capability matrix
- provider contract tests mock
- payload builder en modo test
- fixtures no productivos
```

### 13.3 Prohibido antes de laboratorio

```text
- usar CVSecurity real en flujo automático
- remote open real
- delete person real
- delete card real
- cambiar accLevel real
- asumir PSG si no está validado
- asumir observed state si no fue probado
```

### 13.4 Desbloqueo mínimo de laboratorio

Requiere:

```text
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md ejecutado
resultado PASS documentado
test person/card/dept controlados
Pagador → Moroso → Pagador probado
remote open probado manualmente si se va a usar
QR probado si se va a usar
transacciones observadas si se usarán como observed state
logs sin secretos
rollback manual definido
```

---

## 14. Gate de Hikvision ISAPI PBAC

### 14.1 Estado actual

```text
Research: CERTIFIED_FOR_RESEARCH
Implementación real: BLOCKED_UNTIL_LAB
```

### 14.2 Permitido antes de laboratorio

```text
- adapter interface
- mock adapter
- mapping canónico
- capability matrix
- provider contract tests mock
- documentación de payloads basada en PDF autorizado
```

### 14.3 Prohibido antes de laboratorio

```text
- implementación contra dispositivo real
- RemoteControl/door real
- remoteCheck real
- UserInfo/CardInfo reales
- doorRight/RightPlan reales
- asumir QRCodeEvent como flujo productivo
- usar docs de metadata/video como base de access control
```

### 14.4 Desbloqueo mínimo

Requiere:

```text
hardware/dispositivo real
auth validada
UserInfo PASS
CardInfo PASS
doorRight/RightPlan PASS
RemoteControl/door PASS si se usará apertura remota
AcsEvent PASS si se usará observed events
QRCodeEvent PASS si se usará QR
rollback definido
logs sin secretos
```

---

## 15. Gate de ProviderCapabilityMatrix

### 15.1 Estado

```text
BLOCKED_UNTIL_QA para providers reales
```

### 15.2 Regla

Ninguna capacidad puede tratarse como soportada si está en:

```text
UNKNOWN
UNTESTED
DOCS_ONLY
```

### 15.3 Estados permitidos de capability

```text
SUPPORTED_DOCS_ONLY
SUPPORTED_LAB_CONFIRMED
SUPPORTED_MOCK_ONLY
UNSUPPORTED
UNKNOWN
```

### 15.4 Regla de ejecución

```text
Solo SUPPORTED_LAB_CONFIRMED permite ejecución contra provider real.
SUPPORTED_DOCS_ONLY solo permite diseño y mocks.
SUPPORTED_MOCK_ONLY solo permite QA local.
UNKNOWN bloquea ejecución.
```

---

## 16. Gate de módulos premium

### 16.1 Estado actual

```text
BACKLOG_ONLY
```

### 16.2 Módulos candidatos

```text
WhatsApp / Telegram para soporte, alertas o reportes
reportes avanzados
exportaciones contables
ticketing limitado
amenidades
integración de facturación externa
analytics avanzados
multicondominio premium
apps móviles nativas
```

### 16.3 Regla

Los módulos premium no deben entrar al MVP técnico ni bloquear el MVP core.

### 16.4 Permitido ahora

```text
- nombrarlos como backlog
- detectar dependencias
- marcar riesgos
- no diseñarlos en profundidad
```

### 16.5 Prohibido ahora

```text
- implementarlos
- crear tablas específicas
- crear pantallas finales
- venderlos como v1 core
- acoplarlos al flujo central
```

### 16.6 Desbloqueo

Requiere:

```text
MVP core estable
QA Harness operativo
flujo pagos-morosidad-acceso validado
piloto con feedback real
RFC de módulo premium
análisis comercial
```

---

## 17. Gate de cambios de stack

### 17.1 Estado

```text
BLOCKED_UNTIL_RFC
```

### 17.2 Stack aprobado

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

### 17.3 Cambios que requieren RFC

```text
cambiar NestJS
cambiar PostgreSQL
cambiar Prisma
cambiar Next.js
cambiar pnpm/Turborepo
introducir Temporal/N8N como core
introducir microservicios prematuros
usar mobile nativo en v1
cambiar provider prioritario
```

---

## 18. Gate de seguridad y secretos

### 18.1 Regla

No subir secretos al repo.

Prohibido:

```text
.env
.env.local
tokens reales
access tokens
client secrets
passwords
certificados privados
logs con secretos
capturas con tokens
payloads con credenciales
```

### 18.2 Permitido

```text
.env.example
secret refs
placeholders
documentación de variables
fixtures falsos
tokens ficticios marcados como fake
```

### 18.3 Gate de salida

```text
git diff revisado
secret scan si existe
logs revisados
.env no trackeado
```

---

## 19. Operaciones destructivas

### 19.1 Definición

Se consideran destructivas o sensibles:

```text
delete person
delete card
disable credential real
modify access level real
remote open real
reset database no efímera
delete payment record
mark payment paid sin provider confirmation
restore access real
```

### 19.2 Estado

```text
BLOCKED hasta QA + laboratorio + aprobación humana explícita
```

### 19.3 Regla

Toda operación destructiva requiere:

```text
entorno explícito
fixture o sujeto de prueba
dry-run si existe
confirmación humana
rollback documentado
audit event
log sin secretos
```

---

## 20. RFC obligatorio

Crear RFC antes de:

```text
cambiar stack
cambiar flujo de dinero
cambiar regla de morosidad default
cambiar jerarquía Condominio → Privada → Propiedad
meter vehículos como entidad principal
implementar módulo premium
implementar provider real nuevo
cambiar estrategia Cloud/Edge
cambiar modelo de seguridad/roles
crear migración compleja
```

Formato mínimo de RFC:

```text
Título
Motivo
Problema
Alternativas
Decisión propuesta
Impacto en dominio
Impacto en QA
Impacto en Edge
Impacto en providers
Impacto en pagos
Riesgos
Rollback
Estado
```

---

## 21. Prompts permitidos y prohibidos

### 21.1 Prompts permitidos ahora

```text
Crea el monorepo base sin lógica productiva.
Crea CLAUDE.md basado en AI_AGENT_RULES_AND_HANDOFF.md.
Crea BOOTSTRAP_REPO_TASK.md.
Crea packages vacíos con exports mínimos.
Crea scripts lint/typecheck/test.
Crea stubs de apps sin flujos reales.
Crea estructura de QA Harness skeleton.
Propón DATABASE_SCHEMA_PLAN_V0.1.md sin migraciones.
```

### 21.2 Prompts prohibidos ahora

```text
Implementa todo el backend.
Crea toda la base de datos.
Integra Mercado Pago.
Integra ZKTeco.
Integra Hikvision.
Haz Edge productivo.
Crea todos los módulos premium.
Haz la app completa.
Haz migraciones productivas.
Abre puertas desde el sistema.
Cobra pagos reales.
```

---

## 22. Ruta recomendada después de este documento

Orden seguro:

```text
1. Registrar este documento en DOCS_INDEX_AND_DEPENDENCY_MAP.md.
2. Referenciarlo en README.md.
3. Si se usará para Claude, agregarlo al orden de lectura en AI_AGENT_RULES_AND_HANDOFF.md.
4. Crear CLAUDE.md.
5. Crear BOOTSTRAP_REPO_TASK.md.
6. Ejecutar bootstrap técnico.
7. Crear contracts/domain.
8. Crear QA Harness skeleton.
9. Crear DATABASE_SCHEMA_PLAN_V0.1.md.
10. Solo después considerar schema Prisma inicial.
```

---

## 23. Criterio para pausar Tricor Hábitat

El proyecto puede pausarse de forma segura cuando existan:

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md actualizado
README.md actualizado
AI_AGENT_RULES_AND_HANDOFF.md actualizado
ROADMAP_V0.1.md actualizado
ADVANCED_IMPLEMENTATION_GUARDRAILS_V0.1.md agregado o listo para agregar
CLAUDE.md pendiente o generado
BOOTSTRAP_REPO_TASK.md pendiente o generado
```

Si se pausa antes de `CLAUDE.md`, la reanudación debe iniciar por:

```text
leer README
leer DOCS_INDEX
leer AI_AGENT_RULES
leer ADVANCED_IMPLEMENTATION_GUARDRAILS
generar CLAUDE.md
```

---

## 24. Estado final

```text
Documento: ADVANCED_IMPLEMENTATION_GUARDRAILS_V0.1.md
Estado: CERTIFIED como guardrails de implementación avanzada
Uso permitido: límites, gates, autorización de fases, control de agentes IA
Uso no permitido: sustituir Provider Strategy, QA Harness, Roadmap, Domain Model o specs de proveedores
```

Regla final:

```text
Bootstrap no es implementación productiva.
MVP técnico no es piloto real.
Research no autoriza producción.
Mock no autoriza hardware real.
Webhook no confirma pago por sí solo.
Provider docs no equivalen a lab PASS.
```

# Tricor Hábitat

**Estado del repositorio:** documentación y arquitectura v0.1  
**Estado de implementación:** no iniciar código productivo todavía  
**Objetivo:** servir como puerta de entrada humana para el handoff documental a Claude Code CLI
**Fuente operativa:** este repositorio, rama `main`

---

## 1. Propósito del repositorio

Este repositorio contiene la base documental de **Tricor Hábitat**, una plataforma SaaS + Edge para administración operativa de condominios, privadas y comunidades residenciales, enfocada en:

```text
pagos → morosidad → permisos de acceso → Edge → proveedor → auditoría
```

El objetivo de esta etapa es evitar iniciar desarrollo por parches, supuestos o integraciones improvisadas. Antes de escribir código productivo, el proyecto debe tener claros:

- dominio,
- configuración,
- arquitectura Cloud + Edge,
- proveedores,
- pagos,
- QA Harness,
- roadmap,
- reglas para agentes IA,
- y criterios de handoff.

---

## 2. Estado actual

```text
Fase actual: documentación / arquitectura controlada
Código productivo: no iniciado
Monorepo técnico: pendiente
QA Harness CLI: pendiente
Backend Cloud: pendiente
Edge Agent: pendiente
Provider adapters: pendientes
Handoff a Claude Code CLI: READY_FOR_BOOTSTRAP_REVIEW
Implementación productiva: BLOCKED hasta bootstrap técnico + QA Harness + labs
```

Este repositorio **no debe tratarse todavía como repo de implementación productiva**. El siguiente paso es la revisión humana del bootstrap técnico.

---

## 3. Regla principal

```text
Tricor conserva dominio canónico.
Los proveedores viven en adapters.
El QA Harness valida.
El laboratorio confirma.
Claude Code CLI no implementa por suposición.
```

No se permite implementar proveedor real, pagos reales, Edge productivo ni flujos críticos sin documentos fuente, contratos y QA definidos.

---

## 4. Orden oficial de lectura

Leer en este orden:

```text
1. DOCS_INDEX_AND_DEPENDENCY_MAP.md
2. AI_AGENT_RULES_AND_HANDOFF.md
3. PROJECT_CHARTER_TRICOR_HABITAT.md
4. DOMAIN_MODEL_V0.1.md
5. ACCESS_POLICY_CONFIGURATION_V0.1.md
6. CLOUD_EDGE_ARCHITECTURE_V0.1.md
7. PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
8. PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
9. PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
10. PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
11. PROVIDER_RESEARCH_HIKVISION_V0.1.md
12. QA_HARNESS_SPEC_V0.1.md
13. ROADMAP_V0.1.md
14. REPO_STRUCTURE_V0.1.md
15. USER_MANUAL_PLAN.md
```

Si existe contradicción entre documentos, detener implementación y reportar `CONSISTENCY ISSUE`.

---

## 5. Índice documental

| Documento | Estado | Función |
|---|---:|---|
| `DOCS_INDEX_AND_DEPENDENCY_MAP.md` | CERTIFIED | Fuente única del estado documental global. |
| `PROJECT_CHARTER_TRICOR_HABITAT.md` | CERTIFIED | Visión, alcance, no-goals y producto. |
| `DOMAIN_MODEL_V0.1.md` | CERTIFIED | Modelo de dominio y entidades principales. |
| `ACCESS_POLICY_CONFIGURATION_V0.1.md` | CERTIFIED | Policy Engine, presets, configuración y permisos. |
| `CLOUD_EDGE_ARCHITECTURE_V0.1.md` | CERTIFIED | Arquitectura Cloud + Edge. |
| `PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md` | CERTIFIED_FOR_RESEARCH | Investigación Mercado Pago. |
| `PROVIDER_RESEARCH_HIKVISION_V0.1.md` | CERTIFIED_FOR_RESEARCH | Investigación Hikvision ISAPI PBAC. |
| `PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md` | CERTIFIED_FOR_RESEARCH | Spec ZKTeco / CVSecurity. |
| `PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md` | REVIEW_REQUIRED | Plan de laboratorio ZKTeco, pendiente de ejecución. |
| `PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md` | CERTIFIED | Estrategia multivendor y contratos canónicos. |
| `QA_HARNESS_SPEC_V0.1.md` | CERTIFIED | Especificación del QA Harness. |
| `ROADMAP_V0.1.md` | CERTIFIED | Roadmap actualizado. |
| `AI_AGENT_RULES_AND_HANDOFF.md` | CERTIFIED | Reglas para Claude Code CLI y agentes IA. |
| `REPO_STRUCTURE_V0.1.md` | REVIEW_REQUIRED | Estructura objetivo del monorepo. |
| `USER_MANUAL_PLAN.md` | CERTIFIED | Plan de manuales de usuario. |

---

## 6. Handoff a Claude Code CLI

Estado del handoff documental:

```text
READY_FOR_BOOTSTRAP_REVIEW
```

El estado documental global se consulta en:

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md
```

Esto habilita revisión de bootstrap técnico. No habilita implementación productiva, provider real, Edge productivo ni pagos reales.

Para revisión de bootstrap, confirmar que estos documentos están presentes y actualizados:

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md
AI_AGENT_RULES_AND_HANDOFF.md
PROJECT_CHARTER_TRICOR_HABITAT.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
QA_HARNESS_SPEC_V0.1.md
ROADMAP_V0.1.md
REPO_STRUCTURE_V0.1.md
```

Claude Code CLI debe leer primero:

```text
AI_AGENT_RULES_AND_HANDOFF.md
```

Ningún agente debe implementar código crítico sin bootstrap técnico aprobado, QA Harness, provider contracts y reporte de trabajo.

---

## 7. Proveedores

### 7.1 Access providers

#### ZKTeco / ZKBio CVSecurity

```text
Estado documental: CERTIFIED_FOR_RESEARCH
Estado implementación: BLOCKED hasta laboratorio
Modo esperado: Edge local obligatorio
```

Base documental:

```text
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
```

No implementar provider real hasta ejecutar laboratorio y producir reporte PASS.

#### Hikvision ISAPI Person-Based Access Control

```text
Estado documental: CERTIFIED_FOR_RESEARCH
Estado implementación: BLOCKED hasta laboratorio con dispositivo/controlador real
Modo esperado: Edge local salvo prueba contraria
```

Base documental:

```text
PROVIDER_RESEARCH_HIKVISION_V0.1.md
```

No usar documentación de metadata/video como base de access control productivo.

### 7.2 Payment providers

#### Mercado Pago

```text
Estado documental: CERTIFIED_FOR_RESEARCH
Estado implementación: BLOCKED hasta sandbox/OAuth/webhooks reales
```

Base documental:

```text
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
```

Regla crítica:

```text
Webhook no confirma pago por sí solo.
Webhook despierta validación.
Tricor consulta order/payment por API antes de restaurar acceso.
```

---

## 8. QA Harness

El QA Harness será gate obligatorio.

Debe validar:

```text
dominio
configuración
pagos
webhooks
morosidad
restauración
credenciales
QR
Edge
provider contracts
capability matrix
auditoría
seguridad
```

Base documental:

```text
QA_HARNESS_SPEC_V0.1.md
```

No se acepta flujo crítico sin prueba reproducible.

---

## 9. Stack aprobado

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

No cambiar stack sin RFC técnico formal.

---

## 10. Estructura objetivo futura

La estructura objetivo está definida en:

```text
REPO_STRUCTURE_V0.1.md
```

Resumen:

```text
apps/
  api/
  web/
  edge/
  qa/
packages/
  contracts/
  domain/
  db/
  provider-sdk/
  ui/
  config/
docs/
infra/
scripts/
artifacts/
```

El estado actual puede seguir como repo documental en raíz hasta decidir migración a `/docs`.

---

## 11. Qué NO hacer ahora

No hacer todavía:

```text
- código productivo
- provider real
- pagos reales
- Edge productivo
- app móvil nativa
- facturación fiscal propia
- chat de residentes
- amenidades completas
- WhatsApp
- automatizaciones libres por cliente
- cambios de stack
- copiar código de proyectos legacy
```

---

## 12. Siguiente fase recomendada

Después de este cierre documental:

```text
1. Revisión humana del handoff documental.
2. Auditar repo final una vez más contra DOCS_INDEX_AND_DEPENDENCY_MAP.md.
3. Decidir si se mantiene docs en raíz o se migra a /docs.
4. Preparar bootstrap técnico en una tarea posterior aprobada.
5. Crear estructura técnica solo después de aprobación humana explícita.
```

No iniciar implementación productiva desde este README. El cierre documental no significa implementar providers reales ni pagos reales todavía.

---

## 13. Seguridad

No subir al repo:

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
```

Usar `.env.example` con placeholders.

Ejemplo:

```text
CVSECURITY_ACCESS_TOKEN=<LAB_ACCESS_TOKEN>
MERCADO_PAGO_ACCESS_TOKEN=<SANDBOX_ACCESS_TOKEN>
```

---

## 14. Estado final de este README

```text
Documento: README.md
Estado: CERTIFIED como puerta de entrada documental
Uso permitido: navegación humana, handoff documental, auditoría inicial
Uso no permitido: sustituir documentos técnicos fuente o el estado global de DOCS_INDEX_AND_DEPENDENCY_MAP.md
```

# DOCS_INDEX_AND_DEPENDENCY_MAP.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** DRAFT — índice maestro generado para revisión  
**Fecha:** 2026-06-11  
**Repositorio fuente:** `https://github.com/manzur316/Tricore-Habit`  
**Branch auditada:** `main`  
**Commit hash:** no proporcionado por el usuario al momento de generar este índice  
**Propósito:** mapa maestro de documentos, dependencias, estados y orden de actualización antes del handoff a Claude Code CLI.

---

## 1. Propósito

Este documento funciona como índice maestro y mapa de dependencias para la documentación de Tricor Hábitat.

Su objetivo es evitar que el proyecto avance con documentos fuera de orden, dependencias rotas, fuentes ambiguas o handoff prematuro a agentes de código.

Este archivo no reemplaza los documentos técnicos. Solo define:

- Qué documentos existen.
- Qué función cumple cada documento.
- Qué documentos dependen de otros.
- Qué documentos están certificados, en revisión o bloqueados.
- Qué documentos deben actualizarse antes del handoff.
- Qué documentos no deben tocarse por ahora.
- Qué orden de lectura debe seguir Claude Code CLI cuando el paquete esté listo.

---

## 2. Reglas de uso

### 2.1 Fuente única de verdad

La fuente operativa actual es el repositorio:

```text
https://github.com/manzur316/Tricore-Habit
branch: main
```

Los documentos del chat no deben superar al repositorio si existe contradicción. Si el chat y el repo difieren, debe tratarse como `CONSISTENCY ISSUE`.

### 2.2 Estados documentales permitidos

```text
CERTIFIED
CERTIFIED_FOR_RESEARCH
REVIEW_REQUIRED
BLOCKED
OBSOLETE
DRAFT
```

Definición:

| Estado | Significado |
|---|---|
| `CERTIFIED` | Documento consistente y usable como fuente base. |
| `CERTIFIED_FOR_RESEARCH` | Documento válido para investigación/diseño, pero no autoriza producción. |
| `REVIEW_REQUIRED` | Documento útil, pero requiere actualización o corrección. |
| `BLOCKED` | No debe usarse hasta resolver datos faltantes o ejecutar laboratorio. |
| `OBSOLETE` | Debe reemplazarse o eliminarse. |
| `DRAFT` | Borrador inicial, no certificado. |

### 2.3 Regla de handoff

No entregar a Claude Code CLI hasta que, como mínimo, estén actualizados:

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
QA_HARNESS_SPEC_V0.1.md
ROADMAP_V0.1.md
AI_AGENT_RULES_AND_HANDOFF.md
```

---

## 3. Inventario actual de documentos

| Documento | Estado actual | Función | Dependencias base | Dependientes | Acción recomendada |
|---|---|---|---|---|---|
| `README.md` | REVIEW_REQUIRED | Entrada del repo | Este índice | Todos | Actualizar después del índice y roadmap. |
| `PROJECT_CHARTER_TRICOR_HABITAT.md` | CERTIFIED | Documento rector del producto | Ninguna | Todos | No tocar por ahora. |
| `DOMAIN_MODEL_V0.1.md` | CERTIFIED | Modelo de dominio | Project Charter | Policy, Cloud/Edge, Providers, QA | No tocar por ahora. |
| `ACCESS_POLICY_CONFIGURATION_V0.1.md` | CERTIFIED | Policy Engine, presets, configuración | Charter, Domain | QA, UI, Backend, Providers | No tocar por ahora. |
| `CLOUD_EDGE_ARCHITECTURE_V0.1.md` | CERTIFIED | Cloud + Edge | Charter, Domain, Policy | Provider Strategy, QA, Roadmap | No tocar por ahora. |
| `PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md` | CERTIFIED_FOR_RESEARCH | Mercado Pago research | Charter, Domain, Policy, Cloud/Edge | QA, Roadmap, AI Handoff | No tocar por ahora. |
| `PROVIDER_RESEARCH_HIKVISION_V0.1.md` | CERTIFIED_FOR_RESEARCH | Hikvision ISAPI PBAC research | Domain, Cloud/Edge, Access Policy | Provider Strategy, QA | No tocar por ahora. |
| `PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md` | CERTIFIED_FOR_RESEARCH | ZKTeco/CVSecurity adapter spec | Domain, Cloud/Edge, Access Policy | ZKTeco Lab, Provider Strategy, QA | No tocar por ahora. |
| `PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md` | REVIEW_REQUIRED | Laboratorio ZKTeco/CVSecurity | ZKTeco Adapter Spec | QA, Provider Strategy | No tocar hasta ejecutar laboratorio. |
| `PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md` | REVIEW_REQUIRED | Estrategia multivendor + contratos | Domain, Cloud/Edge, provider research | QA, Roadmap, AI Handoff | Actualizar primero después de este índice. |
| `QA_HARNESS_SPEC_V0.1.md` | REVIEW_REQUIRED | Especificación del QA Harness | Charter, Domain, Policy, Cloud/Edge, Provider Strategy, Payment Research | Roadmap, AI Handoff | Actualizar después de Provider Strategy. |
| `ROADMAP_V0.1.md` | REVIEW_REQUIRED | Roadmap de ejecución | Charter, Domain, Policy, Cloud/Edge, QA | AI Handoff | Actualizar después de QA. |
| `AI_AGENT_RULES_AND_HANDOFF.md` | REVIEW_REQUIRED | Reglas para Claude Code CLI | Roadmap, QA, Provider Strategy | Claude Code CLI | Actualizar después de Roadmap. |
| `REPO_STRUCTURE_V0.1.md` | REVIEW_REQUIRED | Estructura objetivo del repo | Charter, Roadmap, AI Handoff | Bootstrap repo | Revisar cuando se decida estructura `/docs`. |
| `USER_MANUAL_PLAN.md` | CERTIFIED | Plan de documentación de usuario | Charter, Roadmap | Manuales futuros | No tocar por ahora. |

---

## 4. Mapa de dependencias

### 4.1 Núcleo documental

```text
PROJECT_CHARTER_TRICOR_HABITAT.md
    ↓
DOMAIN_MODEL_V0.1.md
    ↓
ACCESS_POLICY_CONFIGURATION_V0.1.md
    ↓
CLOUD_EDGE_ARCHITECTURE_V0.1.md
```

Estos documentos están certificados y no deben tocarse por ahora.

### 4.2 Pagos

```text
PROJECT_CHARTER_TRICOR_HABITAT.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
    ↓
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
    ↓
QA_HARNESS_SPEC_V0.1.md
ROADMAP_V0.1.md
AI_AGENT_RULES_AND_HANDOFF.md
```

Estado actual:

```text
Mercado Pago research: CERTIFIED_FOR_RESEARCH
QA/Roadmap/Handoff: REVIEW_REQUIRED
```

### 4.3 ZKTeco / CVSecurity

```text
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
    ↓
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
    ↓
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
    ↓
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
    ↓
QA_HARNESS_SPEC_V0.1.md
```

Estado actual:

```text
Adapter spec: CERTIFIED_FOR_RESEARCH
Lab test plan: REVIEW_REQUIRED hasta ejecución real
Provider Strategy: REVIEW_REQUIRED
QA Harness: REVIEW_REQUIRED
```

### 4.4 Hikvision

```text
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
    ↓
PROVIDER_RESEARCH_HIKVISION_V0.1.md
    ↓
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
    ↓
QA_HARNESS_SPEC_V0.1.md
```

Estado actual:

```text
Hikvision research: CERTIFIED_FOR_RESEARCH
Provider Strategy: REVIEW_REQUIRED
QA Harness: REVIEW_REQUIRED
```

### 4.5 Handoff

```text
DOCS_INDEX_AND_DEPENDENCY_MAP.md
    ↓
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
    ↓
QA_HARNESS_SPEC_V0.1.md
    ↓
ROADMAP_V0.1.md
    ↓
AI_AGENT_RULES_AND_HANDOFF.md
    ↓
Claude Code CLI
```

---

## 5. Orden oficial de actualización

Antes de entregar a Claude Code CLI, actualizar en este orden estricto:

```text
1. DOCS_INDEX_AND_DEPENDENCY_MAP.md
2. PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
3. QA_HARNESS_SPEC_V0.1.md
4. ROADMAP_V0.1.md
5. AI_AGENT_RULES_AND_HANDOFF.md
6. README.md
```

`REPO_STRUCTURE_V0.1.md` se actualiza solo si se decide mover documentos a `/docs/...` o formalizar la estructura docs-only temporal.

---

## 6. Documentos que no deben tocarse ahora

```text
PROJECT_CHARTER_TRICOR_HABITAT.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
PROVIDER_RESEARCH_HIKVISION_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
USER_MANUAL_PLAN.md
```

`PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md` tampoco debe tocarse hasta ejecutar laboratorio real o recibir datos nuevos.

---

## 7. Bloqueos actuales

### 7.1 Handoff a Claude

```text
Estado: BLOCKED
Motivo: documentos dependientes todavía están en REVIEW_REQUIRED.
```

Bloqueadores mínimos:

```text
- Provider Strategy no absorbe completamente provider docs certificados.
- QA Harness no absorbe endpoints reales y flujo de pagos actualizado.
- Roadmap no refleja estado documental actual.
- AI Handoff no enumera toda la fuente de verdad actual.
- README no guía el uso del repo.
```

### 7.2 Implementación de providers

```text
ZKTeco/CVSecurity: BLOCKED hasta laboratorio.
Hikvision: BLOCKED hasta laboratorio con dispositivo/controlador real.
Mercado Pago: BLOCKED hasta sandbox/OAuth/webhooks validados.
```

---

## 8. CONSISTENCY ISSUE registrados

### CI-001 — Provider Strategy desactualizado frente a research certificado

`PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md` debe actualizarse para consumir:

```text
PROVIDER_RESEARCH_HIKVISION_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
```

### CI-002 — QA Harness no refleja endpoints reales de providers

`QA_HARNESS_SPEC_V0.1.md` debe incorporar suites específicas para:

```text
- ZKTeco/CVSecurity API 2025
- Hikvision ISAPI Person-Based Access Control
- Mercado Pago Orders/Webhooks/Get Payment/Get Order
```

### CI-003 — Roadmap no refleja estado documental actual

`ROADMAP_V0.1.md` debe actualizar fases de providers, pagos, QA y handoff.

### CI-004 — AI Handoff no lista documentos nuevos críticos

`AI_AGENT_RULES_AND_HANDOFF.md` debe incluir como fuentes:

```text
PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
PROVIDER_RESEARCH_HIKVISION_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
REPO_STRUCTURE_V0.1.md
USER_MANUAL_PLAN.md
DOCS_INDEX_AND_DEPENDENCY_MAP.md
```

### CI-005 — README vacío

`README.md` debe convertirse en puerta de entrada del repositorio documental.

### CI-006 — Estructura objetivo vs estructura actual

`REPO_STRUCTURE_V0.1.md` propone `/docs/...`, pero el repo actual mantiene archivos en raíz. Esto no bloquea la auditoría documental, pero debe decidirse antes del bootstrap de código.

---

## 9. Orden oficial de lectura para humanos

Mientras no se actualice AI Handoff, el orden recomendado para revisión humana es:

```text
1. PROJECT_CHARTER_TRICOR_HABITAT.md
2. DOMAIN_MODEL_V0.1.md
3. ACCESS_POLICY_CONFIGURATION_V0.1.md
4. CLOUD_EDGE_ARCHITECTURE_V0.1.md
5. PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
6. PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
7. PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
8. PROVIDER_RESEARCH_HIKVISION_V0.1.md
9. PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
10. QA_HARNESS_SPEC_V0.1.md
11. ROADMAP_V0.1.md
12. AI_AGENT_RULES_AND_HANDOFF.md
13. REPO_STRUCTURE_V0.1.md
14. USER_MANUAL_PLAN.md
```

---

## 10. Orden futuro de lectura para Claude Code CLI

Este orden será válido solo después de actualizar los documentos en `REVIEW_REQUIRED`:

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

---

## 11. Reglas para siguientes actualizaciones

Cada siguiente actualización debe hacerse de un solo archivo.

Formato obligatorio:

```text
MODO: GENERAR / ACTUALIZAR / AUDITAR
ARCHIVO OBJETIVO: <nombre exacto>
FUENTES OBLIGATORIAS: <documentos del repo>
ARCHIVOS QUE NO DEBEN TOCARSE: todos los demás
RESULTADO ESPERADO: <objetivo>
```

No actualizar documentos dependientes por inferencia.

---

## 12. Estado final de este índice

```text
Documento: DOCS_INDEX_AND_DEPENDENCY_MAP.md
Estado: DRAFT
Uso permitido: guía de auditoría y orden de actualización
Uso no permitido: handoff final a Claude hasta actualizar dependientes
```

Siguiente archivo recomendado:

```text
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
```

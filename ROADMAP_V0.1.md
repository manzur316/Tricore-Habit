# ROADMAP_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** Draft formal inicial / fuente de verdad provisional de roadmap  
**Fecha:** 2026-06-10  
**Documentos padre:** `PROJECT_CHARTER_TRICOR_HABITAT.md`, `DOMAIN_MODEL_V0.1.md`, `ACCESS_POLICY_CONFIGURATION_V0.1.md`, `CLOUD_EDGE_ARCHITECTURE_V0.1.md`, `QA_HARNESS_SPEC_V0.1.md`  
**Preparado para:** Arquitectura, planeación de desarrollo, ejecución por CLI, control de alcance, documentación histórica, QA, handoff posterior a Claude Code CLI y preparación de piloto comercial  
**Idioma base:** Español  

---

## 1. Propósito del documento

Este documento define el roadmap inicial de Tricor Hábitat.

Su objetivo es convertir la visión, dominio, políticas, arquitectura Cloud + Edge y QA Harness en una secuencia clara de desarrollo. El roadmap debe impedir que el proyecto avance por parches, features improvisadas o decisiones aisladas.

Este documento debe servir como fuente de verdad para:

- Orden de implementación.
- Control de alcance.
- Priorización de módulos.
- Separación entre MVP técnico, MVP piloto y MVP comercial.
- Reglas de avance entre fases.
- Criterios de terminado.
- Trabajo posterior con Claude Code CLI.
- Documentación histórica del proyecto.
- Planeación de manuales de usuario.
- Preparación de venta y piloto.

Este documento no es un calendario definitivo. Es un mapa de ejecución por fases. Las fechas deben agregarse después, cuando el alcance técnico y recursos disponibles estén confirmados.

---

## 2. Principio rector del roadmap

Tricor Hábitat debe desarrollarse en este orden:

```text
Documentos fuente de verdad
→ Contratos canónicos
→ QA Harness
→ Backend core
→ Policy Engine
→ Pagos
→ Accesos
→ Edge
→ Provider Adapter
→ Interfaces web/PWA
→ Staging
→ Piloto
→ Comercialización
```

No se debe iniciar por pantallas visuales ni por integración directa con proveedor externo sin dominio, contratos y QA Harness.

El producto debe construirse alrededor del flujo comercial central:

```text
Propiedad
→ deuda / pago
→ morosidad
→ perfil de acceso
→ desired state
→ comando Edge
→ proveedor
→ observed state
→ auditoría
```

---

## 3. Estado objetivo de la versión 1

La versión 1 debe entregar una plataforma funcional para controlar accesos residenciales según pagos y morosidad.

### 3.1 Objetivo funcional v1

Tricor Hábitat v1 debe permitir:

- Crear y administrar clientes tipo condominio, privada o comunidad residencial.
- Mantener estructura interna fija `Condominio → Privada → Propiedad`.
- Registrar propietarios/responsables principales.
- Asignar límites de credenciales por propiedad.
- Generar cargos de mantenimiento, multas y cargos extraordinarios.
- Procesar pagos en línea mediante proveedor de pagos.
- Permitir transferencia manual solo como fallback auditado.
- Calcular morosidad por propiedad.
- Aplicar políticas configurables de acceso.
- Bloquear por default acceso vehicular, QR invitados y zonas no esenciales para propiedades morosas.
- Conservar acceso peatonal por default, si el condominio lo configura así.
- Restaurar automáticamente acceso cuando el pago sea confirmado por proveedor.
- Ejecutar cambios de acceso mediante Edge cuando el proveedor lo requiera.
- Auditar pagos, cambios de permisos, comandos, aperturas manuales, excepciones temporales y credenciales.
- Operar una interfaz mínima para Platform, Condo, Guard y Resident.

### 3.2 Objetivo técnico v1

La v1 debe quedar preparada para:

- Desarrollo por CLI.
- QA reproducible.
- Configuración controlada.
- Validación de provider capabilities.
- Edge local obligatorio para proveedor inicial.
- Futuro proveedor adicional sin reescribir el dominio.
- Futuras apps móviles, especialmente Flutter, sin rehacer backend ni contratos.
- Documentación de usuario final.

### 3.3 Objetivo comercial v1

La v1 debe poder venderse como piloto cobrable cuando exista:

- Backend estable.
- Tricor Platform mínimo.
- Tricor Condo funcional.
- Tricor Guard mínimo móvil/PWA.
- Tricor Resident mínimo móvil/PWA.
- Pago en línea funcional en ambiente real controlado.
- Edge funcional con proveedor inicial.
- QA Harness como gate.
- Auditoría básica.
- Manual inicial de operación.

---

## 4. Fuera de alcance de v1

No deben entrar en v1:

- Facturación fiscal propia.
- Contabilidad completa.
- Chat entre residentes.
- Red social.
- Amenidades como módulo completo.
- WhatsApp.
- Aplicaciones móviles nativas completas.
- Automatizaciones libres por cliente.
- Múltiples proveedores implementados al mismo tiempo sin contratos canónicos validados.
- Motor financiero donde Tricor administre dinero de mantenimiento.
- Desarrollo personalizado por cliente.

Pueden quedar como investigación futura:

- Integración de facturación externa.
- Telegram para soporte, alertas y reportes.
- Hikvision / Hik-Connect / ISAPI.
- Flutter para apps móviles futuras.
- Exportaciones contables.
- Ticketing limitado.
- Automatizaciones internas no críticas.

---

## 5. Niveles de entrega

El roadmap distingue tres niveles.

### 5.1 MVP técnico

El MVP técnico prueba que el sistema funciona internamente.

Debe incluir:

- Repo base.
- Contratos canónicos.
- Base de datos.
- Backend core.
- QA Harness.
- Provider mock.
- Edge simulator.
- Payment mock.
- Policy Engine.
- Flujos críticos automatizados.

No requiere UI completa.

### 5.2 MVP piloto

El MVP piloto permite operar en un condominio real controlado.

Debe incluir:

- Tricor Platform mínimo.
- Tricor Condo funcional.
- Tricor Guard mínimo móvil/PWA.
- Tricor Resident mínimo móvil/PWA.
- Pago en línea real o sandbox listo para transición.
- Transferencia manual fallback.
- Edge real.
- Proveedor inicial conectado.
- Auditoría y reportes operativos.

### 5.3 MVP comercial

El MVP comercial permite cobrar con menor fricción.

Debe incluir:

- Onboarding operativo.
- Documentación de usuario.
- Manuales mínimos.
- Soporte interno.
- Monitoreo.
- Métricas de uso.
- Planes y cobro SaaS desde Tricor Platform.
- Procedimientos de instalación y soporte.

---

## 6. Stack aprobado

El roadmap asume el siguiente stack base:

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

## 7. Estructura de repositorio objetivo

Estructura recomendada:

```text
tricor-habitat/
├── apps/
│   ├── api/              # Backend Cloud NestJS
│   ├── web/              # Next.js: Platform, Condo, Guard, Resident
│   ├── edge/             # Edge Agent local
│   └── qa/               # QA Harness CLI
├── packages/
│   ├── contracts/        # Contratos canónicos
│   ├── domain/           # Reglas puras y Policy Engine
│   ├── db/               # Prisma schema/client
│   ├── provider-sdk/     # Interfaces de provider adapters
│   ├── ui/               # Componentes compartidos
│   └── config/           # tsconfig, eslint, env schema
├── docs/
│   ├── PROJECT_CHARTER_TRICOR_HABITAT.md
│   ├── DOMAIN_MODEL_V0.1.md
│   ├── ACCESS_POLICY_CONFIGURATION_V0.1.md
│   ├── CLOUD_EDGE_ARCHITECTURE_V0.1.md
│   ├── QA_HARNESS_SPEC_V0.1.md
│   └── ROADMAP_V0.1.md
└── docker-compose.yml
```

---

## 8. Fases del roadmap

### Fase 0 — Cierre documental inicial

**Objetivo:** dejar una base documental suficiente para no iniciar implementación a ciegas.

**Entregables:**

- `PROJECT_CHARTER_TRICOR_HABITAT.md`
- `DOMAIN_MODEL_V0.1.md`
- `ACCESS_POLICY_CONFIGURATION_V0.1.md`
- `CLOUD_EDGE_ARCHITECTURE_V0.1.md`
- `QA_HARNESS_SPEC_V0.1.md`
- `ROADMAP_V0.1.md`
- `AI_AGENT_RULES_AND_HANDOFF.md`, pendiente.
- `USER_MANUAL_PLAN.md`, pendiente.

**Criterio de terminado:**

- Documentos coherentes entre sí.
- Sin referencias a tecnologías vetadas.
- Roadmap preparado para handoff.
- Puntos abiertos identificados como research, no como bloqueos.

**Prohibido:**

- Iniciar código productivo sin tener este set documental.
- Pasar un repo viejo como fuente de verdad.
- Implementar proveedor real antes de contratos y QA.

---

### Fase 1 — Bootstrap del repo y reglas de agente

**Objetivo:** crear el repo limpio y preparar el entorno para desarrollo por CLI.

**Entregables:**

- Monorepo pnpm + Turborepo.
- Estructura `apps/` y `packages/`.
- Configuración TypeScript.
- Configuración lint/test/format.
- Docker Compose inicial.
- `.env.example` por app.
- `CLAUDE.md`.
- `.claude/settings.json` si aplica.
- `AI_AGENT_RULES_AND_HANDOFF.md`.

**QA mínimo:**

```text
pnpm install
pnpm lint
pnpm typecheck
pnpm test
```

**Criterio de terminado:**

- Repo inicial compila.
- No hay lógica de negocio todavía.
- Claude Code CLI tiene reglas claras.
- Documentos fuente de verdad están dentro de `/docs`.

**Prohibido:**

- Crear pantallas finales.
- Implementar proveedor real.
- Mezclar contexto de otros proyectos.
- Usar código legacy como base principal.

---

### Fase 2 — Contratos canónicos y dominio base

**Objetivo:** implementar las entidades, estados y contratos canónicos sin depender de proveedor externo.

**Entregables:**

- `packages/contracts`.
- `packages/domain`.
- Tipos canónicos:
  - `Condominium`
  - `PrivateCommunity`
  - `Property`
  - `Owner`
  - `Credential`
  - `AccessProfile`
  - `AccessZone`
  - `PaymentAccount`
  - `Charge`
  - `Debt`
  - `PaymentAttempt`
  - `ProofOfPayment`
  - `MorosityPolicy`
  - `DesiredAccessState`
  - `ObservedAccessState`
  - `AccessCommand`
  - `AuditEvent`
- Validadores base.
- Eventos de dominio.

**QA requerido:**

```text
pnpm qa:domain
pnpm qa:contracts
```

**Criterio de terminado:**

- El dominio puede calcular estados sin proveedor.
- Las reglas principales tienen pruebas unitarias.
- No hay conceptos propios del proveedor dentro del dominio.

**Prohibido:**

- Programar payloads del proveedor dentro del dominio.
- Crear reglas personalizadas por cliente.
- Omitir auditoría en eventos críticos.

---

### Fase 3 — Base de datos y migraciones iniciales

**Objetivo:** crear el modelo persistente compatible con el dominio.

**Entregables:**

- `packages/db`.
- Prisma schema inicial.
- Migraciones.
- Seeds determinísticos.
- Índices básicos.
- Entidades de auditoría.
- Entidades de configuración.
- Entidades de provider mappings.

**Tablas o modelos mínimos:**

- Condominiums.
- PrivateCommunities.
- Properties.
- Owners.
- PropertyOwnerships.
- Credentials.
- CredentialEvents.
- Charges.
- Debts.
- PaymentAttempts.
- ProofsOfPayment.
- PaymentProviderAccounts.
- MorosityPolicies.
- AccessProfiles.
- AccessZones.
- DesiredAccessStates.
- ObservedAccessStates.
- AccessCommands.
- EdgeInstallations.
- ProviderInstallations.
- ProviderMappings.
- AuditEvents.
- OperationalIncidents.

**QA requerido:**

```text
pnpm db:migrate
pnpm db:seed
pnpm qa:db
```

**Criterio de terminado:**

- La base puede crear un condominio, privada, propiedad y propietario.
- La base puede persistir credenciales, pagos, morosidad y comandos.
- Seeds reproducibles.

**Prohibido:**

- Guardar archivos binarios de comprobantes dentro de PostgreSQL.
- Crear tablas de vehículos en v1.
- Modelar múltiples propietarios por propiedad.

---

### Fase 4 — QA Harness foundation

**Objetivo:** construir el QA Harness antes de los módulos críticos.

**Entregables:**

- `apps/qa`.
- CLI inicial.
- Reportes `.md` y `.json`.
- Fixtures determinísticos.
- Provider mock.
- Edge simulator básico.
- Payment mock.
- Storage mock.
- Primeros comandos:

```text
pnpm qa:health
pnpm qa:domain
pnpm qa:config
pnpm qa:flow property-debt-access-lifecycle
pnpm qa:report
```

**Criterio de terminado:**

- El QA Harness puede ejecutarse localmente.
- Genera reportes entendibles.
- Puede fallar con mensajes accionables.
- Debe quedar listo antes de implementar pagos reales, Edge real o provider adapter real.

**Prohibido:**

- Aceptar cambios sin reporte QA.
- Usar solo pruebas manuales.
- Crear fixes sin reproducir el fallo.

---

### Fase 5 — Backend Cloud core

**Objetivo:** implementar la API central sin integración real externa todavía.

**Entregables:**

- `apps/api` NestJS.
- Auth base.
- Roles y scopes.
- Multi-tenant por condominio.
- Módulos:
  - Condominiums.
  - PrivateCommunities.
  - Properties.
  - Owners.
  - Credentials.
  - Charges.
  - Debts.
  - Payments mock.
  - AccessProfiles.
  - AccessCommands.
  - AuditEvents.
- OpenAPI/Swagger o contratos documentados.

**QA requerido:**

```text
pnpm qa:api
pnpm qa:security
pnpm qa:flow property-debt-access-lifecycle
```

**Criterio de terminado:**

- API puede ejecutar el flujo técnico con mocks.
- Roles bloquean accesos indebidos.
- Auditoría registra eventos críticos.

**Prohibido:**

- Crear endpoints sin pruebas.
- Permitir acceso cross-tenant.
- Permitir que Finance edite permisos directamente.

---

### Fase 6 — Policy Engine y configuración controlada

**Objetivo:** implementar presets, configuración avanzada, validaciones, dependencias e incompatibilidades.

**Entregables:**

- `PolicyEngine`.
- `ConfigRegistry`.
- `PolicyConfigValidator`.
- Presets:
  - Suave.
  - Estándar.
  - Estricto.
  - Personalizado.
- Configuración de:
  - Morosidad.
  - Pagos.
  - Accesos.
  - QR.
  - Credenciales.
  - Guardias.
  - Roles.
  - Edge.
  - Proveedor.
- Matriz de impacto.
- Validación contra `ProviderCapabilityMatrix`.

**QA requerido:**

```text
pnpm qa:config:valid
pnpm qa:config:invalid
pnpm qa:config:dependencies
pnpm qa:config:provider-capabilities
```

**Criterio de terminado:**

- No se puede guardar configuración inválida.
- Las dependencias se resuelven correctamente.
- El sistema sabe explicar por qué una configuración no aplica.
- Los defaults de morosidad están implementados.

**Prohibido:**

- Crear 200 flags sin jerarquía.
- Exponer modo técnico a usuarios normales.
- Asumir que todos los proveedores soportan todas las capacidades.

---

### Fase 7 — Pagos internos, cargos, deudas y fallback manual

**Objetivo:** implementar el sistema financiero operativo sin pasarela real todavía.

**Entregables:**

- Cargos de mantenimiento.
- Multas.
- Cargos extraordinarios.
- Deudas por propiedad.
- Estados de cuenta.
- PaymentAttempt.
- ProofOfPayment.
- Storage S3-compatible.
- Transferencia manual fallback.
- Aprobación manual por Finance o CondoAdmin.
- Restauración manual auditada por pago manual aprobado.

**QA requerido:**

```text
pnpm qa:payments
pnpm qa:flow manual-payment-fallback-lifecycle
pnpm qa:storage
pnpm qa:audit
```

**Criterio de terminado:**

- El sistema puede generar deuda por propiedad.
- Finance puede aprobar pago manual.
- El comprobante queda como evidencia.
- La restauración sucede por flujo auditado, no por edición directa de permisos.

**Prohibido:**

- Hacer que una imagen de comprobante restaure acceso por sí sola.
- Mezclar dinero de mantenimiento con ingresos SaaS de Tricor.
- Permitir aprobación manual sin auditoría.

---

### Fase 8 — Research e integración Mercado Pago v1

**Objetivo:** validar e implementar el primer proveedor de pagos con API.

**Entregables de research:**

- `PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md`.
- Modelo de cuenta conectada por condominio.
- Webhooks.
- Validación de firma/eventos.
- Estados de pago.
- SPEI / pagos pendientes.
- Comisiones.
- Reembolsos.
- Errores.
- Ambientes sandbox/producción.

**Entregables técnicos:**

- PaymentProvider interface.
- Mercado Pago adapter.
- Webhook controller.
- Payment verification service.
- Payment reconciliation status.
- Payment account connection flow.
- QA mock compatible.

**QA requerido:**

```text
pnpm qa:payments:provider
pnpm qa:payments:mercado-pago --mock
pnpm qa:flow payment-confirmed-restore-access
pnpm qa:flow payment-pending-no-restore
```

**Criterio de terminado:**

- Un pago confirmado restaura acceso automáticamente.
- Un pago pendiente no restaura acceso.
- Un pago rechazado no modifica permisos.
- El dinero se modela como dinero del condominio, no de Tricor Platform.

**Prohibido:**

- Convertir a Tricor en intermediario financiero del mantenimiento.
- Restaurar acceso sin confirmar el pago contra proveedor.
- Ignorar webhooks duplicados.
- Implementar pagos reales sin QA mock.

---

### Fase 9 — Accesos, QR, credenciales y ciclo de vida

**Objetivo:** implementar la lógica de acceso sin proveedor real todavía.

**Entregables:**

- AccessProfile.
- AccessZone.
- DesiredAccessState.
- ObservedAccessState.
- AccessCommand.
- QR residente dinámico.
- QR invitado.
- Bloqueo de QR por morosidad.
- Límite de credenciales por propiedad.
- Reporte de tarjeta/tag perdido por Resident.
- Revocación de credencial perdida.
- MoveOutWorkflow.
- Exresidente y revocación de credenciales.
- Alertas no invasivas por credenciales sin uso.

**QA requerido:**

```text
pnpm qa:access
pnpm qa:qr
pnpm qa:credentials
pnpm qa:flow lost-credential
pnpm qa:flow resident-move-out-credential-revocation
```

**Criterio de terminado:**

- Propiedad al corriente obtiene perfil activo permitido.
- Propiedad morosa obtiene perfil moroso default.
- Exresidente pierde acceso.
- Tarjeta perdida queda bloqueada.
- QR invitado no funciona si la política lo bloquea.

**Prohibido:**

- Crear módulo de vehículos v1.
- Hacer QR estático permanente.
- Eliminar historial de credenciales.
- Registrar nuevas credenciales desde Resident.

---

### Fase 10 — Edge simulator y command lifecycle completo

**Objetivo:** cerrar el ciclo Cloud → Edge → provider mock → observed state.

**Entregables:**

- Edge simulator más completo.
- AccessCommand queue.
- Estados de comando:
  - PENDING.
  - DISPATCHED.
  - APPLIED.
  - FAILED.
  - RETRYING.
  - CANCELLED.
- Idempotency keys.
- Offline simulation.
- Reintentos.
- Observed state.
- Drift detection básico.
- Incident generation.

**QA requerido:**

```text
pnpm qa:edge
pnpm qa:flow payment-confirmed-edge-offline
pnpm qa:flow edge-reconnect-command-drain
pnpm qa:provider-contracts
```

**Criterio de terminado:**

- Si Edge está offline, el pago puede quedar confirmado pero acceso pendiente.
- Al reconectar, Edge procesa comandos pendientes.
- El sistema no miente diciendo que el proveedor ya aplicó algo si no hay confirmación.

**Prohibido:**

- Ejecutar comandos sin idempotencia.
- Marcar acceso como aplicado sin observed state o confirmación equivalente.
- Permitir que Edge decida morosidad.

---

### Fase 11 — Edge real v1

**Objetivo:** construir el agente local real para el proveedor inicial.

**Entregables:**

- `apps/edge`.
- Enrollment.
- Heartbeats.
- Config snapshot.
- Command polling o canal saliente seguro.
- Cola local.
- Logs técnicos.
- UI local técnica mínima.
- Prueba de conexión local.
- Modo offline con snapshot.
- Reporte de estado al Cloud.

**QA requerido:**

```text
pnpm qa:edge:local
pnpm qa:edge:offline
pnpm qa:edge:ui
pnpm qa:flow cloud-edge-command-lifecycle
```

**Criterio de terminado:**

- Edge puede instalarse por condominio/caseta.
- Edge reporta estado.
- Edge puede operar con snapshot.
- UI local no permite administrar residentes ni pagos.

**Prohibido:**

- Abrir proveedor local directamente a internet.
- Usar Edge como fuente de verdad.
- Meter lógica financiera dentro del Edge.

---

### Fase 12 — Provider adapter inicial

**Objetivo:** implementar el primer adapter real de control de acceso usando contratos canónicos.

**Entregables:**

- Provider adapter interface final.
- ProviderCapabilityMatrix inicial.
- Provider mappings:
  - puertas.
  - zonas.
  - perfiles.
  - credenciales.
- Conexión real controlada.
- Smoke tests con hardware/software de prueba.
- Reconciliación básica.
- Drift detection.

**QA requerido:**

```text
pnpm qa:provider-contract zkteco --mock
pnpm qa:provider-contract zkteco --smoke
pnpm qa:flow property-debt-access-lifecycle --hardware-smoke
```

**Criterio de terminado:**

- El adapter traduce desired state canónico a operaciones del proveedor.
- La capability matrix controla lo que se puede configurar.
- No se rompe el dominio por limitaciones del proveedor.

**Prohibido:**

- Programar lógica específica del proveedor dentro de módulos de dominio.
- Activar configuración no soportada por proveedor.
- Ejecutar pruebas destructivas en entorno productivo.

---

### Fase 13 — Tricor Platform mínimo

**Objetivo:** crear el panel interno SaaS para administrar clientes, planes, licencias y salud operativa.

**Entregables:**

- Login Platform.
- Lista de clientes/condominios.
- Crear cliente.
- Activar/suspender cliente.
- Ver plan.
- Ver límites.
- Ver consumo básico:
  - propiedades.
  - credenciales.
  - puertas/zonas.
  - Edge activos.
- Ver salud de Edge.
- Ver incidentes globales.
- Soporte auditado.

**QA requerido:**

```text
pnpm qa:platform
pnpm qa:security:platform
pnpm qa:audit:support-access
```

**Criterio de terminado:**

- Tricor Platform puede crear y operar un cliente.
- El soporte queda auditado.
- Los ingresos SaaS quedan separados de pagos de mantenimiento.

**Prohibido:**

- Administrar dinero de mantenimiento desde Platform.
- Acceso de soporte sin auditoría.
- Cross-tenant access no controlado.

---

### Fase 14 — Tricor Condo funcional

**Objetivo:** crear el panel operativo del cliente.

**Entregables:**

- Dashboard Condo.
- Condominio.
- Privadas.
- Propiedades.
- Propietarios/responsables.
- Credenciales.
- Finanzas:
  - cargos.
  - deudas.
  - pagos.
  - comprobantes.
  - multas.
- Morosidad.
- Configuración.
- Proveedor.
- Edge.
- Auditoría.
- Revisión de credenciales.
- Excepciones temporales.

**QA requerido:**

```text
pnpm qa:condo
pnpm qa:config
pnpm qa:payments
pnpm qa:flow property-debt-access-lifecycle
```

**Criterio de terminado:**

- El cliente puede operar el flujo completo desde Condo.
- Finance puede aprobar fallback manual.
- CondoAdmin puede configurar reglas controladas.
- PrivateAdmin solo ve su privada.

**Prohibido:**

- Permitir que usuarios no autorizados vean otras privadas.
- Permitir que Finance edite permisos directamente.
- Exponer modo técnico sin control.

---

### Fase 15 — Tricor Guard PWA mínima

**Objetivo:** crear la interfaz móvil/web para operación de guardias.

**Entregables:**

- Login Guard.
- Vista por caseta.
- Escaneo QR.
- Resultado claro:
  - permitido.
  - suspendido.
  - inválido.
  - expirado.
  - pendiente por falla.
- Registro de eventos.
- Reportes/rondines básicos.
- Apertura manual por GuardSupervisor.
- Excepción temporal por GuardSupervisor con límites.
- Notificación a CondoAdmin cuando aplique.
- Ocultar información financiera sensible.

**QA requerido:**

```text
pnpm qa:guard
pnpm qa:qr
pnpm qa:flow guard-qr-valid-access
pnpm qa:flow guard-supervisor-manual-open
pnpm qa:flow guard-supervisor-temporary-exception
```

**Criterio de terminado:**

- Guardia normal no abre sin QR válido.
- GuardSupervisor puede abrir manualmente con motivo.
- GuardSupervisor puede crear excepción temporal justificada y limitada.
- Todo queda auditado.

**Prohibido:**

- Permitir edición de residentes desde Guard.
- Permitir edición de pagos desde Guard.
- Mostrar deudas detalladas al guardia normal.

---

### Fase 16 — Tricor Resident PWA mínima

**Objetivo:** crear la interfaz móvil/web para residentes.

**Entregables:**

- Login Resident.
- Estado de propiedad.
- Estado de cuenta.
- Deudas y multas.
- Pago en línea.
- Comprobante para fallback manual.
- QR residente.
- QR invitados.
- Reporte de tarjeta/tag perdida.
- Ver estado de acceso.
- Avisos mínimos.

**QA requerido:**

```text
pnpm qa:resident
pnpm qa:payments:resident
pnpm qa:qr:resident
pnpm qa:flow resident-pay-confirmed-restore
pnpm qa:flow resident-upload-proof-manual-fallback
pnpm qa:flow lost-credential
```

**Criterio de terminado:**

- Residente puede pagar por la plataforma.
- Pago confirmado restaura acceso.
- Residente puede subir comprobante manual si aplica.
- Residente puede bloquear tarjeta/tag perdida.
- Resident no puede registrar nuevas credenciales.

**Prohibido:**

- Construir app social.
- Agregar chat de residentes.
- Agregar amenidades como módulo completo.
- Permitir acciones administrativas desde Resident.

---

### Fase 17 — Staging integral

**Objetivo:** desplegar un ambiente integral previo a piloto.

**Entregables:**

- Ambiente staging.
- Base staging.
- Storage staging.
- Edge staging.
- Proveedor de pagos sandbox.
- Provider mock y/o proveedor real de prueba.
- Seeds de demo.
- CI gate.
- Backups básicos.
- Monitoreo básico.

**QA requerido:**

```text
pnpm qa:ci
pnpm qa:staging
pnpm qa:flow property-debt-access-lifecycle --staging
pnpm qa:flow manual-payment-fallback-lifecycle --staging
pnpm qa:flow payment-confirmed-edge-offline --staging
```

**Criterio de terminado:**

- El ambiente staging puede demostrar flujo completo.
- El QA Harness corre contra staging.
- Hay reporte de fallos y evidencia.

**Prohibido:**

- Probar cambios directos en piloto real sin staging.
- Activar proveedor real sin smoke test.
- Manipular datos manualmente sin registro.

---

### Fase 18 — Piloto real controlado

**Objetivo:** operar Tricor Hábitat en un cliente real con alcance limitado.

**Entregables:**

- Cliente piloto creado en Platform.
- Condominio/privada configurado.
- Propiedades cargadas.
- Credenciales iniciales cargadas.
- Políticas configuradas.
- Edge instalado.
- Proveedor inicial conectado.
- Pagos sandbox o producción controlada.
- Guardias capacitados.
- Administradores capacitados.
- Manuales mínimos.
- Checklist de soporte.

**QA requerido:**

```text
pnpm qa:pilot:precheck
pnpm qa:edge:health
pnpm qa:provider-contract --smoke
pnpm qa:flow pilot-smoke
```

**Criterio de terminado:**

- Se puede cobrar una cuota real o piloto.
- El cliente puede operar pagos, morosidad y acceso.
- El equipo puede dar soporte.
- Hay incidentes y auditoría.

**Prohibido:**

- Piloto sin plan de rollback.
- Piloto sin soporte definido.
- Piloto sin manual mínimo.
- Piloto sin backups.

---

### Fase 19 — MVP comercial

**Objetivo:** preparar el producto para venta inicial repetible.

**Entregables:**

- Planes comerciales.
- Onboarding estándar.
- Checklist de instalación.
- Checklist de configuración.
- Manual Tricor Platform.
- Manual Tricor Condo.
- Manual Tricor Guard.
- Manual Tricor Resident.
- Guía de soporte.
- SLA inicial.
- Métricas de uso.
- Reportes básicos.
- Procedimiento de incidentes.

**QA requerido:**

```text
pnpm qa:release
pnpm qa:security
pnpm qa:audit
pnpm qa:docs
```

**Criterio de terminado:**

- El producto puede replicarse en un segundo cliente sin rehacer código.
- El onboarding está documentado.
- El soporte sabe operar fallos comunes.
- La venta tiene límites claros.

**Prohibido:**

- Vender features no existentes como parte de v1.
- Prometer integraciones futuras sin research cerrado.
- Hacer customizaciones por cliente que rompan el core.

---

### Fase 20 — Research post-v1

**Objetivo:** investigar integraciones futuras sin bloquear v1.

**Research tracks:**

1. Hikvision / Hik-Connect / ISAPI.
2. Telegram para soporte, alertas y reportes.
3. Integración de facturación externa.
4. Exportación contable.
5. Ticketing limitado.
6. Flutter para apps móviles reales.
7. Automatizaciones internas no críticas.

**Criterio de terminado:**

Cada research debe producir:

- Documento técnico.
- Problema que resuelve.
- Beneficio comercial.
- Riesgos.
- Limitaciones.
- Costos.
- Impacto en QA.
- Decisión: implementar, posponer o descartar.

**Prohibido:**

- Meter research al core sin decisión formal.
- Implementar integraciones porque “suenan bien”.
- Romper v1 por features futuras.

---

## 9. Camino crítico recomendado

El camino crítico para llegar al MVP técnico es:

```text
Fase 0 → Fase 1 → Fase 2 → Fase 3 → Fase 4 → Fase 5 → Fase 6 → Fase 7 → Fase 9 → Fase 10
```

El camino crítico para llegar al MVP piloto es:

```text
MVP técnico
→ Fase 8
→ Fase 11
→ Fase 12
→ Fase 13
→ Fase 14
→ Fase 15
→ Fase 16
→ Fase 17
→ Fase 18
```

El camino crítico para llegar al MVP comercial es:

```text
MVP piloto
→ Fase 19
```

---

## 10. Qué puede avanzar en paralelo

Puede avanzar en paralelo:

- Research de Mercado Pago mientras se construye dominio y QA.
- Research de Hikvision sin implementar adapter.
- Diseño UX wireframe sin codificar pantallas definitivas.
- Manuales preliminares mientras se estabilizan flujos.
- Tricor Platform mínimo junto con Tricor Condo, si backend ya tiene roles.
- Guard y Resident PWA después de que API esté estable.

No debe avanzar en paralelo:

- Provider adapter real antes de provider contracts.
- Pago real antes de Payment mock y QA.
- UI avanzada antes de backend core.
- App móvil Flutter antes de validar PWA y APIs.
- Integraciones futuras antes del MVP piloto.

---

## 11. Gates obligatorios

### 11.1 Gate documental

Ninguna fase de código debe iniciar sin documentos base.

### 11.2 Gate QA

Ninguna fase se considera terminada si no pasa QA correspondiente.

### 11.3 Gate seguridad

Cualquier función que afecte pagos, accesos, credenciales o Edge requiere auditoría.

### 11.4 Gate proveedor

Cualquier integración de proveedor requiere:

- Capability matrix.
- Provider mapping.
- Provider contract tests.
- Edge test.
- Smoke test controlado.

### 11.5 Gate pagos

Cualquier integración de pagos requiere:

- Payment mock.
- Webhook validation.
- Idempotencia.
- Estados pendientes.
- Estados rechazados.
- Estados aprobados.
- Auditoría.

### 11.6 Gate configuración

Cualquier configuración nueva requiere:

- Default.
- Validación.
- Dependencias.
- Incompatibilidades.
- Impact preview si afecta accesos.
- QA de configuración válida e inválida.

---

## 12. Reglas para Claude Code CLI

Claude Code CLI debe seguir estas reglas:

1. Leer primero `/docs`.
2. Tratar este roadmap como fuente de verdad provisional.
3. No mezclar contexto de otros proyectos.
4. No usar código legacy como base principal.
5. No implementar features fuera de fase sin autorización.
6. No tocar pagos, acceso, Edge o proveedor sin QA correspondiente.
7. No editar múltiples áreas críticas en un solo cambio.
8. Reportar archivos modificados.
9. Ejecutar comandos QA requeridos.
10. Generar resumen de impacto.
11. No introducir tecnologías vetadas.
12. No reemplazar contratos canónicos por contratos del proveedor.
13. No permitir que el proveedor dicte el dominio.
14. No omitir auditoría en flujos críticos.

Cada tarea de Claude debe tener:

```text
Objetivo
Archivos permitidos
Archivos prohibidos
Comandos QA
Criterio de terminado
Reporte esperado
```

---

## 13. Roadmap de documentación pendiente

Documentos pendientes recomendados:

1. `AI_AGENT_RULES_AND_HANDOFF.md`
2. `USER_MANUAL_PLAN.md`
3. `PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md`
4. `PROVIDER_STRATEGY_V0.1.md`
5. `HIKVISION_RESEARCH_PLAN_V0.1.md`
6. `UI_SURFACES_AND_NAVIGATION_V0.1.md`
7. `SECURITY_AND_AUDIT_MODEL_V0.1.md`
8. `DEPLOYMENT_AND_OPERATIONS_V0.1.md`
9. `PILOT_RUNBOOK_V0.1.md`
10. `COMMERCIAL_MODEL_V0.1.md`

---

## 14. Manuales de usuario futuros

Deben crearse manuales separados.

### 14.1 Manual Tricor Platform

Debe cubrir:

- Crear cliente.
- Activar cliente.
- Suspender cliente.
- Ver consumo.
- Ver estado de Edge.
- Ver incidentes globales.
- Soporte auditado.
- Planes y licencias.

### 14.2 Manual Tricor Condo

Debe cubrir:

- Crear privadas.
- Crear propiedades.
- Registrar propietario.
- Configurar pagos.
- Configurar morosidad.
- Cargar credenciales.
- Revisar pagos.
- Aprobar transferencia manual.
- Ver morosos.
- Crear excepción temporal.
- Revisar auditoría.

### 14.3 Manual Tricor Guard

Debe cubrir:

- Login.
- Escaneo QR.
- Interpretación de resultados.
- Registro de eventos.
- Rondines.
- Apertura manual supervisor.
- Excepción temporal supervisor.
- Reporte de falla.

### 14.4 Manual Tricor Resident

Debe cubrir:

- Login.
- Ver estado de cuenta.
- Pagar en línea.
- Subir comprobante manual.
- Generar QR invitado.
- Ver QR residente.
- Reportar tarjeta/tag perdida.
- Ver estado de acceso.

---

## 15. Riesgos principales

### 15.1 Configuración excesiva

Riesgo: crear demasiadas opciones y volver el producto inmanejable.

Mitigación:

- Presets.
- Configuración avanzada controlada.
- Modo técnico restringido.
- QA de dependencias.

### 15.2 Dependencia del proveedor

Riesgo: que el proveedor limite el diseño del dominio.

Mitigación:

- Contratos canónicos.
- ProviderCapabilityMatrix.
- Provider adapters.
- Provider contract tests.

### 15.3 Pagos manuales vuelven el sistema antiguo

Riesgo: que el fallback manual se vuelva flujo principal.

Mitigación:

- Pago en línea como default.
- Transferencia manual como fallback auditado.
- Reportes para detectar abuso de fallback.

### 15.4 Edge offline

Riesgo: pago confirmado pero acceso no restaurado físicamente.

Mitigación:

- Estado `PAYMENT_CONFIRMED_ACCESS_PENDING`.
- Incidentes.
- Heartbeats.
- Reintentos.
- Monitoreo.

### 15.5 Accesos obsoletos

Riesgo: exresidentes o tarjetas viejas conservan acceso.

Mitigación:

- MoveOutWorkflow.
- Revocación de credenciales.
- Auditoría de credenciales obsoletas.
- Alertas no invasivas.

### 15.6 Scope creep

Riesgo: agregar chat, amenidades, facturación propia o integraciones antes de tiempo.

Mitigación:

- No-goals v1.
- Roadmap por fases.
- Research separado.
- Gates obligatorios.

---

## 16. Definición de DONE por release

Una release no está terminada si solo “funciona en local”. Debe cumplir:

```text
Código implementado
+ pruebas unitarias/integración
+ QA Harness correspondiente
+ auditoría si aplica
+ documentación actualizada
+ reporte de cambios
+ revisión de seguridad básica
+ sin términos ni tecnologías vetadas
```

Para flujos críticos debe existir evidencia en:

```text
artifacts/qa/latest-report.md
artifacts/qa/latest-report.json
```

---

## 17. Secuencia inmediata recomendada

Después de este roadmap, la siguiente secuencia recomendada es:

```text
1. AI_AGENT_RULES_AND_HANDOFF.md
2. USER_MANUAL_PLAN.md
3. PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
4. PROVIDER_STRATEGY_V0.1.md
5. Crear repo limpio
6. Crear QA Harness foundation
7. Implementar contratos canónicos
```

La razón es simple: antes de entregar el proyecto a Claude Code CLI, debe existir un documento específico que diga cómo debe trabajar el agente, qué puede tocar, qué no puede tocar y cómo debe validar cada cambio.

---

## 18. Decisiones cerradas en este roadmap

Quedan asentadas las siguientes decisiones:

1. Tricor Hábitat se desarrollará desde cero como proyecto limpio.
2. El repo viejo no será base productiva.
3. La fuente de verdad vive en `/docs`.
4. La estructura interna será `Condominio → Privada → Propiedad`.
5. El pago/morosidad se controla por propiedad.
6. Tricor no administra dinero de mantenimiento.
7. Cada condominio conecta su propio proveedor de pagos.
8. Mercado Pago será primer research de pagos.
9. Transferencia manual existe solo como fallback auditado.
10. El Edge es obligatorio para el proveedor inicial.
11. El QA Harness es gate obligatorio.
12. Guard y Resident inician como web/PWA móvil.
13. Flutter queda como ruta móvil futura.
14. La configuración vive en Policy Engine controlado.
15. Las capacidades dependen del proveedor conectado.
16. Credenciales obsoletas y exresidentes son parte del diseño.
17. GuardSupervisor puede realizar apertura manual y excepción temporal justificadas.
18. No se implementan módulos fuera de v1 sin research formal.

---

## 19. Estado final del documento

Este roadmap v0.1 está listo para usarse como documento de planeación inicial.

Debe revisarse nuevamente después de crear:

- `AI_AGENT_RULES_AND_HANDOFF.md`
- `USER_MANUAL_PLAN.md`
- `PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md`
- `PROVIDER_STRATEGY_V0.1.md`

Cualquier cambio de alcance debe actualizar este roadmap antes de llegar a implementación.

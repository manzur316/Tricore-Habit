# QA_HARNESS_SPEC_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** Draft formal inicial / fuente de verdad provisional del QA Harness  
**Fecha:** 2026-06-10  
**Documentos padre:** `PROJECT_CHARTER_TRICOR_HABITAT.md`, `DOMAIN_MODEL_V0.1.md`, `ACCESS_POLICY_CONFIGURATION_V0.1.md`, `CLOUD_EDGE_ARCHITECTURE_V0.1.md`  
**Preparado para:** Arquitectura, backend, Edge, provider adapters, pagos, configuración, CI/CD, documentación histórica y handoff posterior a Claude Code CLI  
**Idioma base:** Español  

---

## 1. Propósito del documento

Este documento define la especificación inicial del **QA Harness** de Tricor Hábitat.

El QA Harness es la herramienta técnica que debe impedir que el desarrollo avance por parches, suposiciones o validaciones manuales incompletas. Debe convertir los flujos críticos del producto en pruebas reproducibles, auditables y entendibles para humanos y agentes de código.

Este documento debe servir como fuente de verdad para:

- Diseño del CLI de QA.
- Flujos de prueba end-to-end.
- Pruebas de configuración.
- Pruebas de pagos.
- Pruebas de morosidad.
- Pruebas de accesos.
- Pruebas de credenciales.
- Pruebas de Guard y Resident.
- Pruebas Cloud + Edge.
- Pruebas de provider contracts.
- Pruebas de capability matrix.
- Pruebas de seguridad, roles y auditoría.
- Reportes automáticos para revisión humana y agentes de código.
- Reglas de aceptación para cambios implementados por Claude Code CLI.

No es todavía la implementación final. Es la especificación base que debe guiar la creación del QA Harness antes de desarrollar módulos críticos.

---

## 2. Definición del QA Harness

El QA Harness de Tricor Hábitat es un conjunto de herramientas CLI, fixtures, mocks, validadores, runners y reportes que permiten probar el sistema completo con flujos controlados.

Debe poder responder preguntas como:

```text
¿El backend está sano?
¿La base de datos está conectada?
¿Las migraciones están aplicadas?
¿La configuración del condominio es válida?
¿La política de morosidad genera el perfil correcto?
¿El pago confirmado restaura accesos?
¿El pago manual requiere revisión?
¿El Edge ejecuta comandos correctamente?
¿El proveedor soporta la capacidad requerida?
¿El mapping del proveedor está completo?
¿Guard ve el estado correcto?
¿Resident ve el estado correcto?
¿Se registró auditoría?
¿La misma operación es idempotente?
¿Se rompió un flujo existente?
```

El QA Harness no debe ser una herramienta decorativa. Debe ser un requisito obligatorio para aceptar cambios.

---

## 3. Principio rector

```text
No se acepta ningún cambio crítico si no puede probarse con QA Harness.
```

El proyecto debe pasar de este ciclo:

```text
Prompt → parche → prueba manual → falla → otro parche
```

a este ciclo:

```text
Contrato → fixture → flujo QA → fallo exacto → fix mínimo → regresión → reporte
```

El QA Harness debe ser el mecanismo que mantenga limpio el proyecto cuando sea implementado por agentes de código.

---

## 4. Principios obligatorios

### 4.1 Pruebas reproducibles

Toda prueba debe poder repetirse con los mismos datos, mismos pasos y mismo resultado esperado.

No se deben depender de datos manuales sueltos ni de un estado desconocido de la base de datos.

---

### 4.2 Mock-first

Las pruebas diarias no deben depender de hardware real ni de proveedores reales.

Debe existir:

```text
Provider mock
Edge simulator
Payment provider mock
Storage mock
```

El proveedor real se prueba solo mediante smoke tests controlados.

---

### 4.3 Provider-neutral

El dominio de Tricor Hábitat no debe cambiar porque cambie el proveedor.

El QA Harness debe validar que:

```text
Tricor Domain → Provider Adapter → Provider Mock/Real
```

funcione sin contaminar el dominio con detalles específicos del proveedor.

---

### 4.4 Configuración validada

Toda configuración crítica debe pasar por validación.

El QA Harness debe probar:

```text
Configuraciones válidas
Configuraciones inválidas
Dependencias
Conflictos
Capacidades no soportadas
Mappings incompletos
Cambios de configuración con impacto operativo
```

---

### 4.5 Auditoría obligatoria

Toda acción sensible debe generar auditoría.

Ejemplos:

```text
Aprobar pago manual
Restaurar acceso
Suspender acceso
Crear excepción temporal
Apertura manual
Archivar residente
Bloquear credencial perdida
Cambiar mapping de proveedor
Cambiar política de morosidad
```

El QA Harness debe fallar si la acción ocurre sin auditoría.

---

### 4.6 Idempotencia

Los comandos críticos deben ser idempotentes.

Ejemplo:

```text
Enviar dos veces el mismo comando de restauración no debe duplicar permisos ni generar estados inconsistentes.
```

El QA Harness debe probar comandos repetidos, webhooks repetidos y reconexiones de Edge.

---

### 4.7 Separación Cloud / Edge / Proveedor

El QA Harness debe verificar que se mantenga esta separación:

```text
Cloud = fuente de verdad
Edge = ejecutor local y observador
Proveedor = sistema externo de control de acceso
```

Edge no debe decidir morosidad, pagos ni reglas de negocio.

---

### 4.8 Seguridad por defecto

El QA Harness debe probar seguridad básica desde el inicio:

```text
Tenant isolation
Role-based access
Provider credentials no expuestas
Logs sin secretos
Support access auditado
Guard sin permisos administrativos
Resident sin acceso a datos de otras propiedades
```

---

## 5. Alcance v1 del QA Harness

### 5.1 En alcance

El QA Harness v1 debe incluir:

```text
1. CLI de QA.
2. Health checks.
3. Fixtures determinísticos.
4. Reset/cleanup seguro de datos QA.
5. API client para pruebas.
6. DB inspector de solo lectura para asserts.
7. Flow runner.
8. Assertion engine.
9. Report builder Markdown/JSON.
10. PolicyConfigValidator test runner.
11. Provider mock.
12. Edge simulator.
13. Payment provider mock.
14. Storage mock para comprobantes.
15. Suites de configuración.
16. Suites de pagos.
17. Suites de morosidad.
18. Suites de credenciales.
19. Suites de Guard/Resident.
20. Suites de Cloud + Edge.
21. Provider contract tests.
22. Smoke test opcional con proveedor real.
23. CI gate mínimo.
```

---

### 5.2 Fuera de alcance v1

No entra en QA Harness v1:

```text
1. Pruebas visuales pixel-perfect.
2. Pruebas de carga avanzadas.
3. Pruebas móviles nativas Flutter.
4. Pruebas de tiendas App Store / Play Store.
5. Automatizaciones libres por cliente.
6. Pruebas de facturación fiscal propia.
7. Pruebas de módulos de amenidades como funcionalidad completa.
8. Pruebas de chat entre residentes.
9. Pruebas de integraciones futuras no aprobadas.
```

---

## 6. Ubicación recomendada en el repo

Estructura recomendada:

```text
tricor-habitat/
├── apps/
│   ├── api/
│   ├── web/
│   ├── edge/
│   └── qa/
│       ├── src/
│       │   ├── cli.ts
│       │   ├── config/
│       │   ├── clients/
│       │   ├── fixtures/
│       │   ├── flows/
│       │   ├── suites/
│       │   ├── mocks/
│       │   ├── assertions/
│       │   ├── reporters/
│       │   └── utils/
│       ├── package.json
│       └── README.md
├── packages/
│   ├── contracts/
│   ├── domain/
│   ├── db/
│   ├── provider-sdk/
│   └── config/
└── artifacts/
    └── qa/
```

El QA Harness puede iniciar en `apps/qa`. Si crece, partes reutilizables pueden moverse a `packages/qa-core`.

---

## 7. Comandos CLI recomendados

La interfaz principal debe ser simple.

```text
pnpm qa health
pnpm qa seed
pnpm qa reset
pnpm qa flow property-debt-access-lifecycle
pnpm qa flow manual-payment-fallback-lifecycle
pnpm qa config defaults
pnpm qa config valid
pnpm qa config invalid
pnpm qa config dependencies
pnpm qa provider-contract zkteco
pnpm qa provider-contract hikvision --mock
pnpm qa edge health
pnpm qa edge offline-reconnect
pnpm qa payments mercado-pago --mock
pnpm qa report
pnpm qa ci
```

También pueden existir scripts alias:

```text
pnpm qa:health
pnpm qa:ci
pnpm qa:flow:core
pnpm qa:config
pnpm qa:edge
pnpm qa:provider
pnpm qa:payments
```

Regla:

```text
Los comandos deben ser legibles para humanos y fáciles de pegar en Claude Code CLI.
```

---

## 8. Ambientes de ejecución

El QA Harness debe reconocer ambientes explícitos.

```text
local
ci
staging
hardware-smoke
```

### 8.1 Local

Para desarrollo diario.

Usa:

```text
API local
PostgreSQL local o Docker
Edge simulator
Provider mock
Payment mock
Storage mock
```

---

### 8.2 CI

Para validación automática.

Usa:

```text
Base de datos efímera
Servicios mock
Fixtures determinísticos
Sin proveedor real
Sin hardware real
```

---

### 8.3 Staging

Para pruebas previas a piloto.

Usa:

```text
API staging
Base staging
Edge staging o simulator
Provider mock por default
Payment sandbox/mock
```

---

### 8.4 Hardware smoke

Solo para pruebas controladas.

Usa:

```text
Proveedor real
Edge real
Credenciales de prueba
Puertas o perfiles de prueba
```

No debe ejecutarse automáticamente en cada PR.

---

## 9. Variables de entorno requeridas

El QA Harness no debe depender de credenciales hardcodeadas.

Ejemplo:

```text
QA_ENV=local
QA_BASE_URL=http://localhost:3000
QA_API_URL=http://localhost:4000
QA_EDGE_URL=http://localhost:4100
QA_DB_URL=postgresql://...
QA_RUN_ID=auto
QA_ARTIFACTS_DIR=artifacts/qa
QA_USE_PROVIDER_MOCK=true
QA_USE_PAYMENT_MOCK=true
QA_USE_STORAGE_MOCK=true
QA_ENABLE_DESTRUCTIVE_RESET=false
```

Reglas:

```text
1. Nunca imprimir secretos en logs.
2. Nunca usar credenciales reales en fixtures.
3. Nunca ejecutar reset destructivo fuera de ambiente QA explícito.
4. Toda variable crítica faltante debe fallar en health check.
```

---

## 10. Estrategia de datos de prueba

### 10.1 Fixtures determinísticos

Los fixtures deben tener nombres y claves predecibles.

Ejemplo:

```text
qa_condo_core
qa_private_main
qa_property_a101
qa_owner_juan_perez
qa_credential_tag_001
qa_credential_resident_qr
qa_access_profile_active
qa_access_profile_moroso_default
qa_edge_main_gate
qa_provider_installation_mock
```

---

### 10.2 QA Run ID

Cada corrida debe tener un identificador.

```text
QA_RUN_ID=20260610_qa_001
```

Toda entidad creada por QA debe quedar marcada con:

```text
createdBy = QA
qaRunId = <id>
```

---

### 10.3 Limpieza segura

El QA Harness debe poder limpiar únicamente datos propios.

Regla:

```text
Nunca borrar datos que no tengan qaRunId o prefijo QA.
```

En CI puede usarse base efímera. En local/staging se debe proteger limpieza destructiva.

---

## 11. Componentes internos del QA Harness

### 11.1 CLI

Responsable de recibir comandos, cargar ambiente, ejecutar suites y reportar resultados.

Archivo sugerido:

```text
apps/qa/src/cli.ts
```

---

### 11.2 EnvironmentChecker

Valida:

```text
API disponible
DB disponible
Edge simulator disponible
Provider mock disponible
Payment mock disponible
Storage mock disponible
Variables requeridas
Migraciones aplicadas
Versión esperada del schema
```

---

### 11.3 ApiClient

Cliente para ejecutar acciones reales contra la API.

Debe cubrir:

```text
Auth
Condominios
Privadas
Propiedades
Propietarios/responsables
Credenciales
Pagos
Comprobantes
Morosidad
Accesos
Guard
Resident
Edge
Provider configuration
```

---

### 11.4 DbInspector

Inspector de base de datos para asserts.

Regla:

```text
DbInspector puede leer para validar.
No debe modificar datos salvo en reset/seed controlado.
```

Debe validar:

```text
Payment status
Property access state
DesiredAccessState
AccessCommand
ObservedAccessState
AuditEvent
OperationalIncident
Credential status
Provider mappings
```

---

### 11.5 FixtureFactory

Crea datos de prueba consistentes.

Debe evitar duplicación manual de fixtures.

---

### 11.6 FlowRunner

Ejecuta flujos end-to-end.

Debe soportar:

```text
steps
assertions
snapshots
teardown
failure diagnostics
report sections
```

---

### 11.7 AssertionEngine

Centraliza validaciones.

Ejemplos:

```text
expectPaymentStatus(propertyId, PAID)
expectAccessCapability(propertyId, VEHICULAR, DENIED)
expectAccessCommandCreated(commandType, propertyId)
expectAuditEvent(action, actor)
expectProviderState(profile, credential)
expectGuardView(propertyId, expectedStatus)
expectResidentView(propertyId, expectedStatus)
```

---

### 11.8 PolicyConfigTestRunner

Ejecuta validaciones de configuración.

Debe probar:

```text
presets
checkboxes
selectores
números
matrices
capabilities
conflictos
reglas de impacto
```

---

### 11.9 ProviderMockServer

Simula proveedor de acceso.

Debe soportar:

```text
health check
capabilities
perfiles de acceso
puertas
zonas
credenciales
aplicar comandos
fallas temporales
fallas permanentes
provider offline
estado observado
```

---

### 11.10 EdgeSimulator

Simula Edge local.

Debe soportar:

```text
enrollment
heartbeat
polling de comandos
cola local
modo offline
reconexión
snapshot local
ejecución contra provider mock
reporte de resultados
logs técnicos
```

---

### 11.11 PaymentProviderMock

Simula proveedor de pagos.

Debe soportar:

```text
payment intent created
payment pending
payment approved
payment rejected
webhook válido
webhook duplicado
webhook inválido
pago expirado
```

Para v1 el proveedor prioritario de investigación será Mercado Pago, pero el mock debe hablar en contratos canónicos internos.

---

### 11.12 StorageMock

Simula almacenamiento de comprobantes.

Debe soportar:

```text
subida de archivo
metadata
URL temporal
archivo privado
archivo inexistente
archivo eliminado/archivado
```

---

### 11.13 ReportBuilder

Genera reportes legibles.

Debe producir:

```text
artifacts/qa/latest-report.md
artifacts/qa/latest-report.json
artifacts/qa/runs/<runId>/report.md
artifacts/qa/runs/<runId>/report.json
```

---

## 12. Reportes obligatorios

### 12.1 Markdown report

Debe ser legible para humanos.

Estructura mínima:

```text
# QA Report

Run ID
Fecha
Ambiente
Git branch
Git SHA
Servicios revisados
Suites ejecutadas
Resumen PASS/FAIL
Fallas críticas
Fallas no críticas
Flujos ejecutados
Artefactos generados
Sugerencia de siguiente acción
```

---

### 12.2 JSON report

Debe ser legible por agentes y CI.

Estructura mínima:

```json
{
  "runId": "20260610_qa_001",
  "environment": "local",
  "status": "PASS",
  "startedAt": "2026-06-10T00:00:00.000Z",
  "finishedAt": "2026-06-10T00:01:30.000Z",
  "git": {
    "branch": "main",
    "sha": "abc123"
  },
  "services": [
    { "name": "api", "status": "PASS" },
    { "name": "db", "status": "PASS" },
    { "name": "edge", "status": "PASS" },
    { "name": "providerMock", "status": "PASS" }
  ],
  "suites": [
    {
      "name": "qa:flow:property-debt-access-lifecycle",
      "status": "PASS",
      "tests": []
    }
  ],
  "failures": [],
  "artifacts": []
}
```

---

### 12.3 Failure diagnostics

Cada fallo debe explicar:

```text
Qué falló
Qué se esperaba
Qué ocurrió
En qué step ocurrió
Qué entidad fue afectada
Qué tabla/estado/API lo evidencia
Qué área probablemente está rota
Qué archivos o módulos pueden estar involucrados
```

Ejemplo:

```text
FAIL: expected AccessCommand RESTORE_ACCESS after payment approved.
Expected: commandType=RESTORE_ACCESS, propertyId=qa_property_a101.
Actual: no command found.
Likely area: PaymentConfirmedHandler → AccessPolicyEvaluator → AccessCommandService.
```

---

## 13. Suites obligatorias v1

### 13.1 Health

Comando:

```text
pnpm qa health
```

Debe validar:

```text
API responde
DB responde
Migraciones aplicadas
Auth funciona
Storage disponible
Provider mock disponible
Edge simulator disponible
Payment mock disponible
Variables requeridas presentes
No hay secrets impresos
```

---

### 13.2 Configuración

Comandos:

```text
pnpm qa config defaults
pnpm qa config valid
pnpm qa config invalid
pnpm qa config dependencies
pnpm qa config provider-capabilities
```

Debe probar:

```text
Presets Suave, Estándar, Estricto, Personalizado
Morosidad default
Pagos online
Transferencia manual fallback
QR residente
QR invitados
Guard policies
Credential lifecycle
Edge policies
Role policies
Provider capability matrix
```

---

### 13.3 Dominio

Comando:

```text
pnpm qa domain
```

Debe probar:

```text
Condominio → Privada → Propiedad
Privada independiente sin romper estructura
Propietario con varias propiedades
Propiedad con un responsable principal activo
Credenciales limitadas por propiedad
Propiedad al corriente
Propiedad morosa
Cambio de responsable
Periodo de gracia por mudanza
Archivo histórico sin eliminación destructiva
```

---

### 13.4 Pagos

Comandos:

```text
pnpm qa payments online-provider --mock
pnpm qa payments manual-fallback
```

Debe probar:

```text
Pago creado
Pago pendiente
Pago aprobado por proveedor
Webhook duplicado
Webhook inválido
Pago rechazado
Pago manual con comprobante
Pago manual pendiente de revisión
Finance aprueba pago manual
Comprobante no restaura por sí solo
Pago parcial no restaura por default
```

---

### 13.5 Morosidad

Comando:

```text
pnpm qa morosity
```

Debe probar:

```text
Días de gracia
Vencimiento
Suspensión automática
Perfil moroso default
Peatonal permitido por default
Vehicular bloqueado por default
QR invitados bloqueado por default
Pago confirmado restaura
Pago pendiente no restaura
Cambio de política recalcula estado deseado
```

---

### 13.6 Accesos

Comando:

```text
pnpm qa access
```

Debe probar:

```text
ACTIVE_ACCESS_PROFILE
MOROSO_DEFAULT_PROFILE
FORMER_RESIDENT_PROFILE
TEMPORARY_EXCEPTION_PROFILE
Access zones
Access capabilities
DesiredAccessState
AccessCommand
ObservedAccessState
Drift detection
Provider mapping
Idempotencia
```

---

### 13.7 QR

Comando:

```text
pnpm qa qr
```

Debe probar:

```text
QR residente dinámico
QR invitado con expiración
QR invitado de un solo uso
QR invitado bloqueado por morosidad
QR invalidado por cambio de estado
QR invalidado por residente archivado
Lectura por Guard
Resultado visible en Guard
```

---

### 13.8 Credenciales

Comando:

```text
pnpm qa credentials
```

Debe probar:

```text
Límite de credenciales por propiedad
Credencial activa
Credencial perdida reportada por residente
Bloqueo/revocación de credencial perdida
Administración emite reemplazo
Credencial de exresidente revocada
Credencial sin uso genera alerta no invasiva
Credencial archivada mantiene historial
```

---

### 13.9 Guard

Comando:

```text
pnpm qa guard
```

Debe probar:

```text
Guard normal consulta acceso
Guard normal escanea QR
Guard normal no abre sin QR válido
Guard normal registra evento
GuardSupervisor apertura manual justificada
GuardSupervisor excepción temporal justificada
Motivo obligatorio
Auditoría completa
Notificación a CondoAdmin
Guard no ve información financiera sensible
Guard no edita residentes
Guard no edita pagos
```

---

### 13.10 Resident

Comando:

```text
pnpm qa resident
```

Debe probar:

```text
Resident ve deudas propias
Resident ve multas propias
Resident no ve otra propiedad no vinculada
Resident genera QR invitado si política lo permite
Resident no genera QR invitado si moroso y política bloquea
Resident sube comprobante
Comprobante no restaura automático
Resident reporta tarjeta perdida
Resident no registra nueva tarjeta
```

---

### 13.11 Edge

Comandos:

```text
pnpm qa edge health
pnpm qa edge enrollment
pnpm qa edge heartbeat
pnpm qa edge command-queue
pnpm qa edge offline-reconnect
pnpm qa edge snapshot
pnpm qa edge reconciliation
```

Debe probar:

```text
Enrollment válido
Enrollment vencido rechazado
Tenant isolation
Heartbeat online
Offline por ausencia de heartbeat
Command queue
Reintentos
Comando duplicado
Comando superseded
Snapshot local
Reconexión
Provider disconnected
UI local técnica sin permisos administrativos
Logs sin secretos
```

---

### 13.12 Provider contracts

Comandos:

```text
pnpm qa provider-contract zkteco --mock
pnpm qa provider-contract hikvision --mock
```

Debe probar:

```text
Capabilities
Health check
Mapping validation
Access profiles
Access zones
Credentials
Apply desired state
Revoke access
Restore access
Read observed state
Temporary provider failure
Permanent provider failure
Unsupported capability
```

Para v1, el proveedor implementado debe ser ZKTeco / CVSecurity. Hikvision queda como research y mock conceptual hasta cerrar investigación.

---

### 13.13 Seguridad y roles

Comando:

```text
pnpm qa security
```

Debe probar:

```text
Platform puede ver clientes según rol
Support access requiere auditoría
CondoAdmin ve su condominio
PrivateAdmin ve solo su privada
Finance aprueba solo pagos manuales
Guard no edita datos maestros
Resident solo ve datos propios
Edge no accede a otro condominio
Tokens inválidos fallan
Logs no contienen secretos
```

---

### 13.14 Auditoría

Comando:

```text
pnpm qa audit
```

Debe probar auditoría para:

```text
Pago manual aprobado
Pago manual rechazado
Restauración automática
Suspensión por morosidad
Apertura manual
Excepción temporal
Credencial perdida
Credencial revocada
Cambio de política
Cambio de provider mapping
Support access
```

---

## 14. Primer flujo oficial: property-debt-access-lifecycle

Comando:

```text
pnpm qa flow property-debt-access-lifecycle
```

Este es el flujo principal del producto.

Debe probar el ciclo completo:

```text
Propiedad al corriente
→ deuda vencida
→ morosidad
→ restricción de accesos
→ pago confirmado
→ restauración
```

### 14.1 Steps obligatorios

```text
1. Ejecutar health check.
2. Crear QA run.
3. Crear condominio QA.
4. Crear privada QA.
5. Crear propiedad QA.
6. Crear propietario/responsable QA.
7. Asignar credenciales activas QA.
8. Configurar provider mock.
9. Configurar Edge simulator.
10. Configurar Mercado Pago mock como proveedor de pago.
11. Aplicar preset Estándar de morosidad.
12. Validar ProviderCapabilityMatrix.
13. Validar provider mappings.
14. Crear cuota de mantenimiento.
15. Confirmar propiedad al corriente.
16. Verificar ACTIVE_ACCESS_PROFILE.
17. Marcar cuota como vencida después de días de gracia.
18. Ejecutar evaluación de morosidad.
19. Generar MOROSO_DEFAULT_PROFILE.
20. Verificar peatonal permitido.
21. Verificar vehicular bloqueado.
22. Verificar QR invitados bloqueado.
23. Generar DesiredAccessState.
24. Crear AccessCommand.
25. Edge obtiene comando.
26. Edge aplica comando en provider mock.
27. Provider mock actualiza estado.
28. Edge reporta ObservedAccessState.
29. Guard ve acceso restringido correctamente.
30. Resident ve estado moroso y deuda.
31. Generar PaymentIntent mock.
32. Simular pago aprobado por proveedor.
33. Procesar webhook.
34. Validar pago confirmado.
35. Recalcular estado de propiedad.
36. Generar DesiredAccessState restaurado.
37. Crear RestoreAccessCommand.
38. Edge aplica restauración.
39. Provider mock refleja acceso activo.
40. Guard ve acceso activo.
41. Resident ve acceso activo.
42. Validar auditoría completa.
43. Generar reporte Markdown/JSON.
```

### 14.2 Resultado esperado

```text
Status: PASS
Payment: PAID
Property: ACTIVE
Access profile: ACTIVE_ACCESS_PROFILE
Vehicular: ALLOWED
Pedestrian: ALLOWED
Guest QR: ALLOWED
Provider observed state: MATCHES_DESIRED_STATE
Audit: COMPLETE
Incidents: none open
```

---

## 15. Flujo manual: manual-payment-fallback-lifecycle

Comando:

```text
pnpm qa flow manual-payment-fallback-lifecycle
```

Este flujo valida el escenario real donde el residente transfiere fuera de Tricor y sube comprobante.

### 15.1 Steps obligatorios

```text
1. Crear propiedad morosa.
2. Confirmar que tiene restricciones activas.
3. Crear ManualPaymentAttempt.
4. Subir comprobante a StorageMock.
5. Verificar metadata del comprobante.
6. Confirmar que el comprobante no restaura acceso.
7. Finance revisa comprobante.
8. Finance aprueba pago manual.
9. Sistema cambia pago a APPROVED_MANUAL.
10. Policy Engine recalcula estado.
11. Se crea comando de restauración.
12. Edge aplica comando.
13. Provider mock confirma restauración.
14. Auditoría registra aprobación manual.
15. Resident ve pago aplicado.
16. Guard ve acceso activo.
```

### 15.2 Resultado esperado

```text
El comprobante por sí solo no restaura.
La aprobación manual auditada sí dispara recálculo y restauración.
Finance no edita permisos directamente.
```

---

## 16. Flujo Edge offline: payment-confirmed-edge-offline

Comando:

```text
pnpm qa flow payment-confirmed-edge-offline
```

Debe probar:

```text
1. Propiedad morosa.
2. Edge pasa a OFFLINE.
3. Pago confirmado por proveedor.
4. Tricor marca deuda como pagada.
5. Tricor no marca acceso como aplicado.
6. Estado queda PAYMENT_CONFIRMED_ACCESS_PENDING.
7. Se crea incidente operativo Edge offline.
8. Edge reconecta.
9. Edge ejecuta comando pendiente.
10. ObservedAccessState confirma restauración.
11. Incidente se resuelve.
```

Resultado esperado:

```text
Tricor no miente diciendo que el acceso fue restaurado si Edge no lo aplicó todavía.
```

---

## 17. Flujo de exresidente y credenciales obsoletas

Comando:

```text
pnpm qa flow resident-move-out-credential-revocation
```

Debe probar:

```text
1. Propiedad con responsable activo.
2. Credenciales activas.
3. Se programa salida/mudanza.
4. Se aplica periodo de gracia configurado.
5. Durante gracia, permisos siguen según política.
6. Al expirar gracia, relación pasa a FORMER_RESIDENT.
7. Credenciales anteriores se revocan.
8. Provider mock refleja credenciales revocadas.
9. QR anteriores quedan inválidos.
10. Auditoría registra cambio.
11. Historial se conserva.
```

---

## 18. Flujo de tarjeta perdida

Comando:

```text
pnpm qa flow lost-credential
```

Debe probar:

```text
1. Resident ve credenciales propias.
2. Resident reporta tag perdido.
3. Credencial cambia a LOST_REPORTED.
4. Acceso de esa credencial se revoca.
5. Administración recibe alerta.
6. Resident no puede crear credencial nueva.
7. Administración emite reemplazo.
8. Credencial anterior queda archivada/revocada.
9. Auditoría queda completa.
```

---

## 19. ProviderCapabilityMatrix testing

El QA Harness debe validar que Tricor no active configuraciones incompatibles con el proveedor.

Ejemplos:

| Caso | Configuración | Capacidad proveedor | Resultado esperado |
|---|---|---|---|
| Bloquear QR invitados | QR invitados activo | soportado | PASS |
| Bloquear QR invitados | QR invitados apagado | no aplica | FAIL config |
| Bloquear biometría | proveedor sin biometría | no soportado | FAIL config |
| Perfil moroso | mapping moroso faltante | incompleto | FAIL activation |
| Acceso vehicular | zona vehicular no mapeada | incompleto | FAIL activation |
| Restauración automática | proveedor de pago no conectado | faltante | FAIL config |
| Edge requerido | Edge no enrolled | faltante | FAIL activation |

Regla:

```text
Una configuración que no puede ejecutarse en el proveedor no debe activarse como si funcionara.
```

---

## 20. PolicyConfigValidator testing

El QA Harness debe probar dependencias y conflictos.

### 20.1 Dependencias mínimas

```text
Restauración automática requiere pagos online activos.
Pagos online requieren proveedor de pago conectado.
Transferencia manual requiere comprobantes o registro manual de evidencia.
Bloquear QR invitados requiere QR invitados activo.
Bloquear acceso vehicular requiere zona vehicular activa.
Perfil moroso requiere mapping en proveedor.
Modo offline requiere Edge snapshot activo.
Smoke test real requiere ambiente hardware-smoke.
```

---

### 20.2 Conflictos mínimos

```text
No permitir pago online activo sin cuenta de condominio conectada.
No permitir restaurar por comprobante simple como default.
No permitir Guard normal con apertura manual sin QR si política lo prohíbe.
No permitir PrivateAdmin ver otras privadas.
No permitir Finance editar permisos directamente.
No permitir Edge administrar residentes/pagos.
No permitir provider mapping incompleto en piloto.
```

---

## 21. Command state testing

El QA Harness debe probar estados de comando.

Estados mínimos:

```text
CREATED
QUEUED_FOR_EDGE
PENDING_EDGE_OFFLINE
SENT_TO_EDGE
APPLIED_UNCONFIRMED
APPLIED_CONFIRMED
FAILED_RETRYABLE
FAILED_PERMANENT
SUPERSEDED
CANCELLED
```

Casos:

```text
1. Comando creado correctamente.
2. Comando queda pendiente si Edge offline.
3. Comando se envía al reconectar.
4. Comando duplicado no duplica efecto.
5. Comando viejo queda SUPERSEDED si desired state cambia.
6. Error temporal reintenta.
7. Error permanente crea incidente.
8. APPLIED_UNCONFIRMED se resuelve al recibir confirmación.
```

---

## 22. Observed state y drift detection

El QA Harness debe probar diferencias entre estado deseado y estado observado.

Ejemplo:

```text
Desired: QR invitados bloqueado
Observed: QR invitados activo en proveedor
Resultado: DRIFT_DETECTED + incidente operativo
```

Casos mínimos:

```text
1. Desired y observed coinciden.
2. Observed no llega por proveedor offline.
3. Observed contradice desired.
4. Proveedor tiene credencial huérfana.
5. Proveedor tiene perfil no mapeado.
6. Reconciliación resuelve drift.
7. Drift no se oculta sin auditoría.
```

---

## 23. Security testing

### 23.1 Tenant isolation

Casos:

```text
Condo A no ve Condo B.
PrivateAdmin de Privada A no ve Privada B.
Resident de propiedad A no ve propiedad B.
Edge A no obtiene comandos de Edge B.
Provider installation A no afecta instalación B.
```

---

### 23.2 Roles

Casos:

```text
PlatformOwner ve clientes.
PlatformSupport entra con auditoría.
CondoAdmin configura condominio.
PrivateAdmin opera solo su privada.
Finance aprueba pago manual.
Finance no edita permisos directamente.
Guard no edita residentes.
GuardSupervisor puede apertura manual y excepción temporal con límites.
Resident reporta credencial perdida.
Resident no registra nueva credencial.
```

---

### 23.3 Secrets

Casos:

```text
Logs no imprimen tokens.
Reportes no imprimen credenciales.
Errores no exponen connection strings.
Provider credentials no aparecen en artifacts.
```

---

## 24. UI projection testing

QA Harness no debe hacer pixel testing v1, pero sí debe validar proyecciones/API que alimentan UI.

### 24.1 Tricor Condo

Debe validar que Tricor Condo pueda consultar:

```text
Propiedades
Estado de pagos
Morosidad
Credenciales
Alertas de inactividad
Pagos manuales pendientes
Estado Edge
Estado proveedor
Eventos de auditoría
```

---

### 24.2 Tricor Guard

Debe validar que Tricor Guard reciba:

```text
Acceso permitido/suspendido
QR válido/inválido
Acción recomendada
Motivos visibles según rol
Botón de apertura manual solo para supervisor
```

---

### 24.3 Tricor Resident

Debe validar que Tricor Resident reciba:

```text
Deuda propia
Multas propias
Estado de acceso
QR residente
QR invitados si permitido
Comprobantes propios
Credenciales propias
```

---

## 25. Payment testing

El QA Harness debe tratar pagos como fuente crítica de restauración.

### 25.1 Online provider payment

Estados mínimos:

```text
CREATED
PENDING_PROVIDER
APPROVED
REJECTED
EXPIRED
REFUNDED
CHARGEBACK
```

V1 puede no implementar todos los estados avanzados, pero el dominio debe estar preparado.

Casos mínimos v1:

```text
1. Pago creado.
2. Pago pendiente no restaura.
3. Pago aprobado restaura.
4. Webhook duplicado no duplica restauración.
5. Webhook inválido se rechaza.
6. Pago rechazado no restaura.
7. Pago aprobado con Edge offline queda acceso pendiente.
```

---

### 25.2 Manual fallback

Casos mínimos:

```text
1. Transferencia manual registrada.
2. Comprobante subido.
3. Comprobante queda privado en storage.
4. Finance aprueba.
5. CondoAdmin también puede aprobar si tiene permiso.
6. Sistema recalcula acceso.
7. Auditoría completa.
8. Rechazo no restaura.
```

---

## 26. Storage testing

Debe probar comprobantes.

Casos:

```text
Archivo subido correctamente.
Metadata guardada correctamente.
Archivo no se guarda en base de datos como blob principal.
Archivo privado no es público.
URL temporal expira.
Comprobante queda ligado al pago manual.
Comprobante no restaura acceso solo.
Archivo de prueba se limpia/archiva correctamente.
```

---

## 27. Performance básico v1

No es prueba de carga avanzada, pero sí debe existir smoke de tiempos razonables.

Casos mínimos:

```text
Health check termina en tiempo razonable.
Flow principal termina en tiempo razonable.
Policy recalculation no tarda excesivamente con fixtures pequeñas.
Command queue procesa lote QA sin bloqueo.
```

No definir SLA final en v0.1.

---

## 28. CI gate mínimo

Para PRs o cambios importantes, el gate mínimo debe ser:

```text
pnpm lint
pnpm typecheck
pnpm test
pnpm qa health
pnpm qa config defaults
pnpm qa config dependencies
pnpm qa flow property-debt-access-lifecycle
```

Para cambios en Edge:

```text
pnpm qa edge health
pnpm qa edge offline-reconnect
pnpm qa provider-contract zkteco --mock
```

Para cambios en pagos:

```text
pnpm qa payments online-provider --mock
pnpm qa payments manual-fallback
```

Para cambios en configuración:

```text
pnpm qa config valid
pnpm qa config invalid
pnpm qa config provider-capabilities
```

---

## 29. Regla de aceptación por área

| Área modificada | QA obligatorio |
|---|---|
| Dominio | `pnpm qa domain` + flujo principal |
| Pagos | `pnpm qa payments` + flujo principal |
| Morosidad | `pnpm qa morosity` + flujo principal |
| Accesos | `pnpm qa access` + provider contract |
| Edge | `pnpm qa edge` + offline-reconnect |
| Provider adapter | `pnpm qa provider-contract <provider>` |
| Configuración | `pnpm qa config valid/invalid/dependencies` |
| Guard | `pnpm qa guard` |
| Resident | `pnpm qa resident` |
| Roles/seguridad | `pnpm qa security` |
| Credenciales | `pnpm qa credentials` |

---

## 30. Provider real smoke tests

Debe existir smoke test real, pero no como prueba diaria.

Comando:

```text
pnpm qa provider-real-smoke zkteco
```

Debe validar solo operaciones controladas:

```text
Conexión
Lectura de capabilities reales
Mapping de prueba
Crear perfil/credencial de prueba si aplica
Revocar/restaurar credencial de prueba
Leer estado observado
Limpiar o archivar prueba
```

Reglas:

```text
1. Solo ambiente hardware-smoke.
2. Solo credenciales de prueba.
3. No tocar residentes reales.
4. Requiere confirmación explícita.
5. Genera reporte separado.
```

---

## 31. Manejo de fallos

El QA Harness debe clasificar fallos.

```text
CRITICAL = rompe flujo principal o seguridad.
HIGH = rompe módulo importante.
MEDIUM = afecta escenario secundario.
LOW = reporte, warning o mejora.
INFO = dato operativo.
```

Ejemplos:

```text
CRITICAL: pago aprobado no restaura acceso.
CRITICAL: Guard normal puede abrir sin permiso.
CRITICAL: Condo A ve datos de Condo B.
HIGH: transferencia manual no genera auditoría.
HIGH: Edge offline no crea incidente.
MEDIUM: alerta de credencial sin uso no aparece.
LOW: texto de reporte incompleto.
```

---

## 32. Reglas para debugging

Cuando falle una prueba, el reporte debe indicar:

```text
1. Step fallido.
2. Estado esperado.
3. Estado actual.
4. Entidad afectada.
5. Últimos eventos de dominio.
6. Últimos comandos Edge.
7. Últimos eventos de proveedor mock.
8. Últimos audit logs.
9. Posibles módulos afectados.
10. Comando exacto para reproducir.
```

Esto permite que Claude Code CLI repare con contexto, no a ciegas.

---

## 33. Reglas para Claude Code CLI

Cuando este documento se entregue a Claude Code CLI:

1. No implementar flujo crítico sin prueba QA asociada.
2. No cambiar un contrato canónico sin actualizar QA Harness.
3. No cambiar configuración sin actualizar `PolicyConfigValidator` y suites.
4. No tocar provider adapter sin `provider-contract`.
5. No tocar Edge sin suite Edge.
6. No tocar pagos sin suite payments.
7. No tocar roles sin suite security.
8. No arreglar fallos ocultándolos en fixtures.
9. No actualizar expected results para hacer pasar una prueba rota sin justificarlo.
10. No hardcodear datos QA dentro del core.
11. No usar proveedor real para pruebas diarias.
12. No imprimir secretos en reportes.
13. Todo PR debe indicar qué suites se ejecutaron.
14. Todo fallo debe entregarse con reporte.
15. Si una suite falla, reparar causa raíz antes de agregar features.

---

## 34. Roadmap de implementación del QA Harness

### Fase 0 — Skeleton CLI

Objetivo:

```text
Crear apps/qa con CLI, config loader, report builder y comandos vacíos.
```

Criterio de terminado:

```text
pnpm qa health ejecuta y genera reporte básico.
```

---

### Fase 1 — Health, fixtures y reset seguro

Objetivo:

```text
Health check real, fixtures determinísticos y cleanup seguro.
```

Criterio de terminado:

```text
QA crea y limpia datos propios sin tocar datos externos.
```

---

### Fase 2 — Config y Policy Engine tests

Objetivo:

```text
Probar presets, dependencias, incompatibilidades y capability matrix.
```

Criterio de terminado:

```text
pnpm qa config defaults/valid/invalid/dependencies pasan.
```

---

### Fase 3 — Provider mock y provider contracts

Objetivo:

```text
Simular proveedor y validar contratos canónicos.
```

Criterio de terminado:

```text
pnpm qa provider-contract zkteco --mock pasa.
```

---

### Fase 4 — Edge simulator

Objetivo:

```text
Simular enrollment, heartbeat, comandos, offline, reconexión y snapshots.
```

Criterio de terminado:

```text
pnpm qa edge offline-reconnect pasa.
```

---

### Fase 5 — Payments mock y manual fallback

Objetivo:

```text
Probar pago online confirmado y transferencia manual auditada.
```

Criterio de terminado:

```text
pnpm qa payments online-provider --mock
pnpm qa payments manual-fallback
```

---

### Fase 6 — Flow principal

Objetivo:

```text
Implementar property-debt-access-lifecycle de punta a punta.
```

Criterio de terminado:

```text
pnpm qa flow property-debt-access-lifecycle pasa.
```

---

### Fase 7 — CI gate

Objetivo:

```text
Integrar QA mínimo a CI.
```

Criterio de terminado:

```text
pnpm qa ci pasa en pipeline.
```

---

### Fase 8 — Smoke real controlado

Objetivo:

```text
Agregar smoke test real de proveedor implementado.
```

Criterio de terminado:

```text
pnpm qa provider-real-smoke zkteco funciona solo en hardware-smoke.
```

---

## 35. Artefactos generados

El QA Harness debe generar:

```text
artifacts/qa/latest-report.md
artifacts/qa/latest-report.json
artifacts/qa/runs/<runId>/report.md
artifacts/qa/runs/<runId>/report.json
artifacts/qa/runs/<runId>/logs/
artifacts/qa/runs/<runId>/snapshots/
artifacts/qa/runs/<runId>/fixtures.json
```

Los artefactos no deben contener secretos.

---

## 36. Definición de “PASS”

Una suite solo pasa si:

```text
1. Todos los asserts críticos pasan.
2. No hay incidentes críticos abiertos inesperados.
3. No hay secretos en logs.
4. No hay drift no resuelto.
5. Los reportes se generaron correctamente.
6. El cleanup o aislamiento de datos quedó correcto.
```

---

## 37. Definición de “DONE” para el QA Harness v1

El QA Harness v1 se considera listo cuando existen y pasan:

```text
pnpm qa health
pnpm qa config defaults
pnpm qa config valid
pnpm qa config invalid
pnpm qa config dependencies
pnpm qa provider-contract zkteco --mock
pnpm qa edge offline-reconnect
pnpm qa payments online-provider --mock
pnpm qa payments manual-fallback
pnpm qa flow property-debt-access-lifecycle
pnpm qa security
pnpm qa audit
pnpm qa report
```

---

## 38. Relación con documentación de usuario final

El QA Harness no reemplaza manuales, pero debe ayudar a documentar flujos.

Los reportes de QA pueden usarse como base para:

```text
Manual de Tricor Condo
Manual de Tricor Guard
Manual de Tricor Resident
Manual técnico de Edge
Manual de soporte Tricor Platform
```

Cada flujo validado debe poder convertirse después en documentación operativa.

---

## 39. Riesgos si no se implementa

Si Tricor Hábitat avanza sin QA Harness, los riesgos son:

```text
1. Repetir ciclo de parches.
2. Romper accesos al modificar pagos.
3. Romper pagos al modificar configuración.
4. Activar configuraciones no soportadas por proveedor.
5. Restaurar accesos sin confirmación real.
6. Revocar accesos sin auditoría.
7. Permitir acciones indebidas a guardias o residentes.
8. No detectar credenciales obsoletas.
9. Confundir estado deseado con estado aplicado.
10. Entregar un piloto inestable.
```

Por eso el QA Harness debe crearse temprano, antes de features visuales complejas.

---

## 40. Decisiones cerradas en este documento

```text
1. QA Harness es obligatorio desde el inicio.
2. Debe vivir como CLI TypeScript en el monorepo.
3. Debe probar configuración, dominio, pagos, accesos, Edge, proveedor, roles y auditoría.
4. Debe usar mocks para pruebas diarias.
5. No debe depender de hardware real para CI.
6. Debe generar reportes Markdown y JSON.
7. Debe validar provider capabilities y mappings.
8. Debe probar el flujo property-debt-access-lifecycle.
9. Debe probar transferencia manual fallback.
10. Debe probar Edge offline/reconnect.
11. Debe probar credenciales perdidas, exresidentes e inactividad.
12. Debe servir como gate para Claude Code CLI.
```

---

## 41. Pendientes para documentos posteriores

Este documento no define todavía:

```text
1. Implementación final del CLI.
2. Formato definitivo de cada endpoint.
3. Esquema Prisma final.
4. Provider adapter real completo.
5. Estrategia final de Mercado Pago.
6. Setup final de CI/CD.
7. Smoke test real detallado.
8. Manual de operación de QA para humanos.
9. Matriz final de capacidades por proveedor real.
10. Política final de retención de artefactos QA.
```

Estos temas deben resolverse durante implementación o en documentos técnicos posteriores.

---

## 42. Próximo documento recomendado

Después de este documento, el siguiente entregable recomendado es:

```text
ROADMAP_V0.1.md
```

Ese documento debe convertir Project Charter, Domain Model, Access Policy Configuration, Cloud/Edge Architecture y QA Harness en fases concretas de desarrollo.


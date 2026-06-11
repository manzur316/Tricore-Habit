# CLOUD_EDGE_ARCHITECTURE_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** Draft formal inicial / fuente de verdad provisional de arquitectura Cloud + Edge  
**Fecha:** 2026-06-10  
**Documentos padre:** `PROJECT_CHARTER_TRICOR_HABITAT.md`, `DOMAIN_MODEL_V0.1.md`, `ACCESS_POLICY_CONFIGURATION_V0.1.md`  
**Preparado para:** Arquitectura, backend, Edge, proveedor de acceso, QA Harness, operaciones, despliegue y handoff posterior a Claude Code CLI  
**Idioma base:** Español  

---

## 1. Propósito del documento

Este documento define la arquitectura Cloud + Edge de Tricor Hábitat.

Su objetivo es fijar responsabilidades, límites, flujos de comunicación, estados, seguridad, sincronización, modo offline, proveedor de acceso, monitoreo y criterios de QA antes de iniciar implementación.

Este documento debe evitar que el proyecto vuelva a caer en integración improvisada, parches sobre parches o dependencias directas del proveedor dentro del dominio principal.

Este documento debe servir como fuente de verdad para:

- Backend Cloud.
- Edge local.
- Provider adapters.
- Provider mappings.
- ProviderCapabilityMatrix.
- QA Harness.
- Tricor Platform.
- Tricor Condo.
- Tricor Guard.
- Tricor Resident.
- Operación técnica y soporte.
- Handoff posterior a Claude Code CLI.

No es todavía la implementación final ni el diseño definitivo de infraestructura. Es la especificación base para construir el sistema con límites claros.

---

## 2. Principio rector

La arquitectura de Tricor Hábitat se basa en una separación estricta:

```text
Cloud = fuente de verdad
Edge = ejecutor local y observador
Proveedor = sistema externo de control de acceso
```

Ninguna parte del sistema debe violar esta separación.

---

## 3. Objetivos de la arquitectura

La arquitectura Cloud + Edge debe lograr:

1. Mantener el dominio de Tricor independiente del proveedor de acceso.
2. Ejecutar cambios locales en casetas o condominios cuando el proveedor requiera presencia local.
3. Soportar múltiples condominios y múltiples Edge por condominio.
4. Permitir control de acceso basado en pagos, morosidad, perfiles y reglas configurables.
5. Mantener auditoría completa de cada cambio.
6. Evitar exponer sistemas locales directamente a internet.
7. Operar con modo offline limitado y honesto.
8. Reportar salud, fallos, comandos pendientes e incidentes.
9. Permitir QA automático con mocks, provider contracts y pruebas de sincronización.
10. Preparar el camino para múltiples proveedores sin cambiar el dominio.

---

## 4. No objetivos de Edge

Edge no debe convertirse en otro backend principal.

Edge no debe:

- Decidir morosidad.
- Calcular pagos.
- Aprobar pagos.
- Administrar residentes.
- Administrar propiedades.
- Administrar permisos permanentes.
- Crear reglas de negocio.
- Ser fuente de verdad.
- Reemplazar al Cloud.
- Guardar el historial completo del condominio.
- Manejar dinero.
- Exponer una UI administrativa completa.
- Permitir cambios directos al proveedor sin comando canónico.

La regla es simple:

```text
Cloud decide.
Edge ejecuta.
Proveedor aplica.
Edge observa.
Cloud audita.
```

---

## 5. Vista lógica general

```text
Tricor Platform
        │
        │ monitoreo SaaS, clientes, licencias, salud global
        ▼
┌────────────────────────────────────────────┐
│                Tricor Cloud                │
│                                            │
│  Auth                                      │
│  Condominios / Privadas / Propiedades      │
│  Pagos / Morosidad                         │
│  Policy Engine                             │
│  Desired Access State                      │
│  Provider Mappings                         │
│  Command Queue                             │
│  Observed State                            │
│  Auditoría / Incidentes                    │
└────────────────────────────────────────────┘
        ▲                         │
        │ HTTPS outbound           │ comandos, snapshots, heartbeat
        │ desde Edge               ▼
┌────────────────────────────────────────────┐
│                 Edge Node                  │
│                                            │
│  Edge Agent                                │
│  Local Queue                               │
│  Local Snapshot                            │
│  Provider Adapter                          │
│  Local Technical UI                        │
│  Logs técnicos                             │
└────────────────────────────────────────────┘
        │
        │ red local / integración local
        ▼
┌────────────────────────────────────────────┐
│          Proveedor de control de acceso     │
│                                            │
│  puertas, perfiles, credenciales, eventos   │
└────────────────────────────────────────────┘
```

---

## 6. Componentes principales

### 6.1 Tricor Cloud

Tricor Cloud es la fuente de verdad del sistema.

Responsabilidades:

- Autenticación y autorización.
- Tricor Platform.
- Tricor Condo.
- Tricor Guard.
- Tricor Resident.
- Condominios.
- Privadas.
- Propiedades.
- Propietarios/responsables.
- Credenciales.
- Pagos.
- Comprobantes.
- Morosidad.
- Policy Engine.
- Perfiles de acceso.
- Desired Access State.
- ProviderCapabilityMatrix.
- ProviderInstallation.
- ProviderMapping.
- Generación de comandos.
- Cola Cloud de comandos.
- Estados observados.
- Auditoría.
- Incidentes.
- Reportes.
- Configuración.
- Monitoreo de Edge.

Cloud no debe depender de detalles internos del proveedor.

---

### 6.2 Edge Node

Edge Node es el agente local de ejecución y observación.

Responsabilidades:

- Registrarse contra Cloud.
- Mantener credenciales técnicas de Edge.
- Descargar configuración autorizada.
- Descargar snapshots mínimos.
- Obtener comandos pendientes.
- Ejecutar comandos contra el proveedor local.
- Mantener cola local.
- Reintentar comandos fallidos según política.
- Reportar resultados.
- Reportar heartbeats.
- Reportar estado del proveedor.
- Reportar logs técnicos.
- Reportar incidentes locales.
- Proveer UI local técnica mínima.
- Operar con modo offline limitado.

Edge no decide si una propiedad está morosa. Edge solo ejecuta el estado calculado por Cloud.

---

### 6.3 Provider Adapter

El Provider Adapter traduce contratos canónicos de Tricor hacia el proveedor específico.

Responsabilidades:

- Declarar capacidades soportadas.
- Validar configuración requerida.
- Traducir comandos canónicos a operaciones del proveedor.
- Ejecutar operaciones.
- Leer estado observado cuando el proveedor lo permita.
- Normalizar errores del proveedor.
- Reportar resultados en formato canónico.

Regla:

```text
El proveedor puede cambiar.
El dominio no debe cambiar.
```

---

### 6.4 Proveedor de acceso

El proveedor de acceso es el sistema externo que controla puertas, perfiles, lectores, credenciales o permisos físicos.

En v1, el primer proveedor de acceso implementado requerirá Edge local.

Proveedores futuros deben integrarse mediante el mismo contrato canónico, capability matrix, provider mapping y QA Harness.

---

## 7. Responsabilidad por capa

| Área | Cloud | Edge | Proveedor |
|---|---|---|---|
| Pagos | Sí | No | No |
| Morosidad | Sí | No | No |
| Reglas | Sí | No | No |
| Desired state | Sí | No | No |
| Ejecución local | No | Sí | Sí |
| Estado observado | Recibe y audita | Lee/reporta | Aplica/expone |
| Auditoría | Sí | Reporta datos | No confiable como fuente única |
| UI administrativa | Sí | No | No |
| UI técnica local | No | Sí | No |
| Offline limitado | Coordina | Sí | Según instalación |
| Credenciales permanentes | Define estado | Aplica comando | Almacena/aplica según proveedor |

---

## 8. Comunicación Cloud ↔ Edge

### 8.1 Principio de comunicación

Edge debe iniciar la comunicación hacia Cloud.

Recomendación v1:

```text
Edge → Cloud mediante HTTPS outbound
```

No se debe requerir abrir puertos entrantes del Edge hacia internet.

Beneficios:

- Funciona mejor detrás de routers, NAT o redes residenciales.
- Reduce exposición de seguridad.
- Evita publicar sistemas locales.
- Simplifica instalaciones en condominios.

---

### 8.2 Patrón inicial recomendado

Para v1, usar:

```text
Polling seguro + heartbeat + fetch de comandos
```

Flujo:

```text
1. Edge envía heartbeat.
2. Cloud responde estado general.
3. Edge solicita comandos pendientes.
4. Edge ejecuta comandos localmente.
5. Edge reporta resultado.
6. Cloud actualiza observed state, auditoría e incidentes.
```

WebSocket, streaming o canales persistentes pueden evaluarse después. No deben bloquear el MVP.

---

### 8.3 Endpoints conceptuales Cloud para Edge

Estos son nombres conceptuales, no rutas definitivas.

```text
POST /edge/enroll
POST /edge/heartbeat
GET  /edge/config
GET  /edge/snapshot
GET  /edge/commands
POST /edge/commands/{id}/ack
POST /edge/commands/{id}/result
POST /edge/provider-status
POST /edge/logs
POST /edge/incidents
POST /edge/version
```

Regla:

```text
Edge consume endpoints técnicos.
Usuarios finales no consumen endpoints de Edge directamente, salvo casos controlados de red local futura.
```

---

## 9. Registro y activación de Edge

### 9.1 Flujo de enrollment

Flujo recomendado:

```text
1. CondoAdmin o PlatformSupport crea Edge pendiente en Cloud.
2. Cloud genera enrollment token de corta vida.
3. Técnico instala Edge en sitio.
4. Edge usa enrollment token para registrarse.
5. Cloud valida token, condominio, caseta y proveedor.
6. Edge recibe identidad técnica.
7. Edge genera o almacena credencial técnica local.
8. Cloud marca Edge como CONFIGURED.
9. Se ejecuta prueba de conexión al proveedor.
10. Se ejecuta QA de provider mappings.
11. Cloud marca Edge como ACTIVE.
```

---

### 9.2 Estados de Edge

```text
PENDING_ENROLLMENT
ENROLLED
CONFIGURED
ACTIVE
ONLINE
DEGRADED
OFFLINE
DISABLED
ARCHIVED
```

Descripción:

| Estado | Significado |
|---|---|
| `PENDING_ENROLLMENT` | Edge creado en Cloud, aún no instalado. |
| `ENROLLED` | Edge registrado técnicamente. |
| `CONFIGURED` | Tiene configuración inicial. |
| `ACTIVE` | Puede ejecutar comandos. |
| `ONLINE` | Reporta heartbeat sano. |
| `DEGRADED` | Edge responde, pero proveedor o cola presenta problemas. |
| `OFFLINE` | No hay heartbeat dentro del umbral. |
| `DISABLED` | Deshabilitado manualmente. |
| `ARCHIVED` | Reemplazado o retirado. |

---

### 9.3 Reglas de identidad técnica

Cada Edge debe tener identidad propia.

Debe existir:

```text
edgeNodeId
condominiumId
privateAreaId opcional
guardhouseId opcional
providerInstallationId
edgeVersion
credentialFingerprint
```

Reglas:

- Un Edge no debe poder operar para otro condominio.
- Un Edge no debe descargar configuración de otro condominio.
- Un Edge no debe ejecutar comandos fuera de sus provider installations asignadas.
- Las credenciales técnicas deben poder rotarse.
- La desactivación de Edge debe invalidar su acceso a Cloud.

---

## 10. Edge por condominio, privada o caseta

### 10.1 Modelo base

Un condominio puede tener uno o varios Edge.

Casos:

```text
Condominio pequeño:
- 1 Edge para toda la instalación.

Condominio mediano:
- 1 Edge por caseta principal.

Condominio grande:
- varios Edge por caseta, privada o zona operativa.
```

---

### 10.2 Regla de ownership técnico

Para v1, un recurso físico o instalación de proveedor debe tener un Edge dueño claro.

```text
ProviderInstallation → EdgeNode propietario
```

No debe haber dos Edge activos controlando el mismo recurso físico sin diseño explícito de alta disponibilidad.

Alta disponibilidad multi-Edge queda fuera de v1.

---

### 10.3 Routing de comandos

Cloud debe enrutar comandos según:

```text
Condominium
+ PrivateArea opcional
+ AccessZone
+ Door
+ ProviderInstallation
+ ProviderMapping
+ EdgeNode
```

Si no se puede determinar Edge destino:

```text
command.status = BLOCKED_MAPPING_REQUIRED
incident = COMMAND_ROUTING_FAILED
```

No se debe mandar un comando a un Edge ambiguo.

---

## 11. ProviderInstallation y ProviderMapping

### 11.1 ProviderInstallation

Representa una instalación concreta de proveedor para un condominio, caseta o zona.

Campos conceptuales:

```text
ProviderInstallation
- id
- condominiumId
- privateAreaId opcional
- guardhouseId opcional
- accessProviderId
- edgeNodeId
- displayName
- status
- connectionConfigRef
- capabilitiesSnapshot
- lastValidatedAt
- createdAt
- updatedAt
```

---

### 11.2 ProviderMapping

Mapea conceptos canónicos de Tricor hacia conceptos del proveedor.

Tipos obligatorios iniciales:

```text
ACCESS_PROFILE_MAPPING
ACCESS_ZONE_MAPPING
DOOR_MAPPING
CREDENTIAL_MAPPING
IDENTITY_MAPPING
```

Mappings mínimos para activar proveedor:

```text
ACTIVE_ACCESS_PROFILE
DELINQUENT_ACCESS_PROFILE
REVOKED_ACCESS_PROFILE
PEDESTRIAN_ACCESS_ZONE
VEHICULAR_ACCESS_ZONE
Puertas/zona relevantes
Credenciales soportadas
```

Regla:

```text
Sin mapping validado, no hay activación de proveedor.
```

---

### 11.3 Validación de mappings

Antes de activar una instalación:

```text
1. Probar conexión del Edge con proveedor.
2. Leer capacidades disponibles si el proveedor lo permite.
3. Validar capability matrix.
4. Validar perfiles requeridos.
5. Validar puertas/zona requeridas.
6. Validar credenciales soportadas.
7. Ejecutar QA provider-contract.
8. Aprobar activación.
```

---

## 12. ProviderCapabilityMatrix

### 12.1 Propósito

La matriz de capacidades evita que Tricor muestre o permita reglas que el proveedor no puede cumplir.

Ejemplo:

```text
Si el proveedor no soporta QR invitado:
- no permitir activar QR invitado.
- no permitir regla “bloquear QR invitado por morosidad”.
- QA Harness debe marcar configuración incompatible.
```

---

### 12.2 Capacidades conceptuales

```text
supportsAccessProfiles
supportsDoorMapping
supportsAccessZones
supportsPedestrianZone
supportsVehicularZone
supportsCardOrTagCredentials
supportsResidentQr
supportsGuestQr
supportsTemporaryAccess
supportsManualOpen
supportsObservedStateRead
supportsCredentialRevocation
supportsIdentityArchive
supportsProviderEvents
requiresEdge
supportsCloudDirectFuture
```

---

### 12.3 Reglas

- Cada provider adapter debe declarar capacidades.
- Cada instalación puede ajustar capacidades según hardware/configuración real.
- La UI debe ocultar o deshabilitar configuraciones no soportadas.
- El PolicyConfigValidator debe impedir guardar reglas incompatibles.
- El QA Harness debe probar combinaciones válidas e inválidas.

---

## 13. Flujo principal: pago confirmado → restauración de acceso

### 13.1 Flujo ideal

```text
1. Residente paga por proveedor de pago configurado del condominio.
2. Proveedor de pago confirma el pago.
3. Cloud valida evento/API.
4. Payment status cambia a APPROVED.
5. Debt status cambia a PAID o SETTLED.
6. Policy Engine recalcula estado de propiedad.
7. DesiredAccessState cambia a ACTIVE_ACCESS_PROFILE.
8. Cloud crea AccessCommand RESTORE_ACCESS.
9. Cloud encola comando para Edge destino.
10. Edge obtiene comando.
11. Edge ejecuta operación en proveedor local.
12. Edge reporta resultado.
13. Cloud actualiza ObservedAccessState.
14. Cloud registra auditoría.
15. Tricor Condo, Guard y Resident ven acceso restaurado.
```

---

### 13.2 Si Edge está offline

```text
Pago: confirmado
Deuda: saldada
DesiredAccessState: activo
AccessCommand: pendiente
ObservedAccessState: no confirmado
Motivo: Edge offline
```

Estado recomendado:

```text
PAYMENT_CONFIRMED_ACCESS_PENDING
```

Regla crítica:

```text
No mostrar “acceso restaurado” si el proveedor no lo ha aplicado o Edge no lo ha confirmado.
```

UI sugerida:

```text
Pago confirmado. Restauración de acceso pendiente por conexión del Edge.
```

---

## 14. Flujo principal: morosidad → restricción de acceso

```text
1. Cloud detecta deuda vencida.
2. Aplica días de gracia configurados.
3. Policy Engine evalúa política de morosidad.
4. Propiedad pasa a estado moroso.
5. DesiredAccessState cambia a DELINQUENT_ACCESS_PROFILE.
6. Default recomendado: solo peatonal permitido.
7. Cloud genera AccessCommand APPLY_DELINQUENT_PROFILE.
8. Edge ejecuta en proveedor.
9. Edge reporta resultado.
10. Cloud actualiza ObservedAccessState.
11. Se registra auditoría.
12. Guard y Resident ven estado actualizado según permisos de visibilidad.
```

Si Edge está offline:

```text
DesiredAccessState = DELINQUENT_ACCESS_PROFILE
ObservedAccessState = pendiente/no confirmado
CommandStatus = PENDING_EDGE_OFFLINE
Incident = EDGE_OFFLINE_COMMAND_PENDING
```

---

## 15. Flujo manual fallback: transferencia directa

La transferencia manual existe como escenario secundario y auditado.

```text
1. Residente transfiere fuera de Tricor.
2. Residente sube comprobante.
3. Finance o CondoAdmin revisa.
4. Finance o CondoAdmin aprueba manualmente.
5. Payment status cambia a APPROVED_MANUAL.
6. Policy Engine recalcula estado.
7. Cloud genera comando de restauración si aplica.
8. Edge ejecuta.
9. Cloud audita.
```

Reglas:

- El comprobante no restaura por sí solo.
- Finance no toca permisos directamente.
- Finance aprueba pago manual; el sistema recalcula acceso.
- Toda restauración manual debe quedar auditada.

---

## 16. Desired State, Command y Observed State

### 16.1 DesiredAccessState

Estado que Tricor quiere que exista.

Ejemplos:

```text
ACTIVE_ACCESS_PROFILE
DELINQUENT_ACCESS_PROFILE
REVOKED_ACCESS_PROFILE
TEMPORARY_EXCEPTION_PROFILE
MOVE_OUT_GRACE_PROFILE
```

---

### 16.2 AccessCommand

Instrucción canónica generada por Cloud para Edge/proveedor.

Tipos iniciales:

```text
APPLY_ACCESS_PROFILE
REVOKE_ACCESS
RESTORE_ACCESS
SUSPEND_CREDENTIAL
REVOKE_CREDENTIAL
MARK_CREDENTIAL_LOST
APPLY_TEMPORARY_EXCEPTION
EXPIRE_TEMPORARY_EXCEPTION
SYNC_PROVIDER_STATE
```

---

### 16.3 ObservedAccessState

Estado observado/reportado tras ejecución.

Ejemplos:

```text
APPLIED_CONFIRMED
APPLIED_PARTIAL
PENDING_EDGE_OFFLINE
PENDING_PROVIDER_CONFIRMATION
FAILED_PROVIDER_ERROR
FAILED_MAPPING_REQUIRED
FAILED_CAPABILITY_UNSUPPORTED
UNKNOWN
```

---

### 16.4 Regla de honestidad operativa

El sistema debe distinguir entre:

```text
Queremos que esté restaurado.
Ya fue aplicado en proveedor.
```

No se deben mezclar.

---

## 17. Command Queue

### 17.1 Cola Cloud

Cloud mantiene la cola fuente de comandos.

Campos conceptuales:

```text
AccessCommand
- id
- condominiumId
- privateAreaId opcional
- propertyId opcional
- credentialId opcional
- desiredAccessStateId
- commandType
- edgeNodeId
- providerInstallationId
- idempotencyKey
- desiredVersion
- status
- attempts
- lastError
- createdAt
- updatedAt
```

---

### 17.2 Cola local Edge

Edge mantiene copia local de comandos recibidos y resultados pendientes de reportar.

Campos conceptuales:

```text
EdgeLocalCommand
- cloudCommandId
- edgeNodeId
- providerInstallationId
- commandType
- payloadCanonical
- payloadProvider
- status
- attempts
- lastAttemptAt
- lastError
- receivedAt
- appliedAt opcional
- reportedAt opcional
```

---

### 17.3 Estados de comando

```text
CREATED
QUEUED_FOR_EDGE
FETCHED_BY_EDGE
ACKNOWLEDGED_BY_EDGE
APPLYING
APPLIED_CONFIRMED
APPLIED_PARTIAL
FAILED_RETRYABLE
FAILED_FINAL
BLOCKED_MAPPING_REQUIRED
BLOCKED_CAPABILITY_UNSUPPORTED
PENDING_EDGE_OFFLINE
CANCELLED
SUPERSEDED
```

---

### 17.4 Idempotencia

Cada comando debe tener `idempotencyKey`.

Reglas:

- Ejecutar dos veces el mismo comando no debe duplicar efectos.
- Si Edge recibe comando repetido, debe reconocerlo y reportar estado existente.
- Si Cloud reintenta, debe mantener el mismo `idempotencyKey`.
- Si un comando queda obsoleto por un nuevo desired state, debe marcarse `SUPERSEDED`.

---

### 17.5 Reintentos

Regla recomendada:

```text
Errores temporales → retry con backoff.
Errores de mapping → bloquear y crear incidente.
Errores de capability → bloquear y crear incidente.
Errores de autenticación proveedor → DEGRADED + incidente.
Errores permanentes → FAILED_FINAL.
```

---

## 18. Snapshots locales

### 18.1 Propósito

El snapshot local permite que Edge tenga un subconjunto mínimo de información para operar de forma limitada si pierde comunicación con Cloud.

Snapshot no es base de datos principal.

---

### 18.2 Contenido permitido

El snapshot debe ser mínimo.

Puede contener:

```text
- versión de configuración
- condominio asignado
- caseta/zona asignada
- provider mappings activos
- perfiles de acceso activos
- credenciales autorizadas mínimas
- tokens QR válidos o reglas de validación offline si aplica
- estado operativo mínimo de propiedades relevantes
- flags de política necesarios para validar acceso local
```

Debe evitar contener datos innecesarios:

```text
- historial completo de pagos
- comprobantes
- documentos
- información financiera detallada
- datos de otros condominios
- datos de privadas no asignadas al Edge
```

---

### 18.3 Versión de snapshot

Cada snapshot debe tener versión.

```text
snapshotVersion
policyVersion
mappingVersion
issuedAt
expiresAt opcional
```

Reglas:

- Edge reporta `localSnapshotVersion` en heartbeat.
- Cloud puede ordenar resync si detecta versión vieja.
- Snapshot vencido no debe usarse para operaciones sensibles si la política lo prohíbe.

---

## 19. Modo offline

### 19.1 Cuando Edge pierde internet

Edge debe:

```text
1. Seguir registrando eventos locales.
2. Mantener cola local.
3. Operar con último snapshot válido, si está habilitado.
4. No recibir nuevos comandos de Cloud.
5. No marcar comandos nuevos como aplicados.
6. Intentar reconexión.
7. Reportar backlog al reconectar.
```

Cloud debe:

```text
1. Marcar Edge como OFFLINE.
2. Crear incidente operativo.
3. Mantener comandos pendientes.
4. Mostrar estado en Tricor Platform y Tricor Condo.
5. Evitar prometer que un cambio ya fue aplicado.
```

---

### 19.2 Operaciones permitidas offline

Dependen de configuración y capacidad local.

Permitidas de forma controlada:

```text
- Continuar con estado ya aplicado en proveedor.
- Registrar eventos locales.
- Reintentar operaciones locales ya recibidas antes de perder internet.
- Validar QR offline solo si snapshot y política lo permiten.
```

No permitidas:

```text
- Aprobar pagos.
- Recalcular morosidad.
- Crear nuevas reglas.
- Registrar nuevas credenciales permanentes.
- Asumir restauraciones nuevas no recibidas.
- Aplicar comandos que Cloud no haya emitido.
```

---

### 19.3 QR offline

QR offline es opcional y requiere diseño cuidadoso.

Condiciones mínimas:

```text
qr.offlineValidationWithEdgeSnapshot = true
edge.offlineSnapshot.enabled = true
snapshot no vencido
Guard/lector puede consultar Edge local o proveedor local compatible
QR firmado/verificable
```

Si no se cumplen condiciones:

```text
QR no debe validarse offline.
```

---

### 19.4 Reconciliación al reconectar

Al reconectar:

```text
1. Edge envía heartbeat.
2. Edge envía eventos locales pendientes.
3. Edge envía resultados de comandos aplicados.
4. Cloud actualiza observed state.
5. Cloud envía comandos pendientes nuevos.
6. Edge ejecuta cola pendiente.
7. Cloud cierra o actualiza incidentes.
8. QA/monitoring valida consistencia.
```

---

## 20. UI local técnica de Edge

### 20.1 Aprobación

Edge tendrá UI local mínima técnica.

Propósito:

```text
Diagnóstico y soporte técnico.
```

No propósito:

```text
Administración de condominio.
```

---

### 20.2 Acceso

Recomendación v1:

```text
http://localhost:<puerto>
```

Opcional futuro:

```text
Acceso LAN restringido solo con habilitación explícita de soporte.
```

---

### 20.3 Funciones permitidas

```text
- Estado del Edge.
- Versión instalada.
- Estado de conexión a Cloud.
- Estado de conexión a proveedor.
- Último heartbeat.
- Última sincronización.
- Snapshot actual.
- Cola pendiente.
- Últimos comandos.
- Logs recientes.
- Prueba de conexión local al proveedor.
- Reinicio controlado del servicio.
- Exportación de paquete diagnóstico.
```

---

### 20.4 Funciones prohibidas

```text
- Crear residentes.
- Editar propiedades.
- Aprobar pagos.
- Registrar nuevas credenciales permanentes.
- Cambiar reglas de morosidad.
- Cambiar perfiles de acceso permanentes.
- Ejecutar comandos arbitrarios no emitidos por Cloud.
- Administrar dinero.
```

---

## 21. Seguridad

### 21.1 Principios

```text
Mínimo privilegio.
Identidad por Edge.
Sin exposición innecesaria.
Auditoría completa.
Secretos protegidos.
Datos mínimos en Edge.
```

---

### 21.2 Credenciales técnicas

Edge debe usar credenciales técnicas separadas de usuarios humanos.

Reglas:

- Enrollment token de corta vida.
- Credential/token técnico después de enrollment.
- Rotación posible.
- Revocación inmediata desde Cloud.
- Scoping por condominio y provider installation.
- No reutilizar credenciales entre Edge.

---

### 21.3 Secretos de proveedor

Secretos locales del proveedor deben almacenarse de forma protegida.

Reglas:

- No hardcodear secretos.
- No imprimir secretos en logs.
- No subir secretos en reportes diagnósticos.
- Referenciar secretos mediante `connectionConfigRef`.
- Permitir rotación de credenciales.

---

### 21.4 Datos personales en Edge

Edge debe almacenar datos mínimos.

Permitido si es necesario:

- Identificadores canónicos.
- Estado de acceso mínimo.
- Credenciales técnicas de acceso.
- Snapshot de validación.

Evitar:

- Historial financiero completo.
- Comprobantes.
- Archivos.
- Información de otros condominios.
- Información no necesaria para ejecutar acceso.

---

## 22. Observabilidad y monitoreo

### 22.1 Heartbeat

Edge debe reportar heartbeat periódico.

Campos sugeridos:

```text
edgeNodeId
status
providerConnectionStatus
pendingCommandCount
failedCommandCount
localSnapshotVersion
edgeVersion
cpu/memory opcional
lastProviderCheckAt
receivedAt
```

---

### 22.2 Salud global en Tricor Platform

Tricor Platform debe mostrar:

```text
- Edge online/offline por condominio.
- Edge degradados.
- Proveedores con falla.
- Comandos pendientes.
- Incidentes abiertos.
- Versiones Edge instaladas.
- Última sincronización.
```

---

### 22.3 Salud en Tricor Condo

Tricor Condo debe mostrar solo su alcance:

```text
- Estado de Edge del condominio.
- Casetas afectadas.
- Proveedor conectado/desconectado.
- Comandos pendientes relevantes.
- Incidentes operativos.
```

No debe mostrar información de otros clientes.

---

### 22.4 Estados técnicos recomendados

```text
EDGE_OFFLINE
EDGE_DEGRADED
PROVIDER_DISCONNECTED
PROVIDER_AUTH_FAILED
MAPPING_INVALID
COMMAND_QUEUE_BACKLOG
SNAPSHOT_STALE
COMMAND_FAILED
OBSERVED_STATE_MISMATCH
UNKNOWN_PROVIDER_OBJECT
```

---

## 23. Incidentes operativos

### 23.1 Incidentes automáticos

Crear incidente cuando:

```text
- Edge queda offline.
- Edge vuelve online después de caída.
- Proveedor no responde.
- Mappings requeridos faltan.
- Comando falla definitivamente.
- Hay comandos pendientes por demasiado tiempo.
- Estado observado difiere del desired state.
- Edge reporta credencial/persona desconocida en proveedor.
- Snapshot local está vencido.
```

---

### 23.2 Severidad

```text
INFO
LOW
MEDIUM
HIGH
CRITICAL
```

Ejemplos:

```text
INFO: Edge reconectó.
LOW: Credencial sin uso detectada.
MEDIUM: Comando pendiente por Edge offline.
HIGH: Proveedor desconectado en caseta activa.
CRITICAL: Múltiples Edge offline o proveedor principal caído.
```

---

### 23.3 Cierre de incidentes

Incidentes pueden cerrarse:

```text
- automáticamente si condición se resuelve.
- por soporte técnico con motivo.
- por CondoAdmin si corresponde a operación local.
```

Todo cierre debe auditarse.

---

## 24. Reconciliación proveedor ↔ Tricor

### 24.1 Propósito

La reconciliación detecta diferencias entre lo que Tricor cree que debe existir y lo que el proveedor realmente tiene.

Ejemplos:

```text
- Credencial activa en proveedor pero revocada en Tricor.
- Persona en proveedor sin vínculo canónico.
- Perfil de acceso distinto al desired state.
- Puerta/zona no mapeada.
- Credencial obsoleta de exresidente.
```

---

### 24.2 Flujo

```text
1. Cloud solicita reconciliación.
2. Edge consulta proveedor.
3. Edge normaliza estado observado.
4. Cloud compara contra desired state.
5. Cloud genera diferencias.
6. Cloud crea incidentes o comandos correctivos.
7. CondoAdmin/PlatformSupport revisa si aplica.
```

---

### 24.3 Reglas

- Reconciliación no debe eliminar datos sin aprobación o política explícita.
- Exresidentes con credenciales activas sí pueden disparar revocación automática si la política lo permite.
- Credenciales sin uso solo generan alerta no invasiva por default.
- Objetos desconocidos del proveedor deben ir a revisión.

---

## 25. Guard, Resident y Edge

### 25.1 Tricor Guard

Tricor Guard no debe administrar Edge.

Puede interactuar indirectamente con Edge mediante Cloud o, en futuro controlado, validación local.

Funciones relacionadas:

```text
- Ver estado permitido/suspendido.
- Leer QR.
- Registrar eventos.
- Apertura manual justificada por GuardSupervisor.
- Excepción temporal justificada por GuardSupervisor, con límites y auditoría.
- Reportar falla de pluma/proveedor.
```

---

### 25.2 Apertura manual

Apertura manual es un evento único.

Reglas:

- Solo GuardSupervisor.
- Motivo obligatorio.
- Registro automático de hora, usuario, puerta/caseta y resultado.
- No cambia permisos permanentes.
- Debe generar auditoría.

Motivos sugeridos:

```text
QR válido, pluma no respondió
Falla de proveedor
Emergencia
Autorizado por administración
Otro
```

---

### 25.3 Excepción temporal

Excepción temporal es una regla limitada por tiempo.

Reglas:

- GuardSupervisor puede crearla si la política lo permite.
- Motivo obligatorio.
- Duración máxima configurable.
- Notificación a CondoAdmin.
- Auditoría completa.
- Expiración automática.

No debe convertirse en bypass permanente.

---

### 25.4 Tricor Resident

Tricor Resident no debe hablar directamente con Edge.

Flujo normal:

```text
Resident → Cloud → Desired State / QR / Pagos → Edge cuando aplique
```

El residente puede:

- Ver estado de cuenta.
- Ver estado de acceso.
- Pagar por proveedor de pago configurado.
- Subir comprobante si aplica.
- Generar QR de invitado si la política lo permite.
- Bloquear tarjeta/tag propia por pérdida.

El residente no puede:

- Registrar nuevas credenciales permanentes.
- Editar proveedor.
- Editar reglas.
- Aprobar pagos.

---

## 26. Storage local de Edge

### 26.1 Recomendación

Edge debe tener almacenamiento local ligero para:

```text
- cola local
- snapshot local
- logs técnicos
- resultados pendientes
- configuración técnica cacheada
```

Este storage puede implementarse con una base local embebida o mecanismo equivalente.

Regla:

```text
Storage local de Edge no es fuente de verdad.
```

---

### 26.2 Retención local

Configurar retención para evitar crecimiento ilimitado.

```text
logs recientes: retención limitada
comandos aplicados: retención limitada
snapshots anteriores: conservar última versión válida y versión previa
paquetes diagnósticos: limpieza automática
```

---

## 27. Versionado y actualizaciones

### 27.1 Versionado de Edge

Cada Edge reporta versión.

```text
edgeVersion
adapterVersion
configVersion
policyVersion
mappingVersion
snapshotVersion
```

---

### 27.2 Compatibilidad

Cloud debe saber si una versión Edge es compatible con la configuración activa.

Si no es compatible:

```text
Edge status = DEGRADED
Incident = EDGE_VERSION_INCOMPATIBLE
Bloquear comandos no soportados
```

---

### 27.3 Actualizaciones

v1 puede iniciar con actualización manual controlada.

Futuro:

```text
- canal stable
- canal staging
- rollout por condominio
- rollback controlado
```

No es obligatorio automatizar actualizaciones desde el primer MVP.

---

## 28. Despliegue local

### 28.1 Recomendación técnica v1

Edge debe desplegarse como servicio local.

Opciones válidas:

```text
- Docker container
- Servicio Node.js/TypeScript con systemd o equivalente
```

Recomendación inicial:

```text
Docker para consistencia de instalación.
Servicio local controlado para ambientes donde Docker no sea viable.
```

---

### 28.2 Requisitos mínimos conceptuales

```text
- Equipo local en caseta o red del condominio.
- Acceso de red al proveedor local.
- Salida HTTPS hacia Tricor Cloud.
- Persistencia local mínima.
- Hora del sistema sincronizada.
- Credenciales técnicas protegidas.
```

---

### 28.3 Instalación por soporte

Proceso:

```text
1. Crear Edge en Tricor Condo/Platform.
2. Generar enrollment token.
3. Instalar runtime.
4. Configurar variables mínimas.
5. Ejecutar enrollment.
6. Probar conexión Cloud.
7. Probar conexión proveedor.
8. Validar mappings.
9. Ejecutar QA de Edge.
10. Activar Edge.
```

---

## 29. Seguridad de red

### 29.1 No exposición entrante

Regla recomendada:

```text
No abrir puertos del Edge hacia internet.
```

Edge debe salir hacia Cloud.

---

### 29.2 UI local

La UI local técnica no debe exponerse públicamente.

Reglas:

- Solo localhost por default.
- LAN restringida solo si soporte lo habilita explícitamente.
- Autenticación técnica si se expone en LAN.
- Logs sin secretos.

---

### 29.3 Proveedor local

El proveedor debe estar accesible solo desde la red local necesaria.

No se debe exponer el proveedor directamente a internet como estrategia de integración.

---

## 30. Estados de salud

### 30.1 EdgeHealthStatus

```text
HEALTHY
DEGRADED
OFFLINE
DISABLED
UNKNOWN
```

---

### 30.2 ProviderConnectionStatus

```text
CONNECTED
DISCONNECTED
AUTH_FAILED
TIMEOUT
DEGRADED
UNKNOWN
```

---

### 30.3 SyncStatus

```text
IN_SYNC
PENDING_COMMANDS
STALE_SNAPSHOT
RECONCILIATION_REQUIRED
FAILED
```

---

## 31. Eventos de dominio relacionados

Eventos recomendados:

```text
EdgeEnrolled
EdgeActivated
EdgeDisabled
EdgeWentOffline
EdgeCameOnline
EdgeHeartbeatReceived
EdgeSnapshotSynced
EdgeCommandQueued
EdgeCommandFetched
EdgeCommandApplied
EdgeCommandFailed
EdgeCommandSuperseded
ProviderConnectionFailed
ProviderConnectionRestored
ProviderMappingValidated
ProviderMappingInvalidated
ObservedStateMismatchDetected
ReconciliationRequested
ReconciliationCompleted
ManualOpenRequested
ManualOpenApplied
TemporaryExceptionCreated
TemporaryExceptionExpired
```

Estos eventos deben alimentar auditoría, incidentes, reportes y QA Harness.

---

## 32. QA Harness requerido

El QA Harness debe probar Cloud + Edge desde el inicio.

### 32.1 Suites obligatorias

```text
qa:edge:health
qa:edge:enrollment
qa:edge:heartbeat
qa:edge:command-queue
qa:edge:offline
qa:edge:reconnect
qa:edge:snapshot
qa:edge:provider-connection
qa:edge:provider-contract
qa:edge:mapping-validation
qa:edge:reconciliation
qa:edge:security-boundaries
```

---

### 32.2 Pruebas mínimas

```text
1. Edge puede registrarse con token válido.
2. Edge no puede registrarse con token vencido.
3. Edge no puede acceder a otro condominio.
4. Heartbeat actualiza estado ONLINE.
5. Ausencia de heartbeat marca OFFLINE.
6. Cloud genera comando y Edge lo obtiene.
7. Edge aplica comando en provider mock.
8. Edge reporta APPLIED_CONFIRMED.
9. Pago confirmado con Edge offline queda ACCESS_PENDING.
10. Edge reconecta y ejecuta comando pendiente.
11. Mapping faltante bloquea comando.
12. Capability no soportada bloquea configuración.
13. Comando duplicado no duplica efecto.
14. Comando viejo queda SUPERSEDED por desired state nuevo.
15. Provider disconnected crea incidente.
16. Reconciliación detecta credencial huérfana.
17. UI local no permite administración de residentes/pagos.
18. Logs no contienen secretos.
19. QR offline solo funciona con snapshot válido y política activa.
20. Múltiples Edge enrutan comandos al Edge correcto.
```

---

### 32.3 Provider mock

QA Harness debe incluir provider mock para:

```text
- perfiles de acceso
- puertas
- zonas
- credenciales
- revocación
- restauración
- errores temporales
- errores permanentes
- provider offline
- estado observado
```

No se debe depender de hardware real para pruebas diarias.

---

### 32.4 Smoke test con proveedor real

Debe existir, pero no ser parte de cada corrida normal.

```text
qa:edge:provider-real-smoke
```

Debe ejecutarse solo en ambiente controlado.

---

## 33. Estructura de repo recomendada

```text
tricor-habitat/
├── apps/
│   ├── api/              # Cloud API
│   ├── web/              # Platform, Condo, Guard, Resident
│   ├── edge/             # Edge Agent local
│   └── qa/               # QA Harness CLI
├── packages/
│   ├── contracts/        # contratos canónicos
│   ├── domain/           # reglas puras
│   ├── db/               # Prisma schema/client
│   ├── provider-sdk/     # interfaces de provider adapter
│   ├── edge-sdk/         # cliente Edge/Cloud, tipos compartidos
│   ├── config/           # env, lint, tsconfig
│   └── ui/               # componentes compartidos
└── docs/
    ├── PROJECT_CHARTER_TRICOR_HABITAT.md
    ├── DOMAIN_MODEL_V0.1.md
    ├── ACCESS_POLICY_CONFIGURATION_V0.1.md
    ├── CLOUD_EDGE_ARCHITECTURE_V0.1.md
    └── QA_HARNESS_SPEC_V0.1.md
```

---

## 34. Interface conceptual de Provider Adapter

No es implementación final. Es contrato conceptual.

```ts
interface AccessProviderAdapter {
  providerCode: string;

  getCapabilities(): Promise<ProviderCapabilities>;

  testConnection(configRef: string): Promise<ProviderConnectionResult>;

  validateMappings(input: ValidateMappingsInput): Promise<MappingValidationResult>;

  applyAccessProfile(command: ApplyAccessProfileCommand): Promise<ProviderCommandResult>;

  revokeCredential(command: RevokeCredentialCommand): Promise<ProviderCommandResult>;

  restoreCredential(command: RestoreCredentialCommand): Promise<ProviderCommandResult>;

  readObservedState(input: ReadObservedStateInput): Promise<ObservedStateResult>;

  reconcile(input: ReconciliationInput): Promise<ReconciliationResult>;
}
```

Reglas:

- El adapter no decide si algo debe aplicarse.
- El adapter solo traduce y ejecuta.
- El adapter debe devolver errores normalizados.
- El adapter debe ser testeable con QA Harness.

---

## 35. Interface conceptual Edge ↔ Cloud

```ts
interface EdgeCloudClient {
  enroll(input: EdgeEnrollInput): Promise<EdgeEnrollResult>;

  heartbeat(input: EdgeHeartbeatInput): Promise<EdgeHeartbeatResult>;

  fetchConfig(): Promise<EdgeConfig>;

  fetchSnapshot(): Promise<EdgeSnapshot>;

  fetchCommands(): Promise<EdgeCommand[]>;

  ackCommand(commandId: string): Promise<void>;

  reportCommandResult(result: EdgeCommandResult): Promise<void>;

  reportProviderStatus(status: ProviderStatusReport): Promise<void>;

  reportIncident(incident: EdgeIncidentReport): Promise<void>;

  uploadDiagnosticBundle(bundle: DiagnosticBundle): Promise<void>;
}
```

---

## 36. Reglas de implementación para Claude Code CLI

Cuando se implemente esta arquitectura:

1. No convertir Edge en fuente de verdad.
2. No calcular pagos en Edge.
3. No calcular morosidad en Edge.
4. No permitir administración local de residentes desde Edge.
5. No exponer proveedor local a internet.
6. No ejecutar cambios directos contra proveedor sin `AccessCommand`.
7. No activar proveedor sin capability matrix y mappings validados.
8. No marcar acceso como restaurado sin observed state confirmado.
9. No introducir provider-specific terms en dominio central.
10. No agregar configuración nueva sin validación en `PolicyConfigValidator`.
11. No agregar comando nuevo sin idempotency key.
12. No agregar flujo Edge sin QA Harness.
13. No registrar secretos en logs.
14. No mezclar datos de condominios en Edge.
15. No romper documentos fuente de verdad.

Cada PR relacionado con Edge debe incluir:

```text
- qué flujo modifica
- qué comandos agrega/cambia
- qué estados agrega/cambia
- qué pruebas QA se agregaron
- qué provider capability requiere
- qué impacto tiene en offline mode
- qué impacto tiene en auditoría
```

---

## 37. Criterios de aceptación v0.1

La arquitectura Cloud + Edge se considera aceptable para iniciar implementación cuando:

```text
1. Existe Edge enrollment seguro.
2. Edge reporta heartbeat.
3. Cloud detecta Edge offline.
4. Cloud genera comandos canónicos.
5. Edge obtiene comandos mediante canal outbound.
6. Edge ejecuta contra provider mock.
7. Edge reporta resultado.
8. Cloud actualiza observed state.
9. Cloud registra auditoría.
10. Edge offline deja comandos pendientes.
11. Edge reconecta y procesa cola.
12. Mappings requeridos bloquean configuración inválida.
13. Capabilities bloquean reglas no soportadas.
14. UI local técnica no permite administración.
15. QA Harness cubre flujos principales.
```

---

## 38. Riesgos principales

| Riesgo | Impacto | Mitigación |
|---|---|---|
| Edge offline frecuente | Accesos pendientes | Heartbeat, incidentes, modo offline limitado, soporte técnico. |
| Mappings incorrectos | Permisos mal aplicados | QA provider-contract, validación previa, auditoría. |
| Proveedor no soporta capacidad | Configuración imposible | ProviderCapabilityMatrix y bloqueo de UI. |
| Comandos duplicados | Cambios inconsistentes | Idempotency keys. |
| Credenciales obsoletas | Acceso de exresidentes | Reconciliación y CredentialLifecycleAuditJob. |
| Secretos expuestos | Riesgo de seguridad | No logs de secretos, storage protegido, rotación. |
| Edge usado como backend local | Desorden arquitectónico | Límites estrictos y QA. |
| Offline mal comunicado | Usuarios creen que acceso ya cambió | Estados honestos: desired vs observed. |

---

## 39. Roadmap técnico Cloud + Edge

### Fase 1 — Contratos

```text
- EdgeNode
- ProviderInstallation
- ProviderMapping
- ProviderCapabilityMatrix
- AccessCommand
- DesiredAccessState
- ObservedAccessState
```

### Fase 2 — Edge mock

```text
- Edge fake para QA
- Provider mock
- Command queue básica
- Heartbeat simulado
```

### Fase 3 — Edge real mínimo

```text
- Enrollment
- Heartbeat
- Fetch commands
- Report result
- Local logs
- UI local técnica mínima
```

### Fase 4 — Provider adapter v1

```text
- Conexión proveedor
- Capability matrix
- Mapping validator
- Apply profile
- Revoke/restore credential
- Read observed state si aplica
```

### Fase 5 — Offline y reconciliación

```text
- Snapshot local
- Edge offline
- Reconnect
- Cola local
- Reconciliación
- Incidentes automáticos
```

### Fase 6 — Operación piloto

```text
- Monitoreo Platform
- Monitoreo Condo
- Diagnóstico Edge
- Smoke test proveedor real
- Manual técnico de instalación
```

---

## 40. Manuales derivados

Este documento debe alimentar futuros manuales:

```text
MANUAL_TECNICO_EDGE_INSTALLATION.md
MANUAL_PLATFORM_EDGE_MONITORING.md
MANUAL_CONDO_PROVIDER_SETUP.md
MANUAL_SUPPORT_EDGE_DIAGNOSTICS.md
RUNBOOK_EDGE_OFFLINE.md
RUNBOOK_PROVIDER_FAILURE.md
```

---

## 41. Resumen de decisiones

```text
Cloud es fuente de verdad.
Edge ejecuta y observa.
Proveedor aplica físicamente.
Edge se comunica por salida HTTPS hacia Cloud.
No se exponen puertos de Edge a internet.
Edge tiene UI local técnica mínima.
Edge no administra residentes, pagos ni permisos permanentes.
Provider mappings son obligatorios.
ProviderCapabilityMatrix es obligatoria.
Sin mapping/capability válido, no hay activación.
Modo offline es limitado y honesto.
Pago confirmado con Edge offline queda acceso pendiente.
Comandos deben ser idempotentes.
QA Harness debe probar Edge, provider mock, offline, reconexión, mappings y capabilities.
```

---

## 42. Siguiente documento recomendado

Después de este documento, el siguiente documento formal recomendado es:

```text
QA_HARNESS_SPEC_V0.1.md
```

Ese documento debe convertir las reglas de Project Charter, Domain Model, Access Policy Configuration y Cloud/Edge Architecture en pruebas obligatorias, comandos CLI, mocks, reportes y criterios de aceptación.

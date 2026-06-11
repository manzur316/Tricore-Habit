# PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md

**Proyecto:** Tricor Hábitat  
**Documento:** Provider ZKTeco / CVSecurity Adapter Spec  
**Versión del documento:** v0.1  
**Estado documental:** CERTIFIED como especificación base basada en manual PDF v1.2 / 2025  
**Estado de implementación:** BLOCKED hasta laboratorio real  
**Proveedor objetivo:** ZKTeco / ZKBio CVSecurity  
**Modo de conexión v1:** Edge local obligatorio  
**Archivo autoritativo:** este documento reemplaza cualquier versión previa del mismo nombre  

---

## 0. Veredicto de fuente

**MATCH.** El PDF `ZKBio CVSecurity 3rd Party API User Manual`, versión 1.2, fecha noviembre 2025, para ZKBio CVSecurity 6.0.0 o superior, sí es la fuente correcta para diseñar el adapter ZKTeco / CVSecurity de Tricor Hábitat.

La fuente cubre:

```text
- autorización API por access_token
- formato general de respuesta
- personas
- departamentos
- tarjetas
- biotemplates
- dispositivos de control de acceso
- puertas
- lectores
- niveles/perfiles de acceso
- transacciones
- entrance control / gates
- visitor management / QR
- parking
- elevator
- error codes
```

### 0.1 Resultado de auditoría

```text
Fuente PDF: MATCH
Archivo objetivo: PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md
Acción realizada: reescritura controlada del mismo archivo
Archivos adicionales modificados: ninguno
Estado final del archivo: CERTIFIED para documentación base
Estado de implementación: BLOCKED hasta laboratorio
```

---

## 1. Propósito

Este documento define la especificación técnica del adapter ZKTeco / CVSecurity dentro de Tricor Hábitat.

El adapter traduce contratos canónicos de Tricor hacia endpoints reales de ZKBio CVSecurity, sin contaminar el dominio principal con detalles del proveedor.

```text
Tricor Domain / Policy Engine
→ DesiredAccessState
→ AccessCommand
→ Edge local
→ ZKTeco CVSecurity Adapter
→ ZKBio CVSecurity API
→ ProviderOperationResult
→ ObservedAccessState
→ Auditoría
```

El adapter no decide reglas de negocio. El adapter solo ejecuta comandos ya decididos por Tricor.

---

## 2. Principios arquitectónicos

```text
Cloud = fuente de verdad
Edge = ejecutor local
Provider Adapter = traductor técnico
CVSecurity = sistema externo de control de acceso
```

Reglas duras:

```text
1. Tricor Web nunca llama directamente a CVSecurity.
2. Tricor Cloud no requiere acceso directo a la red local del condominio.
3. Edge ejecuta operaciones locales contra CVSecurity.
4. El dominio Tricor no conoce endpoints, access_token, PIN, levelIds, gateIds, doorIds ni nombres de payloads del proveedor.
5. Toda operación pasa por AccessCommand.
6. Toda operación produce ProviderOperationResult.
7. Todo cambio relevante produce auditoría.
8. UNKNOWN nunca se expone como capacidad disponible.
```

---

## 3. Fuente técnica oficial

Fuente usada para esta versión:

```text
ZKBio CVSecurity 3rd Party API User Manual
Version: 1.2
Date: November 2025
Software Version: ZKBio CVSecurity 6.0.0 or above
```

Características confirmadas por la fuente:

```text
- API de terceros para conexión de datos con plataformas externas.
- Estilo HTTP/REST.
- Soporta HTTPS.
- Requiere activar licencia/API Authorization.
- Usa access_token para validar permisos de solicitud.
- Estructura general de respuesta: { code, message, data }.
```

### 3.1 Inconsistencias de la fuente que deben manejarse

El manual tiene inconsistencias que el adapter debe absorber de forma segura:

```text
1. La sección general dice que code < 0 es fallo y code > 0 es éxito, pero los ejemplos muestran code = 0 como success.
2. Algunos endpoints de transacciones muestran acc_token en vez de access_token.
3. Algunos títulos contienen errores tipográficos, por ejemplo api/psgdDvice/list, aunque el URL mostrado usa /api/psgDevice/list.
4. Algunas etiquetas dicen Post Request URL aunque el método real sea GET.
5. Algunas capacidades dependen de licencia, módulo habilitado, hardware y configuración local.
```

Decisión de adapter:

```text
- successCodes debe ser configurable.
- Default inicial: [0].
- No asumir que >0 es éxito hasta laboratorio.
- En endpoints con acc_token vs access_token, probar ambos solo en laboratorio controlado; en código base preferir access_token salvo endpoint probado.
```

---

## 4. Alcance v1 del adapter

El adapter v1 debe cubrir el flujo comercial central:

```text
pago / morosidad
→ recalcular acceso
→ aplicar perfil externo
→ validar estado observado
→ auditar
```

Operaciones prioritarias:

```text
1. Validar instalación local.
2. Validar token/API license.
3. Leer capacidades indirectas desde endpoints seguros.
4. Leer personas/tarjetas/perfiles/puertas/gates/lectores.
5. Crear o actualizar persona técnica externa.
6. Asociar credenciales tipo tarjeta/tag cuando aplique.
7. Aplicar perfil activo.
8. Aplicar perfil moroso/restringido.
9. Revocar credencial perdida si la operación es granular y segura.
10. Revocar exresidentes al terminar gracia.
11. Leer transacciones/eventos.
12. Leer estados de puerta/gate.
13. Ejecutar reconciliación.
```

Fuera del alcance v1:

```text
- Facturación o pagos dentro del adapter.
- Vehículos como entidad Tricor.
- Parking como módulo core v1.
- Elevator como módulo core v1.
- Biometría como requisito del flujo comercial.
- Face recognition como dependencia v1.
- Time & Attendance.
- Offline/Online Consumption.
- Space Management.
- Smart Video Surveillance.
- Intrusion Alarm.
- Operación administrativa desde Edge UI.
```

---

## 5. ProviderKey y ConnectionMode

```ts
type ProviderKey = 'ZKTECO_CVSECURITY';
type ProviderConnectionMode = 'EDGE_LOCAL';
```

Decisión:

```text
ZKTeco / CVSecurity requiere Edge local en v1.
```

Motivo:

```text
CVSecurity normalmente vive dentro de la red local o servidor técnico del condominio/caseta. Tricor Cloud no debe depender de puertos expuestos ni acceso directo a la red local.
```

---

## 6. Autenticación y transporte

### 6.1 Base URL

Formato base:

```text
http(s)://{serverIP}:{serverPort}/api/...
```

Parámetros base del manual:

```text
serverIP = IP del servidor/computadora donde corre ZKBio CVSecurity
serverPort = puerto de CVSecurity, ejemplo 8088
access_token = token API generado en API Authorization
```

### 6.2 access_token

Reglas:

```text
1. Todas las solicitudes deben pasar access_token válido.
2. access_token nunca se guarda en texto plano.
3. access_token nunca aparece en logs.
4. access_token nunca aparece en reportes QA.
5. Edge debe resolverlo vía secretRef.
6. Cambios de token se auditan.
```

### 6.3 Response parser

Respuesta general:

```json
{
  "code": 0,
  "message": "string",
  "data": {}
}
```

Parser canónico:

```ts
type ZktecoApiResponse<T> = {
  code: number;
  message?: string;
  msg?: string;
  data?: T;
};
```

Reglas:

```text
- successCodes default = [0].
- msg y message deben normalizarse.
- data puede ser objeto, array, string, null o wrapper paginado.
- code no reconocido debe producir PROVIDER_REJECTED o UNKNOWN_PROVIDER_ERROR.
```

---

## 7. Configuración de instalación

```ts
type ZktecoCvsecurityProviderInstallation = {
  id: string;
  condominiumId: string;
  privateAreaId?: string;
  edgeNodeId: string;
  providerKey: 'ZKTECO_CVSECURITY';
  connectionMode: 'EDGE_LOCAL';
  displayName: string;
  status:
    | 'DRAFT'
    | 'PENDING_VALIDATION'
    | 'ACTIVE'
    | 'DEGRADED'
    | 'OFFLINE'
    | 'SUSPENDED'
    | 'ARCHIVED';
  configRef: string;
  capabilitiesVersion: string;
  mappingVersion: string;
  lastHealthCheckAt?: string;
  lastSuccessfulSyncAt?: string;
  createdAt: string;
  updatedAt: string;
};
```

```ts
type ZktecoCvsecurityConnectionConfig = {
  baseUrl: string;
  serverIp?: string;
  serverPort?: number;
  useHttps: boolean;
  accessTokenSecretRef: string;
  requestTimeoutMs: number;
  retryPolicy: {
    maxAttempts: number;
    baseDelayMs: number;
    maxDelayMs: number;
  };
  rateLimit: {
    minMonitorIntervalMs: number;
    maxRequestsPerMinute?: number;
  };
  tls: {
    verifyCertificate: boolean;
    allowSelfSignedForLocalLab: boolean;
  };
  apiVariant: {
    manualVersion: '1.2-2025-11';
    softwareVersionRequirement: 'ZKBio CVSecurity 6.0.0+';
    successCodes: number[];
    tokenQueryParam: 'access_token';
    dateTimeFormat: 'yyyy-MM-dd HH:mm:ss';
  };
};
```

Default inicial:

```ts
const defaultZktecoCvsecurityConnectionConfig = {
  useHttps: true,
  requestTimeoutMs: 15000,
  retryPolicy: {
    maxAttempts: 3,
    baseDelayMs: 1000,
    maxDelayMs: 10000,
  },
  rateLimit: {
    minMonitorIntervalMs: 10000,
  },
  tls: {
    verifyCertificate: true,
    allowSelfSignedForLocalLab: false,
  },
  apiVariant: {
    manualVersion: '1.2-2025-11',
    softwareVersionRequirement: 'ZKBio CVSecurity 6.0.0+',
    successCodes: [0],
    tokenQueryParam: 'access_token',
    dateTimeFormat: 'yyyy-MM-dd HH:mm:ss',
  },
};
```

---

## 8. ProviderCapabilityMatrix

La matriz se genera por instalación, no solo por proveedor.

```ts
type ZktecoCvsecurityCapabilityMatrix = {
  providerInstallationId: string;
  providerKey: 'ZKTECO_CVSECURITY';
  sourceManualVersion: '1.2-2025-11';

  personManagement: CapabilitySupport;
  departmentManagement: CapabilitySupport;
  credentialCardManagement: CapabilitySupport;
  multiCardPerPerson: CapabilitySupport;
  accessControlLevels: CapabilitySupport;
  accessControlDoors: CapabilitySupport;
  accessControlReaders: CapabilitySupport;
  accessControlTransactions: CapabilitySupport;
  accessControlRemoteDoorOpen: CapabilitySupport;

  entranceControlDevices: CapabilitySupport;
  entranceControlGates: CapabilitySupport;
  entranceControlLevels: CapabilitySupport;
  entranceControlReaders: CapabilitySupport;
  entranceControlTransactions: CapabilitySupport;
  entranceControlRemoteGateOpen: CapabilitySupport;

  residentProviderQr: CapabilitySupport;
  visitorProviderQr: CapabilitySupport;
  biometrics: CapabilitySupport;
  parking: CapabilitySupport;
  elevator: CapabilitySupport;
  observedStateRead: CapabilitySupport;
  providerSideReconciliation: CapabilitySupport;

  limitations: string[];
};
```

Valores iniciales documentales:

```ts
const initialZktecoCvsecurityCapabilities = {
  personManagement: 'SUPPORTED_BY_DOCS',
  departmentManagement: 'SUPPORTED_BY_DOCS',
  credentialCardManagement: 'SUPPORTED_BY_DOCS',
  multiCardPerPerson: 'PARTIAL_DOCS_LAB_REQUIRED',
  accessControlLevels: 'SUPPORTED_BY_DOCS',
  accessControlDoors: 'SUPPORTED_BY_DOCS',
  accessControlReaders: 'SUPPORTED_BY_DOCS',
  accessControlTransactions: 'SUPPORTED_BY_DOCS',
  accessControlRemoteDoorOpen: 'SUPPORTED_BY_DOCS_LAB_REQUIRED',

  entranceControlDevices: 'SUPPORTED_BY_DOCS',
  entranceControlGates: 'SUPPORTED_BY_DOCS',
  entranceControlLevels: 'SUPPORTED_BY_DOCS',
  entranceControlReaders: 'SUPPORTED_BY_DOCS',
  entranceControlTransactions: 'SUPPORTED_BY_DOCS',
  entranceControlRemoteGateOpen: 'SUPPORTED_BY_DOCS_LAB_REQUIRED',

  residentProviderQr: 'SUPPORTED_BY_DOCS_LAB_REQUIRED',
  visitorProviderQr: 'SUPPORTED_BY_DOCS_LAB_REQUIRED',
  biometrics: 'FUTURE',
  parking: 'FUTURE',
  elevator: 'FUTURE',
  observedStateRead: 'PARTIAL',
  providerSideReconciliation: 'PARTIAL',
};
```

Reglas:

```text
SUPPORTED_BY_DOCS no significa habilitado en instalación real.
SUPPORTED_BY_DOCS_LAB_REQUIRED no se expone en producción hasta hardware smoke.
FUTURE no se implementa en v1.
UNKNOWN no se muestra en UI normal.
```

---

## 9. Entidades canónicas relacionadas

```text
Condominio
Privada
Propiedad
PropertyOwner / Responsable
AccessCredential
AccessProfile
AccessZone
Door
ProviderInstallation
ProviderMapping
ProviderExternalRef
AccessCommand
DesiredAccessState
ObservedAccessState
ProviderOperationResult
```

El adapter opera con entidades canónicas y mappings, no con datos crudos del proveedor.

---

## 10. Identidad externa: Person / PIN

CVSecurity usa `pin` como identificador de persona.

Modelo Tricor v1:

```text
Una persona técnica externa por propiedad/responsable principal.
```

Ejemplo:

```text
Propiedad: A-101
Responsable: Juan Pérez
Provider person pin: generado por Tricor
Credenciales: TAG-001, TAG-002, QR residente si aplica
```

No se requiere censo de habitantes.

```ts
type ProviderPersonExternalRef = {
  providerInstallationId: string;
  tricorEntityType: 'PROPERTY_ACCESS_SUBJECT';
  tricorEntityId: string;
  externalType: 'PERSON_PIN';
  externalId: string;
  status: 'ACTIVE' | 'SUSPENDED' | 'REVOKED' | 'ARCHIVED';
  createdAt: string;
  updatedAt: string;
};
```

Reglas:

```text
1. El PIN externo debe ser único por instalación.
2. No usar datos personales innecesarios.
3. Si el proveedor exige formato numérico, Tricor genera PIN numérico por instalación.
4. No regenerar PIN si existe ProviderExternalRef activo.
5. Conflictos producen MAPPING_CONFLICT.
```

---

## 11. Credenciales

Modelo aprobado:

```text
Propiedad A-101
Responsable: Juan Pérez
Límite: 3 credenciales activas

Credenciales:
- TAG-001
- TAG-002
- QR residente
```

No se exige identificar al familiar que usa cada credencial.

```ts
type ProviderCredentialExternalRef = {
  providerInstallationId: string;
  accessCredentialId: string;
  externalType:
    | 'PRIMARY_CARD_NO'
    | 'SECONDARY_CARD_NO'
    | 'QR_EXTERNAL_REF'
    | 'BIOMETRIC_EXTERNAL_REF';
  externalId: string;
  providerPersonPin: string;
  status:
    | 'ACTIVE'
    | 'LOST_REPORTED'
    | 'SUSPENDED'
    | 'REVOKED'
    | 'REPLACED'
    | 'ARCHIVED';
  lastObservedAt?: string;
  lastUsedAt?: string;
};
```

El manual documenta tarjeta principal `cardNo`, tarjetas secundarias `supplyCards` en operaciones de persona y `card/set` con `cardType` 0 principal / 1 secundaria. La función multi-card depende de configuración del sistema, por lo que se marca como `PARTIAL_DOCS_LAB_REQUIRED`.

---

## 12. Endpoint catalog - CORE_V1

### 12.1 Personnel / Person

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| crear/editar persona básica con acceso | POST | `/api/person/add?access_token={apitoken}` | CORE_V1 | Soporta `pin`, `deptCode`, `name`, `cardNo`, `personPhoto`, `accLevelIds`, `accStartTime`, `accEndTime`. |
| crear/editar persona básica extendida | POST | `/api/person/addPersonnelBasicInfo?access_token={apitoken}` | CORE_V1 | Soporta `isDisabled`, `cardNo`, `supplyCards`, datos extendidos. No enviar PII innecesaria. |
| bulk add/edit personas | POST | `/api/v2/person/addPersons?access_token={apitoken}` | CONDITIONAL | Útil para importación masiva; no usar como primer flujo crítico. |
| obtener persona por PIN | GET | `/api/person/get/{pin}?access_token={apitoken}` | CORE_V1 | Lectura simple. |
| obtener persona por PIN v2/query | GET | `/api/person/get?pin={pin}&access_token={apitoken}` | CORE_V1 | Incluye más campos como `vislightPhoto`. Sanitizar fotos. |
| buscar lista de personas | POST | `/api/person/getPersonList?access_token={apitoken}` | CORE_V1 | Usa `pins`, `deptCodes`, `pageNo`, `pageSize`. |
| buscar lista de personas v2 paginada | POST | `/api/v2/person/getPersonList?access_token={apitoken}` | CORE_V1 | Respuesta paginada con `total`, `data`. Preferida para reconciliación. |
| marcar salida/resignation | POST | `/api/person/leave?access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Útil para exresidente; probar efecto real sobre acceso. |
| reinstalar persona | POST | `/api/person/reinstated?access_token={apitoken}` | CONDITIONAL | Útil para restaurar tras leave; requiere lab. |
| borrar persona por PIN | DELETE | `/api/person/delete/{pin}?access_token={apitoken}` | DESTRUCTIVE_LAB_ONLY | No usar como default. |
| borrar persona por PIN query | POST | `/api/person/delete?pin={pin}&access_token={apitoken}` | DESTRUCTIVE_LAB_ONLY | No usar como default. |
| bulk delete personas | POST | `/api/v2/person/deleteByPins?access_token={apitoken}&pins={pin1,pin2}` | DESTRUCTIVE_LAB_ONLY | No usar en producción v1. |
| actualizar foto | POST | `/api/person/updatePersonnelPhoto?access_token={apitoken}` | FUTURE | Biometría/foto no es core v1. |
| detectar rostro | POST | `/api/v2/person/detectFace?access_token={apitoken}` | FUTURE | No usar en v1. |
| QR dinámico persona | POST | `/api/person/getQrCode/{pin}?access_token={apitoken}` | CONDITIONAL_LAB_REQUIRED | QR proveedor; no asumir. |
| QR dinámico persona v2 | POST | `/api/v2/person/getQrCode?pin={pin}&access_token={apitoken}` | CONDITIONAL_LAB_REQUIRED | QR proveedor; no asumir. |

### 12.2 Department

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| crear/editar agrupación técnica | POST | `/api/department/add?access_token={apitoken}` | CONDITIONAL | Puede mapear Condominio/Privada técnica. |
| eliminar departamento | DELETE | `/api/department/delete/{code}?access_token={apitoken}` | DESTRUCTIVE_LAB_ONLY | No usar sin laboratorio. |
| eliminar departamento query | POST | `/api/department/delete?code={code}&access_token={apitoken}` | DESTRUCTIVE_LAB_ONLY | No usar sin laboratorio. |
| obtener departamento | GET | `/api/department/get/{code}?access_token={apitoken}` | CONDITIONAL | Validación mapping. |
| obtener departamento v2/query | GET | `/api/department/get?code={code}&access_token={apitoken}` | CONDITIONAL | Validación mapping. |
| listar departamentos | POST | `/api/department/getDepartmentList?pageNo={pageNo}&pageSize={pageSize}&access_token={apitoken}` | CONDITIONAL | Reference data. |

### 12.3 Card / Credential

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| listar tarjetas por PIN | GET | `/api/card/getCards/{pin}?access_token={apitoken}` | CORE_V1 | Devuelve `pin`, `cardNo`, `cardType`. |
| listar tarjetas por PIN v2/query | GET | `/api/card/getCards?pin={pin}&access_token={apitoken}` | CORE_V1 | Preferir si se valida mejor en lab. |
| asignar tarjeta a persona | POST | `/api/card/set?access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Body: `pin`, `cardNo`, `cardType`; multi-card depende de configuración. |

Regla de seguridad:

```text
Si la instalación no soporta remoción granular de tarjeta secundaria, no ejecutar borrado parcial. Generar SAFE_GUARD_BLOCKED.
```

### 12.4 Biotemplate

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| agregar/editar huella | POST | `/api/bioTemplate/add?access_token={apitoken}` | FUTURE | No core v1. |
| obtener huellas por PIN | GET | `/api/bioTemplate/getFgListByPin/{pin}?access_token={apitoken}` | FUTURE | No core v1. |
| borrar huella | varios | `/api/bioTemplate/...` | FUTURE | No core v1. |

---

## 13. Endpoint catalog - Access Control module

Este módulo cubre dispositivos/puertas/lectores/niveles/transacciones de control de acceso tradicional.

### 13.1 Devices

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| listar dispositivos access control | GET | `/api/device/accList?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1 | Reference data. |
| obtener dispositivo por SN | GET | `/api/device/getAcc?sn={sn}&access_token={apitoken}` | CORE_V1 | Reference data / health. |

### 13.2 Doors

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| estado de todas las puertas | GET | `/api/door/allDoorState?timestamp={timestamp}&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Observed state; validar frecuencia. |
| estado puerta por id | GET | `/api/door/doorStateById?doorId={doorId}&timestamp={timestamp}&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Observed state. |
| estado puerta por SN | GET | `/api/door/doorStateBySn?deviceSn={sn}&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Observed state. |
| obtener puerta | GET | `/api/door/get?id={id}&access_token={apitoken}` | CORE_V1 | Mapping. |
| listar puertas | GET | `/api/door/list?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1 | Mapping. |
| apertura remota por id | POST | `/api/door/remoteOpenById?doorId={doorId}&interval={interval}&access_token={apitoken}` | LAB_REQUIRED | Solo GuardSupervisor + motivo. |
| apertura remota por nombre | POST | `/api/door/remoteOpenByName?doorName={doorName}&interval={interval}&access_token={apitoken}` | LAB_REQUIRED | Preferir id si existe mapping. |
| cerrar/off por id | POST | `/api/door/remoteoffById?doorId={doorId}&access_token={apitoken}` | LAB_REQUIRED | Solo técnico/lab. |
| cerrar/off por nombre | POST | `/api/door/remoteoffByName?doorName={doorName}&access_token={apitoken}` | LAB_REQUIRED | Solo técnico/lab. |

### 13.3 Readers

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| listar lectores | GET | `/api/reader/accList?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1 | Mapping entrada/salida. |
| obtener lector | GET | `/api/reader/getAcc?id={id}&access_token={apitoken}` | CORE_V1 | Mapping. |

### 13.4 Access Control Levels

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| quitar nivel a persona | POST | `/api/accLevel/deleteLevel?pin={pin}&levelIds={levelIds}&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Revocación parcial de perfil. |
| obtener nivel por id | GET | `/api/accLevel/getById/{id}?access_token={apitoken}` | CORE_V1 | Mapping. |
| obtener nivel por id v2 | GET | `/api/v2/accLevel/getById?id={id}&access_token={apitoken}` | CORE_V1 | Mapping. |
| obtener nivel por nombre | GET | `/api/accLevel/getByName/{name}?access_token={apitoken}` | CORE_V1 | Mapping. |
| obtener nivel por nombre v2 | GET | `/api/v2/accLevel/getByName?name={name}&access_token={apitoken}` | CORE_V1 | Mapping. |
| listar niveles | GET | `/api/accLevel/list?pageNo={pageNo}&pageSize={pageSize}&access_token={apitoken}` | CORE_V1 | Reference data. |
| listar niveles v2 paginado | GET | `/api/v2/accLevel/list?pageNo={pageNo}&pageSize={pageSize}&access_token={apitoken}` | CORE_V1 | Preferido para UI mapping. |
| sincronizar nivel a dispositivo | POST | `/api/accLevel/syncLevel?levelId={levelId}&access_token={apitoken}` | LAB_REQUIRED | Puede ser necesario tras cambios. |
| sincronizar niveles de persona | POST | `/api/accLevel/syncPerson?pin={pin}&levelIds={levelIds}&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Aplicar perfil calculado. |
| crear/editar nivel | POST | `/api/accLevel/addLevel?access_token={apitoken}` | CONDITIONAL_LAB_REQUIRED | Evitar en v1 si perfiles se preconfiguran. |
| crear/editar nivel y puertas | POST | `/api/accLevel/addLevelDoor?access_token={apitoken}` | CONDITIONAL_LAB_REQUIRED | Modo técnico, no UI normal. |
| bulk asignar nivel a persona | POST | `/api/accLevel/addLevelPerson?levelIds={levelIds}&pin={pin}&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Alternativa para aplicar perfil. |

### 13.5 Access Control Transactions

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| transacciones por device SN | GET | `/api/transaction/device/{deviceSn}?pageNo=1&pageSize=20&acc_token={apitoken}` | REVIEW_REQUIRED | Manual usa `acc_token`; validar. |
| transacciones por device SN v2 | GET | `/api/v2/transaction/device/{deviceSn}?pageNo=1&pageSize=20&acc_token={apitoken}` | REVIEW_REQUIRED | Manual usa `acc_token`; validar. |
| transacciones por device SN v3 | GET | `/api/v3/transaction/device?deviceSn={deviceSn}&pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Preferir por `access_token`. |
| lista transacciones | GET | `/api/transaction/list?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Observed state. |
| lista transacciones v2 | GET | `/api/v2/transaction/list?personPin={personPin}&startDate={startDate}&endDate={endDate}&pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Preferible para filtros. |
| monitor transacciones | GET | `/api/transaction/monitor?timestamp={timestamp}&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Respetar frecuencia mínima. |
| transacciones por PIN | GET | `/api/transaction/person/{pin}?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Inactividad por credencial/persona. |
| transacciones por PIN v2/v3 | GET | `/api/v2/transaction/person/{pin}...`, `/api/v3/transaction/person?...` | LAB_REQUIRED | Validar formato. |
| obtener transacción por id | GET | `/api/transaction/getById/{id}?access_token={apitoken}` | CONDITIONAL | Auditoría. |
| obtener transacción por id v2 | GET | `/api/v2/transaction/getById?id={id}&access_token={apitoken}` | CONDITIONAL | Auditoría. |

---

## 14. Endpoint catalog - Entrance Control / PSG module

Este módulo cubre gates/torniquetes/plumas/barreras bajo `psg*`. Para Tricor, este módulo es importante para casetas, plumas y accesos peatonales/vehiculares si el cliente usa Entrance Control.

### 14.1 PSG Devices

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| listar dispositivos entrance control | GET | `/api/psgDevice/list?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1 | El título del manual tiene typo, el URL correcto usa `psgDevice`. |
| obtener dispositivo por SN | GET | `/api/psgDevice/getBySn?sn={sn}&access_token={apitoken}` | CORE_V1 | Health/mapping. |
| listar dispositivos v2 | GET | `/api/v2/psgDevice/list?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1 | Respuesta paginada. |

### 14.2 PSG Gates

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| estado de todos los gates | GET | `/api/psgGate/allGateState?access_token={apitoken}` | CORE_V1 | Devuelve `connect`, `gateState`, `alarm`, `relay`. |
| estado gate por id | GET | `/api/psgGate/gateStateById?gateId={gateId}&access_token={apitoken}` | CORE_V1 | Observed state. |
| estado gate por SN | GET | `/api/psgGate/gateStateBySn?deviceSn={sn}&access_token={apitoken}` | CORE_V1 | Observed state. |
| obtener gate por id | GET | `/api/psgGate/getById?id={id}&access_token={apitoken}` | CORE_V1 | Mapping. |
| listar gates | GET | `/api/psgGate/list?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1 | Mapping. |
| listar gates v2 | GET | `/api/v2/psgGate/list?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1 | Paginado. |
| apertura remota por gate id | POST | `/api/psgGate/remoteOpenById?gateId={gateId}&openType={openType}&access_token={apitoken}` | LAB_REQUIRED | `openType`: 1 entrada, 2 salida. |
| apertura remota por gate name | POST | `/api/psgGate/remoteOpenByName?gateName={gateName}&openType={openType}&access_token={apitoken}` | LAB_REQUIRED | Preferir id si existe mapping. |

### 14.3 PSG Levels

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| quitar nivel a persona | POST | `/api/psgLevel/deleteLevel?pin={pin}&levelIds={levelIds}&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Revocación/restricción. |
| obtener nivel por id | GET | `/api/psgLevel/getById/{id}?access_token={apitoken}` | CORE_V1 | Mapping. |
| obtener nivel por nombre | GET | `/api/psgLevel/getByName/{name}?access_token={apitoken}` | CORE_V1 | Mapping. |
| obtener nivel por id v2 | GET | `/api/v2/psgLevel/getById/{id}?access_token={apitoken}` | CORE_V1 | Mapping. |
| obtener nivel por nombre v2 | GET | `/api/v2/psgLevel/getByName/{name}?access_token={apitoken}` | CORE_V1 | Mapping. |
| listar niveles PSG | GET | `/api/psgLevel/list?pageNo={pageNo}&pageSize={pageSize}&access_token={apitoken}` | CORE_V1 | Reference data. |
| listar niveles PSG v2 | GET | `/api/v2/psgLevel/list?pageNo={pageNo}&pageSize={pageSize}&access_token={apitoken}` | CORE_V1 | Paginado. |
| sync level | POST | `/api/psgLevel/syncLevel?levelId={levelId}&access_token={apitoken}` | LAB_REQUIRED | Sincronizar a dispositivo. |
| sync person level | POST | `/api/psgLevel/syncPerson?pin={pin}&levelIds={levelIds}&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Aplicar perfil. |
| add level by level | POST | `/api/v2/psgLevel/addLevelByLevel?access_token={apitoken}` | CONDITIONAL_LAB_REQUIRED | Bulk por nivel. |
| add level by person | POST | `/api/v2/psgLevel/addLevelByPerson?access_token={apitoken}` | CONDITIONAL_LAB_REQUIRED | Bulk por persona. |

### 14.4 PSG Readers

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| listar readers PSG | GET | `/api/psgReader/list?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1 | Mapping. |
| obtener reader PSG | GET | `/api/psgReader/getById?id={id}&access_token={apitoken}` | CORE_V1 | Mapping. |
| listar readers PSG v2 | GET | `/api/v2/psgReader/list?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1 | Paginado. |

### 14.5 PSG Transactions

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| transacciones gate por device SN | GET | `/api/psgTransaction/device/{deviceSn}?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Observed state. |
| transacciones gate por device SN v2/v3 | GET | `/api/v2/psgTransaction/device/{deviceSn}...`, `/api/v3/psgTransaction/device/{deviceSn}...` | LAB_REQUIRED | Validar formato. |
| lista gate transactions | GET | `/api/psgTransaction/list?personPin={personPin}&startDate={startDate}&endDate={endDate}&pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Filtros. |
| monitor gate transactions | GET | `/api/psgTransaction/monitor?timestamp={timestamp}&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Manual recomienda no consultar con frecuencia menor a 10s. |
| transacciones gate por PIN | GET | `/api/psgTransaction/person/{pin}?pageNo=1&pageSize=20&access_token={apitoken}` | CORE_V1_LAB_REQUIRED | Inactividad/reconciliación. |
| transacciones gate por PIN v3 | GET | `/api/v3/psgTransaction/person/{pin}?pageNo=1&pageSize=20&access_token={apitoken}` | LAB_REQUIRED | Validar. |
| lista gate transactions v2 | GET | `/api/v2/psgTransaction/list?personPin={personPin}&startDate={startDate}&endDate={endDate}&pageNo=1&pageSize=20&access_token={apitoken}` | LAB_REQUIRED | Paginado. |

---

## 15. Visitor / QR endpoints

Tricor debe mantener QR canónico propio. Estos endpoints se consideran integración condicional si el proveedor QR se valida en laboratorio.

| Uso Tricor | Método | Endpoint | Estado | Notas |
|---|---:|---|---|---|
| registrar visitante | POST | `/api/visRegistration/add?&access_token={apitoken}` | FUTURE_CONDITIONAL | Requiere `persPersonPin`, `certType`, `certNum`, `visEmpName`, `startTime`, `endTime`, `visLevels`. |
| checkout visitante | POST | `/api/visRegistration/exit?certType={certType}&certNum={certNum}&access_token={apitoken}` | FUTURE_CONDITIONAL | Cierre visitante. |
| QR visitante por pin | POST | `/api/visRegistration/getQrCode/{pin}?access_token={apitoken}` | FUTURE_CONDITIONAL_LAB_REQUIRED | Devuelve base64. |
| QR visitante v2 por pin | POST | `/api/visRegistration/getQrCode?pin={pin}&access_token={apitoken}` | FUTURE_CONDITIONAL_LAB_REQUIRED | Devuelve base64. |
| QR visitante por documento/teléfono | POST | `/api/v2/visRegistration/getQrCodeByCertNumber?access_token={apitoken}&certNumber={certNumber}&certType={certType}&phone={phone}` | FUTURE_CONDITIONAL_LAB_REQUIRED | No core v1. |
| lista visitantes | POST | `/api/visVisitor/getVisitorList?access_token={apitoken}&certNumbers={certNumbers}&names={names}&pageNo={pageNo}&pageSize={pageSize}` | FUTURE | No core v1. |
| agregar reservación | POST | `/api/visReservation/add?access_token={apitoken}` | FUTURE | No core v1. |
| eliminar reservación | POST | `/api/visReservation/del?certType={certType}&certNum={certNum}&access_token={apitoken}` | FUTURE | No core v1. |

Decisión:

```text
QR invitado v1 debe ser canónico Tricor. QR proveedor ZKTeco queda CONDITIONAL hasta laboratorio.
```

---

## 16. Parking, Elevator y otros módulos

### 16.1 Parking

El manual incluye parking, park authorization, park transactions, open gate channel y billing. Tricor v1 no manejará vehículo como entidad, por lo que estos endpoints quedan fuera de core.

Endpoints relevantes solo para futuro:

```text
/api/v2/parkBase/getParkingChannelList
/api/v1/parkBase/getParkChannelList
/api/v1/parkBase/openGateChannel
/api/v2/parkPlate/addFixedVehicle
/api/v2/parkPlate/delFixedVehicle
/api/v2/parkPlate/getFixedVehicle
/api/v2/parkTransaction/listParkRecord
/api/v1/parkCost/vehicleBilling
/api/v1/parkCost/payNotify
```

Estado:

```text
FUTURE_RESEARCH. No usar para v1.
```

### 16.2 Elevator

Endpoints documentados:

```text
/api/eleDevice/eleList
/api/eleDevice/getEle
/api/eleLevel/list
/api/eleLevel/syncPerson
/api/floor/list
/api/floor/remoteOpenById
```

Estado:

```text
FUTURE. No v1.
```

### 16.3 Biometric / Face / Time Attendance / Space / Smart Video / Intrusion

Estado:

```text
OUT_OF_SCOPE_V1.
```

---

## 17. Mapping Tricor → CVSecurity

### 17.1 Access subject

```text
Tricor PropertyAccessSubject
→ CVSecurity Person pin
```

### 17.2 Credentials

```text
Tricor AccessCredential CARD_OR_TAG
→ CVSecurity cardNo / supplyCards / card/set
```

### 17.3 Profiles

Access Control module:

```text
Tricor AccessProfile
→ CVSecurity accLevelIds / accLevel levelIds
```

Entrance Control module:

```text
Tricor AccessProfile
→ CVSecurity psgLevel levelIds
```

### 17.4 Doors / gates

```text
Tricor Door / AccessPoint
→ /api/door/* for Access Control doors
→ /api/psgGate/* for Entrance Control gates
```

### 17.5 Transactions

```text
Tricor ObservedAccessEvent
→ /api/transaction/* for Access Control
→ /api/psgTransaction/* for Entrance Control
```

### 17.6 QR

```text
Tricor QR
→ Tricor-owned by default
→ CVSecurity person/visitor QR only if capability validated
```

---

## 18. ProviderMapping mínimo

```ts
type ZktecoCvsecurityProviderMapping = {
  providerInstallationId: string;
  version: string;

  modules: {
    accessControlEnabled: boolean;
    entranceControlEnabled: boolean;
    visitorQrEnabled: boolean;
    parkingEnabled: false;
    elevatorEnabled: false;
  };

  accessProfiles: {
    activeProfileId: string;
    morosoDefaultProfileId: string;
    pedestrianOnlyProfileId: string;
    revokedProfileId?: string;
    temporaryExceptionProfileId?: string;
  };

  externalLevels: Array<{
    tricorAccessProfileId: string;
    providerModule: 'ACCESS_CONTROL' | 'ENTRANCE_CONTROL';
    externalLevelId: string;
    externalLevelName: string;
    purpose:
      | 'ACTIVE'
      | 'MOROSO_DEFAULT'
      | 'PEDESTRIAN_ONLY'
      | 'REVOKED'
      | 'TEMPORARY_EXCEPTION';
  }>;

  accessPoints: Array<{
    tricorDoorId: string;
    providerModule: 'ACCESS_CONTROL' | 'ENTRANCE_CONTROL';
    externalDoorId?: string;
    externalGateId?: string;
    externalReaderId?: string;
    externalDeviceSn?: string;
    name: string;
    zone: 'PEDESTRIAN' | 'VEHICULAR' | 'SERVICE' | 'FUTURE_AMENITY';
  }>;

  credentialRules: {
    maxCardsPerProperty?: number;
    supportsSecondaryCards: boolean;
    supportsIndividualSecondaryCardRemoval: boolean;
  };
};
```

---

## 19. Aplicación de perfiles

### 19.1 Perfil activo

```text
ACTIVE_ACCESS_PROFILE
→ levelIds completos de la propiedad según AccessZone permitida.
```

### 19.2 Perfil moroso default

Default aprobado:

```text
Moroso:
- peatonal permitido
- vehicular bloqueado
- QR invitados bloqueado
- amenidades futuras bloqueadas
```

Traducción:

```text
MOROSO_DEFAULT_PROFILE
→ levelIds peatonales únicamente
→ remover/sustituir levelIds vehiculares
```

### 19.3 Perfil revocado

Opciones posibles:

```text
- aplicar REVOKED_PROFILE si existe mapping seguro
- usar /api/person/leave si el efecto real es seguro
- deshabilitar persona con isDisabled si se valida
- eliminar persona solo como operación destructiva de laboratorio
```

Default seguro:

```text
No borrar persona como primer mecanismo. Preferir perfil revocado o leave validado.
```

---

## 20. Operaciones del adapter

```ts
export interface ZktecoCvsecurityAdapter extends AccessProviderAdapter {
  providerKey: 'ZKTECO_CVSECURITY';

  validateInstallation(input: ValidateInstallationInput): Promise<ValidationResult>;
  getCapabilities(input: GetCapabilitiesInput): Promise<ProviderCapabilityMatrix>;
  fetchReferenceData(input: FetchProviderReferenceDataInput): Promise<ZktecoReferenceData>;
  validateMappings(input: ValidateMappingsInput): Promise<MappingValidationResult>;
  ensurePerson(input: EnsurePersonInput): Promise<ProviderOperationResult>;
  applyAccessProfile(input: ApplyAccessProfileInput): Promise<ProviderOperationResult>;
  revokeCredential(input: RevokeCredentialInput): Promise<ProviderOperationResult>;
  restoreCredential(input: RestoreCredentialInput): Promise<ProviderOperationResult>;
  disableCredential(input: DisableCredentialInput): Promise<ProviderOperationResult>;
  openDoor(input: OpenDoorInput): Promise<ProviderOperationResult>;
  fetchObservedState(input: FetchObservedStateInput): Promise<ObservedAccessStateResult>;
  reconcile(input: ReconcileProviderInput): Promise<ReconciliationResult>;
}
```

---

## 21. validateInstallation

Debe validar:

```text
1. baseUrl alcanzable desde Edge.
2. access_token configurado.
3. API Authorization/licencia activa.
4. endpoint seguro de lectura responde.
5. respuesta coincide con { code, message/msg, data }.
6. successCodes configurados funcionan.
7. latencia aceptable.
8. TLS según política.
```

Endpoint recomendado para prueba inicial:

```text
GET /api/department/getDepartmentList?pageNo=1&pageSize=1&access_token={apitoken}
```

Alternativas:

```text
GET /api/device/accList?pageNo=1&pageSize=1&access_token={apitoken}
GET /api/psgDevice/list?pageNo=1&pageSize=1&access_token={apitoken}
```

---

## 22. fetchReferenceData

Debe cargar:

```text
- departments
- access control devices
- doors
- readers
- accLevels
- entrance control devices
- gates
- psgReaders
- psgLevels
```

```ts
type ZktecoReferenceData = {
  departments: Array<{ code: string; name: string; parentCode?: string }>;
  accessControl: {
    devices: Array<{ id?: string; sn: string; name: string; type?: string; status?: string }>;
    doors: Array<{ id: string; name: string; deviceId?: string; deviceSn?: string }>;
    readers: Array<{ id: string; doorId: string; name: string; readerNo?: number; readerState?: number }>;
    levels: Array<{ id: string; name: string }>;
  };
  entranceControl: {
    devices: Array<{ id?: string; sn: string; name: string; type?: string; status?: string; gateType?: string }>;
    gates: Array<{ id: string; name: string; deviceId?: string; connect?: string; gateState?: string; alarm?: string; relay?: string }>;
    readers: Array<{ id: string; gateId?: string; name: string; readerNo?: number; readerState?: number }>;
    levels: Array<{ id: string; name: string }>;
  };
};
```

Reglas:

```text
- fetchReferenceData no modifica proveedor.
- Se ejecuta desde modo técnico.
- Resultado se versiona.
- Si un módulo no responde, marcar capability DEGRADED/PARTIAL, no fallar todo automáticamente.
```

---

## 23. validateMappings

Debe validar:

```text
1. activeProfileId existe en accLevel o psgLevel según módulo.
2. morosoDefaultProfileId existe.
3. pedestrianOnlyProfileId existe si la política conserva peatonal.
4. accessPoints tienen doorId/gateId existente.
5. accessZone vehicular apunta a gate/door correcto.
6. accessZone peatonal apunta a gate/door correcto.
7. no hay perfil Tricor apuntando a level externo inexistente.
8. no hay access point duplicado sin intención explícita.
9. módulos activados tienen endpoint de referencia válido.
```

Si falla:

```text
No ejecutar comandos destructivos.
ProviderInstallation.status = PENDING_VALIDATION / DEGRADED.
Crear incidente ZKTECO_MAPPING_MISSING o ZKTECO_MAPPING_STALE.
```

---

## 24. applyAccessProfile

Entrada:

```ts
type ApplyAccessProfileInput = {
  providerInstallationId: string;
  accessCommandId: string;
  commandIdempotencyKey: string;
  propertyId: string;
  accessSubjectId: string;
  providerPersonPin: string;
  targetAccessProfile:
    | 'ACTIVE_ACCESS_PROFILE'
    | 'MOROSO_DEFAULT_PROFILE'
    | 'PEDESTRIAN_ONLY_PROFILE'
    | 'REVOKED_PROFILE'
    | 'TEMPORARY_EXCEPTION_PROFILE';
  reason:
    | 'PAYMENT_CONFIRMED'
    | 'MOROSITY_APPLIED'
    | 'MANUAL_PAYMENT_APPROVED'
    | 'MOVE_OUT_EXPIRED'
    | 'TEMPORARY_EXCEPTION'
    | 'ADMIN_RECALCULATION';
};
```

Estrategias documentadas:

```text
A. Crear/editar persona con accLevelIds usando /api/person/add o /api/person/addPersonnelBasicInfo.
B. Asignar niveles con /api/accLevel/addLevelPerson o /api/accLevel/syncPerson.
C. Para Entrance Control, usar /api/psgLevel/syncPerson o v2 addLevelByPerson.
D. Para quitar niveles, usar /api/accLevel/deleteLevel o /api/psgLevel/deleteLevel.
```

Regla de laboratorio:

```text
El flujo exacto de replace profile debe validarse. No asumir que syncPerson reemplaza todos los niveles previos sin probarlo.
```

---

## 25. Credential lifecycle

### 25.1 Tarjeta perdida

```text
Residente reporta tarjeta perdida
→ AccessCredential = LOST_REPORTED
→ AccessCommand REVOKE_CREDENTIAL
→ Adapter intenta revocación granular
→ si no hay soporte seguro, SAFE_GUARD_BLOCKED
```

Endpoints candidatos:

```text
GET /api/card/getCards/{pin}
POST /api/card/set
POST /api/person/addPersonnelBasicInfo con cardNo/supplyCards ajustado
POST /api/person/leave solo si aplica a todo el sujeto
```

### 25.2 Exresidente

```text
MoveOut grace expira
→ Policy Engine marca revocación
→ Adapter aplica REVOKED_PROFILE o leave validado
→ credenciales quedan REVOKED/ARCHIVED en Tricor
```

Endpoint primario a validar:

```text
POST /api/person/leave?access_token={apitoken}
```

No usar por default:

```text
DELETE /api/person/delete/{pin}
POST /api/v2/person/deleteByPins
```

---

## 26. openDoor / openGate

### 26.1 Access Control Door

```text
POST /api/door/remoteOpenById?doorId={doorId}&interval={interval}&access_token={apitoken}
POST /api/door/remoteOpenByName?doorName={doorName}&interval={interval}&access_token={apitoken}
```

### 26.2 Entrance Control Gate

```text
POST /api/psgGate/remoteOpenById?gateId={gateId}&openType={openType}&access_token={apitoken}
POST /api/psgGate/remoteOpenByName?gateName={gateName}&openType={openType}&access_token={apitoken}
```

Reglas:

```text
1. Solo si capability = SUPPORTED_BY_LAB.
2. Solo GuardSupervisor o flujo autorizado.
3. Motivo obligatorio.
4. Auditoría completa.
5. No exponer si mapping de puerta/gate no existe.
```

Motivos rápidos:

```text
QR_VALID_GATE_FAILED
PROVIDER_FAILURE
EMERGENCY
ADMIN_AUTHORIZED
OTHER
```

---

## 27. fetchObservedState

Fuentes:

```text
- persona por PIN
- tarjetas por PIN
- niveles/perfiles asociados si endpoint lo expone
- door/gate state
- transacciones por PIN
- transacciones por device/gate
- transaction monitor
```

Resultado:

```ts
type ObservedAccessStateResult = {
  providerInstallationId: string;
  accessSubjectId: string;
  providerPersonPin: string;
  observedAt: string;
  confidence: 'HIGH' | 'MEDIUM' | 'LOW';
  accessProfileObserved?: string;
  activeCredentials: Array<{
    externalId: string;
    type: 'CARD_OR_TAG' | 'QR' | 'BIOMETRIC' | 'UNKNOWN';
    status: 'ACTIVE' | 'DISABLED' | 'UNKNOWN';
    lastUsedAt?: string;
  }>;
  accessPoints?: Array<{
    externalId: string;
    providerModule: 'ACCESS_CONTROL' | 'ENTRANCE_CONTROL';
    name: string;
    state?: string;
    connection?: string;
  }>;
};
```

Reglas:

```text
- LOW confidence no borra credenciales.
- Ausencia de dato no equivale a contradicción.
- Drift requiere evidencia.
```

---

## 28. Reconciliación

Detectar:

```text
1. Credencial activa en proveedor pero revocada en Tricor.
2. Persona externa sin ProviderExternalRef.
3. Credencial externa sin propiedad activa.
4. Exresidente con credencial activa.
5. Perfil externo distinto al desired state.
6. Gate/door/dispositivo offline.
7. Mapping stale.
8. Transacciones de credenciales desconocidas.
9. Credencial sin uso por X días.
```

Acciones:

```text
- Crear incidente.
- Marcar para revisión.
- Revocar automáticamente solo si política lo permite y mapping es seguro.
- Nunca borrar histórico.
```

---

## 29. Idempotencia

CVSecurity no debe asumirse idempotente.

```ts
type ProviderOperationIdempotencyRecord = {
  commandIdempotencyKey: string;
  providerInstallationId: string;
  operationType: string;
  requestHash: string;
  status:
    | 'STARTED'
    | 'SUCCEEDED'
    | 'FAILED_RETRYABLE'
    | 'FAILED_FINAL'
    | 'PARTIAL';
  providerResultHash?: string;
  createdAt: string;
  updatedAt: string;
};
```

Reglas:

```text
1. Misma idempotency key + mismo requestHash no ejecuta dos veces.
2. Misma idempotency key + requestHash distinto = error crítico.
3. Timeout debe reconciliar antes de repetir operación destructiva.
4. Retry solo para errores retryable.
```

---

## 30. Error normalization

```ts
type ProviderErrorCode =
  | 'AUTH_FAILED'
  | 'LICENSE_REQUIRED'
  | 'NETWORK_UNREACHABLE'
  | 'TIMEOUT'
  | 'INVALID_RESPONSE'
  | 'MAPPING_MISSING'
  | 'MAPPING_STALE'
  | 'PERSON_NOT_FOUND'
  | 'PERSON_CONFLICT'
  | 'DUPLICATE_CARD'
  | 'CARD_NOT_FOUND'
  | 'LEVEL_NOT_FOUND'
  | 'DOOR_NOT_FOUND'
  | 'GATE_NOT_FOUND'
  | 'DEVICE_OFFLINE'
  | 'UNSUPPORTED_CAPABILITY'
  | 'PROVIDER_REJECTED'
  | 'SAFE_GUARD_BLOCKED'
  | 'UNKNOWN_PROVIDER_ERROR';
```

Reglas:

```text
- No filtrar errores crudos al dominio.
- Guardar raw response sanitizado si se requiere diagnóstico.
- access_token nunca debe aparecer en error/reportes.
- Appendix Error Code del manual debe mapearse en laboratorio al normalizer.
```

---

## 31. ProviderOperationResult

```ts
type ProviderOperationResult = {
  providerInstallationId: string;
  accessCommandId: string;
  operationType: string;
  status:
    | 'SUCCESS'
    | 'PARTIAL_SUCCESS'
    | 'FAILED_RETRYABLE'
    | 'FAILED_FINAL'
    | 'SKIPPED_UNSUPPORTED'
    | 'SAFE_GUARD_BLOCKED';
  errorCode?: ProviderErrorCode;
  message?: string;
  externalRefs?: ProviderExternalRef[];
  observedStateHint?: Partial<ObservedAccessStateResult>;
  retryAfterMs?: number;
  rawProviderRequestRef?: string;
  rawProviderResponseRef?: string;
  createdAt: string;
};
```

---

## 32. Flujos Tricor

### 32.1 Pago confirmado restaura acceso

```text
Mercado Pago confirma pago
→ Tricor marca deuda pagada
→ Policy Engine recalcula ACTIVE_ACCESS_PROFILE
→ Cloud crea AccessCommand RESTORE_ACCESS
→ Edge ejecuta adapter
→ Adapter aplica accLevel/psgLevel activo
→ Adapter intenta observed state
→ Cloud muestra restaurado o pendiente
→ Auditoría
```

### 32.2 Morosidad aplica restricción

```text
Job morosidad detecta deuda vencida
→ Policy Engine aplica MOROSO_DEFAULT_PROFILE
→ Cloud crea APPLY_MOROSO_RESTRICTION
→ Adapter aplica perfil peatonal/restringido
→ transacciones/estado observado validan
→ Guard/Resident muestran estado correcto
```

### 32.3 Transferencia manual fallback

```text
Finanzas aprueba pago manual
→ Payment status APPROVED_MANUAL
→ Policy Engine recalcula
→ AccessCommand restaura si corresponde
→ Adapter aplica perfil activo
→ Auditoría registra aprobador y evidencia
```

### 32.4 Mudanza / exresidente

```text
MoveOutWorkflow expira gracia
→ Policy Engine revoca acceso
→ Adapter aplica REVOKED_PROFILE o leave validado
→ Tricor conserva histórico
```

### 32.5 Tarjeta perdida

```text
Residente reporta pérdida
→ credencial LOST_REPORTED
→ Adapter revoca solo credencial objetivo si es seguro
→ Administración registra reemplazo
```

---

## 33. Edge offline

```text
Cloud ordena comando y Edge está offline:
- comando queda PENDING_EDGE_OFFLINE
- se crea incidente
- no se marca como aplicado

Edge aplica comando pero no confirma a Cloud:
- Edge conserva resultado local
- Cloud queda APPLIED_UNCONFIRMED
- al reconectar, Edge sincroniza
```

---

## 34. Edge UI local

Debe mostrar:

```text
- estado conexión CVSecurity
- baseUrl sanitizado
- provider activo
- última prueba de conexión
- último sync exitoso
- cola pendiente
- errores recientes
- mappings cargados
- capabilities detectadas
- probar conexión
- exportar diagnóstico sanitizado
```

No debe permitir:

```text
- editar residentes
- editar pagos
- editar permisos permanentes
- registrar credenciales nuevas
- ver secretos
```

---

## 35. Logging y auditoría

No loggear:

```text
access_token
payloads con fotos/base64
secretRefs resueltos
credenciales completas sin masking
PII innecesaria
```

Eventos mínimos:

```text
PROVIDER_INSTALLATION_VALIDATED
PROVIDER_MAPPING_CHANGED
PROVIDER_CAPABILITY_CHANGED
ACCESS_PROFILE_APPLIED
ACCESS_PROFILE_RESTORED
ACCESS_PROFILE_RESTRICTED
CREDENTIAL_REVOKED
CREDENTIAL_LOST_REPORTED
REMOTE_OPEN_EXECUTED
TEMPORARY_EXCEPTION_APPLIED
RECONCILIATION_DRIFT_DETECTED
PROVIDER_OPERATION_FAILED
```

---

## 36. QA requerido para este adapter

Este documento no actualiza `QA_HARNESS_SPEC_V0.1.md`, pero define pruebas que ese archivo deberá incorporar después de aprobación.

Pruebas mínimas futuras:

```text
1. validateInstallation con token válido.
2. validateInstallation con token inválido.
3. fetchReferenceData Access Control.
4. fetchReferenceData Entrance Control.
5. validateMappings con levels existentes.
6. validateMappings con level faltante.
7. apply ACTIVE_ACCESS_PROFILE via accLevel.
8. apply MOROSO_DEFAULT_PROFILE via accLevel.
9. apply ACTIVE_ACCESS_PROFILE via psgLevel.
10. apply MOROSO_DEFAULT_PROFILE via psgLevel.
11. remoteOpen door bloqueado si capability no validada.
12. remoteOpen gate bloqueado si capability no validada.
13. lost credential con soporte granular.
14. lost credential sin soporte granular → SAFE_GUARD_BLOCKED.
15. exresident leave/revoked profile.
16. transaction monitor con rate limit >= 10s.
17. psgTransaction monitor con rate limit >= 10s.
18. observed state LOW no borra credenciales.
19. errores normalizados.
20. secrets no aparecen en reportes.
```

Hardware smoke requerido:

```text
pnpm qa:hardware-smoke zkteco --manual-approval
```

---

## 37. Implementación por fases

### Fase 1 - Skeleton

```text
- adapter interface
- config schema
- secret handling
- response parser
- error normalizer
- fake client
- unit tests
```

### Fase 2 - Reference data

```text
- departments
- access devices/doors/readers/levels
- psg devices/gates/readers/levels
```

### Fase 3 - Person and credential

```text
- ensurePerson
- get person
- list persons
- card get/set
- safe credential lifecycle
```

### Fase 4 - Access profiles

```text
- accLevel apply/restrict
- psgLevel apply/restrict
- sync person
- safe replace strategy
```

### Fase 5 - Observed state

```text
- door/gate state
- transactions
- monitor with rate limit
- reconciliation
```

### Fase 6 - Hardware smoke

```text
- real lab token
- real mappings
- test person
- test credential
- active/moroso profiles
- remote open only with explicit approval
```

---

## 38. Reglas para Claude Code CLI

Claude Code CLI debe seguir:

```text
1. No modificar dominio para resolver proveedor.
2. No hardcodear levelIds, pins, doors, gates ni tokens.
3. No exponer access_token.
4. No asumir QR nativo.
5. No asumir multi-card granular.
6. No asumir remoteOpen como producción sin hardware smoke.
7. No borrar personas por default.
8. No tocar pagos para arreglar provider adapter.
9. No tocar UI para resolver provider adapter.
10. Todo cambio debe pasar por QA Harness.
```

Prompt inicial recomendado:

```text
Implementa el skeleton del provider adapter ZKTeco / CVSecurity siguiendo PROVIDER_ZKTECO_CVSECURITY_ADAPTER_SPEC_V0.1.md.

Restricciones:
- No tocar dominio.
- No tocar pagos.
- No tocar UI.
- No implementar operaciones destructivas reales.
- No asumir remoteOpen, QR nativo ni multi-card granular.
- Crear interfaces, config schema, response parser, error normalizer, fake client y tests unitarios.

Entregar:
- archivos modificados
- comandos ejecutados
- reporte QA
- riesgos pendientes
```

---

## 39. Riesgos principales

```text
| Riesgo | Impacto | Mitigación |
|---|---:|---|
| API license no activa | Alto | validateInstallation |
| access_token inválido | Alto | secret validation |
| code success inconsistente | Alto | successCodes configurable |
| acc_token vs access_token | Medio | laboratorio endpoint por endpoint |
| multi-card no granular | Alto | SAFE_GUARD_BLOCKED |
| remoteOpen inseguro | Alto | capability + GuardSupervisor + auditoría |
| perfil mal mapeado | Alto | validateMappings |
| borrar persona accidentalmente | Alto | delete endpoints DESTRUCTIVE_LAB_ONLY |
| estado observado incompleto | Medio | confidence LOW/MEDIUM/HIGH |
| Edge offline | Alto | command queue + incident |
```

---

## 40. Decisiones cerradas

```text
1. ZKTeco / CVSecurity es provider v1.
2. Edge local obligatorio.
3. Fuente oficial usada: ZKBio CVSecurity 3rd Party API v1.2 / noviembre 2025 / CVSecurity 6.0.0+.
4. El adapter usa access_token por query.
5. successCodes default = [0], validación en laboratorio.
6. El modelo externo usa Person PIN.
7. Access Control usa accLevel.
8. Entrance Control usa psgLevel.
9. Doors usan /api/door/*.
10. Gates usan /api/psgGate/*.
11. Transactions usan /api/transaction/* y /api/psgTransaction/*.
12. QR proveedor existe documentalmente, pero queda CONDITIONAL_LAB_REQUIRED.
13. Parking/elevator/biometría quedan fuera de v1.
14. Deletes son destructivos y no default.
15. QA Harness es gate obligatorio, pero no se actualiza en este archivo.
```

---

## 41. Archivos relacionados no modificados

Este cambio no modifica:

```text
PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
QA_HARNESS_SPEC_V0.1.md
ROADMAP_V0.1.md
AI_AGENT_RULES_AND_HANDOFF.md
PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
DOMAIN_MODEL_V0.1.md
ACCESS_POLICY_CONFIGURATION_V0.1.md
CLOUD_EDGE_ARCHITECTURE_V0.1.md
```

Posibles actualizaciones posteriores, una por una y solo con confirmación:

```text
1. PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1.md
2. PROVIDER_STRATEGY_AND_CANONICAL_CONTRACTS_V0.1.md
3. QA_HARNESS_SPEC_V0.1.md
4. ROADMAP_V0.1.md
5. AI_AGENT_RULES_AND_HANDOFF.md
```

---

## 42. Definition of Done documental

Este documento queda `CERTIFIED` como base documental porque:

```text
1. Está basado en el PDF correcto v1.2 / noviembre 2025.
2. Elimina referencia incorrecta a manual 20221226 v1.0 como fuente principal.
3. Lista endpoints reales por familia funcional.
4. Separa CORE_V1, CONDITIONAL, FUTURE, OUT_OF_SCOPE y DESTRUCTIVE_LAB_ONLY.
5. Mapea contratos Tricor hacia endpoints CVSecurity.
6. Marca implementación como bloqueada hasta laboratorio.
7. No toca archivos dependientes.
```

Implementación sigue `BLOCKED` hasta:

```text
- CVSecurity lab real
- access_token real
- mappings reales
- hardware smoke
- QA Harness actualizado y ejecutado
```

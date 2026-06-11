# USER_MANUAL_PLAN.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** Draft formal inicial / plan maestro de documentación de usuario  
**Fecha:** 2026-06-10  
**Documentos padre:** `PROJECT_CHARTER_TRICOR_HABITAT.md`, `DOMAIN_MODEL_V0.1.md`, `ACCESS_POLICY_CONFIGURATION_V0.1.md`, `CLOUD_EDGE_ARCHITECTURE_V0.1.md`, `QA_HARNESS_SPEC_V0.1.md`, `ROADMAP_V0.1.md`, `AI_AGENT_RULES_AND_HANDOFF.md`  
**Preparado para:** documentación histórica, manuales de usuario, onboarding, soporte, QA, piloto comercial y handoff posterior a Claude Code CLI  
**Idioma base:** Español  

---

## 1. Propósito del documento

Este documento define el plan maestro para crear la documentación de usuario final, documentación operativa y documentación de soporte de Tricor Hábitat.

El objetivo no es escribir todavía todos los manuales finales. El objetivo es definir:

- Qué manuales deben existir.
- Para quién se escribirá cada manual.
- Qué debe cubrir cada manual.
- Qué queda fuera de cada manual.
- Cuándo debe escribirse cada documento.
- Qué documentos deben acompañar el MVP técnico, piloto y comercial.
- Cómo se mantendrá la documentación alineada con el producto real.
- Cómo los agentes IA deben generar y actualizar documentación sin inventar flujos.

Este plan debe impedir que Tricor Hábitat llegue a piloto o venta sin documentación clara para administradores, guardias, residentes, soporte técnico y operación interna.

---

## 2. Principio rector

La documentación de Tricor Hábitat debe seguir una regla central:

```text
La documentación explica flujos reales probados.
No documenta ideas no implementadas.
No promete funciones fuera del alcance validado.
```

Todo manual debe estar conectado a:

- una pantalla existente,
- un rol existente,
- una configuración existente,
- un flujo probado por QA Harness,
- o una operación de soporte definida.

Si una función no existe o no está validada, no debe aparecer como función disponible en manual de usuario final.

---

## 3. Audiencias principales

Tricor Hábitat tendrá documentación para varias audiencias. No todas deben leer lo mismo.

### 3.1 Usuarios internos de Tricor Platform

Usuarios:

- Platform Owner.
- Platform Admin.
- Platform Support Agent.

Necesitan documentación para:

- crear clientes,
- activar/suspender condominios,
- revisar consumo,
- revisar salud operativa,
- apoyar a clientes,
- operar soporte auditado,
- administrar planes y límites SaaS.

No deben operar dinero de mantenimiento del condominio desde Platform.

---

### 3.2 Administradores de condominio

Usuarios:

- Condo Admin.
- Private Admin.
- Finance Operator.

Necesitan documentación para:

- administrar la estructura `Condominio → Privada → Propiedad`,
- crear propiedades,
- vincular propietarios/responsables,
- configurar pagos,
- configurar morosidad,
- administrar credenciales,
- revisar accesos,
- aprobar transferencias manuales fallback,
- revisar auditoría,
- operar excepciones temporales,
- revisar incidencias.

---

### 3.3 Guardias

Usuarios:

- Guard.
- Guard Supervisor.

Necesitan documentación para:

- iniciar sesión,
- seleccionar caseta,
- escanear QR,
- interpretar resultado,
- registrar eventos,
- registrar rondines,
- reportar incidencias,
- actuar ante falla de pluma/proveedor,
- ejecutar apertura manual justificada si tienen rol supervisor,
- crear excepción temporal justificada si tienen rol supervisor y política habilitada.

No deben editar residentes, pagos, permisos permanentes ni credenciales.

---

### 3.4 Residentes

Usuarios:

- propietario/responsable principal,
- residente autorizado bajo una propiedad,
- usuario con acceso a Tricor Resident.

Necesitan documentación para:

- iniciar sesión,
- consultar estado de cuenta,
- consultar multas/cargos,
- pagar en línea,
- subir comprobante de transferencia manual si aplica,
- consultar estado de acceso,
- generar QR residente,
- generar QR de invitado,
- reportar tarjeta/tag perdida,
- entender qué pasa si la propiedad está morosa.

No deben registrar nuevas credenciales por sí mismos.

---

### 3.5 Soporte técnico

Usuarios:

- Platform Support Agent.
- equipo técnico de instalación.
- operador técnico autorizado.

Necesitan documentación para:

- diagnosticar Edge,
- revisar estado de proveedor,
- revisar logs,
- ejecutar pruebas de conexión,
- revisar cola de comandos,
- identificar incidentes,
- elevar problemas correctamente,
- no intervenir flujos financieros ni permisos sin auditoría.

---

## 4. Manuales oficiales requeridos

La documentación se dividirá en manuales independientes para no mezclar roles.

Estructura recomendada:

```text
docs/
└── manuals/
    ├── README_MANUALS.md
    ├── platform/
    │   ├── PLATFORM_USER_MANUAL_V0.1.md
    │   ├── PLATFORM_SUPPORT_MANUAL_V0.1.md
    │   └── PLATFORM_ONBOARDING_CHECKLIST_V0.1.md
    ├── condo/
    │   ├── CONDO_ADMIN_MANUAL_V0.1.md
    │   ├── CONDO_FINANCE_MANUAL_V0.1.md
    │   ├── CONDO_PRIVATE_ADMIN_MANUAL_V0.1.md
    │   └── CONDO_CONFIGURATION_GUIDE_V0.1.md
    ├── guard/
    │   ├── GUARD_USER_MANUAL_V0.1.md
    │   ├── GUARD_SUPERVISOR_MANUAL_V0.1.md
    │   └── GUARD_QUICK_REFERENCE_V0.1.md
    ├── resident/
    │   ├── RESIDENT_USER_MANUAL_V0.1.md
    │   ├── RESIDENT_PAYMENT_GUIDE_V0.1.md
    │   └── RESIDENT_QR_AND_CREDENTIALS_GUIDE_V0.1.md
    ├── technical/
    │   ├── EDGE_TECHNICAL_SUPPORT_GUIDE_V0.1.md
    │   ├── PROVIDER_INSTALLATION_GUIDE_V0.1.md
    │   └── INCIDENT_RESPONSE_RUNBOOK_V0.1.md
    ├── training/
    │   ├── PILOT_TRAINING_PLAN_V0.1.md
    │   ├── CONDO_ADMIN_TRAINING_SCRIPT_V0.1.md
    │   ├── GUARD_TRAINING_SCRIPT_V0.1.md
    │   └── RESIDENT_ONBOARDING_SCRIPT_V0.1.md
    └── release/
        ├── RELEASE_NOTES_TEMPLATE.md
        ├── CHANGELOG_USER_FACING.md
        └── KNOWN_LIMITATIONS_V0.1.md
```

---

## 5. Manual raíz: README_MANUALS.md

### 5.1 Propósito

Debe ser la puerta de entrada a toda la documentación de usuario.

### 5.2 Contenido mínimo

Debe incluir:

- descripción de Tricor Hábitat,
- lista de manuales disponibles,
- qué manual debe leer cada rol,
- versión de documentación,
- fecha de última actualización,
- contacto o canal de soporte,
- advertencia de que las funciones dependen del rol y de la configuración del condominio.

### 5.3 Fuera de alcance

No debe explicar detalles técnicos internos ni lógica del proveedor.

---

## 6. Manual de Tricor Platform

Archivo principal:

```text
PLATFORM_USER_MANUAL_V0.1.md
```

### 6.1 Audiencia

- Platform Owner.
- Platform Admin.

### 6.2 Objetivo

Explicar cómo operar la plataforma SaaS desde el punto de vista del dueño de Tricor Hábitat.

### 6.3 Capítulos requeridos

```text
1. Introducción a Tricor Platform
2. Iniciar sesión
3. Dashboard general
4. Crear cliente/condominio
5. Configurar plan SaaS
6. Activar o suspender cliente
7. Revisar límites y consumo
8. Revisar Edge activos
9. Revisar estado operativo global
10. Revisar incidentes globales
11. Entrar a soporte auditado
12. Revisar historial de soporte
13. Seguridad y buenas prácticas
14. Preguntas frecuentes
```

### 6.4 Flujos que debe explicar

- Crear un nuevo cliente.
- Configurar plan inicial.
- Activar condominio.
- Suspender servicio SaaS sin tocar dinero de mantenimiento.
- Revisar consumo por propiedades, credenciales, zonas y Edge.
- Revisar estado de conexión de Edge.
- Abrir sesión de soporte auditada.

### 6.5 Advertencias obligatorias

Debe indicar claramente:

```text
Tricor Platform no administra dinero de mantenimiento.
Tricor Platform cobra el SaaS por separado.
Los pagos de residentes pertenecen al condominio.
El acceso de soporte debe quedar auditado.
```

---

## 7. Manual de soporte Platform

Archivo:

```text
PLATFORM_SUPPORT_MANUAL_V0.1.md
```

### 7.1 Audiencia

- Platform Support Agent.
- equipo interno autorizado.

### 7.2 Objetivo

Definir cómo brindar soporte sin romper aislamiento de clientes, permisos ni auditoría.

### 7.3 Capítulos requeridos

```text
1. Principios de soporte auditado
2. Cuándo puede entrar soporte
3. Cómo declarar motivo de soporte
4. Qué puede revisar soporte
5. Qué no puede modificar soporte
6. Cómo revisar incidentes
7. Cómo revisar salud de Edge
8. Cómo revisar logs de proveedor
9. Cómo escalar un incidente
10. Cómo cerrar un caso de soporte
```

### 7.4 Reglas obligatorias

```text
Soporte no debe aprobar pagos manuales salvo que tenga rol explícito del cliente.
Soporte no debe modificar permisos permanentes sin autorización.
Soporte no debe operar apertura de puertas.
Soporte no debe crear credenciales de residentes.
Toda acción debe quedar auditada.
```

---

## 8. Checklist de onboarding Platform

Archivo:

```text
PLATFORM_ONBOARDING_CHECKLIST_V0.1.md
```

### 8.1 Objetivo

Crear una lista repetible para dar de alta un nuevo condominio o privada independiente.

### 8.2 Checklist mínimo

```text
[ ] Crear cliente en Tricor Platform
[ ] Definir tipo comercial visible
[ ] Crear estructura interna Condominio → Privada → Propiedad
[ ] Configurar plan SaaS
[ ] Configurar límites iniciales
[ ] Activar módulos permitidos
[ ] Configurar cuenta de pago del condominio
[ ] Configurar política de morosidad
[ ] Configurar proveedor de acceso
[ ] Instalar Edge si aplica
[ ] Ejecutar QA de instalación
[ ] Crear usuarios CondoAdmin
[ ] Crear usuarios Finance
[ ] Crear usuarios GuardSupervisor
[ ] Crear usuarios Guard
[ ] Cargar propiedades iniciales
[ ] Validar primera propiedad de prueba
[ ] Validar flujo pago → restauración
[ ] Validar QR residente
[ ] Validar QR invitado
[ ] Validar credencial perdida
[ ] Capacitar administración
[ ] Capacitar guardias
[ ] Entregar manuales iniciales
```

---

## 9. Manual de Tricor Condo Admin

Archivo principal:

```text
CONDO_ADMIN_MANUAL_V0.1.md
```

### 9.1 Audiencia

- Condo Admin.

### 9.2 Objetivo

Explicar cómo operar el condominio desde Tricor Condo.

### 9.3 Capítulos requeridos

```text
1. Introducción a Tricor Condo
2. Roles dentro del condominio
3. Dashboard Condo
4. Estructura: Condominio, Privada y Propiedad
5. Crear y editar privadas
6. Crear y editar propiedades
7. Asignar propietario/responsable principal
8. Administrar límites de credenciales
9. Administrar usuarios y roles
10. Configurar pagos
11. Configurar morosidad
12. Configurar accesos y perfiles
13. Configurar QR
14. Configurar guardias
15. Revisar auditoría
16. Revisar incidentes
17. Revisar credenciales obsoletas
18. Gestionar mudanza o cambio de responsable
19. Gestionar excepciones temporales
20. Buenas prácticas operativas
```

### 9.4 Flujos obligatorios

Debe explicar paso a paso:

- Crear privada.
- Crear propiedad.
- Asignar responsable principal.
- Configurar límite de credenciales por propiedad.
- Crear usuarios Finance.
- Crear usuarios PrivateAdmin.
- Crear guardias.
- Revisar estado de propiedad morosa.
- Revisar qué permisos se restringen por morosidad.
- Iniciar cambio de responsable o mudanza.
- Revisar historial de auditoría.

### 9.5 Advertencias obligatorias

```text
Condo Admin puede configurar reglas, pero no debe saltarse auditoría.
Condo Admin no debe usar ajustes manuales como flujo normal de cobranza.
La configuración incorrecta puede afectar accesos.
El sistema debe validar configuración antes de guardar.
```

---

## 10. Manual de Finanzas dentro de Tricor Condo

Archivo:

```text
CONDO_FINANCE_MANUAL_V0.1.md
```

### 10.1 Audiencia

- Finance Operator.
- Condo Admin que supervise finanzas.

### 10.2 Objetivo

Explicar el flujo financiero operativo de cuotas, multas, cargos, pagos, comprobantes y fallback manual.

### 10.3 Capítulos requeridos

```text
1. Qué hace Finanzas en Tricor Condo
2. Qué no hace Finanzas
3. Estados de cuenta por propiedad
4. Cargos de mantenimiento
5. Multas
6. Cargos extraordinarios
7. Pago en línea
8. Pago pendiente por proveedor
9. Pago confirmado
10. Transferencia manual fallback
11. Subida de comprobante
12. Aprobación manual auditada
13. Rechazo de comprobante
14. Restauración automática vs restauración manual auditada
15. Auditoría de pagos
16. Reportes básicos
17. Errores comunes
```

### 10.4 Reglas de negocio que debe explicar

```text
Pago confirmado por proveedor restaura acceso automáticamente.
Transferencia manual requiere revisión y aprobación.
Comprobante no restaura acceso por sí solo.
Finance aprueba pagos manuales; no edita permisos directamente.
Cuando Finance aprueba un pago manual, el sistema recalcula acceso.
```

### 10.5 Flujos obligatorios

- Revisar deuda de propiedad.
- Revisar pago en línea confirmado.
- Revisar pago pendiente.
- Aprobar transferencia manual fallback.
- Rechazar comprobante.
- Auditar restauración posterior a pago.

---

## 11. Manual de Private Admin

Archivo:

```text
CONDO_PRIVATE_ADMIN_MANUAL_V0.1.md
```

### 11.1 Audiencia

- Private Admin.
- presidente o encargado de privada.

### 11.2 Objetivo

Explicar qué puede hacer un administrador de privada sin invadir información de otras privadas.

### 11.3 Capítulos requeridos

```text
1. Qué es Private Admin
2. Alcance de visibilidad
3. Ver propiedades de mi privada
4. Ver responsables de mi privada
5. Solicitar cambios de responsable
6. Registrar incidencias de privada
7. Revisar estado resumido de acceso
8. Qué no puede hacer Private Admin
9. Cómo escalar a Condo Admin
```

### 11.4 Reglas obligatorias

```text
Private Admin solo ve su privada.
Private Admin no ve otras privadas.
Private Admin no aprueba pagos salvo que tenga rol adicional.
Private Admin no configura proveedor.
Private Admin no edita reglas globales de morosidad.
```

---

## 12. Guía de configuración de Tricor Condo

Archivo:

```text
CONDO_CONFIGURATION_GUIDE_V0.1.md
```

### 12.1 Audiencia

- Condo Admin.
- Platform Support Agent.
- usuario técnico autorizado.

### 12.2 Objetivo

Documentar los presets y opciones avanzadas sin saturar al usuario final.

### 12.3 Capítulos requeridos

```text
1. Principio de configuración controlada
2. Presets disponibles
3. Política Suave
4. Política Estándar
5. Política Estricta
6. Política Personalizada
7. Configuración de pagos
8. Configuración de morosidad
9. Configuración de accesos
10. Configuración de QR
11. Configuración de credenciales
12. Configuración de guardias
13. Configuración de roles
14. Configuración de Edge
15. Configuración de proveedor
16. Validaciones antes de guardar
17. Vista de impacto
18. Errores de configuración frecuentes
```

### 12.4 Regla de diseño documental

No se deben mostrar 200 opciones al usuario normal. La documentación debe presentar:

```text
Preset primero.
Opciones avanzadas después.
Modo técnico solo cuando aplique.
```

### 12.5 Configuraciones que debe cubrir

- Bloquear QR invitados por morosidad.
- Bloquear acceso vehicular por morosidad.
- Mantener peatonal permitido por default.
- Bloquear zonas no esenciales.
- Restaurar con pago confirmado.
- Permitir transferencia manual fallback.
- Permitir excepción temporal auditada.
- Permitir apertura manual por GuardSupervisor.
- Limitar credenciales por propiedad.
- Alertar credenciales sin uso.
- Revocar credenciales de exresponsables cuando corresponda.

---

## 13. Manual de Tricor Guard

Archivo principal:

```text
GUARD_USER_MANUAL_V0.1.md
```

### 13.1 Audiencia

- Guard.

### 13.2 Objetivo

Explicar operación diaria de guardia normal.

### 13.3 Capítulos requeridos

```text
1. Introducción a Tricor Guard
2. Iniciar sesión
3. Seleccionar caseta
4. Pantalla principal
5. Escanear QR
6. Interpretar resultado
7. Acceso permitido
8. Acceso suspendido
9. QR inválido
10. QR expirado
11. Falla de comunicación
12. Registrar evento
13. Registrar rondín
14. Reportar incidente
15. Reportar falla de pluma/proveedor
16. Qué hacer si el sistema está lento
17. Qué no puede hacer un guardia normal
```

### 13.4 Mensajes que debe aprender el guardia

El manual debe explicar mensajes operativos simples:

```text
Permitido
Suspendido
Inválido
Expirado
Pendiente
Requiere supervisor
Falla de comunicación
```

### 13.5 Reglas obligatorias

```text
Guardia normal no edita residentes.
Guardia normal no edita pagos.
Guardia normal no registra credenciales.
Guardia normal no abre manualmente sin política habilitada.
Guardia normal no debe ver detalle financiero sensible.
```

---

## 14. Manual de Guard Supervisor

Archivo:

```text
GUARD_SUPERVISOR_MANUAL_V0.1.md
```

### 14.1 Audiencia

- Guard Supervisor.

### 14.2 Objetivo

Explicar acciones superiores de guardia con auditoría.

### 14.3 Capítulos requeridos

```text
1. Diferencia entre Guard y Guard Supervisor
2. Responsabilidad operativa
3. Apertura manual justificada
4. Motivos permitidos de apertura manual
5. Excepción temporal justificada
6. Duración máxima de excepción temporal
7. Notificación a Condo Admin
8. Auditoría de acciones
9. Revisión de eventos de guardia
10. Reporte de fallas operativas
11. Escalamiento a administración
12. Acciones prohibidas
```

### 14.4 Reglas obligatorias

```text
GuardSupervisor puede abrir manualmente con motivo.
GuardSupervisor puede crear excepción temporal si la política lo permite.
Toda acción requiere justificación.
Toda acción se audita.
GuardSupervisor no edita residentes.
GuardSupervisor no edita pagos.
GuardSupervisor no registra nuevas credenciales.
```

### 14.5 Motivos rápidos recomendados

```text
QR válido, pluma no respondió
Falla de proveedor
Emergencia
Autorizado por administración
Mudanza
Otro
```

---

## 15. Tarjeta rápida de guardia

Archivo:

```text
GUARD_QUICK_REFERENCE_V0.1.md
```

### 15.1 Objetivo

Documento de una o dos páginas para caseta.

Debe explicar:

- cómo iniciar turno,
- cómo escanear QR,
- qué significa cada resultado,
- cuándo llamar a supervisor,
- cuándo registrar incidente,
- qué nunca debe hacer un guardia.

### 15.2 Formato recomendado

```text
Una página.
Lenguaje simple.
Con iconos o estados visuales cuando exista UI final.
Sin explicación técnica.
```

---

## 16. Manual de Tricor Resident

Archivo principal:

```text
RESIDENT_USER_MANUAL_V0.1.md
```

### 16.1 Audiencia

- propietario/responsable principal,
- usuario residente autorizado.

### 16.2 Objetivo

Explicar cómo usar la interfaz residente sin convertirla en app social.

### 16.3 Capítulos requeridos

```text
1. Introducción a Tricor Resident
2. Iniciar sesión
3. Ver mi propiedad
4. Ver estado de cuenta
5. Ver cargos y multas
6. Pagar en línea
7. Ver pago pendiente
8. Ver pago confirmado
9. Subir comprobante de transferencia manual
10. Ver estado de acceso
11. Usar QR residente
12. Crear QR de invitado
13. Reportar tarjeta/tag perdida
14. Ver avisos mínimos
15. Preguntas frecuentes
```

### 16.4 Reglas que debe explicar

```text
El pago en línea confirmado puede restaurar acceso automáticamente.
La transferencia manual requiere revisión del condominio.
El comprobante no siempre activa acceso de inmediato.
Si la propiedad está morosa, algunas funciones pueden bloquearse.
El residente puede reportar tarjeta/tag perdida.
El residente no puede registrar una nueva tarjeta/tag por sí mismo.
```

---

## 17. Guía de pagos Resident

Archivo:

```text
RESIDENT_PAYMENT_GUIDE_V0.1.md
```

### 17.1 Objetivo

Explicar pagos sin confundir al residente.

### 17.2 Capítulos requeridos

```text
1. Qué puedo pagar desde Tricor Resident
2. Cómo pagar en línea
3. Qué significa pago pendiente
4. Qué significa pago confirmado
5. Qué pasa si pago por transferencia manual
6. Cómo subir comprobante
7. Cuándo se restaura acceso
8. Qué pasa con pagos parciales
9. Qué hacer si mi pago no aparece
10. A quién contactar
```

### 17.3 Mensaje clave

```text
Si el pago se confirma por proveedor, Tricor puede restaurar acceso automáticamente.
Si el pago fue manual, Finanzas debe revisarlo.
```

---

## 18. Guía de QR y credenciales Resident

Archivo:

```text
RESIDENT_QR_AND_CREDENTIALS_GUIDE_V0.1.md
```

### 18.1 Objetivo

Explicar acceso residente, invitados y tarjetas/tags.

### 18.2 Capítulos requeridos

```text
1. Qué es una credencial
2. Tipos de credencial
3. Límite de credenciales por propiedad
4. QR residente
5. QR invitado
6. QR expirado
7. QR bloqueado por morosidad
8. Tarjeta/tag perdida
9. Cómo reportar pérdida
10. Qué pasa después de reportar pérdida
11. Cómo solicitar reemplazo a administración
```

### 18.3 Reglas obligatorias

```text
Las credenciales pertenecen a la propiedad y quedan bajo responsabilidad del propietario/responsable principal.
El residente puede reportar pérdida.
Administración registra reemplazos.
Una credencial reportada como perdida debe dejar de permitir acceso según flujo de seguridad.
```

---

## 19. Guía técnica local de Edge

Archivo:

```text
EDGE_TECHNICAL_SUPPORT_GUIDE_V0.1.md
```

### 19.1 Audiencia

- soporte técnico,
- instalador autorizado,
- Platform Support Agent.

### 19.2 Objetivo

Documentar la UI local técnica del Edge sin exponer operación administrativa.

### 19.3 Capítulos requeridos

```text
1. Qué es Edge
2. Qué no es Edge
3. Cómo acceder a UI local técnica
4. Ver estado de conexión Cloud
5. Ver estado de proveedor
6. Ver último heartbeat
7. Ver última sincronización
8. Ver cola de comandos
9. Ver logs recientes
10. Probar conexión con proveedor
11. Diagnosticar modo offline
12. Reiniciar servicio de forma segura
13. Exportar diagnóstico
14. Escalar incidente
```

### 19.4 Reglas obligatorias

```text
Edge no administra residentes.
Edge no administra pagos.
Edge no modifica permisos permanentes desde UI local.
Edge no reemplaza al Cloud.
Cloud es fuente de verdad.
```

---

## 20. Guía de instalación de proveedor

Archivo:

```text
PROVIDER_INSTALLATION_GUIDE_V0.1.md
```

### 20.1 Audiencia

- equipo técnico.
- soporte autorizado.

### 20.2 Objetivo

Definir cómo conectar un proveedor de acceso sin contaminar el dominio de Tricor.

### 20.3 Capítulos requeridos

```text
1. Principio de provider adapter
2. ProviderInstallation
3. ProviderCapabilityMatrix
4. ProviderMapping
5. Mapeo de puertas
6. Mapeo de zonas
7. Mapeo de perfiles activos
8. Mapeo de perfil moroso
9. Prueba de conexión
10. Prueba de lectura
11. Prueba de comando
12. Prueba de restauración
13. Prueba de restricción
14. Incidentes comunes
15. Criterios de activación
```

### 20.4 Reglas obligatorias

```text
No se activa proveedor sin prueba de conexión.
No se activa proveedor sin mappings mínimos.
No se activa proveedor sin QA de provider contract.
No se asume que todos los proveedores soportan todas las capacidades.
```

---

## 21. Runbook de respuesta a incidentes

Archivo:

```text
INCIDENT_RESPONSE_RUNBOOK_V0.1.md
```

### 21.1 Audiencia

- Platform Support Agent.
- Condo Admin.
- Guard Supervisor.
- equipo técnico.

### 21.2 Objetivo

Definir qué hacer cuando algo falla.

### 21.3 Incidentes mínimos

```text
1. Edge offline
2. Proveedor no responde
3. Pago confirmado pero acceso pendiente
4. QR válido pero pluma no abre
5. QR inválido reportado por residente
6. Credencial perdida
7. Credencial activa de exresponsable
8. Error de configuración de morosidad
9. Comprobante manual rechazado
10. Apertura manual fuera de política
11. Drift entre Tricor y proveedor
```

### 21.4 Formato por incidente

Cada incidente debe documentarse así:

```text
Nombre del incidente
Síntomas
Impacto
Quién puede verlo
Quién puede resolverlo
Pasos de diagnóstico
Acción recomendada
Acción prohibida
Qué queda auditado
Cómo cerrar incidente
```

---

## 22. Plan de capacitación piloto

Archivo:

```text
PILOT_TRAINING_PLAN_V0.1.md
```

### 22.1 Objetivo

Organizar capacitación inicial para piloto real.

### 22.2 Sesiones mínimas

```text
Sesión 1 — Administración general
Audiencia: Condo Admin
Duración sugerida: 60-90 min

Sesión 2 — Finanzas y pagos
Audiencia: Finance Operator + Condo Admin
Duración sugerida: 60 min

Sesión 3 — Guardia
Audiencia: Guard + GuardSupervisor
Duración sugerida: 45-60 min

Sesión 4 — Residentes
Audiencia: residentes piloto o comité
Duración sugerida: 30-45 min

Sesión 5 — Soporte y contingencias
Audiencia: Condo Admin + GuardSupervisor + soporte Tricor
Duración sugerida: 45-60 min
```

### 22.3 Materiales requeridos

- manuales PDF o web,
- guías rápidas,
- checklist de primera semana,
- contactos de soporte,
- lista de incidentes comunes,
- criterios de escalamiento.

---

## 23. Scripts de capacitación

### 23.1 Condo Admin Training Script

Archivo:

```text
CONDO_ADMIN_TRAINING_SCRIPT_V0.1.md
```

Debe cubrir:

- estructura,
- propiedades,
- responsables,
- roles,
- pagos,
- morosidad,
- credenciales,
- auditoría,
- soporte.

### 23.2 Guard Training Script

Archivo:

```text
GUARD_TRAINING_SCRIPT_V0.1.md
```

Debe cubrir:

- escanear QR,
- interpretar estados,
- registrar eventos,
- escalamiento,
- apertura manual supervisor,
- incidentes.

### 23.3 Resident Onboarding Script

Archivo:

```text
RESIDENT_ONBOARDING_SCRIPT_V0.1.md
```

Debe cubrir:

- login,
- estado de cuenta,
- pago,
- QR,
- invitados,
- credencial perdida,
- morosidad.

---

## 24. Release notes y documentación histórica

Tricor Hábitat debe documentarse como producto serio e histórico de desarrollo.

Archivos requeridos:

```text
RELEASE_NOTES_TEMPLATE.md
CHANGELOG_USER_FACING.md
KNOWN_LIMITATIONS_V0.1.md
```

### 24.1 RELEASE_NOTES_TEMPLATE.md

Debe incluir:

```text
Versión
Fecha
Resumen
Nuevas funciones
Cambios operativos
Cambios de configuración
Cambios de permisos
Correcciones
Impacto para usuarios
Impacto para soporte
Pruebas QA ejecutadas
Limitaciones conocidas
```

### 24.2 CHANGELOG_USER_FACING.md

Debe explicar cambios para usuarios no técnicos.

No debe incluir commits, detalles internos ni información técnica innecesaria.

### 24.3 KNOWN_LIMITATIONS_V0.1.md

Debe listar límites conocidos de forma honesta.

Ejemplos:

```text
La app móvil nativa no está incluida en v1.
Guard y Resident inician como PWA móvil.
Algunas capacidades dependen del proveedor instalado.
Transferencia manual requiere revisión humana.
```

---

## 25. Principios de estilo documental

Todos los manuales deben usar:

```text
Lenguaje claro.
Pasos numerados.
Capturas cuando exista UI estable.
Advertencias visibles.
Tablas simples.
Glosario de términos.
Escenarios reales.
Preguntas frecuentes.
```

Deben evitar:

```text
Jerga técnica innecesaria.
Explicar arquitectura interna a usuarios finales.
Prometer funciones futuras como si ya existieran.
Mezclar roles.
Exponer configuración técnica a usuarios normales.
Duplicar instrucciones contradictorias.
```

---

## 26. Uso de capturas de pantalla

Las capturas deben agregarse después de que la UI esté estable.

Regla:

```text
Primero manual textual.
Después capturas.
Después video o capacitación.
```

No se deben tomar capturas de:

- datos reales de residentes,
- montos reales no anonimizados,
- credenciales reales,
- configuraciones sensibles,
- logs técnicos con secretos.

---

## 27. Glosario obligatorio

Todos los manuales deben compartir un glosario base.

Términos mínimos:

```text
Tricor Platform
Tricor Condo
Tricor Guard
Tricor Resident
Condominio
Privada
Propiedad
Propietario/responsable principal
Credencial
QR residente
QR invitado
Morosidad
Pago en línea
Transferencia manual fallback
Comprobante
Finance Operator
Condo Admin
Private Admin
Guard
GuardSupervisor
Edge
Proveedor de acceso
Perfil de acceso
Zona de acceso
Acceso peatonal
Acceso vehicular
Excepción temporal
Apertura manual
Auditoría
Incidente
```

---

## 28. Relación con QA Harness

La documentación de usuario debe alinearse con flujos probados.

Ejemplo:

| Manual | Flujo QA relacionado |
|---|---|
| CONDO_FINANCE_MANUAL | `manual-payment-fallback-lifecycle` |
| RESIDENT_PAYMENT_GUIDE | `resident-pay-confirmed-restore` |
| GUARD_USER_MANUAL | `guard-qr-valid-access` |
| GUARD_SUPERVISOR_MANUAL | `guard-supervisor-manual-open` |
| CONDO_CONFIGURATION_GUIDE | `qa:config:dependencies` |
| EDGE_TECHNICAL_SUPPORT_GUIDE | `qa:edge:offline-reconnect` |
| PROVIDER_INSTALLATION_GUIDE | `qa:provider-contract` |
| RESIDENT_QR_AND_CREDENTIALS_GUIDE | `lost-credential` |

Regla:

```text
Si un manual describe un flujo, debe existir prueba QA equivalente o pendiente explícito.
```

---

## 29. Secuencia recomendada de creación de manuales

No todos los manuales deben escribirse al mismo tiempo.

### 29.1 Durante MVP técnico

Crear:

```text
README_MANUALS.md
EDGE_TECHNICAL_SUPPORT_GUIDE_V0.1.md
PROVIDER_INSTALLATION_GUIDE_V0.1.md
INCIDENT_RESPONSE_RUNBOOK_V0.1.md
```

Objetivo:

- soporte técnico,
- instalación,
- diagnóstico,
- QA.

### 29.2 Durante MVP piloto

Crear:

```text
CONDO_ADMIN_MANUAL_V0.1.md
CONDO_FINANCE_MANUAL_V0.1.md
GUARD_USER_MANUAL_V0.1.md
GUARD_SUPERVISOR_MANUAL_V0.1.md
RESIDENT_USER_MANUAL_V0.1.md
PILOT_TRAINING_PLAN_V0.1.md
```

Objetivo:

- operar primer piloto real.

### 29.3 Antes de MVP comercial

Crear o pulir:

```text
PLATFORM_USER_MANUAL_V0.1.md
PLATFORM_SUPPORT_MANUAL_V0.1.md
PLATFORM_ONBOARDING_CHECKLIST_V0.1.md
CONDO_CONFIGURATION_GUIDE_V0.1.md
GUARD_QUICK_REFERENCE_V0.1.md
RESIDENT_PAYMENT_GUIDE_V0.1.md
RESIDENT_QR_AND_CREDENTIALS_GUIDE_V0.1.md
CHANGELOG_USER_FACING.md
KNOWN_LIMITATIONS_V0.1.md
```

Objetivo:

- venta,
- onboarding repetible,
- soporte escalable,
- entrenamiento.

---

## 30. Manuales fuera de alcance v1

No crear manuales formales para módulos no incluidos en v1.

Fuera de alcance:

```text
Chat entre residentes.
Amenidades como módulo completo.
Facturación propia.
Contabilidad fiscal completa.
App móvil nativa.
Automatizaciones libres por cliente.
Marketplace.
```

Si se mencionan, debe ser solo en `KNOWN_LIMITATIONS_V0.1.md` como no incluido o futuro no comprometido.

---

## 31. Control de versiones de documentación

Cada manual debe incluir:

```text
Proyecto
Nombre del manual
Versión del documento
Estado
Fecha
Audiencia
Roles aplicables
Producto/release aplicable
Última revisión
```

Estados permitidos:

```text
Draft
Pilot Ready
Commercial Ready
Deprecated
Archived
```

---

## 32. Criterios de aceptación por manual

Un manual se considera listo cuando:

```text
[ ] Tiene audiencia definida.
[ ] Tiene roles aplicables.
[ ] Explica flujos reales.
[ ] No promete funciones no implementadas.
[ ] Incluye advertencias de permisos.
[ ] Incluye pasos operativos.
[ ] Incluye errores comunes.
[ ] Tiene glosario o referencia al glosario.
[ ] Está alineado con QA Harness.
[ ] Fue revisado por Product Owner.
[ ] No contiene información sensible.
[ ] No mezcla instrucciones de otros roles.
```

---

## 33. Reglas para Claude Code CLI

Cuando Claude Code CLI genere manuales, debe obedecer estas reglas:

```text
1. Usar este USER_MANUAL_PLAN.md como fuente base.
2. Usar documentos padre como fuente de verdad.
3. No inventar funciones.
4. No documentar funciones fuera de v1 como disponibles.
5. Separar manuales por rol.
6. No mezclar Platform, Condo, Guard y Resident.
7. No exponer detalles técnicos a usuario final.
8. No exponer secretos, tokens, credenciales ni datos reales.
9. No modificar reglas de negocio desde un manual.
10. Si falta flujo implementado, marcarlo como pendiente, no inventarlo.
```

---

## 34. Prompt recomendado para generar un manual específico

Plantilla:

```text
Genera el archivo <NOMBRE_MANUAL>.md para Tricor Hábitat.
Usa como fuente de verdad:
- PROJECT_CHARTER_TRICOR_HABITAT.md
- DOMAIN_MODEL_V0.1.md
- ACCESS_POLICY_CONFIGURATION_V0.1.md
- CLOUD_EDGE_ARCHITECTURE_V0.1.md
- QA_HARNESS_SPEC_V0.1.md
- ROADMAP_V0.1.md
- USER_MANUAL_PLAN.md

Audiencia del manual: <ROL>
Alcance: <ALCANCE>
No inventes funciones.
No documentes módulos fuera de v1 como disponibles.
Incluye pasos operativos, advertencias, errores comunes, glosario mínimo y criterios de soporte.
```

---

## 35. Plantilla base para cada manual

Todos los manuales deben partir de esta estructura:

```text
# <NOMBRE DEL MANUAL>

Proyecto: Tricor Hábitat
Versión: v0.1
Estado: Draft / Pilot Ready / Commercial Ready
Audiencia: <roles>
Fecha: <fecha>

## 1. Propósito
## 2. Audiencia
## 3. Qué puedes hacer con este manual
## 4. Qué no cubre este manual
## 5. Conceptos básicos
## 6. Requisitos previos
## 7. Flujo principal
## 8. Pasos detallados
## 9. Estados y mensajes
## 10. Errores comunes
## 11. Seguridad y permisos
## 12. Preguntas frecuentes
## 13. Cuándo contactar soporte
## 14. Glosario
## 15. Historial de cambios
```

---

## 36. Decisiones documentales cerradas

```text
1. Habrá manuales separados por superficie y rol.
2. Platform, Condo, Guard y Resident no se mezclarán en un solo manual.
3. Finanzas tendrá manual propio dentro de Tricor Condo.
4. Guard y GuardSupervisor tendrán documentación separada.
5. Resident tendrá guías separadas para pagos y credenciales/QR.
6. Edge tendrá guía técnica, no manual de usuario final.
7. Provider installation tendrá guía técnica separada.
8. La documentación se alineará con QA Harness.
9. Las funciones no implementadas no se documentarán como disponibles.
10. La documentación será parte del producto comercial e histórico del proyecto.
```

---

## 37. Próximos documentos recomendados

Después de este plan, la secuencia recomendada es:

```text
1. REPO_STRUCTURE_V0.1.md
2. DATABASE_SCHEMA_PLAN_V0.1.md
3. API_CONTRACTS_V0.1.md
4. PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md
5. PROVIDER_RESEARCH_ACCESS_V0.1.md
6. CLAUDE.md
```

Los manuales específicos deben generarse después de tener pantallas, rutas y flujos implementados o al menos especificados en API/UI.

---

## 38. Estado final del documento

Este documento queda como fuente de verdad inicial para la documentación de usuario de Tricor Hábitat.

Debe usarse para planear manuales, capacitación, onboarding, soporte y documentación histórica del producto.

No reemplaza los manuales finales. Define cómo deben construirse.

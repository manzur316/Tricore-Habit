# PROJECT_CHARTER_TRICOR_HABITAT.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** Draft formal inicial / fuente de verdad provisional  
**Fecha:** 2026-06-10  
**Preparado para:** Arquitectura, roadmap, documentación histórica y handoff posterior a Claude Code CLI  
**Idioma base:** Español  

---

## 1. Propósito del documento

Este documento establece la visión, límites, arquitectura conceptual, reglas de producto y criterios iniciales de Tricor Hábitat.

Su objetivo es evitar que el desarrollo avance por parches, suposiciones o decisiones aisladas. A partir de este documento, cualquier implementación posterior debe partir de una fuente de verdad clara, validable y auditable.

Este charter no es todavía la especificación técnica completa. Es el documento rector inicial que define:

- Qué es Tricor Hábitat.
- Qué problema resuelve.
- Qué entra en la versión 1.
- Qué queda prohibido o fuera de alcance.
- Cuáles son las superficies del producto.
- Cómo deben separarse pagos, accesos, morosidad, Edge, proveedores y usuarios.
- Cómo debe prepararse el proyecto para desarrollo con agentes de código mediante CLI.

---

## 2. Definición del producto

**Tricor Hábitat** es una plataforma SaaS + Edge para administración operativa de condominios, privadas y comunidades residenciales, enfocada principalmente en control de acceso vinculado a pagos, morosidad, permisos y operación local.

El núcleo comercial del producto es:

```text
Pagos → Morosidad → Permisos de acceso → Ejecución en proveedor → Auditoría
```

Tricor Hábitat no debe ser entendido como una app social, un sistema contable completo ni una plataforma de amenidades. Su primera versión debe resolver un problema concreto y cobrable: automatizar y controlar accesos residenciales según el estado de pago de cada propiedad, con reglas configurables y auditoría completa.

---

## 3. Visión

Crear una plataforma profesional, vendible y escalable para condominios pequeños y grandes, capaz de controlar accesos, pagos, morosidad, credenciales, permisos, guardias y operación local sin desarrollar un sistema diferente para cada cliente.

La flexibilidad debe venir de configuración controlada, perfiles, reglas, capacidades del proveedor y QA Harness, no de parches o personalizaciones manuales.

---

## 4. Problema que resuelve

En muchas privadas y condominios, el control de acceso y el control de pagos están separados. Esto genera problemas operativos:

- Residentes morosos conservan accesos activos.
- Exresidentes o expropietarios conservan tarjetas, tags o credenciales antiguas.
- La administración depende de procesos manuales para revisar comprobantes.
- Los guardias no siempre tienen información confiable.
- Los sistemas de acceso físicos no están alineados con la cobranza.
- Las reglas cambian entre privadas, pero el software tradicional no se adapta bien.
- El control de auditoría suele ser débil o inexistente.

Tricor Hábitat busca resolver esto con una plataforma centralizada y auditable, conectada a proveedores de acceso mediante adaptadores canónicos y Edge local cuando aplique.

---

## 5. Promesa comercial inicial

Tricor Hábitat permite que un condominio controle accesos automáticamente según pagos, reglas de morosidad y configuración operativa, sin perder auditoría ni flexibilidad.

Promesa v1:

```text
“Controla accesos residenciales según pagos y morosidad, con reglas configurables, guardias operativos, residentes conectados, Edge local y auditoría.”
```

---

## 6. Cliente ideal

Tricor Hábitat debe servir para:

- Privadas pequeñas.
- Condominios medianos.
- Fraccionamientos con varias privadas.
- Residenciales grandes con varias casetas.
- Comunidades con estructuras simples o escalables.

La plataforma debe funcionar para clientes pequeños sin imponerles complejidad innecesaria, pero debe estar preparada para clientes grandes con varias privadas, varias casetas, múltiples Edge y reglas diferenciadas.

---

## 7. Superficies del producto

Tricor Hábitat se divide en cuatro superficies funcionales. No representan necesariamente cuatro apps separadas desde v1. Representan entradas, roles, permisos y experiencias separadas dentro de un producto modular.

### 7.1 Tricor Platform

Panel interno del dueño/equipo SaaS.

Responsabilidades:

- Administrar clientes.
- Crear, activar o suspender condominios.
- Administrar planes, licencias y límites.
- Ver uso por condominio.
- Ver salud de Edge e instalaciones.
- Ver proveedores conectados.
- Revisar incidentes globales.
- Operar soporte con sesiones auditadas.
- Cobrar el SaaS a cada condominio.

Tricor Platform no debe administrar el dinero de mantenimiento del condominio.

### 7.2 Tricor Condo

Panel del cliente: administración del condominio, privada o comunidad residencial.

Responsabilidades:

- Gestionar condominios, privadas y propiedades.
- Gestionar propietarios/responsables.
- Gestionar credenciales autorizadas.
- Gestionar pagos, multas y cuotas.
- Gestionar morosidad.
- Configurar reglas de acceso.
- Configurar proveedor de acceso.
- Configurar Edge.
- Revisar auditoría.
- Revisar eventos operativos.
- Gestionar roles internos como finanzas, administradores de privada y guardias.

Dentro de Tricor Condo vivirá el módulo de finanzas. No se creará una superficie separada llamada “Finance”.

### 7.3 Tricor Guard

Interfaz para guardias.

En v1 debe iniciar como web/PWA móvil, no como app móvil nativa obligatoria.

Responsabilidades:

- Escanear QR.
- Consultar estado operativo de acceso.
- Registrar eventos.
- Registrar rondines o reportes básicos.
- Reportar fallas de pluma/proveedor.
- Permitir apertura con QR válido según política.
- Permitir apertura manual y excepción temporal solo al rol autorizado.

Tricor Guard no debe editar residentes, pagos, propiedades, reglas globales ni credenciales nuevas.

### 7.4 Tricor Resident

Interfaz para residentes/propietarios.

En v1 debe iniciar como web/PWA móvil, con APIs preparadas para una app móvil futura.

Responsabilidades:

- Ver estado de cuenta.
- Ver deudas, multas y pagos.
- Pagar desde la plataforma mediante proveedor de pago.
- Subir comprobantes cuando aplique.
- Ver estado de acceso.
- Generar QR de invitado si la política lo permite.
- Usar QR residente si la política y el proveedor lo permiten.
- Reportar tarjeta/tag perdida.

Tricor Resident no debe permitir registrar nuevas tarjetas/tags por cuenta propia.

---

## 8. Estrategia web, PWA y móvil

La versión 1 no debe iniciar con aplicaciones móviles nativas completas. El enfoque aprobado es:

```text
Backend sólido + Web/PWA móvil + APIs mobile-ready
```

Tricor Guard y Tricor Resident deben arrancar como interfaces web/PWA móviles, pero los contratos, rutas, permisos y modelos deben quedar preparados desde el día 1 para una app móvil futura, preferentemente en Flutter.

La futura app móvil no debe requerir rediseñar el backend.

---

## 9. Jerarquía organizacional

La estructura interna oficial es:

```text
Condominio → Privada → Propiedad
```

### 9.1 Condominio

Entidad raíz comercial y operativa dentro de Tricor Hábitat.

Puede representar:

- Condominio.
- Privada independiente.
- Comunidad residencial.
- Fraccionamiento.
- Residencial.

La UI podrá adaptar etiquetas, pero la estructura interna no debe romperse.

### 9.2 Privada

Agrupador intermedio dentro de un condominio.

Puede representar:

- Privada.
- Sección.
- Etapa.
- Zona.
- Área principal.

Aunque una comunidad sea pequeña, debe mantenerse una privada interna para no romper permisos, reportes, roles ni QA Harness.

### 9.3 Propiedad

Unidad final operativa y financiera.

Puede representar:

- Casa.
- Departamento.
- Lote.
- Vivienda.
- Local residencial.

La palabra oficial en el dominio será **Propiedad**.

---

## 10. Modelo de propiedad, propietario y credenciales

La morosidad y los pagos se controlan por propiedad.

Reglas:

- Una propiedad tiene un propietario/responsable principal.
- Una propiedad no debe tener múltiples propietarios principales.
- Un propietario puede tener varias propiedades.
- Una propiedad puede tener varias credenciales activas, limitadas por configuración.
- No se hará censo completo de habitantes en v1.
- Las personas adicionales no deben convertirse en propietarios paralelos.
- Las credenciales se controlan por propiedad y responsable principal.

Modelo conceptual:

```text
Propiedad
├── Propietario/responsable principal
├── Límite de credenciales
├── Credenciales activas
├── Estado de pago
├── Estado de acceso
└── Historial/auditoría
```

Ejemplo:

```text
Propiedad A-101
Propietario: Juan Pérez
Límite: 3 credenciales activas
Credenciales:
- TAG-001
- TAG-002
- QR residente
```

No es necesario identificar que TAG-001 pertenece a un familiar y TAG-002 a otra persona desde v1. Lo esencial es saber que esas credenciales están autorizadas por la propiedad y bajo responsabilidad del propietario.

---

## 11. Núcleo funcional de v1

La versión 1 debe resolver:

```text
Control de acceso + pagos + morosidad + credenciales + guardia básico + residente básico + Edge + auditoría
```

### 11.1 Flujo principal

```text
1. Se crea condominio.
2. Se crea privada.
3. Se crea propiedad.
4. Se asigna propietario/responsable.
5. Se configuran límites de credenciales.
6. Se conecta proveedor de acceso.
7. Se configura Edge cuando aplique.
8. Se generan cuotas/multas/cargos.
9. El residente paga en Tricor Resident.
10. El proveedor de pago confirma.
11. Tricor recalcula estado de propiedad.
12. Tricor genera desired access state.
13. Edge ejecuta comandos en proveedor.
14. Tricor registra estado observado.
15. Guard y Resident reflejan el estado real/auditable.
```

---

## 12. Pagos y finanzas

### 12.1 Principio financiero

Tricor Hábitat no debe ser intermediario financiero del mantenimiento.

El dinero de cuotas, multas y cargos del condominio debe caer en la cuenta configurada por el condominio.

```text
Residente → Proveedor de pago del condominio → Cuenta del condominio
```

Tricor Platform cobra su SaaS por separado:

```text
Condominio → Tricor Platform → Licencia SaaS
```

### 12.2 Proveedor de pagos v1

El primer proveedor de pagos a investigar e integrar será Mercado Pago.

Stripe queda como investigación posterior o comparativa.

### 12.3 Flujo principal de pago

```text
Residente paga dentro de Tricor Resident
→ Mercado Pago confirma pago
→ Tricor valida evento/API
→ deuda queda pagada
→ Policy Engine recalcula acceso
→ se restaura acceso automáticamente si aplica
→ Finanzas audita
```

La administración no debe aprobar manualmente un pago confirmado por proveedor para restaurar acceso.

### 12.4 Transferencia manual fallback

Debe existir como escenario secundario y auditado.

```text
Residente transfiere directo a cuenta del condominio
→ sube comprobante
→ Finanzas/Admin revisa
→ aprueba/rechaza
→ Tricor restaura mediante flujo manual auditado si corresponde
```

Este flujo existe por flexibilidad operativa. No debe ser el flujo principal.

### 12.5 Comprobantes

El residente podrá subir comprobantes desde MVP.

Regla:

```text
El comprobante no restaura permisos por sí solo.
```

Los comprobantes se almacenarán como archivo en storage compatible con S3 y como metadata en base de datos.

Metadata mínima:

- Propiedad.
- Monto declarado.
- Fecha.
- Tipo de pago.
- Estado.
- Archivo asociado.
- Usuario que revisó.
- Resultado.
- Auditoría.

### 12.6 Finanzas dentro de Tricor Condo

El módulo de finanzas vivirá dentro de Tricor Condo.

Responsabilidades:

- Ver pagos.
- Ver deudas.
- Ver multas.
- Revisar comprobantes.
- Aprobar o rechazar transferencias manuales.
- Auditar pagos por pasarela.
- Ver restauraciones automáticas.
- Generar cortes/reportes básicos.

Finanzas no debe editar permisos directamente. Finanzas aprueba pago manual; el sistema recalcula acceso.

---

## 13. Morosidad

La morosidad se calcula por propiedad.

### 13.1 Defaults aprobados

Propiedad al corriente:

```text
Tiene todos los permisos permitidos por su perfil de propiedad.
```

Propiedad morosa default:

```text
- Peatonal permitido.
- Acceso vehicular bloqueado.
- QR invitados bloqueado.
- Zonas no esenciales bloqueadas.
- Amenidades futuras bloqueadas.
```

La configuración debe permitir modificar estos defaults por condominio, pero dentro de reglas controladas.

### 13.2 Configuración de morosidad

Debe existir un panel de configuración con presets y modo avanzado.

Presets iniciales:

- Suave.
- Estándar.
- Estricto.
- Personalizado.

Configuraciones base:

- Días de gracia.
- Hora de ejecución de suspensión.
- Bloqueo de acceso vehicular.
- Bloqueo de QR invitados.
- Bloqueo de QR residente.
- Bloqueo de acceso peatonal.
- Bloqueo de zonas no esenciales.
- Restauración con pago parcial.
- Excepción temporal auditada.
- Restauración automática por pago confirmado.
- Restauración manual auditada por transferencia manual.

### 13.3 Pagos parciales

Default:

```text
Los pagos parciales no restauran acceso.
```

Puede ser configurable por condominio.

---

## 14. Modelo de accesos

Tricor no debe suspender “la propiedad” como objeto físico. Debe suspender capacidades de acceso.

Conceptos canónicos:

- AccessProfile.
- AccessPermission.
- AccessCapability.
- AccessZone.
- Door.
- Credential.
- DesiredAccessState.
- ObservedAccessState.
- ProviderMapping.

### 14.1 Access zones iniciales

- Peatonal.
- Vehicular.
- Servicio/técnica futura.
- Amenidades futuras.

En v1 no se manejará un módulo de vehículos como entidad. Sí se manejará acceso vehicular como zona/capacidad.

### 14.2 Perfiles base

- ACTIVE_ACCESS_PROFILE.
- MOROSO_DEFAULT_PROFILE.
- FORMER_RESIDENT_PROFILE.
- TEMPORARY_EXCEPTION_PROFILE.

### 14.3 Regla canónica

```text
Estado de propiedad
+ Estado de pago
+ Política de morosidad
+ Perfil de acceso
+ Estado de credencial
+ Capacidad del proveedor
= DesiredAccessState
```

Nada debe editarse directamente en el proveedor sin pasar por desired state, comando, Edge, estado observado y auditoría.

---

## 15. QR

El QR es parte importante del MVP.

### 15.1 QR residente

Debe ser preparado desde v1, sujeto a capacidades del proveedor y reglas de seguridad.

Recomendación:

- Dinámico.
- Firmado.
- Expirable.
- Validado contra estado de acceso.
- No confiable si es captura antigua.

### 15.2 QR invitado

Debe estar en v1.

Reglas:

- Generado por residente si la política lo permite.
- Escaneado por guardia.
- Bloqueado por morosidad default.
- Puede ser configurable por condominio.
- Debe registrar cada lectura.
- Debe poder expirar.

---

## 16. Guardia

### 16.1 Roles

- GUARD.
- GUARD_SUPERVISOR.

### 16.2 Guardia normal

Puede:

- Escanear QR.
- Consultar estado de acceso.
- Registrar eventos.
- Registrar rondines/reportes.
- Dar acceso cuando existe QR válido y política lo permite.

No puede:

- Editar residentes.
- Editar propiedades.
- Editar pagos.
- Editar permisos.
- Registrar nuevas credenciales.
- Abrir manualmente sin permiso.

### 16.3 Guardia supervisor

Puede:

- Hacer apertura manual justificada.
- Crear excepción temporal justificada con límites.
- Revisar eventos operativos.
- Reportar falla de pluma/proveedor.

Limitaciones:

- Motivo obligatorio.
- Duración máxima configurable.
- Auditoría completa.
- Notificación a CondoAdmin.
- No edita residentes.
- No edita pagos.
- No registra nuevas credenciales.

### 16.4 Apertura manual

Evento único.

Motivos sugeridos:

- QR válido, pluma no respondió.
- Falla de proveedor.
- Emergencia.
- Autorizado por administración.
- Otro.

El sistema debe capturar automáticamente:

- Guardia.
- Caseta.
- Puerta.
- Hora.
- Motivo.
- Resultado.
- Relación con QR o propiedad si existe.

### 16.5 Excepción temporal auditada

Regla temporal, no evento único.

Casos:

- Mudanza.
- Adulto mayor.
- Error administrativo.
- Pago en aclaración.
- Caso especial autorizado.

Debe tener:

- Duración.
- Motivo.
- Responsable.
- Alcance.
- Auditoría.
- Expiración automática.

---

## 17. Credenciales y ciclo de vida

### 17.1 Tipos iniciales

- Tarjeta/tag.
- QR residente.
- QR invitado.
- Otros futuros según proveedor.

### 17.2 Estados

- ACTIVE.
- SUSPENDED.
- LOST_REPORTED.
- REVOKED.
- REPLACED.
- EXPIRED.
- ARCHIVED.

### 17.3 Límite de credenciales

Cada propiedad tendrá límites configurables:

- Máximo de tarjetas/tags.
- Máximo de QR residentes.
- Máximo de invitados activos.
- Máximo de credenciales temporales.

### 17.4 Credencial perdida

El residente puede bloquear una credencial propia por pérdida.

Flujo:

```text
Residente reporta pérdida
→ credencial pasa a LOST_REPORTED
→ acceso se suspende/revoca
→ administración recibe alerta
→ administración decide reemplazo
```

El residente no puede registrar nueva credencial.

### 17.5 Exresidentes y mudanza

Debe existir un MoveOutWorkflow.

Estados:

- ACTIVE.
- MOVE_OUT_SCHEDULED.
- MOVE_OUT_GRACE_PERIOD.
- FORMER_RESIDENT.
- ARCHIVED.

Debe permitir días de gracia configurables para mudanza.

Al finalizar el periodo:

- Revocar credenciales.
- Bloquear QR residente.
- Bloquear QR invitados.
- Mantener historial.
- No eliminar registros físicos sin auditoría.

### 17.6 Depuración automática

Debe detectar:

- Credenciales activas de exresidentes.
- Credenciales sin uso por X días.
- Residentes sin propiedad activa.
- Credenciales sin propiedad activa.
- Credenciales presentes en proveedor pero no vinculadas a Tricor.

Las credenciales sin uso deben generar alertas no invasivas, no revocarse automáticamente por default.

---

## 18. Configuración y Policy Engine

La flexibilidad del producto vive en configuración controlada.

No todo debe ser checkbox. Se deben usar:

- Presets.
- Checkboxes.
- Selectores.
- Límites numéricos.
- Días de gracia.
- Perfiles.
- Capacidades del proveedor.
- Validaciones de conflicto.

### 18.1 Niveles de configuración

```text
1. Presets simples
2. Configuración avanzada controlada
3. Modo técnico/desarrollador
```

### 18.2 Modo técnico/desarrollador

No debe estar disponible para usuarios normales.

Debe servir para:

- Provider mappings.
- Capacidades del proveedor.
- Diagnóstico de Edge.
- QA Harness.
- Logs técnicos.
- Pruebas de conexión.

### 18.3 Validaciones obligatorias

El sistema debe impedir configuraciones incompatibles.

Ejemplos:

- No activar restauración automática si no hay proveedor de pago activo.
- No bloquear QR invitados si QR invitados está desactivado globalmente.
- No usar perfil moroso si no existe mapeo de perfil moroso en proveedor.
- No permitir apertura manual a guardia normal si la política lo prohíbe.
- No permitir capacidades que el proveedor no soporte.

### 18.4 ProviderCapabilityMatrix

Cada proveedor tendrá una matriz de capacidades.

La configuración visible en Tricor Condo será resultado de:

```text
Capacidades de Tricor
+ Módulos activos del cliente
+ Capacidades del proveedor
+ Mapeos configurados
```

---

## 19. Cloud + Edge

### 19.1 Principio

```text
Cloud = fuente de verdad
Edge = ejecutor local
Proveedor = sistema externo de acceso
```

### 19.2 Cloud

Responsabilidades:

- Autenticación.
- Usuarios.
- Condominios.
- Privadas.
- Propiedades.
- Pagos.
- Morosidad.
- Reglas.
- Desired state.
- Auditoría.
- Reportes.
- Configuración.
- Provider mappings.

### 19.3 Edge

Responsabilidades:

- Ejecutar comandos locales.
- Comunicarse con proveedor local.
- Mantener cola local.
- Reportar heartbeats.
- Reportar estado observado.
- Sincronizar snapshots.
- Operar con modo offline limitado.

Edge no debe:

- Decidir morosidad.
- Calcular pagos.
- Ser fuente de verdad.
- Administrar residentes.
- Administrar permisos permanentes.
- Reemplazar al Cloud.

### 19.4 Edge por condominio/caseta

En v1, Edge será obligatorio para el primer proveedor de acceso implementado.

Debe soportar:

- Edge por condominio.
- Edge por caseta.
- Múltiples Edge por condominio.

### 19.5 Modo offline

Si Edge pierde internet:

- Sigue operando con último snapshot local permitido.
- Cloud marca Edge como offline.
- Nuevos comandos quedan pendientes.
- Se genera incidente operativo.
- Al reconectar, Edge sincroniza y ejecuta cola pendiente.

Si pago confirmado requiere restaurar acceso y Edge está offline:

```text
Pago: confirmado
Acceso: pendiente de restauración
Motivo: Edge offline
```

No se debe marcar acceso como restaurado hasta que el proveedor confirme o Edge reporte ejecución.

### 19.6 UI local de Edge

Aprobada.

Debe ser mínima y técnica.

Funciones:

- Estado de conexión.
- Proveedor conectado/no conectado.
- Última sincronización.
- Cola pendiente.
- Logs recientes.
- Prueba de conexión local.
- Diagnóstico técnico.

No debe permitir administrar residentes, pagos ni permisos permanentes.

---

## 20. Estrategia de proveedores de acceso

La arquitectura debe ser multivendor desde el inicio, pero la implementación debe ser controlada.

### 20.1 Primer proveedor

Primer proveedor implementado en v1:

```text
ZKTeco/CVSecurity mediante Edge local
```

### 20.2 Segundo proveedor / investigación

Hikvision / Hik-Connect / ISAPI queda como research track prioritario, no como bloqueo del MVP.

No debe implementarse antes de cerrar:

- Contratos canónicos.
- ProviderCapabilityMatrix.
- Provider adapter interface.
- QA Harness de contratos.
- Mapeo de perfiles, puertas y zonas.

### 20.3 Regla multivendor

Tricor Condo A puede usar un proveedor.  
Tricor Condo B puede usar otro proveedor.  
El dominio de Tricor no debe cambiar.

Solo cambia el provider adapter.

---

## 21. Stack técnico aprobado v1

Stack base:

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

Este stack queda aprobado como base inicial. Los detalles finales de implementación vivirán en documentos técnicos posteriores.

---

## 22. QA Harness

El QA Harness es componente obligatorio del proyecto desde el inicio.

No debe ser un extra posterior.

### 22.1 Objetivo

Evitar desarrollo por parches. Cada cambio debe poder probarse con flujos reproducibles, reportes y validaciones objetivas.

### 22.2 Primer flujo oficial

```text
property-debt-access-lifecycle
```

Debe probar:

1. Crear condominio.
2. Crear privada.
3. Crear propiedad.
4. Crear propietario/responsable.
5. Asignar credenciales.
6. Configurar proveedor mock.
7. Configurar Edge mock.
8. Crear cuota.
9. Marcar deuda vencida.
10. Aplicar política de morosidad.
11. Generar desired access state.
12. Crear comando para Edge.
13. Ejecutar provider mock.
14. Confirmar observed state.
15. Validar vista Guard.
16. Validar vista Resident.
17. Confirmar pago.
18. Restaurar acceso.
19. Generar reporte.

### 22.3 Suites obligatorias

- qa:health.
- qa:config:defaults.
- qa:config:valid.
- qa:config:invalid.
- qa:config:dependencies.
- qa:config:provider-capabilities.
- qa:config:morosity-policy.
- qa:config:payment-policy.
- qa:config:guard-policy.
- qa:config:credential-lifecycle.
- qa:provider-contract.
- qa:edge-sync.
- qa:payments.
- qa:manual-payment-fallback.

### 22.4 Reportes

Debe generar:

```text
artifacts/qa/latest-report.md
artifacts/qa/latest-report.json
```

Estos reportes serán material de handoff para agentes de código.

---

## 23. No-goals v1

Queda fuera de versión 1:

- Facturación fiscal propia.
- CFDI/timbrado propio.
- Contabilidad fiscal completa.
- WhatsApp.
- Chat entre residentes.
- Red social.
- Amenidades como módulo funcional completo.
- Reservas de alberca/gimnasio/eventos.
- Google Calendar.
- Automatizaciones libres por cliente.
- Implementar varios proveedores a la vez sin contratos canónicos cerrados.
- App móvil nativa obligatoria desde el inicio.
- Censo completo de habitantes por propiedad.
- Vehículos como entidad v1.

---

## 24. Integraciones futuras o research

Estas ideas no entran al core v1, pero pueden investigarse:

### 24.1 Telegram

Posible futuro:

- Bot de soporte para administradores.
- Alertas internas.
- Reportes diarios.
- Notificaciones operativas.

No debe controlar acceso crítico en v1.

### 24.2 Facturación externa

Posible módulo premium futuro.

Regla:

```text
Tricor no timbra.
Tricor prepara datos y se integra con proveedor externo.
```

### 24.3 Exportaciones contables

Posible futuro cercano:

- Exportar pagos.
- Exportar multas.
- Exportar deudas.
- Exportar cortes.

### 24.4 Helpdesk / tickets limitados

Debe mantenerse acotado.

V1 puede tener incidentes y notas operativas básicas, pero no debe convertirse en un sistema completo de tickets.

---

## 25. Modelo de negocio inicial

Tricor Platform cobra el SaaS por separado del dinero operativo del condominio.

Modelo tentativo:

- Cobro por condominio.
- Ajuste por número de propiedades/residentes.
- Ajuste por puertas/casetas/Edge.
- Ajuste por proveedor adicional futuro.
- Módulos premium futuros para integraciones.

El dinero de mantenimiento no debe mezclarse con los ingresos SaaS de Tricor Platform.

---

## 26. Seguridad, auditoría y soporte

### 26.1 Auditoría obligatoria

Debe auditarse:

- Cambios de configuración.
- Aprobaciones manuales.
- Restauraciones.
- Excepciones temporales.
- Aperturas manuales.
- Cambios de credenciales.
- Cambios de proveedor.
- Estado de Edge.
- Acciones de soporte.

### 26.2 Soporte SaaS

Tricor Platform debe incluir rol de soporte.

Regla:

```text
Soporte puede entrar para diagnóstico, pero todo acceso debe quedar auditado.
```

Soporte no es una IA. Es un rol operativo del SaaS.

---

## 27. Documentación obligatoria del proyecto

Este proyecto debe documentarse como producto comercial e histórico.

Documentos iniciales:

- PROJECT_CHARTER_TRICOR_HABITAT.md
- DOMAIN_MODEL_V0.1.md
- ACCESS_POLICY_CONFIGURATION_V0.1.md
- CLOUD_EDGE_ARCHITECTURE_V0.1.md
- PROVIDER_STRATEGY_V0.1.md
- PAYMENT_PROVIDER_STRATEGY_V0.1.md
- QA_HARNESS_SPEC_V0.1.md
- ROADMAP_V0.1.md
- AI_AGENT_RULES_AND_HANDOFF.md
- USER_MANUAL_PLAN.md

Manuales futuros:

- Manual de Tricor Platform.
- Manual de Tricor Condo.
- Manual de Tricor Guard.
- Manual de Tricor Resident.

---

## 28. Handoff para Claude Code CLI

Este documento está preparado para que posteriormente se entregue a Claude Code CLI junto con los documentos técnicos derivados.

Reglas de handoff:

1. Los documentos en `/docs` serán fuente de verdad.
2. La memoria de chat no será fuente de verdad para implementación.
3. No usar contexto de otros proyectos.
4. No usar repos legacy como base de producción.
5. Cualquier referencia legacy será solo lectura y solo para aprendizaje histórico.
6. Ningún agente debe tocar código sin saber qué prueba debe pasar.
7. Ningún cambio se acepta si rompe QA Harness.
8. Ningún agente debe implementar integraciones no aprobadas en este charter.
9. Ningún agente debe modificar varias superficies sin justificación.
10. Cualquier cambio de arquitectura requiere actualización documental antes de implementación.

---

## 29. MVP técnico, piloto y comercial

### 29.1 MVP técnico

Debe demostrar:

- Backend funcionando.
- Base de datos.
- Modelo Condominio → Privada → Propiedad.
- Pagos mock/proveedor simulado.
- Morosidad.
- Policy Engine.
- Edge mock.
- Provider mock.
- QA Harness.

### 29.2 MVP piloto

Debe demostrar en un condominio real:

- Tricor Condo usable.
- Tricor Guard mínimo.
- Tricor Resident mínimo.
- Integración real con primer proveedor.
- Edge local funcionando.
- Pagos y morosidad operativos.
- Restauración automática por pago confirmado.
- Transferencia manual fallback.
- Auditoría.

### 29.3 MVP comercial

Debe agregar:

- Onboarding de cliente.
- Licencia SaaS.
- Soporte básico.
- Reportes básicos.
- Manuales iniciales.
- Estabilidad operacional.
- Proceso de actualización controlado.

---

## 30. Roadmap macro v0.1

1. Charter y documentos base.
2. Domain Model.
3. Access Policy Configuration.
4. Cloud + Edge Architecture.
5. QA Harness Spec.
6. Monorepo inicial.
7. Backend core.
8. Policy Engine.
9. Modelo de pagos.
10. Tricor Condo base.
11. Tricor Platform base.
12. Edge local.
13. Provider adapter v1.
14. Tricor Guard PWA.
15. Tricor Resident PWA.
16. Mercado Pago research/integración.
17. Piloto técnico.
18. Piloto real.
19. Manuales.
20. Investigación Hikvision.
21. Integraciones futuras.

---

## 31. Decisiones cerradas en v0.1

- Nombre del producto: Tricor Hábitat.
- Superficies: Platform, Condo, Guard, Resident.
- Estructura interna: Condominio → Privada → Propiedad.
- Unidad final: Propiedad.
- Cobro/morosidad: por propiedad.
- Dinero de mantenimiento: entra al condominio.
- Tricor cobra SaaS aparte.
- Primer proveedor de pagos a investigar: Mercado Pago.
- Transferencia manual: fallback auditado.
- Comprobantes: MVP, con storage S3-compatible.
- Edge: obligatorio para primer proveedor de acceso v1.
- Edge UI local: mínima y técnica.
- QA Harness: obligatorio desde el inicio.
- Stack base: NestJS, PostgreSQL, Prisma, Next.js, pnpm/Turborepo, Node/TypeScript Edge, S3-compatible, Flutter futuro.
- App móvil nativa: no v1, pero APIs mobile-ready desde día 1.
- Vehículos: no entidad v1.
- Acceso vehicular: sí como zona/capacidad.
- GuardSupervisor: apertura manual y excepción temporal auditadas.
- Residente: puede bloquear tarjeta perdida, no registrar nueva.
- Configuración: Policy Engine con presets, modo avanzado y modo técnico.

---

## 32. Temas abiertos para documentos posteriores

No bloquean este charter:

- Investigación profunda Mercado Pago.
- Comparación posterior con Stripe.
- Investigación Hikvision.
- Diseño final del pricing.
- Diseño visual UI/UX.
- Manuales completos.
- Estrategia legal/privacidad.
- Retención de comprobantes.
- Política de backups.
- Deployment final.
- App móvil Flutter.

---

## 33. Criterio de aprobación del charter

Este documento queda aprobado como v0.1 cuando se acepte que:

- Representa la dirección correcta del producto.
- No se usará el repo legacy como base de producción.
- No se desarrollará por parches.
- El siguiente paso será generar documentos técnicos derivados.
- Claude Code CLI recibirá un handoff limpio basado en docs, no en memoria de conversación.

---

## 34. Próximo documento recomendado

Siguiente archivo a redactar:

```text
DOMAIN_MODEL_V0.1.md
```

Objetivo:

Definir formalmente entidades, relaciones, estados y reglas de dominio para Condominio, Privada, Propiedad, Propietario, Credenciales, Pagos, Morosidad, Accesos, Edge y Proveedores.

# PAYMENT_PROVIDER_RESEARCH_MERCADO_PAGO_V0.1.md

**Proyecto:** Tricor Hábitat  
**Versión del documento:** v0.1  
**Estado:** Draft formal actualizado con documentación oficial Mercado Pago  
**Fecha:** 2026-06-10  
**Preparado para:** Handoff posterior a Claude Code CLI  
**Relación con documentos previos:** `PROJECT_CHARTER_TRICOR_HABITAT.md`, `DOMAIN_MODEL_V0.1.md`, `ACCESS_POLICY_CONFIGURATION_V0.1.md`, `CLOUD_EDGE_ARCHITECTURE_V0.1.md`, `QA_HARNESS_SPEC_V0.1.md`, `ROADMAP_V0.1.md`, `AI_AGENT_RULES_AND_HANDOFF.md`, `REPO_STRUCTURE_V0.1.md`

---

## 1. Propósito del documento

Este documento define la estrategia técnica para usar **Mercado Pago** como primer proveedor de pagos de Tricor Hábitat.

La finalidad es conectar pagos reales con el dominio de Tricor sin romper las decisiones ya asentadas:

```text
Tricor no custodia dinero de mantenimiento.
Cada condominio conecta su propia cuenta de cobro.
Tricor procesa órdenes, estados, auditoría y restauración de acceso.
Tricor Platform cobra el SaaS por separado.
```

Este documento debe ser usado por Claude Code CLI como guía de implementación, pero **no autoriza implementación directa sin QA Harness, ambiente sandbox y revisión humana**.

---

## 2. Fuentes oficiales revisadas

Fuentes oficiales Mercado Pago revisadas para esta actualización:

1. Mercado Pago API Reference  
   https://www.mercadopago.com.mx/developers/es/reference

2. Checkout API vía Orders  
   https://www.mercadopago.com.mx/developers/es/docs/checkout-api-orders/overview

3. API Reference — Create Order  
   https://www.mercadopago.com.mx/developers/es/reference/online-payments/checkout-api/create-order/post

4. API Reference — Get Order  
   https://www.mercadopago.com.mx/developers/es/reference/online-payments/checkout-api/get-order/get

5. Checkout API — Webhooks / Configurar notificaciones  
   https://www.mercadopago.com.mx/developers/es/docs/checkout-api-orders/notifications

6. Checkout API — Status de transacción  
   https://www.mercadopago.com.mx/developers/es/docs/checkout-api-orders/payment-management/status/transaction-status

7. Checkout API — Status de order  
   https://www.mercadopago.com.mx/developers/es/docs/checkout-api-orders/payment-management/status/order-status

8. Checkout API — Transferencias SPEI  
   https://www.mercadopago.com.mx/developers/es/docs/checkout-api-orders/payment-integration/spei-transfers

9. SDKs — Server-side  
   https://www.mercadopago.com.mx/developers/es/docs/sdks-library/server-side

10. SDKs — Client-side MercadoPago.js  
    https://www.mercadopago.com.mx/developers/es/docs/sdks-library/client-side/mp-js-v2

11. SDKs — React  
    https://www.mercadopago.com.mx/developers/es/docs/sdks-library/client-side/sdk-js-react-installation

12. Mercado Pago CLI — Overview  
    https://www.mercadopago.com.mx/developers/es/docs/mp-cli/overview

13. Mercado Pago CLI — Install  
    https://www.mercadopago.com.mx/developers/es/docs/mp-cli/install-mp-cli

14. Mercado Pago CLI — Commands  
    https://www.mercadopago.com.mx/developers/es/docs/mp-cli/commands

15. Mercado Pago MCP Server — Overview  
    https://www.mercadopago.com.mx/developers/es/docs/mcp-server/overview

16. Mercado Pago MCP Server — Connection  
    https://www.mercadopago.com.mx/developers/es/docs/mcp-server/connection

17. Mercado Pago MCP Server — Tools  
    https://www.mercadopago.com.mx/developers/es/docs/mcp-server/tools

18. Split de Pagos 1:1 — Requisitos previos  
    https://www.mercadopago.com.mx/developers/es/docs/split-payments/split-1-1/prerequisites

19. Split de Pagos 1:1 — Crear configuración  
    https://www.mercadopago.com.mx/developers/es/docs/split-payments/split-1-1/integration-configuration/create-configuration

20. Split de Pagos 1:1 — Integrar checkout en Marketplace  
    https://www.mercadopago.com.mx/developers/es/docs/split-payments/split-1-1/integration-configuration/integrate-marketplace

21. Reporte de Liberaciones  
    https://www.mercadopago.com.mx/developers/es/docs/checkout-api-payments/additional-content/reports/released-money

22. Cuentas de prueba  
    https://www.mercadopago.com.mx/developers/es/docs/checkout-api-orders/resources/test-accounts

23. Seguridad  
    https://www.mercadopago.com.mx/developers/es/docs/checkout-api-orders/security/landing-hub

Notas obligatorias:

- La documentación oficial puede cambiar. Antes de producción debe ejecutarse una revisión final.
- Las tarifas, comisiones, KYC, elegibilidad de marketplace, disponibilidad de Split 1:1 y límites comerciales deben validarse directamente con Mercado Pago.
- Este documento no sustituye revisión legal, fiscal, contable ni contractual.

---

## 3. Decisión técnica actual

### 3.1 Proveedor de pagos v1

```text
Proveedor prioritario: Mercado Pago
Proveedor secundario futuro: Stripe
Transferencia manual: fallback auditado
```

### 3.2 Producto Mercado Pago recomendado

La documentación muestra varias rutas posibles. Para Tricor Hábitat se define una estrategia por etapas:

```text
Etapa 1 — MVP piloto:
- Checkout Pro o Checkout API vía Orders, según complejidad final de integración.
- Webhooks obligatorios.
- Consulta server-side obligatoria antes de confirmar pago.
- Transferencia manual fallback.

Etapa 2 — MVP comercial:
- Checkout API vía Orders como ruta principal si las pruebas de seguridad, UX y QA son satisfactorias.
- OAuth / cuenta conectada por condominio como modelo preferente.
- Reportes para conciliación periódica.

Etapa 3 — Optimización futura:
- Evaluar Split de Pagos 1:1 / marketplace si se requiere comisión por transacción o administración formal de vendedores.
- Evaluar suscripciones solo para cobro SaaS de Tricor Platform, no para mantenimiento del condominio.
```

### 3.3 Regla principal

```text
El proveedor de pago confirma dinero.
Tricor confirma deuda.
Tricor recalcula acceso.
Edge/proveedor de acceso aplica permisos.
```

Mercado Pago nunca decide morosidad, reglas de acceso, excepciones temporales ni credenciales.

---

## 4. Arquitectura objetivo

### 4.1 Flujo principal

```text
Tricor Resident
→ solicita pagar deuda/cargo
→ Tricor API crea PaymentOrder interna
→ Tricor API crea order/preference en Mercado Pago
→ Mercado Pago procesa pago
→ Mercado Pago envía webhook
→ Tricor valida firma del webhook
→ Tricor consulta order/payment por API
→ Tricor normaliza estado
→ si pago acreditado, marca deuda pagada
→ Policy Engine recalcula acceso
→ RestoreAccessWorkflow genera DesiredAccessState
→ Edge ejecuta AccessCommand
→ auditoría
```

### 4.2 Flujo de transferencia manual fallback

```text
Residente transfiere directo fuera de Tricor
→ sube comprobante
→ FINANCE_OPERATOR o CONDO_ADMIN revisa
→ aprueba/rechaza manualmente
→ si aprueba, Tricor marca pago manual aprobado
→ Policy Engine recalcula acceso
→ RestoreAccessWorkflow
→ auditoría
```

Este flujo existe por realidad operativa, pero no es el flujo recomendado.

### 4.3 Dinero del mantenimiento

```text
Dinero de mantenimiento:
Residente → Mercado Pago/cuenta conectada del condominio → condominio

Dinero SaaS:
Condominio → Tricor Platform → Tricor
```

Regla dura:

```text
Tricor no debe mezclar ingresos SaaS con cuotas, multas o cargos del condominio.
```

---

## 5. Modelo de cuenta conectada por condominio

### 5.1 Necesidad

Cada condominio debe recibir su propio dinero.

Tricor necesita operar órdenes, estados y webhooks sin pedir que el dinero entre a una cuenta central de Tricor.

### 5.2 Opciones técnicas

#### Opción A — Credenciales directas por condominio

```text
Cada condominio configura su Access Token / Public Key en Tricor Condo.
Tricor usa esas credenciales para crear órdenes.
El dinero cae a la cuenta del condominio.
```

Ventajas:

- Más simple para laboratorio.
- Puede servir para primer piloto controlado.

Riesgos:

- Manejo sensible de credenciales.
- Peor experiencia de onboarding.
- Más soporte operativo.
- No es ideal para escalar.

#### Opción B — OAuth / Split de Pagos 1:1 / Marketplace

La documentación de Split 1:1 indica que el marketplace solicita permisos al vendedor mediante OAuth y luego usa el `access_token` obtenido para operar ventas en nombre del vendedor.

```text
Condominio autoriza a Tricor vía OAuth.
Tricor guarda tokens cifrados.
Tricor crea órdenes usando el token del condominio.
El dinero cae al condominio.
```

Ventajas:

- Mejor ruta SaaS.
- Onboarding más formal.
- Evita pedir tokens manualmente.
- Permite crecer a marketplace si se aprueba comercialmente.

Riesgos / pendientes:

- Mercado Pago exige requisitos para Split 1:1, incluyendo cuenta de vendedor y KYC.
- El modelo 1:N está limitado a cartera asesorada.
- Marketplace fee / application fee puede requerir contacto comercial si se quiere controlar fecha de liberación o condiciones.
- Debe validarse si Tricor necesita Split formal o solo OAuth para operar por vendedor.

### 5.3 Decisión provisional

```text
Laboratorio:
- permitir credenciales sandbox directas.

MVP piloto:
- evaluar credenciales directas solo si OAuth todavía no está listo.

MVP comercial:
- preferir OAuth/cuenta conectada por condominio.
```

---

## 6. Componentes de dominio

### 6.1 PaymentProviderInstallation

Representa la instalación de Mercado Pago para un condominio.

```ts
interface PaymentProviderInstallation {
  id: string;
  condoId: string;
  provider: 'MERCADO_PAGO';
  environment: 'sandbox' | 'production';
  mode: 'DIRECT_CREDENTIALS' | 'OAUTH_CONNECTED_ACCOUNT';
  status:
    | 'DRAFT'
    | 'PENDING_AUTHORIZATION'
    | 'ACTIVE'
    | 'DISABLED'
    | 'ERROR'
    | 'REVOKED';
  publicKeyRef?: string;
  accessTokenSecretRef?: string;
  refreshTokenSecretRef?: string;
  mercadoPagoUserId?: string;
  siteId?: 'MLM';
  webhookSecretRef?: string;
  createdAt: string;
  updatedAt: string;
}
```

Reglas:

- Tokens no se guardan en texto plano.
- Access Token nunca se expone al cliente.
- Public Key sí puede usarse client-side cuando aplique.
- Debe existir auditoría de cambios.

### 6.2 PaymentOrder

Orden interna de Tricor.

```ts
interface PaymentOrder {
  id: string;
  condoId: string;
  propertyId: string;
  ownerId: string;
  currency: 'MXN';
  totalAmount: string;
  status:
    | 'CREATED'
    | 'PENDING_PROVIDER'
    | 'CONFIRMED'
    | 'FAILED'
    | 'EXPIRED'
    | 'CANCELLED'
    | 'REFUNDED'
    | 'CHARGED_BACK'
    | 'DISPUTED';
  provider: 'MERCADO_PAGO';
  providerOrderId?: string;
  providerPaymentId?: string;
  externalReference: string;
  idempotencyKey: string;
  createdAt: string;
  updatedAt: string;
}
```

Regla:

```text
externalReference debe apuntar a un identificador interno estable de Tricor.
```

Ejemplo:

```text
externalReference = tricor:condo:{condoId}:property:{propertyId}:order:{paymentOrderId}
```

### 6.3 PaymentAttempt

Intento de pago por proveedor.

```ts
interface PaymentAttempt {
  id: string;
  paymentOrderId: string;
  provider: 'MERCADO_PAGO';
  method:
    | 'CARD'
    | 'ACCOUNT_MONEY'
    | 'SPEI'
    | 'OXXO_FUTURE'
    | 'OTHER';
  providerOrderId?: string;
  providerTransactionId?: string;
  providerStatus?: string;
  providerStatusDetail?: string;
  tricorStatus:
    | 'CREATED'
    | 'WAITING_PAYMENT'
    | 'PROCESSING'
    | 'ACREDITED'
    | 'FAILED'
    | 'EXPIRED'
    | 'CANCELLED'
    | 'REFUNDED'
    | 'CHARGED_BACK';
  ticketUrl?: string;
  bankReference?: string;
  rawProviderSnapshotRef?: string;
  createdAt: string;
  updatedAt: string;
}
```

### 6.4 PaymentProviderEvent

Evento recibido por webhook.

```ts
interface PaymentProviderEvent {
  id: string;
  provider: 'MERCADO_PAGO';
  eventType: string;
  providerResourceId: string;
  xRequestId?: string;
  xSignature?: string;
  signatureValid: boolean;
  receivedAt: string;
  processedAt?: string;
  status:
    | 'RECEIVED'
    | 'IGNORED'
    | 'PROCESSED'
    | 'FAILED'
    | 'RETRY_REQUIRED';
  rawPayloadRef: string;
}
```

Reglas:

- Webhook duplicado no debe duplicar pagos.
- Todo evento debe ser idempotente.
- El payload crudo se guarda para auditoría con protección de datos.

### 6.5 PaymentReconciliationRecord

Conciliación periódica.

```ts
interface PaymentReconciliationRecord {
  id: string;
  condoId: string;
  provider: 'MERCADO_PAGO';
  periodFrom: string;
  periodTo: string;
  source: 'API' | 'REPORT' | 'MANUAL';
  status: 'PENDING' | 'MATCHED' | 'MISMATCH' | 'REVIEW_REQUIRED';
  totalProviderAmount?: string;
  totalTricorAmount?: string;
  reportFileRef?: string;
  createdAt: string;
}
```

---

## 7. API Mercado Pago: piezas que sí usaremos

### 7.1 Base URL y autenticación

Mercado Pago documenta la base URL:

```text
https://api.mercadopago.com
```

Autenticación:

```http
Authorization: Bearer <ACCESS_TOKEN>
```

Reglas para Tricor:

```text
Access Token solo server-side.
Public Key solo client-side.
Nunca loggear tokens.
Nunca guardar tokens en .env compartidos de repo.
Nunca exponer token en Tricor Resident, Guard o Web/PWA.
```

### 7.2 OAuth

Endpoint documentado:

```http
POST /oauth/token
```

Usos en Tricor:

```text
- Intercambiar authorization code por access_token/refresh_token.
- Renovar tokens.
- Administrar conexión de cuenta por condominio.
```

Estados internos:

```text
PENDING_AUTHORIZATION
ACTIVE
TOKEN_EXPIRED
TOKEN_REFRESH_FAILED
REVOKED
```

QA obligatorio:

```text
- token válido
- token expirado
- refresh exitoso
- refresh fallido
- condominio desconectado
```

### 7.3 Checkout API vía Orders

Endpoint principal documentado:

```http
POST /v1/orders
GET /v1/orders/{order_id}
```

Create Order requiere header de idempotencia:

```http
X-Idempotency-Key: <unique-value>
```

Tricor debe usar:

```text
X-Idempotency-Key = PaymentAttempt.id o UUID estable del intento
```

No usar:

```text
un UUID nuevo en cada retry de la misma operación
```

Campos útiles:

```text
type = online
external_reference = referencia interna Tricor
total_amount = monto
processing_mode = automatic salvo caso especial
transactions.payments[] = medios/montos
```

Para SPEI:

```text
payment_method.id = clabe
payment_method.type = bank_transfer
```

### 7.4 Checkout Pro / Preferences como fallback

Endpoint relevante de referencia:

```http
POST /checkout/preferences
```

Uso recomendado:

```text
- fallback para MVP si Checkout API vía Orders retrasa el piloto.
- flujo redirigido o hosted checkout.
- menor exposición a complejidad de card forms.
```

Campos útiles documentados:

```text
notification_url
external_reference
init_point
marketplace_fee
expiration_date_from
expiration_date_to
```

Decisión:

```text
Checkout Pro no es la visión final de UX, pero puede ser una ruta segura de piloto.
```

### 7.5 SPEI

Mercado Pago documenta Transferencias SPEI usando:

```text
payment_method.id = clabe
payment_method.type = bank_transfer
```

El proveedor devuelve:

```text
ticket_url
reference
status = action_required
status_detail = waiting_payment / waiting_transfer según contexto
```

Reglas Tricor:

```text
- Mostrar ticket_url o botón de pago al residente.
- No restaurar acceso con status action_required.
- Restaurar solo cuando Mercado Pago confirme processed/accredited.
- Usar expiration_time, recomendado P3D para SPEI salvo decisión distinta.
- Si expira, marcar PaymentOrder como EXPIRED.
```

### 7.6 Reembolsos, cancelaciones y contracargos

Deben contemplarse como eventos de auditoría e incidentes, no como flujo principal v1.

Regla:

```text
Un reembolso o contracargo no debe revocar acceso automáticamente sin política explícita.
Debe crear incidente financiero-operativo para revisión.
```

Estados internos:

```text
REFUNDED
PARTIALLY_REFUNDED
CHARGED_BACK
DISPUTED
REVIEW_REQUIRED
```

---

## 8. Webhooks

### 8.1 Uso obligatorio

Webhooks son obligatorios para automatizar restauración por pago confirmado.

Tricor debe exponer:

```http
POST /api/payment-providers/mercado-pago/webhook
```

El endpoint debe:

```text
1. Responder rápido.
2. Guardar evento crudo.
3. Validar firma.
4. Encolar procesamiento interno.
5. Consultar recurso en Mercado Pago.
6. Normalizar estado.
7. Ejecutar flujo de dominio si corresponde.
```

### 8.2 Firma x-signature

Mercado Pago envía `x-signature` con formato similar a:

```text
ts=<timestamp>,v1=<signature>
```

La validación usa:

```text
id:[data.id_url];request-id:[x-request-id_header];ts:[ts_header];
```

Reglas Tricor:

```text
- data.id puede llegar en mayúsculas y debe usarse en minúsculas para validación cuando aplique.
- x-request-id debe capturarse.
- timestamp debe capturarse.
- firma inválida = evento rechazado y auditado.
- no procesar pagos con firma inválida.
```

### 8.3 Consulta posterior obligatoria

No confiar solo en el webhook.

```text
Webhook válido
→ consultar GET /v1/orders/{id} o recurso aplicable
→ comparar external_reference
→ verificar estado
→ normalizar
→ ejecutar dominio
```

### 8.4 Eventos esperados

Mínimo:

```text
order
payment
chargeback
refund / refund-related resource, si aplica en producto final
```

La selección final de tópicos se hará en laboratorio.

### 8.5 Idempotencia de webhooks

Dedupe por:

```text
providerEventId
providerResourceId
x-request-id
eventType
```

Si un webhook se repite:

```text
- no duplicar PaymentOrder
- no duplicar PaymentAttempt
- no ejecutar dos veces RestoreAccessWorkflow
- sí registrar intento/reintento si aporta información útil
```

---

## 9. Mapeo de estados Mercado Pago → Tricor

### 9.1 Orders

| Mercado Pago `status` | `status_detail` | Tricor `PaymentOrder.status` | Acción Tricor |
|---|---|---|---|
| `created` | `created` | `CREATED` | Esperar procesamiento |
| `processing` | `in_process` | `PENDING_PROVIDER` | No restaurar |
| `action_required` | `waiting_payment` | `PENDING_PROVIDER` | No restaurar; mostrar instrucciones |
| `action_required` | `waiting_transfer` | `PENDING_PROVIDER` | No restaurar; esperar SPEI |
| `processed` | `accredited` | `CONFIRMED` | Puede restaurar acceso |
| `processed` | `partially_refunded` | `CONFIRMED_WITH_REVIEW` | Crear alerta financiera |
| `expired` | `expired` | `EXPIRED` | No restaurar |
| `failed` | `failed` | `FAILED` | No restaurar |
| `cancelled` | `cancelled` | `CANCELLED` | No restaurar |
| `refunded` | `refunded` | `REFUNDED` | Crear incidente |
| `charged_back` | `in_process` | `CHARGED_BACK` | Crear incidente |
| `charged_back` | `settled` | `CHARGED_BACK` | Crear incidente |
| `charged_back` | `reimbursed` | `CHARGED_BACK_REIMBURSED` | Crear incidente/resolver según política |

### 9.2 Transactions

| Mercado Pago transaction status | Tricor status | Restauración |
|---|---|---|
| `created/created` | `CREATED` | No |
| `processing/in_process` | `PENDING_PROVIDER` | No |
| `processing/pending_review_manual` | `PENDING_REVIEW` | No |
| `action_required/waiting_payment` | `WAITING_PAYMENT` | No |
| `action_required/waiting_capture` | `WAITING_CAPTURE` | No |
| `action_required/waiting_transfer` | `WAITING_TRANSFER` | No |
| `processed/accredited` | `ACREDITED` | Sí |
| `failed/*` | `FAILED` | No |
| `expired/expired` | `EXPIRED` | No |
| `cancelled/cancelled` | `CANCELLED` | No |
| `refunded/refunded` | `REFUNDED` | Incidente |
| `charged_back/*` | `CHARGED_BACK` | Incidente |

Regla:

```text
Solo processed/accredited puede disparar restauración automática.
```

---

## 10. SDKs

### 10.1 Server-side SDK

Mercado Pago documenta SDKs server-side para reducir esfuerzo de integración y simplificar interacción con APIs. Para Tricor, el stack aprobado es TypeScript/NestJS, por lo que el SDK relevante es:

```text
NodeJS SDK
```

Decisión:

```text
Usar SDK NodeJS solo si soporta de forma limpia los endpoints requeridos.
Si el SDK no cubre Orders/OAuth/Split/Webhooks como necesitamos, usar HTTP client propio.
```

Fuente de verdad:

```text
La API Reference oficial manda sobre el SDK.
```

Regla para Claude Code CLI:

```text
No crear wrappers mágicos alrededor del SDK.
Toda operación debe mapearse a contrato Tricor y tener prueba QA.
```

### 10.2 Client-side SDK

Mercado Pago documenta MercadoPago.js v2 y SDK React.

Uso en Tricor:

```text
- solo para UX de pago client-side cuando usemos Checkout API con tarjetas.
- usar Public Key.
- nunca usar Access Token en cliente.
```

En Next.js:

```text
Opción A: MercadoPago.js vía @mercadopago/sdk-js.
Opción B: SDK React si aporta componentes estables al flujo.
```

Decisión provisional:

```text
Para MVP piloto, evitar complejidad client-side si Checkout Pro resuelve el cobro.
Para Checkout API completo, usar SDK client-side oficial para tokenización/seguridad.
```

---

## 11. Mercado Pago CLI

### 11.1 Rol en Tricor

Mercado Pago CLI (`mpcli`) es herramienta de desarrollo, laboratorio, QA manual y diagnóstico.

No es runtime de producción.

```text
No usar mpcli dentro de apps/api para procesar pagos.
No depender de mpcli para restaurar acceso.
No guardar credenciales CLI en repo.
```

### 11.2 Usos permitidos

```text
- crear y consultar pagos de prueba
- consultar órdenes
- probar OAuth
- generar usuarios/tarjetas de prueba
- crear reportes de prueba si el módulo está habilitado
- diagnosticar webhooks en laboratorio
- abrir documentación desde terminal
```

### 11.3 Comandos relevantes

Documentados por Mercado Pago:

```bash
mpcli payments list
mpcli payments get <id>
mpcli payments search --status approved
mpcli payments search --external-reference "ORDER-001"
mpcli payments create --data @payment.json
mpcli refunds create <payment-id>
mpcli refunds list <payment-id>
mpcli orders get <id>
mpcli chargebacks list
mpcli chargebacks get <id>
mpcli reports releases list
mpcli reports releases create --date-from 2025-01-01 --date-to 2025-01-31
mpcli oauth url --client-id <id> --redirect-uri https://miapp.com/callback
mpcli oauth token --client-id <id> --client-secret <secret> --code <code> --redirect-uri https://miapp.com/callback
mpcli oauth refresh --token <refresh-token>
mpcli tester create
mpcli tester balance set --user-id <id> --amount 10000
mpcli tester card create --user-id <id> --scenario approved
mpcli tester card create --user-id <id> --scenario insufficient_funds
mpcli tester card create --user-id <id> --scenario stolen_card
mpcli tester card create --user-id <id> --scenario high_risk
```

### 11.4 Configuración CLI

La documentación permite perfiles y `.mp.toml`.

Regla para Tricor:

```text
Se permite .mp.toml solo sin tokens.
Tokens por keychain, variable de entorno o secreto local.
```

Ejemplo permitido:

```toml
[defaults]
profile = "tricor-sandbox"
site_id = "MLM"

[output]
no_color = true
```

Prohibido:

```text
commitear Access Tokens
commitear client_secret
commitear refresh_token
pegar tokens en prompts de IA
```

---

## 12. Mercado Pago MCP Server

### 12.1 Rol

Mercado Pago MCP Server permite a agentes IA consultar documentación y usar herramientas de Mercado Pago en entornos compatibles.

Para Tricor:

```text
MCP puede ser útil para investigación, búsqueda de documentación, quality checklist y configuración de webhooks en entorno controlado.
MCP no forma parte del runtime.
MCP no reemplaza QA Harness.
MCP no es fuente de verdad sobre el dominio Tricor.
```

### 12.2 Uso permitido

```text
- consultar documentación oficial desde Claude Code CLI
- revisar quality checklist de Mercado Pago
- diagnosticar webhooks en sandbox
- buscar endpoints durante investigación
```

### 12.3 Uso prohibido

```text
- operar producción sin aprobación humana
- usar credenciales reales en prompts
- modificar configuración productiva de webhooks sin ticket/tarea autorizada
- decidir cambios de dominio Tricor
```

### 12.4 Tools relevantes

Documentadas por Mercado Pago:

```text
search-documentation
quality_checklist
quality_evaluation
save_webhook
notifications_history_diagnostics
```

Regla:

```text
Claude Code CLI puede usar MCP solo como apoyo de documentación y diagnóstico.
La implementación debe seguir este documento, QA_HARNESS_SPEC y AI_AGENT_RULES_AND_HANDOFF.
```

---

## 13. Reportes y conciliación

### 13.1 Uso

Los reportes no son fuente de restauración en tiempo real.

Uso correcto:

```text
- conciliación diaria/semanal
- auditoría financiera
- soporte ante discrepancias
- revisión de contracargos/reembolsos
```

### 13.2 Reporte de Liberaciones

Mercado Pago documenta Reporte de Liberaciones como archivo para revisar saldo disponible, transacciones, bloqueos, desbloqueos, disputas y retiros.

Regla Tricor:

```text
Webhook + consulta API = operación en tiempo real.
Reporte = conciliación posterior.
```

### 13.3 Limitación de pruebas

La documentación indica que reportes generados para cuentas de prueba pueden no traer información, aunque el flujo de generación/consulta/listado funcione.

Implicación:

```text
QA de reportes debe validar flujo técnico, no datos financieros reales, hasta tener ambiente productivo controlado.
```

---

## 14. Tricor Condo: configuración de pagos

Panel:

```text
Tricor Condo → Configuración → Pagos
```

Campos:

```text
Proveedor activo: Mercado Pago
Modo: credenciales directas / cuenta conectada OAuth
Estado: activo / pendiente / error / revocado
Métodos habilitados: tarjeta, cuenta Mercado Pago, SPEI, otros futuros
Transferencia manual fallback: sí/no
Subida de comprobantes: sí/no
Política de comisiones
Webhook status
Última prueba de conexión
Última prueba de webhook
```

Controles:

```text
Conectar cuenta Mercado Pago
Probar conexión
Probar webhook
Desactivar proveedor
Rotar credenciales
Ver eventos recientes
Ver pagos pendientes
Ver pagos confirmados
Ver discrepancias
```

---

## 15. Configuración de comisiones

Mercado Pago cobra comisiones según producto, medio, plazo de liberación y condiciones comerciales.

Tricor debe soportar política configurable:

```text
PaymentFeePolicy:
- ABSORB_BY_CONDO
- PASS_TO_RESIDENT
- INCLUDE_IN_DUE_AMOUNT
```

Regla:

```text
La política de comisión afecta el monto mostrado y cobrado, pero no debe ocultar al residente el total final.
```

No cerrar tarifas en código.

```text
Tarifas = configuración / provider metadata / revisión comercial.
```

---

## 16. Seguridad

### 16.1 Credenciales

Reglas obligatorias:

```text
Access Token server-side solamente.
Public Key client-side solamente.
Refresh Token cifrado.
Webhook secret cifrado.
No tokens en logs.
No tokens en archivos Markdown.
No tokens en prompts de IA.
No tokens en fixtures reales.
```

### 16.2 PCI y datos de tarjeta

Mercado Pago documenta PCI DSS como estándar de seguridad para entidades que almacenan, procesan o transmiten datos de tarjeta.

Regla Tricor:

```text
Tricor no debe almacenar PAN, CVV ni datos sensibles de tarjeta.
Usar SDK/Checkout oficial para tokenización si se procesan tarjetas.
Preferir flujos hosted o SDK seguro para reducir alcance PCI.
```

### 16.3 Webhooks

```text
- Validar x-signature.
- Guardar x-request-id.
- Validar timestamp.
- Consultar recurso por API.
- Rechazar firma inválida.
- Procesar idempotentemente.
```

---

## 17. QA Harness requerido

### 17.1 Suites Mercado Pago

```bash
pnpm qa:payments:mercado-pago:config
pnpm qa:payments:mercado-pago:webhook-signature
pnpm qa:payments:mercado-pago:create-order
pnpm qa:payments:mercado-pago:get-order
pnpm qa:payments:mercado-pago:spei
pnpm qa:payments:mercado-pago:state-mapping
pnpm qa:payments:mercado-pago:oauth
pnpm qa:payments:mercado-pago:manual-fallback
pnpm qa:payments:mercado-pago:edge-offline
pnpm qa:payments:mercado-pago:refund-chargeback
pnpm qa:payments:mercado-pago:reporting
```

### 17.2 Pruebas obligatorias

#### Configuración

```text
1. No permite Mercado Pago ACTIVE sin credenciales válidas.
2. No permite restauración automática si proveedor no está activo.
3. No permite client-side con Access Token.
4. No permite webhook sin secret configurado.
```

#### Order lifecycle

```text
1. Crear PaymentOrder Tricor.
2. Crear order Mercado Pago mock/sandbox.
3. Guardar providerOrderId.
4. Recibir webhook.
5. Validar firma.
6. Consultar order.
7. Mapear processed/accredited a CONFIRMED.
8. Ejecutar RestoreAccessWorkflow.
```

#### SPEI

```text
1. Crear order SPEI con clabe/bank_transfer.
2. Recibir ticket_url/reference.
3. Mantener PENDING_PROVIDER.
4. Simular processed/accredited.
5. Restaurar acceso.
6. Simular expired.
7. No restaurar.
```

#### Webhooks inválidos

```text
1. Firma inválida.
2. x-request-id faltante.
3. data.id en mayúsculas.
4. payload duplicado.
5. evento desconocido.
6. recurso inexistente.
```

#### Fallback manual

```text
1. Residente sube comprobante.
2. Finance aprueba.
3. Sistema recalcula acceso.
4. Auditoría registra aprobador.
5. Comprobante rechazado no restaura.
```

#### Edge offline

```text
1. Pago confirmed.
2. Edge offline.
3. PaymentOrder CONFIRMED.
4. Access restoration PENDING_EDGE_OFFLINE.
5. Edge reconecta.
6. AccessCommand se ejecuta.
7. observed state confirma restauración.
```

---

## 18. Runtime vs tooling

| Pieza | Runtime producción | Desarrollo/QA | Decisión |
|---|---:|---:|---|
| Mercado Pago REST API | Sí | Sí | Obligatoria |
| Webhooks | Sí | Sí | Obligatorios |
| Node SDK server-side | Posible | Sí | Usar si cubre endpoints; si no, HTTP client |
| MercadoPago.js / React SDK | Posible | Sí | Solo para Checkout API client-side |
| Mercado Pago CLI | No | Sí | Laboratorio/QA/diagnóstico |
| MCP Server | No | Opcional | Solo investigación/agente con sandbox |
| Reportes | No tiempo real | Sí | Conciliación/auditoría |
| Transferencia manual | Sí, fallback | Sí | Auditada, no flujo principal |

---

## 19. Reglas para Claude Code CLI

Claude Code CLI debe obedecer:

```text
1. No implementar pagos sin leer este documento completo.
2. No mover reglas de morosidad al provider adapter.
3. No guardar tokens reales.
4. No usar Mercado Pago CLI como dependencia runtime.
5. No usar MCP Server como fuente de verdad del dominio.
6. Toda operación debe pasar por PaymentProviderPort.
7. Toda respuesta Mercado Pago debe normalizarse.
8. Todo webhook debe validarse y consultarse por API antes de confirmar pago.
9. Todo estado processed/accredited debe disparar dominio, no acceso directo al proveedor físico.
10. Toda restauración pasa por Policy Engine → DesiredAccessState → Edge/AccessCommand.
```

---

## 20. PaymentProviderPort

Contrato interno propuesto:

```ts
export interface PaymentProviderPort {
  validateInstallation(input: ValidatePaymentInstallationInput): Promise<ValidatePaymentInstallationResult>;

  createPaymentOrder(input: CreateProviderPaymentOrderInput): Promise<CreateProviderPaymentOrderResult>;

  getPaymentOrder(input: GetProviderPaymentOrderInput): Promise<GetProviderPaymentOrderResult>;

  normalizeWebhook(input: NormalizePaymentWebhookInput): Promise<NormalizedPaymentProviderEvent>;

  verifyWebhookSignature(input: VerifyPaymentWebhookSignatureInput): Promise<VerifyPaymentWebhookSignatureResult>;

  cancelPaymentOrder(input: CancelProviderPaymentOrderInput): Promise<CancelProviderPaymentOrderResult>;

  refundPayment(input: RefundProviderPaymentInput): Promise<RefundProviderPaymentResult>;

  createOAuthAuthorizationUrl(input: CreateOAuthAuthorizationUrlInput): Promise<CreateOAuthAuthorizationUrlResult>;

  exchangeOAuthCode(input: ExchangeOAuthCodeInput): Promise<ExchangeOAuthCodeResult>;

  refreshOAuthToken(input: RefreshOAuthTokenInput): Promise<RefreshOAuthTokenResult>;
}
```

Implementación:

```text
packages/provider-sdk/payment/MercadoPagoPaymentProvider
```

No debe vivir dentro de UI.

---

## 21. Provider adapter Mercado Pago

Ubicación recomendada:

```text
packages/provider-sdk/payment/mercado-pago/
├── mercado-pago-payment-provider.ts
├── mercado-pago-http-client.ts
├── mercado-pago-sdk-client.ts
├── mercado-pago-webhook-verifier.ts
├── mercado-pago-status-normalizer.ts
├── mercado-pago-oauth-client.ts
├── mercado-pago-report-client.ts
├── mercado-pago.types.ts
└── mercado-pago.errors.ts
```

Regla:

```text
El adapter traduce Mercado Pago → Tricor.
Nunca Tricor → dominio Mercado Pago.
```

---

## 22. Endpoint strategy

### 22.1 Core MVP

```text
POST /oauth/token
POST /v1/orders
GET /v1/orders/{order_id}
POST /api/payment-providers/mercado-pago/webhook  (Tricor endpoint)
```

### 22.2 Fallback / piloto

```text
POST /checkout/preferences
GET /v1/payments/{payment_id}
```

### 22.3 Gestión futura

```text
refunds
chargebacks
reports releases
reports settlements
subscriptions for Tricor Platform SaaS, not condo maintenance
```

### 22.4 No usar en v1

```text
Point / POS
shipping
QR presencial
suscripciones de mantenimiento del condominio
almacenamiento de tarjetas salvo futura decisión explícita
```

---

## 23. Open questions / pendientes de laboratorio

1. Confirmar si Checkout API vía Orders cubre todos los medios deseados para México en el flujo de Tricor Resident.
2. Confirmar si se usará Checkout Pro como primera ruta piloto por menor complejidad.
3. Confirmar flujo OAuth/cuenta conectada por condominio.
4. Confirmar requisitos reales para Split de Pagos 1:1 en cuenta mexicana.
5. Confirmar si Tricor cobrará SaaS fuera de Mercado Pago o con otro flujo separado.
6. Confirmar política final de comisiones por condominio.
7. Confirmar tópicos exactos de webhooks para order/payment/refund/chargeback.
8. Confirmar si Mercado Pago CLI puede automatizar parte del laboratorio sin exponer secretos.
9. Confirmar si MCP Server se usará en Claude Code CLI y bajo qué credenciales sandbox.
10. Confirmar comportamiento real de SPEI en sandbox y producción.

---

## 24. Definition of Done para proveedor Mercado Pago

Se considera listo para MVP piloto cuando:

```text
1. PaymentProviderInstallation funciona en sandbox.
2. Se puede crear orden/preferencia desde Tricor.
3. external_reference apunta a PaymentOrder interno.
4. Webhook firma válida se procesa correctamente.
5. Firma inválida se rechaza.
6. Consulta API confirma estado antes de restaurar.
7. processed/accredited restaura acceso.
8. pending/action_required no restaura.
9. SPEI espera confirmación.
10. Transferencia manual fallback funciona y queda auditada.
11. Edge offline deja restauración pendiente, no falsa.
12. QA Harness cubre estados principales.
13. Tokens quedan cifrados y no aparecen en logs.
14. Claude Code CLI entrega Agent Work Report con pruebas.
```

---

## 25. Conclusión

Mercado Pago es viable como primer proveedor de pagos para Tricor Hábitat, con esta estrategia:

```text
MVP:
- Mercado Pago como payment provider principal.
- Webhooks + consulta API obligatoria.
- Pago confirmado restaura acceso.
- Transferencia manual fallback auditada.
- CLI solo para laboratorio.
- SDK solo donde reduzca riesgo y complejidad.
- MCP solo para documentación/diagnóstico, no runtime.
```

La implementación debe mantener el dominio de Tricor independiente:

```text
Mercado Pago confirma transacciones.
Tricor decide estado financiero interno.
Policy Engine decide acceso.
Edge ejecuta permisos físicos.
```

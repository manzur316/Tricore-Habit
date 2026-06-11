# PROVIDER_ZKTECO_CVSECURITY_LAB_TEST_PLAN_V0.1

**Project:** Tricor Hábitat  
**Document type:** Provider laboratory test plan  
**Provider:** ZKTeco / ZKBio CVSecurity  
**Source document:** ZKBio CVSecurity 3rd Party API User Manual v1.2, November 2025, software version ZKBio CVSecurity 6.0.0 or above  
**Local lab target:** ZKBio CVSecurity 6.7.2_R  
**Status:** REVIEW_REQUIRED  
**Implementation status:** BLOCKED until lab execution produces PASS report  
**Last updated:** 2026-06-11  

---

## 1. Purpose

This document defines the controlled laboratory test plan for validating the ZKTeco / ZKBio CVSecurity provider adapter for Tricor Hábitat.

The goal is not to implement production code yet. The goal is to validate, with a real local CVSecurity installation, which API operations actually work for the first Tricor Hábitat use case:

```text
Payments / morosity
→ canonical access state
→ access profile change
→ ZKBio CVSecurity level assignment
→ door permission applied
→ QR / card behavior validated
→ access event observed
→ audit trail captured
```

This lab plan must be completed before the provider adapter is marked production-ready.

---

## 2. Source classification

### 2.1 Source verdict

```text
MATCH
```

The uploaded ZKBio CVSecurity API manual is the correct source for this provider because it documents:

```text
- API authorization by access_token
- general response format
- person endpoints
- card endpoints
- department endpoints
- access control device, door and reader endpoints
- access control level endpoints
- transaction endpoints
- visitor registration and visitor QR endpoints
- entrance control / PSG endpoints
- parking endpoints
- error codes
```

### 2.2 Certification status

```text
Document: REVIEW_REQUIRED
Reason: lab credentials and target data are now available, but endpoints have not been executed yet.
```

### 2.3 Production status

```text
Production implementation: BLOCKED
Reason: remote open, access level swap, QR and observed transaction behavior must be validated in the real CVSecurity 6.7.2_R lab.
```

---

## 3. Lab environment

### 3.1 Target system

```text
Base URL: https://localhost:8098/
CVSecurity version: ZKBio CVSecurity 6.7.2_R
Client ID: TricoreBackend
API credential: provided by user as lab credential
```

### 3.2 Secret handling rule

Do not commit real access tokens or client secrets into the repository.

Use environment variables:

```bash
CVSECURITY_BASE_URL=https://localhost:8098
CVSECURITY_ACCESS_TOKEN=<LAB_ACCESS_TOKEN>
CVSECURITY_CLIENT_ID=TricoreBackend
CVSECURITY_VERSION=ZKBio CVSecurity 6.7.2_R
```

The credential supplied during architecture discussion is treated as a local lab credential only. It must not be embedded in committed `.md`, `.env.example`, source code, reports or screenshots.

---

## 4. Enabled modules in lab

User-provided enabled modules:

```text
- Access Control
- Entrance Control
- Visitor
- Parking
- QR
```

Lab interpretation:

| Module | Lab status | Tricor v1 status | Notes |
|---|---:|---:|---|
| Access Control | Enabled | CORE_V1 | Main test target. |
| Entrance Control | Enabled | LAB_RESEARCH | User currently has readers, no PSG gate/pluma configured. |
| Visitor | Enabled | CONDITIONAL_V1 | Needed for guest QR exploration. |
| Parking | Enabled | OUT_OF_SCOPE_V1 | Do not test unless explicitly requested. |
| QR | Enabled | CONDITIONAL_V1 | Validate resident QR and visitor QR if endpoint works. |

---

## 5. Lab fixture data

### 5.1 Department / hierarchy data

User-provided hierarchy:

```text
CONDOMINIO:
- ID: 1
- Name: REALBILBAO
- Role: parent department

PRIVADA:
- ID: 2
- Name: ARIATZA
- Parent ID: 1

PRIVADA:
- ID: 3
- Name: ARELLANO
- Parent ID: 1
```

Primary test department:

```text
TEST_DEPT_CODE=2
TEST_DEPT_NAME=ARIATZA
```

Canonical mapping:

```text
Tricor Condominio REALBILBAO → CVSecurity department code 1
Tricor Privada ARIATZA → CVSecurity department code 2
Tricor Privada ARELLANO → CVSecurity department code 3
```

### 5.2 Door data

```text
TEST_DOOR_ID=1
TEST_DOOR_NAME=PUERTA1
```

Readers observed:

```text
192.168.1.201-1-Entrada
192.168.1.201-1-Salida
```

Lab interpretation:

```text
PUERTA1 is the only real door/access point for this lab.
Readers represent entry and exit directions connected to the inBio panel.
```

### 5.3 Person / card data

```text
TEST_PERSON_PIN=1
TEST_PERSON_NAME=Tricore
TEST_CARD_NO=7325912
```

Canonical mapping:

```text
Tricor PropertyAccessSubject → CVSecurity pin=1
Tricor AccessCredential → CVSecurity cardNo=7325912
```

### 5.4 Access level data

User-provided profile names:

```text
ACTIVE_ACC_LEVEL_NAME=Pagador
MOROSO_ACC_LEVEL_NAME=Moroso
PEDESTRIAN_ONLY_ACC_LEVEL_NAME=<not configured>
```

Lab interpretation:

```text
Pagador = active access profile with access to PUERTA1.
Moroso = restricted profile with no access.
```

Current lab limitation:

```text
There is only one door and one access control setup.
A real pedestrian-only profile cannot be validated until there are at least two access zones/doors or a second profile with different physical permissions.
```

---

## 6. Canonical Tricor → CVSecurity lab mapping

| Tricor concept | CVSecurity field / endpoint | Lab value |
|---|---|---|
| ProviderInstallation | Base URL + token | `https://localhost:8098/` |
| Condominio | department | `REALBILBAO`, code `1` |
| Privada | department | `ARIATZA`, code `2` |
| PropertyAccessSubject | person pin | `1` |
| Responsible name | person name | `Tricore` |
| AccessCredential | card | `7325912` |
| ActiveAccessProfile | accLevel | `Pagador` |
| MorosoAccessProfile | accLevel | `Moroso` |
| Door | door | `PUERTA1`, id `1` |
| Door readers | reader | Entrada / Salida readers |
| Guest QR | visitor / QR | Visitor module, lab required |
| ObservedAccessEvent | transaction | Access transaction endpoints |

---

## 7. Endpoint source map

This plan uses only endpoints documented by the ZKBio CVSecurity API manual.

### 7.1 General API behavior

The manual defines a general response format:

```json
{
  "code": 0,
  "message": "string",
  "data": {}
}
```

Adapter rule:

```text
The adapter must normalize CVSecurity `code/message/data` into Tricor ProviderOperationResult.
```

### 7.2 Authentication

All requests must include:

```text
?access_token={apitoken}
```

Adapter rule:

```text
The token is never logged in plaintext.
The token is never committed to documentation or source code.
```

---

## 8. Test phases

### Phase 0 — Safety and lab readiness

Purpose:

```text
Confirm that the lab target is reachable and that no destructive test runs without explicit operator confirmation.
```

Required checks:

```text
- CVSecurity UI reachable at https://localhost:8098/
- API license enabled
- API client configured
- access token works
- test person/card/door can be safely modified
- operator understands which tests may open a physical door
```

Do not proceed if:

```text
- the lab target is production
- the token is not scoped for test
- the person/card belongs to a real resident
- the door test could create an unsafe opening
```

---

## 9. Phase 1 — Connectivity and token validation

### T-ZK-001 — Validate API base URL and token with department list

Endpoint candidate:

```http
GET /api/department/getDepartmentList?pageNo=1&pageSize=20&access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Validate base URL, token, general response envelope and basic list pagination.
```

Expected:

```text
- HTTP request succeeds.
- CVSecurity returns `code` and `message`.
- Data includes departments or empty list.
- Department code 1/2/3 can be located if configured.
```

PASS:

```text
Department list responds and includes expected lab departments or confirms reachable empty dataset.
```

FAIL:

```text
- TLS/certificate failure not handled.
- Unauthorized token.
- Non-CVSecurity response.
- Unexpected response envelope.
```

Lab notes:

```text
If localhost certificate is self-signed, the QA client must support explicit lab-only TLS relaxation.
Do not disable certificate validation globally in production code.
```

---

## 10. Phase 2 — Department / hierarchy validation

### T-ZK-010 — Get department by code

Endpoint candidate:

```http
GET /api/department/get?code={code}&access_token={LAB_ACCESS_TOKEN}
```

Test cases:

```text
code=1 → REALBILBAO
code=2 → ARIATZA
code=3 → ARELLANO
```

Purpose:

```text
Validate mapping between Tricor Condominio/Privada and CVSecurity department codes.
```

Expected:

```text
- code 1 exists as parent department.
- code 2 and 3 exist as child/private departments if configured.
- parentCode aligns with lab hierarchy if CVSecurity stores parent relation.
```

PASS:

```text
The lab hierarchy can be read and mapped.
```

FAIL:

```text
Department codes do not exist or do not match user-provided hierarchy.
```

---

## 11. Phase 3 — Person / resident validation

### T-ZK-020 — Get person by PIN

Endpoint candidate:

```http
GET /api/person/get?pin=1&access_token={LAB_ACCESS_TOKEN}
```

Alternative endpoint:

```http
GET /api/person/get/1?access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Validate that test person `pin=1` exists and can be read.
```

Expected:

```text
- pin=1
- name=Tricore or expected lab name
- deptCode=2 or expected test department
- cardNo=7325912 or card present through card endpoint
```

PASS:

```text
Person is found and mapped to Tricor PropertyAccessSubject.
```

FAIL:

```text
Person does not exist or response does not include expected pin.
```

### T-ZK-021 — Add/edit test person basic access data

Endpoint candidate:

```http
POST /api/person/add?access_token={LAB_ACCESS_TOKEN}
```

Request body template:

```json
{
  "pin": "1",
  "deptCode": "2",
  "name": "Tricore",
  "lastName": "Lab",
  "gender": "M",
  "cardNo": "7325912",
  "accLevelIds": "<resolved_Pagador_level_id>",
  "accStartTime": "2026-01-01 00:00:00",
  "accEndTime": "2036-12-31 23:59:59"
}
```

Purpose:

```text
Validate add/edit person and direct assignment of card/access levels through person/add.
```

Safety:

```text
Only execute if pin=1 is a disposable lab person.
Do not execute against a production resident.
```

PASS:

```text
API accepts the request and subsequent get confirms expected values.
```

FAIL:

```text
API rejects accLevelIds, cardNo or deptCode; adapter must fall back to dedicated card/level endpoints.
```

---

## 12. Phase 4 — Card / credential validation

### T-ZK-030 — Get cards by person PIN

Endpoint candidate:

```http
GET /api/card/getCards?pin=1&access_token={LAB_ACCESS_TOKEN}
```

Alternative endpoint:

```http
GET /api/card/getCards/1?access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Validate that card 7325912 is assigned to pin=1.
```

Expected:

```text
- pin=1
- cardNo=7325912
- cardType=0 or 1
```

PASS:

```text
Card is found and mapped to Tricor AccessCredential.
```

FAIL:

```text
Card not found or linked to a different pin.
```

### T-ZK-031 — Set person card

Endpoint candidate:

```http
POST /api/card/set?access_token={LAB_ACCESS_TOKEN}
```

Request body:

```json
{
  "pin": "1",
  "cardNo": "7325912",
  "cardType": "0"
}
```

Purpose:

```text
Validate assigning a card/tag credential to a lab person.
```

Safety:

```text
This may modify credential assignment. Execute only on lab card.
```

PASS:

```text
The card is assigned and visible through card/getCards.
```

FAIL:

```text
Multi-card setting, duplicate card or card format prevents assignment.
```

---

## 13. Phase 5 — Door / reader validation

### T-ZK-040 — List doors

Endpoint candidate:

```http
GET /api/door/list?pageNo=1&pageSize=20&access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Locate PUERTA1 and confirm door ID/name mapping.
```

Expected:

```text
- id=1 or a real provider ID matching PUERTA1
- name=PUERTA1
```

PASS:

```text
Door list contains PUERTA1.
```

FAIL:

```text
Door is not returned or ID differs from user-provided ID.
```

### T-ZK-041 — List readers

Endpoint candidate:

```http
GET /api/reader/list?pageNo=1&pageSize=20&access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Validate Entrada/Salida readers connected to PUERTA1.
```

Expected readers:

```text
192.168.1.201-1-Entrada
192.168.1.201-1-Salida
```

PASS:

```text
Both reader directions are discoverable or can be inferred from device configuration.
```

FAIL:

```text
Readers are not exposed by API; adapter must not depend on reader-level mapping for v1.
```

### T-ZK-042 — Remote open door by ID

Endpoint candidate:

```http
POST /api/door/remoteOpenById?doorId=1&interval=3&access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Validate GuardSupervisor manual opening through provider.
```

Safety level:

```text
DESTRUCTIVE / PHYSICAL ACTION
```

Operator requirements:

```text
- Someone must be physically present or able to observe PUERTA1.
- Door opening must be safe.
- Test interval should be minimal, e.g. 3 seconds.
```

PASS:

```text
API returns success and PUERTA1 opens/unlocks for the expected interval.
```

FAIL:

```text
API returns success but door does not open.
API rejects doorId=1.
Door opens but transaction/audit does not appear.
```

### T-ZK-043 — Remote open door by name

Endpoint candidate:

```http
POST /api/door/remoteOpenByName?doorName=PUERTA1&interval=3&access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Validate whether doorName can be used safely or if provider ID is mandatory.
```

Expected:

```text
This is secondary. Production adapter should prefer stable provider IDs if available.
```

---

## 14. Phase 6 — Access level validation

### T-ZK-050 — List access control levels

Endpoint candidate:

```http
GET /api/accLevel/list?pageNo=1&pageSize=20&access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Resolve provider IDs for Pagador and Moroso profiles.
```

Expected:

```text
- Pagador exists and grants access to PUERTA1.
- Moroso exists and grants no access.
```

PASS:

```text
Both levels are found and can be mapped to canonical profiles.
```

FAIL:

```text
Level names exist but no stable ID is returned.
Level names do not exist.
Moroso does not truly restrict access.
```

### T-ZK-051 — Validate level-door mapping

Endpoint candidates:

```http
POST /api/accLevel/addLevelDoor?access_token={LAB_ACCESS_TOKEN}
```

Request body example:

```json
[
  {
    "doorName": "PUERTA1",
    "levelName": "Pagador"
  }
]
```

Purpose:

```text
Validate whether the API can add/update level-door mapping by names.
```

Safety:

```text
Potentially modifies access level configuration. Execute only if lab level is disposable.
```

Expected:

```text
Pagador grants access to PUERTA1.
Moroso grants no access.
```

LAB_REQUIRED decision:

```text
If Pagador/Moroso already exist, prefer read/verify first.
Do not mutate existing levels unless operator confirms.
```

### T-ZK-052 — Assign active access level to person

Endpoint candidate:

```http
POST /api/accLevel/addLevelPerson?levelIds={PAGADOR_LEVEL_ID}&pin=1&access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Validate restoring access by assigning the active/Pagador profile.
```

Expected:

```text
pin=1 receives Pagador access level.
Subsequent physical/card test should allow PUERTA1 access.
```

PASS:

```text
API returns success and card 7325912 can trigger access at PUERTA1.
```

FAIL:

```text
API success but physical access does not change.
```

### T-ZK-053 — Assign moroso access level to person

Endpoint candidate:

```http
POST /api/accLevel/addLevelPerson?levelIds={MOROSO_LEVEL_ID}&pin=1&access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Validate revocation/restriction by assigning the Moroso profile.
```

Expected:

```text
pin=1 receives Moroso access level.
Card 7325912 must not open PUERTA1.
```

Potential issue:

```text
If addLevelPerson only adds levels without removing old ones, assigning Moroso will not revoke Pagador.
```

This must be tested carefully.

### T-ZK-054 — Determine correct revocation strategy

Candidate strategies:

```text
A. Replace accLevelIds through /api/person/add with Moroso only.
B. Assign Moroso and remove Pagador using delete-level endpoint if available.
C. Use person leave/resignation for full revocation.
D. Disable card/credential.
```

Lab objective:

```text
Determine the safest actual strategy to remove Pagador access.
```

Required result:

```text
The provider adapter must not assume that adding Moroso automatically removes Pagador.
```

PASS:

```text
A tested strategy reliably changes pin=1 from Pagador allowed to Moroso denied.
```

FAIL:

```text
No API-tested strategy can remove access deterministically.
```

---

## 15. Phase 7 — Morosity lifecycle test

### T-ZK-060 — Active/Pagador state allows access

Setup:

```text
pin=1
cardNo=7325912
access profile=Pagador
```

Execution:

```text
1. Assign Pagador profile.
2. Present card/tag 7325912 at PUERTA1 Entrada reader.
3. Query transactions.
```

Expected:

```text
Access granted transaction appears.
Door unlocks if wiring allows.
```

### T-ZK-061 — Moroso state denies access

Setup:

```text
pin=1
cardNo=7325912
access profile=Moroso
```

Execution:

```text
1. Apply tested revocation/restriction strategy.
2. Present card/tag 7325912 at PUERTA1 Entrada reader.
3. Query transactions.
```

Expected:

```text
Access denied or no-open event appears.
Door does not unlock.
```

### T-ZK-062 — Restore from Moroso to Pagador

Execution:

```text
1. Apply Pagador profile.
2. Present card/tag 7325912.
3. Query transactions.
```

Expected:

```text
Access restored.
This simulates payment confirmed → RestoreAccessWorkflow.
```

---

## 16. Phase 8 — QR validation

### T-ZK-070 — Generate resident dynamic QR

Endpoint candidates:

```http
POST /api/person/getQrCode/1?access_token={LAB_ACCESS_TOKEN}
```

Alternative:

```http
POST /api/v2/person/getQrCode?pin=1&access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Validate whether CVSecurity can generate a dynamic QR for the test resident.
```

Expected:

```text
Response data contains base64 or encoded dynamic QR string.
```

PASS:

```text
QR is generated and can be scanned by supported reader/device if available.
```

FAIL:

```text
Endpoint returns success but QR cannot be used by current hardware.
QR module not physically supported.
```

### T-ZK-071 — Resident QR blocked by Moroso profile

Purpose:

```text
Determine whether QR authorization depends on the same access level as card/tag.
```

Execution:

```text
1. Generate resident QR in Pagador state.
2. Apply Moroso state.
3. Try using QR if supported by hardware.
4. Query transactions.
```

Expected:

```text
Moroso should not allow restricted QR access if the QR path uses access permissions.
```

Status:

```text
LAB_REQUIRED
```

---

## 17. Phase 9 — Visitor / guest QR validation

### T-ZK-080 — Add visitor registration

Endpoint candidate:

```http
POST /api/visRegistration/add?access_token={LAB_ACCESS_TOKEN}
```

Request body template:

```json
{
  "persPersonPin": "1",
  "certType": "8",
  "certNum": "TRICOR-GUEST-001",
  "visEmpName": "Guest Test",
  "visitEmpPhone": "",
  "company": "",
  "visitReason": "lab test",
  "visitorCount": "1",
  "startTime": "2026-06-11 09:00:00",
  "endTime": "2026-06-11 18:00:00",
  "cardNo": "",
  "visLevels": ""
}
```

Purpose:

```text
Validate guest QR workflow candidate.
```

Expected:

```text
Visitor registration creates visitor pin/id.
```

### T-ZK-081 — Generate visitor dynamic QR

Endpoint candidates:

```http
POST /api/visRegistration/getQrCode/{VISITOR_PIN}?access_token={LAB_ACCESS_TOKEN}
```

Alternative:

```http
POST /api/visRegistration/getQrCode?pin={VISITOR_PIN}&access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Validate whether the Visitor module can generate guest QR compatible with Tricor Resident guest QR feature.
```

Expected:

```text
Response contains visitor dynamic QR data.
```

### T-ZK-082 — Visitor QR blocked when host is Moroso

Purpose:

```text
Validate if Tricor can enforce “moroso blocks guest QR” through app-side policy even if CVSecurity can still issue QR.
```

Expected Tricor behavior:

```text
If property is Moroso and policy blocks guest QR, Tricor must not call visitor registration/QR generation.
```

Provider test:

```text
Do not rely on CVSecurity to enforce host morosity automatically.
Tricor Policy Engine must enforce this before provider call.
```

---

## 18. Phase 10 — Transaction / observed state validation

### T-ZK-090 — Query access control transactions

Endpoint candidates:

```http
GET /api/transaction/list?pageNo=1&pageSize=20&access_token={LAB_ACCESS_TOKEN}
```

Additional filters to validate if supported:

```text
personPin
startDate
endDate
doorName
eventName
```

Purpose:

```text
Validate observed state after allowed, denied, QR and manual-open events.
```

Expected fields:

```text
pin
name
cardNo
doorName
readerName
eventName
eventPointName
devName
eventTime
capturePhotoBase64, if enabled
```

PASS:

```text
Transaction endpoint can confirm card/QR/manual-open activity.
```

FAIL:

```text
Transactions cannot be queried with enough detail for observed state.
```

### T-ZK-091 — Get door transaction detail

Endpoint candidate:

```http
POST /api/transaction/getDoorTransactionDetail?access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Determine whether detailed door transaction inspection is useful for reconciliation.
```

Status:

```text
CONDITIONAL
```

---

## 19. Phase 11 — Lost credential validation

### T-ZK-100 — Simulate lost card by removing/disabling card

Candidate strategies:

```text
A. Set cardNo empty through person add/edit if manual behavior supports deletion.
B. Reassign person without card.
C. Use card endpoint if deletion behavior exists in lab.
D. Disable by moving person to Moroso/no-access profile while card replacement is pending.
```

Purpose:

```text
Validate Tricor Resident “report lost card” workflow.
```

Expected Tricor behavior:

```text
Resident reports card lost
→ credential status LOST_REPORTED
→ provider operation disables/revokes card
→ Finance/Admin sees replacement task
```

Lab requirement:

```text
Do not delete the only physical card unless replacement/recovery strategy is known.
```

PASS:

```text
Card 7325912 no longer opens PUERTA1 and audit reflects credential blocked.
```

FAIL:

```text
No safe API strategy exists to disable a single card without disrupting the person.
```

---

## 20. Phase 12 — Move-out / exresident validation

### T-ZK-110 — Schedule move-out grace period, then revoke

Purpose:

```text
Validate Tricor MoveOutWorkflow against provider operations.
```

Tricor flow:

```text
ACTIVE
→ MOVE_OUT_SCHEDULED
→ MOVE_OUT_GRACE_PERIOD
→ FORMER_RESIDENT
→ ARCHIVED
```

Provider strategies to test:

```text
A. Move person to Moroso/no-access profile.
B. Clear access levels.
C. Use person leave/resignation endpoint.
D. Delete person only in disposable lab case.
```

Endpoint candidates:

```http
POST /api/person/leave?access_token={LAB_ACCESS_TOKEN}
POST /api/person/reinstated?access_token={LAB_ACCESS_TOKEN}
POST /api/v2/person/deleteByPins?pins=1&access_token={LAB_ACCESS_TOKEN}
```

Safety:

```text
Do not permanently delete lab person until all non-destructive revocation strategies are tested.
```

PASS:

```text
Former resident loses access and can be restored/recreated if needed.
```

FAIL:

```text
Resignation/delete has irreversible side effects or does not revoke card/device state as expected.
```

---

## 21. Phase 13 — Entrance Control / PSG validation

Current lab data:

```text
TEST_PSG_GATE_ID=<empty>
TEST_PSG_GATE_NAME=<empty>
ACTIVE_PSG_LEVEL_ID=<empty>
MOROSO_PSG_LEVEL_ID=<empty>
```

Status:

```text
INCONCLUSIVE / LAB_NOT_CONFIGURED
```

Do not run PSG tests unless a real gate/pluma appears in API lists.

### T-ZK-120 — List PSG gates if module is enabled

Endpoint candidate:

```http
GET /api/psgGate/allGateState?access_token={LAB_ACCESS_TOKEN}
```

Purpose:

```text
Determine whether Entrance Control gates exist even if user says no pluma applies.
```

Expected:

```text
If no gates exist, test records module enabled but no gate configured.
```

Do not execute:

```text
psgGate/remoteOpenById
psgGate/remoteOpenByName
psgLevel/syncPerson
```

unless actual gate IDs and PSG levels are discovered and approved.

---

## 22. Phase 14 — Parking validation

Current v1 status:

```text
Parking is OUT_OF_SCOPE_V1.
```

Reason:

```text
Tricor v1 does not manage vehicle entities.
```

Allowed action:

```text
Capability discovery only.
```

Do not implement parking authorization in v1.

---

## 23. Command sequence for first lab run

Recommended non-destructive sequence:

```text
1. T-ZK-001 department list / connectivity
2. T-ZK-010 department by code
3. T-ZK-020 get person by pin
4. T-ZK-030 get cards by pin
5. T-ZK-040 list doors
6. T-ZK-041 list readers
7. T-ZK-050 list access levels
8. T-ZK-090 query transactions
9. T-ZK-070 generate resident QR
10. T-ZK-080 add visitor registration only if disposable
11. T-ZK-081 generate visitor QR only if visitor registration succeeded
```

Recommended controlled destructive/physical sequence:

```text
1. T-ZK-042 remote open by door ID
2. T-ZK-052 assign Pagador
3. T-ZK-060 test allowed card access
4. T-ZK-053 assign Moroso / tested revocation strategy
5. T-ZK-061 test denied card access
6. T-ZK-062 restore Pagador
7. T-ZK-100 lost card strategy only after backup
8. T-ZK-110 move-out strategy only after recovery path known
```

---

## 24. QA Harness command targets

Future QA Harness commands derived from this lab:

```bash
pnpm qa:provider:zkteco health
pnpm qa:provider:zkteco departments
pnpm qa:provider:zkteco person --pin 1
pnpm qa:provider:zkteco cards --pin 1
pnpm qa:provider:zkteco doors
pnpm qa:provider:zkteco readers
pnpm qa:provider:zkteco levels
pnpm qa:provider:zkteco qr:resident --pin 1
pnpm qa:provider:zkteco visitor:qr --host-pin 1
pnpm qa:provider:zkteco open-door --door-id 1 --interval 3 --confirm-physical
pnpm qa:provider:zkteco flow access-active-moroso-restore --pin 1 --card 7325912
pnpm qa:provider:zkteco transactions --pin 1
pnpm qa:provider:zkteco report
```

These commands are not implemented yet. They are targets for the QA Harness spec after this lab plan is accepted.

---

## 25. Expected lab report format

The lab execution must produce:

```text
artifacts/zkteco-lab/latest-report.md
artifacts/zkteco-lab/latest-report.json
```

Required fields:

```json
{
  "provider": "zkteco-cvsecurity",
  "cvsecurityVersion": "ZKBio CVSecurity 6.7.2_R",
  "baseUrl": "https://localhost:8098/",
  "tokenRedacted": true,
  "startedAt": "ISO_DATE",
  "completedAt": "ISO_DATE",
  "tests": [],
  "capabilityMatrix": {},
  "providerMappings": {},
  "blockedItems": [],
  "approvedForImplementation": false
}
```

---

## 26. Capability matrix to fill after lab

```yaml
provider: zkteco-cvsecurity
version: ZKBio CVSecurity 6.7.2_R
baseUrl: https://localhost:8098/
modules:
  accessControl: true
  entranceControl: true
  visitor: true
  parking: true
  qr: true
capabilities:
  readDepartments: UNKNOWN
  readPerson: UNKNOWN
  upsertPerson: UNKNOWN
  readCards: UNKNOWN
  setCard: UNKNOWN
  listDoors: UNKNOWN
  listReaders: UNKNOWN
  listAccessLevels: UNKNOWN
  assignAccessLevelToPerson: UNKNOWN
  replaceAccessLevels: UNKNOWN
  removeAccessLevelFromPerson: UNKNOWN
  remoteOpenDoorById: UNKNOWN
  remoteOpenDoorByName: UNKNOWN
  generateResidentQr: UNKNOWN
  registerVisitor: UNKNOWN
  generateVisitorQr: UNKNOWN
  readTransactions: UNKNOWN
  entranceControlGateDiscovery: UNKNOWN
  entranceControlRemoteOpen: NOT_CONFIGURED
  parkingIntegration: OUT_OF_SCOPE_V1
```

---

## 27. Provider mapping to fill after lab

```yaml
providerInstallation:
  provider: zkteco-cvsecurity
  baseUrl: https://localhost:8098/
  tokenEnv: CVSECURITY_ACCESS_TOKEN

departments:
  condominio:
    code: "1"
    name: REALBILBAO
  privadas:
    - code: "2"
      name: ARIATZA
      parentCode: "1"
    - code: "3"
      name: ARELLANO
      parentCode: "1"

doors:
  - canonicalDoorId: PUERTA1
    providerDoorId: "1"
    providerDoorName: PUERTA1
    accessZone: ACCESS_CONTROL_MAIN

readers:
  - name: 192.168.1.201-1-Entrada
    direction: ENTRY
    door: PUERTA1
  - name: 192.168.1.201-1-Salida
    direction: EXIT
    door: PUERTA1

accessProfiles:
  active:
    canonical: ACTIVE_ACCESS_PROFILE
    providerLevelName: Pagador
    providerLevelId: LAB_REQUIRED
  moroso:
    canonical: MOROSO_DEFAULT_PROFILE
    providerLevelName: Moroso
    providerLevelId: LAB_REQUIRED
  pedestrianOnly:
    canonical: PEDESTRIAN_ONLY_PROFILE
    providerLevelName: null
    providerLevelId: null
    status: NOT_CONFIGURED
```

---

## 28. PASS / FAIL criteria

### 28.1 Minimum PASS for ZKTeco provider research

The lab passes minimum research if:

```text
- API connectivity works.
- token works.
- person pin=1 can be read.
- card 7325912 can be read or assigned.
- PUERTA1 can be discovered.
- Pagador and Moroso levels can be discovered.
- a tested strategy can restore access.
- a tested strategy can restrict/revoke access.
- transactions can be read after access attempts.
```

### 28.2 PASS for physical provider adapter implementation

Implementation may begin only if:

```text
- remote open by door ID is validated or explicitly deferred.
- Pagador → Moroso → Pagador lifecycle works deterministically.
- no-access state is verified physically or by transaction/event.
- provider operation results are idempotent or can be safely retried.
- errors are normalized.
- token handling is safe.
```

### 28.3 FAIL / BLOCKED

The adapter remains blocked if:

```text
- adding Moroso does not remove Pagador access.
- there is no safe way to remove/restrict access.
- transactions cannot confirm access state.
- remote open is required but not testable.
- QR behavior cannot be validated and is required by MVP.
```

---

## 29. Known lab limitations

```text
1. Only one door is available: PUERTA1.
2. No PSG gate/pluma is configured.
3. Pedestrian-only profile cannot be truly validated with one door.
4. Vehicle access cannot be validated in this lab.
5. Moroso currently means “no access”, not “pedestrian-only”.
6. QR support may depend on physical reader/device support.
7. Parking is enabled but out of Tricor v1 scope.
```

---

## 30. Implementation guardrails for Claude Code CLI

Claude Code CLI must not implement the ZKTeco adapter as production-ready until this lab plan has a PASS report.

Rules:

```text
1. Do not hardcode lab token.
2. Do not commit credentials.
3. Do not assume Pagador/Moroso IDs from names without discovery.
4. Do not assume addLevelPerson replaces existing levels.
5. Do not use PSG endpoints unless gate data exists.
6. Do not implement parking for v1.
7. Do not treat QR generation as sufficient until hardware scan is validated.
8. Do not delete persons/cards in automated tests without explicit destructive-test flag.
9. Do not mark access restored until provider operation succeeds or is explicitly pending.
10. Always produce ProviderOperationResult and audit event.
```

---

## 31. Final status

```text
Document status: REVIEW_REQUIRED
Reason: lab data has been incorporated, but execution results are pending.

Provider implementation status: BLOCKED
Reason: real endpoint execution and physical validation are pending.

Next required action:
Run non-destructive lab tests T-ZK-001 through T-ZK-050.
```

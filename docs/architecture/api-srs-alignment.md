# Pawthy — Architecture / SRS / OpenAPI Alignment (Sprint 2)

> **Architect:** Efe · **Review date:** 20 November 2026 · **Status:** Requirements-to-design cross-check completed; **API contract alignment, implementation and test execution are NOT complete**.  
> **Requirements evidence:** Deniz's 69-page updated Functional Requirements SRS, five-page supplementary *5.2 Software Interfaces* and subsequently supplied **FR-08 View Veterinary Clinical Records** screenshots (screenshots starting on slide 31/222, Must Have). SRS is not committed to the repository in this PR.  
> **Technical contract checked:** `HUARGE/Pawthy:develop`, `api/openapi.yaml` OpenAPI **3.1.0 / version 0.6.3**. That published contract still describes some prior Sprint 1 behavior.  
> **Detailed references:** [Requirement discrepancy ledger](srs-2026-11-20-review.md) · [API owner change proposal](../../api/srs-sync-proposal-2026-11-20.md) · [C4](c4-architecture.md) · [UML](../uml-diagrams/uml-diagrams.md).

## 1. Precedence, methodology and interpretation

1. Read the provided new Functional Requirements SRS and separate **supplementary Software Interfaces 5.2**. The supplement selects **HTML/CSS/JavaScript, Python/Django, MySQL and OpenAPI/YAML**. Browser-to-MySQL direct access is forbidden. Backend validates, authorizes, preserves referential integrity and distinguishes ordinary modifications, **soft deletion**, archival, restoration and deceased status (SI-01–SI-07).
2. Inventory relevant routes and request/response models in version 0.6.3. This is **the published working contract**, not a guarantee of feature implementation.
3. Update architect-owned C4 and UML for SRS target behavior. Where it conflicts with v0.6.3, mark **CONFLICT**; do not silently change `api/openapi.yaml` or teammate documents.
4. Keep Product Owner, API author, QA and implementation responsibilities distinct. A recorded gap is not a failed test; a proposed diagram is not a deployment.
5. **FR-08 content is now supplied separately:** Seven functional requirements and eight acceptance criteria define owner-only clinical read access, newest-first records, all populated veterinarian-managed fields, no empty optional fields, no owner mutations, soft-deleted exclusion, cross-owner denial and informative empty state. The 69-page SRS PDF **still** repeats §3.7 and lacks §3.8; only consolidation into the canonical PDF remains an editorial request.

**Review states:** DESIGN MAPPED = documented in C4/UML; CONTRACT CONFLICT = existing API lacks or contradicts SRS; OPEN = team must decide; TEST NOT RUN = no execution evidence.

## 2. User Story → OpenAPI → C4/UML traceability

| Story | New SRS requirement | Current v0.6.3 API evidence | C4 / UML coverage | Assessment |
|---|---|---|---|---|
| US-01 | Vet-created owner, required full name + phone, generated immutable ID independent of phone | `POST /pet-owners`; `OwnerAccountRequest` currently **requires email** | Owner service, Class A, first-login Sequence | **CONFLICT** |
| US-02 | Login **by Owner ID**, one-time temp password, forced change, vet reset | `POST /auth/login` expects email; change-password exists | Auth service, Sequence 1 | **CONFLICT** |
| US-03 | Server RBAC, owner/pet isolation, private photos | bearer auth and `x-roles` declared; actual access not verified | Auth trust boundary and actor rights | DESIGN MAPPED, TEST NOT RUN |
| US-04 | Vet-controlled life stage and Exact/Estimated DOB, chip status; clinic/private photo split | `Pet` missing accuracy, life stage, chip-status fields; photos exist | Profile service, Class A | **CONFLICT** |
| US-05 | Owner diary, past observation vs creation time, no future entries | `/observations` endpoints | Diary service, Class A | PARTIAL; validation detail OPEN |
| US-06 | Distinct symptom **days**, no default healthy days; period filters | `/observation-statistics` | Diary service and data calculations | PARTIAL; aggregation check OPEN |
| US-07 | Vet clinical CRUD; **soft deletion + restoration** per SI-07; no diary/vaccine cascade | `/medical-records` GET/POST/PATCH; **no DELETE/restore** | Clinical service, Class A | **CONFLICT** |
| **FR-08 / US-08** | **Must Have; Pet Owner** with authenticated ownership; own records newest-first, all populated vet fields, omit unpopulated optional fields, no create/edit/delete, soft-deleted hidden until vet restores, empty-state and cross-owner denial (**FR 1–7, AC-01–08**) | **GET `/pets/{petId}/medical-records` already supports PET_OWNER**, `200` says **Most recent first**, `401/403/404` and paginated `MedicalRecordList`; soft-delete exclusion, optional-field display and empty-state are not explicitly verified | Owner-only Use Case, Class A, **Sequence 6** | **PARTIALLY ALIGNED**, remaining data lifecycle/UI tests, **not a missing user story** |
| US-09 | Vet vaccine creation/update; no vaccination delete | GET/POST/PATCH vaccine operations | Vaccination service, Class A | Broadly compatible; test not run |
| US-10 | Scheduled/Upcoming/Due/Overdue/Completed/Suppressed reminder plus independent **mark read** | Reminder enum lacks Scheduled/Suppressed; no mark-read | Reminder service, Class A, State B | **CONFLICT** |
| US-11 | Vet editable day/week exceptions; Owner authenticated **available-only** weekly view and phone | Public `GET /availability/calendar` returns AVAILABLE/BUSY/CLOSED; `/busy-times` and `/availability/blocks` coexist | Calendar component, available-only Sequence | **CONFLICT** |
| US-12 | Vet manual only, >=10 min, hard overlap rejection, **acknowledged soft out-of-hours override**, Scheduled/Completed/Canceled, in-app owner notices | `/appointments` excludes out-of-hours, enum CONFIRMED/CANCELLED; notices absent | Appointment, Notice, Class B, Sequence 2, State A | **CONFLICT** |
| US-13 | Per-account Light/Dark preference, no clinical data changes | `GET/PATCH /me/theme` | Theme component and use case | DESIGN MAPPED |
| US-14 | Optional P2 four-step onboarding after first-password change; status saved per account; Help replay never resets | No `guided-tour` route/schema in 0.6.3 | Tour component, Class A, Sequence 5, State C | **NEW CONTRACT GAP** |

**Coverage:** All **14 US identifiers have source-backed functional definitions** when the separate FR-08 screenshots are read alongside the updated SRS. The canonical PDF still needs its duplicate §3.7 replaced with §3.8. This is **design traceability only**, not proof that 14 features are implemented, fully API-aligned, tested or formally approved.

## 3. Important functional and access-control boundaries

### 3.1 Authentication and privacy
- Owner ID is not the phone number and must remain stable when the phone is edited; current API's required email cannot be passed off as compliant.
- Two authenticated roles: VETERINARIAN and PET_OWNER. Public visitor is an unauthenticated context, not a third account role.
- Vet creates and updates official identity/clinical/vax records; owner writes own diary/private gallery; vet **cannot browse the owner's personal gallery**. Owner cannot edit clinical records or appointments.
- Backend enforces RBAC/ownership on every protected endpoint. Server responses must not leak other owners' data or raw private records via photo URLs, tour status, calendars or appointment notifications.

### 3.2 Appointment transaction correctness
- Overlap with another **active** appointment always rejects; this is **not overrideable**.
- Outside weekly working hours or inside an unavailable interval: **warn**, then permit a veterinarian-confirmed exception only if no active overlap. A transaction must recheck conflicts at save time.
- Do not turn surrounding hours into general available slots because one exceptional appointment was approved.
- Cancelled appointments retain records but free calendar space; reschedule requires release/occupy consistency, and date/time change/cancellation triggers a role-scoped **in-app** notice.
- New SRS states Scheduled/Completed/Canceled; 0.6.3 has CONFIRMED/CANCELLED; team must decide exact wire semantics and states.

### 3.3 Reminders, dates and lifecycle
- Vaccine record cannot be deleted ordinarily. Reminder completed only by linked veterinarian-entered subsequent vaccination, **not by user marking read**.
- Owner archive hides reminders from own dashboard but leaves veterinarian overview intact; deceased pet suppresses active/future reminders while retaining history.
- Clinical record **soft deletion and restoration** are explicitly required by Section 5.2 SI-07 and must not cascade to separately maintained diary/vaccination data.
- Exact/Estimated DOB, veterinarian-selected life stage, chip status and partial record history are data-integrity concerns, not merely frontend labels.

### 3.4 Calendar projection and SRS delivery
- SRS Owner weekly calendar is authenticated and **available-only**. Current 0.6.3 public projection returns available/busy/closed intervals. Product/API owner must decide whether to preserve public API or introduce a separate owner-specific projection.
- Veterinarian schedule contains private exception/appointment content. Never send private appointments to an owner frontend and rely only on CSS to hide them.
- Versioned OpenAPI must govern frontend↔backend data and errors. New 5.2.4 resource list is **not** an endpoint list; URL choices in [proposal](../../api/srs-sync-proposal-2026-11-20.md) are illustrative.

## 4. Non-functional requirements: architectural mapping only

The QA-owned `non-functional-requirements.md`, `quality-measures.md`, and `test-scenarios.md` are unchanged. The original 15 NFRs are still design inputs, but changing the SRS requires QA reconciliation of test conditions.

| NFR | Mapped protection | Execution status |
|---|---|---|
| NFR-01 RBAC | Backend Auth/RBAC plus per-pet access | TEST NOT RUN |
| NFR-02 Privacy | Separate owner available-only projection, photo and notification scoping | TEST NOT RUN |
| NFR-03 Scheduling integrity | >=10 min, atomic hard collision, no silent override | TEST NOT RUN; fixtures must change for hours warning |
| NFR-04 Calendar consistency | Weekly intervals + exceptions + confirmed appointment occupancy | TEST NOT RUN; busy-times precedence OPEN |
| NFR-05 Diary ownership | Owner writes / vet read-only, missing day not normal | TEST NOT RUN |
| NFR-06 Clinical & vaccination security | Vet-only clinical change and vaccine records, owner view | TEST NOT RUN; add restore and read-status checks |
| NFR-07 Credential lifecycle | Password hashing, mandatory first change, revocation | TEST NOT RUN; Owner-ID login fixtures need revision |
| NFR-08 API consistency | Request validation, OpenAPI schemas, structured error outcomes | TEST NOT RUN |
| NFR-09 Time correctness | Clinic timezone Europe/Istanbul, local-day filters | TEST NOT RUN |
| NFR-10 Read performance | Query planning and indexing | TEST NOT RUN |
| NFR-11 Write performance | Database transactions and validation | TEST NOT RUN |
| NFR-12 Availability | Deployment health checks | TEST NOT RUN |
| NFR-13 Atomicity | No partial appointment/notification data states | TEST NOT RUN |
| NFR-14 Calendar clarity | Vet and owner presentations separated | TEST NOT RUN; UI design is teammate-owned |
| NFR-15 Upload validation | Protected JPEG/PNG access and size policy | TEST NOT RUN |

**QA mismatch warning:** The QA cases written for OpenAPI 0.6.3 may assume outside-hours denial, CONFIRMED/CANCELLED states, public BUSY/CLOSED calendar or email login. **No acceptance claim** is possible until API/QA files are revised after PO decisions.

## 5. Outstanding team decisions and submission gate

| Priority | Issue | Assigned review |
|---|---|---|
| **Editorial** | FR-08 now received; insert the supplied Must Have content as §3.8 in canonical SRS and remove duplicated §3.7 | Deniz (PO) |
| **Critical** | Owner ID/phone vs email login/account contract | PO + API/backend |
| **Critical** | Owner-only available intervals vs public calendar; block precedence | PO + API/backend/security |
| **Critical** | Appointment override warning handshake, statuses, notices, DB atomicity | PO + API/backend |
| **High** | Reminder read state vs completion, SRS status enum | PO + API/QA |
| **High** | Clinical **soft-delete and restore**, visibility, auditing and retention | PO + API/backend |
| **High** | New US-14 account status API and P2 release priority | PO + UI/API |
| **Medium** | Account/pet date accuracy, microchip and life-stage schemas | API/backend |
| **Medium** | SRS-selected Django/MySQL vs superseded DevOps “Django or FastAPI” text; align documentation | Technical lead/DevOps |

### Gate status
- [x] Updated 69-page SRS, Section 5.2 supplement and newly received **FR-08** slides reviewed against GitHub OpenAPI v0.6.3.
- [x] C4 L1–3, UML Use Case/Class/Sequence/State updated to **target** requirements with conflicts clearly labeled.
- [x] Separate proposed API changes and detailed issue ledger provided.
- [x] Team-owned API YAML, SRS, NFR, UI assets, CI/CD plans untouched.
- [ ] PO incorporates supplied FR-08 into canonical §3.8, fixes repeated §3.7 and confirms remaining open interface policy details.
- [ ] API owner publishes a revised, validated OpenAPI contract.
- [ ] Team approves Django/MySQL physical design, soft-delete behavior, permission boundaries and scheduling atomicity.
- [ ] QA executes acceptance tests; peer reviews and merges PR.

**Conclusion:** Architecture/UML designs have been **reconciled as proposals** with the updated 20 November SRSs. The published v0.6.3 contract still contains **substantive contradictions**. Treat the associated PR as **documentation for review**, not an API-compliant implementation.

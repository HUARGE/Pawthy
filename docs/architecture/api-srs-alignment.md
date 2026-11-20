# Pawthy — Architecture / API / SRS Alignment Review (Sprint 2)

> **Reviewer / owner:** Efe — Software Architect · **Review baseline:** 2026-11-19 · **Status:** **DESIGN REVIEW ONLY — formal Sprint 2 SRS review pending**  
> **Primary sources:** `develop` @ `a5c08a8534c8e6512f6621628bf751b434977d83`; `api/openapi.yaml` (OpenAPI 3.1.0, `info.version 0.6.3`); `docs/sprint-reports/Pawthy_Sprint1_Team10-1.md` (v0.5).  
> **Related QA documents:** separately shared `non-functional-requirements.md`, `quality-measures.md`, `test-scenarios.md`; those remain QA-owned, not modified by this review.

## 1. Scope and review method

The architect's assigned task is to **verify that the proposed architecture and UML are consistent with the current API and requirements**, not to rewrite a teammate's SRS, change the API specification, run QA tests or deploy the system. The review consists of:

1. Identify the current contract and correct product scope from Sprint 1 and the team's later messages.
2. Map all **13 current User Stories** to REST operations and to the C4/UML views created in this submission.
3. Check architectural boundaries: roles/ownership, public versus private calendar, manual appointment life cycle, diary/vaccine rules, static frontend and persistence assumptions.
4. Review all **15 QA NFRs** for **design implications**, without declaring metrics proven or claiming the 24 scenarios have run.
5. Record specific mismatches, uncertainty, validation actions and a final-SRS review gate.

**Verification vocabulary:**

| Label | Interpretation |
|---|---|
| **Aligned (design)** | Current Sprint 1 story and current OpenAPI agree about the modeled rule; no implementation/test proof implied |
| **Proposed** | Decomposition/technology/transaction approach is a design recommendation not mandated by the contract |
| **Open** | A source gap or conflicting team design statement requires an explicit decision |
| **Pending SRS** | The separate Sprint 2 SRS has not yet been inspected, so formal SRS compliance cannot be concluded |
| **Not Run** | Testing/measurement has not been carried out in the supplied evidence |

### 1.1 Source integrity and precedence

| Material checked | Result / guidance |
|---|---|
| Live GitHub `develop` | Contains `api/openapi.yaml`, Sprint 1 report, original context PNG, ADR template, CI/CD plan and contribution guidance. **No newly authored `docs/SRS.md` was present at checked HEAD.** |
| Live GitHub `main` + uploaded `Pawthy-main.zip` | Same **current API content**; ZIP is from `main` HEAD `337d9eb2addae6ee496504891a4ee4a3047a54d3`; `develop` also includes new documentation structure |
| `api/openapi.yaml` | API version **0.6.3**; inventory checked: **36 paths / 54 operations / 63 schemas** |
| Historical repo YAML `api/pawthy_openapi_v0.3.0.yaml` | **Not** the current architecture basis |
| Separately uploaded `pawthy_openapi_updated.yaml` | Old **v0.2.0** draft that supports owner appointment requests/approval; contradicts current scope and is not used as primary source |
| Separately uploaded `pet_health_portal_initial_backlog_updated.md` | Older **14-story** backlog with `PENDING_APPROVAL` request flow; superseded by current Sprint 1 **13-story** requirements |
| Uploaded Sprint 2 NFR + quality + scenarios | Explicitly refer to OpenAPI v0.6.3; define acceptance criteria; **tests not executed** |
| WhatsApp screenshots and `sprint 2 planlamasi.txt` | Efe owns C4, UML, API/SRS alignment; frontend changed to HTML/CSS/JS, and preliminary architect task text identifies use-case, class, sequence and state models |
| `docs/devops/ci-cd-plan.md` | Proposes backend framework **Django or FastAPI**, Nginx/static frontend and future Docker deployment; not a final runtime guarantee |

The formal Sprint 2 SRS (Deniz's deliverable) **must be checked when available**, rather than filling any missing SRS requirement with an assumption.

## 2. User Story → API → Architecture traceability (all US-01–US-13)

| Story | Current user goal and role boundary | Contract operations / important schemas | Architecture & UML coverage | Design assessment |
|---|---|---|---|---|
| **US-01** Create owner accounts | **Vet only** creates owner records, generates temporary password shown once; owner cannot register | `POST /pet-owners` (`OwnerAccountRequest`, `OwnerProvisioned`); `POST /pet-owners/{ownerId}/temporary-password` | C4 Auth/Owner modules; Use Case; Sequence 1 | **Aligned (design)** |
| **US-02** First login/password change | Owner must change temporary password; old credentials/tokens invalidated | `POST /auth/login` (`LoginResult.must_change_password`), `POST /auth/change-password`, `POST /auth/logout` | C4 Auth module; Sequence 1 | **Aligned (design)**; token storage/revocation not yet designed |
| **US-03** Role-based access | Owner own pets/diary; vet professional data; other-owner private records blocked | `x-roles`, bearer security, `401`/`403`/private `404` across endpoints | C4 trust boundaries; Use Case; Classes; all protected flows | **Aligned (design)**; runtime access matrix **Not Run** |
| **US-04** Pet profiles | Vet controls pet identity and clinical photo; owner only personal notes/photos and archive | `GET /pets`, `POST /pet-owners/{ownerId}/pets`, `GET/PATCH /pets/{petId}`, `GET/PATCH /pets/{petId}/personal-details`, `PATCH /pets/{petId}/archive`, photo endpoints; `Pet` | C4 Owner/Pet + protected media; Class A; Use Case | **Aligned (design)**; photo delivery policy **Open** |
| **US-05** Record diary | Owner enters/edits/deletes multiple timestamped health observations; vet read-only | `GET /symptoms`, `GET/POST /pets/{petId}/observations`, `PATCH/DELETE /pets/{petId}/observations/{observationId}`; `Observation`, `ObservationItem` | C4 Diary module; Class A; Use Case | **Aligned (design)** |
| **US-06** Review diary history | Both permitted roles see dates and symptom frequency, never diagnose or infer healthy missing days | `GET /pets/{petId}/observations`, `GET /pets/{petId}/observation-statistics`; `DiaryStatistics` | C4 Diary read model; Class A; Use Case | **Aligned (design)** |
| **US-07** Manage vet clinical records | Vet creates/edits records with minimally required visit data; owner cannot write | `GET/POST /pets/{petId}/medical-records`, `PATCH /pets/{petId}/medical-records/{recordId}`; `MedicalRecord` | C4 Medical module; Class A; Use Case | **Aligned (design)** |
| **US-08** View medical history | Authorized owner reads vet records for own pet; vet reads authorized clinic records | `GET /pets/{petId}/medical-records`; `MedicalRecordList` | C4 Medical module; Class A; Use Case | **Aligned (design)** |
| **US-09** Manage vaccination | Vet records vaccines and optional next due dates, supports linked new vaccination | `GET /species/{speciesCode}/vaccines`, `GET/POST /pets/{petId}/vaccinations`, `PATCH /pets/{petId}/vaccinations/{vaccinationId}`; `VaccinationRecord` | C4 Vaccination; Class A; Sequence 3 | **Aligned (design)** |
| **US-10** Vaccination reminders | Owner sees own; vet clinic-wide; 7-day upcoming / due / overdue; linked completion; deceased suppresses | `GET /vaccination-reminders`; `VaccinationReminder` status enum | C4 Vaccination; Class A; Sequence 3; State B | **Aligned (design)**; computation/storage mechanism **Proposed** |
| **US-11** Appointment availability | Vet manages weekly schedule, explicit available/busy blocks and exceptions; owner/public view private-safe times and phone | `GET/PUT /veterinarian/schedule`; `GET/POST /availability/blocks`, `PATCH/DELETE /availability/blocks/{blockId}`; `/busy-times`; `GET /availability/calendar`; `GET/PATCH /clinic/contact` | C4 Calendar and Public Projection; Class B; Sequence 4; Use Case | **Aligned (design)**; precedence between legacy/visual blocks **Open** |
| **US-12** Manual appointments | **Vet only** records/edits/cancels phone/in-person appointments; owner may read own history, never online book | `GET/POST /appointments`, `GET/PATCH /appointments/{appointmentId}`, `GET /veterinarian/calendar`; `ManualAppointmentInput`, `Appointment` | C4 Calendar; Class B; Sequence 2; State A; Use Case | **Aligned (design)**; atomic conflict design **Proposed** |
| **US-13** Theme | Owner and vet manually switch per-user saved Light/Dark preference | `GET/PATCH /me/theme`; `ThemePreference` | C4 UI Preferences; Class B; Use Case | **Aligned (design)** |

**Coverage tally:** 13 of 13 stories have a concrete API mapping and at least one architectural/UML view. This is **traceability coverage of a draft contract**, *not* 13 accepted implementation tests or 13 signed-off SRS checks.

## 3. Explicit API and scope compatibility checks

### 3.1 Current appointment rule — no online booking

- Source: Sprint 1 **US-11 / US-12** + API v0.6.3.
- `POST /appointments` `x-roles: [VETERINARIAN]`. A pet owner **cannot submit** an appointment request through the app. Vet manually records the appointment **after** phone/in-person agreement.
- The output `Appointment.status` has **only** `CONFIRMED` and `CANCELLED`. Cancellation leaves history intact but makes its interval free; reschedule requires another availability check.
- `starts_at` and `ends_at` define the interval; at least **10 minutes** required; overlapping active bookings rejected **409**; other validation failures follow documented response semantics (do not invent an exact code where none is explicit).
- The old v0.2.0 YAML / 14-story backlog's `PENDING_APPROVAL`, `REJECTED` and vet-approval queue **must not appear** in Sprint 2 C4/UML.

**Design result:** **Aligned**; no owner-initiated booking/approval models used. **Implementation verification: Not Run**.

### 3.2 Public vs. private calendar (critical security boundary)

| API | Audience | Information allowed |
|---|---|---|
| `GET /availability/calendar` | Public `security: []` | `AvailabilityCalendar` → `timezone`, `intervals` (`starts_at`, `ends_at`, `status`), `clinic_phone_number`, optional `view`; **no raw Appointment** |
| `GET /veterinarian/calendar` | Vet-only | `VeterinarianCalendar` with private events and appointment details |
| `GET /appointments` | Vet and owner with role/data filtering | Vet clinic-wide; owner **own** recorded appointments only |
| `GET /clinic/contact` | Public | Clinic phone and display message |

**Architectural enforcement:** PublicCalendarProjection is a distinct backend component. Filtering or merely hiding information in the browser does **not** satisfy NFR-02. A correct response **must not** contain another owner's name, pet, clinical note or reason. Documented statuses: `AVAILABLE`, `BUSY`, `CLOSED`.

### 3.3 Authorization and record ownership

| Action | Vet | Pet Owner | Public |
|---|---|---|---|
| Provision owner and create vet pet identity | Create/update | No | No |
| Read own-pet clinical history | Clinic-authorized | Own pets only | No |
| Write vet clinical/vaccination data | Yes | **No** | No |
| Read owner diary | Authorized clinic pets | Own pets | No |
| Write diary entry | **No** | Own records only | No |
| Read/edit owner-private notes/gallery | **No** | Own pets only | No |
| Manage hours, blocks, appointments | Yes | No | No |
| Read calendar time-only availability/contact | Yes | Yes | Yes |
| Read/update own theme | Yes | Yes | No |

**Status distinctions:** `401` missing/invalid protected bearer, `403` authenticated but disallowed role, `404` other-owner private object not accessible. The table states architectural **intended permissions**, not live enforcement evidence.

### 3.4 Calendar rules and time offsets

- Weekly working hours define baseline; explicit `AVAILABLE` and `BUSY` blocks adjust it. Legacy `/busy-times` adds exceptional closure/blocked periods. Noncancelled appointments count as busy. Outside working hours should be `CLOSED` unless explicit `AVAILABLE` opens them.
- `POST /availability/blocks` and `PATCH /availability/blocks/{blockId}` document **409** for conflicting explicit blocks; do not assert exact behavior for every combination of old `/busy-times` and newer `/availability/blocks` until reviewed.
- Appointment and calendar instants are ISO 8601 with UTC offset and Europe/Istanbul clinic semantics. Date-boundary and UTC conversion behavior requires tests; documentation alone cannot prove correctness.

### 3.5 Vaccinations, diary and images

- Vet vaccination writes include optional `next_due_date`; reminders appear **7 days before due date**, `DUE_TODAY` and overdue, and can finish as `COMPLETED` or `DISCONTINUED`. In-app only.
- Diary changes are owner-managed; missing observation days remain **unknown/unrecorded**, not a default healthy state. Symptom statistics only reflect reported observations.
- Owner gallery and private notes are not available to vet; clinical identification photo is vet-managed. An object-storage URL alone must not bypass access checks. Photo delivery/security design needs implementation review.

## 4. NFR-01–NFR-15 — Architectural design review, not QA test execution

| NFR | Required design safeguard or measurable target | Model location | Architect's review state |
|---|---|---|---|
| **NFR-01** RBAC | Protected endpoints, 401/403/private 404 and owner/pet isolation | C4 Auth boundary; UML Use Case and Sequences | **Design mapped**; TS-01–03 Not Run |
| **NFR-02** Privacy | Public availability must reveal **0** private values | C4 Public Projection; UML Sequence 4 | **Design mapped**; TS-04 Not Run |
| **NFR-03** Scheduling integrity | >=10 min, no confirmed overlap, correct cancellations; concurrency safeguards | Calendar component; UML Sequence 2 / State A | **Design mapped**; TS-05–08 Not Run |
| **NFR-04** Calendar consistency | Working hours, AVAILABLE/BUSY blocks, appointment precedence; 0 fixture discrepancies | Calendar + Public Projection; Class B | **Design mapped**; precedence question **Open**; TS-09–10 Not Run |
| **NFR-05** Diary ownership | Owner writes; vet read-only; no fabricated healthy dates | Diary component, Class A | **Design mapped**; TS-11–12 Not Run |
| **NFR-06** Clinical/vaccine permissions | Vet write; owner read; linked reminder status transitions | Medical + Vaccination; Sequence 3 | **Design mapped**; TS-13–14 Not Run |
| **NFR-07** Credential lifecycle | Password change/logout invalidate prior tokens; login may rate-limit with 429 | Auth component; Sequence 1 | **Design mapped**; token design **Open**; TS-15–16 Not Run |
| **NFR-08** API consistency | `items/next_cursor` pagination; documented status/schema; problem+json | API Adapter/Controllers | **Design mapped**; TS-17 Not Run |
| **NFR-09** Time correctness | ISO 8601 offsets; Europe/Istanbul range filtering | Calendar/Appointment module | **Design mapped**; TS-18 Not Run |
| **NFR-10** Read latency | **p95 ≤ 2s**, unintended error rate <1%, 20 VUs / 5m | API / DB indexing & query planning *proposed* | **Acceptance target**, not measured; TS-19 Not Run |
| **NFR-11** Write latency | **p95 ≤ 3s**, unintended error rate <1%, 10 VUs / 5m | Transaction boundary *proposed* | **Acceptance target**, not measured; TS-20 Not Run |
| **NFR-12** Availability | **≥99%** scheduled checks over 7 days | Hosting/runtime monitoring outside logical C4 | **Acceptance target**; health endpoint not in API; TS-21 Not Run |
| **NFR-13** Atomicity | **0 inconsistent partial states / 20** failure injections | Calendar/Reminder transaction boundaries *proposed* | **Acceptance target**, not measured; TS-22 Not Run |
| **NFR-14** Calendar clarity | **≥4/5** participants classify availability correctly | UI semantics + public projection | **Acceptance target**; Figma/UI belongs to UI team; TS-23 Not Run |
| **NFR-15** Upload validation | Only JPEG/PNG up to **5 MiB**, role restricted | Media service; `multipart/form-data` endpoints | **QA acceptance policy** not guaranteed by API numeric schema; TS-24 Not Run |

The 15 NFRs and test references above are **reviewed as source inputs**; their authorship, execution and ownership remain with QA. No `Pass` has been assigned to a runtime criterion, because the supplied test report says **all TS-01–TS-24 are Not Run**.

## 5. Issues, conflicts and architecture decisions for 22–23 November review

| ID | Severity | Finding | Proposed architect action (not altering teammates' work) | Owner to confirm |
|---|---|---|---|---|
| **A-01** | High | **Separate Sprint 2 SRS not yet in `develop`**; cannot formally assert compliance to a document not received | Re-run story/API/UML matrix when SRS becomes available; record discrepancies and revise only our models | Deniz (SRS) + Architect |
| **A-02** | High | Sprint 1 and WhatsApp say Django, but later CI/CD plan says **Django or FastAPI** | Keep “Python REST backend, framework TBD”; request formal decision/ADR; do not rewrite DevOps plan | Technical team |
| **A-03** | High | Two competing calendar exception concepts: `/busy-times` and `/availability/blocks` | Document collision cases and precedence; determine whether legacy path remains supported before implementation | Backend/API owner + Architect |
| **A-04** | High | Photo responses expose URL fields but delivery authorization and media backend are not specified | Decide protected upload/delivery strategy and owner-only vs clinical photo controls | Backend/API owner |
| **A-05** | High | Simultaneous vet appointment requests could race; OpenAPI requires conflict rejection but does not define DB locking | Define atomic conflict checking in implementation; preserve `409` and avoid double booking | Backend/API owner |
| **A-06** | Medium | Credential invalidation mandated but specific bearer/JWT/session revocation not designed | Document architecture decision; guarantee logout/password change invalidation in final implementation | Backend/security owner |
| **A-07** | Medium | NFR-15 5 MiB JPEG/PNG policy is **not encoded** numerically in OpenAPI | Note as QA target and possible API clarification; do not silently change YAML | QA + API owner |
| **A-08** | Medium | DevOps draft calls `/health` after deploy but **no `/health` in API** | Flag as separate implementation/operations decision, not an existing endpoint | DevOps + Backend |
| **A-09** | Medium | Appointment validation has exact `409` conflict but some constraints have no universally defined HTTP error | Confirm expected per-case response codes before test automation | API owner + QA |
| **A-10** | Medium | `develop` document layout includes `docs/architecture/`; WhatsApp task suggestions named `docs/c4-architecture/` | Place C4 and alignment docs in existing `docs/architecture/`, UML in `docs/uml-diagrams/` as new folder; confirm final path in PR | Repository maintainer |
| **A-11** | Low | UI mockups show optional design choices; color/theme is UI team's domain | Model only user-facing capabilities; do not prescribe/implement Figma design | UI team |

### 5.1 Proposed decision priority

**Must resolve to finalize architecture:** A-01, A-02, A-03, A-04, A-05, A-06.  
**Can remain tracked without inventing implementation:** A-07–A-11.  
Do not rewrite the PO's SRS/backlog, QA's thresholds, DevOps plan, or UI designs as part of the architect's PR; bring questions to review and update **architect-owned documentation** when decisions are made.

## 6. Release-independent submission gate

| Check | Evidence | Status |
|---|---|---|
| Current correct source version | `develop` API v0.6.3, Sprint 1 13 stories | **Verified (source inspection)** |
| C4 Level 1 / 2 / 3 | `docs/architecture/c4-architecture.md` | **Draft complete** |
| UML Use Case, Class, sequences, states | `docs/uml-diagrams/uml-diagrams.md`, `use-cases.puml` | **Draft complete** |
| All US-01–US-13 architecture traceability | Section 2 of this document | **Draft mapped, 13/13** |
| NFR-01–15 design mapping | Section 4 | **Draft mapped, 15/15**; execution **Not Run** |
| Formal final SRS consistency | Final Sprint 2 SRS not supplied at baseline | **Pending SRS** |
| Runtime correctness and performance | QA tests all Not Run, implementation not inspected | **Not Verified** |
| GitHub Issue, PR and peer approval | No new GitHub changes made for these deliverables | **Pending team workflow** |

### 6.1 Acceptance checklist for architect PR

- [ ] Reconfirm `develop` HEAD and OpenAPI before creating PR.
- [ ] Ask repository owner whether `docs/architecture/` + `docs/uml-diagrams/` is accepted.
- [ ] Confirm final Django/FastAPI and calendar precedence decisions, then synchronize diagrams.
- [ ] Read Deniz's committed Sprint 2 SRS and capture a final consistency pass/diff.
- [ ] Keep QA and UI deliverables untouched; review comments may point to issues, not overwrite their files.
- [ ] Submit the architect-owned files from a **feature branch from `develop`** with linked issue and review.

## 7. Traceable source links (repository)

- [Current OpenAPI v0.6.3](https://github.com/HUARGE/Pawthy/blob/develop/api/openapi.yaml)
- [Sprint 1 Project Foundation (13-story source)](https://github.com/HUARGE/Pawthy/blob/develop/docs/sprint-reports/Pawthy_Sprint1_Team10-1.md)
- [Original Context Diagram](https://github.com/HUARGE/Pawthy/blob/develop/docs/architecture/system-context-diagram.png)
- [Draft CI/CD Plan](https://github.com/HUARGE/Pawthy/blob/develop/docs/devops/ci-cd-plan.md)
- [Contribution and Branch Rules](https://github.com/HUARGE/Pawthy/blob/develop/CONTRIBUTING.md)

**Conclusion:** The **proposed** C4 and UML models are traceable to the **current** Sprint 1 working requirements and OpenAPI v0.6.3. The review **does not assert formal SRS sign-off or functioning software**; those outcomes require Deniz's Sprint 2 SRS, teammate architecture decisions, implementation and actual QA evidence.

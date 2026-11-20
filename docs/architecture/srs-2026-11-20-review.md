# Pawthy Sprint 2 — 20 November 2026 SRS Reconciliation and Decision Ledger

> **Status:** Architecture review complete as a *document comparison*; product/API decisions, implementation, tests and merge are **not** complete.  
> **Requirements source:** 69-page updated Functional Requirements SRS, five-page Section 5.2 supplement, and **FR-08 View Veterinary Clinical Records** screenshot screenshots starting on slide 31/222 (Must Have), all supplied in team chat; the 69-page source PDF was not edited by this PR.  
> **Current contract compared:** [OpenAPI v0.6.3](../../api/openapi.yaml) and Sprint 1 working report on `HUARGE/Pawthy:develop`.  
> **Author/role:** Efe — Software Architect. Proposed data structures and URLs below are recommendations, not claims about implemented endpoints.

## 1. Document quality and source precedence

The 69-page SRS has sections 3.1–3.7 and 3.9–3.14. **Section 3.7 (US-07) is duplicated and §3.8 is absent from that PDF.** Subsequently Deniz/the team supplied **FR-08 — View Veterinary Clinical Records**, Primary Actor **Pet Owner**, priority **Must Have**, in screenshot screenshots starting on slide 31/222. **FR-08 now has known requirements and acceptance criteria**; it should replace the duplicate §3.7 *in the canonical SRS* rather than simply renumbering a copy of US-07.

US-14 ("First-Time User Guided Tour") is new, **P2/Should Have**, and explicitly *not mandatory for initial Sprint 3 MVP*. It does not retroactively make the pre-SRS 13-story OpenAPI specification a 14-story compliant contract.

**Review rule:** New SRS describes target functionality. Published OpenAPI v0.6.3 remains the current API specification until API owner accepts a revision. Architecture/UML can describe the SRS target **only while marking mismatches**. Do not silently rewrite API-controlled files or teammates' source documents.

## 2. User-story traceability and discrepancy matrix

| US | SRS evidence and expected behavior | Current API baseline | Architect assessment |
|---|---|---|---|
| **01** | FR-01.1–01.9: only Vet provisions owner, mandatory **full name + phone**, stable generated **Owner ID independent of phone** | `OwnerAccountRequest` requires `full_name + email`; `OwnerAccount` has `id,full_name,email` | **Conflict A01:** request/response/validation and phone self-update changes |
| **02** | FR-02.3–02.13: login with **Owner ID + temporary password**, mandatory initial password replacement, veterinarian-only recovery, session invalidation | `LoginRequest.email` is required; change password exists | **Conflict A02:** login identifier; retain security and password rules |
| **03** | FR-03.1–03.12: two authenticated roles; server-side ownership restrictions; separate owner-private photos | Existing bearer/`x-roles` model | **Partial alignment:** new routes and gallery access must preserve RBAC |
| **04** | FR-04.09–04.22: vet-selected `life_stage`, exact/estimated birth date, mandatory microchip status/number condition, official-vs-private photos, deceased/archive behavior | `Pet` has `date_of_birth`, `estimated_age_months`, optional `microchip_number`, but no accuracy/life stage/status fields | **Conflict A03:** target pet schema and validations |
| **05** | FR-05.4/05.12–15: owner observation can be in past, no future, `created_at` distinct from `observed_at`, symptom or written note mandatory, "Normal" exclusive | `Observation` / diary endpoints | **Review A04:** timestamp/validation details need API confirmation |
| **06** | FR-06.4–06.12: each symptom counted once per *calendar day*; default Last 30 Days, optional 90 Days/All Time; missing day never "healthy" | Statistics endpoint exists | **Review A05:** exact counting and filters need confirmation |
| **07** | FR-07.3/07.9/07.16 and supplementary **SI-07**: visit date/type mandatory, diagnosis optional; Vet **soft-deletes and restores** clinical records while preserving diary/vaccination data | Current route has GET/POST and record PATCH, **no soft-delete/restore** | **Conflict A06:** lifecycle endpoint, history visibility, audit and retention semantics |
| **08** | Separately supplied **FR-08 — View Veterinary Clinical Records (Must Have)**: owner authenticated and owns selected pet; newest-first; show populated exam, diagnosis, treatment, meds, vet notes; omit blank optional fields; owner read-only; soft-deleted invisible until vet restores; informative empty state; other-owner denied | API GET already includes US-08, PET_OWNER, most-recent-first and access errors; deletion filtering and UI need verifying | **Resolved substantive gap A07:** source content received; **editorial** insertion into canonical PDF still needed |
| **09** | SRS vaccination: vet entry/edit, no ordinary vaccine deletion | Vaccine GET/POST/PATCH and no DELETE | Broadly consistent in contract; runtime not checked |
| **10** | SRS reminder states **Scheduled, Upcoming, Due, Overdue, Completed, Suppressed**; separate `is_read`; archive hides from owner's active feed only; deceased suppresses | API enum `UPCOMING,DUE_TODAY,OVERDUE,COMPLETED,DISCONTINUED` and no mark-read operation | **Conflict A08:** status mapping, read state, archive/death behavior |
| **11** | FR-11.03–18: multiple daily working windows; exceptions; Vet calendar shows available/unavailable/occupied; **authenticated owner** weekly view lists *available intervals only* with clinic phone | `GET /availability/calendar` is `security: []` public and returns `AVAILABLE/BUSY/CLOSED`; `/busy-times` and `/availability/blocks` coexist | **Conflict A09:** public policy, owner projection, exception precedence |
| **12** | FR-12.1–22: Vet only, same-owner pets, >=10min; **reject overlap**; out-of-hours/unavailable **warning + explicit vet override** if no overlap; **Scheduled/Completed/Canceled**; in-app changed-time/cancellation notice | API currently rejects outside hours, statuses `CONFIRMED/CANCELLED`, no notice route | **Conflict A10:** two distinct validation paths, statuses, notification, concurrent save |
| **13** | FR-13.01–09: manually saved per-user Light/Dark, light default; change does not mutate records | `/me/theme` | Written design alignment; no UI test |
| **14** | FR-14.01–16: Pet Owner only, 4-step optional tour after first required password change, dim/highlight, Next/Back/Skip, persistent account status, Help replay without reset; P2 | API has no guided-tour endpoint/schema | **New gap A11:** proposed account persistence API + UI design |

### 2.1 Especially important permission boundaries

| Resource | Vet | Pet Owner | Public |
|---|---|---|---|
| Owner provision / official pet identity | Create/update | Read own; update own phone only | None |
| Clinic pet photo | Manage official | View own pets | None |
| Owner personal gallery | **No access** | Manage own photos | None |
| Owner diary | View only | Create/edit/delete own | None |
| Clinical records | CRUD subject to confirmed deletion and retention policy | View own pet only | None |
| Vaccine records | Create/update, no ordinary delete | View own pet only | None |
| Reminder read state | System/vet clinic monitoring | Mark own reminder read, **not completed** | None |
| Calendar/appointment edit | Vet only | Read available-only weekly calendar and own appointments | Clinic contact; whether calendar is public **pending decision** |
| Guided Tour | No auto-tour | Own account's tour + replay | None |

**Security assumption:** The web UI is not an authorization boundary. Every API read/write must check role and owner/pet scope server-side; media references and notifications also require isolation.

## 3. SRS source-specific details that must not be conflated

**Appointment conflict vs working-hours warning (US-12):** FR-12.11 and AC-12.05 require an unconditional rejection for *overlapping active appointments*; FR-12.12–13 and AC-12.06 **allow** out-of-hours or otherwise unavailable appointments after vet acknowledgment if no active overlap. The override must never bypass collision checking. SRS FR-12.14 says an out-of-hours appointment must not automatically open adjacent availability intervals. FR-12.17–18 notify the owner **inside the app** for date/time change and cancellation; automated upcoming-appointment reminders are out of scope.

**Calendar view (US-11):** SRS owner view displays *only* available times while the veterinarian sees Available/Unavailable/Occupied. “Excluded from selectable or listed available options” does not by itself authorize unauthenticated public disclosure of the full clinic schedule. There is an unresolved difference with v0.6.3's public BUSY/CLOSED response.

**Reminder read state (US-10):** SRS AC-10.05 says marking read leaves the active reminder in place and does not satisfy the vaccination. Archive hides active reminder from *that owner* while vet still monitors it; death suppresses active/future reminders. A new linked vaccination completes the prior reminder; a subsequent due date can start a different reminder.

**Guided Tour (US-14):** Four steps are **My Pets → Health Diary → Health Records → Vaccinations & Reminders**. Persisted account status is *Not Started, Completed, Skipped*. Tour replay is allowed but must preserve the stored status and not make onboarding mandatory. Onboarding does not require any automated medical recommendation.

## 4. Decisions required — tracked; no silent API edits

| ID | Priority | Decision needed | Primary reviewer |
|---|---|---|---|
| A01 | Critical | Phone mandatory/independent Owner ID; remove **mandatory** email and expose own-phone update | Deniz + API owner |
| A02 | Critical | Owner-ID login wire schema, reset security, token/session invalidation | Backend/API security |
| A03 | High | Pet birth date accuracy, life stage, chip-status representation/validation; avoid automatic medical stage | API/Backend + Deniz |
| A04 | Medium | Observation create/observed timestamps, validation for future and Normal field | API/QA |
| A05 | Medium | Symptom distinct-day aggregation, default time filters | API/QA |
| A06 | High | Vet-confirmed clinical delete and retention/no-cascade rules | Deniz + Backend |
| A07 | Editorial (source) | **FR-08 now supplied (7 functional requirements, 8 acceptance criteria, Must Have)**. Remove duplicate §3.7 and insert FR-08 as §3.8 in the next authoritative SRS | Deniz |
| A08 | High | Reminder lifecycle status mapping; mark-read independent from completion; archive/deceased filtering | API/Backend |
| A09 | Critical | Public vs authenticated owner calendar; available-only projection; legacy `/busy-times` precedence | Deniz + API/Security |
| A10 | Critical | Incompatible appointment status enum; override ACK protocol, concurrent hard conflicts, in-app change/cancel notification | Deniz + API/Backend |
| A11 | P2 | Guided-tour owner-account API, account state, replay semantics; defer from first MVP | Deniz + API/UI |
| A12 | High | Django vs FastAPI final stack ADR; Sprint 1 favors Django, CI/CD draft leaves alternative | Technical team |
| A13 | High | Private photo delivery/storage and bearer session revocation strategy | Backend/Security |
| A14 | Medium | NFR/QA checks, proper error codes, calendar time-zone and media constraints | QA + API |

## 5. Test ideas / acceptance scenarios (not executed)

1. Change phone; generated Owner ID and login continue working; unrelated owner cannot read contact details.
2. Vet creates pet with estimated birth date; displayed age is marked approximate. Chip Yes with missing chip number must reject. Life stage update must not change birth date or sex.
3. Owner diary future observation is rejected; frequency counts two entries with same symptom on same day as **one reported day**.
4. Vet soft-deletes a clinical record after confirmation, verifies it is recoverable and can be restored; independent diary and vaccination records still exist. Owner cannot delete it.
5. Mark an Upcoming reminder read; it remains active. Archive pet hides from owner dashboard but not vet monitoring; deceased suppresses reminders without erasing history.
6. Vet saves a non-overlapping 15-minute appointment during working hours. Second overlapping appointment must be rejected even with override flag.
7. Non-overlapping out-of-hours appointment: warning shown; cancel leaves no record; explicit veterinarian confirmation permits save. Adjacent outside-hours intervals stay unavailable.
8. Vet reschedules then cancels: owner receives correct role-scoped in-app notices; old times free; canceled history retained.
9. Owner calendar shows only available weekly intervals; no other owner ID, pet, visit reason, busy appointment content or edit control.
10. After first password change, Owner sees tour; skipping it preserves account status across device logins; Help replay doesn't reactivate automatic tour; Vet never auto-sees it.

## 6. Review sign-off gate

- [x] Supplied SRS reviewed and compared to published OpenAPI.
- [x] Updated architecture and UML models for known new SRS behaviors.
- [x] Incorporated **FR-08 Must Have** source screenshot, resolved the functional gap, retained canonical §3.7/§3.8 formatting fix as editorial follow-up.
- [x] Explicit high-risk gaps documented rather than calling current API compliant.
- [ ] Product Owner corrects source editorial issue and signs off on behavior priorities.
- [ ] API owner updates/versions OpenAPI; security and backend teams review contracts.
- [ ] QA executes actual tests; no tests claimed passed here.
- [ ] Team merges this architect-only documentation PR after review.

**Companions:** [C4 architecture](c4-architecture.md) · [UML diagrams](../uml-diagrams/uml-diagrams.md) · [API change proposal](../../api/srs-sync-proposal-2026-11-20.md) · [Architecture/API alignment](api-srs-alignment.md).

## 7. Supplementary Section 5.2 Software Interfaces (5-page PDF) — incorporated 20 November

This is a **subsequent SRS source**, not a code implementation report.

| Requirement | Source detail | Architecture/action |
|---|---|---|
| **5.2.1** Overview | Browser frontend, backend, relational persistence with a consistent interface contract | C4 Levels 1–3 separate systems at the appropriate view |
| **5.2.2** Technology Interfaces | **HTML/CSS/JavaScript**, **Python/Django**, **MySQL**, **OpenAPI/YAML** selected | Treat Django/MySQL as **SRS-selected**, not an architect-only proposal; superseded DevOps alternative should be reconciled |
| **SI-01** API-Based Communication | Frontend ↔ backend HTTP API using JSON where appropriate | Model explicit REST boundary |
| **SI-02** Request Validation | Backend validates required fields, data formats, ownership and business rules | C4 controllers and domain validation |
| **SI-03** Authorization Enforcement | Backend enforces authentication and resource-level permissions | Server-side role + object access, not client-only hiding |
| **SI-04** Consistent API Responses | Structured success/error results suitable for user-facing feedback | OpenAPI response/error schemas; error mapping review needed |
| **5.2.4** Resource Interfaces | Auth, owners, pets, diary, clinical, vaccination, reminders, schedule, appointments, preferences and guided-tour status | This is **a capability list, not approved URL paths**; API owner must version the actual operations |
| **SI-05** Database Access | Only backend accesses MySQL; no direct browser DB reads | Keep DB private behind Django service |
| **SI-06** Referential Integrity | Valid owner/pet/clinical/vaccination/appointment relationships | Transaction and FK constraints in implementation design |
| **SI-07** Data Lifecycle Operations | **Modification vs soft deletion vs archival vs restoration vs deceased** must remain distinct; historical information preserved | **Corrected the earlier clinical delete assumption:** implement recoverable soft deletion and veterinarian-authorized restoration; no unrelated data cascades |
| **5.2.6** Integration Boundaries | No external veterinary, booking, Google Calendar, email/SMS, payment or AI providers | Do not add external dependencies to logical architecture |
| **5.2.7** Contract Consistency | OpenAPI is the frontend/backend contract; finalize after approved requirements changes | **v0.6.3 does not yet meet target SRS**; review before acceptance |

**Review gate:** A prior requirements section saying “delete” does not justify irreversible deletion. The supplementary SRS **explicitly calls for soft deletion and restoration** and should control the lifecycle design. Product Owner should clarify precise retention, who can restore, visibility defaults and audit events. Existing OpenAPI has neither operation.

### Remaining project-source gaps

The supplementary 5.2 refers to **Section 5.1 user interface** and **Section 4.2 data model/integrity**; those sections were **not supplied in this new five-page file**. No assertions about their precise fields, UI mockups or physical database tables can be made based on the 5.2 excerpt alone.

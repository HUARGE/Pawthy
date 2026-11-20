# Pawthy — Proposed API Delta for Updated SRS (20 November 2026)

> **Status: Proposal, not published OpenAPI.** The active `api/openapi.yaml` on `HUARGE/Pawthy:develop` is version **0.6.3**. The architect is **not** changing the team-owned API contract in this PR. Paths, enums and request examples below must be reviewed, versioned and implemented by the API/backend owners.
>
> **Evidence:** Deniz's 69-page updated Functional Requirements SRS, the five-page supplementary *5.2 Software Interfaces* section, and separately supplied **FR-08 View Veterinary Clinical Records** slides (31–32/222; Must Have). The latter **selects HTML/CSS/JavaScript + Python/Django + MySQL + OpenAPI/YAML**, states only the backend may access MySQL, and explicitly requires **soft deletion and restoration** of clinical records.

## 1. Contract gap register

| Area | Existing 0.6.3 contract | Revised SRS requires | Review action |
|---|---|---|---|
| Account creation | `OwnerAccountRequest` requires `full_name, email` | `full_name, phone_number` mandatory; system-generated Owner ID independent of phone | Add required phone, make email optional if retained, immutable owner ID, authorized phone edit |
| First login | `LoginRequest.email` | Pet Owners authenticate with Owner ID + password; first-login replacement mandatory | Define owner vs veterinarian login variants, preserve reset/session revocation |
| Pet profile | `date_of_birth`, `estimated_age_months`, `microchip_number` | Exact/Estimated DOB, veterinarian-selected life stage, microchip status and conditional number | Update create/patch/read models and validation |
| Clinical records | GET/POST/PATCH, **no DELETE/restore** | Vet-authorized **soft deletion and restoration**; keep clinical history and unaffected vaccination/diary records | Add lifecycle endpoints and soft-delete read/visibility policy; prevent cascades |
| Diary | observation endpoints/statistics | Past-only observation date, separate creation timestamp, symptom frequencies by distinct calendar day | Clarify validation, UTC/local day filters and response fields |
| Vaccination reminders | `UPCOMING, DUE_TODAY, OVERDUE, COMPLETED, DISCONTINUED`, no read status | `Scheduled, Upcoming, Due, Overdue, Completed, Suppressed`, independent owner mark-read, archived vs deceased behavior | Agree enum mappings, read-status route and visibility |
| Owner calendar | `GET /availability/calendar` is public and includes BUSY/CLOSED | Authenticated owner sees only AVAILABLE weekly times + phone; vet gets full private view | Decide versioned owner-specific projection vs changing public route; maintain privacy |
| Appointments | `CONFIRMED, CANCELLED`; outside-hours blocked; no notice endpoint | `Scheduled, Completed, Canceled`; hard overlap rejection; out-of-hours vet warning/ACK exception; in-app owner reschedule/cancel notices | Version status enum, warning handshake, write transaction and notification contract |
| Guided tour | No route/schema for US-14 | P2 four-step Pet Owner only; persisted Not Started/Completed/Skipped; Help replay without reset | Propose minimal account-level status read/patch API |
| **FR-08** (separately supplied) | `GET /pets/{petId}/medical-records` already `x-user-stories: [US-07, US-08]`, permits PET_OWNER and responds **Most recent first** | Must Have: authenticated owner sees only own selected pet's non-soft-deleted records newest-first; show **all populated** clinical/veterinarian-note fields, omit blank optional fields, no write controls, cross-owner denial and informative empty state | **Already partially aligned**; explicitly specify soft-delete exclusion and add acceptance tests for field presentation/empty state; canonical 69-page PDF still needs §3.8 insertion |

## 2. Candidate schema examples (illustrative only, not copy-paste into 0.6.3)

```yaml
OwnerAccountRequest:
  type: object
  additionalProperties: false
  required: [full_name, phone_number]
  properties:
    full_name: {type: string, minLength: 1}
    phone_number: {type: string, minLength: 1}
    email: {type: string, format: email} # optional, only if product owner retains it

OwnerAccount:
  type: object
  required: [id, full_name, phone_number]
  properties:
    id: {type: string, readOnly: true, description: "Stable generated Owner ID unrelated to phone"}
    full_name: {type: string}
    phone_number: {type: string}
    email: {type: string, format: email}

OwnerLoginRequest:
  type: object
  additionalProperties: false
  required: [owner_id, password]
  properties:
    owner_id: {type: string}
    password: {type: string, format: password}

PetIdentityExtension:
  type: object
  properties:
    birth_date: {type: string, format: date}
    birth_date_accuracy: {type: string, enum: [EXACT, ESTIMATED]}
    life_stage: {type: string, description: "Veterinarian-selected, species-dependent"}
    microchip_status: {type: string, enum: [YES, NO]}
    microchip_number: {type: string, description: "Required when microchip_status=YES"}

GuidedTourStatus:
  type: object
  required: [status, updated_at]
  properties:
    status: {type: string, enum: [NOT_STARTED, COMPLETED, SKIPPED]}
    updated_at: {type: string, format: date-time}
```

**Important:** The owner login schema alone cannot replace the unified `LoginRequest` without specifying how the **veterinarian** signs in. The backend team must decide a valid `oneOf`/discriminator design or distinct endpoints with equal security.

## 3. Candidate API operations / semantics

| Suggested capability | Possible method/path | Required protection and behavior |
|---|---|---|
| Update own phone | `PATCH /me/contact` | PET_OWNER; own account only; owner ID remains unchanged |
| Soft-delete clinical record | `DELETE /pets/{petId}/medical-records/{recordId}` | VETERINARIAN; logical delete only; no cascaded vaccination/diary deletion; confirmation workflow and audit |
| Restore clinical record | `POST /pets/{petId}/medical-records/{recordId}/restore` | VETERINARIAN; restore previous soft-deleted record subject to retention policy |
| Mark own reminder read | `PATCH /vaccination-reminders/{reminderId}/read` | PET_OWNER; own pet; change read flag only, never vaccine completion |
| View own appointment notifications | `GET /me/appointment-notifications` | PET_OWNER; own notices on date/time changes or cancellation |
| Mark notification read | `PATCH /me/appointment-notifications/{id}` | PET_OWNER; own notification only |
| Read guided-tour status | `GET /me/guided-tour` | PET_OWNER; account-stored NOT_STARTED/COMPLETED/SKIPPED; initial eligibility only after password change |
| Save completion/skip | `PATCH /me/guided-tour` | PET_OWNER; persist COMPLETED or SKIPPED; **replay does not reset status** |
| Owner weekly available-only view | versioned `GET /me/availability` **or** new representation of existing calendar | Authenticated owner, no BUSY/CLOSED item, no owner/pet/visit identifiers, include clinic phone |
| Appointment hours override | existing `POST/PATCH /appointments` **with approved warning ACK protocol** | VETERINARIAN only; hard active-overlap rejection persists even with ACK; no adjacent hours auto-opened |

**Endpoint naming is non-binding.** The source's section 5.2.4 specifies resource capabilities rather than actual HTTP paths. Any new endpoint requires API owner acceptance, full operation schemas, authentication, authorization, response codes and error examples in updated OpenAPI.

### Candidate scheduling request interaction

1. Validate owner-pet relationship, duration (minimum 10 minutes), role and input.
2. Check **overlap with active appointments**: always reject; no override.
3. If outside hours or blocked, return a documented **warning/acknowledgment flow**, not an ordinary success or permanent rejection.
4. If the vet explicitly confirms, **revalidate** ownership and conflicts **atomically** before saving.
5. Save `SCHEDULED` under approved status mapping; on reschedule/cancel notify only the affected owner in-app. A canceled appointment frees its interval but remains in history.
6. No owner online scheduling, SMS/email, payment integration or automatic upcoming-appointment reminders.

### Candidate data-lifecycle interaction

- Soft-delete clinical visits with stored deletion metadata (e.g., `deleted_at`, `deleted_by`) and vet-only restore, **if backend approves physical fields**. **FR-08 explicitly requires** deleted records to be excluded from the owner's view until veterinarian restoration; only vet-authorized administration may access deleted versions under a separately approved policy.
- A pet's “archived by owner” flag affects the owner's active list, not clinic data or vet vaccination monitoring.
- “Deceased” suppresses vaccination reminders while retaining vaccination and clinical history.
- Vaccination records **must not be exposed via a delete operation** under SRS US-09.
- Owner-diary deletion is separately managed and does **not** cascade from a clinical visit.

## 4. SRS 5.2 Software Interfaces — architectural invariants

- **SI-01:** HTML/CSS/JavaScript frontend ↔ Django backend via documented HTTP API, JSON where appropriate.
- **SI-02:** Validate mandatory fields, formats, ownership and business rules before mutation.
- **SI-03:** Verify authorization on the **backend** for all protected operations.
- **SI-04:** Structured API outcomes/errors are translated to understandable UI messages.
- **SI-05:** Browser never talks to MySQL; backend alone manages persistent data.
- **SI-06:** Preserve owner↔pet↔clinical/vaccination/appointment relational integrity.
- **SI-07:** Keep modification, **soft deletion**, archive, restore and deceased-state transitions distinct.
- **Section 5.2.6:** No initial-release integration with external veterinary systems, third-party booking, Google Calendar, email/SMS, payment or AI services.
- **Section 5.2.7:** OpenAPI is **the frontend/backend contract** and must be reviewed after approved requirements change; the documents cannot be signed off while incompatible.

## 5. Approval / acceptance checklist

- [x] SRS changes and technical-interface supplement translated into an API impact plan.
- [x] API-owned file `api/openapi.yaml` left untouched and still explicitly identified as v0.6.3.
- [x] Separately model hard overlaps vs vet-confirmed hours warning and soft-delete/restore.
- [x] FR-08 Must Have requirements and AC-01–08 received; GET/read ordering already represented in v0.6.3.
- [ ] PO inserts FR-08 as §3.8 in canonical 69-page SRS and removes duplicated §3.7; confirms remaining status/calendar policy details.
- [ ] API owner supplies a versioned, linted OpenAPI spec with response schemas and explicit warning and soft-delete/restore behavior.
- [ ] Backend/security reviews roles, concurrency, secret handling, database integrity and protected photo access.
- [ ] QA converts these into API tests and executes them; no tests are claimed to have run.

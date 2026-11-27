# Pawthy — Verification & Test Scenarios (Sprint 2)

**Functional baseline:** SRS `seng321.PDF` (US-01–US-14), FR-08 screenshots; API path/status reference: **draft** OpenAPI v0.6.3.  
**Contract reconciliation:** Efe’s latest OpenAPI not yet provided; API paths, payload flags and exact error codes marked for verification are not binding.  
**Execution status:** ALL TESTS **NOT RUN**. These are planned scenarios, not executed results.

## Preconditions and conventions

- Base API prefix proposed by contract: `/api/v1` (verify with backend team).
- Test with separate synthetic fixture accounts for `VETERINARIAN`, owner A and owner B, at least one pet per owner, appointments, working hours and diary data.
- Use bearer tokens for authenticated owner/vet interfaces. `GET /availability/calendar` was public in v0.6.3; whether that remains public requires checking the current OpenAPI. Public API access does **not** imply an unauthenticated Pet Owner dashboard.
- Exact status codes/response schemas must be verified against the **latest approved** API before automation. Check documented codes explicitly. An unauthorized role generally returns **403**, an inaccessible other-owner private resource **404**, and a missing/invalid token **401**.
- Where the contract describes a behavioral rejection without clearly selecting an error code, do **not** invent a binding exact code; confirm expected code with the backend team before automation.
- Tests requiring deployment, concurrent clients, a UI or fault injection may initially be **Blocked** instead of **Fail**.

## Scenarios

### TS-01 — NFR-01

- **Endpoints/component:** `GET /pets/{petId}; GET /appointments/{appointmentId}`
- **Setup and steps:** Create one owner, veterinarian, protected objects. Send no token, invalid token, then valid token.
- **Expected result:** No/invalid bearer token produces 401 on protected endpoints.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-02 — NFR-01

- **Endpoints/component:** `POST /appointments; POST /pet-owners; PATCH /pets/{petId}`
- **Setup and steps:** Sign in as PET_OWNER; attempt all veterinarian-only operations.
- **Expected result:** 403; no writes performed.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-03 — NFR-01

- **Endpoints/component:** `GET /pets/{petId}; GET /pets/{petId}/observations; GET /appointments/{appointmentId}`
- **Setup and steps:** Create two owners with distinct pets/appointments; access B resources as A.
- **Expected result:** 404 for other owner private resources; no private data leak.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-04 — NFR-02

- **Endpoints/component:** `GET /availability/calendar` (public designation in v0.6.3 **to verify**) and Pet Owner authenticated weekly calendar.
- **Setup and steps:** Populate real-looking synthetic appointments and unavailable blocks; inspect API payload recursively (without token only if API contract allows); log in as owner and inspect week navigation, clinic contact and available slot list.
- **Expected result:** No private owner/pet/visit/clinical fields in public response; owner sees **only available intervals**, not Busy/Closed/Occupied or other owners’ appointment details. No booking/edit action.
- **Basis:** SRS US-11/US-03; API exposure requires new contract check.
- **Execution:** Not Run.

### TS-05 — NFR-03

- **Endpoints/component:** `POST /appointments`
- **Setup and steps:** As veterinarian create 9-minute appointment, then a valid 10-minute appointment with one pet. Verify the correct SRS scheduled status and the latest API response.
- **Expected result:** 9-minute duration rejected; valid 10-minute appointment created in the **Scheduled** state defined by SRS. Exact HTTP status and API status enum require latest contract verification.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-06 — NFR-03

- **Endpoints/component:** `POST /appointments`
- **Setup and steps:** Seed an active scheduled appointment; create an overlapping slot; run two concurrent writes for the same free time.
- **Expected result:** Overlapping active appointments rejected; at most one active appointment occupies the interval. Confirm HTTP 409 or replacement documented code with the latest OpenAPI.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-07 — NFR-03

- **Endpoints/component:** `POST /appointments; PATCH /appointments/{appointmentId}` plus veterinarian scheduling UI. **[Verify override confirmation field/status in latest OpenAPI.]**
- **Setup and steps:** (1) Vet selects a time outside working hours and a configured unavailable period. (2) Confirm warning **Cancel** -> inspect appointments. (3) Repeat and explicitly **Confirm** -> inspect stored appointment and availability. (4) Repeat with an active appointment already overlapping. (5) Reschedule with both confirm/cancel variants.
- **Expected result:** A warning appears for out-of-hours/unavailable interval; cancel does **not** save; explicit veterinarian confirmation **saves** non-conflicting appointment; overlapping active appointment **never** saves even with confirmation; regular working hours remain unchanged.
- **Basis:** SRS FR-12.12–12.14, BR-12.04–12.05, AC-12.06; v0.6.3 rejection assumptions superseded.
- **Execution:** Not Run.

### TS-08 — NFR-03

- **Endpoints/component:** `PATCH /appointments/{appointmentId}; GET /appointments; GET /availability/calendar`
- **Setup and steps:** Cancel a scheduled active appointment; query cancelled status and same interval in availability calendar.
- **Expected result:** Cancellation retained in history; it no longer occupies calendar time.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-09 — NFR-04 / NFR-02

- **Endpoints/component:** `PUT /veterinarian/schedule; POST /availability/blocks; GET /availability/calendar` (paths pending latest API) + two role-specific UIs.
- **Setup and steps:** Seed weekly working hours, an additional-availability exception, an unavailable exception and an active appointment. Compare veterinarian day/week statuses with owner weekly availability. Update an exception and check existing appointment remains unchanged.
- **Expected result:** Vet sees Available / Unavailable / Occupied distinctly; owner sees **only** intervals that remain available after exceptions and appointments; no unavailable/occupied interval shown as available; pre-existing appointment retained.
- **Basis:** SRS US-11.
- **Execution:** Not Run.

### TS-10 — NFR-04

- **Endpoints/component:** `POST /availability/blocks; PATCH /availability/blocks/{blockId}`
- **Setup and steps:** Create overlapping explicit blocks; try end before start.
- **Expected result:** Overlapping blocks 409; invalid ranges 422.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-11a — NFR-05

- **Endpoints/component:** `POST /pets/{petId}/observations; PATCH /pets/{petId}/observations/{observationId}; DELETE /pets/{petId}/observations/{observationId}` [teyit: path parametreleri]
- **Setup and steps:** (1) As owner A, create, edit and delete an observation on own pet; after each step verify with GET. (2) As veterinarian, attempt POST, PATCH and DELETE on the same pet's observations; afterwards verify with GET that nothing changed. (3) After owner DELETE, verify GET of the deleted observation and `/observation-statistics` no longer include it.
- **Expected result:** Owner writes succeed with documented 201/200/204; veterinarian writes rejected 403 with no data change; deleted observation returns 404 and is excluded from timeline and statistics.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-11b — NFR-05

- **Endpoints/component:** `POST/PATCH/DELETE /pets/{petId}/observations[/{observationId}]` (exact routes pending new API)
- **Setup and steps:** (1) Send writes with no/invalid token. (2) As owner B, attempt to modify owner A’s pet observations; re-read as A for changes. (3) As owner A, submit invalid/future dates or invalid values. (4) Create **two legitimate observations on the same calendar day** for A’s pet, with different entry timestamps and potentially the same symptom; GET history and statistics.
- **Expected result:** Unauthorized operations blocked without changes; invalid data rejected with documented code/body; **both same-day observations are accepted and independently listed**. The same symptom counted at most once that day in Symptom Frequency (see TS-26b); no duplicate-date rejection.
- **Basis:** SRS US-05 FR-05.3 and US-06 FR-06.5; precise API codes pending new contract.
- **Execution:** Not Run.

### TS-12 — NFR-05

- **Endpoints/component:** `GET /pets/{petId}/observations; GET /pets/{petId}/observation-statistics` (API naming to verify)
- **Setup and steps:** Seed an unrecorded day, an explicitly "Normal / No Concerns" day, and a symptom-report day. Inspect calendar counts, default Last 30 Days and explicit Last 90 Days/All Time filters.
- **Expected result:** Empty days not labeled healthy; normal entries explicitly differentiated, excluded from symptom counts; timeline remains per entry and calendar counts all recorded entries.
- **Basis:** SRS US-05/US-06.
- **Execution:** Not Run.

### TS-13a — NFR-06 (clinical records)

- **Endpoints/component:** Clinical record create/edit/delete, owner history and optional restore operations (**exact API paths to verify**).
- **Setup and steps:** (1) As veterinarian create/edit a clinical record for a registered pet; as owner view the record, including only populated fields. (2) As owner attempt create/edit/delete and verify no change. (3) As veterinarian request clinical deletion, cancel once then confirm; inspect owner’s history. (4) If soft-delete restore is exposed in latest API, restore and check owner visibility. (5) Confirm unrelated health diary and vaccination records remain. (6) Test an owner with no active clinical records.
- **Expected result:** Only veterinarian creates/edits/deletes clinical records; confirmed delete hides record from owner and cancellation keeps it; restored records may reappear as allowed by approved spec. Owner sees only associated pet records, no editing, and an informative empty-state message when none exists; unrelated records stay intact. Soft-delete/restore implementation detail must match the current contract.
- **Basis:** SRS US-07 and supplied FR-08 read-only / soft-delete / empty-state screenshots.
- **Execution:** Not Run.

### TS-13b — NFR-06 (vaccination records)

- **Endpoints/component:** Vaccination create/edit/read and attempted DELETE on record (**verify current API paths; delete may have no route**).
- **Setup and steps:** (1) As veterinarian create and update a vaccination record. (2) As owner view own vaccination history, attempt create/edit and (if exposed) DELETE. (3) As veterinarian attempt DELETE (or verify the API/UI exposes **no** delete operation). (4) Confirm both roles’ prohibited attempts leave existing records unchanged.
- **Expected result:** Veterinarian may create/update but **cannot delete vaccination records**; owner has read-only access. No successful deletion from either role and vaccination history remains available. The absence of a DELETE endpoint is an acceptable enforcement mechanism.
- **Basis:** SRS US-09 FR-09.14–09.17 / BR-09.01.
- **Execution:** Not Run.

### TS-14a — NFR-06

- **Endpoints/component:** `POST /pets/{petId}/vaccinations; GET /vaccination-reminders`
- **Setup and steps:** As veterinarian, create a vaccination with a `next_due_date` **7 days away** for an authorized pet; query reminders as the pet's owner, then repeat with a due date more than 7 days away.
- **Expected result:** Vaccination creation succeeds. At 7 days prior, an Upcoming reminder is shown to owner; more than 7 days prior, it is **not shown** in the owner active list. Verify exact API code/enum in current contract.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-14b — NFR-06

- **Endpoints/component:** `POST /pets/{petId}/vaccinations; PATCH /pets/{petId}/vaccinations/{vaccinationId}; GET /vaccination-reminders` (API/link field pending).
- **Setup and steps:** Seed previous vaccination with active reminder; mark that reminder Read. Vet creates a subsequent vaccination explicitly linked to prior one, with a future next-due date; inspect old/new reminder records and read/completion states. Attempt a second new vaccination with same name/date **without** a linkage.
- **Expected result:** Explicit linking completes prior reminder even if it was unread/read; new record’s next-due date generates a **separate** reminder; matching name or date alone does **not** complete prior reminder. Read and Completed remain independent. Verify status enum and link field against new API.
- **Basis:** SRS US-10 BR-10.03–10.05 and AC-10.05–10.07.
- **Execution:** Not Run.

### TS-14c — NFR-06

- **Endpoints/component:** `PATCH /pets/{petId}/vaccinations/{vaccinationId}; GET /vaccination-reminders`
- **Setup and steps:** (1) PATCH other fields without sending `next_due_date`; verify the date and reminder are unchanged. (2) PATCH `next_due_date: null`; verify the response, the vaccination record and reminders.
- **Expected result:** Omitted field leaves `next_due_date` unchanged. Explicit `null` clears it, removes the active reminder, and the vaccination record is not deleted (still returned by GET).
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-14d — NFR-06

- **Endpoints/component:** `PATCH /pets/{petId}/vaccinations/{vaccinationId}; GET /vaccination-reminders`
- **Setup and steps:** (1) As PET_OWNER, attempt PATCH on a vaccination of own pet. (2) As owner B, attempt PATCH and read A's pet vaccinations. (3) As owner A and B, query reminders, each with seeded vaccinations. (4) Query reminders with no token.
- **Expected result:** Owner PATCH 403 with no change; other-owner private resource 404; each owner's reminder list contains only their own pets; no token 401.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-15 — NFR-07

- **Endpoints/component:** `POST /auth/change-password; GET /pets`
- **Setup and steps:** Use first-login temporary credentials; change password and reuse old token/password.
- **Expected result:** Change 204; previously issued token and old temporary password rejected.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-16 — NFR-07

- **Endpoints/component:** `POST /auth/logout; GET /pets; POST /auth/login`
- **Setup and steps:** Authenticate, log out and reuse token; separately simulate login rate limiting if supported by test environment.
- **Expected result:** Logout 204; token invalidated; rate-limited login 429 when limit triggered.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-17 — NFR-08

- **Endpoints/component:** Representative read/write endpoints including appointments, diary, pet profiles, vaccinations and availability; **latest OpenAPI** required.
- **Setup and steps:** Obtain Efe’s current OpenAPI; diff it against v0.6.3 and SRS requirements for out-of-hours overrides, same-day diary entries, multi-pet appointments, reminder read/completion, and public API versus authenticated owner UI. Validate schema, pagination, response media types, security roles and documented failure responses.
- **Expected result:** No unreviewed SRS/API contradiction; 100% selected requests conform to approved status codes, schema and authentication rules. Exact failure status/payload **not assumed** before reconciliation.
- **Basis:** API compatibility review and SRS US-04–US-12.
- **Execution:** Not Run.

### TS-18 — NFR-09

- **Endpoints/component:** `POST /appointments; GET /availability/calendar`
- **Setup and steps:** Send offset timestamps including day boundary; inspect storage/retrieval and range filtering.
- **Expected result:** ISO 8601 offsets maintained and correct Europe/Istanbul scheduling/filter logic.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-19 — NFR-10

- **Endpoints/component:** `GET /pets; GET /appointments; GET /availability/calendar`
- **Setup and steps:** With seeded isolated database, warm up, then run 20 virtual users for 5 minutes against valid read flows.
- **Expected result:** **Proposed / approval pending:** 20 virtual users for 5 minutes; read p95 ≤2s, unintended errors <1%.
- **Basis:** Proposed quality measure; Product Owner/technical approval pending.
- **Execution:** Not Run.

### TS-20 — NFR-11

- **Endpoints/component:** `POST /appointments; POST /pets/{petId}/observations`
- **Setup and steps:** Generate unique nonoverlapping appointment times and safe owner diary writes; run 10 VUs for 5 minutes.
- **Expected result:** **Proposed / approval pending:** 10 virtual users for 5 minutes; valid write p95 ≤3s, unintended errors <1%.
- **Basis:** Proposed quality measure; Product Owner/technical approval pending.
- **Execution:** Not Run.

### TS-21 — NFR-12

- **Endpoints/component:** `Deployed health/API endpoint (chosen during execution)`
- **Setup and steps:** Select a reachable deployed monitoring endpoint, schedule checks across 7 days, and record success/failure and uptime ratio.
- **Expected result:** **Proposed / approval pending:** 7-day reachability ≥99%, maintenance policy agreed.
- **Basis:** Proposed quality measure; Product Owner/technical approval pending.
- **Execution:** Not Run.

### TS-22 — NFR-13

- **Endpoints/component:** `POST /appointments; POST /pets/{petId}/vaccinations`
- **Setup and steps:** In isolated environment, inject failure during persistence in 20 controlled repetitions and inspect DB state.
- **Expected result:** **Proposed / approval pending:** 0 inconsistent partially persisted states in 20 controlled fault-injection trials.
- **Basis:** Proposed quality measure; Product Owner/technical approval pending.
- **Execution:** Not Run.

### TS-23 — NFR-14 / NFR-02

- **Endpoints/component:** Veterinarian day/week calendar and authenticated owner weekly calendar (API backing to verify).
- **Setup and steps:** Invite five participants to complete scripted role-appropriate tasks: vets identify Available/Unavailable/Occupied intervals and set/inspect exceptions; owners find **only available** consultation intervals, switch weeks, find clinic phone and confirm no online booking. Include an occupied/unavailable hidden fixture and check privacy.
- **Expected result:** **Proposed / approval pending:** ≥4/5 participants succeed without assistance; owners never see busy/closed appointment details or misleading available slots.
- **Basis:** SRS US-11 and product clarification; proposed usability target.
- **Execution:** Not Run.

### TS-24 — NFR-15

- **Endpoints/component:** `POST /pets/{petId}/photos; PUT /pets/{petId}/clinical-photo` (paths pending API reconciliation)
- **Setup and steps:** Attempt unauthorized uploads; with authorized roles try valid JPEG/PNG ≤5 MiB, malformed/nonimage, unsupported type and >5 MiB. Record actual contract behavior and version.
- **Expected result:** Unauthorized uploads denied. **Proposed / approval pending:** JPEG/PNG up to 5 MiB accepted; invalid/oversized types rejected. Do not treat the proposed numeric policy as already implemented.
- **Basis:** SRS role restrictions; proposed upload quality target.
- **Execution:** Not Run.

### TS-25a — NFR-08 / US-04

- **Endpoints/component:** Pet create/edit form/API (**verify current path**).
- **Setup and steps:** Register a pet with mandatory date marked Exact, then Estimated. Observe displayed age label; edit recorded birth date / accuracy classification.
- **Expected result:** Complete date + Exact/Estimated classification required; Estimated-derived age clearly approximate; updates preserved without confusing estimated with confirmed dates.
- **Basis:** SRS US-04 FR-04.18–04.22, AC-04.04.
- **Execution:** Not Run.

### TS-25b — NFR-08 / US-04

- **Endpoints/component:** Pet create/edit form/API.
- **Setup and steps:** Create pets with microchip status Yes and missing number, No/None, and Yes with number. Try life-stage update without changing sex/birth date; change a juvenile with sex Unknown to later life-stage.
- **Expected result:** Microchip status is mandatory; Yes requires number, No maps to None. Life-stage update stays independent of date/sex and prompts reassessment of Unknown sex without automatically assigning one.
- **Basis:** SRS US-04 FR-04.09–04.12, AC-04.03/04.05.
- **Execution:** Not Run.

### TS-26a — NFR-05 / US-05

- **Endpoints/component:** Diary observation create/history (latest API path to verify).
- **Setup and steps:** As owner create multiple observations for same pet on same date, each with different symptoms/times; as veterinarian view; as owner edit and delete one.
- **Expected result:** Multiple same-day entries saved separately; vet can read but not edit/delete; changes shown in history; future observation date rejected.
- **Basis:** SRS US-05 FR-05.3, FR-05.7/05.8, FR-05.12.
- **Execution:** Not Run.

### TS-26b — NFR-05 / US-06

- **Endpoints/component:** Diary calendar, timeline and symptom-frequency chart/API.
- **Setup and steps:** Record the **same symptom twice** on date D in different observations and once on D+1; record a different symptom once on D, plus a "Normal / No Concerns" entry separately. Inspect last-30/last-90/all-time filters; edit/delete entries and recalculate.
- **Expected result:** Same symptom frequency = **2 distinct days**, not 3 entries; timeline retains separate entries; Normal excluded; deleting/editing recomputes counts.
- **Basis:** SRS US-06 FR-06.4–06.12.
- **Execution:** Not Run.

### TS-27a — NFR-06 / US-10

- **Endpoints/component:** Owner reminder UI and reminder status update (API endpoint to verify).
- **Setup and steps:** Create upcoming/due reminders, mark one Read, reload and inspect owner active list/status and associated vaccination record.
- **Expected result:** Read changes only read state; vaccination is **not Completed** and active reminder remains eligible for display according to its due window.
- **Basis:** SRS US-10 FR-10.10–10.11, AC-10.05.
- **Execution:** Not Run.

### TS-27b — NFR-06 / US-10

- **Endpoints/component:** Owner and vet reminder dashboards, pet archive/deceased controls (paths to verify).
- **Setup and steps:** Archive pet with active reminder, inspect owner and vet views; unarchive if supported; mark pet deceased and recheck both, plus historical vaccination list.
- **Expected result:** Archived reminders hidden from owner active dashboard but remain in vet monitoring; deceased active/future reminders suppressed for both roles; vaccination history retained.
- **Basis:** SRS US-10 FR-10.16–10.17, AC-10.09–10.10.
- **Execution:** Not Run.

### TS-27c — NFR-06 / US-10

- **Endpoints/component:** Vaccination creation/update and reminders.
- **Setup and steps:** Save vaccination without next due date; then set it and later change date; create explicit next vaccination linked to previous one; attempt completion without link.
- **Expected result:** No reminder without due date; updates do not duplicate active reminders; **only explicit link** completes earlier reminder; next vaccination with due date gets its own reminder.
- **Basis:** SRS US-10 AC-10.01–10.02/10.06–10.07/10.11.
- **Execution:** Not Run.

### TS-28 — NFR-03 / US-12

- **Endpoints/component:** Veterinarian appointment create/edit, owner appointment view and calendar (**payload/path to verify**).
- **Setup and steps:** Vet creates one appointment containing two registered pets of the **same owner**. Try mixing pets of two owners. Try 9-minute and valid 10-minute durations; verify owner only sees own appointments. Confirm owner cannot book online.
- **Expected result:** Two same-owner pets accepted in one appointment; mixed owners rejected; duration <10 min rejected; owner sees own appointments read-only; no direct owner booking. No other owner details disclosed.
- **Basis:** SRS US-12 AC-12.02–12.04, AC-12.13–12.15.
- **Execution:** Not Run.

### TS-29 — US-14 (P2; deferred execution)

- **Endpoints/component:** Owner guided tour and persisted user preference (UI plus API/storage, to verify).
- **Setup and steps:** Complete tour on device A, log in on B and verify it does not auto-open again; separately Skip tour and verify cross-device persistence; optionally Replay via Help without resetting persisted status.
- **Expected result:** Completed/Skipped persisted **per account across devices**; tour is not automatically replayed; user can explicitly replay where implemented.
- **Basis:** SRS US-14 FR-14.10–14.14, AC-14.07–14.09. **P2: execution may remain Not Run until implemented**.
- **Execution:** Not Run.

### TS-30 — NFR-14 / NFR-16

- **Endpoints/component:** Owner and vet web views, core calendars, accessible forms.
- **Setup and steps:** Using approved browser/viewport matrix (to agree), inspect desktop/mobile widths, keyboard-only navigation, visible focus, field labels, zoom/clipping and automated accessibility scan.
- **Expected result:** **Proposed, approval pending:** no critical clipping/blocked flow; keyboard usable; labels and visible focus; no critical automated accessibility findings.
- **Basis:** Additional UI quality proposal; browser matrix/accessibility level to be approved.
- **Execution:** Not Run.

### TS-31 — NFR-07

- **Endpoints/component:** Login/reset/password storage and deployed HTTP/TLS configuration.
- **Setup and steps:** Check password database fields and logs (never extract real passwords), verify salted secure hashes; attempt protected HTTP access/redirect; exercise first-login/change/reset and previous credential/session invalidation.
- **Expected result:** No plaintext credential storage/logging, protected traffic over HTTPS, prior credentials/sessions rejected after reset/change as specified.
- **Basis:** SRS US-02 FR-02.8 and reset requirements; deployment verification.
- **Execution:** Not Run.

### TS-32 — NFR-02 / NFR-08

- **Endpoints/component:** Public `GET /availability/calendar` (v0.6.3 contract) versus owner dashboard (authenticated SRS US-11), latest API routes/security declaration.
- **Setup and steps:** Review new OpenAPI security requirements; call public API without auth **only if still declared public**, inspect data minimization; log into owner UI and inspect weekly available-only calendar and clinic number; call protected owner endpoints unauthenticated.
- **Expected result:** Public availability endpoint, if retained, has privacy-safe slot data only. Authenticated owner UI shows available consultation intervals only and grants no scheduling/edit rights. Protected owner endpoints reject unauthenticated access. No assumption that a public API makes the owner dashboard public.
- **Basis:** SRS US-11 FR-11.10–11.14 versus OpenAPI v0.6.3.
- **Execution:** Not Run.

## Suggested execution order

1. Reconcile latest approved OpenAPI with the SRS/FR-08 screenshot first (TS-17, TS-32). Resolve endpoint/path, authorization, payload and error-status conflicts before automation.
2. Security/permissions: TS-01–TS-04, TS-11a/11b, TS-13a/13b, TS-14a–14d, TS-15–TS-18, TS-31.
3. Functional regression: TS-05–TS-10, TS-12, TS-25a/b, TS-26a/b, TS-27a/b/c, TS-28, TS-32.
4. UI and proposal reviews: TS-23, TS-30; performance/reliability/file-policy proposals TS-19–TS-22, TS-24 only when approved and environments are available.
5. P2 tour TS-29 may remain **Not Run** until implementation is ready.

## Test-result template

| Test ID | Run date | Build/environment | Actual result | Pass / Fail / Blocked / Not Run | Evidence |
|---|---|---|---|---|---|
| TS-01 | — | — | — | Not Run | — |

## Execution prerequisites and open decisions

- Obtain Efe’s latest OpenAPI to reconcile endpoint names, request/response schema, auth, status codes, override confirmation, multi-pet payload, clinical soft delete/restore, reminder transitions and any availability API exposure.
- Review quantitative p95, uptime, image-size, user-study and browser/accessibility targets with technical team and Product Owner; proposals are **not approved baselines**.
- Provide synthetic data, isolated test database, safe concurrency/fault-injection environment, browser UI, and any deployed health/monitoring endpoint.
- **Nothing has been executed. All test cases remain Not Run**, including P2 US-14.

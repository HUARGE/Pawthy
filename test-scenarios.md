# Pawthy — Verification & Test Scenarios (Sprint 2)

**Contract baseline:** OpenAPI v0.6.3 (draft, not deployed implementation).  
**Execution status:** ALL TESTS **NOT RUN**. These are planned scenarios, not recorded results.

## Preconditions and conventions

- Base API prefix proposed by contract: `/api/v1` (verify with backend team).
- Test with separate synthetic fixture accounts for `VETERINARIAN`, owner A and owner B, at least one pet per owner, appointments, working hours and diary data.
- Reuse bearer tokens only where operation requires authentication; public endpoints declare `security: []`.
- Check documented codes explicitly. An unauthorized role generally returns **403**, an inaccessible other-owner private resource **404**, and a missing/invalid token **401**.
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

- **Endpoints/component:** `GET /availability/calendar`
- **Setup and steps:** As unauthenticated user, request populated day and week (appointments and busy blocks seeded); inspect response recursively.
- **Expected result:** 200; only time/status information; no owner, pet or visit details.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-05 — NFR-03

- **Endpoints/component:** `POST /appointments`
- **Setup and steps:** As veterinarian create 9-minute appointment, then valid 10-minute appointment with one pet.
- **Expected result:** Invalid duration 422; valid appointment 201 CONFIRMED.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-06 — NFR-03

- **Endpoints/component:** `POST /appointments`
- **Setup and steps:** Seed a confirmed slot; create overlapping slot; run two concurrent writes for same free time.
- **Expected result:** Conflicts 409; at most one active appointment for same interval.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-07 — NFR-03

- **Endpoints/component:** `POST /appointments; PATCH /appointments/{appointmentId}`
- **Setup and steps:** Attempt creation and reschedule outside working hours or during blocked time.
- **Expected result:** Violations rejected according to documented validation/conflict behavior; never persisted as confirmed.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-08 — NFR-03

- **Endpoints/component:** `PATCH /appointments/{appointmentId}; GET /appointments; GET /availability/calendar`
- **Setup and steps:** Cancel a confirmed appointment; query cancelled status and same interval in public calendar.
- **Expected result:** Cancellation retained in history; it no longer occupies calendar time.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-09 — NFR-04

- **Endpoints/component:** `PUT /veterinarian/schedule; POST /availability/blocks; GET /availability/calendar`
- **Setup and steps:** Seed weekly hours, AVAILABLE and BUSY blocks and a confirmed appointment. Compare expected day/week intervals.
- **Expected result:** BUSY precedence; appointments remain BUSY and closed times correctly represented.
- **Basis:** Contract.
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

- **Endpoints/component:** `POST/PATCH/DELETE /pets/{petId}/observations[/{observationId}]`
- **Setup and steps:** (1) Send each write with no token and with an invalid token. (2) As owner B, attempt POST, PATCH and DELETE on owner A's pet and observation; verify with owner A that data is unchanged. (3) As owner A, submit invalid observations (invalid or future date, missing required fields, out-of-range values). (4) If the contract restricts one observation per day, create a second one for the same date.
- **Expected result:** Missing/invalid token 401; other-owner private resource 404 with no write and no data leak; invalid payload 400/422 per documented validation behavior, with `application/problem+json`. The same-day duplicate case is rejected, but the exact code is **not binding**: [teyit: backend] before automation.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-12 — NFR-05

- **Endpoints/component:** `GET /pets/{petId}/observations; GET /pets/{petId}/observation-statistics`
- **Setup and steps:** Seed observation on one day and leave another unrecorded; check counts and date limits.
- **Expected result:** Counts/timeline reflect only recorded entries; unrecorded day not treated as healthy.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-13 — NFR-06

- **Endpoints/component:** `POST /pets/{petId}/medical-records; POST /pets/{petId}/vaccinations`
- **Setup and steps:** Attempt write as owner and veterinarian for authorized pet.
- **Expected result:** Owner 403; veterinarian creates records with 201.
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-14a — NFR-06

- **Endpoints/component:** `POST /pets/{petId}/vaccinations; GET /vaccination-reminders`
- **Setup and steps:** As veterinarian, create a vaccination with a future `next_due_date` for an authorized pet; query reminders as the pet's owner.
- **Expected result:** Vaccination created with 201; an active reminder for that due date appears with the initial status defined in the contract [teyit: status enum].
- **Basis:** Contract.
- **Execution:** Not Run.

### TS-14b — NFR-06

- **Endpoints/component:** `POST /pets/{petId}/vaccinations; PATCH /pets/{petId}/vaccinations/{vaccinationId}; GET /vaccination-reminders`
- **Setup and steps:** Seed a vaccination with a due date (use a past due date to also cover the overdue status; fix or control the test clock). Create the next vaccination and link it to the earlier one using the contract's linking field [teyit: field name]. Query reminders before and after linking.
- **Expected result:** After linking, the earlier reminder changes status according to the contract (e.g. completed/closed) and the new vaccination's reminder appears. Expected status values are listed explicitly from the contract. A next vaccination dated before the application date is rejected with 422 [teyit: backend].
- **Basis:** Contract.
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

- **Endpoints/component:** `GET /pets; GET /pet-owners; invalid POST /appointments`
- **Setup and steps:** Inspect pagination items/next_cursor, limit/cursor handling, schema and validation error media type.
- **Expected result:** Matches documented schemas; validation errors use application/problem+json; appropriate 400/422.
- **Basis:** Contract.
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
- **Expected result:** Sprint 2 acceptance target: p95 <=2s; unintended errors <1%.
- **Basis:** Sprint 2 baseline.
- **Execution:** Not Run.

### TS-20 — NFR-11

- **Endpoints/component:** `POST /appointments; POST /pets/{petId}/observations`
- **Setup and steps:** Generate unique nonoverlapping appointment times and safe owner diary writes; run 10 VUs for 5 minutes.
- **Expected result:** Sprint 2 acceptance target: p95 <=3s; unintended errors <1%.
- **Basis:** Sprint 2 baseline.
- **Execution:** Not Run.

### TS-21 — NFR-12

- **Endpoints/component:** `Deployed health/API endpoint (chosen during execution)`
- **Setup and steps:** Select a reachable deployed monitoring endpoint, schedule checks across 7 days, and record success/failure and uptime ratio.
- **Expected result:** Sprint 2 acceptance target: availability >=99%, using an explicitly recorded maintenance/exclusion policy.
- **Basis:** Sprint 2 baseline.
- **Execution:** Not Run.

### TS-22 — NFR-13

- **Endpoints/component:** `POST /appointments; POST /pets/{petId}/vaccinations`
- **Setup and steps:** In isolated environment, inject failure during persistence in 20 controlled repetitions and inspect DB state.
- **Expected result:** Sprint 2 acceptance target: 0 partially persisted inconsistent states.
- **Basis:** Sprint 2 baseline.
- **Execution:** Not Run.

### TS-23 — NFR-14

- **Endpoints/component:** `Day/week calendar UI backed by /availability/calendar`
- **Setup and steps:** Ask five participants to distinguish available, busy, closed intervals using seeded fixture.
- **Expected result:** Sprint 2 acceptance target: >=4 of 5 succeed independently.
- **Basis:** Sprint 2 baseline.
- **Execution:** Not Run.

### TS-24 — NFR-15

- **Endpoints/component:** `POST /pets/{petId}/photos; PUT /pets/{petId}/clinical-photo`
- **Setup and steps:** Try disallowed role, valid JPEG/PNG <=5 MiB, malformed/nonimage inputs and files >5 MiB; verify authorization and validation behavior.
- **Expected result:** Unauthorized uploads rejected; valid JPEG/PNG images of at most 5 MiB accepted; unsupported, malformed and oversized files rejected under the Sprint 2 upload policy.
- **Basis:** Sprint 2 baseline.
- **Execution:** Not Run.

## Suggested execution order

1. Identify the running deployment and test environment; use the fixed Sprint 2 acceptance thresholds defined in `quality-measures.md`.
2. Contract and permission checks: TS-01–TS-04, TS-11a–TS-11b, TS-13, TS-15–TS-18.
3. Appointment and calendar behavior: TS-05–TS-10, TS-12, TS-14a–TS-14d.
4. Optional empirical quality checks after deployment: TS-19–TS-24.

## Test-result template

| Test ID | Run date | Build / environment | Actual result | Pass / Fail / Blocked / Not Run | Evidence |
|---|---|---|---|---|---|
| TS-01 | — | — | — | Not Run | — |

## Execution prerequisites (not outstanding acceptance-target approvals)

- Identify the backend runtime, database and test deployment URL.
- Provide disposable test data and a safe environment for parallel writes and fault injection.
- Identify the endpoint suitable for 7-day reachability monitoring.
- Provide the frontend day/week calendar for the usability test.
- Implement and run the scenarios when executable testing is in scope; until then all results remain **Not Run**.

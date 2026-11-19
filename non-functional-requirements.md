# Pawthy — Non-Functional Requirements (Sprint 2)

**Baseline:** `Pawthy_OpenAPI_v0.6.3`; OpenAPI 3.1.0; draft API-first contract.  
**Status:** Sprint 2 acceptance baseline defined by the requirements owner; **not evidence of implementation or passed tests**.  
**Scope:** Single clinic; veterinarian/administrator and pet owners; backend `/api/v1` (proposed Django prefix).

## Scope and interpretation

Pawthy supports veterinarian-managed appointments (not online owner booking), read-only privacy-safe public availability, owner-managed daily health diary, veterinary medical records, vaccination reminders, and a manual light/dark preference. No billing, external SMS/email, diagnosis, external calendar sync or automated appointment slots are in scope.

- **Contract rule:** behavior already specified in the provided OpenAPI; no new numeric promise is implied.
- **Sprint 2 baseline:** a quantitative acceptance target set for this project by the requirements owner. These additional targets are not claims about the existing OpenAPI contract or implemented behavior.
- **Verification:** see `test-scenarios.md`. A test is **not executed** merely because it is documented.

## Requirements register

### NFR-01 — Role-based access control

- **Quality category:** Security
- **Requirement:** All protected operations enforce `x-roles`; missing/invalid bearer token yields 401 and disallowed role yields 403. Other owners’ private resources yield 404.
- **Basis:** Contract rule.
- **Acceptance criterion:** 100% of tested authorization matrix cases match documented expected status.
- **Verification:** TS-01, TS-02, TS-03.

### NFR-02 — Public availability data minimization

- **Quality category:** Privacy
- **Requirement:** `GET /availability/calendar` must not expose owner identities, pet identities, clinical notes or appointment visit details.
- **Basis:** Contract rule.
- **Acceptance criterion:** 0 private fields or identifiable private values in public calendar payloads.
- **Verification:** TS-04.

### NFR-03 — Appointment scheduling integrity

- **Quality category:** Integrity
- **Requirement:** Only veterinarian can create/reschedule; duration >=10 minutes; working hours and blocked periods respected; conflicting confirmed appointments rejected with 409; cancelled appointments retained but free time.
- **Basis:** Contract rule.
- **Acceptance criterion:** 100% of scheduling edge cases comply; 0 confirmed overlaps after concurrent-create test (concurrency handling to be verified).
- **Verification:** TS-05, TS-06, TS-07, TS-08.

### NFR-04 — Calendar precedence and consistency

- **Quality category:** Integrity
- **Requirement:** Public and veterinarian calendars reconcile recurring weekly hours, explicit AVAILABLE/BUSY blocks, exceptional blocked periods and non-cancelled appointments. Busy overrides available; conflicting explicit blocks rejected.
- **Basis:** Contract rule.
- **Acceptance criterion:** 0 mismatches in the agreed calendar fixture and day/week checks.
- **Verification:** TS-09, TS-10.

### NFR-05 — Diary ownership and meaning

- **Quality category:** Integrity
- **Requirement:** Owner may create/edit/delete their own observations, veterinarian read-only. Empty/unrecorded days must not be labelled healthy; symptom counts reflect actual records.
- **Basis:** Contract rule.
- **Acceptance criterion:** 100% of fixture permission and data-interpretation cases pass.
- **Verification:** TS-11, TS-12.

### NFR-06 — Medical records and vaccination permissions

- **Quality category:** Integrity
- **Requirement:** Veterinarian writes clinical records/vaccinations; owners view authorized history only. Vaccination reminder updates follow documented linked-record behavior.
- **Basis:** Contract rule.
- **Acceptance criterion:** 100% of role and reminder transition cases pass.
- **Verification:** TS-13, TS-14.

### NFR-07 — Credential lifecycle

- **Quality category:** Security
- **Requirement:** Password change invalidates previous temporary password and tokens issued before change; logout invalidates current token; login supports documented 429 response.
- **Basis:** Contract rule.
- **Acceptance criterion:** 100% of credential state-transition tests pass.
- **Verification:** TS-15, TS-16.

### NFR-08 — Contract-compliant API responses

- **Quality category:** Interoperability
- **Requirement:** Status codes, JSON schemas, pagination `items/next_cursor`, and `application/problem+json` validation errors follow OpenAPI v0.6.3.
- **Basis:** Contract rule.
- **Acceptance criterion:** 100% of selected contract examples validate against API schema.
- **Verification:** TS-17.

### NFR-09 — Explicit local time offsets

- **Quality category:** Time correctness
- **Requirement:** All appointment/calendar timestamps use ISO 8601 with offsets aligned to Europe/Istanbul and boundary filtering is correct.
- **Basis:** Contract rule.
- **Acceptance criterion:** 100% of timezone and date-boundary cases pass.
- **Verification:** TS-18.

### NFR-10 — Interactive API latency

- **Quality category:** Performance
- **Requirement:** Routine read endpoints respond promptly at agreed test load.
- **Basis:** Sprint 2 baseline (requirements-owner-defined).
- **Acceptance criterion:** p95 <= 2.0 s and unintended error rate < 1% under 20 concurrent virtual users for 5 minutes.
- **Verification:** TS-19.

### NFR-11 — Write transaction latency

- **Quality category:** Performance
- **Requirement:** Appointment creation and diary writes respond promptly at agreed test load.
- **Basis:** Sprint 2 baseline (requirements-owner-defined).
- **Acceptance criterion:** p95 <= 3.0 s and error rate < 1% for valid write requests under 10 concurrent virtual users for 5 minutes.
- **Verification:** TS-20.

### NFR-12 — Availability of deployed service

- **Quality category:** Reliability
- **Requirement:** Deployed API remains reachable during an agreed observation period.
- **Basis:** Sprint 2 baseline (requirements-owner-defined).
- **Acceptance criterion:** >=99% successful scheduled health/API checks across a 7-day observation window (after excluding planned maintenance if agreed).
- **Verification:** TS-21.

### NFR-13 — Atomicity under failures

- **Quality category:** Reliability
- **Requirement:** Partial failed write operations do not leave inconsistent confirmed appointments or incomplete linked data.
- **Basis:** Sprint 2 baseline (requirements-owner-defined).
- **Acceptance criterion:** 0 inconsistent persisted states over 20 injected failure trials.
- **Verification:** TS-22.

### NFR-14 — Day/week calendar clarity

- **Quality category:** Usability
- **Requirement:** The day/week frontend clearly distinguishes available, busy and closed periods, without private data leakage in owner view.
- **Basis:** Sprint 2 baseline (requirements-owner-defined).
- **Acceptance criterion:** At least 4 of 5 test users correctly identify slot status in a scripted task without assistance.
- **Verification:** TS-23.

### NFR-15 — Photo upload validation

- **Quality category:** Security
- **Requirement:** Personal/clinical photo upload endpoints enforce authorization and reject unsupported or malformed uploads under an agreed file policy.
- **Basis:** Sprint 2 baseline (requirements-owner-defined).
- **Acceptance criterion:** 100% unauthorized uploads denied; only JPEG/PNG images up to 5 MiB accepted; malformed, unsupported and oversized uploads rejected (upload policy is a Sprint 2 target, not an existing OpenAPI guarantee).
- **Verification:** TS-24.

## Traceability and exclusions

The requirement register is mapped to relevant endpoint names and user-story IDs in `test-scenarios.md`. Contract rules must be reconfirmed against later API versions. Sprint 2 numeric thresholds are set as acceptance targets; they are not presented as implemented, measured, or proven. This document does not add online booking, booking approval, private appointment information to the public calendar, external notifications, automated diagnosis or calendar synchronization.

## Ownership and verification status

- **Acceptance-target owner:** Sprint 2 requirements and quality documentation owner. The quantitative targets above are set as the project evaluation baseline; no additional teammate approval is needed to define them.
- **Implementation and execution are separate:** The API description is a draft contract. The tests are written but not executed; actual results must be reported only after running them.
- **Execution dependencies:** Deployment, a test database, concurrency tooling, monitoring and UI access may be required to verify the criteria. These dependencies do not make the acceptance thresholds provisional.
- **Change control:** If implementation constraints require changing an acceptance target, update the requirement and its matching quality metric and test scenario in the same commit.

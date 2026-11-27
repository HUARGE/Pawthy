# Pawthy — Quality Measures (Sprint 2)

**Sources:** SRS `seng321.PDF`, supplied FR-08 screenshots, and draft OpenAPI v0.6.3 (current version from Efe pending); companion to `non-functional-requirements.md`.  
**Status:** Functional behaviors aligned to SRS; numeric quality thresholds are **proposals pending technical feasibility review and Product Owner approval**. No live measurements have been collected; all test scenarios are **Not Run**.

## Measurement table

| NFR | Metric | Acceptance threshold | How to measure | Status |
|---|---|---|---|---|
| NFR-01 | Authorization response correctness | 100% selected cases | Role-by-endpoint HTTP status matrix | Contract |
| NFR-02 | Owner calendar privacy and availability-only display | 0 leaked private values; 0 unavailable/occupied intervals listed as available | Inspect public API if applicable, and authenticated owner weekly calendar | SRS / API reconciliation |
| NFR-03 | Overlaps and veterinarian override | 0 active overlaps; warning + explicit confirmation allows out-of-hours without conflict; ≥10 min; same-owner pets only | Concurrent creates, override/cancel, duration and pet ownership tests | SRS (API mapping pending) |
| NFR-04 | Availability mismatches by role | 0 fixture mismatches; owner sees available intervals only | Vet day/week vs owner weekly views and API fixture | SRS (API mapping pending) |
| NFR-05 | Diary role/day-count correctness | 100% fixture cases; multiple same-day entries allowed; a symptom counts once per day | Create duplicate-date entries; compare individual timeline and de-duplicated daily frequency | SRS (API mapping pending) |
| NFR-06 | Record permissions and reminder independence | 100% role/visibility cases; vaccination deletion denied; Read ≠ Completed; archive/deceased behavior | Vet clinical delete + owner read, vaccination DELETE rejection, reminder transitions | SRS / FR-08 screenshot |
| NFR-07 | Secure authentication and transport | 0 plaintext passwords, 0 accepted invalid credentials; HTTPS for protected deployed traffic | Inspect hash storage and TLS, first-login/change/reset/session tests | SRS + API reconciliation |
| NFR-08 | Latest API schema/response conformity | 100% selected cases after latest contract approval | Contract-test status, endpoint, schemas, auth and error bodies vs current OpenAPI | Pending OpenAPI |
| NFR-09 | Time-format and boundary compliance | 100% selected fixtures | Offset parser + timezone conversion and range assertions | Contract |
| NFR-10 | Routine read latency / HTTP errors | p95 <= 2s; unintended error rate <1% | 20 VUs, 5m; measure on representative reads | Proposed — approval pending |
| NFR-11 | Valid write latency / HTTP errors | p95 <= 3s; unintended error rate <1% | 10 VUs, 5m; clean isolated seeded data | Proposed — approval pending |
| NFR-12 | Reachability ratio | >=99% over 7 days | Successful checks / scheduled checks x 100 | Proposed — approval pending |
| NFR-13 | Partial state after injected failure | 0/20 corrupt partial states | Controlled fault injection, verify persistence | Proposed — approval pending |
| NFR-14 | Role-appropriate calendar task success | Proposed ≥4/5 users; owner calendar must not show Busy/Closed | Five scripted usability sessions (vet and owner tasks) | Proposed — approval pending |
| NFR-15 | Upload authorization and validation | Proposed: 100% permission tests; JPEG/PNG only; maximum 5 MiB | File type/size and role-based upload tests | Proposed — approval pending |
| NFR-16 | Responsive and accessible interface | Proposed: no critical UI blocker in agreed browser/viewport matrix; keyboard-operable core calendar flow | Manual browser/viewport/keyboard checks + automated accessibility scan | Proposed — approval pending |

## Measurement protocol

1. Use a separate test database populated with synthetic owners, pets, diary observations, clinical records and calendar intervals. Never use real medical or contact information for load-test fixtures.
2. Capture deployment commit, API version, database, machine resources, test period, number of virtual users and response-time percentiles.
3. For performance tests, warm up before measurement; exclude intentionally invalid requests from the error-rate denominator; report non-2xx outcomes separately, including expected 409 conflicts.
4. **p95**: 95th percentile of request duration in the measured set. **Error rate**: unintended failed requests / all valid measured requests x 100. **Availability**: successful scheduled health checks / scheduled checks x 100. Document planned maintenance handling before monitoring.
5. Report **Pass / Fail / Blocked / Not Run** with evidence, timestamps and reproducible commands. The values for NFR-10–15 and the proposed UI/browser matrix for NFR-16 require Product Owner sign-off and technical feasibility review before being treated as accepted baselines.

## Sprint 2 measurement baseline

| Measurement dimension | Proposed acceptance target — pending approval |
|---|---|
| Read traffic | 20 concurrent virtual users for 5 minutes, after warm-up |
| Write traffic | 10 concurrent virtual users for 5 minutes, using isolated non-conflicting test data |
| Latency and error budgets | Read p95 <=2 s; write p95 <=3 s; unintended request errors <1% |
| API reachability | >=99% successful scheduled checks over 7 days; monitoring endpoint to be chosen at execution |
| Upload validation policy | JPEG/PNG only; maximum 5 MiB per image; unauthorized access denied |
| Atomicity | 0 inconsistent partial writes in 20 controlled fault-injection trials |
| Calendar usability | At least 4/5 participants complete **role-appropriate** calendar tasks; owners only see available intervals |
| UI/browser accessibility | Agreed browser/viewport matrix, core keyboard navigation, no critical accessibility blockers |

These values are **proposals, not approved acceptance criteria**. The team should check technology, hosting, test time and Product Owner priorities before finalizing. The previous OpenAPI does not guarantee a health endpoint or numeric file-size limit. Once a value is approved, update this table, the corresponding NFR and the linked test together.

## API and product approval checkpoints

- **Product Owner / technical team:** review p95 read/write targets, 7-day availability window, usability success threshold, 5 MiB upload policy and NFR-16 browser/accessibility matrix. Mark approval decisions with date/owner; currently **not approved**.
- **Latest OpenAPI from Efe:** compare public `availability` endpoint designation with **authenticated** owner calendar; confirm override confirmation payload, record DELETE support, reminder read/completion schema, pet IDs in appointments, error codes and paths.
- **Evidence rule:** leave every planned test `Not Run`; no fabricated measurements or status updates.

## Evidence template

| Test ID | Environment / commit | Measured value | Result | Evidence link | Reviewer/date |
|---|---|---|---|---|---|
| TS-XX | Not Run | — | Not Run | — | — |

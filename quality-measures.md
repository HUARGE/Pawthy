# Pawthy — Quality Measures (Sprint 2)

**Source:** OpenAPI v0.6.3; companion to `non-functional-requirements.md`.  
**Status:** Sprint 2 acceptance thresholds are defined by the requirements owner. No live measurements have been collected.

## Measurement table

| NFR | Metric | Acceptance threshold | How to measure | Status |
|---|---|---|---|---|
| NFR-01 | Authorization response correctness | 100% selected cases | Role-by-endpoint HTTP status matrix | Contract |
| NFR-02 | Public-calendar private-field leakage | 0 disclosed private values | Inspect full JSON and nested fields | Contract |
| NFR-03 | Confirmed appointment overlaps | 0 overlaps; 100% boundary compliance | Simultaneous creates + scheduling edge cases | Contract |
| NFR-04 | Availability mismatches | 0 fixture mismatches | Compare derived day/week calendar with expected fixture | Contract |
| NFR-05 | Diary permission/semantic correctness | 100% test fixture cases | Owner/vet role checks and history/statistics assertions | Contract |
| NFR-06 | Clinical authorization/reminder correctness | 100% test fixture cases | Owner write denials and reminder state comparisons | Contract |
| NFR-07 | Invalidated token re-use | 0 successful uses | Reuse old tokens after password change/logout | Contract |
| NFR-08 | Selected response contract conformance | 100% | Automated OpenAPI schema and media-type assertions | Contract |
| NFR-09 | Time-format and boundary compliance | 100% selected fixtures | Offset parser + timezone conversion and range assertions | Contract |
| NFR-10 | Routine read latency / HTTP errors | p95 <= 2s; unintended error rate <1% | 20 VUs, 5m; measure on representative reads | Sprint 2 baseline |
| NFR-11 | Valid write latency / HTTP errors | p95 <= 3s; unintended error rate <1% | 10 VUs, 5m; clean isolated seeded data | Sprint 2 baseline |
| NFR-12 | Reachability ratio | >=99% over 7 days | Successful checks / scheduled checks x 100 | Sprint 2 baseline |
| NFR-13 | Partial state after injected failure | 0/20 corrupt partial states | Controlled fault injection, verify persistence | Sprint 2 baseline |
| NFR-14 | Calendar comprehension | >=4/5 participants succeed | Five scripted usability sessions | Sprint 2 baseline |
| NFR-15 | Upload authorization and validation | 100% permission tests; JPEG/PNG only; maximum 5 MiB | Unsupported file and role-based upload tests | Sprint 2 baseline + contract permissions |

## Measurement protocol

1. Use a separate test database populated with synthetic owners, pets, diary observations, clinical records and calendar intervals. Never use real medical or contact information for load-test fixtures.
2. Capture deployment commit, API version, database, machine resources, test period, number of virtual users and response-time percentiles.
3. For performance tests, warm up before measurement; exclude intentionally invalid requests from the error-rate denominator; report non-2xx outcomes separately, including expected 409 conflicts.
4. **p95**: 95th percentile of request duration in the measured set. **Error rate**: unintended failed requests / all valid measured requests x 100. **Availability**: successful scheduled health checks / scheduled checks x 100. Document planned maintenance handling before monitoring.
5. Report **Pass / Fail / Blocked / Not Run** with evidence, timestamps and reproducible commands. Thresholds NFR-10–15 are fixed Sprint 2 acceptance targets set by the requirements owner, not OpenAPI-defined guarantees or measured results.

## Sprint 2 measurement baseline

| Measurement dimension | Defined acceptance baseline |
|---|---|
| Read traffic | 20 concurrent virtual users for 5 minutes, after warm-up |
| Write traffic | 10 concurrent virtual users for 5 minutes, using isolated non-conflicting test data |
| Latency and error budgets | Read p95 <=2 s; write p95 <=3 s; unintended request errors <1% |
| API reachability | >=99% successful scheduled checks over 7 days; monitoring endpoint to be chosen at execution |
| Upload validation policy | JPEG/PNG only; maximum 5 MiB per image; unauthorized access denied |
| Atomicity | 0 inconsistent partial writes in 20 controlled fault-injection trials |
| Calendar usability | At least 4 out of 5 scripted participants correctly classify displayed availability |

These thresholds are owned by the Sprint 2 documentation owner and can be committed to GitHub as written. The infrastructure, test implementation and observed outcomes must still be recorded when testing takes place. The OpenAPI does not define a health endpoint or numeric upload limits; those are project acceptance targets rather than already implemented API contract rules.

## Evidence template

| Test ID | Environment / commit | Measured value | Result | Evidence link | Reviewer/date |
|---|---|---|---|---|---|
| TS-XX | Not Run | — | Not Run | — | — |

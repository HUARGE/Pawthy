# Pawthy — Non-Functional Requirements (Sprint 2)

**References:** `seng321.PDF` (current supplied SRS functional requirements, US-01–US-14); OpenAPI v0.6.3 (earlier draft, subject to reconciliation with Efe’s next version); FR-08 screenshots supplied separately.  
**Status:** Revised specification and proposed measurable acceptance targets, **pending Product Owner / technical validation**; not evidence of implementation or passed tests.  
**Scope:** Single clinic; veterinarian/administrator and pet owners; API base prefix `/api/v1` from the draft contract (verify against latest OpenAPI).

## Scope and interpretation

Pawthy supports veterinarian-managed appointments (not online owner booking), privacy-safe published availability (visible in the authenticated owner calendar, with a separate public availability API if supported by the finalized contract), owner-managed daily health diary, veterinary medical records, vaccination reminders, and a manual light/dark preference. No billing, external SMS/email, diagnosis, external calendar sync or automated appointment slots are in scope.

- **SRS behavior:** the supplied SRS is authoritative for functional behavior in this revision. Where OpenAPI v0.6.3 disagrees (including out-of-hours scheduling and owner calendar visibility), the discrepancy must be reconciled with the updated API before execution.
- **API-dependent rules:** paths, status codes and payload fields are based on the earlier OpenAPI where available; reconfirm them against Efe’s current OpenAPI before implementing automated tests.
- **Proposed targets:** numeric measures (p95, uptime, study pass rate, file limit, etc.) are quality suggestions awaiting feasibility review and Product Owner agreement, not confirmed contractual limits or measured performance.
- **Verification:** see `test-scenarios.md`; all tests remain **Not Run**.

## Requirements register

### NFR-01 — Role-based access control

- **Quality category:** Security
- **Requirement:** All protected operations enforce `x-roles`; missing/invalid bearer token yields 401 and disallowed role yields 403. Other owners’ private resources yield 404.
- **Basis:** Contract rule.
- **Acceptance criterion:** 100% of tested authorization matrix cases match documented expected status.
- **Verification:** TS-01, TS-02, TS-03.

### NFR-02 — Availability privacy and access separation

- **Quality category:** Privacy
- **Requirement:** The availability API (if designated public by the finalized OpenAPI) must reveal no pet owner, pet, visit or clinical information. The Pet Owner’s **authenticated** weekly calendar displays only available time intervals and the clinic contact number, never Busy/Closed/Occupied labels or other owners’ appointments; veterinarian calendars may show Available/Unavailable/Occupied status.
- **Basis:** SRS US-03/US-11 and public endpoint designation in OpenAPI v0.6.3 (API exposure subject to reconciliation).
- **Acceptance criterion:** 0 private fields/values leaked; 0 non-available slots shown as available in the owner calendar; owner calendar has no editing or booking controls.
- **Verification:** TS-04, TS-09, TS-23, TS-32.

### NFR-03 — Appointment scheduling integrity

- **Quality category:** Integrity
- **Requirement:** Only the veterinarian may create, reschedule or cancel appointments. Minimum duration is 10 minutes; one appointment may contain multiple pets **belonging to the same owner**. Outside working hours / unavailable periods, show a warning; on explicit veterinarian confirmation **allow** the appointment if it does not conflict with another active appointment. Cancel/dismiss the warning -> do not save. Never allow overlap with another active appointment, including after override. Cancellation frees the interval; an override does not expand standard availability.
- **Basis:** SRS US-12, especially FR-12.04–12.05, FR-12.11–12.15, BR-12.02–12.06 and AC-12.02–12.06; reconcile API override representation with updated OpenAPI.
- **Acceptance criterion:** All documented scheduling boundary, role, mixed-ownership, warning/confirmation and conflict tests behave as described; 0 overlapping active appointments after concurrent-create tests.
- **Verification:** TS-05, TS-06, TS-07, TS-08, TS-28.

### NFR-04 — Calendar precedence and consistency

- **Quality category:** Integrity
- **Requirement:** Calendar availability combines recurring weekly hours, date-specific available/unavailable exceptions, and active appointments. Exceptions adjust regular availability; appointment-occupied periods are never advertised as available. Veterinarian day/week views distinguish Available, Unavailable and Occupied. The owner’s read-only **weekly** view shows available intervals only. Existing appointments persist when hours/exceptions change.
- **Basis:** SRS US-11; prior API block conflict codes and exact structures remain subject to current contract verification.
- **Acceptance criterion:** 0 mismatches in the agreed calendar fixture; occupied/unavailable periods never appear in available-only owner intervals; no existing appointment deleted when availability changes.
- **Verification:** TS-09, TS-10, TS-23, TS-32.

### NFR-05 — Diary ownership and meaning

- **Quality category:** Integrity
- **Requirement:** An owner may create, edit and delete their pet’s health observations; veterinarian has read-only access. Multiple entries for one pet **on the same day are allowed**. Symptom Frequency counts each symptom at most once per **calendar day**, even when repeated in multiple entries; timeline retains individual entries. A day without an observation must not be interpreted as healthy; "Normal / No Concerns" is distinct and excluded from symptom-frequency counts.
- **Basis:** SRS US-05 (FR-05.3, FR-05.7) and US-06 (FR-06.4–06.5, FR-06.8–06.12).
- **Acceptance criterion:** 100% permission/validation tests correct; multiple same-day entries are preserved; daily frequency de-duplicates symptoms correctly.
- **Verification:** TS-11a, TS-11b, TS-12, TS-26a, TS-26b.

### NFR-06 — Clinical/vaccination permissions and reminder lifecycle

- **Quality category:** Integrity and privacy
- **Requirement:** Veterinarian may create, update and remove **clinical records** after confirmation; a removed/soft-deleted clinical record must disappear from owner history unless restored. Owners have read-only access to populated clinical fields of their own pets. **Vaccination records may be created/updated by veterinarian but deleted by neither role.** Reminders derive from manually set next-due dates: `Read` is independent of `Completed`; completion requires a **new, explicitly linked** vaccination record. Archived-pet reminders are hidden on the owner’s active dashboard but remain in veterinarian monitoring; deceased-pet active/future reminders are suppressed while history remains intact.
- **Basis:** SRS US-07/US-09/US-10 and FR-08 screenshot (soft-delete/empty state); precise restore behavior and API paths require updated contract check.
- **Acceptance criterion:** 100% tested role, visibility, no-delete, reminder state and archive/deceased transitions correct; no unrelated diary/vaccination records removed when a clinical record is deleted.
- **Verification:** TS-13a, TS-13b, TS-14a–TS-14d, TS-27a–TS-27c.

### NFR-07 — Credential lifecycle and secure transport

- **Quality category:** Security
- **Requirement:** Store account passwords as strong salted password hashes, never plaintext; use HTTPS in deployed authenticated traffic. First-login temporary passwords are single-use after change; reset/change invalidates prior temporary credentials and existing sessions as specified by SRS. Owner password recovery is veterinarian-managed; authentication and role rules apply to protected endpoints. Previously described logout/token invalidation and rate-limit status shall be rechecked against current OpenAPI.
- **Basis:** SRS US-02 (FR-02.8 and password-reset rules); OpenAPI v0.6.3 authentication details pending reconciliation.
- **Acceptance criterion:** 0 plaintext password storage/exposure, 0 successful HTTP downgrade for protected deployed traffic, 0 accepted invalidated credentials in selected cases; 100% first-login/reset-state checks correct.
- **Verification:** TS-15, TS-16, TS-31.

### NFR-08 — Current API contract compatibility

- **Quality category:** Interoperability
- **Requirement:** Document and validate endpoint paths, HTTP status codes, required request fields, response schemas, pagination and error formats against the **latest approved OpenAPI version**, rather than assuming v0.6.3 remains final. Confirm out-of-hours override fields, multi-pet appointments, vaccination record linking, reminder status/read transitions, clinical soft delete and availability endpoint authorization.
- **Basis:** Earlier OpenAPI v0.6.3; updated contract from Efe **pending**.
- **Acceptance criterion:** 100% selected contract fixtures validate **after** new OpenAPI is reconciled; discrepancies logged and resolved before automation.
- **Verification:** TS-17, TS-32; cross-check all API-dependent scenarios.

### NFR-09 — Explicit local time offsets

- **Quality category:** Time correctness
- **Requirement:** All appointment/calendar timestamps use ISO 8601 with offsets aligned to Europe/Istanbul and boundary filtering is correct.
- **Basis:** Contract rule.
- **Acceptance criterion:** 100% of timezone and date-boundary cases pass.
- **Verification:** TS-18.

### NFR-10 — Interactive API latency

- **Quality category:** Performance
- **Requirement:** Routine read endpoints should respond promptly at representative test load.
- **Basis:** Quality proposal; technical feasibility and Product Owner confirmation pending.
- **Acceptance criterion:** **Proposed, pending review:** read p95 ≤ 2.0 s and unintended error rate <1% at 20 virtual users for 5 minutes.
- **Verification:** TS-19.

### NFR-11 — Write transaction latency

- **Quality category:** Performance
- **Requirement:** Appointment creation and diary writes should respond promptly at representative test load.
- **Basis:** Quality proposal; technical feasibility and Product Owner confirmation pending.
- **Acceptance criterion:** **Proposed, pending review:** write p95 ≤ 3.0 s and unintended error rate <1% for valid writes at 10 virtual users for 5 minutes.
- **Verification:** TS-20.

### NFR-12 — Availability of deployed service

- **Quality category:** Reliability
- **Requirement:** A deployed Pawthy API should remain reachable over a defined observation interval.
- **Basis:** Quality proposal; technical feasibility and Product Owner confirmation pending.
- **Acceptance criterion:** **Proposed, pending review:** ≥99% successful scheduled checks over 7 days; agree on health endpoint and planned maintenance policy.
- **Verification:** TS-21.

### NFR-13 — Atomicity under failures

- **Quality category:** Reliability
- **Requirement:** Failed writes must not leave partially committed appointments or inconsistent vaccination/reminder relationships.
- **Basis:** Quality proposal; technical feasibility and Product Owner confirmation pending.
- **Acceptance criterion:** **Proposed, pending review:** 0 inconsistent persisted states in 20 controlled failure trials.
- **Verification:** TS-22.

### NFR-14 — Role-appropriate calendar usability

- **Quality category:** Usability and accessibility
- **Requirement:** Veterinarian’s day/week calendar clearly distinguishes Available / Unavailable / Occupied; the **owner sees a weekly list/calendar of available intervals only**, with clinic phone number and no scheduling action, Busy/Closed labels or other owners’ data. The web app should remain usable on narrow screens, supported modern browsers, and via keyboard with labeled controls (see NFR-16).
- **Basis:** SRS US-11 and Deniz’s product clarification.
- **Acceptance criterion:** **Proposed, pending review:** ≥4 of 5 scripted participants independently complete role-appropriate calendar tasks; zero misleading selectable owner slots. Test both roles rather than asking owners to classify hidden busy/closed slots.
- **Verification:** TS-23, TS-30.

### NFR-15 — Photo upload validation

- **Quality category:** Security
- **Requirement:** Personal/clinical photo upload endpoints enforce authorization and reject unsupported or malformed uploads under an agreed file policy.
- **Basis:** Quality proposal pending technical review and Product Owner approval.
- **Acceptance criterion:** **Proposed, pending review:** 100% unauthorized uploads denied; JPEG/PNG only and maximum 5 MiB per photo; reject malformed, unsupported or oversized files. The formats and size limit require technical and Product Owner sign-off.
- **Verification:** TS-24.

### NFR-16 — Responsive, browser-compatible and accessible web UI

- **Quality category:** Usability / accessibility
- **Requirement:** Owner and veterinarian web views should work on supported desktop and mobile widths, remain navigable via keyboard, provide usable focus indicators and labeled form controls, and render consistently in the approved browser matrix. Exact supported browser versions and accessibility conformance level require team approval.
- **Basis:** New NFR proposal in response to product review; not presented as existing SRS/API guarantee.
- **Acceptance criterion:** **Proposed, pending review:** no critical clipping or blocking interaction in agreed viewport/browser matrix; all core calendar flows keyboard operable; visible labels/focus and a basic automated accessibility scan with no critical findings.
- **Verification:** TS-30.

## Traceability and exclusions

The SRS US-01–US-14, user-provided FR-08 screenshots and updated OpenAPI collectively inform this register; where they differ, record the mismatch and reconcile with the latest approved product/API decision. API-specific paths, body fields and exact rejection status codes remain conditional until the new OpenAPI is reviewed. This scope excludes online owner booking, external SMS/email/push, automatic diagnosis, billing and external calendar integrations.

## Ownership and verification status

- **Functional source:** Current SRS / Product Owner clarification; the earlier OpenAPI v0.6.3 is a draft and not proof of implementation.
- **Quantitative targets:** NFR-10–15 (and UI matrix for NFR-16) are **proposed quality acceptance targets**. Product Owner and technical team should confirm feasibility before final baseline approval; no claim of approval is made here.
- **Execution:** All scenarios in `test-scenarios.md` remain **Not Run**. Do not report Pass/Fail without actual runs and evidence.
- **API reconciliation:** On receiving Efe’s latest OpenAPI, verify endpoint names, auth, status codes, schemas and override/reminder fields; update linked scenarios and NFR/QM traceability together.

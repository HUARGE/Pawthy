# Pawthy — Sprint 2 Architecture Deliverable Index (SRS Update)

**Owner:** Efe (Software Architect / Technical Lead)  
**Reconciled:** 20 November 2026  
**Head:** `Efesen10/Pawthy:feature/sprint2-srs-20261120-sync`; **PR target:** `HUARGE/Pawthy:develop`  
**Live API baseline:** `api/openapi.yaml` — OpenAPI 3.1.0, `info.version: 0.6.3`  
**New source evidence:** Deniz's 69-page Functional Requirements SRS + 5-page Section 5.2 Software Interfaces supplement + separately supplied **FR-08 Must Have** screenshot screenshots starting on slide 31/222 (shared externally; not committed as SRS files).  
**Review state:** Updated architect-owned **design proposal**; API spec remains inconsistent until PO/API owners reconcile, version and QA test. No code has been implemented by these documents.

## 1. Architect-owned files in this follow-up

| Deliverable | Path | Contents |
|---|---|---|
| C4 Level 1, 2, 3 | [C4 Architecture](c4-architecture.md) | Updated actors/containers/components, SRS-selected Django/MySQL, guided tour, notifications, private owner calendar, record soft-delete/restore |
| UML design | [UML Diagrams](../uml-diagrams/uml-diagrams.md) | Use Case, two Class, **six Sequences including confirmed FR-08 owner records**, three State diagrams |
| Formal Use Case source | [PlantUML](../uml-diagrams/use-cases.puml) | Explicit vet vs owner rights, US-14 tour, **confirmed FR-08 owner-only clinical reads** |
| Architecture/API/SRS traceability | [Alignment Review](api-srs-alignment.md) | All 14 story definitions, including separately received FR-08; API discrepancies and NFR design mapping |
| Full issue ledger | [SRS 20 Nov Review](srs-2026-11-20-review.md) | Detailed requirement IDs, permissions, conflicts, necessary PO decisions and supplementary 5.2 SI-01–SI-07 |
| API-owner proposal **only** | [Suggested API Delta](../../api/srs-sync-proposal-2026-11-20.md) | Current 0.6.3 vs proposed owner phone/ID login, clinical restore, calendar, appointments, reminders, US-14; candidate URLs expressly not normative |

## 2. SRS system boundaries now clarified

- Technology selected in the five-page SRS supplement: **HTML/CSS/JavaScript frontend**, **Python/Django backend**, **MySQL database**, **OpenAPI/YAML specification**. MySQL must be accessed **via the backend only**, with server-side validation, authorization and referential integrity.
- **Soft deletion and restoration** required for clinical-record lifecycle; restoration must preserve independent vaccination and owner diary records. Archive, ordinary modification and deceased state must not be confused with deletion.
- New **US-14 Optional Guided Tour** is P2/Should Have, **not a mandatory initial Sprint 3 MVP feature**. State is per owner account; four steps and Help replay are documented.
- Vet only creates appointments after phone/in-person agreement. Hard appointment overlaps are rejected even if hours are overridden. Out-of-hours/unavailable intervals require **explicit vet warning confirmation**; successful override does not unlock adjacent hours.
- Owner can only view **available** weekly times and own appointment information (read-only), never other owners' records or private vet scheduling information.
- Public clinic contact remains; whether the existing *public BUSY/CLOSED* calendar endpoint remains available is **open for PO/API decision**.
- Vaccination read status and clinical completion are independent. Deceased pet suppresses reminders while retaining history; owner archiving hides personal active reminders but should not hide vet monitoring.

## 3. Critical unresolved decisions

1. **PO editorial only:** **FR-08 View Veterinary Clinical Records (Must Have)** is now supplied in separate slides (7 FRs, 8 ACs). Update the 69-page SRS PDF by replacing duplicate §3.7 with its §3.8; owner-view behavior is **no longer provisional**.
2. **API owner:** Current 0.6.3 requires email, lacks required owner phone/Owner ID login, chip/DOB/life-stage fields, clinical restore, reminder mark-read, Guided Tour, appointment notices/Completed state and hours override.
3. **PO + security/API:** Decide whether weekly owner-only AVAILABLE calendar should replace public BUSY/CLOSED or coexist as a separately scoped API view.
4. **Backend:** Design atomic conflict checking, exception warning ACK, protected media authorization, owner/pet relationships and clinical soft-delete/restore audit and retention policies.
5. **QA:** Revise baseline v0.6.3 tests to conform to approved SRS/API changes. No tests executed or claimed passed.

## 4. Submission policy

This branch edits **only architect-owned documents and adds a nonnormative API proposal Markdown file**. It does not change `api/openapi.yaml`, `non-functional-requirements.md`, `test-scenarios.md`, teammate SRS source, frontend, CI/CD code or runtime application files.

- [x] New 69-page + supplementary 5-page SRS evidence incorporated.
- [x] C4 and UML updated and API discrepancies documented rather than hidden.
- [x] FR-08 source content received and integrated into UML/C4/API traceability; canonical PDF insertion remains a PO editorial task.
- [ ] Product Owner and API owner confirm final contract decisions.
- [ ] Diagram rendering/compilation and runtime/QA tests (not executed).
- [ ] PR reviewer approval and merge.

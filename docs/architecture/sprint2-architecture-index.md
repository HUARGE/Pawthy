# Pawthy — Sprint 2 Architecture Deliverable Index

**Owner:** Efe (Software Architect / Technical Lead)  
**Date:** 19 November 2026  
**Base:** `HUARGE/Pawthy` / `develop` (`a5c08a8534c8e6512f6621628bf751b434977d83`)  
**Contract:** `api/openapi.yaml` — OpenAPI 3.1.0, API `0.6.3`  
**Review state:** Complete first-review architecture documentation; formal Sprint 2 SRS conformity **pending** until the PO's finalized SRS is available. No code implementation or QA execution is claimed.

## Files prepared by the architecture owner

| Deliverable | File | Content |
|---|---|---|
| C4 Level 1, 2 and 3 | [C4 architecture](c4-architecture.md) | Context, Container and Backend Component views, trust boundaries and design decisions |
| UML views | [UML diagrams](../uml-diagrams/uml-diagrams.md) | 1 use-case overview, 2 class diagrams, 4 sequences and 2 state machines |
| Formal UML use-case source | [PlantUML use cases](../uml-diagrams/use-cases.puml) | Actor-to-use-case relations covering US-01–US-13 |
| API & requirements analysis | [Architecture alignment](api-srs-alignment.md) | 13/13 story-to-contract traces, 15/15 NFR architecture checks, issues A-01–A-11 and review gates |

## Confirmed scope (not implementation claims)

- **Frontend:** HTML/CSS/vanilla JavaScript per team conversation; not React.
- **Appointment booking:** By phone/in person only; veterinarian enters confirmed appointment in Pawthy. Owner's calendar view is read-only and privacy-safe. No online owner request or approval workflow.
- **User scope:** One clinic, one veterinarian/administrator and multiple pet owners. Public visitors can read the clinic contact and availability view without authentication.
- **Protected data:** Owner-authored diary vs veterinarian-authored clinical data; other owners' private records are inaccessible.
- **Architecture stack:** Python REST API, MySQL and role-protected photo storage are logical/proposed views; backend framework and persistence details require team approval.
- **Non-functional criteria:** Sıla's 15 NFRs and 24 scenarios are reviewed for architectural implications; the performance, reliability, privacy and security tests were documented but **not run**.

## Pending external decisions / next review

1. **SRS:** Compare Deniz's Sprint 2 SRS with the final C4/UML and OpenAPI; record result in `api-srs-alignment.md`. Until then SRS sign-off is **Pending**, not Pass.
2. **Backend:** Confirm Django versus FastAPI and record the decision through the team's ADR process.
3. **Calendar:** Resolve precedence/duplication between `/busy-times` and `/availability/blocks`.
4. **Security/concurrency:** Agree on secure media delivery, token invalidation mechanism, and atomic scheduling write strategy.
5. **Review:** Obtain team feedback; update only architecture-owned files before approval/merge.

## Documentation-only submission policy

This submission **does not** edit the PO's Product Backlog or SRS, QA's NFR/test files, UI/Figma assets, CI/CD plans or release settings. It introduces **no application code**. Models are intended for design review and may change with the implementation team's decisions.

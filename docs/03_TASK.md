# ResQ-Swarm Task Board

> **Last updated:** 2026-08-22  
> **Workflow:** Backlog → Ready → In progress → Review → Done

## Definition of done

A documentation or engineering task is done when its purpose, inputs, outputs, constraints, and relevant review checks are clear; the work is saved in the repository; and linked status, decision, or task records are updated where applicable.

## In progress

| ID | Task | Owner | Dependencies | Definition of done |
| --- | --- | --- | --- | --- |
| DOC-001 | Establish the documentation foundation and Engineering Guide Chapter 1. | Parteek | None | Core living documents exist; vision, scope, users, and non-goals are documented. |

## Ready

| ID | Task | Owner | Dependencies | Definition of done |
| --- | --- | --- | --- | --- |
| REQ-001 | Define the v1 disaster scenario and reference workflow. | Parteek | DOC-001 | A single scenario, actors, triggers, primary flow, exceptions, and success criteria are documented. |
| REQ-002 | Create functional requirements and acceptance criteria. | Parteek | REQ-001 | Each v1 capability has a measurable, testable requirement and clear out-of-scope boundary. |
| REQ-003 | Define non-functional and safety requirements. | Parteek | REQ-001 | Security, privacy, availability, latency, auditability, accessibility, and AI-safety expectations are documented. |
| DOC-002 | Draft Engineering Guide Chapter 2: engineering philosophy and safety constraints. | Parteek | ADR-002 | The project’s safety and engineering principles are concrete and reviewable. |

## Backlog

| ID | Task | Owner | Dependencies | Definition of done |
| --- | --- | --- | --- | --- |
| ARCH-001 | Create high-level system design. | Parteek | REQ-002, REQ-003 | Components, boundaries, integrations, and major data paths are documented. |
| DATA-001 | Design the initial domain model and ER diagram. | Parteek | ARCH-001 | Entities, relationships, constraints, and assumptions are explained before tables are created. |
| BACK-001 | Create FastAPI backend foundation. | Parteek | ARCH-001, DATA-001 | The backend skeleton follows approved architecture and includes validation, configuration, and tests. |

## Done

| ID | Task | Completed | Notes |
| --- | --- | --- | --- |
| DOC-001 | Establish the documentation foundation and Engineering Guide Chapter 1. | 2026-08-22 | Initial record; move this item here after reviewing this first draft. |

## Task rules

- Tasks describe outcomes, not vague activities such as “work on backend.”
- A task enters **Ready** only after its dependencies and success conditions are known.
- Keep one focused task in **In progress** per owner unless there is a documented reason otherwise.
- Record durable choices in the Decision Log; do not hide them only in task comments.
- Update Current Status when the next meaningful action changes.

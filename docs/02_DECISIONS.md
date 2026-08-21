# ResQ-Swarm Decision Log

> Architecture Decision Records (ADRs) capture decisions that are important enough to affect future work. They are append-only: do not rewrite history; supersede an earlier decision with a new record when needed.

## Record format

- **Status:** Proposed, Accepted, Superseded, or Rejected
- **Context:** The problem or constraint prompting the decision.
- **Decision:** The chosen direction.
- **Alternatives considered:** Realistic options and why they were not selected.
- **Consequences:** Benefits, costs, risks, and follow-up work.

---

## ADR-001 — Establish a documentation-first engineering workflow

- **Date:** 2026-08-22
- **Status:** Accepted

### Context

ResQ-Swarm is being developed as both a disaster-response product and a software-engineering learning project. Decisions about safety, architecture, data, and AI must remain understandable to current and future contributors.

### Decision

Maintain living project documentation in `docs/` from the beginning. Keep current status, decisions, tasks, architecture, technical domains, meeting notes, and the development journal in separate documents with clear purposes.

### Alternatives considered

- Document only after implementation: rejected because rationale and constraints are easily lost.
- Keep all notes in one README: rejected because active status, permanent standards, and technical reference material become difficult to find and maintain.

### Consequences

- Contributors must update relevant documentation as part of completing meaningful work.
- Documentation review becomes part of the definition of done for decisions and features.
- The initial development pace is slower, but later architectural and onboarding costs are reduced.

---

## ADR-002 — Keep humans accountable for operational decisions

- **Date:** 2026-08-22
- **Status:** Accepted

### Context

Disaster imagery and AI outputs are uncertain, while rescue decisions can have severe consequences.

### Decision

ResQ-Swarm will be designed as a human-in-the-loop decision-support system. AI detections and priority scores are recommendations with visible evidence and uncertainty; they do not autonomously dispatch resources or replace field verification.

### Alternatives considered

- Autonomous task assignment: rejected for the initial product because it requires safety validation, operational authority, and safeguards beyond the project’s early scope.
- Display detections without explanations: rejected because users need to understand why a recommendation exists before relying on it.

### Consequences

- Requirements, data models, and UI designs must accommodate explanations, provenance, and human review.
- The project must avoid language that represents predictions as facts.
- A later automation feature would require a new decision record and explicit safety review.

---

## ADR-003 — Use MySQL and YOLOv8n as the initial database and detection baseline

- **Date:** 2026-08-22
- **Status:** Accepted

### Context

The project needs a consistent technical baseline before database design, backend integration, and AI-pipeline planning begin. The existing repository README and the earlier project brief referenced different database and YOLO versions.

### Decision

Use **MySQL** as ResQ-Swarm's relational database and **YOLOv8n** as its initial object-detection model baseline. YOLOv8n is the nano variant of YOLOv8; it is suited to establishing a fast, resource-conscious proof of concept before model accuracy and hardware constraints are evaluated with representative data.

### Alternatives considered

- PostgreSQL: deferred. It remains a viable relational database, especially for advanced geospatial requirements, but is not the selected baseline.
- Larger YOLOv8 variants or YOLOv11: deferred. They may offer different accuracy, speed, and deployment trade-offs, but require evidence from evaluation data and target hardware before adoption.

### Consequences

- The database schema, SQLAlchemy configuration, migrations, Docker configuration, and backend documentation must target MySQL.
- AI documentation and the initial inference pipeline must refer to YOLOv8n.
- The team must benchmark YOLOv8n against representative RGB and thermal data before treating it as production-ready.
- Any future model or database change requires a new decision record and migration or evaluation plan.

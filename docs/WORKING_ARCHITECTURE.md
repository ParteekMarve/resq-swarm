# ResQ-Swarm — Working Architecture

> Living design document. Update this file whenever a workflow, architecture, or system-design decision is finalized.

## Status

- Phase: Requirement Analysis / Architecture Discovery
- Version: V1
- Last updated: 2026-08-22

## V1 Product Scope

ResQ-Swarm is a web-based disaster-response decision-support platform for emergency operations. V1 focuses on three primary users:

1. Drone Operator
2. Incident Commander / Lead Rescue Officer
3. Rescue Team Leader

The system assists human responders; it does not replace them or make final medical or rescue decisions.

## Core Communication Architecture

All application communication is coordinated through the FastAPI backend.

```text
User
  ↓
React Frontend (Browser)
  ↓ HTTP/API
FastAPI Backend
  ├── MySQL Database
  └── AI Module
```

The frontend does not access MySQL directly. The AI module does not access MySQL directly. The backend is the integration layer and source of application truth.

## Mission Initialization

The first interaction with ResQ-Swarm is creation of a rescue mission by the Disaster Management Authority or the Incident Commander after an emergency has been confirmed.

```text
Disaster Occurs
  ↓
Emergency Confirmed
  ↓
New Rescue Mission Created
  ↓
Unique Mission ID Generated
  ↓
Mission Data Becomes Available to Participating Modules
```

All subsequent drone activity, AI detections, assignments, and mission updates are associated with the mission.

## Mission Area

A disaster is treated as an area rather than a single point. The commander should define the mission's affected Area of Operation on an interactive map using coordinates and a boundary/polygon.

```text
Create Mission
  ↓
Open Interactive Map
  ↓
Define Affected Area / Boundary
  ↓
Store Boundary Coordinates
  ↓
Select Disaster Type
```

The exact map interaction and GIS implementation are still to be finalized.

## Current End-to-End Workflow (Draft)

```text
Disaster Occurs
  ↓
Emergency Response Team is Deployed
  ↓
Mission Created
  ↓
Mission Area Defined
  ↓
Disaster Type Selected
  ↓
Drone Operator Prepares / Launches Drone
  ↓
Drone Captures RGB / Thermal Data
  ↓
AI Module Processes Data
  ↓
Victims and Hazards Detected
  ↓
AI Returns Structured Results
  ↓
Backend Stores / Processes Results
  ↓
Commander Dashboard Displays Results
  ↓
Incident Commander Reviews Recommendations
  ↓
Commander Assigns Rescue Team
  ↓
Rescue Team Performs Rescue
  ↓
Rescue Team Updates Mission Status
  ↓
Mission Completed
```

This workflow is intentionally a draft and will be refined one decision at a time.

## Information Flow

### Create Mission

```text
Incident Commander
  ↓
React Frontend
  ↓
FastAPI Backend
  ↓
Validate Request
  ↓
MySQL
  ↓
Mission Created + Mission ID
  ↓
React Frontend
```

### AI Detection

```text
Drone Data
  ↓
AI Module
  ↓
Structured Detection Result
  ↓
FastAPI Backend
  ↓
MySQL / Dashboard
```

The backend should consume the AI module's defined output contract without depending on the AI module's internal implementation.

## AI Module Boundary

Member 2 owns the internal AI / computer-vision implementation. The system-level architecture only defines the interface between AI and backend.

Expected AI output may include, subject to final interface design:

- Victim detection
- Hazard detection
- Tracking identity
- Confidence
- Priority level
- Explanation / supporting evidence
- Location / bounding-box information

The internal YOLO, tracking, posture, hazard, and priority algorithms are owned by the AI module and should not be duplicated in the backend.

## Current V1 Boundaries

Included:

- Mission management
- Affected-area definition
- Drone data ingestion / simulation as required for the prototype
- AI detection results
- Commander dashboard
- Rescue-team assignment
- Mission status tracking
- MySQL persistence
- FastAPI backend

Not currently included:

- Government/state/national dashboards
- Hospital integration
- Automatic SMS/email/WhatsApp alerting
- Public portal
- Autonomous drone flight/control
- Large-scale multi-state coordination

These may be future enhancements.

## Architecture Decisions Recorded So Far

### Decision 0 — Central Backend Coordination

All communication between the frontend, database, and AI module is coordinated through the FastAPI backend.

**Reason:** security, validation, maintainability, clear module boundaries, and a single application integration point.

### Decision 1 — Mission-Based Operation

Operations start when an authorized authority / Incident Commander creates a rescue mission. A unique Mission ID identifies the operation.

**Reason:** all mission-related data must remain grouped and traceable.

### Decision 2 — Area-Based Mission Definition

The mission area is represented as an affected geographic area with a boundary/polygon rather than only a single coordinate.

**Reason:** disasters affect regions, and the mission area provides operational context for drones, detections, and rescue teams.

## Open Questions

- What exact information is required to create a mission?
- What happens operationally between mission creation and drone launch?
- How should drone operators be assigned to missions?
- Is live streaming required for V1, or is simulated/uploaded footage sufficient for the prototype?
- What exact data contract will AI provide to the backend?
- How should rescue teams be represented and assigned?

## Update Rule

Whenever a decision is finalized during project design:

1. Update the relevant section of this document.
2. Add or revise the architecture diagram/workflow if needed.
3. Record the decision and its reasoning.
4. Only then move to the next design question.

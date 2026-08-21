# ResQ-Swarm Engineering Guide

> **Status:** Living document  
> **Purpose:** The engineering, learning, and collaboration handbook for ResQ-Swarm.  
> **Last updated:** 2026-08-22

## How to use this guide

This guide records *how* ResQ-Swarm is built and the reasoning behind important choices. It is written for project contributors, reviewers, and future assistants. It is not a replacement for day-to-day status, individual tasks, or dated decisions:

- Use [Current Status](01_CURRENT_STATUS.md) to find the project phase and immediate next step.
- Use [Decisions](02_DECISIONS.md) to understand choices that have been made and their consequences.
- Use [Task Board](03_TASK.md) to find planned and active work.

Each chapter is added only when its subject becomes relevant. A contributor should update this guide when a new standard or enduring engineering practice is agreed, rather than documenting temporary task details here.

## Table of contents

1. [Project vision, scope, users, and non-goals](#1-project-vision-scope-users-and-non-goals)
2. Engineering philosophy and safety constraints *(planned)*
3. Team roles and collaboration *(planned)*
4. Development workflow and definition of done *(planned)*
5. Architecture and repository conventions *(planned)*
6. Technology choices and rationale *(planned)*
7. Engineering standards *(planned)*
8. Git and review workflow *(planned)*
9. Testing, security, and operations *(planned)*
10. Documentation and learning workflow *(planned)*

---

# 1. Project vision, scope, users, and non-goals

## 1.1 Problem

In a disaster, responders must make time-sensitive decisions with incomplete information. Floods, earthquakes, landslides, fires, and building collapses can leave victims obscured, routes blocked, and hazards changing faster than a command centre can manually process incoming drone footage. A single drone mission may create hundreds or thousands of images. Reviewing every image manually delays action; relying on an unexplained automated result can create a different, equally serious risk.

The core problem is therefore not simply object detection. It is turning imperfect aerial observations into timely, understandable decision support for trained human responders.

## 1.2 Need

Emergency teams need a system that helps them answer four practical questions:

1. Where might people requiring help be located?
2. What hazards or access constraints exist near those locations?
3. Which locations deserve attention first, given limited rescue capacity?
4. Why did the system make that recommendation, and how reliable is its evidence?

Any useful answer must preserve uncertainty. A model detection is evidence, not proof of a victim. A priority score is a recommendation, not an instruction to deploy. Human responders retain authority for every operational decision.

## 1.3 Product vision

ResQ-Swarm is an explainable AI-powered disaster-response platform that converts RGB and thermal drone imagery into a shared operational picture for rescue teams.

It will help operators and command-centre personnel identify possible victims and hazards, follow observations across frames, rank rescue candidates, and inspect the factors behind each recommendation. It is designed to support NDRF, SDRF, disaster-management authorities, drone operators, and rescue command centres in high-pressure environments.

The product vision is:

> Give responders faster situational awareness and transparent prioritization while keeping humans accountable for rescue decisions.

This sentence is the design filter for the project. A proposed feature belongs in ResQ-Swarm only if it materially improves situational awareness, transparent prioritization, or coordination without reducing human oversight.

## 1.4 Product principles

The following principles apply to product and engineering decisions.

### Human-in-command

ResQ-Swarm recommends; authorized responders decide and act. The interface must not imply that a recommendation is an order, a detection is certain, or an AI result replaces field verification.

### Explainability is a feature, not an afterthought

Every priority recommendation must expose the evidence and factors that influenced it: for example, detection confidence, hazard proximity, apparent urgency, signal recency, location, and missing or uncertain information. A score without a comprehensible reason is not sufficient for this product.

### Uncertainty must be visible

Computer-vision performance can fall in smoke, poor light, debris, rain, occlusion, low-resolution feeds, and unusual camera angles. The system must communicate confidence, data freshness, and known limitations rather than presenting a false sense of precision.

### Safety before convenience

Features that increase the chance of harmful over-reliance, conceal uncertainty, expose sensitive location data, or create an unauditable workflow require explicit review. Speed is valuable only when it does not compromise safe human decision-making.

### Progressive delivery

The first release should solve a small, testable operational workflow well. Complex capabilities—multi-drone orchestration, fully automatic routing, broad inter-agency integrations, or advanced predictive analytics—must not be assumed before the foundation is validated.

## 1.5 Target users and their needs

| User | Primary need | ResQ-Swarm support |
| --- | --- | --- |
| Drone operator | Turn live or recorded imagery into usable observations quickly. | Upload or stream imagery, inspect detections, correct obvious false positives, and report source context. |
| Rescue command centre | Build and maintain a reliable operational picture. | View candidate locations, hazards, freshness, priority explanations, and team-relevant context. |
| Field rescue team | Receive clear, actionable context before verification on site. | Receive a human-approved task with location, evidence, hazards, and confidence—not an opaque AI instruction. |
| Incident commander | Allocate scarce personnel and justify decisions. | Compare prioritized candidates, review explanations, and retain an auditable decision trail. |
| System administrator | Protect the system and keep it available. | Manage access, data retention, monitoring, and controlled deployment. |

Secondary users, including hospitals, NGOs, volunteers, and government agencies, may consume approved information later. They are not assumed to have the same data access or decision-making authority as primary users.

## 1.6 Initial product scope

The first product increment will be defined through requirements gathering. The following capabilities are in scope as candidate outcomes, not yet implementation commitments:

- Ingest RGB and, where available, thermal drone images or video.
- Identify possible victim-related detections and visible hazards.
- Preserve observation metadata such as mission, time, source, location when available, and confidence.
- Track relevant detections across video frames when a video workflow is selected.
- Present candidate rescue priorities with a factor-by-factor explanation.
- Provide a dashboard that communicates the current situation to authorized users.
- Maintain an auditable record of AI outputs and human review actions appropriate to the selected workflow.

Each capability must receive acceptance criteria, failure cases, data requirements, and a safety review before it enters development.

## 1.7 Explicit non-goals for the first release

Writing non-goals is a professional way to protect delivery. It does not mean an idea is unimportant; it means the idea is deliberately deferred until the team has evidence, capacity, and safe requirements for it.

ResQ-Swarm v1 will not:

- Autonomously dispatch rescue teams, aircraft, or drones.
- Make medical diagnoses, declare a person deceased, or estimate survival outcomes.
- Guarantee that a detected object is a person or that an undetected area contains no victims.
- Replace incident-command procedures, field verification, or responder judgement.
- Provide public, unrestricted access to sensitive disaster imagery or precise victim locations.
- Assume continuous internet connectivity or perfect GPS, thermal, or video data.
- Attempt broad emergency-resource optimization before the core observation-to-explanation workflow is validated.
- Train new detection models as part of the initial platform foundation unless requirements and suitable, governed datasets are established.

## 1.8 A concrete first workflow

The project needs one end-to-end scenario before it needs many features. Our initial reference workflow is:

1. An authorized drone operator submits imagery from a named disaster-response mission.
2. The system records the source and processes the imagery for possible victim and hazard observations.
3. A command-centre user sees candidate observations with evidence, confidence, source time, and location when available.
4. The priority engine produces a recommendation and an explanation of contributing factors.
5. An authorized human reviews the information and records an operational decision outside or inside the system, depending on approved requirements.

This is analogous to a hospital triage board: it helps staff see and prioritize cases, but it does not replace the clinician who assesses, verifies, and takes responsibility for care.

## 1.9 Professional use of scope

Professional teams use scope to make trade-offs visible. When a request arrives, they ask:

1. Which user problem does this solve?
2. Is it needed for the reference workflow?
3. What data, safety, privacy, and operational assumptions does it introduce?
4. How will we know it works, including when it fails?
5. What existing work does it delay?

For ResQ-Swarm, the answer must be written down before a major feature is accepted. Requirements will later turn these answers into user stories and acceptance criteria.

## 1.10 Common mistakes to avoid

- **Building around the model instead of the workflow.** A high-performing detector alone does not tell a rescuer what to do safely.
- **Treating confidence as certainty.** Confidence is a model signal; it is not a verified probability of a victim needing rescue.
- **Adding a priority score without rationale.** A score needs clear factors, data provenance, and uncertainty.
- **Using a dashboard as a substitute for requirements.** Screens should be designed from user decisions and tasks, not from available charts.
- **Expanding to every disaster type immediately.** Start with a defined scenario and record what must change for each additional context.
- **Ignoring data governance.** Drone footage can contain people, homes, locations, and operationally sensitive information.

## 1.11 Interview perspective

When discussing ResQ-Swarm in an interview or review, describe it as a human-in-the-loop decision-support system. Explain that its value is the combination of computer vision, transparent prioritization, and operational context—not merely the use of YOLO, FastAPI, or React. Emphasize the controls: confidence visibility, explanations, auditability, access control, and human approval.

## 1.12 Chapter summary

ResQ-Swarm exists to help trained responders turn drone imagery into transparent, time-sensitive situational awareness. Its first responsibility is decision support, not autonomous action. The first release will focus on a narrow observation-to-explanation workflow and will openly communicate uncertainty. These boundaries will guide requirements, architecture, database design, AI evaluation, interface design, and deployment decisions.


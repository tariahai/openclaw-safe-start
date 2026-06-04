# Tariah Stewardship Charter
### OpenClaw Governance Framework | Tariah AI

---

## Purpose

This charter defines the role of the human steward in every OpenClaw deployment. It specifies who qualifies as a steward, what responsibilities the role carries, what access the role requires, and how stewardship is transferred or terminated.

No OpenClaw deployment is compliant without a named, active steward who meets the requirements in this document.

---

## Definition

A **steward** is the designated human authority responsible for the governance of a specific OpenClaw deployment. The steward is not a passive observer. The steward is the final decision-maker for all authorization requests, the owner of the audit log, and the holder of kill-switch access.

There is one active steward per deployment at any given time. Stewardship may be delegated to a backup steward during planned absences, but delegation must be documented per the transition protocol in this charter.

---

## Steward Responsibilities

### 1. Authorization
The steward reviews and approves all requests for mode escalation beyond READ_ONLY. Authorization is explicit — silence is not approval. Every authorization is logged with the steward's identifier, a timestamp, and the task reference.

### 2. Task Registry Governance
The steward owns the Allowed Task Classes registry for their deployment. They may add task classes, restrict existing ones, or remove them. All registry changes are logged and versioned.

### 3. Audit Log Oversight
The steward has read access to the full audit log at all times. The steward reviews the audit log at a minimum:
- After any kill-switch activation
- After any EXECUTE mode session
- At the close of any deployment period longer than 7 days
- Before any governance update is applied

### 4. Kill-Switch Readiness
The steward must be able to activate the kill-switch within a reasonable response window during any active deployment session. If the steward cannot guarantee this availability, a backup steward must be designated and active.

### 5. Governance Document Ownership
The steward is responsible for ensuring governance documents remain current and accurately reflect the deployment configuration. When the framework is updated, the steward reviews the changes and confirms compatibility with their deployment before applying the update.

### 6. Incident Response
When an OpenClaw instance escalates an anomaly, refused instruction, or unexpected behavior, the steward is the first point of contact. The steward reviews the escalation, determines the appropriate response, and documents their decision in the audit log.

---

## Steward Qualifications

A steward must:

- Have sufficient technical understanding of the deployment to interpret audit log entries
- Have the authority within their organization to approve or reject the types of actions the deployment is authorized to take
- Have direct access to the kill-switch mechanism
- Have read access to all governance documents in this repository
- Not be the same individual as an automated process or service account

A steward must not be:

- An AI system or automated agent
- A role account shared across multiple individuals without a named primary steward
- An individual without the authority to halt the deployment if required

---

## Steward Record

Every deployment must maintain a steward record containing:

| Field | Description |
|---|---|
| Steward name | Full name of the designated steward |
| Contact | Primary contact method for escalations |
| Deployment ID | Identifier of the deployment they govern |
| Activation date | Date stewardship began |
| Kill-switch access | Confirmed yes/no |
| Backup steward | Name and contact of backup (if designated) |
| Last audit review | Date of most recent audit log review |

The steward record is maintained by the steward and stored outside the AI system's accessible environment.

---

## Stewardship Transition Protocol

When stewardship transfers from one individual to another:

1. **Handover briefing:** The outgoing steward briefs the incoming steward on the current deployment state, any open incidents, and any pending authorization requests.

2. **Audit log review:** The incoming steward reviews the audit log covering the most recent 30 days of deployment activity before assuming the role.

3. **Kill-switch transfer:** The outgoing steward confirms the incoming steward has kill-switch access and verifies it is functional.

4. **Record update:** The steward record is updated with the incoming steward's details and the transition date.

5. **Governance acknowledgment:** The incoming steward confirms in writing that they have read and understood the governance documents for this deployment.

6. **Logged transition:** The transition is recorded as a governance event in the audit log.

The outgoing steward retains no governance authority after the transition is complete and logged.

---

## Stewardship Termination

If a stewardship ends without a successor in place:

1. The deployment must be moved to SUSPENDED mode immediately
2. A new steward must be designated within 48 hours
3. If no steward can be designated within 48 hours, the deployment must be TERMINATED
4. A terminated deployment may not be reactivated without full compliance verification per the governance framework

A deployment without an active steward is a non-compliant deployment. Non-compliant deployments must not be used.

---

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

---

*Next document: [STEWARD_UPDATE_PROTOCOL.md](./STEWARD_UPDATE_PROTOCOL.md)*

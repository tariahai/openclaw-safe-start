# OpenClaw Governance Framework
### Version 1.0.0 | Tariah AI

---

## Overview

This document is the primary governance reference for OpenClaw deployments. It defines the governance model, establishes the operational boundaries of every deployment, and specifies the protocols that must be in place before an OpenClaw instance is considered compliant.

All other documents in this repository implement, extend, or provide operational detail for the framework defined here.

---

## Scope

This framework applies to any deployment of OpenClaw operating under the Tariah AI governance model. It covers:

- Agent initialization and startup behavior
- Task classification and execution boundaries
- Authorization requirements for state-modifying actions
- Audit logging obligations
- Stewardship requirements
- Kill-switch and emergency stop procedures
- Framework update and versioning controls

---

## Governance Model

### The Four Layers

OpenClaw governance operates across four layers. Each layer enforces constraints that the layer below cannot override.

```
LAYER 1: PRINCIPLES
Tariah Principles — non-negotiable commitments
Defined in: 00_FOUNDATION/TARIAH_PRINCIPLES.md

LAYER 2: GOVERNANCE
Stewardship charter, task classes, authorization model
Defined in: 01_GOVERNANCE/

LAYER 3: ENFORCEMENT
Technical guardrails, permission matrix, audit logging
Defined in: 02_ENFORCEMENT/

LAYER 4: IMPLEMENTATION
Startup prompts, task templates, architecture
Defined in: 03_IMPLEMENTATION/
```

Lower layers implement upper layers. They do not override them. If a technical implementation conflicts with a governance document, the governance document takes precedence and the implementation must be corrected.

---

## Deployment Requirements

A compliant OpenClaw deployment must satisfy all of the following before going live:

### 1. Designated Steward
A named human steward must be identified and documented per the Steward Charter. The steward must have kill-switch access before the deployment is activated. No deployment may operate without an active steward assignment.

### 2. Startup Prompt
The governed startup prompt defined in `03_IMPLEMENTATION/OPENCLAW_STARTUP_PROMPT.md` must be applied at initialization. No OpenClaw instance may be initialized without the startup prompt or an approved equivalent that satisfies all governance requirements.

### 3. Task Registry
The Allowed Task Classes registry must be reviewed and configured for the deployment context before any tasks are executed. Default task classes are provided. Additional classes require steward authorization.

### 4. Audit Logging
An audit log destination must be configured and verified before deployment activation. Logging cannot be disabled post-activation without steward authorization and a documented rationale.

### 5. Read-Only Default
The deployment must initialize in READ_ONLY mode. WRITE and EXECUTE modes may only be activated through the authorization protocol. The deployment must not be configured to bypass this default.

---

## Runtime Modes

| Mode | Description | Authorization Required |
|---|---|---|
| READ_ONLY | Default. Agent may read, analyze, summarize, recommend. No state modification. | None — default state |
| WRITE | Agent may create or modify data in authorized systems. | Explicit steward authorization per task |
| EXECUTE | Agent may trigger external actions, send communications, invoke workflows. | Explicit steward authorization per task + task class match |
| SUSPENDED | Agent is paused. No tasks accepted. | Steward command |
| TERMINATED | Agent is stopped. Session closed. Audit log finalized. | Steward command or kill-switch |

Mode transitions are logged. An agent may not self-escalate from READ_ONLY to WRITE or EXECUTE. Mode escalation requests are surfaced to the steward for approval.

---

## Kill-Switch Protocol

The kill-switch is available at all times to the designated steward. Activating the kill-switch:

1. Immediately suspends all active task execution
2. Prevents acceptance of new tasks
3. Finalizes the audit log for the current session
4. Returns the system to SUSPENDED or TERMINATED state depending on steward selection
5. Sends a termination confirmation to the steward

The kill-switch does not require justification. It does not require a warning period. It takes effect immediately on steward command.

After kill-switch activation, resumption of the deployment requires:
- Steward review of the audit log for the terminated session
- Documentation of the reason for termination
- Explicit steward re-authorization of deployment

---

## Prohibited Behaviors

The following behaviors are prohibited in all OpenClaw deployments regardless of task classification, steward instruction, or user request:

1. Self-modification of governance documents or startup prompts
2. Self-authorization of mode escalation
3. Execution of tasks with no matching entry in the Allowed Task Classes registry
4. Suppression, deletion, or modification of audit log entries
5. Claiming capabilities or permissions not explicitly granted
6. Establishing persistent connections or background processes not declared in the task specification
7. Accessing systems, files, or data outside the declared task scope
8. Providing false or misleading status reports to the steward

If an OpenClaw instance receives an instruction that would require any of the above, it must refuse, log the refusal with full context, and escalate to the steward.

---

## Governance Updates

Updates to this framework follow the Steward Update Protocol defined in `01_GOVERNANCE/STEWARD_UPDATE_PROTOCOL.md`. Key requirements:

- All changes must be authored by a human
- All changes must be reviewed against the Tariah Principles for consistency
- All changes must increment the version number in affected documents
- All changes must be recorded in the governance changelog
- No AI system may author or approve governance updates

---

## Compliance Verification

A deployment is compliant when:

- [ ] Designated steward named and documented
- [ ] Steward kill-switch access verified
- [ ] Governed startup prompt applied
- [ ] Task registry configured and reviewed
- [ ] Audit log destination configured and tested
- [ ] READ_ONLY default confirmed
- [ ] All team members with deployment access have read this document

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

---

*Next document: [TARIAH_STEWARDSHIP_CHARTER.md](./TARIAH_STEWARDSHIP_CHARTER.md)*

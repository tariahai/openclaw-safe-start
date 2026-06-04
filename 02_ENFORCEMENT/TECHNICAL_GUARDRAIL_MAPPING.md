# Technical Guardrail Mapping
### OpenClaw Governance Framework | Tariah AI

---

## Purpose

This document maps every governance requirement in the OpenClaw framework to its technical implementation. It is the bridge between the governance documents and the code.

For each governance requirement, this document specifies:
- What the requirement is
- Where it is defined
- How it is technically enforced
- What failure looks like
- How failure is handled

Developers implementing OpenClaw should use this document as their technical specification. If a governance requirement has no corresponding technical implementation, that is a gap that must be addressed before the deployment is considered compliant.

---

## Guardrail Map

---

### G-001: Read-Only Default

**Governance source:** `OPENCLAW_GOVERNANCE_FRAMEWORK.md` — Runtime Modes; `READ_ONLY_RUNTIME.md`

**Requirement:** All OpenClaw instances must initialize in READ_ONLY mode. No state-modifying operation may be invoked without explicit mode escalation.

**Technical implementation:**
- Startup prompt initializes with explicit READ_ONLY mode declaration
- Tool permission matrix restricts all write/execute tools to WRITE or EXECUTE mode
- Mode state variable is initialized to `READ_ONLY` and persisted in session context
- Any tool invocation is preceded by a mode check; tools not permitted in current mode are blocked at the tool call layer

**Failure mode:** Agent attempts to invoke a write tool while in READ_ONLY mode

**Failure handling:** Tool call is intercepted and blocked. Refusal is logged with tool name, task reference, and current mode. Escalation surfaced to steward.

---

### G-002: Task Classification Gate

**Governance source:** `ALLOWED_TASK_CLASSES.md`

**Requirement:** Every task must be classified against the Allowed Task Classes registry before any tool is invoked.

**Technical implementation:**
- Task intake step precedes all tool invocation
- Classification logic maps task description to registry entries
- If no match: task is refused, refusal logged, steward notified
- If ambiguous match: most restrictive class selected, ambiguity logged
- Matched class is stored in task context and referenced in all subsequent log entries

**Failure mode:** Task description does not match any registry entry

**Failure handling:** Task refused with logged explanation. No tools invoked. Steward receives escalation with the submitted task description for review.

---

### G-003: Authorization Gate for Mode Escalation

**Governance source:** `AUTHORIZATION_MODEL.md`; `OPENCLAW_GOVERNANCE_FRAMEWORK.md` — Runtime Modes

**Requirement:** WRITE and EXECUTE mode require explicit human authorization. The AI system cannot self-authorize escalation.

**Technical implementation:**
- Mode escalation requests are formatted as structured escalation outputs delivered to the steward interface
- System waits for explicit authorization signal before mode state transitions
- Authorization signal includes steward identifier and task reference
- Authorization is logged before mode transition occurs
- Timeout without authorization returns task to READ_ONLY queue

**Failure mode:** Authorization signal not received within timeout window

**Failure handling:** Task remains in READ_ONLY state. Steward receives a pending authorization reminder. Task is not executed until authorization is provided or explicitly cancelled.

---

### G-004: Audit Log Integrity

**Governance source:** `AUDIT_LOGGING_STANDARD.md`

**Requirement:** All actions, refusals, mode transitions, and escalations must be logged. Logs may not be suppressed, modified, or deleted by the AI system.

**Technical implementation:**
- Audit log is written to an append-only destination outside the agent's write scope
- Log entries are written synchronously before the corresponding action is taken
- Log destination is configured at deployment time and cannot be changed during a session
- Log write failures surface as blocking errors — the action does not proceed if the log cannot be written
- Log entries include: timestamp, session ID, task reference, action type, mode, outcome, agent identifier

**Failure mode:** Log write fails

**Failure handling:** Action is blocked. Agent surfaces a log failure alert to the steward. Session may be suspended pending log destination review.

---

### G-005: Kill-Switch Implementation

**Governance source:** `OPENCLAW_GOVERNANCE_FRAMEWORK.md` — Kill-Switch Protocol

**Requirement:** The steward must be able to immediately halt all execution at any time. Kill-switch activation must be immediate and must not require the agent's cooperation.

**Technical implementation:**
- Kill-switch signal is implemented at the infrastructure layer, not the agent prompt layer
- Signal sources: steward UI command, API endpoint with steward credentials, emergency stop button in deployment interface
- On receipt of kill-switch signal: active task execution is halted at the next atomic step, new task intake is disabled, session state is frozen, audit log entry is written, steward confirmation is generated
- Kill-switch implementation does not depend on the agent's ability to receive or interpret the signal

**Failure mode:** Kill-switch signal not acknowledged

**Failure handling:** Secondary termination mechanism activates (process kill at infrastructure level). Post-incident review required.

---

### G-006: Scope Boundary Enforcement

**Governance source:** `TOOL_PERMISSION_MATRIX.md`; `TARIAH_PRINCIPLES.md` — Principle 1

**Requirement:** An OpenClaw instance may only access systems, files, and data within the declared task scope. Out-of-scope access attempts are refused and logged.

**Technical implementation:**
- Tool permission matrix is loaded at startup and defines accessible resources per task class
- All tool calls are validated against the matrix before execution
- Resource identifiers in tool calls are checked against the scope definition
- Out-of-scope resource identifiers are blocked at the validation layer

**Failure mode:** Tool call references a resource outside declared scope

**Failure handling:** Tool call blocked. Refusal logged with resource identifier and task reference. Steward notified if pattern of out-of-scope attempts is detected.

---

### G-007: Prohibited Behavior Detection

**Governance source:** `OPENCLAW_GOVERNANCE_FRAMEWORK.md` — Prohibited Behaviors

**Requirement:** OpenClaw instances must refuse instructions that would require any of the seven prohibited behaviors.

**Technical implementation:**
- Startup prompt encodes prohibited behavior list as explicit refusal conditions
- Incoming task descriptions are checked for patterns matching prohibited behaviors before classification
- If a prohibited behavior pattern is detected: task is refused, explanation logged, steward escalation generated
- Refusal is not negotiable — no steward override can authorize a prohibited behavior

**Failure mode:** Instruction to execute a prohibited behavior is submitted

**Failure handling:** Task refused with full context logged. Steward receives escalation. If prohibited behavior instruction is repeated, session is flagged for review.

---

### G-008: Governance Document Immutability

**Governance source:** `STEWARD_UPDATE_PROTOCOL.md`; `TARIAH_PRINCIPLES.md` — Principle 3

**Requirement:** Governance documents may not be modified by the AI system. All modifications are human-authored and human-applied.

**Technical implementation:**
- Governance document directory is mounted read-only in the agent's execution environment
- Agent has no write permissions to governance file paths
- Startup prompt explicitly encodes the prohibition on governance self-modification
- Any instruction to modify a governance document triggers a prohibited behavior refusal (G-007)

**Failure mode:** Instruction to modify governance document is submitted

**Failure handling:** Refused via G-007 protocol. Logged with full context.

---

## Guardrail Coverage Matrix

| Governance Requirement | Guardrail ID | Implemented |
|---|---|---|
| Read-only default | G-001 | Required |
| Task classification gate | G-002 | Required |
| Authorization for mode escalation | G-003 | Required |
| Audit log integrity | G-004 | Required |
| Kill-switch | G-005 | Required |
| Scope boundary enforcement | G-006 | Required |
| Prohibited behavior detection | G-007 | Required |
| Governance document immutability | G-008 | Required |

All eight guardrails are required for a compliant deployment. A deployment with any guardrail unimplemented is non-compliant.

---

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

---

*Next document: [TOOL_PERMISSION_MATRIX.md](./TOOL_PERMISSION_MATRIX.md)*

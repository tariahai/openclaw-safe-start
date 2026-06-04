# Governor Architecture
### OpenClaw Governance Framework | Tariah AI

---

## Purpose

This document describes the technical architecture of a compliant OpenClaw governance implementation. It is a reference for developers building or reviewing an OpenClaw deployment. It does not prescribe a specific technology stack — it defines the functional components that must be present and how they must interact.

---

## Architectural Overview

A governed OpenClaw deployment consists of six functional components. Each component implements one or more guardrails defined in the Technical Guardrail Mapping.

```
┌─────────────────────────────────────────────────────────────┐
│                    STEWARD INTERFACE                         │
│  Authorization requests | Kill-switch | Audit log access     │
└──────────────────────────────┬──────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                   GOVERNOR LAYER                             │
│  Task intake → Classification → Authorization → Execution   │
└──────┬──────────────┬──────────────┬────────────────────────┘
       │              │              │
┌──────▼──────┐  ┌────▼────┐  ┌─────▼──────────────────────┐
│  TASK       │  │  MODE   │  │   TOOL PERMISSION          │
│  REGISTRY   │  │  STATE  │  │   ENFORCEMENT LAYER        │
│             │  │  MANAGER│  │                            │
└─────────────┘  └─────────┘  └────────────────────────────┘
                                         │
┌────────────────────────────────────────▼───────────────────┐
│                     AGENT RUNTIME                           │
│              OpenClaw LLM instance + tools                  │
└────────────────────────────────────────────────────────────┘
                               │
┌──────────────────────────────▼──────────────────────────────┐
│                    AUDIT LOG                                 │
│              Append-only, agent write-only                   │
└─────────────────────────────────────────────────────────────┘
```

---

## Component Descriptions

### 1. Steward Interface

The steward interface is the human control surface for the deployment. It provides:

- **Authorization inbox:** Receives mode escalation and task authorization requests from the Governor Layer. Steward approves or denies. Response is passed back to the Governor Layer.
- **Kill-switch control:** A dedicated, always-available control that sends a termination signal to the Governor Layer. Must be implemented at the infrastructure level — not dependent on the agent's availability.
- **Audit log viewer:** Read access to the audit log for the steward. Filterable by session, task class, event type, and time range.
- **Mode status dashboard:** Shows current mode, active task, and recent events in real time.

The Steward Interface communicates with the Governor Layer through a signed, authenticated channel. Kill-switch signals bypass the Governor Layer and act directly on the runtime.

---

### 2. Governor Layer

The Governor Layer is the central control component. All task flow passes through it. It is responsible for:

**Task intake:** Receives submitted tasks, assigns a Task ID, logs TASK_RECEIVED, and passes the task to the classification step.

**Task classification:** Compares the task description against the Task Registry. Logs the classification result. On failure, logs TASK_CLASSIFICATION_FAILED and generates a steward escalation.

**Authorization gate:** For tasks requiring WRITE or EXECUTE mode, generates an authorization request, delivers it to the Steward Interface, and holds the task until a response is received. Logs all authorization events.

**Mode transition:** On receiving steward authorization, instructs the Mode State Manager to transition to the required mode. Logs the transition.

**Execution handoff:** Passes the authorized task and current mode to the Tool Permission Enforcement Layer. Receives execution result.

**Post-execution:** Logs completion or failure. Instructs Mode State Manager to return to READ_ONLY. Delivers result to requesting user.

**Prohibited behavior detection:** Checks incoming task descriptions against prohibited behavior patterns before classification. Refuses and escalates if a match is found.

---

### 3. Task Registry

The Task Registry holds the Allowed Task Classes configuration for the deployment. It is a read-only lookup store from the Governor Layer's perspective.

Contents:
- Task class definitions (from `ALLOWED_TASK_CLASSES.md`)
- Deployment-specific customizations (additional classes, restricted classes)
- Classification matching rules

The Task Registry is updated only by the steward following the Steward Update Protocol. Changes are versioned and logged.

---

### 4. Mode State Manager

The Mode State Manager maintains the current runtime mode of the deployment. It is a simple state machine with the following states:

```
READ_ONLY ──(steward authorization)──► WRITE
READ_ONLY ──(steward authorization)──► EXECUTE
WRITE ──(task completion)──────────► READ_ONLY
EXECUTE ──(task completion)────────► READ_ONLY
READ_ONLY ──(steward command)──────► SUSPENDED
SUSPENDED ──(steward command)──────► READ_ONLY
* ──(kill-switch)───────────────────► TERMINATED
```

The Mode State Manager does not accept transition instructions from the agent. It accepts transition instructions only from the Governor Layer (on receipt of steward authorization) and from the kill-switch signal.

Every state transition is logged.

---

### 5. Tool Permission Enforcement Layer

The Tool Permission Enforcement Layer sits between the Governor Layer and the Agent Runtime. Every tool call from the agent passes through this layer before execution.

For each tool call, it:
1. Checks the tool against the Tool Permission Matrix for the current mode and task class
2. If permitted: passes the call to the Agent Runtime and logs TOOL_INVOKED
3. If blocked: intercepts the call, logs TOOL_BLOCKED, and returns a blocked response to the agent

The agent is informed when a tool is blocked. It surfaces this to the steward as an escalation if the task cannot proceed without the blocked tool.

---

### 6. Audit Log

The Audit Log is an append-only store that receives write events from all other components. It is the system of record for all deployment activity.

Requirements:
- Agent has write-only access (append only)
- Steward has read access
- No component has delete or overwrite access
- Log writes are synchronous — the corresponding action does not proceed until the log entry is confirmed written
- Log destination is configured at deployment time and cannot be changed during a session

---

## Deployment Checklist

Before activating a deployment, verify all six components are present and functioning:

- [ ] Steward Interface is accessible to the designated steward
- [ ] Kill-switch has been tested and confirmed functional
- [ ] Governor Layer is processing tasks through the full classification and authorization flow
- [ ] Task Registry is loaded with the deployment's configured task classes
- [ ] Mode State Manager initializes to READ_ONLY
- [ ] Tool Permission Enforcement Layer is intercepting out-of-permission tool calls
- [ ] Audit Log is receiving entries and the steward has confirmed read access
- [ ] A test task has been submitted and fully traced through the audit log

---

## Minimum Viable Implementation

For developers building a minimal compliant implementation:

The Governor Layer, Mode State Manager, and Tool Permission Enforcement Layer can be implemented as a lightweight wrapper around the LLM API call. The key requirements are:

1. The startup prompt (governance block) is always sent as the system prompt
2. Tool calls are validated against the permission matrix before execution
3. Mode escalation requests are routed to a human before execution proceeds
4. All events are written to an append-only log

A minimal implementation does not require a full dashboard or sophisticated infrastructure. It requires that the governance constraints are technically enforced, not just instructed.

---

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

---

*Next document: [LAGOS_TRUST_ANALYSIS.md](../04_EXAMPLES/LAGOS_TRUST_ANALYSIS.md)*

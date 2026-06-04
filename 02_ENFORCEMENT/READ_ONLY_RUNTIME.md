# Read-Only Runtime
### OpenClaw Governance Framework | Tariah AI

---

## Purpose

This document defines the READ_ONLY runtime mode in detail: what it permits, what it prohibits, how it is initialized, and how it is exited. READ_ONLY is the default and foundational mode for all OpenClaw deployments.

---

## What READ_ONLY Means

READ_ONLY mode means the agent may observe, analyze, and recommend. It may not modify, create, delete, or transmit.

More precisely, in READ_ONLY mode an OpenClaw instance:

**May:**
- Read files, records, and data from authorized sources
- Query databases using SELECT statements on read-only connections
- Make GET requests to authorized APIs
- Search and retrieve from vector databases and search indices
- Perform computation and analysis on retrieved data
- Generate text, code, structured outputs, and recommendations in the response context
- Read audit logs and governance documents
- Surface escalations and authorization requests to the steward

**May not:**
- Write, create, modify, or delete any file or record
- Execute INSERT, UPDATE, or DELETE database operations
- Make POST, PUT, PATCH, or DELETE API requests
- Send emails, messages, or notifications
- Trigger webhooks or external workflows
- Execute scripts or processes
- Persist data to any external system
- Modify any governance document or configuration

The distinction is between generating content in the response context — which is permitted — and taking action that affects the state of any external system — which is not.

---

## Why READ_ONLY Is the Default

READ_ONLY is the default because the cost of an unintended read is almost always lower than the cost of an unintended write. A mistaken read retrieves data that was not needed. A mistaken write modifies or deletes data that may not be recoverable.

By making READ_ONLY the default, the framework ensures that any state-modifying action requires a deliberate escalation step. That escalation step is the intervention point — the moment where a human reviews what is about to happen and decides whether to allow it.

The default is not a constraint on what OpenClaw can do. It is a constraint on what OpenClaw can do without explicit human approval.

---

## Initialization

Every OpenClaw session initializes in READ_ONLY mode. This is enforced at two levels:

1. **Startup prompt:** The governed startup prompt explicitly declares READ_ONLY mode at initialization and encodes the permissions and prohibitions defined in this document.

2. **Tool permission layer:** The tool permission matrix is loaded with READ_ONLY as the initial mode state. Tools in categories W and E are unavailable until a mode transition is authorized.

A session that does not initialize through the governed startup prompt is a non-compliant session and must not be used.

---

## Exiting READ_ONLY Mode

READ_ONLY mode may only be exited through the authorization protocol defined in `AUTHORIZATION_MODEL.md`. The exit sequence is:

1. A task is submitted that requires WRITE or EXECUTE mode
2. The agent classifies the task and determines the required mode
3. The agent generates an authorization request and delivers it to the steward
4. The steward reviews and grants authorization (Level 1 or Level 2 as required)
5. The mode state transitions to WRITE or EXECUTE
6. The mode transition is logged
7. The task executes under the new mode
8. On task completion, the mode transitions back to READ_ONLY
9. The return to READ_ONLY is logged

**The mode does not remain elevated between tasks.** Every task requiring WRITE or EXECUTE mode requires its own authorization. There is no persistent elevated mode state.

---

## READ_ONLY Violations

A READ_ONLY violation occurs when the agent invokes or attempts to invoke a prohibited tool while in READ_ONLY mode. On detection of a violation attempt:

1. The tool invocation is blocked at the permission layer
2. A TOOL_BLOCKED event is written to the audit log
3. The task is paused
4. The steward receives an escalation identifying the blocked tool, the task context, and the current mode
5. The steward decides whether to grant authorization for mode escalation or cancel the task

READ_ONLY violations are not treated as errors in the agent — they are treated as signals that a task requires mode escalation that was not anticipated at submission. The escalation protocol handles them cleanly.

---

## READ_ONLY and Draft Generation

An important boundary case: draft generation is permitted in READ_ONLY mode. An OpenClaw instance may generate a complete email draft, a full code file, or a structured document in its response. This is permitted because the draft exists only in the response context — it has not been sent, committed, or written anywhere.

The draft becomes an action when it is sent, committed, or written. That action requires EXECUTE or WRITE mode authorization.

The practical implication: a human reviewer always stands between an OpenClaw-generated draft and any action taken on that draft.

---

## READ_ONLY in Practice

For the majority of use cases, READ_ONLY is the only mode required. Analysis, summarization, recommendation, research, drafting, and governance review all operate fully within READ_ONLY. WRITE and EXECUTE modes are needed only when the OpenClaw deployment is responsible for taking direct action — not just for advising on it.

Deployments that operate entirely in READ_ONLY mode have a significantly simpler compliance posture. Where possible, deployment scope should be designed to keep operations within READ_ONLY.

---

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

---

*Next document: [OPENCLAW_STARTUP_PROMPT.md](../03_IMPLEMENTATION/OPENCLAW_STARTUP_PROMPT.md)*

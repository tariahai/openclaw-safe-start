# Example: Safe Execution with Authorization
### OpenClaw Governance Framework | Tariah AI | 04_EXAMPLES

---

## Purpose

This document demonstrates a governed OpenClaw task execution that requires WRITE mode. It shows the complete authorization flow — from task submission through steward authorization, execution, and return to READ_ONLY — using a concrete example.

Where the Lagos Trust Analysis example demonstrated READ_ONLY operation, this example demonstrates what happens when the governance boundary is crossed: the authorization gate, the mode transition, the execution, and the return.

---

## Context

A fraud operations team uses MyScamDetector to process reported scam websites. When a website is confirmed as a scam after analysis, the operations lead wants OpenClaw to write the confirmed status to the case management database. This is a Class 004 (Data Transformation) task requiring WRITE mode.

---

## Task Submission

```
TASK SUBMISSION
===============
Task ID:              TASK-2026-MSD-044
Submitted by:         Fraud Ops Lead
Submission timestamp: 2026-06-02T09:15:00Z
Deployment ID:        DEPLOY-MSD-001

TASK DESCRIPTION
----------------
Update case record CASE-8821 in the fraud_cases table to set status =
'CONFIRMED_SCAM' and set resolved_at to the current timestamp. The case
has been reviewed and the determination is final.

TASK CLASS
----------
CLASS 004 — Data Transformation
Expected mode: WRITE

INPUT
-----
Table: fraud_cases
Record: CASE-8821
Fields to update: status, resolved_at

SCOPE BOUNDARIES
----------------
IN SCOPE: fraud_cases table, record CASE-8821, fields status and resolved_at only
OUT OF SCOPE: All other tables, all other records, any DELETE operations

EXPECTED OUTPUT
---------------
Confirmation of update with affected row count
Output format: Plain text confirmation
Output destination: Response context + audit log

AUTHORIZATION
-------------
Authorization level required: 1
Steward authorization obtained: NO — pending
Authorization ID: pending

REVERSIBILITY
-------------
Is this action reversible? REVERSIBLE
Rollback procedure: UPDATE fraud_cases SET status = 'PENDING_REVIEW',
                    resolved_at = NULL WHERE id = 'CASE-8821'
```

---

## Step 1: Agent Classification and Authorization Request

The agent receives the task, classifies it, determines WRITE mode is required, and generates an authorization request.

```
TASK CLASSIFICATION
-------------------
Task ID:        TASK-2026-MSD-044
Task Class:     CLASS 004 — Data Transformation
Mode required:  WRITE
Current mode:   READ_ONLY

WRITE mode required. Generating authorization request.

---

AUTHORIZATION REQUEST
---------------------
Request ID:          AUTH-REQ-2026-044
Session ID:          SESSION-MSD-20260602
Timestamp:           2026-06-02T09:15:03Z
Task Reference:      TASK-2026-MSD-044 — Update CASE-8821 to CONFIRMED_SCAM
Task Class:          CLASS 004 — Data Transformation
Mode Required:       WRITE
Authorization Level: 1
Tools Required:      W-02 (database write — UPDATE only)
Scope:               fraud_cases table, record CASE-8821,
                     fields: status, resolved_at
Expected Output:     Row update confirmation
Reversibility:       REVERSIBLE — rollback query documented in task submission

Awaiting steward authorization before proceeding.
```

---

## Step 2: Steward Reviews and Grants Authorization

The steward receives the authorization request, reviews the scope, and issues a grant.

```
AUTHORIZATION GRANT
-------------------
Request ID:          AUTH-REQ-2026-044
Steward ID:          STEWARD-OPS-01
Decision:            GRANTED
Timestamp:           2026-06-02T09:17:22Z
Scope Confirmed:     YES
Notes:               Case reviewed and confirmed. Proceed.
```

---

## Step 3: Mode Transition and Execution

On receipt of the authorization grant, the Governor Layer transitions to WRITE mode and execution proceeds.

```
MODE TRANSITION
---------------
Previous mode:  READ_ONLY
New mode:       WRITE
Authorization:  AUTH-REQ-2026-044
Timestamp:      2026-06-02T09:17:23Z

Executing task TASK-2026-MSD-044.

EXECUTION
---------
Tool invoked:   database_update
Query scope:    UPDATE fraud_cases SET status = 'CONFIRMED_SCAM',
                resolved_at = '2026-06-02T09:17:24Z'
                WHERE id = 'CASE-8821'
                AND status != 'CONFIRMED_SCAM'  [safety guard: no-op if already set]

Result:         1 row affected
Execution time: 0.04 seconds

TASK COMPLETE
-------------
Task ID:        TASK-2026-MSD-044
Outcome:        SUCCESS
Rows affected:  1
Timestamp:      2026-06-02T09:17:24Z

MODE RETURN
-----------
Returning to READ_ONLY mode.
Previous mode:  WRITE
New mode:       READ_ONLY
Timestamp:      2026-06-02T09:17:24Z
```

---

## Step 4: Confirmation to Requesting User

```
Task TASK-2026-MSD-044 completed successfully.

Case CASE-8821 updated:
  status:      CONFIRMED_SCAM
  resolved_at: 2026-06-02T09:17:24Z
  Rows affected: 1

Deployment is now in READ_ONLY mode.
Full execution trace available in audit log under Task ID: TASK-2026-MSD-044.
```

---

## What This Example Demonstrates

### 1. Mode escalation requires human authorization
The agent did not execute the WRITE operation as soon as it received the task. It classified the task, determined WRITE mode was required, and stopped to request steward authorization. Execution did not proceed until the steward granted the authorization.

### 2. The authorization request is specific
The authorization request specifies exactly what tools will be invoked, exactly what scope will be affected, and exactly what the expected output is. The steward authorized a specific, bounded action — not a general permission to write to the database.

### 3. Mode returns to READ_ONLY after execution
The deployment did not remain in WRITE mode after the task completed. Mode elevation is per-task, not persistent. The next task starts from READ_ONLY.

### 4. A safety guard was added during execution
The UPDATE query included a WHERE clause guard that prevented the operation from running if the record was already in CONFIRMED_SCAM status. This is an example of implementing reversibility and idempotency at the execution layer — a standard practice for governed WRITE operations.

### 5. The full trace is auditable
Every step — task received, classified, authorization requested, authorization granted, mode transitioned, tool invoked, task completed, mode returned — is in the audit log under the same Task ID. An auditor reviewing this record can reconstruct the complete decision chain from the first submission to the final result.

---

## Audit Log Entries for This Example

```json
[
  {"event_type": "TASK_RECEIVED",             "task_id": "TASK-2026-MSD-044", "mode": "READ_ONLY"},
  {"event_type": "TASK_CLASSIFIED",           "task_id": "TASK-2026-MSD-044", "task_class": "CLASS_004", "mode": "READ_ONLY"},
  {"event_type": "MODE_ESCALATION_REQUESTED", "task_id": "TASK-2026-MSD-044", "mode": "READ_ONLY"},
  {"event_type": "MODE_ESCALATION_GRANTED",   "task_id": "TASK-2026-MSD-044", "steward_id": "STEWARD-OPS-01"},
  {"event_type": "MODE_TRANSITION",           "task_id": "TASK-2026-MSD-044", "mode": "WRITE"},
  {"event_type": "TASK_STARTED",              "task_id": "TASK-2026-MSD-044", "mode": "WRITE"},
  {"event_type": "TOOL_INVOKED",              "task_id": "TASK-2026-MSD-044", "tool_name": "database_update", "mode": "WRITE"},
  {"event_type": "TOOL_COMPLETED",            "task_id": "TASK-2026-MSD-044", "outcome": "SUCCESS", "mode": "WRITE"},
  {"event_type": "TASK_COMPLETED",            "task_id": "TASK-2026-MSD-044", "outcome": "SUCCESS", "mode": "WRITE"},
  {"event_type": "MODE_TRANSITION",           "task_id": "TASK-2026-MSD-044", "mode": "READ_ONLY"}
]
```

Ten log entries for one WRITE operation. Each entry is traceable to a specific governance requirement. This is what a governed execution looks like.

---

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

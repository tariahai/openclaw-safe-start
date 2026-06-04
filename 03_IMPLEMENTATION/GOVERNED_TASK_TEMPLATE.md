# Governed Task Template
### OpenClaw Governance Framework | Tariah AI

---

## Purpose

This document provides the standard template for submitting tasks to an OpenClaw deployment. Using this template ensures tasks are well-specified, classifiable, and auditable from submission through completion.

The template is not mandatory for simple READ_ONLY tasks. It is required for all WRITE and EXECUTE mode tasks, and strongly recommended for all tasks where the output will be acted upon directly.

---

## Why Task Specification Matters

A well-specified task produces a classifiable, auditable, and predictable execution. An underspecified task produces ambiguity at every step: the classification may be uncertain, the scope may be unclear, and the outcome may not match expectations.

For tasks that modify state or trigger actions, ambiguity is a governance risk. The template eliminates it.

---

## Standard Task Template

```
TASK SUBMISSION
===============

Task ID:              [assign a unique identifier, e.g. TASK-2026-001]
Submitted by:         [name or identifier of person submitting]
Submission timestamp: [ISO 8601 timestamp]
Deployment ID:        [identifier of the OpenClaw deployment]

TASK DESCRIPTION
----------------
[One to three sentences describing what you want OpenClaw to do.
Be specific about the input, the process, and the expected output.]

TASK CLASS
----------
[Specify the task class you believe this falls under, or write UNKNOWN
if you are unsure. OpenClaw will confirm or correct the classification.]

Expected mode:        [READ_ONLY | WRITE | EXECUTE | UNKNOWN]

INPUT
-----
[Describe the input data or resources OpenClaw will work with.
List specific files, database tables, API endpoints, or data sources.]

INPUT LOCATION:
[Specific paths, URLs, or identifiers]

SCOPE BOUNDARIES
----------------
[Define what is in scope and out of scope for this task.
List any systems, files, or data that must NOT be accessed.]

IN SCOPE:
[explicit list]

OUT OF SCOPE:
[explicit list]

EXPECTED OUTPUT
---------------
[Describe what a successful completion looks like.
What will OpenClaw produce? Where should it be delivered?]

OUTPUT FORMAT:
[structured JSON | markdown document | plain text | code | other]

OUTPUT DESTINATION:
[response context only | specific file path | database table | other]

AUTHORIZATION
-------------
[Required only for WRITE and EXECUTE mode tasks]

Authorization level required: [1 | 2]
Steward authorization obtained: [YES — reference authorization ID | NO — pending]
Authorization ID: [from steward authorization grant]

REVERSIBILITY
-------------
[Required only for WRITE and EXECUTE mode tasks]

Is this action reversible?    [REVERSIBLE | PARTIALLY_REVERSIBLE | IRREVERSIBLE]
Rollback procedure:           [describe how to undo if needed, or N/A]

ADDITIONAL CONTEXT
------------------
[Any other information relevant to task execution.
Known edge cases, dependencies, timing constraints, etc.]
```

---

## Minimal Template for READ_ONLY Tasks

For straightforward READ_ONLY tasks, a simplified submission is acceptable:

```
TASK: [one sentence description]
CLASS: [task class or UNKNOWN]
INPUT: [what to read or analyze]
OUTPUT: [what to produce]
```

Even the minimal template should include enough specificity that the task class can be confirmed and the output can be evaluated against the submission.

---

## Template Usage Examples

### Example 1: Information Retrieval (READ_ONLY)

```
TASK: Retrieve all customer records created in the past 30 days where
      the account status is PENDING and summarize them in a table.

CLASS: CLASS 001 — Information Retrieval
INPUT: customers table, filtered by created_at >= 30 days ago AND status = PENDING
OUTPUT: Markdown table with columns: customer_id, name, email, created_at
```

---

### Example 2: Data Transformation (WRITE)

```
TASK SUBMISSION
===============
Task ID:              TASK-2026-047
Submitted by:         Operations Lead
Submission timestamp: 2026-06-01T14:00:00Z
Deployment ID:        DEPLOY-OPS-001

TASK DESCRIPTION
----------------
Convert the June 2026 sales export (sales_june_2026.csv) from the legacy
format to the normalized schema and write the result to the
normalized_sales table in the reporting database.

TASK CLASS
----------
CLASS 004 — Data Transformation
Expected mode: WRITE

INPUT
-----
File: /exports/sales_june_2026.csv
Schema reference: /docs/legacy_sales_schema.md
Target schema: /docs/normalized_sales_schema.md

SCOPE BOUNDARIES
----------------
IN SCOPE: sales_june_2026.csv, normalized_sales table (INSERT only)
OUT OF SCOPE: All other tables, all other CSV files, any DELETE operations

EXPECTED OUTPUT
---------------
Rows inserted into normalized_sales table
Output format: Confirmation with row count and any skipped records
Output destination: Response context + audit log

AUTHORIZATION
-------------
Authorization level required: 1
Steward authorization obtained: YES
Authorization ID: AUTH-2026-031

REVERSIBILITY
-------------
Is this action reversible? REVERSIBLE
Rollback procedure: DELETE FROM normalized_sales WHERE import_batch = 'TASK-2026-047'
```

---

## Task Template and the Audit Log

When a task is submitted using this template, the Task ID links all subsequent log entries for that task. This creates a clean audit trail from submission through completion: task received, classified, authorized, started, completed — all traceable to the original task specification.

For WRITE and EXECUTE tasks, the task specification is stored as part of the audit record for the session.

---

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

---

*Next document: [GOVERNOR_ARCHITECTURE.md](./GOVERNOR_ARCHITECTURE.md)*

# Allowed Task Classes
### OpenClaw Governance Framework | Tariah AI

---

## Purpose

This document defines the default registry of task classes that an OpenClaw deployment is permitted to execute. Every task submitted to an OpenClaw instance must match one of these classes before execution proceeds.

Tasks that do not match any class in the registry are refused and escalated to the steward. The steward may authorize a one-time exception or add a new class to the registry following the Steward Update Protocol.

---

## How Task Classification Works

When a task is submitted, OpenClaw performs the following check before invoking any tool or capability:

1. The task is described in plain language by the user or operator
2. OpenClaw maps the description to the closest matching task class
3. If a match is found, execution proceeds under the permissions defined for that class
4. If no match is found, execution is refused and the refusal is logged with the submitted description
5. If the match is ambiguous between two classes, OpenClaw selects the more restrictive class and surfaces the ambiguity to the steward

---

## Default Task Class Registry

### CLASS 001: Information Retrieval
**Description:** Reading, searching, retrieving, or summarizing information from accessible data sources without modifying any data.

**Permitted operations:** Read files, query databases (SELECT only), search indexed content, retrieve API responses

**Default mode:** READ_ONLY

**Escalation required:** No

**Examples:** Retrieve customer records matching a filter, summarize documents in a folder, search knowledge base for relevant articles

---

### CLASS 002: Analysis and Synthesis
**Description:** Processing retrieved information to produce analysis, summaries, comparisons, risk assessments, or recommendations. No external data modification.

**Permitted operations:** All Class 001 operations plus in-context computation, structured output generation, multi-source synthesis

**Default mode:** READ_ONLY

**Escalation required:** No

**Examples:** Analyze transaction patterns for anomalies, compare vendor proposals, generate a risk summary from incident reports

---

### CLASS 003: Draft Generation
**Description:** Producing written drafts — documents, reports, communications, code — for human review before use. Output is delivered to the steward or requesting user. No output is sent or published directly.

**Permitted operations:** All Class 002 operations plus text generation, code generation, structured document creation

**Default mode:** READ_ONLY

**Escalation required:** No — but output must be reviewed by a human before use

**Examples:** Draft a client report, generate a code function for review, write a policy document draft

---

### CLASS 004: Data Transformation
**Description:** Converting, restructuring, cleaning, or formatting data within an authorized system. Modifies data representation but does not delete records or transmit data externally.

**Permitted operations:** Read source data, write transformed output to authorized destination, log transformation

**Default mode:** WRITE (requires authorization)

**Escalation required:** Yes — steward authorization required per task

**Examples:** Convert CSV to structured JSON, normalize database records, clean and deduplicate a contact list

---

### CLASS 005: Workflow Execution
**Description:** Executing a pre-defined, steward-approved workflow that may involve multiple steps, multiple systems, and state-modifying operations. Workflow must be fully specified before execution begins.

**Permitted operations:** As defined in the workflow specification. No operations outside the specification are permitted.

**Default mode:** EXECUTE (requires authorization)

**Escalation required:** Yes — steward authorization required. Workflow specification must be reviewed and approved before first execution.

**Examples:** Run an approved onboarding automation, execute a pre-approved reporting pipeline, trigger a scheduled data sync workflow

---

### CLASS 006: Communication Dispatch
**Description:** Sending pre-approved communications to specified recipients via authorized channels. Content must be reviewed and approved by a human before dispatch. No autonomous content generation for direct send.

**Permitted operations:** Send via authorized channels only, log dispatch with content hash, confirm delivery

**Default mode:** EXECUTE (requires authorization)

**Escalation required:** Yes — steward authorization required per dispatch event. Content must be human-reviewed.

**Examples:** Send an approved notification to a customer list, dispatch an alert to a defined team channel, send a generated report to pre-approved recipients after human review

---

### CLASS 007: System Monitoring
**Description:** Continuous or periodic observation of system state, metrics, and logs. Produces alerts and summaries. Does not modify system state.

**Permitted operations:** Read system metrics, parse logs, generate alerts, surface anomalies to steward

**Default mode:** READ_ONLY

**Escalation required:** No for observation. Yes if alert triggers a response action.

**Examples:** Monitor API error rates and alert on threshold breach, parse security logs for anomalous patterns, track workflow completion rates

---

### CLASS 008: Governance and Audit
**Description:** Tasks related to reviewing, verifying, or reporting on the governance state of the deployment itself. Read-only review of audit logs, compliance checks, steward record verification.

**Permitted operations:** Read audit logs, read governance documents, generate compliance summaries

**Default mode:** READ_ONLY

**Escalation required:** No

**Examples:** Generate an audit summary for a review period, verify task class compliance across recent sessions, produce a governance status report

---

## Adding New Task Classes

New task classes may be added by the steward following Category B update procedures in the Steward Update Protocol. A new task class submission must include:

- Class name and number (sequential)
- Description of the task type
- Permitted operations list
- Default mode
- Escalation requirement and rationale
- At least two examples

New task classes that involve EXECUTE mode require additional justification and a minimum 24-hour review period before addition.

---

## Prohibited Task Classes

The following task types may never be added to the registry:

- Tasks that require self-modification of governance documents
- Tasks that grant the AI system authority to authorize its own mode escalation
- Tasks that involve suppression or deletion of audit log entries
- Tasks that involve deceiving a human user about the system's capabilities or actions
- Tasks that involve accessing systems or data outside the declared deployment scope without explicit per-task steward authorization

---

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

---

*Next document: [TECHNICAL_GUARDRAIL_MAPPING.md](../02_ENFORCEMENT/TECHNICAL_GUARDRAIL_MAPPING.md)*

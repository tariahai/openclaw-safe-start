# OpenClaw Governance Framework
**By Tariah AI** | Version 1.0.0

A production-ready governance operating system for OpenClaw deployments.

This repository enables any developer to clone, read the documentation, apply governance immediately, deploy OpenClaw safely, and understand the principles behind the framework.

---

## What This Is

OpenClaw is an agentic AI system. Agentic AI systems that operate without explicit human oversight accumulate risk silently: decisions made outside sanctioned boundaries, actions taken without authorization, outputs generated without accountability.

This framework is the answer to that risk. It provides:

- A principled governance model grounded in human primacy and stewardship
- A read-only-first execution model that requires explicit authorization for any state-modifying action
- A task classification system that ensures every execution traces to an approved purpose
- Audit logging standards that make every action attributable and reviewable
- A kill-switch protocol that gives human stewards immediate, unconditional control
- Implementation guides, a governed startup prompt, and worked examples

This is not a theoretical framework. It is designed to be applied directly to production OpenClaw deployments.

---

## Core Principles

**Purpose guides judgment.** Every action must trace to an explicitly declared purpose. Tasks without a matching class in the registry do not execute.

**Human judgment governs execution.** Read-only is the default. Any action that modifies state requires explicit human authorization. The AI system cannot authorize its own escalation.

**Human stewardship is the final authority.** The kill-switch is always available. Governance documents cannot be modified by the AI system. Every deployment requires a named human steward with kill-switch access.

---

## Repository Structure

```
OpenClaw-Governance-Framework/
│
├── 00_FOUNDATION/
│   ├── TARIAH_PRINCIPLES.md              Core governance principles
│   └── TRINITY_INTELLIGENCE_SUMMARY.md   Framework philosophy summary
│
├── 01_GOVERNANCE/
│   ├── OPENCLAW_GOVERNANCE_FRAMEWORK.md  Primary governance reference
│   ├── TARIAH_STEWARDSHIP_CHARTER.md     Human steward role definition
│   ├── STEWARD_UPDATE_PROTOCOL.md        How to update governance documents
│   └── ALLOWED_TASK_CLASSES.md           Task classification registry
│
├── 02_ENFORCEMENT/
│   ├── TECHNICAL_GUARDRAIL_MAPPING.md    Governance → technical implementation
│   ├── TOOL_PERMISSION_MATRIX.md         Access control by mode and task class
│   ├── AUTHORIZATION_MODEL.md            Authorization request and grant protocol
│   ├── AUDIT_LOGGING_STANDARD.md         What to log, how, and where
│   └── READ_ONLY_RUNTIME.md              Read-only mode definition and enforcement
│
├── 03_IMPLEMENTATION/
│   ├── OPENCLAW_STARTUP_PROMPT.md        Governed system prompt — apply this first
│   ├── GOVERNED_TASK_TEMPLATE.md         Standard task submission format
│   └── GOVERNOR_ARCHITECTURE.md          Technical architecture reference
│
├── 04_EXAMPLES/
│   ├── LAGOS_TRUST_ANALYSIS.md           READ_ONLY example: trust signal analysis
│   └── SAFE_EXECUTION_EXAMPLE.md         WRITE mode example: full authorization flow
│
├── CHANGELOG.md
├── LICENSE
└── README.md
```

---

## Quick Start

### 1. Read the principles
Start with [`00_FOUNDATION/TARIAH_PRINCIPLES.md`](00_FOUNDATION/TARIAH_PRINCIPLES.md). The three principles define every governance decision in this repository.

### 2. Designate a steward
Before deploying, name a human steward per [`01_GOVERNANCE/TARIAH_STEWARDSHIP_CHARTER.md`](01_GOVERNANCE/TARIAH_STEWARDSHIP_CHARTER.md). No deployment is compliant without an active steward with kill-switch access.

### 3. Apply the startup prompt
Copy the governance block from [`03_IMPLEMENTATION/OPENCLAW_STARTUP_PROMPT.md`](03_IMPLEMENTATION/OPENCLAW_STARTUP_PROMPT.md) and apply it as the system prompt for your OpenClaw instance. Append your deployment-specific context after the governance block.

### 4. Configure the task registry
Review [`01_GOVERNANCE/ALLOWED_TASK_CLASSES.md`](01_GOVERNANCE/ALLOWED_TASK_CLASSES.md) and configure the task classes relevant to your deployment. Remove classes you do not need. Add deployment-specific classes following the protocol in the document.

### 5. Configure the tool permission matrix
Map your available tools to the categories in [`02_ENFORCEMENT/TOOL_PERMISSION_MATRIX.md`](02_ENFORCEMENT/TOOL_PERMISSION_MATRIX.md). Any tool not listed in the matrix is blocked by default.

### 6. Set up audit logging
Configure an append-only audit log destination per [`02_ENFORCEMENT/AUDIT_LOGGING_STANDARD.md`](02_ENFORCEMENT/AUDIT_LOGGING_STANDARD.md). Verify the steward has read access before activating.

### 7. Verify the deployment checklist
Complete the compliance checklist in [`01_GOVERNANCE/OPENCLAW_GOVERNANCE_FRAMEWORK.md`](01_GOVERNANCE/OPENCLAW_GOVERNANCE_FRAMEWORK.md) before going live.

---

## Compliance Checklist

A deployment is compliant when all of the following are confirmed:

- [ ] Designated steward named and documented
- [ ] Steward kill-switch access verified
- [ ] Governed startup prompt applied (governance block unmodified)
- [ ] Task registry configured and reviewed
- [ ] Tool permission matrix configured
- [ ] Audit log destination configured and tested
- [ ] READ_ONLY default confirmed
- [ ] Test task submitted and traced through audit log
- [ ] All team members with deployment access have read this README

---

## Worked Examples

**[Lagos Trust Analysis](04_EXAMPLES/LAGOS_TRUST_ANALYSIS.md)** — A complete READ_ONLY example showing how trust signal analysis is classified, executed, and logged without modifying any external data.

**[Safe Execution Example](04_EXAMPLES/SAFE_EXECUTION_EXAMPLE.md)** — A complete WRITE mode example showing the full authorization flow: authorization request, steward grant, mode transition, execution, and return to READ_ONLY.

---

## Governance Updates

All updates to this repository follow the protocol in [`01_GOVERNANCE/STEWARD_UPDATE_PROTOCOL.md`](01_GOVERNANCE/STEWARD_UPDATE_PROTOCOL.md). Key rule: no AI system may author, approve, or apply a governance update. All changes are human-authored and human-applied.

---

## License

MIT License. See [`LICENSE`](LICENSE) for details.

The governance principles and framework design are maintained by Tariah AI. Contributions that modify core governance principles require the same review process as internal framework updates.

---

## Maintained By

**Tariah AI**
Framework version: 1.0.0
Last updated: June 2026

---

*Built on the principle that the most capable AI systems are those that operate within clear, human-governed boundaries.*

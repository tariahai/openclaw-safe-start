# Tariah Principles
### OpenClaw Governance Framework | Tariah AI

---

## Overview

The Tariah Principles define the foundational commitments that govern all OpenClaw deployments. They are not aspirational statements. They are operational constraints. Every technical decision, permission boundary, and escalation pathway in this framework derives from one or more of these principles.

These principles exist because autonomous AI systems operating without explicit human oversight create risks that accumulate silently: decisions made outside sanctioned boundaries, actions taken without authorization, outputs generated without accountability. The Tariah Principles are the answer to that risk.

---

## The Three Core Principles

### Principle 1: Purpose Guides Judgment

Every action taken by an OpenClaw deployment must be traceable to an explicitly defined purpose. Purpose is not assumed. Purpose is declared by a human steward before execution begins, encoded in the task specification, and verified against the Allowed Task Classes before any tool or capability is invoked.

An OpenClaw instance that cannot identify a declared purpose for a requested action must refuse that action and escalate to its designated steward.

**Operational implication:** No task may be executed without a matching entry in the Allowed Task Classes registry. If the task class does not exist, the task does not proceed.

---

### Principle 2: Human Judgment Governs Execution

AI systems process information and generate outputs. Humans decide what to do with those outputs. This boundary is non-negotiable.

OpenClaw operates in read-only mode by default. Any action that modifies state, writes data, sends communications, or affects external systems requires explicit human authorization issued through the authorization protocol. Authorization is logged. Authorization can be revoked. No authorization chain may be constructed by the AI system itself.

An OpenClaw instance may recommend. It may analyze. It may draft. It may not act on its own judgment when that action crosses the read-only boundary.

**Operational implication:** The default runtime is READ_ONLY. Escalation to WRITE or EXECUTE modes requires documented human authorization. The AI system cannot self-authorize escalation.

---

### Principle 3: Human Stewardship Remains the Final Authority

There is no automated process, no sufficiently high confidence score, and no time constraint that overrides human stewardship. When a human steward says stop, the system stops. When a human steward says do not proceed, the system does not proceed.

The kill-switch is always available. The audit log is always accessible. The steward always has the right to inspect, override, and terminate any OpenClaw process at any time without providing justification to the system.

This principle also governs updates to the framework itself. No governance document in this repository may be modified by an AI system acting autonomously. Changes to governance require human authorship, human review, and a documented update record per the Steward Update Protocol.

**Operational implication:** Every OpenClaw deployment must have a designated human steward with kill-switch access. A deployment without an active steward is a non-compliant deployment.

---

## What These Principles Are Not

**They are not performance constraints.** Compliance with these principles does not require sacrificing capability. A well-governed OpenClaw deployment can be both powerful and safe.

**They are not temporary.** These principles do not expire when the system becomes more capable. Increased capability increases, not decreases, the importance of maintaining these boundaries.

**They are not suggestions.** Technical guardrails in this framework enforce these principles at the system level. The principles are not satisfied by agreement alone — they are verified by implementation.

---

## Relationship to the Trinity Intelligence Framework

The Tariah Principles draw from the Trinity Intelligence Framework developed by Tariah AI, specifically the following operational extractions:

| Trinity Concept | Operational Expression in This Framework |
|---|---|
| Purpose guides judgment | Declared task classification before execution |
| Human judgment governs execution | Read-only default with explicit authorization gates |
| AI serves human goals | Tool permission matrix scoped to declared purpose |
| Human stewardship is final authority | Kill-switch controls and steward override at all levels |

The full Trinity Intelligence Framework is documented separately. This repository implements the governance-relevant subset only.

---

## Versioning

These principles are versioned alongside the framework. Any proposed change to a core principle requires:

1. A documented rationale authored by a human steward
2. Review against the existing Steward Update Protocol
3. A version increment in this document
4. An entry in the governance changelog

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

---

*Next document: [TRINITY_INTELLIGENCE_SUMMARY.md](./TRINITY_INTELLIGENCE_SUMMARY.md)*

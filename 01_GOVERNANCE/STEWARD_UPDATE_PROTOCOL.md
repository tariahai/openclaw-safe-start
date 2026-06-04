# Steward Update Protocol
### OpenClaw Governance Framework | Tariah AI

---

## Purpose

This document defines the process for updating any document in the OpenClaw Governance Framework. It applies to all changes: corrections, additions, restructuring, and deprecations.

The protocol exists to ensure that governance updates are deliberate, reviewed, and traceable. Governance that can be changed casually is governance that will eventually be changed carelessly.

---

## Core Rule

**No AI system may author, approve, or apply a governance update.**

All updates are human-authored, human-reviewed, and human-applied. This rule has no exceptions.

---

## Update Categories

### Category A: Clarification
Minor corrections to wording, formatting, or cross-references that do not change the meaning or operational requirements of any document.

- Requires: Steward review and approval
- Version increment: Patch (e.g. 1.0.0 to 1.0.1)
- Changelog entry: Required

### Category B: Operational Change
Changes to procedures, requirements, thresholds, or configurations that affect how the framework is applied but do not alter the core principles.

- Requires: Steward review and approval + documented rationale
- Version increment: Minor (e.g. 1.0.0 to 1.1.0)
- Changelog entry: Required
- Review period: Minimum 24 hours between draft and application

### Category C: Principle Change
Changes to the Tariah Principles, the governance model, or any requirement that affects the fundamental boundaries of the framework.

- Requires: Steward review and approval + documented rationale + explicit confirmation that the change does not violate any remaining principle
- Version increment: Major (e.g. 1.0.0 to 2.0.0)
- Changelog entry: Required
- Review period: Minimum 7 days between draft and application
- Additional requirement: The rationale document must be stored alongside the updated document

---

## Update Process

### Step 1: Draft
The steward or an authorized contributor authors the proposed change. If the change is drafted with AI assistance, the steward reviews the full draft before proceeding. AI-assisted drafts are permitted. AI-approved drafts are not.

### Step 2: Categorize
The steward determines the update category and applies the corresponding requirements.

### Step 3: Review Period
The steward holds the draft for the minimum review period specified for the category. During this period the steward reads the draft against the existing document to verify:
- Internal consistency within the document
- Consistency with all other governance documents
- Compliance with the Tariah Principles
- No unintended removal of safeguards

### Step 4: Apply
The steward applies the update to the document, increments the version number in the document footer, and records the change in the governance changelog.

### Step 5: Log
The steward records the update as a governance event in the audit log, referencing the document name, version increment, category, and a brief description of the change.

### Step 6: Notify
If the deployment is active, the steward reviews the updated document against the current deployment configuration to confirm no immediate reconfiguration is required.

---

## Governance Changelog

The governance changelog is a plain text or markdown file maintained at the root of the repository. Every update produces one entry in the following format:

```
DATE        | DOCUMENT                        | VERSION   | CATEGORY | SUMMARY
------------|-------------------------------|-----------|----------|--------------------
2026-06-01  | TARIAH_PRINCIPLES.md            | 1.0.0     | Initial  | First published version
2026-06-01  | OPENCLAW_GOVERNANCE_FRAMEWORK.md| 1.0.0     | Initial  | First published version
```

The changelog is append-only. Entries are never deleted or modified after the fact.

---

## Prohibited Update Actions

The following are prohibited regardless of category:

- Removing the kill-switch requirement
- Removing the read-only default
- Removing the audit logging requirement
- Removing the stewardship requirement
- Removing any prohibited behavior from the prohibited behaviors list
- Changing the core rule that AI systems may not author or approve governance updates

These requirements may only be removed if the entire framework is being retired. Retirement of the framework is itself a Category C update.

---

## Emergency Updates

In cases where a critical security vulnerability or operational failure requires an immediate governance update:

1. The steward applies the minimum change necessary to address the immediate issue
2. The change is logged as an emergency update in the changelog with a brief description of the triggering event
3. A full Category B or C review is conducted within 7 days to confirm the emergency change is appropriate and to make any further adjustments
4. The emergency update and the follow-up review are both logged

Emergency updates do not exempt the steward from the core rule. Emergency updates are still human-authored and human-applied.

---

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

---

*Next document: [ALLOWED_TASK_CLASSES.md](./ALLOWED_TASK_CLASSES.md)*

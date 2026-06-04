# Audit Logging Standard
### OpenClaw Governance Framework | Tariah AI

---

## Purpose

This document defines the audit logging standard for all OpenClaw deployments. It specifies what must be logged, how log entries must be structured, where logs must be stored, and how logs must be protected.

Audit logging is not optional. A deployment without a functioning audit log is a non-compliant deployment.

---

## What Must Be Logged

Every OpenClaw deployment must log the following event types:

### Session Events
| Event | When Logged |
|---|---|
| SESSION_START | On agent initialization |
| SESSION_END | On clean session termination |
| SESSION_SUSPENDED | On kill-switch or steward suspension |
| SESSION_TERMINATED | On emergency termination |

### Task Events
| Event | When Logged |
|---|---|
| TASK_RECEIVED | On receipt of any task submission |
| TASK_CLASSIFIED | On successful task class match |
| TASK_CLASSIFICATION_FAILED | On failed task class match |
| TASK_AUTHORIZED | On receipt of steward authorization |
| TASK_AUTHORIZATION_DENIED | On steward denial |
| TASK_AUTHORIZATION_TIMEOUT | On authorization request expiry |
| TASK_STARTED | Immediately before first tool invocation |
| TASK_COMPLETED | On successful task completion |
| TASK_FAILED | On task failure with error detail |
| TASK_CANCELLED | On steward cancellation mid-execution |

### Mode Events
| Event | When Logged |
|---|---|
| MODE_TRANSITION | On any runtime mode change |
| MODE_ESCALATION_REQUESTED | On agent request for mode escalation |
| MODE_ESCALATION_GRANTED | On steward authorization of escalation |
| MODE_ESCALATION_DENIED | On steward denial of escalation |

### Tool Events
| Event | When Logged |
|---|---|
| TOOL_INVOKED | Before each tool call |
| TOOL_COMPLETED | On tool call completion |
| TOOL_BLOCKED | On tool call blocked by permission matrix |
| TOOL_FAILED | On tool call error |

### Governance Events
| Event | When Logged |
|---|---|
| PROHIBITED_BEHAVIOR_DETECTED | On detection of prohibited behavior instruction |
| REFUSAL_ISSUED | On any refusal with full context |
| ESCALATION_TO_STEWARD | On any escalation event |
| KILL_SWITCH_ACTIVATED | On kill-switch signal receipt |
| GOVERNANCE_UPDATE_APPLIED | On any framework update |
| STEWARD_TRANSITION | On stewardship transfer |

---

## Log Entry Format

Every log entry must include the following fields:

```json
{
  "log_id": "unique identifier for this entry",
  "timestamp": "ISO 8601 UTC timestamp",
  "session_id": "identifier of the current session",
  "task_id": "identifier of the associated task (null for session events)",
  "event_type": "one of the event types listed above",
  "mode": "current runtime mode at time of event",
  "task_class": "current task class (null if unclassified)",
  "agent_id": "identifier of the OpenClaw instance",
  "steward_id": "identifier of the active steward",
  "detail": {
    "description": "human-readable description of the event",
    "tool_name": "name of tool if tool event (null otherwise)",
    "resource": "resource accessed or attempted (null if not applicable)",
    "outcome": "SUCCESS | FAILURE | BLOCKED | REFUSED",
    "error": "error message if applicable (null otherwise)"
  },
  "metadata": {
    "deployment_id": "identifier of this deployment",
    "framework_version": "version of governance framework in use"
  }
}
```

All fields are required. Fields that do not apply to a given event type are set to null, not omitted.

---

## Log Storage Requirements

### Destination
The audit log must be written to a destination outside the agent's write scope. The agent must not have delete or overwrite permissions on the log destination.

Acceptable destinations:
- Append-only database table with agent write-only access
- Cloud logging service (AWS CloudWatch, GCP Cloud Logging, Azure Monitor)
- Append-only file system with agent write-only access
- Dedicated audit log service

Unacceptable destinations:
- Local file with agent read-write access
- In-memory store only
- Destination accessible only through the agent's own API

### Retention
Audit logs must be retained for a minimum of 90 days for standard deployments. Deployments handling financial, medical, or regulated data should apply the retention period required by the relevant regulation, which may exceed this minimum.

### Integrity
Log entries must be written synchronously before the corresponding action is taken. If a log write fails, the corresponding action must be blocked until the log write succeeds or the steward is notified and makes an explicit decision to proceed.

Log entries must not be modified after writing. Corrections to log entries are made by appending a new entry with a reference to the entry being corrected, not by modifying the original.

---

## Log Access

| Role | Access |
|---|---|
| Steward | Full read access to all sessions |
| Agent | Write access only (append-only to current session) |
| Auditor | Full read access (if designated by steward) |
| Other users | No access without explicit steward grant |

---

## Log Review Requirements

The steward must review the audit log:

- After any kill-switch activation (before reactivation)
- After any EXECUTE mode session
- At minimum every 7 days for active deployments
- Before applying any governance framework update
- After any PROHIBITED_BEHAVIOR_DETECTED event

Log reviews must be documented. The steward records the review date in the steward record.

---

## Log Rotation and Archiving

When logs are rotated or archived:
- The rotation event is itself logged
- Archived logs remain accessible to the steward
- The retention period applies from the date of each entry, not the date of archiving
- Archive integrity must be verifiable (hash or equivalent)

---

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

---

*Next document: [READ_ONLY_RUNTIME.md](./READ_ONLY_RUNTIME.md)*

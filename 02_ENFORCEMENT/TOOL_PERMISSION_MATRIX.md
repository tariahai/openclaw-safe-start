# Tool Permission Matrix
### OpenClaw Governance Framework | Tariah AI

---

## Purpose

This document defines which tools and capabilities are available to an OpenClaw instance under each runtime mode and task class. It is the access control list for the deployment.

Developers configure this matrix at deployment time by mapping their available tools to the categories defined here. No tool may be invoked if it does not appear in this matrix for the current mode and task class.

---

## How the Matrix Works

Every tool invocation is validated against two dimensions:

1. **Current runtime mode** — READ_ONLY, WRITE, or EXECUTE
2. **Current task class** — as classified against the Allowed Task Classes registry

A tool is permitted only if it appears in the intersection of the current mode and the current task class. If a tool is not listed for the current combination, the invocation is blocked.

---

## Tool Categories

Tools are grouped into categories. Deployments map their specific tools to these categories during configuration.

### Category R: Read Operations
Operations that retrieve information without modifying state.

| Tool Type | Description | Example Tools |
|---|---|---|
| R-01 | File system read | read_file, list_directory |
| R-02 | Database query (SELECT) | sql_query (read-only connection) |
| R-03 | API GET request | http_get, fetch_resource |
| R-04 | Search and retrieval | search_index, vector_search |
| R-05 | Log read | read_audit_log, read_system_log |

### Category W: Write Operations
Operations that create or modify data.

| Tool Type | Description | Example Tools |
|---|---|---|
| W-01 | File system write | write_file, create_file, append_file |
| W-02 | Database write | sql_insert, sql_update |
| W-03 | API POST/PUT/PATCH | http_post, http_put |
| W-04 | Object storage write | upload_object, put_blob |

### Category E: Execute Operations
Operations that trigger external actions, send communications, or invoke processes.

| Tool Type | Description | Example Tools |
|---|---|---|
| E-01 | Send communication | send_email, send_message, post_notification |
| E-02 | Trigger workflow | invoke_workflow, trigger_webhook |
| E-03 | Execute process | run_script, execute_command |
| E-04 | Delete operation | delete_file, sql_delete, remove_object |
| E-05 | External API action | api_action, third_party_execute |

### Category G: Governance Operations
Operations related to audit, compliance, and governance review. Always read-only.

| Tool Type | Description | Example Tools |
|---|---|---|
| G-01 | Audit log read | read_audit_log |
| G-02 | Governance document read | read_governance_doc |
| G-03 | Compliance check | verify_task_class, check_authorization |

---

## Permission Matrix

The following matrix defines which tool categories are available per runtime mode and task class. Cells marked **PERMITTED** allow the tool category. Cells marked **BLOCKED** do not.

| Task Class | Mode | R (Read) | W (Write) | E (Execute) | G (Governance) |
|---|---|---|---|---|---|
| CLASS 001: Information Retrieval | READ_ONLY | PERMITTED | BLOCKED | BLOCKED | PERMITTED |
| CLASS 002: Analysis and Synthesis | READ_ONLY | PERMITTED | BLOCKED | BLOCKED | PERMITTED |
| CLASS 003: Draft Generation | READ_ONLY | PERMITTED | BLOCKED | BLOCKED | PERMITTED |
| CLASS 004: Data Transformation | WRITE | PERMITTED | PERMITTED (W-01, W-02, W-04 only) | BLOCKED | PERMITTED |
| CLASS 005: Workflow Execution | EXECUTE | PERMITTED | PERMITTED | PERMITTED (E-02, E-05 only) | PERMITTED |
| CLASS 006: Communication Dispatch | EXECUTE | PERMITTED | BLOCKED | PERMITTED (E-01 only) | PERMITTED |
| CLASS 007: System Monitoring | READ_ONLY | PERMITTED | BLOCKED | BLOCKED | PERMITTED |
| CLASS 008: Governance and Audit | READ_ONLY | PERMITTED (R-05 only) | BLOCKED | BLOCKED | PERMITTED |

**Note on CLASS 005:** E-04 (Delete) is never permitted without explicit per-task steward authorization in addition to standard EXECUTE mode authorization. Delete operations must be individually approved.

---

## Configuring This Matrix for Your Deployment

When deploying OpenClaw, map your specific tools to the categories above:

```yaml
# Example deployment tool configuration
tool_permissions:
  read_operations:
    R-01:
      - read_file
      - list_directory
    R-02:
      - postgres_select  # read-only database connection
    R-03:
      - http_get
    R-04:
      - pinecone_query
      - faiss_search

  write_operations:
    W-01:
      - write_file
    W-02:
      - postgres_insert
      - postgres_update
    W-04:
      - s3_put_object

  execute_operations:
    E-01:
      - sendgrid_send
      - slack_post_message
    E-02:
      - n8n_trigger
      - webhook_dispatch
    E-03: []  # No process execution permitted in this deployment
    E-04: []  # No delete operations permitted without explicit per-task auth
    E-05:
      - stripe_api_action
```

Any tool not listed in this configuration is blocked by default.

---

## Override Requests

If a task requires a tool not permitted by the current matrix, the agent must:

1. Refuse to invoke the tool
2. Log the refusal with tool name, task reference, and reason
3. Surface an escalation to the steward describing the tool required and the task context
4. Wait for steward authorization before proceeding

The steward may grant a one-time per-task override. The override must be logged. A recurring need for the same override indicates the matrix should be updated via the Steward Update Protocol.

---

**Current version:** 1.0.0
**Last updated:** June 2026
**Maintained by:** Tariah AI

---

*Next document: [AUTHORIZATION_MODEL.md](./AUTHORIZATION_MODEL.md)*

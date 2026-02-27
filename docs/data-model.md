# Domain Data Model

This is the initial simplified baseline model for the application.

## Scope

- Multi-tenant policy and process storage
- RBAC authorization
- Change requests with approvals (four-eye principle)
- Basic versioning on policy and process content
- Central audit logging

## Initial ERD

```mermaid
erDiagram
    TENANT {
      uuid tenant_id PK
      string name
      string status
      datetime created_at
    }

    USER {
      uuid user_id PK
      string email
      string display_name
      string status
      datetime created_at
    }

    ROLE {
      uuid role_id PK
      string name
      string description
    }

    USER_ROLE {
      uuid user_role_id PK
      uuid tenant_id FK
      uuid user_id FK
      uuid role_id FK
      datetime assigned_at
    }

    POLICY_DOCUMENT {
      uuid policy_id PK
      uuid tenant_id FK
      string title
      string category
      string current_status
      uuid current_version_id FK
      datetime created_at
    }

    POLICY_VERSION {
      uuid policy_version_id PK
      uuid policy_id FK
      int version_no
      string content_ref
      string change_summary
      uuid created_by FK
      datetime created_at
      boolean is_published
    }

    PROCESS {
      uuid process_id PK
      uuid tenant_id FK
      string name
      string current_status
      uuid current_version_id FK
      datetime created_at
    }

    PROCESS_VERSION {
      uuid process_version_id PK
      uuid process_id FK
      int version_no
      string content_ref
      string change_summary
      uuid created_by FK
      datetime created_at
      boolean is_published
    }

    CHANGE_REQUEST {
      uuid change_request_id PK
      uuid tenant_id FK
      string target_type
      uuid target_id
      string action_type
      string status
      uuid requested_by FK
      datetime requested_at
      datetime decided_at
    }

    APPROVAL {
      uuid approval_id PK
      uuid change_request_id FK
      uuid approver_id FK
      string decision
      string comment
      datetime decided_at
    }

    WORKFLOW_STEP {
      uuid workflow_step_id PK
      uuid tenant_id FK
      string target_type
      int step_order
      string required_role
      int min_approvals
    }

    AUDIT_LOG {
      uuid audit_id PK
      uuid tenant_id FK
      uuid actor_id FK
      string action
      string entity_type
      uuid entity_id
      json metadata
      datetime created_at
    }

    TENANT ||--o{ USER_ROLE : has
    USER ||--o{ USER_ROLE : has
    ROLE ||--o{ USER_ROLE : grants

    TENANT ||--o{ POLICY_DOCUMENT : owns
    POLICY_DOCUMENT ||--o{ POLICY_VERSION : has
    USER ||--o{ POLICY_VERSION : creates

    TENANT ||--o{ PROCESS : owns
    PROCESS ||--o{ PROCESS_VERSION : has
    USER ||--o{ PROCESS_VERSION : creates

    TENANT ||--o{ CHANGE_REQUEST : owns
    USER ||--o{ CHANGE_REQUEST : requests
    CHANGE_REQUEST ||--o{ APPROVAL : has
    USER ||--o{ APPROVAL : decides

    TENANT ||--o{ WORKFLOW_STEP : defines
    TENANT ||--o{ AUDIT_LOG : has
    USER ||--o{ AUDIT_LOG : acts
```

## Core relationships

- `tenant` owns policy documents, processes, change requests, workflow steps, and audit logs.
- `user` gets tenant-specific roles through `user_role`.
- `policy_document` and `process` are versioned through their version tables.
- `change_request` drives updates and is approved through `approval` records.
- `audit_log` captures key user and system actions.

## Four-eye baseline rule

A `change_request` can move to `APPROVED` only when:

- at least two distinct users approved, and
- requester is not one of the approvers.

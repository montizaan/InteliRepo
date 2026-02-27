# State Machines

This file reflects the initial baseline model.

## Change Request Lifecycle

```mermaid
stateDiagram-v2
    [*] --> DRAFT
    DRAFT --> SUBMITTED: submit
    DRAFT --> CANCELLED: cancel

    SUBMITTED --> IN_REVIEW: routed_to_approval
    SUBMITTED --> REJECTED: validation_failed

    IN_REVIEW --> APPROVED: approvals_satisfied
    IN_REVIEW --> REJECTED: rejected
    IN_REVIEW --> CANCELLED: cancel

    APPROVED --> APPLIED: changes_published
    APPLIED --> [*]
    REJECTED --> [*]
    CANCELLED --> [*]
```

## Policy/Process Version Lifecycle

```mermaid
stateDiagram-v2
    [*] --> DRAFT
    DRAFT --> PENDING_APPROVAL: change_request_submitted
    PENDING_APPROVAL --> PUBLISHED: approved_and_applied
    PENDING_APPROVAL --> DRAFT: rejected
    PUBLISHED --> ARCHIVED: superseded_or_retired
    ARCHIVED --> [*]
```

## Minimal enforcement checks

- Requester needs write permission for target entity.
- Requester cannot approve own request.
- At least two distinct approvals required for four-eye.
- Publish applies a new version and writes an audit record.

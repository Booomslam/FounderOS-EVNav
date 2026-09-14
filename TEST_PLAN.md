# FounderOS Test Plan

Status: Approved FOS-000 baseline, Founder MANUAL approval recorded 2026-09-14

## 1. Evidence policy

Verification statuses are NOT_VERIFIED, PARTIALLY_VERIFIED, and FULLY_VERIFIED. FULLY_VERIFIED requires every environment listed for the task. Merge and green CI do not replace E2E, sandbox, staging, or manual evidence.

| Environment | Meaning | Minimum evidence |
|---|---|---|
| LOCAL | Hermetic developer execution | Command, runtime, exit status, test counts |
| CI | Clean GitHub Actions runner | Immutable run link and commit SHA |
| E2E | Isolated browser/system journey | Trace, assertions, screenshots only where visual evidence matters |
| SANDBOX_EXTERNAL | Provider sandbox/test account | Redacted request, provider response/external ID, cleanup |
| STAGING | Deployed production-like system | Release SHA, migration, health, rollback/restore evidence |
| MANUAL | Human judgment or approval | Reviewer, timestamp, scope, result, unresolved notes |

Secrets and sensitive payloads must be redacted from all evidence.

## 2. Test cases

| TC | Requirement / AC | Test | Environments | Primary tasks |
|---|---|---|---|---|
| TC-001 | REQ-001 / AC-001 | Persist direct chat; deny cross-user/company/conversation reads | LOCAL, CI, E2E | FOS-006 |
| TC-002 | REQ-002 / AC-002 | Product X request creates Marketing/Ops tasks and evidence-backed synthesis | LOCAL, CI, E2E | FOS-022, FOS-024, FOS-033 |
| TC-003 | REQ-003 / AC-003 | Versioned agent policy controls runtime tools; metadata cannot expand access | LOCAL, CI | FOS-005 |
| TC-004 | REQ-004 / AC-004 | Legal task transitions and dependency blocking persist transactionally | LOCAL, CI | FOS-007, FOS-008, FOS-023 |
| TC-005 | REQ-005 / AC-005 | Kill worker, expire lease, resume checkpoint, retry without duplicate action | LOCAL, CI, STAGING | FOS-009, FOS-010, FOS-015 |
| TC-006 | REQ-006 / AC-006 | Reject anonymous, wrong-company, wrong-role, and wrong-resource requests | LOCAL, CI, E2E | FOS-004 |
| TC-007 | REQ-007 / AC-007 | Deny unauthorized HR and Finance reads/tools | LOCAL, CI, E2E | FOS-020, FOS-031 |
| TC-008 | REQ-008 / AC-008 | Validate every connector operation has one action level | LOCAL, CI | FOS-013 |
| TC-009 | REQ-009 / AC-009 | Reject missing/expired/consumed/mismatched approval and changed payload | LOCAL, CI, E2E | FOS-016 |
| TC-010 | REQ-010 / AC-010 | Prove all writes use executor, policy, rate, audit, and idempotency | LOCAL, CI, SANDBOX_EXTERNAL | FOS-014, FOS-017 |
| TC-011 | REQ-011 / AC-011 | Readiness states cannot advance from key presence alone | LOCAL, CI, SANDBOX_EXTERNAL | FOS-013, FOS-018 |
| TC-012 | REQ-012 / AC-012 | Retrieval cites sources; restart persists; GBrain outage shows degraded mode | LOCAL, CI, E2E | FOS-019, FOS-032 |
| TC-013 | REQ-013 / AC-013 | AI claim cannot become fact without review; conflicts/supersession persist | LOCAL, CI, E2E | FOS-021 |
| TC-014 | REQ-014 / AC-014 | Knowledge scopes prevent HR/Finance/private leakage | LOCAL, CI, E2E | FOS-020, FOS-031 |
| TC-015 | REQ-015 / AC-015 | Replay one complete trace from request through provider evidence | LOCAL, CI, E2E | FOS-011, FOS-033 |
| TC-016 | REQ-016 / AC-016 | Attribute usage and stop execution at test budget | LOCAL, CI | FOS-012 |
| TC-017 | REQ-017 / AC-017 | Secret scan, client-bundle check, log/prompt redaction, scoped retrieval | LOCAL, CI, STAGING | FOS-003, FOS-029 |
| TC-018 | REQ-018 / AC-018 | Clean install/build; DB migration; staging deploy, rollback, backup/restore | LOCAL, CI, STAGING, MANUAL | FOS-001, FOS-002, FOS-028, FOS-029 |
| TC-019 | REQ-019 / AC-019 | UI status labels match fixtures and runtime evidence | LOCAL, CI, E2E, MANUAL | FOS-025, FOS-026 |
| TC-020 | REQ-020 / AC-020 | Validate ID mappings, dependency DAG, Issue/PR templates, and evidence states | LOCAL, MANUAL | FOS-000, FOS-002 |

## 3. Required suites

- Unit: schemas, policy decisions, state machines, digests, visibility, budgets.
- Repository integration: transactions, dependencies, leases, concurrency, migrations.
- Route integration: authentication, authorization, validation, error and replay behavior.
- E2E: direct chat, delegation, approval, isolation, recovery, audit reconstruction.
- Sandbox contracts: selected email/social read and write with cleanup and exact external IDs.
- Adversarial: prompt injection, tool escalation, cross-scope access, approval substitution, duplicate dispatch.
- Staging operations: migration, health, worker interruption, backup/restore, rollback.

## 4. FOS-000 verification

Required environments: LOCAL and MANUAL.

LOCAL evidence showed that the six files exist, required headings exist, requirement/AC/TC/task references resolve, task IDs are unique, the dependency graph is acyclic, allowed status values are used, and no forbidden dash characters exist. Founder MANUAL approval was received on 2026-09-14. FOS-000 is therefore DONE / IMPLEMENTED / FULLY_VERIFIED.

## 5. Evidence location

GitHub Issues become the execution evidence index after FOS-000 approval. CI links, E2E artifacts, sandbox external IDs, staging records, and manual review notes attach to the canonical Issue. `TASKS.md` stores only the canonical mapping and current status summary, not verbose run logs.

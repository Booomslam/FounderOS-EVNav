# FounderOS Master Task Backlog

Status: FOS-000 approved and complete  
Day-to-day execution uses canonical GitHub Issues for active tasks.

## 1. Status model

Task: BACKLOG, READY, IN_PROGRESS, REVIEW, BLOCKED, DONE.  
Implementation: NOT_STARTED, PARTIAL, IMPLEMENTED.  
Verification: NOT_VERIFIED, PARTIALLY_VERIFIED, FULLY_VERIFIED.

DONE requires every acceptance criterion and every required verification environment. Merge alone is insufficient.

## 2. Canonical backlog

Issue and PR values remain blank until created. `-` means none exists.

| Task | Title / objective | Dependencies | REQ / AC / TC | Environments | Affected areas | Task | Implementation | Verification | Issue | PR |
|---|---|---|---|---|---|---|---|---|---|---|
| FOS-000 | Establish approved project-control documents and GitHub workflow | None | REQ-020 / AC-020 / TC-020 | LOCAL, MANUAL | Six control docs | DONE | IMPLEMENTED | FULLY_VERIFIED | - | - |
| FOS-001 | Pin supported Node 22 runtime | FOS-000 | REQ-018 / AC-018 / TC-018 | LOCAL, CI | package/runtime docs | REVIEW | IMPLEMENTED | PARTIALLY_VERIFIED | #1 | pending | 
| FOS-002 | Add baseline CI gates | FOS-001 | REQ-018, REQ-020 / AC-018, AC-020 / TC-018, TC-020 | CI | workflows/scripts | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-003 | Triage dependency advisories and secret exposure | FOS-001, FOS-002 | REQ-017, REQ-018 / AC-017, AC-018 / TC-017, TC-018 | LOCAL, CI | dependencies/connectors | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-004 | Add human identity and company scope | FOS-002, FOS-003 | REQ-006 / AC-006 / TC-006 | LOCAL, CI, E2E | auth/schema/repos/routes | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-005 | Add versioned agent policy contract | FOS-004 | REQ-003, REQ-006 / AC-003, AC-006 / TC-003, TC-006 | LOCAL, CI | agents/policy/schema | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-006 | Scope conversations and history | FOS-004, FOS-005 | REQ-001 / AC-001 / TC-001 | LOCAL, CI, E2E | chat/schema/repos/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-007 | Add durable task and dependency model | FOS-004 | REQ-004 / AC-004 / TC-004 | LOCAL, CI | task/schema/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-008 | Add run and attempt state machine | FOS-007 | REQ-004 / AC-004 / TC-004 | LOCAL, CI | runtime/schema/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-009 | Implement atomic job claim, lease, and heartbeat | FOS-008 | REQ-005 / AC-005 / TC-005 | LOCAL, CI | worker/runtime/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-010 | Implement checkpoints, cancellation, and resume | FOS-009 | REQ-005 / AC-005 / TC-005 | LOCAL, CI, STAGING | runtime/model/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-011 | Add append-only event ledger | FOS-004, FOS-008 | REQ-015 / AC-015 / TC-015 | LOCAL, CI | events/schema/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-012 | Add usage, cost, budget, and circuit breaker | FOS-008, FOS-011 | REQ-016 / AC-016 / TC-016 | LOCAL, CI | LLM/events/analytics | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-013 | Split connectors into typed read/write capabilities | FOS-005 | REQ-008, REQ-011 / AC-008, AC-011 / TC-008, TC-011 | LOCAL, CI | connector types/adapters | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-014 | Build central policy-aware action executor | FOS-011, FOS-013 | REQ-010 / AC-010 / TC-010 | LOCAL, CI | action service/routes | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-015 | Add action idempotency | FOS-014 | REQ-005, REQ-010 / AC-005, AC-010 / TC-005, TC-010 | LOCAL, CI, SANDBOX_EXTERNAL | action/repos/connectors | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-016 | Add exact-action approval service and UI | FOS-004, FOS-014, FOS-015 | REQ-009 / AC-009 / TC-009 | LOCAL, CI, E2E | approvals/schema/routes/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-017 | Migrate communication writes through executor | FOS-016 | REQ-009, REQ-010 / AC-009, AC-010 / TC-009, TC-010 | LOCAL, CI, SANDBOX_EXTERNAL | email/slack/social routes | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-018 | Add staged connector readiness | FOS-013 | REQ-011 / AC-011 / TC-011 | LOCAL, CI, SANDBOX_EXTERNAL | connector status/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-019 | Add knowledge provenance and citations | FOS-004, FOS-011 | REQ-012 / AC-012 / TC-012 | LOCAL, CI, E2E | brain/GBrain/schema | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-020 | Add knowledge visibility enforcement | FOS-005, FOS-019 | REQ-007, REQ-014 / AC-007, AC-014 / TC-007, TC-014 | LOCAL, CI, E2E | policy/retrieval/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-021 | Add claim, fact, and decision governance | FOS-019, FOS-020 | REQ-013, REQ-014 / AC-013, AC-014 / TC-013, TC-014 | LOCAL, CI, E2E | knowledge/schema/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-022 | Implement Chief of Staff planner/delegator | FOS-007, FOS-008, FOS-010, FOS-012 | REQ-002 / AC-002 / TC-002 | LOCAL, CI, E2E | conductor/runtime/tasks | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-023 | Implement dependency scheduler | FOS-009, FOS-022 | REQ-004 / AC-004 / TC-004 | LOCAL, CI | scheduler/runtime | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-024 | Add consolidation, evidence validation, escalation | FOS-022, FOS-023 | REQ-002, REQ-015 / AC-002, AC-015 / TC-002, TC-015 | LOCAL, CI, E2E | conductor/artifacts/events | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-025 | Align org/status UI with enforced policy and evidence | FOS-005 | REQ-019 / AC-019 / TC-019 | LOCAL, CI, E2E, MANUAL | org/status UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-026 | Add browser E2E harness and core journeys | FOS-006, FOS-016, FOS-024 | REQ-018, REQ-019 / AC-018, AC-019 / TC-001, TC-002, TC-006, TC-009, TC-019 | CI, E2E | test harness | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-027 | Add prompt-injection and escalation tests | FOS-005, FOS-013, FOS-020 | REQ-006, REQ-010, REQ-014 / AC-006, AC-010, AC-014 / TC-006, TC-010, TC-014 | LOCAL, CI | adversarial tests | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-028 | Add production PostgreSQL and migrations | FOS-007, FOS-008, FOS-009, FOS-011 | REQ-018 / AC-018 / TC-018 | LOCAL, CI, STAGING | DB abstraction/migrations | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-029 | Add staging deployment, backup, restore, rollback | FOS-002, FOS-003, FOS-028 | REQ-017, REQ-018 / AC-017, AC-018 / TC-017, TC-018 | CI, STAGING, MANUAL | deploy/ops/runbook | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-030 | Benchmark optional orchestration frameworks | FOS-024, FOS-026 | REQ-018 / AC-018 / TC-018 | LOCAL, MANUAL | isolated spike/report | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-031 | Verify HR and Finance isolation scenario | FOS-020, FOS-025, FOS-027 | REQ-007, REQ-014 / AC-007, AC-014 / TC-007, TC-014 | CI, E2E, MANUAL | policy/fixtures/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-032 | Implement and verify GBrain degraded mode | FOS-019, FOS-020 | REQ-012 / AC-012 / TC-012 | LOCAL, CI, E2E | GBrain/retrieval/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-033 | Verify complete Product X MVP scenario | FOS-017, FOS-021, FOS-024, FOS-026, FOS-031, FOS-032 | REQ-001 to REQ-016, REQ-019 / corresponding AC / TC-001 to TC-016, TC-019 | CI, E2E, SANDBOX_EXTERNAL, MANUAL | full vertical slice | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |
| FOS-034 | Production readiness review | FOS-029, FOS-033 | REQ-001 to REQ-020 / AC-001 to AC-020 / TC-001 to TC-020 | CI, E2E, SANDBOX_EXTERNAL, STAGING, MANUAL | whole system/evidence | BACKLOG | NOT_STARTED | NOT_VERIFIED | - | - |

## 3. Dependency rules

- Codex checks dependencies before beginning a task.
- BACKLOG means dependencies or approval are incomplete. READY means dependencies and task definition are complete, not that work started.
- IN_PROGRESS normally has one canonical Issue and task branch. REVIEW means implementation is reviewable but required review/evidence is incomplete.
- BLOCKED records the exact blocker. DONE requires IMPLEMENTED plus FULLY_VERIFIED.
- If the graph is wrong, stop scope expansion, document the conflict, propose a correction, and wait when roadmap impact is material.

## 4. GitHub execution workflow

FOS-000 creates no Issues. After human approval, migrate tasks as they approach execution, initially FOS-001 through FOS-004 in dependency order. Do not bulk-create FOS-001 through FOS-034 without explicit instruction.

Default execution:

1. Read `AGENTS.md` and the canonical Issue.
2. Read linked requirements, architecture, security, tests, and dependencies.
3. Inspect current source and concurrent work.
4. Implement only approved scope on the task branch.
5. Run required tests and collect redacted evidence.
6. Open or update the Issue-linked PR.
7. Update independent task, implementation, and verification states.
8. Stop at the requested review point. Do not start the next task automatically.

Traceability: `OBJ -> FEAT -> REQ / SEC -> AC -> TC -> FOS TASK -> Issue -> PR -> evidence`.

## 5. Issue template

Title: `FOS-NNN - Task title`

Required sections: Objective; Why; Dependencies and status; Scope; Out of Scope; Affected Areas; Acceptance Criteria with AC IDs; Required Tests with TC IDs; Required Verification; Security Considerations with SEC IDs; Evidence Required; Implementation Status; Verification Status; Blockers.

One task has one canonical Issue unless an explicitly approved decomposition preserves the parent ID. The Issue is the execution tracker, while this file remains the canonical backlog and dependency map.

## 6. Pull Request rules

Every implementation PR references its FOS task and Issue, describes scope and architecture impact, lists tests and environments, maps satisfied ACs, links evidence, states unresolved risks, and avoids unrelated refactors. Use `Closes #N` only when closure conditions will actually be satisfied. Green CI or merge alone never makes a task DONE.

## 7. Task evidence summaries

- FOS-000: LOCAL structural validation passed for six control documents, 20 REQ, 20 AC, 20 TC, 20 SEC, 35 tasks, allowed states, and an acyclic dependency graph. Founder MANUAL approval was received on 2026-09-14. No Issue or PR exists by design.
- FOS-001: Dependency FOS-000 is DONE. GitHub Issue #1 is canonical. LOCAL passed on Node v22.23.2: clean `npm ci`, 111 test files and 963 tests, `npm run typecheck`, and `npm run build` with 24 static pages. Node 26 install was rejected with `EBADENGINE`. Implementation is complete; CI remains required before FULLY_VERIFIED or DONE.

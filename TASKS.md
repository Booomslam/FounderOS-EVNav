# FounderOS Master Task Backlog

Status: FOS-000 approved and complete  
All 35 FOS tasks have canonical GitHub Issues in the `FounderOS-EVNav`
Project. The Issue and Project `Status` are the operational source of status.
This file remains the dependency map, task definition, and Issue/PR index.

## 1. Status model

Task: BACKLOG, READY, IN_PROGRESS, REVIEW, BLOCKED, DONE.  
Implementation: NOT_STARTED, PARTIAL, IMPLEMENTED.  
Verification: NOT_VERIFIED, PARTIALLY_VERIFIED, FULLY_VERIFIED.

DONE requires every acceptance criterion and every required verification environment. Merge alone is insufficient.

## 2. Canonical backlog

Every task has a canonical Issue. `-` means no PR exists yet.

| Task | Title / objective | Dependencies | REQ / AC / TC | Environments | Affected areas | Task | Implementation | Verification | Issue | PR |
|---|---|---|---|---|---|---|---|---|---|---|
| FOS-000 | Establish approved project-control documents and GitHub workflow | None | REQ-020 / AC-020 / TC-020 | LOCAL, MANUAL | Six control docs | DONE | IMPLEMENTED | FULLY_VERIFIED | #3 | - |
| FOS-001 | Pin supported Node 22 runtime | FOS-000 | REQ-018 / AC-018 / TC-018 | LOCAL, CI | package/runtime docs | DONE | IMPLEMENTED | FULLY_VERIFIED | #1 | #2 |
| FOS-002 | Add baseline CI gates | FOS-001 | REQ-018, REQ-020 / AC-018, AC-020 / TC-018, TC-020 | CI | workflows/scripts | READY | NOT_STARTED | NOT_VERIFIED | #4 | - |
| FOS-003 | Triage dependency advisories and secret exposure | FOS-001, FOS-002 | REQ-017, REQ-018 / AC-017, AC-018 / TC-017, TC-018 | LOCAL, CI | dependencies/connectors | BACKLOG | NOT_STARTED | NOT_VERIFIED | #5 | - |
| FOS-004 | Add human identity and company scope | FOS-002, FOS-003 | REQ-006 / AC-006 / TC-006 | LOCAL, CI, E2E | auth/schema/repos/routes | BACKLOG | NOT_STARTED | NOT_VERIFIED | #6 | - |
| FOS-005 | Add versioned agent policy contract | FOS-004 | REQ-003, REQ-006 / AC-003, AC-006 / TC-003, TC-006 | LOCAL, CI | agents/policy/schema | BACKLOG | NOT_STARTED | NOT_VERIFIED | #7 | - |
| FOS-006 | Scope conversations and history | FOS-004, FOS-005 | REQ-001 / AC-001 / TC-001 | LOCAL, CI, E2E | chat/schema/repos/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | #8 | - |
| FOS-007 | Add durable task and dependency model | FOS-004 | REQ-004 / AC-004 / TC-004 | LOCAL, CI | task/schema/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | #9 | - |
| FOS-008 | Add run and attempt state machine | FOS-007 | REQ-004 / AC-004 / TC-004 | LOCAL, CI | runtime/schema/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | #10 | - |
| FOS-009 | Implement atomic job claim, lease, and heartbeat | FOS-008 | REQ-005 / AC-005 / TC-005 | LOCAL, CI | worker/runtime/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | #11 | - |
| FOS-010 | Implement checkpoints, cancellation, and resume | FOS-009 | REQ-005 / AC-005 / TC-005 | LOCAL, CI, STAGING | runtime/model/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | #12 | - |
| FOS-011 | Add append-only event ledger | FOS-004, FOS-008 | REQ-015 / AC-015 / TC-015 | LOCAL, CI | events/schema/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | #13 | - |
| FOS-012 | Add usage, cost, budget, and circuit breaker | FOS-008, FOS-011 | REQ-016 / AC-016 / TC-016 | LOCAL, CI | LLM/events/analytics | BACKLOG | NOT_STARTED | NOT_VERIFIED | #14 | - |
| FOS-013 | Split connectors into typed read/write capabilities | FOS-005 | REQ-008, REQ-011 / AC-008, AC-011 / TC-008, TC-011 | LOCAL, CI | connector types/adapters | BACKLOG | NOT_STARTED | NOT_VERIFIED | #15 | - |
| FOS-014 | Build central policy-aware action executor | FOS-011, FOS-013 | REQ-010 / AC-010 / TC-010 | LOCAL, CI | action service/routes | BACKLOG | NOT_STARTED | NOT_VERIFIED | #16 | - |
| FOS-015 | Add action idempotency | FOS-014 | REQ-005, REQ-010 / AC-005, AC-010 / TC-005, TC-010 | LOCAL, CI, SANDBOX_EXTERNAL | action/repos/connectors | BACKLOG | NOT_STARTED | NOT_VERIFIED | #17 | - |
| FOS-016 | Add exact-action approval service and UI | FOS-004, FOS-014, FOS-015 | REQ-009 / AC-009 / TC-009 | LOCAL, CI, E2E | approvals/schema/routes/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | #18 | - |
| FOS-017 | Migrate communication writes through executor | FOS-016 | REQ-009, REQ-010 / AC-009, AC-010 / TC-009, TC-010 | LOCAL, CI, SANDBOX_EXTERNAL | email/slack/social routes | BACKLOG | NOT_STARTED | NOT_VERIFIED | #19 | - |
| FOS-018 | Add staged connector readiness | FOS-013 | REQ-011 / AC-011 / TC-011 | LOCAL, CI, SANDBOX_EXTERNAL | connector status/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | #20 | - |
| FOS-019 | Add knowledge provenance and citations | FOS-004, FOS-011 | REQ-012 / AC-012 / TC-012 | LOCAL, CI, E2E | brain/GBrain/schema | BACKLOG | NOT_STARTED | NOT_VERIFIED | #21 | - |
| FOS-020 | Add knowledge visibility enforcement | FOS-005, FOS-019 | REQ-007, REQ-014 / AC-007, AC-014 / TC-007, TC-014 | LOCAL, CI, E2E | policy/retrieval/repos | BACKLOG | NOT_STARTED | NOT_VERIFIED | #22 | - |
| FOS-021 | Add claim, fact, and decision governance | FOS-019, FOS-020 | REQ-013, REQ-014 / AC-013, AC-014 / TC-013, TC-014 | LOCAL, CI, E2E | knowledge/schema/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | #23 | - |
| FOS-022 | Implement Chief of Staff planner/delegator | FOS-007, FOS-008, FOS-010, FOS-012 | REQ-002 / AC-002 / TC-002 | LOCAL, CI, E2E | conductor/runtime/tasks | BACKLOG | NOT_STARTED | NOT_VERIFIED | #24 | - |
| FOS-023 | Implement dependency scheduler | FOS-009, FOS-022 | REQ-004 / AC-004 / TC-004 | LOCAL, CI | scheduler/runtime | BACKLOG | NOT_STARTED | NOT_VERIFIED | #25 | - |
| FOS-024 | Add consolidation, evidence validation, escalation | FOS-022, FOS-023 | REQ-002, REQ-015 / AC-002, AC-015 / TC-002, TC-015 | LOCAL, CI, E2E | conductor/artifacts/events | BACKLOG | NOT_STARTED | NOT_VERIFIED | #26 | - |
| FOS-025 | Align org/status UI with enforced policy and evidence | FOS-005 | REQ-019 / AC-019 / TC-019 | LOCAL, CI, E2E, MANUAL | org/status UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | #27 | - |
| FOS-026 | Add browser E2E harness and core journeys | FOS-006, FOS-016, FOS-024 | REQ-018, REQ-019 / AC-018, AC-019 / TC-001, TC-002, TC-006, TC-009, TC-019 | CI, E2E | test harness | BACKLOG | NOT_STARTED | NOT_VERIFIED | #28 | - |
| FOS-027 | Add prompt-injection and escalation tests | FOS-005, FOS-013, FOS-020 | REQ-006, REQ-010, REQ-014 / AC-006, AC-010, AC-014 / TC-006, TC-010, TC-014 | LOCAL, CI | adversarial tests | BACKLOG | NOT_STARTED | NOT_VERIFIED | #29 | - |
| FOS-028 | Add production PostgreSQL and migrations | FOS-007, FOS-008, FOS-009, FOS-011 | REQ-018 / AC-018 / TC-018 | LOCAL, CI, STAGING | DB abstraction/migrations | BACKLOG | NOT_STARTED | NOT_VERIFIED | #30 | - |
| FOS-029 | Add staging deployment, backup, restore, rollback | FOS-002, FOS-003, FOS-028 | REQ-017, REQ-018 / AC-017, AC-018 / TC-017, TC-018 | CI, STAGING, MANUAL | deploy/ops/runbook | BACKLOG | NOT_STARTED | NOT_VERIFIED | #31 | - |
| FOS-030 | Benchmark optional orchestration frameworks | FOS-024, FOS-026 | REQ-018 / AC-018 / TC-018 | LOCAL, MANUAL | isolated spike/report | BACKLOG | NOT_STARTED | NOT_VERIFIED | #32 | - |
| FOS-031 | Verify HR and Finance isolation scenario | FOS-020, FOS-025, FOS-027 | REQ-007, REQ-014 / AC-007, AC-014 / TC-007, TC-014 | CI, E2E, MANUAL | policy/fixtures/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | #33 | - |
| FOS-032 | Implement and verify GBrain degraded mode | FOS-019, FOS-020 | REQ-012 / AC-012 / TC-012 | LOCAL, CI, E2E | GBrain/retrieval/UI | BACKLOG | NOT_STARTED | NOT_VERIFIED | #34 | - |
| FOS-033 | Verify complete Product X MVP scenario | FOS-017, FOS-021, FOS-024, FOS-026, FOS-031, FOS-032 | REQ-001 to REQ-016, REQ-019 / corresponding AC / TC-001 to TC-016, TC-019 | CI, E2E, SANDBOX_EXTERNAL, MANUAL | full vertical slice | BACKLOG | NOT_STARTED | NOT_VERIFIED | #35 | - |
| FOS-034 | Production readiness review | FOS-029, FOS-033 | REQ-001 to REQ-020 / AC-001 to AC-020 / TC-001 to TC-020 | CI, E2E, SANDBOX_EXTERNAL, STAGING, MANUAL | whole system/evidence | BACKLOG | NOT_STARTED | NOT_VERIFIED | #36 | - |

## 3. Dependency rules

- Codex checks dependencies before beginning a task.
- BACKLOG means dependencies or approval are incomplete. READY means dependencies and task definition are complete, not that work started.
- IN_PROGRESS normally has one canonical Issue and task branch. REVIEW means implementation is reviewable but required review/evidence is incomplete.
- BLOCKED records the exact blocker. DONE requires IMPLEMENTED plus FULLY_VERIFIED.
- If the graph is wrong, stop scope expansion, document the conflict, propose a correction, and wait when roadmap impact is material.

## 4. GitHub execution workflow

All FOS tasks have canonical Issues. GitHub Project `Status` is authoritative:
`Todo` means not claimed, `In progress` means claimed or under review, and
`Done` means the Issue is closed after completion conditions pass.

Default execution:

1. Read `AGENTS.md` and the canonical Issue.
2. Read linked requirements, architecture, security, tests, and dependencies.
3. Inspect current source and concurrent work.
4. Add the Issue to the `FounderOS-EVNav` Project and assign it to `Booomslam`.
5. Set Project `Status` to `In progress`, add a claim comment naming the agent
   and task branch, and set the task lifecycle here to `IN_PROGRESS`.
6. Implement only approved scope on the task branch.
7. Run required tests and collect redacted evidence.
8. After required LOCAL verification passes, push the single-task branch.
9. Open or update the Issue-linked PR and set the task lifecycle to `REVIEW`
   when it is ready for review. The Project item remains `In progress`.
10. Update independent task, implementation, and verification states.
11. Set the task to `DONE` only after all required verification passes, then
    close the Issue so Project automation can move the item to `Done`.
12. Stop at the requested review point. Do not start the next task automatically.

An Issue is not claimed merely because a branch or PR exists. Implementation
must not begin while the Issue is unassigned, the Project item is still
`Todo`, the claim comment is missing, or this file is not `IN_PROGRESS`.

Traceability: `OBJ -> FEAT -> REQ / SEC -> AC -> TC -> FOS TASK -> Issue -> PR -> evidence`.

## 5. Issue template

Title: `FOS-NNN - Task title`

Required metadata: Assignee `Booomslam`; `FounderOS-EVNav` Project; Project
`Status` set to `In progress` when claimed.

Required sections: Objective; Why; Dependencies and status; Scope; Out of Scope; Affected Areas; Acceptance Criteria with AC IDs; Required Tests with TC IDs; Required Verification; Security Considerations with SEC IDs; Evidence Required; Claim Record with agent and branch; Implementation Status; Verification Status; Blockers.

One task has one canonical Issue unless an explicitly approved decomposition preserves the parent ID. The Issue and Project `Status` are the operational source of status, while this file remains the canonical definition, dependency map, and Issue/PR index.

## 6. Pull Request rules

Every implementation PR references its FOS task and Issue, describes scope and architecture impact, lists tests and environments, maps satisfied ACs, links evidence, states unresolved risks, and avoids unrelated refactors. Use `Closes #N` only when closure conditions will actually be satisfied. Green CI or merge alone never makes a task DONE.

## 7. Task evidence summaries

- FOS-000: GitHub Issue #3 is the retrospective canonical record. LOCAL structural validation passed for six control documents, 20 REQ, 20 AC, 20 TC, 20 SEC, 35 tasks, allowed states, and an acyclic dependency graph. Founder MANUAL approval was received on 2026-09-14.
- FOS-001: Dependency FOS-000 is DONE. GitHub Issue #1 and PR #2 are canonical. LOCAL passed on Node v22.23.2: clean `npm ci`, 111 test files and 963 tests, `npm run typecheck`, and `npm run build` with 24 static pages. Node 26 install was rejected with `EBADENGINE`. CI runs 34826328734 and 34826548694 passed install, tests, typecheck, and build. Founder authorized PR merge and Issue closure on 2026-09-14. Implementation and required verification are complete.

# FOUNDER OS - agent operating contract

This file is mandatory for Codex, Claude, and every coding agent working in
this repository. `CLAUDE.md` contains useful repository context and commands,
but the six root control documents define the approved operating model.

## 1. Repository operating model

Follow this sequence:

`AGENTS.md -> owning GitHub Issue -> linked REQ / SEC / AC / TC -> dependency check -> implementation -> verification -> evidence -> PR / review`

FOS-000 has no GitHub Issue by design. After FOS-000 receives Founder manual
approval and task migration begins, active implementation work must normally
be driven by one canonical GitHub Issue retaining its FOS task ID.

## 2. Source-of-truth hierarchy

- `AGENTS.md`: rules governing how coding agents work.
- `PRODUCT_SPEC.md`: objectives, features, requirements, acceptance criteria,
  and product constraints.
- `ARCHITECTURE.md`: verified current architecture, target architecture,
  system boundaries, responsibilities, and constraints.
- `SECURITY_MODEL.md`: identities, trust boundaries, data sensitivity,
  action levels, permissions, approvals, and security invariants.
- `TEST_PLAN.md`: test cases, verification environments, and required evidence.
- `TASKS.md`: canonical backlog, dependency graph, task metadata, status, and
  Issue/PR references.
- GitHub Issue: active execution tracker for one FOS task.
- Pull Request: implementation and review unit linked to the owning Issue.

When sources conflict:

1. Verified runtime evidence wins for claims about current behavior.
2. Security invariants must never be weakened silently.
3. Product changes require an update to `PRODUCT_SPEC.md`.
4. Architecture changes require an update to `ARCHITECTURE.md`.
5. Execution must remain consistent with both the owning Issue and `TASKS.md`.

## 3. Required pre-work procedure

Before modifying production code:

1. Read root `AGENTS.md`.
2. Identify the owning FOS task.
3. Read the canonical GitHub Issue when one exists.
4. Read the linked REQ, SEC, AC, and TC entries.
5. Verify every mandatory dependency.
6. Inspect the existing implementation and current git state.
7. Identify affected modules and concurrent work on those surfaces.
8. Identify all required verification environments.
9. Confirm scope and out-of-scope boundaries.

Do not begin implementation when a mandatory dependency is unsatisfied.

## 4. Task lifecycle

- `BACKLOG`: dependencies, approval, or definition are incomplete.
- `READY`: dependencies are satisfied and scope, acceptance criteria, tests,
  and verification environments are sufficiently defined. Work has not begun.
- `IN_PROGRESS`: approved implementation has started, normally with an active
  Issue and task branch.
- `REVIEW`: implementation is reviewable, but required review or verification
  remains incomplete.
- `BLOCKED`: work cannot proceed because an explicit blocker is recorded.
- `DONE`: all acceptance criteria and all required verification have passed.

A commit, green CI result, or merge does not by itself make a task DONE.

## 5. Implementation status

Track independently from lifecycle and verification:

- `NOT_STARTED`
- `PARTIAL`
- `IMPLEMENTED`

Implementation status never implies verification status.

## 6. Verification status

Track independently:

- `NOT_VERIFIED`
- `PARTIALLY_VERIFIED`
- `FULLY_VERIFIED`

`FULLY_VERIFIED` requires every environment assigned by `TEST_PLAN.md` to pass.

## 7. Verification environments

- `LOCAL`: hermetic developer execution.
- `CI`: clean GitHub Actions runner.
- `E2E`: isolated browser or full-system journey.
- `SANDBOX_EXTERNAL`: real provider sandbox or test account.
- `STAGING`: deployed production-like environment.
- `MANUAL`: recorded human judgment or observation.

Never silently substitute environments. LOCAL is not CI. CI is not E2E. A
mock connector is not SANDBOX_EXTERNAL. An automated assertion is not MANUAL.

## 8. GitHub Issue workflow

After Founder approval of FOS-000:

`TASKS.md -> canonical Issue -> branch -> implementation -> tests -> PR -> CI / E2E / evidence -> review -> Issue DONE`

Issue titles retain the canonical ID, for example:

`FOS-001 - Pin supported Node 22 runtime`

GitHub Issues are active execution trackers. `TASKS.md` remains the master
backlog and dependency map. Do not create competing Issues for one FOS task
unless an approved decomposition preserves the parent relationship.

## 9. Pull Request rules

Every implementation PR must:

- Reference the FOS task and owning Issue.
- Describe scope and any architecture impact.
- Identify the AC IDs addressed.
- List tests executed and environments completed.
- Link or attach redacted evidence.
- State unresolved risks and blockers.
- Avoid unrelated refactors.

A green PR does not automatically make a task DONE. A merged PR does not
automatically make it FULLY_VERIFIED.

## 10. Scope discipline

Do not:

- Automatically begin the next task.
- Expand scope because adjacent code appears improvable.
- Perform unrelated refactoring.
- Add a framework without an approved architecture decision.
- Change product requirements silently.
- Convert target behavior into current-behavior documentation.
- Mark demo or seeded functionality as production capability.

When an unexpected architecture problem appears, document it, decide whether
it blocks the task, and propose a follow-up task. Do not absorb unrelated work
silently.

## 11. Architecture rules

- Organization hierarchy is not authorization.
- Agent identity is not human identity.
- Prompt instructions are not security boundaries.
- UI state is not durable execution state.
- Seeded workflow or task data is not proof of execution.
- All future external mutations use the governed action boundary.
- Approval binds the exact action parameters.
- Retries of external mutations are idempotent.
- Knowledge preserves provenance.
- Production concurrency must not rely accidentally on SQLite behavior.

Do not introduce Paperclip, OpenClaw, OpenAI Agents SDK, LangGraph, or another
orchestration framework unless an approved FOS architecture task requires it.

## 12. Security rules

`SECURITY_MODEL.md` is mandatory. Default posture is `DENY` unless explicitly
allowed.

Never:

- Bypass policy checks for convenience.
- Treat prompt instructions as authorization.
- Expose secrets in logs, prompts, client code, or test evidence.
- Infer permissions from department labels.
- Give HR or Finance data broad visibility.
- Execute LEVEL 2, LEVEL 3, or LEVEL 4 actions without the required approval
  contract once implemented.
- Add a mutation path that bypasses the central action executor once that
  boundary exists.

## 13. Testing rules

Before implementation, identify required TC IDs and environments from
`TEST_PLAN.md`. Use TDD for behavior changes. After implementation, run every
required test, report failures and skips honestly, and distinguish mocked
tests from external sandbox evidence.

Do not change tests to hide incorrect behavior or weaken acceptance criteria
to make an implementation pass.

## 14. Evidence rules

`IMPLEMENTED`, `PASS`, `FULLY_VERIFIED`, and `DONE` require evidence appropriate
to the task. Evidence can include CI runs, test output, E2E artifacts, redacted
sandbox responses, screenshots, database state, event records, recovery tests,
migration results, and manual observations.

Never claim an integration works because a credential exists. Provider health,
read behavior, write behavior, approval, and evidence are separate states.

## 15. Documentation synchronization

When implementation changes:

- Product behavior: update `PRODUCT_SPEC.md` when applicable.
- Architecture: update `ARCHITECTURE.md`.
- Security boundary: update `SECURITY_MODEL.md`.
- Test expectations: update `TEST_PLAN.md`.
- Task status or evidence: update `TASKS.md` and the canonical Issue.

Documentation and verified runtime behavior must not silently diverge.

## 16. One-task-at-a-time rule

Unless explicitly instructed otherwise, work on one FOS task at a time. When
it reaches the requested review point, stop. Do not begin the next ready task.

## 17. FOS-000 special rule

FOS-000 establishes documentation and project control only. Product runtime
implementation is forbidden. FOS-000 cannot become DONE until all six control
documents exist, cross-document and traceability validation pass, and Founder
MANUAL approval is recorded.

## Non-negotiables

- **Never commit or copy secrets.** Credentials live in `.env.local`
  (gitignored) and Alex's canonical files; `lib/creds.ts` resolves them.
  Never copy keys from `~/knowledge/.env.agents` into the repo.
- **Never push to any remote or touch `main` without Alex's explicit yes.**
  Commit locally on `founder-os`, small checkpoints, often.
- **Don't kill the dev server on 4100 or 4101** - other sessions use them.
  If your edit crashes the dev server's hot reload, fix it fast: a crash loop
  corrupts `.next` and breaks every session's page chunks (kill the port,
  `rm -rf .next`, restart).
- `/org` markup is frozen - do not restructure it.
- No em/en dashes in anything written for Alex.

## How to work

- TDD: failing test first (`tests/`, one file per module,
  `FOUNDER_OS_DB=:memory:`), then implement. `npm test` and
  `npm run typecheck` must be green before claiming done.
- Everything reads through the repo layer: `lib/db.ts` repos + `lib/schemas.ts`
  Zod validation + `lib/seed.ts` seeds. Never query SQLite from a page/route.
- Theme via CSS vars on `data-theme` (five themes in `app/globals.css`);
  Tailwind `os.*` tokens map to them. Keep `tailwind.config.ts` and
  `globals.css` in sync.
- Commands: `npm run dev` (port 4100) · `npm test` · `npm run typecheck` ·
  `npm run seed` · `npm run brain:docs`.

## Multi-agent etiquette

Multiple agent sessions (Claude, Codex) work this repo concurrently:
- `git log --oneline` to see where others are; commit small and often.
- Coordinate by surface: don't edit a page/component another session has
  uncommitted changes in (`git status` shows them).
- The Playwright browser is shared across sessions - expect interference.
- Leave handoff notes in `docs/` if you stop mid-feature.

# FounderOS Product Specification

Status: Approved FOS-000 baseline, Founder MANUAL approval recorded 2026-09-14  
Baseline: `docs/audits/FOUNDERS_OS_BASELINE_AUDIT.md` at revision `d5e565ea1ee82069e17cd176a33c2f272619e002`  
Owner: Human Founder / CEO  

## 1. Purpose

FounderOS will be a practical company operating system for a solo founder. The Founder remains the accountable human. AI agents may research, draft, coordinate, and execute bounded work, but authority, data access, and external actions must be enforced by software rather than model instructions.

The verified repository is suitable as a foundation, not as a production-ready autonomous company. Existing UI, repositories, schemas, direct agent chat, connector boundaries, tests, and GBrain adapter should be preserved and extended. See `ARCHITECTURE.md` and the baseline audit for evidence.

## 2. Objectives

| ID | Objective | Success signal |
|---|---|---|
| OBJ-001 | Give the Founder one trustworthy operating interface | Direct and Chief of Staff conversations expose real status, evidence, and decisions |
| OBJ-002 | Coordinate a small set of bounded AI roles | Delegated work is durable, dependency-aware, recoverable, and consolidated |
| OBJ-003 | Prevent unauthorized or duplicate side effects | Every write passes policy, approval when required, and idempotent execution |
| OBJ-004 | Build trustworthy company memory | Answers cite sources; facts, claims, decisions, and visibility are distinct |
| OBJ-005 | Make operation observable and recoverable | Runs, events, cost, failures, retries, and evidence are reconstructable |
| OBJ-006 | Keep the system maintainable for one founder | Minimum architecture, typed connectors, automated gates, and staged integrations |

## 3. MVP roles and scenario

MVP roles:

- Human Founder / CEO
- Chief of Staff / Executive Assistant agent
- Research / Marketing agent
- Operations agent
- HR agent, on demand

Canonical MVP request: `Prepare launch planning for Product X.`

Expected outcome:

1. The request creates a scoped conversation, root task, and run.
2. Chief of Staff creates a reviewable plan.
3. Research / Marketing receives competitor and positioning work.
4. Operations receives a launch checklist task.
5. HR remains inactive unless a permitted people question is required.
6. Dependencies block synthesis until required evidence exists.
7. Chief of Staff consolidates results and creates explicit decisions or approvals.
8. The system waits for the Founder before any approval-gated action.
9. Restart or retry does not lose progress or duplicate an effect.

## 4. Features

| ID | Feature | Objectives | Requirements |
|---|---|---|---|
| FEAT-001 | Founder and direct-agent conversations | OBJ-001 | REQ-001 |
| FEAT-002 | Chief of Staff planning and delegation | OBJ-001, OBJ-002 | REQ-002, REQ-003 |
| FEAT-003 | Durable tasks and workflows | OBJ-002, OBJ-005 | REQ-004, REQ-005 |
| FEAT-004 | Identity, organization, and permission policy | OBJ-002, OBJ-003 | REQ-006, REQ-007 |
| FEAT-005 | Action levels, approvals, and safe execution | OBJ-003 | REQ-008, REQ-009, REQ-010 |
| FEAT-006 | Typed external integrations | OBJ-003, OBJ-006 | REQ-011 |
| FEAT-007 | Company and private knowledge | OBJ-004 | REQ-012, REQ-013, REQ-014 |
| FEAT-008 | Audit, evidence, cost, and observability | OBJ-005 | REQ-015, REQ-016 |
| FEAT-009 | Production security and operations | OBJ-003, OBJ-005, OBJ-006 | REQ-017, REQ-018 |
| FEAT-010 | Honest product state and governed delivery | OBJ-001, OBJ-006 | REQ-019, REQ-020 |

## 5. Requirements and acceptance criteria

| Requirement | Statement | Acceptance criterion |
|---|---|---|
| REQ-001 | Every conversation shall be scoped to company, human user, agent, and conversation; direct agent chat shall persist messages and evidence. | AC-001: Cross-scope reads are denied and a direct chat remains retrievable after restart. |
| REQ-002 | Chief of Staff shall create a plan, durable child tasks, dependencies, evidence requirements, and a consolidated response. | AC-002: The MVP request produces separate Marketing and Operations tasks and one evidence-backed synthesis. |
| REQ-003 | Agent identity shall include a versioned role, instructions, owner, department, model policy, allowed capabilities, and escalation path. | AC-003: Runtime authorization uses the versioned registry rather than display metadata. |
| REQ-004 | Tasks and runs shall persist lifecycle, ownership, inputs, outputs, attempts, artifacts, dependencies, deadlines, and failure state. | AC-004: State transitions and dependency blocking are transactionally enforced. |
| REQ-005 | Work shall support claim leases, heartbeat, checkpoints, retry, cancellation, recovery, and general action idempotency. | AC-005: A killed worker resumes and produces no duplicate external effect. |
| REQ-006 | Every request shall have an authenticated human identity and company scope; authorization shall be deny by default. | AC-006: Anonymous, wrong-company, and unauthorized resource requests fail server-side. |
| REQ-007 | Department and private data, especially HR and Finance, shall be isolated independently of the visual org chart. | AC-007: Negative tests prove agents cannot retrieve or act on restricted HR or Finance data. |
| REQ-008 | Every operation shall be classified as LEVEL 0 read-only, LEVEL 1 internal draft, LEVEL 2 external communication, LEVEL 3 external modification, or LEVEL 4 financial/legal commitment. | AC-008: Each connector operation declares one enforced action level. |
| REQ-009 | LEVEL 2 through LEVEL 4 actions shall require an applicable human approval unless a narrower reviewed policy explicitly forbids or permits the exact case. | AC-009: Approval binds actor, company, operation, exact parameters, expiry, and one-time consumption; any material change invalidates it. |
| REQ-010 | All external writes shall pass through one policy-aware action executor with audit, rate limits, and idempotency. | AC-010: Direct connector writes outside the executor fail automated tests. |
| REQ-011 | Connector contracts shall separate read and write capabilities and report adapter, configuration, health, sandbox-read, and approved-write readiness separately. | AC-011: A connector cannot report operational readiness from credential presence alone. |
| REQ-012 | Company knowledge retrieval shall persist across restart, preserve citations and provenance, expose degraded mode, and never invent unavailable evidence. | AC-012: Retrieved answers cite resolvable sources; GBrain failure is visible and does not masquerade as semantic retrieval. |
| REQ-013 | Claims, trusted facts, and decisions shall be separate governed records with confidence, reviewer, conflicts, supersession, and review dates. | AC-013: AI output cannot become a trusted fact without the configured human promotion step. |
| REQ-014 | Knowledge visibility shall support company, department, and private scopes enforced at retrieval. | AC-014: HR-private and Finance-private fixtures remain invisible to unauthorized agents. |
| REQ-015 | An append-only event trail shall reconstruct request, plan, task, policy decision, approval, tool call, action, provider result, artifact, and state transition. | AC-015: One trace query reconstructs the complete MVP run with actors and timestamps. |
| REQ-016 | Model and provider usage, latency, failures, token/cost estimates, budgets, and alerts shall be attributable to company, task, run, and agent. | AC-016: A configured test budget stops further model execution and records the reason. |
| REQ-017 | Secrets shall remain server-side, scoped, masked, rotatable, and excluded from logs, prompts, client bundles, and repository history. | AC-017: Secret scanning and exposure tests pass; production uses an approved secret store. |
| REQ-018 | Production shall use a supported runtime, durable concurrent database, migrations, CI, deployment health checks, backup/restore, and rollback evidence. | AC-018: Staging deployment, migration, forced rollback, and restore are exercised successfully. |
| REQ-019 | UI and documentation shall distinguish REAL, PARTIAL, DEMO / SEEDED, STUB, and NOT PRESENT, and shall not imply completion without evidence. | AC-019: Status labels agree with runtime evidence and tests for the surfaced capability. |
| REQ-020 | Development shall use the approved GitHub Issue and PR workflow with stable traceability from objective through verification evidence. | AC-020: Every executable task has one canonical task ID; after approval its Issue and PR preserve all required links and independent statuses. |

## 6. Product constraints

- The Founder remains the final accountable authority.
- Prompt text is never an authorization boundary.
- Visual reporting relationships do not grant permission.
- Merge, implementation, and full verification are separate states.
- No capability is called autonomous unless runtime evidence proves autonomous execution and recovery.
- No connector is called working from a logo, key, or configuration page alone.
- No external write bypasses the central action executor.
- No AI statement becomes a trusted fact automatically.
- Existing `/org` markup remains structurally frozen unless a separately approved task changes that constraint.
- Existing UI and repository boundaries remain unless evidence justifies a scoped refactor.
- Initial MVP adds no external orchestration framework. FOS-030 evaluates frameworks only after stable contracts and the multi-agent slice exist.
- SQLite remains valid for local tests; production database selection is handled by FOS-028.
- No em or en dashes are used in project documentation written for the Founder.

## 7. Scope boundaries

In MVP scope:

- The five named roles and one launch-planning scenario
- Read-only research and internal drafts
- Durable delegation, evidence, approval gating, permission boundaries, audit, and recovery
- A small number of sandboxed connectors needed to prove the control plane

Out of MVP scope:

- A full virtual corporation
- Autonomous spending or legal commitments
- Broad automatic migration to GitHub Issues
- Replacing the UI shell
- Paperclip, OpenClaw, OpenAI Agents SDK, or LangGraph adoption
- Unreviewed live publication, customer messaging, destructive actions, or credential rotation

## 8. Traceability rule

Canonical chain:

`OBJ -> FEAT -> REQ / SEC -> AC -> TC -> FOS TASK -> GitHub Issue -> Pull Request -> verification evidence`

`SEC-*` invariants are in `SECURITY_MODEL.md`, tests in `TEST_PLAN.md`, architecture in `ARCHITECTURE.md`, and task ownership/status in `TASKS.md`.

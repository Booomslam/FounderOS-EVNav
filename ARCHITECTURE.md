# FounderOS Architecture

Status: Approved FOS-000 baseline, Founder MANUAL approval recorded 2026-09-14  
Evidence baseline: `docs/audits/FOUNDERS_OS_BASELINE_AUDIT.md`  

## 1. Decision

Preserve the existing Next.js shell, repository plus Zod pattern, direct chat, connector adapters, tests, and GBrain boundary. Add a small TypeScript control plane. Do not add an orchestration framework now.

## 2. Verified current architecture

```text
Browser UI
  -> Next.js App Router pages and client components
  -> Route handlers
  -> agent chat / conductor / runtime / connectors
  -> lib/db.ts repositories + lib/schemas.ts
  -> SQLite, local files, provider APIs, GBrain
```

| Boundary | Verified source | Current maturity |
|---|---|---|
| UI | `app/`, `components/` | REAL |
| API | `app/api/` | REAL, but shared authority |
| Agent chat | `lib/agents/chat.ts`, `lib/connectors/llm.ts` | REAL direct chat, PARTIAL runtime |
| Conductor | `lib/agents/conductor.ts` | PARTIAL single-agent router |
| Broadcast | `lib/agents/runtime.ts:58-85` | REAL fan-out, not delegation |
| Tasks | `app/api/agents/work/route.ts`, `lib/db.ts:679-739` | PARTIAL CRUD, no runner |
| Workflows | `components/WorkflowMap.tsx`, `lib/schemas.ts:428-459` | DEMO / SEEDED |
| Organization | `app/org/page.tsx`, `lib/hierarchy.ts` | REAL visualization, no runtime authority |
| Knowledge | `lib/connectors/gbrain.ts`, `lib/brain-dump.ts` | PARTIAL; live GBrain unverified |
| Auth | `middleware.ts`, `lib/access-gate.ts` | PARTIAL shared token |
| Persistence | `lib/db.ts` | REAL SQLite repository layer |

Known runtime path:

`AgentChat -> /api/agents/{id}/chat -> chatWithAgent -> persisted user message -> agent chatTools -> AI gateway generateText -> persisted tool calls/assistant response -> UI`

It is synchronous, not resumable, and conversation history is currently keyed only by agent ID.

## 3. Target architecture

```text
FounderOS UI
  -> Authenticated scoped API
  -> Command and query services
       -> Agent registry and policy engine
       -> Task/workflow state machine
       -> Durable worker queue
       -> Approval service
       -> Idempotent action executor
       -> Knowledge and decision services
       -> Append-only event ledger
  -> Typed read/write connector adapters
  -> PostgreSQL + object storage + secret manager + telemetry
```

| Component | Responsibility | Existing disposition |
|---|---|---|
| UI shell | Conversations, plans, tasks, approvals, evidence | KEEP and extend |
| API/backend | Identity, scope, validation, commands, queries | REFACTOR incrementally |
| Agent runtime | Claim, execute, checkpoint, resume, cancel | EXTEND from current runtime |
| Agent registry | Versioned roles, instructions, capabilities, policy refs | EXTEND static registry |
| Organization model | Reporting and escalation, not implicit authority | KEEP; link explicit policy |
| Task/workflow engine | Durable state, dependencies, attempts, artifacts | EXTEND task repositories |
| Permission engine | Deny-by-default decisions at every action/data boundary | ADD |
| Approval layer | Exact-action digest, approver, scope, expiry, consume once | ADD |
| Tool/connectors | Typed reads/writes; writes only via executor | KEEP adapters, REFACTOR writes |
| Company knowledge | Cited retrieval, provenance, degraded mode | EXTEND GBrain boundary |
| Private knowledge | Company/department/private namespaces | ADD |
| Decision log | Options, evidence, decision, owner, review/result | ADD |
| Audit/event log | Append-only intent and execution timeline | ADD |
| Database | SQLite local/tests; concurrent durable production store | KEEP local, PostgreSQL target |
| Observability | Trace, latency, failures, cost, budgets, alerts | ADD |
| Secrets | Server-side scoped store, masking, rotation, use audit | EXTEND local resolver; add production store |
| Testing | Unit, integration, E2E, policy, recovery, sandbox, staging | EXTEND |

## 4. Required runtime invariants

- API identity and company scope exist before data or tool access.
- Runtime permissions come from the versioned registry and policy engine, not UI labels or prompts.
- Task state transitions are transactional.
- Workers claim a lease, heartbeat, and checkpoint.
- Dependencies are satisfied before a task becomes READY.
- Every external write enters the action executor.
- Approval is checked against the exact immutable action digest immediately before execution.
- Idempotency is recorded before or atomically with provider dispatch where possible.
- Every transition and side effect emits an append-only event.
- Knowledge retrieval enforces visibility before returning content to a model.

## 5. Data ownership

Core production entities: company, human user, membership, agent definition/version, conversation, message, task, dependency, run, attempt, checkpoint, artifact, policy, policy decision, approval, action, idempotency result, source, claim, fact, decision, event, usage record, connector account, and secret reference.

All tenant-bearing records include `company_id`. Sensitive records also include visibility and data-classification fields. Provider secrets are referenced, never stored in task payloads or event bodies.

## 6. Integration boundaries

- Read and write operations are separate typed capabilities.
- Connector readiness states are adapter-present, configured, health-passed, sandbox-read-passed, and approved-write-passed.
- Provider payloads are untrusted input.
- Provider responses and external IDs are evidence, not automatic proof of business completion.
- GitHub is not required for runtime MVP; it is the development execution tracker defined in `TASKS.md`.

## 7. Deployment boundary

Node 22 is the currently verified runtime. FOS-001 pins it. CI, dependency remediation, production database, staging deployment, backup/restore, and rollback are separate evidence states. Merge does not prove staging or production behavior.

## 8. Framework boundary

Use the existing Vercel AI SDK for the first vertical slice. FOS-030 may compare Paperclip, OpenClaw, OpenAI Agents SDK, and LangGraph after FOS-024 and FOS-026. Adoption requires lower operational risk and less code without duplicating task, identity, policy, approval, or audit records.
